---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Izvades attēlu formāti

Chloros eksportē apstrādātos produktus četros failu formātos. Izvēlieties formātu projekta iestatījumos (GUI), izmantojot `--format` (CLI) vai `export_format` (SDK). CLI un SDK pieņem tieši zemāk norādītās virknes.

| Formāta teksta virkne | Paplašinājums | Pikseļu tips | Pikseļu diapazons | Piezīmes |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(noklusējums)* | `.tif` | uint16 ciparu skaitlis | 0 – 65535 | Ieteicams fotogrammetrijai / GIS. |
| `TIFF (8-bit)` | `.tif` | uint8 ciparu skaitlis | 0 – 255 | Visām 8 bitu uzņemtajām bildēm, kuras piedāvā tikai 8 bitu formātus: Chloros nepalielina bitu dziļumu. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0,0 – 1,0 | 1,0 = 100 % atstarošanās. Dažas programmas nevar lasīt TIFF failus ar peldošo komatu; faili ir lielāki. |
| `PNG (8-bit)` | `.png` | uint8 ciparu skaitlis | 0 – 255 | Saspiešana bez zudumiem, piemērota skatīšanai tīmeklī un vizualizācijai. |
| `JPG (8-bit)` | `.jpg` | uint8 ciparu skaitlis | 0 – 255 | Saspiešana ar zudumiem, vismazākie faili. |

## Kur tiek saglabāti izvades faili

Produkti tiek saglabāti projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera (model+lens+filter)
    ├── tiff16/                          # follows --format: tiff16, tiff8, png8, jpg8, or tiff32
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one <INDEX>_Index_Images/ folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Kameras mape ir `LATT-<sensor>-<lens>-F<filter>` kamerai „LATTICE” un `<model>_<filter>` (piem., `Survey3N_RGN`) kamerai Survey3. **Katram eksportētajam produktam tiek saglabāts avota faila nosaukums — produktu identificē mape, nevis faila nosaukuma paplašinājums.** Pilnus noteikumus skatiet sadaļā [Kur tiek saglabāti izvades faili](reference/cli-reference.md) CLI atsauces materiālā.

## LATTICE produkti (uzņemšanas un eksportēšanas līmeņi)

Viens LATTICE neapstrādāts kadrs vienā ciklā tiek sadalīts visos pieprasītajos produktos. Katram produktu tipam ir savs ieslēgšanas/izslēgšanas slēdzis (GUI izvēles rūtiņas vai CLI komandas `--debayered` / `--preview` / `--radiance` / `--reflectance`, visi pēc noklusējuma ir ieslēgti):

| Līmenis | Saturs | Datu tips |
| --- | --- | --- |
| `raw` | Tieši no sensora iegūti Bayer dati (monohronās kameras: viena josla). Apstrāde vienmēr sākas no neapstrādātiem datiem. | Kā uzņemts |
| `debayered` | Lineārā demosaika — 3 kanāli M3C, 1 kanāls pelēktoņu skalā M3M: no kalibrēšanas paketes ir noņemts tumšais nobīde, pēc tam tiek veikta malu ņemšana vērā demosaika bez linearitātes korekcijas, līdzena lauka vai krāsu atdalīšanas. `process` eksportēšana raksta to pašu failu, ko uzņemšana saglabā šajā līmenī, jebkurā apstrādes režīmā. | Lineārais DN |
| `radiance` | Absolūtais spektrālais starojums no pilnās radiometriskās ķēdes, vienībās **W/m²/sr/nm**. Vienmēr tiek saglabāts kā 32 bitu TIFF (`tiff32/Radiance_Images/`), neatkarīgi no izvēlētā eksporta formāta. | float32 |
| `reflectance` | Atstarošanas koeficients ρ, kur **DN 32768 = ρ 1,0 (100 %)** ar rezervi līdz ρ 2,0. Pix4D-saderīgs. | uint16 |
| `preview` | Ekrānam gatavs attēls: RGB = baltā balanss + gamma; multispektrāls = viltus krāsu izstiepums. | 8-bitu attēls |

### Orientācija

