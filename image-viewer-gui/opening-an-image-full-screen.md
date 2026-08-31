# Attēla atvēršana pilnekrāna režīmā

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>Attēls atvērts pilnekrāna režīmā, ar slāņu izvēlni augšējā labajā stūrī</p></figcaption></figure>

Chloros attēlu skatītājs ir pilnekrāna saskarne jūsu attēlu skatīšanai, pārbaudīšanai un mērīšanai. Tieši šeit jūs varat nolasīt **reālās pikseļu vērtības** — DN katram kanālam, atstarošanas procentus vai starojuma intensitāti W/m²/sr/nm — nevis izstiepto priekšskatījumu, ko attēlo ekrāns.

## Piekļuve attēlu skatītājam

### No failu pārlūka

1. Atveriet cilni **Failu pārlūks** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Noklikšķiniet uz jebkuras **sīktēla** [attēlu režģī](image-grid.md)
3. Attēls atveras pilnekrāna režīmā cilnē **Attēlu skatītājs**

Attēls atveras tajā produktā, kuru rādīja rāsts. Ja rāsts ir iestatīts uz `RAW (Reflectance)`, tad tieši šajā slānī jūs nonāksiet.

### Attēlu skatītāja sānu joslas atvēršana

Noklikšķiniet uz **Attēlu skatītāja** ikonas <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kreisajā sānu joslā, lai izslīdētu analīzes paneli. Tajā no augšas uz leju atrodas:

* attēla nosaukums un tā kameras modelis
* poga **Eksportēt/saglabāt attēlu(-us)** (tikai tad, ja ir aktīvs indekss vai LUT)
* **Indeksa**un**LUT** izvēles rūtiņas un indeksa konfigurācijas panelis — skatiet [Indeksa/LUT izmēģinājumu vidi](index-lut-sandbox.md)
* panelis **Kursora vērtības**: nolasījums pa kanāliem, slāņa histogramma un GSD vadības elements***

## Navigācija un tuvināšana

### Attēlu pārlūkošana

* **Nākamais attēls**: poga → vai taustiņš**→** (labā bultiņa)
* **Iepriekšējais attēls**: poga ← vai taustiņš**←** (kreisā bultiņa)
* **Pāreja uz konkrētu attēlu**: atgriezieties pie režģa un noklikšķiniet uz attēla sīktēla

Tuvināšana un panoramēšana saglabājas, pārvietojoties starp attēliem, tādējādi varat pārskatīt attēlu kopu, paliekot tajā pašā kadra daļā.

### Tuvināšana

Tuvināšanu vada ar **peles ratiņu**, 15 % soļos, fiksējot uz kursora — punkts zem rādītāja paliek zem rādītāja. Tā diapazons ir ierobežots ar attēla un loga izmēru: jūs nevarat samazināt attēlu tālāk par „pielāgot logam”, un augšējā robeža ir noteikta ar attēla sākotnējo izšķirtspēju.

Pilnekrāna skatītājā nav atsevišķu tālummaiņas taustiņu. (Tīklā **Ctrl + `+` / `−`** maina sīktēlu izmēru — tas ir atsevišķs vadības elements.)

### Pārvietošana, kad attēls ir palielināts

Noklikšķiniet un turiet nospiestu peles kreiso pogu virs attēla un velciet. Pārvietošana ir ierobežota, tāpēc attēlu nevar velkt ārpus ekrāna.

### Pārbaude pa pikseļiem lielā palielinājumā

Kad efektīvais palielinājums pārsniedz **60×**, Chloros ap atsevišķo attēloto pikseli zem kursora iezīmē izceltu lodziņu un blakus tam parāda peldošu vērtību.

„Efektīvais” palielinājums ņem vērā GSD bloka izmēru: ja bloka izmērs ir 8, izcelšana parādās jau pie 7,5× palielinājuma, nevis pie 60×, jo viens attēlotais pikselis jau atbilst 8 × 8 avota pikseļiem. Samazinot palielinājumu zem sliekšņa, izcelšana pazūd.

### Tastatūras saīsnes

