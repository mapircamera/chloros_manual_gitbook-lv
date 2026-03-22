---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Daudzspektrālo indeksu formulas

Zemāk minētās indeksu formulas izmanto Survey3 filtra vidējās caurlaidības diapazonu kombināciju:

<table><thead><tr><th align="center">Survey3 filtra krāsa</th><th width="196.199951171875" align="center">Survey3 Filtra nosaukums</th><th width="159.800048828125" align="center">Transmisijas diapazons (FWHM)</th><th align="center">Vidējā caurlaidība</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468–483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN– Cyan</td><td align="center">476–512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543–558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598–640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653–668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712–735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798–848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835–865 nm</td><td align="center">850 nm</td></tr></tbody></table>Kad tiek izmantotas šīs formulas, nosaukums var beigties ar &quot;\_1&quot; vai &quot;\_2&quot;, kas atbilst tam, kurš NIR filtrs, vai nu NIR1, vai NIR2, tika izmantots.

***

## EVI - Uzlabotais veģetācijas indekss

Šis indekss sākotnēji tika izstrādāts lietošanai ar MODIS datiem kā uzlabojums salīdzinājumā ar NDVI, optimizējot veģetācijas signālu apgabalos ar augstu lapu platības indeksu (LAI). Tas ir visnoderīgākais reģionos ar augstu LAI, kur NDVI var būt piesātināts. Tas izmanto zilo atstarojuma diapazonu, lai koriģētu augsnes fona signālus un samazinātu atmosfēras ietekmi, tostarp aerosolu izkliedi.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI vērtībām veģetācijas pikseļos jābūt diapazonā no 0 līdz 1. Gaiši objekti, piemēram, mākoņi un baltas ēkas, kā arī tumši objekti, piemēram, ūdens, var izraisīt anomālas pikseļu vērtības EVI attēlā. Pirms EVI attēla izveides no atstarojuma attēla ir jāizslēdz mākoņi un gaiši objekti, kā arī pēc izvēles jānosaka pikseļu vērtību slieksnis no 0 līdz 1.

_Atsauce: Huete, A., et al. &quot;Overview of the Radiometric and Biophysical Performance of the MODIS Vegetation Indices.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Meža seguma indekss 1

Šis indekss atšķir meža vainagus no citiem veģetācijas veidiem, izmantojot multispektrālos atstarojuma attēlus, kuros iekļauta sarkanā malas josla.

$$
FCI1 = Red * RedEdge
$$

Mežainajām teritorijām būs zemākas FCI1 vērtības, jo kokiem ir zemāka atstarojamība un lapotnē ir ēnas.

_Atsauce: Becker, Sarah J., Craig S.T. Daughtry un Andrew L. Russ. „Robusts meža seguma indekss multispektrāliem attēliem.” Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Meža seguma indekss 2

Šis indekss atšķir meža vainagus no cita veida veģetācijas, izmantojot multispektrālos atstarojuma attēlus, kuros nav sarkanās malas joslas.

$$
FCI2 = Red * NIR
$$

Mežainajām teritorijām būs zemākas FCI2 vērtības, jo kokiem ir zemāka atstarojamība un lapotnē ir ēnas.

_Atsauce: Becker, Sarah J., Craig S.T. Daughtry un Andrew L. Russ. &quot;Robust forest cover indices for multispectral images.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## GEMI - Globālais vides monitoringa indekss

Šis nelineārais veģetācijas indekss tiek izmantots globālajai vides uzraudzībai, izmantojot satelītattēlus, un mēģina koriģēt atmosfēras ietekmi. Tas ir līdzīgs NDVI, bet ir mazāk jutīgs pret atmosfēras ietekmi. To ietekmē kailā augsne; tādēļ to nav ieteicams izmantot apgabalos ar retu vai vidēji blīvu veģetāciju.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kur:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Atsauce: Pinty, B., un M. Verstraete. GEMI: nelineārs indekss globālās veģetācijas novērošanai no satelītiem. Vegetation 101 (1992): 15-20._

***

## GARI - Green atmosfēras ietekmei izturīgs indekss

