# Sadaļa „DAQ” programmā Chloros

Lapa „DAQ” — Chloros sānjoslā apzīmēta kā **Gaismas sensori** — ir reāllaika vadības panelis [DAQ-U, DAQ-M un DAQ-E gaismas sensoriem](README.md): pievienojiet sensorus, izmantojot jebkuru datu pārraides protokolu, reāllaikā vērojiet kalibrētus spektrus, aprēķiniet reāllaika atstarojumu no sensoru pāra un ierakstiet `.daq` failus tieši savā projektā.

Cilne kļūst pieejama, tiklīdz ir pabeigta Chloros aizmugurējā sistēmas palaišana. Cilnes diagrammas tiek apgādātas ar Chloros DAQ pakalpojumu, izmantojot tiešsaistes savienojumu, kas automātiski atjaunojas (2–10 s atkāpe), ja tas tiek pārtraukts; kamēr pakalpojums nav sasniedzams, sensora statusa rindā redzams **Nav servera**.

Izkārtojums sastāv no **sensoru sānu joslas**(viena rinda katram pieslēgtam sensoram) un**diagrammu zonas** (viena diagrammas flīze katram sensoram vai grupai).

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Sensora pieslēgšana

Noklikšķiniet uz **Pievienot sensoru** sānu joslas augšdaļā. Pievienošanas dialoglodziņš atveras galvenajā zonā (vai kā pārklājums, pievienojot vēl vienu sensoru — šādā gadījumā parādās poga „Atcelt”).

| Vadības elements | Darbība |
| --- | --- |
| **Ierīces tips** | `DAQ-U (USB)` (noklusējums), `DAQ-M (Bluetooth)` vai `DAQ-E (Ethernet)`. Pārslēgšanās atsāk skenēšanu jaunizvēlētajam transportam. |
| **Ports / BLE ierīce / Hostvārds / IP** | Uzskaita atrastās ierīces kā `device - description`; automātiski tiek izvēlēts pirmais ieraksts, kas atpazīts kā sensors. Skenēšanas laikā tiek parādīts `Scanning...` (USB), `Scanning (N)...` ar 8 sekunžu atskaiti (BLE) vai `Discovering ethernet sensors (N)...` ar 5 sekunžu atskaiti (Ethernet). Ja rezultāti ir tukši, tiek parādīts `No ports` / `No BLE devices` / `No ethernet sensors found`. |
| **↻ Atjaunināt** | Nekavējoties atkārtoti skenē izvēlēto transporta veidu (atspējots BLE/Ethernet skenēšanas laikā). |
| **Savienot** | Kļūst pieejama, tiklīdz ir izvēlēta ierīce; savienojuma izveides laikā nosaukums mainās uz `Connecting...`. |

Meklēšana notiek tikai **tad, kad ekrānā ir redzams savienošanās dialoglodziņš**, un atkārtojas ik pēc 15 sekundēm tikai izvēlētajam transportam — vienkārša cilnes atvēršana nesāk skenēšanu. Ja rodas kļūda, dialoglodziņā parādās: *&quot;Savienošanās neizdevās. Mēģiniet atvienot un atkārtoti pievienot sensoru, pēc tam atkal noklikšķiniet uz &quot;Savienot&quot;.&quot;*

Sānu josla atveras automātiski, kad izveidojas savienojums ar pirmo sensoru.

{% hint style="info" %}
**DAQ-E nav redzams?** DAQ-E nav statusa LED — pārbaudiet PoE/savienojuma indikatoru komutatorā vai injektora portā, kurā tas ir pievienots, un pēc ieslēgšanas pagaidiet dažas sekundes, līdz ierīce uzsāk darbu. Chloros ierīcei jāatrodas tajā pašā apraides domēnā (mDNS nešķērso maršrutētājus). Ierīcē Windows apstipriniet Defender ugunsmūra pieprasījumu, kad ierīce Chloros pirmo reizi izveido savienojumu ar multiraides ligzdām (mDNS UDP 5353, DAQ-E dati UDP 5002, PTP UDP 319/320). Divas DAQ-E vienības vienā LAN tīklā tiek atklātas atsevišķi, katra ar savu `daq-e-<id>.local` uzņēmuma nosaukumu.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Ierīces tips piedāvā DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet)</figcaption></figure>***

## Sensoru sānu josla

