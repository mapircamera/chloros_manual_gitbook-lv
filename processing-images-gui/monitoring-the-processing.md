# Apstrādes uzraudzība

Kad apstrāde ir sākusies, Chloros piedāvā vairākus veidus, kā uzraudzīt progresu, pārbaudīt, vai nav radušās problēmas, un izprast, kas notiek ar jūsu datu kopu. Šajā lapā ir izskaidrots, kā sekot līdzi apstrādes gaitai un interpretēt informāciju, ko sniedz Chloros.

## Progresa joslas pārskats

Progresa josla augšējā galvenajā joslā parāda apstrādes statusu reālajā laikā un pabeigšanas procentuālo daļu. Progresa dati tiek pārraidīti tiešraidē no servera, izmantojot Server-Sent Events (SSE), tādējādi josla atspoguļo to, ko apstrādes procesa ķēde faktiski dara.

### Progresa josla bezmaksas režīmā

Lietotājiem bez „Chloros+” licences:

**2 posmu progresa indikators:**

1.**Mērķu noteikšana** — kalibrēšanas mērķu meklēšana attēlos
2. **Apstrāde** — korekciju piemērošana un eksportēšana**Progresa josla parāda:**

* Kopējo pabeigšanas procentu (0–100 %)
* Pašreizējā posma nosaukums
* Vienkārša horizontāla joslas vizualizācija

### „Chloros+“ progresa josla

Lietotājiem ar „Chloros+“ licenci:

**4 posmu progresa attēlojums:**

1.**Atklāšana** — kalibrēšanas mērķu meklēšana
2. **Analīze** — attēlu pārbaude un apstrādes procesa sagatavošana
3. **Kalibrēšana** – vinjetes un atstarošanas korekciju piemērošana
4. **Eksportēšana** – apstrādāto failu saglabāšana**Interaktīvās funkcijas:*** **Pavelciet peles kursoru** pār progresa joslu, lai redzētu izvērsto 4 posmu paneli
* **Noklikšķiniet** uz progresa joslas, lai iesaldētu/fiksētu izvērsto paneli
* **Noklikšķiniet vēlreiz**, lai atbloķētu, un panelis automātiski paslēpsies, kad peles kursors tiks novilkts prom
* Katrs posms parāda individuālo progresu (0–100 %)

{% hint style="info" %}
**CLI-paritāte**: `chloros-cli process` darbības laikā tie paši četri pavedieni ziņo par „Detecting”, „Analyzing”, „Processing” un „Exporting”, savukārt `chloros-cli export-status` rāda reāllaika 4. pavediena eksportēšanas progresu no cita termināļa. Skatīt [CLI atsauci](../reference/cli-reference.md).
{% endhint %}

***

## Katra apstrādes posma izpratne

{% hint style="info" %}
**Pipeline arhitektūra**: Šie 4 GUI posmi atbilst [4-thread apstrādes pipeline](../processing-architecture/processing-pipeline.md). Sistēmās ar GPU paātrinājumu 3. pavediens (kalibrēšana) izmanto [dinamisko aprēķinu pielāgošanu](../processing-architecture/dynamic-compute-adaptation.md), kas optimizē apstrādi jūsu konkrētajai aparatūrai.
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

* Atklāšana: 0 % → 100 %
* Skenēto attēlu skaits (tiek skaitīti tikai tie attēli, kas faktiski tiek skenēti)
* Atrasto mērķu skaits

**Uz ko jāpievērš uzmanība:**

* Ja mērķi ir pareizi atzīmēti, procesam jānorit ātri
* Ja process ilgst pārāk ilgi, iespējams, mērķi nav atzīmēti
* Pārbaudiet atkļūdošanas žurnālu, lai atrastu ziņojumus „Mērķis atrasts“

### 2. posms: Analīze

**Kas notiek:**

* Attēlu EXIF metadatu nolasīšana (laika zīmogi, ekspozīcijas iestatījumi)
* Kalibrēšanas stratēģijas noteikšana, balstoties uz mērķu laika zīmogiem un pieejamajiem DAQ lejupvērstajiem datiem
* Attēlu apstrādes rindas organizēšana
* Paralēlās apstrādes darba procesu sagatavošana (Tikai Chloros+)

**Ilgums:** 5–30 sekundes**Progresa indikators:**

* Analizēšana: 0 % → 100 %
* Ātrs posms, parasti pabeidzas ātri

**Uz ko jāpievērš uzmanība:**

