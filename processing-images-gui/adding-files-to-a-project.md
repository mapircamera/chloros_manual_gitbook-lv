# Failu pievienošana projektam

Kad esat izveidojis vai atvēris projektu Chloros, nākamais solis ir pievienot multispektrālos attēlus, lai sāktu apstrādi. Failu pārlūks<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> atvieglo attēlu importēšanu un datu kopas pārvaldīšanu.

## Piekļuve failu pārlūkprogrammai

1. Atveriet vai izveidojiet projektu Chloros
2. Noklikšķiniet uz **Failu pārlūkprogramma** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
3. Failu pārlūka panelī tiks parādīts jūsu projekta failu saraksts

{% padoms style=&quot;info&quot; %}
**Atbalstītie failu tipi**: Chloros atbalsta RAW+JPG un JPG attēlu failus no MAPIR Survey3W un Survey3N kamerām. Ieteicams izmantot tikai RAW+JPG failus.
{% endhint %}

***

## Attēlu pievienošana projektam

Ir divi galvenie veidi, kā pievienot attēlus projektam:

### 1. metode: failu pievienošana

Izmantojiet šo opciju, lai importētu atsevišķus attēlu failus vai nelielu failu izlasi.

1. Noklikšķiniet uz pogas **&quot;Pievienot failus&quot;** failu pārlūka paneļa augšdaļā.
2. Pāriet uz mapi, kurā atrodas jūsu attēli.
3. Izvēlieties vienu vai vairākus attēlu failus (lai izvēlētos vairākus failus, turiet nospiestu **Ctrl**).
4. Noklikšķiniet uz **&quot;Atvērt&quot;**, lai importētu izvēlētos failus.

### 2. metode: pievienot mapes

Izmantojiet šo opciju, lai vienlaikus importētu visus attēlus no mapes.

1. Noklikšķiniet uz pogas **&quot;Pievienot mapi&quot;** failu pārlūka paneļa augšdaļā.
2. Atrodiet un izvēlieties mapi, kurā atrodas jūsu uzņemtie attēli.
3. Noklikšķiniet uz **&quot;Izvēlēties mapi&quot;**, lai importētu visus atbalstītos attēlus no šīs mapes.

***

## Failu pārlūka tabulas izpratne

Pēc attēlu importēšanas tie parādās tabulā ar šādām kolonnām:

### Sīkattēls

* Katra attēla mazs priekšskatījums.
* Noklikšķiniet uz sīkattēla, lai galvenajā priekšskatījuma zonā apskatītu pilnu attēlu.

### Faila nosaukums

* Orijinālais faila nosaukums no kameras.
* Saglabā kameras nosaukumu konvenciju (piemēram, IMG\_0001.RAW).

### Laika zīmogs

* Attēla uzņemšanas datums un laiks.
* Izvilkts no attēla EXIF metadatiem.
* Izmanto PPK sinhronizācijai un kalibrēšanas mērķa noteikšanai

### Kameras modelis

* Automātiski noteikta kamera un filtra konfigurācija
* Piemēri: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Izmanto, lai piemērotu pareizos apstrādes profilus

### Mērķa kolonna (izvēles rūtiņa)

* Atzīmējiet šo rūtiņu attēliem, kuros ir kalibrēšanas mērķi
* Ievērojami paātrina mērķa noteikšanu apstrādes laikā
* Sīkāku informāciju skatiet sadaļā [Mērķa attēlu izvēle](choosing-target-images.md)

***

## Failu pārvaldība projektā

### Failu dzēšana

Lai dzēstu nevajadzīgos attēlus no projekta:

1. Izvēlieties vienu vai vairākus attēlus failu pārlūka tabulā
2. Noklikšķiniet uz pogas **&quot;Dzēst atlasītos&quot;**
3. Apstipriniet dzēšanu (faili netiek dzēsti no diska, tikai no projekta)

### Šķirošana un filtrēšana

* **Šķirot pēc kolonnas**: noklikšķiniet uz jebkuras kolonnas virsraksta, lai šķirotu attēlus
* **Šķirošana pēc laika zīmoga**: noderīga, lai organizētu hronoloģiskas uzņemšanas secības
* **Kameras modeļa filtrs**: grupējiet attēlus pēc kameras tipa, ja izmantojat vairākas kameras

***

## Attēla priekšskatīšana

### Pilna attēla skatīšana

Noklikšķiniet uz jebkuras attēla sīktēla failu pārlūkā, lai to parādītu galvenajā priekšskatīšanas laukā:

1. Attēls parādās centrālajā priekšskatīšanas panelī
2. Izmantojiet tālummaiņas vadības elementus, lai apskatītu attēla detaļas
3. Pārvietojieties starp attēliem, izmantojot bultu taustiņus

