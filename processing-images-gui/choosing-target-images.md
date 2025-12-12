# Mērķa attēlu izvēle

Attēlu, kuros ir kalibrēšanas mērķi, atzīmēšana ir ļoti svarīgs solis, kas ievērojami paātrina Chloros apstrādes procesu. Iepriekš atlasot mērķa attēlus, jums nav nepieciešams Chloros skenēt katru attēlu datu kopā, lai atrastu kalibrēšanas mērķus.

## Kāpēc atzīmēt mērķa attēlus?

### Apstrādes ātrums

Ja mērķa attēli netiek atzīmēti, Chloros ir jāveic šādas darbības:

* Jāskenē katrs attēls projektā.
* Jāizpilda mērķa noteikšanas algoritmi katram attēlam.
* Jāpārbauda simtiem vai tūkstošiem attēlu, kas nav nepieciešami.

**Rezultāts**: apstrāde var aizņemt ievērojami vairāk laika, īpaši lielu datu kopu gadījumā.

### Ar atzīmētiem mērķa attēliem

Kad jūs atzīmējat mērķa kolonnu konkrētiem attēliem:

* Chloros skenē tikai atzīmētos attēlus, meklējot mērķus
* Mērķu atklāšana notiek daudz ātrāk
* Kopējais apstrādes laiks ir ievērojami samazināts

{% hint style=&quot;success&quot; %}
**Ātruma uzlabojums**: atzīmējot 2–3 mērķa attēlus 500 attēlu datu kopā, mērķa noteikšanas laiku var samazināt no vairāk nekā 30 minūtēm līdz mazāk nekā 1 minūtei.
{% endhint %}

***

## Kā atzīmēt mērķa attēlus

### 1. solis: Identificējiet mērķa attēlus

Pārskatiet importētos attēlus failu pārlūkprogrammā un identificējiet, kuri attēli satur kalibrēšanas mērķus.

**Bieži sastopami scenāriji:**

* **Mērķis pirms uzņemšanas**: uzņemts pirms sesijas sākuma
* **Mērķis pēc uzņemšanas**: uzņemts pēc sesijas pabeigšanas
* **Mērķi laukā**: mērķi, kas atrodas uzņemšanas zonā
* **Vairāki mērķi**: 2–3 mērķa attēli vienā sesijā (ieteicams)

### 2. solis: pārbaudiet mērķa kolonnu

Katram attēlam, kas satur kalibrēšanas mērķi:

1. Atrodiet attēlu failu pārlūkā
2. Atrodiet **Mērķis** kolonnu (pa labi)
3. Noklikšķiniet uz izvēles rūtiņas mērķa kolonā pie šī attēla
4. Atkārtojiet šo darbību visiem attēliem, kas satur mērķus

### 3. solis: pārbaudiet savu izvēli

Pirms apstrādes pārbaudiet:

* [ ] Ir atzīmēti visi attēli ar kalibrēšanas mērķiem
* [ ] Nav nejauši atzīmēti attēli, kas nav mērķa attēli
* [ ] Mērķi ir skaidri redzami atzīmētajos attēlos

***

## Labākā prakse mērķa attēlu izmantošanai

### Mērķa attēlu uzņemšanas vadlīnijas

**Laiks:**

* Uzņemiet mērķa attēlus tieši pirms un visā uzņemšanas sesijas laikā
* Tādos pašos apgaismojuma apstākļos kā DAQ gaismas sensors
* Ideāli būtu uzņemt mērķa attēlus pēc iespējas biežāk, lai iegūtu labākos rezultātus. Pretējā gadījumā gaismas sensora dati tiks izmantoti, lai laika gaitā pielāgotu kalibrēšanu.

**Kameras novietojums:**

* Turiet kameru virs mērķa tā, lai tā būtu centrēta un aizņemtu apmēram 40–60 % attēla centra.
* Turiet kameru paralēli/nadīram mērķa virsmai

**Apgaismojums:**

* Tāds pats apgaismojums kā jūsu DAQ gaismas sensoram
* Izvairieties no ēnām uz mērķa virsmām
* Neaizsedziet gaismas avotu ar savu ķermeni, transportlīdzekli vai veģetāciju
* Apmākušos apstākļos tiek iegūti visvienmērīgākie rezultāti

**Mērķa stāvoklis:**

* Uzturiet mērķa paneļus tīrus un sausus
* Visiem 4 paneļiem jābūt skaidri redzamiem un neaizsegtām
* Mērķi pēc iespējas perpendikulāri/nadīri gaismas avotam

### Cik daudz mērķa attēlu?

**Minimums:** 1 mērķa attēls vienā sesijā. **Ieteicams:** 3–5 mērķa attēli vienā sesijā.

**Labākā prakse:**

* 3–5 attēli, kas uzņemti īsi pēc gaismas sensora ieraksta sākuma
* Lai iegūtu labākos rezultātus, pagrieziet kameru starp uzņēmumiem
* Pēc izvēles: periodiski sesijas vidū, ja apgaismojuma apstākļi mainās pastāvīgi

***

## Darbs ar vairākām kamerām

### Divu kameru uzstādījumi

