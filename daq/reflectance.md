# Reflektances darba plūsmas

DAQ gaismas sensors pārvērš radiometriskos attēlus reflektancē. Ir divas atšķirīgas darba plūsmas:

1. **Viena sensora** — viens DAQ mēra uz leju vērsto starojuma intensitāti, kamēr kamera veic uzņemšanu, un Chloros sadala kameras starojuma intensitāti ar šo atsauces vērtību.
2. **Divu sensoru** — divi DAQ sensori, no kuriem viens novēro debesis, bet otrs — objektu, ģenerē reāllaika spektrālo atstarojuma līkni, neizmantojot kameru.

## Viens sensors + kamera (uz leju vērsta atsauces vērtība)

DAQ darbojas kā uz leju vērsta gaismas sensora (DLS) loma: kamera mēra augšupvērsto starojuma intensitāti **L**(W/m²/sr/nm), DAQ mēra lejupvērsto starojuma intensitāti**E** (W/m²/nm), un Chloros aprēķina atstarojumu katrā joslā pēc formulas:

> ρ = π · L / E

DAQ rādījums vienmēr ir **laika zīmogā saskaņots ar ekspozīciju** — tieši tāpēc DAQ un kamerām ir kopīgs PTP sinhronizēts pulkstenis (skatīt [DAQ-E tīkla savienojumi un laika sinhronizācija](ethernet-ptp.md)). Āra darbiem uzstādiet „Sunshine” kosinusa vāciņu un to pareizi deklarējiet; vāciņas deklarācija tieši mēro E (skatīt [Cap Profiles & Calibrated Range](caps-and-range.md)). Veicot kvantitatīvu darbu, ņemiet vērā instrumenta raksturlielumu: kvantitatīvais starojuma intensitātes rādītājs tiek aprēķināts, pamatojoties uz vismaz 15 sekunžu rādījumu vidējo vērtību.

### Reāllaika uzņemšana

Savienojiet DAQ ar kameru cilnē „Cameras”: katras kameras iestatījumu panelī ir nolaižamais izvēlnes elements **Light Sensor**, kurā ir uzskaitīti visi savienotie DAQ (DAQ-U/M/E) no cilnes „Light Sensors”; sinhronizētai matricai visai matricai piemērota „Light Sensor” izvēle tiek attiecināta uz katru elementu (atsevišķas kameras joprojām var pārrakstīt šo iestatījumu). Pēc savienošanas sensora spektri tiek ievadīti kameras DLS lauciņā, un eksportētie atstarošanas rādītāji tiek dalīti ar atbilstošo rādījumu. Masīvā elements ar savu sensoru izmanto savu rādījumu — savām joslām reāllaika kompozīcijā, eksportētajiem datiem un automātiskajai ekspozīcijai —, bet pārējie elementi izmanto masīva rādījumus.

<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->


Divas lietas, kas ir vērts zināt:

* **Nav piesaistīts DAQ → atstarojums tiek noraidīts, nevis simulēts.** Chloros noraida atstarojuma rezultātu un reģistrē izlaišanas iemeslu, nevis klusi atgriež zemāku rezultātu.
* **Izmantotais rādījums tiek saglabāts.** Katram atstarojuma kadram faktiski piemērotie DAQ rādījumi tiek ierakstīti kā `.daq` fails uzņemumu mapē `daq/`, lai uzņemumu vēlāk varētu pārstrādāt ([Ierakstīšana un .daq formāts](recording.md)). Blakus esošais `.daq.cameras.json` failā ir norādītas kameras, kuras tas apraksta, un atkārtotā apstrāde to saskaņo tikai ar šo kameru attēliem.

### Ierakstīto attēlu apstrāde

Lidojuma pēcapstrādei sesijas laikā ierakstiet `.daq` failu un saglabājiet to kopā ar attēliem — apstrādes ķēde automātiski atrisina laika zīmogu saskaņoto lejupvērsto starojumu, pirmajā lietošanas reizē lejupielādējot jebkādu trūkstošo rūpnīcas kalibrāciju no MAPIR mākoņa. GUI ieraksti tiek automātiski pievienoti atvērtam projektam, kad tie tiek apturēti.

Atspoguļojuma atsauci var izvēlēties apstrādes laikā — `--reflectance-source` uz `chloros-cli process` vai arī izmantojot atspoguļojuma avota iestatījumu GUI sadaļā „Projekta iestatījumi”:

| Vērtība | Darbība |
| --- | --- |
| `auto` (noklusējums) | Kvalitātes pārbaudi izturējis kalibrēšanas mērķis kadrā ir absolūtais etalons; DAQ lejupvērstā starojuma intensitāte (ρ = π·L/E) tiek izmantota kā rezerves variants. Mērķis, kas iztur QA ar atzīmi (izlaists panelis vai nedarbojas josla), netiek automātiski izvēlēts: tiek izmēģinātas mērķa, mērķa+DAQ un tikai DAQ ķēdes to novērtējuma secībā, kas iegūts uz izslēgtajiem paneļiem. |
| `daq` | DAQ ir noteicošais |
| `target` | Stingrs mērķis rāmī; bez DAQ aizstāšanas |

