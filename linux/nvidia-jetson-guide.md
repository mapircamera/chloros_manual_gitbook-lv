# NVIDIA Jetson rokasgrāmata

Chloros uz NVIDIA Jetson ļauj veikt multispektrālo attēlu apstrādi perifērijā — laukā, bezpilota lidaparātos un attālās instalācijās. Chloros 1.3.0 versija palaišanas brīdī atpazīst jūsu Jetson modeli un optimizē apstrādes stratēģiju atbilstoši atrastajai aparatūrai. **Nav nepieciešama manuāla pielāgošana.**
{% hint style="info" %}
**

1.3.0 Jetson pakete veic apstrādi uz Jetson GPU.** `chloros_1.3.0_arm64_jp6.deb` ir izstrādāts JetPack 6 vajadzībām un izmanto JetPack 6 CUDA versiju PyTorch, tādējādi apstrāde, ieskaitot Texture Aware debayer, notiek uz Orin GPU. Pakete pati neietver CUDA: tā izmanto CUDA un cuDNN bibliotēkas, kuras instalē JetPack 6 (`sudo apt install nvidia-jetpack` attēlā, kurā tās nav). Ja nav pieejams CUDA GPU, apstrāde notiek uz CPU, un „Texture Aware” izmanto standarta debayeru.
{% endhint %}

***

## Atbalstītie „Jetson” modeļi

| Modelis                | RAM            | Apstrādes stratēģija                                     | Ieteicamais lietojums                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32–64 GB kopīgi | `GPU_PARALLEL` (2 darba procesi)                              | Maksimāla veiktspēja, lieli datu kopumi                      |
| **Jetson Orin NX**   | 8–16 GB koplietošana  | `GPU_PARALLEL` (2 darba procesi, 16 GB) / `GPU_SINGLE` (8 GB)   | Galvenais ieteikums izvietošanai lauka apstākļos              |
| **Jetson Orin Nano** | 8 GB koplietošanas atmiņa     | `GPU_SINGLE` (1 darba procesors, secīga apstrāde)                     | Ieejas līmeņa malējā skaitļošana                                 |

{% hint style="info" %}
Linux arm64 paketei ir nepieciešams **JetPack 6**, kas ir pieejams Jetson Orin sērijas ierīcēs. Vecāki modeļi (Nano, TX2, Xavier NX) nevar darbināt JetPack 6, un pašreizējā pakete tos neatbalsta.
{% endhint %}

***

## Prasības

* **JetPack 6.x** (ieteicams jaunākais)
* **NVIDIA CUDA un cuDNN** no JetPack 6 (`sudo apt install nvidia-jetpack`, ja jūsu attēlā tie nav iekļauti)
* **Maksas Chloros+ plāns** — Copper līmenis vai augstāks (nepieciešams visai CLI/SDK piekļuvei; tiek piemērots servera pusē)

## Instalēšana

```bash
# Install the arm64 .deb package (JetPack 6) -- apt, not dpkg -i, so the
# Python SDK's requirements (python3-numpy, python3-requests) come too
sudo apt install ./chloros_1.3.0_arm64_jp6.deb

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.3.0"
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # the .deb installs the SDK

# Run system diagnostics
chloros-cli selftest
```

