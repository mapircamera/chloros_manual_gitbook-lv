# Apstrādes sākšana

Kad esat importējis attēlus, atzīmējis kalibrēšanas mērķus un konfigurējis projekta iestatījumus, varat sākt apstrādi. Šī lapa palīdzēs jums sākt Chloros apstrādes procesu.

## Pārstrādes priekšapstrādes pārbaudes saraksts

Pirms nospiest pogu Start, pārbaudiet, vai viss ir gatavs:

* [ ] **Faili importēti** - Visi attēli parādās failu pārlūkprogrammā
* [ ] **Mērķa attēli atzīmēti** - Mērķa kolonna pārbaudīta kalibrēšanas attēliem
* [ ] **Kameru modeļi atklāti** - Kameru modeļu ailē redzamas pareizās kameras
* [ ] **Iestatījumi konfigurēti** - Projekta iestatījumi pārskatīti un pielāgoti
* [ ] **Indeksi izvēlēti** - Pievienoti vēlamie multispektrālie indeksi (ja nepieciešams)
* [ ] **Eksporta formāts izvēlēts** - Jūsu darba plūsmai atbilstošs izvades formāts

{% hint style=&quot;info&quot; %}
**Padoms**: Pirms apstrādes noklikšķiniet uz dažiem attēliem failu pārlūkprogrammā, lai pārliecinātos, ka tie ir pareizi ielādēti.
{% endhint %}

***

## Apstrādes sākšana

### Atrodiet sākšanas pogu

Sākšanas/atskaņošanas poga atrodas Chloros augšējā galvenes joslā:

* Pozīcija: loga augšējā centrā
* Ikona: **Atskaņošanas/sākšanas poga** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">
* Stāvoklis: Poga ir aktivizēta (spilgta), kad ir gatava apstrādei

### Noklikšķiniet, lai sāktu

1. Noklikšķiniet uz **Atskaņot/Sākt pogas** augšējā joslā
2. Apstrāde sākas nekavējoties
3. Poga kļūst neaktivizēta (pelēka) apstrādes laikā
4. Progresa josla atjaunojas, parādot apstrādes stāvokli

{% hint style=&quot;success&quot; %}
**Apstrāde sākta**: Pēc noklikšķināšanas Chloros automātiski veic visus apstrādes posmus — mērķa noteikšanu, debayering, kalibrēšanu, indeksa aprēķināšanu un eksportēšanu.
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

**Progresa josla rāda 2 posmus:**

1. **Mērķa noteikšana** - Kalibrēšanas mērķu skenēšana
2. **Apstrāde** - Kalibrēšanas piemērošana un attēlu eksportēšana

**Apstrādes laiks:**

* Daudz lēnāks nekā Chloros+ paralēlais režīms
* Piemērots maziem un vidējiem datu kopumiem (&lt; 200 attēli)

### Chloros+ režīms (paralēla apstrāde)

**Nepieciešama Chloros+ licence**

**Kā tas darbojas:**

* Vienlaikus apstrādā vairākus attēlus
* Daudzpavedienu darbība (līdz 16 paralēliem darbiniekiem)
* Izmanto vairākus CPU kodolus
* Papildu GPU (CUDA) paātrinājums ar NVIDIA grafiskajām kartēm

**Progresa josla rāda 4 posmus:**

1. **Atklāšana** — kalibrēšanas mērķu meklēšana
2. **Analīze** — attēlu metadatu pārbaude un cauruļvada sagatavošana
3. **Kalibrēšana** — korekciju un kalibrēšanas piemērošana
4. **Eksportēšana** — apstrādāto attēlu un indeksu saglabāšana

**Progresa joslas mijiedarbība:**

* **Pielieciet peles kursoru** uz joslas, lai redzētu detalizētu 4 posmu nolaižamo paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu nolaižamo paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un paslēptu paneli

**Apstrādes laiks:**