* Procesam jānorit vienmērīgi bez pārtraukumiem
* Brīdinājumi par trūkstošajiem metadatiem parādīsies Debug Log

### 3. posms: Kalibrēšana

**Kas notiek:*** **Debayering**: RAW Bayer modeļa konvertēšana uz 3 kanāliem (LATTICE mono moduļiem tiek izlaists, ar piezīmi)
* **Vignette korekcija**: objektīva malu tumšuma novēršana
* **Atstarošanas kalibrēšana**: normalizēšana atbilstoši mērķa vērtībām un/vai DAQ lejupvērstajai starojuma intensitātei
* **Indeksu aprēķināšana**: multispektrālo indeksu aprēķināšana
* Katra attēla apstrāde visā apstrādes procesā

**Ilgums:** Lielākā daļa no kopējā apstrādes laika (60–80 %)**Progresa indikators:**

* Kalibrēšana: 0 % → 100 %
* Pašreizējais attēls tiek apstrādāts
* Apstrādātie attēli / Kopējais attēlu skaits

**Apstrādes darbības veids:*** **Brīvais režīms**: secīgi apstrādā vienu attēlu pēc otra
* **„Chloros+“ režīms**: izmanto aparatūrai pielāgotu darba procesu kopu — 1–4 vienlaicīgi darbojošies darba procesi GPU sistēmās (atkarībā no VRAM), viens darba process uz katru fizisko kodolu (mīnus viens) sistēmās, kurās ir tikai CPU. Skatīt [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU paātrinājums**: ievērojami paātrina šo posmu**Uz ko jāpievērš uzmanība:**

* Vienmērīga attēlu skaita palielināšanās
* Pārbaudiet atkļūdošanas žurnālu, lai redzētu paziņojumus par katra attēla apstrādes pabeigšanu
* Brīdinājumi par attēlu kvalitāti vai kalibrēšanas problēmām

### 4. posms: Eksportēšana

**Kas notiek:**

* Apstrādāto attēlu ierakstīšana diskā izvēlētajā formātā, tiklīdz tie ir pabeigti
* **LATTICE**: katrs kadrs tiek sadalīts pa visiem aktivizētajiem produktiem (debayered / preview / radiance / reflectance)
* Multispektrālo indeksa attēlu eksportēšana ar LUT krāsām
* Izvades koka `<project>/<camera>/<format>/<Product>_Images/` izveide — eksportētajiem failiem tiek saglabāts avota faila nosaukums; mape identificē produktu

**Ilgums:** 10–20 % no kopējā apstrādes laika**Progresa indikators:**

* Eksportēšana: 0 % → 100 %
* Notiek failu ierakstīšana
* Eksporta formāts un galamērķis

**Uz ko jāpievērš uzmanība:**

* Brīdinājumi par diska vietas trūkumu
* Failu ierakstīšanas kļūdas
* Visu konfigurēto izvades rezultātu pabeigšana

***

## Sadaļa „Debug Log” (Debug žurnāls)

Debug žurnāls sniedz detalizētu informāciju par apstrādes gaitu un visām radušajām problēmām. Arī backend palaišanas ziņojumi tiek atskaņoti žurnāla konsolē, tādējādi žurnāls sniedz pilnīgu pārskatu, pat ja to atverat vēlāk.

### Piekļuve „Debug Log”

1. Noklikšķiniet uz ikonas **„Debug Log”** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

kreisajā sānjoslā
2. Atveras žurnāla panelis, kurā tiek parādīti apstrādes ziņojumi reālajā laikā
3. Notiek automātiska ritināšana, lai parādītu jaunākos ziņojumus

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written), with the 1.3.0 bug icon (Report a bug) visible at the top of the tab -->

### Žurnāla ziņojumu izpratne

