# Mērķa attēlu atlase

Atzīmējot, kuros attēlos ir kalibrēšanas mērķi, programma „Chloros” precīzi zina, kur tos meklēt. Ja kolonnā „Mērķis” ir atzīmēts vismaz viens attēls, programma „Chloros” skenē **tikai atzīmētos attēlus** — tādējādi mērķu atzīmēšana ne tikai paātrina apstrādi, bet arī novērš situāciju, kad apsekojuma attēlus tiek sajaukti ar mērķiem.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Kāpēc atzīmēt mērķa attēlus?

### Atzīmēšana kontrolē skenēšanu

Kad jūs atzīmējat konkrētus attēlus kolonnā „Mērķis”:

* „Chloros” skenē mērķus tikai atzīmētajos attēlos
* Mērķu atpazīšana notiek daudz ātrāk
* Apsekojuma attēli nevar radīt kļūdainu mērķu atpazīšanu

Ja **nav** atzīmēts neviens attēls, „Chloros” pārslēdzas uz visu projekta attēlu skenēšanu:

* Mērķu atklāšanas algoritmi tiek izpildīti katram attēlam
* Nevajadzīgi tiek pārbaudīti simtiem vai tūkstošiem attēlu
* Apstrāde aizņem ievērojami ilgāku laiku, īpaši lielu datu kopu gadījumā

{% hint style="success" %}
**Ātruma uzlabojums**: Atzīmējot 2–3 mērķa attēlus 500 attēlu datu kopā, mērķa atklāšanas laiku var samazināt no vairāk nekā 30 minūtēm līdz mazāk nekā 1 minūtei.
{% endhint %}

***

## Kā atzīmēt mērķa attēlus

### 1. solis: Identificējiet savus mērķa attēlus

Pārskatiet importētos attēlus failu pārlūkā un nosakiet, kuros attēlos ir kalibrēšanas mērķi.

**Bieži sastopami scenāriji:*** **Pirms uzņemšanas mērķis**: Uzņemts pirms sesijas sākuma
* **Mērķis pēc uzņemšanas**: uzņemts pēc sesijas pabeigšanas
* **Mērķi uzņemšanas zonā**: mērķi, kas novietoti uzņemšanas zonā
* **Vairāki mērķi**: 2–3 mērķa attēli katrā sesijā (ieteicams)

### 2. solis: Pārbaudiet mērķa kolonnu <img src="../.gitbook/assets/image (33).png" alt="" data-size="original">

Katram attēlam, kurā ir kalibrēšanas mērķis:

1. Atrodiet attēlu failu pārlūka tabulā
2. Atrodiet **Mērķis** kolonnu (pa labi visā galā)
3. Noklikšķiniet uz izvēles rūtiņas Mērķis kolonnā pie šī attēla
4. Atkārtojiet šo darbību visiem attēliem, kuros ir mērķi

### 3. solis: Pārbaudiet savu atlasi

Pirms apstrādes vēlreiz pārbaudiet:

* [ ] Ir atzīmēti visi attēli ar kalibrēšanas mērķiem
* [ ] Nav nejauši atzīmēti attēli, kuros nav mērķu
* [ ] Mērķi ir skaidri redzami atzīmētajos attēlos

***

## LATTICE: Mērķi ir fakultatīvi, ja notiek datu ieguve (DAQ)

LATTICE multispektrālajām kamerām attēlā esošais kalibrēšanas mērķis ir **viens no diviem** iespējamajiem atstarojuma etaloniem:

* **Kadrā esošais mērķis**: ja atzīmētais mērķa attēls iztur „Chloros” kvalitātes (QA) pārbaudi, mērķis kļūst par**absolūto atstarošanas atsauci** apkārtējiem attēliem.
* **DAQ lejupvērstā starojuma intensitāte**: ja mērķa nav (vai tas neatbilst QA prasībām), „Chloros” aprēķina atstarojumu, izmantojot DAQ gaismas sensora reģistrēto lejupvērsto starojuma intensitāti (ρ = π·L/E). Ja jūsu uzņemtajiem attēliem atbilst `.daq` vai DAQ-M `.csv` ieraksts, jūs iegūsit kalibrētu atstarojumu**pilnīgi bez jebkādiem mērķa attēliem**.

