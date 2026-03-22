# Kartes atzīmes

Sadaļā „Karte“ jūsu attēli tiek attēloti interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Tas sniedz ģeogrāfisku pārskatu par jūsu uzņemšanas sesiju un palīdz vizualizēt telpisko pārklājumu. Tas ir noderīgi arī attēlu pirmreizējās importēšanas laikā, lai ātri izslēgtu attēlus, kurus nav nepieciešams apstrādāt.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Piekļuve cilnei „Karte”

1. Atveriet vai izveidojiet projektu Chloros
2. Importējiet attēlus, kuros ir GPS metadati
3. Noklikšķiniet uz cilnes **Karte** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> cilni kreisajā sānjoslā
4. Kartē tiks parādīti marķieri katra attēla GPS atrašanās vietā

{% hint style="info" %}
**Nepieciešams GPS**: Kartē tiks parādīti tikai attēli, kuru EXIF metadatos ir iekļautas GPS koordinātas. Pārliecinieties, ka uzņemšanas laikā jūsu kamerā ir ieslēgts GPS.
{% endhint %}

***

## Attēlu pielāgošana no cilnes „Karte”

Cilnei **Karte**<img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> ir tāds pats pievienošanas  <img src="../.gitbook/assets/image.png" alt="" data-size="line">   <img src="../.gitbook/assets/image (1).png" alt="" data-size="line">  un noņemšanas  <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">  kā [**Failu pārlūks**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> . Tajā tiek parādīts arī tāds pats projekta failu tabulas saraksts, bet ar atšķirīgiem kolonnu virsrakstiem:

### Faila nosaukums

* Orijinālais faila nosaukums no kameras
* Saglabā kameras nosaukumu piešķiršanas konvenciju (piem., IMG\_0001.RAW)

### Platums

* Attēla platums

### Garums

* Attēla garums

### Augstums

* Attēla augstums

{% hint style="info" %}
Noklikšķinot uz tabulas kolonnu virsrakstiem, tiek arī šķiroti rindu dati
{% endhint %}

***

## Attēlu marķieri

Katrs attēls ar GPS datiem tiek attēlots kā marķieris kartē:

### Marķieru attēlošana

* Atzīmes norāda precīzas GPS koordinātes, kur katrs attēls tika uzņemts
* Atzīmes var grupēties kopā, ja tiek samazināts attēls
* Palieliniet attēlu, lai redzētu atsevišķu attēlu atrašanās vietas

{% hint style="success" %}
SUPER-ZOOM: Kad sasniedzat maksimālo palielinājuma līmeni no kartes flīžu sniedzēja, flīze tiek palielināta, turpinot palielināt attēlu, ļaujot jums redzēt atzīmes, kas atrodas tuvu viena otrai.
{% endhint %}

### Priekšskatījums, uzvedot kursoru

* **Uzvediet peles kursoru** uz jebkuru atzīmi, lai redzētu attēla sīkattēlu
* Tas ļauj ātri vizuāli identificēt attēlu, neizejot no kartes skata
* Noderīgi, lai atrastu konkrētus attēlus lielā uzņemšanas sesijā

***

## Kartes flīžu pakalpojumu sniedzēji

{% hint style="success" %}
**Automātiskā izvēle**: Chloros automātiski izvēlas flīžu pakalpojumu, kas nodrošina labāko tālummaiņas līmeni jūsu pašreizējai kartes atrašanās vietai. Ja vēlaties, varat manuāli pārslēgties starp pakalpojumu sniedzējiem.
{% endhint %}

Sadaļa „Karte” atbalsta divus flīžu pakalpojumu sniedzējus fona kartes attēliem:

### Google Maps

* Standarta satelīta un kartes attēli no Google
* Vispiemērotākais vispārējam pārklājumam visā pasaulē

### ESRI

* Satelīta un aerofoto attēli no ESRI ArcGIS
* Dažos reģionos bieži nodrošina augstākas izšķirtspējas attēlus

***

## Kartes flīžu veidi

Jūs varat izvēlēties kartes slāņa veidu (no kreisās puses uz labo):

 <img src="../.gitbook/assets/image (23).png" alt="" data-size="original">### Reljefs

Rāda augstuma profilus un kartes flīzes ar detaļām (ceļi utt.)

### Karte

Rāda standarta (zemākas joslas platuma) kartes flīzes ar detaļām (ceļi utt.)

### Satelīts

Rāda detalizētas (augstākas joslas platuma) satelītkartes flīzes

### Hibrīds

Rāda satelītkartes flīzes ar pievienotām detaļām (ceļi utt.)

***

## Kartes navigācija

### Tuvināšanas/attālināšanas vadības elementi

* **Tuvināšana/attālināšana**: izmantojiet peles ritentiņu vai tuvināšanas pogas
* **Pilnekrāna režīms**: parādiet karti pilnekrāna režīmā

### Pārvietošanas vadības elementi

* **Pārvietošana**: noklikšķiniet un velciet, lai pārvietotos pa karti***

## Lietošanas gadījumi

### Lidojuma maršruta vizualizācija

* Apskatiet dronu uzņemšanas sesiju pārklājuma zonu
* Identificējiet attēlu pārklājuma nepilnības
* Pārbaudiet lidojuma maršruta izpildi

### Zemes apsekojuma pārskats

* Apskatiet zemes uzņemumu telpisko izkliedi
* Atrodiet kalibrēšanas mērķa attēlus attiecībā pret apsekojuma zonu
* Plānojiet papildu uzņemšanas vietas

### Kvalitātes kontrole

* Ātri identificējiet attēlus, kas uzņemti negaidītās vietās
* Pārbaudiet GPS precizitāti visā datu kopā
* Salīdziniet attēlu atrašanās vietas ar lauka piezīmēm

***

## Problēmu novēršana

### Neparādās marķieri

**Iespējamie iemesli:**

* Attēlos nav GPS metadatu
* Uzņemšanas laikā kamerā bija atspējots GPS
* EXIF dati tika izdzēsti ar ārējo programmatūru

**Risinājums**: Pārbaudiet, vai kamerā ir ieslēgts GPS, un atkārtoti importējiet oriģinālās failus

### Marķieri nepareizā vietā

**Iespējamie cēloņi:**

* Kameras GPS bija slikta satelītu fiksācija
* GPS novirze uzņemšanas laikā

**Risinājums**: Parasti tas ir uzņemšanas laika problēma; apsveriet PPK/RTK GPS izmantošanu precīzām lietojumprogrammām
