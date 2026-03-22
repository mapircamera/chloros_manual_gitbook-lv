# Projekta iestatījumu pielāgošana

Pirms attēlu apstrādes ir svarīgi konfigurēt projekta iestatījumus atbilstoši jūsu darba plūsmas prasībām. Sadaļa „Projekta iestatījumi“ <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> paneļa nodrošina visaptverošu kontroli pār kalibrēšanu, apstrādes opcijām, multispektrālajiem indeksiem un eksporta formātiem.

## Piekļuve projekta iestatījumiem

1. Atveriet savu projektu Chloros
2. Noklikšķiniet uz **Projekta iestatījumi** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
3. Projektu iestatījumu panelī tiek parādītas visas konfigurācijas opcijas

{% hint style="info" %}
**Iestatījumi tiek saglabāti automātiski** kopā ar projektu. Kad atverat projektu no jauna, visi iestatījumi tiek atjaunoti.
{% endhint %}

***

## Ātra konfigurācija tipiskām darba plūsmām

### Noklusējuma iestatījumi (ieteicami lielākajai daļai lietotāju)

Tipiskām MAPIR Survey3 kameru darbplūsmām noklusējuma iestatījumi darbojas labi:

* ✅ **Vignette korekcija**: Iespējota
* ✅ **Atstarošanas kalibrēšana**: Iespējota (nepieciešami MAPIR mērķu attēli)
* ✅ **Debayer metode**: Standarta (ātra, vidēja kvalitāte)
* ✅ **Eksporta formāts**: TIFF (16 bitu)

Vienkārši importējiet savus attēlus un sāciet apstrādi, izmantojot šos noklusējumus.

***

## Projekta iestatījumu pārskats

Projekta iestatījumu panelis ir sadalīts vairākās kategorijās. Zemāk ir katras sadaļas kopsavilkums. Pilnīgu dokumentāciju skatiet [Projekta iestatījumi](../project-settings/project-settings.md).

### Mērķa noteikšana

Kontrolē, kā Chloros identificē kalibrēšanas mērķus jūsu attēlos.

**Galvenie iestatījumi:*** **Minimālā kalibrēšanas parauga platība**: Mērķa atklāšanas izmēra slieksnis (noklusējums: 25 pikseļi)
* **Minimālā mērķu grupēšana**: Līdzības slieksnis mērķa reģionu grupēšanai (noklusējums: 60)**Kad pielāgot:**

* Palieliniet parauga platību, ja tiek iegūti kļūdaini atklājumi
* Samaziniet, ja mērķi netiek atklāti
* Pielāgojiet grupēšanu, ja mērķi tiek sadalīti vairākās atklāšanās

### Apstrāde

Galvenās attēla apstrādes un kalibrēšanas opcijas.

**Galvenie iestatījumi:*** **Vignette korekcija**: Kompensē objektīva tumšošanos malās ✅ Ieteicams
* **Atstarošanas kalibrēšana**: Normalizē vērtības, izmantojot kalibrēšanas mērķus ✅ Ieteicams
* **Debayer metode**: Algoritms RAW failu konvertēšanai 3-kanālu multispektrālos attēlos
* **Minimālais atkārtotas kalibrēšanas intervāls**: Laiks starp kalibrēšanas mērķu izmantošanu (0 = izmantot visus)**Papildu iestatījumi:*** **Gaismas sensora laika zonas nobīde**: PPK laika sinhronizācijai (noklusējums: 0)
* **Piemērot PPK korekcijas**: Izmanto GPS/ekspozīcijas pinu datus no .daq failiem
* **Ekspozīcijas pini 1/2**: Piesaista kameras ekspozīcijas piniem divkameru konfigurācijām

### Debayer metode

Pašlaik Chloros piedāvājam 2 debayering metodes:

#### Standarta (ātra, vidēja kvalitāte)

Standarta debayeringa process ir ātrs, bet parāda debayeringa krāsu troksni, kā rezultātā attēli ir mazāk precīzi un trokšņaināki.

#### Tekstūras apzināšanās (lēns, augstākā kvalitāte) \[Tikai Chloros+]

