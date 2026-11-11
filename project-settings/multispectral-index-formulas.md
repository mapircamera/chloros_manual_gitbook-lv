---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Multispektrālo indeksu formulas

Zemāk minētajās indeksu formulās tiek izmantota Survey3 filtru vidējās caurlaidības diapazonu kombinācija:

<table><thead><tr><th align="center">Survey3 filtra krāsa</th><th width="196.199951171875" align="center">Survey3 filtra nosaukums</th><th width="159.800048828125" align="center">Transmisijas diapazons (FWHM)</th><th align="center">Vidējā caurlaidība</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re-RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>

Izmantojot šīs formulas, nosaukums var beigties ar &quot;\_1&quot; vai &quot;\_2&quot;, kas atbilst tam, kurš NIR filtrs — NIR1 vai NIR2 — tika izmantots.

LATTICE M3C (Bayer trīskāršās joslas) kamerām tas pats indeksa dzinējs izmanto M3C filtra joslas:

| M3C filtrs | 1. josla (centrs/FWHM) | 2. josla (centrs/FWHM) | 3. josla (centrs/FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Zils (Blue) 475 nm / 30 nm | Zaļš (Green) 550 nm / 30 nm | Tuvais infrasarkanais (NIR) 850 nm / 30 nm |

LATTICE M3M kameras ir vienfrekvenču (vienam kameras objektīvam ir viens šaurjoslas filtrs), tāpēc atsevišķam M3M attēlam daudzfrekvenču indeksi netiek aprēķināti. Lai aprēķinātu indeksus ar M3M, apvienojiet divas vai vairākas kameras saskaņotā daudzjoslu attēlu kopā un izmantojiet LATTICE indeksu aprēķināšanas moduli (`chloros-cli lattice index` vai GUI tiešsaistes indeksu kalkulatoru).

***

## Kur darbojas katrs indeksa nosaukums

Chloros ir **trīs** indeksu virsmas, un to iepriekš iestatīto sarakstu saturs nav identisks. Izmantojiet šo sadaļu, lai pārbaudītu, vai nosaukums darbosies tur, kur plānojat to izmantot.

| Kur atrodaties | Kura saraksta piemēro | Skaits |
| --- | --- | --- |
| Projekta iestatījumi → Indekss → Pievienot indeksu (GUI) | Virsma 1 | 27 |
| Attēlu skatītājs [Indeksa/LUT izmēģinājumu vide](../image-viewer-gui/index-lut-sandbox.md) (GUI) | Virsma 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Virsma 2 | 22 |
| SDK `process_folder(indices=[...])` | Virsma 2 | 22 |
| `chloros-cli lattice index --preset` | Virsma 3 | 22 (cits 22) |
| Kameru cilnes reāllaika indeksa kalkulators | Virsma 3 | 22 (cits 22) |

Surface 1 un 2 strādā ar **vienu attēlu vienlaikus no vienas kameras**, izmantojot simbolu vietas `x`/`y`/`z`(/`a`), kas saistītas ar šīs kameras filtra kanāliem. Virsma 3 darbojas ar**saskaņotu daudzjoslu attēlu kopu** — vairākas LATTICE kameras, kas sinhronizētas vienā kubā — un atsaucas uz kanāliem ar mazajiem burtiem.

### 1. GUI projekta iestatījumi / attēlu skatītāja sandbox izvēlne — 27 formulas

Izvēlnē tās ir uzskaitītas šādā secībā (tas ir ievietošanas secība, nevis alfabētiskā secība):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

GUI vidē jūs velkat savas kameras filtru kanālus uz formulas joslu vietām, tādējādi jebkuru formulu var izmantot ar jebkuru joslu piešķiršanu, ko atbalsta jūsu kamera. Jūsu saglabātās pielāgotās formulas tiek pievienotas zem šī saraksta.

**Piecas tikai GUI paredzētās** formulas — tās, kuras CLI/SDK saraksts `--indices` nepieņem — ir īstenotas šādi:

| Tikai GUI paredzēta iestatījuma | Formula (kā īstenota) | Sloti |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (četri sloti) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Katram no tiem paredzētā atbilstība ir norādīta atsevišķā sadaļā tālāk šajā lapā (piemēram, GARI paredz x=Green, y=NIR, z=Blue, a=Red). GARI ir vienīgā formula programmā Chloros, kas izmanto ceturto vietu.

### 2. CLI / SDK `--indices` nosaukumu paplašinājums — 22 iestatījumi

`chloros-cli process --indices` opcija (un SDK `indices` parametrs) atbalsta šādus iestatījumu nosaukumus:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Nezināmi indeksu nosaukumi tiek klusi ignorēti.** Nosaukums, kas nav iekļauts šajā sarakstā (ieskaitot piecas tikai GUI paredzētās formulas `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` un jebkura lietotāja definēta formula, ko esat saglabājis GUI) tiek izlaists, par to vienīgi izvadot paziņojumu žurnālā — izpilde turpinās bez šī indeksa, un izpilde joprojām tiek atzīta par veiksmīgu. Paziņojums tiek izvadīts šādā veidā:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Nosaukumi tiek salīdzināti, neņemot vērā lielos un mazos burtus, pēc atstarpju noņemšanas, tādēļ `ndvi`, `NDVI` un ` NDVI ` ir viena un tā pati iestatījumu kombinācija. Iestatījums tiek izlaists arī tad, ja tam nepieciešama josla, ko jūsu kameras filtrs nenodrošina.
{% endhint %}

Precīzas īstenotās formulas (simboli `x`/`y`/`z` ir spektrālo joslu pozīcijas; noklusējuma saistījums ir norādīts katram iestatījumam):

| Iestatījums | Formula (kā īstenota) | Noklusējuma filtrs | Sloti (x, y, z) |
| --- | --- | --- | --- |
| NDVI | `(y-x)/(y+x)` | RGN | Red, NIR |
| GNDVI | `(y-x)/(y+x)` | RGN | Green, NIR |
| NDRE | `(y-x)/(y+x)` | RE | RE, NIR |
| OSAVI | `(y-x)/(y+x+0.16)` | RGN | Red, NIR |
| SAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Red, NIR |
| MSAVI2 | `(2*y+1-sqrt((2*y+1)*(2*y+1)-8*(y-x)))/2` | RGN | Red, NIR |
| EVI | `2.5*(y-x)/(y+6*x-7.5*z+1)` | RGN | Red, NIR, Blue |
| MSR | `((y/x)-1)/(sqrt(y/x)+1)` | RGN | Red, NIR |
| TDVI | `1.5*(y-x)/sqrt(y*y+x+0.5)` | RGN | Red, NIR |
| LAI | `3.618*(2.5*(y-x)/(y+6*x-7.5*z+1))-0.118` | RGN | Red, NIR, Blue |
| GCI | `(y/x)-1` | RGN | Green, NIR |
| GRVI | `y/x` | RGN | Green, NIR |
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Green, NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Green, NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)*(x/y)` | RGN | Red, Green, NIR |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | NGB | NIR, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### Kā iestatījuma nosaukums tiek pārvērsts par joslu pozīcijām

Kad ievadāt vienkāršu nosaukumu, piemēram, `NDVI`, programmai Chloros ir jāizlemj, kuru faila kanālu katrs simbols nolasīs. Tā izmanto šo tabulu, kas saista filtra kodu ar katra kanāla masīva pozīciju:

| Filtra kods | Kanāls → masīva indekss |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red` tiek pieņemts kā Orange aliass, arī 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

