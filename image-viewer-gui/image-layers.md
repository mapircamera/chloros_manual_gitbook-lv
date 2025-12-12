# Attēlu slāņi

Chloros attēlu skatītāja izvēlne „Attēlu slāņi” ļauj ātri pārslēgties starp dažādām viena attēla versijām — no oriģinālajiem uzņēmumiem līdz apstrādātiem atstarojuma izvades attēliem un aprēķinātiem indeksa attēliem.

## Kas ir attēlu slāņi?

Chloros programmā **slāņi** attiecas uz dažādiem attēlu izvades veidiem, kas pieejami vienam avota attēlam. Kad apstrādājat attēlus, Chloros izveido vairākas versijas:

* **Oriģinālie attēli** (JPG un RAW faili no jūsu kameras)
* **Reflektances kalibrēti** rezultāti (ja reflektances kalibrēšana bija ieslēgta)
* **Mērķa attēli** (ja attēls satur kalibrēšanas mērķus)
* **Indeksa attēli** (NDVI, NDRE, GNDVI utt., ja indeksi bija konfigurēti)

**Sluoksņu izvēlnes nolaižamais izvēlnes elements** attēlu skatītāja augšējā labajā stūrī ļauj jums uzreiz pārslēgties starp šīm versijām, neizejot no skatītāja.

***

## Pieejamie slāņu tipi

### JPG

* Orijināls JPG priekšskatījuma attēls no jūsu kameras
* Vienmēr pieejams visiem attēliem
* Nepārstrādāts, tāds, kāds uzņemts ar kameru
* Ātrākais ielādēšanas un parādīšanas veids

**Kad skatīt:**

* Ātrs oriģinālā uzņēmuma priekšskatījums
* Attēla kompozīcijas un kadrējuma pārbaude
* Uzņēmuma kvalitātes pārbaude pirms apstrādes

### RAW (oriģināls)

* Oriģinālie RAW sensora dati no jūsu kameras
* Debayered bez pēcapstrādes
* Augstāka bitu dziļuma nekā JPG (parasti 12 bitu vai 14 bitu sensora dati)

**Kad skatīt:**

* Oriģinālo sensora datu kvalitātes pārbaude
* Sensora problēmu vai artefaktu pārbaude
* Salīdzināšana pirms/pēc apstrādes rezultātiem

### RAW (mērķis)

* Parādās tikai attēliem, kuros ir identificēti kalibrēšanas mērķi
* Parāda oriģinālo RAW attēlu ar atklāto mērķi
* Izmanto, lai pārbaudītu, vai mērķa atklāšana ir bijusi veiksmīga

**Kad skatīt:**

* Lai apstiprinātu, ka kalibrēšanas mērķi ir atklāti pareizi
* Lai pārbaudītu mērķa attēla kvalitāti
* Lai novērstu kalibrēšanas problēmas

{% hint style=&quot;info&quot; %}
**Mērķa slānis**: Šis slānis parādās tikai izvēlnē attēliem, kas satur kalibrēšanas mērķus. Parastajiem uzņemtajiem attēliem šī opcija nav pieejama.
{% endhint %}

### RAW (atstarošanās)

* Kalibrēts atstarojuma izvades attēls
* Vignette koriģēts (ja iespējots apstrādē)
* Atstarojums kalibrēts, izmantojot mērķa datus (ja iespējots)
* Daudzjoslu TIFF ar visiem kameras kanāliem
* Pikseļu vērtības attēlo atstarojuma procentu (ja izmanto procentu režīmu)
* Gatavs apstrādei ar [Index/LUT Sandbox](index-lut-sandbox.md)

**Kad skatīt:**

* Kalibrēto rezultātu pārbaude
* Kalibrēšanas kvalitātes pārbaude
* Pikseļu vērtību pārbaude zinātniskās precizitātes nodrošināšanai
* Salīdzināšana ar oriģinālu, lai redzētu kalibrēšanas efektus

{% hint style=&quot;success&quot; %}
**Ieteicams**: izmantojiet RAW (atstarošanas) slāni, pārbaudot pikseļu vērtības zinātniskajiem mērījumiem un analīzei.
{% endhint %}

### RAW (NDVI indekss)... un līdzīgi

* Aprēķinātais veģetācijas indeksa attēls (šajā piemērā NDVI)
* Indeksa nosaukums mainās atkarībā no tā, kurš indekss tika konfigurēts apstrādes laikā
* Piemēri: RAW (NDVI indekss), RAW (NDRE indekss), RAW (GNDVI indekss) utt.
* Vienjoslas pelēktoņu attēls, kas parāda indeksa aprēķina rezultātus
* Katram projektā konfigurētajam indeksam parādās viens slānis

