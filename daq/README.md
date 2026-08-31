# DAQ gaismas sensori

> **Meklējat informāciju par aparatūru?**Informācija par pašiem sensoriem — modeļiem, montāžu, vāciņiem, pieslēgvietām, barošanu un lietotni SCANNER — ir aprakstīta**[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq)**. Šajā nodaļā ir aprakstīta to izmantošana, sākot no Chloros.

MAPIR **DAQ** gaismas sensori mēra apkārtējās gaismas intensitāti kā radiometriski kalibrētus spektrus. Chloros versijā tiem ir divas funkcijas:

* **Autonoms spektrālais instruments** — reāllaika spektra diagrammas, kolorimetriskie dati un `.daq` ieraksti — viss no [cilnes „Gaismas sensori”](gui.md), [CLI](cli-quick-start.md) vai Python un SDK.
* **Uz leju vērsta starojuma avots atstarojuma aprēķināšanai** — apstrādes laikā Chloros interpolē jūsu `.daq` rādījumus katram attēlamekspozīcijas laika zīmogu un izmanto izmērīto lejupvērsto gaismu, lai pārvērstu kameras starojuma intensitāti atstarojumā (`--reflectance-source daq`); kalibrētajām joslām nav nepieciešams panelis uzņemšanas vietā.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Trīs modeļi, viens datu formāts

| Modelis | Pārraide | Atklāšana |
| --- | --- | --- |
| **DAQ-U** | USB (sērijas) | skenēšana pa sērijas portu |
| **DAQ-M** | Bluetooth Low Energy | BLE skenēšana pēc nosaukuma |
| **DAQ-E** | Ethernet (IPv4, ar PoE barošanu) | mDNS `_daq-e._tcp` (vārds `daq-e-<id>.local`) |

Visi trīs izmanto vienu un to pašu vadu protokolu un nodrošina identiskus datus:

* **135 punktu spektrs no 340 līdz 1010 nm ar 5 nm soli**, kā arī CIE XYZ trīsstimulu vērtības katrā kadrā.
* **Radiometriski kalibrēts spektrālais starojuma blīvums W/m²/nm** — katras ierīces rūpnīcas kalibrēšanas komplekts (kopā ar tās aktīvo vāka korekcijas profilu) tiek piemērots, pirms dati nonāk pie jums.
* Vienāds **`.daq` ierakstīšanas formāts** (SQLite fails). Turpmākā apstrāde ir identiska neatkarīgi no tā, kurš pārraides kanāls ir radījis failu.

Pārraides slāņi (USB seriālais, BLE, mDNS/zeroconf) ir iekļauti Chloros aizmugurējā modulī — nav nekas jāinstalē, lai sazinātos ar jebkuru no trim modeļiem, izmantojot grafisko lietotāja interfeisu vai CLI komandas.

***

## Kalibrētais diapazons: ziņots 340–1010 nm, kalibrēts ~374–974 nm

Sensors ziņo par pilnu 340–1010 nm diapazonu, taču NIST standartam atbilstošais radiometriskais pastiprinājums aptver aptuveni **374–974 nm**. Chloros noraida absolūtās atstarošanas dalīšanu jebkuram kameras diapazonam, kura spektrālā svara mazāk nekā puse atrodas šajā kalibrētajā diapazonā; izlaistais diapazons tiek ziņots ar izlaišanas iemeslu `dls-uncalibrated-band-<nm>`.

No piegādātajiem LATTICE filtru modeļiem tas attiecas tikai uz **F988**:

F988 atstarošanas koeficients tiek kalibrēts, izmantojot atstarošanas paneli uz vietas: josla atrodas ārpus DAQ gaismas sensora kalibrētā diapazona, tādēļ Chloros izmanto jūsu pēdējo paneļa uzņemumu un saglabā to starp paneļa novērojumiem.

Ja F988 reģistrācija tiek apstrādāta, izmantojot tikai pieejamos DAQ datus, Chloros noraida uz DAQ balstīto atstarojumu šim diapazonam ar izlaišanas iemeslu `dls-uncalibrated-band-988` — [atstarojuma paneļa darba plūsma](../calibration-targets.md) ir atbalstītais ceļš F988 gadījumā.

