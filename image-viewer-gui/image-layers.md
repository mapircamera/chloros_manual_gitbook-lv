# Attēla slāņi

Chloros attēlu skatītājā izvēlne „Attēla slāņi” ļauj ātri pārslēgties starp viena un tā paša attēla dažādām versijām — sākot no sākotnējiem uzņēmumiem līdz apstrādātiem atstarojuma rezultātiem un aprēķinātiem indeksa attēliem.

## Kas ir attēla slāņi?

Chloros programmā **slāņi** attiecas uz dažādiem attēlu izvadiem, kas pieejami vienam avota attēlam. Apstrādājot attēlus, Chloros izveido vairākas versijas:

* **Oriģinālie attēli** (JPG un RAW faili no jūsu kameras)
* **Reflektances kalibrēti** izvadi (ja bija ieslēgta reflektances kalibrēšana)
* **Mērķa attēli** (ja attēls satur kalibrēšanas mērķus)
* **Indeksu attēli** (NDVI, NDRE, GNDVI utt., ja indeksi tika konfigurēti)**Slāņu izvēlnes nolaižamais saraksts** attēlu skatītāja augšējā labajā stūrī ļauj jums uzreiz pārslēgties starp šīm versijām, neizejot no skatītāja.***

## Pieejamie slāņu tipi

### JPG

* Orijinālais JPG priekšskatījuma attēls no jūsu kameras
* Vienmēr pieejams visiem attēliem
* Neapstrādāts, tāds, kādu to uzņēma kamera
* Ātrākais ielādēšanā un attēlošanā

**Kad skatīt:**

* Ātra oriģinālā uzņēmuma priekšskatīšana
* Attēla kompozīcijas un kadrējuma pārbaude
* Uzņēmuma kvalitātes pārbaude pirms apstrādes

### RAW (oriģināls)

* Oriģinālie RAW sensora dati no jūsu kameras
* Debayered bez pēcapstrādes
* Augstāka bitu dziļuma nekā JPG (parasti 12-bitu vai 14-bitu sensora dati)

**Kad skatīt:**

* Oriģinālo sensora datu kvalitātes pārbaude
* Sensora problēmu vai artefaktu pārbaude
* Apstrādes rezultātu salīdzināšana pirms un pēc

### RAW (mērķis)

* Parādās tikai attēliem, kuros ir identificēti kalibrēšanas mērķi
* Parāda oriģinālo RAW attēlu ar atklātu mērķi
* Izmanto, lai pārbaudītu, vai mērķa atklāšana ir bijusi veiksmīga

**Kad skatīt:**

* Lai apstiprinātu, ka kalibrēšanas mērķi ir atklāti pareizi
* Lai pārbaudītu mērķa attēla kvalitāti
* Lai novērstu kalibrēšanas problēmas

{% hint style="info" %}
**Mērķa slānis**: Šis slānis parādās izvēlnē tikai attēliem, kuros ir kalibrēšanas mērķi. Parastajiem uzņemtajiem attēliem šī opcija nebūs pieejama.
{% endhint %}

### RAW (atstarošana)

* Kalibrētais atstarošanas izvades attēls
* Vignette korekcija (ja ieslēgta apstrādē)
* Atstarošana kalibrēta, izmantojot mērķa datus (ja ieslēgta)
* Daudzjoslu TIFF ar visiem kameras kanāliem
* Pikseļu vērtības atspoguļo atstarošanas procentu (ja izmanto procentu režīmu)
* Gatavs apstrādei ar [Index/LUT Sandbox](index-lut-sandbox.md)

**Kad skatīt:**

* Kalibrēto rezultātu pārbaude
* Kalibrēšanas kvalitātes pārbaude
* Pikseļu vērtību pārbaude zinātniskās precizitātes nodrošināšanai
* Salīdzināšana ar oriģinālu, lai redzētu kalibrēšanas efektus

{% hint style="success" %}
**Ieteicams**: izmantojiet RAW (atstarošanas) slāni, pārbaudot pikseļu vērtības zinātniskiem mērījumiem un analīzei.
{% endhint %}

### RAW (NDVI indekss)... un līdzīgi

* Aprēķināts veģetācijas indeksa attēls (šajā piemērā NDVI)
* Indeksa nosaukums mainās atkarībā no tā, kurš indekss tika konfigurēts apstrādes laikā
* Piemēri: RAW (NDVI indekss), RAW (NDRE indekss), RAW (GNDVI indekss) utt.
* Vienkanāla pelēktoņu attēls, kas parāda indeksa aprēķinu rezultātus
* Par katru indeksu, kas konfigurēts projekta iestatījumos, parādās viens slānis

