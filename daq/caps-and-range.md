# Sensoru korpusu profili un kalibrētais diapazons

> Paši vāciņi — kurš vāciņš tiek piegādāts kopā ar kuru sensoru, kā tie tiek uzstādīti un kāda ir to optiskā uzvedība — ir aprakstīti **[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq)**. Šajā lapā ir aprakstīts, kā *paziņot* Chloros par uzstādīto vāciņu, kas nodrošina korekcijas pareizību.

Katram DAQ gaismas sensora rūpnīcas radiometriskajai kalibrācijai ir raksturīgs *tīrais* sensors. Fiziskais vāciņš, kas uzstādīts virs difuzora, maina to, kādu gaismu sensors uztver, tāpēc Chloros piemēro rūpnīcā izmērītu **vāciņa korekcijas profilu** virs kalibrācijas kopuma. Pareizā vāciņa deklarēšana ir daļa no kalibrēto datu iegūšanas — šajā lapā ir aprakstīts, kādi vāciņi ir pieejami katram modelim, kā tos deklarēt un kāds faktiski ir sensora kalibrētais spektrālais diapazons.

## Vāciņu pieejamība atkarībā no modeļa

| Vāciņas profils (`cap_id`) | Fiziskais vāciņš | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | Vāciņš ar saules gaismas kosinusa korektoru (**noklusējuma iestatījums visiem modeļiem**) | Jā | Jā | Jā |
| `fov_15` / `fov_45` / `fov_90` | Redzes lauka ierobežojošie konusi (15° / 45° / 90°) | Jā | — | Jā |
| `fov_30` / `fov_60` | Redzes lauka ierobežojošie konusi (30° / 60°) | Jā | — | — |
| `none` | Bez uzstādīta vāciņa | — | — | Jā |

Piezīmes par konkrētiem modeļiem:

* **DAQ-M ir viens vāciņa profils: `sunshine_cosine`.** „Bare-plus-Sunshine-cap” ir tā produkta definīcija, un „bare” DAQ-M modelim nav nepieciešams ģeometrijas profils.
* **„Bare” DAQ-U modelis ir patiesi „bare”** — tam vispār nav nepieciešams ģeometrijas profils, tāpēc tam nav pieejams `none` profils.
* **`none` uz DAQ-E NAV bezdarbības profils.** DAQ-E iebūvētajam, ar stiklu pārklātajam difuzoram ir sava reālā ģeometrijas korekcija, tāpēc „bez vāciņa” šajā modelī pats par sevi ir izmērīts profils.
* **Neapklāts DAQ-E nevar mērīt tiešos saules starus nevienā augstumā** — „Sunshine cap” ir konfigurācija darbam laukā. Neplānojiet darbu ārpus telpām, izmantojot neapklātu DAQ-E.

GUI iestatījumos katram sensoram atsevišķi (zobrata ikona cilnē „Light Sensors”) izvēlnē **Cap** DAQ-U un DAQ-M modeļiem ir pieejama arī opcija „None (bare sensor)” — šajos divos modeļos „bare” vienkārši nozīmē, ka netiek piemērota vāciņa korekcija, kā norādīts iepriekš. Izvēlieties to tikai tad, ja vāciņš ir fiziski noņemts.

## Vāciņa deklarēšana — un kāpēc tas ir svarīgi

**Deklarētais `cap_id` jāatbilst vāciņam, kas fiziski atrodas uz sensora.** Ne sensors, ne programmatūra nespēj noteikt, vai vāciņš ir uzlikts. Deklarēšana ietekmē divas lietas:

1. **Reāllaika korekciju**, kas tiek piemērota katram spektram.
2. **Vāciņa, kas tiek ierakstīta katrā `.daq` ierakstā**, uz kuru paļaujas turpmākā atstarošanas apstrāde.

„Sunshine” vāciņš **pēc konstrukcijas**vājināšanu aptuveni**12 reizes**, tādēļ ierakstīšana ar nepareizi deklarētu vāciņu spektrus mēro nepareizi aptuveni par šo koeficientu. Deklarējiet vāciņa izmaiņas nekavējoties.

### Vāciņa iestatīšana

GUI: cilne „Gaismas sensori” → rīkzīme sensora rindā → izvēlne **Vāciņš**. Noklusējuma vērtība visiem modeļiem ir `sunshine_cosine` — tas ir pieņēmums (DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez vāciņa, un ieraksts norāda, ka vāciņš tika pieņemts, kamēr jūs neizvēlējāties konkrētu), un izvēle saglabājas visā projektā.



CLI<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->
(backendam jābūt palaistam):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI sintaktiski pieņem pilno `cap_id` sarakstu (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); katrs profils tiek validēts pret sensora modeli savienošanās brīdī, tādēļ nepieejams vāciņa ID (piemēram, tikai E tipa ID uz DAQ-U) izraisa skaidru kļūdu, nevis nepareizu korekciju. Aizmugurējās sistēmas noklusējums, ja nekas netiek nodots, ir `sunshine_cosine`.

