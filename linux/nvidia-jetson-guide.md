# NVIDIA Jetson rokasgrāmata

Chloros uz NVIDIA Jetson nodrošina multispektrālo attēlu apstrādi perifērijā — laukā, bezpilota lidaparātos un attālās instalācijās. Chloros 1.2.0 sākuma posmā atpazīst jūsu Jetson modeli un optimizē apstrādes stratēģiju atbilstoši atrastajai aparatūrai. **Nav nepieciešama manuāla pielāgošana.**

***

## Atbalstītie „Jetson“ modeļi

| Modelis                | RAM            | Apstrādes stratēģija                                     | Ieteicamais lietojums                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32–64 GB kopīgi | `GPU_PARALLEL` (2 darba procesi)                              | Maksimāla veiktspēja, lieli datu kopumi                      |
| **Jetson Orin NX**   | 8–16 GB koplietošana  | `GPU_PARALLEL` (2 darba procesi, 16 GB) / `GPU_SINGLE` (8 GB)   | Galvenais ieteikums izmantošanai gaisā un lauka apstākļos |
| **Jetson Orin Nano** | 8 GB kopīgi     | `GPU_SINGLE` (1 darba vienība, secīga apstrāde)                     | Ieejas līmeņa malējā aprēķināšana                                 |

{% hint style="info" %}
Linux arm64 paketei ir nepieciešams **JetPack 6**, kas ir pieejams Jetson Orin sērijas ierīcēs. Vecāki modeļi (Nano, TX2, Xavier NX) nevar darbināt JetPack 6 un pašreizējā pakete tos neatbalsta.
{% endhint %}

***

## Prasības

* **JetPack 6.x** (ieteicams jaunākais)
* **NVIDIA CUDA** (iekļauts JetPack)
* **Maksas Chloros+ plāns** — Copper līmenis vai augstāks (nepieciešams visai CLI/SDK piekļuvei; tiek piemērots servera pusē)

## Instalēšana

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.2.0"

# Install Python SDK (optional) — the bundled wheel always matches this build
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl

# Run system diagnostics
chloros-cli selftest
```

Vispārīga informācija par Linux instalēšanu, failu atrašanās vietām un problēmu novēršanu ir pieejama sadaļā [Linux instalēšana](linux-installation.md).

{% hint style="info" %}
**Ievietojiet izpakošanas direktoriju ātrdarbīgā atmiņā.** Kompilētie binārie faili katrā palaišanas reizē paši izpakojas pagaidu direktorijā — no SD kartes tas notiek ārkārtīgi lēni. Chloros automātiski izmanto `/mnt/ssd/tmp`, ja tas pastāv; pretējā gadījumā iestatiet `TMPDIR` uz ceļu jūsu NVMe (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Dinamiskā aprēķinu pielāgošana „Jetson”

### Kā tas darbojas

Palaides brīdī Chloros veic jūsu sistēmas profilēšanu:

1. **Atpazīst Jetson modeli**, izmantojot `/proc/device-tree/model`
2. **Nolasa pieejamo kopīgo GPU/CPU atmiņu** (Jetson izmanto vienotu atmiņu)
3. **Izvēlas apstrādes stratēģiju** (`GPU_PARALLEL`, `GPU_SINGLE` vai `CPU_PARALLEL`)
4. **Automātiski iestata darba procesu skaitu, cauruļvada tipu un atmiņas sadalījumu**Lēmumu nosaka**kopējā koplietojamā RAM**, nevis modeļa nosaukums:

* **Ja kopējā RAM ir mazāk par 12 GB**(visi 8 GB Jetson modeļi): `GPU_SINGLE` ar**1 darba procesu — apzināta secīga apstrāde**. Atmiņas resursi ir pārāk ierobežoti, lai nodrošinātu vairāku darba procesu vienlaicīgu darbību, tādēļ attēli tiek apstrādāti pa vienam. Jetson ierīcēs ar**8 GB vai mazāk**

3. pavediens pilnībā izlaiž darba procesu kopu un veic apstrādi katram attēlam atsevišķi procesa ietvaros.
* **12 GB vai vairāk**(Orin NX 16 GB, AGX Orin): vienotā atmiņa atbilst `GPU_PARALLEL` prasībām, taču darba vienību skaits**Jetson ierīcēs ir ierobežots līdz 2** — gan GPU, gan darba procesu RAM, gan katra darba procesa CUDA konteksti izmanto vienu un to pašu kopīgo pūlu, tāpēc lielāks darba procesu skaits rada risku, ka var rasties atmiņas pārslodzes kļūdas.

Automātisko izvēli var pārrakstīt, izmantojot vides mainīgo `CHLOROS_STRATEGY` — skatiet [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Uzvedība katram modelim

| Jetson modelis                | Stratēģija       | Darba procesi | Izpilde                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8 GB**    | `GPU_SINGLE`   | 1       | Sekvenciāla cilpa procesa ietvaros (`tiled_gpu`, ja ir atmiņas trūkums) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | Sekvenciāla cilpa procesa ietvaros                     |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 2       | Vienlaicīgi darba procesi, `fused_gpu` ceļš  |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 2       | Vienlaicīgi darba procesi, `fused_gpu` ceļš  |

Galvenā atšķirība starp platformām ir **atmiņa**. 8 GB Jetson lielas slodzes apstākļos attēlus jāapstrādā pa vienam, izmantojot atmiņas resursus taupīgu mozaīkveida pieeju, savukārt 16 GB un vairāk Orin var vienlaikus apstrādāt 2 attēlus ar GPU, izmantojot augstākas caurlaidspējas apvienoto cauruļvadu.

### GPU budžets katram modelim

Katram „Jetson” modelim ir arī aparatūras profils, kas nosaka, cik daudz no kopējā resursu pūla var pieprasīt apstrāde, un mēro partiju izmērus:

| Modelis | GPU budžeta maksimums | Partijas izmēra reizinātājs | Rezervēts sistēmai/ekrānam |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | 70 % | ×0,8 | 2,0 GB |
| **Jetson Orin NX** | 75 % | ×1,0 | 3,0 GB |
| **Jetson AGX Orin** | 80 % | ×1,5 | 4,0 GB |

Konstatētā RAM atmiņa pielāgo profilu: ja Jetson ziņo par **16 GB vai vairāk**, tā partijas reizinātājs tiek palielināts par ×1,2. Bāzes partijas lielums pirms reizinātāju piemērošanas ir 8 attēli.

Pilnīgu aprēķinu pielāgošanas atsauci skatiet sadaļā [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md).

***

## GPU frekvences ierobežojums funkcijai „Texture Aware” Nano un Orin Nano ierīcēs

„Texture Aware” debayer izmanto GPU neironu tīkla secinājumus, kas var izraisīt **pārslodzes brīdinājumus**zema enerģijas patēriņa „Jetson” modeļos (10–15 W klase) pie pilna GPU takts frekvences. Pirms „Texture Aware” apstrādes uz**„Jetson Nano” vai „Orin Nano”**, Chloros pārbauda GPU maksimālo frekvenci un ierobežo to līdz**510 MHz** (510000000), ja tā pašlaik ir augstāka:

* Ja CLI var rakstīt GPU frekvences sysfs mezglu, ierobežojums tiek **piemērots automātiski** un tiek izvadīts apstiprinājums.
* Ja nē (nepieciešamas root tiesības), CLI parāda precīzu `sudo` komandu, lai ierobežojumu piemērotu manuāli, pagaidītu brīdi, lai jūs to varētu izlasīt, un tad turpina — apstrāde joprojām turpinās, bet var parādīties brīdinājumi par pārslodzi.

Lai ierobežojumu piemērotu pats pirms apstrādes:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Modeļi ar lielāku jaudu (Orin NX 25W, AGX Orin 60W) darbojas ar pilnu GPU ātrumu; ierobežojums netiek piemērots. Standarta debayer nekad neaktivizē ierobežojumu nevienam modelim.

{% hint style="info" %}
**„Texture Aware” uz „Jetson” vienmēr apstrādā vienu attēlu vienlaikus.** Katram darba procesam būtu nepieciešams savs CUDA konteksts (~1 GB) un sava trokšņu noņemšanas modeļa kopija, ko vienotā atmiņa nevar nodrošināt — tādēļ Jetson ierīcēs „Texture Aware” ceļš tiek piesaistīts vienam darba procesam ar serializētu piekļuvi GPU. Jārēķinās, ka „Texture Aware” jebkurā Jetson ierīcē darbosies ievērojami lēnāk nekā „Standard”.
{% endhint %}

***

## Siltuma pārvaldība

Jetson ierīcēm ir ierobežota siltuma rezerves jauda, it īpaši slēgtās telpās vai gaisā. Chloros uzrauga SoC temperatūru un automātiski ierobežo partiju izmērus:

| Temperatūra         | Rīcība                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70 °C**          | Normāla darbība — pilna apstrādes ātrums          |
| **70 °C** (brīdinājums) | Partijas lielums pakāpeniski samazinās (no 100 % līdz 50 % temperatūras diapazonā no 70 °C līdz 80 °C) |
| **80 °C** (Kritisks) | Agresīva jaudas ierobežošana (no 50 % līdz 0 % diapazonā no 80 °C līdz 90 °C) |
| **90 °C** (Izslēgšanās) | Pilnībā aptur GPU apstrādi — nepieciešama atdzesēšana |

{% hint style="warning" %}
**Nodrošiniet atbilstošu ventilāciju un siltuma novadīšanu** ilgstošai apstrādei, it īpaši slēgtos lauka korpusos vai gaisa transporta sistēmās. Termiskais ierobežojums samazinās apstrādes caurlaidspēju, lai aizsargātu aparatūru.
{% endhint %}

***

## Atmiņas pārvaldība

„Jetson“ ierīces izmanto **vienotu atmiņu** — GPU un CPU kopīgi izmanto vienu un to pašu fizisko RAM. Norādītā VRAM (piem., ~15,3 GB Orin NX 16 GB modelī) nav atsevišķa GPU atmiņa; tā ir tā pati RAM, ko izmanto operētājsistēma un visi pārējie procesi.

### Brīdinājums un ieteikumi par apmaiņas atmiņu

Pirms apstrādes uz Jetson ierīces CLI saskaita RAW attēlus jūsu ievades mapē (`.tif`, `.tiff`, `.raw`, `.dng` — JPG priekšskatījumi netiek skaitīti), aprēķina maksimālo atmiņas apjomu, kas nepieciešams darbības veikšanai, un **brīdina pirms sākšanas**, ja RAM + swap, visticamāk, nebūs pietiekams. Brīdinājuma virsraksts ir `LOW MEMORY WARNING - Jetson Detected`, tajā tiek parādīts attēlu skaits, RAM, pašreizējo apmaiņas atmiņu un aprēķināto maksimālo apjomu, pēc tam norāda precīzas `fallocate` / `chmod` / `mkswap` / `swapon` komandas, kuru izmērs ir pielāgots jūsu projektam (nekad mazāks par 8 GB). Tā pauzē dažas sekundes, lai ziņojums neizzustu skrolēšanas vēsturē, pēc tam apstrāde turpinās.**Brīdinājumā izmantotie atmiņas aprēķini:**

| Debayer režīms | Bāzes vērtība | Uz vienu attēlu |
| --- | --- | --- |
| Standarta | ~1,5 GB | ~10 MB |
| Texture Aware | ~2,5 GB (modelis + Python izpildes laiks) | ~15 MB |

Brīdinājums parādās, ja aprēķinātais maksimālais apjoms pārsniedz RAM + apmaiņas atmiņu, atskaitot 1 GB drošības rezervi, un tiek ņemta vērā tikai **failos balstīta** apmaiņas atmiņa — konfigurācija, kurā tiek izmantots tikai zram, joprojām tiks atzīmēta.

Lai manuāli pievienotu apmaiņas atmiņu (piemērs: 8 GB):



<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->

```bash
# Check current memory and swap
free -h