Iestatījuma **noklusējuma filtrs** (augstāk minētā „Noklusējuma filtrs” sleja) tiek izmantots, ja projektā ir attēli ar šo filtru. Ja tādu nav, Chloros pārskata projektā faktiski esošos filtrus secībā `RGN, OCN, NGB, RGB, RE, NIR` un izvēlas pirmo, kas spēj nodrošināt visus kanālus, kas nepieciešami iestatījumam. Ja neviens no tiem to nespēj, iestatījums šajā izpildes reizē netiek izmantots. Tāpēc `NDVI`, kas pieprasīts datu kopai, kurā ir tikai OCN, joprojām rada saprātīgu rezultātu — tas saistās ar OCN pozīcijām Orange un NIR.

LATTICE M3C modeļu virknes satur filtru ar prefiksu `F` (`LATT-M3C-L41-FRGN`), taču prefikss tiek izlaists, kad filtra kods tiek nolasīts no attēla, tādējādi FRGN kamera izšķir caur augšējo `RGN` rindu un tai nav nepieciešama īpaša apstrāde.

### 3. LATTICE indeksa dzinējs (`lattice index --preset`, reāllaika indeksa kalkulators) — 22 iestatījumi

LATTICE dzinējs darbojas ar saskaņotiem daudzjoslu attēlu kopumiem (reāllaika masīviem vai eksportētiem daudzjoslu TIFF failiem) un izmanto kanālu nosaukumus mazajiem burtiem (`red`, `green`, `blue`, `red_edge`, `nir`). Tās iestatījumu saraksts atšķiras no abiem iepriekš minētajiem:

| Iestatījums | Formula | Kanāli |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | sarkans, NIR |
| GNDVI | `(nir - green) / (nir + green)` | zaļais, NIR |
| BNDVI | `(nir - blue) / (nir + blue)` | zils, NIR |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | sarkanais\_malas, NIR |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | zils, zaļš, NIR |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | sarkans, NIR |
| OSAVI | `(nir - red) / (nir + red + 0.16)` | sarkans, NIR |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | sarkans, NIR |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | zils, sarkans, NIR |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | sarkans, NIR |
| CVI | `nir * red / green**2` | sarkans, zaļš, NIR |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | sarkans, NIR |
| TDVI | `1.5 * (nir - red) / sqrt(nir**2 + red + 0.5)` | sarkans, NIR |
| LAI | `3.618 * (2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)) - 0.118` | zils, sarkans, NIR |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | sarkans, zaļš, zils |
| NGRDI | `(green - red) / (green + red)` | sarkans, zaļš |
| VARI | `(green - red) / (green + red - blue)` | sarkans, zaļš, zils |
| TGI | `green - 0.39*red - 0.61*blue` | sarkans, zaļš, zils |
| EXG | `2*green - red - blue` | sarkans, zaļš, zils |
| CIRE | `(nir / red_edge) - 1` | sarkans\_malas, NIR |
| CIGREEN | `(nir / green) - 1` | zaļš, NIR |
| NDWI | `(green - nir) / (green + nir)` | zaļš, nir |

Palaidiet `chloros-cli lattice index --list-presets`, lai izdrukātu šo tabulu no jūsu instalētās versijas, un `--list-gradients`, lai izdrukātu pieejamos krāsu gradientus. Kanālu simboli ir jutīgi pret lielajiem un mazajiem burtiem un tiem jāsakrīt ar iestatījuma nosaukumiem mazajiem burtiem (piem., `--channel red=Red_660 --channel nir=NIR_850`).

***

## CVI — hlorofila veģetācijas indekss

Šis indekss ir jutīgs pret lapu hlorofila saturu plašā lapotnes blīvuma diapazonā. Tajā tiek izmantotas NIR, sarkanā un zaļā spektra joslas.

$$
CVI = NIR * {Red \over Green^{2}}
$$

GUI un CLI/SDK iestatījumu sarakstā tā simbola formula ir `(z/y)*(x/y)`, kur x=Red, y=Green, z=NIR (RGN kameras spektrālās joslas); LATTICE indeksa dzinēja iestatījums ir `nir * red / green**2`. Katrs virsmas laukums aprēķina vienādu indeksu.

_Atsauce: Vincini, M., E. Frazzi un P. D&#x27;Alessio. „Plaša spektra lapu hlorofila veģetācijas indekss lapotnes mērogā.” Precision Agriculture 9 (2008): 303–319._

***

## ENDVI — uzlabotais normalizētais veģetācijas indekss

Šis indekss papildus NIR un zaļajam kanālam izmanto arī zilo kanālu un ir populārs kamerās ar NGB filtru, kurās zilais spektra joslas aizstāj sarkano.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Īstenošanai izmanto simbolu formulu `((x+y)-(2*z))/((x+y)+(2*z))` — piešķiriet savas kameras NIR un Green kanālus x/y pozīcijām, bet Blue — z (NGB kamerai: x=NIR, y=Green, z=Blue).

***

## EVI — uzlabotais veģetācijas indekss

Šis indekss sākotnēji tika izstrādāts lietošanai ar MODIS datiem kā NDVI uzlabojums, optimizējot veģetācijas signālu apgabalos ar augstu lapu platības indeksu (LAI). Tas ir visnoderīgākais reģionos ar augstu LAI, kur NDVI var sasniegt piesātinājumu. Tas izmanto zilo atstarošanas diapazonu, lai koriģētu augsnes fona signālus un samazinātu atmosfēras ietekmi, tostarp aerosolu izkliedi.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI vērtībām veģetācijas pikseļos jābūt diapazonā no 0 līdz 1. Gaiši objekti, piemēram, mākoņi un baltas ēkas, kā arī tumši objekti, piemēram, ūdens, var izraisīt anomālas pikseļu vērtības EVI attēlā. Pirms EVI attēla izveides jāizslēdz mākoņi un spilgti objekti no atstarojuma attēla, kā arī pēc izvēles jānosaka pikseļu vērtību slieksnis no 0 līdz 1.

