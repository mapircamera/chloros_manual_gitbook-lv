# Kameru iestatījumi

Cilne **Kameras**ir Chloros reāllaika vadības panelis LATTICE kamerām: galvenā attēla apskates zona, kurā katra pieslēgtā kamera tiek parādīta kā reāllaika logiņš, un sānu josla, kurā var pārslēgties starp trim lapām —**kameru sarakstu**,**iestatījumu paneļu**(katras kameras, masīva vai uzņemšanas iestatījumi — pa vienam), un**indeksa kalkulators**. Šajā lapā ir aprakstīti visi vadības elementi kameru sarakstā, katras kameras iestatījumu paneļā un masīva iestatījumu paneļa. Uztveršanas režīmi, eksporta veida izvēle un darbplūsma „Capture All” atrodas papildu lapā [Uztveršanas iestatījumi un režīmi](capture.md).

Kad Chloros aizmugurējā sistēma ir gatava, sānu joslā parādās cilne „Kameras”. Visi zemāk minētie vadības elementi sazinās ar vietējo aizmugurējo sistēmu, izmantojot `127.0.0.1:5000`; izmaiņas nekavējoties tiek piemērotas reāllaika kamerai, ja vien nav norādīts citādi.

## Šajā lapā izmantotie kameru veidi

Vadības elementi parādās vai paslēpjas atkarībā no izvēlētā kameras veida. Rokasgrāmatā visā tekstā tiek izmantoti šādi termini:

| Termins | Nozīme | Filtra kanāli |
| --- | --- | --- |
| **RGB kamera** | LATTICE M3C ar FRGB filtru (modelī ietilpst `-FRGB`) | Red / Green / Blue |
| **Bayer multispektrālā** | LATTICE M3C ar FRGN, FOCN vai FNGB | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — viens šaurjoslas filtrs, viena kalibrēta josla | Viena josla |
| **Masīva elements** | Kamera, kas pieslēgta kā daļa no sinhronizēta masīva (apvienots vai atsevišķs displejs) | Atbilstoši tās filtram |

RGB kamerām tiek veikta fotometriskā apstrāde (balansa iestatīšana, krāsu profili, gamma); daudzspektrālajām un mono kamerām tiek piemērota radiometriskā ķēde, un fotometriskās kontroles tiek izlaistas. Masīva elementi nodod straumes līmeņa iestatījumus (pikseļu formāts, izšķirtspēja, binning, trigers, kadru ātrums) masīvam — šīs rindas kļūst tikai lasāmas atsevišķās kameras logā un tiek pārvietotas uz masīva iestatījumu logu.

## Galvenā plūsmas zona

<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Ja nav pieslēgtas nekādas kameras, plūsmas zonā tiek parādīts sākuma ekrāns **„Pieslēdziet kameru, lai sāktu”**ar divām pogām:**Pieslēgt kameru**(zaļa, atver vienas kameras pieslēgšanas dialoglodziņu) un**Pieslēgt masīvu** (zila, atver masīva pieslēgšanas dialoglodziņu). Paši savienošanas dialoglodziņi ir aprakstīti sadaļā [Kameru savienošana](connecting.md); masīva jēdzieni (sinhronizācija, līmeņi, joslas platums) — sadaļā [Daudzkameru masīvi](arrays.md). Kad atverat saglabātu projektu, kurā ir kameras, sākuma ekrānā tiek parādīts griežamā ikona ar uzrakstu „Atveru N saglabātās kameras…”, kamēr Chloros atjauno straumes no pēdējās sesijas.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Augšējā josla

| Vadības elements | Funkcija |
| --- | --- |
| **Skata režīma pārslēgšana**| Pārslēdzas starp**tīkla skatu**(visi laukumi kā šūnas) un**saraksta skatu** (masīvi pilnā platumā augšā, VIENA aktīvā kamera zemāk). Rādītāji: „Pārslēgties uz tīkla skatu” / „Pārslēgties uz saraksta skatu”. |
| **Tīkla bloķēšana**(atslēga) | Noklusējumā**bloķēts** — logi ir fiksēti savās vietās. Atbloķējiet, lai pārvietotu logus uz jebkuru vietu (starpības tiek saglabātas). Tīkls automātiski atkal bloķējas katru reizi, kad pieslēdzas jauna kamera. Rīku padomi: „Atbloķēt tīklu (iespējot logu pārvietošanu)” / &quot;Bloķēt režģi (fiksēt flīzes to vietās)&quot;. |
| **Plūsmas tālummaiņas** sliders | Flīzes izmērs — no 60 pikseļiem līdz pilnam konteinera platumam. Šūnas saglabā 4:3 proporcijas. Ja šūnas platums ir mazāks par 200 pikseļiem, nosaukums un kadrskaitis (fps) tiek paslēpti, lai flīze izskatītos tīra. |

### Plūsmas flīzes

