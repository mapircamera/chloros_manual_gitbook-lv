# Chloros Python SDK atsauces rokasgrāmata

**Versija:**

1.3.0**Izveidots:**2026-07-29 19:19 ·**Pārskatīts:** 2026-11-06**Pakete:** `chloros-sdk` (PyPI)**Mērķauditorija:** Optimizēts LLM lietošanai; cilvēkam saprotams.**Darbības joma:** Visas publiskās klases, funkcijas un palīgfunkcijas, ko piedāvā `import chloros_sdk`, ar piemēriem, kurus var kopēt un ielīmēt, aptverot attēlu apstrādi, vienas kameras vadību, sinhronizētus masīvus, DAQ sensoriem un projekta automatizāciju, kā arī `chloros-mcp` serveri AI palīgiem.

Ja jums nepieciešama tikai galvenā informācija, pārejiet uz:
- [Instalācija un ātrs sākums](#instalācija)
- [Smart-Connect LATTICE kameru masīviem](#smart-connect-for-lattice-cameras)
- [DAQ sensoru sesijas](#daq-sensor-sessions)
- [Kameru palīgrīki](#camera-utilities)
- [Pārejošie masīvi, izlīdzināšanas profili un indeksi](#transient-arrays-alignment-profiles--indices)
- [Projekta mape un receptes](#project-folder--recipes)
- [Projekta automatizācija](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Reāllaika kadri jūsu pašu apstrādes procesā](#live-frames-in-your-own-pipeline)
- [MCP serveris (AI palīgi)](#mcp-server-ai-assistants)

> **Izmaiņas versijā 1.3.0** (ko pamanīs esošais 1.2.0 skripts).
>
> **Izmaiņas, ar kurām 1.2.0 skriptam var būt jāstrādā:**
>
> - **Tagad tiek izraisīta kļūda, ja backend neapstiprina izlaidi.** `CameraSession.disconnect()`, `ArraySession.disconnect()` / `force_disconnect()` masīvā, ko atvēra sesija, `DAQSensorSession.disconnect()` / `force_disconnect()` uz sensora, ko tā atvēra, un `ChlorosProject.disconnect_all()` izraisa kļūdu `ChlorosConnectError`, ja backend noraida atbrīvošanu vai to neapstiprina laicīgi, jo ierīce joprojām var būt aizņemta. Versijā 1.2.0 tie tika klusi atgriezti. `with` bloka iziešana joprojām nekad neizraisa kļūdu.
> - **Aizņemta ierīce nekad netiek atbrīvota.** `with attach_array(...)` un eksplicīts `disconnect()` uz tās ļauj masīvam turpināt darboties (1.2.0 versijā bloka atstāšana to atbrīvoja, pat ja GUI to pārvaldīja); izsauciet `force_disconnect()`, ja vēlaties to pārtraukt. Tāpat arī `connect_daq_sensor()`, kas atrada sensoru jau pūlā (ieskaitot nulles karodziņu viedo atpazīšanu), atstāj to atvērtu uz `disconnect()` un pēc iziešanas no tā `with` bloka.
> - **PNG un JPEG pieņem tikai `display` un indeksētos produktus.** `capture()` ar `format="png"` / `"jpg"` (`ext=".png"` / `".jpg"`) un `radiance` vai `reflectance` līmenī, vai `processing="all"`, pirms jebkura pieprasījuma izraisa `ValueError`; 12 bitu `raw` / `debayered` tiek noraidīts no aizmugures. Skatīt [PNG / JPEG](#png--jpeg-take-the-display-and-index-products-only).
> - **`connect_daq_sensor(sensor_id=...)` atver šo sensoru vai izraisa** `ChlorosConnectError`, uzskaitot to, ko tas atradis; tas nekad neizmanto citu sensoru. Skatīt [Piesaistītais transports](#pinned-transport).
> - **`frame_stream()` izraisa kļūdu, nevis pārtrauc darbību:** `FrameFetchError` noraidījuma gadījumā nākamajā ciklā atkārtos (atstarošanas rādījums, kurā vispār nav iekļauts DAQ), pēc 60 s bez rāmja un pēc 60 s bez jauna triggera. Skatīt [Viens trigers uz vienu nolasījumu](#one-trigger-per-read--group_id).
> - **Nolasīšanas laika limita pārsniegšana vai pārtraukta savienojuma gadījumā tiek izraisīta kļūda `ChlorosConnectError`** no katra „smart-connect” izsaukuma un `analyze_array_network()`, kur 1.2.0 izraisīja pamatā esošo laika limita pārsniegšanu vai savienojuma kļūdu.
> - **`connect_array()` pēc noklusējuma gaida 600 s (`timeout`, 180 s versijā 1.2.0), un savienojums, no kura tas atsakās, tiek atcelts.** Aicinājums nosūta savu `timeout` uz aizmuguri, kas atgriež katru kameru tādā stāvoklī, kādā tā tika atrasta, un neveic savienojumu, ja savienošana netiek pabeigta laicīgi; aicinājums izraisa `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`). Versijā 1.2.0 savienojuma izveide tieši pēc kameru ieslēgšanas varēja notikt pēc izsaukuma beigām un izveidot masīva savienojumu pēc tam, kad izsaukums jau bija izraisījis kļūdu. `connect_all()` gaida katru saglabāto masīvu tādā pašā veidā (120 s 1.2.0 versijā). Skatīt [`connect_array()` paraksts](#connect_array-signature).
> - **`connect_array()` saglabā pilnu sensoru.** Ja nav `width` / `height` / `binning`, katra kamera ir iestatīta uz pilnu sensoru, un kadru ātrums samazinās līdz tam, ko nodrošina serveris un tīkls; 1.2.0 samazināja vai apvienoja kadrus, lai saglabātu kadru ātrumu. Skatīt [Sinhronizētais masīvs](#synchronized-array--arraysession-smart-prep).
> - **Bayer pikseļu formāts M3M (mono) kamerā ir tās dziļums.** M3M piedāvā tikai mono formātus, un iestatījumu nosaukumos minēti Bayer formāti: versijā 1.2.0 kamera noraidīja šo formātu un saglabāja savu, savukārt `settings.pixel_format` nosauca Bayer formātu. `LatticeCamera(preset=...)`, `apply_preset()`, `set_format()`, `CameraSettings(pixel_format=...)`, `connect_camera(preset=...)` un masīvskopīgais formāts tagad nosaka tāda paša dziļuma Mono formātu uz M3M: `"BayerRG8"` un `high_speed` veido `Mono8`; `"BayerRG12"`, `high_quality` un `triggered` dod `Mono12`; `cam.settings.pixel_format` to nosauc. Tas pats attiecas arī otrādi: M3C nekad netiek pārvērsts Mono formātā, tādēļ `"Mono12"` — no jebkura no šiem, `connect_array(pixel_format=...)`, `set_settings(pixel_format=...)` vai masīva saglabātajā formātā — M3C rada `BayerRG12`, neatkarīgi no tā, kura kamera ir masīva galvenā. Pārsūtītais `CameraSettings` saglabā savu nosaukumu, tādējādi tas pats objekts joprojām pieprasa katrai kamerai šo dziļumu savā ģimenē. `connect_camera(preset=...)` saglabā iestatījuma formātu (versijā 1.2.0 kamera pēc iestatījuma piemērošanas tiek iestatīta uz 12 bitiem), un kamera, kas atvērta ar iestatījumu, vairs neieraksta uzņemto informāciju `PRESETS`. Jebkurš cits formāts, ko kamera noraida (kas nav iekļauts tās `PixelFormat` sarakstā, piemēram, `RGB8` uz M3M vai `BayerRG16` programmnodrošinājumā, kurā tas nav iekļauts), netiek nosaukts arī par savu: pēc `LatticeCamera` savienojuma `apply_settings()` vai `set_format()`, `cam.settings.pixel_format` un `packed_transport` norāda formātu, kādā kamera pārraida datus, un viena izdrukātā rinda norāda formātu, kuru tā noraidīja.
> - **`analyze_array_network()` norāda, kāds savienojums tiek izmantots:** `pixel_format=None` (iepriekš `"BayerRG8"`) nozīmē, ka savienojums iestata (Mono12 / BayerRG12), bet `packed="auto"` — lēmumu par saspiešanas veidu, tādēļ norādītais kadru skaits sekundē (fps) var atšķirties no 1.2.0'. Ja nododat `pixel_format`, tas projicē M3M šajā dziļumā. Skatīt [Tīkla analīzes palīgs](#network-analysis-helper).
> - **`CameraSession.capture()` atgriež `CaptureResult`** (joprojām rāmju vārdnīcu saraksts) ar `.skipped`, `.failed`, `.daq_files` un `.daq_matches`. `ArraySession.capture()` rezultāts ietver arī `.alignment` (vai tika piemērota izlīdzināšanas deformācija un kāpēc nē) un `.oriented`.
> - **Masīvu uztverumi pēc noklusējuma tiek izlīdzināti, tāpat kā lietotnes dati.** `ArraySession.capture()`, `continuous()`, `capture_repeated()` un `ArrayHandle.capture()` / `capture_smart()` izkropļo katra dalībnieka `debayered`, `display`, `radiance`, `reflectance` un indeksa failus galvenās kameras kadrā un apgriež tos līdz dalībnieku pārklāšanās (mazāku par sensoru, ņemot vērā kameru nobīdes, `XMP-mapir:AlignmentJSON` `"applied": true`), tādējādi tie ir tie paši faili, ko Chloros lietotne uzņēma tajā pašā ainā; `raw` paliek oriģinālajā izmērā. Iepriekš uzņemot attēlus bez `aligned=True`, katrs fails tika saglabāts sākotnējā izmērā, bez deformācijas. `aligned=False` to saglabā. `chloros-mcp``capture_array` un `start_capture_run` izmanto to pašu noklusējumu. Skatīt [`ArraySession` metodes](#arraysession-methods).
> - **`connect_all()` norāda uz aizmuguri, kas noraida SDK.** Darbojošās aizmugures, kas ir izslēgtas vai kuras izmanto plānu, kas ir zemāks par Copper, tiek ziņotas ar `errors`, norādot savu iemeslu (HTTP 401 / 403), nevis kā „neviens Chloros backend nedarbojas”, un katrs `connect_all()` izsaukums atkārtoti pārbauda backend, tādēļ tā izsaukšana pēc pieteikšanās darbojas ar to pašu projekta objektu.
> - **Relatīvais `output_dir` darbojas ar lokālo backend.** Relatīvie ceļi tiek pārvērsti par absolūtajiem attiecībā pret jūsu skripta darba direktoriju, pirms tie tiek nosūtīti. Versijā 1.2.0 backend tos atrisināja savā instalācijas mapē, kas beidzās ar kļūdu „Piekļuve liegta”.
> - **`numpy` tagad ir SDK prasība** (live-frame izsaukumi atgriež numpy masīvus).
> - **Eksporta līmeņa nosaukums, kas nav „one”, tiek noraidīts.** `processing=` un `levels=` pieņem `raw`, `debayered`, `radiance`, `reflectance` vai `display` (vai arī tādu aliasu kā `preview`; `processing=`, kā arī `"all"`). Jebkurš cits nosaukums tiek noraidīts, pirms kamera tiek pieskārsta (HTTP 400 `UNKNOWN_LEVEL`: `capture()` izraisa `ChlorosConnectError`, `grab()` / `frame_stream()` `FrameFetchError`); 1.2.0 versijā tas tika saglabāts vai atgriezts kā `raw`.
> - **LATTICE uzņēmumiem tiek noraidīta funkcija „Texture Aware”.** Projektā, kurā ir LATTICE uzņēmumi, `configure(debayer="Texture Aware (Slow, Highest Quality)")` izraisa `ChlorosConfigurationError`, un to pašu dara arī `process()` pēc `configure()`, kas pirms importēšanas pieprasīja to pirms importēšanas. Projektā iepriekš saglabātā „Texture Aware” vērtība tiek iestatīta uz „Standard”, izraisot kļūdu `UserWarning`. LATTICE uzņēmumiem vienmēr tiek piemērota standarta demosaika, tāpēc versija 1.2.0 pieņēma šo iestatījumu un to ignorēja; arī lietotne Chloros to šiem uzņēmumiem nepiedāvā. Skatīt [Atbalstītās vērtības](#supported-values).
> - **Kameras tiek atgrieztas tādā stāvoklī, kādā tās tika atrastas — ja vien nav `keep_settings=True`.** `ArraySession.disconnect()` / `force_disconnect()`, katras pārejošās funkcijas beigās (`capture_cameras_transient()`, `start_multi_camera_stream()`, `test_array_sync()`, `calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index()`) un `start_camera_continuous()` apturēšana atjauno katru kameru tādā stāvoklī, kādā tā tika atrasta: tās slēdža un sinhronizācijas līnijas vadu savienojumus, PTP, kā arī visus sesijas laikā mainītos kameras iestatījumus (ekspozīciju un pastiprinājumu, AE mērķis, ierobežojumi un amortizācija, balansa iestatījumi, melnā līmeņa iestatījumi, pikseļu formāts, pikseļu apvienošana un ROI, apgriešana, kadru ātrums). `keep_settings=True` savukārt saglabā katras kameras iestatījumus kameras paša atmiņā, lai tā ieslēgtos ar šiem iestatījumiem; `clear_settings=True` atjauno šo atmiņu uz rūpnīcas iestatījumiem. Abos gadījumos kameras turpina darboties brīvi. Projekts, savienojoties ar kamerām, iestata tās, izmantojot savus saglabātos iestatījumus. Skatīt [Kameras atmiņa](#camera-memory--keep_settings--clear_settings).
> - **Katrs režīms saglabā projektu vienā un tajā pašā veidā.** Jebkuras izmaiņas kamerā, masīvā vai gaismas sensorā, kas pieslēgts projektam — no SDK, lietotnes, CLI vai MCP servera — tiek saglabātas šī projekta `cameras.json` / `sensors.json`, un, atverot projektu jebkurā režīmā, ierīces tiek konfigurētas atbilstoši šiem iestatījumiem; `ChlorosProject` nosauc savu mapi backend. Iepriekš projektu saglabāja tikai lietotne. Skatīt [Viens projekta ieraksts, katrs režīms](#one-project-record-every-mode).
> - **No vairākām kamerām izveidotajos failos tiek norādītas visas kameras.** Daudzjoslu kopums, reāllaika indekss, kas izveidots no vairāku kameru joslām, un masīva uzņemšanas `composite/` attēls satur katras avota kameras sērijas numuru, modeli un kalibrēšanas paketi saraksta veidā (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), savukārt EXIF `SerialNumber` sarakstā sērijas numuri ir uzskaitīti, atdalīti ar komatu. Agrāk tajos netika norādīta neviena kamera vai tika norādīts tikai pirmās kameras sērijas numurs. Skatīt [Faili, kas izveidoti no vairākām kamerām](#files-made-from-several-cameras).
> - **Tikai `raw` failā ir iekļautas kameras kalibrēšanas metadatas.** MicaSense saderīgais komplekts (`BlackLevel`, `RadiometricCalibration`, katrakanālu `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` komplekti) un `CalibrationJSON` datu bloks apraksta sensora neapstrādātās vērtības, tādēļ tās tiek iekļautas tikai `raw` uzņemšanas failā. Visi pārējie faili — uzņemšanas`debayered`, `display`, `radiance`, `reflectance` un indeksa faili, nepārtrauktas ierakstīšanas un daudzkameruplūsmu kadri, kā arī katrs `process()` produkts — satur kameras identifikāciju (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...), kā arī uzņemšanas brīdī — paša kadra ierakstu (`XMP-mapir:FrameJSON`: ekspozīcija, pastiprinājums, temperatūra, pikseļu formāts, uzņemšanas laiks). Versijā 1.2.0 tajos bija iekļauts arī neapstrādāto datu kopums, ko rīks (Pix4D, Metashape), kas to piemēroja, attiecināja uz pikseļiem, kas nav neapstrādātas vērtības. Apstrāde nosaka šāda faila kalibrāciju pēc tā sērijas numura un `CalBundleSha256`.
>
> **Citas izmaiņas:**
>
> - **Masīva automātiskais kadru ātrums pielāgojas tam, ko nodrošina masīvs.** Ātrums, kas zaudē vairāk nekā 1 % no saviem izraisītājiem, tiek samazināts, pamatojoties uz vairākiem ilgstošiem novērojumiem, nevis uz vienu, un ātrums, no kura tas tika samazināts, tiek laiku pa laikam atkārtoti pārbaudīts. Jūsu pašrocīgi iestatītais ātrums paliek nemainīgs.
> - **Automātiskā ekspozīcija ir stabilāka.** Tā saglabā nemainīgu ekspozīciju nemainīgos apgaismojuma apstākļos, pielāgojas izmaiņām dažu sekunžu laikā, ignorē nelielus pārgaismojumus un atjauno kameras darbību, ja tā pārstāj reaģēt spilgtā saules gaismā. Skatīt [Kā darbojas automātiskā ekspozīcija](#how-auto-exposure-behaves).
> - **`import_images(recursive=True)` skaita tikai jūsu attēlus un ierakstus.** Mapē, kurā atrodas projekta `camera_cal/`, šie kalibrēšanas faili vairs netiek uzskatīti par attēliem un ierakstiem, un projekts, kurā tie jau bija iekļauti, tos izmet.
> - **`configure(radiometric_output=...)` tiek piemērots.** Tagad tas nosaka projekta `Export radiance` / `Export reflectance` produktus; iepriekš katra projekta paša produktu iestatījumi bija prioritāri, tāpēc `radiance`, `sensor-response` un `all` visi eksportēja starojuma intensitāti un atstarošanas koeficientu.
> - **`test_array_sync()` sāk darboties no kameru ieslēgšanas brīža.** Tas ieslēdz kameru PTP darbības laikā, gaida, līdz to pulksteņi sinhronizējas un stabilizējas pirms pirmā trigera (`ptp_lock`) — katra kamera sinhronizējas 120 s, apmēram 2 minūtes no ieslēgšanas brīža (`ptp_settle_s=` to iestata), jo tikko sinhronizēts pulkstenis apmēram 2 minūtes atšķiras līdz pat ~200 us — un atjauno katras kameras PTP tā, kā tas bija sākotnēji. Kamera, kas joprojām ir sinhronizēta no iepriekšēja testa, netiek gaidīta atkārtoti. Agrāk PTP tika atstāts izslēgts vienkomutatora tīklā, kurā neviens DAQ-E neatbildēja, un katras kameras brīvi darbojošais pulkstenis tika nolasīts kā PTP pulkstenis, kas nesinhronizētos („PTP nav sinhronizēts”, katrā darbības ciklā).
> - **`.daq` ieraksts norāda, vai tā vāciņš tika izvēlēts vai pieņemts kā esošs.** DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez vāciņa, un Chloros nevar redzēt, vai tāds ir uzstādīts, tāpēc sensors, kas pieslēgts bez nosauktas vāciņa, pēc noklusējuma saņem `sunshine_cosine` — tas ir *pieņēmums*. Katrs Chloros ieraksts (shēma 1.30) tagad glabā `cap_id_source` blakus `cap_id`: `operator`, ja vāciņam ir piešķirts nosaukums (ieskaitot `none`), `auto_default`, ja tas bija noklusējuma iestatījums. Apstrādājot ierakstu, kura kondensators tika pieņemts kā noklusējums, bet projekta DAQ kondensatora iestatījums palika uz „Auto”, tiek izdrukāts `[daq-dls] WARNING`, nosaucot failu: tīrs DAQ-U bez saules gaismas korekcijas rāda apmēram 30 reizes augstākus rādījumus. **Nosauciet uzstādīto vāciņu**, kad veicat savienojumu (`connect_daq_sensor(cap_id=...)` vai `DAQSensorSession.set_cap()`, `chloros-cli daq pool-connect --cap-id` / `daq pool-set-cap` vai GUI kondensatora izvēlnes), vai iestatiet projekta DAQ kondensatoru, un brīdinājums pazudīs.
> - **Windows vairs nerodas pip konsoles logi.** Datorā, kurā ir instalēts Python, backend un katrs no tā attēlu apstrādātājiem sākuma posmā vienu reizi palaida `pip`, un katrs atvēra konsoles logu. Tagad tas vairs nenotiek.
> - **Reāllaika `.daq` ieraksti izdzīvo strāvas padeves pārtraukumu.**Ieraksts tiek rakstīts ar žurnālu un pilnu diska sinhronizāciju katrā fiksēšanas reizē aiz sensora, tādējādi lēns disks aizkavē faila ierakstīšanu, bet nekad neaizkavē mērījumu reģistrēšanu. Ieraksts, kas pārtraukts komitēšanas vidū (strāvas padeves pārtraukums, pārtraukšana), var atstāt blakus failu `<name>.daq-journal`:**kopējiet abus kopā.** Chloros atgriež žurnālu atpakaļ, pirmoreiz lasot failu, tādējādi ieraksts atveras ar visu, kas tika fiksēts pirms apstāšanās (zaudētas ir aptuveni pēdējās 3 sekundes); versijā 1.2.0 strāvas padeves pārtraukums nepareizā brīdī varēja padarīt visu failu nelasāmu („datubāzes diska attēls ir bojāts”).
> - **`DAQSensorSession.record_start()` uz noslogota diska vairs neaptur sensoru**: nolasījumi tiek ievietoti rindā un ierakstīti atsevišķā pavedienā, un tas, ko neizdevās ierakstīt, tiek iekļauts `status()` un `record_errors` (kopā ar `record_last_error`).
> - **Tiešais `LatticeCamera`:**kadrs bez ekspozīcijas/pastiprinājuma tagad ziņo par kameras**faktisko** ekspozīciju un pastiprinājumu (rezerves variants nolasīja kešēto vērtību, kas varēja palikt nemainīga visā automātiskās ekspozīcijas cikla laikā), un `apply_settings(trigger_source='LineN')` / `set_trigger_source('LineN')` piemēro to pašu 5 µs ieejas traucējumu novēršanu, kādu saņem Chloros kabeļu pakļautās kameras (nefiltrēti — pakļautā kamera uzņem kadrus bez nosūtīta trigera signāla).
> - **Programmatūras vadītās kameras vairs neizlaiž katru otro trigeri.** Kamera programmatūras vadības režīmā tagad ievieto rindā trigeri, kas ienāk tās nolasīšanas laikā (vai arī Chloros gaida, līdz tā ir gatava), tāpat kā to jau darīja ar kabeli sinhronizētās kameras.
> - **`discover_daq_sensors()` nosauc DAQ-E-S par `DAQ-E-S`.** Tās `model` bija `DAQ-E` katram Ethernet sensoram; sensors, kas apmēram 2 s laikā neatbild uz identitātes pārbaudi, joprojām tiek uzskaitīts kā `DAQ-E`.
> - **Jauns: `chloros-mcp` — MCP serveris AI palīgiem.** Claude Desktop, Claude Code, Cursor un citi MCP klienti var apstrādāt attēlus (izveidot projektu, importēt uzņēmumus, izvēlēties iestatījumus, apstrādāt, ziņot par eksportētajiem rezultātiem) un vadīt LATTICE kameras, matricas un DAQ gaismas sensorus: izveidot savienojumu, apskatīt priekšskatījumu, iestatīt ekspozīciju un krāsu, uzņemt, ierakstīt. Tā rīki aptver to, ko dara šis SDK. Tas tiek instalēts kopā ar SDK, un Linux .deb failā tas atrodas `/usr/bin`. Skatīt [MCP serveris (AI palīgi)](#mcp-server-ai-assistants).
> - **Jaunums: kameru palīgrīki** — `chloros-cli lattice` komandas, ko izsauc SDK: krāsu profils, piesātinājums/kontrasts, spilgtās zonas, baltā balanss (vienreizējs, automātiska, fiksēti attiecības), iestatījumi, filtru modeļi, programmaparatūras stāvoklis un atjaunināšana, barošana (pārbaude, miega režīms, atmodināšana), mērķa kalibrēšana, tīkla adapteri (pārbaude, novērtēšana, labošana), vinjetes korekcija failos, DLS nolasīšana un uzņēmējdatora pārbaude. Skatīt [Kameras rīki](#camera-utilities).
> - **Jaunums: pārējā CLI kā SDK izsaukumi**, tādējādi CLI, SDK un `chloros-mcp` piedāvā tās pašas funkcijas (CLI drošības nolūkā saglabā tikai pieteikšanos, DAQ-E konfigurēšanu un tiešos DAQ serverus): kameras, kas tiek atvērtas ar vienu komandu (`start_camera_continuous()`, `start_multi_camera_stream()`, `capture_cameras_transient()`, `test_array_sync()`, `detect_array_master()`, `analyze_array_models()`), izlīdzināšanas profilu failus (`calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `alignment_profile_info()`, `reorder_alignment_profile()`), indeksi (`index_catalog()`, `compute_index()`, `capture_live_index()`), PTP galveno failu (`time_sync_status()` un citi), projekta mapi (`project_folder()`, `set_project_folder()`, `reset_project_folder()`) un receptes (`run_project_recipe()`, CLI paša izpildītājs). Gaismas sensoriem: `connect_daq_sensor(cap_id=...)`, `DAQSensorSession.set_cap()`, `daq_bindings()` / `set_daq_bindings()`, `daq_band_presets()` un `light_sensor_reflectance()` (divu sensoru vides + objekta atstarošanas koeficienti un indeksi); kamerai: `CameraSession.status()` un `capture_repeated()`; matricai: `ArraySession.capability()` un `set_member_settings()` (elementa ekspozīcija, pastiprinājums, ...). Skatīt [Pagaidu masīvi, izlīdzināšanas profili un indeksi](#transient-arrays-alignment-profiles--indices), [Projekta mape un receptes](#project-folder--recipes) un [DAQ sensoru sesijas](#daq-sensor-sessions).
> - **Uzlaboti LATTICE indeksu produkti** (NDVI un citi indeksi) no `process()` visos apstrādes režīmos.
> - **LATTICE OCN kameras: uzlaboti starojuma, atstarošanas un indeksu produkti saules gaismā.** Pēc joslu atdalīšanas tiek piemērota flotes korekcija — reāllaikā, uzņemumos un `process()`; RGN kameras paliek nemainīgas. Skatīt [Radiometriskie rezultāti](#radiometric-output-lattice-multispectral-pipeline).
> - **Katra uzņēmuma saglabāšanā tiek saglabāti izmantotie DAQ rādījumi, tāpat kā GUI.** `CameraSession.capture()` un `ArraySession.capture()` / `continuous()` raksta `.daq` un tā `.match.json`, kad vien tiek piesaistīts vai apvienots DAQ, ieskaitot tikai neapstrādātu ierakstu; 1.2.0 versijā neapstrādāts vai debayered uzņēmums tika saglabāts tikai ar `force_daq=True`. `daq=False` to izslēdz, un `daq_tolerance_ms=` nosaka saskaņošanas pielaidi; abi rezultāti norāda, kas tika saglabāts `.daq_files` un `.daq_matches`. Tagad uzņemums gaida DAQ nākamo nolasījumu, lai varētu apvienot nolasījumus abās ekspozīcijas pusēs. Skatīt [DAQ nolasījums, ko saglabā uzņemums](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
> - **`ArraySession` satur reāllaika kadrus un servera puses nepārtrauktu uzņemšanu.** `grab()` un `frame_stream()` atgriež numpy kadrus no viena triggera (tāds pats nolasījums kā projekta API funkcijās `ArrayHandle.grab()` / `frame_stream()`), savukārt `continuous()` saglabā katru sinhronizēto grupu aizmugurē un atgriež [`ContinuousHandle`](#continuoushandle). Skatīt [Reāllaika rāmji jūsu pašu apstrādes ķēdē](#live-frames-in-your-own-pipeline).
> - **Masīvs `grab()` ir piesaistīts vienam trigerim** (`ArrayHandle.grab()` un jaunais `ArraySession.grab()`). Versijā 1.2.0 12 no 20 četriemkameru `ArrayHandle.grab()` nolasījumi aptvēra divus trigerus. Tagad katrs kadrs satur `group_id`, un masīvs `frame_stream()` katru trigeri ģenerē vienu reizi. Skatīt [Viens trigers uz vienu nolasījumu](#one-trigger-per-read--group_id).
> - **Kadri pa joslām un saskaņoti kadri:** `grab()` / `frame_stream()` izmanto `band=`, `align=`, `bit_depth=` un `refl_max=` kameras un masīva rokturiem. Skatīt [Vienas joslas eksportēšana](#exporting-a-single-band--band).
> - **Jaunas `ArraySession` metodes un argumenti:** `set_target_fps()`, `force_disconnect()`, `capture(after_seq=..., orient=...)` (kopā ar `CaptureResult.group_seq` un `.failed`), `continuous()`, `connect_array(packed=...)`; `capture_repeated()` savieno secīgas grupas un apstājas, kad disks ir pilns.
> - **`ArraySession.record()` reģistrē datus, ja GUI nav atvērts.** Ja GUI apvienotais skats ir aizvērts, aizmugurējā sistēma pati renderē skatu: masīvaNDVI vai indeksu, ko nodod kā `expr=`. `RecorderHandle.stop()` izraisa `ChlorosConnectError` kļūdu ierakstam, kas neizdevās (t. i., kurā netika ierakstīts neviens kadrs), un saglabā apstāšanās rezultātu `.result` (ja tiek atstāts `with` bloks, izņēmums netiek izraisīts: pārbaudiet `rec.result`). Skatīt [`RecorderHandle`](#recorderhandle).
> - **Jaunas `DAQSensorSession` metodes:** `enable_ptp()`, `force_disconnect()` un DAQ-E paša iestatījumiem `device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` un `update_firmware()`. `record_stop()` atgriež `warning` un `gaps`. Pārstartēšana, kas aizvēra ierakstu, to nosauc savā atbildē vai, ja pārstartēšana neizdodas, `ChlorosConnectError` (`recording_closed`; skatīt [DAQ-E ierīces iestatījumi](#daq-e-device-settings)). Skatīt [`DAQSensorSession` metodes](#daqsensorsession-methods).
> - **Apvienotais DAQ-E, kas pārtrauc datu nosūtīšanu, tiek atkārtoti savienots ar backend**, saglabājot iepriekšējos iestatījumus, un ieraksts tajā turpinās tajā pašā `.daq`, ar pārtraukumu, kas atzīmēts failā; `record_stop()` atgriež pārtraukumus kā `gaps`. Versijā 1.2.0 sensors palika kluss, līdz tas tika atvienots un atkal pieslēgts, kas pārtrauca ierakstīšanu. Skatīt CLI atsauces sadaļu [DAQ-E, kas pārtrauc datu nosūtīšanu](cli-reference.md#a-daq-e-that-stops-delivering).
> - **`open_project()` darbojas, izmantojot instalētu SDK.** Riteņš (instalētājaun PyPI) tagad satur nepieciešamos projekta lādētājus; iepriekš `open_project()` izraisīja `ImportError`, nosaucot `project_cameras` / `project_sensors` jebkurā ar pip instalētā SDK. `connect_all()` savieno saglabātās kameras, masīvus un gaismas sensorus caur darbojošos backend (gaismas sensori — caur tā DAQ pūlu), un saglabāta ierīce, kuru tas nevar atrast, tiek uzskaitīta zem `errors`, norādot iemeslu, nevis vienkārši izlaista bez paskaidrojuma. Saglabāts masīvs, kas jau darbojas backendā (GUI, vai `chloros-cli project connect`), tiek pievienots un atstāts darbojoties, turpretim iepriekš tas tika ziņots kā kļūda („Kamera  <sn>jau </sn>atrodas <sn>masīvā<id>”). Skatīt [`ChlorosProject` metodes](#chlorosproject-methods).
> - **`ChlorosLocal()`, `process_folder()` un `process_lattice_capture()` atrod Chloros Windows vidē.** Tās meklē vietu, kur instalētājs ievieto backend (pēc noklusējuma `<install folder>\backend\`, `C:\Program Files\Chloros\backend\`, lietotāja izvēlēta mape, kas norādīta instalētāja reģistrā), un izveido savienojumu ar jau darbojošos backend, nemeklējot jaunu. Versijā 1.2.0 tās parādīja kļūdu „Chloros backend nav atrasts” uz instalētā datora, ja vien netika norādīts `backend_exe=`.
> - **Par backend, kas pārtrauc darbību uzsākšanas brīdī, tiek ziņots nekavējoties.** Ja `ChlorosLocal()` vai smart-connect izsaukums uzsāk backend, kas beidz darbību, pirms tas atbild, aicinājums dažu sekunžu laikā izraisa kļūdu `ChlorosBackendError` no `ChlorosLocal` un `ChlorosConnectError` no smart--connect izsaukumiem, norādot iziešanas kodu, izpildīto komandu un pēdējās rindas, ko backend izvadīja. Versija 1.2.0 nogaidīja 60 s uzsākšanas laika limitu un nepaskaidroja iemeslu. Skatīt [Backend automātiskā uzsākšana](#backend-auto-start).
> - **Aizmugurējais modulis turpina darboties starp skriptiem.** Izietot no `with ChlorosLocal(...)` bloka, `process_folder()` / `process_lattice_capture()` atgriešanās un `chloros-mcp` iziešana vairs neaptur SDKs palaisto backend; nākamais skripts to izmanto atkārtoti. Tas apstājas pie `logout()` (ja vien nav atvērta Chloros lietotne vai nav sākta izpilde), pie `shutdown_backend()` vai pats pēc 30 minūtēm, ja nekas to neizmanto (`CHLOROS_BACKEND_IDLE_MIN` aizmugurējās vides iestatījumos to maina; `0`: nekad).
> - **SDKs palaistajam aizmugurējam procesam tiek lūgts izslēgties, nevis to pārtrauc.** `shutdown_backend()` ļauj backendam atbrīvot savas kameras un gaismas sensorus un pabeigt jebkādu ierakstīšanu; tas tiek pārtraukts tikai tad, ja tas joprojām darbojas pēc 10 sekundēm. Versija 1.2.0 to pārtrauca uzreiz (Windows vidē — ar piespiedu pārtraukšanu), kas varēja izraisīt, ka ar šo backend saistītais DAQ gaismas sensors vairs nereaģē. Backend, kuru SDK atrada darbojošos, joprojām tiek atstāts mierā.
> - **Linux vidē automātiskā palaišana nekad nepalaiž `backend_server.py` no pašreizējās mapes.** Ja nebija instalēta backend, skripts, kas tika palaists mapē, kurā atradās backend, to palaida; tagad tiek palaists tikai instalētais backend (vai, `ChlorosLocal` gadījumā, tas, kuru nosauc `backend_exe=`).
> - **`configure(indices=...)`**pievieno katru iestatījumu projektam atbilstoši kameras frekvenču joslām, iekļaujot jaunu projektu, un brīdina (`UserWarning`), ja nevar pievienot kādu nosaukumu.**`import_images()`** ziņo par `.daq` ierakstiem kā `scan_count` un vairs nebrīdina par LATTICE ierakstu mapi.
> - **`read_image_audit_tags()` darbojas pēc pip instalēšanas.** Tas pats nolasa attēla metadatus; versijā 1.2.0 tas atgriezās ar `{}` bez darbvirsmas instalācijas iekšējiem komponentiem Python ceļā.
> - **`capture()` tieši pēc `connect_array()`** gaida līdz 20 sekundēm, līdz tiek saņemta masīva pirmā kadru grupa, nevis izsauc kļūdu ar HTTP 503 „Masīvs joprojām iesilda”.
> - **`status()` ziņo par sistēmas apstāšanos.** Jaunais `stalled` lauks ir „True”, kamēr darbojas datu ieguves cikls, bet neko nepublicē, un `actual_fps` pēc tam ir 0. Versijā 1.2.0 `actual_fps` saglabāja savu pēdējo vērtību , tādēļ apstājusies masīva vērtība bija `actual_fps > 0`, kamēr uzņemšana neizdevās ar kļūdu 503 „Nav jaunas sinhronizētas kadru grupas”.
> - **Mazāk nepilnīgu kadru kopu un 503 kļūdu saglabāšanā:** katras pakļautās kameras trigera ieeja tagad ir attīrīta no traucējumiem.
> - **Saspiesta 12 bitu datu pārraide** (`connect_array(packed="auto")`) palielina pilna sensora masīva kadru ātrumu aptuveni par trešdaļu. Skatīt [Saspiesta 12 bitu datu pārraide](#packed-12-bit-transport-packed).
> - **Masīva ekspozīcija ROI zonā:** neliels ROI vairs neierobežo elementa ekspozīciju sensora brīvās darbības periodā (4,9 ms pie 768 × 304). Skatīt [Ekspozīcija masīva elementos](#exposure-on-array-members).
> - **Automātiskā ekspozīcija vairs nesamazina matricas automātisko kadru ātrumu.** Ar kabeli sinhronizēts masīvs darbojas ar ātrumu, ko ļauj tīkls un kameru nolasīšanas ātrums; automātiskā ekspozīcija aizpilda laiku, ko nodrošina šis ātrums, pēc tam palielina pastiprinājumu. Manuālā ekspozīcija, kas ir ilgāka par šo laiku, palēnina izraisītāju, lai pielāgotos tai, nevis tiek noraidīta. Skatīt [Ekspozīcija masīva elementiem](#exposure-on-array-members).
> - **`read_nodes()`**pievieno `frame_values` un `notes`, kamēr darbojas automātiskā ekspozīcija (skatīt [`read_nodes()` automātiskās ekspozīcijas režīmā](#read_nodes-under-auto-exposure--frame_values-and-notes)). Ja kamera noraida ekspozīciju vai pastiprinājumu,**`set_settings()`** norāda diapazonu, ko tā pieņem, un atjauno automātisko režīmu, kuru tā bija izslēgusi šai vērtībai; `set_settings(target_brightness_auto=True)` ir jauns.
> - **`orient=`** par `ArraySession.capture()` / `capture_fastest()` / `continuous()` / `burst()` un `ArrayHandle.capture()` / `capture_smart()` pagriež produktus atbilstoši kameras orientācijai. Skatīt [`ArraySession` metodes](#arraysession-methods).
> - **`DLS()` ir nepieciešams sensora paša `mac_address`.** Vairs nav noklusējuma MAC (tas bija vienas vienības adrese); `DLS()` bez tā izraisa `DLSError`. Skatīt [DAQ-M joslas lielumus](#daq-m-band-magnitudes--dlsband_magnitudes).
> - **Windows instalētājs** piedāvā opcijas „Iespējot garos Windows ceļus” un „Izslēgt Chloros backend no Microsoft Defender skenēšanas”, abas pēc noklusējuma ir atzīmētas. Skatīt [1. solis](#step-1--install-the-chloros-platform-package).
> - **Linux .deb** instalē Python SDK sistēmai `python3` (iepriekš tas tikai novietoja „wheel” failu uz diska), palielina kodola soketa uztveršanas buferus, kas nepieciešami kameru straumēm, un pievieno backend pakalpojumu, kas darbojas kā jūsu lietotājs. Jetson pakete ir `chloros_1.3.0_arm64_jp6.deb`, paredzēta JetPack 6, kopā ar JetPack CUDA PyTorch. Skatiet [1. soli](#step-1--install-the-chloros-platform-package) un [2. soli](#linux-deb).

---

## Arhitektūra 60 sekundēs

SDK ir plāns Python slānis virs Chloros aizmugurējā servera (tas pats Flask serveris, ko izmanto darbvirsmas GUI un CLI). Automatizācijai importējiet `chloros_sdk` un izsauciet augsta līmeņa metodes; aizkulisēs katrs izsaukums kļūst par HTTP-pieprasījumu vietējam backendam 5000. portā — `http://127.0.0.1:5000/api/...` (apzināti nevis `localhost`, kas Windows vidē vispirms tiek pārveidots par `::1` un izmaksā apmēram 2 sekundes uz vienu pieprasījumu, ja backend darbojas tikai ar IPv4). Aizmugurējam serverim pieder aparatūras resursu kopums — kameras, DAQ sensori, izlīdzināšanas profili, kadru buferi — tādējādi SDK skripti var darboties vienlaikus ar grafisko lietotāja saskarni (GUI), nekonkurējot par seriālajiem portiem vai tīkla kartes joslas platumu.

Jums būs pieejamas trīs saskarnes:

1. **`ChlorosLocal` + brīvās funkcijas** (`process_folder`, `process_lattice_capture`) — attēlu apstrādes cauruļvads. Ar vienu Python izsaukumu varat apstrādāt visu mapi, veicot kalibrēšanu, debayerēšanu un indeksu eksportēšanu.
2. **„Smart-connect“ rokturi** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Atveriet pastāvīgu aizmugures sesiju reāllaika aparatūrai. Tā pati „smart-prep” darbības secība kā GUI: tīkla pārbaude, līmeņa automātiska izvēle, PTP, AE sākotnējā iestatīšana, GPIO trigera konfigurācija.
3. **`ChlorosProject` / `open_project`** — Ielādē saglabātu projektu (mapes ar `cameras.json` + `sensors.json` + `project.json`), vienlaikus pieslēdz tā kameras, matricas un gaismas sensorus, kā arī vadītu attēlu uzņemšanu ar nosauktiem rokturiem.

Virsmas 1 un 2 **automātiski palaista vietējo aizmuguri**, ja tā vēl nav klausīšanās režīmā (tā pati komplektā iekļautā binārā programma, ko palaida GUI/CLI) — tādējādi vienkāršs skripts darbojas no jaunas komandu rindas, bez nepieciešamības vispirms palaist aizmuguri: `ChlorosLocal` (un `process_folder()` / `process_lattice_capture()`), `connect_camera()`, `connect_array()`, `connect_daq_sensor()`, `discover_lattice_cameras()` un `discover_daq_sensors()`. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` un `analyze_array_network()` nekad netiek palaisti: tiem ir nepieciešams darbojošs backend. Norādiet `auto_start_backend=False`, lai atteiktos (piemēram, ja norādīts uz attālo backend, kas nekad netiek palaists). Skatīt [Backend automātiskā palaišana](#backend-auto-start). Surface 3 darbojas citādi: `open_project()` nepieņem `auto_start_backend` parametru, un `connect_all()` nekad nepalaiž backend — tas vienreiz pārbauda `http://127.0.0.1:5000` un, ja nekas neatbild, pārslēdzas uz tiešu (bez aizmugurējās sistēmas) `lattice_sdk` ierīces vadību. Tam ir nepieciešama Arena SDK darblaika vide un OpenCV (`cv2`), kas jāimportē jūsu Python vidē, ko SDK ratiņš neinstalē; bez tiem katra saglabātā kamera un masīvs tiek ziņots kā kļūda, norādot iemeslu. Projekta gaismas sensoriem nav ceļa bez backend: ja backend nedarbojas, katrs saglabātais sensors tiek ziņots kā kļūda, norādot iemeslu (sk. [`SensorHandle`](#sensorhandle)). Tikai `proj.process()` un `stream(..., overlays=True)` lēni veido `ChlorosLocal()` (kas veic automātisku palaišanu).

Visi trīs ir auth-gated: vienreiz palaidiet `chloros-cli login` uz datora vai piesakieties, izmantojot darbvirsmas GUI. Kādu izņēmumu izraisa izsaukums bez derīgas sesijas, ir atkarīgs no saskarnes (skatīt [Izņēmumi](#exceptions)): `ChlorosAuthenticationError` no `ChlorosLocal` (un `process_folder()` / `process_lattice_capture()`), `ChlorosConnectError` no smart-connect izsaukumiem un to sesijām, `status: "error"` ar `error_code` no `analyze_array_network()`, kā arī ieraksts zem `errors` `connect_all()` ziņojumā.

Prasības:
- Python 3.7+ (kā norādīts pakotnē; izstrādāts/testēts ar 3.10)
- Vietējā datorā instalēta „Chloros Desktop“ (backend binārā programma ir iekļauta instalētājā)
- Aktīva „Chloros+“ pieteikšanās **jebkurā maksas „Chloros+“ līmenī (Copper vai augstāk)**; bezmaksas**Iron**līmenim nav piekļuves SDK/CLI. Tas tiek piemērots**no serverapuses**: katram pieprasījumam ar SDK/CLI atzīmi ir jābūt gan aktīvai sesijai, gan maksas plānam. Plānam, kas ir zemāks par Copper, tiek piešķirts kļūdas kods `403` kopā ar `error_code: PLAN_UPGRADE_REQUIRED` (`ChlorosLicenseError` no `ChlorosLocal`, `ChlorosConnectError` no „smart-connect” izsaukumiem). Izslēgtajam zvanītājam tiek piešķirts `401` / `AUTH_REQUIRED`, nevis (`ChlorosAuthenticationError` no `ChlorosLocal`, `ChlorosConnectError` no „smart-connect” zvaniem) — abi ir atšķirīgi, jo, atkārtoti palaistot `chloros-cli login` novērš kļūdu `401`, bet nevar novērst kļūdu `403`, kuras novēršanai ir nepieciešama plāna atjaunināšana.
- Lietošana bezsaistē tiek atbalstīta plāna papildlaika periodā: līmenis tiek nolasīts no servera validācijas keša (5 minūtes) vai parakstītā, ar ierīci saistītā licences keša (30 dienas mēneša plāniem, līdz abonementa termiņa beigām gada plāniem). Kad šis papildlaiks beidzas, plāns pāriet uz bezmaksas līmeni, un SDK/CLI piekļuve tiek pārtraukta, līdz ierīce vismaz reizi var sazināties ar serveri. Kļūda `chloros-cli status` (`GET /api/license-status`) paliek pieejama bezmaksas līmenī, tādējādi ļaujot redzēt tās cēloni.
- Windows 10/11 64 bitu versija, **„Ubuntu“ 22.04 LTS vai jaunāka versija**vai „Jetson“ (JetPack 6). „Ubuntu“ 20.04**netiek** atbalstīta: `.deb` atkarības ir atvasinātas no tā, ar ko ir saistīts backend, ieskaitot `libc6 (>= 2.35)`, un Focal piegādā glibc 2.31.

---

## Instalācija

Python SDK ir plāns Python slānis virs Chloros backend. Ja vien jūs neizmantojat backend citā datorā (skatiet [Remote-Backend Mode](#remote-backend-mode-pip-only-host-via-tunnel)), jums ir nepieciešams **vietējā datorā instalēts Chloros darbvirsmas pakotne** (Windows instalētājs vai Linux `.deb`), kas ietver DAQ gaismas sensorus. Pakete nodrošina backend bināro failu, Arena SDK izpildes vidi LATTICE kamerām un kalibrēšanas komplektus.

Jaunākās lejupielādes: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### 1. solis — Instalējiet Chloros platformas paketi

#### Windows (.exe)

1. Lejupielādējiet `Chloros Installer 1.3.0.exe` no lejupielādes lapas.
2. Palaižiet instalētāju un izpildiet vedņa norādījumus. Noklusējuma instalācijas ceļš ir `C:\Program Files\Chloros\`.
3. Vismaz vienu reizi palaidiet Chloros un piesakieties ar savu „Chloros+” kontu.

Instalētājs ļauj backendam (`chloros-backend.exe`, nekas cits) iziet cauri Windows ugunsmūrim katrā tīkla profilā: UDP ieeja kameru straumēm, PTP, mDNS un DAQ-E straumei, kā arī TCP ieeja tikai no vietējā apakštīkla, lai DAQ-E varētu izveidot savienojumu atpakaļ ar datoru programmatūras atjaunināšanas laikā ([`update_firmware()`](#daq-e-device-settings)). Atinstalētājs noņem abus noteikumus. Backend HTTP API jebkurā gadījumā paliek `127.0.0.1`.

Vadītāja grupā **Sistēmas iestatījumi** ir divas opcijas, abas pēc noklusējuma ir atzīmētas:

| Opcija | Darbība | Atteikšanās no automātiskās instalācijas |
| --- | --- | --- |
| **Iespējot garus Windows ceļus** | Ļauj projekta mapēm atrasties dziļāk par Windows 260 rakstzīmju ceļa ierobežojumu. Ja garie ceļi ir atspējoti (Windows noklusējums), projekta mape, kas ir pārāk dziļa tās produktiem, tiek noraidīta, pirms kaut kas tiek apstrādāts — skatiet [Problēmu novēršana](#troubleshooting-pointers). | `/MERGETASKS="!longpaths"` |
| **Izslēgt Chloros aizmuguri no „Microsoft Defender” skenēšanas** | Pārtrauc „Defender” reāllaikaskenēšanu katram failam, ko ieraksta kameras uzņemums. Pie augstas uzņemšanas ātruma skenēšana aizņem apmēram 30 % no ātruma: mērīts ar ROI 512 uz 9 kameru masīva, nepārtraukta RAW uzņemšana darbojās ar ātrumu 52,7 uzņēmumi/s ar skenēšanu un 77,7 uzņēmumi/s, izslēdzot backend. | `/MERGETASKS="!defenderexclusion"` |

Nepārtraukta uzņemšana ziņo par Defender slodzi kā `defender_cores` (skatīt [`ContinuousHandle`](#continuoushandle)).

#### Linux amd64 (.deb)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

Instalējiet ar `apt install ./…`, nevis `dpkg -i`: apt instalē arī Python SDK prasības, kuras pakete ieteic, nevis prasa (skatīt [2. solis](#linux-deb)).

#### Linux arm64 — „Jetson” (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

„Jetson Orin” / „Orin NX” / „Orin Nano” ar JetPack 6 (L4T R36). Tā PyTorch ir JetPack 6 CUDA versija, tādēļ GPU apstrāde notiek uz Orin GPU; tā izmanto CUDA un cuDNN bibliotēkas, kuras instalē JetPack 6 (`sudo apt install nvidia-jetpack` attēlā, kurā tās trūkst).

#### Linux: uztveršanas buferi un aizmugures pakalpojums

Abi .deb faili palielina kodola soketa uztveršanas buferus, kas nepieciešami LATTICE kameru straumēm, līdz 32 MB (`/etc/sysctl.d/60-chloros-gige.conf`). Pēc kodola noklusējuma kameras paketes tiek izmestas kodolā, un masīvam zūd kadri un kadru ātrums; `chloros-cli lattice network` ziņo par buferiem, kad to apjoms ir zems. Skatiet CLI atsauces sadaļu [Linux serveri — soketa uztveršanas buferi](cli-reference.md#linux-hosts--socket-receive-buffers).

Datorā bez monitora palaidiet backend kā pakalpojumu **kā savs lietotājs**, lai skripts atrastu darbojošos backend (`connect_all()` nekad nepalaiž backend):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
```

Tad backend darbojas kā jūs: tā ierakstītie dati pieder jums, un tas izmanto jūsu `chloros-cli login`. Root `chloros-backend.service` joprojām ir instalēts datoros, kuros tas ir iespējots, bet katrs fails, ko root backend ieraksta, pieder root. Skatīt CLI atsauces sadaļu [Aizmugurējā procesa palaide kā pakalpojums](cli-reference.md#running-the-backend-as-a-service-headless).

Pakalpojuma palaistais aizmugurējais process ir koplietošanas režīmā, un klients to nekad neaptur: SDK skripti un `chloros-cli` to izmanto tā, kā to atrod (`ChlorosLocal.shutdown_backend()` aptur tikai to backend, ko `ChlorosLocal` pats ir palaidis), un `GET /api/status` nosauc vienību kā `service`. Pārstartējiet to ar `sudo systemctl restart chloros-backend@$USER.service`. Paketes atjaunināšana aptur aizmugures pakalpojumus instalācijas laikā, tāpēc darbojošs skripts zaudē savas kameras un sensorus, un pēc tam atkārtoti palaista katru no tiem, kas bija ieslēgti; pakotnes noņemšana tos aptur un atslēdz tos, kas bija ieslēgti. Vienlaikus darbojas tikai viens backend, tāpēc ieslēdziet vienu vienību: otrā atteiksies sākt darbu un paliks `failed` (iziešanas statuss 98, netiek pārstartēts), un tā `systemctl status` norāda iemeslu. Lai pārslēgtos starp vienībām, atspējojiet to, kuru neizmantojat.</id></sn>

### 2. solis — Python SDK instalēšana

**Chloros instalētājs ietver atbilstošu SDK „wheel“ failu.** Katrs Windows instalētājs un Linux .deb fails uz diska izvieto failu `chloros_sdk-X.Y.Z-py3-none-any.whl`, kas precīzi atbilst GUI / CLI / backend versijai. Jums nav jāseko līdzi PyPI, lai saglabātu sinhronizāciju.

#### Windows

Instalētājs automātiski palaista `pip install`, izmantojot komplektā iekļauto wheel failu un jūsu sistēmas Python (vēlams `py.exe` palaidējs, ja tas nav pieejams, tiek izmantots `python -m pip`). Nav nepieciešama nekāda rīcība — pēc veiksmīgas instalācijas `import chloros_sdk` darbojas jūsu Python vidē. Ja datorā nav Python, instalētājs klusi izlaiž šo soli, un GUI + CLI turpina darboties.

#### Linux (.deb)

Abi .deb faili (x86_64 un Jetson) instalē SDK sistēmai `python3`, tādējādi `import chloros_sdk` darbojas pēc instalēšanas, neizmantojot ne pip, ne tīklu. Paketes (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` un projekta lādētāji) tiek ievietoti `/usr/lib/python3/dist-packages`, un SDK komanda `chloros-mcp` ([MCP serveris](#mcp-server-ai-assistants)) tiek iekļauta `/usr/bin`, un visi šie komponenti pieder paketei: to atjaunināšana tos aizstāj, bet paketes noņemšana — arī tos.

SDK paša prasības, `python3-numpy` un `python3-requests`, ir **ieteicamas, nevis obligātas** (GUI, CLI un backend ir patstāvīgi): `sudo apt install ./chloros_<version>_amd64.deb` (`…_arm64_jp6.deb` uz „Jetson”) tos instalē, bet `dpkg -i` — nē. Instalēšanas izvade ziņo par SDK versiju, ko `python3` importē, par jebkurām trūkstošām prasībām, kā arī par `chloros_sdk`, kas atrodas citur `python3` ceļā un aizstāj pakotnes kopiju (piemēram, tādu, kas iepriekš instalēta ar `pip install --user`).

Virtuālajā vidē (virtualenv), kas neredz sistēmas pakotnes, tajā instalējiet komplektā iekļauto „wheel” failu:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Jetson ierīcēm, kas darbojas izolētā vidē (air-gapped), šis process notiek pilnīgi bezsaistē — SDK un „wheel” faili jau atrodas diskā.

#### Publiskais PyPI

Tām sistēmām, kurās darbojas tikai pip (nav instalēta Chloros darbvirsmas pakete; šādā gadījumā SDK vada aizmuguri citā datorā, skatiet [Attālās aizmugures režīms](#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

PyPI piedāvā jaunāko stabilo versiju. Uzturiet SDKu tādā pašā versijā kā Chloros aizmugurējo sistēmu, kuru tas vada (skatiet [Versiju pārvaldība un saderība](#versioning--compatibility)). DAQ-M palīgprogrammai `DLS` ir nepieciešama arī `bleak`: `pip install "chloros-sdk[camera]"` (vai `pip install bleak`).

#### Pārbaudiet

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Nepieciešama Chloros+ abonementa reģistrācija.** Visiem SDK izsaukumiem ir nepieciešama aktīva Chloros+ pieteikšanās. Izpildiet `chloros-cli login user@example.com 'YourPassword'` vienu reizi katrā datorā; pieteikšanās dati tiek saglabāti `~/.chloros/`.

### Vai man ir nepieciešama darbvirsmas pakete?

Lielākajai daļai darba plūsmu ar pip paketi vien **nepietiek**. Šeit ir norādīts, kas nepieciešams katrai SDK saskarnes virsmai:

| SDK saskarnes virsma | Vai nepieciešama darbvirsmas pakete? | Kāpēc |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Jā**(lai palaistu vienu)**/ Nē** (ja backend jau ir pieejams) | Izmantot jau darbojošos backend (datora lietojumprogrammas vai kāda cita palaista skripta); citādi automātiski palaist instalēto: `/usr/lib/chloros/chloros-backend` (Linux) vai `<install folder>\backend\chloros-backend.exe` (Windows; pēc noklusējuma `C:\Program Files\Chloros\backend\`, pielāgota instalācijas mape tiek nolasīta no instalētāja ieraksta). Norādiet `backend_exe=`, lai to nosauktu pēc saviem ieskatiem. |
| `chloros-mcp` ([MCP serveris](#mcp-server-ai-assistants)) | **Jā**(lai palaistu vienu)**/ Nē** (aizmugurējais serveris jau ir pieejams) | Izveidots uz `ChlorosLocal` bāzes, tātad tāpat kā iepriekšējā rindā. |
| `read_image_audit_tags` | **Nē** | Izlasa apstrādātā attēla metadatus (tikai Pythona standarta bibliotēkā). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Jā**(vietējais)**/ Nē**(attālinātais) | Tīri HTTP klienti, izmantojot aizmuguri. Vietējā aizmugure → nepieciešams darbvirsmas pakotne. Attālinātā aizmugure → `backend_url=`**caur tuneli** (skatīt Attālinātās aizmugures režīmu — piegādātās aizmugures piesaista tikai lokālo atgriezenisko saiti). |
| `ChlorosProject` / `open_project` | **Jā** | Vadīta saglabāto projektu apstrāde caur aizmuguri. |
| Tiešas LATTICE klases (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Jā** | Nepieciešama Arena SDK darblaika vide, kas ir iekļauta darbvirsmas paketē, un OpenCV (`cv2`) jūsu Python vidē, ko ne darbvirsmas pakete, ne SDK neinstalē (`pip install opencv-python`). Pretējā gadījumā `CAMERA_AVAILABLE` importēšanas brīdī ir `False`. `DLS` ir nepieciešams arī `bleak`. |

### Attālinātais aizmugures režīms (tikai pip-hosts, izmantojot tuneli)

> **Uzstādītais aizmugures modulis nav sasniedzams pa LAN.** Tas klausās
> tikai uz loopback (`127.0.0.1` / `::1`), tādēļ `backend_url="http://<lan-ip>:5000"`
> **nevar sasniegt instalētu Chloros**. Lai vadītu backend citu
> datoru, pašam jāpāradresē tā loopback ports un jānorāda SDK uz
> tuneli:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"     # the tunnel endpoint
serials = ["213800234", "214000533"]   # master first

# auto_start_backend=False: the tunnel URL looks local, so without it a tunnel
# that is down would start this machine's own backend, if one is installed.
arr = chloros_sdk.connect_array(serials, backend_url=BACKEND,
                                auto_start_backend=False)
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                     backend_url=BACKEND,
                                     auto_start_backend=False)

# The backend opens every path on ITS machine: pass absolute paths valid there.
# (A relative path would be made absolute against this machine's working folder.)
arr.capture("/home/chloros/captures/run1", processing="raw")
```

Bezmonitora / CI / robotikas serveri var saglabāt vienu datoru ar pilnu darbvirsmas instalāciju kā „Chloros serveri” un `pip install chloros-sdk` visur citur — taču datu pārraide starp tiem notiek caur iepriekš minēto lietotāja izveidoto tuneli, nevis tiešu LAN URL.

> **`ChlorosLocal` uz datora, kurā darbojas tikai pip.** `ChlorosLocal(backend_url=BACKEND)` izveido savienojumu ar aizmugurējo serveri, kuru tas var sasniegt bez darbvirsmas instalācijas: tas meklē vietējo aizmugurējā servera bināro failu tikai tad, ja tam ir jāpalaiž (ja nekas neatbild un parādās `auto_start_backend=True`). Līdz versijai 1.2.0 tas vispirms meklēja bināro failu un izraisīja kļūdu `ChlorosBackendError` („Chloros backend not found…”) uz datora, kurā darbojas tikai pip, pat ja backend bija sasniedzams. Mapes, kuras jūs norādāt (`process_folder`, `import_images`), tiek pārbaudītas šajā datorā un nolasītas no backend, tāpēc, izmantojot attālo backend, tām abos datoros jānosauc viena un tā pati mape, piemēram, koplietotais disks.

### Darba plūsma, kurā tiek izmantots tikai DAQ (dators, kurā darbojas tikai pip)

`pip install chloros-sdk` vien nevar vadīt DAQ sensoru: DAQ sensoru vienmēr vada caur Chloros backend ar `connect_daq_sensor()`:

- **Sensors šajā datorā:** šeit instalējiet Chloros darbvirsmas paketi. `connect_daq_sensor()` palaista savu backend, ja neviens vēl nedarbojas.
- **Sensors citā datorā:** šim datoram ir nepieciešama darbvirsmas pakete; piekļūstiet tā backendam caur tuneli [Remote-Backend Mode](#remote-backend-mode-pip-only-host-via-tunnel). Backend atver sensoru savā datorā, tādējādi DAQ-U ir pievienots šim datoram, DAQ-M atrodas tā Bluetooth darbības rādiusā, un DAQ-E atrodas tīklā, kuram tas var piekļūt.

```python
import chloros_sdk

# http://127.0.0.1:5000 is the local desktop install's backend, or the tunnel.
with chloros_sdk.connect_daq_sensor() as daq:   # smart-detect USB / BLE / ETH
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Jebkurā gadījumā backend piemēro „Chloros+” plānu: jāpiesakās datorā, kurā darbojas backend (`chloros-cli login` vai datora lietotne), izmantojot jebkuru maksas līmeni (Copper vai augstāku). Ja no backend ir izieti vai tiek izmantots plāns, kas ir zemāks par „Copper”, tiek izraisīts `ChlorosConnectError`, kurā ir norādīts backend iemesls (`AUTH_REQUIRED` / `PLAN_UPGRADE_REQUIRED`).

---

## Ātrs sākums

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Augstākā līmeņa API indekss

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by CameraSession.capture / ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job
chloros_sdk.ContinuousHandle                      # handle for an array continuous() capture
chloros_sdk.GrabResult                            # dict returned by grab() / yielded by frame_stream()
chloros_sdk.FrameFetchError                       # raised by grab()/frame_stream() on a refusal

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()
chloros_sdk.daq_bindings() / set_daq_bindings(cameras=, arrays=)   # which DAQ feeds which camera
chloros_sdk.daq_band_presets(filter_code=None)    # band sets for Ambient + Object reflectance
chloros_sdk.light_sensor_reflectance(ambient_id, object_id, ...)   # two DAQs -> reflectance + indices
chloros_sdk.time_sync_status() / time_sync_peers() / time_sync_cameras()   # PTP grandmaster
chloros_sdk.restart_time_sync() / set_time_sync_priority(priority1=, priority2=)

# === Camera utilities (the CLI's lattice commands; by serial) ===
chloros_sdk.camera_color_profile(serial) / set_camera_color_profile(serial, ...)
chloros_sdk.camera_color_adjust(serial) / set_camera_color_adjust(serial, ...)
chloros_sdk.camera_highlights(serial) / set_camera_highlights(serial, value)
chloros_sdk.camera_white_balance_once(serial) / set_camera_white_balance(serial, ...)
chloros_sdk.camera_presets() / apply_camera_preset(serial, preset)
chloros_sdk.camera_filter_models() / camera_filter_model_info(name)
chloros_sdk.camera_firmware_status() / update_camera_firmware(serial)
chloros_sdk.camera_power_probe(serial) / sleep_camera(serial) / wake_camera(serial)
chloros_sdk.calibrate_camera_with_target(serial, ...)
chloros_sdk.network_adapters(...) / fix_network_settings()
chloros_sdk.apply_vignette_correction(input_dir, output_dir, ...)
chloros_sdk.read_dls(mac, ...) / probe_system(...) / system_info()

# === Transient cameras, alignment profiles, indices (cameras opened for one command) ===
chloros_sdk.start_camera_continuous(serial, ..., daq=None, daq_tolerance_ms=None)  # → CliJob; a DAQ bound to the camera records one .daq per frame (daq=False: none)
chloros_sdk.start_multi_camera_stream(serials, ...)   # → CliJob
chloros_sdk.capture_cameras_transient(serials, ...)
chloros_sdk.test_array_sync(serials, count, ptp_settle_s=None) / detect_array_master(line) / analyze_array_models(models)
chloros_sdk.calibrate_alignment_profile(serials, save_path, ...)
chloros_sdk.capture_aligned_multiband(profile_path, ...) / start_aligned_stream(profile_path, ...)  # → dict / CliJob
chloros_sdk.alignment_profile_info(path) / reorder_alignment_profile(path, ...)
chloros_sdk.index_catalog() / compute_index(input_path, ...) / capture_live_index(profile_path, ...)
chloros_sdk.CliJob                                # stats() / stop() / wait()

# === Project lifecycle ===
chloros_sdk.open_project(path, relink=None)       # → ChlorosProject
chloros_sdk.project_folder() / set_project_folder(path) / reset_project_folder()
chloros_sdk.run_project_recipe(recipe, project_path)  # `chloros-cli project run`
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics               # DLS: DAQ-M over Bluetooth; needs bleak

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosProjectFilesMissingError      # a ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True when chloros_sdk.project imported; open_project()
                                 # still raises ImportError without the project loaders
```

---

## Attēlu apstrāde — `ChlorosLocal`

Galvenā cauruļvada klase. Palaiž aizmuguri, kad tā tiek izveidota (ja neviena neatbild un `auto_start_backend=True`), izveido/konfigurē projektus, uzrauga progresu, atgriež kopsavilkumus pēc izpildes.

### Konstruktors

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Metodes

| Metode | Apraksts |
| --- | --- |
| `create_project(project_name, camera=None)` | Izveido jaunu projektu (pēc izvēles ar kameras veidni, piemēram, `"Survey3N_RGN"`). |
| `open_project(project_path, relink=None)` | Atver saglabātu projektu (tā mapi vai nosaukumu projektu mapē) un padara to par atvērtu projektu. Pārvietota projekta ceļi tiek pārvirzīti uz mapi, kurā tas atrodas tagad; `relink` nosauc mapi, kurā tagad atrodas pārējie trūkstošie faili. Atgriež aizmugurējās sistēmas atbildi ar `source_files`; izraisa `ChlorosProjectFilesMissingError`, ja neko nevar atrast. Skatīt **Pārvietots projekts** zemāk. |
| `import_images(folder_path, recursive=False)` | Importē RAW/TIF/JPG/DNG attēlus **un `.daq` gaismas sensora ierakstus**. Atgriež `count` (attēlus) un `scan_count` (ierakstus). Brīdina tikai tad, ja mapē nav ne viena, ne otra. |
| `export_light_sensor(daq=True, csv=True)` | Ieraksta kalibrētos `.daq` + `.csv` datus par katru gaismas sensora ierakstu projektā, mapes `<project>/Light Sensor/`. Skatīt [Gaismas sensora ieraksti](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | Iestatīt apstrādes slēdžus. `indices` ir iepriekš iestatīti nosaukumi (`NDVI`, `GNDVI`, `OSAVI`, …), katrs pievienots projektam atbilstoši savas kameras spektrālajiem diapazoniem; nosaukums, kas nav iepriekš iestatīts, vai kuru spektrālie diapazoni nevar veidot (`NDRE` ir nepieciešami RE un NIR diapazoni, kurus neviena kamera atsevišķi nenodrošina), tiek piešķirts nosaukums `UserWarning` un netiek iekļauts indeksa attēls. `daq_log_path` nosauc gaismas sensora ierakstu (fails `.daq` vai `.csv`, vai to mape), ar kuru tiek salīdzināts katrs kadrs gan LATTICE, gan Survey3, nevis ar attēliem importētie ieraksti; ja ceļš nav norādīts vai tajā nav ieraksta, tiek izmantoti importētie ieraksti. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Palaiž apstrādes ķēdi. Atgriež `{"status": "complete", "async": False}`, kā arī `summary` atslēgu, ja aizmugurējā sistēma to nodrošina — skatīt [Kopsavilkums un padomi pēc izpildes](#post-run-summary--hints) — `export_check_failure`, ja izpildes eksportētos datus nevarēja pārbaudīt, un `image_failure`, ja tika zaudēts attēls. `wait=False` atgriež `{"status": "started", "async": True}` uzreiz. |
| `get_config()` / `get_status()` / `status()` | Pārbaudiet aizmugures sistēmas stāvokli. |
| `logout()` | Iziet no sistēmas: dzēš kešatmiņā saglabātās autentifikācijas datus un atbrīvo šīs ierīces vietu (rezultātā — `device_deregistration`). Pašreiz notiekošais apstrādes cikls netiek apturēts: tas tiek pabeigts tajā režīmā, kādā tas tika uzsākts (rezultātā — `run_in_progress: true` un `run_mode`), un nākamais cikls izmanto standarta apstrādi. |
| `shutdown_backend()` | Pārtrauciet aizmugurējo procesu, ja šo instanci ir palaidusi; aizmugurējais process, kas tika atrasts darbojošs, tiek atstāts mierā. `with` bloka atstāšana vai instances atbrīvošana ar atkritumu savākšanas mehānismu to nepārtrauc: aizmugurējais modulis turpina darboties nākamajam skriptam un apstājas pats pēc 30 minūtēm, ja to nekas neizmanto. Aizmugurējam modulim tiek pieprasīts izslēgties, tāpēc tas vispirms atbrīvo savas kameras un gaismas sensorus un pabeidz jebkādu ierakstīšanu; tas tiek pārtraukts tikai tad, ja tas joprojām darbojas pēc 10 sekundēm. |
| `discover_cameras()` | Atklāj LATTICE kameras **caur šīs instances backend** (`/api/camera/discover`). Atgriež vārdnīcu sarakstu (`serial`, `model`, `ip`, …) — tādā pašā formā, kādu redz GUI/CLI. Tukšs saraksts, ja nekas nav atrasts vai backend nav sasniedzams. |
| `camera_capture(output_dir, format="tiff", **settings)` | Uztver vienu kadru**caur backend**(automātiski palaists ar šo rokturi), lai tas tiktu sagatavots tāpat kā GUI/CLI (noklusējumā 12 biti, pūla atkārtota izmantošana, iegultie kalibrēšanas metadati). Mērķi noteikt ar `serial=` vai `device_index=`; nododiet `exposure`/`gain`/`pixel_format`/`preset` kā `**settings`. Atgriež vecā tipa metadatu vārdnīcu (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Ģenerē pārklājumu apvienotus priekšskatījuma kadrus no apvienotās kameras — viegls MJPEG klients, izmantojot aizmugurējās sistēmas `/api/camera/<serial>/stream-annotated` maršrutu (zebra / režģis / krustlīnijas / histogramma / kontrasta maksimumu atzīmēšana / punkta iezīmēšana servera pusē). `decode=True` ģenerē BGR masīvus un tam nepieciešams OpenCV (`cv2`), ko SDK neinstalē; `False` ģenerē neapstrādātus JPEG baitus. Pieejams arī katram projektam atsevišķi kā `ChlorosProject.stream(overlays=True)`. |

**Darba gaitā tiek saglabāts projekts.** Kamēr notiek apstrādes izpilde — šī skripta, lietotnes, `chloros-cli process` vai `chloros-mcp` —, aizmugurējā sistēma atsakās izveidot, atvērt, importēt vai mainīt projekta apstrādes iestatījumus (HTTP 409 `PROCESSING_IN_PROGRESS`): `create_project()` un `import_images()` izraisa `ChlorosProcessingError`, un `configure()` izraisa `ChlorosConfigurationError`, savukārt `process()` ar režīmu, kas atšķiras no pašreizējā, izraisa `ChlorosProcessingError` (izpilde saglabā to režīmu, kādā tā tika uzsākta), katram no tiem pievienojot aizmugurējās sistēmas ziņojumu. Projektā saglabātais ieraksts pievienojas tam, kad izpilde beidzas (to norāda tā atbildes `project_add`). Gaidiet, līdz izpilde beigsies (`get_status()`), tad turpiniet.**Pārvietots projekts.** Projekta `project.json` katram avota failam (katram attēlam un gaismas sensora ierakstam) piešķir nosaukumu pēc tā pilnā ceļa, tāpēc, pārvietojot projekta mapi — vai visu projektu mapi uz citu disku —, tie joprojām tiek nosaukti pēc vecās atrašanās vietas. Katra atvēršana pati izlabo to, ko var: failu, kas atradās projekta mapē, meklē mapē, kurā projekts atrodas tagad, un katrs tur atrastais ceļš tiek pārvirzīts un saglabāts (`project.json` vispirms tiek dublēts kā `project.json.before-relink-<date>-<time>`). Ceļš tiek pārvirzīts tikai uz failu, kas tur atrodas. Faili, kas atradās ārpus projekta mapes — centrmezgla sesija, importēšana ar funkciju „Pievienot mapi” — tiek atrasti tikai ar `relink=<the folder they are in now>` (vai jebkuru mapi virs tās). `open_project()` atgriež `source_files`: `total`, `found`, `missing`, `missing_roots` (mapes, kurās atradās trūkstošie faili), `missing_outside_project` un `relink` (kas tika pārvirzīts, no kurienes uz kurieni). Projekts, kurā trūkst daži faili, atveras kopā ar pārējiem; projekts, kurā nav atrasts neviens failu, netiek atvērts, un `open_project()` izraisa kļūdu `ChlorosProjectFilesMissingError` (`.missing_roots`, `.audit`; HTTP 409 `PROJECT_SOURCES_MISSING`). CLI kļūdas `project open --relink` un `chloros-mcp``open_project` (`relink_folder`) darbojas tāpat.

Izmantojiet kā konteksta pārvaldnieku, lai garantētu tīrīšanu:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Gaismas sensora ieraksti — kalibrēti `.daq` + `.csv`

DAQ-U / DAQ-M / DAQ-E ierakstus var veikt **bez** to kalibrēšanas paketes. Tieši to
publiskie [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
ierakstītāji (`record_daq.py`) dara pēc noklusējuma: tie ieraksta neapstrādātos sensoru skaitījumus un marķē
failu tā, lai Chloros **pēc sērijas numura** — vispirms no vietējās kešatmiņas, tad no MAPIR Cloud — un piemēro to importēšanas laikā.

Chloros rezultātu atkal saglabā kā divus produktus katram ierakstam, zem
`<project>/Light Sensor/`:

| Produkts | Kas tas ir |
| --- | --- |
| `<name>_calibrated.daq` | Pārstrādājams arhīvs — tā pati shēma kā reāllaika ierakstam, tagad norādot paketi, kas to izveidojusi. Tās atkārtota importēšana to **ne** kalibrē otrreiz. |
| `<name>_calibrated.csv` | Spektrālā starojuma intensitāte W/m²/nm uz sensora paša viļņu garuma režģa, viena rinda katram nolasījumam, kā arī fotometriskās kolonnas (kopējā jauda, fotopiskais/skotopiskais lukss, PPFD un tā sadalījums pa zilo, zaļo un sarkano spektru, maksimālā viļņu garuma vērtība). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Tikai sensori bez komplekta (DAQ-A).** Neapstrādāti spektrālie sensora skaitļi — *ne* starojuma intensitāte. Skatīt zemāk. |

`<name>` ir paša ieraksta faila nosaukums. Diviem ierakstiem dažādās mapēs var būt kopīgs
vienu — katram uzņēmumam atsevišķus rādījumus (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`), kas saglabāti
vienā un tajā pašā milisekundē ar divām kamerām — un katrs joprojām saņem savus rezultātus: pirmais saglabā
`<name>`, bet nākamais pievieno mapes, kas to atšķir, **pēc** nosaukuma
(`<name>__<capture>_daq_calibrated.daq`), tādējādi ieraksta datums un laiks joprojām ir pirmajā vietā.

`process()` veic šo eksportu kā vienu no saviem posmiem. Tam **nav** nepieciešami attēli:
atsevišķi lidojošs gaismas sensors ir pirmklasīga darba plūsma, un šādam projektam pēc būtības nav
nekādu attēlu.

**DAQ-A ieraksti tiek eksportēti kā neapstrādāti skaitļi.** DAQ-A sērija ir radusies pirms sērijas
pakotņu sistēmas ieviešanas un tai nav pakotnes, ko lejupielādēt — tā tiek kalibrēta laukā, izmantojot
atstarošanas mērķi, tāpēc tai nekad tāda nav bijusi nepieciešama. Šie ieraksti tiek eksportēti
ar `_raw` sakni, nevis `_calibrated`: tas ir atšķirīgs faila nosaukums, nevis atzīme
failā, jo nosaukumam ir jāiztur nosūtīšana pa e-pastu kā vienkāršam nosaukumam.
`.csv` galvenē ir norādīts `raw spectral sensor counts (NOT irradiance)` un brīdinājums, ka
vērtības ir salīdzināmas **vienā** failā — tieši to, kādam nolūkam tiek izmantota mērķa balstīta kalibrēšana
tās izmanto — nevis starp sensoriem. No jaudas atkarīgās fotometriskās kolonnas (kopējā jauda,
fotopiskais/skotopiskais lukss, PPFD) atgriež **NULL**, nevis tiek integrētas no skaitļiem.

DAQ-U / DAQ-M / DAQ-E, kura pakete vienkārši nevarēja tikt lejupielādēta, joprojām tiek **izlaista**,
nevis ierakstīta neapstrādātā veidā: šajā gadījumā pakete pastāv, un „atjaunot savienojumu un pārstrādāt” ir reāls ieteikums.

Vecākas **v1.01 / v1.02** ierakstīšanas (tos raksta DAQ-A-SD) nesatur atsevišķu epohu katram nolasījumam,
tikai faila rakstīšanas laiku. Attēla↔lejupvērstās gaismas saskaņotājs tos joprojām noraida — saskaņot
kadru ar ierakstīšanas laiku būtu nepamanāmi nepareizi — taču eksportētājs tos nolasa, un
failā CSV tiek izdrukāts `clock=daq_created_on`, tādējādi produkts norāda, uz kādiem pulksteņiem tas balstās.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Ieraksts, kura kalibrēšanas pakete nevar tikt iegūta (bezsaistē vai sensors bez
kalibrēšanas failā), tiek ziņots ar kodu `skipped` **norādot iemeslu**. Tas nekad
netiek izrakstīts kā „kalibrēts” fails, kurā ir neapstrādāti skaitļi — izveidojiet savienojumu ar internetu un
palaidiet atkārtoti, un eksportēšana tiks pabeigta.

### Progresa atgriezeniskie izsaukumi

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Kopsavilkums un padomi pēc izpildes

Pēc pabeigšanas `process()` iegūst `GET /api/processing-summary` un pievieno galveno daļu kā `result["summary"]`. Iegūšana notiek pēc labākās gribas un nekad neblokē veiksmīgu atgriešanos — ja kopsavilkums nav pieejams, `process()` pārslēdzas uz vienkāršo `{"status": "complete", "async": False}` formātu. Katrs ieraksts `summary["hints"]` — pilni teikumi ar ieteiktajiem labojumiem, piemēram, kāpēc izpilde nedeva nekādu rezultātu — tiek atkārtoti ģenerēts kā Pythona `UserWarning`, tādējādi izpildes bez rezultāta ir pašdiagnosticējošas, pat ja jūs nekad nepārbaudāt vārdnīcu:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` ir mašīnlasāmā daļa:

| Atslēga | Ko tā skaita |
| --- | --- |
| `models` | Kameru grupas izpildes laikā. |
| `images_in_groups` | Avota attēli šajās grupās. |
| `targets_found` | Atklātie atstarošanas mērķi. |
| `images_calibrated` | Attēli, par kuriem darbības cikls izveidoja atstarojuma produktu. |
| `images_failed` | Attēli, kuru apstrāde neizdevās un kuri netika eksportēti. |
| `exported_files` | **Attēlu produktu faili , ko izveidoja darba cikls**: visi produkti (debayering, priekšskatījums, starojuma intensitāte, atstarojums, indekss, mērķa attēli) visos formātos. |
| `reflectance_files` | Starp tiem esošie atstarojuma produkti (`Reflectance_Calibrated_Images`), neatkarīgi no tā, vai tos kalibrēja mērķis vai gaismas sensora (DAQ) starojuma intensitāte. Ja procesā bez mērķa tomēr tika ierakstīti atstarošanas dati, tas tiek norādīts piezīmē; tikai procesam, kurā netika ierakstīts nekas, tiek ziņots, ka trūkst atstarošanas izvades datu. |
| `clamp_masks_written` | [Unmix fiksēšanas maskas](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) — tās ir papildus informācija, nevis rezultāti, tādēļ tās nav iekļautas `exported_files` (CLI tās skaita atsevišķi tāpat). |
| `captures_left_out` | Neapstrādāti kadri projekta `captures` mapē, kurus šis izpildes cikls neuzņēma. Darbības sākumā tā gaida iepriekš uzņemtos kadrus, tāpēc šis rādītājs ir 0, ja vien uzņemšana netika saglabāta darbības laikā vai gaidīšanas laiks nebeidzās. `summary["captures_left_out_note"]` norāda, kā rīkoties ar šiem kadriem. |
| `daq_recordings_exported` / `daq_recordings_skipped` | Gaismas sensoru ieraksti, kas apzināti tiek skaitīti atsevišķi — tie nāk no cita posma un pastāv arī izpildēm, kurās vispār nav attēlu, tāpēc to iekļaušana liktu izskatīties, ka izpilde, kurā tika veikta tikai datu ieguve (DAQ), ir eksportējusi attēlus. |

Līdzās tiem: `summary["output_dirs"]` (katrs katalogs, kurā veikti ieraksti),
`summary["light_sensor_export"]`, `summary["stopped"]` (ir spēkā, ja lietotājs pārtrauca
darba gaitu, tādējādi daļējie skaitļi netiek interpretēti kā pabeigta analīze ar nepietiekamu rezultātu), un
`summary["groups"]` (sadale pa grupām).

`exported_files` tiek reģistrēts cauruļvadā **rakstīšanas brīdī**, nevis pēc tam tiek skenēts no
projekta attēlu objektiem. Paralēlās un GPU stratēģijas veido savus attēlu
objektus (GPU ceļu gadījumā — darba procesa apakšprocesos), tāpēc iepriekšējā skenēšana ziņoja
`0 file(s) written` par katru šādu izpildes ciklu un pēc tam izsniedza norādi par nulles eksportu — izpildes ciklos,
kuros viss bija darbojies. Ja jūs veidojat skriptu, balstoties uz šo skaitli, tagad veiksmīgs paralēlais izpildes cikls
ziņo par skaitli, kas nav nulle.

Izlaisto failu ziņojumos tiek norādīts iemesls, ko lasītājs faktiski konstatējis katram failam —
nelasāma shēma, trūkstošs kopums, rakstīšanas kļūda — **bez dublējumiem**, tādējādi divdesmit faili,
kas izlaisti viena iemesla dēļ, tiek uzrādīti kā viens iemesls, nevis divdesmit tā atkārtojumi.

> **`process()` netiek izraisīts, ja izpildes laikā netiek radīti attēli.** Šī ir viena no divām vietām, kur SDK un
> CLI apzināti atšķiras: `chloros-cli process` uzskata „tika pieprasīti produkti, bet neviens netika
> ierakstīts” par kļūdu un iziet ar rezultātu, kas nav nulle, savukārt SDK atgriežas normāli un ziņo par
> stāvokli caur `summary` / norādēm. Ja jūsu apstrādes ķēdei jāapstājas tukšā izpildes reizē, pārbaudiet to
> paši — pārbaudiet `summary` (vai saskaitiet failus projekta mapē), nevis paļaujieties uz
> izņēmuma neesamību. Parastie cēloņi ir ievades mape, kas netika atpazīta kā
> uzņemšanas mape, un produkti, kas tika izlaisti kā nepiemēroti esošajām kamerām (piemēram, starojums no kamerām, kas uzņem tikai RGB
> attēlus).
>
> `process()` netiek izraisīts arī tad, ja nav iespējams pārbaudīt izpildes eksportētos datus: tiek parādīts brīdinājums un iestatīts
> `result["export_check_failure"]`. Produkti tiek ierakstīti. Lai apturētu procesu šajā gadījumā, pārbaudiet šo atslēgu. Tāds pats ir gadījums, ja
> kāds attēls ir pazaudēts: tiek parādīts brīdinājums un iestatīts `result["image_failure"]`, kura gadījumā `chloros-cli process`
> beidzas ar rezultātu, kas nav nulle.

### Ērtības funkcijas

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "GNDVI", "OSAVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# process_folder() / process_lattice_capture() also take input_level=,
# array_alignment=, array_alignment_crop=, array_alignment_interpolation=,
# progress_callback=, poll_interval=, timeout=, processing_stuck_timeout=,
# backend_exe= and api_url= (NOT backend_url=). indices defaults to ["NDVI"].

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

Katra darbība noris savā `with ChlorosLocal(...)`; aizmugurējā programma, kuru tai bija jāuzsāk, turpina darboties, kad tā atgriežas, līdz nākamajam izsaukumam, un apstājas pēc 30 minūtēm, ja to nekas neizmanto. Neviena no tām nepieņem `auto_start_backend`, `radiometric_output` vai `daq_log_path`; izmantojiet `ChlorosLocal` un `configure()`. `process_lattice_capture()` nepieņem `camera`, `debayer` vai `ppk`: tas izlaiž paneļa atpazīšanu un izmanto standarta debayeru.

### Atbalstītās vērtības

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"TIFF (8-bit)"            # all-8-bit images are written as this; never raised
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer for Survey3 imagery, Chloros+ only.
                                         # Survey3 only: on a project holding LATTICE captures
                                         # (which always use the standard demosaic) configure()
                                         # or process() raises ChlorosConfigurationError; a
                                         # Texture Aware value the project saved earlier is set
                                         # to Standard with a UserWarning, as the app does. Needs a
                                         # CUDA GPU the backend's PyTorch can use (an NVIDIA
                                         # GPU, or a Jetson Orin's). Without one, process()
                                         # uses the standard debayer, and the backend prints
                                         # one "[DEBAYER] ... needs a CUDA GPU" line saying why.
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as Chloros's saved debayered (3-channel) product
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Radiometriskā izeja (LATTICE multispektrālā apstrādes ķēde)

`process` apstrādes ķēdes LATTICE multispektrālā (M3C/M3M) eksporta līmenis — `reflectance` (noklusējums), `radiance`, `sensor-response` vai `all` (katrs attēlam piemērojamais režīms) — nosaka projekta **"Eksportēt starojumu"**un**"Eksportēt atstarojumu"** produktus: `radiance` eksportē tikai starojumu, `reflectance` — tikai atstarojumu, `sensor-response` — ne vienu, ne otru (tikai debayeringa un priekšskatījuma produktus), `all` — abus. Produkti pēc debayeringa un priekšskatījuma nav maināmi šajā līmenī. `configure()` tam ir atsevišķs atslēgvārds:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Papildu izejas iespēja — produktu rakstīšana, izmantojot `custom_settings` — joprojām darbojas, bet atcerieties, ka tā aizstāj visu iestatījumu bloku (skatiet brīdinājumu zemāk). Ierakstiet atslēgas `"Export <product>"`: projekta paša `"Export radiance"` / `"Export reflectance"` iestatījumi ir prioritāri salīdzinājumā ar vecāko atslēgu `"Radiometric output"`, tādējādi šī atslēga pati par sevi neko nemaina.

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Export radiance": True, "Export reflectance": False},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (noklusējuma iestatījums) sadala kameras starojuma intensitāti ar **laika zīmogam atbilstošo DAQ lejupvērsto starojumu**, kas automātiski tiek aprēķināts no ierakstītā `.daq` (DAQ-U/M/E)**vai DAQ-M nativā `.csv`**, kas atrodas kopā ar attēliem; jebkurš kameras vai DAQ kalibrēšanas komplekts, kas lokāli trūkst, tiek**automātiski lejupielādēts no AWS** pirmajā lietošanas reizē. CLI to parāda saskaņā ar-tipa produktu slēdžiem `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.**Kura ir šī nolasījuma piederība?**`.daq` ieraksts, ko saglabā uzņemums, ir tās kameras (kameru) nolasījums, kuru tas apraksta — nosaukts blakus esošajā `<file>.daq.cameras.json` papildu ierakstā (atsevišķi, sinhronizēti un masīva ieraksti; neapstrādāta sērija nosauc katra elementa rādījumu savā manifestā) — un tiek saskaņots**tikai ar attēliem no šīm kamerām**. Tādējādi atsevišķa kamera bez saviem rādījumiem vairs neizmanto citas kameras DAQ; tai ir jāpiesaista DAQ vai jāveic ieraksts. DAQ paša ieraksts (`chloros-cli daq pool-record` vai ieraksts, kas uzsākts lietotāja saskarnē), vai jebkurš `.daq` bez šī papildu faila (ieskaitot vecākus ierakstus), joprojām darbojas ar visām kamerām.**OCN kameras saules gaismā.** Katrs OCN kalibrēšanas komplekts ir izveidots šaurjoslas apgaismojumā — viena LED katrai joslai, savukārt saules gaismā joslas sajaucas. Chloros 1.3.0 pēc joslu atdalīšanas piemēro vispārēju korekciju, pamatojoties uz saules gaismu, kas uzlabo OCN starojuma, atstarošanas un indeksa rādītājus saules gaismā. Tā darbojas visur, kur notiek joslu atdalīšana — `process()`, uzņēmumos, `grab()` / `frame_stream()` un reāllaika skatā — OCN komplektiem, kas to atbalsta; RGN kameras paliek nemainīgas. Ar agrāku versiju uzņemtie OCN attēli to saņem arī tad, ja tie tiek apstrādāti datorā, kurā ir saglabāta kalibrācija, ar kuru tie tika uzņemti (Chloros saglabā kameras kalibrāciju, tiklīdz kamera ir pieslēgta); citādi tie tiek apstrādāti tāpat kā iepriekš. Tādējādi OCN starojuma, atstarošanas un indeksa vērtības atšķiras no 1.2.0 versijas vērtībām.

> `custom_settings` **aizstāj** visu aprēķināto iestatījumu bloku (tas pēc dizaina apiet `configure()` pārējos atslēgvārdus un validāciju). Lietojot to, iekļaujiet katru `Project Settings` atslēgvārdu, kas jums ir svarīgs, kā redzams iepriekšējā piemērā.

#### Indeksa pikseļu nolasīšana (NDVI un citi veģetācijas indeksi)

**Visa skaitļa indeksa rastra ir kontrasta izstiepums katram attēlam atsevišķi, nevis −1…1 karte.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` un `JPG (8-bit)` indeksa produkti — ieskaitot 8-bitu TIFF, kādā tiek saglabāts 8-bitu attēls — glabā

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

kur `IndexMin` / `IndexMax` ir **šī kadra zemākā un augstākā galīgā indeksa vērtība**. DN 0 nav −1, un pilna skala nav +1.

Chloros marķē abus galapunktus, lai attēlojums paliktu apgriežams:

| Tag | Nozīme |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — zemāk minētā konvencija |
| `Chloros:IndexMin` | indeksa vērtība, kas saglabāta kā DN 0 |
| `Chloros:IndexMax` | indeksa vērtība, kas saglabāta kā pilna skala |

Izlasiet tos un apgrieziet, vispirms normalizējot saglabāto DN uz 0…1 atbilstoši saglabātā datu tipa maksimālajai vērtībai, lai viens izteiksmes veids aptvertu visus veselo skaitļu formātus:

```python
import json, subprocess
import numpy as np, tifffile

tags = json.loads(subprocess.run(
    ["exiftool", "-j", "-XMP:all", path],
    capture_output=True, text=True).stdout)[0]

# float(): exiftool hands back a negative real as a quoted string, so
# IndexMin arrives as str on most real NDVI data while IndexMax is a number.
lo, hi = float(tags["IndexMin"]), float(tags["IndexMax"])

raw = tifffile.imread(path)                    # uint16 for TIFF (16-bit)
ndvi = lo + raw.astype(np.float64) / np.iinfo(raw.dtype).max * (hi - lo)
```

Lasīšanai nav nepieciešams `-config pix4d.config` — exiftool šos XMP īpašības nolasa pats. Konfigurācija ir nepieciešama tikai to *rakstīšanai*.

> **`IndexMin == IndexMax`** nozīmē, ka katram šī kadra pikselim bija viena un tā pati indeksa vērtība. Rasters ir aizpildīts vidējā mērogā, un nav ar ko dalīt — ņemiet galapunktu.

> **Ja nav `Chloros:Index*` tagu, tas nozīmē, ka nav norādīta kartēšana.** LUT eksports ir krāsots attēls, kura pikseļi ir gradiento krāsas, kas piesaistītas jūsu LUT sliekšņa logam, un kadram bez pikseļa ar galīgu indeksu nav diapazona, ko norādīt. Nevienā no gadījumiem nepieņemiet, ka pastāv kartēšana — tas pats noteikums attiecas uz trūkstošo `PixelScale`.**Tā kā izstiepšana tiek veikta katram attēlam atsevišķi, divi viena lidojuma kadri nav salīdzināmi kā DN**— katram ir savi galapunkti, tāpēc viens un tas pats NDVI rādītājs dažādos attēlos atbilst atšķirīgām DN vērtībām. Lai veiktu kvantitatīvu analīzi visā lidojumā (zonu statistika, fiksēti sliekšņi, laika rindas), eksportējiet**`TIFF (32-bit, Percent)`**: indeksa rastri tajā tiek ierakstīti kā float32**paši indeksa vērtības**, bez nepieciešamības pēc pārskalēšanas vai marķieriem. Neskatoties uz formāta nosaukumu, indeksa produkti nav ×100 — šī mērogošana attiecas tikai uz atstarojumu ar veselo skaitļu datu tipu.

#### Spektrālās atdalīšanas ierobežošanas maska — uz kuriem pikseļiem tika ierobežota josla

3×3 spektrālā atdalīšana var padarīt joslu negatīvu — ja gaismā šī josla ir gandrīz neesoša vai ja aina atrodas ārpus kameras atdalīšanas matricas aprakstītā diapazona. Tā nav reāla vērtība, tāpēc eksportēšana to fiksē uz 0 — un reāllaika skatā tiek parādīts tas pats 0. Josla var tikt ierobežota dažos pikseļos vai gandrīz visos; maska reģistrē abos gadījumos. `Chloros:Band{R,G,B}UnmixClampFraction` norāda, cik lielā kadra daļā tas notika; ierobežojuma maska norāda, **kuri pikseļi**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— mapē `clamp_masks/`**blakus** produkta mapei, nekad tās iekšienē (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`) — katram starojuma, atstarošanas un indeksa produktam, kurā vismaz viens pikselis tika ierobežots. Vienjoslas `uint8`, uz paša produkta pikseļu režģa (orientācija, ROI, binning un masīva izlīdzināšanas deformācija iekļauti). Katra maska nosauc sava produkta kameru (`Make` / `Model` / sērijas numurs), uzņemšanas laiku un objektīva tagus, kā arī uzskaita joslas, kuras apzīmē tās biti (`XMP-Camera:BandName`, sākot ar 0. bitu); tās `ImageDescription` un `Software` to marķē kā fiksēšanas masku, un tajā nav kalibrācijas.
- **Biti**: 0. bits = josla produkta R kanālā (`Chloros:BandRName`), 1. bits = G (`BandGName`), 2. bits = B (`BandBName`). Iestatīts bits nozīmē, ka šajā joslā attiecīgā pikseļa vērtība tika ierobežota līdz 0 — to un jebkuru no tā veidoto indeksu uzskatiet par mazticamu.
- **`Chloros:UnmixClampMask`** ir atzīmēts uz katra produkta, kuram tika veikta krāsu atdalīšana: maskas ceļš attiecībā pret paša produkta mapi (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, vienmēr `/`-atdalīts — pievienojiet to attēla mapei, lai atvērtu masku), vai `none`, ja neviens pikselis netika ierobežots. Ja nav marķējuma, tas nozīmē, ka produktam nekad nav veikta atdalīšana (RGB vai mono kameras, neapstrādāti/debayered līmeņi).
- Uztveršanas rezultāti to iekļauj kā kadra **`clamp_mask`** (maskas ceļš vai `None`, ja nekas nav ierobežots) — `CameraSession.capture()` rezultātos, `ArraySession.capture()`'`CaptureResult` kadros un HTTP uzņemšanas atbildēs. Bezsaistes versija uzskaita savas maskas kā `clamp_masks`.
- Reāllaika indeksa daudzjoslu kaudze (`/api/camera/cli-array-index` ar `save_multiband`) saņem vienu masku katrai avota kamerai, izvades mapes paša `clamp_masks/` — skatiet CLI atsauci'`lattice index`. Videoklipam nav maskas: bezsaistes izveides rezultātā tiek ziņota katra kadra ierobežojuma daļa.

Tā kā nevienā produkta mapē, neatkarīgi no tās līmeņa, nav maskas, produkta mapi var nodot fotogrammetrijas programmatūrai tādu, kāda tā ir — ieskaitot apakšmapes —, un tā apstrādā tikai attēlus. Nododiet tai produkta mapi, nevis mapi, kas atrodas virs tās. „Chloros” paša importēšanas funkcija izlaiž maskas.

```python
import os, numpy as np, tifffile

mask = tifffile.imread(
    "clamp_masks/radiance/capture_<ts>_SN<serial>-<filter>_radiance_clampmask.tif")
# or, from the image: os.path.join(os.path.dirname(image_path), tag), with
# tag = the image's Chloros:UnmixClampMask
r_clamped = (mask & 1) != 0      # the band Chloros:BandRName names
g_clamped = (mask & 2) != 0
b_clamped = (mask & 4) != 0
```

---

## „Smart-Connect” LATTICE kamerām

Pastāvīgas aizmugures sesijas reāllaika aparatūrai. Tiek izmantoti tie paši galapunkti, ko izmanto GUI, tādējādi darbība ir identiska visās platformās — SDK, CLI un GUI.

### Viena kamera — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

Šeit iestatītā ekspozīcija, pastiprinājums un mērķis tiek saglabāti kā jūsu iestatījumi: Chloros automātiskā ekspozīcijas kontrole (ieskaitot DAQ mērīto ekspozīciju) tos nemaina. `cam.set_settings(exposure_auto="Continuous")` (vai `gain_auto=`) nodod ekspozīciju (pastiprinājumu) atpakaļ, un `cam.set_settings(target_brightness_auto=True)` nodod mērķi atpakaļ Chloros automātiskajai mērķa noteikšanai.

#### `connect_camera()` Paraksts

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

- **Tas atgriežas, kad straume ir sākta.** Aizmugurējā sistēma gaida, ievērojot ierobežojumus (aptuveni 15 s, līdz straume sākas, tad 10 s, līdz tiek piepildīts pirmais buferis — nevis līdz pirmajam priekšskatījuma kadram, kas seko aptuveni 1,5 s vēlāk), tāpēc `status()` parādās tieši pēc tam, kad ir norādīts `streaming`. Sesijas `connect_stream` ir atbildes `stream` bloks: `state` `streaming`, `no_frames` (plūsma sākās, bet neko nepiegādāja — to norāda `note`, un, ja kamera izmanto jumbo paketes, ir jāveic atkārtota savienošanās ar 1500 baitu lielu paketi), `starting` vai `start_failed`, kur `waited_s`. `None` kamerai, kas jau bija pieslēgta. Priekšskatījums (`get_camera_preview`, `/api/camera/jpeg/<serial>`), kas tiek pieprasīts uzreiz pēc gaidīšanas, ir ierobežots (4 s), kamēr tiek gaidīts kameras pirmais priekšskatījuma kadrs, nevis tiek atbildēts „Vēl nav pieejams kadrs“; kamera, kuras straume vēl nav pārraidījusi neko (`no_frames` vai kas ir sagatavota darbam ar aparatūras trigeri), joprojām atbild uzreiz.
- **Paketes lielumu varat noteikt paši.** `settings={"gev_packet_size": 1500, "gev_packet_size_auto": False}` atver kameru ar 1500 baitu paketēm, neizpētot (`chloros-cli lattice cam-connect --packet-size standard` nosūta to pašu).
- **Vienlaikus var izmantot tikai vienu savienojumu ar kameru**: atvēršana, paketes izmēra pārbaude, pirmā kalibrēšanas datu lejupielāde (aptuveni 70 s kamerai, kas šajā datorā tiek izmantota pirmo reizi), straumes sākšana. Pieprasījums, kam šajā laikā ir nepieciešama kamera (`read_nodes`, `set_settings`, ierakstīšana, otra savienošanās) gaida to ne ilgāk kā 5 s, pēc tam beidzas ar kļūdu HTTP 503 `CAMERA_POOL_BUSY`, norādot savienojušos kameru (`ChlorosConnectError`); jautājiet pēc brīža. Saraksti nekad negaida: `list_cameras()` (`GET /api/camera/pool`), `list_arrays()` un `CameraSession.status()` atbild uzreiz. Kamera, kas atrodas savienošanas procesā, tiek uzskaitīta zem pūla atbildes `connecting` (`serial`, `phase` — `opening the camera`, `rebalancing the pool bandwidth`, `loading its calibration`, `starting its stream`, `waiting for its stream` — un `elapsed_s`), un tā `status()` norāda uz `connecting: True` ar tās `phase`; pārējās kameras tiek nolasītas reāllaikā, ja resursu kopa ir brīva, citādi tiek izmantoti to pēdējie nolasītie dati (`live: False`).

#### `CameraSession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=15.0)` | Kameras reāllaika stāvoklis, kā to nolasa `chloros-cli lattice status`: `streaming`, `stream_state` (`starting`, `no_frames`, `streaming`, `start_failed` vai `stopped`), `device` (modelis, sērijas numurs, programmaparatūra, temperatūra, `is_mono`) un tā pašreizējos iestatījumus. |
| `read_nodes(names, enum_names=(), timeout=30.0)` | Izlasa GenICam mezglus; atgriež `{nodes, errors, enums, device, was_in_pool, in_array_id}`, kā arī `frame_values` un `notes`, kamēr darbojas automātiskā ekspozīcija (skatīt zemāk). |
| `set_settings(**kwargs)` | Raksta mezglus pēc viegli atpazīstama nosaukuma (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `target_brightness_auto`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). `exposure_time` (`gain`) ir manuālā ekspozīcija (pastiprinājums): ja to raksta bez `exposure_auto` (`gain_auto`), vispirms tiek izslēgts automātiskais režīms, lai darbojošais automātiskais režīms neatteiktu šo vērtību. Rezultātā `applied` saglabā to, ko kamera uzņēma; ieraksts, ko kamera noraida, atrodas `errors` un netiek saglabāts. Kļūda nosaka diapazonu, ko kamera tagad pieņem (`ExposureTime` maksimālā vērtība atbilst kameras kadru ātrumam), un automātiskais režīms, kas tika izslēgts tikai attiecībā uz noraidīto vērtību, tiek atjaunots; `restored` to uzskaita (piem., `{"ExposureAuto": "Continuous"}`). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, daq=None, daq_tolerance_ms=None, settings=None, timeout=None)` | Uztver **vienu** kadru. Atgriež `CaptureResult`: sarakstu ar kadru metadatu vārdnīcām (vienu par katru saglabāto līmeni), kopā ar `.skipped`, `.failed`, `.daq_files` un `.daq_matches`. (Sērijveida/vairāku kadru uzņemšana tika noņemta — ja nepieciešama sērija, izsauciet `capture()` ciklā.) |
| `capture_repeated(output_dir="output", *, count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Secīga (`interval_s=0`) vai intervāla uzņemšana no šīs kameras, ja tiek veikta `count` uzņemšana un/vai `duration_s` (vismaz viena, lai process beigtos), tāpat kā `ArraySession.capture_repeated()` to dara masīvam. Atgriež `CaptureResult` sarakstu; tas apstājas pēc uzņēmuma, kas ziņo par pilnu disku. `on_capture(i, result)` darbojas pēc katra; pārējie atslēgvārdi tiek nosūtīti uz `capture()`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Atbrīvo no pūla, kamera tiek atjaunota tādā stāvoklī, kādā tā tika atrasta savienošanās brīdī (izraisītāja vadu savienojumi, PTP, iestatījumi); `keep_settings=True` tā vietā saglabā savus iestatījumus savā atmiņā, `clear_settings=True` atjauno šo atmiņu (sk. [Kameras atmiņa](#camera-memory--keep_settings--clear_settings)); atgriež `camera_memory`, ja tiek pieprasīts kāds no tiem. Kamera, kas ir masīva elements, atbrīvo visu masīvu. Neveic nekādu darbību, ja pievienojamies jau atvērtai sesijai. Atgriež rezultātu, tiklīdz aizmugure apstiprina atbrīvošanu vai ja nav ko atbrīvot (kamera jau ir pazudusi vai nedarbojas neviena aizmugure); atbrīvošana, kuras laika limits ir beidzies vai kuru backend ir noraidījis, izraisa kļūdu `ChlorosConnectError`, jo kamera joprojām var būt aizņemta un nākamā savienošanās ar to radītu sadursmi. `with` bloka iziešana nekad neizraisa kļūdu. |

Atribūti: `serial`, `device` (kameras atklāšanas ieraksts) un `backend_url`.

`capture()` eksporta kontroles (tāds pats modelis kā masīvam + GUI):

- `processing` / `levels` — `processing="all"` saglabā visus piemērojamos eksporta veidus; `levels=["raw","radiance"]` saglabā tikai tos (pārraksta `processing`). Lai izmantotu aizmugurējās sistēmas noklusējumu, izlaidiet abus.
- `daq` — izmantotais DAQ nolasījums tiek pēc noklusējuma saglabāts kā `.daq` papildu fails, kurā iekļauts tikai neapstrādāts ieraksts, lai kadru vēlāk varētu pārstrādāt atstarojuma/indeksa formātā. `daq=False` to izlaiž, ja neviens līmenis to neizmanto. `force_daq=True` ir vecākais noklusējuma rakstības variants. Bez DAQ, nav `.daq`.
- `daq_tolerance_ms` — cik tālu no ekspozīcijas rādījums var atrasties un joprojām atbilst pieļaujamajām robežām (noklusējums 500). Mainās tikai novērtējums.

#### `read_nodes()` automātiskās ekspozīcijas režīmā — `frame_values` un `notes`

Kamēr darbojas `ExposureAuto` (`GainAuto`), `ExposureTime` (`Gain`) mezgls nolasa **pēdējo ierakstīto vērtību**, nevis to, ko kamera dara tajā brīdī. `nodes` joprojām saglabā šo mezgla vērtību. Ja pieprasāt `ExposureTime` vai `Gain` no pieslēgtas kameras, kuras automātiskais režīms nav `Off`, atbildē tiek pievienots:

- `frame_values` — vērtība, ko pārnesa kameras jaunākais kadrs, zem mezgla nosaukuma (`ExposureTime` mikrosekundēs, `Gain` decibelos);
- `notes` — viens teikums katram šādam mezglam, kurā norādītas abas vērtības.

```python
r = cam.read_nodes(["ExposureTime", "Gain"])
print(r["nodes"]["ExposureTime"])                  # last value written, e.g. 2000.448
print(r.get("frame_values", {}).get("ExposureTime"))  # what the newest frame used, e.g. 19600.0
for name, note in r.get("notes", {}).items():
    print(note)
# ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448);
# the camera's newest frame was at 19600 us.
```

Abas atslēgas nav klāt, ja automātiskais režīms ir `Off` (tad mezgls ir kamerasreālā vērtība) un kamerai, kas nav pieslēgta backendam. Masīvam `ArraySession.status()["member_ae"]` uzreiz sniedz katra elementa kadra vērtības.

#### DAQ mērītā ekspozīcijas ierobežojums (HTTP)

Ja ir pieslēgts DAQ gaismas sensors, Chloros ierobežo kameras ekspozīciju × pastiprinājumu atbilstoši izmērītajai dienasgaismai, lai baltais etalona panelis netiktu pārspīlēts. Ierobežojums darbojas tikai tad, ja kalibrēšanas mērķis atrodas šīs kameras redzes laukā (redzams tās kadrā pēdējo 10 sekunžu laikā); ja mērķis nav redzes laukā, tā vietā darbojas kameras parastā automātiskā ekspozīcija ar savu spilgtuma aizsardzību, lai pārējā aina netiktu attēlota tumšāka, nekā nepieciešams. SDK-apvalks nav pieejams; to var nolasīt vai mainīt katrai kamerai atsevišķi, izmantojot `GET` / `POST /api/camera/<serial>/daq-exposure-cap` (POST ķermenī `{"enabled": false}` to atslēdz attiecīgajai kamerai). Atbildē ir iekļauti `enabled`, `cap_us` (maksimālā vērtība reāllaikā, `None`, kad funkcija ir neaktīva, tostarp tad, ja redzes laukā nav kalibrēšanas mērķa), `k_exp_g` (konstante, ar kuru tiek aprēķināts maksimālais diapazons) un:

| Lauks | Nozīme |
| --- | --- |
| `k_exp_g_source` | Kura konstante tiek izmantota: `camera` — šīs kameras paša, aprēķināta no laboratorijas punkta tās kalibrēšanas komplektā (RGB kamerām); `fleet` — flotes konstante, ko izmanto gadījumos, kad komplektā nav laboratorijas punkta; `override` — lauka pārrakstīšana, kas iestatīta aizmugurē. |
| `k_exp_g_fleet` | Parka konstante, salīdzināšanai. |

### Sinhronizētais masīvs — `ArraySession` (Smart-Prep)

`connect_array` ir **ieteicamais sākumpunkts** konfigurācijām ar vairākām kamerām. Tas fonā izpilda pilnu „Smart-Prep” darbplūsmu ar grafisko lietotāja saskarni:

1. **Tīkla analīze**(`/api/camera/array/recommend`) — iestata**pilnu sensoru** (bez pikseļu apvienošanas, bez ROI apgriešanas) un ļauj kadru ātrumam samazināties līdz tam, ko nodrošina šis serveris un tīkls, ņemot vērā pikseļu formātus un saspiešanas veidu, ko izmantos Connect. Kadri nekad netiek samazināti, lai saglabātu ātrumu: norādiet mazāku `width` / `height` vai `binning=2` / `4`, lai samazinātu izšķirtspēju par labu kadru ātrumam (galapunktā`alternatives` sarakstā ir uzskaitītas šīs opcijas ar to prognozēto kadru skaitu sekundē).
2. **Līmeņa automātiskā izvēle** — `sim-capture-sim-emit` (visas kameras eksponējas pēc viena un tā paša izraisītāja, signālu nosūtīšanas temps tiek regulēts, lai dalītu vadu, kadru ātrums samazinās, samazinoties dalījumam) jebkurā tīklā, kurā kameras var sinhronizēt, lai dalītu vadu; `slip-emit-and-capture` (kameras izmanto vadu pa kārtām, nevis tiek eksponētas vienlaikus) — tikai tad, ja tas nav iespējams.
3. **PTP ir ieslēgts**pēc noklusējuma — dažādu kameru laika zīmogi tiek sinhronizēti ar vienu kopīgu pulksteni ar precizitāti**~1 ms**, atbilstoši aizmugures sistēmas [PTP galvenajam serverim](#ptp-grandmaster--time_sync_status). Vienlaicīga ekspozīcija tiek nodrošināta ar M8 aparatūras trigeri (**< 100 µs** starp moduļiem), nevis ar PTP: PTP saskaņo *laika zīmogus*, nevis ekspozīcijas.
4. **Automātiska pikseļu formāta izvēle katrai kamerai** — mono → `Mono12`, krāsu → `BayerRG12`.
5. **AE sākotnējā iestatīšana** — tiek saglabāts katras kameras pašreizējais AE stāvoklis, lai savienojums neatsāktu ekspozīciju no jauna darbības laikā.
6. **GPIO trigera konfigurācija** — `connect_array` aktivizē katru kameru (`TriggerMode=On`, `TriggerSource=Line2`), lai galvenāsimpulss vadītu pakļautās kameras pa M8 kabeli. Šis solis attiecas tikai uz masīvu: ja tiek atvērta viena kamera ar `LatticeCamera`, tā tā vietā darbojas brīvā režīmā. Atbrīvojot masīvu (`disconnect()` / `force_disconnect()`, `chloros-cli lattice array-disconnect`) atgriež visas kameras brīvā režīmā (`TriggerMode=Off`), saglabājot visus pārējos iestatījumus tā, kā tie tika atrasti savienojuma izveides brīdī: trigera un sinhronizācijas līnijas iestatījumus (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`; galvenās kameras sinhronizācijas līnija atgriezta uz ieeju), PTP un kameras iestatījumus, kas tika mainīti sesijas laikā — izņemot gadījumus, kad `keep_settings=True` / `clear_settings=True` (skatīt [Kameras atmiņa](#camera-memory--keep_settings--clear_settings)).

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

Atribūti: `array_id`, `master_serial`, `slave_serials`, `ptp_enabled`, `sync_mode`, `packed_transport` un `packed_reason` (pēdējie četri ir `None` uz roktura no `attach_array`), `connect_stream` un `backend_url`. `connect_stream` ir savienojuma atbildes `stream` bloks, atsevišķa savienojuma forma plus `members` (`{serial: {state, why?}}`): `streaming`, tiklīdz ir ieradusies masīva pirmā sinhronizētā grupa (aizmugure to gaida, ierobežots laiks, apmēram 10 s), vai `no_frames`, kur katra locekļa pēdējā izgūšanas kļūda ir tā `why`, un, attiecībā uz jumbo pakešiem, 1500 baitu rezerves risinājumu `note`. `None` uz `attach_array` rokturi.

#### `connect_array()` paraksts

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # pin the master trigger rate (auto if None); see set_target_fps
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    packed="auto",                        # packed 12-bit transport: "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=600.0,                        # s; also the backend's deadline (see below)
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`timeout` norāda, cik ilgi izsaukums gaida savienojumu, un tas tiek nosūtīts uz
aizmuguri kā termiņš. Ja līdz tam brīdim savienojums nav izveidots, tas tiek atcelts: katra kamera, ko tas
atvēra, tiek atgriezta tādā stāvoklī, kādā tā tika atrasta, un aizvērta, neviens masīvs netiek savienots, un
izsaukums izraisa kļūdu `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`, norādot
posmu, līdz kuram savienojums bija nonācis). Tātad izsaukums, kas izraisīja kļūdu, nekad neatstāj masīvu
aiz sevis. 9 kameru masīvs izveido savienojumu aptuveni minūtes laikā; tūlīt pēc
kameru ieslēgšanas tas var aizņemt vairākas minūtes. Masīvs, kas ir ieslēgts, bet kam vēl nav
sinhronizētas kadru grupas, tiek atgriezts ar kļūdu `connect_stream["state"]`
`no_frames`. Aizmugures sistēmas žurnālā ir viena laika rinda katram savienošanās posmam
(`[array-connect <id>] <step>: <s> s …`) un pēdējā rinda, kurā norādīts lēnākais posms.

`force_tier` vērtības:
- `"sim-capture-sim-emit"` — patiesa vienlaicība (visas kameras izšauj vienā un tajā pašā takts malā).
- `"sim-capture-ftd-stagger"` — elastīga laika domēna nobīde (kameras raidīšanas brīži ir nedaudz nobīdīti, lai paketes vadā tiktu sērijveidotas).
- `"slip-emit-and-capture"` — secīga uzņemšana katrai kamerai atsevišķi (bez laika sinhronizācijas). Tiek izvēlēts automātiski tikai tad, ja kameru darbības ātrumu nevar samazināt pietiekami, lai tās vienlaicīgi izmantotu vadu.

`wire_ceiling_mbps` pārraksta **uzņēmējdatora ilgstošo vadu budžetu** MB/s — vienīgais
skaitlis, no kura ir atkarīgs visa masīva resursu sadalījums. Atstājiet to `None`, lai izmantotu automātiski noteikto
vērtību. Samaziniet to, ja masīvs ziņo par GVSP bojātiem rāmjiem: automātiskā vērtība tiek aprēķināta
pēc tīkla kartes paziņotā savienojuma ātruma, kas pārvērtē USB adapterus, šauras PCIe joslas un
noslogotas koplietošanas struktūras — un šī pārvērtēšana izpaužas kā bojāti rāmji, nevis kā
redzami lēns savienojums. Šī vērtība tiek saglabāta projekta masīva uztveršanas blokā, tādēļ,
atverot to no jauna vai vēlāk izmantojot `connect_array`, tā tiek atjaunota tāpat kā jebkurš cits masīva iestatījums.
Skatīt [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames).

#### Saspiežta 12 bitu pārraide (`packed=`)

Pie pilnas sensoru jaudas masīva kadru ātrumu nosaka tas, cik ātri katra kamera var nosūtīt
savus kadrus. 12 bitu formāti (`BayerRG12`, `Mono12`) parasti tiek pārraidīti kā 16 bitu
vārdi; saspiestā pārraide nosūta tos pašus 12 bitus 1,5 baitos uz pikseli un
tos izpako uz galvenā datora, tādējādi katra kadra nosūtīšanai nepieciešams par 25 % mazāk laika — aptuveni
**33 % vairāk kadru sekundē, strādājot ar pilnu sensora jaudu** (izmērīts: 10,7 → 14,0 fps uz
9 kameru masīva). **Saglabātie dati ir identiski**: tās pašas vērtības, tie paši 16 bitu
faili, tie paši metadati.

- `packed="auto"` (noklusējums) saspiež datus tikai tad, ja tas palīdz **un** saņēmējierīce var
  atļauties izpakošanu: savienojoties, aizmugurējā sistēma izmēra savu izpakošanas ātrumu,
  procesoru skaitu, pašreizējo slodzi un tīkla resursus. Dators ar brīviem procesora kodoliem
  saspiež datus, izmantojot visus sensorus; noslogots vai zemas jaudas serveris (vienplatnes datori,
  iegultie kontrolieri) pārraida datus nesaspiestā veidā. Nelielas interešu zonas (ROI) un apvienoti kadri gūst nelielu ieguvumu.
- `packed="off"` (vai `False`) nekad nesaspiež — lai to izslēgtu diagnostikas laikā,
  vai uz servera, kam procesora resursi ir nepieciešami citiem uzdevumiem.
- `packed="on"` (vai `True`) vienmēr saspiež 12 bitu formātus.
- Jebkura cita vērtība izraisa `ValueError`. 8-bitu un 16-bitu formāti nekad netiek
  saspiesti.

`analyze_array_network(..., packed="auto")` darbojas pēc tā paša principa,
tāpēc tā fps ir ātrums, ar kādu masīvs darbosies (atbilde satur
`packed_transport` un `packed_reason`); tā `pixel_format` noklusējuma vērtība ir tāda, kādu
piemēro savienojums (Mono12 / BayerRG12), nevis 8 bitu.

Lēmums un tā pamatojums atrodas sesijā:

```python
arr = chloros_sdk.connect_array(serials)          # packed="auto"
print(arr.packed_transport, arr.packed_reason)
# True  auto: projected 11.0 -> 14.4 fps, unpack 0.16 of 1.50 budgeted cores

arr = chloros_sdk.connect_array(serials, packed="off")
```

`ChlorosProject.connect_all(packed=...)` piemēro to pašu pārrakstīšanu visiem
masīviem projektā (noklusējums `None`, kas nozīmē `"auto"`: projekts nesaglabā
saspiestu iestatījumu katram masīvam).
Lai iestatītu noklusējumu visai mašīnai, iestatiet `CHLOROS_ARRAY_PACKED12=off` (vai `on`)
aizmugurējās sistēmas vidē; tomēr eksplicīts `packed="on"` / `"off"` joprojām ir prioritārs.
`/api/camera/array/status` ziņo par `packed_transport` un `packed_decision`.

#### Sagaidāmie kadru ātrumi

Izmērītie masīva kadru ātrumi (sinhronizēti ar aparatūru, 12 biti; pilns sensors = 2048 × 1536). Izmantojiet tos, lai pārbaudītu, vai `status()["actual_fps"]`; `analyze_array_network()` prognozētais kadru ātrums atbilst jūsu datoram un tīklam.

| Konfigurācija | Masīva kadru ātrums |
| --- | --- |
| Pilna sensora konfigurācija, PCIe 10 GbE galvenā tīkla karte, saspiesta datu pārraide (`packed="auto"` uz galddatora) | ~14 fps |
| Pilna sensora režīms, PCIe 10 GbE uzņēmējdatora tīkla karte, nesaspiests (`packed="off"`) | ~10,5 kadrus sekundē |
| Pilna sensora režīms, 8 kameras ar USB 10 GbE adapteri | ~3,8 kadrus sekundē |
| Pilna sensora režīms, 1 GbE galvenā savienojuma līnija | 1,2–1,7 kadrus sekundē |
| 2 × 2 pikseļu apvienošana (`binning=2`), 10 GbE galvenā tīkla karte | ~30–33 kadrus sekundē |

- PCIe 10 GbE savienojumā, izmantojot pilnu sensoru, ierobežojošais faktors ir **kameras**, nevis tīkls: 4 un 8 kameras darbojas ar vienādu ātrumu.
- **`target_fps` ir trigera ātrums, un matrica nekad nedarbojas ātrāk.** `target_fps=1` nodrošina masīva darbību ar 1 fps; `None` (noklusējuma iestatījums) nodrošina darbību ar augstāko ātrumu, kādu spēj nodrošināt galvenais dators un tīkls. Ja kāds rādītājs pārsniedz šo ātrumu, tas tiek samazināts līdz tam (sk. [`set_target_fps`](#interval-capture-for-drone-surveys)).
- **`link_speed_mbps` no `4294` līdz `status()` nav savienojuma ātrums.** Tas nozīmē, ka Windows nevarēja nolasīt adaptera ātrumu (draiveris ziņoja par lielāko 32 bitu vērtību), kas ir tipiski USB Ethernet adapteriem. USB adaptera izmantojamais ātrums Windows vidē ir ierobežots līdz 200 MB/s neatkarīgi no apstākļiem (skatīt [Array Health](#array-health--which-subsystem-is-losing-frames)); lai to pielāgotu, skatiet CLI atsauces sadaļu „USB 10GbE adapteri” zem [Host NIC Setup & Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **USB ierobežojums attiecas arī uz Linux: 400 MB/s x86_64 sistēmās, 200 MB/s NVIDIA Jetson ierīcēs.** Adapters tiek identificēts no sysfs (izmantojot `ethtool -i` kā rezerves variantu), tādēļ USB 5 GbE vai 10 GbE adaptera ātrums tiek ierobežots atbilstoši šim ierobežojumam, nevis tā nominālajam ātrumam 500 / ~1060 MB/s. x86_64 rādītājs tika izmērīts Linux vidē: tas pats adapteris, kas Windows vidē bojā kadrus, ja ātrums pārsniedz 200 MB/s, nodrošināja nevainojamu 4 kameru sistēmas darbību pie jebkura maksimālā ātruma līdz pat 480 MB/s. Uz NVIDIA Jetson tas pats adapteris darbojās bez kļūdām tikai pie 200 MB/s un virs šī ātruma periodiski zaudēja kadrus. Ja jūsu galvenais dators apstrādā lielāku datu apjomu bez `gvsp_corrupt` kadriem, palieliniet to ar `wire_ceiling_mbps`. Linux vidē adapterim, kas pieslēgts USB 2.0 portam, ir piešķirti 30 MB/s; pieslēdziet to USB 3 portam.

#### Pārslogošana (minimālā robeža katrai kamerai)

Masīvs darbojas jebkurā tīklā; kadru ātrums ir atkarīgs no tīkla. Sim-emit pacing katrai kamerai piešķir vienādu daļu no sadursmju drošā vadu budžeta, un visas tās joprojām eksponējas pēc viena un tā paša izraisītāja, tāpēc lēnā vadā daļa — un kadru ātrums — ir neliels: 8 pilnasizšķirtspējas 12 bitu kameras 1 GbE tīklā bez jumbo rāmjiem saņem katra 6,5 MB/s, aptuveni **1 fps**(aptuveni 1,3 fps saspiestā veidā). Daļa ir ierobežota līdz lēnākajam ātrumam, kādā kameras var tikt sinhronizētas (`per_cam_floor_bps`: izlasiet no katras kameras GevSCPD diapazona savienojuma izveides brīdī, un nekad zem 1 MB/s). Ja savienojumam tās ir nepieciešamas lēnāk, savienojums pārslēdzas uz**seriālo**datu pārraidi (`slip-emit-and-capture`): kameras pārraida datus pa kārtām ar pilnu savienojuma ātrumu, tādējādi katrs kadrs tiek saņemts, bet elementi**netiek atklāti vienlaikus**— atbildes līmenis un bloķētāji (`SERIAL: ...`) to norāda; nododiet `force_tier="sim-capture-sim-emit"`, lai atteiktos no pārejas uz rezerves režīmu. Kadru izmērs netiek ņemts vērā sadalē, tādēļ**kadru apvienošana un ROI palielina kadru ātrumu, bet nemaina to, vai masīvs ietilpst**. Tikai tāds savienojums, kas nespēj pārraidīt masīvu nevienā pārraides ritmā — ne vienlaikus, ne pārmaiņus —**pārslogo** — kļūdas režīms ir GVSP pakešu zudums, nevis zemāks kadru ātrums. Risinājumi: mazāk kameru, jumbo kadri no gala līdz galam vai ātrāks tīkla interfeiss.

- Atbildes `analyze_array_network()` un `/api/camera/array/connect` pārraida `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` un `per_cam_floor_bps`. Ja `oversubscribed` ir „true”, projekcija **nullej fps laukus** (`achievable_fps_max` / `fps_bright` / `fps_dark`), nevis ziņo par maldinošu, lēnu, bet darbojošos ātrumu.
- `POST /api/camera/array/connect` pieņem `pin_resolution` ķermeņa parametru (**tikai HTTP — nav SDK kwarg**; `connect_array` to neizpauž). Tas darbojas stingrā režīmā: pārslodzēts savienojums ar iestatītu `pin_resolution` tiek**kategoriski noraidīts**, parādot kļūdu, kurā norādīti visi risinājumi. Ja tas nav iestatīts, savienošana turpinās ar brīdinājumu, ka masīvs zaudēs paketes. Abos gadījumos savienošana saglabā pieprasīto rāmja izmēru — tā nekad pati neapvieno vai neapgriež.
- Tikai testēšanai: iestatiet `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` aizmugurējās vides iestatījumos, lai noraidījumu pazeminātu līdz skaļam brīdinājumam — jūs tomēr izveidojat savienojumu un pieņemat pakešu zudumu.

#### Masīva stāvoklis — kura apakšsistēma zaudē rāmjus

`ArraySession.capability()` (`GET /api/camera/array/<array_id>/capability`) pārraida reāllaika `health` bloku
savienotajā masīvā, ko atkārtoti novērtē **10 sekunžu** intervālā. Tas sadala rāmju zudumu
divos cēloņos, kuriem nepieciešami pretēji risinājumi, nevis vienā „nepilnīgā” rādītājā, kas
nenorāda ne vienu, ne otru:

| Lauks | Kas tas nozīmē | Kura apakšsistēma |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (pēc sērijas numura) | Kadrs **ieradās, bet bija strukturāli bojāts**— GVSP pakešu zudums. |**Tīkls**: vadu jauda, sinhronizācija, NIC RX gredzens, MTU |
| `never_arrived_rate_pct` (pēc sērijas numura) | Kadrs **vispār neieradās**— kamera neizšāva vai no tās nekas neizgāja. |**Trigers / sinhronizācija**: M8 kabelis, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Sliktākais kameras pārraides ātrums katrai. | — |
| `per_cam_rate_pct` | Kopējais nepilnīguma rādītājs katrai kamerai (abi iemesli kopā). | — |
| `stable_for_seconds` | Cik ilgi katra kamera ir palikusi zem 0,01 %. | — |

Papildus `health` šajā pašā ierakstā ir norādīts arī skaitlis, par kuru ir pārkāpts kopējais piešķīrums:

| Lauks | Nozīme |
| --- | --- |
| `wire_ceiling_mbps` | Spēkā esošais uzņēmuma ilgtermiņa vadu budžets, MB/s. |
| `wire_ceiling_source` | No kurienes šis skaitlis ir iegūts, vārdos — piemēram, `USB-capped 200 MB/s (was theoretical 1062; …)` vai `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, ja to iestatīja `wire_ceiling_mbps=`. |
| `nic_is_usb` | `true` USB Ethernet adapterim. |

```python
import chloros_sdk

serials = ["213800234", "214000533"]           # master first
arr = chloros_sdk.attach_array(serials)
h = arr.capability()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    # force_disconnect(), not disconnect(): this handle came from attach_array,
    # and disconnect() never tears down an array the session only attached to.
    # It returns only once the backend confirms the release; a teardown that
    # timed out or was refused raises ChlorosConnectError instead, so the
    # reconnect below never races a half-released array.
    arr.force_disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Lasīšana:** `gvsp_corrupt_rate_pct` vērtība, kas nav nulle, ar `never_arrived_rate_pct` vērtību 0 nozīmē, ka
impulsa signāls un kabeļa sinhronizācija ir ideāla un 100 % zudumu rada tīkla ceļš — samaziniet
`wire_ceiling_mbps` un atkārtoti izveidojiet savienojumu. Pretējais modelis norāda uz sinhronizācijas kabeli vai
trigera līniju.

> **`target_fps` nav rādītājs bojātiem rāmjiem.** GevSCPD ritms tiek iestatīts vienreiz
> savienojuma izveides brīdī, tādēļ trigera frekvences samazināšana maina darba ciklu, nevis
> vienlaicīgās pārraides pārraides ātrumu. Izmērītais 5× pieprasījuma samazinājums nedeva uzlabojumus, savukārt
> savukārt vadu maksimālās ātruma robežas samazināšana no 240 līdz 200 MB/s samazināja bojāto rāmju īpatsvaru tajā pašā iekārtā no 10,4 % līdz
> 0,00 %.

> **Darbībā esošs masīvs nevar pats pārplānot savu darbību ar LATTICE (M3C / M3M) programmaparatūru.** Tā nevar
> to novērst pati; atvienojiet un atkārtoti pievienojiet, lai savienojuma laika izvēlne veiktu pārplānošanu atbilstoši
> jaunajam maksimālajam ātrumam.

**USB Ethernet adaptera ātrums tiek ierobežots līdz 200 MB/s** ar zondi, neatkarīgi no tā
nosaukuma: efektivitātes tabula, kas pārvērš savienojuma ātrumu ilgstošā rādītājā, ir
atvasināta no PCIe, un USB tīkla karte paziņo savu Ethernet savienojuma ātrumu, vienlaikus būdama ierobežota ar
USB šķiedru un tās draiveri. Ierobežojums ir absolūts, nevis daļējs — USB 1 GbE adapteris
sasniedz ~80 MB/s, un tas netiek ietekmēts. Tas attiecas uz Windows un Linux (ieskaitot Jetson);
skatiet [Paredzamie kadru ātrumi](#expected-frame-rates), lai uzzinātu, kur tas tika izmērīts.

#### `ArraySession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=10.0)` | Darbojošā masīva reāllaika stāvoklis: `target_fps` / `target_fps_auto` (izraisītāja frekvence un informācija par to, vai frekvences kontrolieris to pārvalda), `target_fps_clamped_reason` (kāpēc fiksētā frekvence tiek uzturēta zemāka, citādi `None`), `actual_fps` (izmērīts; 0, kad satveršanas cilpa ir pārtraukusi darbību vai iestrēgusi), `recommended_fps`, `streaming` (False, tiklīdz satveršanas cilpa ir pārtraukusi darbību — pārbaudiet šo, nevis tikai `last_error`; paliek „True” pēc iestatījumu atkārtotas palaišanas, ko norāda `restarting`), `stalled` (vērtība „True”, kamēr satveršanas cilpa darbojas, bet nav publicējusi nevienu kadru grupu aptuveni desmit kadru periodu garumā, 2–30 s; `last_error` to norāda tikai pēc 60 s), `last_frame_age_s`, `last_error`, `serials`, `ptp_enabled` (`True` tikai tad, ja masīvs bija savienots ar PTP **un** katrs elements pēdējo reizi nolasīja `PtpEnable` ieslēgts; tas nav tas pats, ko nozīmē bloķēts, ko mēra `ptp_epoch`), `ptp_configured` (tas, ko pieprasīja savienojums), `ptp_off_members` / `ptp_unread_members` (elementi, kas pēdējoreiz nolasīja `PtpEnable` izslēgts / neatbildēja), `member_recovery` (`None` vai dalībnieks, kas zaudējis barošanu vai savienojumu: `state`, `waiting`, kamēr tas neatbild, `reopening`, tad `recovered`, kopā ar `members` un `why` — masīvs to atkal iekļauj, kad tas atkal atbild, un atjauno tā savienojuma brīža PTP, izraisītāja un sinhronizācijas līnijas vadu savienojumus, tempa un kameras iestatījumus, pēc tam atkal sāk datu plūsmu bez jauniem `connect_array`) un `ptp_epoch` (izmērīts), `sync_mode` / `sync_tier`, `packed_transport` / `packed_decision`, `per_cam_emit_mbps`, `link_speed_mbps` (`4294` nozīmē, ka Windows nevarēja nolasīt adaptera ātrumu — skatiet [Paredzamie kadru ātrumi](#expected-frame-rates)), `trigger_pipeline` / `pipeline_tune`, `radiometric_chain` (vai katras joslas starojuma ķēde ir izmantojama un kurš elements to pārtrauca), un `band_clamp` — kamēr reāllaika skats renderē masīvu: to apgaismoto pikseļu daļa, kuru atdalīšanas process ierobežoja līdz 0, katram elementam (`members`: `{serial: [band0, band1, band2]}` kameras R, G, B joslu secībā) un katrai joslai, ko parāda kompozīcija (`composite`: `{band name: share}`), kur `warn` ir daļa, ko lietotāja saskarne atzīmē kā „Dati ar zemu uzticamību joslā” (mērīti aptuveni reizi sekundē; `None`, ja nav ieslēgts tiešraides skats), un `member_ae` — katram elementam ekspozīcija (`exposure_us`) un pastiprinājums (`gain_db`) no pēdējā publicētā kadra, kas iegūts no paša kadra (`read_nodes` vai `ExposureTime` / `Gain` atgriež pēdējo vērtību, kas ierakstīta, kamēr darbojas automātiskā ekspozīcija, nevis to, ko tā dara). |
| `set_member_settings(serial, **kwargs)` | Ieraksta vienas dalībnieka kameras iestatījumus, kā `CameraSession.set_settings()` tos (ekspozīciju, pastiprinājumu, mērķa spilgtumu, ...); kamera paliek masīvā. Izraisa `ChlorosConnectError`, ja sērija nav masīva loceklis. Skatīt [Ekspozīcija masīva locekļiem](#exposure-on-array-members). |
| `capability(timeout=10.0)` | To, ko noteica smart-prep (uzņemšanas līmenis, kadra izmērs, `pixel_format`), aktīvo `health` bloku un uzņēmējdatora vadu budžetu. Skatīt [Masīva darbības stāvoklis](#array-health--which-subsystem-is-losing-frames). |
| `set_target_fps(fps, timeout=10.0)` | Mainīt trigera frekvenci, kamēr masīvs darbojas, neveicot atkārtotu savienošanu. Skaitlis to fiksē (frekvences kontrolieris to atstāj nemainīgu); `None` atgriež to automātiskajā režīmā. Atgriež `{requested, target_fps, target_fps_auto, clamped}` — `clamped` ir True, ja ātrums tika samazināts līdz līmenim, ko var pārraidīt saimniekdators un tīkls. Skatīt [Intervāla uzņemšana dronu apsekojumiem](#interval-capture-for-drone-surveys). |
| `grab(processing="debayered", with_metadata=False, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, bit_depth=None, band=None, align=False, refl_max=None)` | Viens kadrs no katras kameras, **visi no viena izraisītāja**, kā numpy masīvi (netiek rakstīti faili). Atgriež `GrabResult` (vai `dict`): `{serial: array}` vai `{serial: (array, metadata)}` ar `with_metadata=True`. `.group_id` ir izraisītājs, ko kopīgi izmanto kadri; `.errors` katru izlaisto kameru saista ar iemeslu (kamera, kuru nevar apkalpot ar šo izraisītāju, tiek izlaista un nekad netiek aizstāta ar citu). `processing` ir `raw` / `debayered` / `radiance` / `reflectance` / `display`. Tas pats nolasījums kā projekta API [`ArrayHandle.grab()`](#one-trigger-per-read--group_id) — viena kopīga īstenošana — tāpēc šeit darbojas visi tur minētie atslēgvārdi (`band`, `align`, `bit_depth`, `refl_max`) šeit darbojas. Nepieciešams `numpy`. |
| `frame_stream(*, processing="debayered", fps=5.0, duration=None, count=None, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, with_metadata=False, bit_depth=None, band=None, align=False, refl_max=None)` | Iterators, kas katrā takts ciklā atgriež vienu `grab()` rezultātu (tikai atslēgvārdu argumenti), ar ātrumu, kas atbilst `fps`, ierobežots ar `duration` (sekundēs) un/vai `count` (`None` = neierobežots). **Katra iegūta kopa ir jauns trigers:** takts, kura jaunākais trigers ir jau iegūtais kopums, gaida nākamo trigeri, nevis to atkārto, tādējādi plūsma darbojas ar lēnāko no `fps` un masīva paša kadru ātruma. Izraisa `FrameFetchError`, nevis pārtrauc darbību: tūlīt, ja visas kameras kāda iemesla dēļ atsakās, nākamais takts atkārtotos, pēc 60 s taktiem bez kadra un pēc 60 s bez jauna triggera („nav jauna trigera 60 s”). Tāda pati īstenošana kā `ArrayHandle.frame_stream()`. Skatīt [Reāllaika kadri jūsu paša apstrādes ķēdē](#live-frames-in-your-own-pipeline). |
| `continuous(output_dir="output", *, format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, max_captures=None, duration_s=None, daq=None, daq_tolerance_ms=None, timeout=30.0)` | Uzsāk backenda paša nepārtraukto ierakstīšanu (GUI nepārtrauktais režīms) → [`ContinuousHandle`](#continuoushandle). Saglabā **katru** sinhronizēto grupu, ko publicē masīvs, tāpat kā to saglabātu `capture()` (tādi paši eksporta ierobežojumi), līdz tiek apturēts vai līdz `max_captures` / `duration_s` (`None` = līdz brīdim, kad tiek apturēts). Izraisa `ChlorosConnectError`, ja šim masīvam jau darbojas kāds process. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, smart=False, after_seq=None, daq=None, daq_tolerance_ms=None, timeout=300.0)` | Viena sinhronizēta uztveršanas grupa. Atgriež `CaptureResult` (kadru vārdnīcu sarakstu + `.skipped` + `.failed` + `.group_seq` + `.daq_files` + `.daq_matches` + `.alignment` + `.oriented`). Nodod iepriekšējo rezultātu`group_seq` kā `after_seq`, lai iegūtu nākamo sinhronizēto grupu pēc tās, nevis gaidītu jaunu. Eksportēšanas vadības elementi zemāk. |
| `capture(..., smart=True)` | **Vieds uzņemšanas režīms** — gaida, līdz AE stabilizējas visās kamerās, tad aktivizējas. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, orient=None, timeout=120.0)` | Ātrākais uzņemšanas režīms: tikai neapstrādāti dati + piešķirtais DAQ rādījums (+ brīvais kombinētais indekss). Atbilst GUI pogai „Ātrākā uzņemšana”. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Vienreizēja / Nepārtraukta / Intervāla uzņemšana vienā ierobežotā ciklā. Atgriež `list[CaptureResult]`.**Nepieciešams `count` un/vai `duration_s`**, lai to pārtrauktu (SDK-ā nav Ctrl+C). Tāpat beidzas priekšlaicīgi, pēc cikla, kad kāds cikls ziņo par pilnu disku (ieraksts `.failed` ar `disk_full`); šis cikls ir pēdējais rezultāts. |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, expr=None, timeout=30.0)` | Sākt reāllaika kombinētā indeksa skata ierakstīšanu video/GIF formātā → `RecorderHandle`. Video ir Motion-JPEG formātā `.avi` formātā (nav nepieciešams ffmpeg). Ja nav atvērts GUI skats, aizmugurējā sistēma to renderē: `expr` (indeksa izteiksme masīva joslu nosaukumos, piemēram, `"(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)"`) vai pēc noklusējuma masīva NDVI. Viens kompozītiskais ierakstītājs katram masīvam. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, orient=None, timeout=30.0)` | Sākt augstas kadru frekvences neapstrādātu Bayer sērijas uzņemšanu → `RecorderHandle`. Pārstrādāt bezsaistē ar `build_video()`. `orient=True`: produkti, ko izveido `build_video()`, tiek pagriezti atbilstoši kameras orientācijai (katras kameras produkti — atbilstoši savas kameras orientācijai, apvienotais indekss — atbilstoši galvenās kameras orientācijai); neapstrādātie kadri paliek sākotnējā orientācijā, un sērijas manifestā tiek reģistrēta katra elementa orientācija. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Veic saglabātas neapstrādātu kadru sērijas pārstrādi bezsaistē, pārveidojot to par kalibrētu(-iem) video. Blokējas, līdz darbs ir pabeigts (`wait=True`), un atgriež izveides rezultātu: `{success, outputs, errors, skipped, substitutions, notes, degraded, combined}` — skatiet [izveides rezultātu](#the-build_video-result) zemāk. |
| `build_video_status(job_id, timeout=15.0)` | Pārbauda bezsaistes apstrādes uzdevumu: `{running, result, error, burst_dir}`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Atbrīvo visu masīvu — **ja šī sesija to atvēra** (`connect_array`): tad tā ir `force_disconnect()`, un izraisa `ChlorosConnectError`, ja atbrīvošana netiek apstiprināta (`with` bloka iziešana nekad neizraisa kļūdu). Ja tiek izmantots `attach_array` rokturis, masīvs turpina darboties un uz stderr tiek izvadīta viena rinda, kas to norāda: masīvs var piederēt GUI vai citam skriptam. Katra kamera tiek atstāta brīvi darbojošās stāvoklī un citādi tādā pašā stāvoklī, kādā tā tika atrasta savienojuma izveides brīdī; `keep_settings=True` saglabā tās iestatījumus kameras paša atmiņā, `clear_settings=True` atjauno šo atmiņu uz rūpnīcas iestatījumiem (skatīt [Kameras atmiņa](#camera-memory--keep_settings--clear_settings)). |
| `force_disconnect(timeout=60.0, keep_settings=False, clear_settings=False)` | Atbrīvo visu masīvu neatkarīgi no tā, kurš to atvēra. Izmantojiet to uz pievienota roktura, ja jūsu mērķis ir pārtraukt masīva darbību (piemēram, lai to atkārtoti savienotu ar atšķirīgiem iestatījumiem). Atgriež rezultātu, tiklīdz aizmugure apstiprina atbrīvošanu vai ja nav ko atbrīvot (masīvs jau ir pazudis vai nedarbojas neviena aizmugure); atbrīvošanas pārtraukšana, kas beigusies ar laika limitu vai kuru aizmugure noraidījusi, izraisa kļūdu `ChlorosConnectError`. Ar jebkuru no šiem karogiem tā atgriež to, ko katras kameras atmiņa saņēma (`camera_memory`), citādi — `None`. |

`capture()` eksporta kontroles (tāds pats galapunkts, kādu izmanto GUI/CLI):

- `processing` / `levels` — `processing="all"` (vai `levels=["raw","radiance",…]`) saglabā katru piemērojamo eksporta veidu katrai kamerai; viena `processing` vērtība saglabā tikai šo līmeni.
- `aligned` — **pēc noklusējuma ieslēgts (`None`), jo lietotnes Chloros uzņemšana no tā paša masīva ir**(tās**izlīdzinātais** uzņemšanas iestatījums katram masīvam). Katra elementa neapstrādātais eksporta fails tiek pielāgots galvenās kameras kadram, izmantojot masīva [izlīdzināšanas profilu](#array-alignment) un apgriezts līdz elementu kopīgajai pārklāšanās zonai, tādējādi faili tiek reģistrēti pēc pikseļiem un iegūst tādu pašu izmēru, apgriezumu un `Alignment*` tagus kā lietotnē (ROI ietvaros transformācija tiek pārrēķināta atbilstoši logam); `XMP-mapir:AlignmentJSON` norāda uz `"applied": true` ar tā `crop_roi`. Raw faili paliek neizkropļoti sākotnējā izmērā, bet transformācija tiek saglabāta metadatos. `aligned=False` saglabā katru failu sākotnējā izmērā, neizkropļotu; `aligned=True` ir norādīts kā noklusējums. `continuous()`, `capture(smart=True)`, `capture_repeated()` un projekta līmeņa `ArrayHandle.capture()` / `capture_smart()` izmanto to pašu noklusējuma iestatījumu. Neatkarīgi no tā, vai deformācija tiek piemērota vai nē, katrs elementa fails reģistrē savas kameras `Alignment*` transformāciju, tiklīdz masīvam ir piešķirts izlīdzināšanas profils — izņemot kameru, kuru profils neizlīdzina (izlaista vai piespiedu kā identitāte neveiksmīgas risināšanas dēļ), kā arī atsauces kamera, ja neviena cita kamera nav izlīdzināta: tām nav `Alignment*` marķējumu. Ja masīvam nav profila, uzņemums tiek saglabāts neizlīdzināts, un rezultāts to norāda: `result.alignment` ir `{"requested": True, "applied": False, "warnings": [...]}`, viens brīdinājums par katru iemeslu, kāpēc deformācija nevarēja tikt piemērota (nav profila, elementi ar atšķirīgu izšķirtspēju, masīvs navsinhronizēts, kadrs, uz kuru profilu nevar attēlot). `applied` ir `True` tikai tad, ja katrs eksports, kas pieprasīja deformāciju, to saņēma. Masīva automātiskā izlīdzināšana izveido profilu, tiklīdz ekspozīcija ir stabilizējusies; `ArrayHandle.calibrate_alignment()` / CLI komanda `align-calibrate` izveido profilu pēc pieprasījuma. `result.oriented` norāda, vai `orient=True` ir pagriezis produktus.
- `render_index=False` — izlaist katras kameras veģetācijas indeksa pārklājumu; pēc noklusējuma tas tiek attēlots tur, kur tas ir konfigurēts.
- `daq` / `daq_tolerance_ms` — DAQ rādījums pēc noklusējuma tiek saglabāts kā `.daq` papildu fails, pat ja nevienam izvēlētajam līmenim tas nav nepieciešams; `daq=False` šajā gadījumā to izlaiž, un `daq_tolerance_ms` nosaka saskaņošanas pielaidi (noklusējumā 500). `force_daq=True` ir noklusējuma vecākais rakstījums. Skatīt [DAQ rādījumi, ko saglabā uzņemums](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
- `orient=True` — pārvērš katra elementa **produktus**(`display`, `radiance`, `reflectance`, indeksu katrai kamerai un kompozīciju) atbilstoši kameras orientācijai pēc izlīdzināšanas deformācijas: pēc**galvenās**kameras orientācijas, kad tie tika deformēti tās kadrā (`aligned` noklusējums — elementi paliek reģistrēti kopā), citādi — pēc savas kameras orientācijas.**Noklusējums: katrs elementa fails saglabā sākotnējo orientāciju**, kā tas vienmēr ir masīva uzņēmumos; `raw` un `debayered` jebkurā gadījumā saglabā sākotnējo orientāciju. Katrs fails reģistrē tā kadra orientāciju, kurā atrodas tā pikseļi (`XMP-Camera:ChlorosRotation` / `ChlorosFlip*` / `ChlorosOrientationApplied` — neapstrādāts fails, savas kameras), un `Alignment*` XMP saglabā oriģinālā kadra transformāciju, kas piemērota pirms orientācijas; RAW fails, kurā ir šī transformācija, reģistrē arī `ChlorosAligned*` (galvenā kadra orientāciju) — produkta orientāciju, kas deformēta atbilstoši galvenajam kadram. Apstrāde pagriež katru produktu par leņķi, kas pieder tā pikseļiem: par izlīdzināto, ja tiek deformēts RAW fails (ieslēgta masīva izlīdzināšana), vai par kameras paša orientāciju, ja tā nav. `capture_repeated()` un `capture_fastest()` to caurlaida; projekta līmeņa `ArrayHandle.capture()` / `capture_smart()` to arī pārņem.

**TIFF kompresija (regulators HTTP vien):** `ArraySession.capture()` nenosūta `compression` atslēgu, tāpēc tiek piemērots aizmugures sistēmas noklusējums — `POST /api/camera/array/capture` nolasa `compression` ķermeņa parametru, `"none"` pēc noklusējuma: nesaspiests, sensora pikseļi tieši tā, kā tie tiek nolasīti (~6,3 MB uz vienu pilnas izšķirtspējas kadru). `"deflate"` (bezzudumu zlib L1 + horizontālais prediktors, ~4,1 MB/kadrs) raksta mazākus failus lēnāk — abi ir bezzudumu un tiek nolasīti identiski importēšanas brīdī. SDK tam neparedz nekādus kwarg; izeja no šīs situācijas ir `chloros-cli lattice array-capture --compression deflate` vai neapstrādāts HTTP. DEFLATE bloķē Python GIL, tādēļ saspiestā rakstīšana netiek paralizēta starp katras kameras rakstīšanas pavedieniem — ilgstošai pilnas izšķirtspējas uzņemšanai ar sensora ātrumu ir nepieciešama nesaspiestā noklusējuma iestatījuma izmantošana. Sīkāka informācija: [CLI atsauces → masīva uzņemšana](cli-reference.md).**Secīgas grupas (HTTP):** galapunkts atgriež `group_seq`; cilpa, kas to nosūta atpakaļ kā `after_seq` nākamajā pieprasījumā, saņem nākamo sinhronizēto grupu — ieskaitot to, kas publicēta, kamēr tika saglabāta iepriekšējā uzņemšana — nevis gaida grupu, kas publicēta pēc pieprasījuma, un `skipped_groups` ziņo par visām grupām, kuras cilpa ir palaidusi garām. `capture_repeated`, CLI komanda `--continuous` un GUI nepārtrauktais režīms to visu dara; intervāla pārbaudes paliek aktuālas katrā plānotajā brīdī.**Eksporta pārrakstīšana katram loceklim (tikai HTTP):**tas pats galapunkts pieņem arī `exclude_serials` (saraksts — izslēdz elementus no saglabātā kopuma; masīvs joprojām darbojas kā viena sinhronizēta grupa, un izslēgtie elementi tiek atgriezti `excluded`), `serial_levels` (`{serial: [level tokens]}` pārrakstīšana katrai kamerai atsevišķi) un `serial_index` (`{serial: bool}` indeksa pārklājuma pārrakstīšana katrai kamerai atsevišķi). Tie ir GUI-paritātes ķermeņa parametri un**vēl nav SDK kwargs**; elementi, kas nav iekļauti kartēs, tiek aizstāti ar masīvam kopējiem `levels` / `render_index`.

##### Izlaisto kameru pārbaude — `CaptureResult.skipped`

`ArraySession.capture()` atgriež `CaptureResult`, kas ir `list` apakšklase: to var iterēt, indeksēt, `len()` — visi esošie modeļi turpina darboties. Jaunais kods var pārbaudīt `.skipped` atribūtu, lai redzētu, kuras kameras tika izslēgtas un kāpēc. Visbiežāk tas notiek ar RGB-kamerām jaukta filtra masīvā, ja tiek pieprasīts `processing="radiance"` vai `"reflectance"` — starojuma intensitāte uz vienu „Bayer” elementu platjoslas sensoram nav nozīmīga, tāpēc aizmugurējā sistēma izlaiž šīs kameras, nevis ģenerē bezjēdzīgus datus.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Iemesla simboli atbilst šādam paraugam: `<level>-not-applicable-to-rgb-cam` (viens ieraksts par katru izlaisto līmeni, katrs no kuriem satur `level`). Kamera, kuras kalibrēšanas pakete nav ielādēta, izlaiž `radiance` / `reflectance` ar `<level>-needs-calibration (connect the camera so its cal pack loads)`, un, ja nav izmantojama DAQ atstarojuma rādījuma, izlaiž ar `reflectance-skipped-no-dls`. Pārējie atstarojumam specifiskie izlaišanas gadījumi ir `reflectance-skipped-no-fresh-dls` (nav pieejams jauns lejupvērstā starojuma rādījums), `reflectance-skipped-bound-daq-unavailable (…)` (neizdevās sasniegt piesaistīto DAQ) un `dls-uncalibrated-band-<nm>` — josla galvenokārt atrodas ārpus DAQ gaismas sensora radiometriski kalibrētā diapazona (~374–974 nm), tāpēc absolūtais, uz DAQ balstītais atstarojuma sadalījums tiek noraidīts, un kadrs tiek strauji pazemināts līdz sensora reakcijai. No piegādātajiem SKU to izraisa tikai F988; šīs kameras atbalstītā darbplūsma ir atstarojuma paneļa darbplūsma.

##### Fails, kurus neizdevās ierakstīt — `CaptureResult.failed`

Uzņemums, kurā izdevās ierakstīt tikai daļu failu, joprojām atgriež: sarakstā ir iekļauti visi faili, kas **tika** ierakstīti, un `.failed` uzskaita pārējos, pa vienam vārdnīcas ierakstam katram failam ar `serial` (`None` apvienotajam kompozītam un masīva `.daq` papildu failam), `level` (`raw`, `radiance`, …, `index`, `composite`, `daq`, vai `match` — `.daq` izcelsme, kad `.daq` tika ierakstīts, bet tā sidecar netika ierakstīts) un `error`. Ja disks ir pilns, ieraksts satur arī `disk_full: True`; ikviens turpmākais ieraksts šajā diskā beigtos ar tādu pašu kļūdu, tāpēc `capture_repeated()` apstājas šajā vietā. Daļējai ierakstīšanai netiek izraisīts izņēmums, un fails `.failed` netika atstāts uz diska nepilnīgi ierakstīts. `.failed` ir tukšs, ja ierakstīšana notiek bez kļūdām.

```python
result = arr.capture("output/", processing="all")
for f in result.failed:
    print(f"SN:{f['serial']} {f['level']} not written: {f['error']}")
if any(f.get("disk_full") for f in result.failed):
    raise SystemExit("drive full -- free space and capture again")
```

##### DAQ, kas nolasa uzņemšanu, saglabā — `.daq_files` un `.daq_matches`

Katru reizi, kad DAQ ir piesaistīts kamerai vai matricai vai iekļauts kopā, katra uzņemšana saglabā izmantoto lejupvērsto nolasījumu: `.daq` uzņemuma mapes apakšmapē `daq/`, kopā ar `.match.json` un `.cameras.json` (kura rādījums tas ir). Tas attiecas arī uz neapstrādātiem vai debayered uzņēmumiem, un fails ir vienāds neatkarīgi no tā, vai uzņēmums tika veikts, izmantojot GUI, CLI vai SDK. `daq=False` to neiekļauj uzņēmumos, kuru līmeņos netiek izmantots DAQ (`radiance`, `reflectance` vai `display` līmenis to joprojām ieraksta); `daq_tolerance_ms` pārvieto pieļaujamās novirzes robežu šim uzņemšanai, nevis izmantotajam rādījumam. Kura DAQ: tā, kas piesaistīta kamerai vai matricai (GUI gaismas sensora izvēle, projekta piesaistījums vai `chloros-cli daq pool-bind`), vai arī jaunākā, kāda ir backendam. CLI apraksta vadības elementus `--no-daq` un `--daq-tolerance-ms` ([CLI atsauces](cli-reference.md#the-daq-reading-a-capture-saves)).

```python
r = cam.capture("out", levels=["raw"])      # a CameraSession; ArraySession results match
for m in r.daq_matches:                      # one per .daq in r.daq_files
    print(m["daq_file"], m["gap_ms"], m["in_tolerance"])
```

Ieraksts saglabā izmantoto gaismas sensora rādījumu kā `.daq` kopā ar `<file>.daq.match.json` blakus, kas norāda, kā rādījums tika saistīts ar ekspozīciju: `method`, `gap_ms` (no ekspozīcijas līdz tuvākajam DAQ rādījumam) un `in_tolerance` (500 ms robežās vai uzņemšanas `daq_tolerance_ms`). Reflektances faila XMP satur tādu pašu informāciju kā `Chloros:DlsMatchMethod`, `Chloros:DlsMatchGapS` un `Chloros:DlsInTolerance`.

Uztveršana gaida DAQ pirmo nolasījumu pēc ekspozīcijas, lai varētu apvienot nolasījumus abās pusēs (`method` `interpolated`). DAQ-E nosūta rādījumu ik pēc 0,4 s dienasgaismā un ik pēc 1–2 s telpās, tādēļ `capture()` tiek atgriezts aptuveni pēc pusotra šāda intervāla; paša ekspozīcija netiek aizkavēta. DAQ, kas ir pārtraucis datu nosūtīšanu, netiek gaidīts. DAQ-E rādījums ir gaismas vidējā vērtība intervālā kopš iepriekšējā rādījuma, tāpēc tas tiek novietots šī intervāla vidū: `gap_ms` tiek mērīts attiecībā pret šo viduspunktu, un `.match.json`'`bracket_stamps_ns` nodrošina rādījumiem pašu laika zīmogus. `ArraySession.continuous()` gaida, nepalēninot uzņemšanu. `capture_repeated()` atpakaļ-atpakaļgaitas cikli negaida, tādējādi saglabājot savu ātrumu, un tiek saskaņoti ar tuvāko rādījumu pirms ekspozīcijas.

Rādījums nekad nezaudē spēku, tādējādi neviens kadrs netiek zaudēts novēlota rādījuma dēļ — tā vietā tam tiek piešķirts marķējums:

- **DAQ datu plūsma ir apstājusies** — tiek izmantots jaunākais rādījums, un `gap_ms` norāda, cik sen tas ir (`in_tolerance: false` — vairāk nekā 500 ms).
- **Nav DAQ, no kura nolasīt**— atvienots (`disconnect()`, `chloros-cli daq pool-disconnect`) vai nav iespējams sazināties ar piesaistīto DAQ — tiek atkārtoti izmantots kameras pēdējais rādījums, un tas tiek attiecināts uz**šo** ekspozīcijai: `method`, `retained`, `gap_ms` no šīs ekspozīcijas, `in_tolerance` tiek pārrēķināts, un `retained_from_epoch_ns` norāda uz ekspozīciju, ar kuru tas sākotnēji tika saskaņots. Kamerai, kurai nekad nav bijis rādījuma, joprojām netiek piešķirts atstarojums, un rezultāts norāda iemeslu (`reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, vai arī attēla uzņemšanas atteikums).

Faili, ko uzņēmums saglabā projektā, kas atvērts programmā Chloros, pievienojas šī projekta failu sarakstam tāpat kā uzņēmumi lietotnē (faili, kas saglabāti citur, paliek nemainīgi): `CameraSession.capture()`, `ArraySession.capture()` (arī `smart=True`) un `capture_cameras_transient()`. Rezultāts to norāda failā `.project_add` (`capture_cameras_transient()`: vārdnīcas `project_add`) -- `held`, `files` un aizmugurējās sistēmas `message`; `held` ir `True`, kamēr notiek apstrādes cikls, un faili tiek pievienoti, tiklīdz tas beidzas. `None`, ja projektā nav pievienots neviens fails.

`CameraSession.capture()` un `ArraySession.capture()` tos atgriež kā rezultātu `.daq_files` un `.daq_matches`, kā HTTP-uztveršanas atbildes (`/api/camera/<serial>/cli-capture`, `/api/camera/capture`, `/api/camera/capture-all-synced`, `/api/camera/array/capture`) satur `daq_files` un `daq_matches`, pa vienam uz katru `.daq`: `daq_file`, `cameras`, `method`, `gap_ms`, `in_tolerance`, `tolerance_ms` un `stream_fault` — DAQ paša datu plūsmas kļūda, ko pārbauda tikai tad, ja rādījums ir ārpus pieļaujamās robežas. Pirms paļauties uz atspoguļojumu no uzņemšanas, pārbaudiet `in_tolerance` tā `.match.json` vai XMP, vai DAQ [`status()`](#daqsensorsession-methods).

`processing` līmeņi:

| Līmenis | Izeja |
| --- | --- |
| `"raw"` | Vienkanāla Bayer (mono kameras: viena josla) tieši no sensora. |
| `"debayered"` *(SDK noklusējums)* | 3-kanālu krāsu attēls: no neapstrādāta attēla atskaitīts kalibrēšanas paketes tumšais nobīde (DSNU), pēc tam OpenCV malu atpazīstošā demosaika — bez linearitātes LUT, līdzena lauka vai krāsu atdalīšanas (mono kameras: 1 kanāls pelēktoņu skalā). Saglabātais TIFF fails ir **RGB**kanālu secībā (fotometriskais RGB, tādējādi jebkurš attēlu skatītājs parāda patiesās krāsas); atmiņā esošie masīvi, kurus atgriež `grab()` / `frame_stream()`, ir OpenCV**BGR** kārtībā — lai salīdzinātu ar saglabāto failu, apgrieziet kanālus (`frame[..., ::-1]`). `chloros-cli process` raksta to pašu rezultātu kā `Debayered_Images`. |
| `"radiance"` | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi. Tikai multispektrālais režīms — RGB-kameras tiek izlaistas. |
| `"reflectance"` | uint16 0..32768 (Pix4D-saderīgs); absolūtas atsauces nodrošināšanai nepieciešama tiešsaistes DAQ savienošana. Tikai multispektrālais režīms. |
| `"display"` | Pilna ķēde, kas atbilst GUI priekšskatījumam (CCM + WB + gamma saskaņā ar kameras profilu). |
| `"all"` | **Viens fails katram piemērojamajam līmenim** katrai kamerai (atbilstoši GUI opcijai „Capture All“ / CLIa noklusējumam). Atgrieztais `CaptureResult` tad satur vienu kadra vārdnīcu uz katru `(cam, level)`, ar līmeni katrā vārdnīcā; nepiemērojamie līmeņi parādās `.skipped`. DAQ, kas nolasa izmantoto ierakstu, tiek saglabāts kā `.daq` papildu fails. |

> **Piezīme — noklusējuma iestatījumi atšķiras no CLI.** `ArraySession.capture()` noklusējuma vērtība ir `processing="debayered"`; komandas `chloros-cli lattice array-capture` noklusējuma vērtība ir `processing="all"`. Lai atspoguļotu SDK/GUI daudzlīmeņu saglabāšanu, eksplicīti norādiet `processing="all"` no SDK.

##### Masīva uztveršanas failu nosaukumi

Katrs līmenis tiek saglabāts savā apakšmapē `output_dir` (`raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` attiecībā uz `display`, `index/`, `composite/`). Katra faila nosaukumā ir norādīta kameras sērijas numura daļa `SN<serial>-<filter>`: sērijas numurs, pēc tam kameras modeļa filtra daļa — `SN214000533-FOCN` attiecībā uz `M3C-L87-FOCN`, `SN261902142-F450` attiecībā uz `M3M-L87-F450` — apvienots ar `-`, tādējādi nosaukumam ir tādi paši ar `_` atdalīti lauki kā iepriekš. Kamera, kuras modeli nevar nolasīt, ir tikai `SN<serial>`, un ierakstiem no agrākām versijām nosaukumā nav filtra. Faila nosaukums ir atkarīgs no tā, cik līmeņus dalībnieks lūdza:

| Dalībnieka pieprasījums | Faila nosaukums | Piemērs (`reflectance`, noklusējums `format="tiff"`) |
| --- | --- | --- |
| **Viens līmenis** (`processing="reflectance"` vai vienu elementu saturošs `levels`) | `sync_<timestamp>_SN<serial>-<filter>.<format>` — bez līmeņa piedēkļa, tāpat kā iepriekšējās versijās, tādējādi esošie skripti turpina darboties | `reflectance/sync_<ts>_SN214000533-FOCN.tiff` |
| **Vairāki līmeņi** (`processing="all"` vai `levels` ar vairāk nekā vienu vienību) | `sync_<timestamp>_SN<serial>-<filter>_<level>.<format>` | `reflectance/sync_<ts>_SN214000533-FOCN_reflectance.tiff` |

[Fiksācijas maska](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) seko pēc produkta nosaukuma, `clamp_masks/<product folder>/<product name>_clampmask.tif`: vienalīmeņa atstarošanas reģistrēšana raksta `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`, savukārt `processing="all"` raksta `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`. Pielikuma noteikums tiek noteikts katram elementam atsevišķi, ņemot vērā pieprasītos līmeņus, tādēļ izlaists līmenis (piemēram, atstarošanas reģistrēšana ar RGB-kameru) to nemaina. Izlasiet ceļus no atgrieztajiem kadriem, nevis veidojiet tos paši. Vienas kameras reģistrēšana (`CameraSession.capture()`) vienmēr pievieno līmeni: `capture_<ts>_SN<serial>-<filter>_<level>.tiff`.

##### PNG / JPEG — tiek ņemti tikai attēli un indeksa faili

PNG vai JPEG formātā ir 8 biti uz kanālu. Ja tiek pieprasīts līmenis, ko šis formāts nespēj ietvert, pieprasījums tiek noraidīts, pirms kaut kas tiek uzņemts, un tiek parādīts ziņojums, kurā norādīts līmenis un TIFF kā formāts, kas to atbalsta:

- **`radiance`**(float32) un**`reflectance`**(uint16, `32768` = 1,0) — un līdz ar to arī `processing="all"`, kas tos ietver. SDK to pārbauda argumentos: `capture()` izraisa**`ValueError`** bez pieprasījuma (`CameraSession`, `ArraySession`, `CameraHandle`, `ArrayHandle`, `capture_smart`).
- **`raw` / `debayered` no kameras, kas pārraida vairāk nekā 8 bitu** (12-bitu M3C, Mono12 M3M) — aizmugurējā sistēma tos noraida, zinot katras kameras bitu dziļumu: HTTP 400 ar `error_code` `FORMAT_CANNOT_HOLD_LEVEL`, kas izraisīts kā `ChlorosConnectError` (`CameraSession` / `ArraySession`) vai `RuntimeError` (`CameraHandle` / `ArrayHandle`). Vienlīmeņa `debayered` uzņemšana no vienas kameras iegūst 8 bitu kadru PNG/JPEG formātā, un tā netiek noraidīta.

Saglabājiet radiometriskos datus kā `format="tiff"` (`ext=".tiff"`); izmantojiet PNG/JPEG formātu `display` un indeksētajiem attēliem.

#### Kā darbojas automātiskā ekspozīcija

- **Vispirms ekspozīcija, pēc tam pastiprinājums.** Ja pastiprinājums ir iestatīts uz „Auto”, tas paliek 0 dB, līdz ekspozīcija sasniedz maksimāli pieļaujamo vērtību.
- **Manuālā ekspozīcija atstāj pastiprinājumu automātiskajā režīmā**, kamēr jūs nemaināt pastiprinājumu vai neiestatāt to manuālajā režīmā. Atgriežoties automātiskajā režīmā, pastiprinājuma robeža ir tāda pati kā iepriekš.
- **Nelielas spilgtas zonas nepadara ainu tumšāku.** Spīdums, kas aizņem līdz 1 % no kadra, tiek ignorēts, un zināma kalibrēšanas mērķa paneļos — līdz 0,5 % no paneļa.
- **DAQ ekspozīcijas maksimālā robeža tiek piemērota tikai tad, kad kalibrēšanas mērķis ir redzes laukā.** Tas novērš balta paneļa pārklāšanos; ja paneļi nav redzami, kamera eksponē atbilstoši ainas apstākļiem.
- **Nemainīga gaisma paliek stabila, bet mainīga gaisma izlīdzinās dažu sekunžu laikā.** Nemainīgas gaismas apstākļos ekspozīcija un tās robeža paliek nemainīgas, un pēc lielām gaismas izmaiņām attēls izlīdzinās dažu sekunžu laikā. Pārspīlējums vairs neatgriezeniski neierobežo ekspozīciju, un, mainoties interesējošajai zonai (ROI), ekspozīcija tiek atjaunota.
- **Spilgtuma mērķis paliek sasniedzamā diapazonā.** Tas nepalielinās, kamēr kamera atrodas ekspozīcijas robežvērtībā un tās pastiprinājums nevar palielināties, tādējādi atgriežoties gaismai, kadrs netiek pārspīlēts.
- **Kamera, kuras automātiskā ekspozīcija vairs nereaģē, tiek noturēta līmenī, kas atbilst esošajam apgaismojumam.** Spilgtā saulē kamera varētu samazināt ekspozīciju līdz minimālajam līmenim un palikt šajā stāvoklī ar tumšu kadru, lai gan visi tās iestatījumi rādītu normālus rādītājus. Chloros uzrauga kadru, notur kameru līmenī, kas tika saglabāts šim apgaismojumam, un atbrīvo to, kad apgaismojums mainās. To netraucē ne tumšs, ne aizklāts gaismas sensors.

#### Ekspozīcija masīva elementiem

LATTICE kamera ierobežo `ExposureTime` līdz vienam kadru ātruma periodam. Kamēr masīvs pārraida datus, Chloros palaista katru elementu ar ieslēgtu `AcquisitionFrameRateEnable`, ar ātrumu, kas ir nedaudz lielāks par trigera ātrumu, tādējādi elementa ekspozīcijas maksimums atrodas nedaudz zem trigera perioda, **jebkurā ROI**:

- Elementa kadra periods ir trigera periods, no kura atskaitīts 15 % no perioda (vismaz 3 ms, ne vairāk kā puse no perioda), un tā ekspozīcijas maksimums ir šis kadra periods, no kura atskaitīti aptuveni 0,1 ms. Piemērs: 10 fps izraisītājs (100 ms) nodrošina elementa ātrumu apmēram 11,8 fps un ekspozīcijas maksimālo robežu apmēram 85 ms.
- **Manuāla `ExposureTime`, kas ir garāka par šo maksimālo ekspozīcijas laiku, palēnina trigera darbību, lai pielāgotos tai.** Chloros vispirms atver šī elementa maksimālo ekspozīcijas laiku atbilstoši norādītajai vērtībai un ļauj lēnākajai frekvencei darboties dažus trigera periodus (rakstīšana var aizņemt dažas sekundes ilgāk), tad to ieraksta, un trigers nedarbojas ātrāk, nekā to atļauj ekspozīcija. Masīvā ar automātisko ātrumu iepriekšējais ātrums atgriežas, tiklīdz katrs elements, kura manuālā ekspozīcija to bija palēninājusi, atgriežas pie automātiskās ekspozīcijas. Vērtība, ko kamera pati nevar uzņemt, joprojām tiek noraidīta, un kļūdas ziņojumā tiek norādīts diapazons, ko tā tagad pieņem, un dalībnieka automātiskā ekspozīcija tiek atjaunota, ja tā tika izslēgta tikai šīs rakstīšanas laikā. Pirms versijas 1.3.0 neliels ROI ierobežoja katru dalībnieku līdz sensora brīvās darbības periodam, neatkarīgi no trigera ātruma: 4,9 ms pie 768 × 304, tādējādi nevienu elementu nevarēja padarīt gaišāku.
- Chloros nekad neīsina manuālo ekspozīciju, lai pielāgotos ātrākam trigerim. Automātiskā frekvence paliek manuālo ekspozīciju atļauto robežās; fiksēta frekvence, kas ir ātrāka par ekspozīcijas atļauto, atstāj šim elementam pietiekami ilgu maksimālo laiku, un tas pieņem trigerus ne ātrāk, kā to atļauj ekspozīcija. Samaziniet ekspozīciju vai fiksēto frekvenci, ja katram elementam ir jāapstrādā katrs trigers.
- **Atgriezeniskā nolasīšana:** nolasot elementa mezglus, tiek parādīts `AcquisitionFrameRateEnable` True un `AcquisitionFrameRate` atbilstoši elementa ātrumam; aktivizācija atkal izslēdzas, kad masīvs apstājas.
- **Masīvam pieder šie divi mezgli. Nerakstiet `AcquisitionFrameRateEnable` vai `AcquisitionFrameRate` uz masīva elementa** — tā vietā iestatiet masīvaātrumu ar `target_fps` / `set_target_fps()`.

Uzrakstiet elementa ekspozīciju ar `arr.set_member_settings(serial, exposure_time=20000)` (atslēgvārdi `CameraSession.set_settings()`; `chloros-cli lattice exposure --serial` darbojas tāpat). Elementa paliek masīvā. Neatveriet masīva elementu ar `connect_camera()`: connect noraida kameru, kas jau atrodas masīvā, un `CameraSession`, ko tas atgrieztu, izņem kameru no tās masīva, kad tā atvienojas. `ArraySession.status()["member_ae"]` ziņo par katra elementaekspozīciju un pastiprinājumu no jaunākā kadra.

### Viens projekta ieraksts, katrs režīms

Projekta mapē tiek saglabāti katras ierīces iestatījumi: `cameras.json` — katras kameras (`cameras[].settings`, vienai kamerai un katram masīva elementam) un katra masīva vispārējie iestatījumi (`arrays[].capture`: formāts, ROI, binning, trigera frekvence, vadu budžets) — kā arī `sensors.json`, katra gaismas sensora iestatījumi (integrācijas laiks, kadru vidējais, ierobežojums). Lietotne, CLI, SDK un MCP serveris visi lasa un raksta vienus un tos pašus failus vienā un tajā pašā veidā:

- **Ierīce, kas pieslēgta projektam, tiek saglabāta tajā un reģistrē tajā katru izmaiņu.** „Projektā” nozīmē, ka projekts ir atvērts aizmugurē (lietotnē) vai — izmantojot `ChlorosProject` (`open_project()`), kas norāda savu mapi aizmugurē (`project_path`). Kamera, matrica vai gaismas sensors, kas projektā vēl nav iekļauts, tiek pievienots tā failiem, kad tas izveido savienojumu (`connect_camera()`, `connect_array()`, gaismas sensora savienojums, CLI, lietotne, `chloros-mcp`), kopā ar tās iestatījumiem. Pāreja no jebkura režīma (`set_settings()`, `ArraySession.set_member_settings()`, `set_target_fps()`, masīva iestatījumi, `set_cap()`, CLI, lietotnes vadības elementiem) tiek saglabātas šī projekta failos jau pēc brīža. Kamerā iestatītais ekspozīcijas laiks, pastiprinājums vai AE mērķis tiek saglabāts kā fiksēts, tādējādi, atverot projektu no jauna, tas tiek saglabāts atkārtoti.
- **Atverot projektu, tiek iestatītas tajā norādītās ierīces** jebkurā režīmā: kamera, matrica vai gaismas sensors, kas ir pieslēgts bez saviem iestatījumiem, saņem projektasaglabātos iestatījumus (`lattice cam-connect` / `array-connect`, ja projekts ir atvērts, `chloros-cli project connect`, `open_project().connect_all()`, lietotnes atkārtota atvēršana). Masīvs, kas pieslēgts ar jaunu identifikatoru, atjaunina projekta saglabāto masīvu ar tām pašām kamerām; masīvs ar citām kamerām tiek pievienots blakus tam.
- **Lietotnes paša saglabāšana nekad nepārraksta ierīces reāllaika iestatījumus** ar to vecāko kopiju un saglabā katra elementa pārrakstījumus, ko saglabājis CLI vai SDK izsaucējs.
- **Pikseļu formāts, ko iestatāt, ir tas, ko projekts saglabā.** Formāts, kas iestatīts masīva iestatījumos, izmantojot `--pixel-format` vai `connect_array(pixel_format=)`, ieskaitot 8 bitu formātu, tiek saglabāts un precīzi atjaunots. Ja nekas nav iestatīts, katra kamera savienojas ar savu 12-bitu noklusējuma iestatījumu (`BayerRG12`, `Mono12` uz mono sensora). Formāta nosaukums norāda uz tā bitu dziļumu; ģimene attiecas uz sensoru, tādēļ krāsu kamera nekad netiek iestatīta uz mono formātu, bet mono kamera — uz Bayer formātu, un rinda `[PixelFormat <serial>] ... written` to norāda, ja formāts tika pārveidots. 8-bitu uzņemuma eksportētie dati ir 8-bitu, ieskaitot atstarojumu: pārmēroti un marķēti ar `PixelScale`.
- Ierīce, kas pieslēgta bez projekta, neieraksta nekas.
- **Ieraksts pieder pie projekta, kas izveidots pēc tā; ja projekts tiek atvērts, ieraksts paliek savā projektā.**Kad tiek izveidots projekts — `create_project()`, `chloros-mcp` vai lietotnes**Jauns projekts**— vai tiek dublikāts (lietotnes funkcija**Dublikāt**, kas atver kopiju), kamēr ieraksts joprojām tiek rakstīts iepriekš atvērtā projektā, ieraksts turpinās, un tas, kas tiek ierakstīts, nonāk jaunajā projektā, tajā pašā vietā (zemāk). Atverot esošu projektu — `ChlorosProject.process()` (kas atver šo projektu aizmugurē), `chloros-cli process`, lietotnes funkcija**Atvērt projektu** — atstāj ierakstu joprojām notiekošu tajā projektā, kurā tas tiek ierakstīts: tas tur tiek pabeigts, un tā faili pievienojas šī projekta failu sarakstam (lietotne aptur katru ierakstu, pirms tā atver projektu).
  - `ArraySession.record()` un `ArraySession.burst()`: video vai sērijas mape ar visiem kadriem tiek pārvietota tur pilnībā, kad ierakstīšana apstājas. Apstāšanās rezultātā tiek ziņots par jauno ceļu (`out_dir`, `video_path`), un no tā tiek izveidota sērijas versija. Ja disks nepieļauj pārvietošanu, ieraksts paliek tur, kur tas tika ierakstīts, un rezultāts to norāda (`relocation_error`). Pārtrauktas sērijas neapstrādātie kadri un `.daq` tiek pievienoti tā projekta failu sarakstam, kurā tā nonāk; video netiek pievienots projekta failu sarakstam.
  - `ArraySession.continuous()`: katrs ieraksts pēc izmaiņām tiek saglabāts jaunajā projektā; tie, kas saglabāti pirms tam, paliek tur, kur ir.
  - `DAQSensorSession.record_start()` (un pašas lietotnes gaismas sensora ieraksts): `.daq` tiek pārvietots uz jaunā projekta `light_sensor` mapi, kad ierakstīšana tiek pārtraukta; `record_stop()` atgriež jauno ceļu, un fails tiek pievienots šī projekta failu sarakstam.

  Ieraksts mapē ārpus atvērtā projekta paliek tur, kur tas ir, un `start_camera_continuous()` / daudzplūsmu uzdevumi saglabā mapi, kas tiem tika norādīta.
- **Lietotnē ieraksti, kas veikti pēc jauna projekta izveides, tiek pārvietoti uz to.** Projekta izveide vai dublikāta izveide neaptur nevienu ieraksta sesiju vai ierakstīšanu, un katrs pēc tam veiktais ieraksts (Capture All, Start Capturing) tiek saglabāts jaunajā projektā un pievienots tā failu sarakstam. Atverot esošu projektu lietotnē, vispirms tiek apturēti visi ieraksti.
- **Projekts netiek pārvietots, kamēr notiek ierakstīšana.**Atvērtā projekta pārvietošana uz citu darba direktoriju (lietotnes**Iestatījumi → Darba direktorija**; neviens SDK izsaukums nepārvieto projektu, HTTP klients, kas veic ierakstu uz `/api/move-project`, to dara) tiek noraidīts ar HTTP 409 `PROJECT_RECORDING`, kamēr masīva ierakstīšana vai sērija, masīva nepārtraukta uzņemšana, kameras nepārtraukts/daudzplūsmu uzdevums vai gaismas sensora ierakstīšana raksta tajā: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.`

Tas nav saistīts ar to, kādā stāvoklī kameras paliek pēc savienojuma pārtraukšanas (skatīt [Kameras atmiņa](#camera-memory--keep_settings--clear_settings)): projekts pats iestata savas kameras, kad tas tiek atvērts.

### Kameras atmiņa — `keep_settings` / `clear_settings`

Kad masīvs ir pieslēgts, tā kameras pārņem masīva iestatījumus, un jūs tos varat mainīt. Kad Chloros atbrīvo kameras — `ArraySession.disconnect()` / `force_disconnect()`, pārejošas funkcijas beigas, `start_camera_continuous()` apstāšanās — katra kamera tiek atgriezta tādā stāvoklī, kādā tā bija pirms savienošanas: tās trigera un sinhronizācijas līnijas vadu savienojumi, PTP un kameras iestatījumi — `ExposureAuto` / `GainAuto` (kā arī manuālo `ExposureTime` / `Gain`), `TargetBrightness`, kā arī AE robežas un amortizāciju, baltā balansu, melnā līmeni, `PixelFormat`, pikseļu apvienošana un ROI, krāsu apstrādes ķēde (`GammaEnable` / `Gamma`, `LUTEnable`, `ColorTransformationEnable`), `ReverseX` / `ReverseY`, `UserSetSelector`, PTP `PtpSlaveOnly` / `PtpDomainNumber`, paketes lielums (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`), stimulācijas (`DeviceLinkThroughputLimitMode` / `DeviceLinkThroughputLimit`, `GevSCPD`, `GevSCFTD`), datu bloka (`ChunkModeActive`, katrs `ChunkEnable` zem savas `ChunkSelector`), trigera ieejas traucējumu novēršana, masīvs nodrošina pakalpojuma sinhronizācijas līniju (`LineFilterSelector` / `LineFilterWidth`) un datu ieguves kadru ātrumu (`AcquisitionFrameRate` tikai tad, ja tā aktivizēšana bija ieslēgta: ja tā ir izslēgta, kadru ātrums rāda maksimālo vērtību, ko ļauj sinhronizācijas ritms, nevis iestatījums). Ir norādītas tikai atšķirības: kamera apstājās pirmā, sinhronizācijas ritms un kadru ātrums pirms ekspozīcijas, kurai nepieciešams savs kadru periods.

Lai kameras izmantotu ārpus projekta, divi argumenti raksta kameras **pašas atmiņā** — to, ar ko tā ieslēdzas, jebkurā programmā (uz `disconnect()` / `force_disconnect()`, visās pārejošajās funkcijās, `start_camera_continuous()` un `ChlorosProject.disconnect_all()`; MCP: tie paši argumenti kā `disconnect_device` un pārejošajos rīkos; CLI: `--keep-settings` / `--clear-settings`):

- **`keep_settings=True`** saglabā katras kameras iestatījumus tādus, kādi tie bija sesijas beigās (UserSet2, izveidots kā sākuma iestatījumu komplekts). Kameras joprojām darbojas brīvi (`TriggerMode` `Off`), sinhronizācijas līnijas vadu savienojumi paliek tādi, kādi tie tika atrasti. Kamera bez UserSet2 saglabā iestatījumus UserSet1; šis dators to atceras, tāpēc Chloros masīva pašatjaunošanās — kas atiestata UserSet1, kas ir novirzījies no Chloros bāzes līnijas — to atstāj nemainīgu.
- **`clear_settings=True`** atjauno katras kameras atmiņu uz rūpnīcas iestatījumiem: Chloros bāzes līniju, kuru sistēmas pašatjaunošanās fiksē — rūpnīcas noklusējumi ar `TriggerMode`, `Off`, `TriggerSource`, `Line2`, automātisko ekspozīciju, RGB kameras kalibrēto baltā balansu saglabāts — UserSet1, izveidots kā sākuma komplekts.
- Ne abi (`ValueError`; aizmugurējā sistēma atbild ar 400). Atbildē ir iekļauts `camera_memory`, katrai kamerai — `{"saved_to": "UserSet2" | "UserSet1" | None, "errors": [...]}` vai `{"cleared": bool, "errors": [...]}` — `force_disconnect()` to atgriež; uzdevumam tas nāk kopā ar `stop()`.

Lietotne tos nepiedāvā.

**Katrs iziešanas veids atjauno kameras.**Atjaunošana atbilstoši sākotnējam stāvoklim notiek katrā iziešanas ceļā: `CameraSession.disconnect()` (lietotnes kamera**X**), lietotnes**X** masīvā vai jebkurā masīva elementā (visas masīvs tiek atbrīvots), lietotnes aizvēršana (aizmugurei tiek atvēlētas līdz 20 s, lai atjaunotu visas kameras, masīvus un darbojošos nepārtraukto ierakstīšanu; īslaicīga funkcija vai vienreizējs izsaukums, kas joprojām darbojas, apstājas nākamajā drošajā punktā, vispirms atjauno savas kameras, līdz 8 s, un atbild ar HTTP 503 `SHUTTING_DOWN`; joprojām darbojošais `connect_array()` apstājas pirms nākamās kameras un atgriež atvērtās kameras, līdz 10 s, un izraisa `ChlorosConnectError` (HTTP 503 `SHUTTING_DOWN`); un tāda, kas ierodas izslēgšanas laikā, neko neatver: HTTP 503 `SHUTTING_DOWN`), kā arī `chloros-cli` komandu, kas tiek pārtraukta vai kuras terminālis tiek aizvērts (tās darbs tiek apturēts un kameras vispirms tiek atgrieztas). Aizmugurējais modulis, kas avarē vai tiek piespiedu kārtā pārtraukts, nevar atgriezt neko; pēc strāvas cikla katra kamera tiek atgriezta savā atmiņā.**Projekts pats iestata savas kameras.** Kad masīvs izveido savienojumu caur projektu — lietotnes atvērtu projektu vai `ChlorosProject`, kas paziņo savu nosaukumu backendam (`project_path`) — katrs masīva elements saņem iestatījumus, ko projekts tam saglabājis: ekspozīciju, pastiprinājumu vai AE mērķi, kas tam tiek saglabāts (uzskatīts par jūsu iestatījumu, tādējādi automātiskā ekspozīcija to neietekmē), kā arī balansa režīmu (Chloros korekcija; kameras paša balansa iestatījums paliek izslēgts), neatkarīgi no tā, kas ir saglabāts kameras atmiņā.

### Uzņemšanas režīmi un ierakstītāji

Masīva virsma atspoguļo GUI uzņemšanas paneli: vienreizējās / nepārtrauktas / intervāla / ātrākā aizslēga režīmi, kā arī divi ierakstītāji (tiešraides kompozītvideo un neapstrādātu attēlu sērija → pārstrāde bezsaistē).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Continuous — every synced group for 60 s, saved by the backend itself
    with arr.continuous("run1/", processing="raw", duration_s=60) as run:
        run.wait()
    print(run.result["captures"], run.result["captures_per_s"],
          run.result["stop_reason"])

    # Combined-index video/GIF recorder: the GUI's combined view, or with no
    # GUI open the array's NDVI (pass expr="..." for another index)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    if rec.result and rec.result.get("error"):   # leaving the block does not raise
        print("recording failed:", rec.result["error"])
    else:
        print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**ir SDK nepārtrauktas/intervāla cilpa. Tā kā nav `Ctrl+C`, lai to pārtrauktu no skripta, jums**ir** jānodod `count` un/vai `duration_s` (tas apstājas, kad tiek sasniegts kāds no tiem). `interval_s` tiek mērīts no katra cikla sākuma (atbilstoši GUI). Nepārtrauktie cikli (`interval_s=0`) veido ķēdi `group_seq` → `after_seq`, tādējādi tie ir secīgas sinhronizētas grupas masīva. Pārējie kwargi tiek tieši nodoti uz `capture()`.
- **`continuous`**ir paša aizmugurējā moduļa nepārtraukta uztveršana — GUI nepārtrauktais režīms.**Izmantojiet to, nevis `capture_repeated()`, ja vēlaties, lai katrs trigers tiktu saglabāts diskā ar augstu kadru ātrumu:** `capture_repeated()` no jūsu skripta veic vienu HTTP pieprasījumu katrai grupai, savukārt `continuous()` uztur divus ierakstus aizmugurē, neizmantojot datu apmaiņu starp grupām. Mērīts ar ROI 512 9 kameru masīvā: 73–77 ieraksti/s, salīdzinot ar masīvu, kas publicē apmēram 78. Saglabājiet `capture_repeated()` intervāla ierakstīšanai (`interval_s`) un attēliem, kurus pārbaudāt vienu pēc otra. Windows vidē, lai nodrošinātu augstu veiktspēju, izslēdziet backend no Microsoft Defender (skatiet [1. solis](#step-1--install-the-chloros-platform-package)).
- **`record`** ir *uzraudzības līmeņa* risinājums: tas ieraksta renderēto kombinēto indeksa kompozīciju reāllaikā. Ja ir atvērts GUI kombinētais skats, tas ieraksta šo skatu tā, kā tas tiek parādīts. Ja neviens skats nav atvērts, backend pats renderē skatu: `expr`, ja jūs nododat vienu, citādi masīva NDVI (sarkanā un NIR joslas, kas atrodas vistuvāk 660 nm un 850 nm, kā tās uztver reāllaika skats), tiklīdz masīvs ir izlīdzināts. Nosauciet joslas `expr` tā, kā tās nosauc masīvs: `<band>_<filter>` M3C kamerai (`NIR_850_RGN`, `Red_660_RGN`, `Orange_615_OCN`), `Mono_<filter>` — M3M kamerai (`Mono_850`); īss krāsas nosaukums (`NIR`, `Red`) darbojas tikai tad, ja to satur viena masīva josla. Ierakstīšanas laikā atvērtais GUI skats tiek ierakstīts, kamēr tas paliek atvērts. Ieraksts, kas neieraksta nevienu kadru, beidzas ar kļūdu `error` `"no-frames: …"` (masīvs var joprojām veikt saskaņošanu vai ekspozīcijas stabilizēšanu), un tiek izraisīta kļūda `stop()` (skatīt [`RecorderHandle`](#recorderhandle)). Viens kompozītās ierakstīšanas modulis uz vienu masīvu (izraisa kļūdu, ja kāds jau darbojas).
- **`burst` → `build_video`** ir *analīzes līmeņa*: `burst` raksta neapstrādātus kadrus + manifestu katram kadram + vienu `.daq` par katru atšķirīgu DLS nolasījumu saskaņā ar `<output>/bursts/<base>/` ar pilnu uzņemšanas cikla ātrumu (bez ķēdes, bez exiftool, bez tiešraides). `build_video` sinhronizē katru kadru ar tuvāko `.daq` un atkārtoti palaida importēšanas cauruļvada starojuma/reflektances/indeksa ķēdi. `products` ir `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` saraksts (noklusējums: apvienotais indekss). `burst().stop()` arī automātiski uzsāk pēc iespējas labāko apvienotoindeksa izveidi, kas tiek atgriezta kā `build_job` pārtraukšanas rezultātā.

##### `build_video` rezultāts

| Atslēga | Nozīme |
| --- | --- |
| `success` | `True` tikai tad, ja visi pieprasītie produkti tika izveidoti atbilstoši pieprasījumam: vismaz viens izvades rezultāts, nekas `skipped`, un nav `degraded`. |
| `outputs` | Uzrakstīto video (un GIF failu) ceļi. |
| `errors` | Problēmas, teksta formātā. |
| `skipped` | Viens `{"serial", "level", "reason"}` par katru pieprasīto produktu, no kura **netika** iegūts video — kameras (nav kalibrēšanas paketes šai kamerai, nav DLS rādījuma par atstarojumu, apstrādes ķēdes kļūme) vai kombinētā indeksa, kurā ietilpst `"kind": "combined"` un `serial` `None` (kameru vidū nav sarkanā/NIR joslu pāra, nav grupas ar starojuma intensitāti abām joslām). Jebkurš ieraksts rada `success` `False`; pārējie video joprojām tiek ierakstīti un uzskaitīti. |
| `substitutions` | Kameras renderētas citā līmenī nekā pieprasīts, un kāpēc — RGB-kamera, kurai pieprasīts starojuma intensitātes/atstarošanas rādītājs, saņem savu attēla video, kam veikta baltā balansēšana, ja ir pieejams kameras kalibrēšanas komplekts. Tas nav kļūda. |
| `notes` | Informatīvs teksts, nevis kļūdas — piemēram, kamerai, kurai nav indeksa izteiksmes katrai kamerai atsevišķi, nav indeksa videoklipa, vai arī RGB-kameras attēla videoklipam nav veikts balansa iestatījums (nav kalibrēšanas paketes vai tā ir bez rūpnīcas balansa iestatījuma), norādot kameras nosaukumu. Nav kļūda. |
| `degraded` | `True`, ja produkts tika izveidots, bet tas nav pieprasītais: bezsaistes kombinētais indekss ir iebūvētais sarkanais/NIR normalizētais starpības indekss, tāpēc konfigurētais indeksa izteiksme netiek izvērtēta. Izraisa `success` `False`. |
| `combined` | Kombinētā indeksa kopsavilkums: izmantotie spektra joslas, LUT diapazons, izveidotie kadri. |
| `clamp_masks` | Katrakatram kadram kalibrētajiem TIFF failiem (`save_tiffs=True`), atrodas `clamp_masks/<level>/` blakus TIFF failu `<level>/` mapei, pa vienam katram TIFF failam, kurā ir fiksēts pikselis. Videoklipā tādu nav. |
| `clamp_fractions` | Kas video ir norādīts maskas vietā: viens `{"kind", "serial", "level", "frames"}` uz katru video produktu (`kind` `"combined"` apvienotā indeksa dalībnieku kamerām), `frames` saraksts ar `[frame, R, G, B]` — to pikseļu daļu no attēla, kurus atdalīšanas process ierobežoja joslā, ko aizņem tā R / G / B kanāls (`Chloros:Band{R,G,B}UnmixClampFraction`). |

#### `RecorderHandle`

Atgriež `ArraySession.record()` un `ArraySession.burst()`. Izmantojiet to kā konteksta pārvaldnieku, lai automātiski apstātos, izietot no skopā, vai vadiet to manuāli.

| Loceklis | Apraksts |
| --- | --- |
| `job_id` | Aizmugures uzdevuma identifikators (str). |
| `kind` | `"composite"` (no `record`) vai `"raw"` (no `burst`). |
| `start_stats` | Vārdnīca, ko atgriež `start` izsaukums. |
| `result` | `None` darbības laikā; galīgais apstāšanās rezultātu vārdnīca pēc apstāšanās. |
| `stats(timeout=10.0)` | Darba statistika reāllaikā (ierakstītie kadri, sasniegtie kadri sekundē, pagājušais). |
| `stop(timeout=60.0)` | Ierakstītāja apturēšana; atgriež un saglabā galīgo rezultātu (`.result` paliek `None`, ja pats apturēšanas pieprasījums ir neizdevies). Attiecībā uz `record()` ierakstu, kas neizdevās — tādu, kurā netika ierakstīts neviens kadrs (`error` `"no-frames: …"`) — rezultāts tiek saglabāts kešatmiņā kā `.result`, pēc tam tiek izraisīts `ChlorosConnectError`. Idempotents (otrais izsaukums atgriež kešatmiņā saglabāto rezultātu, neizraisot kļūdu). Izejot no `with` bloka, ierakstītājs tiek apturēts bez kļūdas ģenerēšanas, tāpēc pēc tam pārbaudiet `rec.result.get("error")`. |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

#### `ContinuousHandle`

Atgriež `ArraySession.continuous()`. Izmantojiet to kā konteksta pārvaldnieku — iziešana no `with` bloka aptur ierakstīšanu, ja tā joprojām darbojas — vai vadiet to ar `status()` / `wait()` / `stop()`. Vienlaikus katram masīvam tiek izpildīta viena nepārtraukta uztveršana.

| Elementa nosaukums | Apraksts |
| --- | --- |
| `start_status` | Stāvokļa vārdnīca, ko atgriež sākšanas izsaukums. |
| `result` | `None` darbības laikā; galīgais statusa vārdnīcas ieraksts pēc uztveršanas beigām (nosaka `stop()`, vai ar `status()` / `wait()`, kas fiksēja tās beigas). |
| `status(timeout=10.0)` | Reāllaika statusa vārdnīca (lauki zemāk). |
| `wait(timeout=None, poll_s=0.5)` | Bloķē, līdz ierakstīšana beidzas pati — tiek sasniegts `max_captures` vai `duration_s`, masīvs ir izsmelts, disks ir pilns, ir notikusi kļūda — un atgriež galīgo statusu. Izmantojot `timeout`, atgriež jaunāko statusu pēc norādītā laika, pat ja process joprojām darbojas: pārbaudiet `running`. |
| `stop(timeout=30.0)` | Pārtrauc ierakstīšanu un atgriež tās galīgo statusu. Idempotents (otrais izsaukums atgriež kešēto rezultātu). |

Statusa lauki:

| Lauks | Nozīme |
| --- | --- |
| `running` | „True”, kamēr notiek ierakstīšana. |
| `captures` | Sinhronizētās grupas saglabātas. |
| `files` | Faili ierakstīti. |
| `failed_files` | Faili, kurus neizdevās ierakstīt (kā aprakstīts [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)). |
| `failed_captures` | Kopumā neveiksmīgi saglabāšanas mēģinājumi, ieskaitot tos, kurus mēģināja atkārtot (HTTP 503 kļūda, jo masīvam laicīgi nebija jaunas grupas). |
| `skipped_groups` | Masīva publicētās grupas, kas netika saglabātas (saglabāšana atpalika). |
| `captures_per_s` | Vidējais saglabāšanas ātrums kopš sākuma. |
| `elapsed_s` / `started_at` | Līdz šim pagājušās sekundes; sākuma laiks (Unix epohas sekundes). |
| `last_group_seq` | Jaunākā sinhronizētā un saglabātā grupa. |
| `last_error` | Pēdējā kļūda vai `None`. |
| `stop_reason` | Pēc beigām: `count` (sasniegts `max_captures`), `duration` (sasniegts `duration_s`), `stopped` (izsaukts `stop()`), `array_gone` (masīvs tika atvienots), `error` (20 kļūdas pēc kārtas vai pieprasījums, ko aizmugurējā sistēma noraidīja), `disk_full` (fails, kuram diskā nebija vietas). `None` darbības laikā. |
| `max_captures` / `duration_s` | Sākotnējās robežas (`0` = nav). |
| `defender_cores` | Microsoft Defender izmantotie procesora kodoli pēdējo 10 sekunžu laikā; `None`, līdz tiek izmērīts kāds kodols, un vienmēr `None` datorā bez Defender. |
| `defender_warned` | Ir „True”, ja reģistrēts, ka „Defender” to palēnina (izmantojot vienu vai vairākus kodolus). Izslēdziet backend no „Defender” skenēšanas — skatiet [1. solis](#step-1--install-the-chloros-platform-package). |

```python
run = arr.continuous("captures/run1", processing="raw", max_captures=5000)
try:
    while True:
        st = run.wait(timeout=5)              # returns every 5 s while running
        print(f"{st['captures']} captures, {st['captures_per_s']:.1f}/s, "
              f"{st['skipped_groups']} skipped")
        if not st["running"]:
            break
finally:
    final = run.stop()                        # no-op once it has ended
if final["failed_files"] or final["stop_reason"] in ("disk_full", "error"):
    raise SystemExit(f"continuous capture: {final['stop_reason']}: "
                     f"{final['last_error']}")
```

### Reāllaika kadri jūsu paša apstrādes ķēdē

Lai sinhronizētus kadrus ievadītu savā kodā — mašīnmācībā, pielāgotā skatītājā, savā ierakstītājā — izmantojiet `ArraySession.frame_stream()`. Katrs takts ģenerē `GrabResult`: `{serial: numpy array}` katrai kamerai, **visas no viena trigera**, ar šī trigera identifikatoru `.group_id`. Tā ir tā pati īstenošana kā projekta API `ArrayHandle.frame_stream()`, tāpēc viss, kas aprakstīts sadaļās [Viens trigers uz vienu nolasīšanu](#one-trigger-per-read--group_id), [`band`](#exporting-a-single-band--band), [`align`](#aligning-the-streams--align) un [`bit_depth`](#bit_depth--trading-precision-for-bandwidth) attiecas uz abiem.

```python
import chloros_sdk
from chloros_sdk import FrameFetchError

serials = ["213800234", "214000533", "214701288", "214701292"]   # master first

with chloros_sdk.connect_array(serials) as arr:
    try:
        for frames in arr.frame_stream(processing="raw", fps=10, duration=300,
                                       with_metadata=True):
            if not frames:              # no camera answered this tick
                continue
            if frames.errors:           # cameras missing from this trigger
                print("left out:", {s: str(e) for s, e in frames.errors.items()})
            for serial, (img, hdr) in frames.items():
                # img: numpy array (raw: one Bayer or mono plane)
                # hdr: group_id, frame_id, timestamp_ns, exposure_us, gain_db, ...
                my_pipeline(frames.group_id, serial, img, hdr["timestamp_ns"])
    except FrameFetchError as exc:      # the stream cannot continue
        print("stream ended:", exc.status, exc)
```

- **Katra nodotā kopa ir jauns trigers.** `frames.group_id` nekad neatkārtojas vienā plūsmā; ja pieprasāt vairāk `fps`, nekā masīvs apstrādā, plūsma gaida nākamo trigeri, tādējādi tā darbojas pēc lēnākā no abiem.
- **Trūkstošās kameras tiek nosauktas, nevis aizpildītas.** Kamera, kuru nevarēja apkalpot šajā trigerī, nav iekļauta vārdnīcā un tiek uzskaitīta `frames.errors`.
- **Tas beidzas pēkšņi.** `FrameFetchError` atteikuma gadījumā nākamais cikls atkārtotos pēc 60 sekundēm bez kadra vai pēc 60 sekundēm bez jauna trigera.**Ko tas nodrošina.**`frame_stream()` pārvieto veselu numpy kadru kopu pa lokālo HTTP savienojumu un sasniedz maksimālo ātrumu aptuveni**170 MB/s**. Tas ir pietiekami neapstrādātu kadru, grupētu vai ROI masīvu apstrādei reāllaikā, taču nepietiek, lai katru trigeri saglabātu pilnā izšķirtspējā — lai saglabātu**katru** trigeri, saglabājiet uz diska ar [`continuous()`](#continuoushandle). Mērīts uz četru kameru masīva (FOCN, FRGN, FRGB, F450) ar saspiestu datu pārraidi; katrs takts bija pilns viena trigera komplekts:

| | Pilns sensors (masīvs 14,2–14,4 fps) | 2 × 2 apvienošana (masīvs ~33 fps) |
| --- | --- | --- |
| `frame_stream(processing="raw")` | 6,7 komplekti/s | 22,2 komplekti/s |
| `frame_stream(processing="debayered")` | 2,3 komplekti/s | 9,3 komplekti/s |
| `frame_stream(processing="display")` | 0,8 komplekti/s | 3,0 komplekti/s |
| MJPEG katrai kamerai, 4 straumes ar izšķirtspēju 720p | katra 13,5 kadri sekundē; katrs kadrs saskaņots ar izraisītāju | katra ~25 kadri sekundē; ~45 % izraisītāju ir klāt visās 4 straumēs |

Izmantojiet `processing="raw"`, lai nodrošinātu caurlaidspēju un demosaiku savā pusē; `debayered` līmenis arī noņem kalibrēšanas paketes tumšā nobīdi, ko `raw` demosaika neveic.

#### MJPEG straumes katrai kamerai (bez RTSP)

**RTSP netiek atbalstīts.** Straumēšanas saskarne ir MJPEG pār HTTP (`multipart/x-mixed-replace`, viens JPEG katrā daļā, katrai daļai ar `Content-Length`), viena URL katrai kamerai:

| URL | Ko tā nodrošina |
| --- | --- |
| `GET /api/camera/stream/<serial>` | 8 bitu priekšskatījums, līdz ~30 kadriem sekundē. Masīva elementam `max_h` (noklusējums 720, 64–2160) un `jpeg_q` (noklusējums 80) nosaka izmēru un kvalitāti. |
| `GET /api/camera/<serial>/stream-annotated` | Tas pats priekšskatījums ar uz tā uzvilktām GUI pārklājuma līnijām (zebra, režģis, krustveida līnija, histogramma, maksimuma rādītājs, punktu ekspozīcijas mērītājs). `ChlorosLocal.camera_stream()` to nolasīs jūsu vietā. |
| `GET /api/camera/<serial>/stream-reflectance` | 8 bitu atstarojums katrā joslā — skatiet [Reāllaika atstarojuma plūsma](#live-reflectance-stream-mjpeg). |

- **Trigera galvenes.** Masīva elementam katrā no visām trim plūsmām ir `X-Chloros-Group-Id`, `X-Chloros-Frame-Id` un `X-Chloros-Timestamp-Ns`. Daļas no dažādām kamerām ar vienādu `X-Chloros-Group-Id` ir viens un tas pats trigers: savāciet daļas un grupējiet pēc šīs galvenes, lai atkal saliktu kopu. Atsevišķai kamerai nav grupas: tās priekšskatījuma daļas nesatur nevienu no šīm galvenēm (tās atstarošanas detaļām ir tukšs `X-Chloros-Group-Id`).
- **Priekšskatīšanas plūsma slodzes apstākļos izlaiž kadrus.** Tā nodrošina jaunāko kadru un izlaiž visus, kurus nevarēja nosūtīt laicīgi, tāpēc pie augstiem masīva pārraides ātrumiem ne katrs trigers sasniedz katras kameras plūsmu (skatīt tabulu augšā). Katram trigerim izmantojiet `frame_stream()` vai `continuous()`.
- **`/api/camera/stream/<serial>` aptur darbību, kamēr ir atvērta masīva apvienotā tiešraides plūsma** (`/api/camera/array/stream/<array_id>` — to atver GUI masīva skats). Pēc tam katras kameras straume nosūta vienu aizstājējkadru „Paused (array stream active)“ bez `X-Chloros-*` galvenes, un neko vairāk, līdz apvienotā straume tiek aizvērta; pēc tam tā pati atsāk darbību. `stream-annotated` un `stream-reflectance` nepauzējas. Aizveriet GUI masīva skatu, kad skripts nolasa katras kameras priekšskatījuma straumes.
- **Skaitļiem, nevis attēliem, izmantojiet `frame_stream()`.**JPEGs ir ar zudumiem un 8 bitu; `frame_stream()` atgriež tos pašus rezultātus bez kodeka ceļā.

### Intervāla uzņemšana dronu apsekojumiem

Apsekojuma lidojumam ir nepieciešama viena sinhronizēta attēlu grupa ik pēc dažiem metriem lidojuma, nevis katrs kadrs, ko masīvs spēj ģenerēt. To kontrolē divi iestatījumi:**`target_fps`**nosaka, cik bieži kameras uzņem kadru, un**`capture_repeated(interval_s=…)`** nosaka, cik bieži kadrs tiek saglabāts.**Kameras turpina straumēt starp saglabāšanas reizēm.** Katrs intervāls saglabā jaunāko sinhronizēto grupu; starp tiem esošie kadri netiek ierakstīti. Automātiskā ekspozīcija pielāgojas katram kadram, tādējādi ekspozīcija jau ir nostabilizējusies, kad notiek saglabāšana. Nav tāda režīma, kurā kameras apstātos starp uzņēmumiem un tiktu aktivizētas katram atsevišķi: tādā gadījumā katram uzņēmumam būtu jāgaida, līdz sākas datu plūsma un stabilizējas ekspozīcija.**Fiksējiet kadru ātrumu atbilstoši intervāla vajadzībām.**Ja atstājat automātisko režīmu, masīvs darbojas ar augstāko ātrumu, kādu atbalsta serveris un tīkls. Fiksējot zemāku ātrumu, samazinās kameras siltuma izdalīšanās, tīkla slodze un servera procesora noslogojums, nemainot to, kas tiek saglabāts. Izvēlieties ātrumu, kas nodrošina**2–4 kadrus vienā intervālā**:

- Katrs saglabāšanas process gaida nākamo kadru pēc pieprasījuma saņemšanas, tāpēc saglabātā grupa nonāk līdz pat viena kadra perioda ilgumā pēc plānotā brīža. Augstāks kadru skaits samazina šo starpību.
- Automātiskā ekspozīcija pielāgojas reizi vienā kadrā, tāpēc ļoti zems kadru skaits palēnina to, cik ātri ekspozīcija reaģē uz garām peldošu mākoni.

**Aprēķiniet intervālu, balstoties uz lidojuma plānu:**

```
spacing    = along-track footprint × (1 − forward overlap)
interval_s = spacing / ground speed
```

Piemēram, 60 m pārklājuma laukums ar 80 % pārklāšanos uz priekšu nozīmē 12 m atstarpi; pie 10 m/s tas ir `interval_s=1.2`, un fiksēts `target_fps=3` nodrošina 3 līdz 4 kadrus vienā intervālā. Uzņemšana notiek pēc laika: SDK-ā nav attāluma vai GPS vadīta režīma, tāpēc uzturiet nemainīgu ātrumu attiecībā pret zemi uz mērījumu līnijām.

**Saglabājiet neapstrādātos datus lidaparātā un kalibrējiet vēlāk.** `interval_s` tiek mērīts no katras saglabāšanas sākuma, tādēļ saglabāšana, kas ilgst ilgāk par intervālu, izraisa nākamās saglabāšanas novēlošanos un attāluma novirzes. Saglabājot neapstrādātos datus kopā ar gaismas sensora rādījumu (`processing="raw"`; rādījums tiek saglabāts pēc noklusējuma), katrs saglabāšanas process ir īss un katrs kadrs vēlāk ir pārveidojams par atstarojumu.

```python
import chloros_sdk

INTERVAL_S = 1.2      # from the flight plan
FLIGHT_S = 20 * 60    # bounds the loop; it also stops at count= if given

with chloros_sdk.connect_array(serials, target_fps=3.0) as arr:
    groups = arr.capture_repeated(
        "survey/", interval_s=INTERVAL_S, duration_s=FLIGHT_S,
        processing="raw", render_index=False,
        on_capture=lambda i, r: print(f"group {i}: {len(r)} frames"))
```

- **`connect_array(target_fps=…)`**fiksē ātrumu no sākuma;**`arr.set_target_fps(fps)`**to maina lidojuma laikā bez atkārtotas savienošanās, un**`arr.set_target_fps(None)`** atgriež to automātiskajā režīmā. Ātrums, kas pārsniedz to, ko var pārraidīt serveris un tīkls, tiek samazināts līdz šim maksimumam — pārbaudiet `clamped` rezultātos —, un zemākais ātrums ir 0,1 fps.
- **Nelietojiet `burst()`, lai samazinātu kadru skaitu.** Tas ieraksta katru kadru, ko ģenerē masīvs; tas seko fiksētam `target_fps`, tādēļ tas ir piemērots rīks tikai tad, ja ir nepieciešami visi kadri.
- **`target_fps` nelabo bojātus kadrus.** Tā samazināšana samazina ģenerēto kadru skaitu, nevis katra kadra nosūtīšanas ātrumu; GVSP bojātu kadru gadījumā tā vietā samaziniet `wire_ceiling_mbps` (skatiet [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames)).

```python
r = arr.set_target_fps(5.0)
if r["clamped"]:
    print(f"asked for {r['requested']} fps, running at {r['target_fps']}")
```

### Pievienošanās jau savienotam masīvam — `attach_array`

Ja masīvs jau darbojas (to atvēra GUI vai iepriekšējā SDK sesija izsauca `connect_array`), izmantojiet `attach_array`, lai iegūtu piekļuves rīku, nevis veicot atkārtotu savienošanos. Šādā situācijā `connect_array` vienmēr izdod kļūdu „Camera  <sn>jau ir masīvā<id>”, jo POST pieprasījums ar kodu `/array/connect` attiecībā uz pūla elementu nav idempotents; `attach_array` nolasa `/api/camera/array/list` un veic saskaņošanu pēc array_id vai sērijas numura.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Paraugs: SDK skriptiem, kas darbojas kopā ar darbvirsmas grafisko lietotāja saskarni, vispirms jāmēģina izmantot `attach_array` un jāpāriet uz `connect_array`, ja pūlā vēl nav neviena masīva.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Pievienotais masīvs ir aizņemts, nevis pieder.** `attach_array` rokturis nekad nenodzēš masīvu: to neatbrīvo ne `with` bloka iziešana, ne arī eksplicīts `disconnect()` izsaukums (eksplicītais izsaukums izdrukā vienu rindu uz stderr, norādot, ka masīvs joprojām darbojas). Tas pats noteikums attiecas uz `CameraSession` / `DAQSensorSession`. Tādējādi kopīga izmantošana ar GUI ir droša gan ar, gan bez `with`:
>
> ```python
> with chloros_sdk.attach_array(serials) as arr:
>     arr.capture("output/", processing="reflectance")
> # … the array stays up for the GUI
> ```
>
> Ja jūs patiešām vēlaties to pārtraukt — piemēram, lai atkārtoti izveidotu savienojumu ar citu `wire_ceiling_mbps` — izsauciet `arr.force_disconnect()`. Atkārtota savienošanās pēc vienkārša `disconnect()` uz pievienota roktura neizdodas ar kļūdu "Kamera <sn>jau </sn>atrodas <sn>masīvā<id>", jo nekas netika atbrīvots.

### Tīkla analīzes palīgs

Noderīgs pirms masīva atvēršanas — prognozē, vai jūsu ierosinātie iestatījumi būs piemēroti:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    # width / height are sensor pixels BEFORE binning (what connect_array
    # takes with binning=); out_width / out_height is what is streamed.
    print(f"Connect with width={r['width']} height={r['height']} "
          f"binning={r['binning']} ({r['out_width']}x{r['out_height']} streamed)")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

```python
analyze_array_network(
    master_serial,
    slave_serials=None,
    width=2048, height=1536,              # sensor pixels before binning
    pixel_format=None,                    # None = what connect applies (Mono12 / BayerRG12)
    binning=1,                            # 1 | 2 | 4
    force_tier=None,
    packed="auto",                        # "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",
    timeout=30.0,
) -> dict
```

Tas nekad nepalaiž aizmuguri: ja tā neatbild, tiek izraisīta kļūda `ChlorosConnectError`, savukārt noraidīts pieprasījums tiek atgriezts kā rezultāts. Nepareiza `packed` izraisa kļūdu `ValueError`.

`status` ir viens no `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (citādi `error` — ieskaitot noraidītu pieprasījumu: nav pieteicies, `error_code: AUTH_REQUIRED`, vai plāns zem SDK minimālā līmeņa, `PLAN_UPGRADE_REQUIRED`). `auto_capped_fps` nozīmē, ka pieprasītā izšķirtspēja atbilst RX gredzenam tikai ar ierobežotu trigera ātrumu — saglabājiet izšķirtspēju un pārejiet no `target_fps=result["recommended"]["recommended_target_fps"]` uz `connect_array` (skatīt [6. piemēru](#6-capability-probe-before-connecting-a-4-cam-array)). `auto_shrunk` parādās tikai tad, ja pieprasīto kadru nevar piegādāt ar nekādu kadru ātrumu; savienojums nekad pats par sevi nesamazina kadrus, tāpēc `recommended` piemērojiet paši.

**Kā izlasīt projekciju** (tāds pats modelis kā GUI paneļa „Array Settings”):

- **Burst (`frame_bytes_total`) tiek summēts katrai kamerai atsevišķi, izmantojot katras kameras reālo pikseļu formātu.**Mono**M3M**kameras pārraida Mono12 (2 B/px) vai, izmantojot `pixel_format`, savas dziļuma Mono formātu (`"BayerRG8"` ir `Mono8`, 1 B/px), tādējādi bez tā 4 kameru pilnas izšķirtspējas kadrs ir**~25 MB** ar trim mono kamerām, nevis ~12,6 MB, kā to paredz pieņēmums par pilnībā 8 bitu formātu. Aizmugurējā sistēma nosaka katras kameras formātu pēc tās modeļa.
- **Admittance (`burst_fits_nic_ring`) ņem vērā datu plūsmas iztukšošanu**, nevis „visas pārraides pret gredzenu”: „sim-emit” režīms darbojas, ja hosts iztukšo RX gredzenu ātrāk, nekā kameras to piepilda. 10G hosts + 1 GbE kameras**atbalsta** pilnas izšķirtspējas attēlus pat tad, ja pārraides apjoms pārsniedz gredzena kapacitāti; 1 GbE hosts bloķē (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` ir konservatīvs sērijas izguves maksimums** — `max(readout+emit, N×emit)` ar katras kameras emisiju ierobežotu līdz 1 GbE kameras savienojumam, neatkarīgi no ekspozīcijas. Piemēram, ~2,8 kadrus sekundē 4 kameru pilnas izšķirtspējas 12 bitu matricai (atbilst izpildes laikā izmērītajiem ~2,7–3,0). Pilnais modelis: [CLI atsauce → Masīva kadru skaits sekundē un sērijas uzņemšanas modelis](cli-reference.md#array-fps--burst-model).
- **Pēc nepārtrauktas uzņemšanas projekcija norāda, kāds bija kadru ātrums.** Uzņemšana, kas ilgst 30 s vai vairāk ar automātisko kadru ātrumu, reģistrē to kadru ātrumu, kas šajā datorā nostabilizējās pie šī masīva izmēra; kadru ātruma kontroliera īslaicīgie mēģinājumi sasniegt ātrāku kadru ātrumu netiek ņemti vērā. No šī brīža `array-connect`'rinda, `connect_array()` analīze un paneļa „Masīva iestatījumi” norāda šo ātrumu, ja tas ir zemāks par modeļa ātrumu, norādot, kas to ierobežoja `cycle_limiter`: `"clock"` (masīvs darbojās ar to ātrumu, uz kuru tas bija nostabilizējies, savukārt datu ieguves cikls gaidīja savu trigera takts signālu), `"loop"` (datu ieguves ciklam vairs nebija laika), `"capture"` (uzņemšana atpalika un izlaida grupas) vai `"measured"` (nav norādīts, kurš no tiem). Modeļa rādītājs ir `wire_fps_max`. Masīvs joprojām pieaug līdz tam, ko pārraida vads.
- **Pārslogotība (`oversubscribed: true`) nozīmē, ka vads nespēj pārraidīt masīvu nevienā no kameru sasniegtajiem kadru ātrumiem** — fps lauki (`achievable_fps_max` / `fps_bright` / `fps_dark`) rāda 0, un to nevar labot ar attēlu apvienošanu (tas samazina baitu skaitu kadrā, nevis sinhronizēto baitu skaitu sekundē). Lēns savienojums pats par sevi to neizraisa: tā vietā samazinās kadru ātrums, un, ja kameru darbību nevar sinhronizēt pietiekami lēni, līmenis kļūst par `slip-emit-and-capture` (sērijveida). Risinājumi ir mazāks kameru skaits, jumbo rāmji vai ātrāks tīkla interfeiss; `max_cams_collision_safe` norāda jūsu vadu un kameru maksimālo jaudu. Atbildē ir iekļauti arī `aggregate_demand_bps`, `collision_safe_ceiling_bps` un `per_cam_floor_bps`. Skatīt [Pārslogotība](#over-subscription-the-per-cam-floor).

### Atklāšana un saraksta izveide

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

Katra no tām pieņem tikai atslēgvārdus `backend_url=` un `timeout=30.0` un atgriež vārdnīcu sarakstu. `discover_lattice_cameras()` (`serial`, `ip`, `mac`, `model`, `lattice_model`, `vendor`, `connected`, `array_id`, …) pieņem arī `auto_start_backend=True`; `list_*` izsaukumi nekad neuzsāk aizmugurējo procesu.

---

## Smart-AE / Smart-Capture

LATTICE masīvi, tiklīdz tie ir pieslēgti, fonā nepārtraukti veic AE, taču nesen iestatītai ainai ir nepieciešams brīdis, lai konverģētu. **Smart-capture** ir ērta funkcija: tā noskaidro katras kameras ekspozīciju, gaida, līdz masīvs visā logā ir stabils, un tad sāk uzņemšanu. Tā atbilst GUI: darbvirsmas lietotnes „smart” uzņemšanas poga izsauc to pašu aizmugures galapunktu.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

Izmantojot `ChlorosProject` (nākamajā sadaļā), jums ir pieejami papildu iestatījumi:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Viedās AE politika pēc noklusējuma ir konservatīva. Padariet `exposure_tolerance_pct` stingrāku, ja veicat precīzu radiometrisko darbu; padariet to plašāku strauji mainīgās ainās, kurās vēlaties vienkārši „pietiekami tuvu”.

---

## DAQ sensoru sesijas

Pastāvīgs aizmugures resursu kopums spektrālajiem sensoriem (DAQ-U caur USB, DAQ-M caur BLE, DAQ-E caur Ethernet). Atspoguļo kameras virsmu: viedā atpazīšana, kopuma atkārtota izmantošana, idempotenta pievienošana.

### Viedā atpazīšana (bez konfigurācijas)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Prioritāte: Ethernet → BLE → USB. Norādiet jebkuru konkrētu norādi, lai fiksētu pārraides veidu.

### Fiksēts pārraides veids

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# A specific sensor by id (as list_daq_sensors() shows it; ':' or '-' alike)
daq = chloros_sdk.connect_daq_sensor(sensor_id="CB-7C-A7-5A-73")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

`sensor_id=` nosauc vienu sensoru, un tiek atvērts tikai šis sensors. Tas tiek salīdzināts ar jau atvērtajiem sensoriem, pēc tam tiek pieprasīts DAQ-E uzņēmuma nosaukums vai IP (DAQ-E, kuru tīkla skenēšana redz tikai pēc IP, tiek pieprasīts, tikai lasīšanai, tā nosaukums, MAC un sērijas numurs, tādējādi to var atrast arī pēc jebkura no šiem rādītājiem), DAQ-M BLE MAC adresi un identifikatoru, ko DAQ-U vai DAQ-M paziņo pēc atvēršanas (visas pārējās ierīces, kas tika atvērtas pārbaudei, tiek atkal aizvērtas). Ja ierīce netiek atrasta, `connect_daq_sensor` izraisa `ChlorosConnectError`, uzskaitot to, kas tika atrasts; norādot arī adresi (`port=` / `mac=` / `eth_host=`), kurā atrodas cits sensors, arī tiek izraisīts izņēmums. Tā nekad neizmanto to sensoru, kurš ir redzams — tieši tam ir paredzēts izsaukums ar nulles karodziņu.</id></sn></id></sn>

### `connect_daq_sensor()` Paraksts

```python
connect_daq_sensor(
    *,                                    # every argument is keyword-only
    sensor_id=None,                       # one named sensor (see above)
    transport=None,                       # "usb" | "ble" | "eth"; None = smart-detect
    port=None,                            # DAQ-U serial port ("COM3", "/dev/ttyUSB0")
    mac=None,                             # DAQ-M BLE MAC (implies transport="ble")
    eth_host=None,                        # DAQ-E hostname or IP (implies transport="eth")
    integration_time=None,                # ms; None = the sensor's own
    frame_avg=None,
    enable_ae=None,
    cap_id=None,                          # cap correction: "sunshine_cosine", "fov_30", ..., "none" (bare)
    start_streaming=True,
    backend_url="http://127.0.0.1:5000",
    timeout=60.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> DAQSensorSession
```

Sensors, kas jau atrodas pūlā, tiek atgriezts tāds, kāds tas ir (aizņemts): `integration_time`, `frame_avg` un `enable_ae` tam netiek piemēroti. Lai to atvērtu ar saviem iestatījumiem, vispirms to atvienojiet. Izņēmums ir `cap_id`: aizņemtajam sensoram tas tiek iestatīts ar `set_cap()`. Ja to neievada, tiek izmantots aizmugurējās sistēmas noklusējums (skatiet piezīmi zem metožu tabulas).

### `DAQSensorSession` metodes

| Metode | Apraksts |
| --- | --- |
| `status(timeout=10.0)` | Pūla ieraksta kopsavilkums (straumēšanas/ierakstīšanas stāvoklis, viļņu garuma diapazons, kalibrēšanas sha, integrācijas laiks, frame_avg, AE stāvoklis). Attiecībā uz DAQ-E, kura straume apklusa: `reconnect` (ja backend automātiski atjauno savienojumu: `state`, `attempts`, `next_attempt_in_s`, `last_error`; `None`, kamēr notiek datu plūsma), `stream_lost_s`, `reconnects` un `last_reconnect`, kā arī `record_gaps`, ja notiekošajai ierakstīšanai; `rx_stream_fault` norāda atkārtotas savienošanās stāvokli, līdz tiek saņemts pirmais spektrs. |
| `latest(*, n=1, timeout=10.0)` | Atgriež līdz N pēdējiem spektra kadriem (katrs `ts_ns`, `spectrum`, `is_saturated`, `integration_time`, `x`, `y`, `z`). |
| `stream_start()` / `stream_stop()` | Turpināt / apturēt datu plūsmu (rokturis paliek atvērts). |
| `record_start(*, output_dir=None, device_name=None, timeout=15.0)` | Sāk ierakstīt .daq failu. Atgriež faila ceļu. Netiek atbalstīts DAQ-U/M bez AWS kalibrēšanas komplekta (izņemot DAQ-E). |
| `record_stop()` | Pārtrauc ierakstīšanu. Atgriež `{path, rows, warning, gaps}`; `warning` ir `None`, ja ieraksts ir bez kļūdām, pretējā gadījumā — aizmugurējās sistēmas diagnozi (piemēram, tukšs fails vai pārtraukums plūsmā), kas tiek izvadīta arī uz stderr. `gaps` uzskaita katru posmu, kurā plūsma neko nepiegādāja: `start_utc`, `end_utc` (`None`: joprojām zaudēts apstāšanās brīdī), `duration_s`, `reconnected`, `marked_in_file`. Nepārtrauktas ierakstīšanas gadījumā tas ir tukšs. |
| `set_cap(cap_id, timeout=15.0)` | Iestatiet maksimālā līmeņa korekcijas profilu, kas tiks piemērots nākamajiem un turpmākajiem rādījumiem: `"sunshine_cosine"`, `"fov_15"` ... `"fov_90"` vai `"none"` (vai `None`) neapvalkotam sensoram. Atgriež sensorakopsavilkumu, kuru `cap_id` nolasa atpakaļ. DAQ-E-S saglabā `as_recorded` neatkarīgi no pieprasījuma (tam ir iebūvēts difuzors). Palielina `ChlorosConnectError` profilam, kas neizdodas ielādēt. CLI komanda: `chloros-cli daq pool-set-cap`. |
| `enable_ptp(timeout=15.0)` | Tikai DAQ-E: atkal ieslēdz PTP ierīcei, kuras PTP ir izslēgts (`status()["rx_ptp_enabled"]` ir `False`; iestatījums tiek saglabāts ierīcē, tādēļ tas paliek izslēgts, līdz tiek atkal ieslēgts). Atgriež atjaunināto statusu. Izraisa kļūdu `ChlorosConnectError` ierīcēm DAQ-U / DAQ-M vai gadījumā, ja ierīce neapstiprina. Chloros nekad pats neieslēdz ierīci atpakaļ, jo izslēgts stāvoklis var būt apzināts. CLI komanda: `chloros-cli daq pool-enable-ptp`. |
| `device_info(timeout=15.0)` | Tikai DAQ-E: ierīces paša iestatījumi. `firmware` (`running`, `bundled`, `update_available`), `ip` un `ip_mode` (`dhcp` / `linklocal` / `static`: kas darbojas pašlaik), `network` (saglabātie iestatījumi: `mode`, kā arī `ip` / `mask` / `gateway`, ja ir statisks režīms), `network_change_pending` (spēkā, kamēr ierīce darbojas ar saglabātajiem iestatījumiem, kuros ir iekļauts `set_network(..., reboot=False)`), `auto_stream`, `ptp`. `firmware["update_available"]` ir `None`, ja ierīcesversiju neizdevās nolasīt. Kamēr ierīces datu plūsma nesasniedz šo datoru, tā tiek nolasīta, izveidojot jaunu savienojumu, un vārdnīca pievieno `stream_silent` (`True`), `stream_fault` un `reconnect`; rodas brīdinājums par ierīci, kas neatbild. CLI: `pool-info`. |
| `set_network(mode="auto", *, ip=None, mask=None, gateway=None, reboot=True, force=False, comeback_timeout=45.0)` | DAQ-E tikai: DHCP (`"auto"`) vai statiska adrese. Pārstartē ierīci, lai to piemērotu, un atkārtoti izveido savienojumu; šī sesija turpina darboties, un `address` seko ierīcei. `reboot=False` to tikai saglabā. Skatīt [DAQ-E ierīces iestatījumi](#daq-e-device-settings). CLI komanda: `pool-set-network`. |
| `set_auto_stream(enabled, timeout=15.0)` | Tikai DAQ-E: datu plūsma ieslēgšanas brīdī bez savienotas programmatūras (saglabāts ierīcē). Atgriež apstiprināto iestatījumu. CLI: `pool-set-auto-stream`. |
| `reboot(*, comeback_timeout=45.0)` | Tikai DAQ-E: pārstartē ierīci un atkārtoti pieslēdz to ar iepriekšējiem iestatījumiem. CLI: `pool-reboot`. |
| `update_firmware(*, comeback_timeout=45.0)` | Tikai DAQ-E: uzinstalējiet šajā Chloros izlaidumā iekļauto programmaparatūras versiju ierīcē, kurā darbojas vecāka versija, pēc tam atkārtoti pieslēdziet to (aptuveni viena minūte; neizslēdziet strāvu). Atgriež `updated`, `fw_before`, `fw_after`, `bundled` un `warning`, ja ierīce atgriezās lēni vai atgriezās ar citu versiju; `updated` ir `False` ierīcei, kuras versija jau ir aktuāla. Izraisa kļūdu „`ChlorosConnectError`” ierīcei, kuras versiju nevar nolasīt. CLI: „`pool-update-firmware`”. |
| „`disconnect()`” | Atbrīvošana no pūla. Navoperācija pievienotiem, bet nepiederīgiem rokturiem. Sensoram, ko atvēra šī sesija, tas ir `force_disconnect()`, un izraisa `ChlorosConnectError`, ja atbrīvošana netiek apstiprināta (`with` bloka iziešana nekad neizraisa kļūdu). |
| `force_disconnect(timeout=30.0)` | Atbrīvo sensoru neatkarīgi no tā, kurš to atvēra — piemēram, sensoru, ko avarējis skripts atstājis pūlā un kuru jauns skripts sasniedz tikai kā pievienotu rokturi, kura `disconnect()` neko nedara. Cita klienta sensora pārtraukšana pabeidz jebkādu reģistrēšanu, ko tas veic. Atgriežas, tiklīdz aizmugure apstiprina atbrīvošanu vai ja nav ko atbrīvot (sensors nav pūlā vai nedarbojas neviens backend); atbrīvošana, kurai beidzies laika limits vai kuru backend ir noraidījis, izraisa `ChlorosConnectError`. CLI: `chloros-cli daq pool-disconnect --sensor-id ID`. |

Atribūti: `sensor_id`, `model`, `transport`, `address` (seko DAQ-E, kuru `set_network()` pāradresēja), `info` un `backend_url`.

> **Nosauciet vāciņu.** DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez kondensatora, un Chloros nevar noteikt, vai tas ir uzstādīts: sensors, kas pieslēgts bez `cap_id`, saņem `sunshine_cosine` — pieņēmums, ka tā reģistrācijas dati liecina, ka tas tika veikts (`cap_id_source=auto_default`). Neapvalkots DAQ-U ar šo korekciju rāda apmēram 30 reizes augstākus rādījumus, tāpēc neapvalkotam sensoram norādiet `cap_id="none"` (vai `set_cap("none")`), un citādi — uzstādītā vāciņa.

### DAQ-E-S — fiksēts difuzors un slīpuma sensors

**DAQ-E-S** ir DAQ-E ar divām atšķirībām, kas ir svarīgas programmatūrai. Pārējās funkcijas — atklāšana, savienošana, PTP, ierakstīšana, zemāk minētie ierīces iestatījumi — darbojas tieši tāpat kā DAQ-E gadījumā.**Tā difuzors ir pastāvīgs, tādēļ nav nepieciešama vāciņa korekcija.** „Sunshine” tipa difuzors ir iebūvēts un atradās uz ierīces, kad tika veikta tās rūpnīcas kalibrēšana, tādējādi šī kalibrēšana jau precīzi rāda lejupvērsto starojuma intensitāti. Chloros, neatkarīgi no pieprasījuma, iestata savu vāciņu uz `as_recorded` (bez profila): vāciņa, kas nosaukta pēc tās (`sunshine_cosine`, `fov_*`, `none`) tiek ignorēts, parādot ziņojumu, un tā ierakstos tiek saglabāts `cap_id_source=model`, tādēļ brīdinājums par pieņemto ierobežotāju tam nekad neparādās. Neuzlieciet ierobežojumu tā datiem arī manuāli: `sunshine_cosine` augšējā pozīcijā rādītu apmēram 11 reizes augstāku vērtību.

**Tam ir 3-asu akselerometrs.** Saules gaismas difuzora kosinusa reakcija ir spēkā tikai tad, ja tas atrodas horizontālā stāvoklī, tādēļ sasvērtā sensora rādījumi par starojuma intensitāti ir nepareizi — un atstarojums pārņem šo kļūdu — bez jebkādiem citiem simptomiem. Tādēļ katrs DAQ-E-S rādījums ietver informāciju par sensora orientāciju:

| Lauciņš | Nozīme |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Leņķi grādos. `None`, ja programmatūra atzīmē leņķi kā nedefinētu (brīvais kritiens, nedarbojoša ass). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Paātrinājums miligravos. `mag_mg`, kas ir tālu no ~1000, nozīmē, ka ierīce bija kustībā un leņķi šim paraugam nav nozīmīgi. |
| `cal_applied` | Iestatīts: asis un leņķi ir difuzora koordinātu sistēmā (piemērota ierīces montāžas kalibrācija). Neiestatīts: neapstrādāta platnes koordinātu sistēma, kas var rādīt līdz pat ~2,3° slīpumu, kas nav reāls. |
| `sample_age_ms` | Akselerometra parauga vecums brīdī, kad tika nosūtīts spektrs. |
| `temp_raw` | Nekalibrēta mikroshēmas temperatūras **tendence** — tikai starpības, nekad ne termometra rādījums. |

`DAQSensorSession.latest()` tos atgriež zem katra rāmja `imu` atslēgas (`None` uz DAQ-U, DAQ-M vai parastā DAQ-E). No DAQ-E-S ierakstītais `.daq` saglabā tās pašas vērtības savās `imu_*` kolonnās (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) blakus katram spektram, lai lasītājs varētu atšķirt mākoni no apgāzta statīva. Visos pārējos modeļos šīs kolonnas ir tukšas.

Stāvokļa plūsma ziņo par saviem laukiem `status()` (`rx_imu_stream_fault`, `rx_imu_stream_faults`, `rx_last_imu_stream_fault`), kas ir nodalīti no spektrālajiem laukiem, lai akselerometra darbības pārtraukums nekad netiktu uztverts kā ieraksta pārtraukums. `discover_daq_sensors()` un sesijas `model` norāda uz `DAQ-E-S`.

### DAQ-E ierīces iestatījumi

```python
with chloros_sdk.connect_daq_sensor(eth_host="daq-e-def330.local") as daq:
    info = daq.device_info()
    print(info["ip"], info["ip_mode"], info["firmware"])

    # A fixed address on the network this computer is on
    daq.set_network("static", ip="192.168.1.50", gateway="192.168.1.1")
    print(daq.address)              # 192.168.1.50: the session follows it

    if info["firmware"]["update_available"]:
        daq.update_firmware()
```

- **Šī datora tīklā ir jābūt statiskai adresei.** `set_network` izraisa `ChlorosConnectError` gadījumā, ja subnetā nav iekļauta šī datorasensora tīklā: pēc pārstartēšanas nekas nevarēs sasniegt sensoru, lai to atgrieztu sākotnējā stāvoklī. Izmantojiet `force=True` tikai tad, ja šim datoram tiks piešķirta jauna adrese vai tas tiks maršrutēts uz jauno apakštīklu.
- **`set_network`, `reboot` un `update_firmware` pārstartē sensoru** un izraisa kļūdu `ChlorosConnectError`, kamēr tas veic ierakstīšanu, un ja tas neatgriežas `comeback_timeout` sekunžu laikā. Sensora ID un `daq-e-<id>.local` uzņēmuma nosaukums nekad nemainās.
- **Ieraksts, kas tika sākts tieši pirms pārstartēšanas, tiek slēgts un nosaukts.** Kad sensors atgriežas, atgrieztā vārdnīca satur `recording_closed` (faila ceļu) un `warning`, kas to norāda. Ja sensors neatgriežas vai programmatūras pārraide neizdodas, to norāda `ChlorosConnectError`: tā ziņojums beidzas ar brīdinājumu, un tiek iestatīti `e.recording_closed` / `e.warning` tiek iestatīti (abi `None`, ja netika aizvērta neviena ierakstīšana). Kad sensors ir atgriezies, sāciet ierakstīšanu no jauna.
- **Tiek atkārtoti savienots tikai pats sensors.** Pēc atkārtotās palaišanas ierīcei jāatbild, kur tā atradās — un pēc `set_network("auto")` arī ar savu `.local` nosaukumu — norādot savu nosaukumu, uzņēmuma nosaukumu un modeli, kā arī tīkla adaptera (MAC) adresi, kas tam bija; `address` kļūst par IP adresi, kas atbildēja. Atbilde ar nosaukumu tiek ņemta vērā tikai ar šo MAC adresi, tādēļ no datora, kas sasniedz sensoru caur maršrutētāju, vai no WSL2 noklusējuma NAT tīkla, sensors, kuru pārvietoja DHCP, netiek atkārtoti pieslēgts (`ChlorosConnectError`; atkārtoti savienojiet to ar jauno adresi).
- **Lai veiktu programmaparatūras atjauninājumu, sensoram ir jāizveido savienojums no jauna.** `update_firmware()` nosūta uzaicinājumu ierīcei, un ierīce atver TCP savienojumu atpakaļ uz šo datoru, lai saņemtu attēlu, izmantojot operētājsistēmas izvēlētu portu. Aizmugures serveris klausās adapterī, kas sasniedz ierīci (visos adapteros, ja nevar noteikt, kurš tieši), un apkalpo tikai šo ierīci: jebkurš cits savienojums tiek noraidīts (`[DAQ-FW] … refused a firmware connection from <ip>` aizmugurējās sistēmas izvades logā), kamēr tā turpina gaidīt. Ugunsmūris, kas bloķē savienojumu, izraisa atjaunināšanas kļūdu ar kodu `device never connected back on TCP <port>`. Windows instalētājs pievieno šim nolūkam atbilstošu noteikumu (skatīt [1. solis](#windows-exe)); citā ugunsmūrī atļaujiet ienākošo TCP satiksmi uz backend no sensora apakštīkla.
- **WSL2 vidē izmantojiet spoguļoto tīklu** (`networkingMode=mirrored` un `.wslconfig`): noklusējuma NAT režīmā tiek noraidīta katra statiskā adrese (WSL pašas adrese neatrodas sensora tīklā), datu plūsma nesasniedz WSL, un programmaparatūras atjaunināšana nevar tikt pabeigta.
- Grafiskā lietotāja saskarne (GUI) uztur savu savienojumu ar sensoru: pārstartēšana no skripta pārtrauc GUI sesiju ar to. GUI ir tie paši iestatījumi (cilne „Light Sensors”, sensora iestatījumi, „Network”).

### Atklāšana — adreses meklēšana savienojuma izveidei

`discover_daq_sensors()` pārskata USB / BLE / ETH, meklējot sensorus, kurus jūs *varētu* atvērt. Tas ir DAQ ekvivalents `discover_lattice_cameras()`, un vienīgais veids, kā iegūt **DAQ-M BLE MAC** — DAQ-E ierīcei ir uzņēmuma nosaukums, bet DAQ-U — COM ports, taču MAC adrese nav ne uzrakstīta uz ierīces, ne uzrādīta operētājsistēmā.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               DAQ-U  {'vid': '0403', 'pid': '6001', 'manufacturer': 'FTDI'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Lauks | Apraksts |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM ports / BLE MAC / uzņēmuma nosaukums — nododiet uz `connect_daq_sensor` kā `port=` / `mac=` / `eth_host=`. |
| `display` | Cilvēkam saprotams nosaukums. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` \| `DAQ-E-S`, vai `None`. Ethernet sensors ir `DAQ-E-S` vai `DAQ-E`, jo tas ziņo par sevi, kad tiek pieprasīts, tikai lasāms, skenēšanas laikā (`DAQ-E`, ja tas neatbild laicīgi). USB seriālais ports tiek nolasīts kā `DAQ-U`, ja tā tilta mikroshēma ir viena no tām, ko izmanto DAQ-U (FTDI, CH340, CP210x, Prolific) — norāde no porta apraksta, nevis zonde — un `None` jebkurai citai USB ierīcei, kas joprojām ir sarakstā, tāpēc to var nodot kā `port=`. Sērijas ports bez USB ražotāja identifikatora un bez šāda tilta mikroshēmas (piemēram, pamatplates UART, kā Linux `/dev/ttyS*`, Bluetooth sērijas savienojums, Intel AMT) nevar būt DAQ-U un nav iekļauts sarakstā. |
| `extra` | Informācija par katru transporta veidu (BLE paziņotais nosaukums; USB `vid`, `pid`, `manufacturer`, `serial_number`; DAQ-E ip/fw/…). Tukšas vērtības ir izlaistas. |

| Parametrs | Noklusējums | Apraksts |
| --- | --- | --- |
| `transports` | visi trīs | Sekvence (vai CSV virkne), kas ierobežo skenēšanu. Vērts norādīt, ja zināt, ko vēlaties — BLE ir lēnākais posms. |
| `scan_timeout` | 5 | Skenēšanas logs katram transporta veidam sekundēs; aizmugurējā sistēma ierobežo to līdz 1–20. |
| `timeout` | 60,0 | HTTP maksimālais laiks visam izsaukumam (tāpat kā citur SDK). |
| `auto_start_backend` | `True` | Izveido vietējo backend, ja neviens nedarbojas. Nekad neizveido attālinātu `backend_url`. |

> **Sensori, kas jau ir atvērti pūlā, netiek parādīti.** Savienota BLE perifērija pārtrauc reklāmēšanu, un atvērtu COM portu nevar pārbaudīt, tādēļ atklāšanas sarakstā tiek uzskaitīts tas, kas ir *pieejams savienošanai*. Tūlīt pēc savienošanās ir sagaidāms tukšs rezultāts — izmantojiet `list_daq_sensors()`, lai redzētu to, kas jums jau ir. Transporti, kuru skenēšana nevar tikt veikta (nav instalēts bleak / zeroconf), tiek izlaisti, nevis parādīti kā kļūda, tādējādi ierīce bez Bluetooth joprojām saņem atbildes par USB un ETH.

### Saraksts

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Kura DAQ apkalpo kuru kameru — `daq_bindings()`

Uztveršanas atstarošanas koeficients un `.daq`, ko tas saglabā, izmanto DAQ, kas piesaistīts šai kamerai vai matricai, citādi — jaunāko DAQ, kas pieejams backendā. To nosaka GUI gaismas sensora izvēle un `chloros-cli daq pool-bind`; tāpat kā:

```python
chloros_sdk.set_daq_bindings(cameras={"214701281": "COM7"},          # a sensor id or an address
                             arrays={"array-1779862544497": "daq-e-def330"})
chloros_sdk.set_daq_bindings(cameras={"214701281": None})            # None clears it
for serial, b in chloros_sdk.daq_bindings()["cameras"].items():
    print(serial, b["binding"], b["source"], b["resolved"]["in_pool"])
```

`daq_bindings()` atgriež `{"cameras": {serial: b}, "arrays": {array_id: b}}`: katrs `b` satur `binding` (iestatītā vērtība), `source` (`project`, no projekta `sensors.json` vai `runtime`), `known` / `known_as` (vai kameras vai masīva atslēgas nosaukums ir kāds, ko Chloros zina, un kā: `connected`, `project` — atvērtā projekta `cameras.json` — vai, ja runa ir par kameru, `seen`: iepriekš atvērts šajā datorā; `known: False` atslēgai, kas neko nenosauc) un `resolved` (pūla sensors, ar kuru tas tagad ir saistīts: `in_pool`, `sensor_id`, `address`, `model`, `streaming`). Darbības laika saistīšana ir pārāka par projektasesijas laikā; projekta fails netiek mainīts. Atvienojot kameru, masīvu vai DAQ, tiek atcelta katra šīs aparatūras saistība, neatkarīgi no tā, kurš to ir iestatījis (masīva elementu saistības tiek atceltas kopā ar pašu masīvu).

`set_daq_bindings()` saista tikai kameru, kas ir pieslēgta, atrodas atvērtā projektā vai iepriekš bijusi atvērta šajā datorā, kā arī masīvu, kas ir pieslēgts vai atrodas atvērtā projektā (vai jau saistītu taustiņu). Jebkurš cits taustiņš izraisa `ChlorosConnectError`, nosaucot to (`400`, `code`, `UNKNOWN_BINDING_TARGET`, ar atslēgām `unknown`), un šajā izsaukumā **nekas** netiek piesaistīts. `None` dzēš jebkuru atslēgu.

### Vides + objekta atstarojums — `light_sensor_reflectance()`

Divi apvienoti DAQ, no kuriem viens vērsts uz debesīm (apkārtējā vide) un otrs uz mērķi (objekts), sniedz mērķa atstarojumu un veģetācijas indeksus, balstoties uz saviem jaunākajiem rādījumiem: GUI pāri „Apkārtējā vide + Objekts” un `chloros-cli daq reflectance`, neatsverot sensorus otrreiz. Abām ierīcēm jābūt pieslēgtām un jāveic datu pārraide.

```python
r = chloros_sdk.light_sensor_reflectance("daq-e-def330", "daq-e-54b774")
print(r["reflectance_type"])                       # "Apparent Reflectance" when both are calibrated
for name, band in r["bands"].items():
    print(name, band["rho"] if band["valid"] else band["reason"])
for idx in r["indices"]:
    print(idx["name"], idx["value"])
```

```python
light_sensor_reflectance(
    ambient_sensor_id, object_sensor_id, *,
    bands=None,          # [{"name", "center_nm", "fwhm_nm", "role"}]; role blue / green / red / nir
    preset=None,         # a preset id from daq_band_presets(); default: the suggested one, else "GENERIC"
    expressions=None,    # custom indices: [{"name": "MyNDVI", "expr": "(NIR_850 - Red_660) / (NIR_850 + Red_660)"}]
    builtins=True,       # include the built-in indices
    backend_url="http://127.0.0.1:5000", timeout=30.0,
) -> dict
```

- **Joslas.** Katras joslas atstarojums ir objekta joslasintegrētā starojuma intensitāte virs vides starojuma. `daq_band_presets(filter_code=None)` uzskaita joslu kopas: pa vienai katram LATTICE M3C filtram (`RGN`, `OCN`, `NGB`, kameras paša joslas modelis, tādējādi DAQ NDVI un kameras NDVI ir viens un tas pats lielums) un `GENERIC` platjoslas. Tās `suggested` ir iepriekš iestatītais, kas atbilst `filter_code`, citādi — pirmās pieslēgtās kameras filtrs (`suggested_from` norāda, kurš).
- **Atgriež** `bands` (katrai joslai `rho`, `valid`, `reason`, `e_ambient`, `e_object`), `indices` (iebūvētais), `custom` (katrs kopā ar `value` vai `valid: False` un `error`), `invalid_bands`, `reflectance_type`, `preset` un abu rādījumu `timestamps` (`ts_ns`). Diapazons, kuru vides sensors nevarēja izmērīt (ārpus tā diapazona, pie trokšņa sliekšņa), tiek atzīts par nederīgu, norādot iemeslu, un visi indeksi, kuriem tas nepieciešams, tiek izlaisti, tos nekad neaprēķinot pret nulli.
- **Šķietamie vai relatīvie.** No mēroga atkarīgie indeksi (SAVI, OSAVI, EVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) prasa absolūtu attiecību, tāpēc tie tiek aprēķināti tikai tad, ja abi sensori ir kalibrēti pēc jaudas (katram ir `calibration_bundle_sha`): `"Apparent Reflectance"`. Pretējā gadījumā rezultāts ir `"Relative Reflectance"`, kurā ir tikai mēroga neatkarīgie indeksi.
- **Divi dažādi viļņu garumu tīkli** tiek salīdzināti pēc to pārklāšanās: vides sensora punkti atrodas objekta diapazonā, un objekta spektrs tiek interpolēts uz tiem.
- **Divi sensori, nekad ne viens.** Ja vienu sensoru nosauc kā abus (ar to pašu identifikatoru, jebkurā rakstībā, ko pieņem datu kopa), rodas kļūda `ChlorosConnectError`: sensora rādījums par sevi pašu ir 1,0 katrā joslā, kas neko nemēra. Arī backend to noraida (`POST /api/daq/indices` atbild ar `400`, `code` `SAME_SENSOR`, ja `ambient_sensor_id` un `object_sensor_id`, ko nosūta SDK, ir viens apvienots sensors, piemēram, tā ID un tā ports).

### Koplietošana ar CLI un citiem skriptiem

Aizmugures DAQ sensoru kopu kopīgi izmanto visi CLI un SDK klienti šajā datorā. Ja sensors jau atrodas kopā — atvērts ar `chloros-cli daq pool-connect` vai citu skriptu —, tad, izsaucot `connect_daq_sensor(port="COM3")` no Python, tiek atgriezts aizņemts rīks: tā `disconnect()` ir bezdarbības operācija, tādējādi jūsu skripts neizrauj sensoru no klienta, kurš to atvēra, izietot no skopas. Tas pats attiecas uz `sensor_id=` (ievadīts ar `:` vai `-` — abas variācijas der), ja DAQ-E, kas nosaukts pēc tā IP, ja tas tika atvērts pēc hostvārda (vai otrādi), kā arī attiecībā uz viedo atklāšanu ar nulles karodziņu, ja backend atbild ar sensoru, kas tam jau pieder — `with` bloks atbrīvo tikai sensoru, ko tas pats ir atvēris.

Chloros GUI **nav** daļa no šī pūla: tā DAQ panelis uztur savu savienojumu ar sensoru, tādēļ `connect_daq_sensor` nekad neaizņemas sensoru, kuru GUI ir atvēris. Nelietojiet vienu un to pašu sensoru vienlaikus gan no GUI, gan no skripta. DAQ-U vai DAQ-M, ko GUI parasti pārvalda, vienkārši neizdosies atvērt, bet DAQ-E pieņem otru sesiju: skripta savienojums maina ierīces datu ieguves iestatījumus GUI vidē, un tā `disconnect()` aptur ierīces datu plūsmu, kas pārtrauc GUI tiešraidi un jebkādu ierakstīšanu, ko tā veic. Vispirms aizveriet sensoru GUI vidē.

### PTP Grandmaster — `time_sync_status()`

Aizmugurējā sistēma darbojas kā PTP grandmaster, no kura LATTICE kameras un DAQ-E ierīces iegūst savus laika zīmogus. Tā sākas pēc pieprasījuma: `connect_camera()`, `connect_array()`, `connect_daq_sensor()` un `connect_all()` to sāk, ja tas nedarbojas — ieskaitot savienojuma izveidi, kas pēc tam neizdodas, piemēram, ja `connect_all()` mēģina izveidot savienojumu ar DAQ-E, kas nav pieejams — to dara arī `test_array_sync()` un katrs izsaukums, kas atver kameras vienai komandai (`capture_cameras_transient()`, `start_multi_camera_stream()`, izlīdzināšanas izsaukumi, `capture_live_index()`).

- **Kur tas paziņo.** Uz vadu tīkla adapteriem, kas savienoti ar kamerām (ieskaitot `169.254.x.x` lokālo saites adresi), citādi — uz noklusējuma maršruta adaptera, ja tas ir vadu tīklā. Nekad ne uz Wi-Fi, ne uz virtuāla adaptera (Tailscale, WSL): aiz tā nav ne kameras, ne DAQ-E. Ja šāds adapteris nav pieejams, tas darbojas, paziņojot par neko (`health` `NO_SOCKETS`), un piesaista adapteri, tiklīdz tiek izveidots savienojums ar nākamo kameru vai DAQ.
- **Pēc savienojuma pārtraukšanas.** Kad kameras adapteris zaudē savienojumu un to atgūst (kabeļa izvilkšana, komutatora nomaiņa, USB adaptera atkārtota numurēšana), galvenais serveris pats atjauno savus savienojumus bez pārstartēšanas, un atkārtoti pārbauda katru pieslēgto DAQ-E, atkārtoti aktivizējot to, kas palicis neaktīvs. Tas veic atjaunošanu arī tad, ja visas kameras un DAQ-E, kuras tas apkalpoja, 15 sekundes paliek neaktīvas.
- **Vai tas sasniedz vadu?** Lasiet `time_sync_status()` (`GET /api/time-sync/status`). `state` norāda tikai to, ka galvenā procesa pavediens darbojas; `health` norāda, vai tā paketes tiek nosūtītas: `OK` (kļūdas nav novērotas), `STOPPED`, `NO_SOCKETS`, `SENDS_FAILING`, `DEGRADED` (daži adapteri nedarbojas) vai `PEERS_SILENT` (kameras un DAQ-E, kurus tas apkalpoja, vairs neatbild), ar teikumu `health_detail`. `announce_sent` / `sync_sent` / `delay_resps_sent` uzskaita tikai tos ziņojumus, kas tika nosūtīti; `announce_send_errors`, `sync_send_errors`, `follow_up_send_errors`, `delay_resp_send_errors`, `last_send_error`, `failing_interfaces` un `last_delay_req_age_s` apraksta tos, kas netika nosūtīti, bet `rebinds` / `last_rebind` — atkārtotos mēģinājumus. `sync_sends_slow` uzskaita sinhronizācijas, kuru nosūtīšana ilga ilgāk par 200 us, un `sync_send_max_us` ir ilgākā; katras Follow_Up izcelsmes laiks nosaka ne vairāk kā 200 us no tās nosūtīšanas. Grandmaster darbojas savā atsevišķā procesā (`own_process`, `process_pid`), ar augstu prioritāti Windows vidē (`process_priority`), tādējādi noslogots backend nevar aizkavēt laika zīmogošanu un vienlaikus sinhronizēt visu kameru pulksteņus; ja šo procesu nevar uzsākt, tas darbojas backend vidē, kā iepriekš (`own_process` false).

```python
st = chloros_sdk.time_sync_status()
if st.get("health") not in (None, "OK", "STOPPED"):
    print("PTP:", st["health"], "-", st.get("health_detail"))
    chloros_sdk.restart_time_sync()
```

| Funkcija | Ko tā dara |
| --- | --- |
| `time_sync_status()` | Galvenā servera stāvoklis, darbspēja, pulksteņa identifikators, prioritātes un ziņojumu skaitītāji (sk. iepriekš). `chloros-cli time-sync status`. |
| `time_sync_peers()` | PTP pakļautie serveri, kurus tas uztver (kameras, DAQ-E): `peers`, katrs `port_identity`, `last_seen_ip`, `last_delay_req_age_ms`, `delay_req_count`. `time-sync peers`. |
| `time_sync_cameras()` | Katras pieslēgtās matricas kameras `PtpStatus`, `PtpOffsetFromMaster` un `PtpMeanPathDelay` (kamerai, kuras programmaparatūrā nav PTP mezglu, ir tikai sērijas numurs). `time-sync cameras`. |
| `restart_time_sync()` | Pārtrauc un atkārtoti palaista galvenā kamera; pēc tam tiek atkārtoti pārbaudīts katras apvienotās DAQ-E PTP (`daq_ptp_rechecked`). Izraisa `ChlorosConnectError`, ja tā tas neatgriežas. `time-sync restart`. |
| `set_time_sync_priority(priority1=None, priority2=None)` | Tā PTP prioritātes (0–255, zemākā uzvar „best-master” vēlēšanās) tīklam ar citu PTP galveno mezglu. Nepieciešams, lai tas darbotos. `time-sync set-priority`. |

Katrs no tiem izmanto `backend_url=` un `timeout=` un palaista vietējo backend, kas vēl nedarbojas.

---

## Kameras palīgrīki

Ko `chloros-cli lattice` dara papildus savienošanai un attēlu uzņemšanai, kā vienkāršas funkcijas `chloros_sdk` (modulis `chloros_sdk.camera_utils`). Katrs izsauc aizmugurējo sistēmu, kuru izmanto CLI komanda, ievērojot CLI noteikumus, tādējādi abas darbojas līdzīgi. Tās pieņem kameras **sērijas numuru**, nevis sesiju, un tos pašus `backend_url=` / `timeout=` atslēgvārdus, ko izmanto smart-connect izsaukumi; tiek palaists aizmugurējais serveris, kas nedarbojas, tāpat kā to dara `connect_camera()`. Noraidījuma gadījumā tiek izraisīts `ChlorosConnectError` ar aizmugurējā servera norādīto iemeslu.

```python
import chloros_sdk as c

c.set_camera_color_profile("214701281", profile="natural")
c.set_camera_highlights("214701281", -40)
print(c.camera_firmware_status()["cameras"])
st = c.network_adapters(estimate=True, cameras=4, pixel_format="BayerRG12")
```

| Funkcija | Darbība |
| --- | --- |
| `camera_color_profile(serial)` / `set_camera_color_profile(serial, profile=, custom_cct_k=)` | Ekrāna krāsu profils: `raw`, `linear`, `natural`, `enhanced` vai `custom_temp`, kā arī tā krāsu temperatūra (ierobežota diapazonā 2000–10000 K; tiek nolasīta, ja profils ir `custom_temp`). |
| `camera_color_adjust(serial)` / `set_camera_color_adjust(serial, saturation=, contrast=, reset=False)` | Ekrāna piesātinājums un kontrasts, katrs 0–200 % (100 = nemainīts); `reset=True` abus atgriež uz 100. |
| `camera_highlights(serial)` / `set_camera_highlights(serial, value)` | Samazina ekrāna attēla spilgtākos, baltākos pikseļus: -100..0 (noapaļots uz nulli, pēc tam ierobežots; 0 = izslēgts). |
| `camera_white_balance_once(serial, settle_s=None)` | Vienreizējais baltā balanss: kamera īsu brīdi pārraida datus, konverģē un atgriež `red_ratio` / `blue_ratio` (`ratios_read` „False”, ja to nevar paziņot). Tā saglabā aprēķināto baltā balansu; visi pārējie iestatījumi, ko raksta tās atvērtais fails, tiek atjaunoti tā, kādi tie tika atrasti. |
| `set_camera_white_balance(serial, mode=None, red=None, blue=None)` | `mode="auto"` (nepārtraukts) vai `"off"`; vai fiksēti `red` / `blue` līdzsvara koeficienti, kas vispirms izslēdz automātisko režīmu. Attiecība, ko kamera noraida, ir nosaukta ar `errors`. |
| `camera_presets()` / `apply_camera_preset(serial, preset)` | Kameras iestatījumi, un, piemērojot vienu no tiem: kamera saglabā iestatījumus ar iestatījumu nosaukumiem (atbildē ir `settings`, jo kamera tos nolasa pēc tam, piemēram, kadru ātrumu, ar kādu tā darbojas; `None` laukam, kuru iestatījums nenosauc), un visi pārējie iestatījumi, kurus atvērtā faila rakstīja, tiek atjaunoti tā, kā tie tika atrasti. `"default"` atjauno kameras rūpnīcas noklusējumus (rūpnīcas iestatījumus ar brīvi darbojošos automātisko ekspozīciju, atslēgtu savienojuma caurlaidspējas ierobežojumu un defektu korekciju, saglabājot RGB kameras kalibrēto baltā balansu), kas netiek saglabāti kameras atmiņā: atbildē ir `factory_defaults: True` un visi `settings` lauki. `camera_presets()` gadījumā `default` ir `{factory_defaults: True, description}`. |
| `camera_filter_models()` / `camera_filter_model_info(name)` | Filtru modeļi un viena modeļa detaļas (joslas, spektrālās atbildes kondicionālais skaitlis). Kameras filtra modelis tiek nolasīts no tās modeļa virknes; to neievada manuāli. |
| `camera_firmware_status(serial=None)` | Katras kamerasprogrammatūra: `current_version`, `available_version`, `update_offered`, `needs_minimum_update` un `minimum_firmware`. |
| `update_camera_firmware(serial, force=False)` | Ierakstiet šīs Chloros versijas programmatūru vienā kamerā: rūpnīcas iestatījumu atjaunošana, ierakstīšana, pārstartēšana, Chloros pamatuzstādījumu pārrakstīšana; kameras kalibrācija tiek saglabāta. Vairākas minūtes; kamerai jāpaliek ieslēgtai. `force=True` atkārtoti ieraksta programmatūru esošajā kamerā. |
| `camera_power_probe(serial)` | Kameras barošanas un termiskie mezgli un tās temperatūra. |
| `sleep_camera(serial)` / `wake_camera(serial)` | Kameras zema enerģijas patēriņa gaidstāves režīms (tā paliek miega režīmā, līdz tiek pamodināta: tās savienojuma caurlaidspējas ierobežojums ir ieslēgts (`DeviceLinkThroughputLimitMode` `On`) minimālajā līmenī, enerģijas taupīšanas režīms ir ieslēgts, ja kamerai tāds ir), ar temperatūru pirms un pēc; pamodināšana atjauno savienojumu tādā stāvoklī, kādā tas bija miega režīmā (ierobežojums, režīms un paketes aizkave `GevSCPD`, ko kamera uztur 0 vērtībā, kamēr ierobežojums ir ieslēgts; ieraksts tiek saglabāts šajā datorā, tādējādi to var atrast arī vēlāka aizmugures sistēma, kas to pamodina), vai arī, ja kamera, kuru šis dators nav pārvedis miega režīmā, izskatās kā miega režīmā esoša (tās ierobežojums ir iestatīts uz minimālo līmeni), ar izslēgtu ierobežojumu un `GevSCPD` atstāts 0 pie 80 (rūpnīcas iestatījumi); ierobežojums, kas iestatīts virs minimālā līmeņa, tiek atstāts nemainīgs. Tiek saglabāts tikai enerģijas stāvoklis; visi pārējie iestatījumi, kurus atklātā rakstīšana mainīja, tiek atjaunoti sākotnējā stāvoklī. |
| `calibrate_camera_with_target(serial, filter=None, attempts=30, settings=None)` | Uztver kadrus, līdz redzes laukā parādās MAPIR kalibrēšanas mērķis, un pielāgo kanālu koeficientus no tā paneļiem: `target_id`, `panel_dns`, `coefficients`. |
| `network_adapters(estimate=False, cameras=1, pixel_format="BayerRG8")` | Šī datora GigE Vision Ethernet adapteri (MTU, savienojuma ātrums, jumbo kadri) un jebkuri `issues`; `estimate=True` pievieno kadru ātrumu un joslas platumu, ko iegūtu `cameras` kameras. |
| `fix_network_settings()` | Piemēro GigE Vision adaptera iestatījumus šī datorakameras adapterim. Tas maina adaptera iestatījumus un darbojas tikai tad, ja Chloros tiek palaists kā administrators; pretējā gadījumā tiek parādīts žurnāls ar atrastajiem rezultātiem. |
| `apply_vignette_correction(input_dir, output_dir, lens_model=None)` | Veiciet attēlu vinjetes korekciju mapē, katram attēlam izmantojot savas objektīva līdzenā lauka karti: `processed`, `lens_models` (objektīvs, par kuru katrs fails tika koriģēts), `skipped` (ar pamatojumu). Ja nav `lens_model`, katra faila objektīvs tiek ņemts no tā EXIF modeļa; `lens_model` attiecas uz failiem, kuru modelī nav norādīta kamera, un fails, kura modelis tam ir pretrunā, tiek izlaists. Tikai faili. |
| `read_dls(mac, filter=None, camera_serial=None)` | Viens nolasījums no DLS (Bluetooth lejupvērstā gaismas sensora): spektru, starojuma intensitāti katrā joslā un joslu magnitūdas, izmantojot pašas kameras reakciju, ja ir norādīts `camera_serial`. `discover_daq_sensors(transports=["ble"])` nosaka MAC adresi. |
| `probe_system(pixel_format="BayerRG12", discover=True)` / `system_info()` | Kameru uzņēmējdatora aparatūra un ieteicamā konfigurācija (`report_text`), kā arī sistēmas CPU / GPU / PyTorch izpildes vide. |

- **Dažām kamerām ir nepieciešama atsevišķa kamera.** `camera_white_balance_once()`, `apply_camera_preset()`, `sleep_camera()` / `wake_camera()`, `calibrate_camera_with_target()` un `update_camera_firmware()` atver kameru. Kamēr Chloros to aizņem (savienota lietotnē, ar `connect_camera()` kā masīva elementu vai pašlaik tiek izmantota ar citu komandu, piemēram, masīva pārbaude vai uzņemšana, atverot savas kameras), aizmugures sistēma atgriež atteikumu ar HTTP 409 `CAMERA_HELD` (gan `code`, gan `error_code`), norādot, kā to atbrīvot. Krāsu iestatījumi, izcelšanas, `camera_power_probe()` un nolasījumi darbojas ar pieslēgtu kameru.
- **Krāsa, izcelšanas un balansa iestatījumi ir paredzēti RGB kamerām.** Vienjoslas mono (M3M) kamerā šie iestatītāji neko nemaina un atgriež `{"skipped": True, "reason": ...}`, tāpat kā CLI. Tie ir displeja iestatījumi: tiešraides skats un attēlu uzņemšana displeja līmenī, nekad ne starojums, ne atstarošanās.

---

## Pārejošie masīvi, izlīdzināšanas profili un indeksi

Ko dara `chloros-cli lattice` bez pastāvīga savienojuma, tāpat kā funkcijas `chloros_sdk` (modulis `chloros_sdk.array_utils`): `continuous`, `multi-capture`, `multi-stream`, `multi-test`, `multi-detect`, `analyze-array`, `align-calibrate`, `align-apply`, `align-stream`, `align-info`, `align-reorder` un `index`. Katra no tām nosūta pieprasījumu, ko nosūta CLI komanda, izmantojot CLI noklusējumus.

```python
import chloros_sdk as c

prof = c.calibrate_alignment_profile(["214000535", "214000551", "214000556"],
                                     "D:/rig/alignment_profile.json", method="orb", model="rigid")
print({sn: t["reprojection_error"] for sn, t in prof["transforms"].items()})

c.capture_aligned_multiband("D:/rig/alignment_profile.json", "D:/rig/out")
idx = c.capture_live_index("D:/rig/alignment_profile.json", preset="NDVI",
                           output_dir="D:/rig/ndvi", capture_level="reflectance")

with c.start_multi_camera_stream(["214000535", "214000551"], "D:/rig/stream", fps=5, count=50) as job:
    print(job.wait()["final_stats"])          # waits for the 50 groups, then closes the cameras
```

- **Tās pašas atver kameras.** Katra komanda atver savas kameras, veic savu darbību un tās aizver, tādēļ tai ir nepieciešamas kameras, kuras Chloros neaizņem (kas ir pieslēgtas lietotnē, ar `connect_camera()` vai kā masīva elements); aizmugurējā sistēma atgriež HTTP 409 kļūdu `CAMERA_HELD` un norāda, kā atbrīvot vienu kameru. Pagaidu atvēršana aizņem kādu laiku (apmēram 40 s dažām kamerām). Atkārtotai attēlu uzņemšanai `connect_array()` vienreiz ir ātrākais veids, un tas saglabā visus eksporta veidus, kur īslaicīga uzņemšana saglabā attēlu bez debayeringa. `calibrate_alignment_profile()` ir izņēmums: savienotā šo kameru masīvā tas izmanto masīva paša kadrus (`used_live_array`).
- **`serials`** ir atveramās kameras, no kurām pirmā iedarbina trigeri; `None` atver visas tīklā esošās kameras.
- **`settings`** ir vārdnīca ar kameru iestatījumu pārrakstījumiem — laukiem, kurus nosaka CLI karodziņi: `exposure_time` (µs), `gain` (dB), `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `frame_rate`, `gev_packet_size` un `preset` (kameras iestatījums pēc nosaukuma). Norādītā ekspozīcija vai pastiprinājums, tāpat kā karodziņi, izslēdz šo automātisko kontroli.
- **`gpu`**: `True` piespiež izmantot GPU deformāciju, `False` — CPU (`--gpu` / `--no-gpu`); `None` ļauj backendam izvēlēties.
- **`keep_settings`**/**`clear_settings`** (visas funkcijas, kas atver kameras, noklusējums ir `False`): beigās katra kamera tiek atjaunota tādā stāvoklī, kādā tā tika atrasta; `keep_settings=True` tā vietā saglabā darbības iestatījumus pašas kameras atmiņā (brīvā darbība), `clear_settings=True` atjauno šīs atmiņas rūpnīcas iestatījumus — skatiet [Kameras atmiņa](#camera-memory--keep_settings--clear_settings). Atbilde (uz uzdevumu, `stop()`) satur `camera_memory`.
- **Celiņi ir backend**: relatīvie ceļi tiek pārvērsti par absolūtiem attiecībā pret jūsu darba direktoriju, ja aizmugurējā sistēma atrodas šajā datorā. Alignement-profile faila funkcijas (`alignment_profile_info()`, `reorder_alignment_profile()`) nolasa failu šajā datorā, tāpat kā to dara CLI.

| Funkcija | Darbība |
| --- | --- |
| `start_camera_continuous(serial, output_dir="output", *, format="tiff", jpeg_quality=95, queue_depth=64, settings=None)` | Nepārtraukti ieraksta vienu kameru, līdz tiek apturēta. Atgriež `CliJob`. `tiff` izpildes kadri ir `raw` fails — masīvs, kurā tiek ierakstīti šīs kameras attēli (viena mozaīkas plakne ar sensorapaša vērtībām, nesaspiestas); `png` un `jpg` satur 8 bitus, tādēļ šie cikli ir demosaicēti kadri skatīšanai. Saistīts vai apvienots DAQ ieraksta vienu `.daq` failu par katru kadru cikla`daq/` mapē (`daq_files` tās statistikā). Katrs kadrs tiek ierakstīts vienu reizi ar pilnīgiem metadatiem (modelis, uzņemšanas laiks, objektīvs, spektrālās joslas, kalibrēšana uz neapstrādāta kadra); `stop()` atgriežas, kad katrs jau uzņemtais kadrs ir ierakstīts (līdz 25 s), un tikai tad kamera tiek izslēgta. Kamera, kas palikusi gatavībā aparatūras trigerim (`TriggerMode` `On`) uz laiku tiek iestatīta brīvā režīmā (sākuma atbildē `trigger` ir norādīts, kas tika atrasts). Pēc apstāšanās kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta — ar tās sākotnējiem izraisītāja iestatījumiem un visiem iestatījumiem, kurus mainīja šis cikls —, ja vien netiek izmantots `keep_settings=True`, kas saglabā cikla iestatījumus kameras pašas atmiņā (brīvā režīmā), vai `clear_settings=True`, kas atiestata šo atmiņu. Darbības cikls, kurā netiek ierakstīts neviens kadrs, beidzas pēc 10 sekundēm ar `stop_reason` un `no_frames`, un iemesls ir norādīts `last_error`, un tās `stop()` atgriež šo iemeslu kā `error`. Kadri, kas tika izlaisti, jo ierakstītāji nespēja tikt līdzi, tiek uzskaitīti, un `drop_reason` norāda iemeslu. Attiecībā uz Chlorosa kamerai, izmantojiet `CameraSession.capture_repeated()`. `lattice continuous`. |
| `start_multi_camera_stream(serials=None, output_dir="output", *, fps=3.0, count=None, format="tiff", jpeg_quality=95, settings=None)` | Sinhronizē plūsmas kadru grupas ar `fps`, `count` grupām vai līdz apturēšanai. Atgriež `CliJob` (tā `start_reply` satur `n_cams` un vadu `pacing`); tās galīgajās statistikās tiek uzskaitītas iegūtās, ierakstītās un izlaistās grupas, faili, kurus rakstītājs nevarēja ierakstīt, un grupas, par kurām kamera neko nepiegādāja. `lattice multi-stream`. |
| `capture_cameras_transient(serials=None, output_dir="output", *, format="tiff", jpeg_quality=95, settings=None)` | Viena sinhronizēta uzņemšana, viens attēls ar debayeringu no katras kameras: `results` (`filepath`, `serial`, `frame_id`) un `pacing`. Parādās brīdinājums par grupu, kurā trūkst kameras, norādot, cik faili tika saglabāti vispirms. `lattice multi-capture` bez pieslēgta masīva. |
| `test_array_sync(serials=None, count=10, ptp_settle_s=None, timeout=None)` | `count` sinhronizēti uzņēmumi ar fiksētu ekspozīciju: `sync_groups`, `master`, viena `group_timestamps` rinda uz katru izraisītāju (kamera, kurai izlaists viens kadrs, nav iekļauta savā rindā) un `sync`: katras pārējās kameras nobīde no galvenās kameras, katram izraisījumam atsevišķi (`offsets_us`: katrai kamerai `mean_us`, `min_us`, `max_us`, `jitter_us`, `groups`), vienā takts ciklā (`clock_domain`: `ptp` vai `device` — PTP izslēgts, katrai kamerai savs darbības laika pulkstenis, tādējādi nobīde ir brīdis, kad tā tika palaista, un sinhronizāciju mēra tikai svārstības). Trigeris, kura laika zīmogi apvieno ierīces pulksteni un PTP (PTP izslēgts vai joprojām sinhronizējas), tiek izlaists un uzskaitīts (`groups_mixed_domain`), tāpat kā gadījums, kad PTP pulkstenis joprojām atšķiras par vairāk nekā `agree_within_us` no galvenā (`groups_clocks_disagree`); to norāda `notes`. `clock_offset_mean_us` / `clock_offset_jitter_us` ir lielākais vidējais rādītājs un svārstības katrai kamerai. `success` ir „false”, tāpat kā `error`, ja ir izlaists trigers, ja mazāk nekā divi trigeri visām kamerām ir vienā takts signālā vai ja takts signāli nekad nesakrīt (`sync.problems`); rindas tiek atgrieztas jebkurā gadījumā. Tā sāk PTP galveno pulksteni, ja tas nedarbojas (`time_sync` norāda uz `grandmaster_running` un `grandmaster_started_by_this_test`), ieslēdz katras kameras PTP (kā to dara `connect_array()`) un pirms pirmā trigera gaida, līdz katras kameras PTP pulkstenis sinhronizējas ar galveno pulksteni un nostabilizējas: `PtpStatus` „Slave” stāvoklī vienam galvenajam pulkstenim, atrodoties 100 us attālumā no tā un stabili 3 s, un katra kamera sinhronizēta šādā veidā, bez pārtraukuma, 120 s — apmēram 2 minūtes pēc ieslēgšanas. Pulkstenis, kas tikko ir sinhronizējies, apmēram 2 minūtes pēc tam, kad tas nolasīts kā „Slave”, atšķiras līdz pat ~200 us, un pašas kameras `PtpOffsetFromMaster` to neparāda; 120 s laikā kameras sasniedz ~10 us no stabilizētā pulksteņa. Kamera, kas joprojām ir sinhronizēta kopš iepriekšējā testa, kurā PTP tika atstāts ieslēgts, kā to redzēja šis backend, skaita no šīs sinhronizācijas, tādējādi, ja testi notiek viens pēc otra, atkārtota gaidīšana nav nepieciešama; kamera, kuru tests ieslēdz, vai kamera, kas atrasta ar ieslēgtu PTP, bet kuras sinhronizāciju šis backend vēl nav redzējis (ieslēgta citur vai pirms backenda pārstartēšanas), gaida savas 120 s, un abu kameru masīvā tās, kas tikko sinhronizējušās, joprojām gaida savu laiku. Kamera, kas iziet no „Slave” režīma, pārsniedz 100 us vai seko citam „grandmaster” 3 nolasījumus pēc kārtas (1,5 s) gaidīšanas laikā, sāk skaitīšanu no jauna; viens vai divi šādi mērījumi tiek reģistrēti (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`), un skaitīšana turpinās, savukārt grandmaster identifikators, kuru nevar nolasīt, tiek uzskatīts par nezināmu, nevis par izmaiņu. Ja visu kameru skaitīšana atsākas vienlaikus (vienas sekundes laikā), tas liecina par „grandmaster” laika izmaiņām, nevis kameru laika izmaiņām: backend’žurnālā ir norādīts `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` to uzskaita kā `grandmaster_steps`, un gaidīšanas laika beigām seko kļūda ar kodu `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Šim gadījumam robeža netiek pagarināta. `ptp_settle_s` (0–600; `0`, ja nav gaidīšanas laika) nosaka gaidīšanas laiku izpildei; cita vērtība tiek noraidīta (HTTP 400). Gaidīšanas laiks ir ierobežots līdz norēķinu laikam plus 90 s (pēc noklusējuma 210 s, vismaz 90 s), un izsaukums gaida aizmugurējo sistēmu `timeout` s: pēc noklusējuma 240 plus norēķinu laiks (360 s), tāpat kā to dara `chloros-mcp` un `test_array_sync`. `ptp_lock` ir `converged`, `waited_s`, `timeout_s`, `settle_s`, `locked_s` (cik ilgi katra kamera ir bijusi fiksēta un stabila), `found_settled` (kameras, kas jau bija fiksētas uz stabilizācijas laiku, kad tās tika atrastas) un, katrai kamerai atsevišķi, `status`, `offsets_ns`, `slave_s` (cik ilgi tā ir bijusi sinhronizēta; „None”: nav sinhronizēta) un `remaining_s` („None”, ja nevienas kameras PTP nav ieslēdzies). Aizmugurējās sistēmas žurnālā ik pēc 5 s ir rinda ar katras kameras stāvokli, cik ilgi tā ir bijusi sinhronizēta un cik ilgi vēl palicis. Katra kamera ieslēdzas ar izslēgtu PTP; pēc tam pulksteņi sinhronizējas apmēram 10 sekundēs. Pēc tam katra kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta testa laikā — tās PTP, automātiskā ekspozīcija un pastiprinājums testa laikā tiek turēti izslēgti (`ExposureAuto` / `GainAuto` un manuālais režīms `ExposureTime` / `Gain`), un pārējās, ja vien nav `keep_settings=True` — tātad kamera, kurai PTP ir ieslēgts (`PtpEnable=True` vai darba cikls ar `keep_settings=True`) paliek bloķēta, un, tiklīdz šis backend ir redzējis, ka tā ir bloķēta uz stabilizēšanās laiku, vēlāks tests to negaida. Ja pulksteņi nestabilizējas, `success` ir nepatiesa pirms jebkura triggera, un `error` nosaka, ko darīt: kamera, kas nekad nav fiksējusies (`not_slave`), nozīmē, ka galvenais serveris to nesasniedz (`time_sync_status()`); kamera, kas pastāvīgi pārtrauca savienojumu, tiek nosaukta pēc laika, cik ilgi tā bija fiksējusies (`remaining_s`); pulksteņi joprojām saskaņojas (`far`): pārbaudiet `time_sync_status()`, un, lai tie būtu sinhronizēti pirms testa, ieslēdziet PTP un atstājiet to ieslēgtu (`PtpEnable=True` katrā kamerā vai `keep_settings=True`; tests atjauno atrasto stāvokli, tādēļ tas paliek ieslēgts), tad palaidiet to. `lattice multi-test`. |
| `detect_array_master(line=None)` | Kameras sinhronizācijas līnijā un noklusējuma galvenā kamera: `master_serial`, `slave_serials`, `line` tika pārbaudītas. `lattice multi-detect`. |
| `analyze_array_models(camera_models=None, *, width=2048, height=1536, n_active=None, force_tier=None, binning=1, packed="auto", pixel_format=None)` | Kāds šo kameru modeļu klāsts (produktu nosaukumi, `"LATT-M3C-L87-FOCN"`) varētu parādīties šī datora tīklā: `tier`, `achievable_fps_max`, vadu budžets (`max_cams_collision_safe`, `oversubscribed`), `cam_caps` un `fix_suggestions`. Pēc noklusējuma tiek parādīti atrasto kameru modeļi; nav atvērta neviena kamera. Lai uzzinātu, kādas kameras jums ir, kā arī to iestatījumus, kurus izmantotu savienojuma izveidei, skatiet [`analyze_array_network()`](#network-analysis-helper). `lattice analyze-array`. |
| `calibrate_alignment_profile(serials=None, save_path="alignment_profile.json", *, frames=3, method="orb", model="rigid", reference_serial=None, checkerboard=None, control_points=None, preview=False, vignette=False, roi=None, mask_path=None, rms_threshold_px=None, per_cam_overrides=None, settings=None, ...)` | Izveidojiet saskaņošanas profilu, izmantojot reāllaika kadrus, un saglabājiet to. `method`: `orb`, `akaze`, `phase`, `checkerboard` (kopā ar `checkerboard="9x6"` — iekšējie stūri) vai `manual` (kopā ar `control_points={serial: [[x, y], ...]}` — arī atsauces kameras). `model`: `translation`, `rigid`, `affine` vai `homography`. Sīkākas opcijas ir CLI karodziņi: `max_features`, `ratio_threshold`, `matcher` (`bf` / `flann`), `knn_k`, `prefilter` (`none` / `gradient` / `clahe` / `blur` / `hist_match`), `ransac_threshold_px`, `ransac_max_iters`, `ransac_confidence`, `averaging`, `lock_rotation`, `lock_scale`, `lock_axis`; `per_cam_overrides` ir `{serial: {option: value}}`. `rms_threshold_px` atsakās saglabāt sliktāku risinājumu. Atgriež `save_path`, `output_width` / `output_height`, `crop_roi`, `reference_serial`, `transforms` (katrai kamerai `reprojection_error`, `num_matches`) un `preview_path`. `lattice align-calibrate`. |
| `capture_aligned_multiband(profile_path, output_dir="output", *, serials=None, format="tiff", bit_depth=None, gpu=None, no_crop=False, bands=None, order=None, vignette=False, per_band=False, per_camera=False, interpolation="linear", border_mode="constant", border_value=0, settings=None)` | Viens saskaņots daudzjoslu attēls visā profilā: `files` (daudzjoslu TIFF-fails ar `bands` un izmēru, plus viens attēls katrai joslai ar `per_band` un viens attēls katrai kamerai ar `per_camera`) un `vignette_corrected`. `bands` atlasa un `order` sakārto joslas pēc nosaukuma (saraksts vai ar komatu atdalīta virkne). `lattice align-apply`. |
| `start_aligned_stream(profile_path, output_dir="output", *, serials=None, fps=3.0, count=None, ...)` | Plūsmai pielāgoti daudzjoslu attēli (tādas pašas deformācijas opcijas kā `capture_aligned_multiband()`), līdz `count` vai līdz apstādināšanai. Atgriež `CliJob`; tā galīgie statistikas rādītāji ir `frames_written` un jebkurš `frame_groups_incomplete`. `lattice align-stream`. |
| `alignment_profile_info(path)` | Profila fails, kas apkopots kā `align-info`, to izdrukā: `name`, `created`, `method`, `reference_serial`, `sensor_resolution`, `output_width` / `output_height`, `crop_roi`, `camera_serials`, `transforms` (katrai kamerai `model`, `reprojection_error`, `num_matches`, `reference`), `camera_models`, `layer_order` (katrai kamerai `band_names`, `wavelengths_nm`, `enabled`); `profile` ir viss fails. |
| `reorder_alignment_profile(path, order=None, enable=None, disable=None)` | Mainīt profila failu uz vietas: `order` ir joslu nosaukumi (katrs pārvieto savas kameras slāni uz augšu šajā secībā), `enable` / `disable` ir sērijas numuri. Atgriež jauno `layer_order`. `lattice align-reorder`. |
| `index_catalog()` | indeksa iestatījumi (`presets`: katrs `name`, `channels`, `formula`) un krāsa `gradients`. `lattice index --list-presets` / `--list-gradients`. |
| `compute_index(input_path, *, preset=None, formula=None, channel_map=None, output=None, capture_level="unknown", output_format="all", colorize=False, gradient="RdYlGn", vmin=None, vmax=None, percentile=None, bg_mode="clip")` | Indekss virs saskaņota daudzjoslu TIFF faila: `preset` (`NDVI`, `NDRE`, ...) vai `formula` pār simboliem, kurus `channel_map` attēlo kā joslas (`{"NIR": "NIR_850", "Red": 0}`, pēc nosaukuma vai indeksa). Iepriekš iestatītaissimbols `channel_map` izlaiž un iekļauj vienu faila joslu, kuras nosaukums sākas ar to (`red` -> `Red_660`, `nir` → `NIR_850_RGN`), kuru nosauc pats fails (tā joslu saraksts vai Chloros uzņemuma / produkta `Chloros:Band{R,G,B}Name`), tāpēc NDVI rādītājam uz Chloros atstarojuma uzņēmumā nav nepieciešams `channel_map`; divas joslas, kas atbilst vienam simbolam, tiek noraidītas, nevis pieņemtas kā iespējamās. `capture_level` norāda, ko šīs joslas ietver: `reflectance` vai `radiance` padara vērtības kvantitatīvas; nekalibrēts slānis (`align-apply`) sniedz kvalitatīvas vērtības; `"unknown"` to nolasa no faila, kurā ir iekļauts Chloros faila `Chloros:ProcessingLevel`. Atgriež `stats` (forma, 1.–99. percentiles diapazons, LUT diapazons, izmantotais `capture_level`) un `files`, kas ierakstīts blakus ievadei (`<input stem>_<preset>.tif`, `..._LUT.png`) vai pie `output`, ceļš: ja failiem ir attēla paplašinājums, tie tiek nosaukti pēc to saknes (`output="D:/out/ndvi_FRGN.png"` raksta `ndvi_FRGN.tif` un `ndvi_FRGN_LUT.png`), bez tā tiek izmantots prefikss (`D:/out/ndvi` → `ndvi.tif`, `ndvi_LUT.png`); ceļus nolasiet no `files`. Tie satur ieejas kameras datus (ražotājs / modelis / sērijas numurs, GPS, objektīvs, uzņemšanas laiks pēc UTC, kalibrēšanas identifikators, bet nekad ne tās raw-DN kalibrēšanu), `Chloros:ProcessingLevel=index`, un indeksu kā vienu joslu (`XMP-Camera:BandName`, TIFF un LUT PNG). Tikai faili; bez kameras. `lattice index --input`. |
| `capture_live_index(profile_path, *, preset=None, formula=None, channel_map=None, serials=None, output_dir="output", output_prefix="index", capture_level="reflectance", save_multiband=False, ...)` | Uzņemšana, kalibrēšana (`reflectance` vai `radiance`; `raw` to izlaiž), saskaņo un aprēķina indeksu vienā solī; `save_multiband=True` saglabā kalibrēto kopu, kuru `compute_index()` var izmantot vēlāk. Atgriež `stats` (kopā ar `serials`, kura joslas tika izmantotas) un `files`. Tā atver `serials` (pirmais iedarbina trigeri), citādi profila kameras, kuru `layer_order` ieraksts ir ieslēgts, vispirms atsauces (tiek atvērta pat tad, ja tās joslas ir atspējotas); kamera, kuras joslas ir atspējotas, nekad netiek kalibrēta. `channel_map` ir nepieciešams tikai simbolam, uz kuru kaudze nevar atbildēt, kā `compute_index()`: iestatījuma simbols izmanto vienu aktivizētu joslu, kuras nosaukums sākas ar to, un to pārbauda pirms jebkuras kameras atvēršanas (ja neviena vai vairākas atbild, tās tiek noraidītas, nosaucot tās); katram simbolam ir nepieciešams pielāgots `formula`. `index.tif` un LUT satur steka kameru, uzņemšanas laiku (atskaites kameras ekspozīciju, UTC) un objektīvu, un abi nosauc indeksu kā savu vienīgo joslu (`XMP-Camera:BandName`, piemēram, `NDVI`: krāsu PNG failā tiek norādīts indekss, nevis tā krāsu kanāli). `lattice index --live`. |

### `CliJob`

Rokturis, ko atgriež trīs straumes. Darbs noris aizmugurē, un tam pieder kameras, līdz tas tiek apturēts.

| Elementa nosaukums | Apraksts |
| --- | --- |
| `stats(timeout=10.0)` | `running`, `state`, `stats` (saglabātie / ierakstīti, izmesti, `fps`, `elapsed`, ierakstīšanas kļūdas, `stop_reason`; nepārtraukta uzdevuma gadījumā arī `frames_grabbed` un `frames_queued` — katrs noķertais kadrs tiek ierakstīts, ievietots rindā, izmests vai radās kļūda — un `drop_reason`, `writers` un `write_ms`, vidējais laiks, kas bija nepieciešams viena kadra ierakstīšanai) un `output_dir`. `state` ir `running`, `ended` (tas ir apstājies pats un vēl nav apturēts), `stopping` (apstādinātais process izraksta savu rindu: reāllaika statistika) un `stopped_by`) vai `finished` (tā galīgā statistika, apstāšanās `error` un `stopped_by`). Pārtraukta uzdevuma ieraksts tiek saglabāts 10 minūtes (ne vairāk kā pēdējās 32), katram klientam: cits skripts vai `chloros-mcp` nolasa to pašu stāvokli un galīgās statistikas datus. Uzdevums `chloros-cli` tika uzsākts (`lattice continuous`, `multi-stream`, `align-stream`) tiek apturēts ar backend, kad CLI 30 sekundes neveic pārbaudi — tiek izbeigts, tā mašīna ir miega režīmā — ar `stop_reason`, `client_gone` un `stopped_by` backend; tās statistikas nolasīšana no šejienes to neuztur darbībā. No SDK uzsākts uzdevums darbojas, līdz tas tiek apturēts. Katrs uzsākums nosūta savu `timeout` kā backend termiņu (`client_deadline_s`): kamera, kas atveras pēc tam (viena vēl sāk darboties), tiek atgriezta un aizvērta, un netiek uzsākts neviens uzdevums, un izsaukums izraisa `ChlorosConnectError` (`CLIENT_DEADLINE_PASSED`). Kamera, ko aizņem uzdevums, tiek noraidīta visiem pārējiem atvērtajiem (`connect_camera()`, citam sākumam, īslaicīgai uzņemšanai) ar HTTP 409 kļūdu `CAMERA_HELD`, norādot uzdevumu un to, kurš to sācis. |
| `stop(timeout=30.0)` | Pārtrauc to un aizver tās kameras: `final_stats`, `output_dir` un `error`, ja apturēšana nenotika bez problēmām. Otrais izsaukums atgriež to pašu atbildi, tāpat kā apturēšana no cita klienta: ja tā tiek nosūtīta, kamēr pirmais vēl raksta savu rindu, tā gaida (līdz 25 s; pēc tam tiek izraisīta kļūda, un galīgās statistikas parādās `stats()`). |
| `wait(timeout=None, poll_s=0.5)` | Gaidiet, līdz tas beidzas pats no sevis (sasniegts `count`, pilns disks, kļūda), tad `stop()` to, lai savāktu galīgos skaitļus. `timeout` izraisa `TimeoutError`, kamēr tas joprojām darbojas; uzdevums bez `count` beidzas tikai tad, kad to aptur. |
| `job_id`, `kind`, `output_dir`, `start_reply`, `stopped` | Tās identifikators un veids (`continuous`, `multi-stream`, `align-stream`), kur tas raksta, sākotnējā maršruta atbilde un vai `stop()` ir palaists. |

Izmantojiet to kā konteksta pārvaldnieku, lai to apturētu, izkāpjot no darbības jomas.

---

## Projekta mape un receptes

**Projekta mape** ir mape, kurā tiek izveidoti jauni projekti, un to kopīgi izmanto lietotne Chloros, CLI un SDK (modulis `chloros_sdk.system_utils`; `chloros-cli project-folder`):

```python
print(chloros_sdk.project_folder())                # the configured folder, else the default
chloros_sdk.set_project_folder("D:/Flights")       # created if missing
chloros_sdk.reset_project_folder()                 # -> {"path": <default>, "was_set": True}
```

Ja konfigurētā mape vairs nepastāv, tiek izmantota noklusējuma mape (`Documents/MAPIR/Chloros Projects`), tāpat kā lietotnē. Relatīvais ceļš, kas norādīts `set_project_folder()`, tiek ņemts no jūsu darba direktorija, ja aizmugurējā sistēma atrodas šajā datorā.

**Recepte** ir YAML/JSON darbību saraksts, ko `chloros-cli project run PATH RECIPE` izpilda attiecībā uz saglabātu projektu. `run_project_recipe()` to izpilda, izmantojot CLIpaša izpildītāju (kas instalēts kopā ar SDK), tādējādi viena recepte darbojas vienādi abos gadījumos:

```python
r = chloros_sdk.run_project_recipe("D:/campaign/morning.yaml", "D:/Projects/Field_A")
print(r["ok"], r["actions"])
print(r["log"])                                    # what the CLI would have printed

chloros_sdk.run_project_recipe({"actions": [{"apply": {"name": "FrontLeft", "settings": {"exposure_us": 5000}}},
                                            {"wait": "2s"},
                                            {"capture": {"name": "FrontLeft", "output": "pose_a/", "format": "tiff"}}]},
                               "D:/Projects/Field_A")
```

```python
run_project_recipe(recipe, project_path, *, dry_run=False, echo=False,
                   backend_url="http://127.0.0.1:5000") -> dict
```

- **`recipe`** ir `.yaml` / `.yml` / `.json` failu vai pašu recepti: vārdnīca ar `actions` (`apply`, `capture`, `burst`, `stream`, `sensor`, `wait`), kā arī pēc izvēles `overrides` un `stop_on_error` (noklusējuma vērtība ir „true”: izpilde tiek pārtraukta pie pirmās darbības, kas neizdodas). Darbības ir norādītas CLI atsauces [Recipe DSL](cli-reference.md#recipe-dsl); relatīvais `output` tiek atrisināts, ņemot vērā jūsu skripta darba direktoriju.
- **Atgriež** `ok` (visas darbības izpildītas veiksmīgi), `actions` (cik daudz), `log` (izpildītāja izvade; `echo=True` to izdrukā arī izpildes laikā) un `validation`. Ja recepte trūkst vai to nevar analizēt, tiek izraisīts `ChlorosConnectError`.
- **Pārbaudīts pirms izpildes.**`validation` ir `{"ok", "errors", "warnings", "steps"}`: katrai darbībai kā atslēgai jābūt tieši vienam zināmam darbības vārdam (`{"capture": {"name": ...}}`, nevis `{"action": "capture"}` vai `{"type": "capture"}`), kas nosauc projektā esošu kameru, masīvu vai sensoru pareizā veidā. Receptes ar kļūdu**neizpilda** nevienu darbību (`ok` false, kļūdas `log`), neatkarīgi no tā, ko norāda `stop_on_error`. Neizmantotās atslēgas, kā arī visi masīvi vai gaismas sensori, kurus recepte vada, bet kuri pašlaik nav pieslēgti, ir `warnings`. Recepte nepieslēdz ierīces: pieslēdziet projekta pirmo (`ChlorosProject.connect_all()`, `chloros-cli project connect`, vai MCP rīku `connect_project_devices`). `dry_run=True` neko nepalaiž un atgriež `{"recipe": ..., "validation": ...}`.
- Vienā procesā vienlaikus darbojas tikai viena recepte. YAML receptei ir nepieciešams PyYAML.

---

## Projekta automatizācija — `ChlorosProject`

Saglabāts Chloros projekts ir mape, kas satur `cameras.json` + `sensors.json` + `project.json`. `open_project(path, relink=None)` ielādē manifestu (`relink`: mape, kurā tagad atrodas pārvietotā projekta trūkstošie avota faili, ko izmanto, kad to ielādē aizmugurējā sistēma — `process()` — skatīt **Pārvietots projekts** sadaļā `ChlorosLocal`), un `connect_all` pieslēdz tiešsaistē katru saglabāto kameru, matrici un gaismas sensoru ar tā saglabātajiem iestatījumiem — tādu pašu aparatūras stāvokli, kādu radītu grafiskā lietotāja saskarne. Gaismas sensori savienojas caur aizmugurējās sistēmas [DAQ sensoru pūlu](#daq-sensor-sessions), tāpēc tiem ir nepieciešams darbojošs backend (skatīt [`SensorHandle`](#sensorhandle)).

### Vienkāršs piemērs

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Light sensors are addressable by name OR slot_id
reading = proj.sensors["Sky"].read()   # (spectrum, is_saturated, ...) or None

# One frame from every camera and array, one spectrum from every sensor
proj.capture_all("./out")

proj.disconnect_all()
```

Vai arī kā konteksta pārvaldnieks:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` metodes

| Metode | Apraksts |
| --- | --- |
| `connect_all(*, cameras=True, arrays=True, sensors=True, verbose=False, align=None, packed=None)` | Atrod un savieno visas saglabātās kameras, masīvus un gaismas sensorus. Atgriež savienojumu pārskatu pa klasēm (`{"cameras": {...}, "arrays": {...}, "sensors": {...}}`, katram no tiem — `connected`, `missing` un `errors`; `arrays` ietver arī `alignment`). Ja aizmugurē darbojas serveris, kas klausās uz `127.0.0.1:5000`, saglabātās kameras un masīvi tiek atrasti un savienoti caur to. Ja tāda nav, šis process tos atrod un vadī pats caur `lattice_sdk`, kam nepieciešama „Arena“ SDK darblaika vide un OpenCV (`cv2`) — SDK pakotne neinstalē ne vienu, ne otru; ja tas nav iespējams, katra saglabātā kamera un matrica tiek uzskaitīta zem `errors` ar iemeslu, un `missing` paliek tukšs. Aizmugurējais modulis, kas darbojas, bet noraida SDK (izslēgts: HTTP 401; plāns zem Copper: 403), nav uzskatāms par „nav aizmugurējā moduļa”: katra saglabātā kamera, masīvs un gaismas sensors tiek uzskaitīts zem `errors` ar aizmugurējā moduļa iemeslu, un šajā procesā nekas netiek atvērts. Katrs izsaukums atkārtoti pārbauda backend, tāpēc `connect_all()` pēc ieejas sistēmā vai pēc Chloros palaišanas izveido savienojumu ar to pašu projekta objektu. Saglabāts masīvs, kas jau atrodas backendā ar tām pašām kamerām (GUI vai `chloros-cli project connect`), tiek pievienots tāds, kāds tas ir, nepielietojot atkārtoti tā saglabātos iestatījumus, un `disconnect_all()` ļauj tam turpināt darboties. Gaismas sensori vienmēr darbojas caur backend (sk. [`SensorHandle`](#sensorhandle)). Tas nekad neizveido jaunu backend. |
| `disconnect_all()` | Viss tiek atvienots. Vispirms tiek atbrīvota katra ierīce; pēc tam, ja backend nav apstiprinājis kameras, masīva vai gaismas sensora atbrīvošanu (atvienošanas laika limits ir beidzies vai atvienošana tika noraidīta), katram tiek piešķirts viens `ChlorosConnectError` nosaukums. Ierīce, kas jau ir atbrīvota, vai ja nedarbojas backend, ir klusa. Masīvs vai gaismas sensors, ko cits klients bija atvēris, paliek darbojoties. Projekta `with` iziešana nekad netiek izraisīta. |
| `capture_all(output_dir=".")` | Viens kadrs no katras pieslēgtās kameras + masīva, un viens spektrs (`SensorHandle.read()`) no katra pieslēgtā gaismas sensora, kas tiek uzņemti secīgi (vispirms kameras, tad masīvi, tad sensori), nevis vienā mirklī. |
| `stream(camera, overlays=False, fps=10.0)` | Ģenerators, kas ģenerē BGR `numpy` kadrus no nosauktas kameras (vai matricas). `overlays=False` nolasa kadrus ar rokturi`grab()` `debayered` līmenī: caur aizmuguri, ja projekts ir savienots ar to, citādi — tieša `lattice_sdk` satveršanas cilpa (masīvi ģenerē `{serial: frame}` vārdnīcas). `overlays=True` maršrutē caur `ChlorosLocal.camera_stream()` → aizmugurējās sistēmas `/api/camera/<serial>/stream-annotated` MJPEG plūsmu, ar kameras saglabāto `ui.overlay` bloku, kas tiek nodots kā vaicājuma parametri. Nepieciešams backend režīms, OpenCV (`cv2`) un **autonomā kamera**: tiešā režīma kamera izraisa `RuntimeError` (backend nevar iegūt kameru, kas pieder šim procesam), un masīvs izraisa `NotImplementedError` (uzliek kompozīciju katrai kamerai — straumē elementu pēc nosaukuma). Vienreizējais ekvivalents: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Veic izlīdzināšanu katram pašlaik pieslēgtajam masīvam. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Izpildīt kalibrēšanas/indeksēšanas procesu projekta attēliem (ietver `ChlorosLocal.process`; šie četri ir **vienīgie** pieņemamie argumenti — `indices=` utt. izraisa `TypeError`; indeksus iestata ar `ChlorosLocal.configure()`). Lēnprātīgi veido `ChlorosLocal()`, kas automātiski palaista aizmugurējo moduli. |

Atribūti:
- `proj.cameras` — `Dict[str, CameraHandle]`, indeksēts pēc nosaukuma UN sērijas numura.
- `proj.arrays` — `Dict[str, ArrayHandle]`, kuras atslēgas ir nosaukums UN array_id.
- `proj.sensors` — `Dict[str, SensorHandle]`, kuras atslēgas ir nosaukums UN slot_id.
- `proj.config` — `project.json["config"]` vārdnīca.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**Apstrādes līmeņi.** `capture()`, `grab()` un `frame_stream()` visiem ir viens un tas pats `processing`
tokenu, un ķēde ir kumulatīva — katrs līmenis izpilda visu, kas atrodas virs tā:

| Līmenis | Izeja | Piezīmes |
| --- | --- | --- |
| `raw` | 1 kanāla Bayer, sensora nativais | Bez demosaic. Šajā līmenī pārklājumi nav pieejami. |
| `debayered` | 3-kanālu krāsu (**noklusējums** `CameraHandle` un visiem `grab()` / `frame_stream()`; `ArrayHandle.capture()`, `capture_smart()` un `stream()` noklusējuma iestatījums ir `raw`): BGR atmiņā (`grab()` / `frame_stream()`), saglabāts kā RGB, izmantojot `capture()` | Aizmugurējā režīmā, kā uzņēmums: kalibrēšanas paketes tumšā nobīde (DSNU) no neapstrādāta attēla, pēc tam OpenCV malu atpazīstošā demosaika — bez linearitātes LUT, līdzena lauka vai krāsu atdalīšanas. Vienīgais līmenis, kas darbojas bez aizmugures režīma, kurā tiek izmantota kameras bibliotēkas vienkāršā bilineārā demosaika (bez tumšā nobīdes). Saglabātais TIFF fails ir fotometriskais RGB, tāpēc apgrieziet atmiņā esošā kadra kanālus (`frame[..., ::-1]`), lai to salīdzinātu ar failu. |
| `radiance` | float32, W/m²/sr/nm, **viena plakne katrai joslai** | Pilna radiometriskā ķēde: demosaic + 3×3 atdalīšana (multispec) + DSNU + flat-field + NIST skala, kur ekspozīcija × pastiprinājums ir izdalīts, lai vērtības būtu absolūtas. |
| `reflectance` | uint16, 32768 = 1,0 (vai uint8, 255 = 1,0 — skatīt `bit_depth`), **viens plaknes slānis katram diapazonam** | Starojuma intensitāte, dalīta ar lejupvērsto starojuma intensitāti (ρ = π·L/E). Nepieciešams DLS/DAQ rādījums — skatīt piezīmi zemāk. |
| `display` | 8 bitu sRGB tipa | GUI ekvivalents: CCM + baltā balanss + gamma, izmantojot kameras aktīvo krāsu profilu. |

Tikai tiešajā (bez backend) režīmā ir pieejams tikai `debayered`: `grab()` citā līmenī ignorē
`NotImplementedError`, `capture()` ignorē `processing`, un `frame_stream()` paaugstina
`RuntimeError` katrā līmenī. `reflectance` ir nepieciešams izmantojams lejupvērstais rādījums — kadra beigu punkts automātiski ievilina
apvienoto DAQ kameras DLS slotā, taču, ja nav piesaistīts DAQ, ķēde noraida
reflektances izeju un godīgi atzīmē pazeminājumu atgrieztos metadatos, nevis klusi
atdodot zemākas kvalitātes rezultātu.

> **Reflektances DN skala — to nedrīkst ieprogrammēt.** LATTICE atstarojums izmanto `32768` = ρ 1,0 un atzīmē
> XMP `Chloros:PixelScale=32768`; Survey3 atstarojums izmanto `65535` = ρ 1,0 un nesatur
> `Chloros:*` tagu. Izlasiet tagu un daliet ar to. Tas ir definēts uint16 domēnā, tādēļ tas paliek
> `32768` visos formātos, kuros tiek veikta mērogu pārrēķināšana (16-bitu TIFF, 8-bitu TIFF, 8-bitu PNG/JPG, 32-bitu procentos)
> — vispirms normalizējiet saglabāto datu tipu atpakaļ uz uint16 (×257 no 8 bitu, ×65535 no float). Vienīgais
> izņēmums ir fails no versijas pirms 1.3.0: toreiz 8 bitu TIFF tika *apgriezts*, nevis pārskalots, tāpēc
> to nevar aprakstīt ar mērogu, un tajā nav `PixelScale` vai MicaSense tuples. Trūkstošu tagu
> LATTICE atstarošanas failā uzskatiet par „nav derīga mēroga”, nevis par noklusējumu.

**Kalibrēšanas tagi: tikai `raw`.** Uzņemuma `raw` failā ir iekļauts kameras kalibrēšanas pakete: `CalibrationJSON` datu bloku un ar MicaSense saderīgās birkas (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), kas apraksta sensora neapstrādātos datus. Katrs nākamais līmeņa fails (`debayered`, `display`, `radiance`, `reflectance`, `index/` attēls) un katrs nepārtrauktas ierakstīšanas / daudzkameru plūsmas kadrs satur kameras identifikatoru (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...), kā arī paša kadra ierakstu — `XMP-mapir:FrameJSON` (ekspozīcija, pastiprinājums, temperatūra, pikseļu formāts, uzņemšanas laiks, DAQ saskaņotība), jo katram `process()` produktam ir savs identifikators. Apstrāde atrod šāda faila kalibrāciju pēc tā sērijas numura un `CalBundleSha256` „Chloros” kalibrācijas kešatmiņā, vienreiz lejupielādējot paketi, ja šajā datorā tā nav.

#### No vairākām kamerām izveidoti faili

Fails, kas izveidots no vairāku kameru spektrālajiem diapazoniem — daudzkanālu salikums (`capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index(save_multiband=True)`, `Registration.save_multiband()`), no vairākām kamerām izveidots reāllaika indekss (`index.tif`, `index_LUT.png`), masīva uzņemuma attēls `composite/` — nosauc katru kameru, kas tajā iekļauta:

- **Katrā-kameru saraksti, joslu secībā** (kompozīcija: fona slānis, tad priekšplāns; indeksa slānis, kameras, no kurām nāk to joslas): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (kalibrēšanas pakete, kuru izgājušas katras kameras joslas, citādi — `capture_aligned_multiband()` / `start_aligned_stream()` kopums, kura joslas neiziet nevienu — kameras pakete, ko Chloros ir saglabājis kešatmiņā, tas, kas atbilst tās uzņemto failu nosaukumam), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — paralēli saraksti, kur katra *n*. elements apraksta to pašu kameru; `-`, kur kamerai nav vērtības (nav kalibrēšanas pakas, nezināms objektīvs). Katra kamera ir **viens** elements: ja tās pakai ir vairākas vērtības vienam laukam — pakas halogēnās lampas sertifikāti — tie tiek savienoti ar `;` (`F-1479-1;F-1480-1;F-1481-1`), nekad ar komatu, tādējādi sarakstā ir viens ieraksts katrai kamerai neatkarīgi no tā, kā tas tiek izdrukāts.
- **EXIF `SerialNumber`** satur tos pašus sērijas numurus, kas savienoti ar komatu (`214000535,214000551`).
- Tajā **nav** atsevišķas kameras identifikācijas (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): tie norāda uz vienu kameru un tās komplektu, un apstrādes procesā no tiem tiek noteikta faila kalibrācija. No vairāku kameru joslām aprēķinātais indekss (`index.tif`, `index_LUT.png`) EXIF laukā ir `Model` `LATTICE` (iekārta), nevis vienas kameras `LATT-…` modelis.**Vienas** kameras joslu fails — TIFF fails katrai joslai, `capture_aligned_multiband(per_camera=True)` kadrs, vienas kameras attēlu kopa vai reāllaika indekss — satur šīs kameras identifikācijas tagus, tāpat kā jebkurš cits uzņemšanas fails. `capture_aligned_multiband()` kopai, katras joslas un katras kameras failiem, kā arī katrai `start_aligned_stream()` kopai un tās katras joslas failiem ir norādīts grupas uzņemšanas laiks (atskaites kameras ekspozīcija, UTC), un mono kameras `per_camera` kadrs ir viena plakne, tāpat kā `bit_depth` attēlu kopa.**Kameras orientācija (apgriešana / pagriešana).** Kadri no `grab()` / `frame_stream()` vienmēr ir
kameras sākotnējā orientācijā visos līmeņos. Saglabātie uzņēmumi atbilst kameras orientācijas
iestatījumiem (GUI opcijas „Mirror H” / „Mirror V” / rotācija):

- **`raw` un `debayered` faili precīzi atspoguļo to, ko nolasījis sensors**, neatkarīgi no iestatījumiem — pagriezta vai apgriezta Bayer
  mozaīka būtu atšķirīgs krāsu filtru raksts, un kalibrēšanas kartes ir
  atrodas uz paša sensora režģa.
- **`display`, `radiance`, `reflectance` un indeksa faili ir orientēti** — pagriezti vienu reizi pēc visiem
  kalibrēšanas aprēķiniem. To fiksēšanas maskas atrodas uz tā paša režģa.
- **Katrs uzņemtais fails reģistrē iestatījumu** XMP formātā: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, pulksteņrādītāja virzienā, pēc
  apgriešanas) un `ChlorosOrientationApplied` — `false` neapstrādātiem un debayered attēliem, `true` orientētiem
  produktiem. `ChlorosProcessing` uzskaita operācijas, kas piemērotas šī faila pikseļiem (`none`, ja
  tā ir oriģinālā formātā). Tas ietver katru kadru no nepārtrauktas ierakstīšanas (kameras paša iestatījums,
  nepiemērots) un no daudzkameru straumes (identitāte: tās sastāvā esošie kadri ir oriģināli, tāpat kā
  masīva uzņemumā).
- **Projekta apstrāde piemēro ierakstīto orientāciju** produktiem, ko tā izveido no neapstrādāta
  (vispirms kalibrēšana sensora koordinātēs, pēc tam masīva izlīdzināšanas deformācija, visbeidzot orientācija),
  tādējādi tie atbilst tiešraides uzņemšanas rezultātiem — ieskaitot nekalibrētos sensora reakcijas / vinjetes korekcijas
  rezerves rezultātus; tā debayered rezultāts paliek nativs.
- **Chloros attēlu skatītājs parāda neapstrādātu vai debayered failu, kas pagriezts atbilstoši ierakstam** — tikai attēlošanai,
  fails netiek mainīts; fails bez ieraksta tiek parādīts tā, kā tas ir saglabāts.
- **Uzņemumi bez ieraksta tiek apstrādāti kā oriģināli.** Pirms versijas 1.3.0 viena kameras uzņemums
  pats pagrieza savus neapstrādātos un no bayera atbrīvotos failus; Chloros nevar atšķirt šādu neapstrādātu failu no oriģinālā un
  to neuzmin.
- **Masīva uzņemumi pēc noklusējuma saglabā katru atsevišķo failu oriģinālā formātā** un reģistrē identitāti;
  `ArraySession.capture(orient=True)` pārveido to rezultātus (skatiet masīva uzņemšanas vadības elementus).

#### Izvade pa joslām un jauktie masīvi

`radiance` un `reflectance` līmeņos kamera atgriež **vienu plakni katrai šīs
kameras joslai** — trīs M3C gadījumā (tās trīs nemaisītās joslas, BGR kanālu secība), vienu — M3M gadījumā (tās
vienīgais šaurjoslas filtrs). Mono kameras dublētās plaknes tiek apvienotas, tādējādi **jaukta
M3C/M3M matrica katram sērijas numuram rada atšķirīgas matricas formas**. Izlasiet `bands` atslēgu no kadra
metadatiem, lai uzzinātu, kas ir katra plakne; neizdariet secinājumus, balstoties uz kanālu skaitu:

```python
for serial, (arr, hdr) in arr_handle.grab(processing="reflectance",
                                          with_metadata=True).items():
    print(serial, arr.shape, hdr["bands"])   # ('…', (1536, 2048, 3), ['nir','green','red'])
                                             # ('…', (1536, 2048, 1), ['F685'])
    rho = arr.astype("float32") / hdr["pixel_scale"]
```

Vienmēr pārbaudiet `hdr["processing_level"] == "reflectance"`, pirms izmantojat šīs vērtības. Pazemināšana
(nav DAQ rādījuma, nav kalibrēšanas paketes, nezināms joslu izkārtojums) tiek ziņota `processing_level` un
`radiometric_refused`, nevis paaugstināta — saņemtā matrica joprojām ir derīgs kadrs, vienkārši
nav tas produkts, ko jūs pieprasījāt. Saglabāta ierakstīšana, kurai ir pazemināts statuss, savā XMP nes to pašu kodu
kā `Chloros:RadiometricRefused` (piem., `no-band-layout`, `no-dls-snapshot`).

#### Viens trigers uz vienu nolasījumu — `group_id`

**Masīva elementi tiek apkalpoti no masīva sinhronizētās kadru grupas, nevis no katras kameras paša
uzņemšanas bufera.** Aparatūras sinhronizēta masīva kameras eksponē kopā, bet N nolasījumi katrai kamerai
netiek *paraugoti* kopā — katras kameras pēdējā buferizētā kadra nolasīšana pārklājas pāri trigera
robežas, un viens vienīgs izlaists kadrs desinhronizē kopumu, neko nepamanot.

Apstrāde no grupas ir nepieciešama, bet pati par sevi nav pietiekama: katrs pieprasījums joprojām
izveido momentuzņēmumu no *jaunākās* grupas savā brīdī, un uzņemšanas cikls var publicēt kadrus starplaikā.
Veicot mērījumus reālā 4 kameru masīvā, **12 no 20 paralēlajiem nolasījumiem pārklājās divām grupām** — vienmēr
tieši blakus. Tādējādi nolasījums tiek **fiksēts**:

1. Katrs kadrs satur `group_id` — sistēmas monotonisko publicēšanas skaitītāju.
2. `grab()` nolasa galveno kameru (vai, ja galvenā kamera nav pieejama, nākamo kameru, kas to var izdarīt),
   pēc tam pārējās kameras piesaista grupas identifikatoram, uz kura tā nonāca (`?group_id=N` uz HTTP
   galapunktu).
3. Aizmugures sistēma saglabā nesenās grupas **vismaz pussekundi**, tādējādi brālis, kuru jau
   ir apsteigušas vēlākas publikācijas, joprojām apkalpo šo trigeri — pietiekami ilgi, lai katras kameras
   reflektances eksporta datu pārraidei notiktu pat 9 kameru pilna sensora masīvā. Piesaistīšana, kuru aizmugurējā sistēma vairs
   nesaglabā, tiek noraidīta ar kodu `409` un `group_aged_out: true`, nevis klusi atbildēta
   ar citu trigeri — `grab()` atkārtoti nolasa iestatījumus.

Atkārtoti izmērīts ar fiksēšanu: **20 no 20 nolasījumiem atgriezās viens trigers.** `grab()` to dara jūsu vietā;
HTTP-galapunktu vadot tieši, jums ir nepieciešams tikai `group_id=`.

- Kamera, kuru nevar apkalpot fiksētajā grupā — tā trūkst grupā vai noraida pieprasījumu
  (`band`, kāda tai nav, līmenis, ko tā nevar radīt) — tiek **izlaista, nevis aizpildīta**
  no cita izraisītāja, un pārējās kameras joprojām tiek atgrieztas.
- Vārdnīca, ko atgriež `grab()`, ir `GrabResult` (vienkārša `dict` apakšklase;
  `from chloros_sdk import GrabResult`): `.errors` katru
  izlaisto sērijas numuru saista ar izņēmumu, ko izraisīja tā nolasīšana, un `.group_id` ir kopīgā grupa. Tukša
  `.errors` nozīmē, ka visas kameras veido šo grupu.
- Autonomai kamerai nav grupas, tā ziņo `group_id: None` un **noraida** `group_id=`
  pin, nevis to ignorē.

```python
frames = arr_handle.grab(processing="reflectance")
for serial, err in frames.errors.items():
    print(f"SN:{serial} omitted: {err}")       # e.g. status=400 ... band 'F685' not in [...]

frames = arr_handle.grab(processing="reflectance", with_metadata=True)
ids = {hdr["group_id"] for _a, hdr in frames.values()}
assert len(ids) == 1, f"straddled triggers: {ids}"     # belt and braces
```

Tas pats skaitītājs tiek publicēts **katrā atsevišķā kameras MJPEG plūsmā**, kas pieder masīva loceklim —
priekšskatījumā (`/api/camera/stream/<serial>`), anotētajā priekšskatījumā (`/stream-annotated`) un
reflektances plūsma (`/stream-reflectance`) — kā `X-Chloros-Group-Id` katrā daļā, papildus
`X-Chloros-Frame-Id` un `X-Chloros-Timestamp-Ns`. Plūsmas ir nepārtrauktas, nevis
konkrētā brīdī fiksētas, tāpēc tās tiek saskaņotas, apkopojot daļas un grupējot pēc šīs galvenes — nav nepieciešams
iestatīt nekādu pinu. Atsevišķai kamerai nav grupas. Skatīt
[MJPEG straumes katrai kamerai](#per-camera-mjpeg-streams-no-rtsp).

**`frame_stream()` nekad neapklust.** Tiks, kas neizdodas iemesla dēļ, kas var atrisināties pats no sevis,
tiek izlaists — kameras plūsma par to neko nedod, masīva plūsma parāda tās kameras, kas
atbildēja (iespējams, neviena). Šie iemesli ir: vēl nav kadra; kameras datu ieguves pavediens ir apturēts uz
sekundi vai divām (`404 not streaming`, uz ko aizmugurējā sistēma atbild ikreiz, kad cita kamera izveido savienojumu
vai pārtrauc to, vai arī iestatījumu maiņa atjauno pūla līdzsvaru); aizmugurējās sistēmas pārstartēšana vai `5xx`; izlīdzināšana
vēl notiek (`409 … retry shortly`); un atstarojuma atteikums, jo DAQ vēl nav rādījuma
(`no-dls-snapshot`, novecojis vai tukšs rādījums). Noraidījums nākamajā taktsciklā tikai atkārtotos —
nezināms `band`, `bit_depth=8` attiecībā uz starojuma intensitāti, kalibrēšanas pakete bez starojuma intensitātes skalas, atstarojuma
noraidījums, ja **nav nekāda DAQ vispār** (neviens nav piesaistīts kamerai vai tās matricai, neviens nav savienots ar
backend: atteikuma `dls_source` ir `"none"`), jebkurš cits `4xx` —
izraisa `chloros_sdk.FrameFetchError` (`RuntimeError`, kas pārraida `.status` un `.body`) uzreiz.
Tāpat notiek, ja **60 sekundes** tiek izlaisti takti bez starpā esoša kadra, neatkarīgi no `fps` — pietiekami
ilgs, lai pārvarētu saskaņošanas stabilizēšanos un aizmugures sistēmas atkārtotu palaišanu, pietiekami īss, lai kamera, kas
patiešām ir pazudusi, pārtrauktu straumi. Šī atteikšanās kļūda ir arī `FrameFetchError`, gan kamerai, gan
masīvam, ar pēdējā nolasījuma paša izņēmumu `.errors` (un, kamerai, kā `__cause__`). Attiecībā uz
masīvu „noraidīts” nozīmē, ka visas kameras ir noraidītas; izņēmums `.errors` satur katras kameras
iemeslu.

**Masīvs `frame_stream()` katru trigeri izdod vienu reizi** (tāpat kā `ArrayHandle` un `ArraySession`).
Tiks, kura jaunākais kopums ir jau nodotais izraisītājs, gaida nākamo izraisītāju, nevis
atkal nodod to pašu kopumu, tādējādi plūsma darbojas ar lēnāko no `fps` un masīva paša kadru, un secīgi kopumi nekad nedala vienu un to pašu `group_id`. Masīvs, kas pārtrauc publicēšanu, izraisa
`FrameFetchError` („nav jauna izraisītāja 60 s”) nevis atkārto savu pēdējo kopu. Bez šīs funkcijas
plūsma, kurai tika pieprasīts vairāk, nekā masīvs publicēja — vai sākās tieši pēc savienošanās — izsniedza
atkārtojumus: vienā izmērītajā ciklā 20 kopām bija 11 atšķirīgi grupu identifikatori.

```python
from chloros_sdk import FrameFetchError

try:
    for arr in cam.frame_stream(processing="radiance", bit_depth=8):
        my_analysis(arr)
except FrameFetchError as exc:
    print(exc.status, exc.body)        # the HTTP status and the backend's reason
```

#### Vienas joslas eksportēšana — `band`

`grab()` / `frame_stream()` izmanto `band="<name>"`, lai eksportētu tikai vienu no kameras joslām kā
vienkanāla `(H, W, 1)` masīvu — tāda pati forma, kādu rada mono kamera, tādēļ vienas joslas izvade tiek
apstrādāta identiski neatkarīgi no tā, vai tā nāk no M3M vai no M3C sadalīšanas. FRGN sarkanā josla:

```python
red = cam.grab(processing="reflectance", band="red")        # (1536, 2048, 1)
```

Pieņemamie rakstības varianti ir pilnais izkārtojuma nosaukums (`Red_660`, `NIR_850`, `Green_550`) vai tā saīsinātais
galvenais nosaukums (`red`, `nir`, `green`), neņemot vērā lielos un mazos burtus. Nezināms nosaukums tiek noraidīts, uzrādot pieejamos
nosaukumus.

> **Izvēle notiek pēc nosaukuma, nevis pēc kanāla numura — tas ir apzināti.** „1. kanāls” ir `Green_550`
> masīva BGR kanālu secībā, bet *sarkans* filtra kodaburtu secībā (`RGN`), un abas
> nesakrīt. Izvēloties nepareizo, tiek iegūts pilnīgi ticams kadrs no nepareizās joslas, ko
> nekas tālāk ķēdē nespēj atpazīt. Šī iemesla dēļ nav ciparu indeksa.

#### Plūsmu saskaņošana — `align`

`align=True` deformē kadru atbilstoši masīva kopējam atsauces kadram. Katra kamera tiek deformēta
izmantojot **to pašu** profila izšķirtspēju un apgriezto ROI, tādējādi katras kameras straumes, kas atvērtas neatkarīgi viena no otras,
iznāk **savstarpēji reģistrētas** — tās nav jāpieprasa kopā:

```python
frames = arr_handle.grab(processing="reflectance", align=True)   # {serial: array}
# or one MJPEG stream per camera, each co-registered on its own:
#   GET /api/camera/<serial>/stream-reflectance?align=1
```

Prasības un darbība:

- Kamerai jāatrodas **aparatūras sinhronizētā** masīvā (M8 sinhronizācijas kabelis), kam ir izlīdzināšanas
  profils — savstarpējā reģistrācija pieņem, ka elementi tiek eksponēti vienlaikus, ko programmatūras vai
  brīvi darbojošās matricas nenodrošina. Pretējā gadījumā tiek noraidīts ar kodu `409`.
- **Tiek noraidīts, nekad netiek klusi atstāts nesaskaņots.** Uztveršanas ceļš var saglabāt nesaskaņotus datus ar brīdinājumu,
  jo fails saglabā savas izcelsmes birkas un to var vēlāk reģistrēt no jauna; tiešraidei nav
  šādas otrās iespējas, tāpēc profila trūkums ir kļūda, nevis rezerves risinājums.
- **Saskaņošanas pieprasīšana to sāk.** Pieprasot `align` masīvam bez profila, tiek uzsākta
  kalibrēšana un atgriezts `409` ar to, ko tas gaida (piem., *"gaida, līdz ekspozīcija
  nostabilizējas uz 214000551 (1/3)"*) — mēģiniet vēlreiz, un tas izdosies, parasti dažu desmitu sekunžu laikā.
  Tas ir svarīgi bez monitora režīmā: automātiskā saskaņošana savienošanās brīdī pārtraucas pēc 30 s, un katrs mēģinājums
  šajā laika posmā tiek izlaists, kamēr viedā AE joprojām stabilizējas, tāpēc masīvam, kura ekspozīcija
  konverģē lēni, agrāk visai sesijai nebija profila un nebija iespējas to iegūt,
  neatverot kombinēto skatu darbvirsmā. `alignment_status` ziņo arī par
  `ae_align_holdouts`, norādot kameras, kas vēl stabilizējas, un to progresu.
- Katram viena masīva straumam piešķiriet **vienādu `max_h`**, citādi to apgriezumi nonāks uz atšķirīgiem pikseļu
  tīkliem.
- Izlīdzināšana tiek piemērota *pirms* bitu dziļuma kodēšanas, tādējādi deformācija interpolē pilnas precizitātes
  vērtības.

#### `bit_depth` — precizitātes apmaiņa pret joslas platumu

`grab()` un `frame_stream()` pieņem `bit_depth=16` (noklusējums) vai `bit_depth=8`:

| `bit_depth` | Atstarošanas kodēšana | Izmaksas |
| --- | --- | --- |
| `16` | uint16, 32768 = ρ 1,0, rezerves diapazons līdz ρ 2,0 | Pilna precizitāte. |
| `8` | uint8, aptverot `0..refl_max` (noklusējuma ρ 1,0) | Puse no baitu skaita; ~0,004 ρ kvantizācija pie noklusējuma augšējās robežas. Vērtības virs augšējās robežas tiek nogrieztas — skatīt zemāk. |

##### 8 bitu augšējā robeža — `refl_max`

`refl_max` nosaka ρ diapazonu, ko aptver 8 biti, tādējādi `pixel_scale = 255 / refl_max` un ρ joprojām ir
`pixel / pixel_scale`. `grab()` un `frame_stream()` to izmanto kā atslēgvārdu gan kameras, gan
masīva rīkos (`?refl_max=` uz HTTP-galapunktā). Palieliniet to, lai iegūtu rezervi spoguļveida
spilgtumiem, proporcionāli rupjākos soļos:

```python
frame = cam.grab(processing="reflectance", bit_depth=8, refl_max=1.5)   # 0..1.5 in 0..255
```

| `refl_max` | `pixel_scale` | solis |
| --- | --- | --- |
| `1.0` (noklusējums) | 255 | 0,0039 ρ |
| `1.5` | 170 | 0,0059 ρ |
| `2.0` | 127,5 | 0,0078 ρ |

> **Automātiskā ekspozīcija nevar novērst 8 bitu klipu, un, mēģinot to izmantot, dati kļūst sliktāki.**
> ρ = π·L/E, no kuras ir atdalīta ekspozīcija un pastiprinājums, tādējādi ρ ir ainas īpašība, nevis
> kameras iestatījuma īpašība — mērījumi uz reāla matricas rāda, ka ekspozīcijas samazināšana uz pusi mainīja vidējo ρ par 1,3 %. Savukārt
> ekspozīciju, lai tā „pielāgotos” diapazonam, drīzāk noved pikseļus līdz **sensora** piesātinājumam, kas
> novērtē L par zemu un nobīda ρ **zemāk**, neradot kļūdu (izmērīts: maks. ρ 1,203 → 0,985 pie
> 2× ekspozīcijas, vidējā vērtība −23 %). Izvēlieties diapazonu ar `refl_max`; sensora pasargāšana no
> piesātinājuma jau ir automātiskās ekspozīcijas (AE) uzdevums, un tas aizsargā ρ *derīgumu*, nevis tā diapazonu.

**Lasiet `reflectance_clipped_fraction`, nevis atzīmi.** `reflectance_clipped_at_1_0` iedarbojas uz
vienu pikseli — reālā ainā tas ziņoja par `True`, kamēr daļa bija `0.000001`, viens pikselis
no miljona. Tieši šī daļa norāda, vai ir jāpaaugstina augšējā robeža.

**`bit_depth=8` attiecas tikai uz atstarojumu.** Pieprasījums par `radiance` tiek noraidīts, nevis
izpildīts: atstarošanai ir fiksēts diapazons 0..1, kas saglabājas 8 bitu kodējumā, savukārt starojumam nav
fiksēta diapazona, tāpēc, lai to ietilpinātu 8 bitos, ir nepieciešama katram kadram atsevišķa procentuālā izstiepšana, kuras attēlojums
mainās katrā kadrā — tas ir attēls, nevis mērījums.

#### Reāllaika atstarošanas plūsma (MJPEG)

`GET /api/camera/<serial>/stream-reflectance` nodrošina 8 bitu reflektances datus katram diapazonam kā
`multipart/x-mixed-replace` — M3C formātā kā 3-kanālu JPEG, M3M formātā kā pelēktoņu attēlu. Vaicājuma parametri:
`fps` (noklusējums 10), `jpeg_q` (noklusējums 95), `max_h` (0 = oriģinālais), `band` (viens nosaukts diapazons, kā
iepriekš), `align` (`1` kopreģistrēšanai — tie paši noteikumi kā SDK `align`), `refl_max` (8 bitu
kodēšanas maksimālā vērtība, kā iepriekš). Katra daļa satur
`X-Chloros-Group-Id`, `X-Chloros-Frame-Id`, `X-Chloros-Timestamp-Ns`, `X-Chloros-Bands`,
`X-Chloros-Pixel-Scale`, `X-Chloros-Aligned` un `X-Chloros-Reflectance-Clipped`.
**Daļas no dažādām kamerām, kurām ir viens un tas pats `X-Chloros-Group-Id`, ir viens un tas pats trigers** —
tādā veidā tiek atkārtoti apvienotas vairākas vienas matricas plūsmas no katras kameras, un tas tiek garantēts
servera pusē, jo matricas elementi tiek apkalpoti no sinhronizētās kadru grupas (sk.
[Viens trigers uz vienu nolasīšanu](#one-trigger-per-read--group_id)). Tas atgriež `409`, ja atstarojums
savienošanās brīdī nav pieejams, un pārtrauc plūsmu, ja tas kļūst nepieejams darbības laikā, tādējādi apstājusies
plūsma nozīmē „vairs nav atstarojuma”, nevis „joprojām viss kārtībā, tikai statisks attēls”.

> **JPEG ir zudumu nesošs formāts — šie pikseļi ir mērījums.** Saspiešanas kļūda tieši ietekmē
> atstarošanas vērtības un ir visizteiktākā malās, kur atrodas materiālu robežas. Šī iemesla dēļ kvalitātes
> noklusējuma vērtība ir 95. Ja jums ir nepieciešami nemainīti skaitļi, izmantojiet
> `grab(processing="reflectance")` / `frame_stream(...)`, kas atgriež to pašu rezultātu bez
> kodeka ceļā (un `bit_depth=8`, ja ierobežojums ir datu pārraides izmaksas, nevis kodeks).

> **EXIF dati tiek pārnesti uz eksportēto failu.** `process()` kopē avota uzņēmuma GPS bloku
> **un tā ExifIFD** uz katru produktu, tādējādi eksportētajās failos ir `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` un `CameraSerialNumber`, kā arī
> ģeoreferencēšanu. `FocalLength` ir tas, no kā Pix4D aprēķina zemes parauga attālumu — bez tā
> rekonstrukcija tiek veikta ārkārtīgi nepareizā mērogā (vienā izmērītajā gadījumā 411 m liela teritorija
> tika pārvērsta par 47,8 km lielu). Kopija apzināti nav `-all:all`: IFD0 strukturālās birkas traucē
> LATTICE izvadi, un `ExifImageWidth`/`Height` ir izslēgti, jo tie apraksta avota
> uzņemšanu, nevis eksportēto rastra attēlu.

Uztveršanas posma apakškarodziņi (attiecas uz radiometriskajiem līmeņiem — `radiance`, `reflectance`, `display`):

| Karodziņš | Noklusējums | Nozīme |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + vienmērīgais lauks + 3x3 atdalīšana + NIST radiometriskā skala. |
| `apply_white_balance` | `True` | WB LUT. DLS atbalsts, ja DAQ ir piesaistīts kamerai. |
| `apply_index` | `False` | Veģetācijas indeksa novērtēšana. |
| `index_expression` | `None` | Formulas pārrakstīšana. Jatukšs → automātiski aktivizē indeksu. |
| `annotated` | `False` | GUI dekorāciju pārklāšana (zebra/tīkls/smaile). Nav pieejams `raw`. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Atgrieztā vērtība ir `chloros_sdk.CapturePaths`, tipa `CapturePathMap`, nevis `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` ir `Dict[str, Union[str, List[str]]]`: vienlīmeņa
> `processing` katram sērijas numuram piešķir vienu ceļu, savukārt `processing="all"` tam piešķir
> **sakārtotu sarakstu** ar visiem produktiem, kas saglabāti šai kamerai. `ArrayHandle.capture()` nepieņem
> nepieņem `levels=`, un ignorē atslēgvārdu argumentus, kurus tas nepazīst (`levels`, `daq`,
> `render_index`, `force_daq`), tāpēc nododiet tikai tos, kas šeit parādīti (`aligned=False`
> saglabā nativus failus, tāpat kā `ArraySession.capture()`); izmantojiet
> `ArraySession.capture()` šiem vadības elementiem. Reāllaika kombinēts kompozīts, ja tāds tika straumēts, ierodas zem papildu
> `"combined"` atslēgas vietā, nevis sērijas numura zem. Kods, kas pieņem `str`, pārtrauc darbu
> saraksta formā, neizraisot tipa pārbaudītāja iebildumus — anotācijā bija teikts `Dict[str, str]`
> vēl kādu laiku pēc saraksta formas izlaišanas, tāpēc šis aliass pastāv. Normalizējiet
> tad, ja vēlaties plakanu formu:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

> **Ko nevarēja uzrakstīt: `.failed`.** Karte, ko atgriež `capture()`, ir
> `chloros_sdk.CapturePaths` — vienkārša `dict` apakšklase, tādēļ viss iepriekš minētais darbojas
> bez izmaiņām — kas ietver arī `.failed`: failus, kurus aizmugurējā sistēma nevarēja ierakstīt,
> tie paši ieraksti kā [`CaptureResult.failed`](#files-that-could-not-be-written--captureresultfailed)
> (`serial`, `level`, `error` un `disk_full: True`, ja disks ir pilns). Daļējā
> saglabāšana joprojām atgriež visus ceļus, kas tika ierakstīti; pilnā diskā karte ir
> tukša, un `.failed` norāda iemeslu. `.skipped` uzskaita izlaides pa kamerām, un
> `.group_seq` — sinhronizēto grupu, un `.alignment` / `.oriented` ir
> aizmugures sistēmas, tāpat kā `CaptureResult`. `capture_smart()` tos atgriež kā
> `result["frames"].failed` un `result["failed"]`.
>
> ```python
> paths = arr.capture("./out", processing="all")
> if any(f.get("disk_full") for f in paths.failed):
>     raise SystemExit("drive full -- free space and capture again")
> ```

### Masīva izlīdzināšana

`ArrayHandle` atklāj pilnu izlīdzināšanas virsmu. Profili pēc noklusējuma ir pieejami tikai sesijas laikā — lai tos saglabātu, eksplicīti izsauciet `export_alignment()`.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Manual control points: each camera's pixel coordinates of the same
# features, the reference camera's included (>= 2 per camera). Every one
# of num_frames is solved from these points.
arr.calibrate_alignment(method="manual", model="rigid", control_points={
    "214701173": [[412, 300], [1630, 310], [1020, 1180]],   # reference (master)
    "214701292": [[418, 297], [1637, 306], [1027, 1177]],
})

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Izlīdzināšana savienošanās brīdī

`connect_all(align=...)` var automātiski izlīdzināt katru masīvu savienošanās brīdī:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Ja nav norādīts, tiek izmantots `project.json["config"]["auto_align_on_connect"]`.

### `SensorHandle`

Saglabāts gaismas sensors (DAQ-U, DAQ-M vai DAQ-E), ko atver `connect_all()` aizmugurējās sistēmas [DAQ sensoru pūlā](#daq-sensor-sessions) — tajā pašā pūlā, ko izmanto `connect_daq_sensor()` un `chloros-cli daq pool-connect`.

```python
reading = proj.sensors["Sky"].read()
if reading is not None:
    spectrum, is_saturated, integration_time, x, y, z = reading
```

- `read()` gaida sensora **nākamo** spektru — tas, kas jau ir buferizēts izsaukuma brīdī, netiek ņemts vērā — un atgriež `(spectrum, is_saturated, integration_time, x, y, z)` vai `None`, ja 5 sekunžu laikā nekas neierodas. Pauzēta plūsma tiek atsākta. `spectrum` ir izteikts W/m²/nm, ja sensors ir kalibrēts.
- `.sensor` ir sensora [`DAQSensorSession`](#daq-sensor-sessions), visos pārējos gadījumos: `latest(n=...)`, `status()`, `record_start()` / `record_stop()`.
- `.slot_id`, `.model`, `.serial`, `.name` un `.settings` ir saglabātie ieraksti no `sensors.json`.

**Ko atjauno `connect_all`.** Katrs sensors tiek atvērts pēc tā saglabātās adreses — COM porta, Bluetooth adreses vai DAQ-E servera — un, ja projektā ir reģistrēts sensora identifikators, tam jābūt tieši šim sensoram: cita ierīce šajā adresē tiek noraidīta kā nepieslēgta. Tiek piemērots tā saglabātais integrācijas laiks, kadru vidējā vērtība un automātiskā ekspozīcija, kā arī tā vāciņa korekcija:

| Saglabātais vāka profils | Savienots ar |
| --- | --- |
| Vāka profils (piem., `fov_30`) | Šis profils. |
| Nav izvēlēts | `sunshine_cosine`, savienošanas noklusējums, reģistrēts kā pieņemts (`cap_id_source=auto_default`), tādēļ apstrāde brīdina. Sensori tiek piegādāti bez vāciņa: izvēlieties uzstādīto vāciņu. |
| `none` — izvēlēts kā **Nav (sensors bez vāciņa)** lietotāja saskarnē | Nav vāciņa korekcijas. |

DAQ-E-S neņem vērā vāciņa profilu, neatkarīgi no tā, kas tika saglabāts: tā difuzors ir daļa no kalibrēšanas (skatīt [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor)). Projektā, ko saglabājis agrāks Chloros, kā vienīgā izvēle bija reģistrēts neaizsargāts sensors, tāpēc tas atkārtoti izveido savienojumu ar `sunshine_cosine`; lietotāja saskarnē (GUI) vēlreiz izvēlieties **Nekāds (neaizsargāts sensors)** un saglabājiet projektu.

Sensors, kuru cits klients jau ir atvēris pūlā (CLI, cits skripts), tiek pievienots tāds, kāds tas ir, bez iestatījumu atkārtotas piemērošanas, un `disconnect_all()` ļauj tam turpināt darboties; sensors, ko atvēra `connect_all()`, tiek atbrīvots. Tāpat kā ar `connect_daq_sensor()`, Chloros GUI **nav** daļa no pūla: aizveriet sensoru GUI, pirms savienojat projektu no skripta.**Prasības un ziņojums.** Gaismas sensoriem ir nepieciešams darbojošs Chloros backend, kas ir pieteicies maksas „Chloros+” līmenī (Copper vai augstāk), un `connect_all()` to nekad nepalaiž. Ja neviens nedarbojas, katrs saglabātais sensors tiek uzskaitīts zem `report["sensors"]["errors"]`, norādot to. Tur tiek uzskaitīti arī sensori, kurus backend nevar atvērt, norādot backend iemeslu; `report["sensors"]["missing"]` paliek tukšs gaismas sensoriem.

---

## Tieša aparatūra (bez backenda)

Ja vēlaties, lai LATTICE kamerām (CI, bezgalvas roboti, iegultās sistēmas) nebūtu nekādas atkarības no backend, importējiet `lattice_sdk` tieši — to atkārtoti eksportē `chloros_sdk`. Uzmanību attiecībā uz `CAMERA_AVAILABLE`: `lattice_sdk` atrodas SDK ratiņā, taču tam ir nepieciešama Arena SDK darblaika vide un OpenCV (`cv2`), un ratiņš neinstalē ne vienu, ne otru. Gaismas sensori darbojas caur backend — [`connect_daq_sensor()`](#daq-sensor-sessions) vai projekta `proj.sensors` — ar vienu izņēmumu, DAQ-M joslas lieluma palīgrīku [`DLS`](#daq-m-band-magnitudes--dlsband_magnitudes), kam nepieciešams arī `bleak`.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Iestatījumi un trigers

Trīs no četriem iestatījumiem darbojas **brīvā režīmā**: kamera nepārtraukti eksponē, un
`capture()` atgriež nākamo kadru. `triggered` ir izņēmums — tas sagatavo
kameru aparatūras signālam 2. līnijā, tādēļ tā neko neuzņem, kamēr šāds signāls neierodas.

| Iestatījums | Trigeris | Lietot, ja |
| --- | --- | --- |
| `default` | brīvā darbība | vispārējai lietošanai |
| `high_speed` | brīvā darbība | 8 biti, 60 fps ierobežojums, īsa ekspozīcija |
| `high_quality` | brīvā režīma | 12 bitu, bez kadru skaita ierobežojuma — parastā izvēle fotogrāfijām |
| `triggered` | **gatavs, 2. līnija** | kamera ir pieslēgta ar M8 sinhronizācijas kabeli, un to iedarbina kāds cits avots |

Ja izvēlaties `triggered` (vai paši iestatāt `trigger_mode="On"`), kad nekas
nedarbina 2. līniju, katram `capture()` beigsies laiks — pareizi, jo jūs lūdzāt
kamerai gaidīt. SDK to izskaidro, kad tas notiek; skatiet
[SC_ERR_TIMEOUT uzņemšanas laikā](#direct-hardware-backend-free).

> **Piezīme — „GVSP probe” / `SC_ERR_TIMEOUT -1011` ziņojumi savienojuma izveides brīdī nav kļūdas.**
> Savienojuma izveides brīdī SDK mēģina vienoties par**jumbo rāmjiem** (9000 baitu GVSP paketes), lai nodrošinātu lielāku caurlaidspēju. Tiešā „punkts-punkts” tīkla kartes savienojumā (piem., lokālā `169.254.x.x` adrese) tīkls parasti nespēj pārraidīt jumbo rāmjus, tāpēc šī pārbaude beidzas ar laika limitu un protokolā tiek reģistrētas tādas rindas kā:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Tas ir **paredzētais rezerves risinājums**: SDK automātiski atgriežas pie standarta 1500 baitu pakešiem, un kamera turpina savienoties kā parasti (sekojošās `[chunk-enable …]` rindas ir daļa no parastās savienošanās secības). Attēlu uzņemšana joprojām darbojas.
>
> Jūs varat izlaist šo pārbaudi, bet **tā nav tikai žurnāla ierakstu slāpētājs — tā atslēdz jumbo rāmjus.** Kamera atbild uz „Don't-Fragment” pingiem tikai līdz 1500 baitiem, neatkarīgi no tā, cik labs ir jūsu tīkls, tāpēc ar pinga testu vien nekad nevar atrast jumbo rāmjus; šī pārbaude ir vienīgā, kas to spēj. Atspējojiet to, un kamera uz visiem laikiem darbosies ar standarta 1500 baitu pakešiem, jebkurā tīklā:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Tas ir lietderīgi tikai tīklā, par kuru jūs *zināt*, ka tas nespēj pārraidīt jumbo rāmjus, kur tas ietaupa aptuveni vienu sekundi savienošanās laika katrai kamerai. Tā kā tas ir reāls kompromiss, nevis tikai kosmētisks, SDK tagad to norāda, kad to izmantojat:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Neaiztiekiet to, ja vien jums nav iemesla.**Ja funkcija ir ieslēgta, katra kamera tiek izmērīta vienu reizi katrā backend sesijā un atkārtoti, kad mainās tās savienojums (cits adapteris, cits savienojuma ātrums vai pārtraukts savienojums, piemēram, komutatora nomaiņas gadījumā); atkārtotās savienošanās izmanto iepriekšējo rezultātu.**Pēc izmaiņām aiz komutatora, kas palicis ieslēgts, restartējiet backend, lai veiktu atkārtotu mērīšanu.** Pirmajai savienošanai šī pārbaude dažreiz var aizņemt dažas minūtes, un žurnālā tiek norādīts, ja tas notiek.
>
> Ja *vēlaties* jumbo caurlaidspēju, aktivizējiet jumbo no gala līdz galam (NIC MTU 9000 + komutators, kas tos caurlaida), vai fiksējiet to ar `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000`, ja zināt, ka savienojums to atbalsta — tomēr priekšroku dodiet komandas līmeņa iestatījumam `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …`, nevis pastāvīgai iestatīšanai, jo fiksēts izmērs izlaiž pārbaudi un pārtrauc pielāgošanos tīklam, kas atrodas tā priekšā. **Katrai** ierīcei ceļā ir jāpārraida jumbo paketes — ieskaitot jebkuru PoE sadalītāju vai injektoru, kas parasti ir iemesls, kāpēc citādi jumbo-spējīga konfigurācija nespēj tās pārraidīt.

> **`SC_ERR_TIMEOUT -1011` laikā, kad darbojas `capture()` / `grab*()`, ir atšķirīga problēma — tā ir reāla kļūda.**
> Iepriekš minētā piezīme attiecas tikai uz `-1011`, ko reģistrējis**savienojuma laika zondes**. Tā pati kļūda, kas parādās**uzņemšanas** laikā, nozīmē, ka kamera ir veiksmīgi savienota, bet nesūta attēlus:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> To var atpazīt pēc kameras, kuras *vadības* kanāls darbojas pareizi — atklāšana notiek, iestatījumi un `[chunk-enable …]` ieraksti ir veiksmīgi —, bet *katrs* kadrs pārsniedz laika limitu.
>
> **Parasti iemesls ir tas, ka kamera ir iestatīta uz aparatūras trigeri.** Ja parādās kļūdas `trigger_mode="On"` un `trigger_source="Line2"`, kamera neko nenosūta, kamēr M8 sinhronizācijas kabelī neparādās elektriskais signāls. Ja šai līnijai nav pievienots kabelis, katrs attēla iegūšanas mēģinājums gaida bezgalīgi. Kamera nav bojāta un tīkls darbojas pareizi — tā rīkojas tieši tā, kā tai ir norādīts.
>
> `CameraSettings()` un `default` / `high_speed` / `high_quality` iestatījumi darbojas brīvā režīmā, un attēla uzņemšana, kurai beidzas laiks, kamēr kamera ir ieslēgta, par to ziņo, nevis vienkārši izdrukā `-1011`. `PRESETS["triggered"]` ieslēdz Line2, kā paredzēts.
>
> Lai piespiestu jebkuru kameru darboties brīvā režīmā:
>
> ```python
> import copy
> settings = copy.deepcopy(PRESETS["high_quality"])   # PRESETS entries are shared
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> Ja ar `trigger_mode="Off"` joprojām notiek laika izbeigšanās, kamera patiešām nenodod datus — nosūtiet mums žurnālu un `ip link show`.

#### Krāsu profili (RGB reāllaika priekšskatījums) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` izvēlas displeja krāsu profilu **reāllaika priekšskatījumam** RGB kamerās (daudzspektru kameras šo iestatījumu neņem vērā):

| Profils | Nozīme |
| --- | --- |
| `raw` | Pilnībā apiet radiometrisko ķēdi. |
| `linear` | DSNU + flat + WB, bez CCM, bez gamma. |
| `natural` | Lineārs + izmērīts CCM + sRGB gamma, tikai ar vienkāršu apdari (krāsu izlīdzināšana + spilgtāko toņu desaturācija) — reālistiskais noklusējums. |
| `enhanced` | `natural` plus pilnīga „hub-parity” apstrāde (malu izlīdzināšana, vibrance, CLAHE lokālais kontrasts). Bagātāks izskats, kas izmaksā aptuveni **divreiz vairāk uz vienu kadru**, tādējādi zemāks LIVE kadru ātrums. |
| `custom_temp` | `natural`, bet WB fiksēts uz `custom_cct_k` Kelvina (DLS tiek ignorēts; fiksēts uz 2000–10000 K aizmugurējā procesora pusē). |

Profils ir **tikai reāllaika priekšskatīšanai paredzēts** ātruma/izskata regulētājs: saglabātajiem kadriem vienmēr tiek piemērota pilnībā bagātīga apdare neatkarīgi no izvēlētā profila, tādēļ `natural` izvēle, lai atgūtu kadra apstrādes laiku, nesamazina uz diska saglabātā materiāla kvalitāti. Nezināms profils palielina `ValueError`; ja ir pieejams chloros backend, izmaiņas tiek nosūtītas arī uz to, lai nākamais priekšskatījuma kadrs tās atspoguļotu (tiešie SDK lietotāji bez backenda joprojām saņem iestatījumu izmaiņas).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Izcelšanas (RGB-displejs) — `set_highlights`

`LatticeCamera.set_highlights(value)` izceļ spilgtākos, baltākos pikseļus **displejā** RGB-kamerās — tas novērš problēmu, kad spilgti balta virsma tiek attēlota kā vienkrāsains balts plankums. `value` ir `-100`..`0` (ierobežots); `0` ir izslēgts, un attēls tiek atveidots tieši tā, kā tas izskatās bez šī iestatījuma. Spēcīgāki iestatījumi samazina ekrāna baltumu (līdz aptuveni 60 % pie `-100`) un paplašina toņu līkni, tādējādi virsma, kuru priekšskatījuma automātiskais spilgtums bija padarījis par tīri baltu, atgūst savas detaļas.

Tas ir displeja vadības elements, taču atšķirībā no piesātinājuma un kontrasta (kas maina tikai reāllaika priekšskatījumu), tas maina arī to, ko jūs saglabājat: reāllaika priekšskatījums un **displeja līmeņa** uzņēmumi to ņem vērā, tāpēc arī šie faili iznāk tumšāki (tiem tiek piemērots `Chloros:DisplayHighlights`); spožuma un atstarošanas eksporti to nekad nedara, un tas nevar atjaunot sensora datus, kas ir nogriezti — šādā gadījumā samaziniet ekspozīciju. Multispektrālās un mono kameras to ignorē. Vērtība tiek saglabāta `settings.highlights` un nosūtīta uz sasniedzamu chloros backend, tieši tāpat kā `set_color_profile`; CLI ekvivalents ir `lattice highlights --set N`.

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_highlights(-60)                      # 0 = off, -100 = strongest
```

#### Mono (M3M) kameras un `Calibration`

Mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) ir vienfrekvences: viena pelēktoņu plakne, bez Bayer mozaīkas, bez 3×3 spektrālās pārklāšanās matricas. `Calibration` to atpazīst un parāda `is_mono` karodziņu. Atstarošanās joprojām tiek piemērota kā radiometriska karte katram diapazonam (atdalīšanas matrica ir identitātes matrica), taču daudzjoslu aprēķini, izmantojot vienu kameru, rada jēgpilnus rezultātus, nevis bezjēdzīgus:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Lai izveidotu veģetācijas indeksu, izmantojot mono kameru aparatūru, apvienojiet vairākas M3M kameras ar dažādiem viļņu garumiem vienā saskaņotā daudzjoslu kopā (skatīt [Masīva saskaņošana](#array-alignment)) un aprēķiniet indeksu visai šai kopai, nevis tikai vienai kamerai.

#### DAQ-M joslu magnitūdas — `DLS.band_magnitudes`

`DLS(mac_address, filter_model="RGN-IMX265")` savienojas ar DAQ-M pa Bluetooth, izmantojot savu BLE MAC. Tā ir vienīgā gaismas sensoru klase, kas sazinās ar sensoru tieši, nevis caur aizmugurējo sistēmu, tāpēc tai ir nepieciešams tas pats, kas tiešajām kameru klasēm (`CAMERA_AVAILABLE`: „Arena“ SDK izpildes vide un OpenCV), kā arī `bleak` Bluetooth savienojumam (`pip install "chloros-sdk[camera]"` vai `pip install bleak`; bez tā `DLS` izraisa `DLSError`). Ja citur pieslēgts DAQ-M (aizmugurējās sistēmas pūls, projekts, GUI) pārtrauc reklāmu, tāpēc vispirms atbrīvojiet to tur. **`mac_address` ir obligāts**: nav noklusējuma vērtības, jo katrai vienībai ir sava, un `DLS()` bez tās izraisa `DLSError`, pirms tiek mēģināts izveidot jebkādu savienojumu. Atrodiet MAC ar `discover_daq_sensors(transports=["ble"])` (skatīt [Atklāšana](#discovery--finding-an-address-to-connect-with)) vai `chloros-cli daq pool-discover --only ble`.

`DLS.band_magnitudes(spectrum=None, *, spectral_response=None)` integrē DAQ-M spektru (pēdējais nolasījums, ja `spectrum` ir `None`; `DLSError`, ja tāda vēl nav) ar kameras trīs kanālu atbildēm un atgriež `{"red": …, "green": …, "nir": …}` — atslēgas nosauc kanālus šajā secībā; izmantojot RGB-kameras līknes, trešais (`nir`) ir tās zilais kanāls.

- Bez `spectral_response` tiek izmantota filtra modeļa standarta līkne (ar kuru tika izstrādāts `filter_model` un `DLS`), kas tika izmērīta uz vienas ierīces.
- `spectral_response` tā vietā izmanto kameras **pašas** izmērītās līknes: vārdnīca `{"wavelengths": [...], "R": [...], "G": [...], "B": [...]}`, kā tā ir saglabāta šīs kameras kalibrēšanas paketē (`color_correction.spectral_response_smoothed` — RGB-kameras; multispektrālajās paketēs šāda līkne nav iekļauta). Ja vārdnīca nav izmantojama, tiek izmantota flotes līkne.

```python
from chloros_sdk import DLS

# camera_curves: {"wavelengths": [...], "R": [...], "G": [...], "B": [...]}
with DLS(mac_address="AA:BB:CC:DD:EE:FF") as dls:
    reading = dls.acquire()
    fleet = dls.band_magnitudes(reading)                         # fleet curve
    own = dls.band_magnitudes(reading,
                              spectral_response=camera_curves)   # the camera's own
```

CLI komanda `chloros-cli lattice dls --camera SERIAL` veic to pašu darbību, izmantojot aizmugurējo sistēmu, atrodot kameras līkni tās ielādētajā vai kešētajā kalibrēšanas paketē, un norāda, kuras kameras līkne tika izmantota. HTTP protokolā tas ir `POST /api/dls/cli-read` ar `mac` (obligāti: bez tā atbilde ir HTTP 400, `code` `MAC_REQUIRED`) un `camera_serial` atbildes ķermenī; atbildes `band_magnitudes_source` ir `camera <serial>`, ja tika izmantota šīs kameras paša līkne, vai `model <filter>`, ja tika izmantota flotes līkne (kamera bez līknes: nav RGB vai nav ielādēts pakotne vai nav saglabāts kešatmiņā).

---

## Izņēmumi

Izmantojiet bāzes klasi, lai apstrādātu „visas Chloros kļūdas”:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` un `ChlorosConfigurationError` ir `chloros_sdk` (un `chloros_sdk.exceptions`), bet ne `__all__`, tādēļ `from chloros_sdk import *` tos neiekļauj: importējiet tos pēc nosaukuma. `ChlorosError` ģimene tiek izveidota, pamatojoties uz `ChlorosLocal` un uz tā balstītajām funkcijām (`process_folder()`, `process_lattice_capture()`); viedās savienošanas virsma izveido `ChlorosConnectError`.

Hierarhija:

```

ChlorosError
├── ChlorosBackendError           (backend binary not found / failed to start)
├── ChlorosConnectionError        (HTTP transport failure; no backend running
│                                  with auto_start_backend=False)
├── ChlorosLicenseError           (403 PLAN_UPGRADE_REQUIRED: plan below Copper)
├── ChlorosAuthenticationError    (401 AUTH_REQUIRED: not logged in)
├── ChlorosConfigurationError     (bad configure() inputs)
└── ChlorosProcessingError        (pipeline failed; create/import refused)
    └── ChlorosProjectFilesMissingError  (open_project(): none of the
                                  project's source files found, even after
                                  the relink; .missing_roots, .audit)

ChlorosConnectError                (the smart-connect surface: connect_*, attach_array,
                                    list_*, discover_*, every CameraSession /
                                    ArraySession / DAQSensorSession method,
                                    RecorderHandle, ContinuousHandle,
                                    disconnect_all(), and analyze_array_network()
                                    when no backend answers; 401 and 403 included.
                                    Derives from plain Exception, NOT from
                                    ChlorosError, so `except ChlorosError` will
                                    not catch it. .warning: a warning the
                                    backend sent beside its error (also at the
                                    end of the message); .recording_closed: the
                                    recording a failed DAQ-E restart closed;
                                    both None otherwise)

FrameFetchError (RuntimeError)     (grab() / frame_stream() refusals; .status,
                                    .body, .errors)
ValueError                         (capture() arguments: PNG/JPEG with a level they
                                    cannot hold; a bad packed=)
FileNotFoundError / ImportError    (open_project(): no such folder / no project
                                    loaders)
RuntimeError                       (CameraHandle / ArrayHandle: a backend refusal)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Pilnīgi izstrādāti piemēri

### 1. Mapes apstrāde ar pielāgotu progresa joslu

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Reāllaika LATTICE masīvs → atstarošanās + DAQ atsauce

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Projektorientēta datu ieguves kampaņa

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Daudzkameru kadru plūsma → NumPy apstrādes ķēde

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. Bezgalvas tiešās aparatūras (bez aizmugures) uzņemšanas skripts

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. Funkciju pārbaude pirms 4 kameru masīva pieslēgšanas

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["width"], height=r["height"],     # before binning
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Ierakstīšanas receptes ekvivalents (tīrā Python valodā)

`run_project_recipe()` izpilda CLI recepti tādā veidā, kāda tā ir (skatīt [Projekta mape un receptes](#project-folder--recipes)). Tās pašas darbības, kas izrakstītas Python valodā, ir šādas:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply: connect_all() applied each camera's saved settings. CameraHandle
    # has no settings method; a CameraSession on the same pooled camera does
    # (it is borrowed, so it never releases the camera)
    chloros_sdk.connect_camera(cam.serial).set_settings(
        exposure_time=5000, gain=6.0)

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## MCP serveris (AI palīgi)

`chloros-mcp` ļauj AI palīgam, kas izmanto [Model Context Protocol](https://modelcontextprotocol.io) (MCP), piemēram, Claude Desktop, Claude Code vai Cursor, vadīt Chloros: apstrādāt attēlus (izveidot projektu, importēt uzņemto attēlu mapi, izvēlēties iestatījumus, apstrādāt, ziņot par eksportētajiem rezultātiem) un vadīt LATTICE kameras, matricas un DAQ gaismas sensorus (izveidot savienojumu, apskatīt priekšskatījumu, iestatīt ekspozīciju un krāsu, uzņemt attēlus, ierakstīt, atjaunināt programmatūru). Tā rīki aptver to, ko dara šis SDK. Jūs uzdot jautājumu vienkāršā valodā, un asistents izsauc servera rīkus:

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

The assistant calls:
  create_project          {"project_name": "Flight_2026-10-17"}
  import_images           {"folder_path": "D:\\Flights\\2026-10-17"}
  configure_processing    {"indices": ["NDVI"]}
  start_processing        {}
  get_processing_status   {}      (every 30-60 s until its state is "finished")

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.

The assistant calls:
  list_devices            {}
  get_camera_preview      {"array_id": "array-1779862544497"}
  start_capture_run       {"array_id": "array-1779862544497", "mode": "interval",
                           "interval_s": 10, "duration_s": 300,
                           "processing": "reflectance"}
  get_job_status          {"job_id": "..."}   (until its state is "finished")
```

Serveris ir veidots, balstoties uz šo SDK: apstrādes rīki izsauc [`ChlorosLocal`](#image-processing--chloroslocal), bet aparatūras rīki — [smart-connect izsaukumus](#smart-connect-for-lattice-cameras), [DAQ sesijas](#daq-sensor-sessions), `ChlorosProject` un [kameras palīgrīkus](#camera-utilities), kas visi sazinās ar to pašu aizmuguri, ko izmanto Chloros lietotne. Tās pieprasījumos ir iekļauts `X-Chloros-Client: sdk`, tādējādi backend piemēro SDK pieteikšanās un plāna pārbaudes, kā arī SDK noteikumus (piemēram, funkcija „Texture Aware” tiek noraidīta attēliem, kas uzņemti ar LATTICE kamerām).

### Instalēšana

Serveris tiek piegādāts `chloros-sdk` paketē; nekas cits nav jāinstalē. SDK instalē komandu `chloros-mcp`, kas ir tā pati kā `python -m chloros_sdk.mcp_server`:

```bash
pip install chloros-sdk        # not needed where the Chloros installer set up the SDK (Step 2)
chloros-mcp --help
```

- **Windows:** Chloros instalētājs ar komandu „pip” instalē SDK jūsu Python vidē, tādējādi ievietojot `chloros-mcp.exe` šīs Python vides `Scripts` mapē.
- **Linux (.deb):** pakete instalē SDK sistēmai `python3` un ievieto komandu `chloros-mcp` mapē `/usr/bin`, tādējādi tā atrodas katra lietotāja `PATH`. Virtuālajā vidē (virtualenv) `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` pievieno `chloros-mcp` virtuālās vides `bin`.

Serveris izmanto tikai standarta bibliotēku papildus SDK (Python 3.7+). MCP klients to palaista un sazinās ar to, izmantojot stdin/stdout; jūs to nepalaidāt paši, izņemot ar `--help`.

### Klienta konfigurācija

**Claude Desktop:**pievienojiet serveri `claude_desktop_config.json` (Windows vidē — `%APPDATA%\Claude\claude_desktop_config.json`; to var atvērt, izvēloties**Settings > Developer > Edit Config**), pēc tam pārstartējiet Claude Desktop:

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

**Claude Code:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Kursors:** tāds pats ieraksts `mcpServers` kā Claude Desktop, atrodams `~/.cursor/mcp.json` (katrā projektā; `%USERPROFILE%\.cursor\mcp.json` operētājsistēmā Windows) vai `.cursor/mcp.json` vienā projektā.

Opcijas ievada `args` vai pēc komandas Claude Code (`claude mcp add chloros -- chloros-mcp --no-auto-start`). Lai izmantotu konkrētu Python SDK vietā `chloros-mcp` uz `PATH`, izpildiet moduli ar šo Python (norādot tā pilno ceļu kā `command`, ja tas nav pirmais `python3` uz `PATH`):

```json
{
  "mcpServers": {
    "chloros": {"command": "python3", "args": ["-m", "chloros_sdk.mcp_server"]}
  }
}
```

Klients sāk serveri bez jūsu čaulas, tādēļ, ja tas ziņo, ka serveris nav sācies, `chloros-mcp`, visticamāk, neatrodas `PATH`, ko redz klients. Tā vietā norādiet pilnu ceļu (`where chloros-mcp` sistēmā Windows, `which chloros-mcp` sistēmā Linux, kur .deb faili atrodas `/usr/bin/chloros-mcp`), vai palaidiet moduli ar Python, kurā ir iekļauts SDK.

### Opcijas

| Opcija | Noklusējums | Darbība |
| --- | --- | --- |
| `--backend-url URL` | `http://127.0.0.1:5000` | Izmantojamais Chloros backend. |
| `--no-auto-start` | off | Nekad nepalaiž backend: izmanto jau darbojošos, piemēram, Chloros lietotnes, Linux backend pakalpojumu vai tuneli uz citas ierīces (sk. [Tālbackenda režīms](#remote-backend-mode-pip-only-host-via-tunnel); šajā gadījumā mapju ceļiem abās ierīcēs jānorāda viena un tā pati mape). |
| `--backend-exe PATH` | tur, kur to instalē Chloros | Aizmugurējais modulis, kas jāpalaiž, ja neviens nedarbojas, gadījumā, ja Chloros ir instalēts vietā, kur SDK to nemeklē. |
| `--log-level LEVEL` | `WARNING` | `DEBUG`, `INFO`, `WARNING` vai `ERROR`. Žurnāli tiek nosūtīti uz stderr, ko lielākā daļa klientu parāda savā MCP žurnālā; stdout pārraida tikai protokola ziņojumus. |

### Rīki

Katram rīkam argumenti ir tādi paši kā SDK izsaukumam, kuru tas aptver; klients uzskaita katru no tiem ar pilnu shēmu. Celiņi ir absolūti.

**Apstrāde**

| Rīks | SDK izsaukums | Ko tas dara |
| --- | --- | --- |
| `create_project` | `ChlorosLocal.create_project` | Izveido projektu un padara to par atvērtu projektu, ar kuru strādā apstrādes rīki. Vienkāršs nosaukums izveido mapi noklusējuma projekta mapē; absolūts ceļš to izveido tur. |
| `open_project` | `ChlorosLocal.open_project` | Atver saglabātu projektu un padara to par atvērtu projektu. Pārvietota projekta ceļi tiek pārvirzīti uz mapi, kurā tas atrodas tagad; `relink_folder` (absolūtais) nosauc mapi, kurā tagad atrodas pārējie trūkstošie faili. Atgriež `source_files` un `image_count`. Neviens nav atrasts: kļūda, norādot to atrašanās vietu un nosaucot `relink_folder`; nekas nav atvērts. |
| `import_images` | `import_images` | Importē Survey3 `.RAW`+`.JPG` pārus, „LATTICE” `.tif` uzņemumus un DAQ `.daq` ierakstus; atgriež `count` (attēlus) un `scan_count` (ierakstus). |
| `configure_processing` | `configure` | Maina nodotos iestatījumus un neko citu (vērtības: [Atbalstītās vērtības](#supported-values)); `custom_settings` nosūta visu konfigurāciju tā, kā tā ir norādīta. |
| `get_project_settings` | `get_config` | Nolasīta atvērtā projekta saglabātos apstrādes, eksportēšanas un indeksēšanas iestatījumus. |
| `start_processing` | `process(wait=False)` | Sāk apstrādāt atvērto projektu un nekavējoties atgriežas. |
| `get_processing_status` | `get_status` | Ziņo par `state` (`idle`, `starting`, `processing` vai `finished`) un gaitu; kad process ir pabeigts, tiek ierakstīti `summary`, `export_check_failure`, ja eksportētos datus nevarēja pārbaudīt, un `image_failure`, ja attēli tika zaudēti; pārējie rezultāti tiek ierakstīti; ziņo, ka process ir neizdevies. |
| `process_folder` | `process_folder` / `process_lattice_capture` | Izveido projektu mapē, importē to, konfigurē un sāk izpildi vienā izsaukumā (`lattice: true` attēlu uzņemšanai ar LATTICE). Kamēr darbība ir procesā, tā tiek pārtraukta, pirms kaut kas tiek izveidots, un arī backend šajā brīdī nepieņem projekta izmaiņas no jebkura rīka. |
| `export_light_sensor` | `export_light_sensor` | Raksta kalibrētus `.daq` un `.csv` failus projekta gaismas sensora ierakstiem, neapstrādājot attēlus, iegūstot rūpnīcas kalibrāciju no MAPIR Cloud, ja tā nav saglabāta kešatmiņā (sk. [Gaismas sensora ieraksti](#light-sensor-recordings--calibrated-daq--csv)). |
| `read_image_audit_tags` | `read_image_audit_tags` | Nolasa apstrādāta attēla Chloros pārbaudes metadatus (avots, kalibrēšana, piemērotie posmi). |

**Kameras un matricas**

| Rīks | SDK izsaukums | Funkcija |
| --- | --- | --- |
| `list_devices` | `list_cameras`, `list_arrays`, `list_daq_sensors` | Kas šobrīd ir pieslēgts jebkurā klientā, katram ar `connected_here`; kā arī servera izpildāmie uzdevumi. |
| `discover_devices` | `discover_lattice_cameras`, `discover_daq_sensors` | Kameras un gaismas sensori, kurus varētu pieslēgt. |
| `get_device_status` | `CameraSession.status`, `ArraySession.status` / `capability`, `DAQSensorSession.status` | Vienas kameras, masīva (ar tā iespējām un darbspēju) vai gaismas sensora reāllaika stāvoklis. |
| `analyze_array_network` | `analyze_array_network` | Ko šis hosts un tīkls var nodrošināt ierosinātajam masīvam. |
| `connect_camera` / `connect_array` | `connect_camera` / `connect_array` | Pievienot kameru vai sinhronizētu masīvu (vispirms galveno). |
| `connect_project_devices` | `open_project(...).connect_all` | Pievienojiet visas ierīces, kas ir saglabātas projektā, kopā ar to iestatījumiem. |
| `disconnect_device` | `disconnect` / `force_disconnect` | Atvienojiet kameru, masīvu vai gaismas sensoru (skatiet sadaļu „Darbība”). `keep_settings: true`, kas darbojas ar matrici, saglabā katras kameras iestatījumus tās paša atmiņā, `clear_settings: true` atiestata šo atmiņu (tā rezultāts ir `camera_memory`). |
| `disconnect_project_devices`, `align_project_arrays`, `capture_project_devices` | `disconnect_all`, `align_arrays`, `capture_all` | Projekta ierīces kopā. |
| `read_camera_settings` / `set_camera_settings` | `CameraSession.read_nodes` / `set_settings` | Lasīt vai rakstīt kameras iestatījumus, izmantojot draudzīgo nosaukumu vai GenICmezgla nosaukumu. |
| `set_array_frame_rate` | `ArraySession.set_target_fps` | Fiksēt masīva ātrumu vai atgriezt to automātiskajā režīmā. |
| `capture_camera` / `capture_array` | `CameraSession.capture` / `ArraySession.capture` | Viena uzņēmuma (`fastest` un `wait_for_exposure` ir `capture_fastest` un uzņēmums ar fiksētu ekspozīciju). Ja nav `output_dir`, tas tiek saglabāts atvērtā projekta`captures` mapē, un faili tiek pievienoti projektam (tiek aizturēti līdz apstrādes cikla beigām); atbilde `project_add` to apstiprina. |
| `start_capture_run` | `ArraySession.continuous`, `capture_repeated` | Nepārtraukta vai intervāla uzņemšana kā uzdevums. |
| `start_array_recording` | `ArraySession.record` / `burst` | Video ar apvienotu indeksu vai neapstrādāta sērija kā uzdevums. |
| `build_burst_video` | `ArraySession.build_video` | Sērijas pārstrāde par kalibrētu video, kā uzdevums. |
| `get_job_status` / `stop_job` | | Uzdevuma izpildes turpināšana vai apturēšana. |
| `get_camera_preview` | | Katras kameras tiešraides priekšskatījums, kas tiek atgriezts kā attēls. |
| `grab_frames` | `ArraySession.grab`, `CameraHandle.grab` | Viens kadrs (vai viens sinhronizēts kopums) apstrādes līmenī, nesaglabājot: statistika par katru plakni un katras PNG attēls. |
| `calibrate_array_alignment`, `get_array_alignment`, `clear_array_alignment`, `nudge_array_alignment`, `export_array_alignment`, `import_array_alignment` | `ArrayHandle.calibrate_alignment`, `alignment_status`, `clear_alignment`, `tweak_alignment`, `export_alignment`, `import_alignment` | Masīva izkārtojums. |

**Gaismas sensori**

| Rīks | SDK izsaukums | Funkcija |
| --- | --- | --- |
| `connect_light_sensor` | `connect_daq_sensor` | Pievienojiet DAQ-U, DAQ-M vai DAQ-E (`cap_id` iestata tā kapacitātes korekciju). |
| `get_light_sensor_reading` | `DAQSensorSession.latest` | Pēdējie mērījumi: laika zīmogs, piesātinājums, spektra diapazons un maksimums, kā arī spektra vidējā vērtība, sadalīta pa viļņu garuma intervāliem (`full_spectrum` katram punktam). |
| `set_light_sensor_streaming` | `stream_start` / `stream_stop` | Sāk vai aptur datu plūsmu. |
| `start_light_sensor_recording` / `stop_light_sensor_recording` | `record_start` / `record_stop` | Ierakstīt `.daq` failu kā uzdevumu; `duration_s` to aptur pats. |
| `set_light_sensor_cap` | `DAQSensorSession.set_cap` | Vāka korekcija (`none` neapklātam sensoram). |
| `get_light_sensor_bindings` / `set_light_sensor_bindings` | `daq_bindings` / `set_daq_bindings` | Kura sensora dati tiek izmantoti katras kameras un matricas atstarošanas koeficienta aprēķināšanai. |
| `get_light_sensor_band_presets` / `get_light_sensor_reflectance` | `daq_band_presets` / `light_sensor_reflectance` | Viļņu joslu kopas, kā arī divu straumējošo sensoru vides un objekta atstarojums un indeksi. |
| `get_light_sensor_device_info`, `set_light_sensor_network`, `set_light_sensor_auto_stream`, `enable_light_sensor_ptp`, `reboot_light_sensor`, `update_light_sensor_firmware` | `device_info`, `set_network`, `set_auto_stream`, `enable_ptp`, `reboot`, `update_firmware` | DAQ-E ierīces pašas iestatījumi, kas saglabāti ierīcē. |

**Kameras palīgrīki** ([Kameras palīgrīki](#camera-utilities))

| Rīks | SDK izsaukums | Funkcija |
| --- | --- | --- |
| `get_camera_color_settings` / `set_camera_color_settings` | `camera_color_profile`, `camera_color_adjust`, `camera_highlights` un to iestatītāji | RGB kameras attēla krāsa: profils un krāsu temperatūra, piesātinājums / kontrasts, spilgtās zonas. |
| `run_camera_white_balance` / `set_camera_white_balance` | `camera_white_balance_once` / `set_camera_white_balance` | Vienreizējais baltā balanss vai automātiskais / izslēgts / fiksēti koeficienti. |
| `list_camera_presets` / `apply_camera_preset` | `camera_presets` / `apply_camera_preset` | Kameras iestatījumi. |
| `list_filter_models` | `camera_filter_models`, `camera_filter_model_info` | Filtru modeļi vai viens pēc nosaukuma. |
| `get_camera_firmware_status` / `update_camera_firmware` | `camera_firmware_status` / `update_camera_firmware` | Programmatūras versijas un zibspuldzes iestatījumi (pēc noklusējuma — uzdevums). |
| `get_camera_power_info` / `set_camera_power` | `camera_power_probe`, `sleep_camera`, `wake_camera` | Enerģijas un termiskais stāvoklis; miega režīms vai aktīvais režīms. |
| `calibrate_camera_with_target` | `calibrate_camera_with_target` | Koeficienti no redzamā kalibrēšanas mērķa. |
| `get_network_adapters` / `fix_network_settings` | `network_adapters` / `fix_network_settings` | Kameras tīkla adapteri, novērtējums un labojums (Tikai administratoram). |
| `apply_vignette_correction` | `apply_vignette_correction` | Attēlu mapes vinjetes korekcija. |
| `read_dls` | `read_dls` | Viens DLS nolasījums. |
| `probe_system` / `get_system_info` | `probe_system` / `system_info` | Vērtsaistītāja aparatūra un darbības laiks. |

**Pagaidu kameras, izlīdzināšanas profili un indeksi** ([Pagaidu masīvi, izlīdzināšanas profili un indeksi](#transient-arrays-alignment-profiles--indices)). Katrs šeit minētais rīks, kas atver kameras, izmanto `keep_settings` / `clear_settings` (noklusējuma vērtība ir „false”): skatiet [Kameras atmiņa](#camera-memory--keep_settings--clear_settings).

| Rīks | SDK-izsaukums | Funkcija |
| --- | --- | --- |
| `start_camera_continuous` / `start_multi_camera_stream` | `start_camera_continuous` / `start_multi_camera_stream` | Ieraksta vienu kameru vai sinhroni pārraida vairākas kameras kā uzdevumu (`duration_s` to aptur automātiski). |
| `capture_cameras_transient` | `capture_cameras_transient` | Viena sinhronizēta ierakstīšana bez masīva pieslēgšanas. |
| `test_array_sync` / `detect_array_master` | `test_array_sync` / `detect_array_master` | Trigera sinhronizācijas pārbaude un informācija par to, kuras kameras atrodas sinhronizācijas līnijā. |
| `analyze_array_models` | `analyze_array_models` | Kādu kameru masīvu no šiem modeļiem varētu izmantot šajā tīklā. |
| `calibrate_alignment_profile` | `calibrate_alignment_profile` | Izrēķināt un saglabāt izlīdzināšanas profila failu (`options` satur precīzākas risinājuma opcijas). |
| `capture_aligned_multiband` / `start_aligned_stream` | `capture_aligned_multiband` / `start_aligned_stream` | Izlīdzināts daudzjoslu attēls vai to plūsma kā uzdevums. |
| `read_alignment_profile` / `reorder_alignment_profile` | `alignment_profile_info` / `reorder_alignment_profile` | Profila faila saturs; tā joslu secība un ieslēgtās kameras. |
| `list_index_presets`, `compute_index`, `capture_live_index` | `index_catalog`, `compute_index`, `capture_live_index` | Indeksa iestatījumi un gradienti; indekss daudzjoslu TIFF failam; uzņemšana un indeksēšana vienā solī. |

**Laika sinhronizācija, projekta mape un receptes** ([PTP Grandmaster](#ptp-grandmaster--time_sync_status), [Projekta mape un receptes](#project-folder--recipes))

| Rīks | SDK izsaukums | Funkcija |
| --- | --- | --- |
| `get_time_sync_status` | `time_sync_status`, `time_sync_peers`, `time_sync_cameras` | PTP galvenā servera stāvoklis, tā vienādranga serveri un katras masīva kameras PTP stāvoklis. |
| `restart_time_sync` / `set_time_sync_priority` | `restart_time_sync` / `set_time_sync_priority` | Pārstartējiet to; iestatiet tā prioritātes. |
| `get_project_folder` / `set_project_folder` / `reset_project_folder` | `project_folder` / `set_project_folder` / `reset_project_folder` | Mape, kurā tiek izveidoti jauni projekti. |
| `run_project_recipe` | `run_project_recipe` | Izpildiet saglabāta projekta recepti kā uzdevumu ar tā žurnālu. Vispirms tiek veikta pārbaude: recepte ar kļūdu tiek noraidīta un nekas netiek palaists; par nepieslēgtiem masīviem / sensoriem tiek parādīts brīdinājums (tie netiek pieslēgti: vispirms tiek izpildīts `connect_project_devices`). `dry_run` pārtrauc pārbaudi un neko nepalaista. |

Nav rīka, ar ko atvērtu saglabāto projektu apstrādei: katrs apstrādes uzdevums sākas ar `create_project` (vai `process_folder`).

### Prasības

- **Chloros jābūt instalētam tajā pašā datorā.** Apstrādi veic Chloros backend. Serveris var izmantot arī backend, kas sasniedzams caur tuneli (`--backend-url` kopā ar `--no-auto-start`, skatīt [Opcijas](#options)).
- **Jābūt pieteicies šajā datorā**, izmantojot Chloros lietotni vai ar `chloros-cli login`. Serveris nevar pieteikties jūsu vietā: rīks, kas tiek izsaukts, ja esat atslēdzies, atbild „Nav pieteicies Chloros”, norādot backend iemeslu, un palīgs jums to paziņo.
- **Jebkurš maksas „Chloros+“ līmenis (Copper vai augstāks).** Serverim ir SDK piekļuve, bet bezmaksas „Iron“ līmenim tā nav: rīks, kas izsaukts bezmaksas līmenī, atbild „Nav pieejams šajā Chloros plānā“, norādot backend iemeslu.
- Python 3.7 vai jaunāka versija ar SDK un MCP klients, kas palaiž vietējos (stdio) serverus.

### Darbība

- **Aizmugurējais serveris.** Servera palaišana vai tā rīku saraksta parādīšana neko neuzsāk; pirmais rīka izsaukums izveido savienojumu. Ja darbojas Chloros lietotne (vai Linux aizmugurējā servera pakalpojums), serveris izmanto tās aizmugurējo serveri un ļauj tam turpināt darboties. Ja neviens backend nedarbojas, serveris palaista instalēto, bez loga. Kad serveris beidz darbu (jūs aizverat klientu vai tas pārstartē serveri), tas aptur uzsāktos attēlu uztveršanas uzdevumus un gaismas sensora ierakstus, kā arī atbrīvo savienotās ierīces; backend turpina darboties, tādējādi nepabeigta apstrāde turpinās līdz galam, un tas pats izslēdzas pēc 30 minūtēm, ja to nekas neizmanto. Ar `--no-auto-start` serveris nekad nepalaiž backend: rīka izsaukums, ja neviens backend nedarbojas, lūdz jums atvērt Chloros lietotni, un nākamais izsaukums pēc tam, kad to izdarāt, izveido savienojumu.
- **Viens atvērts projekts.** Aizmugurējam procesam ir viens atvērts projekts, kas tiek kopīgi izmantots ar Chloros lietotni, kad tā ir atvērta, tāpēc nedrīkst strādāt ar citu projektu lietotnē, kamēr palīgs darbojas.
- **Mainās tikai nodotie iestatījumi.** `configure_processing` atstāj katru iestatījumu, kas nav norādīts, tā saglabātajā vērtībā (SDK `configure()` atiestata debayer, vinjetes korekciju, atstarošanas kalibrāciju, PPK un eksporta formātu uz to noklusējumiem, ja tie nav nodoti; serveris tos aizpilda no projekta). Ja tas nevar nolasīt projekta saglabātos iestatījumus, tas neko nemaina un par to paziņo, ja vien nav norādīti visi pieci. `indices` aizstāj projekta indeksu sarakstu. Rezultātā tiek uzskaitīts tas, kas tika nosūtīts kā `settings_sent`, un `warnings` nosauc visus indeksus, kurus kameras joslas nevar izveidot.
- **Apstrāde notiek pēc principa „sāc un pārbaudi”.** `start_processing` atgriežas, tiklīdz sākas apstrāde, kurai pēc tam nepieciešamas minūtes vai stundas, un palīgs tam seko ar `get_processing_status`. `state` ir `idle` (nav apstrādes), `starting` (aizmugurējā sistēma vēl nav ziņojusi par izpildi, līdz pat 20 s), `processing` vai `finished`, ar izpildes [kopsavilkums](#post-run-summary--hints): kas tika eksportēts, kā arī norādes, ja eksportētais apjoms ir mazāks nekā gaidīts (nav attēlu, nav atrasts kalibrēšanas mērķis). Rezultāti tiek saglabāti projekta mapē, tāpat kā lietotnē. `start_processing` tiek noraidīts, kamēr darbs ir procesā, un aizmugurējā sistēma, kas pārstāj atbildēt, tiek ziņota kā kļūda, nevis kā pabeigts darbs.
- **Ko redz palīgs.** SDK brīdinājumi parādās rīka rezultātos zem `warnings`, bet kļūdas (izrakstīšanās, plāns, nepareizs ceļš) — kā kļūdas teksts, ko palīgs var nolasīt un pārsūtīt jums. Saraksts, kurā ir vairāk nekā 20 vienību, tiek saīsināts līdz pirmajām 20 vienībām, bet tā pilnais garums tiek norādīts kā `<key>_total`. Ceļiem jābūt absolūtiem.
- **Ierīces tiek koplietotas.** Kameras, matricas un gaismas sensori atrodas backendā, tādēļ palīgs var vadīt vienu no tiem, izmantojot savienoto Chloros lietotni, un izmaiņas tiek parādītas lietotnē. `disconnect_device` parāda, ar ko ir savienots serveris; ierīci, ar kuru ir savienojies cits klients (lietotne, `chloros-cli`, skripts, cits `chloros-mcp`) tiek atbrīvota tikai ar `force: true`, kas ir SDK `force_disconnect`, un atteikuma gadījumā tiek norādīts, kurš to savienoja, ja backend to zina; ierīce, kuru backend vispār neuzglabā, tiek ziņota kā nepieslēgta (`released: false`, `connected: false`, nav ko atbrīvot). Ieraksts vai straume, ko atskaņo backend, rada savu `get_job_status` skatu ar `counts` — `frames_written`, `frames_dropped`, `frames_failed`, `drop_reason` (kadru grupas daudzkameru straumēšanai) — un vienrindas `summary` („ierakstīti 220 kadri, 55 izlaisti, 0 kļūdas: ...”), kamēr tas darbojas, un, pabeidzot, tā galīgos skaitļus; cita klienta saraksts par to (`other_clients_jobs`) satur to pašu (`list_cameras()`, `list_arrays()` un `list_daq_sensors()` katrai ierīcei`connected_by`: `client`, `label`, `name`, `at`). Katra `chloros-mcp` sevi paziņo backendam kā `chloros-mcp (pid N)` (`chloros_sdk.connect.CLIENT_LABEL`, nosūtīts kā `X-Chloros-Client-Label`; jebkurš SDK skripts to var iestatīt). Cita klienta gaismas sensora ierakstīšanas apturēšanai ir nepieciešams arī `force`.
- **Ilgstošs darbs ir uzdevums.** `start_capture_run`, `start_array_recording`, `build_burst_video`, `start_light_sensor_recording`, `start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`, `run_project_recipe`, un jebkurai pieprasītajai fiksēšanai, risināšanai vai indeksēšanai `background` nekavējoties atgriež `job_id`; tam seko `get_job_status`, un `stop_job` to pabeidz to. Darbības cikls turpinās, līdz tiek apturēts, ja vien jūs neiedodat `max_captures`, `duration_s` (vai, ja tas ir pārraides cikls, `max_frames`); `min_free_gb` atsakās sākties, ja diskā ir mazāk brīvās vietas. Netiek piemērots nekāds ierobežojums, ja vien jūs tādu neuzstādāt. Ieraksts vai straume, ko izpilda backend (`start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`) saglabā backend procesa identifikatoru, tādējādi cits `chloros-mcp` — kas palaists vēlāk — to atrod pēc šī identifikatora: `get_job_status` to pārņem (un uzskaita šādus uzdevumus kā `other_clients_jobs`, sākot no `GET /api/camera/cli-jobs`, katram ar `started_by`), un `stop_job` to aptur tikai ar `force: true`. Jebkurš cits uzdevums (uzņemšanas cikls, masīva ierakstīšana vai sērija, fona uzņemšana) darbojas tajā `chloros-mcp`, kas to sāka, un tikai šis serveris to zina: cits serveris, kas to pieprasījis, to norāda. Pārtraukts darbs nolasa `stopping`, kamēr tas izraksta savu rindu, pēc tam `finished` ar galīgajiem skaitļiem, 10 minūtes, un no jebkura `chloros-mcp`: tas, kas to uzsāka, redz cita servera piespiedu apstādināšanu tādā pašā veidā. `get_job_status` nekad negaida apstādināšanu: tās laikā (savas `stop_job`, cita klientavai apstāšanās, kas atbrīvo kameras no uzdevuma, kas beidzies pats no sevis), tas uzreiz atbild ar `stopping` un reāllaika skaitļiem, un `stop_job` no uzdevuma, kas jau apstājas, to norāda. Uzdevums, kas beidzies, norāda iemeslu `stop_reason`: savu iemeslu (`no_frames`, `disk_full`, `member_missing`, vai masīva nepārtrauktas darbības gadījumā — `count`, `duration`, `array_gone`), `limit reached` — tikai tad, ja tā skaitļi norāda uz `count` (vai sērijas `max_frames`, ieraksta`duration_s`) sasniegts, `error`, `stopped` (ar `stopped_by`), ja klients to apturējis, citādi `finished`.
- **Kur tiek saglabāti ieraksti.** Uz atvērtā projekta mapi `captures`, kur lietotne tos saglabā, ja vien nav norādīts `output_dir`.
- **Attēli.** `get_camera_preview` un `grab_frames` atgriež attēlus, kurus palīgs var apskatīt, papildus to JSON datiem. `grab_frames` attēliem kontrasts ir palielināts apskatei; to statistikas rādītāji atspoguļo reālās vērtības.
- **Kad serveris tiek izbeigts**, tā uzsāktie uzdevumi tiek apturēti un tiek slēgta gaismas sensora ierakstīšana, ko tas bija uzsācis. Ja serveris bija uzsācis aizmugurējo sistēmu, tas pirms aizmugurējās sistēmas apturēšanas atbrīvo arī ierīces, ar kurām bija izveidojis savienojumu; lietotnes aizmugurējā sistēmā tās paliek savienotas.
- **Protokols.** Tikai stdio (bez HTTP-galapunkta), tikai rīki (bez resursiem vai uzvednēm), teksta un attēlu rezultāti, MCP protokola versijas no 2024. gada 5. novembra līdz 2025. gada 25. novembrim.**Kas nav iekļauts.** Piesakšanās vai izrakstīšanās (konts pieder jums; serveris nevar piesakīties) un tāda backend apstādināšana, kuru serveris nav uzsācis. Visam pārējam, ko šis SDK dara saistībā ar apstrādi un aparatūru, ir atbilstošs rīks. (DAQ-E konfigurēšana — tā nosaukums, autentifikācijas žetons, OTA parole, paraksta prasība, rūpnīcas iestatījumu atjaunošana — un tiešie DAQ serveri ir tikai `chloros-cli`, kas atrodas arī SDK: nepareizs nosaukums vai žetons bloķē katru Chloros instalāciju, neļaujot tai darboties ierīcē.)

---

## Aizmugurējā procesa automātiska palaišana

„Smart-connect” ieejas punkti — `connect_camera`, `connect_array`, `connect_daq_sensor`, `discover_lattice_cameras` un `discover_daq_sensors` — ir vienkārši HTTP klienti, kas pieņem, ka backend klausās uz `127.0.0.1:5000` (viedās savienošanas saskarnes noklusējuma URL). Ja GUI vai CLI jau darbojas, tāds jau ir. No vienkārša skripta tas var nebūt — tādēļ šīs funkcijas **automātiski palaista komplektā iekļauto backend bināro failu** (bez loga, tāpat kā to dara `ChlorosLocal`) pirms pirmā izsaukuma, pēc tam gaida līdz 60 s, kamēr tas sāks darboties. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` un `analyze_array_network()` to nekad nepalaiž.

Noteikumi:

- **Tiek palaists tikai lokāls URL.** `backend_url`, kas norāda uz `localhost` / `127.0.0.1` / `[::1]` ir atbilstošs; jebkurš cits hosts tiek uzskatīts par citas personas datoru un nekad netiek palaists.
- **Ja backend beidz darbu, pirms ir atbildējis, gaidīšana tiek pārtraukta.** Izsaukums nekavējoties tiek pārtraukts, norādot iziešanas kodu, izpildīto komandu un pēdējās rindas, ko backend izvadīja, nevis gaidot 60 sekundes. Izņēmums ir iziešanas kods 98: cits backend jau ir ieņēmis portu vai bloķēšanas failu (Chloros lietotnes, backend pakalpojuma vai cita skripta, kas vēl sāk darboties), tāpēc izsaukums turpina gaidīt, kamēr tas atbild. `ChlorosLocal` darbojas tāpat.
- **Backend tiek atstāts darbojošs atkārtotai izmantošanai** (tāpat kā CLI) — skriptam beidzoties, netiek veikta implicīta izslēgšana. Atkārtoti palaistot skriptu, tiek atkārtoti izmantots aktīvais backend.
- **Atteikties no šīs funkcijas ar `auto_start_backend=False`** jebkurā no šiem izsaukumiem (piemēram, ja esat norādījis uz attālo backend vai pats pārvaldāt backend dzīves ciklu).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Ja komplektā iekļauto bināro failu nevar atrast vai palaist, nākamais HTTP izsaukums izraisa rīcībai piemērotu, **platformai atbilstošu** `ChlorosConnectError` kļūdu, nevis vienkāršu savienojuma atteikuma izsekojumu — Windows sistēmā tas norāda uz darbvirsmas lietotni vai komandu `chloros-cli`; Linux sistēmā (bez grafiskās lietotāja saskarnes) tas norāda uz komandu `chloros-cli` vai `.deb`.

---

## Vide un galvenes

SDK katru aizmugures HTTP izsaukumu atzīmē ar `X-Chloros-Client: sdk`. Aizmugure piemēro SDK/CLI licencēšanas noteikumus (nepieciešama pieteikšanās **un** maksas Chloros+ plāns), nevis GUI bezmaksas līmeņa ceļu. Tas tiek iestatīts automātiski importēšanas brīdī — jums nekas nav jādara.

Galvene tiek pievienota katram `requests` / `urllib` izsaukumam, kura URL satur `localhost` vai `127.0.0.1`, jebkurā portā — ieskaitot jūsu pašu vietējos pakalpojumus — un aizstāj vērtību, ko esat iestatījuši paši. Aicinājumi uz citiem serveriem paliek nemainīgi.

Pārrakstiet aizmugures URL, nododot `backend_url=` (`ChlorosLocal` pieņem `backend_url=` vai `api_url=`; `process_folder()` un `process_lattice_capture()` pieņem tikai `api_url=`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(Instalētais backend klausās tikai uz loopback, tādēļ LAN `backend_url` nevar to sasniegt; skatiet [Remote-Backend Mode](#remote-backend-mode-pip-only-host-via-tunnel), lai uzzinātu par tuneļa konfigurāciju.)

---

## Versiju pārvaldība un saderība

- SDK versija tiek norādīta kā `chloros_sdk.__version__`.
- SDK pinu darbība ir atkarīga no komplektā iekļautās backend versijas. Vecākas SDK versijas lietošana kopā ar jaunāku backend parasti darbojas (uz priekšu saderīgi galapunkti), taču jaunākas SDK versijas lietošana kopā ar vecāku backend var izraisīt `404` kļūdas jaunajos galapunktos — atjauniniet darbvirsmas lietotni, lai tā atbilstu.
- „Smart-connect” saskarne (`connect_camera` / `connect_array` / `connect_daq_sensor`) un tīkla analīzes galapunkts atgriež stabilas JSON-shēmas; jauni lauki tiek pievienoti.

---

## Problēmu novēršanas norādes

- **`ChlorosAuthenticationError: Login required`** (no `ChlorosLocal`; „Smart-Connect” izsaukumi izraisa kļūdu `ChlorosConnectError: Login required. …` tā paša iemesla dēļ) → Vienu reizi palaidiet `chloros-cli login EMAIL PASSWORD` šajā datorā vai piesakieties, izmantojot Chloros darbvirsmas lietotni.
- **`ChlorosProcessingError: Failed to start processing: … The project folder is too deep for this computer …`** (vai `Failed to create project: …`, kas izriet no `create_project()`) → šajā datorā ir atspējoti garie Windows ceļi (Windows noklusējuma iestatījums), tāpēc neviens ceļš nedrīkst pārsniegt 260 rakstzīmes, un produkti atrodas aptuveni 130 rakstzīmes zemāk par projekta mapi. Nekas netika apstrādāts. Ziņojumā norādīta garākā projekta mape, kas atbilst prasībām: izmantojiet īsāku vai ieslēdziet Windows garos ceļus un pārstartējiet Chloros. Windows instalētājs piedāvā šo opciju kā „Iespējot Windows garos ceļus”, kas pēc noklusējuma ir atzīmēta; citādi izmantojiet grupas politiku: *Datoru konfigurācija > Administratīvie šabloni > Sistēma > Failu sistēma > Iespējot Win32 garos ceļus*.
- **`ChlorosConnectError: No Chloros backend is running …`** → „Smart-connect” izsauc vietējā backend automātisko palaišanu, tāpēc šī kļūda parādās tikai tad, ja nevar atrast/palaižot komplektā iekļauto bināro failu (piemēram, pip-hosts bez darbvirsmas pakotnes). Ziņojums ir atkarīgs no platformas: Windows sistēmā atveriet darbvirsmas lietotni vai palaidiet jebkuru `chloros-cli` komandu; Linuxā palaidiet komandu `chloros-cli` (nav grafiskās saskarnes) vai instalējiet `.deb`. Attālinātajam backendam nododiet `backend_url=` (un `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** importēšanas brīdī → `lattice_sdk` neizdevās ielādēt: Arena SDK izpildes vide vai OpenCV (`cv2`) šajā Python versijā nav importējams. Aizmugurējās sistēmas vadītā virsma (`connect_*`, projekti caur aizmugurējo sistēmu, `ChlorosLocal`) joprojām darbojas.
- **Masīvs savienojas ar pilnu sensoru, bet zemā kadru ātrumā**→ Tā ir paredzēts: automātiskais režīms saglabā pilnu sensoru un ļauj ātrumam samazināties līdz tam, ko spēj pārraidīt savienojums. Lai iegūtu vairāk kadru, izmantojiet `binning=2` / `4` vai mazāku `width` / `height`, lai iegūtu vairāk kadru sekundē, vai arī ieslēdziet jumbo kadrus / uzlabojiet savienojumu. Binning un ROI**ne** novērš kopējo pārslogojumu (`oversubscribed: true`, fps lauki 0): pārāk liels kameru skaits attiecībā pret vadu nevar tikt novērsts ar binning/ROI — samaziniet kameru skaitu, aktivizējiet jumbo rāmjus vai pārejiet uz ātrāku tīkla karti (skatiet [Pārslogotība](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` ziņo, ka tīkla kartes RX gredzens ir ļoti mazs (~0,26 MB) / savienojumu vārti ar brīdinājumu „FRAMES WILL DROP”** → Tīkla kartes saņemšanas gredzens ir noklusējuma iestatījumos (bieži tiek atiestatīts uz 32 pēc tīkla kartes draivera atjaunināšanas). Realtek USB 10GbE adapterī iestatiet `ReceiveBufferLen=256` un `PendingReceives=64` (ar paaugstinātām tiesībām), pēc tam pārstartējiet backend, lai tas atkārtoti nolasītu gredzenu. Pilnā procedūra: [CLI atsauces → Galvenā tīkla kartes iestatīšana un optimizēšana](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Galvenais dators „iekārtojas” pārstartēšanas/izslēgšanas laikā, vēlāk rodas WMI kļūdas `Invalid class` / tīkla karte neieslēdzas** → Novecojis USB 10GbE draiveris izraisa kļūdu `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atjauniniet adaptera draiveri uz aktuālo versiju (≥ 2026) un atkārtoti piemērojiet saņemšanas gredzena iestatījumus. Skatīt [CLI atsauces → Host NIC iestatīšana un optimizēšana](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Masīva uztveršana neizdodas ar HTTP 503 kļūdu „Nav jaunas sinhronizētas rāmju grupas”** → Masīvs gaidīšanas laikā nepublicēja nevienu jaunu rāmju grupu. Pārbaudiet `arr.status()`: `stalled: True` (vai `streaming: False`) kopā ar `actual_fps` — ja 0, tas nozīmē, ka uztveršanas cikls ir pārtraucis publicēšanu — atvienojiet un atkārtoti pieslēdziet masīvu. Ja `stalled` ir „False” un `actual_fps` ir lielāks par 0, mēģiniet vēlreiz. Tieši pēc `connect_array()` uztveršana jau gaida līdz 20 s uz pirmo grupu.
- **`status()` parāda `link_speed_mbps: 4294`** → Windows nevarēja nolasīt adaptera ātrumu; tas ir tipiski USB Ethernet adapteriem un nav kļūda. Skatīt [Paredzamie kadru ātrumi](#expected-frame-rates).
- **`update_firmware()` neizdodas ar `device never connected back on TCP <port>`** → Ugunsmūris bloķēja DAQ-E savienojumu ar šo datoru. Windows instalētājs pievieno šo noteikumu (skatiet [DAQ-E ierīces iestatījumi](#daq-e-device-settings)); ja izmantojat citu ugunsmūri, atļaujiet ienākošos TCP savienojumus uz serveri no sensora apakštīkla.
- **Atstarošanas mērījums atteikts** → Lai iegūtu reflektances vērtības absolūtā mērogā, aktīvam DAQ jābūt piesaistītam kamerai (vai sensoru masīvam). Piesaistiet to, izmantojot grafisko lietotāja saskarni, vai izmantojiet `processing="radiance"` (W/m²/sr/nm), kam nav nepieciešams pāri savienots sensors.
- **`smart=True` datu ieguve ilgst ilgāk nekā gaidīts** → AE konverģence ir atkarīga no ainas dinamikas. Izmantojot `ArrayHandle.capture_smart()`, paplašiniet `exposure_tolerance_pct`, saīsiniet `stability_window_s` vai samaziniet `settle_timeout_s`, lai iegūtu ātrāku, mazāk stabilu izraisītāju.

---

## Skatīt arī

- [CLI atsauces materiāls](cli-reference.md) — katra CLI apakškommanda atbilst kādam SDK izsaukumam.
- [DAQ gaismas sensori](https://mapir.gitbook.io/chloros/daq-light-sensors) Chloros rokasgrāmatā — sensori, to ierobežojumi un kalibrētais diapazons, ierakstīšana, DAQ-E tīkla savienojumi.
- [LATTICE kameras](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) Chloros rokasgrāmatā — pieslēgšana, kameru iestatījumi, uzņemšanas režīmi, masīvi.
- Tiešsaistes dokumentācija: `https://mapir.gitbook.io/chloros/api-python-sdk`
