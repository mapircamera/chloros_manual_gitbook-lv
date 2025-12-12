# Attēla atvēršana pilnā ekrānā

Chloros attēlu skatītājs nodrošina īpašu pilna ekrāna saskarni multispektrālo attēlu skatīšanai, analīzei un apstrādei. Neatkarīgi no tā, vai skatāt oriģinālos attēlus vai apstrādātos rezultātus, attēlu skatītājs piedāvā jaudīgus rīkus pārbaudei un analīzei.

## Piekļuve attēlu skatītājam

### No failu pārlūka

Visbiežāk izmantotais veids, kā atvērt attēlu attēlu skatītājā:

1. Pārliecinieties, ka atrodaties cilnē **Failu pārlūks** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Noklikšķiniet uz jebkuras **attēla miniaturas** attēlu režģī
3. Attēls atveras **galvenajā priekšskatīšanas zonā** (ekrāna centrā)
4. Attēls tagad ir ielādēts un gatavs pilna ekrāna skatīšanai

### Image Viewer cilnes atvēršana

Kad attēls ir ielādēts priekšskatīšanas zonā:

1. Noklikšķiniet uz **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
2. Atvērsies attēlu skatītāja cilne, kurā izvēlētais attēls tiks parādīts pilna ekrāna režīmā
3. Kreisajā sānjoslā kļūs pieejami papildu skatīšanas un analīzes rīki

***

## Attēlu skatītāja interfeisa pārskats

### Galvenā attēla skatīšanas zona

Lielākā ekrāna daļa parāda jūsu attēlu:

* **Pilna izšķirtspēja**: attēli tiek parādīti oriģinālajā izšķirtspējā
* **Tuvināms**: izmantojiet vadības elementus vai peles ratu, lai tuvinātu attēlu
* **Pārvietojams**: noklikšķiniet un velciet, lai pārvietotos, kad attēls ir tuvināts
* **Saglabāts attēla proporcijas**: attēli tiek proporcionāli mērogi

***

## Skatīšanas opcijas

### Pamata attēlu navigācija

#### Attēlu pārlūkošana

Pārlūkojiet attēlu kopu, izmantojot tastatūras saīsnes vai pogas:

* **Nākamais attēls**: noklikšķiniet uz pogas → vai nospiediet taustiņu **→** (labā bultiņa)
* **Iepriekšējais attēls**: noklikšķiniet uz pogas ← vai nospiediet taustiņu **←** (kreisā bultiņa)
* **Pāriet uz konkrētu attēlu**: atgriezieties failu pārlūkprogrammā un noklikšķiniet uz vēlamā sīkattēla

#### Tuvināšanas vadības elementi

Pielāgojiet palielinājumu, lai apskatītu attēla detaļas:

**Tuvināt:**

* Noklikšķiniet uz **+** (plus) pogas
* Nospiediet **+** vai **=** taustiņu
* Pagrieziet peles ratu **uz augšu**

**Attālināt:**

* Noklikšķiniet uz **−** (mīnus) pogas
* Nospiediet **−** (mīnus) taustiņu
* Pagrieziet peles ratu **uz leju**

**Pielāgot ekrānam:**

* Noklikšķiniet uz **↔** (Pielāgot) pogas
* Nospiediet **0** (Nulle) taustiņu
* Divreiz noklikšķiniet uz attēla

#### Pārvietošana, kad ir palielināts

Kad ir palielināts vairāk nekā ekrāna izmērs:

1. Pārvietojiet peles kursoru uz attēla
2. Noklikšķiniet un **turiet nospiestu peles kreiso pogu**
3. **Velciet**, lai pārvietotu attēlu
4. Atlaidiet, lai pārtrauktu pārvietošanu

**Alternatīva**: izmantojiet bultu taustiņus, lai pārvietotu attēlu nelielos solīšos

***

## Pikseļu vērtību pārbaude

### Pikseļu vērtību skatīšana kursora vietā

Kad pārvietojat peles kursoru pār attēlu, pikseļu vērtības tiek parādītas reāllaikā:

**Vērtību parādīšanas vieta:**

* **Peldošs skaitlis un sarkana līnija labās puses indeksa LUT gradienta leģendā**
* **Kad tiek palielināts attēls, peldoša vērtība pie kursora un izceltā pikseļa**
* Parāda vērtības pikselim **zem kursora vai izceltam**
* Atjaunina, kad pārvietojat peli

***

## Attēlu veidi, kurus varat apskatīt

### Orijinālie attēli (pirms apstrādes)

**RAW + JPG attēli no kameras:**

* Parāda RAW datus kā priekšskatījumu
* Parāda oriģinālās, nekoriģētās vērtības
* Noderīgi attēla kvalitātes pārbaudei pirms apstrādes

### Kalibrēti atstarojuma attēli

**Pēc apstrādes:**

* Korekcija ar vinjeti
* Kalibrēta atstarojamība
* Daudzjoslu TIFF (Red, Green, NIR utt.)
* Zinātniskie dati gatavi analīzei

### Indeksa attēli

**NDVI, NDRE, GNDVI utt. (\_NDVI.tif faili):**

