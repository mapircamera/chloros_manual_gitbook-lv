# Dinamiskā aprēķinu pielāgošana

Chloros 1.2.0 izmanto aparatūras atpazīšanu un automātisku apstrādes stratēģijas izvēli. Apstrādes dzinējs pielāgojas jūsu aparatūrai — sākot no „Jetson Orin Nano” līdz darbstacijai ar vairākiem GPU — bez jebkādas manuālas konfigurācijas.

***

## Kā tas darbojas

Kad Chloros tiek palaists, tas veic sistēmas profilēšanu:

1. **Atpazīst operētājsistēmu** — Windows vai Linux
2. **Identificē procesora kodolus un kopējo RAM**

3.**Noteic GPU klātbūtni** — NVIDIA CUDA atbalsts, VRAM, modelis
4. **Identificē Jetson modeli** (ja piemērojams) — izmantojot `/proc/device-tree/model`
5. **Pārbauda termiskos sensorus** (Jetson) — temperatūru ņemot vērā apstrādei
6. **Izvēlas aprēķinu stratēģiju** — pamatojoties uz visu atklāto aparatūru
7. **Automātiski konfigurē darba vienību skaitu, cauruļvada tipu un atmiņas sadalījumu**

Atklātais profils tiek saglabāts sesijas cache atmiņā un uz diska, tādējādi turpmākie izpildes cikli sākas ātrāk:

| Platforma | Cache profils |
| --- | --- |
| **Linux / Jetson** | `~/.config/chloros/system_config.json` (atbilst `XDG_CONFIG_HOME`) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Dzēsiet šo failu, lai piespiestu veikt jaunu atpazīšanu — tas ir noderīgi pēc GPU vai papildu RAM pievienošanas. Chloros arī automātiski veic atkārtotu atpazīšanu, ja kešatmiņa tika rakstīta ar nesaderīgu vecāku versiju.

***

## Aprēķinu stratēģijas

Chloros izvēlas vienu no trim aprēķinu stratēģijām atkarībā no jūsu aparatūras:

| Stratēģija | Izvēlas, ja | Darbinieki | Izpildītājs | Konveijers |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**| CUDA GPU, kas ziņo par**12 GB+ VRAM**(uz Jetson vienotās atmiņas, nepieciešama arī kopējā koplietošanas RAM vismaz 12 GB) | `min(4, VRAM ÷ 4GB)`, vismaz 2 —**ierobežots līdz 2 uz Jetson** | `ProcessPoolExecutor` (spawn) | `fused_gpu` |
| **`GPU_SINGLE`**| CUDA GPU ar**2–12 GB VRAM**| 3 (I/O pārklāšanās; GPU piekļuve sērijveidā ar semaforu).**1 (sekvenciāli) uz „Jetson” ar mazāk nekā 12 GB RAM** | `ProcessPoolExecutor` (spawn); sekvenciāli procesā uz „Jetson” ar mazu RAM | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | Bez CUDA GPU vai ar mazāk nekā 2 GB VRAM | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

`GPU_PARALLEL` darba vienību formulas piemēri: 12 GB VRAM → 3 darba vienības, 16 GB un vairāk → 4 darba vienības, jebkurš Jetson → 2 darba vienības.

Paralēlisms tiek īstenots ar Python standarta `concurrent.futures`: GPU stratēģijas izmanto `ProcessPoolExecutor` ar **spawn** (katrs darba process ir atsevišķs process ar savu CUDA kontekstu — `fork` kopētu jau inicializētu CUDA stāvokli un sabojātu apakšprocesus), savukārt CPU stratēģija izmanto `ThreadPoolExecutor`. Chloros neizmanto nekādu trešās puses izstrādātu sadalīto sistēmu (piemēram, Ray).

### Apstrādes ceļu veidi

* **`fused_gpu`** — Pilnīgs GPU apstrādes ceļš. Debayer, korekcijas un indeksēšanas operācijas tiek izpildītas uz GPU vienā apvienotā ciklā. Augstākā caurlaidspēja, prasa visvairāk VRAM.
* **`tiled_gpu`** — Atmiņai efektīvs GPU ceļš. Apstrādā attēlus flīzēs, lai tie ietilptu ierobežotajā GPU atmiņā. Mazāka caurlaidspēja, bet darbojas ierīcēs ar ierobežotu atmiņu.
* **`cpu_fallback`** — Apstrāde tikai ar CPU, izmantojot daudzpavedienu paralēlismu. Tiek izmantota, ja nav pieejams NVIDIA GPU, kā arī kā pēdējā iespēja, ja abas GPU apstrādes ķēdes nedarbojas.

