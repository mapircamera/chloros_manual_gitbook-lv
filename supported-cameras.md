---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/supported-cameras
---

# Atbalstītās kameras

Chloros apstrādā attēlus no divām MAPIR kameru sērijām uz **visām platformām** (Windows, Linux amd64 un Linux arm64/Jetson):

* **Survey3** — Survey3W (plaša leņķa) un Survey3N (šauras leņķa) kameras. Ievade: `RAW+JPG`.
* **LATTICE**— M3C un M3M multispektrālie kameru moduļi. Ievade: `.tif`/`.tiff` uzņēmumi. LATTICE kameras var arī**vadīt reāllaikā** no Chloros — izmantojot GUI cilni „Cameras” (Windows) vai `chloros-cli lattice` / Python SDK (Windows un Linux) — ieskaitot sinhronizētus daudzkameru masīvus. Skatīt [LATTICE rokasgrāmatu](lattice/).

Apstrādes procesā tiek pieņemti arī `.dng` ievades faili.

## Survey3

<table data-header-hidden><thead><tr><th width="156">Ražotājs</th><th width="250">Kameras modelis</th><th width="138">Filtra modelis</th><th width="187">Attēla tips</th></tr></thead><tbody><tr><td><strong>Ražotājs</strong></td><td><strong>Kameras modelis</strong></td><td><strong>Filtra modelis</strong></td><td><strong>Attēla tips</strong></td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>OCN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RE</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NIR</td><td>RAW+JPG, JPG</td></tr></tbody></table>## LATTICE

LATTICE sērija ir modulāra multispektrālā kameru sistēma, kas balstās uz Sony IMX265 globālā aizslēga sensoru (3,1 MP, 3,45 µm pikseļi). Katra kamera saglabā savu identifikāciju kā modeļa virkni:

```
<sensor>-<lens>-F<filter>        e.g.  M3C-L41-FRGN,  M3M-L87-F550
```

Chloros to parāda ar prefiksu `LATT-` (piemēram, `LATT-M3M-L41-F550`), un modeļa virkne nosaka visu turpmāko — sensora profils, spektrālo joslu izkārtojums un kalibrēšana tiek noteikta automātiski; katrai kamerai atsevišķi nav nekas jākonfigurē. Objektīva numurs atbilst **horizontālajam redzes laukam grādos**: `L41` = šaurs 41°, `L87` = plats 87°.

Ir divas sensora konfigurācijas:

| Konfigurācija | Sensors      | Filtra tips                           | Frekvenču joslas katrai kamerai                                                        |
| ------------- | ----------- | ------------------------------------- | ----------------------------------------------------------------------- |
| **M3C**       | Bayer krāsu | Trīskāršs joslas filtrs                       | 3 spektrālās joslas no vienas ekspozīcijas                                 |
| **M3M**       | Monohroms  | Vienots šaurjoslas interferences filtrs | 1 kalibrēta josla — apvienojiet vairākas M3M kameras, lai iegūtu veģetācijas indeksus |

### M3C (Bayer) filtra opcijas

| Filtrs | Joslas (nosaukums @ centrs nm / FWHM nm)       |
| ------ | ---------------------------------------- |
| `FRGB` | Blue 475/30 · Green 550/30 · Red 625/30  |
| `FRGN` | Red 660/21 · Green 550/30 · NIR 850/30   |
| `FOCN` | Orange 615/21 · Cyan 490/38 · NIR 808/14 |
| `FNGB` | Blue 475/30 · Green 550/30 · NIR 850/30  |

### M3M (mono) filtru katalogs — 23 SKU

F-skaitlis ir SKU marķējums; izmērītā josla (iezīmogota katrā kalibrētajā eksporta vienībā) ir filtra skenējums katrai partijai:

| SKU    | Centrs (nm, izmērīts) | FWHM malas (nm) | Platums (nm) |
| ------ | --------------------- | --------------- | ---------- |
| F385   | 379,4                 | 367–392         | 25         |
| F405   | 403,9                 | 390–417         | 27         |
| F450   | 443,7                 | 430–458         | 28         |
| F485   | 489,7                 | 478–502         | 24         |
| F520   | 519,9                 | 504–536         | 32         |
| F550   | 548,4                 | 531–566         | 35         |
| F590   | 589,0                 | 570–608         | 38         |
| F615   | 623,8                 | 614–634         | 20         |
| F632   | 633,4                 | 616–651         | 35         |
| F650   | 651,1                 | 636–666         | 30         |
| F685   | 686,2                 | 675–698         | 23         |
| F715   | — (nomināls)           | 706–724         | 18         |
| F725   | 725.2                 | 712–738         | 26         |
| F750   | 746.0                 | 729–763         | 34         |
| F780   | 775.1                 | 754–796         | 42         |
| F808   | 810.3                 | 789–832         | 43         |
| F832   | 826,1                 | 810–843         | 33         |
| F850   | 846,5                 | 828–865         | 37         |
| F880   | — (nominālais)           | 867–893         | 26         |
| F905   | — (nominālais)           | 892–920         | 28         |
| F940   | 940,6                 | 923–958         | 35         |
| F950   | 945,1                 | 929–961         | 32         |
| F988 † | 985,3                 | 968–1003        | 35         |

_&quot;Lentes malas tiek mērītas kā pilnā platuma vērtības pie puses maksimuma no MAPIR partiju filtru skenējumiem — tās pašas vērtības, ko Chloros iezīmē katrā kalibrētajā eksportā.&quot;_ „— (nominālais)” = partijas skenēšana vēl nav veikta; šiem SKU norādītais centrs ir SKU numurs, bet platums — ražotāja norādītais lielums.

† „F988 atstarojums tiek kalibrēts, izmantojot atstarojuma paneli, kas atrodas skenējamajā laukā: josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tādēļ Chloros izmanto jūsu pēdējo paneļa uzņemto attēlu un saglabā to starp paneļa novērojumiem.” Skatīt [Kalibrēšanas mērķus](calibration-targets.md).

Informāciju par kameras vadību reāllaikā, masīviem, tīkla konfigurāciju un radiometrisko apstrādes ķēdi skatiet [LATTICE rokasgrāmatā](lattice/).
