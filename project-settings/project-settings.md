# Projekta iestatījumi

Projekta iestatījumi <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> sānu josla Chloros ļauj konfigurēt visus attēlu apstrādes aspektus, kalibrēšanas mērķa noteikšanu, multispektrālo indeksu aprēķinus un eksportēšanas opcijas jūsu projektam. Šie iestatījumi tiek saglabāti kopā ar jūsu projektu un var tikt saglabāti kā veidnes atkārtotai izmantošanai vairākos projektos.

## Piekļuve projekta iestatījumiem

Lai piekļūtu projekta iestatījumiem:

1. Atveriet projektu Chloros
2. Noklikšķiniet uz cilnes **Projekta iestatījumi**  <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> cilni kreisajā sānjoslā
3. Iestatījumu panelī tiks parādītas visas pieejamās konfigurācijas opcijas, kas sakārtotas pēc kategorijām

***

## Mērķa noteikšana

Šie iestatījumi kontrolē, kā Chloros atklāj un apstrādā kalibrēšanas mērķus jūsu attēlos.

### Minimālā kalibrēšanas parauga platība (px)

* **Tips**: skaitlis
* **Diapazons**: no 0 līdz 10 000 pikseļiem
* **Noklusējums**: 25 pikseļi
* **Apraksts**: nosaka minimālo platību (pikseļos), kas nepieciešama, lai atklātais reģions tiktu uzskatīts par derīgu kalibrēšanas mērķa paraugu. Mazākas vērtības atklās mazākus mērķus, bet var palielināt kļūdainu pozitīvo rezultātu skaitu. Lielākas vērtības prasa lielākus, skaidrākus mērķa reģionus atklāšanai.
* **Kad pielāgot**:
  * Palieliniet, ja saņemat kļūdainus atklājumus uz maziem attēlu artefaktiem.
  * Samaziniet, ja jūsu kalibrēšanas mērķi attēlos izskatās mazi un netiek atklāti.

### Minimālā mērķu grupēšana (0–100)

* **Tips**: skaitlis
* **Diapazons**: no 0 līdz 100
* **Noklusējums**: 60
* **Apraksts**: kontrolē klasterizācijas slieksni, lai grupētu līdzīgas krāsas reģionus, atklājot kalibrēšanas mērķus. Augstākas vērtības prasa, lai tiktu grupētas vairāk līdzīgas krāsas, kas rezultātā rada konservatīvāku mērķu atklāšanu. Zemākas vērtības ļauj lielāku krāsu variāciju mērķa grupā.
* **Kad pielāgot**:
  * Palieliniet, ja kalibrēšanas mērķi tiek sadalīti vairākās atklāšanās
  * Samaziniet, ja kalibrēšanas mērķi ar krāsu variāciju netiek pilnībā atklāti

***

## Apstrāde

Šie iestatījumi kontrolē, kā Chloros apstrādā un kalibrē jūsu attēlus.

### Vignette korekcija

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Piemēro vinjetes korekciju, lai kompensētu objektīva tumšāko zonu attēlu malās. Vinjetēšana ir izplatīta optiska parādība, kad attēla stūri un malas izskatās tumšākas nekā centrs objektīva īpašību dēļ.
* **Kad atspējot**: Atspējot tikai tad, ja jūsu kameras/objektīva kombinācija jau ir piemērojusi vinjetes korekciju vai ja vēlaties manuāli koriģēt vinjetēšanu pēcapstrādē.

### Atstarošanas kalibrēšana / balansa iestatīšana

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Ieslēgts (atzīmēts)
* **Apraksts**: Iespējo automātisku atstarošanas kalibrēšanu, izmantojot attēlos atklātos kalibrēšanas mērķus. Tas normalizē atstarošanas vērtības visā datu kopā un nodrošina konsekventus mērījumus neatkarīgi no apgaismojuma apstākļiem.
* **Kad atspējot**: Atspējot tikai tad, ja vēlaties apstrādāt neapstrādātus, nekalibrētus attēlus vai ja izmantojat citu kalibrēšanas darba plūsmu.

### Debayer metode

* **Tips**: Izvēles saraksts
* **Opcijas**:
  * Augsta kvalitāte (ātrāka) - Pašlaik vienīgā pieejamā opcija
* **Noklusējums**: Augsta kvalitāte (ātrāka)
* **Apraksts**: Izvēlas demosaicing algoritmu, ko izmanto, lai pārvērstu neapstrādātus Bayer modeļa sensora datus pilnkrāsu attēlos. Metode „Augsta kvalitāte (ātrāka)” nodrošina optimālu līdzsvaru starp apstrādes ātrumu un attēla kvalitāti.
* **Piezīme**: Papildu debayer metodes var tikt pievienotas nākotnes Chloros versijās.