Šis indekss ir jutīgāks pret plašu hlorofila koncentrāciju diapazonu un mazāk jutīgs pret atmosfēras ietekmi nekā NDVI.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gamma konstante ir svēršanas funkcija, kas ir atkarīga no aerosola apstākļiem atmosfērā. ENVI izmanto vērtību 1,7, kas ir Gitelson, Kaufman un Merzylak (1996, 296. lpp.) ieteiktā vērtība.

_Atsauce: Gitelson, A., Y. Kaufman un M. Merzylak. &quot;Green kanāla izmantošana globālās veģetācijas attālajā novērošanā no EOS-MODIS.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI – Green hlorofila indekss

Šo indeksu izmanto, lai novērtētu lapu hlorofila saturu plašā augu sugu klāstā.

$$
GCI = {NIR \over Green} - 1
$$

Plaša NIR un zaļo viļņu spektra izmantošana nodrošina labāku hlorofila satura prognozi, vienlaikus nodrošinot lielāku jutību un augstāku signāla un trokšņa attiecību.

_Atsauce: Gitelson, A., Y. Gritz un M. Merzlyak. „Saistība starp lapu hlorofila saturu un spektrālo atstarojumu, kā arī algoritmi augstāko augu lapu hlorofila neinvazīvai novērtēšanai.” Journal of Plant Physiology 160 (2003): 271–282._

***

## GLI – Green lapu indekss

Šis indekss sākotnēji tika izstrādāts lietošanai kopā ar digitālo RGB kameru kviešu seguma mērīšanai, kur sarkano, zaļo un zilo digitālo skaitļu (DN) diapazons ir no 0 līdz 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI vērtības ir diapazonā no -1 līdz +1. Negatīvās vērtības attēlo augsni un nedzīvos objektus, savukārt pozitīvās vērtības attēlo zaļas lapas un stublājus.

_Atsauce: Louhaichi, M., M. Borman un D. Johnson. &quot;Telpiski lokalizēta platforma un aerofotogrāfija kviešu ganīšanas ietekmes dokumentēšanai.&quot; Geocarto International 16, Nr. 1 (2001): 65-70._

***

## GNDVI - Green Normalizētais veģetācijas indekss

Šis indekss ir līdzīgs NDVI, izņemot to, ka tas mēra zaļo spektru no 540 līdz 570 nm, nevis sarkano spektru. Šis indekss ir jutīgāks pret hlorofila koncentrāciju nekā NDVI.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Atsauce: Gitelson, A., un M. Merzlyak. &quot;Hlorofila koncentrācijas attālās uzrādes augstāko augu lapās.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - Green Optimizētais augsnes koriģētais veģetācijas indekss

Šis indekss sākotnēji tika izstrādāts, izmantojot krāsu-infrasarkano fotogrāfiju, lai prognozētu kukurūzas slāpekļa vajadzības. Tas ir līdzīgs OSAVI, bet zaļo joslu aizstāj ar sarkano.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Atsauce: Sripada, R., et al. &quot;Kukurūzas slāpekļa vajadzību noteikšana sezonas laikā, izmantojot aerofoto krāsu-infrasarkano fotogrāfiju.&quot; Doktora disertācija, Ziemeļkarolīnas Valsts universitāte, 2005._

***

## GRVI – Green attiecības veģetācijas indekss

Šis indekss ir jutīgs pret fotosintēzes ātrumu meža vainagu slānī, jo zaļās un sarkanās atstarojuma intensitātes ir spēcīgi ietekmētas no lapu pigmentu izmaiņām.

$$
GRVI = {NIR \over Green }
$$

_Atsauce: Sripada, R., et al. &quot;Aerial Color Infrared Photography for Determining Early In-season Nitrogen Requirements in Corn.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Green Augsnes koriģētais veģetācijas indekss

Šis indekss sākotnēji tika izstrādāts, izmantojot krāsu-infrasarkano fotogrāfiju, lai prognozētu kukurūzas slāpekļa vajadzības. Tas ir līdzīgs SAVI, bet zaļo joslu aizstāj ar sarkano.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Atsauce: Sripada, R., et al. &quot;Kukurūzas slāpekļa vajadzību noteikšana sezonas laikā, izmantojot krāsu-infrasarkano aerofotogrāfiju.&quot; Doktorantūras disertācija, Ziemeļkarolīnas Valsts universitāte, 2005._

***

## LAI - Lapu platības indekss

