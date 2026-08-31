# Apstrādes uzraudzība

Kad apstrāde ir sākusies, Chloros piedāvā vairākus veidus, kā uzraudzīt apstrādes gaitu, pārbaudīt, vai nav radušās problēmas, un izprast, kas notiek ar jūsu datu kopu. Šajā lapā ir izskaidrots, kā sekot līdzi apstrādei un interpretēt informāciju, ko sniedz Chloros.

## Progresa joslas pārskats

Progresa josla augšējā galvenajā joslā parāda apstrādes statusu reālajā laikā un pabeigšanas procentu. Progresa dati tiek pārraidīti tiešraidē no servera, izmantojot Server-Sent Events (SSE), tādējādi josla atspoguļo to, ko apstrādes ceļš faktiski dara.

### Progresa josla bezmaksas režīmā

Lietotājiem bez Chloros+ licences:

**2 posmu progresa attēlošana:**

1.**Mērķa noteikšana** — kalibrēšanas mērķu meklēšana attēlos
2. **Apstrāde** — korekciju piemērošana un eksportēšana**Progresa josla parāda:**

* Kopējo pabeigšanas procentuālo daļu (0–100 %)
* Pašreizējā posma nosaukumu
* Vienkāršu horizontālu joslas vizualizāciju

### Chloros+ progresa josla

Lietotājiem ar Chloros+ licenci:

**4 posmu progresa indikators:**

1.**Atklāšana** – kalibrēšanas mērķu atrašana
2. **Analīze** – attēlu pārbaude un apstrādes procesa sagatavošana
3. **Kalibrēšana** – vinjetes un atstarošanas korekciju piemērošana
4. **Eksportēšana** – apstrādāto failu saglabāšana**Interaktīvās funkcijas:*** **Pavelciet peles kursoru pāri** progresa joslai, lai redzētu izvērsto 4 posmu paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu izvērsto paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu paneli, un tā automātiski paslēpsies, kad peles kursors tiks novirzīts prom
* Katrs posms parāda individuālo progresu (0–100 %)

{% hint style="info" %}
**CLI paritāte**: `chloros-cli process` darbības laikā tie paši četri pavedieni ziņo par „Detecting”, „Analyzing”, „Processing”, „Eksportēšana”, savukārt `chloros-cli export-status` rāda 4. pavediena eksportēšanas gaitu reāllaikā no citas termināļa. Skatīt [CLI atsauci](../reference/cli-reference.md).
{% endhint %}

***

## Katra apstrādes posma izpratne

{% hint style="info" %}
**Konveijera arhitektūra**: Šie 4 GUI posmi atbilst [4-pavedienu apstrādes konveijeram](../processing-architecture/processing-pipeline.md). Sistēmās ar GPU paātrinājumu 3. pavediens (kalibrēšana) izmanto [dinamisko aprēķinu pielāgošanu](../processing-architecture/dynamic-compute-adaptation.md), kas optimizē apstrādi jūsu konkrētajai aparatūrai.
{% endhint %}

### 1. posms: Atklāšana (mērķa atklāšana)

**Kas notiek:**

* Chloros skenē attēlus, kurus esat atzīmējuši ar izvēles rūtiņu „Mērķis” (visus attēlus, ja neviens nav atzīmēts)
* Datorredzes algoritmi identificē kalibrēšanas paneļus
* No katra paneļa tiek iegūtas atstarošanas vērtības
* Tiek reģistrēti mērķu laika zīmogi, lai nodrošinātu pareizu kalibrēšanas plānošanu

**Ilgums:**

* Ar atzīmētiem mērķiem: 10–60 sekundes
* Bez atzīmētiem mērķiem: 5–30+ minūtes (tiek skenēti visi attēli)

**Progresa indikators:**

* Atpazīšana: 0 % → 100 %
* Skenēto attēlu skaits (skaita tikai tos attēlus, kas faktiski tiek skenēti)
* Atrasto mērķu skaits

**Uz ko jāpievērš uzmanība:**

