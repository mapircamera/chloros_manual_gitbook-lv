# API: Python SDK

{% hint style="info" %}
**Meklējat pilnīgo API?** Šī lapa ir praktisks apmācības materiāls. Visas publiskās klases, metodes, precīzas parakstīšanas un kopēšanai un ielīmēšanai piemērotie piemēri atrodami [SDK atsauces materiālā](reference/sdk-reference.md), kas ir optimizēts AI palīgiem.**Strādājat ar AI palīgu?** Ielīmējiet šo URL čatā, lai tam būtu pieejama pilnā, aktuālā Chloros 1.3.0 API versija:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Katra šīs rokasgrāmatas lapa ir pieejama kā neapstrādāts Markdown fails ar mazajiem burtiem rakstītu slugu + `.md`, un visa rokasgrāmata ir indeksēta `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` vietnē PyPI) nodrošina visas darbvirsmas lietotnes funkcijas, izmantojot Python: attēlu apstrādi partijās, LATTICE kameras un matricas vadību reāllaikā, DAQ gaismas sensoru sesijas un saglabāto projektu automatizāciju. Tas ir plāns slānis virs tā paša lokālā backend, ko izmanto GUI un CLI (HTTP uz `127.0.0.1:5000`), tādēļ darbība ir identiska visās trīs saskarnēs.

## Instalēšana

SDK ir iekļauts Chloros darbvirsmas paketē, kas nodrošina arī apstrādes backend un aparatūras izpildes vidi, un tā versija vienmēr atbilst SDK versijai.

**

