# LATTICE kameras

LATTICE ir MAPIR modulārā multispektrālā kameru sistēma, kas paredzēta attēlveidošanai lauksaimniecībā un zinātnē. Katra LATTICE kamera ir būvēta uz Sony IMX265 globālā aizslēga sensora (**3,1 MP, 3,45 µm pikseļi**) bāzes un tiek pieslēgta caur Ethernet kā**GigE Vision** ierīce.

Chloros 1.2.0 nodrošina LATTICE kameru vadību reāllaikā — atklāšanu, reāllaika priekšskatīšanu, attēlu uzņemšanu un sinhronizētu daudzkameru sistēmu darbību — no trim saskarnēm:

| Saskarne    | Kur                                                          | Platformas                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | **Kameras** cilne Chloros sānjoslā                         | Windows 10/11 x64                                        |
| CLI        | `chloros-cli lattice` komandu grupa                           | Windows 10/11 x64, Linux x86_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Meklējat aparatūru?**Kameru moduļi, objektīvi, filtri un joslas, rāmji un stiprinājumi, kabeļi, PoE un trigera vadu savienojumi ir aprakstīti [**LATTICE lietotāja rokasgrāmatā**](https://mapir.gitbook.io/lattice-camera). Šajā nodaļā ir aprakstīta kameru vadība no Chloros.

LATTICE uzņēmumi ir standarta `.tif`/`.tiff` faili, un Chloros tos vienmēr apstrādā, sākot no neapstrādāta uzņēmuma. Skatīt [CLI atsauci](../reference/cli-reference.md) un [SDK atsauci](../reference/sdk-reference.md), lai iepazītos ar pilnu komandu un API virsmu.

## Divas sensoru konfigurācijas

| Konfigurācija | Sensors       | Filtrs                                | Ko nodrošina viena kamera                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| Bayer krāsu | trīskāršs joslas caurlaides filtrs                |**Trīs kalibrētas joslas no vienas ekspozīcijas**                 |
| **M3M**| Monohroms   | viens šaurjoslas interferences filtrs |**Viena kalibrēta josla**; indeksu iegūšanai apvienojiet vairākas M3M kameras |

Tā kā M3M kamera ir monohroniska aiz viena filtra, katram diapazonam tiek veikta atsevišķa ekspozīcija. M3C kamera aptver visus trīs savus diapazonus ar vienu sensora ekspozīciju.

## Modeļu virknes un nosaukumi

Katra kamera savu identifikāciju saglabā GenICam `DeviceUserID` kā modeļa virkni:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros to parāda ar prefiksu `LATT-` (piemēram, `LATT-M3M-L87-F450`). Tā pati virkne `LATT-…` tiek ierakstīta katra eksporta EXIF `Model` tagā un tiek izmantota kā kameras izvades mapes nosaukums apstrādātajos projektos.

| Komponents | Vērtības                                                   | Nozīme                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Sensors    | `M3C` / `M3M`                                            | Bayer krāsu / monohroms                                                                          |
| Objektīvs      | `L41` / `L87`                                            | Šis skaitlis norāda **horizontālo redzes leņķi grādos**: L41 = šaurs (41°), L87 = plats (87°)    |
| Filtrs    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) vai `F<nm>` (M3M) | Skatīt [Filtri un spektrālās joslas](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Modeļa virkne nosaka visu turpmāko: Chloros nosaka sensora profilu, joslu izkārtojumu un rūpnīcas kalibrāciju, pamatojoties uz `DeviceUserID` + `DeviceSerialNumber`. Katrai kamerai nav nekas jākonfigurē — skatiet [Kameru pieslēgšana](connecting.md).

## Filtri un joslas

Spektrālo joslu centri, FWHM malas un pilnais 23-SKU M3M katalogs ir produkta specifikācijas, tāpēc tās atrodamas aparatūras rokasgrāmatā: [**Filtri un spektrālās joslas**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Kas ir svarīgi programmatūras ziņā: filtrēšanas kods modeļa virknē nosaka, kurus produktus Chloros var izveidot. RGB filtru kameras (`FRGB`) ģenerē tikai debayered un priekšskatījuma produktus — starojuma intensitāte un atstarošanās pa joslām nav nozīmīga platjoslas sensoram, tāpēc Chloros tos izlaiž un par to informē. Jebkurš cits filtrs nodrošina pilnu starojuma → atstarojuma → indeksa ķēdi.

## Radiometriskā kalibrēšana īsumā

Katra LATTICE kamera rūpnīcā tiek individuāli kalibrēta, izmantojot NIST izsekojamu ķēdi, un tiek piegādāta kopā ar katrai kamerai atsevišķu sertifikātu. Kas tajā ietilpst, kā tas tiek mērīts un kādu precizitāti varat norādīt, ir aprakstīts aparatūras rokasgrāmatā: [**Rūpnīcas radiometriskā kalibrēšana**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Programmatūras ziņā svarīgi ir tas, ka Chloros nosaka pareizo kalibrāciju, kad kamera tiek pieslēgta, un fiksē piemērotos koeficientus katrā eksportētajā datu kopā — skatiet [Kameru pieslēgšana](connecting.md).

## Šajā nodaļā

* [Kameru pieslēgšana](connecting.md) — automātiskā atklāšana, GUI savienošanas dialoglodziņš, CLI/SDK ekvivalenti, kā arī to, kā tiek noteikta rūpnīcas kalibrēšana (kameras komplektā vai mākonī), kad kamera tiek pieslēgta.

Citas LATTICE tēmas — kameru iestatījumi un vadība reāllaikā, uzņemšanas režīmi, daudzkameru sistēmas, kā arī mono (M3M) apstrāde un indeksi — ir aprakstītas atsevišķās šīs rokasgrāmatas sadaļās, savukārt pilnīga komandu saraksta atrodama [CLI atsauces materiālā](../reference/cli-reference.md) un [SDK atsauces materiālā](../reference/sdk-reference.md).
