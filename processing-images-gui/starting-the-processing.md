# Apstrādes uzsākšana

Kad esat importējis attēlus, atzīmējis kalibrēšanas mērķus un konfigurējis projekta iestatījumus, varat sākt apstrādi. Šī lapa palīdzēs jums uzsākt Chloros apstrādes procesu.

## Pārbaudes saraksts pirms apstrādes

Pirms noklikšķināt uz pogas „Sākt”, pārliecinieties, ka viss ir gatavs:

* [ ] **Faili importēti** – visi attēli parādās failu pārlūkā
* [ ] **Mērķa attēli atzīmēti** – mērķa kolonna ir atzīmēta kalibrēšanas attēliem
* [ ] **Kameru modeļi atpazīti** – kameru modeļu kolonnā redzamas pareizās kameras
* [ ] **Iestatījumi konfigurēti** – projekta iestatījumi pārskatīti un pielāgoti
* [ ] **Izvēlēti indeksi** – pievienoti vēlamie multispektrālie indeksi (ja nepieciešams)
* [ ] **Izvēlēts eksporta formāts** – izvades formāts, kas atbilst jūsu darba plūsmai

{% hint style="info" %}
**Padoms**: Pirms apstrādes noklikšķiniet uz dažiem attēliem failu pārlūkā, lai pārliecinātos, ka tie ir ielādēti pareizi.
{% endhint %}

***

## Apstrādes sākšana

### Atrodiet pogu „Sākt”

Poga „Sākt/Atskaņot” atrodas Chloros augšējā galvenes joslā:

* Atrašanās vieta: loga augšējā centrā
* Ikona: **Poga „Atskaņot/Sākt”** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
* Stāvoklis: Poga ir aktīva (spilgta), kad ir gatava apstrādei

### Noklikšķiniet, lai sāktu

1. Noklikšķiniet uz **Atskaņot/Sākt pogas** augšējā joslā
2. Apstrāde sākas nekavējoties
3. Apstrādes laikā poga kļūst neaktīva (izbalināta)
4. Progresa josla atjaunojas, parādot apstrādes statusu

{% hint style="success" %}
**Apstrāde sākta**: Pēc noklikšķināšanas Chloros automātiski veic visus apstrādes posmus — mērķa noteikšanu, debayeringu, kalibrēšanu, indeksa aprēķināšanu un eksportēšanu.
{% endhint %}

***

## Apstrādes režīmu izpratne

Chloros darbojas divos dažādos apstrādes režīmos atkarībā no jūsu licences:

### Bezmaksas režīms (secīga apstrāde)

**Pieejams visiem lietotājiem**

**Kā tas darbojas:**

* Apstrādā attēlus pa vienam, secīgi
* Vienpakalpes darbība
* Mazāks atmiņas patēriņš

**Progresa josla parāda 2 posmus:**

1.**Mērķa noteikšana** — kalibrēšanas mērķu skenēšana
2. **Apstrāde** — kalibrēšanas piemērošana un attēlu eksportēšana**Apstrādes laiks:**

* Daudz lēnāks nekā Chloros+ paralēlais režīms
* Piemērots maziem un vidējiem datu kopumiem (&lt; 200 attēli)

### Chloros+ režīms (paralēla apstrāde)

**Nepieciešama Chloros+ licence**

**Kā tas darbojas:**

* Vienlaikus apstrādā vairākus attēlus, izmantojot [4-pavedienu apstrādes cauruļvadu](../processing-architecture/processing-pipeline.md)
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) automātiski izvēlas optimālo stratēģiju jūsu aparatūrai
* GPU (CUDA) paātrinājums ar NVIDIA grafiskajām kartēm (galddatoriem un Jetson)
* Mērogojams no Jetson Nano (1 darba procesors) līdz galddatoram ar 12 GB+ GPU (3–4 darba procesori)

**Progresa josla parāda 4 posmus** (atbilstoši 4 cauruļvadu pavedieniem):

1. **Atklāšana** (1. pavediens) – kalibrēšanas mērķu meklēšana
2. **Analīze** (2. pavediens) – attēla metadatu pārbaude un kalibrēšanas aprēķināšana
3. **Kalibrēšana** (3. pavediens) – GPU debayering, vinjetes korekcija, indeksa aprēķināšana
4. **Eksportēšana** (4. pavediens) – apstrādāto attēlu un indeksu saglabāšana**Progresa joslas mijiedarbība:*** **Pavelciet peles kursoru** pāri joslai, lai redzētu detalizētu 4 posmu izvēlnes paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu izvēlnes paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un paslēptu paneli**Apstrādes laiks:**

* Ievērojami ātrāks nekā bezmaksas režīmā
* Mērogs atkarīgs no CPU kodolu skaita
* GPU paātrinājums vēl vairāk uzlabo ātrumu

{% hint style="info" %}
**Chloros+ ātrums**: Paralēla apstrāde var būt 5–10 reizes ātrāka nekā secīgais režīms lieliem datu kopumiem. 500 attēlu projekts, kas bezmaksas režīmā aizņem 2 stundas, ar Chloros+ var tikt pabeigts 15–20 minūtēs.
{% endhint %}

