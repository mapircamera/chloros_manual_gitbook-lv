# API : Python SDK

{% hint style="info" %}
**Meklējat pilnīgo API?** Šī lapa ir praktisks apmācības materiāls. Visas publiskās klases, metodes, precīzās parakstīšanas un kopēšanai un ielīmēšanai piemērotie piemēri ir atrodami [SDK atsauces materiālā](reference/sdk-reference.md), kas ir optimizēts AI palīgiem.**Strādājat ar AI palīgu?** Ielīmējiet šo URL čatā, lai tam būtu pieejama pilnā, aktuālā Chloros 1.2.0 API versija:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Katra šīs rokasgrāmatas lapa ir pieejama kā neapstrādāts Markdown formāts, izmantojot tās mazos burtus slug + `.md`, un visa rokasgrāmata ir indeksēta `https://mapir.gitbook.io/chloros/llms.txt`.
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` vietnē PyPI) nodrošina visas darbvirsmas lietotnes funkcijas, sākot no Python: attēlu partiju apstrādi, LATTICE kameras un matricas vadību reāllaikā, DAQ gaismas sensoru sesijas un saglabāto projektu automatizāciju. Tas ir plāns slānis virs tā paša lokālā aizmugurējā moduļa, ko izmanto gan grafiskā lietotāja saskarne, gan CLI (HTTP uz `127.0.0.1:5000`), tādēļ darbība visās trīs vidēs ir identiska.

## Instalēšana

Instalēšana notiek divos posmos: vispirms Chloros darbvirsmas pakete (tā nodrošina apstrādes backend un aparatūras izpildes vidi), pēc tam Python pakete.

**

1. solis — Instalējiet Chloros.** Windows: palaidiet darbvirsmas instalētāju (noklusējuma ceļš `C:\Program Files\MAPIR\Chloros\`) no [Lejupielādes](download.md) lapas. Linux: instalējiet `.deb` paketi ([Linux instalēšana](linux/linux-installation.md)).**

2. solis — Instalējiet SDK** (Python 3.7+):

```bash
pip install chloros-sdk
```

Jums pat var nebūt nepieciešams pip: katrs instalētājs ietver atbilstošu SDK wheel. Windows instalētājs to automātiski instalē jūsu sistēmā Python; Linux `.deb` to novieto `/usr/lib/chloros/sdk/` un parāda precīzu `pip install --user` komandu. PyPI tiek atjaunināts katrā izlaides versijā, tādēļ `pip install chloros-sdk` atbilst jaunākajai stabilajai versijai.

**

3. solis — Piesakieties vienu reizi katrā datorā:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Pieslēgšanās dati tiek saglabāti kešatmiņā `~/.chloros/` (abās platformās). Windows varat pieteikties arī, izmantojot darbvirsmas lietotnes cilni „Lietotāju<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">”. SDK prasa maksas Chloros+ plānu — skatiet [licences prasības](#license-requirement) zemāk.

| Prasība | Sīkāka informācija |
| --- | --- |
| **Chloros instalēts** | Windows: datora instalētājs; Linux: `.deb` pakete (nodrošina aizmugures bināro failu) |
| **Python** | 3.7 vai jaunāka versija (izstrādāts/testēts ar 3.10) |
| **Operētājsistēma** | Windows 10/11 64-bit, Ubuntu 22.04 LTS vai jaunāka versija, vai NVIDIA Jetson (JetPack 6) |
| **Licence** | Aktīva Chloros+ pieteikšanās, jebkurš maksas līmenis (Copper vai augstāks) |

## 60 sekunžu uzvara

Ar vienu izsaukumu tiek izveidots projekts, importēta mape, konfigurēta apstrāde un palaista apstrādes ķēde — automātiski palaistot aizmugures procesu, ja tas vēl nav palaists:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(Linux lietojiet Linux ceļus: `/home/user/drone_images/flight001`. SDK darbojas identiski abās platformās.)

Apstrādājat LATTICE uzņemšanas mapi? Izmantojiet LATTICE-draudzīgo apvalku — tas piemēro pareizos noklusējumus (bez paneļa mērķa noteikšanas, standarta debayer):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — pilnīga apstrādes procesa kontrole

Visiem uzdevumiem, kas ir sarežģītāki par vienu rindu, izmantojiet `ChlorosLocal`. Tā pirmajā lietošanas reizē palaista aizmugurējo moduli (`auto_start_backend=True`), izveido un konfigurē projektus, uzrauga procesa gaitu un pēc izpildes atgriež kopsavilkumu.

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
Saglabājiet noklusējuma `http://127.0.0.1:5000`, nevis aizstājiet to ar `localhost` — Windows gadījumā `localhost` vispirms tiek pārveidots par `::1` un izmaksā apmēram 2 sekundes uz vienu pieprasījumu, izmantojot tikai IPv4 atbalsta sistēmu.
{% endhint %}

