# Kameru iestatījumi

Cilne **Kameras** ir „Chloros “ tiešraides vadības panelis LATTICE kamerām: galvenā attēla apskates zona, kurā katra pieslēgtā kamera tiek parādīta kā tiešraides logiņš, un sānu josla, kurā var pārslēgties starp trim lapām — **kameru sarakstu**,**iestatījumu logu**(katras kameras, masīva vai uzņemšanas iestatījumi — pa vienam), un**indeksa kalkulators**. Šajā lapā ir aprakstīti visi vadības elementi kameru sarakstā, katras kameras iestatījumu panelī un masīva iestatījumu panelī. Uzņemšanas režīmi, eksporta veida izvēle un „Capture All” darbplūsma ir aprakstīti papildu lapā [Uztveršanas iestatījumi un režīmi](capture.md).

Lapa „Kameras” parādās sānjoslā, tiklīdz Chloros aizmugurējā sistēma ir gatava. Visi zemāk minētie vadības elementi sazinās ar vietējo aizmugurējo sistēmu, izmantojot `127.0.0.1:5000`; izmaiņas nekavējoties tiek piemērotas kameras tiešraidei, ja vien nav norādīts citādi.

## Šajā lapā izmantotie kameru tipi

Vadības elementi parādās vai paslēpjas atkarībā no izvēlētās kameras veida. Rokasgrāmatā visā tekstā tiek izmantoti šādi termini:

| Termins | Nozīme | Filtra kanāli |
| --- | --- | --- |
| **RGB kamera** | LATTICE M3C ar FRGB filtru (modelī ir `-FRGB`) | Red / Green / Blue |
| **Bayer multispektrālā** | LATTICE M3C ar FRGN, FOCN vai FNGB | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — viens šaurjoslas filtrs, viena kalibrēta josla | Viena josla |
| **Masīva elements** | Kamera, kas pieslēgta kā daļa no sinhronizēta masīva (apvienots vai atsevišķs displejs) | Atbilstoši tās filtram |

 RGB-kamerām tiek veikta fotometriskā apstrāde (balansa iestatīšana, krāsu profili, gamma); multispektrālajām un mono kamerām tiek veikta radiometriskā apstrāde, izlaižot fotometriskos iestatījumus. Masīva elementiem tiek nodoti straumes līmeņa iestatījumi (pikseļu formāts, izšķirtspēja, pikseļu apvienošana, izraisītājs, kadru ātrums) masīvam — šīs rindas kļūst tikai lasāmas atsevišķās kameras logā un tiek pārvietotas uz masīva iestatījumu logu.

## Galvenā plūsmas zona

<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->



Ja nav pieslēgtas kameras, plūsmas zonā tiek parādīts sākuma ekrāns **„Pieslēdziet kameru, lai sāktu”**ar divām pogām:**Pieslēgt kameru**(zaļa, atver vienas kameras savienošanas dialoglodziņu) un**Savienot masīvu** (zila, atver masīva savienošanas dialoglodziņu). Paši savienošanas dialoglodziņi ir aprakstīti sadaļā [Kameru savienošana](connecting.md); masīva jēdzieni (sinhronizācija, līmeņi, joslas platums) — sadaļā [Daudzkameru masīvi](arrays.md). Kad atverat saglabātu projektu, kurā ir kameras, sākuma ekrānā tiek parādīts rotējošs indikators ar tekstu „Atverot N saglabātās kameras…”, kamēr Chloros atjauno straumes no pēdējās sesijas.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Augšējā josla

| Vadības elements | Funkcija |
| --- | --- |
| **Skatīšanas režīma pārslēgšana**| Pārslēdzas starp**tīkla skatu**(visi laukumi kā šūnas) un**saraksta skatu**(pilna platuma rindas: katrs apvienotais masīvs kā viena salikta rinda, katrs**atsevišķa** masīva elements kā sava rinda ar savu plūsmu un kalibrēšanas mērķa tabulu, tad VIENA atsevišķa kamera — aktīvā — zemāk). Rīku padomi: „Pāriet uz režģa skatu” / „Pāriet uz saraksta skatu”. |
| **Režģa fiksēšana**(atslēga) | Noklusējumā**fiksēts** — flīzes ir fiksētas savās vietās. Atbloķējiet, lai pārvietotu flīzes uz jebkuru vietu (starpības tiek saglabātas). Tīkls automātiski atkal tiek bloķēts katru reizi, kad pieslēdzas jauna kamera. Rīku padomi: „Atbloķēt tīklu (iespējot flīžu vilkšanu)” / „Bloķēt tīklu (fiksēt flīzes to vietās)”. |
| **Plūsmas tālummaiņas** sliders | Flīzes izmērs, sākot no 60 pikseļiem līdz pat pilnam konteinera platumam. Šūnas saglabā 4:3 proporcijas. Ja šūnas platums ir mazāks par 200 pikseļiem, nosaukums un kadru skaits sekundē (fps) tiek paslēpti, lai flīze izskatītos tīra. |

### Plūsmas flīzes

