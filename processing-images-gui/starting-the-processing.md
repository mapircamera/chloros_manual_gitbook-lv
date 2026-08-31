# Apstrādes uzsākšana

Kad esat importējis attēlus, atzīmējis kalibrēšanas mērķus un konfigurējis projekta iestatījumus, varat sākt apstrādi. Šī lapa palīdzēs jums uzsākt Chloros apstrādes procesu.

## Pārbaudes saraksts pirms apstrādes

Pirms noklikšķināt uz pogas „Sākt”, pārliecinieties, ka viss ir gatavs:

* [ ] **Faili importēti** – visi attēli parādās failu pārlūkprogrammā
* [ ] **Mērķa attēli atzīmēti** — kolonnā „Mērķis” ir atzīmēti kalibrēšanas attēli (vai importēts `.daq` ieraksts LATTICE lietošanai)
* [ ] **Kameru modeļi atpazīti** — kolonnā „Kameras modelis” redzami pareizie kameru modeļi
* [ ] **Iestatījumi konfigurēti** — projekta iestatījumi pārskatīti un pielāgoti
* [ ] **Indeksi izvēlēti** — pievienoti vēlamie multispektrālie indeksi (ja nepieciešams)
* [ ] **Eksporta formāts izvēlēts** — izejas formāts, kas atbilst jūsu darba plūsmai

{% hint style="info" %}
**Padoms**: Pirms apstrādes noklikšķiniet uz dažiem attēliem failu pārlūkā, lai pārliecinātos, ka tie ir ielādēti pareizi.
{% endhint %}

***

## Apstrādes uzsākšana

### Atrodiet pogu „Sākt”

Sākšanas/atskaņošanas poga atrodas Chloros augšējā galvenes joslā:

* Atrašanās vieta: loga augšējā centrā
* Ikona: **Atskaņošanas/sākšanas poga** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Stāvoklis: poga ir aktivizēta (spilgti izgaismota), kad ir gatava apstrādei

### Noklikšķiniet, lai sāktu

1. Noklikšķiniet uz **Atskaņot/Sākt pogas** augšējā joslā
2. Apstrāde sākas nekavējoties
3. Apstrādes laikā poga pārvēršas par **Apstādināt** pogu
4. Progresa josla atjaunojas, parādot apstrādes statusu

{% hint style="success" %}
**Apstrāde sākta**: Pēc noklikšķināšanas Chloros automātiski veic visus apstrādes posmus — mērķa noteikšanu, debayeringu, kalibrēšanu, indeksa aprēķināšanu un eksportēšanu. Tā automātiski noteic, vai jūsu projekts ir Survey3, LATTICE vai to kombinācija, un katrai kamerai piemēro atbilstošo apstrādes plūsmu.
{% endhint %}

***

## Apstrādes režīmu izpratne

Chloros darbojas divos dažādos apstrādes režīmos atkarībā no jūsu licences:

### Bezmaksas režīms (secīga apstrāde)

**Pieejams visiem lietotājiem**

**Kā tas darbojas:**

* Apstrādā attēlus pa vienam, secīgi
* Vienpavediena darbība
* Mazāks atmiņas patēriņš

**Progresa josla parāda 2 posmus:**

1.**Mērķa noteikšana** — kalibrēšanas mērķu skenēšana
2. **Apstrāde** — kalibrēšanas piemērošana un attēlu eksportēšana**Apstrādes laiks:**

* Daudz lēnāks nekā Chloros+ paralēlais režīms
* Piemērots maziem un vidējiem datu kopumiem (&lt; 200 attēli)

### Chloros+ režīms (paralēlā apstrāde)

**Nepieciešama Chloros+ licence**

**Kā tas darbojas:**

