# Projekta iestatījumi

Projekta iestatījumu <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> sānu joslā Chloros ļauj konfigurēt visus attēlu apstrādes aspektus, kalibrēšanas mērķa noteikšanu, multispektrālo indeksu aprēķinus un eksportēšanas opcijas jūsu projektam. Šie iestatījumi tiek saglabāti kopā ar jūsu projektu un var tikt saglabāti kā veidnes atkārtotai izmantošanai vairākos projektos.

## Piekļuve projekta iestatījumiem

Lai piekļūtu projekta iestatījumiem:

1. Atveriet projektu programmā Chloros
2. Noklikšķiniet uz cilnes **Projekta iestatījumi**  <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> cilni kreisajā sānjoslā
3. Iestatījumu panelī tiks parādītas visas pieejamās konfigurācijas opcijas, kas sakārtotas pēc kategorijām

***

## Mērķa noteikšana

Šie iestatījumi kontrolē to, kā Chloros atpazīst un apstrādā kalibrēšanas mērķus jūsu attēlos.

### Minimālā kalibrēšanas parauga platība (px)

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 10 000 pikseļiem
* **Noklusējums**: 25 pikseļi
* **Apraksts**: Nosaka minimālo platību (pikseļos), kas nepieciešama, lai atklāto reģionu uzskatītu par derīgu kalibrēšanas mērķa paraugu. Mazākas vērtības atklās mazākus mērķus, bet var palielināt kļūdainu pozitīvo rezultātu skaitu. Lielākas vērtības prasa lielākus, skaidrākus mērķa reģionus atklāšanai.
* **Kad pielāgot**:
  * Palieliniet, ja saņemat kļūdainus atklājumus uz maziem attēla artefaktiem
  * Samaziniet, ja jūsu kalibrēšanas mērķi attēlos izskatās mazi un netiek atklāti

### Minimālā mērķu grupēšana (0–100)

* **Tips**: Skaitlis
* **Diapazons**: 0 līdz 100
* **Noklusējums**: 60
* **Apraksts**: Kontrolē klasterizācijas slieksni, lai grupētu līdzīgas krāsas reģionus, atklājot kalibrēšanas mērķus. Augstākas vērtības prasa, lai tiktu grupētas vairāk līdzīgas krāsas, rezultātā radot konservatīvāku mērķu atklāšanu. Zemākas vērtības ļauj lielāku krāsu variāciju mērķu grupā.
* **Kad pielāgot**:
  * Palieliniet, ja kalibrēšanas mērķi tiek sadalīti vairākās atklāšanās
  * Samaziniet, ja kalibrēšanas mērķi ar krāsu variācijām netiek pilnībā atklāti

***

## Apstrāde

Šie iestatījumi kontrolē to, kā Chloros apstrādā un kalibrē jūsu attēlus.

### Vignēšanas korekcija

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Piemēro vinjetes korekciju, lai kompensētu objektīva tumšošanos attēlu malās. Vinjetēšana ir izplatīta optiska parādība, kuras dēļ attēla stūri un malas izskatās tumšākas nekā centrs objektīva īpašību dēļ.
* **Kad atspējot**: Atspējiet tikai tad, ja jūsu kameras/objektīva kombinācija jau ir piemērojusi vinjetes korekciju vai ja vēlaties manuāli koriģēt vinjetēšanu pēcapstrādē.

### Atstarošanas kalibrēšana / balansa iestatīšana

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Iespējo automātisku atstarošanas kalibrēšanu, izmantojot attēlos atklātos kalibrēšanas mērķus. Tas normalizē atstarošanas vērtības visā datu kopā un nodrošina konsekventus mērījumus neatkarīgi no apgaismojuma apstākļiem.
* **Kad atspējot**: Atspējojiet tikai tad, ja vēlaties apstrādāt neapstrādātus, nekalibrētus attēlus vai ja izmantojat citu kalibrēšanas darba plūsmu.

### Debayer metode

* **Tips**: Izvēles saraksts
* **Opcijas**:
  * Standarta (ātra, vidēja kvalitāte)
  * Tekstūras apzināta (lēna, augstākā kvalitāte) \[Chloros+]
* **Noklusējums**: Standarta (ātra, vidēja kvalitāte)
* **Apraksts**: Izvēlas demosaicing algoritmu, ko izmanto, lai pārvērstu neapstrādātus Bayer modeļa sensora datus pilnkrāsu attēlos. Metode „Standarta (ātra, vidēja kvalitāte)” nodrošina optimālu līdzsvaru starp apstrādes ātrumu un attēla kvalitāti. &quot;Tekstūras atpazīšana (lēns, augstākā kvalitāte)&quot; \[Chloros+] izmanto augstas kvalitātes malu atpazīšanas demosaicing, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kas noņem gandrīz visus demosaicing trokšņus. Tekstūras atpazīšanas modelim darbībai ir nepieciešama GPU atmiņa (VRAM). Mēs iesakām to izmantot, ja jums ir pieejami &gt;4GB VRAM, lai nodrošinātu ātrāku apstrādi.
* **Piezīme**: Turpmākajās Chloros versijās var tikt pievienotas papildu debayer metodes.