Skatīt [Kalibrēšanas mērķus](../calibration-targets.md), lai iepazītos ar mērķu apstrādes plūsmām, un [nodaļu „LATTICE”](../lattice/README.md), kā arī [CLI atsauci](../reference/cli-reference.md), lai iepazītos ar pilnu apstrādes procesu. Lasot eksportētos atstarošanas pikseļus, izmantojiet norādīto mērogu (LATTICE: 32768 = ρ 1,0, XMP `Chloros:PixelScale`; Survey3: 65535) — skatiet [Izvades attēlu formāti](../output-image-formats.md).

### Joslas ārpus DAQ kalibrētā diapazona

DAQ radiometriski kalibrētais diapazons ir ~374–974 nm. Chloros noraida uz DAQ balstītu atstarojumu jebkuram kameras diapazonam, kura spektrālā svara mazāk nekā puse atrodas šajā diapazonā, ziņojot izlaišanas iemeslu `dls-uncalibrated-band-<nm>`. No piegādātajiem modeļiem tas attiecas tikai uz F988: F988 atstarošanas koeficients tiek kalibrēts, izmantojot atstarošanas paneli uzņemšanas vietā; jo šis diapazons atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tāpēc Chloros izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem. Ja F988 kamera darbojas tikai DAQ režīmā, Chloros noraida uz DAQ balstīto atstarojumu šim diapazonam, norādot izlaišanas iemeslu `dls-uncalibrated-band-988` — atbalstītā darbības secība ir paneļa izmantošana.

## Divu sensoru konfigurācija (apkārtējā gaisma + objekts)

Divi DAQ sensori — jebkurš pāris, neatkarīgi no transporta — nodrošina reāllaika atstarošanas spektru bez kameras: viens sensors ir vērsts pret debesīm (**apkārtējās vides gaismas avots**), otrs — pret objektu (**objekta skeneris**), un Chloros aprēķina katram viļņa garumam:

> R(λ) = objekts(λ) / apkārtējā gaisma(λ)

(nulle, ja apkārtējā gaisma ≤ 0).

### Lietotāja saskarnē

Kad abi sensori ir pieslēgti cilnē „Gaismas sensori”, atveriet sensora pievienošanas logu (poga „+” uz diagrammas flīzes režģa skatā) un izvēlieties **Apvienot apkārtējo gaismu + objektu**. Izvēlieties abus sensorus nolaižamajos izvēlnēs „Vides gaismas avots” un „Objekta skeneris” un noklikšķiniet uz „Izveidot”. Grupa parādās kā atsevišķs grafiks un kā sānu joslas rinda ar zaļu**REF** zīmi.

<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->


Zem atstarojuma diagrammas (saraksta skatā) reāllaika **veģetācijas indeksu tabula** aprēķina indeksus no līknes, izmantojot spektrālo joslu centrus: zils 450 / zaļš 550 / sarkans 670 / NIR 800 nm. Vienmēr tiek parādīti uz attiecību balstīti indeksi, kas neņem vērā absolūto skalu (NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR), vienmēr tiek parādīti; indeksi, kuriem nepieciešama absolūtā atstarojamība (EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI), parādās tikai tad, ja abi sensori ir jaudas kalibrēti modeļi.

### Šķietamā pret relatīvo — marķēšanas noteikums

Chloros marķē divu sensoru izvadi atbilstoši tam, ko sensoru pāris faktiski var nodrošināt:

| Sensoru pāris | Marķējums |
| --- | --- |
| Abi sensori kalibrēti — ielādēts rūpnīcas kalibrēšanas komplekts | **Šķietamā atstarojamība** |
| Vismaz viens sensors nav kalibrēts | **Relatīvā atstarojamība** |

Visi trīs modeļi ir radiometriski: tiklīdz ir ielādēts sensora rūpnīcas kalibrēšanas komplekts, tā spektri ir absolūti W/m²/nm, tādējādi kalibrētu sensoru pāris nodrošina attiecību pret absolūto šķietamo atstarojamību — transporta režīms to nenosaka. Sensors, kas joprojām pārraida neapstrādātus skaitļus (komplekts nav pieejams), pazemina rezultātu līdz relatīvai līknei (spektrālā forma joprojām ir spēkā). Abiem sensoriem jābūt ar pareizi deklarētiem ierobežojumiem ([Ierobežojumu profili un kalibrētais diapazons](caps-and-range.md)).

### No Python


Apvienotajā SDK saskarnē nav atsevišķa divu sensoru izsaukuma: atveriet divas sesijas ar `chloros_sdk.connect_daq_sensor()` un paši salīdziniet to spektrus ar `latest()`, piemērojot to pašu marķēšanas konvenciju. (Divu sensoru reģistrēšanas rīks ir pieejams arī MAPIR iekšējā tiešās aparatūras saskarnē, kas pilnīguma labad ir uzskaitīts [CLI atsauces dokumentā](../reference/cli-reference.md) — tas nav daļa no piegādātās CLI; iepriekš minētā GUI darbplūsma ir atbalstītā reālā darbības secība.)
