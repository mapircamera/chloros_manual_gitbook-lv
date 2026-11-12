# Linux pārskats

Chloros 1.3.0 nodrošina Linux sistēmai paredzētu atbalstu **CLI**un**Python SDK** — multispektrālo attēlu apstrāde bez monitora, kā arī LATTICE kameru un DAQ gaismas sensoru vadība reāllaikā — uz Linux darbstacijām, serveriem un NVIDIA Jetson maljas ierīcēm.

{% hint style="info" %}
**Linux vidē nav darbvirsmas GUI.**Chloros darbvirsmas GUI ir pieejama tikai Windows vidē. Linux lietotāji ar Chloros mijiedarbojas, izmantojot [CLI](../CLI.md) un [Python SDK](../api-python-sdk.md). `.deb` pievieno**Chloros CLI** ierakstu jūsu lietojumprogrammu izvēlnē — tas vienkārši atver termināļa emulatoru, kurā darbojas `chloros-cli`.
{% endhint %}

***

## Platformu atbalsta tabula

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **GUI darbvirsma** | Jā | Nav pieejams | Nē | Nē |
| **CLI** (`chloros-cli`) | Jā | Jā | Jā | Jā |
| **Python SDK** (`chloros-sdk`) | Jā | Jā | Jā | Jā |
| **Attēlu apstrādes procesa ķēde** | Jā | Jā | Jā | Jā |
| **LATTICE kameru vadība (reāllaikā)** | Jā (cilne „Cameras”) | Jā (`chloros-cli lattice`, SDK) | Jā | Jā |
| **DAQ gaismas sensori (reāllaikā)** | Jā (cilne „Gaismas sensori”) | Jā (`chloros-cli daq pool-*`, SDK) | Jā | Jā |
| **PTP laika sinhronizācija (galvenais dators ir grandmaster)** | Jā | Jā (`chloros-cli time-sync`) | Jā | Jā |
| **GPU paātrinājums (CUDA)** | Jā | Jā | Jā | Jā (JetPack 6) |
| **Tekstūru ņemot vērā debayer** (nepieciešams CUDA GPU) | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) |
| **Dinamiskā aprēķinu pielāgošana** | Jā | Jā | Jā | Jā |
| **Aizmugurējā sistēma kā sistēmas pakalpojums** (`chloros-backend@<user>.service`) | Nē | Nē | Jā (pēc izvēles) | Jā (pēc izvēles) |
| **Atjauninātājs uz vietas** (`chloros-cli update`) | Nē (palaidiet instalētāju) | Nē (palaidiet instalētāju) | Jā | Jā |

***

## Atbalstītās arhitektūras

| Arhitektūra | Apraksts | Pakete |
| --- | --- | --- |
| **amd64 (x86_64)** | Standarta darbvirsmas/serveru procesori (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM procesori — NVIDIA Jetson Orin sērija (JetPack 6) | `chloros_<version>_arm64_jp6.deb` |

## Atbalstītās Linux distribūcijas

* **Ubuntu 22.04 LTS vai jaunāka** (amd64)
* **Debian 12 vai jaunāka** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson Orin platformas)

***

## Kas pieejams Linux lietotājiem

