# Linux pārskats

Chloros 1.1.0 versija nodrošina Linux atbalstu **CLI**un**Python SDK**, nodrošinot bezmonitora multispektrālo attēlu apstrādi uz Linux darbstacijām, serveriem un NVIDIA Jetson malu ierīcēm.

{% hint style="info" %}
**Linux nav GUI.** Chloros darbvirsmas GUI ir pieejama tikai Windows. Linux lietotāji mijiedarbojas ar Chloros, izmantojot [CLI](../CLI.md) un [Python SDK](../api-python-sdk.md).
{% endhint %}

***

## Platformas atbalsta matrica

| Funkcija | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Datoru GUI** | Jā | Nav pieejams | Nē | Nē |
| **CLI** | Jā | Jā | Jā | Jā |
| **Python SDK** | Jā | Jā | Jā | Jā |
| **GPU paātrinājums (CUDA)** | Jā | Jā | Jā | Jā (JetPack 6) |
| **Tekstūru atpazīstošais debayer** | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) | Jā (Chloros+) |
| **Dinamiskā aprēķinu pielāgošana** | Jā | Jā | Jā | Jā |***

## Atbalstītās arhitektūras

| Arhitektūra | Apraksts | Instalēšanas metode |
| --- | --- | --- |
| **amd64 (x86_64)** | Standarta darbvirsmas/serveru procesori (Intel, AMD) | `.deb` pakete |
| **arm64 (aarch64)** | ARM bāzes procesori, galvenokārt NVIDIA Jetson | `.deb` pakete (JetPack 6) |

## Atbalstītās Linux distribūcijas

* **Ubuntu 20.04+** (amd64)
* **Debian 11+** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson platformas)***

## Ko iegūst Linux lietotāji

* **Chloros CLI** — Pilna komandrindas saskarne partiju apstrādei, automatizācijai un skriptu izstrādei
* **Chloros Python SDK** — Programmatiska Python saskarne (`pip install chloros-sdk`) integrācijai pētniecības procesā un pielāgotos rīkos
* **GPU paātrinājums** — CUDA paātrināta apstrāde uz NVIDIA GPU (galddatoriem un Jetson)
* **Dinamiskā aprēķinu pielāgošana** — Automātiska aparatūras noteikšana un apstrādes stratēģijas optimizācija
* **Visas apstrādes funkcijas** — Tā pati multispektrālā apstrādes procesa virkne kā Windows (kalibrēšana, vinjetes korekcija, veģetācijas indeksi, visi eksporta formāti)
* **Chloros+ funkcijas** — daudzpavedienu apstrāde, tekstūru atpazīstošs debayer, pielāgoti indeksi (ar Chloros+ licenci)

## Kas Linux lietotājiem nav pieejams

* **Datoru grafiskā lietotāja saskarne** — Nav grafiskās saskarnes; visa mijiedarbība notiek caur CLI vai Python SDK
* **Attēlu skatītājs** — Nav interaktīva attēlu skatītāja, režģa skata vai kartes marķieru
* **Vizuālā projektu vadība** — Projektus pārvalda, izmantojot CLI komandas un SDK izsaukumus***

## Darba sākšana ar Linux

1. **Instalējiet Chloros** — skatiet [Linux instalēšana](linux-installation.md), lai uzzinātu par `.deb` paketes instalēšanu
2. **Instalējiet Python SDK** (pēc izvēles) — `pip install chloros-sdk`
3. **Aktivizējiet savu licenci** — `chloros-cli login your@email.com 'password'`
4. **Apstrādājiet savu pirmo datu kopu** — `chloros-cli process ~/datasets/flight001`

NVIDIA Jetson lietotājiem skatiet īpašo [NVIDIA Jetson rokasgrāmatu](nvidia-jetson-guide.md), lai uzzinātu par platformai specifisku konfigurāciju un optimizāciju.

***

## Nākamie soļi

* [Linux instalācija](linux-installation.md) — detalizētas instalācijas instrukcijas amd64 un arm64
* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson specifiska konfigurācija, siltuma vadība un izvietošana
* [CLI : Komandu rinda](../CLI.md) — Pilna CLI atsauce
* [API : Python SDK](../api-python-sdk.md) — Pilnīga SDK atsauce
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — Kā Chloros pielāgojas jūsu aparatūrai