`raw` un `debayered` faili vienmēr saglabā sensora **oriģinālo** orientāciju; kameras apgriešanas/pagriešanas iestatījums tiek piemērots tikai pārējiem produktiem (priekšskatījums, starojums, atstarošanās, indekss). Masīva uzņēmumos katra elementa faili saglabā oriģinālo orientāciju, ja vien neesat norādījis orientāciju (`--orient` CLI vidē, `orient=True` SDK vidē). Katrs fails XMP formātā reģistrē orientāciju, kas pieder tā pikseļiem — `ChlorosRotation`, `ChlorosFlipHorizontal`, `ChlorosFlipVertical` — un `ChlorosOrientationApplied` norāda, vai orientāciju nosaka paša faila pikseļi (`false` neapstrādātos un debayered failos). Chloros attēlu skatītājs pārveido neapstrādātos un debayered failus atbilstoši šim ierakstam tikai attēlošanas nolūkā; paši faili nekad netiek pārrakstīti. Fails bez šāda ieraksta (kas izveidots pirms versijas 1.3.0 vai ar citu rīku) tiek parādīts tādā veidā, kādā tas ir saglabāts.

## Klipēšanas maskas

Daudzspektrālās M3C kameras starojuma un atstarošanas rezultāti tiek apstrādāti, veicot 3×3 joslu atdalīšanu. Ja atdalīšanas rezultātā kādā joslā rodas negatīva vērtība, tā tiek nogriezta līdz nullei (reāllaika skatā nogriešana notiek tieši tāpat). Jaunums versijā 1.3.0 — produktam, kurā vismaz viens pikselis ir nogriezts, tiek pievienota **nogriešanas maska**, kas precīzi norāda, kuri pikseļi un joslas ir skarti:

* **Kur:**mapē `clamp_masks/`**blakus** produktu mapēm, nekad to iekšienē: `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif` un `tiff32/Radiance_Images/x.tif` → `tiff32/clamp_masks/Radiance_Images/x_clampmask.tif`.
* **Kas:** vienas joslas `uint8` formāta TIFF fails uz paša produkta pikseļu režģa (iekļaujot orientāciju, ROI, binningu un masīva izlīdzināšanu). Bits 0 (`mask & 1`) tiek iestatīts tur, kur produkta R kanālā josla tika nogriezta, bits 1 (`& 2`) — G kanālā, 2. bits (`& 4`) — B kanālā. Katras joslas apgriezto pikseļu daļa tiek arī atzīmēta produktā kā `Chloros:BandRUnmixClampFraction`, `Chloros:BandGUnmixClampFraction` un `Chloros:BandBUnmixClampFraction`.
* **Kā tas ir norādīts produktā:** XMP tagā `Chloros:UnmixClampMask` ir norādīts maskas ceļš attiecībā pret produkta mapi (`../clamp_masks/<product folder>/<name>_clampmask.tif`, vienmēr atdalīts ar `/`), `none`, ja neviens pikselis netika nogriezts (maska netiek ierakstīta), vai `unavailable`, ja masku nevarēja ierakstīt. Produktam bez atzīmes nekad netika veikta krāsu atdalīšana (RGB- un mono-kameras, neapstrādāti un debayered līmeņi).

Tā kā nevienā produkta mapē nav maskas, jūs varat nodot Pix4D, Metashape vai citai fotogrammetrijas programmatūrai tādu produkta mapi kā, piemēram, `Reflectance_Calibrated_Images/`, tādu, kāda tā ir, ieskaitot apakšmapes — nododiet tai produkta mapi, nevis formāta mapi, kas atrodas virs tās. Chloros pašas importēšanas funkcija izlaiž klampmaskas. CLI tās skaita atsevišķi: `Image products written: N (plus M clamp masks)`.

## Indeksa produkti

Katrs indekss nonāk savā `<INDEX>_Index_Images/` mapē (piem., `NDVI_Index_Images/`).

