# LATTICE kameras

LATTICE ir MAPIR modulārā multispektrālā kameru sistēma, kas paredzēta lauksaimniecības un zinātniskajai attēlveidošanai. Katra LATTICE kamera ir veidota uz Sony IMX265 globālā aizslēga sensora bāzes (**3,1 MP, 3,45 µm pikseļi**) un tiek pieslēgta caur Ethernet kā**GigE Vision** ierīce.

Chloros 1.3.0 nodrošina LATTICE kameru vadību reāllaikā — atklāšanu, reāllaika priekšskatīšanu, attēlu uzņemšanu un sinhronizētu daudzkameru sistēmu darbību — no trim saskarnēm:

| Saskarne    | Kur                                                          | Platformas                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | **Kameras** cilne Chloros sānjoslā                         | Windows 10/11 x64                                        |
| CLI        | `chloros-cli lattice` komandu grupa                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Meklējat aparatūru?**Kameru moduļi, objektīvi, filtri un joslas, rāmji un stiprinājumi, kabeļi, PoE un trigera vadu savienojumi ir aprakstīti [**LATTICE lietotāja rokasgrāmatā**](https://mapir.gitbook.io/lattice-camera). Šajā nodaļā ir aprakstīta kameru vadība no Chloros.

LATTICE uzņemtie attēli ir standarta `.tif`/`.tiff` faili, un Chloros tos vienmēr apstrādā, sākot no neapstrādāta uzņemuma. Pilnu komandu un API interfeisu skatiet [CLI atsauces materiālā](../reference/cli-reference.md) un [SDK atsauces materiālā](../reference/sdk-reference.md).

## Divas sensoru konfigurācijas

| Konfigurācija | Sensors       | Filtrs                                | Ko nodrošina viena kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| Bayer krāsu | trīskāršs joslas filtrs                |**Trīs kalibrētas joslas no vienas ekspozīcijas**                 |
| **M3M**| Monohronā   | viens šaurjoslas interferences filtrs |**Viena kalibrēta josla**; indeksu iegūšanai apvienojiet vairākas M3M kameras |

Tā kā M3M kamera ir monohronā ar vienu filtru, katrai joslai tiek veikta atsevišķa ekspozīcija. M3C kamera aptver visas trīs savas joslas ar vienu sensora ekspozīciju.

## Modeļu virknes un nosaukumi

Katra kamera glabā savu identifikāciju GenICam `DeviceUserID` kā modeļa virkni:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros to parāda ar prefiksu „`LATT-`” (piemēram, „`LATT-M3M-L87-F450`”). Tā pati virkne „`LATT-…`” tiek ierakstīta katra eksporta EXIF tagā „`Model`” un tiek izmantota kā kameras izvades mapes nosaukums apstrādātajos projektos.

| Komponents | Vērtības                                                   | Nozīme                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Sensors    | `M3C` / `M3M`                                            | Bayer krāsu / monohroms                                                                          |
| Objektīvs | `L41` / `L87`                                            | Šis skaitlis norāda **horizontālo redzes leņķi grādos**: L41 = šaurs (41°), L87 = plats (87°)    |
| Filtrs    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) vai `F<nm>` (M3M) | Skatīt [Filtri un spektrālās joslas](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Modeļa virkne nosaka visu turpmāko darbību: Chloros nosaka sensora profilu, joslu izkārtojumu un rūpnīcas kalibrāciju, izmantojot `DeviceUserID` + `DeviceSerialNumber`. Katrai kamerai nav nekas jākonfigurē — skatiet [Kameru pieslēgšana](connecting.md).

## Filtri un spektrālās joslas

Spektrālo joslu centri, FWHM malas un pilnais 23 SKU M3M katalogs ir produkta specifikācijas, tādēļ tās atrodamas aparatūras rokasgrāmatā: [**Filtri un spektrālās joslas**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Kas ir svarīgi programmatūras ziņā: filtra kods modeļa virknē nosaka, kurus produktus Chloros var izveidot. RGB filtru kameras (`FRGB`) ģenerē tikai debayered un priekšskatījuma produktus — starojuma intensitāte un atstarošanās katrā joslā nav nozīmīga platjoslas sensoram, tāpēc Chloros tos izlaiž un par to informē. Katrs cits filtrs nodrošina pilnu starojuma intensitātes → atstarošanās → indeksa ķēdi.

## Radiometriskā kalibrēšana īsumā

Katra LATTICE kamera rūpnīcā tiek individuāli kalibrēta, izmantojot NIST izsekojamu ķēdi, un tiek piegādāta kopā ar katrai kamerai atsevišķu sertifikātu. Kas tajā ir iekļauts, kā tas tiek mērīts un kādu precizitāti varat norādīt, ir aprakstīts aparatūras rokasgrāmatā: [**Rūpnīcas radiometriskā kalibrēšana**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Programmatūras ziņā svarīgi ir tas, ka Chloros nosaka pareizo kalibrāciju, kad kamera tiek pieslēgta, un fiksē piemērotos koeficientus katrā eksportētajā datu kopā — skatiet [Kameru pieslēgšana](connecting.md).

## Šajā nodaļā

* [Kameru pieslēgšana](connecting.md) — automātiskā atklāšana, GUI savienošanas dialoglodziņš, CLI/SDK ekvivalenti un to, kā tiek noteikta rūpnīcas kalibrēšana (kameras pakete pret mākonis), kad kamera tiek pieslēgta.
* [Kameru iestatījumi](camera-settings.md) — visi vadības elementi cilnē „Kameras”: kameru saraksts, iestatījumu logs katrai kamerai un masīva iestatījumu logs (ieskaitot ROI redaktoru un izlīdzināšanu).
* [Uzņemšanas iestatījumi un režīmi](capture.md) — „Uzņemt visu”, vienreizēja / nepārtraukta / intervāla uzņemšana, eksporta veidi un uzņemto attēlu mapes struktūra.
* [Daudzkameru masīvi](arrays.md) — masīva savienošanas vednis, sinhronizācijas kabeļa zonde, joslas platums, PTP un masīva skats reāllaikā.
* [Mono kameras un veģetācijas indeksi](mono-indices.md) — M3M kameras un darba plūsma „izlīdzināšana → salikšana → indeksēšana”.

Pilnīga komandu virsma ir pieejama [CLI atsauces materiālā](../reference/cli-reference.md) un [SDK atsauces materiālā](../reference/sdk-reference.md).
