# Chloros izmantošana kopā ar AI palīgiem

Šī rokasgrāmata ir paredzēta divām mērķauditorijām: cilvēkiem un AI palīgiem, ar kuriem cilvēki arvien biežāk strādā. Katrā lapā ir publicētas precīzas vērtības, noklusējumi un komandas, kuras var kopēt un ielīmēt, lai palīgs (Claude, ChatGPT, Copilot, programmēšanas aģents u. c.) jau pirmajā mēģinājumā varētu izveidot darbojošos Chloros automatizāciju.

Chloros versija: **

1.3.0**. CLI/SDK platformas: Windows 10/11 x64 un Linux (x86\_64 / Jetson aarch64).

## Ko nodot savam asistentam

| Resurss | URL | Kam tas paredzēts |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Mašīnlasāms indekss visām šīs rokasgrāmatas lapām. |
| **CLI atsauces materiāls** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Pilnīga `chloros-cli` komandu virsma: visas komandas, opcijas, noklusējuma iestatījumi, iziešanas kodi un izvades mapes noteikumi. Izstrādāts LLM lietošanai. |
| **SDK atsauces** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Pilnīgs `chloros_sdk` Python API: klases, paraksti, izņēmumi un praktiski piemēri. Sagatavots LLM lietošanai. |
| **Jebkura lapa neapstrādātā Markdown formātā** | pievienojiet `.md` lapas URL | piemēram, `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` atgriež lapu neapstrādātā Markdown formātā — ideāli piemērots ielīmēšanai konteksta logā vai iegūšanai no aģenta. |

Saites rokasgrāmatā: [CLI atsauces](reference/cli-reference.md) · [SDK atsauces](reference/sdk-reference.md).

{% hint style="info" %}
Abas atsauces lapas ir pilnīgas: palīgam, kas ir izlasījis vienu no tām, nav nepieciešama pārējā rokasgrāmata, lai uzrakstītu pareizu skriptu.
{% endhint %}

## Ļaujiet savam palīgam tieši palaist Chloros: `chloros-mcp`

