# Apstrādes cauruļvads

Chloros 1.1.0 izmanto 4-pavedienu apstrādes cauruļvadu, kas darbojas kā posmu konveijers. Katrs pavedienu apstrādā atsevišķu apstrādes darba plūsmas posmu, ļaujot vienlaikus apstrādāt vairākus attēlus dažādos posmos.

***

## Cauruļvada arhitektūra

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Katrs attēls secīgi iziet cauri visiem četriem pavedieniem. Izmantojot Chloros+ daudzpavedienu apstrādi, vairāki attēli vienlaikus var atrasties dažādos pavedienos — kamēr 3. pavedienā tiek apstrādāts viens attēls, 1. pavedienā var tikt atklāts nākamais, 2. pavedienā var tikt kalibrēts vēl viens, bet 4. pavedienā var tikt ierakstīts iepriekš apstrādātais attēls uz diska.

***

## Pavedienu informācija

### 1. pavediens: Atklāšana

**Mērķis**: Ielādēt attēlus un atklāt kalibrēšanas mērķus.

* Nolasa attēlu failus no diska (RAW, JPG)
* Izgūst EXIF metadatus (GPS, kameras modelis, laika zīmogi, ekspozīcija)
* Atpazīst ArUco kalibrēšanas mērķus atzīmētos mērķa attēlos
* Izvade: attēla dati + metadati + mērķu atpazīšanas rezultāti

Šis ir galvenokārt I/O un CPU-saistīts pavediens.

### 2. pavediens: Kalibrēšana

**Mērķis**: Aprēķināt kalibrēšanas parametrus no atpazītajiem mērķiem.

* Aprēķina atstarošanas kalibrēšanas koeficientus no mērķu attēliem
* Aprēķina vinjetes korekcijas parametrus
* Nosaka kalibrēšanas līknes katram diapazonam
* Rezultāti: kalibrēšanas parametri katram attēlam

Šis ir ar procesoru saistīts aprēķinu pavediens.

### 3. pavediens: Apstrāde (GPU)

**Mērķis**: Piemērot korekcijas un aprēķināt veģetācijas indeksus.**Šis ir visintensīvākais aprēķinu pavediens.*** **Debayering**: Konvertē RAW Bayer modeļa datus daudzkanālu attēlos
  * Standarta (ātrs, vidēja kvalitāte) — noklusējums
  * Tekstūras apzināts (lēns, augstākā kvalitāte) — tikai Chloros+, izmanto AI/ML trokšņu noņemšanu
* **Vignette korekcija**: Piemēro objektīva vignette korekciju visam attēlam
* **Atstarošanas kalibrēšana**: piemēro kalibrēšanas koeficientus, lai konvertētu atstarošanas vērtības
* **Indeksu aprēķināšana**: aprēķina veģetācijas indeksus (NDVI, NDRE, GNDVI utt.)
* Rezultāti: apstrādāti attēla dati, kas gatavi eksportam

Šis pavediens visvairāk gūst labumu no GPU paātrinājuma. [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) sistēma galvenokārt optimizē šī pavediena darbību.

### 4. pavediens: Eksportēšana

**Mērķis**: Apstrādātos attēlus ierakstīt diskā.

* Ieraksta izvades failus izvēlētajā formātā (TIFF 16-bit, TIFF 32-bit %, PNG, JPG)
* Ievieto EXIF metadatus izvades failos (GPS, laika zīmogi, apstrādes parametri)
* Sakārto izvadi kameras modeļu apakšmapēs
* Izvade: galīgie faili uz diska

Šis galvenokārt ir I/O-saistīts pavediens. SSD uzglabāšana ievērojami uzlabo 4. pavediena veiktspēju.

***

## Sekvenciālā apstrāde pret cauruļveida apstrādi

### Brīvais režīms (sekvenciāls)

Chloros bezmaksas versijā attēli tiek apstrādāti **pa vienam**, secīgi visos četros posmos:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI progresa josla parāda 2 posmus: mērķa noteikšanu un apstrādi.

### Chloros+ režīms (pipeline)

Ar Chloros+ licenci visi četri pavedieni darbojas **vienlaikus** ar dažādiem attēliem:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI progresa josla parāda 4 posmus: atklāšana, analīze, kalibrēšana, eksportēšana. Pārvietojiet kursoru pār progresa joslu, lai redzētu katra pavediena progresu.

{% hint style="success" %}
**Pipeline apstrāde ar Chloros+** var būt 3–5 reizes ātrāka nekā secīga apstrāde, atkarībā no jūsu aparatūras un datu kopas lieluma. Ātruma pieaugums ir vislielākais sistēmās ar ātriem GPU un SSD.
{% endhint %}

***

## 4. pavediena eksportēšanas gaita

Chloros 1.1.0 versijā eksportēšanas pavedienam (4. pavedienam) ir sava atsevišķa gaita. Jūs varat atsevišķi uzraudzīt eksportēšanas gaitu:**CLI:**
```bash
chloros-cli export-status
```

**SDK:**
```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Apstrāde ir pabeigta, kad 4. pavediens sasniedz 100 %.

***

## Saistība ar dinamisko aprēķinu pielāgošanu

[Dinamiskās aprēķinu pielāgošanas](dynamic-compute-adaptation.md) sistēma galvenokārt ietekmē **

3. pavedienu (apstrāde)**:

* **`GPU_PARALLEL`** stratēģija: 3. pavediens vienlaikus apstrādā vairākus attēlus, izmantojot GPU un `fused_gpu` cauruļvadu
* **`GPU_SINGLE`** stratēģija: 3. pavediens apstrādā vienu attēlu vienlaikus, izmantojot atmiņas ziņā efektīvo `tiled_gpu` cauruļvadu
* **`CPU_PARALLEL`** stratēģija: 3. pavediens izmanto CPU balstītu apstrādi ar daudzpavedienu paralēlismu

3. pavediena GPU atmiņas sadale arī mainās dinamiski, kad 1. un 2. pavedieni pabeidz darbu — skatiet [Dinamiskā GPU atmiņas sadale](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Turpmākie soļi

* [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) — Kā Chloros izvēlas optimālo stratēģiju jūsu aparatūrai
* [NVIDIA Jetson rokasgrāmata](../linux/nvidia-jetson-guide.md) — Platformas specifiska cauruļvada darbība uz Jetson
* [Apstrādes uzraudzība](../processing-images-gui/monitoring-the-processing.md) — GUI progresa uzraudzība
