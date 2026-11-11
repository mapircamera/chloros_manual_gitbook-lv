# DAQ-E tīkla konfigurācija un laika sinhronizācija

> Sensora fiziskā tīkla konfigurācija — kabeļu pieslēgšana, PoE, IP adrešu piešķiršana un paša ierīces tīkla iestatījumi — ir aprakstīta **[DAQ lietotāja rokasgrāmatā](https://mapir.gitbook.io/daq/daq-e/network-setup)**. Šajā lapā ir aprakstīta Chloros

puse: savienošana, laika sinhronizācija un rīcība, ja atklāšanas process nedod rezultātus.

DAQ-E ir DAQ ģimenes Ethernet ierīce: tā tiek barota ar PoE, tiek atklāta, izmantojot mDNS (pakalpojums `_daq-e._tcp`), un to var adresēt, izmantojot hostvārdu, kas atvasināts no sensora ID — `daq-e-<6 hex>.local`, piemēram, `daq-e-def330.local`. Šajā lapā ir aprakstīts, kā ierīce pārraida datus tīklā un kā tā piedalās PTP laika sinhronizācijā.

## Pārraides režīmi

| Režīms | Galapunkts | Lietotāji | Piezīmes |
| --- | --- | --- | --- |
| **Multicast** (noklusējums) | UDP `239.10.10.10:5002` | Jebkurš skaits tajā pašā LAN saņem to pašu datu plūsmu | Katra datagramma tiek validēta ar CRC-16/CCITT |
| **Raw** | TCP ports `5000` | Tieši viens klients (ekskluzīvi) | Pilnībā baitu saderīgs ar DAQ-U |

Chloros

pēc noklusējuma izmanto multicast, kas ļauj GUI, CLI

un SDK

vienlaikus novērot vienu sensoru.

## Tīkla prasības

* **Viena un tā pati apraides domēna.** Datoram, kurā darbojas Chloros

, jāatrodas tajā pašā L2 tīkla segmentā, kurā atrodas sensors — mDNS atklāšana nešķērso maršrutētājus.
* **Ugunsmūris.** Windows

vidē instalētājs katrā tīkla profilā pievieno **Chloros

Backend (UDP-In)**ugunsmūra noteikumu backendam, kas aptver DAQ-E datus (UDP 5002), mDNS (UDP 5353) un PTP (UDP 319/320), kā arī**„Chloros

Backend (TCP-In)“** noteikumu, kas ierobežots ar lokālo apakštīklu, lai nodrošinātu DAQ-E savienojumu ar datoru programmatūras atjaunināšanas laikā. Linux

vidē `.deb` atver UDP portus `ufw` vai `firewalld`, ja šī ugunsmūra ir aktīva instalācijas brīdī. Trešās puses ugunsmūrim vai vēlāk aktivizētam ugunsmūrim ir jāatļauj tie paši porti.
* **PoE barošana, nav statusa LED.** DAQ-E ierīcei nav savas LED — pārbaudiet barošanu, izmantojot savienojuma/PoE indikatoru uz komutatora vai injektora porta, un pēc ieslēgšanas pagaidiet dažas sekundes, lai ierīce uzsāktu darbu un pievienotos tīklam.

## Savienošana

**GUI:** cilne „Gaismas sensori“ → „Savienot sensoru“ → ierīces tips „DAQ-E (Ethernet)“. Atklāšana notiek tikai tikmēr, kamēr ekrānā ir redzams savienošanas dialoglodziņš (mDNS pārlūkošana un ARP skenēšana Windows

vidē), atkārtojoties ik pēc 15 sekundēm; pogas „Atjaunināt“ nospiešana veic tūlītēju atkārtotu skenēšanu. Atrastie sensori parādās nolaižamajā izvēlnē; pirmais atklātais sensors tiek automātiski atlasīts.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (darbojas backend):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

DAQ-E, kas jau atrodas pūlā, tiek atpazīts gan pēc tā uzņēmuma nosaukuma, gan pēc IP adreses: ja to atkārtoti savieno ar citu adresi, tiek atgriezts esošais ieraksts pūlā, nevis atvērta ierīce otrreiz.

### Datori ar vairākiem tīkla interfeisiem un pirmā savienošanās pēc sistēmas uzsākšanas

Datoros ar vairāk nekā vienu aktīvu tīkla interfeisu **pirmais** `pool-connect --eth` pēc sistēmas uzsākšanas var būt tukšs, pat ja sensors darbojas pareizi — atklāšanas pārlūkošana var nepamanīt interfeisu, kurā atrodas sensors, kamēr ARP kešatmiņa vēl nav iesilusi. Uzticams risinājums ir izlaist atklāšanu un adresi norādīt tieši:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host` pieņem mDNS uzņēmuma nosaukumu vai IP adresi, vienmēr mērķē uz pareizo sensoru un ir ieteicamais risinājums skriptiem un bezgalvas instalācijām. Grafiskajā lietotāja saskarnē izmantojiet savienošanās dialoglodziņa pogu „Atjaunināt” un ļaujiet notikt atkārtotai skenēšanai.

## Ierīces iestatījumi un programmaparatūra

Sensors pats saglabā savus tīkla iestatījumus: DHCP (rūpnīcas iestatījums, kas, ja neviens DHCP serveris neatbild, pārslēdzas uz 169.254.x.x lokālo adresi) vai statisku adresi. Tas arī saglabā informāciju par to, vai sensora ieslēgšanas brīdī tiek uzsākta datu pārraide. Tos var mainīt grafiskajā lietotāja saskarnē (sensora iestatījumu ratiņš, **Tīkls**:**Mainīt…**,**Sākt datu pārraidi pēc ieslēgšanas**,**Pārstartēt sensoru**; skatiet [cilni „DAQ”](gui.md)), vai arī izmantojot CLI

un SDK

ar komandām `pool-set-network`, `pool-set-auto-stream` un `pool-reboot` (`DAQSensorSession.set_network()`, `set_auto_stream()`, `reboot()`; skatiet [CLI

atsauci](../reference/cli-reference.md#daq-e-device-settings)). Sensors tiek pārstartēts, lai piemērotu tīkla izmaiņas, un Chloros

to atkārtoti pieslēdz. Tīklā, kurā darbojas Chloros

, ir jābūt statiskai adresei: Chloros

noraida adresi, kuru pēc pārstartēšanas nevar sasniegt. Sensora ID un tā `daq-e-<id>.local` uzņēmuma nosaukums nekad nemainās. Jaunums versijā 1.3.0.

**Programmatūras atjauninājumi ir integrēti lietotāja saskarnē.** Ja pieslēgtajam DAQ-E ir programmatūra, kas ir vecāka par attēlu, kas iekļauts jūsu Chloros

versijā, sensora rindā parādās dzintara krāsas **Atjauninājums pieejams** ikona, un iestatījumu logā ir pieejama <version>poga</version>

„Atjaunināt uz <version>„”. Atjauninājums tiek nosūtīts pa tīklu aptuveni 30 sekundēs; sensors automātiski pārstartējas un atkārtoti izveido savienojumu, un pārtraukta datu pārsūtīšana atstāj pašreizējo programmatūru neskartu. No CLI vai SDK, kā arī Linux vidē, kurā nav grafiskās lietotāja saskarnes, palaidiet komandu `chloros-cli daq pool-update-firmware --sensor-id <id>` (`DAQSensorSession.update_firmware()`).

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP laika sinhronizācija

DAQ-E programmaparatūras versija v1.2.0+ darbojas IEEE 1588 PTPv2 standartā kā parasts (tikai pakļautais) pulkstenis. **Chloros saimniekdatoram ir PTP galvenā pulksteņa loma** — katrs DAQ-E un katra LATTICE kamera lokālajā tīklā (LAN) darbojas kā tā pakļautais pulkstenis 0. domēnā, uzturot visu ierīču laika zīmogu precizitāti ar ~1 ms pielaidi. Tieši šis kopīgais pulkstenis nodrošina, ka DAQ rādījumu laika zīmogus var saskaņot ar kameras ekspozīcijām (skatīt [Ierakstīšana un .daq formāts](recording.md)).

Pārbaudiet sinhronizāciju no CLI:

| Komanda | Rāda |
| --- | --- |
| `chloros-cli time-sync status` | Galvenā datora „grandmaster” stāvoklis, BMCA prioritātes, pulksteņa identifikators un `health`: `OK` vai `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` ar `health_detail`, kas norāda, kāpēc tā paketes nesasniedz tīklu |
| `chloros-cli time-sync peers` | Visi redzamie pakļautie ierīces (DAQ-E sensori + LATTICE kameras) |
| `chloros-cli time-sync cameras` | PTP darbības stāvoklis katrai kamerai (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Grandmaster procesa pārstartēšana |

GUI vidē DAQ-E iestatījumu logā tiek parādīta reāllaika **PTP Sync** rinda ar sensora pašreizējo PTP stāvokli.**DAQ-E ar atslēgtu PTP paliek atslēgts.** Ierīce saglabā savu PTP ieslēgšanas/izslēgšanas iestatījumu savā atmiņā, tādēļ ierīce, kuras PTP bija atslēgts, paliek atslēgta arī pēc atkārtotas savienošanās vai strāvas cikla, un tās laika zīmogi netiek sinhronizēti ar PTP. `chloros-cli daq pool-list` atzīmē šādu ierīci (`!! PTP is switched OFF on this unit …`) un izdrukā komandu, kas to labot:

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

Chloros nekad pats neieslēdz ierīci atpakaļ, jo izslēgts stāvoklis var būt apzināts.

Sīkāka informācija lietotājiem, kam nepieciešama stingra saskaņošana:

* Katram straumētajam datagramam ir karogu lauks; **

2. bits ir iestatīts rāmjos, kuru laika zīmogs ir sinhronizēts ar PTP**. Caurvadiem, kam nepieciešama stingra kameras/DAQ saskaņošana, jāizmanto šis bits kā filtrēšanas kritērijs.
* Pirms sinhronizētas uzņemšanas pārliecinieties, ka sensors parādās `chloros-cli time-sync peers` un ka `chloros-cli daq pool-list` neatzīmē tā PTP kā izslēgtu. (MAPIR iekšējie tiešās aparatūras rīki var arī vadīt ierakstīšanu pēc PTP sinhronizācijas, izmantojot `--wait-ptp` karodziņu, kas gaida līdz 15 sekundēm, kamēr sensors sasniedz SLAVE stāvokli; šie rīki nav iekļauti piegādātajā CLI.)
* Kamēr PTP aktīvi darbojas kā pakļautais, sensors nepieņem manuālus pulksteņa sinhronizācijas signālus („PTP nodrošina pulksteni”). Tas ir paredzēts — uzticieties PTP.

## Piezīmes par Linux

* **PTP porti bez root tiesībām.** PTP izmanto UDP 319/320. `.deb` raksta `/etc/sysctl.d/60-chloros-ptp.conf` (`net.ipv4.ip_unprivileged_port_start = 319`), tādējādi aizmugurējais modulis tos piesaista, darbojoties kā jūsu lietotājs. Sākot ar versiju 1.3.0, pakete neuzstāda nekādas failu tiesības (`setcap`) aizmugurē un tai nav nepieciešams `libcap2-bin`; nepievienojiet tiesības paši, jo tad izpakotais aizmugurējs nedarbosies. Ja PTP nevar piesaistīt 319. portu, skatiet [Linux instalācija](../linux/linux-installation.md#ptp-will-not-start--cannot-bind-port-319).

* **Jetson bez monitora / Linuxa serveris:** pakete instalē aizmugurējā procesa pakalpojumus, bet nevienu no tiem neaktivizē. Lai nodrošinātu pastāvīgi darbojošos PTP (un DAQ pieejamību) bez nepieciešamības kādam izpildīt komandu, aktivizējiet pakalpojumu kā savs lietotājs:

  ```bash
  sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
  ```

  Bez tā PTP darbojas tikai tad, ja kaut kas ir palaidis aizmugurē darbojošos pakalpojumu — komandu `chloros-cli` vai SDK skriptu. Skatīt [Linux instalācija](../linux/linux-installation.md#always-on-ptp-for-headless-hosts), lai uzzinātu par root `chloros-backend.service` un to, kā no tā pāriet.

## Problēmu novēršana: „Nav atrastas DAQ-E ierīces”

| Pārbaude | Detalizēta informācija |
| --- | --- |
| Barošana | Sensora LED nedeg — pārbaudiet slēdža/injektora porta PoE un savienojuma indikatorus; pēc ieslēgšanas pagaidiet dažas sekundes |
| Pārraides domēns | Hosts un sensors atrodas vienā L2 segmentā; mDNS neveic maršrutēšanu |
| Ugunsmūris | Windows: instalētāja **Chloros Backend (UDP-In)** noteikums ļauj ienākt DAQ-E plūsmai (UDP 5002), mDNS (5353) un PTP (319/320); trešās puses ugunsmūrim ir nepieciešami tie paši iestatījumi. Linux: `.deb` atver šos portus `ufw` / `firewalld` tikai tad, ja ugunsmūris bija aktīvs instalēšanas brīdī — atļaujiet tos pašrocīgi, ja ugunsmūris tiek ieslēgts vēlāk |
| Dators ar vairākiem tīkla interfeisiem | Atklāšana pārlūko mDNS, izmantojot ARP kā rezerves risinājumu; `--eth-host <ip-or-hostname>` izlaiž atklāšanu un izveido savienojumu tieši |
| Atkārtota skenēšana ar GUI | Atklāšana darbojas tikai tad, kamēr ir atvērts savienojuma dialoglodziņš; izmantojiet pogu „Atjaunināt“ |</version>