* Vienlaikus apstrādā vairākus attēlus, izmantojot [4-pavedienu apstrādes cauruļvadu](../processing-architecture/processing-pipeline.md)
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) programmas palaišanas brīdī automātiski izvēlas optimālo stratēģiju jūsu aparatūrai
* GPU (CUDA) paātrinājums ar NVIDIA grafiskajām kartēm (galddatoriem un Jetson)
* **Darbinieku skaits pielāgojas aparatūrai**: GPU stratēģijas palaista**1–4 vienlaicīgus darbiniekus** (mērogs atkarīgs no VRAM — Jetson ar nelielu atmiņu palaista 1, galddatoru GPU ar 12 GB un vairāk palaista līdz 4); sistēmās, kurās izmanto tikai CPU, tiek palaists viens darbinieks uz katru fizisko kodolu, atskaitot vienu**Progresa josla parāda 4 posmus** (atbilstoši 4 cauruļvada pavedieniem):

1. **Atklāšana** (1. pavediens) — kalibrēšanas mērķu atrašana
2. **Analīze** (2. pavediens) — attēla metadatu pārbaude un kalibrēšanas aprēķināšana
3. **Kalibrēšana** (pavediens Nr. 3) — debayering, vinjetes korekcija, kalibrēšana, indeksa aprēķināšana
4. **Eksportēšana** (pavediens Nr. 4) — apstrādāto attēlu un indeksu saglabāšana**Progresa joslas lietošana:*** **Pavelciet peles kursoru** virs joslas, lai redzētu detalizētu 4 posmu nolaižamo paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu nolaižamo paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un paslēptu paneli**Apstrādes laiks:**

* Ievērojami ātrāks nekā bezmaksas režīmā
* GPU paātrinājums vēl vairāk palielina ātrumu

{% hint style="info" %}
**Chloros+ Ātrums**: Lieliem datu kopumiem paralēlā apstrāde var būt 5–10 reizes ātrāka nekā secīgā režīmā. Projekts ar 500 attēliem, kura apstrāde bezmaksas režīmā aizņem 2 stundas, ar Chloros+ var tikt pabeigts 15–20 minūtēs.
{% endhint %}

***

## Kas notiek apstrādes laikā

### 1. posms: Mērķa atpazīšana

**Ko dara Chloros:**

* Pārskata attēlus, kurus esat atzīmējuši kolonnā „Mērķis” (ja neviens nav atzīmēts, tiek pārskatīti visi attēli)
* Identificē kalibrēšanas paneļus katrā mērķī
* Izgūst atstarošanas vērtības no mērķa paneļiem
* Reģistrē mērķu laika zīmogus kalibrēšanas plānošanai

**Ilgums:** 1–30 sekundes (ar atzīmētiem mērķiem), 5–30+ minūtes (bez atzīmēm)

### 2. posms: Debayering (RAW konvertēšana)

**Chloros darbības:**

* Konvertē RAW Bayer modeļa datus pilnvērtīgos 3-kanālu attēlos (LATTICE mono moduļi paliek vienkanāla — tiem debayering tiek izlaists, par to atzīmējot žurnālā)
* Piemēro izvēlēto demosaicing algoritmu
* Saglabā maksimālu attēla kvalitāti un detaļas

**Ilgums:** Atšķiras atkarībā no attēlu skaita un CPU/GPU ātruma

### 3. posms: Kalibrēšana

**Chloros funkcijas:*** **Vignettinga korekcija**: novērš objektīva izraisīto attēla tumšošanos malās
* **Atstarošanas kalibrēšana**: normalizē, izmantojot mērķa atstarošanas vērtības un/vai DAQ lejupvērstos datus
* Piemēro korekcijas visos diapazonos/kanālos
* Katram attēlam izmanto atbilstošu kalibrēšanas atsauci, pamatojoties uz laika zīmogu

**Ilgums:** Lielākā daļa apstrādes laika

### 4. posms: Indeksu aprēķināšana

**Ko dara Chloros:**