**Iespējamie indeksa nosaukumi:**

* RAW (NDVI indekss)
* RAW (NDRE indekss)
* RAW (GNDVI indekss)
* RAW (OSAVI indekss)
* RAW (EVI indekss)
* RAW (SAVI indekss)
* Un daudzi citi... (skatīt [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md))

**Kad skatīt:**

* Indeksa aprēķina rezultātu pārbaude
* Indeksa vērtību diapazonu pārbaude
* Interesējošo apgabalu identificēšana
* Indeksa attēlu pārbaude pirms izmantošanas GIS vai analīzē

***

## Slāņu izvēlnes izmantošana

### Izvēlnes atvēršana

1. Atveriet attēlu pilnekrāna režīmā (noklikšķiniet uz jebkuras attēla skatītāja sīktēla)
2. Atrodiet **slāņu izvēlni** skatītāja augšējā labajā stūrī
3. Izvēlnē tiek parādīts pašlaik izvēlētais slānis (piemēram, &quot;JPG&quot;)
4. Noklikšķiniet uz izvēlnes, lai redzētu visus pieejamos slāņus

### Slāņu pārslēgšana

1. Noklikšķiniet uz slāņu izvēlnes, lai atvērtu sarakstu
2. Tiek parādīti visi pieejamie slāņi pašreizējam attēlam
3. Noklikšķiniet uz jebkura slāņa nosaukuma, lai pārslēgtos uz šo versiju
4. Attēls tiek nekavējoties atjaunināts, lai parādītu izvēlēto slāni.

**Ātra pārslēgšanās:**

* Izvēlne atceras jūsu pēdējo izvēli.
* Pārejot uz nākamo attēlu, Chloros mēģina parādīt to pašu slāņa tipu.
* Ja šis slānis nākamajā attēlā nepastāv, tiek izmantots JPG.

### Slāņu pieejamība

Ne visi slāņi ir pieejami katram attēlam:

**Vienmēr pieejami:**

* ✅ JPG (katram attēlam ir JPG priekšskatījums)

**Pieejami ar nosacījumiem:**

* ⚠️ RAW (oriģināls) — tikai tad, ja attēls ir uzņemts RAW vai RAW+JPG režīmā
* ⚠️ RAW (mērķis) — tikai tad, ja attēls satur atklātus kalibrēšanas mērķus
* ⚠️ RAW (atstarošanās) — tikai pēc apstrādes ar ieslēgtu atstarošanās kalibrēšanu
* ⚠️ RAW (\[indekss] indekss) — tikai pēc apstrādes ar konfigurētiem indeksiem

***

## Slāņu saglabāšana

### Pārvietošanās starp attēliem

Kad pārvietojaties uz citu attēlu (izmantojot bultu taustiņus vai noklikšķinot uz sīktēliem):

**Sluoksnes iestatījumi tiek saglabāti:**

* Ja skatāties &quot;RAW (Reflektance)&quot;, nākamais attēls parāda &quot;RAW (Reflektance)&quot; (ja pieejams)
* Ja skatāties &quot;RAW (NDVI Indekss)&quot;, nākamais attēls parāda &quot;RAW (NDVI Indekss)&quot; (ja pieejams)
* Ja tāds pats slānis nepastāv, noklusējuma iestatījums ir JPG

**Piemērs darba plūsmā:**

1. Atveriet attēlu 1, pārslēdzieties uz RAW (NDVI Index)
2. Nospiediet →, lai apskatītu attēlu 2
3. Attēls 2 automātiski parāda slāni RAW (NDVI indekss)
4. Turpiniet navigāciju — visi attēli parāda slāni NDVI
5. Ļoti efektīvs, lai pārskatītu indeksa rezultātus daudzos attēlos

***

## Bieži izmantotas darba plūsmas

### Darba plūsma 1: Salīdzinājums pirms/pēc

**Mērķis**: salīdzināt oriģinālo attēlu ar kalibrēto attēlu

1. Atveriet apstrādāto attēlu attēlu skatītājā
2. Izvēlieties **RAW (oriģināls)** no nolaižamā izvēlnes
3. Pievērsiet uzmanību vinjetēšanai un nekalibrētajām vērtībām
4. Pārejiet uz **RAW (atstarošanās)** no nolaižamā izvēlnes
5. Salīdziniet — vinjetēšana ir noņemta, vērtības ir kalibrētas

