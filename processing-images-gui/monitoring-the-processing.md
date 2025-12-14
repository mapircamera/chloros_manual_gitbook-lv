# Apstrādes uzraudzība

Kad apstrāde ir sākusies, Chloros piedāvā vairākus veidus, kā uzraudzīt progresu, pārbaudīt problēmas un saprast, kas notiek ar jūsu datu kopu. Šajā lapā ir izskaidrots, kā izsekot apstrādei un interpretēt informāciju, ko sniedz Chloros.

## Progresa joslas pārskats

Progresa josla augšējā galvenajā joslā parāda apstrādes statusu un pabeigšanas procentu reālajā laikā.

### Brīvā režīma progresa josla

Lietotājiem bez Chloros+ licences:

**2 posmu progresa parādīšana:**

1. **Mērķa noteikšana** — kalibrēšanas mērķu meklēšana attēlos
2. **Apstrāde** — korekciju piemērošana un eksportēšana

**Progresa josla parāda:**

* Kopējo pabeigšanas procentu (0–100 %)
* Pašreizējā posma nosaukumu
* Vienkāršu horizontālu joslu vizualizāciju

### Chloros+ progresa josla

Lietotājiem ar Chloros+ licenci:

**4 posmu progresa attēlojums:**

1. **Atklāšana** — kalibrēšanas mērķu meklēšana
2. **Analīze** — attēlu pārbaude un cauruļvada sagatavošana
3. **Kalibrēšana** — vinjetes un atstarojuma korekciju piemērošana
4. **Eksportēšana** — apstrādāto failu saglabāšana

**Interaktīvās funkcijas:**

* **Pielieciet peles kursoru** uz progresa joslas, lai redzētu paplašinātu 4 posmu paneli
* **Noklikšķiniet** uz progresa joslas, lai iesaldētu/fiksētu paplašināto paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un automātiski paslēptu, novietojot peles kursoru
* Katrs posms parāda individuālo progresu (0–100 %)

***

## Katra apstrādes posma izpratne

### 1. posms: Atklāšana (mērķa atklāšana)

**Kas notiek:**

* Chloros skenē attēlus, kas atzīmēti ar izvēles rūtiņu Mērķis
* Datorredzes algoritmi identificē 4 kalibrēšanas paneļus
* No katra paneļa tiek iegūtas atstarojuma vērtības
* Mērķu laika zīmogi tiek reģistrēti, lai nodrošinātu pareizu kalibrēšanas plānošanu

**Ilgums:**

* Ar atzīmētiem mērķiem: 10–60 sekundes
* Bez atzīmētiem mērķiem: 5–30+ minūtes (skenē visus attēlus)

**Progresa indikators:**

* Atklāšana: 0% → 100%
* Skenēto attēlu skaits
* Atrasto mērķu skaits

**Kas jāievēro:**

* Ja mērķi ir pareizi atzīmēti, process jāpabeidz ātri.
* Ja process ilgst pārāk ilgi, mērķi var būt neatzīmēti.
* Pārbaudiet Debug Log, vai nav ziņojumu &quot;Target found&quot; (Mērķis atrasts).

### 2. posms: Analīze

**Kas notiek:**

* Attēlu EXIF metadatu lasīšana (laika zīmogi, ekspozīcijas iestatījumi)
* Kalibrēšanas stratēģijas noteikšana, pamatojoties uz mērķu laika zīmogiem
* Attēlu apstrādes rindas organizēšana
* Paralēlās apstrādes darbinieku sagatavošana (tikai Chloros+)

**Ilgums:** 5–30 sekundes

**Progresa indikators:**

* Analīze: 0 % → 100 %
* Ātrs posms, parasti tiek pabeigts ātri

**Kas jāievēro:**

* Progresam jābūt stabilam bez pārtraukumiem
* Brīdinājumi par trūkstošiem metadatiem parādīsies Debug Log

### 3. posms: Kalibrēšana

**Kas notiek:**

