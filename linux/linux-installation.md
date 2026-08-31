# Linux instalēšana

Chloros tiek izplatīts Linux kā `.deb` paketes, kas instalē CLI un aizmugures serveri. Python SDK ir atsevišķs pip pakotne (kas iekļauta arī `.deb` kā versijai atbilstoša wheel).

Pakotņu failu nosaukumos ir norādīta versija un arhitektūra: `chloros_1.2.0_amd64.deb` x86_64 un `chloros_1.2.0_arm64_jp6.deb` JetPack 6 Jetson kompilācijām. Turpmākajās komandās aizstājiet ar failu, kuru faktiski lejupielādējāt.

***

## Linux amd64 (x86_64)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Distribūcija** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **Procesors** | x86_64 (Intel/AMD) | Intel Core i7 vai labāks |
| **Atmiņa (RAM)** | 8 GB | 16 GB vai vairāk |
| **Grafikas karte** | Nav nepieciešama (apstrāde ar procesoru) | NVIDIA GPU ar 4 GB+ VRAM (12 GB+ atbloķē `GPU_PARALLEL`, 7 GB+ novērš Texture Aware iekļaušanu vienattēla ceļā) |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | SSD ar 10 GB vai vairāk brīvās vietas |
| **Python** | Python 3.7+ (SDK) | Python 3.10+ |

> **Ubuntu 20.04 un Debian 11 netiek atbalstītas.** `.deb` atkarību saraksts ir
> atvasināts no tā, ar ko Chloros aizmugurējais modulis faktiski veido saites, un tajā ietilpst
> `libc6 (>= 2.34)`. Gan Focal, gan bullseye tiek piegādātas ar glibc 2.31, tāpēc `apt`
> uzreiz noraida instalēšanu, nevis ļauj tai vēlāk neizdoties darbības laikā.

### Instalēšana

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i` neatrisina atkarības. Ja tiek ziņots par trūkstošiem pakotnēm, `sudo apt-get install -f` (vai `sudo apt --fix-broken install`) pabeidz instalēšanu — tas ir normāls process, nevis kļūda.
{% endhint %}

Pārbaudiet instalāciju:



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```***

## Linux arm64 (NVIDIA Jetson)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Platforma** | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **JetPack** | JetPack 6.x | Jaunākā JetPack 6 versija |
| **Atmiņa (RAM)** | 8 GB (kopīgi izmantojama GPU/CPU) | 16 GB+ kopīgi izmantojama (12 GB+ ir minimālā prasība paralēliem GPU darba procesiem) |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | NVMe SSD ar vismaz 10 GB brīvas vietas |
| **Python** | Python 3.7+ (SDK gadījumā) | Python 3.10+ |

### Instalācija

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

Tāds pats izkārtojums kā amd64 `.deb`, ar CUDA versiju, kas pielāgota Jetson Orin / Orin NX / Orin Nano. Informāciju par Jetson atmiņas, siltuma un izvietošanas uz vietas darbību skatiet [NVIDIA Jetson rokasgrāmatā](nvidia-jetson-guide.md).

***

## Python un SDK instalācija (visi Linux)

SDK ir tīrs Python HTTP klients backendam, tādēļ šis pats pakotne darbojas gan uz amd64, gan arm64. Divi avoti:**No PyPI** — publicētā stabilā versija:

```bash
pip install chloros-sdk
```

**No komplektā iekļautā wheel faila** — garantēti atbilst CLI/backend, ko tikko instalējāt (izmantojiet to, ja jūsu `.deb` ir jaunāks par PyPI):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**PEP 668 distribūcijas** (Ubuntu 23.10+, Debian 12+) nepieļauj pip instalācijas visā sistēmā. Izmantojiet `pip install --user …`, virtuālo vidi vai `sudo pip install --break-system-packages …`. Pakotņu instalētājs nekad automātiski neinstalē SDK jūsu sistēmas Python — šo izvēli atstāj jūsu ziņā.
{% endhint %}

Papildu opcijas:

| Papildinājums | Komanda | Pievieno |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` reāllaika progresa straumēšanai |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` BLE (DAQ-M) datu pārraidei |

