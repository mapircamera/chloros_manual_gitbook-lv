# Mērķa attēlu atlase

To attēlu atzīmēšana, kuros ir kalibrēšanas mērķi, ir ļoti svarīgs solis, kas ievērojami paātrina Chloros apstrādes procesu. Iepriekš atlasot mērķa attēlus, jūs atbrīvojat Chloros no nepieciešamības pārskatīt katru attēlu jūsu datu kopā, meklējot kalibrēšanas mērķus.

## Kāpēc atzīmēt mērķa attēlus?

### Apstrādes ātrums

Ja mērķa attēli nav atzīmēti, Chloros ir jāveic šādas darbības:

* Pārskatīt katru attēlu jūsu projektā
* Izpildīt mērķa noteikšanas algoritmus katram attēlam
* Nevajadzīgi pārbaudīt simtiem vai tūkstošiem attēlu

**Rezultāts**: Apstrāde var aizņemt ievērojami ilgāku laiku, īpaši lielu datu kopu gadījumā.

### Ar atzīmētiem mērķa attēliem

Kad jūs atzīmējat konkrētus attēlus kolonnā „Mērķis”:

* Chloros mērķus skenē tikai atzīmētajos attēlos
* Mērķu atpazīšana notiek daudz ātrāk
* Kopējais apstrādes laiks ievērojami samazinās

{% hint style="success" %}
**Ātruma uzlabojums**: Atzīmējot 2–3 mērķa attēlus 500 attēlu datu kopā, mērķu atpazīšanas laiku var samazināt no vairāk nekā 30 minūtēm līdz mazāk nekā 1 minūtei.
{% endhint %}

***

## Kā atzīmēt mērķa attēlus

### 1. solis: Identificējiet savus mērķa attēlus

Pārskatiet importētos attēlus failu pārlūkā un identificējiet, kuri attēli satur kalibrēšanas mērķus.

**Bieži sastopami scenāriji:*** **Mērķis pirms uzņemšanas**: Uzņemts pirms sesijas sākuma
* **Mērķis pēc uzņemšanas**: Uzņemts pēc sesijas pabeigšanas
* **Mērķi uz vietas**: Mērķi, kas novietoti uzņemšanas zonā
* **Vairāki mērķi**: 2–3 mērķa attēli vienā sesijā (ieteicams)

### 2. solis: Pārbaudiet mērķa kolonnu

Katram attēlam, kurā ir kalibrēšanas mērķis:

1. Atrodiet attēlu failu pārlūka tabulā
2. Atrodiet **Mērķis** kolonnu (pa labi esošā kolonna)
3. Noklikšķiniet uz izvēles rūtiņas Mērķis kolonnā attiecīgajam attēlam
4. Atkārtojiet šo darbību visiem attēliem, kuros ir mērķi

### 3. solis: Pārbaudiet savu izvēli

Pirms apstrādes pārbaudiet vēlreiz:

* [ ] Visi attēli ar kalibrēšanas mērķiem ir atzīmēti
* [ ] Nav nejauši atzīmēti attēli, kuros nav mērķu
* [ ] Mērķi ir skaidri redzami atzīmētajos attēlos

***

## Labākā prakse attiecībā uz mērķa attēliem

### Mērķa attēlu uzņemšanas vadlīnijas

**Laiks:**

* Uzņemiet mērķa attēlus tieši pirms un visā uzņemšanas sesijas laikā
* Tādās pašās apgaismojuma apstākļos kā jūsu DAQ gaismas sensors
* Ideāli būtu uzņemt mērķa attēlus pēc iespējas biežāk, lai iegūtu labākos rezultātus. Pretējā gadījumā gaismas sensora dati tiks izmantoti, lai laika gaitā pielāgotu kalibrēšanu.

**Kameras novietojums:**

* Turiet kameru virs mērķa tā, lai tas būtu centrēts un aizņemtu apmēram 40–60 % no attēla centra.
* Turiet kameru paralēli/vertikāli pret mērķa virsmu

**Apgaismojums:**

* Tāds pats apgaismojums kā jūsu DAQ gaismas sensoram
* Izvairieties no ēnām uz mērķa virsmām
* Neaizsedziet gaismas avotu ar savu ķermeni, transportlīdzekli vai veģetāciju
* Apmākušos apstākļos tiek iegūti visvienmērīgākie rezultāti

**Mērķa stāvoklis:**

* Uzturiet mērķa paneļus tīrus un sausus
* Visiem 4 paneļiem jābūt skaidri redzamiem un neaizsegtām
* Ja iespējams, mērķi novietojiet perpendikulāri/nadīri pret gaismas avotu

### Cik daudz mērķa attēlu?

**Minimums:**1 mērķa attēls vienā sesijā.**Ieteicams:** 3–5 mērķa attēli vienā sesijā.**Labākā prakse:**

* 3–5 attēli, kas uzņemti īsi pēc gaismas sensora reģistrēšanas sākuma
* Lai iegūtu labākus rezultātus, mainiet kameras novietojumu starp uzņēmumiem
* Pēc izvēles: periodiski sesijas vidū, ja apgaismojuma apstākļi pastāvīgi mainās

