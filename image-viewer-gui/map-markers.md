# Kartes atzīmes

Sadaļā „Karte” jūsu attēli tiek attēloti interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Tā sniedz ģeogrāfisku pārskatu par uzņemšanas sesiju un ir ātrākais veids, kā uzreiz pēc importēšanas atlasīt attēlus, kurus nevēlaties apstrādāt.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Kā atvērt cilni „Karte”

1. Atveriet vai izveidojiet projektu programmā Chloros
2. Importējiet attēlus, kuros ir GPS metadati
3. Noklikšķiniet uz cilnes **„Karte”** <img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> kreisajā sānjoslā
4. Kartē katra attēla GPS atrašanās vietā parādās marķieris

{% hint style="info" %}
**Nepieciešams GPS**: kartē parādās tikai tie attēli, kuru EXIF metadatos ir norādītas GPS koordinātas. Attēls bez koordinātām joprojām atrodas projektā un tiek apstrādāts kā parasti — tam vienkārši nav atzīmes.
{% endhint %}

***

## Attēlu pielāgošana cilnē „Karte”

Cilnē **Karte**<img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> ir tādas pašas pogas failu pievienošanai <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> un noņemšanai <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line"> kā cilnē [**Failu pārlūks**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">. Tajā tiek parādīts tas pats projekta failu saraksts ar ģeogrāfiskajām kolonnām:

| Kolonna        | Saturs                                                           |
| ------------- | ------------------------------------------------------------------ |
| **Nosaukums**      | Faila nosaukums tā, kā tas tika saglabāts no kameras                             |
| **Platums**  | Decimālie grādi, seši zīmīši aiz komata                                |
| **Garums** | Decimālie grādi, sešas zīmes aiz komata                                |
| **Augstums**  | Metri, viena zīme aiz komata — `-`, ja attēlam nav augstuma |

{% hint style="info" %}
Noklikšķiniet uz jebkuras slejas virsraksta, lai kārtotu pēc tās; noklikšķiniet vēlreiz, lai mainītu secību.
{% endhint %}

{% hint style="warning" %}
**Augstums ir augstums virs jūras līmeņa, nevis augstums virs zemes.** Šī vērtība ir iegūta no attēla EXIF `GPSAltitude` tagiem, kas attiecas uz vidējo jūras līmeni. Tas nav lidojuma augstums virs reljefa, un Chloros no tā neaprēķinās attālumu līdz zemes virsmai — virs lauka, kas atrodas 300 m virs jūras līmeņa, dronis, kas lido 100 m virs zemes (AGL), šeit reģistrē aptuveni 400 m. Izmantojiet šo kolonnu, lai atklātu novirzes un pārliecinātos par lidojuma augstuma stabilitāti, nevis kā AGL mērījumu.
{% endhint %}

***

## Attēlu marķieri

Katram attēlam ar GPS datiem tiek piešķirts marķieris tā koordinātās.

### Marķieru attēlošana

* Marķieri atrodas tieši tajās koordinātēs, kas reģistrētas katram attēlam
* Marķieri, kas atrodas tuvu viens otram, var vizuāli pārklāties, ja attēls ir samazināts — palieliniet attēlu, lai tos nošķirtu
* Izvēlētie un izceltie marķieri tiek attēloti virs pārējiem

### Priekšskatījums, uzvedot kursoru

* **Uzvediet kursoru** uz jebkura marķiera, lai parādītu attēla sīktēlu ar tā faila nosaukumu
* **Noklikšķiniet**uz marķiera, lai atlasītu attēlu un**fiksētu** atvērtā loga palikšanu — tas paliks atvērts, kamēr neklikšķināsiet citur. Kamēr logs ir fiksēts, uzvedot kursoru uz citiem marķieriem, tas netiks aizstāts
* Tas ir ātrs veids, kā atrast konkrētu kadru lielā sesijā, neizejot no kartes

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Sadaļā „Karte” tiek attēloti visi projektā esošie attēli ar ģeogrāfiskajām atzīmēm</p></figcaption></figure>### Super-tuvinājums

{% hint style="success" %}
**SUPER-TUVIŅŠANĀS**: kad sasniedzat maksimālo tuvinājumu, kāds ir pieejams attēlu sniedzēja sniegtajos attēlos, turpmāka tuvināšana nevis apstājas, bet gan palielina flīzes, tādējādi ļaujot atšķirt marķierus, kas atrodas gandrīz viens virs otra.
{% endhint %}

* Super-zoom tiek aktivizēts tikai tad, ja esat **sasniedzis** pakalpojuma sniedzēja maksimālo tuvinājumu konkrētajai vietai un flīzes ir pilnībā ielādētas. Ja tuvinājums ir mazāks, tā darbība ir standarta
* Diapazons ir **no 1× līdz 32×** virs pakalpojuma sniedzēja noteiktā maksimālā tuvinājuma
* Stūrī esošais indikators parāda pašreizējo super-tuvinājumu procentos, un **×** poga blakus tam ar vienu klikšķi atgriež jūs pie parastā tuvinājuma
* Attālināšanās vienmēr tiek nodota pašai kartei, tādējādi jūs nekad nevarat iestrēgt super-tuvinājumā
* Tuvināšana un panoramēšana supertuvināšanas režīmā pārnes izrietošo nobīdi atpakaļ uz karti, tādējādi novirzītajai zonai, uz kuru esat pārvietojies, tiek turpināta flīžu pieprasīšana, nevis ekrāns paliek tukšs
* Marķieri tiek attēloti kā vektoru elementi, nevis rasterizēti, tādējādi tie saglabā asumu jebkurā supertuvināšanas līmenī

