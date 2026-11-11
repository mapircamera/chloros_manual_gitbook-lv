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
| **Grafikas karte**    | DirectX 11 saderīga                                | NVIDIA GPU ar 4 GB vai vairāk VRAM                            |
| **Uzglabāšanas vieta**          | 6 GB brīvās vietas                                       | SSD ar 10 GB vai vairāk brīvās vietas                            |
| **Ekrāns**          | 1920x1080                                            | 2560x1440 vai augstāka izšķirtspēja                                  |
| **Internets**         | Nepieciešams \[pēc izvēles] „Chloros+“ licences aktivizēšanai | Nepieciešams \[pēc izvēles] „Chloros+“ licences aktivizēšanai |

#### Linux amd64 (x86\_64)

| Prasības       | Minimālās                    | Ieteicamās               |
| ----------------- | -------------------------- | ------------------------- |
| **Distribūcija**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **Procesors**     | x86\_64 (Intel/AMD)        | Intel Core i7 vai labāks   |
| **Atmiņa (RAM)**  | 8 GB                        | 16 GB vai vairāk              |
| **Grafikas karte** | Nav nepieciešama (apstrāde ar procesoru)      | NVIDIA GPU ar 4 GB+ VRAM |
| **Uzglabāšanas vieta**       | 2 GB brīvās vietas             | SSD ar 10 GB+ brīvās vietas       |
| **Python**        | Python 3.7+ (SDK vajadzībām)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Prasības      | Minimālās                      | Ieteicamās                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platforma**     | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **Atmiņa (RAM)** | 8 GB (kopīga GPU/CPU)         | 16 GB+ kopīga                    |
| **Uzglabāšanas vieta**      | 2 GB brīvās vietas               | NVMe SSD ar 10 GB+ brīvas vietas        |
| **Python**       | Python 3.7+ (SDK vajadzībām)        | Python 3.10+                    |

{% hint style="info" %}
**GPU paātrinājums**: „Chloros+“ lietotāji ar NVIDIA GPU var izmantot CUDA paātrinājumu, lai nodrošinātu ievērojami ātrāku apstrādi. Tas darbojas ar NVIDIA galddatoru GPU operētājsistēmās Windows un Linux (amd64), kā arī ar NVIDIA Jetson ar JetPack 6. „Chloros+“ lietotāji iegūst arī daudzpavedienu apstrādi, lai nodrošinātu maksimālu ātrumu.
{% endhint %}

***

## Lejupielādēt Chloros


### Jaunākā stabilā versija: versija 1.3.0 (2026. gada 11. novembris)

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Lejupielādēt Chloros operētājsistēmai Windows (.exe)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Lejupielādēt Chloros operētājsistēmai Linux amd64 (.deb)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Lejupielādēt Chloros Linux arm64 / Jetson (.deb)</a>


#### Windows instalētājs (GUI + CLI + Backend)

* **Faila tips**: .exe (Windows instalētājs)

**Instalēšanas soļi:**

