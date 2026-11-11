# Apstrādes uzsākšana

Kad esat importējis attēlus, atzīmējis kalibrēšanas mērķus un konfigurējis projekta iestatījumus, varat sākt apstrādi. Šī lapa palīdzēs jums uzsākt Chloros apstrādes procesu.

## Pārbaudes saraksts pirms apstrādes

Pirms noklikšķināt uz pogas „Sākt“, pārliecinieties, ka viss ir gatavs:

* [ ] **Faili importēti** — visi attēli parādās failu pārlūkā
* [ ] **Mērķa attēli atzīmēti** — kolonnā „Mērķis” ir atzīmēti kalibrēšanas attēli (vai importēts `.daq` ieraksts LATTICE lietošanai)
* [ ] **Atpazīti kameru modeļi** — kolonnā „Kameras modelis” redzamas pareizās kameras
* [ ] **Iestatījumi konfigurēti** — projekta iestatījumi pārskatīti un pielāgoti
* [ ] **Indeksi izvēlēti** — pievienoti vēlamie multispektrālie indeksi (ja nepieciešams)
* [ ] **Izvēlēts eksporta formāts** — izvēlēts jūsu darba plūsmai atbilstošs izvades formāts

{% hint style="info" %}
**Padoms**: Pirms apstrādes noklikšķiniet uz dažiem attēliem failu pārlūkā, lai pārliecinātos, ka tie ir ielādēti pareizi.
{% endhint %}

***

## Apstrādes uzsākšana

### Atrodiet pogu „Sākt”

Poga „Sākt/Atskaņot” atrodas programmas Chloros augšējā galvenes joslā:

* Atrašanās vieta: loga augšējā centrā
* Ikona: **Poga „Atskaņot/Sākt”** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Stāvoklis: poga ir aktīva (spilgta), kad programma ir gatava apstrādei

### Noklikšķiniet, lai sāktu

1. Noklikšķiniet uz **Atskaņot/Sākt pogas** augšējā joslā
2. Apstrāde sākas nekavējoties
3. Apstrādes laikā poga pārvēršas par **Apstādināt** pogu
4. Progresa josla atjaunojas, parādot apstrādes statusu

{% hint style="success" %}
**Apstrāde uzsākta**: Pēc noklikšķināšanas Chloros automātiski veic visus apstrādes posmus — mērķa noteikšanu, debayeringu, kalibrēšanu, indeksa aprēķināšanu un eksportēšanu. Tas automātiski atpazīst, vai jūsu projekts ir Survey3, „LATTICE” vai to kombinācija, un katrai kamerai piemēro atbilstošo apstrādes procesu.
{% endhint %}

***

## Apstrādes režīmu izpratne Atkarībā no jūsu licences,

Chloros darbojas divos dažādos apstrādes režīmos:

### Bezmaksas režīms (secīga apstrāde)

**Pieejams visiem lietotājiem**

**Kā tas darbojas:**

* Apstrādā attēlus pa vienam, secīgi
* Vienpavediena darbība
* Mazāks atmiņas patēriņš

**Progresa josla rāda 2 posmus:**

1.**Mērķa noteikšana** — kalibrēšanas mērķu skenēšana
2. **Apstrāde** — kalibrēšanas piemērošana un attēlu eksportēšana**Apstrādes laiks:**

* Daudz lēnāks nekā „Chloros+“ paralēlais režīms
* Piemērots maziem un vidējiem datu kopumiem (< 200 attēli)

### „Chloros+“ režīms (paralēla apstrāde)

**Nepieciešama „Chloros+“ licence**

**Kā tas darbojas:**

* Vienlaikus apstrādā vairākus attēlus, izmantojot [4-pavedienu apstrādes cauruļvadu](../processing-architecture/processing-pipeline.md)
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) automātiski izvēlas optimālo stratēģiju jūsu aparatūrai, sākot darbību
* GPU (CUDA) paātrinājums ar NVIDIA grafiskajām kartēm
* **Darbinieku skaits pielāgojas aparatūrai**: GPU stratēģijas palaista**2–4 vienlaicīgus darbiniekus** (mērogs atkarīgs no VRAM — 3 uz 2–12 GB GPU, 2 līdz 4 uz 12 GB+ GPU); Sistēmās, kurās tiek izmantots tikai CPU, tiek palaists viens darba process uz katru fizisko kodolu, atņemot vienu**Progresa josla parāda 4 posmus** (kas atbilst 4 cauruļvada pavedieniem):

