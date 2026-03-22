---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# Bieži uzdotie jautājumi

<details>

<summary>Vai ar Chloros varu apstrādāt attēlus no kamerām, kas nav MAPIR zīmola?</summary>

Nē, Chloros atbalsta tikai MAPIR kameru attēlu apstrādi. Lūdzu, skatiet [atbalstīto kameru modeļu](supported-cameras.md) sarakstu, lai iegūtu vairāk informācijas. Mēs piedāvājam citu kameru attēlu apstrādi MAPIR Cloud, pilnu sarakstu skatiet [šeit](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Vai varu kalibrēt savus attēlus atstarojumam bez kalibrēšanas mērķa?</summary>

Nē. Ja nav attēla ar kalibrēšanas mērķi, kas uzņemts apmēram tajā pašā laikā, kad uzņemti attēli bez mērķa, jūs nevarēsiet attēla pikseļu vērtības saistīt ar zināmu atstarojuma procentu. Ja jūs neiekļausiet arī MAPIR gaismas sensora protokolu, tad netiks izmērīts vides gaismas spektrs, un atstarojuma rezultāti nebūs precīzi.

</details>

<details>

<summary>Vai es varu rediģēt savus attēlus pirms apstrādes Chloros?</summary>

Nē. Chloros pieņem, ka ievades dati nav mainīti. Nemainiet failu nosaukumus.

</details>

<details>

<summary>Vai varu iestatīt savas MAPIR un Survey3 kameras uz automātisko ekspozīciju un apstrādāt attēlus programmā Chloros?</summary>

Nē. Survey3 attēlu datu kopām jābūt ar fiksētu/bloķētu ekspozīciju, tātad bez automātiskā slēdža ātruma vai automātiskā ISO. Visiem attēliem, kas uzņemti ar vienu un to pašu kameras modeli, jābūt ar identisku slēdža ātrumu un ISO (ekspozīciju).

</details>

<details>

<summary>Vai Chloros var apstrādāt vai analizēt ortomosaīkas attēlus?</summary>

Nē. Tiek atbalstīti tikai atsevišķi MAPIR kameras attēli, nevis savienoti attēli, piemēram, ortomosaikas karte.

</details>

<details>

<summary>Kā es varu paātrināt Chloros mērķa noteikšanas posmu?</summary>

Failu pārlūka tabulā, iepriekš atlasot mērķa attēlus labajā kolonnā, Chloros tiks norādīts meklēt kalibrēšanas mērķus tikai šajos attēlos, kas ievērojami paātrinās apstrādi.

</details>

<details>

<summary>Ja es augšupielādēšu savus attēlus uz <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud,</a> vai man pirms augšupielādes ir jāveic apstrāde Chloros?</summary>

Ja plānojat augšupielādēt mūsu tiešsaistes apstrādes platformā [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), neizmainiet attēlus pirms augšupielādes. Cloud veiks visu to pašu apstrādi un vēl vairāk.

</details>

<details>

<summary>Vai MAPIR kādreiz atbalstīs X funkciju? Es ļoti vēlētos, lai MAPIR piedāvātu X.</summary>

Mēs vienmēr esam ieinteresēti saņemt atsauksmes par mūsu produktiem. Ja atrodat kādu problēmu ar mūsu produktiem vai jums ir ieteikums, kā mēs varam uzlabot mūsu produktus, lūdzu, [Sazinieties ar mums](https://www.mapir.camera/community/contact), lai dalītos ar savām domām. Lielākā daļa mūsu pētniecības un attīstības darba tiek virzīta, ieklausoties mūsu klientu galvenajās vajadzībās.

</details>

<details>

<summary>Vai Chloros ir pieejams Linux?</summary>

Jā! Chloros 1.1.0 atbalsta Linux amd64 (x86\_64) un arm64 (NVIDIA Jetson JetPack 6) ar `.deb` pakotnēm. CLI un Python SDK ir pilnībā atbalstīti Linux. Linux nav grafiskās lietotāja saskarnes — visa mijiedarbība notiek caur [CLI](CLI.md) vai [Python SDK](api-python-sdk.md). Sīkāku informāciju skatiet [Linux pārskatā](linux/linux-overview.md).

</details>

<details>

<summary>Vai varu palaist Chloros uz NVIDIA Jetson?</summary>

Jā! Chloros 1.1.0 atbalsta NVIDIA Jetson platformas, tostarp Jetson Nano, Orin Nano, Orin NX un AGX Orin, kurās darbojas JetPack 6. Chloros automātiski atpazīst jūsu Jetson modeli un optimizē tā apstrādes stratēģiju. Skatīt [NVIDIA Jetson rokasgrāmatu](linux/nvidia-jetson-guide.md), lai iegūtu uzstādīšanas un ieviešanas instrukcijas.

</details>

<details>

<summary>Vai Chloros automātiski optimizējas manai aparatūrai?</summary>

Jā! Chloros 1.1.0 ietver [dinamisko aprēķinu pielāgošanu](processing-architecture/dynamic-compute-adaptation.md), kas automātiski atpazīst jūsu CPU, GPU, RAM un (uz Jetson) termiskos sensorus. Tad tas izvēlas optimālo apstrādes stratēģiju — no `GPU_PARALLEL` sistēmās ar lielu atmiņu līdz `GPU_SINGLE` ierīcēs ar ierobežotām iespējām un `CPU_PARALLEL` sistēmās bez NVIDIA GPU. Nav nepieciešama manuāla konfigurācija.

</details>

<details>

<summary>Kas ir 4-pavedienu apstrādes cauruļvads?</summary>

Chloros 1.1.0 izmanto 4-pavedienu cauruļvadu arhitektūru Chloros+ lietotājiem: 1. pavediens (detektēšana) ielādē attēlus un atpazīst kalibrēšanas mērķus, 2. pavediens (kalibrēšana) aprēķina atstarošanas kalibrēšanu, 3. pavediens (apstrāde) veic GPU paātrinātu debayeringu un indeksa aprēķinu, un 4. pavediens (eksportēšana) raksta izvades failus. Lai nodrošinātu maksimālu caurlaidspēju, vienlaikus dažādos pavedienos var atrasties vairāki attēli. Sīkāku informāciju skatiet sadaļā [Apstrādes cauruļvads](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kā veikt diagnostiku manā Chloros instalācijā?</summary>

Izmantojiet komandu `selftest`, lai palaistu 7 sistēmas diagnostikas pārbaudes, tostarp versijas pārbaudi, portu pieejamību, backend palaišanu, API savienojamību, sistēmas informāciju, trokšņu samazināšanas modeļus un CUDA pieejamību:

```bash
chloros-cli selftest
```

Tas ir īpaši noderīgi Linux/Jetson sistēmās, lai pārbaudītu GPU un CUDA iestatījumus.

</details>
