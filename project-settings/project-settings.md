# Projekta iestatījumi

Chloros

programmas sānu joslā „Projekta iestatījumi“ <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

varat konfigurēt visus attēlu apstrādes aspektus, kalibrēšanas mērķa noteikšanu, multispektrālo indeksu aprēķinus un projekta eksportēšanas opcijas. Šie iestatījumi tiek saglabāti kopā ar projektu, un tos var saglabāt kā veidnes, lai tos atkārtoti izmantotu vairākos projektos.

## Piekļuve projekta iestatījumiem

Lai piekļūtu projekta iestatījumiem:

1. Atveriet projektu programmā Chloros


2. Noklikšķiniet uz cilnes **Projekta iestatījumi** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

kreisajā sānjoslā
3. Iestatījumu panelī tiks parādītas visas pieejamās konfigurācijas opcijas, kas sakārtotas pēc kategorijām

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->



{% hint style="info" %}
**Iestatījumi, kas ir atkarīgi no citiem iestatījumiem, ir izcelti pelēkā krāsā.** Ja augstāka līmeņa slēdzis padara kādu iestatījumu neiespējamu (piemēram, atceļot atzīmi no *Atstarošanas kalibrēšana / balansa iestatīšana*, kļūst neiespējama *Atstarošanas eksportēšana*), atkarīgais vadības elements tiek atspējots, un tā rīkjoslā tiek norādīts slēdzis, kas jāmaina.
{% endhint %}

***

## Ekrāns

### Attēla sīktēla izšķirtspēja

* **Tips**: Izvēlne
* **Opcijas**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Noklusējums**: Noklusējums (512 pikseļi)
* **Apraksts**: Izšķirtspēja (garākā mala, pikseļos), ar kādu tiek attēlotas attēlu režģa sīktēmas. Augstākas vērtības izskatās asākas, kad attēlu palielina, taču tās lādējas lēnāk un patērē vairāk atmiņas. Pilna izšķirtspēja atbilst sākotnējam attēla izmēram.
* **Piezīme**: Tikai attēlošanai — tas nekad neietekmē apstrādi vai eksportētos failus.***

## Mērķa atpazīšana

Šie iestatījumi nosaka, kā Chloros

atpazīst un apstrādā kalibrēšanas mērķus jūsu attēlos. Abi ir aktīvi tikai tad, ja ir ieslēgta **Atstarošanas kalibrēšana / balansa iestatīšana** (pretējā gadījumā tie ir izslēgti, jo mērķa atpazīšana tiek pilnībā izlaista).

### Minimālā kalibrēšanas parauga platība (px)

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 10 000 pikseļiem
* **Noklusējums**: 25 pikseļi
* **Apraksts**: Nosaka minimālo platību (pikseļos), kas nepieciešama, lai atklāto apgabalu uzskatītu par derīgu kalibrēšanas mērķa paraugu. Mazākas vērtības ļaus atklāt mazākus mērķus, taču var palielināt kļūdainu atklājumu skaitu. Lielākas vērtības prasa lielākus un skaidrākus mērķa apgabalus atklāšanai.
* **Kad pielāgot**:
  * Palieliniet, ja rodas kļūdaini atklājumi attiecībā uz nelieliem attēla artefaktiem
  * Samaziniet, ja jūsu kalibrēšanas mērķi attēlos izskatās mazi un netiek atklāti

### Minimālā mērķu grupēšana (0–100)

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 100
* **Noklusējums**: 60
* **Apraksts**: Kontrolē grupēšanas slieksni, lai kalibrēšanas mērķu noteikšanas laikā grupētu līdzīgas krāsas apgabalus. Augstākas vērtības prasa, lai kopā tiktu grupētas vairāk līdzīgas krāsas, kas rada konservatīvāku mērķu noteikšanu. Zemākas vērtības ļauj lielāku krāsu variāciju vienas mērķu grupas ietvaros.
* **Kad pielāgot**:
  * Palieliniet, ja kalibrēšanas mērķi tiek sadalīti vairākās atklāšanas reizēs
  * Samaziniet, ja kalibrēšanas mērķi ar krāsu variācijām netiek pilnībā atklāti

***

## Apstrāde

Šie iestatījumi nosaka, kā programma Chloros

apstrādā un kalibrē jūsu attēlus.

### Vignēšanas korekcija

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Piemēro vinjetes korekciju, lai kompensētu objektīva radīto attēla malu tumšošanos. Vinjetēšana ir izplatīta optiska parādība, kuras dēļ attēla stūri un malas izskatās tumšākas nekā centrs objektīva īpašību dēļ.
* **Blakus efekts**: Šis slēdzis arī nosaka, kuru *nekalibrētu rezerves produktu* programma izveido (skatīt zemāk).

### Atstarošanas kalibrēšana / baltā balanss

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Iespējo atstarošanas kalibrēšanu — izmantojot kadrā atklātos kalibrēšanas mērķus un/vai DAQ gaismas sensora lejupvērstos datus, atkarībā no kameras un pieejamajiem resursiem. Tas normalizē atstarošanas vērtības visā datu kopā un nodrošina konsekventus mērījumus neatkarīgi no apgaismojuma apstākļiem.
* **Ja atspējots**: Mērķu noteikšana tiek pilnībā izlaista, un**neviena kamera nevar izveidot atstarojuma produktu** — ne Survey3

