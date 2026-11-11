---
metaLinks: {}
---

# Sākums

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros
ir [MAPIR
](https://www.mapir.camera) izstrādāta programmatūra, kas paredzēta multispektrālo attēlu apstrādei, MAPIR
aparatūras vadībai reāllaikā un sensoru datu reģistrēšanai. Chloros
1.3.0 atbalsta visu MAPIR
produktu saimi:

* **Survey3
kameras** — apstrādā RAW+JPG attēlus, pārvēršot tos par kalibrētām atstarojuma un veģetācijas indeksa kartēm. Skatīt [Atbalstītās kameras](supported-cameras.md).
* **LATTICE kameras** — reāllaikā pieslēdziet GigE multispektrālos kameru moduļus atsevišķi vai kā sinhronizētus daudzkameru masīvus: priekšskatiet, uzņemiet un apstrādājiet, lai iegūtu kalibrētus starojuma un atstarojuma produktus. Skatīt [LATTICE sadaļu](lattice/README.md).
* **DAQ gaismas sensori** — DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E un DAQ-E-S (Ethernet) spektrālie sensori: reāllaika kalibrēti spektri, `.daq` ieraksti un lejupvērstais apgaismojums atstarošanas apstrādei. Skatīt [DAQ sadaļu](daq/README.md).

{% hint style="success" %}
**Jaunumi programmā Chloros
1.3.0**: AI palīgi var vadīt Chloros
ar `chloros-mcp`, un CLI
, SDK
un `chloros-mcp` piedāvā tās pašas funkcijas. LATTICE indeksa produkti ir uzlaboti, pilnās sensoru matricas darbojas ātrāk un darbojas jebkurā tīklā, un aizmugurējā sistēma turpina darboties starp komandām. Skatiet zemāk esošo jaunumu sarakstu un [lejupielādējiet](download.md) instalācijas programmas.
{% endhint %}

{% hint style="info" %}
**Izmantojat Chloros
kopā ar AI palīgu?** Šī rokasgrāmata ir paredzēta tieši tam. Norādiet savam palīgam:

* `https://mapir.gitbook.io/chloros/llms.txt` — mašīnlasāms katras lapas indekss.
* Jebkura lapa neapstrādātā Markdown formātā — pievienojiet `.md` tās URL
-am (piem., `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI
atsauces](reference/cli-reference.md) un [SDK
atsauces](reference/sdk-reference.md) — pilnīgas, precīzu vērtību atsauces lapas, kas izstrādātas LLM lietošanai.

Piemērs: *&quot;Izlasi https://mapir.gitbook.io/chloros/reference/cli-reference.md,, tad uzraksti skriptu, kas piesakās un apstrādā mapi ~/flights/flight_001, pārvēršot to par reflektances + NDVI
GeoTIFF failiem.&quot;*

Vai arī ļaujiet tam tieši palaist Chloros
: `chloros-mcp`, MCP serveris, kas instalēts kopā ar SDK
, darbojas ar „Claude Desktop”, „Claude Code”, „Cursor” un citiem MCP klientiem.

Pilnīga rokasgrāmata: [Chloros
izmantošana ar AI palīgiem](ai-assistants.md).
{% endhint %}

***

## Jaunumi Chloros
1.3.0 versijā

* **AI palīgi var vadīt Chloros
.** `chloros-mcp`, MCP serveris, kas instalēts kopā ar Python
SDK
, ļauj Claude Desktop, Claude Code, Cursor un citiem MCP klientiem apstrādāt attēlus un vadīt LATTICE kameras, matricas un DAQ gaismas sensorus. Skatīt [Chloros
izmantošana ar AI palīgiem](ai-assistants.md).
* **CLI
, SDK
un `chloros-mcp` piedāvā tās pašas funkcijas.** Drošības nolūkā CLI
paliek tikai pieteikšanās, DAQ-E konfigurēšana un tiešie DAQ serveri. Dažiem 1.2.0 skriptiem nepieciešamas izmaiņas: skatiet sadaļu **Izmaiņas versijā 1.3.0** [CLI
atsauces](reference/cli-reference.md) un [SDK
atsauces](reference/sdk-reference.md) sākumā.
* **Uzlaboti LATTICE indeksa produkti.** Visos apstrādes režīmos ir uzlaboti NDVI
un citi indeksa produkti, kas eksportēti no LATTICE uzņēmumiem.
* **Windows
instalētāja opcijas.**Sadaļā**Sistēmas iestatījumi**instalētājs piedāvā opcijas**Iespējot garos Windows
ceļus**un**Izslēgt Chloros
a backend no Microsoft Defender skenēšanas**, abas pēc noklusējuma ir atzīmētas. Pirmā opcija ļauj projekta mapēm atrasties dziļāk par 260 rakstzīmēm; otrā novērš to, ka reāllaika skenēšana palēnina kameras uzņemšanu (aptuveni par 30 % no uzņemšanas ātruma pie augstiem ātrumiem). Instalētājs tagad arī pievieno CLI
mapi `PATH` tikai vienu reizi un noņem dublētos ierakstus, ko atstājuši iepriekšējie instalētāji. Skatīt [Lejupielāde](download.md).
* **Pārāk dziļas projekta mapes tiek noraidītas jau sākumā.** Ja Windows
garie ceļi ir atspējoti (Windows
noklusējums), projekta mape, kas ir pārāk dziļa, lai atbilstu 260 rakstzīmju ceļa ierobežojumam, tiek noraidīta, pirms tiek palaista jebkura darbība, norādot garāko projekta mapi, kas atbilst ierobežojumam, un to, kā ieslēgt garos ceļus. Iepriekš šāda darbība neradīja nekādus rezultātus. Ja garie ceļi ir ieslēgti, produkti ļoti dziļās mapēs tagad saglabā savus metadatus (GPS un XMP tagus).
* **Masku ierobežošana.** Ja LATTICE starojuma vai atstarošanas produkta joslu atdalīšana rada negatīvu joslas vērtību, šī vērtība tiek nogriezta līdz nullei, un `clamp_masks/` mapē blakus produktu mapēm tiek saglabāta maska katram pikselim, kurā tiek reģistrēts, kuri pikseļi un joslas tika ietekmētas. Produkts nosauc savu masku `Chloros:UnmixClampMask` tagā. Reāllaika skati tagad tiek nogriezti tieši tāpat kā eksportēšanas laikā. Skatīt [Izvades attēlu formāti](output-image-formats.md).
* **Produkti apraksta paši sevi.** Indeksa produkti izmanto savas apstrādes tagus (`Chloros:ProcessingLevel=index`, indeksa izteiksmi un informāciju par to, vai tie aprēķināti no starojuma vai atstarošanas) nevis avota neapstrādāto datu tagus. Survey3
produkti visos apstrādes režīmos izmanto Chloros
apstrādes tagus. LATTICE neapstrādātie un debayered faili saglabā sensora sākotnējo orientāciju un reģistrē savu orientāciju XMP; attēlu skatītājs tos pagriež tikai attēlošanas nolūkā.
* **Linux
paketes tiek piegādātas neizpakotas.** Aizmugurējā programma un CLI
tiek instalētas kā mapes zem `/usr/lib/chloros/`, tādējādi tās vairs neizpako sevi katrā palaišanas reizē. x86_64 vidē backend vairs nepatērē apmēram 100 sekundes, lai sāktu darbu, un `.deb` ir aptuveni divas reizes mazāks nekā iepriekš. Komplektā iekļautais ExifTool tagad darbojas uz tīras Ubuntu instalācijas. `.deb` tagad instalē arī Python
SDK
sistēmai `python3` (instalējiet to ar `sudo apt install ./chloros_1.3.0_<arch>.deb`), palielina kodola soketa uztveršanas buferus, kas nepieciešami kameras straumēm, un pievieno aizmugurējā procesa pakalpojumu, kas darbojas kā jūsu lietotājs. Jetson pakete, `chloros_1.3.0_arm64_jp6.deb`, ir izstrādāta JetPack 6 vajadzībām un apstrādā datus uz Jetson GPU, izmantojot JetPack CUDA versiju PyTorch. Linux
vidē katrs eksportētais attēls tagad nonāk uz diska vienu reizi (tiek ierakstīts un marķēts atmiņā, pēc tam kopēts uz galīgo vietu): uz Jetson, kur diska ātrums nosaka darba tempu, 288 attēlu LATTICE izpilde aizņem 14 minūtes, nevis 24. Skatīt [Linux
instalācija](linux/linux-installation.md).
* **LATTICE OCN
kameras saules gaismā nolasa datus precīzāk.** Pēc joslu atdalīšanas starojuma intensitātei, atstarošanai un indeksiem tiek veikta ātra korekcija, tādējādi pelēks mērķis saulē tiek nolasīts tuvu neitrālajam NDVI
. RGN
kameras paliek nemainīgas.
* **Kļūdu ziņošana lietotnē.** Kļūdas ikona cilnes „Debug Log” augšdaļā nosūta uz MAPIR
aprakstu, līdz 10 attēliem, žurnālus un sistēmas informāciju. Skatīt [GUI: Navigācija](navigation.md).
* **LATTICE masīvi.**Masīvi pēc noklusējuma saglabā pilnu sensora diapazonu, un saspiestais 12 bitu pārraides formāts (`--packed`, noklusējums `auto`) palielina pilna sensora kadru ātrumu aptuveni par trešdaļu; masīvs darbojas jebkurā tīklā ar tā atļauto kadru ātrumu (lēns savienojums samazina kadru ātrumu, nevis noraida masīvu vai samazina tā kadrus); masīvs, kura kameras pēc strāvas pārtraukuma atjaunojas pašas, atkal ieslēdzot PTP; automātiskā ekspozīcija vairs nesamazina šo ātrumu vājā apgaismojumā (tā aizpilda periodu, pēc tam palielina pastiprinājumu); Nepārtraukta uzņemšana spēj tikt līdzi ātrām masīvu sistēmām;**Highlights** kontrole RGB
kamerām; un SDK
`ArraySession` iegūst `grab()`, `frame_stream()` un `continuous()`. Skatīt [sadaļu „LATTICE”](lattice/README.md).
* **Stabilāka automātiskā ekspozīcija.** Ekspozīcija tiek uzturēta nemainīgā gaismas līmenī un stabilizējas dažās sekundēs pēc izmaiņām; nelielas spilgtas vietas vairs nepadara ainu tumšāku; manuālā ekspozīcija atstāj pastiprinājumu automātiskajā režīmā; un kamera, kas pārstāj reaģēt spilgtā saulē, tiek uzturēta atbilstoši gaismas līmenim, ko nolasa gaismas sensors vai, ja tāds nav pieslēgts, kameras paša kadri. Skatīt [Kameras iestatījumi](lattice/camera-settings.md).
* **Gaismas sensori.** **DAQ-E-S atbalsts:** tās fiksētajam difuzoram nav nepieciešama vāciņa, un katrs nolasījums ietver ierīces slīpumu, pagriezienu un pacelšanu. Ieraksts norāda, vai vāciņš tika izvēlēts vai pieņemts kā noklusējums, un apstrādes laikā tiek parādīts brīdinājums par pieņemto vāciņu: DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez vāciņa, tāpēc norādiet uzstādīto vāciņu. Reāllaika `.daq` ieraksti saglabājas arī pēc strāvas padeves pārtraukuma (saglabājiet `.daq-journal` failu kopā ar tā `.daq`), un DAQ-E, kas pārtrauc datu nosūtīšanu, tiek atkārtoti pieslēgts, ierakstā atzīmējot pārtraukuma vietu. Projekts, kurā ir tikai gaismas sensoru ieraksti, tos uzskaita un apstrādā no lietotnes un CLI
, un ar `chloros_scripts` veiktais ieraksts tiek uzskaitīts kā DAQ, kas to veica, ar ierobežojumu, kuru varat iestatīt. Katrs ieraksts saglabā izmantoto DAQ rādījumu. Skatīt [DAQ sadaļu](daq/README.md).
* **Apstrāde.**LATTICE kadru apstrāde sākas mērķa noteikšanas laikā. Mērķa un DAQ atsauces tiek noteiktas katram frekvenču joslas diapazonam atsevišķi. Pārvietots projekts atkārtoti saista savus failus. 8-bitu ieraksta eksportētie faili ir 8-bitu formātā, izmantojot jauno**TIFF
(8-bitu)** formātu. Survey3
, kas palaists bez kalibrēšanas mērķa, tiek saglabāts kā rezerves produkts (`Vignette_Corrected_Images` vai `Sensor_Response_Images`), nevis kā atstarojuma attēls. Atstarojuma attēla DLS birkas katram diapazonam vienmēr pieder pašam kadram. LATTICE uzņemumiem tiek atteikta funkcija „Texture Aware”, un tiem vienmēr tiek piemērota standarta debayer apstrāde.
* **CLI
un SDK
.** Relatīvie izvades ceļi tagad atrodas mapē, no kuras tiek palaista komanda vai skripts (iepriekš instalētais backend mēģināja tos atrisināt savā instalācijas mapē, kas izraisīja kļūdu). Aizmugurējais modulis turpina darboties starp komandām un skriptiem un pats apstājas pēc 30 minūtēm, ja netiek izmantots; to pārvalda `chloros-cli backend status` un `backend stop`. Ierakstīšanas komandas nosauc katru failu, kuru tās nevarēja ierakstīt, un beidz darbu ar kodu 1, savukārt atkārtotas ierakstīšanas tiek pārtrauktas, ja disks ir pilns. Veiksmīga `process` izpilde ziņo par `Image products written: N (plus M clamp masks)`. Windows
vidē SDK
`ChlorosLocal` / `process_folder` atrod instalēto backend (un izmanto to, kas jau darbojas), un `read_image_audit_tags` darbojas no parastā `pip install`.

***

## Jaunumi Chloros
1.2.0 versijā

* **Kameru vadība reāllaikā — jauna cilne „Cameras”.** Pievienojiet LATTICE kameras pa vienai vai kā sinhronizētus daudzkameru masīvus (PTP laika sinhronizācija, aparatūras izraisīta uzņemšana), ar reāllaika priekšskatījuma pārklājumiem, histogrammām katram diapazonam, viedo automātisko ekspozīciju, reāllaika indeksa kalkulatoru un kameras programmaparatūras atjauninājumiem lietotnē.
* **Gaismas sensori — jauna cilne „Gaismas sensori”.** Pievienojiet DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet) sensorus; skatiet reāllaika kalibrētus spektrus (W/m²/nm), ierakstiet `.daq` failus savā projektā, izvēlieties kap-korekcijas profilus un atjauniniet DAQ-E programmatūru pa tīklu.
* **Uztveršanas režīmi un ierakstītāji.** Vienreizēja / nepārtraukta / intervāla uztveršana, kā arī tikai neapstrādātu datu ātrākais uztveršanas režīms; katram projektam atsevišķi izvēlēties, kuras kameras un eksporta veidus ģenerē funkcija „Capture All“; masīva ierakstītāji uzraudzības līmeņa indeksētiem video un analīzes līmeņa neapstrādātu datu sērijām ar video kompilācijām bezsaistē.
* **LATTICE apstrādes procesa ķēde.** Importējiet LATTICE uzņemšanas mapes un katru neapstrādāto kadru sadaliet uz debayeringa, priekšskatījuma, float32 starojuma (W/m²/sr/nm) un atstarošanas produktiem ar katram produktam atsevišķiem ieslēgšanas/izslēgšanas slēdžiem. Atstarošanas dati var tikt iegūti no kadra iekšējā kalibrēšanas mērķa vai DAQ lejupvērstā starojuma; eksportētajiem datiem tiek piemērota masīva izlīdzināšana; trūkstošā rūpnīcas kalibrēšana tiek automātiski lejupielādēta pēc kameras sērijas numura.
* **Projekti atceras aparatūru.** Pieslēgtās kameras un gaismas sensori tiek saglabāti kopā ar projektu (`cameras.json` / `sensors.json`) un, atverot projektu no jauna, atkārtoti savienojas ar saglabātajiem iestatījumiem. Skatīt [GUI: Projekti](projects.md).
* **Attēlu skatītāja uzlabojumi.** Kursora pikseļu/indeksa nolasīšana ar pareizu atstarojuma mērogošanu katram failam, slāņu histogrammas, GSD binninga slīdnis, režīmi „Per Trigger” / „Per Camera”, LATTICE produktu skati un indeksa/LUT sandbox eksportēšana uz disku.
* **Ievērojami paplašināta CLI
un SDK
.** Jaunas komandu grupas `lattice`, `daq pool-*`, `project` un `time-sync`; jaunas `process` opcijas (`--input-level`, pārslēgšanas opcijas katram produktam, `--reflectance-source`, masīva izlīdzināšanas karodziņi); SDK
viedās savienojuma rokturi (`connect_camera` / `connect_array` / `connect_daq_sensor`), kas automātiski palaista aizmuguri; `open_project()` automatizācija; SDK
rīks ir iekļauts instalētāju paketē un publicēts PyPI kā `chloros-sdk`.
* **Pārredzama kļūdu semantika.** `chloros-cli process` izpilde, kas pieprasīja produktus, bet neizveidoja nevienu, tagad skaidri ziņo par kļūdu un beidzas ar rezultātu, kas nav nulle; veiksmīgas izpildes ziņo, cik daudz attēlu produktu tās izveidoja.
* **Jauns izvades izkārtojums.** Produkti tiek saglabāti `<project>/<camera>/<format>/<Product>_Images/` mapēs, saglabājot avota faila nosaukumu — produktu identificē mape, nevis faila nosaukuma paplašinājums. Skatīt [Izvades attēlu formāti](output-image-formats.md).
* **Vairāk ievades avotu, plānu un valodu.** `.dng` ievades atbalsts; visas 38 saskarnes valodas ir pilnībā pieejamas; aparatūras ierobežojumi katram plānam, kas bezmaksas (bez pieteikšanās) ļauj izmantot līdz 4 kamerām un 2 gaismas sensoriem.
* **Uzticamība.** Funkcija „Stop Processing” tiek pārtraukta pareizi, sniedzot precīzu izpildes kopsavilkumu; daudzkameru projektos tiek eksportēti visi kameru dati, un instalētāja atjauninājumi vairs neizraksta jūs no sistēmas.***

Chloros
ir pieejams 3 lietojumvides versijās:

## Chloros
: darbvirsmas lietojumprogramma ar grafisko lietotāja saskarni

Atsevišķs logs ar visām funkcijām, ieskaitot cilnes „Kameras reāllaikā” un „Gaismas sensori”. _Tikai Windows._

## [Chloros
CLI
: komandrindas saskarne](CLI.md)

Komandrindas pakotņu apstrāde, kā arī reāllaika komandas `lattice`, `daq pool-*`, `project` un `time-sync`. Ideāli piemērota automatizācijai, skriptu izstrādei un darbībai bez grafiskās saskarnes. Pieejama **Windows
, Linux
amd64 un Linux
arm64 (NVIDIA Jetson)**. _Lai piekļūtu CLI
, nepieciešams maksas Chloros
+ pakalpojuma līmenis._

## [Chloros
API
: Python
SDK
](api-python-sdk.md)

Programmatiska Python
saskarne automatizācijai un pielāgotām darba plūsmām: pilna procesa apstrāde, kameru/matricas sesijas reāllaikā, DAQ sensoru sesijas un saglabāto projektu automatizācija. Tiek instalēts kopā ar darbvirsmas/CLI paketi un publicēts arī kā `pip install chloros-sdk`. _Lai piekļūtu API, ir nepieciešams maksas Chloros
+ līmenis._

***

## Atbalstītās platformas

| Platforma | GUI | CLI
| Python
SDK
|
| --- | --- | --- | --- |
| **Windows
10/11 (x64)** | Jā | Jā | Jā |
| **Linux
amd64 (x86_64)** | Nē | Jā | Jā |
| **Linux
arm64 (NVIDIA Jetson)** | Nē | Jā | Jā |

Linux
instalēšanas instrukcijas skatiet sadaļā [Linux
un Edge Computing](linux/linux-overview.md).

***

## Sāciet trīs soļos

1. **Instalēšana** — lejupielādējiet un palaidiet instalētāju savai platformai. Skatiet [Lejupielāde](download.md).
2. **Piesakieties (nav obligāti, ja izmantojat GUI)** — GUI apstrādā attēlus bez maksas, neizmantojot kontu. [Chloros
+ pieteikšanās](chloros+-login.md) atbloķē paralēlo apstrādi, GPU paātrinājumu, augstākus ierīču limitus un piekļuvi CLI
un SDK
.
3. **Izveidojiet savu pirmo projektu** — atveriet Chloros
, izveidojiet [Jaunu projektu](projects.md), [pievienojiet savus attēlus](processing-images-gui/adding-files-to-a-project.md) un [sāciet apstrādi](processing-images-gui/starting-the-processing.md). Lai vietā vadītu reālu aparatūru, atveriet cilni „Kameras” vai „Gaismas sensori” — skatiet [GUI: navigācija](navigation.md).

***

## „Chloros
+”

Lai gan Chloros
lielākajai daļai uzdevumu ir bezmaksas, iespējams, jūs vēlēsieties vairāk. Tādā gadījumā jums noderēs maksas licence „Chloros
+”. Ar „Chloros
+” licenci jūs varat atbloķēt jaunas funkcijas, piemēram:

* **Daudzpavedienu apstrāde**: ievērojami paātriniet attēlu apstrādi lielākiem projektiem, vienlaikus apstrādājot attēlus caur apstrādes plūsmu.
* **GPU (CUDA) paātrinājums**: izmantojiet mūsdienu GPU atmiņas iespējas, lai vēl vairāk paātrinātu attēlu apstrādes plūsmu. Labāko rezultātu sasniegšanai ieteicams izmantot 4 GB vai vairāk VRAM.
* **Chloros
+**[**CLI**](CLI.md) **Piekļuve**: palaidiet Chloros
+ no komandrindas, lai automatizētu un integrētu savā programmnodrošinājumā. Pieejams jebkurā maksas līmenī; tiek īstenots servera pusē.
* **Chloros
+**[**API**](api-python-sdk.md) **Piekļuve:** palaidiet Chloros
+ no Python
, lai nodrošinātu programmatisku kontroli, kas ļauj vienkārši integrēt to jūsu pētniecības procesā, datu analīzes darba plūsmās un pielāgotās lietojumprogrammās. Pieejams jebkurā maksas līmenī; tiek īstenots servera pusē.
* **Augstāki aparatūras ierobežojumi**: vienlaikus pieslēdziet vairāk kameru un gaismas sensoru. Bez pieteikšanās grafiskā lietotāja saskarne (GUI) atbalsta līdz 4 kamerām un 2 DAQ gaismas sensoriem; maksas plāni palielina abus ierobežojumus:

| Plāns | Kameras | DAQ gaismas sensori |
| --- | --- | --- |
| Iron (bezmaksas, bez pieteikšanās) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Vairāku ierīču izmantošana**: katra „Chloros
+“ licence ļauj reģistrēt 2 vai vairāk ierīču. Izmantojiet savu „MAPIR
Cloud“ kontu, lai pārvaldītu reģistrētās ierīces. Paplašiniet atbalstu vairākām ierīcēm, uzlabojot savu „Chloros
+“ licenci.
* **Uzlabota tekstūru ņemšana vērā debayerēšanas metode:** augstas kvalitātes, malu ņemšana vērā debayerēšanas metode, apvienota ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayerēšanas radītos trokšņus.
* **Pielāgotas multispektrālo indeksu formulas:** ievadiet pielāgotus multispektrālos indeksus Chloros
rastra kalkulatoros gan apstrādei, gan attēlu apskates testēšanas vidē.
* **Linux
un malu aprēķini:** palaidiet Chloros
uz Linux
x86\_64 un ARM64 platformām, tostarp NVIDIA Jetson, lai veiktu apstrādi laukā un malās. Skatīt [Linux
pārskatu](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ cenas un reģistrācija</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.3.0 build so the banner prints "Chloros CLI 1.3.0". -->