### Minimālais pārkalibrēšanas intervāls

* **Tips**: skaitlis
* **Diapazons**: no 0 līdz 3600 sekundēm
* **Noklusējums**: 0 sekundes
* **Apraksts**: Nosaka minimālo laika intervālu (sekundēs) starp kalibrēšanas mērķu izmantošanu. Ja iestatīts uz 0, Chloros izmantos katru atklāto kalibrēšanas mērķi. Ja iestatīts uz augstāku vērtību, Chloros izmantos tikai kalibrēšanas mērķus, kas atdalīti vismaz par šo sekunžu skaitu, samazinot apstrādes laiku datu kopām ar biežām kalibrēšanas mērķu uzņemšanām.
* **Kad pielāgot**:
  * Iestatiet uz 0, lai nodrošinātu maksimālu kalibrēšanas precizitāti, ja apgaismojuma apstākļi mainās.
  * Palieliniet (piemēram, līdz 60–300 sekundēm), lai nodrošinātu ātrāku apstrādi, ja apgaismojums ir nemainīgs un jums ir bieži kalibrēšanas mērķu attēli.

### Gaismas sensora laika zonas nobīde

* **Tips**: skaitlis
* **Diapazons**: no -12 līdz +12 stundām
* **Noklusējums**: 0 stundas
* **Apraksts**: Norāda laika zonas nobīdi (stundās no UTC) gaismas sensora datu laika zīmogiem. To izmanto, apstrādājot PPK (pēc apstrādes kinemātiskos) datu failus, lai nodrošinātu pareizu laika sinhronizāciju starp attēlu uzņemšanu un GPS datiem.
* **Kad pielāgot**: Iestatiet to atbilstoši savam vietējam laika zonu nobīdei, ja jūsu PPK dati izmanto vietējo laiku, nevis UTC. Piemēram:
  * Klusā okeāna laiks: -8 vai -7 (atkarībā no vasaras laika)
  * Austrumu laiks: -5 vai -4 (atkarībā no vasaras laika)
  * Centrāleiropas laiks: +1 vai +2 (atkarībā no vasaras laika)

### Piemērot PPK korekcijas

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Atvienots (neatzīmēts)
* **Apraksts**: Ļauj izmantot pēcapstrādes kinemātiskās (PPK) korekcijas no MAPIR DAQ reģistratoriem, kas satur GPS (GNSS). Ja šī funkcija ir ieslēgta, Chloros izmantos jebkādus .daq žurnāla failus, kas satur ekspozīcijas pinu datus jūsu projekta direktorijā, un piemēros precīzas ģeolokācijas korekcijas jūsu attēliem.
* **Prasība**: .daq žurnāla failam ar ekspozīcijas pinu ierakstiem jābūt jūsu projekta direktorijā
* **Kad ieslēgt**: Ieteicams vienmēr ieslēgt PPK korekciju, ja jūsu .daq žurnāla failā ir ekspozīcijas atgriezeniskās saites ieraksti.

### Ekspozīcijas pīns 1

* **Tips**: Izvēles saraksts
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta funkcija &quot;Piemērot PPK korekcijas&quot; UN ekspozīcijas dati ir pieejami pīnam 1
* **Opcijas**:
  * Projektā atklātie kameru modeļu nosaukumi
  * &quot;Nelietot&quot; — ignorēt šo ekspozīcijas pīnu
* **Noklusējums**: Automātiski izvēlēts atbilstoši projekta konfigurācijai
* **Apraksts**: piešķir konkrētu kameru ekspozīcijas kontaktdakšai 1 PPK laika sinhronizācijai. Ekspozīcijas kontaktdakša reģistrē precīzu laiku, kad tiek iedarbināts kameras aizslēgs, kas ir ļoti svarīgi precīzai PPK ģeolokācijai.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viena kontaktdakša: automātiski izvēlas kameru
  * Viena kamera + divi kontakti: 1. kontakts automātiski piešķirts kamerai
  * Vairākas kameras: nepieciešama manuāla izvēle

### Ekspozīcijas kontakts 2

* **Tips**: izvēles saraksts
* **Redzamība**: redzams tikai tad, ja ir ieslēgta opcija „Piemērot PPK korekcijas” UN ekspozīcijas dati ir pieejami 2. kontaktam
* **Opcijas**:
  * Projektā atrastie kameru modeļu nosaukumi
  * &quot;Nelietot&quot; — ignorē šo ekspozīcijas kontaktu
* **Noklusējums**: automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: piešķir konkrētu kameru ekspozīcijas kontaktam 2 PPK laika sinhronizācijai, izmantojot divu kameru konfigurāciju.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: kontakts 2 automātiski iestatīts uz &quot;Nelietot&quot;
  * Viena kamera + divi kontakti: 2. kontakts automātiski iestatīts uz &quot;Nelietot&quot;
  * Vairākas kameras: nepieciešama manuāla izvēle