Pārbaudiet SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` instalē Chloros, CLI un aizmugurējo sistēmu. Python SDK sazinās ar šo backend caur lokālo HTTP API (`http://127.0.0.1:5000`) un nepieciešamības gadījumā to automātiski palaista. Vienmēr izmantojiet burtisko IPv4 adresi, nevis `localhost` — `localhost` var tikt atrisināts kā `::1` un izmaksāt aptuveni divas sekundes uz vienu pieprasījumu.
{% endhint %}

***

## Pirmreizējā konfigurācija

### 1. Piesakieties

Lai piekļūtu CLI un SDK, ir nepieciešams maksas pakalpojuma līmenis Chloros+ (**Copper** vai augstāks), kas tiek piemērots servera pusē: lietotājam, kurš nav pieteicies, tiek piešķirts `401 AUTH_REQUIRED`, bet bezmaksas pakalpojuma (Iron) lietotājam — `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Piekļuves dati tiek saglabāti cache atmiņā `~/.chloros/user_session.json`.

{% hint style="warning" %}
**Pēc katras instalācijas vai atjaunināšanas ir jāpiesakās no jauna.** Paketes skripts `prerm` apzināti dzēš `~/.chloros/user_session.json` un kešēto licenci visiem datora lietotājiem, lai jaunā versija vienmēr atkārtoti pārbaudītu licences derīgumu, nevis paļautos uz novecojušu kešatmiņu.
{% endhint %}

### 2. Pārbaudiet savas licences statusu

```bash
chloros-cli status
```

`chloros-cli status` darbojas jebkurā līmenī (ieskaitot bezmaksas), tādējādi jūs vienmēr varat redzēt, kāpēc piekļuve ir vai nav pieejama.

### 3. Veiciet sistēmas diagnostiku

```bash
chloros-cli selftest
```

Tiek secīgi veikti septiņi pārbaudes soļi, un komanda beidzas ar rezultātu, kas nav nulle, ja kāds no tiem neizdodas:

| # | Pārbaude | Ko tā pierāda |
| --- | --- | --- |
| 1 | **Versija** | CLI ziņo par savu versiju (`v1.2.0`). |
| 2 | **Ports pieejams** | Ports 5000 ir brīvs, *vai* uz to jau ir atbildējis darbspējīgs Chloros backend (tas tiek uzskatīts par izturētu pārbaudi). |
| 3 | **Backend palaišana** | Tiek palaista backend binārā programma. |
| 4 | **API tests (`/api/test`)** | Aizmugurējais serveris atbild ar `status: ok`. |
| 5 | **Sistēmas informācija** | Izvada `GPU: <name>, CUDA: <bool>, PyTorch: <version>` no `/api/system-info`. |
| 6 | **Trokšņu noņemšanas modeļi** | Atrod `*.pth.enc` modeļus (uz Linux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + trokšņu noņemšanas rīks**| Funkcija „Texture Aware” faktiski ir izmantojama — tai nepieciešama CUDA**un** vismaz viens modeļa fails. |

Apstrāde beidzas ar `N/7 checks passed`, uzskaitot visas kļūdas pēc nosaukuma.

### 4. Apstrādājiet savu pirmo datu kopu

```bash
chloros-cli process ~/datasets/flight001
```

***

## Fails un direktoriji

### Katram lietotājam

Chloros glabā savas autentifikācijas datus un CLI konfigurāciju vienā starpplatformu direktorijā, **`~/.chloros/`** (uz Windows, `%USERPROFILE%\.chloros\`). Divas Linux specifiskas kešatmiņas savukārt atbilst XDG konvencijām — tās ņem vērā `XDG_CONFIG_HOME` / `XDG_CACHE_HOME`, ja tās ir iestatītas.

| Ceļš | Mērķis |
| --- | --- |
| `~/.chloros/user_session.json` | Pieslēgšanās sesijas kešatmiņa, ko raksta `chloros-cli login` (tiek iztīrīta katras programmatūras instalācijas/atjaunināšanas laikā) |
| `~/.chloros/working_directory.txt` | Noklusējuma projekta mapes pārrakstīšana (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI valodas iestatījumi (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Valodas iestatījums, kas tiek kopīgi izmantots ar Windows lietotāja saskarni — šeit `language` ir prioritāte pār `cli_language.json` |
| `~/.chloros/update_cache.json` | Vienas stundas kešatmiņa Linux/Jetson palaišanas atjauninājumu pārbaudei |
| `~/.chloros/backend.log` | Aizmugures sistēmas žurnāls, kad aizmugures sistēma tika palaista ar CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Kešēti LATTICE kalibrēšanas pakotnes katrai kamerai, indeksētas pēc sērijas numura un pakotnes hash |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Izvēles lietotāja pārrakstījumi DAQ kapacitātes korekcijas profiliem |
| `~/.config/chloros/system_config.json` | Kešēts aparatūras profils no Dynamic Compute Adaptation — izdzēsiet to, lai piespiestu veikt jaunu aparatūras atpazīšanu |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Backend servera žurnāli, viens fails par katru palaišanu |
| `~/Chloros Projects/` | Noklusējuma projekta mape, ja nav iestatīta pārrakstīšana |

### Vissistēmas līmenī

| Ceļš | Mērķis |
| --- | --- |
| `/usr/bin/chloros-cli` | Apvalkskripts — iestata `LD_LIBRARY_PATH` iekļautajām nativajām bibliotēkām, pēc tam palaista reālo bināro failu |
| `/usr/bin/chloros-backend` | Ietvērējskripts — tas pats, papildus `CHLOROS_PRODUCTION=1`, lai aizmugures autentifikācijas vārti nekad nevarētu klusi atspēkoties |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Kompilētie binārie faili |
| `/usr/lib/chloros/arena_runtime/` | „Arena” SDK izpildes vide, kas nepieciešama LATTICE kamerām |
| `/usr/lib/chloros/models/*.pth.enc` | Šifrēti trokšņu noņemšanas modeļi, ko izmanto „Texture Aware” debayer |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK pakotne, kas atbilst tieši šai versijai |
| `/usr/lib/chloros/exiftool` | Iekļauts exiftool (simboliska saite uz `/usr/local/bin/exiftool` tiek izveidota tikai tad, ja sistēmā nav exiftool) |
| `/etc/chloros/update.conf` | Atjauninakanāla konfigurāciju, ko nolasa `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Iestata `net.ipv4.ip_unprivileged_port_start = 319` tā, lai aizmugurējā programma varētu piesaistīt PTP portus bez root tiesībām |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Norāda dinamiskajam lādētājam uz `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Piešķir pieteicies lietotājam piekļuvi DAQ-U USB seriālajam tiltam (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend.service` | Iespējot pastāvīgi darbojošos fona pakalpojumu (uzstādīts, **nav ieslēgts**) |
| `/usr/share/applications/chloros-cli.desktop` | Lietojumprogrammu izvēlnes ieraksts „Chloros CLI”, kas atver termināli |

## Aizmugurējā procesa izpildāmā faila atrašanās vieta

CLI un SDK automātiski atpazīst aizmugurējo procesu:

| Komponents | Celiņš |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Aizmugurējā programma | `/usr/lib/chloros/chloros-backend` |

Pārrakstiet backend ceļu, izmantojot `--backend-exe` un CLI karodziņu vai `backend_exe` un SDK konstruktora parametru, un portu, izmantojot `--port` (noklusējuma vērtība ir `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` norāda uz **`lattice`**,**`project`**, un**`daq pool-*`** komandu grupām attālinātā backendā. Galvenās komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) to apzināti ignorē un vienmēr vēršas uz `http://127.0.0.1:<port>`.
{% endhint %}

***

## LATTICE kameras un DAQ gaismas sensori uz Linux

Visas „live-hardware” komandu grupas darbojas uz Linux (amd64 un Jetson):

* **`chloros-cli lattice`** — atklāj, savieno, konfigurē un veic datu ieguvi no LATTICE kamerām un sinhronizētiem masīviem. `.deb` apvieno nepieciešamo Arena SDK izpildes vidi un reģistrē to dinamiskajā lādētājā.
* **`chloros-cli daq pool-*`** — savienojiet DAQ-U/M/E gaismas sensorus caur aizmugures pūlu, straumējiet kalibrētus spektrus un ierakstiet `.daq` failus. Kompilētais CLI piegādā tikai `pool-*` ģimeni: `pool-connect`, `pool-disconnect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — vadīt saglabātu projektu (tā kameras, sensorus un apstrādes iestatījumus) bez lietotāja iejaukšanās.
* **`chloros-cli time-sync`** — pārbaudīt PTP galveno serveri, kurā darbojas Chloros aizmugurējā sistēma LATTICE kamerām un DAQ-E sensoriem.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` ir nepieciešams `pool-latest`, `pool-stream`, `pool-record` un `pool-set-cap`; `pool-list` parāda ID, kas pašlaik atrodas pūlā.

{% hint style="info" %}
**Daudzadrešu datorā pirmajai DAQ-E savienošanai ieteicams izmantot `--eth-host`.** Automātiskā atklāšana pārlūko mDNS un var nepamanīt sensora interfeisu no neaktīvas ARP kešatmiņas, tāpēc pirmais `pool-connect --eth` pēc sistēmas uzsākšanas var neizdoties, pat ja sensors darbojas pilnīgi normāli. Norādot sensora IP adresi vai uzņēmuma nosaukumu, atklāšana tiek pilnībā izlaista.
{% endhint %}

**DAQ-U sērijas atļaujas** tiek pārvaldītas ar instalēto udev noteikumu (`uaccess` + grupa `dialout`). Ja sensors, kas jau bija pievienots, joprojām ir nepieejams, pārlādējiet noteikumus vai atkārtoti pievienojiet to:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Pilnu komandu kopumu skatiet [CLI atsaucē](../CLI.md).

### Pastāvīgi ieslēgts PTP bezmonitora datoriem

Pirmās instalācijas laikā tiek ģenerēta systemd vienība `chloros-backend.service`, taču tā **nav ieslēgta**. Uz bezmonitora Jetson vai servera, kuram ir jānodrošina nepārtraukta PTP laika sinhronizācija DAQ-E sensoriem un LATTICE kamerām, to jāaktivizē:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

Bez tā PTP darbojas tikai tad, kad darbojas Chloros backend — tas ir, aktīvas CLI/SDK sesijas laikā.

Ierīce piesaista backendu pie `127.0.0.1:5000` (`CHLOROS_HOST` / `CHLOROS_PORT` vides iestatījumi ierīcē; pārrakstiet ar `sudo systemctl edit chloros-backend.service`) un pēc 5 sekundēm to pārstartē, ja rodas kļūda.

**Kā PTP iegūst savus portus.** PTP izmanto UDP 319/320, abi zem parastā 1024 privilēģētāportu robežas. Paketes `postinst` ieraksta `/etc/sysctl.d/60-chloros-ptp.conf` ar `net.ipv4.ip_unprivileged_port_start = 319`, kas ļauj aizmugurējam procesam tos piesaistīt, darbojoties kā jūsu lietotājam. Tāpat kā papildu drošības pasākums tiek piemērots `setcap cap_net_bind_service,cap_net_raw=+ep` aizmugurējās programmas binārajam failam — tieši tāpēc `libcap2-bin` ir deklarēta pakotnes atkarība.***

## Bash skriptu piemēri

{% hint style="info" %}
**Skriptēšanai piemēroti iziešanas kodi.**`chloros-cli process` veiksmīgas darbības gadījumā iziet ar kodu `0` un**ar kodu, kas nav nulle, neveiksmes gadījumā — ieskaitot izpildi, kurā tika pieprasīti attēlu produkti, bet netika uzrakstīts neviens** (tas izdrukā `Processing finished but wrote no image products.` un norāda projekta mapes nosaukumu, kā arī parastos iemeslus). Veiksmīgas darbības ziņo, cik daudz attēlu produktu tika saglabāti (`Image products written: N`). Beigšanas kodi: `0` — veiksmīgi, `1` — kļūda, `2` — argumenta kļūda, `130` — pārtraukts.
{% endhint %}

### Vairāku datu kopu apstrāde

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### Apstrāde ar pielāgotiem iestatījumiem

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

Derīgās `--format` vērtības ir tieši četras, un tās satur atstarpes — tās vienmēr jāievieto pēdiņās:

| `--format` vērtība | Izvades mape |
| --- | --- |
| `TIFF (16-bit)` *(noklusējums)* | `tiff16` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` pieņem `standard` (noklusējums) vai `texture-aware` (Chloros+).

### Automātiska apstrāde ar Cron

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK piemērs

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Problēmu novēršana

### CLI nav atrodams pēc instalēšanas

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### Atļauja atteikta

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### „setcap failed” instalācijas laikā

`.deb` piemēro `cap_net_bind_service` uz `/usr/lib/chloros/chloros-backend`, lai tas varētu piesaistīt PTP portus 319/320 bez root tiesībām. Ja instalēšanas brīdī trūka `libcap2-bin`, izsaukums tiek izlaists. Instalējiet to un atkārtoti instalējiet paketi:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP nepalaižas / nevar piesaistīt portu 319

Pārliecinieties, ka neprivileģēto portu minimālā robeža ir pazemināta, un, ja tā nav, piemērojiet to atkārtoti pašreizējai sistēmas palaišanai:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Tad pārbaudiet galveno serveri:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### „LATTICE kameru draiveri nav atrasti”

Arena SDK izpildes vide netiek atrisināta. Pārliecinieties, vai ir pieejama un atjaunināta pakotnes rakstītā lādētāja konfigurācija:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
ls /usr/lib/chloros/arena_runtime | head
```

### Backend palaišana neizdevās

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

Backend žurnāli par neveiksmīgo palaišanu atrodas `~/.cache/chloros/logs/`.

### CUDA nav atpazīta

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest` vienā rindā ziņo par to pašu: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Trūkst koplietošanas bibliotēku

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### Lēna palaišana sistēmās ar SD karti

Kompilētie binārie faili katrā palaišanas reizē izvēršas pagaidu direktorijā. Ja `/mnt/ssd/tmp` pastāv, Chloros to izmanto automātiski; pretējā gadījumā iestatiet `TMPDIR` uz ātru failu sistēmu:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Chloros atjaunināšana uz Linux

Komanda `update` ir pieejama tikai Linux/Jetson. Tā pārbauda versiju, kas publicēta atjauninājumu kanālā, kurš konfigurēts `/etc/chloros/update.conf`, un piedāvā lejupielādēt un instalēt atbilstošo `.deb`:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/Jetson sistēmā CLI katrā palaišanas reizē veic arī neblokējošu atjauninājumu pārbaudi (rezultāts tiek saglabāts kešatmiņā uz vienu stundu `~/.chloros/update_cache.json`) un parāda `Update available: vX.Y.Z`, ja ir pieejama jaunāka versija. Jūsu iestatījumi un projekti paliek nemainīgi pēc atjauninājuma; pēc tam būs nepieciešams atkārtoti pieteikties.

## Atinstalēšana

```bash
sudo apt remove chloros
```

Atinstalēšana aptur `chloros-backend.service`, atjauno noklusējuma neprivilēgēto portu minimālo robežu (1024), noņem komplektā iekļautā exiftool simbolisko saiti un Arena loader konfigurāciju, kā arī dzēš kešēto autentifikācijas informāciju. Jūsu projekti un `~/.chloros/` datu faili paliek nemainīgi.

***

## Turpmākie soļi

* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson-specifiska optimizācija un ieviešana
* [CLI : Komandrinda](../CLI.md) — CLI rokasgrāmata
* [API : Python SDK](../api-python-sdk.md) — rokasgrāmata SDK
* [CLI atsauces materiāls](../reference/cli-reference.md) un [SDK atsauces materiāls](../reference/sdk-reference.md) — izsmeļošs komandu/API saraksts versijai 1.2.0
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — kā Chloros pielāgojas jūsu aparatūrai
