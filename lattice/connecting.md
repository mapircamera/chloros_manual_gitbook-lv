# Kameru pieslēgšana

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Sadaļa „Kameras”, pirms kaut kas ir pieslēgts</p></figcaption></figure>Chloros automātiski atklāj LATTICE kameras savienojumā — no GUI cilnes „Kameras”, no `chloros-cli lattice` vai no Python SDK. Kameras modeļa virkne nosaka visu turpmāko darbību: Chloros nosaka sensora profilu, joslu izkārtojumu un rūpnīcas kalibrāciju, izmantojot kameras `DeviceUserID` + `DeviceSerialNumber`, tādēļ **katrai kamerai nav nekas jākonfigurē**.

Pirms savienošanas pārliecinieties, ka ir konfigurēts galvenais tīkls — lokālā adresācija, jumbo rāmji un, ja izmantojat masīvus, tīkla kartes uztveršanas bufera iestatījumi. Tā ir aparatūras konfigurācija, kas aprakstīta LATTICE rokasgrāmatā: [**Tīkla konfigurācija**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Savienošanās no grafiskās lietotāja saskarnes

Atveriet cilni **Kameras**Chloros sānjoslā (aparatūras cilnes parādās, kad ir pabeigta aizmugures sistēmas palaišana) vai izmantojiet galveno izvēlni →**Savienot ar kameru**. Abos gadījumos atveras dialoglodziņš**Savienot kameru(-as)**.

### Dialoglodziņš **Pievienot kameru(-as)**Dialoglodziņš, tiklīdz tas tiek atvērts, sāk skenēt tīklu („Skenē tīklu...”) un uzskaita visas atrastās kameras. Katrā rindā ir redzams kameras**modelis**(piem., `LATT-M3M-L41-F550`),**sērijas numurs**un**IP adrese**.

* **Noklikšķiniet uz rindas, lai to atlasītu**(tiek izcelta zaļā krāsā). Jūs varat atlasīt**vairākas kameras** un savienot tās vienā reizē — Chloros savieno tās secīgi.
* Rindas ar atzīmi **„Pieslēgts“** jau ir pieslēgtas un tās nevar atkārtoti atlasīt.
* Rindas ar atzīmi **„Masīvā“** pieder pašlaik pieslēgtam kameru masīvam. Lai izmantotu šo kameru atsevišķi, vispirms atvienojiet masīvu.
* **Pievienot** — pievieno izvēlēto kameru(-as); ja ir izvēlētas vairākas kameras, pogā uzrāda to skaitu, piemēram, „Pievienot (3)”.
* **Atkārtota skenēšana** — atkārtoti veic kameru meklēšanu.
* **Aizvērt** — aizver dialoglodziņu.
* Ja skenēšana beidzas bez rezultātiem, dialoglodziņā parādās paziņojums **„Tīklā nav atrastas kameras”** — skatiet [Problēmu novēršana](connecting.md#troubleshooting) zemāk.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialoglodziņš „Pievienot kameru(-as)” — šeit parādīts gadījumā, ja tīklā nav nevienas kameras</p></figcaption></figure>### Pirmā savienošana: kalibrēšanas paketes lejupielāde

**Pirmoreiz**, kad konkrētā kamera tiek savienota ar ierīci, Chloros lejupielādē kameras rūpnīcas kalibrēšanas paketi (\~3,8 MB) no pašas kameras, izmantojot GigE. Kamēr šis process noris, dialoglodziņā parādās zaļš panelis**„Lejupielādē kalibrēšanas datus no kameras“**ar progresijas joslu katram sērijas numuram — rēķinieties ar aptuveni**70 sekundēm** katrai kamerai. Pakete tiek saglabāta kešatmiņā uz servera, tādēļ turpmākajiem šīs pašas kameras pieslēgumiem lejupielāde tiek pilnībā izlaista (un panelis vairs netiek parādīts).

### Sistēmas analīze

Dialoglodziņā esošā poga **„Sistēmas analīze”** pārbauda serveri un tīklu (procesā tiek parādīts uzraksts „Analizē...” un tiek izveidots diagnostikas ziņojums):

* **Serveris** — procesora kodoli un RAM; GPU nosaukums un atmiņa vai „GPU: nav atklāts”.
* **Tīkla interfeisi** — katra tīkla kartes nosaukums, savienojuma ātrums, MTU (ar atzīmi „jumbo”, ja tā ir aktīva), augšup/lejup stāvoklis un informācija par to, vai tā atrodas USB šīnā.
* **Kameras**— sērijas numurs, modelis, IP adrese un**uz kāda tīkla interfeisa katra kamera ir pieslēgta**.
* **Veiktspēja** — pašreizējais un ideālais kadru skaits sekundē (fps) katrai kamerai atbilstoši pikseļu formātam, ar zaļu rindu „Potenciāls: iespējams N× uzlabojums”, ja ideālais rādītājs pārsniedz pašreizējo.
* **Brīdinājumi un numurēti ieteikumi** — vai arī „Sistēma darbojas labi, ņemot vērā pašreizējo kameru skaitu”, ja nav nekas jālabo.

Palaidiet to ikreiz, kad atklāšana vai straumēšana darbojas negaidīti — tā identificē lielāko daļu tīkla kartes problēmu (nepareizs MTU, kamera pieslēgta nepareizajai saskarnes kartes interfeisai, USB adaptera ierobežojumi), neizejot no dialoglodziņa.

### Kameru masīva savienošana

Lai savienotu divas vai vairākas kameras kā **sinhronizētu masīvu**, izmantojiet masīva savienošanas vedni (**Connect Camera Array**): tas palīdz izvēlēties galveno/pakārtoto kameru (iepriekš aizpildīts ar GPIO vadu pārbaudi), izvēlēties attēlošanas režīmu (atsevišķi vai apvienoti laukumi) un konfigurēt masīva iestatījumus, pirms apstiprināt izmaiņas, parādot reāllaika prognozi par sasniedzamo kadru skaitu sekundē (fps) un vadu joslas platumu. Vadītājs un masīva darbplūsmas ir aprakstītas šīs rokasgrāmatas sadaļā par daudzkameru masīviem; CLI ekvivalents ir „LATTICE kameras pirmās savienošanas darbplūsma” [CLI atsauces dokumentā](../reference/cli-reference.md).

## Savienošanās no CLI un SDK

Lai piekļūtu no CLI un SDK, ir nepieciešams maksas pakalpojuma līmenis Chloros+ un jābūt pieteicies; tas tiek nodrošināts servera pusē (`401 AUTH_REQUIRED`, ja nav pieteicies, `403 PLAN_UPGRADE_REQUIRED` bezmaksas līmenī).

```bash
# List cameras on the network (vendor, model, serial, IP, MAC)
chloros-cli lattice info

# Single-camera smoke test: capture one frame (saves every applicable export type)
chloros-cli lattice capture -o output/

# Connect a synchronized array — same smart-prep flow as the GUI
chloros-cli lattice array-connect --serials 213800234,214000533
```

```python
import chloros_sdk

# Persistent live-camera session through the backend
with chloros_sdk.connect_camera("213800234") as cam:
    ...

# Array session (smart-prep: network probe, tier auto-pick, PTP, AE seeding, trigger config)
with chloros_sdk.connect_array(["213800234", "214000533"]) as array:
    ...
```

Pilnīgas parakstīšanas, opcijas un datu ieguves darbplūsmas: [CLI Atsauce](../reference/cli-reference.md) § `chloros-cli lattice`, [SDK Atsauce](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Kā tiek veikta kalibrēšana savienojuma izveides brīdī

Katrā LATTICE kamerā **kamerā pašā** ir iebūvēts rūpnīcas kalibrēšanas komplekts, un Chloros, izveidojot savienojumu, pārbauda arī MAPIR mākoni:

| Situācija   | Ko izmanto Chloros                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tiešsaistē**|**Jaunākā kalibrēšana, kas publicēta šim sērijas numuram** — mākoņa kopija ir prioritāra salīdzinājumā ar kamerā esošo kopiju. Tādējādi kamera, kas ir pārkalibrēta vai atjaunināta ar MAPIR, atjauninās automātiski; lietotāja rīcība nav nepieciešama. |
| **Bezsaistē**|**Kamerā esošais kalibrācijas pakotnes** saturs paliek nemainīgs. Darba plūsmas, kas darbojas pilnībā bezsaistē, turpina darboties; tās vienkārši neuzņem jaunākos kalibrējumus, kamēr kamera nav vismaz reizi bijusi tiešsaistē (vai nav veikta rūpnīcas iestatījumu atjaunošana).                                                  |

Uzņemšanas brīdī faktiski piemērotie koeficienti tiek **fiksēti katra attēla XMP metadatos**. Vēlāks kalibrācijas atjauninājums nekad nemainīs jau uzņemtos attēlus bez jūsu ziņas — pārstrādājot vecu uzņēmumu, tiek izmantoti koeficienti, kas ierakstīti tā XMP metadatos, nevis tie, kas šodien ir jaunākie.

## Problēmu novēršana

* **&quot;Tīklā nav atrastas kameras”**— pārbaudiet lokālo savienojuma konfigurāciju sadaļā [Tīkla iestatījumi](https://mapir.gitbook.io/lattice-camera/setup/network-setup): statiskais tīkla interfeisa (NIC) adrese ir `169.254.x.x/16`, kamerām jāatrodas tajā pašā tīkla segmentā, DHCP/vārteja nav paredzēta. Tad izmantojiet**„Analyze System“**savienojuma dialoglodziņā, lai pārbaudītu, uz kura tīkla kartes katra kamera ir (vai nav) redzama. Pēc jebkādām kabeļu vai tīkla kartes izmaiņām veiciet**atkārtotu skenēšanu**.
* **Iepriekš darbojusies iekārta vairs nevēlas izveidot savienojumu** (masīva paneļa vārti ar `FRAMES WILL DROP` / `Reduce ROI to enable`) — tīkla kartes draivera atjauninājums nemanāmi ir atiestatījis uztveršanas gredzena iestatījumus. Piemērojiet tos atkārtoti vai palaidiet komandu `chloros-cli lattice network --fix` no termināļa ar paaugstinātiem tiesībām; skatiet [Tīkla iestatīšana](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera rāda „In Array”** — tā pieder pie savienotas masīva sesijas. Atvienojiet masīvu, lai kameru izmantotu atsevišķi.