1. **Atklāšana** (1. pavediens) — kalibrēšanas mērķu meklēšana
2. **Analīze** (pavediens Nr. 2) — attēla metadatu pārbaude un kalibrēšanas aprēķināšana
3. **Kalibrēšana** (pavediens Nr. 3) — debayering, vinjetes korekcija, kalibrēšana, indeksa aprēķināšana
4. **Eksportēšana** (pavediens Nr. 4) — apstrādāto attēlu un indeksu saglabāšana**Progresa joslas lietošana:*** **Pavelciet peles kursoru** pāri joslai, lai redzētu detalizētu 4 posmu nolaižamo paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu nolaižamo paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un paslēptu paneli**Apstrādes laiks:**

* Ievērojami ātrāk nekā bezmaksas režīmā
* GPU paātrinājums vēl vairāk palielina ātrumu

{% hint style="info" %}
**Chloros+ ātrums**: Lieliem datu kopumiem paralēlā apstrāde var būt 5–10 reizes ātrāka nekā secīgajā režīmā. Projekts ar 500 attēliem, kura apstrāde bezmaksas režīmā aizņem 2 stundas, ar Chloros+ var tikt pabeigts 15–20 minūtēs.
{% endhint %}

***

## Kas notiek apstrādes laikā

### 1. posms: Mērķu atpazīšana

**Ko dara Chloros:**

* Pārskata attēlus, kurus esat atzīmējuši kolonnā „Mērķis” (ja neviens nav atzīmēts, tiek pārskatīti visi attēli)
* Identificē kalibrēšanas paneļus katrā mērķī
* Izgūst atstarošanas vērtības no mērķa paneļiem
* Reģistrē mērķu laika zīmogus kalibrēšanas plānošanai

**Ilgums:** 1–30 sekundes (ar atzīmētiem mērķiem), 5–30+ minūtes (bez atzīmēm)

### 2. posms: Debayering (RAW konvertēšana)

**Ko dara Chloros:**

* Konvertē RAW Bayer modeļa datus pilnos 3-kanālu attēlos (LATTICE mono moduļi paliek vienkanāla — tiem debayering tiek izlaists, par to atzīmējot žurnālā)
* Piemēro izvēlēto demosaicing algoritmu
* Saglabā maksimālu attēla kvalitāti un detaļas

**Ilgums:** Atšķiras atkarībā no attēlu skaita un CPU/GPU ātruma

### 3. posms: Kalibrēšana

**Ko dara Chloros:*** **Vignettinga korekcija**: Noņem objektīva izraisīto tumšošanos malās
* **Atstarošanas kalibrēšana**: normalizē, izmantojot mērķa atstarošanas vērtības un/vai DAQ lejupvērstos datus
* Piemēro korekcijas visos diapazonos/kanālos
* Katram attēlam izmanto atbilstošu kalibrēšanas atsauci, pamatojoties uz laika zīmogu

**Ilgums:** lielākā daļa apstrādes laika

### 4. posms: Indeksu aprēķināšana

**Ko dara Chloros:**

* Aprēķina konfigurētos multispektrālos indeksus (NDVI, NDRE utt.)
* Piemēro joslu matemātiskās operācijas kalibrētajiem attēliem
* Ģenerē indeksu attēlus katram izvēlētajam indeksam

**Ilgums:** Dažas sekundes uz vienu attēlu

### 5. posms: Eksportēšana

**Ko dara Chloros:**

* Saglabā apstrādātos attēlus izvēlētajā formātā
* **LATTICE fan-out**: katrs neapstrādāts LATTICE kadrs vienā darbības ciklā tiek eksportēts kā visi aktivizētie produkti — bez bayera filtra, priekšskatījums, starojums (vienmēr float32), atstarošanās
* Ieraksta failus projekta izvades kokā: `<project>/<camera>/<format>/<Product>_Images/`
* **Saglabā avota faila nosaukumu** — mape identificē produktu, netiek pievienots nekāds paplašinājums**Ilgums:** Atšķiras atkarībā no eksporta formāta un faila izmēra***

## Apstrādes darbība