Vides izpildes rezerves ķēde vienmēr ir `fused_gpu` → `tiled_gpu` → `cpu_fallback`.

***

## Stratēģijas manuāla pārrakstīšana

Iestatiet vides mainīgo `CHLOROS_STRATEGY`, lai piespiedu kārtā izmantotu konkrētu stratēģiju — tas ir eksperta „avārijas izejas” risinājums gadījumiem, kad automātiskā atpazīšana izvēlas risinājumu, kas nav piemērots jūsu situācijai (piemēram, lai atstātu GPU brīvu citiem uzdevumiem):

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Mainīgais tiek salīdzināts, neņemot vērā lielos un mazos burtus; viss, kas nav viens no trim nosaukumiem, tiek ignorēts, un automātiskā noteikšana turpinās kā parasti. Pārrakstīšanas gadījumā Chloros joprojām izvēlas darba procesu skaitu jūsu vietā:

| Pārrakstīšana | Izmantotais darba procesu skaits |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Ieteicams to iestatīt katrai komandai atsevišķi, nevis pastāvīgi, lai parastās darbības turpinātu automātiski pielāgoties.

***

## Platformas specifiska darbība

| Platforma | Stratēģija | Darbinieki | Apstrādes ķēde | Piezīmes |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8 GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekvenciāls) | Atmiņas taupīšanas režīms, pa vienam attēlam |
| **Jetson Orin NX 8 GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sekvenciāla) | Kopējā RAM, kas mazāka par 12 GB, nosaka sekvenciālu apstrādi |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (vienlaicīgi) | Ieteicamā malējā ierīce — Jetson ierobežots līdz 2 darba procesiem |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (vienlaicīgi) | Maksimālā malas ierīces veiktspēja (arī Jetson ierobežots līdz 2 darba vienībām) |
| **Galddators ar 8 GB GPU** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 darba vienības pārklājas I/O, kamēr semafors serializē piekļuvi GPU |
| **Datoru ar 12 GB+ GPU** | `GPU_PARALLEL` | 3–4 | `fused_gpu` (vienlaicīgi) | Optimāla darbvirsmas veiktspēja: 12 GB → 3 darba procesi, 16 GB+ → 4 |
| **Tikai CPU sistēma** | `CPU_PARALLEL` | fiziskie kodoli − 1 (min. 2) | `cpu_fallback` | GPU nav nepieciešams, izmanto pavedienu kopu |

{% hint style="info" %}
**Jetson vienotā atmiņa**: Jetson ierīces kopīgi izmanto GPU un CPU atmiņu. Jetson Orin NX 16 GB ziņo par ~15,3 GB VRAM, taču tā ir tā pati fiziskā RAM, ko izmanto operētājsistēma un CPU procesi. Tāpēc 16 GB un lielākas Jetson ierīces atbilst `GPU_PARALLEL` prasībām tāpat kā 12 GB un lielākas galddatoru GPU, tomēr tām ir noteikts ierobežojums — 2 darba procesi — GPU, darba procesi un to CUDA konteksti katram darba procesam izmanto to pašu kopīgo pūlu.
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

Atsevišķie GPU sistēmai rezervē tikai 0,5 GB, jo tie neizmanto sistēmas RAM. Jetson profili rezervē daudz vairāk un nosaka zemākus ierobežojumus — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Dinamiska GPU atmiņas sadale

Chloros izmanto [4 pavedienu apstrādes cauruļvadu](processing-pipeline.md):

* **

1. pavediens** (Atpazīšana) — attēla ielāde, EXIF analīze, mērķa atpazīšana
* **

2. pavediens** (kalibrēšana) — atstarojuma kalibrēšanas aprēķini
* **

3. pavediens** (apstrāde) — GPU debayer, vinjetes korekcija, indeksa aprēķins
* **

4. pavediens** (eksportēšana) — failu rakstīšana, metadatu iegūšana

1., 2. un 4. pavedieni patērē maz GPU resursu; 3. pavediens ir visresursietilpīgākais. Kad iepriekšējie procesa posma pavedieni pabeidz darbu, to GPU resursi tiek **pārdalīti starp atlikušajiem aktīvajiem pavedieniem**, tādējādi 3. pavediens procesa gaitā pakāpeniski saņem arvien vairāk atmiņas.