* **Piezīme**: Vienu un to pašu kameru nevar vienlaikus piešķirt gan 1. kontaktam, gan 2. kontaktam.

***

## Indekss

Šie iestatījumi ļauj konfigurēt multispektrālos indeksus analīzei un vizualizācijai.

### Pievienot indeksu

* **Tips**: Speciāls indeksa konfigurācijas panelis
* **Apraksts**: Atver interaktīvu paneli, kurā varat izvēlēties un konfigurēt multispektrālos veģetācijas indeksus (NDVI, NDRE, EVI utt.), kas jāaprēķina attēla apstrādes laikā. Jūs varat pievienot vairākus indeksus, katram ar saviem vizualizācijas iestatījumiem.
* **Pieejamie indeksi**: Sistēma ietver vairāk nekā 30 iepriekš definētus multispektrālos indeksus, tostarp:
  * NDVI (normalizētais veģetācijas indekss)
  * NDRE (Normalizēta atšķirība RedEdge)
  * EVI (Uzlabots veģetācijas indekss)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Un daudzi citi (pilnu sarakstu skatiet [Daudzspektrālo indeksu formulas](multispectral-index-formulas.md))
* **Funkcijas**:
  * Izvēlieties no iepriekš definētām indeksu formulām
  * Konfigurējiet vizualizācijas krāsu gradientus (LUT — Look-Up Tables)
  * Iestatiet analīzes sliekšņa vērtības
  * Izveidojiet pielāgotas indeksu formulas

### Pielāgotas formulas (Chloros+ funkcija)

* **Tips**: Pielāgotu formulu definīciju masīvs
* **Apraksts**: Ļauj izveidot un saglabāt pielāgotas multispektrālo indeksu formulas, izmantojot joslu matemātiku. Pielāgotas formulas tiek saglabātas kopā ar projekta iestatījumiem un var tikt izmantotas tāpat kā iebūvēti indeksi.
* **Kā izveidot**:
  1. Indeksa konfigurācijas panelī meklējiet pielāgotās formulas opciju.
  2. Definējiet formulu, izmantojot joslu identifikatorus (piemēram, NIR, Red, Green, Blue).
  3. Saglabājiet formulu ar aprakstošu nosaukumu.
* **Formulas sintakse**: Tiek atbalstītas standarta matemātiskās darbības, tostarp:
  * Aritmētika: `+`, `-`, `*`, `/`
  * Aizkavēšanas zīmes darbību secībai
  * Band references: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Eksportēšana

Šie iestatījumi kontrolē eksportēto apstrādāto attēlu formātu un kvalitāti.

### Kalibrēts attēla formāts

* **Tips**: Izvēles saraksts
* **Opcijas**:
  * **TIFF (16 bitu)** - Nesaspiests 16 bitu TIFF formāts
  * **TIFF (32 bitu, procentos)** - 32 bitu peldošā punkta TIFF ar atstarojuma vērtībām procentos
  * **PNG (8 bitu)** - saspiests 8 bitu PNG formāts
  * **JPG (8 bitu)** - saspiests 8 bitu JPEG formāts
* **Noklusējums**: TIFF (16 bitu)
* **Apraksts**: Izvēlas faila formātu apstrādātu un kalibrētu attēlu saglabāšanai.
* **Formāta ieteikumi**:
  * **TIFF (16 bitu)**: Ieteicams zinātniskai analīzei un profesionāliem darba procesiem. Saglabā maksimālu datu kvalitāti bez kompresijas artefaktiem. Labākais risinājums multispektrālajai analīzei un turpmākai apstrādei GIS programmatūrā.
  * **TIFF (32 bitu, procentos)**: Labākais risinājums darba procesiem, kuros nepieciešami atstarojuma vērtības procentos (0–100 %). Nodrošina maksimālu precizitāti radiometriskajiem mērījumiem.
  * **PNG (8 bitu)**: piemērots tīmekļa skatīšanai un vispārējai vizualizācijai. Mazāki failu izmēri ar bezzaudējumu saspiešanu, bet samazināts dinamiskais diapazons.
  * **JPG (8 bitu)**: mazākie failu izmēri, vislabāk piemērots tikai priekšskatīšanai un tīmekļa attēlošanai. Izmanto zaudējumu saspiešanu, kas nav piemērota zinātniskai analīzei.

***

## Saglabāt projekta veidni

Šī funkcija ļauj saglabāt pašreizējās projekta iestatījumu kā atkārtoti izmantojamu veidni.

