# Attēlu slāņi

**Slāņu izvēlne** attēlu skatītāja labajā augšējā stūrī ļauj pārslēgties starp visām skatāmā attēla versijām — sākot no avota uzņēmuma, caur katru apstrādāto produktu līdz aprēķinātajiem indeksa attēliem — neizejot no skatītāja.

## Kas ir attēla slāņi?

„Slānis” programmā Chloros ir viens **produkta fails**, kas reģistrēts saistībā ar vienu avota attēlu. Importēšana nodrošina avota failus; apstrāde pievieno slāni katram produktam, ko izveidojusi apstrādes kārta. Eksportētajos failos tiek saglabāts avota faila nosaukums — produktu identificē**mape**, un slāņa nosaukums ir Chloros piešķirtais nosaukums šai mapei.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Slāņu saraksts

### Vienmēr pieejami

| Slānis | Kas tas ir |
| --- | --- |
| **JPG**(vai**PNG**/**TIFF**) | Pamata fails, kas tika iegūts kopā ar uzņemto attēlu. Survey3 importē failu „`.JPG`” blakus katram „`.RAW`”; „LATTICE” uzņemumi nodrošina PNG vai TIFF attēla priekšskatījumu. Nosaukts atbilstoši tam, kas faktiski tika importēts |
| **RAW (oriģināls)** | Avota neapstrādātais kadrs, kas attēlošanai ir atbrīvots no bayera filtra, bez jebkādām korekcijām. Pieejams jau no importēšanas brīža — tam nav nepieciešama apstrāde |

LATTICE uzņemumam, kura pamatfails **ir** tā neapstrādātais kadrs, nav atsevišķa pamata ieraksta: to jau aptver `RAW (Original)`.

### Survey3 apstrādes rezultāti

| Slānis | Ierakstīts | Pastāv, ja |
| --- | --- | --- |
| **RAW (mērķis)** | — | Kadrs tika identificēts kā tāds, kas satur kalibrēšanas mērķi |
| **RAW (atstarošana)** | `Reflectance_Calibrated_Images/` | Šajā kadrā veiksmīgi tika veikta atstarošanas kalibrēšana |
| **Vignette Corrected**| `Vignette_Corrected_Images/` | Kadrs nevarēja tikt kalibrēts pēc atstarojuma**un** *Vignette korekcija* bija ieslēgta |
| **Sensora reakcija**| `Sensor_Response_Images/` | Kadrs nevarēja tikt kalibrēts pēc atstarojuma**un** *vignētes korekcija* bija izslēgta |
| **Baltā balanss** | `White_Balanced_Images/` | Tika izveidots produkts ar baltā balansu |

{% hint style="info" %}
**Vignētes korekcija un sensora reakcija ir alternatīvas, nekad abas vienlaikus.** Katram kameras modelim katrā apstrādes ciklā pastāv tieši viens nekalibrēts rezerves produkts, un *Vignētes korekcijas* slēdzis izvēlas, kurš no tiem tiks izmantots. Skatīt [Projekta iestatījumus](../project-settings/project-settings.md).
{% endhint %}

### LATTICE līmeņi

LATTICE vienā apstrādes ciklā sadala datus šajos līmeņos. To, kuri no tiem ir pieejami, nosaka eksporta slēdži katram produktam projekta iestatījumos un tas, kas attiecas uz konkrēto kameru.

| Slānis | Rakstīts uz | Attiecas uz |
| --- | --- | --- |
| **RAW (Debayered)** | `Debayered_Images/` | RGB un multispektrālais |
| **RAW (priekšskatījums)** | `Preview_Images/` | Multispektrālais (viltus krāsu izstiepums) |
| **Ar balansa korekciju** | `Preview_Images/` | RGB galvenās kameras — RGB priekšskatījums tiek reģistrēts ar šo nosaukumu, lai tas saskanētu ar tāda paša nosaukuma Survey3 slāni |
| **RAW (starojums)** | `Radiance_Images/` | Tikai multispektrāls |
| **RAW (atstarošana)** | `Reflectance_Calibrated_Images/` | Tikai multispektrāls, un tikai tad, ja attēlu aptver atbilstošs `.daq` lejupvērstais ieraksts vai kvalitātes pārbaudi izturējis mērķis kadrā |

RGB galvenajām kamerām nav radiometrijas pa joslām, tāpēc tām starojuma intensitāte un atstarojums tiek izlaisti kā **nepiemērojami** — žurnālā tas tiek norādīts, nevis klusi ignorēts.

{% hint style="info" %}
**Neapstrādātie un debayered slāņi tiek saskaņoti ar produktiem (jaunums versijā 1.3.0).**LATTICE neapstrādātie un debayered faili tiek saglabāti sensora sākotnējā orientācijā, un XMP ierakstā tiek norādīta tiem piemītošā orientācija; pārējie produkti tiek saglabāti ar jau piemērotu orientāciju. Skatītājs un attēlu režģis parādīšanai pārslēdz**RAW (oriģināls)**un**RAW (bez bayera)** atbilstoši šim ierakstam, tādējādi kadrs vairs neapgriežas, pārslēdzoties starp produkta slāni un neapstrādātu slāni. Tas attiecas tikai uz attēlošanu — faili nekad netiek pārrakstīti. Faili bez orientācijas ieraksta (uzņemti pirms versijas 1.3.0 vai sagatavoti ar citu programmatūru) tiek parādīti tā, kā tie ir saglabāti. Skatīt [Izvades attēlu formāti](../output-image-formats.md#orientation).
{% endhint %}

Fiksēšanas maskas (`*_clampmask.tif` mapē `clamp_masks/`) nav slāņi: programma Chloros tās ignorē importēšanas laikā. Skatīt [Izvades attēlu formāti](../output-image-formats.md#clamp-masks).

### Indeksa, LUT un smilšu kastes slāņi

| Slāņa veids | Piemērs | No kurienes tas nāk |
| --- | --- | --- |
| **RAW (`<INDEX>` indekss)** | `RAW (NDVI Index)` | Viens uz katru indeksu, kas konfigurēts projekta iestatījumos, aprēķināts apstrādes laikā |
| **`<INDEX>` LUT** | `NDVI LUT` | Indeksa versija ar krāsu kartēšanu |
| **Sandbox (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Viens par katru [Indeksa/LUT smilšu kastes](index-lut-sandbox.md) eksportēšanas cikls |

Ja viens un tas pats indeksa nosaukums ir konfigurēts vairākkārt ar atšķirīgiem iestatījumiem, otrajam un turpmākajiem nosaukumā tiek pievienots numurs (`RAW (NDVI2 Index)`), lai slāņus varētu atšķirt.

***

## Slāņu izvēlnes izmantošana

1. Atveriet attēlu pilnekrāna režīmā, noklikšķinot uz sīktēla režģī
2. Noklikšķiniet uz **slāņu izvēlnes** skatītāja augšējā labajā stūrī
3. Izvēlieties slāni — attēls atjauninās nekavējoties

Nolaižamajā izvēlnē vispirms šādā secībā tiek parādīti **JPG, RAW (oriģināls), RAW (mērķis), RAW (atstarošana)**, bet pārējie slāņi tiek uzskaitīti pēc tam tādā secībā, kādā produkti tika reģistrēti.

### Slāņu prioritāte pārlūkošanas laikā

Nospiežot **←**/**→**, tiek atvērts nākamais attēls, un tiek mēģināts saglabāt to pašu slāni:

1. **Vispirms precīza atbilsme** — ja nākamajam attēlam ir slānis ar tādu pašu nosaukumu, tiek atvērts šis slānis. Tādējādi, pārskatot visu kopu, jūs paliksiet slānī `RAW (NDVI Index)`
2. **Tad saskaņošana pēc tipa** — indeksa slānis meklē jebkuru indeksa slāni, LUT — jebkuru LUT, atstarojuma slānis — atstarojuma slāni, mērķa slānis — mērķa slāni, oriģināls — oriģinālu, bāzes slānis — bāzes slāni
3. **Tad, tikai eksporta slāņiem** — nosaukums tiek saglabāts pat tad, ja slāņu saraksts vēl nav atjaunināts, jo fails jau pastāv diskā. Tas ļauj jums pārskatīt produktus, kamēr apstrāde tos vēl raksta
4. **Pārējos gadījumos** — pirmais pieejamais slānis, kas parasti ir bāzes attēls

Projekta sidecar faili `.daq` un `.csv` tiek izlaisti, pārvietojoties ar bultu taustiņiem, tādējādi, pārskatot attēlus, nekad netiek nonākts pie gaismas sensora ieraksta.

Tuvināšana un panoramēšana darbojas arī starp attēliem, kas atvieglo viena un tā paša lauka pozīcijas salīdzināšanu pirms un pēc.

***

## Pikseļu vērtību izpratne pa slāņiem

[Kursora vērtību panelis](opening-an-image-full-screen.md#cursor-values) parāda reālo vērtību katrā kanālā zem kursora, vienībās, kādās šis slānis ir saglabāts. Tā kolonnas mainās atkarībā no slāņa:

| Slānis | Parādītās vienības | Piezīmes |
| --- | --- | --- |
| Pamats (JPG / PNG / TIFF priekšskatījums) | DN, 0–255 | Ekrāna vērtības, ar gamma korekciju RGB. Tikai vizuālai pārbaudei |
| RAW (oriģināls) | DN | Neapstrādāti sensora digitālie skaitļi. Histogrammas ass norāda dziļumu: 255 (8 biti), 4095 (12 biti) vai 65535 (16 biti) |
| RAW (bez bayera filtra) | DN | Lineārs, bez attēla izstiepšanas |
| RAW (priekšskatījums) / ar balansa korekciju | DN | Attēla rezultāts — izstiepts vai ar gamma korekciju. Nav paredzēts mērījumiem |
| RAW (starojums) | **W/m²/sr/nm** | Float32 fiziskais starojums. Nav DN kolonnas |
| RAW (atstarošana) | DN **un %** | Procentuālā vērtība aprēķināta, izmantojot šī faila paša mērogu — skatīt zemāk |
| Indekss / LUT / eksporti uz sandbox | Indeksa vērtība vai RGB komponenti | Vienkanāla indeksa fails norāda indeksa vērtību; krāsu kartēts LUT fails norāda Red /Green /Blue komponentes |

### Atstarošana: skala ir katram failam atsevišķi

{% hint style="warning" %}
**"Dalīt ar 65 535" ir pareizi tikai attiecībā uz Survey3.** LATTICE atstarošanas koeficients tiek saglabāts citā mērogā, un abu dalītāju sajaukšana ir visbiežāk sastopamais veids, kā iegūt atstarošanas koeficienta vērtības, kas ir tieši puse no tām, kādām tām vajadzētu būt.
{% endhint %}

| Avots | DN, kas atbilst atstarojumam 1,0 | Identificēts pēc |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | XMP birka `Chloros:PixelScale=32768`, kas iekļauta katrā LATTICE atstarojuma eksportā. 2× rezerves diapazons nozīmē, ka ρ virs 1,0 ir attēlojams, nevis nogriezts |
| **Survey3**| **65535** | Nav Chloros XMP mēroga tag — Survey3 kalibrēšana ieraksta ρ × dtype-max un nogriež pie 1,0 |

ĢIS un skriptu vajadzībām: nolasiet `Chloros:PixelScale` no faila un daliet ar to. Ja marķējums nav, fails ir Survey3 mērogā (65535). Skatītājs, indeksa/LUT izmēģinājumu vide un indeksa eksports visi nosaka mērogu šādā pašā veidā, tādēļ skaitlis, ko redzat pie kursora, ir tas pats skaitlis, ko izmanto indeksa aprēķini.

Papildus šim mērogam — formātam specifiska uzglabāšana:

* **TIFFs (32 bitu, procentos)** uzglabā DN / 65535 kā peldošā punkta skaitli
* **PNG (8 bitu)**un**JPG (8 bitu)** uzglabā DN × 255 / 65535
* **8 bitu DN, kas iegūts no 8 bitu uzņemšanas**, vai atstarojuma fails, kas izveidots pirms versijas 1.3.0, tiek ierobežots diapazonā no 0 līdz 255 un tam nav mēroga atzīmes. Panelī šiem failiem tiek parādīts tikai DN, bez procentu kolonnas

### Indeksu vērtību diapazoni

| Indeksu grupa | Tipisks diapazons | Rādījums |
| --- | --- | --- |
| Normalizētā starpība (NDVI, GNDVI, NDRE, ENDVI…) | no −1 līdz +1 | Veselīgai veģetācijai parasti 0,4–0,9; kailai augsnei tuvu 0; ūdenim negatīvs |
| Pēc augsnes koriģēti (SAVI, OSAVI, MSAVI2…) | aptuveni no −1 līdz +1,5 | Rādījums līdzīgs NDVI, bet ar nomāktu augsnes fonu |
| Attiecība (GRVI, GCI, MSR, CIRE…) | bez augšējās robežas | Attiecības pieaug bez ierobežojuma, jo saucēja josla tuvojas nullei |
| EVI / LAI | no 0 līdz ~1, no 0 līdz ~3,5 | Mākoņi un citi piesātināti pikseļi abas vērtības izspiež ārpus diapazona — vispirms tos jāmaskē |

Skatīt [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md), lai uzzinātu precīzo formulu, kas ir katras iestatījuma pamatā.

***

## Tipiskas darbplūsmas

### Salīdzinājums „pirms” un „pēc”

1. Izvēlieties **RAW (oriģināls)** un pievērsiet uzmanību vinjetēšanai un nekalibrētajām vērtībām
2. Pārejiet uz **RAW (Reflectance)**

3. Salīdziniet — vinjetēšana noņemta, vērtības kalibrētas. Tuvinājums un panoramēšana paliek nemainīgi, tādējādi jūs skatāties uz to pašu zemes virsmu

### Viena indeksa pārskatīšana visā attēlu kopumā

1. Atveriet pirmo apstrādāto attēlu un izvēlieties indeksa slāni
2. Vairākas reizes nospiediet **→** — indeksa slānis seko līdzi no attēla uz attēlu
3. Vērojiet histogrammu sānjoslā: kadrs, kurā sadalījums strauji mainās, ir vērts apskatīt sīkāk

### Kalibrēšanas mērķu pārbaude

1. Izvēlieties **RAW (Mērķis)** uz mērķa kadra
2. Pārliecinieties, ka mērķis ir skaidri redzams un atpazīts
3. Pārejiet uz nākamo mērķa kadru — mērķa slānis seko līdzi

### Pārbaudiet atstarojuma vērtību precizitāti

1. Izvēlieties **RAW (Atstarojums)**

2. Nolasiet**%** kolonnu panelī „Cursor Values” — tā jau ir pareizi mērogota šim failam
3. Veiciet loģiskas pārbaudes, salīdzinot ar kadra zināmajiem materiāliem: veselīgai veģetācijai ir augsts NIR līmenis un zems sarkanais līmenis; kalibrēšanas mērķim jāuzrāda vērtība, kas ir tuvu tā publicētajai atstarojuma vērtībai

***

## Problēmu novēršana

### Nolaižamajā izvēlnē nav slāņa, ko gaidīju

**Iespējamie iemesli**

* Attēls nekad nav apstrādāts — pastāv tikai bāzes slānis un `RAW (Original)`
* Projekta iestatījumos nav atzīmēta produkta eksportēšanas opcija
* Produkts neattiecas uz šo kameru (starojums un atstarojums uz RGB-galvenā attēla; jebkurš indekss uz vienkanāla M3M mono kameras)
* Atstarošanas kalibrēšanai nebija ar ko strādāt — nebija `.daq` lejupvērstā seguma un nebija kvalitātes pārbaudi izturējuša mērķa kadrā — tādēļ kadrs tika pārveidots uz „Vignette Corrected” vai „Sensor Response”

**Ko darīt**

1. Pārbaudiet izpildes žurnālu: Chloros norāda, kad pieprasītais eksporta produkts nebija iespējams un kāpēc
2. Pārbaudiet eksporta izvēles slēdžus katram produktam atsevišķi [Projekta iestatījumos](../project-settings/project-settings.md)
3. Pārliecinieties, ka produkta mape atrodas projekta izvades struktūrā
4. Veiciet atkārtotu apstrādi, aktivizējot šo produktu

### Slāņu saraksts izskatās novecojis

Chloros atkārtoti skenē projekta produktu mapes, kamēr norit apstrāde, un izlabo trūkstošās slāņu reģistrācijas, balstoties uz to, kas faktiski atrodas diskā, tāpēc slānis, kura eksportēšana ir pabeigta, parasti parādās pats no sevis aptaujā. Pāreja prom no attēla un atpakaļ piespiež veikt jaunu izšķirtspējas aprēķinu.

### Reflektances vērtības šķiet divreiz mazākas nekā vajadzētu

Gandrīz noteikti jūs dalāt LATTICE failu ar 65535. Izmantojiet `Chloros:PixelScale` (32768) vai skatieties **%** sleju, kurā šis koeficients jau ir piemērots.

### Indeksa slānis pastāv, bet attēls ir tukšs

Indeksam ir nepieciešami kanāli, kādu jūsu slānī nav — piemēram, indekss, kas nolasa trešo kanālu, ir piemērots vienkanāla vai divkanālu failam. Pārejiet uz daudzkanālu slāni (atstarošanas vai bezbayera) vai izvēlieties indeksu, kas atbilst kameras filtram.

***

## Turpmākie soļi

* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — kursora rādījumi, histogramma un GSD kontrole
* [**Indeksu/LUT izmēģinājumu vide**](index-lut-sandbox.md) — interaktīva indeksu vizualizācija un eksportēšana
* [**Multispektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — indeksu atsauces
* [**Apstrādes pabeigšana**](../processing-images-gui/finishing-the-processing.md) — izvades mapju koks, uz kuru norāda šie slāņi
