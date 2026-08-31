---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Lejupielāde

Lejupielādējiet jaunāko Chloros versiju, lai sāktu darbu ar multispektrālo attēlu apstrādi.

### Sistēmas prasības

#### Windows

| Prasība          | Minimālā                                              | Ieteicamā                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Operētājsistēma** | Windows 10 (64 bitu)                                  | Windows 11 (64 bitu)                                  |
| **Procesors**        | Intel Core i5 vai līdzvērtīgs                          | Intel Core i7 vai labāks                              |
| **Atmiņa (RAM)**     | 8 GB                                                  | 16 GB vai vairāk                                         |
| **Grafikas karte**    | DirectX 11 saderīga                                | NVIDIA GPU ar 4 GB+ VRAM                            |
| **Cietais disks**          | 6 GB brīvās vietas                                       | SSD ar 10 GB vai vairāk brīvās vietas                            |
| **Ekrāns**          | 1920x1080                                            | 2560x1440 vai augstāka izšķirtspēja                                  |
| **Internets**         | Nepieciešams \[pēc izvēles] Chloros+ licences aktivizēšanai | Nepieciešams \[pēc izvēles] Chloros+ licences aktivizēšanai |

#### Linux amd64 (x86\_64)

| Prasība           | Minimālā                    | Ieteicamā               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribūcija**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Procesors**     | x86\_64 (Intel/AMD)        | Intel Core i7 vai labāks   |
| **Atmiņa (RAM)**  | 8 GB                        | 16 GB vai vairāk              |
| **Grafikas karte** | Nav nepieciešama (apstrāde ar procesoru)      | NVIDIA GPU ar 4 GB+ VRAM |
| **Uzglabāšanas vieta**       | 2 GB brīvās vietas             | SSD ar 10 GB+ brīvās vietas       |
| **Python**        | Python 3.7+ (SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Prasība      | Minimālā                      | Ieteicamā                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **Atmiņa (RAM)** | 8 GB (kopīga GPU/CPU)         | 16 GB+ kopīga                    |
| **Uzglabāšanas vieta**      | 2 GB brīvās vietas               | NVMe SSD ar 10 GB+ brīvas vietas        |
| **Python**       | Python 3.7+ (SDK gadījumā)        | Python 3.10+                    |

{% hint style="info" %}
**GPU paātrinājums**: Chloros+ lietotāji ar NVIDIA GPU var izmantot CUDA paātrinājumu, lai nodrošinātu ievērojami ātrāku apstrādi. Tas darbojas gan ar Windows (galddatoru GPU), gan ar Linux (galddatoru GPU un NVIDIA Jetson). Chloros+ lietotāji iegūst arī daudzpavedienu apstrādi, lai nodrošinātu maksimālu ātrumu.
{% endhint %}

***

## Lejupielādēt Chloros

### Jaunākā stabilā versija: 1.2.0

<!-- NOLAN: replace installer links + release date for 1.2.0 — the three download buttons below still point at the 1.1.0 Google Drive files, and the release date needs to be added to the heading above. -->



### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Lejupielādēt Chloros Windows (.exe)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Lejupielādēt Chloros versiju Linux amd64 (.deb)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Lejupielādēt Chloros versijai Linux arm64 / Jetson (.deb)</a>

#### Windows instalētājs (GUI + CLI + Backend)

* **Faila tips**: .exe (Windows instalētājs)**Instalēšanas soļi:**

1. Lejupielādējiet iepriekš minēto .exe failu
2. Divreiz noklikšķiniet uz instalētāja, lai sāktu instalēšanu
3. Sekojiet instalēšanas vedņa norādēm
4. Izvēlieties instalēšanas direktoriju (noklusējums: `C:\Program Files\MAPIR\Chloros\`)
5. Pabeidziet instalēšanu un palaidiet Chloros vai Chloros CLI
6. Piesakieties ar savu [MAPIR Cloud Chloros+ kontu](https://cloud.mapir.camera/pricing) (vai turpiniet ar bezmaksas versiju)

{% hint style="success" %}
Instalētājs automātiski pievieno `chloros-cli` jūsu sistēmas PATH, lai nodrošinātu piekļuvi no komandrindas.
{% endhint %}

#### Linux amd64 (.deb pakete — CLI + Backend)

* **Faila tips**: .deb (Debian/Ubuntu pakete)
* **Arhitektūra**: x86\_64 (amd64)

```bash
sudo dpkg -i chloros-amd64.deb
chloros-cli --version  # Verify installation
```

#### Linux arm64 — NVIDIA Jetson (.deb pakete — CLI + Backend)

* **Faila tips**: .deb (JetPack 6)
* **Arhitektūra**: aarch64 (arm64)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
chloros-cli --version  # Verify installation
```

Sīkākas uzstādīšanas instrukcijas skatiet sadaļā [Linux uzstādīšana](linux/linux-installation.md), bet norādījumus par konkrētiem Jetson modeļiem — [NVIDIA Jetson rokasgrāmatā](linux/nvidia-jetson-guide.md).

#### Python SDK (visas platformas)

Katrs instalētājs ietver atbilstošu `chloros_sdk` ratu, tādējādi SDK versija vienmēr atbilst instalētajai GUI/CLI/backend. Sistēmā Windows instalētājs to automātiski instalē jūsu sistēmā Python; Linux gadījumā `.deb` novieto ratu `/usr/lib/chloros/sdk/` un parāda instalēšanas komandu:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Tām sistēmām, kurās ir pieejams tikai pip (nav instalēta Chloros pakete), SDK ir pieejams arī PyPI:

```bash
pip install chloros-sdk
```

Skatīt [API : Python SDK](api-python-sdk.md) un [SDK atsauci](reference/sdk-reference.md), lai iepazītos ar dokumentāciju.

{% hint style="info" %}
**Linux lietotājiem**: `.deb` pakete instalē CLI un aizmuguri. Linux nav grafiskās lietotāja saskarnes — visa mijiedarbība notiek caur CLI vai SDK.
{% endhint %}

***

## Papildu resursi

### Python SDK

Izstrādātājiem un automatizācijas darba plūsmām iestatīiet Chloros, Python un SDK:

```bash
pip install chloros-sdk
```

**Dokumentācija**: [API: Python SDK](api-python-sdk.md)**Prasības**: ir jābūt instalētam Chloros (Windows instalētājs vai Linux `.deb` pakete), nepieciešama Chloros+ licences pieteikšanās***

## Kas ir iekļauts

### Windows instalētājs

* ✅ **Chloros GUI** — pilnfunkciju grafiskā saskarne
* ✅ **Chloros CLI** - Komandrindas saskarne (nepieciešama Chloros+ licence)
* ✅ **Chloros Backend** — apstrādes dzinējs
* ✅ **Kameru profili** — iepriekš konfigurēti MAPIR kameru veidnes

### Linux .deb pakete

* ✅ **Chloros CLI** — komandrindas saskarne (nepieciešama Chloros+ licence)
* ✅ **Chloros Backend** — apstrādes dzinējs
* ✅ **Kameru profili** — iepriekš konfigurēti MAPIR kameru šabloni
* ❌ Nav grafiskās lietotāja saskarnes — Linux darbojas tikai bez grafiskās saskarnes (headless) režīmā CLI/SDK

### Python SDK (pip, visas platformas)

* ✅ **Chloros SDK** — Python API (nepieciešama Chloros+ licence)***

## Pāreja uz Chloros+

Atbloķējiet papildu funkcijas ar Chloros+ abonementu:

* 🚀 **Daudzpavedienu apstrāde** — attēlu paralēla apstrāde
* ⚡ **GPU (CUDA) paātrinājums** — izmantojiet NVIDIA GPU jaudu
* 💻 **CLI piekļuve** - Automatizējiet ar komandrindas rīkiem
* 🐍 **Python SDK** – Programmatiska API piekļuve
* 📱 **Vairākas ierīces** — izmantojiet 2–10 un vairāk ierīcēs (atkarībā no plāna)
* **🐻 Uzlabota tekstūru ņemšana vērā debayeringa metode** — augstas kvalitātes malu ņemšana vērā debayeringa metode, kas apvienota ar AI/ML trokšņu samazināšanas modeli, kas novērš gandrīz visus debayeringa radītos trokšņus.
* 🧮 **Pielāgotas formulas** — izveidojiet pielāgotus multispektrālos indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Apskatiet Chloros+ plānus un cenas</a></p>***

## Palīdzība instalēšanā

### Problēmu novēršana

**Instalēšana neizdodas, parādot šādu kļūdas ziņojumu:**

* Pārliecinieties, ka jums ir administratora tiesības
* Uz laiku atspējojiet antivīrusu programmatūru
* Pārbaudiet, vai jūsu sistēma atbilst minimālajām sistēmas prasībām

**Programma nepalaižas (Windows):**

* Pārbaudiet, vai ir instalēta Windows 10/11 (64 bitu versija)
* Atjauniniet grafikas draiverus
* Pārbaudiet Windows notikumu skatītāju, lai iegūtu informāciju par kļūdu
* Sazinieties ar atbalsta dienestu, pievienojot kļūdu žurnālus

**CLI nepalaižas (Linux):**

* Pārbaudiet, vai `.deb` pakete ir pareizi instalēta: `dpkg -l | grep chloros`
* Pārbaudiet atļaujas: `sudo chmod +x /usr/bin/chloros-cli`
* Veiciet diagnostiku: `chloros-cli selftest`
* Pārbaudiet, vai nav trūkstošu bibliotēku: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Problēmas ar licences aktivizēšanu:**

* Pārliecinieties, ka interneta savienojums darbojas
* Pārbaudiet autentifikācijas datus vietnē [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Pārbaudiet, vai ugunsmūris neblokē Chloros
* Sīkākas instrukcijas skatiet [Chloros+ Pieslēgšanās](chloros+-login.md)

### Atbalsta saņemšana

Vajadzīga palīdzība ar instalēšanu vai konfigurēšanu?

* 📧 **E-pasts**: info@mapir.camera
* 🌐 **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentācija**: [Sākums](./)
* ❓ **FAQ**: [Bieži uzdotie jautājumi](faq.md)***

## Programmatūras atjauninājumi

Chloros pārbauda, vai ir pieejami atjauninājumi, paziņo, kad ir pieejama jauna versija, un norāda saiti uz šo lejupielādes lapu — atjauninājumu veicat, palaistot jauno parakstīto instalētāju. Jūsu iestatījumi un projekti paliek nemainīgi pēc atjauninājuma. Linux un Jetson versijās `chloros-cli update` pārbauda, vai ir pieejama jaunāka versija, un piedāvā lejupielādēt un instalēt atbilstošo `.deb` (šī komanda ir pieejama tikai Linux versijā).

***

## Izmaiņu žurnāls**Versija 1.2.0 (jaunākā)**— pilnu funkciju sarakstu skatiet sadaļā**Jaunumi Chloros 1.2.0** lappusē [Sākums](./).

<details>

<summary>Versija 1.0.5</summary>

**Izlaides datums: 2026. gada 10. februāris**

**Jaunas funkcijas*** **Tekstūru ņemjošā debayeringa metode \[Tikai Chloros+] —** Tekstūru ņemjošā metode izmanto augstas kvalitātes malu ņemjošu debayeringu, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayeringa radītos trokšņus.
* **Atbalsts T4P kalibrēšanas mērķiem*** **Ātrāka Chloros+ GPU apstrāde, labāka atmiņas pārvaldība**

**Kļūdu labojumi*** Pilnīgi jauna lietotāja saskarne (GUI), tagad būtu jādarbojas visos Windows datoros.

</details>

<details>

<summary>Versija 1.0.4</summary>

**Izlaides datums: 2026. gada 5. janvāris**

**Jaunas funkcijas*** **Attēla/metadatu pārslēgšana**: failu pārlūkā pievienota pārslēgšanas funkcija, lai izvēlētā attēla metadatus skatītu tabulā, nevis attēlu režģī
* **Attēlu režģa tālummaiņas slīdnis**: jauns lietotāja saskarnes slīdnis sīktēlu izmēra regulēšanai (atbalsta arī CTRL + peles ratiņu)
* **Attēlu režģa eksportēšanas pogas**: Pogas augšējā rindā, lai pārslēgtu sīktēlus no JPG uz apstrādātiem eksportiem (Mērķi, Reflektance, Indekss, LUT)
* **Kartes cilne**: Jauna interaktīva 2D karte, kas parāda attēlu GPS atrašanās vietas marķierus
  * Atbalsta „Google Maps“ un ESRI kartes flīzes (automātiski izvēlas labāko flīžu pakalpojumu atkarībā no pieejamā tālummaiņas līmeņa)
  * Pārvietojot peles kursoru pār sīktēliem, tiek parādīts priekšskatījums uz kartes atzīmēm

**Kļūdu labojumi*** Uzlabots atbalsts Chloros instalēšanai datoros, kuros valoda nav angļu

</details>

<details>

<summary>Versija 1.0.3</summary>

**Izlaišanas datums: 2025. gada 20. decembris**

**Jaunas funkcijas*** Sākotnējā versija

**Uzlabojumi*** Sākotnējā versija

**Kļūdu labojumi*** Sākotnējā versija

**Zināmās problēmas*** Sākotnējā versija

</details>***

## Licences līgums**Autortiesību aizsargāta programmatūra** – Autortiesības (c) 2026 MAPIR Inc.

Aizliegta neatļauta izmantošana, izplatīšana vai modificēšana.

**Bezmaksas versija**: pieejama personiskai un komerciālai lietošanai ar funkciju ierobežojumiem**Chloros+**: abonementa licence papildu funkcijām un komerciālai izmantošanai