### Piešķiršanas posmi

| Posms | Aktīvās pavedienu | GPU atmiņas sadale |
| --- | --- | --- |
| **Sākumā** | 1, 2, 3, 4 | Sadalīta starp visiem pavedieniem, lielākā daļa tiek piešķirta 3. pavedienam |
| **Sākuma vidusposms** | 2, 3, 4 | 1. pavediena daļa pārdalīta |
| **Vidusposma beigas** | 3, 4 | 1. un 2. pavediena daļas tiek novirzītas 3. un 4. pavedienam |
| **Vēlā fāze** | 3 vai 4 | Pēdējais aktīvais pavediens saņem maksimālo piešķīrumu |

Šos skaitļus nosaka divi noteikumi:

* Pavediens, kas ir **vienīgais** aktīvais, saņem savā profilā noteikto maksimālo piešķīrumu.
* Ja ir aktīvs vairāk nekā viens *resursietilpīgs* GPU uzdevums, katra resursietilpīgā uzdevuma bāzes piešķīrums tiek sadalīts starp tiem (nekad nepazeminot to zem konfigurētā minimuma).

Vērtība, kas faktiski tiek izmantota izpildes laikā, ir **mazāka** no platformas profila piešķīruma un GPU atmiņas monitora reālā ieteikuma, tādējādi noslogota karte vienmēr uzvar pār optimistisku profilu.***

## Tekstūru apzināta apstrāde

Tekstūru ņemot vērā debayer (**tikai Chloros+** — `--debayer texture-aware`) izmanto AI/ML trokšņu noņemšanas modeli, kam vienai kopijai nepieciešami aptuveni 1,75 GB VRAM FP16 formātā, tādējādi tas patērē daudz vairāk GPU atmiņas nekā standarta metode:

* Sistēmas ar **mazāk nekā 7 GB VRAM**apstrādā „Texture Aware”**sinhronā ciklā, vienu attēlu pēc otra** — tajās nevar ietilpt vairāki modeļa eksemplāri, un darba grupas izmantošana tikai palielinātu konkurenci
* Sistēmas ar **7 GB un vairāk VRAM** var apstrādāt „Texture Aware” vienlaicīgi, lai gan ar mazāku darba procesoru skaitu salīdzinājumā ar standarta metodi
* Uz **Jetson**, „Texture Aware” vienmēr tiek piesaistīts vienam darba procesam, un zema enerģijas patēriņa modeļos (Nano, Orin Nano) tas automātiski piemēro arī GPU frekvences ierobežojumu — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)***

## Siltuma vadība (Jetson)

Jetson ierīcēm ir siltuma ierobežojumi, it īpaši slēgtās telpās vai gaisā. Chloros uzrauga Jetson ierīces iebūvētos temperatūras sensorus un automātiski pielāgo partiju izmērus:

| Temperatūra | Reakcija |
| --- | --- |
| **&lt; 70 °C** | Normāla darbība — pilna ātruma režīms |
| **70 °C** (brīdinājums) | Partijas lielums pakāpeniski samazinās (no 100 % līdz 50 % temperatūras diapazonā no 70 °C līdz 80 °C) |
| **80 °C** (Kritiska) | Agresīva jaudas ierobežošana (no 50 % līdz 0 % diapazonā no 80 °C līdz 90 °C) |
| **90 °C** (Izslēgšanās) | Pilnībā aptur GPU apstrādi |

Galddatoru sistēmās ar atbilstošu dzesēšanu termiskā jaudas ierobežošana tiek iedarbināta reti.

***

## Atmiņas slodzes pārvaldība

Chloros nepārtraukti uzrauga GPU atmiņu apstrādes laikā un reaģē trīs līmeņos.

