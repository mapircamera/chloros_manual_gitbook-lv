# CLI : Komandrinda

> **Pilnīga atsauces informācija:**[CLI Atsauce](reference/cli-reference.md) apraksta**katras apakškommandas katru parametru** un ir optimizēts AI palīgiem — ielīmējiet tās URL savā palīgā un lūdziet darba komandu: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Padoms AI rīkiem:** jebkura šīs rokasgrāmatas lapa ir pieejama kā neapstrādāts Markdown, pievienojot `.md` tās URL (piem., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), un `https://mapir.gitbook.io/chloros/llms.txt` indeksē visu rokasgrāmatu izmantošanai lielvalodas modeļos (LLM).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.2.0 build so the version line reads "Chloros CLI 1.2.0" -->
## Kas ir CLI

`chloros-cli` ir komandrindas saskarne tam pašam apstrādes dzinējam, ko izmanto Chloros datora lietotne. Tā ir viegla HTTP klienta programma, kas darbojas virs Chloros aizmugurējās daļas (vietējais serveris uz `127.0.0.1:5000`) — lielākā daļa komandu automātiski palaista aizmugurējo daļu, tāpēc skriptam pietiek ar vienu `chloros-cli process …` izsaukumu.

Tā darbojas uz **Windows 10/11 (x64)**un**Linux (x86_64, kā arī NVIDIA Jetson arm64 ar JetPack 6)**, jebkurā terminālī, bez nepieciešamības pēc grafiskās lietotāja saskarnes. Pārbaudiet instalāciju ar:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```

Komandu grupas īsumā:

* **Apstrāde un konts** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 valodas — skatiet [Atbalstītās valodas](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (tikai Linux/Jetson)
* **Reāla aparatūra** — `lattice` (LATTICE kameras vadība, vairāk nekā 45 apakškomandas), `daq pool-*` (DAQ gaismas sensori), `time-sync` (PTP)
* **Automatizācija** — `project` (saglabāta Chloros projekta vadība bez grafiskās saskarnes, ieskaitot YAML uzņemšanas receptes)

Vērts zināt šādas globālās opcijas: `--port N` (backend ports, noklusējums `5000`), `-v/--verbose`, `--restart` (backend piespiedu atkārtota palaišana), `--backend-exe PATH`. Pilnu sarakstu skatiet [CLI atsauces dokumentā](reference/cli-reference.md).

***

## Instalācija

CLI **ir iekļauts Chloros instalācijas programmā** visās platformās — atsevišķa CLI lejupielāde nav pieejama. Lejupielādējiet instalācijas programmu no [Lejupielādes](download.md) lapas.

### Windows

Instalētājs ievieto CLI šādā vietā:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

un pievieno šo mapi jūsu sistēmai `PATH` — pēc instalēšanas **atveriet jaunu termināli**, lai tiktu atpazīts atjauninātais `PATH`. Instalētājs instalācijas saknes direktorijā ievieto arī palaišanas skriptus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) instalācijas saknes direktorijā, kā arī**Chloros CLI** sākuma izvēlnes saīsni, no kuras katra atver termināli ar `chloros-cli`, kas ir gatavs lietošanai.

### Linux

Instalējiet `.deb` savai arhitektūrai:

```bash
# Linux x86_64
sudo dpkg -i chloros-amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Tas instalē `chloros-cli` līdz `/usr/bin/chloros-cli` (jau ir `PATH`) un aizmugurējo moduli līdz `/usr/lib/chloros/chloros-backend`, kā arī LATTICE kamerām nepieciešamo Arena SDK izpildes vidi. Sīkāku informāciju skatiet sadaļā [Linux instalācija](linux/linux-installation.md).

### Pārbaudiet

