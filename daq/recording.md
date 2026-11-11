# Ierakstīšana un .daq formāts

`.daq` fails ir „Chloros” gaismas sensora ierakstīšanas formāts: **SQLite datu bāze**, kas satur kalibrētus spektrālos kadrus no viena DAQ sensora. Ierakstiet vienu failu uzņemšanas sesijas laikā, un atstarojuma apstrādes sistēma vēlāk varēs katru attēlu dalīt ar lejupvērsto starojuma intensitāti, kas izmērīta tieši tajā brīdī.

## Kas ir iekļauts .daq failā

| Īpašība | Vērtība |
| --- | --- |
| Konteiners | SQLite datubāze, viens fails katram sensoram katrai ierakstīšanai |
| Faila nosaukums | Ietver **sensora ID**un**laika zīmogu**, piemēram, `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektrs katrā kadrā | 135 punkti, 340–1010 nm ar 5 nm soli, kā arī CIE XYZ trīsstimulusa vērtības |
| Vienības | Kalibrēts spektrālais starojuma blīvums, **W/m²/nm** (piemērots rūpnīcas kalibrēšanas komplekts + vāciņa profils) |
| Ierakstītie metadati | Sensora ID (atslēga, lai iegūtu šīs vienības rūpnīcas kalibrāciju) un spēkā esošais vāka profils — skatīt [Vāka profili un kalibrētais diapazons](caps-and-range.md) |

Formāts ir identisks visiem DAQ-U, DAQ-M, DAQ-E un DAQ-E-S modeļiem, tādēļ turpmākajai apstrādei nav nozīmes, kurš transportlīdzeklis to ir ierakstījis. DAQ-E-S ierakstā blakus katram spektram tiek saglabāts arī ierīces sānsvārstības, griešanās un slīpuma leņķis.

**Ieraksti saglabājas arī strāvas padeves pārtraukuma gadījumā (1.3.0).**Reāllaika ieraksts tiek rakstīts, izmantojot žurnālu un veicot pilnu diska sinhronizāciju pēc katras izmaiņu saglabāšanas, tādējādi strāvas padeves pārtraukums vai pārtraukts process izraisīs pēdējo apmēram 3 sekunžu datu zudumu, nevis visa faila zudumu. Ieraksts, kas pārtraukts rakstīšanas vidū, var atstāt failu `<name>.daq-journal` blakus failam `.daq`:**saglabājiet un kopējiet abus kopā.** Chloros atgriež žurnālu atpakaļ, pirmoreiz lasot failu. Aizņemts disks aizkavē faila ierakstīšanu, nekad ne sensora rādījumus.

**DAQ-E, kas pārtrauc datu nosūtīšanu, tiek atkārtoti pieslēgts (1.3.0).** Kad DAQ-E datu plūsma apklust (tika pārstartēts tā komutators vai pārtrūka savienojums), backend to atkārtoti pieslēdz ar iepriekšējiem iestatījumiem, un ierakstīšana turpinās tajā pašā `.daq` failā, kurā ir atzīmēts pārtraukums.

Kalibrētai ierakstīšanai ir nepieciešams sensora rūpnīcas kalibrēšanas komplekts. DAQ-U un DAQ-M gadījumā backend lejupielādē komplektu no MAPIR mākoņa pēc sensora ID (ja tas nav iespējams, ierakstīšana tiek atteikta); DAQ-E vienības ir izņēmums, jo tās glabā kalibrēšanas datus pašā ierīcē.

## Ierakstīšana no GUI

Ierakstīšanai GUI ir nepieciešams **atvērts projekts** (pretējā gadījumā pogas „Ierakstīt” ir atspējotas):

* **Ierakstīt visu / Pārtraukt visu** — augšpusē gaismas sensoru sānjoslā; vienlaikus sāk vai pārtrauc `.daq` ierakstu visos pieslēgtajos sensoros.
* **Ierakstīt / Pārtraukt ierakstīšanu** — katram sensoram atsevišķi, rīka iestatījumu logā. Ierakstīšanas laikā sensora reāllaika informācijas rindās parādās sarkans „REC” indikators.

Faili tiek saglabāti `<project>/light_sensor/`, un, kad ierakstīšana tiek apturēta — vai nu izmantojot „Apturēt”, „Apturēt visu” vai atvienojot ierakstīšanas sensoru —, pabeigtais `.daq` tiek **automātiski pievienots atvērtam projektam**. Tas parādās projekta failu sarakstā bez manuālas pievienošanas, jau gatavs atstarošanas apstrādei.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->



## Ierakstīšana no CLI



CLI veic ierakstīšanu, izmantojot backend sensoru kopu (backendam jābūt darbojošam — šīs komandas ir vienkārši HTTP klienti):

```bash
# Connect the sensor into the backend pool
chloros-cli daq pool-connect --eth-host daq-e-def330.local

