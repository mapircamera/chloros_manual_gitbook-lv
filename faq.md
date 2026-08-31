---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# Bieži uzdotie jautājumi

<details>

<summary>Vai ar Chloros varu apstrādāt attēlus no kamerām, kas nav MAPIR zīmola?</summary>

Nē, Chloros atbalsta tikai MAPIR kameru attēlu apstrādi — Survey3 un LATTICE sērijas. Lai iegūtu vairāk informācijas, lūdzu, skatiet [atbalstīto kameru modeļu](supported-cameras.md) sarakstu. Mēs piedāvājam citu kameru attēlu apstrādi platformā MAPIR Cloud; pilnu sarakstu skatiet [šeit](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Vai Chloros atbalsta LATTICE kameras?</summary>

Jā. Chloros 1.2.0 pilnībā atbalsta LATTICE M3C un M3M kameru moduļus: **vadība reāllaikā**— atklāšana, savienošana, priekšskatīšana un attēlu uzņemšana no lietotāja saskarnes cilnes „Kameras”, `chloros-cli lattice` vai Python SDK, ieskaitot sinhronizētus daudzkameru masīvus ar PTP laika sinhronizāciju — kā arī**pilnīgu uzņemto attēlu radiometrisko apstrādi** (neapstrādāti dati → debayering → starojums → atstarošanās → indekss). Skatīt [Atbalstītās kameras](supported-cameras.md) un [LATTICE rokasgrāmatu](lattice/README.md).

</details>

<details>

<summary>Vai es varu kalibrēt savus attēlus atstarojuma mērījumiem bez kalibrēšanas mērķa?</summary>

**Survey3:** Nē. Ja, uzņemot attēlus bez kalibrēšanas mērķa, netiek uzņemts arī kalibrēšanas mērķa attēls, jums nebūs iespējams saistīt attēla pikseļu vērtības ar zināmu atstarojuma procentuālo daļu. Ja jūs neiekļausiet arī MAPIR gaismas sensora reģistrācijas datus, tad netiks izmērīts apkārtējās gaismas spektrs, un atstarošanas rezultāti nebūs precīzi.**LATTICE:** Jā. Atstarošanas koeficientu var saistīt ar lejupvērsto starojuma intensitāti, ko mēra DAQ gaismas sensors, nevis panelis (ρ = π·L/E). Ja kadrā *ir* klāt mērķis, kas izturējis kvalitātes kontroli, tas pēc noklusējuma kļūst par absolūto atsauci (`--reflectance-source auto`). Viens izņēmums: „F988 atstarojums tiek kalibrēts, izmantojot atstarojuma paneli kadrā: šis diapazons atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tādēļ Chloros izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.” Skatīt [Kalibrēšanas mērķus](calibration-targets.md).

</details>

<details>

<summary>Vai man ir nepieciešams DAQ gaismas sensors?</summary>

Ne, ja runa ir par starojuma intensitāti: LATTICE starojuma intensitātes eksporta dati tiek iegūti, pamatojoties uz katras kameras rūpnīcas radiometrisko kalibrēšanu, un tiem nav nepieciešams ne DAQ sensors, ne mērķis. Lai iegūtu **atstarošanas koeficientu**, jums ir nepieciešams vides gaismas etalons — vai nu DAQ gaismas sensora lejupvērstā gaismas plūsmas mērījums, vai arī kadrā esošs kalibrēšanas mērķis. DAQ sensors ļauj iegūt kalibrētu atstarojumu,**neievietojot ainas kadrā nekādus paneļus**. Ierakstītie `.daq` faili tiek automātiski saskaņoti ar jūsu attēliem pēc laika zīmoga. Skatīt [Kalibrēšanas mērķus](calibration-targets.md) un [CLI atsauci](reference/cli-reference.md).

</details>

<details>

<summary>Vai es varu izmantot Chloros kopā ar AI palīgu (Claude, ChatGPT utt.)?</summary>

Jā — šī rokasgrāmata un CLI/SDK ir izstrādātas tieši šim nolūkam:

* Pilns rokasgrāmatas rādītājs ir pieejams `https://mapir.gitbook.io/chloros/llms.txt`, lai AI palīgi varētu atrast katru lapu.
* Katras lapas neapstrādātais Markdown kods ir pieejams tās mazajiem burtiem rakstītajā lapā URL, kam pievienots `.md` (piemēram, `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI atsauces](reference/cli-reference.md) un [SDK atsauces](reference/sdk-reference.md) ir rakstītas LLM lietošanai: precīzi rādītāji, noklusējumi, iziešanas semantika un komandas, kuras var kopēt un ielīmēt.

Skatīt [AI palīgi](ai-assistants.md), lai uzzinātu, kā norādīt savam palīgam uz Chloros.

</details>

<details>

<summary>Kur nonāk manas apstrādātās izejas faili?</summary>

Rezultāti tiek saglabāti projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kameras mape** — `LATT-<sensor>-<lens>-F<filter>` attēliem no LATTICE, `<model>_<filter>` (piem., `Survey3N_RGN`) attēliem no Survey3
* **formāta mape** — `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32`
* **produktu mapes** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (vienmēr atrodas mapē `tiff32`), `<INDEX>_Index_Images/`**Eksportētajiem failiem tiek saglabāts avota faila nosaukums — mape identificē produktu, nevis faila nosaukuma paplašinājumu.**Izmantojot CLI, projekta mape tiek izveidota blakus ievades mapei, ja vien netiek norādīts `-o`. Ņemiet vērā, ka `chloros-cli process` izpilde, kas pieprasīja produktus, bet neko neierakstīja, izdrukā `Processing finished but wrote no image products.` un**beidzas ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt. Skatīt [Izvades attēlu formātus](output-image-formats.md) un [CLI atsauci](reference/cli-reference.md).

</details>

<details>

<summary>Vai es varu rediģēt savus attēlus pirms apstrādes programmā Chloros?</summary>

Nē. Chloros pieņem, ka ievades dati nav mainīti. Nemainiet failu nosaukumus.

</details>

<details>

<summary>Vai varu iestatīt savas MAPIR un Survey3 kameras uz automātisko ekspozīciju un apstrādāt attēlus programmā Chloros?</summary>

Nē. Survey3 attēlu datu kopām jābūt ar fiksētu/bloķētu ekspozīciju, tātad nedrīkst izmantot automātisko slēdža ātrumu vai automātisko ISO. Visiem viena un tā paša kameras modeļa attēliem jābūt ar identisku slēdža ātrumu un ISO (ekspozīciju).

LATTICE kamerām šāds ierobežojums nav: Chloros ekspozīciju regulē reāllaikā (Smart AE), un katrā uzņēmumā tiek reģistrēta faktiski izmantotā ekspozīcija un pastiprinājums, ko ņem vērā radiometriskā apstrādes ķēde.

</details>

<details>

<summary>Vai Chloros var apstrādāt vai analizēt ortomozaīkas attēlus?</summary>

Nē. Tiek atbalstīti tikai atsevišķi MAPIR kameras attēli, nevis savienoti attēli, piemēram, ortomozaīkas karte.

</details>

<details>

<summary>Kā varu paātrināt mērķa noteikšanas posmu programmā Chloros?</summary>

Failu pārlūka tabulā, iepriekš atlasot mērķa attēlus labajā ailē, Chloros tiks norādīts meklēt kalibrēšanas mērķus tikai šajos attēlos, kas ievērojami paātrina apstrādi.

</details>

<details>

<summary>Ja es augšupielādēšu savus attēlus uz <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">„MAPIR Cloud”,</a> vai man pirms augšupielādes ir jāveic apstrāde programmā „Chloros”?</summary>

Ja plānojat augšupielādēt mūsu tiešsaistes apstrādes platformā [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), neapstrādājiet attēlus pirms augšupielādes. Cloud veiks visu to pašu apstrādi un vēl vairāk.

</details>

<details>

<summary>Vai MAPIR kādreiz atbalstīs X funkciju? Es ļoti vēlētos, lai MAPIR piedāvātu X.</summary>

Mēs vienmēr esam ieinteresēti saņemt atsauksmes par mūsu produktiem. Ja atrodat kādu problēmu ar mūsu produktiem vai jums ir ieteikums, kā mēs varam tos uzlabot, lūdzu, [Sazinieties ar mums](https://www.mapir.camera/community/contact), lai dalītos ar savām domām. Lielākā daļa mūsu pētniecības un attīstības darba balstās uz to, ka mēs ieklausāmies mūsu klientu galvenajās vajadzībās.

</details>

<details>

<summary>Vai Chloros ir pieejams Linux?</summary>

Jā! Chloros 1.2.0 atbalsta Linux amd64 (x86_64) un arm64 (NVIDIA Jetson JetPack 6) ar `.deb` pakotnēm. CLI un Python SDK tiek pilnībā atbalstīti Linux, ieskaitot reāllaika LATTICE kameras un DAQ sensoru vadību. Linux nav grafiskās lietotāja saskarnes — visa mijiedarbība notiek, izmantojot [CLI](CLI.md) vai [Python SDK](api-python-sdk.md). Sīkāku informāciju skatiet [Linux pārskatā](linux/linux-overview.md).

</details>

<details>

<summary>Vai varu palaist Chloros uz NVIDIA Jetson?</summary>

Jā! Chloros atbalsta NVIDIA Jetson platformas, tostarp Jetson Nano, Orin Nano, Orin NX un AGX Orin, kurās darbojas JetPack 6. Chloros automātiski atpazīst jūsu Jetson modeli un optimizē tā apstrādes stratēģiju. Skatīt [NVIDIA Jetson rokasgrāmatu](linux/nvidia-jetson-guide.md), lai uzzinātu par uzstādīšanas un ieviešanas norādījumiem.

</details>

<details>

<summary>Vai Chloros automātiski optimizējas atbilstoši manai aparatūrai?</summary>

Jā! Chloros ietver [dinamisko aprēķinu pielāgošanu](processing-architecture/dynamic-compute-adaptation.md), kas automātiski atpazīst jūsu CPU, GPU, RAM un (uz Jetson) termiskos sensorus. Pēc tam tā izvēlas optimālo apstrādes stratēģiju — sākot no `GPU_PARALLEL` sistēmās ar lielu atmiņas apjomu līdz `GPU_SINGLE` ierīcēs ar ierobežotiem resursiem un `CPU_PARALLEL` sistēmās bez NVIDIA GPU. Nav nepieciešama manuāla konfigurācija.

</details>

<details>

<summary>Kas ir 4-diegu apstrādes cauruļvads?</summary>

Chloros izmanto 4-diegu cauruļveida arhitektūru Chloros+ lietotājiem: 1. pavediens (detekcija) ielādē attēlus un atpazīst kalibrēšanas mērķus, 2. pavediens (kalibrēšana) aprēķina atstarošanas kalibrēšanu, 3. pavediens (apstrāde) veic ar GPU paātrinātu debayeringu un indeksa aprēķinu, un 4. pavediens (eksportēšana) raksta izejas failus. Lai nodrošinātu maksimālu caurlaidspēju, vienlaikus vairākos pavedienos var atrasties vairāki attēli. Sīkāku informāciju skatiet sadaļā [Apstrādes cauruļvads](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kā veikt Chloros instalācijas diagnostiku?</summary>

Izmantojiet komandu `selftest`, lai veiktu 7 posmu pārbaudi: versija, portu pieejamība, aizmugurējās sistēmas palaišana, API savienojamība (`/api/test`), sistēmas informācija (`/api/system-info` — GPU/CUDA/PyTorch), trokšņu noņemšanas modeļa klātbūtne un CUDA + trokšņu noņemšanas gatavība:

```bash
chloros-cli selftest
```

Tas ir īpaši noderīgi Linux/Jetson sistēmās, lai pārbaudītu GPU un CUDA konfigurāciju.

</details>