`.deb` instalē Python SDK sistēmai `python3`, kas atbilst šai versijai. Tikai virtualenv vidē ir nepieciešams instalēt komplektā iekļauto wheel failu: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` (pilnībā bezsaistē). Vispārīga informācija par instalāciju Linux vidē, failu atrašanās vietām un problēmu novēršanu ir pieejama sadaļā [Instalācija Linux vidē](linux-installation.md).

{% hint style="info" %}
**Vairs nav nepieciešama izpakošana palaišanas brīdī.** Sākot ar versiju 1.3.0, aizmugurējā programma un CLI tiek instalētas neizpakotas mapē `/usr/lib/chloros/`, tādējādi tās vairs neizpako sevi pagaidu direktorijā katru reizi, kad tiek palaistas. Pagaidu faili joprojām automātiski tiek saglabāti `/mnt/ssd/tmp`, ja šī mape pastāv; pretējā gadījumā varat iestatīt `TMPDIR` kā ceļu uz savu NVMe (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Dinamiskā aprēķinu pielāgošana uz Jetson

### Kā tas darbojas

Palaides brīdī Chloros veic jūsu sistēmas profilēšanu:

1. **Atpazīst Jetson modeli**, izmantojot `/proc/device-tree/model`
2. **Nolasīta pieejamā koplietojamā GPU/CPU atmiņa** (Jetson izmanto vienotu atmiņu)
3. **PyTorch tiek jautāts, vai tas var izmantot CUDA** — ja ir instalēts JetPack 6 ar CUDA un cuDNN, tas ir iespējams
4. **Automātiski izvēlas apstrādes stratēģiju** (`GPU_PARALLEL`, `GPU_SINGLE` vai `CPU_PARALLEL`) un darba procesoru skaitu

Lēmumu nosaka **kopējā koplietojamā RAM**, nevis modeļa nosaukums:

* **Ja kopējā RAM ir mazāka par 12 GB**(visi 8 GB Jetson modeļi):**1 darba process — apzināta secīga apstrāde**. Atmiņas resursi ir pārāk ierobežoti vienlaicīgai vairāku darba procesu darbībai, tādēļ attēli tiek apstrādāti pa vienam. Jetson ierīcēs ar**8 GB vai mazāk**

3. pavediens pilnībā izlaiž darba vienību kopu un katra attēla apstrādi veic procesa ietvaros. Aizmugures žurnālā šī stratēģija ir nosaukta par `GPU_SINGLE`.
* **12 GB vai vairāk**(Orin NX 16 GB, AGX Orin): vienotā atmiņa atbilst `GPU_PARALLEL`, taču darba procesu skaits**Jetson ierīcēs ir ierobežots līdz 2** — gan GPU, gan darba procesu RAM, gan katra darba procesa CUDA konteksti izmanto vienu un to pašu kopīgo pūlu, tādēļ, palielinot darba procesu skaitu, pieaug risks, ka notiks kļūdas atmiņas pārpildes dēļ.
* **Nav izmantojama CUDA** (trūkst JetPack CUDA bibliotēku): `CPU_PARALLEL` ar 12 GB vai vairāk, vienlaikus izmantojot vienu attēlu, kā norādīts zemāk.

Automātisko izvēli var pārrakstīt ar vides mainīgo `CHLOROS_STRATEGY` — skatiet [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Uzvedība atkarībā no modeļa

| Jetson modelis                | Stratēģija       | Darba procesi | Izpilde                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8 GB**    | `GPU_SINGLE`   | 1       | Sekvenciāla cikla izpilde procesā (`tiled_gpu`, ja ir atmiņas trūkums) |
| **Jetson Orin NX 8 GB**      | `GPU_SINGLE`   | 1       | Sekvenciāla cilpa procesa ietvaros                     |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 2       | Vienlaicīgi darba procesi, `fused_gpu` ceļš  |
| **Jetson AGX Orin 32–64 GB** | `GPU_PARALLEL` | 2       | Vienlaicīgi darba procesi, `fused_gpu` ceļš  |

Galvenā atšķirība starp platformām ir **atmiņa**. 8 GB Jetson, ja slodze ir liela, attēlus jāapstrādā pa vienam, izmantojot atmiņas resursus taupīgu mozaīkveida pieeju, savukārt 16 GB un lielāka Orin var vienlaikus apstrādāt 2 attēlus ar GPU, izmantojot augstākas caurlaidspējas apvienoto cauruļvadu.

### GPU budžets katram modelim

Katram „Jetson“ modelim ir arī aparatūras profils, kas nosaka, cik daudz no kopējā resursu pūla apstrāde drīkst izmantot, un mēro partiju izmērus:

| Modelis | GPU budžeta maksimums | Partijas izmēra reizinātājs | Rezervēts sistēmai/ekrānam |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | 70 % | ×0,8 | 2,0 GB |
| **Jetson Orin NX** | 75 % | ×1,0 | 3,0 GB |
| **Jetson AGX Orin** | 80 % | ×1,5 | 4,0 GB |

Konstatētā RAM atmiņa pielāgo profilu: ja Jetson ziņo par **16 GB vai vairāk**, tā partijas reizinātājs tiek palielināts par ×1,2. Pamata partijas lielums pirms reizinātāju piemērošanas ir 8 attēli.

Pilnīgu informāciju par aprēķinu pielāgošanu skatiet sadaļā [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md).

***

## GPU frekvences ierobežojums funkcijai „Texture Aware” Nano un Orin Nano ierīcēs

„Texture Aware“ debayer izmanto GPU neironu tīkla secinājumus, kas var izraisīt **pārslodzes brīdinājumus**zema enerģijas patēriņa „Jetson“ modeļos (10–15 W klase) pie pilnas GPU takts frekvences. Kad nododat `--debayer texture-aware` uz**Jetson Nano vai Orin Nano**, CLI pārbauda GPU maksimālo frekvenci un ierobežo to līdz **510 MHz** (510000000), ja tā pašlaik ir augstāka:

* Ja CLI i ir iespējams rakstīt GPU frekvences sysfs mezglu, ierobežojums tiek **piemērots automātiski** un tiek parādīts apstiprinājums.
* Ja nē (nepieciešamas root tiesības), CLIs parāda precīzu komandu `sudo`, lai ierobežojumu piemērotu manuāli, pagaidītu brīdi, lai jūs to varētu izlasīt, un tad turpina — apstrāde joprojām turpinās, bet var parādīties brīdinājumi par pārslodzi.

Lai ierobežojumu piemērotu pats pirms apstrādes:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Modeli ar lielāku jaudu (Orin NX 25W, AGX Orin 60W) darbojas ar pilnu GPU ātrumu; ierobežojums netiek piemērots. Standarta debayer nekad neaktivizē ierobežojumu nevienam modelim.

{% hint style="info" %}
**„Texture Aware” uz „Jetson” vienmēr apstrādā vienu attēlu vienlaikus.** Katram darba procesam būtu nepieciešams savs CUDA konteksts (~1 GB), kā arī sava trokšņu noņemšanas modeļa kopija, ko vienotā atmiņa nespēj nodrošināt — tādēļ Jetson ierīcēs Texture Aware ceļš tiek piesaistīts vienam darba procesam ar serializētu piekļuvi GPU. Jārēķinās, ka Texture Aware jebkurā Jetson ierīcē darbosies ievērojami lēnāk nekā Standard. „Texture Aware” attiecas uz Survey3 attēliem: „LATTICE” uzņemtajiem attēliem vienmēr tiek piemērots „Standard” debayer. Ja nav izmantojama CUDA GPU, tiek izmantots „Standard”, un backend reģistrē vienu rindu `[DEBAYER] … needs a CUDA GPU …`, kurā norādīts iemesls (parādīts kopā ar `chloros-cli --verbose`).
{% endhint %}

***

## Siltuma vadība

„Jetson“ ierīcēm ir ierobežota siltuma rezerves jauda, it īpaši slēgtās telpās vai gaisā. Chloros uzrauga SoC temperatūru un automātiski ierobežo partiju izmērus:

| Temperatūra         | Darbība                                            |
| ------------------- | ------------------------------------------------- |
| **< 70 °C**          | Normāla darbība — pilna apstrādes ātruma          |
| **70 °C** (Brīdinājums) | Partijas lielums pakāpeniski samazinās (no 100 % līdz 50 % temperatūras diapazonā no 70 °C līdz 80 °C) |
| **80 °C** (Kritiska) | Agresīva jaudas ierobežošana (no 50 % līdz 0 % diapazonā no 80 °C līdz 90 °C) |
| **90 °C** (Izslēgšana) | Pilnībā aptur GPU apstrādi — nepieciešama atdzesēšana |

{% hint style="warning" %}
**Nodrošiniet atbilstošu ventilāciju un siltuma novadīšanu** ilgstošai apstrādei, īpaši slēgtos lauka korpusos vai gaisa sistēmās. Termiskais ierobežojums samazinās apstrādes caurlaidspēju, lai aizsargātu aparatūru.
{% endhint %}

***

## Atmiņas pārvaldība

Jetson ierīces izmanto **vienotu atmiņu** — GPU un CPU kopīgi izmanto vienu un to pašu fizisko RAM. Norādītā VRAM (piemēram, ~15,3 GB Orin NX 16 GB modelī) nav atsevišķa GPU atmiņa; tā ir tā pati RAM, ko izmanto operētājsistēma un visi pārējie procesi.

### Brīdinājums par apmaiņas atmiņu un ieteikumi

Pirms apstrādes uz Jetson CLI saskaita RAW attēlus jūsu ievades mapē (`.tif`, `.tiff`, `.raw`, `.dng` — JPG priekšskatījumi netiek skaitīti), aprēķina maksimālo atmiņas apjomu, kas nepieciešams procesa izpildei, un **brīdina pirms sākšanas**, ja RAM + swap, visticamāk, būs nepietiekams. Brīdinājuma virsraksts ir `LOW MEMORY WARNING - Jetson Detected`, tajā tiek parādīts attēlu skaits, RAM, pašreizējo apmaiņas atmiņu un aprēķināto maksimālo apjomu, pēc tam sniedz precīzas komandas `fallocate` / `chmod` / `mkswap` / `swapon`, kas pielāgotas jūsu projektam (nekad mazākas par 8 GB). Tā pauzē dažas sekundes, lai ziņojums neizzustu ritināšanas vēsturē, pēc tam apstrāde turpinās.**Brīdinājumā izmantotie atmiņas aprēķini:**

| Debayer režīms | Bāzes | Uz vienu attēlu |
| --- | --- | --- |
| Standarta | ~1,5 GB | ~10 MB |
| Tekstūru atbalstošais | ~2,5 GB (modelis + Python izpildes vide) | ~15 MB |

Brīdinājums parādās, ja aprēķinātais maksimālais apjoms pārsniedz RAM + apmaiņas atmiņu, atskaitot 1 GB drošības rezervi, un tiek ņemta vērā tikai **ar failiem balstīta** apmaiņas atmiņa — konfigurācija, kurā tiek izmantota tikai zram, joprojām tiks atzīmēta.

Lai manuāli pievienotu apmaiņas atmiņu (piemērs: 8 GB):

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
```

