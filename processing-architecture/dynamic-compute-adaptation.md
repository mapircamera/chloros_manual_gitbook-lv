# Dinamiskā aprēķinu pielāgošana

Chloros
1.3.0 izmanto aparatūras atpazīšanu un automātisku apstrādes stratēģijas izvēli. Apstrādes dzinējs pielāgojas jūsu aparatūrai — sākot no „Jetson Orin Nano“ līdz darbstacijai ar vairākiem GPU — bez jebkādas manuālas konfigurācijas.

***

## Kā tas darbojas

Kad Chloros
tiek palaists, tas veic jūsu sistēmas profilēšanu:

1. **Atpazīst operētājsistēmu** — Windows
vai Linux
2. **Identificē procesora kodolus un kopējo RAM**

3.**Atpazīst GPU klātbūtni** — NVIDIA CUDA atbalsts, VRAM, modelis
4. **Identificē „Jetson“ modeli** (ja piemērojams) — izmantojot `/proc/device-tree/model`
5. **Pārbauda termiskos sensorus** (Jetson) — apstrādei, ņemot vērā temperatūru
6. **Izvēlas aprēķinu stratēģiju** — pamatojoties uz visu atklāto aparatūru
7. **Automātiski konfigurē darba vienību skaitu, apstrādes plūsmas tipu un atmiņas sadalījumu**

Atklātais profils tiek saglabāts sesijas cache atmiņā un uz diska, tādējādi nākamās izpildes sākas ātrāk:

| Platforma | Cache profils |
| --- | --- |
| **Linux
/ Jetson** | `~/.config/chloros/system_config.json` (ņem vērā `XDG_CONFIG_HOME`) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Dzēsiet šo failu, lai piespiestu veikt jaunu atklāšanu — tas ir noderīgi pēc GPU vai papildu RAM pievienošanas. Chloros
automātiski veic atkārtotu atklāšanu arī tad, ja kešatmiņa ir rakstīta ar nesaderīgu vecāku versiju.

***

## Aprēķinu stratēģijas

Chloros
izvēlas vienu no trim aprēķinu stratēģijām atkarībā no jūsu aparatūras:

| Stratēģija | Izvēlas, ja | Darbinieki | Izpildītājs | Apstrādes ceļš |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**| CUDA GPU, kas ziņo par**12 GB+ VRAM**(uz Jetson vienotās atmiņas, nepieciešama arī kopējā koplietojamā RAM vismaz 12 GB+) | `min(4, ⌊(VRAM − 0.5GB) × 0.9 ÷ 3.5GB⌋)`, vismaz 2 —**ierobežots līdz 2 uz Jetson** | Darba procesi (atsevišķi CUDA konteksti) | `fused_gpu` |
| **`GPU_SINGLE`**| CUDA GPU ar**2–12 GB VRAM**| 3 (I/O pārklāšanās; GPU piekļuve serializēta ar semaforu).**1 (sekvenciāli) uz Jetson ierīcēm ar mazāk nekā 12 GB RAM** | Darba procesi (atsevišķi CUDA konteksti); sekvenciāli vienā procesā uz Jetson ierīcēm ar mazu RAM apjomu | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | Bez CUDA GPU vai ar mazāk nekā 2 GB VRAM | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

`GPU_PARALLEL` darba vienības formulas piemēri: 12 GB VRAM → 2 darba vienības, 16 GB → 3 darba vienības, 20 GB+ → 4 darba vienības. Katram darba procesam ir atvēlēti 3,5 GB, kas atbilst aprēķinātajām vajadzībām uzdevumam ar tekstūru atpazīšanu, nepārsniedzot 90 % no VRAM, kas paliek pēc 0,5 GB rezerves atskaitīšanas.