Chloros žurnāla rindām priekšā ir iekavās norādītas apzīmējumi, kas nosauc apakšsistēmu — piemēram, `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Svarīgākais, kas jāzina, ir **izpildes kopsavilkums**, kas tiek izdrukāts katras izpildes beigās (ieskaitot apturētās izpildes):

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Papildu `[RUN-SUMMARY]` norāžu rindas seko ikreiz, kad kaut kas ir jāpaskaidro — piemēram, izpilde, kas nedeva nekādu rezultātu, vai kamera, kuras pieprasītais rezultāts tika izlaists kā nepiemērots. `[EXPORT-CHECK]` rindas izskaidro izlaišanas iemeslus katrai kamerai atsevišķi (piemēram, kāpēc RGB-kamera nesaņēma starojuma rezultātu).

Vispārējie ziņojumu nopietnības līmeņi (zemāk minētie piemēri ir ilustratīvi, nevis burtiski):

#### Informatīvie ziņojumi (balti/pelēki)

Parastās apstrādes atjauninājumi: apstrāde uzsākta, mērķi atklāti (ar paneļu skaitu), kalibrēšanas gaita katram attēlam, faili eksportēti, apstrāde pabeigta.

#### Brīdinājuma ziņojumi (dzeltenā krāsā)

Nekritiskas problēmas, kas neaptur apstrādi — piemēram, trūkstoši GPS dati kadrā, liels laika zīmju atšķirības intervāls starp mērķa attēliem vai zems kontrasts kalibrēšanas panelī.

**Rīcība:** Pārskatiet brīdinājumus pēc apstrādes pabeigšanas, bet neapturiet apstrādi

#### Kļūdu ziņojumi (sarkanā krāsā — Red)

Kritiskas problēmas, kas var izraisīt apstrādes neveiksmi — piemēram, disks ir pilns, bojāts attēla fails vai nav atklāti mērķi, kamēr tika pieprasīta atstarojuma kalibrēšana.

**Rīcība:** Pārtrauciet apstrādi, novēršiet kļūdu un sāciet no jauna

### Bieži sastopamas situācijas žurnālā

| Situācija                             | Nozīme                                       | Nepieciešamā rīcība                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| Mērķis atklāts failā \[faila nosaukums]        | Kalibrēšanas mērķis veiksmīgi atrasts         | Nav — normāli                                         |
| Progresa līnijas katram attēlam              | Pašreizējā progresa atjauninājums                       | Nav — normāli                                         |
| Mērķi nav atrasti                      | Nav atklāti kalibrēšanas mērķi               | Atzīmējiet mērķa attēlus vai atspējojiet atstarošanas kalibrēšanu |
| Nepietiekama diska vieta               | Nav pietiekami daudz vietas izvades datiem                 | Atbrīvojiet diska vietu                                    |
| Izlaiž bojātu failu               | Attēla fails ir bojāts                         | Atkārtoti nokopējiet failu no SD kartes                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Uztveršanu bez neapstrādāta kadra nevar apstrādāt | Veiciet atkārtotu uztveršanu ar neapstrādātu kadru, vai izmantojiet CLI `--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | Apstrāde neradīja attēlu rezultātus — ziņots kā kļūda ar norādēm | Izlasiet norāžu rindas; pārbaudiet, kas tika izlaists un kāpēc |
| `[PROCESS] refused: The project folder is too deep for this computer…` | Windows garie ceļi ir atspējoti, un projekta mape atrodas pārāk dziļi, lai ievērotu 260 rakstzīmju ceļa ierobežojumu; nekas netika apstrādāts | Izmantojiet īsāku projekta mapi (ziņojumā norādīts garākais pieļaujamais ceļš), vai ieslēdziet Windows garos ceļus un pārstartējiet Chloros — skatiet [Apstrādes uzsākšana](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer) |
| `[unmix-clamp-mask] …` | Neizdevās ierakstīt LATTICE produkta fiksācijas masku; pats produkts darbojas pareizi un parāda kļūdu `Chloros:UnmixClampMask=unavailable` | Pārbaudiet diska vietu un mapes atļaujas, pēc tam atkārtojiet apstrādi, ja maska jums ir nepieciešama |

### Žurnāla datu kopēšana

Lai kopētu žurnālu problēmu novēršanai vai atbalsta nolūkos:

1. Atveriet paneļa „Debug Log” (Debug žurnāls)
2. Noklikšķiniet uz pogas **Copy**(kopē žurnālu uz starpliktuvi) vai**Download** (lejupielādēt), lai saglabātu to kā teksta failu
3. Ielīmējiet teksta failā vai e-pastā
4. Vajadzības gadījumā nosūtiet to MAPIR atbalsta dienestam

### Kļūdas ziņošana no cilnes „Debug Log”

Jaunums versijā 1.3.0: kļūdas ikona (**Ziņot par kļūdu**) cilnes „Debug Log” augšdaļā nosūta ziņojumu tieši uz MAPIR.