mērķu vadītā, ne „LATTICE DAQ” vadītā. Atkarīgie iestatījumi (*Eksportēt atstarojumu*, *Minimālais atkārtotās kalibrēšanas intervāls* un mērķu noteikšanas sliekšņi) ir izslēgti.

### Nekalibrēti rezerves rezultāti: Eksportēt sensora reakciju / Eksportēt vinjetes korekciju

* **Tips**: Divas izvēles rūtiņas
* **Noklusējumi**: Abas ieslēgtas (atzīmētas)
* **Apraksts**: Ja kadru nav iespējams kalibrēt pēc atstarojuma (nav atrasts kalibrēšanas mērķis vai atstarojuma kalibrēšana ir izslēgta), tas tiek saglabāts kā *nekalibrēts rezerves produkts*. **Katram kameras modelim katrā darbības ciklā pastāv tieši viens no diviem rezerves produktiem**, ko izvēlas ar slēdzi *Vignette correction*:
  * Vignette korekcija **ieslēgta**→ `Vignette_Corrected_Images/` (regulē**Eksportēt ar vignette korekciju**)
  * Vignette korekcija **izslēgta**→ `Sensor_Response_Images/` (regulē**Eksportēt sensora reakciju**)
* Rezerves variants, kas netiek izmantots, ir izslēgts (izcelts pelēkā krāsā). Atceļot atzīmi no izmantotā varianta, šis fails vispār netiks saglabāts.

### LATTICE eksporta produkti

Projektiem, kuros ir iekļauti LATTICE uzņēmumi, katrs importētais LATTICE kadrs vienā apstrādes ciklā tiek sadalīts visos aktivizētajos **un piemērojamajos**produktos. Šo sadalīšanu kontrolē četri izvēles rūtiņas (visas pēc noklusējuma ir**ieslēgtas**):

| Iestatījums | Izvades mape | Ko eksportē |
| --- | --- | --- |
| **Eksportēt bez debayeringa** | `Debayered_Images/` | Lineārs attēls bez debayeringa: no kalibrēšanas paketes ir noņemts tumšais nobīde, pēc tam veikta malu ņemšana vērā demosaicēšana bez linearitātes korekcijas, līdzena lauka vai atdalīšanas — tas pats fails, ko uzņēmums saglabā bez bayera līmenī. Saglabāts sensora sākotnējā orientācijā (reģistrēts XMP). Attiecas uz RGB

un multispektrālajām kamerām. |
| **Eksportēt priekšskatījumu** | `Preview_Images/` | Ekrāna priekšskatījums. RGB

= baltā balanss (DAQ apgaismojums, ja pieejams, citādi pelēkā pasaule) + gamma; multispektrālais = viltus krāsu izstiepums. |
| **Eksportēt starojuma intensitāti** | `Radiance_Images/` | Float32 spektrālā starojuma intensitāte W/m²/sr/nm. Tikai multispektrālajām (M3C/M3M) — neattiecas uz RGB

oriģināliem. Vienmēr tiek saglabāts kā 32 bitu TIFF

, neatkarīgi no iestatījuma *Kalibrētais attēla formāts*. |
| **Eksportēt atstarojumu**| `Reflectance_Calibrated_Images/` | Uint16 atstarojums, mērogots tā, ka**32768 = atstarojums 1,0** (atzīmēts kā XMP `Chloros:PixelScale`). Tikai multispektrāliem attēliem, tiek ierakstīts, ja atbilstošs `.daq` lejupvērstais ieraksts (vai kvalitātes pārbaudi izturējis mērķis kadrā) aptver kadru. |

* RGB

galvenās kameras izstaro debayered + priekšskatījumu; starojums/atstarošanās tām tiek izlaisti, jo nav piemērojami.
* Debayered/priekšskatījuma bitu dziļums atbilst iestatījumam *Kalibrētais attēla formāts*; starojums vienmēr ir float32.
* Šie četri slēdži neietekmē Survey3

apstrādi.
* Multispektrālo M3C kameru starojuma un atstarošanas dati var arī ierakstīt **fiksēšanas masku** `clamp_masks/` mapē blakus produktu mapēm, reģistrējot vietas, kur joslas atdalīšanas procesā negatīva vērtība tika noregulēta līdz nullei. Skatīt [Izvades attēlu formāti](../output-image-formats.md#clamp-masks).

Tie paši četri slēdži pastāv arī bez galvenes kā `chloros-cli process --debayered / --preview / --radiance / --reflectance`. Tie aizstāja veco CLI

`--radiometric-output` karodziņu, kas vairs nepastāv.

{% hint style="warning" %}
**Ja tiek atspējoti visi piemērojamie produkti, apstrādes cikls beidzas ar kļūdu.** Sākot ar versiju 1.2.0, apstrādes cikls, kurā tika pieprasīti produkti, bet netika izveidots neviens attēla produkts, ziņo par kļūdu, un CLI

beidz darbu ar rezultātu, kas nav nulle, nevis klusi ziņo par veiksmīgu izpildi. Žurnālā tiek norādīts produkts, kuru neizdevās izveidot, un iemesls. Apzināti veikts cikls, kurā tiek ģenerēti tikai metadati (nekas netika pieprasīts) joprojām tiek uzskatīts par veiksmīgu.
{% endhint %}

### Reflektances avots (projekta iestatījums, kas tiek konfigurēts caur CLI

)

