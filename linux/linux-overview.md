# Linux pārskats

Chloros 1.2.0 nodrošina Linux atbalstu **CLI**un**Python SDK** — bezmonitora multispektrālo attēlu apstrādei, kā arī LATTICE kameru un DAQ gaismas sensoru vadībai reāllaikā — uz Linux darbstacijām, serveriem un NVIDIA Jetson maljas ierīcēm.

{% hint style="info" %}
**Linux nav darbvirsmas grafiskās lietotāja saskarnes (GUI).**Chloros darbvirsmas GUI ir pieejama tikai Windows. Linux lietotāji mijiedarbojas ar Chloros, izmantojot [CLI](../CLI.md) un [Python SDK](../api-python-sdk.md). `.deb` pievieno**Chloros CLI** ierakstu jūsu lietojumprogrammas izvēlnē — tas vienkārši atver termināļa emulatoru, kurā darbojas `chloros-cli`.
{% endhint %}

***

## Platformu atbalsta tabula

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Datoru grafiskā lietotāja saskarne** | Jā | Nav pieejams | Nē | Nē |
| **CLI** (`chloros-cli`) | Jā | Jā | Jā | Jā |
| **Python SDK** (`chloros-sdk`) | Jā | Jā | Jā | Jā |
| **Attēlu apstrādes procesa ķēde** | Jā | Jā | Jā | Jā |
| **LATTICE kameru vadība (reāllaikā)** | Jā (cilne „Kameras”) | Jā (`chloros-cli lattice`, SDK) | Jā | Jā |
| **DAQ gaismas sensori (reāllaikā)** | Jā (cilne „Gaismas sensori”) | Jā (`chloros-cli daq pool-*`, SDK) | Jā | Jā |
| **PTP laika sinhronizācija (galvenais dators ir grandmaster)** | Jā | Jā (`chloros-cli time-sync`) | Jā | Jā |
| **GPU paātrinājums (CUDA)** | Jā | Jā | Jā | Jā (JetPack 6) |
| **Tekstūru ņemot vērā debayer** | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) |
| **Dinamiskā aprēķinu pielāgošana** | Jā | Jā | Jā | Jā |
| **Aizmugurējā sistēma kā sistēmas pakalpojums** (`chloros-backend.service`) | Nē | Nē | Jā (pēc izvēles) | Jā (pēc izvēles) |
| **Atjauninātājs uz vietas** (`chloros-cli update`) | Nē (izpildiet instalētāju) | Nē (izpildiet instalētāju) | Jā | Jā |***

## Atbalstītās arhitektūras

| Arhitektūra | Apraksts | Pakete |
| --- | --- | --- |
| **amd64 (x86_64)** | Standarta galddatoru/serveru procesori (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM procesori — NVIDIA Jetson Orin sērija | `chloros_<version>_arm64_jp6.deb` (JetPack 6 versija) |

## Atbalstītās Linux distribūcijas

* **Ubuntu 22.04 LTS vai jaunāka versija** (amd64)
* **Debian 12 vai jaunāka versija** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson Orin platformas)***

## Ko iegūst Linux lietotāji

