# Projekta iestatījumi

Chloros sānjoslā „Projekta iestatījumi“ (<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

) var konfigurēt visus attēlu apstrādes aspektus, kalibrēšanas mērķa noteikšanu, multispektrālo indeksu aprēķinus un projekta eksportēšanas opcijas. Šie iestatījumi tiek saglabāti kopā ar projektu un tos var saglabāt kā veidnes, lai tos atkārtoti izmantotu vairākos projektos.

## Piekļuve projekta iestatījumiem

Lai piekļūtu projekta iestatījumiem:

1. Atveriet projektu programmā Chloros
2. Noklikšķiniet uz cilnes **Projekta iestatījumi**<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

kreisajā sānjoslā
3. Iestatījumu panelī tiks parādītas visas pieejamās konfigurācijas opcijas, sakārtotas pēc kategorijām

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->

{% hint style="info" %}
**Iestatījumi, kas ir atkarīgi no citiem iestatījumiem, ir izslēgti.** Ja kāds augstāka līmeņa slēdzis neļauj veikt konkrētu iestatījumu (piemēram, atceļot atzīmi no *Atstarošanas kalibrēšana / baltā līdzsvara* neļauj veikt *Atstarošanas eksportēšanu*), atkarīgais vadības elements tiek atspējots un tā rīkjoslā tiek norādīts slēdzis, kas jāmaina.
{% endhint %}

***

## Ekrāns

### Attēla sīktēla izšķirtspēja

* **Tips**: Izvēlne
* **Opcijas**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Noklusējums**: Noklusējums (512 pikseļi)
* **Apraksts**: Izšķirtspēja (garākā mala, pikseļos), ar kādu tiek attēlotas attēlu režģa sīktēli. Augstākas vērtības izskatās asākas, kad tiek palielinātas, taču tās lādējas lēnāk un patērē vairāk atmiņas. Pilna izšķirtspēja atbilst oriģinālā attēla izmēram.
* **Piezīme**: Tikai parādīšanai — tas nekad neietekmē apstrādi vai eksportētos failus.***

## Mērķa atpazīšana

Šie iestatījumi nosaka, kā Chloros atpazīst un apstrādā kalibrēšanas mērķus jūsu attēlos. Abi iestatījumi darbojas tikai tad, ja ir ieslēgta **Reflektances kalibrēšana / balansa iestatīšana** (pretējā gadījumā tie ir nedarbīgi, jo mērķu noteikšana tiek pilnībā izlaista).

### Minimālā kalibrēšanas parauga platība (px)

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 10 000 pikseļiem
* **Noklusējums**: 25 pikseļi
* **Apraksts**: Nosaka minimālo platību (pikseļos), kas nepieciešama, lai atklāto apgabalu uzskatītu par derīgu kalibrēšanas mērķa paraugu. Mazākas vērtības ļaus atklāt mazākus mērķus, taču var palielināt kļūdainu atklājumu skaitu. Lielākas vērtības prasa lielākus un skaidrākus mērķa reģionus atklāšanai.
* **Kad pielāgot**:
  * Palieliniet, ja rodas kļūdaini atklājumi attēlos esošos mazos artefaktos
  * Samaziniet, ja jūsu kalibrēšanas mērķi attēlos izskatās mazi un netiek atklāti

### Minimālā mērķu grupēšana (0–100)

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 100
* **Noklusējums**: 60
* **Apraksts**: Kontrolē grupēšanas slieksni, lai kalibrēšanas mērķu atklāšanas laikā grupētu līdzīgas krāsas apgabalus. Augstākas vērtības prasa, lai kopā tiktu grupētas vairāk līdzīgas krāsas, kā rezultātā mērķu atklāšana kļūst konservatīvāka. Zemākas vērtības ļauj mērķu grupā būt lielākām krāsu variācijām.
* **Kad pielāgot**:
  * Palieliniet, ja kalibrēšanas mērķi tiek sadalīti vairākās atklāšanās
  * Samaziniet, ja kalibrēšanas mērķi ar krāsu variācijām netiek pilnībā atklāti

***

## Apstrāde

Šie iestatījumi kontrolē, kā Chloros apstrādā un kalibrē jūsu attēlus.

### Vignēšanas korekcija

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Piemēro vignēšanas korekciju, lai kompensētu objektīva radīto attēla malu tumšošanos. Vignēšana ir izplatīta optiska parādība, kuras dēļ attēla stūri un malas izskatās tumšākas nekā centrs objektīva īpašību dēļ.
* **Blakus efekts**: Šis slēdzis arī izvēlas, kuru *nekalibrētu rezerves produktu* programma ieraksta (skatīt zemāk).