_Atsauce: Huete, A., et al. „MODIS veģetācijas indeksu radiometrisko un biofizikālo īpašību pārskats.” Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 — Meža seguma indekss 1

_Tikai GUI — nav pieejams kā CLI/SDK `--indices` iestatījums._

Šis indekss atšķir meža vainagus no citiem veģetācijas veidiem, izmantojot multispektrālos atstarojuma attēlus, kuros iekļauta sarkanā malas josla.

$$
FCI1 = Red * RedEdge
$$

Mežainajām teritorijām būs zemākas FCI1 vērtības, jo kokiem ir zemāka atstarojamība un lapotnē ir ēnas.

_Atsauce: Becker, Sarah J., Craig S.T. Daughtry un Andrew L. Russ. „Robust forest cover indices for multispectral images.” Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505–512._

***

## FCI2 — Meža seguma indekss 2

_Tikai GUI — nav pieejams kā CLI/SDK `--indices` iestatījums._

Šis indekss atšķir meža lapotni no cita veida veģetācijas, izmantojot multispektrālos atstarojuma attēlus, kuros nav iekļauta sarkanā malas josla.

$$
FCI2 = Red * NIR
$$

Mežainajām teritorijām būs zemākas FCI2 vērtības, jo kokiem ir zemāka atstarojamība un lapotnē ir ēnas.

_Atsauce: Becker, Sarah J., Craig S.T. Daughtry un Andrew L. Russ. „Robust forest cover indices for multispectral images.” Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505–512._

***

## GEMI — Globālais vides monitoringa indekss

_Tikai GUI — nav pieejams kā CLI/SDK `--indices` iestatījums._

Šo nelineāro veģetācijas indeksu izmanto globālajai vides uzraudzībai, izmantojot satelītattēlus, un tas cenšas koriģēt atmosfēras ietekmi. Tas ir līdzīgs NDVI, taču ir mazāk jutīgs pret atmosfēras ietekmi. To ietekmē kaila augsne; tādēļ to nav ieteicams izmantot apgabalos ar retu vai vidēji blīvu veģetāciju.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Atsauce: Pinty, B., un M. Verstraete. „GEMI: nelineārs indekss globālās veģetācijas novērošanai no satelītiem”. Vegetation 101 (1992): 15–20._

***

## GARI — Green Atmospherically Resistant Index

_Tikai GUI — nav pieejams kā CLI/SDK `--indices` iestatījums._

Šis indekss ir jutīgāks pret plašu hlorofila koncentrāciju diapazonu un mazāk jutīgs pret atmosfēras ietekmi nekā NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gammas konstante ir svēršanas funkcija, kas ir atkarīga no aerosolu apstākļiem atmosfērā. ENVI izmanto vērtību 1,7, kas ir Gitelson, Kaufman un Merzylak ieteiktā vērtība (1996, 296. lpp.).

_Atsauce: Gitelson, A., Y. Kaufman un M. Merzylak. „Zaļā kanāla izmantošana globālās veģetācijas attālajā izpētē, izmantojot EOS-MODIS.” Remote Sensing of Environment 58 (1996): 289–298._

***

## GCI — zaļais hlorofila indekss (Green Chlorophyll Index)

Šo indeksu izmanto, lai novērtētu lapu hlorofila saturu plašā augu sugu klāstā.

$$
GCI = {NIR \over Green} - 1
$$

Plaša NIR un zaļā viļņu diapazona izmantošana nodrošina precīzāku hlorofila satura prognozi, vienlaikus nodrošinot lielāku jutību un augstāku signāla-trokšņa attiecību.

_Atsauce: Gitelson, A., Y. Gritz un M. Merzlyak. „Saistība starp lapu hlorofila saturu un spektrālo atstarošanos, kā arī algoritmi hlorofila nedestruktīvai novērtēšanai augstāko augu lapās.” Journal of Plant Physiology 160 (2003): 271–282._

***

