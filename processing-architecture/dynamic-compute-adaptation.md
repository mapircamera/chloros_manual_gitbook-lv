# Dinamiskā aprēķinu pielāgošana

Chloros 1.1.0 versijā ieviesta viedā aparatūras atpazīšana un automātiska apstrādes stratēģijas izvēle. Apstrādes dzinējs pielāgojas jūsu aparatūrai — sākot no Jetson Nano līdz darbstacijai ar vairākiem GPU — bez jebkādas manuālas konfigurācijas.

***

## Kā tas darbojas

Kad Chloros sākas, tas automātiski veic jūsu sistēmas profilēšanu:

1. **Atpazīst operētājsistēmu** — Windows vai Linux
2. **Identificē CPU kodolus un kopējo RAM**

3.**Atpazīst GPU klātbūtni** — NVIDIA CUDA iespējas, VRAM, modeli
4. **Identificē Jetson modeli** (ja piemērojams) — izmantojot `/proc/device-tree/model`
5. **Pārbauda termiskos sensorus** (Jetson) — temperatūrai pielāgotai apstrādei
6. **Izvēlas optimālo aprēķinu stratēģiju** — pamatojoties uz visu atklāto aparatūru
7. **Automātiski konfigurē darba vienību skaitu, cauruļvada tipu un atmiņas sadali**Rezultāts tiek saglabāts kešatmiņā, lai nākamās izpildes sāktos ātrāk. Ja aparatūra mainās (piemēram, tiek pievienots GPU), Chloros nākamajā palaišanas reizē veic profilēšanu no jauna.***

## Aprēķinu stratēģijas

Chloros izvēlas vienu no trim aprēķinu stratēģijām, pamatojoties uz jūsu aparatūru:

