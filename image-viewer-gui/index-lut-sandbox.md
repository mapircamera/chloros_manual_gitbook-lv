# Indeksu/LUT izmēģinājumu vide

Indeksu/LUT izmēģinājumu vide ir interaktīva darba vide programmā Chloros Image Viewer, kas ļauj reāllaikā eksperimentēt ar multispektrālo indeksu aprēķiniem un krāsu vizualizācijām. Šis jaudīgais rīks palīdz pārbaudīt dažādus indeksus, precizēt vērtību diapazonus un izveidot publicēšanai gatavas vizualizācijas, nepārstrādājot visu datu kopu.

## Kas ir indeksa/LUT smilšu kaste?

### Mērķis

Smilšu kaste nodrošina:

* **Indeksa aprēķināšanu reāllaikā** — nekavējoties piemērojiet jebkuru veģetācijas indeksu
* **Interaktīvu LUT pielāgošanu** — precīzi noregulējiet krāsu gradientus un diapazonus
* **Darba plūsmas optimizāciju** — nosakiet labākos iestatījumus pirms partijas apstrādes

### Sandbox salīdzinājumā ar projekta apstrādi

**Indeksu/LUT Sandbox (interaktīvs):**

* Vienu attēlu vienlaikus
* Tūlītēja atgriezeniskā saite
* Eksperimentāls un iteratīvs
* Nav pastāvīgu izmaiņu failos
* Ideāli piemērots izpētei un testēšanai

**Projekta apstrāde (partija):**

* Viss datu kopums vienlaikus
* Iepriekš konfigurēti iestatījumi
* Pastāvīgi izvades faili
* Laikietilpīgs
* Vislabākais, ja iestatījumi ir galīgi apstiprināti

{% hint style="success" %}
**Labākais darba process**: Izmantojiet Sandbox, lai eksperimentētu un atrastu optimālos indeksa un LUT iestatījumus, pēc tam piemērojiet šos iestatījumus projekta apstrādes laikā visam datu kopumam.
{% endhint %}

***

## Darbs ar indeksa/LUT Sandbox

### Iepriekš aprēķināto indeksu izpratne

Chloros indeksus var piemērot projekta apstrādes laikā. Lai noteiktu, kurus indeksa un LUT iestatījumus vēlaties piemērot eksportam, visvienkāršāk ir izmantot attēlu skatītāja Sandbox.

Sandbox ļauj jums:

* **Piemērot jaunus indeksus un krāsu gradientus (LUT)**, lai vizualizētu datus
* **Interaktīvi pielāgot vizualizācijas iestatījumus*** **Skatīt** jau aprēķinātus indeksu attēlus
* **Pārbaudīt** pikseļu vērtības visos tālummaiņas līmeņos

### Sandbox atvēršana

Index/LUT Sandbox ir pieejams **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu joslā:

1. Noklikšķiniet uz attēla failu pārlūka attēlu režģī, tas atvērsies **Attēlu skatītāja** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilnē
2. Noklikšķiniet uz **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> , lai atvērtu kreiso izvelkamo sānu joslu, ja tā vēl nav atvērta

### Attēla izvēle, kam piemērot indeksu/LUT

Lai strādātu ar indeksu attēlu skatītājā <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox:

1. **Atveriet attēlu** no galvenā attēlu rāsta, uzklikšķinot uz tā
2. Tad atvērsies **Image Viewer** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilne
3. Noklikšķiniet uz **slāņu izvēlnes** (skatītāja augšējā labajā stūrī)
4. Izvēlieties slāni no izvēlnes:
   * RAW (atstarošana)

### Indeksa piemērošana attēlam

Kad attēls ir pilnekrāna režīmā un **Attēlu skatītāja** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu josla ir atvērta:

1. Atzīmējiet indeksa lodziņu sānu joslas augšdaļā
2. Izvēlieties savas kameras filtru no kreisās izvēlnes
3. Izvēlieties vēlamo indeksa formulu no labās izvēlnes
4. Velciet filtra kanāla krāsu apļus uz vietām indeksa formulā zemāk
5. Kad formula ir derīga, attēls atjaunināsies un parādīs indeksa vērtības
6. Pārvietojiet peles kursoru, lai redzētu vērtības kursora atrašanās vietā
7. Palieliniet attēlu, lai redzētu atsevišķus pikseļus un ar tiem saistītās vērtības

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

Pilnīga indeksa formulas dokumentācija ir pieejama sadaļā [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md).

***

## Darbs ar LUT (meklēšanas tabulām)

### Kas ir LUT?

**Meklēšanas tabula (LUT)** vizualizācijas nolūkā saista skaitliskās indeksa vērtības ar krāsām:

* **Ievade**: Indeksa pikseļa vērtība (piem., NDVI 0,65)
* **Izvade**: RGB krāsa (piem., spilgti zaļa)
* **Mērķis**: Padarīt modeļus vieglāk saskatāmus un interpretējamus**Pelēktoņu skala pret krāsu LUT:**

* Pelēktoņu skala: Zinātniska un neitrāla, parāda neapstrādātus datus
* Krāsu LUT: Intuitīva un iespaidīga, izceļ modeļus un atšķirības

{% hint style="success" %}
**Vizualizācijas spēks**: Krāsu LUT piemērošana pelēktoņu indeksa attēlam ievērojami atvieglo modeļu, anomāliju un interesējošo zonu identificēšanu ar vienu skatienu.
{% endhint %}

### LUT piemērošana indeksa attēlam

Kad jums ir indeksa attēls, kas parāda

1. Noklikšķiniet uz <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> &quot;+Pievienot LUT&quot; pogu
2. Izvēlieties krāsu gradientu
3. Pielāgojiet minimālo/maksimālo atskaites punktu
4. Pielāgojiet atskaites režīmu
5. Atzīmējiet indeksa lodziņu **Attēlu skatītājā** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sānu joslā, lai piemērotu LUT

### Krāsu gradienta izvēle

**Gradienta izvēle:**

1. LUT panelī atrodiet**krāsu gradienta joslu**

2. Novietojiet peles kursoru virs tās, lai apskatītu pieejamos gradienta iestatījumus
3. Izvēlieties vēlamo gradientu
4. Attēls **tiek nekavējoties atjaunināts** ar jaunām krāsām, kad ir atzīmēta izvēles rūtiņa „Indekss”

{% hint style="success" %}
**Labākā prakse**: Veģetācijas indeksiem, piemēram, NDVI, Red-Yellow-Green gradients ir visintuitīvākais, jo tas atbilst dabiskajām krāsu asociācijām (zaļš = veselīgs, dzeltens = vidējs, sarkans = stresa stāvoklī).
{% endhint %}

### Krāsu klases pielāgošana

**Klases kontrole**nosaka, cik daudz atsevišķu krāsu pakāpju parādās jūsu gradientā:**Klasu skaita opcijas:*** **2–5 klases**: Ļoti plašas kategorijas, izteiktas zonas
* **6–10 klases**: Līdzsvarots, piemērots klasifikācijai
* **11–20 klases**: Gludi gradienti, nepārtraukts izskats
* **20+ klases**: Gandrīz nepārtraukts, maksimāla gluduma**Kā pielāgot:**

1. LUT panelī atrodiet**krāsu paraugu kvadrātiņus zem gradienta joslas**

2. Pielāgojiet klašu skaitu, pievienojot ar pogu +
3. Samaziniet klašu skaitu, divreiz noklikšķinot uz krāsu parauga
4. Gradients attēlā atjaunojas **reālajā laikā**

