# Linux instalēšana

Chloros Linux vidē tiek izplatīts kā `.deb` paketes, kas instalē CLI, aizmugures serveri un Python SDK (sistēmai `python3`, kā arī versijai atbilstošu wheel failu virtuālajām vidēm).

Pakotņu failu nosaukumos ir norādīta versija un arhitektūra: `chloros_1.3.0_amd64.deb` x86_64, un `chloros_1.3.0_arm64_jp6.deb` — arm64 (NVIDIA Jetson, JetPack 6). Turpmākajās komandās aizstājiet ar failu, kuru faktiski lejupielādējāt.

{% hint style="info" %}
**Jaunums versijā 1.3.0: programmas tiek piegādātas neiesaiņotas.** Aizmugurējā daļa un CLI tiek instalētas kā mapes — `/usr/lib/chloros/backend/` un `/usr/lib/chloros/cli/` — nevis kā atsevišķi pašizpakojoši faili. Tie vairs neizpako sevi pagaidu mapē katrā palaišanas reizē, tādēļ tie sākas daudz ātrāk (x86_64 vidē backend agrāk katrā palaišanas reizē pavadīja apmēram 100 sekundes izpakošanai) un vairs nav nepieciešami vairāki GB brīvās vietas mapē `/tmp`. Arī amd64 versijas `.deb` izmērs ir aptuveni uz pusi mazāks nekā iepriekš.
{% endhint %}

***

## Linux amd64 (x86_64)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Distribūcija** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **Procesors** | x86_64 (Intel/AMD) | Intel Core i7 vai labāks |
| **Atmiņa (RAM)** | 8 GB | 16 GB vai vairāk |
| **Grafikas karte** | Nav nepieciešama (apstrāde ar procesoru) | NVIDIA GPU ar 4 GB+ VRAM (12 GB+ atbloķē `GPU_PARALLEL`, 7 GB+ nodrošina, ka Texture Aware netiek izmantots vienattēla ceļā) |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | SSD ar 10 GB+ brīvās vietas |
| **Python** | Python 3.7+ (SDK vajadzībām) | Python 3.10+ |

> **Ubuntu 20.04 un Debian 11 netiek atbalstītas.** `.deb` atkarību saraksts ir
> atvasināts no tā, ar ko faktiski saistās Chloros aizmugurējā programma, un tajā ietilpst
> `libc6`, kas ir jaunāks par glibc 2.31, ko piedāvā „focal” un „bullseye”, tāpēc `apt`
> uzreiz noraida instalēšanu, nevis ļauj tai vēlāk neizdoties darbības laikā.

### Instalēšana

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**Izmantojiet `apt install ./…`, nevis `dpkg -i`.** `apt` atrisina pakotnes atkarības un instalē arī to, ko tā iesaka: Python SDK prasības, `python3-numpy` un `python3-requests`. `dpkg -i` neinstalē nevienu no tām. CLI un backend ir patstāvīgi un darbojas bez tiem, bet `import chloros_sdk` tos nepieciešami; instalācijas izvade norāda visus trūkstošos. Saglabājiet `./`, lai `apt` instalētu failu, nevis meklētu pakotni ar šādu nosaukumu. Pakotne deklarē arī `libgl1`, ko ielādē komplektā iekļautais OpenCV un kas var nebūt iekļauts minimālajā servera attēlā.
{% endhint %}

Pārbaudiet instalāciju:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Platforma** | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **JetPack** | JetPack 6.x | Jaunākā JetPack 6 versija |
| **Atmiņa (RAM)** | 8 GB (kopīgi izmantojama GPU/CPU) | 16 GB+ kopīgi izmantojama (12 GB+ ir minimālā prasība paralēlai apstrādei) |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | NVMe SSD ar 10 GB+ brīvas vietas |
| **Python** | Python 3.7+ (SDK vajadzībām) | Python 3.10+ |

