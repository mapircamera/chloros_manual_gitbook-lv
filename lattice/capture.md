# Uzņemšanas iestatījumi un režīmi

Uzņemšana cilnē „Kameras” tiek vadīta ar vienu sarkanu pogu **„Capture All”**un vienu paneļu**„Capture Settings”**, kurā tiek noteikts, ko šī poga izraisa: kuras kameras tiek iesaistītas, kādus eksporta veidus katra kamera saglabā un vai aizslēgs tiek iedarbināts vienreiz, nepārtraukti vai ar noteiktu intervālu. Šajā lapā ir aprakstīts viss process — konfigurācija, pati uzņemšana, kur faili tiek saglabāti diskā un kā tos vēlāk pārstrādāt, lai iegūtu kalibrētus rezultātus. Paši kameru un masīvu vadības elementi atrodas sadaļā [Kameru iestatījumi](camera-settings.md).

{% hint style="info" %}
**Uzņemšanai ir nepieciešams atvērts projekts.** Poga „Uzņemt visu” un rīks „Uzņemšanas iestatījumi” ir atspējoti, kamēr nav atvērts projekts („Izveidojiet vai atveriet projektu, lai saglabātu uzņēmumus”). Katrs uzņēmums tiek saglabāts projekta mapē `captures/`.
{% endhint %}

## Ierakstīšanas iestatījumu logs

To var atvērt, izmantojot **zobratu blakus opcijai „Ierakstīt visu”**kameru sarakstā sānjoslā vai ar pogu**„Atvērt ierakstīšanas iestatījumus…”** jebkura atsevišķas kameras iestatījumu loga apakšā. Virsrakstā ir redzams uzraksts „Ieraksta iestatījumi” ar pogu ← atpakaļ.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->

Jūsu izvēles šeit — iekļautās kameras, atzīmes atbilstoši tipam un ieraksta režīms — tiek saglabātas **katram projektam atsevišķi** un atjaunotas, kad projektu atverat no jauna.

### Uzņemšanas režīmi

Trīs režīmu pogas paneļa augšdaļā:

| Režīms | Kā tas darbojas | Papildu iestatījumi (noklusējumi) |
| --- | --- | --- |
| **Vienreizējs** *(noklusējums)* | Viena uzņemšana ar katru izvēlēto kameru. | — |
| **Nepārtraukts**| Nepārtraukta uzņemšana līdz apstāšanās nosacījumam. | Apstāšanās pēc**uzņemto attēlu skaita** (noklusējums 1) *vai* **uzņemšanas ilguma** (noklusējums 10 s; vienības: sekundes / minūtes / stundas / dienas). |
| **Intervāls**(laika nobīde) | Sērijveida uzņemšana pēc taimera. |**Uzņēmumu skaits / intervāls**(noklusējums 1) ·**Ik pēc**N vienībām (noklusējums 5 s) ·**Ilgums** N vienības (noklusējums 1 m). |

Nepārtrauktā vai intervāla režīmā pogai „Uztvert visu” darbības laikā mainās nosaukums uz **„Pārtraukt (N)”**, skaitot uzņēmumus, tiklīdz tie tiek saglabāti.

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->

### Kameru un eksporta veidu izvēle

Logā redzamais palīdzības teksts to apkopojis: izvēlieties, kuras kameras un eksporta veidus izmanto funkcija „Capture All“ — pēc noklusējuma viss ir ieslēgts, un izvēle tiek saglabāta kopā ar šo projektu.

* **„Izvēlēties visus“ / „Neizvēlēties nevienu“** pogas vienlaikus maina visu kameru iekļaušanas izvēles rūtiņu stāvokli.
* **Masveida eksporta veidu slēdži**(divas pogu rindas):**Visi neapstrādāti / Visi bez bayera filtra / Visi priekšskatījumi / Visi starojuma / Visi atstarošanas / Visi indeksa**. Katram no tiem ir trīs stāvokļu krāsojums: zaļš ✓ = ieslēgts visām kamerām, kas to atbalsta, dzeltens – = ieslēgts dažām, pelēks = nevienai. Pārslēdzējs ir atspējots, ja neviena no pieslēgtām kamerām neatbalsta šo tipu. Visi tie kļūst pelēki, kamēr ir ieslēgta funkcija „Fastest Capture”.
* **Rindas katrai kamerai**: izvēles rūtiņa „iekļaut”, kā arī izvēršams (▸/▾) saraksts ar šīs kameras piemērojamiem eksporta veidiem un atsevišķām izvēles rūtiņām. Rindā tiek parādīts ieslēgto skaits, piemēram, „4/6”.

