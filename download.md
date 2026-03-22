---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# Lejupielāde

Lejupielādējiet jaunāko Chloros versiju, lai sāktu darbu ar multispektrālo attēlu apstrādi.

### Sistēmas prasības

#### Windows

| Prasība          | Minimālās                                              | Ieteicamās                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **Operētājsistēma** | Windows 10 (64 bitu)                                  | Windows 11 (64 bitu)                                  |
| **Procesors**        | Intel Core i5 vai līdzvērtīgs                          | Intel Core i7 vai labāks                              |
| **Atmiņa (RAM)**     | 8 GB                                                  | 16 GB vai vairāk                                         |
| **Grafikas karte**    | Saderīga ar DirectX 11                                | NVIDIA GPU ar 4 GB+ VRAM                            |
| **Uzglabāšanas vieta**          | 6 GB brīvās vietas                                       | SSD ar 10 GB+ brīvās vietas                            |
| **Ekrāns**          | 1920x1080                                            | 2560x1440 vai augstāka izšķirtspēja                                  |
| **Internets**         | Nepieciešams \[pēc izvēles] Chloros+ licences aktivizēšanai | Nepieciešams \[pēc izvēles] Chloros+ licences aktivizēšanai |

#### Linux amd64 (x86\_64)

| Prasības       | Minimālās                    | Ieteicamās               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribūcija**  | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+             |
| **Procesors**     | x86\_64 (Intel/AMD)        | Intel Core i7 vai labāks   |
| **Atmiņa (RAM)**  | 8 GB                        | 16 GB vai vairāk              |
| **Grafikas karte** | Nav (apstrāde ar procesoru)      | NVIDIA GPU ar 4 GB+ VRAM |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas             | SSD ar 10 GB+ brīvās vietas       |
| **Python**        | Python 3.7+ (SDK)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Prasība      | Minimālā                      | Ieteicamā                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **Atmiņa (RAM)** | 8 GB (kopīga GPU/CPU)         | 16 GB+ kopīga                    |
| **Uzglabāšanas vieta**      | 2 GB brīvās vietas               | NVMe SSD ar 10 GB+ brīvās vietas        |
| **Python**       | Python 3.7+ (SDK)        | Python 3.10+                    |

{% hint style="info" %}
**GPU paātrinājums**: Chloros+ lietotāji ar NVIDIA GPU var izmantot CUDA paātrinājumu, lai nodrošinātu ievērojami ātrāku apstrādi. Tas darbojas gan ar Windows (galddatoru GPU), gan ar Linux (galddatoru GPU un NVIDIA Jetson). Chloros+ lietotāji iegūst arī daudzpavedienu apstrādi maksimālai ātrdarbībai.
{% endhint %}

***

## Lejupielādēt Chloros

### Jaunākā stabilā versija (2026. gada 23. marts): Versija 1.1.0

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Lejupielādēt Chloros Windows (.exe)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Lejupielādēt Chloros Linux amd64 (.deb)</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Lejupielādēt Chloros Linux arm64 / Jetson (.deb)</a>

#### Windows instalētājs (GUI + CLI + Backend)

* **Faila tips**: .exe (Windows instalētājs)**Instalēšanas soļi:**