Katra kamera renderē saliktu tiešraides flīzi; kamera var papildus parādīt trīs pelēktoņu **kanālu sadalījuma** flīzes (skatīt [Kanālu sadalījumi](#display-overlays-drawn-over-the-live-feed)), savukārt kameru masīvi attēlo apvienotu flīzi. Aktīvajai flīzei ir atlases gredzens kameras (vai masīva) krāsā.

Pārvietojot kursoru pār flīzi, parādās **X** aizvērtnes poga:

* Aizverot **apvienoto** logu, kamēr tā kanālu sadalījumi paliek redzami, tiek paslēgts tikai apvienotais logs.
* Aizverot **pēdējo redzamo atsevišķas kameras logu**, šī kamera tiek atvienota.
* **Apvienotā masīva locekļu sadalītie logi nekad neatvieno** kameru — tie tikai paslēpj to.

Ja režģis ir atbloķēts, velciet jebkuru flīzi uz jebkuru vietu; izkārtojums tiek saglabāts kopā ar projektu.

## Sānu josla — kameru saraksts

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

Pirmajā sānu joslas lapā ir uzskaitītas visas pieslēgtās kameras un matricas:

* **Pievienot kameru**(zaļa) /**Pievienot masīvu** (zila, skenēšanas laikā parāda „Meklē...“). Abas opcijas ir atspējotas, kamēr ir atvērts savienošanas dialoglodziņš.
* **Ierakstīt visu** (sarkana) — ieraksta katru sarakstā iekļauto kameru, izmantojot eksporta veidus, kas izvēlēti iestatījumos „Capture Settings“. Nepieciešams atvērts projekts. Pilnībā dokumentēts sadaļā [Ierakstīšanas iestatījumi un režīmi](capture.md).
* **Ierakstīšanas iestatījumu ratiņš** (blakus pogai „Ierakstīt visu”) — atver [Ierakstīšanas iestatījumu logu](capture.md#the-capture-settings-pane). Ir atspējota, ja nav projekta vai uzņemšanas laikā.

### Kameru rindas

Katrā kameras rindā redzama krāsā iezīmēta robeža (kameras pielāgotā krāsa), uzraksts „CAM“ — ar zilu **M**(galvenā) vai zaļo**S** (pakļautā) lomas burtu masīva locekļiem — un parādīto nosaukumu. Noklusējuma nosaukums ir `LATTICE-MODEL (serial)`; to var pārdēvēt kameras iestatījumu panelī. Rindu pogas:

| Poga | Efekts |
| --- | --- |
| **Acs**| Pārslēgt redzamību. Paslēptās kameras pazūd no režģa un tiek**izslēgtas no funkcijas „Capture All“**. |
| **Ritenītis** | Atver katras kameras iestatījumu logu (nākamajā sadaļā). |
| **Pauze / Atskaņot**| Iesaldē tiešraides priekšskatījumu**tikai ekrānā** — datu ieguve serverī turpinās. Pausētajām kamerām nav iespējams veikt ierakstu. |
| **X** | Atvienot. Lietotāja saskarne atjaunojas nekavējoties (optimālais gadījums); bet savienojuma pārtraukšanai fonā var būt nepieciešamas 10–30 sekundes. |

### Masīva rindas

Masīva rindā redzams „ARRAY” marķējums masīva krāsā, masīva nosaukums (pārnosaucams masīva iestatījumos) un **DAQ · ieslēgts/izslēgts**pogu —**ieslēgts**, ja ir iestatīts masīva līmeņa gaismas sensors *vai* kādam no elementiem ir kameras sensors; tās rīkjoslā ir precīzi norādīts, kurš sensors ko pārsūta. Masīva kameras ir uzskaitītas ar atkāpi zemāk, katrā savā rindā. Masīva rindu pogas: **acs**(slēpj/parāda VISUS elementus kopā),**rūtiņa**(masīva iestatījumu logs),**X**(atvieno visu masīvu).

Gaismas sensora (DLS) statuss, kas tiek izmantots masīva rindās un masīva iestatījumu logā, ir četros stāvokļos:**izslēgts**,**gaida**(vēl nav spektra),**aktīvs**(spektrs saņemts pēdējo 3 s laikā) un**novecojis** — pēdējo 3 s laikā nav jauna spektra, bet pēdējais rādījums *joprojām tiek izmantots* (DAQ rādījumi nekad nezaudē spēku uztveršanas ceļā).

Līdz brīdim, kamēr jūs to nepārkārtosiet, sarakstā vispirms tiek parādīti masīvi, tad atsevišķas kameras, kur kameras (un katra masīva elementi) ir sakārtotas **kameru secībā**: M3C (Bayer) pirms M3M (mono); starp M3C — FRGB, FRGN, FOCN, tad FNGB; M3M — pēc filtra viļņa garuma, sākot ar viszemāko. Tīkla noklusējuma izkārtojums atbilst tai pašai secībai. Jūs varat pārvilkt atsevišķas kameras un veselas matricas grupas viena gar otrai sānjoslā, lai mainītu saraksta secību; matricas elementus nevar pārvilkt atsevišķi.

## Iestatījumu logs katrai kamerai

Atveriet, nospiežot **zobratu** kameras rindā. Logs parādās virs kameru saraksta.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->



**Virsraksts**: kameras**krāsu paraugs**(noklikšķiniet, lai atvērtu sistēmas krāsu izvēlni — nosaka sānu joslas apmales un flīžu izvēles gredzena krāsu),**nosaukums**ar zīmuli un pogu**Pārdēvēt**(ja saglabājat tukšu nosaukumu, tiek atjaunots noklusējuma nosaukums `MODEL (serial)`) un**×**, lai aizvērtu.

### Reāllaika histogramma

Logotipa augšdaļā atrodas reāllaika luma histogramma, kas aprēķināta no JPEG priekšskatījuma ar frekvenci ~8 Hz. Vidējā vērtība ir svērta pēc Beijera metodes — (R+2G+B)/4 — lai atbilstu kameras paša AE mērījumiem.

* **Oranža (Orange) pārtraukta līnija**= AE mērķis.**Velciet to horizontāli, lai mainītu mērķi** — atlaižot tiek nosūtīta viena komanda, un velkot AE mērķa režīms pārslēdzas uz Manual.
* **Zaļa (Green) nepārtraukta līnija** = faktiskais vidējais luma (to, ko AE pašlaik nodrošina).
* **RGB pogu** (labajā augšējā stūrī): ieslēdz vai izslēdz katras joslas pārklājuma histogrammas, kas iekrāsotas atbilstoši kameras filtram (piem., FRGN režīmā: pelēks NIR, zaļš, sarkans). Mono (M3M) kamerās pogai ir uzraksts „MONO” un tā ir atspējota — mono režīmā vienmēr tiek parādīta vienjoslas luma histogramma.
* X-ass apzīmējumi atbilst pašreizējā pikseļu formāta sensora bitu dziļumam: 0..255, 0..1023, 0..4095 vai 0..65535.

### Kameras informācijas rindas

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->



| Rinda | Darbība |
| --- | --- |
| **Modelis** | Tikai lasāms (piem., `LATT-M3C-L87-FRGN`). |
| **Radiometriskā kalibrēšana** | Zaļa (Green) **"Active"**zīme ar aprakstu, kurā norādīts kalibrēšanas līmenis, hash, kalibrēšanas datums un joslu saraksts, kas ielādēts no kameras kalibrēšanas paketes (sk. [Rūpnīcas radiometriskā kalibrēšana](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**RGB-kamerām paslēpts** — tām ir fotometriskā balansa kalibrēšana, nevis starojuma intensitāte katrā joslā. |
| **Kalibrēšanas ziņojums**|**Lejupielādēt** poga — atver kameras NIST kalibrēšanas sertifikātu PDF formātā, kas ir saistīts ar sērijas numuru, jūsu operētājsistēmas skatītājā. Ja sertifikāts vēl nav saglabāts kešatmiņā, Chloros tā vietā parāda norādi. |
| **Sērijas numurs** | Tikai lasīšanai. |
| **Programmatūra**| Parāda pašreizējo versiju, pēc tam nosaka šim modelim pieejamo versiju (saglabāts cache atmiņā katram modelim — N kameru masīvs pārbauda serveri vienu reizi). Stāvokļi: „Pārbauda…“ →**„Atjaunināt uz X“**poga → „Atjaunina…“ → „Atjaunināts no A uz B“ / „Neizdevās: …“ / „Izlaists: …“ / zaļš**„Atjaunināts“**. Atjaunināšanas pogas rīkjosla: „Rūpnīcas iestatījumu atjaunošana + atjaunināšana + UserSet1 pārprogrammēšana. ~2–3 minūtes; neatvienojiet.“ |
| **IP** | Tikai lasīšanai. |
| **Temperatūra** | Tikai lasīšanai, atjaunojas ik pēc 3 s. Kļūst oranža, ja ≥65 °C, un sarkana ar ⚠, ja ≥75 °C. |
| **Kalibrēšanas mērķis** izvēles rūtiņa | Iespējo ArUco atstarošanas mērķa noteikšanu, izmantojot NDVI validācijas tabulu katram paneļam zem tiešraides (saraksta skatā) — skatiet [Kalibrēšanas mērķa tabulu](#the-calibration-target-table). Tikai sesijas laikā — vienmēr atveras izslēgts. |
| **Gaismas sensors** nolaižamais izvēlnes saraksts | Piesaista DAQ gaismas sensoru (DAQ-E/M/U, no cilnes „Gaismas sensori” saraksta) šai kamerai, lai veiktu lejupvērstās gaismas (DLS) apgaismojuma korekciju un prognozējošu automātisko ekspozīciju. „"Nekāds" atceļ saistīšanu. Ja nav pieslēgti sensori, izvēlnē parādās "(sensori nav pieslēgti — atveriet cilni "DAQ")". Piesaistīšana tiek saglabāta kopā ar projektu. |

#### Kalibrēšanas mērķa tabula

Tabula ir **tikai validācijas nolasījums** („tikai validācija — nemaina kalibrēšanu”). Tās virsrakstā ir norādīts kameras sērijas numurs, filtrs, mērķa numurs un marķiera izmērs pikseļos, un tajā ir viena rinda katram panelim:

* **Panelis**, pēc tam viena kolonna katrai joslai ar paneļa vērtību — Bayer kamera parāda savas trīs joslas pēc nosaukuma un viļņa garuma (piem., FRGN: Red 660 nm / Green 550 nm / NIR 850 nm); **mono (M3M) kamera parāda savu vienīgo joslu** (piem., `F750`) un tai nav NDVI kolonnu.
* **Sat** — paneļa pikseļu daļa, kas ir piesātināti (dzintara krāsā no 1 %, sarkanā krāsā no 5 %).
* **NDVI Meas**,**NDVI Theo** (panela atsauces reflektances NDVI) un **Δ**— tikai Bayer multispektrālajām kamerām. Mazs simbols aiz „NDVI Meas” norāda, no kā tika aprēķināts izmērītais NDVI:**ρ**(reflektance — ir pieejams gaismas sensora rādījums),**L**(starojums — nav gaismas sensora rādījuma) vai**DN**(kameras neapstrādātie dati). Tikai**ρ** NDVI ir salīdzināms ar NDVI Theo: L un DN gadījumā Δ sleja ir izslēgta, un piezīme to norāda („Nav gaismas sensora rādījuma: NDVI ir aprēķināts no starojuma, tādēļ Δ nav kalibrācijas pārbaude” / „NDVI ir aprēķināts no neapstrādātiem kameras rādījumiem, tādēļ Δ nav kalibrācijas pārbaude”). Ja izmanto ρ, Δ ir zaļš, ja vērtība ir mazāka par 0,02, dzeltens — ja mazāka par 0,05, citos gadījumos — sarkans.

<!-- SCREENSHOT-NEEDED: a 1.3.0 Calibration Target table under a live feed in list view — a Bayer camera's table showing the band columns, Sat, and "NDVI Meas ρ" / NDVI Theo / Δ; ideally next to a mono camera's single-band table with no NDVI columns. -->

### Ekspozīcija un pastiprinājums

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Visas šeit ievadāmās skaitliskās vērtības izmanto „turiet, lai paātrinātu” rotējošos regulētājus: pieskāriens = ±1, turēšana >1,5 s = ±10, turēšana >3 s = ±100. Vērtība tiek nosūtīta kamerai, kad atlaidat pirkstu.

| Vadības elements | Diapazons / izvēles | Noklusējums | Attiecas uz | Funkcija |
| --- | --- | --- | --- | --- |
| **Ekspozīcija (us)**| Kameras reāllaika minimālā/maksimālā vērtība | Automātiski | Viss | Ekspozīcijas laiks mikrosekundēs, ar**Automātisks/Manuāls**slēdzi. Automātisks = nepārtraukta kameras automātiskā ekspozīcija. Iestatot manuālo ekspozīciju,**pastiprinājums** paliek automātiskā režīmā, ja tas tāds bija, ļaujot izmantot visu kameras pastiprinājuma diapazonu, līdz brīdim, kad jūs rediģējat pastiprinājumu vai iestatāt to manuālajā režīmā. |
| **Pastiprinājums (dB)**| Kameras reālais minimums/maksimums (piem., līdz 48 dB) | Manuāli (izslēgts) | Visi | Analogais/digitālais pastiprinājums ar savu**Auto/Manual** slēdzi. |
| **AE mērķa spilgtums**| 0–255 | 80, režīms**Auto**| Visi (rediģējams, ja ir ieslēgts AE vai automātiskais pastiprinājums) | Spilgtums, uz kuru vērsts AE.**Auto**(noklusējuma iestatījums) uz histogrammu balstīts aizmugurējais kontrolieris pats izvēlas mērķi, uzturot ekspozīciju 60–75 % no sensora maksimālās vērtības. Ierakstot vērtību vai velkot histogrammas oranžo līniju, režīms pārslēdzas uz**Manuālo**. |
| **AE izlīdzināšana** | 0,5–40, solis 0,1 | 8,0 | Viss | AE amortizācija. Rīka padoms: "Zemāka vērtība = AE reaģē ātrāk (var pulsēt pie augsta kadru skaita sekundē). Augstāka vērtība = vienmērīgāka / lēnāka.” Vērtības, kas ir ievērojami zemākas par noklusējuma vērtību, var izraisīt AE pulsēšanu un destabilizēt straumēšanu pie augsta kadru skaita sekundē; 8,0 ir stabils noklusējums. |
| **AE interešu zona**| Iespējošanas izvēles rūtiņa +**Mērķēšana**poga | Izslēgts | Visi | Ja ir ieslēgts, AE mēra tikai zaļo pārtraukto līniju apgabalu, nevis visu kadru.**Mērķēšana** aktivizē funkciju „noklikšķini, lai novietotu” tiešraidē: ar vienu klikšķi reģions tiek centrēts 30 % attāluma no kadra malas; klikšķis un velšana izveido pielāgotu taisnstūri (vismaz 5 % × 5 %). Funkcija „Aim” izslēdzas pati pēc vienas novietošanas. Zona tiek pārveidota atpakaļ uz kameras nativajām koordinātēm atbilstoši jebkurai jūsu iestatītajai rotācijai/spoguļošanai un tiek saglabāta kopā ar projektu. |
| **AE Tune Speed** | 0,1–5, solis 0,1 | 1,0 | Tikai masīva locekļiem | Cik ātri automātiskā AE mērķa sistēma seko līdzi ainas spilgtuma izmaiņām; 1,0× pārbauda ik pēc 2,5 s. |
| **Spilgtuma aizsardzība** | Stingra (1 %) / Normāla (5 %) / Atvieglota (15 %) | Stingra | Kameras, kas atklāj šo iestatījumu | Cik liela kadra daļa drīkst izbalēt līdz baltam, pirms AE padara attēlu tumšāku. |

{% hint style="info" %}
**Gaismas prasības Bayer multispektrālajām kamerām (RGN / OCN / NGB):** ainai jābūt pietiekami apgaismotai visos trīs kanālos, citādi kalibrēšana nedarbosies pareizi — viena sensora ekspozīcija aptver visus trīs spektrus. Lai izmērītu apgaismojumu, izmantojiet DAQ gaismas sensoru vai pārejiet uz pilnībā monohromu režīmu (M3M), lai katram diapazonam būtu sava ekspozīcija. Ja uzņemšana neatbilst šim nosacījumam, Chloros to atklāj un brīdina jūs (paziņojums par diapazona ierobežošanu). Reāllaikā kanāla sadalījuma logā tiek parādīts **„Zema uzticamība joslas datiem · N%”**, ja „unmix” ir ierobežojis šo joslu 25 % vai vairāk no tās pikseļiem.
{% endhint %}

{% hint style="info" %}
**Kā automātiskā ekspozīcija izmanto ekspozīcijas laiku un pastiprinājumu (1.3.0).** Ja pastiprinājums ir iestatīts uz „Auto”, Chloros uztur pastiprinājumu 0 dB līmenī, līdz ekspozīcija sasniedz ilgāko atļauto vērtību (AE augšējo robežu, matricas trigera periodu vai DAQ ekspozīcijas maksimumu); tikai tad pastiprinājums palielinās. Matricas gadījumā prioritāte ir trigera frekvencei: automātiskā ekspozīcija aizpilda periodu, ko nosaka frekvence, pēc tam palielina pastiprinājumu, tādējādi ainas tumšāka kļūšana vairs nesamazina automātisko trigera frekvenci (versijā 1.2.0 tā notika). **Manuāla**ekspozīcija masīva elementā, kas ilgst ilgāk par trigera periodu, palēnina masīva trigeri, lai tas tam pielāgotos; automātiskā frekvence atjaunojas, tiklīdz visi elementi, kas to palēnināja, atgriežas automātiskajā ekspozīcijā. Ja gan**ekspozīcija**, gan**AE mērķa spilgtums** ir iestatīti uz „Automātiski” un DAQ gaismas sensors sniedz rādījumus, katras kameras ekspozīcija tiek saglabāta, tiklīdz automātiskā ekspozīcija ir stabilizējusies, kamēr izmērītā gaisma paliek aptuveni ±25 % robežās; lielāka, ilgstoša gaismas izmaiņa, iestatījumu atkārtota uzsākšana (piemēram, ROI maiņa) vai jūsu paša veiktā ekspozīcijas maiņa to atkārtoti aktivizē.
{% endhint %}

{% hint style="info" %}
**Kā darbojas automātiskā ekspozīcija (1.3.0).**

* Manuālā ekspozīcija atstāj pastiprinājumu automātiskajā režīmā, kamēr jūs nemaināt pastiprinājumu.
* Nelielas spilgtas vietas nepadara ainu tumšāku: tiek ignorēts spīdums, kas nepārsniedz 1 % no kadra, un zināma kalibrēšanas mērķa paneļos — līdz 0,5 % no paneļa.
* DAQ ekspozīcijas ierobežojums tiek piemērots tikai tad, ja redzes laukā atrodas kalibrēšanas mērķis; ja paneļi nav redzami, kamera eksponē atbilstoši ainai.
* Ekspozīcija paliek nemainīga pastāvīgā apgaismojumā un stabilizējas dažās sekundēs pēc lielām izmaiņām. Klips vairs neierobežo ekspozīciju uz visiem laikiem, un, mainot interesējošo zonu (ROI), ekspozīcija tiek atjaunota.
* Spilgtuma mērķis nepārsniedz kamerassasniedzamības robežās, tādējādi atgriežoties gaismai, kadrā nerodas atspīdums.
* Kamera, kuras automātiskā ekspozīcija pārstāj reaģēt spilgtā saulē, tiek uzturēta atbilstoši apgaismojumam, ko nosaka gaismas sensors vai, ja tāds nav pieslēgts, paša kameras uzņemtie kadri, un atbrīvojas, kad apgaismojums mainās. Tumšs vai aizklāts gaismas sensors to netraucē.
{% endhint %}

### Pikseļu formāts un izšķirtspēja

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

**Masīva locekļi** parāda tikai lasāmas rindas „Pašreizējais” (formāts + WxH) un „Binning” ar piezīmi „Iestatīts masīva iestatījumos” — straumes atsākšana vienā locekli pārtrauktu sinhronizāciju, tāpēc tie tiek pārvaldīti [masīva iestatījumu panelī](#masīva-iestatījumu-logā).**Atsevišķām kamerām** ir pieejams:

| Vadības elements | Izvēles | Funkcija |
| --- | --- | --- |
| **Pikseļu formāts** | 8-bit / 10-bit / 12-bit / 16 bitu | Sensora bitu dziļums; formāta grupa atbilst sensora specifikācijām. Noklusējuma vērtība ir 12 biti, un projekts saglabā jūsu iestatījumus, ieskaitot 8 bitu, un atkārtoti izveido savienojumu ar šiem iestatījumiem. |
| **Izšķirtspēja** | Pilna / Puse / Ceturtdaļa | Attiecībā pret pašreizējo pikseļu apvienošanu: Pilna = 2048/N × 1536/N N×N binning gadījumā. |
| **Pikseļu apvienošana** | 1x1 (nav) / 2x2 / 4x4 | Aparatūras N×N pikseļu apvienošana — lielākas vērtības samazina izšķirtspēju, bet palielina signāla-trokšņa attiecību (SNR) un kadru ātrumu. Tās maiņa pārstartē datu plūsmu un atjauno jebkuru interešu zonu (ROI) atbilstoši jaunajam pilnajam redzes laukam. |
| **Pašreizējais** | tikai lasāms | Faktiskais WxH un (x, y) nobīde, kas ir spēkā. |

### Tiešraides priekšskatījums

Viss šajā sadaļā attiecas **tikai uz attēla parādīšanu**— tas maina to, ko redzat tiešraidē, savukārt saglabātie attēli paliek lineāri un nemainīti — ar vienu izņēmumu:**Vignette** ir radiometriska un ietekmē arī eksportētos attēlus (kā norādīts zemāk).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->



| Kontrole | Diapazons / izvēles | Noklusējums | Attiecas uz | Kā darbojas |
| --- | --- | --- | --- | --- |
| **Renderēšanas izšķirtspēja** | 360p (ātrākā) / 480p / 720p / 1080p / Sensora oriģinālā izšķirtspēja (lēnākā) | 720p | Visi | Augstums, kurā aizmugurējā sistēma palaista radiometrisko priekšskatījuma ķēdi. Zemāka vērtība nodrošina lielāku kadru skaitu, nemainot redzes lauku. |
| **Indekss**| Izvēles rūtiņa „Iespējot” + zobrats | Izslēgts | Tikai Bayer multispektrālie sensori,**nevis** kombinēto matricas sensori | Reāllaika veģetācijas indeksa priekšskatījums. Rīks atver kopīgo [Indeksa kalkulatoru](#index-calculator-pane), kurā jau ir iepriekš ielādēti kameras filtra dabiskie spektra joslas (piem., `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Katram priekšskatījuma kadram tiek aprēķināts pielāgots izteiksmes un LUT (ieslēgts/izslēgts, līmeņa noklusējums 7 pakāpes, minimālais noklusējums 0,2, maksimālais noklusējums 1) tiek aprēķināts katrā priekšskatījuma kadrā. Kombinētā masīva locekļi paslēpj šo rindu — masīvam pieder viens kopīgs indekss. |
| **Baltā balanss** | Izslēgts / Vienreiz / Nepārtraukti + atkārtotas uzņemšanas poga | Nepārtraukti | Tikai RGB | Baltā balanss reāllaikā. Atjaunināšanas poga no jauna uzņem baltā līdzsvaru no pašreizējā DLS spektra (atspējota, ja režīms ir „Izslēgts”). |
| **Gamma** | Ieslēgts / Izslēgts | Ieslēgts | Tikai RGB | Parāda gamma (γ = 2,2 LUT) reālajā priekšskatījumā. Saglabātie attēli paliek lineāri. |
| **Trokšņu samazināšana** | Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 50 | Visi (katrai kamerai atsevišķi, pat masīvos) | Divpusējs filtrs reāllaika priekšskatījumā. Augstāka vērtība = gludāks, bet mazāk izteikts detaļu attēlojums. |
| **Asums** | Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 30 | Visi | Neskaidrības maska reāllaika priekšskatījumā, tiek piemērota pēdējā. Var pastiprināt troksni. Tikai priekšskatījumā. |
| **Vignette**| Izvēles rūtiņa + intensitāte 0–100 | Izslēgts / 0 | Visi | Manuāla atlikušās vignetēšanas novēršana (izgaismo stūrus), kas tiek uzklāta virs masīva „Smart Vignette” aprēķina.**Radiometriska — ietekmē reāllaika skatu UN eksportu**, atšķirībā no trokšņu samazināšanas un asuma. |
| **Krāsu profils** | Raw / Lineārs / Dabisks / Uzlabots / Pielāgota temperatūra | Dabisks | Tikai RGB | Skatīt zemāk. |
| **Krāsu temperatūra** | 2000–10000 K, solis 100 | 5500 K | Tikai RGB, pielāgots temperatūras profils | Fiksē balansa iestatījumus uz noteiktu korelēto krāsu temperatūru (DLS ievade netiek ņemta vērā). Pēdējā izvēlētā Kelvina vērtība tiek saglabāta, mainot profilus. |
| **Spilgtās zonas** | no −100 līdz 0, solis 1 | 0 (izslēgts) | Tikai RGB | Jaunums versijā 1.3.0. Padara tumšākas attēla visgaišākās un baltākās zonas (0 = nemainīts, −100 = visstiprākais). Attiecas uz reāllaika priekšskatījumu **un uz ekrāna līmeņa (priekšskatījuma) uzņēmumiem**, kas reģistrē šo iestatījumu (`Chloros:DisplayHighlights`); tas nekad neietekmē starojuma un atstarošanas eksportu. Tas nevar atjaunot sensora datus, kas ir pārgriezti — šim nolūkam samaziniet ekspozīciju. Zebra pārklājums seko aptumšotajam attēlam. Atspējots ar Raw krāsu profilu („Netiek piemērots ar Raw krāsu profilu.”). |
| **Saturācija** | 0–200 (100 = neitrāla) | 100 | Tikai RGB | HSV saturācija reāllaika priekšskatījumā. |
| **Kontrasts** | 0–200 (100 = neitrāls) | 100 | Tikai RGB | Lineārs kontrasts ap vidēji pelēko toņu reālajā priekšskatījumā. |
| **Spoguļot pa horizontāli / Spoguļot pa vertikāli** | Izvēles rūtiņas | Izslēgts | Visi | Apgriež priekšskatījumu horizontāli / vertikāli. |
| **Rotācija**| 0° / 90° / 180° / 270° | 0° | Visi | Pagriež priekšskatījumu. Orientācija tiek piemērota aizmugurējās priekšskatīšanas ķēdes beigās.**Saglabātie attēli (1.3.0):**atsevišķas kameras priekšskatīšanas, starojuma, atstarošanas un indeksa produkti tiek pagriezti, izmantojot Spoguļošanu/Rotāciju, savukārt**„Raw“ un „Debayered“ saglabā kamerai raksturīgo orientāciju**; katrs uzņēmuma fails reģistrē šo iestatījumu savā XMP, tādējādi vēlāk apstrādājot „raw“ failu, tas tiek piemērots tādā pašā veidā.**Masīva elementu faili saglabā sākotnējo orientāciju** (iestatījums tiek reģistrēts, nevis piemērots), un masīva saliktie skati to ignorē. |

**Krāsu profilu semantika** (RGB kameras):

* **Raw** — pilnībā apiet apstrādes ķēdi.
* **Lineārs** — tumšais signāls + līdzenais lauks + baltā balanss; bez krāsu matricas, bez gammas.
* **Dabiska** *(noklusējums)* — lineāra, papildus izmantojot izmērīto krāsu korekcijas matricu un ainai pielāgotu toņu līkni.
* **Uzlabota**— Dabiska, papildus izmantojot krāsu dzīvīgumu un CLAHE lokālo kontrastu. Papildu izmaksas attiecas**tikai uz tiešraides priekšskatījumu** — saglabātie attēli vienmēr tiek pilnībā apstrādāti neatkarīgi no profila.
* **Pielāgota temperatūra** — „Dabiskais” ar baltā līdzsvara fiksēšanu uz jūsu izvēlēto Kelvina vērtību.

{% hint style="warning" %}
Režīmos „Dabiska”, „Uzlabota” un „Pielāgota temperatūra” paneļā tiek parādīta piezīme par toņiem: kadri tiek izgaismoti atbilstoši konkrētajai ainai, tādēļ saglabātie *ekrāna* attēli nav salīdzināmi kadrs ar kadru. **Eksportējiet starojuma vai atstarošanas datus mērījumiem.**
{% endhint %}

### Ekrāna pārklājumi (uzvilkti virs tiešraides)

Tie ir pieejami tikai lietotāja saskarnē — uzvilkti virs video, nekad neietekmējot straumi vai ierakstus.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Pārklājums | Vadības elementi | Noklusējums | Darbība |
| --- | --- | --- | --- |
| **Zebra** | Izvēles rūtiņa + slieksnis 200–255 | Izslēgts / 250 | Magentas krāsas diagonālas svītras uz nogrieztajiem pikseļiem. |
| **Krusts** | Izvēles rūtiņa | Izslēgts | Kadra centra atzīme. |
| **Tīkls** | Izslēgts / 3 × 3 / 9 × 9 | Izslēgts | Kompozīcijas tīkls. |
| **Histogramma** | Izvēles rūtiņa + platums 0,10–0,90 no kadra | Izslēgts / 0,25 | Histogrammas josla tiešraidē. |
| **Fokusa maksimums** | Izvēles rūtiņa + slieksnis 20–200 + krāsu paraugs | Izslēgts / 80 / `#ff5722` | Sobela malu izcelšana fokusa iestatīšanai. |
| **Kanālu sadalījumi** | "Rādīt sadalījumus (Red / Green / NIR)" / Poga „Paslēpt sadalījumus” | Paslēgts | Pievieno trīs neatkarīgas pelēktoņu plāksnes katram kanālam blakus kompozīcijai (pogas nosaukums atbilst kameras filtra kanāliem). Katru sadalīto plāksni var pārvietot, un tai ir tā pati krāsa kā kameras apmalei. Nav pieejams mono kamerām. Tiek saglabāts kopā ar projektu. |

### Punktu eksponometrs

* **Noklikšķiniet, lai ņemtu paraugu**izvēles rūtiņa: noklikšķiniet uz tiešraides attēla, lai ņemtu paraugu no viena pikseļa (to atzīmē krustveida tēmēklis), vai noklikšķiniet un velciet, lai izvēlētos apgabalu pikseļu vidējās vērtības aprēķināšanai.**Dzēst**noņem paraugu un tēmēkli. Savstarpēji izslēdzas ar AE-ROI**Mērķēšanas** režīmu.
* **Rādīt**izvēlne:**Raw (bitu dziļums)**— sākotnējie digitālie skaitļi ar sensora bitu dziļumu (piem., 12 biti → 0..4095) — vai**Displejs (8 biti)** (noklusējums). Ja ir aktīvs reāllaika indekss, Display vietā parāda aprēķināto indeksa vērtību (piem., NDVI).
* Nolasīšanas panelī ir uzskaitītas pikseļu koordinātas, kadra izmērs, pikseļu formāts, bitu dziļums un kanālu tabula (Chan / Value / %) ar joslu apzīmējumiem un viļņu garumiem; Bayer zaļo pāru vidējie rādītāji tiek aprēķināti; reģiona paraugi rāda „N px avg”.

Punktu eksponometra stāvoklis ir spēkā tikai sesijas laikā.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Prognozējošā automātiskā ekspozīcija (DLS vadīta)

Šī sadaļa parādās tikai tad, ja **ir pieslēgts vismaz viens DAQ gaismas sensors** — risinātājam ir nepieciešams reāllaika lejupvērstais spektrs, lai to vadītu.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->



| Vadība | Diapazons | Noklusējums | Funkcija |
| --- | --- | --- | --- |
| **Ieslēgt** | Izvēles rūtiņa | Ieslēgts (autonomās kameras) | Slēgtas formas risinātājs izmanto DLS spektru un kameras kalibrēšanas paketes skalārus, lai visgaišāko joslu novietotu tuvu piesātinājumam, vienlaikus saglabājot vājāko joslu virs SNR minimālā līmeņa — viena ekspozīcija uz katru risinājumu, bez stabilizācijas cikla. Izstrādāts saules enerģijas darbināmiem timelapse uzņēmumiem, kur katram kadram jābūt pareizi eksponētam. Aizmugurējā sistēma automātiski pārslēdzas uz reaktīvo AE, ja DLS rādījums ir novecojis/trūkst vai kalibrēšanas pakete nav ielādēta. |
| **Izlīdzināšana (α)** | 0,05–1,0, solis 0,05 | 0,3 | Pēc kārtas sekojošo prognozēto risinājumu izlīdzināšana (zemāka vērtība = lielāka izlīdzināšana). |
| **Ainas atstarošanas koeficients**|**Pārkalibrēt ρ** poga | — | No jauna aprēķina ainas atstarošanas koeficientu, ko izmanto risinātājs. |

{% hint style="info" %}
**Masīva savienojums pēc noklusējuma atslēdz prognozējošo AE** — masīviem ekspozīciju nodrošina „Chloros” viedā AE un kameras automātiskā ekspozīcija (ar piesātinājuma aizsardzību), un prognozējošās AE vienas ainasatstarošanas novērtējums nav drošs jauktās ainas gadījumā. Šeit to varat atkal ieslēgt katrai kamerai atsevišķi, ja konkrēti vēlaties DLS vadītu radiometrisko ekspozīciju.
{% endhint %}

**DAQ vadīts ekspozīcijas maksimums un uz incidentu fiksēta AE.** Neatkarīgi no iepriekš minētā izvēles rūtiņas, ja DAQ gaismas sensors ir piešķirts RGB kamerai, Chloros aprēķina — pamatojoties uz izmērīto absolūto lejupvērsto starojuma intensitāti — maksimālo ekspozīciju × pastiprinājumu, pie kura 100 % atstarošanas virsma paliek zem klipēšanas sliekšņa, un piemēro to kā **robežu**automātiskajai ekspozīcijai. Sākot ar versiju 1.3.0, šī robeža tiek piemērota tikai tad, ja kalibrēšanas mērķis atrodas šīs kameras redzes laukā (redzams tās kadrā pēdējo 10 sekunžu laikā); ja mērķis nav redzes laukā, tā vietā darbojas kameras parastā automātiskā ekspozīcija ar savu spilgtuma aizsardzību, tādējādi pārējā aina netiek uzturēta tumšāka, nekā nepieciešams. Kamēr maksimālā robeža ir aktīva, kamera darbojas**pēc krītošā gaismas mērījuma**: tā darbojas atvērtā ciklā ar ekspozīciju, kas noteikta pēc krītošās gaismas mērījuma, ar pastiprinājumu 0 dB — ekspozīcija seko mērītajai gaismai, nevis ainas saturam. Tā kā maksimālā vērtība var tikai saīsināt ekspozīciju, tā pati nevar izraisīt izgriešanu. Sākot ar versiju 1.3.0, maksimālā vērtība tiek aprēķināta, izmantojot kameras paša ekspozīcijas konstanti no tās kalibrēšanas paketes (flotes vidējā vērtība tiek izmantota tikai tad, ja paketē tā nav iekļauta). Maksimālā vērtība automātiski atslēdzas — un atsākas parastā ainas automātiskā ekspozīcija — ikreiz, kad redzes laukā nav kalibrēšanas mērķa, trūkst DAQ rādījuma, tas ir novecojis (>30 s), tumšs vai ja ≥15 % kadra tiek nogriezts pie fiksētās ekspozīcijas (tas nozīmē, ka sensors un kamera uztver atšķirīgu apgaismojumu). Nav GUI slēdža; tā ir standarta darbība, ja RGB-kamerai ir piesaistīts DAQ.

### Datu ieguve un izraisīšana

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

Masīva locekļiem papildus tiek parādītas tikai lasāmas **Loma**(galvenais — zilā krāsā / pakļautais — zaļā krāsā),**Sinhronizācijas līnija**un**Līdzvērtīgie** rindas.

| Vadība | Izvēles | Noklusējums | Piezīmes |
| --- | --- | --- | --- |
| **Trigera režīms** | Izslēgts / Ieslēgts | Ieslēgts | Atspējots masīva elementiem (masīvs pārvalda trigera darbību). |
| **Trigera avots** | Programmatūra / Line0 (M8) / Line1 / Line2 | Line0 | Paslēpts, ja trigera režīms ir izslēgts; masīva elementiem atspējots. Line0 ir M8 optiski izolēta ārējā trigera ieeja. |
| **Kadru ātrums**| Automātiski / Manuāli + vērtība | Automātiski |**Automātiski**: kameras kadru ātruma ierobežojums ir atslēgts — ekspozīcija nosaka kadrus sekundē, un lodziņā tiek parādīts reālais kadru ātrums tiešraidē.**Manuāli**: jūs ierobežojat kadrus sekundē ar slideri (no 1 līdz maksimālajam, ko ierobežo joslas platums), pamatojoties uz pašreizējo faktisko kadru skaitu. Masīva locekļiem tiek parādīts tikai lasāms „N kadri sekundē (reāllaikā)” ar norādi „Iestatīts masīva iestatījumos”. |

### Tīkls / Pārraide

| Rinda | Darbība |
| --- | --- |
| **Paketes izmērs**| 1500 (standarta) / 9000 (Jumbo) — noklusējums**Jumbo**. |
| **Pārraides ātrums** | Tikai lasāms savienojuma pārraides ātruma ierobežojums MB/s. Aizmugurējā sistēma to pārbalansē starp visām pieslēgtajām kamerām katrā pieslēgšanās/atvienošanās reizē. |
| **Bufera apstrāde** | Tikai lasāms bufera apstrādes režīms. |

### Ierakstīšana

Logu noslēdz poga **„Atvērt ierakstīšanas iestatījumus…”**, kas pāradresē uz [ierakstīšanas iestatījumu logu](capture.md#the-capture-settings-pane) (atspējots, kamēr nav atvērts projekts — "Izveidojiet vai atveriet projektu, lai saglabātu uzņemumus"). Ja kamera ir paslēpta vai apturēta, parādās norāde, kas atgādina, ka pirms uzņemšanas tā jāatklāj vai jāatsāk.

## Masīva iestatījumu logs

Atveriet, nospiežot **zobratu**uz MASĪVA rindas. Virsraksts: masīva nosaukums ar zīmuli pārnosaukšanai un**×**, lai aizvērtu. Sadaļas zemāk, kas atzīmētas ar *tikai apvienots*, parādās tikai masīviem, kas savienoti apvienotā attēlošanas režīmā.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->



### Sinhronizācija

Tikai lasāmas **Galvenā**,**Pakļautās**un**Sinhronizācijas līnija** rindas.

### Vides gaismas sensors

Tiek parādīts gan kombinētiem, gan atsevišķiem masīviem:

* **Gaismas sensors** nolaižamā izvēlne — piesaista vienu DAQ visam masīvam. Izvēle stājas spēkā nekavējoties, izplatās uz katras masīva kameras gaismas sensora nolaižamo izvēlni (jūs joprojām varat pārrakstīt iestatījumus katrai kamerai atsevišķi) un sāk nosūtīt spektrus uz masīvu.
* Reāllaika **Stāvoklis** rinda: Izslēgts · „Gaida pirmo spektru…“ · „Aktīvs — visām masīva kamerām ir veikta apgaismojuma korekcija“ · „Pēdējās 3 sekundēs nav jauna spektra — joprojām tiek izmantots pēdējais rādījums (nav novecojušā laika limita)…“.
* Piezīme paneļā: "Radiometriskā korekcija visam masīvam. Katras kameras iestatījumi to pārraksta."

### Kalibrēšanas mērķis

Atsevišķa grupa tieši aiz vides gaismas sensora, gan apvienotiem, gan atsevišķiem masīviem:

* **Ieslēgt** izvēles rūtiņa — "Atklāt MAPIR ArUco mērķi un validēt NDVI pret paneļa atstarošanas LUT"; nosaka apvienotā lauciņa mērķa pārklājumu un validācijas tabulas. **Atsevišķā** masīvā tas ieslēdz katra elementa paša [Kalibrēšanas mērķa tabulu](#the-calibration-target-table) (viena katrai kameras rindai saraksta skatā).

### Uztveršana — vienoti sensora iestatījumi

Tiek parādīti gan kombinētiem, gan atsevišķiem masīviem (grupas virsraksts ir „Uztveršana (vienota visām kamerām)”). Šie iestatījumi tiek piemēroti vienādi visiem elementiem (izmaiņas katram elementam atsevišķi izjauktu sinhronizāciju). Izmaiņas tiek sagatavotas un piemērotas kopā ar **Piemērot**— izņemot slēdzi**ROI ieslēgts / ROI izslēgts**, kas tiek piemērots uzreiz.

<!-- SCREENSHOT-NEEDED: array settings Capture section (1.3.0) — Pixel Format, Binning, Resolution preset, the ROI crop row with the "ROI on" toggle, the "Select" button, the "max WxH" hint and Reset button, the W/H/X/Y fields, the full-sensor ROI map below them with the orange crop box and its "W×H @ X,Y" label above the box, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons. -->

| Kontrole | Izvēles / diapazons | Kā darbojas |
| --- | --- | --- |
| **Pikseļu formāts** | 8 bitu / 10 bitu / 12 bitu / 16 bitu | Vienots bitu dziļums visiem elementiem; katra kamera savam sensoram izmanto šī dziļuma formātu. |
| **Binning** | 1x1 / 2x2 / 4x4 | Aparatūras binning — saglabā pilnu redzes lauku, vienlaikus palielinot signāla-trokšņa attiecību (SNR) un kadru ātrumu. Tās maiņa atjauno ROI laukus atbilstoši jaunajam pilnajam redzes laukam. |
| **Iepriekš iestatīta izšķirtspēja** | Pilna / Puse / Ceturtdaļa | Attiecībā pret pikseļu apvienošanu; aizpilda ROI laukus ar centrētu apgriezumu. |
| **ROI apgriešana (px)**|**ROI ieslēgts / ROI izslēgts**slēdzis,**Izvēlēties**poga, W / H / X / Y lauki, pilna sensora karte | Sensora apgriešana — skatiet [ROI redaktoru](#the-roi-editor). Platums/augstums pielāgojas 16 (vismaz 64); nobīdes pielāgojas 4 reizinājumiem. Norāde „max WxH” parāda maksimālo vērtību, un**Atjaunot** atgriež laukus uz pilnu redzes lauku. Rediģēšanas laikā oranžā apgriešanas rāmītis seko līdzi katrai lauka izmaiņai kartē un matricas segmentā. |
| **Trigera frekvence**| Pārslēgšana starp „Auto“ un „Manuāli“ + fps 0,5–10, solis 0,5 |**Auto**(noklusējums): sistēma aprēķina izraisīšanas ātrumu, pamatojoties uz izšķirtspēju un joslas platumu — ievades lauks ir atspējots un tajā tiek parādīta aprēķinātā vērtība.**Manuāli**: fiksē jūsu ievadīto vērtību, nospiežot „Piemērot”. Fiksētais ātrums, ko jaunais kadra izmērs nespēj nodrošināt, tiek ierobežots līdz maksimālajam ātrumam, ko šis izmērs atļauj, un tiek atjaunots, kad kadra izmērs to atkal ļauj. |

Piezīme paneļā: „Formāta/izšķirtspējas izmaiņas uz īsu brīdi pārstartē visas kameras. Trigera frekvence tiek piemērota reāllaikā.” **Piemērot / Atcelt** pogas atrodas paneļa apakšā.

#### ROI redaktors

Jaunums versijā 1.3.0. ROI apgriešanas rinda un zem tās esošā karte darbojas kopā:

* **ROI ieslēgts / ROI izslēgts**—**nekavējoties**ieslēdz vai izslēdz sensora apgriešanu (kameras uz īsu brīdi pārstartējas; matricas lauciņā parādās uzraksts „Piemēro iestatījumus…“).**Izslēgts**nolasa visu sensora attēlu un**atceras jūsu apgriešanu**— laukos tā joprojām tiek parādīta;**Ieslēgts** to piemēro atkārtoti. Ieslēdzot ROI bez iestatītas apgriešanas, tiek piemērota centrēta apgriešana ar pusi no izmēra („Apgriešana netika iestatīta, tādēļ tika piemērota centrēta apgriešana ar pusi no izmēra. Pielāgojiet to, izmantojot laukus.”).
* **Izvēlēties**— iezīmē apgriešanu kartē: velciet pāri tai vai noklikšķiniet uz diviem pretējiem stūriem (Esc atceļ). Taisnstūris tiek ievadīts W/H/X/Y laukos, pielāgojoties iepriekš minētajiem soļiem; nekas netiek piemērots, kamēr jūs nenospiedat**Piemērot**, tādēļ vispirms varat to precīzi pielāgot ar lauku bultiņām. Viens taisnstūris uz katru**Izvēlēties** nospiešanu.
* **Karte**parāda apgriezumu uz visa sensora, tādēļ jūs varat to atkal paplašināt: „Pilns sensors, tiešraide”, kamēr kameras nolasa pilnu sensoru, vai „Pilna kadra momentuzņēmums no {laiks}. Kameras nolasa tikai apgriezto daļu”, kamēr tās ir apgrieztas. Ja matrica līdz šim ir pārraidījusi tikai apgrieztu attēlu, kartē redzams „Vēl nav pilna kadra skata: šī matrica ir pārraidījusi tikai apgrieztu attēlu.” ar pogu**Rādīt pilnkadru**, kas izslēdz ROI. Izgriezuma marķējums `W×H @ X,Y` atrodas ārpus rāmja (virs tā vai zem tā, ja rāmis pieskaras augšējai malai) gan kartē, gan reāllaika flīzē.
* **Piemērot**apstiprina laukus: apgriezums ieslēdz ROI; pilna kadra režīms (pēc**Atjaunot**) to atstāj izslēgtu, saglabājot atmiņā iepriekšējo apgriezumu; ROI, kas ir izslēgts un kura laukos joprojām redzams saglabātais apgriezums, paliek izslēgts, kad piemērojat citas izmaiņas (formāts, binning, frekvence).
* **Saskaņotā matricas gadījumā katra elementa apgriešanas logs tiek novietots atbilstoši saskaņošanai**, tādējādi katra kamera ierāmē vienu un to pašu ainas daļu. Matrica, kas apgriezta pirms saskaņošanas, vispirms tiek saskaņota attiecībā pret pilnu sensoru (apgriešana tiek atcelta, saskaņošana veikta un apgriešana atjaunota ar logiem katrai kamerai atsevišķi).
* Pēc apgriešanas izmaiņām automātiskā ekspozīcija tiek atkārtoti pielāgota jaunajam kadrējumam. Apgriešanas laikā elementa ekspozīcija joprojām var turpināties līdz pat izraisīšanas perioda beigām.
* ROI ieslēgšanas/izslēgšanas stāvoklis un saglabātais kadrējums tiek saglabāti kopā ar projektu.

### Saskaņošana (kopreģistrācija)

Parādīts gan **apvienotiem, gan atsevišķiem masīviem** (versijā 1.2.0 tas tika parādīts tikai apvienotiem masīviem); grupas virsraksts ir „Alignment” (Saskaņošana).

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Atlikuma** indikators: „RMS x.xx px“ — zaļš, ja mazāks par 1 px, dzeltens, ja mazāks par 3 px, sarkans citos gadījumos vai ja kāda kamera nedarbojas; „nav profila“ pirms pirmā risinājuma.
* Kopsavilkuma rinda: „✓ Visas kameras saskaņotas (N)” / „⚠ p/N kameras saskaņotas —  <serial (filter)="">kļūda” / „Apgriešana aktīva — Pārkalibrējiet, lai saskaņotu (izmanto visu sensoru)” / „Gaida, kamēr ekspozīcija nostabilizējas…” / „Piemēro iestatījumus…” (kameras tiek pārstartētas) / „Masīvs vairs nepiegādā kadrus — atkārtoti savienojiet masīvu”.
* Tabula katrai kamerai: kamera (pēdējie 4 cipari no sērijas numura + filtrs), reprojekcijas kļūda pikseļos ar saskaņojumu skaitu („ref” galvenajai kamerai) un pārklāšanās normalizētā krustkorelācijas rādītājs salīdzinājumā ar minimālo slieksni 0,35.
* **Pārkalibrēt saskaņošanu** Poga (pirms pirmā profila uzraksts „Kalibrēt izlīdzinājumu”) — atkārtoti veic kopreģistrāciju ar jauniem kadriem.
* **"Automātiski eksponēt kameras saskaņošanai"** izvēles rūtiņa (pēc noklusējuma atzīmēta) — uz laiku padara gaišākas tumšas vai vienmuļas kameras (vispirms ekspozīcija, tad pastiprinājums), lai tām būtu tekstūra, ko saskaņot, pēc tam atjauno automātisko ekspozīciju.

Chloros automātiski saskaņo katru masīvu pēc savienošanās abos attēlošanas režīmos, tiklīdz elementu automātiskā ekspozīcija ir stabilizējusies (tas mēģina līdz pat 5 minūtēm); apvienotais priekšskatījums arī saskaņojas atvēršanas brīdī. Atsevišķam masīvam nav saliktas flīzes, tāpēc šī sadaļa pārbauda saskaņošanas stāvokli un pati veic pārkalibrēšanu. Veiciet pārkalibrēšanu, ja ir mainījies fokuss vai ainas dziļums. Saskaņošana **pēc dizaina ir paredzēta tikai sesijai** — tā nekad netiek saglabāta profilā, jo tā ir atkarīga no konkrētā brīža ainas attāluma. Uzņēmumus joprojām var eksportēt ar pikseļu reģistrāciju (sk. [Saskaņoti eksporti](capture.md#per-array-controls)).

### Viedā vinjetēšana

* **Iespējot korekciju**izvēles rūtiņa — piemēro katras kameras vinjetēšanas novērtējumu radiometriskajai ķēdei (reāllaikā**un** eksportos).
* **Kalibrēt no pašreizējā skata**— vispirms vēršiet matrici uz vienveidīgu mērķi (plakanu paneli, sienu vai debesis); katra kamera tiek izlīdzināta atsevišķi, un statusa ziņojumā tiek norādīts izlīdzinājuma pieaugums „n/N kameras · −x,x %”.**Dzēst** noņem novērtējumu.
* Stāvoklis: „Kalibrēts”, „Nav kalibrēts” vai „Daļēji kalibrēts — atkārtojiet”, ja tikai dažu kameru novērtējumi joprojām attiecas uz to pašreizējo kadrējumu (piemēram, pēc tam, kad funkcija „Pārkalibrēt” ir pārvietojusi kādas kameras apgriešanas logu) — atkārtojiet kalibrēšanu.
* Veiciet precīzu regulēšanu katrai kamerai, izmantojot katras kameras **Vignette** slideri [Reāllaika priekšskatījumā](#live-preview).

### Reāllaika priekšskatījums

* **Histogramma**izvēles rūtiņa + platuma sliders (0,10–0,90 no kadra) — histogrammas josla tiešraidē uz katra elementa flīzes vienlaikus (ieskaitot sadalīto kanālu flīzes), visam masīvam iestatītais [Histogrammas pārklājums](#display-overlays-drawn-over-the-live-feed), kas iestatīts visam masīvam. Tas parādās kā ieslēgts, ja katram dalībniekam tas jau ir.**Atsevišķā** masīvā šī ir vienīgā rinda grupā.
* *Tikai kombinēti:* **Indekss**: atzīmējiet izvēles rūtiņu + zobrats — atver kopīgo [indeksa kalkulatoru](#index-calculator-pane) ar joslām, kas izveidotas no**visām** dalībnieku kamerām. Izteiksmes priekšskatījuma rinda zemāk parāda pašreizējo izteiksmi („Izteiksme nav iestatīta — atveriet kalkulatoru, lai izveidotu jaunu”), kas tiek atjaunināta ik sekundi.
* *Tikai kombinētajā režīmā:* **Renderēšanas izšķirtspēja**izvēlne (tie paši iestatījumi kā katrai kamerai atsevišķi, noklusējums 720p): tiešraides plūsmas augstums**un** saglabātā kompozīcijas eksporta izmērs. Piezīme paneļā: „Priekšskatījums + saglabātās kompozīcijas izmērs. Katras kameras attēli vienmēr tiek eksportēti pilnā izšķirtspējā.”

### Ekrāna slāņi *(tikai kombinētajā režīmā)** **Iespējot** izvēles rūtiņa (noklusējumā izslēgta — galvenā kamera tiek parādīta tieši; ieslēgta = slāņots kompozīts).
* **Nolaižamās izvēlnes**Priekšplāns**/**Fons**: katra dalībnieka kamera (pēc nosaukuma) vai**Indekss**. Ja Priekšplāns ir Indekss, pikseļi ārpus LUT minimālā/maksimālā diapazona parāda Fona slāni.

### Dalītais skats *(tikai apvienotajā režīmā)*

**„Rādīt dalībnieku kameras”**— poga**„Sadalīt / Paslēpt dalībnieku kameras”**, kas pievieno katras dalībnieka kameras tiešraidi kā atsevišķus režģa laukumiņus blakus kompozīcijai. Laukumiņi nolasa masīva esošo kadru buferi (nav nepieciešams papildu kameras savienojums). Tikai režģa skatā; tiek saglabāts katram masīvam kopā ar projektu.

### Funkcijas

Tikai lasāms panelis, kas atjaunojas ik pēc 5 s:

* **Līmeņa apzīmējums**: „Vienlaicīga uzņemšana“ (zaļš) · „Vienlaicīga uzņemšana (FTD — pakāpeniska emisija)“ (zaļš) · „Pakāpeniska uzņemšana (100 ms novirze)“ (dzeltens) · "Konfigurācija pārāk liela" (sarkana).
* **Kadra stāvoklis**: "x,xx % nepabeigts" — zaļa, ja mazāk par 1 %, dzeltens, ja mazāk par 5 %, sarkana, ja 5 % vai vairāk.
* ****Savienojuma līnija**: „NIC {mbps} Mbps — ilgstošs {MB/s} MB/s”.

Tas ir masīva reālais joslas platuma budžets. Par pamatā esošo kadru skaitu sekundē (fps) un tīkla modeli — kā arī par to, ko mainīt, ja līmenis kļūst dzeltens vai sarkans — skatiet [Daudzkameru masīvi](arrays.md) un [CLI atsauci](../reference/cli-reference.md).

<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->

## Logs „Index Calculator“

Trešā sānu joslas lapa, ko kopīgi izmanto gan atsevišķu kameru „Index“ rīks, gan apvienotā masīva „Index“ rīks (vienlaikus tikai viens — virsraksts ir „Index Calculator — <camera name="">“ vai „Index Calculator — <array name="">“). Tajā tiek saņemts joslu saraksts (kameras filtra-dabiskās joslas vai visas joslas visās masīva kamerās), pašreizējo izteiksmi un LUT konfigurāciju (ieslēgts/izslēgts, līmenis — noklusējums 7 stopi, min. — noklusējums 0,2, maks. — noklusējums 1), kā arī indeksa histogrammu reāllaikā. Nospiežot **Piemērot**, izteiksme tiek piemērota; LUT izmaiņas tiek piemērotas priekšskatījumā reāllaikā.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Iestatījumi katrai kamerai atsevišķi vai masīva pārvaldīti

Īsa atsauce par to, kas atrodas kur, ja kamera ir masīva loceklis:

| Masīva pārvaldīti (kameras paneļā tikai lasāmi) | Joprojām katrai kamerai atsevišķi masīva ietvaros |
| --- | --- |
| Pikseļu formāts, izšķirtspēja, binning | Automātiskā ekspozīcija (ekspozīcija, pastiprinājums, mērķis, izlīdzināšana, ROI) |
| Izraisītāja režīms/avots, kadru ātrums | Trokšņu samazināšana, asums, vinjetēšana |
| | Orientācija (spoguļošana/pagriešana), ekrāna pārklājumi (masīva iestatījumi → „Live Preview” vienlaikus iestata histogrammu visiem masīva elementiem), punktu ekspozīcijas mērītājs |
| | Indekss (masīvi ar atsevišķiem ekrāniem), gaismas sensora piesaistīšana |

Citas vispārīgas funkcijas:

* **Kombinēts vai atsevišķs attēlojums** tiek izvēlēts masīva savienošanas brīdī: kombinēts = viena saskaņota salikta flīze (elementi tiek pārraidīti tikai caur Split View); atsevišķs = katrs elements renderē savu sinhronizētu flīzi. Kamera nekad neparāda gan atsevišķu plūsmu, gan masīva flīzi vienlaikus.
* ****Automātiska atkārtota savienošana**: atverot saglabātu projektu, tiek atjaunotas tā kameras un masīvi, un visi saglabātie iestatījumi tiek atkārtoti piemēroti aizmugurējam serverim, pirms straumes atsākas.
* **Uzņemšanas filtrēšana**: slēptās vai apturētās kameras tiek izslēgtas no funkcijas „Capture All“; masīvs tiek pilnībā bloķēts tikai tad, ja VISI elementi ir slēpti/apturēti. Skatīt [Ierakstīšanas iestatījumi un režīmi](capture.md).

## Kā iestatījumi tiek saglabāti

Kameru cilnes stāvoklis tiek saglabāts **kopā ar projektu**, nevis pārlūkprogrammā:

* Katra reaģējoša izmaiņa veic kameru un masīvu momentuzņēmumu projekta `cameras.json` (ar 500 ms atskaņošanas aizkavi). Tas ietver kameru nosaukumus un krāsas, ekspozīcijas/pastiprinājuma/AE iestatījumus, pikseļu formātu/izšķirtspēju/binningu, trigera ātrumu, priekšskatīšanas iestatījumus (renderēšanas izšķirtspēju, trokšņu samazināšanu, asumu, vinjeti, krāsu profilu, spilgtākās vietas, piesātinājumu/kontrastu), orientāciju, pārklājumus, kanālu sadalījumus, indeksa konfigurāciju, prognozējošos AE iestatījumus, AE ROI, masīvu nosaukumus, attēlošanas režīmu, masīva uzņemšanas iestatījumus (ieskaitot ROI apgriešanu, to, vai ROI ir ieslēgts, un saglabāto apgriešanu, kamēr tas ir izslēgts), masīva vadu budžetu un režģa bloku (plūsmas tālummaiņa, skatīšanas režīms, režģa fiksācija, manuāla flīžu secība, slēptās kameras, aizvērtās flīzes, aktīvā kamera).
* Gaismas sensoru saistījumi tiek saglabāti projekta failā `sensors.json`.
* Atverot projektu no jauna, tiek atjaunota savienojums ar aparatūru un atkārtoti piemēroti visi iestatījumi.
* **Ja projekts nav atvērts = tikai sesija**: ja nav projekta, pēc programmas Chloros aizvēršanas nekas netiek saglabāts.
* Tikai sesijai neatkarīgi no projekta: pauzes stāvoklis, punktu ekspozimetra paraugi, atsevišķu kameru kalibrēšanas mērķa izvēles rūtiņa (vienmēr atvērta) un matricas izlīdzināšanas profils (pēc dizaina pārrēķināts katrā sesijā).
* Viens izņēmums: **Uzņemšanas iestatījumu** eksporta izvēles un uzņemšanas režīms tiek saglabāti katram projektam atsevišķi vietējā lietotnes krātuvē, nevis `cameras.json` — skatīt [Uzņemšanas iestatījumi un režīmi](capture.md).</array></camera></serial>
