# Apstrādes cauruļvads

Chloros1.2.0 izmanto 4-pavedienu apstrādes cauruļvadu, kas darbojas kā posmveida konveijers. Katrs pavediens apstrādā atsevišķu darba plūsmas posmu, tādējādi vienlaikus vairākus attēlus var apstrādāt dažādos posmos.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## Apstrādes procesa arhitektūra

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Katrs attēls secīgi iziet cauri visiem četriem pavedieniem. Izmantojot Chloros+ daudzpavedienu apstrādi, vairāki attēli vienlaikus atrodas dažādos pavedienos — kamēr 3. pavediens apstrādā vienu attēlu, 1. pavediens var atpazīt nākamo, 2. pavediens kalibrēt vēl vienu, bet 4. pavediens ierakstīt pabeigto attēlu diskā.

Progress tiek ziņots par katru pavedienu un tiek pārraidīts, izmantojot Server-Sent Events (backend tos publicē uz `/api/events`). „CLI” reāllaika progresa rādītājā četri posmi ir apzīmēti kā **Atklāšana, Analīze, Apstrāde, Eksportēšana**.***

## Vītņu informācija

### 1. vītne: Atklāšana

**Mērķis**: Ielādēt attēlus un atklāt kalibrēšanas mērķus.

* Nolasa attēlu failus no diska — Survey3 `.raw`+`.jpg` pāri, „LATTICE“ `.tif`/`.tiff` uzņēmumi un `.dng`
* Izgūst EXIF metadatus (GPS, kameras modelis, laika zīmogi, ekspozīcija)
* Atpazīst kalibrēšanas mērķus: ar ArUco atzīmētas mērķu ģeometrijas „LATTICE“ uzņēmumiem un klasisko paneļa detektoru „Survey3“ kalibrēšanas mērķu fotogrāfijām
* Izvade: attēla dati + metadati + mērķu atpazīšanas rezultāti

Galvenokārt I/O un CPU ierobežots pavediens.

### Vītne 2: Kalibrēšana

**Mērķis**: Aprēķina kalibrēšanas parametrus no atklātajiem mērķiem.

* Aprēķina atstarojuma kalibrēšanas koeficientus no mērķu attēliem
* Aprēķina vinjetes korekcijas parametrus
* Nosaka kalibrēšanas līknes katrai joslai
* Izvade: kalibrēšanas parametri katram attēlam

Ar procesora jaudu saistīts aprēķinu pavediens. 3. pavediens gaida tā pabeigšanu, ja ir ieslēgta atstarojuma kalibrēšana, lai koeficienti būtu gatavi, pirms tiek apstrādāts jebkurš attēls.

### Vītne 3: Apstrāde (GPU)

**Mērķis**: piemērot korekcijas un aprēķināt veģetācijas indeksus.**Šī ir aprēķinu ziņā visintensīvākā vītne.*** **Debayering**: konvertē RAW Bayer datus daudzkanālu attēlos
  * Standarta (ātrs, vidēja kvalitāte) — noklusējums, `--debayer standard`
  * Tekstūras ņemšana vērā (lēns, augstākā kvalitāte) — tikai „Chloros+”, `--debayer texture-aware`, izmanto AI/ML trokšņu noņemšanas modeli
  * LATTICE mono (M3M) uzņēmumi ir vienkanāla: tiem tiek izlaisti demosaic un baltā balansa soļi (ar vienas rindas žurnāla ziņojumu), savukārt jebkuriem M3C/Bayer attēliem tajā pašā ciklā šie soļi joprojām tiek veikti
* **Vignētas korekcija**: piemēro objektīva vignētas korekciju visam attēlam
* **Atstarošanas kalibrēšana**: piemēro kalibrēšanas koeficientus, lai pārvērstu vērtības atstarošanas vērtībās
* **Indeksu aprēķināšana**: aprēķina veģetācijas indeksus (NDVI, NDRE, GNDVI, …)
* Rezultāti: apstrādāti attēla dati, kas gatavi eksportam

Šis pavediens visvairāk gūst labumu no GPU paātrinājuma, un tieši šo pavedienu pielāgo [Dynamic Compute Adaptation](dynamic-compute-adaptation.md).

### 4. pavediens: Eksportēšana

**Mērķis**: apstrādāto attēlu ierakstīšana diskā.

* Raksta izejas failus izvēlētajā formātā — `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Iegulda metadatus izvades failos (GPS, laika zīmogi, apstrādes parametri)
* Sakārto izvadi projekta mapē kā `<camera>/<format>/<Product>_Images/` — piemēram, `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Eksportētie faili saglabā avota faila nosaukumu; mape identificē produktu.**
* Attēlu uzņemšanai ar LATTICE vienu avota kadru var sadalīt vairākos produktos (Debayered, Preview, Radiance, Reflectance, Index), katru savā produktu mapē
* Izvade: galīgie faili uz diska

Galvenokārt I/O ierobežots pavediens — SSD uzglabāšana to ievērojami uzlabo.

***

## Aizkulisēs: izpildītāji

3. pavedienā darbs ar katru attēlu tiek paralizēts, izmantojot „Python” standarta `concurrent.futures`:

* **GPU stratēģijas**(`GPU_SINGLE`, `GPU_PARALLEL`) izmanto `ProcessPoolExecutor` ar**spawn** — katrs darba process ir atsevišķs process ar savu CUDA kontekstu (`fork` pārņemtu vecāka procesa inicializēto CUDA stāvokli un sabojātu bērnu procesus)
* **`CPU_PARALLEL`** izmanto `ThreadPoolExecutor` — NumPy un OpenCV atbrīvo GIL, tādēļ pietiek ar pavedieniem
* „Jetson“ ierīces ar 8 GB vai mazāku kopīgo RAM atmiņu izpildītāju pilnībā izlaiž un apstrādā procesa ietvaros, secīgi
* „Texture Aware“ uz GPU ar mazāk nekā 7 GB VRAM arī darbojas secīgi — trokšņu noņemšanas modelis nevar ietilpt vairāk nekā vienu reizi „

Chloros“ neizmanto nekādu trešo pušu izstrādātu sadalīto sistēmu (piemēram, „Ray“). Skatīt [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md), lai uzzinātu, kā tiek izvēlēta stratēģija un darba pavedienu skaits.

***

## Sekvenciāla apstrāde pret cauruļveida apstrādi

### Brīvais režīms (sekvenciāls)

Chloros bezmaksas versijā attēli tiek apstrādāti **pa vienam**, secīgi izietot visus četrus posmus:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI bezmaksas režīmā parāda vienkāršotu progresa joslu; tās secīgās fāzes tiek atspoguļotas kā **Mērķa noteikšana**un pēc tam**Apstrāde**.

### „Chloros”+ režīms (pakāpeniska apstrāde)

Ar „Chloros”+ licenci visi četri pavedieni darbojas **vienlaikus** ar dažādiem attēliem:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI progresa josla parāda četrus posmus; uzvediet kursoru uz tās, lai redzētu katras pavedienu progresa stāvokli. „CLI” tie paši četri posmi tiek rādīti reāllaikā kā **Detecting, Analyzing, Processing, Exporting**.

{% hint style="info" %}
**Viens apzīmējums, divi nosaukumi.** „CLI” 3. posmu sauc par _Apstrāde_. Backend’a premium režīma progresa plūsma — tā, ko attēlo GUI progresa josla — to pašu posmu apzīmē kā _Kalibrēšana_. Tie ir viens un tas pats pavediens, kas veic vienu un to pašu darbu (3. pavediens: debayer, korekcijas, indeksi).
{% endhint %}

{% hint style="success" %}
**Pipeline apstrāde ar „Chloros”** var būt 3–5 reizes ātrāka nekā secīga apstrāde, atkarībā no jūsu aparatūras un datu kopas lieluma. Ātruma pieaugums ir vislielākais sistēmās ar ātriem GPU un SSD diskiem.
{% endhint %}

***

## 4. pavediena eksportēšanas gaita

Eksportēšanas pavedienam ir sava gaita, kuru varat pārbaudīt atsevišķi:**CLI:**

```bash
chloros-cli export-status
```

**SDK:**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Apstrāde ir pabeigta, kad 4. pavediens sasniedz 100 %.

{% hint style="info" %}
**Apstrāde, kuras laikā netiek ierakstīti attēli, ir neveiksmīga.**Veiksmīgas apstrādes gadījumā `chloros-cli process` ziņo, cik daudz attēlu produktu tika ierakstīti (`Image products written: N`). Ja tika pieprasīti produkti, bet**neviens**netika ierakstīts — tikai `project.json` un `calibration_data.json` —, tad „CLI” izdrukā `Processing finished but wrote no image products.` un**iziet ar rezultātu, kas nav nulle**, norādot projekta mapi un parastos iemeslus (ievades mape netika atpazīta kā uzņemšanas mape — pārbaudiet izkārtojumu un `--input-level` — vai arī visi pieprasītie produkti nebija piemērojami šīm kamerām). Skripti var paļauties uz iziešanas kodu.
{% endhint %}

***

## Saistība ar dinamisko aprēķinu pielāgošanu

[Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) galvenokārt ietekmē **

3. pavedienu (apstrāde)**:

* **`GPU_PARALLEL`**: 3. pavediens vienlaikus apstrādā vairākus attēlus ar GPU, izmantojot `fused_gpu` cauruļvadu
* **`GPU_SINGLE`**: 3. pavediens serializē piekļuvi GPU ar semaforu, kamēr darba procesi pārklājas ar I/O, izmantojot `fused_gpu` vai atmiņai efektīvo `tiled_gpu` cauruļvadu
* **`CPU_PARALLEL`**: 3. pavediens izmanto uz CPU balstītu apstrādi ar daudzpavedienu paralēlismu

3. pavediena GPU atmiņas piešķiršana palielinās arī tad, kad 1. un 2. pavedieni pabeidz darbu — skatiet [Dinamiskā GPU atmiņas piešķiršana](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Turpmākie soļi

* [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) — kā „Chloros” izvēlas optimālo stratēģiju jūsu aparatūrai
* [NVIDIA Jetson rokasgrāmata](../linux/nvidia-jetson-guide.md) — Platformai specifiska apstrādes plūsmas darbība uz Jetson
* [Apstrādes uzraudzība](../processing-images-gui/monitoring-the-processing.md) — Apstrādes gaitu uzraudzība ar grafisko lietotāja interfeisu
* [„CLI” atsauces materiāls](../reference/cli-reference.md) — `process`, `export-status`, iziešanas kodi un izvades izkārtojums
