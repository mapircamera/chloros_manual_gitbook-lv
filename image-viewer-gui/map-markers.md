# Kartes marķieri

Kartes cilnē jūsu attēli tiek parādīti interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Tas nodrošina ģeogrāfisku pārskatu par jūsu uzņemšanas sesiju un palīdz vizualizēt telpisko pārklājumu. Tas ir noderīgi arī, kad pirmo reizi importējat attēlus, lai ātri izdzēstu attēlus, kurus nevajag apstrādāt.

## Piekļuve cilnei „Karte”

1. Atveriet vai izveidojiet projektu Chloros
2. Importējiet attēlus, kas satur GPS metadatus
3. Noklikšķiniet uz cilnes **Karte** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> cilni kreisajā sānjoslā
4. Karte parādīs atzīmes katra attēla GPS atrašanās vietā

{% hint style=&quot;info&quot; %}
**Nepieciešams GPS**: kartē tiks parādīti tikai attēli, kuru EXIF metadatos ir iegultas GPS koordinātas. Pārliecinieties, ka jūsu kamerā uzņemšanas laikā ir ieslēgts GPS.
{% endhint %}

***

## Attēlu pielāgošana no cilnes „Karte”

Cilnē **Karte**<img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> ir tādas pašas pievienošanas  <img src="../.gitbook/assets/image.png" alt="" data-size="line">   <img src="../.gitbook/assets/image (1).png" alt="" data-size="line">  un noņemšanas  <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">  poga, kāda ir [**Failu pārlūks**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> cilnē. Tajā ir redzams arī tāds pats projekta failu tabulas saraksts, bet ar atšķirīgiem kolonnas virsrakstiem:

### Faila nosaukums

* Orijinālais faila nosaukums no kameras
* Saglabā kameras nosaukumu konvenciju (piemēram, IMG\_0001.RAW)

### Platums

* Attēla platums

### Garums

* Attēla garums

### Augstums

* Attēla augstums

{% hint style=&quot;info&quot; %}
Uzklikšķinot uz tabulas kolonnu virsrakstiem, tiek arī šķiroti rindu dati.
{% endhint %}

***

## Attēlu marķieri

Katrs attēls ar GPS datiem tiek attēlots ar marķieri kartē:

### Marķiera attēlošana

* Marķieri norāda precīzas GPS koordinātes, kurās katrs attēls tika uzņemts.
* Marķieri var grupēties kopā, ja tiek samazināts attēla mērogs.
* Palieliniet attēlu, lai redzētu atsevišķu attēlu atrašanās vietas.

{% hint style=&quot;success&quot; %}
SUPER-ZOOM: Kad sasniedzat maksimālo tālummaiņas līmeni no kartes flīžu piegādātāja, flīzes tiek palielinātas, turpinot tālummaiņu, ļaujot jums redzēt marķierus, kas atrodas tuvu viens otram.
{% endhint %}

### Pārskatīšana, uzvedot peles kursoru

* **Pielieciet peles kursoru** uz jebkuru atzīmi, lai redzētu attēla sīkbildes priekšskatījumu.
* Tas ļauj ātri vizuāli identificēt attēlu, neizejot no kartes skata.
* Noderīgi, lai atrastu konkrētus attēlus lielā uzņēmumu sesijā.

***

## Kartes flīžu piegādātāji

{% hint style=&quot;success&quot; %}
**Automātiskā izvēle**: Chloros automātiski izvēlas flīžu pakalpojumu, kas nodrošina labāko tālummaiņas līmeni jūsu pašreizējai kartes atrašanās vietai. Ja vēlaties, varat manuāli pārslēgties starp piegādātājiem.
{% endhint %}

Kartes cilne atbalsta divus flīžu piegādātājus fona kartes attēliem:

### Google Maps

* Standarta satelīta un kartes attēli no Google
* Labākais vispārējam pārklājumam visā pasaulē

### ESRI

* Satelīta un aerofoto attēli no ESRI ArcGIS
* Bieži nodrošina augstākas izšķirtspējas attēlus noteiktos reģionos

***

## Kartes flīžu veidi

Jūs varat izvēlēties kartes slāņa veidu (no kreisās puses uz labo):

 <img src="../.gitbook/assets/image (23).png" alt="" data-size="original">### Reljefs

Rāda augstuma profilus un kartes flīzes ar detaļām (ceļi utt.)

### Karte

Rāda standarta (zemāka joslas platuma) kartes flīzes ar detaļām (ceļi utt.)

### Satelīts

Rāda detalizētas (augstāka joslas platuma) satelīta kartes flīzes

### Hibrīds

Rāda satelīta kartes flīzes ar pievienotām detaļām (ceļi utt.)

***

## Kartes navigācija

### Tuvināšanas/attālināšanas vadības elementi

* **Tuvināšana/attālināšana**: izmantojiet peles ritentiņu vai tuvināšanas pogas.
* **Pilna ekrāna režīms**: karti parāda pilna ekrāna režīmā.

### Pārvietošanas vadības elementi

* **Pārvietošana**: noklikšķiniet un velciet, lai pārvietotos pa karti.***

## Lietošanas piemēri

### Lidojuma trajektorijas vizualizācija

* Apskatiet drona uzņemšanas sesiju pārklājuma zonu
* Identificējiet attēlu pārklājuma nepilnības
* Pārbaudiet lidojuma trajektorijas izpildi

### Zemes apsekojuma pārskats

* Apskatiet zemes uzņemšanas attēlu telpisko izkliedi
* Atrodiet kalibrēšanas mērķa attēlus attiecībā pret apsekojuma zonu
* Plānojiet papildu uzņemšanas vietas

### Kvalitātes kontrole

* Ātri identificējiet attēlus, kas uzņemti negaidītās vietās
* Pārbaudiet GPS precizitāti visā datu kopā
* Salīdziniet attēlu atrašanās vietas ar lauka piezīmēm

***

## Problēmu novēršana

### Nav redzami marķieri

**Iespējamie iemesli:**

* Attēli nesatur GPS metadatus
* GPS bija atspējots kamerā uzņemšanas laikā
* EXIF dati tika izdzēsti ar ārējo programmatūru

**Risinājums**: Pārbaudiet, vai kamerā ir ieslēgts GPS, un atkārtoti importējiet oriģinālās failus

### Marķieri nepareizā vietā

**Iespējamie iemesli:**

* Kameras GPS bija slikta satelītu fiksācija
* GPS novirze uzņemšanas laikā

**Risinājums**: Parasti tas ir uzņemšanas laika jautājums; apsveriet PPK/RTK GPS izmantošanu precīziem pielietojumiem