Projektā tiek saglabāts arī tas, kuru **reflektances atsauci** izmanto LATTICE reflektances produkts. Iestatījumu panelī nav atsevišķa vadības elementa; vērtība tiek saglabāta projekta konfigurācijā kā `Processing → "Target reflectance source"` un tiek iestatīta ar `chloros-cli process --reflectance-source {auto,target,daq}` (SDK

parametram `configure()` nav atbilstoša atslēgvārda):

* **`auto`** (noklusējums): QA pārbaudi izturējis kalibrēšanas mērķis kadrā kļūst par absolūto atsauci, bet, ja mērķa nav vai QA pārbaude nav izturēta, tiek izmantots DAQ lejupvērstās starojuma sadalījums (ρ = πL/E). Mērķis, kas iztur QA ar brīdinājuma karodziņu (nokritusi paneļa daļa vai nedarbojas kāda josla), netiek automātiski izvēlēts: tiek izmēģinātas mērķa, mērķa+DAQ un tikai DAQ ķēdes to novērtējuma secībā, kas iegūts uz atsevišķām paneļu daļām.
* **`target`**: stingri mērķa vadīta atstarojamība — bez DAQ aizstāšanas.
* **`daq`**: DAQ noteicošā atstarojamība; kadrā esošie mērķi netiek izmantoti kā etalons.

Saglabātā vērtība tiek salīdzināta, neņemot vērā lielos un mazos burtus, un daži rakstības varianti tiek pieņemti kā sinonīmi: `target`, `target_image`, `empirical` un `empirical_line` — visi nozīmē **mērķi**; `daq`, `dls`, `light_sensor` un `sensor` visi nozīmē**daq**. Jebkurš cits variants — ieskaitot trūkstošu atslēgu — tiek interpretēts kā**auto**.**Izmērītie** mērķa skenējumi pa vienībām tiek meklēti pēc mērķa vienības sērijas numura/QR koda, piemēram, `<serial>.csv`, trīs vietās: katalogā, kas norādīts ar `--target-reflectance-dir` (saglabāts kā `Processing → "Target reflectance dir"`), paša projekta `target_reflectance/` mapē, un ceļā, kas norādīts `CHLOROS_TARGET_REFLECTANCE_DIR` vides mainīgajā. Ja šai vienībai nav mērīta skenējuma, tā vietā tiek izmantota mērķa modeļa nominālā publicētā līkne.

### Debayer metode

* **Tips**: Izvēles saraksts
* **Opcijas**:
  * Standarta (ātra, vidēja kvalitāte)
  * Tekstūras ņemšana vērā (lēna, augstākā kvalitāte) \[Chloros

+]
* **Noklusējums**: Standarta (ātra, vidēja kvalitāte)
* **Apraksts**: Izvēlas demosaicinga algoritmu, ko izmanto, lai pārvērstu neapstrādātos Bayer modeļa sensora datus pilnkrāsu attēlos. Metode „Standarta (ātra, vidēja kvalitāte)” nodrošina optimālu līdzsvaru starp apstrādes ātrumu un attēla kvalitāti. Metode „Ar tekstūras ņemšanu vērā (lēna, visaugstākā kvalitāte)” \[Chloros

+] izmanto augstas kvalitātes, kontūru ņemošu demosaicinga algoritmu, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kurš novērš gandrīz visus demosaicinga radītos trokšņus. Lai darbinātu „Texture Aware” modeli, ir nepieciešama GPU atmiņa (VRAM). Lai nodrošinātu ātrāku apstrādi, iesakām to izmantot, ja jums ir pieejami &gt;4 GB VRAM.
* **Ja rinda vispār ir nolaižamais izvēlnes elements**: divu izvēļu nolaižamā izvēlne parādās tikai tad, ja**abas** ir patiesas — esat pieteicies ar atbilstošu „Chloros

+” abonementu, **un** projektā nav LATTICE uzņēmumu. Pretējā gadījumā rinda tiek attēlota kā parasts teksts „`Standard (Fast, Medium Quality)`”, kurā nav ko izvēlēties.
* **Piezīme par LATTICE**: Nav LATTICE apmācīta „Texture Aware” modeļa, un apstrādes ķēde LATTICE kadriem piespiež izmantot standarta demosaiku neatkarīgi no saglabātās vērtības. Ja pievienojat LATTICE mapi projektam, kurā jau bija atlasīts „Texture Aware”, Chloros

pārraksta iestatījumu atpakaļ uz „Standard”, nevis atstāj novecojušu vērtību `project.json`.

### Minimālais pārkalibrēšanas intervāls

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 3 600 sekundēm
* **Noklusējums**: 0 sekundes
* **Apraksts**: Nosaka minimālo laika intervālu (sekundēs) starp kalibrēšanas mērķu izmantošanu. Ja iestatīts uz 0, Chloros

izmantos katru atklāto kalibrēšanas mērķi. Ja iestatīts uz lielāku vērtību, Chloros

izmantos tikai tos kalibrēšanas mērķus, kuru starpība ir vismaz šis laiks sekundēs, tādējādi samazinot apstrādes laiku datu kopām, kurās bieži tiek uztverti kalibrēšanas mērķi.
* **Kad jāpielāgo**:
  * Iestatiet uz 0, lai nodrošinātu maksimālu kalibrēšanas precizitāti mainīgos apgaismojuma apstākļos
  * Palieliniet (piemēram, līdz 60–300 sekundēm), lai paātrinātu apstrādi, ja apgaismojums ir nemainīgs un jums ir bieži kalibrēšanas mērķu attēli