### Darba plūsma 2: indeksa pārskatīšana

**Mērķis**: ātri pārskatīt NDVI rezultātus visā datu kopā

1. Atveriet pirmo apstrādāto attēlu.
2. Izvēlieties **RAW (NDVI indekss)** no nolaižamā izvēlnes.
3. Izmantojiet → bultu taustiņu, lai pārietu uz nākamo attēlu.
4. NDVI slānis saglabājas automātiski.
5. Turpiniet pārskatīt visus attēlus, pārbaudot NDVI modeļus.
6. Pārejiet uz **RAW (NDRE Index)**, lai salīdzinātu.

### Darba plūsma 3: Mērķa pārbaude

**Mērķis**: Pārbaudīt, vai visi mērķa attēli ir pareizi atpazīti

1. Pāriet uz mērķa attēlu
2. Izvēlieties **RAW (Target)** no nolaižamā izvēlnes
3. Pārbaudiet, vai kalibrēšanas mērķi ir skaidri redzami un atpazīti
4. Pāriet uz nākamo mērķa attēlu
5. Atkārtojiet pārbaudi visiem mērķiem

### Darba plūsma 4: Pikseļu vērtību pārbaude

**Mērķis**: Pārbaudīt atstarojuma vērtības, lai nodrošinātu zinātnisko precizitāti

1. Atveriet apstrādāto attēlu
2. Izvēlieties slāni **RAW (Atstarojums)**
3. Aktivizējiet režīmu **Pikseļu procenti** (poga augšējā labajā rīkjoslā)
4. Pārvietojiet kursoru pār veģetācijas zonām
5. Pārbaudiet, vai pikseļu vērtības atbilst sagaidāmajam diapazonam (30–70 % NIR gadījumā, 5–15 % Red gadījumā).
6. Pārbaudiet, vai augsnes un ūdens teritorijām ir atbilstošas vērtības.

***

## Pikseļu vērtību izpratne pēc slāņa

Dažādi slāņi parāda atšķirīgas pikseļu vērtību diapazonus:

### JPG slānis

* **Diapazons**: 0–255 (8 biti)
* **Nozīme**: attēlo vērtības, gamma korekcija
* **Lietošana**: tikai vizuāla pārbaude, nevis zinātniski mērījumi

### RAW (oriģināls)

* **Diapazons**: 0–65535 (16 biti)
* **Nozīme**: Neapstrādāti sensora digitālie skaitļi
* **Lietošana**: sensora darbības pārbaude, nav kalibrēts

### RAW (atstarošanās)

* **Diapazons**: 0–65 535 (16 bitu TIFF) vai 0,0–1,0 (32 bitu procenti)
* **Nozīme**: kalibrēta procentuālā atstarošanās
* **Lietojums**: zinātniskie mērījumi un analīze

**16 bitu TIFF gadījumā:** daliet ar 65 535, lai iegūtu procentuālo atstarošanas koeficientu **32 bitu procentu gadījumā:** vērtības tieši atspoguļo procentu (0,5 = 50 % atstarošanas koeficients)

### RAW (indeksa attēli)

* **Diapazons**: atšķiras atkarībā no indeksa (parasti no -1,0 līdz +1,0 normalizētiem indeksiem)
* **Nozīme**: indeksa aprēķina rezultāts
* **Piemēri**:
  * NDVI: no -1 līdz +1 (vegetācija parasti no 0,4 līdz 0,9)
  * NDRE: no -1 līdz +1 (stresa noteikšana)
  * EVI: no 0 līdz 1 (uzlabota veģetācija)

***

## Padomi un labākā prakse

### Efektīva slāņu pārslēgšana

* **Tastatūras saīsnes**: lai gan slāņiem nav tastatūras saīsnes, navigācijas bultiņas (←/→) darbojas visos slāņos
* **Vienota darba plūsma**: izvēlieties vienu slāni (piemēram, NDVI) un pārskatiet visu datu kopu, pirms pāriet uz citu
* **Ātrs salīdzinājums**: pārslēdzieties starp Original un Reflectance, lai pārbaudītu apstrādes kvalitāti

### Veiktspējas apsvērumi

* **JPG tiek ielādēts visātrāk**: izmantojiet ātrai navigācijai pa daudziem attēliem.
* **RAW slāņi tiek ielādēti lēnāk**: augstāka izšķirtspēja un bitu dziļums.
* **Indeksa slāņi**: ātrums ir līdzīgs atstarojuma slāņiem.
* **Pirmā ielāde ir lēnākā**: turpmākie skatījumi uz to pašu slāni tiek saglabāti cache atmiņā un ir ātrāki.

