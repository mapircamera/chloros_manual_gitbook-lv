# Projekta iestatījumu pielāgošana

Pirms attēlu apstrādes ir svarīgi konfigurēt projekta iestatījumus atbilstoši jūsu darba plūsmas prasībām. Panelis „Projekta iestatījumi“ <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> nodrošina visaptverošu kontroli pār kalibrēšanu, apstrādes opcijām, multispektrālajiem indeksiem un eksporta formātiem.

## Piekļuve projekta iestatījumiem

1. Atveriet savu projektu programmā Chloros
2. Noklikšķiniet uz ikonas **„Projekta iestatījumi“** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> kreisajā sānjoslā
3. Panelī „Projekta iestatījumi“ tiek parādītas visas konfigurācijas opcijas

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Projekta iestatījumu panelis — attēla parādīšana, mērķa noteikšana un apstrāde</p></figcaption></figure>

{% hint style="info" %}
**Iestatījumi tiek saglabāti automātiski** kopā ar projektu. Atverot projektu no jauna, visi iestatījumi tiek atjaunoti.
{% endhint %}

***

## Ātra konfigurācija tipiskiem darba procesiem

### Noklusējuma iestatījumi (ieteicami lielākajai daļai lietotāju)

Noklusējuma iestatījumi labi darbojas tipiskiem Survey3 un „LATTICE” darba procesiem:

* ✅ **Vignette korekcija**: Iespējota
* ✅ **Atstarošanas kalibrēšana / baltā balanss**: ieslēgts (izmanto MAPIR mērķus un/vai DAQ gaismas sensora datus)
* ✅ **Debayer metode**: standarta (ātra, vidēja kvalitāte)
* ✅ **Eksporta formāts**: TIFF (16 bitu) (TIFF (8 bitu) projektiem, kas ir pilnībā 8 bitu)
* ✅ **Visi eksporta rezultāti**: ieslēgts (LATTICE automātiski saglabā attēlus, kas sadalīti pēc Debayera metodes, priekšskatījumu, starojuma un atstarošanas)

Vienkārši importējiet savus attēlus un sāciet apstrādi, izmantojot šos noklusējumus.

***

## Projekta iestatījumu pārskats

Projekta iestatījumu panelis ir sadalīts zemāk minētajās sadaļās. Divas papildu sadaļas — **DAQ gaismas sensors**un**Masīva izlīdzināšana** — parādās automātiski, ja jūsu projektā ir attiecīgie faili. Pilnīga dokumentācija ir pieejama sadaļā [Projekta iestatījumi](../project-settings/project-settings.md).

### Ekrāns

* **Attēlu sīktēlu izšķirtspēja**: attēlu režģa sīktēlu izšķirtspēja. Iespējas:**Noklusējums (512 pikseļi)**,**1024 pikseļi**,**2048 pikseļi**,**Pilna izšķirtspēja**. Tikai attēlošanai — nekad neietekmē apstrādi. Augstākas vērtības izskatās asākas, palielinot attēlu, bet ielādējas lēnāk.

### Mērķa atpazīšana

Nosaka, kā Chloros identificē kalibrēšanas mērķus jūsu attēlos.

**Galvenie iestatījumi:**

* **Minimālā kalibrēšanas parauga platība (px)**: Mērķa atpazīšanas izmēra slieksnis (noklusējums:**25**, diapazons 0–10000)
* **Minimālā mērķu grupēšana (0–100)**: Līdzības slieksnis mērķu reģionu grupēšanai (noklusējums:**60**)**Kad pielāgot:**

* Palieliniet parauga laukumu, ja rodas kļūdainas atklāšanas
* Samaziniet, ja mērķi netiek atklāti
* Pielāgojiet grupēšanu, ja mērķi tiek sadalīti vairākās atklāšanās

{% hint style="info" %}
Šie iestatījumi ir nedarbīgi, ja ir atspējota **atstarošanas kalibrēšana / baltā balanss** — ja tā ir atspējota, mērķu noteikšana vispār nedarbojas.
{% endhint %}

### Apstrāde

Galvenās attēlu apstrādes un kalibrēšanas opcijas.

