# Apstrādes cauruļvads

Chloros 1.3.0 izmanto 4 pavedienu apstrādes cauruļvadu, kas darbojas kā posmu konveijers. Katrs pavediens apstrādā atsevišķu darba plūsmas posmu, tādējādi vienlaikus vairākus attēlus var apstrādāt dažādos posmos.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## Apstrādes procesa arhitektūra

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Katrs attēls secīgi iziet cauri visiem četriem pavedieniem. Izmantojot „Chloros+“ daudzpavedienu apstrādi, vairāki attēli vienlaikus atrodas dažādos pavedienos — kamēr 3. pavediens apstrādā vienu attēlu, 1. pavediens var veikt nākamā attēla atpazīšanu, 2. pavediens — vēl viena attēla kalibrēšanu, bet 4. pavediens — gatavā attēla ierakstīšanu diskā.

Progress tiek ziņots par katru pavedienu un tiek pārraidīts, izmantojot Server-Sent Events (backend tos publicē uz `/api/events`). Komandrindas interfeisā (CLI) reāllaika progresa rādījumā četri posmi ir apzīmēti kā **Atklāšana, Analīze, Apstrāde, Eksportēšana**.***

## Pavedienu informācija

### 1. pavediens: Atklāšana

**Mērķis**: Ielādēt attēlus un atklāt kalibrēšanas mērķus.

* Nolasa attēlu failus no diska — Survey3 `.raw`+`.jpg` pāri, LATTICE `.tif`/`.tiff` uzņēmumi un `.dng`
* Izgūst EXIF metadatus (GPS, kameras modelis, laika zīmogi, ekspozīcija)
* Atpazīst kalibrēšanas mērķus: ar ArUco marķētas mērķu ģeometrijas „LATTICE“ uzņēmumiem un klasisko paneļa detektoru Survey3 kalibrēšanas mērķu fotogrāfijām
* Izvade: attēla dati + metadati + mērķu atpazīšanas rezultāti

Galvenokārt I/O un CPU resursu ietilpīgs pavediens.

### Vītne 2: Kalibrēšana

**Mērķis**: Aprēķināt kalibrēšanas parametrus no atklātajiem mērķiem.

* Aprēķina atstarojuma kalibrēšanas koeficientus no mērķu attēliem
* Aprēķina vinjetes korekcijas parametrus
* Nosaka kalibrēšanas līknes katram viļņu diapazonam
* Izvade: kalibrēšanas parametri katram attēlam

Ar procesora jaudu saistīts aprēķinu pavediens. Veicot Survey3 vai jauktu apstrādi, 3. pavediens gaida, kamēr tiek veikta atstarojuma kalibrēšana, tādējādi nodrošinot, ka koeficienti ir gatavi, pirms tiek apstrādāts jebkurš attēls. Veicot tikai LATTICE apstrādi, 3. pavediens darbojas atklāšanas laikā: katrs kadrs sākas, tiklīdz tā mērķa atskaites punkts ir galīgi noteikts. Kadrs izmanto laika ziņā tuvāko mērķi abās pusēs, tādēļ tas gaida, līdz ir ieskenēti visi kameras kadri, kas laika ziņā ir tuvāki nekā pašreizējais tuvākais mērķis. Rezultāti ir tie paši, tikai ātrāk.

### 3. pavediens: Apstrāde (GPU)

**Mērķis**: piemērot korekcijas un aprēķināt veģetācijas indeksus.**Šis ir aprēķinu ziņā visintensīvākais pavediens.*** **Debayering**: konvertē RAW Bayer datus daudzkanālu attēlos
  * Standarta (ātrs, vidēja kvalitāte) — noklusējums, `--debayer standard`
  * Tekstūras ņemšana vērā (lēns, augstākā kvalitāte) — tikai „Chloros+”, `--debayer texture-aware`, izmanto AI/ML trokšņu noņemšanas modeli
  * LATTICE mono (M3M) uzņēmumi ir vienkanāla: tiem tiek izlaisti demosaic un baltā balansā soļi (ar vienrindas žurnāla ziņojumu), savukārt jebkuriem M3C/Bayer attēliem tajā pašā apstrādes ciklā šie soļi joprojām tiek veikti
