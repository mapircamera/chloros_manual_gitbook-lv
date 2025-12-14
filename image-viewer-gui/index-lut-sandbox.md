# Indekss/LUT smilšu kaste

Indekss/LUT smilšu kaste ir interaktīva darba vide Chloros attēlu skatītājā, kas ļauj eksperimentēt ar multispektrālo indeksu aprēķiniem un krāsu vizualizācijām reālajā laikā. Šis jaudīgais rīks palīdz testēt dažādus indeksus, precizēt vērtību diapazonus un izveidot publikācijai gatavas vizualizācijas, neapstrādājot atkārtoti visu datu kopu.

## Kas ir indeksa/LUT smilšu kaste?

### Mērķis

Smilšu kaste nodrošina:

* **Reāllaika indeksa aprēķināšanu** — jebkura veģetācijas indeksa tūlītēju piemērošanu
* **Interaktīvu LUT pielāgošanu** — krāsu gradiento un diapazonu precizēšanu
* **Darba plūsmas optimizāciju** — labāko iestatījumu noteikšanu pirms partijas apstrādes

### Sandbox salīdzinājumā ar projekta apstrādi

**Index/LUT Sandbox (interaktīvs):**

* Viena attēla apstrāde vienā reizē
* Tūlītēja atgriezeniskā saite
* Eksperimentāls un iteratīvs
* Nav pastāvīgu izmaiņu failos
* Ideāli piemērots izpētei un testēšanai

**Projekta apstrāde (partijas apstrāde):**

* Viss datu kopums vienā reizē
* Iepriekš konfigurēti iestatījumi
* Pastāvīgi izvades faili
* Laikietilpīgs
* Labākais risinājums, ja iestatījumi ir galīgi

{% hint style=&quot;success&quot; %}
**Labākais darba process**: izmantojiet Sandbox, lai eksperimentētu un atrastu optimālos indeksa un LUT iestatījumus, pēc tam piemērojiet šos iestatījumus projekta apstrādes laikā visam datu kopumam.
{% endhint %}

***

## Darbs ar indeksa/LUT Sandbox

### Iepriekš aprēķināto indeksu izpratne

Chloros indeksus var piemērot projekta apstrādes laikā. Lai noteiktu, kurus indeksa un LUT iestatījumus vēlaties piemērot eksportam, visvienkāršāk ir izmantot attēlu skatītāja sandbox.

Sandbox ļauj jums:

* **Piemērot jaunus indeksus un krāsu gradientus (LUT)**, lai vizualizētu datus
* **Interaktīvi pielāgot vizualizācijas iestatījumus**
* **Skatīt** jau aprēķinātus indeksa attēlus
* **Pārbaudīt** pikseļu vērtības visos tālummaiņas līmeņos

### Sandbox atvēršana

Indeksa/LUT Sandbox ir pieejama **attēlu skatītāja** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu joslas cilnē:

1. Noklikšķiniet uz attēla failu pārlūka attēlu režģī, tas atvērsies **attēlu skatītāja** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilnē
2. Noklikšķiniet uz **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilni, lai atvērtu kreiso izlēkamo sānu joslu, ja tā vēl nav atvērta

### Attēla izvēle, kam piemērot indeksu/LUT

Lai strādātu ar indeksu Image Viewer <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox:

1. **Atveriet attēlu** no galvenā attēlu režģa, uzklikšķinot uz tā
2. Tad atvērsies **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilne
3. Noklikšķiniet uz **Sluoksnis izvēlnes** (skatītāja augšējā labajā stūrī)
4. Izvēlieties slāni no izvēlnes:
   * RAW (atstarošana)

### Indeksa piemērošana attēlam

Kad attēls ir pilnekrāna režīmā un **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilnes sānu josla ir atvērta:

1. Atzīmējiet indeksa lodziņu sānu joslas augšdaļā
2. Izvēlieties kameras filtru no kreisās izvēlnes
3. Izvēlieties vēlamo indeksa formulu no labās izvēlnes
4. Velciet filtra kanāla krāsu apļus uz vietām indeksa formulā zemāk
5. Kad formula ir derīga, attēls atjaunināsies un parādīs indeksa vērtības
6. Pārvietojiet peles kursoru, lai redzētu vērtības kursora atrašanās vietā
7. Palieliniet attēlu, lai redzētu atsevišķus pikseļus un to saistītās vērtības.

