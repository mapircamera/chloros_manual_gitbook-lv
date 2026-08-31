# Failu pievienošana projektam

Kad esat izveidojis vai atvēris projektu programmā Chloros, nākamais solis ir pievienot multispektrālos attēlus, lai sāktu apstrādi. Cilne „Failu pārlūks” (<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">) atvieglo attēlu importēšanu un datu kopas pārvaldīšanu.

## Piekļuve failu pārlūkam

1. Atveriet vai izveidojiet projektu programmā Chloros
2. Noklikšķiniet uz ikonas **Failu pārlūks** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> kreisajā sānjoslā
3. Panelī „Failu pārlūks” tiks parādīts jūsu projekta failu saraksts

{% hint style="info" %}
**Atbalstītie failu tipi**:

* **Survey3W / Survey3N**: RAW+JPG pāri un JPG attēli (ieteicams izmantot RAW+JPG)
* **LATTICE**: `.tif` / `.tiff` ieraksti — saglabāti ar kameras vadības programmu Chloros vai ar LATTICE koncentratoru
* **Gaismas sensora dati**: `.daq` ieraksti (DAQ-U/M/E) un DAQ-M `.csv` lejupvērstie reģistrācijas dati — importēti kopā ar attēliem, lai veiktu atstarojuma kalibrēšanu
{% endhint %}

***

## Attēlu pievienošana projektam

Ir divi galvenie veidi, kā pievienot attēlus projektam:

### 1. metode: Failu pievienošana

Izmantojiet šo opciju, lai importētu atsevišķus attēlu failus vai nelielu failu izlasi.

1. Noklikšķiniet uz pogas **&quot;Pievienot failus&quot;** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> failu pārlūka paneļa augšdaļā
2. Atveriet mapi, kurā atrodas jūsu attēli
3. Izvēlieties vienu vai vairākus attēlu failus (turiet nospiestu **Ctrl**, lai atlasītu vairākus failus)
4. Noklikšķiniet uz **&quot;Atvērt&quot;**, lai importētu atlasītos failus

### 2. metode: Pievienot mapi

Izmantojiet šo opciju, lai vienā reizē importētu visus attēlus no mapes. Vienā dialoglodziņā varat atlasīt **vairākas mapes**.

1. Noklikšķiniet uz pogas **&quot;Pievienot mapes&quot;** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> failu pārlūka paneļa augšdaļā
2. Atveriet un izvēlieties mapes, kurās atrodas jūsu uzņemšanas sesijas attēli
3. Noklikšķiniet uz **&quot;Izvēlēties mapes&quot;**, lai importētu visus atbalstītos attēlus

{% hint style="info" %}
**Tiek ziņots par failiem, kurus neizdodas ielādēt.** Ja mapē ir faili, kurus Chloros atpazīst, bet nevar ielādēt, par to tiek parādīts brīdinājums — attēli netiek klusi pazudināti no režģa.
{% endhint %}

***

## LATTICE uzņemšanas mapju importēšana

LATTICE uzņēmumi tiek saglabāti ar **vienu apakšmapu katram eksporta līmenim** — piemēram, `raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` — ar atbilstošo lejupvērsto failu `.daq` saknes direktorijā:

```
output/
├── raw/           capture_<timestamp>_SN<serial>_raw.tif
├── debayered/     capture_<timestamp>_SN<serial>_debayered.tif
├── preview/       capture_<timestamp>_SN<serial>_display.tif
└── *.daq          the downwelling reading matched to the capture
```

**Norādiet mapes atrašanās vietu uzņemto attēlu saknes mapē** (`output/` augstāk). Ja izvēlētajā mapē pašā nav attēlu, bet tajā ir apakšmapes, Chloros automātiski pārskata tās — visas līmeņa apakšmapes un saknes mape `.daq` tiek ievāktas vienā reizē.**Kā notiek attēlu importēšana:*** Katrs attēls tiek importēts kā **viens attēls**, grupēts pēc attēla (nevis viens ieraksts katrā līmenī). Pārējie tā paša attēla līmeņi parādās kā šī viena attēla skatīšanas režīmi.
* **Apstrāde vienmēr sākas no neapstrādātā kadra.** Pārējie līmeņi ir apskatāmi, taču caur apstrādes ķēdi tiek virzīts tikai `raw` — jau apstrādātā produkta atkārtota apstrāde divkārši piemērotu korekcijas, tādēļ Chloros tiek noraidīts. Atkārtoti importēts eksporta fails nekad nevar aizņemt uzņemuma neapstrādātā faila vietu.
* Uzņemumu mape, kas saglabāta **bez** neapstrādāto failu importēšanas, tiek parādīta kā parasti, taču apstrāde to izlaiž un par to ziņo žurnālā. (Šajā gadījumā CLI karodziņš `--input-level` var piespiest ieejas punktu — skatiet [CLI atsauci](../reference/cli-reference.md#what-a-captures-folder-looks-like).)**LATTICE hub sesijas** importē tāpat: norādiet „Add Folder” (Pievienot mapi) uz sesijas mapi, kas kopēta no koncentratora (tajā ir `raw/` un `previews/`), kopā ar jebkuru DAQ-M `.csv` lejupvērsto žurnālu. Ja kameras vai DAQ kalibrēšanas dati vēl nav saglabāti jūsu datorā, Chloros tos importēšanas laikā automātiski lejupielādē pēc sērijas numura (vienreiz nepieciešams interneta pieslēgums).***

## Failu pārlūka tabulas izpratne

Kad attēli ir importēti, tie parādās tabulā ar šādām kolonnām:

### Faila nosaukums

* Orijinālais faila nosaukums no kameras
* Saglabā kameras nosaukumu veidošanas konvenciju (piem., IMG\_0001.RAW vai capture\_20260816\_101500\_SN213800234\_raw.tif)

### Laika zīmogs

* Attēla uzņemšanas datums un laiks
* Iegūts no attēla EXIF metadatiem
* Tiek izmantots gaismas sensora saskaņošanai, PPK sinhronizācijai un kalibrēšanas mērķu plānošanai

### Kameras modelis

* Automātiski noteikta kameras un filtra konfigurācija
* Survey3 piemēri: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* LATTICE piemēri: LATT-M3M-L41-F550, LATT-M3C-L87-FRGN
* Izmanto, lai piemērotu pareizos apstrādes profilus

### Mērķu kolonna (izvēles rūtiņa)

* Atzīmējiet šo izvēles rūtiņu attēliem, kuros ir kalibrēšanas mērķi
* Ja ir atzīmēts vismaz viens attēls, mērķu meklēšanai **tiek skenēti tikai atzīmētie attēli*** Sīkāku informāciju skatiet sadaļā [Mērķa attēlu izvēle](choosing-target-images.md)

### Attēla metadatu apskatīšana

Noklikšķinot uz pārslēgšanas pogas tabulas augšējā labajā stūrī, izvēlētā attēla metadati tiek parādīti attēlu režģa zonā.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Gaismas sensoru faili jūsu projektā

* Faili `.daq` un `.csv` parādās failu pārlūka sarakstā, bet tie nav attēli, uz kuriem var noklikšķināt — tie nodrošina lejupvērsto starojuma intensitāti atstarojuma kalibrēšanai.
* Katrs importētais `.daq`/`.csv` failu ir uzskaitīts sadaļā **Projekta iestatījumi → DAQ gaismas sensors**, kur varat pārskatīt katram failam piemēroto difuzora vāciņa korekciju. Skatīt [Projekta iestatījumu pielāgošana](adjusting-project-settings.md).
* Ieraksti, kurus veicat cilnē **Gaismas sensori**, tiek automātiski pievienoti atvērtam projektam — manuāla importēšana nav nepieciešama.***

## Failu pārvaldība projektā

### Failu noņemšana

Lai no projekta noņemtu nevajadzīgos attēlus:

1. Izvēlieties vienu vai vairākus attēlus failu pārlūka tabulā
2. Noklikšķiniet uz pogas **&quot;Noņemt atlasītos&quot;** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
3. Apstipriniet noņemšanu (faili netiek dzēsti no diska, tie tiek noņemti tikai no projekta)

### Šķirošana un filtrēšana

* **Šķirošana pēc kolonnas**: noklikšķiniet uz jebkuras kolonnas virsraksta, lai šķirotu attēlus
* **Šķirošana pēc laika zīmoga**: noderīga, lai sakārtotu hronoloģiskas uzņemšanas secības
* **Filtrs pēc kameras modeļa**: grupējiet attēlus pēc kameras tipa, ja izmantojat vairākas kameras***

## Attēla priekšskatījums

### Pilna attēla apskate

Noklikšķiniet uz jebkuras attēla miniaturas failu pārlūkā, lai to parādītu galvenajā priekšskatījuma zonā:

1. Attēls parādās centrālajā priekšskatījuma panelī
2. Izmantojiet tālummaiņas vadības elementus, lai apskatītu attēla detaļas
3. Pārvietojieties starp attēliem, izmantojot bultu taustiņus

### Ātrā navigācija

* **Iepriekšējais attēls**: noklikšķiniet uz kreisās bultiņas vai nospiediet taustiņu ←
* **Nākamais attēls**: noklikšķiniet uz labās bultiņas vai nospiediet taustiņu →
* **Tuvināšana/attālināšana**: izmantojiet peles ratiņu vai tālummaiņas pogas
* **Pārvietošana**: kad attēls ir tuvināts, noklikšķiniet uz tā un velciet***

## Dublikātu apstrāde

Chloros automātiski atpazīst un ignorē dublikātus:

* Faili ar identiskiem nosaukumiem tiek izlaisti
* Novērš nejaušu divkāršu apstrādi
* Tiek parādīts brīdinājuma ziņojums, ja tiek atklāti dublikāti

{% hint style="warning" %}
**Svarīgi**: Pirms importēšanas nepārdēvējiet un nemainiet oriģinālos attēlu failus. Chloros pareizai apstrādei izmanto oriģinālos failu nosaukumus un metadatus.
{% endhint %}

***

## Jaukti kameru datu kopumi

Ja jūsu projektā ir attēli no vairākām MAPIR kamerām:

1. Chloros automātiski atpazīst katru kameras modeli — Survey3, LATTICE vai to kombināciju
2. Katrs kameras tips tiek apstrādāts ar atbilstošu kalibrēšanas profilu
3. Failu pārlūkā kameras modelis tiek parādīts kolonnā „Kameras modelis”
4. Apstrādes laikā katrai kamerai tiek piešķirta sava izvades mapju struktūra

**Piemēri**: Survey3W RGN + Survey3N OCN divu kameru konfigurācija, vai LATTICE masīvs ar RGB galveno kameru un vairākiem šaurjoslas moduļiem***

## Labākā prakse

### Sakārtošana pirms importēšanas

* Glabājiet kalibrēšanas mērķa attēlus tajā pašā mapē, kurā atrodas uzņemto attēlu mapes
* Katras uzņemšanas sesijas `.daq` / `.csv` gaismas sensoru failus glabājiet kopā ar šīs sesijas attēliem
* Saglabājiet sākotnējo mapju struktūru no jūsu kameras/SD kartes/koncentratora
* Vienā projektā nesajauciet datu kopas no dažādām sesijām

### Failu nosaukumu piešķiršana

* Saglabājiet sākotnējos kameras failu nosaukumus (IMG\_0001.RAW, capture\_..., utt.)
* Pirms importēšanas nepārdēvējiet failus
* Sākotnējie nosaukumi satur svarīgus metadatus

### Kalibrēšanas mērķa attēli

* Vienmēr iekļaujiet 1–2 kalibrēšanas mērķa attēlus katrā sesijā (Survey3; LATTICE gadījumā tos var aizstāt ar DAQ ierakstu — skatiet [Mērķa attēlu izvēle](choosing-target-images.md))
* Uztveriet mērķus pirms un pēc uzņemšanas sesijas
* Novietojiet mērķus tādos pašos apgaismojuma apstākļos kā uzņemšanas zonā
* Atzīmējiet mērķa attēlus, izmantojot izvēles rūtiņu „Target”

***

## Bieži sastopamas problēmas un to risinājumi

### Attēli neparādās pēc importēšanas

**Iespējamie iemesli:**

* Faila formāts netiek atbalstīts (skatiet atbalstīto formātu sarakstu šīs lapas augšdaļā)
* Attēli ir uzņemti ar kamerām, kas nav MAPIR (skatiet [Atbalstītās kameras](../supported-cameras.md))
* Fails ir bojāts vai pārraide no SD kartes nav pabeigta

**Risinājums**: Pārbaudiet faila formāta un kameras modeļa savietojamību, kā arī pārbaudiet failu ielādes brīdinājumu, lai noskaidrotu, kuri konkrēti faili netika ielādēti

### Kameras modelis netiek atpazīts

**Iespējamie iemesli:**

* Pārveidoti EXIF metadati
* Attēli ir rediģēti ārējā programmā
* Nepilnīga failu pārsūtīšana

**Risinājums**: No jauna importējiet oriģinālos, nemodificētos failus no kameras/SD kartes

### Trūkst laika zīmogu

**Iespējamie cēloņi:**

* Kameras pulkstenis nav pareizi iestatīts
* EXIF dati ir izdzēsti ar ārējo programmatūru

**Risinājums**: Pārbaudiet, vai kameras laika iestatījumi uzņemšanas brīdī bija pareizi

### Atkārtoti atvērts projekts ziņo par trūkstošiem failiem

Ja avota faili ir pārvietoti vai izdzēsti kopš pēdējās reizes, kad projekts tika atvērts, Chloros norāda, **kuri** faili ir pazuduši, nevis atver tukšu tabulu. Atjaunojiet failus to sākotnējās atrašanās vietās vai izdzēsiet trūkstošos ierakstus un importējiet tos no jauna.***

## Turpmākie soļi

Kad faili ir importēti:

1. **Pārskatiet failu sarakstu** — pārliecinieties, ka visi attēli ir ielādēti pareizi
2. **Pārbaudiet kameru modeļus** — pārliecinieties, ka kameras ir atpazītas pareizi
3. **Atzīmējiet mērķa attēlus** — skatiet [Mērķa attēlu izvēle](choosing-target-images.md)
4. **Pielāgojiet iestatījumus** — konfigurējiet apstrādes opcijas [Projekta iestatījumos](adjusting-project-settings.md)
5. **Sāciet apstrādi** — skatiet [Apstrādes sākšana](starting-the-processing.md)

Sīkāku informāciju par projekta konfigurāciju skatiet sadaļā [Projekta iestatījumu pielāgošana](adjusting-project-settings.md).