Katra kamera renderē saliktu tiešraides flīzi; kamera var papildus parādīt trīs pelēktoņu **kanālu sadalījuma** flīzes (skatīt [Kanālu sadalījumi](#display-overlays-drawn-over-the-live-feed)), un masīvi renderē apvienotu flīzi. Aktīvajā flīzē ir atlases gredzens kameras (vai masīva) krāsā.

Pārvietojot kursoru pār flīzi, parādās **X** aizvērtnes poga:

* Aizverot **kompozīcijas** flīzi, kamēr tās kanālu sadalījumi paliek redzami, tiek paslēgta tikai kompozīcija.
* Aizverot **pēdējo redzamo flīzi no atsevišķas kameras**, šī kamera tiek atvienota.
* **Apvienotā masīva dalīto flīžu slēgšana nekad neatvieno** kameru — tās tikai paslēpj.

Kad režģis ir atbloķēts, velciet jebkuru flīzi uz jebkuru vietu; izkārtojums tiek saglabāts kopā ar projektu.

## Sānu josla — kameru saraksts

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

Pirmajā sānu joslas lapā ir uzskaitītas visas pieslēgtās kameras un masīvi:

* **Pieslēgt kameru**(zaļa) /**Pievienot masīvu** (zils, skenēšanas laikā parāda „Detecting...“). Abi ir atspējoti, kamēr ir atvērts savienošanas dialoglodziņš.
* **Ierakstīt visu** (sarkans) — ieraksta katru sarakstā iekļauto kameru ar eksporta veidiem, kas izvēlēti iestatījumos „Capture Settings“. Nepieciešams atvērts projekts. Pilnībā dokumentēts sadaļā [Capture Settings &amp; Modes](capture.md).
* **Ierakstīšanas iestatījumu ratiņš** (blakus „Ierakstīt visu”) — atver [Ierakstīšanas iestatījumu logu](capture.md#the-capture-settings-pane). Ir atspējots, ja nav projekta vai ierakstīšanas laikā.

### Kameru rindas

Katrā kameras rindā redzama krāsā iezīmēta robežlīnija (kamerai iestatītā krāsa), uzraksts „CAM“ — ar zilu **M**(galvenā) vai zaļu**S** (slave) — un parādīto nosaukumu. Noklusējuma nosaukums ir `LATTICE-MODEL (serial)`; to var pārdēvēt kameras iestatījumu paneļā. Rindu pogas:

| Poga | Efekts |
| --- | --- |
| **Acs**| Pārslēgt redzamību. Paslēptās kameras pazūd no režģa un tiek**izslēgtas no funkcijas „Capture All”**. |
| **Ritenītis** | Atver katras kameras iestatījumu logu (nākamajā sadaļā). |
| **Pauze / Atskaņot**| Iesaldē tiešraides priekšskatījumu**tikai ekrānā** — ierakstīšana serverī turpinās. Pauzētās kameras nevar veikt ierakstīšanu. |
| **X** | Atvienot. Lietotāja saskarne atjauninās nekavējoties (optimālais gadījums); atvienošanās procesa pabeigšanai fonā var būt nepieciešamas 10–30 sekundes. |

### Masīva rindas

Masīva rindā tiek parādīts uzraksts „ARRAY” masīva krāsā, masīva nosaukumu (pārnosaucams masīva iestatījumos) un **DAQ · ieslēgts/izslēgts**pogu —**ieslēgts**, ja ir iestatīts masīva līmeņa gaismas sensors *vai* kādam elementam ir sensors katrai kamerai; tā rīkjoslā ir precīzi norādīts, kurš sensors ko pārsūta. Elementu kameras ir uzskaitītas ar atkāpi zem tām savās atsevišķajās rindās. Masīva rindas pogas: **acs**(paslēpj/parāda VISUS elementus kopā),**rūtiņa**(masīva iestatījumu logs),**X**(atvieno visu masīvu).

Gaismas sensora (DLS) statuss, kas tiek izmantots masīva rindās un masīva iestatījumu logā, ir četros stāvokļos:**izslēgts**,**gaida**(vēl nav spektra),**aktīvs**(spektrs saņemts pēdējo 3 s laikā) un**novecojis** — pēdējo 3 s laikā nav jauna spektra, bet pēdējais rādījums *joprojām tiek izmantots* (DAQ rādījumi nekad nezaudē derīgumu uztveršanas ceļā).

Lai mainītu saraksta secību, sānu joslā varat pārvilkt atsevišķas kameras un veselas masīva grupas viena pāri otrai; masīva elementus atsevišķi pārvilkt nav iespējams.

## Iestatījumu panelis katrai kamerai

Atveriet, nospiežot **zobratu** kameras rindā. Logu var pārvietot pāri kameru sarakstam.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->

**Virsraksts**: kameras**krāsu paraugs**(noklikšķiniet, lai atvērtu sistēmas krāsu izvēlni — nosaka sānu joslas apmales un flīžu izvēles gredzena krāsu),**nosaukums**ar zīmuli**Pārdēvēt**pogu (ja saglabājat tukšu nosaukumu, tiek atjaunots noklusējuma nosaukums `MODEL (serial)`) un**×**, lai aizvērtu.

### Reāllaika histogramma

Logas augšdaļā atrodas reāllaika luminances histogramma, kas aprēķināta no JPEG priekšskatījuma pie ~8 Hz. Vidējā vērtība ir svērta pēc Bejera metodes — (R+2G+B)/4 — lai atbilstu kameras paša AE mērījumiem.

* **Orange pārtraukta līnija**= AE mērķis.**Velciet to horizontāli, lai mainītu mērķi** — atlaidot tiek nosūtīta viena komanda, un velkot AE mērķa režīms pārslēdzas uz manuālo.
* **Green nepārtraukta līnija** = faktiskais vidējais luma (tas, ko AE pašlaik nodrošina).
* **RGB poga** (labajā augšējā stūrī): pārslēdz katras joslas pārklājuma histogrammas, kas iekrāsotas atbilstoši kameras filtram (piem., FRGN režīmā: pelēka NIR, zaļa, sarkana). Mono (M3M) kamerās pogai ir uzraksts „MONO” un tā ir atspējota — mono režīmā vienmēr tiek parādīta vienas joslas luma histogramma.
* X ass apzīmējumi atbilst pašreizējā pikseļu formāta sensora bitu dziļumam: 0..255, 0..1023, 0..4095 vai 0..65535.

### Kameras informācijas rindas

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->

| Rinda | Darbība |
| --- | --- |
| **Modelis** | Tikai lasāms (piem., `LATT-M3C-L87-FRGN`). |
| **Radiometriskā kalibrēšana**| Green**„Active”**ikona ar aprakstu, kurā norādīts kalibrēšanas līmenis, hash, kalibrēšanas datums un joslu saraksts, kas ielādēts no kameras kalibrēšanas paketes (sk. [Rūpnīcas radiometriskā kalibrēšana](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**Paslēpts RGB kamerām** — tām ir fotometriska balansa kalibrēšana, nevis starojuma intensitāte katrā joslā. |
| **Kalibrēšanas ziņojums**|**Lejupielādēt** poga — atver kameras sērijas numuram atbilstošo NIST kalibrēšanas sertifikātu PDF formātā jūsu operētājsistēmas skatītājā. Ja sertifikāts vēl nav saglabāts kešatmiņā, Chloros tā vietā parāda norādi. |
| **Sērijas numurs** | Tikai lasāms. |
| **Programmatūra**| Parāda pašreizējo versiju, pēc tam nosaka šim modelim pieejamo versiju (saglabāta kešatmiņā katram modelim — N kameru masīvs vienu reizi pārbauda serveri). Stāvokļi: „Pārbauda…“ →**„Atjaunināt uz X“**pogu → „Atjaunina…“ → „Atjaunināts no A uz B“ / „Neizdevās: …“ / „Izlaists: …“ / zaļš**„Atjaunināts“**. Atjaunināšanas pogas rādītājs: „Rūpnīcas iestatījumu atjaunošana + atjaunināšana + UserSet1 pārprogrammēšana. ~2–3 minūtes; neatvienojiet.” |
| **IP** | Tikai lasīšanai. |
| **Temperatūra** | Tikai lasīšanai, atjaunojas ik pēc 3 s. Kļūst oranža, ja ≥65 °C, un sarkana ar ⚠, ja ≥75 °C. |
| **Kalibrēšanas mērķis** izvēles rūtiņa | Aktivizē ArUco atstarošanas mērķa noteikšanu, izmantojot katram paneļam atsevišķu NDVI validācijas tabulu zem tiešraides (saraksta skatā). Tikai sesijas laikā — vienmēr atvērta izslēgta. |
| **Gaismas sensors** nolaižamais izvēlnes saraksts | Piesaista DAQ gaismas sensoru (DAQ-E/M/U, no cilnes „Gaismas sensori” saraksta) šai kamerai, lai veiktu lejupvērstās gaismas (DLS) apgaismojuma korekciju un prognozējošu automātisko ekspozīciju. Izvēloties &quot;Nekāds&quot;, saistība tiek atcelta. Ja nav pieslēgti sensori, izvēlnē parādās &quot;(sensori nav pieslēgti — atveriet cilni &quot;DAQ&quot;)&quot;. Saistība tiek saglabāta kopā ar projektu. |

### Ekspozīcija un pastiprinājums

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Visiem šeit esošajiem skaitliskajiem ievades laukiem tiek izmantoti „turiet, lai paātrinātu” regulatori: pieskāriens = ±1, turēšana &gt;1,5 s = ±10, turēšana &gt;3 s = ±100. Vērtība tiek nosūtīta kamerai, kad atlaidat.

| Vadības elements | Diapazons / izvēles | Noklusējums | Attiecas uz | Funkcija |
| --- | --- | --- | --- | --- |
| **Ekspozīcija (us)**| Kameras reāllaika minimālā/maksimālā vērtība | Automātiski | Visi | Ekspozīcijas laiks mikrosekundēs, ar**Automātiski/Manuāli** slēdzi. Automātisks = nepārtraukta kameras automātiskā ekspozīcija. |
| **Pastiprinājums (dB)**| Kameras reālais minimums/maksimums (piem., līdz 48 dB) | Manuāls (izslēgts) | Visi | Analogais/digitālais pastiprinājums ar savu**Automātisks/Manuāls** slēdzi. |
| **AE mērķa spilgtums**| 0–255 | 80, režīms**Automātisks**| Visi (rediģējams, ja ir ieslēgta AE vai automātiska pastiprinājuma funkcija) | Spilgtums, uz kuru vērsta AE.**Automātiskajā**režīmā (noklusējums) uz histogrammas balstīts aizmugures kontrolieris pats izvēlas mērķi, uzturot ekspozīciju 60–75 % no sensora maksimālās vērtības. Ierakstot vērtību vai velkot histogrammas oranžo līniju, režīms pārslēdzas uz**manuālo**. |
| **AE izlīdzināšana** | 0,5–40, solis 0,1 | 8,0 | Visi | AE amortizācija. Rīka padoms: „Zemāka vērtība = AE reaģē ātrāk (var pulsēt pie augsta kadru skaita). Augstāka vērtība = vienmērīgāka / lēnāka.&quot; Vērtības, kas ir ievērojami zemākas par noklusējuma vērtību, var izraisīt AE pulsēšanu un destabilizēt straumēšanu pie augsta kadru skaita; 8,0 ir stabils noklusējums. |
| **AE interesējošā zona**| Aktivizēšanas izvēles rūtiņa +**Aim**poga | Izslēgts | Viss | Ja ir ieslēgts, AE mēra tikai zaļo pārtraukto līniju apzīmēto zonu, nevis visu kadru.**Aim** aktivizē klikšķa novietošanu tiešraidē: klikšķis centrē zonu 30 % attālumā no kadra; klikšķis un velšana izveido pielāgotu taisnstūri (vismaz 5 % × 5 %). Funkcija „Aim” atslēdzas pēc vienas novietošanas. Zona tiek atspoguļota atpakaļ kameras nativajās koordinātēs atbilstoši jebkurai jūsu iestatītajai rotācijai/spoguļošanai un tiek saglabāta kopā ar projektu. |
| **AE pielāgošanas ātrums** | 0,1–5, solis 0,1 | 1,0 | Tikai masīva locekļiem | Cik ātri automātiskās AE mērķa sistēma seko līdzi ainas spilgtuma izmaiņām; 1,0× pārbauda ik pēc 2,5 s. |
| **Spilgtuma aizsardzība** | Stingra (1 %) / Normāla (5 %) / Atvieglota (15 %) | Stingra | Kameras, kurās šis iestatījums ir pieejams | Cik liela kadra daļa drīkst izbalēt līdz baltam, pirms AE padara attēlu tumšāku. |

{% hint style="info" %}
**Apgaismojuma prasības Bayer multispektrālajām kamerām (RGN / OCN / NGB):** ainai jābūt pietiekami apgaismotai visos trīs kanālos, citādi kalibrēšana nedarbosies pareizi — viena sensora ekspozīcija aptver visus trīs spektrus. Lai izmērītu apgaismojumu, izmantojiet DAQ gaismas sensoru vai pārslēdzieties uz pilnībā monohromu režīmu (M3M), lai katrai joslai būtu sava ekspozīcija. Ja uzņemšana neatbilst šai prasībai, Chloros to atklāj un brīdina jūs (paziņojums „unmix-clamp”).
{% endhint %}

### Pikseļu formāts un

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

izšķirtspēja**Masīva elementi** parāda tikai lasāmas rindas „Current” (formāts + WxH) un „Binning” ar piezīmi „Set in array settings” — straumes atkārtota sākšana vienā elementā pārtrauktu sinhronizāciju, tāpēc tās tiek pārvaldītas [masīva iestatījumu panelī](#array-settings-pane).**Atsevišķām kamerām** ir pieejams:

| Kontrole | Izvēles | Funkcija |
| --- | --- | --- |
| **Pikseļu formāts** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Sensora pikseļu formāts (bitu dziļums). |
| **Izšķirtspēja** | Pilna / Puse / Ceturtdaļa | Attiecībā pret pašreizējo binningu: Pilna = 2048/N × 1536/N N×N pikseļu apvienošanai. |
| **Pikseļu apvienošana** | 1x1 (nav) / 2x2 / 4x4 | Aparatūras N×N pikseļu apvienošana — lielākas vērtības samazina izšķirtspēju, bet palielina signāla-trokšņa attiecību (SNR) un kadru ātrumu. Tās maiņa pārstartē straumi un atjauno jebkuru interešu zonu (ROI) atbilstoši jaunajam pilnajam redzes laukam. |
| **Pašreizējais** | tikai lasāms | Faktiskais WxH un (x, y) nobīde, kas ir spēkā. |

### Tiešraides priekšskatījums

Viss šajā sadaļā attiecas **tikai uz attēla parādīšanu**— tas maina to, ko redzat tiešraidē, savukārt saglabātie attēli paliek lineāri un nemainīti — ar vienu izņēmumu:**Vignette** ir radiometriska un ietekmē arī eksportētos attēlus (aprakstīts zemāk).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->

| Kontrole | Diapazons / izvēles | Noklusējums | Attiecas uz | Kā darbojas |
| --- | --- | --- | --- | --- |
| **Renderēšanas izšķirtspēja** | 360p (ātrākā) / 480p / 720p / 1080p / Sensora nativā izšķirtspēja (lētākais) | 720p | Visi | Augstums, kurā backend izpilda radiometrisko priekšskatīšanas ķēdi. Zemāka vērtība nodrošina augstāku kadru ātrumu, nemainot redzes lauku. |
| **Indekss**| Iespējošanas izvēles rūtiņa + zobrats | Izslēgts | Tikai Bayer multispektrālie sensori,**ne** kombinēto matricas sensoru lietotājiem | Reāllaika veģetācijas indeksa priekšskatījums. Zobrats atver kopīgo [Indeksa kalkulatoru](#index-calculator-pane), kurā jau ir ievadīti kameras filtra dabiskie viļņu diapazoni (piem., `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Katram priekšskatījuma kadram tiek aprēķināts pielāgots izteiksmes un LUT (ieslēgts/izslēgts, līmeņa noklusējums 3, minimālais noklusējums 0,2, maksimālais noklusējums 1) tiek aprēķināts katrā priekšskatījuma kadrā. Kombinētās matricas elementi paslēpj šo rindu — matricai pieder viens kopīgs indekss. |
| **Baltā balanss** | Izslēgts / Vienreiz / Nepārtraukts + atkārtotas uzņemšanas poga | Nepārtraukts | Tikai RGB | Baltā balanss reāllaikā. Atjaunošanas poga atkārtoti uzņem baltā balansu no pašreizējā DLS spektra (atspējots, ja režīms ir „Izslēgts”). |
| **Gamma** | Ieslēgts / Izslēgts | Ieslēgts | Tikai RGB | Parāda gamma (γ = 2,2 LUT) reāllaika priekšskatījumā. Saglabātie attēli paliek lineāri. |
| **Trokšņu samazināšana** | Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 50 | Visi (katrai kamerai atsevišķi, pat masīvos) | Divpusējais filtrs reāllaika priekšskatījumā. Augstāka vērtība = gludāks, bet mazāk izteikts detaļu attēlojums. |
| **Asums** | Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 30 | Visi | Neasuma maska reāllaika priekšskatījumā, tiek piemērota pēdējā. Var pastiprināt troksni. Tikai priekšskatījumā. |
| **Vignette**| Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 0 | Visi | Manuāla atlikušā vignettēšanas noņemšana (izgaismo stūrus), kas tiek uzlikta virs masīva „Smart Vignette” aprēķina.**Radiometriskais — ietekmē reāllaika skatu UN eksportu**, atšķirībā no trokšņu samazināšanas/asuma. |
| **Krāsu profils** | Raw / Lineārs / Dabisks / Uzlabots / Pielāgota temperatūra | Dabisks | Tikai RGB | Skatīt zemāk. |
| **Krāsu temperatūra** | 2000–10000 K, solis 100 | 5500 K | Tikai RGB, pielāgotais temperatūras profils | Fiksē balansa iestatījumus uz noteiktu korelēto krāsu temperatūru (DLS ievade netiek ņemta vērā). Pēdējā izvēlētā Kelvērtība tiek saglabāta, mainot profilus. |
| **Saturācija** | 0–200 (100 = neitrāla) | 100 | Tikai RGB | HSV saturācija reāllaika priekšskatījumā. |
| **Kontrasts** | 0–200 (100 = neitrāls) | 100 | Tikai RGB | Lineārs kontrasts ap vidēji pelēko krāsu reāllaika priekšskatījumā. |
| **Spoguļot pa horizontāli / Spoguļot pa vertikāli** | Izvēles rūtiņas | Izslēgts | Visi | Apgriež priekšskatījumu pa horizontāli / vertikāli. |
| **Pagrieziens**| 0° / 90° / 180° / 270° | 0° | Visi | Pagriež priekšskatījumu. Orientācija tiek piemērota aizmugurējās apstrādes priekšskatījuma ķēdes beigās —**saglabātie attēli paliek kamerai raksturīgajā orientācijā**, un masīva saliktie skati to ignorē. |**Krāsu profila semantika** (RGB kameras):

* **Raw** — pilnībā apiet apstrādes ķēdi.
* **Lineārs** — tumšais signāls + līdzenais lauks + baltā balanss; bez krāsu matricas, bez gamma.
* **Natural** *(noklusējums)* — lineārs, papildināts ar izmērīto krāsu korekcijas matricu un ainai pielāgotu toņu līkni.
* **Enhanced**— Natural, papildināts ar vibrance un CLAHE lokālo kontrastu. Papildu izmaksas attiecas**tikai uz tiešraides priekšskatīšanu** — saglabātie attēli vienmēr tiek apstrādāti pilnībā neatkarīgi no profila.
* **Pielāgota temperatūra** — „Dabiska” ar baltā līdzsvaru, kas fiksēts uz jūsu izvēlēto Kelvina vērtību.

{% hint style="warning" %}
Režīmos „Dabiska”, „Uzlabota” un „Pielāgota temperatūra” paneļā tiek parādīta piezīme par toņiem: kadri tiek izgaismoti atbilstoši konkrētajai ainai, tādēļ saglabātie *ekrāna* attēli nav salīdzināmi kadrs ar kadru. **Eksportējiet starojuma intensitāti vai atstarojumu mērījumiem.**
{% endhint %}

### Ekrāna pārklājumi (uzvilkti virs tiešraides)

Tie darbojas tikai lietotāja saskarnē — tiek uzvilkti virs videoklipa, nekad neietekmējot straumi vai uzņēmumus.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Pārklājums | Vadības elementi | Noklusējums | Funkcija |
| --- | --- | --- | --- |
| **Zebra** | Izvēles rūtiņa + slieksnis 200–255 | Izslēgts / 250 | Magentas krāsas diagonālas svītras uz nogrieztajiem pikseļiem. |
| **Krustpunkts** | Izvēles rūtiņa | Izslēgts | Kadra centra atzīme. |
| **Tīkls** | Izslēgts / 3 × 3 / 9 × 9 | Izslēgts | Kompozīcijas tīkls. |
| **Histogramma** | Izvēles rūtiņa + platums 0,10–0,90 no kadra | Izslēgts / 0,25 | Histogrammas josla attēla plūsmā. |
| **Fokusa maksimums** | Izvēles rūtiņa + slieksnis 20–200 + krāsu paraugs | Izslēgts / 80 / `#ff5722` | Sobela malu izcelšana fokusa iestatīšanai. |
| **Kanālu sadalījumi** | &quot;Rādīt sadalījumus (Red / Green / NIR)&quot; / &quot;Paslēpt sadalījumus&quot; poga | Paslēpts | Pievieno trīs neatkarīgus pelēktoņu laukus katram kanālam blakus kompozīcijai (pogas nosaukums atbilst kameras filtra kanāliem). Katru sadalīto laukumu var pārvilkt, un tam ir tā pati robežkrāsa kā kamerai. Nav pieejams mono kamerām. Tiek saglabāts kopā ar projektu. |

### Punktu eksponometrs

* **Noklikšķiniet, lai ņemtu paraugu**izvēles rūtiņa: noklikšķiniet uz tiešraides attēla, lai ņemtu paraugu no viena pikseļa (to atzīmē krustveida tēmēklis), vai noklikšķiniet un velciet, lai izvēlētos apgabalu pikseļu vidējās vērtības aprēķināšanai.**Dzēst**noņem paraugu un tēmēkli. Savstarpēji izslēdzas ar AE-ROI**Mērķēšanas** režīmu.
* **Rādīt**nolaižamais izvēlnes elements:**Raw (bitu dziļums)**— sākotnējie digitālie skaitļi atbilstoši sensora bitu dziļumam (piem., 12 biti → 0..4095) — vai**Display (8-bit)** (noklusējums). Ja ir aktīvs reāllaika indekss, Display vietā parāda aprēķināto indeksa vērtību (piem., NDVI).
* Nolasīšanas panelī ir uzskaitītas pikseļu koordinātas, kadra izmērs, pikseļu formāts, bitu dziļums un kanālu tabula (Chan / Value / %) ar joslu apzīmējumiem un viļņu garumiem; Bayer zaļo pāru vidējās vērtības tiek aprēķinātas; reģiona paraugi rāda „N px avg”.

Punktu eksponometra stāvoklis ir spēkā tikai sesijas laikā.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Prognozējošā automātiskā ekspozīcija (DLS vadīta)

Šī sadaļa parādās tikai tad, ja **ir pieslēgts vismaz viens DAQ gaismas sensors** — risinātājam ir nepieciešams reāllaika lejupvērstais spektrs, lai to vadītu.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->

| Vadības elements | Diapazons | Noklusējums | Funkcija |
| --- | --- | --- | --- |
| **Ieslēgt** | Izvēles rūtiņa | Ieslēgts (autonomās kameras) | Slēgtaveida risinātājs izmanto DLS spektru un kameras kalibrēšanas paketes skalārus, lai visgaišāko joslu novietotu tuvu piesātinājumam, vienlaikus saglabājot vājāko joslu virs SNR minimālā līmeņa — viena ekspozīcija uz katru risinājumu, bez stabilizācijas cikla. Izstrādāts saules enerģijas darbināmiem timelapse uzņēmumiem, kur katram kadram jābūt pareizi eksponētam. Aizmugurējā sistēma nemanāmi pārslēdzas uz reaktīvo AE , ja DLS rādījums ir novecojis/trūkst vai kalibrēšanas pakete nav ielādēta. |
| **Izlīdzināšana (α)** | 0,05–1,0, solis 0,05 | 0,3 | Secīgu prognozējošo risinājumu izlīdzināšana (zemāka vērtība = gludāks rezultāts). |
| **Ainas atstarošanās**|**Pārkalibrēt ρ** poga | — | Atkārtoti aprēķina ainas atstarošanās koeficientu, ko izmanto risinātājs. |

{% hint style="info" %}
**Masīva savienojums pēc noklusējuma izslēdz prognozējošo AE** — masīviem Chloros viedā AE kopā ar kameras puses automātiskoekspozīciju (ar piesātinājuma aizsardzību), un prognozējošās AE vienreizējais ainas atstarojamības novērtējums nav drošs jauktās ainās. Šeit to var atkārtoti ieslēgt katrai kamerai atsevišķi, ja konkrēti vēlaties DLS vadītu radiometrisko ekspozīciju.
{% endhint %}

**DAQ vadīts ekspozīcijas ierobežojums un uz incidentālo gaismu fiksēta AE.**Neatkarīgi no iepriekš minētā izvēles lodziņa, ja DAQ gaismas sensors ir piešķirts RGB kamerai, Chloros aprēķina — no izmērītā absolūtā lejupvērstā starojuma intensitātes — aprēķina maksimālo ekspozīciju × pastiprinājumu, pie kura virsma ar 100 % atstarošanas koeficientu paliek zem klipēšanas sliekšņa, un piemēro to kā**robežvērtību**automātiskajai ekspozīcijai. Kamēr robežvērtība ir aktīva, kamera darbojas**pēc krītošā gaismas intensitātes**: tā darbojas atvērtā ciklā ar ekspozīciju, kas noteikta pēc krītošā gaismas mērījuma, ar pastiprinājumu 0 dB — ekspozīcija seko izmērītajai gaismai, nevis ainas saturam. Tā kā maksimālā robeža var tikai saīsināt ekspozīciju, tā pati nevar izraisīt pārklāšanos. Ierobežojums automātiski atslēdzas — un atsākas parastā ainas automātiskā ekspozīcija — ikreiz, kad trūkst DAQ rādījuma, tas ir novecojis (&gt;30 s) vai tumšs, vai ja ≥15 % no kadra tiek nogriezts pie fiksētās ekspozīcijas (tas nozīmē, ka sensors un kamera uztver atšķirīgu apgaismojumu). Nav GUI slēdža; tā ir standarta darbība, ja RGB kamerai ir piesaistīts DAQ.

### Datu ieguves un trigera

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

masīva locekļiem papildus tiek parādītas tikai lasāmas rindas **Loma**(galvenais — zilā krāsā / pakļautais — zaļā krāsā),**Sinhronizācijas līnija**un**Līdzvērtīgie**.

| Vadība | Izvēles | Noklusējums | Piezīmes |
| --- | --- | --- | --- |
| **Trigera režīms** | Izslēgts / Ieslēgts | Ieslēgts | Atslēgts masīva elementiem (masīvs pārvalda trigera darbību). |
| **Trigera avots** | Programmatūra / Line0 (M8) / Line1 / Line2 | Line0 | Paslēpts, ja trigera režīms ir izslēgts; atslēgts masīva elementiem. Line0 ir M8 optiski izolētā ārējā izraisītāja ieeja. |
| **Kadru ātrums**| Automātiski / Manuāli + vērtība | Automātiski |**Automātiski**: kameras kadru ātruma ierobežojums ir atspējots — ekspozīcija nosaka kadrus sekundē (fps), un lodziņā tiek parādīts reālais kadru ātrums tiešraidē.**Manuāli**: jūs ierobežojat kadrus sekundē ar slideri (no 1 līdz maksimālajam, ko ierobežo joslas platums), pamatojoties uz pašreizējo faktisko kadru skaitu. Masīva locekļiem tiek parādīts tikai lasāms rādītājs „N kadri sekundē (reāllaikā)“ ar norādi „Iestatīts masīva iestatījumos“. |

### Tīkls / Pārraide

| Rinda | Darbība |
| --- | --- |
| **Paketes izmērs**| 1500 (standarta) / 9000 (Jumbo) — noklusējums**Jumbo**. |
| **Pārraides ātrums** | Tikai lasāms savienojuma pārraides ātruma ierobežojums MB/s. Aizmugures sistēma to pārbalansē starp visām pieslēgtajām kamerām katrā pieslēgšanās/atvienošanās reizē. |
| **Bufera apstrāde** | Tikai lasāms bufera apstrādes režīms. |

### Uztveršana

Logu noslēdz poga **„Atvērt uztveršanas iestatījumus…”**, kas pāradresē uz [Uztveršanas iestatījumu logu](capture.md#the-capture-settings-pane) (atspējots, kamēr nav atvērts projekts — „Izveidojiet vai atveriet projektu, lai saglabātu uztverumus”). Ja kamera ir paslēpta vai apturēta, parādās brīdinājums, kas atgādina, ka pirms ierakstīšanas tā jāatklāj vai jāatsāk.

## Masīva iestatījumu logs

Atveriet, nospiežot **zobratu**uz ARRAY rindas. Virsraksts: masīva nosaukums ar zīmuli pārdēvēšanai un**×**, lai aizvērtu. Zemāk esošās sadaļas, kas atzīmētas ar *tikai apvienotās*, parādās tikai masīviem, kas savienoti apvienotā attēlošanas režīmā.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->

### Sinhronizācija

Tikai lasāmas **Galvenā**,**Pakļautās**un**Sinhronizācijas līnija** rindas.

### Vides gaismas sensors

Tiek parādīts gan apvienotiem, gan atsevišķiem masīviem:

* **Kalibrēšanas mērķis** izvēles rūtiņa — „Atklāt MAPIR ArUco mērķi un validēt NDVI pret paneļa atstarošanas LUT”; vadīta kombinētā laukuma mērķa pārklājumu un validācijas tabulu.
* ****Gaismas sensors** nolaižamais izvēlnes elements — piesaista vienu DAQ visam masīvam. Izvēle stājas spēkā nekavējoties, tiek pārnesta uz katras masīvā iekļautās kameras paša „Gaismas sensors” nolaižamo izvēlni (jūs joprojām varat pārrakstīt iestatījumus katrai kamerai atsevišķi) un sāk nosūtīt spektrus uz masīvu.
* **Stāvoklis** reāllaika rinda: Izslēgts · „Gaida pirmo spektru…” · &quot;Aktīvs — visām masīva kamerām ir veikta apgaismojuma korekcija&quot; · &quot;Pēdējās 3 sekundēs nav jauna spektra — joprojām tiek izmantots pēdējais rādījums (nav novecojušā laika limita)…&quot;.
* Piezīme logā: &quot;Radiometriskā korekcija visam masīvam. Katras kameras iestatījumi pārraksta šo.&quot;

### Uztveršana — vienoti sensora iestatījumi *(tikai apvienoti)*

Šie iestatījumi vienoti attiecas uz katru masīva elementu (izmaiņas katram elementam atsevišķi traucētu sinhronizāciju). Izmaiņas tiek sagatavotas un piemērotas kopā.

<!-- SCREENSHOT-NEEDED: array settings Capture section — Pixel Format, Binning, Resolution preset, the ROI crop W/H/X/Y fields with the "max WxH" hint and Reset button, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons; ideally with the live orange crop-preview box visible on the array tile. -->

| Kontrole | Izvēles / diapazons | Kā tas darbojas |
| --- | --- | --- |
| **Pikseļu formāts** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Vienots sensora formāts visiem elementiem. |
| **Pikseļu apvienošana** | 1x1 / 2x2 / 4x4 | Aparatūras līmeņa pikseļu apvienošana — saglabā pilnu redzes lauku, vienlaikus palielinot signāla-trokšņa attiecību (SNR) un kadru ātrumu. Tā maiņa atjauno ROI laukus atbilstoši jaunajam pilnajam redzes laukam. |
| **Iepriekš iestatīta izšķirtspēja** | Pilna / Puse / Ceturtdaļa | Attiecībā pret pikseļu apvienošanu; aizpilda ROI laukus ar centrētu apgriezumu. |
| **ROI apgriešana (px)**| W / H / X / Y skaitliskie lauki | Sensora apgriešana. Platums/augstums pielāgojas 16 reizinājumiem (minimums 64); nobīdes pielāgojas 4 reizinājumiem. Norāde „max WxH” parāda maksimālo izmēru, un**Atjaunot** atgriež pilno redzes lauku. Rediģēšanas laikā uz matricas flīzes tiek attēlots oranžs izgriezuma priekšskatījuma lodziņš (ieskaitot pilna sensora shēmu, ja izgriezums tiek paplašināts uz āru). |
| **Trigera frekvence**| Automātisks / Manuāls slēdzis + fps 0,5–10, solis 0,5 |**Automātisks**(noklusējums): sistēma aprēķina trigera frekvenci, pamatojoties uz izšķirtspēju un joslas platumu — ievades lauks ir atspējots un tajā tiek parādīta aprēķinātā vērtība.**Manuāls**: fiksē jūsu ievadīto vērtību, nospiežot „Piemērot”. |

Piezīme paneļā: „Formāta/izšķirtspējas izmaiņas īslaicīgi pārstartē visas kameras. Trigera frekvence tiek piemērota reāllaikā.” **Piemērot / Atcelt** pogas atrodas paneļa apakšā.

### Saskaņošana (kopreģistrācija) *(tikai kombinēti)*

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Atlikuma** indikators: „RMS x,xx pikseļi“ — zaļš, ja mazāk par 1 pikseli, dzintara krāsā, ja mazāk par 3 pikseļiem, sarkans citos gadījumos vai ja kāda kamera nedarbojas; „nav profila“ pirms pirmā risinājuma.
* Kopsavilkuma rinda: „✓ Visas kameras saskaņotas (N)” / „⚠ p/N kameras saskaņotas —  <serial (filter)="">neizdevās” / „Aktīva apgriešana — Pārkalibrējiet, lai saskaņotu (izmanto visu sensoru)” / &quot;Gaida, kamēr ekspozīcija nostabilizējas…&quot;.
* Tabula pa kamerām: kamera (pēdējie 4 cipari no sērijas numura + filtrs), reprojekcijas kļūda pikseļos ar saskaņojumu skaitu („ref” galvenajai kamerai) un pārklāšanās normalizētā krustkorelācijas rādītājs, salīdzinot ar minimālo robežvērtību 0,35.
* **Poga „Pārkalibrēt saskaņošanu”** (pirms pirmā profila redzams uzraksts „Kalibrēt saskaņošanu”) — atkārtoti veic kopreģistrāciju ar jauniem kadriem.
* **&quot;Automātiski eksponēt kameras saskaņošanai&quot;** izvēles rūtiņa (pēc noklusējuma atzīmēta) — uz laiku padara gaišākas tumšas vai vienmuļas kameras (vispirms ekspozīcija, tad pastiprinājums), lai tām būtu tekstūra, ar ko saskaņot, pēc tam atjauno automātisko ekspozīciju.

Apvienotais priekšskatījums automātiski saskaņojas atvēršanas brīdī; veiciet atkārtotu kalibrēšanu, ja ir mainījies fokuss vai ainas dziļums. Saskaņošana **pēc dizaina ir paredzēta tikai konkrētajai sesijai** — tā nekad netiek saglabāta profilā, jo tā ir atkarīga no konkrētā brīža ainas attāluma. Uztverumus joprojām var eksportēt ar pikseļu reģistrāciju (skatīt [Saskaņotus eksportus](capture.md#per-array-controls)).

### Viedā vinjetēšana

* Izvēles rūtiņa **Iespējot korekciju**— piemēro katrai kamerai aprēķināto vinjetēšanas novirzi radiometriskajai ķēdei (reāllaikā**un** eksportos).
* **Kalibrēt no pašreizējā skata**— vispirms vēršiet kameru masīvu uz vienveidīgu mērķi (plakanu paneli, sienu vai debesis); katra kamera tiek izlīdzināta atsevišķi, un statusa ziņojumā tiek norādīts izlīdzinājuma pieaugums „n/N kameras · −x,x %”. Nospiežot**Dzēst**, novērtējums tiek noņemts.
* Veiciet precīzu regulēšanu katrai kamerai ar katras kameras **Vignette** slideri [Reāllaika priekšskatījumā](#live-preview).

### Reāllaika priekšskatījums *(tikai apvienots)** **Indekss**: atzīmējiet izvēles rūtiņu + zobrats — atver kopīgo [Indeksa kalkulatoru](#index-calculator-pane) ar joslām, kas izveidotas no**visām** iekļautajām kamerām. Izteiksmes priekšskatīšanas rinda zemāk parāda pašreizējo izteiksmi („Izteiksme nav iestatīta — atveriet kalkulatoru, lai izveidotu jaunu”), kas tiek atjaunināta ik sekundi.
* **Renderēšanas izšķirtspēja**izvēlne (tie paši iestatījumi kā katrai kamerai atsevišķi, noklusējums 720p): reāllaika skata plūsmas augstums**un** saglabātā kompozīcijas eksporta izmērs. Piezīme panelī: „Priekšskatījums + saglabātā kompozīcijas izmērs. Attēli no katras kameras vienmēr tiek eksportēti pilnā izšķirtspējā.”

### Attēlošanas slāņi *(tikai apvienoti)** **Iespējot** izvēles rūtiņa (noklusējumā izslēgta — galvenā kamera tiek parādīta tieši; ieslēgta = slāņots kompozīts).
* **Priekšplāns**/**Fons**nolaižamās izvēlnes: katra dalībnieka kamera (pēc nosaukuma) vai**Indekss**. Ja Priekšplāns ir Indekss, pikseļi ārpus LUT minimālā/maksimālā diapazona parāda Fona slāni.

### Dalītais skats *(tikai apvienotajā režīmā)*

**„Rādīt dalībnieku kameras”**— poga**„Dalīt / Paslēpt dalībnieku kameras”**, kas pievieno katra dalībnieka savu tiešraides plūsmu kā atsevišķus režģa laukumiņus blakus kompozīcijai. Laukumiņi nolasa masīva esošo kadru buferi (nav nepieciešams papildu kameras savienojums). Tikai režģa skats; tiek saglabāts katram masīvam kopā ar projektu.

### Funkcijas

Tikai lasāms panelis, kas atjaunojas ik pēc 5 s:

* **Līmeņa apzīmējums**: „Vienlaicīga uzņemšana“ (zaļš) · „Vienlaicīga uzņemšana (FTD — pakāpeniska izstarošana)“ (zaļš) · „Pakāpeniska uzņemšana (100 ms novirze)“ (dzeltens) · „Konfigurācija pārāk liela” (sarkana).
* **Kadru stāvoklis**: „x,xx % nepilnīgs” — zaļš, ja mazāk par 1 %, dzeltens, ja mazāk par 5 %, sarkans, ja 5 % vai vairāk.
* **Savienojuma līnija**: „NIC {mbps} Mbps — pastāvīgs {MB/s} MB/s”.

Tas ir masīva reālais joslas platuma budžets. Informāciju par pamatā esošo kadru skaitu sekundē (fps) un tīkla modeli — kā arī par to, ko mainīt, ja līmenis kļūst dzeltens vai sarkans — skatiet [Daudzkameru masīvi](arrays.md) un [CLI atsauci](../reference/cli-reference.md).



<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->## Logs „Indeksa kalkulators“

Trešā sānu joslas lapa, ko kopīgi izmanto gan atsevišķu kameru indeksa rīks, gan apvienotā masīva indeksa rīks (vienlaikus tikai viens — virsraksts ir „Indeksa kalkulators — <camera name="">“ vai „Indeksa kalkulators —<array name="">

“). Tajā tiek ievadīts joslu saraksts (kameras filtrēto dabisko joslu saraksts vai visas joslas visās masīva sastāvdaļās), pašreizējo izteiksmi un LUT konfigurāciju (ieslēgts/izslēgts, līmenis — noklusējums 3, minimums — noklusējums 0,2, maksimums — noklusējums 1), kā arī indeksa histogrammu reāllaikā. Nospiežot **Piemērot**, izteiksme tiek apstiprināta; LUT izmaiņas tiek piemērotas priekšskatījumam reāllaikā.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Katras kameras iestatījumi salīdzinājumā ar masīva pārvaldītajiem iestatījumiem

Īsa atsauce par to, kas atrodas kur, ja kamera ir masīva elements:

| Pārvaldīts masīvā (kameras paneļā tikai lasāms) | Joprojām katrai kamerai atsevišķi masīvā |
| --- | --- |
| Pikseļu formāts, izšķirtspēja, binning | Automātiskā ekspozīcija (ekspozīcija, pastiprinājums, mērķis, izlīdzināšana, ROI) |
| Izraisītāja režīms/avots, kadru ātrums | Trokšņu samazināšana, asums, vinjetēšana |
| | Orientācija (spoguļošana/pagriešana), ekrāna uzlikas, punktu eksponometrs |
| | Indekss (masīvi ar atsevišķu attēlojumu), gaismas sensora piesaistīšana |

Citas vispārīgas funkcijas:

* **Kombinēts vai atsevišķs attēlojums** tiek izvēlēts masīva savienošanas brīdī: kombinēts = viena saskaņota salikta flīze (elements tiek pārraidīts tikai caur „Split View”); atsevišķs = katrs elements renderē savu sinhronizēto flīzi. Kamera nekad neparāda gan atsevišķu plūsmu, gan masīva flīzi.
* **Automātiska atkārtota savienošana**: saglabāta projekta atvēršana atjauno tajā iekļautās kameras un masīvus, kā arī atkārtoti piemēro visus saglabātos iestatījumus aizmugurē, pirms plūsmas atsākas.
* **Uzņemšanas filtrēšana**: slēptās vai apturētās kameras tiek izslēgtas no funkcijas „Capture All“; masīvs tiek pilnībā bloķēts tikai tad, ja VISI elementi ir slēpti/apturēti. Skatīt [Uzņemšanas iestatījumi un režīmi](capture.md).

## Kā tiek saglabāti iestatījumi

Kameru cilnes stāvoklis tiek saglabāts **kopā ar projektu**, nevis pārlūkprogrammā:

* Katra reaģējoša izmaiņa veic kameru un masīvu momentuzņēmumu projekta`cameras.json` (ar 500 ms atkārtojuma aizkavi). Tas ietver kameru nosaukumus un krāsas, ekspozīcijas/pastiprinājuma/automātiskās ekspozīcijas (AE) iestatījumus, pikseļu formātu/izšķirtspēju/binningu, izraisīšanas ātrumu, priekšskatīšanas iestatījumus (renderēšanas izšķirtspēju, trokšņu samazināšanu, asumu, vinjeti, krāsu profilu, piesātinājumu/kontrastu), orientāciju, pārklājumus, kanālu sadalījumus, indeksa konfigurāciju, prognozējošās AE iestatījumus, AE ROI, masīvu nosaukumus, attēlošanas režīmu, masīvu uzņemšanas iestatījumus (ieskaitot ROI apgriešanas pozīciju) un režģa bloku (plūsmas tālummaiņu, skata režīmu, režģa fiksāciju, manuālo flīžu secību, slēptās kameras, aizvērtās flīzes, aktīvo kameru).
* Gaismas sensoru saistījumi tiek saglabāti projekta failā `sensors.json`.
* Atkārtoti atverot projektu, tiek atjaunota savienojums ar aparatūru un atkārtoti piemēroti visi iestatījumi.
* **Ja projekts nav atvērts = tikai sesija**: ja nav projekta, pēc Chloros aizvēršanas nekas netiek saglabāts.
* Tikai sesijai neatkarīgi no projekta: apturēšanas stāvoklis, punktu ekspozimetra paraugi, atsevišķu kameru kalibrēšanas mērķa izvēles rūtiņa (vienmēr atvērta) un matricas izlīdzināšanas profils (pēc dizaina pārrēķināts katrā sesijā).
* Vienīgais izņēmums: **Uzņemšanas iestatījumu** eksporta izvēles un uzņemšanas režīms tiek saglabāti katram projektam atsevišķi vietējā lietotnes krātuvē, nevis `cameras.json` — skatiet [Uzņemšanas iestatījumi un režīmi](capture.md).</array></camera></serial>