* **Debayering**: RAW Bayer modeļa konvertēšana uz 3 kanāliem
* **Vignette korekcija**: objektīva malu tumšuma noņemšana
* **Atstarošanas kalibrēšana**: normalizēšana ar mērķa vērtībām
* **Indeksa aprēķināšana**: daudzspektrālo indeksu aprēķināšana
* Katra attēla apstrāde visā procesā

**Ilgums:** lielākā daļa no kopējā apstrādes laika (60–80 %)

**Progresa indikators:**

* Kalibrēšana: 0 % → 100 %
* Pašreizējais attēls tiek apstrādāts
* Pabeigti attēli / Kopējais attēlu skaits

**Apstrādes darbība:**

* **Brīvais režīms**: secīgi apstrādā vienu attēlu pēc otra
* **Chloros+ režīms**: vienlaikus apstrādā līdz 16 attēliem
* **GPU paātrinājums**: ievērojami paātrina šo posmu

**Kas jāievēro:**

* Stabils progress attēlu skaita ziņā
* Pārbaudiet Debug Log, lai redzētu ziņojumus par katra attēla apstrādes pabeigšanu
* Brīdinājumi par attēla kvalitāti vai kalibrēšanas problēmām

### 4. posms: eksportēšana

**Kas notiek:**

* Kalibrēto attēlu ierakstīšana diskā izvēlētajā formātā
* Daudzspektrālo indeksa attēlu eksportēšana ar LUT krāsām
* Kameras modeļu apakšmapju izveide
* Orijinālo failu nosaukumu saglabāšana ar atbilstošiem papildu nosaukumiem

**Ilgums:** 10–20 % no kopējā apstrādes laika

**Progresa indikators:**

* Eksportēšana: 0 % → 100 %
* Failu rakstīšana
* Eksporta formāts un galamērķis

**Kas jāuzrauga:**

* Diskas vietas brīdinājumi
* Failu rakstīšanas kļūdas
* Visu konfigurēto izvades pabeigšana

***

## Debug Log cilne

Debug Log sniedz detalizētu informāciju par apstrādes gaitu un visām radušajām problēmām.

### Piekļuve Debug Log

1. Noklikšķiniet uz **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> ikonu kreisajā sānjoslā
2. Atveras žurnāla panelis, kurā tiek parādīti reāllaika apstrādes ziņojumi
3. Automātiski tiek parādīti jaunākie ziņojumi

### Žurnāla ziņojumu izpratne

#### Informatīvie ziņojumi (balti/pelēki)

Normālas apstrādes atjauninājumi:

```
[INFO] Processing started
[INFO] Target detected in IMG_0015.RAW - 4 panels found
[INFO] Calibrating IMG_0234.RAW
[INFO] Exported NDVI image: IMG_0234_NDVI.tif
[INFO] Processing complete
```

#### Brīdinājuma ziņojumi (dzelteni)

Nekritiskas problēmas, kas neaptur apstrādi:

```
[WARN] No GPS data found in IMG_0145.RAW
[WARN] Target image timestamp gap > 30 minutes
[WARN] Low contrast in calibration panel - results may vary
```

**Darbība:** Pārskatiet brīdinājumus pēc apstrādes, bet neapturiet to.

#### Kļūdu ziņojumi (Red)

Kritiskas problēmas, kas var izraisīt apstrādes kļūdu:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Darbība:** Pārtrauciet apstrādi, novēršiet kļūdu, restartējiet.

### Bieži sastopami žurnāla ziņojumi

| Ziņojums                          | Nozīme                                | Nepieciešamā darbība                                         |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| &quot;Mērķis atrasts \[faila nosaukums]&quot; | Kalibrēšanas mērķis veiksmīgi atrasts  | Nav - normāli                                         |
| &quot;Apstrādā attēlu X no Y&quot;        | Pašreizējā progresa atjauninājums                | Nav - normāli                                         |
| &quot;Mērķi nav atrasti&quot;               | Kalibrēšanas mērķi nav atrasti        | Atzīmējiet mērķa attēlus vai atspējojiet atstarojuma kalibrēšanu |
| &quot;Nepietiekama diska vieta&quot;        | Nepietiekama atmiņa izvadei          | Atbrīvojiet diska vietu                                    |
| &quot;Izlaiž bojātu failu&quot;        | Attēla fails ir bojāts                  | No jauna kopējiet failu no SD kartes                             |
| &quot;PPK dati piemēroti&quot;               | GPS korekcijas no .daq faila piemērotas | Nav - normāli                                         |