### Gaismas sensora laika zonas nobīde

* **Tips**: Skaitlis
* **Diapazons**: no -12 līdz +12 stundām
* **Noklusējums**: 0 stundas
* **Apraksts**: Norāda laika zonas nobīdi (stundās no UTC) gaismas sensora datu laika zīmogiem, ko izmanto, saskaņojot gaismas sensora žurnālus ar attēlu uzņemšanas laikiem. Jaunākie `.daq` ieraksti satur savu laika zonas izcelsmi, tādēļ šī iestatījuma galvenokārt ir nepieciešams vecākiem žurnāliem, kas reģistrēti pēc vietējā laika.

### Piemērot PPK korekcijas

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Atspējots (neatzīmēts)
* **Apraksts**: Ļauj izmantot pēcapstrādes kinemātiskās (PPK) korekcijas no MAPIR

DAQ ierakstītājiem, kuros ir GPS (GNSS). Ja šī funkcija ir ieslēgta, Chloros

izmantos visus .daq žurnāla failus, kas satur ekspozīcijas pinu datus jūsu projekta direktorijā, un piemēros precīzas ģeolokācijas korekcijas jūsu attēliem.
* **Prasība**: Jūsu projekta direktorijā jābūt .daq žurnāla failam ar ekspozīcijas pinu ierakstiem
* **Kad ieslēgt**: Ieteicams vienmēr ieslēgt PPK korekciju, ja jūsu .daq žurnāla failā ir ekspozīcijas atgriezeniskās saites ieraksti.

### Ekspozīcijas kontakts 1

* **Tips**: Izvēlne
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta opcija „Piemērot PPK korekcijas” UN ekspozīcijas dati ir pieejami 1. kontaktam
* **Opcijas**:
  * Projektā atpazītie kameru modeļu nosaukumi
  * „Nelietot” — ignorēt šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: Piešķir konkrētu kameru ekspozīcijas kontaktam Nr. 1 PPK laika sinhronizācijai. Ekspozīcijas kontakts reģistrē precīzu brīdi, kad tiek iedarbināts kameras aizslēgs, kas ir ļoti svarīgi precīzai PPK ģeolokācijai.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: automātiski izvēlas kameru
  * Viena kamera + divi kontakti: 1. kontakts automātiski tiek piešķirts kamerai
  * Vairākas kameras: nepieciešama manuāla izvēle

### Ekspozīcijas kontakts 2

* **Tips**: Izvēle no nolaižamā saraksta
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta opcija „Piemērot PPK korekcijas” UN ja ekspozīcijas dati ir pieejami 2. kontaktam
* **Opcijas**:
  * Projektā atpazītie kameru modeļu nosaukumi
  * „Nelietot” — ignorē šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts atbilstoši projekta konfigurācijai
* **Apraksts**: Pieskaita konkrētu kameru ekspozīcijas kontaktam Nr. 2, lai nodrošinātu PPK laika sinhronizāciju, izmantojot divu kameru konfigurāciju.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: 2. kontakts automātiski tiek iestatīts uz „Nelietot”
  * Viena kamera + divi kontakti: 2. kontakts automātiski tiek iestatīts uz „Nelietot”
  * Vairākas kameras: nepieciešama manuāla izvēle
* **Piezīme**: Vienu un to pašu kameru nevar vienlaikus piešķirt gan 1. kontaktam, gan 2. kontaktam.***

## DAQ gaismas sensors

Šī sadaļa parādās projekta iestatījumos un uzskaita visus projektā esošos DAQ lejupvērstās gaismas failus — `.daq` ierakstus un DAQ-M `.csv` lejupvērstās gaismas žurnāli. Ieraksti, kas veikti cilnē „Gaismas sensori”, tiek automātiski pievienoti atvērtam projektam.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->



Katrā rindā ir norādīts fails, sensora modelis un difuzora vāciņa korekcija, kas faktiski tiek piemērota šim failam. Virs rindām atrodas viens projekta līmeņa vadības elements:

### Vāciņas pārrakstīšana (visi faili)

* **Tips**: izvēlne
* **Opcijas**: `Auto`, kā arī difuzora vāciņa korekcijas profili, kas ir spēkā projekta sensoru tipiem
* **Noklusējums**: Automātiski
* **Saglabāts kā**: `Processing → "DAQ cap id"` (noklusējuma vērtība `auto`)
* **Apraksts**: `Auto` izmanto katra faila reģistrēto vāciņu (ja nekas nav reģistrēts, tiek pieņemts, ka izmantots „Sunshine” vāciņš, taču DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez vāciņa, un Chloros

nevar redzēt, kas ir uzstādīts, tāpēc apstrādes laikā tiek parādīts brīdinājums par pieņemto ierobežotāju: izvēlieties to ierobežotāju, kas bija uzstādīts). Konkrēta ierobežotāja izvēle pārraksta **visus** projekta lejupvērstos failus, izņemot DAQ-E-S failus, kuru difuzors ir daļa no kalibrācijas; projektā, kurā ir tikai DAQ-E-S, izvēlnes nav: neapstrādātie ieraksti tiek koriģēti ar to, un ierakstiem, kuriem jau ir piemērots vāciņš, tiek pārrēķināti atsauces punkti (ierakstītā korekcija tiek atcelta un tiek piemērota izvēlētā).
* **Svarīgi**: Izvēlētajam vāciņam jāsakrīt ar to vāciņu, kas fiziski bija uzstādīts ierakstīšanas laikā. Ne sensors, ne programmatūra nevar noteikt fizisko vāciņu — neatbilstošs vāciņa ID spektrus koriģē nepareizi.