### Atstarošanas kalibrēšana / baltā balanss

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Apraksts**: Iespējo atstarošanas kalibrēšanu — izmantojot kadrā atklātos kalibrēšanas mērķus un/vai DAQ gaismas sensora lejupvērstos datus, atkarībā no kameras un pieejamajiem resursiem. Tas normalizē atstarojuma vērtības visā datu kopā un nodrošina konsekventus mērījumus neatkarīgi no apgaismojuma apstākļiem.
* **Ja atspējots**: Mērķu atklāšana tiek pilnībā izlaista, un**neviena kamera nevar ģenerēt atstarojuma rezultātu** — gan Survey3 mērķu vadītajā, gan LATTICE DAQ vadītajā režīmā. Atkarīgie iestatījumi (*Eksportēt atstarojumu*, *Minimālais atkārtotās kalibrēšanas intervāls* un mērķu atpazīšanas sliekšņi) ir izslēgti.

### Nekalibrēti rezerves rezultāti: Eksportēt sensora reakciju / Eksportēt vinjetes korekciju

* **Tips**: Divas izvēles rūtiņas
* **Noklusējumi**: Abas ieslēgtas (atzīmētas)
* **Apraksts**: Ja kadru nav iespējams kalibrēt pēc atstarojuma (nav atrasts kalibrēšanas mērķis vai atstarojuma kalibrēšana ir izslēgta), tas tiek saglabāts kā *nekalibrēts rezerves produkts*. **Katram kameras modelim katrā uzņemšanas ciklā pastāv tieši viens no diviem rezerves produktiem**, ko izvēlas ar slēdzi *Vignettinga korekcija*:
  * Vignette korekcija **ieslēgta**→ `Vignette_Corrected_Images/` (regulē**Eksportēt ar vignette korekciju**)
  * Vignette korekcija **izslēgta**→ `Sensor_Response_Images/` (regulē**Eksportēt sensora reakciju**)
* Rezerves produkts, kas netiek izmantots, ir izslēgts (izcelts pelēkā krāsā). Atceļot atzīmi no izmantotā produkta, šis fails vispār netiek saglabāts.

### LATTICE eksporta produkti

Projektiem, kuros ir LATTICE uzņēmumi, katrs importētais LATTICE kadrs vienā apstrādes ciklā tiek sadalīts pa visiem aktivizētajiem **un piemērojamajiem**produktiem. Šo sadalīšanu kontrolē četri izvēles rūtiņas (visas pēc noklusējuma ir**ieslēgtas**):

| Iestatījums | Izvades mape | Ko eksportē |
| --- | --- | --- |
| **Eksportēt bez bayera** | `Debayered_Images/` | Lineārs attēls bez bayera. Attiecas uz RGB un multispektrālajām kamerām. |
| **Eksportēt priekšskatījumu** | `Preview_Images/` | Ekrāna priekšskatījums. RGB = baltā līdzsvara korekcija (DAQ apgaismojums, ja pieejams, citādi pelēkā skala) + gamma; multispektrālais = viltus krāsu izstiepums. |
| **Eksportējamais starojums** | `Radiance_Images/` | Float32 spektrālais starojums vienībās W/m²/sr/nm. Tikai multispektrālajām (M3C/M3M) — neattiecas uz RGB paraugiem. Vienmēr tiek rakstīts kā 32 bitu TIFF neatkarīgi no iestatījuma *Kalibrēts attēla formāts*. |
| **Eksportējamā atstarojamība**| `Reflectance_Calibrated_Images/` | Uint16 atstarojamība, mērogota tā, ka**32768 = atstarojamība 1,0** (atzīmēts kā XMP `Chloros:PixelScale`). Tikai multispektrāliem attēliem; tiek ierakstīts, ja atbilstošs `.daq` lejupvērstais ieraksts (vai kvalitātes pārbaudi izturējis mērķis kadrā) aptver kadru. |

* RGB galvenās kameras izstaro debayered + priekšskatījumu; starojums/atstarošanās tām tiek izlaisti, jo nav piemērojami.
* Debayered/priekšskatījuma bitu dziļums atbilst iestatījumam *Kalibrēts attēla formāts*; starojums vienmēr ir float32.
* Šie četri slēdži neietekmē Survey3 apstrādi.

Tie paši četri slēdži pastāv arī bez galvas kā `chloros-cli process --debayered / --preview / --radiance / --reflectance` un kā SDK atbilstošie parametri. Tie aizstāja veco `--radiometric-output` karodziņu, kas vairs nepastāv.

{% hint style="warning" %}
**Ja tiek atspējoti visi piemērojamie produkti, apstrāde beidzas ar kļūdu.** Sākot ar versiju 1.2.0, apstrādes cikls, kurā tika pieprasīti produkti, bet netika uzrakstīti attēlu produkti, ziņo par kļūdu, un CLI iziet ar rezultātu, kas nav nulle, nevis ziņo par klusu veiksmīgu izpildi. Žurnālā ir norādīts produkts, kuru neizdevās saglabāt, un iemesls. Apzināti tikai ar metadatiem veikts apstrādes cikls (nekas netika pieprasīts) joprojām tiek uzskatīts par veiksmīgu.
{% endhint %}

### Reflektances avots (projekta iestatījums, iestatāms ar CLI/SDK)