**Partijas lieluma noteikšana.** Partija sākas ar 8 attēliem, reizinātiem ar platformas koeficientu no augstāk minētajām tabulām. Chloros pēc tam pārbauda brīvo VRAM, rezervē 20 % no tās PyTorch paša pārvaldības vajadzībām un pieņem, ka uz vienu 12 MP attēlu nepieciešami aptuveni 100 MB GPU atmiņas — partijas lielums ir mazākais no diviem: no atmiņas atvasinātais limits vai platformas bāzes vērtība. Tas nekad nenokrīt zem 1.**Preventīva samazināšana.**Ja**VRAM izmantojums pārsniedz 85%**, partiju izmēri tiek samazināti, pirms rodas kļūmes.**Piešķīruma samazināšana katram pavedienam.** Tā kā reālā izmantošana pieaug, katra pavediena GPU budžets tiek samazināts: ×0,75, ja izmantošana pārsniedz 80 %, ×0,5, ja pārsniedz 90 %. Uzraudzības sliekšņi ir 70 % (konservatīvs), 85 % (normālais darbības limits) un 95 % (OOM risks).**OOM atkāpšanās un atjaunošanās.** Ja tomēr notiek atmiņas izsmelšanas gadījums:

* partijas lielums tiek **samazināts uz pusi** un atkārtoti samazināts uz pusi katrā nākamajā atmiņas izsmelšanas gadījumā — katra nākamā veiksmīgi apstrādātā partija atceļ šo ierobežojumu par vienu pakāpi
* aktīvo pavedienu piešķīrumi tiek samazināti līdz 70 % no to pašreizējās vērtības, un piešķīrējs pārslēdzas uz konservatīvo stratēģiju, atkal atvieglojot ierobežojumus pēc virknes veiksmīgu piešķīrumu
* lielas slodzes apstākļos cauruļvads pāriet no `fused_gpu` uz `tiled_gpu` un, kā pēdējais līdzeklis, uz `cpu_fallback`

**Viesdatora RAM (Jetson).** Pirms apstrādes CLI aprēķina maksimālo uzņēmējdatora atmiņas apjomu, pamatojoties uz attēlu skaitu un debayer režīmu, un brīdina, ja RAM un failu balstītā apmaiņas atmiņa, visticamāk, būs nepietiekama, izvadot precīzas komandas apmaiņas atmiņas pievienošanai — skatiet [NVIDIA Jetson rokasgrāmatu](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).***

## Aprēķinu pielāgošanas uzraudzība

### Sistēmas diagnostika

`chloros-cli selftest` ir ātrākais veids, kā pārliecināties par to, ko redz aprēķinu slānis:

```bash
chloros-cli selftest
```

Tās 7 pārbaudes aptver versiju, portu pieejamību, aizmugures sistēmas palaišanu, `/api/test`, sistēmas informāciju, trokšņu noņemšanas modeļa klātbūtni un CUDA + trokšņu noņemšanas gatavību. 5. pārbaude tieši izdrukā aparatūras rindu:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

7. pārbaude izvada `CUDA: <bool>, Denoiser: <bool>` — abām pārbaudēm jābūt patiesām, lai Texture Aware vispār būtu izmantojams.

### Aizmugurējās sistēmas žurnāli

Stratēģija un darba procesu skaits tiek izvēlēts aizmugurējās sistēmas iekšienē katras darbības sākumā — nav nekāda CLI paziņojuma, kas tos atklātu. Ja kaut kas darbojas negaidīti (GPU ceļa atgriešanās pie rezerves varianta, atmiņas pārpildījums (OOM), trokšņu noņemšanas modulis, kas neiekraujas), tas parādās šīs sesijas backend žurnālā:

| Platforma | Žurnāla atrašanās vieta |
| --- | --- |
| **Linux / Jetson** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (viens fails katram palaišanas gadījumam) |
| **Linux, CLI — palaistais backend** | arī `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Reāllaika progresa rādītāji

Darbības laikā CLI rāda reāllaika progresu katram pavedienam (detektēšana, analīze, apstrāde, eksportēšana), kas tiek pārraidīts, izmantojot Server-Sent Events — tas ir praktisks rādītājs, lai noteiktu, vai 3. pavediens ir šaurā vieta. Skatīt [Apstrādes cauruļvadu](processing-pipeline.md).

***

## Turpmākie soļi

* [Apstrādes cauruļvads](processing-pipeline.md) — 4 pavedienu cauruļvada arhitektūras izpratne
* [NVIDIA Jetson rokasgrāmata](../linux/nvidia-jetson-guide.md) — Jetson specifiska ieviešana un optimizācija
* [CLI : Komandrinda](../CLI.md) — CLI rokasgrāmata
* [CLI atsauces materiāls](../reference/cli-reference.md) — Izsmeļošs komandu saraksts versijai 1.2.0