* Aprēķina konfigurētos multispektrālos indeksus (NDVI, NDRE utt.)
* Piemēro joslu matemātiskās operācijas kalibrētajiem attēliem
* Ģenerē indeksa attēlus katram izvēlētajam indeksam

**Ilgums:** Dažas sekundes uz vienu attēlu

### 5. posms: Eksportēšana

**Ko dara Chloros:**

* Saglabā apstrādātos attēlus izvēlētajā formātā
* **LATTICE fan-out**: katrs neapstrādāts LATTICE kadrs tiek eksportēts kā visi aktivizētie produkti vienā darbības ciklā — bez bayera filtra, priekšskatījums, starojums (vienmēr float32), atstarošanās
* Ieraksta failus projekta izvades struktūrā: `<project>/<camera>/<format>/<Product>_Images/`
* **Saglabā avota faila nosaukumu** — mape identificē produktu, netiek pievienots nekāds paplašinājums**Ilgums:** Atšķiras atkarībā no eksporta formāta un faila lieluma***

## Apstrādes darbība

### Automātiskā apstrādes procesa plūsma

Pēc palaišanas visa procesa plūsma darbojas automātiski:

* Nav nepieciešama lietotāja iejaukšanās
* Visi konfigurētie soļi tiek izpildīti secīgi
* Progresa atjauninājumi tiek parādīti reāllaikā
* Eksportētie faili tiek ierakstīti diskā, tiklīdz tie ir pabeigti — jūs varat atvērt gatavos rezultātus, kamēr apstrāde turpinās

### Datorresursu izmantošana apstrādes laikā

**Brīvais režīms:**

* Salīdzinoši zema procesora noslodze (vienpavediena)
* Dators joprojām reaģē uz citām uzdevumiem
* Var droši samazināt Chloros logu un strādāt citās lietojumprogrammās

**Chloros+ paralēlais režīms:**

* Augsta procesora noslogotība visā stratēģijas darba grupā
* Ar GPU paātrinājumu: augsta GPU noslogotība
* Apstrādes laikā dators var reaģēt lēnāk
* Izvairieties no citu procesoru resursus intensīvu uzdevumu palaišanas

{% hint style="warning" %}
**Veiktspējas padoms**: Lai nodrošinātu labāko Chloros+ veiktspēju, aizveriet citas lietojumprogrammas un ļaujiet Chloros izmantot visus sistēmas resursus.
{% endhint %}

### Apstrādi nevar apturēt (bet to var pilnībā pārtraukt)

* Pēc uzsākšanas apstrādi nevar apturēt un vēlāk atsākt
* Noklikšķinot uz **Stop**, apstrāde tiek pilnībā pārtraukta jau ar pirmo klikšķi
* Produkti, kas jau bija eksportēti pirms pārtraukšanas, paliek diskā
* Pārtraukta apstrāde precīzi atspoguļo to, kas ir pabeigts (skatiet `[RUN-SUMMARY]` rindas žurnālā)
* Jauns apstrādes cikls sāk procesu no sākuma

**Plānošanas padoms:** Ļoti lieliem projektiem apsveriet iespēju veikt apstrādi pa daļām vai izmantot CLI, lai nodrošinātu labāku kontroli.***

## Apstrādes uzraudzība

Kamēr apstrāde noris, jūs varat:

* **Sekot līdzi progresa joslai** — redzēt kopējo pabeigšanas procentu
* **Skatīt pašreizējo posmu** — atklāšana, analīze, kalibrēšana vai eksportēšana
* **Pārbaudīt žurnāla cilni** — skatīt detalizētus apstrādes ziņojumus un brīdinājumus
* **Priekšskatīt pabeigtos attēlus** — eksportētie faili parādās diskā apstrādes laikā

Sīkāku informāciju par uzraudzību skatiet sadaļā [Apstrādes uzraudzība](monitoring-the-processing.md).

***

## Apstrādes apturēšana

Ja jums ir nepieciešams apturēt apstrādi:

### Kā apturēt

1. Atrodiet **Pārtraukšanas pogu** (apstrādes laikā tā aizstāj Sākšanas pogu)
2. Noklikšķiniet uz tās vienu reizi — joslā parādīsies uzraksts **„Apturēšana...”**, kamēr tiek pabeigta pašreizējā attēla apstrāde
3. Apstrāde beidzas galīgā apturētajā stāvoklī, un žurnālā tiek izdrukāts precīzs `[RUN-SUMMARY]` pārskats par to, kas tika pabeigts

### Kad apturēt

**Pamatoti iemesli apturēšanai:**

* Tika konstatēts, ka izmantoti nepareizi iestatījumi
* Aizmirsts atzīmēt mērķa attēlus
* Ir importēti nepareizi attēli
* Sistēma darbojas pārāk lēni vai nereaģē

**Pēc apstādināšanas:**

* Pirms apstādināšanas eksportētie rezultāti paliek diskā
* Pārskatiet un novēršiet jebkādas problēmas, nepieciešamības gadījumā pielāgojiet iestatījumus
* Atkārtoti uzsāciet apstrādi — apstrāde sākas no sākuma

***

## Apstrādes laika aplēses

Faktiskais apstrādes laiks ievērojami atšķiras atkarībā no:

* Attēlu skaita
* Attēlu izšķirtspējas
* Ievades formāta (RAW vai JPG)
* Apstrādes režīma (Free vai Chloros+)
* Procesora ātruma un kodolu skaita
* Grafiskā procesora pieejamība (tikai Chloros+)
* Aprēķināmo indeksu skaits
* Aktivizēto eksportējamo produktu skaits (LATTICE)

### Aptuvenas aplēses (Chloros+, 12 MP attēli, moderns procesors)

| Attēlu skaits | Bezmaksas režīms | Chloros+ (procesors) | Chloros+ (grafiskais procesors) |
| ----------- | --------- | -------------- | -------------- |
| 50 attēli   | 15–20 min | 5–8 min        | 3–5 min        |
| 100 attēli  | 30–40 min | 10–15 min      | 5–8 min        |
| 200 attēli  | 1–1,5 stundas | 20–30 minūtes      | 10–15 minūtes      |
| 500 attēli  | 2–3 stundas   | 45–60 minūtes      | 20–30 minūtes      |
| 1000 attēli | 4–6 stundas   | 1,5–2 stundas      | 40–60 min      |

{% hint style="info" %}
**Pirmā palaišana**: Sākotnējā apstrāde var ilgt ilgāk, jo Chloros veido kešatmiņas un profilus. Turpmākā līdzīgu datu kopu apstrāde notiks ātrāk.
{% endhint %}

***

## Bieži sastopamas problēmas sākumā

### Sākšanas poga ir atspējota (izbalināta)

**Iespējamie iemesli:**

* Nav importēti attēli
* Aizmugurējā sistēma nav pilnībā palaista
* Joprojām noris iepriekšējā apstrāde
* Projekts nav pilnībā ielādēts

**Risinājumi:**

1. Pagaidiet, līdz aizmugurējā sistēma pilnībā inicializējas (pārbaudiet galvenās izvēlnes ikonu)
2. Pārbaudiet, vai attēli ir importēti failu pārlūkā
3. Ja poga joprojām ir neaktīva, restartējiet Chloros
4. Pārbaudiet kļūdu ziņojumus debug žurnālā

### Apstrāde sākas, bet tūlīt pat pārtraucas

**Iespējamie cēloņi:**

* Projektā nav derīgu attēlu
* Bojāti attēlu faili
* Nepietiekama diska vieta
* Nepietiekama atmiņa (RAM)

**Risinājumi:**

1. Pārbaudiet kļūdu ziņojumus debug logā <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">
2. Pārbaudiet, vai ir pieejama diska vieta
3. Mēģiniet apstrādāt mazāku attēlu kopu
4. Pārbaudiet, vai attēli nav bojāti