Šo indeksu izmanto, lai novērtētu lapu segumu un prognozētu kultūraugu augšanu un ražu. ENVI aprēķina zaļo LAI, izmantojot šādu empīrisko formulu no Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

Kur EVI ir:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Augstas LAI vērtības parasti svārstās no aptuveni 0 līdz 3,5. Tomēr, ja attēlā ir mākoņi un citi spilgti objekti, kas rada piesātinātus pikseļus, LAI vērtības var pārsniegt 3,5. Ideāli būtu, ja pirms LAI attēla izveides no attēla izslēgtu mākoņus un spilgtus objektus.

_Atsauce: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde un A. Thomsen. &quot;Gaisa multispektrālie dati lapu platības indeksa, slāpekļa koncentrācijas un fotosintēzes efektivitātes kvantificēšanai lauksaimniecībā.&quot; Remote Sensing of Environment 81, nr. 2-3 (2002): 179–193._

***

## LCI – Lapu hlorofila indekss

Šo indeksu izmanto, lai novērtētu hlorofila saturu augstākajos augos, kas ir jutīgi pret atstarojuma izmaiņām, ko izraisa hlorofila absorbcija.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Atsauce: Datt, B. „Eikaliptu lapu ūdens satura attālās uzrādes novērošana.” Journal of Plant Physiology 154, nr. 1 (1999): 30–36._

***

## MNLI – Modificētais nelineārais indekss

Šis indekss ir nelineārā indeksa (NLI) uzlabojums, kurā iekļauts augsnes koriģētais veģetācijas indekss (SAVI), lai ņemtu vērā augsnes fonu. ENVI izmanto lapotnes fona korekcijas koeficientu (_L_) ar vērtību 0,5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Atsauce: Yang, Z., P. Willis un R. Mueller. „Band-Ratio Enhanced AWIFS attēla ietekme uz kultūraugu klasifikācijas precizitāti.” Pecora 17 Tālizpētes simpozija materiāli (2008), Denvera, Kolorādo._

***

## MSAVI2 – Modificētais augsnei pielāgots veģetācijas indekss 2

Šis indekss ir vienkāršota versija indeksam MSAVI, ko ierosināja Qi u.c. (1994) un kas uzlabo augsnes koriģēto veģetācijas indeksu (SAVI). Tas samazina augsnes troksni un palielina veģetācijas signāla dinamisko diapazonu. MSAVI2 balstās uz induktīvo metodi, kas neizmanto konstantu _L_ vērtību (kā SAVI), lai izceltu veselīgu veģetāciju.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Atsauce: Qi, J., A. Chehbouni, A. Huete, Y. Kerr un S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## NDRE- Normalizētā starpība RedEdge

Šis indekss ir līdzīgs NDVI, bet salīdzina kontrastu starp NIR un RedEdge, nevis Red, kas bieži vien ātrāk atklāj veģetācijas stresu.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Normalizētais veģetācijas indekss

Šis indekss ir veselīgas, zaļas veģetācijas rādītājs. Tā normalizētās starpības formulējuma kombinācija un hlorofila augstākās absorbcijas un atstarošanas reģionu izmantošana padara to stabilu plašā apstākļu diapazonā. Tomēr tas var sasniegt piesātinājumu blīvas veģetācijas apstākļos, kad LAI kļūst augsts.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Šī indeksa vērtība ir no -1 līdz 1. Zaļajai veģetācijai raksturīgais diapazons ir no 0,2 līdz 0,8.

_Atsauce: Rouse, J., R. Haas, J. Schell un D. Deering. Vegetācijas sistēmu monitorings Lielajās līdzenumos ar ERTS. Trešais ERTS simpozijs, NASA (1973): 309–317._

***

## NLI – nelineārais indekss

Šis indekss pieņem, ka attiecības starp daudziem veģetācijas indeksiem un virsmas biofizikālajiem parametriem ir nelineāras. Tas linearizē attiecības ar virsmas parametriem, kas parasti ir nelineāri.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Atsauce: Goel, N. un W. Qin. &quot;Lapojuma arhitektūras ietekme uz sakarībām starp dažādiem veģetācijas indeksiem un LAI un Fpar: datorizēta simulācija.&quot; Remote Sensing Reviews 10 (1994): 309–347._

***