Python SDK: savienošanās brīdī nosauciet kapu ar `connect_daq_sensor(cap_id="sunshine_cosine")` vai mainiet to darbības laikā ar `DAQSensorSession.set_cap("fov_45")`; skatiet [SDK atsauci](../reference/sdk-reference.md#daq-sensor-sessions).

Papildu informācija: profili tiek piegādāti kopā ar Chloros instalāciju kā `daq/cap_profiles/<u|m|e>/<cap_id>.json`, un tos var pārrakstīt katram lietotājam atsevišķi, izmantojot `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`.

Neatkarīgi no ierobežojumiem sensori, kas nekad nav tikuši pārkalibrēti, automātiski saņem nelielu, no iekārtu parka iegūtu tumšā nobīdes precizējumu — bez lietotāja iejaukšanās.

## DAQ-E-S: fiksēts difuzors un slīpums

**DAQ-E-S** ir DAQ-E ar divām atšķirībām, kas ir svarīgas Chloros. Pārējās funkcijas (atklāšana, savienošana, PTP, ierakstīšana, ierīces iestatījumi) darbojas tieši tāpat kā DAQ-E gadījumā.

* **Tā difuzors ir pastāvīgs, tādēļ tam nav nepieciešama vāciņa korekcija.** Difuzors bija uz ierīces, kad tika veikta tās rūpnīcas kalibrēšana, tādējādi kalibrēšanas rezultāti jau atspoguļo patieso lejupvērsto starojuma intensitāti. Chloros tam nepiemēro nekādu maksimālā līmeņa profilu, neatkarīgi no pieprasījuma: maksimālā līmeņa profils, kas nosaukts šim difuzoram, tiek ignorēts, parādot ziņojumu, ierakstos norādīts, ka maksimālais līmenis ir iegūts no modeļa, un brīdinājums par pieņemto maksimālo līmeni šim difuzoram nekad neparādās. Sadaļā „Projekta iestatījumi” → „DAQ gaismas sensors” tā rindā nav redzams nekāds ierobežojums, un projekta līmeņa ierobežojuma pārrakstīšana to neietekmē. Neuzlieciet ierobežojumu tā datiem manuāli: „Sunshine” korekcija šajā gadījumā rādītu apmēram 11 reizes augstākus rādījumus.
* **Tam ir iebūvēts 3-asu akselerometrs.** Difuzora kosinusa reakcija ir spēkā tikai tad, ja tas atrodas horizontālā stāvoklī, tādēļ sasvērtā sensora rādījumi par starojuma intensitāti ir nepareizi, neizraisot citus simptomus. Tādēļ katrs DAQ-E-S rādījums ietver sensora slīpuma, sānsveres un pacelšanās leņķus, un no viena sensora reģistrētais `.daq` tos saglabā blakus katram spektram (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, …), tādējādi ļaujot atšķirt mākoņus no sagāztas montāžas.

Informāciju par katru orientācijas lauku un to, kā tiek ziņots par kļūdu orientācijas datu plūsmā, skatiet [„DAQ-E-S CLI atsauces rokasgrāmatā”](../reference/cli-reference.md#daq-e-s--fixed-diffuser-and-tilt-sensor).

## „Sunshine cap” veiktspēja (konfigurācija ārpus telpām)

Skaitļi, uz kuriem var balstīt procedūras:

| Īpašība | Vērtība |
| --- | --- |
| Redzes lauks | 180° puslodes veida |
| Kosinusa reakcijas kļūda | ≤ ±4 % līdz 60° krituma leņķim; ≤ ±4,5 % līdz 70° |
| Zemas saules robeža | Nav ieteicams zem ~15° saules augstuma |
| Vājināšanās | ~12× (pēc konstrukcijas) |
| Vāciņa atkārtotas uzstādīšanas atkārtojamība | ≈ 1,5 % |
| Kvantitatīvais starojuma intensitātes rādītājs | Vidējie **≥ 15 s** mērījumu rezultāti (ierīces raksturlielums, nevis defekts) |

Jebkuram kvantitatīvam starojuma intensitātes rādītājam — ieskaitot atstarojuma atsauces — izmantojiet vismaz 15 sekunžu mērījumu vidējo vērtību, nevis atsevišķu kadru.

## Kalibrētais spektrālais diapazons

| Īpašība | Vērtība |
| --- | --- |
| Spektrālā paraugu ņemšana | 340–1010 nm ar 5 nm soli (135 punkti) |
| Radiometriski kalibrētais diapazons | **~374–974 nm** (noteikts programmatūrā) |

Sensors reģistrē pilnu 340–1010 nm tīklu, taču NIST standartam atbilstošais radiometriskais pastiprinājums aptver ~374–974 nm. Chloros **nepieņem absolūtās atstarošanas sadalījumu** nevienai kameras joslai, kuras spektrālā svara mazāk nekā puse atrodas šajā diapazonā, ziņojot izlaišanas iemeslu `dls-uncalibrated-band-<nm>`, nevis ģenerējot nekalibrētu rezultātu. No piegādātajām kameru SKU tikai F988 filtrs atrodas ārpus šī diapazona; tā vietā tiek izmantota atstarošanas paneļa darba plūsma — skatiet [Atstarošanas darba plūsmas](reflectance.md).

Informāciju par sensoru modeļiem, transportiem un sensoru ID skatiet [DAQ pārskatā](README.md). Informāciju par to, kā apstrādes laikā tiek izmantots ierobežojuma zīmogs, skatiet [Ierakstīšana un .daq formāts](recording.md).
