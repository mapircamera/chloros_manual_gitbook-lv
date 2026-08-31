---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Izvades attēlu formāti

Chloros eksportē apstrādātos rezultātus četros failu formātos. Izvēlieties formātu projekta iestatījumos (GUI), izmantojot `--format` (CLI) vai `export_format` (SDK). CLI un SDK pieņem tieši tādus simbolu virknējumus, kā norādīts zemāk.

| Formāta virkne | Paplašinājums | Pikseļu tips | Pikseļu diapazons | Piezīmes |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(noklusējums)* | `.tif` | uint16 ciparu skaitlis | 0 – 65535 | Ieteicams fotogrammetrijai / ĢIS. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0,0 – 1,0 | 1,0 = 100 % atstarošanas koeficients. Dažas programmas nespēj lasīt TIFF failus ar peldošo komatu; faili ir lielāki. |
| `PNG (8-bit)` | `.png` | uint8 ciparu skaitlis | 0 – 255 | Saspiešana bez zudumiem, piemērota skatīšanai tīmeklī un vizualizācijai. |
| `JPG (8-bit)` | `.jpg` | uint8 ciparu skaitlis | 0 – 255 | Saspiešana ar zudumiem, vismazākie faili. |

## Kur tiek saglabāti izvades faili

Produkti tiek saglabāti projekta mapē, grupēti pēc kameras un pēc tam pēc failu formāta:

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

Kameras mape ir `LATT-<sensor>-<lens>-F<filter>` kamerai LATTICE un `<model>_<filter>` (piemēram, `Survey3N_RGN`) kamerai Survey3. **Katram eksportētajam produktam tiek saglabāts avota faila nosaukums — produktu identificē mape, nevis faila nosaukuma paplašinājums.** Pilnus noteikumus skatiet sadaļā [Kur nonāk izvades dati](reference/cli-reference.md) CLI atsauces dokumentā.

## LATTICE produkti (uzņemšanas un eksportēšanas līmeņi)

Viens LATTICE neapstrādāts kadrs vienā ciklā tiek sadalīts visos pieprasītajos produktos. Katram produkta tipam ir savs ieslēgšanas/izslēgšanas slēdzis (GUI izvēles rūtiņas vai CLI `--debayered` / `--preview` / `--radiance` / `--reflectance`, visi pēc noklusējuma ir ieslēgti):

| Līmenis | Saturs | Datu tips |
| --- | --- | --- |
| `raw` | Tieši no sensora iegūti Bayer dati (monohronās kameras: viena josla). Apstrāde vienmēr sākas no neapstrādātiem datiem. | Kā uzņemts |
| `debayered` | Lineārā demosaika — 3 kanāli M3C, 1 kanāls pelēktoņu skalā M3M. | Lineārais DN |
| `radiance` | Absolūtais spektrālais starojums no pilnās radiometriskās ķēdes, vienībās **W/m²/sr/nm**. Vienmēr tiek ierakstīts kā 32 bitu TIFF (`tiff32/Radiance_Images/`), neatkarīgi no izvēlētā eksporta formāta. | float32 |
| `reflectance` | Atstarošanas koeficients ρ, kur **DN 32768 = ρ 1,0 (100 %)** ar rezervi līdz ρ 2,0. Pix4D-saderīgs. | uint16 |
| `preview` | Ekrānam gatavs attēls: RGB = balansa korekcija + gamma; multispektrāls = viltus krāsu izstiepums. | 8 bitu attēls |

## Reflektances pikseļu vērtību nolasīšana

Atstarošanas koeficients tiek saglabāts kā vesels skaitlis, un **DN, kas nozīmē ρ = 1,0 (100 % atstarošanas koeficients), ir atkarīgs no avota kameras**:

| Avota kamera | ρ = 1,0 ir DN | Kā noteikt |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēta XMP birka `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (apgriezts pie ρ 1,0) | Nav `Chloros:*` XMP marķējumu — šī neesamība ir pazīme. |

**Nolasiet `Chloros:PixelScale` XMP tagu un daliet ar to**, nevis pieņemiet, ka tas ir konstants. Taga vērtība ir definēta uint16 tipā, tādēļ tā saglabājas nemainīga visos izvades formātos, kas veic pārskalēšanu — vispirms normalizējiet saglabāto datu tipu atpakaļ uz uint16 (×257 no 8 bitu, ×65535 no float32).

{% hint style="warning" %}
**Vienā gadījumā mērogs nav iekļauts, kā paredzēts.** Kad 8 bitu avota ieraksts (BayerRG8) tiek rakstīts kā 8 bitu TIFF, apstrādes ķēde to nogriež diapazonā 0–255, nevis pārskalē, tādēļ failam nav norādīts mērogs — Chloros šajā gadījumā apzināti izlaiž `Chloros:PixelScale`. Ja LATTICE atstarošanas failā šī birka nav iekļauta, nepieņemiet, ka ir mērogs; tā vietā eksportējiet failu no jauna 16 bitu vai 32 bitu formātā.
{% endhint %}

Pilnīgos noteikumus (ieskaitot ar MicaSense saderīgās atzīmes) skatiet sadaļā **„Reflektances pikseļu nolasīšana”** [CLI atsauces dokumentā](reference/cli-reference.md).
