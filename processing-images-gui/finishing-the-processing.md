# Apstrādes pabeigšana

Kad Chloros ir pabeidzis apstrādi, ir pienācis laiks pārskatīt rezultātus, pārbaudīt izvades kvalitāti un sagatavot apstrādātos attēlus izmantošanai jūsu darba plūsmā. Šī lapa palīdzēs jums iziet pēdējos soļus un veikt turpmākās darbības.

## Apstrādes pabeigšanas indikatori

Kad apstrāde veiksmīgi pabeigta, redzēsiet vairākus indikatorus:

* ✅ **Progresa josla**: sasniedz 100 % pabeigšanu
* ✅ **Debug Log**: parāda pēdējās `[RUN-SUMMARY]` rindas ar skaitļiem (attēli, kameru grupas, mērķi, kalibrētie attēli, ierakstītie faili)
* ✅ **Sākt pogu**: Atkal kļūst pieejama (gatava nākamajai apstrādes kārtai)
* ✅ **Izvades faili**: visi apstrādātie attēli ir saglabāti projekta izvades struktūrā (zemāk)

{% hint style="warning" %}
**Apstrādes kārta, kuras laikā netiek saglabāti attēli, tiek uzskatīta par neveiksmīgu.** Ja esat pieprasījis attēlu produktus, bet apstrādes cikls neierakstīja nevienu, Chloros to ziņo kā kļūdu — `[RUN-SUMMARY]` žurnāla nosaukumā norāda iespējamo cēloni (nekas nav importēts, nav atklāts mērķis vai visi pieprasītie produkti ir izlaisti kā nepiemēroti). CLI ekvivalents beidzas ar rezultātu, kas nav nulle. Apzināti veikta darbība, kurā tiek apstrādāti tikai metadati (visi eksporta produkti izslēgti, nav indeksu), joprojām tiek uzskatīta par veiksmīgu. Skatīt [CLI atsauci](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## Apstrādāto attēlu atrašana

### Izvades mapes atvēršana

1. Noklikšķiniet uz **galvenās izvēlnes** ikonas „<img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">” (kreisajā augšējā stūrī)
2. Izvēlieties **„Atvērt projekta mapi””**

3. Atvērsies failu pārlūks, kurā redzama projekta mape
4. Atrodiet savu projektu pēc nosaukuma

### Izvades koks

Produkti tiek saglabāti **projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per selected index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

* **Kameras mape**: `LATT-<sensor>-<lens>-F<filter>` kamerai LATTICE (atbilstoši uzņēmuma EXIF datiem `Model`), `<model>_<filter>` attēlam Survey3 (piem., `Survey3N_RGN`). Divām kamerām, kurām ir viens sensors un filtrs, bet atšķiras objektīvs, tiek izveidotas atsevišķas mapes — atšķiras vinjetēšana, redzes lauks un izkliede.
* **Formāta mape**: atbilst jūsu eksporta formāta iestatījumiem — `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32` līdz TIFF (32-bitu, procentos). Spožums vienmēr ir float32 un vienmēr atrodas zem `tiff32`.
* **Produkta mapes**:
  * `Reflectance_Calibrated_Images/` — kalibrēta atstarošanās
  * `Debayered_Images/` — lineāri debayered (LATTICE)
  * `Preview_Images/` — priekšskatījums ekrānā (LATTICE)
  * `Radiance_Images/` — spektrālā starojuma intensitāte float32 formātā, W/m²/sr/nm (LATTICE multispektrāls)
  * `Vignette_Corrected_Images/` **vai** `Sensor_Response_Images/` — nekalibrēts rezerves risinājums kadriem bez atstarojuma atsauces; katrā apstrādes ciklā pastāv tieši viens no šiem diviem, ko nosaka iestatījums „Vignette correction“
  * `<INDEX>_Index_Images/` — viena mape katram izvēlētajam indeksam (piem., `NDVI_Index_Images`)

{% hint style="info" %}
**Katram eksportētajam produktam tiek saglabāts AVOTA faila nosaukums.**`capture_..._raw.tif` starojuma eksporta nosaukums joprojām ir `capture_..._raw.tif` — tas vienkārši atrodas mapē `tiff32/Radiance_Images/`.**Produktu identificē mape, nevis faila nosaukums**, tāpēc, meklējot `*radiance*.tif`, nekas netiks atrasts; tā vietā meklējiet pēc mapes.
{% endhint %}



<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-… camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images) and tiff32 (Radiance_Images) subfolders visible -->### Cik daudz failu vajadzētu būt?

