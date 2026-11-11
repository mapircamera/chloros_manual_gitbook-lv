# Mono kameras un veģetācijas indeksi

## Viena kamera = viena josla

**M3M**kamera ir Bayer**M3C** mono versija: monohroms IMX265 sensors aiz viena šaurjoslas interferences filtra. Modeļa nosaukumā ir norādīts spektrālais joslas nosaukums — `M3M-<lens>-F<wavelength>`, piemēram, `M3M-L87-F685` (Chloros programmā tiek parādīts kā `LATT-M3M-L87-F685`). Sensors nodrošina **vienu pelēktoņu joslu** bez Bayer mozaīkas: nav nekas, ko demosaicēt, nav kanālu savstarpējās pārklāšanās, ko atdalīt, un nav jāiestata balansa iestatījumi.

Sekas, kas jāzina, pirms plānojat mono sistēmu:

* **Starojums un atstarojums ir pilnībā definēti katram joslas kanālam.**Tās ir radiometriskas kartes katram joslas kanālam, tādējādi viena M3M kamera rada kalibrētu float32 starojuma intensitāti (W/m²/sr/nm) un uint16 atstarošanas koeficientu (`32768` = ρ 1,0) tieši tāpat kā M3C josla. Mono kadriem ir**identitātes** sensora reakcijas matrica — 3×3 atdalīšana nav nepieciešama un netiek piemērota.
* **Viena mono kamera nevar ģenerēt veģetācijas indeksu.** NDVI, NDRE un citiem indeksiem ir nepieciešami vismaz divi spektra joslas. Lai aprēķinātu indeksus, izmantojot mono aparatūru, ir jāapvieno vairākas M3M kameras — skatīt zemāk.
* M3M kameras pārraida **Mono12** (12 biti: 2 baiti/pikselis pārraides laikā vai 1,5 baiti/pikselis, ja matrica izmanto [saspiestu 12 bitu pārraidi](arrays.md#packed-12-bit-transport)), kas ir svarīgi [matricas joslas platuma plānošanai](arrays.md#bandwidth-how-many-cameras-fit).

## Ko Chloros izlaiž mono režīmā — un kā tas par to informē

Krāsu apstrādes posmi vienkārši neattiecas uz vienjoslas sensoru. Chloros **tos izlaiž, parādot vienrindas ziņojumu**, nevis izraisot kļūdu, un tajā pašā sesijā tos joprojām izpilda kā parasti jebkurai M3C (Bayer) kamerai:

| Posms | Mono (M3M) darbība | M3C darbība |
| --- | --- | --- |
| Demosaic / debayer | Izlaists — `debayered` eksporta līmenis ir 1-kanālu pelēktoņu attēls. | 3-kanālu demosaic. |
| Baltā balanss (`lattice white-balance`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Krāsu profils (`lattice color-profile`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Saturācija/kontrasts (`lattice color`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Spektrālā pārklāšanās atdalīšana | Identitāte (bez 3×3 matricas). | Piemērota 3×3 matrica katrai kamerai. |
| Starojums / atstarošanās | **Darbojas** — katram diapazonam, pilnībā kalibrēts. | Darbojas katram diapazonam. |

GUI piemēro to pašu filtrēšanu: mono kamerai katras kameras iestatījumu panelī tiek paslēptas tikai RGB rindas (balansa, gamma, krāsu profils, piesātinājums, kontrasts, kanālu sadalījums), un reāllaika histogramma ir fiksēta uz vienu **MONO** līkni. Diskriminators visā slāņu kopā ir `M3M` simbols modeļa virknē, kas GUI/SDK vidē parādās kā `is_mono`.

## Indeksiem nepieciešami ≥ 2 kanāli: saskaņošana → slāņu kopa → indekss

Mono indeksa darba plūsma vienmēr sastāv no trim vienādiem soļiem:

1. **Saskaņošana** — vērst vairākas M3M kameras uz dažādiem viļņu garumiem (piemēram, F650 „Red” un F850 „NIR”), savienot tās kā [daudzkameru masīvu](arrays.md) un ļaujiet Chloros aprēķināt kameru savstarpējo reģistrācijas deformāciju.
2. **Sakārtošana** — saskaņotie kadri kļūst par vienu daudzjoslu attēlu (katra kamera veido vienu nosauktu joslu).
3. **Indekss** — aprēķina indeksa formulu attēlu kopas joslām, pēc izvēles renderējot to ar LUT palīdzību.

GUI vidē visa šī ķēde ir **Kombinētās kameras**masīva attēlošanas režīms: reāllaika kompozīcija jau ir saskaņota, un masīva indeksa kalkulators (zemāk) nosaka formulu, ko tas renderē. Iegūtos eksportus var deformēt, lai panāktu to pašu saskaņojumu, izmantojot uzņemšanas opciju**Aligned**.

## Indeksa kalkulators

Indeksa kalkulators veido indeksa izteiksmi, ko izmanto reāllaika skatā un indeksa eksportos katrai kamerai atsevišķi. Tas ir viens kopīgs logs, ko var atvērt no divām vietām cilnē „Cameras” (Kameras) sānu joslā:

* **Katrai kamerai atsevišķi**— Reāllaika priekšskatījums →**Indekss** (tikai RGN/OCN/NGB Bayer kamerām; atsevišķai mono kamerai nav indeksa vadības, jo viena josla nevar veidot indeksu).
* **Katram masīvam**— masīva iestatījumi → Reāllaika priekšskatījums →**Indekss**ratiņš. Šis ir mono ceļš: joslu saraksts aptver**visas masīvā iekļautās kameras**, tādējādi mono pāris šeit iegulda savas divas joslas.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Tā vadības elementi, no augšas uz leju:

* **Joslu mikroshēmas** („Joslas — noklikšķiniet, lai pievienotu izteiksmei”) — viena poga katrai pieejamai joslai, ar krāsas nosaukumu + viļņa garumu nm (atkārtoti krāsu nosaukumi tiek nošķirti, piemēram, „Krāsa 850”). Noklikšķinot, joslas simbols tiek ievietots kursora vietā. Joslas no kamerām, kas nevar ģenerēt starojuma intensitāti pa atsevišķām joslām (RGB/FRGB), tiek atfiltrētas.
* **Operatoru un funkciju pogas** — `+ - * / ( ) ^ ,` un `abs() sqrt() log() log10() exp() min() max() pow()`.
* **Izteiksmes teksta lauks** — brīvi ievadāma formula; vietas turētājs parāda klasisko NDVI formu `(NIR - Red) / (NIR + Red)`. Virs tā esošais tikai lasāmais, simbolos sadalītais priekšskatījums attēlo joslu čipus, skaitļus un karodziņus kā nezināmus simbolus.
* **Derīguma josla**— pelēka „Tukša — netiks piemērots nekāds indekss”; zaļa „Derīgs izteiksmes teksts”; sarkana ar konkrētu sintakses kļūdu (nezināms spektra joslas nosaukums, neskaidra spektra josla, ko uztver vairākas kameras, trūkstošas iekavas, …); vai dzeltens, ja izteiksme ir derīga, bet**konstante** (piem., `X/X` vai NDVI saucējs, kas ievadīts kā `−`, nevis `+`) — konstante attēlo visu kadru vienā krāsā.
* Atsevišķs dzintara krāsas brīdinājums parādās, ja piemērotais izteiksmes ir pareizs, bet **reāllaika kadrs ir vienveidīgs** (plakana vai piesātināta aina) — tiek automātiski atklāta histogrammas sabrukšana.
* **Piemērot LUT**(noklusējumā ieslēgts; izslēgts = pelēkto toņu izstiepšana),**Līmenis**2/3/5/7 pakāpes (noklusējumā 7 pakāpes) un**Min / Maks**ievades lauki, kas atrodas abpus gradienta joslai. Min noklusējuma vērtība ir**0,2**— tā palielina krāsu pāreju uz veģetācijai atbilstošo diapazonu, savukārt vērtības zem šīs robežas tiek attēlotas kā pelēktoņu skala; iestatiet Min uz −1, lai iegūtu pilnu indeksa diapazonu (poga**Reset** atjauno diapazonu no −1 līdz +1). Max noklusējuma vērtība ir 1.
* **Reāllaika histogramma** indeksa sadalījumam — kvadrātsaknes mērogā skalētas joslas, dzintara krāsas p2/p98 percentilu līnijas, balta mediānas līnija un ārpus diapazona galos esošie rādījumi („◀ N% < lo” / „hi < N% ▶”), kas virs 1 % kļūst dzintara krāsā, norādot, ka jāpaplašina Min/Max logs.
* **Piemērot** piemēro izteiksmi reāllaika plūsmā; LUT pielāgojumi tiek piemēroti reāllaikā, nespiežot pogu „Piemērot”. Ja projekts ir atvērts, izteiksmes un LUT iestatījumi tiek saglabāti kopā ar projektu un atjaunoti, kad to atverat no jauna; ja projekts nav atvērts, tie saglabājas tikai sesijas laikā.

<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->



## CLI ceļš

Tā pati „izlīdzināšana → sakārtošana → indeksēšana” ķēde, pilnībā skriptējama:

```bash
chloros-cli lattice align-calibrate --serials SN_RED,SN_NIR --save align.json
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`align-calibrate` izmanto savienotu masīvu, kurā atrodas tā kameras, vai arī pats tās atver vienam izpildes ciklam. `index --live` vienmēr pats atver kameras ([pagaidu masīvs](../reference/cli-reference.md#multi-camera-transient-sessions)) un tam nav `--serials`: tas atver visas kameras tīklā. Tāpēc atbrīvojiet visas kameras, kuras pirms tam turēja Chloros — GUI, `cam-connect`, `array-connect` (`chloros-cli lattice array-disconnect --all`): ja aizturētu kameru atverat otro reizi, tas var pārtraukt tās turētāja straumi vai izraisīt aizmugurējās sistēmas kļūdu. Kamera, kas nepiegādā nevienu kadru, izraisa izpildes kļūdu, un netiek ierakstīts ne skripts, ne indekss.

`--channel` saista iestatījuma simbolus ar skriptā norādītajiem joslu nosaukumiem. Divi noteikumi palīdz izvairīties no neveiksmīgas darbības:

* **Simboliem tiek ņemta vērā lielā un mazā burta atšķirība**, un tiem precīzi jāsakrīt ar iestatījuma kanālu nosaukumiem — iestatījumos tiek izmantoti mazie burti (piemēram, NDVI iestatījumi ir `red`, `nir`; skatiet `--list-presets`). `--channel red=Red_660` darbojas; `--channel RED=660` neizdodas, parādot kļūdu `channel_map missing entries`.
* Band side jānorāda josla saskaņotajā kopā (`lattice align-info --profile align.json` tās uzskaita). Bezsaistes režīms pieņem arī no 0 sākotnējus joslu indeksus, piemēram, `--channel red=0 --channel nir=1`.

`lattice index` darbojas arī bezsaistē ar saglabātu, saskaņotu daudzjoslu TIFF failu. Katra forma darbojas aizmugurē, tāpēc tai jābūt palaistai un jābūt pieteiktai maksas „Chloros+” līmenī; relatīvais `--input` vai `--output` tiek atrisināts attiecībā pret mapi, no kuras palaidāt komandu:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Indeksa iestatījumi

`lattice index --preset` (un cilnes „Attēls” [Indeksa/LUT smilšu kaste](../image-viewer-gui/index-lut-sandbox.md), kas izmanto to pašu dzinēju) piedāvā šos **22 iestatījumus**:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Izvadiet `chloros-cli lattice index --list-presets`, lai apskatītu katras iestatījuma formulas un kanālu simbolus, un `--list-gradients`, lai apskatītu pieejamos krāsu gradientus. Pielāgotās formulas izmanto `--formula EXPR` ar tādu pašu sintaksi kā indeksa kalkulators. Ņemiet vērā, ka šis iestatījumu saraksts attiecas konkrēti uz LATTICE indeksa dzinēju — izvēlne „Apstrāde” (Processing) projekta iestatījumos importētiem attēliem ir atšķirīgs saraksts (skatiet [Multispektrālo indeksu formulas](../project-settings/multispectral-index-formulas.md)).

Pilnīgais karogu kopums (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, `--live` izlīdzināšanas deformācijas slēdži un citi) ir aprakstīts [CLI atsauces sadaļā § Indeksu / veģetācijas matemātika](../reference/cli-reference.md#index--vegetation-maths); SDK ekvivalenti atrodami [SDK atsauces materiālā](../reference/sdk-reference.md).

## Indeksa produktu iegūšana no mono masīva

Pēc masīva pieslēgšanas un indeksa izteiksmes piemērošanas `array-capture` (vai GUI **Capture All**) saglabā eksporta līmeņus katrai kamerai *un* indeksa renderējumu — `--index`/`--no-index` to ieslēdz vai izslēdz CLI vidē, un saglabāšana pēc noklusējuma ietver visus piemērojamos līmeņus. Mono kameras ieguldījums katrā ierakstīšanas grupā ir tās viena josla neapstrādātā/debayered (pelēktoņu)/starojuma/atstarošanas līmeņos, kā arī kopīgais kombinētā indeksa kompozīts, ja masīvs darbojas kombinētajā režīmā. Skatīt [Daudzkameru masīvi § Uztveršana](arrays.md#capturing-monitoring-vs-analysis).