{% hint style="info" %}
**GPU stratēģijām ir nepieciešams PyTorch, kas spēj izmantot CUDA.** Ierīce, kuras PyTorch nav izmantojamas CUDA ierīces, saņem `CPU_PARALLEL`, neatkarīgi no tās GPU. 1.3.0 Jetson pakete izmanto JetPack 6 CUDA versiju PyTorch, tādēļ Jetson atbilst prasībām, tiklīdz ir instalētas JetPack CUDA un cuDNN bibliotēkas. Jetson ar mazāk nekā 12 GB RAM vienmēr apstrādā vienu attēlu vienlaikus, procesa laikā (reģistrēts kā `GPU_SINGLE`).
{% endhint %}

GPU stratēģijas izmanto darba procesu kopu, kur katrs process ir atsevišķs operētājsistēmas process ar savu CUDA kontekstu, ko Chloros
sāk, atkārtoti palaistot savu instalēto aizmugurējo programmu (nevis izmantojot Python
`multiprocessing`, kura darba procesu palaišana nedarbojas iepakotajā lietojumprogrammā). CPU stratēģija izmanto pavedienu kopu (Python
`ThreadPoolExecutor`). Chloros
neizmanto nekādu trešo pušu izstrādātu izkliedēto sistēmu (piemēram, Ray).

### Apstrādes ceļu veidi

* **`fused_gpu`** — Pilnīgs GPU apstrādes ceļš. Debayera, korekcijas un indeksēšanas operācijas tiek izpildītas uz GPU vienā apvienotā ciklā. Augstākā caurlaidspēja, prasa visvairāk VRAM.
* **`tiled_gpu`** — Atmiņai efektīvs GPU ceļš. Apstrādā attēlus flīzēs, lai tie ietilptu ierobežotajā GPU atmiņā. Zemāka caurlaidspēja, bet darbojas ierīcēs ar ierobežotu atmiņu.
* **`cpu_fallback`** — Apstrāde tikai ar CPU, izmantojot daudzpavedienu paralēlismu. Tiek izmantota, ja nav pieejams NVIDIA GPU, kā arī kā pēdējā iespēja, ja abas GPU apstrādes ķēdes nedarbojas.

Vides izpildes rezerves ķēde vienmēr ir `fused_gpu` → `tiled_gpu` → `cpu_fallback`.

***

## Stratēģijas manuāla pārrakstīšana

Iestatiet vides mainīgo `CHLOROS_STRATEGY`, lai piespiedu kārtā piemērotu konkrētu stratēģiju — tas ir eksperta „glābšanas lūka” gadījumiem, kad automātiskā noteikšana izvēlas kaut ko, kas nav piemērots jūsu situācijai (piemēram, lai GPU paliktu brīvs citiem uzdevumiem):

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Mainīgais tiek salīdzināts, neņemot vērā lielos un mazos burtus; viss, kas nav viens no trim nosaukumiem, tiek ignorēts, un automātiskā noteikšana turpinās kā parasti. Pārrakstīšanas gadījumā Chloros
joprojām izvēlas darba procesoru skaitu jūsu vietā:

| Pārrakstīšana | Izmantotais darba procesoru skaits |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Pārrakstīšana ar `GPU_SINGLE` vai `GPU_PARALLEL` datorā, kurā nav izmantojamas CUDA ierīces, tiek noraidīta, un žurnālā parādās rinda `[STRATEGY]`, un izpilde izmanto `CPU_PARALLEL`.

Ieteicams to iestatīt katrai komandai atsevišķi, nevis pastāvīgi, lai parastās izpildes turpinātu automātiski pielāgoties.

***

## Platformas specifiska uzvedība

| Platforma | Stratēģija | Darbinieki | Pieslēguma kanāls | Piezīmes |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekvenciāls) | Atmiņas taupīšanas režīms, viens attēls vienlaikus |
| **Jetson Orin NX 8 GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekvenciāla) | Kopējā RAM, kas mazāka par 12 GB, nosaka sekvenciālu apstrādi |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (vienlaicīgi) | Ieteicamā malējā ierīce — Jetson ierobežots līdz 2 darba procesiem |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (vienlaicīgi) | Maksimāla malas ierīces veiktspēja (arī Jetson ierobežots līdz 2 darba procesiem) |
| **Galddators ar 8 GB GPU** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 darba procesi pārklājas I/O, kamēr semafors serializē piekļuvi GPU |
| **Datoram ar 12 GB+ GPU** | `GPU_PARALLEL` | 2–4 | `fused_gpu` (vienlaicīgi) | Optimāla darbvirsmas veiktspēja: 12 GB → 2 darba procesi, 16 GB → 3, 20 GB+ → 4 |
| **Tikai CPU sistēma** | `CPU_PARALLEL` | fiziskie kodoli − 1 (min. 2) | `cpu_fallback` | GPU nav nepieciešams, izmanto pavedienu kopu |

