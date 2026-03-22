# Failu pievienošana projektam

Kad esat izveidojis vai atvēris projektu programmā Chloros, nākamais solis ir pievienot multispektrālos attēlus, lai sāktu apstrādi. Izvēlne „File Browser“<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> atvieglo attēlu importēšanu un datu kopas pārvaldīšanu.

## Piekļuve failu pārlūkam

1. Atveriet vai izveidojiet projektu programmā Chloros
2. Noklikšķiniet uz **Failu pārlūks** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
3. Failu pārlūka panelī tiks parādīts jūsu projekta failu saraksts

{% hint style="info" %}
**Atbalstītie failu tipi**: Chloros atbalsta RAW+JPG un JPG attēlu failus no MAPIR, Survey3W un Survey3N kamerām. Ieteicams izmantot tikai RAW+JPG.
{% endhint %}

***

## Attēlu pievienošana projektam

Ir divi galvenie veidi, kā pievienot attēlus projektam:

### 1. metode: Pievienot failus

Izmantojiet šo opciju, lai importētu atsevišķus attēlu failus vai nelielu failu izlasi.

1. Noklikšķiniet uz pogas **&quot;Pievienot failus&quot;** <img src="../.gitbook/assets/image.png" alt="" data-size="line"> poga failu pārlūka paneļa augšdaļā
2. Pāriet uz mapi, kurā atrodas jūsu attēli
3. Izvēlieties vienu vai vairākus attēlu failus (turiet nospiestu **Ctrl**, lai izvēlētos vairākus failus)
4. Noklikšķiniet uz **&quot;Atvērt&quot;**, lai importētu izvēlētos failus

### 2. metode: Pievienot mapi

Izmantojiet šo opciju, lai vienlaikus importētu visus attēlus no mapes.

1. Noklikšķiniet uz pogas **&quot;Pievienot mapi&quot;** <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> poga failu pārlūka paneļa augšdaļā
2. Atveriet un izvēlieties mapi, kurā atrodas jūsu uzņemšanas sesijas attēli
3. Noklikšķiniet uz **&quot;Izvēlēties mapi&quot;**, lai importētu visus atbalstītos attēlus no šīs mapes***

## Failu pārlūka tabulas izpratne

Kad attēli ir importēti, tie parādās tabulā ar šādām kolonnām:

### Faila nosaukums

* Orijinālais faila nosaukums no kameras
* Saglabā kameras nosaukumu konvenciju (piem., IMG\_0001.RAW)

### Laika zīmogs

* Datums un laiks, kad attēls tika uzņemts
* Izgūts no attēla EXIF metadatiem
* Tiek izmantots PPK sinhronizācijai un kalibrēšanas mērķa noteikšanai

### Kameras modelis

* Automātiski noteikta kameras un filtra konfigurācija
* Piemēri: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Tiek izmantots, lai piemērotu pareizos apstrādes profilus

### Mērķa kolonna (izvēles rūtiņa)

* Atzīmējiet šo izvēles rūtiņu attēliem, kuros ir kalibrēšanas mērķi
* Ievērojami paātrina mērķu noteikšanu apstrādes laikā
* Sīkāku informāciju skatiet sadaļā [Mērķa attēlu izvēle](choosing-target-images.md)

### Attēla metadatu apskatīšana

Noklikšķinot uz pārslēgšanas pogas tabulas augšējā labajā stūrī, izvēlētā attēla metadati tiek parādīti attēlu režģa zonā.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Failu pārvaldība projektā

### Failu dzēšana

Lai no projekta dzēstu nevajadzīgos attēlus:

1. Izvēlieties vienu vai vairākus attēlus failu pārlūka tabulā
2. Noklikšķiniet uz pogas **&quot;Dzēst atlasītos&quot;** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line"> pogu
3. Apstipriniet dzēšanu (faili netiek dzēsti no diska, tikai no projekta)

### Šķirošana un filtrēšana

* **Šķirošana pēc kolonnas**: noklikšķiniet uz jebkuras kolonnas virsraksta, lai šķirotu attēlus
* **Šķirošana pēc laika zīmoga**: noderīga hronoloģisku uzņēmumu secību organizēšanai
* **Kameras modeļa filtrs**: grupējiet attēlus pēc kameras tipa, ja izmantojat vairākas kameras***

## Attēla priekšskatījums

### Pilna attēla skatīšana

Noklikšķiniet uz jebkuras attēla sīktēla failu pārlūkā, lai to parādītu galvenajā priekšskatījuma zonā:

