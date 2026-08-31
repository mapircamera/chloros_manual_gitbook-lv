# Daudzkameru sistēmas

LATTICE **sistēma**ir divas vai vairākas LATTICE kameras, kas savienotas kā viena sinhronizēta vienība. Viena kamera ir**galvenā**: tā nosūta aparatūras GPIO trigera impulsu pa kopīgu sinhronizācijas līniju (noklusējumā**Line2**), tādējādi katra sistēmas sastāvdaļa fiksē vienu un to pašu mirkli. Chloros pievieno PTP laika sinhronizāciju, tiešraides priekšskatījumu (atsevišķi attēli no katras kameras vai viens saskaņots daudzjoslu kompozīts attēls), un sinhronizētu uzņemšanu — katrs uzņemšanas cikls rada vienu**kadru grupu**, kurā visām kamerām ir viens un tas pats laika zīmogs un kadra ID (uzņemšanas izvades datu plūsmā tiek ziņots kā `fid:N`).

Masīvi ir veids, kādā mono (M3M) kameras ģenerē veģetācijas indeksus — viena kamera nodrošina vienu joslu, un masīvs tās saskaņo daudzjoslu kopā. Skatīt [Mono kameras un veģetācijas indeksi](mono-indices.md).

Ir trīs līdzvērtīgi veidi, kā savienot masīvu, un visos trijos tiek izpildīta viena un tā pati „smart-prep” plūsma:

| Virsma | Ieejas punkts |
| --- | --- |
| GUI | cilne „Kameras“ → **Pievienot masīvu** (zila poga) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (pirmais sērijas numurs = galvenā kamera) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (pirmais sērijas numurs = galvenais) |

Smart-prep veic šādus pasākumus secībā: tīkla iespēju pārbaudi (ICMP DF ping + GVSP pārbaude), sinhronizācijas līmeņa izvēli, rāmja izmēra automātisku samazināšanu, lai tas atbilstu vadu kapacitātei, PTP ieslēgšanu, pikseļu formāta automātisku izvēli katrai kamerai, automātiskās ekspozīcijas sākotnējo iestatīšanu, balstoties uz katras kameras saglabāto stāvokli, un GPIO trigera konfigurāciju Line2.