* Ja mērķi ir pareizi atzīmēti, procesam jānorit ātri
* Ja process ilgst pārāk ilgi, iespējams, mērķi nav atzīmēti
* Pārbaudiet debug žurnālu, vai tajā ir ziņojumi „Target found“

### 2. posms: Analīze

**Kas notiek:**

* Attēlu EXIF metadatu nolasīšana (laika zīmogi, ekspozīcijas iestatījumi)
* Kalibrēšanas stratēģijas noteikšana, balstoties uz mērķu laika zīmogiem un pieejamajiem DAQ lejupvērstajiem datiem
* Attēlu apstrādes rindas organizēšana
* Paralēlās apstrādes darba procesu sagatavošana (tikai Chloros+)

**Ilgums:** 5–30 sekundes**Progresa indikators:**

* Analizēšana: 0 % → 100 %
* Ātrs posms, parasti pabeidzas ātri

**Uz ko jāpievērš uzmanība:**

* Procesam jānorit vienmērīgi bez pārtraukumiem
* Brīdinājumi par trūkstošajiem metadatiem parādīsies debug žurnālā

### 3. posms: Kalibrēšana

**Kas notiek:*** **Debayering**: RAW Bayer modeļa konvertēšana uz 3 kanāliem (LATTICE mono moduļiem tiek izlaista, ar piezīmi)
* **Vignettinga korekcija**: objektīva malu tumšuma novēršana
* **Atstarošanas kalibrēšana**: normalizēšana atbilstoši mērķa vērtībām un/vai DAQ lejupvērstajai starojuma intensitātei
* **Indeksu aprēķināšana**: multispektrālo indeksu aprēķināšana
* Katra attēla apstrāde visā apstrādes procesā

**Ilgums:** Lielākā daļa no kopējā apstrādes laika (60–80 %)**Progresa indikators:**

* Kalibrēšana: 0 % → 100 %
* Pašreizējais attēls tiek apstrādāts
* Apstrādātie attēli / Kopējais attēlu skaits

**Apstrādes darbība:*** **Brīvais režīms**: secīgi apstrādā vienu attēlu pēc otra
* **Chloros+ režīms**: izmanto aparatūrai pielāgotu darba grupu — 1–4 vienlaicīgi darbojošies darba procesi GPU sistēmās (atkarībā no VRAM), viens darba process uz katru fizisko kodolu (mīnus viens) sistēmās, kurās ir tikai CPU. Skatīt [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU paātrinājums**: Ievērojami paātrina šo posmu**Uz ko jāpievērš uzmanība:**

* Vienmērīga attēlu skaita palielināšanās
* Pārbaudiet atkļūdošanas žurnālu, lai redzētu paziņojumus par katra attēla apstrādes pabeigšanu
* Brīdinājumi par attēlu kvalitāti vai kalibrēšanas problēmām

### 4. posms: Eksportēšana

**Kas notiek:**

* Apstrādāto attēlu ierakstīšana diskā izvēlētajā formātā, tiklīdz tie ir pabeigti
* **LATTICE**: katrs kadrs tiek sadalīts pa visiem aktivizētajiem produktiem (debayering / priekšskatījums / starojums / atstarojums)
* Multispektrālo indeksa attēlu eksportēšana ar LUT krāsām
* Izvades koka `<project>/<camera>/<format>/<Product>_Images/` izveide — eksportētajiem failiem tiek saglabāts avota faila nosaukums; mape identificē produktu

**Ilgums:** 10–20 % no kopējā apstrādes laika**Progresa indikators:**

* Eksportēšana: 0 % → 100 %
* Notiek failu ierakstīšana
* Eksporta formāts un galamērķis

**Uz ko jāpievērš uzmanība:**

* Brīdinājumi par diska vietas trūkumu
* Failu ierakstīšanas kļūdas
* Visu konfigurēto izvadu pabeigšana

***

## Sadaļa „Debug Log” (Atkļūdošanas žurnāls)

Atkļūdošanas žurnāls sniedz detalizētu informāciju par apstrādes gaitu un visām radušajām problēmām. Arī backend palaišanas ziņojumi tiek atskaņoti žurnāla konsolē, tādējādi žurnāls sniedz pilnīgu pārskatu pat tad, ja to atverat vēlāk.

### Kā piekļūt atkļūdošanas žurnālam

1. Noklikšķiniet uz **Atkļūdošanas žurnāls** (<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

) ikonas kreisajā sānjoslā
2. Atveras žurnāla panelis, kurā tiek parādīti apstrādes ziņojumi reāllaikā
3. Ekrāns automātiski ritina uz leju, lai parādītu jaunākos ziņojumus

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written) -->