### Automātiskā apstrādes procesa ķēde

Pēc palaišanas visa procesa ķēde darbojas automātiski:

* Nav nepieciešama lietotāja iejaukšanās
* Visi konfigurētie soļi tiek izpildīti secīgi
* Progresa atjauninājumi tiek parādīti reāllaikā
* Eksportētie faili tiek ierakstīti diskā, tiklīdz tie ir pabeigti — jūs varat atvērt gatavos rezultātus, kamēr apstrāde turpinās

### Datora resursu izmantošana apstrādes laikā

**Bezmaksas režīms:**

* Salīdzinoši zems procesora noslogojums (vienpavediena režīms)
* Dators joprojām reaģē uz citām uzdevumiem
* Var droši samazināt Chloros logu un strādāt citās lietojumprogrammās

**Chloros+ paralēlais režīms:**

* Augsta procesora noslogotība visā stratēģijas darba grupā
* Ar GPU paātrinājumu: augsta GPU noslogotība
* Apstrādes laikā dators var reaģēt lēnāk
* Izvairieties no citu procesoru resursus intensīvu uzdevumu uzsākšanas

{% hint style="warning" %}
**Padoms par veiktspēju**: Lai nodrošinātu vislabāko Chloros+ veiktspēju, aizveriet citas programmas un ļaujiet Chloros izmantot visus sistēmas resursus.
{% endhint %}

### Apstrādi nevar apturēt (bet tās pārtraukšana notiek bez problēmām)

* Pēc uzsākšanas apstrādi nevar apturēt un vēlāk atsākt
* Noklikšķinot uz **Stop**, apstrāde tiek pilnībā pārtraukta jau ar pirmo klikšķi
* Produkti, kas jau bija eksportēti pirms pārtraukšanas, paliek diskā
* Pārtraukta apstrāde precīzi atspoguļo to, kas ir paveikts (skatiet `[RUN-SUMMARY]` rindas žurnālā)
* Jauns apstrādes cikls sāk procesu no sākuma

**Plānošanas padoms:** Ļoti lieliem projektiem apsveriet iespēju veikt apstrādi partijās vai izmantot CLI, lai nodrošinātu labāku kontroli.***

## Apstrādes uzraudzība

Kamēr notiek apstrāde, jūs varat:

* **Sekot līdzi progresa joslai** — redzēt kopējo pabeigšanas procentu
* **Apskatīt pašreizējo posmu** — atklāšana, analīze, kalibrēšana vai eksportēšana
* **Pārbaudīt žurnāla cilni** — redzēt detalizētus apstrādes ziņojumus un brīdinājumus
* **Priekšskatīt pabeigtos attēlus** — eksportētie faili parādās diskā apstrādes laikā

Sīkāku informāciju par uzraudzību skatiet sadaļā [Apstrādes uzraudzība](monitoring-the-processing.md).

***

## Apstrādes apturēšana

Ja jums ir nepieciešams apturēt apstrādi:

### Kā apturēt

1. Atrodiet **poga „Stop”** (apstrādes laikā tā aizstāj pogu „Sākt”)
2. Noklikšķiniet uz tās vienu reizi — joslā parādīsies uzraksts **„Pārtraukšana...”**, kamēr tiek pabeigts attēla apstrādes process
3. Apstrāde beidzas galīgā pārtraukšanas stāvoklī, un žurnālā tiek izdrukāts precīzs `[RUN-SUMMARY]` par to, kas tika pabeigts

### Kad pārtraukt

**Pamatoti iemesli pārtraukšanai:**

* Tika konstatēts, ka izmantoti nepareizi iestatījumi
* Aizmirsts atzīmēt mērķa attēlus
* Importēti nepareizi attēli
* Sistēma darbojas pārāk lēni vai nereaģē

**Pēc pārtraukšanas:**

* Pirms pārtraukšanas eksportētie rezultāti paliek diskā
* Pārskatiet un novēršiet visas problēmas, nepieciešamības gadījumā pielāgojiet iestatījumus
* Atkārtoti uzsāciet apstrādi — apstrāde sākas no sākuma

***

## Apstrādes laika aplēses

Faktiskais apstrādes laiks ievērojami atšķiras atkarībā no:

* Attēlu skaita
* Attēlu izšķirtspējas
* Ievades formāta (RAW vai JPG)
* Apstrādes režīma (Bezmaksas vai Chloros+)
* Procesora ātrums un kodolu skaits
* Grafiskā procesora pieejamība (tikai Chloros+)
* Aprēķināmo indeksu skaits
* Aktivizēto eksportējamo produktu skaits (LATTICE)

### Aptuvenas aplēses (Chloros+, 12 MP attēli, mūsdienīgs procesors)

| Attēlu skaits | Bezmaksas režīms | Chloros+ (procesors) | Chloros+ (grafiskais procesors) |
| ----------- | --------- | -------------- | -------------- |
| 50 attēli   | 15–20 min | 5–8 min        | 3–5 min        |
| 100 attēli  | 30–40 min | 10–15 min      | 5–8 min        |
| 200 attēli  | 1–1,5 stundas | 20–30 min      | 10–15 min      |
| 500 attēli  | 2–3 stundas   | 45–60 min      | 20–30 min      |
| 1000 attēli | 4–6 stundas   | 1,5–2 stundas      | 40–60 min      |

{% hint style="info" %}
**Pirmā palaišana**: Sākotnējā apstrāde var ilgt ilgāk, jo Chloros veido kešatmiņas un profilus. Turpmākā līdzīgu datu kopu apstrāde notiks ātrāk.
{% endhint %}

***

## Bieži sastopamas problēmas sākumā

### Sākšanas poga ir atspējota (izbalināta)

**Iespējamie iemesli:**

* Nav importēti attēli
* Aizmugurējā sistēma nav pilnībā uzsākta
* Joprojām notiek iepriekšējā apstrāde
* Projekts nav pilnībā ielādēts

**Risinājumi:**

1. Pagaidiet, līdz backend ir pilnībā inicializējies (pārbaudiet galvenās izvēlnes ikonu)
2. Pārbaudiet, vai attēli ir importēti failu pārlūkā
3. Ja poga joprojām ir neaktīva, pārstartējiet Chloros
4. Pārbaudiet kļūdu ziņojumus debug žurnālā

### Apstrāde sākas, bet tūlīt pat pārtraucas

**Iespējamie cēloņi:**

* Projektā nav derīgu attēlu
* Bojāti attēlu faili
* Nepietiekama diska vieta
* Nepietiekama atmiņa (RAM)

**Risinājumi:**

1. Pārbaudiet debug žurnālu <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">, lai atrastu kļūdu ziņojumus
2. Pārbaudiet pieejamo diska vietu
3. Mēģiniet apstrādāt mazāku attēlu apakškopu
4. Pārbaudiet, vai attēli nav bojāti

### Apstrāde beidzas, bet attēli netiek saglabāti

Apstrāde, kurā tika pieprasīti attēlu rezultāti, bet neviens netika saglabāts, tiek uzskatīta par **neveiksmi, nevis panākumu** — Chloros to skaidri norāda:

* GUI žurnālā tiek izvadīti `[RUN-SUMMARY]` norādes, kas nosauc iespējamo cēloni — nav importēti attēli, nav atklāts mērķis vai visi pieprasītie produkti ir izlaisti kā nepiemēroti (piemēram, pieprasot starojuma/atstarošanas datus no kamerām, kas uzņem tikai RGB)
* CLI-ekvivalents (`chloros-cli process`) izvada `Processing finished but wrote no image products.` un **iziet ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt
* Apzināts izpildes cikls, kurā tiek apstrādāti tikai metadati (visi eksporta produkti ir atspējoti, nav indeksu), joprojām tiek uzskatīts par veiksmīgu