## OSAVI – Optimizētais augsnes koriģētais veģetācijas indekss

Šis indekss balstās uz augsnes koriģēto veģetācijas indeksu (SAVI). Tas izmanto standarta vērtību 0,16 lapotnes fona korekcijas koeficientam. Rondeaux (1996) noteica, ka šī vērtība nodrošina lielāku augsnes variāciju nekā SAVI zemas veģetācijas seguma gadījumā, vienlaikus parādot paaugstinātu jutību pret veģetācijas segumu, kas pārsniedz 50 %. Šo indeksu vislabāk izmantot apgabalos ar salīdzinoši retu veģetāciju, kur augsne ir redzama caur lapotni.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Atsauce: Rondeaux, G., M. Steven un F. Baret. &quot;Optimization of Soil-Adjusted Vegetation Indices.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI — renormalizētais veģetācijas indekss

Šis indekss izmanto starpību starp tuvu infrasarkano un sarkano viļņu garumiem kopā ar NDVI, lai izceltu veselīgu veģetāciju. Tas nav jutīgs pret augsnes un saules novērošanas ģeometrijas ietekmi.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Atsauce: Roujean, J., un F. Breon. &quot;Veģetācijas absorbētā PAR novērtēšana, izmantojot divvirzienu atstarojuma mērījumus.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI – Augsnes koriģētais veģetācijas indekss

Šis indekss ir līdzīgs NDVI, taču tas samazina augsnes pikseļu ietekmi. Tas izmanto lapotnes fona korekcijas koeficientu _L_, kas ir atkarīgs no veģetācijas blīvuma un bieži prasa iepriekšēju informāciju par veģetācijas daudzumu. Huete (1988) iesaka optimālo vērtību _L_=0,5, lai ņemtu vērā pirmās pakāpes augsnes fona variācijas. Šo indeksu vislabāk izmantot apgabalos ar salīdzinoši retu veģetāciju, kur augsne ir redzama caur lapotni.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Atsauce: Huete, A. &quot;A Soil-Adjusted Vegetation Index (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI – transformētais veģetācijas indekss

Šis indekss ir noderīgs veģetācijas seguma novērošanai pilsētvidē. Tas nesasniedz piesātinājumu tāpat kā NDVI un SAVI.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Atsauce: Bannari, A., H. Asalhi un P. Teillet. &quot;Transformed Difference Vegetation Index (TDVI) for Vegetation Cover Mapping&quot; In Proceedings of the Geoscience and Remote Sensing Symposium, IGARSS &#x27;02, IEEE International, Volume 5 (2002)._

***

## VARI – redzamā atmosfēras izturības indekss

Šis indekss balstās uz ARVI un tiek izmantots, lai novērtētu veģetācijas daļu attēlā ar zemu jutību pret atmosfēras ietekmi.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Atsauce: Gitelson, A., et al. &quot;Vegetation and Soil Lines in Visible Spectral Space: A Concept and Technique for Remote Estimation of Vegetation Fraction. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI — veģetācijas indekss ar plašu dinamisko diapazonu

Šis indekss ir līdzīgs NDVI, taču tajā tiek izmantots svēruma koeficients (_a_), lai samazinātu atšķirību starp tuvu infrasarkano un sarkano signālu ieguldījumu NDVI. WDRVI ir īpaši efektīvs ainās ar vidēju līdz augstu veģetācijas blīvumu, kad NDVI pārsniedz 0,6. NDVI parasti izlīdzinās, palielinoties veģetācijas daļai un lapu platības indeksam (LAI), savukārt WDRVI ir jutīgāks pret plašāku veģetācijas daļu diapazonu un izmaiņām LAI.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Svēruma koeficients (_a_) var būt no 0,1 līdz 0,2. Henebry, Viña un Gitelson (2004) iesaka vērtību 0,2.

_Atsauces_

_Gitelson, A. &quot;Wide Dynamic Range Vegetation Index for Remote Quantification of Biophysical Characteristics of Vegetation.&quot; Journal of Plant Physiology 161, Nr. 2 (2004): 165-173._

_Henebry, G., A. Viña un A. Gitelson. „Vegetācijas indekss ar plašu dinamisko diapazonu un tā potenciālā lietderība plaisu analīzē.” Gap Analysis Bulletin 12: 50–56._
