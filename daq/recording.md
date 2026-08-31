# Ierakstīšana un .daq formāts

`.daq` fails ir Chloros gaismas sensora ierakstīšanas formāts: **SQLite datu bāze**, kas satur kalibrētus spektrālos kadrus no viena DAQ sensora. Ierakstiet vienu failu uzņemšanas sesijas laikā, un atstarojuma apstrādes sistēma vēlāk varēs katru attēlu dalīt ar lejupvērsto starojuma intensitāti, kas izmērīta tieši tajā brīdī.

## Kas ir iekļauts .daq failā

| Īpašība | Vērtība |
| --- | --- |
| Konteiners | SQLite datubāze, viens fails katram sensoram katrai ierakstīšanai |
| Faila nosaukums | Ietver **sensora ID**un**laika zīmogu**, piemēram, `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Spektrs katrā kadrā | 135 punkti, 340–1010 nm ar 5 nm soli, kā arī CIE XYZ trīsstimulācijas vērtības |
| Vienības | Kalibrēts spektrālais starojuma blīvums, **W/m²/nm** (piemērots rūpnīcas kalibrēšanas komplekts + vāciņa profils) |
| Ierakstītie metadati | Sensora ID (atslēga, lai iegūtu šīs vienības rūpnīcas kalibrāciju) un spēkā esošais vāciņa profils — skatīt [Vāciņu profili un kalibrētais diapazons](caps-and-range.md) |

Formāts ir identisks visiem DAQ-U, DAQ-M un DAQ-E modeļiem, tādēļ turpmākajai apstrādei nav nozīmes, kurš pārraides modulis to ir reģistrējis.

Kalibrētai reģistrēšanai ir nepieciešams sensora rūpnīcas kalibrēšanas komplekts. DAQ-U un DAQ-M gadījumā aizmugures sistēma lejupielādē komplektu no MAPIR mākonis, izmantojot sensora ID (ja tas nav iespējams, ierakstīšana tiek noraidīta); DAQ-E vienības ir izņēmums, jo tās glabā kalibrēšanas datus pašā ierīcē.

## Ierakstīšana no grafiskās lietotāja saskarnes

Ierakstīšanai GUI ir nepieciešams **atvērts projekts** (pretējā gadījumā pogas „Ierakstīt” ir atspējotas):

* **Ierakstīt visu / Pārtraukt visu** — gaismas sensoru sānu joslas augšdaļā; vienlaikus sāk vai pārtrauc `.daq` ierakstīšanu visos pieslēgtajos sensoros.
* **Ierakstīt / Pārtraukt ierakstīšanu** — katram sensoram atsevišķi, rīka iestatījumu logā. Ierakstīšanas laikā sensora reāllaika informācijas rindās parādās sarkans „REC” indikators.

Faili tiek saglabāti `<project>/light_sensor/`, un, kad ieraksts tiek apturēts — izmantojot „Stop”, „Stop All” vai atvienojot ieraksta sensoru — pabeigtais `.daq` tiek **automātiski pievienots atvērtam projektam**. Tas parādās projekta failu sarakstā bez manuālas pievienošanas, jau gatavs atstarošanas apstrādei.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->

## Ierakstīšana no CLI

CLI veic ierakstīšanu, izmantojot backend sensoru kopu (backendam jābūt darbojošam — šīs komandas ir vieglie HTTP klienti):

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

Iegūst `--sensor-id` vērtību no `chloros-cli daq pool-list`. Divas svarīgas noklusējuma vērtības:

| Opcija | Noklusējums |
| --- | --- |
| `--duration` | `0` — ierakstīt līdz `pool-record --stop` |
| `--output` / `-o` | `~/Documents/DAQ Live View/` **backend** failu sistēmā, nevis CLI failu sistēmā |

Atšķirība starp izvades direktorijiem ir svarīga, ja CLI ir vērsts uz backend citā datorā: fails nonāk tur, kur darbojas backend.

## Ierakstīšana no Python

`DAQSensorSession` (ko atgriež `chloros_sdk.connect_daq_sensor()`) atklāj to pašu apvienoto ierakstu: `record_start(output_dir=None, device_name=None)` atgriež faila ceļu, `record_stop()` atgriež `{path, rows}`. Pilnu sesiju API skatiet [SDK atsauces materiālā](../reference/sdk-reference.md). SDK tiešās aparatūras klases (tikai darbvirsmas instalācijās) pēc noklusējuma ierakstus raksta `~/Documents/DAQ/`; izlaistajām versijām tiek atbalstīts iepriekš minētais kopīgais ceļš.

## .daq faila izmantošana apstrādes laikā

Lai no attēliem iegūtu atstarojumu, Chloros ir nepieciešams katrai ekspozīcijai atbilstošs lejupvērstais starojuma blīvums:

* **Saglabājiet `.daq` kopā ar attēliem.**Apstrādes laikā sistēma automātiski nosaka**laika zīmogam atbilstošo lejupvērsto starojuma intensitāti** no ierakstītā `.daq` (jebkura DAQ modeļa) — vai no DAQ-M nativā `.csv` — kas atrodas kopā ar attēliem. GUI ieraksti šo nosacījumu izpilda automātiski, jo tie tiek pievienoti projektam brīdī, kad tiek apturēti.
* **Kalibrēšana tiek lejupielādēta pēc pieprasījuma.** Ja kameras vai DAQ rūpnīcas kalibrēšanas pakete vēl nav saglabāta vietējā kešatmiņā, Chloros to automātiski lejupielādē no MAPIR mākoņa pirmajā lietošanas reizē (vienreiz nepieciešams interneta savienojums; saglabāts kešatmiņā zem `~/.chloros/`).
* **Reāllaika uzņēmumi rada savu papildu failu.** Jebkuram reāllaikā uzņemtam atstarošanas kadram faktiski izmantotais DAQ rādījums tiek saglabāts kā `.daq` papildu fails blakus attēlam, lai uzņēmumu vēlāk varētu pārstrādāt bez sākotnējā ieraksta.

## Starojuma intensitātes atgūšana

Projekta apstrāde eksportē arī visus tajā esošos gaismas sensora ierakstus uz
`Light Sensor/` mapi blakus attēlu produktiem. Tam **nav** nepieciešami attēli:
atsevišķi lidojošs gaismas sensors veido pilnīgu ierakstu, un mape, kurā atrodas tikai `.daq`
faili, ir derīgs ievades avots. Apstrādes ziņojumā tiek norādīts, cik daudz gaismas sensora produktu tika saglabāti.

| Produkts | Kas tas ir |
| --- | --- |
| `<name>_calibrated.daq` | Pārstrādājams arhīvs ar tādu pašu shēmu kā reāllaika ieraksts, kurā tagad ir norādīts kalibrēšanas komplekts, ar kura palīdzību tas tika izveidots. Tā atkārtota importēšana to **nekalibrē** otrreiz. |
| `<name>_calibrated.csv` | Spektrālā starojuma intensitāte W/m²/nm uz paša sensora viļņu garuma režģa, viena rinda katram nolasījumam, kā arī fotometriskās kolonnas: kopējā jauda, fotopiskais un skotopiskais lukss, PPFD ar sadalījumu pa zilo, zaļo un sarkano krāsu, un maksimālā viļņu garuma vērtība. |

DAQ-U vai DAQ-M, kura kalibrēšanas pakete nevar tikt iegūta — jūs esat bezsaistē vai
šim sensoram nav kalibrēšanas datu failā — tiek **izlaists ar iemesla norādīšanu**, nekad netiek saglabāts
kā „kalibrēts” fails, kurā ir neapstrādāti skaitļi. Izveidojiet savienojumu ar internetu un palaidiet atkārtoti. DAQ-E
uzglabā savu kalibrēšanu, tāpēc tā ir nepieciešama tikai tad, ja ierīce nav pieslēgta un
vietējā cache nav saglabāts nekas.

### DAQ-A: neapstrādāti skaitļi un kāpēc tas ir pareizais risinājums

**DAQ-A** ir izstrādāts pirms sērijas kalibrēšanas pakotņu sistēmas ieviešanas un tam nav pakotnes, ko
lejupielādēt. Tas nav pārskatīšanās: DAQ-A tiek kalibrēts uz vietas, izmantojot
atstarošanas mērķi, un mērķa balstītai kalibrēšanai ir nepieciešama tikai sensora *relatīvā*
reakcija — kas ir tieši tas, ko veido tā neapstrādātie skaitļi. Chloros šodien veic kalibrēšanu, izmantojot tos.

Tātad DAQ-A ieraksts tiek eksportēts, taču ar citu nosaukumu:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, nevis `_calibrated` — tas ir atšķirīgs faila nosaukums, nevis atzīme faila iekšienē,
jo šai informācijai jāiztur faila nosūtīšana pa e-pastu kā vienkāršam nosaukumam. `.csv`
galvenē ir norādīts `raw spectral sensor counts (NOT irradiance)` un ir brīdinājums, ka vērtības ir
salīdzināmas **vienā** failā, nevis starp sensoriem. Kolonnas, kurām ir nozīme
tikai reālajam starojuma intensitātes rādītājam — kopējā jauda, lukss, PPFD — tiek atstātas tukšas, nevis
aprēķinātas no skaitļiem.

Vecākie DAQ-A-SD ieraksti (shēma v1.01 / v1.02) reģistrē tikai faila ierakstīšanas laiku, nevis
laika zīmogu katram nolasījumam. Chloros nesaskaņos attēlus ar šiem datiem — attēla kadra sasaistīšana ar
ierakstīšanas laiku būtu nepareiza, lai gan vizuāli nekas neliktos nepareizi —, taču eksportētais fails tos lasa bez problēmām, un
CSV norāda, uz kādu pulksteni tas attiecas.

Pilnīgu informāciju par atstarošanas procesu — ar vienu sensoru kopā ar kameru un diviem sensoriem (apkārtējās vides/objekta) — skatiet [Atstarošanas darba plūsmas](reflectance.md).
