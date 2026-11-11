# Sadaļa „DAQ” programmā Chloros

Sadaļa „DAQ” — kas Chloros sānjoslā ir apzīmēta kā **Gaismas sensori** — ir reāllaika vadības panelis [DAQ-U, DAQ-M un DAQ-E gaismas sensoriem](README.md): pievienojiet sensorus, izmantojot jebkuru datu pārraides protokolu, reāllaikā vērojiet kalibrētos spektrus, aprēķiniet reāllaika atstarojumu no sensoru pāra un ierakstiet `.daq` failus tieši savā projektā.

Cilne kļūst pieejama, tiklīdz Chloros aizmugurējā sistēma ir pabeigusi palaišanu. Cilnes diagrammas tiek apgādātas ar datiem no „Chloros“ DAQ pakalpojuma, izmantojot reāllaika savienojumu, kas automātiski atjaunojas (2–10 s pārtraukuma laiks), ja tas tiek pārtraukts; kamēr pakalpojums nav sasniedzams, sensora statusa rindā redzams uzraksts **Nav servera**.

Izkārtojums sastāv no **sensoru sānu joslas**(viena rinda katram pieslēgtam sensoram) un**diagrammu zonas** (viena diagrammas flīze katram sensoram vai grupai).

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Sensora pieslēgšana

Noklikšķiniet uz **Pieslēgt sensoru** sānu joslas augšdaļā. Pieslēgšanās dialoglodziņš atveras galvenajā logā (vai kā pārklājums, pievienojot vēl vienu sensoru — šādā gadījumā parādās poga „Atcelt”).

| Kontrole | Darbība |
| --- | --- |
| **Ierīces tips** | `DAQ-U (USB)` (noklusējums), `DAQ-M (Bluetooth)` vai `DAQ-E (Ethernet)`. Pārslēgšanās atkārtoti sāk skenēšanu jaunizvēlētajam pārraides veidam. |
| **Ports / BLE ierīce / Hostvārds / IP** | Uzskaita atrastās ierīces kā `device - description`; automātiski tiek izvēlēts pirmais ieraksts, kas atpazīts kā sensors. Skenēšanas laikā tiek parādīts `Scanning...` (USB), `Scanning (N)...` ar 8 sekunžu atskaiti (BLE) vai `Discovering ethernet sensors (N)...` ar 5 sekunžu atskaiti (Ethernet). Ja rezultāti ir tukši, tiek parādīts `No ports` / `No BLE devices` / `No ethernet sensors found`. |
| **↻ Atjaunināt** | Nekavējoties atkārtoti skenē izvēlēto pārraides veidu (atspējots BLE/Ethernet skenēšanas laikā). |
| **Savienot sensoru** | Kļūst pieejams, tiklīdz ir izvēlēta ierīce; savienojuma izveides laikā nosaukums mainās uz `Connecting...`. |

Meklēšana darbojas tikai **kamēr ekrānā ir redzams savienošanas dialoglodziņš** un atkārtojas ik pēc 15 sekundēm tikai izvēlētajam transportam — vienkārša cilnes atvēršana neveic skenēšanu. Ja rodas kļūda, dialoglodziņā parādās: *&quot;Savienošanai neizdevās. Mēģiniet atvienot un atkārtoti pievienot sensoru, pēc tam noklikšķiniet uz &quot;Savienot&quot; vēlreiz.&quot;*

Sānu josla atveras automātiski, kad izveidojas savienojums ar pirmo sensoru.

{% hint style="info" %}
**DAQ-E nav redzams?**DAQ-E nav statusa LED — pārbaudiet PoE/savienojuma indikatoru uz komutatora vai injektora porta, kurā tas ir pievienots, un pēc ieslēgšanas pagaidiet dažas sekundes, lai ierīce uzsāktu darbu. Chloros ierīcei jāatrodas tajā pašā apraides domēnā (mDNS nešķērso maršrutētājus). Instalētāja**Chloros backend (UDP-In)** ugunsmūra noteikums ļauj ienākt mDNS (UDP 5353), DAQ-E datiem (UDP 5002) un PTP (UDP 319/320); trešās puses ugunsmūrim ir jāatļauj tie paši porti. Divas DAQ-E vienības vienā LAN tīklā tiek atklātas atsevišķi, katra ar savu `daq-e-<id>.local` uzņēmuma vārdu.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Ierīces tips piedāvā DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet)</figcaption></figure>