### Instalācija

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Tāds pats izkārtojums kā amd64 `.deb` (iekļauts Python SDK, uztveršanas buferi un aizmugures pakalpojumi), paredzēts Jetson Orin / Orin NX / Orin Nano ar **JetPack 6** (L4T R36). Tā PyTorch ir JetPack 6 CUDA versija Orin, tādēļ GPU apstrāde, ieskaitot Texture Aware debayer, darbojas uz Jetson GPU. Pakete pati neietver CUDA: tā izmanto CUDA un cuDNN bibliotēkas, kuras instalē JetPack 6 (`sudo apt install nvidia-jetpack` attēlā, kurā tās nav). Ja nav pieejams CUDA GPU, apstrāde notiek uz CPU, un `--debayer texture-aware` izmanto standarta debayeru (backend reģistrē vienu `[DEBAYER] … needs a CUDA GPU …` rindu, kurā paskaidrots iemesls). Informāciju par „Jetson“ atmiņas, termiskajiem un ekspluatācijas apstākļiem skatiet [„NVIDIA Jetson“ rokasgrāmatā](nvidia-jetson-guide.md).

***

## Python SDK instalēšana (visām Linux versijām)**`.deb` automātiski instalē SDK.** Abi pakotnes (amd64 un Jetson) to instalē sistēmai `python3`, tādējādi `import chloros_sdk` darbojas uzreiz pēc instalēšanas, bez pip un bez tīkla. Tās paketes (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` un projekta lādētāji) tiek iekļauti `/usr/lib/python3/dist-packages`, kas pieder paketei: atjauninājums tos aizstāj, un, noņemot paketi, tie tiek noņemti. Versija vienmēr atbilst jūsu instalētajai CLI un backend.