1. Lejupielādējiet iepriekš minēto .exe failu
2. Divreiz noklikšķiniet uz instalētāja, lai sāktu instalēšanu
3. Sekojiet instalēšanas vedņa norādēm
4. Izvēlieties instalēšanas direktoriju (noklusējums: `C:\Program Files\Chloros\`)
5. Lapā **Izvēlieties papildu uzdevumus**atstājiet atzīmētas abas**Sistēmas iestatījumi** opcijas (ieteicams — skatiet zemāk)
6. Pabeidziet instalēšanu un palaidiet Chloros vai Chloros CLI
7. Piesakieties ar savu [MAPIR Cloud Chloros+ kontu](https://cloud.mapir.camera/pricing) (vai turpiniet ar bezmaksas versiju)

**Sistēmas iestatījumu opcijas** (abas pēc noklusējuma ir atzīmētas):

| Opcija, kā norādīts instalētājā | Kāda ir tās funkcija | Iespēja atteikties no automātiskās instalācijas |
| --- | --- | --- |
| **Iespējot garus ceļus Windows** (ļauj projekta mapēm atrasties dziļāk par 260 rakstzīmēm; ieteicams) | Iestata Windows sistēmas līmeņa iestatījumu `LongPathsEnabled` uz 1. Windows sistēmā šis iestatījums ir atspējots pēc noklusējuma, un tādā gadījumā neviens faila ceļš nedrīkst būt garāks par 260 rakstzīmēm; Chloros produkti atrodas aptuveni 130 rakstzīmes zemāk par projekta mapi, tādēļ, ja garie ceļi ir atspējoti, tiek atteikta dziļāka projekta mapes izveide. Stājas spēkā programmām, kas tiek palaistas pēc instalēšanas. Pēc programmas atinstalēšanas šis iestatījums paliek ieslēgts, jo citas programmas var būt atkarīgas no tā. | `/MERGETASKS="!longpaths"` |
| **Izslēdziet Chloros a aizmugurējo moduli no Microsoft Defender skenēšanas** (daudz ātrāka kameras attēlu uzņemšana; ieteicams) | Pievieno Microsoft Defender procesa izņēmumu `chloros-backend.exe` instalācijas mapē, lai reāllaika aizsardzība neskenētu katru failu, ko aizmugurējais modulis raksta, neatkarīgi no tā, kur tas to raksta. Pie augstas uzņemšanas ātruma skenēšana aizņem apmēram 30 % no uzņemšanas ātruma (77,7 pret 52,7 uzņēmumiem sekundē, veicot nepārtrauktu uzņemšanu ar 9 kamerām). Nekāda cita programma netiek izslēgta, un izslēgums tiek noņemts, atinstalējot programmu. Ja Defender nav instalēts vai ir bloķēts ar politiku, šis solis klusi neizdodas un instalēšana turpinās. | `/MERGETASKS="!defenderexclusion"` |

Klusa instalēšana (`/SILENT` vai `/VERYSILENT`) piemēro abas opcijas, ja vien jūs neatsakāties no tām, izmantojot parādīto `/MERGETASKS` vērtību.

{% hint style="success" %}
Instalētājs vienreiz pievieno CLI mapi (`C:\Program Files\Chloros\cli\`) jūsu sistēmai `PATH` — atkārtota instalēšana vai atjaunināšana vairs nepievieno dublikātu ierakstu un noņem dublikātus, ko pievienojuši iepriekšējie instalētāji. Pēc instalēšanas atveriet jaunu termināli, lai izmantotu `chloros-cli`. Instalētājs katrā tīkla profilā pievieno arī divus Windows ugunsmūra noteikumus backendam: **Chloros Backend (UDP-In)**— kameru straumēm, PTP, mDNS un DAQ-E datu straumei, un**Chloros Backend (TCP-In)** — ierobežots uz vietējo apakštīklu, DAQ-E programmaparatūras atjaunināšanai, kurā sensors atkal savienojas ar šo datoru, lai savāktu attēlu. Abi noteikumi tiek noņemti, atinstalējot programmu.
{% endhint %}

#### Linux amd64 (.deb pakete — CLI + Backend)

* **Faila tips**: .deb (Debian/Ubuntu pakete)
* **Arhitektūra**: x86\_64 (amd64)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

#### Linux arm64 — NVIDIA Jetson (.deb pakete — CLI + Backend)

* **Faila tips**: .deb (JetPack 6)
* **Arhitektūra**: aarch64 (arm64)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

Izmantojiet faktiski lejupielādēto faila nosaukumu un saglabājiet `./`, lai `apt` instalētu šo failu, nevis meklētu paketi pēc nosaukuma. Instalējiet ar `apt install`, nevis ar `dpkg -i`: apt instalē arī Python SDK prasības, kuras pakete ieteic, nevis prasa, un `dpkg -i` tās izlaiž. Sākot ar versiju 1.3.0, aizmugurējā sistēma un CLI tiek instalēti neiesaiņoti zem `/usr/lib/chloros/`, tādējādi tie sākas, iepriekš neizpakojoties.

Jetson pakete izmanto JetPack 6 CUDA versiju PyTorch, tādējādi apstrāde, ieskaitot iekļauto Texture Aware debayer, notiek uz Jetson GPU. Tā izmanto CUDA un cuDNN bibliotēkas, kuras instalē JetPack 6 (`sudo apt install nvidia-jetpack` attēlā, kurā tās nav).

Sīkākas uzstādīšanas instrukcijas skatiet sadaļā [Linux instalācija](linux/linux-installation.md), bet Jetson-specifiskus norādījumus — [NVIDIA Jetson rokasgrāmatā](linux/nvidia-jetson-guide.md).

#### Python SDK (visas platformas)

Katrs instalētājs iekļauj atbilstošo `chloros_sdk` wheel, tādējādi SDK versija vienmēr atbilst instalētajai GUI/CLI/backend versijai. Windows vidē instalētājs to automātiski instalē jūsu sistēmas Python vidē. Linux vidē `.deb` instalē SDK sistēmai `python3` (SDK vidē), tādējādi `import chloros_sdk` darbojas uzreiz pēc instalēšanas, bez pip un bez tīkla. Tikai virtualenv, kas neredz sistēmas pakotnes, prasa, lai tajā tiktu instalēts komplektā iekļautais wheel:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Datoriem, kuros darbojas tikai pip (nav instalēts Chloros pakotne; šādā gadījumā SDK vada aizmuguri citā datorā), SDK ir pieejams arī PyPI:

```bash
pip install chloros-sdk
```

Dokumentāciju skatiet sadaļā [API: Python SDK](api-python-sdk.md) un [SDK atsauces materiālos](reference/sdk-reference.md).

{% hint style="info" %}
**Linux lietotāji**: `.deb` pakete instalē CLI, backend un Python SDK. Linux versijai nav grafiskās lietotāja saskarnes (GUI) — visa mijiedarbība notiek, izmantojot CLI vai SDK.
{% endhint %}

***

## Papildu resursi

### Python SDK


Izstrādātājiem un automatizācijas darba plūsmām Chloros Python SDK ir iekļauts katrā instalētājā: Windows instalētājs to instalē jūsu Python vidē, bet Linux `.deb` to instalē sistēmai `python3`. Datorā, kurā nav Chloros paketes (tādā, kas vada aizmuguri citā datorā), instalējiet to no PyPI:

```bash
pip install chloros-sdk
```

**Dokumentācija**: [API: Python SDK](api-python-sdk.md)

**Prasības**: vadāms Chloros backend (to instalē Windows instalētājs vai Linux `.deb` pakete) un „Chloros+” pieteikšanās dati maksas līmenī

***

## Kas ir iekļauts

### Windows instalētājs

* ✅ **Chloros GUI** — pilnfunkciju grafiskā saskarne
* ✅ **Chloros CLI** — komandrindas saskarne (nepieciešama Chloros+ licence)
* ✅ **Chloros backend** — apstrādes dzinējs
* ✅ **Python SDK** — tiek instalēts jūsu Python vidē, ja tāda ir pieejama (nepieciešama Chloros+ licence)
* ✅ **Kameru profili** — iepriekš konfigurēti MAPIR kameru veidnes

### Linux.deb pakete

* ✅ **Chloros CLI** — komandrindas saskarne (nepieciešama Chloros+ licence)
* ✅ **Chloros Backend** — apstrādes dzinējs
* ✅ **Python SDK** — instalēts sistēmai `python3` (nepieciešama Chloros+ licence)
* ✅ **Kameru profili** — iepriekš konfigurēti MAPIR kameru šabloni
* ❌ Nav grafiskās lietotāja saskarnes — Linux darbojas tikai bez grafiskās saskarnes, izmantojot CLI/SDK


### Python SDK (PyPI, datoriem bez Chloros pakotnes)

* ✅ **Chloros SDK** — Python API (nepieciešama Chloros+ licence)

***

## Pāreja uz Chloros+

Atbloķējiet papildu funkcijas ar Chloros+ abonementu:

* 🚀 **Daudzpavedienu apstrāde** — attēlu paralēla apstrāde
* ⚡ **GPU (CUDA) paātrinājums** - Izmantojiet NVIDIA GPU jaudu
* 💻 **Piekļuve CLI** — automatizējiet ar komandrindas rīkiem
* 🐍 **Python SDK** — programmatiska piekļuve API

* 📱 **Vairākas ierīces** — izmantojiet 2–10 un vairāk ierīcēs (atkarībā no plāna)
* **🐻 Uzlabota tekstūru ņemšana vērā debayer metode** — augstas kvalitātes, malu ņemšana vērā debayer metode, kas apvienota ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayerēšanas radītos trokšņus.
* 🧮 **Pielāgotas formulas** — izveidojiet pielāgotus multispektrālos indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Apskatiet „Chloros+“ plānus un cenas</a></p>***

## Palīdzība instalēšanā

### Problēmu novēršana

**Instalēšana neizdodas, parādot šādu kļūdas ziņojumu:**

* Pārliecinieties, ka jums ir administratora tiesības
* Uz laiku atspējojiet antivīrusu programmatūru
* Pārbaudiet, vai jūsu sistēma atbilst minimālajām sistēmas prasībām

**Lietojumprogramma nepalaižas (Windows):**

* Pārbaudiet, vai ir instalēta Windows 10/11 (64 bitu versija)
* Atjauniniet grafikas draiverus
* Pārbaudiet Windows notikumu skatītāju, lai iegūtu informāciju par kļūdu
* Sazinieties ar atbalsta dienestu, pievienojot kļūdu žurnālus

**CLI nepalaižas (Linux):**

* Pārbaudiet, vai `.deb` pakete ir pareizi instalēta: `dpkg -l | grep chloros`
* Pārbaudiet atļaujas: `sudo chmod +x /usr/bin/chloros-cli`
* Palaižiet diagnostiku: `chloros-cli selftest`
* Pārbaudiet, vai nav trūkstošas bibliotēkas: `ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"` (sākot ar versiju 1.3.0, `/usr/lib/chloros/chloros-backend` ir neliels palaišanas skripts; pati programma atrodas `/usr/lib/chloros/backend/`)

**Problēmas ar licences aktivizēšanu:**

* Pārliecinieties, ka interneta savienojums darbojas
* Pārbaudiet autentifikācijas datus [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Pārbaudiet, vai ugunsmūris neblokē Chloros

* Sīkākas instrukcijas skatiet sadaļā [Chloros+ Pieslēgšanās](chloros+-login.md)

### Atbalsta saņemšana

Vajadzīga palīdzība ar instalēšanu vai konfigurēšanu?

* 📧 **E-pasts**: info@mapir.camera
* 🌐 **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentācija**: [Sākums](./)
* ❓ **FAQ**: [Bieži uzdotie jautājumi](faq.md)***

## Programmatūras atjauninājumi

Chloros pārbauda, vai ir pieejami atjauninājumi, paziņo, kad ir pieejama jauna versija, un sniedz saiti uz šo lejupielādes lapu — atjauninājumu veicat, palaistot jauno parakstīto instalētāju. Jūsu iestatījumi, projekti un pieteikšanās dati paliek nemainīgi pēc atjaunināšanas. Linux un Jetson vidē `chloros-cli update` pārbauda, vai ir pieejama jaunāka versija, un piedāvā lejupielādēt un instalēt atbilstošo `.deb` (šī komanda darbojas tikai Linux vidē).

***

## Izmaiņu žurnāls**Versija 1.3.0 (jaunākā, 2026. gada 11. novembris)**— pilnu sarakstu skatiet sadaļā**Jaunumi programmā Chloros

1.3.0** lappusē [Sākums](./).**Versija 1.2.0**— skatiet sadaļu**Jaunumi programmā Chloros 1.2.0** lapā [Sākums](./).

<details>

<summary>Versija 1.0.5</summary>

**Izlaišanas datums: 2026. gada 10. februāris**

**Jaunas funkcijas*** **Tekstūru ņemot vērā debayer metode \[Tikai Chloros+] —** Tekstūru ņemot vērā metode izmanto augstas kvalitātes, malu ņemot vērā debayer metodi, kas apvienota ar AI/ML trokšņu noņemšanas modeli, kas novērš gandrīz visus debayerēšanas radītos trokšņus.
* **Atbalsts T4P kalibrēšanas mērķiem*** **Ātrāka „Chloros+“ apstrāde ar GPU, labāka atmiņas pārvaldība**

**Kļūdu labojumi*** Pilnīgi jauna lietotāja saskarne (GUI), tagad būtu jādarbojas visos Windows datoros.

</details>

<details>

<summary>Versija 1.0.4</summary>

**Izlaišanas datums: 2026. gada 5. janvāris**

**Jaunas funkcijas*** **Attēla/metadatu pārslēgšana**: failu pārlūkā pievienota pārslēgšanas funkcija, lai izvēlētā attēla metadatus skatītu tabulā, nevis attēlu režģī
* **Attēlu režģa tālummaiņas sliders**: jauns lietotāja saskarnes sliders, lai pielāgotu sīktēlu izmēru (atbalsta arī CTRL + peles ratiņu)
* **Attēlu režģa eksportēšanas pogas**: Pogas augšējā rindā, lai pārslēgtu sīktēlus no JPG uz apstrādātiem eksportiem (Mērķi, Reflektance, Indekss, LUT)
* **Kartes cilne**: Jauna interaktīva 2D karte, kas parāda attēlu GPS atrašanās vietu marķierus
  * Atbalsta „Google Maps“ un ESRI kartes flīzes (automātiski izvēlas labāko flīžu pakalpojumu, pamatojoties uz pieejamo tālummaiņas līmeni)
  * Sīktēlu priekšskatījums, uzvedot peles kursoru uz kartes marķieriem

**Kļūdu labojumi*** Uzlabota Chloros instalēšanas atbalsts datoros, kuros nav angļu valodas

</details>

<details>

<summary>Versija 1.0.3</summary>

**Izlaišanas datums: 2025. gada 20. decembris**

**Jaunas funkcijas*** Sākotnējā versija

**Uzlabojumi*** Sākotnējā versija

**Kļūdu labojumi*** Sākotnējā versija

**Zināmās problēmas*** Sākotnējā versija

</details>

***

## Licences līgums**Autortiesību aizsargāta programmatūra** — Autortiesības (c) 2026 MAPIR Inc.

Aizliegta neatļauta izmantošana, izplatīšana vai modificēšana.

**Bezmaksas versija**: Pieejama personiskai un komerciālai lietošanai ar funkciju ierobežojumiem**Chloros+**: Abonementa licence papildu funkcijām un komerciālai izmantošanai
