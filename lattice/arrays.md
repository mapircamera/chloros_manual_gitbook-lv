# Daudzkameru sistēmas

LATTICE **sistēma**ir divas vai vairākas LATTICE kameras, kas savienotas kā viena sinhronizēta vienība. Viena kamera ir**galvenā**: tā nosūta aparatūras GPIO trigera impulsu pa kopīgo sinhronizācijas līniju (noklusējumā**Line2**), tādējādi katra sistēmas sastāvdaļa uzņem attēlu tajā pašā brīdī. Chloros nodrošina PTP laika sinhronizāciju, tiešraides priekšskatījumu (atsevišķi attēla laukumi katrai kamerai vai viens saskaņots daudzjoslu kompozīts) un sinhronizētu uzņemšanu — katrs uzņemšanas cikls rada vienu **kadru grupu**, kurā visām kamerām ir vienāds laika zīmogs un kadra ID (uzrādīts kā `fid:N` uzņemšanas izvades datos).

Masīvi ir veids, kādā mono (M3M) kameras ģenerē veģetācijas indeksus — viena kamera nodrošina vienu joslu, un masīvs tās saskaņo daudzjoslu kopā. Skatīt [Mono kameras un veģetācijas indeksi](mono-indices.md).

Ir trīs līdzvērtīgi veidi, kā savienot masīvu, un visos tiek izpildīta viena un tā pati „smart-prep” darbplūsma:

| Virsma | Ieejas punkts |
| --- | --- |
| GUI | Sadaļa „Kameras” → **Savienot masīvu** (zila poga) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (pirmais sērijas numurs = galvenais) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (pirmais sērijas numurs = galvenais) |