***

## Kartes flīžu pakalpojumu sniedzēji

{% hint style="success" %}
**Automātiskā izvēle**: Chloros izvēlas to flīžu pakalpojumu, kas piedāvā labāko tuvinājuma līmeni jebkurā vietā, kur atrodas jūsu attēli. Jūs to varat mainīt manuāli jebkurā brīdī.
{% endhint %}

| Piegādātājs        | Piezīmes                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Google Maps** | Plašs pārklājums visā pasaulē; atbalsta visus četrus flīžu veidus                                                                                                            |
| **Esri ArcGIS**| Dažos reģionos bieži vien ir pieejami augstākas izšķirtspējas aerofotoattēli. Esri gadījumā flīžu veids**Terrain** netiek piedāvāts, un, kamēr ir izvēlēts Esri, tā poga ir atspējota |***

## Kartes flīžu veidi

Izvēlieties kartes slāņa veidu, izmantojot pogas (no kreisās puses uz labo):

![](&lt;../.gitbook/assets/image (14).png&gt;)

| Veids                 | Rāda                                                                |
| -------------------- | -------------------------------------------------------------------- |
| **Reljefs**          | Augstuma ēnojums ar kartes detaļām (ceļi, apzīmējumi). Tikai Google       |
| **Karte**              | Standarta ielu kartes flīzes — variants ar vismazāko datu pārraides platjoslu              |
| **Satelīts**        | Detalizēti satelīta attēli bez apzīmējumiem — variants ar vislielāko datu pārraides platjoslu |
| **Hibrīds** (noklusējums) | Satelīta attēli ar uz tiem uzvilktiem ceļiem un apzīmējumiem                |

Lapa „Karte” atveras režīmā **Hibrīds**. Jūsu izvēle tiek pārnesta uz pakalpojuma sniedzēja maiņu, ja pakalpojuma sniedzējs to atbalsta.***

## Navigācija kartē

* **Tuvināšana**: peles ritentiņš vai tuvināšanas pogas uz kartes
* **Pārvietošana**: noklikšķiniet un velciet
* **Pilnekrāna režīms**: pilnekrāna vadības elements paplašina karti uz visu logu***

## Lietošanas piemēri

### Lidojuma maršruta pārskatīšana

* Vienā skatienā apskatiet drona lidojuma sesijas pārklājuma zonu
* Atklājiet vietas, kur lidojums nav noticis
* Pārliecinieties, ka lidojums noritēja saskaņā ar plānoto maršrutu

### Zemes apsekojuma pārskatīšana

* Apskatiet, kā izvietoti uz zemes uzņemtie attēli
* Atrodiet kalibrēšanas mērķu rāmjus attiecībā pret apsekojamo zonu
* Izlemiet, kur nepieciešami papildu attēli

### Kvalitātes kontrole

* Atrodiet attēlus, kas uzņemti neparedzētā vietā, un izdzēsiet tos pirms apstrādes
* Šķirojiet pēc augstuma, lai atrastu kadru, kas uzņemts nepareizā augstumā, vai tādu, kurā GPS signāls bija vājš
* Salīdziniet attēlu atrašanās vietas ar lauka piezīmēm

***

## Problēmu novēršana

### Neparādās marķieri

**Iespējamie iemesli**

* Attēlos nav GPS metadatu
* Uzņemšanas laikā kamerā bija atspējots GPS
* EXIF dati tika noņemti ar citu programmatūru pirms importēšanas

**Ko darīt**: pārliecinieties, ka kamerā ir ieslēgts GPS, un atkārtoti importējiet sākotnējos failus. Jūs varat pārbaudīt, vai konkrētam failam ir koordinātas, meklējot to cilnē „Karte” esošajā failu tabulā — attēlam bez koordinātām tur nav atsevišķas rindas.

### Atzīmes atrodas nepareizā vietā

**Iespējamie cēloņi**: slikta satelītu signāla uztveršana uzņemšanas brīdī vai GPS novirze sesijas laikā.**Ko darīt**: šī ir problēma, kas radusies uzņemšanas brīdī, nevis kaut kas, ko Chloros var labot pēc tam. Precīzam darbam izmantojiet PPK/RTK GPS darba plūsmu — skatiet iestatījumu**Piemērot PPK korekcijas** sadaļā [Projekta iestatījumi](../project-settings/project-settings.md).

### Karte ir tukša vai flīzes vairs neielādējas

Flīžu piegādātāji ir tiešsaistes pakalpojumi. Ja flīzes vairs netiek saņemtas, pārbaudiet ierīces tīkla savienojumu, pēc tam mēģiniet mainīt piegādātāju. Ja bijāt ļoti tuvinājis attēlu, nospiediet **×** atjaunošanas pogu, lai atgrieztos pie normāla tuvinājuma līmeņa un ļautu kartē atkārtoti pieprasīt flīzes.***

## Saistītās lapas

* [**Attēlu rāsts**](image-grid.md) — tas pats attēlu kopums, kas izmantots kā sīktēli
* [**Attēla atvēršana pilnekrāna režīmā**](opening-an-image-full-screen.md) — viena attēla detalizēta apskate
* [**Failu pievienošana projektam**](../processing-images-gui/adding-files-to-a-project.md) — šajā cilnē pieejamās pogas failu pievienošanai un noņemšanai
