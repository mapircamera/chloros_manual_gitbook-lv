# Apstrādes pabeigšana

Kad Chloros ir pabeidzis apstrādi, ir pienācis laiks pārskatīt rezultātus, pārbaudīt izvades kvalitāti un sagatavot apstrādātos attēlus izmantošanai jūsu darba plūsmā. Šī lapa palīdzēs jums iziet pēdējos soļus un veikt turpmākās darbības.

## Apstrādes pabeigšanas indikatori

Kad apstrāde ir veiksmīgi pabeigta, jūs redzēsiet vairākus indikatorus:

* ✅ **Progresa josla**: sasniedz 100 % pabeigšanu
* ✅ **Debug Log**: parāda pēdējās `[RUN-SUMMARY]` rindas ar skaitļiem (attēli, kameru grupas, mērķi, kalibrētie attēli, ierakstītie faili)
* ✅ **Sākšanas poga**: atkal kļūst pieejama (gatava nākamajam apstrādes ciklam)
* ✅ **Izvades faili**: visi apstrādātie attēli ir saglabāti projekta izvades struktūrā (zemāk)

{% hint style="warning" %}
**Apstrādes cikls, kurā netiek saglabāti attēli, tiek uzskatīts par neveiksmīgu.** Ja esat pieprasījis attēlu produktus, bet apstrādes cikls neierakstīja nevienu, Chloros to ziņo kā kļūdu — `[RUN-SUMMARY]` norāda uz iespējamo cēloni žurnāla nosaukumā (nekas nav importēts, nav atklāts mērķis vai visi pieprasītie produkti ir izlaisti kā nepiemēroti). CLI ekvivalents beidzas ar rezultātu, kas nav nulle. Apzināts izpildes cikls, kurā tiek apstrādāti tikai metadati (visi eksporta produkti ir atspējoti, nav indeksu), joprojām tiek uzskatīts par veiksmīgu. Skatīt [CLI atsauci](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## Apstrādāto attēlu atrašana

### Izvades mapes atvēršana

1. Noklikšķiniet uz **Galvenās izvēlnes** ikonas <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">
(kreisajā augšējā stūrī)
2. Izvēlieties **"Atvērt projekta mapi"**

3. Atvērsies failu pārlūks, kurā būs redzama projekta mape
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

* **Kameras mape**: `LATT-<sensor>-<lens>-F<filter>` kamerai LATTICE (atbilstoši uzņēmuma EXIF datiem `Model`), `<model>_<filter>` — Survey3 (piem., `Survey3N_RGN`). Divām kamerām, kurām ir viens sensors un filtrs, bet atšķirīgs objektīvs, tiek izveidotas atsevišķas mapes — atšķiras vinjetēšana, redzes lauks un izkliede.
* **Formāta mape**: atbilst jūsu eksporta formāta iestatījumiem — `tiff16`, `tiff8`, `png8`, `jpg8`, vai `tiff32` TIFF formātam (32 bitu, procentos). Spilgtums vienmēr ir float32 formātā un vienmēr atrodas mapē `tiff32`.
* **Produkta mapes**:
  * `Reflectance_Calibrated_Images/` — kalibrēta atstarošanās
  * `Debayered_Images/` — lineāri debayered (LATTICE): noņemts tumšais nobīdes līmenis un veikta demosaicēšana, bez flat field vai unmix, sensora sākotnējā orientācijā
  * `Preview_Images/` — attēla priekšskatījums (LATTICE)
  * `Radiance_Images/` — spektrālā starojuma intensitāte float32 formātā, W/m²/sr/nm (LATTICE multispektrālais)
  * `Vignette_Corrected_Images/` **vai** `Sensor_Response_Images/` — nekalibrēts rezerves variants kadriem bez atstarojuma atsauces; katrā darbības ciklā pastāv tieši viens no šiem diviem, ko izvēlas ar iestatījumu „Vignette correction“
  * `<INDEX>_Index_Images/` — viena mape katram izvēlētajam indeksam (piem., `NDVI_Index_Images`)
* **`clamp_masks/`** (jaunums versijā 1.3.0, tikai LATTICE multispektrālajām M3C kamerām): parādās formāta mapē blakus produktu mapēm, ja joslas atdalīšanas procesā negatīvās vērtības starojuma vai atstarošanas produktā tiek nogrieztas līdz nullei. Tajā ir viena maska uz pikseli katram ietekmētajam produktam, piemēram, `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif`. Maskas nekad neatrodas produkta mapē, tādēļ produkta mapi var nodot fotogrammetrijas programmatūrai tādā veidā, kāda tā ir. Skatīt [Clamp masks](../output-image-formats.md#clamp-masks).

{% hint style="info" %}
**Katrs eksportētais produkts saglabā AVOTA faila nosaukumu.**`capture_..._raw.tif` starojuma eksporta produkts joprojām saucas `capture_..._raw.tif` — tas vienkārši atrodas `tiff32/Radiance_Images/`.**Produkts tiek identificēts pēc mapes, nevis pēc faila nosaukuma**, tāpēc, meklējot `*radiance*.tif`, nekas netiks atrasts; tā vietā jāmeklē pēc mapes nosaukuma.
{% endhint %}

<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-M3C multispectral camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images, clamp_masks) and tiff32 (Radiance_Images, clamp_masks) subfolders visible -->


### Cik daudz failu vajadzētu būt?

Neskaitiet pēc formulas — izvades failu skaits ir atkarīgs no tā, kuri produkti tika aktivizēti un kuri attiecas uz katru kameru (piemēram, RGB kamerām netiek aprēķināts starojums/atstarošanās). Oficiālais skaits ir norādīts žurnālā: pēdējā `[RUN-SUMMARY]` rinda precīzi norāda, cik daudz failu tika ierakstīti, un paskaidrojumu rindas izskaidro visu, kas tika izlaists.

***

## Apstrādāto attēlu pārskatīšana

### Ātra priekšskatīšana failu pārlūkā

**Iebūvēta priekšskatīšana Windows:**

1. Atveriet produkta mapi (piem., `tiff16/Reflectance_Calibrated_Images/`)
2. Izvēlieties attēla failu
3. Priekšskatījums parādās Windows pārlūka priekšskatījuma logā
4. Izmantojiet bultu taustiņus, lai pārlūkotu attēlus

### Priekšskatījums ārējās attēlu skatītājprogrammās

**Ieteicamās skatītājprogrammas:*** **QGIS** — bezmaksas ĢIS programmatūra (vislabāk piemērota ģeoreferencētai multispektrālajai analīzei)
* **IrfanView** — ātra, viegla attēlu skatītāja programma (atbalsta TIFF formātu)
* **Adobe Photoshop** — profesionāla rediģēšana (TIFF atbalsts)
* **GIMP** — bezmaksas alternatīva programma Photoshop
* **Windows Photos** — pamata skatīšana (var neatbalstīt 16 bitu TIFF)

### Priekšskatīšana Chloros attēlu skatītājā

Izmantojiet „Chloros “ iebūvēto attēlu skatītāju, lai veiktu padziļinātu vizualizāciju:

1. Noklikšķiniet uz attēla sīktēla failu pārlūkā
2. Attēls atveras galvenajā priekšskatīšanas zonā
3. Noklikšķiniet uz cilnes **Attēlu skatītājs** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
kreisajā sānjoslā
4. Interaktīvai analīzei izmantojiet [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md)

Sīkākas instrukcijas skatiet [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Reflektances pikseļu vērtību nolasīšana (GIS / Pix4D / Skripti)

Atstarošanas koeficients tiek saglabāts kā vesels skaitlis DN, un **DN, kas atbilst ρ = 1,0, ir atkarīgs no avota kameras**:

| Avots          | ρ = 1,0 ir | Kā noteikt                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēta XMP birka `Chloros:PixelScale=32768` |
| Survey3          | **65535** (nogriezts pie ρ 1,0)     | Nav `Chloros:PixelScale` tag — šī trūkums ir pazīme (Survey3 produktiem ir citi `Chloros:*` apstrādes tagi) |

**Nolasiet `Chloros:PixelScale` marķējumu un daliet ar to**, nevis pieņemiet, ka visur ir 65535 — dalot LATTICE atstarojumu ar 65535, katra vērtība tiek nemanāmi samazināta uz pusi. Atstarošanas fails, kas izveidots pirms versijas 1.3.0 un ierakstīts kā 8 bitu, tika apgriezts, nevis pārskalots, un tam nav mēroga tagu: to vajadzētu eksportēt atkārtoti 16 bitu vai 32 bitu formātā, nevis dalīt. Pilnu informāciju skatiet sadaļā [Izvades attēlu formāti](../output-image-formats.md).

***

## Metadati, kas tiek pārnesti uz eksportētajiem failiem

Katrs produkts saglabā avota uzņēmuma **GPS bloku**un tā**EXIF apakš-IFD**, tādējādi
eksportā tiek iekļauti `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` un
`CameraSerialNumber`, kā arī ģeoreferencēšanu.

{% hint style="warning" %}
**Ja ortomosaīka iznāk absurdi lielā mērogā, vispirms pārbaudiet `FocalLength`.**
Pix4D aprēķina zemes parauga attālumu, izmantojot fokusa attālumu un augstumu. Bez šīs atzīmes programma
izmanto ārkārtīgi nepareizu mērogu — vienā lidojumā ar 49 uzņēmumiem 411 m × 160 m
apelsīnu dārzs tika rekonstruēts kā 47,8 km × 13 km, radot 455 megapikseļu ortomosaiku, kurā galvenokārt bija
tukša telpa. Lēna mozaīkas veidošana un negaidīti liels faila izmērs ir šīs problēmas simptomi, nevis atsevišķas
problēmas.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

Netiek kopētas *visas* birkas. IFD0 strukturālās birkas tiek apzināti atstātas (to kopēšana
bojā LATTICE izvadi), un `ExifImageWidth` / `ExifImageHeight` tiek izslēgtas,
jo tās apraksta sākotnējo uzņemumu — eksportam, kura izmērs tika mainīts, citādi
tiktu piešķirti izmēri, kas ir pretrunā ar paša rastra izmēriem.

Chloros arī raksta savas `Chloros:*` XMP tagus, kas apraksta, kas tika veikts ar katru produktu
(piemēram, `Chloros:ProcessingLevel`, `Chloros:CalibrationApplied`,
`Chloros:VignetteCorrected`). Sākot ar versiju 1.3.0, Survey3 produkti tos iekļauj katrā apstrādes
režīmā; indeksa produkti apraksta pašu indeksu (`Chloros:ProcessingLevel=index`,
formulu un to, vai tā tika aprēķināta, izmantojot atstarojumu vai starojumu), nevis atkārto
avota neapstrādāto datu tagus; turklāt produkti mapēs, kuru nosaukumi ir garāki par 260 rakstzīmēm, saglabā arī savus metadatus. Skatīt [Izvades attēlu formāti](../output-image-formats.md#index-products).

***

## Debug žurnāla pārskatīšana

### Brīdinājumu vai kļūdu pārbaude

1. Atveriet cilni **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

2. Pārskatiet ziņojumus
3. Meklējiet dzeltenos brīdinājumus vai sarkanās kļūdas
4. Izlasiet `[RUN-SUMMARY]` rindas un jebkādus padomus
5. Sazinieties ar MAPIR atbalsta dienestu, lai saņemtu palīdzību

### Žurnāla saglabāšana

Lai saglabātu apstrādes ierakstu vai nosūtītu to MAPIR atbalsta dienestam:

1. Noklikšķiniet uz pogas **"Kopēt"**vai**"Lejupielādēt"**

2. Saglabājiet kā teksta failu projekta mapē
3. Pievienojiet projekta dokumentācijai
4. Ja rodas problēmas, nosūtiet to MAPIR atbalsta dienestam

Lai problēmu nosūtītu tieši MAPIR, izmantojiet kļūdas ikonu (**Ziņot par kļūdu**) cilnes „Debug Log” augšdaļā — skatiet [Kļūdas ziņošana no cilnes „Log”](monitoring-the-processing.md#reporting-a-bug-from-the-log-tab).

***

## Bieži sastopamas izvades problēmas un to risinājumi

### Problēma: Trūkstoši izvades faili

**Iespējamie cēloņi:**

* Produkts nav piemērots šai kamerai (piem., starojuma intensitāte/atstarošanās RGB-kamerām — tas ir norādīts žurnālā)
* Trūka nepieciešamā atsauce (piem., atstarošanas rādītāji bez mērķa un bez `.daq` lejupvērstā starojuma)
* Projekta iestatījumos bija atspējota produkta eksportēšanas izvēles rūtiņa
* Eksportēšanas laikā beidzās diska vieta

**Risinājumi:**

1. Pārbaudiet `[RUN-SUMMARY]` norādes un `[EXPORT-CHECK]` rindas atkļūdošanas žurnālā — tajās ir paskaidrots, kādas kameras tiek izlaistas
2. Pārbaudiet produkta eksporta izvēles rūtiņas [Projekta iestatījumos](adjusting-project-settings.md)
3. Pārbaudiet, vai bija pietiekami daudz diska vietas
4. Pēc cēloņa novēršanas veiciet apstrādi no jauna

### Problēma: Tumšas vai gaišas malas (joprojām redzama vinjetēšana)

**Iespējamie cēloņi:**

* Vignēšanas korekcija ir atspējota
* Kamera/objektīvs nav iekļauts Chloros profilu datu bāzē
* Pārmērīga vignēšana, kas pārsniedz korekcijas iespējas

**Risinājumi:**

1. Pārbaudiet, vai projekta iestatījumos ir ieslēgta vignēšanas korekcija
2. Pārbaudiet, vai kameras modelis ir pareizi atpazīts
3. Ja vignēšana joprojām pastāv, sazinieties ar MAPIR atbalsta dienestu

### Problēma: Nepareizas krāsas vai vērtības

**Iespējamie cēloņi:**

* Nav atklāti kalibrēšanas mērķi
* Izvēlēts nepareizs kalibrēšanas mērķa modelis
* Atstarošanas kalibrēšana ir atspējota
* Zemas kvalitātes mērķa attēli

**Risinājumi:**

1. Pārbaudiet, vai atstarošanas kalibrēšana ir ieslēgta
2. Pārbaudiet ziņojumus „Mērķis atrasts” (Target found) atkļūdošanas žurnālā (Debug Log)
3. Pārbaudiet mērķa attēla kvalitāti
4. Veiciet atkārtotu apstrādi, atzīmējot pareizos mērķus

### Problēma: NDVI vērtības šķiet nepareizas

**Paredzamie NDVI diapazoni:*** **Ūdens, akmeņi, augsne**: no -0,1 līdz 0,2
* **Reta/neveselīga veģetācija**: no 0,2 līdz 0,4
* **Vidēja veģetācija**: no 0,4 līdz 0,6
* **Veselīga, blīva veģetācija**: no 0,6 līdz 0,9**Ja vērtības atrodas ārpus šiem diapazoniem:**

1. Pārbaudiet, vai tika piemērota atstarojuma kalibrēšana
2. Pārbaudiet, vai tika iekļauts gaismas sensora žurnāls
3. Pārbaudiet, vai tika atpazīti kalibrēšanas mērķi
4. Pārliecinieties, ka tika atpazīts pareizais kameras modelis
5. Pārskatiet mērķu attēlu uzņemšanas laiku un apstākļus
6. Ja indeksus aprēķināt paši, izmantojot atstarojuma failus, pārliecinieties, ka esat dalījuši ar faila `Chloros:PixelScale` (skatīt iepriekš)

***

## Apstrādāto attēlu izmantošana

### Fotogrammetrijai / ortomosaīkas izveidei

**Ieteicamā darba plūsma:**

1.**Importējiet kalibrētus atstarojuma attēlus** fotogrammetrijas programmās:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Saglabājiet EXIF metadatus**: nodrošiniet, ka GPS dati tiek saglabāti ģeotagēšanai
3. **Kalibrētas darba plūsmas**: izmantojiet atstarojuma attēlus zinātniskai precizitātei — LATTICE atstarojuma attēlos ir iekļautas XMP kalibrēšanas birkas, kuras nolasa Pix4D
4. **Apstrādājiet indeksa mozaīkas**: izveidojiet NDVI ortomozaīkas no atsevišķiem indeksa attēliem
5. **Eksportējiet ģeoreferencētus GeoTIFF failus**: izmantošanai ĢIS lietojumprogrammās

### ĢIS analīzei

**Ieteicamā darba plūsma:**

1.**Ielādējiet QGIS, ArcGIS vai līdzīgā programmā**

2.**Izmantojiet 16 bitu TIFF** atstarojuma attēlus daudzjoslu analīzei (daliet ar faila `Chloros:PixelScale`)
3. **Izmantojiet indeksa attēlus** (NDVI, NDRE) kā gatavus izmantošanai veģetācijas slāņus
4. **Rastra kalkulators**: apvienojiet joslas pielāgotai analīzei
5. **Eksportējiet**: izveidojiet klasifikācijas kartes, izmaiņu noteikšanu, veģetācijas veselības kartes

### Tiešai analīzei / ziņojumu sagatavošanai

**Ieteicamā darba kārtība:**

1.**Izmantojiet indeksa attēlus ar LUT krāsām** vizuāliem ziņojumiem
2. **Iegūstiet statistiku**: vidējais NDVI rādītājs katram laukam/parcelim
3. **Laika rindas**: salīdziniet indeksus starp vairākām sesijām
4. **Izveidojiet pārskatus**: iekļaujiet kartes, statistiku un vizualizācijas***

## Arhivēšana un dublējumi

### Ieteicamā dublējumu stratēģija

**Ko saglabāt:*** ✅ **Oriģinālie RAW/JPG attēli vai LATTICE neapstrādātie attēli** — arhivējiet atsevišķā diskā/mākonī; neapstrādātie attēli ir apstrādes procesa avots, un no tiem var atjaunot visu pārējo
* ✅ **`.daq` / `.csv` gaismas sensoru faili** — nepieciešami, lai vēlāk atkārtoti aprēķinātu atstarojumu
* ✅ **Apstrādātie rezultāti** — saglabājiet kalibrētos attēlus un indeksus
* ✅ **Projekta mape** (`project.json` un saistītie faili) — satur visus iestatījumus, kas nepieciešami atkārtotai apstrādei, ja nepieciešams
* ✅ **Debug Log** — dokumentē apstrādes detaļas
* ✅ **Kalibrēšanas mērķa attēli** — pārbaudei un atkārtotai apstrādei**Ieteikumi datu uzglabāšanai:*** **Tūlītēja dublējuma izveide**: Ārējais cietais disks
* **Ilgtermiņa arhīvs**: Mākoņuzglabāšana (Google Drive, Dropbox utt.)
* **Kritiskie dati**: Saglabājiet 2–3 kopijas dažādās vietās***

## Nākamie apstrādes cikli

### Projekta iestatījumu atkārtota izmantošana

Ja nākotnē tiks apstrādāti līdzīgi datu kopumi:

1. **Saglabājiet projekta veidni** (ja tas vēl nav izdarīts)
2. **Izveidojiet jaunu projektu**, izmantojot saglabāto veidni
3. **Importējiet jaunus attēlus**

4.**Apstrādājiet**ar identiskiem iestatījumiem, lai nodrošinātu konsekvenci

### Vairāku sesiju apstrāde partijās

Vairākām sesijām/datu kopām:**

1. variants: GUI — vairāki projekti**

* Izveidojiet atsevišķu projektu katrai sesijai
* Izmantojiet vienotus veidnes iestatījumus
* Apstrādājiet pa vienam

**

2. variants: Chloros CLI (tikai Chloros+)**

* Automatizējiet partiju apstrādi
* Apstrādājiet vairākas mapes, izmantojot skriptus
* Skatiet [CLI dokumentāciju](../CLI.md) un [CLI atsauci](../reference/cli-reference.md)

**

3. variants: Python SDK (tikai Chloros+)**

* Programmatiska kontrole
* Integrācija ar analīzes procesiem
* Skatīt [API dokumentāciju](../api-python-sdk.md) un [SDK atsauci](../reference/sdk-reference.md)

***

## Pēcapstrādes problēmu novēršana

### Atkārtota apstrāde ar atšķirīgiem iestatījumiem

Ja rezultāti nav apmierinoši:

1. Saglabājiet sākotnējos attēlus (nekad tos nedzēsiet)
2. Atveriet to pašu projektu programmā Chloros
3. Pielāgojiet iestatījumus paneļā „Projekta iestatījumi“
4. Veiciet apstrādi atkārtoti — rezultāti tiek saglabāti tajās pašās produktu mapēs, tādējādi faili ar tādu pašu nosaukumu no iepriekšējās apstrādes tiek aizstāti

### Attēlu apakškopas apstrāde

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
* 📖 **Dokumentācija**: [Chloros rokasgrāmata](../)

***

## Kopsavilkums: pilnīga darba plūsma

Tagad esat pabeiguši pilnu Chloros apstrādes darba plūsmu:

1. ✅ **Izveidots projekts** — skatiet [Projekti](../projects.md)
2. ✅ **Pievienoti faili** — skatiet [Failu pievienošana](adding-files-to-a-project.md)
3. ✅ **Pielāgoti iestatījumi** — skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
4. ✅ **Atzīmēti mērķi** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
5. ✅ **Sākta apstrāde** — skatiet [Apstrādes sākšana](starting-the-processing.md)
6. ✅ **Uzraudzīts apstrādes gaita** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)
7. ✅ **Pārskatīti rezultāti** — Šī lapa**Jūsu kalibrētie, atstarojuma korekciju veikušie multispektrālie attēli ir gatavi analīzei!**

***

## Papildu resursi

### Papildu funkcijas

* [**Attēlu skatītājs**](../image-viewer-gui/opening-an-image-full-screen.md) — interaktīva vizualizācija un analīze
* [**Indeksu/LUT izmēģinājumu vide**](../image-viewer-gui/index-lut-sandbox.md) — pielāgotu indeksu testēšana
* [**Multispektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — pilnīga indeksu atsauce

### Automatizācija un integrācija

* [**CLI dokumentācija**](../CLI.md) — apstrāde pa daļām, izmantojot komandrindu
* [**Python SDK**](../api-python-sdk.md) — automatizācija, izmantojot programmēšanu
* [**„Chloros+“ funkcijas**](../#chloros) — papildu apstrādes iespējas

### Atbalsts un apmācība

* [**Bieži uzdotie jautājumi**](../faq.md) — atbildes uz bieži uzdotajiem jautājumiem
* [**Kalibrēšanas mērķi**](../calibration-targets.md) — atstarojuma kalibrēšanas izpratne
* [**Atbalstītās kameras**](../supported-cameras.md) — saderīgā aparatūra
