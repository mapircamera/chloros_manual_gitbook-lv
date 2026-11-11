# GUI: Projekti

Chloros ļauj izveidot projektus, kurus nākotnē var atvērt no jauna. Projekts ir parasta mape (jūsu projekta mapē), kas satur:

* `project.json` — projekta iestatījumi, failu saraksts un attēlošanas iestatījumi
* `cameras.json` — kameras un sensoru masīvi, kas bija pieslēgti, kamēr projekts bija atvērts, kopā ar to iestatījumiem
* `sensors.json` — DAQ gaismas sensori, kas bija pieslēgti, kamēr projekts bija atvērts, kā arī kameru un sensoru savienojumi
* jūsu uzņemtie attēli, `.daq` ieraksti un apstrādāto rezultātu mapes

Nav īpašas projekta failu formāta — mape un tajā esošie JSON faili veido projektu, kas arī atvieglo projektu kopēšanu, arhivēšanu un vadīšanu no [CLI](CLI.md) vai [Python SDK](api-python-sdk.md).

Ja vien to nemaināt [Projekta iestatījumos](project-settings/project-settings.md), projekta mape atrodas `Documents\MAPIR\Chloros Projects` jūsu Dokumentu mapē (`~/Documents/MAPIR/Chloros Projects` Linux sistēmā).

{% hint style="warning" %}
**Windows: izmantojiet īsus projekta mapju nosaukumus, ja vien nav ieslēgti garie ceļi.**Windows sistēmā garie ceļi ir atspējoti pēc noklusējuma, un tādā gadījumā neviens faila ceļš nedrīkst būt garāks par 260 rakstzīmēm. Apstrādātie produkti atrodas aptuveni 130 rakstzīmes zemāk par projekta mapi, tāpēc, sākot ar versiju 1.3.0, Chloros noraida pārāk dziļu projekta mapi — gan to izveidojot vai atverot, gan sākot apstrādi —, nevis vienkārši palaist programmu un neko neierakstīt. Ziņojumā tiek norādīta garākā projekta mape, kas atbilst prasībām. Izmantojiet īsāku projekta mapi vai ieslēdziet Windows garos ceļus (instalētāja opcija**Ieslēgt Windows garos ceļus**, kas pēc noklusējuma ir atzīmēta) un pārstartējiet Chloros. Skatīt [Apstrādes uzsākšana](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).
{% endhint %}

## Jauns projekts

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Galvenajā izvēlnē izvēlieties „Jauns projekts” un ievadiet unikālu nosaukumu savam projektam.

Ja esat saglabājuši kādus projekta veidnes, zem nosaukuma lauka parādās nolaižamā izvēlne **Izvēlēties veidni** — izvēloties vienu no tām, jauns projekts tiek uzsākts, izmantojot šīs veidnes iestatījumus. Veidnes tiek saglabātas sadaļā [Projekta iestatījumi](project-settings/project-settings.md): ievadiet nosaukumu laukā „Saglabāt projekta veidnes nosaukumu” un noklikšķiniet uz saglabāšanas ikonas.

## Atvērt projektu

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Sadaļā „Atvērt projektu” ir uzskaitīti visi projekti jūsu projektu mapē, bet apakšā atrodas <strong>opcija Atvērt projektu mapi</strong></p></figcaption></figure>

Izvēlieties „Atvērt projektu”, lai apskatītu esošo projektu sarakstu projekta mapē. Ja projektu nav, papildu sānu izvēlne neparādīsies. Augšējā attēlā redzami daži ar grafisko lietotāja saskarni izveidoti projekti (t1, t2, t3). Projekti ar nosaukumiem „DATE\_TIME“ tika izveidoti, izmantojot CLI, saskaņā ar noklusējuma projektu nosaukumu shēmu. Noklikšķinot uz jebkura projekta nosaukuma, tas tiks atvērts.

Noklikšķinot uz pogas „Atvērt projekta mapi“, tiks atvērts jūsu datora failu pārlūks, kurā būs norādīts projekta ceļš. Projekta ceļu varat pielāgot sadaļā [Projekta iestatījumi](project-settings/project-settings.md).

Ja kopš pēdējās atvēršanas kāds no projekta avota attēlu failiem ir pārvietots vai dzēsts, Chloros parāda dialoglodziņu, kurā ir precīzi uzskaitīti trūkstošie faili, nevis atver tukšu režģi.

## Projekta dublikāts

Šī funkcija ir pieejama, kad projekts ir atvērts. Izvēlieties „Duplicate Project”, lai kopētu pašreizējo projektu ar jaunu nosaukumu — Chloros ieteiks nākamo brīvo nosaukumu (piem., „MyProject (2)”) — un dublikāts tiks atvērts nekavējoties.

## Pievienot failus

Pēc projekta atvēršanas izvēlieties „Pievienot failus” galvenajā izvēlnē, lai pievienotu atsevišķus attēlu failus pašreizējam projektam. Šī funkcija atspoguļo failu pārlūka pievienošanas funkcionalitāti, taču ērtības labad ir pieejama tieši no galvenās izvēlnes.

