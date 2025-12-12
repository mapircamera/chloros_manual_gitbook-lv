---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrēšanas mērķi

MAPIR piedāvā dažādus kalibrēšanas mērķus, lai aptvertu plašu lietojumu klāstu. Kompaktā T4-R50, kas redzama zemāk, satur 4 paneļus, kuru gaismas atstarošanās koeficients ir izmērīts diapazonā no 250 līdz 2500 nm.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>

T4 difūzās references mērķiem ir šādas atstarošanas līknes, [datu lejupielāde šeit](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 atstarošanās :: 250–2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 atstarojums :: 400–1000 nm</p></figcaption></figure>

Apskatot atstarošanas grafiku, var redzēt, ka vērtības ir viļņa garums (x ass) pret atstarošanas procentu (y ass). Kad mēs uzņemam kalibrēšanas mērķa attēlu, mēs izveidojam saistību starp pikseļu vērtību un atstarošanas procentu spektrā, uz kuru reaģē katrs kameras sensora joslas.

Tas nozīmē, ka katram attēlam, ko uzņemat ar mūsu kamerām, varat izmantot mūsu atstarojuma mērķu fotoattēlus, piemēram, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) vai [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125), lai kalibrētu attēlus atstarojuma ziņā. Pēc kalibrēšanas katrs attēla pikselis atbilst atstarojuma procentam.

Ja kalibrētos attēlus izvadāt Chloros kā tipisku JPG vai TIFF, tad atstarojuma procentu aprēķina, dalot pikseļu vērtību ar attēla formāta bitu dziļumu. Tātad JPG gadījumā dala ar 255, bet TIFF gadījumā dala ar 65 535. Jūs varat arī izvēlēties PERCENT formāta izvadi Chloros, un tad katrs pikselis būs diapazonā no 0,0 līdz 1,0 procentiem (0% līdz 100% atstarojums). Vienkārši ņemiet vērā, ka dažas attēlu lietojumprogrammas nepieņem procentu (peldošā punkta) attēlus, un tie ir lieli pēc izmēra uzglabāšanas ziņā.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