1. Noklikšķiniet uz kļūdas ikonas cilnes „Debug Log” augšdaļā
2. Aprakstiet problēmu: ko jūs darījāt, ko gaidījāt un kas notika tā vietā (obligāti)
3. Pēc izvēles atzīmējiet **Pievienot dažus attēlus (līdz 10)**un izvēlieties attēlus no atvērtā projekta vai izmantojiet**Pārlūkot...**, lai pievienotu jebkuru citu attēla failu
4. Noklikšķiniet uz **Nosūtīt**

Ziņojumā ir iekļauts jūsu apraksts, visi pievienotie attēli, Chloros žurnāli un sistēmas informācija (Chloros versija, operētājsistēma, GPU un projekta iestatījumi). Lai nosūtītu ziņojumu, jums ir jābūt pieteicies Chloros (cilne „Lietotājs“). Nosūtīšanas laikā logu var aizvērt; kad nosūtīšana būs pabeigta, žurnālā tiks ierakstīts atsauces numurs, piemēram, `Bug report sent. Reference: BR-…` — norādiet to, sazinoties ar MAPIR. Ja ziņojumu nevar augšupielādēt (piemēram, nav interneta savienojuma), Chloros to saglabā jūsu datorā un parāda ceļu, lai jūs to varētu nosūtīt pa e-pastu uz support@mapir.camera.

***

## Sistēmas resursu uzraudzība

### Procesora izmantošana

**Brīvais režīms:**

* 1 procesora kodols darbojas ar ~100 % slodzi
* Pārējie kodoli atrodas dīkstāves režīmā vai ir pieejami
* Sistēma joprojām reaģē

**„Chloros+“ paralēlais režīms:**

* Vairāki kodoli darbojas ar augstu noslogojumu — to skaits ir atkarīgs no stratēģijas, kas izvēlēta ar [Dinamisko aprēķinu pielāgošanu](../processing-architecture/dynamic-compute-adaptation.md)
* Sistēma var šķist mazāk reaģējoša

**Lai uzraudzītu:**

* Windows uzdevumu pārvaldnieks (Ctrl+Shift+Esc)
* Sadaļa „Veiktspēja” → Sadaļa „Procesors”
* Meklējiet procesus „Chloros” vai „chloros-backend”

### Atmiņas (RAM) izmantošana

**Tipiska izmantošana:**

* Nelieli projekti (< 100 attēli): 2–4 GB
* Vidēji projekti (100–500 attēli): 4–8 GB
* Lieli projekti (500+ attēli): 8–16 GB
* „Chloros+“ paralēlais režīms patērē vairāk RAM

**Ja atmiņas ir maz:**

* Apstrādājiet mazākas partijas
* Aizveriet citas programmas
* Ja regulāri apstrādājat lielus datu kopumus, palieliniet RAM apjomu

### GPU izmantošana („Chloros+” ar CUDA)

Ja ir ieslēgta GPU paātrināšana:

* NVIDIA GPU izmantošanas līmenis ir augsts (60–90 %)
* Palielinās VRAM izmantošana (nepieciešami 4 GB+ VRAM; 7 GB+ vienlaicīgai Texture Aware debayering apstrādei)
* Kalibrēšanas posms noris ievērojami ātrāk

**Lai uzraudzītu:**

* NVIDIA sistēmas paneli ikona
* Uzdevumu pārvaldnieks → Veiktspēja → GPU
* GPU-Z vai līdzīgs uzraudzības rīks

### Diska I/O

**Ko sagaidīt:**

* Augsta diska lasīšanas intensitāte analīzes posmā
* Augsta diska rakstīšanas intensitāte eksportēšanas posmā
* SSD ir ievērojami ātrāks nekā HDD

**Veiktspējas padoms:**

* Ja iespējams, izmantojiet SSD projekta mapes glabāšanai
* Izvairieties no tīkla diskiem lielu datu kopu gadījumā
* Pārliecinieties, ka disks nav gandrīz pilns (ietekmē rakstīšanas ātrumu)

***

## Problēmu atklāšana apstrādes laikā

### Brīdinājuma pazīmes

**Progress apstājas (nekādas izmaiņas 5+ minūtes):**

* Pārbaudiet kļūdu žurnālu (Debug Log)
* Pārbaudiet pieejamo diska vietu
* Pārbaudiet uzdevumu pārvaldnieku (Task Manager), lai pārliecinātos, ka Chloros darbojas

**Bieži parādās kļūdu ziņojumi:**

* Pārtrauciet apstrādi un pārskatiet kļūdas
* Biežākie cēloņi: diska vietas trūkums, bojāti faili, atmiņas problēmas
* Skatiet sadaļu „Problēmu novēršana” zemāk