***

## Kas notiek apstrādes laikā

### 1. posms: Mērķa noteikšana

**Ko dara Chloros:**

* Skenē atzīmētos mērķa attēlus (vai visus attēlus, ja neviens nav atzīmēts)
* Identificē 4 kalibrēšanas paneļus katrā mērķī
* Izgūst atstarojuma vērtības no mērķa paneļiem
* Reģistrē mērķa laika zīmogus kalibrēšanas plānošanai

**Ilgums:** 1–30 sekundes (ar atzīmētiem mērķiem), 5–30+ minūtes (bez atzīmēm)

### 2. posms: Debayering (RAW konvertēšana)

**Chloros funkcijas:**

* Konvertē RAW Bayer modeļa datus pilnvērtīgos RGB attēlos
* Piemēro augstas kvalitātes demosaicing algoritmu
* Saglabā maksimālu attēla kvalitāti un detaļas

**Ilgums:** Atšķiras atkarībā no attēlu skaita un procesora ātruma

### 3. posms: Kalibrēšana

**Chloros funkcijas:*** **Vignette korekcija**: Noņem objektīva tumšošanos malās
* **Atstarošanas kalibrēšana**: Normalizē, izmantojot mērķa atstarošanas vērtības
* Piemēro korekcijas visos diapazonos/kanālos
* Izmanto atbilstošu kalibrēšanas mērķi katram attēlam, pamatojoties uz laika zīmogu

**Ilgums:** Lielākā daļa apstrādes laika

### 4. posms: Indeksu aprēķināšana

**Ko dara Chloros:**

* Aprēķina konfigurētos multispektrālos indeksus (NDVI, NDRE utt.)
* Piemēro joslu matemātiku kalibrētajiem attēliem
* Ģenerē indeksa attēlus katram izvēlētajam indeksam

**Ilgums:** Dažas sekundes uz vienu attēlu

### 5. posms: Eksportēšana

**Ko dara Chloros:**

* Saglabā kalibrētos attēlus izvēlētajā formātā
* Eksportē indeksa attēlus ar konfigurētām LUT krāsām
* Raksta failus kameras modeļa apakšmapēs
* Saglabā oriģinālās failu nosaukumus ar paplašinājumiem

**Ilgums:** Atšķiras atkarībā no eksporta formāta un faila izmēra***

## Apstrādes darbība

### Automātiskā apstrādes procesa gaita

Pēc sākšanas viss process noris automātiski:

* Nav nepieciešama lietotāja iejaukšanās
* Visi konfigurētie soļi tiek izpildīti secīgi
* Progresa atjauninājumi tiek parādīti reāllaikā

### Datora izmantošana apstrādes laikā

**Brīvais režīms:**

* Salīdzinoši zems procesora noslogojums (vienpakalpojumu)
* Dators paliek atsaucīgs citām uzdevumiem
* Droši var samazināt Chloros logu un strādāt citās lietojumprogrammās

**Chloros+ Paralēlais režīms:**

* Augsts procesora noslogojums (daudzpakalpojumu, līdz 16 kodoliem)
* Ar GPU paātrinājumu: augsta GPU izmantošana
* Apstrādes laikā dators var reaģēt lēnāk
* Izvairieties no citu CPU resursu ietilpīgu uzdevumu sākšanas

{% hint style="warning" %}
**Padoms par veiktspēju**: Lai nodrošinātu labāko Chloros+ veiktspēju, aizveriet citas programmas un ļaujiet Chloros izmantot visus sistēmas resursus.
{% endhint %}

### Apstrādi nevar apturēt

**Svarīgi ierobežojumi:**

* Kad apstrāde ir sākta, to nevar apturēt
* Jūs varat atcelt apstrādi, bet progress tiks zaudēts
* Daļējie rezultāti netiek saglabāti
* Ja apstrāde tiek atcelta, tā jāuzsāk no sākuma

**Plānošanas padoms:** Ļoti lieliem projektiem apsveriet apstrādi partijās vai CLI izmantošanu labākai kontrolei.***

## Apstrādes uzraudzība

Kamēr apstrāde noris, jūs varat:

* **Skatīt progresa joslu** — redzēt kopējo pabeigšanas procentu
* **Skatīt pašreizējo posmu** — atklāšana, analīze, kalibrēšana vai eksportēšana
* **Pārbaudīt cilni „Log”** — skatīt detalizētus apstrādes ziņojumus un brīdinājumus
* **Priekšskatīt pabeigtos attēlus** — daži eksporta faili var parādīties apstrādes laikā

Detalizētu informāciju par uzraudzību skatiet sadaļā [Apstrādes uzraudzība](monitoring-the-processing.md).

***

## Apstrādes atcelšana

Ja jums ir nepieciešams pārtraukt apstrādi:

### Kā atcelt

1. Atrodiet **Pārtraukt/Atcelt pogu** (apstrādes laikā aizstāj Sākt pogu)
2. Noklikšķiniet uz Pārtraukt pogas
3. Apstrāde tiek nekavējoties pārtraukta
4. Daļējie rezultāti tiek izmesti