Tekstūras apzināšanās izmanto augstas kvalitātes malu apzināšanās debayeringu kombinācijā ar AI/ML trokšņu samazināšanas modeli, kas noņem gandrīz visu debayeringa troksni. Tekstūras atpazīšanas modelim darbībai ir nepieciešama GPU atmiņa (VRAM). Lai nodrošinātu ātrāku apstrādi, ieteicams to izmantot, ja jums ir pieejami &gt;4 GB VRAM.

### Indekss (daudzspektrālie indeksi)

Konfigurējiet, kurus veģetācijas indeksus aprēķināt un eksportēt.

**Kā pievienot indeksus:**

1. Noklikšķiniet uz pogas**&quot;Pievienot indeksu&quot;**

2. Izvēlieties indeksu no nolaižamās izvēlnes (NDVI, NDRE, GNDVI utt.)
3. Konfigurējiet vizualizācijas iestatījumus (LUT krāsas, vērtību diapazoni)
4. Pēc nepieciešamības pievienojiet vairākus indeksus

**Populāri indeksi:*** **NDVI**: Vispārējais veģetācijas veselības stāvoklis (visbiežāk izmantotais)
* **NDRE**: Agrīna stresa noteikšana kopā ar RedEdge
* **GNDVI**: Jutīgs pret hlorofila koncentrāciju
* **OSAVI**: labi darbojas ar redzamu augsni
* **EVI**: reģioni ar augstu lapu platības indeksu (LAI)**Pielāgotas formulas (tikai Chloros+):**

* Izveidojiet pielāgotas multispektrālo indeksu formulas
* Izmantojiet joslu matemātiku ar visiem attēla kanāliem
* Saglabājiet pielāgotās formulas atkārtotai izmantošanai