Ja vienlaikus izmantojat divas MAPIR kameras (piemēram, Survey3W RGN + Survey3N OCN):

1. Uzņemiet mērķa attēlus ar **abām kamerām** vienlaikus.
2. Abām kamerām izmantojiet **vienu un to pašu fizisko mērķi**.
3. Failu pārlūkā atzīmējiet mērķa attēlus **abiem kameru tipiem**.
4. Chloros izmantos atbilstošus mērķus katras kameras kalibrēšanai.

### Kameras modeļa kolonna

Kolonna **Kameras modelis** palīdz identificēt, kuri attēli ir uzņemti ar kuru kameru:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* utt.

Izmantojiet šo kolonnu, lai pārbaudītu, vai esat atzīmējis mērķus katram kameras tipam savā projektā.

***

## Mērķu noteikšanas iestatījumi

### Noteikšanas jutīguma pielāgošana

Ja Chloros nekonstatē jūsu mērķus pareizi, pielāgojiet šos iestatījumus [Projekta iestatījumos](adjusting-project-settings.md):

**Minimālā kalibrēšanas parauga platība:**

* **Noklusējums**: 25 pikseļi
* **Palieliniet**, ja tiek iegūti kļūdaini atklājumi uz maziem artefaktiem
* **Samaziniet**, ja mērķi netiek atklāti

**Minimālā mērķu klasterizācija:**

* **Noklusējums**: 60
* **Palieliniet**, ja mērķi tiek sadalīti vairākās atklāšanās
* **Samaziniet**, ja mērķi ar krāsu variācijām netiek pilnībā atklāti

***

## Bieži sastopamas problēmas ar mērķa attēliem

### Problēma: netiek atklāti mērķi

**Iespējamie cēloņi:**

* Mērķa attēli nav atzīmēti failu pārlūkā
* Mērķis ir pārāk mazs kadrā (&lt; 30 % no attēla)
* Slikts apgaismojums (ēnas, atspīdumi)
* Mērķu atklāšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārbaudiet, vai mērķu kolonna ir atzīmēta pareizajiem attēliem
2. Pārskatiet mērķu attēlu kvalitāti priekšskatījumā
3. Ja kvalitāte ir slikta, atkārtoti uzņemiet mērķus
4. Ja nepieciešams, pielāgojiet mērķu atklāšanas iestatījumus

### Problēma: kļūdaini mērķu atklājumi

**Iespējamie cēloņi:**

* Baltas ēkas, transportlīdzekļi vai zemes segums, kas tiek sajaukti ar mērķiem
* Gaiši plankumi veģetācijā
* Pārāk zema atklāšanas jutība

**Risinājumi:**

1. Atzīmējiet tikai faktiskos mērķa attēlus, lai ierobežotu atklāšanas apjomu
2. Palieliniet minimālo kalibrēšanas parauga platību
3. Palieliniet minimālo mērķa klasterizācijas vērtību
4. Pārliecinieties, ka mērķa attēlos redzams tikai mērķis (minimāls fona traucējums)

***

## Pārbaudes kontrolsaraksts

Pirms apstrādes sākšanas pārbaudiet mērķa attēlu atlasi:

* [ ] Vismaz 1 mērķa attēls atzīmēts katrā sesijā
* [ ] Mērķa kolonnas izvēles rūtiņas ir atzīmētas visiem mērķa attēliem
* [ ] Mērķa attēli uzņemti tajā pašā laika periodā, kad veikta apsekošana
* [ ] Mērķi ir skaidri redzami priekšskatījumā, uzklikšķinot uz tiem
* [ ] Visi 4 kalibrēšanas paneļi ir redzami katrā mērķa attēlā
* [ ] Uz mērķiem nav ēnu vai šķēršļu
* [ ] Divu kameru gadījumā: mērķi ir atzīmēti abu kameru tipiem

***

## Apstrāde bez mērķiem

### Apstrāde bez kalibrēšanas mērķiem

Lai gan tas nav ieteicams zinātniskam darbam, jūs varat veikt apstrādi bez mērķiem:

1. Atstājiet visas mērķu kolonnas izvēles rūtiņas neatzīmētas
2. **Atvienojiet** &quot;Reflektances kalibrēšana&quot; projekta iestatījumos
3. Vignette korekcija joprojām tiks piemērota
4. Izvade netiks kalibrēta absolūtajai atstarošanai

{% hint style=&quot;warning&quot; %}
**Nav ieteicams**: bez atstarošanas kalibrēšanas pikseļu vērtības attēlo tikai relatīvo spilgtumu, nevis zinātniskos atstarošanas mērījumus. Lai iegūtu precīzus, atkārtojamus rezultātus, izmantojiet kalibrēšanas mērķus.
{% endhint %}

***

## Nākamie soļi

Kad esat atzīmējis mērķa attēlus:

1. **Pārskatiet savus iestatījumus** - skatiet [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
2. **Sāciet apstrādi** - Skatīt [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet progresu** - Skatīt [Apstrādes uzraudzība](monitoring-the-processing.md)

Lai iegūtu vairāk informācijas par kalibrēšanas mērķiem, skatiet [Kalibrēšanas mērķi](../calibration-targets.md).