Izmantojiet to kā konteksta pārvaldnieku, lai garantētu tīrīšanu:

```python
import chloros_sdk

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

`configure()` atbalsta šādus atslēgvārdus: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` un `custom_settings`. Galvenās vērtības:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE specifiskie regulatori (`input_level`, `radiometric_output`, `array_alignment*` sērija) ir dokumentēti kopā ar pilnām vērtību tabulām [SDK atsauces dokumentā](reference/sdk-reference.md#supported-values).

### Procesu gaitu uzraudzība

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

Pēc pabeigšanas `process()` pievieno aizmugurējās sistēmas apstrādes kopsavilkumu kā `result["summary"]`. Katrs ieraksts `summary["hints"]` ir pilns teikums, kas izskaidro jebkuru ievērojamo apstākli — piemēram, kāpēc kāds izpildes cikls nedeva nekādu rezultātu — un katrs norādījums tiek atkārtoti izvadīts kā Python `UserWarning`, tādējādi tukšie izpildes cikli tiek automātiski diagnosticēti, pat ja jūs nekad nepārbaudāt vārdnīcu:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` netiek izraisīts, ja izpildes rezultātā netiek radīti attēli.** Šī ir vienīgā vieta, kur SDK un CLI apzināti atšķiras: `chloros-cli process` uzskata situāciju „tika pieprasīti rezultāti, bet neviens netika saglabāts” par kļūdu un beidz darbu ar rezultātu, kas nav nulle, savukārt SDK beidz darbu normāli un ziņo par šo stāvokli, izmantojot `summary` / hints. Ja jūsu procesa ķēdei ir jāapstājas tukšā izpildes gadījumā, pārbaudiet to paši — pārbaudiet `summary` (vai saskaitiet failus projekta mapē), nevis paļaujieties uz izņēmumu.
{% endhint %}

## Smart Connect — aktīvā aparatūra

Trīs palīgrīki atver pastāvīgas sesijas aizmugurējās sistēmas aparatūras pūlā — tajā pašā pūlā, ko izmanto grafiskā lietotāja saskarne, tādējādi SDK skripti var darboties vienlaikus ar galda lietojumprogrammu, nekonkurējot par seriālajiem portiem vai tīkla joslas platumu. Visi trīs automātiski palaista vietējo aizmugurējo sistēmu, ja neviena no tām nedarbojas.

### Viena LATTICE kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### Sinhronizēts masīvs — `connect_array`

`connect_array` ir ieteicamais sākumpunkts daudzkameru sistēmām. Tas izpilda to pašu viedo sagatavošanas plūsmu kā grafiskā lietotāja saskarne: tīkla analīze, sinhronizācijas līmeņa automātiska izvēle, PTP laika sinhronizācija, pikseļu formāta izvēle katrai kamerai, AE sākotnējā iestatīšana un GPIO trigera aktivizēšana. **Pirmais sērijas numurs ir galvenais** (tas izraisa aparatūras trigera impulsu); pārējie ir pakļautie.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Pievienojiet `smart=True` jebkurai masīva uzņemšanai, lai pirms triggera aktivizēšanas pagaidītu, kamēr visās kamerās nostabilizējas automātiskā ekspozīcija. Par uzņemšanas režīmiem (Vienreizējs / Nepārtraukts / Intervāls / Ātrākais), ierakstītājiem, burst-to-video un masīva izlīdzināšanu skatiet [SDK atsauci](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### DAQ gaismas sensors — `connect_daq_sensor`

Ja nav norādīti argumenti, `connect_daq_sensor()` automātiski atpazīst datu pārraides veidu (prioritāte: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Katrā rāmī ir iekļauts 135 punktu `spectrum` (W/m²/nm pēc kalibrēšanas), `is_saturated` karodziņš un CIE `x`, `y`, `z`. Lai norādītu konkrētu sensoru vai transporta veidu — uzticama izvēle datoriem ar vairākiem tīkla interfeisiem, kur Ethernet automātiskā atklāšana pirmajā mēģinājumā var nepamanīt darbspējīgu DAQ-E — nododiet vienu skaidru norādi:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Ņemiet vērā, ka kapacitātes korekcijas profili (`cap_id`) **nav** SDK regulētājs — izvēlieties tos, izmantojot `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap`.

### Saglabātie projekti — `open_project`

Saglabāts Chloros projekts saglabā savu pieslēgto aparatūru (`cameras.json` + `sensors.json` kopā ar `project.json`), un `chloros_sdk.open_project(path)` var visu atkārtoti savienot vienlaikus un veikt ierakstus pēc ierīces nosaukuma. Skatīt [Projekta automatizācija](reference/sdk-reference.md#project-automation--chlorosproject) atsauces materiālā.

## Ko iegūst, veicot instalāciju tikai ar pip

Pirms aparatūras virsmu izmantošanas pārbaudiet pieejamības karodziņus moduļu līmenī:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

Uz servera, kurā ir **tikai** `pip install chloros-sdk` un nav Chloros darbvirsmas paketes:

* `ChlorosLocal`, `process_folder` un `process_lattice_capture` **nedarbojas** — tiem ir nepieciešama aizmugures binārā programma, kas ir iekļauta darbvirsmas instalētājā.
* Smart-connect palīgrīki (`connect_camera`, `connect_array`, `connect_daq_sensor`) ir tīri HTTP klienti, tāpēc tie darbojas ar backend, kas atrodas citā datorā — taču piegādātie backendi ir saistīti tikai ar loopback, tāpēc jums pašiem ir jāpāradresē ports (piem., `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) un nodot `backend_url="http://127.0.0.1:5000"` kopā ar `auto_start_backend=False`. Skatiet [Attālinātā aizmugurējā servera režīmu](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Tieši ar aparatūru saistītās LATTICE klases (`LatticeCamera`, `CameraPool`, …) var importēt, taču tām ir nepieciešama Arena SDK izpildes vide no darbvirsmas pakotnes — bez tās `CAMERA_AVAILABLE` ir `False`.
* `daq_sdk` (tiešās DAQ klases) tiek piegādātas kopā ar darbvirsmas instalāciju, nevis PyPI pakotni, tāpēc `DAQ_AVAILABLE` ir `False` sistēmā, kurā tiek izmantots tikai pip — tā vietā vadiet DAQ sensorus caur `connect_daq_sensor()` pret (tunelētu) backend.

## Licences prasības

Lai piekļūtu SDK, ir nepieciešama aktīva Chloros+ pieteikšanās jebkurā maksas līmenī — **Copper vai augstāk**(Copper / Bronze / Silver / Gold); bezmaksas līmenim „Iron” nav piekļuves SDK/CLI. Prasību izpilde notiek**servera pusē**: katram SDK pieprasījumam ir jābūt gan aktīvai sesijai, gan maksas plānam, pretējā gadījumā backend atgriež `403` / `PLAN_UPGRADE_REQUIRED` (ko `ChlorosLocal` ģenerē kā `ChlorosLicenseError`, bet `connect_*` palīgrīki — kā `ChlorosConnectError`). Izslēgtajam lietotājam tiek parādīts `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — `chloros-cli login` atkārtota izpilde atrisina pirmo gadījumu, bet ne otro.

Lietošana bezsaistē darbojas plāna papildlaika periodā: līmenis tiek nolasīts no servera validācijas keša (5 minūtes) vai parakstītas, ar datoru saistītas licences keša (30 dienas mēneša plāniem; līdz abonementa termiņa beigām gada plāniem). Kad atbrīvojuma periods beidzas, plāns pāriet uz bezmaksas līmeni, un SDK piekļuve tiek pārtraukta, līdz ierīce vismaz reizi izveido savienojumu ar serveri. `chloros-cli status` paliek pieejams bezmaksas līmenī, tādējādi iemesls vienmēr ir redzams. Skatīt [Chloros+ Pieslēgšanās](chloros+-login.md).

## Izņēmumi

Izmantojiet bāzes klasi, lai apstrādātu „visus Chloros kļūdas gadījumus”:

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

Visi cauruļvada izņēmumi (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) izriet no `ChlorosError`. Viena niansīte: `ChlorosConnectError` — to izraisa tikai `connect_camera` / `connect_array` / `connect_daq_sensor` — izriet no parastā `Exception`, **nevis** no `ChlorosError`, tādēļ `except ChlorosError` to neuzķers. Pilnā hierarhija ir atrodama [SDK atsauces dokumentā](reference/sdk-reference.md#exceptions).

## Skatīt arī

* [SDK atsauce](reference/sdk-reference.md) — pilnīga API virsma, kas optimizēta AI palīgiem.
* [CLI atsauces materiāls](reference/cli-reference.md) — katra CLI apakškommanda atspoguļo SDK izsaukumu.
* [Lejupielāde](download.md) — instalatori Windows un Linux.