**Galvenie iestatījumi:**

* **Vignēšanas korekcija**: kompensē objektīva tumšošanos malās ✅ Ieteicams
* **Atstarošanas kalibrēšana / baltā balanss**: kalibrē attēlus, izmantojot atklātos mērķus (Survey3) un/vai DAQ gaismas sensora datus (LATTICE) ✅ Ieteicams
* **Debayer metode**: Algoritms RAW failu konvertēšanai 3-kanālu multispektrālos attēlos
* **Minimālais atkārtotās kalibrēšanas intervāls**: Minimālais laiks sekundēs starp kalibrēšanas mērķa izmantošanu (noklusējums:**0** = izmantot visus, diapazons 0–3600)**Nekalibrēti rezerves produkti:**Ja attēla rāmi nav iespējams kalibrēt pēc atstarojuma (nav pieejams mērķis vai kalibrēšana ir atspējota), tas tiek eksportēts kā viens no diviem rezerves produktiem —**katrā darbības ciklā pastāv tieši viens no šiem diviem**, ko izvēlas ar Vignette korekcijas slēdzi:

* **Eksportēt sensora reakciju**: ieraksta `Sensor_Response_Images` — izmanto, ja „Vignette” korekcija ir**izslēgta**
* **Eksportēt ar vignetēšanas korekciju**: ieraksta `Vignette_Corrected_Images` — izmanto, ja vignetēšanas korekcija ir**ieslēgta**Izvēles rūtiņa, kas nav aktīva, ir izdzēsta. Atceļot atzīmi no aktīvās rūtiņas, šī faila ierakstīšana tiek pilnībā apturēta.**LATTICE eksporta produkti** (parādās katram projektam; tie attiecas uz LATTICE uzņēmumiem):

* **Eksportēt bez demosaicēšanas**: lineārs attēls bez mozaīkas (`Debayered_Images`): no kalibrēšanas paketes ir noņemts tumšais nobīdes kompensācijas slānis, pēc tam veikta demosaicēšana bez līdzena lauka vai krāsu atdalīšanas — tas pats fails, ko uzņemums saglabā bez mozaīkas līmenī — sensora oriģinālajā orientācijā. Attiecas uz RGB un multispektrālajiem moduļiem.
* **Eksportēt priekšskatījumu**: ekrāna priekšskatījums (`Preview_Images`). RGB = baltā balanss (DAQ apgaismojums, ja pieejams, citādi pelēkā pasaule) + gamma; multispektrālais = viltus krāsu izstiepums.
* **Eksportēt starojuma intensitāti**: float32 spektrālā starojuma intensitāte (`Radiance_Images`, W/m²/sr/nm). Tikai multispektrālie moduļi — neattiecas uz RGB oriģināliem.
* **Eksportēt atstarošanu**: uint16 atstarošana (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1,0), ja `.daq` lejupvērstais rādījums vai kadrā esošais mērķis aptver kadru. Tikai multispektrālie moduļi.

Visi četri ir **pēc noklusējuma ieslēgti**— viens importēts LATTICE neapstrādāts kadrs tiek sadalīts pa visiem iespējotajiem un piemērojamajiem produktiem vienā apstrādes ciklā. Izvēles rūtiņa**Eksportēt atstarojumu** ir nedzēšama, ja atstarojuma kalibrēšana ir izslēgta. Iestatījumi, kurus neļauj izmantot augstāk esošais slēdzis, vienmēr ir izslēgti ar pelēku fonu, un parādās rīkjosla, kurā norādīts, kuru slēdzi jāmaina.

**Papildu iestatījumi:**

* **Gaismas sensora laika zonas nobīde**: Stundas no UTC gaismas sensora laika saskaņošanai (noklusējums: 0, diapazons no −12 līdz +12)
* **Piemērot PPK korekcijas**: izmanto GPS/ekspozīcijas pinu datus no `.daq` failiem (noklusējums: izslēgts)
* **Ekspozīcijas pini 1/2**: piešķir kameras ekspozīcijas piniem divkameru konfigurācijām

