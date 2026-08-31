---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrēšanas mērķi

MAPIR piedāvā dažādus kalibrēšanas mērķus, kas piemēroti dažādām lietojumprogrammām. Zemāk redzamais kompaktais T4-R50 satur 4 paneļus, kuru gaismas atstarošanas koeficients ir noteikts diapazonā no 250 līdz 2500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 difūzās atsauces mērķiem ir šādas atstarojuma līknes, [datu lejupielāde šeit](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atstarojums :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atstarojums :: 400–1000 nm</p></figcaption></figure>T4P difūzajiem etaloniem ir šādas atstarojuma līknes, [datus lejupielādēt šeit](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P atstarojums :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P atstarošanas koeficients :: 400–1000 nm</p></figcaption></figure>Apskatot atstarojuma grafiku, var redzēt, ka vērtības attēlo viļņa garumu (x ass) pret atstarojuma procentu (y ass). Kad mēs uzņemam kalibrēšanas mērķa attēlu, mēs izveidojam sakarību starp pikseļa vērtību un atstarojuma procentu spektrā, uz kuru ir jutīgas katras kameras sensora joslas.

Tas nozīmē, ka katram attēlam, ko uzņemat ar mūsu kamerām, varat izmantot kādu no mūsu atstarojuma mērķu attēliem, piemēram, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) vai [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), lai attēlus kalibrētu atbilstoši atstarojumam. Pēc kalibrēšanas katrs attēla pikselis atbilst atstarojuma procentiem.

Attiecībā uz **Survey3** izvades gadījumā, ja kalibrētos attēlus izvadāt Chloros formātā kā parasto JPG vai TIFF, tad atstarošanas procentu aprēķina, dalot pikseļa vērtību ar attēla formāta bitu dziļumu. Tātad JPG formātā daliet ar 255, bet TIFF formātā — ar 65 535. Jūs varat arī izvēlēties izvadi PERCENT formātā, piemēram, Chloros, un tad katra pikseļa vērtība būs diapazonā no 0,0 līdz 1,0 procentiem (no 0 % līdz 100 % atstarojuma). Vienkārši ņemiet vērā, ka dažas attēlu apstrādes programmas nepieņem attēlus procentu (peldošā punkta) formātā, turklāt tie aizņem daudz vietas datu glabāšanā.

{% hint style="info" %}
**LATTICE atstarošanai tiek izmantota atšķirīga pikseļu skala.** LATTICE atstarošana tiek saglabāta ar DN 32768 = 100 % atstarošanu (nevis 65535), un katram failam ir pievienota XMP `Chloros:PixelScale` birka, kas norāda tā skalu. Izlasiet tagu un daliet ar to, nevis pieņemiet, ka skala ir nemainīga — skatiet [Izvades attēlu formāti](output-image-formats.md).
{% endhint %}

## Kalibrēšanas mērķi ar LATTICE kamerām

Izmantojot LATTICE kameras, kalibrēšanas mērķis atstarošanas koeficientam ir **pēc izvēles**: Chloros tā vietā var atsaukties uz atstarošanas koeficientu attiecībā pret lejupvērsto starojuma intensitāti, ko mēra DAQ gaismas sensors (ρ = π·L/E). Atskaites punkts tiek izvēlēts ar atstarošanas avota iestatījumu (Projekta iestatījumi lietotāja saskarnē; `--reflectance-source` programmā CLI; `reflectance_source` programmā SDK):

| Vērtība | Darbība |
| --- | --- |
| `auto` *(noklusējums)* | Kvalitātes pārbaudi izturējis mērķis kadrā ir **absolūtais etalons**; ja mērķis nav klāt vai kvalitātes pārbaude nav izturēta, Chloros izmanto DAQ lejupvērsto sadalījumu. |
| `target` | Tikai mērķis — bez DAQ aizstāšanas. |
| `daq` | DAQ ir noteicošs — lejupvērstais mērījums vienmēr ir atsauce. |

Papildu mērķa darbība LATTICE gadījumā:

* **Mērķu ģeometrijas** — tiek atbalstīti ArUco marķēti paneļi, paneļi ar fiksētu ROI un sloksnes mērķi; ģeometrija tiek ņemta no projekta mērķa konfigurācijas.
* **Mērītie mērķa dati pa vienībām** — `--target-reflectance-dir DIR` norāda uz direktoriju, kurā atrodas mērķa atstarojuma skenējumi pa vienībām (`<serial>.csv`, kas tiek atrasti pēc mērķa vienības sērijas numura/QR koda). Ja mērījums nav veiksmīgs, Chloros izmanto nominālos T3/T4P spektrus.
* **Laika fiksācija** — atklātais mērķis kalibrē ap to esošos kadrus un tiek saglabāts starp mērķa novērojumiem.

Pilnīga karogu semantika un piemēri atrodami [CLI atsauces dokumentā](reference/cli-reference.md) (skatīt „Eksportēšanas slēdži katram produktam”).

### F988

„F988 atstarošanās tiek kalibrēta, izmantojot ainas iekšējo atstarošanās paneli: josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tādēļ Chloros piemēro jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.”

Ja F988 tiek palaists, izmantojot tikai DAQ kalibrēšanu, Chloros noraida uz DAQ balstīto atstarojumu šim diapazonam un norāda iemeslu (izlaides iemesls `dls-uncalibrated-band-988`); paneļa darba plūsma ir atbalstītais risinājums.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
