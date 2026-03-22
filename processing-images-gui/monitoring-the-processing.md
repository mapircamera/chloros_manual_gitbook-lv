# Apstrādes uzraudzība

Kad apstrāde ir sākusies, Chloros piedāvā vairākus veidus, kā uzraudzīt apstrādes gaitu, pārbaudīt, vai nav radušās problēmas, un izprast, kas notiek ar jūsu datu kopu. Šajā lapā ir izskaidrots, kā sekot līdzi apstrādei un interpretēt informāciju, ko sniedz Chloros.

## Progresa joslas pārskats

Progresa josla augšējā galvenajā joslā parāda apstrādes statusu reālajā laikā un pabeigšanas procentuālo daļu.

### Progresa josla bezmaksas režīmā

Lietotājiem bez Chloros+ licences:

**2 posmu progresa attēlojums:**

1.**Mērķa noteikšana** — kalibrēšanas mērķu meklēšana attēlos
2. **Apstrāde** — korekciju piemērošana un eksportēšana**Progresa josla parāda:**

* Kopējo pabeigšanas procentuālo daļu (0–100 %)
* Pašreizējā posma nosaukumu
* Vienkāršu horizontālu joslas vizualizāciju

### Chloros+ progresa josla

Lietotājiem ar Chloros+ licenci:

**4 posmu progresa indikators:**

1.**Atklāšana** – kalibrēšanas mērķu atrašana
2. **Analīze** – attēlu pārbaude un procesa sagatavošana
3. **Kalibrēšana** – vinjetes un atstarošanas korekciju piemērošana
4. **Eksportēšana** – apstrādāto failu saglabāšana**Interaktīvās funkcijas:*** **Pavelciet peles kursoru** virs progresa joslas, lai redzētu izvērsto 4 posmu paneli
* **Noklikšķiniet** uz progresa joslas, lai fiksētu/piestiprinātu izvērsto paneli
* **Noklikšķiniet atkārtoti**, lai atbloķētu un automātiski paslēptu, kad peles kursors tiek novilkts
* Katrs posms parāda individuālo progresu (0–100 %)

***

## Katra apstrādes posma izpratne

{% hint style="info" %}
**Pipeline arhitektūra**: Šie 4 GUI posmi atbilst [4-diegu apstrādes pipeline](../processing-architecture/processing-pipeline.md). Sistēmās ar GPU paātrinājumu, 3. diegs (Kalibrēšana) izmanto [Dinamisko aprēķinu pielāgošanu](../processing-architecture/dynamic-compute-adaptation.md), kas optimizē apstrādi jūsu konkrētajai aparatūrai.
{% endhint %}

### 1. posms: Atklāšana (mērķa atklāšana)

**Kas notiek:**

* Chloros skenē attēlus, kas atzīmēti ar izvēles rūtiņu „Mērķis”
* Datorredzes algoritmi identificē 4 kalibrēšanas paneļus
* No katra paneļa tiek iegūtas atstarošanas vērtības
* Tiek reģistrēti mērķa laika zīmogi pareizai kalibrēšanas plānošanai

**Ilgums:**

* Ar atzīmētiem mērķiem: 10–60 sekundes
* Bez atzīmētiem mērķiem: 5–30+ minūtes (skenē visus attēlus)

**Progresa indikators:**

* Atklāšana: 0% → 100%
* Skenēto attēlu skaits
* Atrasto mērķu skaits

**Uz ko jāpievērš uzmanība:**

* Ja mērķi ir pareizi atzīmēti, procesam jābūt ātram
* Ja process ilgst pārāk ilgi, iespējams, mērķi nav atzīmēti
* Pārbaudiet Debug Log, vai tajā ir ziņojumi &quot;Target found&quot;

### 2. posms: Analīze

**Kas notiek:**

* Attēla EXIF metadatu nolasīšana (laika zīmogi, ekspozīcijas iestatījumi)
* Kalibrēšanas stratēģijas noteikšana, pamatojoties uz mērķu laika zīmogiem
* Attēlu apstrādes rindas organizēšana
* Paralēlās apstrādes darba procesu sagatavošana (tikai Chloros+)