{% hint style="info" %}
Lai šīs funkcijas darbotos, kamerām jābūt sasniedzamām pa savienojumu — skatiet [Kameru pieslēgšana](connecting.md), lai uzzinātu par atklāšanu, adresēšanu un pirmās savienošanas kalibrēšanas lejupielādi. Daudzkameru sistēmās galvenā tīkla kartes (NIC) uztveršanas gredzena iestatījumi ir tikpat svarīgi kā savienojuma ātrums; pilnīga simptomu→risinājumu tabula atrodama [CLI Atsauce § Galvenās tīkla kartes (NIC) iestatīšana un optimizēšana](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialogs „Array Connect“

Cilne „Cameras“ → **„Connect Array“**atver trīs soļu vedni:**„Select“ → „Display Mode“ → „Settings“**.

### 1. solis — Izvēlieties galveno kameru un pakārtotās kameras

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered. Table showing Camera / Serial / IP / Master radio / Slave checkbox columns, with the green "GPIO master detected — selections pre-populated" probe banner visible above the table. -->

Tiklīdz dialoglodziņš tiek atvērts, tas sāk skenēt tīklu („Scanning network...“), pēc tam pārbauda GPIO trigera vadu savienojumus („Probing GPIO wiring...“). Lai izveidotu masīvu, ir nepieciešamas vismaz **2 kameras**.

Savienojumu pārbaude, ja iespējams, iepriekš aizpilda lomu izvēli un parāda vienu no trim paziņojumiem:

| Paziņojums | Nozīme |
| --- | --- |
| „Atklāts GPIO galvenais modulis — izvēles iepriekš aizpildītas“ (zaļš) | Pārbaude ir atklājusi trigera topoloģiju; izvēles rūtiņas „galvenais modulis“ un „pakļautie moduļi“ jau ir atzīmētas. |
| „Galvenais modulis nav atklāts — pārbaudiet GPIO kabeli“ (oranža) | Neviena kamera nesaņēma trigera impulsu; pārbaudiet sinhronizācijas vadu savienojumus. Jūs joprojām varat izvēlēties lomas manuāli. |
| „Nav sinhronizācijas kabeļa: {sērijas numuri}“ (oranža) | Uzskaitītajām kamerām nav pievienots sinhronizācijas kabelis. |

Kameru tabulā ir šādas kolonnas: **Kamera / Sērijas numurs / IP / Galvenā (radio) / Pakļautā (izvēles rūtiņa)**:

* Izvēlieties tieši **vienu galveno**un**vienu vai vairākas pakļautās**. Atkārtoti noklikšķinot uz pašreizējā galvenā radio, tā atzīme tiek dzēsta.
* Kameru, kurai ir atzīme **„Nav sinhronizācijas kabeļa“**, nekad nevar izvēlēties kā pakļautu — pakļautā kamera bez trigera vadu savienojuma bezgalīgi gaidītu sinhronizācijas līnijā un nodrošinātu nefunkcionālu attēlu. Tā vietā šo kameru pieslēdziet kā autonomu kameru.
* Kameras, kas jau ir pieslēgtas kā patstāvīgas, *netiek* atspējotas: masīva savienošana atbrīvo patstāvīgo sesiju un atkārtoti atver kameru masīvā.

**Tālāk: Rādīšanas režīms →**kļūst pieejams, tiklīdz ir izvēlēts viens galvenais un vismaz viens pakļautais.**Atkārtota skenēšana** atkārtoti palaista atklāšana un vadu pārbaude.

{% hint style="warning" %}
**Atcelt** ir atspējots, kamēr notiek skenēšana vai pārbaude — atcelšana pārbaudes vidū var izraisīt kameras SDK avāriju LATTICE kameras programmaparatūrā. Gaidiet, līdz rotējošais indikators apstājas.
{% endhint %}

### 2. solis — Ekrāna režīms

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->

| Režīms | Kas tiek parādīts |
| --- | --- |
| **Atsevišķas kameras** | Viena reāllaika flīze katrai kamerai, visas tiek aktivizētas vienlaikus, lai kadri paliktu sinhronizēti. Katra kamera saglabā savu krāsu un iestatījumus. |
| **Apvienotas kameras** *(noklusējums)* | Viena flīze, kas attēlo saskaņoto daudzjoslu NDVI/indeksa kompozīciju. Kamerām ir kopīga matricas krāsa. |

Attēlošanas režīms maina tikai tiešraides priekšskatījuma attēlojumu — ierakstīšanas darbība abos režīmos ir vienāda.

### 3. solis — Masīva iestatījumi un prognozētais rezultāts

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Pin resolution / Trigger Rate controls, right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->

Ieejot šajā ainā, Chloros pieprasa no aizmugures sistēmas **ieteikumu**un automātiski piemēro ROI + binninga kombināciju, kas atbilst jūsu NIC uztveršanas gredzenam (tā dod priekšroku binningam, nevis ROI apgriešanai, jo binning saglabā pilnu redzes lauku). Katra izdarītā izmaiņa no jauna palaida analīzi reāllaikā un atjaunina labajā pusē esošo**Prognozētais rezultāts** paneli.

Kreisā kolonna — iestatījumi:

| Kontrole | Izvēles | Noklusējums | Piezīmes |
| --- | --- | --- | --- |
| **ROI (redzes lauks)** | Pilns (2048×1536) / Puse (1024×768) / Ceturtdaļa (512×384) | Pilns | Sensora apgriešana: apgriešana uz pusi vai ceturtdaļu, lai iegūtu mazāku reģionu ar sākotnējo pikseļu izkārtojumu. |
| **Pikseļu apvienošana** | 1× / 2× (summa 2×2) / 4× (summa 4×4) | 1× | Aparatūras pikseļu apvienošana: 2×2 = pilns redzes lauks par ceturto daļu no datu pārraides izmaksām; 4×4 = pilns redzes lauks par 1/16. Paslēpts, ja kameras neatbalsta binning. |
| **Vadu puses attēls** (nolasīšana) | — | — | Pēc binninga platums × augstums, kas faktiski tiek nosūtīts pa vadu, noapaļots līdz 16 reizinājumiem (minimums 64). |
| **Pinu izšķirtspēja**| atzīme | izslēgts | Chloros parasti automātiski aktivizē binningu savienojuma izveides brīdī, ja prognozētais pārraides ātrums nokrīt zem**1,5 fps**. Pinning saglabā izvēlēto kadra izmēru un pieņem zemāko ātrumu — un pārslodzētu konfigurāciju pārvērš par stingru savienojuma atteikumu, nevis automātisku ātruma samazināšanu. |
| **Trigera ātrums** | 0,5–60 fps, solis 0,1 | tukšs = automātiski | Galvenā ierīces trigera izšaušanas ātrums. Atstājiet tukšu, lai Chloros to aprēķinātu pats. |
| **Vadu joslas platums**| 20–2000 MB/s, solis 10 | tukšs = automātiski | Cik daudz saimniekdators faktiski spēj apstrādāt, izteikts MB/s —**vienīgais skaitlis, no kura ir atkarīga visa masīva resursu sadale.** Automātiski noteikts no tīkla adaptera. Samaziniet to, ja masīvs ziņo par bojātiem rāmjiem: noteiktā vērtība pārspīlē USB adapteru un koplietošanas komutatoru jaudu. Mainot to, prognoze tiek atkārtoti aprēķināta reāllaikā. |

Labā kolonna — **Prognozētais rezultāts**:

* **Sinhronizācijas līmenis** — „Vienlaicīga uzņemšana“ (zaļš), „Vienlaicīga uzņemšana (FTD — pakāpeniska izlaide)“ (zaļš), „Pakāpeniska uzņemšana (100 ms nobīde)“ (dzeltens) vai „Konfigurācija pārāk liela“ (sarkans).
* **fps prognoze** — parādīta kā diapazons („vājš → spilgts”), jo sinhronizētā masīva ātrums ir atkarīgs no lēnākās kameras ekspozīcijas.
* **NIC līnija** — savienojuma ātrums un ilgstošā caurlaidspēja („NIC {mbps} Mbps · ilgstošā {N} MB/s”).
* **Simultānas emisijas pārsūtīšanas pārbaude** — vai uzņēmējdatora tīkla kartes gredzens spēj uzņemt vienu vienlaicīgu pārsūtīšanas plūsmu no visām kamerām („Simultāna emisija: X MB · izmantojamais tīkla kartes gredzens: Y MB ✓/✗”).
* **Vadu budžeta pārbaude** — stabilā stāvoklī kopējais pieprasījums salīdzinājumā ar sadursmju drošo vadu maksimālo jaudu („Vadu budžets: {pieprasījums} MB/s, ko pieprasa {n} kameras · maksimālā jauda {ceiling} MB/s ✓/✗ pārsniegts”).
* **„Maksimālais kameru skaits šajā vadā: {n} — noteikts atbilstoši minimālajai joslas platībai katrai kamerai, tādēļ grupēšana to nepalielina.”** — parādās, ja esat tuvu (vai pārsniedzat) kameru skaita ierobežojumu.
* **„Ar šiem iestatījumiem KADRI TIKS ZAUDĒTI.”**— sarkans brīdinājums ar aizmugurējās sistēmas iemeslu, kā arī bloķētāju sarakstu un ziliem**risinājumu ieteikumiem** („Lai šo masīvu pielāgotu tīklam” / „Lai atbloķētu vienlaicīgu uzņemšanu”).**Piemērot un savienot** ir bloķēts, kamēr nav prognozes, un tā uzraksts norāda, kāpēc tiek atteikts:

| Poga | Nozīme | Kas patiešām palīdz |
| --- | --- | --- |
| „Analizē...” | Analīze vēl notiek. | Gaidiet. |
| **„Pārāk daudz kameru šim tīklam”**| Masīvs pārslogo tīkla kanālu (kopējā pārbaude neizdevās). | Mazāk kameru, „jumbo” rāmji no gala līdz galam vai ātrāks tīkla interfeiss.**Mazāks ROI NEPALĪDZ** — skatiet zemāk. |
| **&quot;Samaziniet ROI, lai aktivizētu&quot;** | Ar šiem iestatījumiem kadri tiktu zaudēti (neizdevās pārbaudīt pārraides plūsmu/gredzenu). | Samaziniet ROI, palieliniet binningu vai salabojiet tīkla kartes uztveršanas gredzenu. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->

Savienošanās laikā var parādīties zaļš **kalibrēšanas lejupielādes logs** ar progresijas joslu katram seriālajam savienojumam: pirmoreiz, kad kamera tiek pieslēgta datoram, Chloros lejupielādē no kameras aptuveni 3,8 MB lielu rūpnīcas kalibrēšanas paketi pa GigE (aptuveni 70 sekundes uz vienu kameru). Cachingā saglabātās kameras šo paneli nekad neparāda. Skatīt [Kameru pieslēgšana](connecting.md).

## Pārraides joslas platums: cik daudz kameru var pieslēgt

To, cik daudz kameru var pārvadīt, nosaka vadu īpašības, nevis Chloros, tāpēc plānošanas dati ir atrodami aparatūras rokasgrāmatā: **[Masīva joslas platuma plānošana](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Kā Chloros ar tiem rīkojas: savienošanas dialoglodziņā tiek palaista tīkla pārbaude, tiek prognozēts sasniedzamais kadru ātrums un izvēlēts atbilstošs līmenis. Ja masīvs pārslogo vadu, tas atsakās izveidot savienojumu, nevis klusi izmet paketes — skatiet iepriekš aprakstīto prognozēto rezultātu paneli.

## Kad pazūd kadri

Kamera var nebūt iekļauta publicētajā grupā divu pilnīgi atšķirīgu iemeslu dēļ,
un tiem nepieciešami pretēji risinājumi. Chloros tos uzskaita atsevišķi, nevis ziņo par vienu
„nepilnīgu” skaitli, kas nenorāda ne vienu, ne otru:

| Kas notika | Ko tas nozīmē | Kur meklēt |
| --- | --- | --- |
| **Sabojāts**— kadrs ieradās, bet tā struktūra bija bojāta | GVSP paketes zudums tīkla ceļā |**Vadu budžets**, tīkla kartes uztveršanas gredzens, jumbo kadri, komutators |
| **Nekad nav ieradies**— rāmis vispār nav ieradies | Kamera nav iedarbinājusies vai no tās nekas nav izgājis |**M8 sinhronizācijas kabelis**, sinhronizācijas līnija, vai visi dalībnieki ir ieslēgti |

Sadale tiek pārvērtēta ik pēc 10 sekundēm, kamēr masīvs pārraida datus. Ja tā pārsniedz 5 %, tas tiek
reģistrēts, norādot abus skaitļus, un par katru bojāto buferi tiek ziņots pirmoreiz, kad tas
notiek katrai kamerai, pēc tam dati tiek apkopoti reizi minūtē, lai ilgstoša sesija paliktu lasāma.

**Bojāti kadri ar nulles „nekad nav ieradušies” rādītāju nozīmē, ka izraisīšana un kabeļa sinhronizācija darbojas nevainojami**un katrs zaudētais kadrs atrodas tīkla ceļā. Risinājums ir samazināt**vadu budžetu** un
atjaunot savienojumu.

{% hint style="warning" %}
**Trigera ātruma samazināšana nepalīdz novērst bojātos kadrus.** Kameras pakešu
pacing tiek iestatīts vienreiz, savienojuma izveides brīdī. Trigera ātruma samazināšana maina to, cik bieži notiek pārraides
sērija, nevis to, cik ātri pati sērija nonāk vadā. Uz izmērītas 4 kameru iekārtas
izraisīšanas ātruma samazināšana 5 reizes neko nemainīja, savukārt vadu budžeta samazināšana no 240 līdz
200 MB/s samazināja bojāto rāmju īpatsvaru tajā pašā iekārtā no 10,4 % līdz nullei.
{% endhint %}

Darbojošs masīvs nevar pats sevi pārplānot — atvienojieties un atkārtoti izveidojiet savienojumu, lai savienošanās laika
izvēlne varētu darboties atbilstoši jaunajam budžetam.

### USB tīkla adapteriem ir ierobežojums 200 MB/s

USB Ethernet adapteris norāda savu *Ethernet* savienojuma ātrumu, taču to, ko tas faktiski
spēj uzturēt, ierobežo USB šķautne un tās draiveris. USB 10GbE adapterim agrāk tika piedēvēta
aptuveni 1000 MB/s caurlaidspēja — skaitlis, ko neviens nekad nebija izmērījis — un,
četrām kamerām, balstoties uz šo iedomāto rezervi, izraisīja 6–18 % kadru bojājumus, kamēr sistēma
joprojām rādīja pareizu mērķa kadru ātrumu. USB pieslēgtajiem adapteriem tagad ir noteikts ierobežojums
**200 MB/s**. Šis ierobežojums ir absolūts, nevis procentuāls, jo ierobežojošais faktors ir
šīna: USB 1 GbE adapteris sasniedz apmēram 80 MB/s, un tas netiek ietekmēts.

Ja jūsu galvenais dators patiešām darbojas ātrāk par šo ierobežojumu, palieliniet **Wire Budget**, lai to norādītu.

## PTP laika sinhronizācija

Kadru *sinhronizācija* notiek, izmantojot aparatūras trigeri; **PTP** (IEEE 1588 PTPv2) nodrošina salīdzināmus *laika zīmogus* visās ierīcēs. Tas ir ieslēgts pēc noklusējuma, kad matrica tiek pieslēgta:

* **Chloros**uzņēmējdatoru backend darbojas kā PTP grandmaster**. LATTICE kameras un DAQ-E gaismas sensori darbojas kā tā pakalpiņi 0. domēnā, tādējādi attēlu laika zīmogi un DAQ spektri tiek sinhronizēti ar vienu pulksteni (~1 ms).
* `--no-ptp` (CLI) to atslēdz darbam uz darba galda — tādā gadījumā laika zīmogi starp kamerām **nav** salīdzināmi.
* Pārbaudiet sinhronizācijas stāvokli ar CLI:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Pašā cilnē „Cameras” nav PTP indikatora; tur par katru kameru tiek parādīti tikai lasāmi lauki **Role**(Master/Slave),**Sync Line** un masīva iespēju līmenis. DAQ-E PTP stāvoklis tiek parādīts cilnes „Light Sensors” sensoru informācijā.

## Masīva skats

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows, and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

reāllaikā Galvenajā attēla apgādes zonā ir pieejami divi izkārtojumi (pārslēdzami augšējā joslā): **tīkla skats**(katra flīze ir šūna; pārkārtot var, velkot, ja tīkla atslēga ir atbloķēta) un**saraksta skats**(masīvi pilnā platumā augšā, viena aktīvā kamera zemāk). Slīdnis**Feed Zoom** maina flīžu izmēru; ja šūnas platums ir mazāks par 200 pikseļiem, nosaukuma un kadru skaita pārklājumi automātiski paslēpjas.**Atsevišķais režīms** parāda vienu flīzi katrai kamerai. Katrā logā tiek parādīts:

* kameras nosaukums (kreisajā augšējā stūrī),
* **kadru skaita (fps) rādījums** (labajā augšējā stūrī) — tas ir kameras *faktiskais kadru uzņemšanas ātrums*, ko ziņo serveris, nevis priekšskatījuma atjaunošanas ātrums (tiešraides priekšskatījums ir ierobežots līdz 30 fps neatkarīgi no kadru uzņemšanas ātruma),
* statusa punktiņu — zaļš (straumēšana) / dzeltens (iekraušana) / sarkans (kļūda),
* **novecojušā kadra rotējošo ikonu**, ja 2 s laikā nav saņemts jauns kadrs — tas ir normāli apmēram 5 s pēc jebkuras savienošanās/atsavienošanās, kamēr backend pārbalansē datu pārraides resursus starp kamerām.**Kombinētajā režīmā**tiek parādīts viens salikts laukums: backend veic debayeringu, mērogu pielāgošanu, izlīdzināšanu, trokšņu noņemšanu, konvertēšanu uz starojuma intensitāti katrā joslā (plus DLS atstarošanas koeficients, ja ir pieslēgts gaismas sensors), izvērtē masīva indeksa izteiksmi, piemēro LUT un straumē rezultātu kā MJPEG. Līdz brīdim, kad tiek renderēts pirmais izlīdzinātais kadrs, logiņš norāda savu stāvokli: „Sagatavojot masīvu…”, „Kalibrējot izlīdzinājumu…”, „Gaida pirmo kadru…”, vai — ja automātiskās izlīdzināšanas atkārtošanas laiks (~30 s) ir izsmelts — „Nepieciešama izlīdzināšana” ar pogu**Kalibrēt izlīdzināšanu**.

Noderīga informācija par kombinēto režīmu:

* Kompozīcija tiek sinhronizēta ar **galvenās**kameras kadru. AE-ROI mērķēšana un punktu ekspozīcijas mērīšana uz kompozīcijas ir precīza galvenajai kamerai un aptuvena palīgkamerām; izmantojiet**Dalīto skatu** (masīva iestatījumi → „Rādīt dalībkameras”), lai iegūtu pikseļu precizitātes mozaīkas katrai kamerai atsevišķi, neizveidojot papildu kameru savienojumus.
* **Display Layers**(masīva iestatījumi; noklusējumā izslēgts) ļauj izvēlēties priekšplāna un fona slāni — jebkuru dalībkameru vai**Index**. Ja priekšplāns = Index, pikseļi ārpus LUT minimālā/maksimālā diapazona parāda fona slāni.
* **Render resolution** (noklusējumā 720p) nosaka tiešraides augstumu *un* saglabātā kompozīcijas eksporta izmēru. Attēli no katras kameras vienmēr tiek eksportēti pilnā izšķirtspējā.
* Izlīdzināšana tiek aprēķināta katrā sesijā un netiek saglabāta — skatiet masīva iestatījumu paneļa sadaļu par izlīdzināšanu, lai redzētu RMS atlikumus un pogu „Recalibrate”.

## Ierakstīšana: uzraudzība pret analīzi

Masīva ierakstīšanas virsmas skaidri sadalās **uzraudzības līmenī**(ieraksta to, ko redzat) un**analīzes līmenī** (ieraksta neapstrādātus datus, kalibrē vēlāk):

| Darba plūsma | Līmenis | Kas tiek saglabāts | Lietotāja saskarne | CLI |
| --- | --- | --- | --- | --- |
| **Ierakstīšana**(nekustīgi attēli) | Analīze | Viena sinhronizēta kadru grupa katrā ciklā; faili katrai kamerai katrā izvēlētajā eksporta līmenī (neapstrādāti/bez debayeringa/starojums/atstarošanās/priekšskatījums/indekss) + `.daq` papildu fails |****Uztvert visu** poga + Uztveršanas iestatījumi | `lattice array-capture` |
| **Ierakstīt indeksa video** | Uzraudzība | Rādītais kombinētais indeksa kompozīts reāllaikā — 8 bitu, priekšskatījuma izšķirtspēja, iebūvēta LUT; nepieciešams atvērt reāllaika straumi | ● Ierakstīt indeksa video (apvienoti masīvi) | `lattice array-record` |
| **Neapstrādāta sērija → izveidot video**| Analīze | Neapstrādāti sensora kadri ar pilnu uzņemšanas ātrumu + manifests + `.daq`, pēc tam bezsaistē rekonstruēti kā kalibrēts starojuma / atstarošanas / indeksa video, laika ziņā saskaņots ar DAQ rādījumiem | ⦿ Ierakstīt neapstrādātu sēriju →**Izveidot video** | `lattice array-burst` → `lattice array-build-video` |

Praktisks padoms: ja pikseļi tiks izmantoti *mērījumiem*, izmantojiet uzņemšanu vai sēriju (analīzes kvalitāte); ja jums vienkārši nepieciešams *noskatīties vai parādīt*, ko redzēja matrica, ierakstiet indeksa video (uzraudzības kvalitāte).

### Ierakstīšanas iestatījumi (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->

Rīks blakus **Capture All** atver logu „Capture Settings” (nepieciešams atvērts projekts — ieraksti tiek saglabāti tajā):

* **Ierakstīšanas režīms**:**Single**(viens cikls) /**Continuous**(pārtraukuma bez pārtraukuma; ierobežots ar uzņemšanas skaitu, noklusējums 1, vai ilgumu, noklusējums 10 s) /**Intervāls** (laika nobīde: N uzņēmumi ik pēc X intervāla, kopā Y, noklusējums 1 ik pēc 5 s 1 minūtes garumā).
* **Eksporta veidi katrai kamerai**: Raw, Debayered, Radiance, Reflectance, Preview, Index — pēc noklusējuma ir ieslēgti visi piemērojamie veidi. Radiance/Reflectance ir paslēpti kamerām ar RGB-filtru;**Reflectance parādās tikai tad, ja kamerai ir DAQ gaismas sensors** (pašas vai mantotas no masīva); Index prasa konfigurētu indeksa izteiksmi.
* **Aligned**(katram masīvam, noklusējumā**ieslēgts**): deformē elementu eksportus atbilstoši masīva izlīdzināšanas profilam, lai eksporti būtu pikseļu līmenī reģistrēti. Raw vienmēr paliek nedeformēts, bet transformāciju nes metadatos.
* **Ātrākā uzņemšana** (slēdzis): tikai neapstrādāti dati + piešķirtais DAQ rādījums + bezmaksas kombinētā indeksa kompozīcija, izlaižot kalibrēšanas aprēķinus uzņemšanas brīdī, lai sasniegtu maksimālo ātrumu — vēlāk atjaunojiet starojumu/atstarošanos/indeksu no saglabātā `.daq`.
* Atlase saglabājas kopā ar projektu. Slēptās vai apturētās kameras tiek izlaistas.

Līdzvērtīgais CLI (tāds pats aizmugures galapunkts, tāda pati semantika):

```bash
# One synced group, every applicable export level per camera (the default)
chloros-cli lattice array-capture -o output/

# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# 30-second monitoring clip of the combined index view, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# 5-second analysis-grade raw burst, then build the combined index video
chloros-cli lattice array-burst --duration 5 --build --products combined:index --fps 10 -o capture/
```

TIFF saspiešana uzņemumiem ir `deflate` (bez zudumiem, noklusējums) vai `none` — pilnās karogu tabulas, uzņemumu mapes izkārtojums un pārstrādes noteikumi atrodas [CLI atsauces dokumentā](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## DAQ gaismas sensora pārošana

Lai iegūtu atspoguļojuma un apgaismojuma korekciju priekšskatījumus, ir nepieciešami no DAQ sensora (kas pieslēgts cilnē **Gaismas sensori**) iegūtie lejupvērstās gaismas dati:

* Sānu joslā **matricas rindā**redzams**&quot;DAQ · ieslēgts/izslēgts&quot; pogu** — tā ir *ieslēgta*, ja ir iestatīts matricas līmeņa gaismas sensors **vai** kādai no kamerām ir savs sensors; rīkjoslā ir precīzi norādīts, kurš sensors nodrošina datus konkrētajai kamerai.
* Iestatiet visai masīvai kopīgus parametrus masīva iestatījumos → **Vides gaismas sensors**→**Gaismas sensors** nolaižamajā izvēlnē. Izvēle saglabājas kopā ar projektu, tiek piemērota visām masīva kamerām, un atsevišķas kameras joprojām var to pārrakstīt ar savu sensoru.
* Statusa rinda zem tās parāda pašreizējo stāvokli: **Izslēgts**→ „Gaida pirmo spektru…” →**„Aktīvs — visām masīva kamerām ir veikta apgaismojuma korekcija”** → vai, ja pēdējo 3 s laikā nav saņemts jauns spektrs, parādās paziņojums par novecojušiem datiem — turpina izmantot pēdējo nolasījumu (rādījumi uzņemšanas ceļā nekad nezaudē derīgumu).

Ja sensors ir piešķirts: kļūst pieejams eksporta veids „Reflectance” (atstarošana), reāllaika priekšskatījumi tiek koriģēti atbilstoši apgaismojumam, prognozējošā automātiskā ekspozīcija var izmantot spektru, un katra atstarošanas uzņemšana pie attēla ieraksta faktiski izmantoto DAQ rādījumu kā **`.daq` sidecar** blakus attēlam, lai uzņemumu vēlāk varētu pārstrādāt.

## `array-connect` CLI opcijas

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automātiski atklāt visas LATTICE kameras (nepieciešamas ≥2) | **Pirmais sērijas numurs ir MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinhronizācijas līnija. |
| `--target-fps F` | automātiski | Galvenā slēdža izšaušanas ātrums. |
| `--binning {1,2,4}` | automātiski | Aparatūras binning. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | automātiski | Sinhronizācijas līmeņa izvēlētāja pārrakstīšana ekspertu režīmā. |
| `--wire-ceiling-mbps MB_PER_S` | automātiski noteikts | Vada budžets MB/s — **Vada budžeta** lauka forma CLI. Samaziniet to, ja masīvs ziņo par bojātiem rāmjiem. Tiek saglabāts kopā ar projektu, tādējādi vēlākā atkārtotā savienošanās to atjauno. |
| `--no-recommend` | izslēgts | Izlaist tīkla analīzes posmu. |
| `--no-ptp` | izslēgts | Atspējot PTP (tad kameru savstarpējie laika zīmogi nav salīdzināmi). |

`lattice array-list`, `array-status` un `array-disconnect` pārvalda pastāvīgo sesiju. Pilnīga apakškommandu atsauces informācija, ieskaitot saskaņošanu (`align-calibrate` / `align-apply`) un tīkla rīkus, atrodama [CLI Atsauces § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); SDK ekvivalenti (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) atrodas [SDK atsaucē](../reference/sdk-reference.md). Sākot ar Python, vadu budžets ir `connect_array(..., wire_ceiling_mbps=120)`, un sadalījums starp bojātajiem un nepiegādātajiem vadiem atrodas [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
