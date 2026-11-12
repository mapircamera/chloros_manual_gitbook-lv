# DAQ gaismas sensori

> **Meklējat informāciju par aparatūru?**Paši sensori — modeļi, uzstādīšana, vāciņi, savienojumi, barošana un lietotne SCANNER — ir aprakstīti**[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq)**. Šajā nodaļā ir aprakstīta to izmantošana programmā Chloros.

MAPIR **DAQ** gaismas sensori mēra vides gaismu kā radiometriski kalibrētus spektrus. Programmā Chloros tiem ir divas funkcijas:

* **Autonoms spektrālais instruments** — reāllaika spektra diagrammas, kolorimetriskie dati un `.daq` ieraksti, kas visi pieejami [cilnes „Gaismas sensori”](gui.md), [CLI](cli-quick-start.md) vai Python SDK.
* **Leņķveida starojuma avots atstarojuma aprēķiniem** — apstrādes laikā Chloros interpolē jūsu `.daq` rādījumus katra uzņēmuma ekspozīcijas laika zīmogam un izmanto izmērīto lejupvērsto gaismu, lai pārvērstu kameras starojuma intensitāti par atstarošanas koeficientu (`--reflectance-source daq`); kalibrētiem diapazoniem nav nepieciešams „in-scene“ panelis.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Trīs modeļi, viens datu formāts

| Modelis | Pārraide | Atklāšana |
| --- | --- | --- |
| **DAQ-U** | USB (sērijas) | skenēšana caur sērijas portu |
| **DAQ-M** | Bluetooth Low Energy | BLE skenēšana pēc nosaukuma |
| **DAQ-E** | Ethernet (IPv4, ar PoE barošanu) | mDNS `_daq-e._tcp` (vārds `daq-e-<id>.local`) |

