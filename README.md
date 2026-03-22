---
metaLinks: {}
---

# Sākums

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>Chloros ir programmatūras lietojumprogramma no [MAPIR](https://www.mapir.camera) attēlu un citu sensoru datu apstrādei.

***{% hint style="success" %}**Jaunumi Chloros 1.1.0**: Linux atbalsts (amd64 un arm64), NVIDIA Jetson malu datu apstrāde, dinamiskā aprēķinu pielāgošana, 4-pavedienu apstrādes cauruļvads, jaunas CLI komandas un opcijas. Pilnu izmaiņu sarakstu skatiet [Lejupielāde](download.md).
{% endhint %}

Chloros ir pieejams 3 lietošanas režīmos:

## Chloros: Darbvirsmas GUI lietojumprogramma

Atsevišķs logs ar visām funkcijām. _Tikai Windows._

## [Chloros CLI: Komandrindas interfeiss](CLI.md)

Komandrindas pakotņu apstrāde. Ideāli piemērota automatizācijai, skriptu izveidei un darbībai bez grafiskās saskarnes. Pieejams **Windows, Linux amd64 un Linux arm64 (NVIDIA Jetson)**. _Lai piekļūtu CLI, nepieciešama Chloros+ licence._

## [Chloros API: Python SDK](api-python-sdk.md)

Programmatiska Python saskarne automatizācijai un pielāgotām darba plūsmām. Ideāli piemērota pētniecības procesiem, integrācijai ar esošajām Python lietojumprogrammām un pielāgotu rīku izstrādei. Pieejama **visās platformās**, izmantojot `pip install chloros-sdk`. _Lai piekļūtu API, ir nepieciešama Chloros+ licence._***

## Atbalstītās platformas

| Platforma | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11** | Jā | Jā | Jā |
| **Linux amd64 (x86_64)** | Nē | Jā | Jā |
| **Linux arm64 (NVIDIA Jetson)** | Nē | Jā | Jā |

Linux instalēšanas instrukcijas skatiet sadaļā [Linux &amp; Edge Computing](linux/linux-overview.md).

***

## Chloros+

Lai gan Chloros ir bezmaksas lietošanai lielākajā daļā uzdevumu, iespējams, jūs vēlēsieties vairāk. Tādos gadījumos jums noderēs maksas licence Chloros+. Ar Chloros+ licenci varat atbloķēt jaunas funkcijas, piemēram:

* **Daudzpavedienu apstrāde**: ievērojami paātriniet attēlu apstrādi lielākiem projektiem, vienlaikus apstrādājot attēlus caur apstrādes ķēdi.
* **GPU (CUDA) paātrinājums**: izmantojiet mūsdienu lielākas GPU atmiņas iespējas, lai vēl vairāk paātrinātu attēlu apstrādes cauruļvadu. Labāko rezultātu sasniegšanai mēs iesakām 4 GB vai vairāk VRAM.
* **Chloros+**[**CLI**](CLI.md)**Piekļuve**: palaidiet Chloros+ no komandrindas, lai automatizētu un integrētu savā programmā.
* **Chloros+**[**API**](api-python-sdk.md)**Piekļuve:** palaidiet Chloros+ no Python programmatiskai kontrolei, nodrošinot vienotu integrāciju ar jūsu pētniecības procesiem, datu analīzes darbplūsmām un pielāgotajām lietojumprogrammām.
* **Lietošana vairākās ierīcēs**: katra Chloros+ licence ļauj reģistrēt 2 vai vairāk ierīces. Izmantojiet savu MAPIR Cloud kontu, lai pārvaldītu reģistrētās ierīces. Paplašiniet atbalstu vairākām ierīcēm, uzlabojot savu Chloros+ licenci.
* **Uzlabota tekstūru atpazīstoša debayer metode:** augstas kvalitātes malu atpazīstošs debayer, kas apvienots ar AI/ML trokšņu noņemšanas modeli, kas noņem gandrīz visus debayeringa trokšņus. 
* **Pielāgotas multispektrālo indeksu formulas:** ievadiet pielāgotus multispektrālos indeksus Chloros rastra kalkulatoros gan apstrādei, gan attēlu skatīšanas sandbox.
* **Linux un malu aprēķini:** palaidiet Chloros uz Linux x86\_64 un ARM64 platformām, tostarp NVIDIA Jetson, lauka un malu apstrādei. Skatīt [Linux pārskatu](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Cenas un reģistrācija</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
