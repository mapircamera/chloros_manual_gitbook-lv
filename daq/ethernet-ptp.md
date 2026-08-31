# DAQ-E tīkla konfigurācija un laika sinhronizācija

> Sensora fiziskā tīkla konfigurācija — kabeļu savienojumi, PoE, IP adrešu piešķiršana un paša ierīces tīkla iestatījumi — ir aprakstīta **[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Šajā lapā ir aprakstīta Chloros puse: savienošana, laika sinhronizācija un rīcība, ja atklāšanas process nedod rezultātus.

DAQ-E ir DAQ ģimenes Ethernet loceklis: to baro ar PoE, atrod ar mDNS (pakalpojums `_daq-e._tcp`) un to var adresēt ar hostvārdu, kas atvasināts no tā sensora ID — `daq-e-<6 hex>.local`, piemēram, `daq-e-def330.local`. Šajā lapā ir aprakstīts, kā tas pārraida datus tīklā un kā tas piedalās PTP laika sinhronizācijā.

## Pārraides režīmi

| Režīms | Galapunkts | Lietotāji | Piezīmes |
| --- | --- | --- | --- |
| **Multicast** (noklusējums) | UDP `239.10.10.10:5002` | Jebkurš skaits tajā pašā LAN saņem to pašu datu plūsmu | Katra datagramma tiek validēta ar CRC-16/CCITT |
| **Raw** | TCP ports `5000` | Tieši viens klients (ekskluzīvi) | Pilnībā saderīgs ar DAQ-U baitu līmenī |

Chloros pēc noklusējuma izmanto multiraidi, kas ļauj GUI, CLI un SDK vienlaikus novērot vienu sensoru.

## Tīkla prasības

* **Viena un tā pati apraides domēna.** Datoram, kurā darbojas Chloros, jāatrodas tajā pašā L2 tīkla segmentā, kurā atrodas sensors — mDNS atklāšana nešķērso maršrutētājus.
* **Windows ugunsmūra brīdinājums: apstipriniet to.** Pirmoreiz, kad Chloros piesaista multiraides soketus, Windows Defender uzdod šo jautājumu vienreiz. Atļaujot to, tiek segti DAQ-E dati (UDP 5002), mDNS (UDP 5353) un PTP (UDP 319/320). Linux gadījumā tas notiek klusi.
* **PoE barošana, nav statusa LED indikatora.** DAQ-E ierīcei nav sava LED indikatora — pārbaudiet barošanu, izmantojot savienojuma/PoE indikatoru uz komutatora vai injektora porta, un pēc ieslēgšanas pagaidiet dažas sekundes, lai ierīce uzsāktu darbu un pievienotos tīklam.

## Savienošana

**GUI:** cilne „Gaismas sensori“ → „Savienot sensoru“ → ierīces tips „DAQ-E (Ethernet)“. Atklāšana notiek tikai tad, kamēr ekrānā ir redzams savienošanas dialoglodziņš (mDNS pārlūkošana un ARP skenēšana uz Windows), atkārtojoties ik pēc 15 sekundēm; pogas „Atjaunināt” nospiešana veic tūlītēju atkārtotu skenēšanu. Atklātie sensori parādās nolaižamajā izvēlnē; pirmais atklātais sensors tiek automātiski atlasīts.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (darbojas aizmugurējā programma):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

### Datori ar vairākiem tīkla interfeisiem un pirmā savienošanās pēc sistēmas uzsākšanas

Datoros ar vairāk nekā vienu aktīvu tīkla interfeisu **pirmais** `pool-connect --eth` pēc sistēmas uzsākšanas var būt tukšs, pat ja sensors darbojas pareizi — atklāšanas skenēšana var nepamanīt interfeisu, uz kura atrodas sensors, kamēr ARP kešatmiņa vēl nav iesildījusies. Uzticams risinājums ir izlaist atklāšanu un adresi norādīt tieši:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` pieņem mDNS uzņēmuma nosaukumu vai IP adresi, vienmēr mērķē uz pareizo sensoru un ir ieteicamais veids skriptiem un bezgalvas instalācijām. GUI vidē izmantojiet savienošanās dialoglodziņa pogu „Atjaunot” un ļaujiet notikt atkārtotai skenēšanai.

## Ierīces iestatījumi un programmaparatūra

Pats sensors glabā tīkla iestatījumus — statisko IP vai DHCP + lokālo adresāciju, ierīces nosaukumu, automātisko datu plūsmu sistēmas uzsākšanas brīdī, OTA paroli. Šie ierīces puses iestatījumi nav pieejami kā komandas piegādātajā CLI; tos pārvalda caur Chloros grafisko lietotāja saskarni, kur tie tiek parādīti, vai ar MAPIR atbalstu.

**Programmatūras atjauninājumi ir integrēti lietotāja saskarnē.**Ja pieslēgtajam DAQ-E ir programmatūra, kas ir vecāka par attēlu, kas iekļauts jūsu Chloros versijā, tā sensora rindā parādās dzintara krāsas**Pieejams atjauninājums** ikona, un iestatījumu logā ir pieejama<version>

pogu</version> „Atjaunināt uz<version>

„. Atjauninājums tiek nosūtīts pa tīklu aptuveni 30 sekundēs; sensors automātiski pārstartējas un atkārtoti izveido savienojumu, un pārtraukta datu pārsūtīšana atstāj pašreizējo programmatūru neskartu.

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP laika sinhronizācija

DAQ-E programmaparatūras versija v1.2.0+ darbojas IEEE 1588 PTPv2 protokolā kā parasts (tikai pakļautais) pulkstenis. **Chloros galvenā ierīce ir PTP galvenais pulkstenis** — katrs DAQ-E un katra LATTICE kamera lokālajā tīklā (LAN) darbojas kā tā pakļautais pulkstenis 0. domēnā, uzturot visu ierīču laika zīmogus ar ~1 ms pielaidi. Tieši šis kopīgais pulkstenis nodrošina, ka DAQ nolasījumu laika zīmes saskan ar kameru ekspozīcijām (skatīt [Ierakstīšana un .daq formāts](recording.md)).

Pārbaudiet sinhronizāciju no CLI:

| Komanda | Rāda |
| --- | --- |
| `chloros-cli time-sync status` | Galvenā servera stāvoklis, BMCA prioritātes, pulksteņa identifikators |
| `chloros-cli time-sync peers` | Visas redzamās pakārtotās ierīces (DAQ-E sensori + LATTICE kameras) |
| `chloros-cli time-sync cameras` | PTP darbības stāvoklis katrai kamerai (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Grandmaster procesa pārstartēšana |

GUI vidē DAQ-E iestatījumu logā tiek parādīta reāllaika **PTP Sync** rinda ar sensora pašreizējo PTP stāvokli.

Sīkāka informācija lietotājiem, kam nepieciešama stingra saskaņošana:

* Katram pārraidītajam datagramam ir karogu lauks; **

2. bits ir iestatīts rāmjos, kuru laika zīmogs ir PTP sinhronizēts**. Pārraides kanāliem, kam nepieciešama stingra kameras/DAQ saskaņošana, jāizmanto šis bits kā filtrēšanas kritērijs.
* Pirms sinhronizētas uzņemšanas pārliecinieties, ka sensors parādās `chloros-cli time-sync peers`. (MAPIR iekšējie tiešās aparatūras rīki var arī vadīt ierakstīšanu pēc PTP sinhronizācijas, izmantojot `--wait-ptp` karodziņu, kas gaida līdz 15 s, kamēr sensors sasniedz SLAVE stāvokli; šis rīks nav iekļauts piegādātajā CLI.)
* Kamēr PTP aktīvi darbojas kā pakļautais, sensors nepieņem manuālus pulksteņa signālu pārraidījumus („PTP nodrošina pulksteni”). Tas ir paredzēts — uzticieties PTP.

## Linux piezīmes

* **PTP instalācijas laikā ir nepieciešams `libcap2-bin`.** `.deb` postinst piešķir `cap_net_bind_service=+ep` tiesības uz `/usr/lib/chloros/chloros-backend`, lai tas varētu piesaistīt PTP portus 319/320 bez root tiesībām. Ja trūkst `libcap2-bin`, šis solis tiek izlaists un PTP nevarēs sākt darbu. Risinājums:

  ```bash
  sudo apt install libcap2-bin
  sudo apt reinstall chloros
  ```

* **Jetson / Raspberry Pi bez grafiskās saskarnes:** pirmajā instalācijā tiek ģenerēta systemd vienība `chloros-backend.service`, taču tā nav ieslēgta. Lai nodrošinātu pastāvīgi darbojošos PTP (un DAQ pieejamību) bez grafiskās saskarnes:

  ```bash
  sudo systemctl enable --now chloros-backend.service
  ```

  Bez tā PTP darbojas tikai tad, kamēr ir atvērta Chloros grafiskā saskarne.

## Problēmu novēršana: „Nav atrastas DAQ-E ierīces”

| Pārbaude | Sīkāka informācija |
| --- | --- |
| Barošana | Sensora LED nedeg — pārbaudiet slēdža/injektora porta PoE un savienojuma indikatorus; pēc ieslēgšanas pagaidiet dažas sekundes |
| Raidīšanas domēns | Hosts un sensors atrodas vienā L2 segmentā; mDNS neveic maršrutēšanu |
| Windows ugunsmūris | Pirmajā palaišanas reizē apstipriniet Defender pieprasījumu (UDP 5002, 5353, 319/320) |
| Dators ar vairākiem tīkla kartēm | Pirmajā atklāšanas procesā pēc sistēmas uzsākšanas sensors var netikt atklāts — izveidojiet savienojumu ar `--eth-host <ip-or-hostname>` |
| Atkārtota skenēšana GUI | Atklāšana notiek tikai tad, kamēr ir atvērts savienojuma dialoglodziņš; izmantojiet pogu „Atjaunināt“ |</version>