## GLI — zaļo lapu indekss (Green Leaf Index)

Šis indekss sākotnēji tika izstrādāts lietošanai kopā ar digitālo RGB kameru, lai mērītu kviešu segumu, kur sarkano, zaļo un zilo digitālo skaitļu (DN) diapazons ir no 0 līdz 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI vērtības ir diapazonā no -1 līdz +1. Negatīvās vērtības atspoguļo augsni un nedzīvos objektus, savukārt pozitīvās vērtības — zaļās lapas un stublājus.

_Atsauce: Louhaichi, M., M. Borman un D. Johnson. „Telpiski lokalizēta platforma un aerofotogrāfija kviešu ganīšanas ietekmes dokumentēšanai.” Geocarto International 16, Nr. 1 (2001): 65–70._

***

## GNDVI — normalizētais zaļās spektra veģetācijas indekss (Green)

Šis indekss ir līdzīgs NDVI, izņemot to, ka tas mēra zaļo spektru no 540 līdz 570 nm, nevis sarkano spektru. Šis indekss ir jutīgāks pret hlorofila koncentrāciju nekā NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Atsauce: Gitelson, A., un M. Merzlyak. „Hlorofila koncentrācijas attālās uzrādes augstāko augu lapās.” Advances in Space Research 22 (1998): 689–692._

***

## GOSAVI — zaļajai krāsai optimizēts, augsnei pielāgots veģetācijas indekss (Green Optimized Soil Adjusted Vegetation Index)

Šis indekss sākotnēji tika izstrādāts, izmantojot krāsu-infrasarkano fotogrāfiju, lai prognozētu kukurūzas slāpekļa vajadzības. Tas ir līdzīgs OSAVI indeksam, taču sarkano joslu aizstāj ar zaļo.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Atsauce: Sripada, R., et al. „Kukurūzas slāpekļa vajadzību noteikšana sezonas laikā, izmantojot krāsu-infrasarkano aerofotogrāfiju.” Doktorantūras disertācija, Ziemeļkarolīnas Valsts universitāte, 2005._

***

## GRVI — zaļā attiecības veģetācijas indekss (Green Ratio Vegetation Index)

Šis indekss ir jutīgs pret fotosintēzes intensitāti meža lapotnē, jo zaļās un sarkanās gaismas atstarošanos būtiski ietekmē izmaiņas lapu pigmentos.

$$
GRVI = {NIR \over Green }
$$

_Atsauce: Sripada, R., et al. „Krāsu un infrasarkano staru aerofotogrāfija kukurūzas slāpekļa vajadzību noteikšanai sezonas sākumā.” Agronomy Journal 98 (2006): 968–977._

***

## GSAVI — zaļās augsnes koriģētais veģetācijas indekss (Green Soil Adjusted Vegetation Index)

Šis indekss sākotnēji tika izstrādāts, izmantojot krāsu-infrasarkano fotogrāfiju, lai prognozētu kukurūzas slāpekļa vajadzības. Tas ir līdzīgs SAVI indeksam, taču zaļo spektra joslu aizstāj ar sarkano.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Atsauce: Sripada, R., et al. „Kukurūzas sezonas laikā nepieciešamā slāpekļa daudzuma noteikšana, izmantojot krāsu infrasarkano aerofotogrāfiju.” Doktorantūras disertācija, Ziemeļkarolīnas Valsts universitāte, 2005._

***

## LAI — lapu platības indekss

Šo indeksu izmanto, lai novērtētu lapu segumu un prognozētu kultūraugu augšanu un ražu. ENVI aprēķina zaļo LAI, izmantojot šādu empīrisko formulu no Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI ir:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Augstas LAI vērtības parasti svārstās no aptuveni 0 līdz 3,5. Tomēr, ja attēlā ir mākoņi un citi spilgti objekti, kas rada piesātinātus pikseļus, LAI vērtības var pārsniegt 3,5. Ideālā gadījumā pirms LAI attēla izveides no attēla vajadzētu izslēgt mākoņus un spilgtus objektus.

_Atsauce: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde un A. Thomsen. „Gaisā iegūti multispektrālie dati lapu platības indeksa, slāpekļa koncentrācijas un fotosintēzes efektivitātes kvantificēšanai lauksaimniecībā.” Remote Sensing of Environment 81, nr. 2–3 (2002): 179–193._

***

## LCI — lapu hlorofila indekss

