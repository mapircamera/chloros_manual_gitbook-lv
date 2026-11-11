---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrēšanas mērķi

MAPIR piedāvā dažādus kalibrēšanas mērķus, kas piemēroti plašam lietojumu klāstam. Zemāk redzamais kompaktais T4-R50 sastāv no 4 paneļiem, kuru gaismas atstarošanas koeficients ir noteikts diapazonā no 250 līdz 2 500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 difūzās atsauces mērķiem ir šādas atstarošanas līknes, [datus var lejupielādēt šeit](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atstarojums :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atstarojums :: 400–1000 nm</p></figcaption></figure>T4P difūzajiem etaloniem ir šādas atstarojuma līknes, [datus var lejupielādēt šeit](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P atstarojums :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P atstarojums :: 400–1000 nm</p></figcaption></figure>Apskatot atstarojuma grafiku, var redzēt, ka vērtības attēlo viļņa garumu (x-ass) pret atstarojuma procentu (y-ass). Kad mēs uzņemam kalibrēšanas mērķa attēlu, mēs izveidojam sakarību starp pikseļa vērtību un atstarojuma procentu spektrā, uz kuru ir jutīgas katras kameras sensora joslas.

Tas nozīmē, ka katram attēlam, ko uzņemat ar mūsu kamerām, varat izmantot mūsu atstarojuma mērķu fotogrāfiju, piemēram, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) vai [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), lai attēlus kalibrētu atbilstoši atstarošanai. Pēc kalibrēšanas katrs attēla pikselis atbilst atstarošanas procentam.

Attiecībā uz **Survey3** izvadi: ja kalibrētos attēlus programmā Chloros eksportējat kā standarta JPG vai TIFF failus, tad atstarojuma procentuālo vērtību aprēķina, dalot pikseļa vērtību ar attēla formāta bitu dziļumu. Tātad JPG gadījumā daliet ar 255, bet TIFF gadījumā — ar 65 535. Jūs varat arī izvēlēties izvades formātu „PERCENT” programmā Chloros, un tad katra pikseļa vērtība būs diapazonā no 0,0 līdz 1,0 procentiem (no 0 % līdz 100 % atstarojuma). Vienkārši ņemiet vērā, ka dažas attēlu apstrādes programmas nepieņem attēlus procentu (peldošā punkta) formātā, turklāt tie aizņem daudz vietas datu glabāšanas vietā.

{% hint style="info" %}
**LATTICE atstarojumam tiek izmantota atšķirīga pikseļu skala.** LATTICE atstarojums tiek saglabāts ar DN 32768 = 100 % atstarojumu (nevis 65535), un katram failam ir pievienota XMP `Chloros:PixelScale` birka, kas norāda tā mērogu. Izlasiet tagu un daliet ar to, nevis pieņemiet, ka vērtība ir nemainīga — skatiet [Izvades attēlu formāti](output-image-formats.md).
{% endhint %}

## Kalibrēšanas mērķi ar LATTICE kamerām

Izmantojot LATTICE kameras, kalibrēšanas mērķis atstarojumam ir **pēc izvēles**: tā vietā programma Chloros var atsaukties uz atstarojumu, izmantojot lejupvērsto starojuma intensitāti, ko mēra DAQ gaismas sensors (ρ = π·L/E). Atskaites punkts tiek izvēlēts ar projekta atstarojuma avota iestatījumu, kas tiek konfigurēts ar `chloros-cli process --reflectance-source` (lietotāja saskarnē nav šī iestatījuma vadības elementa, un tiek izmantota projektā saglabātā vērtība, pēc noklusējuma — `auto` — skatīt [Projekta iestatījumi](project-settings/project-settings.md)):

| Vērtība | Darbība |
| --- | --- |
| `auto` *(noklusējums)* | Kvalitātes pārbaudi (QA) izturējis mērķis kadrā ir **absolūtais etalons**; ja mērķis nav klāt vai QA nav izturēta, Chloros izmanto DAQ lejupvērsto sadalījumu. Mērķis, kas iztur QA ar brīdinājumu (izlaists panelis vai nedarbojas josla), netiek automātiski izvēlēts: tiek izmēģinātas mērķa, mērķa+DAQ un tikai DAQ ķēdes to novērtējuma secībā, kas iegūts uz izslēgtajiem paneļiem. |
| `target` | Stingri tikai mērķis — bez DAQ aizstāšanas. |
| `daq` | DAQ ir noteicošais — lejupvērstais mērījums vienmēr ir atsauce. |

Papildu mērķa darbības veidi LATTICE:

* **Mērķa ģeometrijas** — tiek atbalstīti gan ar ArUco marķēti paneļi, gan paneļi ar fiksētu ROI, gan sloksnes mērķi; ģeometrija tiek ņemta no projekta mērķa konfigurācijas.
* **Mērītie mērķa dati pa vienībām** — `--target-reflectance-dir DIR` norāda uz direktoriju, kurā atrodas mērķa atstarojuma skenējumi pa vienībām (`<serial>.csv`, kas tiek atrasti pēc mērķa vienības sērijas numura/QR koda). Ja mērījums nav veiksmīgs, Chloros izmanto nominālos T3/T4P spektrus.
* **Laika fiksēšana** — atklātais mērķis kalibrē ap to esošos kadrus un tiek saglabāts starp mērķa novērojumiem.

Pilnīga karogu semantika un piemēri atrodami [CLI atsauces dokumentā](reference/cli-reference.md) (skatiet „Eksportēšanas slēdži katram produktam”).

### F988

„F988 atstarošanas koeficients tiek kalibrēts, izmantojot atstarošanas paneli ainas ietvaros: šis diapazons atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tāpēc Chloros piemēro jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.”

Ja F988 tiek palaists, izmantojot tikai DAQ kalibrēšanu, Chloros noraida uz DAQ balstīto atstarojumu šim diapazonam un norāda iemeslu (izlaišanas iemesls `dls-uncalibrated-band-988`); paneļa darba plūsma ir atbalstītais risinājums.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
