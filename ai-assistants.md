# Chloros izmantošana kopā ar AI palīgiem

Šī rokasgrāmata ir paredzēta divām mērķauditorijām: cilvēkiem un AI palīgiem, ar kuriem cilvēki arvien biežāk strādā. Katrā lapā ir norādītas precīzas vērtības, noklusējuma iestatījumi un komandas, kuras var kopēt un ielīmēt, lai asistents (Claude, ChatGPT, Copilot, programmēšanas aģents u. c.) jau pirmajā mēģinājumā varētu izveidot darbojošos Chloros automatizāciju.

Chloros versija: **

1.2.0**. CLI/SDK platformas: Windows 10/11 x64 un Linux (x86\_64 / Jetson aarch64).

## Ko nodot savam asistentam

| Resurss | URL | Kādam nolūkam tas paredzēts |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Mašīnlasāms indekss par katru šīs rokasgrāmatas lappusi. |
| **CLI Atsauce** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Pilnīga `chloros-cli` komandu virsma: katra komanda, karodziņš, noklusējums, iziešanas kods un izvades mapes noteikums. Sagatavots LLM lietošanai. |
| **SDK atsauces materiāls** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Pilnīga `chloros_sdk` Python API: klases, paraksti, izņēmumi un izstrādāti piemēri. Sagatavots LLM lietošanai. |
| **Jebkura lapa kā neapstrādāts Markdown** | pievienojiet `.md` lapai URL | piemēram, `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` atgriež lapu kā neapstrādātu Markdown formātu — ideāli piemērots ielīmēšanai konteksta logā vai izgūšanai no aģenta. |

Saites rokasgrāmatā: [CLI Atsauce](reference/cli-reference.md) · [SDK Atsauce](reference/sdk-reference.md).

{% hint style="info" %}
Abas atsauces lapas ir patstāvīgas: palīgam, kas ir izlasījis vienu no tām, nav nepieciešama pārējā rokasgrāmata, lai uzrakstītu pareizu skriptu.
{% endhint %}

## Gatavas komandas

Kopējiet, aizpildiet `<placeholders>` un ielīmējiet savā palīgprogrammā.

### 1. Apstrādājiet lidojuma mapi, izmantojot NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Uzraugiet uzņemto datu direktoriju partiju režīmā

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Pieslēdziet LATTICE masīvu un veiciet uzņemšanu

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. Reģistrējiet DAQ gaismas sensora spektrus

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
DAQ skriptu izpilde no komandrindas vienmēr notiek, izmantojot `daq pool-*` sēriju (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Citas `daq` apakškommandas, ko jūsu palīgs var izdomāt, nav pieejamas piegādātajās versijās un izraisa kļūdu.
{% endhint %}

## Kāpēc AI rakstīti skripti labi darbojas ar Chloros

Katra no šīm funkcijām ir reāla, pārbaudīta Chloros 1.2.0 darbība — tās novērš klasiskos kļūdu veidus, kas raksturīgi mašīnu rakstītai automatizācijai:

* **Nav nepieciešama sarežģīta konfigurācija.**SDK viedās savienošanas palīgrīki (`connect_camera`, `connect_array`, `connect_daq_sensor`) un apstrādes sākumpunkti (`ChlorosLocal`, `process_folder`)**automātiski palaista vietējo backend**. Ģenerētajam skriptam nav nepieciešams atvērts grafiskais interfeiss vai manuāli palaists serveris — tam ir nepieciešams tikai instalēts „desktop/CLI“ pakotne.
* **Visa apstrādes ķēde notiek ar vienu izsaukumu.** `chloros_sdk.process_folder("path", indices=["NDVI"])` no sākuma līdz galam veic importu → kalibrēšanu → atstarošanas koeficienta aprēķinu → indeksa eksportu. Mazāka virsma, mazāk vietu, kur ģenerētajai skriptam varētu rasties kļūdas.
* **Darbības bez izvades veic pašdiagnostiku.** Pēc `process()` darbības kopsavilkums tiek pievienots rezultātam, un katrs apstrādes norādījums (piem., *kāpēc* cikls nedeva izvadi) tiek atkārtoti izsūtīts kā Python `UserWarning` — tādējādi pat skripts, kas nekad nepārbauda rezultātu vārdnīcu, parāda diagnozi.
* **CLI kļūda ir skaidri pamanāma.**`chloros-cli process` izpilde, kas pieprasīja rezultātus, bet neizveidoja nevienu, izvada `Processing finished but wrote no image products.` un**beidzas ar nenulles kodu**, tādējādi čaulas skripti un CI to atklāj, vienkārši pārbaudot iziešanas kodu. Veiksmīgi izpildes ziņo par `Image products written: N`.

Viena asimetrija, par kuru asistentam jāzina: SDK `process()` apzināti **neizraisa** kļūdu, ja izpildes rezultātā nav neviena produkta — tā vietā tas ziņo caur kopsavilkumu/padomiem. Ja Python procesa ķēdei ir jāapstājas tukšā izpildes gadījumā, pārbaudiet kopsavilkumu (to dara 2. recepte).

## Brīdinājumi

* **Chloros+ ir nepieciešama pieteikšanās.**CLI un SDK prasa**maksas** Chloros+ līmeni, kas tiek piemērots servera pusē: pieprasījumi ar kodu `401 AUTH_REQUIRED` neizdodas, ja nav veikta pieteikšanās, un ar kodu `403 PLAN_UPGRADE_REQUIRED` — bezmaksas līmenī. Pirms ģenerēto skriptu palaides katrā datorā vienu reizi palaidiet `chloros-cli login`. Skatīt [Chloros+ Pieslēgšanās](chloros+-login.md).
* **Uztveršanas komandas vadītu reālo aparatūru.** Komandas `lattice` / `daq` / `project` un sesijas objekti SDK izveido savienojumu ar fiziskām kamerām un sensoriem, nodrošina datu plūsmu un tos aktivizē. Pirms pirmās izpildes pārskatiet ģenerēto skriptu un izpildiet to, klāt esot aparatūrai.
* **Veiciet izvades datu izlases pārbaudi.** Pirms rezultātu publicēšanas pārbaudiet produktu mapes un dažas pikseļu vērtības. Jo īpaši atstarojuma TIFF faili tiek mēroti atbilstoši avotam — izlasiet `Chloros:PixelScale` XMP tagu (LATTICE: 32768 = 1,0 atstarošanas koeficients; Survey3: 65535), nevis pieņemot dalītāju. Abās atsauces lapās tas ir aprakstīts sadaļā „Atstarošanas pikseļu nolasīšana”.
* **Nelielas grūtības, kas traucē ģenerētā koda darbību:**`pool-record` raksta**backend servera** failu sistēmā (noklusējumā `~/Documents/DAQ Live View/`); datoros ar vairākiem tīkla interfeisiem priekšroku dodiet `daq pool-connect --eth-host <ip-or-hostname>`, nevis automātiskajai atklāšanai; un izmantojiet `http://127.0.0.1:5000` (nekad `localhost`) visur, kur parādās aizmugures URL.