**Sistēma vairs nereaģē:**

* Chloros paralēlais režīms patērē pārāk daudz resursu
* Apsveriet vienlaicīgo uzdevumu skaita samazināšanu vai aparatūras modernizēšanu
* Brīvais režīms patērē mazāk resursu

### Kad pārtraukt apstrādi

Pārtrauciet apstrādi, ja redzat:

* ❌ Kļūdas „Disks pilns” vai „Nevar ierakstīt failu”
* ❌ Atkārtotas attēlu failu bojājumu kļūdas
* ❌ Sistēma ir pilnībā iesaldējusies (nereagē)
* ❌ Ir konstatēts, ka ir konfigurēti nepareizi iestatījumi
* ❌ Ir importēti nepareizi attēli

**Kā pārtraukt:**

1. Noklikšķiniet uz**Pārtraukt pogas** (aizstāj Sākt pogu) — pietiek ar vienu reizi
2. Lente rāda „Pārtraucot...”, kamēr tiek pabeigta iesākta attēla apstrāde, pēc tam apstrāde beidzas pārtrauktajā stāvoklī
3. Jau eksportētie produkti paliek diskā; žurnālā tiek izdrukāts precīzs `[RUN-SUMMARY]` ziņojums par to, kas ir pabeigts
4. Novēršiet problēmas un restartējiet — apstrāde sākas no sākuma

***

## Problēmu novēršana apstrādes laikā

### Apstrāde notiek ļoti lēni

**Iespējamie cēloņi:**

* Neatzīmēti mērķa attēli (tiek skenēti visi attēli)
* HDD vietā tiek izmantota SSD atmiņa
* Nepietiekami sistēmas resursi
* Konfigurēti daudzi indeksi
* Piekļuve tīkla diskam

**Risinājumi:**

1. Ja apstrāde tikko sākta un atrodas atklāšanas posmā: apturiet, atzīmējiet mērķus, restartējiet
2. Turpmāk: izmantojiet SSD, samaziniet indeksu skaitu, modernizējiet aparatūru
3. Apsveriet iespēju izmantot CLI, lai masveidā apstrādātu lielus datu kopumus

### Brīdinājumi par „diska vietu”

**Risinājumi:**

1. Nekavējoties atbrīvojiet diska vietu
2. Pārvietojiet projektu uz disku ar lielāku vietu
3. Samaziniet eksportējamo indeksu skaitu
4. Atvienojiet nevajadzīgos LATTICE eksporta produktus (Projekta iestatījumi → Apstrāde)
5. Izmantojiet JPG formātu, nevis TIFF u (mazāki faili)

### Bieži parādās ziņojumi par „bojātiem failiem”

**Risinājumi:**

1. Atkārtoti nokopējiet attēlus no SD kartes, lai nodrošinātu to integritāti
2. Pārbaudiet SD karti uz kļūdām
3. No projekta izdzēsiet bojātos failus
4. Turpiniet pārējā attēlu apstrādi

### Sistēmas pārkaršana / veiktspējas ierobežošana

**Risinājumi:**

1. Nodrošiniet pietiekamu ventilāciju
2. Notīriet putekļus no datora ventilācijas atverēm
3. Samaziniet apstrādes slodzi (izmantojiet „Free” režīmu, nevis „Chloros+”)
4. Veiciet apstrādi vēsākā dienas laikā

***

## Paziņojums par apstrādes pabeigšanu

Kad apstrāde ir pabeigta:

* Progresa josla sasniedz 100 %
* Debug Log parādās rindas „`[RUN-SUMMARY]`” ar galīgajiem skaitļiem
* Poga „Start” atkal kļūst aktīva
* Visi izvades faili atrodas projekta izvades kokā, kas sadalīts pa kamerām: `<project>/<camera>/<format>/<Product>_Images/`

***

## Turpmākie soļi

Kad apstrāde ir pabeigta:

1. **Pārskatiet rezultātus** — skatiet [Apstrādes pabeigšana](finishing-the-processing.md)
2. **Pārbaudiet izvades mapi** — pārliecinieties, ka visi faili ir eksportēti pareizi
3. **Pārskatiet atkļūdošanas žurnālu** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Priekšskatiet apstrādātos attēlus** — izmantojiet attēlu skatītāju vai ārējo programmatūru

Informāciju par apstrādāto rezultātu pārskatīšanu un izmantošanu skatiet sadaļā [Apstrādes pabeigšana](finishing-the-processing.md).