# Create a swap file
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```### OOM (Out of Memory) apstrāde

Apstrādes laikā Chloros uzrauga GPU atmiņu un, nevis avarējot, pakāpeniski samazina veiktspēju:

1. Kad GPU atmiņas izmantojums pārsniedz **85 %**, partiju izmēri tiek preventīvi samazināti
2. Ja joprojām rodas atmiņas trūkuma gadījums, partijas izmērs tiek **samazināts uz pusi** un atkārtoti samazināts uz pusi katrā nākamajā OOM gadījumā; katra nākamā veiksmīgi apstrādātā partija šo ierobežojumu atceļ par vienu soli
3. Ilgstošas slodzes apstākļos apstrādes ceļš pāriet no `fused_gpu` uz atmiņas ziņā efektīvāko `tiled_gpu` ceļu, un kā pēdējais līdzeklis — uz apstrādi ar CPU

***

## Ieviešana praksē

### Enerģijas patēriņa apsvērumi

| Jetson modelis     | Tipiskais enerģijas patēriņš | Piezīmes                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7–15 W              | DC cilindriskais savienotājs          |
| Jetson Orin NX   | 10–25 W             | DC cilindriskais savienotājs          |
| Jetson AGX Orin  | 15–60 W             | USB-C PD vai cilindriskais savienotājs |

Plānojiet enerģijas patēriņu ilgstošai apstrādei — maksimālais enerģijas patēriņš rodas GPU intensīvā 3. posmā (apstrāde).

### Ieteikumi par datu uzglabāšanu

* **NVMe SSD** ir stingri ieteicams arm64 ieviešanai
* SD kartes ir pārāk lēnas apstrādei — izmantojiet tās tikai kā sākumlādēšanas datu nesējus
* Plānojiet 2–3 reizes lielāku vietu nekā neapstrādāto attēlu datu apjoms, lai uzglabātu apstrādātos rezultātus

### Darbība bez monitora, izmantojot SSH

Chloros CLI ir ideāli piemērots Jetson ieviešanai bez monitora:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### Pastāvīgi darbojošs backend LATTICE / DAQ-E laika sinhronizācijai

Ja jūsu Jetson bez monitora vada LATTICE kameras vai DAQ-E gaismas sensorus, aktivizējiet backend systemd pakalpojumu, lai PTP grandmaster darbotos nepārtraukti (vienība ir instalēta, bet pēc noklusējuma nav aktivizēta):

```bash
sudo systemctl enable --now chloros-backend.service
chloros-cli time-sync status
```

Sīkāku informāciju, tostarp par to, kā pakete ļauj piesaistīt PTP portus 319/320 bez root tiesībām, skatiet [Linux instalācijā](linux-installation.md#always-on-ptp-for-headless-hosts).

### Automātiska apstrāde ar systemd

Izveidojiet systemd pakalpojumu automātiskai apstrādei:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

`chloros-cli process` iziet ar rezultātu, kas nav nulle, ja izpilde, kas pieprasīja produktus, neieraksta attēlus, tādējādi systemd kļūdas statuss ir nozīmīgs uzraudzībai.

Savienojiet ar systemd taimeri plānotai apstrādei:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## Darba plūsmu piemēri

### Pamata apstrāde ar Jetson

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI
```