Projektā tiek saglabāts arī tas, kādu **reflektances atsauci** izmanto LATTICE reflektances produkts. Iestatījumu panelī nav atsevišķa vadības elementa; vērtība tiek saglabāta projekta konfigurācijā kā `Processing → "Target reflectance source"` un tiek iestatīta ar `chloros-cli process --reflectance-source {auto,target,daq}` vai SDK parametru `reflectance_source`:

* **`auto`** (noklusējums): kvalitātes pārbaudi (QA) izturējis kalibrēšanas mērķis kadrā kļūst par absolūto atsauci, bet, ja mērķa nav vai QA neizdodas, tiek izmantots DAQ lejupvērstās starojuma sadalījuma koeficients (ρ = πL/E).
* **`target`**: stingri mērķa vadīta atstarojamība — bez DAQ aizstāšanas.
* **`daq`**: DAQ noteiktā atstarojamība; kadrā esošie mērķi netiek izmantoti kā atsauce.

Saglabātā vērtība tiek saskaņota, neņemot vērā lielos un mazos burtus, un daži rakstības varianti tiek pieņemti kā sinonīmi: `target`, `target_image`, `empirical` un `empirical_line` — visi nozīmē **mērķi**; `daq`, `dls`, `light_sensor` un `sensor` visi nozīmē**daq**. Jebkurš cits variants — ieskaitot neesošu atslēgu — tiek interpretēts kā**auto**.**Izmērītie** mērķa skenējumi pa vienībām tiek meklēti pēc mērķa vienības sērijas numura/QR koda, piemēram, `<serial>.csv`, trīs vietās: direktorijā, kas norādīta ar `--target-reflectance-dir` (saglabāts kā `Processing → "Target reflectance dir"`), projekta paša `target_reflectance/` mapē un ceļā, kas norādīts `CHLOROS_TARGET_REFLECTANCE_DIR` vides mainīgajā. Ja šai vienībai nav pieejams izmērīts skenējums, tā vietā tiek izmantota mērķa modeļa nominālā publicētā līkne.

### Demosaicinga metode

* **Tips**: Izvēlne
* **Opcijas**:
  * Standarta (ātra, vidēja kvalitāte)
  * Tekstūras ņemšana vērā (lēna, augstākā kvalitāte) \[Chloros+]
* **Noklusējums**: Standarta (ātra, vidēja kvalitāte)
* **Apraksts**: Izvēlas demosaicinga algoritmu, ko izmanto, lai pārvērstu neapstrādātos Bayer modeļa sensora datus pilnkrāsu attēlos. Metode „Standarta (ātra, vidēja kvalitāte)” nodrošina optimālu līdzsvaru starp apstrādes ātrumu un attēla kvalitāti. Metode „Ar tekstūras ņemšanu vērā (lēna, visaugstākā kvalitāte)” \[Chloros+] izmanto augstas kvalitātes, kontūru ņemošu demosaicinga algoritmu, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kurš novērš gandrīz visus demosaicinga radītos trokšņus. „Texture Aware” modelim darbībai ir nepieciešama GPU atmiņa (VRAM). Mēs iesakām to izmantot, ja jums ir pieejami &gt;4 GB VRAM, lai nodrošinātu ātrāku apstrādi.
* **Ja rinda vispār ir nolaižamais izvēlnes elements**: divu opciju izvēlne parādās tikai tad, ja**abas**nosacījumi ir izpildīti — esat pieteicies ar atbilstošu Chloros+ abonementu,**un** projektā nav LATTICE uzņēmumu. Pretējā gadījumā rinda tiek attēlota kā parasts teksts ar uzrakstu „`Standard (Fast, Medium Quality)`”, un izvēle nav pieejama.
* **LATTICE piezīme**: Nav LATTICE apmācīta „Texture Aware“ modeļa, un apstrādes procesa posms LATTICE kadriem piespiež izmantot standarta demosaiku neatkarīgi no saglabātās vērtības. Ja pievienojat LATTICE mapi projektam, kurā jau bija atlasīta opcija „Texture Aware“, Chloros pārraksta iestatījumu atpakaļ uz „Standard”, nevis atstāj novecojušu vērtību `project.json`.

### Minimālais pārkalibrēšanas intervāls

* **Tips**: Skaitlis
* **Diapazons**: no 0 līdz 3600 sekundēm
* **Noklusējums**: 0 sekundes
* **Apraksts**: Nosaka minimālo laika intervālu (sekundēs) starp kalibrēšanas mērķu izmantošanu. Ja iestatīts uz 0, Chloros izmantos katru atklāto kalibrēšanas mērķi. Ja iestatīts uz lielāku vērtību, Chloros izmantos tikai tos kalibrēšanas mērķus, starp kuriem ir vismaz šāds laika intervāls, samazinot apstrādes laiku datu kopām, kurās bieži tiek uzņemti kalibrēšanas mērķi.
* **Kad pielāgot**:
  * Iestatiet uz 0, lai nodrošinātu maksimālu kalibrēšanas precizitāti mainīgos apgaismojuma apstākļos
  * Palieliniet (piemēram, līdz 60–300 sekundēm), lai paātrinātu apstrādi, ja apgaismojums ir nemainīgs un jums ir bieži uzņemti kalibrēšanas mērķu attēli