### Žurnāla ziņojumu izpratne

Chloros žurnāla rindām priekšā ir iekavās ielikti marķieri, kas nosauc apakšsistēmu — piemēram, `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Svarīgākais, kas jāzina, ir **izpildes kopsavilkums**, kas tiek izdrukāts katras izpildes beigās (ieskaitot apturētās izpildes):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Papildu `[RUN-SUMMARY]` norāžu rindas seko ikreiz, kad kaut kas ir jāpaskaidro — piemēram, darbs, kas nedeva nekādu rezultātu, vai kamera, kuras pieprasītais produkts tika izlaists kā nepiemērots. `[EXPORT-CHECK]` rindas paskaidro izlaišanas iemeslus katrai kamerai atsevišķi (piemēram, kāpēc RGB kamera nesaņēma starojuma produktu).

Vispārīgās ziņojumu nopietnības pakāpes (zemāk minētie piemēri ir ilustratīvi, nevis burtiski):

#### Informatīvie ziņojumi (balti/pelēki)

Parastie apstrādes atjauninājumi: apstrāde sākta, mērķi atklāti (ar paneļu skaitu), attēla kalibrēšanas gaita, faili eksportēti, apstrāde pabeigta.

#### Brīdinājuma ziņojumi (dzeltenā krāsā)

Nekritiskas problēmas, kas neaptur apstrādi — piemēram, trūkstoši GPS dati kadrā, liels laika zīmju atšķirības starp mērķa attēliem vai zems kontrasts kalibrēšanas panelī.

**Rīcība:** Pārskatiet brīdinājumus pēc apstrādes, bet neapturiet to

#### Kļūdu ziņojumi (Red)

Kritiskas problēmas, kas var izraisīt apstrādes neveiksmi — piemēram, disks ir pilns, bojāts attēla fails vai nav atklāti mērķi, kamēr tika pieprasīta atstarojuma kalibrēšana.

**Rīcība:** Pārtrauciet apstrādi, novēršiet kļūdu un sāciet no jauna

### Bieži sastopamas situācijas žurnālā

| Situācija                             | Nozīme                                       | Nepieciešamā rīcība                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| Mērķis atrasts failā \[faila nosaukums]        | Kalibrēšanas mērķis veiksmīgi atrasts         | Nav — normāli                                         |
| Progresa līnijas katram attēlam              | Pašreizējā progresa atjauninājums                       | Nav — normāli                                         |
| Mērķi nav atrasti                      | Nav atrasti kalibrēšanas mērķi               | Atzīmējiet mērķa attēlus vai atspējojiet atstarojuma kalibrēšanu |
| Nepietiekama diska vieta               | Nav pietiekami daudz vietas izvadei                 | Atbrīvojiet diska vietu                                    |
| Izlaiž bojātu failu               | Attēla fails ir bojāts                         | Pārkopējiet failu no SD kartes                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Uztveršanu bez neapstrādāta kadra nevar apstrādāt | Veiciet atkārtotu uztveršanu ar neapstrādātu kadru vai izmantojiet CLI `--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Izpildes rezultātā netika iegūti attēlu produkti — ziņots kā kļūda ar norādēm | Izlasiet norāžu rindas; pārbaudiet, kas tika izlaists un kāpēc |

### Žurnāla datu kopēšana