* Ievērojami ātrāks nekā bezmaksas režīms
* Mērogs atbilstoši CPU kodolu skaitam
* GPU paātrinājums vēl vairāk uzlabo ātrumu

{% hint style=&quot;info&quot; %}
**Chloros+ ātrums**: Paralēla apstrāde var būt 5–10 reizes ātrāka nekā secīgs režīms lieliem datu kopumiem. 500 attēlu projekts, kas bezmaksas režīmā aizņem 2 stundas, ar Chloros+ var tikt pabeigts 15–20 minūtēs.
{% endhint %}

***

## Kas notiek apstrādes laikā

### 1. posms: mērķa noteikšana

**Chloros darbība:**

* Skenē atzīmētos mērķa attēlus (vai visus attēlus, ja nav atzīmēti)
* Identificē 4 kalibrēšanas paneļus katrā mērķī
* Izgūst atstarojuma vērtības no mērķa paneļiem
* Reģistrē mērķa laika zīmogus kalibrēšanas plānošanai

**Ilgums:** 1–30 sekundes (ar atzīmētiem mērķiem), 5–30+ minūtes (bez atzīmēm)

### 2. posms: Debayering (RAW konvertēšana)

**Chloros funkcijas:**

* Konvertē RAW Bayer modeļa datus pilnā RGB attēlos
* Piemēro augstas kvalitātes demosaicing algoritmu
* Saglabā maksimālu attēla kvalitāti un detaļas

**Ilgums:** Atšķiras atkarībā no attēlu skaita un procesora ātruma

### 3. posms: Kalibrēšana

**Chloros funkcijas:**

* **Vignette korekcija**: noņem objektīva tumšāko daļu malās
* **Atstarošanas kalibrēšana**: normalizē, izmantojot mērķa atstarošanas vērtības
* Piemēro korekcijas visiem diapazoniem/kanāliem
* Izmanto atbilstošu kalibrēšanas mērķi katram attēlam, pamatojoties uz laika zīmogu

**Ilgums:** Lielākā daļa apstrādes laika

### 4. posms: Indeksa aprēķināšana

**Ko dara Chloros:**

* Aprēķina konfigurētos multispektrālos indeksus (NDVI, NDRE utt.)
* Piemēro joslu matemātiku kalibrētiem attēliem
* Ģenerē indeksa attēlus katram izvēlētajam indeksam

**Ilgums:** Dažas sekundes uz attēlu

### 5. posms: Eksportēšana

**Ko dara Chloros:**

* Saglabā kalibrētos attēlus izvēlētajā formātā
* Eksportē indeksa attēlus ar konfigurētām LUT krāsām
* Raksta failus kameras modeļa apakšmapēs
* Saglabā sākotnējos failu nosaukumus ar papildu piedēkļiem

**Ilgums:** Atšķiras atkarībā no eksporta formāta un faila lieluma

***

## Apstrādes darbība

### Automātiskā apstrādes caurule

Pēc palaišanas visa caurule darbojas automātiski:

* Nav nepieciešama lietotāja iejaukšanās
* Visi konfigurētie soļi tiek izpildīti secīgi
* Progresa atjauninājumi tiek parādīti reālajā laikā

### Datoru izmantošana apstrādes laikā

**Brīvais režīms:**

* Salīdzinoši zems CPU izmantojums (vienpakalpes)
* Dators paliek atsaucīgs citām uzdevumiem
* Droši samazināt Chloros un strādāt citās lietojumprogrammās

**Chloros+ Paralēlais režīms:**

* Augsta CPU izmantošana (daudzpavedienu, līdz 16 kodoliem)
* Ar GPU paātrinājumu: augsta GPU izmantošana
* Dators apstrādes laikā var būt mazāk reaģētspējīgs
* Izvairieties no citu CPU intensīvu uzdevumu sākšanas

{% hint style=&quot;warning&quot; %}
**Veiktspējas padoms**: Lai nodrošinātu labāko Chloros+ veiktspēju, aizveriet citas programmas un ļaujiet Chloros izmantot visus sistēmas resursus.
{% endhint %}