<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->


### OOM (atmiņas trūkums) apstrāde

Apstrādes laikā Chloros uzrauga GPU atmiņu un, nevis avarējot, pakāpeniski samazina veiktspēju:

1. Kad GPU atmiņas izmantojums pārsniedz **85 %**, partiju izmēri tiek preventīvi samazināti
2. Ja atmiņas trūkuma gadījums tomēr notiek, partijas izmērs tiek **samazināts uz pusi**, un katrā nākamajā OOM gadījumā — vēlreiz uz pusi; katra nākamā veiksmīgi apstrādātā partija atceļ šo ierobežojumu par vienu soli
3. Ilgstošas slodzes apstākļos apstrādes ceļš pāriet no `fused_gpu` uz atmiņas ziņā efektīvāko `tiled_gpu` ceļu, un kā pēdējais līdzeklis — uz apstrādi ar CPU

Tā kā GPU izmanto Jetson RAM, RAM un apmaiņas atmiņa joprojām ierobežo darbību — iepriekš minētais brīdinājums par apmaiņas atmiņu ir pārbaude, kurai jāpievērš uzmanība.

***

## Ieviešana praksē

### Enerģijas patēriņa apsvērumi

| Jetson modelis     | Tipiskais enerģijas patēriņš | Piezīmes                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7–15 W              | DC cilindriskais savienotājs          |
| Jetson Orin NX   | 10–25 W             | DC cilindriskais savienotājs          |
| Jetson AGX Orin  | 15–60 W             | USB-C PD vai cilindriskais savienotājs |