Tīši ir paredzēts **viens** visam projektam kopīgs iestatījums, nevis izvēlnes katram failam atsevišķi: šis iestatījums attiecas uz visiem lejupvērstajiem avotiem projektā.***

## Masīva saskaņošana

Šī sadaļa parādās **tikai** tad, ja vismaz vienam attēlam projektā ir moduļu savstarpējās saskaņošanas transformācija, ko LATTICE masīvi pievieno uzņemšanas brīdī (XMP `Chloros:Alignment*` birkas). Tajā tiek parādīts, cik daudziem attēliem ir izlīdzināšanas tagi, kura kamera ir atsauces kamera (`REF` marķējums), kā arī tabula ar attēlu skaitu katrai kamerai.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Pielietot masīva izlīdzināšanu

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Saglabāts kā**: `Processing → "Array alignment"`
* **Apraksts**: Izlīdzina katru apstrādāto rezultātu (debayering / priekšskatījums / starojums / atstarošanās / indekss) atbilstoši masīva kopīgajai atsauces ģeometrijai, izmantojot uzņemšanas brīdī fiksēto transformāciju. Izslēgts = eksportē sensora oriģinālajā ģeometrijā.

### Apgriezt līdz kopīgajai pārklāšanās zonai

* **Tips**: Izvēles rūtiņa (aktīva tikai tad, ja ir ieslēgta opcija *Piemērot masīva izlīdzināšanu*)
* **Noklusējums**: Ieslēgts (atzīmēts)
* **Saglabāts kā**: `Processing → "Array alignment crop"`
* **Apraksts**: Apgriež izlīdzinātos eksportus līdz reģionam, ko kopīgi izmanto visi kameras moduļi, tādējādi katrai joslai ir vienāds laukums. Izslēgts saglabā pilnu sensora laukumu (melna aizpildīšana ārpus avota).

### Pārparaugošana

* **Tips**: Izvēles saraksts (aktīvs tikai tad, ja ir ieslēgta opcija *Piemērot masīva izlīdzināšanu*)
* **Opcijas**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Noklusējums**: Bilineārs
* **Saglabāts kā**: `Processing → "Array alignment interpolation"`
* **Apraksts**: Interpolācija, ko izmanto izlīdzināšanas deformācijā. „Tuvākais” saglabā precīzas avota vērtības (bez pikseļu sajaukšanas) stingrai radiometriskai analīzei; „Bilineārs” ir vislabākais kartēšanai un vizuālai izmantošanai.

Tās pašas trīs opcijas ir pieejamas arī bez galotnes kā `chloros-cli process --array-alignment`, `--array-alignment-crop` un `--array-alignment-interp {bilinear,nearest,cubic}`.

***

## Indekss

Šie iestatījumi ļauj konfigurēt multispektrālos indeksus analīzei un vizualizācijai.

### Pievienot indeksu

* **Tips**: Speciāls indeksu konfigurācijas panelis
* **Apraksts**: Atver interaktīvu paneli, kurā varat izvēlēties un konfigurēt multispektrālos veģetācijas indeksus (NDVI

, NDRE

, EVI

utt.), kurus aprēķināt attēla apstrādes laikā. Var pievienot vairākus indeksus, katram ar saviem vizualizācijas iestatījumiem.
* **Pieejamie indeksi**: GUI nolaižamajā izvēlnē ir iekļautas**27** iepriekš definētas multispektrālo indeksu formulas (skatiet [Multispektrālo indeksu formulas](multispectral-index-formulas.md), lai redzētu pilnu sarakstu, tostarp nosaukumus, kurus atbalsta arī CLI

/SDK

`--indices` opcijā).
* **Funkcijas**:
  * Izvēlieties no iepriekš definētām indeksu formulām
  * Velciet kameras filtru kanālus uz formulas joslu vietām
  * Konfigurējiet vizualizācijas krāsu gradientus (LUT — Look-Up Tables)
  * Iestatiet sliekšņa vērtības un apgriešanas režīmus
  * Izveidojiet pielāgotas indeksa formulas
* **Piezīme**: Indeksi netiek aprēķināti vienjoslas LATTICE M3M mono kamerām — daudzjoslu indeksi vienā joslā nav definēti. Tas neattiecas uz Survey3

un LATTICE M3C.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->



Katrs pievienotais indekss attēlo savu formulu kā matemātisku izteiksmi, ar krāsainu apli katram joslas laukumam: sarkans = Red

, zaļš = Green

, zils = Blue

, oranžs = Orange

, ciāns = Cyan

, violets = NIR

, magenta = RE. Velciet apli no rindas virs formulas uz slota, lai to piesaistītu; dubultklikšķiniet uz piesaistītā slota, lai to dzēstu. Indekss tiek aprēķināts tikai tad, ja katrā slotā, ko izmanto formula, ir kanāls.

### Pielāgotas formulas (funkcija Chloros

+)

* **Tips**: Pielāgoto formulu definīciju masīvs
* **Pieejamība**: Nepieciešama pieteikšanās ar atbilstošu „Chloros