„Smart-prep“ veic: tīkla iespēju pārbaudi (ICMP DF ping + GVSP pārbaude), sinhronizācijas līmeņa izvēli, kadru ātruma prognozi pie pieprasītā kadra izmēra, lēmumu par saspiestu 12 bitu datu pārraidi (sk. [Saspiests 12-bitu transports](#packed-12-bit-transport)), PTP aktivizēšanu, automātisku pikseļu formāta izvēli katrai kamerai, automātiskās ekspozīcijas sākotnējo iestatīšanu, balstoties uz katras kameras saglabāto stāvokli, un GPIO trigera konfigurāciju Line2.

**Noklusējumā tiek izmantots pilnais sensors (jaunums versijā 1.3.0).** Connect saglabā jūsu pieprasīto kadru izmēru — pilnu 2048×1536 sensoru, bez pikseļu apvienošanas un bez apgriešanas, ja vien jūs neizvēlaties citādi — un ļauj kadru ātrumam samazināties līdz tam, ko nodrošina serveris un tīkls. Tas nekad pats neveic pikseļu apvienošanu vai apgriešanu (versijā 1.2.0 pikseļu apvienošana tika automātiski aktivizēta, ja kadru ātrums bija zemāks par 1,5 fps). Pikseļu apvienošana vai mazāks ROI ir jūsu izvēle: dialoglodziņā „Connect” izvēlieties vienu no tiem, un sadaļā „Projected Outcome” tiks parādīts attēlu atjaunošanas ātrums, ko tas nodrošina; komanda `array-connect` izdrukā ātrākās opcijas ar to prognozētajiem ātrumiem.

{% hint style="info" %}
Lai kaut kas no šī visa darbotos, kamerām jābūt sasniedzamām pa savienojumu — skatiet [Kameru savienošana](connecting.md), lai veiktu atklāšanu, adresēšanu un pirmās savienošanas kalibrēšanas lejupielādi. Daudzkameru sistēmās galvenā tīkla kartes (NIC) uztveršanas gredzena iestatījumi ir tikpat svarīgi kā savienojuma ātrums; pilnīga simptomu→risinājumu tabula atrodama [CLI atsauces § Galvenās tīkla kartes (NIC) iestatīšana un regulēšana](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays).
{% endhint %}

## Dialoglodziņš „Array Connect”

Cilne „Cameras” → **Connect Array**atver trīs soļu vedni:**Select → Display Mode → Settings**.

### 1. solis — Izvēlieties galveno un pakārtotās kameras



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered (an FRGB among them). Table showing Camera / Serial / IP / Master radio / Slave checkbox columns in camera order (M3C first, FRGB at the top), with the FRGB pre-selected as master and the green "Sync cable found on N cameras. Any of them can be master; the RGB camera (else the first) is pre-selected." probe banner visible above the table. -->

Tiklīdz dialoglodziņš tiek atvērts, tas sāk skenēt tīklu („Scanning network...“), pēc tam pārbauda sinhronizācijas kabeļus („Probing sync cables...“), lai noteiktu, kuras kameras ir pieslēgtas M8 sinhronizācijas līnijai. Lai izveidotu kameru masīvu, ir nepieciešamas vismaz **2 kameras**.

M8 sinhronizācijas kabeļi saplūst vienā savienojuma punktā, tādēļ **jebkura ar kabeli pieslēgta kamera var būt galvenā**. Pārbaude iepriekš piešķir lomas — RGB (`-FRGB`) kamera kā galvenā, ja tā ir pieslēgta ar kabeli, citādi pirmā pieslēgtā kamera, bet pārējās pieslēgtās kameras kā pakļautās — un parāda vienu no trim paziņojumiem:

| Paziņojums | Nozīme |
| --- | --- |
| „Sinhronizācijas kabelis atrasts {n} kamerās. Jebkura no tām var būt galvenā; iepriekš ir izvēlēta RGB kamera (citādi — pirmā).” (zaļa) | Zonde ir atradusi kameras sinhronizācijas līnijā; izvēles rūtiņas „galvenā” un „pakļautā” jau ir atzīmētas. |
| „Galvenā kamera nav atklāta — pārbaudiet GPIO kabeli“ (oranža) | Neviena kamera nesaņēma trigera impulsu; pārbaudiet sinhronizācijas kabeļu savienojumus. Lomas joprojām ir iepriekš aizpildītas (RGB kamera, citādi pirmā kamera sarakstā, kā galvenā), un jūs varat tās mainīt. |
| „Nav sinhronizācijas kabeļa: {serialnumuri}“ (oranža) | Uzskaitītajām kamerām nav pieslēgts sinhronizācijas kabelis. |

Kameru tabulā ir šādas kolonnas: **Kamera / Sērijas numurs / IP / Galvenā (radio) / Pakļautā (izvēles rūtiņa)**. Rindas ir sakārtotas**pēc kameru secības** — M3C (Bayer) kameras pirms M3M (mono); starp M3C — FRGB, FRGN, FOCN, tad FNGB; M3M — pēc filtra viļņa garuma, sākot ar viszemāko (F450 pirms F808). Sānu joslas kameru saraksts un tabulas noklusējuma izkārtojums izmanto to pašu secību.

* Izvēlieties tieši **vienu galveno kameru**un**vienu vai vairākas pakārtotās kameras**. Atkārtota klikšķa uz pašreizējās galvenās kameras izvēles pogas to atceļ. Citas galvenās kameras izvēle notiek ar vienu klikšķi: jaunā galvenā kamera atstāj pakārtoto kameru sarakstu, un iepriekšējā galvenā kamera kļūst par pakārtoto kameru (ja vien tai nav sinhronizācijas kabeļa).
* Kameru, kas atzīmēta ar **"Nav sinhronizācijas kabeļa"**, nekad nevar izvēlēties kā pakļautu — pakļautā kamera bez trigera vadu savienojuma bezgalīgi gaidītu sinhronizācijas līniju un nodrošinātu neaktīvu signālu. Tā vietā šo kameru pieslēdziet kā autonomu kameru.
* Kameras, kas jau ir pieslēgtas kā autonomas, *netiek* atspējotas: savienošana ar masīvu atbrīvo autonomo sesiju un atkārtoti atver kameru masīvā.

**Tālāk: Rādīšanas režīms →**kļūst pieejams, tiklīdz ir izvēlēts viens galvenais un vismaz viens pakļautais.**Atkārtota skenēšana** atkārtoti palaista atklāšana un vadu pārbaude.

{% hint style="warning" %}
**Atcelt** ir atspējots, kamēr notiek skenēšana vai pārbaude — atcelšana pārbaudes vidū var izraisīt kameras SDK avāriju LATTICE kameras programmaparatūrā. Gaidiet, līdz rotējošais indikators apstājas.
{% endhint %}

### 2. solis — Attēlošanas režīms

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->



| Režīms | Kas tiek parādīts |
| --- | --- |
| **Atsevišķas kameras** | Viena reāllaika flīze katrai kamerai, visas tiek aktivizētas vienlaikus, lai kadri paliktu sinhronizēti. Katra kamera saglabā savu krāsu un iestatījumus. |
| **Apvienotas kameras** *(noklusējums)* | Viena flīze, kas attēlo saskaņotu daudzjoslu NDVI/indeksa kompozīciju. Kamerām ir kopīga matricas krāsa. |

Attēlošanas režīms maina tikai tiešraides priekšskatījuma izskatu — uzņemšanas darbība abos režīmos ir vienāda.

### 3. solis — Matricas iestatījumi un prognozētais rezultāts

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Trigger Rate / Wire Budget controls (1.3.0 has no "Pin resolution" checkbox), right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->



Ieejot šajā ainā, Chloros pieprasa **ieteikumu**no servera un to piemēro:**pilns sensora diapazons — bez binninga, bez ROI apgriešanas — ja vien kāds kadru ātrums to spēj nodrošināt**, neatkarīgi no tā, kādu ātrumu nodrošina serveris un savienojums. Tikai tad, ja neviens ātrums nespēj nodrošināt pilnu sensora diapazonu, ieteikums samazina kadru, un tad tas dod priekšroku pikseļu apvienošanai, nevis ROI apgriešanai, jo pikseļu apvienošana saglabā pilnu redzes lauku. No šī brīža jūs apmaināt izšķirtspēju pret kadru ātrumu. Katra izdarītā izmaiņa atkārtoti palaista analīzi reāllaikā un atjaunina labajā pusē esošo**Prognozētais rezultāts** paneļu.

Kreisā kolonna — iestatījumi:

| Kontrole | Izvēles | Noklusējums | Piezīmes |
| --- | --- | --- | --- |
| **ROI (redzes lauks)** | Pilns (2048×1536) / Puse (1024×768) / Ceturtdaļa (512×384) | Pilns | Sensora apgriešana: apgriešana uz pusi vai ceturtdaļu, lai iegūtu mazāku apgabalu ar sākotnējo pikseļu blīvumu. |
| **Pikseļu apvienošana** | 1× / 2× (summa 2×2) / 4× (summa 4×4) | 1× | Aparatūras pikseļu apvienošana: 2×2 = pilns redzes lauks ar ceturto daļu no datu pārraides izmaksām; 4×4 = pilns redzes lauks ar 1/16 daļu. Paslēpts, ja kameras neatbalsta binning. |
| **Vadu puses attēls** (nolasīšana) | — | — | Pēc binninga platums × augstums, kas faktiski tiek nosūtīts pa vadu, noapaļots līdz 16 reizinājumiem (minimums 64). |
| **Trigera frekvence** | 0,5–60 fps, solis 0,1 | tukšs = automātiski | Galvenās kameras trigera izšaušanas frekvence. Atstājiet tukšu, lai Chloros to aprēķinātu. |
| **Vadu budžets**| 20–2000 MB/s, solis 10 | tukšs = automātiski | Cik daudz saimniekdators faktiski var apstrādāt, MB/s —**vienīgais skaitlis, no kura ir atkarīga visa masīva piešķiršana.** Automātiski noteikts no tīkla adaptera. Samaziniet to, ja masīvs ziņo par bojātiem rāmjiem: noteiktais vērtējums pārspīlē USB adapteru un koplietošanas komutatoru jaudu. Mainot to, prognoze tiek atkārtoti aprēķināta reāllaikā. Lauciņš atveras ar to joslas platumu, ar kādu pēdējā masīva bija savienots (saglabāts kopā ar projektu), tādējādi vienreiz koriģētais joslas platums tiek saglabāts. |

No versijas 1.2.0 ir pazudis **Pin izšķirtspēja** izvēles rūtiņa: savienojums vienmēr saglabā jūsu izvēlēto kadru izmēru un tā vietā samazina kadru ātrumu.

Labā kolonna — **Prognozētais rezultāts**:

* **Sinhronizācijas līmenis** — „Vienlaicīga uzņemšana” (zaļa), „Vienlaicīga uzņemšana (FTD — pakāpeniska raidīšana)” (zaļa), „Pakāpeniska uzņemšana (100 ms nobīde)” (dzeltens) vai „Konfigurācija pārāk liela” (sarkans). Chloros izvēlas līmeni: vienlaicīga uzņemšana, ja vadu var koplietot ar ātrumu, kuram var pielāgot kameru darbību (kadru ātrums samazinās, samazinoties katras kameras daļai), un pakāpeniska uzņemšana tikai tad, ja tas nav iespējams — tādējādi masīvs savienojas ar jebkuru tīklu ar tādu kadru ātrumu, kādu atļauj šis tīkls.
* **Kadrs/sek. prognoze** — kadru ātrums, ar kādu paredzēts, ka masīvs darbosies. Vienlaicīgas uzņemšanas gadījumā tas ir viens skaitlis: vispirms tiek noteikts kadru ātrums, un automātiskā ekspozīcija aizpilda tam atvēlēto laiku, pēc tam palielina pastiprinājumu, lai vājā apgaismojuma apstākļos tas nesamazinātos (sk. [Kā automātiskā ekspozīcija izmanto ekspozīcijas laiku un pastiprinājumu](camera-settings.md)). Veicot pakāpenisku uzņemšanu, tas tiek parādīts kā diapazons („tumšs → gaišs”), jo katras kameras ekspozīcija pagarina pakāpenisko uzņemšanu vājā apgaismojumā.
* **NIC līnija** — savienojuma ātrums un ilgstošā caurlaides spēja („NIC {mbps} Mbps · ilgstošā {N} MB/s”).
* **Simultānas pārraides pārbaude** — vai uzņēmējdatora tīkla kartes gredzens spēj uzņemt vienu vienlaicīgu datu plūsmu no visām kamerām („Simultāna pārraide: X MB · izmantojamais tīkla kartes gredzens: Y MB ✓/✗”).
* **Vadu budžeta pārbaude** — stabilā stāvoklī kopējais pieprasījums salīdzinājumā ar sadursmju drošo vadu maksimālo jaudu („Vadu budžets: {pieprasījums} MB/s, ko pieprasa {n} kameras · maksimālā jauda {ceiling} MB/s ✓/✗ pārsniegts”).
* **"Maksimālais kameru skaits šajā vadā: {n} — noteikts atbilstoši minimālajai joslas platībai katrai kamerai, tādēļ grupēšana to nepalielina."** — parādās, ja esat tuvu (vai pārsniedzat) kameru skaita ierobežojumu.
* **"Ar šiem iestatījumiem KADRI TIKS ZAUDĒTI."**— sarkans brīdinājums ar servera puses iemeslu, kā arī bloķētāju saraksts un zili**risinājumu ieteikumi** („Lai šo masīvu pielāgotu tīklam” / „Lai atbloķētu vienlaicīgu uzņemšanu”).**Piemērot un savienot** ir bloķēts, kamēr nav prognozes, un tā uzraksts norāda, kāpēc tiek atteikts:

| Poga | Nozīme | Kas patiešām palīdz |
| --- | --- | --- |
| „Analizē...” | Analīze vēl notiek. | Gaidiet. |
| **„Šim tīklam ir pārāk daudz kameru"**| Tīkls nespēj pārraidīt masīvu pat ar vienu kameru vienlaikus (kopējā pārbaude neizdevās): kameru skaits pārsniedz tīkla jaudu vai tajā jau tiek pārraidīti citu kameru dati. | Mazāk kameru, jumbo rāmji no gala līdz galam vai ātrāks tīkla interfeiss.**Mazāks ROI nepalīdzēs** — skatīt zemāk. |
| **"Samaziniet ROI, lai aktivizētu"** | Ar šiem iestatījumiem kadri tiktu zaudēti (burst/ring pārbaude neizdevās). | Samaziniet ROI, palieliniet binningu vai salabojiet tīkla kartes uztveršanas gredzenu. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->



Savienojoties, var parādīties zaļš **kalibrēšanas lejupielādes panelis** ar progresijas joslu katram seriālajam savienojumam: pirmoreiz, kad kamera tiek pieslēgta datoram, Chloros lejupielādē no kameras aptuveni 3,8 MB lielu rūpnīcas kalibrēšanas paketi, izmantojot GigE (aptuveni 70 sekundes katrai kamerai). Kamerām, kas ir saglabātas kešatmiņā, šis panelis nekad netiek parādīts; ja ir vairāk nekā sešas kameras, tā rindas ritina. Skatīt [Kameru pieslēgšana](connecting.md).

### Saspiežta 12 bitu datu pārraide

12 bitu formāti (`BayerRG12`, `Mono12`) parasti tiek pārraidīti kā 16 bitu vārdi. **Saspiestā**pārraide nosūta tos pašus 12 bitus 1,5 baitos uz pikseli un izpako tos uz galvenās ierīces, tādējādi katra kadra nosūtīšanai nepieciešams par 25 % mazāk laika — pie pilnas sensora izmantošanas tas nozīmē aptuveni par trešdaļu vairāk kadru sekundē.**Saglabātie dati ir identiski.** „Array connect” to nosaka automātiski (`auto`): saspiež tikai tad, ja tas palīdz, un ja uzņēmējdatoram ir pietiekami daudz procesora jaudas atspiešanai. Grafiskajā lietotāja saskarnē (GUI) tam nav atsevišķa slēdža; no komandrindas (CLI) izmantojiet `array-connect --packed {auto,on,off}` (noklusējums `auto`). 8-bitu un 16-bitu formāti nekad netiek saspiežti.

## Pārraides joslas platums: cik daudz kameru var pieslēgt

Tas, cik daudz kameru var pārraidīt, ir atkarīgs no tīkla, nevis no Chloros, tāpēc plānošanas dati ir atrodami aparatūras rokasgrāmatā: **[Array Bandwidth Planning](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Kā Chloros ar tiem rīkojas: savienošanas dialoglodziņā tiek palaista tīkla pārbaude, tiek prognozēts sasniedzamais kadru ātrums un izvēlēts atbilstošs līmenis. Masīvs savienojas ar jebkuru tīklu: lēnā savienojumā katras kameras darbība tiek pielāgota tās vadu daļai, un kadru ātrums samazinās, nevis masīvs tiek noraidīts vai tā kadri tiek samazināti. Tiek noraidīts tikai tāds masīvs, kuru savienojums nespēj pārraidīt pat vienu kameru vienlaikus (**Pārāk daudz kameru šim tīklam**), nevis klusi izmetot paketes — skatiet iepriekš aprakstīto prognozēto rezultātu paneli.

## Kad kadri pazūd

Kamera var nebūt publicētajā grupā divu pilnīgi atšķirīgu iemeslu dēļ,
un tiem nepieciešami pretēji risinājumi. Chloros tos uzskaita atsevišķi, nevis ziņo par vienu
„nepilnīgu” skaitli, kas nenorāda ne vienu, ne otru:

| Kas notika | Ko tas nozīmē | Kur meklēt |
| --- | --- | --- |
| **Sabojāts**— kadrs ieradās, bet bija strukturāli bojāts | GVSP pakešu zudums tīkla ceļā |**Vadu jauda**, tīkla kartes uztveršanas gredzens, jumbo kadri, komutators |
| **Nekad nav ieradies**— rāmis vispār nav ieradies | Kamera nav iedarbinājusies vai no tās nekas nav izgājis |**M8 sinhronizācijas kabelis**, sinhronizācijas līnija, vai visi dalībnieki ir ieslēgti |

Sadale tiek pārvērtēta ik pēc 10 sekundēm, kamēr masīvs pārraida datus. Ja tā pārsniedz 5 %, tas tiek
reģistrēts, norādot abus skaitļus, un par katru bojāto buferi tiek ziņots pirmoreiz, kad tas
notiek katrai kamerai, pēc tam dati tiek apkopoti reizi minūtē, lai garas sesijas paliktu lasāmas.

**Bojāti kadri ar nulles „nekad nav ieradušies” skaitu nozīmē, ka izraisīšana un kabeļa sinhronizācija darbojas perfekti**un katrs zaudētais kadrs atrodas tīkla ceļā. Risinājums ir samazināt**vadu budžetu** un
atjaunot savienojumu.

{% hint style="warning" %}
**Trigera ātruma samazināšana nepalīdz novērst bojātus kadrus.** Kameras pakešu
pacing tiek iestatīts vienreiz, izveidojot savienojumu. Trigera ātruma samazināšana maina to, cik bieži notiek datu plūsmas
izsūtīšana, nevis to, cik ātri pati datu plūsma nonāk vadā. Uz izmērītas 4 kameru iekārtas
5× samazinājums trigera ātrumā neko nemainīja, savukārt samazinot vadu budžetu no 240 līdz
200 MB/s samazināja bojāto kadru īpatsvaru tajā pašā sistēmā no 10,4 % līdz nullei.
{% endhint %}

Darbojošs masīvs nevar pats pārplānot savu darbību — atvienojiet un atkārtoti pieslēdzieties, lai savienošanās laika
izvēlne varētu darboties atbilstoši jaunajam budžetam.

### USB tīkla adapteriem ir ierobežojumi

USB Ethernet adapteris norāda savu *Ethernet* savienojuma ātrumu, taču to, ko tas faktiski
spēj uzturēt, ierobežo USB šīna un tās draiveris. USB 10GbE adapterim agrāk tika piedēvēta
aptuveni 1000 MB/s caurlaidspēja — skaitlis, ko neviens nekad nebija izmērījis — un četru kameru sinhronizēšana
atbilstoši šai iedomātajai rezerves jaudai sabojāja 6–18 % kadru, kamēr masīvs
joprojām ziņoja par normālu mērķa kadru ātrumu. USB pieslēgtajiem adapteriem tagad ir noteikts ierobežojums
**200 MB/s** operētājsistēmā Windows un NVIDIA Jetson, kā arī **400 MB/s** operētājsistēmā Linux x86_64. Šis ierobežojums ir absolūts, nevis procentuāls, jo ierobežojošais faktors ir
šīna: USB 1 GbE adapteris nodrošina aptuveni 80 MB/s un tas netiek ietekmēts.

Ja jūsu galvenais dators patiešām darbojas ātrāk par ierobežojumu, palieliniet **Wire Budget**, lai to norādītu.

## PTP laika sinhronizācija

Kadru *sinhronizācija* notiek, izmantojot aparatūras trigeri; **PTP** (IEEE 1588 PTPv2) nodrošina salīdzināmus *laika zīmogus* visās ierīcēs. Tā ir ieslēgta pēc noklusējuma, kad masīvs tiek pieslēgts:

* **Chloros**saimniekdatoru backend darbojas kā PTP galvenais serveris**. „LATTICE“ kameras un „DAQ-E“ gaismas sensori darbojas kā tā pakļautie ierīces 0. domēnā, tādējādi attēlu laika zīmogi un DAQ spektri tiek sinhronizēti ar vienu pulksteni (~1 ms).
* `--no-ptp` (CLI) to atspējo darbam uz galda — tādā gadījumā laika zīmogi starp kamerām **nav** salīdzināmi.
* Pārbaudiet sinhronizācijas stāvokli, izmantojot CLI:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity, health (OK, or why its packets are not reaching the network)
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Pašā cilnē „Cameras” nav PTP indikatora; tur par katru kameru atsevišķi tiek parādīti tikai lasāmi lauki **Role**(Master/Slave),**Sync Line** un masīva iespēju līmenis. DAQ-E PTP stāvoklis tiek parādīts cilnē „Light Sensors” sensoru informācijā.

## Masīva skats

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows in camera order (M3C before M3M, monos by wavelength), and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

reāllaikā Galvenajā attēla apgādes zonā ir pieejami divi izkārtojumi (pārslēdzami augšējā joslā): **tīkla skats**(katra flīze ir šūna; pārkārtošana, velkot, ja režģa atslēga ir atbloķēta) un**saraksta skats**(pilna platuma rindas:**apvienots**masīvs ir viena rinda — tā saliktais laukums —, savukārt**atsevišķs**masīvs piešķir**katram elementam savu rindu**ar savu tiešraidi un savu kalibrēšanas mērķa tabulu; zem masīviem atrodas viena atsevišķa kamera — aktīvā).**Plūsmas tālummaiņas** sliders maina lauciņu izmērus; ja šūnas platums ir mazāks par 200 pikseļiem, nosaukuma/kadru skaita uzlikumi automātiski paslēpjas. Kameras parādās sānjoslā un režģa noklusējuma izkārtojumā kameru secībā (M3C pirms M3M, vispirms FRGB, mono kameras pēc viļņa garuma); atbloķējot režģi un velkot, joprojām tiek pārrakstīta režģa secība.

<!-- SCREENSHOT-NEEDED: list view of a connected SEPARATE array (1.3.0): one full-width row per member camera, each with its live feed and its own Calibration Target table below it. -->



**Atsevišķais režīms** parāda vienu mozaīkas elementu katrai kamerai. Katram mozaīkas elementam tiek parādīts:

* kameras nosaukumu (kreisajā augšējā stūrī),
* **kadru skaita rādījumu** (labajā augšējā stūrī) — tas ir kameras *faktiskais kadru uzņemšanas ātrums*, ko ziņo serveris, nevis priekšskatījuma atjaunināšanas ātrums (tiešraides priekšskatījums ir ierobežots līdz 30 kadriem sekundē neatkarīgi no kadru uzņemšanas ātruma),
* statusa punktiņš — zaļš (straumēšana) / dzintara krāsā (iekraušana) / sarkans (kļūda),
* **novecojušā kadra rotējošais simbols**, ja 2 sekundes nav saņemts jauns kadrs — tas ir normāli apmēram 5 sekundes pēc jebkuras savienošanas/atsavienošanas, kamēr backend pārbalansē datu pārraides resursus starp kamerām.**Kombinētajā režīmā**tiek parādīts viens salikts laukums: backend veic debayeringu, mērogošanu, izlīdzināšanu, trokšņu noņemšanu, konvertēšanu uz starojuma intensitāti katrā joslā (plus DLS atstarošanas koeficientu, ja ir pieslēgts gaismas sensors), izvērtē masīva indeksa izteiksmi, piemēro LUT un pārraida rezultātu kā MJPEG. Līdz brīdim, kad tiek renderēts pirmais izlīdzinātais kadrs, mozaīka norāda savu stāvokli: „Sagatavojot masīvu…”, „Kalibrē izlīdzinājumu…”, „Gaida pirmo kadru…”, vai — ja flīzes automātiskā izlīdzināšanas atkārtojumu skaits (aptuveni viena minūte) ir izsmelts — „Nepieciešama izlīdzināšana” ar pogu**Kalibrēt izlīdzinājumu**.

Masīva flīzes fps rādījums arī informē par straumes stāvokli:**„Piemēro iestatījumus…“**, kamēr iestatījumu maiņa (formāts, binning, ROI) pārstartē kameras,**„Iestrēdzis“**, ja masīvs darbojas, bet ir pārtraucis piegādāt kadru grupas, un**„Pārtraukts”**, ja tā ir pārtraukusi datu pārraidi — šādā gadījumā atkārtoti pieslēdziet masīvu. Ja spektrālajai atdalīšanai bija jāierobežo kompozīcijā redzamā josla lielā daļā tās pikseļu (25 % vai vairāk), lauciņā parādās**„Zemas ticamības joslas dati · N%"**: uz šīs joslas balstītās indeksa vērtības ir apšaubāmas (parasti viena josla ir saņēmusi pārāk maz gaismas).

Noderīga informācija par kombinēto režīmu:

* Kompozīcija ir reģistrēta attiecībā pret **galvenās**kameras kadru. AE-ROI mērķēšana un punktu ekspozīcijas mērīšana uz kompozīcijas ir precīza galvenajai kamerai un aptuvena palīgkamerām; izmantojiet**Split View** (masīva iestatījumi → „Show member cameras”), lai iegūtu pikseļu precizitāti katrai kamerai atsevišķi, neizveidojot papildu kameru savienojumus.
* **Display Layers** (masīva iestatījumi; noklusējumā izslēgts) ļauj izvēlēties priekšplāna un fona slāni — jebkuru dalībkameru vai**Index**. Ja priekšplāns = Index, pikseļi ārpus LUT minimālā/maksimālā diapazona parāda fona slāni.
* **Renderēšanas izšķirtspēja** (noklusējumā 720p) nosaka tiešraides augstumu *un* saglabātā kompozīcijas eksporta izmēru. Attēli no katras kameras vienmēr tiek eksportēti pilnā izšķirtspējā.
* Izlīdzināšana tiek aprēķināta katrai sesijai atsevišķi un netiek saglabāta. Chloros **katru masīvu automātiski izlīdzina pēc savienošanās abos attēlošanas režīmos**, tiklīdz dalībnieku automātiskā ekspozīcija ir stabilizējusies (tas turpina mēģināt līdz pat 5 minūtēm). Masīva iestatījumu paneļa sadaļā**Saskaņošana**tiek parādīti RMS atlikumi un poga**Pārkalibrēt saskaņošanu** — arī atsevišķiem masīviem, kuriem nav kompozīta flīzes.

## Uztveršana: uzraudzība pret analīzi

Masīva uzņemšanas virsmas skaidri sadalās **uzraudzības līmenī**(ieraksta to, ko redzat) un**analīzes līmenī** (ieraksta neapstrādātus datus, kalibrē vēlāk):

| Darba plūsma | Līmenis | Ko saglabā | GUI | CLI |
| --- | --- | --- | --- | --- |
| **Ierakstīšana**(nekustīgi attēli) | Analīze | Viena sinhronizēta kadru grupa katrā caurlaidē; faili katrai kamerai katrā izvēlētajā eksporta līmenī (neapstrādāti/bez debayeringa/starojums/atstarošanās/priekšskatījums/indekss) + `.daq` sidecar |**Capture All** poga + Capture Settings | `lattice array-capture` |
| **Record index video** | Monitoring | Rādītais kombinētā indeksa kompozīts reāllaikā — 8 bitu, priekšskatījuma izšķirtspēja, iebūvēta LUT. No CLI, ja GUI skats nav atvērts, aizmugurējā sistēma renderē masīva NDVI (vai `--expr`), tiklīdz masīvs ir izlīdzināts; ierakstīšana, kas neieraksta nevienu kadru, neizdodas | ● Ierakstīt indeksa video (apvienoti masīvi) | `lattice array-record` |
| **Neapstrādāta sērija → video veidošana**| Analīze | Neapstrādāti sensora kadri ar pilnu uzņemšanas ātrumu + manifests + `.daq`, pēc tam bezsaistē rekonstruēti kā kalibrēts starojuma / atstarošanas / indeksa video, laika ziņā saskaņots ar DAQ rādījumiem | ⦿ Ierakstīt neapstrādātu sēriju →**Veidot video** | `lattice array-burst` → `lattice array-build-video` |

Praktisks padoms: ja pikseļi tiks izmantoti *mērījumiem*, izmantojiet uzņemšanu vai sēriju (analīzes līmenis); ja jums vienkārši nepieciešams *noskatīties vai parādīt*, ko redzēja matrica, ierakstiet indeksa video (uzraudzības līmenis).

### Ierakstīšanas iestatījumi (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->



Zobrats blakus **Capture All** atver logu „Capture Settings” (nepieciešams atvērts projekts — ieraksti tiek saglabāti tajā):

* **Uztveršanas režīms**:**Vienreizējs**(viens cikls) /**Nepārtraukts**(secīgi; ierobežots ar uztveršanas skaitu, noklusējums 1, vai ilgumu, noklusējums 10 s) /**Intervāls** (laika nobīde: N uzņēmumi ik pēc X intervāla, kopā Y; noklusējumā 1 ik pēc 5 s 1 minūtes garumā).
* **Eksporta veidi katrai kamerai**: Raw, Debayered, Radiance, Reflectance, Preview, Index — noklusējumā ir ieslēgti visi piemērojamie. Radiance/Reflectance ir paslēpti RGB filtra kamerām; **Reflectance parādās tikai tad, ja kamerai ir DAQ gaismas sensors** (pašas vai mantots no masīva); Index prasa konfigurētu indeksa izteiksmi.
* **Aligned**(katram masīvam, noklusējumā**ieslēgts**): deformē elementu eksportus atbilstoši masīva izlīdzināšanas profilam, lai eksporti būtu pikseļu reģistrēti. Neapstrādātie dati vienmēr paliek neizkropļoti, bet transformācija tiek ietverta metadatos. Masīva elementu faili tiek saglabāti katras kameras sākotnējā orientācijā (jebkādi spoguļošanas/pagriešanas iestatījumi tiek ierakstīti failā, bet netiek piemēroti).
* **Ātrākā uzņemšana** (slēdzis): tikai neapstrādāti dati + piešķirtais DAQ rādījums + bezmaksas kombinētā indeksa kompozīcija, izlaižot kalibrēšanas aprēķinus uzņemšanas brīdī, lai panāktu maksimālo ātrumu — vēlāk atjaunojiet starojumu/atstarošanu/indeksu no saglabātā `.daq`.
* Izvēles saglabājas kopā ar projektu. Slēptās vai apturētās kameras tiek izlaistas.
* **Nepārtraukta** režīma darbība masīvos notiek Chloros aizmugurē (jaunums versijā 1.3.0): ja katra sarakstā iekļautā kamera pieder kādam masīvam, aizmugure pati izpilda uzņemšanas ciklu, ierakstot vienu grupu, kamēr izvēlas nākamo, bez atsevišķa pieprasījuma katrai uzņemšanai. Pilns disks aptur darbību („Pārtraukts: disks pilns. Saglabāts N, F neizdevās”). Skatīt [Ierakstīšanas iestatījumi un režīmi](capture.md#capture-modes), lai iepazītos ar Microsoft Defender piezīmi par augstiem ierakstīšanas ātrumiem.

Līdzvērtīgā CLI (tāds pats backend galapunkts, tāda pati semantika):

```bash
# One synced group, every applicable export level per camera (the default)
chloros-cli lattice array-capture -o output/

# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# 30-second monitoring clip of the combined index view, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# 5-second analysis-grade raw burst, then build the combined index video
chloros-cli lattice array-burst --duration 5 --build --products combined:index --fps 10 -o capture/
```

TIFF kompresija uzņemtajiem attēliem ir `deflate` (bez zudumiem, noklusējums) vai `none` — pilnās karogu tabulas, uzņemto attēlu mapes struktūra un pārstrādes noteikumi ir atrodami [CLI atsauces materiālā](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## DAQ gaismas sensora pārošana

Lai iegūtu atstarojuma un apgaismojuma korekciju priekšskatījumus, ir nepieciešami lejupvērstās gaismas dati no DAQ sensora (kas pieslēgts cilnē **Gaismas sensori**):

* Sānu joslā **masīva rindā**redzams**"DAQ · ieslēgts/izslēgts" pogu** — *ieslēgts*, ja ir iestatīts masīva līmeņa gaismas sensors **vai** kādai no masīva kamerām ir savs sensors; tā rīkjoslā ir precīzi norādīts, kurš sensors apgādā kuru kameru.
* Piesakiet masīvam kopīgu iestatījumu masīva iestatījumos → **Vides gaismas sensors**→**Gaismas sensors** nolaižamajā izvēlnē. Izvēle saglabājas kopā ar projektu, tiek piemērota visām masīva kamerām, un atsevišķas kameras joprojām var to pārrakstīt ar savu sensoru.
* Stāvokļa rinda zem tās parāda pašreizējo stāvokli: **Izslēgts**→ „Gaida pirmo spektru…” →**„Aktīvs — visām masīva kamerām ir veikta apgaismojuma korekcija”** → vai, ja pēdējo 3 s laikā nav saņemts jauns spektrs, parādās paziņojums par novecojušiem datiem — turpina izmantot pēdējo nolasījumu (nolasījumi nekad nezaudē spēku uzņemšanas ceļā).

Ja ir piešķirts sensors: kļūst pieejams eksporta veids „Reflectance”, reāllaika priekšskatījumi tiek koriģēti atbilstoši apgaismojumam, prognozējošā automātiskā ekspozīcija var izmantot spektru, un katrs atstarojuma uzņemšanas process ieraksta faktiski izmantoto DAQ rādījumu kā **`.daq` failu** uzņemumu mapē `daq/` mapē, lai uzņemumu vēlāk varētu pārstrādāt. Turklāt `.daq.cameras.json` nosauc kameras, kuru kadrus šis rādījums apraksta; pārstrādājot, Chloros saskaņo rādījumu tikai ar attēliem no šīm kamerām.

## `array-connect` CLI opcijas

| Opcija | Noklusējums | Apraksts |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automātiski atklāt visas LATTICE kameras (nepieciešamas ≥2) | **Pirmais sērijas numurs ir MASTER.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinhronizācijas līnija. |
| `--target-fps F` | auto | Galvenā slēdža izšaušanas ātrums. |
| `--binning {1,2,4}` | auto | Aparatūras binning. Iestatījums „auto” nozīmē pilnu sensora izmantošanu (bez binninga), ja kāds kadru ātrums to atbalsta; savienojums nekad neizmanto binningu, lai saglabātu augstu ātrumu. |
| `--packed {auto,on,off}` | `auto` | [Saspiesta 12-bitu pārraide](#packed-12-bit-transport): `auto` veic saspiešanu tikai tad, ja tas palīdz, un ja saimniekdators spēj veikt atspiešanu. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | auto | Sinhronizācijas līmeņa izvēlētāja pārrakstīšana ekspertu režīmā. |
| `--wire-ceiling-mbps MB_PER_S` | automātiski noteikts | Hostinga vadu budžets MB/s — **Vadu budžeta** lauka CLIs forma. Samaziniet to, ja masīvs ziņo par bojātiem rāmjiem. Tiek saglabāts kopā ar projektu, tādējādi vēlāk, atkārtoti izveidojot savienojumu, tas tiek atjaunots. |
| `--no-recommend` | izslēgts | Izlaist tīkla analīzes posmu. |
| `--no-ptp` | izslēgts | Atspējot PTP (tad kameru savstarpējie laika zīmogi nav salīdzināmi). |

`lattice array-list`, `array-status` un `array-disconnect` pārvalda pastāvīgo sesiju. Pilnīga apakškommandu atsauces informācija, ieskaitot saskaņošanu (`align-calibrate` / `align-apply`) un tīkla rīkus, atrodama [CLI atsauces sadaļā § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); SDK ekvivalenti (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) ir atrodami [SDK atsauces materiālā](../reference/sdk-reference.md). Pythonā datu pārraides budžets ir `connect_array(..., wire_ceiling_mbps=120)`, bet sadalījums starp bojātiem un nepiegādātiem datiem atrodams [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames).