**Ietekme uz vizualizāciju:*** **Mazāk klases** (3–5): rada atšķirīgas zonas, vienkāršota klasifikācija, vieglāk atšķirt kategorijas
* **Vidējs klases skaits** (6–10): līdzsvarota pieeja, piemērota lielākajai daļai lietojumu
* **Lielāks klases skaits** (15–20): vienmērīgi pārejas, detalizētas variācijas, fotogrāfiska izskata**Kad izmantot:*** **Maz klases (3–5)**: Prezentācijas slaidi, klasifikācijas kartes, vienkārši ziņojumi
* **Vidējs klases skaits (6–10)**: Vispārēja analīze, līdzsvarota detalizācija, standarta ziņojumi
* **Daudz klases (15–20)**: Zinātniskā analīze, detalizēta pārbaude, publikāciju kvalitātes rezultāti

### Vērtību diapazonu precizēšana

**Vērtību diapazonu vadības elementi**nosaka, kuras indeksa vērtības tiek saistītas ar kurām krāsām jūsu gradientā:**Diapazona vadības elementi LUT panelī:*** **Minimālā vērtība**: krāsu skalas apakšējā robeža
* **Maksimālā vērtība**: krāsu skalas augšējā robeža
* **Vidējās vērtības**: automātiski sadalītas starp minimālo un maksimālo vērtību (balstoties uz klases skaitu)

#### Minimālo/maksimālo vērtību pielāgošana

**Lai pielāgotu vērtību diapazonus:**

1. LUT panelī atrodiet ievades laukus**Minimālā vērtība**un**Maksimālā vērtība**

2. Noklikšķiniet uz lauka**Minimālā vērtība**

3. Ierakstiet vēlamo minimālo vērtību (piem., `0.2`)
4. Nospiediet **Enter** vai noklikšķiniet ārpus lauka
5. Atkārtojiet to pašu ar **Maksimālā vērtība** lauku (piem., `0.9`)
6. Vizualizācija **tiek atjaunināta nekavējoties**{% hint style="info" %}**Automātiskā mērogošana**: Kad pirmo reizi piemērojat LUT, Chloros automātiski iestata minimālo/maksimālo vērtību atbilstoši attēla faktiskajam datu diapazonam. Pēc tam varat sašaurināt šo diapazonu, lai koncentrētos uz konkrētiem interesējošiem vērtību diapazoniem.
{% endhint %}

**Piemērs NDVI diapazona pielāgojumiem:*** **Pilns diapazons**: no `-1.0` līdz `1.0` (parāda visas iespējamās vērtības)
* **Koncentrēšanās uz veģetāciju**: no `0.2` līdz `0.9` (izslēdz kailu augsni un ūdeni)
* **Tikai veselīga veģetācija**: no `0.5` līdz `0.9` (izcelt tikai spēcīgus augus)
* **Stresa noteikšana**: no `0.2` līdz `0.5` (uzsvērt problemātiskās zonas)
* **Pielāgots diapazons**: pielāgojiet, pamatojoties uz novērotajām pikseļu vērtībām**Kāpēc pielāgot diapazonus?*** **Palieliniet kontrastu** jūsu interesējošajā zonā
* **Izslēdziet neatbilstošas vērtības** (piem., ūdens objektus, kailu augsni)
* **Standartizējiet vizualizāciju** vairākos attēlos vai datumos
* **Uzsvērtiet smalkas atšķirības** šaurā vērtību diapazonā

### Diapazona ārpusē esošo vērtību nogriešana

Ja pikseļu vērtības atrodas ārpus jūsu definētā minimālā/maksimālā diapazona, jūs varat kontrolēt to attēlošanu, izmantojot **nogriešanas režīmus**.

#### **Pieejamās nogriešanas režīma opcijas:**

#### 1. Minimums un maksimums

* Pikseļi **zem minimuma**→ attēlojiet, izmantojot**pirmo krāsu** gradientā (piem., sarkanu)
* Pikseļi **virs maksimuma**→ attēlo, izmantojot**pēdējo krāsu** gradientā (piem., zaļo)
* **Lietošanas gadījums**: izcelt galējības, parādīt pilnu datu diapazonu ar piesātinātām krāsām robežās
* **Piemērs**: NDVI vērtības zem 0,2 visas parādās sarkanas, vērtības virs 0,9 visas parādās zaļas

