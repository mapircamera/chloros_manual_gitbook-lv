# CLI: Komandu rinda

> **Pilnīga atsauce:**[CLI atsauce](reference/cli-reference.md) dokumentē**katras apakškommandas katru parametru** un ir optimizēta AI palīgiem — ielīmējiet tās URL savā palīgā un lūdziet darba komandu: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **Padoms AI rīkiem:** jebkura šīs rokasgrāmatas lapa ir pieejama kā neapstrādāts Markdown formāts, pievienojot `.md` tās URL-am (piem., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), un `https://mapir.gitbook.io/chloros/llms.txt` indeksē visu rokasgrāmatu izmantošanai ar lielajiem valodas modeļiem (LLM).

> **Vēlaties, lai jūsu palīgs pats palaistu Chloros?** `chloros-mcp` — MCP serveris, kas instalēts kopā ar Python SDK — darbojas ar „Claude Desktop”, „Claude Code”, „Cursor” un citiem MCP klientiem: skatiet [Chloros izmantošana ar AI palīgiem](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## Kas ir CLI


`chloros-cli` ir komandrindas interfeiss tam pašam apstrādes dzinējam, ko izmanto Chloros
datora lietojumprogramma. Tas ir viegls HTTP
-klients, kas darbojas ar Chloros
-backendu (vietējais serveris uz `127.0.0.1:5000`) — lielākā daļa komandu automātiski palaista backendu, tādēļ skriptam ir nepieciešams tikai viens `chloros-cli process …` izsaukums. Pēc tam backend turpina darboties, gaidot nākamo komandu: tas apstājas pēc `chloros-cli logout` (ja vien lietotne nav atvērta vai nav sākta izpilde vai kompilācija), pēc `chloros-cli backend stop` vai pats pēc 30 minūtēm, ja nav klienta, izpildes vai pieslēgtas ierīces. To parāda komanda `chloros-cli backend status`.

Tas darbojas **Windows
10/11 (x64)**un**Linux
(x86_64, kā arī NVIDIA Jetson arm64 ar JetPack 6)**, jebkurā terminālī, bez nepieciešamības pēc grafiskās lietotāja saskarnes. Pārbaudiet instalāciju ar:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

Komandu grupas īsumā:

* **Apstrāde un konts** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 valodas — skatiet [Atbalstītās valodas](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (tikai Linux
/Jetson)
* **Darbības režīma aparatūra** — `lattice` (LATTICE kameras vadība, vairāk nekā 45 apakškommandas), `daq pool-*` (DAQ gaismas sensori), `time-sync` (PTP)
* **Automatizācija** — `project` (saglabāta Chloros
projekta vadība bez grafiskās saskarnes, ieskaitot YAML uzņemšanas receptes)

Vērts zināt šādas globālās opcijas: `--port N` (aizmugures modulis, noklusējums `5000`), `-v/--verbose`, `--restart` (piekļuves servera piespiedu pārstartēšana), `--backend-exe PATH`. Pilnu sarakstu skatiet [CLI
atsauces materiālā](reference/cli-reference.md).

***

## Instalēšana

CLI
**ir iekļauts Chloros
instalētājā** visās platformās — atsevišķa CLI
lejupielāde nav pieejama. Lejupielādējiet instalētāju no [Lejupielādes](download.md) lapas.

### Windows


Instalētājs CLI
novieto šeit:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

un vienreiz pievieno šo mapi jūsu sistēmai `PATH` (atjaunināšana arī noņem dublētos ierakstus, ko atstājušas iepriekšējās instalācijas programmas) — pēc instalēšanas **atveriet jaunu termināli**, lai tiktu atpazīta atjauninātā `PATH`. Instalētājs instalācijas saknes direktorijā ievieto arī palaišanas skriptus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`), kā arī**Chloros
CLI
** sākuma izvēlnes saīsni, no kuras katra atver termināli ar `chloros-cli`, kas ir gatavs lietošanai.

### Linux


Instalējiet savai arhitektūrai atbilstošo `.deb`, izmantojot `apt` (nevis `dpkg -i`, kas neievēro Python
SDK
ieteiktās prasības):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

Tas instalē `chloros-cli` līdz `/usr/bin/chloros-cli` (jau atrodas `PATH`), aizmugurējo moduli un CLI
programmas, kas izpakotas `/usr/lib/chloros/backend/` un `/usr/lib/chloros/cli/` (`/usr/lib/chloros/chloros-backend` ir palaišanas programma, kas uzsāk aizmugurējo moduli), Arena SDK
izpildes vide, kas aizmugurējam modulim nepieciešama LATTICE kamerām, un Python
SDK
sistēmai `python3`. Sīkāku informāciju skatiet sadaļā [Instalēšana Linux
vidē](linux/linux-installation.md).

### Pārbaudiet

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Pieslēgšanās un licencēšana

Piekļuvei CLI
(un Python
SDK
) ir nepieciešams **maksas Chloros
+ plāns**— tas ir pieejams jebkurā maksas līmenī, bet bezmaksas līmenī nav. Minimālais ierobežojums tiek piemērots**servera pusē** ar backend palīdzību, nevis ar CLI
bināro failu: izslēgta lietotāja zvans tiek noraidīts ar kļūdu `401 AUTH_REQUIRED`, bet pieteiktam izsaukumam bezmaksas līmenī tiek atgriezts kļūdas kods `403 PLAN_UPGRADE_REQUIRED`, neatkarīgi no tā, vai tas nāk no `chloros-cli`, SDK
vai pašizstrādāta HTTP
klienta. Veiciet atjaunināšanu [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

Piesakieties **vienu reizi no katras ierīces**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**Paroles ar speciāliem simboliem**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` tiek izkropļota komandrindā (CLI
to atpazīst pēc 401 kļūdas un automātiski mēģina atkārtot, taču, izmantojot vienkāršās pēdiņas, šo problēmu var pilnībā novērst).
{% endhint %}

Sesija tiek saglabāta kešatmiņā ar `~/.chloros/user_session.json` un turpina darboties bezsaistē plāna papildlaika periodā (30 dienas mēneša plāniem, līdz termiņa beigām gada plāniem). `chloros-cli status` darbojas pat bez maksas plāna, tādēļ atteikuma iemesls vienmēr ir redzams.

{% hint style="danger" %}
**Plānojat bezinterfeisa darbu? Vispirms piesakieties.**Aizmugures procesa palaišanas komandas (`process`, `status`, `export-status`, …) izpilde**bez kešētas sesijas**nenotiek ātri — tā pāriet uz interaktīvo `Email:` / `Password:` uzvedni stdin. Tādēļ bez uzraudzības darbojošs cron uzdevums vai CI solis**iesaldēsies, gaidot ievadi**. Pirms kaut ko plānot, vienu reizi palaidiet `chloros-cli login EMAIL 'PASSWORD'` uz datora.
{% endhint %}

***

## Jūsu pirmais apstrādes cikls

Norādiet `process` uz ierakstu mapi — tas automātiski atpazīst Survey3
(`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` vai to kombināciju:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

Progresa plūsmas tiek pārraidītas tiešraidē katram cauruļvada pavedienam (detektēšana, analīze, apstrāde, eksportēšana), un veiksmīga izpilde beidzas ar ziņojumu par to, cik daudz attēlu produktu tika ierakstīti: `Image products written: N`, kam seko `(plus M clamp masks)`, ja izpildes laikā tika saglabātas arī [clamp maskas](output-image-formats.md#clamp-masks) (tās netiek uzskatītas par produktiem).

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->


Ja Chloros
backend jau darbojas (piemēram, darbvirsmas lietotnes), `process` to izmanto un tajā atver savu projektu. Tas atteiksies, kamēr lietotne ir atvērta projektā, kuru tā pati atvēra: veiciet apstrādi no lietotnes vai aizveriet šo projektu. Ja iepriekšējais izpildes cikls vēl nav pabeigts, tas gaida līdz 15 sekundēm, pirms atsakās. `--restart` piespiež sākt jaunu backend. Linux
sistēmā backend, ko palaida systemd pakalpojums, nekad netiek pārstartēts: `process` to izmanto tādu, kāds tas ir, un gadījumos, kad citādi tas tiktu pārstartēts (`--restart` vai imports, kas neko neatrada), tad tiek izvadīts `sudo systemctl restart <unit>` un programma tiek izbeigta `1`.

### Kur tiek saglabāti izvades rezultāti

`process` raksta **projekta mapē**, nevis jūsu ievades mapē:

* Ja nav norādīts `-o`: projekts tiek izveidots jūsu noklusējuma projekta mapē (kopīga ar GUI; to var pārvaldīt ar `get-project-folder` / `set-project-folder`; ja nekas nav iestatīts, `Documents/MAPIR/Chloros Projects` jūsu lietotāja mapē), nosaucot to pēc `-n/--project-name` vai laika zīmoga (`YYYYMMDD_HHMMSS`), ja tas nav norādīts.
* Ar `-o PATH`: šī mape **ir** projekta mape. Ja tajā jau atrodas `project.json`, tiek izveidots `_1`/`_2`… ar šādu paplašinājumu, nevis pārrakstot esošo failu. Relatīvais `PATH` tiek ņemts attiecībā pret mapi, no kuras jūs palaidāt `chloros-cli`.

{% hint style="info" %}
**Relatīvie ceļi (izmaiņas versijā 1.3.0).** Katrs ceļš, ko atver aizmugurējā programma — `-o`/`--output` jebkurai komandai (ieskaitot noklusējuma `output` uzņemšanas komandām), `--input`, `--burst-dir`, `--profile`, `--save` un `--mask` — tiek atrisināts, ņemot vērā mapi, no kuras tiek palaists `chloros-cli`, tādējādi `-o captures/` raksta uz `<current folder>/captures/`. Versijā 1.2.0 aizmugurējā sistēma tos atrisināja savā instalācijas mapē, kas beidzās ar kļūdu „Piekļuve liegta”. Attiecībā uz attālo aizmugurējo sistēmu (`CHLOROS_BACKEND_URL`) ceļi tiek nosūtīti tā, kā tie ir ievadīti, un tiek atrisināti uz šīs mašīnas.
{% endhint %}

{% hint style="warning" %}
**Windows
: saglabājiet projekta mapi īsu, ja vien nav ieslēgti gari ceļi.** Ja Windows
sistēmā gari ceļi ir atspējoti (Windows
noklusējums), neviens faila ceļš nedrīkst būt garāks par 260 rakstzīmēm, un produkti atrodas līdz aptuveni 130 rakstzīmēm zem projekta mapes. `process` to pārbauda pirms sākšanas: pārāk dziļa projekta mape tiek noraidīta ar ziņojumu **„Projekta mape ir pārāk dziļa šim datoram”**, tiek norādīta garākā projekta mape, kas der, un kā ieslēgt garos ceļus — nekas netiek apstrādāts. Izmantojiet īsāku `-o` (vai `set-project-folder`) vai ieslēdziet Windows
garos ceļus (instalētāja opcija **Ieslēgt Windows
garos ceļus**, kas pēc noklusējuma ir atzīmēta) un pārstartējiet Chloros
.
{% endhint %}

Projektā produkti ir sagrupēti **pēc kameras, tad pēc faila formāta**:

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

LATTICE kameras mape ir `LATT-<sensor>-<lens>-F<filter>` (atbilstoši uzņēmuma EXIF `Model`) un `<model>_<filter>` (piem., `Survey3N_RGN`) attēliem, kas uzņemti ar Survey3
. Formāta mapes nosaukumi seko pēc `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32` attiecībā uz `TIFF (32-bit, Percent)`.

Daudzspektrālajai M3C kamerai formāta mapē blakus produktu mapēm var parādīties arī mape `clamp_masks/` (piemēram, `tiff16/clamp_masks/Reflectance_Calibrated_Images/` un `tiff32/clamp_masks/Radiance_Images/`). Tajā atrodas starojuma un atstarošanas produktu maskas katram pikselim, kurās joslas atdalīšanas procesā negatīvās vērtības tika fiksētas uz nulli — skatīt [Fiksēšanas maskas](output-image-formats.md#clamp-masks). Nevienā produktu mapē nekad nav maskas.

{% hint style="info" %}
**Katrs eksportētais produkts saglabā AVOTA faila nosaukumu.**`capture_..._raw.tif` starojuma eksporta faila nosaukums joprojām ir `capture_..._raw.tif` — tas vienkārši atrodas `tiff32/Radiance_Images/`.**Mapes nosaukums identificē produktu, nevis faila nosaukumu**, tāpēc izmantojiet globālo meklēšanu pēc direktorija, nevis pēc `*radiance*` paplašinājuma.
{% endhint %}

### Opcijas, kuras jūs faktiski izmantosiet

| Opcija | Noklusējums | Darbība |
| --- | --- | --- |
| `-o, --output PATH` | noklusējuma projekta mape | Projekta mapes atrašanās vieta (skatīt iepriekš). |
| `-n, --project-name NAME` | laika zīmogs | Projekta nosaukums. |
| `--format FMT` | `TIFF (16-bit)` | Viens no `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros
neveic izšķirtspējas palielināšanu: visos 8 bitu attēlos dziļāks formāts tiek ierakstīts kā `TIFF (8-bit)`, un tas tiek norādīts vienu reizi. |
| `--indices NAME [NAME ...]` | nav | Eksportējami veģetācijas indeksi (sk. [Veģetācijas indeksi](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = neironu debayer, lēnāks, augstākā kvalitāte (Chloros
+, nepieciešams CUDA GPU: ja tā nav, tiek izmantots `standard`; netiek piemērots LATTICE uzņēmumiem, kuriem vienmēr tiek izmantots `standard`). |
| `--vignette / --no-vignette` | ieslēgts | Vignēšanas korekcija. |
| `--reflectance / --no-reflectance` | ieslēgts | Atstarošanas kalibrēšana; LATTICE gadījumā tas ir arī atstarošanas reizinājuma ieslēgšanas/izslēgšanas slēdzis. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Piespiedu cauruļvada sākumpunkts LATTICE TIFF failiem. |

Par visu pārējo — mērķa noteikšanas pielāgošanu, PPK, ekspozīcijas atskaites punktiem, masīva izlīdzināšanas karodziņiem — skatiet [CLI
atsauces sadaļu „`process`”](reference/cli-reference.md).

***

## Eksportējamo datu izvēle (LATTICE produkti)

LATTICE apstrāde vienā ciklā tiek sadalīta **visiem piemērojamajiem produktiem**. Četri katram produktam paredzētie slēdži**pēc noklusējuma ir ieslēgti**; izmantojiet `--no-` veidlapu, lai atslēgtu vienu:

| Pārslēgšanas slēdzis | Produkts |
| --- | --- |
| `--debayered` | Lineārā demosaika → `Debayered_Images/`: kalibrēšanas pakas tumšā nobīde noņemta, pēc tam tiek veikta malu ņemšana vērā demosaic, bez linearitātes korekcijas, flat field vai unmix — tas pats fails, ko uzņemšana saglabā savā `debayered` līmenī. Saglabāts sensora sākotnējā orientācijā (ierakstīts XMP). |
| `--preview` | Priekšskatījuma attēls (balansa iestatīšana + gamma; viltus krāsu izstiepšana multispektrālajiem attēliem) → `Preview_Images/` |
| `--radiance` | float32 starojuma intensitāte, W/m²/sr/nm → `Radiance_Images/` (vienmēr `tiff32/`) |
| `--reflectance` | uint16 atstarošanas koeficients, Pix4D-saderīgs → `Reflectance_Calibrated_Images/` |

Tikai RGB
-galvenās kameras vienmēr izstaro debayered + priekšskatījumu — starojums/atstarošanās pa atsevišķām joslām nav nozīmīgs platjoslas sensoram, tāpēc šie slēdži tām nedarbojas. Survey3
`.raw` ignorē slēdžus un seko standarta atstarošanās/mērķa ceļam.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (noklusējums `auto`) izvēlas atstarojuma atsauci: `auto` izmanto QA pārbaudi izturējušu kadrā esošu [kalibrēšanas mērķi](calibration-targets.md) kā absolūto atsauci un, ja mērķis nav klāt, izmanto DAQ gaismas sensora lejupvērsto starojuma sadalījumu (ρ = π·L/E), ja mērķis nav klāt (mērķis, kas iztur QA ar atzīmi, konkurē ar DAQ par izmērīto rezultātu); `target` ir stingrs (bez DAQ aizstāšanas); `daq` ir DAQ autoritatīvs. Mērītos mērķa skenējumus pa vienībām var piegādāt ar `--target-reflectance-dir`.

{% hint style="info" %}
**Reflektances pikseļu nolasīšana:**DN, kas nozīmē ρ = 1,0, ir**uz avotu** — LATTICE faili XMP ieraksta `Chloros:PixelScale=32768`; Survey3
faili izmanto 65535 un nesatur `Chloros:PixelScale` marķējumu (tie satur citus `Chloros:*` apstrādes marķējumus). Nolasiet marķieri un daliet ar to, nevis pieņemiet, ka tas ir konstants. Sīkāka informācija un viens apzināts gadījums bez mēroga ir atrodams [CLI
atsauces dokumentā](reference/cli-reference.md).
{% endhint %}

**Apstrāde vienmēr sākas no `raw`.** Atvasinātie produkti (eksportētie dati bez debayeringa, starojuma vai atstarošanas dati) nekad netiek atgriezti apstrādes ķēdē — to atkārtota importēšana un apstrāde divkārši piemērotu kalibrēšanas aprēķinus, tāpēc Chloros
tos izlaiž un par to informē. `--input-level` ir apzināti paredzēta „avārijas izeja”, ja patiešām ir nepieciešams piespiest sākuma punktu.

***

## Kad apstrāde neizdodas

Sākot ar versiju 1.2.0, `process` skaļi paziņo par kļūdu, nevis „izdodas”, neparādot nekādu rezultātu:

* Izpilde, kas **pieprasīja produktus, bet neierakstīja nevienu**— tikai `project.json` un `calibration_data.json` — izvada `Processing finished but wrote no image products.` un**iziet ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt. Parastie iemesli: ievades mape netika atpazīta kā uzņemums (pārbaudiet izkārtojumu un `--input-level`), vai arī visi pieprasītie produkti nebija piemērojami šīm kamerām (piemēram, pieprasot starojuma intensitāti/atstarošanas koeficientu no kamerām, kas atbalsta tikai RGB
).
* **Apzināta darbība, izmantojot tikai metadatus** (visi produkti atspējoti, bez `--indices`) joprojām tiek uzskatīta par veiksmīgu — tukša attēla izvade šajā gadījumā ir pareizais rezultāts.
* Veiciet atkārtotu apstrādi ar `--verbose` un pārbaudiet aizmugures žurnālu, meklējot rindas `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`, kurās ir paskaidrots, kādas kameras tika izlaistas.
* Windows
sistēmā, kurā ir atspējoti gari ceļi, projekta mape, kas ir pārāk dziļa, lai ietilptu 260 rakstzīmju ceļa ierobežojumā, tiek noraidīta **pirms** apstrādes sākuma, izmantojot garāko projekta mapi, kas ietilpst (skatiet [Kur nonāk izvades faili](#where-the-outputs-land)).
* Ierakstīšanas komandas (`lattice capture`, `lattice array-capture` un citas ierakstīšanas komandu grupas) nosauc katru failu, kuru tās nevarēja ierakstīt, un iziet ar kodu `1`; atkārtotā ierakstīšana tiek pārtraukta, kad disks ir pilns.

Izbeigšanas kodi: `0` — veiksmīga izpilde · `1` — vispārēja kļūda · `2` — argumenta kļūda · `130` — pārtraukta ar Ctrl+C.

***

## Veģetācijas indeksi

Izvadiet komandu `--indices` ar vienu vai vairākiem iepriekš iestatītiem nosaukumiem; katrs indekss tiek saglabāts savā `<INDEX>_Index_Images/` mapē:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

22 iepriekš iestatīti nosaukumi, kurus `process --indices` pieņem:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

Projektam tiek pievienots nosaukums, kas tajā vēl nav iekļauts. Nosaukums, kas nav iepriekš iestatīts vai ko kameru joslas nevar izveidot, tiek izlaists, par to izvadot brīdinājumu. Ja CLI
nevar nolasīt projekta pašreizējo indeksa sarakstu, tas atstāj sarakstu nemainīgu un par to brīdina, nevis to aizstāj.

{% hint style="warning" %}
**Pastāv trīs indeksa saraksti — tos nedrīkst sajaukt.**GUI izvēlnē „Projekta iestatījumi” ir 27 formulas (pievieno `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — šīs piecas ir pieejamas tikai GUI un**nav** derīgas `--indices`). Reāllaika/offline komanda `lattice index --preset` izmanto savu atsevišķo 22 iestatījumu sarakstu. Formulas un joslu aprēķini ir aprakstīti sadaļā [Multispektrālo indeksu formulas](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## DAQ gaismas sensori: īss pārskats

`daq pool-*` sērija vadMAPIR
DAQ spektrālos sensorus (DAQ-U caur USB, DAQ-M caur BLE, DAQ-E caur Ethernet) izmantojot backend pastāvīgo pūlu — GUI, CLI
un SDK
visi izmanto vienu reāllaika rokturi. **`pool-*` ir atbalstītais DAQ ceļš piegādātajā CLI
**; citas `daq` apakškommandas, uz kurām varat sastapties, ir tikai MAPIR
iekšējai lietošanai paredzētas saskarnes, un tās beidzas ar skaidru kļūdas ziņojumu, kas norāda uz `pool-*`.

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

`pool-record` bez `--duration` darbojas līdz `pool-record --stop`; noklusējuma izvades katalogs ir `~/Documents/DAQ Live View/` **aizmugurējās sistēmas datorā**. Kapacitātes korekcijas profils tiek izvēlēts savienošanās brīdī (`--cap-id`, aizmugures sistēmas noklusējums `sunshine_cosine`) un to var mainīt reāllaikā, izmantojot `pool-set-cap` — kapacitātes profili un sensora kalibrētais diapazons ir aprakstīti šīs rokasgrāmatas nodaļās par datu ieguvi (DAQ).

{% hint style="warning" %}
**DAQ-E uz datora ar vairākiem tīkla interfeisiem:** pirmā `pool-connect --eth` automātiskā atklāšana pēc sistēmas uzsākšanas var neizdoties pat tad, ja sensors darbojas pareizi. `--eth-host <ip-or-hostname>` ir uzticamākais risinājums — izmantojiet to, ja atklāšana nedod rezultātus.
{% endhint %}

***

## LATTICE kameras, PTP un projektu automatizācija

`lattice` komandu grupa (vairāk nekā 45 apakškomandas) aptver LATTICE kameru darbību no sākuma līdz galam: atklāšanu, atsevišķus uzņēmumus, pastāvīgas sinhronizētas matricas ar GUI viedās sagatavošanas savienošanas plūsmu, tiešsaistes pārlūka priekšskatījumu, izlīdzināšanu, indeksu aprēķinus un servera tīkla kartes diagnostiku. Neliels ieskats:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Papildus tam: `chloros-cli time-sync` sniedz ziņojumu par PTP „grandmaster”, ko vada Chloros
saimniekdators (LATTICE kameras un DAQ-E sensori darbojas kā tā „slaves”, lai nodrošinātu laika zīmogus starp ierīcēm), un „`chloros-cli project`” atver saglabātu Chloros
projektu un bez grafiskās saskarnes vada tā kameras, masīvus un sensorus — ieskaitot skriptētus YAML datu ieguves scenārijus.

Šīs trīs grupas (`lattice`, `project`, `daq pool-*`) ir arī vienīgās, kas atbalsta `CHLOROS_BACKEND_URL`, lai vadītu **attālinātu** aizmuguri; galvenās komandas vienmēr ir vērstas uz lokālo datoru.

Pilnīgas instrukcijas atrodamas šīs rokasgrāmatas nodaļās par LATTICE; visi parametri ir aprakstīti [CLI
atsauces sadaļā](reference/cli-reference.md).

***

## Problēmu novēršana: 5 biežāk sastopamās

| Simptoms | Risinājums |
| --- | --- |
| `Login required` vai plānotais uzdevums iestrēdzis pie `Email:` uzvednes | Vienreiz palaidiet `chloros-cli login EMAIL 'PASSWORD'` šajā datorā — komandas bez kešētas sesijas tiks izpildītas interaktīvā režīmā, nevis nekavējoties izraisīs kļūdu. |
| `backend unreachable` | Palaižiet Chloros
darbvirsmas lietotni vai paši palaidiet aizmugurējo programmu: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` operētājsistēmā Windows
(`backend` mape jūsu instalācijas mapē; tā nav `PATH`), `chloros-backend` uz Linux
. `chloros-cli backend status` parāda, vai kāds no tiem darbojas; `lattice`/`project`/`daq pool-*` nekad to nepalaiž. Ja jūs tos norādāt uz attālo backend, pārbaudiet `CHLOROS_BACKEND_URL`. |
| Masīva savienojums bloķēts: `FRAMES WILL DROP` / `Reduce ROI to enable` | Hostinga tīkla kartes (NIC) uztveršanas gredzens atiestatīts uz noklusējumiem — galvenais iemesls, kāpēc iepriekš darbojusies iekārta vairs nevēlas izveidot savienojumu, parasti pēc tīkla kartes draivera atjaunināšanas. Palaidiet `chloros-cli lattice network --fix` no **privilēgētas** termināļa (vai iestatiet `ReceiveBufferLen=256`, `PendingReceives=64`); skatiet atsauces sadaļu *Host NIC Setup &amp; Tuning*. |
| `daq` apakškommanda iziet: „nepieciešams pilnais DAQ pakotne…“ | Izmantojiet komandas `daq pool-*`, kas vadītu to pašu sensoru caur aizmuguri: savienošana, datu plūsma, ierakstīšana, ierobežojumu izvēle, kā arī DAQ-E tīkla iestatījumi, pārstartēšana un programmaparatūras atjauninājumi (vai `chloros_sdk.connect_daq_sensor()` no Python
). |
| Jetson parāda brīdinājumu par pagriezienu pirms lielām mapēm | Pievienojiet failu atbalstītu pagriezienu — CLI
parāda precīzas `fallocate`/`swapon` komandas, kas jāizpilda. |

***

## Palīdzības saņemšana

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Katrs parametrs, katra apakškommanda:** [CLI
atsauces materiāls](reference/cli-reference.md)
* **Python
ekvivalents:** [Python
SDK
](api-python-sdk.md) un [SDK Reference](reference/sdk-reference.md)
* **Atbalsts:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