### Minimālais pārkalibrēšanas intervāls

* **Tips**: Skaitlis
* **Diapazons**: No 0 līdz 3600 sekundēm
* **Noklusējums**: 0 sekundes
* **Apraksts**: Nosaka minimālo laika intervālu (sekundēs) starp kalibrēšanas mērķu izmantošanu. Ja iestatīts uz 0, Chloros izmantos katru atklāto kalibrēšanas mērķi. Ja iestatīts uz lielāku vērtību, Chloros izmantos tikai tos kalibrēšanas mērķus, starp kuriem ir vismaz šāds laika intervāls, samazinot apstrādes laiku datu kopām ar biežiem kalibrēšanas mērķu uztveršanas gadījumiem.
* **Kad pielāgot**:
  * Iestatiet uz 0, lai nodrošinātu maksimālu kalibrēšanas precizitāti, ja apgaismojuma apstākļi mainās
  * Palieliniet (piemēram, līdz 60–300 sekundēm), lai nodrošinātu ātrāku apstrādi, ja apgaismojums ir nemainīgs un jums ir bieži kalibrēšanas mērķu attēli

### Gaismas sensora laika zonas nobīde

* **Tips**: Skaitlis
* **Diapazons**: no -12 līdz +12 stundām
* **Noklusējums**: 0 stundas
* **Apraksts**: Norāda laika zonas nobīdi (stundās no UTC) gaismas sensora datu laika zīmogiem. To izmanto, apstrādājot PPK (pēcapstrādātas kinemātiskās) datu failus, lai nodrošinātu pareizu laika sinhronizāciju starp attēlu uzņemšanu un GPS datiem.
* **Kad pielāgot**: Iestatiet to atbilstoši jūsu vietējai laika zonas nobīdei, ja jūsu PPK dati izmanto vietējo laiku, nevis UTC. Piemēram:
  * Klusā okeāna laiks: -8 vai -7 (atkarībā no vasaras laika)
  * Austrumu laiks: -5 vai -4 (atkarībā no vasaras laika)
  * Centrāleiropas laiks: +1 vai +2 (atkarībā no vasaras laika)

### Piemērot PPK korekcijas

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Atspējots (neatzīmēts)
* **Apraksts**: Ļauj izmantot pēcapstrādes kinemātiskās (PPK) korekcijas no MAPIR DAQ reģistratoriem, kuros ir GPS (GNSS). Ja šī funkcija ir ieslēgta, Chloros izmantos jebkādus .daq žurnāla failus, kas satur ekspozīcijas pinu datus jūsu projekta direktorijā, un piemēros precīzas ģeolokācijas korekcijas jūsu attēliem.
* **Prasība**: .daq žurnāla failam ar ekspozīcijas pinu ierakstiem jābūt jūsu projekta direktorijā
* **Kad ieslēgt**: Ieteicams vienmēr ieslēgt PPK korekciju, ja jūsu .daq žurnāla failā ir ekspozīcijas atgriezeniskās saites ieraksti.

### Ekspozīcijas kontakts 1

* **Tips**: Izvēlne
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta funkcija &quot;Piemērot PPK korekcijas&quot; UN ir pieejami ekspozīcijas dati kontaktam 1
* **Opcijas**:
  * Projektā atklātie kameru modeļu nosaukumi
  * &quot;Nelietot&quot; - Ignorēt šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: Piesaka konkrētu kameru ekspozīcijas kontaktam 1 PPK laika sinhronizācijai. Ekspozīcijas kontakts reģistrē precīzu laiku, kad tiek iedarbināts kameras aizslēgs, kas ir ļoti svarīgi precīzai PPK ģeolokācijai.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: Automātiski izvēlas kameru
  * Viena kamera + divi kontakti: Kontakts 1 automātiski tiek piešķirts kamerai
  * Vairākas kameras: Nepieciešama manuāla izvēle

### Ekspozīcijas kontakts 2

* **Tips**: Izvēles saraksts
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta opcija &quot;Piemērot PPK korekcijas&quot; UN ja ekspozīcijas dati ir pieejami kontaktam 2
* **Opcijas**:
  * Projektā atpazītie kameru modeļu nosaukumi
  * &quot;Nelietot&quot; — ignorē šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: Piesaka konkrētu kameru ekspozīcijas kontaktam 2 PPK laika sinhronizācijai, izmantojot divu kameru konfigurāciju.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: Kontakts 2 automātiski iestatīts uz &quot;Nelietot&quot;
  * Viena kamera + divi kontakti: 2. kontakts automātiski tiek iestatīts uz &quot;Nelietot&quot;
  * Vairākas kameras: nepieciešama manuāla izvēle
