# Index/LUT Sandbox

Index/LUT Sandbox ir interaktīva darba vide Chloros attēlu skatītāja sānjoslā. Jūs izvēlaties formulu, piesaistāt tai savas kameras kanālus, iekrāsojat to ar gradientu un pielāgojat vērtību diapazonu — un attēls tiek atjaunināts reāllaikā, kamēr jūs to darāt. Sākot ar versiju 1.2.0, jūs varat arī **saglabāt to, ko esat izveidojuši**, gan vienam attēlam, gan visam projektam, neveicot atkārtotu apstrādi.

## Kādam nolūkam paredzēta „Sandbox”

| „Index/LUT Sandbox” (interaktīva)        | Projekta apstrāde (partija)       |
| -------------------------------------- | -------------------------------- |
| Viens attēls vienlaikus, tūlītēja atgriezeniskā saite  | Viss datu kopums vienā apstrādes ciklā     |
| Eksperimentāls un iteratīvs             | Iepriekš konfigurēti iestatījumi          |
| Renderē reāllaikā; saglabā tikai pēc jūsu pieprasījuma  | Vienmēr raksta gala failus      |
| Ideāli piemērots pareizo iestatījumu meklēšanai | Labākais risinājums, kad iestatījumi ir galīgi |

{% hint style="success" %}
**Parastais darba process**: pielāgojiet iestatījumus Sandbox, līdz vizualizācija atbilst jūsu vēlmēm, pēc tam vai nu eksportējiet tieši no Sandbox, vai arī kopējiet tos pašus indeksa un LUT iestatījumus uz [Projekta iestatījumiem](../project-settings/project-settings.md), lai nākamajā apstrādes ciklā tie tiktu piemēroti katram attēlam.
{% endhint %}

***

## „Sandbox” atvēršana

1. Noklikšķiniet uz attēla režģī — tas atvērsies pilnekrāna režīmā cilnē **Attēlu skatītājs** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
2. Noklikšķiniet uz **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> ikonas, lai izslīdētu kreiso sānu joslu, ja tā vēl nav atvērta
3. Izvēlieties daudzjoslu slāni no slāņu izvēlnes augšējā labajā stūrī — parasti izvēlas **RAW (Reflectance)**, jo indeksa vērtības, kas aprēķinātas pēc kalibrētas atstarošanas, ir salīdzināmas starp attēliem

Sānu joslā no augšas uz leju tiek parādīts:

* attēla nosaukums un kameras modelis
* poga **Eksportēt/saglabāt attēlu(-us)**— parādās, kad ir atzīmēts**Indekss**vai**LUT*** izvēles rūtiņas **Indekss**un**LUT**
* indeksa konfigurācijas panelis
* panelis **Kursora vērtības** ar rādījumu, histogrammu un GSD kontroli

{% hint style="warning" %}
**Nav pieejams mono kamerām.** Vienas joslas LATTICE M3M attēlā abi izvēles rūtiņas ir atspējotas, un parādās rīkjoslas uzvedne _&quot;Nav pieejams mono (M3M) sensoriem&quot;_ — daudzjoslu indekss vienā joslā nav definēts. Lai aprēķinātu indeksus no M3M kamerām, apvienojiet divas vai vairākas kameras vienā saskaņotā daudzjoslu attēlu kopā un izmantojiet LATTICE indeksa dzinēju.
{% endhint %}

***

## Indeksa piemērošana

1. Atzīmējiet izvēles rūtiņu **Indekss** sānu joslas augšdaļā
2. Izvēlieties savas kameras filtru no kreisās izvēlnes (`RGN`, `OCN`, `NGB`, `RGB`, `RE`, `NIR`)
3. Izvēlieties indeksa formulu no labās izvēlnes — 27 iebūvētas formulas, kā arī jebkuras jūsu saglabātās pielāgotās formulas
4. Formula tiek attēlota kā matemātiska izteiksme zemāk, ar tukšu apli katrā joslas ailē. **Pavelciet krāsainu kanāla apli uz aili**, lai to piesaistītu
5. Kad visas formulas izmantotās ailes ir piesaistītas, attēls atjauninās un parādīs indeksa vērtības
6. Pārvietojiet kursoru pār attēlu, lai nolasītu vērtības; panelī **Cursor Values** tiek pievienota indeksa rinda ar vērtību zem kursora