* Vienjoslas pelēktoņu attēli
* Pikseļu vērtības atspoguļo indeksa aprēķina rezultātus
* Normālo indeksu diapazons parasti ir no -1 līdz +1
* Vizualizācijai var piemērot krāsu LUT

***

## Indeksa un LUT piemērošana

Piemērojiet multispektrālos indeksus un krāsu meklēšanas tabulas:

1. Atrodiet **Index/LUT Sandbox** **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu joslā
2. Izvēlieties veģetācijas indeksu (NDVI, NDRE utt.)
3. Izvēlieties multispektrālo formulu vai izveidojiet savu pielāgotu formulu (tikai Chloros+)
4. Lai vizualizētu, piemērojiet krāsu LUT gradientu
5. Pielāgojiet vērtību diapazonus un sliekšņus

Sīkākas instrukcijas skatiet [Indekss/LUT Sandbox](index-lut-sandbox.md).

***

## Tastatūras saīsnes

### Navigācija

* **→** (labā bultiņa): nākamais attēls
* **←** (kreisā bultiņa): iepriekšējais attēls
* **Sākums**: pirmais attēls sarakstā
* **End**: Pēdējais attēls sarakstā

### Tuvināšana

* **+** vai **=**: Tuvināšana
* **−**: Attālināšana
* **0** (Nulle): Pielāgošana ekrānam
* **Peles ritenītis**: Tuvināšana/attālināšana

### Skatīšanas vadības elementi

* **P**: Pārslēgt pikseļu procentu režīmu
* **L**: Pārslēgt slāņu paneli
* **Esc**: Aizvērt pilna ekrāna režīmu vai atgriezties failu pārlūkā

### Citi

* **Ctrl+S**: Saglabāt pašreizējo attēlu
* **F**: Pilna ekrāna režīms (ja pieejams)

***

### Indeksu aprēķinu pārbaude

Pārbaudiet, vai indeksi ir aprēķināti pareizi:

1. Atveriet NDVI vai citu indeksa attēlu.
2. Pārbaudiet veģetācijas platības:
   * **NDVI**: Veseliem augiem jābūt 0,4–0,9.
   * **NDRE**: augstākas vērtības enerģiskai augšanai
   * **GNDVI**: līdzīgs NDVI, bet jutīgs pret hlorofilu
3. Pārbaudiet neaugu zonu:
   * **Augsne**: tuvu 0 vai nedaudz negatīva
   * **Ūdens**: negatīvas vērtības (-0,5 līdz 0)

***

## Problēmu novēršana Attēlu skatīšanas problēmas

### Attēls nevar atvērties

**Iespējamie iemesli:**

* Fails bojāts apstrādes laikā
* Nepiedāvāts faila formāts
* Nepietiekama atmiņa liela izmēra attēlam

**Risinājumi:**

1. Mēģiniet atvērt ārējā skatītājā, lai pārbaudītu faila integritāti
2. Pārbaudiet, vai faila formāts atbilst gaidītajam tipam
3. Aizveriet citas programmas, lai atbrīvotu atmiņu
4. Mēģiniet mazāku/citu attēlu

### Melna vai balta attēla parādīšana

**Iespējamie iemesli:**

* Vērtību diapazons ārpus displeja iespējām
* 32 bitu peldošā attēla ar neparastām vērtībām
* Indeksa aprēķina kļūda

**Risinājumi:**

1. Pārbaudiet pikseļu vērtības — ja visas ir ļoti zemas vai ļoti augstas, pielāgojiet attēla diapazonu.
2. Mēģiniet atvērt QGIS vai līdzīgā programmā ar automātisko diapazona pielāgošanu.
3. Pārbaudiet apstrādes kļūdu žurnālu, lai atrastu kļūdas.

### Pikseļu vērtības šķiet nepareizas

**Iespējamie iemesli:**

* Tiek skatīts nepareizs attēls (oriģināls pret apstrādātu)
* Kalibrēšana nav veikta pareizi
* Gaismas sensora dati nav iekļauti ievadē
* Procentu režīms ir nepareizi pārslēgts

**Risinājumi:**

1. Pārbaudiet, vai skatāties apstrādāto rezultātu (pārbaudiet faila nosaukuma papildu daļu)
2. Pārbaudiet procentu režīma pogas stāvokli
3. Salīdziniet ar zināmiem labiem attēliem no tā paša datu kopuma

***

## Nākamie soļi

Tagad, kad varat skatīt attēlus pilnekrāna režīmā:

* [**Attēlu slāņi**](image-layers.md) — uzziniet par daudzjoslu vizualizāciju
* [**Indekss/LUT smilšu kaste**](index-lut-sandbox.md) — piemērojiet pielāgotus indeksus un krāsu kartēšanu
* [**Daudzjoslu indeksa formulas**](../project-settings/multispectral-index-formulas.md) — izpratne par pieejamajiem indeksiem

Par apstrādes darba plūsmu skatiet:

* [**Attēlu apstrāde (GUI)**](../processing-images-gui/adding-files-to-a-project.md) — pilnīga apstrādes rokasgrāmata