```bash
chloros-cli --version    # "Chloros CLI 1.2.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Pieslēgšanās un licencēšana

Lai piekļūtu CLI (un Python SDK), ir nepieciešams **maksas Chloros+ plāns**— tas ir pieejams jebkurā maksas līmenī; bezmaksas līmenī tas nav pieejams. Ierobežojums tiek piemērots**servera pusē** ar backend palīdzību, nevis ar CLI bināro failu: izslēgta lietotāja zvans tiek noraidīts ar kļūdas kodu `401 AUTH_REQUIRED`, bet bezmaksas plāna lietotāja zvans — ar kļūdas kodu `403 PLAN_UPGRADE_REQUIRED`, neatkarīgi no tā, vai tas nāk no `chloros-cli`, SDK vai pašizstrādāta HTTP klienta. Veiciet atjaunināšanu [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Piesakieties **vienu reizi katrā datorā**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.2.0 build showing the license tier line -->
{% hint style="warning" %}
**Paroles ar speciāliem simboliem**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` tiek izkropļota čaulā (CLI to atpazīst kā 401 kļūdu un automātiski mēģina atkārtot, taču, izmantojot vienkāršās pēdiņas, šo problēmu var pilnībā novērst).
{% endhint %}

Sesija tiek saglabāta kešatmiņā ar nosaukumu `~/.chloros/user_session.json` un turpina darboties bezsaistē plāna papildlaika periodā (30 dienas mēneša plāniem, līdz termiņa beigām gada plāniem). `chloros-cli status` darbojas pat bez maksas plāna, tādēļ atteikuma iemesls vienmēr ir redzams.

{% hint style="danger" %}
**Plānojat bezinterfeisa darbu? Vispirms ieietiet sistēmā.**Aizmugures procesa palaišanas komandas (`process`, `status`, `export-status`, …) izpilde**bez kešētas sesijas**nenotiek ātri — tā pāriet uz interaktīvo `Email:` / `Password:` uzvedni uz stdin. Tādējādi bez uzraudzības darbojošs cron uzdevums vai CI solis**iesaldēsies, gaidot ievadi**. Pirms kaut ko plānot, vienreiz palaidiet `chloros-cli login EMAIL 'PASSWORD'` uz datora.
{% endhint %}

***

## Jūsu pirmais apstrādes cikls