* **Chloros CLI** — pilnīga komandrindas saskarne partiju apstrādei, automatizācijai un skriptu izveidei
* **Chloros Python SDK** — programmatiska Python saskarne pētniecības procesiem un pielāgotiem rīkiem (instalējama no PyPI, kā arī iekļauta `.deb` kā versijai atbilstošs wheel)
* **LATTICE kameru vadība** — LATTICE kameru un sinhronizētu daudzkameru sistēmu atklāšana, savienošana, konfigurēšana un attēlu uzņemšana, izmantojot `chloros-cli lattice` un SDK; `.deb` ietver Arena SDK izpildes vidi, kas nepieciešama kamerām
* **DAQ gaismas sensoru vadība** — pieslēdziet DAQ-U/M/E sensorus, pārraidiet kalibrētus spektrus un ierakstiet `.daq` failus, izmantojot `chloros-cli daq pool-*` un SDK
* **PTP laika sinhronizācija** — Chloros backend darbojas kā PTP grandmaster, kuram pakļautas LATTICE kameras un DAQ-E sensori; pārbaudiet to ar `chloros-cli time-sync`, un nodrošiniet tā darbību bez monitora, izmantojot `chloros-backend.service` systemd vienību (skatiet [Linux instalācija](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Projekta automatizācija** — vadiet saglabātos projektus bez lietotāja iejaukšanās, izmantojot `chloros-cli project` un SDK modulu `open_project`
* **GPU paātrinājums** — CUDA paātrināta apstrāde uz NVIDIA GPU (galddatoriem un Jetson)
* **Dinamiskā aprēķinu pielāgošana** — automātiska aparatūras atpazīšana un apstrādes stratēģijas izvēle, izmantojot `CHLOROS_STRATEGY` pārrakstīšanu kā ekspertu avārijas izeju
* **Visas apstrādes funkcijas** — tā pati apstrādes virkne kā Windows: kalibrēšana, vinjetes korekcija, veģetācijas indeksi un visi eksporta formāti
* **Chloros+ funkcijas** — daudzpavedienu (pipeline) apstrāde, Texture Aware debayer un pielāgotie indeksi, izmantojot maksas Chloros+ plānu

## Kas nav pieejams Linux lietotājiem

* **Datoru lietotāja saskarne** — nav grafiskās saskarnes; visa mijiedarbība notiek caur CLI vai Python SDK
* **Attēlu skatītājs** — nav interaktīva attēlu skatītāja, režģa skata vai kartes marķieru
* **Vizuālā projektu vadība** — projekti tiek izveidoti un vadīti, izmantojot CLI komandas un SDK izsaukumus (pašu aparatūru — kameras, sensori, ierakstīšanas iekārtas — joprojām var pilnībā vadīt no termināļa)***

## Licences prasības

Lai piekļūtu CLI un SDK, ir nepieciešams **maksas Chloros+ līmenis — Copper vai augstāks**(Copper, Bronze, Silver, Gold). Bezmaksas**Iron** līmenim nav piekļuves CLI/SDK. Šo ierobežojumu piemēro backend, nevis tikai CLI:

| Situācija | Backend atbilde |
| --- | --- |
| Nav pieteicies | `401` ar `error_code: AUTH_REQUIRED` |
| Pieslēdzies bezmaksas „Iron” līmenī | `403` ar `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` darbojas jebkurā līmenī — tas ir vienīgais maršruts, kas ir atbrīvots no vārtiem — tādēļ atteikuma iemesls vienmēr ir redzams.

***

## Kā sākt darbu ar Linux

1. **Instalējiet Chloros** — skatiet [Linux instalēšana](linux-installation.md), lai uzzinātu par `.deb` instalēšanu
2. **Pārbaudiet** — `chloros-cli --version` izdrukā `Chloros CLI 1.2.0`; `chloros-cli selftest` palaista 7 soļu diagnostika
3. **Instalējiet Python un SDK** (pēc izvēles) — `pip install chloros-sdk`
4. **Piesakieties** — `chloros-cli login your@email.com 'your-password'` (vienreiz katrai ierīcei un atkārtoti pēc katras programmatūras atjaunināšanas)
5. **Apstrādājiet savu pirmo datu kopu** — `chloros-cli process ~/datasets/flight001`

Attiecībā uz NVIDIA Jetson skatiet īpašo [NVIDIA Jetson rokasgrāmatu](nvidia-jetson-guide.md), kurā aprakstīta platformai specifiska konfigurācija, siltuma izkliedēšana un izvietošana darbā.

***

## Turpmākie soļi

* [Linux instalācija](linux-installation.md) — detalizēta instalācija, failu atrašanās vietas un problēmu novēršana amd64 un arm64
* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson specifiska konfigurācija, atmiņas un siltuma vadība, izvietošana reālos apstākļos
* [CLI : Komandrinda](../CLI.md) — CLI rokasgrāmata
* [API : Python SDK](../api-python-sdk.md) — SDK rokasgrāmata
* [CLI atsauce](../reference/cli-reference.md) un [SDK atsauce](../reference/sdk-reference.md) — izsmeļošs komandu/API saraksts versijai 1.2.0
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — kā Chloros pielāgojas jūsu aparatūrai

{% hint style="info" %}
**Šīs rokasgrāmatas lasīšana, izmantojot programmēšanu.** Katra lapa ir pieejama arī kā neapstrādāts Markdown formāts savā atsevišķajā URL un `.md` (piemēram, `https://mapir.gitbook.io/chloros/linux/linux-installation.md`), un visa rokasgrāmatas satura rādītājs ir publicēts [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
