# Projekta iestatījumu pielāgošana

Pirms attēlu apstrādes ir svarīgi konfigurēt projekta iestatījumus atbilstoši darba plūsmas prasībām. Projekta iestatījumu <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> nodrošina visaptverošu kontroli pār kalibrēšanu, apstrādes opcijām, multispektrāliem indeksiem un eksporta formātiem.

## Piekļuve projekta iestatījumiem

1. Atveriet savu projektu Chloros
2. Noklikšķiniet uz **Projekta iestatījumi** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
3. Projektu iestatījumu panelī tiek parādītas visas konfigurācijas opcijas

{% hint style=&quot;info&quot; %}
**Iestatījumi tiek automātiski saglabāti** kopā ar projektu. Atverot projektu atkārtoti, visi iestatījumi tiek atjaunoti.
{% endhint %}

***

## Ātra konfigurācija tipiskiem darba procesiem

### Noklusējuma iestatījumi (ieteicams lielākajai daļai lietotāju)

Tipiskām MAPIR Survey3 kameru darba plūsmām labi der noklusējuma iestatījumi:

* ✅ **Vignette korekcija**: ieslēgta
* ✅ **Atstarošanas kalibrēšana**: ieslēgta (nepieciešami MAPIR mērķu attēli)
* ✅ **Debayer metode**: Augsta kvalitāte (ātrāka)
* ✅ **Eksporta formāts**: TIFF (16 bitu)

Vienkārši importējiet savus attēlus un sāciet apstrādi ar šiem noklusējumiem.

***

## Projekta iestatījumu pārskats

Projekta iestatījumu panelis ir sadalīts vairākās kategorijās. Zemāk ir katras sadaļas kopsavilkums. Pilnīga dokumentācija ir pieejama [Projekta iestatījumi](../project-settings/project-settings.md).

### Mērķa atklāšana

Kontrolē, kā Chloros identificē kalibrēšanas mērķus jūsu attēlos.

**Galvenie iestatījumi:**

* **Minimālā kalibrēšanas parauga platība**: izmēra slieksnis mērķa noteikšanai (noklusējums: 25 pikseļi)
* **Minimālā mērķa klasterizācija**: līdzības slieksnis mērķa reģionu grupēšanai (noklusējums: 60)

**Kad pielāgot:**

* Palieliniet parauga platību, ja tiek iegūti kļūdaini rezultāti
* Samaziniet, ja mērķi netiek noteikti
* Pielāgojiet klasterizāciju, ja mērķi tiek sadalīti vairākās noteiktajās vietās

### Apstrāde

Galvenās attēlu apstrādes un kalibrēšanas opcijas.

**Galvenie iestatījumi:**

* **Vignette korekcija**: kompensē objektīva tumšākošanu malās ✅ Ieteicams
* **Atstarošanas kalibrēšana**: normalizē vērtības, izmantojot kalibrēšanas mērķus ✅ Ieteicams
* **Debayer metode**: algoritms RAW konvertēšanai 3 kanālu multispektrā
* **Minimālais pārkalibrēšanas intervāls**: laiks starp kalibrēšanas mērķu izmantošanu (0 = izmantot visus)

**Papildu iestatījumi:**

* **Gaismas sensora laika zonas nobīde**: PPK laika sinhronizācijai (noklusējums: 0)
* **Piemērot PPK korekcijas**: izmanto GPS/ekspozīcijas pinu datus no .daq failiem
* **Ekspozīcijas pīns 1/2**: piešķir kameras ekspozīcijas pīniem divu kameru konfigurācijām

### Indekss (daudzspektrālie indeksi)

Konfigurējiet, kurus veģetācijas indeksus aprēķināt un eksportēt.

**Kā pievienot indeksus:**

