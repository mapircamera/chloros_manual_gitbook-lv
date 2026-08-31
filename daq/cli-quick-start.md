# CLI Ātrā uzsākšana (pool-*)

Piegādātie `chloros-cli` diski vadīta DAQ sensorus, izmantojot **`daq pool-*`** komandu grupu — vieglajiem HTTP klientiem, kas vadīja sensoru, izmantojot Chloros aizmugurējās sistēmas pastāvīgo sensoru kopu. Aizmugurējam serverim pieder transporta kanāls, tādēļ grafiskā lietotāja saskarne, CLI un SDK skripti visi izmanto vienu aktīvu rīku, nevis cīnās par portu. Viss, kas nepieciešams klientam, ir pieejams caur `pool-*`: savienošanās, datu plūsma, kalibrētu `.daq` failu ierakstīšana un kapu profilu maiņa.

`pool-*` ir arī **vienīgā** DAQ virsma izlaistajās versijās. `chloros-cli daq --help` uzskaita `pool-*` apakškommandas, un, izsaucot tiešās aparatūras DAQ apakškommandu izlaistajā versijā, programma tiek pārtraukta ar skaidru kļūdas ziņojumu, kurā norādīts trūkstošais pakotnes nosaukums un ieteikts atgriezties pie `pool-*` — nekas neiziet nepamanīts. (Komandas, kas darbojas tieši ar aparatūru, darbojas tikai no MAPIR avota izvilkuma; arī `pip install chloros-sdk` tās nenodrošina.)

***

## Priekšnosacījumi

* **Chloros backendam jābūt palaistam** — `pool-*` komandas ir HTTP klienti, nevis aparatūras draiveri. Windows sistēmā palaidiet Chloros darbvirsmas lietotni (tā palaida backend). Uz bezmonitora Linux/Jetson ierīcēm aktivizējiet pakalpojumu: `sudo systemctl enable --now chloros-backend.service`.
* **Pieslēgšanās ar Chloros+ (maksas līmenis)**: vispirms palaidiet `chloros-cli login`. Pārbaude notiek servera pusē — bez pieteikšanās komandas neizdodas ar `401 AUTH_REQUIRED`; bezmaksas (Iron) līmenī tās neizdodas ar `403 PLAN_UPGRADE_REQUIRED`.
* Komandas pēc noklusējuma ir vērstas uz `http://127.0.0.1:5000`; `daq pool-*` komandu grupa ņem vērā `CHLOROS_BACKEND_URL` vides mainīgo, ja jūsu backend darbojas citur.

***

## Piecu minūšu sesija

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

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

## `pool-connect` — atvērt sensoru pūlā

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

{% hint style="warning" %}
**`--eth` automātiskās atklāšanas brīdinājums.** Uz daudzadrešu datora (ar vairāk nekā vienu aktīvu tīkla interfeisu) *pirmais* `pool-connect --eth` pēc sistēmas palaišanas var būt tukšs, pat ja sensors darbojas pareizi — atklāšanas pārlūkošana var nepamanīt sensora interfeisu, kamēr ARP kešatmiņa ir neaktīva. Ja `--eth` neko neatrod, mēģiniet vēlreiz vai pilnībā izlaidiet atklāšanu, izmantojot `--eth-host <ip-or-hostname>`, kas ir uzticams risinājums datoriem ar vairākiem tīkla savienojumiem. DAQ-E datora nosaukums ir `daq-e-<id>.local` (piem., `daq-e-def330.local`); der arī tā IP adrese.
{% endhint %}

## `pool-list` — apskatiet, kas ir pieslēgts

Parāda visus sensorus aizmugures pūlā, ieskaitot `sensor_id`, kas nepieciešams visām pārējām komandām:

| Modelis | `sensor_id` forma | Piemērs |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetu garš ar defisi | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — spektra rāmju nolasīšana

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

Atgriež pēdējo kadru vai pēdējos `--recent N` kadrus; `--json` ģenerē mašīnlasāmu izvadi skriptu izmantošanai. Kadri ir radiometriski kalibrēts spektrālais starojuma blīvums (W/m²/nm) 135 punktu tīklā diapazonā no 340 līdz 1010 nm, kur jau ir piemērots sensora vāciņa profils. Lai iegūtu kvantitatīvus starojuma intensitātes rādītājus, aprēķiniet vidējo vērtību vismaz 15 sekunžu garumā — tā ir ierīces īpašība, nevis defekts.

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
| `--duration SEC` / `-d SEC` | `0` | Ieraksta ilgums sekundēs; `0` nozīmē, ka programma darbojas, līdz tiek izsniegts `--stop`. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Izvades katalogs, kas tiek noteikts **datorā, kurā darbojas backend**. |
| `--device-name NAME` | — | Etiķete, kas tiek saglabāta kopā ar ierakstu. |
| `--stop` | — | Pārtrauc notiekošo ierakstu. |