* **Vignēšanas korekcija**: piemēro objektīva vignēšanas korekciju visam attēlam
* **Atstarošanas kalibrēšana**: piemēro kalibrēšanas koeficientus, lai pārvērstu vērtības atstarošanas vērtībās
* **Indeksu aprēķināšana**: aprēķina veģetācijas indeksus (NDVI, NDRE, GNDVI, …)
* Rezultāti: apstrādāti attēla dati, kas gatavi eksportam

Šis pavediens visvairāk gūst labumu no GPU paātrinājuma, un tieši to pielāgo [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md).

### 4. pavediens: Eksportēšana

**Mērķis**: ierakstīt apstrādātos attēlus diskā.

* Ieraksta izvades failus izvēlētajā formātā — `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Ievieto metadatus izvades failos (GPS, laika zīmogi, apstrādes parametri) — ieskaitot `Chloros:*` apstrādes tagus, kuri, sākot ar versiju 1.3.0, tiek ierakstīti katrā apstrādes režīmā, gan Survey3, gan „LATTICE”
* Sakārto izvadi projekta mapē kā `<camera>/<format>/<Product>_Images/` — piemēram, `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Eksportētie faili saglabā avota faila nosaukumu; mape identificē produktu.**
* Attiecībā uz LATTICE uzņēmumiem viens avota kadrs var tikt sadalīts vairākos produktos (Debayered, Preview, Radiance, Reflectance, Index), katrs savā produktu mapē. Indeksa produkti tiek aprēķināti no atspoguļojuma produkta, ko izveidojusi apstrāde (ja tāda nav, tad no cita produkta, ko izveidojusi apstrāde, piemēram, starojuma). Daudzspektrālo M3C kameru starojuma un atstarojuma produktiem var tikt piešķirta arī pikseļu līmeņa ierobežošanas maska `<format>/clamp_masks/` (skatīt [Izvades attēlu formāti](../output-image-formats.md#clamp-masks))
* Izejas dati: galīgie faili uz diska

Galvenokārt I/O ierobežots pavediens — SSD uzglabāšana to ievērojami uzlabo.

***

## Aizkulisēs: izpildītāji

3. pavedienā darbs ar katru attēlu tiek paralizēts šādi:

* **GPU stratēģijas**(`GPU_SINGLE`, `GPU_PARALLEL`) izmanto**darbinieku procesu** kopu, katrs no kuriem ir atsevišķs operētājsistēmas process ar savu CUDA kontekstu. Chloross katru darba procesu sāk, atkārtoti palaistot savu instalēto aizmugures programmu — nevis izmantojot Python `multiprocessing`, kura darba procesu palaišana nedarbojas iepakotajā lietojumprogrammā
* **`CPU_PARALLEL`** izmanto pavedienu kopu (Python funkciju `ThreadPoolExecutor`) — NumPy un OpenCV atbrīvo GIL, tādēļ ar pavedieniem pietiek
* „Jetson“ ierīces ar 8 GB vai mazāku kopīgo RAM atmiņu izpildītāju pilnībā izlaiž un apstrādā procesa ietvaros, secīgi
* „Texture Aware“ uz GPU ar mazāk nekā 7 GB VRAM arī darbojas secīgi — trokšņu noņemšanas modelis nevar ietilpt vairāk nekā vienu reizi

Chloros neizmanto nekādu trešo pušu izstrādātu izkliedēto sistēmu (piemēram, „Ray“). Skatīt [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md), lai uzzinātu, kā tiek izvēlēta stratēģija un darba pavedienu skaits.

***

## Sekvenciālā apstrāde salīdzinājumā ar cauruļveida apstrādi

### Brīvais režīms (sekvenciāls)

Chloros bezmaksas versijā attēli tiek apstrādāti **pa vienam**, secīgi izietot visus četrus posmus:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI bezmaksas režīmā parāda vienkāršotu progresa joslu; tās secīgās fāzes tiek norādītas kā **Mērķa atpazīšana**un pēc tam**Apstrāde**.

### „Chloros+“ režīms (pipeline)

Izmantojot „Chloros+“ licenci, visi četri pavedieni darbojas **vienlaikus** ar dažādiem attēliem:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI progresa josla parāda četrus posmus; novietojiet kursoru virs tās, lai redzētu katra pavediena progresu. Komandrindā (CLI) tie paši četri posmi tiek parādīti reāllaikā kā **Detecting, Analyzing, Processing, Exporting**.

{% hint style="info" %}
**Viens apzīmējums, divi nosaukumi.** CLI šo 3. posmu sauc par _Apstrāde_. Aizmugurējās sistēmas premium režīma progresa plūsma — tā, ko attēlo GUI progresa josla — šo pašu posmu apzīmē kā _Kalibrēšana_. Tie ir viens un tas pats pavediens, kas veic vienu un to pašu darbu (3. pavediens: debayer, korekcijas, indeksi).
{% endhint %}

{% hint style="success" %}
**Chloros+** izmantojamā cauruļveida apstrāde var būt 3–5 reizes ātrāka nekā secīgā apstrāde, atkarībā no jūsu aparatūras un datu kopas lieluma. Ātruma pieaugums ir vislielākais sistēmās ar ātriem GPU un SSD diskiem.
{% endhint %}

***

## 4. pavediena eksportēšanas gaita

Eksportēšanas pavedienam ir sava gaita, kuru varat pārbaudīt atsevišķi:**Komandrindas interfeiss (CLI):**

```bash
chloros-cli export-status
```

**Programmatūras izstrādes komplekts (SDK):**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

Apstrāde ir pabeigta, kad 4. pavediens sasniedz 100 %.

{% hint style="info" %}
**Vērsiens, kurā netiek ierakstīti attēli, tiek uzskatīts par neveiksmīgu.**Veiksmīgas apstrādes gadījumā `chloros-cli process` ziņo, cik daudz attēlu produktu tika ierakstīti (`Image products written: N`). Ja tika pieprasīti produkti, bet**neviens**netika ierakstīts — tikai `project.json` un `calibration_data.json` — CLI izvada `Processing finished but wrote no image products.` un**iziet ar rezultātu, kas nav nulle**, norādot projekta mapi un parastos iemeslus (ievades mape netika atpazīta kā uzņemšanas mape — pārbaudiet izkārtojumu un `--input-level` — vai arī neviens no pieprasītajiem produktiem nebija piemērots šīm kamerām). Skripti var paļauties uz iziešanas kodu.
{% endhint %}

***

## Saistība ar dinamisko aprēķinu pielāgošanu

[Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) galvenokārt ietekmē **

3. pavedienu (apstrāde)**:

* **`GPU_PARALLEL`**: 3. pavediens vienlaikus apstrādā vairākus attēlus ar GPU, izmantojot `fused_gpu` cauruļvadu
* **`GPU_SINGLE`**: 3. pavediens serializē piekļuvi GPU ar semaforu, kamēr darba procesi pārklājas I/O, izmantojot `fused_gpu` vai atmiņai efektīvo `tiled_gpu` cauruļvadu
* **`CPU_PARALLEL`**: 3. pavediens izmanto uz CPU balstītu apstrādi ar daudzpavedienu paralēlismu

3. pavediena GPU atmiņas piešķīrums palielinās arī tad, kad 1. un 2. pavedieni pabeidz darbu — skatiet [Dinamiskā GPU atmiņas piešķiršana](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Turpmākie soļi

* [Dinamiskā aprēķinu pielāgošana](dynamic-compute-adaptation.md) — Kā Chloros izvēlas optimālo stratēģiju jūsu aparatūrai
* [„NVIDIA Jetson” rokasgrāmata](../linux/nvidia-jetson-guide.md) — Platformas specifiska cauruļvada darbība uz „Jetson”
* [Apstrādes uzraudzība](../processing-images-gui/monitoring-the-processing.md) — Apstrādes gaitu uzraudzība ar grafisko lietotāja saskarni
* [Komandrindas interfeisa (CLI) atsauces materiāls](../reference/cli-reference.md) — `process`, `export-status`, iziešanas kodi un izvades izkārtojums
