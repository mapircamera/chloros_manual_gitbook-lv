# Grafiskais interfeiss: Projekti

Chloros ļauj izveidot projektus, kurus var atvērt atkārtoti nākotnē. Projekts ir parasta mape (jūsu projektu mapē), kas satur:

* `project.json` — projekta iestatījumi, failu saraksts un attēlošanas iestatījumi
* `cameras.json` — kameras un sensoru masīvi, kas bija pieslēgti, kamēr projekts bija atvērts, kopā ar to iestatījumiem
* `sensors.json` — DAQ gaismas sensori, kas bija pieslēgti, kamēr projekts bija atvērts, kā arī kameru un sensoru savienojumi
* jūsu uzņemtie attēli, `.daq` ieraksti un apstrādāto rezultātu mapes

Nav īpašas projekta failu formāta — mape un tajā esošie JSON faili veido projektu, kas arī atvieglo projektu kopēšanu, arhivēšanu un pārvietošanu no [CLI](CLI.md) vai [Python SDK](api-python-sdk.md).

## Jauns projekts

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Galvenajā izvēlnē izvēlieties „Jauns projekts” un ievadiet savam projektam unikālu nosaukumu.

Ja esat saglabājuši kādus projekta veidnes, zem nosaukuma lauka parādās nolaižamā izvēlne **Izvēlēties veidni** — izvēloties vienu no tām, jauns projekts tiek uzsākts, izmantojot šīs veidnes iestatījumus. Veidnes tiek saglabātas sadaļā [Projekta iestatījumi](project-settings/project-settings.md): ievadiet nosaukumu laukā „Saglabāt projekta veidnes nosaukumu” un noklikšķiniet uz saglabāšanas ikonas.

## Atvērt projektu

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Sadaļā „Atvērt projektu” ir uzskaitīti visi projekti jūsu projektu mapē, bet apakšā atrodas <strong>opcija</strong> <strong>„Atvērt projektu mapi”</strong></p></figcaption></figure>Izvēlieties „Atvērt projektu”, lai apskatītu esošo projektu sarakstu projekta mapē. Ja projektu nav, papildu sānu izvēlne neparādīsies. Iepriekšējā attēlā redzami daži ar GUI izveidoti projekti (t1, t2, t3). DATE\_TIME projekti tika izveidoti ar CLI, izmantojot noklusējuma projekta nosaukumu shēmu. Noklikšķinot uz jebkura projekta nosaukuma, tas tiks atvērts.

Noklikšķinot uz pogas „Atvērt projekta mapi”, atvērsies jūsu datora failu pārlūks, kurā būs norādīts projekta ceļš. Projekta ceļu varat pielāgot sadaļā [Projekta iestatījumi](project-settings/project-settings.md).

Ja kāds no projekta avota attēlu failiem ir pārvietots vai dzēsts kopš pēdējās atvēršanas reizes, Chloros parāda dialoglodziņu, kurā ir precīzi uzskaitīti trūkstošie faili, nevis atver tukšu režģi.

## Projekta dublikāts

Šī funkcija ir pieejama, kad projekts ir atvērts. Izvēlieties „Duplicēt projektu”, lai kopētu pašreizējo projektu ar jaunu nosaukumu — Chloros ieteiks nākamo brīvo nosaukumu (piem., „MyProject (2)”) — un dublikāts tiks atvērts nekavējoties.

## Pievienot failus

Pēc projekta atvēršanas izvēlieties „Pievienot failus” galvenajā izvēlnē, lai pievienotu atsevišķus attēlu failus pašreizējam projektam. Šī funkcija atspoguļo failu pārlūka pievienošanas funkcionalitāti, taču ērtības labad ir pieejama tieši no galvenās izvēlnes.

## Pievienot mapi

Pēc projekta atvēršanas izvēlieties galvenajā izvēlnē „Pievienot mapi”, lai pašreizējam projektam pievienotu attēlu mapes. Vienā reizē varat izvēlēties vairākas mapes. Dublikāti tiek ignorēti.

## Apstrādes sākšana / apturēšana