+” abonementu.
* **Apraksts**: Ļauj izveidot un saglabāt pielāgotas multispektrālo indeksu formulas, izmantojot joslu matemātiku. Pielāgotās formulas tiek saglabātas kopā ar jūsu projekta iestatījumiem un tās var izmantot tāpat kā iebūvētos indeksus.
* **Kā izveidot**:
  1. Indeksa konfigurācijas panelī atveriet pielāgoto formulu kalkulatoru
  2. Uzrakstiet formulu, izmantojot **joslu-slota simbolus**, nevis joslu nosaukumus
  3. Saglabājiet formulu ar aprakstošu nosaukumu — tā parādīsies formulu nolaižamā izvēlnē apakšā, un jūs varēsiet pārvilkt savas kameras kanālu apļus uz tās vietām tieši tāpat kā iebūvētajā iestatījumā
* **Formulas sintakse**:
  * Spektrālo joslu vietas: `x`, `y`, `z`, `a`, `b`, `c` — sešas pozīcijas, kuras varat piesaistīt reāliem kanāliem, velkot ar peles kursoru
  * Operatori: `+`, `-`, `*`, `/`, `^` un `()` grupēšanai
  * Funkcijas: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Kāpēc simboli, nevis grupu nosaukumi**: formula, kas uzrakstīta kā `(y-x)/(y+x)`, darbojas ar jebkuru kameru, jo „vilk-un-met” (drag-and-drop) saistīšana nosaka, vai `y` ir NIR

850 nm no RGN

filtra vai NIR

808 nm no OCN

filtra. Iebūvētie iestatījumi tiek saglabāti tādā pašā veidā — skatiet [Multispektrālo indeksu formulas](multispectral-index-formulas.md), lai uzzinātu precīzo simbolu formu visiem 27 indeksiem.
* **Kur tās darbojas**: pielāgotās formulas tiek saglabātas kopā ar projekta iestatījumiem un tās var izmantot gan [indeksu/LUT izmēģinājumu vidē](../image-viewer-gui/index-lut-sandbox.md), gan apstrādē. Tās**netiek** atbalstītas CLI

/SDK

`--indices` nosaukumu sarakstā, kurā tiek izvērsti tikai 22 iebūvēto iestatījumu nosaukumi.

***

## Eksportēšana

Šie iestatījumi nosaka eksportēto apstrādāto attēlu formātu un kvalitāti.

### Kalibrēta attēla formāts

* **Tips**: Izvēlne
* **Opcijas**:
  * **TIFF

(16 bitu)** — nesaspiests 16 bitu TIFF

formāts
  * **TIFF

(8 bitu)** — nesaspiests 8 bitu TIFF

formāts
  * **TIFF

(32 bitu, procentos)** — 32 bitu peldošā punkta TIFF

ar atstarojuma vērtībām procentos
  * **PNG

(8 bitu)** — saspiests 8 bitu PNG

formāts
  * **JPG (8 bitu)** — saspiests 8 bitu JPEG

formāts
* **Noklusējums**: TIFF

(16 bitu); projektā, kurā visi attēli ir 8 bitu formātā, tiek parādīts TIFF

(8 bitu) un piedāvāti tikai 8 bitu formāti
* **Apraksts**: Izvēlas failu formātu apstrādāto un kalibrēto attēlu saglabāšanai. Eksportētie attēli tiek saglabāti atsevišķās apakšmapēs katrā kameras mapē atbilstoši formātam (`tiff16`, `tiff32`, `png8`, `jpg8`), ar vienu `<Product>_Images/` mapi katram produktam. Eksportētajiem failiem tiek saglabāts avota faila nosaukums — produktu identificē mape, nevis faila nosaukuma paplašinājums.
* **Formātu ieteikumi**:
  * **TIFF

(16 bitu)**: Ieteicams zinātniskai analīzei un profesionālām darba plūsmām. Saglabā maksimālu datu kvalitāti bez kompresijas artefaktiem. Vispiemērotākais multispektrālajai analīzei un turpmākai apstrādei ĢIS programmās.
  * **TIFF

(32 bitu, procentos)**: Vispiemērotākais darba plūsmām, kurās nepieciešamas atstarojuma vērtības procentos (0–100 %). Nodrošina maksimālu precizitāti radiometriskajiem mērījumiem.
  * **PNG

(8 bitu)**: Piemērots skatīšanai tīmeklī un vispārējai vizualizācijai. Mazāki failu izmēri ar bezzaudējumu saspiešanu, taču samazināts dinamiskais diapazons.
  * **JPG (8 bitu)**: Vismazākie failu izmēri, piemērots tikai priekšskatīšanai un attēlošanai tīmeklī. Izmanto kompresiju ar zudumiem, kas nav piemērota zinātniskai analīzei.
* **Piezīme**: LATTICE starojuma dati vienmēr tiek eksportēti kā 32 bitu peldošā punkta TIFF

formātā neatkarīgi no šī iestatījuma.

***

## Projekta veidnes saglabāšana

Šī funkcija ļauj saglabāt pašreizējos projekta iestatījumus kā atkārtoti izmantojamu veidni.

