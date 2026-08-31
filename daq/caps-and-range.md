# Vāciņu profili un kalibrētais diapazons

> Informācija par pašiem vāciņiem — kurš vāciņš tiek piegādāts kopā ar kuru sensoru, kā tie tiek uzstādīti un kādas ir to optiskās īpašības — ir aprakstīta **[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq)**. Šajā lapā ir aprakstīts, kā *norādīt* uzstādīto vāciņu Chloros, kas nodrošina pareizu korekciju.

Katram DAQ gaismas sensora rūpnīcas radiometriskajai kalibrācijai ir raksturīgs *tīrais* sensors. Fiziskais vāciņš, kas uzstādīts virs difuzora, maina to, kādu gaismu sensors uztver, tāpēc Chloros piemēro rūpnīcā izmērītu **vāciņa korekcijas profilu** virs kalibrācijas kopuma. Pareizā vāciņa ir būtiska daļa no kalibrēto datu iegūšanas — šajā lapā ir aprakstīts, kādi vāciņi ir pieejami katram modelim, kā tos deklarēt un kāds faktiski ir sensora kalibrētais spektrālais diapazons.

## Vāciņu pieejamība atkarībā no modeļa

| Vāciņas profils (`cap_id`) | Fiziskais vāciņš | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | Vāciņš ar saules gaismas kosinusa korektoru (**noklusējuma iestatījums visiem modeļiem**) | Jā | Jā | Jā |
| `fov_15` / `fov_45` / `fov_90` | Redzes lauka ierobežojošie konusi (15° / 45° / 90°) | Jā | — | Jā |
| `fov_30` / `fov_60` | Redzes lauka ierobežojošie konusi (30° / 60°) | Jā | — | — |
| `none` | Bez uzstādīta vāka | — | — | Jā |

Piezīmes par konkrētiem modeļiem:

* **DAQ-M ir viens vāka profils: `sunshine_cosine`.** „Bare-plus-Sunshine-cap” ir tā produkta definīcija, un DAQ-M bez vāciņa nav nepieciešams ģeometrijas profils.
* **DAQ-U bez vāciņa ir pilnīgi bez vāciņa** — tam vispār nav nepieciešams ģeometrijas profils, tāpēc tam nepastāv `none` profils.
* **`none` uz DAQ-E NAV bezdarbības profils.** DAQ-E iebūvētajam, ar stiklu pārklātajam difuzoram ir sava reālā ģeometrijas korekcija, tāpēc „bez vāciņa” šajā modelī pats par sevi ir izmērīts profils.
* **Neapklāts DAQ-E nevar mērīt tiešos saules starus nevienā augstumā** — „Sunshine cap” ir konfigurācija darbam laukā. Neplānojiet darbu ārpus telpām, izmantojot neapklātu DAQ-E.

GUI iestatījumos katram sensoram atsevišķi (zobrata ikona cilnē „Light Sensors”) izvēlnē **Cap** DAQ-U un DAQ-M modeļiem ir pieejama arī opcija „None (bare sensor)” — šajos divos modeļos „bare” vienkārši nozīmē, ka netiek piemērota vāciņa korekcija, kā norādīts iepriekš. Izvēlieties to tikai tad, ja vāciņš ir fiziski noņemts.

## Vāciņa deklarēšana — un kāpēc tas ir svarīgi

**Deklarētais `cap_id` jāatbilst vāciņam, kas fiziski atrodas uz sensora.** Ne sensors, ne programmatūra nespēj noteikt, vai vāciņš ir uzlikts. Deklarēšana nosaka divas lietas:

1. **Reāllaika korekciju**, kas tiek piemērota katram spektram.
2. **Vāciņa zīmogu, kas tiek ierakstīts katrā `.daq` ierakstā**, uz kuru paļaujas turpmākā atstarojuma apstrāde.

„Sunshine” vāciņš pēc konstrukcijas vājinātu signālu aptuveni **12 reizes**, tādēļ ierakstīšana ar nepareizi deklarētu vāciņu izkropļo spektru mērogu aptuveni par šo koeficientu. Vāciņa jāmaina nekavējoties.

### Vāciņa iestatīšana