* **Chloros CLI** — pilnīga komandrindas saskarne partiju apstrādei, automatizācijai un skriptu izstrādei
* **Chloros Python SDK** — programmējamā Python saskarne pētniecības procesiem un pielāgotiem rīkiem, ko instalē `.deb` sistēmai `python3` (versijai atbilstošs „wheel” fails virtuālajām vidēm atrodas `/usr/lib/chloros/sdk/`; PyPI ir paredzēts datoriem bez Chloros pakotnes)
* **LATTICE kameru vadība** — atklājiet, pieslēdziet, konfigurējiet un veiciet uzņemšanu no LATTICE kamerām un sinhronizētiem daudzkameru masīviem, izmantojot `chloros-cli lattice` un SDK; `.deb` ietver Arena SDK izpildes vidi, kas nepieciešama kamerām
* **DAQ gaismas sensoru vadība** — savienojiet DAQ-U/M/E sensorus, straumējiet kalibrētus spektrus un ierakstiet `.daq` failus, izmantojot `chloros-cli daq pool-*` un SDK
* **PTP laika sinhronizācija** — Chloros backend darbojas kā PTP grandmaster, kuram pakļautas „LATTICE“ kameras un DAQ-E sensori; pārbaudiet to ar `chloros-cli time-sync` un nodrošiniet tā darbību bez lietotāja iejaukšanās, izmantojot `chloros-backend@<user>.service` systemd vienību (skatiet [Linux instalācija](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Projekta automatizācija** — vadiet saglabātos projektus bez lietotāja iejaukšanās, izmantojot `chloros-cli project` un SDK `open_project`
* **GPU paātrinājums** — CUDA paātrināta apstrāde uz NVIDIA GPU (galddatoru GPU uz amd64 un Jetson GPU ar JetPack 6)
* **Dinamiskā aprēķinu pielāgošana** — automātiska aparatūras atpazīšana un apstrādes stratēģijas izvēle, ar `CHLOROS_STRATEGY` pārrakstīšanu kā speciālu avārijas izeju
* **Visas apstrādes funkcijas** — tā pati apstrādes virkne kā Windows vidē: kalibrēšana, vinjetes korekcija, veģetācijas indeksi un visi eksporta formāti
* **Chloros+ funkcijas** — daudzpavedienu (pipeline) apstrāde, Texture Aware debayer (uz CUDA GPU) un pielāgotie indeksi, izmantojot maksas Chloros+ plānu

## Kas nav pieejams Linux lietotājiem

* **Datorvides grafiskā saskarne** — nav grafiskās saskarnes; visa mijiedarbība notiek caur CLI vai Python SDK
* **Attēlu skatītājs** — nav interaktīva attēlu skatītāja, režģa skata vai kartes marķieru
* **Vizuālā projektu vadība** — projekti tiek izveidoti un vadīti, izmantojot CLI komandas un SDK izsaukumus (pašu aparatūru — kameras, sensori, attēlu uzņemšana — joprojām var pilnībā kontrolēt no termināļa)

***

## Licences prasības

Piekļuvei CLI un SDK ir nepieciešams **maksas Chloros+ līmenis — Copper vai augstāks**(Copper, Bronze, Silver, Gold). Bezmaksas**Iron** līmenim nav piekļuves CLI/SDK. Šis ierobežojums tiek piemērots no servera puses, nevis tikai CLI:

| Situācija | Servera atbilde |
| --- | --- |
| Nav pieteicies | `401` ar `error_code: AUTH_REQUIRED` |
| Pieslēdzies bezmaksas „Iron” līmenī | `403` ar `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` darbojas jebkurā līmenī — tas ir vienīgais maršruts, kas ir atbrīvots no vārtiem — tādēļ atteikuma iemesls vienmēr ir redzams.

***

## Sākums Linux

1. **Instalējiet Chloros** — skatiet [Linux instalācija](linux-installation.md), lai uzstādītu `.deb`
2. **Pārbaudiet** — `chloros-cli --version` izdrukā `Chloros CLI 1.3.0`; `chloros-cli selftest` palaista 7 soļu diagnostika
3. **Pārbaudiet Python SDK** — `.deb` to instalē: `python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"` (virtuālajā vidē — `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`)
4. **Piesakieties** — `chloros-cli login your@email.com 'your-password'` (vienu reizi katrā datorā; pakotnes atjauninājumi saglabā pieteikšanās datus, bet pakotnes noņemšana tos dzēš)
5. **Apstrādājiet savu pirmo datu kopu** — `chloros-cli process ~/datasets/flight001`

Attiecībā uz NVIDIA Jetson skatiet īpašo [NVIDIA Jetson rokasgrāmatu](nvidia-jetson-guide.md), kurā aprakstīta platformai specifiska konfigurācija, siltuma izdalīšanās un izvietošana praksē.

***

## Turpmākie soļi

* [Instalēšana Linux](linux-installation.md) — detalizēta instalēšana, failu atrašanās vietas un problēmu novēršana amd64 un arm64
* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson-specifiska konfigurācija, atmiņas un termiskā uzvedība, izvietošana ekspluatācijā
* [CLI: komandrinda](../CLI.md) — CLI rokasgrāmata
* [API: Python SDK](../api-python-sdk.md) — SDK rokasgrāmata
* [CLI atsauces materiāls](../reference/cli-reference.md) un [SDK atsauces materiāls](../reference/sdk-reference.md) — izsmeļošs komandu/API saraksts versijai 1.3.0
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — kā Chloros pielāgojas jūsu aparatūrai

{% hint style="info" %}
**Šīs rokasgrāmatas lasīšana, izmantojot programmēšanu.** Katra lapa ir pieejama arī kā neapstrādāts Markdown formāts ar savu URL, kam pievienots `.md` (piemēram, `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), un visa rokasgrāmatas satura rādītājs ir publicēts [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