1. solis — Instalējiet Chloros.** Windows: palaidiet darbvirsmas instalētāju (noklusējuma ceļš `C:\Program Files\Chloros\`) no [Lejupielādes](download.md) lapas. Linux: instalējiet `.deb` paketi kopā ar `sudo apt install ./chloros_1.3.0_<arch>.deb` ([Linux instalācija](linux/linux-installation.md)).**

2. solis — Pārbaudiet SDK** (Python 3.7+). Parasti nav nekas jāinstalē:

* **Windows:** instalētājs instalē komplektā iekļauto SDK wheel jūsu sistēmas Python vidē (ja Python nav instalēts, šis solis tiek izlaists).
* **Linux:** `.deb` instalē SDK sistēmai `python3`, `/usr/lib/python3/dist-packages`. Pakete iesaka tās prasības — `python3-numpy` un `python3-requests`: `apt install` tās iekļauj, bet `dpkg -i` — nē.
* **Virtualenv** neredz sistēmas pakotnes; tajā jāinstalē komplektā iekļautais „wheel” fails: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` Linux vidē vai `chloros_sdk-*.whl`, kas atrodas `sdk` mapē Windows instalācijas mapē (pēc noklusējuma — `C:\Program Files\Chloros\sdk\`).

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

Datorā, kurā nav Chloros pakotnes un kura SDK vada aizmuguri citā datorā (skatiet [Ko iegūst, veicot instalāciju tikai ar pip](#what-a-pip-only-install-gets)), instalējiet to no PyPI: `pip install chloros-sdk`. PyPI tiek atjaunināts katrā izlaides versijā, tādējādi tas atbilst jaunākajai stabilajai versijai.

**

3. solis — Piesakieties vienu reizi katrā datorā:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Pieslēgšanās dati tiek saglabāti kešatmiņā `~/.chloros/` (abās platformās). Windows vidē varat pieteikties arī, izmantojot darbvirsmas lietotnes cilni „Lietotājs” <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">. SDK prasa maksas Chloros+ plānu — skatiet [Licences prasības](#license-requirement) zemāk.

| Prasība | Sīkāka informācija |
| --- | --- |
| **Chloros instalēts** | Windows: datora instalētājs; Linux: `.deb` pakete (nodrošina backend bināro failu un SDK) |
| **Python** | 3.7 vai jaunāka versija (izstrādāts/testēts ar 3.10) |
| **Operētājsistēma** | Windows 10/11 64 bitu, Ubuntu 22.04 LTS vai jaunāka versija, vai NVIDIA Jetson (JetPack 6) |
| **Licence** | Aktīva „Chloros+“ pieteikšanās, jebkurš maksas līmenis (Copper vai augstāks) |

## Uzvara 60 sekundēs

Ar vienu izsaukumu tiek izveidots projekts, importēta mape, konfigurēta apstrāde un palaista apstrādes ķēde — automātiski palaistot backend, ja tas vēl nedarbojas:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(Linux sistēmā izmantojiet Linux ceļus: `/home/user/drone_images/flight001`. SDK darbojas identiski abās platformās.)

Apstrādājat LATTICE uzņemto attēlu mapi? Izmantojiet LATTICE-draudzīgo apvalku — tas piemēro pareizos noklusējumus (bez paneļa mērķa noteikšanas, standarta debayer):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — pilnīga apstrādes procesa kontrole

Ja nepieciešams kaut kas vairāk nekā viena rinda, izmantojiet `ChlorosLocal`. Tas izmanto jau darbojošos backend (galddatoru lietotnes), vai arī pirmajā lietošanas reizē palaista instalēto (`auto_start_backend=True`; Windows vidē no Chloros instalācijas mapes `backend` mapes, pēc noklusējuma — `C:\Program Files\Chloros\backend\`), izveido un konfigurē projektus, uzrauga izpildes gaitu un pēc izpildes atgriež kopsavilkumu.

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

{% hint style="info" %}
Saglabājiet noklusējuma `http://127.0.0.1:5000`, nevis aizstājiet to ar `localhost` — Windows vidē `localhost` vispirms tiek pārveidots par `::1`, un tas aizņem apmēram 2 sekundes uz vienu pieprasījumu, izmantojot tikai IPv4 atbalsta sistēmu.
{% endhint %}

Izmantojiet to kā konteksta pārvaldnieku. Izietot no bloka, tiek pārtraukta šīs instances aizmugurējās sistēmas izmantošana, nevis pati aizmugurējā sistēma: tā turpina darboties nākamajam skriptam un apstājas pati pēc 30 minūtēm, ja netiek izmantota. `shutdown_backend()` aptur vienu, ko šī instance ir sākusi.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` pieņem šādus atslēgvārdus: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` un `custom_settings`. Galvenās vērtības:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE specifiskie regulatori (`input_level`, `radiometric_output`, `array_alignment*` sērija) ir dokumentēti kopā ar to pilnajām vērtību tabulām [SDK atsauces materiālā](reference/sdk-reference.md#supported-values).

### Progresa uzraudzība

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Izpildes kopsavilkuma nolasīšana — un tukšo izpildes ciklu atklāšana

Pēc pabeigšanas `process()` pievieno aizmugurējās sistēmas apstrādes kopsavilkumu kā `result["summary"]`. Katrs ieraksts `summary["hints"]` ir pilns teikums, kas izskaidro jebkuru ievērojamo apstākli — piemēram, kāpēc izpildes rezultātā netika iegūts neviens izvades rezultāts — un katrs norādījums tiek atkārtoti izvadīts kā Python `UserWarning`, tādējādi tukšās izpildes tiek automātiski diagnosticētas, pat ja jūs nekad nepārbaudāt vārdnīcu:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` netiek izraisīts, ja izpildes rezultātā netiek ģenerēti attēli.** Šī ir vienīgā vieta, kur SDK un CLI apzināti atšķiras: `chloros-cli process` uzskata situāciju „tika pieprasīti produkti, bet neviens netika ierakstīts” par kļūdu un beidz darbu ar rezultātu, kas nav nulle, savukārt SDK atgriežas normāli un ziņo par šo stāvokli, izmantojot `summary` / norādes. Ja jūsu procesa ķēdei jāapstājas, ja izpilde ir tukša, pārbaudiet to paši — pārbaudiet `summary` (vai saskaitiet failus projekta mapē), nevis paļaujieties uz izņēmumu.
{% endhint %}

## Smart Connect — reāllaika aparatūra

Trīs palīgprogrammas atver pastāvīgas sesijas aizmugurējās sistēmas aparatūras pūlā — tajā pašā pūlā, ko izmanto CLI, citi skripti un GUI kameru cilnes, tādējādi SDK skripti var darboties kopā ar tiem, nekonkurējot par seriālajiem portiem vai tīkla joslas platumu. Visas trīs automātiski palaista vietējo aizmugurējo sistēmu, ja neviena no tām nedarbojas. GUI DAQ panelis nav daļa no pūla: aizveriet gaismas sensoru GUI, pirms to atverat no skripta.

### Viena LATTICE kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**Relatīvie ceļi atrodas tur, kur darbojas jūsu skripts (mainīts 1.3.0 versijā).** Izmantojot lokālo backend, relatīvais `output_dir` (ieskaitot noklusējuma `"output"`) — un pārējie ceļi, kurus atver backend (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — tiek atrisināti attiecībā pret jūsu skripta darba direktoriju, tādējādi `cam.capture("output/")` raksta uz `<current folder>/output/`. Versijā 1.2.0 instalētais backend tos atrisināja savā instalācijas mapē un izraisīja kļūdu „Piekļuve liegta”. Attiecībā uz attālo backend ceļi tiek nosūtīti tā, kā tie ir ievadīti.
{% endhint %}

### Sinhronizēts masīvs — `connect_array`

`connect_array` ir ieteicamais sākumpunkts daudzkameru sistēmām. Tas izpilda to pašu „smart-prep” darbplūsmu kā grafiskā lietotāja saskarne: tīkla analīze, sinhronizācijas līmeņa automātiska izvēle, PTP laika sinhronizācija, pikseļu formāta izvēle katrai kamerai, AE sākotnējā iestatīšana un GPIO trigera aktivizēšana. **Pirmā sērijas numura kamera ir galvenā** (tā raidīta aparatūras trigera impulsu); pārējās ir pakļautās.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Pievienojiet `smart=True` jebkurai masīva uzņemšanai, lai pirms trigera aktivizēšanas pagaidītu, kamēr automātiskā ekspozīcija izlīdzinās visās kamerās.

Jaunums versijā 1.3.0 — `ArraySession` arī nodrošina sinhronizētus kadrus tieši, nerakstot failus:

* `arr.grab()` atgriež vienu kadru no katras kameras, visus no viena trigera, kā numpy masīvus (`{serial: array}`); `.group_id` ir kopīgais izraisītājs, un `.errors` nosauc jebkuru kameru, kas nav iekļauta.
* `arr.frame_stream(fps=5.0)` katrā takts ciklā nodrošina vienu šādu kopu, lai to varētu apstrādāt reāllaikā jūsu paša apstrādes ķēdē.
* `arr.continuous("output/")` palaista aizmugurējās sistēmas paša nepārtraukto uzņemšanu (GUI nepārtrauktais režīms) un atgriež `ContinuousHandle` kopā ar `status()`, `wait()` un `stop()` — izmantojiet to uzņemšanas cilpas vietā, ja jums ir nepieciešams, lai katrs trigers tiktu saglabāts ar augstu ātrumu.

Par reģistrēšanas režīmiem (Vienreizējs / Nepārtraukts / Intervāls / Ātrākais), reģistratoriem, burst-to-video un masīva izlīdzināšanu skatiet [SDK atsauci](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### DAQ gaismas sensors — `connect_daq_sensor`

Ja nav norādīti argumenti, `connect_daq_sensor()` automātiski atpazīst datu pārraides veidu (prioritāte: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Katrs rāmis satur 135 punktu `spectrum` (W/m²/nm pēc kalibrēšanas), `is_saturated` karodziņu un CIE `x`, `y`, `z`. Lai norādītu konkrētu sensoru vai transporta veidu, nododiet vienu skaidru norādi:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Ņemiet vērā, ka maksimālā līmeņa korekcijas profili (`cap_id`) **nav** SDK regulētājs — tos izvēlieties, izmantojot `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap`.

### Saglabātie projekti — `open_project`

Saglabāts Chloros projekts saglabā savu pieslēgto aparatūru (`cameras.json` + `sensors.json` kopā ar `project.json`), un `chloros_sdk.open_project(path)` var vienlaikus atkārtoti savienot savas kameras, matricas un gaismas sensorus un vadīt uzņemšanu pēc ierīces nosaukuma. Gaismas sensori atkārtoti savienojas caur darbojošos Chloros backend, saglabājot savus iestatījumus un uzņemšanas ierobežojumus. Skatīt [Projekta automatizācija](reference/sdk-reference.md#project-automation--chlorosproject) atsauces sadaļā.

## Kas tiek iegūts, veicot instalāciju tikai ar pip

Pirms aparatūras virsmu izmantošanas pārbaudiet pieejamības karodziņus moduļu līmenī:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Uz servera, kurā ir **tikai** `pip install chloros-sdk` un nav Chloros darbvirsmas paketes:

* `ChlorosLocal`, `process_folder` un `process_lattice_capture` **nedarbojas** — tiem ir nepieciešama aizmugures binārā programma, kas ir iekļauta darbvirsmas instalētājā.
* „Smart-connect” palīgrīki (`connect_camera`, `connect_array`, `connect_daq_sensor`) ir tīri HTTP-klienti, tādēļ tie darbojas ar backend, kas atrodas citā datorā — taču piegādātie backend&#x27;i ir saistīti tikai ar loopback, tāpēc jums pašiem ir jāpāradresē ports (piem., `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) un jāpārsūta `backend_url="http://127.0.0.1:5000"` kopā ar `auto_start_backend=False`. Skatīt [Attālinātā aizmugurējā servera režīms](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Tiešās aparatūras LATTICE klases (`LatticeCamera`, `CameraPool`, …) tiek importētas, taču tām ir nepieciešama Arena SDK izpildes vide no darbvirsmas paketes — bez tās `CAMERA_AVAILABLE` ir `False`.
* DAQ sensori tiek vadīti tikai caur (tunelētu) backend — `connect_daq_sensor()` vai projekta `proj.sensors`; backend atver sensoru savā datorā.

## AI palīgi — `chloros-mcp`

SDK ietver `chloros-mcp` — MCP serveri, kas ļauj „Claude Desktop”, „Claude Code”, „Cursor” un citiem MCP klientiem vadīt Chloros: apstrādāt attēlus un darbināt „LATTICE” kameras, matricas un DAQ gaismas sensorus. Tā rīki aptver visu, ko dara šis SDK. Tas tiek instalēts kopā ar SDK, tāpēc „Claude Code” konfigurēšanai pietiek ar vienu rindu:

```bash
claude mcp add chloros -- chloros-mcp
```

Citi klienti: [Chloros izmantošana ar AI palīgiem](ai-assistants.md). Opcijas un visi rīki: [MCP serveris (AI palīgi)](reference/sdk-reference.md#mcp-server-ai-assistants).

## Licences prasības

Lai piekļūtu SDK, ir nepieciešama aktīva Chloros+ pieteikšanās jebkurā maksas līmenī — **Copper vai augstāk**(Copper / Bronze / Silver / Gold); bezmaksas līmenim „Iron” nav piekļuves SDK/CLI. Pārbaude notiek**servera pusē**: katram SDK pieprasījumam jābūt saistītam gan ar aktīvu sesiju, gan ar maksas plānu, pretējā gadījumā backend atgriež kļūdas kodus `403` / `PLAN_UPGRADE_REQUIRED` (ko `ChlorosLocal` palīgi ģenerē kā `ChlorosLicenseError`, bet `connect_*` palīgi — kā `ChlorosConnectError`). Lietotājs, kurš ir izslēdzies no sistēmas, saņem kļūdas kodus `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — atkārtota `chloros-cli login` izpilde atrisina pirmo gadījumu, bet ne otro.

Lietošana bezsaistē darbojas plāna papildlaika periodā: līmenis tiek nolasīts no servera validācijas keša (5 minūtes) vai parakstītā, ar ierīci saistītā licences keša (30 dienas mēneša plāniem; līdz abonementa termiņa beigām gada plāniem). Kad atbrīvojuma periods beidzas, plāns pāriet uz bezmaksas līmeni un SDK piekļuve tiek pārtraukta, līdz ierīce vismaz reizi izveido savienojumu ar serveri. `chloros-cli status` paliek pieejams bezmaksas līmenī, tādējādi iemesls vienmēr ir redzams. Skatīt [Chloros+ pieteikšanās](chloros+-login.md).

## Izņēmumi

Piesakieties uz bāzes klasi, lai apstrādātu „visas Chloros kļūdas”:

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

Visi cauruļvada izņēmumi (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) izriet no `ChlorosError`. Viena niansīte: `ChlorosConnectError` — to izraisa tikai `connect_camera` / `connect_array` / `connect_daq_sensor` — izriet no parastā `Exception`, **nevis** no `ChlorosError`, tādēļ `except ChlorosError` to neuzķers. Pilnā hierarhija ir atrodama [SDK atsauces dokumentācijā](reference/sdk-reference.md#exceptions).

## Skatīt arī

* [SDK atsauces dokumentācija](reference/sdk-reference.md) — pilnīga API virsma, kas optimizēta AI palīgiem.
* [CLI atsauces materiāls](reference/cli-reference.md) — katra CLI apakškommanda atspoguļo SDK izsaukumu.
* [Chloros izmantošana ar AI palīgiem](ai-assistants.md) — `chloros-mcp` uzstādīšanas un uzvedņu paraugi.
* [Lejupielāde](download.md) — instalatori Windows un Linux sistēmām.