### Gaismas sensora laika zonas nobīde

* **Tips**: Skaitlis
* **Diapazons**: no -12 līdz +12 stundām
* **Noklusējums**: 0 stundas
* **Apraksts**: Nosaka laika zonas nobīdi (stundās no UTC) gaismas sensora datu laika zīmogiem, ko izmanto, saskaņojot gaismas sensora žurnālus ar attēlu uzņemšanas laikiem. Jaunākie `.daq` ieraksti satur savu laika zonas izcelsmi, tādēļ šī funkcija galvenokārt ir nepieciešama vecākiem žurnāliem, kas ierakstīti pēc vietējā laika.

### Piemērot PPK korekcijas

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Atspējots (neatzīmēts)
* **Apraksts**: Ļauj izmantot pēcapstrādes kinemātiskās (PPK) korekcijas no MAPIR DAQ ierakstītājiem, kuros ir iebūvēts GPS (GNSS). Ja šī funkcija ir ieslēgta, Chloros izmantos visus .daq žurnāla failus, kas satur ekspozīcijas pinu datus jūsu projekta direktorijā, un piemēros precīzas ģeolokācijas korekcijas jūsu attēliem.
* **Prasība**: jūsu projekta direktorijā ir jābūt .daq žurnāla failam ar ekspozīcijas pinu ierakstiem
* **Kad ieslēgt**: Ieteicams vienmēr ieslēgt PPK korekciju, ja jūsu .daq žurnāla failā ir ekspozīcijas atgriezeniskās saites ieraksti.

### Ekspozīcijas kontakts 1

* **Tips**: Izvēlne
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta opcija „Piemērot PPK korekcijas” UN ja ekspozīcijas dati ir pieejami 1. kontaktam
* **Opcijas**:
  * Projektā atpazītie kameru modeļu nosaukumi
  * „Nelietot” — ignorēt šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: Piesaka konkrētu kameru ekspozīcijas kontaktpunktam 1 PPK laika sinhronizācijai. Ekspozīcijas kontaktpunkts reģistrē precīzo brīdi, kad tiek iedarbināts kameras aizslēgs, kas ir ļoti svarīgi precīzai PPK ģeolokācijai.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: automātiski izvēlas kameru
  * Viena kamera + divi kontakti: 1. kontakts automātiski tiek piešķirts kamerai
  * Vairākas kameras: nepieciešama manuāla izvēle

### Ekspozīcijas 2. kontakts

* **Tips**: izvēle no nolaižamā saraksta
* **Redzamība**: Redzams tikai tad, ja ir ieslēgta opcija „Piemērot PPK korekcijas” UN ja ekspozīcijas dati ir pieejami 2. kontaktam
* **Opcijas**:
  * Projektā atpazītie kameru modeļu nosaukumi
  * „Nelietot” — ignorē šo ekspozīcijas kontaktu
* **Noklusējums**: Automātiski izvēlēts, pamatojoties uz projekta konfigurāciju
* **Apraksts**: Piesaka konkrētu kameru ekspozīcijas kontaktam 2, lai nodrošinātu PPK laika sinhronizāciju, izmantojot divu kameru konfigurāciju.
* **Automātiskās izvēles darbība**:
  * Viena kamera + viens kontakts: Kontaktam 2 automātiski tiek iestatīts „Nelietot”
  * Viena kamera + divi kontakti: 2. kontakts automātiski tiek iestatīts uz „Nelietot”
  * Vairākas kameras: nepieciešama manuāla izvēle
* **Piezīme**: Vienu un to pašu kameru nevar vienlaikus piešķirt gan 1. kontaktam, gan 2. kontaktam.***

## DAQ gaismas sensors

Šī sadaļa parādās projekta iestatījumos un uzskaita visus projektā esošos DAQ lejupvērstās gaismas failus — `.daq` ierakstus un DAQ-M `.csv` lejupvērstās gaismas žurnāli. Ieraksti, kas veikti cilnē „Gaismas sensori”, automātiski tiek pievienoti atvērtam projektam.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->

Katrā rindā ir norādīts fails, sensora modelis un difuzora vāciņa korekcija, kas faktiski tiek piemērota šim failam. Virs rindām atrodas viens projekta līmeņa vadības elements:

### Vāciņa (visi faili)