### Žurnāla datu kopēšana

Lai kopētu žurnālu problēmu novēršanai vai atbalstam:

1. Atveriet paneļa Debug Log (Debug žurnāls)
2. Noklikšķiniet uz pogas **&quot;Copy Log&quot;** (Kopēt žurnālu) (vai noklikšķiniet ar peles labo pogu → Select All (Atlasīt visu))
3. Ielīmējiet tekstfailā vai e-pastā
4. Vajadzības gadījumā nosūtiet uz MAPIR atbalsta dienestu

***

## Sistēmas resursu uzraudzība

### CPU izmantošana

**Brīvais režīms:**

* 1 CPU kodols ~100%
* Pārējie kodoli ir neaktīvi vai pieejami
* Sistēma paliek atsaucīga

**Chloros+ Paralēlais režīms:**

* Vairāki kodoli 80-100% (līdz 16 kodoliem)
* Augsta kopējā CPU izmantošana
* Sistēma var šķist mazāk reaģējoša

**Lai uzraudzītu:**

* Windows Uzdevumu pārvaldnieks (Ctrl+Shift+Esc)
* Sadaļa „Veiktspēja” → sadaļa „CPU”
* Meklējiet procesus „Chloros” vai „chloros-backend”

### Atmiņas (RAM) izmantošana

**Tipiska izmantošana:**

* Mazi projekti (&lt; 100 attēli): 2–4 GB
* Vidēji projekti (100–500 attēli): 4–8 GB
* Lieli projekti (500+ attēli): 8–16 GB
* Chloros+ paralēlais režīms izmanto vairāk RAM

**Ja atmiņa ir nepietiekama:**

* Apstrādājiet mazākas partijas
* Aizveriet citas programmas
* Ja regulāri apstrādājat lielus datu kopumus, uzlabojiet RAM

### GPU izmantošana (Chloros+ ar CUDA)

Kad ir ieslēgta GPU paātrināšana:

* NVIDIA GPU rāda augstu izmantošanu (60–90 %)
* VRAM izmantošana palielinās (nepieciešams 4 GB+ VRAM)
* Kalibrēšanas posms ir ievērojami ātrāks

**Lai uzraudzītu:**

* NVIDIA sistēmas paplātes ikona
* Uzdevumu pārvaldnieks → Veiktspēja → GPU
* GPU-Z vai līdzīgs uzraudzības rīks

### Diska I/O

**Ko gaidīt:**

* Augsta diska lasīšana analizēšanas posmā
* Augsta diska rakstīšana eksportēšanas posmā
* SSD ir ievērojami ātrāks nekā HDD

**Veiktspējas padoms:**

* Ja iespējams, izmantojiet SSD projekta mapē
* Izvairieties no tīkla diskiem lieliem datu kopumiem
* Pārliecinieties, ka disks nav tuvu kapacitātes robežai (ietekmē rakstīšanas ātrumu)

***

## Problēmu atklāšana apstrādes laikā

### Brīdinājuma pazīmes

**Progresa apstāšanās (nekādas izmaiņas vairāk nekā 5 minūtes):**

* Pārbaudiet Debug Log, vai nav kļūdas
* Pārbaudiet pieejamo diska vietu
* Pārbaudiet Task Manager, vai darbojas Chloros

**Bieži parādās kļūdu ziņojumi:**

* Pārtrauciet apstrādi un pārbaudiet kļūdas
* Bieži sastopami iemesli: diska vieta, bojāti faili, atmiņas problēmas
* Skatīt sadaļu „Problēmu novēršana” zemāk