**Ilgums:** 5–30 sekundes**Progresa indikators:**

* Analīze: 0% → 100%
* Ātrs posms, parasti pabeidzas ātri

**Uz ko jāpievērš uzmanība:**

* Procesam jānorit vienmērīgi bez pārtraukumiem
* Brīdinājumi par trūkstošajiem metadatiem parādīsies Debug Log

### 3. posms: Kalibrēšana

**Kas notiek:*** **Debayering**: RAW Bayer modeļa konvertēšana uz 3 kanāliem
* **Vignette korekcija**: Objektīva malu tumšuma noņemšana
* **Atstarošanas kalibrēšana**: Normalizēšana ar mērķa vērtībām
* **Indeksa aprēķināšana**: Multispektrālo indeksu aprēķināšana
* Katra attēla apstrāde visā procesā

**Ilgums:** Lielākā daļa no kopējā apstrādes laika (60–80 %)**Progresa indikators:**

* Kalibrēšana: 0% → 100%
* Pašreizējais apstrādājamais attēls
* Apstrādātie attēli / Kopējais attēlu skaits

**Apstrādes darbība:*** **Brīvais režīms**: Apstrādā vienu attēlu pēc otra secīgi
* **Chloros+ režīms**: Apstrādā līdz 16 attēliem vienlaikus
* **GPU paātrinājums**: Ievērojami paātrina šo posmu**Uz ko jāpievērš uzmanība:**

* Vienmērīga attēlu skaita palielināšanās
* Pārbaudiet Debug Log, lai redzētu ziņojumus par katra attēla pabeigšanu
* Brīdinājumi par attēla kvalitāti vai kalibrēšanas problēmām

### 4. posms: Eksportēšana

**Kas notiek:**

* Kalibrēto attēlu ierakstīšana diskā izvēlētajā formātā
* Multispektrālo indeksa attēlu eksportēšana ar LUT krāsām
* Kameras modeļu apakšmapju izveide
* Orijinālo failu nosaukumu saglabāšana ar atbilstošiem paplašinājumiem

**Ilgums:** 10–20 % no kopējā apstrādes laika**Progresa indikators:**

* Eksportēšana: 0 % → 100 %
* Failu ierakstīšana
* Eksporta formāts un galamērķis

**Uz ko jāpievērš uzmanība:**

* Brīdinājumi par diska vietu
* Failu ierakstīšanas kļūdas
* Visu konfigurēto izvades datu pabeigšana

***

## Sadaļa „Debug Log” (Debug žurnāls)

Debug žurnāls sniedz detalizētu informāciju par apstrādes gaitu un jebkurām radušāmies problēmām.

### Piekļuve debug žurnālam

1. Noklikšķiniet uz ikonas **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> ikona kreisajā sānjoslā
2. Atveras žurnāla panelis, kurā tiek parādīti apstrādes ziņojumi reālajā laikā
3. Automātiski ritina uz leju, lai parādītu jaunākos ziņojumus

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

**Rīcība:** Pārskatiet brīdinājumus pēc apstrādes, bet neapturiet to

#### Kļūdu ziņojumi (Red)

Kritiskas problēmas, kas var izraisīt apstrādes kļūdu:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Rīcība:** Pārtrauciet apstrādi, novēršiet kļūdu un sāciet no jauna

### Bieži sastopami žurnāla ziņojumi

| Ziņojums                          | Nozīme                                | Nepieciešamā rīcība                                         |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| &quot;Mērķis atrasts \[faila nosaukums]&quot; | Kalibrēšanas mērķis veiksmīgi atrasts  | Nav - normāli                                         |
| &quot;Apstrādā attēlu X no Y&quot;        | Pašreizējā apstrādes gaita                | Nav - normāli                                         |
| &quot;Mērķi nav atrasti&quot;               | Kalibrēšanas mērķi nav atrasti        | Atzīmējiet mērķa attēlus vai atspējojiet atstarojuma kalibrēšanu |
| &quot;Nepietiekama diska vieta&quot;        | Nepietiekama vieta izvades datiem          | Atbrīvojiet diska vietu                                    |
| &quot;Izlaiž bojātu failu&quot;        | Attēla fails ir bojāts                  | Atkārtoti kopējiet failu no SD kartes                             |
| &quot;PPK dati piemēroti&quot;               | GPS korekcijas no .daq faila piemērotas | Nav - normāli                                         |