Katram indeksam ir specifisks vērtību diapazons un nozīme:

#### NDVI piemērs

```
Formula: (NIR - Red) / (NIR + Red)

For Survey3W RGN camera:
NIR = 850nm band
Red = 661nm band

Result range: -1.0 to +1.0
Typical vegetation: 0.4 to 0.9
Stressed vegetation: 0.2 to 0.4
Bare soil: 0.0 to 0.2
Water: -0.1 to 0.1
```

Pilnīga indeksa formulas dokumentācija ir pieejama [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

***

## Darbs ar LUT (meklēšanas tabulām)

### Kas ir LUT?

**Meklēšanas tabula (LUT)** attēlo skaitliskās indeksa vērtības krāsās vizualizācijai:

* **Ievade**: indeksa pikseļu vērtība (piemēram, NDVI 0,65)
* **Izvade**: RGB krāsa (piemēram, spilgti zaļa)
* **Mērķis**: Padarīt modeļus vieglāk saskatāmus un interpretējamus

**Pelēktoņu un krāsu LUT:**

* Pelēktoņi: Zinātniski un neitrāli, parāda neapstrādātus datus
* Krāsu LUT: Intuitīvi un iespaidīgi, izceļ modeļus un atšķirības

{% hint style=&quot;success&quot; %}
**Vizualizācijas jauda**: krāsu LUT piemērošana pelēktoņu indeksa attēlam ievērojami atvieglo modeļu, anomāliju un interesējošo jomu identificēšanu ar vienu skatienu.
{% endhint %}

### LUT piemērošana indeksa attēlam

Kad jums ir indeksa attēls, kas parāda

1. Noklikšķiniet uz <img src="../.gitbook/assets/image.png" alt="" data-size="line"> &quot;+Pievienot LUT&quot; pogu
2. Izvēlieties krāsu gradiento
3. Pielāgojiet minimālo/maksimālo griešanas punktu
4. Pielāgojiet griešanas režīmu
5. Atzīmējiet indeksa lodziņu **Attēlu skatītāja** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilnes sānjoslā, lai piemērotu LUT

### Krāsu gradienta izvēle

**Gradienta izvēle:**

1. LUT panelī atrodiet **krāsaino gradienta joslu**.
2. Uzvediet peles kursoru uz tās, lai apskatītu pieejamos gradienta iestatījumus.
3. Izvēlieties vēlamo gradientu.
4. Attēls **nekavējoties atjauninās** ar jaunām krāsām, kad tiks atzīmēta indeksa izvēles rūtiņa.

{% hint style=&quot;success&quot; %}
**Labākā prakse**: Veģetācijas indeksiem, piemēram, NDVI, visintuitīvākais ir Red-Yellow-Green gradients, jo tas atbilst dabiskajām krāsu asociācijām (zaļš = veselīgs, dzeltens = vidējs, sarkans = stresa ietekmēts).
{% endhint %}

### Krāsu klašu pielāgošana

**Klašu kontrole** nosaka, cik daudz atsevišķu krāsu pakāpju parādās jūsu gradientā:

**Klasu skaita opcijas:**

* **2–5 klases**: ļoti plašas kategorijas, atšķirīgas zonas
* **6–10 klases**: līdzsvarotas, piemērotas klasifikācijai
* **11–20 klases**: vienmērīgi gradienti, nepārtraukts izskats
* **20+ klases**: gandrīz nepārtraukts, maksimāla vienmērība

**Kā pielāgot:**

1. LUT panelī atrodiet **krāsu paraugu kvadrātiņus zem gradienta joslas**.
2. Pielāgojiet klases skaitu, pievienojot ar + pogu.
3. Noņemiet klases skaitu, divreiz noklikšķinot uz krāsu parauga.
4. Gradients attēlā atjaunojas **reālajā laikā**.

**Ietekme uz vizualizāciju:**

* **Mazāk klases** (3-5): izveido atšķirīgas zonas, vienkāršotu klasifikāciju, vieglāk atšķirt kategorijas
* **Vidējas klases** (6-10): līdzsvarota pieeja, piemērota lielākajai daļai lietojumu
* **Vairāk klases** (15-20): vienmērīgas pārejas, detalizētas variācijas, fotogrāfiska izskats

**Kad lietot:**

* **Maz klases (3-5)**: Prezentācijas slaidi, klasifikācijas kartes, vienkārši ziņojumi
* **Vidējas klases (6-10)**: Vispārēja analīze, līdzsvarotas detaļas, standarta ziņojumi
* **Daudzas klases (15-20)**: Zinātniskā analīze, detalizēta pārbaude, publikāciju kvalitātes rezultāti

### Vērtību diapazonu precizēšana

**Vērtību diapazona kontrole** nosaka, kuras indeksa vērtības tiek attēlotas ar kurām krāsām jūsu gradiento:

**Diapazona kontrole LUT panelī:**

* **Minimālā vērtība**: krāsu skalas apakšējā robeža
* **Maksimālā vērtība**: krāsu skalas augšējā robeža
* **Vidējās vērtības**: automātiski sadalītas starp minimālo un maksimālo vērtību (balstoties uz klases skaitu)

#### Minimālo/maksimālo vērtību pielāgošana

**Lai pielāgotu vērtību diapazonus:**

1. LUT panelī atrodiet **Minimālā vērtība** un **Maksimālā vērtība** ievades laukus
2. Noklikšķiniet uz **Minimālā vērtība** lauka
3. Ierakstiet vēlamo minimālo vērtību (piemēram, `0.2`)
4. Nospiediet **Enter** vai noklikšķiniet ārpus lauka
5. Atkārtojiet šo darbību **Maksimālās vērtības** laukā (piemēram, `0.9`)
6. Vizualizācija **tiek nekavējoties atjaunināta**

{% hint style=&quot;info&quot; %}
**Automātiska mērogošana**: Kad pirmo reizi piemērojat LUT, Chloros automātiski iestata minimālo/maksimālo vērtību atbilstoši faktiskajam datu diapazonam attēlā. Pēc tam varat sašaurināt šo diapazonu, lai koncentrētos uz konkrētiem interesējošiem vērtību diapazoniem.
{% endhint %}

**NDVI diapazona pielāgošanas piemērs:**

* **Pilns diapazons**: `-1.0` līdz `1.0` (parāda visas iespējamās vērtības)
* **Uz veģetāciju orientēts**: `0.2` līdz `0.9` (izslēdzot kailu augsni un ūdeni)
* **Tikai veselīga veģetācija**: `0.5` līdz `0.9` (izcelt tikai spēcīgus augus)
* **Stresa noteikšana**: `0.2` līdz `0.5` (izcelt problēmzonas)
* **Pielāgots diapazons**: pielāgot atbilstoši novērotajiem pikseļu vērtībām

**Kāpēc pielāgot diapazonus?**

* **Palielināt kontrastu** jūsu interesējošajā zonā
* **Izslēgt neatbilstošas vērtības** (piemēram, ūdens objekti, kailā augsne)
* **Standartizēt vizualizāciju** vairākās attēlos vai datumos
* **Izcelt nelielas atšķirības** šaurā vērtību diapazonā

### Izgriezt vērtības, kas ir ārpus diapazona

Ja pikseļu vērtības ir ārpus jūsu definētā minimālā/maksimālā diapazona, jūs varat kontrolēt to attēlošanu, izmantojot **izgriešanas režīmus**.

#### **Pieejamie izgriešanas režīmu varianti:**

#### 1. Minimums un maksimums

* Pikseļi **zem minimuma** → attēlošana, izmantojot **pirmo krāsu** gradientā (piemēram, sarkanu)
* Pikseļi **virs maksimālās vērtības** → attēlošana, izmantojot **pēdējo krāsu** gradientā (piemēram, zaļā)
* **Lietošanas gadījums**: izcelt galējās vērtības, parādīt pilnu datu diapazonu ar piesātinātām krāsām robežās
* **Piemērs**: NDVI vērtības zem 0,2 visas parādās sarkanā krāsā, vērtības virs 0,9 visas parādās zaļā krāsā

#### 2. Caurspīdīgs fons

* Pikseļi **ārpus diapazona** kļūst **pilnībā caurspīdīgi**
* Tikai pikseļi **diapazonā** parāda krāsu gradientu
* **Lietošanas gadījums**: GIS pārklājums, izolējot konkrētus vērtību diapazonus, izceļot tikai interesējošās jomas
* **Piemērs**: Parādīt tikai NDVI 0,4–0,7 krāsā, viss pārējais caurspīdīgs

{% hint style=&quot;warning&quot; %}
**Caurspīdīguma ierobežojums**: Caurspīdīgi pikseļi skatītājā parādīsies kā fona krāsa. Eksportējot apstrādes laikā, caurspīdīgums tiek saglabāts PNG formātā, bet ne JPG formātā.
{% endhint %}

#### 3. Indeksa fons

* Pikseļi **ārpus diapazona** tiek attēloti **pelēkā tonī** (parādot neapstrādātas indeksa vērtības)
* Pikseļi **diapazonā** parāda **krāsu gradāciju**
* **Lietošanas gadījums**: Diskrēta izcelšana, konteksta saglabāšana, vienlaikus izceļot interesējošās zonas
* **Piemērs**: Izceliet krāsā izcelto veģetāciju (NDVI 0,3–0,5), vienlaikus parādot veselīgas zonas pelēkā krāsā

#### 4. Orijinālais fons

* Pikseļi **ārpus diapazona** parāda **oriģinālo multispektrālo attēlu**
* Pikseļi **diapazonā** parāda **krāsu gradiento**
* **Lietošanas gadījums**: visintuitīvākais — apvieno dabisko attēla kontekstu ar analītisku krāsu pārklājumu
* **Piemērs**: skatiet faktisko lauka/kultūraugu izskatu ar krāsu kodētiem stresa apgabaliem

### Pareizā apgriešanas režīma izvēle

| Apgriešanas režīms              | Labākais                                   | Vizualizācijas stils          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimums un maksimums**    | Pilna datu parādīšana, zinātniskā analīze     | Visi pikseļi ir krāsoti           |
| **Caurspīdīgs fons** | GIS pārklājumi, izolējot konkrētus diapazonus    | Krāsa diapazonā, tukša ārpus tā |
| **Indeksa fons**       | Neliels uzsvars, saglabājot datu kontekstu  | Krāsa diapazonā, pelēka ārpus tā  |
| **Oriģinālais fons**    | Ziņojumi, prezentācijas, intuitīva analīze | Krāsa diapazonā, foto ārpus tā |

### Pielāgotu LUT krāsu izveide

Lai pilnībā kontrolētu vizualizāciju, varat izveidot **pielāgotus krāsu gradientus**, rediģējot atsevišķus krāsu pārtraukumus.

**Lai izveidotu pielāgotu gradientu:**

1. LUT panelī atrodiet **gradienta priekšskatījuma joslu**
2. Meklējiet **krāsu paraugu kvadrātiņus** zem gradienta
3. **Noklikšķiniet uz krāsu pārtraukuma**, lai to atlasītu
4. Atvērsies **krāsu izvēlne**
5. Izvēlieties jaunu krāsu, izmantojot:
   * **Krāsu ritenīti**: vizuāla krāsu izvēle
   * **RGB/HSV sliderus**: precīza krāsu kontrole
   * **Hex koda ievadi**: precīzu krāsu specifikāciju (piemēram, `#FF0000` sarkanai krāsai)
6. Noklikšķiniet ārpus krāsu izvēlnes, **lai piemērotu jauno krāsu**
7. Gradientam **tiek veikti tūlītēji atjauninājumi** attēlā

**Krāsu pieturpunktu pievienošana vai noņemšana:**

* **Pievienot pārtraukumu**: noklikšķiniet uz ikonas +, lai pievienotu jaunu paraugu beigās
* **Noņemt pārtraukumu**: divreiz noklikšķiniet uz krāsu kvadrāta, lai noņemtu paraugu

**Pielāgošanas stratēģijas:**

* **Apgriezt gradientu**: apgrieziet krāsu secību, lai mainītu nozīmi (piemēram, zaļš = zems, sarkans = augsts)
* **Zīmola krāsas**: pielāgojiet savas organizācijas krāsu paleti ziņojumiem
* **Krāsu aklajiem draudzīgs**: izmantojiet oranžu-zilo vai violeto-dzelteno kombināciju
* **Drukāšanas optimizācija**: izvēlieties krāsas, kas der gan krāsu, gan pelēktoņu drukāšanai
* **Daudzpakāpju slieksnis**: izmantojiet atšķirīgas krāsas konkrētiem vērtību sliekšņiem klasificēšanai

{% hint style=&quot;info&quot; %}
**Pielāgotu gradiento saglabāšana**: Pielāgotos gradiento var saglabāt un atkārtoti izmantot. Noklikšķiniet uz saglabāšanas ikonas LUT panelī, lai saglabātu savas pielāgotās krāsu shēmas turpmākai izmantošanai.
{% endhint %}

***

## Interaktīva darba plūsma

### Reāllaika atjauninājumi

Visi LUT pielāgojumi smilšu kastē atjaunina attēlu **tūlītēji un interaktīvi**:

* **Pārslēgt slāni** → Attēls mainās uzreiz
* **Izvēlēties gradienti** → Krāsas atjauninās uzreiz
* **Pielāgot vērtību diapazonu** → Kontrasts mainās reāllaikā
* **Mainīt klases** → Gradientu gludums atjauninās uzreiz
* **Modificēt izgriešanu** → Fona attēlojums mainās uzreiz
* **Rediģēt krāsas** → Pielāgots gradients tiek piemērots nekavējoties

**Nav nepieciešama pogas &quot;Piemērot&quot;** - visas izmaiņas ir reāllaikā un interaktīvas!

{% hint style=&quot;success&quot; %}
**Reāllaika atsauksmes**: Tūlītējās vizuālās atsauksmes ļauj ātri eksperimentēt ar dažādiem iestatījumiem, līdz atrodat optimālo vizualizāciju savām analīzes vajadzībām.
{% endhint %}

### Iteratīva pilnveidošanas darba plūsma

**Tipiska LUT optimizācijas darba plūsma:**

1. **Izvēlieties indeksa slāni** (piemēram, RAW (atstarošanās))
2. **Piemērojiet indeksu** — izvēlieties kameras filtru un indeksa formulu, velciet krāsainos apļus uz atbilstošo vietu indeksa formulā
3. **Piemērojiet LUT gradientu** — sāciet ar Red-Yellow-Green iestatījumu
4. **Pārbaudiet pikseļu vērtības** — pārvietojiet kursoru, ņemiet vērā vērtību diapazonus
5. **Pielāgojiet min/max** - sašauriniet, lai koncentrētos uz veģetāciju (piemēram, no 0,2 līdz 0,9)
6. **Izvēlieties apgriešanu** - izmēģiniet &quot;Original Background&quot; (Oriģinālais fons) konteksta dēļ
7. **Precizējiet krāsas** - vajadzības gadījumā pielāgojiet gradientu, lai izceltu konkrētas detaļas
8. **Pabeidziet iestatījumu konfigurēšanu** - dokumentējiet iestatījumus un kopējiet tos uz Project Settings (Projekta iestatījumi), lai veiktu eksportēšanu

### Pikseļu vērtību pārbaude

Faktisko pikseļu vērtību izpratne ir ļoti svarīga, lai iestatītu efektīvus LUT diapazonus:

**Kā pārbaudīt vērtības:**

1. Pikseļu vērtības parādās, ja attēlam ir atzīmēta vai nu Index, vai gan Index, gan LUT **lodziņi**.
2. **Pārvietojiet kursoru** pār dažādām attēla zonām
3. **Novērojiet pikseļu vērtības**, kas parādās leģendā, kad uzvedat kursoru
4. Palieliniet attēlu, lai redzētu atsevišķus pikseļus, kas ir izcelti ar peldošu vērtību
5. **Pierakstiet** vērtību diapazonus dažādām pazīmēm:
   * **Veselīga veģetācija**: piemēram, NDVI 0,55–0,85
   * **Stresā esoša veģetācija**: piemēram, NDVI 0,30–0,50
   * **Kails augsnes slānis**: piemēram, NDVI 0,05–0,25
   * **Ūdens** (ja ir): piemēram, NDVI -0,05 līdz 0,10

**LUT diapazonu iestatīšana, izmantojot pikseļu vērtības:**

Pēc pikseļu vērtību pārbaudes atbilstoši pielāgojiet LUT minimālo/maksimālo vērtību:

**Piemērs:**

* **Novērojums**: Augsnes vērtības = 0,05–0,25, Stresa stāvoklis = 0,25–0,50, Veselīgs stāvoklis = 0,50–0,85
* **Mērķis**: Vizualizēt tikai augu veselību (izslēdzot augsni)
* **LUT iestatījumi**: Min = `0.25`, Maks = `0.85`
* **Apgriešana**: &quot;Oriģinālais fons&quot;, lai redzētu augsni dabiskā krāsā
* **Rezultāts**: Krāsu gradients attiecas tikai uz veģetāciju, augsne redzama kā oriģinālajā attēlā

{% hint style=&quot;info&quot; %}
**Dinamiskais diapazons**: dažādiem kultūraugiem, gadalaikiem un augšanas stadijām būs atšķirīgi vērtību diapazoni. Vienmēr pārbaudiet pikseļu vērtības savā konkrētajā datu kopā, pirms iestatāt LUT diapazonus.
{% endhint %}

***

## Pielāgotie indeksi (Chloros+)

### Pielāgotu indeksu formulu izveide

{% hint style=&quot;info&quot; %}
**Kur izveidot**: Pielāgotos indeksus var konfigurēt **Projekta iestatījumos** pirms apstrādes, kā arī attēlu skatītāja sandbox sānjoslā.
{% endhint %}

**Lai izveidotu pielāgotu indeksu:**

1. **Atveriet Projekta iestatījumi** (pirms apstrādes) vai attēlu skatītāja sandbox sānu joslu
2. Pāriet uz **Indeksa formulas nolaižamo izvēlni**
3. Meklējiet opciju **&quot;Pielāgots&quot;** (jābūt pieteicies ar Chloros+ licenci)
4. **Definējiet savu formulu**, izmantojot joslas mainīgos:
   * Bandu nosaukumi: `NIR`, `Red`, `Green`, `Blue`, `RedEdge` utt.
   * Operatori: `+`, `-`, `*`, `/`, `^` (eksponents)
   * Funkcijas: `sqrt()`, `abs()` utt. (ja atbalstīts)
   * Aizkavējošās zīmes: `()` darbību secībai
5. **Nosauciet savu indeksu** (piemēram, &quot;MyIndex&quot; vai &quot;CustomNDVI&quot;)
6. **Saglabājiet konfigurāciju**

**Piemēri pielāgotām formulām:**

```
Modified NDVI with offset:
(NIR - Red) / (NIR + Red + 0.5)

Simple ratio:
NIR / Red

Complex multi-band:
(NIR - Red) / (NIR + Red - Blue)

Exponential index:
(NIR / Red) ^ 2
```

{% hint style=&quot;warning&quot; %}
**Formulas validēšana**: Pārliecinieties, ka jūsu formula izmanto kamerā pieejamos diapazonus. Piemēram, RedEdge ir pieejams tikai kamerās ar RedEdge filtru.
{% endhint %}

***

## Nākamie soļi

Tagad, kad jūs saprotat indeksa/LUT smilšu kasti:

* **Piemērojiet apstrādei**: izmantojiet atrastos iestatījumus [Projekta iestatījumos](../project-settings/project-settings.md)
* **Partijas apstrāde**: piemērojiet optimizētus indeksus visiem datu kopumiem
* **Uzzināt vairāk**: izlasiet [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md)

Saistītā dokumentācija:

* [**Attēlu slāņi**](image-layers.md) — slāņu pārvaldība un vizualizācija
* [**Attēla atvēršana pilnā ekrānā**](opening-an-image-full-screen.md) — attēlu skatītāja pamati
* [**Attēlu apstrāde (GUI)**](../processing-images-gui/adding-files-to-a-project.md) — pilna apstrādes darba plūsma
