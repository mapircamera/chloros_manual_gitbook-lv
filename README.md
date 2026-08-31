---
metaLinks: {}
---

# Sākums

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>Chloros ir programmatūras lietojumprogramma no [MAPIR](https://www.mapir.camera), kas paredzēta multispektrālo attēlu apstrādei, MAPIR aparatūras vadībai reāllaikā un sensoru datu reģistrēšanai. Chloros 1.2.0 atbalsta visu MAPIR produktu saimi:

* **Survey3 kameras** — apstrādā RAW+JPG uzņēmumus, pārvēršot tos kalibrētos atstarošanas un veģetācijas indeksa kartēs. Skatiet [Atbalstītās kameras](supported-cameras.md).
* **LATTICE kameras** — tiešsaistē savienojiet GigE multispektrālos kameru moduļus atsevišķi vai kā sinhronizētus daudzkameru masīvus: priekšskatiet, uzņemiet un pārveidojiet par kalibrētiem starojuma un atstarošanas rādītājiem. Skatīt [LATTICE sadaļu](lattice/README.md).
* **DAQ gaismas sensori** — DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet) spektrālie sensori: reāllaika kalibrēti spektri, `.daq` ieraksti un lejupvērsta apgaismojuma dati atstarošanas apstrādei. Skatīt [sadaļu par DAQ](daq/README.md).

{% hint style="success" %}
**Jaunumi Chloros 1.2.0 versijā**: LATTICE kameru un matricas vadība reāllaikā, DAQ gaismas sensoru integrācija, uzņemšanas režīmi un ierakstītāji, pilnīga LATTICE radiometriskās apstrādes sistēma, projektu automatizācija no CLI/SDK un daudz kas cits. Skatiet jaunumu sarakstu zemāk un [lejupielādējiet](download.md), lai iepazītos ar izmaiņu žurnālu.
{% endhint %}

{% hint style="info" %}
**Izmantojat Chloros kopā ar AI palīgu?** Šī rokasgrāmata ir paredzēta tieši tam. Norādiet savam palīgam:

* `https://mapir.gitbook.io/chloros/llms.txt` — mašīnlasāms katras lapas rādītājs.
* Jebkura lapa neapstrādātā Markdown formātā — pievienojiet `.md` tās URL (piem., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI atsauce](reference/cli-reference.md) un [SDK atsauce](reference/sdk-reference.md) — pilnīgas atsauces lapas ar precīzām vērtībām, kas izstrādātas LLM lietošanai.

Piemērs: *&quot;Izlasi https://mapir.gitbook.io/chloros/reference/cli-reference.md,, tad uzraksti skriptu, kas piesakās un apstrādā mapu ~/flights/flight_001, pārveidojot to par atspoguļojuma + NDVI GeoTIFF failiem.&quot;*

Pilnīga rokasgrāmata: [Chloros izmantošana ar AI palīgiem](ai-assistants.md).
{% endhint %}

***

## Jaunumi Chloros 1.2.0 versijā