### Kad atcelt

**Pamatoti iemesli atcelšanai:**

* Ir konstatēts, ka tika izmantoti nepareizi iestatījumi
* Aizmirsts atzīmēt mērķa attēlus
* Importēti nepareizi attēli
* Sistēma darbojas pārāk lēni vai nereaģē

**Pēc atcelšanas:**

* Pārskatiet un novēršiet visas problēmas
* Pielāgojiet iestatījumus pēc nepieciešamības
* Sāciet apstrādi no sākuma
* Lai nodrošinātu vislabāko pieredzi, pilnībā aizveriet Chloros un sāciet no jauna

{% hint style="warning" %}
**Nav daļēju rezultātu**: Atcelšana izdzēš visu paveikto. Chloros nesaglabā daļēji apstrādātus attēlus.
{% endhint %}

***

## Apstrādes laika aplēses

Faktiskais apstrādes laiks ievērojami atšķiras atkarībā no:

* Attēlu skaita
* Attēlu izšķirtspējas
* Ievades formāta (RAW vai JPG)
* Apstrādes režīma (Bezmaksas vs Chloros+)
* Procesora ātrums un kodolu skaits
* GPU pieejamība (tikai Chloros+)
* Aprēķināmo indeksu skaits
* Eksporta formāta sarežģītība

### Aptuvenas aplēses (Chloros+, 12 MP attēli, moderns procesors)

| Attēlu skaits | Bezmaksas režīms | Chloros+ (CPU) | Chloros+ (GPU) |
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

1. Pagaidiet, līdz aizmugurējā sistēma ir pilnībā inicializējusies (pārbaudiet galvenās izvēlnes ikonu)
2. Pārbaudiet, vai attēli ir importēti failu pārlūkā
3. Ja poga joprojām ir atspējota, pārstartējiet Chloros
4. Pārbaudiet Debug Log, lai atrastu kļūdu ziņojumus

### Apstrāde sākas, bet tūlīt pat neizdodas

**Iespējamie cēloņi:**

* Projektā nav derīgu attēlu
* Bojāti attēlu faili
* Nepietiekama diska vieta
* Nepietiekama atmiņa (RAM)

**Risinājumi:**

1. Pārbaudiet Debug Log <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> , lai pārbaudītu, vai nav kļūdu ziņojumu
2. Pārbaudiet pieejamo diska vietu
3. Mēģiniet apstrādāt mazāku attēlu apakškopu
4. Pārbaudiet, vai attēli nav bojāti

### Brīdinājums „Nav atrasti mērķi”

**Iespējamie cēloņi:**

* Aizmirsts atzīmēt mērķa attēlus
* Mērķa attēlos nav redzamu mērķu
* Mērķu noteikšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārskatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. Atzīmējiet atbilstošos attēlus kolonnā &quot;Mērķis&quot;
3. Pārbaudiet, vai mērķi ir redzami atzīmētajos attēlos
4. Vajadzības gadījumā pielāgojiet mērķu atklāšanas iestatījumus

***

## Padomi veiksmīgai apstrādei

### Pirms sākšanas

1. **Vispirms veiciet testu ar nelielu apakškopu** — apstrādājiet 10–20 attēlus, lai pārbaudītu iestatījumus
2. **Pārbaudiet pieejamo diska vietu** — nodrošiniet 2–3 reizes lielāku brīvo vietu nekā datu kopas izmērs
3. **Aizveriet nevajadzīgās programmas** — atbrīvojiet sistēmas resursus
4. **Pārbaudiet mērķa attēlus** — apskatiet atzīmētos mērķus, lai pārliecinātos par kvalitāti
5. **Saglabājiet projektu** – projekts tiek saglabāts automātiski, bet ieteicams to saglabāt arī manuāli

### Apstrādes laikā

1. **Izvairieties no sistēmas miega režīma** – atslēdziet enerģijas taupīšanas režīmus
2. **Saglabājiet Chloros priekšplānā** – vai vismaz redzamu uzdevumjoslā
3. **Laiku pa laikam pārbaudiet progresu** – pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Nelādējiet citas resursietilpīgas programmas** – īpaši, ja izmantojat Chloros+ paralēlo režīmu

### Chloros+ GPU paātrinājums

Ja izmantojat NVIDIA GPU paātrinājumu:

1. Atjauniniet NVIDIA draiverus uz jaunāko versiju
2. Pārliecinieties, ka GPU ir 4 GB+ VRAM
3. Aizveriet programmas, kas intensīvi izmanto GPU (spēles, videomontāža)
4. Uzraugiet GPU temperatūru (nodrošiniet atbilstošu dzesēšanu)

***

## Turpmākie soļi

Kad apstrāde ir sākusies:

1. **Uzraugiet progresu** — skatiet [Apstrādes uzraudzība](monitoring-the-processing.md)
2. **Pagaidiet, līdz apstrāde ir pabeigta** — apstrāde notiek automātiski
3. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)

Informāciju par to, ko darīt apstrādes laikā, skatiet [Apstrādes uzraudzība](monitoring-the-processing.md).