### Eksporta veidi un kameras, kurām tie ir pieejami

Ir seši eksporta veidi: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Katras kameras rindā parādās tikai piemērojamie veidi:

| Eksporta veids | Saturs | RGB (FRGB) | Bayer multispektrālais (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Bayer mozaīka (mono: viena josla) tieši no sensora | ✓ | ✓ | ✓ |
| **Debayered** | Lineārā demosaika (mono: 1 kanāla pelēktoņu skala) | ✓ | ✓ | ✓ |
| **Priekšskatījums** | Pilna attēla apstrādes ķēde (balansa iestatīšana + gamma saskaņā ar kameras profilu; multispektrāls: viltus krāsu izstiepšana) | ✓ | ✓ | ✓ |
| **Starojums** | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi | — (nav pieejams) | ✓ | ✓ |
| **Atstarošanās** | uint16 ρ (32768 = 1,0) | — (nav pieejams) | ✓ — tiek parādīts tikai tad, ja kamerai ir DAQ gaismas sensors (savs vai mantots no masīva) | tāpat kā multispektrālajā režīmā |
| **Indekss** | Veģetācijas indeksa (LUT) attēlojums | — | ✓ — prasa, lai kamerā būtu ieslēgts, ne tukšs indeksa izteiksme, un netiek piedāvāts kombinēto matricas locekļiem (matricai pieder viens kopīgs indekss) | — (indeksam nepieciešamas ≥2 joslas; skatīt [Mono kameras un veģetācijas indeksi](mono-indices.md)) |

Starpība un atstarojums nekad netiek piedāvāti RGB kamerām — starpība katram Bayer elementam nav nozīmīga platjoslas fotometriskajam sensoram.

### Ātrākā uzņemšana

Pārslēdzējs **⚡ Ātrākā uzņemšana — tikai neapstrādāti dati**(ieslēgts — oranžā krāsā) pārraksta visas eksporta izvēles uz**tikai neapstrādātiem datiem** — kā arī bezmaksas kombinēto indeksu kompozīciju masīviem — lai kadrs tiktu saglabāts pēc iespējas ātrāk: starojuma/atstarošanas/attēlošanas aprēķini uzņemšanas brīdī tiek pilnībā izlaisti.

{% hint style="info" %}
**`.daq` joprojām tiek saglabāts.** Ja ir piešķirts gaismas sensors, „Ātrākā uzņemšana” joprojām pieraksta DAQ lejupvērsto rādījumu blakus neapstrādātajiem kadriem — tādējādi starojuma, atstarošanas un indeksa produktus vēlāk var izveidot, veicot atkārtotu apstrādi (skatīt [Uzņemto attēlu atkārtota apstrāde](#re-processing-captures-into-calibrated-products)). Programma „Fastest Capture” arī neietekmē jūsu izvēlēto izvēles rūtiņu iestatījumus: izslēdziet to, un tie atgriezīsies.
{% endhint %}

### Vadības elementi katram masīvam

Katram pieslēgtajam masīvam paneļā ir sava grupas karte:

* **Izvēles rūtiņa „Iekļaut”** (trīs stāvokļi visiem elementiem) un masīva nosaukums ar tā attēlošanas režīmu: „(apvienots | atsevišķs)”.
* **„Aligned“**izvēles rūtiņa (noklusējumā**ieslēgta**): deformē elementu eksportus atbilstoši masīva izlīdzināšanas profilam, lai eksporti būtu pikseļu līmenī saskaņoti starp kamerām. Neapstrādātie dati paliek neizkropļoti, bet to metadatos tiek saglabāta transformācija. (Pats profils tiek aprēķināts [masīva iestatījumu paneļā](camera-settings.md#alignment-co-registration-combined-only).)
* Elementu kameru rindas ir ievietotas kartē.

Masīva kartē atrodas arī divi ierakstītāji. Uztveriet tos kā **uzraudzību pret analīzi**:

| Ierakstītājs | Līmenis | Ko tas ieraksta |
| --- | --- | --- |
| **● Ierakstīt indeksa video / ■ Pārtraukt ierakstīšanu** *(tikai kombinētajām masīvām)* | **Uzraudzība** | Kombinētā indeksa kompozīcija reāllaikā, kas tiek pārveidota video ar 10 kadriem sekundē — 8 bitu, priekšskatīšanas izšķirtspēja, iebūvēta LUT. Nepieciešams atvērts projekts un straumēts reāllaika skats. Rāda kadrus un pagājušo laiku ierakstīšanas laikā. |
| **⦿ Ierakstīt neapstrādātu sēriju / ■ Pārtraukt neapstrādātu sēriju** *(jebkura matrica)* | **Analīze**| Neapstrādāti Bayer kadri ar reāllaika uzņemšanas ātrumu (bez apstrādes), kā arī katra kadra manifests un `.daq` rādījumi, kas tiek saglabāti kā `captures/bursts/`. Pēc sērijas uzņemšanas parādās poga**Izveidot video**: tā sēriju pārstrādā bezsaistē, izveidojot kalibrētu video — apvienoto indeksu un/vai katras kameras starojuma / atstarošanas / indeksa datus — kā arī papildu TIFF failus pēc izvēles. Apvienotā indeksa izveide sākas automātiski, kad apturat sērijas uzņemšanu. |##

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

„Capture All” (Uzņemt visu) darbplūsma

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->

Nospiediet **„Capture All”** kameru sarakstā sānjoslā:

1. Visas iekļautās, redzamās un nepauzētās kameras veic uzņemšanu ar izvēlētajiem eksporta veidiem. **Masīvi darbojas kā viens sinhronizēts izraisītājs** (viena sinhronizēta grupa visiem locekļiem — skatiet [Daudzkameru masīvi](arrays.md)); atsevišķas kameras veic uzņemšanu individuāli.
2. Paslēptās (acīm) vai apturētās kameras tiek izlaistas. Masīvs tiek pilnībā bloķēts tikai tad, ja *visi* tā locekļi ir paslēpti vai apturēti.
3. Ja ir piešķirts gaismas sensors, atbilstošais DAQ lejupvērstais rādījums tiek saglabāts kā `.daq` fails kopā ar attēliem — pat attēliem, kas uzņemti tikai neapstrādātā formātā —, lai vēlāk vienmēr varētu iegūt radiometriskos produktus.
4. Poga parāda reāllaika progresu — „Uzņemšana… pabeigta/kopā” — un nepārtrauktajā/intervāla režīmā tā kļūst par **Stop (N)**. Katram uzņemšanas elementam ir 300 s laika ierobežojums.
5. Kad uzņemšanas cikls ir pabeigts, rezultātu logā parādās ziņojums **„Saglabāti N faili”**vai**„Saglabāti N, F neizdevās”**, kā arī „(S paslēpts/pauzēts/izlaists)”, ja kameras tika izlaistas.

## Kur tiek saglabāti uzņemumi

Uzņemumi tiek saglabāti atvērtā projektā ar nosaukumu `<project>/captures/`. Katrs eksporta veids tiek saglabāts **savā apakšmapē**, tādējādi daudzlīmeņu uzņemumos veidi nekad nesajaucas:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── *.daq          the downwelling reading matched to the capture
```

* `<ts>` ir uzņemuma laika zīmogs, bet `<serial>` — kameras sērijas numurs. Atsevišķi uzņemumi tiek nosaukti par `capture_<ts>_SN<serial>_<level>`; masīva uzņemumi no viena sinhronizēta izraisītāja tiek nosaukti par `sync_<ts>_SN<serial>_<level>` un **visām grupas kamerām ir kopīgs viens laika zīmogs** (līmeņa piedēklis tiek izlaists, ja kamera saglabā tikai vienu līmeni).
* **Viena asimetrija, kas jāzina:** attēlošanas līmenis tiek saglabāts mapē ar nosaukumu `preview/`, savukārt failu nosaukumos paliek `_display` — mape un piedēklis atšķiras tikai šim līmenim.
* Nezināmi līmeņi tiek saglabāti mapē ar savu nosaukumu; ja apakšmapes izveide nav iespējama, fails tiek ierakstīts uzņemto attēlu saknes mapē, nevis tiek zaudēts.
* Capture TIFF faili pēc noklusējuma tiek saspiesti bez datu zuduma (DEFLATE) un to pilnīgie kalibrēšanas un apstrādes metadati tiek saglabāti **faila XMP iekšienē** — uzņemtie attēli ir pašraksturojoši, un tiem nav papildu failu, izņemot `.daq`.

Šis ir tas pats izkārtojums, kādu `chloros-cli lattice capture` / `array-capture` ieraksta savā `-o` direktorijā — dokumentēts [CLI atsauces sadaļā „Kā izskatās uzņemto attēlu mape”](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, a .daq file at the root, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Uzņemto attēlu pārstrāde kalibrētos produktos

Uztvertie neapstrādātie kadri un saglabātie `.daq` faili ir viss, kas nepieciešams apstrādes procesam — tieši tāpēc „Fastest Capture” ir droši izmantojams reālajā darbā.

* **GUI**: pievienojiet uzņemto attēlu mapīti projektam ([Failu pievienošana projektam](../processing-images-gui/adding-files-to-a-project.md)) un apstrādājiet kā parasti.
* **CLI**: norādiet `process` uz**uzņemto attēlu saknes mapi**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` parasti importē tikai norādīto mapi, bet, ja tajā nav attēlu un ir apakšmapes, tas automātiski pārskata apakšmapes — tādējādi vienā reizē tiek iekļautas gan līmeņu apakšmapes, gan saknes `.daq` faili. Katra uzņemšana tiek importēta kā **viens attēls**, kam pārējie līmeņi tiek pievienoti kā apskatāmi režīmi, nevis kā atsevišķs attēls katram līmenim.

Līmeņa apakšmapes tieša nosaukšana (piem., `…/captures/raw/`) arī darbojas, bet galvenie `.daq` faili paliek neiekļauti — kopējiet tos vienlaikus, kad no `raw/` atkārtoti izveidojat radiometrisko produktu, citādi laika zīmju saskaņošanai nebūs ar ko salīdzināt.

{% hint style="warning" %}
**Apstrāde vienmēr sākas no `raw`.**Katrā uzņemšanā neapstrādātais kadrs ir apstrādes procesa avots; `debayered`, `radiance`, `reflectance` un `preview` ir pieejami kā apskatāmi režīmi, bet nekad netiek atgriezti apstrādes procesā — atvasinātā produkta atkārtota apstrāde nozīmētu atkārtoti piemērot vinjetēšanu, krāsu un starojuma aprēķinus, kas jau ir iestrādāti tā pikseļos, tāpēc Chloros tiek noraidīts, nevis apstrādāts divreiz. Renderējumi `index/` un `composite/` netiek apstrādāti vispār (tie ir izvades faili, nevis uzņemumi). „Captures” mape, kas saglabāta**bez** neapstrādātu failu importēšanas, tiek parādīta normāli, taču `process` to izlaiž un par to paziņo; `--input-level {raw,debayered,processed}` ir apzināti izveidota „glābšanas lūka”, kas piespiež izmantot sākuma punktu. Precīzus izlaišanas ziņojumus skatiet [CLI atsaucē](../reference/cli-reference.md#what-a-captures-folder-looks-like).
{% endhint %}

Vēl divas parādības, kas ir vērts zināt, veidojot skriptus atkārtotai apstrādei:

* `chloros-cli process` izpilde, kas pieprasīja produktus, bet **nerakstīja attēlu produktus,**beidzas ar kļūdu un iziet ar rezultātu, kas nav nulle** — jūs nekad nesaņemsiet klusu tukšu izpildi. Veiksmīgas izpildes ziņo par savu produktu skaitu. (Apzināta izpilde, kurā tiek apstrādāti tikai metadati, joprojām tiek uzskatīta par veiksmīgu.)
* Atkārtoti importēti apstrādāti eksporti nekad neaizņem uzņemuma neapstrādātās datu vietas — sākotnējie neapstrādātie dati vienmēr paliek apstrādes procesa avots.

## CLI ekvivalenti

Viss šajā lapā var tikt vadīts bez grafiskās saskarnes. GUI uzņemšanas režīmi tieši atbilst `chloros-cli lattice array-capture`:

| GUI | CLI |
| --- | --- |
| Vienreizējs | `chloros-cli lattice array-capture` |
| Nepārtraukts | `array-capture --continuous [--count N] [--duration S]` |
| Intervāls | `array-capture --interval S [--duration S]` |
| Ātrākā uzņemšana | `array-capture --fastest` |
| Izlīdzināšanas izvēles rūtiņa | `--aligned / --no-aligned` |
| Eksporta tipa izvēles rūtiņas | `--processing LEVEL` vai `--levels L1,L2,…` (noklusējums `all`) |
| Video ar indeksu | `chloros-cli lattice array-record` |
| Neapstrādātu attēlu sērijas ierakstīšana / Video veidošana | `chloros-cli lattice array-burst` / `array-build-video` |

Pilnās karogu tabulas, viedā automātiskā ekspozīcija (`--smart`) un pastāvīgas ātruma režīms atrodas sadaļā [CLI Atsauce § Uzņemšanas režīmi, ierakstītāji un pārstrāde bezsaistē](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