| Taustiņš                             | Kur       | Darbība                              |
| ------------------------------- | ----------- | ----------------------------------- |
| **→**                           | Pilnekrāna režīms | Nākamais attēls                          |
| **←**                           | Pilnekrāna režīms | Iepriekšējais attēls                      |
| **Ctrl + R**                    | Pilnekrāna režīms | Atjaunot indeksa/LUT izmēģinājumu vidi         |
| **Ctrl + `+`**/**Ctrl + `=`** | Tīkls        | Lielākas sīktēmas (4 pikseļi katram nospiedienam)  |
| **Ctrl + `−`**                  | Tīkls        | Mazākas sīktēmas (4 pikseļi katram nospiedienam) |***

## Kursora vērtības

Pārvietojiet kursoru pār attēlu, un panelī **Kursora vērtības** tiks parādītas katra zem tā esošā kanāla vērtības.

{% hint style="success" %}
**Tās ir faila reālās vērtības.** Ekrānā redzamais kanvas ir 8 bitu izstiepts priekšskatījums, kas nevar nodrošināt šos rādītājus, tāpēc Chloros no faktiskā faila ņem paraugus, lai parādītu rādījumus. Tāpēc 12 bitu neapstrādāts kadrs parāda vērtības, kas pārsniedz 255, un float32 starojuma slānis parāda fiziskās vienības.
{% endhint %}

### Kolonnu nozīme

Panelis pielāgojas slānim, kuru jūs skatāties:

| Skatāmais slānis              | Rādītās kolonnas    | Piezīmes                                                                                           |
| ---------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------- |
| Atstarošanas koeficients                        | **DN**un**%** | Procentuālā vērtība tiek aprēķināta, izmantojot šī faila paša mērogu — skatīt zemāk                                      |
| Starojuma intensitāte                           | **W/m²/sr/nm**   | Fizikālās vērtības ar peldošo komatu; nav DN kolonnas, jo DN šeit nav nozīmīgs                           |
| Neapstrādāts / Debayered / priekšskatījums / JPG    | **DN**           | Veseli skaitļi                                                                         |
| 32bitu procentuālā atstarojamība | tikai **%**       | Saglabātā peldošā komata vērtība nav DN, tāpēc, noapaļojot to līdz veselskaitlim, tiktu izdrukāts bezjēdzīgs `0` vai `1` |

Katra rinda ir apzīmēta ar jūsu kameras filtra kanāla nosaukumu — `Red / Green / NIR` attiecībā uz RGN, `Orange / Cyan / NIR` attiecībā uz OCN, `NIR / Green / Blue` — NGB, `Red / Green / Blue` — RGB, kā arī vienkanāla nosaukums kamerām RE, NIR un mono M3M kamerām. Katram marķējumam ir krāsains punkts, kas atbilst kanālu apļiem, kurus izmanto indeksa formulas redaktorā.

Saglabātie **indeksa un LUT** attēli ir īpašs gadījums: tie satur krāsu kartes komponentus, nevis spektrālos joslu, tāpēc to rindas ir apzīmētas ar `Red / Green / Blue` (vai `Index` vienkanāla indeksa failam), nevis ar kameras filtru nosaukumiem.

Kad indekss ir aktīvs „smilšu kastē”, zem kanāliem parādās papildu rinda, kurā redzama **indeksa vērtība** kursora atrašanās vietā, kopā ar indeksa nosaukumu un baltu punktu, kas atbilst tā marķierim histogrammā.

### Atstarošanas procenti izmanto katra faila paša mērogu

{% hint style="warning" %}
**Neuzskatiet, ka 65535 = 100 %.** Chloros saglabā atstarojumu dažādās skalās atkarībā no tā, kura kamera to radījusi, un skatītājs katram failam nosaka pareizo.
{% endhint %}

| Avots                  | DN, kas atbilst atstarošanas koeficientam 1,0 | Kā to identificē                                                                                                                               |
| ----------------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LATTICE**(M3C / M3M) |**32768**                      | XMP birka `Chloros:PixelScale=32768` tiek ierakstīta katrā LATTICE atstarojuma eksportā. 2× rezerves diapazons ļauj failā iekļaut ρ vērtības, kas pārsniedz 1,0, neizraisot nogriešanu |
| **Survey3**|**65535**                      | Nav Chloros XMP mēroga tag — Survey3 kalibrēšana ieraksta ρ × dtype-max un nogriež pie 1,0                                                               |

Skatītājs, indeksa/LUT smilšu kaste un indeksa eksports visi aprēķina mērogu, izmantojot vienu un to pašu īstenojumu, tādēļ vērtība, ko redzat pie kursora, ir tā pati vērtība, ko izmanto indeksa aprēķini.

Divas sekas, par kurām ir vērts zināt:

* **32 bitu procentuālais**TIFF saglabā DN/65535 kā peldošā punkta skaitli, savukārt**8 bitu** PNG/JPG eksports saglabā DN × 255/65535 — skatītājs abus pārvērš atpakaļ, pirms izdrukā procentuālo vērtību.
* Vienu gadījumu nevar atjaunot: **8-bitu TIFF eksports no 8-bitu avota uzņēmuma** tiek nogriezts diapazonā 0–255, nevis pārskalots, un tam apzināti nav pievienota mēroga atzīme. Šiem failiem panelis izdrukā tikai DN, bez procentu kolonnas. Šī ir godīga atbilde, nevis kļūda.***

## Slāņa histogramma

Zem kursora rindām atrodas reāllaika histogramma par slāni, kuru jūs skatāties, **256 intervālos**. Pēc noklusējuma tiek attēlota viena apvienota līkne, svērta `(R + 2G + B) / 4` — tajā pašā mērījumu telpā, ko izmanto LATTICE kameru histogrammas. Ieslēdzot**RGB** , tā tiek aizstāta ar līknēm katram kanālam atsevišķi kanālu krāsās, kas tiek aditīvi sajauktas, lai pārklāšanās joprojām būtu salasāmas. Mono slāņiem vienmēr tiek attēlota viena līkne.

Horizontālā ass ir slāņa paša vienībās:

| Slānis       | Ass vienība  | Ass maksimālā vērtība                                               |
| ----------- | ---------- | ---------------------------------------------------------- |
| Atstarošanās | procenti    | 125% — produkta rezerves diapazons ļauj ρ vērtībai pārsniegt 1,0           |
| Starojums    | W/m²/sr/nm | Kadra maksimālā vērtība, noapaļota līdz divām nozīmīgajām cipārām |
| 8-bitu dati  | DN         | 255                                                        |
| 12-bitu dati | DN         | 4095                                                       |
| 16-bitu dati | DN         | 65535                                                      |

Ja ass ir iestatīta uz DN un atrodas uz viena no šiem trim maksimālajiem rādītājiem, Chloros zina arī to, kāda ir skatāmā attēla bitu dziļums.

Virs histogrammas atrodas trīs pogas:

| Poga     | Noklusējums | Efekts                                                                                                                                                                                                                                                                                   |
| ---------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **KURSORS** | Ieslēgts | Uzzīmē marķierlīnijas uz histogrammas tieši pie tām vērtībām, kas norādītas rindās augšā, lai jūs varētu redzēt, kur kadrā atrodas pikselis zem jūsu kursora. RGB režīmā katram kanālam ir viens marķieris savā krāsā; pretējā gadījumā tiek parādīts viens balts marķieris pie apvienotās vērtības |
| **INDEKSS**| Ieslēgts      | Parādās tikai tad, ja ir aktīvs indekss. Pārslēdz histogrammu no avota joslām uz**indeksa vērtību sadalījumu**, kur abas klipēšanas sliekšņa līnijas tiek attēlotas kā oranžas pārtrauktas līnijas, bet kursora indeksa vērtība — kā balta līnija                                                          |
| **RGB**| Izslēgts     | Pārslēdz no apvienotās līknes uz atsevišķām kanālu līknēm. Mono sensora gadījumā šī poga rāda uzrakstu**MONO** un ir atspējota — ir tikai viens kanāls, ko parādīt                                                                                                                                  |

Histogramma tiek aprēķināta pēc **redzamajiem blokiem**, nevis pēc avota pikseļiem, kas atrodas aiz tiem: mainot GSD bloka izmēru, sadalījums tiek pārrēķināts, tādējādi histogramma, kursora marķieris un attēlotais attēls vienmēr saskan.***

## GSD bloka izmērs

Paneļa apakšdaļā atrodas **GSD (px)**vadības elements: skaitļu lauks, slīdnis no**1 līdz 256**un**RESET** poga.

Tas padara _parādīto_ attēlu rupjāku, aprēķinot vidējo vērtību N × N avota pikseļu blokam un pārvēršot to vienā parādāmajā pikselī. `1` ir oriģinālā izšķirtspēja.

* Tas ietekmē **pilnekrāna skatu, režģa sīktēlus, kursora rādījumus un abas histogrammas** — viss, kas attēlo attēlu, atbilst vienai un tai pašai pamatizšķirtspējai.
* Tas attiecas **tikai uz attēlošanu**. Apstrāde un eksportēšana netiek ietekmētas. Vienīgais izņēmums ir apzināts: eksportēšana no [Index/LUT Sandbox](index-lut-sandbox.md) saglabā to, ko jūs redzat, tādējādi saglabājot pašreizējo bloka izmēru, un eksportēšanas panelis brīdina, ja bloka izmērs pārsniedz 1.
* Vērtība tiek saglabāta **katram projektam atsevišķi** kā `viewer_display.gsd_bin` failā `project.json`, tādējādi tā saglabājas arī pēc programmas aizvēršanas un atkārtotas atvēršanas.
* Kursora rādījums parāda bloka vērtību, nevis avota pikseļa vērtību, ja bloka izmērs pārsniedz 1 — parādītā vērtība ir bloka vidējā vērtība zem jūsu kursora.

{% hint style="info" %}
**Kāpēc „bloka izmērs”, nevis centimetri uz pikseli?** Lai aprēķinātu cm/px rādītāju, ir nepieciešams augstums virs zemes. Viena kadra EXIF dati satur GPS augstumu virs vidējā jūras līmeņa, nevis virs reljefa, uz kuru tā bija vērsta, tāpēc Chloros neuzrādīs attālumu līdz zemei, ko tas nevar precīzi aprēķināt. Bloka izmērs avota pikseļos ir tā pati rezerves vērtība, ko izmanto MAPIR mākoņu rīki, ja attālums līdz zemei nav zināms.
{% endhint %}

***

## Attēlu veidi, kurus varat apskatīt

Skatītāja labajā augšējā stūrī esošajā slāņu nolaižamajā izvēlnē ir uzskaitītas visas pašreizējā attēla versijas. Kādi ieraksti tiek parādīti, ir atkarīgs no kameras un no tā, kas ir apstrādāts — skatiet [Attēlu slāņi](image-layers.md), lai redzētu pilnu sarakstu un uzzinātu, kā darbojas izvēlne.

### Survey3

* **JPG** — kameras paša priekšskatījuma fails
* **RAW (oriģināls)** — avota `.RAW`, attēla parādīšanai atbrīvots no bayera, bez korekcijām
* **RAW (mērķis)** — kadrs, kas identificēts kā tāds, kurā ir kalibrēšanas mērķis
* **RAW (Atstarošana)** — kalibrētais atstarošanas rezultāts (65535 = ρ 1,0)
* **Koriģēta vinjetēšana**/**Sensora reakcija** — nekalibrētais rezerves rezultāts
* **White Balanced** — attēls ar balansa korekciju
* **RAW (`<INDEX>` indekss)**un**`<INDEX>` LUT** — aprēķināti indeksa attēli