**Iespējamie indeksu nosaukumi:**

* RAW (NDVI indekss)
* RAW (NDRE indekss)
* RAW (GNDVI indekss)
* RAW (OSAVI indekss)
* RAW (EVI indekss)
* RAW (SAVI indekss)
* Un daudzi citi... (skatīt [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md))

**Kad skatīt:**

* Indeksa aprēķinu rezultātu pārbaude
* Indeksa vērtību diapazonu pārbaude
* Interesējošo apgabalu identificēšana
* Indeksa attēlu pārbaude pirms izmantošanas GIS vai analīzē

***

## Slāņu izvēlnes izmantošana

### Izvēlnes atvēršana

1. Atveriet attēlu pilnekrāna režīmā (noklikšķiniet uz jebkuras sīktēmas attēlu skatītājā)
2. Atrodiet **slāņu izvēlni** skatītāja augšējā labajā stūrī
3. Izvēlnē tiek parādīts pašlaik izvēlētais slānis (piem., &quot;JPG&quot;)
4. Noklikšķiniet uz izvēlnes, lai redzētu visus pieejamos slāņus

### Slāņu maiņa

1. Noklikšķiniet uz slāņu izvēlnes, lai atvērtu sarakstu
2. Tiek parādīti visi pieejamie slāņi pašreizējam attēlam
3. Noklikšķiniet uz jebkura slāņa nosaukuma, lai pārietu uz šo versiju
4. Attēls nekavējoties atjauninās, lai parādītu izvēlēto slāni

**Ātra pārslēgšanās:**

* Izvēlne atceras jūsu pēdējo izvēli
* Pārejot uz nākamo attēlu, Chloros mēģina parādīt to pašu slāņa tipu
* Ja šāds slānis nākamajā attēlā nepastāv, par noklusējuma slāni tiek izmantots JPG

### Slāņu pieejamība

Ne visi slāņi ir pieejami katram attēlam:

**Vienmēr pieejami:*** ✅ JPG (katram attēlam ir JPG priekšskatījums)

**Pieejami ar nosacījumiem:**

* ⚠️ RAW (oriģināls) — tikai tad, ja attēls ir uzņemts RAW vai RAW+JPG režīmā
* ⚠️ RAW (mērķis) — tikai tad, ja attēls satur atpazītus kalibrēšanas mērķus
* ⚠️ RAW (atstarošana) — tikai pēc apstrādes ar ieslēgtu atstarošanas kalibrēšanu
* ⚠️ RAW (\[Index] indekss) — tikai pēc apstrādes ar konfigurētiem indeksiem

***

## Slāņu saglabāšana

### Pārvietošanās starp attēliem

Kad pārvietojaties uz citu attēlu (izmantojot bultu taustiņus vai noklikšķinot uz sīktēliem):**Slāņa iestatījumi tiek saglabāti:**

* Ja skatāt &quot;RAW (Reflectance)&quot;, nākamais attēls rāda &quot;RAW (Reflectance)&quot; (ja pieejams)
* Ja skatāt &quot;RAW (NDVI Index)&quot;, nākamais attēls rāda &quot;RAW (NDVI Index)&quot; (ja pieejams)
* Ja tāds pats slānis nepastāv, noklusējuma iestatījums ir JPG

**Darba plūsmas piemērs:**

1. Atveriet attēlu 1, pārslēdzieties uz RAW (NDVI Index)
2. Nospiediet →, lai apskatītu attēlu 2
3. Attēls 2 automātiski parāda RAW (NDVI Index) slāni
4. Turpiniet pārlūkošanu — visos attēlos tiek parādīts NDVI slānis
5. Ļoti efektīvs, lai pārskatītu indeksa rezultātus daudzos attēlos

***

## Bieži izmantotas darbplūsmas

### Darbplūsma 1: Salīdzinājums pirms un pēc

**Mērķis**: Salīdzināt oriģinālo attēlu ar kalibrēto attēlu

1. Atveriet apstrādāto attēlu attēlu skatītājā
2. Izvēlieties **RAW (Oriģināls)** no izvēlnes
3. Pievērsiet uzmanību vinjetēšanai un nekalibrētajām vērtībām
4. Pārejiet uz **RAW (Atstarošana)** no izvēlnes
5. Salīdziniet – vinjetēšana noņemta, vērtības kalibrētas

### Darba plūsma 2: Indeksa pārskatīšana