# Record for 150 seconds, with a human-friendly device label
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
    -o ./out --device-name "rooftop-A"

# Or run open-ended and stop explicitly
chloros-cli daq pool-record --sensor-id daq-e-def330            # --duration defaults to 0 = run until --stop
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

Iegūst `--sensor-id` vērtību no `chloros-cli daq pool-list`. Divas noderīgas noklusējuma vērtības:

| Opcija | Noklusējums |
| --- | --- |
| `--duration` | `0` — ierakstīt līdz `pool-record --stop` |
| `--output` / `-o` | `~/Documents/DAQ Live View/` atrodas **backend** failu sistēmā, nevis CLI |

Atšķirība starp izvades direktorijiem ir svarīga, ja CLI ir vērsta uz backend, kas atrodas citā datorā: fails nonāk tur, kur darbojas backend. Ja backend atrodas tajā pašā datorā, relatīvais `-o` (piemēram, iepriekš minētais `./out`) tiek atrisināts, ņemot vērā mapi, kurā tika palaista komanda.

## Ierakstīšana no Python

`DAQSensorSession` (ko atgriež `chloros_sdk.connect_daq_sensor()`) atklāj to pašu apvienoto ierakstu: `record_start(output_dir=None, device_name=None)` atgriež faila ceļu, `record_stop()` atgriež `{path, rows}`. Pilnu sesijas API skatiet [SDK atsauces materiālā](../reference/sdk-reference.md). SDK tiešās aparatūras klases (tikai datoru instalācijām) pēc noklusējuma ierakstus raksta uz `~/Documents/DAQ/`; izlaistajās versijās tiek atbalstīts iepriekš minētais apvienotais ceļš.

## .daq faila izmantošana apstrādes laikā

Lai no attēliem iegūtu atstarojumu, programmai Chloros ir nepieciešams katrai ekspozīcijai atbilstošs lejupvērstais starojuma intensitātes lielums:

* **Saglabājiet failu `.daq` kopā ar attēliem.**Apstrādes laikā apstrādes ķēde automātiski nosaka**laika zīmogam atbilstošo lejupvērsto starojuma intensitāti** no ierakstītā `.daq` (jebkura DAQ modeļa) — vai no DAQ-M nativā `.csv` — kas atrodas kopā ar attēliem. GUI ieraksti šo nosacījumu izpilda automātiski, jo tie tiek pievienoti projektam brīdī, kad tiek pārtraukta to ierakstīšana.
* **Kalibrēšana tiek lejupielādēta pēc pieprasījuma.** Ja kamerai vai DAQ ierīcei paredzētais rūpnīcas kalibrēšanas komplekts vēl nav saglabāts vietējā kešatmiņā, Chloros to pirmajā lietošanas reizē automātiski lejupielādē no „MAPIR” mākoņa (vienreiz nepieciešams interneta savienojums; saglabāts `~/.chloros/` failā).
* **Reāllaika uzņēmumi raksta savu papildu failu.** Jebkuram reāllaikā uzņemtam atstarošanas kadram faktiski izmantotais DAQ rādījums tiek saglabāts kā `.daq` fails uzņemumu mapē `daq/`, tādējādi uzņemumu vēlāk var pārstrādāt bez sākotnējā ieraksta.
* **Ieraksti no `chloros_scripts` ierakstītājiem (1.3.0).**`.daq`, ko izveidojuši publiskie [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) reģistratori, tiek norādīts kā DAQ modelis, kas to izveidojis, un tā ierobežojumu var iestatīt**Projekta iestatījumi → DAQ gaismas sensors** tāpat kā jebkuram citam ierakstam. Šie ierakstītāji ieraksta neapstrādātus sensora skaitījumus; Chloros iegūst sensora rūpnīcas kalibrāciju pēc sērijas numura un to piemēro.
* **Kam pieder šis rādījums (1.3.0).**Blakus katram ierakstam `.daq` ir norādīts `.daq.cameras.json`, kas nosauc kameru(-as), kuras kadrus šis rādījums apraksta, un apstrāde to saskaņo**tikai ar attēliem no šīm kamerām**. Tādējādi atsevišķa kamera bez sava gaismas sensora vairs neizmanto citas kameras rādījumu — tai ir jāpiesaista sensors vai jāreģistrē savs. Ieraksts, kas veikts ar pašu DAQ (GUI Record, `daq pool-record`), un jebkurš `.daq` bez šī faila (ieskaitot vecākus ierakstus), joprojām darbojas ar visām kamerām.

