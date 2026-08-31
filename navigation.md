# Grafiskā lietotāja saskarne (GUI): Navigācija

Pirmoreiz palaistot programmu Chloros, tā sāk darboties apstrādes fonā. Kad apstrādes fonā notiekošais process ir gatavs, kreisajā augšējā stūrī parādās galvenās izvēlnes ikona „<img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">”, un kreisajā sānjoslā atbloķējas cilnes „Cameras” un „Light Sensors” (līdz tam brīdim tās ir neaktīvas).

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

No kreisās puses uz labo augšējā galvenajā joslā atrodas:

### „<img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">” galvenā izvēlne

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

No galvenās izvēlnes varat:

* **Jauns projekts**— izveidot jaunu projektu. Ja esat saglabājuši projekta veidnes, parādās nolaižamā izvēlne**Izvēlēties veidni**, lai jauno projektu varētu sākt, izmantojot veidnes iestatījumus.
* **Atvērt projektu**— atvērt esošu projektu. Sarakstā ir iekļauta poga**Atvērt projekta mapi**, kas atver projekta mapi jūsu failu pārlūkprogrammā.
* **Dublikāt projektu** — kopējiet pašlaik atvērto projektu ar jaunu nosaukumu (tiek ieteikts brīvi izvēlēts nosaukums, piemēram, &quot;MansProjekts (2)&quot;) un atveriet kopiju. _(redzama pēc projekta atvēršanas)_
* **Pievienot failus** — pievienojiet atsevišķus attēlu failus pašreizējam projektam _(redzama pēc projekta atvēršanas)_
* **Pievienot mapi** — pievienojiet vienu vai vairākas attēlu mapes pašreizējam projektam _(redzama pēc projekta atvēršanas)_
* **Sākt apstrādi / Pārtraukt apstrādi** — sāk vai pārtrauc attēlu apstrādes procesu _(pieejams pēc failu pievienošanas)_
* **Savienot ar kameru** — pāriet uz [cilni „Kameras”](lattice/), lai savienotu LATTICE kameru vai matrici. Darbojas arī bez atvērtā projekta.
* **Savienot ar gaismas sensoru** — pāriet uz [cilni „Gaismas sensori”](daq/), lai savienotu DAQ gaismas sensoru. Darbojas, pat ja projekts nav atvērts.

{% hint style="info" %}
**Tikai Windows**: Chloros darbvirsmas grafiskā lietotāja saskarne ir pieejama Windows. Linux lietotājiem jāiepazīstas ar [CLI](CLI.md) un [Python SDK](api-python-sdk.md) dokumentāciju par apstrādi bez grafiskās saskarnes.
{% endhint %}

###<img src=".gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">

Atskaņošanas/sākšanas poga

Ja šī funkcija ir ieslēgta, apstrādes sākšanas poga uzsāk attēlu apstrādes procesu.

###<img src=".gitbook/assets/image (4).png" alt="" data-size="line">

Progresa josla<img src=".gitbook/assets/image (5).png" alt="" data-size="line">

Bezmaksas Chloros režīmā, kurā visi faili tiek apstrādāti secīgi, progresa josla parādīs 2 posmus: mērķa atklāšana un apstrāde.

Maksas Chloros+ licencētajā režīmā, kurā visi faili tiek apstrādāti vienlaikus, progresa josla parāda 4 posmus: atklāšana, analīze, kalibrēšana, eksportēšana. Ja uzvedīsiet peles kursoru virs Chloros+ progresa joslas, atvērsies izvērstais 4 posmu progresa joslas panelis, lai jūs varētu sekot līdzi procesam. Noklikšķinot uz augšējās progresa joslas, izvērstais panelis tiks fiksēts, bet, noklikšķinot vēlreiz, tas tiks atbloķēts.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Sānu izvēlne

Kreisās sānu joslas izvēlnē ir dažādas ikonas, ar kurām varat mijiedarboties, šādā secībā no augšas uz leju:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Projekta iestatījumi](project-settings/project-settings.md)

Sadaļā „Projekta iestatījumi” varat pielāgot vispārējos projekta un apstrādes iestatījumus. Pielāgojiet šos iestatījumus, pirms sākat apstrādāt failus.

#### <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> Failu pārlūks

Pievienojiet failus/mapes un noņemiet failus no projekta. Dublikāti tiek ignorēti. Atzīmējiet mērķa kolonnas lodziņu pie jebkura mērķa attēla, un apstrāde ņems vērā tikai atzīmētos attēlus kā mērķus, ievērojami paātrinot apstrādes laiku. Izmantojiet slēdzi „Attēls/Metadati”, lai pārslēgtos starp izvēlētā attēla sīktēlu rāsti un detalizētu metadatu tabulu.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Attēlu skatītājs](image-viewer-gui/opening-an-image-full-screen.md)

Kad galvenajā attēlu skatītājā tiek noklikšķināts uz attēla, tas tiek atvērts pilnekrāna režīmā cilnē „Attēlu skatītājs”.

#### <img src=".gitbook/assets/image (3) (1).png" alt="" data-size="line"> [Kartes skatītājs](image-viewer-gui/map-markers.md)

Skatiet savus attēlus interaktīvā 2D kartē, pamatojoties uz to GPS koordinātēm. Atbalsta „Google Maps“ un ESRI kartes fragmentu sniedzējus, automātiski izvēloties labāko pakalpojumu jūsu atrašanās vietai. Pārvietojiet kursoru pār marķieriem, lai redzētu attēlu sīktēlu priekšskatījumus.

#### <img src=".gitbook/assets/image (17).png" alt="" data-size="line"> [Kameras](lattice/)

Pieslēdzieties un vadiet LATTICE kameras tiešraidē — pa vienai vai kā sinhronizētas daudzkameru sistēmas. Šajā cilnē tiek parādīti reāllaika priekšskatījuma mozaīkas ar pārklājumiem un histogrammām, iestatījumi katrai kamerai un katram masīvam, kā arī uzņemšanas iestatījumi, kas nosaka, kuras kameras un eksporta veidus izmanto funkcija „Capture All”. Pieejams, tiklīdz aizmugurējā sistēma ir gatava; pilnu aprakstu skatiet [LATTICE sadaļā](lattice/).

#### <img src=".gitbook/assets/image (23).png" alt="" data-size="line"> [Gaismas sensori](daq/)

Pievienojiet DAQ gaismas sensorus — DAQ-U (USB), DAQ-M (Bluetooth) un DAQ-E (Ethernet) — un skatiet to reāllaika kalibrētos spektra diagrammas W/m²/nm vienībās. Šeit varat ierakstīt `.daq` failus atvērtā projektā, pārdēvēt sensorus, izvēlēties kapacitātes korekcijas profilus un atjaunināt DAQ-E programmaparatūru. Pieejams, kad backend ir gatavs; pilnu aprakstu skatiet [DAQ sadaļā](daq/).

#### „<img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line">” atkļūdošanas žurnāls

Ja rodas problēmas, pārskatiet žurnālu, lai atrastu atkļūdošanas izdrukas. Kopējiet/lejupielādējiet žurnālu un nosūtiet to [MAPIR atbalsta dienestam](https://www.mapir.camera/community/contact), lai saņemtu palīdzību.

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Lietotāja pieteikšanās](chloros+-login.md)

Lietotāja pieteikšanās sānu josla ļauj jums pieteikties savā Chloros+ kontā, lai atbloķētu papildu funkcijas. Jūs varat arī apskatīt pašreizējo lietojumprogrammas versiju, kā arī mainīt Chloros lietotāja saskarnes un CLI parādītā teksta valodu.
