# Attēlu slāņi

**Slāņu izvēlne** attēlu skatītāja labajā augšējā stūrī ļauj pārslēgties starp visām skatāmā attēla versijām — sākot no avota uzņēmuma, caur katru apstrādāto produktu līdz aprēķinātajiem indeksa attēliem — neizejot no skatītāja.

## Kas ir attēla slāņi?

„Slānis” programmā Chloros ir viens **rezultāta fails**, kas saistīts ar vienu avota attēlu. Importēšanas procesā tiek iegūti avota faili; apstrādes procesā tiek pievienots slānis katram rezultātam, ko izveidojusi apstrādes kārta. Eksportētajos failos tiek saglabāts avota faila nosaukums — tieši**mapes** nosaukums identificē produktu, un slāņa nosaukums ir Chloros piešķirtais nosaukums šai mapei.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Slāņu saraksts

### Vienmēr klāt

| Slānis | Kas tas ir |
| --- | --- |
| **JPG**(vai**PNG**/**TIFF**) | Pamata fails, kas tika importēts kopā ar uzņemumu. Survey3 importē `.JPG` blakus katram `.RAW`; LATTICE uzņemumi nodrošina PNG vai TIFF attēla priekšskatījumu. Marķēts atbilstoši tam, kas faktiski tika importēts |
| **RAW (oriģināls)** | Avota neapstrādātais kadrs, kas attēlošanai atbrīvots no bayera filtra, bez jebkādām korekcijām. Pieejams jau no importēšanas brīža — nav nepieciešama apstrāde |

LATTICE uzņēmumam, kura pamatfails **ir** tā neapstrādātais kadrs, nav atsevišķa pamata ieraksta: to jau aptver `RAW (Original)`.

### Survey3 apstrādes rezultāti

| Slānis | Ierakstīts | Pastāv, ja |
| --- | --- | --- |
| **RAW (mērķis)** | — | Kadrs tika identificēts kā tāds, kas satur kalibrēšanas mērķi |
| **RAW (atstarošana)** | `Reflectance_Calibrated_Images/` | Šajā kadrā veiksmīgi tika veikta atstarošanas kalibrēšana |
| **Vignette Corrected**| `Vignette_Corrected_Images/` | Kadrs nevarēja tikt kalibrēts pēc atstarošanas**un** *vignēšanas korekcija* bija ieslēgta |
| **Sensora reakcija**| `Sensor_Response_Images/` | Kadrs nevarēja tikt kalibrēts pēc atstarojuma**un** *vignēšanas korekcija* bija izslēgta |
| **Baltā balanss** | `White_Balanced_Images/` | Tika sagatavots produkts ar baltā balansu |

{% hint style="info" %}
**Vignētes korekcija un sensora reakcija ir alternatīvas, nekad abas vienlaikus.** Katram kameras modelim katrā apstrādes ciklā pastāv tieši viens nekalibrēts rezerves produkts, un *vignētes korekcijas* slēdzis izvēlas, kurš no tiem tiks izmantots. Skatīt [Projekta iestatījumi](../project-settings/project-settings.md).
{% endhint %}

### LATTICE līmeņi

LATTICE vienā apstrādes ciklā sadala attēlu šajos līmeņos. Kuri no tiem tiek izmantoti, ir atkarīgs no projekta iestatījumos katram produktam norādītajiem eksportēšanas slēdžiem un no tā, kas attiecas uz konkrēto kameru.

| Slānis | Rakstīts uz | Attiecas uz |
| --- | --- | --- |
| **RAW (bez Bayer filtra)** | `Debayered_Images/` | RGB un multispektrālais |
| **RAW (priekšskatījums)** | `Preview_Images/` | Multispektrālās (viltus krāsu izstiepums) |
| **Ar balansa korekciju** | `Preview_Images/` | RGB galvenajām kamerām — RGB priekšskatījums ir reģistrēts ar šo nosaukumu, lai tas saskanētu ar tāda paša nosaukuma Survey3 slāni |
| **RAW (starojums)** | `Radiance_Images/` | Tikai multispektrāls |
| **RAW (atstarošana)** | `Reflectance_Calibrated_Images/` | Tikai multispektrāls, un tikai tad, ja attēlu pārklāj atbilstošs `.daq` lejupvērstais ieraksts vai kvalitātes pārbaudi izturējis mērķis kadrā |

RGB galvenajām kamerām nav radiometrijas pa joslām, tāpēc tām starojums un atstarošanās tiek izlaisti kā **nepiemērojami** — žurnālā tas tiek norādīts, nevis klusi ignorēts.

### Indeksa, LUT un smilšu kastes slāņi

| Slāņa modelis | Piemērs | No kurienes tas nāk |
| --- | --- | --- |
| **RAW (`<INDEX>` indekss)** | `RAW (NDVI Index)` | Viens uz katru indeksu, kas konfigurēts projekta iestatījumos, aprēķināts apstrādes laikā |
| **`<INDEX>` LUT** | `NDVI LUT` | Indeksa krāsu kartēšanas versija |
| **Smilšu kaste (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Viens par katru [Indeksa/LUT Sandbox](index-lut-sandbox.md) eksporta cikls |

Ja viens un tas pats indeksa nosaukums ir konfigurēts vairākkārt ar atšķirīgiem iestatījumiem, otrajam un turpmākajiem nosaukumā tiek pievienots numurs (`RAW (NDVI2 Index)`), lai slāņus varētu atšķirt.

***

## Slāņu izvēlnes izmantošana

1. Atveriet attēlu pilnekrāna režīmā, noklikšķinot uz sīktēla režģī
2. Noklikšķiniet uz **slāņu izvēlnes** skatītāja augšējā labajā stūrī
3. Izvēlieties slāni — attēls atjauninās nekavējoties

Nolaižamajā izvēlnē vispirms šādā secībā tiek parādīti **JPG, RAW (oriģināls), RAW (mērķis), RAW (atstarošana)**, bet pēc tam — visi pārējie slāņi tādā secībā, kādā produkti tika reģistrēti.

### Slāņu prioritāte pārlūkošanas laikā

Nospiežot **←**/**→**, tiek atvērts nākamais attēls, un tiek mēģināts saglabāt to pašu slāni:

1. **Vispirms precīza atbilsme** — ja nākamajam attēlam ir slānis ar tādu pašu nosaukumu, tiek atvērts tieši tas. Tādējādi, pārskatot visu kopu, jūs paliksiet slānī `RAW (NDVI Index)`
2. **Tad saskaņošana pēc tipa** — indeksa slānis meklē jebkuru indeksa slāni, LUT — jebkuru LUT, atstarošanas slānis — atstarošanas slāni, mērķa slānis — mērķa slāni, oriģināls — oriģinālu, bāzes slānis — bāzes slāni
3. **Tad, tikai eksporta slāņiem** — nosaukums tiek saglabāts pat tad, ja slāņu saraksts vēl nav atjaunināts, jo fails jau pastāv diskā. Tas ļauj jums pārskatīt produktus, kamēr apstrāde tos vēl raksta
4. **Pārējos gadījumos** — pirmais pieejamais slānis, kas parasti ir bāzes attēls

Projekta sidecar faili `.daq` un `.csv` tiek izlaisti, pārvietojoties ar bulttaustiņiem, tādējādi, pārskatot attēlus, nekad netiek atvērtas gaismas sensora ierakstītas bildes.

Tuvināšana un panoramēšana darbojas arī starp attēliem, kas atvieglo viena un tā paša lauka pozīcijas salīdzināšanu pirms un pēc.

***

## Pikseļu vērtību izpratne pa slāņiem

[Kursora vērtību panelis](opening-an-image-full-screen.md#cursor-values) parāda reālo vērtību katram kanālam zem kursora, vienībās, kādās slānis ir saglabāts. Tā kolonnas mainās atkarībā no slāņa:

| Slānis | Parādītā vienība | Piezīmes |
| --- | --- | --- |
| Bāzes (JPG / PNG / TIFF priekšskatījums) | DN, 0–255 | Ekrāna vērtības, ar gamma korekciju RGB. Tikai vizuālai pārbaudei |
| RAW (oriģināls) | DN | Neapstrādāti sensora digitālie skaitļi. Histogrammas ass norāda dziļumu: 255 (8 biti), 4095 (12 biti) vai 65535 (16 biti) |
| RAW (bez bayera filtra) | DN | Lineārs, bez attēla izstiepšanas |
| RAW (Priekšskatījums) / Balansa korekcija | DN | Attēlojamais rezultāts — izstiepts vai ar gamma korekciju. Nav paredzēts mērījumiem |
| RAW (Starojums) | **W/m²/sr/nm** | Float32 fiziskais starojums. Nav DN kolonnas |
| RAW (atstarošana) | DN **un %** | Procentuālais rādītājs aprēķināts, izmantojot šī faila paša mērogu — skatīt zemāk |
| Indeksa / LUT / sandbox eksporti | Indeksa vērtība vai RGB komponenti | Vienkanāla indeksa fails norāda indeksa vērtību; bet krāsu kartēts LUT fails norāda Red/Green/Blue komponentus |

### Atstarošanas koeficients: skala ir norādīta katram failam atsevišķi

{% hint style="warning" %}
**„Dalīt ar 65 535” ir pareizi tikai attiecībā uz Survey3.** LATTICE atstarošanas koeficients tiek saglabāts citā mērogā, un abu dalītāju sajaukšana ir visbiežāk sastopamais veids, kā iegūt atstarošanas koeficientus, kas ir tieši puse no tā, kādiem tiem vajadzētu būt.
{% endhint %}

| Avots | DN, kas atbilst atstarošanas koeficientam 1,0 | Identificē pēc |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | XMP tag `Chloros:PixelScale=32768`, kas iekļauts katrā LATTICE atstarojuma eksportā. 2× rezerves diapazons nozīmē, ka ρ virs 1,0 ir attēlojams, nevis nogriezts |
| **Survey3**|**65535** | Ja nav Chloros XMP mēroga tagu — Survey3 kalibrēšana ieraksta ρ × dtype-max un nogriež pie 1,0 |

ĢIS un skriptu izstrādei: nolasiet `Chloros:PixelScale` no faila un daliet ar to. Ja marķieris nav, failam ir Survey3 mērogs (65535). Skatītājs, indeksa/LUT smilšu kaste un indeksa eksports visi nosaka mērogu šādā pašā veidā, tādēļ skaitlis, ko redzat pie kursora, ir tas pats skaitlis, ko izmanto indeksa aprēķini.

Papildus šim mērogam — formātam specifiska uzglabāšana:

* **TIFF (32 bitu, procentos)** uzglabā DN / 65535 kā peldošā punkta skaitli
* **PNG (8 bitu)**un**JPG (8 bitu)** saglabā DN × 255 / 65535
* **8 bitu TIFF eksports no 8 bitu avota uzņemuma** tiek nogriezts diapazonā 0–255, nevis pārskalots, un tam apzināti nav mēroga marķējuma. Panelī šiem failiem tiek parādīts tikai DN, bez procentu kolonnas

### Indeksa vērtību diapazoni

| Indeksa grupa | Tipisks diapazons | Nolasījums |
| --- | --- | --- |
| Normalizētā starpība (NDVI, GNDVI, NDRE, ENDVI…) | no −1 līdz +1 | Veselīgai veģetācijai parasti 0,4–0,9; kaila augsne — apmēram 0; ūdens — negatīvs |
| Pielāgots augsnei (SAVI, OSAVI, MSAVI2…) | aptuveni no −1 līdz +1,5 | rādītājs līdzīgs NDVI, bet ar nomāktu augsnes fona ietekmi |
| Attiecība (GRVI, GCI, MSR, CIRE…) | bez ierobežojuma virs | Attiecības pieaug bez ierobežojuma, kad saucēja josla tuvojas nullei |
| EVI / LAI | no 0 līdz ~1, no 0 līdz ~3,5 | Mākoņi un citi piesātināti pikseļi abas vērtības izspiež ārpus diapazona — vispirms tos jāmaskē |

Skatīt [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md), lai uzzinātu precīzo formulu, kas ir katras iestatījuma pamatā.

***

## Bieži izmantotās darbplūsmas

### Salīdzinājums pirms un pēc

1. Izvēlieties **RAW (oriģināls)** un pievērsiet uzmanību vinjetēšanai un nekalibrētajām vērtībām
2. Pāriet uz **RAW (atstarošana)**

3. Salīdziniet — vinjetēšana noņemta, vērtības kalibrētas. Tuvinājums un panoramēšana paliek nemainīgi, tādējādi jūs skatāties uz to pašu zemes virsmu

### Pārskatiet vienu indeksu visā attēlu kopumā

1. Atveriet pirmo apstrādāto attēlu un izvēlieties indeksa slāni
2. Vairākas reizes nospiediet **→** — indeksa slānis seko līdzi no attēla uz attēlu
3. Darba gaitā vērojiet histogrammu sānjoslā: kadrs, kurā sadalījums strauji mainās, ir vērts apskatīt sīkāk

### Pārbaudiet kalibrēšanas mērķus

1. Izvēlieties **RAW (Mērķis)** uz mērķa kadra
2. Pārliecinieties, ka mērķis ir skaidri redzams un atpazīts
3. Pārejiet uz nākamo mērķa kadru — mērķa slānis seko līdzi

### Pārbaudiet atstarošanas vērtību precizitāti

1. Izvēlieties **RAW (Atstarošana)**

2. Nolasiet**%** sleju panelī „Cursor Values” — tā jau ir pareizi mērogota šim failam
3. Veiciet loģiskas pārbaudes, salīdzinot ar kadra zināmajiem materiāliem: veselīgai veģetācijai ir augsts NIR rādītājs un zems sarkanais rādītājs; kalibrēšanas mērķim jābūt tuvu tā publicētajai atstarošanai

***

## Problēmu novēršana

### Nolaižamajā izvēlnē nav slāņa, ko gaidīju

**Iespējamie iemesli**

* Attēls nav apstrādāts — ir pieejami tikai bāzes slānis un `RAW (Original)`
* Projekta iestatījumos nav atzīmēta produkta eksportēšanas izvēles poga
* Produkts neattiecas uz šo kameru (starojums un atstarošanas koeficients uz RGB galvenās kameras; jebkurš indekss uz vienkanāla M3M mono kameras)
* Atstarošanas kalibrēšanai nebija ar ko strādāt — nebija `.daq` lejupvērstā seguma un nebija kvalitātes pārbaudi izturējuša mērķa kadrā — tādēļ kadrs tika atgriezts pie „Vignette Corrected” vai „Sensor Response”

**Ko darīt**

1. Pārbaudiet izpildes žurnālu: Chloros norāda, kad pieprasītais eksporta produkts nebija iespējams un kāpēc
2. Pārbaudiet eksporta ieslēgšanas/izslēgšanas opcijas katram produktam atsevišķi sadaļā [Projekta iestatījumi](../project-settings/project-settings.md)
3. Pārliecinieties, ka produkta mape atrodas projekta izvades struktūrā
4. Veiciet atkārtotu apstrādi, ieslēdzot šo produktu

### Slāņu saraksts izskatās novecojis

Chloros atkārtoti skenē projekta produktu mapes, kamēr norit apstrāde, un izlabo trūkstošās slāņu reģistrācijas, balstoties uz to, kas faktiski atrodas diskā; tādējādi slānis, kura eksportēšana ir pabeigta, parasti parādās pats no sevis apsekojumā. Pāreja prom no attēla un atpakaļ piespiež veikt jaunu izšķirtspējas aprēķinu.

### Reflektances vērtības šķiet divreiz mazākas nekā vajadzētu

Gandrīz noteikti jūs dalāt LATTICE failu ar 65535. Izmantojiet `Chloros:PixelScale` (32768) vai apskatiet **%** sleju, kurā šis koeficients jau ir piemērots.

### Indeksa slānis pastāv, bet attēls ir tukšs

Indeksam ir nepieciešami spektra joslas, kas jūsu slānī nav — piemēram, indekss, kas nolasa trešo kanālu, ir piemērots vienkanāla vai divkanālu failam. Pārejiet uz daudzjoslu slāni (atstarošanas vai debayered) vai izvēlieties indeksu, kas atbilst kameras filtram.

***

## Turpmākie soļi

* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — kursora rādījumi, histogramma un GSD kontrole
* [**Indeksa/LUT izmēģinājumu vide**](index-lut-sandbox.md) — interaktīva indeksa vizualizācija un eksportēšana
* [**Daudzspektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — indeksu atsauces
* [**Apstrādes pabeigšana**](../processing-images-gui/finishing-the-processing.md) — izvades mapju koks, uz kuru norāda šie slāņi