Viss iepriekš minētais liek jūsu palīgam *uzrakstīt* skriptu vai komandu. Izmantojot `chloros-mcp`, asistents, kas atbalsta [Model Context Protocol](https://modelcontextprotocol.io) (MCP), piemēram, Claude Desktop, Claude Code vai Cursor, pats vada paChloros. Jūs lūdzat vienkāršā valodā, un tas izsauc servera rīkus:

* **Attēlu apstrāde:** izveidojiet projektu, importējiet uzņemto attēlu mapi, izvēlieties iestatījumus, apstrādājiet un ziņojiet, ko eksportēja izpilde.
* **Aparatūras vadība:** pieslēdziet LATTICE kameras, matricas un DAQ gaismas sensorus, apskatiet priekšskatījumu, iestatiet ekspozīciju un krāsu, uzņemiet attēlus, reģistrējiet un atjauniniet programmatūru.

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

Servera rīki aptver to, ko dara [Python SDK](api-python-sdk.md). Tie tiek instalēti kopā ar SDK, tāpēc parasti nav nepieciešams instalēt neko citu: Windows vidē Chloros instalētājs ievieto `chloros-mcp.exe` jūsu Python `Scripts` mapē, bet Linuxa `.deb` ievieto `chloros-mcp` mapē `/usr/bin`.

**Claude kods:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Claude Desktop:**pievienojiet to `claude_desktop_config.json` (to var atvērt, izvēloties**Iestatījumi &gt; Izstrādātājs &gt; Rediģēt konfigurāciju**), pēc tam pārstartējiet Claude Desktop.**Kursors** izmanto to pašu ierakstu `~/.cursor/mcp.json`.

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

Ja klients ziņo, ka serveris nav uzsācies, iespējams, `chloros-mcp` nav iekļauts `PATH`, ko tas redz: tad norādiet pilnu ceļu (`where chloros-mcp` Windows sistēmā, `which chloros-mcp` Linux sistēmā).

Jums ir nepieciešams, lai tajā pašā datorā būtu instalēts Chloros un jūs būtu tajā pieteicies — lietotnē vai izmantojot `chloros-cli login` — ar jebkuru maksas „Chloros+” pakalpojuma līmeni (Copper vai augstāku). Serveris nevar pieteikties jūsu vietā.

{% hint style="info" %}
Klienta iestatījumi katrai lietotnei, opcijas, visi rīki un darbības gaita: [MCP Server (AI Assistants)](reference/sdk-reference.md#mcp-server-ai-assistants) SDK atsauces materiālā.
{% endhint %}

## Komandu paraugi

Kopējiet, aizpildiet `<placeholders>` un ielīmējiet savā palīgā.

### 1. Apstrādājiet lidojuma mapi, lai iegūtu NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Uzraugiet uzņemto attēlu direktoriju partiju režīmā

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Pievienojiet LATTICE matrici un veiciet uzņemšanu

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. DAQ gaismas sensora spektru reģistrēšana

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
DAQ skriptu izpilde no komandrindas vienmēr notiek, izmantojot `daq pool-*` ģimeni (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). Citas `daq` apakškommandas, ko jūsu palīgs varētu izdomāt, nav pieejamas piegādātajās versijās un izraisa kļūdu.
{% endhint %}

## Kāpēc AI rakstīti skripti labi darbojas ar Chloros

Katrs no šiem piemēriem ir reāla, pārbaudīta Chloros 1.3.0 darbība — tie novērš klasiskos kļūdu veidus, kas raksturīgi mašīnu rakstītai automatizācijai:

* **Nav nepieciešama sarežģīta konfigurācija.**SDK viedās savienošanas palīgrīki (`connect_camera`, `connect_array`, `connect_daq_sensor`) un apstrādes sākumpunkti (`ChlorosLocal`, `process_folder`)**automātiski palaista vietējā aizmugure**. Ģenerētajam skriptam nav nepieciešams atvērts grafiskais interfeiss vai manuāli palaists serveris — tam nepieciešams tikai instalēts darbvirsmas/CLI pakotne.
* **Visa apstrādes ķēde ir viens izsaukums.** `chloros_sdk.process_folder("path", indices=["NDVI"])` no sākuma līdz beigām veic importu → kalibrēšanu → atstarošanas mērījumus → indeksa eksportu. Mazāka virsma, mazāk vietu, kur ģenerētajai skriptam varētu rasties kļūdas.
* **Darbības bez izvades veic pašdiagnostiku.** Pēc `process()` darbības kopsavilkums tiek pievienots rezultātam, un katrs apstrādes norādījums (piem., *kāpēc* darbība nedeva izvadi) tiek atkārtoti izvadīts kā Pythona skripts `UserWarning` — tādējādi pat skripts, kas nekad nepārbauda rezultātu vārdnīcu, parāda diagnozi.
* **CLI kļūda ir skaidri pamanāma.**`chloros-cli process` izpilde, kas pieprasīja rezultātus, bet neko neierakstīja, izvada `Processing finished but wrote no image products.` un**beidzas ar nenulles kodu**, tādējādi čaulas skripti un CI to atklāj, vienkārši pārbaudot iziešanas kodu. Veiksmīgas izpildes ziņo par kļūdu `Image products written: N`, kā arī par `(plus M clamp masks)`, ja tika ierakstītas maskas. Uztveršanas komandas tāpat nosauc katru failu, kuru tās nevarēja ierakstīt, un iziet ar kļūdu `1`.
* **Relatīvie ceļi nozīmē to, ko skripts sagaida.** Izmantojot lokālo backend, relatīvais izvades ceļš (`-o out/` CLI, `output_dir="out/"` SDK) tiek noteikts attiecībā pret mapi, no kuras tiek palaista komanda vai skripts.

Viena asimetrija, par kuru palīgam jāzina: SDK `process()` apzināti **nerada** kļūdu, ja izpildes rezultāts ir nulle — tā vietā tā ziņo caur kopsavilkumu/padomiem. Ja Python cauruļvads ir jāaptur tukšā izpildes gadījumā, pārbaudiet kopsavilkumu (kā to dara 2. recepte).

## Brīdinājumi

* **Nepieciešama Chloros+ pieteikšanās.**CLI un SDK prasa**maksas** Chloros+ līmeni, kas tiek piemērots servera pusē: pieprasījumi beidzas ar kļūdu `401 AUTH_REQUIRED`, ja nav pieteicies, un ar kļūdu `403 PLAN_UPGRADE_REQUIRED` bezmaksas līmenī. Pirms ģenerēto skriptu izpildes katrā datorā vienu reizi izpildiet komandu `chloros-cli login`. Skatīt [Chloros+ pieteikšanās](chloros+-login.md).
* **Uztveršanas komandas vada reālo aparatūru.** `lattice` / `daq` / `project` komandas, SDK sesijas objekti un `chloros-mcp` rīki izveido savienojumu ar fiziskajām kamerām un sensoriem, nodrošina datu plūsmu un tos aktivizē. Pirms pirmās palaišanas pārskatiet ģenerēto skriptu un palaidiet to, klāt esot aparatūrai.
* **Veiciet izvades datu izlases pārbaudi.** Pirms rezultātu publicēšanas pārbaudiet produkta mapes un dažas pikseļu vērtības. Jo īpaši atstarojuma TIFF faili tiek mērogi pēc avota — izlasiet `Chloros:PixelScale` XMP tagu (LATTICE faili to ietver: 32768 = 1,0 atstarojums; Survey3 failos tā nav, un tajos tiek izmantots 65535 = 1,0), nevis pieņemot dalītāju. Abās atsauces lapās tas ir aprakstīts sadaļā „Reflektances pikseļu nolasīšana”.
* **Nelielas nianses, kas var traucēt ģenerētā koda darbību:**`pool-record` raksta**backend servera** failu sistēmā (noklusējumā `~/Documents/DAQ Live View/`); datoros ar vairākiem tīkla interfeisiem priekšroku dodiet `daq pool-connect --eth-host <ip-or-hostname>`, nevis automātiskajai atklāšanai; izmantojiet `http://127.0.0.1:5000` (nekad ne `localhost`) visur, kur parādās aizmugures URL; skriptam, kas pārskata projekta izvades koka struktūru, jāizlaiž `clamp_masks/` mapes (to `*_clampmask.tif` faili ir maskas, nevis attēli — skatīt [Izvades attēlu formāti](output-image-formats.md#clamp-masks)); un Windows vidē, kur ir atspējoti gari ceļi, projekta mapes jāsaglabā īsas — projekta mape, kuras nosaukums pārsniedz aptuveni 130 rakstzīmes, tiek noraidīta pirms apstrādes sākšanas.