| Stratēģija | Nepieciešams GPU | Darbinieki | Pieslēguma kanāls | Vispiemērotākais |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`** | Jā (12 GB+ VRAM vai 16 GB+ koplietošana) | 3–4 | `fused_gpu` | Datoru GPU ar 12 GB+, Jetson Orin NX 16 GB, AGX Orin |
| **`GPU_SINGLE`** | Jā (&lt; 12 GB VRAM) | 1–3 | `tiled_gpu` | Ieejas līmeņa GPU, Jetson Nano, Orin Nano |
| **`CPU_PARALLEL`** | Nē | kodoli - 1 | `cpu_fallback` | Sistēmas bez NVIDIA GPU |

### Apstrādes ceļu veidi

* **`fused_gpu`** — Pilns GPU apstrādes ceļš. Visas debayer, korekcijas un indeksēšanas operācijas tiek izpildītas uz GPU vienā apvienotā ciklā. Augstākā caurlaidspēja, bet prasa vairāk VRAM.
* **`tiled_gpu`** — Atmiņas ziņā efektīvs GPU ceļš. Apstrādā attēlus flīzēs, lai tie ietilptu ierobežotajā GPU atmiņā. Zemāka caurlaidspēja, bet darbojas ierīcēs ar ierobežotu atmiņu.
* **`cpu_fallback`** — Apstrāde tikai ar CPU, izmantojot daudzpavedienu paralēlismu. Tiek izmantota, ja NVIDIA GPU nav pieejams.***

## Platformas specifiska darbība

| Platforma | Stratēģija | Darbinieki | Caurule | Piezīmes |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sērijveida) | Atmiņas taupīšanas režīms, apstrādā vienu attēlu vienlaikus |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (vienlaicīgi) | Ieteicamā malu ierīce — reāla paralēla GPU apstrāde |
| **Jetson AGX Orin 64 GB** | `GPU_PARALLEL` | 4 | `fused_gpu` (vienlaicīgi) | Maksimāla malu ierīču veiktspēja |
| **Galddators ar 8 GB GPU** | `GPU_SINGLE` | 3 | `tiled_gpu` | Laba galddatora veiktspēja ar atmiņas resursus taupījošiem blokiem |
| **Datoru ar 12 GB+ GPU** | `GPU_PARALLEL` | 3–4 | `fused_gpu` | Optimāla datora veiktspēja |
| **Tikai CPU sistēma** | `CPU_PARALLEL` | kodoli - 1 | `cpu_fallback` | Nav nepieciešams GPU, izmanto ThreadPool |

{% hint style="info" %}
**Jetson vienotā atmiņa**: Jetson ierīces kopīgi izmanto GPU un CPU atmiņu. Jetson Orin NX 16 GB ziņo par ~15,3 GB VRAM, taču tā ir tā pati fiziskā RAM, ko izmanto operētājsistēma un CPU procesi. Chloros to ņem vērā, nosakot atmiņas sadales sliekšņus.
{% endhint %}

***

## Dinamiska GPU atmiņas sadale

Chloros izmanto [4 pavedienu apstrādes cauruļvadu](processing-pipeline.md):

* **

1. pavediens** (Atklāšana) — attēla ielāde, EXIF analīze, mērķa atklāšana
* **

2. pavediens** (Kalibrēšana) — atstarojuma kalibrēšanas aprēķini
* **

3. pavediens** (Apstrāde) — GPU debayer, vinjetes korekcija, indeksa aprēķins
* **

4. pavediens** (Eksportēšana) — Failu rakstīšana, metadatu iegūšana

Tiklīdz iepriekšējie procesa pavedieni pabeidz darbu (piemēram, visi attēli ir atklāti), to piešķirtā GPU atmiņa tiek atbrīvota un **pārdalīta atlikušajiem aktīvajiem pavedieniem**. Tas nozīmē, ka 3. pavedienam (GPU intensīvam posmam) tiek piešķirta arvien lielāka atmiņa, kā procesa posmi virzās uz priekšu, uzlabojot caurlaidspēju visintensīvākajiem aprēķiniem.

### Piešķiršanas posmi

| Posms | Aktīvie pavedieni | GPU atmiņas sadale |
| --- | --- | --- |
| **Sākumā** | 1, 2, 3, 4 | Sadalīta starp visiem pavedieniem |
| **Sākuma vidus** | 2, 3, 4 | Pārdalīta pavediena 1 atmiņa |
| **Vidus-beigas** | 3, 4 | Pavedienu 1+2 atmiņa tiek novirzīta uz 3+4 |
| **Beigas** | 3 vai 4 | Maksimālā atmiņa atlikušajam pavedienam |***

## Tekstūru apzināta apstrāde

Tekstūru apzināta debayer metode (tikai Chloros+) izmanto ievērojami vairāk GPU atmiņas nekā standarta metode, jo tiek izmantots AI/ML trokšņu noņemšanas modelis:

* Sistēmām ar **&lt; 7 GB VRAM** tiek uzspiesta sinhronā apstrādes cilpa tekstūru apzināta režīmā (viens attēls vienlaikus)
* Sistēmas ar **7 GB+ VRAM** var apstrādāt tekstūru atpazīšanu vienlaicīgi, lai gan ar samazinātu darba vienību skaitu salīdzinājumā ar standarta režīmu***

## Siltuma vadība (Jetson)

Jetson ierīcēm ir siltuma ierobežojumi, īpaši slēgtās vai gaisā esošās instalācijās. Chloros uzrauga GPU un CPU temperatūras un automātiski pielāgo apstrādi:

| Temperatūra | Reakcija |
| --- | --- |
| **&lt; 70°C** | Normāla darbība — pilna ātruma režīms |
| **70°C** (Brīdinājums) | Samazināt partijas lielumu |
| **80°C** (Kritiska situācija) | Agresīva jaudas ierobežošana — samazināt vienlaicīgumu un darba procesoru skaitu |
| **90°C** (izslēgšana) | Pilnībā pārtrauciet GPU apstrādi |

Temperatūras uzraudzībai Jetson platformās tiek izmantots `tegrastats`. Datoru sistēmās ar atbilstošu dzesēšanu termiskais ierobežojums tiek iedarbināts reti.

***

## Atmiņas slodzes pārvaldība

Chloros apstrādes laikā uzrauga sistēmas atmiņas slodzi:

* **Atmiņas slieksnis**: 85 % izmantojums izraisa konservatīvu darbību
* **OOM samazinājums**: Ja notiek atmiņas pārpildīšanās, piešķīrums tiek samazināts par 25 % (0,75x reizinātājs)
* **Pipeline rezerves režīms**: Lielas atmiņas slodzes gadījumā cauruļvads automātiski pāriet no `fused_gpu` uz `tiled_gpu`
* **Ieteikumi par apmaiņas atmiņu**: Jetson ierīcē Chloros brīdina, ja apmaiņas atmiņas vietas nav pietiekami jūsu datu kopas izmēram***

## Apstrādes pielāgojumu uzraudzība

### CLI statusa izvade

Kad sākas apstrāde, CLI parāda atklāto aparatūras profilu:

```

Chloros CLI 1.1.0
Platform: Linux aarch64 (Jetson Orin NX 16GB)
Strategy: GPU_PARALLEL | Workers: 3 | Pipeline: fused_gpu
CUDA: Available | GPU Memory: 15.3 GB (shared)
```

### Sistēmas diagnostika

Palaidiet `chloros-cli selftest`, lai redzētu pilnu aparatūras profilu un pārbaudītu aprēķinu iespējas:

```bash
chloros-cli selftest
```

Tas pārbauda CUDA pieejamību, GPU atmiņu, trokšņu samazināšanas modeļus un aizmugures savienojamību.

***

## Turpmākie soļi

* [Apstrādes cauruļvads](processing-pipeline.md) — 4 pavedienu cauruļvada arhitektūras izpratne
* [NVIDIA Jetson rokasgrāmata](../linux/nvidia-jetson-guide.md) — Jetson specifiska ieviešana un optimizācija
* [CLI : Komandrinda](../CLI.md) — Pilna CLI atsauce