#### 2. Caurspīdīgs fons

* Pikseļi **ār diapazonu**kļūst**pilnībā caurspīdīgi*** Tikai pikseļi **diapazonā** parāda krāsu gradientu
* **Lietošanas gadījums**: GIS pārklājums, izdalot konkrētus vērtību diapazonus, izceļot tikai interesējošās zonas
* **Piemērs**: Parādīt krāsā tikai NDVI 0,4–0,7, pārējo atstāt caurspīdīgu

{% hint style="warning" %}
**Caurspīdīguma ierobežojums**: Caurspīdīgie pikseļi skatītājā tiks attēloti kā fona krāsa. Eksportējot apstrādes laikā, caurspīdīgums tiek saglabāts PNG formātā, bet ne JPG formātā.
{% endhint %}

#### 3. Indeksa fons

* Pikseļi **ārpus diapazona**tiek attēloti**pelēktoņu skalā** (parādot neapstrādātas indeksa vērtības)
* Pikseļi **diapazonā**parāda**krāsu gradientu*** **Lietošanas gadījums**: Diskrēta izcelšana, saglabājot kontekstu, vienlaikus izceļot interesējošās zonas
* **Piemērs**: Izceliet krāsā stresa skarto veģetāciju (NDVI 0,3–0,5), vienlaikus attēlojot veselīgās zonas pelēkā krāsā

#### 4. Orijinālais fons

* Pikseļi **ārpus diapazona**attēlo**oriģinālo multispektrālo attēlu*** Pikseļi **diapazonā**attēlo**krāsu gradientu*** **Lietošanas gadījums**: Vissintuitīvākais — apvieno dabisko attēla kontekstu ar analītisku krāsu pārklājumu
* **Piemērs**: Redziet faktisko lauka/kultūraugu izskatu ar pārklātu krāsu kodētu stresa zonu

### Pareizā apgriešanas režīma izvēle

| Apgriešanas režīms              | Vispiemērotākais                                   | Vizualizācijas stils          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimums un maksimums**    | Pilna datu attēlošana, zinātniskā analīze     | Visi pikseļi iekrāsoti           |
| **Caurspīdīgs fons** | GIS pārklājumi, konkrētu diapazonu izdalīšana    | Krāsa diapazonā, ārpus tā tukšs |
| **Indeksa fons**       | Diskrēta izcelšana, saglabājot datu kontekstu  | Krāsa diapazonā, pelēks ārpus tā  |
| **Oriģinālais fons**    | Ziņojumi, prezentācijas, intuitīva analīze | Krāsa diapazonā, foto ārpus tā |

### Pielāgotu LUT krāsu izveide

Lai pilnībā kontrolētu vizualizāciju, varat izveidot **pielāgotus krāsu gradientus**, rediģējot atsevišķus krāsu posmus.**Lai izveidotu pielāgotu gradientu:**

1. LUT panelī atrodiet**gradienta priekšskatījuma joslu**

2. Meklējiet**krāsu paraugu kvadrātiņus** zem gradienta
3. **Noklikšķiniet uz krāsu pārtraukuma**, lai to atlasītu
4. Atveras **krāsu izvēlne**

5. Izvēlieties jaunu krāsu, izmantojot:
   * **Krāsu ratu**: vizuāla krāsu izvēle
   * **RGB/HSV slideri**: Precīza krāsu kontrole
   * **Hex koda ievade**: Precīza krāsu specifikācija (piem., `#FF0000` sarkanai krāsai)
6. Noklikšķiniet ārpus krāsu izvēlnes, **lai piemērotu jauno krāsu**