**Mērķis**: Ātri pārskatīt NDVI rezultātus visā datu kopā

1. Atveriet pirmo apstrādāto attēlu
2. Izvēlieties **RAW (NDVI Index)** no izvēlnes
3. Izmantojiet → bultu taustiņu, lai pārietu uz nākamo attēlu
4. NDVI slānis saglabājas automātiski
5. Turpiniet pārskatīt visus attēlus, pārbaudot NDVI modeļus
6. Pāriet uz **RAW (NDRE Index)**, lai salīdzinātu

### Darba plūsma 3: Mērķa pārbaude

**Mērķis**: Pārbaudīt, vai visi mērķa attēli ir atpazīti pareizi

1. Pāriet uz mērķa attēlu
2. Izvēlieties **RAW (Target)** no izvēlnes
3. Pārbaudiet, vai kalibrēšanas mērķi ir skaidri redzami un atpazīti
4. Pāriet uz nākamo mērķa attēlu
5. Atkārtojiet pārbaudi visiem mērķiem

### Darba plūsma 4: Pikseļu vērtību pārbaude

**Mērķis**: Pārbaudīt atstarojuma vērtības zinātniskās precizitātes nodrošināšanai

1. Atveriet apstrādāto attēlu
2. Izvēlieties slāni **RAW (Reflectance)**

3. Iespējojiet režīmu**Pixel Percent** (poga rīkjoslā augšējā labajā stūrī)
4. Pārvietojiet kursoru pār veģetācijas apgabaliem
5. Pārbaudiet, vai pikseļu vērtības atrodas paredzētajā diapazonā (30–70 % attēlam NIR, 5–15 % attēlam Red)
6. Pārbaudiet, vai augsnes un ūdens platību vērtības ir atbilstošas

***

## Pikseļu vērtību izpratne pa slāņiem

Dažādiem slāņiem ir atšķirīgi pikseļu vērtību diapazoni:

### JPG slānis

* **Diapazons**: 0–255 (8 biti)
* **Nozīme**: attēla vērtības, ar gamma korekciju
* **Lietošana**: tikai vizuālai pārbaudei, nevis zinātniskiem mērījumiem

### RAW (oriģināls)

* **Diapazons**: 0–65535 (16 biti)
* **Nozīme**: sensora neapstrādāti digitālie skaitļi
* **Lietošana**: sensora darbības pārbaude, nav kalibrēts

### RAW (atstarošana)

* **Diapazons**: 0–65 535 (16 bitu TIFF) vai 0,0–1,0 (32 bitu procentos)
* **Nozīme**: Kalibrēta atstarojuma procentuālā vērtība
* **Lietošana**: Zinātniskie mērījumi un analīze**16 bitu TIFF gadījumā:**Daliet ar 65 535, lai iegūtu atstarojuma procentuālo vērtību**32 bitu procentuālā vērtība gadījumā:** Vērtības tieši atspoguļo procentuālo vērtību (0,5 = 50 % atstarojums)

### RAW (Indeksa attēli)

* **Diapazons**: Atšķiras atkarībā no indeksa (parasti no -1,0 līdz +1,0 normalizētiem indeksiem)
* **Nozīme**: Indeksa aprēķina rezultāts
* **Piemēri**:
  * NDVI: no -1 līdz +1 (veģetācijai parasti no 0,4 līdz 0,9)
  * NDRE: no -1 līdz +1 (stresa noteikšana)
  * EVI: no 0 līdz 1 (uzlabota veģetācija)

***

## Padomi un labākā prakse

### Efektīva slāņu pārslēgšana

* **Tastatūras saīsnes**: Lai gan slāņiem nav tastatūras saīsnes, navigācijas bultiņas (←/→) darbojas visos slāņos
* **Vienota darba plūsma**: Izvēlieties vienu slāni (piem., NDVI) un pārskatiet visu datu kopu, pirms pāriet uz citu
* **Ātrs salīdzinājums**: Pārslēdzieties starp slāņiem „Original” un „Reflectance”, lai pārbaudītu apstrādes kvalitāti

### Apstrādes ātruma apsvērumi

* **JPG faili ielādējas visātrāk**: izmantojiet tos ātrai navigācijai starp daudziem attēliem
* **RAW slāņi ielādējas lēnāk**: augstāka izšķirtspēja un bitu dziļums
* **Indeksa slāņi**: ātrums ir līdzīgs „Reflectance” slāņiem
* **Pirmā ielāde ir lēnākā**: turpmākie skatījumi uz to pašu slāni tiek saglabāti kešatmiņā un ir ātrāki