Norādiet `process` uz ierakstu mapi — tas automātiski atpazīst Survey3 (`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` vai to kombināciju:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Progresa plūsmas tiek pārraidītas tiešraidē katram cauruļvada pavedienam (atklāšana, analīze, apstrāde, eksportēšana), un veiksmīga izpilde beidzas ar ziņojumu par to, cik daudz attēlu produktu tika ierakstīti (`Image products written: N`).

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N" summary line -->
### Kur nonāk izvades dati

`process` raksta **projekta mapē**, nevis jūsu ievades mapē:

* Ja nav `-o`: projekts tiek izveidots jūsu noklusējuma projekta mapē (kopīga ar GUI; to pārvalda ar `get-project-folder` / `set-project-folder`, rezerves variants `~/Chloros Projects`), un to nosauc pēc `-n/--project-name` vai laika zīmoga (`YYYYMMDD_HHMMSS`), ja nosaukums nav norādīts.
* Ja ir norādīts `-o PATH`: šī mape **ir** projekta mape. Ja tajā jau atrodas `project.json`, tiek izveidota mape ar papildu nosaukumu `_1`/`_2`… tā vietā, lai pārrakstītu esošo.

Projektā produkti tiek grupēti **pēc kameras, tad pēc faila formāta**:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Kameras mape ir `LATT-<sensor>-<lens>-F<filter>` attēlam „LATTICE” (atbilstoši attēla EXIF datiem `Model`) un `<model>_<filter>` (piem., `Survey3N_RGN`) attēliem, kas uzņemti ar Survey3. Formāta mapes nosaukumi ir šādi: `--format`, `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32`, lai aizstātu `TIFF (32-bit, Percent)`.

{% hint style="info" %}
**Katram eksportētajam produktam tiek saglabāts AVOTA faila nosaukums.**`capture_..._raw.tif` starojuma eksporta failam joprojām ir nosaukums `capture_..._raw.tif` — tas vienkārši atrodas `tiff32/Radiance_Images/` mapē.**Produktu identificē mape, nevis faila nosaukums**, tāpēc izmantojiet globālo meklēšanu pēc direktorija, nevis pēc paplašinājuma `*radiance*`.
{% endhint %}

### Opcijas, kuras jūs faktiski izmantosiet

| Opcija | Noklusējums | Darbība |
| --- | --- | --- |
| `-o, --output PATH` | noklusējuma projekta mape | Projekta mapes atrašanās vieta (skatīt iepriekš). |
| `-n, --project-name NAME` | laika zīmogs | Projekta nosaukums. |
| `--format FMT` | `TIFF (16-bit)` | Viens no `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | nav | Eksportējamie veģetācijas indeksi (skatīt [Veģetācijas indeksi](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = neironu debayer, lēnāks, augstākā kvalitāte (Chloros+, NVIDIA GPU). |
| `--vignette / --no-vignette` | ieslēgts | Vignetes korekcija. |
| `--reflectance / --no-reflectance` | ieslēgts | Atstarošanas kalibrēšana; LATTICE gadījumā tas ir arī atstarošanas reizinājuma ieslēgšanas/izslēgšanas slēdzis. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Piespiedu cauruļvada sākumpunkta iestatīšana LATTICE TIFF failiem. |

Par visu pārējo — mērķa noteikšanas pielāgošanu, PPK, ekspozīcijas atskaites punktiem, matricas izlīdzināšanas marķieriem — skatiet [`process` sadaļu CLI atsauces materiālā](reference/cli-reference.md).

***

## Eksportējamo datu izvēle (LATTICE produkti)

LATTICE apstrāde vienā ciklā tiek sadalīta **uz visiem piemērojamajiem produktiem**. Četri katram produktam paredzētie slēdži**pēc noklusējuma ir ieslēgti**; izmantojiet veidlapu `--no-`, lai atslēgtu vienu:

| Slēdzis | Produkts |
| --- | --- |
| `--debayered` | Lineārā demosaika → `Debayered_Images/` |
| `--preview` | Priekšskatīšanas parādīšana (balansa iestatīšana + gamma; viltus krāsu izstiepšana multispektrālajiem attēliem) → `Preview_Images/` |
| `--radiance` | float32 starojuma intensitāte, W/m²/sr/nm → `Radiance_Images/` (vienmēr `tiff32/`) |
| `--reflectance` | uint16 atstarošanas koeficients, Pix4D-saderīgs → `Reflectance_Calibrated_Images/` |

RGB galvenās kameras vienmēr izstaro tikai debayered + priekšskatījumu — starojums/reflektance pa joslām nav nozīmīga platjoslas sensoram, tādēļ šie slēdži tām nedarbojas. Survey3 `.raw` ignorē šos slēdžus un izmanto standarta atstarošanas/mērķa ceļu.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (noklusējums `auto`) izvēlas atstarojuma atsauci: `auto` izveido kvalitātes pārbaudi izturējušu [kalibrēšanas mērķi](calibration-targets.md) kā absolūto atsauci un, ja mērķis nav klāt, izmanto DAQ gaismas sensora lejupvērstā starojuma sadalījumu (ρ = π·L/E); `target` darbojas stingri (bez DAQ aizstāšanas); `daq` ir DAQ autoritatīvs. Mērītos mērķa skenējumus uz vienu vienību var nodrošināt ar `--target-reflectance-dir`.

{% hint style="info" %}
**Reflektances pikseļu nolasīšana:**DN, kas nozīmē ρ = 1,0, ir**uz avotu** — LATTICE faili XMP formātā iezīmē ar `Chloros:PixelScale=32768`; Survey3 faili izmanto 65535 (un nesatur `Chloros:*` tagus). Nolasiet marķējumu un daliet ar to, nevis pieņemiet, ka tas ir konstants. Sīkāka informācija un viens apzināti izvēlēts gadījums bez mēroga ir atrodams [CLI atsauces dokumentā](reference/cli-reference.md).
{% endhint %}

**Apstrāde vienmēr sākas no `raw`.** Atvasinātie produkti (eksportētie debayeringa, starojuma vai atstarošanas dati) nekad netiek atgriezti apstrādes ķēdē — to atkārtota importēšana un apstrāde divkārši piemērotu kalibrēšanas aprēķinus, tāpēc Chloros tos izlaiž un par to informē. `--input-level` ir apzināti paredzēta „avārijas izeja”, ja patiešām ir nepieciešams piespiest sākuma punktu.***

## Kad apstrāde neizdodas

Sākot ar versiju 1.2.0, `process` skaidri ziņo par kļūdu, nevis “izdodas”, neparādot nekādu rezultātu:

* Izpilde, kas **pieprasīja produktus, bet neierakstīja nevienu**— tikai `project.json` un `calibration_data.json` — izvada `Processing finished but wrote no image products.` un**iziet ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt. Parastie iemesli: ievades mape netika atpazīta kā uzņemšana (pārbaudiet izkārtojumu un `--input-level`), vai arī katrs pieprasītais produkts nebija piemērojams šīm kamerām (piemēram, pieprasot starojuma/reflektances no kamerām, kas atbalsta tikai RGB).
* **Apzināta darbība, izmantojot tikai metadatus** (visi produkti atspējoti, bez `--indices`) joprojām tiek uzskatīta par veiksmīgu — tukša attēla izvade šajā gadījumā ir pareizais rezultāts.
* Veiciet atkārtotu izpildi ar `--verbose` un pārbaudiet aizmugures žurnālu, meklējot rindas ar `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`, kurās ir paskaidroti izlaišanas iemesli katrai kamerai atsevišķi.

Izbeigšanas kodi: `0` — veiksmīga izpilde · `1` — vispārēja kļūda · `2` — argumenta kļūda · `130` — pārtraukta ar Ctrl+C.

***

## Veģetācijas indeksi

Ievadiet `--indices` ar vienu vai vairākiem iepriekš iestatītiem nosaukumiem; katrs indekss tiek saglabāts savā `<INDEX>_Index_Images/` mapē:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI NDRE GNDVI
```

22 iepriekš iestatīti nosaukumi, kurus `process --indices` pieņem:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

{% hint style="warning" %}
**Ir trīs indeksu saraksti — tos nedrīkst sajaukt.**GUI izvēlnē „Projekta iestatījumi” ir 27 formulas (pievienotas `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — šīs piecas formulas ir pieejamas tikai GUI un**nav** derīgas `--indices`). Reāllaika/bezsaistes komanda `lattice index --preset` izmanto savu atsevišķo 22 iestatījumu sarakstu. Formulas un joslu aprēķini ir aprakstīti sadaļā [Multispektrālo indeksu formulas](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## DAQ gaismas sensori: īss pārskats

`daq pool-*` sērija vadīta MAPIR DAQ spektrālos sensorus (DAQ-U caur USB, DAQ-M caur BLE, DAQ-E caur Ethernet) izmantojot backend pastāvīgo pūlu — GUI, CLI un SDK visi izmanto vienu aktīvo rokturi. **`pool-*` ir atbalstītais DAQ ceļš piegādātajā CLI**; citas `daq` apakškommandas, uz kurām varat sastapties, ir tikai MAPIR iekšējā avota virsma, un tās beidzas ar skaidru kļūdu, kas norāda uz `pool-*`.

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record` bez `--duration` darbojas līdz `pool-record --stop`; noklusējuma izvades katalogs ir `~/Documents/DAQ Live View/` **aizmugurējās sistēmas datorā**. Kondensatora korekcijas profils tiek izvēlēts savienošanās brīdī (`--cap-id`, aizmugures datoriem noklusējuma profils ir `sunshine_cosine`) un to var mainīt reāllaikā, izmantojot `pool-set-cap` — kapacitātes profili un sensora kalibrētais diapazons ir aprakstīti šīs rokasgrāmatas DAQ nodaļās.

{% hint style="warning" %}
**DAQ-E uz datora ar vairākiem tīkla interfeisiem (NIC):** pirmā `pool-connect --eth` automātiskā atklāšana pēc sistēmas uzsākšanas var neizdoties pat tad, ja sensors darbojas pareizi. `--eth-host <ip-or-hostname>` ir uzticamākais risinājums — izmantojiet to, ja atklāšana nedod rezultātus.
{% endhint %}

***

## LATTICE kameras, PTP un projektu automatizācija

`lattice` sērija (vairāk nekā 45 apakškomandas) aptver LATTICE kameru darbību no sākuma līdz galam: atklāšanu, atsevišķus uzņēmumus, pastāvīgi sinhronizētus masīvus ar GUI viedās sagatavošanas savienošanas plūsmu, tiešsaistes pārlūka priekšskatījumu, izlīdzināšanu, indeksa aprēķinus un servera tīkla kartes diagnostiku. Piemērs:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Papildus tam: `chloros-cli time-sync` ziņo par PTP galveno serveri, ko vada Chloros galvenais dators (LATTICE kameras un DAQ-E sensori darbojas kā tā pakļautie, lai nodrošinātu laika zīmogus starp ierīcēm), un `chloros-cli project` atver saglabāto Chloros projektu un bez lietotāja iejaukšanās vada tā kameras, masīvus un sensorus — ieskaitot skriptētus YAML datu ieguves receptes.

Šīs trīs ģimenes (`lattice`, `project`, `daq pool-*`) ir arī vienīgās, kas atbalsta `CHLOROS_BACKEND_URL`, lai vadītu **attālinātu** aizmuguri; galvenās komandas vienmēr ir vērstas uz lokālo datoru.

Pilnīgas instrukcijas atrodamas šīs rokasgrāmatas LATTICE nodaļās; visi parametri ir aprakstīti [CLI atsauces dokumentā](reference/cli-reference.md).

***

## Problēmu novēršana: 5 biežāk sastopamās

| Simptoms | Risinājums |
| --- | --- |
| `Login required` vai plānotais uzdevums „iesaldējas” pie `Email:` uzvednes | Vienreiz palaidiet `chloros-cli login EMAIL 'PASSWORD'` šajā datorā — komandas bez kešētas sesijas tiks izpildītas interaktīvā režīmā, nevis nekavējoties izraisīs kļūdu. |
| `backend unreachable` | Palaižiet Chloros darbvirsmas lietotni vai tieši palaidiet aizmugures bināro failu (`chloros-backend`). Ja norādāt `lattice`/`project`/`daq pool-*` uz attālo backend, pārbaudiet `CHLOROS_BACKEND_URL`. |
| Masīva savienojums bloķēts: `FRAMES WILL DROP` / `Reduce ROI to enable` | Uzņēmējdatora tīkla kartes (NIC) uztveršanas gredzens atiestatīts uz noklusējumiem — galvenais iemesls, kāpēc iepriekš darbojusies iekārta vairs nevēlas izveidot savienojumu, parasti pēc tīkla kartes draivera atjaunināšanas. Palaidiet `chloros-cli lattice network --fix` no **paaugstinātu tiesību** termināļa (vai iestatiet `ReceiveBufferLen=256`, `PendingReceives=64`); skatiet rokasgrāmatas sadaļu *Host NIC Setup &amp; Tuning*. |
| `daq` apakškommanda iziet: „nepieciešams pilnais DAQ pakotne…“ | Sagaidāms piegādātajās versijās — kompilētais CLI ietver tikai `daq pool-*` ģimeni, kas aptver savienošanu, datu plūsmu, ierakstīšanu un kanālu izvēli. Izmantojiet `pool-*` (vai `chloros_sdk.connect_daq_sensor()` no Python). |
| „Jetson” parāda brīdinājumu par apmaiņu pirms lielām mapēm | Pievienojiet failu balstītu apmaiņu — CLI parāda precīzas `fallocate`/`swapon` komandas, kas jāizpilda. |

***

## Palīdzības saņemšana

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Katrs parametrs, katra apakškommanda:** [CLI atsauces materiāls](reference/cli-reference.md)
* **Python ekvivalents:** [Python SDK](api-python-sdk.md) un [SDK atsauces materiāls](reference/sdk-reference.md)
* **Atbalsts:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