### Apstrādi nevar apturēt

**Svarīgi ierobežojumi:**

* Pēc sākšanas apstrādi nevar apturēt.
* Apstrādi var atcelt, bet tās gaita tiek zaudēta.
* Daļējie rezultāti netiek saglabāti.
* Ja apstrāde tiek atcelta, tā jāuzsāk no sākuma.

**Plānošanas padoms:** Ļoti lieliem projektiem apsveriet iespēju apstrādāt tos pa daļām vai izmantot CLI, lai nodrošinātu labāku kontroli.

***

## Apstrādes uzraudzība

Apstrādes laikā varat:

* **Skatīt progresa joslu** — redzēt kopējo pabeigšanas procentu
* **Skatīt pašreizējo posmu** — atklāt, analizēt, kalibrēt vai eksportēt
* **Pārbaudīt žurnāla cilni** — redzēt detalizētus apstrādes ziņojumus un brīdinājumus
* **Priekšskatīt pabeigtos attēlus** — apstrādes laikā var parādīties daži eksporta faili

Sīkāku informāciju par uzraudzību skatiet sadaļā [Apstrādes uzraudzība](monitoring-the-processing.md).

***

## Apstrādes atcelšana

Ja jums ir nepieciešams pārtraukt apstrādi:

### Kā atcelt

1. Atrodiet **poga Stop/Cancel** (apstrādes laikā aizstāj pogu Start)
2. Noklikšķiniet uz pogas Stop
3. Apstrāde tiek nekavējoties pārtraukta
4. Daļējie rezultāti tiek izmesti

### Kad atcelt

**Pamatoti iemesli atcelšanai:**

* Tika izmantoti nepareizi iestatījumi
* Aizmirsts atzīmēt mērķa attēlus
* Importēti nepareizi attēli
* Sistēma darbojas pārāk lēni vai nereaģē

**Pēc atcelšanas:**

* Pārskatiet un novēršiet visus problēmas
* Pielāgojiet iestatījumus pēc nepieciešamības
* Atkārtoti sāciet apstrādi no sākuma
* Lai nodrošinātu vislabāko pieredzi, pilnībā aizveriet Chloros un sāciet no jauna

{% hint style=&quot;warning&quot; %}
**Nav daļēju rezultātu**: Atcelšana izdzēš visu progresu. Chloros nesaglabā daļēji apstrādātus attēlus.
{% endhint %}

***

## Apstrādes laika aprēķini

Faktiskais apstrādes laiks ievērojami atšķiras atkarībā no:

* Attēlu skaita
* Attēlu izšķirtspējas
* RAW vai JPG ievades formāta
* Apstrādes režīma (bezmaksas vai Chloros+)
* CPU ātrums un kodolu skaits
* GPU pieejamība (tikai Chloros+)
* Aprēķināmo indeksu skaits
* Eksporta formāta sarežģītība

### Aptuvenas aplēses (Chloros+, 12 MP attēli, moderns CPU)

| Attēlu skaits | Bezmaksas režīms | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 attēli   | 15–20 min | 5–8 min        | 3–5 min        |
| 100 attēli  | 30–40 min | 10–15 min      | 5–8 min        |
| 200 attēli  | 1–1,5 stundas | 20–30 min      | 10–15 min      |
| 500 attēli  | 2–3 stundas   | 45–60 min      | 20–30 min      |
| 1000 attēli | 4–6 stundas   | 1,5–2 stundas      | 40–60 min      |

{% hint style=&quot;info&quot; %}
**Pirmais palaides reiss**: Sākotnējā apstrāde var ilgt ilgāk, jo Chloros veido kešatmiņas un profilus. Turpmākā līdzīgu datu kopu apstrāde būs ātrāka.
{% endhint %}

***

## Bieži sastopamas problēmas sākumā

### Sākuma pogas ir atspējotas (izbalinātas)