SDK paša prasības, `python3-numpy` un `python3-requests`, ir **ieteicamas, nevis obligātas**: `sudo apt install ./chloros_<version>_<arch>.deb` tos instalē, bet `dpkg -i` — nē. Instalācijas izvade ziņo par SDK versiju, ko `python3` importē, par jebkuru trūkstošu prasību kā arī par jebkuru `chloros_sdk`, kas atrodas citur `python3` ceļā un aizstāj pakotnes kopiju (piemēram, tādu, kas iepriekš instalēta ar `pip install --user`).**Virtuālajā vidē**, kas neredz sistēmas pakotnes, tajā instalējiet komplektā iekļauto „wheel” failu (pilnīgi bezsaistē):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**No PyPI** — tikai datoriem, kuros nav instalēts Chloros pakotne un kuru SDK vada aizmuguri citā datorā (skatiet [Attālās aizmugures režīms](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

Papildu opcijas, ja izmantojat pip instalēšanu (virtuālajā vidē vai no PyPI):

| Papildinājums | Komanda | Pievieno |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` reāllaika progresa straumēšanai |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` BLE (DAQ-M) pārraidei |

Pārbaudiet SDK:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Python SDK sazinās ar `.deb` instalēto backend, izmantojot lokālo HTTP API (`http://127.0.0.1:5000`). `ChlorosLocal` un savienošanas palīgrīki to automātiski palaista, kad nepieciešams; saglabāta projekta `connect_all()` to nekad nedara, tāpēc datorā bez monitora palaidiet backend kā [pakalpojumu](#always-on-ptp-for-headless-hosts). Vienmēr izmantojiet burtisko IPv4 adresi, nevis `localhost` — `localhost` var tikt pārveidots uz `::1` un katram pieprasījumam var aizņemt aptuveni divas sekundes.
{% endhint %}

***

## Pirmreizējā konfigurācija

### 1. Piesakieties

Lai piekļūtu CLI un SDK, ir nepieciešams maksas Chloros+ līmenis (**Copper** vai augstāks), kas tiek piemērots servera pusē: lietotājam, kurš nav pieteicies, tiek piešķirts `401 AUTH_REQUIRED`, bet bezmaksas līmeņa (Iron) lietotājam — `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

Pieslēgšanās dati tiek saglabāti kešatmiņā `~/.chloros/user_session.json`.

{% hint style="info" %}
**Pārejot uz augstāku pakalpojuma līmeni, jūsu pieslēgšanās dati tiek saglabāti; to noņemšana tos dzēš.** Ja uzstādat jaunāku `.deb` versiju virs esošās instalācijas (tostarp izmantojot `chloros-cli update`), `~/.chloros/user_session.json` un kešētajā atmiņā saglabātā licence tiek saglabāta, tādējādi bezgrafiskie CLI/SDK uzdevumi turpina darboties. Paketes (`sudo apt remove chloros`) noņemšana dzēš saglabāto pieteikšanās informāciju un licences kešatmiņu visiem datora lietotājiem, tādēļ jaunai instalācijai atkal ir nepieciešama `chloros-cli login`.
{% endhint %}

### 2. Pārbaudiet savas licences statusu

```bash
chloros-cli status
```

`chloros-cli status` darbojas jebkurā līmenī (ieskaitot bezmaksas), tādējādi jūs vienmēr varat redzēt, kāpēc piekļuve ir vai nav pieejama.

### 3. Palaižiet sistēmas diagnostiku

```bash
chloros-cli selftest
```

Tiek secīgi veikti septiņi pārbaudes soļi, un komanda beidzas ar rezultātu, kas nav nulle, ja kāds no tiem neizdodas:

| # | Pārbaude | Ko tā pārbauda |
| --- | --- | --- |
| 1 | **Versija** | CLI ziņo savu versiju (`v1.3.0`). |
| 2 | **Ports pieejams** | Ports 5000 ir brīvs, *vai* uz to jau ir atbildējis darbspējīgs Chloros backend (tas tiek uzskatīts par izturētu pārbaudi). |
| 3 | **Backend palaišana** | Tiek palaista backend binārā programma. |
| 4 | **API tests (`/api/test`)** | Backend atbild ar `status: ok`. |
| 5 | **Sistēmas informācija** | Izvada `GPU: <name>, CUDA: <bool>, PyTorch: <version>` no `/api/system-info`. |
| 6 | **Trokšņu noņemšanas modeļi** | Atrod `*.pth.enc` modeļus (Linux vidē: `/usr/lib/chloros/models`). |
| 7 | **CUDA + trokšņu noņemšanas rīks**| Funkcija „Texture Aware” faktiski ir lietojama — tai nepieciešama CUDA**un** vismaz viens modeļa fails. |

Vizualizācija beidzas ar `N/7 checks passed`, uzskaitot visas kļūdas pēc nosaukuma.

### 4. Apstrādājiet savu pirmo datu kopu

```bash
chloros-cli process ~/datasets/flight001
```

***

## Fails un direktoriji

### Katram lietotājam

Chloros glabā savas autentifikācijas datus un CLI konfigurāciju vienā starpplatformu direktorijā, **`~/.chloros/`** (Windows vidē — `%USERPROFILE%\.chloros\`). Divas Linux specifiskās kešatmiņas savukārt atbilst XDG konvencijām — tās izmanto `XDG_CONFIG_HOME` / `XDG_CACHE_HOME`, ja tās ir iestatītas.

| Celiņš | Mērķis |
| --- | --- |
| `~/.chloros/user_session.json` | Pieslēgšanās sesijas kešatmiņa, ko raksta `chloros-cli login` (saglabājas pēc pakotnes atjaunināšanas; tiek dzēsta, kad pakotne tiek noņemta) |
| `~/.chloros/working_directory.txt` | Noklusējuma projekta mapes pārrakstīšana (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI valodas iestatījums (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Valodas iestatījums, kas tiek kopīgi izmantots ar Windows grafisko lietotāja saskarni — šeit `language` ir prioritāte pār `cli_language.json` |
| `~/.chloros/update_cache.json` | Vienas stundas kešatmiņa „Linux/Jetson” palaišanas atjauninājumu pārbaudei |
| `~/.chloros/backend.log` | Aizmugurējās sistēmas žurnāls, ja aizmugurējā sistēma tika palaista ar CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Kešēti LATTICE kalibrēšanas komplekti katrai kamerai, indeksēti pēc sērijas numura un komplekta haša |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | Izvēles lietotāja pārrakstījumi DAQ kap-korekcijas profiliem |
| `~/.config/chloros/system_config.json` | Kešēts aparatūras profils no Dynamic Compute Adaptation — izdzēsiet to, lai piespiestu veikt jaunu aparatūras atpazīšanu |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Backend servera žurnāli, viens fails par katru palaišanu |
| `~/Documents/MAPIR/Chloros Projects/` | Noklusējuma projekta mape, ja nav iestatīta pārrakstīšana |

### Sistēmas līmenī

| Celiņš | Mērķis |
| --- | --- |
| `/usr/bin/chloros-cli` | Ietverošais skripts — iestata `LD_LIBRARY_PATH` komplektā iekļautajām nativajām bibliotēkām (un `TMPDIR` uz `/mnt/ssd/tmp`, ja šī mape pastāv), pēc tam palaista faktisko bināro failu |
| `/usr/bin/chloros-backend` | Ietvērējskripts — tas pats attiecas uz aizmuguri, kuru tas atzīmē kā instalētu ražošanas versiju |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | Kompilētā CLI un aizmugure, instalēta neiesaiņota (jaunums versijā 1.3.0); programmas ir `cli/chloros-cli` un `backend/chloros-backend` |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Divrindu palaišanas skripti, kas palaista iepriekš minētās programmas — tie tiek glabāti šajos ceļos, jo SDK, CLI un vecākie skripti tos meklē šeit |
| `/usr/lib/chloros/arena_runtime/` | „Arena“ SDK” izpildes vide, kas nepieciešama „LATTICE“ kamerām |
| `/usr/lib/chloros/models/*.pth.enc` | Šifrēti trokšņu noņemšanas modeļi, ko izmanto „Texture Aware“ debayer |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (un `lattice_sdk`, `lattice_cam`, `arena_api` — projekta ielādētāji) | Python SDK, kas instalēts sistēmai `python3` un pieder šim paketam |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK „wheel“, kas precīzi atbilst šai versijai, paredzēts „virtualenv“ |
| `/usr/lib/chloros/exiftool` | Komplektā iekļautais ExifTool palaišanas rīks (ar simbolisku saiti uz `/usr/local/bin/exiftool` tikai tad, ja sistēmā nav exiftool). Sākot ar versiju 1.3.0, ExifTool skripts tiek piegādāts ar savu Perl bibliotēku `/usr/lib/chloros/exiftool-dist/`, tādējādi tas darbojas bez sistēmas `libimage-exiftool-perl` pakotnes |
| `/etc/chloros/update.conf` | Atjauninājumu kanāla konfigurācija, ko nolasa `chloros-cli update` |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Iestata `net.ipv4.ip_unprivileged_port_start = 319`, lai aizmugurējā programma varētu piesaistīt PTP portus bez root tiesībām |
| `/etc/sysctl.d/60-chloros-gige.conf` | Palielina kodola soketa uztveršanas buferus (`net.core.rmem_max`, `net.core.rmem_default`) līdz 32 MB LATTICE kameru straumēm un piemēro tos instalācijas laikā (ja vērtība jau ir iestatīta augstāka, tā netiek mainīta). Pēc kodola noklusējuma kameras paketes tiek izmestas no kodola, un masīvam zūd kadri un kadru ātrums; `chloros-cli lattice network` ziņo par buferiem, kad to līmenis ir zems. Skatīt [Linux hosti — soketa uztveršanas buferi](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Norāda dinamiskajam lādētājam uz `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Piešķir pieteikušajam lietotājam piekļuvi DAQ-U USB seriālajam tiltam (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | Piesakās uz pastāvīgi darbojošos aizmugures pakalpojumu, kas palaista aizmuguri kā lietotājs, kuru nosaucat (uzstādīts, **nav ieslēgts**) — tas, kas jāieslēdz, skatiet [Always-On PTP](#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | Tas pats pakalpojums, kas palaista fonā kā **root**(uzstādīts,**nav ieslēgts**), tiek saglabāts, lai dators, kurā tas ir ieslēgts, turpinātu darboties |
| `/var/log/chloros/` | Root pakalpojuma detalizētais žurnāls, ko var lasīt `adm` grupa (tiek dzēsts, kad pakete tiek pilnībā noņemta) |
| `/usr/share/applications/chloros-cli.desktop` | „Chloros CLI” lietojumprogrammu izvēlnes ieraksts, kas atver termināli |

**Ugunsmūris (jauns 1.3.0 versijā).** Ja instalēšanas brīdī ir aktīvs `ufw` vai `firewalld`, pakete atver UDP portus `5002` (DAQ-E dati), `5353` (mDNS atklāšana), `319` un `320` (PTP), un noņem šos noteikumus, kad pakete tiek noņemta. Ja ugunsmūris ir instalēts, bet nav aktīvs, tas neko nepievieno un parāda atļaujamos portus gadījumam, ja to vēlāk aktivizētu. Tas neatver nevienu TCP portu: DAQ-E programmaparatūras atjaunināšana, kuras laikā sensors atkal izveido savienojumu ar šo datoru, izmantojot TCP, lai savāktu attēlu, beidzas ar kļūdu `… device never connected back on TCP <port> …`, ja aktīvs ugunsmūris bloķē šo savienojumu.

## Aizmugurējās programmas izpildāmā faila atrašanās vieta

CLI un SDK automātiski atpazīst aizmugurējo programmu:

| Komponents | Celiņš |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Aizmugurējā programma | `/usr/lib/chloros/chloros-backend` (palaišanas programma `/usr/lib/chloros/backend/chloros-backend`) |

Pārrakstiet backend ceļu, izmantojot `--backend-exe` CLI karodziņu vai `backend_exe` SDK konstruktora parametru, un portu — ar `--port` (noklusējuma vērtība ir `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` norāda uz **`lattice`**,**`project`**un**`daq pool-*`** komandu grupām attālajā backendā. Galvenās komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) to apzināti ignorē un vienmēr vēršas uz `http://127.0.0.1:<port>`.
{% endhint %}

***

## LATTICE kameras un DAQ gaismas sensori Linux vidē

Visas „live-hardware” komandu grupas darbojas Linux vidē (amd64 un Jetson):

* **`chloros-cli lattice`** — atklāj, savieno, konfigurē un veic datu ieguvi no LATTICE kamerām un sinhronizētiem masīviem. `.deb` apvieno nepieciešamo Arena SDK izpildes vidi un reģistrē to dinamiskajā lādētājā.
* **`chloros-cli daq pool-*`** — savienot DAQ-U/M/E gaismas sensorus caur aizmugures resursu kopu, straumēt kalibrētus spektrus, ierakstīt `.daq` failus un mainīt kapu profilus, kā arī DAQ-E gadījumā mainīt tā tīkla iestatījumus, pārstartēt to un atjaunināt tā programmaparatūru. `chloros-cli daq discover` meklē sensorus, kurus varat pieslēgt, un `chloros-cli daq --help` uzskaita `pool-*` apakškommandas.
* **`chloros-cli project`** — bez monitora vadīt saglabātu projektu (tā kameras, sensorus un apstrādes iestatījumus).
* **`chloros-cli time-sync`** — pārbaudiet PTP galveno serveri, kuru Chloros aizmugurējā sistēma izmanto LATTICE kamerām un DAQ-E sensoriem.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` ir nepieciešams komandai `pool-latest`, `pool-stream`, `pool-record` un `pool-set-cap`; `pool-list` parāda ID, kas pašlaik atrodas pūlā.

{% hint style="info" %}
**`--eth-host` izlaiž atklāšanu.** `pool-connect --eth` atrod DAQ-E, izmantojot mDNS, ar ARP rezerves risinājumu, kas darbojas no tukšas ARP kešatmiņas; nododot sensora IP adresi vai uzņēmuma nosaukumu ar `--eth-host`, tiek izveidots tiešs savienojums ar to.
{% endhint %}

**DAQ-U seriālās atļaujas** tiek pārvaldītas ar instalēto udev noteikumu (`uaccess` + grupa `dialout`). Ja sensors, kas jau bija pievienots, joprojām ir nepieejams, pārlādējiet noteikumus vai atkārtoti pievienojiet to:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Pilnu komandu kopumu skatiet [CLI atsauces materiālā](../CLI.md).

### Pastāvīgi ieslēgts PTP bezmonitora datoriem

Pakete instalē divus systemd pakalpojumus aizmugurējam serverim, neviens no kuriem nav ieslēgts. Uz bezmonitora „Jetson” vai servera, kuram ir jānodrošina nepārtraukta PTP laika sinhronizācija DAQ-E sensoriem un LATTICE kamerām, vai kur SDK skripti savieno saglabātus projektus (`connect_all()` nekad nepalaiž aizmuguri), aktivizējiet pakalpojumu **kā savs lietotājs**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` palaista backend kā šis lietotājs: tā ierakstītie dati pieder šim lietotājam, detalizētais žurnāls atrodas viņa `~/.cache/chloros/logs`, un tas izmanto viņa `chloros-cli login` (`~/.chloros`), tāpēc vispirms reizi piesakieties kā šis lietotājs. Paketes instalēšana izdrukā šo komandu, kurā ir ievadīts jūsu lietotājvārds.

Bez pakalpojuma backend (un līdz ar to arī PTP grandmaster) darbojas tikai tad, ja to ir palaidis kāds — komanda „`chloros-cli`” vai SDK skripts.

Modulis piesaista backend pie `127.0.0.1:5000` (vienības iekšējie iestatījumi `CHLOROS_HOST` / `CHLOROS_PORT`; pārrakstiet ar `sudo systemctl edit chloros-backend@.service`) un pēc 5 sekundēm to pārstartē, ja rodas kļūda.

**Root pakalpojums.**`chloros-backend.service` palaida backend kā**root**. Tas joprojām ir instalēts, lai dators, kurā tas ir ieslēgts, turpinātu darboties, bet viss, ko root aizmugurējais modulis ieraksta, pieder root, tādēļ lietotājs nevar dzēst savus ierakstus; tā žurnāls atrodas `/var/log/chloros`, un to var lasīt `adm` grupa. Lai pārslēgtos uz savu lietotāju:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**Pakalpojuma backend ir koplietošanas režīmā, un klients to nekad neaptur.** „`chloros-cli`” un SDK skripti to izmanto tā, kā to atrod: „`process`” darbojas uz tā, ielādējot savu projektu pēdējā vietā. Gadījumā, ja CLI citādi pārstartētu backend (`--restart` vai `process`, kura importēšana neko neatrada), tā nosauc vienību, izdrukā `sudo systemctl restart <unit>` un iziet no `1`, jo backend, kas tiktu apturēts šādā veidā, paliktu neaktīvs. Pārstartējiet to paši ar `sudo systemctl restart chloros-backend@$USER.service`. Pakalpojuma backend nekad pats neapstājas, ja tas ir neaktīvs, un `chloros-cli backend stop` to noraida, nosaucot komandu `systemctl stop`.**Kā PTP iegūst savus portus.** PTP izmanto UDP 319/320, kuri abi atrodas zem parastā 1024 privilēģēto portu sliekšņa. Paketes `postinst` raksta `/etc/sysctl.d/60-chloros-ptp.conf` ar `net.ipv4.ip_unprivileged_port_start = 319`, kas ļauj aizmugurējam modulim tos piesaistīt, darbojoties kā jūsu lietotājam. Sākot ar versiju 1.3.0, pakete aizmugurē neuzstāda nekādas failu iespējas (`setcap`), un `libcap2-bin` vairs nav atkarība: neiesaiņotajā aizmugurē failu iespēja neļautu tai sākties, tāpēc to nepievienojiet paši.

***

## Bash skriptu piemēri

{% hint style="info" %}
**Skriptēšanai piemēroti iziešanas kodi.**`chloros-cli process` veiksmīgas darbības gadījumā iziet no `0`, bet**ne-nulle — neveiksmes gadījumā, ieskaitot izpildi, kurā tika pieprasīti attēlu produkti, bet netika uzrakstīts neviens** (tad tiek izvadīts `Processing finished but wrote no image products.` un norādīts projekta mapes nosaukums, kā arī parastie iemesli). Veiksmīgas darbības ziņo, cik daudz attēlu produktu tika saglabāti (`Image products written: N`, kā arī `(plus M clamp masks)`, ja tika saglabātas maskas). Izvades kodi: `0` — veiksmīga izpilde, `1` — kļūda, `2` — argumenta kļūda, `130` — pārtraukta izpilde.
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
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

Derīgās `--format` vērtības ir tieši četras, un tās satur atstarpes — tās vienmēr jāievieto pēdiņās:

| `--format` vērtība | Izvades mape |
| --- | --- |
| `TIFF (16-bit)` *(noklusējums)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
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
    indices=["NDVI", "OSAVI"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Problēmu novēršana

### Pēc instalēšanas nav atrasts CLI

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
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### Aizmugurējā sistēma nepalaižas pēc failu iespēju pievienošanas

Neizpildiet `setcap` aizmugurējā sistēmā. Sākot ar versiju 1.3.0, pakete nenostāda nekādas failu iespējas, un PTP porti 319/320 tiek iegūti no `sysctl` iestatījuma, kas aprakstīts sadaļā [Kā PTP iegūst savus portus](#always-on-ptp-for-headless-hosts). Failu iespēja pārvērš izpakoto aizmugurējo sistēmu drošā izpildes režīmā, kurā tā nespēj atrast savas bibliotēkas, tādēļ tā nepalaižas. Ja esat pievienojis šādu iespēju, noņemiet to:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP nepalaižas / nevar piesaistīt 319. portu

Pārliecinieties, ka neprivileģēto portu minimālais līmenis ir pazemināts, un, ja tas nav izdarīts, piemērojiet to atkārtoti pašreizējai sistēmas uzsākšanai:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Tad pārbaudiet galveno serveri:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### `lattice` komandas neizdodas ar kļūdu „LATTICE kameras draiveri nav atrasti”

Aizmugurējā sistēma nevarēja ielādēt LATTICE kameras darbības vidi, ar kuru tā ir instalēta. Katra `lattice` komanda tiek izpildīta aizmugurējā sistēmā; pati CLI neietver kameru draiverus. Pāriinstalējiet Chloros (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`): `.deb` instalē darbības vidi blakus backendam, `/usr/lib/chloros/arena_runtime`, un reģistrē to dinamiskajā lādētājā. Lai pārbaudītu šo reģistrāciju:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
ls /usr/lib/chloros/arena_runtime | head
```

### Aizmugurējās sistēmas palaišana neizdevās

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

Aizmugurējās sistēmas žurnāli par neveiksmīgo palaišanu atrodas `~/.cache/chloros/logs/`. Ja ports 5000 pieder aizmugurējās sistēmas pakalpojumam (`systemctl status chloros-backend@$USER.service`), pārstartējiet pakalpojumu, nevis pārtrauciet tā procesu.

### CUDA nav atpazīta

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest` vienā rindā ziņo par to pašu: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Trūkst koplietošanas bibliotēku

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### Lēna palaišana sistēmās ar SD karti

Sākot ar versiju 1.3.0, aizmugurējais pakalpojums un CLI tiek instalēti neiesaiņoti, tādējādi tie vairs neizpako sevi pagaidu direktorijā katrā palaišanas reizē. Pagaidu faili automātiski tiek saglabāti `/mnt/ssd/tmp`, ja šī mape pastāv; pretējā gadījumā tos var novirzīt uz ātru failu sistēmu:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Chloros atjaunināšana Linux

Komanda `update` ir pieejama tikai Linux/Jetson vidē. Tā pārbauda versiju, kas publicēta atjauninājumu kanālā, kurš konfigurēts `/etc/chloros/update.conf`, un piedāvā lejupielādēt un instalēt atbilstošo `.deb`:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/Jetson vidē CLI katrā palaišanas reizē veic arī neblokējošu atjauninājumu pārbaudi (rezultāts tiek saglabāts kešatmiņā uz vienu stundu `~/.chloros/update_cache.json`) un parāda `Update available: vX.Y.Z`, ja ir pieejama jaunāka versija. Jūsu iestatījumi, projekti un pieteikšanās dati paliek nemainīgi pēc atjauninājuma.

Paketes atjaunināšana aptur visus aizmugures pakalpojumus uzstādīšanas laikā, tādēļ darbojošs skripts zaudē savas kameras un sensorus, un pēc tam atkārtoti palaista katra no tām, kas ir ieslēgta (tā, kas palaista manuāli, bet nekad nav bijusi ieslēgta, paliek apturēta, tāpat kā pēc pārstartēšanas).

## Atinstalēšana

```bash
sudo apt remove chloros
```

Atinstalēšana aptur un atspējo visus aizmugures pakalpojumus, atjauno noklusējuma neprivilēgēto portu robežu (1024), izdzēš saņemšanas bufera iestatījumu failu (pašreizējās vērtības saglabājas līdz sistēmas pārstartēšanai), komplektā iekļauto exiftool simbolisko saiti, „Arena“ ielādētāja konfigurāciju, ugunsmūra noteikumus, kurus pievienoja pakete, un instalēto Python SDK, kā arī dzēš kešēto autentifikācijas informāciju. Jūsu projekti un `~/.chloros/` datu faili paliek neskarti. `sudo apt purge chloros` arī dzēš `/var/log/chloros`.

***

## Turpmākie soļi

* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson-specifiska optimizācija un ieviešana
* [CLI: Komandrinda](../CLI.md) — CLI rokasgrāmata
* [API: Python SDK](../api-python-sdk.md) — SDK rokasgrāmata
* [CLI atsauces materiāls](../reference/cli-reference.md) un [SDK atsauces materiāls](../reference/sdk-reference.md) — izsmeļošs komandu/API saraksts versijai 1.3.0
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — kā Chloros pielāgojas jūsu aparatūrai
