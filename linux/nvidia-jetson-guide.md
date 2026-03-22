# NVIDIA Jetson rokasgrāmata

Chloros uz NVIDIA Jetson nodrošina multispektrālo attēlu apstrādi perifērijā — laukā, bezpilota lidaparātos un attālās instalācijās. Chloros automātiski atpazīst jūsu Jetson modeli un optimizē apstrādes stratēģiju atbilstoši jūsu aparatūrai.

***

## Atbalstītie Jetson modeļi

| Modelis                | RAM            | Apstrādes stratēģija                                   | Ieteicamais lietojums                                          |
| -------------------- | -------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32–64 GB kopīgi | `GPU_PARALLEL` (4 darba procesi)                            | Maksimāla veiktspēja, lieli datu kopumi                      |
| **Jetson Orin NX**   | 8–16 GB kopīgi  | `GPU_PARALLEL` (3 darba vienības, 16 GB) / `GPU_SINGLE` (8 GB) | Galvenais ieteikums izmantošanai gaisā un laukā |
| **Jetson Orin Nano** | 8 GB koplietošana     | `GPU_SINGLE` (1 darbinieks)                               | Ieejas līmeņa malu aprēķini                                 |
| **Jetson Nano**      | 4–8 GB koplietošana   | `GPU_SINGLE` (1 darba vienība)                               | Ieejas līmeņa risinājums ar ierobežotu atmiņu                          |

{% hint style="info" %}
**Vecākie Jetson modeļi** (TX2, TX1, Xavier NX) var netikt atbalstīti. Veiktspēja atšķirsies atkarībā no pieejamās GPU atmiņas un CUDA iespējām.
{% endhint %}

***

## Prasības

* **JetPack 6.x** (ieteicams jaunākais)
* **NVIDIA CUDA** (iekļauts JetPack)
* **Chloros+ licence** (nepieciešama, lai piekļūtu CLI/SDK)

## Instalēšana

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

Vispārīga informācija par Linux instalēšanu ir pieejama sadaļā [Linux instalēšana](linux-installation.md).

***

## Dinamiskā aprēķinu pielāgošana Jetson

Chloros automātiski atpazīst jūsu Jetson modeli un izvēlas optimālo apstrādes stratēģiju. **Nav nepieciešama manuāla pielāgošana.**

### Kā tas darbojas

Palaides brīdī Chloros veic jūsu sistēmas profilēšanu:

1. **Atpazīst Jetson modeli**, izmantojot `/proc/device-tree/model`
2. **Nolasīta pieejamā GPU/koplietošanas atmiņa**

3.**Izvēlēta apstrādes stratēģija** (`GPU_PARALLEL`, `GPU_SINGLE` vai `CPU_PARALLEL`)
4. **Automātiski iestatīts darba vienību skaits, cauruļvada tips un atmiņas sadale**

### Uzvedība katram modelim

| Jetson modelis                | Stratēģija       | Darbinieki | Pieslēguma kanāls                       | Vienlaicīgums |
| --------------------------- | -------------- | ------- | ------------------------------ | ----------- |
| **Jetson Nano 8GB**         | `GPU_SINGLE`   | 1       | `tiled_gpu` (atmiņas efektīvs) | Sērijveida  |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | `tiled_gpu`                    | Sērijveida  |
| **Jetson Orin NX 8 GB**      | `GPU_SINGLE`   | 2       | `tiled_gpu`                    | Serializēts  |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 3       | `fused_gpu` (pilns GPU ceļš)    | Vienlaicīgi  |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 4       | `fused_gpu`                    | Vienlaicīgi  |

{% hint style="success" %}
**Jetson Orin NX 16 GB** ir ideāls risinājums izvietošanai perifērijā — tam tiek piemērota `GPU_PARALLEL` stratēģija ar 3 vienlaicīgiem darba procesiem, nodrošinot reālu paralēlu GPU apstrādi kompakta izmēra ierīcē.
{% endhint %}

Galvenā atšķirība starp platformām ir **atmiņa**. Jetson Nano ar 8 GB kopējās atmiņas attēlus jāapstrādā pa vienam, izmantojot atmiņas ziņā efektīvu mozaīkveida pieeju, savukārt Orin NX ar 16 GB var vienlaikus apstrādāt 3 attēlus ar GPU, izmantojot augstākas caurlaidspējas apvienoto cauruļvadu.

Pilnīgu aprēķinu pielāgošanas atsauci skatiet [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md).

***

## Siltuma vadība

Jetson ierīcēm ir ierobežota siltuma rezerves jauda, īpaši slēgtās vai gaisā esošās instalācijās. Chloros ietver automātisku siltuma uzraudzību un ierobežošanu:

| Temperatūra         | Darbība                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70 °C**          | Normāla darbība — pilna apstrādes ātruma          |
| **70 °C** (Brīdinājums)  | Automātiski samazināt partijas lielumu                   |
| **80 °C** (Kritiska situācija) | Agresīva jaudas ierobežošana — zemāka vienlaicīguma pakāpe         |
| **90°C** (Izslēgšana) | Pilnībā pārtrauc GPU apstrādi — nepieciešama atdzesēšana |

{% hint style="warning" %}
**Nodrošiniet atbilstošu ventilāciju un siltuma novadīšanu** ilgstošai apstrādei, īpaši slēgtās lauka korpusos vai gaisa sistēmās. Termiskā jaudas ierobežošana samazinās apstrādes caurlaidspēju, lai aizsargātu aparatūru.
{% endhint %}

***

## Atmiņas pārvaldība

Jetson ierīces izmanto **vienotu atmiņu** — GPU un CPU dala vienu un to pašu fizisko RAM. Tas nozīmē, ka norādītā VRAM (piem., 15,3 GB Orin NX 16 GB) nav atvēlēta tikai GPU; tā tiek dalīta ar operētājsistēmu un citiem procesiem.

### Ieteikumi par apmaiņas atmiņu

Lieliem datu kopumiem vai Texture Aware debayer apstrādei Chloros var ieteikt izveidot apmaiņas atmiņu:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Aplēses par atmiņas patēriņu vienam attēlam:**

* Standarta debayer: ~10 MB vienam attēlam
* Texture Aware debayer: ~15 MB vienam attēlam

Chloros automātiski aprēķina nepieciešamo atmiņu, pamatojoties uz jūsu datu kopas lielumu, un brīdina, ja tiek ieteikts izmantot apmaiņas telpu.

### OOM (Out of Memory) rezerves risinājums

Ja apstrādes laikā tiek konstatēts atmiņas trūkums:

1. Chloros automātiski samazina GPU darba vienību skaitu
2. Pāriet no `fused_gpu` uz `tiled_gpu` cauruļvadu (efektīvāks atmiņas izmantojums)
3. Turpina apstrādi ar samazinātu caurlaidspēju, nevis pārtrauc darbību

***

## Ieviešana laukā

### Enerģijas patēriņa apsvērumi

| Jetson modelis     | Tipisks enerģijas patēriņš | Piezīmes                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Nano      | 5–10 W              | USB-C vai cilindriskais savienotājs    |
| Jetson Orin Nano | 7–15 W              | DC cilindriskais savienotājs          |
| Jetson Orin NX   | 10–25 W             | DC cilindriskais savienotājs          |
| Jetson AGX Orin  | 15–60 W             | USB-C PD vai cilindriskais savienotājs |

Plānojiet enerģijas budžetu ilgstošai apstrādei — maksimālais enerģijas patēriņš rodas GPU intensīvā 3. pavedienā (Apstrāde).

### Ieteikumi par datu uzglabāšanu

* **NVMe SSD** ir stingri ieteicams arm64 ieviešanai
* SD kartes ir pārāk lēnas apstrādei — izmantojiet tās tikai kā sākuma datu nesējus
* Plānojiet 2–3 reizes lielāku apjomu nekā jūsu neapstrādāto attēlu datiem apstrādātajam rezultātam

### Darbība bez monitora, izmantojot SSH

Chloros CLI ir ideāls bezmonitora Jetson ieviešanai:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### Automatizēta apstrāde ar systemd

Izveidojiet systemd pakalpojumu automatizētai apstrādei:

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

### Pamata Jetson apstrāde

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
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
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Ieteicamās Jetson sistēmas lietošanai laukā

Lietošanai laukā un gaisā apsveriet šādas Jetson Orin NX 16 GB nesējplates opcijas:

* **Gaisā/dronos**: sistēmas ar vibrācijas izturību (MIL-STD), vieglas (mazāk par 300 g), ar pasīvo dzesēšanu
* **Izturīgas lauka apstākļos**: IP67/IP69K ūdensizturīgi korpusi ar PoE GigE kameru savienojamību
* **Minimāls/budžeta**: Izstrādātāju komplekti ar papildus korpusiem

Sazinieties ar [MAPIR atbalsta dienestu](https://www.mapir.camera/community/contact), lai saņemtu konkrētus aparatūras ieteikumus jūsu izmantošanas scenārijam.

***

## Nākamie soļi

* [Linux uzstādīšana](linux-installation.md) — Vispārīga informācija par Linux uzstādīšanu
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — Pilnīga aprēķinu stratēģijas atsauce
* [Apstrādes cauruļvads](../processing-architecture/processing-pipeline.md) — 4 pavedienu cauruļvada izpratne
* [CLI : Komandrinda](../CLI.md) — Pilnīga CLI atsauce
* [API : Python SDK](../api-python-sdk.md) — Pilna SDK atsauce