* **Kameru vadība reāllaikā — jauna cilne „Kameras”.** Pievienojiet LATTICE kameras pa vienai vai kā sinhronizētu daudzkameru sistēmu (PTP laika sinhronizācija, aparatūras izraisīta uzņemšana), izmantojot reāllaika priekšskatījuma pārklājumus, histogrammas katram frekvenču diapazonam, viedo automātisko ekspozīciju, reāllaika indeksa aprēķinātāju un kameras programmatūras atjauninājumus lietotnē.
* **Gaismas sensori — jauna cilne „Gaismas sensori”.** Pievienojiet DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet) sensorus; skatiet reāllaika kalibrētus spektrus (W/m²/nm), ierakstiet `.daq` failus savā projektā, izvēlieties kap-korekcijas profilus un atjauniniet DAQ-E programmatūru pa tīklu.
* **Uzņemšanas režīmi un ierakstītāji.** Vienreizēja / nepārtraukta / intervāla uzņemšana, kā arī tikai neapstrādātu datu režīms „Fastest Capture“; katram projektam atsevišķi izvēlēties, kuras kameras un eksporta veidus ģenerē funkcija „Capture All“; masīva ierakstītāji, lai iegūtu indeksētu video monitoringa kvalitātē un neapstrādātu datu sērijas analīzes kvalitātē ar bezsaistes video kompilācijām.
* **LATTICE apstrādes procesa ķēde.** Importējiet LATTICE uzņemšanas mapes un katru neapstrādāto kadru sadaliet par debayeringa, priekšskatīšanas, float32 starojuma (W/m²/sr/nm) un atstarošanas produktiem ar katram produktam atsevišķiem slēdžiem. Atstarošanas dati var tikt iegūti no kadrā esoša kalibrēšanas mērķa vai DAQ lejupvērstā starojuma; eksportētajiem datiem tiek piemērota masīva izlīdzināšana; trūkstošā rūpnīcas kalibrēšana tiek automātiski lejupielādēta pēc kameras sērijas numura.
* **Projekti atceras aparatūru.** Pieslēgtās kameras un gaismas sensori tiek saglabāti kopā ar projektu (`cameras.json` / `sensors.json`) un, atverot projektu no jauna, tie atkārtoti savienojas ar saglabātajiem iestatījumiem. Skatīt [GUI: Projekti](projects.md).
* **Attēlu skatītāja uzlabojumi.** Kursora pikseļu/indeksa nolasīšana ar pareizu atstarojuma mērogošanu katram failam, slāņu histogrammas, GSD binninga sliders, režīmi „Per Trigger” / „Per Camera”, LATTICE produktu skati un indeksa/LUT sandbox eksportēšana uz disku.
* **CLI un SDK, ievērojami paplašināti.** Jaunas `lattice`, `daq pool-*`, `project` un `time-sync` komandu grupas; jaunas `process` opcijas (`--input-level`, atsevišķu produktu slēdži, `--reflectance-source`, masīva izlīdzināšanas karodziņi); SDK viedās savienošanas rokturi (`connect_camera` / `connect_array` / `connect_daq_sensor`), kas automātiski palaista aizmugurējo sistēmu; `open_project()` automatizācija; SDK rīks ir iekļauts instalētājos un publicēts PyPI kā `chloros-sdk`.
* **Pārredzama kļūdu semantika.** `chloros-cli process` izpilde, kas pieprasīja produktus, bet neizveidoja nevienu, tagad skaidri ziņo par kļūdu un beidzas ar nenulles kodu; veiksmīgas izpildes ziņo, cik daudz attēlu produktu tās izveidoja.
* **Jauns izvades izkārtojums.** Produkti tiek saglabāti `<project>/<camera>/<format>/<Product>_Images/` mapēs un saglabā avota faila nosaukumu — produktu identificē mape, nevis faila nosaukuma paplašinājums. Skatīt [Izvades attēlu formāti](output-image-formats.md).
* **Vairāk ievades avotu, plānu un valodu.** `.dng` ievades atbalsts; visas 38 saskarnes valodas ir pilnībā pieejamas; aparatūras ierobežojumi katram plānam, ļaujot bezmaksas (bez pieteikšanās) lietošanu līdz 4 kamerām un 2 gaismas sensoriem.
* **Uzticamība.** Funkcija „Pārtraukt apstrādi” tiek pārtraukta bez kļūdām, sniedzot precīzu izpildes kopsavilkumu; daudzkameru projekti eksportē katras kameras datus; un instalētāja atjauninājumi vairs neizraksta jūs no sistēmas.***

Chloros ir pieejams 3 lietojumvides versijās:

## Chloros: Galda datora GUI lietojumprogramma

Atsevišķs logs ar visām funkcijām, ieskaitot cilnes „Kameras” un „Gaismas sensori” reāllaikā. _Tikai Windows._

## [Chloros CLI: Komandrindas interfeiss](CLI.md)

Komandrindas pakotņu apstrāde, kā arī reāllaika komandas `lattice`, `daq pool-*`, `project` un `time-sync`. Ideāli piemērots automatizācijai, skriptu izveidei un darbībai bez grafiskās saskarnes. Pieejams **Windows, Linux amd64 un Linux arm64 (NVIDIA Jetson)**. _Lai piekļūtu CLI, ir nepieciešams maksas Chloros+ līmenis._

## [Chloros API: Python SDK](api-python-sdk.md)

Programmatiska Python saskarne automatizācijai un pielāgotām darba plūsmām: pilna procesa apstrāde, tiešraides kameru/matricas sesijas, DAQ sensoru sesijas un saglabāto projektu automatizācija. Tiek instalēta kopā ar desktop/CLI paketi un publicēta arī kā `pip install chloros-sdk`. _Lai piekļūtu API, ir nepieciešams maksas Chloros+ līmenis._

***

## Atbalstītās platformas