_Tikai GUI — nav pieejams kā CLI/SDK `--indices` iestatījums._

Šo indeksu izmanto, lai novērtētu hlorofila saturu augstākajos augos, kas ir jutīgi pret atstarojuma izmaiņām, ko izraisa hlorofila absorbcija.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Atsauce: Datt, B. „Eikaliptu lapu ūdens satura attālās uzrādes novērošana.” Journal of Plant Physiology 154, nr. 1 (1999): 30–36._

***

## MNLI — Modificētais nelineārais indekss

Šis indekss ir nelineārā indeksa (NLI) uzlabojums, kurā iekļauts augsnes korekcijas veģetācijas indekss (SAVI), lai ņemtu vērā augsnes fona ietekmi. ENVI izmanto lapotnes fona korekcijas koeficienta (_L_) vērtību 0,5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Atsauce: Yang, Z., P. Willis un R. Mueller. „Band-Ratio Enhanced AWIFS attēla ietekme uz kultūraugu klasifikācijas precizitāti.” Pecora 17 Tālizpētes simpozija materiāli (2008), Denvera, Kolorādo._

***

## MSAVI2 — Modificētais augsnes koriģētais veģetācijas indekss 2

Šis indekss ir vienkāršota versija MSAVI indeksam, ko ierosināja Qi u.c. (1994), kas uzlabo augsnei pielāgoto veģetācijas indeksu (SAVI). Tas samazina augsnes troksni un palielina veģetācijas signāla dinamisko diapazonu. MSAVI2 balstās uz induktīvu metodi, kas neizmanto konstantu _L_ vērtību (kā SAVI gadījumā), lai izceltu veselīgu veģetāciju.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Atsauce: Qi, J., A. Chehbouni, A. Huete, Y. Kerr un S. Sorooshian. „A Modified Soil Adjusted Vegetation Index.” Remote Sensing of Environment 48 (1994): 119–126._

***

## MSR — modificētais vienkāršais attiecības indekss

Šis indekss ir vienkāršā NIR/Red attiecības modifikācija, kas izstrādāta, lai linearizētu tās saistību ar biofizikālajiem parametriem, un tas ir jutīgāks nekā NDVI pie augstākas veģetācijas blīvuma.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Atsauce: Chen, J. „Vegetācijas indeksu un modificētā vienkāršā attiecības novērtējums boreālo reģionu lietojumiem.” Canadian Journal of Remote Sensing 22 (1996): 229–242._

***

## NDRE — normalizētā starpība RedEdge

Šis indekss ir līdzīgs NDVI, taču salīdzina kontrastu starp NIR un RedEdge, nevis Red, kas bieži vien ļauj ātrāk noteikt veģetācijas stresu.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI — normalizētā veģetācijas starpība

Šis indekss ir veselīgas, zaļas veģetācijas rādītājs. Normalizētās starpības formulējuma kombinācija ar hlorofila augstākās absorbcijas un atstarošanas spektra daļu izmantošanu nodrošina tā stabilitāti plašā apstākļu diapazonā. Tomēr tas var pārsātināties blīvas veģetācijas apstākļos, kad LAI rādītājs kļūst augsts.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šī indeksa vērtība svārstās no -1 līdz 1. Zaļajai veģetācijai raksturīgais diapazons ir no 0,2 līdz 0,8.

_Atsauce: Rouse, J., R. Haas, J. Schell un D. Deering. Veģetācijas sistēmu monitorings Lielajās līdzenumos ar ERTS. Trešais ERTS simpozijs, NASA (1973): 309–317._

***

## NLI — nelineārais indekss

Šis indekss balstās uz pieņēmumu, ka sakarība starp daudziem veģetācijas indeksiem un virsmas biofizikālajiem parametriem ir nelineāra. Tas linearizē sakarības ar virsmas parametriem, kurām parasti piemīt nelineārs raksturs.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Atsauce: Goel, N., un W. Qin. „Lapu vainaga arhitektūras ietekme uz sakarībām starp dažādiem veģetācijas indeksiem un LAI, kā arī Fpar: datorizēta simulācija.” Remote Sensing Reviews 10 (1994): 309–347._

***

## OSAVI — optimizētais augsnes koriģētais veģetācijas indekss