Katram pieslēgtajam sensoram tiek piešķirta viena rinda (plus viena rinda katrai „Ambient+Object“ grupai). Rindas var pārkārtot, velkot tās ar peles kursoru, un to secība maina arī diagrammu lauciņu secību. Noklikšķiniet uz rindas, lai šo sensoru/grupu padarītu par aktīvo diagrammu saraksta skatā.

| Elements | Nozīme |
| --- | --- |
| Krāsaina kreisā mala | Sensora diagrammas krāsa. |
| Transporta ikona | `DAQ-U` / `DAQ-M` / `DAQ-E`, vai zaļa `REF` zīme „Ambient+Object“ atstarošanas grupai. |
| Ierīces nosaukums | Noklusējumā ir sensora sērijas numurs (tā stabilā identifikācija kalibrēšanai, `.daq` failu nosaukumiem un importēšanas saskaņošanai); pielāgotie nosaukumi saglabājas katrā projektā. |
| **Kalibrēts** ikona (zaļa) | Parādās, kad ir ielādēts sensora rūpnīcas kalibrēšanas komplekts, t. i., spektri ir izteikti W/m²/nm. |
| **Pieejams atjauninājums** (dzintara krāsā, tikai DAQ-E) | Pašreizējā programmaparatūras versija ir vecāka par attēlu, kas iekļauts šajā Chloros versijā. Atjaunināšanas laikā tiek parādīts reāllaika progress (`Flashing… N%`, `Restarting sensor…`, tad `Updated X → Y` vai `Failed`). |
| Acs | Pārslēdz šī sensora redzamību diagrammā. |
| Rīks | Atver sensora iestatījumu logu (zemāk). |
| ✕ (sarkans) | Atvieno sensoru vai noņem „Ambient+Object” grupu. |

Virs rindām atrodas divas pogas:

* **Pievienot sensoru** — atver savienošanas dialoglodziņu (darba laikā nosaukums mainās uz `Connecting...`).
* **Ierakstīt visu / Pārtraukt visu**— sāk vai pārtrauc `.daq` ierakstīšanu uz**visiem**pieslēgtajiem sensoriem. Nepieciešams vismaz viens sensors**un atvērts projekts** (rādītājs: &quot;Atveriet projektu, lai veiktu ierakstu&quot;); pogai kļūst sarkana, kamēr notiek ierakstīšana.

Tukšā stāvoklī redzams uzraksts „Nav pieslēgtu sensoru”.

<!-- SCREENSHOT-NEEDED: sensor sidebar with three rows — a DAQ-E showing both the green Calibrated pill and the amber Update Available pill, a DAQ-U row, and a green REF group row — plus the Connect Sensor and Record All buttons -->

***

## Iestatījumi katram sensoram (modālais logs ar zobratu ikonu)

Atveriet, noklikšķinot uz zobratu ikonas sensora rindā. Saturs secībā:

* **Informācijas rindas** — Ierīces tips (DAQ-U/M/E), Savienojums (`Serial (USB)` / `Bluetooth` / `Ethernet`), ports (COM ports, BLE adrese vai hosts) un seriālais numurs.
* **Kalibrēšanas ziņojums: Lejupielādēt** — lejupielādē šīs ierīces NIST izsekojamu kalibrēšanas sertifikātu (PDF) un atver to jūsu PDF skatītājā. Pieejams, tiklīdz ir zināms sērijas numurs; sertifikāts tiek saglabāts kešatmiņā pēc pirmās savienošanās.
* **Ierīces nosaukums** — noklikšķiniet uz zīmuli, lai pārdēvētu; saglabājas katram projektam atsevišķi.
* **Grafikas līnijas krāsa** — krāsu paraugs; saglabājas katram projektam atsevišķi.
* **Integrācijas laiks (ms)**— slīdnis + skaitlis,**1–500 ms**, noklusējums**32 ms**. Atspējots, ja AE ir ieslēgts.
* **Kadru vidējais skaits**— slīdnis + skaitlis,**1–50 kadri**, noklusējums**20**.
* **AE: IESLĒGTS/IZSLĒGTS**— automātiskās ekspozīcijas slēdzis;**noklusējumā IESLĒGTS** savienošanās brīdī. Izslēdziet to, lai integrācijas laiku iestatītu manuāli.
* **Pārtraukt straumēšanu / Sākt straumēšanu** — apturēt vai atsākt tiešraidi.
* **Ierakstīt / Pārtraukt ierakstīšanu** — ierakstīšana ar sensoru `.daq` (nepieciešams atvērts projekts).
* **Cap** — ekspozīcijas korekcijas profils (nākamajā sadaļā).
* **Reāllaika informācijas rindas** — integrācijas laiks (ms), kadru skaits sekundē (FPS), paraugi, ierakstīšana (sarkans `REC` vai `Off`)un statuss (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### Tikai DAQ-E: tīkla, programmaparatūras un PTP rindas

* **Hostname / IP** — ierīces pašreizējā adrese.
* **Programmatūra** — aktuālā programmatūras versija, kā arī darbības lauciņš:<version\>

pogas</version\>

**Atjaunināt uz \<version\>** parādās, ja šajā Chloros versijā ir iekļauts jaunāks DAQ-E programmatūras attēls. Atjauninājums tiek nosūtīts pa tīklu aptuveni 30 sekundēs; sensors automātiski pārstartējas un atkārtoti izveido savienojumu, un pārtraukta datu pārsūtīšana atstāj pašreizējo programmatūru neskartu. Progress tiek rādīts reāllaikā (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`), un, ja versija ir aktuāla, šūnā redzams `Up to date`.
* **PTP sinhronizācija** — reāllaika PTP stāvoklis (atgriežas pie `unknown`). DAQ-E programmaparatūras versija v1.2.0+ darbojas IEEE 1588 PTPv2 standartā kā tikai pakļautais pulkstenis; Chloros galvenā datora backend ir PTP galvenais pulkstenis, un visas DAQ-E un LATTICE kameras lokālajā tīklā (LAN) darbojas kā tā pakļautie pulksteņi domēnā 0, uzturot laika zīmogus ar precizitāti aptuveni 1 ms.

„Ambient+Object“ grupai aprīkojuma modālais logs parāda tikai grupas avota sensorus, ierīces nosaukumu un grafika līnijas krāsu.

<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->

### Vāciņa izvēle

Nolaižamais izvēlnes elements **Cap** norāda Chloros, kurš fiziskais vāciņš ir uzstādīts uz sensora difuzora, un piemēro šī vāciņa rūpnīcā izmērīto korekcijas profilu katram spektram. Izvēles iespējas atkarīgas no modeļa:

| Modelis | Vāciņu izvēles |
| --- | --- |
| DAQ-U | Nav (atklāts sensors), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, „Sunshine“ (kosinusa korektors) |
| DAQ-M | Nav (atklāts sensors), „Sunshine” (kosinusa korektors) |
| DAQ-E | Nav (atklāts sensors), FOV 15°, FOV 45°, FOV 90°, „Sunshine” (kosinusa korektors) |

**Visiem modeļiem noklusējuma iestatījums ir „Saules gaisma” (kosinusa korektors)** — MAPIR piegādā katru DAQ ar uzstādītu „Saules gaisma” vāciņu, un tā ir standarta āra konfigurācija: 180° puslodes skats ar kosinusa kļūdu ≤ ±4 % līdz 60° un ≤ ±4,5 % līdz 70° (nav ieteicams, ja Saules augstums ir zemāks par ~15°), ar konstrukcijā iestrādātu vājinājumu (~12×). Jūsu izvēle tiek saglabāta projektā.

{% hint style="warning" %}
**Vāciņa jāizvēlas atbilstoši fiziskajam vāciņam.**Ne sensors, ne programmatūra nespēj noteikt, kurš vāciņš ir uzstādīts. Izvēle ietekmē gan reāllaika korekciju, gan zīmogu, kas tiek ierakstīts katrā `.daq` failā — ņemot vērā Sunshine vāciņa ~12× vājinājumu, nedeklarēta vāciņa izmaiņa spektrus nepareizi koriģē aptuveni par šo koeficientu. (Tā paša vāciņa noņemšana un atkārtota uzstādīšana atkārtojas aptuveni 1,5 % apmērā.) Izvēlieties**„None” (kails sensors)** tikai tad, ja vāciņš ir fiziski noņemts; DAQ-E gadījumā opcija „None” joprojām piemēro rūpnīcas ģeometrijas profilu tā iebūvētajam stikla difuzoram — tā nav bezdarbības opcija —, un DAQ-E bez difuzora ir laboratorijas konfigurācija, nevis atbalstīta lauka konfigurācija.
{% endhint %}

{% hint style="info" %}
Atjauninājums no iepriekšējās rokasgrāmatas: pārlūkprogrammā esošais slēdzis „Sunshine Diffuser Installed” (Saules gaismas difuzors uzstādīts) no versijas 1.1.0 vairs nav pieejams. Vāciņa tagad tiek apstrādāta saskaņā ar šo sensora vāciņas profilu, kas tiek piemērots servera pusē.
{% endhint %}

***

## Diagrammu apgabals

Fiksētā augšējā joslā atrodas **slēdzis „saraksts ⇄ režģa skats”**un**diagrammas tālummaiņas** slīdnis (flīzes izmērs 200–2000 pikseļi). Skats automātiski pārslēdzas uz režģa skatu, ja ir vairāk nekā viena diagrammu grupa, un atgriežas pie saraksta skata, ja ir viena vai mazāk grupu. Skatīšanas režīms un diagrammas izmērs tiek saglabāts katram projektam atsevišķi.

Katram sensoram **spektra diagramma** parāda:

* **X ass** — viļņa garums (nm). Sensora režģis ir 340–1010 nm ar 5 nm soli (135 punkti), kas attēlošanai tiek interpolēts līdz 1 nm.
* **Y ass** — jauda (W/m²), ar automātisku SI prefiksu (m/µ/n), kas izvēlēts no maksimālās vērtības. Spektri ir radiometriski kalibrēti spektrālie starojuma intensitātes rādītāji (W/m²/nm) visos trīs pārraides veidos.
* Vienas līknes apakšā redzama varavīksnes spektrālā pildījuma krāsa; vairāki sensori vienā diagrammā tiek pārklāti kā krāsainas līnijas ar izbalinātu pildījumu.
* **Pārvietojot kursoru**— vertikāls kursors ar viļņa garumu un vērtību katram sensoram;**velciet**, lai mainītu mērogu (palielinājuma režīmā parādās samazināšanas poga).
* **+** poga (tikai režģa skatā), lai pievienotu sensoru šim grafikam vai izveidotu grupu (skatīt zemāk).
* Ierīces nosaukums izvietots centrā augšā, un rotējošs indikators, kamēr tiek saņemts pirmais kadrs.

**Piesātinājums** nav atzīmēts pašā diagrammā: piesātināts sensors parāda sarkanu `SATURATED` statusa tekstu un sarkanu `Saturated: Yes` rindu reāllaika datu tabulā. Lai to dzēstu, samaziniet integrācijas laiku vai atkārtoti ieslēdziet AE.

<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->

***

## Reāllaika datu tabula (saraksta skats)

Zem diagrammas saraksta skatā, kas atjaunojas ik pēc 500 ms:

* **Visi modeļi**: Gaismas krāsas paraugs (sRGB no CIE XYZ), Pārsātināts (Jā/Nē), CIE 1931 X/Y/Z, Hromatiskums x/y, CIE u′/v′, CCT (K), CRI (Ra), Dominējošais viļņa garums (nm), maksimālais viļņa garums (nm), ekscitācijas tīrība, Duv, CIE L\*/a\*/b\* un Munsell H/V/C.
* **Tikai kalibrēti sensori**(jebkurš no DAQ-U / DAQ-M / DAQ-E, ja ir ielādēts tā rūpnīcas kalibrēšanas komplekts — to norāda zaļā**Kalibrēts** zīme sensora rindā): Kopējā jauda (W/m²), Fotopiskais lukss (lx), skotopiskais lukss (lx), S/P attiecība, PPFD un PPFD Red/Green/Blue (µmol/m²/s) un opiskie starojuma blīvumi — S-konuss, melanopiskais, rodopiskais, M-konuss, L-konuss (visi W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Atstarošanas grupas (apkārtējā vide + objekts)

Divi savienoti sensori var tikt apvienoti reāllaika atstarojuma attēlošanai — bez kameras iesaistīšanas:

1. Tīkla skatā noklikšķiniet uz **+**diagrammas lauciņā un izvēlieties**Apvienot apkārtējo gaismu + objektu**.
2. Izvēlieties **Vides gaismas avota**sensoru un**Objekta skenera**sensoru (divus atšķirīgus sensorus), pēc tam**Izveidot**.

Chloros aprēķina R(λ) = objekts(λ) / apkārtējā gaisma(λ) katram viļņa garumam, izmantojot abus reāllaika datu plūsmas (0, ja apkārtējā gaisma ≤ 0). Grupas nosaukums atbilst sensoru kalibrēšanas klasei:

* Abi sensori kalibrēti (komplekts ielādēts) → **&quot;Šķietamā atstarošanās&quot;**.
* Ja kāds no sensoriem nav kalibrēts → **&quot;Relatīvā atstarošanās&quot;**.

Grupa tiek parādīta kā zaļa `REF` rinda sānjoslā un savā atsevišķajā diagrammā (pildīta ar varavīksnes krāsām, vērtības parādās, uzvedot kursoru, līdz 4 zīmēm aiz komata, palielināšana ar velkšanu).

Izvēlnē **+**ir pieejama arī opcija**Pievienot jaunu sensoru** ar trim izvietojuma iespējām: *Apvienot jauno sensoru* (pievienot šim grafikam), *Pārvietot esošo sensoru šeit* vai *Skatīt jauno sensoru* (atsevišķs grafiks).

<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->

### Veģetācijas indeksa tabula

Saraksta skatā zem atstarojuma grupas diagrammas atrodas veģetācijas indeksa tabula, kas aprēķināta, izmantojot reāllaika atstarojuma datus joslu centros **zils 450 / zaļš 550 / sarkans 670 / NIR 800 nm** (vērtības ar 4 zīmēm aiz komata, `---`, ja nav aprēķināms; uzvediet kursoru uz indeksa nosaukuma, lai redzētu tā pilno nosaukumu):

* **Vienmēr tiek parādīti** (neskar mērogu, jebkura sensoru kombinācija): NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR.
* **Tikai tad, ja abi sensori ir kalibrēti pēc jaudas** (abi sensoru komplekti ielādēti): EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## `.daq` failu ierakstīšana

* Ierakstīšanai ir nepieciešams **atvērts projekts** — pretējā gadījumā gan funkcija „Ierakstīt visu” (sānu joslā), gan katra sensora ierakstīšanas poga ir atspējotas.
* Faili tiek saglabāti kā **`<project folder>/light_sensor/`**; failu nosaukumos ir iekļauts sensora ID un laika zīmogs, un ieraksta laikā tiek saglabāts arī ierīces nosaukums.
* Kad ierakstīšana tiek pārtraukta („Stop”, „Stop All” vai savienojuma pārtraukums ierakstīšanas laikā), pabeigtais `.daq` **tiek automātiski pievienots atvērtam projektam** — tas parādās projekta failu sarakstā bez manuālas pievienošanas, gatavs kalpot kā lejupvērstie dati [atstarošanas apstrādei](README.md).
* Ierakstīšanas laikā iestatījumu loga reāllaika rindās parādās sarkans `REC` indikators.

Lai iegūtu kvantitatīvus starojuma intensitātes rādītājus, aprēķiniet vidējo vērtību vismaz 15 sekunžu datiem — tā ir ierīces īpašība, nevis defekts.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->

***

## Daudzsensoru izkārtojumi un projekta saglabāšana

* Apvienojiet vairākus sensorus vienā diagrammā (kopīgas ass), saglabājiet atsevišķas diagrammas (automātisks režģa izkārtojums), pārvietojiet sensorus starp diagrammām, pārvelciet un mainiet rindu/lauciņu secību, kā arī paslēpiet atsevišķus sensorus, izmantojot acs ikonu.
* Katram projektam Chloros saglabā: ierīču nosaukumus, diagrammu krāsas, diagrammas izmēru, skatīšanas režīmu un katra sensora iestatījumus (integrācijas laiku, kadru vidējošanu, AE stāvokli, vāciņa izvēli).
* **Atverot projektu no jauna, tā sensori automātiski atkal savienojas** pēc adreses — COM ports DAQ-U gadījumā, BLE ierīcei DAQ-M, mDNS uzņēmuma nosaukums DAQ-E (tiek atrisināts pat tad, ja ierīces IP ir mainījies) — un atkārtoti piemēro katra sensora saglabāto vāciņa profilu, kadru vidējošanu, AE stāvokli un manuālo integrācijas laiku.***

## Kameras pārošana (DLS)

Nav nekas, ko pāri savienot. Atšķirībā no dronu DLS darba plūsmām, kurās gaismas sensors tiek saistīts ar kameru jau sākumā, Chloros saskaņo DAQ datus ar attēliem pēcapstrādes posmā: importēšanas/apstrādes brīdī `.daq` rādījumi tiek interpolēti uz katra attēla ekspozīcijas laika zīmogu. Veiciet ierakstu ar jebkuru pieslēgtu sensoru (`.daq` automātiski tiek iekļauts projektā), un atstarojuma apstrāde atrod pareizos rādījumus pēc laika — skatiet [DAQ gaismas sensori](README.md), lai uzzinātu, kā tiek izmantoti lejupvērstie dati.</version\>
