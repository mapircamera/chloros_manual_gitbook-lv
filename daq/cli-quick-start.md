# Ātrs sākums ar CLI (pool-*)

Piegādātie `chloros-cli` diski vada DAQ sensorus, izmantojot **`daq pool-*`** komandu grupu — vienkāršus HTTP-klientus, kas vadī sensorus, izmantojot Chloros aizmugurējās sistēmas pastāvīgo sensoru pūlu. Aizmugurējā sistēma pārvalda datu pārraidi, tādējādi GUI, CLI un SDK skripti visi izmanto vienu aktīvu rokturi, nevis cīnās par portu. Viss, kas nepieciešams klientam, ir pieejams, izmantojot `pool-*`: savienošanās, datu plūsma, kalibrētu `.daq` failu ierakstīšana un kapu profilu maiņa.

`pool-*` ir atbalstītā DAQ virsma izlaistajās versijās. `chloros-cli daq --help` uzskaita `pool-*` apakškommandas, un `daq discover` / `daq list` veic to pašu skenēšanu kā `pool-discover`. Ja tiešā aparatūras DAQ apakškommanda nav pieejama, programma tiek pārtraukta, parādot skaidru kļūdas ziņojumu, kurā norādīts trūkstošais pakotnes nosaukums un ieteikts izmantot `pool-*` — nekas neizdodas klusi. (Arī `pip install chloros-sdk` nenodrošina komandas tiešai saziņai ar aparatūru.)

***

## Priekšnosacījumi