Šī automātiskā darbība ir noklusējuma iestatījums. Failā „CLI” / „SDK” tas atbilst `--reflectance-source auto`; jūs varat arī piespiest izmantot `target` (stingrs — bez DAQ aizstāšanas) vai `daq` (DAQ ir noteicošais). Skatīt [CLI atsauci](../reference/cli-reference.md#per-product-export-toggles-lattice-multispectral).

**LATTICE mērķu ģeometrijas**: papildus klasiskajai paneļu atpazīšanai, ko izmanto Survey3, LATTICE apstrāde atbalsta**ArUco marķētus mērķus**,**fiksētu ROI mērķus**un**sloksnes mērķus**, kas konfigurēti katram projektam atsevišķi.**Izmērītos** mērķa atstarojuma skenējumus katrai vienībai var iesniegt pēc sērijas numura (CLI: `--target-reflectance-dir`, viens `<serial>.csv` katrai mērķa vienībai), izmantojot nominālos T3/T4P spektrus kā rezerves variantu.

{% hint style="info" %}
**F988 modulis**: F988 atstarošanas koeficients tiek kalibrēts, izmantojot atstarošanas paneli uz vietas: šī josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tāpēc programma „Chloros” izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem. Ja F988 modulis tiek apstrādāts, izmantojot tikai DAQ, programma „Chloros” noraida uz DAQ balstīto atstarojumu šim diapazonam (izlaišanas iemesls `dls-uncalibrated-band-988`) — atbalstītā metode ir paneļa darba plūsma.
{% endhint %}

***

## Labākā prakse mērķa attēlu uzņemšanai

### Mērķa attēlu uzņemšanas vadlīnijas

**Laiks:**

* Uzņemiet mērķa attēlus tieši pirms uzņemšanas sesijas sākuma un tās laikā
* Tādā pašā apgaismojumā kā jūsu DAQ gaismas sensors
* Ideāli būtu uzņemt mērķa attēlus pēc iespējas biežāk, lai iegūtu vislabākos rezultātus. Pretējā gadījumā gaismas sensora dati tiks izmantoti, lai laika gaitā pielāgotu kalibrāciju.

**Kameras novietojums:**

* Turiet kameru virs mērķa tā, lai tas būtu centrā un aizņemtu apmēram 40–60 % no attēla centra.
* Kameru turiet paralēli mērķa virsmai vai virs tās

**Apgaismojums:**

* Tādi paši vides apgaismojuma apstākļi kā jūsu DAQ gaismas sensoram
* Izvairieties no ēnām uz mērķa virsmām
* Neaizsedziet gaismas avotu ar savu ķermeni, transportlīdzekli vai augāju
* Apmākušos apstākļos tiek iegūti visvienmērīgākie rezultāti

**Mērķa stāvoklis:**

* Uzturiet mērķa paneļus tīrus un sausus
* Visiem mērķa paneļiem (piemēram, visiem 4 paneļiem uz T4) jābūt skaidri redzamiem un neaizsegtām
* Ja iespējams, mērķi novietojiet perpendikulāri gaismas avotam vai virs tā

### Cik daudz mērķa attēlu?

**Minimums:**1 mērķa attēls vienā sesijā.**Ieteicams:** 3–5 mērķa attēli vienā sesijā.**Labākā prakse:**

* 3–5 attēli, kas uzņemti īsi pēc gaismas sensora reģistrēšanas sākuma
* Lai iegūtu labākus rezultātus, mainiet kameras novietojumu starp attēlu uzņemšanas reizēm
* Pēc izvēles: periodiski sesijas vidū, ja apgaismojuma apstākļi pastāvīgi mainās

***

## Darbs ar vairākām kamerām

### Divu kameru konfigurācijas

Ja vienlaikus izmantojat divas „MAPIR” kameras (piem., Survey3W RGN + Survey3N OCN):

1. Uzņemiet mērķa attēlus ar **abām kamerām** vienlaikus
2. Abām kamerām izmantojiet **vienu un to pašu fizisko mērķi**

3. Failu pārlūkā atzīmējiet mērķa attēlus**abiem kameru tipiem**

4. Programma „Chloros” katras kameras kalibrēšanai izmantos atbilstošos mērķus

### Kolonna „Kameras modelis”

Kolonna **„Kameras modelis”** palīdz noteikt, kuri attēli ir no kuras kameras:

* Survey3W\_RGN
* Survey3N\_OCN
* LATT-M3M-L41-F550
* LATT-M3C-L87-FRGN
* utt.

Izmantojiet šo sleju, lai pārliecinātos, ka esat atzīmējuši mērķus katram kameras tipam savā projektā.

***

## Mērķu atpazīšanas iestatījumi

### Atpazīšanas jutības pielāgošana

Ja programma „Chloros” neatkopj jūsu mērķus pareizi, pielāgojiet šos iestatījumus sadaļā [Projekta iestatījumi](adjusting-project-settings.md):**Minimālā kalibrēšanas parauga platība (px):*** **Noklusējums**: 25 pikseļi
* **Palieliniet**, ja rodas kļūdaina atpazīšana attiecībā uz maziem artefaktiem
* **Samaziniet**, ja mērķi netiek atpazīti**Minimālā mērķu grupēšana (0–100):*** **Noklusējums**: 60
* **Palieliniet**, ja mērķi tiek sadalīti vairākās atklāšanās
* **Samaziniet**, ja mērķi ar krāsu variācijām netiek pilnībā atklāti

{% hint style="info" %}
**Padoms par CLI**: `chloros-cli process` atbalsta tos pašus regulētājus (`--min-target-size`, `--target-clustering`), un tā `--target`/`--targets` karodziņš atzīmē visu ievades mapi kā „tikai mērķu panelim”. Skatīt [CLI atsauci](../reference/cli-reference.md).
{% endhint %}

***

## Bieži sastopamas problēmas ar mērķa attēliem

### Problēma: mērķi nav atklāti

**Iespējamie iemesli:**

* Mērķa attēli nav atzīmēti failu pārlūkā
* Mērķis kadrā ir pārāk mazs (&lt; 30 % no attēla)
* Nepietiekams apgaismojums (ēnas, atspīdumi)
* Pārāk stingri mērķa atklāšanas iestatījumi

**Risinājumi:**

1. Pārbaudiet, vai kolonnā „Mērķis” ir atzīmēti pareizie attēli
2. Pārbaudiet mērķa attēla kvalitāti priekšskatījumā
3. Ja kvalitāte ir slikta, atkārtoti uzņemiet mērķus
4. Vajadzības gadījumā pielāgojiet mērķu atklāšanas iestatījumus

### Problēma: Kļūdaini atklāti mērķi

**Iespējamie cēloņi:**

* Baltas ēkas, transportlīdzekļi vai augsnes segums tiek sajaukts ar mērķiem
* Spilgti laukumi veģetācijā
* Pārāk zema atklāšanas jutība

**Risinājumi:**

1. Atzīmējiet tikai faktiskos mērķa attēlus — tiek skenēti tikai atzīmētie attēli
2. Palieliniet minimālo kalibrēšanas parauga platību
3. Palieliniet minimālo mērķu grupēšanas vērtību
4. Pārliecinieties, ka mērķa attēlos redzams tikai mērķis (minimāls fona troksnis)

***

## Pārbaudes pārbaudes saraksts

Pirms apstrādes sākšanas pārbaudiet mērķa attēlu atlasi:

* [ ] Vismaz 1 atzīmēts mērķa attēls katrā sesijā (vai, LATTICE gadījumā, `.daq`/`.csv` ieraksts, kas aptver sesiju)
* [ ] Visos mērķa attēlos ir atzīmētas mērķa kolonnas izvēles rūtiņas
* [ ] Mērķa attēli uzņemti tajā pašā laika periodā, kad veikta apsekošana
* [ ] Mērķi ir skaidri redzami priekšskatījumā, uz tiem noklikšķinot
* [ ] Katrā mērķa attēlā ir redzami visi kalibrēšanas paneļi
* [ ] Uz mērķiem nav ēnu vai šķēršļu
* [ ] Divkameru sistēmai: mērķi ir atzīmēti abu kameru tipiem

***

## Apstrāde bez mērķiem

### LATTICE: ar DAQ ierakstu

Ja DAQ gaismas sensors ir reģistrējis lejupvērsto starojuma intensitāti LATTICE uzņemšanas laikā, mērķis nav nepieciešams:

1. Importējiet failu `.daq` (vai DAQ-M `.csv`) kopā ar attēliem
2. Atstājiet kolonnu „Mērķis” neatzīmētu
3. Atstarošanās tiek aprēķināta automātiski, izmantojot DAQ uz leju vērsto starojuma intensitāti
4. Starojuma intensitātei nekad nav nepieciešams mērķis vai DAQ — tā tiek iegūta vienīgi no kameras rūpnīcas radiometriskās kalibrācijas

### Apstrāde bez jebkāda atsauces avota

Jūs varat veikt apstrādi arī bez mērķiem un bez DAQ:

1. Atstājiet visas „Mērķis” kolonnas izvēles rūtiņas neatzīmētas
2. **Atvienojiet** „Atstarošanas kalibrēšana / baltā balanss” projekta iestatījumos — tādā gadījumā mērķa noteikšana tiek pilnībā izlaista
3. Vignettinga korekcija joprojām tiks piemērota
4. Rezultāts netiks kalibrēts attiecībā uz absolūto atstarošanu (LATTICE multispektrālais režīms joprojām eksportē debayered, priekšskatījuma un starojuma produktus)

{% hint style="warning" %}
**Nav ieteicams zinātniskajam darbam ar „Survey3”**: bez atstarojuma kalibrēšanas „Survey3” pikseļu vērtības atspoguļo tikai relatīvo spilgtumu, nevis zinātniskos atstarojuma mērījumus. Lai iegūtu precīzus un atkārtojamus rezultātus, izmantojiet kalibrēšanas mērķus (vai, „LATTICE” gadījumā, DAQ gaismas sensoru).
{% endhint %}

***

## Turpmākie soļi

Kad esat atzīmējuši mērķa attēlus:

1. **Pārskatiet savus iestatījumus** — skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
2. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet apstrādes gaitu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Lai iegūtu vairāk informācijas par pašiem kalibrēšanas mērķiem, skatiet [Kalibrēšanas mērķi](../calibration-targets.md).
