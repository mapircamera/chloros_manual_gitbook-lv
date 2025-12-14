# Apstrādes pabeigšana

Kad Chloros ir pabeidzis apstrādi, ir pienācis laiks pārskatīt rezultātus, pārbaudīt izvades kvalitāti un sagatavot apstrādātos attēlus izmantošanai darba plūsmā. Šī lapa palīdzēs jums veikt pēdējos soļus un nākamās darbības.

## Apstrādes pabeigšanas indikators

Kad apstrāde ir veiksmīgi pabeigta, redzēsiet vairākus indikatorus:

* ✅ **Progresa josla**: sasniedz 100 % pabeigšanu
* ✅ **Debug Log**: parāda ziņojumu &quot;Processing Complete&quot; (Apstrāde pabeigta)
* ✅ **Sākt pogu**: atkal kļūst pieejama (gatava nākamajai apstrādei)
* ✅ **Izvades faili**: visi apstrādātie attēli saglabāti kameras modeļa apakšmapē

***

## Apstrādāto attēlu atrašana

### Izvades mapes atvēršana

1. Noklikšķiniet uz **galvenās izvēlnes** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> (kreisajā augšējā stūrī)
2. Izvēlieties **&quot;Atvērt projekta mapi&quot;**
3. Jūsu failu pārlūks atveras projekta direktorijā
4. Atrodiet savu projektu pēc nosaukuma

***

## Apstrādāto attēlu pārskatīšana

### Ātrā priekšskatīšana failu pārlūkprogrammā

**Windows iebūvēta priekšskatīšana:**

1. Pāriet uz kameras modeļa apakšmapes
2. Izvēlieties attēla failu
3. Priekšskatīšana parādās Windows Explorer priekšskatīšanas logā
4. Izmantojiet bultu taustiņus, lai pārlūkotu attēlus

### Pārskatīšana ārējos attēlu skatītājos

**Ieteicamie skatītāji:**

* **QGIS** - bezmaksas GIS programmatūra (vislabāk piemērota ģeogrāfiski atsauktu multispektrālo analīžu veikšanai)
* **IrfanView** - ātrs, viegls attēlu skatītājs (atbalsta TIFF)
* **Adobe Photoshop** — profesionāla rediģēšana (atbalsta TIFF)
* **GIMP** — bezmaksas alternatīva Photoshop
* **Windows Photos** — pamata apskate (var neatbalstīt 16 bitu TIFF)

### Priekšskatīšana Chloros attēlu skatītājā

Izmantojiet Chloros iebūvēto attēlu skatītāju, lai veiktu uzlabotu vizualizāciju:

1. Noklikšķiniet uz attēla sīktēla failu pārlūkā
2. Attēls atveras galvenajā priekšskatīšanas zonā
3. Noklikšķiniet uz **Attēlu skatītājs** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> cilni kreisajā sānjoslā.
4. Interaktīvai analīzei izmantojiet [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md).

Sīkākas instrukcijas skatiet [Image Viewer](../image-viewer-gui/opening-an-image-full-screen.md).

***

## Debug Log pārskatīšana

### Pārbaudiet brīdinājumus vai kļūdas

1. Atveriet **Debug Log** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> cilni
2. Pārskatiet ziņojumus
3. Meklējiet dzeltenos brīdinājumus vai sarkanās kļūdas
4. Pārskatiet visas atzīmētās problēmas
5. Sazinieties ar MAPIR atbalsta dienestu, lai saņemtu palīdzību

### Žurnāla saglabāšana

Lai saglabātu apstrādes ierakstu vai nosūtītu to MAPIR atbalsta dienestam:

1. Noklikšķiniet uz pogas **&quot;Kopēt&quot;** vai **&quot;Lejupielādēt&quot;**
2. Saglabājiet kā teksta failu projekta mapē
3. Iekļaujiet projekta dokumentācijā
4. Nosūtiet MAPIR atbalsta dienestam, ja rodas problēmas

***

## Bieži sastopamas izvades problēmas un to risinājumi

### Problēma: trūkstoši izvades faili

**Iespējamie cēloņi:**

* Faili neatbilst apstrādes kritērijiem
* Tikai mērķa attēli (izslēgti no eksporta)
* Eksporta laikā beidzās diska vieta
* Failu bojājums apstrādes laikā

**Risinājumi:**

1. Pārbaudiet Debug Log, vai nav izlaides/kļūdu ziņojumu
2. Pārbaudiet, vai diska vietas ir pietiekami
3. Saskaitiet failus: jāatbilst (sākotnējais skaits - mērķa skaits) × (indeksi + 1)
4. Atkārtoti importējiet un apstrādājiet trūkstošos failus

### Problēma: tumšas vai gaišas malas (joprojām redzama vinjetēšana)