**DAQ-E-S (jauns 1.3.0 versijā).** DAQ-E ar pastāvīgu difuzoru un slīpuma sensoru. Chloros to uzrāda kā `DAQ-E-S` un darbojas tieši tāpat kā DAQ-E, izņemot to, ka tam nav nepieciešama kapes korekcija un katrs nolasījums ietver ierīces slīpumu, sānsvārstību un pacelšanos. Skatīt [DAQ-E-S: fiksēts difuzors un slīpums](caps-and-range.md#daq-e-s-fixed-diffuser-and-tilt).

Visi trīs izmanto vienu un to pašu vadu protokolu un sniedz identiskus datus:

* **135 punktu spektrs no 340 līdz 1010 nm ar 5 nm soli**, kā arī CIE XYZ trīsstimulācijas vērtības katrā kadrā.
* **Radiometriski kalibrēts spektrālais starojuma blīvums W/m²/nm** — katras ierīces rūpnīcas kalibrēšanas komplekts (kā arī tās aktīvais vāciņa korekcijas profils) tiek piemērots, pirms dati nonāk pie jums.
* Vienāds **`.daq` ierakstīšanas formāts** (SQLite fails). Turpmākā apstrāde ir identiska neatkarīgi no tā, kurš pārraides veids ir radījis failu.

Pārraides protokoli (USB seriālais, BLE, mDNS/zeroconf) ir iekļauti Chloros backendā — nav nekas jāinstalē, lai sazinātos ar jebkuru no trim modeļiem, izmantojot GUI vai CLI komandas `pool-*`.

***

## Kalibrētais diapazons: ziņots 340–1010 nm, kalibrēts ~374–974 nm

Sensors ziņo par pilnu 340–1010 nm diapazonu, taču NIST standartam atbilstošais radiometriskais pastiprinājums aptver aptuveni **374–974 nm**. Chloros noraida absolūtās atstarošanas sadalījumu jebkurai kameras joslai, kuras spektrālā svara mazāk nekā puse atrodas šajā kalibrētajā diapazonā; izlaistā josla tiek ziņota ar izlaišanas iemeslu `dls-uncalibrated-band-<nm>`.

No piegādātajiem LATTICE filtru modeļiem tas attiecas tikai uz **F988**:

F988 atstarojums tiek kalibrēts, izmantojot atstarojuma paneli uz vietas: josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tāpēc Chloros izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.

Ja F988 uzņemums tiek apstrādāts, izmantojot tikai pieejamos DAQ datus, Chloros noraida uz DAQ balstīto atstarojumu šim diapazonam ar izlaišanas iemeslu `dls-uncalibrated-band-988` — [reflektances paneļa darba plūsma](../calibration-targets.md) ir atbalstītā metode F988 gadījumā.

***

## Sensoru ID

Katrs DAQ ziņo par stabilu sensora ID. Tā forma atšķiras atkarībā no modeļa:

| Modelis | ID forma | Piemērs |
| --- | --- | --- |
| DAQ-U | 5 oktetu ar defisi | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktetu garš ar defisēm | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Sensora identifikators ir:

* ierakstīts katrā `.daq` failā, ko tas reģistrē,
* atslēga, ko Chloros izmanto, lai iegūtu šīs vienības rūpnīcas kalibrēšanas paketi,
* vērtība, ko jūs nododat `--sensor-id` CLI komandās, un
* DAQ-E gadījumā — arī tā mDNS uzņēmuma nosaukums (`daq-e-def330.local`) — vērtība, ko pieņem `--eth-host`.

***

## Rūpnīcas kalibrēšana un mākonis

Katra DAQ vienība tiek individuāli rūpnīcas kalibrēta, izmantojot NIST izsekojamu radiometrisko ķēdi, un Chloros katrai vienībai ielādē kalibrēšanas paketi, kas saistīta ar tās sensora ID. Kalibrēšanas ziņojumu (PDF) katrai ierīcei var lejupielādēt no sensora iestatījumiem [cilnē „Gaismas sensori”](gui.md).

{% hint style="warning" %}
**DAQ-U un DAQ-M kalibrēšanai ir nepieciešama piekļuve mākonim.**Neviens no šiem modeļiem neuzglabā datus ierīcē: to rūpnīcas kalibrēšanas komplekti atrodas MAPIR mākonī un tiek lejupielādēti pēc sensora identifikatora (pēc tam tiek saglabāti vietējā kešatmiņā). Chloros ir nepieciešams interneta savienojums, lai no DAQ-U vai DAQ-M nosūtītu kalibrētus W/m²/nm datus.**DAQ-E ir izņēmums** — tas glabā kalibrēšanas datus pašā ierīcē.
{% endhint %}
<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

***

## Kur tiek saglabāti ieraksti

| Virsma | Noklusējuma `.daq` galamērķis |
| --- | --- |
| GUI — cilne „Gaismas sensori” | `<project folder>/light_sensor/` (pabeigtie ieraksti tiek automātiski pievienoti projektam) |
| CLI — `daq pool-record` | `~/Documents/DAQ Live View/` datorā, kurā darbojas backend (relatīvais `-o` tiek atrisināts attiecībā pret mapi, kurā tika palaista komanda, ja backend atrodas tajā pašā datorā) |

Katrs `.daq` faila nosaukums ietver sensora ID un laika zīmogu.

***

## Šajā nodaļā

* [**Cilne „DAQ” programmā Chloros**](gui.md) — pilnīgs grafiskās lietotāja saskarnes (GUI) apraksts: katra modeļa pieslēgšana, iestatījumi katram sensoram, spektra diagrammas, kolorimetriskie dati reāllaikā, divu sensoru atstarošanas koeficients un datu reģistrēšana.
* [**Ātrs CLIs sākums (pool-\*)**](cli-quick-start.md) — DAQ sensoru vadība no `chloros-cli daq pool-*`, atbalstītais komandrindas ceļš.
* [**Ierobežojumu profili un kalibrētais diapazons**](caps-and-range.md) — kādi ierobežojumi ir katram modelim, kā tos deklarēt, kā arī detalizēta informācija par kalibrēto spektrālo diapazonu.
* [**Ierakstīšana un .daq formāts**](recording.md) — `.daq` SQLite formāts un ierakstīšanas darba plūsmas.
* [**DAQ-E tīkla savienojumi un laika sinhronizācija**](ethernet-ptp.md) — DAQ-E datu pārraides režīmi un PTP laika sinhronizācija.
* [**Reflektances darba plūsmas**](reflectance.md) — DAQ lejupvērsto datu izmantošana reflektances aprēķināšanai.
* Pilnīgu dokumentāciju par karodziņu līmeņa parametriem skatiet [CLI atsauces materiālā](../reference/cli-reference.md) (sadaļā `chloros-cli daq`) un [SDK atsauces materiālā](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`), kas abas ir izstrādātas tā, lai tās varētu tieši izmantot AI palīgi.