{% hint style="info" %}
**LATTICE ieejas līmenis ir automātisks.** LATTICE uzņēmumos apstrādes līmenis tiek saglabāts XMP metadatos, un apstrāde vienmēr sākas no neapstrādāta kadra — lietotāja saskarnē nav nekas jākonfigurē. (CLI karodziņš `--input-level` ir paredzēts pieredzējušiem lietotājiem, lai pārrakstītu iestatījumus attēliem ar zaudētiem metadatiem; skatiet [CLI atsauci](../reference/cli-reference.md).)
{% endhint %}

### Debayeringa metode

Pašlaik Chloros piedāvā 2 debayeringa metodes:

#### Standarta (ātra, vidēja kvalitāte)

Standarta debayeringa metode darbojas ātri, taču rada krāsu troksni, kā rezultātā attēli ir mazāk precīzi un trokšņaināki.

#### Tekstūras ņemšana vērā (lēna, visaugstākā kvalitāte) \[Tikai Chloros+]

Metode „Tekstūras ņemšana vērā” izmanto augstas kvalitātes, malu ņemšanu vērā ņemošu debayeringu, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kurš novērš gandrīz visus debayeringa radītos trokšņus. Lai modelis darbotos, ir nepieciešama GPU atmiņa (VRAM): ja **VRAM ir 7 GB vai vairāk**, tas var vienlaikus apstrādāt vairākus attēlus; ja VRAM ir mazāk par 7 GB, tas apstrādā vienu attēlu pēc otra (ievērojami lēnāk). Skatīt [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**LATTICE uzņēmumos vienmēr tiek izmantota standarta demosaic metode.** Nav LATTICE apmācīta „Texture Aware” modeļa, tāpēc šī opcija netiek piedāvāta LATTICE attēliem — tomēr to joprojām var izmantot Survey3 attēliem tajā pašā projektā.
{% endhint %}

### Indekss (daudzspektrālie indeksi)

Konfigurējiet, kurus veģetācijas indeksus aprēķināt un eksportēt. Grafiskās lietotāja saskarnes nolaižamajā izvēlnē ir pieejamas **27 iepriekš definētas indeksu formulas**.**Kā pievienot indeksus:**

1. Noklikšķiniet uz pogas**„Pievienot indeksu””**

2. Izvēlieties indeksu no nolaižamās izvēlnes (NDVI, NDRE, GNDVI utt.)
3. Konfigurējiet vizualizācijas iestatījumus (LUT krāsas, vērtību diapazoni)
4. Pēc nepieciešamības pievienojiet vairākus indeksus

**Populāri indeksi:**

* **NDVI**: Vispārējais veģetācijas veselības stāvoklis (visbiežāk izmantotais)
* **NDRE**: RedEdge izmantošana agrīnai stresa noteikšanai* **GNDVI**: jutīgs pret hlorofila koncentrāciju
* **OSAVI**: labi darbojas ar redzamu augsni
* **EVI**: reģioni ar augstu lapu platības indeksu (LAI)

**Pielāgotas formulas:**

* Izveidojiet pielāgotas multispektrālo indeksu formulas, izmantojot joslu aprēķinus visos attēla kanālos
* Saglabājiet pielāgotās formulas atkārtotai izmantošanai
* Pielāgotās formulas ir „Chloros+” funkcija; to pieejamība ir atkarīga no jūsu plāna līmeņa

Lai apskatītu visus pieejamos indeksus un formulas — tostarp to, kuri nosaukumi ir pieejami tikai grafiskajā lietotāja saskarnē (GUI) un kuri darbojas arī CLI vai SDK — skatiet [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

### Eksportēšana

Nosaka izejas faila formātu.

**Pieejamie formāti**(iestatījums:**Kalibrēts attēla formāts**, noklusējums**TIFF (16 bitu)**):

* **TIFF (16 bitu)**: Ieteicams ģeogrāfiskās informācijas sistēmām (GIS) un zinātniskajai analīzei
* **TIFF (8-bit)**: 8-bitu TIFF
* **TIFF (32-bit, Percent)**: Peldošā punkta vērtības
* **PNG (8-bit)**: Bezzaudējumu saspiešana vizualizācijai
* **JPG (8-bit)**: Vismazākie faili, saspiešana ar zaudējumiem

Saraksts parāda, kādus attēlus projekts var ietvert: Chloros nepalielina bitu dziļumu. Attēliem, kas ir pilnībā 8-bitu, ir pieejami tikai formāti TIFF (8-bitu), PNG (8-bitu) un JPG (8-bitu), un noklusējuma formāts ir TIFF (8-bitu). Jauktā projektā katrs uzņēmums tiek eksportēts ar savu dziļumu vai zemāku. Radiance paliek 32 bitu peldošā komata formātā.

Izvades faili tiek saglabāti projekta mapē, grupēti pēc kameras un formāta: `<project>/<camera>/<format>/<Product>_Images/`. Radiance **vienmēr** tiek saglabāts kā float32 mapē `tiff32` neatkarīgi no šī iestatījuma. Eksportētie faili saglabā avota faila nosaukumu — mape identificē produktu. LATTICE multispektrālie eksporti var arī ierakstīt pikseļu fiksēšanas maskas `clamp_masks/` mapē blakus produktu mapēm. Pilnu izvades struktūru skatiet sadaļā [Apstrādes pabeigšana](finishing-the-processing.md).

{% hint style="warning" %}
**Atstarošanas koeficientu nolasīšana**: DN vērtība, kas atbilst ρ = 1,0, ir atkarīga no avota kameras — LATTICE izmanto 32768 (atzīmēts kā XMP `Chloros:PixelScale`), savukārt Survey3 izmanto 65535. Lasiet tagu, nevis pieņemiet, ka vērtība ir nemainīga. Skatīt [Izvades attēlu formāti](../output-image-formats.md).
{% endhint %}

### DAQ gaismas sensors

Šajā sadaļā ir uzskaitīti visi jūsu projektā esošie DAQ lejupvērstās gaismas faili (`.daq` / `.csv`), pa vienai rindai katram failam, norādot sensora modeli, faila nosaukumu un difuzora **vāciņa** korekciju, kas ir spēkā šim failam.

* **Vāka pārrakstīšana (visi faili)**: viens izvēles saraksts visam projektam.**Auto** (noklusējums) izmanto katra faila reģistrēto ierobežojumu — ja nekas nav reģistrēts, tiek pieņemts, ka ir saules gaisma, taču DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez ierobežojuma, un programma Chloros nevar redzēt, kas ir piemērots, tāpēc apstrādes laikā tiek parādīts brīdinājums par pieņemto ierobežojumu: izvēlieties to ierobežojumu, kas tika piemērots. Ierobežojuma izvēle pārraksta visus failus, izņemot DAQ-E-S failus, kuru difuzors ir daļa no tā kalibrācijas: neapstrādātie ieraksti tiek koriģēti ar to, un ierakstiem, kuriem jau ir piemērots ierobežojums, tiek veikta atkārtota atsauces noteikšana (ierakstītā korekcija tiek atcelta, tiek piemērots izvēlētais ierobežojums).
* Rindās tiek parādīti brīdinājumi, ja reģistrētais vāciņš bija sadales mezgla pieņemtā noklusējuma vērtība, nevis operatora apstiprināta, kā arī tad, ja izvēlētajam vāciņam nav profila šim ierīces modelim (šim failam pārrakstīšana tiek noraidīta).

„Light Sensors” cilnē veiktie DAQ ieraksti tiek automātiski pievienoti atvērtam projektam, un importētie `.daq` / `.csv` faili parādās šeit, tiklīdz tie tiek pievienoti.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Apakšējie projekta iestatījumi — indekss, eksporta formāts, DAQ sadaļa „Gaismas sensori” un projekta veidnes/mapes vadības elementi</p></figcaption></figure>### Masīva izlīdzināšana

Šī sadaļa parādās **tikai** tad, ja vismaz vienam attēlam projektā ir piemērota moduļu savstarpējā izlīdzināšanas transformācija, ko LATTICE masīvi iezīmē uzņemšanas brīdī (`Chloros:Alignment*` XMP). Tajā tiek parādīts, cik daudziem attēliem ir šīs atzīmes un kura kamera ir atsauces kamera, izmantojot šādus vadības elementus:

* **Piemērot masīva izlīdzināšanu** (noklusējums: ieslēgts): izlīdzina katru apstrādāto rezultātu (debayering / priekšskatījums / starojums / atstarošanās / indekss) atbilstoši masīva kopīgajai atsauces ģeometrijai. Izslēgts = eksportē sensora oriģinālajā ģeometrijā.
* **Apgriezt līdz kopīgajai pārklāšanās zonai** (noklusējums: ieslēgts): apgriež izlīdzinātos eksportētos attēlus līdz reģionam, kas ir kopīgs visiem moduļiem, tādējādi katrai joslai ir vienāds attēla laukums. Izslēgts saglabā pilnu sensora laukumu (melna aizpildīšana ārpus avota).
* **Pārparaugošana**:**Bilineāra (gluda, noklusējums)**,**Tuvākā (saglabā precīzas vērtības)**— bez pikseļu savstarpējas sajaukšanas, stingrai radiometriskai analīzei — vai**Kubiska (visaskarpākā)**.

***

## Iestatījumu saglabāšana un ielādēšana

### Projekta veidnes saglabāšana

Izveidojiet atkārtoti izmantojamas veidnes, lai nodrošinātu vienotu darba plūsmu:

1. Konfigurējiet visus vēlamos iestatījumus paneļā „Projekta iestatījumi”
2. Pārvietojieties uz sadaļu **„Saglabāt projekta veidni”** apakšā
3. Ievadiet aprakstošu veidnes nosaukumu (piem., „Survey3N\_RGN\_Agriculture”)
4. Noklikšķiniet uz saglabāšanas ikonas

**Priekšrocības:**

* Piemērojiet identiskus iestatījumus vairākos projektos
* Dalieties ar konfigurācijām ar komandas locekļiem
* Saglabājiet konsekvenci atkārtotās apsekošanas gadījumā

### Veidnes ielāde jaunā projektā

Izveidojot jaunu projektu:

1. Galvenajā izvēlnē izvēlieties **„Jauns projekts“**

2. Izvēlieties projekta veidni papildu veidņu izvēlnē
3. Visi veidnes iestatījumi tiek automātiski piemēroti

### Darba katalogs

Iestatījums **"Darba katalogs"** nosaka, kur pēc noklusējuma tiek izveidoti jauni projekti:

* **Noklusējuma atrašanās vieta**: `Documents\MAPIR\Chloros Projects` jūsu Windows mapē „Dokumenti” (parasti `C:\Users\[Username]\Documents\MAPIR\Chloros Projects`; tā seko mapes „Dokumenti” atrašanās vietai, ja OneDrive vai klīstošais profils to pārvieto)
* **Mainīt atrašanās vietu**: noklikšķiniet uz rediģēšanas ikonas un izvēlieties jaunu mapi
* **Koplietošana ar CLI**: `chloros-cli` izmanto to pašu noklusējuma projekta mapes iestatījumu
* **Windows sistēmā izmantojiet īsu nosaukumu**: ja vien nav ieslēgti Windows garie ceļi, projekta mape, kuras nosaukums pārsniedz aptuveni 130 rakstzīmes, tiek noraidīta pirms apstrādes — skatiet [Apstrādes uzsākšana](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer)
* **Kad mainīt**:
  * Tīkla disks komandas sadarbībai
  * Cits disks ar lielāku uzglabāšanas vietu
  * Sakārtota mapju struktūra pēc gada/klienta

***

## PPK (pēcapstrādātā kinemātika) iestatīšana

Ja izmantojat MAPIR DAQ reģistratorus ar GPS precīzai ģeolokācijai:

### Priekšnosacījumi

* MAPIR DAQ ar GPS (GNSS) moduli
* .daq žurnāla fails ar ekspozīcijas kontaktu ierakstiem
* Kamera, kas uzņemšanas sesijas laikā ir pieslēgta DAQ ekspozīcijas kontaktiem

### Konfigurācijas soļi

1. Ievietojiet .daq žurnāla failu savā projekta mapē
2. Projekta iestatījumos atzīmējiet izvēles rūtiņu **„Piemērot PPK korekcijas“**

3. Vajadzības gadījumā iestatiet**„Gaismas sensora laika zonas nobīdi”** (noklusējums: 0 UTC)
4. Piešķiriet kameras ekspozīcijas kontaktiem:
   * **Viena kamera**: automātiski piešķirta 1. kontaktam
   * **Divas kameras**: katru kameru manuāli piešķiriet pareizajam kontaktam

**Ekspozīcijas pinu piešķiršana:**

* **Ekspozīcijas pinu 1**: izvēlieties kameras modeli no nolaižamā izvēlnes
* **Ekspozīcijas pinu 2**: izvēlieties otro kameru vai “Nelietot”
* Vienu un to pašu kameru nevar piešķirt abiem piniem

{% hint style="warning" %}
**Svarīgi**: Ekspozīcijas kontaktiem jābūt pareizi piešķirtiem attiecīgajām kamerām. Nepareiza piešķiršana izraisīs nepareizus ģeolokācijas datus.
{% endhint %}

***

## Papildu scenāriji

### Projekti ar vairākām kamerām

Apstrādājot attēlus no vairākām MAPIR kamerām vienā projektā:

1. Chloros automātiski atpazīst katra kameras modeli (gan Survey3, gan LATTICE)
2. Katrai kamerai tiek piešķirti atbilstoši apstrādes profili, un katrai kamerai tiek izveidota sava izvades mapju struktūra
3. PPK: katru Survey3 kameru manuāli piešķiriet pareizajam ekspozīcijas kontaktam
4. Visas kameras izmanto vienādu eksporta formātu un indeksus

**Piemēri**: divkameru sistēma Survey3W RGN + Survey3N OCN, vai LATTICE sistēma, kurā RGB galvenā kamera tiek kombinēta ar šaurjoslas moduļiem

### Laika nobīdes vai vairāku datumu uzmērījumi

Veicot atkārtotus uzmērījumus vienā un tajā pašā teritorijā laika gaitā:

1. Izveidojiet šablonu ar saviem standarta iestatījumiem
2. Katrā sesijā izmantojiet vienotu kalibrēšanas mērķu konfigurāciju
3. Apstrādājiet katru datumu kā atsevišķu projektu
4. Lai iegūtu salīdzināmus rezultātus, izmantojiet identiskus iestatījumus
5. Eksportējiet vienā un tajā pašā formātā, lai veiktu laika analīzi

### Lieli datu kopumi

Projektiem ar lielu attēlu skaitu (500+):

* Apsvērt sadalīšanu mazākos projektos pēc datuma vai teritorijas
* Lai iegūtu ātrākus rezultātus, izmantojiet „Chloros+“ paralēlo apstrādi
* Apdomājiet iespēju izmantot CLI vai API, lai automatizētu partiju apstrādi
* Pielāgojiet minimālo atkārtotās kalibrēšanas intervālu, lai samazinātu mērķa noteikšanas laiku

***

## Iestatījumu pārbaude

Pirms apstrādes sākšanas pārbaudiet šos galvenos iestatījumus:

* [ ] Kameras modelis ir pareizi atpazīts failu pārlūkā
* [ ] Vignēšanas korekcija ir ieslēgta
* [ ] Atstarošanas kalibrēšana ir ieslēgta
* [ ] Programmai Survey3: ir importēts un pārbaudīts vismaz viens kalibrēšanas mērķa attēls; programmai „LATTICE”: ir pieejams mērķis un/vai `.daq` lejupvērstais ieraksts
* [ ] Pievienoti vēlamie multispektrālie indeksi
* [ ] Eksporta formāts atbilst jūsu darba plūsmai
* [ ] PPK iestatījumi konfigurēti (ja izmantojat .daq failus ar ekspozīcijas notikumiem)

***

## Turpmākie soļi

Kad iestatījumi ir konfigurēti:

1. **Atzīmējiet kalibrēšanas mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet apstrādes gaitu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Pilnīgu informāciju par visiem pieejamajiem iestatījumiem skatiet atsauces dokumentācijā [Projekta iestatījumi](../project-settings/project-settings.md).
