---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---
# Lejupielāde

Lejupielādējiet jaunāko Chloros versiju Windows, lai sāktu darbu ar multispektrālo attēlu apstrādi.

### Sistēmas prasības

| Prasība          | Minimālā                         | Ieteicamā                     |
| -------------------- | ------------------------------- | ------------------------------- |
| **Operētājsistēma** | Windows 10 (64 bitu)             | Windows 11 (64 bitu)             |
| **Procesors**        | Intel Core i5 vai līdzvērtīgs     | Intel Core i7 vai labāks         |
| **Atmiņa (RAM)**     | 8 GB                             | 16 GB vai vairāk                    |
| **Grafikas karte**    | DirectX 11 saderīga           | NVIDIA GPU ar 4 GB+ VRAM       |
| **Atmiņas vieta**          | 2 GB brīvas vietas                  | SSD ar 10 GB+ brīvas vietas       |
| **Ekrāns**          | 1920x1080                       | 2560x1440 vai augstāka izšķirtspēja             |
| **Internets**         | Nepieciešams licences aktivizēšanai | Nepieciešams licences aktivizēšanai |

{% hint style=&quot;info&quot; %}
**GPU paātrinājums**: Chloros+ lietotāji ar NVIDIA GPU (4 GB+ VRAM) var izmantot CUDA paātrinājumu, lai ievērojami paātrinātu apstrādi.
{% endhint %}

***

## Lejupielādēt Chloros

### <a href="https://drive.google.com/file/d/1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4/view?usp=drive_link" class="button primary">Lejupielādēt Chloros šeit</a>

### Jaunākā stabilā versija

**Chloros instalētājs Windows**

* **Versija**: 1.0.3
* **Izlaides datums**: 2025. gada decembris?
* **Faila izmērs**: 1,6 GB
* **Faila tips**: .exe (Windows instalētājs)

#### **Instalēšanas soļi:**

1. Lejupielādējiet failu `CHLOROS INSTALLER - CURRENT VERSION.exe`.
2. Divreiz noklikšķiniet uz instalētāja, lai sāktu instalēšanu.
3. Sekojiet instalēšanas vedņa norādījumiem.
4. Izvēlieties instalēšanas direktoriju (noklusējums: `C:\Program Files\Chloros\`).
5. Pabeidziet instalēšanu un palaidiet Chloros.
6. Piesakieties ar savu MAPIR Cloud Chloros+ kontu (vai turpiniet ar bezmaksas versiju)

{% hint style=&quot;success&quot; %}
Instalētājs automātiski pievieno `chloros-cli` jūsu sistēmas PATH, lai nodrošinātu piekļuvi komandrindai.
{% endhint %}

***

## Papildu resursi

### Python SDK

Izstrādātājiem un automatizācijas darba plūsmām instalējiet Chloros Python SDK:

```bash
pip install chloros-sdk
```

**Dokumentācija**: [API: Python SDK](api-python-sdk.md)

**Prasības**: Chloros Desktop jābūt instalētam, nepieciešama Chloros+ licence.

***

## Kas ir iekļauts

Chloros instalācijā ir iekļauts:

* ✅ **Chloros Desktop GUI** - pilnfunkciju grafiskais interfeiss
* ✅ **Chloros (pārlūks)** - tīmekļa interfeiss sistēmām ar zemāku specifikāciju
* ✅ **Chloros CLI** – komandrindas saskarne (nepieciešama Chloros+ licence)
* ✅ **Backend Engine** – attēlu apstrādes cauruļvads
* ✅ **Kameru profili** - Iepriekš konfigurēti MAPIR kameru veidnes

***

## Pāreja uz Chloros+

Atbloķējiet papildu funkcijas ar Chloros+ abonementu:

* 🚀 **Daudzpavedienu apstrāde** - attēlu paralēla apstrāde
* ⚡ **GPU (CUDA) paātrinājums** - izmantojiet NVIDIA GPU jaudu
* 💻 **CLI piekļuve** - automatizējiet ar komandrindas rīkiem
* 🐍 **Python SDK** - Programmatiska API piekļuve
* 📱 **Vairākas ierīces** - Lietošana 2-10+ ierīcēs (atkarībā no plāna)
* 🧮 **Pielāgotas formulas** - Izveidojiet pielāgotus multispektrālos indeksus

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Skatīt Chloros+ plānus un cenas</a></p>***

## Palīdzība instalēšanā

### Problēmu novēršana

**Instalēšana neizdodas, parādoties kļūdas ziņojumam:**

* Pārliecinieties, ka jums ir administratora tiesības
* Uz laiku atspējojiet antivīrusu programmatūru
* Pārbaudiet, vai atbilstat minimālajām sistēmas prasībām

**Lietojumprogramma nepalaižas:**

* Izmēģiniet Chloros (pārlūka) versiju
* Pārbaudiet, vai ir instalēta Windows 10/11 (64 bitu) versija
* Atjauniniet grafikas draiverus
* Pārbaudiet Windows notikumu skatītāju, lai uzzinātu kļūdas detaļas
* Sazinieties ar atbalsta dienestu, nosūtot kļūdu žurnālus

**Licences aktivizēšanas problēmas:**

* Pārbaudiet, vai interneta savienojums ir aktīvs
* Pārbaudiet autentifikācijas datus [https://cloud.mapir.camera](https://cloud.mapir.camera)
* Pārbaudiet, vai ugunsmūris neblokē Chloros
* Sīkākas instrukcijas skatiet [Chloros+ Pieslēgšanās](chloros+-login.md)

### Atbalsta saņemšana

Nepieciešama palīdzība ar instalēšanu vai konfigurēšanu?

* 📧 **E-pasts**: info@mapir.camera
* 🌐 **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Dokumentācija**: [Sākums](./)
* ❓ **FAQ**: [Bieži uzdotie jautājumi](faq.md)

***

## Izmaiņu žurnāls

<details>

<summary>Versija 1.0.3</summary>

### **Izlaišanas datums**: 2025. gada decembris?

#### Jaunas funkcijas

* Sākotnējā versija

#### Uzlabojumi

* Sākotnējā versija

#### Kļūdu labojumi

* Sākotnējā versija

#### Zināmas problēmas

* Sākotnējā versija

</details>***

## Licences līgums

**Proprietārā programmatūra** - Autortiesības (c) 2025 MAPIR Inc.

Neatļauta izmantošana, izplatīšana vai modificēšana ir aizliegta.

**Bezmaksas versija**: pieejama personiskai un komerciālai lietošanai ar funkciju ierobežojumiem.

**Chloros+**: abonementa licence papildu funkcijām un komerciālai izmantošanai.

<figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption></figcaption></figure>