### Žurnāla datu kopēšana

Lai kopētu žurnālu problēmu novēršanai vai atbalsta dienestam:

1. Atveriet paneļa &quot;Debug Log&quot;
2. Noklikšķiniet uz pogas **&quot;Copy Log&quot;** (vai noklikšķiniet ar peles labo pogu → Izvēlēties visu)
3. Ielīmējiet teksta failā vai e-pastā
4. Vajadzības gadījumā nosūtiet MAPIR atbalsta dienestam

***

## Sistēmas resursu uzraudzība

### CPU izmantošana

**Bezmaksas režīms:**

* 1 CPU kodols pie ~100%
* Pārējie kodoli neaktīvi vai pieejami
* Sistēma joprojām reaģē

**Chloros+ Paralēlais režīms:**

* Vairāki kodoli pie 80–100% (līdz 16 kodoliem)
* Augsta kopējā CPU izmantošana
* Sistēma var šķist mazāk reaģējoša

**Lai uzraudzītu:**

* Windows Uzdevumu pārvaldnieks (Ctrl+Shift+Esc)
* Sadaļa „Veiktspēja” → Sadaļa „CPU”
* Meklējiet procesus „Chloros” vai „chloros-backend”

### Atmiņas (RAM) izmantošana

**Tipiska izmantošana:**

* Mazi projekti (&lt; 100 attēli): 2–4 GB
* Vidēji projekti (100–500 attēli): 4–8 GB
* Lieli projekti (500+ attēli): 8–16 GB
* Chloros+ paralēlais režīms izmanto vairāk RAM

**Ja atmiņas ir maz:**

* Apstrādājiet mazākas partijas
* Aizveriet citas programmas
* Ja regulāri apstrādājat lielus datu kopumus, palieliniet RAM

### GPU izmantošana (Chloros+ ar CUDA)

Kad ir ieslēgta GPU paātrināšana:

* NVIDIA GPU rāda augstu izmantošanu (60–90 %)
* Palielinās VRAM izmantošana (nepieciešams 4 GB+ VRAM)
* Kalibrēšanas posms ir ievērojami ātrāks

**Lai uzraudzītu:**

* NVIDIA sistēmas paneli ikona
* Uzdevumu pārvaldnieks → Veiktspēja → GPU
* GPU-Z vai līdzīgs uzraudzības rīks

### Diska I/O

**Ko gaidīt:**

* Augsta diska lasīšanas intensitāte analīzes posmā
* Augsta diska rakstīšanas intensitāte eksportēšanas posmā
* SSD ir ievērojami ātrāks nekā HDD

**Padoms par veiktspēju:**

* Ja iespējams, izmantojiet SSD projekta mapes glabāšanai
* Izvairieties no tīkla diskiem lielu datu kopu gadījumā
* Pārliecinieties, ka disks nav gandrīz pilns (ietekmē rakstīšanas ātrumu)

***

## Problēmu atklāšana apstrādes laikā

### Brīdinājuma pazīmes

**Progress apstājas (nekādas izmaiņas vairāk nekā 5 minūtes):**

* Pārbaudiet kļūdas atkļūdošanas žurnālā
* Pārbaudiet pieejamo diska vietu
* Pārbaudiet uzdevumu pārvaldnieku, lai pārliecinātos, ka Chloros darbojas

**Kļūdu ziņojumi parādās bieži:**

* Pārtrauciet apstrādi un pārskatiet kļūdas
* Biežākie iemesli: diska vieta, bojāti faili, atmiņas problēmas
* Skatīt sadaļu „Problēmu novēršana” zemāk