* **Piezīme**: Vienu un to pašu kameru nevar vienlaikus piešķirt gan 1. kontaktam, gan 2. kontaktam.***

## Indekss

Šie iestatījumi ļauj konfigurēt multispektrālos indeksus analīzei un vizualizācijai.

### Pievienot indeksu

* **Tips**: Speciāls indeksu konfigurācijas panelis
* **Apraksts**: Atver interaktīvu paneli, kurā varat izvēlēties un konfigurēt multispektrālos veģetācijas indeksus (NDVI, NDRE, EVI utt.), kurus aprēķināt attēla apstrādes laikā. Jūs varat pievienot vairākus indeksus, katram ar saviem vizualizācijas iestatījumiem.
* **Pieejamie indeksi**: Sistēma ietver vairāk nekā 30 iepriekš definētus multispektrālos indeksus, tostarp:
  * NDVI (Normalizētais veģetācijas indekss)
  * NDRE (Normalizētā starpība RedEdge)
  * EVI (Uzlabotais veģetācijas indekss)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Un daudzi citi (pilnu sarakstu skatiet [Daudzspektrālo indeksu formulas](multispectral-index-formulas.md))
* **Funkcijas**:
  * Izvēlieties no iepriekš definētām indeksu formulām
  * Konfigurējiet vizualizācijas krāsu gradientus (LUT — Look-Up Tables)
  * Iestatiet sliekšņa vērtības analīzei
  * Izveidojiet pielāgotas indeksu formulas

### Pielāgotas formulas (Chloros+ funkcija)

* **Tips**: Pielāgotu formulu definīciju masīvs
* **Apraksts**: Ļauj izveidot un saglabāt pielāgotas multispektrālo indeksu formulas, izmantojot joslu matemātiku. Pielāgotās formulas tiek saglabātas kopā ar projekta iestatījumiem un tās var izmantot tāpat kā iebūvētos indeksus.
* **Kā izveidot**:
  1. Indeksa konfigurācijas panelī meklējiet pielāgotās formulas opciju
  2. Definējiet savu formulu, izmantojot joslu identifikatorus (piem., NIR, Red, Green, Blue)
  3. Saglabājiet formulu ar aprakstošu nosaukumu
* **Formulas sintakse**: Tiek atbalstītas standarta matemātiskās darbības, tostarp:
  * Aritmētika: `+`, `-`, `*`, `/`
  * Iekavās norādiet darbību secību
  * Bandu atsauces: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Eksportēšana

Šie iestatījumi nosaka eksportēto apstrādāto attēlu formātu un kvalitāti.

### Kalibrēta attēla formāts

* **Tips**: Izvēles saraksts
* **Opcijas**:
  * **TIFF (16-bit)** - Nesaspiests 16-bitu TIFF formāts
  * **TIFF (32 bitu, procentos)** - 32 bitu peldošā punkta TIFF ar atstarojuma vērtībām procentos
  * **PNG (8 bitu)** - Saspiests 8-bitu PNG formāts
  * **JPG (8-bitu)** - Saspiests 8-bitu JPEG formāts
* **Noklusējums**: TIFF (16-bitu)
* **Apraksts**: Izvēlas faila formātu apstrādāto un kalibrēto attēlu saglabāšanai.
* **Formāta ieteikumi**:
  * **TIFF (16 bitu)**: Ieteicams zinātniskai analīzei un profesionālām darba plūsmām. Saglabā maksimālu datu kvalitāti bez saspiešanas artefaktiem. Vispiemērotākais multispektrālajai analīzei un turpmākai apstrādei GIS programmās.
  * **TIFF (32 bitu, procentos)**: Vispiemērotākais darba plūsmām, kurās nepieciešamas atstarojuma vērtības procentos (0–100 %). Nodrošina maksimālu precizitāti radiometriskajiem mērījumiem.
  * **PNG (8 bitu)**: Piemērots skatīšanai tīmeklī un vispārējai vizualizācijai. Mazāki failu izmēri ar bezzaudējumu kompresiju, bet samazināts dinamiskais diapazons.
  * **JPG (8-bit)**: Mazākie failu izmēri, vislabāk piemērots tikai priekšskatīšanai un attēlošanai tīmeklī. Izmanto zaudējumu kompresiju, kas nav piemērota zinātniskai analīzei.***

## Saglabāt projekta veidni

Šī funkcija ļauj saglabāt pašreizējos projekta iestatījumus kā atkārtoti izmantojamu veidni.