Plānojiet enerģijas patēriņu ilgstošai apstrādei — maksimālais enerģijas patēriņš rodas GPU intensīvā 3. pavedienā (apstrāde).

### Ieteikumi par datu uzglabāšanu

* **NVMe SSD** ir stingri ieteicams arm64 ieviešanai
* SD kartes ir pārāk lēnas apstrādei — izmantojiet tās tikai kā sākuma nesējus
* Plānojiet, ka apstrādāto rezultātu apjoms būs 2–3 reizes lielāks nekā neapstrādāto attēlu datu apjoms

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

Ja jūsu Jetson bez monitora vada LATTICE kameras vai DAQ-E gaismas sensorus, aktivizējiet backend systemd pakalpojumu **kā savam lietotājam**, lai PTP grandmaster darbotos nepārtraukti un SDK skripti atrastu darbojošos backend (vienība ir instalēta, bet pēc noklusējuma nav aktivizēta):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
chloros-cli time-sync status
```

Tad backend darbojas kā jūs: tā ierakstītie dati pieder jums, un tas izmanto jūsu `chloros-cli login`. Sīkāku informāciju, tostarp par root `chloros-backend.service` un to, kā pakete padara PTP portus 319/320 piesaistāmus bez root tiesībām, skatiet [Linux instalācijā](linux-installation.md#always-on-ptp-for-headless-hosts).

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

`chloros-cli process` beidz darbu ar rezultātu, kas nav nulle, ja izpilde, kas pieprasīja produktus, neieraksta attēlus, tādējādi systemd kļūdas statuss ir nozīmīgs uzraudzībai.

Savienojiet ar systemd taimeri, lai nodrošinātu plānoto apstrādi:

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
    --indices NDVI GNDVI OSAVI
```

