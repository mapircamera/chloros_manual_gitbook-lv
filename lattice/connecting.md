# Kameru pieslēgšana

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Lapa „Kameras”, pirms kaut kas ir pieslēgts</p></figcaption></figure>

Chloros automātiski atrod LATTICE kameras savienojumā — no GUI cilnes „Kameras”, no `chloros-cli lattice` vai no Python SDK. Kameras modeļa virkne nosaka visu turpmāko darbību: Chloros nosaka sensora profilu, joslu izkārtojumu un rūpnīcas kalibrāciju, izmantojot kameras `DeviceUserID` + `DeviceSerialNumber`, tādēļ **nav nekas jākonfigurē katrai kamerai atsevišķi**.

Pirms savienošanas pārliecinieties, ka ir konfigurēts uzņēmējtīkls — lokālā adresācija, jumbo rāmji un, ja izmantojat kameru masīvus, tīkla kartes uztveršanas bufera iestatījumi. Tā ir aparatūras konfigurācija, kas aprakstīta LATTICE rokasgrāmatā: [**Tīkla konfigurācija**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## Savienošanās no grafiskās lietotāja saskarnes

Atveriet cilni **Kameras** Chloros sānjoslā (aparatūras cilnes parādās, kad ir pabeigta aizmugurējās sistēmas palaišana) vai izmantojiet galveno izvēlni → **Savienot ar kameru**. Abos gadījumos atveras dialoglodziņš**Savienot kameru(-as)**.

### Dialoglodziņš „Pievienot kameru(-as)”

Dialoglodziņš sāk skenēt tīklu brīdī, kad tas tiek atvērts („Skenē tīklu...”), un uzskaita visas atrastās kameras. Katrā rindā ir norādīts kameras **modelis**(piem., `LATT-M3M-L41-F550`),**sērijas numurs**un**IP adrese**.

* **Noklikšķiniet uz rindas, lai to atlasītu**(tiek izcelta zaļā krāsā). Jūs varat atlasīt**vairākas kameras** un savienot tās vienā reizē — Chloros tās savieno secīgi.
* Rindas ar atzīmi **„Savienots”** jau ir savienotas un tās nevar atlasīt atkārtoti.
* Rindas ar atzīmi **„Masīvā”** pieder pašlaik savienotam kameru masīvam. Lai izmantotu šo kameru atsevišķi, vispirms atvienojiet masīvu.
* **Pievienot** — pievieno atlasīto(-ās) kameru(-as); ja ir atlasītas vairākas kameras, pogā uzrāda to skaitu, piemēram, „Pievienot (3)”.
* **Atkārtota skenēšana** — atkārtoti veic meklēšanu.
* **Aizvērt** — aizver dialoglodziņu.
* Ja skenēšana beidzas bez rezultātiem, dialoglodziņā parādās paziņojums **„Tīklā nav atrastas kameras“** — skatiet [Problēmu novēršana](connecting.md#troubleshooting) zemāk.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Dialoglodziņš „Pievienot kameru(-as)” — šeit parādīts gadījumā, ja tīklā nav nevienas kameras</p></figcaption></figure>

### Pirmā savienošana: kalibrēšanas paketes lejupielāde

**Pirmoreiz**, kad konkrētā kamera tiek savienota ar ierīci, Chloros caur GigE no pašas kameras lejupielādē kameras rūpnīcas kalibrēšanas paketi (\~3,8 MB). Kamēr tas notiek, dialoglodziņā parādās zaļš panelis **„Lejupielādē kalibrēšanas datus no kameras”**ar atsevišķu progresijas joslu katram sērijas numuram — rēķinieties ar aptuveni**70 sekundēm** katrai kamerai. Ja kameru skaits pārsniedz sešas, progresijas rindas ritina pa paneli. Pakete tiek saglabāta cache atmiņā uz servera, tādēļ, vēlāk pieslēdzot to pašu kameru, lejupielāde tiek pilnībā izlaista (un panelis vairs netiek parādīts).

### Sistēmas analīze

Dialoglodziņā esošā poga **„Analizēt sistēmu”** pārbauda serveri un tīklu (izpildes laikā uzraksts ir „Analizē...” ) un ģenerē diagnostikas ziņojumu:

* **Serveris** — procesora kodoli un RAM; GPU nosaukums un atmiņa, vai „GPU: nav atklāts”.
* **Tīkla interfeisi** — katra tīkla kartes nosaukums, savienojuma ātrums, MTU (ar atzīmi „jumbo”, ja tā ir aktīva), augšup/lejup stāvoklis un informācija par to, vai tā atrodas USB šīnā.
* **Kameras**— sērijas numurs, modelis, IP adrese un**uz kāda tīkla interfeisa katra kamera ir pieslēgta**.
* **Veiktspēja** — pašreizējais un ideālais kadru skaits sekundē (fps) katrai kamerai attiecīgajam pikseļu formātam, ar zaļu rindu „Potenciāls: iespējams N× uzlabojums”, ja ideālais rādītājs pārsniedz pašreizējo.
* **Brīdinājumi un numurēti ieteikumi** — vai arī „Sistēma darbojas labi, ņemot vērā pašreizējo kameru skaitu”, ja nav nekas jālabo.

Palaidiet to ikreiz, kad atklāšana vai straumēšana darbojas negaidīti — tā identificē lielāko daļu ar tīkla kartēm saistīto problēmu (nepareizs MTU, kamera pievienota nepareizajai saskarnes karti, USB adaptera ierobežojumi), neizejot no dialoglodziņa.

### Kameru masīva savienošana

Lai savienotu divas vai vairākas kameras kā **sinhronizētu masīvu**, izmantojiet masīva savienošanas vedni (**Connect Camera Array**): tas palīdz izvēlēties galveno un pakļautās kameras (iepriekš aizpildīts ar sinhronizācijas kabeļa pārbaudi — jebkura ar kabeli savienota kamera var būt galvenā, un kamera „RGB `-FRGB`” ir iepriekš atlasīta, ja tā ir pieslēgta ar kabeli), attēla režīma izvēli (atsevišķi vai apvienoti laukumi) un masīva iestatījumu skatu ar reāllaika prognozi par sasniedzamo kadru skaitu sekundē (fps) un vadu joslas platumu, pirms apstiprināt izmaiņas. Vadītājs un masīva darbplūsmas ir aprakstītas šīs rokasgrāmatas sadaļā par daudzkameru masīviem; CLI ekvivalents ir „LATTICE Camera First-Connect Workflow” [CLI atsauces dokumentā](../reference/cli-reference.md).

## Savienošanās no CLI un SDK
Lai piekļūtu

CLI un SDK, ir nepieciešams maksas Chloros+ līmenis un jābūt pieteicies; šis nosacījums tiek piemērots servera pusē (`401 AUTH_REQUIRED`, ja nav pieteicies, `403 PLAN_UPGRADE_REQUIRED` bezmaksas līmenī).

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

Pilnīgas parakstīšanas, opcijas un datu ieguves darbplūsmas: [CLI atsauces materiāls](../reference/cli-reference.md) § `chloros-cli lattice`, [SDK atsauces](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Kā tiek veikta kalibrēšana savienojuma izveides brīdī

Katrā LATTICE kamerā **kamerā** ir iebūvēts rūpnīcas kalibrēšanas pakotne, un Chloros pārbauda arī „MAPIR” mākoni, kad kamera izveido savienojumu:

| Situācija   | Ko izmanto Chloros                                                                                                                                                                                                           |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tiešsaistē**|**Jaunākā kalibrēšana, kas publicēta šim sērijas numuram** — mākonī esošā kopija ir prioritāra salīdzinājumā ar kamerā esošo kopiju. Tādējādi kamera, kas ir pārkalibrēta vai atjaunināta ar MAPIR, automātiski atjauninās pati; lietotāja rīcība nav nepieciešama. |
| **Bezsaistē**|**Kamerā esošais kalibrācijas pakotne**, tāda, kāda tā ir. Darba plūsmas, kas darbojas pilnībā bezsaistē, turpina darboties; tās vienkārši neuzņem jaunākas kalibrācijas, līdz kamera vismaz reizi ir bijusi tiešsaistē (vai ir veikta rūpnīcas iestatījumu atjaunošana).                                                  |

Uzņemšanas brīdī faktiski piemērotie koeficienti tiek **fiksēti katra attēla XMP metadatos**. Vēlāks kalibrācijas atjauninājums nekad nemainīs jau uzņemtos attēlus — pārstrādājot vecu uzņēmumu, tiek izmantoti koeficienti, kas ierakstīti tā XMP, nevis tie, kas šodien ir jaunākie.

## Problēmu novēršana

* **"Tīklā nav atrastas kameras”**— pārbaudiet lokālo savienojuma konfigurāciju sadaļā [Tīkla iestatījumi](https://mapir.gitbook.io/lattice-camera/setup/network-setup): statiskais tīkla interfeisa (NIC) adrese `169.254.x.x/16`, kameras atrodas tajā pašā savienojumā, DHCP/vārteja nav paredzēta. Tad izmantojiet**„Analyze System“**savienošanās dialoglodziņā, lai pārbaudītu, uz kura tīkla interfeisa katra kamera ir (vai nav) redzama. Pēc jebkādām kabeļu vai tīkla interfeisa izmaiņām veiciet**atkārtotu skenēšanu**.
* **Iepriekš darbojusies iekārta atsakās izveidot savienojumu** (masīva paneļa vārti ar `FRAMES WILL DROP` / `Reduce ROI to enable`) — tīkla kartes draivera atjauninājums ir nemanāmi atiestatījis uztveršanas gredzena iestatījumus. Piemērojiet tos atkārtoti vai palaidiet komandu `chloros-cli lattice network --fix` no termināļa ar paaugstinātām tiesībām; skatiet [Tīkla iestatījumi](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Kamera rāda „In Array”** — tā pieder pie savienotas masīva sesijas. Atvienojiet masīvu, lai kameru izmantotu atsevišķi.