Neskaitiet pēc formulas — izvades failu skaits ir atkarīgs no tā, kuri produkti tika aktivizēti un kuri attiecas uz katru kameru (piemēram, RGB kamerām netiek aprēķināts starojums/atstarošanās). Precīzs skaits ir norādīts žurnālā: pēdējā `[RUN-SUMMARY]` rinda precīzi norāda, cik daudz failu tika ierakstīti, un paskaidrojošās rindas izskaidro visu, kas tika izlaists.

***

## Apstrādāto attēlu pārskatīšana

### Ātrā priekšskatīšana failu pārlūkā

**Windows iebūvēta priekšskatīšana:**

1. Atveriet produkta mapi (piem., `tiff16/Reflectance_Calibrated_Images/`)
2. Izvēlieties attēla failu
3. Priekšskatījums parādās Windows Explorer priekšskatījuma logā
4. Izmantojiet bultu taustiņus, lai pārlūkotu attēlus

### Priekšskatījums ārējos attēlu skatītājos

**Ieteicamās skatīšanas programmas:*** **QGIS** — bezmaksas ĢIS programmatūra (vispiemērotākā ģeoreferencētai multispektrālajai analīzei)
* **IrfanView** — ātra, viegla attēlu skatīšanas programma (atbalsta TIFF)
* **Adobe Photoshop** — profesionāla rediģēšana (atbalsta TIFF)
* **GIMP** — bezmaksas alternatīva programma Photoshop
* **Windows Photos** — pamata skatīšana (var neatbalstīt 16 bitu TIFF)

### Priekšskatīšana Chloros attēlu skatītājā

Izmantojiet Chloros iebūvēto attēlu skatītāju, lai veiktu paplašinātu attēlu apskati:

1. Noklikšķiniet uz attēla sīktēla failu pārlūkā
2. Attēls atveras galvenajā priekšskatīšanas laukā
3. Noklikšķiniet uz **Image Viewer** cilnes <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kreisajā sānjoslā
4. Izmantojiet [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) interaktīvai analīzei