{% hint style="info" %}
**Jetson vienotā atmiņa**: Jetson ierīces kopīgi izmanto GPU un CPU atmiņu. Jetson Orin NX ar 16 GB atmiņu rāda apmēram 15,3 GB VRAM, taču tā ir tā pati fiziskā RAM, ko izmanto operētājsistēma un CPU procesi. Tāpēc 16 GB un lielākas Jetson ierīces atbilst `GPU_PARALLEL` prasībām tāpat kā 12 GB un lielākas galddatoru GPU, taču tām ir noteikts ierobežojums — 2 darba procesi — GPU, darba procesi un to CUDA konteksti katram darba procesam izmanto to pašu kopīgo pūlu.
{% endhint %}

### GPU budžets atkarībā no VRAM (atsevišķas GPU)

Uz x86_64 serveriem ar atsevišķu NVIDIA GPU atklātā VRAM apjoms nosaka arī to, cik daudz no kartes apstrādes resursiem var pieprasīt un cik lielas var kļūt partijas:

| Konstatētā VRAM | GPU budžeta maksimums | Partijas lieluma reizinātājs |
| --- | --- | --- |
| **8 GB+** | 90 % | ×2,0 |
| **6–8 GB** | 85 % | ×1,75 |
| **3,5–6 GB** | 80 % | ×1,5 |
| **2–3,5 GB** | 75 % | ×1,25 |
| **Mazāk par 2 GB** | 70 % | ×1,0 |

Diskrētie GPU sistēmai rezervē tikai 0,5 GB, jo tie neizmanto sistēmas RAM. Jetson profili rezervē daudz vairāk un nosaka zemāku ierobežojumu — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Dinamiska GPU atmiņas sadale

Chloros
izmanto [4 pavedienu apstrādes cauruļvadu](processing-pipeline.md):

* **

1. pavediens** (Atpazīšana) — attēla ielāde, EXIF analīze, mērķa atpazīšana
* **

2. pavediens** (Kalibrēšana) — atstarojuma kalibrēšanas aprēķini
* **

3. pavediens** (Apstrāde) — GPU debayer, vinjetes korekcija, indeksa aprēķins
* **

4. pavediens** (Eksportēšana) — failu rakstīšana, metadatu ievietošana

1., 2. un 4. pavedieni patērē maz GPU resursu; 3. pavediens ir visresursietilpīgākais. Kad iepriekšējie procesa pavedieni pabeidz darbu, to GPU resursi tiek **pārdalīti starp atlikušajiem aktīvajiem pavedieniem**, tādējādi 3. pavediens procesa gaitā pakāpeniski saņem arvien vairāk atmiņas.

### Resursu sadales posmi

| Posms | Aktīvie pavedieni | GPU atmiņas sadale |
| --- | --- | --- |
| **Sākumā** | 1, 2, 3, 4 | Sadalīta starp visiem pavedieniem, lielākā daļa — 3. pavedienam |
| **Sākuma vidusposms** | 2, 3, 4 | 1. pavediena daļa pārdalīta |
| **Vidus-beigas** | 3, 4 | 1. un 2. pavediena daļas tiek piešķirtas 3. un 4. pavedienam |
| **Beigas** | 3 vai 4 | Pēdējais aktīvais pavediens saņem maksimālo piešķīrumu |

Šos skaitļus regulē divi noteikumi:

* Vītnei, kas ir **vienīgā** aktīvā, tiek piešķirts tās profila maksimālais piešķīrums.
* Ja ir aktīvs vairāk nekā viens *resursietilpīgs* GPU uzdevums, katra resursietilpīgā uzdevuma bāzes piešķīrums tiek sadalīts starp tiem (nekad nepazeminot to zemāk par konfigurēto minimumu).

Vērtība, kas faktiski tiek izmantota darbības laikā, ir **mazāka** no platformas profila piešķīruma un GPU atmiņas monitora reālā ieteikuma, tādējādi noslogota karte vienmēr uzvar pār optimistisku profilu.***

## Tekstūru apzināta apstrāde

„Texture Aware“ debayer (**tikai Chloros
+** — `--debayer texture-aware`) izmanto AI/ML trokšņu noņemšanas modeli, kam vienai kopijai nepieciešami aptuveni 1,75 GB VRAM FP16 formātā, tādējādi tas izmanto daudz vairāk GPU atmiņas nekā standarta metode:

* Sistēmas ar **mazāk nekā 7 GB VRAM**apstrādā tekstūru ņemšanu vērā**sinhronā ciklā, vienu attēlu pēc otra** — tajās nevar ietilpt vairākas modeļa kopijas, un darba grupu izmantošana tikai palielinātu konkurenci
* Sistēmas ar **7 GB un vairāk VRAM** var apstrādāt „Texture Aware” vienlaicīgi, lai gan ar mazāku darba vienību skaitu salīdzinājumā ar standarta metodi
* Uz **Jetson** „Texture Aware” vienmēr tiek piesaistīts vienam darba procesam
* Ja **nav izmantojama CUDA GPU**, izpilde izmanto standarta debayeru, un aizmugurējā sistēma reģistrē vienu `[DEBAYER] … needs a CUDA GPU …` rindu, kurā norādīts iemesls
* Zema enerģijas patēriņa „Jetson” ierīcēs (Nano, Orin Nano) CLI
piemēro GPU frekvences ierobežojumu, ja tiek pieprasīta „Texture Aware” funkcija — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)

***

## Siltuma vadība (Jetson)

Jetson ierīcēm ir siltuma ierobežojumi, it īpaši slēgtās telpās vai gaisā. Chloros
uzrauga Jetson iebūvētos temperatūras sensorus un automātiski pielāgo partiju izmērus:

| Temperatūra | Reakcija |
| --- | --- |
| **&lt; 70 °C** | Normāla darbība — pilna ātruma režīms |
| **70 °C** (brīdinājums) | Partijas lielums pakāpeniski samazinās (no 100 % līdz 50 % temperatūras diapazonā no 70 °C līdz 80 °C) |
| **80 °C** (Kritiska) | Agresīva jaudas ierobežošana (no 50 % līdz 0 % diapazonā no 80 °C līdz 90 °C) |
| **90 °C** (Izslēgšanās) | Pilnībā pārtrauc GPU apstrādi |

Datoros ar atbilstošu dzesēšanu termiskā jaudas ierobežošana tiek aktivizēta reti.

***

## Atmiņas slodzes pārvaldība

Programma Chloros
apstrādes laikā nepārtraukti uzrauga GPU atmiņu un reaģē trīs līmeņos.

**Partijas lieluma noteikšana.** Partija sākas ar 8 attēliem, reizinātiem ar platformas koeficientu no iepriekšējām tabulām. Pēc tam Chloros
pārbauda brīvo VRAM, rezervē 20 % no tās PyTorch paša pārvaldības vajadzībām un pieņem, ka uz vienu 12 MP attēlu nepieciešami aptuveni 100 MB GPU atmiņas — partijas lielums ir mazākais no diviem: no atmiņas atvasinātais limits vai platformas bāzes vērtība. Tas nekad nenokrīt zem 1.