Divkārši noklikšķiniet uz piesaistītas vietas, lai to dzēstu. Nepilnīga formula ir normāls stāvoklis, kamēr tiek vilkta formula, nevis kļūda — attēls vienkārši netiek atjaunināts, kamēr formula nav pabeigta.

Kanālu apļiem ir krāsu kods: sarkans = Red, zaļš = Green, zils = Blue, oranža = Orange, ciāna = Cyan, violeta = NIR, magenta = RE. Tās pašas krāsas tiek izmantotas kanālu punktiem un histogrammas līknēm panelī „Cursor Values” (Kursora vērtības).

### NDVI piemērs

```

Formula: (NIR - Red) / (NIR + Red)

For a Survey3W RGN camera:
  NIR = 850 nm band
  Red = 661 nm band

Result range:          -1.0 to +1.0
Typical vegetation:     0.4 to 0.9
Stressed vegetation:    0.2 to 0.4
Bare soil:              0.0 to 0.2
Water:                 -0.1 to 0.1
```

Pilnīgu formulu atsauci — visus trīs iepriekš iestatīto sarakstu un to, kuri nosaukumi kur darbojas — skatiet [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

### Ar atzīmētu indeksu, bet bez LUT

Attēls tiek attēlots **pelēktoņu skalā**, izstiepts starp divām sliekšņa vērtībām. Tas ir apzināti: indeksa attēls ir skalāri dati, un pelēktoņu skala ir tā patiesā attēlošana. Pievienojiet LUT, ja vēlaties krāsu.***

## Darbs ar LUT (meklēšanas tabulām)**Meklēšanas tabula** saista indeksa vērtības ar krāsām: ievadiet NDVI 0,65, rezultātā iegūsiet konkrētu zaļo krāsu. Tā nemaina datus — tā maina to, kā jūs tos interpretējat.

### LUT pievienošana

1. Noklikšķiniet uz <img src="../.gitbook/assets/image (1) (1) (1).png" alt="" data-size="line"> **&quot;+ Pievienot LUT&quot;** pogas zem formulas
2. Izvēlieties krāsu gradientu
3. Iestatiet minimālo un maksimālo nogriešanas vērtību
4. Izvēlieties nogriešanas režīmu
5. Atzīmējiet **LUT** lodziņu sānjoslā, lai to renderētu

LUT izvēles lodziņš paliek neaktīvs, kamēr LUT nav faktiski konfigurēts indeksā.

### Krāsu gradienta izvēle

Pavelciet peles kursoru pār **gradienta joslu**, lai atvērtu iestatījumu sarakstu — Chloros piedāvā**septiņus** gradienta iestatījumus:

| # | Gradients                            | Forma                                                               |
| - | ----------------------------------- | ------------------------------------------------------------------- |
| 1 | Red → Dzeltena → Green (**noklusējums**)  | Diverģējoša — atbilst parastajai izpratnei par veģetāciju: zaļš = veselīgs |
| 2 | Violeta → Dzeltena → Green             | Diverģējošs, ar izteiktu zemo diapazonu                                  |
| 3 | Brūna → Balta → Blue                | Diverģējošs ap gaišu viduspunktu                                   |
| 4 | Melna → Violeta → Rozā → Gaiši dzeltena | Sekvenciāla, no tumšas uz gaišu                                           |
| 5 | Red → Dzeltena → Blue                 | Atšķirīga ap gaišu viduspunktu                                   |
| 6 | Violeta → Blue → Green → Dzeltena      | Secīga, no tumšas uz gaišu                                           |
| 7 | Orange → Balts → Violets             | Izplešanās ap gaišu viduspunktu                                   |

**Diverģējošs**gradients novieto neitrālu krāsu loga vidū, kas ir labi saskatāms, ja viduspunkts nozīmē kaut ko konkrētu (slieksnis, atsauces datums).**Sekvenciāls** gradients vienmērīgi pāriet no tumšā uz gaišo, kas ir labi saskatāms, ja lielumam ir tikai „vairāk” un „mazāk”.

Katram iestatījumam ir septiņi krāsu posmi. Noklikšķiniet uz iestatījuma, un attēls nekavējoties atjaunināsies (ja ir atzīmēta LUT izvēles rūtiņa).

### Krāsu posmu rediģēšana

Zem gradienta joslas atrodas krāsu paraugu rinda, pa vienam paraugam katram posmam:

* **Krāsas maiņa**: noklikšķiniet uz parauga, lai atvērtu krāsu izvēlni (krāsu ritenis, RGB/HSV slīdņi vai heksadecimālais kods, piemēram, `#FF0000`)
* **Pievienot pārtraukumu**: noklikšķiniet uz pogas**+** rindas galā — tiek pievienots balts pārtraukums
* **Noņemt pārtraukumu**:**divreiz noklikšķiniet** uz parauga
* **Saglabājiet rediģēto gradiento**: noklikšķiniet uz saglabāšanas ikonas blakus gradiento joslai, lai pievienotu rediģēto gradiento iestatījumu sarakstam, lai to varētu izvēlēties atkārtoti

Gradients, ko esat konfigurējis indeksā, tiek saglabāts kopā ar šo indeksu projekta iestatījumos, tādējādi tas saglabājas arī pēc projekta aizvēršanas un atkārtotas atvēršanas.

**Mazāks pieturpunktu skaits**rada izteiktas zonas, kas uztveramas kā klasifikācija;**lielāks pieturpunktu skaits** rada gludus, gandrīz fotoreālistiskus pārejas. Trīs līdz pieci pieturpunkti ir piemēroti prezentācijas slaidiem un klasifikācijas kartēm; seši līdz desmit — vispārējai analīzei; piecpadsmit vai vairāk — detalizētai pārbaudei un publikāciju attēliem.

### Vērtību diapazona iestatīšana

Slēguma vadības elements ir **slīdnis ar diviem rokturiem**, kura diapazons ir no −1 līdz +1, ar rediģējamu teksta lauku katrā galā precīzu vērtību ievadīšanai un**AUTO** pogu.

* Velciet jebkuru rokturi vai ievadiet skaitli attiecīgajā lodziņā un nospiediet Enter
* **AUTO**iestata diapazonu uz attēla derīgo indeksa vērtību**

2. un 98. percentili** — tas ir labs sākumpunkts, kas ignorē novirzes. Chloros rezultātu noapaļo adaptīvi: līdz 4 zīmēm aiz komata ļoti šaurā diapazonā, līdz 3 zīmēm aiz komata šaurā diapazonā un citos gadījumos līdz 2 zīmēm aiz komata
* Jebkura manuāla korekcija ir prioritāra salīdzinājumā ar AUTO, līdz atkārtoti nospiežat AUTO

Piemērs NDVI logiem:

| Mērķis                                    | Min  | Maks |
| --------------------------------------- | ---- | --- |
| Rādīt visu                         | −1,0 | 1,0 |
| Tikai veģetācija, izņemot augsni un ūdeni | 0,2  | 0,9 |
| Tikai veselīga veģetācija                 | 0,5  | 0,9 |
| Izcelt stresa pazīmes                        | 0,2  | 0,5 |

Logu sašaurinot, palielinās kontrasts jūsu interesējošajā apgabalā, un viss pārējais tiek izstumts ārpus diapazona — tur **apgriešanas režīms** nosaka, kas ar to notiek.***

## Apgriešanas režīmi

Kad pikseļa indeksa vērtība atrodas ārpus minimālā/maksimālā loga, apgriešanas režīms nosaka, kā tas tiek attēlots.

| Izvēlnes nosaukums                  | Saglabātā vērtība      | Vērtības ārpus diapazona pikseļi tiek attēloti kā                                                                                                |
| ------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Minimums un maksimums** (noklusējums) | `clip`            | Tuvākā gradienta gala krāsa — vērtības zem minimuma iegūst pirmo krāsu, vērtības virs maksimuma iegūst pēdējo |
| **Caurspīdīgs fons**      | `transparent`     | Pilnīgi caurspīdīgs (reāls alfa kanāls)                                                                                                  |
| **Indeksa fons**| `indexColor`      | Pelēktoņu skala, izstieptas pāri attēla**pilnajam** indeksa diapazonam, tādējādi struktūra, kas atrodas ārpus diapazona, joprojām ir redzama pelēkā krāsā                |
| **Oriģinālais fons**         | `backgroundColor` | Pats pamatattēls, tādējādi krāsu pārklājums atrodas virs reālās ainas                                                |

| Režīms                       | Vispiemērotākais                               | Izskats                                      |
| -------------------------- | -------------------------------------- | ----------------------------------------- |
| **Minimums un maksimums**      | Pilnīgs datu attēlojums, zinātniskā analīze | Katrs pikselis ir krāsots                      |
| **Caurspīdīgs fons** | GIS pārklājumi, vērtību diapazona izdalīšana   | Krāsa loga iekšpusē, ārpus tā nekas |
| **Indeksa fons**       | Akcentēšana, saglabājot datu kontekstu    | Krāsa iekšpusē, pelēks ārpusē               |
| **Oriģinālais fons**    | Ziņojumi un prezentācijas              | Krāsa iekšpusē, fotogrāfija ārpusē         |

{% hint style="info" %}
**Pikseļi bez datiem vienmēr ir caurspīdīgi, jebkurā režīmā.** Pikselis, kura indekss nav galīgs (dalīšana ar 0/0) vai ir tieši −1,0 vai +1,0 (saturācijas indikatori, kad vienā joslā rādītājs ir nulle, bet otrā — nē), tiek uzskatīts par pikseli bez datiem, nevis par galējo vērtību. Tādējādi pārspīlētās gaismas un pilnīgi tumšās ēnas netiek iekļautas krāsu skalā, nevis attēlotas kā visekstrēmākie rādītāji kadrā. Tas pats noteikums nosaka, kuri pikseļi tiek izmantoti AUTO sliekšņu un indeksa histogrammas aprēķināšanai, lai visi trīs rādītāji saskanētu.
{% endhint %}

Caurspīdīgums tiek saglabāts, ja eksportētais fails tiek saglabāts kā PNG. To nevar attēlot JPG formātā.

***

## Vērtību nolasīšana, veicot regulēšanu**Kursora vērtības** panelis zem konfigurācijas paneļa ir „Sandbox” mērīšanas instruments:

* Pārvietojiet kursoru pār attēlu un nolasiet katra kanāla avota vērtības, kā arī indeksa vērtību atsevišķā rindā
* Nospiediet pogu **INDEX** virs histogrammas, lai redzētu indeksa vērtību sadalījumu kadrā, kur abas klipa sliekšņa līnijas ir attēlotas kā oranžas pārtrauktas līnijas, bet kursora vērtība — kā balta līnija; tas ir ātrākais veids, kā izvēlēties logu, kurā faktiski atrodas jūsu dati
* Ieslēdziet **CURSOR**, lai redzētu atzīmju līnijas pie vērtībām zem kursora
* Palieliniet attēlu vairāk nekā 60× (mazāk, ja ir iestatīts GSD bloka izmērs), lai izceltu atsevišķus attēlotos pikseļus ar mainīgu vērtību

Praktiska darbības kārtība:

1. Pierakstiet vērtības virs veselīgas veģetācijas, stresa skartas veģetācijas, kailas augsnes un ūdens
2. Pārbaudiet, kur šie klasteri atrodas indeksa histogrammā
3. Iestatiet minimālo un maksimālo vērtību, lai ierobežotu jums interesējošo klasteri
4. Izvēlieties apgriešanas režīmu — _Original Background_ saglabā redzamu ainu ap to

***

## Eksportēšana no Sandbox

Viss iepriekš minētais ir reāllaika priekšskatījums, kamēr to nesaglabājat. Poga **Eksportēt/Saglabāt attēlu(-us)** sānu joslas augšdaļā atver logu, kas pārklāj sānu joslu (nevis attēlu, tādējādi jūs joprojām varat redzēt to, par ko pieņemat lēmumu).

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>### Opcijas

| Opcija                          | Efekts                                                                                                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Piemērot pašreizējam attēlam**      | Saglabā tieši redzamo attēlu ar šiem iestatījumiem                                                                                                |
| **Piemērot visiem projekta attēliem** | Atkārto identisku konfigurāciju katram projekta attēlam. Attēli, kuros nav šim indeksam nepieciešamās joslas, tiek izlaisti un netiek uzskatīti par kļūdainiem |
| **Indeksa/LUT gradienta josla**      | Katram eksportam tiek saglabāts arī atsevišķs leģendas attēls ar norādītu vērtību diapazonu                                                                     |
| **Indeksa histogramma**             | Katram eksportam tiek saglabāts arī atsevišķs histogrammas attēls, kurā redzami datu minimālās un maksimālās vērtības, kā arī klipēšanas sliekšņi                                               |

Ja attēla cilnē **GSD bloka izmērs** ir lielāks par 1, logā par to tiek parādīts brīdinājums pirms apstiprināšanas: eksportēšanas procesā tiek saglabāts tas, ko redzat, ieskaitot bloku vidējošanu. Ja vēlaties pilnu izšķirtspēju, vispirms iestatiet GSD kontroli atpakaļ uz 1.

### Kur tiek saglabāti faili

Katrs **Eksportēt**klikšķis izveido**jaunu, nekad atkārtoti neizmantotu mapi**:

```
<project folder>/Sandbox_Exports/<IndexName>_<Index|LUT>_<NNN>/
```

Piemēri: `Sandbox_Exports/NDVI_LUT_001/`, tad `Sandbox_Exports/NDVI_LUT_002/` nākamajai eksportēšanai. Numurēšana tiek veidota, pārskatot to, kas jau atrodas diskā, tādējādi tā saglabājas arī pēc sistēmas pārstartēšanas un mapju manuālas dzēšanas. Nekas netiek pārrakstīts — „Sandbox” galvenais mērķis ir salīdzināt vienu mēģinājumu ar iepriekšējo.

Mapē, katram attēlam:

| Fails                                                   | Saturs                                                   |
| ------------------------------------------------------ | ---------------------------------------------------------- |
| `<source name>_<IndexName>_<Index\|LUT>.png`           | Renderētais attēls, pikselis par pikseli tāds, kādu to parādīja skatītājs |
| `<source name>_<IndexName>_<Index\|LUT>_legend.png`    | Gradientu joslas papildu fails, ja pieprasīts                     |
| `<source name>_<IndexName>_<Index\|LUT>_histogram.png` | Indeksa histogrammas papildu fails, ja pieprasīts                  |

Abas papildu joslas vienmēr tiek saglabātas **pilnā izšķirtspējā**, pat ja galvenajam attēlam ir piemērota blokveida vidējā vērtība: bloka izmērs atbilst ekrāna izšķirtspējai, un abas papildu joslas atspoguļo patiesās indeksa vērtības katram pikselim. Tās arī attēlo vairāk informācijas nekā ekrānā redzamās versijas — abas norāda gan izstiepšanas logu, gan patiesos datu minimālos un maksimālos vērtības, tādējādi saglabātā leģenda ir lasāma arī pēc vairākiem mēnešiem, neatraujot projektu.

### Gaitas rādītāji un rezultāti

Visa projekta eksportēšana aizņem dažas minūtes, tāpēc process ziņo par gaitu reāllaika kanālā, nevis bloķē sistēmu:

* Gaitas josla parāda `current / total` un failu, kas tiek rakstīts
* Kad eksportēšana ir pabeigta, logā tiek parādīts, cik attēlu tika eksportēti, cik tika izlaisti, kā arī izvades mapes ceļš
* Izlaistie attēli tiek uzskaitīti kopā ar iemeslu (tiek parādīti līdz pieciem, pēc tam rinda „+N vairāk”). Parastais iemesls ir slānis, kurā nav kanālu, kas nepieciešami šim indeksam
* Ja projektā **neviens** attēls nevar izmantot indeksu, darbības rezultāts tiek ziņots kā kļūda, nevis atstājot tukšu mapi

Vienlaikus var darboties tikai viens eksportēšanas process izolētajā vidē. Ja tiek mēģināts sākt otro procesu, kamēr pirmais vēl noris, tiek parādīts skaidrs paziņojums, nevis ļaujot abiem procesiem cīnīties par to pašu projekta failu.

### Tīkls parāda eksportēšanas rezultātus

Katrs pabeigtais eksportēšanas process parādās kā atsevišķa poga [attēlu tīklā](image-grid.md) rīkjoslā ar apzīmējumu `<IndexName> <Index|LUT> <NNN>`. Tādā veidā var salīdzināt eksportēšanas procesus: veiciet eksportu divas reizes ar atšķirīgiem gradientiem vai sliekšņiem, pēc tam pārslēdzieties starp abām pogām režģī.

***

## Pielāgotas indeksa formulas (Chloros+)

{% hint style="info" %}
**Kur tās izveidot**: „Sandbox” sānu joslā vai**Projekta iestatījumos** pirms apstrādes. Abos gadījumos tās tiek ierakstītas vienā un tajā pašā projekta līmeņa sarakstā.
{% endhint %}

1. Atveriet pielāgotās formulas kalkulatoru no indeksa formulu nolaižamā izvēlnes (nepieciešams pieteikties ar atbilstošu Chloros+ abonementu)
2. Ierakstiet formulu, izmantojot **joslu un pozīciju simbolus** `x`, `y`, `z`, `a`, `b`, `c` — nevis joslu nosaukumus
3. Pieejamie operatori: `+`, `-`, `*`, `/`, `^` un `()` grupēšanai
4. Pieejamās funkcijas: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
5. Nosauciet un saglabājiet to — tā parādīsies formulas nolaižamās izvēlnes apakšā, un jūs varat piesaistīt tās vietas, velkot kanālu apļus, tieši tāpat kā iebūvētajai iestatījumam

```

Modified NDVI with an offset:   (y-x)/(y+x+0.5)
Simple ratio:                   y/x
Three-band difference:          (y-x)/(y+x-z)
Squared ratio:                  (y/x)^2
```

{% hint style="warning" %}
**Pielāgotās formulas ir pieejamas tikai lietotāja saskarnē.** Opcija CLI/SDK `--indices` paplašina 22 iebūvēto iestatījumu sarakstu un automātiski izlaiž visu pārējo, ieskaitot jūsu pielāgotās formulas. Lai pielietotu pielāgotu formulu vairākiem attēliem vienlaikus, konfigurējiet to projekta iestatījumos un palaidiet apstrādi, vai izmantojiet Sandbox eksporta funkciju „Piemērot visiem projekta attēliem”.
{% endhint %}

***

## Problēmu novēršana

### „Šim slānim nav kanālu, kas nepieciešami šim indeksam”

Formula nolasītu kanāla pozīciju, kādas pašreizējam slānim nav — piemēram, trīs pozīciju indeksu vienkanāla vai divkanālu failā. Pārejiet uz daudzjoslu slāni (reflektances vai debayered) vai izvēlieties indeksu, kas atbilst jūsu kameras filtram.

### „Neizdevās sazināties ar attēlu apstrādes backend”

Backend neatbild. Pārbaudiet cilni „Log“; ja aizmugure tiek pārstartēta, „Sandbox“ atgūstas pati, tiklīdz tā atkal sāk darboties.

### Attēls nemainījās, kad es vilku apli

Formula vēl nav pabeigta. Nepabeigta formula tiek uzskatīta par parastu stāvokli vilkšanas laikā — nekas netiek renderēts un nekas netiek ziņots kā kļūda. Aizpildiet katru lauciņu, ko izmanto formula.

### Viss attēls ir vienā krāsā

Jūsu klipa logs, visticamāk, atrodas tālu ārpus datu diapazona. Nospiediet **AUTO**, lai to pielāgotu 2. vai 98. percentilam, vai ieslēdziet**INDEX** histogrammu, lai redzētu, kur faktiski atrodas dati.

### Eksportētās krāsas neatbilst tam, ko redzēju

Tām vajadzētu atbilst — eksporta ceļš ir apzināti veidots kā reālā priekšskatījuma spoguļattēls, ieskaitot klipēšanas režīma alfu, un bloku vidējā vērtība tiek piemērota _pēc_ krāsošanas tieši tāpat, kā to dara skatītājs. Ja tās atšķiras, pārbaudiet, vai GSD bloka izmērs nav mainījies starp apskati un eksportēšanu.

***

## Turpmākie soļi

* [**Attēla slāņi**](image-layers.md) — uz kura slāņa veikt indeksu un ko nozīmē tā vērtības
* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — kursora rādījums, histogramma un GSD vadība sīkāk
* [**Multispektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) — katra iestatījuma paraugs uz katras virsmas
* [**Projekta iestatījumi**](../project-settings/project-settings.md) — atrasto iestatījumu saglabāšana apstrādes ciklā