Pilnu semantiku skatiet [CLI atsauces dokumentā](../reference/cli-reference.md#a-run-that-writes-no-images-fails).

### Projekta mape šim datoram ir pārāk dziļa

**Tikai Windows, jauns 1.3.0 versijā.** Windows tiek piegādāts ar atspējotiem gariem ceļiem, un tādējādi neviens faila ceļš nedrīkst būt garāks par 260 rakstzīmēm. Chloros produkti atrodas aptuveni 130 rakstzīmes zem projekta mapes, tādēļ projekta mape, kas atrodas dziļāk par to, nevar saņemt savus produktus. Tā vietā, lai palaistu un neko neierakstītu, Chloros tagad pirms apstrādes sākšanas veic pārbaudi:

* Noklikšķinot uz „Sākt”, izpilde netiek uzsākta: progresa josla parāda iemeslu, un Debug Log rāda `[PROCESS] refused: The project folder is too deep for this computer…`. Ziņojumā ir norādīts projekta mapes garums un garākā projekta mape, kas tajā ietilpst.
* Tiek atteikta arī projekta izveide vai atvēršana mapē, kas ir pārāk dziļa pat paša projekta failiem. Ziņojuma logā tiek norādīts iemesls, garākā projekta mapes nosaukuma garums, kas ietilpst, un kā ieslēgt garos ceļus, un nekas netiek izveidots vai atvērts; debug logā tas tiek reģistrēts kā `[PROJECT] refused: …`. Projekta dublikāta izveide ar garāku nosaukumu arī var pārsniegt ierobežojumu: kopija tiek izveidota, bet netiek atvērta, un tajā pašā ziņojumā ir norādīts iemesls.

**Risinājumi:**

1. Glabājiet projektus īsākā mapē — mainiet**Darba direktoriju** sadaļā [Projekta iestatījumi](../project-settings/project-settings.md) un izveidojiet projektu no jauna, vai
2. Ieslēdziet garos Windows ceļus un pārstartējiet Chloros: atkārtoti palaidiet Chloros instalētāju, atzīmējot opciju **Iespējot garos Windows ceļus** (tā ir atzīmēta pēc noklusējuma), vai izmantojiet grupas politiku: *Datoru konfigurācija > Administratīvie šabloni > Sistēma > Failu sistēma > Iespējot garos Win32 ceļus*.

### Brīdinājums „Nav atrasti mērķi”

**Iespējamie cēloņi:**

* Aizmirsu atzīmēt mērķa attēlus
* Mērķa attēlos nav redzamu mērķu
* Mērķu atpazīšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārskatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. Atzīmējiet atbilstošos attēlus kolonnā „Mērķis”
3. Pārbaudiet, vai mērķi ir redzami atzīmētajos attēlos
4. Vajadzības gadījumā pielāgojiet mērķu atklāšanas iestatījumus

***

## Padomi veiksmīgai apstrādei

### Pirms sākšanas

1. **Vispirms veiciet testu ar nelielu apakškopu** — apstrādājiet 10–20 attēlus, lai pārbaudītu iestatījumus
2. **Pārbaudiet pieejamo diska vietu** — nodrošiniet, lai brīvās vietas apjoms būtu 2–3 reizes lielāks par datu kopas izmēru (vairāk, ja ir ieslēgti visi LATTICE produkti)
3. **Aizveriet nevajadzīgās programmas** — atbrīvojiet sistēmas resursus
4. **Pārbaudiet mērķu attēlus** — apskatiet atzīmētos mērķus, lai pārliecinātos par kvalitāti
5. **Saglabājiet projektu** — projekts tiek saglabāts automātiski, taču ieteicams to saglabāt arī manuāli

### Apstrādes laikā

1. **Izvairieties no sistēmas miega režīma** — atspējojiet enerģijas taupīšanas režīmus
2. **Saglabājiet „Chloros” priekšplānā** — vai vismaz redzamu uzdevumjoslā
3. **Laiku pa laikam pārbaudiet apstrādes gaitu** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Neatveriet citas resursietilpīgas programmas** — īpaši, ja izmantojat „Chloros+” paralēlo režīmu

### „Chloros+“ GPU paātrinājums

Ja izmantojat NVIDIA GPU paātrinājumu:

1. Atjauniniet NVIDIA draiverus līdz jaunākajai versijai
2. Pārliecinieties, ka GPU ir vismaz 4 GB VRAM (vismaz 7 GB vienlaicīgai tekstūru apstrādei)
3. Aizveriet programmas, kas intensīvi izmanto GPU (spēles, videomontāža)
4. Uzraugiet GPU temperatūru (nodrošiniet pietiekamu dzesēšanu)

***

## Turpmākie soļi

Kad apstrāde ir sākusies:

1. **Uzraugiet apstrādes gaitu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)
2. **Pagaidiet, līdz apstrāde ir pabeigta** — apstrāde noris automātiski
3. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)

Informāciju par to, ko darīt apstrādes laikā, skatiet [Apstrādes uzraudzība](monitoring-the-processing.md).