**Sistēma nereagē:**

* Chloros+ paralēlais režīms izmanto pārāk daudz resursu
* Apsveriet vienlaicīgo uzdevumu skaita samazināšanu vai aparatūras modernizēšanu
* Brīvais režīms ir mazāk resursietilpīgs

### Kad pārtraukt apstrādi

Pārtrauciet apstrādi, ja redzat:

* ❌ Kļūdas „Disks pilns” vai „Nevar rakstīt failu”
* ❌ Atkārtotas attēlu failu bojājumu kļūdas
* ❌ Sistēma ir pilnībā iesaldēta (nereagē)
* ❌ Ir konstatēts, ka ir konfigurēti nepareizi iestatījumi
* ❌ Ir importēti nepareizi attēli

**Kā pārtraukt:**

1. Noklikšķiniet uz **poga &quot;Pārtraukt/Atcelt&quot;** (aizstāj pogu &quot;Sākt&quot;)
2. Apstrāde tiek pārtraukta, progress tiek zaudēts
3. Novēršiet problēmas un sāciet no sākuma

***

## Problēmu novēršana apstrādes laikā

### Apstrāde ir ļoti lēna

**Iespējamie iemesli:**

* Neatzīmēti mērķa attēli (visu attēlu skenēšana)
* HDD vietā SSD uzglabāšana
* Nepietiekami sistēmas resursi
* Konfigurēti daudzi indeksi
* Tīkla diska piekļuve

**Risinājumi:**

1. Ja tikko sākts un atrodas atklāšanas posmā: atceliet, atzīmējiet mērķus, sāciet no jauna
2. Nākotnei: izmantojiet SSD, samaziniet indeksus, uzlabojiet aparatūru
3. Apsveriet CLI izmantošanu lielu datu kopu partiju apstrādei

### Brīdinājumi par &quot;diska vietu&quot;

**Risinājumi:**

1. Nekavējoties atbrīvojiet diska vietu
2. Pārvietojiet projektu uz disku ar lielāku vietu
3. Samaziniet eksportējamo indeksu skaitu.
4. Izmantojiet JPG formātu, nevis TIFF (mazāki faili).

### Bieži parādās ziņojumi par bojātiem failiem

**Risinājumi:**

1. Atkārtoti kopējiet attēlus no SD kartes, lai nodrošinātu integritāti.
2. Pārbaudiet SD karti, vai tajā nav kļūdas.
3. No projekta izdzēsiet bojātos failus.
4. Turpiniet apstrādāt atlikušos attēlus.

### Sistēmas pārkaršana/droseles ierobežošana

**Risinājumi:**

1. Nodrošiniet pietiekamu ventilāciju.
2. Notīriet putekļus no datora ventilācijas atverēm.
3. Samaziniet apstrādes slodzi (izmantojiet bezmaksas režīmu, nevis Chloros+).
4. Veiciet apstrādi vēsākā dienas laikā.

***

## Paziņojums par apstrādes pabeigšanu

Kad apstrāde ir pabeigta:

* Progresa josla sasniedz 100 %
* Debug Log parādās ziņojums **&quot;Processing Complete&quot;** (Apstrāde pabeigta)
* Start pogas atkal kļūst pieejamas
* Visi izvades faili atrodas kameras modeļa apakšmapē

***

## Nākamie soļi

Kad apstrāde ir pabeigta:

1. **Pārskatiet rezultātus** - Skatīt [Apstrādes pabeigšana](finishing-the-processing.md)
2. **Pārbaudiet izvades mapes** — pārbaudiet, vai visi faili ir eksportēti pareizi
3. **Pārskatiet debug log** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Priekšskatiet apstrādātos attēlus** — izmantojiet Image Viewer vai ārējo programmatūru

Informāciju par apstrādāto rezultātu pārskatīšanu un izmantošanu skatiet [Apstrādes pabeigšana](finishing-the-processing.md).
