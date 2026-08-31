# Chloros Python SDK Atsauce

**Versija:**

1.2.0**Izveidots:**2026-07-29 19:19 ·**Pārskatīts:** 2026-08-30**Pakete:** `chloros-sdk` (PyPI)**Mērķauditorija:** Optimizēts LLM izmantošanai; cilvēkam lasāms.**Darbības joma:** Visas publiskās klases, funkcijas un palīgfunkcijas, ko piedāvā `import chloros_sdk`, ar piemēriem, kurus var kopēt un ielīmēt, aptverot attēlu apstrādi, vienas kameras vadību, sinhronizētus masīvus, DAQ sensorus un projekta automatizāciju.

Ja jums nepieciešama tikai galvenā informācija, pārejiet uz:
- [Instalācija un ātrs sākums](#installation)
- [Smart-Connect LATTICE kameru masīviem](#smart-connect-for-lattice-cameras)
- [DAQ sensoru sesijas](#daq-sensor-sessions)
- [Projekta automatizācija](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)

---

## Arhitektūra 60 sekundēs

SDKs ir plāns „Python” slānis virs „Chloros” aizmugurējās daļas (tas pats Flask serveris, ko izmanto gan darbvirsmas GUI, gan CLI). Automatizācijai importējiet `chloros_sdk` un izsauciet augsta līmeņa metodes; aizkulisēs katrs izsaukums kļūst par HTTP pieprasījumu vietējam backendam 5000. portā — `http://127.0.0.1:5000/api/...` (apzinātiiberāti nevis `localhost`, kas vispirms tiek pārveidots par `::1` vietnē Windows un izmaksā apmēram 2 s uz vienu pieprasījumu, izmantojot tikai IPv4 atbalsta sistēmu). Atbalsta sistēma pārvalda aparatūras resursu kopumu — kameras, DAQ sensorus, izlīdzināšanas profilus, kadru buferi — tādējādi skripti SDK var darboties vienlaikus ar grafisko lietotāja saskarni, nekonkurējot par seriālajiem portiem vai tīkla kartes joslas platumu.

Jūs izmantosiet trīs saskarnes:

1. **`ChlorosLocal` + brīvās funkcijas** (`process_folder`, `process_lattice_capture`) — attēlu apstrādes cauruļvads. Ar vienu „Python” izsaukumu apstrādājiet visu mapi, veicot kalibrēšanu, debayeringu un indeksu eksportu.
2. **„Smart-connect” rokturi** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Atver pastāvīgu aizmugures sesiju reāllaika aparatūrai. Tāds pats „smart-prep” process kā GUI: tīkla pārbaude, automātiska līmeņa izvēle, PTP, AE sākotnējā iestatīšana, GPIO trigera konfigurācija.
3. **`ChlorosProject` / `open_project`** — Ielādē saglabātu projektu (mapes ar `cameras.json` + `sensors.json` + `project.json`), vienlaikus savienot visu un veikt datu uztveršanu, izmantojot nosauktus rokturus.

Virsmas 1 un 2 **automātiski palaist vietējo backend**, ja tas vēl nav klausīšanās režīmā (tā pati komplektā iekļautā binārā programma, ko palaista GUI/CLI) — tādējādi skripts darbojas no jaunas komandu rindas, bez nepieciešamības vispirms palaist backend. Lai atteiktos, nododiet `auto_start_backend=False` (piem., norādot uz attālo backend, kas nekad netiek palaists). Skatīt [Aizmugurējās sistēmas automātiska palaišana](#backend-auto-start). Surface 3 darbojas citādi: `open_project()` nepieņem `auto_start_backend` parametru, un `connect_all()` nekad nepalaiž backendu — tas vienreiz pārbauda `http://127.0.0.1:5000` un, ja nekas neatbild, klusi pārslēdzas uz tiešu (bez backenda) `lattice_sdk` ierīces vadību. Tikai `proj.process()` un `stream(..., overlays=True)` lēni izveido `ChlorosLocal()` (kas veic automātisku palaišanu).

Visi trīs ir auth-gated: vienreiz palaidiet `chloros-cli login` uz datora vai piesakieties, izmantojot darbvirsmas GUI. Izsaukumi SDK bez derīgas sesijas izraisa `ChlorosAuthenticationError`.

Prasības:
- Python 3.7+ (kā norādīts pakotnē; izstrādāts/testēts ar 3.10)
- Vietējā datorā instalēta „Chloros” darbvirsma (aizmugures binārā programma ir iekļauta instalētājā)
- Aktīva pieteikšanās vietnē Chloros+. Minimālais līmenis SDK / CLI ir **Copper**vai augstāks (Copper / Bronze / Silver / Gold); bezmaksas**Iron**līmenim nav piekļuves SDK / CLI. Tas tiek piemērots**servera pusē**: katram pieprasījumam ar atzīmi SDK / CLI ir jābūt gan aktīvai sesijai, gan apmaksātam plānam, pretējā gadījumā backend atgriež kļūdu `403` ar `error_code: PLAN_UPGRADE_REQUIRED` (kas parādās kā `ChlorosLicenseError`, ko ģenerē `ChlorosLocal`, un kā `ChlorosConnectError`, ko parāda palīgprogrammas `connect_*`). Izslēgts lietotājs saņem kļūdu `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — abi ir atšķirīgi, jo, atkārtoti palaistot `chloros-cli login`, tiek novērsta pirmā kļūda, bet otrā netiek novērsta.
- Lietošana bezsaistē tiek atbalstīta plāna papildlaika periodā: pakete tiek nolasīta no servera validācijas keša (5 min) vai parakstītās, ar ierīci saistītās licences keša (30 dienas mēneša plāniem, līdz abonementa termiņa beigām gada plāniem). Kad šis papildlaiks beidzas, plāns pāriet uz bezmaksas līmeni un piekļuve SDK / CLI tiek pārtraukta, līdz datora var vismaz reizi savienoties ar serveri. `chloros-cli status` (`GET /api/license-status`) paliek pieejams bezmaksas līmenī, tāpēc iemesls ir skaidrs — tas ir vienīgais SDK / CLI maršruts, kas ir atbrīvots no līmeņa ierobežojumiem.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS vai jaunāka versija**, vai Jetson (JetPack 6). Ubuntu 20.04**netiek** atbalstīta: `.deb` atkarības ir atvasinātas no tā, ar ko saistās aizmugurējā sistēma, ieskaitot `libc6 (>= 2.34)`, un „focal” versijā tiek piegādāta glibc 2.31.

---

## Instalēšana

Python SDK ir plāns Python slānis virs Chloros backend. Lai izmantotu kaut ko vairāk nekā tikai dažas DAQ darbplūsmas, jums ir nepieciešams **vietēji instalēts Chloros darbvirsmas pakotne** (Windows instalētājs vai Linux `.deb`) — tas nodrošina backend bināro failu, Arena SDK izpildes vidi LATTICE kamerām un kalibrēšanas paketes.

Jaunākās lejupielādes: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### 1. solis — Instalējiet platformas paketi „Chloros”

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

### 2. solis — Instalējiet „Python” SDK

**„Chloros” instalētājs ietver atbilstošu „SDK” ratu.** Katra „Windows” instalētāja un „Linux” .deb pakete uz diska ievieto `chloros_sdk-X.Y.Z-py3-none-any.whl`, kas precīzi atbilst GUI / CLI / backend versijai. Jums nav jāseko līdzi PyPI, lai saglabātu sinhronizāciju.

#### Windows

Instalētājs automātiski palaista `pip install` pret komplektā iekļauto wheel failu, izmantojot jūsu sistēmas Python (vēlams `py.exe` palaidējs, ja tas nav pieejams, tiek izmantots `python -m pip`). Nav nepieciešama nekāda rīcība — `import chloros_sdk` darbojas jūsu Python vidē pēc veiksmīgas instalācijas. Ja datorā nav Python, instalētājs klusi izlaiž šo soli, un GUI + CLI turpina darboties.

#### Linux (.deb)

.deb fails novieto „wheel” failu `/usr/lib/chloros/sdk/`. `postinst` izdrukā precīzu komandu — PEP 668 distribūcijas pēc noklusējuma nepieļauj globālas pip rakstīšanas darbības, tādēļ mēs neveicam automātisku-instalējam:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Jetson ierīcēm ar izolētu tīklu šis process notiek pilnīgi bezsaistē — „wheel” jau atrodas diskā.

#### Publiskais PyPI

Tām sistēmām, kurās darbojas tikai pip (nav instalēta „Chloros” darbvirsmas pakete; darba plūsmas ar attālo aizmuguri vai tikai DAQ):

```bash
pip install chloros-sdk
```

PyPI tiek atjaunināts, izveidojot instalētāja versijas, tādējādi publicētais „wheel” atbilst jaunākajai stabilajai versijai. Izstrādes versijas (piem., `1.1.4.dev1`) tiek piegādātas tikai kopā ar instalētāja „wheel”.

#### Pārbaudiet

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Nepieciešama Chloros+ abonementa.** Visiem SDK izsaukumiem ir nepieciešama aktīva Chloros+ pieteikšanās. Izpildiet `chloros-cli login user@example.com 'YourPassword'` vienu reizi katrā datorā; pieteikšanās dati tiek saglabāti `~/.chloros/`.

### Vai man ir nepieciešama darbvirsmas pakete?

Lielākajai daļai darba plūsmu ar pip paketi vien **nepietiek**. Šeit ir norādīts, kas nepieciešams katrai SDK virsmai:

| SDK virsma | Vai nepieciešama darbvirsmas pakete? | Kāpēc |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Jā** | Automātiski palaista aizmugurējā sistēmas binārā programma vietnē `/usr/lib/chloros/chloros-backend` (Linux) vai `C:\Program Files\MAPIR\Chloros\…` (Windows). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Jā**(vietējais)**/ Nē**(attālināts) | Tīrie „HTTP” klienti caur aizmuguri. Vietējai aizmugurei → nepieciešama darbvirsmas pakete. Attālinātai aizmugurei → `backend_url=`**caur tuneli** (sk. Attālinātā aizmugurējā sistēma — piegādātās aizmugurējās sistēmas veido savienojumu tikai ar lokālo atgriezenisko saiti). |
| `ChlorosProject` / `open_project` | **Jā** | Vadīšana saglabātiem projektiem caur backend. |
| Tiešās LATTICE klases (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Jā** | Nepieciešama „Arena“ SDK nativā izpildes vide, kas ir iekļauta darbvirsmas paketē. `CAMERA_AVAILABLE` citādi importēšanas brīdī ir `False`. |
| Tiešās DAQ klases (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **Nē** | Tīra „Python” izmantošana, izmantojot pyserial/bleak/zeroconf. Vide, kurā darbojas tikai pip, var vadīt DAQ no sākuma līdz galam. |

### Attālinātā aizmugurējā daļa (tikai pip uzņēmējdators, izmantojot tuneli)

> **Piegādātā aizmugurējā daļa nav sasniedzama pa LAN.** Ražošanas
> versijas saista tikai loopback (abas loopback ģimenes) un kategoriski noraida
> vienīgo režīmu bez loopback (`CHLOROS_CLOUD_MODE`), tādēļ
> `backend_url="http://<lan-ip>:5000"` **nevar darboties ar instalētu
> Chloros** — šis risinājums vienmēr darbojās tikai ar source/dev
> backend. Lai vadītu backend citā datorā, pašam pāradresējiet tā loopback
> portu un norādiet SDK uz tuneli:

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

Bezmonitora / CI / robotikas serveri var vienu datoru ar pilnu darbvirsmas instalāciju paturēt kā „Chlorosa serveri”, bet visur citur izmantot `pip install chloros-sdk` — taču datu pārraide starp tiem notiek, izmantojot iepriekš minēto lietotāja izveidoto tuneli, nevis tiešu LAN URL.

> **Zināms ierobežojums — `ChlorosLocal` neatbalsta tikai pip.** `ChlorosLocal(backend_url=BACKEND)` pašlaik savā konstruktorā atrisina vietējo aizmugures bināro failu *pirms* URL pārbaudes un izraisa kļūdu `ChlorosBackendError` („Chloros aizmugure nav atrasta…”), ja nav instalēts neviens darbvirsmas pakotnes — pat ja ir pieejama attālā aizmugure. Tikai iepriekš minētā „smart-connect” saskarne (`connect_camera` / `connect_array` / `connect_daq_sensor`, kā arī `analyze_array_network` un `list_*` / `discover_*` palīgprogrammas) darbojas no datora, kurā ir instalēts tikai pip.

### Darba plūsma, kurā tiek izmantota tikai DAQ (tīmekļa serveris, kurā darbojas tikai „pip” pakete)

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

## Augstākā līmeņa „API” indekss

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

Galvenā apstrādes plūsmas klase. Pirmajā lietošanas reizē palaista aizmugurējo moduli, izveido un konfigurē projektus, uzrauga gaitu un atgriež kopsavilkumus pēc izpildes.

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
| `create_project(project_name, camera=None)` | Izveido jaunu projektu (pēc izvēles ar kameras veidni, piemēram, `"Survey3N_RGN"`). |
| `import_images(folder_path, recursive=False)` | Importē RAW/TIF/JPG/DNG attēlus **un `.daq` gaismas sensora ierakstus**. Atgriež `count` (attēlus) un `scan_count` (ierakstus). Brīdina tikai tad, ja mapē nav ne viena, ne otra. |
| `export_light_sensor(daq=True, csv=True)` | Ieraksta kalibrētos `.daq` + `.csv` par katru projekta gaismas sensora ierakstu, ierakstot tos failā `<project>/Light Sensor/`. Skatīt [Gaismas sensora ieraksti](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Iestatiet apstrādes parametrus. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Palaižiet apstrādes cauruļvadu. Atgriež `{"status": "complete", "async": False}`, kā arī `summary` atslēgu, ja aizmugurējā sistēma to nodrošina — skatīt [Kopsavilkums un padomi pēc izpildes](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | Pārbaudiet aizmugurējās sistēmas stāvokli. |
| `logout()` | Dzēsiet kešēto autentifikācijas datus. |
| `shutdown_backend()` | Pārtrauciet aizmugurējās sistēmas darbību (ja tā tika palaista ar komandu `SDK`). |
| `discover_cameras()` | Atrodiet LATTICE kameras **caur šīs instances aizmugurējo sistēmu** (`/api/camera/discover`). Atgriež vārdnīcu sarakstu (`serial`, `model`, `ip`, …) — tādā pašā formātā, kādu redz GUI/ CLI. Tukšs saraksts, ja nekas nav atrasts vai backend nav sasniedzams. |
| `camera_capture(output_dir, format="tiff", **settings)` | Uztver vienu kadru**caur backend**(automātiski palaists ar šo rokturi), lai tas tiktu sagatavots tāpat kā GUI/ CLI (noklusējumā 12 biti, atkārtota pūla izmantošana, iegultie kalibrēšanas metadati). Nosakiet mērķi ar `serial=` vai `device_index=`; nododiet `exposure`/`gain`/`pixel_format`/`preset` kā `**settings`. Atgriež vecā tipa metadatu vārdnīcu (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Izvada pārklājuma-komponēti priekšskatījuma kadri no apvienotās kameras — viegls MJPEG klients, izmantojot aizmugures sistēmas `/api/camera/<serial>/stream-annotated` maršrutu (zebra / režģis / krustlīnijas / histogramma / maksimuma rādītājs / servera pusē zīmēts punkts). `decode=True` nodrošina BGR masīvus; `False` nodrošina neapstrādātus „JPEG” baitus. Pieejams arī katram projektam atsevišķi kā `ChlorosProject.stream(overlays=True)`. |

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

DAQ-U / DAQ-M / DAQ-E datus var ierakstīt **bez** to kalibrēšanas paketes. Tieši to
publiskie [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
ierakstītāji (`record_daq.py`) dara pēc noklusējuma: tie ieraksta neapstrādātos sensoru skaitījumus un marķē
failu tā, lai Chloros iegūtu šī sensora rūpnīcas kalibrāciju **pēc sērijas numura** — vispirms no vietējās kešatmiņas,
pēc tam no MAPIR mākonis — un piemērotu to importēšanas brīdī.

Chloros rezultātu atkal izraksta kā divus produktus katram ierakstam, sadaļā
`<project>/Light Sensor/`:

| Produkts | Kas tas ir |
| --- | --- |
| `<name>_calibrated.daq` | Pārstrādājams arhīvs — tā pati shēma kā reāllaika ierakstam, tagad norādot kopumu, kas to radīja. To atkārtoti importējot, to ****ne** veic tā atkārtotu kalibrēšanu. |
| `<name>_calibrated.csv` | Spektrālā starojuma intensitāte W/m²/nm uz paša sensora viļņu garuma režģa, viena rinda katram mērījumam, kā arī fotometriskās kolonnas (kopējā jauda, fotopiskais/skotopiskais lukss, PPFD un tā sadalījums pa zilajām, zaļajām un sarkanajām krāsām, maksimālais viļņa garums). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Tikai sensori bez pakotnēm (DAQ-A).** Neapstrādāti spektrālie sensora skaitījumi — *nevis* starojuma intensitāte. Skatīt zemāk. |

`process()` veic šo eksportu kā vienu no saviem posmiem. Tam **nav** nepieciešami attēli:
atsevišķi lidojošs gaismas sensors ir pirmklasīga darba plūsma, un šādam projektam pēc būtības nav
attēlu.

**DAQ-A ieraksti tiek eksportēti kā neapstrādāti skaitļi.** DAQ-A sērija ir radusies pirms sērijas
pakotņu sistēmas ieviešanas un tai nav pakotnes, ko lejupielādēt — tā tiek kalibrēta laukā, izmantojot
reflektances mērķa, tāpēc tam nekad nav bijis nepieciešams komplekts. Šie ieraksti tiek eksportēti
ar kodu `_raw`, nevis `_calibrated`: ar atšķirīgu faila nosaukumu, nevis ar atzīmi
failā, jo norādei ir jāiztur nosūtīšana pa e-pastu kā vienkāršs nosaukums.
`.csv` galvenē ir norādīts `raw spectral sensor counts (NOT irradiance)` un brīdinājums, ka
vērtības ir salīdzināmas **viena faila ietvaros** — tieši tam, kādam nolūkam tās izmanto
kalibrēšana, balstoties uz mērķi — un nevis starp dažādiem sensoriem. No jaudas atkarīgās fotometriskās kolonnas (kopējā jauda,
fotopiskie/skotopiskie luks, PPFD) tiek atgriezti kā **NULL**, nevis integrēti no skaitījumiem.

DAQ-U / DAQ-M / DAQ-E, kura pakete vienkārši nevarēja tikt iegūta, joprojām tiek **izlaista**,
nevis ierakstīta neapstrādātā veidā: tur pakete pastāv, un „atjaunot savienojumu un pārstrādāt” ir reāls padoms.

Vecākas **v1.01 / v1.02** ierakstiem (tos raksta DAQ-A-SD) nav atsevišķas epohas katram nolasījumam,
tikai faila ierakstīšanas laiku. Attēla↔lejupvērstās plūsmas saskaņotājs tos joprojām noraida —
kadra saskaņošana ar ierakstīšanas laiku būtu nepamanāmi kļūdaina —, bet eksportētājs tos nolasa, un
CSV izdrukā `clock=daq_created_on`, tādējādi produkts norāda, uz kāda pulksteņa tas balstās.

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

Ieraksts, kura kalibrēšanas pakete nevar tikt iegūta (bezsaistē vai sensors bez
kalibrēšanas failā), tiek ziņots ar kodu `skipped` **norādot iemeslu**. Tas nekad
netiek saglabāts kā „kalibrēts” fails, kas satur neapstrādātus skaitījumus — izveidojiet savienojumu ar internetu un
izpildiet atkārtoti, un eksportēšana tiks pabeigta.

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

Pēc pabeigšanas `process()` lejupielādē `GET /api/processing-summary` un pievieno galveno daļu kā `result["summary"]`. Lejupielāde notiek pēc iespējas, un tā nekad neblokē veiksmīgu atgriešanos — ja kopsavilkums nav pieejams, `process()` pārslēdzas uz vienkāršo `{"status": "complete", "async": False}` formātu. Katrs ieraksts `summary["hints"]` — pilni teikumi ar ieteikto labojumu, piemēram, kāpēc izpilde nedeva nekādu rezultātu — tiek atkārtoti nosūtīts kā Python `UserWarning`, tādējādi izpildes bez rezultāta ir pašdiagnosticējošas, pat ja jūs nekad nepārbaudāt vārdnīcu:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` ir mašīnlasāmā daļa:

| Atslēga | Ko tā skaita |
| --- | --- |
| `models` | Kameru grupas darbā. |
| `images_in_groups` | Avota attēli šajās grupās. |
| `targets_found` | Atklātie atstarošanas mērķi. |
| `images_calibrated` | Attēli, ar kuriem tika veikta kalibrēšana. |
| `exported_files` | **Attēlu rezultātu faili, ko izveidoja izpildes cikls.** |
| `daq_recordings_exported` / `daq_recordings_skipped` | Gaismas sensoru ieraksti, kas apzināti uzskaitīti atsevišķi — tie ir iegūti citā posmā un pastāv arī izpildījumos, kuros nav nekādu attēlu, tāpēc to iekļaušana liktu izpildījumam, kurā tika veikta tikai datu ieguve, izskatīties tā, it kā ka tajā būtu eksportēti attēli. |

Papildus tiem: `summary["output_dirs"]` (katrs katalogs, kurā veikta ierakstīšana),
`summary["light_sensor_export"]`, `summary["stopped"]` (ir patiesība, ja lietotājs pārtrauca
izpildi, tādējādi daļējie skaitļi netiek interpretēti kā pabeigta izpilde ar nepietiekamu rezultātu), un
`summary["groups"]` (sadale pa grupām).

`exported_files` tiek reģistrēts cauruļvadā **rakstīšanas brīdī**, nevis pēc tam izlasīts no
projekta attēlu objektiem. Paralēlās un GPU stratēģijas veido savus attēlu
objektus (GPU ceļu gadījumā — darba apakšprocesos), tāpēc vecā skenēšana ziņoja
`0 file(s) written` par katru šādu izpildes ciklu un pēc tam izvadīja norādi par nulles eksportiem — izpildes ciklos,
kuros viss bija noritējis veiksmīgi. Ja izveidojat skriptu, balstoties uz šo skaitli, tagad veiksmīgs paralēlais izpildes cikls
ziņo par skaitli, kas nav nulle.

Light-sensor izlaišanas ziņojumos tiek norādīts iemesls, ko lasītājs faktiski konstatējis katram failam —
nelasāma shēma, trūkstošs kopums, rakstīšanas kļūda — **deduplicēts**, tādējādi divdesmit faili,
, kas izlaisti viena iemesla dēļ, tiek uzskatīti par vienu iemeslu, nevis divdesmit tā atkārtojumiem.

> **`process()` netiek izraisīts, ja izpildes reizē netiek radīti attēli.** Šī ir vienīgā vieta, kurā „SDK” un
> „CLI” apzināti atšķiras: `chloros-cli process` uzskata „tika pieprasīti produkti, bet neviens netika
> uzrakstīts” par kļūdu un beidz darbu ar nenulles rezultātu, savukārt „SDK” atgriežas normāli un ziņo par
> stāvokli, izmantojot `summary` / hints. Ja jūsu procesa ķēdei vajadzētu apstāties tukšā cikla gadījumā, pārbaudiet to
> paši — pārbaudiet `summary` (vai saskaitiet failus projekta mapē), nevis paļaujieties uz
> izņēmuma neesamību. Parasti iemesli ir ievades mape, kas netika atpazīta kā
> uzņemšanas mape, un produkti, kas tika izlaisti kā nepiemēroti esošajām kamerām (piemēram, starojums no kamerām, kas darbojas tikai
> RGB režīmā).

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

#### Radiometriskā izeja (LATTICE multispektrālā apstrādes ķēde)

`process` apstrādes ķēdes LATTICE multispektrālā (M3C/M3M) eksporta līmenis — `reflectance` (noklusējums), `radiance`, `sensor-response`, vai `all` (katrs attēlam piemērojamais režīms) — atbilst projekta **&quot;Radiometriskā izvade&quot;** apstrādes iestatījumam. `configure()` tam ir atsevišķs atslēgvārds:

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

Papildu izejas iespēja — projekta `"Radiometric output"` atslēgas rakstīšana caur `custom_settings` — joprojām darbojas, bet atcerieties, ka tā aizstāj visu iestatījumu bloku (skatiet brīdinājumu zemāk):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (noklusējuma iestatījums) sadala kameras starojuma intensitāti ar **laika zīmogam atbilstošo DAQ lejupvērsto plūsmu**, kas automātiski tiek noteikts no ierakstītā `.daq` (DAQ-U/M/E)**vai DAQ-M nativā `.csv`**, kas atrodas kopā ar attēliem; jebkurš kameras vai DAQ kalibrēšanas komplekts, kas lokāli trūkst, tiek**automātiski lejupielādēts no AWS** pirmajā lietošanas reizē. „CLI” to parāda kā katra veida produkta slēdžus `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **aizstāj** visu aprēķināto iestatījumu bloku (tas pēc dizaina apiet `configure()` pārējās atslēgvārdus un validāciju). Kad to izmantojat, iekļaujiet katru `Project Settings` atslēgvārdu, kas jums ir svarīgs, kā redzams iepriekšējā piemērā.

---

## „Smart-Connect” LATTICE kamerām

Pastāvīgas aizmugures sesijas reāllaika aparatūrai. Tiek izmantoti tie paši galapunkti, ko izmanto grafiskā lietotāja saskarne (GUI), tādējādi darbība ir identiskSDK, CLI un GUI.

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
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | Uztver **vienu** kadru. Atgriež vienelementu sarakstu ar kadru metadatu vārdnīcām. (Sērijveida/vairāku kadru uzņemšana tika noņemta — ja nepieciešama sērija, izsauciet `capture()` ciklā.) |
| `disconnect()` | Atbrīvo no pūla. Neveic nekādu darbību, ja esam pievienojušies jau atvērtai sesijai. |

`capture()` eksporta kontroles (tāds pats modelis kā masīvam + GUI):

- `processing` / `levels` — `processing="all"` saglabā visus piemērojamos eksporta veidus; `levels=["raw","radiance"]` saglabā tikai tos (pārraksta `processing`). Lai izmantotu aizmugurējās sistēmas noklusējumu, izlaidiet abus.
- `force_daq=True` — saglabā piešķirto DAQ/DLS rādījumu kā `.daq` papildu failu pat tad, ja tiek veikta tikai neapstrādātu datu ieguve, lai kadru vēlāk varētu pārstrādāt atstarojumā/indeksā. Ja nav piesaistīts neviens DAQ, šī darbība netiek veikta.

### Sinhronizētais masīvs — `ArraySession` (Smart-Prep)

`connect_array` ir **ieteicamais sākuma punkts** daudzkameru konfigurācijām. Tas fonā izpilda pilnu GUI „smart-prep” darbību secību:

1. **Tīkla analīze** (`/api/camera/array/recommend`) — nosaka lielāko kadra izmēru, kas atbilst sim-emit līmenim, nezaudējot kadrus.
2. **Līmeņa automātiskā izvēle** — `sim-capture-sim-emit`, ja vads to spēj apstrādāt; pretējā gadījumā `sim-capture-ftd-stagger` vai `slip-emit-and-capture`.
3. **Automātiska samazināšana**— klusi samazina rāmja izmēru / palielina grupēšanu, ja vads nespēj uzturēt pieprasīto izšķirtspēju.**Šis drošības tīkls neattiecas uz kopējo pārsubskripciju**: ja vadu pārslodze ir pārāk liela, to nevar novērst, samazinot kadru izmēru — skatīt [Pārslodze](#over-subscription-the-per-cam-floor).
4. **PTP ir ieslēgts**pēc noklusējuma — dažādu kameru laika zīmogi tiek sinhronizēti ar vienu kopīgu pulksteni ar precizitāti**~1 ms**. Vienlaicīga ekspozīcija tiek nodrošināta ar M8 aparatūras trigeri (**&lt; 100 µs** starp moduļiem), nevis ar PTP: PTP sinhronizē *laika zīmogus*, nevis ekspozīcijas.
5. **Automātiska pikseļu formāta izvēle katrai kamerai** — RGB kameras → `BayerRG8`, multispektrālās → `BayerRG12`.
6. **AE sākotnējā iestatīšana** — tiek saglabāts katras kameras pašreizējais AE stāvoklis, lai savienojums neatsāktu ekspozīciju no jauna darbības laikā.
7. **GPIO trigera konfigurācija** — `connect_array` aktivizē visas kameras (`TriggerMode=On`, `TriggerSource=Line2`), lai galvenās kameras impulss vadītu pakļautās kameras pa M8 kabeli. Šis solis ir paredzēts tikai masīvam: ja tiek atvērta viena kamera ar `LatticeCamera`,darbojas.

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
- `"sim-capture-ftd-stagger"` — elastīga laika domēna nobīde (kameras raidīšanas brīži ir nedaudz nobīdīti, tādējādi paketes tiek sērijveidā pārraidītas pa vadu).
- `"slip-emit-and-capture"` — secīga uztveršana katram kameras modulim atsevišķi (bez laika sinhronizācijas; vienīgā iespēja, ja neviens rāmja izmērs neatbilst vienlaicīgajai darbībai).

`wire_ceiling_mbps` pārraksta **uzņēmējdatora ilgstošo vadu joslas platumu** MB/s — šis viens
skaitlis, no kura atkarīgs visa masīva resursu sadalījums. Atstājiet to `None`, lai izmantotu automātiski noteikto
vērtību. Samaziniet to, ja masīvs ziņo par GVSP bojātiem rāmjiem: automātiski noteiktā vērtība tiek aprēķināta
pēc tīkla kartes paziņotā savienojuma ātruma, kas pārvērtē USB adapterus, vājos PCIe kanālus un
noslogotās koplietošanas struktūras — un šī pārvērtēšana izpaužas kā bojāti kadri, nevis kā
redzami lēns savienojums. Vērtība tiek saglabāta projekta masīva ierakstīšanas blokā, tādēļ,
atverot to no jauna vai vēlāk izmantojot `connect_array`, tā tiek atjaunota tāpat kā jebkurš cits masīva iestatījums.
Skatīt [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames).

#### Pārslogošana (minimālais limits katrai kamerai)

Sim-emit pacing katrai kamerai piešķir daļu no sadursmju drošā vadu budžeta, kura minimālais limits ir **8 MB/s katrai kamerai**(`per_cam_floor_bps`). Tiklīdz `N × floor` pārsniedz sadursmju drošo maksimālo robežu, masīvs**pārsniedz vadu kapacitāti**— kļūmes režīms ir GVSP paketes zudums, nevis zemāks kadru ātrums — un nav iespējams to novērst, mainot kadra izmēru:**kopējā pārbaude salīdzina kadrā apvienotos un ROI zemākos baitus, nevis regulētos baitus sekundē**. Praktiskie maksimālie ierobežojumi pilnā izšķirtspējā uz 1 GbE resursdatora:**6 kameras ar 1500 MTU, 9 ar jumbo kadriem** (`max_cams_collision_safe` analīzes atbildē norāda jūsu vadu maksimālo robežu). Risinājumi: mazāk kameru, jumbo rāmji no gala līdz galam, vai ātrāks tīkla interfeiss.

- Atbildēs `analyze_array_network()` un `/api/camera/array/connect` ir iekļautas atbildes `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` un `per_cam_floor_bps`. Ja `oversubscribed` ir patiesa vērtība, prognoze **nullej fps laukus** (`achievable_fps_max` / `fps_bright` / `fps_dark`), nevis ziņo par maldinošu lēnu, bet tomēr funkcionējošu ātrumu.
- `POST /api/camera/array/connect` pieņem `pin_resolution` ķermeņa parametru (**tikai HTTP — nav SDK kwarg**; `connect_array` to neizpauž). Fiksēšana noņem binninga pakāpeniskās samazināšanas drošības tīklu, tādēļ pārslodzēts savienojums ar iestatītu `pin_resolution` tiek**kategoriski noraidīts**, norādot kļūdu, kurā minēti visi risinājumi. Bez fiksēšanas savienošana turpinās ar pakāpenisku samazināšanu, bet brīdina, ka samazināšana nevar iztīrīt kopējo apjomu.
- Testēšanasizkļūšanas iespēja: iestatiet `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` aizmugures vides iestatījumos, lai atteikumu pazeminātu līdz skaļam brīdinājumam — jūs tomēr izveidojat savienojumu un pieņemat paketes zudumu.

#### Masīva stāvoklis — kura apakšsistēma zaudē kadrus

`GET /api/camera/array/<array_id>/capability` rāda aktīvu `health` bloku
savienotajā masīvā, kas tiek pārvērtēts **10 sekunžu** ilgā logā. Tas sadala rāmju zudumu
divos cēloņos, kuriem nepieciešami pretēji risinājumi, nevis kā vienu „nepilnīgu” rādītāju, kas
nenorāda ne vienu, ne otru:

| Lauks | Kas tas nozīmē | Kura apakšsistēma |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (uz katru seriālo portu) | Kadrs **ieradās, bet bija strukturāli bojāts**— GVSP paketes zudums. |**Tīkls**: vadu jauda, sinhronizācija, NIC RX gredzens, MTU |
| `never_arrived_rate_pct` (uz katru seriālo portu) | Kadrs **vispār neieradās**— kameraneizšāva vai no tās nekas neizgāja. |**Trigers / sinhronizācija**: M8 kabelis, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Sliktākais kameras pārraides ātrums katrā gadījumā. | — |
| `per_cam_rate_pct` | Kopējais nepilnīguma rādītājs katrai kamerai (abi iemesli kopā). | — |
| `stable_for_seconds` | Cik ilgi katra kamera ir palikusi zem 0,01 %. | — |

Līdzās `health` šajā pašā ierakstā ir norādīts arī skaitlis, no kā atkarīgs kopējais piešķīrums:

| Lauks | Ko tas nozīmē |
| --- | --- |
| `wire_ceiling_mbps` | Hostamspēkā esošais ilgtermiņa vadu budžets, MB/s. |
| `wire_ceiling_source` | No kurienes šis skaitlis ir iegūts, vārdos — piemēram, `USB-capped 200 MB/s (was theoretical 1062; …)` vai `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, kad `wire_ceiling_mbps=` to iestatīja. |
| `nic_is_usb` | `true` USB Ethernet adapterim. |

Šim galapunktam nav SDK ietvara — nolasiet to tieši:

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

**Nolasīšana:** `gvsp_corrupt_rate_pct`, kas nav nulle, ar `never_arrived_rate_pct` vērtību 0 nozīmē, ka
izraisīšana un kabeļa sinhronizācija ir ideāla un 100 % zudumu rada tīkla ceļš — samaziniet
`wire_ceiling_mbps` un atkārtoti izveidojiet savienojumu. Pretējais modelis norāda uz sinhronizācijas kabeli vai
izraisītāja līniju.

> **`target_fps` nav rādītājs bojātiem rāmjiem.** GevSCPD ritms tiek iestatīts vienreiz
> savienojuma izveides brīdī, tādēļ izraisītāja ātruma samazināšana maina darba ciklu, nevis
> vienlaicīgoizstarošanas pārraides ātrumu. Izmērītais 5× pieprasījuma samazinājums nedeva uzlabojumus, savukārt
> vadu maksimālā ātruma samazināšana no 240 līdz 200 MB/s samazināja bojāto rāmju īpatsvaru tajā pašā iekārtā no 10,4 % līdz
> 0,00 %.

> **TRI032S programmaparatūras versijā nav pieejama automātiskā samazināšana datu plūsmas vidū.** Darbojošs masīvs nevar
> to izlabot pats; atvienojiet un atkārtoti pievienojiet, lai savienošanas laika izvēlne pārplānotu atbilstoši
> jaunajam maksimālajam ātrumam.

**USB Ethernet adapterim zonde nosaka 200 MB/s ierobežojumu** neatkarīgi no tā
nosaukuma: efektivitātes tabula, kas pārvērš savienojuma ātrumu ilgtspējīgā rādītājā, ir
balstīta uz PCIe, un USB tīkla karte paziņo savu Ethernet savienojuma ātrumu, vienlaikus esot ierobežota ar
USB šķiedru un tās draiveri. Ierobežojums ir absolūts, nevis relatīvs — USB 1 GbE adapteris
sasniedz ~80 MB/un tas netiek ietekmēts.

#### `ArraySession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=10.0)` | Live `{fps, ptp, frame_count, last_error, …}`. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | Viena sinhronizēta uzņemšanas grupa. Atgriež `CaptureResult` (kadru vārdnīcu saraksts + `.skipped`). Eksportēšanas kontrole ir aprakstīta zemāk. |
| `capture(..., smart=True)` | **Vieds uzņemšanas režīms** — gaida, kamēr AE stabilizējas visās kamerās, un tad sāk uzņemšanu. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | Ātrākā uzņemšana: tikai neapstrādāti dati + piešķirtais DAQ rādījums (+ brīvais kombinētais indekss). Atbilst GUI poga „Ātrākā uzņemšana”. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Vienreizēja / Nepārtraukta / Intervāla uzņemšana vienā ierobežotā ciklā. Atgriež `list[CaptureResult]`.**Nepieciešams `count` un/vai `duration_s`**, lai to pārtrauktu (SDKā nav Ctrl+C). |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | Sāk ierakstīt kombinētā indeksa skatu reāllaikā video/GIF formātā → `RecorderHandle`. Viens kompozīcijas ierakstītājs uz vienu masīvu. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | Sākt augstas kadru frekvences neapstrādātu Bayer sērijas uzņemšanu → `RecorderHandle`. Veiciet atkārtotu apstrādi bezsaistē ar `build_video()`. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Veiciet saglabātās neapstrādātās sērijas atkārtotu apstrādi bezsaistē, lai iegūtu kalibrētu(-us) video. Bloķējas, līdz process ir pabeigts (`wait=True`) un atgriež `{outputs, errors, combined}`. |
| `build_video_status(job_id, timeout=15.0)` | Pārbauda bezsaistes izveides uzdevumu: `{running, result, error, burst_dir}`. |
| `disconnect()` | Atbrīvo visu masīvu. |

`capture()` eksporta kontrole (tāds pats galapunkts, kādu izmanto GUI/CLI):

- `processing` / `levels` — `processing="all"` (vai `levels=["raw","radiance",…]`) saglabā katru piemērojamo eksporta tipu katrai kamerai; viena `processing` vērtība saglabā tikai šo līmeni.
- `aligned=True` — deformē katra elementa ne-neapstrādāto eksportu atbilstoši masīva [izlīdzināšanas profilu](#array-alignment) (kopīgi reģistrēts); neapstrādātie dati paliek neizlīdzināti, bet transformācija tiek iekļauta metadatos. Ja masīvam nav profila, tiek izmantota neizlīdzināta (ar brīdinājumu, kas parādās rezultāta `alignment`), ja masīvam nav profila.
- `render_index=False` — izlaiž katras kameras veģetācijas indeksa pārklājumu; pēc noklusējuma to renderē tur, kur tas ir konfigurēts.
- `force_daq=True` — saglabā piešķirto DAQ/DLS rādījumu kā `.daq` papildu failu, pat ja nevienam izvēlētajam līmenim tas nav nepieciešams.

**„TIFF” kompresija (tikai HTTP):**`ArraySession.capture()` nenosūta `compression` atslēgu, tādēļ tiek piemērots aizmugurējās sistēmas noklusējums — `POST /api/camera/array/capture` nolasa `compression` ķermeņa parametru, `"deflate"` pēc noklusējuma (bezzudumu zlib L1 + horizontālais prognozētājs, ~4,1 MB uz vienu pilnas izšķirtspējas kadru). `"none"` raksta nesaspiestu (~6,3 MB/kadrs) ar**~5× ātrāku ierakstīšanu** — abi formāti ir bezzuduma un importēšanas laikā tiek nolasīti identiski. Funkcijā `SDK` tam nav pieejams nekāds argumentu pāris; izejas ceļš ir `chloros-cli lattice array-capture --compression none` vai neapstrādāts `HTTP`. DEFLATE arī aiztur „Python” GIL, tādēļ saspiestā rakstīšana netiek paralizēta starp katras kameras rakstīšanas pavedieniem — ilgstošai 8 kameru pilnā-res uzņemšanai ar sensora ātrumu ir nepieciešams `compression: "none"`. Sīkāka informācija: [CLI Atsauce → array-capture](cli-reference.md).**Eksporta pārrakstīšana katram elementam (tikai HTTP):**tas pats galapunkts pieņem arī `exclude_serials` (saraksts — izslēdz elementus no saglabātās kopas; masīvs joprojām darbojas kā viena sinhronizēta grupa, un izslēgtie elementi tiek atgriezti `excluded`), `serial_levels` (`{serial: [level tokens]}` pārrakstījumi katrai kamerai atsevišķi) un `serial_index` (`{serial: bool}` indeksa pārklājuma pārrakstīšana katrai kamerai). Tie ir GUI-paritātes ķermeņa parametri un**vēl nav SDK kwargs**; elementi, kas nav iekļauti kartēs, tiek aizstāti ar masīva līmeņa `levels` / `render_index`.

##### Izlaisto kameru pārbaude — `CaptureResult.skipped`

`ArraySession.capture()` atgriež `CaptureResult`, kas ir `list` apakšklase: atkārtojiet to, indeksējiet to, veikt ar to „`len()`” — visi esošie modeļi turpina darboties. Jaunais kods var pārbaudīt `.skipped` atribūtu, lai redzētu, kuras kameras tika izslēgtas un kāpēc. Visbiežāk sastopamais gadījums ir „RGB” kameras jaukta filtra masīvā, ja tiek pieprasīts `processing="radiance"` vai `"reflectance"` — starojums uz vienu Bayer pikseli platjoslas sensoram nav nozīmīgs, tāpēc aizmugurējā sistēma izlaiž šos kameru elementus, nevis ģenerē bezjēdzīgus rezultātus.

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

Iemesla simboli atbilst šādam paraugam: `<level>-not-applicable-to-rgb-cam` (viens ieraksts par katru izlaisto līmeni, katrā norādot `level`). Izlaišanas iemesli, kas saistīti ar atstarošanas koeficientu, ir `reflectance-skipped-no-fresh-dls` (nav pieejams jauns lejupvērstais rādījums), `reflectance-skipped-bound-daq-unavailable (…)` (neizdevās sasniegt piesaistīto DAQ), un `dls-uncalibrated-band-<nm>` — josla galvenokārt atrodas ārpus DAQ gaismas sensora radiometriski kalibrētā diapazona (~374–974 nm), tādēļ tiek noraidīta absolūtā, uz DAQ balstītā atstarošanas sadale, un kadrs tiek pārveidots, balstoties uz sensora reakciju. No piegādātajiem SKU to izraisa tikai F988; šīs kameras atbalstītais darbības veids ir darba plūsma ar atstarošanas paneli.

`processing` līmeņi:

| Līmenis | Izeja |
| --- | --- |
| `"raw"` | Vienkanāla Bayer (mono kameras: viena josla) tieši no sensora. |
| `"debayered"` *(SDK noklusējums)* | 3 kanālu BGR, izmantojot bilineāro demosaiku (mono kameras: 1 kanāla pelēktoņu skala). |
| `"radiance"` | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi. Tikai multispektrālais režīms — RGB kameras tiek izlaistas. |
| `"reflectance"` | uint16 0..32768 (Pix4D-ready); absolūtas atsauces nodrošināšanai nepieciešama tiešsaistes DAQ savienošana. Tikai multispektrālais režīms. |
| `"display"` | Pilnā ķēde, kas atbilst GUI priekšskatījumam (CCM + WB + gamma saskaņā ar kameras profilu). |
| `"all"` | **Viens fails katram piemērojamajam līmenim** katrai kamerai (atbilstoši GUI iestatījumam „Capture All” / CLI noklusējumam). Atgrieztais `CaptureResult` tad satur vienu kadra diktu katram `(cam, level)`, kur katrā vārdnīcā ir norādīts līmenis; nepiemērojamie līmeņi parādās `.skipped`. Jebkuram atstarošanas kadram izmantotais DAQ rādījums tiek saglabāts kā `.daq` papildu fails. |

> **Piezīme — noklusējuma vērtība atšķiras no „CLI”.** `ArraySession.capture()` noklusējuma vērtība ir `processing="debayered"`; komandas `chloros-cli lattice array-capture` noklusējuma vērtība ir `processing="all"`. Lai atspoguļotu CLI /GUI daudzlīmeņu saglabāšanu, eksplicīti nododiet `processing="all"` no SDK.

### Uztveršanas režīmi un ierakstītāji

Masīva virsma atspoguļo GUI ierakstīšanas paneli: režīmi „Single” (Vienreizējs), „Continuous” (Nepārtraukts), „Interval” (Intervāls) un „Fastest shutter” (Ātrākais aizslēgs), kā arī divi ierakstītāji (tiešraides kompozītvideo un neapstrādāta sērija → pārstrāde bezsaistē).

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

- **`capture_repeated`**ir „SDK” nepārtrauktais/intervāla cikls. Tā kā nav `Ctrl+C`, lai to pārtrauktu no skripta, jums**jā** nodod `count` un/vai `duration_s` (cilpa apstājas, kad tiek sasniegts kāds no tiem). `interval_s` tiek mērīts no katra cikla sākuma (atbilstoši GUI). Pārējie kwargi tiek nodoti tieši uz `capture()`.
- **`record`** ir *uzraudzības līmeņa*: tas uztver reāllaika kombinēto indeksu kompozīciju, kā-tiek parādīts, tādēļ kombinētajai plūsmai jābūt atvērtai, lai tajā varētu ienākt kadri. Viens kompozīta ierakstītājs uz vienu masīvu (izraisa kļūdu, ja kāds jau darbojas).
- **`burst` → `build_video`** ir *analīzes līmeņa*: `burst` raksta neapstrādātus kadrus + katra kadra manifestu + vienu `.daq` par katru atšķirīgu DLS nolasījumu zem `<output>/bursts/<base>/` ar saglabāšanas cikla pilnu ātrumu (bez ķēdes, bez exiftool, nav tiešraides). `build_video` sinhronizē katru kadru ar tuvāko `.daq` un atkārtoti izpilda importēšanas procesa starojuma/reflektances/indeksa ķēdi. `products` ir `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` saraksts (noklusējums: apvienotais indekss). `burst().stop()` arī automātiski uzsāk apvienotā indeksa izveidi pēc labākās iespējas, kas tiek atgriezta kā `build_job` pārtraukšanas rezultātā.

#### `RecorderHandle`

To atgriež `ArraySession.record()` un `ArraySession.burst()`. Izmantojiet to kā konteksta pārvaldnieku, lai automātiski pārtrauktu darbību, izkāpjot no darbības jomas, vai vadiet to manuāli.

| Loceklis | Apraksts |
| --- | --- |
| `job_id` | Aizmugures uzdevuma identifikators (str). |
| `kind` | `"composite"` (no `record`) vai `"raw"` (no `burst`). |
| `start_stats` | Vārdnīca, ko atgriež `start` izsaukums. |
| `result` | `None` darbības laikā; galīgais apstāšanās rezultāta vārdnīca pēc apstāšanās. |
| `stats(timeout=10.0)` | Darba statistika reāllaikā (ierakstītie kadri, sasniegtais kadrskaitis sekundē, pagājušais laiks). |
| `stop(timeout=60.0)` | Pārtrauc ierakstīšanu; atgriež un saglabā cache galīgo rezultātu. Idempotents (otrais izsaukums atgriež rezultātu no cache). |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### Pievienošanās jau savienotam masīvam — `attach_array`

Ja masīvs jau darbojas (to atvēra GUI vai iepriekšējā „SDK” sesija izsauca `connect_array`), izmantojiet `attach_array`, lai iegūtu piekļuves rīku, nevis veikt atkārtotu savienošanos. <sn><id>Šādā situācijā</id></sn> `connect_array` vienmēr izraisa kļūdu „Kamera <sn>jau </sn>atrodas <sn>masīvā<id>”, jo `/array/connect` nosūtīšana loceklimnav idempotenta; `attach_array` nolasa `/api/camera/array/list` un veic saskaņošanu pēc array_id vai serials.

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

Paraugs: skriptiem SDK, kas darbojas kopā ar darbvirsmas GUI, vispirms jāmēģina izmantot `attach_array` un jāpāriet uz `connect_array`, ja pūlā vēl nav neviena masīva.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Svarīgi — context-manager iziešana PATIEŠĀM pārtrauc savienojumu.**`ArraySession.disconnect()` vienmēr veic POST pieprasījumu uz `/array/disconnect`; nav tāda pievienota aizsardzības mehānismakā tas ir `CameraSession` / `DAQSensorSession` gadījumā. Ja izmantojat kopīgu resursu ar GUI un nevēlaties nojaukt masīvu, izbeidzot darbības jomu,**nelietojiet `with` bloku** — saglabājiet rādītāju parastajā mainīgajā un izlaidiet eksplicīto `disconnect()`:
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

`status` ir viens no `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (citādi `error`). `auto_capped_fps` nozīmē, ka pieprasītā izšķirtspēja atbilst RX gredzenam tikai pie ierobežotas trigera frekvences — saglabājiet izšķirtspēju un pārejiet no `target_fps=result["recommended"]["recommended_target_fps"]` uz `connect_array` (skatīt [6. piemēru](#6-capability-probe-before-connecting-a-4-cam-array)).

**Kā izlasīt projekciju** (tāds pats modelis kā GUI paneļā „Array Settings”):

- **Burst (`frame_bytes_total`) tiek summēts katrai kamerai atsevišķi, izmantojot katras kameras reālo pikseļu formātu.**Mono**M3M**kameras pārraida Mono12 (2 B/px) neatkarīgi no tā, kādu `pixel_format` vērtību jūs norādāt, tādējādi 4 kameru pilnas izšķirtspējas kadrs ir**~25 MB** ar trim mono kamerām, nevis ~12,6 MB, kā to paredz pieņēmums par-8 bitu pieņēmums. Aizmugurējā sistēma nosaka katras kameras formātu pēc tās modeļa.
- **Pieņemamība (`burst_fits_nic_ring`) ņem vērā iztukšošanas ātrumu**, nevis salīdzina pilnu datu plūsmu ar gredzenu: sim-emit darbojas, ja hosts iztukšo RX gredzenu ātrāk, nekā kameras to piepilda. 10G hosts + 1 GbE kameras**pieņem** pilnas izšķirtspējas kadrus pat tad, ja burst pārsniedz gredzena kapacitāti; 1 GbE hosts bloķē (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` ir konservatīvs sērijas izgūšanas maksimums** — `max(readout+emit, N×emit)` ar katraskameras datu pārraides ierobežots līdz 1 GbE kameras savienojumam, neatkarīgi no ekspozīcijas. Piemēram, ~2,8 fps 4 kameru pilnas izšķirtspējas 12 bitu masīvam (atbilst izpildes laikā izmērītajiem ~2,7–3,0). Pilnais modelis: [CLI Atsauce → Masīva kadrus sekundē un sērijas uzņemšanas modelis](cli-reference.md#array-fps--burst-model).
- **Pārslogotība (`oversubscribed: true`) nozīmē, ka N × minimālā vērtība katrai kamerai pārsniedz sadursmjudrošības augšējo robežu** — fps lauki (`achievable_fps_max` / `fps_bright` / `fps_dark`) rāda 0, un automātiskā samazināšana/apvienošana to nevar novērst (tie samazina baitu skaitu kadrā, nevis baitu plūsmu sekundē). Risinājumi ir mazāks kameru skaits, jumbo rāmji vai ātrāks tīkla interfeiss; `max_cams_collision_safe` ziņo par maksimālo robežu (6 pilnas izšķirtspējas kameras 1 GbE tīklā ar 1500 MTU, 9 — izmantojot jumbo rāmjus). Atbildē ir iekļauti arī kodi `aggregate_demand_bps`, `collision_safe_ceiling_bps`, un `per_cam_floor_bps` (8 MB/s). Skatīt [Pārslogotība](#over-subscription-the-per-cam-floor).

### Atklāšana un uzskaitīšana

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## Smart-AE / Smart-Capture

LATTICE masīvi, tiklīdz tie ir pieslēgti, fonā nepārtraukti veic AE, taču nesen iestatītai ainai ir nepieciešams brīdis, lai konverģētu. **Smart-capture** ir ērta funkcija: tā noskaidro katras kameras ekspozīciju, gaida, līdz masīvs ir stabils visā logā, un tad aktivizē uzņemšanu. Tā darbojas tāpat kā grafiskajā lietotāja saskarnē: darbvirsmas lietotnes „viedās” uzņemšanas poga izsauc to pašu aizmugures galapunktu.

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

Viedās AE politika pēc noklusējuma ir konservatīva. Padariet `exposure_tolerance_pct` stingrāku, ja veicat precīzu radiometrisko darbu; padariet to plašāku strauji mainīgās ainās, kurās vēlaties vienkārši „pietiekami tuvu”.

---

## DAQ sensoru sesijas

Pastāvīgs aizmugures resursu kopums spektrālajiem sensoriem (DAQ-U caur USB, DAQ-M caur BLE, DAQ-E caur Ethernet). Atspoguļo kameras darbību: viedā atpazīšana, kopuma atkārtota izmantošana, idempotenta pievienošana.

### Viedā atpazīšana (Zero-Config)

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

### Fiksēts pārraides veids

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
| `status(timeout=10.0)` | Pūla ieraksta kopsavilkums (straumēšanas/ierakstīšanas stāvoklis, viļņa garuma diapazons, kalibrēšanas SHA, integrācijas laiks, frame_avg, AE stāvoklis). |
| `latest(n=1, timeout=10.0)` | Atgriež līdz N pēdējiem spektra kadriem. |
| `stream_start()` / `stream_stop()` | Turpināt / apturēt straumēšanu (rokturis paliek atvērts). |
| `record_start(output_dir=None, device_name=None)` | Sāk ierakstīt .daq failu. Atgriež faila ceļu. Neizpilda DAQ-U/M bez AWS kalibrēšanas komplekta (izņemot DAQ-E). |
| `record_stop()` | Pārtrauc ierakstīšanu. Atgriež `{path, rows}`. |
| `disconnect()` | Atbrīvo no pūla. Neveic darbību, ja rokturi ir pievienoti, bet nepieder lietotājam. |

> **Kapacitātes korekcijas profili (`cap_id`) nav „SDK” regulētājs.** `connect_daq_sensor()` / `DAQSensorSession` neizpauž nevienu `cap_id` parametru vai `set_cap` metodi. Izvēlieties flotes ierobežojuma korekcijas profilu, izmantojot CLI (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) vai izmantojot aizmugurējās sistēmas `/api/daq` maršrutus „HTTP” (`/api/daq/connect` un `/api/daq/<id>/cap-id` pieņem `cap_id`).

### Atklāšana — adreses atrašana savienojuma izveidei

`discover_daq_sensors()` skenē USB / BLE / ETH, meklējot sensorus, kurus *varētu* atvērt. Tas ir DAQ ekvivalents `discover_lattice_cameras()`, un vienīgais veids, kā iegūt **DAQ-M BLE MAC** — DAQ-E ierīcei ir uzņēmuma nosaukums, bet DAQ-U — COM ports, taču MAC adrese nav ne uzrakstīta uz ierīces, ne uzrādīta operētājsistēmā.

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
| `address` | COM ports / BLE MAC / uzņēmuma nosaukums — nodod `connect_daq_sensor` kā `port=` / `mac=` / `eth_host=`. |
| `display` | Cilvēkamlasāms nosaukums. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` vai `None` — ports, kuru skenēšana nevar identificēt (USB seriālie adapteri bez zondes nav atšķirami, tādēļ nezināmie tiek parādīti, nevis slēpti). |
| `extra` | Informācija par katru pārraides veidu (BLE paziņotais nosaukums, USB ražotājs, DAQ-E IP/fw/…). Tukšas vērtības tiek izlaistas. |

| Parametrs | Noklusējums | Apraksts |
| --- | --- | --- |
| `transports` | visi trīs | Sekvence (vai CSV virkne), kas ierobežo skenēšanu. Vērts norādīt, ja zināt, ko vēlaties — BLE ir lēnākais posms. |
| `scan_timeout` | 5 | Skenēšanas logs katram transporta veidam sekundēs; aizmugurējā sistēma ierobežo to līdz 1–20. |
| `timeout` | 60,0 | „HTTP” maksimālais laiks visam izsaukumam (tāpat kā citviet „SDK” dokumentā). |
| `auto_start_backend` | `True` | Izveido vietējo backend, ja neviens nedarbojas. Nekad neizveido attālinātu `backend_url`. |

> **Sensori, kas jau ir atvērti pūlā, netiek parādīti.** Savienotai BLE perifērijai tiek pārtraukta reklāma, un atvērtu COM portu nevar pārbaudīt, tāpēc atklāšanas saraksts uzrāda to, kas ir *pieejams savienošanai*. Tūlīt pēc savienošanās ir sagaidāms tukšs rezultāts — izmantojiet `list_daq_sensors()`, lai iegūtu informāciju par to, kas jums jau ir. Transporti, kuru skenēšana nevar tikt veikta (nav instalēts bleak / zeroconf), tiek izlaisti, nevis parādīti kā kļūda, tādējādi ierīce bez Bluetooth joprojām saņem atbildes par USB un ETH.

### Saraksts

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Koplietošana ar GUI / CLI

Ja GUI jau ir atvērts sensors, izsaucot `connect_daq_sensor(port="COM3")` no Python, tiek atgriezts rīks ar apzīmējumu `already_connected=True`. Sesijas`disconnect()` ir bezdarbības operācija, tādējādi jūsu skripts „SDK” neizrauj sensoru no GUI, izietot no skopas.

### Tiešās aparatūras klases (bez aizmugures)

`daq_sdk` tiek atkārtoti eksportēts ar `chloros_sdk`, tādējādi jūs varat vadīt sensorus no sākuma līdz galam procesa ietvaros bez aizmugurējās sistēmas:

> **Pieejamība:**`daq_sdk` ir iekļauts „Chloros” darbvirsmas instalācijā,**bet nav** iekļauts PyPI paketē — `pip install chloros-sdk` nodrošina `lattice_sdk`, taču atstāj `chloros_sdk.DAQ_AVAILABLE == False`. Pirms šo klašu izmantošanas pārbaudiet šo atzīmi; datorā, kurā darbojas tikai pip, sensoru vadiet caur [`connect_daq_sensor()`](#daq-sensor-sessions), kam nav nepieciešamas lokālās transporta bibliotēkas.

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

Saglabāts „Chloros” projekts ir mape, kas satur `cameras.json` + `sensors.json` + `project.json`. `open_project` ielādē manifestu, un `connect_all` katru saglabāto ierīci tiešsaistē ar tās saglabātajiem iestatījumiem — tādā pašā aparatūras stāvoklī, kādu radītu grafiskā lietotāja saskarne.

### Minimāls piemērs

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

Vai kā konteksta pārvaldnieks:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` metodes

| Metode | Apraksts |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | Atklāj un savieno katru saglabāto ierīci. Atgriež savienojuma ziņojumu par katru klasi. Izmanto darbojošos aizmuguri, ja tā klausās uz `127.0.0.1:5000`; pretējā gadījumā klusi pārslēdzas uz tiešu (bez aizmugures) `lattice_sdk` ierīču vadību — tā nekad neizveido aizmuguri. |
| `disconnect_all()` | Pārtrauc visu. |
| `capture_all(output_dir=".")` | Viens kadrs no katras kameras + masīvs + spektrs no katra sensora. |
| `stream(camera, overlays=False, fps=10.0)` | Ģenerators, kas ģenerē BGR `numpy` kadrus no nosauktas kameras (vai masīva). `overlays=False` ir tieša `lattice_sdk` satveršanas cilpa (masīvi ģenerē `{serial: frame}` vārdnīcas). `overlays=True` maršruts caur `ChlorosLocal.camera_stream()` → aizmugurējās daļas `/api/camera/<serial>/stream-annotated` MJPEG plūsmu, kur kamerassaglabātais `ui.overlay` bloks tiek nodots kā vaicājuma parametri. Nepieciešams aizmugures režīms un **autonomā kamera**: tiešā režīma kamera izraisa `RuntimeError` (aizmugures sistēma nevar iegūt kameru, kas pieder šim procesam), bet masīvs izraisa `NotImplementedError` (uzliek kompozīcijas uzlikumus katrai kamerai — straumē elementu pēc nosaukuma). Vienavienas darbības ekvivalents: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Veic izlīdzināšanu katram pašlaik pieslēgtam masīvam. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Veic kalibrēšanu / indeksēšanas cauruļvadu uz projekta attēliem (ietver `ChlorosLocal.process`; šie četri ir **vienīgie** pieņemamie kwargi — `indices=` utt. izraisa `TypeError`; indeksus iestata ar `ChlorosLocal.configure()`). Lēni veido `ChlorosLocal()`, kas automātiski palaida aizmuguri. |

Atribūti:
- `proj.cameras` — `Dict[str, CameraHandle]`, indeksēts pēc nosaukuma UN sērijas numura.
- `proj.arrays` — `Dict[str, ArrayHandle]`, indeksēts pēc nosaukuma UN array_id.
- `proj.sensors` — `Dict[str, SensorHandle]`, indeksēti pēc nosaukuma UN slot_id.
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

**Apstrādes līmeņi.** `capture()`, `grab()` un `frame_stream()` visiem tiek izmantots viens un tas pats `processing`
token, un ķēde ir kumulatīva — katrs līmenis izpilda visu, kas atrodas virs tā:

| Līmenis | Izvade | Piezīmes |
| --- | --- | --- |
| `raw` | 1-kanālu Bayer, sensora nativais | Bez demosaika. Šajā līmenī pārklājumi nav pieejami. |
| `debayered` | 3-kanālu BGR (**noklusējums**) | Bilineārā demosaika. Vienīgais līmenis, kas darbojas bez aizmugures režīma. |
| `radiance` | float32, W/m²/sr/nm | Pilna radiometriskā ķēde: demosaika + 3×3 atdalīšana (multispektrāla) + DSNU + līdzenumalauks + NIST skala, kur ekspozīcija × pastiprinājums ir izdalīts, lai vērtības būtu absolūtas. |
| `reflectance` | uint16, 32768 = 1,0 | Starojuma intensitāte, dalīta ar lejupvērsto starojuma intensitāti (ρ = π·L/E). Nepieciešams DLS/DAQ rādījums — skatīt piezīmi zemāk. |
| `display` | 8 bitu sRGB-līdzīgs | GUI ekvivalents attēls: CCM + balansa korekcija + gamma, izmantojot kameras aktīvo krāsu profilu. |

Jebkurš cits, izņemot `debayered`, prasa backend režīmu; tiešā režīma kamera izraisa
`NotImplementedError`. `reflectance` ir nepieciešams izmantojams lejupvērstais rādījums — kadra beigu punkts automātiski ievieto
apkopoto DAQ kameras DLS slotā, taču, ja DAQ nav piesaistīts, ķēde noraida
reflektances izeju un atklāti atzīmē pazeminājumu atgrieztos metadatos, nevis klusi
atdod zemākas kvalitātes rezultātu.

> **Reflektances DN skala — to nedrīkst ieprogrammēt fiksēti.** LATTICE reflektance izmanto `32768` = ρ 1,0 un atzīmē
> XMP `Chloros:PixelScale=32768`; Survey3 reflektance izmanto `65535` = ρ 1.0 un nesatur
> `Chloros:*` marķierus. Izlasiet marķieri un daliet ar to. Tas ir definēts uint16 domēnā, tādēļ tas paliek
> `32768` visos formātos, kuros tiek veikta mēroga maiņa (16 bitu TIFF, 8 bitu PNG /JPG, 32 bitu procentos) — vispirms normalizējiet
> saglabāto datu tipu atpakaļ uz uint16 (×257 no 8 bitu, ×65535 no float). Vienīgais izņēmums:
> 8-bitu avota ieraksts, kas rakstīts kā 8-bitu TIFF, tiek *apgriezts*, nevis pārskalots, tāpēc to neapraksta neviens mērogs
> to — Chloros šajā gadījumā pilnībā izlaiž `PixelScale` un MicaSense tuplu. Trūkstošu
> tagu LATTICE atstarošanas failā uzskatiet par „nav derīga mēroga”, nevis par noklusējumu.

> **EXIF dati tiek pārnesti uz eksportēto failu.** `process()` uz katru produktu kopē avota uzņēmuma GPS bloku
> **un tā ExifIFD** uz katru produktu, tādējādi eksporta faili satur `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` un `CameraSerialNumber`, kā arī
> ģeoreferencēšanu. `FocalLength` ir tas, no kā Pix4D aprēķina zemes parauga attālumu — bez tā
> rekonstrukcija atgriežas ļoti nepareizā mērogā (izmērītajā gadījumā 411 m liela teritorija
> 47,8 km platu). Kopija apzināti nav `-all:all`: IFD0 strukturālās birkas sabojā
> LATTICE izvadi, un `ExifImageWidth`/`Height` ir izslēgti, jo tie apraksta avota
> uztveršanu, nevis eksportēto rastra attēlu.

Uztveršanasposma apakškarodziņi (attiecas uz radiometriskajiem līmeņiem — `radiance`, `reflectance`, `display`):

| Karodziņš | Noklusējums | Nozīme |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + vienmērīgais lauks + 3x3 atdalīšana + NIST radiometriskā skala. |
| `apply_white_balance` | `True` | WB LUT. Ņem vērā DLS, ja DAQ ir piesaistīts kamerai. |
| `apply_index` | `False` | Veģetācijas indeksa novērtēšana. |
| `index_expression` | `None` | Formulas pārrakstīšana. Ja lauks nav tukšs → automātiskiindeksa aktivizēšana. |
| `annotated` | `False` | GUI dekorāciju pārklāšana (zebra/tīkls/smaile). Nav pieejams `raw`. |

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
> `processing` katram sērijas elementam piešķir vienu ceļu, savukārt daudzlīmeņalīmeņa (`"all"` vai
> eksplicīts `levels` saraksts) piešķir tai **sakārtotu sarakstu** ar visiem produktiem, kas saglabāti šai
> kamerai. Kombinēts tiešraides kompozīts, ja tāds tiek pārraidīts, nonāk zem papildu
> `"combined"` atslēgai, nevis zem sērijas numura. Kods, kas pieņem, ka `str` darbojas ar
> saraksta formātu, neizraisot tipa pārbaudītāja iebildumus — anotācijā kādu laiku pēc saraksta formāta ieviešanas bija norādīts `Dict[str, str]`,
> tāpēc šis aliasis pastāv. Normalizējiet
> ja vēlaties plakanu formu:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### Masīva izlīdzināšana

`ArrayHandle` atklāj pilnu izlīdzināšanas virsmu. Profili pēc noklusējuma ir pieejami tikai sesijas laikā — izsauciet `export_alignment()` eksplicīti, lai saglabātu.

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

Ja vēlaties pilnīgu neatkarību no aizmugures sistēmas (CI, bezgalvas roboti, iegultās sistēmas), importējiet `lattice_sdk` un `daq_sdk` tieši — abus atkārtoti eksportē `chloros_sdk`. Uzmanību attiecībā uz `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk` atrodas PyPI paketē (bet tam ir nepieciešama „Arena” SDK izpildes vide), savukārt `daq_sdk` tiek piegādāts tikai kopā ar datora instalācijas versiju.

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

Trīs no četriem iestatījumiem ir **brīvā režīmā**: kamera nepārtraukti eksponē, un
`capture()` atgriež nākamo kadru. `triggered` ir izņēmums — tas aktivizē
kameru, gaidot aparatūras signālu 2. līnijā, tāpēc tā neko neuzņem, kamēr tāds neierodas.

| Iestatījums | Izraisītājs | Lieto, kad |
| --- | --- | --- |
| `default` | brīvā darbība | vispārējai lietošanai |
| `high_speed` | brīvā darbība | 8 biti, 60 fps ierobežojums, īsa ekspozīcija |
| `high_quality` | brīvā darbība | 12bitu, bez kadru skaita ierobežojuma — parastā izvēle fotogrāfijām |
| `triggered` | **ieslēgts, 2. līnija** | kamera ir pieslēgta ar M8 sinhronizācijas kabeli un to iedarbina kāds cits avots |

Ja izvēlaties `triggered` (vai paši iestatāt `trigger_mode="On"`), kad nekas
nedarbina 2. līniju, katram `capture()` beigsies gaidīšanas laiks — pareizi, jo jūs esat lūguši
kamerai gaidīt. „SDK” to izskaidro, kad tas notiek; skatiet
[SC_ERR_TIMEOUT uzņemšanas laikā](#direct-hardware-backend-free).

> **Piezīme — „GVSP probe” / `SC_ERR_TIMEOUT -1011` ziņojumi savienojuma izveides brīdī nav kļūdas.**&gt; Izveidojot savienojumu, „SDK” mēģina vienoties par**jumbo rāmjiem** (9000 baitu GVSP paketes), lai nodrošinātu lielāku caurlaidspēju. Tiešā punkts-punkts tīkla kartes savienojumā (piem., lokālā `169.254.x.x` adrese) tīkls parasti nespēj pārraidīt jumbo rāmjus, tāpēc šī pārbaude beidzas ar laika limitu un reģistrē tādus ierakstus kā:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Šī ir **paredzētā rezerves rīcība**: „SDK” automātiski atgriežas pie standarta 1500 baitu pakešiem, un kamera turpina savienoties kā parasti (sekojošās `[chunk-enable …]` rindas ir daļa no parastās savienošanās secības). Attēlu uzņemšana joprojām darbojas.
>
> Jūs varat izlaist šo pārbaudi, bet **tā nav tikai žurnāla ierakstu klusinātājs — tā atslēdz jumbo rāmjus.** Kamera atbild uz „Don&#x27;t-Fragment” pingiem tikai līdz 1500 baitiem, neatkarīgi no tā, cik labs ir jūsu tīkls, tāpēc ar ping testu vien nekad nevar atrast jumbo rāmjus; šī pārbaude ir vienīgā, kas to spēj. Atspējojiet to, un kamera jebkurā tīklā uz visiem laikiem darbosies ar standarta 1500 baitu pakešiem:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Tas ir vērts tikai tādā tīklā, par kuru jūs *zināt*, ka tas nespēj pārraidīt jumbo rāmjus, jo šādā gadījumā tas ietaupa aptuveni vienu sekundi savienošanās laika katrai kamerai. Tā kā tas ir reāls kompromiss, nevis tikai kosmētisks, tagad, izmantojot šo funkciju, par to informē arī „SDK”:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Neaiztiekiet to, ja vien jums nav iemesla.** Ja funkcija paliek ieslēgta, katrs savienojums no jauna izmēra jūsu faktisko tīklu: pieslēdzieties komutatoram, kas atbalsta jumbo paketes, un nākamais savienojums pats automātiski sāks izmantot jumbo paketes, bez jebkādas konfigurācijas un bez pārstartēšanas.
>
> Ja *vēlaties* izmantot „jumbo” caurlaidspēju, aktivizējiet „jumbo” no gala līdz-end (NIC MTU 9000 + komutators, kas tos caurlaida), vai fiksējiet to ar `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000`, ja zināt, ka savienojums to atbalsta — tomēr priekšroku dodiet komandas līmeņa `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …`, nevis pastāvīgai iestatīšanai, jo fiksētais izmērs izlaiž pārbaudi un pārtrauc pielāgoties tīklam, kas atrodas pirms tā. **Katrai** ierīcei ceļā ir jāpārraida jumbo paketes — ieskaitot jebkuru PoE sadalītāju vai injektoru, kas parasti ir iemesls, kāpēc citādi jumbo atbalstoša konfigurācija nespēj tās pārraidīt.

> **`SC_ERR_TIMEOUT -1011`, kas rodas `capture()` / `grab*()` laikā, ir atšķirīga problēma — tā ir reāla kļūda.**&gt; Iepriekš minētā piezīme attiecas tikai uz `-1011`, ko reģistrējis**connect-time probe**. Ja tāda pati kļūda parādās**capture** rezultātā, tas nozīmē, ka kamera ir veiksmīgi pieslēgusies, bet nesūta attēlus:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> To var atpazīt pēc kameras, kuras *vadības* kanāls darbojas pareizi — atklāšana notiek, iestatījumi un `[chunk-enable …]` ieraksti tiek veikti veiksmīgi —, bet *katrs* kadrs pārsniedz laika limitu.
>
> **Parastais iemesls ir tas, ka kamera ir iestatīta uz aparatūras trigeri.** Ar `trigger_mode="On"` un `trigger_source="Line2"` kamera neizraida nekādu signālu, kamēr M8 sinhronizācijas kabelī neparādās elektriskais impulss. Ja šai līnijai nav pievienots kabelis, katrs attēla uztveršanas mēģinājums gaida bezgalīgi. Kamera nav bojāta un tīkls darbojas pareizi — tā dara tieši to, kas tai ir norādīts.
>
> `CameraSettings()` un `default` / `high_speed` / `high_quality` iestatījumi darbojas brīvidarbību, un attēla uzņemšana, kas beidzas ar laika limitu, kad kamera ir ieslēgta, par to informē, nevis vienkārši izdrukā `-1011`. `PRESETS["triggered"]` ieslēdz Line2, kā paredzēts.
>
> Lai piespiestu jebkuru kameru darboties brīvā režīmā:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Ja ar `trigger_mode="Off"` joprojām notiek laika izbeigšanās, kamera patiešām nenodod datus — nosūtiet mums žurnālu un `ip link show`.

#### Krāsu profili (reāllaika priekšskatījums RGB) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` izvēlas displeja krāsu profilu **reāllaika priekšskatīšanai** kamerās ar RGB (multispec kameras ignorē šo iestatījumu):

| Profils | Nozīme |
| --- | --- |
| `raw` | Pilnībā apiet radiometrisko ķēdi. |
| `linear` | DSNU + flat + WB, bez CCM, bez gamma. |
| `natural` | Lineārs + izmērīts CCM + sRGB gamma, tikai ar vienkāršu apdari (krāsu izlīdzināšana + spilgtāko toņu desaturācija) — reālistiskais noklusējums. |
| `enhanced` | `natural` plus pilnīga „hub-parity” apdare (malu izlīdzināšana, dzīvīgums, CLAHE lokālais kontrasts). Bagātāks izskats, aptuveni **divkāršām apstrādes izmaksām uz vienu kadru**, tādējādi samazinot reāllaika kadru ātrumu. |
| `custom_temp` | `natural`, bet balansa iestatījums fiksēts uz `custom_cct_k` Kelvina (DLS tiek ignorēts; fiksēts uz 2000–10000 K aizmugurējā procesa pusē). |

Profils ir **tikai reāllaika priekšskatīšanai paredzēts** ātruma/izskata regulētājs: saglabātajiem kadriem vienmēr tiek nodrošināts pilnīgs un bagātīgs apdares efekts neatkarīgi no izvēlētā profila, tādēļ `natural` izvēle, lai atgūtu kadru laiku, nesamazina diska ieraksta kvalitāti. Nezināms profils palielina `ValueError`; ja chloros backend ir pieejams, izmaiņas tiek nosūtītas arī uz to, lai nākamais priekšskatījuma kadrs tās atspoguļotu (lietotāji, kas izmanto tiešo SDK bez backend, joprojām saņem iestatījumu izmaiņas).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Mono (M3M) kameras un `Calibration`

Mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) ir vienjoslas: viena pelēkto toņu plakne, bez Bayer mozaīkas, bez 3×3 spektrālās pārrāvuma matricas. `Calibration` to atpazīst un parāda `is_mono` karodziņu. Atstarošanās joprojām tiek piemērota kā radiometriska karte katram diapazonam (atdalīšana ir identitātes matrica), taču daudzjoslu aprēķini ar vienu kameru rada drīzāk jēgpilnus rezultātus, nevis bezjēdzīgus:

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

Lai izveidotu veģetācijas indeksu, izmantojot monoaparatūru, apvienojiet vairākas M3M kameras ar dažādiem viļņu garumiem vienā saskaņotā daudzjoslu kopā (skatīt [Masīva saskaņošana](#array-alignment)) un aprēķiniet indeksu visam kopumam, nevis atsevišķai kamerai.

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

> **`apply_sensor_settings` pieņemamās atslēgas**— tieši `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; vairs netiek izmantots, jo priekšroka tiek dota `cap_id`), `filter_model` (DAQ-M) un `cap_id` (visi DAQ veidi; `None`/`""`/`"none"` = neapstrādāts sensors, bez vāka korekcijas). Nezināmas atslēgas tiek**klusi ignorētas** — piemēram, `{"integration_time": 64}` neko nedara (tai jābūt `integration_time_ms`). Atgriež `{"applied": [...], "errors": {...}}` un nekad neizraisa kļūdu.

`chloros_sdk` atkārtoti eksportē tikai iepriekš izmantoto pamatvirsmu. Pilnā `daq_sdk` publiskā „API” (22 nosaukumi) pievieno šādus elementus — importējiet tos tieši no `daq_sdk`:

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

Uztveriet bāzes klasi, lai apstrādātu „visus gadījumus, kad kaut kas „Chloros” ir nogājis greizi”:

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

### 2. Reāllaika LATTICE masīvs → atstarojums + DAQ atsauce

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

### 3. Projektorientēta datu ieguves kampaņa

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

### 5. Bezinterfeisa tiešās aparatūras (bez aizmugures) uzņemšanas skripts

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

### 7. Ierakstīšanas receptes ekvivalents (tīrs „Python”)

„CLI” receptes DSL ir tiešs „Python” ekvivalents:

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

## Aizmugures automātiskā palaišana

Smart-Connect ieejas punkti — `connect_camera`, `connect_array`, `connect_daq_sensor` un `discover_lattice_cameras` — ir vieglie „HTTP” klienti, kas pieņem, ka backend klausās uz `127.0.0.1:5000` (viedās savienošanas saskarnes noklusējuma URL). Ja GUI vai CLI jau darbojas, tāds jau ir. Ja tiek palaists tikai skripts, tā var nebūt — tādēļ šīs funkcijas **automātiski palaista komplektā iekļauto aizmugurējā servera bināro failu** (bez logabez loga, tāpat kā to dara `ChlorosLocal`) pirms to pirmā izsaukuma, pēc tam gaida līdz pat `backend_startup_timeout`, kamēr tas sāk darboties.

Noteikumi:

- **Tiek palaists tikai lokālais URL.** `backend_url`, kas norāda uz `localhost` / `127.0.0.1` / `[::1]` ir pieņemams; jebkurš cits hosts tiek uzskatīts par cita lietotāja datoru un netiek palaists.
- **Aizmugurējais serviss tiek atstāts darbojoties, lai to varētu atkārtoti izmantot** (tāpat kā „CLI”) — skriptam beidzoties, netiek veikta automātiska izslēgšana. Atkārtoti palaistot skriptu, tiek izmantots jau darbojošais aizmugurējais serviss.
- **Atteikties no `auto_start_backend=False`** jebkurā no šiem izsaukumiem (piem., ja esat norādījis uz attālo backend vai pats pārvaldāt backend dzīves ciklu).

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

Ja komplektā iekļautais binārais fails nav atrodams vai to nevar palaist, nākamais izsaukums „HTTP” izraisa rīcībai piemērotu, **platformasatbilstošu** kļūdu `ChlorosConnectError`, nevis vienkāršu traci par atteiktu savienojumu — sistēmā „Windows” tā norāda uz galda lietojumprogrammu vai komandu `chloros-cli`; Linux (bez grafiskās saskarnes) tas norāda uz komandu `chloros-cli` vai `.deb`.

---

## Vide un galvenes

SDKs katru aizmugurējās sistēmas HTTP izsaukumu atzīmē ar `X-Chloros-Client: sdk`. Aizmugurējā sistēma piemēro SDK / CLI licencēšanas noteikumus (nepieciešama pieteikšanās **un** maksas Chloros+ plāns), nevis bezmaksas līmeņa ceļu, kāds ir GUI. Tas tiek iestatīts automātiski importēšanas brīdī — jums nav nekas jādara.

`http://localhost` un `http://127.0.0.1` tiek atpazīti kā vietējais backend. Aicinājumi uz citiem serveriem (piemēram, jūsu paša analītikas pakalpojumu) netiek mainīti.

Pārrakstiet aizmugurējos serverus URL, norādot `backend_url=` (vai `api_url=` uz `ChlorosLocal`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(`backend_url`, kas nav loopback, sasniedz tikai avota/ierīces backend — piegādātie backendi piesaistās tikai loopback; skatiet sadaļu „Tālbackend režīms”, lai uzzinātu par tuneļa modeli.)

---

## Versiju pārvaldība un savietojamība

- „SDK” versija tiek eksponēta kā `chloros_sdk.__version__`.
- „SDK” piesaista darbību komplektā iekļautajai backend versijai. Vecākas „SDK” versijas apvienošana ar jaunāku backend parasti darbojas (uz priekšu saderīgi galapunkti), taču jaunākas „SDK” versijas apvienošana ar vecāku backend var izraisīt `404` kļūdas jaunajos galapunktos — atjauniniet datora lietotni atbilstoši.
- „Smart-connect” saskarne (`connect_camera` / `connect_array` / `connect_daq_sensor`) un tīkla analīzes galapunkts atgriež stabilas JSON shēmas; jaunie lauki tiek pievienoti.

---

## Problēmu novēršanas norādes

- **`ChlorosAuthenticationError: Login required`** → Vienreiz palaidiet `chloros-cli login EMAIL PASSWORD` šajā datorā vai piesakieties, izmantojot darbvirsmas lietotni „Chloros”.
- **`ChlorosConnectError: No Chloros backend is running …`** → „Smart-Connect“ izsaukumi automātiski palaista vietējo backend, tādēļ šis paziņojums parādās tikai tad, ja nevar atrast/palaist komplektā iekļauto bināro failu (piemēram, uz datoru, kurā ir tikai pip un nav darbvirsmas pakotnes). Ziņojums ir atkarīgs no platformas: sistēmā „Windows” atveriet darbvirsmas lietotni vai izpildiet jebkuru `chloros-cli` komandu; Linux vidē palaidiet komandu `chloros-cli` (GUI nav pieejams) vai instalējiet `.deb`. Attālinātajam backendam izmantojiet `backend_url=` (un `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** importēšanas laikā → `lattice_sdk` neizdevās ielādēt (parasti nav instalētas Arena „SDK” darbības laika DLL). Virsma bez kameras joprojām darbojas.
- **„Array connect” atgriež zemāku izšķirtspēju nekā sistēmai raksturīga**→ Aizmugurējās sistēmas „smart-prep” automātiski samazina kadra izmēru, lai tas iederētos vadā. Izmantojiet `analyze_array_network()`, lai noskaidrotu iemeslu, pēc tam vai nu uzlabojiet savienojumu, pieņemiet izmēra samazināšanu vai izmantojiet `force_tier="slip-emit-and-capture"` secīgai uzņemšanai. Samazināšanas drošības tīkls**neaptver** kopējo pārslogojumu (`oversubscribed: true`, fps lauki 0): pārāk liels kameru skaits vadam nevar tikt novērsts ar binning/ROI — samaziniet kameru skaitu, ieslēdziet jumbo rāmjus vai pārejiet uz ātrāku tīkla karti (skatiet [Pārslogojums](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` ziņo, ka tīkla kartes RX gredzens ir ļoti mazs (~0,26 MB) / savienojuma vārti ar &quot;FRAMES WILL DROP&quot;** → Galvenā tīkla kartes uztveršanas gredzens ir nokonfigurēts pēc noklusējuma (bieži vien pēc tīkla kartes draivera atjaunināšanas tiek atiestatīts uz 32). Realtek USB 10GbE adapterī iestatiet `ReceiveBufferLen=256` un `PendingReceives=64` (ar paaugstinātām tiesībām), pēc tam pārstartējiet backend, lai tas atkārtoti nolasītu gredzenu. Pilnā procedūra: [CLI Atsauce → Galvenā NIC konfigurēšana un optimizēšana](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Hosts iesaldējas atkārtotas palaišanas/izslēgšanas laikā, vēlāk rodas WMI kļūdas `Invalid class` / tīkla karte neaktivizējas** → Novecojis USB 10GbE draiveris izraisa `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atjauniniet adaptera draiveri uz aktuālo versiju (≥ 2026) un atkārtoti piemērojiet saņemšanas gredzena iestatījumus. Skatīt [CLI Atsauce → Host NIC Setup &amp; Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Atstarošanās noraidīta** → Lai iegūtu atstarošanos absolūtajā skalā, kamerai (vai matricai) ir jāpiesaista aktīvs DAQ. Piesaistiet to, izmantojot grafisko lietotāja saskarni, vai izmantojiet `processing="radiance"` (W/m²/sr/nm), kam nav nepieciešams pāra sensors.
- **`smart=True` datu ieguve ilgst ilgāk nekā gaidīts** → AE konverģence ir atkarīga no ainas dinamikas; samaziniet `exposure_tolerance_pct` vai saīsiniet `stability_window_s`, ja vēlaties ātrāku (mazāk stabilu) izraisītāju.

---

## Skatīt arī

- [CLI Atsauce](cli-reference.md) — katra „CLI” apakškommanda atspoguļo „SDK” izsaukumu.
- [DAQ sensoru rokasgrāmata](../daq/README.md) — sensoru specifiskie vadu savienojumi, kalibrēšana un ierakstīšanas noteikumi.
- Tiešsaistes dokumentācija: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