* **Chloros aizmugurējam procesam jābūt palaistam** — `pool-*` komandas ir HTTP klienti, nevis aparatūras draiveri. Windows vidē palaidiet Chloros darbvirsmas lietotni (tā palaida aizmugurējo procesu). Linux/Jetson sistēmās bez grafiskā interfeisa aktivizējiet pakalpojumu kā savs lietotājs: `sudo systemctl enable --now chloros-backend@$USER.service` (skatiet [Linux instalācija](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **Chloros+ (maksas pakalpojums) pieteikšanās**: vispirms palaidiet `chloros-cli login`. Pārbaude notiek servera pusē — bez pieteikšanās komandas neizdodas ar `401 AUTH_REQUIRED`; bezmaksas (Iron) līmenī tās neizdodas ar `403 PLAN_UPGRADE_REQUIRED`.
* Komandas pēc noklusējuma ir vērstas uz `http://127.0.0.1:5000`; `daq pool-*` komandu grupa ņem vērā `CHLOROS_BACKEND_URL` vides mainīgo, ja jūsu backend darbojas citur.

***

## Piecu minūšu sesija

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — atver sensoru pūlā

| Variants | Nozīme |
| --- | --- |
| `daq pool-connect` | Viedā atpazīšana: atrod jebkuru DAQ šajā datorā. |
| `daq pool-connect --port PORT` | DAQ-U konkrētā seriālajā portā (piem., `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | DAQ-M, izmantojot BLE, automātiski skenēts MAC. |
| `daq pool-connect --mac MAC` | DAQ-M ar zināmu BLE MAC (nozīmē `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E ar zināmu uzņēmuma nosaukumu vai IP — **uzticamais ceļš**. |
| `daq pool-connect --eth` | DAQ-E ar automātisko atklāšanu (mDNS, ar ARP rezerves risinājumu). Skatīt zemāk minēto brīdinājumu. |

Optimizācijas parametri, visi ir fakultatīvi:

| Parametrs | Nozīme |
| --- | --- |
| `--integration-time MS` / `-t MS` | Manuālais integrācijas laiks milisekundēs. |
| `--frame-avg N` / `-f N` | Vidējais kadru skaits uz vienu ziņoto spektru. |
| `--no-ae` | Atspējot automātisko ekspozīciju (AE pēc noklusējuma ir ieslēgta). |
| `--no-stream` | Izveidot savienojumu, neuzsākot datu plūsmu (vēlāk turpināt ar `pool-stream --start`). |
| `--cap-id CAP` | Cap korekcijas profils; aizmugurējās sistēmas noklusējums ir `sunshine_cosine`. Skatīt [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

Pievienojot DAQ-E, kas jau atrodas pūlā ar citu savu adresi — tā `daq-e-<id>.local` uzņēmuma nosaukumu vai IP adresi —, tiek atgriezts esošais pūla ieraksts, nevis atkārtoti atvērta ierīce.

{% hint style="warning" %}
**`--eth` automātiskās atklāšanas brīdinājums.** Uz daudzadrešu datora (ar vairāk nekā vienu aktīvu tīkla interfeisu) *pirmais* `pool-connect --eth` pēc sistēmas uzsākšanas var būt tukšs, pat ja sensors darbojas pareizi — atklāšanas pārlūkošana var nepamanīt sensora interfeisu, kamēr ARP kešatmiņa ir neaktīva. Ja `--eth` neko neatrod, mēģiniet vēlreiz vai pilnībā izlaidiet atklāšanu, izmantojot `--eth-host <ip-or-hostname>`, kas ir uzticamākais risinājums datoriem ar vairākiem tīkla savienojumiem. DAQ-E datora nosaukums ir `daq-e-<id>.local` (piem., `daq-e-def330.local`); der arī tā IP adrese.
{% endhint %}

## `pool-discover` — skenēšana bez savienošanās

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

Meklē sensorus, ar kuriem varētu izveidot savienojumu, un parāda adresi, ar kuru savienoties, neveidojot savienojumu. `--only` pieņem ar komatu atdalītu apakškopu no `usb,ble,eth`; `--timeout SEC` nosaka skenēšanas laiku katram transporta veidam (noklusējums 5 s, ierobežots diapazonā no 1 līdz 20 s). Sensori, kas jau ir atvērti pūlā, netiek uzskaitīti (pieslēgts DAQ-M pārtrauc reklāmu) — šiem sensoriem izmantojiet `pool-list`. Piegādātajā versijā `chloros-cli daq discover` un `daq list` veic šo pašu skenēšanu.

## `pool-list` — apskatīt, kas ir pieslēgts

Parāda visus sensorus aizmugurējā pūlā, ieskaitot `sensor_id`, kas nepieciešams visām pārējām komandām:

| Modelis | `sensor_id` forma | Piemērs |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetu garš ar defisi | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

DAQ-E, kura PTP ir izslēgts (iestatījums tiek saglabāts ierīcē), tiek atzīmēts `pool-list` ar `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` un komandu, kas to atkal ieslēdz — skatīt [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — spektra rāmju nolasīšana

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Atgriež pēdējo rāmi vai pēdējos `--recent N` rāmjus; `--json` ģenerē mašīnlasāmu izvadi skriptu izmantošanai. Kadri ir radiometriski kalibrēts spektrālais starojuma blīvums (W/m²/nm) 135 punktu tīklā diapazonā 340–1010 nm, kur jau ir piemērots sensora vāciņa profils. Lai iegūtu kvantitatīvus starojuma intensitātes rādītājus, aprēķiniet vidējo vērtību vismaz 15 sekunžu garumā — tā ir ierīces īpašība, nevis defekts.

## `pool-stream` — apturēt vai atsākt datu plūsmu

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — ierakstīt `.daq` failu

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| Karodziņš | Noklusējums | Nozīme |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | Ieraksta ilgums sekundēs; `0` nozīmē, ka process turpināsies, līdz tiks izdots `--stop`. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Izvades katalogs. Ja backend atrodas šajā datorā, relatīvais ceļš tiek noteikts attiecībā pret mapi, kurā tika palaista komanda; ja backend atrodas attālināti, ceļš tiek noteikts **datorā, kurā darbojas backend**. |
| `--device-name NAME` | — | Ar ierakstu saglabātais apzīmējums. |
| `--stop` | — | Pārtrauc notiekošo ierakstīšanu. |
| `--confirm-after SEC` | `20` | Pēc sākšanas gaidiet līdz šim laikam, kamēr tiek ierakstīta pirmā rinda, un skaļi brīdiniet (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`), ja tā neierodas. `0` atspējo pārbaudi. |
| `--heartbeat SEC` | `30` | Ar `--duration` ik pēc SEC sekundēm izdrukājiet rindu skaitu un brīdiniet, ja tas vairs nepalielinās. `0` to atspējo. |

{% hint style="info" %}
Ierakstīšana notiek backendā, tādēļ `.daq` fails nonāk **backend datora** failu sistēmā — pēc noklusējuma `~/Documents/DAQ Live View/` tur, nevis obligāti tur, kur jūs palaidāt CLI. Failu nosaukumos ir iekļauts sensora ID un laika zīmogs.
{% endhint %}

## `pool-set-cap` — norādiet uzstādīto vāciņu

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Vāciņa ID izvēlas rūpnīcā izmērīto korekcijas profilu, kas tiek piemērots katram spektram, un tam **jāsakrīt ar vāciņu, kas fiziski uzstādīta uz sensora** — ne sensors, ne programmatūra nevar patstāvīgi atpazīt vāciņu, un izvēle tiek ierakstīta katrā `.daq` failā. Noklusējuma vērtība visur ir `sunshine_cosine` (katrs DAQ tiek piegādāts ar uzstādītu „Sunshine” kosinusa korektora vāciņu, kuras projektētā vājināšana ir apmēram 12× — nedeklarēta vāciņas maiņa spektrus nepareizi koriģē aptuveni par šo koeficientu).

| `--cap-id` | Pieejams |
| --- | --- |
| `sunshine_cosine` (noklusējums) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | tikai DAQ-U |
| `none` | tikai DAQ-E — skatīt piezīmi |

Savienojuma izveides brīdī tiek noraidīts vāciņa ar identifikācijas numuru, kas neatbilst sensora komplektam, un tiek parādīta skaidra kļūda. `none` (DAQ-E) nozīmē, ka vāciņš ir fiziski noņemts — tas joprojām piemēro rūpnīcas ģeometrijas profilu DAQ-E padziļinātajam stikla difuzoram, tādēļ tas nav bezdarbības stāvoklis, un DAQ-E bez vāciņa ir laboratorijas konfigurācija, nevis atbalstīta konfigurācija lietošanai laukā. (Neapvalkots DAQ-U ir pilnībā neapvalkots un tam vispār nav nepieciešams korekcijas profils; DAQ-M tiek izmantots kopā ar tā Sunshine vāciņu.)

## `pool-disconnect` — sensoru atbrīvošana

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect` atbrīvo sensoru neatkarīgi no tā, kurš to atvēra, tādējādi atbrīvojot arī sensoru, ko pūlā atstājis avarējis skripts (pirms tam tiek pabeigta uz tā veikta ierakstīšana). Sensoru, kas neatrodas pūlā, nav ko atbrīvot: komanda izdrukā `<sensor-id>: already released (not in the pool)` un iziet no programmas `0`. Ja atbrīvošanu aizmugurējā sistēma noraidīja vai neapstiprināja, programma iziet no programmas `1`.

## `pool-enable-ptp` — atkal ieslēgt DAQ-E PTP

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

DAQ-E saglabā savu PTP ieslēgšanas/izslēgšanas iestatījumu savā atmiņā, tādēļ ierīce, kuras PTP tika izslēgts, paliek izslēgta — un tās laika zīmogi netiek sinhronizēti ar PTP — līdz brīdim, kad tas tiek atkal ieslēgts. `pool-list` atzīmē šādu ierīci un izdrukā šo komandu attiecībā uz to. Chloros nekad pats neieslēdz ierīci atpakaļ, jo izslēgšana var būt apzināta. Jaunums versijā 1.3.0.

## DAQ-E ierīces iestatījumi

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info` parāda sensora programmaparatūru (un to, vai šajā Chloros versijā ir pieejama jaunāka versija), tā adresi un to, kā tā tika iegūta, saglabātos tīkla iestatījumus, ja tie atšķiras no pašreizējiem, ieslēgšanas datu plūsmu un PTP. `pool-set-network` pārstartē sensoru, lai piemērotu izmaiņas, un atkārtoti savieno to ar iepriekšējiem iestatījumiem; `--no-reboot` tos tikai saglabā. Šī datora tīklā ir jābūt statiskai adresei, citādi Chloros to noraida; `--force` to ignorē, taču tikai attiecībā uz datoru, kuram tiks piešķirta jauna adrese vai kurš tiks maršrutēts uz to. Pārstartēšana tiek noraidīta, kamēr sensors veic ierakstīšanu. `pool-update-firmware` ir GUI funkcijas **Update to \<version\>** ekvivalents komandrindai un veids, kā atjaunināt DAQ-E sistēmu Linux vidē, kurā nav GUI. Jaunums versijā 1.3.0.

***

## Komandu kopsavilkums

| Komanda | Mērķis |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Atver sensoru aizmugures pūlā. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Meklē sensorus, neveicot savienojumu (atrod DAQ-M BLE MAC adresi). |
| `daq pool-list` | Parāda visus pūlā esošos sensorus kopā ar to `sensor_id`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | Pēdējie N kalibrētie spektra kadri. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Turpināt / apturēt datu plūsmu. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | Sākt / apturēt `.daq` ierakstu (backend pusē). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Mainīt maksimālā līmeņa korekcijas profilu darbības laikā. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atbrīvot vienu sensoru vai visus. |
| `daq pool-enable-ptp --sensor-id ID` | Atkārtoti ieslēgt PTP ierīcei DAQ-E, kuras PTP ir izslēgts. |
| `daq pool-info --sensor-id ID [--json]` | DAQ-E programmaparatūra, adrese, saglabātie tīkla iestatījumi, straumēšana ieslēgšanas brīdī un PTP. |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP vai statiska adrese; pārstartē un atkārtoti savieno sensoru. |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | Datu straumēšana ieslēgšanas brīdī. |
| `daq pool-reboot --sensor-id ID` | DAQ-E pārstartēšana un atkārtota savienošana. |
| `daq pool-update-firmware --sensor-id ID` | Šīs versijas DAQ-E programmaparatūras ierakstīšana vecākā sensora atmiņā. |

***

## Problēmu novēršana, pirmoreiz pieslēdzot DAQ-E

1. DAQ-E nav statusa LED — pārbaudiet barošanu, izmantojot PoE/savienojuma indikatoru uz komutatora vai injektora porta, un pēc ieslēgšanas pagaidiet dažas sekundes, lai ierīce uzsāktu darbu un pievienotos tīklam.
2. Aizmugures datoram jāatrodas **tajā pašā apraides domēnā** kā sensoram — mDNS nešķērso maršrutētājus.
3. Ugunsmūrim ir jāļauj ienākt mDNS (UDP 5353), DAQ-E datiem (UDP 5002) un PTP (UDP 319/320). Windows instalētājs pievieno šim nolūkam noteikumu, savukārt Linux `.deb` atver tos `ufw` / `firewalld`, kas bija aktīvs instalācijas brīdī; trešās puses ugunsmūrim vai vēlāk aktivizētam ugunsmūrim ir jāatļauj šie savienojumi.
4. Joprojām nav nekādas reakcijas no `--eth`? Izmantojiet `--eth-host`, norādot ierīces uzņēmuma nosaukumu (`daq-e-<id>.local`) vai IP adresi, kas ļauj izlaist atklāšanas procesu.

***
{% hint style="info" %}
**Padoms AI palīgiem.** Katra šīs rokasgrāmatas lapa tiek nodrošināta kā neapstrādāts Markdown formāts — pievienojiet `.md` lapas mazajiem burtiem rakstītajam URL-slugam (šī lapa: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); mašīnlasāmais indekss ir `https://mapir.gitbook.io/chloros/llms.txt`. Lai iegūtu pilnīgu dokumentāciju par `chloros-cli daq` un visām pārējām komandu grupām, izmantojiet [CLI atsauci](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Pythons ceļš ir `chloros_sdk.connect_daq_sensor()` [SDK atsauces materiālā](../reference/sdk-reference.md).
{% endhint %}
</version\>