7. Gradients**tūlīt atjauninās** attēlā**Krāsu punktu pievienošana vai noņemšana:*** **Pievienot punktu**: Noklikšķiniet uz ikonas +, lai pievienotu jaunu krāsu paraugu beigās
* **Noņemt punktu**: Divreiz noklikšķiniet uz krāsu kvadrāta, lai noņemtu krāsu paraugu**Pielāgošanas stratēģijas:*** **Gradienta apgriešana**: Apgrieziet krāsu secību, lai mainītu nozīmi (piem., zaļš = zems, sarkans = augsts)
* **Zīmola krāsas**: Pielāgojiet ziņojumus jūsu organizācijas krāsu paletei
* **Piemērots cilvēkiem ar krāsu redzes traucējumiem**: Izmantojiet oranž-zilo vai violeto-dzelteno krāsu kombinācijas
* **Drukas optimizācija**: Izvēlieties krāsas, kas der gan krāsainai, gan pelēktoņu drukai
* **Daudzpakāpju**: Izmantojiet atšķirīgas krāsas pie noteiktām vērtību robežvērtībām klasifikācijai

{% hint style="info" %}
**Pielāgoto gradientu saglabāšana**: Pielāgotos gradientus var saglabāt un izmantot atkārtoti. Noklikšķiniet uz saglabāšanas ikonas LUT panelī, lai saglabātu savas pielāgotās krāsu shēmas turpmākai lietošanai.
{% endhint %}

***

## Interaktīva darba plūsma

### Atjauninājumi reālajā laikā

Visi LUT pielāgojumi izmēģinājumu vidē atjaunina attēlu **tūlīt un interaktīvi**:

* **Pārslēdziet slāni** → Attēls mainās nekavējoties
* **Izvēlieties gradientu** → Krāsas atjauninās tūlīt
* **Pielāgojiet vērtību diapazonu** → Kontrasts mainās reālajā laikā
* **Mainiet klases** → Gradienta gludums atjauninās nekavējoties
* **Modificējiet apgriešanu** → Fona attēls mainās nekavējoties
* **Rediģējiet krāsas** → Pielāgotais gradients tiek piemērots nekavējoties**Nav nepieciešama &quot;Piemērot&quot; poga** — visas izmaiņas ir redzamas tiešsaistē un interaktīvas!

{% hint style="success" %}
**Reāllaika atgriezeniskā saite**: Tūlītējā vizuālā atgriezeniskā saite ļauj ātri eksperimentēt ar dažādiem iestatījumiem, līdz atrodat optimālo vizualizāciju savām analīzes vajadzībām.
{% endhint %}

### Iteratīvās pilnveidošanas darba plūsma

**Tipiska LUT optimizācijas darba plūsma:**

1.**Izvēlieties indeksa slāni** (piem., RAW (atstarošanās))
2. **Piemērojiet indeksu** — izvēlieties kameras filtru un indeksa formulu, velciet krāsainos apļus uz atbilstošo vietu indeksa formulā
3. **Piemērojiet LUT gradientu** — sāciet ar Red-Yellow-Green iestatījumu
4. **Pārbaudiet pikseļu vērtības** — pārvietojiet kursoru, ņemiet vērā vērtību diapazonus
5. **Pielāgojiet minimālo/maksimālo vērtību** – sašauriniet diapazonu, lai koncentrētos uz veģetāciju (piem., no 0,2 līdz 0,9)
6. **Izvēlieties apgriešanu** – izmēģiniet “Original Background” konteksta saglabāšanai
7. **Precizējiet krāsas** – nepieciešamības gadījumā pielāgojiet gradientu, lai izceltu konkrētas detaļas
8. **Pabeigt iestatījumus**– Dokumentējiet iestatījumus un kopējiet tos uz Projekta iestatījumiem eksportēšanas apstrādei

### Pikseļu vērtību pārbaude

Faktisko pikseļu vērtību izpratne ir būtiska, lai noteiktu efektīvus LUT diapazonus:**Kā pārbaudīt vērtības:**

