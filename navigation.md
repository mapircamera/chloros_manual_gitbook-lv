# Lietotāja saskarne: Navigācija

Pirmoreiz palaistot programmas Chloros un Chloros (pārlūks), tiks uzsākta to aizmugurējā sistēma. Tiklīdz tā būs gatava, kreisajā augšējā stūrī parādīsies galvenās izvēlnes ikona <img src=".gitbook/assets/image (1) (1) (1).png" alt="" data-size="line"> .

<figure><img src=".gitbook/assets/header.JPG" alt=""><figcaption></figcaption></figure>

No kreisās puses uz labo augšējā galvenajā joslā ir:

### <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> Galvenā izvēlne

<figure><img src=".gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

No galvenās izvēlnes jūs varat:

* **Jauns projekts** — izveidot jaunu projektu
* **Atvērt projektu** — atvērt esošu projektu
* **Atvērt projekta mapi** — atvērt projekta mapi failu pārlūkā
* **Pievienot failus** — pievienot atsevišķus attēlu failus pašreizējam projektam _(redzams pēc projekta atvēršanas)_
* **Pievienot mapi** — pievienot attēlu mapi pašreizējam projektam _(redzams pēc projekta atvēršanas)_
* **Sākt apstrādi / Pārtraukt apstrādi** — sākt vai pārtraukt attēlu apstrādes procesu _(iespējams pēc failu pievienošanas)_

{% hint style="info" %}
**Tikai Windows**: Chloros darbvirsmas grafiskais interfeiss ir pieejams Windows. Linux lietotājiem jāskatās [CLI](CLI.md) un [Python SDK](api-python-sdk.md) dokumentāciju par apstrādi bez grafiskās saskarnes.
{% endhint %}

### <img src=".gitbook/assets/image (2) (1).png" alt="" data-size="line"> Atskaņot/Sākt pogu

Ja ir ieslēgta, apstrādes sākšanas poga sāk attēlu apstrādes procesu.

### <img src=".gitbook/assets/image (4).png" alt="" data-size="line"> Progresa josla <img src=".gitbook/assets/image (5).png" alt="" data-size="line">Bezmaksas Chloros režīmā, kas apstrādā visus failus secīgi, progresa josla parādīs 2 posmus: Mērķa noteikšana un Apstrāde.

Maksas Chloros+ licencētajā režīmā, kas apstrādā visus failus vienlaicīgi, progresa josla parādīs 4 posmus: Noteikšana, Analīze, Kalibrēšana, Eksportēšana. Ja uzvedat peles kursoru uz Chloros+ progresa joslas, atvērsies paplašinātais 4 progresa joslu panelis, lai jūs varētu sekot līdzi. Noklikšķinot uz augšējās progresa joslas, izvēlnes panelis tiks fiksēts, noklikšķinot atkārtoti, tas tiks atbloķēts.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Sānu izvēlne

Kreisajā sānu izvēlnē ir dažādas ikonas, ar kurām varat mijiedarboties:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Projekta iestatījumi](project-settings/project-settings.md)

Sadaļā „Projekta iestatījumi” varat pielāgot projekta vispārējos un apstrādes iestatījumus. Pielāgojiet tos pirms failu apstrādes sākšanas.

#### <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> Failu pārlūks

Pievienojiet failus/mapes un noņemiet failus no projekta. Dublikāti tiek ignorēti. Atzīmējiet mērķa attēla kolonnas lodziņu, un apstrāde meklēs mērķus tikai atzīmētajos attēlos, ievērojami paātrinot apstrādes laiku. Izmantojiet pogu „Attēls/Metadati”, lai pārslēgtos starp izvēlētā attēla sīktēlu rāsti un detalizētu metadatu tabulu.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Attēlu skatītājs](image-viewer-gui/opening-an-image-full-screen.md)

Kad galvenajā attēlu skatītājā tiek noklikšķināts uz attēla, tas tiek atvērts pilnekrāna režīmā cilnē „Attēlu skatītājs”.

#### <img src=".gitbook/assets/image (7).png" alt="" data-size="line"> [Karte](image-viewer-gui/map-markers.md)

Skatiet savus attēlus interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Atbalsta Google Maps un ESRI flīžu pakalpojumu sniedzējus, automātiski izvēloties labāko pakalpojumu jūsu atrašanās vietai. Pārvietojiet kursoru pār marķieriem, lai redzētu attēlu sīktēlu priekšskatījumus.

#### <img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line"> Debug Log

Pārskatiet žurnālu, lai atrastu debug izdrukas, ja rodas problēmas. Kopējiet/lejupielādējiet žurnālu un nosūtiet to [MAPIR atbalstam](https://www.mapir.camera/community/contact), lai saņemtu palīdzību.

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Lietotāja pieteikšanās](chloros+-login.md)

Lietotāja pieteikšanās sānu josla ļauj jums pieteikties savā Chloros+ kontā, lai atbloķētu papildu funkcijas. Jūs varat arī apskatīt pašreizējo lietojumprogrammas versiju, kā arī pielāgot Chloros GUI un CLI parādītā teksta valodu.