### Apstrāde beidzas, bet attēli netiek ierakstīti

Apstrāde, kurā tika pieprasīti attēlu produkti, bet netika saglabāti, tiek uzskatīta par **neveiksmi, nevis veiksmīgu izpildi** — Chloros to skaidri norāda:

* GUI žurnālā tiek izvadīti `[RUN-SUMMARY]` norādes, kas nosauc iespējamo cēloni — nav importēti attēli, nav atklāts mērķis vai visi pieprasītie produkti ir izlaisti kā nepiemēroti (piemēram, pieprasot starojuma/atstarošanas datus no kamerām, kas atbalsta tikai RGB)
* CLI ekvivalents (`chloros-cli process`) izvada `Processing finished but wrote no image products.` un **iziet ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt
* Apzināta darbība, kurā tiek apstrādāti tikai metadati (visi eksporta produkti atspējoti, nav indeksu), joprojām tiek uzskatīta par veiksmīgu

Pilnīgu semantiku skatiet [CLI atsauces dokumentā](../reference/cli-reference.md#a-run-that-writes-no-images-fails).

### Brīdinājums „Nav atklāti mērķi”

**Iespējamie cēloņi:**

* Aizmirsts atzīmēt mērķa attēlus
* Mērķa attēlos nav redzamu mērķu
* Mērķu atklāšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārskatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. Atzīmējiet atbilstošos attēlus kolonnā „Mērķis”
3. Pārliecinieties, ka mērķi ir redzami atzīmētajos attēlos
4. Vajadzības gadījumā pielāgojiet mērķu atklāšanas iestatījumus

***

## Padomi veiksmīgai apstrādei

### Pirms sākšanas

1. **Vispirms veiciet testu ar nelielu attēlu kopu** — apstrādājiet 10–20 attēlus, lai pārbaudītu iestatījumus
2. **Pārbaudiet pieejamo diska vietu** — nodrošiniet, lai brīvās vietas apjoms būtu 2–3 reizes lielāks par datu kopas izmēru (vairāk, ja ir ieslēgti visi LATTICE produkti)
3. **Aizveriet nevajadzīgās programmas** — atbrīvojiet sistēmas resursus
4. **Pārbaudiet mērķu attēlus** — apskatiet atzīmētos mērķus, lai pārliecinātos par kvalitāti
5. **Saglabājiet projektu** — projekts tiek saglabāts automātiski, taču ieteicams to saglabāt arī manuāli

### Apstrādes laikā

1. **Izvairieties no sistēmas miega režīma** — atspējojiet enerģijas taupīšanas režīmus
2. **Saglabājiet Chloros priekšplānā** — vai vismaz redzamu uzdevumu joslā
3. **Laiku pa laikam pārbaudiet apstrādes gaitu** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Nelādējiet citas resursietilpīgas programmas** — it īpaši, ja izmantojat Chloros+ paralēlo režīmu

### Chloros+ GPU paātrinājums

Ja izmantojat NVIDIA GPU paātrinājumu:

1. Atjauniniet NVIDIA draiverus uz jaunāko versiju
2. Pārliecinieties, ka GPU ir vismaz 4 GB VRAM (7 GB vai vairāk, ja vienlaikus tiek izmantota Texture Aware debayering funkcija)
3. Aizveriet programmas, kas intensīvi izmanto GPU (spēles, videomontāža)
4. Uzraugiet GPU temperatūru (nodrošiniet pietiekamu dzesēšanu)

***

## Turpmākie soļi

Kad apstrāde ir sākusies:

1. **Uzraugiet apstrādes gaitu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)
2. **Pagaidiet, līdz apstrāde beigsies** — apstrāde notiek automātiski
3. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)

Informāciju par to, ko darīt apstrādes laikā, skatiet [Apstrādes uzraudzība](monitoring-the-processing.md).
