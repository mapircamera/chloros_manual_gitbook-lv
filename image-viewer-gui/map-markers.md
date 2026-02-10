# Kartes marķieri

Kartes cilnē jūsu attēli tiek parādīti interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Tas nodrošina ģeogrāfisku pārskatu par jūsu uzņemšanas sesiju un palīdz vizualizēt telpisko pārklājumu. Tas ir noderīgi arī, kad pirmo reizi importējat attēlus, lai ātri izdzēstu attēlus, kurus nevajag apstrādāt.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Piekļuve cilnei „Karte”

1. Atveriet vai izveidojiet projektu Chloros
2. Importējiet attēlus, kas satur GPS metadatus
3. Noklikšķiniet uz cilnes **Karte** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> cilni kreisajā sānjoslā
4. Karte parādīs atzīmes katra attēla GPS atrašanās vietā

{% hint style="info" %}
**Nepieciešams GPS**: kartē tiks parādīti tikai attēli, kuru EXIF metadatos ir iebūvētas GPS koordinātas. Pārliecinieties, ka jūsu kamerā uzņemšanas laikā ir ieslēgts GPS.
{% endhint %}

***

## Attēlu pielāgošana no cilnes „Karte”

Cilnei **Karte**<img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> cilnē ir tādas pašas pievienošanas  <img src="../.gitbook/assets/image.png" alt="" data-size="line">   <img src="../.gitbook/assets/image (1).png" alt="" data-size="line">  un noņemšanas  <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">  failu pogas kā [**Failu pārlūks**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> cilnē. Tajā tiek parādīts arī tāds pats projekta failu tabulas saraksts, bet ar atšķirīgiem kolonnas virsrakstiem:

### Faila nosaukums

* Orijinālais faila nosaukums no kameras
* Saglabā kameras nosaukumu konvenciju (piemēram, IMG\_0001.RAW)

### Platums

* Attēla platums

### Garums

* Attēla garums

### Augstums

* Attēla augstums

{% hint style="info" %}
Uzklikšķinot uz tabulas kolonnu virsrakstiem, tiek arī šķiroti rindu dati.
{% endhint %}

***

## Attēlu marķieri

Katrs attēls ar GPS datiem tiek attēlots ar marķieri kartē:

### Marķiera attēlošana

* Marķieri norāda precīzas GPS koordinātes, kurās katrs attēls tika uzņemts.
* Grupēti marķieri var tikt apvienoti, ja attēlu samazina.
* Palieliniet attēlu, lai redzētu atsevišķu attēlu atrašanās vietas.

{% hint style="success" %}
SUPER-ZOOM: Kad sasniedzat maksimālo palielinājuma līmeni no kartes flīžu piegādātāja, flīzes tiek palielinātas, ļaujot redzēt marķierus, kas atrodas tuvu viens otram.
{% endhint %}

### Pārskatīšana ar peles kursoru

* **Pārvietojiet peles kursoru** uz jebkuru marķieri, lai redzētu attēla sīkbildes priekšskatījumu
* Tas ļauj ātri vizuāli identificēt attēlu, neizejot no kartes skata
* Noderīgi, lai atrastu konkrētus attēlus lielā uzņemšanas sesijā

***

## Kartes flīžu piegādātāji

{% hint style="success" %}
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

Rāda reljefa profilus un kartes flīzes ar detaļām (ceļi utt.)

### Karte

Rāda standarta (zemākas joslas platuma) kartes flīzes ar detaļām (ceļi utt.)

### Satelīts

Rāda detalizētas (augstākas joslas platuma) satelīta kartes flīzes

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

* Skatīt drona uzņemšanas sesiju pārklājuma zonu
* Identificēt attēlu pārklājuma nepilnības
* Pārbaudīt lidojuma trajektorijas izpildi

### Zemes apsekojuma pārskats

* Skatīt zemes uzņemšanas attēlu telpisko izkliedi
* Atrast kalibrēšanas mērķa attēlus attiecībā pret apsekojuma zonu
* Plānot papildu uzņemšanas vietas

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