### Kvalitātes pārbaude

* **Vienmēr pārbaudiet RAW (oriģinālu)**: pārbaudiet avota datu kvalitāti, pirms uzticaties apstrādātajiem rezultātiem
* **Salīdziniet slāņus**: izmantojiet slāņu pārslēgšanu, lai pārliecinātos, ka apstrāde ir veikta pareizi
* **Pārbaudiet indeksa diapazonus**: izmantojiet režīmu „Pikseļu procenti” ar indeksa slāņiem, lai pārliecinātos, ka vērtības ir saprātīgas***

## Problēmu novēršana

### Slānis nav pieejams

**Problēma**: Gaidītais slānis neparādās nolaižamajā izvēlnē**Iespējamie cēloņi:**

* Attēls netika apstrādāts (pieejami tikai JPG un RAW (oriģināls))
* Apstrādes laikā bija atspoguļojuma kalibrēšana bija atspējota
* Konkrētais indekss nebija konfigurēts projekta iestatījumos
* Attēls ir tikai mērķa attēls (mērķiem netiek ģenerēti indeksi)

**Risinājumi:**

1. Pārbaudiet, vai attēls ir apstrādāts (pārbaudiet izvades mapi, lai atrastu apstrādātos failus)
2. Pārbaudiet projekta iestatījumus, lai pārliecinātos, ka indeksi ir konfigurēti
3. Veiciet atkārtotu apstrādi, aktivizējot vēlamos indeksus

### Tiek parādīts nepareizs slānis

**Problēma**: Attēls atveras negaidītā slānī**Cēlonis**: No iepriekšējā attēla pārnesta slāņa izvēle, bet šis slānis pašreizējā attēlā nepastāv**Risinājums**: Chloros automātiski pārslēdzas uz JPG, ja vēlamais slānis nav pieejams — tā ir normāla darbība

### Nevar redzēt kalibrēšanas mērķus

**Problēma**: RAW (mērķa) slānī netiek parādīta mērķu atpazīšana**Iespējamie cēloņi:**

* Mērķi netika atklāti apstrādes laikā
* Attēls faktiski nesatur mērķus
* Mērķu atklāšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārbaudiet atkļūdošanas žurnālu, vai tajā ir ziņojumi &quot;Mērķis atrasts&quot;
2. Pārbaudiet, vai attēls faktiski satur redzamus kalibrēšanas mērķus
3. Pielāgojiet mērķu atklāšanas iestatījumus projekta iestatījumos
4. Skatīt [Mērķa attēlu izvēle](../processing-images-gui/choosing-target-images.md)

***

## Saistītās funkcijas

### Attēlu skatītāja rīki

Skatot jebkuru slāni, varat izmantot:

* **Tuvināšanas vadības elementus**: palieliniet, lai pārbaudītu detaļas
* **Pārvietošanu**: noklikšķiniet un velciet, lai pārvietotos pa tuvināto attēlu
* **Pikseļu vērtību pārbaude**: skatiet vērtības kursora atrašanās vietā
* **Navigācijas bultiņas**: pārvietojieties starp attēliem, saglabājot slāni
* **Pikseļu procentu režīms**: pārslēdzieties starp DN un procentuālo attēlojumu

Pilnīgu attēlu skatītāja dokumentāciju skatiet sadaļā [Attēla atvēršana pilnekrāna režīmā](opening-an-image-full-screen.md).

### Indeksa/LUT izmēģinājumu vide

Interaktīvai indeksa testēšanai un vizualizācijai:

* **Indeksa aprēķināšana reālajā laikā**: Testējiet dažādas indeksa formulas
* **LUT krāsu kartēšana**: Pielietojiet krāsu gradientus pelēkto toņu indeksiem
* **Vizualizāciju eksportēšana**: Saglabājiet krāsainus indeksa attēlus

Sīkāku informāciju skatiet sadaļā [Indeksa/LUT izmēģinājumu vide](index-lut-sandbox.md).

***

## Nākamie soļi

Tagad, kad jūs saprotat attēlu slāņus:

* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — pilnīga Image Viewer rokasgrāmata
* [**Index/LUT Sandbox**](index-lut-sandbox.md) — interaktīva indeksu vizualizācija
* [**Daudzspektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — pieejamo indeksu atsauce
* [**Apstrādes pabeigšana**](../processing-images-gui/finishing-the-processing.md) — apstrādāto rezultātu izpratne