* **Tips**: Izvēlne
* **Opcijas**: `Auto`, kā arī vāciņas korekcijas profili, kas ir spēkā projekta sensoru tipiem
* **Noklusējums**: Automātiski
* **Saglabāts kā**: `Processing → "DAQ cap id"` (noklusējumā `auto`)
* **Apraksts**: `Auto` izmanto katra faila reģistrēto maksimālās vērtības ierobežojumu (ja nekas nav reģistrēts, tiek pieņemts „Sunshine” ierobežojums — visi MAPIR datu ieguves ierīces tiek piegādātas ar „Sunshine” korektoru). Konkrēta vāciņa pārraksta**visus** projekta lejupvērstos failus: neapstrādātie ieraksti tiek koriģēti ar to, un ierakstiem, kuriem jau ir pievienota vāciņa, tiek mainīta atsauce (ierakstītā korekcija tiek atcelta un piemērota izvēlētā).
* **Svarīgi**: Izvēlētajam vāciņam jāatbilst tam vāciņam, kas reģistrācijas laikā bija fiziski uzstādīts. Ne sensors, ne programmatūra nespēj noteikt fizisko vāciņu — neatbilstošs vāciņa ID spektrus koriģē nepareizi.

Tīši ir paredzēts **viens** projekta mēroga vadības elements, nevis atsevišķi izvēles saraksti katram failam: šis iestatījums attiecas uz katru lejupvērsto avotu projektā.***

## Masīva saskaņošana

Šī sadaļa parādās **tikai** tad, ja vismaz vienam attēlam projektā ir piemērota moduļu savstarpējās saskaņošanas transformācija, ko LATTICE masīvi pievieno uzņemšanas brīdī (XMP `Chloros:Alignment*` birkas). Tajā tiek parādīts, cik daudziem attēliem ir izlīdzināšanas tagi, kura kamera ir atsauces kamera (`REF` marķējums), kā arī attēlu skaita tabula pa kamerām.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Pielietot masīva izlīdzināšanu

* **Tips**: Izvēles rūtiņa
* **Noklusējums**: Iespējots (atzīmēts)
* **Saglabāts kā**: `Processing → "Array alignment"`
* **Apraksts**: Pārveido katru apstrādāto rezultātu (debayering / priekšskatījums / starojums / atstarošanās / indekss) atbilstoši masīva kopīgajai atsauces ģeometrijai, izmantojot uzņemšanas brīdī ievietoto transformāciju. Izslēgts = eksportē sākotnējā ģeometrijā katram sensora modulim.

### Apgriezt līdz kopīgajai pārklāšanās zonai

* **Tips**: Izvēles rūtiņa (aktīva tikai tad, ja ir ieslēgta opcija *Piemērot masīva izlīdzināšanu*)
* **Noklusējums**: Ieslēgts (atzīmēts)
* **Saglabāts kā**: `Processing → "Array alignment crop"`
* **Apraksts**: Apgriež izlīdzinātos eksportus līdz reģionam, ko dala visi kameras moduļi, tādējādi katrai joslai ir vienāds laukums. Izslēgts saglabā pilnu sensora laukumu (melna pildījuma zona ārpus avota).

### Pārparaugošana

* **Tips**: Izvēles saraksts (aktīvs tikai tad, ja ir ieslēgta opcija *Piemērot matricas izlīdzināšanu*)
* **Opcijas**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Noklusējums**: Bilineārs
* **Saglabāts kā**: `Processing → "Array alignment interpolation"`
* **Apraksts**: Interpolācija, ko izmanto izlīdzināšanas deformācijā. “Tuvākais” saglabā precīzas avota vērtības (bez pikseļu sajaukšanas) stingrai radiometriskai analīzei;**Bilineārā** ir vislabāk piemērota kartēšanai un vizuālai izmantošanai.

Tās pašas trīs opcijas ir pieejamas arī bez nosaukuma kā `chloros-cli process --array-alignment`, `--array-alignment-crop` un `--array-alignment-interp {bilinear,nearest,cubic}`.

***

## Indekss

Šie iestatījumi ļauj konfigurēt multispektrālos indeksus analīzei un vizualizācijai.

### Pievienot indeksu

* **Tips**: Speciāls indeksu konfigurācijas panelis
* **Apraksts**: Atver interaktīvu paneli, kurā varat izvēlēties un konfigurēt multispektrālos veģetācijas indeksus (NDVI, NDRE, EVI, utml.), kurus aprēķināt attēla apstrādes laikā. Var pievienot vairākus indeksus, katram ar saviem vizualizācijas iestatījumiem.
* **Pieejamie indeksi**: GUI nolaižamajā izvēlnē ir iekļautas**27** iepriekš definētas multispektrālo indeksu formulas (skatiet [Multispektrālo indeksu formulas](multispectral-index-formulas.md), lai redzētu pilnu sarakstu, tostarp to nosaukumus, kurus pieņem arī CLI/SDK `--indices` opcija).
* **Funkcijas**:
  * Izvēlieties no iepriekš definētām indeksu formulām
  * Velciet kameras filtra kanālus uz formulas joslu vietām
  * Konfigurējiet vizualizācijas krāsu gradientus (LUT — meklēšanas tabulas)
  * Iestatiet sliekšņa vērtības un nogriešanas režīmus
  * Izveidojiet pielāgotas indeksu formulas