* **Tips**: Teksta ievade + Saglabāt pogu
* **Apraksts**: Ievadiet aprakstošu nosaukumu savai iestatījumu veidnei un noklikšķiniet uz saglabāšanas ikonas. Veidne saglabās visus pašreizējos projekta iestatījumus (mērķa noteikšana, apstrādes opcijas, indeksi un eksporta formāts), lai tos varētu viegli atkārtoti izmantot nākotnes projektos.
* **Lietošanas gadījumi**:
  * Izveidojiet veidnes dažādām kameru sistēmām (RGB, multispektrāla, NIR)
  * Saglabājiet standarta konfigurācijas konkrētiem kultūraugu veidiem vai analīzes darba plūsmām
  * Dalieties ar vienotiem iestatījumiem visā komandā
* **Lietošana**:
  1. Konfigurējiet visus vēlamos projekta iestatījumus
  2. Ievadiet veidnes nosaukumu (piemēram, &quot;RedEdge Survey3 NDVI Standarts&quot;)
  3. Noklikšķiniet uz saglabāšanas ikonas
  4. Tagad veidni var ielādēt, izveidojot jaunus projektus

***

## Saglabāt projekta mapi

Šis iestatījums nosaka, kur pēc noklusējuma tiek saglabāti jauni projekti.

* **Tips**: Direktoriāta ceļa parādīšana + Rediģēt pogu
* **Noklusējums**: `C:\Users\[Username]\Chloros Projects`
* **Apraksts**: Parāda pašreizējo noklusējuma direktoriātu, kurā tiek izveidoti jauni Chloros projekti. Noklikšķiniet uz rediģēt ikonas, lai izvēlētos citu direktoriātu.
* **Kad mainīt**:
  * Iestatiet tīkla disku komandas sadarbībai
  * Mainiet uz disku ar lielāku uzglabāšanas vietu lieliem datu kopumiem
  * Organizējiet projektus pēc gada, klienta vai projekta veida dažādās mapēs
* **Piezīme**: Šī iestatījuma maiņa ietekmē tikai JAUNUS projektus. Esošie projekti paliek sākotnējās vietās.

***

## Iestatījumu saglabāšana

Visi projekta iestatījumi tiek automātiski saglabāti kopā ar projekta failu (`.mapir` projekta formāts). Kad atverat projektu atkārtoti, visi iestatījumi tiek atjaunoti tieši tā, kā tos atstājāt.

### Iestatījumu hierarhija

Iestatījumi tiek piemēroti šādā secībā:

1. **Sistēmas noklusējumi** — iebūvēti noklusējumi, kas definēti ar Chloros
2. **Veidnes iestatījumi** — ja veidojot projektu, jūs ielādējat veidni
3. **Saglabātie projekta iestatījumi** — iestatījumi, kas saglabāti kopā ar projekta failu
4. **Manuālas korekcijas** — jebkuras izmaiņas, ko veicat pašreizējā sesijā

### Iestatījumi un attēlu apstrāde

Lielākā daļa iestatījumu izmaiņu (īpaši kategorijās „Apstrāde” un „Eksportēšana”) izraisīs attēlu atkārtotu apstrādi, lai atspoguļotu jaunās iestatījumu vērtības. Tomēr daži iestatījumi ir „tikai eksportēšanai” un neprasa tūlītēju atkārtotu apstrādi:

* Saglabāt projekta veidni
* Darba katalogs
* Kalibrēts attēlu formāts (attiecas uz eksportēšanu)

***

## Labākā prakse

1. **Sāciet ar noklusējumiem**: noklusējuma iestatījumi darbojas labi lielākajai daļai MAPIR kameru sistēmu un tipiskiem darba procesiem.
2. **Izveidojiet veidnes**: kad esat optimizējis iestatījumus konkrētam darba procesam vai kamerai, saglabājiet tos kā veidni, lai nodrošinātu konsekvenci visos projektos.
3. **Pārbaudiet pirms pilnīgas apstrādes**: eksperimentējot ar jauniem iestatījumiem, pārbaudiet tos uz nelielu attēlu apakškopu, pirms apstrādājat visu datu kopu.
4. **Dokumentējiet savus iestatījumus**: izmantojiet aprakstošus veidņu nosaukumus, kas norāda kameras sistēmu, apstrādes veidu un paredzēto lietojumu (piemēram, &quot;Survey3\_RGB\_NDVI\_Agriculture&quot;).
5. **Eksporta formāta izvēle**: izvēlieties eksporta formātu atbilstoši galīgajam lietojumam:
   * Zinātniskā analīze → TIFF (16 bitu vai 32 bitu)
   * GIS apstrāde → TIFF (16 bitu)
   * Ātra vizualizācija → PNG (8 bitu)
   * Koplietošana tīmeklī → JPG (8 bitu)

***

Lai iegūtu vairāk informācijas par multispektrāliem indeksiem Chloros, skatiet lapu [Multispektrālo indeksu formulas](multispectral-index-formulas.md).