Sīkākas instrukcijas skatiet [Attēlu skatītājā](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Reflektances pikseļu vērtību nolasīšana (GIS / Pix4D / skripti)

Atstarošanās tiek saglabāta kā vesels skaitlis DN, un **DN, kas nozīmē ρ = 1,0, ir atkarīgs no avota kameras**:

| Avots          | ρ = 1,0 ir | Kā to noteikt                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēta XMP birka `Chloros:PixelScale=32768` |
| Survey3         | **65535** (apgriezts pie ρ 1,0)     | Nav `Chloros:*` XMP marķējumu — šī trūkums ir signāls |

**Nolasiet `Chloros:PixelScale` tagu un daliet ar to**, nevis pieņemiet vispārēju vērtību 65535 — LATTICE atstarojuma dalīšana ar 65535 klusi samazina katru vērtību uz pusi. Viens robežgadījums pēc dizaina nav ar mērogu: 8-bitu avota uzņemums, kas ierakstīts kā 8-bitu izvade, tiek nogriezts, nevis pārmērogots, un apzināti nesaņem mēroga tagu — tā vietā, lai to dalītu, veiciet atkārtotu eksportu 16-bitu vai 32-bitu formātā. Pilnu aprakstu skatiet sadaļā [Izvades attēlu formāti](../output-image-formats.md).***

## Metadati, kas tiek pārnesti uz eksportētajiem failiem

Katrs produkts saglabā avota attēla **GPS bloku**un tā**EXIF apakš-IFD**, tādējādi
eksportētajā failā tiek iekļauti `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` un
`CameraSerialNumber`, kā arī ģeoreferencēšanu.

{% hint style="warning" %}
**Ja ortomosaīka izveidojas ar absurdiem mērogiem, vispirms pārbaudiet `FocalLength`.**
Pix4D aprēķina attālumu starp zemes paraugiem, pamatojoties uz fokusa attālumu un augstumu. Bez šīs atzīmes programma
izmanto ārkārtīgi nepareizu mērogu — vienā lidojumā ar 49 uzņēmumiem 411 m × 160 m
apelsīnu dārzs tika rekonstruēts kā 47,8 km × 13 km, radot 455 megapikseļu ortomosaiku, kurā galvenokārt bija
tukša telpa. Lēna mozaīkas veidošana un negaidīti liels faila izmērs ir šīs problēmas simptomi, nevis atsevišķas
problēmas.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

Netiek kopētas *visas* birkas. IFD0 strukturālās birkas tiek apzināti atstātas ārpus kopijas (to
kopēšana sabojā LATTICE izvadi), un `ExifImageWidth` / `ExifImageHeight` tiek izslēgtas,
jo tās apraksta sākotnējo uzņemumu — eksportam, kura izmērs tika mainīts, citādi
tiktu piešķirti izmēri, kas ir pretrunā ar paša rastra izmēriem.

***

## Debug Log pārskatīšana

### Brīdinājumu vai kļūdu pārbaude

1. Atveriet cilni **Debug Log** (<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">)
2. Pārskatiet ziņojumus
3. Meklējiet dzeltenos brīdinājumus vai sarkanās kļūdas
4. Izlasiet `[RUN-SUMMARY]` rindas un visus padomus
5. Sazinieties ar MAPIR atbalsta dienestu, lai saņemtu palīdzību

### Žurnāla saglabāšana

Lai saglabātu apstrādes ierakstu vai nosūtītu to MAPIR atbalsta dienestam:

1. Noklikšķiniet uz pogas **„Kopēt”**vai**„Lejupielādēt”**

2. Saglabājiet kā teksta failu projekta mapē
3. Pievienojiet projekta dokumentācijai
4. Ja rodas problēmas, nosūtiet to MAPIR atbalsta dienestam

***

## Bieži sastopamas izvades problēmas un to risinājumi

### Problēma: Trūkstoši izvades faili

**Iespējamie cēloņi:**

* Produkts nav piemērots šai kamerai (piemēram, starojums/atstarošanās RGB kamerām — tas norādīts žurnālā)
* Trūka nepieciešamās atsauces (piem., atstarošanas rādītājs bez mērķa un bez `.daq` lejupvērstā starojuma)
* Projekta iestatījumos bija atspējota produkta eksportēšanas izvēles rūtiņa
* Eksportēšanas laikā beidzās diska vieta

**Risinājumi:**

1. Pārbaudiet `[RUN-SUMMARY]` norādes un `[EXPORT-CHECK]` rindas atkļūdošanas žurnālā — tajās ir izskaidroti izlaišanas iemesli katrai kamerai atsevišķi
2. Pārbaudiet eksportējamo produktu izvēles rūtiņas [Projekta iestatījumos](adjusting-project-settings.md)
3. Pārbaudiet, vai bija pietiekami daudz diska vietas
4. Pēc cēloņa novēršanas veiciet apstrādi no jauna

### Problēma: Tumšas vai gaišas malas (joprojām redzama vinjetēšana)

**Iespējamie cēloņi:**

* Vinjetēšanas korekcija ir atspējota
* Kamera/objektīvs nav iekļauts Chloros profilu datu bāzē
* Pārmērīga vinjetēšana, kas pārsniedz korekcijas iespējas

**Risinājumi:**

1. Pārbaudiet, vai projekta iestatījumos ir ieslēgta vinjetēšanas korekcija
2. Pārbaudiet, vai kameras modelis ir pareizi atpazīts
3. Ja vinjetēšana joprojām pastāv, sazinieties ar MAPIR atbalsta dienestu

### Problēma: Nepareizas krāsas vai vērtības

**Iespējamie cēloņi:**

* Nav atklāti kalibrēšanas mērķi
* Izvēlēts nepareizs kalibrēšanas mērķa modelis
* Atspoguļojuma kalibrēšana ir atspējota
* Sliktas kvalitātes mērķa attēli

**Risinājumi:**

1. Pārbaudiet, vai ir ieslēgta atspoguļojuma kalibrēšana
2. Pārbaudiet ziņojumus „Mērķis atrasts” atkļūdošanas žurnālā
3. Pārbaudiet mērķu attēlu kvalitāti
4. Veiciet atkārtotu apstrādi, atzīmējot pareizos mērķus

### Problēma: NDVI vērtības šķiet nepareizas

**Paredzamie NDVI diapazoni:*** **Ūdens, akmeņi, augsne**: no -0,1 līdz 0,2
* **Reta/neskaidra veģetācija**: no 0,2 līdz 0,4
* **Vidēja veģetācija**: no 0,4 līdz 0,6
* **Veselīga, blīva veģetācija**: no 0,6 līdz 0,9**Ja vērtības atrodas ārpus šiem diapazoniem:**

1. Pārbaudiet, vai ir veikta atstarošanas kalibrēšana
2. Pārbaudiet, vai ir iekļauts gaismas sensora protokols
3. Pārbaudiet, vai ir atpazīti kalibrēšanas mērķi
4. Pārliecinieties, ka ir atpazīts pareizais kameras modelis
5. Pārskatiet mērķa attēlu uzņemšanas laiku un apstākļus
6. Ja indeksus aprēķināt paši, izmantojot atstarojuma failus, pārliecinieties, ka esat dalījuši ar faila `Chloros:PixelScale` (skatīt iepriekš)

***

## Apstrādāto attēlu izmantošana

### Fotogrammetrijai / ortomosaikas izveidei

**Ieteicamā darba plūsma:**

1.**Importējiet kalibrētos atstarojuma attēlus** fotogrammetrijas programmās:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Saglabājiet EXIF metadatus**: nodrošiniet, ka GPS dati tiek saglabāti ģeotagēšanai
3. **Kalibrētas darba plūsmas**: izmantojiet reflektances attēlus zinātniskai precizitātei — LATTICE reflektances attēlos ir iekļautas XMP kalibrēšanas metadatas, kuras nolasa Pix4D
4. **Apstrādājiet indeksa mozaīkas**: Izveidojiet NDVI ortomozaīkas no atsevišķiem indeksa attēliem
5. **Eksportējiet ģeoreferencētos GeoTIFF**: izmantošanai ĢIS lietojumprogrammās

### ĢIS analīzei

**Ieteicamā darba plūsma:**

1.**Ielādējiet QGIS, ArcGIS vai līdzīgā programmā**

2.**Izmantojiet 16-bitu TIFF** atstarojuma attēlus daudzjoslu analīzei (daliet ar faila `Chloros:PixelScale`)
3. **Izmantojiet indeksa attēlus** (NDVI, NDRE) kā gatavus veģetācijas slāņus
4. **Rastra kalkulators**: apvienojiet joslas pielāgotai analīzei
5. **Eksportēšana**: izveidojiet klasifikācijas kartes, izmaiņu noteikšanu, veģetācijas veselības kartes

### Tiešai analīzei / ziņojumu sagatavošanai

**Ieteicamā darba plūsma:**

1.**Izmantojiet indeksa attēlus ar LUT krāsām** vizuāliem ziņojumiem
2. **Iegūstiet statistiku**: vidējais NDVI rādītājs katram laukam/parcelim
3. **Laika rindas**: salīdziniet indeksus starp vairākām sesijām
4. **Izveidojiet atskaites**: iekļaujiet kartes, statistiku un vizualizācijas***

## Arhivēšana un dublējumi

### Ieteicamā dublējumu stratēģija

**Ko saglabāt:*** ✅ **Oriģinālie RAW/JPG attēli vai LATTICE neapstrādātie attēli** — arhivējiet atsevišķā diskā/mākonī; neapstrādātie attēli ir apstrādes procesa avots, un no tiem var atjaunot visu pārējo
* ✅ **`.daq` / `.csv` gaismas sensoru faili** — nepieciešami, lai vēlāk atkārtoti aprēķinātu atstarojumu
* ✅ **Apstrādātie rezultāti** — saglabājiet kalibrētus attēlus un indeksus
* ✅ **Projekta mape** (`project.json` un saistītie faili) — satur visus iestatījumus atkārtotai apstrādei, ja nepieciešams
* ✅ **Debug Log** — dokumentē apstrādes detaļas
* ✅ **Kalibrēšanas mērķa attēli** - Pārbaudei un atkārtotai apstrādei**Ieteikumi datu glabāšanai:*** **Tūlītēja dublējuma izveide**: Ārējais cietais disks
* **Ilgtermiņa arhivēšana**: Mākoņglabātuve (Google Drive, Dropbox utt.)
* **Kritiskie dati**: Saglabājiet 2–3 kopijas dažādās vietās***

## Nākamie apstrādes cikli

### Projekta iestatījumu atkārtota izmantošana

Ja nākotnē apstrādāsiet līdzīgus datu kopumus:

1. **Saglabājiet projekta veidni** (ja vēl neesat to izdarījuši)
2. **Izveidojiet jaunu projektu**, izmantojot saglabāto veidni
3. **Importējiet jaunus attēlus**

4.**Apstrādājiet**, izmantojot identiskus iestatījumus, lai nodrošinātu konsekvenci

### Vairāku sesiju partiju apstrāde

Vairākām sesijām/datu kopām:**

1. variants: GUI — vairāki projekti**

* Izveidojiet atsevišķu projektu katrai sesijai
* Izmantojiet vienotus veidnes iestatījumus
* Apstrādājiet pa vienam

**

2. variants: Chloros CLI (tikai Chloros+)**

* Automatizējiet partiju apstrādi
* Apstrādājiet vairākas mapes, izmantojot skriptus
* Skatīt [CLI dokumentāciju](../CLI.md) un [CLI atsauci](../reference/cli-reference.md)

**

3. variants: Python SDK (tikai Chloros+)**

* Programmatiska vadība
* Integrācija ar analīzes procesiem
* Skatīt [API dokumentāciju](../api-python-sdk.md) un [SDK atsauci](../reference/sdk-reference.md)

***

## Pēcapstrādes problēmu novēršana

### Atkārtota apstrāde ar atšķirīgiem iestatījumiem

Ja rezultāti nav apmierinoši:

1. Saglabājiet sākotnējos attēlus (nekad tos nedzēsiet)
2. Atveriet to pašu projektu Chloros
3. Pielāgojiet iestatījumus paneļā „Project Settings”
4. Veiciet apstrādi atkārtoti — rezultāti tiek saglabāti tajās pašās produktu mapēs, tādējādi faili ar tādu pašu nosaukumu no iepriekšējās apstrādes tiek aizstāti

### Attēlu apakškopa apstrāde

Lai atkārtoti apstrādātu tikai konkrētus attēlus:

1. Izveidojiet jaunu projektu
2. Importējiet tikai tos attēlus, kuriem nepieciešama atkārtota apstrāde
3. Izmantojiet to pašu iestatījumu veidni
4. Apstrādājiet mazāku datu kopu

### Palīdzības saņemšana

Ja rodas problēmas:

* 📧 **E-pasts**: info@mapir.camera (pievienojiet atkļūdošanas žurnālu)
* 🌐 **Atbalsts**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **FAQ**: [Bieži uzdotie jautājumi](../faq.md)
* 📖 **Dokumentācija**: [Chloros rokasgrāmata](../)***

## Kopsavilkums: Pilnīga darba plūsma

Tagad esat pabeidzis pilnu Chloros apstrādes darba plūsmu:

1. ✅ **Izveidots projekts** — skatiet [Projekti](../projects.md)
2. ✅ **Pievienoti faili** — skatiet [Failu pievienošana](adding-files-to-a-project.md)
3. ✅ **Pielāgoti iestatījumi** – skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
4. ✅ **Atzīmēti mērķi** – skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
5. ✅ **Sākta apstrāde** — skatiet [Apstrādes sākšana](starting-the-processing.md)
6. ✅ **Progresa uzraudzība** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)
7. ✅ **Pārskatīti rezultāti** — Šī lapa**Jūsu kalibrētie, atstarojuma korekciju veikušie multispektrālie attēli ir gatavi analīzei!**

***

## Papildu resursi

### Papildu funkcijas

* [**Attēlu skatītājs**](../image-viewer-gui/opening-an-image-full-screen.md) — Interaktīva vizualizācija un analīze
* [**Indeksu/LUT izmēģinājumu vide**](../image-viewer-gui/index-lut-sandbox.md) — Pielāgotu indeksu testēšana
* [**Multispektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — pilnīga indeksu atsauces informācija

### Automatizācija un integrācija

* [**CLI dokumentācija**](../CLI.md) - Komandrindas pakotņu apstrāde
* [**Python SDK**](../api-python-sdk.md) – Automatizācija ar programmēšanas palīdzību
* [**Chloros+ funkcijas**](../#chloros) — paplašinātas apstrādes iespējas

### Atbalsts un apmācība

* [**Bieži uzdotie jautājumi**](../faq.md) - Atbildes uz bieži uzdotajiem jautājumiem
* [**Kalibrēšanas mērķi**](../calibration-targets.md) - Reflektances kalibrēšanas izpratne
* [**Atbalstītās kameras**](../supported-cameras.md) - Savietojamā aparatūra