**Iespējamie cēloņi:**

* Vinjetēšanas korekcija ir atspējota
* Kamera/objektīvs nav Chloros profilu datu bāzē
* Ekstrēma vinjetēšana, kas pārsniedz korekcijas iespējas

**Risinājumi:**

1. Pārbaudiet, vai vinjetēšanas korekcija ir ieslēgta projekta iestatījumos
2. Pārbaudiet, vai kameras modelis ir pareizi atpazīts
3. Ja vinjetēšana joprojām ir redzama, sazinieties ar MAPIR atbalsta dienestu

### Problēma: Nepareizas krāsas vai vērtības

**Iespējamie cēloņi:**

* Nav atrasti kalibrēšanas mērķi
* Izvēlēts nepareizs kalibrēšanas mērķa modelis
* Atstarošanas kalibrēšana ir atspējota
* Mērķa attēlu kvalitāte ir slikta

**Risinājumi:**

1. Pārbaudiet, vai ir ieslēgta atstarojuma kalibrēšana.
2. Pārbaudiet ziņojumus „Mērķis atrasts” debug žurnālā.
3. Pārbaudiet mērķa attēla kvalitāti.
4. Veiciet atkārtotu apstrādi, atzīmējot pareizos mērķus.

### Problēma: NDVI vērtības šķiet nepareizas

**Paredzamie NDVI diapazoni:**

* **Ūdens, akmeņi, augsne**: no -0,1 līdz 0,2
* **Retā/neveselīga veģetācija**: no 0,2 līdz 0,4
* **Vidēja veģetācija**: no 0,4 līdz 0,6
* **Veselīga, blīva veģetācija**: no 0,6 līdz 0,9

**Ja vērtības ir ārpus šiem diapazoniem:**

1. Pārbaudiet, vai ir veikta atstarojuma kalibrēšana.
2. Pārbaudiet, vai ir iekļauts gaismas sensora žurnāls.
3. Pārbaudiet, vai ir atklāti kalibrēšanas mērķi.
4. Pārliecinieties, ka ir atklāts pareizais kameras modelis.
5. Pārskatiet mērķa attēla uzņemšanas laiku un apstākļus.

***

## Apstrādāto attēlu izmantošana

### Fotogrammetrijai/ortomosaikas izveidei

**Ieteicamais darba process:**

1. **Importējiet kalibrētus atstarojuma attēlus** fotogrammetrijas programmā:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **Saglabājiet EXIF metadatus**: pārliecinieties, ka GPS dati ir saglabāti ģeotagēšanai
3. **Kalibrētas darba plūsmas**: izmantojiet atstarojuma attēlus zinātniskai precizitātei
4. **Apstrādājiet indeksa mozaīkas**: izveidojiet NDVI ortomozaīkas no atsevišķiem indeksa attēliem
5. **Eksportējiet ģeoreferencēto GeoTIFF**: izmantošanai GIS lietojumprogrammās

### GIS analīzei

**Ieteicamais darba process:**

1. **Ielādējiet QGIS, ArcGIS vai līdzīgā programmā**
2. **Izmantojiet 16 bitu TIFF** atstarojuma attēlus daudzjoslu analīzei
3. **Izmantojiet indeksa attēlus** (NDVI, NDRE) kā gatavus veģetācijas slāņus
4. **Rastra kalkulators**: apvienojiet joslas pielāgotai analīzei
5. **Eksportējiet**: izveidojiet klasifikācijas kartes, izmaiņu noteikšanu, veģetācijas veselības kartes

### Tiešai analīzei/ziņojumu sagatavošanai

**Ieteicamais darba process:**

1. **Izmantojiet indeksa attēlus ar LUT krāsām** vizuāliem ziņojumiem
2. **Iegūstiet statistiku**: vidējais NDVI uz lauku/zemes gabalu
3. **Laika rindas**: salīdziniet indeksus vairākās sesijās
4. **Izveidojiet ziņojumus**: iekļaujiet kartes, statistiku un vizualizācijas

***

## Arhivēšana un dublēšana

### Ieteicamā dublēšanas stratēģija

**Ko saglabāt:**

* ✅ **Oriģinālie RAW/JPG attēli** — arhivējiet atsevišķā diskā/mākonī
* ✅ **Apstrādātie rezultāti** — saglabājiet kalibrētus attēlus un indeksus
* ✅ **Projekta fails** — satur visus iestatījumus atkārtotai apstrādei, ja nepieciešams
* ✅ **Debug Log** — dokumentē apstrādes detaļas
* ✅ **Kalibrēšanas mērķa attēli** – pārbaudei un atkārtotai apstrādei

**Ieteikumi uzglabāšanai:**