Pēc failu pievienošanas projektam galvenajā izvēlnē kļūst pieejama opcija „Sākt apstrādi”. Šī darbība atbilst pogas „Atskaņot/Sākt” nospiešanai augšējā galvenajā joslā. Apstrādes laikā izvēlnes elements mainās uz „Apturēt apstrādi”, lai ļautu jums apturēt apstrādes procesu.

## Savienot ar kameru / Savienot ar gaismas sensoru

Galvenās izvēlnes apakšdaļā ir divas aparatūras saīsnes, kas ir pieejamas neatkarīgi no tā, vai projekts ir atvērts vai nē:

* **Savienot ar kameru** — atver [cilni „Kameras”](lattice/), lai savienotu LATTICE kameru vai matrici.
* **Savienot ar gaismas sensoru** — atver [Gaismas sensoru cilni](daq/), lai savienotu DAQ gaismas sensoru.

Aparatūras savienošana, kamēr projekts ir atvērts, to saglabā projektā (skatīt zemāk). Ja projekts nav atvērts, savienojumi ir spēkā tikai konkrētajā sesijā.

{% hint style="info" %}
Izvēlnes punkti „Pievienot failus”, „Pievienot mapi” un „Sākt/apstādināt apstrādi” ir redzami vai pieejami tikai tad, ja ir atvērts projekts un ir pievienoti faili. Tie nodrošina ātru piekļuvi darbībām, kas ir pieejamas arī caur failu pārlūka sānu joslu un galvenes pogām.
{% endhint %}

## Projekti atceras jūsu aparatūru

Jaunums versijā 1.2.0: projekts saglabā aparatūru, ko pieslēdzat, kamēr tas ir atvērts. Kameras un masīvi (kopā ar katras kameras iestatījumiem, nosaukumiem, krāsām un režģa izkārtojumu) tiek saglabāti kā momentuzņēmums failā `cameras.json`, bet gaismas sensori (kopā ar nosaukumiem, krāsām un kameru saistījumiem) — failā `sensors.json` — automātiski, kamēr jūs strādājat.

Kad jūs **atkal atverat** projektu, Chloros nekavējoties neizveido savienojumu ar nevienu aparatūru. Katra puse atkārtoti izveido savienojumu, kad pirmo reizi atverat cilni, kurai tā pieder:

* Atverot cilni **Kameras**, tiek atjaunota savienojuma ar saglabātajām kamerām un masīviem un atkārtoti piemēroti to saglabātie iestatījumi.
* Atverot cilni **Gaismas sensori**, tiek atjaunota savienojuma ar saglabātajiem DAQ sensoriem.

Tādējādi, atverot projektu tikai attēlu pārlūkošanai vai eksportēšanai, kameras nekad netiek ieslēgtas straumēšanai. Ja, atverot cilni, saglabāto ierīci nevar atrast, dialoglodziņā tiek norādīts, kuras ierīces nav pieejamas, lai jūs varētu atjaunot savienojumu vai tās noņemt.

## DAQ ieraksti un .daq faili projektā

* `.daq` ieraksti, kas veikti, kamēr projekts ir atvērts (no cilnes „Gaismas sensori” vai uzņemšanas laikā), tiek **automātiski pievienoti projektam**.
* Importētie `.daq` faili un visi projekta ieraksti ir uzskaitīti [Projekta iestatījumu](project-settings/project-settings.md) sadaļā **DAQ gaismas sensors**, katram ar savu maksimālās vērtības korekcijas profilu.
* Apstrādes laikā projekta `.daq` faili nodrošina lejupvērsto apgaismojumu atstarojuma produktiem — skatīt [Izvades attēlu formāti](output-image-formats.md).

## Saglabāta projekta vadīšana bez grafiskās saskarnes

Saglabātu projektu var palaist bez grafiskās lietotāja saskarnes:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run` darbojas ar projekta mapes ceļu — skatiet [CLI atsauci](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` atgriež projekta rīku; `connect_all()` pārslēdz visas saglabātās kameras un sensorus tiešsaistes režīmā ar to saglabātajiem iestatījumiem — skatiet [SDK atsauci](reference/sdk-reference.md).