* **Piezīme**: Indeksi netiek aprēķināti vienjoslas LATTICE M3M mono kamerām — daudzjoslu indeksi vienā joslā nav definēti. Tas neattiecas uz Survey3 un LATTICE M3C.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->

Katram pievienotajam indeksam tā formula tiek attēlota kā matemātiska izteiksme ar krāsainu apli katram joslas slotam: sarkans = Red, zaļš = Green, zils = Blue, oranžs = Orange, ciāna = Cyan, violeta = NIR, magenta = RE. Velciet apli no rindas virs formulas uz slota, lai to piesaistītu; dubultklikšķiniet uz piesaistītā slota, lai to atbrīvotu. Indekss tiek aprēķināts tikai tad, ja visām formulu izmantotajām vietām ir kanāls.

### Pielāgotās formulas (Chloros+ funkcija)

* **Tips**: Pielāgotu formulu definīciju masīvs
* **Pieejamība**: Nepieciešama pieteikšanās ar atbilstošu Chloros+ abonementu.
* **Apraksts**: Ļauj izveidot un saglabāt pielāgotas multispektrālo indeksu formulas, izmantojot joslu matemātiku. Pielāgotās formulas tiek saglabātas kopā ar jūsu projekta iestatījumiem un tās var izmantot tāpat kā iebūvētos indeksus.
* **Kā izveidot**:
  1. Indeksa konfigurācijas panelī atveriet pielāgoto formulu kalkulatoru
  2. Uzrakstiet formulu, izmantojot **joslu-slotu simbolus**, nevis joslu nosaukumus
  3. Saglabājiet formulu ar aprakstošu nosaukumu — tā parādīsies formulu nolaižamā izvēlnē apakšā, un jūs varēsiet pārvilkt savas kameras kanālu apļus uz tās vietām tieši tāpat kā iebūvētajā iestatījumā
* **Formulas sintakse**:
  * Frekvenču joslu sloti: `x`, `y`, `z`, `a`, `b`, `c` — sešas pozīcijas, kuras varat piesaistīt reāliem kanāliem, velkot
  * Operatori: `+`, `-`, `*`, `/`, `^` un `()` grupēšanai
  * Funkcijas: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Kāpēc simboli, nevis grupu nosaukumi**: formula, kas rakstīta kā `(y-x)/(y+x)`, darbojas jebkurā kamerā, jo velk-un--nomet” kartēšana nosaka, vai `y` ir 850 nm NIR no RGN filtra vai 808 nm NIR no OCN filtra. Iebūvētās priekšiestatījumi tiek saglabāti tādā pašā veidā — skatiet [Multispektrālo indeksu formulas](multispectral-index-formulas.md), lai uzzinātu precīzo simbolu formu visiem 27 indeksiem.
* **Kur tās darbojas**: pielāgotās formulas tiek saglabātas kopā ar projekta iestatījumiem un tās var izmantot gan [Indeksu/LUT izmēģinājumu vidē](../image-viewer-gui/index-lut-sandbox.md), gan apstrādē. Tās**netiek** atbalstītas CLI/SDK `--indices` nosaukumu sarakstā, kurš tikai paplašina 22 iebūvēto iestatījumu nosaukumus.***

## Eksportēšana

Šie iestatījumi nosaka eksportēto apstrādāto attēlu formātu un kvalitāti.

### Kalibrēta attēla formāts

* **Tips**: Izvēlne
* **Opcijas**:
  * **TIFF (16 bitu)** — nesaspiests 16 bitu TIFF formāts
  * **TIFF (32 bitu, procentos)** — 32 bitu peldošā punkta TIFF ar atstarojuma vērtībām procentos
  * **PNG (8 bitu)** — saspiests 8 bitu PNG formāts
  * **JPG (8 bitu)** — saspiests 8 bitu JPEG formāts
* **Noklusējums**: TIFF (16 bitu)
* **Apraksts**: Izvēlas failu formātu, kurā saglabāt apstrādātos un kalibrētos attēlus. Eksportētie attēli tiek saglabāti atbilstoši formātam atsevišķās apakšmapēs katras kameras mapē (`tiff16`, `tiff32`, `png8`, `jpg8`), ar vienu `<Product>_Images/` mapi katram produktam. Eksportētajiem failiem tiek saglabāts avota faila nosaukums — produktu identificē mape, nevis faila nosaukuma paplašinājums.
* **Formāta ieteikumi**:
  * **TIFF (16 bitu)**: Ieteicams zinātniskai analīzei un profesionālām darba plūsmām. Saglabā maksimālu datu kvalitāti bez kompresijas artefaktiem. Vispiemērotākais multispektrālajai analīzei un turpmākai apstrādei ĢIS programmās.
  * **TIFF (32 bitu, procentos)**: Vispiemērotākais darba plūsmām, kurās nepieciešamas atstarojuma vērtības procentos (0–100 %). Nodrošina maksimālu precizitāti radiometriskajos mērījumos.
  * **PNG (8 bitu)**: Piemērots skatīšanai tīmeklī un vispārējai vizualizācijai. Mazāki failu izmēri ar bezzaudējumu saspiešanu, taču samazināts dinamiskais diapazons.
  * **JPG (8 bitu)**: Vismazākie failu izmēri, piemērots tikai priekšskatīšanai un attēlošanai tīmeklī. Izmanto saspiešanu ar zaudējumiem, kas nav piemērota zinātniskai analīzei.