1. Pikseļu vērtības parādās, ja attēlam ir**atzīmētas**vai nu Index, vai gan Index, gan LUT**izvēles rūtiņas**.
2. **Pārvietojiet kursoru** pār dažādām attēla zonām
3. **Novērojiet pikseļu vērtības**, kas parādās leģendā, kad uzvedat kursoru
4. Palieliniet attēlu, lai redzētu atsevišķus pikseļus, kas izcelti ar peldošu vērtību
5. **Pierakstiet** vērtību diapazonus dažādām pazīmēm:
   * **Veselīga veģetācija**: piemēram, NDVI 0,55–0,85
   * **Stresa skarta veģetācija**: piemēram, NDVI 0,30–0,50
   * **Kaila augsne**: piemēram, NDVI 0,05–0,25
   * **Ūdens** (ja ir): piemēram, NDVI -0,05 līdz 0,10**Pikseļu vērtību izmantošana LUT diapazonu iestatīšanai:**Pēc pikseļu vērtību pārbaudes atbilstoši pielāgojiet savu LUT minimālo/maksimālo vērtību:**Piemērs:*** **Novērojums**: Augsnes vērtības = 0,05–0,25, Stresā = 0,25–0,50, Veselīgas = 0,50–0,85
* **Mērķis**: Vizualizēt tikai augu veselību (izslēgt augsni)
* **LUT iestatījumi**: Min = `0.25`, Max = `0.85`
* **Apgriešana**: &quot;Oriģinālais fons&quot;, lai redzētu augsni dabīgās krāsās
* **Rezultāts**: Krāsu gradients attiecas tikai uz veģetāciju, augsne tiek attēlota kā oriģinālajā attēlā

{% hint style="info" %}
**Dinamiskais diapazons**: Dažādiem kultūraugiem, gadalaikiem un augšanas stadijām būs atšķirīgi vērtību diapazoni. Vienmēr pārbaudiet pikseļu vērtības savā konkrētajā datu kopā, pirms iestatāt LUT diapazonus.
{% endhint %}

***

## Pielāgotie indeksi (Chloros+)

### Pielāgotu indeksu formulu izveide

{% hint style="info" %}
**Kur izveidot**: Pielāgotos indeksus var konfigurēt**Projekta iestatījumos** pirms apstrādes, kā arī attēlu skatītāja sandbox sānu joslā.
{% endhint %}

**Lai izveidotu pielāgotu indeksu:**

1.**Atveriet projekta iestatījumus** (pirms apstrādes) vai attēlu skatītāja sandbox sānu joslu
2. Pārejiet uz **indeksa formulas izvēlni**

3. Meklējiet opciju**&quot;Pielāgots&quot;** (jābūt pieteicies ar Chloros+ licenci)
4. **Definējiet savu formulu**, izmantojot joslu mainīgos:
   * Joslu nosaukumi: `NIR`, `Red`, `Green`, `Blue`, `RedEdge` utt.
   * Operatori: `+`, `-`, `*`, `/`, `^` (eksponents)
   * Funkcijas: `sqrt()`, `abs()` utt. (ja atbalstītas)
   * Aizvērtnes: `()` darbību secībai
5. **Nosauciet savu indeksu** (piem., &quot;MyIndex&quot; vai &quot;CustomNDVI&quot;)
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

{% hint style="warning" %}
**Formulas validācija**: Pārliecinieties, ka jūsu formula izmanto jūsu kamerā pieejamos diapazonus. Piemēram, RedEdge ir pieejams tikai kamerās ar RedEdge filtru.
{% endhint %}

***

## Nākamie soļi

Tagad, kad jūs saprotat indeksa/LUT smilšu kasti:

* **Piemērojiet apstrādei**: izmantojiet atrastos iestatījumus [Projekta iestatījumos](../project-settings/project-settings.md)
* **Partijas apstrāde**: piemērojiet optimizētus indeksus pilniem datu kopumiem
* **Uzzināt vairāk**: izlasiet [Daudzspektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md)

Saistītā dokumentācija:

* [**Attēlu slāņi**](image-layers.md) — slāņu pārvaldība un vizualizācija
* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — attēlu skatītāja pamati
* [**Attēlu apstrāde (GUI)**](../processing-images-gui/adding-files-to-a-project.md) — pilna apstrādes darba plūsma