GUI: cilne „Light Sensors” (Gaismas sensori) → zobrata ikona sensora rindā → nolaižamais izvēlnes elements **Cap** (Vāciņš). Noklusējuma iestatījums visiem modeļiem ir `sunshine_cosine` (visiem DAQ sensoriem tiek piegādāti ar uzstādītu kosinusa korektoru), un šī izvēle saglabājas visā projekta laikā.

<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->

CLI (jābūt palaistam backendam):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI sintaktiski atbalsta pilno `cap_id` sarakstu (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); katrs profils tiek validēts pret sensora modeli savienošanās brīdī, tādēļ nepieejams vāka identifikators (piemēram, tikai E identifikators uz DAQ-U) izraisa skaidru kļūdu, nevis nepareizu korekciju. Aizmugurējās sistēmas noklusējums, ja nekas netiek nodots, ir `sunshine_cosine`.

Python SDK piezīme: `cap_id` **nav** SDK vadības poga — `connect_daq_sensor()` / `DAQSensorSession` neizpauž nekādus vāciņa parametrus. Izvēlieties ierobežojumu, izmantojot iepriekš minētās CLI komandas vai GUI nolaižamo izvēlni; skatiet [SDK atsauci](../reference/sdk-reference.md).

Papildu informācija: profili tiek piegādāti Chloros instalācijas ietvaros, kas atrodas `daq/cap_profiles/<u|m|e>/<cap_id>.json`, un tos var pārrakstīt katram lietotājam atsevišķi `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`.

Neatkarīgi no ierobežojumiem sensoriem, kas nekad nav bijuši pārkalibrēti, automātiski tiek piemērots neliels, no flotes datiem atvasināts tumšā nobīdes precizējums — bez lietotāja iejaukšanās.

## Saules gaismas ierobežotāja darbība (konfigurācija ārpus telpām)

Skaitļi, uz kuriem var balstīt procedūras:

| Īpašība | Vērtība |
| --- | --- |
| Redzes lauks | 180° puslodes veida |
| Kosinusa reakcijas kļūda | ≤ ±4 % līdz 60° krituma leņķim; ≤ ±4,5 % līdz 70° |
| Zemās saules robeža | Nav ieteicams zem ~15° saules augstuma |
| Vājināšanās | ~12× (pēc konstrukcijas) |
| Vāciņa atkārtotas uzstādīšanas atkārtojamība | ≈ 1,5 % |
| Kvantitatīvais starojuma intensitātes rādītājs | Vidējie **≥ 15 s** mērījumi (ierīces raksturlielums, nevis defekts) |

Jebkuram kvantitatīvam starojuma intensitātes rādītājam — ieskaitot atstarojuma atsauces — izmantojiet vismaz 15 sekunžu mērījumu vidējo vērtību, nevis atsevišķu kadru.

## Kalibrētais spektrālais diapazons

| Īpašība | Vērtība |
| --- | --- |
| Spektrālā paraugu ņemšana | 340–1010 nm ar 5 nm soli (135 punkti) |
| Radiometriski kalibrētais diapazons | **~374–974 nm** (noteikts programmatūrā) |

Sensors reģistrē pilnu 340–1010 nm režģi, taču NIST standartam atbilstošais radiometriskais pastiprinājums aptver ~374–974 nm. Chloros **noraida absolūtās atstarošanas sadalījumu** jebkuram kameras diapazonam, kura spektrālā svara mazāk nekā puse atrodas šajā diapazonā, ziņojot izlaišanas iemeslu `dls-uncalibrated-band-<nm>`, nevis ģenerējot nekalibrētu rezultātu. No piegādājamajiem kameru modeļiem tikai F988 filtrs atrodas ārpus šī diapazona; tā vietā tas izmanto atstarošanas paneļa darba plūsmu — skatiet [Atstarošanas darba plūsmas](reflectance.md).

Informāciju par sensoru modeļiem, transportiem un sensoru ID skatiet [DAQ pārskatā](README.md). Informāciju par to, kā apstrādes laikā tiek izmantots ierobežojuma zīmogs, skatiet sadaļā [Ierakstīšana un .daq formāts](recording.md).