Lai kopētu žurnālu problēmu novēršanai vai atbalsta nolūkos:

1. Atveriet paneļa „Debug Log”
2. Noklikšķiniet uz pogas **„Kopēt žurnālu”** (vai noklikšķiniet ar peles labo pogu → Izvēlēties visu)
3. Ielīmējiet teksta failā vai e-pastā
4. Vajadzības gadījumā nosūtiet MAPIR atbalsta dienestam

***

## Sistēmas resursu uzraudzība

### Procesora izmantošana

**Brīvais režīms:**

* 1 procesora kodols darbojas ar ~100 % noslogojumu
* Pārējie kodoli ir neaktīvi vai pieejami
* Sistēma joprojām reaģē

**Chloros+ paralēlais režīms:**

* Vairāki kodoli ar augstu noslogojumu — to skaits ir atkarīgs no [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md) izvēlētās stratēģijas
* Sistēma var šķist mazāk reaģējoša

**Lai uzraudzītu:**

* Windows Uzdevumu pārvaldnieks (Ctrl+Shift+Esc)
* Sadaļa „Veiktspēja” → Sadaļa „Procesors”
* Meklējiet procesus „Chloros” vai „chloros-backend”

### Atmiņas (RAM) izmantošana

**Tipiskais patēriņš:**

* Mazi projekti (&lt; 100 attēli): 2–4 GB
* Vidēji projekti (100–500 attēli): 4–8 GB
* Lieli projekti (500+ attēli): 8–16 GB
* Chloros+ paralēlais režīms patērē vairāk RAM

**Ja atmiņas ir maz:**

* Apstrādājiet mazākas partijas
* Aizveriet citas programmas
* Palieliniet RAM, ja regulāri apstrādājat lielus datu kopumus

### GPU izmantošana (Chloros+ ar CUDA)

Kad ir ieslēgts GPU paātrinājums:

* NVIDIA GPU rāda augstu izmantošanas pakāpi (60–90 %)
* Palielinās VRAM izmantojums (nepieciešami 4 GB+ VRAM; 7 GB+ vienlaicīgai tekstūru apstrādei ar Texture Aware)
* Kalibrēšanas posms noris ievērojami ātrāk

**Lai uzraudzītu:**

* NVIDIA sistēmas panela ikona
* Uzdevumu pārvaldnieks → Veiktspēja → GPU
* GPU-Z vai līdzīgs uzraudzības rīks

### Diska I/O

**Ko sagaidīt:**

* Augsta diska lasīšanas intensitāte analīzes posmā
* Augsta diska rakstīšanas intensitāte eksportēšanas posmā
* SSD ir ievērojami ātrāks nekā HDD

**Veiktspējas padoms:**

* Ja iespējams, izmantojiet SSD projekta mapes glabāšanai
* Izvairieties no tīkla diskiem, strādājot ar lieliem datu kopumiem
* Pārliecinieties, ka disks nav gandrīz pilns (ietekmē rakstīšanas ātrumu)

***

## Problēmu atklāšana apstrādes laikā

### Brīdinājuma pazīmes

**Progress apstājas (nekādas izmaiņas 5+ minūtes):**

* Pārbaudiet kļūdas atkļūdošanas žurnālā
* Pārbaudiet pieejamo diska vietu
* Pārbaudiet uzdevumu pārvaldnieku, lai pārliecinātos, ka process Chloros darbojas

**Bieži parādās kļūdu ziņojumi:**

* Pārtrauciet apstrādi un pārskatiet kļūdas
* Biežākie cēloņi: diska vietas trūkums, bojāti faili, atmiņas problēmas
* Skatiet sadaļu „Problēmu novēršana” zemāk

**Sistēma vairs nereaģē:**

* Chloros+ paralēlais režīms patērē pārāk daudz resursu
* Apsvērt vienlaicīgo uzdevumu skaita samazināšanu vai aparatūras modernizēšanu
* Brīvais režīms ir mazāk resursietilpīgs

### Kad pārtraukt apstrādi