### Python SDK uz Jetson

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### Vairāku lidojumu partiju apstrāde

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Ieteicamās Jetson sistēmas lietošanai laukā

Lietošanai laukā un gaisā apsveriet šādas Jetson Orin NX 16 GB nesējplates iespējas:

* **Lietošanai gaisā/dronos**: sistēmas ar vibrācijas izturības klasifikāciju (MIL-STD), vieglas (mazāk nekā 300 g), ar pasīvo dzesēšanu
* **Izturīgas lietošanai laukā**: IP67/IP69K ūdensizturīgi korpusi ar PoE GigE kameras savienojamību
* **Minimāls/budžeta risinājums**: izstrādātāju komplekti ar papildus korpusiem

Sazinieties ar [MAPIR atbalsta dienestu](https://www.mapir.camera/community/contact), lai saņemtu konkrētus aparatūras ieteikumus jūsu izmantošanas scenārijam.

***

## Turpmākie soļi

* [Linux uzstādīšana](linux-installation.md) — Vispārīga informācija par Linux uzstādīšanu
* [Dinamiskā skaitļošanas pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — Pilnīga skaitļošanas stratēģijas atsauce
* [Apstrādes cauruļvads](../processing-architecture/processing-pipeline.md) — 4 pavedienu cauruļvada izpratne
* [CLI: Komandrinda](../CLI.md) — CLI rokasgrāmata
* [API : Python SDK](../api-python-sdk.md) — SDK rokasgrāmata
* [CLI atsauces](../reference/cli-reference.md) un [SDK atsauces](../reference/sdk-reference.md) — Izsmeļošs komandu/API saraksts versijai 1.2.0
