# Uzņemšanas iestatījumi un režīmi

Uzņemšana cilnē „Kameras” tiek vadīta ar vienu sarkanu pogu **„Capture All”**un vienu logu**„Capture Settings”**, kurā tiek noteikts, kādu rezultātu šī poga rada: kuras kameras tiek iesaistītas, kādus eksporta veidus katra kamera saglabā un vai aizslēgs tiek iedarbināts vienreiz, nepārtraukti vai ar intervālu. Šajā lapā ir aprakstīts viss process — konfigurācija, pati uzņemšana, failu saglabāšanas vieta diskā un to, kā tos vēlāk pārstrādāt par kalibrētiem produktiem. Paši kameru un masīvu vadības elementi atrodas sadaļā [Kameru iestatījumi](camera-settings.md).

{% hint style="info" %}
**Uzņemšanai ir nepieciešams atvērts projekts.** Funkcijas „Capture All” un „Capture Settings” ir atspējotas, kamēr nav atvērts projekts („Izveidojiet vai atveriet projektu, lai saglabātu uzņēmumus”). Katrs uzņēmums tiek saglabāts projekta mapē `captures/`.
{% endhint %}

## Ierakstīšanas iestatījumu logs

To var atvērt, izmantojot **zobratu blakus „Ierakstīt visu”**kameru sarakstā sānjoslā vai ar pogu**„Atvērt ierakstīšanas iestatījumus…”** jebkura atsevišķas kameras iestatījumu loga apakšā. Virsraksts ir „Ierakstīšanas iestatījumi” ar atpakaļ pogu ←.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->



Jūsu izvēles šeit — iekļautās kameras, atzīmju rūtiņas katram tipam un uzņemšanas režīms — tiek saglabātas **katram projektam atsevišķi** un atjaunotas, kad projektu atverat no jauna.

### Uzņemšanas režīmi

Trīs režīmu pogas paneļa augšdaļā:

| Režīms | Funkcija | Papildu iestatījumi (noklusējumi) |
| --- | --- | --- |
| **Vienreizēja** *(noklusējums)* | Viena uzņemšana ar visām izvēlētajām kamerām. | — |
| **Nepārtraukta**| Uzņemšana secīgi, līdz tiek sasniegts apturēšanas nosacījums. | Pārtraukšana pēc**uzņemto attēlu skaita** (noklusējums 1) *vai* **uzņemšanas ilguma** (noklusējums 10 s; vienības: sekundes / minūtes / stundas / dienas). |
| **Intervāls**(laika nobīde) | Sērijveida uzņemšana pēc taimera. |**Uzņemto attēlu skaits / intervāls**(noklusējums 1) ·**Ik pēc**N vienībām (noklusējums 5 s) ·**Ilgst** N vienības (noklusējums 1 m). |