Pārtrauciet apstrādi, ja redzat:

* ❌ Kļūdas „Disks pilns” vai „Nevar ierakstīt failu”
* ❌ Atkārtotas attēlu failu bojājumu kļūdas
* ❌ Sistēma ir pilnībā iesaldējusies (nereagē)
* ❌ Ir konstatēts, ka ir konfigurēti nepareizi iestatījumi
* ❌ Ir importēti nepareizi attēli

**Kā pārtraukt:**

1. Noklikšķiniet uz**poga „Pārtraukt“** (aizstāj pogu „Sākt“) — pietiek ar vienu reizi
2. Lente rāda „Pārtrauc...“, kamēr tiek pabeigta iesākto attēlu apstrāde, pēc tam apstrāde beidzas pārtrauktajā stāvoklī
3. Jau eksportētie produkti paliek diskā; žurnālā tiek izdrukāts precīzs `[RUN-SUMMARY]` ziņojums par to, kas ir pabeigts
4. Novēršiet problēmas un restartējiet — procesa izpilde sākas no sākuma

***

## Problēmu novēršana apstrādes laikā

### Apstrāde notiek ļoti lēni

**Iespējamie cēloņi:**

* Neatzīmēti mērķa attēli (tiek skenēti visi attēli)
* HDD vietā tiek izmantots SSD disks
* Nepietiekami sistēmas resursi
* Konfigurēti daudzi indeksi
* Piekļuve tīkla diskam

**Risinājumi:**

1. Ja process tikko sākts un atrodas „Detecting” posmā: apturiet, atzīmējiet mērķus, restartējiet
2. Turpmāk: izmantojiet SSD, samaziniet indeksu skaitu, uzlabojiet aparatūru
3. Lielu datu kopu partiju apstrādei apsveriet iespēju izmantot CLI

### Brīdinājumi par „diska vietu”

**Risinājumi:**

1. Nekavējoties atbrīvojiet diska vietu
2. Pārvietojiet projektu uz disku ar lielāku vietu
3. Samaziniet eksportējamo indeksu skaitu
4. Atvienojiet nevajadzīgos LATTICE eksporta produktus (Projekta iestatījumi → Apstrāde)
5. Izmantojiet JPG formātu TIFF vietā (mazāki faili)

### Bieži parādās ziņojumi par „bojātiem failiem”

**Risinājumi:**

1. Atkārtoti nokopējiet attēlus no SD kartes, lai nodrošinātu to integritāti
2. Pārbaudiet SD karti uz kļūdām
3. Izņemiet bojātos failus no projekta
4. Turpiniet apstrādāt atlikušos attēlus

### Sistēmas pārkaršana / veiktspējas ierobežošana

**Risinājumi:**

1. Nodrošiniet pietiekamu ventilāciju
2. Notīriet putekļus no datora ventilācijas atverēm
3. Samaziniet apstrādes slodzi (izmantojiet „Free” režīmu, nevis Chloros+)
4. Veiciet apstrādi vēsākā dienas laikā

***

## Paziņojums par apstrādes pabeigšanu

Kad apstrāde ir pabeigta:

* Progresa josla sasniedz 100%
* Debug Log parādās rindas `[RUN-SUMMARY]` ar galīgajiem skaitļiem
* „Sākt” poga atkal kļūst aktīva
* Visi izvades faili atrodas projekta izvades kokā, kas sadalīts pa kamerām: `<project>/<camera>/<format>/<Product>_Images/`

***

## Turpmākie soļi

Kad apstrāde ir pabeigta:

1. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)
2. **Pārbaudiet izvades mapi** — pārliecinieties, ka visi faili ir eksportēti pareizi
3. **Pārskatiet atkļūdošanas žurnālu** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Apskatiet apstrādātos attēlus** — izmantojiet attēlu skatītāju vai ārējo programmatūru

Informāciju par apstrādāto rezultātu pārskatīšanu un izmantošanu skatiet sadaļā [Apstrādes pabeigšana](finishing-the-processing.md).