### Ātra navigācija

* **Iepriekšējais attēls**: noklikšķiniet uz kreisās bultiņas vai nospiediet taustiņu ←
* **Nākamais attēls**: noklikšķiniet uz labās bultiņas vai nospiediet taustiņu →
* **Tuvināt/attālināt**: izmantojiet peles ratu vai tuvināšanas pogas
* **Pārvietot**: noklikšķiniet un velciet attēlu, kad tas ir tuvināts

***

## Dublikātu failu apstrāde

Chloros automātiski atpazīst un ignorē dublikātus failus:

* Faili ar identiskiem failu nosaukumiem tiek izlaisti
* Novērš nejaušu divkāršu apstrādi
* Brīdinājuma ziņojums tiek parādīts, ja tiek atrasti dublikāti

{% hint style=&quot;warning&quot; %}
**Svarīgi**: Pirms importēšanas nemainiet oriģinālo attēlu failu nosaukumus un nemodificējiet tos. Chloros pareizai apstrādei izmanto oriģinālos failu nosaukumus un metadatus.
{% endhint %}

***

## Jaukti kameru datu kopumi

Ja jūsu projektā ir attēli no vairākām MAPIR kamerām:

1. Chloros automātiski atpazīst katru kameras modeli.
2. Katrs kameras tips tiek apstrādāts ar atbilstošu kalibrēšanas profilu.
3. Failu pārlūkprogramma parāda kameras modeli kolonnā „Kameras modelis”.
4. Apstrāde piemēro pareizos iestatījumus katram kameras tipam.

**Piemērs**: Survey3W RGN + Survey3N OCN divu kameru konfigurācija

***

## Labākā prakse

### Sakārtot pirms importēšanas

* Kalibrēšanas mērķa attēlus glabājiet tajā pašā mapē, kurā atrodas apsekojuma attēli.
* Saglabājiet kameras/SD kartes sākotnējo mapju struktūru.
* Vienā projektā nemaisiet datu kopas no dažādām sesijām.

### Failu nosaukumi

* Saglabājiet kameras sākotnējos failu nosaukumus (IMG\_0001.RAW utt.).
* Pirms importēšanas failus nepārnosaukiet.
* Sākotnējie nosaukumi satur svarīgus metadatus.

### Kalibrēšanas mērķa attēli

* Vienmēr iekļaujiet 1–2 kalibrēšanas mērķa attēlus katrā sesijā.
* Uzņemiet mērķus pirms un pēc uzņemšanas sesijas.
* Novietojiet mērķus tādos pašos apgaismojuma apstākļos kā uzņemšanas zonā.
* Atzīmējiet mērķa attēlus, izmantojot izvēles rūtiņu „Mērķis”, lai paātrinātu apstrādi.

***

## Bieži sastopamas problēmas un to risinājumi

### Attēli neparādās pēc importēšanas

**Iespējamie iemesli:**

* Failu formāts nav atbalstīts (tikai RAW+JPG un JPG no MAPIR kamerām)
* Attēli ir no kamerām, kas nav MAPIR (skatiet [Atbalstītās kameras](../supported-cameras.md))
* Failu bojājums vai nepilnīga pārsūtīšana no SD kartes

**Risinājums**: Pārbaudiet failu formāta un kameras modeļa saderību.

### Kameras modelis netiek atpazīts

**Iespējamie iemesli:**

* Modificēti EXIF metadati
* Attēli rediģēti ārējā programmā
* Nepilnīga failu pārsūtīšana

**Risinājums**: Atkārtoti importējiet oriģinālos, nemodificētos failus no kameras/SD kartes.

### Trūkstoši laika zīmogi

**Iespējamie iemesli:**

* Kameras pulkstenis nav pareizi iestatīts
* EXIF dati izdzēsti ar ārējo programmatūru

**Risinājums**: Pārbaudiet, vai kameras laika iestatījumi uzņemšanas laikā bija pareizi

***

## Nākamie soļi

Kad faili ir importēti:

1. **Pārskatiet failu sarakstu** - Pārliecinieties, ka visi attēli ir pareizi ielādēti
2. **Pārbaudiet kameras modeļus** - Pārbaudiet, vai kamera ir pareizi atpazīta
3. **Atzīmējiet mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
4. **Pielāgojiet iestatījumus** — konfigurējiet apstrādes opcijas [Projekta iestatījumi](adjusting-project-settings.md)
5. **Sāciet apstrādi** – skatiet [Apstrādes sākšana](starting-the-processing.md)

Sīkāku informāciju par projekta konfigurāciju skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md).