### Python SDK uz „Jetson“

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "GNDVI", "OSAVI"],
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
        --indices NDVI OSAVI
    echo "Completed $name"
done
```

***

## Ieteicamās Jetson sistēmas lietošanai laukā

Lietošanai laukā un gaisā apsveriet šādas Jetson Orin NX 16 GB nesējplates opcijas:

* **Gaisā/dronos**: sistēmas ar vibrācijas izturību (MIL-STD), vieglas (mazāk nekā 300 g), ar pasīvo dzesēšanu
* **Izturīgas lauka apstākļos**: IP67/IP69K ūdensizturīgi korpusi ar PoE GigE kameru savienojamību
* **Minimālas prasības/budžeta variants**: Izstrādātāju komplekti ar papildus korpusiem

Sazinieties ar [MAPIR atbalsta dienestu](https://www.mapir.camera/community/contact), lai saņemtu konkrētus aparatūras ieteikumus jūsu izmantošanas scenārijam.

***

## Turpmākie soļi

* [Linux instalācija](linux-installation.md) — Vispārīga informācija par Linux instalāciju
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — Pilnīga aprēķinu stratēģijas rokasgrāmata
* [Apstrādes cauruļvads](../processing-architecture/processing-pipeline.md) — 4 pavedienu cauruļvada izpratne
* [CLI: komandrinda](../CLI.md) — CLI rokasgrāmata
* [API: Python SDK](../api-python-sdk.md) — SDK rokasgrāmata
* [CLI atsauces materiāls](../reference/cli-reference.md) un [SDK atsauces materiāls](../reference/sdk-reference.md) — Izsmeļošs komandu/API saraksts versijai 1.3.0