* **Piezīme**: LATTICE starojums vienmēr tiek eksportēts kā 32-bitu peldošā punkta formāts TIFF neatkarīgi no šiem iestatījumiem.***

## Projekta veidnes saglabāšana

Šī funkcija ļauj saglabāt pašreizējos projekta iestatījumus kā atkārtoti izmantojamu veidni.

* **Tips**: Teksta ievade + Saglabāt poga
* **Apraksts**: Ievadiet aprakstošu nosaukumu savai iestatījumu veidnei un noklikšķiniet uz saglabāšanas ikonas. Veidnē tiks saglabāti visi jūsu pašreizējie projekta iestatījumi (mērķa noteikšana, apstrādes opcijas, indeksi un eksporta formāts), lai tos varētu viegli atkārtoti izmantot turpmākajos projektos. Veidnes tiek saglabātas mapē `Project Templates/` jūsu projekta saglabāšanas mapē, un tās var arī izvēlēties vai eksportēt no galvenās izvēlnes (*Izvēlēties veidni* / *Saglabāt veidni* / *Eksportēt veidni*).
* **Lietošanas piemēri**:
  * Izveidojiet veidnes dažādām kameru sistēmām (RGB, multispektrālās, NIR)
  * Saglabājiet standarta konfigurācijas konkrētiem kultūraugu veidiem vai analīzes darba plūsmām
  * Dalieties ar vienotiem iestatījumiem visai komandai
* **Kā lietot**:
  1. Konfigurējiet visus vēlamos projekta iestatījumus
  2. Ievadiet veidnes nosaukumu (piem., „RedEdge Survey3 NDVI Standarts”)
  3. Noklikšķiniet uz saglabāšanas ikonas
  4. Tagad šo veidni var ielādēt, veidojot jaunus projektus

***

## Projekta mapes saglabāšana

Šis iestatījums nosaka, kur pēc noklusējuma tiek saglabāti jauni projekti.

