# Attēlu režģis

Pēc attēlu importēšanas projektā jūs tos redzēsiet izkārtotus režģī galvenajā logā. Režģī jūs izvēlaties, **kuru katra attēla versiju jūs skatāties** — pogas virs tā vienlaikus pārslēdz katru sīktēlu starp avota failiem un katru apstrādāto rezultātu.

## Sīktēlu izmērs

Lai pielāgotu attēlu sīktēlu izmēru, izmantojiet palielināšanas slideri labajā augšējā stūrī. Slidera diapazons ir no **64 pikseļiem līdz 1200 pikseļiem**.

* **Ctrl + peles ritenītis** arī maina sīktēlu izmēru.
* **Ctrl + `+`**/**Ctrl + `=`**un**Ctrl + `−`** maina izmēru par 4 pikseļiem katrā nospiedienā. Tastatūras komandas diapazons sākas no 64 pikseļiem mazākajā galā un beidzas ar izmēru, kas precīzi ietilpst divām sīktēliem vienā rindā pašreizējā logā.
* Izvēlētais izmērs tiek saglabāts kopā ar projektu (`UI → Grid thumbnail size` līdz `project.json`, noklusējums — `160`), tādējādi, atverot projektu no jauna, tas tiek atjaunots.

<figure><img src="../.gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>Sīktēla *izšķirtspēja* ir atsevišķs iestatījums no sīktēla *izmēra*: skatiet **Ekrāns → Attēla sīktēla izšķirtspēja** sadaļā [Projekta iestatījumi](../project-settings/project-settings.md) (noklusējuma vērtība — 512 pikseļi garajā malā). Izmērs nosaka, cik liela tiek attēlota flīze; izšķirtspēja nosaka, cik daudz detaļu tiek ielādētas, lai to aizpildītu.***

## Tīkla rīkjosla

Pogu rinda virs tīkla sastāv no līdz pat trim grupām, no kreisās puses uz labo:

1. **Pēc izraisītāja / Pēc kameras** — grupēšanas režīms. Parādās tikai projektos, kuros ir iekļauti LATTICE uzņēmumi.
2. **Kameru filtrēšanas pogas** — viena katrai LATTICE kamerai. Parādās tikai režīmā „Pēc kameras”.
3. **Eksportēšanas/skatīšanas režīma pogas** — kurš produkts tiek parādīts katrā sīktēlā.

Ja logs ir pārāk šaurs, lai tās visas ietilptu, grupas no labās puses uz kreiso pusi saraujas, pārvēršoties par izvēlnēm, kas parādās, uzvedot peles kursoru: vispirms saraujas eksportēšanas/skatīšanas pogas, tad kameru pogas. Saraujusies grupa atstāj vienu pogu ar norādi uz pašlaik aktīvo izvēli, un, uzvedot uz tās peles kursoru, tiek parādīts pilnais pogu komplekts. **Pēc izraisītāja / Pēc kameras nekad neaizveras.

<!-- SCREENSHOT-NEEDED: Image grid toolbar of a LATTICE array project at full width, showing all three button groups inline: Per Trigger / Per Camera, three camera filter buttons labelled "LATT-M3M (serial)", and the export/view buttons including TIFF, RAW (Original), RAW (Radiance), RAW (Reflectance). -->

*****

## Eksportēšanas un skatīšanas pogas

Šīs pogas pārslēdz sīktēlu režģi starp attēlu tipiem. **Poga parādās, tiklīdz pastāv produkts, kuru tā nosauc** — kas avota failu gadījumā nozīmē tūlīt pēc importēšanas, nevis pēc apstrādes. Chloros atkārtoti skenē projekta produktus, kamēr noris apstrāde, tādēļ pogas parādās apstrādes laikā, tiklīdz katrs produkts sāk ierakstīties diskā.

### Pamata poga

Pa kreisi esošajai eksportēšanas pogai ir norādīts **tas, ko jūs faktiski importējāt**:

| Kas tika importēts | Poga |
| --- | --- |
| Survey3 RAW+JPG | `JPG` |
| LATTICE uzņēmumi ar priekšskatījumu blakus RAW kadram | `PNG` vai `TIFF`, atkarībā no tā, kādi ir priekšskatījumi |
| LATTICE uzņēmumi, kur pamatfails **ir** RAW kadrs | *nav pogas* — `RAW (Original)` jau parāda šo failu |

Jauktā projektā uzraksts atbilst tam paplašinājumam, ko izmanto lielākā daļa attēlu.

### Produkta pogas

| Poga | Rāda | Kad parādās |
| --- | --- | --- |
| **Mērķi** | Attēli ar atklātu kalibrēšanas mērķi | Pēc darbības, kuras laikā tika atklāti mērķi |
| **Atstarošanās** | Kalibrēti atstarošanās attēli | Tikai Survey3 projekti — LATTICE projekti izmanto `RAW (Reflectance)`, tādēļ režģī nekad netiek parādītas divas atstarošanās pogas |
| **Baltā balanss** | Attēls ar baltā balansu (RGB kameras) | Pēc apstrādes |
| **Vignēšanas korekcija** | Nekalibrētais attēls ar vignēšanas korekciju | Pēc darbības, kurā nevarēja piemērot atstarošanas kalibrēšanu un bija ieslēgta *Vignēšanas korekcija* |
| **Sensora reakcija** | Nekalibrētais rezerves variants ar sensora reakciju | Tas pats, bet ar izslēgtu *vignēšanas korekciju* |
| **`RAW (<INDEX> Index)`** | Viena poga katram aprēķinātajam indeksam | Pēc darba cikla ar konfigurētiem indeksiem |
| **`<INDEX> LUT`** | Viena poga katram indeksam ar krāsu kartēšanu | Pēc darbības cikla ar konfigurētu LUT |
| **`<Index> <Index\|LUT> <NNN>`** | Viena poga par katru [Indeksa/LUT smilšu kastes](index-lut-sandbox.md) eksporta ciklu | Brīdī, kad smilšu kastes eksports ir pabeigts |

### LATTICE līmeņu pogas

Projekti, kuros ir iekļauti LATTICE uzņēmumi, pievieno šīs pogas, kas ir marķētas ar līmeņa nosaukumu, nevis produkta nosaukumu:

| Poga | Līmenis |
| --- | --- |
| **RAW (Oriģināls)** | Avota neapstrādātais kadrs, tāds, kāds importēts |
| **RAW (starojums)** | Float32 spektrālais starojums, W/m²/sr/nm |
| **RAW (atstarošana)** | uint16 atstarošana, 32768 = ρ 1,0 |

`RAW (Original)` ir pieejams jau no importēšanas brīža — tam nav nepieciešama apstrāde. Ja LATTICE importam vispār nav bāzes pogas (katra uzņēmuma bāzes fails ir tā neapstrādātais kadrs), režģis pats pārvietojas uz pirmo pieejamo līmeņa pogu, lai rīkjoslas izcelšana atbilstu tam, ko redzat.

Divlīmeņu Chloros eksportiem **nav savas režģa pogas**:

* **Debayered** — `RAW (Original)` skats jau tiek renderēts bez bayera filtra, tāpēc otrā poga uz vizuāli identiska attēla radītu lieku troksni. `RAW (Debayered)` produkts joprojām tiek ierakstīts diskā un joprojām ir izvēlējams no pilnekrāna slāņu izvēlnes.
* **Priekšskatījums** — RGB kamerās priekšskatījums tiek reģistrēts kā `White Balanced` slānis, kuram ir poga. Daudzspektrālajās kamerās tā tiek reģistrēta kā `RAW (Preview)` un ir pieejama no pilnekrāna slāņu izvēlnes.

{% hint style="info" %}
Šīs līmeņu pogas tiek attēlotas tikai projektos, kuros faktiski ir iekļauti LATTICE kadri. Survey3 projekti reģistrē dažus no tiem pašiem iekšējiem slāņu nosaukumiem, un pogas tiek filtrētas, lai Survey3 režģim paliktu ierastais `JPG / Targets / Reflectance` komplekts.
{% endhint %}

Noklikšķinot uz režģa sīktēla, tiek atvērts pilnekrāna [Attēlu skatītājs](opening-an-image-full-screen.md) **tam pašam produktam, ko rāda režģis** — ja režģis ir iestatīts uz `Targets`, sīktēls atver eksportēto mērķa attēlu.

<figure><img src="../.gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: This GIF predates the LATTICE level buttons and the toolbar group separators. Reshoot on a LATTICE project cycling base -> RAW (Original) -> RAW (Radiance) -> RAW (Reflectance) -> an index button, so the new button set and the level names are visible. -->

***

## LATTICE projekta grupēšana: pēc izraisītāja vai pēc kameras

Masīva uzņēmumi rada vairākus attēlus no viena un tā paša mirkļa, kas uzņemti ar dažādiem kameru moduļiem. Grupēšana nosaka, kā režģis tos sakārto. Abos režīmos tiek attēlotas pilna platuma saslēdzamas virsraksta joslas; **katra grupa sākotnēji ir izvērsta**, un Chloros atceras tās, kuras jūs aizverat. Saslēgšanas stāvoklis tiek uzskaitīts atsevišķi katram režīmam, tādēļ grupas aizvēršana režīmā „Pēc kameras” neaizver neko režīmā „Pēc izraisītāja”.

### Pēc kameras (noklusējums)

Viena grupa katram kameras modulim. Virsrakstā tiek parādīts kameras modelis un sērijas numurs (`LATT-M3M — <serial>`), kā arī attēlu skaits. Attēli grupā tiek sakārtoti hronoloģiskā secībā pēc uzņemšanas brīža.

Šajā režīmā rīkjoslā parādās arī viena **kameras filtrēšanas poga katrai kamerai** ar nosaukumu `MODEL (SERIAL)`. Sākotnēji visas kameras ir atlasītas; noklikšķinot uz pogas, atlasīšana tiek atcelta un attiecīgās kameras grupa tiek noņemta no režģa. Tas ir ātrs veids, kā pārskatīt vienu joslu visā lidojuma laikā.

### Pēc izraisītāja

Viena grupa uz vienu uzņemšanas notikumu — visu moduļu uzņemto kadru kopums, kas uzņemti vienā un tajā pašā izraisītājā. Virsrakstā tiek parādīts uzņemšanas laiks, iesaistīto kameru skaits un ikonas katram grupā esošajam kameras modelim. Grupas iekšējie attēli ir sakārtoti pēc kameru sērijas numuriem, tādējādi katram izraisītājam viena un tā pati josla atrodas vienā un tajā pašā slejā.

<!-- SCREENSHOT-NEEDED: Image grid in Per Trigger mode for a 3-camera LATTICE array, showing two consecutive trigger groups with their header bars (chevron, capture timestamp, "3 cameras", and the three model badges) and one group collapsed to show the closed state. -->
Jauktā projektā iekļautie attēli, kas nav no LATTICE, netiek grupēti — tie tiek attēloti kā vienkārši attēli pēc grupām.

***

## Tīkla sīktēli atbilst GSD bloka izmēram

Ja attēla cilnes sānjoslā esat iestatījis **GSD (px)** bloka izmēru, režģa sīktēli tiek attēloti ar to pašu zemes izšķirtspēju — ne tikai pilnekrāna skatā. Bloka izmērs 8 nozīmē, ka katrs attēlotais pikselis ir 8 × 8 avota pikseļu bloka vidējais rādītājs visur lietotnē, kur tiek parādīts attēls.

Tā kā flīzes sākotnēji ir tikai pāris simtu pikseļu platas, rupji bloka izmēri vairs nerada redzamu atšķirību režģī jau krietni agrāk nekā pilnekrāna skatā: 4000 px rāmis, kas iezīmēts 160 px flīzē, jau atbilst aptuveni 25 avota pikseļiem uz vienu attēloto pikseli. Skatīt [Attēla atvēršana pilnekrāna režīmā](opening-an-image-full-screen.md#gsd-block-size), lai uzzinātu par pašu vadības elementu.

***

## Saistītās lapas

* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — pilnekrāna skatītājs, kursora vērtības un histogramma
* [**Attēla slāņi**](image-layers.md) — slāņu izvēlne pilnekrāna skatītājā
* [**Indeksa/LUT izmēģinājumu vide**](index-lut-sandbox.md) — indeksa vizualizāciju veidošana un eksportēšana
* [**Projekta iestatījumi**](../project-settings/project-settings.md) — eksportēšanas slēdži, kas nosaka, kādi produkti vispār pastāv