* **Tips**: Teksta ievade + Saglabāt poga
* **Apraksts**: Ievadiet aprakstošu nosaukumu savai iestatījumu veidnei un noklikšķiniet uz saglabāšanas ikonas. Veidnē tiks saglabāti visi jūsu pašreizējie projekta iestatījumi (mērķa atpazīšana, apstrādes opcijas, indeksi, un eksporta formāts), lai tos varētu viegli atkārtoti izmantot nākotnes projektos. Veidnes tiek saglabātas mapē „`Project Templates/`”, kas atrodas jūsu projekta saglabāšanas mapē, un tās var arī izvēlēties vai eksportēt no galvenās izvēlnes (*Izvēlēties veidni* / *Saglabāt veidni* / *Eksportēt veidni*).
* **Lietošanas piemēri**:
  * Izveidojiet veidnes dažādām kameru sistēmām (RGB

, multispektrālās, NIR

)
  * Saglabājiet standarta konfigurācijas konkrētiem kultūraugu veidiem vai analīzes darba plūsmām
  * Dalieties ar vienotiem iestatījumiem visai komandai
* **Kā lietot**:
  1. Konfigurējiet visus vēlamos projekta iestatījumus
  2. Ievadiet veidnes nosaukumu (piem., „RedEdge

Survey3

NDVI

Standard”)
  3. Noklikšķiniet uz saglabāšanas ikonas
  4. Tagad veidni var ielādēt, veidojot jaunus projektus

***

## Projekta saglabāšanas mape

Šis iestatījums nosaka, kur pēc noklusējuma tiek saglabāti jauni projekti.

* **Tips**: Kataloga ceļa parādīšana + pogu „Rediģēt”
* **Noklusējums (Windows

)**: `Documents\MAPIR\Chloros Projects` jūsu mapē „Dokumenti” — parasti `C:\Users\[Username]\Documents\MAPIR\Chloros Projects` vai tur, kur OneDrive vai pārvietojamais profils ir pārvietojis mapes „Dokumenti”
* **Noklusējums (Linux

)**: `~/Documents/MAPIR/Chloros Projects`
* **Apraksts**: Parāda pašreizējo noklusējuma direktoriju, kurā tiek izveidoti jauni Chloros

projekti. Noklikšķiniet uz rediģēšanas ikonas, lai izvēlētos citu direktoriju. Pārrakstītais iestatījums tiek saglabāts kā viena teksta rinda failā `~/.chloros/working_directory.txt` — Windows

sistēmā tas ir `C:\Users\<Username>\.chloros\working_directory.txt`. Ja šis fails trūkst vai tajā norādīts ceļš, kas vairs nepastāv, Chloros

izmanto iepriekš minēto noklusējumu. CLI

lasa un raksta to pašu failu, tādēļ `chloros-cli` un GUI vienmēr saskan par to, kur atrodas projekti.
* **Projekta veidnes** atrodas šī kataloga apakšmapē `Project Templates/`.
* **Kad mainīt**:
  * Norādiet tīkla disku, lai veicinātu komandas sadarbību
  * Mainiet uz disku ar lielāku uzglabāšanas vietu lieliem datu kopumiem
  * Sakārtot projektus pēc gada, klienta vai projekta veida atsevišķās mapēs
* **Piezīme**: Šī iestatījuma maiņa ietekmē tikai JAUNUS projektus. Esošie projekti paliek savās sākotnējās atrašanās vietās.
* **Windows

ceļa garums**: ja vien nav ieslēgti Windows

garie ceļi (instalētāja opcija **Iespējot Windows

garos ceļus**), projekta mape, kuras nosaukums ir garāks par aptuveni 130 rakstzīmēm, tiek noraidīta pirms apstrādes, jo tās rezultāti pārsniegtu Windows