Visus pieejamos indeksus un formulas skatiet [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

### Eksportēšana

Kontrolē izvades faila formātu un kvalitāti.

**Pieejamie formāti:*** **TIFF (16 bitu)**: Ieteicams ģeogrāfiskās informācijas sistēmām (GIS) un zinātniskai analīzei (diapazons 0–65 535)
* **TIFF (32 bitu, procentos)**: Atstarošanas vērtības ar peldošo komatu (diapazons 0,0–1,0)
* **PNG (8 bitu)**: bezzaudējumu kompresija vizualizācijai (diapazons 0–255)
* **JPG (8 bitu)**: vismazākie faili, zaudējumu kompresija (diapazons 0–255)***

## Iestatījumu saglabāšana un ielāde

### Projekta veidnes saglabāšana

Izveidojiet atkārtoti izmantojamus veidnes, lai nodrošinātu vienotu darba plūsmu:

1. Konfigurējiet visus vēlamos iestatījumus paneļā „Projekta iestatījumi”
2. Pārvietojieties uz sadaļu **„Saglabāt projekta veidni”** apakšā
3. Ievadiet aprakstošu veidnes nosaukumu (piem., „Survey3N\_RGN\_Agriculture”)
4. Noklikšķiniet uz saglabāšanas ikonas

**Priekšrocības:**

* Piemērojiet identiskus iestatījumus vairākos projektos
* Dalieties ar konfigurācijām ar komandas locekļiem
* Saglabājiet konsekvenci atkārtotām aptaujām

### Ielādējiet veidni jaunā projektā

Izveidojot jaunu projektu:

1. Izvēlieties **&quot;Jauns projekts&quot;** galvenajā izvēlnē
2. Izvēlieties opciju **&quot;Ielādēt no veidnes&quot;**

3. Izvēlieties savu saglabāto veidni
4. Visi iestatījumi tiek automātiski piemēroti

### Darba katalogs

Iestatījums **&quot;Projekta saglabāšanas mape&quot;** nosaka, kur pēc noklusējuma tiek izveidoti jauni projekti:

* **Noklusējuma atrašanās vieta**: `C:\Users\[Username]\Chloros Projects`
* **Mainīt atrašanās vietu**: Noklikšķiniet uz rediģēšanas ikonas un izvēlieties jaunu mapi
* **Kad mainīt**:
  * Tīkla disks komandas sadarbībai
  * Cits disks ar lielāku uzglabāšanas vietu
  * Organizēta mapju struktūra pēc gada/klienta

***

## PPK (pēcapstrādātas kinemātikas) iestatījumi

Ja izmantojat MAPIR DAQ ierakstītājus ar GPS precīzai ģeolokācijai:

### Priekšnosacījumi

* MAPIR DAQ ar GPS (GNSS) moduli
* .daq žurnāla fails ar ekspozīcijas kontaktu ierakstiem
* Kamera, kas uzņemšanas sesijas laikā ir pieslēgta DAQ ekspozīcijas kontaktiem

### Konfigurācijas soļi

1. Ievietojiet .daq žurnāla failu savā projekta mapē
2. Projektu iestatījumos atzīmējiet izvēles rūtiņu **&quot;Piemērot PPK korekcijas&quot;**

3. Ja nepieciešams, iestatiet**&quot;Gaismas sensora laika zonas nobīdi&quot;** (noklusējums: 0 UTC)
4. Piešķiriet kameras ekspozīcijas kontaktligzdām:
   * **Viena kamera**: automātiski piešķirta 1. kontaktligzdai
   * **Divas kameras**: manuāli piešķiriet katru kameru pareizajai kontaktligzdai**Ekspozīcijas kontaktligzdu piešķiršana:*** **Ekspozīcijas kontakts 1**: Izvēlieties kameras modeli no izvēlnes
* **Ekspozīcijas kontakts 2**: Izvēlieties otro kameru vai &quot;Nelietot&quot;
* Vienu un to pašu kameru nevar piešķirt abiem kontaktiem

{% hint style="warning" %}
**Svarīgi**: Ekspozīcijas kontaktiem jābūt pareizi piešķirtiem attiecīgajām kamerām. Nepareiza piešķiršana izraisīs nepareizus ģeolokācijas datus.
{% endhint %}

***

## Papildu scenāriji

### Projektu ar vairākām kamerām

Apstrādājot attēlus no vairākām MAPIR kamerām vienā projektā:

1. Chloros automātiski atpazīst katras kameras modeli
2. Katrai kamerai tiek piešķirts atbilstošs apstrādes profils
3. PPK: manuāli piešķiriet katrai kamerai pareizo ekspozīcijas kontaktu
4. Visas kameras izmanto vienādu eksporta formātu un indeksus

**Piemērs**: Survey3W RGN + Survey3N OCN divkameru statīvs

### Laika nobīdes vai vairāku datumu apsekojumi

Lai laika gaitā atkārtoti veiktu apsekojumus vienā un tajā pašā teritorijā:

1. Izveidojiet šablonu ar saviem standarta iestatījumiem
2. Katrā sesijā izmantojiet vienotu kalibrēšanas mērķa konfigurāciju
3. Apstrādājiet katru datumu kā atsevišķu projektu
4. Lai iegūtu salīdzināmus rezultātus, izmantojiet identiskus iestatījumus
5. Eksportējiet vienā un tajā pašā formātā, lai veiktu laika analīzi

### Lieli datu kopumi

Projektiem ar daudziem attēliem (500+):

* Apsvērt sadalīšanu mazākos projektos pēc datuma vai teritorijas
* Izmantot Chloros+ paralēlo apstrādi ātrākiem rezultātiem
* Apsvērt CLI vai API partiju automatizācijai
* Pielāgot minimālo atkārtotās kalibrēšanas intervālu, lai samazinātu mērķa noteikšanas laiku

***

## Jūsu iestatījumu pārbaude

Pirms apstrādes sākšanas pārbaudiet šos galvenos iestatījumus:

* [ ] Kameras modelis pareizi atpazīts failu pārlūkā
* [ ] Vignette korekcija ieslēgta
* [ ] Reflektances kalibrēšana ieslēgta
* [ ] Importēts vismaz viens kalibrēšanas mērķa attēls
* [ ] Pievienoti vēlamie multispektrālie indeksi
* [ ] Eksporta formāts atbilst jūsu darba plūsmai
* [ ] PPK iestatījumi konfigurēti (ja izmantojat .daq ar ekspozīcijas notikumiem)

***

## Turpmākie soļi

Kad iestatījumi ir konfigurēti:

1. **Atzīmējiet kalibrēšanas mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet progresu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Pilnīga informācija par visiem pieejamajiem iestatījumiem ir atrodama atsauces dokumentācijā [Projekta iestatījumi](../project-settings/project-settings.md).