1. Attēls parādās centrālajā priekšskatījuma panelī
2. Izmantojiet tālummaiņas vadības elementus, lai apskatītu attēla detaļas
3. Pārvietojieties starp attēliem, izmantojot bultu taustiņus

### Ātrā navigācija

* **Iepriekšējais attēls**: Noklikšķiniet uz kreisās bultas vai nospiediet ← taustiņu
* **Nākamais attēls**: noklikšķiniet uz labās bultiņas vai nospiediet → taustiņu
* **Tuvināšana/attālināšana**: izmantojiet peles ratu vai tālummaiņas pogas
* **Pārvietošana**: noklikšķiniet un velciet attēlu, kad tas ir tuvināts***

## Datu dublikātu apstrāde

Chloros automātiski atpazīst un ignorē datu dublikātus:

* Faili ar identiskiem nosaukumiem tiek izlaisti
* Novērš nejaušu divkāršu apstrādi
* Tiek parādīts brīdinājuma ziņojums, ja tiek atklāti dublikāti

{% hint style="warning" %}
**Svarīgi**: Pirms importēšanas nepārdēvējiet vai nemainiet oriģinālos attēlu failus. Chloros pareizai apstrādei izmanto oriģinālos failu nosaukumus un metadatus.
{% endhint %}

***

## Jaukti kameru datu kopumi

Ja jūsu projektā ir attēli no vairākām MAPIR kamerām:

1. Chloros automātiski atpazīst katru kameras modeli
2. Katrs kameras tips tiek apstrādāts ar atbilstošu kalibrēšanas profilu
3. Failu pārlūks parāda kameras modeli kolonnā „Kameras modelis”
4. Apstrāde piemēro pareizos iestatījumus katram kameras tipam

**Piemērs**: Survey3W RGN + Survey3N OCN divu kameru konfigurācija***

## Labākā prakse

### Organizēšana pirms importēšanas

* Glabājiet kalibrēšanas mērķa attēlus tajā pašā mapē, kurā atrodas uzņemto attēlu mape
* Saglabājiet sākotnējo mapju struktūru no jūsu kameras/SD kartes
* Nevienā projektā nemaisiet datu kopas no dažādām sesijām

### Failu nosaukumi

* Saglabājiet sākotnējos kameras failu nosaukumus (IMG\_0001.RAW utt.)
* Pirms importēšanas nepārdēvējiet failus
* Sākotnējie nosaukumi satur svarīgus metadatus

### Kalibrēšanas mērķa attēli

* Vienmēr iekļaujiet 1–2 kalibrēšanas mērķa attēlus katrā sesijā
* Uzņemiet mērķus pirms un pēc uzņemšanas sesijas
* Novietojiet mērķus tādos pašos apgaismojuma apstākļos kā uzņemšanas zona
* Atzīmējiet mērķa attēlus, izmantojot izvēles rūtiņu „Target”, lai paātrinātu apstrādi

***

## Bieži sastopamas problēmas un to risinājumi

### Attēli neparādās pēc importēšanas

**Iespējamie iemesli:**

* Failu formāts netiek atbalstīts (tikai RAW+JPG un JPG no MAPIR kamerām)
* Attēli ir no kamerām, kas nav MAPIR (skatiet [Atbalstītās kameras](../supported-cameras.md))
* Fails ir bojāts vai pārraide no SD kartes nav pabeigta

**Risinājums**: Pārbaudiet faila formāta un kameras modeļa saderību

### Kameras modelis nav atpazīts

**Iespējamie cēloņi:**

* Modificēti EXIF metadati
* Attēli ir rediģēti ārējā programmā
* Nepabeigta failu pārraide

**Risinājums**: No jauna importējiet oriģinālos, nemodificētos failus no kameras/SD kartes

### Trūkstoši laika zīmogi

**Iespējamie iemesli:**

* Nepareizi iestatīts kameras pulkstenis
* EXIF dati izdzēsti ar ārējo programmatūru

**Risinājums**: Pārbaudiet, vai kameras laika iestatījumi uzņemšanas brīdī bija pareizi***

## Turpmākie soļi

Kad faili ir importēti:

1. **Pārskatiet failu sarakstu** – pārliecinieties, ka visi attēli ir ielādēti pareizi
2. **Pārbaudiet kameru modeļus** — pārliecinieties, ka kamera ir atpazīta pareizi
3. **Atzīmējiet mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
4. **Pielāgojiet iestatījumus** — konfigurējiet apstrādes opcijas [Projekta iestatījumos](adjusting-project-settings.md)
5. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)

Sīkāku informāciju par projekta konfigurāciju skatiet sadaļā [Projekta iestatījumu pielāgošana](adjusting-project-settings.md).