{% hint style="info" %}
Ierakstīšana notiek backendā, tāpēc `.daq` fails nonāk **backend datora** failu sistēmā — pēc noklusējuma `~/Documents/DAQ Live View/` tur, nevis obligāti tur, kur jūs palaidāt CLI. Failu nosaukumos ir iekļauts sensora ID un laika zīmogs.
{% endhint %}

## `pool-set-cap` — norādiet uzstādīto vāciņu

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Vāciņa ID izvēlas rūpnīcā izmērīto korekcijas profilu, kas tiek piemērots katram spektram, un tam **jāatbilst vāciņai, kas fiziski uzstādīta uz sensora** — ne sensors, ne programmatūra nevar patstāvīgi atpazīt vāciņu, un izvēle tiek ierakstīta katrā `.daq` failā. Noklusējuma vērtība visur ir `sunshine_cosine` (katrs DAQ tiek piegādāts ar uzstādītu „Sunshine” kosinusa korektora vāciņu, kuras projektētā vājināšana ir apmēram 12× — nedeklarēta vāciņas maiņa spektrus kļūdaini koriģē aptuveni par šo koeficientu).

| `--cap-id` | Pieejams |
| --- | --- |
| `sunshine_cosine` (noklusējums) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | Tikai DAQ-U |
| `none` | Tikai DAQ-E — skatīt piezīmi |

Savienojuma izveides brīdī tiek noraidīts vāciņa ar identifikatoru, kas neatbilst sensora komplektam, un tiek parādīta skaidra kļūda. `none` (DAQ-E) nozīmē, ka vāciņš ir fiziski noņemts — tas joprojām piemēro rūpnīcas ģeometrijas profilu DAQ-E padziļinātajam stikla difuzoram, tādēļ tas nav bezdarbības stāvoklis, un neapklāts DAQ-E ir laboratorijas konfigurācija, nevis atbalstīta lauka konfigurācija. (Neapvalkots DAQ-U ir pilnībā neapvalkots un tam vispār nav nepieciešams korekcijas profils; DAQ-M tiek izmantots kopā ar tā Sunshine vāciņu.)

## `pool-disconnect` — sensoru atbrīvošana

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## Komandu kopsavilkums

| Komanda | Mērķis |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Atvērt sensoru aizmugurējā pūlā. |
| `daq pool-list` | Parādīt visus pūlā esošos sensorus kopā ar to `sensor_id`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | Pēdējie N kalibrētie spektra kadri. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Turpināt / apturēt datu plūsmu. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | Sākt / apturēt `.daq` ierakstu (backend pusē). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Mainīt maksimālā līmeņa korekcijas profilu darbības laikā. |
| `daq pool-disconnect --sensor-id ID [--all]` | Atbrīvot vienu sensoru vai visus. |

***

## Pirmās DAQ-E savienošanas problēmu novēršana

1. DAQ-E nav statusa LED — pārbaudiet barošanu, izmantojot PoE/savienojuma indikatoru uz komutatora vai injektora porta, un pēc ieslēgšanas pagaidiet dažas sekundes, lai ierīce uzsāktu darbu un pievienotos tīklam.
2. Aizmugures datoram jāatrodas **tajā pašā apraides domēnā** kā sensoram — mDNS nešķērso maršrutētājus.
3. Windows ierīcē pirmajā palaišanas reizē apstipriniet Defender ugunsmūra pieprasījumu (mDNS UDP 5353, DAQ-E dati UDP 5002, PTP UDP 319/320).
4. Joprojām nav nekādas reakcijas no `--eth`? Izmantojiet `--eth-host`, norādot ierīces hostvārdu (`daq-e-<id>.local`) vai IP adresi — tas ir uzticamākais veids, īpaši datoriem ar vairākiem IP adresēm.

***{% hint style="info" %}**Padoms AI palīgiem.** Katra šīs rokasgrāmatas lapa tiek nodrošināta kā neapstrādāts Markdown — pievienojiet `.md` lapas mazajiem burtiem rakstītajam slugam URL (šī lapa: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); mašīnlasāmais indekss ir `https://mapir.gitbook.io/chloros/llms.txt`. Lai iegūtu pilnīgu dokumentāciju par `chloros-cli daq` un visām pārējām komandu grupām, lejupielādējiet [CLI atsauci](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Python ceļš ir `chloros_sdk.connect_daq_sensor()` [SDK atsauces materiālā](../reference/sdk-reference.md).
{% endhint %}