| Platforma | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11 (x64)** | Jā | Jā | Jā |
| **Linux amd64 (x86_64)** | Nē | Jā | Jā |
| **Linux arm64 (NVIDIA Jetson)** | Nē | Jā | Jā |

Linux instalēšanas instrukcijas skatiet sadaļā [Linux un Edge Computing](linux/linux-overview.md).

***

## Sāciet trīs soļos

1. **Instalēšana** — lejupielādējiet un palaidiet instalētāju savai platformai. Skatiet [Lejupielāde](download.md).
2. **Piesakieties (nav obligāti, ja izmantojat grafisko lietotāja saskarni)** — grafiskā lietotāja saskarne apstrādā attēlus bez maksas arī bez konta. [Chloros+ piesakoties](chloros+-login.md) tiek atbloķēta paralēlā apstrāde, GPU paātrinājumu, lielākus ierīču limitus un piekļuvi CLI/SDK.
3. **Izveidojiet savu pirmo projektu** — atveriet Chloros, izveidojiet [Jaunu projektu](projects.md), [pievienojiet savus attēlus](processing-images-gui/adding-files-to-a-project.md) un [uzsāciet apstrādi](processing-images-gui/starting-the-processing.md). Lai vadītu reālu aparatūru, atveriet cilni „Kameras” vai „Gaismas sensori” — skatiet [GUI: navigācija](navigation.md).***

## Chloros+

Lai gan Chloros ir bezmaksas lietošanai lielākajai daļai uzdevumu, iespējams, jūs vēlēsieties vairāk. Tādos gadījumos jums var noderēt maksas licence Chloros+. Ar Chloros+ licenci jūs varat atbloķēt jaunas funkcijas, piemēram:

* **Daudzpavedienu apstrāde**: ievērojami paātrina attēlu apstrādi lielākiem projektiem, vienlaikus apstrādājot attēlus caur apstrādes ķēdi.
* **GPU (CUDA) paātrinājums**: izmantojiet mūsdienu GPU atmiņas iespējas, lai vēl vairāk paātrinātu attēlu apstrādes procesu. Lai sasniegtu labākos rezultātus, ieteicams izmantot 4 GB vai vairāk VRAM.
* **Chloros+**[**CLI**](CLI.md)**Piekļuve**: palaidiet Chloros+ no komandrindas, lai automatizētu un integrētu savā programmnodrošinājumā. Pieejams jebkurā maksas līmenī; tiek piemērots servera pusē.
* **Chloros+**[**API**](api-python-sdk.md)**Piekļuve:** palaidiet Chloros+ no Python, lai nodrošinātu programmatisku kontroli, kas ļauj vienkārši integrēt sistēmu jūsu pētniecības procesos, datu analīzes darba plūsmās un pielāgotās lietojumprogrammās. Pieejams jebkurā maksas līmenī; tiek īstenots servera pusē.
* **Augstāki aparatūras ierobežojumi**: vienlaikus pieslēdziet vairāk kameru un gaismas sensoru. Bez pieteikšanās grafiskais interfeiss (GUI) atbalsta līdz 4 kamerām un 2 DAQ gaismas sensoriem; maksas plāni palielina abus ierobežojumus:

| Plāns | Kameras | DAQ gaismas sensori |
| --- | --- | --- |
| Iron (bezmaksas, bez pieteikšanās) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Vairāku ierīču izmantošana**: katra Chloros+ licence ļauj reģistrēt 2 un vairāk ierīču. Izmantojiet savu MAPIR Cloud kontu, lai pārvaldītu reģistrētās ierīces. Paplašiniet atbalstu vairākām ierīcēm, atjauninot savu Chloros+ licenci.
* **Uzlabota tekstūru ņemot vērā debayer metode:** augstas kvalitātes, malas ņemot vērā debayer metode, apvienota ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayeringa radītos trokšņus.
* **Pielāgotas multispektrālo indeksu formulas:** ievadiet pielāgotus multispektrālos indeksus Chloros rastra kalkulatoros gan apstrādei, gan attēlu apskates vidē.
* **Linux un malu datu apstrāde:** palaidiet Chloros uz Linux x86\_64 un ARM64 platformām, tostarp NVIDIA Jetson, lai veiktu apstrādi laukā un malu datu apstrādi. Skatīt [Linux pārskatu](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ cenas un reģistrācija</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.2.0 build so the banner prints "Chloros CLI 1.2.0". -->
