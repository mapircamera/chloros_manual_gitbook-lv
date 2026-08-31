# Mono kameras un veģetācijas indeksi

## Viena kamera = viena spektrālā josla

**M3M**kamera ir Bayer**M3C**mono versija: monohroms IMX265 sensors aiz viena šaurjoslas interferences filtra. Modeļa nosaukumā ir norādīta josla — `M3M-<lens>-F<wavelength>`, piemēram, `M3M-L87-F685` (parādās kā Chloros, piemēram, `LATT-M3M-L87-F685`). Sensors nodrošina**vienu pelēktoņu joslu** bez Bayer mozaīkas: nav nekas, ko demosaicēt, nav kanālu savstarpējās pārklāšanās, ko atdalīt, un nav jāiestata baltā balanss.

Sekas, kas jāzina, pirms plānojat mono sistēmu:

* **Starojums un atstarošanās ir pilnībā definēti katram diapazonam.**Tās ir radiometriskas kartes katram diapazonam, tādējādi viena M3M kamera rada kalibrētu float32 starojuma intensitāti (W/m²/sr/nm) un uint16 atstarošanas koeficientu (`32768` = ρ 1,0) tieši tāpat kā M3C diapazons. Mono kadriem ir**identitātes** sensora reakcijas matrica — 3×3 atdalīšana nav nepieciešama un netiek piemērota.
* **Viena mono kamera nevar ģenerēt veģetācijas indeksu.** NDVI, NDRE un līdzīgiem indeksiem ir nepieciešami vismaz divi spektrālie diapazoni. Lai aprēķinātu indeksus, izmantojot mono aparatūru, ir jāapvieno vairākas M3M kameras — skatīt zemāk.
* M3M kameras pārraida **Mono12** (12 biti, 2 baiti/pikselis pārraides laikā), kas ir svarīgi [matricas joslas platuma plānošanai](arrays.md#bandwidth-the-rules-of-thumb).

## Ko Chloros izlaiž mono režīmā — un kā tas to paziņo

Krāsu apstrādes posmi vienkārši neattiecas uz vienjoslas sensoru. Chloros **izlaiž tos, parādot vienrindas ziņojumu**, nevis izsaucot kļūdu, un tajā pašā sesijā tos joprojām izpilda kā parasti jebkurai M3C (Bayer) kamerai:

| Posms | Mono (M3M) darbība | M3C darbība |
| --- | --- | --- |
| Demosaic / debayer | Izlaists — `debayered` eksporta līmenis ir 1-kanālu pelēktoņu attēls. | 3-kanālu demosaic. |
| Baltā balanss (`lattice white-balance`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Krāsu profils (`lattice color-profile`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Saturācija/kontrasts (`lattice color`) | Izlaists ar vienrindas ziņojumu. | Darbojas normāli. |
| Spektrālā pārklāšanās atdalīšana | Identitāte (bez 3×3 matricas). | Piemērota 3×3 matrica katrai kamerai. |
| Starojums / atstarošanās | **Darbojas** — katram diapazonam, pilnībā kalibrēts. | Darbojas katram diapazonam. |

GUI piemēro to pašu filtrēšanu: mono kamerai iestatījumu panelī katrai kamerai atsevišķi tiek paslēgtas rindas, kas attiecas tikai uz RGB (balansa iestatījumi, gamma, krāsu profils, piesātinājums, kontrasts, kanālu sadalījums), un reāllaika histogramma ir fiksēta uz vienu **MONO** līkni. Atšķirības kritērijs visā slāņu kopā ir `M3M` simbols modeļa virknē, kas GUI/SDK tiek parādīts kā `is_mono`.

## Indeksiem nepieciešamas ≥ 2 joslas: saskaņošana → salikšana → indeksēšana

Mono indeksa darba plūsma vienmēr sastāv no trim soļiem:

1. **Saskaņošana** — vērst vairākas M3M kameras uz dažādiem viļņu garumiem (piemēram, F650 „Red” un F850 &quot;NIR&quot;), savienojiet tās kā [daudzkameru masīvu](arrays.md) un ļaujiet Chloros aprēķināt kameru savstarpējo reģistrācijas deformāciju.
2. **Kopa** — saskaņotie kadri kļūst par vienu daudzjoslu attēlu (katra kamera veido vienu nosauktu joslu).
3. **Indekss** — aprēķiniet indeksa formulu attiecībā uz kopas joslām, pēc izvēles renderējot to ar LUT palīdzību.

GUI vidē visa šī ķēde ir **Combined Cameras**masīva attēlošanas režīms: reāllaika kompozīcija jau ir saskaņota, un masīva indeksa kalkulators (zemāk) definē formulu, ko tas renderē. Uztvertos eksportus var deformēt līdz tai pašai saskaņošanai, izmantojot**Aligned** uztveršanas opciju.

## Indeksa kalkulators

Indeksa kalkulators veido indeksa izteiksmi, ko izmanto reāllaika skatā un eksportos ar indeksu katrai kamerai atsevišķi. Tas ir viens kopīgs logs, ko var atvērt no divām vietām cilnes „Cameras” (Kameras) sānjoslā:

* **Katrai kamerai atsevišķi**— Reāllaika priekšskatījums →**Indekss** ratiņš (tikai RGN/OCN/NGB Bayer kameras; atsevišķai mono kamerai nav indeksa vadības, jo ar vienu joslu nevar veidot indeksu).
* **Katram masīvam**— masīva iestatījumi → Reāllaika priekšskatījums →**Indekss**ratiņš. Šis ir mono ceļš: joslu saraksts aptver**visas masīvā iekļautās kameras**, tādējādi mono pāris šeit iegulda savas divas joslas.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Tās vadības elementi, no augšas uz leju:

* **Bandu mikroshēmas** („Bands — noklikšķiniet, lai pievienotu izteiksmei”) — viena poga katram pieejamajam diapazonam, ar norādītu krāsas nosaukumu + viļņa garumu nm (atkārtoti krāsu nosaukumi tiek nošķirti, piemēram, „Color 850”). Noklikšķinot, joslas simbols tiek ievietots kursora vietā. Joslas no kamerām, kas nevar radīt starojumu katrai joslai atsevišķi (RGB/FRGB), tiek atfiltrētas.
* **Operatoru un funkciju pogas** — `+ - * / ( ) ^ ,` un `abs() sqrt() log() log10() exp() min() max() pow()`.
* **Izteiksmes teksta lauks** — brīvi ievadāma formula; vietas turētājs parāda klasisko NDVI formu `(NIR - Red) / (NIR + Red)`. Virs tā esošais tikai lasāmais, simbolos sadalītais priekšskatījums attēlo joslu čipus, skaitļus un karogus kā nezināmus simbolus.
* **Derīguma josla**— pelēka „Tukša — netiks piemērots nekāds indekss”; zaļš „Derīgs izteiksmes teksts”; sarkans ar konkrētu sintakses kļūdu (nezināma josla, neskaidra josla, ko fiksējušas vairākas kameras, trūkstošas iekavas, …); vai dzintara krāsā, ja izteiksme ir derīga, bet ir**konstante** (piem., `X/X`, vai NDVI saucējs, kurā `−` ir ievadīts `+` vietā) — konstante attēlo visu kadru vienā krāsā.
* Atsevišķs dzintara krāsas brīdinājums parādās, ja piemērotais izteiksmes ir pareizs, bet **reālais kadrs ir vienveidīgs** (plakana vai piesātināta aina) — tiek automātiski atklāta histogrammas sabrukšana.
* **Piemērot LUT**(pēc noklusējuma ieslēgts; izslēgts = pelēktoņu izstiepšana),**Līmenis**2/3/5/7 pakāpes (pēc noklusējuma 7 pakāpes) un**Min / Maks**ievades lauki, kas atrodas abpus gradienta joslai. Min noklusējuma vērtība ir**0,2**— tā palielina krāsu rampu līdz veģetācijai atbilstošajam diapazonam, savukārt vērtības zem šīs robežas tiek attēlotas kā pelēkā toņu skala; lai iegūtu pilnu indeksa diapazonu, iestatiet Min uz −1 (poga**Reset** atjauno diapazonu no −1 līdz +1). Max noklusējuma vērtība ir 1.
* **Reāllaika histogramma** indeksa sadalījumam — kvadrātsaknes mērogā skalētas joslas, dzintara krāsas p2/p98 percentiles līnijas, balta mediānas līnija un ārpus diapazona galos esošie rādījumi („◀ N% &lt; lo” / „hi &lt; N% ▶&quot;), kas virs 1 % kļūst dzintara krāsā, norādot, ka jāpaplašina Min/Max logs.
* **Piemērot**piemēro izteiksmi reāllaika plūsmā; LUT pielāgojumi tiek piemēroti reāllaikā, nespiežot pogu „Piemērot”. Izteiksmes ir apzināti**tikai sesijai** — tās netiek saglabātas starp sesijām.

<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->

## CLI ceļš

Tā pati izlīdzināšanas → kaudzes → indeksa ķēde, pilnībā skriptējama:

```bash
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` saista iestatījuma simbolus ar kaudzes joslu nosaukumiem. Divi noteikumi palīdz izvairīties no neveiksmīgas izpildes:

* **Simboliem tiek ņemta vērā lielais un mazais burts**, un tiem precīzi jāsakrīt ar iestatījuma kanālu nosaukumiem — iestatījumos tiek izmantoti mazie burti (NDVI ir `red`,`nir`; pārbaudiet `--list-presets`). `--channel red=Red_660` darbojas; `--channel RED=660` neizdodas, parādot kļūdu `channel_map missing entries`.
* Frekvenču joslas pusē ir jānorāda frekvenču josla izlīdzinātajā kopā (`lattice align-info --profile align.json` tās uzskaita). Bezsaistes režīmā tiek pieņemti arī frekvenču joslu indeksi, sākot no 0, piemēram, `--channel red=0 --channel nir=1`.

`lattice index` darbojas arī pilnībā bezsaistē, izmantojot saglabātu saskaņotu daudzjoslu TIFF:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Indeksu iestatījumi

`lattice index --preset` (un cilnes „Attēls” [Indeksa/LUT smilšu kaste](../image-viewer-gui/index-lut-sandbox.md), kas izmanto to pašu dzinēju) piedāvā šos **22 iestatījumus**:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Izmantojiet `chloros-cli lattice index --list-presets`, lai apskatītu katras iestatījuma formulas un kanālu simbolus, un `--list-gradients`, lai apskatītu pieejamos krāsu gradientus. Pielāgotās formulas izmanto `--formula EXPR` ar tādu pašu sintaksi kā indeksa kalkulators. Ņemiet vērā, ka šis iestatījumu saraksts attiecas konkrēti uz LATTICE indeksu dzinēju — izvēlne „Project Settings” (Projekta iestatījumi) importētiem attēliem ir atšķirīgs saraksts (skatīt [Multispektrālās indeksu formulas](../project-settings/multispectral-index-formulas.md)).

Pilnais karogu kopums (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, izlīdzināšanas deformācijas slēdži `--live`, un citi) ir aprakstīts [CLI atsauces rokasgrāmatā § Indekss / Veģetācijas matemātika](../reference/cli-reference.md#index--vegetation-maths); SDK ekvivalenti atrodami [SDK atsauces dokumentā](../reference/sdk-reference.md).

## Indeksa produktu saglabāšana no mono masīva

Ja ir pieslēgts masīvs un piemērots indeksa izteiksme, `array-capture` (vai GUI opcija **Capture All**) saglabā eksporta līmeņus katrai kamerai *un* indeksa renderējumu — `--index`/`--no-index` to ieslēdz vai izslēdz CLI, un saglabāšana pēc noklusējuma ietver visus piemērojamos līmeņus. Vienas kameras ieguldījums katrā uzņemšanas grupā ir tās viena josla neapstrādātos/debayered (pelēktoņu)/starojuma/atstarošanas līmeņos, kā arī kopīgais kombinētā indeksa kompozīts, ja masīvs darbojas kombinētajā režīmā. Skatīt [Daudzkameru matricas § Uztveršana](arrays.md#capturing-monitoring-vs-analysis).