### LATTICE

LATTICE uzņēmumiem tiek izmantota tā pati nolaižamā izvēlne ar apstrādes posmu nosaukumiem:

| Slānis                 | Kas tajā atrodas                                                        |
| --------------------- | -------------------------------------------------------------------- |
| **RAW (oriģināls)**    | Avota neapstrādātais kadrs tā, kā tas tika uzņemts                                     |
| **RAW (bez bayera)**   | Lineārais attēls bez bayera                                           |
| **RAW (Priekšskatījums)**     | Ekrāna priekšskatījums — viltus krāsu izstiepums multispektrālajām kamerām |
| **Ar balansa korekciju**    | Ekrāna priekšskatījums RGB galvenajām kamerām (balansa korekcija + gamma)   |
| **RAW (starojums)**    | Float32 spektrālais starojums W/m²/sr/nm                              |
| **RAW (atstarošanās)** | uint16 atstarošanās, 32768 = ρ 1,0                                    |

Starojums un atstarošanās ir pieejami tikai multispektrālajā režīmā: RGB galvenajai kamerai nav radiometrijas pa joslām, tādēļ šie slāņi tai netiek ģenerēti.

***

## Indeksa un LUT piemērošana

Piemērojiet multispektrālos indeksus un krāsu meklēšanas tabulas (LUT) no sānu joslas:

1. Atveriet **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu joslā
2. Atzīmējiet **Index**

3. Izvēlieties savas kameras filtru un indeksa formulu, pēc tam velciet kanālu apļus uz formulas lauciņiem
4. Pievienojiet LUT un izvēlieties gradientu, sliekšņus un apgriešanas režīmu
5. Nolasiet vērtības pie kursora un saglabājiet rezultātu, izmantojot **Eksportēt/Saglabāt attēlu(-us)**Pilnu pamācību skatiet [Indeksa/LUT izmēģinājumu vidē](index-lut-sandbox.md).***

## Problēmu novēršana

### Attēls neatsveras

**Iespējamie iemesli**: fails tika pārvietots vai dzēsts pēc importēšanas; produkts netika saglabāts; nav pietiekami daudz atmiņas ļoti lielam attēlam.**Ko darīt**:

1. Pārbaudiet, vai slāņa fails joprojām atrodas projekta izvades struktūrā
2. Atveriet failu ārējā skatītājā, lai pārliecinātos, ka tas ir neskarts
3. Aizveriet citas programmas, lai atbrīvotu atmiņu

### Attēls ir melns, balts vai ar neparastām krāsām

**Iespējamie cēloņi**: ekrāna izstiepumam nav ar ko strādāt (gandrīz nemainīgs kadrs); „float32” slānis ar neparastām vērtībām; indekss, kas neradīja derīgus datus.**Ko darīt**:

1. Nolasiet kursora vērtības — ja katrs kanāls ir nulle vai tuvu nullei, problēma ir datos, nevis attēlošanā
2. Pārbaudiet histogrammu: viens pīķis vienā galā liecina, ka kadrs ir nogriezts vai tukšs
3. Pārbaudiet apstrādes žurnālu par to apstrādes ciklu, kurā tika izveidots slānis

### Vērtības izskatās nepareizas

**Iespējamie cēloņi**: jūs atrodaties citā slānī, nekā domājat; jūs salīdzināt procentuālo vērtību ar neapstrādātu DN; jūs salīdzināt LATTICE failu ar Survey3 failu, izmantojot to pašu dalītāju.**Ko darīt**:

1. Pārbaudiet izvēlēto slāni nolaižamajā izvēlnē — paneļa vienības atbilst slānim
2. Attiecībā uz atstarošanas koeficientu izmantojiet **%** kolonnu, nevis paši daliet DN; ja jums ir jādalās, izmantojiet šī faila `Chloros:PixelScale` (32768 LATTICE gadījumā; ja nav norādīts, tad Survey3 gadījumā — 65535)
3. Iestatiet GSD bloka izmēru atpakaļ uz 1 — ja tas pārsniedz 1, jūs lasāt bloka vidējo vērtību, nevis pikseli
4. Pārbaudiet, vai atstarojuma kalibrēšana šim kadram tiešām ir veikta; nekalibrēts rezerves produkts (Sensor Response / Vignette Corrected) nav atstarojums

***

## Turpmākie soļi

* [**Attēla slāņi**](image-layers.md) — katra slāņa nosaukums (ja tāds ir) un tā vērtību nozīme
* [**Indeksa/LUT izmēģinājumu vide**](index-lut-sandbox.md) — indeksa vizualizāciju veidošana, pielāgošana un eksportēšana
* [**Kartes marķieri**](map-markers.md) — tas pats attēlu kopums uz kartes
* [**Daudzspektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — indeksu atsauces

Apstrādes darba plūsmu skatiet sadaļā [Attēlu apstrāde (GUI)](../processing-images-gui/adding-files-to-a-project.md).