1. Lejupielādējiet iepriekš minēto .exe failu
2. Divreiz noklikšķiniet uz instalētāja, lai sāktu instalēšanu
3. Sekojiet instalēšanas vedņa norādēm
4. Izvēlieties instalācijas direktoriju (noklusējums: `C:\Program Files\[USER]\Chloros\`)
5. Pabeidziet instalāciju un palaidiet Chloros vai Chloros CLI
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

Sīkākas uzstādīšanas instrukcijas skatiet [Linux uzstādīšana](linux/linux-installation.md), bet norādījumus par konkrētiem Jetson modeļiem — [NVIDIA Jetson rokasgrāmatā](linux/nvidia-jetson-guide.md).

#### Python SDK (visas platformas)

```bash
pip install chloros-sdk
```

Dokumentāciju skatiet [API : Python SDK](api-python-sdk.md).

{% hint style="info" %}
**Linux lietotāji**: `.deb` pakete instalē CLI un backend. Python SDK tiek instalēts atsevišķi, izmantojot pip. Linux nav grafiskās lietotāja saskarnes — visa mijiedarbība notiek caur CLI vai SDK.
{% endhint %}

***

## Papildu resursi

### Python SDK

Izstrādātājiem un automatizācijas darba plūsmām instalējiet Chloros Python SDK:

```bash
pip install chloros-sdk
```

**Dokumentācija**: [API: Python SDK](api-python-sdk.md)**Prasības**: Ir jābūt instalētam Chloros (Windows instalētājs vai Linux `.deb` pakete), nepieciešama Chloros+ licences pieteikšanās***

## Kas ir iekļauts

### Windows instalētājs

* ✅ **Chloros GUI** - Pilnfunkciju grafiskā saskarne
* ✅ **Chloros CLI** - Komandrindas interfeiss (nepieciešama Chloros+ licence)
* ✅ **Chloros Backend** - Apstrādes dzinējs
* ✅ **Kameru profili** - Iepriekš konfigurēti MAPIR kameru šabloni

### Linux .deb pakete

* ✅ **Chloros CLI** - Komandrindas interfeiss (nepieciešama Chloros+ licence)
* ✅ **Chloros Backend** - Apstrādes dzinējs
* ✅ **Kameru profili** - Iepriekš konfigurēti MAPIR kameru šabloni
* ❌ Nav GUI — Linux ir tikai bezgalvas CLI/SDK

### Python SDK (pip, visas platformas)

* ✅ **Chloros SDK** - Python API (nepieciešama Chloros+ licence)***

## Pāreja uz Chloros+

Atbloķējiet papildu funkcijas ar Chloros+ abonementu:

* 🚀 **Daudzpavedienu apstrāde** - Apstrādājiet attēlus paralēli
* ⚡ **GPU (CUDA) paātrinājums** - Izmantojiet NVIDIA GPU jaudu
* 💻 **CLI piekļuve** — automatizējiet ar komandrindas rīkiem
* 🐍 **Python SDK** — programmatiska API piekļuve
* 📱 **Vairākas ierīces** – izmantojiet 2–10+ ierīcēs (atkarībā no plāna)
* **🐻 Uzlabota tekstūru atpazīstoša debayer metode** – augstas kvalitātes malu atpazīstoša debayer metode, kas apvienota ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayer trokšņus.
* 🧮 **Pielāgotas formulas** – izveidojiet pielāgotus multispektrālos indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Skatīt Chloros+ plānus un cenas</a></p>***

## Palīdzība instalēšanā

### Problēmu novēršana

**Instalēšana neizdodas ar kļūdas ziņojumu:**

* Pārliecinieties, ka jums ir administratora tiesības
* Pagaidām atspējojiet antivīrusu programmatūru
* Pārbaudiet, vai jūsu sistēma atbilst minimālajām sistēmas prasībām

**Lietojumprogramma nepalaižas (Windows):**

* Pārbaudiet, vai ir instalēta Windows 10/11 (64 bitu) versija
* Atjauniniet grafikas draiverus
* Pārbaudiet Windows notikumu skatītāju, lai iegūtu informāciju par kļūdu
* Sazinieties ar atbalsta dienestu, pievienojot kļūdu žurnālus

**CLI nepalaižas (Linux):**

* Pārbaudiet, vai `.deb` pakete ir instalēta pareizi: `dpkg -l | grep chloros`
* Pārbaudiet atļaujas: `sudo chmod +x /usr/bin/chloros-cli`
* Veiciet diagnostiku: `chloros-cli selftest`
* Pārbaudiet, vai nav trūkstošas bibliotēkas: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Problēmas ar licences aktivizēšanu:**

* Pārliecinieties, ka interneta savienojums darbojas
* Pārbaudiet autentifikācijas datus [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Pārbaudiet, vai ugunsmūris neblokē Chloros
* Sīkākas instrukcijas skatiet [Chloros+ Pieslēgšanās](chloros+-login.md)

### Atbalsta saņemšana

Vajadzīga palīdzība ar instalēšanu vai konfigurēšanu?

* 📧 **E-pasts**: info@mapir.camera
* 🌐 **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentācija**: [Sākums](./)
* ❓ **FAQ**: [Bieži uzdotie jautājumi](faq.md)***

## Izmaiņu žurnāls

<details>

<summary>Versija 1.1.0 (Jaunākā)</summary>

**Izlaišanas datums: 2026. gada marts**

**Jaunas funkcijas*** **Linux atbalsts** — Nativais CLI un SDK Linux amd64 (x86\_64) un arm64 (NVIDIA Jetson JetPack 6). Instalējiet, izmantojot `.deb` paketes.
* **NVIDIA Jetson atbalsts** — Optimizēta apstrāde Jetson Nano, Orin Nano, Orin NX un AGX Orin malu ierīcēm.
* **Dinamiskā aprēķinu pielāgošana** — Automātiska aparatūras atpazīšana un apstrādes stratēģijas optimizācija. Chloros pielāgojas jūsu aparatūrai no Jetson Nano līdz daudzprocesoru darbstacijai.
* **4-pavedienu apstrādes cauruļvads** — Vienlaicīgi darbojas atpazīšanas, kalibrēšanas, apstrādes un eksportēšanas pavedieni ar dinamisku GPU atmiņas sadali.
* **Jaunas CLI komandas** — `selftest` (sistēmas diagnostika) un `update` (Linux atjauninājumu pārvaldība).
* **Jauni CLI procesa karodziņi** — `--debayer` (standarta/tekstūru atpazīšana), `--indices` (indeksu norādīšana), `--target` (vispirms meklē mērķa apakšmapes, lai paātrinātu atklāšanu).
* **Jauni GUI izvēlnes elementi** — Pievienot failus, Pievienot mapi un Sākt/Pārtraukt apstrādi tagad pieejami galvenās izvēlnes nolaižamajā izvēlnē.**Uzlabojumi**

* Daudzplatformas aizmugures automātiskā atpazīšana (Windows un Linux ceļi)
* Uzlabots SDK `get_status()` ar progresa izsekošanu katram pavedienam
* Jauni SDK izņēmumi: `ChlorosConfigurationError`, `ChlorosAuthenticationError`
* Siltuma vadība un adaptīvā jaudas ierobežošana NVIDIA Jetson
* Automātiska atmiņas vadība ar OOM rezerves risinājumu uz GPU apstrādi ar flīzēm

</details>

<details>

<summary>Versija 1.0.5</summary>

**Izlaišanas datums: 2026. gada 10. februāris**

**Jaunas funkcijas*** **Tekstūru apzināta debayer metode \[Tikai Chloros+] -** Tekstūru apzināta metode izmanto augstas kvalitātes malu apzinātu debayer, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kas noņem gandrīz visu debayer troksni.
* **Atbalsts T4P kalibrēšanas mērķiem*** **Ātrāka Chloros+ GPU apstrāde, labāka atmiņas pārvaldība**

**Kļūdu labojumi*** Pilnīgi jauns lietotāja interfeiss (GUI), tagad būtu jādarbojas visos Windows datoros.

</details>

<details>

<summary>Versija 1.0.4</summary>

**Izlaišanas datums: 2026. gada 5. janvāris**

**Jaunas funkcijas*** **Attēla/metadatu pārslēgšana**: failu pārlūkā pievienota pārslēgšanas funkcija, lai izvēlētā attēla metadatus skatītu tabulā, nevis attēlu režģī
* **Attēlu režģa tālummaiņas sliders**: jauns lietotāja saskarnes sliders, lai pielāgotu sīktēlu izmēru (atbalsta arī CTRL + peles ratu)
* **Attēlu režģa eksportēšanas pogas**: Pogas augšējā rindā, lai pārslēgtos no JPG sīktēliem uz apstrādātiem eksportiem (Mērķi, Atstarošanās, Indekss, LUT)
* **Kartes cilne**: Jauna interaktīva 2D karte, kas parāda attēlu GPS atrašanās vietas marķierus
  * Atbalsta Google Maps un ESRI kartes flīzes (automātiski izvēlas labāko flīžu pakalpojumu, pamatojoties uz pieejamo tālummaiņas līmeni)
  * Pārvietojot peles kursoru, tiek parādīts sīktēlu priekšskatījums uz kartes marķieriem

**Kļūdu labojumi*** Uzlabota atbalsta Chloros instalēšanai datoros, kuros nav angļu valodas

</details>

<details>

<summary>Versija 1.0.3</summary>

**Izlaides datums: 2025. gada 20. decembris**

**Jaunas funkcijas*** Sākotnējā versija

**Uzlabojumi*** Sākotnējā versija

**Kļūdu labojumi*** Sākotnējā versija

**Zināmas problēmas*** Sākotnējā versija

</details>***

## Licences līgums**Autortiesību aizsargāta programmatūra** - Autortiesības (c) 2026 MAPIR Inc.

Nekāda neatļauta izmantošana, izplatīšana vai modificēšana ir aizliegta.

**Bezmaksas versija**: Pieejama personiskai un komerciālai lietošanai ar funkciju ierobežojumiem**Chloros+**: Abonementa licence papildu funkcijām un komerciālai izmantošanai