Šis indekss balstās uz augsnes koriģēto veģetācijas indeksu (SAVI). Tas izmanto standarta vērtību 0,16 lapotnes fona korekcijas koeficientam. Rondeaux (1996) konstatēja, ka šī vērtība nodrošina lielāku augsnes variāciju nekā SAVI gadījumā ar zemu veģetācijas segumu, vienlaikus parādot paaugstinātu jutību pret veģetācijas segumu, kas pārsniedz 50 %. Šo indeksu vislabāk izmantot apgabalos ar salīdzinoši retu veģetāciju, kur caur lapotni ir redzama augsne.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Atsauce: Rondeaux, G., M. Steven un F. Baret. „Optimization of Soil-Adjusted Vegetation Indices.” Remote Sensing of Environment 55 (1996): 95–107._

***

## RDVI — renormalizētais veģetācijas starpības indekss

Šis indekss izmanto starpību starp tuvu infrasarkano un sarkano viļņu garumiem kopā ar NDVI, lai izceltu veselīgu veģetāciju. Tas nav jutīgs pret augsnes ietekmi un saules novērošanas ģeometriju.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Atsauce: Roujean, J., un F. Breon. „Veģetācijas absorbētā PAR novērtēšana, izmantojot divvirzienu atstarošanas mērījumus.” Remote Sensing of Environment 51 (1995): 375–384._

***

## SAVI — augsnes korekcijas veģetācijas indekss

Šis indekss ir līdzīgs NDVI, taču tas samazina augsnes pikseļu ietekmi. Tajā tiek izmantots lapotnes fona korekcijas koeficients _L_, kas ir atkarīgs no veģetācijas blīvuma un bieži vien prasa iepriekšējas zināšanas par veģetācijas daudzumu. Huete (1988) ierosina optimālo _L_ vērtību 0,5, lai ņemtu vērā pirmās pakāpes augsnes fona svārstības. Šo indeksu vislabāk izmantot apgabalos ar salīdzinoši retu veģetāciju, kur augsne ir redzama caur lapotni.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Atsauce: Huete, A. „A Soil-Adjusted Vegetation Index (SAVI).” Remote Sensing of Environment 25 (1988): 295–309._

***

## TDVI — transformētais veģetācijas starpības indekss

Šis indekss ir noderīgs veģetācijas seguma novērošanai pilsētvidē. Tas nesasniedz piesātinājumu tāpat kā NDVI un SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Atsauce: Bannari, A., H. Asalhi un P. Teillet. „Transformētais veģetācijas indekss (TDVI) veģetācijas seguma kartēšanai” In „Geoscience and Remote Sensing Symposium, IGARSS &#x27;02” konferences materiāli, IEEE International, 5. sējums (2002)._

***

## VARI — redzamās gaismas atmosfēras ietekmei izturīgs indekss

Šis indekss balstās uz ARVI un tiek izmantots, lai novērtētu veģetācijas daļu attēlā, kam ir zema jutība pret atmosfēras ietekmi.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Atsauce: Gitelson, A., et al. „Veģetācijas un augsnes līnijas redzamās spektrālās telpā: koncepcija un metode veģetācijas daļas attālajai novērtēšanai“. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI — veģetācijas indekss ar plašu dinamisko diapazonu

Šis indekss ir līdzīgs NDVI, taču tajā tiek izmantots svēršanas koeficients (_a_), lai samazinātu atšķirības starp tuvu infrasarkano un sarkano signālu ieguldījumu NDVI. WDRVI ir īpaši efektīvs ainavās ar vidēju līdz augstu veģetācijas blīvumu, kad NDVI pārsniedz 0,6. NDVI parasti izlīdzinās, palielinoties veģetācijas daļai un lapu platības indeksam (LAI), savukārt WDRVI ir jutīgāks pret plašāku veģetācijas daļu diapazonu un LAI izmaiņām.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svēruma koeficients (_a_) var būt no 0,1 līdz 0,2. Henebry, Viña un Gitelson (2004) iesaka vērtību 0,2.

_Atsauces_

_Gitelson, A. „Vegetācijas indekss ar plašu dinamisko diapazonu (WDRVI) vegetācijas biofizikālo raksturlielumu attālajai kvantificēšanai.” Journal of Plant Physiology 161, Nr. 2 (2004): 165–173._

_Henebry, G., A. Viña un A. Gitelson. „Vegetācijas indekss ar plašu dinamisko diapazonu un tā potenciālā lietderība plaisu analīzē.” Gap Analysis Bulletin 12: 50–56._
