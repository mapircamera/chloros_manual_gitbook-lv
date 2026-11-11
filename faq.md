---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# Bieži uzdotie jautājumi

<details>

<summary>Vai ar Chloros varu apstrādāt attēlus no kamerām, kas nav MAPIR zīmola?</summary>

Nē, Chloros atbalsta tikai MAPIR kameru attēlu apstrādi — Survey3 un „LATTICE” sērijas. Lai iegūtu vairāk informācijas, lūdzu, skatiet [atbalstīto kameru modeļu sarakstu](supported-cameras.md). Mēs piedāvājam citu kameru attēlu apstrādi MAPIR Cloud vidē, pilnu sarakstu skatiet [šeit](https://mapir.gitbook.io/mapir-cloud/supported-cameras).

</details>

<details>

<summary>Vai Chloros atbalsta „LATTICE” kameras?</summary>

Jā. Chloros 1.3.0 versija pilnībā atbalsta „LATTICE M3C“ un „M3M“ kameru moduļus: **reāllaika vadība**— atklāšana, savienošana, priekšskatīšana un attēlu uzņemšana no lietotāja saskarnes cilnes „Cameras” (`chloros-cli lattice`) vai Python SDK, ieskaitot sinhronizētus daudzkameru masīvus ar PTP laika sinhronizāciju — un**pilnīga radiometriskā apstrāde** uzņemtajiem attēliem (neapstrādāti dati → debayering → starojums → atstarojums → indekss). Skatīt [Atbalstītās kameras](supported-cameras.md) un [LATTICE rokasgrāmatu](lattice/README.md).

</details>

<details>

<summary>Vai es varu kalibrēt savus attēlus atstarojuma mērījumiem bez kalibrēšanas mērķa?</summary>

**Survey3:** Nē. Ja, uzņemot attēlus bez kalibrēšanas mērķa, netiek uzņemts arī kalibrēšanas mērķa attēls, jūs nevarēsiet attēla pikseļu vērtības saistīt ar zināmu atstarojuma procentu. Ja jūs neiekļausiet arī MAPIR gaismas sensora reģistrācijas datus, tad netiks izmērīts vides gaismas spektrs, un atstarojuma rezultāti nebūs precīzi.

**LATTICE:** Jā. Atstarošanas koeficientu var saistīt ar lejupvērsto starojuma intensitāti, ko mēra DAQ gaismas sensors, nevis panelis (ρ = π·L/E). Ja kadrā *ir* klāt mērķis, kas izturējis kvalitātes kontroli (QA), tas pēc noklusējuma kļūst par absolūto atsauci (`--reflectance-source auto`), izņemot gadījumus, kad tas iztur pārbaudi ar atzīmi — tad mērķis un DAQ ķēdes sacenšas pēc izmērītā rezultāta. Viens izņēmums: „F988 atstarojums tiek kalibrēts, izmantojot kadrā esošu atstarojuma paneli: josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tāpēc Chloros izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.” Skatīt [Kalibrēšanas mērķus](calibration-targets.md).

</details>

<details>

<summary>Vai man ir nepieciešams DAQ gaismas sensors?</summary>

Ne, ja runa ir par starojuma intensitāti: LATTICE starojuma intensitātes eksportētie dati tiek iegūti no katras kameras rūpnīcas radiometriskās kalibrācijas, un tiem nav nepieciešams ne DAQ sensors, ne mērķis. **Reflektances**gadījumā jums ir nepieciešams vides gaismas etalons — vai nu DAQ gaismas sensora lejupvērstais mērījums, vai arī kadrā esošs kalibrācijas mērķis. DAQ sensors ļauj iegūt kalibrētu atstarošanas koeficientu,**neievietojot ainas kadrā nekādus paneļus**. Ierakstītie `.daq` faili tiek automātiski saskaņoti ar jūsu attēliem pēc laika zīmoga. Skatīt [Kalibrēšanas mērķus](calibration-targets.md) un [CLI atsauci](reference/cli-reference.md).

</details>

<details>

<summary>Vai es varu izmantot Chloros kopā ar AI palīgu (Claude, ChatGPT utt.)?</summary>

Jā — šī rokasgrāmata un CLI/SDK ir izstrādāti tieši šim nolūkam:

* Pilnais rokasgrāmatas rādītājs ir pieejams vietnē `https://mapir.gitbook.io/chloros/llms.txt`, lai AI palīgi varētu atrast katru lapu.
* Katras lapas neapstrādātais Markdown kods ir pieejams tās URL-ā ar mazajiem burtiem, kam pievienots `.md` (piemēram, `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI atsauces materiāls](reference/cli-reference.md) un [SDK atsauces materiāls](reference/sdk-reference.md) ir izstrādāti lietošanai ar lielajiem valodas modeļiem (LLM): precīzi parametri, noklusējumi, iziešanas semantika un komandas, kuras var kopēt un ielīmēt.

Skatīt [AI palīgi](ai-assistants.md), lai uzzinātu, kā norādīt savam palīgam, ka jāizmanto Chloros.

</details>

<details>

<summary>Kur nonāk manas apstrādātās izejas faili?</summary>

Rezultāti tiek saglabāti projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kameras-mape** — `LATT-<sensor>-<lens>-F<filter>` programmai „LATTICE”, `<model>_<filter>` (piem., `Survey3N_RGN`) programmai Survey3
* **formāta mape** — `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32`
* **produktu mapes** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (vienmēr atrodas zem `tiff32`), `<INDEX>_Index_Images/`
* **`clamp_masks/`** — tikai LATTICE multispektrālie dati: maskas katram pikselim atrodas blakus produktu mapēm, skatīt zemāk**Eksportētajiem failiem tiek saglabāts avota faila nosaukums — produktu identificē mape, nevis faila nosaukuma paplašinājums.**Izmantojot CLI komandu, projekta mape tiek izveidota jūsu noklusējuma projekta mapē (`Documents/MAPIR/Chloros Projects`, ja vien neesat to mainījuši), ja vien neesat norādījuši `-o`; relatīvais `-o` tiek ņemts attiecībā pret mapi, no kuras izpildāt komandu. Ņemiet vērā, ka `chloros-cli process` izpilde, kas pieprasīja produktus, bet neko neierakstīja, izdrukā `Processing finished but wrote no image products.` un**iziet ar rezultātu, kas nav nulle**, tādējādi skripti to var atpazīt. Skatīt [Izvades attēlu formātus](output-image-formats.md) un [CLI atsauci](reference/cli-reference.md).

</details>

<details>

<summary>Kas ir „clamp_masks” mapes un *_clampmask.tif faili?</summary>

Jaunums versijā 1.3.0. Multispektrālās LATTICE M3C kameras starojuma un atstarošanas produktiem tiek veikta 3×3 joslu atdalīšana, un gadījumos, kad kādā joslā tiek iegūta negatīva vērtība, programma Chloros šo vērtību nogriež līdz nullei. Produktam, kurā ir vismaz viens nogriezts pikselis, tiek piešķirta vienjoslas `uint8` maska, kas atzīmē, kuri pikseļi un joslas tika nogriezti (bits 0 = produkta R kanāls, bits 1 = G, bits 2 = B). Maski tiek saglabātas `clamp_masks/` mapē **blakus** produkta mapēm — piemēram, `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif` — un produkta `Chloros:UnmixClampMask` XMP tagā ir norādīts maskas nosaukums (vai arī ir norādīts `none`).

Tās nav ainas attēli: „Chloros” paša importēšanas funkcija tās izlaiž, un tā kā tās nekad neatrodas produkta mapē, jūs varat nodot produkta mapi programmai „Pix4D” vai „Metashape” tādu, kāda tā ir. Skatīt [Clamp maskas](output-image-formats.md#clamp-masks).

</details>

<details>

<summary>Kāpēc Chloros parāda paziņojumu „Projekta mape ir pārāk dziļa šim datoram”?</summary>

Tas attiecas tikai uz Windows. Windows tiek piegādāts ar atspējotiem gariem ceļiem, un tādējādi neviens faila ceļš nedrīkst būt garāks par 260 rakstzīmēm. Chloros produkti atrodas aptuveni 130 rakstzīmes zem projekta mapes, tāpēc Chloros pirms apstrādes noraida dziļāku projekta mapi — nevis palaist programmu un neko neierakstīt, kā tas varēja notikt pirms versijas 1.3.0. Ziņojumā tiek norādīta garākā projekta mape, kas atbilst prasībām.

To var labot jebkurā no šiem veidiem:

* Izmantojiet īsāku projekta mapi (**Darba direktoriju** projekta iestatījumos vai `-o` / `chloros-cli set-project-folder`, ja izmantojat CLI).
* Ieslēdziet Windows garos ceļus un pārstartējiet Chloros. To nodrošina Chloros instalētāja opcija **Enable Windows long paths** (pēc noklusējuma atzīmēta); ja esat to atzīmējumu noņēmis, palaidiet instalētāju no jauna.

Skatīt [Apstrādes uzsākšana](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

</details>

<details>

<summary>Vai es varu rediģēt savus attēlus pirms apstrādes programmā Chloros?</summary>

Nē. Chloros pieņem, ka ievades dati nav mainīti. Nemainiet failu nosaukumus.

</details>

<details>

<summary>Vai varu iestatīt savas „MAPIR Survey3” kameras uz automātisko ekspozīciju un apstrādāt attēlus programmā Chloros?</summary>

Nē. Survey3 attēlu datu kopām jābūt ar fiksētu/bloķētu ekspozīciju, tātad nedrīkst būt automātiskais slēdža ātrums vai automātiskais ISO. Visiem attēliem, kas uzņemti ar vienu un to pašu kameras modeli, jābūt ar identisku slēdža ātrumu un ISO (ekspozīciju).

LATTICE kamerām šāds ierobežojums nav: Chloros reāllaikā regulē to ekspozīciju (Smart AE), un katrā uzņēmumā tiek reģistrēta faktiski izmantotā ekspozīcija un pastiprinājums, ko ņem vērā radiometriskā apstrādes ķēde.

</details>

<details>

<summary>Vai Chloros var apstrādāt vai analizēt ortomozaīkas attēlus?</summary>

Nē. Tiek atbalstīti tikai atsevišķi MAPIR kameras attēli, nevis savienoti attēli, piemēram, ortomozaīkas karte.

</details>

<details>

<summary>Kā es varu paātrināt mērķa noteikšanas posmu programmā Chloros?</summary>

Failu pārlūka tabulā, iepriekš atlasot mērķa attēlus labajā ailē, tiks norādīts Chloros, lai tas meklētu kalibrēšanas mērķus tikai šajos attēlos, kas ievērojami paātrina apstrādi.

</details>

<details>

<summary>Ja es augšupielādēšu savus attēlus uz „<a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud”,</a> vai man tie ir jāapstrādā programmā Chloros pirms augšupielādes?</summary>

Ja plānojat augšupielādēt attēlus mūsu tiešsaistes apstrādes platformā [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription), neapstrādājiet attēlus pirms augšupielādes. Cloud veiks visu to pašu apstrādi un vēl vairāk.

</details>

<details>

<summary>Vai MAPIR kādreiz atbalstīs X funkciju? Es ļoti vēlētos, lai MAPIR piedāvātu X.</summary>

Mēs vienmēr esam ieinteresēti saņemt atsauksmes par mūsu produktiem. Ja atrodat kādu problēmu ar mūsu produktiem vai jums ir ieteikums, kā mēs varam tos uzlabot, lūdzu, [Sazinieties ar mums](https://www.mapir.camera/community/contact), lai dalītos ar savām domām. Lielākā daļa mūsu pētniecības un attīstības darba balstās uz mūsu klientu galveno vajadzību ieklausīšanos.

</details>

<details>

<summary>Vai Chloros ir pieejams Linux operētājsistēmai?</summary>

Jā! Chloros 1.3.0 versija atbalsta Linux amd64 (x86_64) un arm64 (NVIDIA Jetson JetPack 6) ar `.deb` pakotnēm. CLI un Python SDK ir pilnībā atbalstīti Linux vidē, ieskaitot LATTICE kameru un DAQ sensoru vadību reāllaikā. Linux vidē nav grafiskās lietotāja saskarnes (GUI) — visa mijiedarbība notiek, izmantojot [CLI](CLI.md) vai [Python SDK](api-python-sdk.md). Sīkāku informāciju skatiet [Linux pārskatā](linux/linux-overview.md).

</details>

<details>

<summary>Vai es varu palaist Chloros uz NVIDIA Jetson?</summary>

Jā! Chloros atbalsta NVIDIA Jetson **„Orin” sēriju”** — „Orin Nano”, „Orin NX” un „AGX Orin” —, kurās darbojas „JetPack 6”. Vecākie modeļi (Jetson Nano, TX2, Xavier NX) nevar darbināt JetPack 6, un pašreizējais pakotnes variants tos neatbalsta. Chloros automātiski atpazīst jūsu Jetson modeli un optimizē tā apstrādes stratēģiju. Jetson pakete izmanto JetPack 6 CUDA versiju PyTorch, tādējādi apstrāde notiek uz Jetson GPU. Skatīt [NVIDIA Jetson rokasgrāmatu](linux/nvidia-jetson-guide.md), lai iegūtu uzstādīšanas un ieviešanas norādījumus.

</details>

<details>

<summary>Vai Chloros automātiski optimizējas manai aparatūrai?</summary>

Jā! Chloros ietver [dinamisko aprēķinu pielāgošanu](processing-architecture/dynamic-compute-adaptation.md), kas automātiski atpazīst jūsu CPU, GPU, RAM un (uz „Jetson”) termiskos sensorus. Pēc tam tas izvēlas optimālo apstrādes stratēģiju — sākot no `GPU_PARALLEL` GPU ar lielu atmiņu līdz `GPU_SINGLE` mazākiem GPU un līdz `CPU_PARALLEL` sistēmām bez CUDA GPU, ko PyTorch var izmantot. Nav nepieciešama manuāla konfigurācija.

</details>

<details>

<summary>Kas ir 4-diegu apstrādes cauruļvads?</summary>

Chloros izmanto 4-pavedienu cauruļvadu arhitektūru Chloros+ lietotājiem: 1. pavediens (detekcija) ielādē attēlus un atpazīst kalibrēšanas mērķus, 2. pavediens (kalibrēšana) aprēķina atstarošanas kalibrēšanu, 3. pavediens (apstrāde) veic ar GPU paātrinātu debayeringu un indeksa aprēķinu, un 4. pavediens (eksportēšana) raksta izejas failus. Lai nodrošinātu maksimālu caurlaidspēju, vienlaikus dažādos pavedienos var atrasties vairāki attēli. Sīkāku informāciju skatiet sadaļā [Apstrādes cauruļvads](processing-architecture/processing-pipeline.md).

</details>

<details>

<summary>Kā veikt Chloros instalācijas diagnostiku?</summary>

Izmantojiet komandu `selftest`, lai veiktu 7 posmu ātrās pārbaudes testu: versija, portu pieejamība, aizmugures sistēmas palaišana, API savienojamība (`/api/test`), sistēmas informācija (`/api/system-info` — GPU/CUDA/PyTorch), trokšņu noņemšanas modeļa klātbūtne un CUDA + trokšņu noņemšanas gatavība:

```bash
chloros-cli selftest
```

Tas ir īpaši noderīgi Linux sistēmās, lai pārbaudītu GPU un CUDA konfigurāciju. „Jetson” ierīcē `CUDA: False` parasti nozīmē, ka trūkst „JetPack 6” CUDA un cuDNN bibliotēku: instalējiet tās ar komandu `sudo apt install nvidia-jetpack`.

</details>