* **Tūlītēja dublēšana**: ārējais cietais disks
* **Ilgtermiņa arhīvs**: mākonis (Google Drive, Dropbox utt.)
* **Kritiskie dati**: saglabājiet 2–3 kopijas dažādās vietās

***

## Nākamās apstrādes kārtas

### Projekta iestatījumu atkārtota izmantošana

Ja nākotnē apstrādāsiet līdzīgus datu kopumus:

1. **Saglabājiet projekta veidni** (ja vēl neesat to izdarījuši)
2. **Izveidojiet jaunu projektu**, izmantojot saglabāto veidni
3. **Importējiet jaunus attēlus**
4. **Apstrādājiet** ar identiskām iestatījumiem, lai nodrošinātu konsekvenci

### Vairāku sesiju partijas apstrāde

Vairākām sesijām/datu kopumiem:

**1. variants: GUI - vairāki projekti**

* Izveidojiet atsevišķu projektu katrai sesijai
* Izmantojiet konsekventus veidnes iestatījumus
* Apstrādājiet vienu pēc otra

**2. variants: Chloros CLI (tikai Chloros+)**

* Automatizējiet partiju apstrādi
* Apstrādājiet vairākas mapes ar skriptiem
* Skatīt [CLI dokumentāciju](../CLI.md)

**3. variants: Python SDK (tikai Chloros+)**

* Programmatiska kontrole
* Integrācija ar analīzes cauruļvadiem
* Skatīt [API dokumentāciju](../api-python-sdk.md)

***

## Problēmu novēršana pēc apstrādes

### Atkārtota apstrāde ar citām iestatījumiem

Ja rezultāti nav apmierinoši:

1. Saglabājiet oriģinālās attēlus (nekad nedzēsiet)
2. Atveriet to pašu projektu Chloros
3. Pielāgojiet iestatījumus paneļā Project Settings (Projekta iestatījumi)
4. Apstrādājiet atkārtoti — rezultāti pārrakstīs iepriekšējos rezultātus

### Attēlu apakškopas apstrāde

Lai atkārtoti apstrādātu tikai konkrētus attēlus:

1. Izveidojiet jaunu projektu
2. Importējiet tikai tos attēlus, kuriem nepieciešama atkārtota apstrāde
3. Izmantojiet to pašu iestatījumu veidni
4. Apstrādājiet mazāku datu kopu

### Palīdzības saņemšana

Ja rodas problēmas:

* 📧 **E-pasts**: info@mapir.camera (iekļaujiet Debug Log)
* 🌐 **Atbalsts**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **FAQ**: [Bieži uzdotie jautājumi](../faq.md)
* 📖 **Dokumentācija**: [Chloros rokasgrāmata](../)

***

## Kopsavilkums: pilnīga darba plūsma

Tagad esat pabeidzis pilnu Chloros apstrādes darba plūsmu:

1. ✅ **Izveidots projekts** - Skatīt [Projekti](../projects.md)
2. ✅ **Pievienoti faili** - Skatīt [Failu pievienošana](adding-files-to-a-project.md)
3. ✅ **Pielāgotas iestatījumi** - Skatīt [Projekta iestatījumu pielāgošana](adjusting-project-settings.md)
4. ✅ **Atzīmēti mērķi** - Skatīt [Mērķa attēlu izvēle](choosing-target-images.md)
5. ✅ **Sākta apstrāde** - Skatīt [Apstrādes sākšana](starting-the-processing.md)
6. ✅ **Uzraudzīts process** - Skatīt [Apstrādes uzraudzība](monitoring-the-processing.md)
7. ✅ **Pārskatīti rezultāti** - Šī lapa

**Jūsu kalibrētie, atstarojuma korekcijas multispektrālie attēli ir gatavi analīzei!**

***

## Papildu resursi

### Papildu funkcijas

* [**Attēlu skatītājs**](../image-viewer-gui/opening-an-image-full-screen.md) - Interaktīva vizualizācija un analīze
* [**Indeksa/LUT smilšu kaste**](../image-viewer-gui/index-lut-sandbox.md) - Pielāgota indeksa testēšana
* [**Daudzspektrālo indeksu formulas**](../project-settings/multispectral-index-formulas.md) - Pilnīga indeksu atsauce

### Automatizācija un integrācija

* [**CLI dokumentācija**](../CLI.md) - Komandrindas partiju apstrāde
* [**Python SDK**](../api-python-sdk.md) – Programmatiska automatizācija
* [**Chloros+ funkcijas**](../#chloros) – Uzlabotas apstrādes iespējas

### Atbalsts un apmācība

* [**FAQ**](../faq.md) – Atbildes uz bieži uzdotajiem jautājumiem
* [**Kalibrēšanas mērķi**](../calibration-targets.md) – Reflektances kalibrēšanas izpratne
* [**Atbalstītās kameras**](../supported-cameras.md) – Saderīgā aparatūra