* **Tips**: Teksta ievade + Saglabāt pogu
* **Apraksts**: Ievadiet aprakstošu nosaukumu savam iestatījumu veidnei un noklikšķiniet uz saglabāšanas ikonas. Veidne saglabās visus jūsu pašreizējos projekta iestatījumus (mērķa noteikšana, apstrādes opcijas, indeksi un eksporta formāts), lai tos varētu viegli atkārtoti izmantot nākotnes projektos.
* **Lietošanas gadījumi**:
  * Izveidojiet veidnes dažādām kameru sistēmām (RGB, multispektrāla, NIR)
  * Saglabājiet standarta konfigurācijas konkrētiem kultūraugu veidiem vai analīzes darba plūsmām
  * Dalieties ar vienotiem iestatījumiem visā komandā
* **Kā lietot**:
  1. Konfigurējiet visus vēlamos projekta iestatījumus
  2. Ievadiet veidnes nosaukumu (piem., &quot;RedEdge Survey3 NDVI Standarts&quot;)
  3. Noklikšķiniet uz saglabāšanas ikonas
  4. Tagad veidni var ielādēt, veidojot jaunus projektus

***

## Saglabāt projekta mapi

Šis iestatījums nosaka, kur pēc noklusējuma tiek saglabāti jauni projekti.

* **Tips**: Kataloga ceļa parādīšana + Rediģēt pogu
* **Noklusējums (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Noklusējums (Linux)**: `~/.local/share/chloros/projects`
* **Apraksts**: Parāda pašreizējo noklusējuma direktoriju, kurā tiek izveidoti jauni Chloros projekti. Noklikšķiniet uz rediģēšanas ikonas, lai izvēlētos citu direktoriju.
* **Kad mainīt**:
  * Iestatiet tīkla disku komandas sadarbībai
  * Mainiet uz disku ar lielāku uzglabāšanas vietu lieliem datu kopumiem
  * Sakārtot projektus pēc gada, klienta vai projekta veida dažādās mapēs
* **Piezīme**: Šī iestatījuma maiņa ietekmē tikai JAUNUS projektus. Esošie projekti paliek savās sākotnējās vietās.***

## Iestatījumu saglabāšana

Visi projekta iestatījumi tiek automātiski saglabāti kopā ar projekta failu (`.mapir` projekta formāts). Kad atverat projektu no jauna, visi iestatījumi tiek atjaunoti tieši tā, kā tos atstājāt.

### Iestatījumu hierarhija

Iestatījumi tiek piemēroti šādā secībā:

1. **Sistēmas noklusējumi** — iebūvēti noklusējumi, kas definēti ar Chloros
2. **Veidnes iestatījumi** — ja projekta izveides laikā ielādējat veidni
3. **Saglabātie projekta iestatījumi** — iestatījumi, kas saglabāti kopā ar projekta failu
4. **Manuālie pielāgojumi** — jebkuras izmaiņas, ko veicat pašreizējās sesijas laikā

### Iestatījumi un attēlu apstrāde

Lielākā daļa iestatījumu izmaiņu (īpaši kategorijās „Apstrāde” un „Eksportēšana”) izraisīs attēlu atkārtotu apstrādi, lai atspoguļotu jaunās iestatījumu vērtības. Tomēr daži iestatījumi ir „tikai eksportēšanai” un neprasa tūlītēju atkārtotu apstrādi:

* Saglabāt projekta veidni
* Darba katalogs
* Kalibrētais attēla formāts (attiecas uz eksportēšanu)

***

## Labākā prakse

1. **Sāciet ar noklusējumiem**: Noklusējuma iestatījumi darbojas labi lielākajai daļai MAPIR kameru sistēmu un tipiskiem darba procesiem.
2. **Izveidojiet veidnes**: Kad esat optimizējis iestatījumus konkrētam darba procesam vai kamerai, saglabājiet tos kā veidni, lai nodrošinātu konsekvenci starp projektiem.
3. **Pārbaudiet pirms pilnīgas apstrādes**: Eksperimentējot ar jauniem iestatījumiem, pārbaudiet tos uz nelielu attēlu apakškopu, pirms apstrādājat visu datu kopu.
4. **Dokumentējiet savus iestatījumus**: Izmantojiet aprakstošus veidņu nosaukumus, kas norāda kameru sistēmu, apstrādes veidu un paredzēto lietojumu (piem., &quot;Survey3\_RGB\_NDVI\_Agriculture&quot;).
5. **Eksporta formāta izvēle**: Izvēlieties eksporta formātu atbilstoši galīgajam lietojumam:
   * Zinātniskā analīze → TIFF (16 bitu vai 32 bitu)
   * GIS apstrāde → TIFF (16 bitu)
   * Ātra vizualizācija → PNG (8 bitu)
   * Koplietošana tīmeklī → JPG (8 bitu)

***

Lai iegūtu vairāk informācijas par multispektrālajiem indeksiem Chloros, skatiet lapu [Multispektrālo indeksu formulas](multispectral-index-formulas.md).