***

## Sensoru sānu josla

Katram pieslēgtam sensoram tiek piešķirta viena rinda (plus viena rinda katrai „Ambient+Object” grupai). Rindas var pārkārtot, velkot tās ar peles kursoru, un to secība maina arī diagrammu lauciņu secību. Noklikšķiniet uz rindas, lai šo sensoru/grupu padarītu par aktīvo diagrammu saraksta skatā.

| Elements | Nozīme |
| --- | --- |
| Krāsaina kreisā mala | Sensora diagrammas krāsa. |
| Transporta ikona | `DAQ-U` / `DAQ-M` / `DAQ-E` vai zaļa `REF` zīme „Ambient+Object“ atstarošanas grupai. |
| Ierīces nosaukums | Noklusējumā ir sensora sērijas numurs (tā stabilā identifikācija kalibrēšanai, `.daq` failu nosaukumiem un importēšanas saskaņošanai); pielāgotie nosaukumi saglabājas katram projektam atsevišķi. |
| **Kalibrēts** ikona (zaļa) | Parādās, kad ir ielādēts sensora rūpnīcas kalibrēšanas komplekts, t. i., spektri ir izteikti W/m²/nm. |
| **Pieejams atjauninājums** (dzintara krāsā, tikai DAQ-E) | Pašreizējā programmaparatūras versija ir vecāka par attēlu, kas iekļauts šajā Chloros

versijā. Atjaunināšanas laikā tiek parādīts reāllaika progresa rādītājs (`Flashing… N%`, `Restarting sensor…`, tad `Updated X → Y` vai `Failed`). |
| Acs | Pārslēdz šī sensora redzamību diagrammā. |
| Rīks | Atver sensora iestatījumu logu (zemāk). |
| ✕ (sarkans) | Atvieno sensoru vai noņem „Ambient+Object” grupu. |

Virs rindām atrodas divas pogas:

* **Pievienot sensoru** — atver savienošanas dialoglodziņu (darba laikā pārdēvējas par `Connecting...`).
* **Ierakstīt visu / Pārtraukt visu**— sāk vai pārtrauc `.daq` ierakstīšanu**visos**pieslēgtajos sensoros. Nepieciešams vismaz viens sensors**un atvērts projekts** (rādītājs: „Atveriet projektu, lai veiktu ierakstu”); tā kļūst sarkana, kamēr notiek jebkāds ieraksts.

Tukšā stāvoklī redzams uzraksts „Nav pieslēgtu sensoru”.

<!-- SCREENSHOT-NEEDED: sensor sidebar with three rows — a DAQ-E showing both the green Calibrated pill and the amber Update Available pill, a DAQ-U row, and a green REF group row — plus the Connect Sensor and Record All buttons -->

***

## Iestatījumi katram sensoram (rūtiņas logs)

Atveriet, noklikšķinot uz rīkzīmes ikonas sensora rindā. Saturs secībā:

* **Informācijas rindas** — Ierīces tips (DAQ-U/M/E), Savienojums (`Serial (USB)` / `Bluetooth` / `Ethernet`), ports (COM ports, BLE adrese vai hosts) un seriālais ports.
* **Kalibrēšanas ziņojums: lejupielādēt** — lejupielādē šīs ierīces NIST izsekojamu kalibrēšanas sertifikātu (PDF) un atver to jūsu PDF skatītājā. Pieejams, tiklīdz ir zināms sērijas numurs; sertifikāts tiek saglabāts kešatmiņā pēc pirmās savienošanās.
* **Ierīces nosaukums** — noklikšķiniet uz zīmuli, lai pārdēvētu; saglabājas katram projektam atsevišķi.
* **Grafika līnijas krāsa** — krāsu paraugs; saglabājas katram projektam atsevišķi.
* **Integrācijas laiks (ms)**— slīdnis + skaitlis,**1–500 ms**, noklusējums**32 ms**. Atspējots, kamēr AE ir ieslēgts.
* **Kadru vidējais**— slīdnis + skaitlis,**1–50 kadri**, noklusējums**20**.
* **AE: IESLĒGTS/IZSLĒGTS**— automātiskās ekspozīcijas slēdzis;**noklusējumā IESLĒGTS** pieslēgšanās brīdī. Izslēdziet to, lai integrācijas laiku iestatītu manuāli.
* **Pārtraukt straumēšanu / Sākt straumēšanu** — apturēt vai atsākt tiešraidi.
* **Ierakstīt / Pārtraukt ierakstīšanu** — ierakstīšana ar katru sensoru `.daq` (nepieciešams atvērts projekts).
* **Cap** — ekspozīcijas korekcijas profils (nākamajā sadaļā).
* **Reāllaika informācijas rindas** — integrācijas laiks (ms), kadru skaits sekundē (FPS), paraugi, ierakstīšana (sarkans `REC` vai `Off`) un statuss (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### Tikai DAQ-E: tīkla, programmaparatūras un PTP rindas

* **Hostname / IP** — ierīces pašreizējā adrese.
* **Programmatūra** — aktuālā programmatūras versija, kā arī darbības lauciņš:<version\>

pogu</version\>

**Atjaunināt uz \<version\>** parādās, ja šajā Chloros

versijā ir iekļauts jaunāks DAQ-E programmatūras attēls. Atjauninājums tiek nosūtīts pa tīklu aptuveni 30 sekundēs; sensors automātiski pārstartējas un atkārtoti izveido savienojumu, un pārtraukta pārsūtīšana atstāj pašreizējo programmatūru nemainīgu. Progresa dati tiek pārraidīti reāllaikā (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`), un šūnā tiek parādīts `Up to date`, ja tas ir aktuāls.
* **PTP sinhronizācija** — reāllaika PTP stāvoklis (pāriet uz `unknown`). DAQ-E programmaparatūras versija v1.2.0+ darbojas IEEE 1588 PTPv2 standartā kā tikai pakļautais pulkstenis; Chloros

a servera backend ir PTP grandmaster, un katra DAQ-E un LATTICE kamera lokālajā tīklā (LAN) darbojas kā tā pakļautais pulkstenis domēnā 0, uzturot laika zīmogus ar precizitāti aptuveni 1 ms.
* **Tīkls**— adrese, ar kuru ierīce darbojas, un kā tā to ieguvusi (DHCP, lokālā saite vai statiska), kā arī saglabāts iestatījums, kas tiek piemērots pēc pārstartēšanas.**Mainīt…**atver veidlapu:**Automātiski (DHCP)**vai**Statiska IP**ar IP adresi, apakštīkla masku un neobligātu vārteju.**Piemērot un pārstartēt** to saglabā, pārstartē sensoru un atkārtoti to pieslēdz. Statiska adrese ārpus šī datora tīkla tiek noraidīta, jo Chloros

pēc pārstartēšanas nevarēja sazināties ar sensoru; tad parādās lodziņš, kas ļauj to tomēr atļaut, ja dators tiks pāradresēts vai maršrutēts uz šo tīklu. **Datu plūsma ieslēgšanas brīdī**nosaka, vai sensors sāk datu plūsmu, tiklīdz tiek ieslēgts, pirms kāda programmatūra izveido savienojumu.**Restart sensor** (Pārstartēt sensoru) to pārstartē un atkārtoti savieno ar pašreizējiem iestatījumiem. Pārstartēšana tiek noraidīta, kamēr sensors veic ierakstīšanu. Jaunums versijā 1.3.0.

Ambient+Object grupai rīkzobu logā tiek parādīti tikai grupas avota sensori, ierīces nosaukums un grafika līnijas krāsa.

<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->



### Vāciņa

Nolaižamajā izvēlnē **Vāciņa** tiek norādīts programmai Chloros

, kāda fiziskā vāciņa ir uzlikta uz sensora difuzora, un katram spektram tiek piemērots šīs vāciņas rūpnīcā izmērītais korekcijas profils. Izvēles iespējas atkarīgas no modeļa:

| Modelis | Vāciņu izvēles |
| --- | --- |
| DAQ-U | Nav (atklāts sensors), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, „Sunshine” (kosinusa korektors) |
| DAQ-M | Nav (atklāts sensors), „Sunshine” (kosinusa korektors) |
| DAQ-E | Nav (atklāts sensors), FOV 15°, FOV 45°, FOV 90°, „Sunshine” (kosinusa korektors) |

**Visiem modeļiem noklusējuma iestatījums ir „Saules gaisma” (kosinusa korektors)** — MAPIR

piegādā katru DAQ ar uzstādītu „Saules gaismas” vāciņu, un tā ir standarta konfigurācija lietošanai ārpus telpām: 180° puslodes skats ar kosinusa kļūdu ≤ ±4 % līdz 60° un ≤ ±4,5 % līdz 70° (nav ieteicams, ja saules augstums ir zemāks par ~15°), ar konstrukcijā paredzētu vājinājumu (~12×). Jūsu izvēle tiek saglabāta projektā.

{% hint style="warning" %}
**Vāciņa jāizvēlas atbilstoši fiziski uzstādītajai vāciņai.**Ne sensors, ne programmatūra nespēj noteikt, kura vāciņa ir uzstādīta. Izvēle nosaka gan reāllaika korekciju, gan zīmogu, kas tiek ierakstīts katrā `.daq` failā — ņemot vērā „Sunshine” vāciņas ~12× vājinājumu, nedeklarēta vāciņa spektrus nepareizi koriģē aptuveni par šo koeficientu. (Tā paša vāciņa noņemšana un atkārtota uzstādīšana atkārtojas līdz aptuveni 1,5 %.) Izvēlieties**&quot;None&quot; (kails sensors)** tikai tad, ja vāciņa ir fiziski noņemta; DAQ-E gadījumā opcija „None” joprojām piemēro rūpnīcas ģeometrijas profilu tā iebūvētajam stikla difuzoram — tā nav bezdarbības opcija —, un DAQ-E bez difuzora ir laboratorijas konfigurācija, nevis atbalstīta lauka konfigurācija.
{% endhint %}

{% hint style="info" %}
Atjauninājums no iepriekšējās rokasgrāmatas: pārlūkprogrammā esošais slēdzis „Sunshine Diffuser Installed” (Saules gaismas difuzors uzstādīts) no versijas 1.1.0 vairs nav pieejams. Vāciņa tagad tiek apstrādāta atbilstoši katram sensoram atsevišķi, izmantojot vāciņas profilu, kas tiek piemērots servera pusē.
{% endhint %}

***

## Diagrammas laukums

Fiksētā augšējā joslā atrodas **slēdzis „saraksts ⇄ režģa skats”**un**diagrammas tālummaiņas** slīdnis (flīzes izmērs 200–2000 pikseļi). Skats automātiski pārslēdzas uz režģa skatu, ja ir vairāk nekā viena diagrammu grupa, un atgriežas pie saraksta skata, ja ir viena vai mazāk grupu. Skatījuma režīms un diagrammas izmērs tiek saglabāts katram projektam atsevišķi.

Katram sensoram **spektra diagramma** parāda:

* **X ass** — viļņa garums (nm). Sensora režģis ir 340–1010 nm ar 5 nm soli (135 punkti), kas attēlošanai tiek interpolēts līdz 1 nm.
* **Y ass**— kalibrētam sensoram (zaļā**Kalibrēts**ikona),**Jauda (W/m²/nm)**— radiometriski kalibrētais spektrālais starojuma blīvums visos trīs pārnesumos — ar automātisku SI prefiksu (m/µ/n), kas izvēlēts no maksimuma. Sensors, kura kalibrēšanas pakete nav ielādēta, attēlo savus neapstrādātos rādījumus kā**Intensitāti (skaitļus)**, nemērogotus. Ja diagrammā tiek attēloti abi veidi, jauda tiek parādīta pa kreiso asi, bet skaitļi — pa labo asi.
* Vienai līknei zem tās ir spektrāla varavīksnes krāsojuma pildījums; vairāki sensori vienā diagrammā tiek attēloti kā krāsainas līnijas ar izbalinātu pildījumu.
* **Pārvietojot kursoru**— parādās vertikāls kursors ar viļņa garumu un vērtību katram sensoram (W/m²/nm kalibrētam sensoram, citos gadījumos — kopējais skaits);**velciet**, lai mainītu mērogu (palielinājuma režīmā parādās samazināšanas poga).
* **+** poga (tikai režģa skatā), lai pievienotu sensoru šim grafikam vai izveidotu grupu (skatīt zemāk).
* Ierīces nosaukums izvietots centrā augšā un rotējošs indikators, kamēr tiek saņemts pirmais kadrs.

**Pārsātinājums** nav atzīmēts pašā diagrammā: pārsātināts sensors parāda sarkanu `SATURATED` statusa tekstu un sarkanu `Saturated: Yes` rindu reāllaika datu tabulā. Lai to dzēstu, samaziniet integrācijas laiku vai atkārtoti ieslēdziet AE.

<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->



***

## Reāllaika datu tabula (saraksta skats)

Zem diagrammas saraksta skatā, kas atjaunojas ik pēc 500 ms:

* **Visi modeļi**: Gaismas krāsas paraugs (sRGB no CIE XYZ), Piesātināts (Jā/Nē), CIE 1931 X/Y/Z, Hromatiskums x/y, CIE u′/v′, CCT (K), CRI (Ra), dominējošais viļņa garums (nm), maksimālais viļņa garums (nm), ekscitācijas tīrība, Duv, CIE L\*/a\*/b\* un Munsell H/V/C.
* **Tikai kalibrēti sensori**(jebkurš no DAQ-U / DAQ-M / DAQ-E, ja ir ielādēts tā rūpnīcas kalibrēšanas komplekts — to norāda zaļā**Kalibrēts** zīme sensora rindā): Kopējā jauda (W/m²), fotopiskais lukss (lx), skotopiskais lukss (lx), S/P attiecība, PPFD un PPFD Red

/Green

/Blue

(µmol/m²/s), kā arī opiskie starojuma blīvumi — S-konuss, melanopiskais, rodopiskais, M-konuss, L-konuss (visi W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Atstarošanas grupas (Vides + objekta)

Divi savienoti sensori var tikt apvienoti reāllaika atstarojuma attēlojumā — bez kameras izmantošanas:

1. Tīkla skatā noklikšķiniet uz **+**diagrammas lauciņā un izvēlieties**Apvienot vides + objekta**.
2. Izvēlieties **Vides gaismas avota**sensoru un**Objekta skenera**sensoru (divus atšķirīgus sensorus), pēc tam**Izveidot**.

Chloros

aprēķina R(λ) = objekts(λ) / vide(λ) katram viļņa garumam, izmantojot abus reāllaika datu plūsmas (0, ja vides apstākļi ≤ 0). Grupas nosaukums atbilst sensoru kalibrēšanas klasei:

* Abi sensori kalibrēti (komplekts ielādēts) → **„Šķietamā atstarojamība”**.
* Ja kāds no sensoriem nav kalibrēts → **&quot;Relatīvā atstarojamība&quot;**.

Grupa parādās kā zaļa `REF` rinda sānjoslā un savā diagrammā (varavīksnes krāsojums, vērtības pēc peles uzvedšanas līdz 4 zīmēm aiz komata, palielināšana ar velšanu).

Izvēlnē **+**ir pieejama arī opcija**Pievienot jaunu sensoru** ar trim izvietošanas iespējām: *Apvienot jauno sensoru* (pievienot šim grafikam), *Pārvietot esošo sensoru šeit* vai *Skatīt jauno sensoru* (atsevišķs grafiks).

<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->



### Veģetācijas indeksu tabula

Saraksta skatā zem atstarojuma grupas diagrammas atrodas veģetācijas indeksu tabula, kas aprēķināta, izmantojot reāllaika atstarojuma datus joslu centros **zils 450 / zaļš 550 / sarkans 670 / NIR

800 nm** (vērtības ar 4 zīmēm aiz komata, `---`, ja nav aprēķināmas; uzvediet kursoru uz indeksa nosaukuma, lai redzētu pilno nosaukumu):

* **Vienmēr tiek parādīts** (neskarot mērogu, jebkura sensoru kombinācija): NDVI

, GNDVI

, ENDVI, WDRVI

, GRVI

, CVI

, GCI

, MSR.
* **Tikai tad, ja abi sensori ir kalibrēti pēc jaudas** (abi sensoru komplekti ielādēti): EVI

, SAVI

, OSAVI

, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI

, NLI, MNLI, FCI, GEMI

.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## `.daq` failu ierakstīšana

* Ierakstīšanai ir nepieciešams **atvērts projekts** — pretējā gadījumā gan „Ierakstīt visu” (sānu joslā), gan katra sensora „Ierakstīt” poga ir atspējotas.
* Faili tiek saglabāti mapē **`<project folder>/light_sensor/`**; failu nosaukumos ir iekļauts sensora ID un laika zīmogs, un ieraksta laikā tiek saglabāts arī ierīces nosaukums.
* Kad ierakstīšana tiek pārtraukta (ar pogu „Stop”, „Stop All” vai savienojuma pārtraukšanu ierakstīšanas laikā), pabeigtais `.daq` **tiek automātiski pievienots atvērtam projektam** — tas parādās projekta failu sarakstā bez manuālas pievienošanas, gatavs kalpot kā lejupvērstie dati [atstarošanas apstrādei](README.md).
* Ierakstīšanas laikā iestatījumu loga reāllaika rindās parādās sarkans `REC` indikators.

Lai iegūtu kvantitatīvus starojuma intensitātes rādītājus, aprēķiniet vidējo vērtību vismaz 15 sekunžu datiem — tā ir ierīces īpašība, nevis defekts.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->



***

## Daudzsensoru izkārtojumi un projekta saglabāšana

* Apvienojiet vairākus sensorus vienā diagrammā (kopīgas ass), saglabājiet atsevišķas diagrammas (automātisks režģa izkārtojums), pārvietojiet sensorus starp diagrammām, pārvelciet un pārkārtojiet rindas/flīzes, kā arī paslēpiet atsevišķus sensorus, izmantojot acs ikonu.
* Katram projektam Chloros

saglabā: ierīču nosaukumus, diagrammu krāsas, diagrammas izmēru, skatīšanas režīmu un katra sensora iestatījumus (integrācijas laiku, kadru vidējošanu, AE stāvokli, vāciņa izvēli).
* **Atverot projektu no jauna, tā sensori automātiski atkal savienojas** pēc adreses — COM ports DAQ-U gadījumā, BLE ierīce DAQ-M gadījumā, mDNS uzņēmuma nosaukums DAQ-E gadījumā (tiek atrisināts pat tad, ja ierīces IP adrese ir mainījusies) — un atkārtoti piemēro katra sensora saglabāto vāciņa profilu, kadru vidējošanu, AE stāvokli un manuālo integrācijas laiku.***

## Kameras pārošana (DLS)

Nav nekas, ko pārot. Atšķirībā no dronu DLS darba plūsmām, kurās gaismas sensors tiek saistīts ar kameru jau sākumā, Chloros

saskaņo DAQ datus ar attēliem vēlāk: importēšanas/apstrādes brīdī `.daq` rādījumi tiek interpolēti uz katra uzņēmuma ekspozīcijas laika zīmogu. Veiciet ierakstu ar jebkuru pieslēgtu sensoru (`.daq` automātiski nonāk projektā), un atstarojuma apstrāde atrod pareizos rādījumus pēc laika — skatiet [DAQ gaismas sensori](README.md), lai uzzinātu, kā tiek izmantoti lejupvērstie dati. **Reāllaika**LATTICE uzņēmumiem izvēlieties sensoru cilnē „Cameras” (Kameras) izvēlnē**Light Sensor** (Gaismas sensors); tad katrs uzņēmums saglabā izmantoto rādījumu, kas atbilst tikai šī uzņēmuma kamerām (skatiet [Atstarošanas darba plūsmas](reflectance.md)).</version\>