## Pievienot mapi

Pēc projekta atvēršanas izvēlieties galvenajā izvēlnē „Pievienot mapi”, lai pašreizējam projektam pievienotu attēlu mapes. Vienā reizē varat izvēlēties vairākas mapes. Dublētie faili tiek ignorēti.

## Apstrādes sākšana / apturēšana

Pēc failu pievienošanas projektam galvenajā izvēlnē kļūst pieejama opcija „Sākt apstrādi”. Šī darbība ir tāda pati kā uzklikšķināšana uz pogas „Atskaņot/Sākt” augšējā galvenajā joslā. Apstrādes laikā izvēlnes elements mainās uz „Apturēt apstrādi”, lai ļautu jums apturēt apstrādes procesu.

## Savienot ar kameru / Savienot ar gaismas sensoru

Galvenās izvēlnes apakšdaļā ir divas aparatūras saīsnes, kas ir pieejamas neatkarīgi no tā, vai projekts ir atvērts vai nē:

* **Savienot ar kameru** — atver [cilni „Kameras”](lattice/), lai savienotu LATTICE kameru vai matrici.
* **Pievienot gaismas sensoru** — atver [Gaismas sensoru cilni](daq/), lai pievienotu DAQ gaismas sensoru.

Aparatūras pievienošana, kamēr projekts ir atvērts, to saglabā projektā (skatīt zemāk). Ja projekts nav atvērts, savienojumi ir spēkā tikai konkrētajā sesijā.

{% hint style="info" %}
Izvēlnes punkti „Pievienot failus”, „Pievienot mapi” un „Sākt/apstādināt apstrādi” ir redzami vai pieejami tikai tad, ja projekts ir atvērts un faili ir pievienoti. Tie nodrošina ātru piekļuvi darbībām, kas ir pieejamas arī caur failu pārlūka sānu joslu un galvenes pogām.
{% endhint %}

## Projekti atceras jūsu aparatūru

Jaunums versijā 1.2.0: projekts saglabā aparatūru, kuru pieslēdzat, kamēr tas ir atvērts. Kameras un masīvi (ar katras kameras iestatījumiem, nosaukumiem, krāsām un režģa izkārtojumu) tiek saglabāti kā momentuzņēmums failā `cameras.json`, bet gaismas sensori (ar nosaukumiem, krāsām un kameru saistījumiem) — failā `sensors.json` — automātiski, kamēr jūs strādājat.

Kad jūs **atkal atverat** projektu, Chloros nekavējoties neietekmē nevienu aparatūru. Katra puse atkārtoti izveido savienojumu, pirmoreiz, kad jūs atverat tai piederošo cilni:

* Atverot cilni **Kameras**, tiek atkārtoti izveidoti savienojumi ar saglabātajām kamerām un masīviem un atkārtoti piemēroti to saglabātie iestatījumi.
* Atverot cilni **Gaismas sensori**, tiek atjaunota savienojuma ar saglabātajiem DAQ sensoriem un atkārtoti piemēroti to saglabātie iestatījumi, ieskaitot vāciņu. Sensors, kura Cap izvēlnē ir iestatīts uz**None (bare sensor)**, paliek bez vāciņa. Projektā, ko saglabājis agrāks Chloros, sensors bez vāciņa tika reģistrēts kā neizvēlēts, tāpēc tas atgriežas ar saules gaismas kosinusa korekciju; iestatiet to atkal uz None un saglabājiet projektu.

Tādējādi, atverot projektu tikai attēlu pārlūkošanai vai eksportēšanai, kameras nekad netiek ieslēgtas straumēšanai. Ja, atverot cilni, nevar atrast saglabātu ierīci, dialoglodziņā tiek norādīts, kuras ierīces nav pieejamas, lai jūs varētu tās atkārtoti savienot vai noņemt.

## DAQ ieraksti un .daq faili projektā

* `.daq` ieraksti, kas veikti, kamēr projekts ir atvērts (no cilnes „Light Sensors” vai uzņemšanas laikā), tiek **automātiski pievienoti projektam**.
* Importētie `.daq` faili un visi projekta ieraksti ir uzskaitīti [Projekta iestatījumu](project-settings/project-settings.md) sadaļā **DAQ gaismas sensors**, katram ar savu spīduma korekcijas profilu.
* Apstrādes laikā projekta `.daq` faili nodrošina lejupvērsto apgaismojumu atstarošanas produktiem — skatiet [Izvades attēlu formāti](output-image-formats.md).

## Saglabāta projekta vadīšana bez grafiskās saskarnes

Saglabātu projektu var vadīt bez grafiskās saskarnes:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run` darbojas ar projekta mapes ceļu — skatiet [CLI atsauci](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` atgriež projekta identifikatoru; `connect_all()` aktivizē visas saglabātās kameras, matricas un gaismas sensorus ar to saglabātajiem iestatījumiem (gaismas sensori — izmantojot darbojošos Chlorosa backend) — skatiet [SDK atsauci](reference/sdk-reference.md).