260 rakstzīmju ceļa garuma ierobežojumu. Skatīt [Apstrādes uzsākšana](../processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

***

## Iestatījumu saglabāšana

Chloros

projekts ir **mape**. Visi projekta iestatījumi tiek saglabāti tajā esošajā `project.json`; savienotā aparatūra tiek saglabāta kopā ar tiem mapēs `cameras.json` un `sensors.json`, tādējādi, atverot projektu no jauna, tiek atjaunota arī kameru un gaismas sensoru savienojums. Kad atverat projektu no jauna, visi iestatījumi tiek atjaunoti tieši tā, kā tos atstājāt. Saglabātos projektus var vadīt arī bez grafiskās saskarnes, izmantojot failu „`chloros-cli project`” vai SDK

failu „`open_project`”.

### Iestatījumu hierarhija

Iestatījumi tiek piemēroti šādā secībā:

1. **Sistēmas noklusējumi** — iebūvēti noklusējumi, ko definējis Chloros

2. **Veidnes iestatījumi** — ja projekta izveides laikā tiek ielādēta veidne
3. **Saglabāto projektu iestatījumi** — iestatījumi, kas saglabāti kopā ar projekta failu
4. **Manuālie pielāgojumi** — jebkuras izmaiņas, ko veicat pašreizējās sesijas laikā

### Iestatījumi un attēlu apstrāde

Apstrādes iestatījumi tiek nolasīti, sākot apstrādes ciklu. Iestatījuma maiņa neietekmē jau diskā esošos rezultātus — lai piemērotu jaunās iestatījumu vērtības, apstrāde jāveic no jauna. Daži iestatījumi nekad neietekmē apstrādi:

* Attēla sīktēla izšķirtspēja (tikai parādīšanai)
* Saglabāt projekta veidni
* Saglabāt projekta mapi

***

## Konfigurācijas atslēgu atsauces

Automatizācijai (CLI

: `--config`, SDK

: `configure` vai tiešai `project.json` nolasīšanai) šie ir precīzie atslēgas vārdi `Project Settings`:

| Atslēgas ceļš | Tips | Noklusējums |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | skaitlis no 0 līdz 10000 | `25` |
| `Target Detection → Minimum Target Clustering (0-100)` | skaitlis 0–100 | `60` |
| `Processing → Vignette correction` | bool | `true` |
| `Processing → Reflectance calibration / white balance` | bool | `true` |
| `Processing → Export sensor response` | bool | `true` |
| `Processing → Export vignette corrected` | bool | `true` |
| `Processing → Export debayered` | bool | `true` |
| `Processing → Export preview` | bool | `true` |
| `Processing → Export radiance` | bool | `true` |
| `Processing → Export reflectance` | bool | `true` |
| `Processing → Array alignment` | bool | `true` |
| `Processing → Array alignment crop` | bool | `true` |
| `Processing → Array alignment interpolation` | `"Bilinear" \| "Nearest" \| "Cubic"` | `"Bilinear"` |
| `Processing → Debayer method` | `"Standard (Fast, Medium Quality)" \| "Texture Aware (Slow, Highest Quality)"` | Standarts |
| `Processing → Minimum recalibration interval` | skaitlis 0-3600 | `0` |
| `Processing → Light sensor timezone offset` | skaitlis -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | ierobežojuma profila identifikators vai `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | indeksu konfigurāciju saraksts | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (8-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

`Array alignment` atslēgas tiek ierakstītas, pirmoreiz renderējot sadaļu „Masīva izlīdzināšana” vai kad tās iestata automatizācijas izsaukums. Kamēr tās nav pieejamas, apstrādes ķēde izmanto tās pašas vērtības, kas parādītas iepriekš (`true`, `true`, bilineārs), tādējādi projekts bez šīm atslēgām darbojas identiski projektam, kurā tās ir iekļautas.

### Atslēgas, kas glabājas `project.json` un kurām nav vadības iestatījumu panelī

Tie atrodas tajā pašā `Project Settings` kokā un tiek nolasīti apstrādes laikā, taču sānu joslā jūs neatradīsiet tiem paredzētu logrīku:

| Atslēgas ceļš | Tips | Noklusējums | Iestatīts ar |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK

`input_level=`. Pārraksta to, kā tiek interpretēti LATTICE ievades TIFF

faili; `auto` to nosaka, pamatojoties uz katra faila `Chloros:ProcessingLevel` XMP tagu un kanālu skaitu. Netiek ņemts vērā attiecībā uz Survey3

`.raw` uzņēmumiem. Apzināti nav GUI iestatījums — automātiskais režīms ir pareizs visos parastos gadījumos. |
| `Processing → Target reflectance dir` | ceļa virkne | `""` | `chloros-cli process --target-reflectance-dir` vai projekta mērķa API

|
| `Processing → Target reflectance config` | vārdnīca, kuras atslēga ir kameras sērijas numurs | `{}` | Mērķa reģistrēšana kadrā (režīms `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | ceļa virkne | `""` | SDK

`process_folder(daq_log_path=…)`. Norāda uz `.daq` ierakstu vai to mapi |
| `Target Detection → Minimum calibration target squares` | skaitlis | `4` | Vecā standarta vērtība; bez vadības un bez CLI

. rādītāja |
| `UI → Grid thumbnail size` | skaitlis | `160` | Attēlu režģa paša sīktēlu palielināšanas sliders |

Divi skatītāja iestatījumi tiek saglabāti **augstākajā līmenī `project.json`**, pilnīgi ārpus `Project Settings`, jo tie ir attēlošanas stāvoklis, nevis apstrādes iestatījumi:

| Atslēgas ceļš | Tips | Noklusējums | Iestatīts ar |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | vesels skaitlis 1–256 | `1` | Attēla cilnes GSD (px) vadības elements — skatiet [Attēla atvēršana pilnekrāna režīmā](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## Labākā prakse

1. **Sāciet ar noklusējuma iestatījumiem**: Noklusējuma iestatījumi labi darbojas lielākajai daļai MAPIR

kameru sistēmu un tipiskajām darba plūsmām.
2. **Izveidojiet veidnes**: Kad esat optimizējuši iestatījumus konkrētai darba plūsmai vai kamerai, saglabājiet tos kā veidni, lai nodrošinātu konsekvenci visos projektos.
3. **Pārbaudiet pirms pilnīgas apstrādes**: Eksperimentējot ar jauniem iestatījumiem, pārbaudiet tos uz nelielu attēlu apakškopu, pirms apstrādājat visu datu kopu.
4. **Dokumentējiet savus iestatījumus**: izmantojiet aprakstošus veidņu nosaukumus, kas norāda uz kameru sistēmu, apstrādes veidu un paredzēto lietojumu (piemēram, „Survey3

\_RGB\_NDVI\_Agriculture”).
5. **Eksporta formāta izvēle**: izvēlieties eksporta formātu atbilstoši galīgajam lietojumam:
   * Zinātniskā analīze → TIFF (16 bitu vai 32 bitu)
   * ĢIS apstrāde → TIFF

(16 bitu)
   * Ātra vizualizācija → PNG

(8 bitu)
   * Dalīšanās tīmeklī → JPG (8 bitu)

***

Lai iegūtu vairāk informācijas par multispektrālajiem indeksiem programmā Chloros

, skatiet lapu [Multispektrālo indeksu formulas](multispectral-index-formulas.md).