1. Noklikšķiniet uz pogas **&quot;Pievienot indeksu&quot;**
2. Izvēlieties indeksu no nolaižamās izvēlnes (NDVI, NDRE, GNDVI utt.)
3. Konfigurējiet vizualizācijas iestatījumus (LUT krāsas, vērtību diapazoni)
4. Pievienojiet vairākus indeksus pēc nepieciešamības

**Populāri indeksi:**

* **NDVI**: Vispārējais veģetācijas veselības stāvoklis (visbiežāk izmantotais)
* **NDRE**: Agrīna stresa noteikšana ar RedEdge
* **GNDVI**: Jutīgs pret hlorofila koncentrāciju
* **OSAVI**: labi darbojas ar redzamu augsni
* **EVI**: reģioni ar augstu lapu platības indeksu (LAI)

**Pielāgotas formulas (tikai Chloros+):**

* Izveidojiet pielāgotas multispektrālo indeksu formulas
* Izmantojiet joslu matemātiku visiem attēla kanāliem
* Saglabājiet pielāgotās formulas atkārtotai izmantošanai

Visus pieejamos indeksus un formulas skatiet [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

### Eksportēšana

Kontrolē izvades faila formātu un kvalitāti.

**Pieejamie formāti:**

* **TIFF (16 bitu)**: ieteicams GIS un zinātniskai analīzei (diapazons 0–65 535)
* **TIFF (32 bitu, procentos)**: peldošā punkta atstarojuma vērtības (diapazons 0,0–1,0)
* **PNG (8 bitu)**: bezzaudējumu kompresija vizualizācijai (diapazons 0–255)
* **JPG (8 bitu)**: mazākie faili, zaudējumu kompresija (diapazons 0–255)

***

## Iestatījumu saglabāšana un ielāde

### Projekta veidnes saglabāšana

Izveidojiet atkārtoti izmantojamus veidnes vienotai darba plūsmai:

1. Konfigurējiet visus vēlamos iestatījumus paneļā Projekta iestatījumi.
2. Pārvietojieties uz sadaļu **&quot;Saglabāt projekta veidni&quot;** apakšā.
3. Ievadiet aprakstošu veidnes nosaukumu (piemēram, &quot;Survey3N\_RGN\_Agriculture&quot;).
4. Noklikšķiniet uz saglabāšanas ikonas.

**Priekšrocības:**

* Vienādi iestatījumi vairākiem projektiem
* Konfigurāciju koplietošana ar komandas locekļiem
* Atkārtotu aptauju konsekvence

### Veidnes ielāde jaunā projektā

Jaunā projekta izveide:

1. Galvenajā izvēlnē izvēlieties **&quot;Jauns projekts&quot;**.
2. Izvēlieties opciju **&quot;Ielādēt no veidnes&quot;**.
3. Izvēlieties saglabāto veidni.
4. Visi iestatījumi tiek automātiski piemēroti.

### Darba katalogs

Iestatījums **&quot;Saglabāt projekta mapes&quot;** nosaka, kur pēc noklusējuma tiek izveidoti jauni projekti:

* **Noklusējuma atrašanās vieta**: `C:\Users\[Username]\Chloros Projects`
* **Mainīt atrašanās vietu**: noklikšķiniet uz ikonas &quot;Rediģēt&quot; un izvēlieties jaunu mapi
* **Kad mainīt**:
  * Tīkla disks komandas sadarbībai
  * Cits disks ar lielāku uzglabāšanas vietu
  * Organizēta mapju struktūra pēc gada/klienta

***

## PPK (pēc apstrādes kinemātika) iestatījumi

Ja izmantojat MAPIR DAQ ierakstītājus ar GPS precīzai ģeolokācijai:

### Priekšnoteikumi

* MAPIR DAQ ar GPS (GNSS) moduli
* .daq žurnāla fails ar ekspozīcijas pinu ierakstiem
* Kamera, kas savienota ar DAQ ekspozīcijas pīniem uzņemšanas sesijas laikā

### Konfigurācijas soļi

1. Ievietojiet .daq žurnāla failu savā projekta mapē.
2. Projekta iestatījumos atļaujiet **&quot;Piemērot PPK korekcijas&quot;** izvēles rūtiņu.
3. Vajadzības gadījumā iestatiet **&quot;Gaismas sensora laika zonas nobīde&quot;** (noklusējums: 0 UTC).
4. Piešķiriet kameras ekspozīcijas pīniem:
   * **Viena kamera**: Automātiski piešķirta kontaktligzdai 1
   * **Divkāršas kameras**: Manuāli piešķiriet katrai kamerai pareizo kontaktligzdu

**Ekspozīcijas kontaktligzdu piešķiršana:**

* **Ekspozīcijas kontaktligzda 1**: Izvēlieties kameras modeli no nolaižamā izvēlnes
* **Ekspozīcijas kontaktligzda 2**: Izvēlieties otro kameru vai &quot;Nelietot&quot;
* Vienu un to pašu kameru nevar piešķirt abām kontaktligzdām

{% hint style=&quot;warning&quot; %}
**Svarīgi**: Ekspozīcijas tapas ir pareizi jāpiešķir attiecīgajām kamerām. Nepareiza piešķiršana radīs nepareizus ģeolokācijas datus.
{% endhint %}

***

## Papildu scenāriji

### Daudzkameru projekti

Apstrādājot attēlus no vairākām MAPIR kamerām vienā projektā:

1. Chloros automātiski atpazīst katras kameras modeli
2. Katrai kamerai tiek piešķirts atbilstošs apstrādes profils
3. PPK: manuāli piešķiriet katrai kamerai pareizo ekspozīcijas tapu
4. Visas kameras izmanto vienādu eksporta formātu un indeksus

**Piemērs**: Survey3W RGN + Survey3N OCN divkameru platforma

### Laika nobīdes vai vairāku datumu apsekojumi

Atkārtotiem apsekojumiem vienā un tajā pašā teritorijā laika gaitā:

1. Izveidojiet veidni ar standarta iestatījumiem
2. Katrā sesijā izmantojiet vienotu kalibrēšanas mērķa iestatījumu
3. Apstrādājiet katru datumu kā atsevišķu projektu
4. Lai iegūtu salīdzināmus rezultātus, izmantojiet identiskus iestatījumus
5. Eksportējiet vienā formātā laika analīzei

### Lieli datu kopumi

Projektiem ar daudziem attēliem (500+):

* Apsveriet iespēju sadalīt mazākos projektos pēc datuma vai teritorijas.
* Lai iegūtu ātrākus rezultātus, izmantojiet Chloros+ paralēlo apstrādi.
* Apsveriet CLI vai API izmantošanu partiju automatizācijai.
* Pielāgojiet minimālo pārkalibrēšanas intervālu, lai samazinātu mērķa noteikšanas laiku.

***

## Jūsu iestatījumu pārbaude

Pirms sākat apstrādi, pārskatiet šos galvenos iestatījumus:

* [ ] Kameras modelis pareizi noteikts failu pārlūkprogrammā
* [ ] Vignette korekcija ieslēgta
* [ ] Atstarošanas kalibrēšana ieslēgta
* [ ] Importēts vismaz viens kalibrēšanas mērķa attēls
* [ ] Pievienoti vēlamie multispektrālie indeksi
* [ ] Eksporta formāts atbilstošs jūsu darba plūsmai
* [ ] PPK iestatījumi konfigurēti (ja izmantojat .daq ar ekspozīcijas notikumiem)

***

## Nākamie soļi

Kad iestatījumi ir konfigurēti:

1. **Atzīmējiet kalibrēšanas mērķa attēlus** - skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. **Sāciet apstrādi** - skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet progresu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Pilnīga informācija par visiem pieejamajiem iestatījumiem ir atrodama [Projekta iestatījumu](../project-settings/project-settings.md) atsauces dokumentācijā.