**Preventīva samazināšana.**Pārsniedzot**85 % VRAM izmantojumu**, partiju izmēri tiek samazināti, pirms rodas kāda kļūme.**Piešķīruma samazināšana katram pavedienam.** Palielinoties reālajai izmantošanai, katra pavediena GPU budžets tiek samazināts: ×0,75, ja izmantošana pārsniedz 80 %, ×0,5, ja pārsniedz 90 %. Monitoringa sliekšņi ir 70 % (konservatīvs), 85 % (normālais darbības limits) un 95 % (atmiņas izsmelšanas risks).**Atmiņas izsmelšanas (OOM) atkāpšanās un atgūšanās.** Ja tomēr notiek atmiņas izsmelšanas gadījums:

* partijas lielums tiek **samazināts uz pusi** un atkārtoti samazināts uz pusi katrā nākamajā atmiņas izsmelšanas gadījumā — katra nākamā veiksmīgi apstrādātā partija atceļ šo ierobežojumu par vienu pakāpi
* aktīvo pavedienu piešķīrumi tiek samazināti līdz 70 % no to pašreizējās vērtības, un piešķīrējs pārslēdzas uz konservatīvo stratēģiju, atkal atvieglojot ierobežojumus pēc virknes veiksmīgu piešķīrumu
* lielas slodzes apstākļos cauruļvads pāriet no `fused_gpu` uz `tiled_gpu` un, kā pēdējais līdzeklis, uz `cpu_fallback`

**Virtuālās mašīnas RAM (Jetson).** Pirms apstrādes CLI
, pamatojoties uz attēlu skaitu un debayer režīmu, aprēķina maksimālo uzņēmējdatora atmiņas apjomu un brīdina, ja RAM un failu atbalstītā apmaiņas atmiņa, visticamāk, būs nepietiekama, izvadot precīzas komandas apmaiņas atmiņas pievienošanai — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).

***

## Aprēķinu pielāgošanas uzraudzība

### Sistēmas diagnostika

`chloros-cli selftest` ir ātrākais veids, kā pārliecināties par to, ko redz aprēķinu slānis:

```bash
chloros-cli selftest
```

Tās 7 pārbaudes aptver versiju, porta pieejamību, aizmugures sistēmas palaišanu, `/api/test`, sistēmas informāciju, trokšņu noņemšanas modeļa klātbūtni un CUDA + trokšņu noņemšanas gatavību. 5. pārbaude tieši izdrukā aparatūras rindu:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

7. pārbaude izvada `CUDA: <bool>, Denoiser: <bool>` — abiem jābūt patiesiem, lai Texture Aware vispār būtu izmantojams.

### Aizmugures moduļa žurnāli

Stratēģija un darba procesu skaits tiek izvēlēts backendā katras darbības sākumā — nav nekāda CLI
banner, kas tos paziņotu. Ja kaut kas darbojas negaidīti (GPU ceļa atgriešanās, OOM, trokšņu slāpētājs, kas neiekraujas), tas parādās šīs sesijas backend žurnālā:

| Platforma | Žurnāla atrašanās vieta |
| --- | --- |
| **Linux
/ Jetson** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (viens fails katram palaišanas reizē) |
| **Linux
, ar CLI
palaists backend** | arī `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Reāllaika progresa rādītāji

Darbības laikā CLI
parāda reāllaika progresu katram pavedienam (detektēšana, analīze, apstrāde, eksportēšana), kas tiek pārraidīts, izmantojot Server-Sent Events — tas ir praktisks rādītājs, lai noteiktu, vai 3. pavediens ir šaurā vieta. Skatīt [Apstrādes cauruļvadu](processing-pipeline.md).

***

## Turpmākie soļi

* [Apstrādes cauruļvads](processing-pipeline.md) — 4 pavedienu cauruļvada arhitektūras izpratne
* [NVIDIA Jetson rokasgrāmata](../linux/nvidia-jetson-guide.md) — Jetson-specifiska ieviešana un optimizācija
* [CLI
: komandrinda](../CLI.md) — CLI
rokasgrāmata
* [CLI
atsauces materiāls](../reference/cli-reference.md) — Izsmeļošs komandu saraksts versijai 1.3.0
