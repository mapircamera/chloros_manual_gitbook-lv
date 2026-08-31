# Projekta iestatījumu pielāgošana

Pirms attēlu apstrādes ir svarīgi konfigurēt projekta iestatījumus atbilstoši jūsu darba plūsmas prasībām. Panelis „Projekta iestatījumi“ (<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">) nodrošina visaptverošu kontroli pār kalibrēšanu, apstrādes opcijām, multispektrālajiem indeksiem un eksporta formātiem.

## Piekļuve projekta iestatījumiem

1. Atveriet savu projektu programmā Chloros
2. Noklikšķiniet uz ikonas **Projekta iestatījumi** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> kreisajā sānjoslā
3. Paneļā „Projekta iestatījumi” tiek parādītas visas konfigurācijas opcijas

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Projekta iestatījumu panelis — attēlojums, mērķa noteikšana un apstrāde</p></figcaption></figure>{% hint style="info" %}
**Iestatījumi tiek saglabāti automātiski** kopā ar jūsu projektu. Kad atkal atverat projektu, visi iestatījumi tiek atjaunoti.
{% endhint %}

***

## Ātra konfigurēšana tipiskiem darba procesiem

### Noklusējuma iestatījumi (ieteicami lielākajai daļai lietotāju)

Noklusējuma iestatījumi labi darbojas tipiskos Survey3 un LATTICE darba procesos:

* ✅ **Vignētes korekcija**: Ieslēgta
* ✅ **Atstarošanas kalibrēšana / baltā balanss**: Ieslēgts (izmanto MAPIR mērķus un/vai DAQ gaismas sensora datus)
* ✅ **Debayer metode**: Standarta (ātra, vidēja kvalitāte)
* ✅ **Eksporta formāts**: TIFF (16 bitu)
* ✅ **Visi eksporta rezultāti**: Iespējots (LATTICE automātiski saglabā izkliedētos datus kā debayered, priekšskatījumu, starojuma intensitāti un atstarošanas koeficientu)

Vienkārši importējiet savus attēlus un sāciet apstrādi, izmantojot šos noklusējumus.

***

## Projekta iestatījumu pārskats

Projekta iestatījumu panelis ir sadalīts zemāk minētajās sadaļās. Divas papildu sadaļas — **DAQ gaismas sensors**un**Masīva izlīdzināšana** — parādās automātiski, ja jūsu projektā ir attiecīgie faili. Pilnīgu dokumentāciju skatiet sadaļā [Projekta iestatījumi](../project-settings/project-settings.md).

### Attēlojums

* **Attēlu sīktēlu izšķirtspēja**: attēlu režģa sīktēlu izšķirtspēja. Iespējas:**Noklusējums (512 pikseļi)**,**1024 pikseļi**,**2048 pikseļi**,**Pilna izšķirtspēja**. Tikai attēlošanai — nekad neietekmē apstrādi. Augstākas vērtības izskatās asākas, palielinot attēlu, bet ielādējas lēnāk.

### Mērķa atpazīšana

Nosaka, kā Chloros identificē kalibrēšanas mērķus jūsu attēlos.

**Galvenie iestatījumi:*** **Minimālā kalibrēšanas parauga platība (px)**: Izmēra slieksnis mērķu atpazīšanai (noklusējums:**25**, diapazons 0–10000)
* **Minimālā mērķu grupēšana (0–100)**: Līdzības slieksnis mērķu reģionu grupēšanai (noklusējums:**60**)**Kad jāpielāgo:**

* Palieliniet parauga laukumu, ja rodas kļūdainas atklāšanas
* Samaziniet to, ja mērķi netiek atklāti
* Pielāgojiet grupēšanu, ja mērķi tiek sadalīti vairākās atklāšanās

{% hint style="info" %}
Šie iestatījumi ir izslēgti, ja ir atspējota **atstarošanas kalibrēšana / baltā balanss** — ja tā ir atspējota, mērķa noteikšana vispār nedarbojas.
{% endhint %}

### Apstrāde

Galvenās attēla apstrādes un kalibrēšanas opcijas.

**Galvenie iestatījumi:*** **Vignette korekcija**: Kompensē objektīva tumšošanos malās ✅ Ieteicams
* **Atstarošanas kalibrēšana / baltā balanss**: Kalibrē attēlus, izmantojot atklātos mērķus (Survey3) un/vai DAQ gaismas sensora datus (LATTICE) ✅ Ieteicams
* **Debayer metode**: Algoritms RAW failu konvertēšanai 3-kanālu multispektrālajā formātā
* **Minimālais atkārtotās kalibrēšanas intervāls**: Minimālais laiks sekundēs starp kalibrēšanas mērķa izmantošanu (noklusējums:**0** = izmantot visus, diapazons 0–3600)**Nekalibrēti rezerves produkti:**Ja attēlu nav iespējams kalibrēt pēc atstarojuma (nav pieejams mērķis vai kalibrēšana ir atspējota), tas tiek eksportēts kā viens no diviem rezerves produktiem —**katrā izpildes ciklā pastāv tieši viens no šiem diviem**, ko izvēlas ar slēdzi „Vignette korekcija”:

* **Eksportēt sensora reakciju**: ieraksta `Sensor_Response_Images` — izmanto, ja vinjetes korekcija ir**izslēgta*** **Eksportēt ar vignetēšanas korekciju**: ieraksta `Vignette_Corrected_Images` — izmanto, ja vignetēšanas korekcija ir**ieslēgta**Izvēles rūtiņa, kas netiek izmantota, ir izslēgta. Atceļot atzīmi no aktīvās rūtiņas, šī faila ierakstīšana tiek pilnībā apturēta.**LATTICE eksporta produkti** (parādās katram projektam; tie attiecas uz LATTICE uzņēmumiem):

* **Eksportēt bez bayera**: lineārs attēls bez bayera (`Debayered_Images`). Attiecas uz RGB un multispektrāliem moduļiem.
* **Eksportēt priekšskatījumu**: ekrāna priekšskatījums (`Preview_Images`). RGB = baltā balanss (DAQ-apgaismojums, ja pieejams, citādi pelēkā pasaule) + gamma; multispektrāls = viltus krāsu izstiepšana.
* **Eksportēt starojuma intensitāti**: float32 spektrālā starojuma intensitāte (`Radiance_Images`, W/m²/sr/nm). Tikai multispektrālie moduļi — neattiecas uz RGB paraugiem.
* ****Eksportēt atstarošanas koeficientu**: uint16 atstarošanas koeficients (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1,0), ja `.daq` lejupvērstais mērījums vai kadrā esošais mērķis aptver kadru. Tikai multispektrālie moduļi.

Visi četri ir **pēc noklusējuma ieslēgti**— viens importēts LATTICE neapstrādāts kadrs tiek sadalīts pa visiem iespējotajiem un piemērojamajiem produktiem vienā apstrādes ciklā. Izvēles rūtiņa**Eksportēt atstarojumu** ir nedzēšama, ja atstarojuma kalibrēšana ir izslēgta. Iestatījumi, kurus neļauj izmantot augšējā līmeņa slēdzis, vienmēr ir izslēgti, un blakus tiem parādās rīkjosla ar norādi uz slēdzi, kas jāmaina.**Papildu iestatījumi:*** **Gaismas sensora laika zonas nobīde**: Stundas no UTC gaismas sensora laika saskaņošanai (noklusējums: 0, diapazons no −12 līdz +12)
* **Piemērot PPK korekcijas**: izmanto GPS/ekspozīcijas pinu datus no `.daq` failiem (noklusējums: izslēgts)
* **Ekspozīcijas pini 1/2**: piešķir kameras ekspozīcijas piniem divkameru konfigurācijām

{% hint style="info" %}
**LATTICE ieejas līmenis ir automātisks.** LATTICE uzņēmumos apstrādes līmenis tiek saglabāts XMP metadatos, un apstrāde vienmēr sākas ar neapstrādātu kadru — lietotāja saskarnē nav nekas jākonfigurē. (Atzīme CLI `--input-level` ir paredzēta pieredzējušiem lietotājiem, lai pārrakstītu iestatījumus uzņēmumiem ar zaudētiem metadatiem; skatiet [CLI atsauci](../reference/cli-reference.md).)
{% endhint %}

### Debayeringa metode

Pašlaik Chloros piedāvājam 2 debayeringa metodes:

#### Standarta (ātra, vidēja kvalitāte)

Standarta debayeringa metode darbojas ātri, taču rada krāsu troksni, kā rezultātā attēli ir mazāk precīzi un trokšņaināki.

#### Tekstūras ņemšana vērā (lēna, visaugstākā kvalitāte) \[Tikai Chloros+]

Metode „Tekstūras ņemšana vērā” izmanto augstas kvalitātes malu atpazīšanas debayeringu, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayeringa radītos trokšņus. Lai modelis darbotos, ir nepieciešama GPU atmiņa (VRAM): ja **VRAM ir 7 GB vai vairāk**, tas var vienlaikus apstrādāt vairākus attēlus; ja VRAM ir mazāk par 7 GB, tas apstrādā vienu attēlu pēc otra (ievērojami lēnāk). Skatīt [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**LATTICE uzņēmumos vienmēr tiek izmantota standarta demosaic metode.** Nav LATTICE apmācīta Texture Aware modeļa, tāpēc šī opcija nav pieejama LATTICE attēliem — tomēr to joprojām var izmantot Survey3 attēliem tajā pašā projektā.
{% endhint %}

### Indekss (daudzspektrālie indeksi)

Konfigurējiet, kurus veģetācijas indeksus aprēķināt un eksportēt. Lietotāja saskarnes nolaižamajā izvēlnē ir pieejamas **27 iepriekš definētas indeksu formulas**.**Kā pievienot indeksus:**

1. Noklikšķiniet uz pogas**„Pievienot indeksu””**

2. Izvēlieties indeksu no izvēlnes (NDVI, NDRE, GNDVI utt.)
3. Konfigurējiet vizualizācijas iestatījumus (LUT krāsas, vērtību diapazoni)
4. Pēc nepieciešamības pievienojiet vairākus indeksus

**Populāri indeksi:*** **NDVI**: Vispārējais veģetācijas veselības stāvoklis (visbiežāk izmantotais)
* **NDRE**: Agrīna stresa noteikšana kopā ar RedEdge
* **GNDVI**: Jutīgs pret hlorofila koncentrāciju
* **OSAVI**: labi darbojas ar redzamu augsni
* **EVI**: reģioni ar augstu lapu platības indeksu (LAI)**Pielāgotas formulas:**

* Izveidojiet pielāgotas multispektrālo indeksu formulas, izmantojot joslu aprēķinus visos attēla kanālos
* Saglabājiet pielāgotās formulas atkārtotai izmantošanai
* Pielāgotās formulas ir Chloros+ funkcija; to pieejamība ir atkarīga no jūsu plāna līmeņa

Lai apskatītu visus pieejamos indeksus un formulas — tostarp to, kuri nosaukumi ir pieejami tikai lietotāja saskarnē (GUI) un kuri darbojas arī programmās CLI/SDK — skatiet [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

### Eksportēšana

Nosaka izejas faila formātu.

**Pieejamie formāti**(iestatījums:**Kalibrēta attēla formāts**, noklusējums**TIFF (16 bitu)**):

* **TIFF (16-bit)**: Ieteicams ģeogrāfiskās informācijas sistēmām (GIS) un zinātniskajai analīzei
* **TIFF (32-bit, procentos)**: Peldošā punkta vērtības
* **PNG (8 bitu)**: bezzaudējumu saspiešana vizualizācijai
* **JPG (8 bitu)**: vismazākie faili, saspiešana ar zaudējumiem

Izvades faili tiek saglabāti projekta mapē, sagrupēti pēc kameras un formāta: `<project>/<camera>/<format>/<Product>_Images/`. Radiance **vienmēr** tiek saglabāts kā float32 `tiff32` mapē neatkarīgi no šī iestatījuma. Eksportētie faili saglabā avota faila nosaukumu — mape identificē rezultātu. Pilnu izvades struktūru skatiet sadaļā [Apstrādes pabeigšana](finishing-the-processing.md).

{% hint style="warning" %}
**Atstarošanas koeficientu nolasīšana**: DN, kas nozīmē ρ = 1,0, ir atkarīgs no avota kameras — LATTICE izmanto 32768 (atzīmēts kā XMP `Chloros:PixelScale`), Survey3 izmanto 65535. Lasiet tagu, nevis pieņemiet, ka vērtība ir nemainīga. Skatīt [Izvades attēlu formāti](../output-image-formats.md).
{% endhint %}

### DAQ gaismas sensors

Šajā sadaļā ir uzskaitīti visi jūsu projektā esošie DAQ lejupvērstās gaismas faili (`.daq` / `.csv`), pa vienai rindai katram failam, norādot sensora modeli, faila nosaukumu un difuzora **vāciņa** korekciju, kas attiecas uz šo failu.

* **Vāka pārrakstīšana (visi faili)**: viena izvēlne visam projektam.**Auto**(noklusējums) izmanto katra faila reģistrēto vāku — ja nekas nav reģistrēts, tiek pieņemts, ka ir saules gaisma, jo visi MAPIR DAQ tiek piegādāti ar saules gaismas korektoru.**Cap**izvēle pārraksta visus failus: neapstrādātie ieraksti tiek koriģēti ar to, un ierakstiem, kuriem jau ir piešķirts**cap**, tiek pārrēķināti atsauces (ierakstītā korekcija tiek atcelta, tiek piemērots izvēlētais**cap**).
* Rindās parādās brīdinājums, ja reģistrētais ierobežojums bija centrmezgla pieņemtā noklusējuma vērtība, nevis operatora apstiprināta, kā arī tad, ja izvēlētajam ierobežojumam nav profila šim ierīces modelim (pārrakstīšana šim failam tiek noraidīta).

„Light Sensors” cilnē veiktie DAQ ieraksti tiek automātiski pievienoti atvērtam projektam, un importētie `.daq` / `.csv` faili parādās šeit, tiklīdz tie tiek pievienoti.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Apakšējie projekta iestatījumi — indekss, eksporta formāts, DAQ sadaļa „Gaismas sensori” un projekta veidnes/mapes vadības elementi</p></figcaption></figure>### Masīvu izlīdzināšana

Šī sadaļa parādās **tikai** tad, ja vismaz vienam attēlam projektā ir piemērota moduļu savstarpējā izlīdzināšanas transformācija, ko LATTICE masīvi piemēro uzņemšanas brīdī (`Chloros:Alignment*` XMP). Tajā tiek parādīts, cik daudziem attēliem ir šīs atzīmes un kura kamera ir atsauces kamera, izmantojot šādus vadības elementus:

* **Piemērot masīva izlīdzināšanu** (noklusējums: ieslēgts): izlīdzina katru apstrādāto rezultātu (debayering / priekšskatījums / starojums / atstarošanās / indekss) atbilstoši masīva kopīgajai atsauces ģeometrijai. Izslēgts = eksportē sensora oriģinālajā ģeometrijā.
* **Apgriezt līdz kopīgajai pārklāšanās zonai** (noklusējums: ieslēgts): apgriež izlīdzinātos eksportētos attēlus līdz reģionam, kas ir kopīgs visiem moduļiem, tādējādi katrai joslai ir vienāds laukums. Izslēgts saglabā pilnu sensora laukumu (melns aizpildījums ārpus avota).
* **Pārparaugošana**:**Bilineārā (gluda, noklusējums)**,**Tuvākā (saglabā precīzas vērtības)**— bez pikseļu savstarpējās sajaukšanas, stingrai radiometriskai analīzei — vai**Kubiskā (asākā)**.***

## Iestatījumu saglabāšana un ielādēšana

### Projekta veidnes saglabāšana

Izveidojiet atkārtoti izmantojamas veidnes, lai nodrošinātu vienotu darba plūsmu:

1. Konfigurējiet visus vēlamos iestatījumus paneļā „Projekta iestatījumi”
2. Pārvietojieties uz sadaļu **„Saglabāt projekta veidni”** apakšā
3. Ievadiet aprakstošu veidnes nosaukumu (piem., „Survey3N\_RGN\_Agriculture”)
4. Noklikšķiniet uz saglabāšanas ikonas

**Priekšrocības:**

* Piemērojiet identiskus iestatījumus vairākos projektos
* Dalīties ar konfigurācijām ar komandas locekļiem
* Nodrošināt konsekvenci atkārtotās aptaujās

### Veidnes ielāde jaunā projektā

Izveidojot jaunu projektu:

1. Galvenajā izvēlnē izvēlieties **„Jauns projekts”**

2. Izvēlieties projekta veidni papildu veidņu izvēlnē
3. Visi iestatījumi no veidnes tiek automātiski piemēroti

### Darba katalogs

**&quot;Darba katalogs&quot;** iestatījums nosaka, kur pēc noklusējuma tiek izveidoti jauni projekti:

* **Noklusējuma atrašanās vieta**: `C:\Users\[Username]\Chloros Projects`
* **Mainīt atrašanās vietu**: noklikšķiniet uz rediģēšanas ikonas un izvēlieties jaunu mapi
* **Koplietošana ar CLI**: `chloros-cli` izmanto to pašu noklusējuma projekta mapes iestatījumu
* **Kad mainīt**:
  * Tīkla disks komandas sadarbībai
  * Atsevišķs disks ar lielāku uzglabāšanas vietu
  * Sakārtota mapju struktūra pēc gada/klienta

***

## PPK (pēcapstrādātā kinemātika) iestatījumi

Ja izmantojat MAPIR DAQ reģistratorus ar GPS precīzai ģeolokācijai:

### Priekšnosacījumi

* MAPIR DAQ ar GPS (GNSS) moduli
* .daq žurnāla fails ar ekspozīcijas kontaktu ierakstiem
* Kamera, kas uzņemšanas sesijas laikā ir pieslēgta DAQ ekspozīcijas kontaktiem

### Konfigurācijas soļi

1. Ievietojiet .daq reģistrācijas failu savā projekta mapē
2. Projekta iestatījumos atzīmējiet izvēles rūtiņu **&quot;Piemērot PPK korekcijas&quot;**

3. Vajadzības gadījumā iestatiet**&quot;Gaismas sensora laika zonas nobīdi&quot;** (noklusējums: 0 UTC)
4. Piešķiriet kameras ekspozīcijas kontaktiem:
   * **Viena kamera**: automātiski piešķirta 1. kontaktam
   * **Divas kameras**: katru kameru manuāli piešķiriet pareizajam kontaktam**Ekspozīcijas kontaktu piešķiršana:*** **Ekspozīcijas kontakts 1**: Izvēlieties kameras modeli no nolaižamā izvēlnes
* **Ekspozīcijas kontakts 2**: Izvēlieties otro kameru vai „Nelietot”
* Vienu un to pašu kameru nevar piešķirt abiem kontaktiem

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
4. Visas kameras izmanto vienu un to pašu eksporta formātu un indeksus

**Piemēri**: Survey3W RGN + Survey3N OCN divkameru sistēma, vai arī LATTICE sistēma, kurā RGB galvenā kamera tiek apvienota ar šaurjoslas moduļiem

### Laika nobīdes vai vairāku datumu pētījumi

Lai laika gaitā veiktu atkārtotus pētījumus vienā un tajā pašā teritorijā:

1. Izveidojiet šablonu ar saviem standarta iestatījumiem
2. Katrā sesijā izmantojiet vienādu kalibrēšanas mērķa konfigurāciju
3. Apstrādājiet katru datumu kā atsevišķu projektu
4. Lai iegūtu salīdzināmus rezultātus, izmantojiet identiskus iestatījumus
5. Eksportējiet vienā formātā, lai veiktu laika analīzi

### Lieli datu kopumi

Projektiem ar daudziem attēliem (500+):

* Apdomājiet iespēju sadalīt projektus mazākos projektos pēc datuma vai teritorijas
* Lai iegūtu ātrākus rezultātus, izmantojiet Chloros+ paralēlo apstrādi
* Apdomājiet CLI vai API izmantošanu partiju automatizācijai
* Pielāgojiet minimālo pārkalibrēšanas intervālu, lai samazinātu mērķa atklāšanas laiku

***

## Iestatījumu pārbaude

Pirms apstrādes sākšanas pārbaudiet šos galvenos iestatījumus:

* [ ] Kameras modelis pareizi atpazīts failu pārlūkā
* [ ] Vignēšanas korekcija ieslēgta
* [ ] Atstarošanas kalibrēšana ieslēgta
* [ ] Survey3 gadījumā: ir importēts un pārbaudīts vismaz viens kalibrēšanas mērķa attēls; LATTICE gadījumā: ir pieejams mērķis un/vai `.daq` lejupvērstais ieraksts
* [ ] Pievienoti vēlamie multispektrālie indeksi
* [ ] Eksporta formāts atbilst jūsu darba plūsmai
* [ ] Konfigurēti PPK iestatījumi (ja izmantojat .daq failus ar ekspozīcijas notikumiem)

***

## Turpmākie soļi

Kad iestatījumi ir konfigurēti:

1. **Atzīmējiet kalibrēšanas mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)
3. **Uzraugiet apstrādes gaitu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)

Pilnīgu informāciju par visiem pieejamajiem iestatījumiem skatiet atsauces dokumentācijā [Projekta iestatījumi](../project-settings/project-settings.md).