**Iespējamie iemesli:**

* Nav importēti attēli
* Backend nav pilnībā sācies
* Iepriekšējā apstrāde joprojām darbojas
* Projekts nav pilnībā ielādēts

**Risinājumi:**

1. Gaidiet, līdz backend ir pilnībā inicializēts (pārbaudiet galvenā izvēlnes ikonu)
2. Pārbaudiet, vai attēli ir importēti failu pārlūkā
3. Ja poga joprojām ir atspējota, restartējiet Chloros
4. Pārbaudiet Debug Log, vai nav kļūdu ziņojumu

### Apstrāde sākas, bet tūlīt pat neizdodas

**Iespējamie iemesli:**

* Projektā nav derīgu attēlu
* Bojāti attēlu faili
* Nepietiekama diska vieta
* Nepietiekama atmiņa (RAM)

**Risinājumi:**

1. Pārbaudiet debug log <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> kļūdu ziņojumus
2. Pārbaudiet pieejamo diska vietu
3. Mēģiniet apstrādāt mazāku attēlu apakškopu
4. Pārbaudiet, vai attēli nav bojāti

### Brīdinājums &quot;Nav atrasti mērķi&quot;

**Iespējamie iemesli:**

* Aizmirsts atzīmēt mērķa attēlus
* Mērķa attēlos nav redzamu mērķu
* Mērķa noteikšanas iestatījumi ir pārāk stingri

**Risinājumi:**

1. Pārskatiet [Mērķa attēlu izvēle](choosing-target-images.md)
2. Atzīmējiet atbilstošos attēlus kolonnā „Mērķis”
3. Pārbaudiet, vai mērķi ir redzami atzīmētajos attēlos
4. Vajadzības gadījumā pielāgojiet mērķu noteikšanas iestatījumus

***

## Padomi veiksmīgai apstrādei

### Pirms sākšanas

1. **Vispirms veiciet testu ar nelielu apakškopu** — apstrādājiet 10–20 attēlus, lai pārbaudītu iestatījumus
2. **Pārbaudiet pieejamo diska vietu** — nodrošiniet 2–3 reizes lielāku brīvo vietu nekā datu kopas izmērs
3. **Aizveriet nevajadzīgās programmas** — atbrīvojiet sistēmas resursus
4. **Pārbaudiet mērķa attēlus** — apskatiet atzīmētos mērķus, lai pārliecinātos par to kvalitāti
5. **Saglabājiet projektu** — projekts tiek saglabāts automātiski, bet ieteicams to saglabāt arī manuāli.

### Apstrādes laikā

1. **Izvairieties no sistēmas miega režīma** — atspējojiet enerģijas taupīšanas režīmus.
2. **Saglabājiet Chloros priekšplānā** — vai vismaz redzamu uzdevumjoslā.
3. **Laiku pa laikam pārbaudiet apstrādes gaitu** — pārbaudiet, vai nav brīdinājumu vai kļūdu.
4. **Nelādējiet citas smagas programmas** – īpaši, ja izmantojat Chloros+ paralēlo režīmu

### Chloros+ GPU paātrinājums

Ja izmantojat NVIDIA GPU paātrinājumu:

1. Atjauniniet NVIDIA draiverus uz jaunāko versiju
2. Pārliecinieties, ka GPU ir 4 GB+ VRAM
3. Aizveriet GPU intensīvas programmas (spēles, video rediģēšana)
4. Uzraugiet GPU temperatūru (nodrošiniet atbilstošu dzesēšanu)

***

## Nākamie soļi

Kad apstrāde ir sākusies:

1. **Uzraugiet procesa gaitu** - Skatīt [Apstrādes uzraudzība](monitoring-the-processing.md)
2. **Pagaidiet, līdz apstrāde ir pabeigta** — apstrāde notiek automātiski
3. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)

Informāciju par to, ko darīt apstrādes laikā, skatiet [Apstrādes uzraudzība](monitoring-the-processing.md).