* **Visa skaitļa formāti**(`TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`) glabā**lineāru izstiepšanu katram attēlam**, nevis −1…1 karti: DN 0 ir šī kadra zemākā indeksa vērtība, bet pilna skala — augstākā. Failā ir `Chloros:IndexScaling=linear_minmax`, `Chloros:IndexMin` un `Chloros:IndexMax`, tātad arī `value = IndexMin + DN / full_scale × (IndexMax − IndexMin)`. Tā kā katram kadram ir savi galapunkti, salīdziniet kadrus pēc vērtībām, nevis pēc DN.
* **`TIFF (32-bit, Percent)`** saglabā pašas indeksa vērtības kā float32, bez pārskalēšanas — labākā izvēle kvantitatīviem aprēķiniem visā lidojuma laikā.

Jaunums versijā 1.3.0 — indeksa produkts apraksta **pašu sevi**, nevis atkārto avota neapstrādāto datu tagus. Tā GPS un kameras tagi tiek kopēti no produkta, no kura tas tika aprēķināts (reflektances produkts, ja tāds ir), un pēc tam:

| Tag | Vērtība |
| --- | --- |
| `Chloros:ProcessingLevel` | `index` (tādējādi fails nekad netiek atkārtoti importēts kā uzņemums) |
| `Chloros:IndexApplied` / `Chloros:IndexExpression` | `True` / izmantotā formula |
| `Chloros:IndexLevel` | `reflectance` vai `radiance` — no kā tika aprēķināts indekss (nav norādīts, ja nav neviena no abiem) |
| `Chloros:IlluminationInvariant` | `True` indeksam, kas aprēķināts no atstarojuma, `False` — no starojuma vai sensora vērtībām |
| `Chloros:ChlorosPipelineVersion` | Chloros versija, kas izveidoja indeksu |

`Chloros:PixelScale` ir izņemts no indeksa produktiem — izmantojiet iepriekš minēto `IndexMin` / `IndexMax`. Pilnīga atpakaļlasīšanas instrukcija atrodama sadaļā „Visa skaitļa indeksa rasters ir kontrasta izstiepums katram attēlam” [CLI atsauces materiālā](reference/cli-reference.md).

## Reflektances pikseļu vērtību nolasīšana

Atstarošanās tiek saglabāta kā vesels cipars, un **DN, kas nozīmē ρ = 1,0 (100 % atstarošanās), ir atkarīgs no avota kameras**:

| Avota kamera | ρ = 1,0 ir DN | Kā noteikt |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēta XMP birka `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (apgriezts pie ρ 1,0) | Nav `Chloros:PixelScale` marķējuma — šī trūkums ir pazīme. (Sākot ar versiju 1.3.0, Survey3 produkti katrā apstrādes režīmā satur citas `Chloros:*` apstrādes birkas, piemēram, `Chloros:CalibrationApplied`.) |

**Izlasiet `Chloros:PixelScale` XMP tagu un daliet ar to**, nevis pieņemiet, ka tas ir konstants. Taga definīcija ir uint16 domēnā, tādēļ tā saglabājas kā `32768` visos izvades formātos, kuros tiek veikta mērogu pārrēķināšana — vispirms normalizējiet saglabāto datu tipu atpakaļ uz uint16 (×257 no 8-bit, ×65535 no float32).

{% hint style="warning" %}
**8-bitu uzņemumi.** 8-bitu uzņemuma eksportētie dati ir 8-bitu. Tā atstarošanas koeficients tiek pārskalots un marķēts ar `Chloros:PixelScale=32768`, tādējādi saglabājot mērogu; 8-bitu DN produkti un atstarojuma faili, kas izveidoti pirms versijas 1.3.0, tiek apgriezti diapazonā 0–255 un tiem nav mēroga marķējuma. Ja LATTICE atstarojuma failā šis marķējums nav norādīts, nepieņemiet, ka mērogs ir saglabāts; tā vietā veiciet atkārtotu eksportēšanu 16-bitu vai 32-bitu formātā.
{% endhint %}

Pilnīgos noteikumus (ieskaitot ar MicaSense saderīgās atzīmes) skatiet sadaļā **„Reflektances pikseļu nolasīšana”** [CLI atsauces materiālā](reference/cli-reference.md).
