# Chloros Python SDK Atsauce

**Versija:**

1.2.0**Izveidots:**2026-07-29 19:19 ·**Pārskatīts:** 2026-08-30**Pakete:** `chloros-sdk` (PyPI)**Mērķauditorija:** Optimizēts LLM lietošanai; cilvēkam saprotams.**Darbības joma:** Visas publiskās klases, funkcijas un palīgfunkcijas, ko piedāvā `import chloros_sdk`, ar piemēriem, kurus var kopēt un ielīmēt, aptverot attēlu apstrādi, vienas kameras vadību, sinhronizētus masīvus, DAQ sensorus un projekta automatizāciju.

Ja jums vajadzīgi tikai galvenie punkti, pārejiet uz:
- [Instalācija un ātrs sākums](#installation)
- [Smart-Connect LATTICE kameru masīviem](#smart-connect-for-lattice-cameras)
- [DAQ sensoru sesijas](#daq-sensor-sessions)
- [Projekta automatizācija](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)

---

## Arhitektūra 60 sekundēs

SDK ir plāns Python slānis virs Chloros aizmugurējās sistēmas (tas pats Flask serveris, ko izmanto gan darbvirsmas GUI, gan CLI). Automatizācijas nolūkā jūs importējat `chloros_sdk` un izsaucat augsta līmeņa metodes; aizkulisēs katrs izsaukums pārvēršas par HTTP pieprasījumu vietējam backendam 5000. portā — `http://127.0.0.1:5000/api/...` (apzināti nevis `localhost`, kas vispirms tiek pārveidots par `::1` uz Windows un izmaksā apmēram 2 s uz vienu pieprasījumu, ja backend darbojas tikai ar IPv4). Backend pārvalda aparatūras resursu kopumu — kameras, DAQ sensoriem, izlīdzināšanas profiliem, kadru buferiem — tādējādi SDK skripti var darboties vienlaikus ar GUI, nekonkurējot par seriālajiem portiem vai tīkla kartes joslas platumu.

Jūs izmantosiet trīs saskarnes:

1. **`ChlorosLocal` + bezmaksas funkcijas** (`process_folder`, `process_lattice_capture`) — attēlu apstrādes cauruļvads. Ar vienu Python izsaukumu varat apstrādāt visu mapi, veicot kalibrēšanu, debayerēšanu un indeksu eksportu.
2. **Viedās savienošanas rīki** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Atver pastāvīgu aizmugures sesiju reāllaika aparatūrai. Tāds pats „smart-prep” process kā GUI: tīkla pārbaude, slāņa automātiskāizvēle, PTP, AE sēšana, GPIO trigera konfigurācija.
3. **`ChlorosProject` / `open_project`** — Ielādē saglabātu projektu (mapes ar `cameras.json` + `sensors.json` + `project.json`), vienlaikus savieno visu un veic uztveršanu ar nosauktiem rokturiem.

Virsmas 1 un 2 **automātiski palaista vietējo aizmuguri**, ja tā vēl nav klausīšanās režīmā (tā pati komplektā iekļautā binārā programma, ko palaida GUI/CLI) — tādējādi vienkāršs skripts darbojas no jaunas komandrindas, bez nepieciešamības vispirms palaist aizmuguri. Lai atteiktos no šīs funkcijas, nododiet `auto_start_backend=False`, lai to atspēkotu (piemēram, ja norādāt uz attālo backend, kas nekad netiek palaists). Skatīt [Backend automātiskā palaišana](#backend-auto-start). „Surface 3” darbojas citādi: `open_project()` nepieņem `auto_start_backend` parametru, un `connect_all()` nekad nepalaiž aizmuguri — tas vienu reizi pārbauda `http://127.0.0.1:5000` vienu reizi un, ja nekas neatbild, klusi pārslēdzas uz tiešu (bez aizmugurējās sistēmas) `lattice_sdk` ierīces vadību. Tikai `proj.process()` un `stream(..., overlays=True)` lēni izveido `ChlorosLocal()` (kas veic automātiskopalaišanu).

Visas trīs ir autentifikācijas ierobežotas: vienreiz palaidiet `chloros-cli login` uz datora vai piesakieties, izmantojot darbvirsmas grafisko lietotāja saskarni. SDK izsaukumi bez derīgas sesijas izraisa `ChlorosAuthenticationError`.

Prasības:
- Python 3.7+ (kā norādīts paketē; izstrādāts/testēts ar 3.10)
- Vietējā datorā instalēta Chloros darbvirsma (aizmugures binārā programma ir iekļauta instalētājā)
- Aktīva Chloros+ pieteikšanās. SDK/CLI minimālais līmenis ir **Copper**vai augstāks (Copper / Bronze / Silver / Gold); bezmaksas**Iron**līmenim nav piekļuves SDK/CLI. Tas tiek piemērots**servera pusē**: katram pieprasījumam ar SDK/CLI marķējumu ir jābūt gan aktīvai sesijai, gan apmaksātam plānam, pretējā gadījumā backend atgriež `403` ar `error_code: PLAN_UPGRADE_REQUIRED` (kas tiek parādīts kā `ChlorosLicenseError`, izmantojot `ChlorosLocal`, un kā `ChlorosConnectError`, izmantojot `connect_*` palīgfunkcijas). Izslēgts lietotājs saņem `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — abas ir atšķirīgas, jo `chloros-cli login` atkārtota izpilde novērš pirmo kļūdu, bet nevar novērst otro.
- Lietošana bezsaistē tiek atbalstīta plāna papildlaika periodā: līmenis tiek nolasīts no servera validācijas keša (5 min) vai parakstītā, ierīceisaistītajā licenču kešatmiņā (30 dienas mēneša plāniem, līdz abonementa termiņa beigām gada plāniem). Kad šis papildlaiks beidzas, plāns pāriet uz bezmaksas versiju, un piekļuve SDK/CLI tiek pārtraukta, līdz ierīce var vismaz reizi sazināties ar serveri. `chloros-cli status` (`GET /api/license-status`) paliek pieejams bezmaksas līmenī, tādējādi iemesls ir redzams — tas ir vienīgais SDK/CLI maršruts, kas ir izņēmums no līmeņa vārtiem.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS vai jaunāka versija**, vai Jetson (JetPack 6). Ubuntu 20.04**netiek** atbalstīta: `.deb` atkarības ir atvasinātas no tā, ar ko saistās backend, ieskaitot `libc6 (>= 2.34)`, un Focal piegādā glibc 2.31.

---

## Instalēšana

Python SDK ir plāns Python slānis virs Chloros backend. Lai veiktu jebkuras darbības, kas pārsniedz dažas tikai DAQ darba plūsmas, jums ir nepieciešams **vietējā datorā instalēts Chloros darbvirsmas pakotne** (Windows instalētājs vai Linux `.deb`) — tas nodrošina backend bināro failu, Arena SDK izpildes vidi LATTICE kamerām un kalibrēšanas paketes.

Jaunākās lejupielādes: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### 1. solis — Instalējiet Chloros platformas paketi

#### Windows (.exe)

1. Lejupielādējiet `Chloros-Setup-x.y.z.exe` no lejupielādes lapas.
2. Palaižiet instalētāju un izpildiet vedņa norādījumus. Noklusējuma instalācijas ceļš ir `C:\Program Files\MAPIR\Chloros\`.
3. Vismaz vienu reizi palaidiet Chloros un piesakieties ar savu Chloros+ kontu.

#### Linux amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### 2. solis — Python instalēšana SDK

**Chloros instalētājs ietver atbilstošu SDK wheel failu.** Katrs Windows instalētājs un Linux .deb fails uz diska izvieto `chloros_sdk-X.Y.Z-py3-none-any.whl`, kas precīzi atbilst GUI / CLI / backend versijai. Jums nav jāseko līdzi PyPI, lai saglabātu sinhronizāciju.

#### Windows

Instalētājs automātiski palaista `pip install`, izmantojot komplektā iekļauto „wheel” failu un jūsu sistēmas Python (vēlams izmantot `py.exe` palaišanas programmu, citādi tiek izmantota `python -m pip`). Nav nepieciešama nekāda rīcība — `import chloros_sdk` darbojas jūsu Python vidē pēc veiksmīgas instalācijas. Ja datorā nav Python, instalētājs klusi izlaiž šo soli, un GUI + CLI turpina darboties.

#### Linux (.deb)

.deb fails novieto „wheel” failu `/usr/lib/chloros/sdk/`. `postinst` izdrukā precīzu komandu — PEP 668 distribūcijas pēc noklusējuma nepieļauj globālas pip rakstīšanas darbības, tādēļ mēs neveicam automātisku instalēšanu:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Jetson ierīcēm ar izolētu tīklu šis process notiek pilnīgi bezsaistē — ratiņš jau atrodas diskā.

#### Publiskais PyPI

Pip-tikai serveriem (nav instalēta Chloros darbvirsmas pakete; attālināta aizmugure vai tikai DAQ darba plūsmas):

```bash
pip install chloros-sdk
```

PyPI tiek atjaunināts izlaides versijas instalētāja kompilācijās, tādējādi publicētais „wheel” atbilst jaunākajai stabilajai versijai. Dev versijas (piem., `1.1.4.dev1`) tiek piegādātas tikai kopā ar instalētāja „wheel” failu.

#### Pārbaudiet

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Nepieciešama Chloros+ abonementa.** Visiem SDK izsaukumiem ir nepieciešama aktīva Chloros+ pieteikšanās. Palaidiet `chloros-cli login user@example.com 'YourPassword'` vienu reizi katrā datorā; piekļuves dati tiek saglabāti `~/.chloros/`.

### Vai man ir nepieciešama darbvirsmas pakete?

Pip pakete pati par sevi **nav** pietiekams lielākajai daļai darba plūsmu. Šeit ir norādīts, kas nepieciešams katrai SDK virsmai:

| SDK virsma | Vai nepieciešams darbvirsmas pakotne? | Kāpēc |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Jā** | Automātiski palaista aizmugures binārā programma `/usr/lib/chloros/chloros-backend` (Linux) vai `C:\Program Files\MAPIR\Chloros\…` (Windows). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Jā**(vietējais)**/ Nē**(attālinātais) | Nepieciešami tīrie HTTP klienti aizmugurē. Vietējais backend → nepieciešama darbvirsmas pakete. Attālais backend → `backend_url=`**caur tuneli** (skatīt Attālinātā aizmugurējā sistēma režīmu — piegādātās aizmugurējās sistēmas piesaista tikai lokālo atgriezenisko saiti). |
| `ChlorosProject` / `open_project` | **Jā** | Pārvalda saglabātos projektus caur backend. |
| Tiešās LATTICE klases (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Jā** | Nepieciešama Arena SDK nativā izpildes vide, kas ir iekļauta darbvirsmas paketē. Pretējā gadījumā `CAMERA_AVAILABLE` importēšanas brīdī ir `False`. |
| Tiešās DAQ klases (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **Nē** | Tīra Python izmantošana ar pyserial/bleak/zeroconf. Vide, kurā tiek izmantots tikai pip, var vadīt DAQ no gala līdz galam. |

### Attālinātais aizmugures režīms (tikai pip uzņēmējdators, izmantojot tuneli)

> **Piegādātais backend nav sasniedzams pa LAN.** Ražošanas
> versijas saistās tikai ar loopback (abas loopback ģimenes) un kategoriski noraida
> vienīgo režīmu bez loopback (`CHLOROS_CLOUD_MODE`), tāpēc
> `backend_url="http://<lan-ip>:5000"` **nevar darboties ar instalētu
> Chloros** — šis risinājums vienmēr darbojās tikai ar source/dev
> backend. Lai vadītu backend citā datorā, pašam jāpāradresē tā loopback
> ports un jānorāda SDK uz tuneli:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

Bezmonitora / CI / robotikas serveri var saglabāt vienu datoru ar pilnu darbvirsmas instalāciju kā „Chloros serveri”, bet visur citur — `pip install chloros-sdk` — taču datu pārraide starp tiem notiek, izmantojot iepriekš minēto lietotāja izveidoto tuneli, nevis tiešu LAN URL savienojumu.

> **Zināms ierobežojums — `ChlorosLocal` nespēj darboties tikai ar pip.** `ChlorosLocal(backend_url=BACKEND)` pašlaik savā konstruktorā atrisina vietējo aizmugures bināro failu *pirms* pārbauda URL, un izraisa kļūdu `ChlorosBackendError` („Chloros backend nav atrasts…”) ja nav instalēts neviens darbvirsmas pakotnes — pat tad, ja attālais backend ir sasniedzams. Tikai iepriekš minētā „smart-connect” saskarne (`connect_camera` / `connect_array` / `connect_daq_sensor`, kā arī `analyze_array_network` un palīgprogrammas `list_*` / `discover_*`) darbojas no datora, kurā ir instalēts tikai pip.

### Darba plūsma tikai ar DAQ (tikai „pip” serveris)

Ja jums nepieciešami tikai DAQ sensori un jūs neizmantojat LATTICE kameras vai attēlu apstrādi, „pip” pakete ir pilnībā autonoma:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

Nav nepieciešams ne backend, ne .deb, ne Chloros+ pieteikšanās, lai strādātu ar tiešo aparatūras DAQ.

---

## Ātrs sākums

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## API augstākā līmeņa indekss

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## Attēlu apstrāde — `ChlorosLocal`

Galvenā apstrādes plūsmas klase. Pirmajā lietošanas reizē palaista backend, izveido un konfigurē projektus, uzrauga gaitu, atgriež kopsavilkumus pēc izpildes.

### Konstruktors

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Metodes

| Metode | Apraksts |
| --- | --- |
| `create_project(project_name, camera=None)` | Izveido jaunu projektu (pēc izvēles ar kameras šablonu, piemēram, `"Survey3N_RGN"`). |
| `import_images(folder_path, recursive=False)` | Importē RAW/TIF/JPG/DNG attēlus **un `.daq` gaismas sensora ierakstus**. Atgriež `count` (attēlus) un `scan_count` (ierakstus). Brīdina tikai tad, ja mapē nav ne viena, ne otra. |
| `export_light_sensor(daq=True, csv=True)` | Ieraksta kalibrētos `.daq` + `.csv` par katru gaismas sensora ierakstu projektā, `<project>/Light Sensor/`. Skatīt [Gaismas sensora ieraksti](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Iestatīt apstrādes slēdžus. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Palaižiet apstrādes cauruļvadu. Atgriež `{"status": "complete", "async": False}`, kā arī `summary` atslēgu, ja aizmugurējā sistēma to nodrošina — skatiet [Kopsavilkums un padomi pēc apstrādes](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | Pārbaudiet aizmugurējās sistēmas stāvokli. |
| `logout()` | Dzēsiet kešēto autentifikācijas informāciju. |
| `shutdown_backend()` | Pārtrauciet aizmugurējo sistēmu (ja SDK ir palaista). |
| `discover_cameras()` | Atklāj LATTICE kameras **caur šīs instances backend** (`/api/camera/discover`). Atgriež vārdnīcu sarakstu (`serial`, `model`, `ip`, …) — ar tādu pašu struktūru, kādu redz GUI/CLI. Tukšs saraksts, ja nekas nav atrasts vai backend nav sasniedzams. |
| `camera_capture(output_dir, format="tiff", **settings)` | Uztver vienu kadru**caur backend**(automātiski palaists ar šo rokturi), lai tas tiktu sagatavots tāpat kā GUI/CLI (noklusējumā 12 biti, pūla atkārtota izmantošana, iegultie kalibrēšanas metadati). Noteikt mērķi ar `serial=` vai `device_index=`; nodot `exposure`/`gain`/`pixel_format`/`preset` kā `**settings`. Atgriež vecā tipa metadatu vārdnīcu (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Izveido pārklājuma apvienotus priekšskatījuma kadrus no apvienotās kameras — viegls MJPEG klients, izmantojot aizmugurējās sistēmas `/api/camera/<serial>/stream-annotated` maršrutu (zebra / režģis / krustlīnijas / histogramma / maksimumu izcelšana / punkta iezīmēšana servera pusē). `decode=True` ģenerē BGR masīvus; `False` ģenerē neapstrādātus JPEG baitus. Pieejams arī katram projektam kā `ChlorosProject.stream(overlays=True)`. |

Izmantojiet kā konteksta pārvaldnieku, lai garantētu tīrīšanu:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Gaismas sensoru ieraksti — kalibrēti `.daq` + `.csv`

DAQ-U / DAQ-M / DAQ-E ierakstus var veikt **bez** to kalibrēšanas paketes. Tieši to
pēc noklusējuma dara publiskie [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
ierakstītāji (`record_daq.py`): tie ieraksta neapstrādātos sensoru skaitījumus un marķē
failu, lai Chloros iegūtu šī sensora rūpnīcas kalibrāciju **pēc sērijas numura** — vispirms no vietējās kešatmiņas,
pēc tam no MAPIR mākoņa — un piemēro to importēšanas brīdī.

Chloros rezultātu atpakaļ ieraksta kā divus produktus katram ierakstam, zem
`<project>/Light Sensor/`:

| Produkts | Kas tas ir |
| --- | --- |
| `<name>_calibrated.daq` | Pārstrādājams arhīvs — tā pati shēma kā reāllaika ierakstam, tagad norādot paketi, kas to izveidojusi. Tā atkārtota importēšana to **nekalibrē** otrreiz. |
| `<name>_calibrated.csv` | Spektrālā starojuma intensitāte W/m²/nm uz paša sensora viļņu garuma tīkla, viena rinda katram mērījumam, kā arī fotometriskās kolonnas (kopējā jauda, fotopiskais/skotopiskais lukss, PPFD un tā sadalījums pa zilo, zaļo un sarkano spektru, maksimālā viļņa). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Tikai sensori bez pakotnēm (DAQ-A).** Neapstrādāti spektrālie sensora skaitījumi — *ne* starojuma intensitāte. Skatīt zemāk. |

`process()` veic šo eksportu kā vienu no saviem posmiem. Tam **nav** nepieciešami attēli:
atsevišķi lidojošs gaismas sensors ir pirmklasīga darba plūsma, un šādam projektam pēc būtības nav
nekādu attēlu.

**DAQ-A ieraksti tiek eksportēti kā neapstrādāti skaitļi.** DAQ-A sērija ir radusies pirms sērijas
komplektu sistēmas ieviešanas un tai nav nekāda komplekta, ko lejupielādēt — tā tiek kalibrēta laukā, izmantojot
atstarošanas mērķi, tāpēc tai nekad nav bijis nepieciešams komplekts. Šie ieraksti tiek eksportēti
ar `_raw` sakni, nevis `_calibrated`: ar atšķirīgu faila nosaukumu, nevis ar atzīmi
failā, jo informācijai ir jāiztur nosūtīšana pa e-pastu kā vienkāršs nosaukums.
`.csv` galvenē ir norādīts `raw spectral sensor counts (NOT irradiance)` un brīdinājums, ka
vērtības ir salīdzināmas **viena faila ietvaros** — tieši tam, kādam nolūkam tās izmanto
mērķa balstītā kalibrēšana —, nevis starp dažādiem sensoriem. No jaudas atkarīgās fotometriskās kolonnas (kopējā jauda,
fotopiskais/skotopiskie luksi, PPFD) tiek atgriezti kā **NULL**, nevis integrēti no skaitļiem.

DAQ-U / DAQ-M / DAQ-E, kura pakete vienkārši nevarēja tikt iegūta, joprojām tiek **izlaista**,
nevis ierakstīta neapstrādātā veidā: šajā gadījumā pakete pastāv, un “atjaunot savienojumu un pārstrādāt” ir reāls ieteikums.

Vecākas versijas **v1.01 / v1.02** ieraksti (tos raksta DAQ-A-SD) nesatur atsevišķu laika posmu katram nolasījumam,
tikai faila rakstīšanas laiku. Attēla↔lejupvērstās plūsmas saskaņotājs tos joprojām noraida — saskaņot
kadru ar ierakstīšanas laiku būtu nepamanāmi nepareizi —, bet eksportētājs tos nolasa, un
CSV izdrukā `clock=daq_created_on`, tādējādi produkts norāda, uz kādiem pulksteņiem tas balstās.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Ieraksts, kura kalibrēšanas pakete nevar tikt lejupielādēta (bezsaistē vai sensors bez
kalibrēšanas failā), tiek ziņots ar kodu `skipped` **norādot iemeslu**. Tas nekad
netiek izrakstīts kā „kalibrēts” fails, kas satur neapstrādātus skaitījumus — izveidojiet savienojumu ar internetu un
izpildiet to no jauna, un eksportēšana tiks pabeigta.

### Progresa atgriezeniskie izsaukumi

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Kopsavilkums un padomi pēc izpildes

Pēc pabeigšanas `process()` iegūst `GET /api/processing-summary` un pievieno galveno daļu kā `result["summary"]`. Iegūšana ir vislabāk-pūļu un nekad neblokē veiksmīgu atgriešanos — ja kopsavilkums nav pieejams, `process()` pārslēdzas uz vienkāršo `{"status": "complete", "async": False}` formātu. Katrs ieraksts `summary["hints"]` — pilni teikumi ar ieteikto risinājumu, piemēram, kāpēc izpildes rezultāts bija nulle — tiek arīizvadīts kā Python `UserWarning`, tādējādi izpildes ar nulles rezultātu ir pašdiagnosticējošas, pat ja jūs nekad nepārbaudāt vārdnīcu:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` ir mašīnlasāmā daļa:

| Atslēga | Ko tā uzskaita |
| --- | --- |
| `models` | Kameru grupas darbības ciklā. |
| `images_in_groups` | Avota attēli šajās grupās. |
| `targets_found` | Atklātie atstarošanas mērķi. |
| `images_calibrated` | Attēli, kurus kalibrēja izpildes laikā. |
| `exported_files` | **Attēlu produktu faili, kurus izveidoja darba cikls.** |
| `daq_recordings_exported` / `daq_recordings_skipped` | Gaismas sensoru ieraksti, kas apzināti tiek skaitīti atsevišķi — tie ir no cita posma un pastāv arī tādos ciklos, kuros nav nekādu attēlu, tāpēc to iekļaušana liktu domāt, ka ciklā, kurā tika veikta tikai datu ieguve, tika eksportēti attēli. |

Papildus tiem: `summary["output_dirs"]` (katrs katalogs, kurā veikta ierakstīšana),
`summary["light_sensor_export"]`, `summary["stopped"]` (ir spēkā, ja lietotājs pārtrauca
izpildes ciklu, tādējādi daļējie skaitļi netiek interpretēti kā pabeigta darbības sērija ar nepietiekamu rezultātu), un
`summary["groups"]` (sadale pa grupām).

`exported_files` tiek reģistrēts procesā **rakstīšanas brīdī**, nevis pēc tam skenēts no
projekta attēlu objektiem. Paralēlās un GPU stratēģijas veido savus attēla
objektus (GPU ceļu gadījumā — darba apakšprocesos), tāpēc vecā skenēšana ziņoja
`0 file(s) written` par katru šādu izpildi un pēc tam izvadīja norādi par nulles eksportiem — izpildēs,
kurās viss bija darbojies. Ja jūs veidojat skriptu, balstoties uz šo skaitli, tagad veiksmīga paralēla darbība
ziņo par skaitli, kas nav nulle.

Izlaisto failu ziņojumi norāda iemeslu, ko lasītājs faktiski konstatējis katram failam —
nelasāma shēma, trūkstošs pakotnes fails, rakstīšanas kļūda — **bez dublējumiem**, tādējādi divdesmit faili,
kas izlaisti viena iemesla dēļ, tiek uzrādīti kā viens iemesls, nevis divdesmit tā atkārtojumi.

> **`process()` netiek izraisīts, ja izpildes laikā netiek ģenerēti attēli.** Šī ir vienīgā vieta, kur SDK un
> CLI apzināti atšķiras: `chloros-cli process` uzskata „tika pieprasīti rezultāti, bet neviens netika
> uzrakstīts” kā kļūdu un beidz darbu ar rezultātu, kas nav nulle, turpretim SDK atgriežas normāli un ziņo par
> stāvokli, izmantojot `summary` / norādes. Ja jūsu apstrādes ķēdei vajadzētu apstāties tukšā izpildes reizē, pārbaudiet to
> pats — pārbaudiet `summary` (vai saskaitiet failus projekta mapē), nevis paļaujieties uz
> izņēmuma neesamību. Parastie iemesli ir ievades mape, kas netika atpazīta kā
> uzņemšanas mape, un produkti, kas tika izlaisti kā nepiemēroti esošajām kamerām (piemēram, starojums no kamerām, kas atrodamas tikai RGB
>).

### Ērtības funkcijas

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### Atbalstītās vērtības

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Radiometriskā izvade (LATTICE multispektrālā apstrādes ķēde)

`process` apstrādes ķēdes LATTICE multispektrālā (M3C/M3M) eksporta līmenis — `reflectance` (noklusējums), `radiance`, `sensor-response` vai `all` (katrs attēlam piemērojamais režīms) — atbilst projekta **&quot;Radiometriskā izvade&quot;** apstrādes iestatījumam. `configure()` tam ir atsevišķs atslēgvārds:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Papildu izejas iespēja — projekta `"Radiometric output"` atslēgvārda rakstīšana caur `custom_settings` — joprojām darbojas, bet atcerieties, ka tas aizstāj visu iestatījumu bloku (skatiet brīdinājumu zemāk):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (noklusējuma iestatījums) sadala kameras starojuma intensitāti ar **laika zīmogam atbilstošo DAQ lejupvērsto starojumu**, kas automātiski tiek noteikts no ierakstītā `.daq` (DAQ-U/M/E)**vai DAQ-M nativā `.csv`**, kas atrasts kopā ar attēliem; jebkurškamera vai DAQ kalibrēšanas pakete, kas lokāli trūkst, tiek**automātiski lejupielādēta no AWS** pirmajā lietošanas reizē. CLI parāda to saskaņā ar-tipa produktu slēdžiem `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **aizstāj** visu aprēķināto iestatījumu bloku (tas pēc dizaina apiet `configure()` pārējos atslēgvārdus un validāciju). Kad to izmantojat, iekļaujiet katru `Project Settings` atslēgvārdu, kas jums ir svarīgs, kā redzams iepriekšējā piemērā.

---

## Smart-Connect LATTICE kamerām

Pastāvīgas aizmugures sesijas reāllaika aparatūrai. Tiek izmantoti tie paši galapunkti, ko izmanto GUI, tādējādi darbība ir identiska visās SDK / CLI / GUI vidēs.

### Viena kamera — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

#### `connect_camera()` paraksts

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

#### `CameraSession` metodes

| Metode | Apraksts |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Izlasa GenICam mezglus; atgriež `{nodes, errors, enums, device}`. |
| `set_settings(**kwargs)` | Raksta mezglus pēc draudzīgā nosaukuma (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | Uztver **vienu** kadru. Atgriež viena elementa sarakstu ar kadra metadatu vārdnīcām. (Sērijveida/vairāku kadru uzņemšana tika noņemta — ja nepieciešama sērija, izsauciet `capture()` cilpā.) |
| `disconnect()` | Atbrīvo no pūla. Neveic nekādu darbību, ja esam pievienojušies jau atvērtai sesijai. |

`capture()` eksporta kontroles (tāds pats modelis kā masīvam + GUI):

- `processing` / `levels` — `processing="all"` saglabā visus piemērojamos eksporta veidus; `levels=["raw","radiance"]` saglabā tikai tos (pārraksta `processing`). Lai izmantotu aizmugurējās sistēmas noklusējumu, izlaidiet abus.
- `force_daq=True` — saglabā piešķirto DAQ/DLS rādījumu kā `.daq` papildu failu pat tad, ja tiek veikta tikai neapstrādātu datu ieguve, lai rāmi vēlāk varētu pārstrādāt par atstarošanas koeficientu/indeksu. Neveic darbību, ja nav saistīts DAQ.

### Sinhronizētais masīvs — `ArraySession` (Smart-Prep)

`connect_array` ir **ieteicamais sākuma punkts** daudzkameru konfigurācijām. Tas aizkulisēs palaida pilnu GUI smart-prep darbplūsmu:

1. **Tīkla analīze** (`/api/camera/array/recommend`) — nosaka lielāko kadru izmēru, kas atbilst sim-emit līmenim, nezaudējot kadrus.
2. **Līmeņa automātiskā izvēle** — `sim-capture-sim-emit`, ja vads to spēj apstrādāt; citādi `sim-capture-ftd-stagger` vai `slip-emit-and-capture`.
3. **Automātiska samazināšana**— klusi samazina rāmja izmēru / palielina binningu, ja vads nespēj uzturēt pieprasīto izšķirtspēju.**Šis drošības tīkls neattiecas uz kopējo pārsubskripciju**: pārāk daudz kameru vadiem nevar atrisināt, samazinot kadrus — skatīt [Pārslogotība](#over-subscription-the-per-cam-floor).
4. **PTP ir ieslēgts** pēc noklusējuma — laika zīmogi starp kamerām ir salīdzināmi ar precizitāti līdz mikrosekundēm.
5. **Automātiska pikseļu formāta izvēle katrai kamerai** — RGB kameras → `BayerRG8`, multispec → `BayerRG12`.
6. **AE sākotnējā iestatīšana** — tiek saglabāts katras kameras pašreizējais AE stāvoklis, lai savienojums neatsāktu ekspozīcijas iestatīšanu darbības laikā.
7. **GPIO trigera konfigurācija** — `connect_array` aktivizē visas kameras (`TriggerMode=On`, `TriggerSource=Line2`), lai galvenās kameras impulsi vadītu pakļautās kameras pa M8 kabeli. Šis solis attiecas tikai uz masīvu: ja tiek atvērta viena kamera ar `LatticeCamera`, tā darbojas brīvā režīmā.

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### `connect_array()` paraksts

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`force_tier` vērtības:
- `"sim-capture-sim-emit"` — patiesa vienlaicība (visas kameras izšauj vienā un tajā pašā takts malā).
- `"sim-capture-ftd-stagger"` — elastīga laika domēna nobīde (kameras raidīšanas laiki ir nedaudz nobīdīti, tādējādi paketes tiek sērijveidā pārraidītas pa vadu).
- `"slip-emit-and-capture"` — secīga uzņemšana katrai kamerai atsevišķi (nav laika sinhronizācijas; vienīgā iespēja, ja neviens rāmja izmērs neatbilst simulācijai).

`wire_ceiling_mbps` pārraksta **uzņēmējdatora ilgstošo vadu budžetu** MB/s — vienīgais
skaitlis, no kura ir atkarīgs visa masīva resursu sadalījums. Atstājiet to kā `None`, lai izmantotu automātiski noteikto
vērtību. Samaziniet to, ja masīvs ziņo par GVSP bojātiem rāmjiem: automātiskā vērtība tiek aprēķināta
pēc tīkla kartes paziņotā savienojuma ātruma, kasneatspoguļo USB adapterus, vājas PCIe joslas un
noslogotas koplietošanas struktūras — un šī pārvērtēšana izpaužas kā bojāti rāmji, nevis kā
redzami lēns savienojums. Vērtība tiek saglabāta projekta masīva reģistrācijas blokā, tādējādi,
atverot to no jauna vai vēlāk izmantojot `connect_array`, tā tiek atjaunota tāpat kā jebkurš cits masīva iestatījums.
Skatīt [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames).

#### Pārslogotība (minimālā robeža katrai kamerai)

Sim-emit pacing katrai kamerai piešķir daļu no sadursmju drošā vadu budžeta, kura minimālā robeža ir **8 MB/s katrai kamerai**(`per_cam_floor_bps`). Tiklīdz `N × floor` pārsniedz sadursmju drošo maksimālo robežu, masīvs**pārsniedz vadu kapacitāti**— kļūdas režīms ir GVSP pakešu zudums, nevis zemāks kadru ātrums — un nav nekāda risinājuma attiecībā uz kadru izmēru:**apvienošana un ROI samazina baitu skaitu kadrā, nevis regulēto baitu skaitu sekundē**, ko salīdzina kopējā pārbaude. Praktiskie maksimālie ierobežojumi pilnā izšķirtspējā uz 1 GbE resursa:**6 kameras ar 1500 MTU, 9 ar jumbo rāmjiem** (`max_cams_collision_safe` analīzes atbildē norāda jūsu vadu maksimālo robežu). Risinājumi: mazāk kameru, jumbo rāmji no gala līdz galam, vai ātrāks tīkla interfeiss.

- Atbildēs `analyze_array_network()` un `/api/camera/array/connect` ir iekļauti `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` un `per_cam_floor_bps`. Ja `oversubscribed` ir patiesa, prognoze **nullej fps laukus** (`achievable_fps_max` / `fps_bright` / `fps_dark`), nevis ziņo par maldinošu, lēnu, bet darbojošos ātrumu.
- `POST /api/camera/array/connect` pieņem `pin_resolution` ķermeņa parametru (**tikai HTTP — nav SDK kwarg**; `connect_array` to neizpauž). Fiksēšana noņem binninga pakāpeniskās samazināšanas drošības tīklu, tādējādi pārslogots savienojums ar iestatītu `pin_resolution` tiek**kategoriski noraidīts**, norādot kļūdu, kurā minēti visi risinājumi. Bez fiksēšanas savienošana turpinās ar pakāpenisko samazināšanu, bet brīdina, ka samazināšana nevar atbrīvot kopējo resursu.
- Darba vides avārijas izeja: iestatiet `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` aizmugurējās vides iestatījumos, lai pazeminātu atteikuma līmeni līdz skaļam brīdinājumam — jūs tomēr izveidojat savienojumu un pieņemat pakešu zudumu.

#### Masīva stāvoklis — kura apakšsistēma zaudē rāmjus

`GET /api/camera/array/<array_id>/capability` rāda reāllaika `health` bloku
savienotajā masīvā, kas tiek pārvērtēts **10 sekunžu** ilgā logā. Tas sadala rāmju zudumu
divos cēloņos, kuriem nepieciešami pretēji risinājumi, nevis vienā „nepilnīgā” rādītājā, kas
nenorāda ne vienu, ne otru:

| Lauks | Kas tas nozīmē | Kura apakšsistēma |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (uz katru seriālo interfeisu) | Rāmis **ieradās, bet bija strukturāli bojāts**— GVSP paketes zudums. |**Tīkls**: vadu jauda, sinhronizācija, NIC RX gredzens, MTU |
| `never_arrived_rate_pct` (katram sērijas numuram) | Kadrs **vispār neieradās**— kamera neizšāva vai no tās nekas neizgāja. |**Izraisītājs / sinhronizācija**: M8 kabelis, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Sliktākais kameras rādītājs katram. | — |
| `per_cam_rate_pct` | Kombinētais nepilnīgo rādītāju īpatsvars uz vienu kameru (abi iemesli kopā). | — |
| `stable_for_seconds` | Cik ilgi katra kamera ir palikusi zem 0,01 %. | — |

Līdzās `health` tajā pašā ierakstā ir norādīts skaitlis, no kura atkarīgs viss piešķīrums:

| Lauks | Kas tas nozīmē |
| --- | --- |
| `wire_ceiling_mbps` | Spēkā esošais uzņēmēja ilgstošais vadu budžets, MB/s. |
| `wire_ceiling_source` | No kurienes šis skaitlis ir iegūts, vārdos — piemēram, `USB-capped 200 MB/s (was theoretical 1062; …)` vai `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, ja to iestatīja `wire_ceiling_mbps=` to iestatīja. |
| `nic_is_usb` | `true` USB Ethernet adapterim. |

Šim galapunktam nav SDK apvalka — lasiet to tieši:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Lasot to:** `gvsp_corrupt_rate_pct`, kas nav nulle, ar `never_arrived_rate_pct` vērtību 0 nozīmē, ka
izraisīšana un kabeļa sinhronizācija ir ideālas, un 100 % zudumu rada tīkla ceļš — samaziniet
`wire_ceiling_mbps` un atkārtoti izveidojiet savienojumu. Pretējais modelis norāda uz sinhronizācijas kabeli vai
izraisītāja līniju.

> **`target_fps` nav rādītājs bojātiem rāmjiem.** GevSCPD ritms tiek iestatīts vienreiz
> savienošanās brīdī, tādēļ trigera frekvences samazināšana maina darba ciklu, nevis
> vienlaicīgās pārraides pārsūtīšanas ātrumu. Izvērtētais 5× pieprasījuma samazinājums nedeva uzlabojumus, savukārt
> vadu maksimālās ātruma robežas pazemināšana no 240 līdz 200 MB/s samazināja bojāto rāmju īpatsvaru tajā pašā iekārtā no 10,4 % bojāto rāmju skaitu līdz
> 0,00 %.

> **TRI032S programmaparatūras versijā nav pieejama automātiskā samazināšana datu plūsmas vidū.** Darbojošs masīvs nevar
> to novērst pats; atvienojiet un atkārtoti pievienojiet, lai savienošanās laika izvēlne veiktu atkārtotu plānošanu, ņemot vērā
> jauno maksimālo ātrumu.

**USB Ethernet adapterim zonde nosaka 200 MB/s ierobežojumu** neatkarīgi no tā
nominālajiem parametriem: efektivitātes tabula, kas pārvērš savienojuma ātrumu ilgstošā rādītājā, ir
atvasināta no PCIe, un USB tīkla karte paziņo savu Ethernet savienojuma ātrumu, vienlaikus būdama ierobežota ar
USB šķiedru un tās draiveri. Ierobežojums ir absolūts, nevis daļa — USB 1 GbE adapteris
nodrošina ~80 MB/s, un tas netiek ietekmēts.

#### `ArraySession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=10.0)` | Darbojas ar `{fps, ptp, frame_count, last_error, …}`. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | Viena sinhronizēta ierakstīšanas grupa. Atgriež `CaptureResult` (kadru vārdnīcu saraksts + `.skipped`). Eksportēšanas vadības elementi zemāk. |
| `capture(..., smart=True)` | **Vieds uzņemšanas režīms** — gaida, līdz AE stabilizējas visās kamerās, un tad aktivizējas. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | Ātrākā uzņemšana: tikai neapstrādāti dati + piešķirtais DAQ rādījums (+ brīvais kombinētais indekss). Atbilst GUI pogai „Ātrākā uzņemšana”. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Vienreizēja / Nepārtraukta / intervāls vienā ierobežotā ciklā. Atgriež `list[CaptureResult]`.**Nepieciešams `count` un/vai `duration_s`**, lai tas beigtos (SDK nav Ctrl+C). |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | Sāk ierakstīt kombinētā indeksa skatu reāllaikā video/GIF formātā → `RecorderHandle`. Viens kompozīta ierakstītājs uz vienu masīvu. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | Sākt augstas kadru frekvences neapstrādātu Bayer sērijas uzņemšanu → `RecorderHandle`. Veiciet atkārtotu apstrādi bezsaistē ar `build_video()`. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Veiciet saglabātās neapstrādāto attēlu sērijas atkārtotu apstrādi bezsaistē, lai iegūtu kalibrētus video. Bloķējas, līdz process ir pabeigts (`wait=True`) un atgriež `{outputs, errors, combined}`. |
| `build_video_status(job_id, timeout=15.0)` | Pārbauda bezsaistes izveides uzdevumu: `{running, result, error, burst_dir}`. |
| `disconnect()` | Atbrīvo visu masīvu. |

`capture()` eksporta kontroles (tāds pats galapunkts, kādu izmanto GUI/CLI):

- `processing` / `levels` — `processing="all"` (vai `levels=["raw","radiance",…]`) saglabā katru piemērojamo eksporta tipu katrai kamerai; viena `processing` vērtība saglabā tikai šo līmeni.
- `aligned=True` — deformē katra elementa neapstrādāto eksportu atbilstoši masīva [izlīdzināšanas profilam](#array-alignment) (kopīgi reģistrēts); neapstrādātie dati paliek neizkropļoti, bet transformācija tiek ietverta metadatos. Ja masīvam nav profila, tiek izmantota neizlīdzināta versija (ar brīdinājumu, kas parādās rezultāta `alignment`).
- `render_index=False` — izlaiž uz katru kameru attiecināto veģetācijas indeksa pārklājumu; pēc noklusējuma to renderē tur, kur tas ir konfigurēts.
- `force_daq=True` — saglabā piešķirto DAQ/DLS rādījumu kā `.daq` papildu failu, pat ja nevienam izvēlētajam līmenim tas nav nepieciešams.

**TIFF saspiešana (tikai HTTP regulētājs):**`ArraySession.capture()` nenosūta `compression` atslēgu, tādēļ tiek piemērots aizmugurējās sistēmas noklusējums — `POST /api/camera/array/capture` nolasa `compression` ķermeņa parametru, `"deflate"` pēc noklusējuma (bezzaudējumu zlib L1 + horizontālais prediktors, ~4,1 MB uz vienu pilnas izšķirtspējas kadru). `"none"` raksta nesaspiestu datu plūsmu (~6,3 MB/kadrs) ar**~5 reizes ātrāku rakstīšanu** — abi formāti ir bezzaudējuma un tiek nolasīti identiski importēšanas brīdī. SDK neizvada nekādu kwarg šim nolūkam; izvairīšanās iespēja ir `chloros-cli lattice array-capture --compression none` vai neapstrādātais HTTP. DEFLATE arī patur Python GIL, tāpēc saspiestā rakstīšana netiek paralizēta starp katras kameras rakstīšanas pavedieniem — ilgstošai 8 kameru pilnas izšķirtspējas uzņemšanai ar sensora ātrumu ir nepieciešams `compression: "none"`. Sīkāka informācija: [CLI Atsauce → masīva uzņemšana](cli-reference.md).**Eksporta pārrakstīšana katram elementam (tikai HTTP):**tas pats galapunkts pieņem arī `exclude_serials` (saraksts — izslēdz elementus no saglabātā kopuma; masīvs joprojām darbojas kā viena sinhronizēta grupa, un izslēgtie elementi tiek atgriezti `excluded`), `serial_levels` (`{serial: [level tokens]}` pārrakstījumi katrai kamerai) un `serial_index` (`{serial: bool}` indeksa pārklājuma pārrakstījumi katrai kamerai). Tie ir GUI-paritātes ķermeņa parametri un**vēl nav SDK kwargi**; locekļi, kas nav iekļauti kartēs, tiek aizstāti ar masīvu`levels` / `render_index`.

##### Izlaisto kameru pārbaude — `CaptureResult.skipped`

`ArraySession.capture()` atgriež `CaptureResult`, kas ir `list` apakšklase: to var iterēt, indeksēt, `len()` — visi esošie modeļi turpina darboties. Jaunais kods var pārbaudīt `.skipped` atribūtu, lai redzētu, kuras kameras tika izslēgtas un kāpēc. Visbiežāk sastopamais gadījums ir RGB kameras jauktajāfiltru masīvā, ja tiek pieprasīts `processing="radiance"` vai `"reflectance"` — starojuma intensitāte uz vienu Bayer elementu platjoslas sensoram nav nozīmīga, tāpēc aizmugurējā sistēma izlaiž šos kameru elementus, nevis ģenerē bezjēdzīgus rezultātus.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Iemesla simboli atbilst šādam paraugam: `<level>-not-applicable-to-rgb-cam` (viens ieraksts par katru izlaisto līmeni, katrs satur `level`). Atstarošanasspecifiskie izlaišanas iemesli ir `reflectance-skipped-no-fresh-dls` (nav pieejams jauns lejupvērstais mērījums), `reflectance-skipped-bound-daq-unavailable (…)` (neizdevās sasniegt piesaistīto datu ieguves ierīci) un `dls-uncalibrated-band-<nm>` — josla galvenokārt atrodas ārpus DAQ gaismas sensoraradiometriski kalibrētā diapazona (~374–974 nm), tāpēc absolūtā, uz DAQ balstītā atstarojuma sadalīšana tiek noraidīta, un kadrs tiek strauji pazemināts līdz sensora reakcijas līmenim. No piegādātajiem SKU to izraisa tikai F988; šīs kameras atbalstītā darbplūsma ir atstarojuma paneļa darbplūsma.

`processing` līmeņi:

| Līmenis | Izeja |
| --- | --- |
| `"raw"` | Vienkanāla Bayer (mono kameras: viens diapazons) tieši no sensora. |
| `"debayered"` *(SDK noklusējums)* | 3-kanālu BGR, izmantojot bilineāro demosaiku (monohronās kameras: 1-kanāla pelēktoņu skala). |
| `"radiance"` | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi. Tikai multispektrālais režīms — RGB kameras tiek izlaistas. |
| `"reflectance"` | uint16 0..32768 (Pix4D-saderīgs); absolūtas atsauces nodrošināšanai nepieciešama reāllaika DAQ savienošana. Tikai multispektrālais režīms. |
| `"display"` | Pilna ķēde, kas atbilst GUI priekšskatījumam (CCM + WB + gamma saskaņā ar kameras profilu). |
| `"all"` | **Viens fails katram piemērojamajam līmenim** katrai kamerai (atbilstoši GUI opcijai „Capture All“ / CLI noklusējumam). Atgrieztajā `CaptureResult` failā katrā `(cam, level)` ietilpst viens kadra vārdnīca, kurā norādīts līmenis; nepiemērojamie līmeņi parādās `.skipped` failā. DAQ rādījums, kas izmantots jebkuram atstarošanas kadram, tiek saglabāts kā `.daq` papildu fails. |

> **Piezīme — noklusējuma vērtība atšķiras no CLI.** `ArraySession.capture()` noklusējuma vērtība ir `processing="debayered"`; komandas `chloros-cli lattice array-capture` noklusējuma vērtība ir `processing="all"`. Lai atspoguļotu CLI/GUI daudzlīmeņu saglabāšanu, `processing="all"` ir eksplicīti jāpārsūta no SDK.

### Uztveršanas režīmi un ierakstītāji

Masīva virsma atspoguļo GUI ierakstīšanas paneli: vienreizējais / nepārtrauktais / intervāla / ātrākais aizslēga režīms, kā arī divas ierakstīšanas ierīces (tiešraides kompozītvideo un neapstrādāta sērija → pārstrāde bezsaistē).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**ir SDK nepārtrauktas/intervāla cilpa. Tā kā nav `Ctrl+C`, lai to pārtrauktu no skripta, jums**jā** nodot `count` un/vai `duration_s` (tas apstājas, kad tiek sasniegts kāds no tiem). `interval_s` tiek mērīts no katras cikla sākuma (atbilstoši GUI). Pārējie kwargi tiek nodoti tieši uz `capture()`.
- **`record`** ir *uzraudzības līmeņa*: tas fiksē reāllaika kombinētā indeksa kompozītu tā, kā tas tiek parādīts, tādēļ kombinētajai straumei jābūt atvērtai, lai kadri tiktu ierakstīti. Viens kompozīta ierakstītājs uz katru masīvu (izraisa kļūdu, ja kāds jau darbojas).
- **`burst` → `build_video`** ir *analīzes līmeņa*: `burst` raksta neapstrādātus kadrus + katra kadra manifestu + vienu `.daq` katram atsevišķam DLS nolasījumam zem `<output>/bursts/<base>/` ar uzņemšanas cikla pilnu ātrumu (bez ķēdes, bez exiftool, bez tiešraides). `build_video` sinhronizē katru kadru ar tuvāko `.daq` un atkārtoti palaista importēšanas procesa starojuma/reflektances/indeksa ķēdi. `products` ir `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` saraksts (noklusējums: apvienotais indekss). `burst().stop()` arī automātiski uzsāk apvienotā indeksa veidošanu pēc labākās iespējas, kas tiek atgriezts kā `build_job` pārtraukšanas rezultātā.

#### `RecorderHandle`

To atgriež `ArraySession.record()` un `ArraySession.burst()`. Izmantojiet to kā konteksta pārvaldnieku, lai automātiski pārtrauktu darbību, izkāpjot no darbības jomas, vai vadiet to manuāli.

| Loceklis | Apraksts |
| --- | --- |
| `job_id` | Aizmugures uzdevuma identifikators (str). |
| `kind` | `"composite"` (no `record`) vai `"raw"` (no `burst`). |
| `start_stats` | Vārdnīca, ko atgriež `start` izsaukums. |
| `result` | `None` darbības laikā; galīgā apstāšanās rezultāta vārdnīca pēc apstāšanās. |
| `stats(timeout=10.0)` | Darba statistika reāllaikā (ierakstītie kadri, sasniegtais kadrskaitlis sekundē, pagājušais laiks). |
| `stop(timeout=60.0)` | Pārtrauc ierakstīšanu; atgriež un saglabā kešatmiņā galīgo rezultātu. Idempotents (otrais izsaukums atgriež kešēto rezultātu). |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### Pievienošanās jau savienotam masīvam — `attach_array`

Ja masīvs jau darbojas (to atvēra GUI vai iepriekšējā SDK sesija izsauca `connect_array`), izmantojiet `attach_array`, lai iegūtu tā rīku, nevis veikt atkārtotu savienošanos. Šādā situācijā `connect_array` vienmēr izdod kļūdu „Kamera <sn>jau atrodas masīvā<id>”, jo `/array/connect` nosūtīšana uz masīva elementu nav idempotenta; `attach_array` nolasa `/api/camera/array/list` un salīdzina vai nu pēc array_id, vai pēc serials.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Paraugs: skriptiem SDK, kas darbojas kopā ar darbvirsmas grafisko lietotāja saskarni, vispirms jāmēģina `attach_array` un jāpāriet uz `connect_array`, ja pūlā vēl nav neviena masīva.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Svarīgi — context-manager iziešana PĀRTRAUC savienojumu.**`ArraySession.disconnect()` vienmēr veic POST pieprasījumu uz `/array/disconnect`; šeit nav pievienota-nepiederīga aizsargmehānisma, kāds ir `CameraSession` / `DAQSensorSession` gadījumā. Ja izmantojat kopīgu nomu ar GUI un nevēlaties nojaukt masīvu, izbeidzot darbības jomu,**nelietojiet `with` bloku** — saglabājiet rādītāju parastā mainīgajā un izlaidiet eksplicīto `disconnect()`:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### Tīkla analīzes palīgrīks

Noderīgs pirms masīva atvēršanas — prognozē, vai jūsu ierosinātie iestatījumi būs piemēroti:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status` ir viens no `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (citādi — `error`). `auto_capped_fps` nozīmē, ka pieprasītā izšķirtspēja atbilst RX gredzenam tikai pie ierobežota trigera ātruma — saglabājiet izšķirtspēju un pārejiet no `target_fps=result["recommended"]["recommended_target_fps"]` uz `connect_array` (skatīt [6. piemēru](#6-capability-probe-before-connecting-a-4-cam-array)).

**Kā izlasīt projekciju** (tāds pats modelis kā GUI paneļa „Array Settings”):

- **Sērija (`frame_bytes_total`) tiek summēta katrai kamerai atsevišķi, izmantojot katras kameras reālo pikseļu formātu.**Mono**M3M**kameras pārraida Mono12 (2 B/px) neatkarīgi no tā, kādu `pixel_format` vērtību jūs norādāt, tādējādi 4 kameru pilnas izšķirtspējas kadrs ir**~25 MB** ar trim mono kamerām, nevis ~12,6 MB, kā to paredz pieņēmums par pilnībā 8 bitu formātu. Aizmugurējā sistēma nosaka katras kameras formātu pēc tās modeļa.
- **Admittance (`burst_fits_nic_ring`) ņem vērā datu plūsmas iztukšošanu**, nevis visu sēriju-pretstatā gredzenam: sim-emit darbojas, ja hosts iztukšo RX gredzenu ātrāk, nekā kameras to piepilda. 10G hosts + 1 GbE kameras**pieņem** pilnas izšķirtspējas datus pat tad, ja datu plūsma pārsniedz gredzena kapacitāti; 1 GbE hosts bloķē (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` ir konservatīvs sērijas izguves maksimums** — `max(readout+emit, N×emit)` ar katraskatras kameras datu pārraides ātrums ir ierobežots līdz 1 GbE kameru savienojumam, neatkarīgi no ekspozīcijas. Piemēram, ~2,8 kadri sekundē 4 kameru pilnas izšķirtspējas 12 bitu masīvam (atbilst izpildes laikā izmērītajiem ~2,7–3,0). Pilnais modelis: [CLI Atsauce → Masīva kadru skaits sekundē un sērijas uzņemšanas modelis](cli-reference.md#array-fps--burst-model).
- **Pārslogotība (`oversubscribed: true`) nozīmē, ka N × minimālais rādītājs katrai kamerai pārsniedz sadursmju drošo maksimālo robežu** — fps lauki (`achievable_fps_max` / `fps_bright` / `fps_dark`) rāda 0, un automātiskā samazināšana/apvienošana to nevar novērst (tās samazina baitu skaitu kadrā, nevis regulēto baitu skaitu sekundē). Risinājumi ir mazāks kameru skaits, jumbo rāmji vai ātrāks tīkla interfeiss; `max_cams_collision_safe` ziņo par maksimālo robežu (6 kameras ar pilnu izšķirtspēju uz 1 GbE ar 1500 MTU, 9 — ar jumbo rāmjiem). Atbildē ir iekļauti arī `aggregate_demand_bps`, `collision_safe_ceiling_bps` un `per_cam_floor_bps` (8 MB/s). Skatīt [Pārslogošana](#over-subscription-the-per-cam-floor).

### Atklāšana un uzskaitīšana

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## Smart-AE / Smart-Capture

LATTICE masīvi, tiklīdz tie ir pieslēgti, fonā nepārtraukti veic AE, taču nesen iestatītai ainai ir nepieciešams brīdis, lai konverģētu. **Smart-capture** ir ērta funkcija: tā pārbauda katras kameras ekspozīciju, pagaida, līdz masīvs visā logā ir stabils, un tad uzsāk uzņemšanu. Tas atbilst GUI darbībai: darbvirsmas lietotnes „viedās” uzņemšanas poga izsauc to pašu aizmugures galapunktu.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Izmantojot `ChlorosProject` (nākamajā sadaļā), jums ir pieejami papildu iestatījumi:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Viedā-AE politika pēc noklusējuma ir konservatīva. Padariet `exposure_tolerance_pct` stingrāku, ja veicat precīzu radiometrisko darbu; padariet to plašāku strauji mainīgām ainām, kurās vēlaties vienkārši „pietiekami tuvu”.

---

## DAQ sensoru sesijas

Pastāvīgs aizmugures resursu kopums spektrālajiem sensoriem (DAQ-U caur USB, DAQ-M caur BLE, DAQ-E caur Ethernet). Atspoguļo kameras darbību: viedā atpazīšana, resursu kopuma atkārtota izmantošana, idempotenta pievienošana.

### Viedā atpazīšana (bez konfigurācijas)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Prioritāte: Ethernet → BLE → USB. Norādiet jebkuru konkrētu norādi, lai fiksētu pārraides veidu.

### Fiksēts transporta veids

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### `DAQSensorSession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=10.0)` | Pūla ieraksta kopsavilkums (straumēšanas/ierakstīšanas stāvoklis, viļņu garuma diapazons, kalibrēšanas SHA, integrācijas laiks, frame_avg, AE stāvoklis). |
| `latest(n=1, timeout=10.0)` | Atgriež līdz N pēdējiem spektra kadriem. |
| `stream_start()` / `stream_stop()` | Turpināt / apturēt straumēšanu (rokturis paliek atvērts). |
| `record_start(output_dir=None, device_name=None)` | Sākt .daq faila ierakstīšanu. Atgriež faila ceļu. Neizpilda DAQ-U/M bez AWS kalibrēšanas paketes (izņemot DAQ-E). |
| `record_stop()` | Pārtrauc ierakstīšanu. Atgriež `{path, rows}`. |
| `disconnect()` | Atbrīvo no pūla. Neveic darbību, ja rokturis ir pievienots, bet nepieder. |

> **Kapacitātes korekcijas profili (`cap_id`) nav SDK regulētājs.** `connect_daq_sensor()` / `DAQSensorSession` neizpauž `cap_id` parametru vai `set_cap` metodi. Izvēlieties flotes ierobežojumakorekcijas profilu, izmantojot CLI (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) vai aizmugurējās sistēmas `/api/daq` HTTP maršrutiem (`/api/daq/connect` un `/api/daq/<id>/cap-id` pieņem `cap_id`).

### Atklāšana — adreses meklēšana savienojuma izveidei

`discover_daq_sensors()` skenē USB / BLE / ETH, meklējot sensorus, kurus *varētu* atvērt. Tas ir DAQ ekvivalents `discover_lattice_cameras()`, un vienīgais veids, kā iegūt **DAQ-M BLE MAC** — DAQ-E ierīcei ir uzņēmuma nosaukums, bet DAQ-U — COM ports, taču MAC nav ne uzrakstīts uz ierīces, ne uzskaitīts operētājsistēmā.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Lauks | Apraksts |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM ports / BLE MAC / datorvārds — nodod `connect_daq_sensor` kā `port=` / `mac=` / `eth_host=`. |
| `display` | Cilvēkam saprotams nosaukums. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` vai `None` portam, kuru skenēšana nevar identificēt (USB seriālie adapteri bez zondes nav atšķirami, tādēļ nezināmie tiek parādīti, nevis slēpti). |
| `extra` | Informācija par katru pārraides veidu (BLE paziņotais nosaukums, USB ražotājs, DAQ-E ip/fw/…). Tukšas vērtības tiek izlaistas. |

| Parametrs | Noklusējums | Apraksts |
| --- | --- | --- |
| `transports` | visi trīs | Sekvence (vai CSV virkne), kas ierobežo skenēšanu. Vērts norādīt, ja zināt, ko vēlaties — BLE ir lēnākais posms. |
| `scan_timeout` | 5 | Skenēšanas logs katram pārraides veidam sekundēs; backend ierobežo to līdz 1–20. |
| `timeout` | 60,0 | HTTP maksimālais laiks visam izsaukumam (kā arī citur SDK). |
| `auto_start_backend` | `True` | Izveido vietējo backend, ja neviens nedarbojas. Nekad neizveido attālinātu `backend_url`. |

> **Sensori, kas jau ir atvērti pūlā, netiek parādīti.** Savienota BLE perifērija pārtrauc reklāmēšanu, un atvērtu COM portu nevar pārbaudīt, tādēļ atklāšanas sarakstā tiek uzskaitīts tas, kas ir *pieejams savienošanai*. Tiek sagaidīts tukšs rezultāts tieši pēc tam, kad esat kaut ko pieslēdzis — izmantojiet `list_daq_sensors()`, lai redzētu to, kas jums jau ir. Transporti, kuru skenēšana nevar tikt izpildīta (nav instalēts bleak / zeroconf), tiek izlaisti, nevis izraisa kļūdu, tādējādi ierīce bez Bluetooth joprojām saņem savus USB un ETH atbildes.

### Saraksts

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Co-Lietošana kopā ar GUI / CLI

Ja GUI jau ir atvērts sensors, izsaucot `connect_daq_sensor(port="COM3")` no Python, tiek atgriezts rokturis ar atzīmi `already_connected=True`. Tādējādi sesijas `disconnect()` ir bezdarbības operācija, tādējādi jūsu SDK skripts neizrauj sensoru no GUI, izietot no skopas.

### Tiešās aparatūras klases (bez aizmugures)

`daq_sdk` tiek-eksportēts ar `chloros_sdk`, tādējādi jūs varat vadīt sensorus no gala līdz galam procesa ietvaros bez aizmugures:

> **Pieejamība:**`daq_sdk` tiek piegādāts kopā ar Chloros datora instalācijā,**nevis** kopā ar PyPI pakotni — `pip install chloros-sdk` nodrošina `lattice_sdk`, bet atstāj `chloros_sdk.DAQ_AVAILABLE == False`. Pirms šo klašu izmantošanas pārbaudiet šo iestatījumu; sistēmā, kurā darbojas tikai pip, vadiet sensoru, izmantojot [`connect_daq_sensor()`](#daq-sensor-sessions), kam nav nepieciešamas lokālās transporta bibliotēkas.

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

Ieteicams izmantot „smart-connect” ceļu (`connect_daq_sensor`), ja vēlaties kopīgu piederību ar GUI; izmantojiet tiešās klases skriptiem bez grafiskās saskarnes, kuriem sensors pieder ekskluzīvi.

---

## Projekta automatizācija — `ChlorosProject`

Saglabāts Chloros projekts ir mape, kas satur `cameras.json` + `sensors.json` + `project.json`. `open_project` ielādē manifestu, un `connect_all` pieslēdz visas saglabātās ierīces ar to saglabātajiem iestatījumiem — tādā pašā aparatūras stāvoklī, kādu radītu grafiskā lietotāja saskarne.

### Vienkāršs piemērs

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

Vai arī kā konteksta pārvaldnieks:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` metodes

| Metode | Apraksts |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | Atrod un savieno katru saglabāto ierīci. Atgriež savienošanas ziņojumu par katru klasi. Izmanto darbojošos aizmuguri, ja tā klausās uz `127.0.0.1:5000`; pretējā gadījumā klusi pāriet uz tiešu (bez aizmugures) `lattice_sdk` ierīču vadību — tā nekad neizveido aizmuguri. |
| `disconnect_all()` | Pārtrauc visas savienojumus. |
| `capture_all(output_dir=".")` | Viens kadrs no katras kameras + masīvs + spektrs no katra sensora. |
| `stream(camera, overlays=False, fps=10.0)` | Ģenerators, kas ģenerē BGR `numpy` kadrus no nosauktas kameras (vai masīva). `overlays=False` ir tiešs `lattice_sdk` attēlu ieguves cikls (matricas ģenerē `{serial: frame}` vārdnīcas). `overlays=True` maršrutē caur `ChlorosLocal.camera_stream()` → aizmugurējās sistēmas`/api/camera/<serial>/stream-annotated` MJPEG plūsmu, kur kameras saglabātais `ui.overlay` bloks tiek nodots kā vaicājuma parametri. Nepieciešams aizmugurējās sistēmas režīms un **autonomā kamera**: tiešā režīma kamera izraisa `RuntimeError` (backend nevar iegūt kameru, kas pieder šim procesam), bet masīvs izraisa `NotImplementedError` (uzliek kompozīciju katrai kamerai — straumē elementu pēc nosaukuma). Vienreizējās darbības ekvivalents: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Veic izlīdzināšanu katram pašlaik pieslēgtajam masīvam. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Izpilda kalibrēšanas/indeksēšanas procesu projekta attēliem (ietver `ChlorosLocal.process`; šīs četras ir **vienīgās** pieņemamās kwargs — `indices=` utt. izraisa `TypeError`; indeksus iestata ar `ChlorosLocal.configure()`). Lēni veido `ChlorosLocal()`, kas automātiski palaista aizmuguri. |

Atribūti:
- `proj.cameras` — `Dict[str, CameraHandle]`, indeksēts pēc nosaukuma UN sērijas numura.
- `proj.arrays` — `Dict[str, ArrayHandle]`, indeksēts pēc nosaukuma UN array_id.
- `proj.sensors` — `Dict[str, SensorHandle]`, kuras atslēgas vārds ir nosaukums UN slot_id.
- `proj.config` — `project.json["config"]` vārdnīca.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Apstrādes līmeņi.** `capture()`, `grab()` un `frame_stream()` visiem ir viens un tas pats `processing`
tokenu, un ķēde ir kumulatīva — katrs līmenis izpilda visu, kas atrodas virs tā:

| Līmenis | Izeja | Piezīmes |
| --- | --- | --- |
| `raw` | 1-kanālu Bayer, sensora nativais | Nav demosaika. Šajā līmenī pārklājumi nav pieejami. |
| `debayered` | 3-kanālu BGR (**noklusējums**) | Bilineāra demosaika. Vienīgais līmenis, kas darbojas bez aizmugures režīma. |
| `radiance` | float32, W/m²/sr/nm | Pilna radiometriskā ķēde: demosaika + 3×3 atdalīšana (multispektrāla) + DSNU + līdzenā lauka korekcija + NIST skala, kur ekspozīcija × pastiprinājums ir izdalīts, lai vērtības būtu absolūtas. |
| `reflectance` | uint16, 32768 = 1,0 | Starojuma intensitāte, dalīta ar lejupvērsto starojuma intensitāti (ρ = π·L/E). Nepieciešams DLS/DAQ rādījums — skatīt piezīmi zemāk. |
| `display` | 8-bitu sRGB-līdzīgs | GUI ekvivalents attēls: CCM + baltā balanss + gamma, izmantojot kameras aktīvo krāsu profilu. |

Jebkurš cits rādītājs, izņemot `debayered`, prasa backend režīmu; tiešā režīma kamera izraisa
`NotImplementedError`. `reflectance` ir nepieciešams izmantojams lejupvērstais starojuma rādījums — kadra beigu punkts automātiski ievada
apkopotos DAQ datus automātiski kameras DLS slotā, taču, ja nav piesaistīts DAQ, ķēde noraida
reflektances izvadi un godīgi atzīmē pazeminājumu atgrieztos metadatos, nevis klusi
atdod zemākas kvalitātes rezultātu.

> **Reflektances DN skala — to nedrīkst ieprogrammēt cietā kodā.** LATTICE atstarojums izmanto `32768` = ρ 1,0 un atzīmē
> XMP `Chloros:PixelScale=32768`; Survey3 atstarojums izmanto `65535` = ρ 1,0 un nesatur
> `Chloros:*` tagus. Izlasiet tagu un daliet ar to. Tas ir definēts uint16 domēnā, tādēļ tas paliek
> `32768` visos formātos, kas veic pārskalēšanu (16 bitu TIFF, 8 bitu PNG/JPG, 32 bitu procenti) — vispirms normalizējiet
> saglabāto datu tipu atpakaļ uz uint16 (×257 no 8 bitu, ×65535 no float). Vienīgais izņēmums:
> 8 bitu avota ieraksts, kas rakstīts kā 8 bitu TIFF, tiek *nogriezts*, nevis pārskalots, tāpēc to neapraksta nekāda skala
> — Chloros šajā gadījumā pilnībā izlaiž `PixelScale` un MicaSense tuplu. Trūkstošu
> tagu LATTICE atstarošanas failā uzskatiet par „nav derīga mēroga”, nevis kā noklusējumu.

> **EXIF tiek pārnests uz eksportu.** `process()` kopē avota uzņēmuma GPS bloku
> **un tā ExifIFD** uz katru produktu, tādējādi eksportētajās failos ir `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` un `CameraSerialNumber`, kā arī
> ģeoreferencēšanu. `FocalLength` ir tas, no kā Pix4D aprēķina zemes paraugu attālumu — bez tā
> rekonstrukcija tiek veikta pilnīgi nepareizā mērogā (vienā izmērītajā gadījumā 411 m liela teritorija
> tika pārvērsta par 47,8 km lielu). Kopija apzināti nav `-all:all`: IFD0 strukturālās birkas sabojā
> LATTICE izvadi, un `ExifImageWidth`/`Height` ir izslēgti, jo tie apraksta avota
> uzņemšanu, nevis eksportēto rastra attēlu.

Uzņemšanas posma apakškarodziņi (attiecas uz radiometriskajiem līmeņiem — `radiance`, `reflectance`, `display`):

| Karodziņš | Noklusējums | Nozīme |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + vienmērīgais lauks + 3x3 atdalīšana + NIST radiometriskā skala. |
| `apply_white_balance` | `True` | WB LUT. DLS atbalsts, ja DAQ ir piesaistīts kamerai. |
| `apply_index` | `False` | Veģetācijas indeksa novērtēšana. |
| `index_expression` | `None` | Formulas pārrakstīšana. Ja nav tukšs → indekss tiek automātiski aktivizēts. |
| `annotated` | `False` | GUI dekorāciju (zebra/tīkls/pīķi) pārklāšana. Nav pieejams `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Atgriešanas tips ir `CapturePathMap`, nevis `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` ir `Dict[str, Union[str, List[str]]]`: vienlīmeņa
> `processing` katram sērijas numuram piešķir vienu ceļu, savukārt daudzlīmeņu (`"all"` vai
> eksplicīts `levels` saraksts) piešķir tam **sakārtotu sarakstu** ar visiem produktiem, kas saglabāti šai
> kamerai. Kombinēts tiešraides kompozīts, ja tāds tiek straumēts, ierodas zem papildu
> `"combined"` atslēgas, nevis zem sērijas numura. Kods, kas pieņem `str`, nedarbojas
> saraksta formā, un neviens tipa pārbaudītājs tam neiebilst — anotācijā kādu laiku pēc saraksta formas ieviešanas bija norādīts `Dict[str, str]`,
> tāpēc šis aliass pastāv. Normalizējiet,
> ja vēlaties plakanu formu:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### Masīva izlīdzināšana

`ArrayHandle` atklāj pilnu izlīdzināšanas virsmu. Profili pēc noklusējuma ir pieejami tikai sesijas laikā — lai tos saglabātu, eksplicīti izsauciet `export_alignment()`.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Izlīdzināšana savienošanās brīdī

`connect_all(align=...)` var automātiski izlīdzināt katru masīvu savienošanās brīdī:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Ja nav norādīts, tiek izmantots `project.json["config"]["auto_align_on_connect"]`.

### `SensorHandle`

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## Tieša aparatūra (bez aizmugures sistēmas)

Ja vēlaties pilnīgi izvairīties no atkarības no backend (CI, bezgalvas roboti, iegultās sistēmas), importējiet `lattice_sdk` un `daq_sdk` tieši — abus atkārtoti eksportē `chloros_sdk`. Aizsardzība uz `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk` atrodas PyPI paketē (bet tam ir nepieciešama Arena SDK darblaika vide), savukārt `daq_sdk` tiek piegādāts tikai kopā ar datora instalāciju.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Iestatījumi un izraisītājs

Trīs no četriem iestatījumiem darbojas **brīvā režīmā**: kamera nepārtraukti eksponē, un
`capture()` atgriež nākamo kadru. `triggered` ir izņēmums — tas aktivizē
kameru, lai tā reaģētu uz aparatūras signālu 2. līnijā, tādēļ tā neko neuzņem, kamēr šāds signāls nav saņemts.

| Iestatījums | Izraisītājs | Lietot, ja |
| --- | --- | --- |
| `default` | brīvā darbība | vispārējai lietošanai |
| `high_speed` | brīvā darbība | 8 bitu, 60 fps ierobežojums, īsa ekspozīcija |
| `high_quality` | brīvā režīma | 12 bitu, bez kadru skaita ierobežojuma — parastā izvēle fotogrāfijām |
| `triggered` | **gatavs darbam, 2. līnija** | kamera ir pieslēgta ar M8 sinhronizācijas kabeli, un to iedarbina kāds cits signāls |

Ja izvēlaties `triggered` (vai paši iestatāt `trigger_mode="On"`), kad nekas
nedarbina 2. līniju, katrs `capture()` beigsies ar laika limitu — pareizi, jo jūs lūdzāt
kamerai gaidīt. SDK to paskaidro, kad tas notiek; skatiet
[SC_ERR_TIMEOUT uzņemšanas laikā](#direct-hardware-backend-free).

> **Piezīme — „GVSP probe” / `SC_ERR_TIMEOUT -1011` ziņojumi savienojuma izveides brīdī nav kļūdas.**&gt; Izveidojot savienojumu, SDK mēģina vienoties par**jumbo rāmjiem** (9000 baitu GVSP paketes), lai nodrošinātu lielāku caurlaidspēju. Tiešā punkts-punkts tīkla kartes savienojumā (piem., savienojumā-vietējā `169.254.x.x` adrese) tīkls parasti nespēj pārraidīt jumbo rāmjus, tāpēc šī pārbaude beidzas ar laika limitu un protokolā tiek reģistrētas šādas rindas:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Tas ir **paredzētais rezerves risinājums**: SDK automātiski atgriežas pie standarta 1500 baitu pakešiem, un kamera turpina savienoties kā parasti (sekojošās `[chunk-enable …]` rindas ir daļa no parastās savienošanās secības). Attēlu uzņemšana joprojām darbojas.
>
> Jūs varat izlaist šo pārbaudi, bet **tā nav tikai žurnāla ierakstu slāpētājs — tā atslēdz jumbo rāmjus.** Kamera atbild uz „Don&#x27;t-Fragment” pingiem tikai līdz 1500 baitiem neatkarīgi no tā, cik labs ir jūsu tīkls, tādēļ, izmantojot tikai ping testu, nekad nevar atrast jumbo rāmjus; šī pārbaude ir vienīgais veids, kā to izdarīt. Atspējojiet to, un kamera jebkurā tīklā uz visiem laikiem darbosies ar standarta 1500 baitu pakešiem:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Tas ir vērts tikai tādā tīklā, par kuru *zināt*, ka tas nespēj pārraidīt jumbo rāmjus, kur tas ietaupa aptuveni vienu sekundi savienošanās laika katrai kamerai. Tā kā tas ir reāls kompromiss, nevis tikai kosmētisks, SDK tagad to norāda, kad jūs to izmantojat:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Neaiztiekiet to, ja vien jums nav iemesla.** Ja funkcija paliek ieslēgta, katrs savienojums no jauna izmēra jūsu faktisko tīklu: pieslēdzieties komutatoram, kas atbalsta jumbo paketes, un nākamais savienojums pats automātiski sāks izmantot jumbo paketes, bez nepieciešamības veikt konfigurāciju vai pārstartēt sistēmu.
>
> Ja *vēlaties* jumbo datu caurlaidspēju, aktivizējiet jumbo no gala līdz galam (NIC MTU 9000 + komutators, kas tos caurlaida), vai arī fiksējiet to ar `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000`, ja zināt, ka savienojums to atbalsta — tomēr ieteicams izmantot `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` katrai komandai atsevišķi, nevis iestatīt to pastāvīgi, jo fiksētais izmērs izlaiž pārbaudi un pārtrauc pielāgošanos tīklam, kas atrodas tā priekšā. **Katrai** ierīcei ceļā ir jāpārraida jumbo paketes — ieskaitot jebkuru PoE sadalītāju vai injektoru, kas parasti ir iemesls, kāpēc citādi jumbo paketes atbalstoša konfigurācija nespēj tās pārraidīt.

> **`SC_ERR_TIMEOUT -1011` laikā, kad darbojas `capture()` / `grab*()`, ir atšķirīga problēma — tā ir reāla kļūda.**&gt; Iepriekš minētā piezīme attiecas tikai uz `-1011`, ko reģistrējis**savienojuma laika zondes**. Tāda pati kļūda, kas parādās**uzņemšanas** laikā, nozīmē, ka kamera ir veiksmīgi savienota, bet nesūta nekādus attēlus:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> To var atpazīt pēc kameras, kuras *vadības* kanāls darbojas pareizi — atklāšana notiek, iestatījumi un `[chunk-enable …]` ieraksti ir veiksmīgi —, bet *katrs* kadrs pārsniedz laika limitu.
>
> **Parastais iemesls ir tas, ka kamera ir iestatīta uz aparatūras trigeri.** Ar `trigger_mode="On"` un `trigger_source="Line2"` kamera neko neizsūta, kamēr M8 sinhronizācijas kabelī neparādās elektriskais signāls. Ja šai līnijai nav pievienots kabelis, katrs attēla uzņemšanas mēģinājums gaida bezgalīgi. Kamera nav bojāta un tīkls darbojas pareizi — tā rīkojas tieši tā, kā tai ir norādīts.
>
> `CameraSettings()` un `default` / `high_speed` / `high_quality` iestatījumi darbojas brīvā režīmā, un uzņemšana, kurai beidzas laika limits , kamēr ir ieslēgta, sniedz skaidrojumu, nevis vienkārši izdrukā `-1011`. `PRESETS["triggered"]` ieslēdz Line2, kā paredzēts.
>
> Lai piespiestu jebkuru kameru darboties brīvā režīmā:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Ja ar `trigger_mode="Off"` joprojām beidzas laiks, kamera patiešām nenodod datus — nosūtiet mums žurnālu un `ip link show`.

#### Krāsu profili (RGB tiešraides priekšskatījums) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` izvēlas displeja krāsu profilu **reāllaika priekšskatīšanai** RGB kamerās (daudzspektrālās kameras ignorē šo iestatījumu):

| Profils | Nozīme |
| --- | --- |
| `raw` | Pilnībā apiet radiometrisko ķēdi. |
| `linear` | DSNU + flat + WB, bez CCM, bez gamma. |
| `natural` | Lineārs + izmērīts CCM + sRGB gamma, tikai ar vienkāršu apdari (krāsu izlīdzināšana + spilgtumu desaturācija) — reālistiskais noklusējums. |
| `enhanced` | `natural` plus pilnīga hub-parity apdare (malu izlīdzināšana, vibrance, CLAHE lokālais kontrasts). Bagātāks izskats, aptuveni **divkāršojot apstrādes izmaksas uz vienu kadru**, tādējādi samazinot LIVE kadru ātrumu. |
| `custom_temp` | `natural`, bet baltā balanss fiksēts uz `custom_cct_k` Kelvina (DLS ignorēts; ierobežots līdz 2000–10000 K aizmugurējā procesa pusē). |

Profils ir **tikai tiešraides priekšskatīšanai** paredzēts ātruma/izskata regulētājs: saglabātajiem kadriem vienmēr tiek nodrošināta pilnībā bagātīga apdare neatkarīgi no izvēlētā profila, tādēļ `natural` izvēle, lai atgūtu kadru apstrādes laiku, nesamazina uz diska saglabātā materiāla kvalitāti. Nezināms profils palielina `ValueError`; ja ir pieejams chloros backend, izmaiņas tiek nosūtītas arī uz to, lai nākamais priekšskatījuma kadrs tās atspoguļotu (tiešie SDK lietotāji bez backenda joprojām saņem iestatījumu izmaiņas).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Mono (M3M) kameras un `Calibration`

Mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) ir vienjoslas: viena pelēkto toņu plakne, bez Bayer mozaīkas, nav 3×3 spektrālās pārklāšanās matricas. `Calibration` to atpazīst un parāda `is_mono` karodziņu. Atstarošanās joprojām tiek piemērota kā radiometriska karte katrai joslai (atdalīšanas matrica ir identitātes matrica), taču daudzjoslu aprēķini, izmantojot vienu kameru, rada jēgpilnus rezultātus, nevis bezjēdzīgus:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Lai izveidotu veģetācijas indeksu, izmantojot mono kameru aparatūru, apvienojiet vairākas M3M kameras ar dažādiem viļņu garumiem vienā saskaņotā daudzjoslu kopā (skatīt [Masīva saskaņošana](#array-alignment)) un aprēķiniet indeksu visai šai kopai, nevis tikai vienai kamerai.

DAQ tiešais režīms:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` pieņemamās atslēgas**— tieši `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; vairs netiek izmantots, tā vietā izmanto `cap_id`), `filter_model` (DAQ-M) un `cap_id` (visi DAQ veidi; `None`/`""`/`"none"` = sensoru bez vāka, bez vāka korekcijas). Nezināmas atslēgas tiek**klusi ignorētas** — piemēram, `{"integration_time": 64}` neko nedara (tam jābūt `integration_time_ms`). Atgriež `{"applied": [...], "errors": {...}}` un nekad neizraisa kļūdu.

`chloros_sdk` atkārtoti eksportē tikai iepriekš izmantoto kodola virsmu. Pilnā `daq_sdk` publiskā API (22 nosaukumi) pievieno šādus elementus — importējiet tos tieši no `daq_sdk`:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

## Izņēmumi

Uztveriet bāzes klasi, lai apstrādātu „visas kļūdas, kas radušās Chloros”:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` un `ChlorosConfigurationError` tiek eksportēti augstākajā līmenī kopā ar pārējiem; tos var importēt arī no `chloros_sdk.exceptions`, kā parādīts.

Hierarhija:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Pilnīgi izstrādāti piemēri

### 1. Mapes apstrāde ar pielāgotu progresa joslu

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Reāllaika LATTICE masīvs → atstarošanās + DAQ atsauce

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Projektvadīta datu ieguves kampaņa

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Daudzkameru kadru plūsma → NumPy apstrādes ķēde

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. Bezgalvas tiešās aparatūras (bez aizmugures) ierakstīšanas skripts

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Funkciju pārbaude pirms 4 kameru masīva pieslēgšanas

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Ierakstīšanas receptes ekvivalents (tīrs Python)

CLI receptes DSL ir tiešs Python ekvivalents:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## Backend automātiskā palaišana

Viedās savienošanas ieejas punkti — `connect_camera`, `connect_array`, `connect_daq_sensor` un `discover_lattice_cameras` — ir vieglie HTTP klienti, kas pieņem, ka aizmugurējā sistēma klausās uz `127.0.0.1:5000` (viedā savienojuma saskarnes noklusējuma URL). Ja GUI vai CLI jau darbojas, viens no tiem darbojas. Ja tiek palaists tikai skripts, tas var nebūt — tādēļ šīs funkcijas **automātiski palaista komplektā iekļauto aizmugurējā procesa bināro failu** (bez loga, tāpat kā to dara `ChlorosLocal`) pirms to pirmā izsaukuma, pēc tam gaida līdz `backend_startup_timeout`, kamēr tas sāk darboties.

Noteikumi:

- **Tiek palaists tikai lokāls URL.** `backend_url`, kas norāda uz `localhost` / `127.0.0.1` / `[::1]` ir atbilstošs; jebkurš cits hosts tiek uzskatīts par cita lietotāja datoru un nekad netiek izveidots.
- **Aizmugurējā sistēma paliek darbojošā stāvoklī, lai to varētu atkārtoti izmantot** (tāpat kā CLI) — skripta izbeigšanās brīdī netiek veikta automātiska aizvēršana. Skripta atkārtota izpilde izmanto jau darbojošos aizmugurējo procesu.
- **Atteikties, izmantojot `auto_start_backend=False`** jebkurā no šiem izsaukumiem (piemēram, ja esat norādījis uz attālo backend vai pats pārvaldāt backend dzīves ciklu).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Ja komplektā iekļauto bināro failu nevar atrast vai palaist, nākamais HTTP izsaukums izraisa rīcībai piemērotu, **platformai pielāgotu** `ChlorosConnectError`, nevis vienkāršu paziņojumu par atteiktu savienojumu — Windows gadījumā tas norāda uz galda lietotni vai `chloros-cli` komandu; Linux (bez grafiskās saskarnes) tas norāda uz `chloros-cli` komandu vai `.deb`.

---

## Vide un galvenes

SDK katru aizmugurējās sistēmas HTTP izsaukumu atzīmē ar `X-Chloros-Client: sdk`. Aizmugurējā sistēma piemēro SDK/CLI licencēšanas noteikumus (nepieciešama pieteikšanās **un** maksas Chloros+ plāns) , nevis GUI bezmaksas līmeņa ceļu. Tas tiek iestatīts automātiski importēšanas brīdī — jums nekas nav jādara.

`http://localhost` un `http://127.0.0.1` tiek atpazīti kā vietējais backend. Aicinājumi uz citiem serveriem (piem., jūsu paša analītikas pakalpojums) netiek mainīti.

Aizstājiet aizmuguri URL, norādot `backend_url=` (vai `api_url=` uz `ChlorosLocal`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(`backend_url`, kas nav loopback, sasniedz tikai avota/ierīces backend — piegādātie backendi piesaistās tikai loopback; skatiet sadaļu „Attālinātā backenda režīms”, lai uzzinātu par tuneļa modeli.)

---

## Versiju pārvaldība un savietojamība

- SDK versija tiek eksponēta kā `chloros_sdk.__version__`.
- SDK pinu darbība ir atkarīga no komplektā iekļautās backend versijas. Vecākas versijas SDK apvienošana ar jaunāku backend parasti darbojas (uz priekšu saderīgi galapunkti), bet jaunākas SDK versijas apvienošana ar vecāku backend var izraisīt `404` kļūdas jaunajos galapunktos — atjauniniet darbvirsmas lietotni, lai tā atbilstu.
- Smart-connect saskarne (`connect_camera` / `connect_array` / `connect_daq_sensor`) un tīkla analīzes galapunkts atgriež stabilas JSON shēmas; jaunie lauki tiek pievienoti.

---

## Problēmu novēršanas norādes

- **`ChlorosAuthenticationError: Login required`** → Vienreiz palaidiet `chloros-cli login EMAIL PASSWORD` šajā datorā, vai pieteikties, izmantojot Chloros darbvirsmas lietotni.
- **`ChlorosConnectError: No Chloros backend is running …`** → „Smart-connect” automātiski palaista vietējo aizmugurējo sistēmu, tādēļ šis ziņojums parādās tikai tad, ja komplektā iekļautais binārais fails nav atrodams vai nevar tikt palaists (piemēram, ja datorā ir tikai pip, bet nav darbvirsmas pakotnes). Ziņojums ir atkarīgs no platformas: uz Windows atveriet darbvirsmas lietotni vai izpildiet jebkuru `chloros-cli` komandu; uz Linux izpildiet `chloros-cli` komandu (nav GUI) vai instalējiet `.deb`. Attālinātajam backendam nododiet `backend_url=` (un `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** importēšanas laikā → `lattice_sdk` neizdevās ielādēt (parasti nav instalētas Arena SDK darbības laika DLL). Virsma bez kameras joprojām darbojas.
- **Masīva savienojums atgriež zemāku izšķirtspēju nekā nativā**→ Aizmugures sistēmas “smart-prep” funkcija automātiski samazina kadra izmēru, lai tas iederētos vadā. Izmantojiet `analyze_array_network()`, lai noskaidrotu iemeslu, pēc tam vai nu uzlabojiet savienojumu, pieņemiet samazinājumu vai izmantojiet `force_tier="slip-emit-and-capture"` secīgai uzņemšanai. Samazinājuma drošības tīkls**ne** aptver kopējo pārslodzi (`oversubscribed: true`, fps lauki 0): pārāk liels kameru skaits vadam nevar tikt novērsts ar binningu/ROI — samaziniet kameru skaitu, aktivizējiet jumbo rāmjus vai pārejiet uz ātrāku tīkla karti (skatiet [Pārslogotība](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` ziņo, ka tīkla kartes (NIC) RX gredzens ir ļoti mazs (~0,26 MB) / savienojuma vārti ar brīdinājumu „FRAMES WILL DROP“** → Host NIC uztveršanas gredzens ir iestatīts uz noklusējuma vērtību (bieži tiek atiestatīts uz 32 pēc NIC draivera atjaunināšanas). Realtek USB 10GbE adapterī iestatiet `ReceiveBufferLen=256` un `PendingReceives=64` (paaugstināts), pēc tam pārstartējiet backend, lai tas atkārtoti nolasītu gredzenu. Pilnā procedūra: [CLI Atsauce → Galvenā NIC konfigurācija un optimizācija](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Hosts „iekārtojas” pārstartēšanas/izslēgšanas laikā, vēlāk rodas WMI `Invalid class` kļūdas / tīkla karte neaktivizējas** → Novecojis USB 10GbE draiveris, kas izraisa `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atjauniniet adaptera draiveri uz aktuālo versiju (≥ 2026) un atkārtoti piemērojiet uztveršanas gredzena iestatījumus. Skatiet [CLI Atsauce → Host NIC Setup &amp; Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Atstarošanas koeficients noraidīts** → Lai iegūtu absolūtās skalas atstarošanas koeficientu, kamerai (vai matricai) jābūt saistītai ar aktīvu DAQ. Veiciet saistīšanu, izmantojot grafisko lietotāja saskarni, vai izmantojiet `processing="radiance"` (W/m²/sr/nm), kam nav nepieciešams pāri savienots sensors.
- **`smart=True` uzņemšana ilgst ilgāk nekā gaidīts** → AE konverģence ir atkarīga no ainas dinamikas; ja vēlaties ātrāku (mazāk stabilu) trigeri, palieliniet `exposure_tolerance_pct` vai saīsiniet `stability_window_s`.

---

## Skatīt arī

- [CLI atsauces materiāls](cli-reference.md) — katra CLI apakškommanda atbilst SDK izsaukumam.
- [DAQ sensoru rokasgrāmata](../daq/README.md) — sensoru specifiskie vadu savienojumi, kalibrēšana un reģistrēšanas noteikumi.
- Tiešsaistes dokumentācija: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