## Starojuma intensitātes atgūšana

Projekta apstrāde eksportē arī visus tajā esošos gaismas sensoru ierakstus uz
`Light Sensor/` mapi blakus attēlu produktiem. Tam **nav** nepieciešami attēli:
atsevišķi ierakstīts gaismas sensora ieraksts ir pilnīgs ieraksts, un mape, kurā atrodas tikai `.daq`
faili, ir derīga ievade. Projekts, kurā ir tikai gaismas sensoru ieraksti, tos uzskaita un
apstrādā gan lietotnē, gan CLI, un tie paliek sarakstā arī pēc apstrādes. Apstrādes
ziņojumā tiek norādīts, cik daudz gaismas sensoru produktu tika izveidoti.

| Produkts | Kas tas ir |
| --- | --- |
| `<name>_calibrated.daq` | Atkārtoti apstrādājams arhīvs, kas atbilst tai pašai shēmai kā reāllaika ieraksts, tagad norādot kalibrēšanas paketi, ar kuras palīdzību tas tika izveidots. Tā atkārtota importēšana to **nekalibrē** otrreiz. |
| `<name>_calibrated.csv` | Spektrālā starojuma intensitāte W/m²/nm sensora paša viļņu garuma tīklā, viena rinda katram mērījumam, kā arī fotometriskās kolonnas: kopējā jauda, fotopiskais un skotopiskais lukss, PPFD ar sadalījumu pa zilo, zaļo un sarkano krāsu, un maksimālā viļņu garuma vērtība. |

DAQ-U vai DAQ-M, kura kalibrēšanas pakete nevar tikt lejupielādēta — jūs esat bezsaistē vai
šim sensoram nav kalibrēšanas faila — tiek **izlaists ar iemesla norādīšanu**, nekad netiek saglabāts
kā „kalibrēts” fails, kas satur neapstrādātus skaitļus. Izveidojiet savienojumu ar internetu un palaidiet atkārtoti. DAQ-E
uzglabā savu kalibrēšanu, tāpēc tā ir nepieciešama tikai tad, ja ierīce nav pieslēgta un
vietējā cache nav saglabāts nekas.

### DAQ-A: neapstrādāti skaitļi un kāpēc tas ir pareizais risinājums

**DAQ-A** ir izstrādāts pirms sērijas kalibrēšanas pakotņu sistēmas ieviešanas un tam nav pakotnes, ko
lejupielādēt. Tas nav pārskatīšanās: DAQ-A tiek kalibrēts uz vietas, izmantojot
atstarošanas mērķi, un mērķa balstītai kalibrēšanai nepieciešama tikai sensora *relatīvā*
reakcija — kas ir tieši tas, ko atspoguļo tā neapstrādātie skaitļi. Chloros šodien veic kalibrēšanu, izmantojot šos datus.

Tātad DAQ-A ieraksts tiek eksportēts, taču ar citu nosaukumu:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, nevis `_calibrated` — tas ir atšķirīgs faila nosaukums, nevis atzīme faila iekšienē,
jo šai informācijai ir jāiztur faila nosūtīšana pa e-pastu kā vienkāršam nosaukumam. `.csv`
galvenē ir norādīts `raw spectral sensor counts (NOT irradiance)` un brīdinājums, ka vērtības ir
salīdzināmas **vienā** failā, nevis starp dažādiem sensoriem. Kolonnas, kurām ir nozīme tikai
attiecībā uz reālo starojuma intensitāti — kopējā jauda, lukss, PPFD — tiek atstātas tukšas, nevis
aprēķinātas no skaitļiem.

Vecākie DAQ-A-SD ieraksti (shēma v1.01 / v1.02) reģistrē tikai faila ierakstīšanas laiku, nevis
laika zīmogu katram nolasījumam. Chloros nesalīdzinās attēlus ar šiem datiem — kadra sasaistīšana ar
ierakstīšanas laiku būtu nepareiza, lai gan vizuāli nekas neliecinātu par kļūdu —, taču eksportētais fails tos nolasī bez problēmām, un
CSV failā ir norādīts, uz kura pulksteņa tas balstās.

Pilnīgu informāciju par atstarošanas mērījumiem — ar vienu sensoru kopā ar kameru un diviem sensoriem (apkārtējā vide/objekts) — skatiet [Atstarošanas darba plūsmas](reflectance.md).