### Kvalitātes pārbaude

* **Vienmēr pārbaudiet RAW (oriģinālu)**: Pārbaudiet avota datu kvalitāti, pirms uzticaties apstrādātajiem rezultātiem
* **Salīdziniet slāņus**: izmantojiet slāņu pārslēgšanu, lai pārbaudītu, vai apstrāde ir veikta pareizi
* **Pārbaudiet indeksa diapazonus**: izmantojiet Pixel Percent režīmu ar indeksa slāņiem, lai pārbaudītu, vai vērtības ir saprātīgas

***

## Problēmu novēršana

### Slānis nav pieejams

**Problēma**: gaidītais slānis neparādās nolaižamajā izvēlnē

**Iespējamie iemesli:**

* Attēls nav apstrādāts (pieejami tikai JPG un RAW (oriģināls))
* Apstrādes laikā atstarojuma kalibrēšana bija atspējota
* Projektu iestatījumos nav konfigurēts konkrēts indekss
* Attēls ir tikai mērķa attēls (mērķiem nav ģenerēti indeksi)

**Risinājumi:**

1. Pārbaudiet, vai attēls ir apstrādāts (pārbaudiet apstrādāto failu izvades mapi)
2. Pārbaudiet projektu iestatījumus, lai pārliecinātos, ka indeksi ir konfigurēti
3. Apstrādājiet atkārtoti, aktivizējot vēlamos indeksus

### Tiek parādīts nepareizs slānis

**Problēma**: attēls atveras negaidītā slānī

**Cēlonis**: iepriekšējā attēla slāņa iestatījumi ir pārnesti, bet šis slānis pašreizējā attēlā nepastāv

**Risinājums**: Chloros automātiski pārslēdzas uz JPG, ja vēlamais slānis nav pieejams — tā ir normāla darbība

### Nevar redzēt kalibrēšanas mērķus

**Problēma**: RAW (mērķa) slānis neparāda mērķa atklāšanu.

**Iespējamie cēloņi:**

* Mērķi netika atklāti apstrādes laikā.
* Attēls faktiski nesatur mērķus.
* Mērķa atklāšanas iestatījumi ir pārāk stingri.

**Risinājumi:**

1. Pārbaudiet debug log, vai tajā ir ziņojumi „Target found” (Mērķis atrasts).
2. Pārbaudiet, vai attēls faktiski satur redzamus kalibrēšanas mērķus
3. Pielāgojiet mērķu noteikšanas iestatījumus projekta iestatījumos
4. Skatīt [Mērķa attēlu izvēle](../processing-images-gui/choosing-target-images.md)

***

## Saistītās funkcijas

### Attēlu skatītāja rīki

Skatot jebkuru slāni, varat izmantot:

* **Tuvināšanas vadības elementus**: palieliniet, lai apskatītu detaļas
* **Pārvietošanu**: Noklikšķiniet un velciet, lai pārvietotos pa palielināto attēlu
* **Pikseļu vērtību pārbaude**: skatiet vērtības kursora atrašanās vietā
* **Navigācijas bultiņas**: pārvietojieties starp attēliem, saglabājot slāni
* **Pikseļu procentu režīms**: pārslēdzieties starp DN un procentuālo attēlojumu

Skatīt [Attēla atvēršana pilnā ekrānā](opening-an-image-full-screen.md), lai iegūtu pilnīgu attēlu skatītāja dokumentāciju.

### Indeksa/LUT smilšu kaste

Interaktīvai indeksa testēšanai un vizualizācijai:

* **Reāllaika indeksa aprēķināšana**: Testējiet dažādas indeksa formulas
* **LUT krāsu kartēšana**: Piemērojiet krāsu gradientus pelēkto toņu indeksiem
* **Eksportējiet vizualizācijas**: Saglabājiet krāsainus indeksa attēlus

Sīkāku informāciju skatiet sadaļā [Indeksa/LUT smilšu kaste](index-lut-sandbox.md).

***

## Nākamie soļi

Tagad, kad jūs saprotat attēlu slāņus:

* [**Attēla atvēršana pilnā ekrānā**](opening-an-image-full-screen.md) - pilnīga Image Viewer rokasgrāmata
* [**Index/LUT Sandbox**](index-lut-sandbox.md) - interaktīva indeksa vizualizācija
* [**Daudzspektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — pieejamo indeksu atsauce
* [**Apstrādes pabeigšana**](../processing-images-gui/finishing-the-processing.md) — apstrādāto rezultātu izpratne
