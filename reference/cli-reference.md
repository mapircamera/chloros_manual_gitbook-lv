# Chloros CLI Atsauce

**Versija:**

1.2.0**Izveidots:**2026-07-29 19:19 ·**Pārskatīts:** 2026-08-30**Mērķauditorija:** Optimizēts LLM lietošanai; cilvēkam saprotams.**Darbības joma:** Katra lietotāja, ar opcijām un piemēriem, kurus var kopēt un ielīmēt.

Šis dokuments ir pilnīga atsauces rokasgrāmata par `chloros-cli` komandrindas rīku, kas iekļauts MAPIR Chloros. Tas ir apzināti izsmeļošs, lai LLM (vai cilvēks) varētu izveidot jebkuru atbalstītu darba plūsmu, izmantojot zemāk sniegtos sarakstus, neizpētot avota kodu.

Ja jums nepieciešama tikai galvenā informācija, pārejiet uz:
- [Piecu minūšu ātrsākums](#five-minute-quickstart)
- [LATTICE kameras pirmās savienošanas darba plūsma](#lattice-camera-first-connect-workflow)
- [DAQ sensora pirmās savienošanas darba plūsma](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Ierakstīšanas režīmi, ierakstītāji un pārstrāde bezsaistē](#capture-modes-recorders--offline-reprocess)

---

## Konvencijas

- Visām komandām priekšā ir prefikss `chloros-cli`. Uz Windows binārā faila nosaukums ir `chloros-cli.exe`; uz Linux /Jetson tas ir `chloros-cli`.
- Neobligātie argumenti tiek attēloti kā `--flag`. Obligātie pozicionālie argumenti tiek parādīti bez iekavām.
- Ja ir norādīta noklusējuma vērtība, izlaižot karodziņu, tiek izmantota šī vērtība.
- CLI ir viegls HTTP klients, kas darbojas ar Chloros backend (Flask serveris uz `127.0.0.1:5000`). Backend tiek automātiski palaists ar lielāko daļu komandu. `CHLOROS_BACKEND_URL=<url>` norāda uz **`lattice`**,**`project`**un**`daq pool-*`** komandu grupas uz attālo backend — galvenās komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) apzināti fiksē `http://127.0.0.1:<port>` un to ignorē (IPv4 literāls novērš Windows&#x27; `localhost`→`::1` ~2 s sodu par katru pieprasījumu). Skatīt [Vides mainīgie](#environment-variables).
- Lai veiktu visiem SDK / CLI izsaukumiem (izpildiet `chloros-cli login` vienu reizi katrā datorā; tiek saglabāts kešatmiņā `~/.chloros/`).
- Piemēros izmantoti ceļi Linux; vietnē Windows aizstājiet `/home/user/...` ar `C:/Users/.../...`.

---

## Augstākā līmeņa kopsavilkums

```
chloros-cli [global options] COMMAND [command options]
```

### Vispārējās opcijas

| Opcija | Apraksts |
| --- | --- |
| `--backend-exe PATH` | Pārrakstīt automātiski atklāto aizmugurējās sistēmas izpildāmo failu. |
| `--port N` | Aizmugurējās sistēmas HTTP ports (noklusējums: `5000`). |
| `-v, --verbose` | Iespējot detalizētu izvadi. |
| `--restart` | Piespiedu atkārtota aizmugurējās programmas palaišana (pārtrauc visus darbojošos `backend_server.py` procesus). |
| `--version` | Izvada versiju (`Chloros CLI 1.2.0`). |
| `--help` | Parādīt augstākā līmeņa palīdzību. |

### Komandu rādītājs

| Komanda | Mērķis |
| --- | --- |
| [`process`](#chloros-cli-process) | Apstrādā mapu ar „Survey3” vai „LATTICE” ierakstiem no sākuma līdz beigām. |
| [`login`](#chloros-cli-login) | Autentificē šo datoru ar „Chloros” kontu. |
| [`logout`](#chloros-cli-logout) | Dzēst kešatmiņā saglabātās autentifikācijas datus. |
| [`status`](#chloros-cli-status) | Parādīt pašreizējo licences / autentifikācijas statusu. |
| [`export-status`](#chloros-cli-export-status) | Rāda „Thread-4“ eksportēšanas gaitu reāllaikā, kamēr tiek izpildīta komanda `process`. |
| [`language`](#chloros-cli-language) | Iestatīt vai parādīt „CLI” parādīšanas valodu (atbalstītas 38 valodas). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Noklusējuma projekta mape (kopīga ar GUI). |
| [`update`](#chloros-cli-update) | Pārbaudīt un instalēt „CLI” atjauninājumus (Linux /Jetson). |
| [`selftest`](#chloros-cli-selftest) | Sistēmas diagnostika + ātrās pārbaudes. |
| [`time-sync`](#chloros-cli-time-sync) | PTP galvenā servera statuss / kontrole. |
| [`lattice`](#chloros-cli-lattice) | LATTICE kameras vadība un attēlu uzņemšana (vairāk nekā 45 apakškommandas). |
| [`daq`](#chloros-cli-daq) | DAQ spektrālo sensoru vadība (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Saglabāta „Chloros” projekta atvēršana un vadība (kameras + DAQ). |

---

## Instalēšana

`chloros-cli` ir iekļauts „Chloros“ darbvirsmas instalētājā visās atbalstītajās platformās — atsevišķa CLI lejupielāde nav pieejama. Instalējot platformas paketi, `chloros-cli` tiek pievienots jūsu `PATH` kopā ar darbvirsmas lietotni un aizmugurējā binārā faila, ko tā vada.

Jaunākās lejupielādes: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Instalētājs ietver arī ērtus palaišanas skriptus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`), kas atver gatavuCLI apvalku; tie ir aprakstīti [CLI lietotāja rokasgrāmatā](../CLI.md) un šeit netiek atkārtoti.

### „Windows” (.exe)

1. Lejupielādējiet „Windows” instalētāju no lejupielādes lapas.
2. Palaižiet `Chloros-Setup-x.y.z.exe` un izpildiet vedņa norādījumus. Noklusējuma instalācijas ceļš ir `C:\Program Files\Chloros\` (CLIs atrodas `C:\Program Files\Chloros\cli\`, ko instalētājs pievieno PATH).
3. Atveriet jaunu termināli (`cmd.exe`, PowerShell vai „Windows” termināli), lai tiktu izvēlēts atjauninātais `PATH` .

```powershell
chloros-cli --version
```

Instalētājs automātiski pievieno `chloros-cli.exe` jūsu sistēmai `PATH` un iekļauj tajā „Arena” SDK izpildes vidi, kas nepieciešama LATTICE kamerām.

### Linux amd64 (.deb)

Ubuntu 22.04 LTS vai jaunākām versijām / uz Debian balstītām x86_64 darbstacijām.

> **Ubuntu 20.04 netiek atbalstīta.** Paketes atkarību saraksts ir atvasināts no
> tā, ar ko backend faktiski veido saites, un tajā ir iekļauts `libc6 (>= 2.34)`;
> focal piegādā glibc 2.31. `apt` neļauj instalēt, nevis ļauj instalācijai neizdoties
> darbības laikā.

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

.deb instalē:
- `chloros-cli` līdz `/usr/bin/chloros-cli`
- kompilēto backend uz `/usr/lib/chloros/chloros-backend`
- „Arena“ SDK izpildes vidi (LATTICE kamerām)
- Trokšņu noņemšanas modeļus, kalibrēšanas paketes un atjauninājumu kanāla konfigurāciju

### „Linux“ arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Tāds pats izkārtojums kā amd64 .deb, ar CUDA versiju, kas pielāgota Jetson Orin / Orin NX / Orin Nano.

### Autentificēšanās vienu reizi katrai ierīcei

Katrai platformai ir nepieciešama vienreizēja pieteikšanās Chloros+ pirms SDK / CLI izsaukumi sāk darboties:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Pieslēgšanās dati tiek saglabāti kešatmiņā `~/.chloros/user_session.json`.

### Instalācijas pārbaude

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Nepieciešama Chloros+ abonēšana.**Lai izmantotu CLI, ir nepieciešams aktīvs Chloros+ plāns.**Copper**ir sākuma līmenis Chloros+ — katram maksas Chloros+ līmenim ir piekļuve CLI / SDK; tikai bezmaksas**Iron** līmenim tā nav. (Plānu identifikatoru atbilstība: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Pārejiet uz augstāku līmeni šeit: [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Šo ierobežojumu piemēro backend, nevis tikai CLI: pieprasījums ar atzīmi SDK / CLI bez apmaksāta plāna tiek noraidīts ar kļūdas kodu `403 PLAN_UPGRADE_REQUIRED`, neatkarīgi no tā, vai tas nāk no `chloros-cli`, Python SDK vai pašizstrādāta HTTP klienta. Izslēgtajam lietotājam tiek parādīts kļūdas kods `401 AUTH_REQUIRED`. Piekļuve darbojas bezsaistē plāna papildlaika periodā (30 dienas mēnesī, līdz gada plāna termiņa beigām) un tiek pārtraukta, kad šis periods beidzas; `chloros-cli status` turpina darboties, lai iemesls būtu redzams (tas ir vienīgais maršruts SDK / CLI, kas ir atbrīvots no līmeņu ierobežojuma — `GET /api/license-status`).

---

## Piecu minūšu ātrs sākums

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

---

## `chloros-cli process`

Apstrādājiet attēlu mapi, izmantojot pilnu Chloros apstrādes ķēdi (mērķa noteikšana → kalibrēšana → vinjete → atstarošanās → indeksa eksports).

### Kopsavilkums

```
chloros-cli process INPUT [OPTIONS]
```

### Pozicionālie argumenti

| Arguments | Apraksts |
| --- | --- |
| `INPUT` | Ceļš uz ievades mapi, kas satur `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE) vai `.dng` failus. |

### Vispārējās opcijas

| Opcija | Noklusējums | Apraksts |
| --- | --- | --- |
| `-o, --output PATH` | jauna mape ar laika zīmogu jūsu noklusējuma projekta ceļā (`~/Chloros Projects`, ja nav konfigurēts citādi) | Projekta mape, kuru izveidot vai atkārtoti izmantot. Ja mapē jau atrodas `project.json`, tad tā vietā tiek izveidota `_1`/`_2` mape, nevis pārrakstīta esošā. |
| `-n, --project-name NAME` | automātiski (laika zīmogs) | Projekta nosaukums. |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` izmanto Chloros+ neironu debayeru; lēnāks, bet nodrošina augstāku kvalitāti. |
| `--vignette / --no-vignette` | `--vignette` | Vignette korekcija. |
| `--reflectance / --no-reflectance` | `--reflectance` | Atstarošanas kalibrēšana (izmanto paneļa mērķi, ja tāds ir atrasts, NIST sērijas kalibrēšanu LATTICE gadījumā). LATTICE multispektrālajam režīmam šī opcija darbojas arī kā atstarojuma **produkta** ieslēgšanas/izslēgšanas slēdzis — skatiet [Eksportēšanas slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | izslēgts | Piemērot PPK GNSS korekcijas no sidecar failiem. |
| `--exposure-pin-1 MODEL` | izslēgts | Fiksēt „Survey3” divkameru iekārtas „pin-1” modeli. |
| `--exposure-pin-2 MODEL` | izslēgts | Fiksēt „pin-2” modeli. |
| `--recal-interval SECONDS` | 0 | Piespiest atkārtoti izpildīt kalibrēšanas aprēķinus ik pēc N sekundēm no uzņemšanas laika. |
| `--timezone-offset HOURS` | local | Pārrakstīt laika zonas nobīdi, kas iekļauta izvades metadatos. |
| `--format FORMAT` | `TIFF (16-bit)` | Viens no `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | nav | Veģetācijas indeksi (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Piespiedu cauruļvada sākumpunkts LATTICE TIFF failiem (tas neietekmē Survey3 .raw failus). Tāpat arī avārijas izeja, kas ļauj apstrādāt ierakstus, kuros **nav raw** failu — skatīt [Kā izskatās ierakstu mape](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | ieslēgts | Izvadīt lineāro debayeringa rezultātu (`Debayered_Images`). Skatīt [Eksportēšanas slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | ieslēgts | Izvada ekrāna priekšskatījumu (`Preview_Images`): RGB = baltā balanss (DAQ-gaismas avots, ja pieejams, citādi pelēkā pasaule) + gamma; multispektrāls = viltus krāsu izstiepums. |
| `--radiance / --no-radiance` | ieslēgts | Izvada float32 starojuma intensitāti (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | Atskaites punkts LATTICE atstarošanas produktam: `auto` = QA pārbaudi izturējis mērķis kadrā ir absolūtais etalons, DAQ lejupvērstā starojuma (ρ = π·L/E) rezerves variants; `target` = stingrs (bez DAQ aizstāšanas); `daq` = DAQ-autoritatīvs. Skatīt [Eksportēšanas slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | nav | Katras vienības **izmērīto** mērķa atstarošanas skenējumu katalogs (`<serial>.csv`); neveiksmes gadījumā izmanto nominālos T3/T4P spektrus. |
| `--array-alignment / --no-array-alignment` | ieslēgts | LATTICE masīvi: piemēro katras uzņemšanas `Chloros:Alignment*` XMP failā iezīmēto moduļu savstarpējo saskaņošanu visiem apstrādātajiem produktiem (debayering / priekšskatījums / starojums / atstarojums / indekss). Neveic darbību attēliem bez šīm birkām. |
| `--array-alignment-crop / --no-array-alignment-crop` | apgriešana | Apgriež saskaņotos eksportētos datus līdz masīva kopējai pārklāšanās zonai, lai visiem moduļiem būtu viena kopīga platība; `--no-…` saglabā pilnu sensora laukumu (melna aizpildīšana ārpus avota). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Pārparaugošana saskaņošanas deformācijas dēļ. `nearest` saglabā precīzus avota dinamiskos diapazonus (bez radiometrisko vērtību sajaukšanas starp pikseļiem). |

### Mērķa noteikšanas opcijas

| Parametrs | Apraksts |
| --- | --- |
| `--min-target-size PIXELS` | Minimālais paneļa mērķa izmērs (px) detektoram. |
| `--target-clustering 0-100` | Klasterizācijas jutība. |
| `--target / --targets` | Apstrādāt ievades mapi tikai kā mērķa-tikai paneļa mērķi (izlaist apsekojuma atklāšanu). |

### Piemēri

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### Eksportēšanas slēdži katram produktam (LATTICE multispektrālais)

LATTICE apstrāde vienā ciklā sadala rezultātus **uz visiem piemērojamajiem produktiem**. Četri slēdži katram tipam — `--debayered`, `--preview`, `--radiance`, `--reflectance` — visi ir**pēc noklusējuma ieslēgti**; izmantojiet veidlapu `--no-<type>`, lai kādu no tiem atslēgtu. RGB galvenās kameras vienmēr raida tikai debayered + priekšskatījumu (bez starojuma/atstarošanas pa joslām), tāpēc `--radiance`/`--reflectance` tām ir bezdarbīgas. Pārslēgšanas opcijas tiek ignorētas attiecībā uz Survey3 `.raw` (kas seko standarta atstarošanas/mērķa ceļam). *(Vecais `--radiometric-output {reflectance,radiance,sensor-response}` karodziņš tika **noņemts** un aizstāts ar šiem slēdžiem; `sensor-response` līmeņa vairs nav.)*

| Produkts | Izvade | Nepieciešama DAQ lejupvērstā starojuma reģistrēšana? |
| --- | --- | --- |
| `--debayered` | Lineārā demosaika (`Debayered_Images`). | Nē. |
| `--preview` | Ekrāna priekšskatījums (`Preview_Images`): „RGB” = WB + gamma; „multispec” = viltus krāsu izstiepšana. | Nē. |
| `--radiance` | float32 W/m²/sr/nm no pilnās radiometriskās ķēdes (`Radiance_Images`). | Nr. |
| `--reflectance` | uint16 atstarošanas koeficients ρ (`32768` = 1,0), Pix4D-saderīgs. | **Jā**, ja vien to nefiksē kvalitātes pārbaudi izturējis mērķis kadrā (skatīt zemāk). |

`--reflectance-source` izvēlas atstarošanas atsauci:**`auto`**(noklusējums) padara QA pārbaudi izturējušu mērķi kadrā par**absolūto atsauci**— mērķim piesaistītās empīrisko līniju ķēdes tiek krustnovērtētas uz atsevišķiem paneļiem, un tiek piemērots mērītais uzvarētājs — ja mērķis nav klāt vai kvalitātes pārbaude nav izieta, tiek izmantots DAQ lejupvērstais sadalījums (ρ = π·L/E);**`target`**ir stingrs (bez DAQ aizstāšanas);**`daq`**izvēlas rīkoties saskaņā ar DAQ autoritatīvo uzvedību. Mērķa ģeometrija (ArUco / fiksēts ROI / sloksne) tiek ņemta no projekta mērķa konfigurācijas; `--target-reflectance-dir DIR` glabā uz vienību attiecinātos**izmērītos** skenējumus (`<serial>.csv`), kurus meklē pēc mērķa vienības sērijas numura/QR, izmantojot nominālos T3/T4P spektrus kā rezerves variantu.

DAQ atstarojuma ceļš automātiski nosaka **laika zīmogam atbilstošo lejupvērsto starojumu**no ierakstītā**`.daq`**(DAQ-U/M/E)**vai DAQ-M nativā `.csv`**, kas atrodas kopā ar attēliem. Ja kameras vai DAQ kalibrēšanas pakete nav saglabāta vietējā kešatmiņā, apstrādes ķēde**to automātiski lejupielādē no AWS** pirmajā lietošanas reizē (vienreiz nepieciešams interneta savienojums; tiek saglabāts kešatmiņā ar nosaukumu `~/.chloros/`).

#### Reflektances pikseļu nolasīšana (Pix4D / Metashape / jūsu pašu skripti)

Reflektance tiek saglabāta kā vesels skaitlis DN, un **DN, kas nozīmē ρ = 1,0, ir atkarīgs no avota kameras**:

| Avots | ρ = 1,0 ir | Kā noteikt |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēts XMP `Chloros:PixelScale=32768`. |
| „Survey3“ | `65535` (apgriezts pie ρ 1,0) | Nav `Chloros:*` XMP marķējumu — šī neesamība *ir* pazīme. |

**Izlasiet `Chloros:PixelScale` un daliet ar to**, nevis pieņemiet, ka tā ir konstante. Tag ir definēts uint16 domēnā, tādēļ tas paliek nemainīgs `32768` visos izvades formātos, kuros tiek veikta mērogošana — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` un `TIFF (32-bit, Percent)` ir pašraksturojošas (vispirms normalizējiet saglabāto datu tipu atpakaļ uz uint16: ×257 no 8bitu, ×65535 no float).

> **Vienā gadījumā mērogs nav iekļauts, kā paredzēts.** Kad 8-bitu avota ieraksts (BayerRG8) tiek rakstīts kā 8-bitu TIFF, apstrādes ķēde *nogriež* līdz 0..255, nevis pārskalē, tādējādi katra vērtība, kas pārsniedz ρ≈0,008, tiek izlīdzināta līdz 255, un failam netiek piešķirts nekāds mērogs. „Chloros” apzināti izlaiž gan `Chloros:PixelScale`, gan `MicaSense:RadiometricCalibration` tuplu, un reģistrē iemeslu. **Ja LATTICE atstarošanas failā šī birka nav, nepieņemiet mērogu — veiciet atkārtotu eksportu 16-bitu vai 32-bitu formātā**, nevis dalot pikseļus, kas nekad nav bijuši dalāmi.

#### EXIF dati, kas tiek pārnesti uz eksportēto failu

`process` uz katru produktu kopē avota uzņēmuma **GPS bloku un tā ExifIFD**, tādējādi
eksportā tiek iekļauti `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` un
`CameraSerialNumber` kopā ar ģeoreferencēšanu.

**`FocalLength` nav fakultatīvs fotogrammetrijai.** Pix4D aprēķina zemes parauga attālumu, izmantojot
fokusa attālumu un augstumu; ja šī birka nav pieejama, tiek izmantots ļoti nepareizs mērogs. Vienā
lidojumā virs apelsīnu dārza ar 49 uzņēmumiem trūkstošā tagu dēļ 411 m × 160 m liela teritorija tika pārveidota par rekonstruētu
47,8 km × 13 km lielu teritoriju — 455 MP ortofoto, kurā galvenokārt bija „nodata”, kas tika interpretēts kā mozaīkas problēma un
BigTIFF problēmu, pirms kāds pārbaudīja GSD. Ja jūsu ortofoto iznāk neiespējamā
mērogā, vispirms palaidiet `exiftool -FocalLength` pār eksportēto produktu.

Šī kopija apzināti **nav** `-all:all`: IFD0 strukturālās birkas sabojā LATTICE izvadi, kad
tās tiek kopētas, un `ExifImageWidth` / `ExifImageHeight` ir izslēgti, jo tie apraksta
*avota* uzņēmumu — eksportam, kura izmērs kādreiz tika mainīts, citādi būtu dimensijas,
kas ir pretrunā ar paša rastra izmēriem. XMP tiek rakstīts tieši, nevis kopēts, jo ExifTool
izmet XMP tagus, kas radīti vienā un tajā pašā izsaukumā, kad XMP bloks tiek kopēts (kas izslēgtu MAPIR
kalibrēšanas tagus).

### Kur tiek saglabāti izvades faili

Produkti tiek saglabāti **projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Kameras mape ir `LATT-<sensor>-<lens>-F<filter>` attēlam LATTICE (atbilstoši uzņēmuma EXIF
`Model`) un `<model>_<filter>` kamerai „Survey3” — divām kamerām, kurām ir kopīgs sensors un filtrs, bet atšķiras
objektīvs, tiek saglabātas atsevišķas mapju struktūras, jo atšķiras vinjetēšana, redzes lauks un izkliede. Formāta
mapes nosaukums ir šāds: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32` attiecībā uz
`TIFF (32-bit, Percent)`.

> **Katrs eksportētais produkts saglabā AVOTA faila nosaukumu.** Radiance eksporta fails
> `capture_…_raw.tif` joprojām sauc par `capture_…_raw.tif` — tas vienkārši atrodas
> `tiff32/Radiance_Images/`. **Produktu identificē mape, nevis faila nosaukums**, tāpēc, veicot globālo meklēšanu
> pēc `*radiance*.tif`, nekas netiek atrasts; tā vietā veiciet saskaņošanu pēc direktorija.

### Gaismas sensora ieraksti — kalibrēti `.daq` + `.csv`

`process` apstrādā arī `.daq` ierakstus jūsu ievades mapē, un tam **nav**
vajadzīgi nekādi attēli: DAQ-U / DAQ-M / DAQ-E, kas lido atsevišķi, nodrošina pilnīgu
ierakstīšanu, un mape, kurā atrodas tikai `.daq` faili, ir derīga ievade.

DAQ ierakstus var veikt **bez** tā kalibrēšanas — tieši to pēc noklusējuma dara publiski pieejamie
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) ierakstītāji
(`record_daq.py`) dara pēc noklusējuma: tie ieraksta neapstrādātos sensora skaitījumus un marķē failu tā, lai
Chloros iegūtu šī sensora rūpnīcas kalibrāciju **pēc sērijas numura** (vispirms no vietējās kešatmiņas,
pēc tam no MAPIR mākoņa) un to piemēro. `process` rezultātu ieraksta atpakaļ:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` katrā nolasījumā ietver vienu rindu: UTC laika zīmogs, integrācijas laiks, kopējā jauda,
fotopiskais/skotopisko luksu, PPFD (un tā sadalījumu pa zilo, zaļo un sarkano), maksimālo viļņa garumu, tad
pilno spektru uz paša sensora viļņa garuma režģa. `.daq` atkārtoti importē datus, tos
otru reizi nekalibrējot.

Veiksmīgas darbības gadījumā tiek ziņots par `Light-sensor products written: N (calibrated .daq + .csv)`.
Teksts iekavās apraksta to, kas faktiski tika ierakstīts, tātad tas skan
`(RAW COUNTS — this sensor has no calibration bundle)` sensora bez paketes gadījumā un
`(N calibrated, M raw counts)` mapes gadījumā, kurā atrodas abi. Aizmugurējās sistēmas paša
`[DAQ-EXPORT]` un `[RUN-SUMMARY]` virsraksti ir veidoti pēc tā paša principa — neviens no
šiem trim nevar saukt nekalibrētu eksportu par kalibrētu.

DAQ-U / DAQ-M / DAQ-E ieraksts, kura kalibrēšanas pakete nevar tikt iegūta — jūs esat
bezsaistē vai šim sensoram failā nav kalibrācijas — tiek **izlaists ar iemeslu**
`[DAQ-EXPORT]` rindā un nekad netiek izrakstīts kā „kalibrēts” fails, kurā ir neapstrādāti skaitļi.
Izveidojiet savienojumu ar internetu un palaidiet atkārtoti. Iemesls ir tas, ko lasītājs faktiski
konstatējis attiecībā uz šo failu (nelasāma shēma, nav paketes, rakstīšanas kļūda), un izpildes
kopsavilkumā ir uzskaitīti **atšķirīgi** iemesli — divdesmit faili, kas izlaisti viena iemesla dēļ, tiek uzskatīti par vienu
iemeslu, nevis divdesmit šī iemesla atkārtojumus.

#### DAQ-A ierakstu eksportēšana kā neapstrādāti skaitļi

**DAQ-A** sērija ir radusies pirms sērijas bundļu sistēmas ieviešanas un tai nav kalibrēšanas bundļa,
ko lejupielādēt — tā vietā tā tiek kalibrēta uz vietas, izmantojot atstarošanas mērķi, kas ir
tāpēc tai nekad nav bijis nepieciešams kalibrēšanas komplekts. Atsakoties no šiem ierakstiem, lietotājiem vairs nebija iespējas
iegūt savus skaitļus vispār, tāpēc tie tiek eksportēti ar **citu nosaukumu**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Cits faila nosaukums, nevis atzīme faila iekšienē, jo informācijai ir jāiztur
nosūtīšana pa e-pastu kā vienkāršs nosaukums. `.csv` galvenē ir norādīts
`raw spectral sensor counts (NOT irradiance)` un brīdina, ka vērtības ir salīdzināmas
**failā** — tieši tam tās izmanto mērķorientētā kalibrēšana — un
nevis starp sensoriem. No jaudas atkarīgās fotometriskās kolonnas (kopējā jauda, fotopiskie un
skotopiskie luksi, PPFD) tiek ierakstītas kā **NULL**, nevis integrētas no skaitījumiem, un izpildes
kopsavilkumā norādīts `RAW COUNTS`, tādēļ žurnālā „eksportētos” datus nevar interpretēt kā starojuma intensitāti.

Vecākas versijas **v1.01 / v1.02** ieraksti (tos raksta DAQ-A-SD) nesatur atsevišķu laika posmu katram nolasījumam,
tikai failaierakstīšanas laiks. Attēla↔lejupvērstās starojuma plūsmas saskaņotājs tos joprojām noraida —
kadrā saskaņošana ar ierakstīšanas laiku būtu nepamanāmi kļūdaina —, bet eksportētājs tos nolasa, un
CSVā tiek izdrukāts `clock=daq_created_on`, tādējādi produkts norāda, uz kura pulksteņa tas atrodas.

### Piezīmes

- `process` automātiski nosaka, vai jūsu mape ir „Survey3”, „LATTICE” vai jaukta.
- Progresa plūsmas tiek pārraidītas, izmantojot „Server-Sent Events”; „CLI” rāda reāllaika progresu katram pavedienam (detektēšana, analīze, apstrāde, eksportēšana).
- Attiecībā uz „Linux” / „Jetson” CLI pārbauda apmaiņas atmiņu un var parādīt brīdinājumu pirms lielu mapju apstrādes. Tekstūru atpazīstošais debayer arī automātiski piemēro GPU frekvences ierobežojumu zema enerģijas patēriņa „Jetson” ierīcēm (Nano, Orin Nano).
- Ja apstrāde ir veiksmīga, izpildes ziņojumā tiek norādīts, cik daudz attēlu rezultātu tika saglabāti (`Image products written: N`).

#### Apstrāde, kuras rezultātā netiek saglabāti attēli, tiek uzskatīta par neveiksmīgu

Ja jūs pieprasījāt rezultātus, bet apstrādes rezultātā netika saglabāts **neviens** — tikai `project.json` un
`calibration_data.json` — `process` to uzskata par kļūdu: tas izvada
`Processing finished but wrote no image products.` un **iziet ar rezultātu, kas nav nulle**, tādējādi skripts to var
konstatēt. Ziņojumā ir norādīta projekta mape un parastie iemesli:

- ievades mape netika atpazīta kā uzņemšanas mape (pārbaudiet izkārtojumu un `--input-level`), vai
- visi pieprasītie rādītāji tika izlaisti kā nepiemēroti šīm kamerām (piemēram, pieprasot
  starojuma intensitāti/atstarošanas koeficientu no kamerām, kas atbalsta tikai RGB).

Palaidiet atkārtoti ar `--verbose` un pārbaudiet aizmugures žurnālu, meklējot rindas `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` rindas,
kas izskaidro katras kameras izlaišanas gadījumus, kuri citādi neparādās CLI izvades datos.

Apzināta darbība, izmantojot tikai metadatus — visi produkti ir atspējoti un nav `--indices` — joprojām ir
**veiksmīga**, jo tukša attēlu izeja šajā gadījumā ir pareizais rezultāts.

Tāpat arī **izpilde, kurā tiek izmantots tikai gaismas sensors**: `.daq` ierakstiem pēc definīcijas nav attēlu, ko eksportēt,
un darbība tiek vērtēta pēc kalibrētajiem `.daq` / `.csv`, ko tā uzrakstīja tā vietā.

---

## `chloros-cli login`

Autentificējiet šo ierīci ar Chloros+ mākoņpakalpojuma kontu. Pieslēgšanās dati tiek droši saglabāti `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Piemēri

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (noņemot daļu no paroles vai dublējot tās daļas). Ja tiek saņemts 401 kļūdas kods, CLI automātiski veic atkārtotu mēģinājumu, pievienojot `$$` atkārtoti pievienotu, pēc tam ar atkārtojumu noņemtu pusi paroles; ja atkārtotais mēģinājums izdodas, tas jūs piesakās un parāda pareizo vienkāršo pēdiņu sintaksi, kas jāizmanto nākamreiz.

> **Lietošana bez grafiskās saskarnes/ar skriptu: nav saglabātas sesijas, tātad tiek parādīts interaktīvs uzvednis, nevis ātra kļūda.** Jebkura komanda, kas palaiž aizmugurējo procesu (`process`, `status`, `export-status`, `time-sync`, …) un tiek izpildīta bez licences/sesijas kešatmiņas, pirms turpināšanas stdin parādās interaktīva `Email:` / `Password:` uzvedne. Tādējādi bez uzraudzības darbs bez sesijas kešatmiņas iesaldēsies, gaidot ievadi — pirms bezmonitora darbu plānošanas katrā datorā vienu reizi palaidiet `chloros-cli login EMAIL PASSWORD`.

---

## `chloros-cli logout`

Dzēš sesiju no kešatmiņas un piespiež veikt jaunu pieteikšanos nākamajā izsaukumā.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Parāda pašreizējo licences līmeni (Iron/Copper/Bronze/Silver/Gold), autentificēto lietotāju un ierīču piesaistīšanas skaitu.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Pārbauda Thread-4 eksportēšanas gaitu reāllaikā. To var droši izsaukt **`process`** darbības laikā no citas apvalka vides.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Iestata CLI displeja valodu (atbalstītas 38 valodas, tostarp CJK, RTL un Indijas valodas). Vecākās konsolēs, kas nespēj attēlot skriptu, tiek automātiski pārslēgts uz angļu valodu.

```
chloros-cli language [LANG_CODE] [--list]
```

### Piemēri

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Projekta mapes komandas

Tās pārvalda projekta mapes noklusējuma atrašanās vietu (kopīgu ar GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

---

## `chloros-cli update`

Linux/ Tikai Jetson. Pārbauda `version_url` no `/etc/chloros/update.conf` un piedāvā lejupielādēt un instalēt atbilstošo `.deb`.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linuxā / Jetsonā CLI arī veic **automātisku atjauninājumu pārbaudi katrā palaišanas reizē** (nenebloķējoša, nekad neaizkavē komandu): tā nolasa `/etc/chloros/update.conf`, rezultātu uzglabā kešatmiņā uz 1 stundu `~/.chloros/update_cache.json` un izdrukā `Update available: vX.Y.Z / Run: chloros-cli update`, ja ir pieejama jaunāka versija. Klusi izlaiž kļūdas gadījumā un, ja tiek saņemts Windows.

---

## `chloros-cli selftest`

Veic 7 posmu ātrās pārbaudes: versija, porta pieejamība, aizmugures sistēmas palaišana, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), trokšņu noņemšanas modeļa klātbūtne, CUDA + trokšņu noņemšanas gatavība.

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP galvenā servera statuss un vadība. „Chloros” serveris darbojas kā PTP galvenais serveris; „LATTICE” kameras un „DAQ-E” vienības darbojas kā tā pakļautās ierīces, lai nodrošinātu laika zīmogus starp ierīcēm.

| Apakškommanda | Apraksts |
| --- | --- |
| `status` | Parādīt galvenā servera stāvokli, BMCA prioritātes, pulksteņa identifikatoru. |
| `peers` | Uzskaitīt pakļautās ierīces, kas redzamas caur Delay_Req (kameras + DAQ-E sensori). |
| `cameras` | PTP darbspēja katrai kamerai (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Pārstartē galvenā procesa darbību. |
| `set-priority --priority1 N --priority2 N` | Pārrakstīt BMCA prioritātes. |

### Piemēri

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

LATTICE kameras vadība. Katra apakškommanda tiek maršrutēta caur „Chloros” aizmuguri; aizmugurei pieder kameru kopums, tādēļ turpmākie „CLI” izsaukumi atkārtoti izmanto to pašu atvērto rokturi.

### Bieži izmantotās opcijas (kopīgas lielākajai daļai apakškommandu)

| Opcija | Apraksts |
| --- | --- |
| `-d, --device N` | Kameras indekss (noklusējums: 0). |
| `-s, --serial SN` | Konkrēts sērijas numurs; pārraksta `--device`. |
| `--serials SN1,SN2,…` | Ar komatu atdalīti sērijas numuri darbībai ar vairākām kamerām. |
| `--all` | Darbojas ar katru atrasto kameru. |
| `--exposure US` | Ekspozīcijaslaiks mikrosekundēs. |
| `--gain DB` | Pastiprinājums dB. |
| `--pixel-format FMT` | piemēram, `BayerRG8`, `BayerRG12`. |
| `--width N` / `--height N` | Attēla izmēri. |
| `--preset {default,high_quality,high_speed,triggered}` | Piemērot iestatījumu priekšizvēli. Visi darbojas brīvā režīmā, izņemot `triggered`, kas sagatavo kameru aparatūras signālam 2. līnijā — ja šī līnija netiek aktivizēta, kamera gaidīs bezgalīgi, nevis veiks uzņemšanu. |
| `-o, --output DIR` | Izvades katalogs (noklusējums: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paketes izmērs. `auto` izpilda ICMP+GVSP pārbaudes; `jumbo` = 9000; `standard` = 1500. |

### LATTICE kameru pirmās savienošanas darba plūsma

```bash
# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Auto-shrink frame size to fit the wire
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Apakškommandu atsauces

#### Atklāšana un informācija

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice info` | Uzskaitīt pieslēgtās kameras (ražotājs, modelis, sērijas numurs, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analizē uzņēmēj sistēmu, lai noteiktu optimālo kameras konfigurāciju. `--no-discover` izlaiž kameru atklāšanu (ātrāka, tikai tīkla kartes analīze). |
| `lattice network [--fix] [--estimate] [--cameras N]` | Pārbauda/labo tīkla kartes iestatījumus; aprēķina joslas platumu/FPS. |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | Stabilas shēmas aizmugures tīkla iespējas + masīva ieteikums (atgriež `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `auto_capped_fps` saglabā pieprasīto izšķirtspēju, bet ierobežo mērķa kadrus sekundē — nolasiet `recommended.recommended_target_fps` un nododiet to kā savienojuma mērķi; uzskatiet to par veiksmīgu rezultātu, nevis kļūdu. |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | „What-if” analīze, neatsverot kameras. **`--n-active` ir kopējais kameru skaits tīklā, nevis tikai šajā masīvā**— palieliniet to, ja autonomās kameras straumē vienlaicīgi vai ja tīkla budžets tiek aprēķināts, pamatojoties uz prasībai, kas tās nepietiekami ņem vērā (noklusējums: `len(--models)`). Vienmēr izdrukā kopējo `Wire budget:` (pieprasītie MB/s pret sadursmju drošo maksimālo robežu) un `Max cameras:` rindas, kā arī atzīmē `** OVER-SUBSCRIBED**`, ja masīvs pārslodzē vadu — skatiet [Masīva fps un pārslogojuma modelis](#array-fps--burst-model). |
| `lattice gpu` | Parādīt GPU statusu. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Pārbaudīt vai atjaunināt kameras programmaparatūru. Vietējā `.fwa` izvēle ir fiksēta: fails `firmware/<MODEL_PREFIX>/`, kas atbilst izstrādes `MIN_FIRMWARE_VERSION`, tiek ierakstīts, ja tas ir pieejams (augstākā versija tiek izmantota tikai kā rezerves variants), tādējādi jaunāks ražotāja attēls, kas sagatavots uz diska, paliek neaktīvs, kamēr šis fiksējums netiek atcelts — apzināti jaunākas versijas nonāk ierīcēs caur parakstītu AWS manifestu, kas ir vēlams, ja versija ir jaunāka. |
| `lattice presets [--apply NAME]` | Kameras iestatījumu saraksts vai piemērošana. |
| `lattice status` | Kameras reāllaika statuss. |

#### Uztveršana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | Vienu kadru. **Pēc noklusējuma saglabā visus eksporta veidus** (`--processing all`); skatīt [Ierakstīšanas eksporta līmeņi](#capture-export-levels-the-all-default). `--levels` saglabā konkrētu apakškopu (pārraksta `--processing`); `--force-daq` ieraksta piešķirto DAQ rādījumu kā `.daq` papildu failu pat tad, ja tiek veikta tikai neapstrādātu datu uzņemšana. `--jpeg-quality` = JPEG kvalitāte 1–100 (noklusējums 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | Datu plūsma uz disku līdz Ctrl+C. |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Pārlūkprogrammā balstīts MJPEG priekšskatījums reāllaikā. `--ae-damping` iestata automātiskoekspozīcijas amortizāciju (0,4–100). |

#### Sensora regulēšana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | Jebkura GenICam mezgla lasīšana/rakstīšana. |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | Ekspozīcija un AE. |
| `lattice gain [--auto] [--off] [--set DB]` | Pastiprinājums un automātiskais pastiprinājums. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | Sensora ROI un pikseļu apvienošana. |
| `lattice format [--set FMT] [--list]` | Pikseļu formāts. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Aparatūras/programmatūras trigers. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (bez karodziņiem = vienreizēja balansa iestatīšana) | WB operācijas. Tikai RGB/Bayer kamerām; mono M3M gadījumā netiek veikta (izlaista). |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | „RGB” attēla krāsu apstrādes ķēde. `natural` (noklusējums) ir lēts reāllaika apstrādes risinājums; `enhanced` pievieno defringe + vibrance + CLAHE lokālo kontrastu, lai iegūtu pilnīgu „hub-parity” izskatu, kas izmaksā apmēram divas reizes vairāk par katru kadru, tādējādi samazinot **reāllaika** kadru ātrumu — saglabātie ieraksti vienmēr iegūst pilnīgu apstrādi jebkurā gadījumā. Tikai RGB /Bayer kamerām; tiek izlaists mono M3M. |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | Ekrāna piesātinājums/kontrasts (kameras ar RGBa filtru). Tiek izlaists mono M3M. |
| `lattice filter [--set NAME] [--list]` | Iestata kameras filtra modeli (`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | Zondē strāvas/termiskos mezglus; ieslēdz/izslēdz zema enerģijas patēriņa režīmu. |

#### Kalibrēšana un sensori

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibrēšana, izmantojot atstarojuma mērķi. |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` | Iebūvēto lejupvērstās gaismas sensoru komandas. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Piemērot vinjetes korekciju esošajiem attēliem. |

#### Daudzkameru režīms (pārejošas sesijas)

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice multi-info` | Uzskaitīt visas kameras ar sinhronizācijas lomām. |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | Viens sinhronizēts kadrs no katras kameras. **Pēc noklusējuma**saglabā visus eksporta veidus, ja ir pieslēgts pastāvīgs masīvs; pagaidu rezerves variants bez masīva tiek**tikai debayered** (pārējiem vispirms palaidiet `array-connect`). |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | Pārraida sinhronizētus kadrus (pagaidu). |
| `lattice multi-test [--count N]` | GPIO sinhronizācijas laika tests. |
| `lattice multi-detect [--line LINE] [--json]` | Automātiska GPIO galvenā/pakļautā savienojuma noteikšana. |

#### Izlīdzināšana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — papildus detektora/saskaņotāja regulatori `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC regulatori `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, vairāku kadru kombinācija `[--averaging mean\|median\|inlier_weighted]`, ģeometriskie ierobežojumi `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, telpiskie ierobežojumi `[--roi X0,Y0,X1,Y1] [--mask PATH]` un pārrakstīšanas iestatījumi katram pakalpiņam `[--per-cam-override SN:KEY=VALUE]` (atkārtojams) | Aprēķina saskaņošanas profilu no reāllaika kamerām. `--prefilter` noklusējuma iestatījums ir `gradient` (malu karte; atbilst GUI/masīva saskaņotājam — malas saglabājas visās spektrālajās joslās). `--matcher flann` atmaksājas, ja ir vairāk nekā ~5000 pazīmju; `--averaging median` ir izturīgs pret vienu neveiksmīgu uzņēmumu, `inlier_weighted` svērtā vērtība ir atkarīga no saskaņojumu skaita; `--lock-scale` projicē uz tuvāko rotāciju (bez mēroga), `--lock-axis` nulles vienu translācijas komponenti; `--mask` attiecas uz katru kameru (izmantojiet `--per-cam-override`, lai veiktu iestatījumus katrai kamerai atsevišķi, piemēram, `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` atsakās saglabāt kalibrāciju, kuras reprojekcijas RMS pārsniedz slieksni. |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | Uztver vienu saskaņotu daudzjoslu kadru. `--bit-depth` pēc noklusējuma pielāgojas kamerai; `--no-crop` saglabā pilnu kadru (aizpildot ar melnu); `--interpolation` (noklusējums `linear`) un `--border-mode`/`--border-value` (noklusējums `constant`/0) kontrolē CPU deformāciju — GPU ceļš jebkurā gadījumā ir bilineārs. |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | Plūsmai pielāgoti daudzjoslu kadri (tie paši deformācijas regulatori kā `align-apply`). |
| `lattice align-info --profile PATH [--json]` | Parādīt profila informāciju. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Mainīt slāņu secību. |

#### Indekss / Veģetācijas matemātika

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

Pilnais karogu kopums: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI / NDRE / EVI / SAVI / GNDVI /…), `--formula EXPR`, `--channel SYM=BAND` (atkārtojams), `--capture-level raw|debayered|radiance|reflectance|unknown` (pārraksta avotā TIFF reģistrēto ierakstīšanas līmeni; noklusējums: nolasīts no TIFF metadatiem), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Ar `--live` tiek piemēroti arī izlīdzināšanas deformācijas regulatori: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **`--channel` simboliem ir svarīgs lielais un mazais burts.** Simbolu pusei precīzi jāsakrīt ar iestatījuma kanālu nosaukumiem (iestatījumos tiek izmantoti mazie burti, piemēram, NDVI = `red`, `nir` — pārbaudiet `--list-presets`), un frekvenču joslas pusei jāsakrīt ar frekvenču joslas nosaukumu saskaņotajā sarakstā (vai arī jābūt joslas indeksam, sākot no 0, bezsaistes režīmā). `--channel red=Red_660 --channel nir=NIR_850` darbojas; `--channel RED=660` nedarbojas, parādot kļūdu `channel_map missing entries`.

#### Pastāvīgi savienojumi (Smart-Prep, GUI ekvivalents)

Šīs komandas saglabā kameras atvērtas aizmugures pūlā vairākos „CLI” izsaukumos.

| Apakškomanda | Mērķis |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Pievieno vienu kameru pūlam (viena kamera, bez masīva). |
| `lattice cam-disconnect [--serial SN] [--all]` | Atbrīvo. |
| `lattice cam-list` | Uzskaita kameraspūlā. |
| **`lattice array-connect`**|**Pievienot pastāvīgu sinhronizētu masīvu (ieteicamais sākuma punkts).** Izpilda pilnu GUI smart-prep plūsmu. |
| `lattice array-disconnect [--array-id ID] [--all]` | Atbrīvot masīvu. |
| `lattice array-list` | Uzskaitīt pieslēgtos masīvus. |
| `lattice array-status [--array-id ID]` | Reāllaika kadru skaits sekundē (fps), PTP, pēdējā kļūda. |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Viens sinhronizēts uzņēmums no reāllaika masīva — Vienreizējs / Nepārtraukts / Intervāls / Ātrākais. **Noklusējuma iestatījums ir `all`** (viens fails katram piemērojamajam eksporta tipam katrai kamerai). Izlaistās kameras (piem., RGB, kas izslēgtas no starojuma/atstarošanas) tiek norādītas ar `Skipped: SN:<serial> (<reason>)`; atstarošanai izmantotais DAQ rādījums tiek saglabāts kopā ar tām un norādīts ar `DAQ: <path>`. Skatīt [Uztveršanas režīmi, ierakstītāji un pārstrāde bezsaistē](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | Ieraksta kombinētā indeksa skatu reāllaikā video/GIF formātā (uzraudzības kvalitāte; nepieciešams atvērt kombinēto plūsmu). |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | Neapstrādāta Bayer sērija ar augstu kadru skaitu sekundē (analīzes kvalitāte; pārstrādājama bezsaistē). |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Saglabātas neapstrādātas sērijas pārstrādāšana kalibrētā(-os) video. |

##### `array-connect` opcijas

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automātiski atklāt visas LATTICE kameras (nepieciešamas ≥2) | Pirmā sērijas numura kamera ir MASTER. Ja nav norādīts, atklāšana tiek filtrēta pēc LATTICE (`TRI032*`) modeļiem un visas tiek savienotas. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinhronizācijas līnija. |
| `--target-fps F` | automātiski | Galvenās kameras izšaušanas ātrums. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Apliecināt līmeņu izvēlni. |
| `--wire-ceiling-mbps MB_PER_S` | automātiski noteikts | **Uzņēmējdatora ilgstošais vadu budžets, MB/s — skaitlis, no kura ir atkarīgs visa masīva resursu sadalījums.** Samaziniet to, ja masīvs ziņo par GVSP bojātiem rāmjiem: automātiskā vērtība tiek aprēķināta, pamatojoties uz tīkla kartes norādīto savienojuma ātrumu, kas pārspīlē USB adapteru, šauru PCIe joslu un noslogotu koplietošanas tīklu ātrumu. Tiek saglabāts projekta masīva uztveršanas blokā, tādēļ atkārtota atvēršana / CLI / SDK atjaunot savienojumu, tas tiek atjaunots. Skatīt [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | auto | Aparatūras binning. |
| `--no-recommend` | off | Izlaist tīkla analīzes posmu. |
| `--no-ptp` | izslēgts | Atspējot PTP (tad starpkameru laika zīmogi **nav** salīdzināmi). |

### Smart-AE / Smart-Capture

LATTICE masīvi, tiklīdz tie ir pieslēgti, fonā nepārtraukti veic automātisko ekspozīcijas regulēšanu (AE), taču nesen iestatītai ainai ir nepieciešams brīdis, lai konverģētu. `array-capture --smart` ir **iekļauta ērtības funkcija**: tas gaida, līdz AE stabilizējas visās masīva kamerās, un tikai tad sāk uzņemšanu. Izmantojiet to, ja sesijas laikā maināt ainas.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Stabilizēšanās politika pēc noklusējuma ir konservatīva: 5 s laika limits, 1,5 s stabilitātes logs, ±5 % ekspozīcijas izkliedes pielaide. Ja nepieciešama atšķirīga automatizācijas darbība, to var pielāgot, izmantojot „SDK” (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`).

### Uzņemšanas eksporta līmeņi (`all` noklusējums)

Sākot ar šo versiju, `lattice capture`, `lattice multi-capture` un `lattice array-capture` **noklusējuma iestatījums ir `--processing all`** — viens saglabāts fails katram eksporta tipam, kas attiecas uz katru kameru, atbilstoši lietotāja saskarnes iestatījumam &quot;Capture All” darbībai. Līmeņi ir šādi:

| Līmenis | Izvade | Attiecas uz |
| --- | --- | --- |
| `raw` | Vienkanāla Bayer (mono kameras: viena josla) tieši no sensora. | Visas kameras. |
| `debayered` | 3-kanālu BGR demosaics (mono kamerām: 1-kanāla pelēktoņu skala). | Visas kameras. |
| `radiance` | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi. | Tikai multispektrālās (M3C/M3M) — **izlaists kamerām ar „RGB” filtru**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), Pix4D-saderīgs. | Tikai multispektrālais režīms un **tikai tad, ja ir piesaistīts DAQ + kamera ir kalibrēta**; citādi tiek izlaists. |
| `preview` / `display` | Pilna GUI priekšskatīšanas ķēde (CCM + WB + gamma saskaņā ar kameras profilu). `lattice capture` nosauc to par `preview`; `array-capture`/`multi-capture` izmanto `display`. | Visas kameras. |

Norādiet vienu līmeni, lai saglabātu tikai šo vienu (`--processing debayered`). Ja pieprasāt `all`, līmeņi, kas neattiecas uz konkrēto kameru, tiek izlaisti (un tiek ziņoti), nevis tiek uzskatīti par kļūdu — nepieslēgta vai nekalibrēta kamera joprojām saņem `raw` / `debayered` / `preview`.

Jebkuram atstarošanas kadram DAQ faktiski izmantotais lejupvērstais rādījums tiek ierakstīts **`.daq`** papildu failā blakus attēlam (lai uzņemumu vēlāk varētu pārstrādāt) un norādīts `DAQ:` rindā.

### Kā izskatās uzņemto attēlu mape

Katrs eksporta veids nonāk savā **atsevišķā apakšmapē** zem `-o`, tādējādi daudzlīmeņu uzņemumos veidi nekad nesajaucas:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` ir uzņemšanas laika zīmogs, bet `<serial>` — kameras sērijas numurs, tādējādi viena sinhronizēta grupa izmanto vienotu
laika zīmogu visām kamerām. **Ņemiet vērā vienu asimetriju:** `display` līmenis tiek saglabāts mapē
ar nosaukumu `preview/`, savukārt pašos failos nosaukumā tiek saglabāts `_display` — mape un paplašinājums atšķiras
tikai šim līmenim. Nezināmi līmeņi tiek saglabāti mapē ar savu nosaukumu, un, ja apakšmapesmapes
nevar izveidot, fails tiek ierakstīts izvades saknes mapē, nevis pazūd.

**Uztverto failu mapes atkārtota apstrāde:**norādiet `chloros-cli process` uz**uztverto failu saknes**
(`output/`). `process` parasti importē tikai jūsu norādīto mapi, bet, ja tajā nav
attēlu, bet tajā ir apakšmapes, tas automātiski pārskata apakšmapes — tādējādi saknes līmeņa apakšmapes un pati
sakne `.daq` tiek ievāktas vienā reizē. Katrs uzņemumu līmenis tiek importēts kā viens attēls ar
pārējie līmeņi ir pieejami kā režīmi, nevis kā viens attēls katram līmenim.

**Līmeņa apakšmapes** tieša nosaukšana (piem., `output/raw/`) arī darbojas. Tādējādi saknes mape
`.daq` paliek neiekļauta, tāpēc, atkārtoti iegūstot radiometrisko
produktu no `raw/`, kopējiet vai norādiet DAQ nolasījumu blakus — pretējā gadījumā laika zīmoga saskaņošanai nebūs ar ko salīdzināt.

**Apstrāde vienmēr sākas no `raw`.** Katrā uzņemšanā neapstrādātais kadrs ir apstrādes avots;
`debayered`, `radiance`, `reflectance` un `preview` ir pieejami kā skatāmie režīmi, bet nekad netiek
atgriezti caur apstrādes cauruļvadu. Atvasinātā produkta atkārtota apstrāde nozīmētu atkārtoti piemērot vinjetēšanu, CCM un
starojuma aprēķinus, kas jau ir iebakēti tā pikseļos, tāpēc „Chloros” to noraida, nevis
veic dubultu apstrādi. Divas sekas, par kurām ir vērts zināt:

- `index/` un `composite/` renderējumi **nekad** netiek apstrādāti. Tie ir izvades rezultāti, nevis uzņemumi —
  NDVIa LUT renderējumam nav jēgpilnas starojuma interpretācijas.
- Uztverumu mape, kas eksportēta **bez** `raw` (piem., `array-capture --processing reflectance`),
  nav leģitīma apstrādes procesa avota. Šie ieraksti tiek importēti un attēloti normāli, bet `process` tos izlaiž
  un par to paziņo:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Ja jums patiešām ir nepieciešams caurlaist atvasinātu produktu — ar
  ieslēgtu `demosaic`, vai vecāka tipa mapi — `--input-level {raw,debayered,processed}` piespiež ieejas
  punktu un atceļ izlaišanu. Šis karodziņš ir apzināti paredzēta izeja; `auto` (noklusējuma iestatījums)
  nekad neapstrādā ierakstu, kuram nav neapstrādātu datu.

### Izlaistie ieraksti jaukta tipa filtru masīvos

Ja vienā masīvā tiek apvienotas „RGB” un multispektrālās kameras, `array-capture --processing radiance` (vai `reflectance`) saglabā multispektrālos kadrus un **izlaiž** „RGB” kameras — starojuma intensitāte uz vienu „Bayer” elementu nav nozīmīga platjoslas sensoram. „CLI” skaidri izdrukā katru saglabāto failu (ar tā eksporta līmeni), katru uzrakstīto „`.daq`” un katru izlaišanu, tāpēc failu skaits navnav pārsteidzošs:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Izlaišanas iemesla simboli atbilst šādam paraugam: `<level>-not-applicable-to-rgb-cam`. Atstarošanas rādītāji var tikt izlaisti arī ar `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, kā arī ar `dls-uncalibrated-band-<nm>`, ja josla galvenokārt atrodas ārpus DAQ gaismas sensora radiometriski kalibrētā diapazona (~374–974 nm) — no piegādātajiem SKU tikai F988, kura atbalstītā darbplūsma ir atstarojuma paneļa darbplūsma.

Izmantojiet `--processing debayered` (vai `display`), lai iekļautu katru kameru neatkarīgi no filtra tipa, vai noklusējuma `all`, lai vienā uzņēmumā iegūtu visus piemērojamos līmeņus katrai kamerai.

---

## Uztveršanas režīmi, ierakstītāji un apstrāde bezsaistē

Visi šie darbojas **pastāvīgā masīvā** (vispirms palaidiet `array-connect`). Tie atspoguļo GUI uzņemšanas paneli.

### `array-capture` režīmi

`array-capture` ir viena komanda ar četriem aizslēga režīmiem un virkni eksporta slēdžu:

| Režīms | Karodziņš | Darbība |
| --- | --- | --- |
| **Vienreizējs** *(noklusējums)* | (nav) | Viena sinhronizēta uzņemšanas grupa, pēc tam iziet. |
| **Nepārtraukts** | `--continuous` | Secīgas caurlaides līdz brīdim, kad tiek izpildīta `Ctrl+C`, `--count N` vai `--duration S`. |
| **Intervāls** | `--interval S` | Viens cikls ik pēc `S` sekundēm (skaitot no katra cikla sākuma), tās pašas robežas. |
| **Ātrākais** | `--fastest` | Tikai neapstrādāti dati + piešķirtais DAQ rādījums + kombinētā indeksa kompozīts; izlaiž starojuma/atstarošanas/attēlošanas aprēķinus, lai kadrs tiktu parādīts ātrāk. Ietver `--processing raw --force-daq`. Vēlāk pārstrādājiet saglabāto `.daq` kalibrētos produktos. |

Eksporta slēdži (apvienojiet ar jebkuru režīmu; visiem ir kopīgs GUI/SDKgalapunkts):

| Karodziņš | Efekts |
| --- | --- |
| `--processing LEVEL` | Vienots eksporta līmenis, vai `all` (noklusējums). |
| `--levels L1,L2,…` | Eksporta veidu skaidri norādīta apakškopa (piem., `raw,radiance,reflectance`); **pārraksta `--processing`**. |
| `--aligned` / `--no-aligned` | Katra elementa neapstrādāto eksportu deformē atbilstoši masīva [izlīdzināšanas profilam](#alignment) (kopīgi reģistrēts). Neapstrādātie dati paliek neizlīdzināti, bet transformācija tiek ietverta metadatos. Ja masīvam nav profila, tiek izmantota neizlīdzināta versija (ar brīdinājumu). |
| `--index` / `--no-index` | Saglabāt / izlaist katras kameras veģetācijasindeksa pārklājumu, ja tāds ir konfigurēts. Noklusējums: renderēt to. |
| `--force-daq` | Saglabāt piešķirto DAQ/DLS rādījumu kā `.daq` papildu failu, pat ja nevienam izvēlētajam līmenim tas nav nepieciešams (piem., ja tiek veikts tikai neapstrādātu datu uzņemums), lai kadrus varētu pārstrādāt atstarojuma/indeksa formātā bezsaistē. |
| `--smart` | Pirms aktivizēšanas gaidiet, līdz AE stabilizējas visās kamerās (skatīt [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | „TIFF” pikseļu kompresija. `deflate` (noklusējums) = bezzaudējumu zlib L1 + horizontālais prognozētājs, ~4,1 MB uz vienu pilnas izšķirtspējas kadru; `none` = nesaspiests, ~5 reizes ātrāka rakstīšana ar ~6,3 MB uz katru kadru — izmantojiet maksimālam ilgstošajam ātrumam, ja diska kapacitāte to ļauj. Abas metodes ir bezzuduma un tiek nolasītas identiski importēšanas laikā. |

> **Vienreizējās ierakstīšanas TIFF + ilgstošā ātruma modelis.**Ieraksti tiek ierakstīti**vienā**TIFF faila caurlaidē, kas ietver pikseļus + XMP + IFD0 ražotāju/modeli (izmērīts pilnas izšķirtspējas Mono12: 36 ms saspiestā formātā / 6,5 ms nesaspiestā formātā, salīdzinājumā ar ~148 ms vecajam „rakstīšanas, tad pārrakstīšanas ar ExifTool” procesam); vienīgais atlikušais ExifTool darbs (EXIF apakš-IFD pilnveidošana) notiek asinhronā fona procesā, un kadrs ir pabeigts un gatavs importēšanai, pat ja šis process nekad netiek izpildīts. Ņemiet vērā, ka DEFLATE saspiešana aizņem Python GIL, tādēļ saspiestie ieraksti**netiek**paralizēti pa atsevišķajiemkameru rakstīšanas pavedienos — ilgstoša 8 kameru pilnas izšķirtspējas uzņemšana ar sensora ātrumu (~10,4 fps) prasa `--compression none`**un** NVMe klases disku (~500 MB/s ilgstošas rakstīšanas ātrums). Tas pats regulētājs ir pieejams kā `compression` uz `POST /api/camera/array/capture`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — kombinētā indeksa video/GIF (uzraudzības kvalitāte)

Ieraksta visu, ko **reāllaika kombinētā indeksa skats** parāda uz `.avi` (un pēc izvēles uz `.gif`). Tā kā tas izmanto reāllaika kompozīciju, kombinētajai plūsmai jābūt atvērtai (piemēram, masīvs tiek priekšskatīts GUI), lai kadri tiktu ierakstīti. Tas ik pēc 2 s pārbauda progresu un apstājas uz `--duration`, `Ctrl+C` vai tad, kad ierakstītājs pats pārtrauc darbību.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--array-id ID` | tikai masīvs | Mērķa masīvs (izlaist, ja ir pieslēgts tikai viens). |
| `-o, --output DIR` | `output` | Izvades katalogs (backend-local). |
| `--fps F` | `10` | Ierakstīšanas kadru ātrums. |
| `--duration S` | līdz Ctrl+C | Automātiska apstāšanās pēc `S` sekundēm. |
| `--gif` | izslēgts | Ierakstīt arī animētu GIF failu. |
| `--gif-only` | izslēgts | Ierakstīt tikai GIF failu (bez `.avi`). |

### `array-burst` — neapstrādāts Bayer augstas kadru frekvences sērijas uzņēmums (analīzes kvalitāte)

Lasīts tieši no uzņemšanas cikla sinhronizētās grupas bufera — **nav nepieciešama ne kalibrēšanas ķēde, ne exiftool, ne reāllaika skats** — tādējādi tas darbojas ar kameras pilnu uzņemšanas ātrumu. Raksta neapstrādātus kadrus + manifestu katram kadram + vienu `.daq` par katru atsevišķu DLS nolasījumu saskaņā ar `<output>/bursts/<base>/`. Pārstrādājiet bezsaistē (nākamā komanda) vai nododiet `--build`, lai to izdarītu nekavējoties pēc apstāšanās.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--array-id ID` | tikai masīvs | Mērķa masīvs. |
| `-o, --output DIR` | `output` | Izvades katalogs (dati tiek saglabāti `<DIR>/bursts/<base>/`). |
| `--duration S` | līdz Ctrl+C | Automātiska apstāšanās pēc `S` sekundēm. |
| `--max-frames N` | neierobežots | Automātiska apstāšanās pēc `N` neapstrādātu kadru. |
| `--build` | izslēgts | Pēc apstāšanās nekavējoties atkārtoti apstrādāt sēriju (tāpat kā `array-build-video`). |
| `--products …` | `combined:index` | Ar `--build`: kurus video jāveido (skatīt zemāk). |
| `--fps F` | `10` | Kopā ar `--build`: izejas video kadru skaits sekundē. |
| `--save-tiffs` | izslēgts | Ar `--build`: saglabā arī katram kadram kalibrētus TIFF failus. |
| `--gif` | izslēgts | Ar `--build`: raksta arī animētus GIF(us). |

### `array-build-video` — saglabātās sērijas pārstrāde bezsaistē

Katru neapstrādāto kadru laika ziņā saskaņo ar tuvāko saglabāto `.daq` rādījumu un virza to caur **pa to pašu starojuma / atstarošanas / indeksa ķēdi kā importēšanas procesā**, renderējot vienu vai vairākus video.

`--products` ir ar komatu atdalīts `kind:level` elementu saraksts, kur `kind` ∈ `per_cam` | `combined` un `level` ∈ `radiance` | `reflectance` | `index`. Ja nav norādīts `level` (nav `kind:`), par noklusējuma vērtību tiek izmantots `per_cam`. Noklusējuma vērtība ir `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--burst-dir DIR` | (obligāts) | Ceļš uz sērijas mapi (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` saraksts, kā iepriekš. |
| `--fps F` | `10` | Izvades video kadru skaits sekundē (fps). |
| `--save-tiffs` | izslēgts | Kopā ar video saglabāt arīkatru kadru kalibrētus TIFF failus kopā ar video. |
| `--gif` | izslēgts | Ierakstīt arī animētus GIF failus. |

> **Izvēlieties pareizo ierakstītāju.** `array-record` ir *monitoringa*— tas ieraksta reāllaika kompozīciju tā, kā tā tiek parādīta, un tam ir nepieciešams atvērts datu plūsmas avots. `array-burst` → `array-build-video` ir *analīzes līmeņa* — tas saglabā neapstrādātus sensora datus ar pilnu ātrumu un pēc tam rekonstruē kalibrētus starojuma/atstarošanas/indeksa video, kam nav nepieciešams reāllaika skats.

### Mono (M3M) vienjoslas kameras

**M3M**sērija ir Bayer**M3C**mono versija: viena šaurjoslas interferences filtra uz katru kameru (`M3M-<lens>-F<wavelength>`, piemēram, `M3M-L87-F685`), tādējādi sensors nodrošina**vienu pelēktoņu joslu** bez Bayer mozaīkas. Nav nekas, ko demosaicēt, nav kanālu savstarpējās pārklāšanās, ko atdalīt, un nav jāiestata balansa — visa krāsu apstrādes ķēde „RGB” vienkārši nav piemērojama.

Ko tas nozīmē attiecībā uz „CLI”:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**atpazīst mono kameru un**izlaiž ar vienrindas ziņojumu**, nevis piemēro bezjēdzīgus iestatījumus. Tās joprojām darbojas normāli ar „RGB” / „Bayer M3C” kameru tajā pašā sesijā.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** joprojām darbojas — starojuma intensitāte un atstarošanās ir *katrai joslai* radiometriskas kartes un ir pilnīgi precīzi definētas vienai joslai. Mono kadriem ir **identitātes** sensora reakcijas matrica (bez 3×3 atdalīšanas), tādēļ kalibrēšanas aprēķini tos neietekmē.
- **Viena mono kamera nevar ģenerēt veģetācijas indeksu.**NDVI / NDRE / utt. ir nepieciešami vismaz divi kanāli (piem., Red + NIR). Lai iegūtu indeksu no mono aparatūras, vēršiet**vairākas** M3M kameras uz dažādiem viļņu garumiem, salieciet tās vienā daudzkanālu kopā un aprēķiniet *to*:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` simboliem **precīzi** jāsakrīt ar iestatījuma kanālu nosaukumiem (jāievēro lielie un mazie burti; NDVI ir mazie burti `red`,`nir` — skatīt `--list-presets`), un frekvenču joslas nosaukums norāda uz frekvenču joslu saskaņotajā kopā (bezsaistes režīmā tiek pieņemti arī frekvenču joslu indeksi, kas sākas ar 0, piemēram, `--channel red=0 --channel nir=1`).

Diskriminators visā skavā ir simbols `M3M` modeļa virknē (tas nekad neparādās `M3C` virknē), kas tiek parādīts GUI/SDKā kā `is_mono`.

---

## Host NIC iestatīšana un optimizēšana (LATTICE masīvi)

LATTICE kameras pārraida GVSP caur uzņēmējdatora Ethernet adapteri, tāpēc daudzkameru masīvos adaptera **draiveris**un**uzņemšanas gredzena izmērs** ir tikpat svarīgi kā savienojuma ātrums. Nepareizi iestatījumi parādās kā `FRAMES WILL DROP` / `Reduce ROI to enable` kļūda paneļā „Array Settings” (un kā `lattice network-analysis` / SDK `analyze_array_network()`), pat ja pašas kameras darbojas pareizi.

### USB 10GbE adapteri — Realtek RTL8157 („Realtek USB 10GbE Family Controller”)

| Pozīcija | Nepieciešamā vērtība | Kāpēc tas ir svarīgi |
| --- | --- | --- |
| **Draivera versija**|**≥ v10.67 (2026. gada janvāris)**, INF `rtump64x64sta.inf` | Novecojušais**

2016. gada**draiveris (v10.65, `rtump64x64.inf`) nepareizi apstrādā izslēgšanos un kļūdu pārbaudes ar**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**sistēmas izslēgšanas, pārstartēšanas vai miega režīma laikā. Pāreja iestrēgst (~5 minūšu laika limits), lietotājs veic piespiedu izslēgšanu, un atkārtotas nekorektas izslēgšanas**bojā WMI krātuvi**(PowerShell/rīki sāk nedarboties ar kļūdu `Invalid class`) un**bloķē USB sistēmu** nākamajā palaišanas reizē (tīkla karte neieslēdzas; USB diski vairs netiek atpazīti). Veiciet atjauninājumu no realtek.com (vai no dongla ražotāja), pirms paļauties uz tīru pārstartēšanu. |
| **Uztveršanas buferi**— atslēgvārds `ReceiveBufferLen` |**256**(draivera maksimums) | NIC RX gredzens. Draivera noklusējuma vērtība**32**atstāj tikai ~0,26 MB izmantojama gredzena — tas ir pārāk mazs, lai apstrādātu vairāku kameru uzņemto attēlu sēriju — tādēļ masīva panelis ziņo par kļūdu `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` un bloķē savienojumus. Pie**256**gredzens ir liels (**~13,5 MB, izmērīts laboratorijas 10GbE serverī**), nodrošinot RX cauruļvadam reālu rezerves kapacitāti daudzkameru GVSP sērijveida datu pārraidei. (To, vai konkrētā konfigurācija faktiski *izveido savienojumu*, nosaka divas pārbaudes — **drain-aware**piekļuves pārbaude un**kopējās pārsubskripcijas** pārbaude — nevis vienkārša sērijas un gredzena salīdzināšana; skatīt [Masīva fps un sērijas modelis](#array-fps--burst-model).) |
| **Saņemamie URB**— atslēgvārds `PendingReceives` |**64** (maks.) | USB pieprasījumu bloki pārraides laikā; palieliniet kopā ar saņemšanas buferiem, lai absorbētu pārsūtījumu plūsmas. |
| **Jumbo rāmis** — atslēgvārds `*JumboPacket` | **9014** | Nepieciešams 9000 baitu GVSP pakešiem (6 reizes mazāk pakešu/rāmī nekā 1500). |

> ⚠️ **NIC draivera atjaunināšana ATJAUNO šos papildu parametrus uz noklusējuma vērtībām.**Pēc adaptera draivera atjaunināšanas vai nomaiņas**no jauna piemērojiet** `ReceiveBufferLen=256` un `PendingReceives=64`, citādi masīva panelis atkal bloķēsies, pat ja „aparatūrā nekas nav mainījies”. Tas ir galvenais iemesls, kāpēc iepriekš darbojusies iekārta pēkšņi vairs nevēlas izveidot savienojumu.

Piemērojiet no **administratora tiesībām** ar PowerShell (aizstājiet ar savu adaptera nosaukumu, piemēram,piem., `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` attiecas uz USB 10GbE adapteriem.** Tagad tas atpazīst adaptera tipu un iestata pareizo uztveršanas-ring atslēgvārdu: `*ReceiveBuffers`→2048 PCIe tīkla kartēm (Intel I219 utt.), vai `ReceiveBufferLen`→256 + `PendingReceives`→64 Realtek **USB** 10GbE kontrolierim (kurš neizpauž `*ReceiveBuffers`). Mērķvērtības tiek ierobežotas līdz katra draivera ziņotajai maksimālajai vērtībai (`NumericParameterMaxValue`), tādējādi nekad netiek ierakstīta vērtība, kas ir ārpus diapazona. Izpildiet to no termināla ar **paplašinātām** tiesībām; tāpat kā jebkura reģistrapārveidojums, izmaiņas stājas spēkā pēc adaptera pārstartēšanas vai datora pārstartēšanas. Iepriekš minētās manuālās `Set-NetAdapterAdvancedProperty` komandas joprojām ir laba alternatīva — tās tiek piemērotas nekavējoties (atjaunojot adaptera saistīšanu) bez pārstartēšanas.

### Tīkla pamati (visi LATTICE savienojumi)

- **Adresēšana:** savienojuma lokālā `169.254.0.0/16` (GigE Vision LLA). Dators izmanto statisku `169.254.x.x/16`; kameras + DAQ-E pašas piešķir sev adreses tajā pašā diapazonā. Nav nepieciešams DHCP/vārteja.
- **Paketes izmērs:**ieteicams izmantot jumbo (9000), bet ļaujiet automātiskajai pārbaudei to noteikt — tā veic atkārtotu mērīšanu katrā savienojumā un, izmantojot GVSP pārbaudi, jau ņem vērā kameras 1500 baitu ICMP ierobežojumu, tādējādi izvēloties jumbo izmēru visur, kur vads to patiešām spēj pārraidīt. Izmantojiet `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` tikai tad, ja zini labāk nekā pārbaudītājs, un dod priekšroku iestatījumam katrai komandai, nevis pastāvīgam: iestatījums apiet pārbaudi, tādēļ, ja ceļš faktiski nevar pārraidīt 9000,**katra** uztveršana beidzas ar laika limitu, izmantojot `SC_ERR_TIMEOUT -1011` (sk. [Vides mainīgie](#environment-variables)).
- **RX gredzens mainās atkarībā no `ReceiveBufferLen`:**pie noklusējuma `32` izmantojamais gredzens ir ~0,26 MB (pārāk mazs jebkurai daudzkameru pārsūtīšanai); pie maksimālā `256` tas ir liels (~13,5 MB, izmērīts laboratorijas 10GbE serverī), nodrošinot reālu rezerves kapacitāti. To, vai konfigurācija izveido savienojumu, nosaka gan pārslodzes apzināta piekļuves pārbaude,**gan** zemāk aprakstītā kopējā pārslogotības pārbaude — nevis vienkārša salīdzināšana starp sērijas pārraides ātrumu un gredzena kapacitāti.

### Masīva kadru skaits sekundē (fps) un sērijas uzņemšanas modelis

Kā izprast paneļa „Array Settings” (un `lattice analyze-array` / „SDK” `analyze_array_network`) saturu:

- **Sērijas attēli tiek summēti katrai kamerai atsevišķi, izmantojot katras kameras reālo pikseļu formātu.**Mono**M3M**kameras pārraida**Mono12 (2 B/px)**;**M3C**Bayer kameras pārraida 8 vai 12 bitu datus (TRI032S klusi pārraida BayerRG12 pat tad, ja tiek pieprasīts BayerRG8). Tātad 4 kameru pilnas izšķirtspējas kadrs ir**~12,6 MB, ja visas ir 8bitu, bet ~25 MB, ja tiek izmantotas trīs 12-bitu mono kameras**. Projekcija nosaka katras kameras formātu pēc tās modeļa (identitātes kešatmiņa), tādējādi datu plūsma atbilst tam, ko faktiski pārraida vads — nevis vienam visiem piemērojamam BayerRG8 pieņēmumam.
- **USB Ethernet adaptera ātrums ir ierobežots līdz 200 MB/s neatkarīgi no tā specifikācijās norādītā.** Efektivitātes tabula, kas pārvērš savienojuma ātrumu ilgstošā rādītājā, ir atvasināta no PCIe; USB tīkla karte norāda savu *Ethernet* savienojuma ātrumu, taču to ierobežo USB šīna un tās draiveris. USB 10GbE adapteris agrāk uzrādīja ~1063 MB/s „pastāvīgo” ātrumu — skaitli, kas nekad netika pārbaudīts —, un rezultātā radušais ritma traucējums sabojāja 6–18 % kadru, vienlaikus joprojām ziņojot par pareizu mērķa kadrēšanas ātrumu (fps). USB pieslēgtajiem tīkla adapteriem tagad ir noteikts ierobežojums **200 MB/s** (ierobežojums ir šķautne, tāpēc tas nemainās atkarībā no nominālās jaudas; USB 1 GbE adapteris sasniedz ~80 MB/s, un tas netiek ietekmēts). `wire_ceiling_source` spēju ierakstā to skaidri norāda, un `nic_is_usb` to atzīmē. Pārrakstiet jebkurā gadījumā ar `--wire-ceiling-mbps`.
- **Pieeja ņem vērā datu plūsmas izsīkšanu, nevis salīdzina pilnu pārsūtījuma sēriju ar gredzenu.** Vienlaicīgajai pārsūtījuma sērijai ir jāiekļaujas tikai *pārejošajam uzkrājumam* = `max(0, Σ per-cam arrival − host drain) × emit_window`, nevis visam pārsūtīšanas blokam. Ātrā saimniekdatorā / lēnā kamerā (a **PCIe**10G saimniekdators + 4× 1 GbE kameras: ienākošais datu plūsmas ātrums ≈ 320 MB/s, iztukšošanas ātrums ≈ 1063 MB/s) hosts iztukšo ātrāk, nekā kameras piepilda, uzkrājums ≈ 0, tādēļ pilnas izšķirtspējas sim-emit**ļauj**datu plūsmu, lai gan 25 MB pārsūtīšanas sērija pārsniedz 13,5 MB gredzenu. Ja tās pašas četras kameras pieslēdz pie**USB**10GbE adaptera, datu nosūtīšanas ātrums ir 200 MB/s, nevis 1063 — datu ienākšana pārsniedz izvadīšanas ātrumu, un zudums izpaužas kā bojāti kadri, nevis kā zemāks kadru ātrums. Uz 1 GbE saimniekdatorā kameru 31,25 MB/s DLThr minimums liek datu ienākšanai pārsniegt izvadīšanas ātrumu → tas pareizi**bloķē** (attiecībā uz *šo* bloķēšanas klasi samaziniet ROI vai izmantojiet binningu ≥ 2). Ieeja ir viena no **divām** savienojuma vārtiņām — otra ir zemāk minētā kopējā pārslogojuma pārbaude.
- **Prognozētais kadru skaits sekundē (fps) ir konservatīvs sērijiskās izguves maksimums.**Datoru satveršanas cilpa pašlaik izvelk katras kameras buferi**sērijveidā**(aptuveni viens izsūtīšanas logs katrai kamerai), tādējādi cikls ir ierobežots ar `max(readout+emit, N × emit)`, kur katras kameras izlaide ir ierobežota līdz kameras**piekļuves savienojumam**(1 GbE ≈ 80 MB/s), nevis uzņēmējdatora augšupsaitei. 4 kameru pilnas izšķirtspējas 12 bitu masīvam tas ir**~2,8 kadrus sekundē**, kas atbilst izmērītajiem ~2,7–3,0 kadriem sekundē. Šis rādītājs ir apzināti**neatkarīgs no ekspozīcijas**, tāpēc vājā apgaismojumā faktiskais rādītājs var nedaudz noslīdēt zem maksimālā līmeņa, palielinoties ekspozīcijas ilgumam. Sērijveida datu ieguve ir patiesais kadru skaita ierobežotājs; tās paralēlizēšana paaugstinātu maksimālo līmeni, tuvinot to vienlaicīgās datu pārraides ātrumam.
- **Kopējā pārslogotība ir būtisks savienojuma bloķētājs.**Pieslēguma joslas platuma piešķiršana katrai kamerai sasniedz minimālo robežu**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`), tādēļ, tiklīdz minimālā robeža tiek sasniegta, kopējais pieprasījums (`per_cam × N`) var pārsniegt**sadursmju drošo vadu maksimālo robežu**(`sustained × sim_emit_factor`). Praktiskie maksimālie ierobežojumi pilnā izšķirtspējā 1 GbE tīklā:**6 kameras ar 1500 MTU, 9 ar jumbo**. Šis ierobežojums ir atkarīgs tikai no vadu un minimālā sliekšņa — tas ir**neatkarīgs no rāmja izmēra**, tādēļ**datu grupēšana un mazāks ROI NEPALĪDZ** (tie samazina baitu skaitu vienā *rāmī*, nevis GevSCPD ritmā pārraidīto baitu skaitu *sekundē*); vienīgie risinājumi ir mazāks kameru skaits, jumbo rāmji no viena gala līdz otram vai ātrāks tīkla interfeiss. Simptoms būtu GVSP pakešu zudums, nevis pakāpenisks kadru skaita (fps) samazinājums, tāpēc `analyze-array` iestata sasniedzamo kadru skaitu uz nulli un parāda `**OVER-SUBSCRIBED**`, savukārt `array-connect` ar fiksētu izšķirtspēju **atsakās izveidot savienojumu** (pretējā gadījumā „walk-down” apvieno kadrus, kas arī neizslēdz šīs klases bloķēšanu). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` pazemina atteikumu līdz skaļam brīdinājumam testēšanas darbam — skatiet [Vides mainīgie](#environment-variables).

### Masīva stāvoklis — kura apakšsistēma zaudē kadrus

Pievienotā masīva `GET /api/camera/array/<array_id>/capability` rāda reāllaika
`health` bloku, kas tiek pārvērtēts **10 sekunžu** ilgā logā. Tas sadala kadru zudumu
divos cēloņos, kuriem nepieciešami pretēji risinājumi, nevis ziņo par vienu „nepilnīgu”
rādītāju, kas nenorāda ne vienu, ne otru:

| Lauks | Kas tas nozīmē | Kura apakšsistēma |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pēc sērijas numura) | Kadrs **ieradās, bet bija strukturāli bojāts**— GVSP pakešu zudums. |**Tīkls**: vadu jauda, sinhronizācija, NIC RX gredzens, MTU |
| `never_arrived_rate_pct` (pēc sērijas numura) | Rāmis **vispār neieradās**— kamera neizšāva vai no tās nekas neizgāja. |**Trigers / sinhronizācija**: M8 kabelis, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Sliktākais kameras rādītājs katrai. | — |
| `per_cam_rate_pct` | Kopējais nepilnīguma rādītājs katrai kamerai (abi iemesli kopā). | — |
| `stable_for_seconds` | Cik ilgi katra kamera ir palikusi zem 0,01 %. | — |

Ja rādītājs pārsniedz 5 %, backend reģistrē `[array-health <id>] WARN` rindu, norādot sadalījumu — pie
pirmā pārkāpuma, pie nopietnības līmeņa izmaiņām, reizi minūtē, kamēr tas pastāv, un vienu reizi, kad
tas izlīdzinās. Bojātā puse izdrukā `[gvsp-corrupt <SN>]` par pirmo reģistrēto gadījumu katrai kamerai un
iemeslu, pēc tam kopsavilkumu ik pēc 60 s. Katrs novērtējums joprojām nonāk aizmugures žurnāla failā;
skaitītāji mainās katrā buferī neatkarīgi no tā, kas tiek izvadīts.

Tajā pašā ierakstā tiek ziņots par kopējo piešķīrumu:

| Lauks | Kas tas nozīmē |
| --- | --- |
| `wire_ceiling_mbps` | Spēkā esošais uzņēmējdatora ilgstošais datu pārraides budžets, MB/s. |
| `wire_ceiling_source` | No kurienes šis skaitlis ir iegūts, vārdos — piemēram, `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` vai `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, ja to iestatīja `--wire-ceiling-mbps` (vai GUI lauks **Vadu budžets**). |
| `nic_is_usb` | `true` USB Ethernet adapterim — skatiet iepriekš minēto 200 MB/s ierobežojumu. |

**Lasīšana:** `gvsp_corrupt_rate_pct`, kas nav nulle, ar `never_arrived_rate_pct` vērtību 0
nozīmē, ka trigera signāls un kabeļa sinhronizācija ir ideāla un 100 % zudumu rada tīkla
ceļš — samaziniet `--wire-ceiling-mbps` un atkārtoti izveidojiet savienojumu. Pretējais modelis norāda uz
sinhronizācijas kabeli vai trigera līniju.

> **`--target-fps` nav rādītājs bojātiem rāmjiem.** GevSCPD ritms tiek iestatīts
> vienreiz savienojuma izveides brīdī, tādēļ trigera ātruma samazināšana maina darba ciklu, nevis
> vienlaicīgās pārraides pārsūtīšanas ātrumu. Izmērītais 5× pieprasījuma samazinājums nedeva uzlabojumus;
> samazinot vadu maksimālo ātrumu no 240 līdz 200 MB/s samazināja šīs pašas iekārtas bojājumu līmeni no 10,4 %
> līdz 0,00 %.

> **TRI032S programmaparatūras versijā nav pieejama automātiskā samazināšana datu plūsmas vidū.** Darbojošs masīvs
> nevar to novērst pats; atvienojiet un atkārtoti pievienojiet, lai savienošanās brīža izvēlne varētu
> veikt atkārtotu plānošanu ar jauno maksimālo ātrumu.

### Simptoms → risinājums

| Simptoms (Masīva iestatījumi / savienošana / `analyze_array_network`) | Cēlonis | Risinājums |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` atiestatīts uz 32 (parasti pēc draivera atjaunināšanas) | Iestatiet `ReceiveBufferLen`→256, `PendingReceives`→64; atveriet paneli no jauna (pārstartējiet backend, ja tas ir saglabājis veco gredzena izmēru kešatmiņā) |
| Pārstartēšana/izslēgšana iestrēdz; vēlāk `Invalid class` WMI kļūdas, tīkla karte neieslēdzas, trūkst USB disku | Vecais 2016. gada Realtek USB 10GbE draiveris → BSOD `0x9F` → piespiedu izslēgšana| Atjauniniet adaptera draiveri līdz vismaz v10.67 (2026), pēc tam atkārtoti piemērojiet iepriekš minētos uztveršanas gredzena iestatījumus |
| Savienošanās izdodas, bet tiek atgriezta zemāka izšķirtspēja | Smart-prep automātiski samazināja rāmi, lai tas iederētos vadā | Uzlabojiet savienojumu / pieņemiet samazinājumu / `--force-tier slip-emit-and-capture` |
| Masīvs ziņo par pareizu mērķa kadrusekundē, bet nodrošina tikai daļu no tā; `health.gvsp_corrupt_rate_pct` nav nulle, `never_arrived_rate_pct` 0 | Uztverējamā vadu kapacitāte pārsniedz to, ko sistēma faktiski spēj uzturēt (tipiski USB Ethernet adapterim, šaurā PCIe joslā vai koplietotā tīklā) | Atkārtoti izveidojiet savienojumu ar zemāku `--wire-ceiling-mbps` vērtību un atkārtoti pārbaudiet darbības stāvokļa bloku. **Nav** `--target-fps` — GevSCPD ritms tiek fiksēts savienojuma izveides brīdī |
| Kameras nav iekļautas publicētajās grupās; `health.never_arrived_rate_pct` navnav nulle, `gvsp_corrupt_rate_pct` 0 | Izraisītāja / sinhronizācijas ceļš — kameras nedarbojas, nav tīkla problēma | Pārbaudiet M8 sinhronizācijas kabeli un `--line`; pārliecinieties, ka visi dalībnieki ir ieslēgti (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` pārsniegts `analyze-array`, vai savienojuma atteikums ar fiksētu izšķirtspēju (`array over-subscribes the wire`) | Kopējais pieprasījums uz vienu kameru (8 MB/s minimums × N kameras) pārsniedz sadursmju drošo vadu maksimālo jaudu — 6 kameras ar pilnu izšķirtspēju 1 GbE tīklā ar 1500 MTU, 9 kameras ar jumbo rāmjiem | Mazāks kameru skaits, jumbo rāmji visā maršrutā vai ātrāks tīkla interfeiss. **ROI/binning NEPALĪDZĒS** (robeža nav atkarīga no rāmja izmēra). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` pārraksta testēšanas vidē (pieņem paketes zudumu) |

---

## `chloros-cli daq`

Spektrālo sensoru komandas. Divas klases:
- **`pool-*`**— vieglie „HTTP” klienti, kas vada sensoru caur backend pastāvīgo pūlu.**Šis ir atbalstītais ceļš un vienīgais, kas ir pieejams piegādātajā „CLI”.** Backend pārvalda transportu, tādēļ GUI, „CLI” un „SDK” skripti visi izmanto vienu aktīvu rokturi, nevis cīnās par seriālo portu.
- **Viss pārējais**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — tieša piekļuve aparatūrai, kas pilnīguma labad aprakstīta zemāk. Šiem ir nepieciešams `daq` Python pakete, kas**nav iekļauta nevienā piegādātajā artefaktā**: kompilētais CLI exto (`scripts/Build-CLI.ps1` iestata `--nofollow-import-to=daq`, un transporti `pyserial` / `bleak` / `zeroconf` to neietver), un arī PyPI SDK pakete to nesatur. Tās darbojas tikai no avota izvilkuma, tāpēc uzskatiet tās par MAPIR iekšējo izstrādes ceļu, nevis par kaut ko, pēc kā tiekties.
- **`discover` / `list`** atrodas starp abiem: tās ir tiešas aparatūras komandas no avota izvilkuma, bet piegādātajā versijā tās izmanto `pool-discover` un aizmugure veic skenēšanu. Tādējādi skenēšana darbojas visur — kas ir svarīgi, jo tas ir vienīgais veids, kā uzzināt DAQ-M BLE MAC.

> **`chloros-cli daq --help`** (un `-h` / `help`) uzskaita `pool-*` apakškommandas — palīdzība tiek apzināti novirzīta uz pūla klientu, lai tā atspoguļotu komandas, kas faktiski tiek izpildītas. Ja izsaucat tiešās aparatūras apakškommandu piegādātajā versijā, tā beigsies ar skaidru kļūdu, nosaucot trūkstošo pakotni un norādot uz `pool-*`; nekas neiziet klusi. (`discover` / `list` ir izņēmums — tās novirza uz `pool-discover` un vienkārši darbojas.)
>
> **Viss, kas klientam nepieciešams, ir pieejams caur `pool-*`** — savienošanās, datu plūsma, ierakstīt kalibrētus `.daq` failus un mainīt kapu profilus. DAQ var vadīt arī no Python, izmantojot `chloros_sdk.connect_daq_sensor()`, kas izmanto to pašu apvienoto ceļu.

### DAQ sensora pirmās savienošanas darba plūsma

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` atsauce

| Apakškommanda | Mērķis |
| --- | --- |
| `daq pool-connect` (viedā atpazīšana) | Atver sensoru aizmugures pūlā. |
| `daq pool-connect --port PORT` | DAQ-U konkrētā seriālajā portā. |
| `daq pool-connect --ble` | DAQ-M pār BLE, automātiski skenēts MAC. |
| `daq pool-connect --mac MAC` | DAQ-M caur BLE ar zināmu MAC adresi (nozīmē `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E caur Ethernet ar zināmu resursdatoru. |
| `daq pool-connect --eth` | DAQ-E pār Ethernet, uzņēmējdators automātiski atklāts (mDNS + ARP rezerves risinājums; darbojas no tukša ARP keša vietnēs Windows un Linux). |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | Integrācijas loga / AE stāvokli. |
| `daq pool-connect --no-stream` | Izveidot savienojumu, bet vēl nesākt straumēšanu (turpināt ar `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Cap korekcijas profils. Noklusējuma iestatījums backendā ir `sunshine_cosine`. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Pārskata katru transportu, meklējot sensorus, ar kuriem varētu izveidot savienojumu, neizveidojot to. **Tādā veidā var atrast DAQ-M BLE MAC adresi.** `daq discover` / `daq list` piegādātajās versijās automātiski novirzās uz šo vietu. Sensori, kas jau ir atvērti pūlā, netiek uzskaitīti — savienots DAQ-M pārtrauc reklāmēšanu — tāpēc šiem sensoriem izmantojiet `pool-list`. |
| `daq pool-list` | Parādīt visus sensorus backend pūlā. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atbrīvot. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | Pēdējie N spektra rāmji. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Turpināt / apturēt straumēšanu. |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | Sākt / apturēt .daq ierakstu. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Mainīt cap korekcijas profilu darbības laikā. |

### Tiešās aparatūras apakškommandas (tikai avota izvilkumā — nav piegādātajās versijās)

> Uzskaitītas pilnīguma labad. Tām nepieciešams `daq` pakete „Python” kā arī `pyserial` / `bleak` / `zeroconf`, no kurām neviena nav iekļauta kompilētajā CLI vai PyPI SDK — tās darbojas tikai no MAPIR avota izvilkuma. **Ja izmantojat izlaisto Chloros versiju, tad izmantojiet iepriekš minētās `pool-*` komandas**; tās aptver savienošanos, straumēšanu, ierakstīšanu un kameras izvēli.

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

Atveriet, izveidojiet savienojumu ar un vadiet saglabātu „Chloros” projektu (mapes ar `cameras.json` + `sensors.json` + `project.json`). Viss tiek maršrutēts caur backend, tādējādi GUI un CLI nodrošina identisku aparatūras stāvokli.

### Apakškommandu atsauces

| Apakškommanda | Mērķis |
| --- | --- |
| `project open PATH` | Izdrukāt projekta ierīču sarakstu (kameras, masīvi, sensori). |
| `project devices PATH [--reconnect]` | Uzskaitīt vai atkārtoti palaist atklāšanu. |
| `project connect PATH [--cameras-only] [--sensors-only]` | Savienot visas saglabātās kameras / masīvus / sensorus. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Veikt vienu uzņēmumu no norādītas kameras vai masīva. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | N kadru sērija no norādītas kameras vai masīva (`-n/--count` noklusējums 5; `-i/--interval` sekundes starp kadriem, noklusējums 0). Kameru grupu sērijas atbrīvojas no atkārtotām sinhronizētām grupām (novecojušo attēlu uzraugs), lai daļēji cikliska kamera nevarētu atgriezt N kopijas viena kadra; izvada rezultātus pēc katras iterācijas. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | Pārsūtīšana uz disku, izmantojot aizmugures uzdevumu. `--poll-interval` = sekundes starp `/stats` pārbaudēm (noklusējums 2,0). |
| `project sensor read PATH NAME [--json]` | Jaunākais spektra kadrs. |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | Ierakstīt .daq failu. |
| `project run PATH RECIPE.yaml` | Izpildīt YAML/JSON datu ieguves recepti. `--dry-run` pārbauda, neizpildot. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Aprēķina masīva saskaņošanu — skatiet [zemāk esošo karogu tabulu](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Izvada pašreizējo saskaņošanas profilu. |
| `project align clear PATH NAME` | Dzēš kešēto profilu. |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | Nedaudz maina viena pakalpojuma transformāciju. |
| `project align export PATH NAME --to FILE` | Saglabāt profilu failā „JSON”. |
| `project align import PATH NAME --from FILE [--no-validate]` | Ielādēt saglabātu profilu. |

#### `project align calibrate` opcijas

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Izlīdzināšanas metode. **Šie rakstības veidi atšķiras no `lattice align-calibrate`**, kurā tiek izmantoti saīsinātie nosaukumi `orb` / `akaze` / `phase`; šajā opcijā abas komandas nav savstarpēji aizvietojamas. |
| `--model {translation, rigid, affine, homography}` | `affine` | Pārveido modeli, lai tas iederētos. |
| `--frames N` | `1` | Sinhronizēto kadruar vidējo vērtību. |
| `--reference SN` | galvenā | Atskaites kameras sērijas numurs; visi pārējie elementi tiek deformēti, lai atbilstu tam. |
| `--max-features N` | `5000` | ORB objektuskaita ierobežojums. |
| `--ratio-threshold F` | `0.75` | Lowe&#x27;s koeficients. |
| `--ransac-threshold-px F` | `3.0` | RANSAC iekšējo punktu slieksnis. |
| `--min-matches N` | `15` | **Kvalitātes slieksnis** — noraidīt risinājumu, ja iekšējo punktu saskaņojumu skaits ir mazāks par šo vērtību. |
| `--max-reproj-err-px F` | `4.0` | **Kvalitātes slieksnis** — noraidīt risinājumu, ja RMS reprojekcijas kļūda pārsniedz šo vērtību. |
| `--checkerboard RxC` | — | Plates ģeometrija `--method checkerboard`, piemēram, `9x6`. |
| `--name PROFILE` | tukšs | Profila nosaukums, kas iekļauts saglabātajā JSON. **Ne masīva nosaukums** — tas ir pozicionālais `NAME`. |

Šie divi kvalitātes vārti ir iemesls, kāpēc kalibrēšana var veiksmīgi atrisināt uzdevumu, bet tomēr
atteikties saglabāt: profils, kas neatbilst kādam no tiem, klusi reģistrētu nepareizi katru
turpmāko uzņemšanu, tāpēc tas tiek noraidīts, nevis saglabāts.

### Piemēri

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### Receptes DSL

`project run RECIPE.yaml` pieņem YAML vai JSON failu, kurā aprakstīta darbību secība:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Atbalstītās darbības: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. Darbībai `burst` darbībai ir nepieciešams `name` (obligāts), `count` (noklusējums 5), `interval` (sekundes, noklusējums 0), `output`, `format` un `settings` (tāds pats kameras iestatījumu veids kā `apply`); masīva sērijas izmanto to pašu nesen sinhronizētās grupas uzraugu kā `project burst`.

Palaidiet to:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## Vides mainīgie

| Mainīgais | Efekts |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Pārraksta aizmugurējās sistēmas „URL” (noklusējums — `http://127.0.0.1:5000`) — **tiek ņemts vērā tikai `lattice`, `project`, un `daq pool-*` komandu grupām.** Pamata komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) norāda uz `http://127.0.0.1:<port>` un ignorē šo mainīgo (IPv4 literāls apiet Windows `localhost`→`::1` ~2 s sodu par katru pieprasījumu), tādējādi tie vienmēr vēršas pret vietējo mašīnu. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1` pazemina masīva pārslodzes(kopējais pieprasījums uz vienu kameru &gt; sadursmju drošības vadu maksimums ar `pin_resolution`) uz skaļu brīdinājumu un turpināšanu, pieņemot GVSP pakešu zudumu. Tikai izmantošanai testēšanā — skatīt [Masīva fps un pārslogojuma modelis](#array-fps--burst-model). |
| `CHLOROS_CLI_MODE` | Iestata pats CLI; norāda aizmugurējam serverim, ka jāieslēdz paralēlā apstrāde. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` izlaiž GVSP rezerves pārbaudi (tikai ICMP rezultāti). **Tas atslēdz jumbo, tas ne tikai samazina žurnāla ierakstu skaitu** — kamera atbild uz DF pingiem tikai līdz 1500 katrā maršrutā, tādēļ šī pārbaude ir vienīgais veids, kā var noteikt jumbo. Ietaupa ~1 s uz katru kameru katrā savienojumā; izmaksā apmēram 1,45 reizes vairāk nekā vadu maksimālā jauda, ja tīkls *varētu* pārraidīt jumbo. „SDK” brīdina, kad to iestatāt. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Fiksē GVSP paketes izmēru uz N baitiem; pilnībā izlaiž pārbaudi. Ieteicams izmantot komandu (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`), nevis iestatīt to pastāvīgi: fiksēts izmērs vairs nepielāgojas tīklam, kas atrodas tā priekšā, un, fiksējot 9000 maršrutā, kas nespēj pārraidīt jumbo paketes, **katra** uzņemšanas laika pārsniegšanu ar `SC_ERR_TIMEOUT -1011`. |
| `TMPDIR` (Linux) | Pārraksta Nuitka onefile izvilkšanas direktoriju. CLI automātiski izmanto `/mnt/ssd/tmp`, ja tāds ir pieejams. |

---

## Izvades kodi

| Kods | Nozīme |
| --- | --- |
| `0` | Veiksmīgi. |
| `1` | Vispārīga kļūda (vairums apakškommandu kļūdu). |
| `2` | Argumenta kļūda. |
| `130` | Pārtraukts ar Ctrl+C. |

---

## Problēmu novēršanas norādes

- **&quot;Nepieciešama pieteikšanās&quot;** → Vienreiz palaidiet `chloros-cli login EMAIL PASSWORD` šajā datorā.
- **&quot;backend unreachable&quot;** → Palaižiet darbvirsmas lietojumprogrammu „Chloros” vai tieši palaidiet backend bināro failu (`chloros-backend`), vai pārbaudiet `CHLOROS_BACKEND_URL`, ja izmantojat attālinātu piekļuvi.
- **`lattice` komandas neizdodas ar kļūdu &quot;LATTICE kameras draiveri nav atrasti&quot;** → Nav instalēta Arena SDK izpildes vide; CLI tiek piegādāta kopā ar `win32api`, kas iekļauts Windows, taču C izpildes vide ir daļa no GUI instalētāja.
- **„Array connect“ / „Array Settings“ rāda „FRAMES WILL DROP“ vai „Reduce ROI to enable“** → Uzņēmējdatora tīkla kartes (NIC) uztveršanas gredzens ir pārāk mazs (parasti pēc tīkla kartes draivera atjaunināšanas tas tiek atiestatīts uz 32). Skatīt [Host NIC Setup &amp; Tuning](#host-nic-setup--tuning-lattice-arrays) — iestatiet `ReceiveBufferLen=256`, `PendingReceives=64`.
- **Dators „iekārtojas” pēc pārstartēšanas/izslēgšanas brīdī, pēc tam WMI `Invalid class` / tīkla karte netiekieslēgt / trūkst USB disku** → Novecojis USB 10GbE adaptera draiveris izraisa `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atjauniniet adaptera draiveri — skatiet [Host NIC Setup &amp; Tuning](#host-nic-setup--tuning-lattice-arrays).
- **Jetson swap brīdinājums** → Pievienojiet failu balstītu swap; failā „CLI” ir norādītas precīzas `fallocate` / `swapon` komandas.
- **Trūkst DAQ tiešo komandu** → Sagaidāms: piegādātajā `chloros-cli` pakotnē apzināti nav iekļauts `daq`, tāpēc ir pieejams tikai `pool-*` (arī PyPI SDK to neiekļauj). Izmantojiet `pool-*`, kas vadītu to pašu sensoru caur backend, vai `chloros_sdk.connect_daq_sensor()` no Python.

---

## Skatīt arī

- [Python SDK Atsauce](sdk-reference.md) — programmatisks ekvivalents katrai CLI komandai.
- [DAQ sensoru rokasgrāmata](../daq/README.md) — sensoru specifiska vadu pieslēgšana un kalibrēšana.
- Tiešsaistes dokumentācija: `https://mapir.gitbook.io/chloros/cli`