Nepārtrauktajā vai Intervāla režīmā pogai „Uztvert visu” darbības laikā mainās nosaukums uz **Pārtraukt (N)**, skaitot uzņēmumus, tiklīdz tie tiek veikti. Pilna diska piepildīšanās aptur darbību (skatīt [„Capture All” darbības gaitu](#the-capture-all-flow)).**Nepārtrauktais režīms masīvos darbojas backendā (jaunums versijā 1.3.0).** Ja visas sarakstā iekļautās kameras pieder masīvam (atsevišķās kameras ir paslēptas vai izslēgtas), nepārtrauktais režīms vairs nesūta vienu pieprasījumu par katru uzņemšanu: Chloros aizmugurējā sistēma pati izpilda uzņemšanas ciklu, ierakstot vienu sinhronizētu grupu, kamēr izvēlas nākamo, un poga noskaidro to skaitu. Ja sarakstā ir atsevišķas kameras, Chloros veic uzņemšanu pa posmiem, kā iepriekš.

{% hint style="warning" %}
**Ātra uzņemšana Windows vidē: izslēdziet Chloros a backend no Microsoft Defender skenēšanas.** Reāllaika skenēšana katram failam, ko raksta ātrdarbīga uzņemšana, MAPIR testos aizņem apmēram 30 % no uzņemšanas ātruma (simtiem failu sekundē nelielā ROI). Windows instalētājs piedāvā opciju **„Izslēgt Chloros backend no „Microsoft Defender” skenēšanas (daudz ātrāka kameras uzņemšana; ieteicams)”**, kas pēc noklusējuma ir atzīmēta, un atinstalēšanas laikā šo izslēgšanu atceļ. Ja esat atspējojis šo opciju, palaidiet instalētāju no jauna ar to atzīmētu vai pievienojiet Defender procesa izņēmumu `chloros-backend.exe` (Chloros instalācijas mapes `backend` mapē). Kad „Defender” izmanto vienu vai vairākus procesora kodolus masīva nepārtrauktas uzņemšanas laikā, Chloros backend to vienreiz atzīmē savā žurnālā, norādot šos pašus divus risinājumus.
{% endhint %}

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->




### Kameru un eksporta veidu izvēle

Logā redzamais palīdzības teksts to apkopojis: izvēlieties, kuras kameras un eksporta veidus ģenerē opcija „Capture All“ — pēc noklusējuma viss ir ieslēgts, un izvēles tiek saglabātas kopā ar šo projektu.

* **„Izvēlēties visus” / „Neizvēlēties nevienu”** pogas vienlaikus maina katras kameras iekļaušanas atzīmes stāvokli.
* **Masveida eksporta veidu slēdži**(divas pogu rindas):**Visi Raw / Visi Debayered / Visi Preview / Visi Radiance / Visi Reflectance / Visi Index**. Katram no tiem ir trīs stāvokļi: zaļš ✓ = ieslēgts visām kamerām, kas to atbalsta, dzintara krāsā – = ieslēgts dažām, pelēks = nevienai. Pārslēgšanas poga ir atspējota, ja neviena pieslēgtā kamera neatbalsta šo veidu. Visas pogas kļūst pelēkas, kamēr ir ieslēgta funkcija „Ātrākā uzņemšana”.
* **Rindas katrai kamerai**: iekļaušanas izvēles rūtiņa, kā arī izvēršams (▸/▾) saraksts ar šīs kameras piemērojamiem eksporta veidiem, kuriem ir atsevišķas izvēles rūtiņas. Rindā tiek parādīts ieslēgto skaits, piemēram, „4/6”.

### Eksporta veidi un kameras, kurās tie ir pieejami

Ir seši eksporta veidi: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Katras kameras rindā tiek parādīti tikai piemērojamie veidi:

| Eksporta veids | Saturs | RGB (FRGB) | Bayer multispektrālais (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Bayer mozaīka (mono: viena josla) tieši no sensora | ✓ | ✓ | ✓ |
| **Debayered** | Lineārā demosaika: no neapstrādātā attēla noņemts kalibrēšanas paketes tumšais nobīde (DSNU), pēc tam veikta malu ņemšana vērā demosaika — bez linearitātes korekcijas, līdzena lauka vai krāsu atdalīšanas. Saglabāts kā 3-kanālu RGB TIFF (mono: 1-kanālu pelēktoņu) | ✓ | ✓ | ✓ |
| **Priekšskatījums** | Pilna attēla apstrādes ķēde (balansa un gamma korekcija saskaņā ar kameras profilu, kā arī RGB kameras iestatījums „Highlights“; multispektrālais: viltus krāsu izstiepšana) | ✓ | ✓ | ✓ |
| **Starojuma intensitāte** | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi | — (nav pieejams) | ✓ | ✓ |
| **Atstarošanās** | uint16 ρ (32768 = 1,0) | — (nav pieejams) | ✓ — tiek parādīts tikai tad, ja kamerai ir DAQ gaismas sensors (pašas vai mantots no masīva) | tāpat kā multispektrālajam |
| **Indekss** | Veģetācijas indeksa (LUT) attēlojums | — | ✓ — prasa, lai kamerā būtu ieslēgts, ne tukšs indeksa izteiksme, un netiek piedāvāts kombinēto matricas locekļiem (matricai pieder viens kopīgs indekss) | — (indeksam nepieciešamas ≥2 joslas; skatīt [Mono kameras un veģetācijas indeksi](mono-indices.md)) |

RGB kamerām nekad netiek piedāvāts starojums un atstarošanās — starojums uz vienu Bayer elementu nav nozīmīgs platjoslas fotometriskajam sensoram.

### Ātrākā uzņemšana

Pārslēdzējs **⚡ Ātrākā uzņemšana — tikai RAW**(ieslēgts — oranžs) pārraksta visas eksporta izvēles uz**tikai RAW** — kā arī bezmaksas kombinēto indeksu kompozīciju masīviem — tādējādi kadrs tiek saglabāts pēc iespējas ātrāk: starojuma/atstarošanas/attēlošanas aprēķini uzņemšanas brīdī tiek pilnībā izlaisti.

{% hint style="info" %}
**`.daq` joprojām tiek saglabāts.** Ja ir piešķirts gaismas sensors, funkcija „Ātrākā uzņemšana” joprojām ieraksta DAQ lejupvērsto starojuma rādījumu (uzņemumu mapē „`daq/`”) — tādējādi starojuma, atstarošanas un indeksa produktus var izveidot vēlāk, veicot atkārtotu apstrādi (sk. [Ierakstu atkārtota apstrāde](#re-processing-captures-into-calibrated-products)). Funkcija „Fastest Capture” arī neietekmē jūsu izvēles atzīmju stāvokli: izslēdziet to, un tās atgriezīsies.
{% endhint %}

### Vadības elementi katram masīvam

Katram pieslēgtam masīvam paneļā ir sava grupas karte:

* **Izvēles rūtiņa „Iekļaut”** (trīs stāvokļi visiem elementiem) un masīva nosaukums ar tā attēlošanas režīmu: „(apvienots | atsevišķs)”.
* **„Aligned“**izvēles rūtiņa (noklusējumā**ieslēgta**): deformē elementu eksportus atbilstoši masīva izlīdzināšanas profilam, lai eksporti būtu pikseļu līmenī saskaņoti starp kamerām. Neapstrādātie dati paliek neizkropļoti, bet to metadatos tiek saglabāta transformācija. (Pats profils tiek aprēķināts [masīva iestatījumu paneļā](camera-settings.md#alignment-co-registration).) Masīva elementu faili tiek saglabāti katras kameras sākotnējā orientācijā, un jebkādi spoguļošanas/rotācijas iestatījumi tiek ierakstīti failā, nevis piemēroti.
* Elementu kameru rindas ir ievietotas kartē.

Masīva kartē atrodas arī divi ierakstītāji. Uztveriet tos kā **uzraudzību pret analīzi**:

| Ierakstītājs | Līmenis | Ko tas ieraksta |
| --- | --- | --- |
| **● Ierakstīt indeksa video / ■ Pārtraukt ierakstīšanu** *(tikai kombinētiem masīviem)* | **Uzraudzība** | Kombinētā indeksa kompozīcija reāllaikā video formātā ar 10 kadriem sekundē — 8 bitu, priekšskatīšanas izšķirtspēja, iebūvēta LUT. Nepieciešams atvērts projekts un tiešraides skats. Ierakstīšanas laikā parāda kadrus un pagājušo laiku. |
| **⦿ Ierakstīt neapstrādātu sēriju / ■ Pārtraukt neapstrādātu sēriju** *(jebkurš masīvs)* | **Analīze**| Neapstrādāti Bayer kadri ar reāllaika uzņemšanas ātrumu (bez apstrādes), kā arī katra kadra manifests un `.daq` rādījumi, kas tiek saglabāti `captures/bursts/` formātā. Pēc sērijas uzņemšanas parādās poga**Izveidot video**: tā sēriju pārstrādā bezsaistē, izveidojot kalibrētu video — kombinēto indeksu un/vai katras kameras starojuma / atstarošanas / indeksa datus — kā arī papildu TIFF failus pēc izvēles. Kombinētā indeksa izveide sākas automātiski, kad apturat sērijas uzņemšanu. |

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

## „Capture All” (Uzņemt visu) darbplūsma

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->



Nospiediet **„Capture All”** kameru sarakstā sānjoslā:

1. Katra iekļautā, redzamā un nepauzētā kamera veic uzņemšanu ar izvēlētajiem eksporta veidiem. **Masīvi darbojas kā viens sinhronizēts trigers** (viena sinhronizēta grupa visiem locekļiem — skatīt [Daudzkameru masīvi](arrays.md)); atsevišķas kameras veic uzņemšanu individuāli.
2. Slēptās (acis) vai apturētās kameras tiek izlaistas. Masīvs tiek pilnībā bloķēts tikai tad, ja *visi* tā locekļi ir slēpti vai apturēti.
3. Ja ir piešķirts gaismas sensors, atbilstošais DAQ lejupvērstais rādījums tiek saglabāts kā `.daq` fails uzņemumu mapē `daq/` — pat tad, ja uzņemumi ir tikai neapstrādātā formātā —, lai vēlāk vienmēr varētu iegūt radiometriskos produktus.
4. Poga parāda reāllaika progresu — „Uztveršana… pabeigta/kopā” — un nepārtrauktā/intervāla režīmā tā kļūst par **Stop (N)**. Katram uztveršanas elementam ir 300 s laika ierobežojums.
5. Kad uzņemšanas cikls ir pabeigts, rezultātu logā parādās ziņojums **„Saglabāti N faili”**vai**„Saglabāti N, F neizdevās”**, kā arī „(S paslēpts/pauzēts/izlaists)”, ja kameras tika izlaistas. Ja disks piepildās, darbība tiek apturēta un tiek ziņots**„Apturēts: disks pilns. Saglabāti N, F neizdevās”** — katrs fails, kuru neizdevās ierakstīt, tiek uzskatīts par neizdevies.

## Kur tiek saglabāti ieraksti

Ieraksti tiek saglabāti atvērtā projektā mapē `<project>/captures/`. Katrs eksporta veids tiek saglabāts **savā apakšmapē**, tādējādi daudzlīmeņu ierakstā veidi nekad nesajaucas:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_clampmask.tif, reflectance/…, index/… — unmix clamp masks,
│                  one subfolder per product folder, only when a band was clamped
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── daq/           the downwelling reading(s) matched to each capture (.daq), each with a
                   .daq.cameras.json naming the cameras it describes
```

* `<ts>` ir uzņemuma laika zīmogs, bet `<serial>` — kameras sērijas numurs. Atsevišķi uzņemumi tiek nosaukti par `capture_<ts>_SN<serial>_<level>`; masīva uzņemumi no viena sinhronizēta izraisītāja tiek nosaukti par `sync_<ts>_SN<serial>_<level>` un **visām grupas kamerām ir viens kopīgs laika zīmogs** (līmeņa piedēklis tiek izlaists, ja kamera saglabā tikai vienu līmeni).
* **Viena asimetrija, par kuru jāzina:** attēlošanas līmenis tiek saglabāts mapē ar nosaukumu `preview/`, savukārt failu nosaukumos paliek `_display` — mape un piedēklis atšķiras tikai šim līmenim.
* Nezināmi līmeņi tiek saglabāti mapē ar savu nosaukumu; ja apakšmapes nevar izveidot, fails tiek ierakstīts uzņemto attēlu saknes mapē, nevis pazūd.
* Uzņemtie TIFF faili pēc noklusējuma tiek saspiesti bez zudumiem (DEFLATE) un satur pilnus kalibrēšanas un apstrādes metadatus **faila XMP iekšienē** — uzņēmumi ir pašaprakstoši, bez papildu failiem, izņemot `.daq` lasījumu (un tā `.daq.cameras.json`).
* **Ierobežošanas maskas (jaunums versijā 1.3.0).**Gadījumos, kad 3×3 spektrālajai atdalīšanai bija jāierobežo josla līdz 0 vismaz vienā pikselī, starojuma, atstarošanas un indeksa produktiem tiek piešķirta maska katram pikselim atsevišķi, `clamp_masks/<product folder>/<product name>_clampmask.tif` (bits 0 = josla produkta R kanālā, bits 1 = G, bits 2 = B); uz to norāda produkta `Chloros:UnmixClampMask` XMP tag. Maski atrodas**blakus** produktu mapēm, nekad to iekšienē, tādēļ produktu mapi, piemēram, `reflectance/`, var nodot fotogrammetrijas programmatūrai tādu, kāda tā ir. „Chloros” paša importēšanas funkcija izlaiž maskas.
* **Orientācija (1.3.0).** Atsevišķas kameras produkti „Preview”, „Radiance”, „Reflectance” un „Index” tiek pagriezti atbilstoši tās „Mirror/Rotation” iestatījumam; „Raw” un „Debayered” paliek tādā stāvoklī, kādā tos nolasīja sensors. Visi masīva faili paliek sākotnējā formātā. Katrs fails reģistrē šo iestatījumu savā XMP, tādējādi atkārtotā apstrāde to piemēro tādā pašā veidā.
* **Kurai kamerai pieder `.daq` (1.3.0).** Aizturēšanas `.daq` apstrādes laikā tiek saskaņots tikai ar attēliem no tām kamerām, kuras norāda tā `.daq.cameras.json`. Tādējādi atsevišķa kamera bez sava gaismas sensora vairs neizmanto citas kameras rādījumus — tai ir jāpiesaista sensors vai jāreģistrē sensors cilnē „Gaismas sensori”. Gaismas sensora ieraksts (un jebkurš `.daq` bez šī faila, ieskaitot vecākus ierakstus) joprojām darbojas visām kamerām.

Tas ir tas pats izkārtojums, kādu `chloros-cli lattice capture` / `array-capture` ieraksta savā `-o` direktorijā — dokumentēts [CLI atsauces sadaļā § Kā izskatās uzņemto attēlu mape](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, the daq/ and clamp_masks/ folders, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Uzņemto attēlu pārstrāde par kalibrētiem produktiem

Uzņemtie neapstrādātie kadri kopā ar saglabāto `.daq` ir viss, kas nepieciešams apstrādes procesam — tieši tāpēc „Fastest Capture” ir drošs risinājums reālam darbam.

* **GUI**: pievienojiet uzņemto attēlu mapi projektam ([Failu pievienošana projektam](../processing-images-gui/adding-files-to-a-project.md)) un apstrādājiet kā parasti.
* **CLI**: norādiet `process` uz **uzņemto attēlu saknes mapi**:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` parasti importē tikai norādīto mapi, bet, ja tajā nav attēlu un tajā ir apakšmapes, programma automātiski pārskata apakšmapes — tādējādi `daq/` mape un tās apakšmapes tiek importētas vienā reizē. Katra uzņemšana tiek importēta kā **viens attēls**, kam pārējie līmeņi tiek pievienoti kā apskatāmi režīmi, nevis kā viens attēls katram līmenim.

Līmeņa apakšmapes tieša nosaukšana (piem., `…/captures/raw/`) arī darbojas, taču atstāj mapi `daq/` neiekļautu — kopējiet to līdzās, kad-izveidojat radiometrisko produktu no `raw/`, citādi laika zīmju saskaņošanai nebūs ar ko salīdzināt.

{% hint style="warning" %}
**Apstrāde vienmēr sākas no `raw`.** Katrā uzņemšanā neapstrādātais kadrs ir apstrādes procesa avots; `debayered`, `radiance`, `reflectance`, un `preview` parādās kā skatāmie režīmi, bet nekad netiek atgriezti caur apstrādes cauruļvadu — atkārtota atvasinātā produkta apstrāde nozīmētu atkārtoti piemērot vinjetēšanu, krāsu un starojuma aprēķinus, kas jau ir iebūvēti tā pikseļos, tāpēc Chloros to noraida, nevis veic dubultu apstrādi. Renderējumi `index/` un `composite/` netiek apstrādāti vispār (tie ir izvades faili, nevis uzņemtie attēli). „Captures” mape, kas saglabāta **bez** neapstrādātu datu importēšanas, tiek parādīta normāli, taču `process` to izlaiž un par to paziņo; `--input-level {raw,debayered,processed}` ir apzināti izveidota „glābšanas lūka”, kas piespiež izmantot ieejas punktu. Skatīt [CLI atsauci](../reference/cli-reference.md#what-a-captures-folder-looks-like), lai uzzinātu precīzus izlaišanas ziņojumus.
{% endhint %}

Vēl divas darbības, kas ir vērts zināt, veidojot skriptus atkārtotai apstrādei:

* `chloros-cli process` izpilde, kas pieprasīja rezultātus, bet **nerakstīja nekādus attēlu rezultātus,**beidzas ar kļūdu un iziet ar rezultātu, kas nav nulle** — jūs nekad nesaņemsiet klusu, tukšu izpildi. Veiksmīgas izpildes ziņo par rezultātu skaitu. (Apzināta izpilde, kurā tiek apstrādāti tikai metadati, joprojām tiek uzskatīta par veiksmīgu.)
* Atkārtoti importēti apstrādāti eksporti nekad neaizņem uzņemuma neapstrādāto datu vietu — sākotnējie neapstrādātie dati vienmēr paliek apstrādes procesa avots.

## CLI ekvivalenti

Viss šajā lapā aprakstītais var tikt vadīts bez grafiskās saskarnes. GUI uzņemšanas režīmi tieši atbilst `chloros-cli lattice array-capture`:

| GUI | CLI |
| --- | --- |
| Vienreizējs | `chloros-cli lattice array-capture` |
| Nepārtraukts | `array-capture --continuous [--count N] [--duration S]` |
| Intervāls | `array-capture --interval S [--duration S]` |
| Ātrākā uztveršana | `array-capture --fastest` |
| Izlīdzināšanas izvēles rūtiņa | `--aligned / --no-aligned` |
| Eksporta tipa izvēles rūtiņas | `--processing LEVEL` vai `--levels L1,L2,…` (noklusējums `all`) |
| Video ieraksta indekss | `chloros-cli lattice array-record` |
| Neapstrādātu attēlu sērijas ierakstīšana / Video veidošana | `chloros-cli lattice array-burst` / `array-build-video` |

Pilnās karogu tabulas, viedās automātiskās ekspozīcijas (smart-AE) fiksētas uzņemšanas opcija (`--smart`) un pastāvīgas ātruma modelis ir aprakstīti sadaļā [CLI atsauces materiāls § Uzņemšanas režīmi, ierakstītāji un pārstrāde bezsaistē](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).