***

## Sensoru identifikatori

Katrs DAQ ziņo par stabilu sensora identifikatoru. Tā forma atšķiras atkarībā no modeļa:

| Modelis | Identifikatora forma | Piemērs |
| --- | --- | --- |
| DAQ-U | 5 oktetu ar defisi | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktetu garš ar defisi | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Sensora identifikators ir:

* ierakstīts katrā `.daq` failā, ko tas reģistrē,
* atslēga, ko Chloros izmanto, lai iegūtu šīs vienības rūpnīcas kalibrēšanas paketi,
* vērtība, ko jūs nododat `--sensor-id` komandās CLI un `pool-*`, un
* DAQ-E gadījumā arī tā mDNS uzņēmuma nosaukumu (`daq-e-def330.local`) — vērtību, ko pieņem `--eth-host`.

***

## Rūpnīcas kalibrēšana un mākonis

Katra DAQ vienība tiek individuāli rūpnīcas kalibrēta, izmantojot NIST izsekojamu radiometrisko ķēdi, un Chloros ielādē katras vienības kalibrēšanas paketi, kas ir saistīta ar tās sensora ID. Kalibrēšanas ziņojumu par katru vienību (PDF) var lejupielādēt no sensora iestatījumiem [cilnē „Gaismas sensori”](gui.md).

{% hint style="warning" %}
**DAQ-U un DAQ-M kalibrēšanai ir nepieciešama piekļuve mākonim.**Neviens no šiem modeļiem neuzglabā datus ierīcē: to rūpnīcas kalibrēšanas komplekti atrodas MAPIR mākonī un tiek lejupielādēti pēc sensora identifikatora (pēc tam tiek saglabāti vietējā kešatmiņā). Chloros ir nepieciešams interneta savienojums, lai no DAQ-U vai DAQ-M saņemtu kalibrētos W/m²/nm datus.**DAQ-E ir izņēmums** — tas glabā kalibrēšanas datus ierīcē.

<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

{% endhint %}***

## Kur tiek saglabāti ieraksti

| Virsma | Noklusējuma `.daq` galamērķis |
| --- | --- |
| Lietotāja saskarne — cilne „Gaismas sensori“ | `<project folder>/light_sensor/` (pabeigtie ieraksti tiek automātiski pievienoti projektam) |
| CLI — `daq pool-record` | `~/Documents/DAQ Live View/` datorā, kurā darbojas backend |

Katrā `.daq` faila nosaukumā ir iekļauts sensora ID un laika zīmogs.

***

## Šajā nodaļā

* [**Sadaļa „DAQ” programmā Chloros**](gui.md) — pilnīgs grafiskās lietotāja saskarnes apraksts: katra modeļa pieslēgšana, iestatījumi katram sensoram, spektra diagrammas, kolorimetriskie dati reāllaikā, divu sensoru atstarošanas koeficients un ierakstīšana.
* [**CLI ātrā uzsākšana (pool-\*)**](cli-quick-start.md) — DAQ sensoru vadība no `chloros-cli daq pool-*`, atbalstītais komandrindas ceļš.
* [**Ierobežojumu profili un kalibrētais diapazons**](caps-and-range.md) — kādi ierobežojumi pastāv katram modelim, kā tos deklarēt, kā arī detalizēts apraksts par kalibrēto spektrālo diapazonu.
* [**Ierakstīšana un .daq formāts**](recording.md) — `.daq` SQLite formāts un ierakstīšanas darba plūsmas.
* [**DAQ-E tīkla savienojumi un laika sinhronizācija**](ethernet-ptp.md) — DAQ-E datu pārraides režīmi un PTP laika sinhronizācija.
* [**Reflektances darba plūsmas**](reflectance.md) — DAQ lejupvērsto datu izmantošana reflektances aprēķināšanai.
* Pilnīgu dokumentāciju par karodziņu līmeni skatiet [CLI atsauces dokumentā](../reference/cli-reference.md) (sadaļā `chloros-cli daq`) un [SDK atsauces materiālu](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`), kas abi ir izstrādāti tā, lai tos varētu tieši izmantot AI palīgi.