**Sistēma nereaģē:**

* Chloros+ paralēlais režīms izmanto pārāk daudz resursu
* Apsvērt vienlaicīgo uzdevumu skaita samazināšanu vai aparatūras modernizēšanu
* Brīvais režīms ir mazāk resursietilpīgs

### Kad pārtraukt apstrādi

Pārtrauciet apstrādi, ja redzat:

* ❌ Kļūdas &quot;Disks pilns&quot; vai &quot;Nevar ierakstīt failu&quot;
* ❌ Atkārtotas attēlu failu bojājumu kļūdas
* ❌ Sistēma ir pilnībā iesaldējusies (nereagē)
* ❌ Ir konstatēts, ka ir konfigurēti nepareizi iestatījumi
* ❌ Ir importēti nepareizi attēli

**Kā pārtraukt:**

1. Noklikšķiniet uz**Pārtraukt/Atcelt pogas** (aizstāj Sākt pogu)
2. Apstrāde tiek apturēta, progress tiek zaudēts
3. Novēršiet problēmas un sāciet no sākuma

***

## Problēmu novēršana apstrādes laikā

### Apstrāde ir ļoti lēna

**Iespējamie iemesli:**

* Neatzīmēti mērķa attēli (tiek skenēti visi attēli)
* HDD vietā tiek izmantota SSD atmiņa
* Nepietiekami sistēmas resursi
* Konfigurēti daudzi indeksi
* Piekļuve tīkla diskam

**Risinājumi:**

1. Ja tikko sākts un atrodas atklāšanas posmā: atceliet, atzīmējiet mērķus, sāciet no jauna
2. Turpmāk: izmantojiet SSD, samaziniet indeksu skaitu, uzlabojiet aparatūru
3. Apsveriet CLI lietošanu lielu datu kopu partiju apstrādei

### Brīdinājumi par &quot;diska vietu&quot;

**Risinājumi:**

1. Nekavējoties atbrīvojiet diska vietu
2. Pārvietojiet projektu uz disku ar lielāku vietu
3. Samaziniet eksportējamo indeksu skaitu
4. Izmantojiet JPG formātu, nevis TIFF (mazāki faili)

### Bieži parādās ziņojumi par &quot;bojātiem failiem&quot;

**Risinājumi:**

1. Atkārtoti nokopējiet attēlus no SD kartes, lai nodrošinātu to integritāti
2. Pārbaudiet SD karti uz kļūdām
3. No projekta izdzēsiet bojātos failus
4. Turpiniet apstrādāt atlikušos attēlus

### Sistēmas pārkaršana / ātruma ierobežošana

**Risinājumi:**

1. Nodrošiniet pietiekamu ventilāciju
2. Notīriet putekļus no datora ventilācijas atverēm
3. Samaziniet apstrādes slodzi (izmantojiet Free režīmu, nevis Chloros+)
4. Veiciet apstrādi vēsākā dienas laikā

***

## Paziņojums par apstrādes pabeigšanu

Kad apstrāde ir pabeigta:

* Progresa josla sasniedz 100%
* **&quot;Apstrāde pabeigta&quot;** ziņojums parādās Debug Log
* Sākuma poga atkal kļūst pieejama
* Visi izvades faili atrodas kameras modeļa apakšmapē

***

## Nākamie soļi

Kad apstrāde ir pabeigta:

1. **Pārskatiet rezultātus** - Skatīt [Apstrādes pabeigšana](finishing-the-processing.md)
2. **Pārbaudiet izvades mapi** — pārliecinieties, ka visi faili ir eksportēti pareizi
3. **Pārskatiet debug logu** — pārbaudiet, vai nav brīdinājumu vai kļūdu
4. **Priekšskatiet apstrādātos attēlus** — izmantojiet attēlu skatītāju vai ārējo programmatūru

Informāciju par apstrādāto rezultātu pārskatīšanu un izmantošanu skatiet sadaļā [Apstrādes pabeigšana](finishing-the-processing.md).