***

## Darbs ar vairākām kamerām

### Divu kameru konfigurācijas

Ja vienlaikus izmantojat divas MAPIR kameras (piem., Survey3W RGN + Survey3N OCN):

1. Uztveriet mērķa attēlus ar **abām kamerām** vienlaikus
2. Izmantojiet **vienu un to pašu fizisko mērķi** abām kamerām
3. Atzīmējiet mērķa attēlus **abiem kameru tipiem** failu pārlūkprogrammā
4. Chloros izmantos atbilstošos mērķus katras kameras kalibrēšanai

### Kameras modeļa kolonna

**Kameras modeļa** kolonna palīdz identificēt, kuri attēli ir no kuras kameras:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* utt.

Izmantojiet šo kolonnu, lai pārbaudītu, vai esat atzīmējis mērķus katram kameras tipam savā projektā.

***

## Mērķu atklāšanas iestatījumi

### Atklāšanas jutības regulēšana

Ja Chloros neatklāj jūsu mērķus pareizi, pielāgojiet šos iestatījumus [Projekta iestatījumos](adjusting-project-settings.md):**Minimālā kalibrēšanas parauga platība:*** **Noklusējums**: 25 pikseļi
* **Palieliniet**, ja tiek iegūti kļūdaini atklājumi uz maziem artefaktiem
* **Samaziniet**, ja mērķi netiek atklāti**Minimālā mērķu grupēšana:*** **Noklusējums**: 60
* **Palieliniet**, ja mērķi tiek sadalīti vairākos atklājumos
* **Samaziniet**, ja mērķi ar krāsu variācijām netiek pilnībā atklāti***

## Bieži sastopamas problēmas ar mērķa attēliem

### Problēma: Mērķi netiek atklāti

**Iespējamie iemesli:**

* Mērķa attēli nav atzīmēti failu pārlūkā
* Mērķis kadrā ir pārāk mazs (&lt; 30% no attēla)
* Slikts apgaismojums (ēnas, atspīdumi)
* Pārāk stingri mērķa atklāšanas iestatījumi

**Risinājumi:**

1. Pārbaudiet, vai kolonnā „Mērķis” ir atzīmēti pareizie attēli
2. Pārskatiet mērķa attēla kvalitāti priekšskatījumā
3. Ja kvalitāte ir slikta, nofotografējiet mērķus no jauna
4. Vajadzības gadījumā pielāgojiet mērķu atklāšanas iestatījumus

### Problēma: nepareizi atklāti mērķi

**Iespējamie cēloņi:**

* Baltas ēkas, transportlīdzekļi vai augsnes segums tiek sajaukts ar mērķiem
* Spilgti laukumi veģetācijā
* Pārāk zema atklāšanas jutība

**Risinājumi:**

1. Atzīmējiet tikai faktiskos mērķa attēlus, lai ierobežotu atklāšanas apjomu
2. Palieliniet minimālo kalibrēšanas parauga platību
3. Palieliniet minimālo mērķu grupēšanas vērtību
4. Pārliecinieties, ka mērķa attēlos redzams tikai mērķis (minimāls fona troksnis)

***

## Pārbaudes saraksts

Pirms apstrādes sākšanas pārbaudiet savu mērķa attēlu atlasi:

* [ ] Vismaz 1 atzīmēts mērķa attēls katrā sesijā
* [ ] Visos mērķa attēlos ir atzīmētas mērķa kolonnas izvēles rūtiņas
* [ ] Mērķa attēli uzņemti tajā pašā laika periodā, kad veikta apsekošana
* [ ] Mērķi ir skaidri redzami priekšskatījumā, uz tiem noklikšķinot
* [ ] Katrā mērķa attēlā redzami visi 4 kalibrēšanas paneļi
* [ ] Uz mērķiem nav ēnu vai šķēršļu
* [ ] Divkameru gadījumā: mērķi atzīmēti abu kameru tipiem

***

## Apstrāde bez mērķiem

### Apstrāde bez kalibrēšanas mērķiem

Lai gan tas nav ieteicams zinātniskiem darbiem, jūs varat veikt apstrādi bez mērķiem:

1. Atstājiet visas mērķa kolonnas izvēles rūtiņas neatzīmētas
2. **Atvienojiet** &quot;Atstarošanas kalibrēšanu&quot; projekta iestatījumos
3. Vignette korekcija joprojām tiks piemērota
4. Rezultāts netiks kalibrēts absolūtajai atstarošanai

{% hint style="warning" %}
**Nav ieteicams**: Bez atstarojuma kalibrēšanas pikseļu vērtības atspoguļo tikai relatīvo spilgtumu, nevis zinātniskus atstarojuma mērījumus. Lai iegūtu precīzus un atkārtojamus rezultātus, izmantojiet kalibrēšanas mērķus.
{% endhint %}

***

## Turpmākie soļi

Kad esat atzīmējuši mērķa attēlus:

1. **Pārskatiet savus iestatījumus** — skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
2. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet progresu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Lai iegūtu vairāk informācijas par kalibrēšanas mērķiem, skatiet [Kalibrēšanas mērķi](../calibration-targets.md).