* **Tips**: Kataloga ceļa parādīšana + pogas „Rediģēt”
* **Noklusējums (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Noklusējums (Linux)**: `~/Chloros Projects`
* **Apraksts**: Parāda pašreizējo noklusējuma direktoriju, kurā tiek izveidoti jauni Chloros projekti. Noklikšķiniet uz rediģēšanas ikonas, lai izvēlētos citu direktoriju. Pārrakstītais iestatījums tiek saglabāts kā viena teksta rinda failā `~/.chloros/working_directory.txt` — failā Windows, kas ir `C:\Users\<Username>\.chloros\working_directory.txt`. Ja šis fails nav atrodams, vai norāda ceļu, kas vairs nepastāv, Chloros izmanto iepriekš minēto noklusējuma iestatījumu. CLI lasa un raksta tajā pašā failā, tādēļ `chloros-cli` un lietotāja saskarne vienmēr saskan par to, kur projekti atrodas.
* **Projekta veidnes** atrodas šī kataloga apakšmapē `Project Templates/`.
* **Kad mainīt**:
  * Iestatiet tīkla disku, lai veicinātu komandas sadarbību
  * Mainiet uz disku ar lielāku uzglabāšanas vietu lieliem datu kopumiem
  * Sakārtot projektus pēc gada, klienta vai projekta veida atsevišķās mapēs
* **Piezīme**: Šī iestatījuma maiņa attiecas tikai uz JAUNIEM projektiem. Esošie projekti paliek savās sākotnējās atrašanās vietās.***

## Iestatījumu saglabāšana

Chloros projekts ir **mape**. Visi projekta iestatījumi tiek saglabāti tajā esošajā `project.json`; savienotā aparatūra tiek saglabāta kopā ar tiem `cameras.json` un `sensors.json`, tādējādi, atverot projektu no jauna, tiek atjaunota arī savienojuma ar kamerām un gaismas sensoriem. Kad atverat projektu no jauna, visi iestatījumi tiek atjaunoti tieši tā, kā tos atstājāt. Saglabātos projektus var vadīt arī bez monitora, izmantojot `chloros-cli project` vai SDK failā esošo `open_project`.

### Iestatījumu hierarhija

Iestatījumi tiek piemēroti šādā secībā:

1. **Sistēmas noklusējumi** — iebūvēti noklusējumi, kurus definē Chloros
2. **Veidnes iestatījumi** — ja, izveidojot projektu, tiek ielādēta veidne
3. **Saglabātie projekta iestatījumi** — iestatījumi, kas saglabāti kopā ar projekta failu
4. **Manuālie pielāgojumi** — jebkuras izmaiņas, ko veicat pašreizējās sesijas laikā

### Iestatījumi un attēlu apstrāde

Apstrādes iestatījumi tiek nolasīti, sākot apstrādes ciklu. Iestatījuma maiņa neietekmē jau diskā esošos rezultātus — lai piemērotu jaunās iestatījumu vērtības, apstrāde jāveic no jauna. Daži iestatījumi nekādā veidā neietekmē apstrādi:

* Attēla sīktēla izšķirtspēja (tikai parādīšanai)
* Saglabāt projekta veidni
* Saglabāt projekta mapi

***

## Konfigurācijas atslēgu apraksts

Automatizācijai (CLI `--config`, SDK `configure` vai lasot `project.json` tieši), šie ir precīzie atslēgas `Project Settings`:

| Atslēgas ceļš | Tips | Noklusējums |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | skaitlis 0–10000 | `25` |
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
| `Processing → Minimum recalibration interval` | skaitlis 0–3600 | `0` |
| `Processing → Light sensor timezone offset` | skaitlis -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | ierobežojuma profila identifikators vai `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | indeksu konfigurāciju saraksts | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

`Array alignment` atslēgas tiek ierakstītas, kad pirmo reizi tiek renderēta sadaļa „Array Alignment” vai tās iestata automatizācijas izsaukums. Kamēr tās nav pieejamas, apstrādes ceļš izmanto tās pašas vērtības, kas norādītas iepriekš (`true`, `true`, bilineārs), tādējādi projekts bez šiem atslēgvārdiem darbojas tieši tāpat kā projekts ar tiem.

### Atslēgas, kas glabājas `project.json` un kurām nav vadības elementa iestatījumu panelī

Tās atrodas tajā pašā `Project Settings` kokā un tiek nolasītas apstrādes laikā, taču sānu joslā jūs neatradīsiet tām paredzētu vadības elementu:

| Atslēgas ceļš | Tips | Noklusējums | Iestatīts ar |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK `input_level=`. Pārraksta to, kā tiek interpretēti LATTICE ieejas TIFF faili; `auto` secina no katra faila `Chloros:ProcessingLevel` XMP tagu un kanālu skaitu. Netiek ņemts vērā Survey3 `.raw` uzņemumiem. Apzināti nav GUI iestatījums — iestatījums „auto” ir pareizs visos parastos gadījumos. |
| `Processing → Target reflectance dir` | ceļa virkne | `""` | `chloros-cli process --target-reflectance-dir` vai projekta mērķis API |
| `Processing → Target reflectance config` | vārdnīca, kuras atslēga ir kameras sērijas numurs | `{}` | Mērķa reģistrēšana kadrā (režīms `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | ceļa virkne | `""` | SDK `process_folder(daq_log_path=…)`. Norāda uz `.daq` ierakstu vai to mapi |
| `Target Detection → Minimum calibration target squares` | skaitlis | `4` | Vecā standarta iestatījums; bez vadības un bez CLI atzīmes |
| `UI → Grid thumbnail size` | skaitlis | `160` | attēlu režģa paša sīktēlu tuvināšanas sliders |

Divi skatītāja iestatījumi tiek saglabāti **augstākajā līmenī `project.json`**, pilnīgi ārpus `Project Settings`, jo tie ir attēlošanas stāvoklis, nevis apstrādes iestatījumi:

| Atslēgas ceļš | Tips | Noklusējums | Iestata |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | vesels skaitlis 1–256 | `1` | Attēla cilnes GSD (px) vadības elements — skatīt [Attēla atvēršana pilnekrāna režīmā](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## Labākā prakse

1. **Sāciet ar noklusējumiem**: Noklusējuma iestatījumi labi darbojas lielākajai daļai MAPIR kameru sistēmu un tipiskajām darba plūsmām.
2. **Izveidojiet veidnes**: Kad esat optimizējuši iestatījumus konkrētai darba plūsmai vai kamerai, saglabājiet tos kā veidni, lai nodrošinātu konsekvenci visos projektos.
3. **Pārbaudiet pirms pilnīgas apstrādes**: Eksperimentējot ar jauniem iestatījumiem, pārbaudiet tos uz nelielu attēlu apakškopu, pirms apstrādājat visu datu kopu.
4. **Dokumentējiet savus iestatījumus**: Izmantojiet aprakstošus veidņu nosaukumus, kas norāda uz kameru sistēmu, apstrādes veidu un paredzēto lietojumu (piem., „Survey3\_RGB\_NDVI\_Lauksaimniecība”).
5. **Eksporta formāta izvēle**: Izvēlieties eksporta formātu atbilstoši galīgajam lietojumam:
   * Zinātniskā analīze → TIFF (16 bitu vai 32 bitu)
   * ĢIS apstrāde → TIFF (16 bitu)
   * Ātra vizualizācija → PNG (8 bitu)
   * Koplietošana tīmeklī → JPG (8 bitu)

***

Lai iegūtu vairāk informācijas par multispektrālajiem indeksiem programmā Chloros, skatiet lapu [Multispektrālo indeksu formulas](multispectral-index-formulas.md).
