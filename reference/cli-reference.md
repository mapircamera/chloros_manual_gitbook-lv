# Chloros CLI atsauces rokasgrāmata

**Versija:**

1.3.0**Izveidots:**2026-07-29 19:19 ·**Pārskatīts:** 2026-11-06**Mērķauditorija:** Optimizēts LLM lietošanai; cilvēkam saprotams.**Darbības joma:** Visas `chloros-cli` lietotājam pieejamās apakškommandas, ar opcijām un piemēriem, kurus var kopēt un ielīmēt.

Šis dokuments ir pilnīga atsauces rokasgrāmata par komandrindas rīku `chloros-cli`, kas iekļauts MAPIR Chloros. Tas ir apzināti izsmeļošs, lai LLM (vai cilvēks) varētu izveidot jebkuru atbalstītu darba plūsmu, izmantojot zemāk sniegtos sarakstus, neizskatot avota kodu.

Ja jums nepieciešama tikai galvenā informācija, pārejiet uz:
- [Piecu minūšu ātrsākums](#five-minute-quickstart)
- [LATTICE kameras pirmās savienošanas darba plūsma](#lattice-camera-first-connect-workflow)
- [DAQ sensora pirmās savienošanas darba plūsma](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Uztveršanas režīmi, ierakstītāji un pārstrāde bezsaistē](#capture-modes-recorders--offline-reprocess)

> **Vai vadāt Chloros ar AI palīgu?** „Claude Desktop”, „Claude Code”, „Cursor” un citi MCP klienti var vadīt apstrādi, „LATTICE” kameras un matricas, kā arī DAQ gaismas sensorus, izmantojot `chloros-mcp`, kas ir MCP serveris, kurš tiek instalēts kopā ar Python SDK (kā arī ar Linux .deb failu, kas atrodams `/usr/bin`): `claude mcp add chloros -- chloros-mcp`. Konfigurācija, opcijas un rīki ir atrodami SDK atsauces sadaļā [MCP Server (AI Assistants)](sdk-reference.md#mcp-server-ai-assistants). Palīgs ar komandu rindu var arī tieši izpildīt jebkuru komandu šajā lapā.

> **Izmaiņas versijā 1.3.0** (ko pamanīs esošs 1.2.0 skripts vai darba plūsma).
>
> **Izmaiņas, ar kurām 1.2.0 skriptam var būt nepieciešams rīkoties:**
>
> - **Tiek piemērots `--preset`.** `lattice capture`, `continuous`, `calibrate`, `multi-capture`, `multi-stream`, `align-calibrate`, `align-apply` un `align-stream` tika izņemti 1.2. versijā.0 un darbojās ar kameras pašas iestatījumiem. Tagad tas nosaka iestatījuma formātu, ekspozīciju, pastiprinājumu un kadru ātrumu: `--preset high_speed` uzņem 8 bitu attēlus ar 5 ms, 6 dB, 60 kadriem sekundē. `lattice cam-connect --preset` arī saglabā iestatījuma formātu (versijā 1.2.0 kamera pēc iestatījuma piemērošanas tika iestatīta uz 12 bitu). Skatīt [Vispārīgās opcijas](#common-options-shared-by-most-subcommands).
> - **Bayer pikseļu formāts M3M (mono) kamerā ir tās dziļums.** Iestatījumi nosauc Bayer formātus, kurus mono sensors nevar uzņemt: 1.2.0 versijā kamera noraidīja formātu un saglabāja esošo, tāpēc `--preset high_speed` tur nebija 8 bitu. Tagad tas ir mono formāts ar tādu pašu dziļumu: `high_speed` un `--pixel-format BayerRG8` dod `Mono8`, `high_quality`, `triggered` un `BayerRG12` dod `Mono12`. Jauktā M3C + M3M masīvā katra kamera izmanto kopīgo formātu savā ģimenē, un `network-analysis` / `analyze-array --pixel-format` katru kameru attēlo šajā dziļumā. Tas pats attiecas arī otrādi: M3C, kam pieprasīts Mono formāts, izmanto šīs dziļuma Bayer formātu, tādējādi masīvs, kura galvenā kamera ir M3M, vairs neatver savas M3C kameras Mono režīmā. Skatīt [Mono (M3M) vienfrekvences kameras](#mono-m3m-single-band-cameras).
> - **`lattice array-capture` eksporti pēc noklusējuma ir izlīdzināti, tāpat kā lietotnes eksporti.** Katra dalībnieka `debayered`, `display`, `radiance`, `reflectance` un indeksa faili tiek deformēti atbilstoši galvenās kameras kadram un apgriezti līdz dalībnieku pārklāšanās zonai (kas ir mazāks par sensoru par kameru nobīdēm, `XMP-mapir:AlignmentJSON` `"applied": true`), tādējādi CLI uzņemums sastāv no tiem pašiem failiem, kas Chloros lietotnes uzņemumā no tās pašas ainas; `raw` saglabājas oriģinālajā formātā. Agrāk uzņemums bez `--aligned` saglabāja katru failu oriģinālajā izmērā, neizkropļotu. `--no-aligned` to saglabā. Tas pats noklusējums attiecas uz `--smart`, `--continuous` un `--interval`, kā arī uz SDK un `chloros-mcp`. Skatīt [`array-capture` režīmi](#array-capture-modes).
> - **`lattice array-capture --compression` noklusējuma iestatījums ir `none`.** Faili tiek ierakstīti nesaspiesti (aptuveni 6,3 MB uz vienu pilnas izšķirtspējas kadru), lai nodrošinātu visātrāko ilgstošo ierakstīšanas ātrumu; versija 1.2.0 ierakstīja `deflate` (aptuveni 4,1 MB). Lai iegūtu mazākus failus, norādiet `--compression deflate`. Abas opcijas ir bez zudumiem.
> - **PNG un JPEG atbalsta tikai `display` / `preview` un indeksētos produktus.** `--format png` vai `jpg` ierakstīšana ar līmeni, ko 8 bitu fails nevar ietilpināt, tiek noraidīta, pirms kaut kas tiek ierakstīts (HTTP 400, iziet no `1`), tāpat kā noklusējuma `--processing all` ar `--format png`/`jpg`. Skatīt [Iegūšanas eksporta līmeņi](#capture-export-levels-the-all-default).
> - **`lattice dls` ir nepieciešams `--mac`.**Vairs nav noklusējuma MAC (tas bija vienas vienības adrese); `daq pool-discover --only ble` atrod sensora adresi.**`lattice dls --camera SERIAL`** ir jauns.
> - **Vairākas kļūdas izraisa `1` iziešanu.** `process`, ja nav iespējams pārbaudīt darbības eksportētos datus vai ir zaudēts attēls, lai gan pārējie produkti tiek ierakstīti; uzņemšanas komanda ar jebkuru failu, kuru nevarēja ierakstīt (pārējie faili joprojām tiek saglabāti); `lattice continuous` un `multi-stream`, ja disks ir pilns; `array-burst` / `array-record`, ja ierakstītājs ziņo par kļūdu; `array-build-video`, ja pieprasītais produkts tika izlaists vai netika izveidots atbilstoši pieprasījumam; `project burst` / `stream`, ja ierakstīšana daļēji neizdodas; `project sensor log`, ja ierakstā nav nekādu rādījumu; `lattice exposure --set`, `gain --set` un `configure --set` ar vērtību, ko kamera noraida. `lattice network-analysis --json` beidzas tāpat kā kopsavilkums (`1` attiecībā uz `needs_force_slip`). Pretēji: `daq pool-disconnect` no sensora, kas vairs nav pūlā, iziet `0`X. Skatīt [Izbeigšanas kodi](#exit-codes).
> - **Lēns `lattice array-connect` nekad neizraisa kļūdu, ja masīvs ir pieslēgts.** Tas gaida līdz 600 s (180 s versijā 1.2.0; `project connect` 90 s), pakāpeniski izvadot aizmugurējās sistēmas darbības posmus, un savienojums, kas līdz tam brīdim nav izveidots, tiek atcelts ar aizmugurējās sistēmas palīdzību — katra kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta — tādējādi `Error:` (iziešana `1`) nozīmē, ka masīvs nav pieslēgts. Versijā 1.2.0 savienošana tieši pēc kameru ieslēgšanas varēja pārsniegt gaidīšanas laiku un savienot masīvu pēc tam, kad komanda bija cietusi neveiksmi. Tagad Ctrl+C atceļ savienošanu. Skatīt [Pastāvīgi savienojumi](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-connect` saglabā pilnu sensoru.** Ja nav `--width` / `--height` / `--binning`, katra kamera tiek iestatīta uz pilnu sensoru, un kadru ātrums samazinās līdz tam, ko nodrošina šis dators un tīkls; savienošanas izvade uzrāda ātrākas iespējas (attēlu apvienošana vai mazāks ROI) ar to prognozētajiem rādītājiem. Versijā 1.2.0 kadri tika samazināti vai apvienoti, lai saglabātu kadru ātrumu. Skatīt [`array-connect` opcijas](#array-connect-options).
> - **Lēns tīkls vairs neatsaka masīvu.** Katras kameras darbība tiek pielāgota tās tīkla joslas daļai, un kadru ātrums samazinās atbilstoši tam. Ja kameru darbību nevar pielāgot tik lēni, masīvs darbojas sērijveida izlaides režīmā (`slip-emit-and-capture`) režīmā: katrs kadrs tiek saņemts, bet kameras netiek eksponētas vienlaikus. `--force-tier sim-capture-sim-emit` prasa vienlaicīgu ekspozīciju. Skatīt [Masīva kadru ātrums un sērijas uzņemšanas modelis](#array-fps--burst-model).
> - **`lattice network-analysis` / `analyze-array` nosaka, kāds savienojums tiek piemērots**: bez `--pixel-format`, formāti, ko nosaka savienojums (Mono12 / BayerRG12, agrāk bija BayerRG8), un lēmums par [saspiestu 12 bitu](#packed-12-bit-transport---packed). Tādējādi šī pati komanda var norādīt atšķirīgu kadru ātrumu nekā 1.2.0 versijā.
> - **Relatīvie ceļi attiecas uz vietu, kur tiek izpildīta komanda.** `-o` un pārējie ceļi, kurus atver backend (`--input`, `--burst-dir`, `--profile`, `--save`, `--mask`), receptes soļa `output`, un `lattice index --live` noklusējuma `output/` tiek atrisināti, ņemot vērā mapi, no kuras jūs palaidāt `chloros-cli`. Versijā 1.2.0 aizmugurējā sistēma tos atrisināja savā instalācijas mapē, kas uzstādītajā versijā beidzās ar kļūdu „Piekļuve liegta”. Skatīt [Konvencijas](#conventions).
> - **`lattice index` darbojas backendā jebkurā formā**, tāpat kā pārējās `lattice` komandas, tāpēc vispirms palaidiet backend. Offline indeksu (`--input`) aprēķina backend, kas nolasa TIFF failu un raksta rezultātus blakus tam vai `-o`, un `--list-presets` / `--list-gradients` izdrukā backend tabulas. Windows sistēmā tagad darbojas šādas komandas: iepriekš Windows CLI atbildēja uz jebkuru `lattice index` formātu, ieskaitot `--live`, ar `Error: lattice_sdk not available`. Linux vidē offline indekss agrāk darbojās CLI vidē bez backend un bez pieteikšanās; tagad tam ir nepieciešams gan backend, gan pieteikšanās, tāpat kā visām pārējām CLI komandām. Skatīt [Indekss / Veģetācijas matemātika](#index--vegetation-maths).
> - **Jauni faili blakus produktiem.** Katram starojuma, atstarošanas un indeksa produktam, kurā joslas atdalīšana ir ierobežojusi pikseli, tiek pievienota maska mapē `clamp_masks/` blakus produkta mapei, un izdrukā tiek iekļauta rinda `clamp mask:`. Skatīt [Unmix ierobežojuma maska](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on).
> - **Kameras orientācija.** Uzņemot ar vienu kameru, tiek saglabāti faili `raw` un `debayered` tādā veidā, kā tos nolasījis sensors, un kameras apgriešana/pagriešana tiek reģistrēta XMP failā; `process` to piemēro izgatavotajiem produktiem. Masīva elementu faili paliek sākotnējā formātā, ja vien nepārsūtāt jauno `--orient` (`array-capture`, `array-burst`, `project capture` / `burst` / `stream`). Skatīt [Kameras orientācija](#kā-izskatās-uzņemto-attēlu-mape).
> - **Eksporta līmeņa nosaukums, kas nav viens no minētajiem, tiek noraidīts.** `lattice capture --processing` un katrs `--levels` saraksts pieņem `raw`, `debayered`, `radiance`, `reflectance` vai `display` (vai arī tāds aliass kā `preview`; `--processing`, kā arī `all`). Jebkurš cits nosaukums tiek noraidīts, pirms kamera tiek pieskārsta (HTTP 400 `UNKNOWN_LEVEL`, iziet `1`); 1.2.0 to saglabāja kā `raw` un izbeidza darbību ar `0`.
> - **`daq pool-set-cap` ir nepieciešams `--cap-id`.** Bez tā komanda iziet `2`; 1.2.0 noņēma vāciņa korekciju, atstājot atklātu sensoru. `--cap-id none` pieprasa neapstrādātu sensoru.
> - **`lattice resolution --binning-mode` vairs nav pieejams.** Binning vienmēr ir „Average“; karodziņš joprojām tiek pieņemts, parādās brīdinājums, bet nekas netiek darīts. Skatīt [Sensora regulēšana](#sensor-tuning).
> - **`process --debayer texture-aware` tiek noraidīts attiecībā uz LATTICE uzņēmumiem.** Ja mapē atrodas LATTICE uzņēmumi, `process` pēc importēšanas iziet no `1`, norādot to skaitu, pirms sāk apstrādi. LATTICE uzņēmumiem vienmēr tiek piemērota standarta demosaika, tāpēc 1.2.0 versija pieņēma šo opciju un to ignorēja; arī lietotne Chloros to šiem uzņēmumiem nepiedāvā. Skatīt [`chloros-cli process`](#chloros-cli-process).
> - **Kameras tiek atgrieztas tajā stāvoklī, kādā tās tika atrastas — ja vien nav norādīts `--keep-settings`.** `lattice array-disconnect`, katra pagaidu komanda (`multi-capture` bez masīva, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`) un `lattice continuous` katru kameru atgriež tādā stāvoklī, kādā tā tika atrasta: tās slēdža un sinhronizācijas līnijas vadu savienojumus, PTP, kā arī visus kameras iestatījumus, kas tika mainīti sesijas laikā (ekspozīciju un pastiprinājumu, AE mērķi, robežas un amortizāciju, balansa iestatījumus, melnā līmeni, pikseļu formātu, binningu un ROI, apgriezienus, kadru ātrumu). `--keep-settings` savukārt saglabā katras kamerasiestatījumus kameras paša atmiņā, lai tā ieslēgtos ar tiem; `--clear-settings` atjauno šo atmiņu uz rūpnīcas iestatījumiem. Abos gadījumos kameras tiek atstātas brīvā darbībā. Projekts iestata savas kameras no saviem saglabātajiem iestatījumiem, kad tās pieslēdz. Skatīt [Kameras atmiņa](#camera-memory----keep-settings----clear-settings).
> - **Katrs režīms saglabā projektu tādā pašā veidā.** Jebkuras izmaiņas kamerā, masīvā vai gaismas sensorā, kas pieslēgts projektam — no lietotnes, CLI, SDK vai MCP servera — tiek saglabātas šī projekta `cameras.json` / `sensors.json`, un, atverot projektu jebkurā režīmā, tiek iestatītas tajā esošās ierīces. Iepriekš tikai lietotne saglabāja projektu, un nākamā saglabāšana pārrakstīja visu pārējo. Skatīt [Viens projekta ieraksts, katrs režīms](#one-project-record-every-mode).
> - **Faili, kas izveidoti no vairākām kamerām, nosauc katru no tām.** Daudzjoslu kopums (`align-apply`, `align-stream`, `index --live --save-multiband`), no vairāku kameru joslām veidots reāllaika indekss un masīva uzņemuma attēls `composite/` satur katras avota kameras sērijas numuru, modeli un kalibrēšanas paketi saraksta veidā (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …), savukārt EXIF `SerialNumber` uzskaita sērijas numurus, kas atdalīti ar komatu. Agrāk tajos vispār netika iekļauta informācija par kameru vai tika norādīts tikai pirmās kameras sērijas numurs. Skatīt [Faili, kas izveidoti no vairākām kamerām](#files-made-from-several-cameras).
> - **Tikai `raw` failā ir iekļautas kameras kalibrēšanas metadatas.** MicaSense saderīgais komplekts (`BlackLevel`, `RadiometricCalibration`, katram kanālam atsevišķais `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` komplekti) un `CalibrationJSON` datu bloks apraksta sensora neapstrādātos vērtības, tādēļ tie atrodas tikai `raw` uzņemšanas failā. Visi pārējie faili — uzņemšanas faili `debayered`, `display`, `radiance`, `reflectance` un indeksa faili, `lattice continuous` un `multi-stream` kadri, kā arī katrs `process` produkts — satur kameras identifikatoru (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) un, uzņemot attēlu, paša kadra ierakstu (`XMP-mapir:FrameJSON`: ekspozīcija, pastiprinājums, temperatūra, pikseļu formāts, uzņemšanas laiks). Versijā 1.2.0 tajos bija iekļauts arī neapstrādāto datu kopums, ko rīks, piemērojot to (Pix4D, Metashape), piemēroja pikseļiem, kas nav neapstrādātas vērtības. `process` atrod šāda faila kalibrāciju pēc tā sērijas numura un `CalBundleSha256`.
>
> **Citas izmaiņas:**
>
> - **Masīva automātiskais kadru ātrums pielāgojas tam, ko nodrošina masīvs.** Ātrums, kas zaudē vairāk nekā 1 % no saviem trigeriem, tiek samazināts, to novērtējot vairāku ilgstošu novērojumu laikā, nevis vienā, un ātrums, no kura tas tika samazināts, tiek laiku pa laikam atkārtoti pārbaudīts. Jūsu pašrocīgi iestatītais ātrums paliek nemainīgs.
> - **Automātiskā ekspozīcija ir stabilāka.** Tā saglabā nemainīgu ekspozīciju nemainīgos apgaismojuma apstākļos, pielāgojas izmaiņām dažu sekunžu laikā, ignorē nelielus pārgaismojumus un atjauno kameras darbību, ja tā pārstāj reaģēt spilgtā saules gaismā. Skatīt [Kā darbojas automātiskā ekspozīcija](#how-auto-exposure-behaves).
> - **`process` skaita tikai jūsu attēlus un ierakstus.** Atkārtota izpilde mapē, kurā atrodas projekta `camera_cal/`, vairs neskaita šos kalibrēšanas failus („Atrasti 96 attēli” 90 attēlu kopā), un projekts, kurā tie jau ir iekļauti, tos izmet.
> - **`lattice multi-test` sāk darbu no kameru ieslēgšanas brīža.** Tas ieslēdz kameru PTP darbības veikšanai, gaida, līdz to pulksteņi sinhronizējas un stabilizējas pirms pirmā izraisījuma — katra kamera sinhronizējas 120 s, aptuveni 2 minūtes pēc ieslēgšanas (`--ptp-settle SECONDS` to iestata), jo tikko sinhronizēts pulkstenis apmēram 2 minūtes var atšķirties līdz pat ~200 us — un katras kameras PTP atgriež tādā stāvoklī, kādā tas tika atrasts. Kamera, kas joprojām ir sinhronizēta no iepriekšēja testa, netiek gaidīta atkārtoti. Agrāk PTP tika atstāts izslēgts tīklā ar vienu komutatoru, kurā neviens DAQ-E neatbildēja, un katras kameras brīvi darbojošais pulkstenis tika nolasīts kā PTP pulkstenis, kas nesinhronizētos (`PTP is not locked`, katrā darbības ciklā). Skatīt [Vairāku kameru (pārejošas sesijas)](#multi-camera-transient-sessions).
> - **`.daq` ieraksts norāda, vai vāciņš tika izvēlēts vai pieņemts kā esošs.** DAQ-U, DAQ-M un DAQ-E tiek piegādāti bez vāciņa, un Chloros nevar redzēt, vai tāds ir uzstādīts, tāpēc sensors, kas pieslēgts bez nosauktas vāciņa, pēc noklusējuma saņem `sunshine_cosine` — tas ir *pieņēmums*. Katrs Chloros ieraksts (shēma 1.30) tagad glabā `cap_id_source` blakus `cap_id`: `operator`, ja vāciņam esat piešķīris nosaukumu (ieskaitot `none`), un `auto_default`, ja tas bija noklusējuma nosaukums. Apstrādājot ierakstu, kura vāciņš tika pieņemts kā noklusējums, un projekta DAQ vāciņa atstāta iestatījumā „Auto”, tiek izvadīts `[daq-dls] WARNING`, nosaucot failu: DAQ-U bez korekcijas saules gaismas ietekmes dēļ rāda apmēram 30 reizes augstākus rādījumus. **Nosauciet uzstādīto ierobežotāju**, kad izveidojat savienojumu, vai iestatiet projekta DAQ ierobežotāju, un brīdinājums pazudīs.
> - **Windows vairs neparādās pip konsoles logi.** Datorā, kurā ir instalēts Python, backend un katrs no tā attēlu apstrādātājiem palaida `pip` vienu reizi pēc palaišanas, un katrs atvēra konsoles logu. Tagad tas vairs nenotiek.
> - **`.daq` reāllaika ieraksti izdzīvo strāvas padeves pārtraukumu.**Ieraksts tiek rakstīts ar žurnālu un pilnu diska sinhronizāciju katrā fiksēšanas reizē aiz sensora, tādējādi lēns disks aizkavē faila ierakstīšanu, bet nekad — mērījumu reģistrēšanu. Ieraksts, kas pārtraukts komita vidū (strāvas padeves pārtraukums, procesa pārtraukšana), var atstāt blakus `<name>.daq-journal`:**kopējiet abus kopā.** Chloros atgriež žurnālu atpakaļ, pirmoreiz lasot failu, tāpēc ieraksts atveras ar visu, kas tika fiksēts pirms apstāšanās (zaudētas ir aptuveni pēdējās 3 sekundes); versijā 1.2.0 strāvas padeves pārtraukums nepareizā brīdī varēja padarīt visu failu nelasāmu („datubāzes diska attēls ir bojāts”).
> - **Ieraksts uz noslogota diska vairs neaptur sensora darbību.** `daq pool-record` ievieto nolasījumus rindā un raksta tos atsevišķā pavedienā; nolasījums, kuru neizdevās ierakstīt, tiek uzskaitīts (`daq pool-list` parāda `N write error(s)` ar pēdējo kļūdu), nevis iesaldē sensoru (viena ierakstīšana aizņēma 8,4 s, kamēr četras kameras saglabāja TIFF failus).
> - **`lattice multi-capture` bez masīva vairs nezaudē ierakstītos kadrus, ja izraisītājam beidzas laika limits.** Kameras trigera laika izbeigšanās (`SC_ERR_TIMEOUT -1011`, novērots ar četrām kamerām uz viena 1 GbE komutatora) pārtrauc darbību pie šīs kadru grupas: jau saglabātās grupas tiek uzskaitītas ar `Saved:`, pēc tam kļūda, iziet `1`. Versijā 1.2.0 tika parādīts HTTP-kļūdas kods 500 bez saraksta, lai gan faili atradās diskā.
> - **Programmatūras vadītās kameras vairs neizlaiž katru otro izraisītāju.** Kamera programmatūras vadības režīmā tagad ievieto rindā izraisītāju, kas ienāk tās nolasīšanas laikā (vai arī Chloros gaida, līdz tā ir gatava), tāpat kā to jau darīja ar kabeli sinhronizētās kameras.
> - **`daq pool-discover` nosauc DAQ-E-S `DAQ-E-S`** savas `MODEL` kolonnas (un `--json` — savas `model`). Katram Ethernet sensoram tika norādīts `DAQ-E`; sensors, kas apmēram 2 sekunžu laikā neatbild uz identitātes pārbaudi, joprojām tiek uzskaitīts kā `DAQ-E`.
> - **Uzlaboti LATTICE indeksu produkti** (NDVI un citi indeksi) no `process` visos apstrādes režīmos.
> - **OCN kameras: uzlabota starojuma intensitāte, atstarošanās un indeksu produkti saules gaismā.** Pēc spektrālo joslu atdalīšanas tiek piemērota flotes korekcija; RGN kameras paliek nemainīgas, un OCN vērtības atšķiras no 1.2.0 versijas vērtībām. Tas attiecas arī uz OCN uzņēmumiem, kas veikti ar agrāku versiju, ja tie tiek apstrādāti datorā, kurā ir saglabāta kalibrācija, ar kuru uzņēmums tika veikts (programma Chloros saglabā kameras kalibrāciju, tiklīdz kamera ir pieslēgta).
> - **`lattice continuous` ieraksta neapstrādātu kadru.** `tiff` darbības cikls izveidoja demosaicētus 3plānu kadrus, no kuriem `process` nevar sākt darbu. Tās kadri tagad ir `raw` fails, ko raksta masīva uzņemums: viens mozaīkas plāns ar paša sensora vērtībām, nesaspiests, ar kalibrēšanas paketi. Skripts, kas no šiem failiem nolasa trīs plaknes, nolasa tikai vienu.
>
> - **Katra uzņemšana saglabā izmantoto DAQ rādījumu, tāpat kā to dara GUI.** `lattice capture`, `lattice continuous` (viens `.daq` uz katru kadru), `array-capture` un `multi-capture` ar pieslēgtu masīvu (un SDK) raksta `.daq` un tā `.match.json` ikreiz, kad tiek piesaistīts vai apvienots DAQ, ieskaitot tikai neapstrādātu ierakstu; 1.2.0 versijā neapstrādāts vai debayered ieraksts tiek saglabāts tikai ar `--force-daq`. `--no-daq` to atslēdz, `--daq-tolerance-ms MS` nosaka saskaņošanas pielaidi šim ierakstam, un jaunais `daq pool-bind` no CLI piesaista kameru vai masīvu DAQ. `.daq` saglabātais uzņēmums tiek saskaņots `process` tikai ar tām kamerām, kuras tajā ir norādītas. Tagad uzņemums gaida DAQ nākamo nolasījumu, lai varētu apvienot nolasījumus abās ekspozīcijas pusēs. Skatīt [DAQ nolasījums, ko saglabā uzņemšana](#the-daq-reading-a-capture-saves) un [DAQ nolasījums tālu no ekspozīcijas](#a-daq-reading-far-from-the-exposure).
> - **`process --indices NAME`** pievieno indeksu projektam katras kameras joslām, iekļaujot jaunu projektu. Versijā 1.2.0 ar `Unknown index name(s) ignored` tika ignorēts nosaukums, kas vēl nebija iekļauts projektā.
> - **`process` blakus reāllaika masīvam:**vairs nepārstartē aizmuguri, kurā atrodas kameras vai DAQ, un gaida līdz 15 sekundēm, kamēr beidzas darba cikls.**Ctrl+C** aptur darba ciklu aizmugurē, nevis tikai CLI. Skatīt [`process` piezīmes](#notes).
> - **Projekta mape, kas atrodas pārāk dziļi, lai atbilstu Windows 260 rakstzīmju ceļa ierobežojumam**, tiek noraidīta jau sākumā, izmantojot garāko mapi, kas atbilst ierobežojumam, nevis palaidi, kas neko neieraksta. Windows instalētājs tagad piedāvā opciju „Iespējot garus Windows ceļus” un Microsoft Defender izņēmumu backendam, abas opcijas pēc noklusējuma ir atzīmētas. Skatīt [Windows (.exe)](#windows-exe).
> - **`lattice array-capture --continuous`** palaista aizmugurējā procesa paša nepārtraukto datu ieguvi: progresa josla ik pēc 2 sekundēm, kopsavilkums, nevis saraksts pa atsevišķiem failiem, un iziešana ar kodu, kas nav nulle, ja kāds fails nav ierakstīts vai disks ir pilns. Skatīt [`array-capture` režīmus](#array-capture-modes).
> - **Pirmais `array-capture` pēc `array-connect`** gaida līdz 20 s, lai saņemtu masīva pirmo kadru grupu, nevis izsauc kļūdu ar HTTP 503 „Masīvs joprojām iesilda”.
> - **Masīva ekspozīcija zem ROI:** neliels ROI vairs neierobežo masīva elementa ekspozīciju sensora brīvās darbības periodā (4,9 ms pie 768 × 304). Skatīt [Ekspozīcija uz masīva elementa](#exposure-on-an-array-member).
> - **Automātiskā ekspozīcija vairs nesamazina matricas automātisko kadru ātrumu.** Ar kabeli sinhronizēta matrica darbojas ar ātrumu, ko ļauj tīkls un kameru nolasīšanas ātrums; automātiskā ekspozīcija aizpilda laiku, ko nodrošina šis ātrums, pēc tam palielina pastiprinājumu. Manuālā ekspozīcija, kas ir ilgāka par periodu (`lattice exposure --set`, `configure --set`), palēnina izraisītāju, lai pielāgotos tai, nevis to noraidītu, un kad elements atgriežas automātiskajā režīmā, kadru ātrums atjaunojas. Skatīt [Ekspozīcija masīva elementā](#exposure-on-an-array-member).
> - **Masīvi rada mazāk nepilnīgu kadru grupu** un mazāk 503 kļūdu („Nav jaunas sinhronizētas kadru grupas”): katras pakļautās kameras izraisītāja ieeja tagad ir attīrīta no traucējumiem.
> - **Saspiesta 12 bitu datu pārraide** (`--packed`, noklusējuma iestatījums `auto`) palielina pilna sensora masīva kadru ātrumu aptuveni par trešdaļu. Tagad tiek piemērots `array-connect --packet-size` (versija 1.2.0 to pieņēma un ignorēja). Skatīt [Saspiests 12-bitu datu pārraides režīms](#packed-12-bit-transport---packed).
> - **Masīvs, kura kameras pēc strāvas pārtraukuma atjaunojas pašas.** Pēc strāvas pārtraukuma `array-connect` masīvsatver kameras, kad tās atbild, un atsāk straumēšanu ar atjaunotu PTP; 1.2.0 versijā tas pārtrauca darbību uz visiem laikiem, un bija nepieciešams jauns `array-connect`. `array-status` ziņo par `ptp_enabled: False` (un `ptp_off:`), kamēr kamera nolasīja PTP kā izslēgtu, turpretim iepriekš tika ziņots par iestatījumu, ar kuru masīvs bija savienots. Skatīt [Pastāvīgie savienojumi](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-record` veic ierakstīšanu, ja GUI nav atvērts.** Ja GUI apvienotais skats ir aizvērts, aizmugurējā sistēma pati renderē skatu: masīvaNDVI vai indeksu, ko jūs nododat ar jauno `--expr`. Ieraksts, kas neraksta nevienu kadru, tagad beidzas ar kļūdu (`no-frames: …`, iziet no `1`). Iepriekš ierakstīšana notika tikai tad, ja GUI apvienotais skats bija atvērts, un ieraksts, kas neko neierakstīja, joprojām izbeidza `0`. Skatīt [`array-record`](#array-record--combined-index-videogif-monitoring-grade).
> - **Kameru, ko Chloros jau tur, nevar atvērt otrreiz.** Komanda, kas to mēģinātu atvērt, saņem HTTP 409 kļūdu `CAMERA_HELD`, kurā norādīts, kas to tur, un komanda, kas to atbrīvo. Tas attiecas arī uz kameru, kuru pašlaik izmanto cita komanda (piemēram, `multi-test` vai `multi-capture`, kas atver savas kameras, vai `lattice capture`): otrā komanda tiek noraidīta, līdz pirmā ir pabeigta. Tās atkārtota atvēršana var pārtraukt turētāja straumi vai izraisīt aizmugures sistēmas kļūdu. Skatīt [Pastāvīgie savienojumi](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **Pagaidu daudzkameru komandas nodrošina katras kameras darbību vai arī neizdodas.** `lattice multi-capture` bez pieslēgta masīva, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream` un `index --live` iziet no `1`, nosaucot kameru, kas nepiegādāja nevienu kadru. Plūsmas izlaiž kadru grupu, kurā trūkst kādas kameras, un apstājas pēc trim secīgiem izlaidumiem; `multi-test` izdrukā vienu rindu par katru trigeri, `(no frame)` norādot, kurā kamerā tas netika reģistrēts; `align-stream` arī iziet no `1`, ja rodas uzdevuma kļūda. Pēc katras darbības cikla katra kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta: ar tās trigera un sinhronizācijas līnijas vadu savienojumiem, PTP (`PtpEnable`) un visus kameras iestatījumus, kurus darbības cikls mainīja — ekspozīciju un pastiprinājumu, AE mērķi, robežas un amortizāciju, baltā balansu, melnā līmeni, pikseļu formātu, binningu un ROI, apgriezienus, kadru ātrumu — ja vien `--keep-settings` / `--clear-settings` (skatīt [Kameras atmiņa](#camera-memory----keep-settings----clear-settings)). Iepriekš šīs komandas saglabāja visus saņemtos kadrus un izbeidza darbību ar `0` (`align-stream` izvadīja uzdevuma kļūdu kā `Note:`), un atstāja katru kameru `TriggerMode` `On`. Tās arī iestatīja katras kameras ekspozīciju no ainas pirms kadra saglabāšanas (ne vairāk kā dažas sekundes), uz mērķa savienotais masīvs katru kameru sāk ar: 40 multispektrālo, 60 RGB (to iestata `--brightness N`), un saglabā šos iestatījumus visam darbības laikam; tāpat rīkojas arī atsevišķs `lattice capture` kamerai, kuru Chloros neuzrauga. Agrāk katras kameras pirmais kadrs tika uzņemts ar kamerai raksturīgo ekspozīcijas ierobežojumu — apmēram 100 ms — neatkarīgi no apgaismojuma, un bieži vien tas tika nogriezts. Skatīt [Daudzkameru (pagaidu sesijas)](#multi-camera-transient-sessions).
> - **Pagaidu daudzkameru komandas izmanto tīklu tāpat kā `array-connect` un atver tikai `--serials`.** Katrai kamerai tiek piešķirta atbilstoša daļa no galvenā savienojuma, tādējādi četras kameras darbojas 1 Gbps savienojumā; iepriekš četras kameras kopā pieprasīja 125 MB/s, izmantojot visu 1 GbE joslu, un katrā kadrā tika zaudēti paketi. Masīvs, ko tīkls nespēj pārraidīt, tiek noraidīts, pirms tiek sākta jebkuras kameras straumēšana. Tagad `--serials` izvēlas kameras, un pirmā no tām iedarbina trigeri; iepriekš katra komanda atvēra visas kameras tīklā neatkarīgi no tā, ko norādīja `--serials`. Kļūda, kas ir GVSP paketes zudums, tagad to norāda, nevis norāda uz M8 sinhronizācijas kabeli. Skatīt [Vairāku kameru (pārejošas sesijas)](#multi-camera-transient-sessions).
> - **`lattice array-disconnect` ļauj kamerām darboties brīvi, kā tās tika atrastas.** `TriggerMode`, `Off` un viss pārējais tā, kā to konstatēja savienojums — trigera un sinhronizācijas līnijas iestatījumi (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`), PTP un kameras iestatījumus, kurus sesija mainīja — tādējādi vienakamera komanda, kas vērsta uz bijušo dalībnieku, uzreiz iegūst kadrus, un vadības kamera vairs netiek atstāta uz `TriggerSource`, `Software`. `--keep-settings` saglabā sesijas iestatījumus katras kameras atmiņā, `--clear-settings` atjauno šo atmiņu (sk. [Kameras atmiņa](#camera-memory----keep-settings----clear-settings)). `lattice continuous` arī iestata kameru, kuru tā atrod ieslēgtu, uz brīvo režīmu sesijas laikā (un pēc tam atgriež to sākotnējā stāvoklī), un sesija bez kadra iziet no `1`.
> - **`lattice configure --get` / `lattice exposure`** izdrukā `note:` ar kamerasfaktisko ekspozīciju vai pastiprinājumu, kamēr darbojas tās automātiskais režīms. Noraidīts `lattice exposure --set` / `configure --set` norāda diapazonu, ko kamera pieņem, un atjauno automātisko režīmu, nevis atstāj automātisko ekspozīciju izslēgtu. Skatīt [Sensora regulēšana](#sensor-tuning).
> - **Jaunas kameras vadības funkcijas:**`lattice highlights` (samazina spilgti baltu virsmu spilgtumu RGB-attēla renderējumā) un `lattice exposure --brightness-auto`.**`lattice multi-detect`** vienā pārbaudes ciklā atrod vadu kameras un pēc noklusējuma iestata galveno kameru kā RGB kameru.
> - **Jaunas DAQ komandas:** `daq pool-bind`; `pool-enable-ptp`; un DAQ-E pašas iestatījumiem — `pool-info`, `pool-set-network`, `pool-set-auto-stream`, `pool-reboot` un `pool-update-firmware`. Skatīt [DAQ-E ierīces iestatījumus](#daq-e-device-settings).
> - **DAQ-E, kas pārtrauc datu pārraidi, tiek atkārtoti savienots ar backend.** Ja apvienotā DAQ-E datu plūsma apklust (pārtraukta strāvas padeve komutatoram, kas to baro, pārtraukta kameras savienojuma dēļ), backend to atkārtoti pieslēdz ar iepriekšējiem iestatījumiem, atkārtojot mēģinājumus ar arvien lielākiem intervāliem, kamēr ierīce nav pieejama. Ierakstīšana uz tās turpinās tajā pašā `.daq` failā, kurā ir atzīmēts pārtraukums, un `pool-record --stop` uzskaita katru pārtraukumu. Versijā 1.2.0 sensors palika kluss līdz brīdim, kad tika izveidoti faili `pool-disconnect` + `pool-connect`, kas arī pārtrauca ierakstīšanu. Skatīt [DAQ-E, kas pārtrauc datu nosūtīšanu](#a-daq-e-that-stops-delivering).
> - **`time-sync status`** ziņo, vai PTP galvenā servera paketes sasniedz tīklu (`health`), un, ja tās nesasniedz, uz stderr tiek izvadīts `WARNING:`. Skatīt [`time-sync`](#chloros-cli-time-sync).
> - **Linux .deb:** tas palielina kodola soketa uztveršanas buferus, kas nepieciešami kameru straumēm (kodola noklusējuma iestatījumos masīvi zaudē kadrus un kadru ātrumu), pievieno aizmugures pakalpojumu, kas darbojas kā jūsu lietotājs, un `lattice network` pārbauda buferus. CLI nekad neaptur pakalpojuma aizmugurējo procesu, un atjaunināšana atkārtoti palaista aktivizētos aizmugurējos pakalpojumus. Skatīt [Aizmugurējā procesa palaide kā pakalpojums](#running-the-backend-as-a-service-headless) un [Linux serveri — soketa uztveršanas buferi](#linux-hosts--socket-receive-buffers).
> - **`lattice array-capture` norāda, kad nav bijis iespējams veikt saskaņošanu:**katrs iemesls, kāpēc ieraksts tika saglabāts nesaskaņots (nav saskaņošanas profila, elementi ar atšķirīgu izšķirtspēju, masīvs nav sinhronizēts ar kabeli, kadrs, uz kuru profilu nevar attēlot), tiek izvadīts kā `Alignment:` rinda.**`chloros-cli daq help`** ir `daq --help` (tas izbeidza `2`), un Linux sistēmā palīdzības sadaļā ir uzskaitītas tiešās aparatūras `daq` komandas , kas ir iekļautas šajā kompilācijā.
> - **Jetson:** pakete ir `chloros_1.3.0_arm64_jp6.deb`, paredzēta JetPack 6, un tās GPU apstrāde darbojas uz Orin ar JetPack CUDA PyTorch. Skatīt [Linux arm64 — Jetson](#linux-arm64--jetson-jetpack-6).

---

## Konvencijas

- Visām komandām priekšā ir prefikss `chloros-cli`. Windows vidē binārā programma ir `chloros-cli.exe`; Linux/Jetson — `chloros-cli`.
- Neobligātie argumenti tiek attēloti kā `--flag`. Obligātie pozicionālie argumenti tiek attēloti bez iekavām.
- Ja ir norādīta noklusējuma vērtība, tad, neizmantojot atslēgvārdu, tiek izmantota šī vērtība.
- CLI ir vienkāršs HTTP klients, kas darbojas ar Chloros aizmuguri (serveris ar adresi `127.0.0.1:5000`, ko izmanto arī darbvirsmas lietotne). **Kuras komandas to palaista:**
  - Galvenās komandas — `process`, `login`, `logout`, `status`, `export-status`, `time-sync` un `selftest` — izmanto jau darbojošos backend vai palaista jaunu, ja neviens nedarbojas, un to atkal aptur, kad tās pabeidz darbu.
  - **Komandas `lattice`, `project` un `daq` nekad to nepalaiž.** Tām ir nepieciešams jau darbojošs backend: Windowsa darbvirsmas lietotnes backend, Linux [backend pakalpojums](#running-the-backend-as-a-service-headless) vai tāds, ko palaidāt paši (skatiet [Backend palaišana](#start-the-backend)). Ja tāda nav, tās parāda kļūdu `Error: Chloros backend not reachable at http://127.0.0.1:5000` kopā ar komandu, kas to sāk, un iziet no programmas ar kļūdu `1`.
- `CHLOROS_BACKEND_URL=<url>` norāda uz **`lattice`**,**`project`**un**`daq pool-*`** uz citu aizmuguri (`lattice network-analysis` savukārt norāda uz `--backend-url`). Galvenās komandas apzināti fiksē `http://127.0.0.1:<port>` un to ignorē (IPv4 literāls novērš Windows sistēmā novēroto `localhost`→`::1` ~2 s kavējumu uz vienu pieprasījumu). Skatīt [Vides mainīgie](#environment-variables).
- **Relatīvie ceļi tiek atrisināti, ņemot vērā mapi, no kuras tiek palaists `chloros-cli`.** Aizmugurējais process ir tas, kas atver izejas un ieejas mapes, tāpēc pirms ceļa nosūtīšanas CLI to pārvērš absolūtā formā: `-o` / `--output` (ieskaitot tā noklusējuma vērtību, `output`), `--input`, `--burst-dir`, `--profile`, `--save` un `--mask`, kā arī [receptes](#recipe-dsl) posma `output` (noklusējuma `output`), kā arī `lattice index --live` noklusējuma mape `output`. Tādējādi `-o captures/` raksta uz `<current folder>/captures/`, un izdrukātie ceļi ir absolūti. `~` tiek izvērsts. Attiecībā uz attālo backend (`CHLOROS_BACKEND_URL`, kas norāda uz citu datoru) ceļi tiek nosūtīti tā, kā tie ir ievadīti, un tiek atrisināti uz šī datora.
- Visiem SDK/CLI izsaukumiem (katrai mašīnai vienu reizi palaidiet `chloros-cli login`; tiek saglabāts kešatmiņā `~/.chloros/`).
- Piemēros izmantoti Linux ceļi; Windows vidē aizstājiet `/home/user/...` ar `C:/Users/.../...`. DAQ-Usērijas ports Windows vidē ir `COM3` formā, bet Linux vidē — `/dev/ttyUSB0` / `/dev/ttyACM0`.
- Kopsavilkumos savstarpēji izslēdzošie karodziņi tiek rakstīti kā `{--a | --b}`, bet fakultatīvos — kā `[...]`.

---

## Augstākā līmeņa kopsavilkums

```
chloros-cli [global options] COMMAND [command options]
```

### Globālās opcijas

| Karodziņš | Apraksts |
| --- | --- |
| `--backend-exe PATH` | Pārrakstīt automātiski atklāto aizmugures izpildāmo failu. |
| `--port N` | Aizmugures HTTP-ports (noklusējums: `5000`). To izmanto tikai galvenās komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`); `lattice`, `project` un `daq` izmanto `CHLOROS_BACKEND_URL` (noklusējuma `http://127.0.0.1:5000`). |
| `-v, --verbose` | Iespējot detalizētu izvadi. |
| `--restart` | Piespiedu atkārtota aizmugurējā procesa palaišana (pārtrauc jebkuru darbojošos `backend_server.py`). Aizmugurējais process, ko palaida systemd pakalpojums (Linux .deb), nekad netiek apturēts: komanda tā vietā izdrukā `sudo systemctl restart <unit>` un iziet no programmas `1`. |
| `--version` | Izvada versiju (`Chloros CLI 1.3.0`). |
| `--help` | Parāda augstākā līmeņa palīdzību. |

### Komandu rādītājs

| Komanda | Mērķis |
| --- | --- |
| [`process`](#chloros-cli-process) | Apstrādā Survey3 vai „LATTICE” uzņemumu mapi no sākuma līdz beigām. |
| [`login`](#chloros-cli-login) | Autentificē šo ierīci ar „Chloros+” kontu. |
| [`logout`](#chloros-cli-logout) | Dzēš kešatmiņā saglabātās autentifikācijas datus. |
| [`status`](#chloros-cli-status) | Parādīt pašreizējo licences / autentifikācijas statusu. |
| [`export-status`](#chloros-cli-export-status) | Rāda Thread-4 eksporta gaitu reāllaikā, kamēr darbojas `process`. |
| [`language`](#chloros-cli-language) | Iestatīt vai parādīt CLIs attēlošanas valodu (atbalstītas 38 valodas). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Noklusējuma projekta mape (kopīga ar GUI). |
| [`update`](#chloros-cli-update) | Pārbaudīt un instalēt Chloros atjauninājumus (Linux/Jetson). |
| [`selftest`](#chloros-cli-selftest) | Sistēmas diagnostika + ātrās pārbaudes. |
| [`time-sync`](#chloros-cli-time-sync) | PTP galvenā servera statuss / vadība. |
| [`lattice`](#chloros-cli-lattice) | LATTICE kameras vadība un attēlu uzņemšana (vairāk nekā 45 apakškommandas). |
| [`daq`](#chloros-cli-daq) | DAQ spektrālo sensoru vadība (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Atveriet un vadiet saglabātu Chloros projektu (kameras + DAQ). |

---

## Instalēšana

`chloros-cli` ir iekļauts Chloros darbvirsmas instalētājā visās atbalstītajās platformās — atsevišķa CLI lejupielāde nav pieejama. Instalējot platformas paketi, `chloros-cli` tiek pievienots jūsu `PATH` kopā ar darbvirsmas lietotni un tās vadīto aizmugures bināro failu.

Jaunākās lejupielādes: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Instalētājs ietver arī ērtus palaišanas skriptus (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`), kas atver gatavu lietošanai CLI apvalku.

### Windows (.exe)

1. Lejupielādējiet Windows instalētāju no lejupielādes lapas.
2. Palaižiet `Chloros Installer 1.3.0.exe` un izpildiet vedņa norādījumus. Noklusējuma instalācijas ceļš ir `C:\Program Files\Chloros\` (CLIs atrodas `C:\Program Files\Chloros\cli\`, ko instalētājs pievieno PATH).
3. Atveriet jaunu termināli (`cmd.exe`, PowerShell vai Windows Terminal), lai tiktu atpazīts atjauninātais `PATH`.

```powershell
chloros-cli --version
```

Instalētājs automātiski pievieno `chloros-cli.exe` jūsu sistēmas `PATH` un iekļauj LATTICE kameru draiverus. Aizmugurējā daļa ir `C:\Program Files\Chloros\backend\chloros-backend.exe` (mapes `backend` jūsu instalācijas mapē), kas neatrodas `PATH`.

**Sistēmas iestatījumi.** Instalētāja lapā *Izvēlēties papildu uzdevumus* sadaļā **Sistēmas iestatījumi**ir divas opcijas, abas**pēc noklusējuma atzīmētas**:

| Opcija (kā formulēta instalētājā) | Kāda ir tās funkcija | Atteikties no klusās instalācijas |
| --- | --- | --- |
| **Iespējot garos Windows ceļus** (ļauj projekta mapēm atrasties dziļāk par 260 rakstzīmēm; ieteicams) | Iestata Windows sistēmas līmeņa `LongPathsEnabled` vērtību uz 1. Windows tiek piegādāts ar šo iestatījumu izslēgtu, un tādā gadījumā neviens ceļš nedrīkst pārsniegt 260 rakstzīmes; Chloros produkti atrodas aptuveni 130 rakstzīmes zemāk par projekta mapi, tādēļ dziļāka projekta mape tiek noraidīta (skatīt [Problēmu novēršana](#troubleshooting-pointers)). Stājas spēkā programmām, kas palaistas pēc instalēšanas. Atstāts ieslēgts pēc atinstalēšanas, jo citas programmas var paļauties uz to. | `/MERGETASKS="!longpaths"` |
| **Izslēdziet Chloros aizmugurējo moduli no „Microsoft Defender” skenēšanas**(daudz ātrāka kameras uzņemšana; ieteicams) | Pievieno „Defender”**procesa** izņēmumu `chloros-backend.exe` instalācijas mapē, tādējādi reāllaika aizsardzība neskenē katru failu, ko aizmugurējais modulis raksta, neatkarīgi no tā, kur tas to raksta. Nekāda cita programma netiek izslēgta; izslēgums tiek noņemts, atinstalējot programmu. Ja Defender nav instalēts vai ir bloķēts ar politiku, šis solis klusi neizdodas un instalēšana turpinās. | `/MERGETASKS="!defenderexclusion"` |

**Kāpēc Defender opcija ir svarīga:** pie augstas ierakstīšanas ātruma reāllaika skenēšana ierakstītajiem failiem aizņem apmēram 30 % no ierakstīšanas ātruma (izmērīts, veicot nepārtrauktu ierakstīšanu ar 9 kamerām: 77,7 ieraksti/s netiek iekļauti, 52,7 tiek skenēti). `lattice array-capture --continuous` parāda paziņojumu, ja tiek konstatēts, ka Defender ierakstīšanas laikā izmanto vienu vai vairākus procesora kodolus; palaidiet instalētāju atkārtoti ar atzīmētu šo opciju.

Automātiskā instalēšana (`/SILENT` vai `/VERYSILENT`) piemēro abas opcijas, ja vien tās nav atspējotas, piemēram, `"Chloros Installer 1.3.0.exe" /VERYSILENT /MERGETASKS="!defenderexclusion"`.

**Windows ugunsmūris.** Instalētājs vienmēr pievieno divus ieejas noteikumus `chloros-backend.exe` katram tīkla profilam, un atinstalētājs tos noņem: `Chloros Backend (UDP-In)` — kameru straumēm, PTP, mDNS un DAQ-E datu plūsmai, kā arī `Chloros Backend (TCP-In)`, kas ir ierobežots ar lokālo apakštīklu, DAQ-E programmaparatūras atjaunināšanai, kurā sensors atkal savienojas ar šo datoru, lai savāktu attēlu (skatīt [DAQ-E ierīces iestatījumi](#daq-e-device-settings)). Aizmugurējās sistēmas HTTP API paliek saistīts ar `127.0.0.1`.

### Linux amd64 (.deb)

Ubuntu 22.04 LTS vai jaunākām versijām / uz Debian balstītām x86_64 darbstacijām.

> **Ubuntu 20.04 netiek atbalstīta.** Paketes atkarību saraksts ir atvasināts no
> tā, ar ko backend faktiski veido saites, un tajā ir iekļauts `libc6 (>= 2.35)`;
> focal piegādā glibc 2.31. `apt` neļauj instalēšanai notikt, nevis ļauj tai neizdoties
> darbības laikā. Sarakstā ir minēts arī `libgl1`, ko ielādē komplektā iekļautais OpenCV un kas var trūkt minimālajā
> servera attēlā; `apt install` to nodrošina.

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
```

.deb faila instalēšana:
- `chloros-cli` līdz `/usr/bin/chloros-cli` un `chloros-backend` (palaiž aizmuguri) līdz `/usr/bin/chloros-backend`
- Kompilēto backend un CLI, izpakotus, `/usr/lib/chloros/backend/` un `/usr/lib/chloros/cli/`
- LATTICE kameru draiveri
- Trokšņu noņemšanas modeļi, kalibrēšanas paketes un atjauninājumu kanāla konfigurācija
- Sistēmas Python SDK `python3` (`import chloros_sdk`). `apt install` ietver arī savas prasības: `python3-numpy` un `python3-requests` (ieteicams, nav obligāti: iepriekš minētās programmas ir patstāvīgas; `dpkg -i` tās ignorē, un instalācijas izvade norāda, kas trūkst). Virtuālajā vidē (virtualenv): `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`
- `/etc/sysctl.d/60-chloros-gige.conf`, kas palielina kodola soketa uztveršanas buferus kameras plūsmu 32 MB un piemēro tos uzreiz (ja vērtība jau ir iestatīta augstāka, tā paliek nemainīga). Paketes noņemšana noņem arī failu. Skatīt [Linux serveri — soketa uztveršanas buferi](#linux-hosts--socket-receive-buffers).
- Divi fakultatīvi systemd pakalpojumi aizmugurējam modulim, neviens no kuriem nav ieslēgts (nākamajā sadaļā)

#### Aizmugurējā moduļa darbināšana kā pakalpojums (bez grafiskās saskarnes)

Bez pakalpojuma aizmugurējais modulis (un līdz ar to arī PTP galvenais serveris) darbojas tikai tikmēr, kamēr kaut kas to ir palaidis: `chloros-backend` terminālī, SDK skriptā vai galvenajā `chloros-cli` komandā uz tā darbības laiku. Lai backend vienmēr būtu ieslēgts bezmonitora datorā, aktivizējiet pakalpojumu **kā savs lietotājs**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` palaista backend kā šis lietotājs. Tās rakstītie ieraksti pieder šim lietotājam, detalizētais žurnāls atrodas viņa `~/.cache/chloros/logs`, un tas izmanto šī lietotāja `chloros-cli login` (`~/.chloros`), tāpēc vispirms reizi piesakieties kā šis lietotājs. PTP nav nepieciešamas root tiesības. Tāpat kā ikviens instalēts backend, tas klausās tikai uz `127.0.0.1:5000`.

`chloros-backend.service` palaista backend kā **root**. Tas joprojām ir instalēts tā, lai dators, kurā tas ir ieslēgts, turpinātu darboties, taču viss, ko root backend ieraksta, pieder root, tāpēc lietotājs nevar dzēst savus ierakstus. Lai pārietu uz savu lietotāju:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

Sīkāks root pakalpojuma žurnāls atrodas `/var/log/chloros`, un to var lasīt `adm` grupas locekļi (pakalpojums iestata `CHLOROS_LOG_DIR`; skatīt [Vides mainīgie](#environment-variables)). Paketes instalēšanas laikā tiek parādīta komanda `enable` ar ievadītu jūsu lietotājvārdu, kā arī komanda pārslēgšanai, ja root pakalpojums ir ieslēgts.

**Ieslēdziet vienu aizmugures vienību.** Vienlaikus darbojas tikai viena aizmugures vienība. Otrā ieslēgtā vienība (`chloros-backend.service` līdzās `chloros-backend@<user>.service` vai divu lietotāju `@` vienības) atteiksies sākt darbu un paliks kā `failed` ar iziešanas statusu 98; systemd to nepārstartēs. Tās `systemctl status` norāda iemeslu: `Another instance is already running. Exiting.`, tad to, kas aiztur aizmugurējo moduli (5000. ports vai bloķēšanas fails un lietotājs, kuram tas pieder), tad `systemd will not restart <unit> (exit status 98)`. Pēc pārstartēšanas var darboties jebkurš no šiem moduļiem. Lai pārslēgtos, atspējojiet to, kuru neizmantojat, kā aprakstīts iepriekš.**CLI nekad neaptur pakalpojuma backend.** `chloros-cli` to izmanto tādu, kāds tas ir, tāpat kā GUI un SDK: `process` darbojas uz tā, ielādējot savu projektu pēdējā vietā. Gadījumā, ja CLI citādi pārstartētu backend (`--restart` vai `process`, kura importēšana neko neatrada), tā nosauc vienību, izdrukā `sudo systemctl restart <unit>` un iziet no `1`, jo šādā veidā apturēts backend paliktu neaktīvs. `GET /api/status` nosauc vienību kā `service` (`null`, ja backendā nedarbojas neviens pakalpojums).**Atjaunināšana un noņemšana.** Paketes atjaunināšana aptur visus aizmugurē darbojošos pakalpojumus, pēc tam atkārtoti sāk katru no tiem, kas ir iespējots, vispirms vienības ar nosaukumu `@<user>` (vienība, kas sākta manuāli, bet nekad nav bijusi iespējota, paliek apturēta, tāpat kā pēc sistēmas pārstartēšanas). Ja ir aktivizēti vairāki pakalpojumi, darbojas tikai viens, bet pārējie paliek ar kļūdu, kā aprakstīts iepriekš; atjaunināšanas laikā tiek izvadīti kodi `Chloros: N backend units are enabled: …` un `Keep one enabled: sudo systemctl disable --now <unit>`. Paketes noņemšana aptur un deaktivizē šos pakalpojumus. Tās iztīrīšana arī dzēš `/var/log/chloros`.

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

Tāds pats izkārtojums kā amd64 .deb failam, iekļauti uztveršanas buferi un [backend pakalpojumi](#running-the-backend-as-a-service-headless), paredzēts Jetson Orin / Orin NX / Orin Nano ar **JetPack 6** (L4T R36). Tā PyTorch ir JetPack 6 CUDA versija Orin, tādēļ GPU apstrāde, ieskaitot tekstūru atpazīstošo debayer, darbojas uz Jetson GPU. Pakete pati neietver CUDA: tā izmanto CUDA un cuDNN bibliotēkas, kuras instalē JetPack 6 (`sudo apt install nvidia-jetpack` attēlā, kurā tās trūkst). Ja CUDA GPU nav pieejams, apstrāde notiek uz CPU, un tekstūru atpazīšanas funkcija izmanto standarta debayeru (skatīt [`process`](#chloros-cli-process)).

### Autentificēšanās vienu reizi katrai ierīcei

Katrā platformā ir nepieciešama vienreizēja Chloros+ pieteikšanās, lai SDK/CLI izsaukumi darbotos:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Pieslēgšanās dati tiek saglabāti kešatmiņā `~/.chloros/user_session.json`.

### Backend palaišana

Komandas `lattice`, `project` un `daq` sazinās ar jau darbojošos backend; tās nekad to nepalaiž (skatīt [Konvencijas](#konvencijas)). Kamēr izmantojat šīs komandas, atstājiet vienu no tiem darbojošos:

| Platforma | Kā |
| --- | --- |
| Windows | Atveriet Chloros darbvirsmas lietotni (tās backend apkalpo arī CLI), vai palaidiet `"C:\Program Files\Chloros\backend\chloros-backend.exe"` atsevišķā terminālī (jūsu instalācijas mapes `backend` mapē) un atstājiet to atvērtu. |
| Linux/Jetson | Palaižiet `chloros-backend` atsevišķā terminālī vai nodrošiniet, lai viens vienmēr darboties, izmantojot [backend pakalpojumu](#running-the-backend-as-a-service-headless). |

Vienlaikus darbojas tikai viens backend; visi klienti (GUI, CLI, SDK skripti) to izmanto kopīgi. Arī galvenās komandas (`process`, `login`, `status`, …) to izmanto un palaista vienu, ja neviens nedarbojas.

**Aizmugurējais modulis turpina darboties starp komandām**, tādējādi nākamajai komandai nav jāveic tā palaišana no jauna. Tas apstājas, kad iziet no sistēmas (`chloros-cli logout`, ja vien nav atvērta Chloros lietotne vai nav sākta izpilde vai bezsaistes kompilācija), kad palaidāt `chloros-cli backend stop` vai pats pēc 30 minūtēm, ja nekas to neizmanto: nav ne komandas, ne skripta, ne lietotnes, ne izpildes, ne bezsaistes kompilācijas, ne pieslēgtas kameras, ne gaismas sensora. `backend stop` to noraida, kamēr notiek izpilde vai bezsaistes kompilācija, to nosaucot; `--force` to aptur uzreiz, un šī izpilde vai kompilācija tiek zaudēta (sērija tiek saglabāta, un `array-build-video` to veido no jauna). Iestatiet `CHLOROS_BACKEND_IDLE_MIN` pirms aizmugurējā procesa sākuma, lai mainītu 30 minūšu laiku (`0`: nekad); [aizmugurējais pakalpojums](#running-the-backend-as-a-service-headless) nekad neapstājas pats no sevis. `chloros-cli backend status` parāda, vai kāds darbojas, kas to izmanto un vai notiek kāda bezsaistes kompilācija. `process` atver savu projektu darbojošajā backendā, ja vien nav atvērta Chloros lietotne ar projektu, kuru tā atvēra: tādā gadījumā tā to noraida, un jums vispirms jāveic darbības lietotnē vai jāaizver šis projekts. Projekts, ko iepriekš atstājis atvērtu `process` vai SDK skripts, vienkārši tiek aizstāts.

### Pārbaudiet instalāciju

```bash
chloros-cli --version           # prints "Chloros CLI 1.3.0"
chloros-cli selftest            # 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

`selftest` iziet no `0` tikai tad, ja ir izietas visas septiņas pārbaudes. Pēdējai ir nepieciešams CUDA GPU (NVIDIA GPU, vai Jetson Orin) un trokšņu noņemšanas modeļiem, tāpēc datorā, kurā tā nav, tiek parādīts `Failed: CUDA + Denoiser` un programma iziet no `1`; tur joprojām darbojas viss, izņemot tikai GPU tekstūru atpazīstošo debayeru.

> **Nepieciešama Chloros+ abonementa.**CLI darbībai ir nepieciešams aktīvs Chloros+ plāns.**Copper**ir Chloros+ pamata līmenis — katram maksas Chloros+ līmenim ir piekļuve CLI/SDK; tikai bezmaksas**Iron** līmenim tā nav. (Plāna identifikatoru atbilstība: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) Pārejiet uz augstāku plānu, izmantojot [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> Šo ierobežojumu piemēro backend, nevis tikai CLI: pieprasījums ar SDK/CLI atzīmi bez apmaksāta plāna tiek noraidīts ar kļūdas kodu `403 PLAN_UPGRADE_REQUIRED`, neatkarīgi no tā, vai tas nāk no `chloros-cli`, Python SDK vai pašizstrādāta HTTP klienta. Izslēgtajam lietotājam tiek atgriezts kļūdas kods `401 AUTH_REQUIRED`. Piekļuve darbojas bezsaistē plāna papildlaika periodā (30 dienas mēnesī, līdz gada plāna termiņa beigām) un tiek pārtraukta, kad tas beidzas; `chloros-cli status` turpina darboties, lai iemesls būtu redzams (tas ir vienīgais SDK/CLI maršruts, kas ir atbrīvots no līmeņu ierobežojuma — `GET /api/license-status`).

---

## Piecu minūšu ātrs sākums

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI GNDVI OSAVI

# 3. The lattice / daq commands need a running backend: on Windows open the
#    Chloros desktop app; on Linux start one (or see "Start the Backend")
chloros-backend &

# 4. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 5. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 6. Read a spectrum from a connected DAQ-U (Windows: --port COM3)
chloros-cli daq pool-connect --port /dev/ttyUSB0
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

`process` (2. solis) uzsāk savu backend darbību, ja neviens cits nedarbojas, un to aptur, kad tas ir pabeigts, tāpēc uzsāciet vienu no 4. soļa un tālāk. Skatīt [Aizmugurējās sistēmas palaišana](#start-the-backend).

---

## `chloros-cli process`

Apstrādājiet attēlu mapi, izmantojot pilnu Chloros apstrādes ķēdi (mērķa noteikšana → kalibrēšana → vinjete → atstarošanās → indeksa eksportēšana).

### Kopsavilkums

```
chloros-cli process INPUT [OPTIONS]
```

### Pozicionālie argumenti

| Arguments | Apraksts |
| --- | --- |
| `INPUT` | Ceļš uz ievades mapi, kurā atrodas `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE) vai `.dng` faili. |

### Vispārējās opcijas

| Opcija | Noklusējums | Apraksts |
| --- | --- | --- |
| `-o, --output PATH` | jauna mape ar nosaukumu `-n` noklusējuma projekta mapē | Projekta mape. Projekts tiek nosaukts pēc tās, un mape, kurā jau atrodas `project.json`, iegūst `_1` / `_2`, nevis tiek pārrakstīta. Noklusējuma projekta mape ir tā, kuru izvēlējās `set-project-folder` (vai lietotāja saskarne), citādi — `Documents/MAPIR/Chloros Projects`. |
| `-n, --project-name NAME` | automātiski (laika zīmogs) | Projekta nosaukums, ko izmanto tikai **bez** `-o` (ar `-o` projektam tiek piešķirts mapes nosaukums). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` izmanto „Chloros+“ neironu debayeru Survey3 attēliem; lēnāks, bet augstākas kvalitātes. Tas ir paredzēts tikai Survey3: LATTICE uzņēmumiem vienmēr tiek izmantots standarta demosaics, tāpēc mapē, kurā atrodas LATTICE uzņēmumi, `process` pēc importēšanas noraida `texture-aware`, parādot kļūdu, kurā norādīts to skaits, un iziet no `1` pirms jebkādas apstrādes (Chloros lietotne arī šiem attēliem nepiedāvā Texture Aware funkciju). Tam ir nepieciešams CUDA GPU (NVIDIA GPU uz Windows/Linux x64, Orin GPU uz Jetson): bez tā programma izmanto `standard`, un aizmugurējā sistēma reģistrē vienu `[DEBAYER] … needs a CUDA GPU …` rindu, kurā norādīts iemesls (parādīts ar `--verbose`). |
| `--vignette / --no-vignette` | `--vignette` | Vignetes korekcija. |
| `--reflectance / --no-reflectance` | `--reflectance` | Atstarošanas kalibrēšana (izmanto paneļa mērķi, ja tāds tiek atrasts, vai NIST sērijas kalibrēšanu LATTICE gadījumā). LATTICE multispektrālajiem datiem tas darbojas arī kā atstarošanas **reizinājums** slēdzis — skatīt [Eksporta slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | izslēgts | Piemērot PPK GNSS korekcijas no sidecar failiem. |
| `--exposure-pin-1 CAMERA_MODEL` | nav | Kameras modelis, kas pieslēgts ekspozīcijas kontaktam Nr. 1, PPK laika sinhronizācijai. |
| `--exposure-pin-2 CAMERA_MODEL` | nav | Kameras modelis, kas pieslēgts ekspozīcijas kontaktam Nr. 2, PPK laika sinhronizācijai. |
| `--recal-interval SECONDS` | `0` | Minimālais pārkalibrēšanas intervāls sekundēs (projekta *Minimālais pārkalibrēšanas intervāls*). |
| `--timezone-offset HOURS` | `0` | Gaismas sensora laika zonas nobīde no UTC, stundās (projekta *Gaismas sensora laika zonas nobīde*). |
| `--format FORMAT` | `TIFF (16-bit)` | Viens no `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros nekad nepalielina attēla bitu dziļumu: visiem 8-bitu attēliem dziļāks formāts tiek ierakstīts kā `TIFF (8-bit)`, un programma to norāda vienu reizi. Projektā, kurā ir apvienoti 8 bitu un 12/16 bitu attēli, katrs attēls tiek eksportēts ar savu dziļumu vai zemāku. Radiance vienmēr ir 32 bitu peldošā komata. |
| `--indices NAME [NAME ...]` | nav | Veģetācijas indeksi pēc iestatījuma nosaukuma (`NDVI`, `GNDVI`, `OSAVI`, `SAVI`, `EVI`, `GCI`, …), neņemot vērā lielos un mazos burtus. Katrs no tiem tiek pievienots projektam atbilstoši kameras spektrālajiem diapazoniem, iekļaujot jaunu projektu. Nosaukums, kas nav iepriekš iestatīts vai kuru kameras spektrālie diapazoni nevar izveidot (piemēram, „`NDRE`” ir nepieciešami RE un NIR diapazoni, kurus neviena kamera atsevišķi nenodrošina), tiek norādīts brīdinājumā un nesaņem indeksa attēlu. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | Piespiedu apstrādes procesa sākumpunkts LATTICE TIFF failiem (tas neietekmē Survey3.raw failus). Tāpat arī „glābšanas lūka”, kas ļauj apstrādāt uzņemumus, kuros **nav RAW failu** — skatīt [Kā izskatās uzņemumu mape](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | ieslēgts | Izvada lineāro debayeringa rezultātu (`Debayered_Images`). Skatīt [Eksportēšanas slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | ieslēgts | Izvada ekrāna priekšskatījumu (`Preview_Images`): RGB = baltā balanss (DAQ apgaismojums, ja pieejams, citādi pelēkā skala) + gamma; multispektrālais = viltus krāsu izstiepums. |
| `--radiance / --no-radiance` | ieslēgts | Izvada float32 starojuma intensitāti (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | Atsauce uz LATTICE atstarojuma produktu: `auto` = kvalitātes pārbaudi izturējis mērķis kadrā ir absolūtais etalons (mērķis ar kvalitātes pārbaudes atzīmi konkurē ar DAQ pēc izmērītā rezultāta), DAQ lejupvērstā starojuma (ρ = π·L/E) rezerves variants; `target` = stingrs (bez DAQ aizstāšanas); `daq` = DAQ ir noteicošais. Skatīt [Eksportēšanas slēdži katram produktam](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | nav | Katras vienības **izmērīto** mērķa atstarojuma skenējumu katalogs (`<serial>.csv`); ja nav pieejami, tiek izmantoti nominālie T3/T4P spektri. |
| `--array-alignment / --no-array-alignment` | ieslēgts | LATTICE masīvi: piemēro katras uzņemšanas `Chloros:Alignment*` XMP failā iezīmēto moduļu savstarpējo saskaņošanu visiem apstrādātajiem produktiem (debayering / priekšskatījums / starojums / atstarojums / indekss). Neveic darbību attēliem bez šīm birkām. |
| `--array-alignment-crop / --no-array-alignment-crop` | apgriešana | Apgriež saskaņotos eksportētos attēlus līdz masīva kopējai pārklāšanās zonai, lai visiem moduļiem būtu viena kopīga platība; `--no-…` saglabā pilnu sensora laukumu (melna aizpildīšana ārpus avota). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Pārparaugošana izlīdzināšanas deformācijas dēļ. `nearest` saglabā precīzus avota dinamiskos diapazonus (bez radiometrisko vērtību sajaukšanas starp pikseļiem). |

### Mērķa noteikšanas opcijas

| Atzīme | Apraksts |
| --- | --- |
| `--min-target-size PIXELS` | Minimālais kalibrēšanas mērķa izmērs pikseļos detektoram. |
| `--target-clustering 0-100` | Mērķu grupēšanas procentuālā daļa (noklusējums `60`). |
| `--target / --targets` | Meklēt kalibrēšanas mērķus tikai ievades `target` vai `targets` apakšmapē (ātrāk). |

### Piemēri

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a project folder of your choice (the project is named after it)
chloros-cli process "C:/input" -o "C:/projects/Field_A_2026-05-26"

# Name the project and let it go to the default project folder
chloros-cli process "C:/input" -n "Field_A_2026-05-26"
```

### Eksportēšanas slēdži katram produktam (LATTICE multispektrālais)

LATTICE apstrāde vienā ciklā sadala rezultātus **visiem piemērojamajiem produktiem**. Četri slēdži katram tipam — `--debayered`, `--preview`, `--radiance`, `--reflectance` — visi ir**pēc noklusējuma ieslēgti**; izmantojiet veidlapu `--no-<type>`, lai kādu no tiem atslēgtu. RGB-galvenās kameras vienmēr raida tikai debayered + priekšskatījumu (bez starojuma/atstarošanas pa joslām), tāpēc `--radiance`/`--reflectance` tām nedod nekādu efektu. Šie slēdži tiek ignorēti attiecībā uz Survey3 `.raw` (kas seko standarta atstarošanas/mērķa ceļam). *(Vecais `--radiometric-output {reflectance,radiance,sensor-response}` karodziņš tika **noņemts** un aizstāts ar šiem slēdžiem; `sensor-response` līmeņa vairs nav.)*

| Produkts | Izvade | Nepieciešama DAQ lejupvērsta datu pārraide? |
| --- | --- | --- |
| `--debayered` | Lineārā demosaika (`Debayered_Images`): tas pats fails, ko uzņemšana saglabā savā `debayered` līmenī — kalibrēšanas paketes tumšā nobīde (DSNU) no neapstrādātajiem datiem, pēc tam OpenCV malu atpazīstošā demosaika; bez linearitātes LUT, līdzena lauka vai krāsu atdalīšanas. | Nē. |
| `--preview` | Ekrāna priekšskatījums (`Preview_Images`): RGB = WB + gamma; multispektrālais = viltus krāsu izstiepšana. | Nr. |
| `--radiance` | float32 W/m²/sr/nm no pilnās radiometriskās ķēdes (`Radiance_Images`). | Nr. |
| `--reflectance` | uint16 atstarojums ρ (`32768` = 1,0), Pix4D-saderīgs. | **Jā**, ja vien to nefiksē kvalitātes pārbaudi izturējis mērķis kadrā (skatīt zemāk). |

`--reflectance-source` izvēlas atstarojuma atsauci:**`auto`**(noklusējums) padara kvalitātes pārbaudi izturējušu mērķi kadrā par**absolūto atsauci**— mērķa nostiprinātās empīriskās līniju ķēdes tiek salīdzinātas ar izslēgtajiem paneļiem, un uzvarētājs tiek piemērots josla pa joslai: josla, kuru DAQ nevar kalibrēt vai kuras DAQ slīpuma pielāgošana neizdodas, pārņem mērķa paša līniju (`ReflectanceElBandMethods` norāda, kura) — mērķis, kas iztur QA ar atzīmi (izlaists panelis, nedarbojas josla), arī konkurē ar DAQ sadalījumu par šo izmērīto rezultātu, un kadrs, kuram tas zaudē, tiek atzīmēts ar `ReflectanceChain=daq` — ja mērķis nav klāt vai kvalitātes kontrole (QA) neizdodas, tiek izmantota DAQ lejupvērstā sadalīšana (ρ = π·L/E);**`target`**ir stingrs (bez DAQ aizstāšanas);**`daq`**izvēlas DAQ autoritatīvo darbību. Mērķa ģeometrija (ArUco / fiksēts ROI / sloksne) tiek ņemta no projekta mērķa konfigurācijas; `--target-reflectance-dir DIR` saglabā katras vienības**izmērītos** skenējumus (`<serial>.csv`), kurus nosaka pēc mērķa vienības sērijas numura/QR, izmantojot nominālos T3/T4P spektrus kā rezerves risinājumu.

DAQ atstarošanas ceļš automātiski nosaka **laika zīmogam atbilstošo lejupvērsto starojumu**no ierakstītā**`.daq`**(DAQ-U/M/E)**vai DAQ-M nativā `.csv`**, kas atrasts kopā ar attēliem. Ja kameras vai DAQ kalibrēšanas pakete nav saglabāta vietējā kešatmiņā, apstrādes ķēde**to automātiski lejupielādē no AWS** pirmajā lietošanas reizē (vienreiz nepieciešams interneta savienojums; tiek saglabāts `~/.chloros/`).**Kura ir šī nolasījuma piederība?**`.daq`, ko saglabā uzņemums, ir tās kameras(-u) nolasījums, kuru(-as) tas apraksta — nosaukums norādīts blakus esošajā `<file>.daq.cameras.json` papildu failā (`lattice capture`, `multi-capture`, `array-capture` un GUI; neapstrādāta sērija nosauc katra elementa rādījumu savā manifestā) — un `process` to saskaņo**tikai ar attēliem no šīm kamerām**. Tādējādi atsevišķa kamera, kurai nav savu rādījumu, vairs neizmanto citas kameras DAQ; tai ir jāpiesaista DAQ vai jāveic ieraksts. DAQ paša ieraksts (`daq pool-record` vai ieraksts, kas uzsākts GUI), vai jebkurš `.daq` bez šī papildu faila (ieskaitot vecākus ierakstus), joprojām kalpo visām kamerām.

#### Reflektances pikseļu nolasīšana (Pix4D / Metashape / jūsu pašu skripti)

Reflektance tiek saglabāta kā vesels skaitlis DN, un **DN, kas nozīmē ρ = 1,0, ir atkarīgs no avota kameras**:

| Avots | ρ = 1,0 ir | Kā noteikt |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (rezerves diapazons līdz ρ 2,0) | Failā ir iezīmēts XMP `Chloros:PixelScale=32768`. |
| Survey3 | `65535` (nogriezts pie ρ 1,0) | Nav `Chloros:*` XMP marķējumu — šī neesamība *ir* pazīme. |

**Izlasiet `Chloros:PixelScale` un daliet ar to**, nevis pieņemiet, ka tas ir konstants. Tagi ir definēti uint16 domēnā, tādēļ tie paliek nemainīgi `32768` visos izvades formātos, kuros tiek veikta mērogu pārrēķināšana — `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)` un `TIFF (32-bit, Percent)` ir pašraksturojoši (vispirms normalizējiet saglabāto datu tipu atpakaļ uz uint16: ×257 no 8 bitu, ×65535 no float).

> **Faili no vecākām versijām var nebūt mērogoti.** 8-bitu atstarošanas TIFF-fails tiek pārmērogots un marķēts kā `Chloros:PixelScale`, tāpat kā pārējie. Versijās pirms 1.3.0 to tā vietā *apgrieza* līdz 0..255, tādējādi katra vērtība virs ρ≈0,008 tika izlīdzināta līdz 255, un tika izlaistas gan `Chloros:PixelScale`, gan `MicaSense:RadiometricCalibration` tuples. **Ja LATTICE atstarojuma failā šī birka nav, nepieņemiet, ka mērogs ir zināms — pārstrādājiet to**, nevis daliet pikseļus, kas nekad nav bijuši dalāmi.

#### Indeksa pikseļu nolasīšana (NDVI un citi veģetācijas indeksi)

**Visa skaitļa indeksa rastra ir kontrasta izstiepums katram attēlam atsevišķi, nevis −1…1 karte.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` un `JPG (8-bit)` indeksa produkti — ieskaitot 8-bitu TIFF, kādā tiek saglabāts 8-bitu attēls — glabā

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

kur `IndexMin` / `IndexMax` ir **šī kadra zemākā un augstākā galīgā indeksa vērtība**. DN 0 nav −1, un pilna skala nav +1.

Chloros marķē abus galapunktus, lai attēlošana paliktu apgriežama:

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

Lasīšanai nav nepieciešams `-config pix4d.config` — exiftool šīs XMP īpašības nolasa pats. Konfigurācija ir nepieciešama tikai to *rakstīšanai*.

> **`IndexMin == IndexMax`** nozīmē, ka katram šī kadra pikselim bija viena un tā pati indeksa vērtība. Rasters ir aizpildīts vidējā skalā, un nav ar ko dalīt — ņemiet galapunktu.

> **Ja nav `Chloros:Index*` tagu, tas nozīmē, ka nav norādīta kartēšana.** LUT eksports ir krāsots attēls, kura pikseļi ir gradienta krāsas, kas piesaistītas jūsu LUT sliekšņa logam, un kadram bez pikseļa ar galīgu indeksu nav diapazona, ko norādīt. Nekādā gadījumā nepieņemiet, ka pastāv kartēšana — tas pats noteikums attiecas uz trūkstošo `PixelScale`.**Tā kā izstiepšana notiek katram attēlam atsevišķi, divi viena lidojuma kadri nav salīdzināmi kā DN**— katram ir savi galapunkti, tāpēc viens un tas pats NDVI atbilst atšķirīgam DN dažādos attēlos. Lai veiktu kvantitatīvu darbu visā lidojumā (zonālā statistika, fiksēti sliekšņi, laika rindas), eksportējiet**`TIFF (32-bit, Percent)`**: indeksa rastri tajā tiek ierakstīti kā float32**paši indeksa vērtības**, bez nepieciešamības pēc pārskalēšanas vai marķieriem. Neskatoties uz formāta nosaukumu, indeksa produkti nav ×100 — šī mērogošana attiecas tikai uz atstarojumu ar veselo skaitļu datu tipu.

#### Spektrālās atdalīšanas ierobežošanas maska (kuros pikseļos josla tika ierobežota)

3×3 spektrālā atdalīšana var padarīt joslu negatīvu — ja gaismā šī josla gandrīz nav sastopama vai ja aina atrodas ārpus kameras atdalīšanas matricas aprakstītā diapazona. Tā nav reāla vērtība, tāpēc eksportēšanas procesā tā tiek fiksēta kā 0 — un reāllaika skatā tiek parādīts tas pats 0. Josla var tikt ierobežota dažos pikseļos vai gandrīz visos; maska reģistrē abos gadījumos. `Chloros:Band{R,G,B}UnmixClampFraction` norāda, cik lielai kadra daļai tas notika; ierobežošanas maska norāda, **kuri pikseļi**:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— `clamp_masks/` mapē**blakus** produkta mapei, nekad tās iekšienē (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`; `process` eksporta `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif`) — katram starojuma, reflektances un indeksa produktam, kurā vismaz viens pikselis ir ierobežots: gan uzņemtajiem attēliem, gan eksportētajiem `process` (kā arī katra kadra TIFF failiem `array-build-video --save-tiffs`). Vienas joslas `uint8`, uz paša produkta pikseļu režģa (ieskaitot orientāciju, ROI, binningu un masīva izlīdzināšanu). Katra maska nosauc sava produkta kameru (`Make` / `Model` / sērijas numurs), uzņemšanas laiku un objektīva tagus, kā arī uzskaita joslas, kuras apzīmē tās biti (`XMP-Camera:BandName`, sākot ar 0. bitu); tās `ImageDescription` un `Software` to marķē kā fiksācijas masku, un tajā nav kalibrācijas.
- **Biti**: 0. bits = josla produkta R kanālā (`Chloros:BandRName`), 1. bits = G (`BandGName`), 2. bits = B (`BandBName`). Iestatīts bits nozīmē, ka šīs joslas pikseļa vērtība tika ierobežota līdz 0 — to un jebkuru no tā veidoto indeksu uzskatiet par mazticamu.
- **`Chloros:UnmixClampMask`** ir atzīmēts uz katra produkta, kuram tika veikta atdalīšana: maskas ceļš attiecībā pret paša produkta mapi (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, vienmēr atdalīts ar `/` — pievienojiet to attēla mapei, lai atvērtu masku), vai `none`, ja neviens pikselis nav ierobežots. Ja marķējuma nav, tas nozīmē, ka produktam nekad nav veikta krāsu atdalīšana (RGB vai mono kameras, neapstrādāti/debayered līmeņi).
- `lattice capture` / `array-capture` izdrukā maskas ceļu zem saglabātā faila (`clamp mask: …`), un uzņemšanas rezultātu sarakstā tā tiek norādīta kā kadra `clamp_mask`.
- `lattice index --live --save-multiband` raksta vienu masku katrai avota kamerai daudzjoslu kopai — skatīt [`lattice index` — daudzjoslu kopas maskas](#lattice-index--multiband-stack-masks). Videoklipam nav maskas: `array-build-video` tā vietā ziņo par katra kadra ierobežojuma daļām (skatīt [`array-build-video`](#array-build-video--offline-reprocess-a-saved-burst)).

Tā kā nevienā produkta mapē, neatkarīgi no līmeņa, nav maskas, produkta mapi (`radiance/`, `reflectance/`, `process` eksporta `*_Images/` mape) var tikt nodota Pix4D, Metashape vai citai fotogrammetrijas programmatūrai tādā veidā, kāda tā ir — ieskaitot apakšmapes — un tā pieņem tikai attēlus. Nododiet tai produktu mapi, nevis mapi virs tās (tajā atrodas arī `clamp_masks/` un visi pārējie eksporta veidi). „Chloros” paša importēšanas funkcija izlaiž maskas. Maskas nolasīšana: `(mask & 1) != 0` nozīmē „šeit tika ierobežota R kanāla josla”, `& 2` — G, `& 4` — B.

#### EXIF dati tiek pārnesti uz eksportēto failu

`process` kopē avota uzņēmuma **GPS bloku un tā ExifIFD** uz katru produktu, tādējādi
eksportā tiek pārnesti `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` un
`CameraSerialNumber` kopā ar ģeoreferencēšanu.

**`FocalLength` nav fakultatīvs fotogrammetrijai.** Pix4D aprēķina zemes parauga attālumu, izmantojot
fokusa attālumu un augstumu; ja šī birka trūkst, programma izmanto ārkārtīgi nepareizu mērogu. Vienā
lidojumā virs apelsīnu dārza, kurā tika uzņemti 49 attēli, trūkstošā birka pārvērta 411 m × 160 m lielu teritoriju par rekonstruētu
47,8 km × 13 km — 455 MP ortofoto, kurā galvenokārt bija „nodata”, kas tika uzskatīts par mozaīkas problēmu un
BigTIFF problēmu, pirms kāds pārbaudīja GSD. Ja jūsu ortofoto iznāk neiespējamā
mērogā, vispirms palaidiet `exiftool -FocalLength` pār eksportēto produktu.

Kopija apzināti **nav** `-all:all`: IFD0'struktūras tagi traucē LATTICE izvadi, kad
tā tiek kopēta, un `ExifImageWidth` / `ExifImageHeight` ir izslēgti, jo tie apraksta
*avota* uzņemšanu — eksportam, kura izmēri jebkad ir mainīti, citādi būtu izmēri, kas ir pretrunā ar paša rastra izmēriem. XMP tiek rakstīts tieši, nevis kopēts, jo ExifTool
izmet vienā izsaukumā radītās XMP birkas, kad XMP bloks tiek kopēts (kas izraisītu MAPIR kalibrēšanas birku zudumu).

### Kur tiek saglabāti izvades faili

Produkti tiek ierakstīti **projekta mapē, grupēti pēc kameras un pēc tam pēc faila formāta**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

Kameras mape LATTICE gadījumā ir `LATT-<sensor>-<lens>-F<filter>` (atbilstoši uzņēmuma EXIF
`Model`) un `<model>_<filter>` — Survey3 gadījumā — divas kameras, kurām ir kopīgs sensors un filtrs, bet atšķiras
objektīvs, tiek saglabātas atsevišķās mapju struktūrās, jo atšķiras vinjetēšana, redzes lauks un izkliede. Formāta
mapes nosaukums ir šāds: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` vai `tiff32` attiecībā uz
`TIFF (32-bit, Percent)`.

> **Katram eksportētajam produktam tiek saglabāts AVOTA faila nosaukums.** Spilgtuma eksporta fails
> `capture_…_raw.tif` joprojām saucas `capture_…_raw.tif` — tas vienkārši atrodas
> `tiff32/Radiance_Images/`. **Produktu identificē mape, nevis faila nosaukums**, tāpēc, veicot globālo meklēšanu
> pēc `*radiance*.tif`, nekas netiks atrasts; tā vietā veiciet saskaņošanu pēc direktorija.

### Gaismas sensora ieraksti — kalibrēti `.daq` + `.csv`

`process` apstrādā arī ierakstus `.daq` jūsu ievades mapē, un tam **nav**
nav vajadzīgi nekādi attēli: atsevišķi palaists DAQ-U / DAQ-M / DAQ-E veic pilnīgu
ierakstu, un mape, kurā ir tikai `.daq` faili, ir derīga ievade.

DAQ ierakstīšana ir iespējama **bez** tā kalibrēšanas — tieši to pēc noklusējuma dara publiskie
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) ierakstītāji
(`record_daq.py`) dara pēc noklusējuma: tie ieraksta neapstrādātos sensora skaitījumus un marķē failu tā, lai
Chloros iegūtu šī sensora rūpnīcas kalibrāciju **pēc sērijas numura** (vispirms no vietējās kešatmiņas,
pēc tam no MAPIR mākoņa) un to piemēro. `process` rezultātu atkal ieraksta:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` katrā nolasījumā ietver vienu rindu: UTC laika zīmogu, integrācijas laiku, kopējo jaudu,
fotopisko/skotopisko luksu, PPFD (un tā sadalījumu pa zilo, zaļo un sarkano), maksimālo viļņa garumu, tad
pilns spektrs uz paša sensora viļņu garuma režģa. `.daq` tiek importēts atkārtoti, neveicot
otru kalibrēšanu.

`<name>` ir paša ieraksta faila nosaukums. Divi ieraksti dažādās mapēs var dalīt
vienu — katram uzņēmumam atsevišķus rādījumus (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`), kas saglabāti
vienā un tajā pašā milisekundē ar divām kamerām — un katrs joprojām saņem savus produktus: pirmais saglabā
`<name>`, bet nākamais **pēc** nosaukuma pievieno mapes, kas to atšķir
(`<name>__<capture>_daq_calibrated.daq`), tādējādi ieraksta datums un laiks joprojām ir pirmajā vietā.

Veiksmīgas darbības gadījumā izpildes rezultāts ir `Light-sensor products written: N (calibrated .daq + .csv)`.
Teksts iekavās apraksta to, kas faktiski tika ierakstīts, tātad tas izskatās šādi:
`(RAW COUNTS — this sensor has no calibration bundle)`, ja sensora dati nav apvienoti mapē, un
`(N calibrated, M raw counts)`, ja mapē ir abi. Pats backend
`[DAQ-EXPORT]` un `[RUN-SUMMARY]` virsrakstu formulējumi ir veidoti tāpat — neviens no
šiem trim nevar saukt neapstrādātu eksportu par kalibrētu.

DAQ-U / DAQ-M / DAQ-E ieraksts, kura kalibrēšanas pakete nevar tikt iegūta — jūs esat
bezsaistē vai šim sensoram nav reģistrētas kalibrācijas — tiek **izlaists ar iemeslu**
`[DAQ-EXPORT]` rindā, nekad netiek saglabāts kā „kalibrēts” fails, kas satur neapstrādātus skaitļus.
Izveidojiet savienojumu ar internetu un palaidiet atkārtoti. Iemesls ir tas, ko lasītājs faktiski
konstatējis attiecībā uz šo failu (nelasāma shēma, nav paketes, rakstīšanas kļūda), un izpildes
kopsavilkumā ir uzskaitīti **atšķirīgi** iemesli — divdesmit faili, kas izlaisti viena iemesla dēļ, tiek uzrādīti kā viens
iemesls, nevis divdesmit tā atkārtojumi. CLI izpildes beigās nosauc katru izlaisto ierakstu kopā ar tā iemeslu:

```
⚠ Light-sensor recording not exported: <file>.daq: no calibration bundle available for …
```

Mapes ar ierakstiem, no kuras netiek eksportēts **neviens** ieraksts, izpilde neizdodas: skatiet
[Izpilde, kas neieraksta attēlus, neizdodas](#a-run-that-writes-no-images-fails).

Ieraksts, kura nosaukums beidzas ar `_calibrated` vai `_raw` (`field_raw.daq`), **netiek
importēts**: tie ir nosaukumi, ko `process` piešķir saviem rezultātiem, un rezultāts, kas tiek nolasīts
kā ieraksts, tiktu uzskaitīts divreiz. CLI to norāda un nosauc failu —
`Not imported as a light-sensor recording: field_raw.daq … Rename the file to import it.`
— tā vietā, lai ziņotu par tukšu mapi. Pirms apstrādes pārdēvējiet šādu ierakstu.

#### DAQ-A ierakstu eksportēšana kā neapstrādāti skaitļi

**DAQ-A** sērija ir radusies pirms sērijas numura pakotņu sistēmas ieviešanas un tai nav kalibrēšanas pakotnes,
ko lejupielādēt — tā tiek kalibrēta uz vietas, izmantojot atstarošanas mērķi, tāpēc
tai tā nekad nav bijusi nepieciešama. Šo ierakstu noraidīšana liegtu iespēju vispār iegūt to
datus, tāpēc tie tiek eksportēti ar **citu nosaukumu**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Cits faila nosaukums, nevis atzīme faila iekšienē, jo informācijai ir jāiztur
nosūtīšana pa e-pastu kā vienkāršs nosaukums. `.csv` galvenē ir norādīts
`raw spectral sensor counts (NOT irradiance)` un brīdinājums, ka vērtības ir salīdzināmas
**viena faila ietvaros** — tieši tam tās tiek izmantotas mērķa balstītā kalibrācijā — un
nevis starp sensoriem. No jaudas atkarīgajās fotometriskajās kolonnās (kopējā jauda, fotopiskais un
skotopiskais lukss, PPFD) tiek ierakstīts **NULL**, nevis integrētas vērtības no skaitījumiem, un darbības
kopsavilkumā ir norādīts `RAW COUNTS`, tādēļ „eksportētais” žurnālā nevar tikt interpretēts kā starojuma intensitāte.

Vecākas **v1.01 / v1.02** ierakstīšanas versijas (tās raksta DAQ-A-SD) neietver atsevišķu laika posmu katram nolasījumam,
tikai faila ierakstīšanas laiku. Attēla↔lejupvērstās starojuma plūsmas saskaņotājs tos joprojām noraida —
kadra saskaņošana ar ierakstīšanas laiku būtu nepamanāmi kļūdaina —, taču eksportētājs tos nolasa, un
CSV-failā tiek izdrukāts `clock=daq_created_on`, tādējādi norādot, uz kāda pulksteņa tas atrodas.

### Piezīmes

- `process` automātiski nosaka, vai jūsu mape ir Survey3, „LATTICE” vai jaukta.
- Progresa informācija tiek pārraidīta, izmantojot Server-Sent Events; CLI rāda reāllaika progresu katram pavedienam (detektēšana, analīze, apstrāde, eksportēšana).
- Linux/„Jetson” gadījumā CLI pārbauda apmaiņas atmiņu un var parādīt brīdinājumu pirms lielu mapju apstrādes. Tekstūru atpazīstošais debayer arī automātiski piemēro GPU frekvences ierobežojumu zema enerģijas patēriņa „Jetson” ierīcēm (Nano, Orin Nano).
- Veiksmīgas apstrādes gadījumā tiek ziņots, cik daudz attēlu produktu tika saglabāti (`Image products written: N`, savukārt `(plus M clamp masks)` — gadījumā, ja atdalīšanas procesā tika ierobežots kāds pikselis).
- **`process` droši koplieto darbojošos backend.** Tas automātiski pārstartē jau darbojošos backend tikai tad, ja šis backend atrodas dīkstāves režīmā ar iepriekš ielādētu projektu un nesatur ne kameras, ne masīvus, ne DAQ. Aizmugurējo moduli, kurā ir kāds no šiem elementiem (GUI, kas pārraida masīvu, SDK skripts, DAQ ieraksts), izmanto tādu, kāds tas ir, to nekad nepārstartē, tādējādi `process` var darboties vienlaikus ar aktīvu masīvu. Ja aizmugurējais modulis ziņo par notiekošu darbību, `process` gaida līdz 15 sekundēm, līdz tā beidzas (tikko pabeigta darbība tiek iztīrīta aptuveni vienas sekundes laikā), tad atgriež kļūdu `Backend is mid-run (processing). Wait for it to finish, or pass --restart to force a fresh backend (this kills the in-flight run).`. Globālais karodziņš `--restart` joprojām piespiež izmantot jaunu backend un pārtrauc visas tajā notiekošās kameru, masīvu vai DAQ sesijas. Backend, ko palaida kā Linuxa systemd pakalpojums, nekad netiek pārstartēts: `process` to izmanto, un `--restart` tā vietā izvada `sudo systemctl restart <unit>` (skatīt [Aizmugurējās sistēmas darbināšana kā pakalpojums](#running-the-backend-as-a-service-headless)). Tāpat arī nav Chloros lietotnes paša backend: ja lietotne ir atvērta, `process` to izmanto, ielādējot savu projektu lietotnes projekta vietā (backend, kas tiktu palaists tās vietā, bloķētu atvērto lietotni).
- **Darba cikls, kas jau ir sācies, saglabā savu projektu.** Kamēr notiek apstrādes darbs — ko sākusi lietotne, `process`, SDK skripts vai `chloros-mcp` — backend atsakās izveidot, atvērt, importēt, pievienot vai noņemt failus, pārvietot, dzēst vai mainīt projekta apstrādes iestatījumus, atgriežot HTTP 409 kļūdu `PROCESSING_IN_PROGRESS` un `A processing run is in progress on project '<name>', so ... now is refused`; ja tiek saņemts `process`, kas to izbeidz, tiek izbeigta `1`. Ekrāna iestatījumi (sīktēlu izmērs) joprojām tiek saglabāti. Apstrādes režīms un projekta mērķa atstarošanas iestatījumi tiek noraidīti tādā pašā veidā (apstrādes cikls saglabā režīmu, kādā tas tika uzsākts). Projekta izpildes laikā saglabāts uzņēmums (no lietotnes, CLI, SDK vai `chloros-mcp`) tiek saglabāts kā parasti, bet pievienojas projekta failu sarakstam tikai tad, kad izpilde beidzas; tas ir norādīts atbildē (`project_add`), tāpat kā gaismas sensora ieraksts, kas apturēts projektā. Iepriekš otrs klients varēja pārslēgties uz atvērtu projektu darbības laikā, un notiekošais darbs tika eksportēts uz citu projektu.
- **Ctrl+C aptur izpildi aizmugurē, nevis tikai CLI.** CLI lūdz backend pārtraukt apstrādi, izdrukā `Waiting for the backend to stop (Ctrl+C again to stop waiting)...` un gaida līdz 120 s, lai saņemtu `Processing stopped.`, tādējādi `chloros-cli process … && next` neuzsāk `next` aizmugurē, kas joprojām ir aizņemta ar izpildi. Otrs Ctrl+C pārtrauc gaidīšanu. `process` pēc tam iziet no `130`, tāpat kā jebkura pārtraukta komanda.

#### Izpilde, kas neieraksta attēlus, beidzas ar kļūdu

Ja jūs pieprasījāt produktus, bet izpilde ierakstīja **nekādu** — tikai `project.json` un
`calibration_data.json` — `process` to uzskata par kļūdu: tas izdrukā
`Processing finished but wrote no image products.` un **iziet ar rezultātu, kas nav nulle**, tādējādi skripts to var
konstatēt. Ziņojumā ir norādīta projekta mape un parastie iemesli:

- ievades mape netika atpazīta kā uzņemums (pārbaudiet izkārtojumu un `--input-level`), vai
- visi pieprasītie produkti tika izlaisti kā nepiemēroti šīm kamerām (piemēram, pieprasot
  starojuma/atstarošanas no kamerām, kas atbalsta tikai RGB).

Palaidiet atkārtoti ar `--verbose` un pārbaudiet aizmugures žurnālu, meklējot rindas `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`,
kas paskaidro katras kameras izlaišanas gadījumus, kuri citādi neparādās CLI izvades datos.

Apzināta darbība, kurā tiek apstrādāti tikai metadati — visi produkti ir atspējoti un netiek izmantots `--indices` — joprojām tiek uzskatīta par
**veiksmīgu**, jo tukša attēla izvade šajā gadījumā ir pareizais rezultāts.

#### Darbība, kuras eksportētos failus nevarēja pārbaudīt, tiek uzskatīta par neveiksmīgu

Pēc darbības `process` pārbauda eksportēto failu tagus ar ExifTool. Ja ExifTool nevarēja darboties, tas
izvada `Eksporta pārbaude NEIZDEVĀS: ExifTool nevarēja darboties, tāpēc N no M eksportēto failu tagi
netika pārbaudītas (skatiet [EXIFTOOL] rindas aizmugures žurnālā)` and `Rezultāti ir saglabāti un paliek
savā vietā; palaidiet procesu atkārtoti, lai tos pārbaudītu.`, does not print `Apstrāde pabeigta!`, un **iziet ar rezultātu, kas nav nulle**.
Rezultāti tiek saglabāti.

#### Kādi attēli tiek iekļauti izpildes rezultātos

Tiek iekļauti attēli, kas saglabāti pirms `process` izpildes. Attēli, kas saglabāti izpildes laikā, netiek iekļauti; `process`
brīdina, cik daudz attēlu tika izlaisti, un, izpildot rīku atkārtoti, tie tiek iekļauti.

#### Darbības cikls, kurā zaudēts attēls, beidzas ar kļūdu

Ja attēla iegūšana izpildes laikā neizdevās, `process` izvada `N image(s) failed and were not exported` ar
visbiežāko iemeslu, neizvada `Processing complete!` un **iziet ar nenulles kodu**. Pārējie rezultāti tiek
ierakstīti un paliek savā vietā, un aizmugures žurnālā ir reģistrētas visas kļūdas.

Tāpat ir ar **izpildi, kurā tiek izmantots tikai gaismas sensors**: mape ar `.daq` ierakstiem pēc definīcijas nesatur eksportējamos attēlus,
un darbības cikls tiek novērtēts pēc kalibrētajiem `.daq` / `.csv`, kurus tas uzrakstīja tā vietā.
Ja netika ierakstīts **nekas** — visi ieraksti tika izlaisti —, tiek izvadīts
`Processing finished but wrote no light-sensor products.`, katram ierakstam tiek piešķirts nosaukums ar
izlaišanas iemeslu, un **programma beidzas ar rezultātu, kas nav nulle**.

---

## `chloros-cli login`

Autentificējiet šo ierīci ar „Chloros+“ mākoņpakalpojuma kontu. Pieslēgšanās dati tiek droši saglabāti kešatmiņā failā `~/.chloros/user_session.json`.

```
chloros-cli login EMAIL PASSWORD
```

### Piemēri

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (izņemot vai dublējot paroles daļas). Ja tiek saņemts 401 kļūdas kods, CLI automātiski veic atkārtotu mēģinājumu, pievienojot `$$`-pievienotu, pēc tam ar atkārtojumu noņemtu paroles pusi; ja atkārtotais mēģinājums izdodas, sistēma jūs pieteiks un parādīs pareizo vienkāršo pēdiņu sintaksi, kas jāizmanto nākamreiz.

> **Lietošana bez grafiskās saskarnes/ar skriptu: ja sesija nav saglabāta kešatmiņā, tiek parādīts pieteikšanās uzvednis.** Pamata komandas (`process`, `status`, `export-status`, `time-sync`, …) izpilde bez sesijas kešatmiņas pirms turpināšanas pieprasa `Email:` / `Password:` no standarta ievades (stdin). Ja standarta ievade ir aizvērta (cron, systemd vienība), programma nekavējoties beidz darbu ar kļūdu `login cancelled`; ja standarta ievade ir atvērts terminālis vai caurule, programma gaida ievadi. Pirms bezgalvas darba plānošanas katrā datorā vienu reizi palaidiet `chloros-cli login EMAIL PASSWORD`.

---

## `chloros-cli logout`

Dzēš sesijas kešatmiņu un nākamajā izsaukumā piespiež veikt jaunu pieteikšanos.

Iziet no sistēmas, kamēr notiek apstrādes cikls, to neaptur: cikls tiek pabeigts tajā apstrādes režīmā, kādā tas tika uzsākts, un nākamais cikls izmanto standarta apstrādi. To norāda `logout` (`The processing run in progress finishes in <mode> mode; the next run uses standard`), kā arī lietotne, kad izietat no tās.

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Parāda pašreizējo licences līmeni (Iron/Copper/Bronze/Silver/Gold), autentificēto lietotāju un ierīču piesaistīšanas skaitu.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Pārbauda Thread-4 eksporta gaitu reāllaikā. To var droši izsaukt **`process`** izpildes laikā no citas komandu rindas.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

Iestata CLI parādīšanas valodu (atbalsta 38 valodas, tostarp CJK, RTL un Indijas valodas). Vecākās konsolēs, kas nespēj attēlot skriptu, tiek veikta vienmērīga pāreja uz angļu valodu.

```
chloros-cli language [LANG_CODE] [--list]
```

### Piemēri

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Projekta mapes komandas

Šīs komandas pārvalda noklusējuma projekta mapes atrašanās vietu (kopīgu ar GUI).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

`get-project-folder` parāda mapi, kurā tiek saglabāti jauni projekti. Ja konfigurētā mape vairs nepastāv (tā ir dzēsta, pārdēvēta vai atrodas diskā, kas nav pieslēgts), CLI, tāpat kā GUI, izmanto noklusējuma mapi: noklusējuma mape tiek izvadīta uz stdout, brīdinājums ar trūkstošās mapes nosaukumu tiek nosūtīts uz stderr, un iziešanas kods ir `0`.

---

## `chloros-cli update`

Tikai Linux/Jetson. Pārbauda `version_url` no `/etc/chloros/update.conf` un, ja ir pieejama jaunāka versija, lejupielādē atbilstošo `.deb` un nekavējoties to instalē ar `sudo dpkg -i` (bez apstiprinājuma lūguma; `sudo` var lūgt ievadīt paroli). Uz „Jetson“ atjauninājums tiek piedāvāts tikai „JetPack 6“ sistēmai. Windows vidē komanda nedara neko un ziņo: `up to date`: atjauniniet ar jaunu instalētāju.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linux/Jetson vidē CLI katrā palaišanas reizē veic arī **automātisku atjauninājumu pārbaudi** (nebloķējoša, nekad neaizkavē komandu): tā nolasa `/etc/chloros/update.conf`, rezultātu uzglabā kešatmiņā uz 1 stundu `~/.chloros/update_cache.json` un izvada `Update available: vX.Y.Z / Run: chloros-cli update`, ja ir pieejama jaunāka versija. Kļūdas gadījumā un Windows vidē tiek klusi izlaista; `CHLOROS_DISABLE_UPDATE_CHECK=1` to atslēdz. 1 stundas kešatmiņas dēļ versija, kas publicēta šīs stundas laikā, joprojām var tikt uzskatīta par atjauninātu.

---

## `chloros-cli selftest`

Veic 7 posmu ātrtestu: versija, porta pieejamība, aizmugurējās sistēmas palaišana, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), trokšņu noņemšanas modeļa klātbūtne, CUDA+trokšņu noņemšanas gatavība. Tā izmanto jau darbojošos backend vai arī testēšanas nolūkā to palaista un pēc tam aptur. Tā izvada `N/7 checks passed` un neveiksmīgās pārbaudes, un iziet no `0` tikai tad, ja visas septiņas pārbaudes ir izturētas; CUDA+trokšņu noņemšanas pārbaude neizdodas datorā bez CUDA GPU (skatīt [Instalācijas pārbaude](#verify-the-install)).

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP galvenā servera statuss un vadība. Chloros serveris darbojas kā PTP galvenais serveris; „LATTICE” kameras un DAQ-E vienības darbojas kā tā pakļautās ierīces, lai nodrošinātu laika zīmogu saskaņotību starp ierīcēm (aptuveni 1 ms starp ierīcēm). PTP saskaņo **laika zīmogus**: masīva kameras eksponējas vienlaikus, jo tās iedarbina M8 aparatūras trigers (laiks starp moduļiem ir mazāks par 100 µs), nevis PTP dēļ. `chloros-cli time-sync` bez apakškommandas palaista `status`.

| Apakškommanda | Apraksts |
| --- | --- |
| `status` | Parāda galvenā servera stāvokli, BMCA prioritātes, pulksteņa identitāti un to, vai tā paketes sasniedz tīklu: `health` ir `OK` (`STOPPED`, ja galvenais mezgls nedarbojas), vai `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` kopā ar `health_detail`, un tad uz stderr tiek izvadīta rinda `WARNING:` (stdout paliek JSON formātā). `sync_sent` / `announce_sent` skaita tikai tos ziņojumus, kas tika nosūtīti; `*_send_errors` un `last_send_error` skaita tos, kas netika nosūtīti. Pēc tam, kad kameras savienojums pārtrūkst un atjaunojas, galvenais mezgls pats atjauno savus soketus (`rebinds`, `last_rebind`). |
| `peers` | Saraksts ar pakalpiņiem, kas redzami caur Delay_Req (kameras + DAQ-E sensori). |
| `cameras` | PTP darbības stāvoklis katrai kamerai (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Grandmaster procesa pārstartēšana. |
| `set-priority --priority1 N --priority2 N` | BMCA prioritāšu pārrakstīšana. |

Grandmaster veic paziņojumus kameru tīkla interfeisos (jebkurā adapterī, kura adrese ietver `169.254.x.x`). Ja neviens no tiem nav pieejams, tas izmanto tikai noklusējuma maršruta adapteri, ja vien tas ir vadu savienojums, nekad ne Wi-Fi vai virtuālo adapteri (Tailscale, WSL), jo neviena kamera vai DAQ-E neatrodas aiz tā. Ja nav ko paziņot, tas darbojas bez soketiem (`health` `NO_SOCKETS`), un nākamā kamera vai DAQ savienojums to atjauno uz tīkla interfeisa, kas tajā brīdī ir aktīvs; `time-sync restart` to dara uzreiz.

Grandmaster darbojas savā atsevišķā procesā, blakus backendam (`own_process` / `process_pid` failā `time-sync status`), ar augstu prioritāti Windows vidē (`process_priority`), tādējādi noslogots backend (apstrādes cikls, eksports, vairākas komandas vienlaikus) nevar aizkavēt laiku, ko tas reģistrē; agrāk viena šāda aizkavēšanās varēja vienlaikus nobīdīt visu kameru pulksteņus. Šis process beidzas kopā ar backend; ja to nevar palaist, grandmaster darbojas backend iekšienē, kā iepriekš (`own_process` false, un backend žurnālā tas ir norādīts).

### Piemēri

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

„LATTICE“ kameru vadība. Katra apakškommanda tiek maršrutēta caur Chloros backend; backend pārvalda kameru kopu, tādēļ turpmākie CLI izsaukumi atkārtoti izmanto to pašu atvērto rokturi.

### Bieži izmantotās opcijas (kopīgas lielākajai daļai apakškommandu)

| Karodziņš | Apraksts |
| --- | --- |
| `-d, --device N` | Kameras indekss (noklusējums: 0). |
| `-s, --serial SN` | Konkrēts sērijas numurs; pārraksta `--device`. |
| `--serials SN1,SN2,…` | Ar komatu atdalīti sērijas numuri darbībai ar vairākām kamerām. [Pagaidu daudzkameru komandas](#multi-camera-transient-sessions) atver tikai šīs kameras, pirmā no kurām iedarbina slēdzi. |
| `--all` | Darbojas ar katru atrasto kameru. |
| `--exposure US` | Ekspozīcijas laiks mikrosekundēs. |
| `--gain DB` | Pastiprinājums dB. |
| `--pixel-format FMT` | piemēram, `BayerRG8`, `BayerRG12`; `Mono8`, `Mono12` uz M3M. Nosaukums nosaka tā bitu dziļumu kameras paša ģimenē: M3M modelī Bayer nosaukums nosaka Mono formāta dziļumu (`BayerRG8` nosaka `Mono8`), savukārt M3C modelī Mono nosaukums nosaka Bayer nosaukumu (`Mono12` nosaka `BayerRG12`). |
| `--width N` / `--height N` | Attēla izmēri. |
| `--preset {default,high_quality,high_speed,triggered}` | Pielietot iestatījumu priekšizvēli: pikseļu formātu, ekspozīciju, pastiprinājumu un kadru ātrumu (`high_speed`: `BayerRG8`, 5 ms, 6 dB, 60 fps; `high_quality`: `BayerRG12`, 20 ms, 0 dB; `lattice presets` tos uzskaita). M3M ierīcē formāts ir mono ar tādu pašu dziļumu: `high_speed` ieraksta `Mono8`, `high_quality` un `triggered` `Mono12`. Ar to norādītais karodziņš (`--exposure`, `--gain`, `--pixel-format`) ir prioritāra pār iestatījumiem, un iestatījums, kas nav minēts iestatījumos, saglabā kameras paša iestatījumu. Visi brīviedarbojas automātiski, izņemot `triggered`, kas aktivizē kameru, reaģējot uz aparatūras signālu 2. līnijā — ja šajā līnijā nav signāla, kamera gaidīs bezgalīgi, nevis veiks uzņemšanu. |
| `-o, --output DIR` | Izvades katalogs (noklusējums: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paketes izmērs. `auto` palaista ICMP+GVSP pārbaudes; `jumbo` = 9000; `standard` = 1500. |

### LATTICE kameras pirmās savienošanās darbplūsma

```bash
# 0. A backend must be running: the Chloros desktop app (Windows),
#    `chloros-backend`, or the Linux service (see "Start the Backend")

# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Full sensor (no binning, no ROI); the frame rate falls to
#        what the host and wire deliver, and the faster options are
#        printed
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Apakškommandu atsauces

#### Atklāšana un informācija

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice info` | Uzskaitīt pieslēgtās kameras (ražotājs, modelis, sērijas numurs, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Analizē uzņēmēj sistēmu, lai noteiktu optimālo kameras konfigurāciju. `--no-discover` izlaiž kameru atklāšanu (ātrāk, analīze tikai ar tīkla karti). |
| `lattice network [--fix] [--estimate] [--cameras N] [--pixel-format FMT]` | Pārbauda/labo tīkla kartes iestatījumus (Linux sistēmās — arī kodola soketa uztveršanas buferus — skatīt [Linux hosti](#linux-hosts--socket-receive-buffers)); aprēķina joslas platumu/FPS. |
| `lattice network-analysis --master SN [--slaves SN1,SN2,…] [--width N] [--height N] [--pixel-format FMT] [--binning {1,2,4}] [--force-tier TIER] [--packed {auto,on,off}] [--backend-url URL] [--json]` | Stable-schema aizmugures tīklaspēja + masīva ieteikums (atgriež `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `--width` / `--height` pēc noklusējuma ir pilnais sensors, `2048` × `1536`. Tas beidzas ar `0` attiecībā uz `ok`, `auto_capped_fps` un `auto_shrunk`, kā arī `1` uz `needs_force_slip` un `error`, ar vai bez `--json`. Tas sasniedz backend pie `--backend-url` (noklusējuma vērtība `http://127.0.0.1:5000`), nevis `CHLOROS_BACKEND_URL`. `auto_capped_fps` saglabā pieprasīto izšķirtspēju, bet ierobežo mērķa kadrus sekundē — nolasiet `recommended.recommended_target_fps` un nododiet to kā savienojuma mērķi; uzskatiet to par veiksmīgu, nevis kļūdu. `auto_shrunk` parādās tikai tad, ja pieprasīto kadru nevar piegādāt ar nekādu kadru ātrumu; savienojums nekad pats no sevis nesamazina kadrus. |
| `lattice analyze-array [--models M1,M2,…] [--binning {1,2,4}] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--packed {auto,on,off}] [--json]` | „What-if” analīze, neatraujot kameras (`--width` / `--height` noklusējums `2048` × `1536`). Gan šis, gan `network-analysis` prognozē to, ko „connect” pārraidīs: bez `--pixel-format` tiek piemēroti savienojumam raksturīgie formāti katrai kamerai (Mono12 / BayerRG12), bet ar `--packed auto` (noklusējums) — lēmums, ko savienojums pieņemtu šajā datorā attiecībā uz [saspiestu 12 bitu](#packed-12-bit-transport---packed) lēmums, ko „connect” pieņemtu šajā datorā — tātad norādītais fps ir ātrums, ar kādu darbojas masīvs. Abi izvada saspiešanas lēmumu. **`--n-active` ir kopējais kameru skaits vadā, nevis tikai šajā masīvā**— palieliniet to, ja vienlaikus straumē atsevišķas kameras vai ja vadu jauda tiek aprēķināta, pamatojoties uz pieprasījumu, kurā tās ir nepietiekami ņemtas vērā (noklusējums: `len(--models)`). Vienmēr izvada apkopotās rindas `Wire budget:` (pieprasītie MB/s pret sadursmju drošo maksimumu) un `Max cameras:`, kā arī atzīmē `** OVER-SUBSCRIBED**`, ja masīvs pārsniedz vadu kapacitāti — skatīt [Masīva fps un pārslogojuma modelis](#array-fps--burst-model). |
| `lattice gpu` | Parādīt GPU statusu. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Pārbaudīt vai atjaunināt kameras programmaparatūru. Vietējā `.fwa` izvēle ir fiksēta: fails `firmware/<MODEL_PREFIX>/`, kas atbilst izstrādes `MIN_FIRMWARE_VERSION`, tiek ierakstīts, ja tas ir pieejams (tikai augstākā versija kā rezerves variants), tādējādi jaunāks ražotāja attēls, kas sagatavots diskā, paliek neaktīvs, kamēr šis fiksējums netiek mainīts — apzināti jaunākas versijas nonāk ierīcēs caur parakstītu AWS manifestu, kas ir vēlams, ja versija ir jaunāka. |
| `lattice presets [--apply NAME]` | Uzskaitīt vai piemērot kameras iestatījumus. `--apply` nosaka, kādi ir iestatījumu nosaukumi (to pikseļu formāts, ekspozīcija, pastiprinājums, kadru frekvence, izraisītājs), un izdrukā šos iestatījumus tā, kā kamera tos nolasa pēc tam, M3M ierīcē — iestatījuma dziļuma Mono formāts; kadru ātrums, ar kādu tā darbojas, kas var būt zemāks nekā pieprasa iestatījums (`high_speed` pieprasa 60 fps; pilnkadra kamera ar 1 Gbps savienojumu darbojas apmēram ar 35). Visi pārējie iestatījumi, ko programma „open” ierakstīja, tiek atjaunoti tā, kā tie tika atrasti. `--apply default` atjauno kameras rūpnīcas noklusējumus: rūpnīcas iestatījumus ar brīvi darbojošos automātisko ekspozīciju 2. līnijā, savienojuma caurlaidspējas ierobežojumu un defektu korekciju izslēgtu, kā arī saglabātu RGB-kameras kalibrēto balansa iestatījumu (visos pārējos gadījumos — vienots). Nekas netiek saglabāts kameras atmiņā, tāpēc nākamajā ieslēgšanas reizē tā sāk darbu no savas atmiņas tāpat kā iepriekš. Kā uzņemšanas vai savienošanas komanda `--preset`, `default` nav iepriekš iestatīta. |
| `lattice status` | Kameras reāllaika stāvoklis. |

#### Uztveršana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice capture [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--no-daq] [--daq-tolerance-ms MS]` | Vienu kadru. **Pēc noklusējuma saglabā visus eksporta veidus**(`--processing all`); skatiet [Ierakstīšanas eksporta līmeņi](#capture-export-levels-the-all-default). `--processing` pieņem `all`, `raw`, `debayered`, `radiance`, `reflectance` vai `display` (`preview` tiek pieņemts `display`); jebkurš cits nosaukums tiek noraidīts, pirms kamera tiek pieskārsta (iziet no `1`). `--levels` saglabā konkrētu apakškopu (pārraksta `--processing`). Uzņemšanai tiek atvērta kamera, kuru Chloros nepārvalda, un tās ekspozīcija vispirms tiek iestatīta atbilstoši ainai (ne vairāk kā dažas sekundes), tāpat kā to dara [pagaidu komandas](#multi-camera-transient-sessions); kad uzņemšana beidzas, neatkarīgi no tā, kā tā beidzas, šī kamera tiek atjaunota tādā stāvoklī, kādā tā tika atrasta (formāts, ekspozīcija, krāsu apstrādes ķēde, visi uzņemšanas laikā mainītie iestatījumi — saraksts sadaļā [Vairāku kameru (pagaidu sesijas)](#multi-camera-transient-sessions)). DAQ, kas nolasa izmantoto uzņemumu, tiek saglabāts kā `.daq` sidecar fails, kurā iekļauts tikai neapstrādāts uzņemums; `--no-daq` / `--daq-tolerance-ms` to pielāgo ([DAQ, kas nolasa uzņemumu, saglabā](#the-daq-reading-a-capture-saves)). `--force-daq` joprojām tiek pieņemts. `--jpeg-quality` = JPEG kvalitāte 1–100 (noklusējums 95).**Faili, kas saglabāti atvērtā projektā, tiek pievienoti tam.** Tāpat kā lietotnē, uzņemums, kura faili nonāk Chloros atvērtā projekta mapē, tos pievieno šī projekta failu sarakstam; faili, kas saglabāti citur, paliek nemainīgi. Komanda izdrukā vienu rindu, kas to norāda (`Project: ...`), vai, ja apstrādes cikls jau ir sācies, ka tie tiek aizturēti un pievienoti, kad cikls beigsies. Tāpat rīkojas arī `multi-capture`, `array-capture` (arī `--smart`) un `project capture`. |
| `lattice continuous [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--queue-depth N] [--no-daq] [--daq-tolerance-ms MS]` | Pārraida uz disku, līdz tiek nospiests Ctrl+C. `tiff` apstrādes ciklakadri veido `raw` failu — masīvu, ko no šīs kameras ieraksta: sensora mozaīka, viena plakne ar savām vērtībām, nesaspiesta, ar kalibrēšanas paketi (2048 x 1536 12-bitu kadrs ir 6,0 MiB plus pakete, neatkarīgi no ainas), tāpēc `process` sāk darbu no tiem. `png` un `jpg` satur 8 bitus, tādēļ šie ieraksti ir demosaicēti kadri apskatei, no kuriem `process` nesākas. Ja ir piesaistīts DAQ (`daq pool-bind`) vai apvienotiem datiem katram kadram tiek piešķirts savs `.daq` un `.match.json` sērijas `daq/` mapē, jo `lattice capture` saglabā vienu; `--no-daq` to atslēdz, un, nospiežot Ctrl+C, tiek izdrukāts, cik daudz. Katrs kadrs tiek ierakstīts vienu reizi ar pilnīgiem metadatiem; nospiežot Ctrl+C, jau uzņemtie kadri tiek ierakstīti, pirms kamera tiek atbrīvota. Programma pati apstājas, kad disks piepildās, izdrukā `Failed:` / `Disk full:` un iziet no programmas ar kodu `1`; jebkurš kadrs, kuru programma nevarēja ierakstīt, arī izraisa programmas iziešanu ar kodu `1`. Kamera, kas atstāta gatavībā darbībai ar aparatūras slēdzi (`TriggerMode` `On`) uzņemšanas cikla laikā tiek iestatīta brīvā režīmā. Kad cikls beidzas, kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta — tās izraisītāja iestatījumi un visi ciklā mainītie iestatījumi (ekspozīcija, pastiprinājums, formāts, …) — izņemot gadījumus, kad tiek izmantota funkcija `--keep-settings`, kas saglabā uzņemšanas sesijas iestatījumus kameras iekšējā atmiņā (brīvā režīmā), vai `--clear-settings`, kas atjauno šīs atmiņas rūpnīcas iestatījumus. Sērija, kurā netiek ierakstīts neviens kadrs, tiek uzskatīta par kļūdu: ja 10 sekundes nav neviena kadra, tā pati apstājas, izdrukā `No frames:` ar iemeslu un iziet no programmas ar `1`. Kadri, kurus ierakstītāji nespēja apstrādāt, tiek uzskatīti par zaudētiem, un pārtraukšanas brīdī tiek izvadīts `Dropped:` ar iemeslu (kameras kadru ātrums, ierakstītāju ātrums un rinda); zemāks kadru ātrums vai 8 bitu pikseļu formāts ļauj saglabāt visus kadrus. Komanda sazinās ar backend katras 2 s; ja tā beidz darbību, neapturot ierakstīšanu (`kill -9`, atmiņas trūkums), backend to aptur 30 s pēc pēdējās saziņas un atgriež kameru (`stop_reason` `client_gone`, kā arī ieraksts aizmugurējās sistēmas žurnālā). `multi-stream` un `align-stream` darbojas tāpat. Sākums, kurā kameras atvēršanai nepieciešams ilgāks laiks nekā komanda gaida (60 s; 180 s gadījumā ar `multi-stream` / `align-stream`) — kamera joprojām uzsāk darbību — tiek atcelts ar backend palīdzību: kamera tiek atgriezta sākotnējā stāvoklī un aizvērta, un ierakstīšana netiek turpināta (HTTP 504 `CLIENT_DEADLINE_PASSED`). |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Pārlūkprogrammas balstīts MJPEG tiešraides priekšskatījums: izveido savienojumu ar kameru, parāda straumes URLu un atver to jūsu pārlūkprogrammā. `--ae-damping` iestata automātiskās ekspozīcijas amortizāciju (0,4–100). |

##### Tiešraides (MJPEG; bez RTSP)

Chloros nodrošina tiešraides video kā **MJPEG pār HTTP**(`multipart/x-mixed-replace`, robežvērtība `frame`) no servera.**RTSP netiek atbalstīts.** Atveriet URLu pārlūkprogrammā vai nolasiet to ar jebkuru MJPEG klientu (OpenCV `cv2.VideoCapture(url)`, ffmpeg, VLC). Kamera vispirms ir jāpievieno backendā (`lattice cam-connect`, `lattice array-connect`, `lattice viewer` vai GUI).

| URL (`http://127.0.0.1:5000` + …) | Plūsma |
| --- | --- |
| `/api/camera/stream/<serial>` | Ekrāna priekšskatījums, līdz aptuveni 30 kadriem sekundē. Masīva elementam `?max_h=` (noklusējums `720`) un `?jpeg_q=` (noklusējums `80`) nosaka augstumu un JPEG kvalitāti. Šī ir URL, ko atver `lattice viewer`. |
| `/api/camera/<serial>/stream-annotated` | Tas pats priekšskatījums ar iezīmētiem GUI pārklājumiem: `zebra=1`, `crosshair=1`, `grid=3x3` (vai `9x9`), `histogram=1`, `peaking=1`, `spot=1`; ja nav pieprasījuma, tiek izmantoti projekta saglabātie pārklājumu iestatījumi šai kamerai. |
| `/api/camera/<serial>/stream-reflectance` | 8 bitu atstarojums katrā joslā: ρ = pikselis ÷`X-Chloros-Pixel-Scale` galvenes (255, ja vien nav iestatīts `refl_max`). `fps` (noklusējums `10`), `jpeg_q` (noklusējums `95`), `max_h` (noklusējums `0` = oriģinālais), `band=<name>`, `align=1`. JPEG formāts, tātad ar zudumiem. Tiek noraidīts ar HTTP 409 kļūdu, ja kamerai nav atstarošanas koeficienta (RGB kamera, nav kalibrēšanas paketes, nav DAQ rādījumu). |

- **Kadru saskaņošana starp kamerām.**Katra daļa, ko nosūta**masīva elements**, visos trīs plūsmās satur `X-Chloros-Group-Id` (masīva sinhronizētās grupas skaitītājs), `X-Chloros-Frame-Id` un `X-Chloros-Timestamp-Ns` galvenes. Daļas no dažādām kamerām ar vienādu `X-Chloros-Group-Id` nāk no viena un tā paša izraisītāja. Autonomai kamerai nav grupas, un tā nesūta grupas identifikatoru. Pārlūki ignorē katras daļas galvenes; klients, kas pats analizē daudzdaļu plūsmu, tās nolasa.
- **Pauzēts, kamēr ir atvērts masīva skats.** Masīva locekļa `/api/camera/stream/<serial>` nosūta statisku „pauzēts” kadru, kamēr ir atvērta masīva apvienotā tiešraides plūsma (piemēram, GUI masīva skats), un atsāk darbību, kad tā tiek aizvērta.

#### Sensora regulēšana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice configure [{--get N1 N2… \| --set N=V N=V… \| --dump}] [--json]` | Lasīt/rakstīt jebkuru GenICam mezglu. `--set ExposureTime=…` (`Gain=…`) bez tā `ExposureAuto` (`GainAuto`) slēdžiem, kas vispirms izslēdz automātisko režīmu, kā to dara `lattice exposure --set`; ja kamera noraida vērtību, uz stderr tiek izvadīts `Failed to set <node>: …` un programma beidz darbu `1`. `--get` un noklusējuma grupētais skats izdrukā `note:` zem `ExposureTime` / `Gain`, kamēr darbojas automātiskais režīms — skatīt [Ekspozīcijas un pastiprinājuma nolasīšana automātiskajā režīmā](#reading-exposure-and-gain-under-auto-mode). |
| `lattice exposure [{--auto \| --auto-once \| --off \| --set US}] [--brightness 0-255] [--brightness-auto] [--damping 0.4-100] [--upper-limit US]` | Ekspozīcija un AE. `--set` ekspozīcija vai `--brightness` mērķis tiek saglabāts kā jūsu (Chloros automātiskā ekspozīcijas kontrole to nemaina); pastiprinājums paliek nemainīgs, tāpēc automātiskajā režīmā tas turpina pielāgoties mērķim, līdz jūs iestatāt `lattice gain`. `--auto` atgriež ekspozīciju, `--brightness-auto` atgriež mērķi Chloros automātiskajam mērķim. Ja nav iestatīti nekādi karodziņi, tas nolasa ekspozīcijas mezglus, izmantojot to pašu `note:` kā `configure --get`. Ja kamera noraida `--set` vērtību, tā izdrukā `Failed to set ExposureTime: ExposureTime=<value> refused (<camera error>); the camera accepts <min>..<max> now (ExposureAuto put back to <mode>)` uz stderr — ja automātiskais režīms, kurā rakstīšana bija izslēgta, tiek atjaunots, nevis atstāts izslēgts — netiek izvadīta `Exposure: … us (auto OFF)` rinda, un programma beidz darbu ar `1`. |
| `lattice gain [{--auto \| --off \| --set DB}]` | Pastiprinājums un automātiskais pastiprinājums. Ja `--set` vērtība ir tāda, ka kamera atsakās izvadīt `Failed to set Gain: …` uz stderr, netiek izvadīta `Gain: … dB (auto OFF)` rinda un programma tiek izbeigta ar `1`. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N]` | Sensora ROI un pikseļu apvienošana. Pikseļu apvienošana vienmēr ir **vidējā** — skatīt zemāk. |
| `lattice format [--set FMT] [--list]` | Pikseļu formāts. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Aparatūras/programmatūras trigers. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (bez karodziņiem = vienreizējais balansa iestatījums) | Balansa iestatīšanas darbības. Tikai RGB/Bayer kamerām; mono M3M gadījumā — bezdarbība (izlaista). Vienreizējā darbība saglabā noteikto baltā balansu (`BalanceWhiteAuto` un pastiprinājumus); visi pārējie iestatījumi, kuru vērtības tiek rakstītas atklātā veidā, tiek atjaunoti sākotnējā stāvoklī, un tas viss notiek, ja vienreizējā darbība nevar tikt izpildīta. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct 2000-10000] [--get]` | RGB attēla krāsu apstrādes ķēde. `natural` (noklusējums) ir vienkāršs reāllaika apstrādes variants; `enhanced` pievieno krāsu malu noņemšanu + dzīvīgumu + CLAHE lokālo kontrastu, lai iegūtu pilnīgu „hub-parity” izskatu, kas izmaksā apmēram divas reizes vairāk uz vienu kadru, tādējādi samazinot **reāllaika** kadru ātrumu — saglabātie attēli vienmēr tiek apstrādāti pilnībā neatkarīgi no apstākļiem. Tikai RGB/Bayer kamerām; izlaists mono M3M. |
| `lattice color [--saturation 0-200] [--contrast 0-200] [--reset] [--get]` | Reāllaika priekšskatījuma piesātinājums/kontrasts (kameras ar RGB filtru); `100` ir neitrāls, `--reset` abus iestata uz 100. Tas neietekmē saglabātos attēlus. Tiek izlaists mono M3M kamerās. |
| `lattice highlights [{--set -100..0 \| --reset \| --get}]` (bez karodziņiem = lasīt) | Rāda **spilgtākās vietas** (kameras ar RGB filtriem): samazina spilgtākos, baltākos pikseļus — piemēram, spilgti balta virsma, kas attēlā izskatās kā vienkrāsains balts plankums. `0` = izslēgts, `-100` = visstiprākais. Reāllaika priekšskatījums un attēli ekrāna līmenī to ievēro; starojums/atstarošanās to nekad neievēro, un tas nevar atjaunot sensora datus, kas ir nogriezti (samaziniet ekspozīciju šim nolūkam). Izlaists mono M3M. |
| `lattice filter [--list]` | Uzskaita filtru modeļus, kurus atpazīst Chloros (`RGN-IMX265`, `OCN`, `NGB`, …). Kameras filtrs tiek nolasīts no tās modeļa virknes un to nevar iestatīt: `--set NAME` tikai nosaka modeli, pēc tam iziet no `1` ar `setting the filter model is not supported`. |
| `lattice power [--sleep]` | Pārbauda barošanas/termiskos mezglus; ieslēdz/izslēdz zema enerģijas patēriņa gaidstāves režīmu. `--sleep` atstāj kameru miega režīmā (tās savienojuma caurlaidspējas ierobežojums ieslēgts (`DeviceLinkThroughputLimitMode` `On`) minimālajā līmenī, enerģijas taupīšanas režīms ieslēgts, ja kamerai tāds ir) visus pārējos iestatījumus atjauno tā, kā tie tika atrasti sākotnēji; ja miega režīma aktivizēšana neizdodas, kamera tiek atjaunota pilnībā. Pēc atmodināšanas savienojums tiek atjaunots tādā stāvoklī, kādā tas bija pirms miega režīma aktivizēšanas (ierobežojums, režīms un pakešu aizkave `GevSCPD`, ko kamera uztur 0 vērtībā, kamēr ierobežojums ir ieslēgts; šie dati tiek saglabāti šajā datorā, tādējādi to atrod arī pēc aizmigšanas, kad aizmugurējais serveris ir pārstartēts), vai arī kamerai, kuru šis dators nav ievadījis miega režīmā, bet kura izskatās aizmigusi (tās ierobežojums ir iestatīts uz minimālo līmeni), ar izslēgtu ierobežojumu un `GevSCPD` atstāts uz 0 pie 80 (rūpnīcas iestatījumi); ierobežojums, kas iestatīts virs minimālā līmeņa, paliek nemainīgs. Zonde un atmodināšana atjauno to, ko to atvēršana uzrakstīja. |

> **Binning vienmēr ir vidējais.** `lattice resolution --binning N` iestata vidējo binningu un
> nepiedāvā režīma pārslēgšanu. Sum binning reizinā katru DN ar N², kas izmaksā aptuveni divus stopus
> spilgtuma rezerves uz katru binninga soli; Chloros sadala šo koeficientu atpakaļ pirms
> radiometriskās ķēdes, bet pikseļi, kurus „Sum” ir novadījis līdz sensora maksimālajai vērtībai, tiek nogriezti un tos nevar
> atgūt. Tas nedod nekādu papildu labumu, ko nedod vidējais — indekss ir mēroga neizmainīgs, un starojuma
> skalāri tiek kalibrēti pēc vidējā mēroga. Ja kamera tiek piegādāta jau iestatīta uz summēšanu (ko
> tur atstājis cits rīks vai rūpnīcas iestatījumu atjaunošana), Chloros to atpazīst no mezgla nolasījuma un
> kompensē; palaistot `lattice resolution --binning N`, tas tiek atgriezts uz „Average”. 1.2.0
> `--binning-mode` karodziņš joprojām tiek pieņemts, tādēļ darbojas arī vecāki skripti, taču tiek parādīts tikai brīdinājums:
> pikseļu apvienošana paliek vidējā.

> **Izmaiņas binningā, pikseļu formātā un trigerī kamerā netiek saglabātas.** Kamerā
> backend paliek atvērts un veic straumēšanu (`cam-connect`, GUI), `lattice resolution --binning`
> neietekmē (tiek parādīts, ka binning joprojām ir spēkā, `Binning: 1x1`), un
> `lattice format --set` un `lattice trigger --mode` kamera noraida
> (`SC_ERR_ERROR -1001`). Vispirms atbrīvojiet to (`lattice cam-disconnect -s <SN>`) vai iestatiet
> tos savienošanās brīdī (`cam-connect --pixel-format …`, `array-connect --binning …`).

##### Ekspozīcijas un pastiprinājuma nolasīšana automātiskajā režīmā

Kamēr darbojas kameras `ExposureAuto` (`GainAuto`), tās `ExposureTime` (`Gain`) mezgls saglabā **pēdējo ierakstīto vērtību**, nevis to, ko kamera dara pašlaik. Kamerai, kuras backend ir atvērts (`cam-connect`, `array-connect`, GUI), `lattice configure --get`, noklusējuma skats `lattice configure` un `lattice exposure` (bez karodziņiem) tādēļ parāda mezgla vērtību un zem tās piezīmi ar kameras jaunāko kadru:

```

ExposureTime = 2000.448
  note: ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448); the camera's newest frame was at 4900 us.
```

Nolasiet kadra vērtību no piezīmes; mezgla vērtība ir tikai pēdējā ierakstītā vērtība. Pār HTTP to pašu `POST /api/camera/<serial>/read-nodes` atbildi saglabā `nodes` nemainīgu un pievieno `frame_values` (`{"ExposureTime": <us>, "Gain": <dB>}`, jaunākā kadra) un `notes` (iepriekšējais teikums). Abi ir klāt tikai tad, ja automātiskais režīms nav `Off` un kadrs ir zināms; kamerai, kas atvērta tikai lasīšanai, nav kadra, un tā nesaņem ne vienu, ne otru. `lattice gain` (bez karodziņiem) izdrukā tikai mezglu — izmantojiet `lattice configure --get Gain`, lai izdrukātu piezīmi.

##### Ekspozīcija masīva elementā

Kamēr masīvs veic datu plūsmu, katrs elements darbojas ar **`AcquisitionFrameRateEnable`**, ar kadru ātrumu, kas ir nedaudz lielāks par masīva trigera ātrumu: tā periods ir trigera periods mīnus 15 % (vismaz 3 ms) vai pašas kameras maksimālais ātrums, ja trigers jau ir tuvu tam. Tādējādi `configure --get` uz elementa šajā iestatījumā nolasa `AcquisitionFrameRateEnable` kā `True` un `AcquisitionFrameRate`; aktivizēšana atkal izslēdzas, kad masīvs apstājas.**Nerakstiet šos divus mezglus uz masīva elementa** — masīvs tos sinhronizē ar savu trigeri un pārraksta.

Šis iestatījums ir arī elementa ekspozīcijas maksimums: apmēram 85 ms pie 10 fps trigera (100 ms periods), pie **jebkuras ROI vai binninga**. Pirms versijas 1.3.0 neliels ROI ierobežoja elementa ekspozīciju līdz sensora brīvās darbības periodam (4,9 ms pie 768 × 304), neatkarīgi no tā, cik lēni matrica tika iedarbināta.**Manuāli iestatīts `ExposureTime`, kas pārsniedz maksimālo robežu, palēnina trigeri, lai tas atbilstu šim iestatījumam**, ja tas tiek ierakstīts ar `lattice exposure --set` vai `configure --set` (vai no lietotāja saskarnes). Masīvs vispirms atver šī elementa maksimālo robežu attiecīgajai vērtībai un ļauj lēnākajam ātrumam darboties dažus izraisīšanas periodus, tāpēc ierakstīšana var aizņemt dažas sekundes ilgāk; pēc tam izraisīšana notiek ne ātrāk, kā to atļauj ekspozīcija. Masīvā ar automātisko ātrumu iepriekšējais ātrums atgriežas, tiklīdz visi elementi, kuru manuālā ekspozīcija to bija palēninājusi, atgriežas pie automātiskās ekspozīcijas. Vērtība, ko kamera pati nevar uzņemt, joprojām tiek noraidīta, kļūdas ziņojumā norādot pieļaujamo diapazonu, un dalībnieka automātiskā ekspozīcija tiek atjaunota, ja tā bija izslēgta tikai šīs rakstīšanas laikā. Receptes `apply` ekspozīcija nepalēnina trigeri: tā tiek noturēta pie maksimālās robežas. Chloros nekad neīsina manuālo ekspozīciju, lai pielāgotos ātrākam trigerim; fiksēts ātrums (`array-connect --target-fps F`), kas ir ātrāks par to, ko atļauj ekspozīcija, atstāj šim elementam pietiekami ilgu maksimālo robežu, un šis elements uztver trigerus ne ātrāk, kā to atļauj tā ekspozīcija.

#### Kalibrēšana un sensori

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Kalibrē pēc atstarojuma mērķa. Tiek pārbaudīti līdz pat `--attempts` kadriem (noklusējums `30`); nepilnīgs kadrs (GVSP paketes zudums) izmaksā vienu mēģinājumu, nevis visu ciklu. Kalibrēšana neizdodas, ja tiek sasniegts `no target detected in N attempts`, vai `no complete frame in N attempts: <last error>`, ja neviens kadrs nav saņemts pilnībā. |
| `lattice dls {--connect \| --spectrum \| --irradiance} --mac MAC [--filter NAME] [--camera SERIAL] [--json]` | DAQ-M spektrs un joslu amplitūdas, kas nolasīti caur Bluetooth (BLE) ar backend palīdzību. `--mac` ir paša sensora BLE MAC adrese, un tā ir nepieciešama jebkurai no trim darbībām: nav noklusējuma vērtības, jo katrai ierīcei ir sava (`daq pool-discover --only ble` tās uzskaita); bez tā komanda iziet no `1`. Ja nav norādīta darbība, tiek parādīta palīdzība. `--camera SERIAL` integrē izdrukātos joslu lielumus, izmantojot šīs kameras paša izmērīto spektrālo atbildi, nevis filtra modeļa līkni — RGB kamerām, ja kameras kalibrēšanas pakete ir ielādēta (pievienota) vai saglabāta šī datora kešatmiņā. Virsrakstā ir norādīts, kura līkne tika izmantota: `Band magnitudes (camera <SERIAL>):` vai `Band magnitudes (model <filter>):`, ja kamerai tādas nav (nav RGB kamera, vai nav ielādēts vai kešēts komplekts) un tā vietā tika izmantota standarta līkne. Pār HTTP `POST /api/dls/cli-read` pieņem `camera_serial` un atgriež `band_magnitudes_source`. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Piemēro vinjetes korekciju esošajiem attēliem, katram ar savas lēcas līdzenā lauka (no tā EXIF modeļa). `--lens-model` tiek piemērots attēliem, kuru modelis nenorāda uz kameru; attēls, kura modelis ir pretrunā ar to, tiek izlaists. |

#### Daudzkameru režīms (pagaidu sesijas)

Pagaidu komanda atver kameras vienam darbības ciklam un aizver tās, kad tas beidzas: `multi-capture` bez pievienota pastāvīga masīva, `multi-stream` un `multi-test` šeit, un `align-calibrate`, `align-apply`, `align-stream` un `index --live` zemāk. Šie noteikumi attiecas uz visām:

- **`--serials SN1,SN2,…` izvēlas kameras.** Tiek atvērtas tikai šīs kameras, un pirmā no tām iedarbina slēdzi (attiecībā uz `align-calibrate`, `--reference`, ja tās ir norādītas). Sērijas numurs, kas nav tīklā, izraisa skriptu kļūdu, pirms tiek atvērta jebkura kamera. Bez `--serials` tiek atvērtas visas tīklā esošās kameras. Kamera, kuru jau kontrolē Chloros (lietotāja saskarne, `cam-connect`, `array-connect`), noraida darbības izpildi neatkarīgi no tā, vai `--serials` to nosauc vai nē: tā izmanto to pašu tīklu, kuram ir paredzēta darbība. (`index --live` bez `--serials` tā vietā atver savā profilā aktivizētās kameras: skatīt [Indekss / Veģetācijas matemātika](#index--vegetation-maths).)
- **Kameras izmanto tīklu tāpat, kā to izmanto `array-connect`.** Katra kamera tiek sinhronizēta atbilstoši savai daļai no tā, ko pārraida galvenais savienojums (paketes starpības aizkave, nevis kameras caurlaides spēja, kuras minimālo robežu 31,25 MB/s četras kameras 1 GbE tīklā jau pārsniedz), tādējādi četras kameras darbojas 1 Gbps savienojumā. Ja kameras nevar visas vienlaikus sūtīt datus ar lēnāko sasniegto ātrumu, tās tiek aktivizētas viena pēc otras, katrai izmantojot savu savienojumu, un komanda izdrukā `Serial emit: the wire cannot carry these cameras at once, so each is triggered <ms> ms after the one before; their frames are not exposed together`. Ja pat viena kamera vienlaikus neietilpst, darbība tiek noraidīta, pirms kāda kamera sāk straumēt: `array over-subscribes the wire: <n> cameras demand <x> MB/s but the collision-safe ceiling is <y> MB/s. …`, HTTP 409, iziet `1`.
- **Visām kamerām ir jāpārraida dati, citādi darbība neizdodas.** Kamera, kas nepārraida nevienu kadru, ir kļūda: komanda izvada `Error: … no frame from <n> of <total> cameras: SN:<serial> (<why>), …`, kur `<why>` ir šīs kameras datu ieguves kļūda, pēc tam norādot uz cēloņiem, un iziet no programmas ar `1`. Kadrs, kas saņemts ar GVSP pakešu zudumu (`IncompleteBufferError: GVSP packet loss …`), nozīmē, ka tīkls nespēj pārvadāt kameru kopējo joslas platumu (`Every missing frame arrived with GVSP packet loss: …`): pārbaudiet galvenā datora un komutatora augšupsaistes ātrumu (`lattice network`), izmantojiet jumbo kadrus, vai atveriet mazāk kameru ar komandu `--serials`. Kamera, kas neko nav nosūtījusi, parasti nesaņem galvenās kameras signālu: pārbaudiet tās M8 sinhronizācijas kabeli, pēc tam palaidiet komandu `lattice multi-detect`. Katras komandas darbība, ja rāmju grupā trūkst kādas kameras, ir norādīta attiecīgajā rindā.
- **Katra kamera tiek atjaunota tādā stāvoklī, kādā tā tika atrasta.** Kad darbības cikls beidzas, katra kamera atgūst to stāvokli, kādā tā bija brīdī, kad cikls to atvēra: `TriggerMode`, `TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, un `LineMode` / `LineSource` uz `Line2` un `Line3` (un trigera ieejas traucējumu novēršanas masīvs nodrošina pakļautā līniju, `LineFilterSelector` / `LineFilterWidth`); tā ir `PtpEnable` (un ar to kopā iestatītais `PtpSlaveOnly` / `PtpDomainNumber`); `ExposureAuto` / `GainAuto` (un `ExposureTime` / `Gain`, kur tas bija iestatīts uz manuālu vērtību); `TargetBrightness`, `ExposureAutoLimitAuto` / `ExposureAutoUpperLimit` / `ExposureAutoLowerLimit`, `GainAutoUpperLimit`, `ExposureAutoDamping`; `BalanceWhiteAuto` (un katrs `BalanceRatio`, kur balansa iestatīšana bija manuāla); `BlackLevelAuto` / `BlackLevel`; `PixelFormat`, pikseļu apvienošana, `Width` / `Height` / `OffsetX` / `OffsetY`; `GammaEnable` (un `Gamma`, kur bija ieslēgta gamma), `LUTEnable`, `ColorTransformationEnable`; `ReverseX` / `ReverseY`; `UserSetSelector`; paketes lielums (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`); ritms, `DeviceLinkThroughputLimitMode` (un `DeviceLinkThroughputLimit`, kur režīms bija `On`), starppaketes aizkave `GevSCPD` un sākuma nobīde `GevSCFTD`, ko katrai kamerai nodrošina masīvs; katras plūsmas datu bloks tiek ieslēgts (`ChunkModeActive`, katrs `ChunkEnable` zem sava `ChunkSelector`); `AcquisitionFrameRateEnable` (un `AcquisitionFrameRate`, kur ieslēgšana bija ieslēgta — ja tā ir izslēgta, ātrums ir tikai lasāms un rāda maksimālo, ko ļauj ritms, tādēļ tas nav iestatījums). Ir norādītas tikai atšķirības: kamera vispirms apstājās, kā arī stimulācijas ātrums un kadru ātrums pirms ekspozīcijas, kurai nepieciešams savs kadru periods (kamera ierobežo `ExposureTime` līdz vienam kadru periodam). Ar `--keep-settings` trigera un sinhronizācijas līnijas vadu savienojumi tiek atjaunoti, kamera darbojas brīvā režīmā, un pārējie dati tiek saglabāti kameras paša atmiņā atbilstoši darbības iestatījumiem; `--clear-settings` tur ievada rūpnīcas iestatījumus.
- **Ekspozīcija tiek iestatīta, pamatojoties uz ainu pirms pirmā kadra saglabāšanas.** Uzņemšanas cikls sinhronizē kadrus grupās un tos izmet, vienlaikus katrai kamerai iestatot ekspozīciju un pastiprinājumu, pamatojoties uz šiem kadriem. Mērķis ir panākt vidējo spilgtumu uz kameras mērķa, nepārsniedzot 1 % kadra izgriešanu; ainas ar spilgtām gaismām netiek pilnībā uzņemtas. Parasti tas aizņem 2–6 kadru grupas un ne vairāk kā 5 s.
  - Katra kamera vērsta uz mērķi, no kura to sāk savienotais masīvs, izmantojot savu filtru: TargetBrightness **40**multispektrālajai kamerai (RGN, OCN, NGB) un**60** RGB kamerai. Tas ir puse no ekspozīcijas mērķa, ko nosaka vērtība 80 (RGB kamerām — trīs ceturtdaļas), tādējādi ir mazāka varbūtība, ka tiks nogriezts spilgts atstarojošais panelis. `--brightness N` (0–255) tā vietā nosaka katras kameras mērķi (`multi-test` nav `--brightness`: tās automātiskā ekspozīcija ir izslēgta). Aizmugurējās sistēmas žurnālā katras kameras mērķis tiek nosaukts: `[transient-ae] AE target per camera (filter default): SN:<serial> <target>, …`.
  - Ekspozīcija tiek noteikta pirmā, līdz kameras paša automātiskās ekspozīcijas robežai. Pastiprinājums attiecas tikai uz to, kas paliek pāri.
  - Noteiktā ekspozīcija un pastiprinājums tiek saglabāti visam darbības ciklam, tādējādi katrs `multi-stream` kadrs tiek uzņemts ar vienādu ekspozīciju. Uzņemšana, kurai jāseko mainīgajam apgaismojumam, pieder pie savienota masīva (`array-connect`), kuras automātiskā ekspozīcija nepārtraukti pielāgojas.
  - `--exposure` un `--gain` tiek saglabāti tieši tā, kā norādīts. Ja ir norādīti abi, neviens kadrs netiek izmests.
  - Kad uzņemšana beidzas, katras kameras automātiskā ekspozīcija tiek atkal ieslēgta.
  - Aizmugurējās sistēmas žurnālā tiek ziņots par rezultātu: `[array-ae] exposure solved in <s> s (<n> warm-up groups): SN:<serial> <ms> ms <dB> dB mean <m>/<target>, …` vai `exposure NOT solved after …` ar iemeslu, kā arī `(not settled)` katrai kamerai, kurai tas netika veikts. Šajā gadījumā kadri joprojām tiek uzņemti.
  - `CHLOROS_TRANSIENT_AE_SETTLE_S` maina 5 s ierobežojumu; `0` to atslēdz (skatīt [Vides mainīgie](#environment-variables)).

**Kamēr Chloros kontrolē jebkuru kameru** (lietotāja saskarni, `cam-connect` vai pieslēgtu masīvu), `multi-stream`, `multi-test`, `multi-detect`, `align-apply`, `align-stream` un `index --live` atteiksies darboties, jo tie atver katru kameru tīklā, un otrā atvērtā atvērtā kameras atkārtota atvēršana var izraisīt aizmugures sistēmas kļūdu. (`index --live` atteicas, ja kamera, kuru tā vēlētos atvērt, jau ir aizņemta: vai nu tā ir `--serials` vai tās profila kameras, vai jebkura kamera profilam, kurā nav norādītas konkrētas kameras.) Aizmugures sistēma atbild ar HTTP 409 kļūdu `CAMERA_HELD`, norādot katru aizņemto kameru un atbrīvošanas komandas (`chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`), un komanda tiek pārtraukta ar kodu `1`. Ja ir pieslēgts masīvs, `array-capture --continuous` to reģistrē `multi-stream` vietā, un `array-capture` (pēc noklusējuma izlīdzināts) saglabā izlīdzinātu kopu `align-apply` vietā; `multi-capture` un `align-calibrate` izmanto savienotu masīvu, kurā atrodas to kameras, nevis tās atver.

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice multi-info [--serials SN1,SN2,…]` | Uzskaita visas kameras ar sinhronizācijas lomām; kopā ar `--serials` — tikai tās, pirmā kā `MASTER`. |
| `lattice multi-capture [--serials SN1,SN2,…] [--format FMT] [--jpeg-quality N] [--processing LEVEL] [--brightness N] [--no-daq] [--daq-tolerance-ms MS]` | Viens sinhronizēts kadrs no katras kameras. **Pēc noklusējuma saglabā visus eksporta veidus**, ja ir pieslēgts pastāvīgs masīvs, un izmantoto DAQ nolasījumu ([DAQ nolasījums, ko saglabā uzņemšana](#the-daq-reading-a-capture-saves)); pagaidu rezerves režīms bez masīva tiek**tikai debayered** (pārējiem vispirms palaidiet `array-connect`). `--brightness` attiecas tikai uz rezerves režīmu: pieslēgta matrica saglabā savu automātisko ekspozīciju. Rezerves režīms nesaglabā `.daq` (`--no-daq` / `--daq-tolerance-ms` tur nedarbojas), un `multi-capture` neņem vērā `--force-daq`. Rezerves režīmā kamera, kas nepiegādā nevienu kadru, neizdodas uzņemt (`Error: multi-capture failed: no frame from …`, iziet `1`), un neviens šīs kadru grupas fails netiek saglabāts. |
| `lattice multi-stream [--serials SN1,SN2,…] [--fps F] [--count N] [--format FMT] [--jpeg-quality N] [--brightness N]` | Sinhronizētie kadri straumē (pagaidu), `--fps` noklusējuma `3`, līdz Ctrl+C vai `--count` grupām. Kadru grupa, par kuru kamera neko nav nosūtījusi, netiek ierakstīta, un trīs pēc kārtas pārtrauc straumi. Ja kāda grupa tika izlaista, darbība beidzas, izdrukājot `Incomplete: <n> frame group(s) not written, a camera delivered nothing (SN:<serial> x<count>, …)`, kā arī `Error: the stream stopped: …`, norādot kameras nosaukumu, kad tā pārtrauca straumi, un iziet no `1`X. Pārtrauc darbību, kad disks ir piepildīts, izdrukā `Failed:` / `Disk full:` un iziet no programmas ar kodu `1`; jebkurš fails, kuru tā nevarēja ierakstīt, arī iziet no `1`. |
| `lattice multi-test [--serials SN1,SN2,…] [--count N] [--ptp-settle SECONDS]` | GPIO sinhronizācijas laika tests (pārejošs), `--count` noklusējuma `10` izraisītāji. Izvada informāciju par to, kā tiek koplietots tīkls (`Wire: each camera paced to <x> MB/s (<n> on a <y> MB/s ceiling, <link> Mbps link)`, un rinda `Serial emit:`, ja kameras tiek iedarbinātas viena pēc otras, kuru laika nobīde tiek iekļauta laika zīmogos), viena `Sync group: master SN:<serial> -> SN:<serial>, …` rinda katrai sinhronizācijas grupai (`(no slaves: not synced)` kamerai bez sinhronizācijas kabeļa, kas tiek aktivizēta patstāvīgi), tad viena rinda katram izraisījumam: `[  0] SN:<serial>=<timestamp> \| …`, ar `SN:<serial>=(no frame)` kamerai, kas šim izraisījumam neko nenosūtīja. Tad `Offsets from master SN:<serial> (<PTP / device (PTP off)> clock, <n> trigger(s)):` un viena `SN:<serial>: mean <±x> us, jitter <y> us` rinda katrai kamerai, katra kamera salīdzinājumā ar galveno kameru viena takts cikla laikā: izraisītājs, kura laika zīmogi apvieno kameru ierīču takts frekvences un PTP (PTP izslēgts vai joprojām sinhronizējas) tiek izlaists, un to skaitu uzskaita `Note:`. Jebkurš izlaists kadrs izraisa testa neveiksmi (`Error: multi-test failed: <k> of <n> frame groups incomplete (SN:<serial> missed <k>); …`, iziešana `1`); trīs nepilnīgi trigeri pēc kārtas pārtrauc to priekšlaicīgi. Tests tiek uzskatīts par neizturētu (iziet no `1`) arī tad, ja mazāk nekā divos trigeros visas kameras ir sinhronizētas ar vienu pulksteni (`… the clocks cannot be compared`), vai ja pulksteņi nekad nesakrīt: PTP pulksteņi atšķiras no galvenā pulksteņa par vairāk nekā 1 ms (plus jebkāds sērijas līmeņa nobīde) (`… PTP is not locked`), vai ierīču pulksteņi, kuru nobīde starp trigeriem pārsniedz šo vērtību. Tā palaista PTP galveno pulksteni, ja tas nedarbojas, ieslēdz katras kameras PTP (kā to dara `array-connect`, neatkarīgi no tīkla konfigurācijas) un pirms pirmā trigera gaida, līdz katras kameras PTP pulkstenis sinhronizējas ar galveno pulksteni un stabilizējas: `PtpStatus` Slave ir pieslēgts vienam galvenajam pulkstenim, atrodas 100 us attālumā no tā un ir stabils 3 s, un katra kamera ir sinhronizēta šādā veidā bez pārtraukuma 120 s — apmēram 2 minūtes pēc ieslēgšanas. Pulkstenis, kas tikko ir sinhronizējies, apmēram 2 minūtes pēc tam, kad tas nolasīts kā pakļautais, atšķiras līdz pat ~200 us, un kameras paša `PtpOffsetFromMaster` to neparāda; 120 s laikā kameras sasniedz stabilu pulksteni ar novirzi ~10 us. Kamera joprojām ir sinhronizēta kopš iepriekšējā testa, kurā PTP palika ieslēgts, kā to redzēja šis backend, skaita no šīs sinhronizācijas brīža, tādēļ, veicot testu uzreiz pēc iepriekšējā, atkārtota gaidīšana nav nepieciešama; kamera, kuru tests ieslēdz, vai kamera, kas atrasta ar ieslēgtu PTP un kuras sinhronizāciju šis backend vēl nav reģistrējis (ieslēgta citur vai pirms backenda pārstartēšanas), gaida savas 120 s, un abu kameru masīvā tās, kas tikko sinhronizējušās, joprojām gaida savu laiku. Kamera, kas iziet no „Slave” režīma, pārsniedz 100 us vai gaidīšanas laikā 3 nolasījumus pēc kārtas (1,5 s) seko citam „grandmaster”, sāk skaitīšanu no jauna; viens vai divi šādi nolasījumi tiek reģistrēti (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`), un skaitīšana turpinās; savukārt „grandmaster” identifikators, kuru nevar nolasīt, tiek uzskatīts par nezināmu, nevis par izmaiņu. Ja visu kameru skaitīšana atsākas vienlaikus (vienas sekundes laikā), tas liecina par „grandmaster” laika virzību, nevis kameru laiku: aizmugures sistēmas žurnālā ir ieraksts `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …`, `ptp_lock` to uzskaita `grandmaster_steps`, un gaidīšanas laiks, kas pēc tam beidzas, sāk savu kļūdu ar `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …`. Šim gadījumam laika ierobežojums netiek pagarināts. `--ptp-settle SECONDS` nosaka izlīdzināšanas laiku izpildei (0–600; `0` — bez izlīdzināšanas gaidīšanas). Gaidīšanas laiks ir ierobežots līdz stabilizācijas laikam plus 90 s (pēc noklusējuma 210 s, vismaz 90 s), un komanda gaida aizmugurējo sistēmu 240 s plus stabilizācijas laiku (pēc noklusējuma 360 s). Tā izvada `PTP: locked after <s> s (each camera locked <settle> s, already locked: SN:<serial>, …; from the grandmaster: SN:<serial> <±x> us, …)`, un aizmugurējās sistēmas žurnālā ik pēc 5 s parādās rinda ar katras kameras stāvokli un nobīdi, cik ilgi tā ir bloķēta un cik ilgi vēl palicis: `[array-sync] PTP at <s> s: SN:<serial> Slave <±x> us for <s> s (<s> s left), … -- <s> s left, the wait gives up at <s> s`. Katra kamera ieslēdzas ar izslēgtu PTP; pēc tam pulksteņi sinhronizējas apmēram 10 s laikā. Pēc tam katra kamera tiek atgriezta tādā stāvoklī, kādā tā tika atrasta testa laikā — tās PTP, automātiskā ekspozīcija un pastiprinājums testa laikā tiek turēti izslēgti, un pārējie iestatījumi (ja vien nav `--keep-settings`) — tātad kamera, kurai PTP ir ieslēgts (`lattice configure -s <serial> --set PtpEnable=True` vai tests ar `--keep-settings`), paliek sinhronizēta, un, tiklīdz šis backend ir konstatējis, ka tā ir sinhronizēta uz stabilizācijas laiku, nākamais tests to vairs negaida. Ja pulksteņi gaidīšanas laikā nesaskaņojas, tests neizdodas pirms izraisīšanas (`PTP: NOT locked …`, iziešana `1`) un norāda, kā rīkoties: kamera, kas nekad nav fiksējusies (`PtpStatus Listening`), nozīmē, ka galvenais serveris to nesasniedz (`time-sync status`); kamera, kas pastāvīgi pārtrauca savienojumu, tiek nosaukta pēc laika, cik ilgi tā bija fiksēta (`… had not been locked for the 120 s settle time … (SN:<serial> locked <s> s, <s> s to go)`); pulksteņi joprojām saskaņojas: pārbaudiet `time-sync status`, un, lai tās būtu sinhronizētas pirms testa, ieslēdziet PTP un atstājiet to ieslēgtu (`lattice configure -s <serial> --set PtpEnable=True` katrai kamerai vai veiciet testu ar `--keep-settings`; tests atjauno atrasto stāvokli, tādējādi tas paliek ieslēgts), pēc tam palaidiet to. |
| `lattice multi-detect [--line LINE] [--json]` | Atrodiet kameras M8 sinhronizācijas līnijā un noklusējuma galveno kameru: RGB-kameru (modeļa nosaukums beidzas ar `-FRGB`), ja tā ir pieslēgta ar kabeli, citādi — pirmo ar kabeli pieslēgto kameru pēc sērijas numura. M8 kabeļi saplūst vienā savienojumā, tādēļ **jebkura pieslēgta kamera var būt galvenā** — uzrakstītais `MASTER` ir noklusējums, nevis faktiskais savienojums. Katra kamera pēc kārtas vadī līniju, un pārējās tiek pārbaudītas, vai tās uztver tās impulsu; kamera, kas jau atrasta pieslēgto kameru grupā, netiek pārbaudīta atkārtoti, tāpēc pilnībā savienotai kameru grupai ir nepieciešams viens pārbaudes cikls. Ja nav `--line`, tiek pārbaudīts `Line2`, tad `Line3`. Izdrukā `MASTER: SN:…`, pa vienam `SLAVE: SN:…` par katru citu savienoto kameru un `(probed on <line>)`; `--json` pievieno galīgo rezultātu (`master`, `slaves`, `no_cable` — kameras, kuras impulss nesasniedza — `line`, un katrai kamerai atsevišķi `results`). Kad process ir pabeigts, katrai kamerai tiek atjaunoti tās iepriekšējie trigera un sinhronizācijas līnijas iestatījumi (tie paši mezgli, kas minēti iepriekš). |

#### Izlīdzināšana

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice align-calibrate [--serials SN1,SN2,…] [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N] [--brightness N]` — plus detektora/saskaņotāja regulatori `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC regulētāji `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, vairāku kadru kombinācija `[--averaging mean\|median\|inlier_weighted]`, ģeometriskie ierobežojumi `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, telpiskie ierobežojumi `[--roi X0,Y0,X1,Y1] [--mask PATH]` un-pakļautā pārrakstīšanas `[--per-cam-override SN:KEY=VALUE]` (atkārtojams) | Aprēķina saskaņošanas profilu no reāllaika kamerām. Noklusējumi: `--method orb`, `--model rigid`, `--frames 3`, `--save alignment_profile.json` (mapē, no kuras to palaidāt), `--max-features 2000`, `--ratio-threshold 0.75`, `--matcher bf`, `--knn-k 2`, `--ransac-threshold-px 3.0`, `--ransac-iters 2000`, `--ransac-confidence 0.99`, `--averaging mean`. (`project align calibrate` ir savi noklusējumi; skatiet [tā tabulu](#project-align-calibrate-options).) `--prefilter` noklusējuma iestatījumi ir `gradient` (malu karte; atbilst GUI/masīva izlīdzinātājam — malas saglabājas visās spektrālajās joslās). `--matcher flann` ir efektīvs, ja ir vairāk nekā ~5000 pazīmju; `--averaging median` ir izturīgs pret vienu neveiksmīgu uzņemšanu, `inlier_weighted` svēršana notiek pēc saskaņojumu skaita; `--lock-scale` projicē uz tuvāko rotāciju (bez mēroga), `--lock-axis` vienu translācijas komponenti iestata uz nulli; `--mask` attiecas uz katru kameru (izmantojiet `--per-cam-override`, lai veiktu iestatījumus katrai kamerai atsevišķi, piemēram, `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` atsakās saglabāt kalibrāciju, kuras reprojekcijas RMS pārsniedz slieksni. `--method manual` ņem savus punktus no `--points PATH`: JSON-objekts `{"<serial>": [[x, y], ...], ...}`, kas norāda katras kameras pikseļu koordinātas vienām un tām pašām iezīmēm, ieskaitot atsauces kameru (vismaz 2 katrai kamerai); katrs no `--frames` tiek aprēķināts, izmantojot šos punktus. Ja kameras atrodas savienotā masīvā, kalibrēšana izmanto šī masīva reāllaika kadrus, un profils kļūst arī par masīva izlīdzinājumu tā uzņemtajiem attēliem. Pretējā gadījumā tiek atvērts [pagaidu masīvs](#multi-camera-transient-sessions): ja kāda kamera nesniedz nevienu kadru nevienā no `--frames`, kalibrēšana neizdodas (iziet no `1`, to nosaucot), un profils netiek saglabāts. |
| `lattice align-apply --profile PATH [--serials SN1,SN2,…] [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N] [--brightness N]` | Uztver vienu izlīdzinātu daudzjoslu kadru. `--bit-depth` pēc noklusējuma pielāgojas kamerai; `--no-crop` saglabā pilnu kadru (aizpildot ar melnu); `--interpolation` (noklusējums `linear`) un `--border-mode`/`--border-value` (noklusējums `constant`/0) kontrolē CPU deformāciju — GPU ceļš jebkurā gadījumā ir bilineārs. Atver [pagaidu masīvu](#multi-camera-transient-sessions): kamera, kas nepiegādā nevienu kadru, neiztur uzņemšanu (iziet no `1`, to nosaucot), un nekas netiek ierakstīts. |
| `lattice align-stream --profile PATH [--serials SN1,SN2,…] [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N] [--brightness N]` | Plūsmai saskaņoti daudzjoslu kadri (tie paši deformācijas regulatori kā `align-apply`), `--fps` noklusējums `3`. Atver [pagaidu masīvu](#multi-camera-transient-sessions): kadru grupa, par kuru kamera neko nav piegādājusi, netiek saglabāta, un trīs pēc kārtas pārtrauc plūsmu. Ja kāda grupa tika izlaista, izpilde beidzas, izvadot `Incomplete: <n> frame group(s) not saved, a camera delivered nothing (SN:<serial> x<count>, …)` un `Error: …`, un iziet ar kodu `1`. Plūsma, kas beidzas ar jebkuru citu kļūdu, arī izdrukā `Error: …` un iziet ar kodu `1`. |
| `lattice align-info --profile PATH [--json]` | Parāda profila informāciju. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Maina slāņu secību. |

#### Rādītājs / Veģetācijas matemātika

```bash
# Offline: compute NDVI from an aligned multi-band TIFF (an RGN camera's bands)
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: open the cameras, capture one aligned set with a profile made by
# `lattice align-calibrate`, and compute the index from it
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --save-multiband -o output/
```

**`--channel` piešķir iestatījumu simboliem joslas** (NDVI: `red`, `nir`), pa vienam `--channel SYMBOL=BAND` katram.

- **`--live`: tikai simbolam, uz kuru kopa nevar atbildēt.** Tāpat kā ar `--input`, iestatījuma simbols, kuru izlaižat, pieņem vienu joslu no kopas (profilā ieslēgto kameru joslas), kuras nosaukums sākas ar to. Tas tiek pārbaudīts pirms jebkuras kameras atvēršanas: simbols, uz kuru nereaģē neviens vai vairāki diapazoni, tiek noraidīts, tos nosaucot, un iziet no `1`. Pielāgotajam `--formula` katram simbolam ir nepieciešams `--channel`.
- **`--live` produkti norāda, kam tie pieder.** `index.tif` un `index_LUT.png` satur informāciju par kameru kopā (ražotājs, modelis, sērijas numurs), tās uzņemšanas laiku (atsauces kameras ekspozīcija, UTC) un objektīvu, un abi nosauc indeksu kā savu vienīgo joslu (`XMP-Camera:BandName` `NDVI`; krāsu PNG faili nosauc indeksu, nevis tā krāsu kanālus), tāpat kā to dara `--input` produkts. Savukārt indekss, kas veidots no vairāku kameru joslām, nosauc katru no tām, un tā `Model` ir `LATTICE` — skatīt [Faili, kas izveidoti no vairākām kamerām](#files-made-from-several-cameras).
- **`--input`: tikai simbolam, uz kuru fails nevar atbildēt.** Simbols, ko izlaižat, tiek piešķirts tai faila joslai, kuras nosaukums sākas ar šo simbolu: `red` pieņem `Red_660` vai `Red_660_RGN`, `nir` pieņem `NIR_850`. Nosaukumi ir ņemti no paša faila: no paša skenējuma joslu saraksta vai, Chloros starojuma / atstarošanas uzņemumā vai `process` produktā — tā `Chloros:Band{R,G,B}Name` / `XMP-Camera:BandName` tagiem. Tātad `lattice index --input <reflectance capture> --preset NDVI` nav nepieciešams `--channel`.
- **Divi spektra joslas, kas atbilst vienam simbolam, netiek atšķirti** (9 kameru kopai ir divas NIR spektra joslas): nosauciet to. Simbols, kuram nekas neatbilst, tiek noraidīts ar `channel_map missing entries`, iziet no `1`.**Joslu nosaukumi kopā** atspoguļo kameras filtru: M3C kameras joslas ir `<band>_<filter>` — `NIR_850_RGN`, `Green_550_RGN`, `Red_660_RGN` uz RGN; `NIR_808_OCN`, `Cyan_490_OCN`, `Orange_615_OCN` uz OCN; `Blue_475_RGB`, `Green_550_RGB`, `Red_625_RGB` uz RGB; `Blue_475_NGB`, `Green_550_NGB`, `NIR_850_NGB` uz NGB — un M3M kamerasvienīgais spektrālais kanāls ir `Mono_<filter>` (`Mono_660`, `Mono_850`). `lattice align-info --profile PATH` uzskaita profila spektrālos kanālus.

Pilnais karogu kopums: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI/NDRE/EVI/SAVI/GNDVI/…), `--formula EXPR`, `--channel SYM=BAND` (atkārtojams), `--capture-level raw|debayered|radiance|reflectance|unknown` (pārraksta avota TIFF failā reģistrēto uzņemšanas līmeni; noklusējums: nolasīts no TIFF metadatiem, izmantojot Chloros faila `Chloros:ProcessingLevel`, tādējādi atstarojuma uzņemšana ziņo `reflectance`), `--output PATH` / `-o` (bezsaistē: ceļš; ja ir attēla paplašinājums, faili tiek nosaukti pēc tā saknes — `-o ndvi_FRGN.png` raksta `ndvi_FRGN.tif` un `ndvi_FRGN_LUT.png` — un bez tā tas ir prefikss, `-o ndvi` -> `ndvi.tif`, `ndvi_LUT.png`; noklusējums blakus ievadei, kā `<input name>_<preset>`; ar `--live` un bez `-o` — mape `output/` tajā mapē, no kuras izpildāt komandu), `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. Ar `--live` tiek piemēroti arī izkliedēšanas regulēšanas slēdži: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`, `--brightness N` (kameru automātiskās ekspozīcijas mērķis, skatīt pārejošo sesiju noteikumus), `--serials SN1,SN2,…`. `--live` atver [pārejošo sesiju masīvu](#daudzkameru-pārejošo-sesiju): kamera, kas nepiegādā nevienu kadru, neiztur pārbaudi (iziet no `1`, to nosaucot), un netiek ierakstīts ne skripts, ne indekss. Tā atver `--serials` (pirmais iedarbina trigeri), citādi profila kameras, kuru `layer_order` ieraksts ir ieslēgts, vispirms atsauces kamera — atsauces kamera pat tad, ja tās joslas ir atspējotas, jo pārējās ir saskaņotas ar tai un tā iedarbina trigeri. Kamera, kuras joslas ir atspējotas, nekad netiek kalibrēta, tādēļ profilā atspējota RGB-kamera vairs neizraisa atstarojuma indeksa kļūdu (`no-absolute-radiance-scale`). Profils, kurā nav nosauktas nekādas kameras, atver visas tīklā esošās kameras, tāpat kā iepriekš.

Katra veidlapa darbojas aizmugurē, tāpēc tai ir jābūt palaistai: palaižiet to no Chloros grafiskās lietotāja saskarnes vai ar komandu, ko CLI izdrukā, ja nevar to sasniegt. Bezsaistē backend nolasa `--input` un pats raksta peldošā punkta TIFF un krāsoto PNG failu, tādējādi relatīvais `--input` vai `-o` tiek saistīts ar mapi, no kuras izpildāt komandu (skatīt [Konvencijas](#conventions)). Abi bezsaistes produkti ietver ieejas datu kameras ierakstu, tāpat kā to dara `process` indeksa eksports: zīmols / modelis / sērijas numurs, GPS, objektīva marķējumi, uzņemšanas laiks (ieejas datu paša; ja tā EXIF neuzrāda nekādu laiku, tad ekspozīcijas brīdis, kādu tas reģistrējis, vai faila nosaukuma laika zīmogs, UTC formātā) un MAPIR kalibrācijas identifikators — nekad ne „raw-DN” kalibrācija, kas neattiecas uz indeksu — kā arī `Chloros:ProcessingLevel=index` un indeksa izteiksme; peldošā punkta TIFF nosauc vienu joslu — iestatījumu. `--list-presets` un `--list-gradients` izdrukā aizmugurējās sistēmas iestatījumu un gradienta tabulas. Formulu, ko aizmugurējā sistēma noraida, vai `--channel` joslu, kādas TIFF failā nav, izved ar aizmugurējās sistēmas norādīto iemeslu.

> **`--channel` simboliem tiek ņemta vērā lielie un mazie burti.** Simbola pusei precīzi jāsakrīt ar iestatījuma kanālu nosaukumiem (iestatījumos tiek izmantoti mazie burti, piemēram, NDVI = `red`, `nir` — pārbaudiet `--list-presets`), un joslas daļai jāsakrīt ar joslas nosaukumu saskaņotajā kopā (vai jābūt 0-bāzes joslas indeksam bezsaistes režīmā). `--channel red=Red_660_RGN --channel nir=NIR_850_RGN` darbojas; `--channel RED=660` nedarbojas, radot `channel_map missing entries` kļūdu.

#### `lattice index` — daudzjoslu kopu maskas

Pēc `--capture-level radiance` vai `reflectance` katras kameras 3×3 atdalīšana var ierobežot joslu līdz 0 (sk. [Atdalīšanas ierobežošanas maska](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on)). Daudzjoslu kopums satur vairāku kameru joslas, un katra kamera ierobežo savu joslu, tādēļ `--live --save-multiband` raksta **vienu masku katrai avota kamerai** blakus kopumam:

```
output/
├── index_multiband.tif
└── clamp_masks/
    ├── index_multiband_SN<serial A>-<filter A>_clampmask.tif
    └── index_multiband_SN<serial B>-<filter B>_clampmask.tif
```

- Katra maska ir vienjoslas `uint8` uz **kopas** pikseļu režģa (izmantojot to pašu izlīdzināšanas deformāciju un apgriešanu), ar šīs kameras pašu bitiem: bits 0 = tās R kanāla josla, bits 1 = G, bits 2 = B. Stāva josla, kas ņemta no kameras *c* kanāla, tiek ierobežota tur, kur ir iestatīts šīs kameras bits *c* (R = 0, G = 1, B = 2).
- Katras joslas iegultā izcelsme nosauc tās kameras masku (`unmix_clamp_mask`, attiecībā pret kopas mapi, vai `none`) un nolasāmo bitu (`unmix_clamp_mask_bit`).
- Kamera, kuras atdalīšanas procesā nekas netika ierobežots, nesaņem failu; mono vai RGB-kamera neveic atdalīšanu un arī nesaņem failu. CLI izvada katru masku zem kopas (`clamp mask SN…: …`); atbilde tās uzskaita kā `clamp_masks`.
- Tās pašas darbības indeksa TIFF/PNG faili tiek aprēķināti no tā paša skata, uz tā paša režģa, tāpēc maskas attiecas arī uz tiem. `--capture-level raw` neveic atdalīšanu un neieraksta masku.
- Stacks un indekss atrodas tieši `--output`, tādēļ maskas tiek ievietotas `--output` pašas mapē `clamp_masks/` — nekad virs jūsu nosauktās mapes. Šajā mapē ir sajaukti dažāda veida produkti (kaudze, indekss, krāsots renderējums): atlasiet tajā esošos failus, nevis nododiet visu mapi fotogrammetrijas programmatūrai.

#### Pastāvīgi savienojumi (Smart-Prep, GUI ekvivalents)

Šīs komandas saglabā kameras atvērtas aizmugures pūlā starp dažādiem CLI izsaukumiem.

| Apakškommanda | Mērķis |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Pievieno vienu kameru pūlam (viena kamera, bez masīva). [Vispārējās opcijas](#common-options-shared-by-most-subcommands) `--exposure`, `--gain`, `--preset`, `--pixel-format`, `--width` / `--height` un `--packet-size` (`standard` = 1500, `jumbo` = 9000 vai baiti; `auto` zondes) tiek piemērotas savienojuma brīdī (`--exposure` vai `--gain` izslēdz automātisko režīmu). Tā atgriežas, tiklīdz ir sākusies kameras datu plūsma (uz to gaida apmēram 15 s, pēc tam 10 s, līdz tiek saņemts pirmais pilnais buferis) un izdrukā `stream: streaming` vai `stream: no_frames -- connected, no frames yet: …`, ja datu plūsma neko nepiegādāja — kamerai, kas izmanto jumbo paketes, ar ieteikumu atkārtoti izveidot savienojumu ar `--packet-size standard`. Kamēr viena kamera izveido savienojumu (pirmais savienojums lejupielādē tās kalibrāciju, aptuveni 70 s), cita komanda, kurai nepieciešama kamera, gaida to ne ilgāk kā 5 s, pēc tam rodas kļūda ar HTTP 503 kodu „`CAMERA_POOL_BUSY`”, norādot kameru, kas izveido savienojumu; pēc brīža to var palaist atkārtoti. „`lattice status`” un kameru saraksts nekad negaida: kamera, kas atrodas savienojuma izveides procesā, tiek parādīta kā savienojas, norādot tās fāzi. |
| `lattice cam-disconnect [--serial SN] [--all] [--keep-settings \| --clear-settings]` | Atbrīvošana. Kamera tiek atjaunota tādā stāvoklī, kādā tā tika atrasta savienošanās brīdī (izraisītāja vadu savienojumi, PTP, iestatījumi); `--keep-settings` tā vietā saglabā savus iestatījumus savā atmiņā, `--clear-settings` atjauno šo atmiņu uz rūpnīcas iestatījumiem (skatīt [Kameras atmiņa](#camera-memory----keep-settings----clear-settings)). Kamera, kas ir masīva loceklis, atbrīvo visu masīvu. |
| `lattice cam-list` | Uzskaitīt kameras pūlā. |
| **`lattice array-connect`**|**Izveido pastāvīgu sinhronizētu masīvu (ieteicamais sākuma punkts).** Izpilda pilnu GUI smart-prep darbplūsmu. Aptuveni viena minūte 9 kamerām; tūlīt pēc kameru ieslēgšanas tas var aizņemt vairākas minūtes. Gaidīšanas laikā tiek izdrukāts katrs solis, ko sasniedz backend, kā `    <s> s: <step>` (piemēram, `    41 s: open SN:214000535 (4 of 9)`), un aizmugures sistēmas žurnālā katram solim ir viena laika rinda (`[array-connect <id>] <step>: <s> s …`) un pēdējā rinda ar lēnākajiem soļiem. Tā gaida līdz 600 s. Ja līdz tam brīdim savienojums nav izveidots, backend to atceļ: katra kamera, ko tā bija atvērusi, tiek atgriezta tādā stāvoklī, kādā tā tika atrasta, un aizvērta, komanda izdrukā `Error:` ar sasniegto soli un iziet `1`, un neviens masīvs netiek savienots (HTTP 504 `CLIENT_DEADLINE_PASSED`). Ctrl+C atceļ savienojumu tādā pašā veidā (`Interrupted: …`); ja masīvs jau bija izveidojis savienojumu, rindā tas tiek norādīts un nosaukts `array-disconnect`, kas to atbrīvo. Komanda, kas tiek pārtraukta gaidīšanas laikā (`kill -9`), savienojums tiek atcelts, ja aizmugurējā sistēma no tās nav saņēmusi ziņu 30 sekundes. Masīvs, kas darbojas, bet kam vēl nav sinhronizētas rāmju grupas, joprojām tiek uzrādīts kā savienots, un tā plūsma ir `no_frames`. |
| `lattice array-disconnect [--array-id ID] [--all] [--keep-settings \| --clear-settings]` | Atbrīvo masīvu. Katra kamera tiek atstāta brīvā režīmā (`TriggerMode` `Off`), saglabājot visu pārējo tā, kā tas bija savienojuma izveides brīdī — tās trigera un sinhronizācijas līnijas iestatījumus (galvenā`TriggerSource` atgriežot to sākotnējā stāvoklī, sinhronizācijas līniju atgriežot kā ieeju), tās PTP un sesijas laikā mainītos kameras iestatījumus — tādējādi viena kameras komanda dalībniekam darbojas uzreiz. `--keep-settings` saglabā katras kamerasiestatījumus tā, kā tie bija sesijas beigās, kameras paša atmiņā; `--clear-settings` atjauno šo atmiņu uz rūpnīcas iestatījumiem. Skatīt [Kameras atmiņa](#camera-memory----keep-settings----clear-settings). |
| `lattice array-list` | Uzskaitīt pieslēgtos masīvus. |
| `lattice array-status [--array-id ID]` | Reāllaika kadru skaits sekundē (fps), PTP, pēdējā kļūda. `ptp_enabled` ir `True` tikai tad, ja visiem masīva elementiem ir ieslēgts PTP; `ptp_off:` norāda uz elementu, kuram tas nav ieslēgts. `state:` rinda nolasa `restarting (applying settings)` iestatījumu atkārtotas uzsākšanas laikā, `recovering (…)` — kad dalībnieks, kuram bija pārtraukta strāvas padeve, tiek atkal atvērts (skatīt piezīmi zemāk), `stalled (…)` — kamēr darbojas datu ieguves cikls, bet netiek publicēta neviena rāmju grupa, un `stopped (…)` — tiklīdz datu ieguves cikls ir pārtraukts (atjaunojiet masīva savienojumu). |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--orient] [--index\|--no-index] [--no-daq] [--daq-tolerance-ms MS] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Viena sinhronizēta uzņemšana no aktīvā masīva — Vienreizēja / Nepārtraukta / Intervāla / Ātrākais. **Noklusējuma iestatījums ir `all`**(viens fails katram piemērojamajam eksporta tipam katrai kamerai),**saskaņots** tāpat kā lietotnes uzņemums (`--no-aligned` oriģinālajiem failiem). Izlaistās kameras (piem., RGB, kas izslēgta no starojuma/atstarošanas) tiek atspoguļotas ar `Skipped: SN:<serial> (<reason>)`; DAQ, kas nolasa izmantoto uzņēmumu, tiek saglabāts kopā ar to, iekļaujot tikai neapstrādātu uzņēmumu, un atspoguļots ar `DAQ: <path>` ([DAQ, kas nolasa uzņēmumu, saglabā](#the-daq-reading-a-capture-saves)). Skatīt [Uztveršanas režīmi, ierakstītāji un pārstrāde bezsaistē](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only] [--expr EXPR]` | Ierakstiet kombinētā indeksa skatu reāllaikā video/GIF formātā (uzraudzības kvalitātē; ja nav atvērts GUI skats, aizmugurējā sistēma renderē masīva NDVI vai `--expr`). |
| `lattice array-burst [--duration S] [--max-frames N] [--orient] [--build] [--products …]` | Neapstrādātu Bayer attēlu sērija ar augstu kadru skaitu (analīzes kvalitāte; pārstrādājama bezsaistē). Tāpat kā `array-record` un `array-capture --continuous`, tā katrā aptaujā sazinās ar aizmuguri: tiek pārtraukta bez apstāšanās (`kill -9`), backend to aptur 30 sekundes pēc pēdējās reģistrēšanās (`stop_reason` `client_gone`; sērijas manifests tiek pabeigts un sākas tās apvienotā indeksa veidošana, tāpat kā apturēšanas gadījumā), un masīvs paliek savienots. |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Saglabāto neapstrādāto sēriju pārstrādā par kalibrētu(-iem) video. |

> **Kamera, kurai pazūd barošana vai savienojums, atjauno savienojumu pati.** Kad kāds masīva elements pārstāj atbildēt (komutatora barošanas cikla pārstartēšana pārstartē visas kameras, kuras tas baro), masīvs to gaida, atkal atver savienojumu, kad tas atkal atbild, un atjauno tai `array-connect` iestatījumus: PTP, trigera un sinhronizācijas līniju, tīkla tempu, pikseļu formātu un kadra izmēru, kā arī automātisko ekspozīciju. Pēc tam tā atkal sāk straumēt bez jauna `array-connect`. Tikmēr `array-status` parāda `state: recovering (...)`, un `last_error` norāda, kuru kameru tā gaida; pēc tam tā izdrukā `re-opened:`. Kamera, kas ir pārstartēta, atgriežas ar izslēgtu PTP, tāpēc, kamēr tā netiek atvērta no jauna, `ptp_enabled` ir `False`, un `ptp_off:` to nosauc.

> **Aizturēta kamera nekad netiek atvērta otrreiz.** Otrreizēja atvēršana aptur turētāja datu plūsmu un pārraksta tās iestatījumus, kā arī var izraisīt aizmugurējās sistēmas kļūdu. Tātad kamerai, ko tur pūls (GUI vai `cam-connect`), masīva elementam vai kamerai, ko tur fona uzdevums (darbojošs `lattice continuous`, `multi-stream` vai `align-stream`, savs vai cita klienta), `lattice continuous`, `lattice capture`, `cam-connect`, `power --sleep`, `calibrate`, vienu-shot `white-balance` un `presets --apply` noraida ar HTTP 409 kļūdu `CAMERA_HELD`, norādot turētāju un atbrīvošanas komandu (`chloros-cli lattice cam-disconnect -s <SN>`, `chloros-cli lattice array-disconnect --array-id <ID>` vai, ja runa ir par uzdevumu, tā identifikators un tas, kurš to uzsāka), un iziet `1`. Tāpat rīkojas kamera, kuru šobrīd izmanto cita komanda (`multi-test` vai `multi-capture`, kas atver savas kameras, vēl viens vienreizējs uzdevums), katrai šeit minētajai komandai: kļūda liek gaidīt, kamēr šī komanda pabeigs darbu. `lattice power` bez `--sleep` izlasa caur turētāja paša rīku, ja tāds ir (pūls, masīvs, uzdevuma atvērta kamera), un tiek noraidīts, ja kameru izmanto cita komanda. Katrā `CAMERA_HELD` atbildē kods ir iekļauts gan `code`, gan `error_code`. Komandas, kas atver visas kameras, skatiet sadaļā [Vairāku kameru (pagaidu sesijas)](#multi-camera-transient-sessions).

##### `array-connect` opcijas

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--serials SN1,SN2,…` | automātiski atklāt visas LATTICE kameras (nepieciešamas ≥2) | Pirmā sērijas numura kamera ir MASTER. Ja šis parametrs netiek norādīts, atklāšana tiek filtrēta pēc LATTICE (M3C / M3M) modeļiem un visas tās tiek savienotas. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO sinhronizācijas līnija. |
| `--target-fps F` | automātiski | Galvenā kameras trigera darbības ātrums. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | automātiski | Apliecināt līmeņu izvēlni. |
| `--wire-ceiling-mbps MB_PER_S` | automātiski noteikts | **Uzņēmējdatora ilgstošais vadu budžets, MB/s — skaitlis, no kura ir atkarīga visa masīva resursu sadale.** Samaziniet to, ja masīvs ziņo par GVSP bojātiem kadriem: automātiskā vērtība tiek aprēķināta, pamatojoties uz tīkla kartes paziņoto savienojuma ātrumu, kas pārspīlē USB adapteru, šauru PCIe joslu un noslogotu koplietošanas tīklu ātrumu. Tiek saglabāts projekta masīva uztveršanas blokā, tādējādi to atjauno, atkārtoti atverot / izmantojot CLI / SDK atkārtotu savienojumu. Skatīt [Masīva stāvoklis](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | nav (pilns sensors) | Aparatūras binning (vienmēr „Vidējais”). |
| `--width N` / `--height N` / `--pixel-format FMT` | pilns sensors; formāts katrai kamerai | Konkrēts kadra izmērs vai pikseļu formāts katrai kamerai, nevis pilna sensora un formāta izvēle katrai kamerai (Mono12 / BayerRG12). |
| `--no-recommend` | izslēgts | Izlaist tīkla analīzes posmu (tad katra kamera saglabā savu pašreizējo izmēru, nevis tiek iestatīta uz pilnu sensoru). |
| `--no-ptp` | izslēgts | Atspējot PTP (tad starpkameru laika zīmogi **nav** salīdzināmi). |
| `--packed {auto,on,off}` | `auto` | Pārraida 12 bitu kadrus saspiestā veidā un atspiež tos šajā datorā — vairāk kadru sekundē, izmantojot pilnu sensora izšķirtspēju, identiski dati. `auto` saspiež tikai tad, ja tas palīdz un šis dators to var atļauties; `off` nekad nesaspiež. Skatīt [Saspiests 12-bitu transports](#packed-12-bit-transport---packed). |
| `--packet-size {auto,jumbo,standard,N}` | `auto` | GVSP paketes izmērs katrai kamerai masīvā. `auto` ļauj katrai kamerai pārbaudīt tīklu; `jumbo` = 9000, `standard` = 1500 vai izmērs baitos to bloķē. Pirms versijas 1.3.0 `array-connect` pieņēma šo karodziņu un to ignorēja. |

> **Noklusējumā tiek izmantots pilns sensors.** Ja nav `--width` / `--height` / `--binning` /
> `--pixel-format`, `array-connect` katru kameru iestata uz pilnu sensoru — bez
> binninga, nav ROI apgriešanas — un ļauj kadru ātrumam samazināties līdz tam, ko nodrošina šis dators un
> tīkls, nevis samazināt kadrus, lai saglabātu ātrumu.
> Tīkla analīzes rindā tiek parādīts prognozētais ātrums, un nākamajā rindā ir uzskaitītas
> ātrākas opcijas (pikseļu apvienošana vai mazāks ROI) ar to prognozētajiem ātrumiem; izvēlieties
> `--binning` vai `--width`/`--height`, lai izvēlētos vienu no tām. „Connect” nekad neveic pikseļu apvienošanu vai apgriešanu
> pats no sevis.

##### Saspiežta 12 bitu pārraide (`--packed`)

Pie pilnas sensora jaudas masīva kadru ātrumu nosaka tas, cik ātri katra kamera var nosūtīt
savus kadrus. 12 bitu formāti (`BayerRG12`, `Mono12`) parasti tiek pārraidīti kā 16 bitu
vārdi; **saspiestā** pārraide nosūta tos pašus 12 bitus 1,5 baitos uz pikseli un
tos izpako šajā datorā, tādējādi katra kadra nosūtīšanai nepieciešams par 25 % mazāk laika —
aptuveni **par 33 % vairāk kadru sekundē, izmantojot pilnu sensora jaudu** (izmērīts: 10,7 → 14,0 fps
9 kameru sistēmā). **Saglabātie dati ir identiski**: tās pašas vērtības, tie paši
16 bitu faili, tie paši metadati.

- `auto` (noklusējuma iestatījums) saspiež datus tikai tad, ja tas palīdz **un** šis dators spēj
  veikt izpakošanu. Pieslēgšanās brīdī aizmugures sistēma izmēra savu izpakošanas ātrumu, procesoru skaitu,
  pašreizējo procesora slodzi un tīkla resursu pieejamību, tādējādi datoram ar brīviem procesoru kodoliem saspiešana notiek
  pie pilna sensora, savukārt noslogotam vai zemas jaudas datoram dati tiek pārraidīti neiespiesti. Maziem ROI un apvienotiem kadriem ieguvums
  nelielu ieguvumu.
- `off` nekad nesaspiež — izmantojiet to, lai izslēgtu saspiešanu diagnostikas laikā vai datorā
  datorā, kam viss procesora jaudas resurss ir jāatstāj citiem uzdevumiem.
- `on` vienmēr saspiež 12 bitu formātus.

„connect” izvade parāda lēmumu un tā pamatojumu (`saspiests 12 bitu: (auto:
prognozēts 11,0 -> 14,4 fps, …)`); `/api/camera/array/status` ziņo
`packed_transport` un `packed_decision`. Lai iestatītu visai mašīnai kopīgu noklusējumu
tā vietā, lai katru reizi nodotu karodziņu, iestatiet `CHLOROS_ARRAY_PACKED12=off` (vai
`on`) aizmugurējās vides iestatījumos — tomēr eksplicīts `--packed on|off` joprojām ir prioritārs.
8-bitu un 16-bitu formāti nekad netiek saspiežti.

### Viens projekta ieraksts, visi režīmi

Projekta mapē tiek glabāti visu ierīču iestatījumi: `cameras.json` — katras kameras (`cameras[].settings`, vienai kamerai un katram masīva elementam) un katra masīva vispārējie iestatījumi (`arrays[].capture`: formāts, ROI, binning, trigera frekvence, vadu budžets) — kā arī `sensors.json`, katra gaismas sensora (integrācijas laiks, kadru vidējais, ierobežojums). Lietotne, CLI, SDK un MCP serveris visi lasa un raksta vienus un tos pašus failus vienā un tajā pašā veidā:

- **Ierīce, kas pieslēgta projektam, tiek saglabāta tajā un reģistrē tajā katru izmaiņu.** „Projektā” nozīmē, ka projekts ir atvērts aizmugurē (lietotnē) vai — no SDK — caur `ChlorosProject`, kas nosauc savu mapi. Kamera, matrica vai gaismas sensors, kas vēl nav iekļauts projektā, tiek pievienots tā failiem, kad tas izveido savienojumu (`lattice cam-connect`, `array-connect`, `daq pool-connect`, lietotne, SDK, `chloros-mcp`), kopā ar tās iestatījumiem. Jebkura režīma maiņa (`lattice exposure`, `gain`, `configure --set`, `resolution`, masīvaun izraisīšanas ātrumu, gaismas sensora vāciņu, lietotnes vadības elementus) tiek saglabāta šī projekta failos pēc brīža. Kamerā iestatītais ekspozīcijas laiks, pastiprinājums vai AE mērķis tiek saglabāts kā fiksēts, tādējādi, atverot projektu no jauna, tas tiek saglabāts atkārtoti.
- **Atverot projektu, tiek iestatītas tajā norādītās ierīces** visos režīmos: kamera, matrica vai gaismas sensors, kas ir pieslēgts bez saviem iestatījumiem, saņem projekta saglabātos iestatījumus (`lattice cam-connect` / `array-connect`, kad projekts ir atvērts, `chloros-cli project connect`, `open_project().connect_all()`, lietotnes atkārtota atvēršana). Masīvs, kas pieslēgts ar jaunu identifikatoru, atjaunina projekta saglabāto masīvu ar tām pašām kamerām; blakus tam tiek pievienots masīvs ar citām kamerām.
- **Lietotnes paša saglabāšana nekad nepārraksta ierīces reāllaika iestatījumus** ar to vecāko kopiju un saglabā katra elementa pārrakstījumus, ko saglabājis CLI vai SDK izsaucējs.
- **Jūsu iestatītais pikseļu formāts ir tas, ko saglabā projekts.** Formāts, kas iestatīts masīva iestatījumos, izmantojot `--pixel-format` vai `connect_array(pixel_format=)` (ieskaitot 8 bitu formātu), tiek saglabāts un precīzi atjaunots. Ja nekas nav iestatīts, katra kamera savienojas ar savu 12-bitu noklusējuma iestatījumu (`BayerRG12`, `Mono12` mono sensora gadījumā). Formāta nosaukums norāda tā bitu dziļumu; formāta ģimene atbilst sensora ģimenei, tādēļ krāsu kamera nekad netiek iestatīta uz mono formātu, bet mono kamera — uz Bayer formātu, un rinda `[PixelFormat <serial>] ... written` to norāda, ja formāts tika pārveidots. 8bitu uzņemuma eksportētie dati ir 8 bitu, ieskaitot atstarojumu: pārmēroti un marķēti ar `PixelScale`.
- Ierīce, kas pieslēgta bez projekta, neko neieraksta.
- **Ieraksts seko projektam, kas izveidots pēc tā; atverot projektu, ieraksts paliek tajā.**Kad tiek izveidots projekts — SDK `create_project()`, `chloros-mcp` vai lietotnes**Jauns projekts**— vai tiek dublikāts (lietotnes**Duplicēt**, kas atver kopiju), kamēr ieraksts joprojām tiek rakstīts iepriekš atvērtā projektā, ieraksts turpinās, un tas, kas tiek ierakstīts, nonāk jaunajā projektā, tajā pašā vietā (zemāk). Atverot esošu projektu — `chloros-cli process`, `project devices --reconnect`, `ChlorosProject.process()`, lietotnes funkcija**Atvērt projektu** — ieraksts turpinās tajā projektā, kurā tas tiek ierakstīts: tas tur tiek pabeigts, un tā faili pievienojas šī projekta failu sarakstam (lietotne aptur katru ierakstu, pirms atver projektu).
  - `array-record` un `array-burst`: video vai sērijas mape ar visiem kadriem tiek pārvietota tur pilnībā, kad ierakstīšana apstājas (tās `--duration`, Ctrl+C). `Burst dir`, ko tā izdrukā, ir jaunais, un `--build` un `array-build-video` darbojas, balstoties uz to. Ja disks atsakās pārvietot, ieraksts paliek tur, kur tas tika uzrakstīts;beigu žurnālā ir norādīts iemesls, tāpat kā apstāšanās HTTP-atbildē (`relocation_error`). Apstādinātas sērijas neapstrādātie kadri un `.daq` tiek pievienoti tā projekta failu sarakstam, kurā tā nonāk; video (`array-record`, no sērijas izveidots video) netiek pievienots projekta failu sarakstam.
  - `array-capture --continuous`: katrs ieraksts pēc izmaiņām tiek saglabāts jaunajā projektā; tie, kas saglabāti pirms tam, paliek savā vietā.
  - `daq pool-record` un `project sensor log` (kā arī lietotnes paša gaismas sensora ieraksts): `.daq` pēc ierakstīšanas pabeigšanas tiek pārvietots uz jaunā projekta`light_sensor` mapē, kad ierakstīšana tiek pārtraukta, un tiek pievienots šī projekta failu sarakstam.

  Ieraksts mapē ārpus atvērtā projekta (piemēram, `-o` citur) paliek tur, kur ir, un `lattice continuous` un `multi-stream` saglabā mapi, kas tiem tika piešķirta.
- **Lietotnē ieraksti, kas veikti pēc jauna projekta izveides, tiek iekļauti tajā.** Projekta izveide vai dublikāta izveide neaptur nevienu ieraksta sesiju vai ierakstīšanu, un katrs pēc tam veiktais ieraksts (Capture All, Start Capturing) tiek saglabāts jaunajā projektā un pievienots tā failu sarakstam. Esoša projekta atvēršana lietotnē vispirms aptur visus ierakstus.
- **Projekts netiek pārvietots, kamēr notiek ierakstīšana.**Atvērtā projekta pārvietošana uz citu darba direktoriju (lietotnes**Iestatījumi → Darba direktorija**; neviena CLI komanda nepārvieto projektu, to dara HTTP klients, veicot pieprasījumu uz `/api/move-project`) tiek noraidīta ar HTTP 409 kļūdu `PROJECT_RECORDING`, kamēr masīva ierakstīšana vai sērija, masīva nepārtraukta uzņemšana, `lattice continuous` / `multi-stream` uzdevums vai gaismas sensora ierakstīšana raksta tajā: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.` Lietotne noraida tāpat, pirms tā lūdz norādīt mapi, kamēr darbojas tās paša funkcija „Capture All”, „Start Capturing” vai gaismas sensora ierakstīšana.

Tas nav saistīts ar to, kādā stāvoklī kameras paliek pēc savienojuma pārtraukšanas (sk. [Kameras atmiņa](#camera-memory----keep-settings----clear-settings)): projekts pats iestata savas kameras, kad tas tiek atvērts.

### Kameras atmiņa — `--keep-settings` / `--clear-settings`

Kad tiek pieslēgts masīvs, tā kameras pārņem masīva iestatījumus, un jūs tos pēc tam varat mainīt. Kad Chloros atbrīvo kameras — `array-disconnect`, pārejošas komandas beigas (`multi-capture` bez masīva, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`), `lattice continuous` apturēšana, `lattice capture` beigām kamerai, kuru tā neuzglabā — tā katru no tām atgriež tādā stāvoklī, kādā tā tika atrasta pirms savienošanas: tās trigera un sinhronizācijas līnijas vadu savienojumus, PTP un kameras iestatījumus (pilnais saraksts atrodams sadaļā [Vairāku kameru (pagaidu sesijas)](#multi-camera-transient-sessions)).

Lai kameras izmantotu ārpus projekta, divi karodziņi jebkurā no šīm komandām ieraksta kameras **pašas atmiņā** — to, ar ko tā ieslēdzas, neatkarīgi no programmatūras:

- **`--keep-settings`** saglabā katras kameras iestatījumus tādus, kādi tie bija sesijas beigās, kameras atmiņā (UserSet2, kas kļūst par sākuma iestatījumiem). Kameras joprojām darbojas brīvi (`TriggerMode` `Off`), sinhronizācijas līnijas vadu savienojumi paliek tādi, kādi tie ir. Kamera bez UserSet2 saglabā iestatījumus UserSet1; šis dators to atceras, tāpēc Chloros masīva pašatjaunošanās — kas atjauno „UserSet1”, kas ir novirzījies no Chloros bāzes līnijas — to atstāj nemainīgu.
- **`--clear-settings`** atjauno katras kameras atmiņu uz rūpnīcas iestatījumiem: Chloros bāzes līniju, kuru sistēmas pašatjaunošanās fiksē — rūpnīcas noklusējumi ar `TriggerMode`, `Off`, `TriggerSource` un `Line2`, automātiskā ekspozīcija, saglabāts RGB-kameras kalibrētais balansa iestatījums — UserSet1, kas veido sākuma iestatījumu kopumu.
- Abus nevar apvienot. Katra kamera saņem vienu rindu: `SN:…: settings saved to the camera's memory (UserSet2); it powers up with them.` vai `SN:…: camera memory reset to the factory settings.`; atbilde `camera_memory` ir tāda pati katrai kamerai.

Lietotne nepiedāvā šos karodziņus.

**Jebkurš iziešanas veids atgriež kameras sākotnējā stāvoklī.**Tā pati atgriešana, kāda tika veikta sākotnēji, notiek katrā iziešanas ceļā, ne tikai iepriekš minētajās komandās: `cam-disconnect` (un lietotnes kameras**X**); lietotnes**X** uz masīva vai jebkuras masīva elementa rindas (masīvs nevar darboties bez elementa, tādēļ tiek atbrīvots viss masīvs); lietotnes aizvēršana (tā dod backendam līdz 20 s, lai atgrieztu katru kameru, masīvu un darbojošos `lattice continuous` atpakaļ, pirms to aptur; komanda, kas tajā brīdī joprojām darbojas — īslaicīga komanda, kas atver savas kameras vai gaida to PTP pulksteņus, vienreizēja `lattice capture`, `power`, `white-balance` vai `presets --apply` — apstājas nākamajā drošajā punktā, vispirms atjauno savas kameras, līdz pat 8 s, un atbild ar HTTP 503 `SHUTTING_DOWN`; joprojām darbojošs masīva savienojums — `lattice array-connect`, lietotnes savienojums — apstājas pirms nākamās kameras un atgriež atpakaļ atvērtās kameras uz laiku līdz 10 sekundēm, un atbild ar HTTP 503 `SHUTTING_DOWN`; un tā, kas ierodas izslēgšanas laikā, neko neatver: HTTP 503 `SHUTTING_DOWN`); un CLI komanda, kas tiek pārtraukta vai kuras terminālis aizveras (SIGTERM / SIGHUP pārtrauc tās darbu — un atgriež kameras sākotnējā stāvoklī — pirms tā iziet no programmas `143` / `129`, tāpat kā Ctrl+C). `lattice continuous`, `multi-stream` vai `align-stream`, kas tiek nekavējoties pārtraukts (SIGKILL) un nevar neko apturēt, — tā darbība tiek pārtraukta un kameras atgrieztas sākotnējā stāvoklī ar aizmugurējās sistēmas palīdzību 30 sekundes pēc pēdējās reģistrēšanās. Aizmugurējais serveris, kas avarē vai tiek piespiedu kārtā izbeigts, nevar atjaunot neko; strāvas cikla pārstartēšana atgriež katru kameru tās atmiņā, un nākamais Chloros savienojums pārkonfigurē kameru, kuru tas atrod ieslēgtu.**Projekts pats konfigurē savas kameras.** Kad masīvs izveido savienojumu ar atvērtu projektu, katrs tā elements saņem iestatījumus, ko projekts tam saglabājis — ekspozīciju, pastiprinājumu vai AE mērķi, kas tam tiek saglabāts (uzskatīts par jūsu iestatījumu, tādējādi automātiskā ekspozīcija to neietekmē), kā arī balansa režīmu (Chloros korekcija; kameras paša balansa iestatījums paliek izslēgts) — neatkarīgi no tā, kas ir saglabāts kameras atmiņā.

### Smart-AE / Smart-Capture

LATTICE masīvi, tiklīdz tie ir savienoti, fonā nepārtraukti darbojas ar automātisko ekspozīciju (AE), taču nesen izvēlētajai ainai ir nepieciešams brīdis, lai ekspozīcija stabilizētos. `array-capture --smart` ir **iekļauts ērtības nolūkā**: tas gaida, līdz AE stabilizējas visās masīva kamerās, un tikai tad aktivizē uzņemšanu. Izmantojiet to, ja sesijas laikā maināt ainas.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Nostabilizēšanās politika pēc noklusējuma ir konservatīva: 5 s laika limits, 1,5 s stabilitātes logs, ±5 % ekspozīcijas izkliedes pielaide. Ja nepieciešama atšķirīga automatizācijas darbība, to var pielāgot, izmantojot SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`).

#### Kā darbojas automātiskā ekspozīcija

- **Vispirms ekspozīcija, pēc tam pastiprinājums.** Ja pastiprinājums ir iestatīts uz „Auto”, tas paliek 0 dB līmenī, līdz ekspozīcija sasniedz ilgāko pieļaujamo vērtību.
- **Manuāla ekspozīcija atstāj pastiprinājumu režīmā „Auto”**, kamēr jūs nemaināt pastiprinājumu vai neiestatāt to manuālā režīmā. Atgriežoties režīmā „Auto”, pastiprinājuma ierobežojums ir tāds pats kā iepriekš.
- **Nelieli spilgtie apgabali nepadara ainu tumšāku.** Spīdums, kas aizņem līdz 1 % no kadra, tiek ignorēts, bet zināma kalibrēšanas mērķa paneļos — līdz 0,5 % no paneļa.
- **DAQ ekspozīcijas maksimālā robeža ir spēkā tikai tad, ja redzes laukā atrodas kalibrēšanas mērķis.** Tā neļauj baltajam paneļam pārspīlēties; ja paneļi nav redzami, kamera eksponē atbilstoši ainai.
- **Nemainīga gaisma paliek stabila, bet mainīga gaisma izlīdzinās dažu sekunžu laikā.** Nemainīgas gaismas apstākļos ekspozīcija un tās robeža paliek nemainīgas, un pēc lielām gaismas izmaiņām attēls izlīdzinās dažu sekunžu laikā. Izgriezums vairs neierobežo ekspozīciju uz visiem laikiem, un, mainoties interesējošajai zonai (ROI), ekspozīcija tiek atjaunota.
- **Spilgtuma mērķis paliek sasniedzamā diapazonā.** Tas nepalielinās, kamēr kamera atrodas ekspozīcijas robežvērtībā un tās pastiprinājums nevar palielināties, tādējādi atgriežoties gaismai, kadrs netiek pārspīlēts.
- **Kamera, kuras automātiskā ekspozīcija vairs nereaģē, tiek noturēta līmenī, kas atbilst esošajam apgaismojumam.** Spilgtā saulē kamera var samazināt ekspozīciju līdz minimālajam līmenim un palikt šajā stāvoklī ar tumšu kadru, lai gan visi tās iestatījumi rāda normālus rādījumus. Chloros uzrauga kadru, uztur kameru līmenī, kas tika saglabāts šim apgaismojumam, un atbrīvo to, kad apgaismojums mainās. Tumšs vai aizklāts gaismas sensors to netraucē.

### Uztveršanas eksporta līmeņi (`all` noklusējums)

Sākot ar šo versiju, `lattice capture`, `lattice multi-capture` un `lattice array-capture` **noklusējuma iestatījums ir `--processing all`** — viens saglabāts fails katram eksporta tipam, kas attiecas uz katru kameru, atbilstoši lietotāja saskarnes funkcijai „Capture All”. Līmeņi ir šādi:

| Līmenis | Izvade | Attiecas uz |
| --- | --- | --- |
| `raw` | Vienkanāla Bayer (mono kameras: viena josla) tieši no sensora. | Visas kameras. |
| `debayered` | 3-kanālu krāsu attēls, saglabāts **RGB**kanālu secībā (TIFF fails ar RGB fotometrisko interpretāciju; PNG/JPEG faili arī atveras kā RGB): no neapstrādātā attēla atņemts kalibrēšanas paketes tumšā nobīde (DSNU), pēc tam piemērota OpenCV maludemosaic — bez linearitātes LUT, līdzena lauka vai krāsu atdalīšanas (mono kameras: 1 kanāls pelēktoņu skalā). Atmiņā esošie demosaicēti kadri, kurus atgriež PythonSDK (`grab()`, `frame_stream()`), ir**BGR**, OpenCV secībā; tikai saglabātais fails ir RGB formātā. | Visas kameras. |
| `radiance` | float32 W/m²/sr/nm, izmantojot pilnu radiometrisko ķēdi. | Tikai multispektrālās (M3C/M3M) kameras — **izlaistas RGB filtra kamerām**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), Pix4D-saderīgs. | Tikai multispektrāls, un **tikai tad, ja ir piesaistīts DAQ + kamera ir kalibrēta**; citādi tiek izlaists. |
| `display` | Pilna GUI priekšskatīšanas ķēde (CCM + WB + gamma atbilstoši kameras profilam). Saglabāts `preview/` mapē. `lattice capture` pieņem arī nosaukumu `preview`; `array-capture` / `multi-capture` pieņem tikai `display`. | Visas kameras. |

Norādiet vienu līmeni, lai saglabātu tikai to vienu (`--processing debayered`). Ja pieprasāt `all`, līmeņi, kas neattiecas uz konkrēto kameru, tiek izlaisti (un tiek ziņoti), nevis tiek uzskatīti par kļūdu — nepieslēgta vai nekalibrēta kamera joprojām saņem `raw` / `debayered` / `preview`.

#### DAQ, kas nolasa uzņemto attēlu, saglabā

Katru reizi, kad DAQ ir piesaistīts kamerai vai matricai vai iekļauts kopā, uzņemšanas laikā izmantotais lejupvērstais nolasījums tiek ierakstīts **`.daq`** failā uzņemumu mapes `daq/` apakšmapē, norādot tās `.match.json` (cik tālu rādījums atradās no ekspozīcijas) un `.cameras.json` (kam pieder šis rādījums), un tiek atspoguļots `DAQ:` rindā. Tas attiecas uz katru līmeni, ieskaitot neapstrādātus vai no Bayer matricas atbrīvotus attēlus, tādējādi jebkuru attēlu vēlāk var pārstrādāt starojuma, atstarošanas vai indeksa formātā. Tas ir viens un tas pats fails ar vienādu nosaukumu un papildu failiem neatkarīgi no tā, vai uzņēmums tika veikts, izmantojot GUI, CLI vai SDK. Ja nav neviena DAQ rādījuma, nav arī `.daq`, un `multi-capture` bez pievienota masīva (pārejas rezerves variants) neko nesaglabā.

| Karodziņš | Efekts |
| --- | --- |
| `--no-daq` | Nesaglabāt rādījumu no uzņemšanas, kuras līmeņi to neizmanto (`raw`, `debayered`). `radiance`, `reflectance` vai `display` līmenis to joprojām ieraksta, jo atkārtotai importēšanai ir nepieciešams rādījums, ar kuru produkts tika izgatavots. |
| `--daq-tolerance-ms MS` | Cik tālu no ekspozīcijas rādījums drīkst atrasties, lai joprojām tiktu uzskatīts par atbilstošu pielaidei (noklusējums 500). Mainās tikai novērtējums (`.match.json`, `in_tolerance` un `tolerance_ms`, kā arī atstarošanas rādītājs `DlsInTolerance`), nekad — izmantotais rādījums. Skatīt [DAQ rādījums, kas ir tālu no ekspozīcijas](#a-daq-reading-far-from-the-exposure). |
| `--force-daq` | Vecākais veids, kā pieprasīt `.daq`, veicot tikai neapstrādātu datu uzņemšanu. Tagad tas ir noklusējuma iestatījums, un `lattice capture` un `array-capture` joprojām pieņem šo karodziņu (`multi-capture` to nepieņem). `--no-daq` to aizstāj. |

Kura DAQ: tā, kas piesaistīta kamerai vai matricai (GUI gaismas sensora izvēle, projektā saglabāta piesaistīšana vai `chloros-cli daq pool-bind`), vai arī jaunākā, kāda ir backendā. Neapstrādātu attēlu sērija (`array-burst`) saglabā savu `.daq` katram atsevišķajam nolasījumam sērijas mapē, tāpat kā iepriekš.

**PNG un JPEG formāti satur 8 bitus uz kanālu, tādēļ tie izmanto tikai `display` / `preview` un indeksa produktus.** `--format png` vai `jpg` ar līmeni, ko tie nevar ietvert, tiek noraidīti, pirms kaut kas tiek ierakstīts, norādot līmeni un TIFF kā formātu, kas to ietver:

- `radiance` (float32) un `reflectance` (uint16, `32768` = 1,0) — vienmēr, tādēļ tiek noraidīts arī `--processing all` (noklusējuma vērtība) kopā ar `--format png`/`jpg`: izmantojiet `--format tiff` vai `--levels display`.
- `raw` un `debayered` no kameras, kas pārraida vairāk nekā 8 bitu (12 bitu M3C / Mono12 M3M kadri) — CLI nevar noteikt kameras bitu dziļumu, tāpēc aizmugurējā sistēma tos noraida (HTTP 400). Vienkāršaiskadrs `lattice capture --processing debayered` iegūst 8 bitu kadru PNG/JPEG formātā un netiek noraidīts, tāpat kā `multi-capture` bez pastāvīga masīva (8 bitu bez bayera filtra).

Katrs uztveršanas maršruts atbild vienādi (`error_code` `FORMAT_CANNOT_HOLD_LEVEL`): `lattice capture`, `multi-capture`, `array-capture`, `project capture` / `burst`, kā arī aiz tiem esošie HTTP-maršruti.

### Kā izskatās ierakstu mape

Katrs eksporta veids tiek saglabāts savā **atsevišķā apakšmapē**, kas atrodas `-o` mapē, tādējādi daudzlīmeņu ierakstā nekad nesajaucas dažādi veidi. Skicē attēlota**viena kameras **noklusējuma `all`** uzņemšana, ko veic `lattice capture` — vairāki līmeņi katrai kamerai, tāpēc katram failam ir `_<level>` paplašinājums:

```
output/
├── raw/           capture_<ts>_SN<serial>-<filter>_raw.tiff
├── debayered/     capture_<ts>_SN<serial>-<filter>_debayered.tiff
├── radiance/      capture_<ts>_SN<serial>-<filter>_radiance.tiff
├── reflectance/   capture_<ts>_SN<serial>-<filter>_reflectance.tiff
├── preview/       capture_<ts>_SN<serial>-<filter>_display.tiff
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_radiance_clampmask.tif, reflectance/…, index/… — the unmix clamp
│                  masks, one subfolder per product folder, only when a band clamped
└── daq/           daq_data_<sensor id>_<ts>.daq: the downwelling reading matched to the capture
                   (+ .daq.cameras.json: whose)
```

Kā mainās failu nosaukumi:

- **Kameras lauks — sērijas numurs, tad filtrs:** katra ieraksta faila nosaukumā ir norādīta kamera, ar kuru tas uzņemts, kā `SN<serial>-<filter>`, kur `<filter>` ir kameras modeļa filtra daļa: `SN261902142-F450` attēlam ar nosaukumu `M3M-L87-F450`, `SN213800234-FRGN` attēlam „`M3C-L87-FRGN`”, `SN214701292-FRGB` attēlam RGB kamerai. Filtrs pievieno sērijas numuru, piemēram, „`-`”, nevis ar `_`, tādējādi nosaukumam ir tādi paši ar `_` atdalīti lauki kā iepriekš (prefiks, datums, laiks, milisekundes, kamera, līmenis). Kamerai, kuras modeli nevar nolasīt (dzēsts DeviceUserID), tiek piešķirts nosaukums tikai `SN<serial>`. Ierakstiem no agrākām versijām nosaukumā nav filtra; Chloros nolasa abus.
- **Prefiks:** `lattice capture` nosauc failus `capture_…`; `array-capture`, `multi-capture` ar savienotu masīvu, un `project capture` / `burst` / `stream`, kas ir masīva nosaukums, tos nosauc par `sync_<ts>_SN<serial>-<filter>…`.
- **Līmeņa piedēklis tiek izmantots tikai tad, ja ir vairāki līmeņi.**Kamera, kas vienā uzņēmumā saglabā**vairākus**līmeņus (`all` vai `--levels a,b`), pievieno `_<level>`. Kamera, kas saglabā**vienu** līmeni, saglabā vienkāršo nosaukumu bez piedēkļa — `--processing reflectance` raksta `reflectance/sync_<ts>_SN<serial>-<filter>.tiff` — tādējādi skripti, kas rakstīti vienlīmeņa uzņēmumiem, turpina darboties. Līmeni joprojām nosaka mape.
- **Fiksēšanas maskas seko produkta nosaukumam:** `<product name>_clampmask.tif`. Vienlīmeņa atstarošanas uzņemums raksta `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif`; `all` uzņemums raksta `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif`.
- **Paplašinājums** seko pēc `--format`: noklusējuma `tiff` rada `.tiff`, `--format tif` rada `.tif` (fiksēšanas maskas vienmēr ir `.tif`).
- **Viens izņēmums:** `lattice capture --processing debayered` (vai `--levels debayered`) izmanto ātru vienu-kadru ceļu un raksta `capture_<ts>_SN<serial>-<filter>.tiff` tieši `-o` — bez apakšmapes.
- **Plūsmas un pārsūtīšanas sērijas** pārraida to pašu lauku: `lattice continuous` ieraksta `frame_<seq>_SN<serial>-<filter>.tiff`, `lattice multi-stream`, `stream_<seq>_SN<serial>-<filter>_<frame id>.tiff` un neapstrādātu sēriju `<burst>_f<frame>_SN<serial>-<filter>_raw.tiff`.

`<ts>` ir uzņemšanas laika zīmogs, bet `<serial>` — kameras sērijas numurs, tādējādi viena sinhronizēta grupa izmanto vienotu
laika zīmogu visām kamerām. **Ņemiet vērā vienu asimetriju:** `display` līmenis tiek saglabāts mapē
ar nosaukumu `preview/`, savukārt daudzlīmeņu failiem nosaukumā — mape un paplašinājums atšķiras
tikai šim līmenim. Nezināmi līmeņi tiek novietoti mapē ar savu nosaukumu, un, ja apakšmapes
nevar izveidot, fails tiek ierakstīts izvades saknē, nevis pazūd.

**Uztverto attēlu mapes atkārtota apstrāde:**norādiet `chloros-cli process` uz**„captures“ saknes mapi**
(`output/`). `process` parasti importē tikai jūsu norādīto mapi, bet, ja tajā nav
attēlu, bet tajā ir apakšmapes, tas automātiski pārskata apakšmapes — tādējādi visus līmeņu apakšmapes un
apakšmapes „`daq/`” tiek iekļautas vienā reizē. Katrs „capture” līmenis tiek importēts kā viens attēls,
bet pārējie līmeņi ir pieejami kā režīmi, nevis kā viens attēls katram līmenim.

**Līmeņa apakšmapes** tieša nosaukšana (piem., `output/raw/`) arī darbojas. Tādā gadījumā `daq/`
paliek neiekļauts, tāpēc, kad atkārtotiizveidojat radiometrisko
produktu no `raw/` — pretējā gadījumā laika zīmoga saskaņošanai nebūs ar ko salīdzināt.

**Apstrāde vienmēr sākas no `raw`.** Katrā uzņemšanā neapstrādātais kadrs ir apstrādes avots;
`debayered`, `radiance`, `reflectance` un `preview` ir pieejami kā skatāmi režīmi, bet nekad netiek
atgriezti caur apstrādes cauruļvadu. Atvasinātā produkta atkārtota apstrāde nozīmētu atkārtoti piemērot vinjetēšanu, CCM un
starojuma aprēķinus, kas jau ir iebakēti tā pikseļos, tāpēc Chloros to noraida, nevis
veic divkāršu apstrādi. Divas sekas, par kurām ir vērts zināt:

- Renderējumi `index/` un `composite/` **nekad** netiek apstrādāti. Tie ir izvades dati, nevis uzņemumi —
  NDVI LUT renderējumam nav jēgpilnas starojuma interpretācijas.
- Uzņemumu mape, kas eksportēta **bez** `raw` (piem., `array-capture --processing reflectance`), nav
  nav leģitīma apstrādes procesa avota. Šie ieraksti tiek importēti un parādīti normāli, bet `process` tos izlaiž
  un par to paziņo:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Ja jums patiešām ir nepieciešams izvadīt atvasinātu produktu — hub sesija, kas ierakstīta ar
  ieslēgtu `demosaic`, vai vecāka tipa mapi — `--input-level {raw,debayered,processed}` piespiež ieejas
  punktu un atceļ izlaišanu. Šis karodziņš ir apzināti paredzēta izeja ārkārtas gadījumiem; `auto` (noklusējums)
  nekad neapstrādā ierakstu, kurā nav neapstrādātu datu.

**Kalibrēšanas marķieri: tikai ar `raw`.** Uztveršanas `raw` fails satur kameras kalibrēšanas paketi: `CalibrationJSON` datu bloku un ar MicaSense saderīgās birkas (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), kas apraksta sensora neapstrādātās vērtības. Visi pārējie līmeņu faili (`debayered`, `display`, `radiance`, `reflectance`, `index/` renderējums) un katrs `lattice continuous` / `multi-stream` kadrs satur kameras identifikāciju (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) un paša kadra ierakstu, `XMP-mapir:FrameJSON` (ekspozīcija, pastiprinājums, temperatūra, pikseļu formāts, uzņemšanas laiks, DAQ saskaņošana), jo katram `process` produktam ir savs identifikators. `process` atrod šāda faila kalibrāciju pēc tā sērijas numura un `CalBundleSha256` „Chloros” kalibrācijas kešatmiņā, lejupielādējot paketi vienu reizi, ja datorā tā nav.

#### No vairākām kamerām izveidoti faili

Fails, kas izveidots no vairāku kameru spektrālajiem diapazoniem — daudzkanālu kopums (`align-apply`, `align-stream`, `index --live --save-multiband`), no vairākām kamerām veidots reāllaika indekss (`index.tif`, `index_LUT.png`), masīva uzņēmuma attēls `composite/` — nosauc katru kameru, kas tajā iekļauta:

- **Saraksti pa kamerām, joslu secībā** (kompozīcija: fona slānis, tad priekšplāns; indeksa slānis, kameras, no kurām nāk tā joslas): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (kalibrēšanas pakete, caur kuru ir izgājušas katras kameras joslas, citādi — `align-apply` / `align-stream` kopums, kura joslas netiek pārbaudītas — kameras pakete, ko Chloross ir saglabājis kešatmiņā, tā, pēc kuras nosaukta tās uzņemto failu nosaukums), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — paralēli saraksti, kur katra *n*. elements apraksta to pašu kameru; `-`, kur kamerai nav vērtības (nav kalibrēšanas pakas, nezināms objektīvs). Katra kamera ir **viens** ieraksts: ja tās paketē vienam laukam ir vairākas vērtības — piemēram, paketes halogēnās lampas sertifikāti — tās tiek savienotas ar `;` (`F-1479-1;F-1480-1;F-1481-1`), nekad neizmantojot komatu, tādējādi sarakstā ir viena vienība katrai kamerai, neatkarīgi no tā, kā tā ir izdrukāta.
- **EXIF `SerialNumber`** satur tos pašus sērijas numurus, kas savienoti ar komatu (`214000535,214000551`).
- Tajā **nav** atsevišķas kameras identifikācijas (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): tie nosauc vienu kameru un tās komplektu, un `process` no tiem noteiktu faila kalibrāciju. Indekss, kas aprēķināts no vairāku kameru joslām (`index.tif`, `index_LUT.png`) EXIF laukā ir norādīts `Model` `LATTICE` (iekārta), nevis vienas kameras `LATT-…` modeli.

Fails ar **vienas** kameras joslām — katras joslas TIFF (`--per-band`), `align-apply --per-camera` kadrs, viena kameras attēlu kopa vai reāllaika indekss — satur šīs kameras identifikācijas tagus, tāpat kā jebkurš cits uzņemtais fails. `align-apply` kopas, katra joslas un katras kameras faili, kā arī katra `align-stream` kopa un tās katras joslas faili satur savas grupas uzņemšanas laiku (atskaites kameras ekspozīciju, UTC), un mono kameras `--per-camera` kadrs ir viena plakne, tāpat kā `--bit-depth` kopums.**Kameras orientācija (apgriešana / pagriešana).** Kameras orientācijas iestatījums (GUI opcijas „Mirror H” /
Mirror V / rotācija) nosaka, kā tiek saglabāts uzņemtais attēls:

- **`raw` un `debayered` tiek saglabāti tieši tā, kā tos nolasa sensors**, neatkarīgi no iestatījumiem.
  Pagriezta vai apgriezta „Bayer” mozaīka veidotu atšķirīgu krāsu filtra rakstu, un kalibrēšanas
  kartes (tumšais nobīde, līdzenais lauks) atrodas uz paša sensora režģa.
- **`preview`/`display`, `radiance`, `reflectance` un renderētais `index/` ir orientēti** — pagriezti
  vienu reizi pēc visiem kalibrēšanas aprēķiniem, tādējādi tie atveras tā, kā kamera bija iestatīta. To
  `clamp_masks/…_clampmask.tif` faili atrodas uz viena un tā paša režģa.
- **Katrs uzņemtais fails reģistrē iestatījumus** XMP formātā: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, pulksteņrādītāja virzienā, pēc
  apgriešanas) un `ChlorosOrientationApplied` — `false` attiecībā uz neapstrādātiem un debayered (oriģināliem pikseļiem),
  `true` orientētajos produktos. `ChlorosProcessing` uzskaita operācijas, kas piemērotas šī faila
  pikseļiem (`none`, ja tie ir oriģinālie). Tas ietver katru kadru, ko pārraida `lattice continuous` (
  kameras paša iestatījumi, nepiemēroti), un katru `lattice multi-stream` kadru (identitāte: tā
  sastāvdaļas ir oriģinālie kadri, tāpat kā masīva uzņemumos). `lattice align-stream` / `align-apply`
  kopas reģistrē galvenās kameras iestatījumus, kas nav piemēroti: to joslas tiek deformētas, lai atbilstu galvenā
  kadram, bet netiek pagrieztas.
- **`process` piemēro reģistrēto orientāciju**, apstrādājot neapstrādātos datus: vispirms tumšā nobīde, līdzenuma
  lauks, demosaika un atdalīšana sensora koordinātēs, pēc tam masīva izlīdzināšanas deformācija, un
  orientāciju — tādējādi priekšskatījuma, starojuma, atstarošanas un indeksa rezultāti iznāk tieši tādi paši kā
  reāllaika uzņemšanas rezultāti, un tiek reģistrēts `ChlorosOrientationApplied=true`. Nekalibrētie rezerves
  produkti (`Sensor_Response_Images`, `Vignette_Corrected_Images`) tiek orientēti tāpat, pēc
  plakana lauka. No „Bayes” režīma atbrīvotais produkts paliek sākotnējā formātā.
- **Chloros attēlu skatītājs parāda neapstrādātu vai no „Bayes” režīma atbrīvotu failu, kas pagriezts atbilstoši tā ierakstam** (tikai attēlošanai —
  fails netiek mainīts), tāpēc tas izskatās kā tā produkti; sīktēls, pilna izmēra skats,
  kursora rādījums un indeksa/LUT smilšu kaste — visi izmanto to pašu pagriezienu. Fails bez ieraksta un
  produkts (kas jau ir orientēts) tiek parādīts tā, kā tas ir saglabāts. Eksportēšana uz smilšu kasti reģistrē orientāciju, kādu
  pikseļi nes (`ChlorosOrientationApplied=true`; identifikators slānim bez reģistrācijas).
- **Uzņēmumi bez ieraksta tiek apstrādāti kā oriģināli** (nemainīga darbība). Pirms 1.3.0 versijas
  viena kameras uzņēmums ar iestatītu apgriešanu vai pagriezienu pats pagrieza savus neapstrādātos un debayered failus;
  Chloros nevar atšķirt šādu jaupagrieztu neapstrādāto failu no oriģinālā un neuzņemas minēt, tāpēc to
  apstrādā tā, kā tas ir saglabāts. Kalibrēšanas kartes atrodas uz paša sensora režģa, tāpēc šādai
  uzņemšanai izmantojiet uzņemumu, kas veikts ar 1.3.0 versiju, ja kalibrēšanai ir nozīme.
- **Masīvi pēc noklusējuma nepagriež nevienu sastāvdaļas failu** (izlīdzināšanas deformācija un `Alignment*` XMP atrodas
  pašu kameru nepagrieztos kadros) un reģistrē identitāti. `array-capture --orient` pagriež katra
  komponenta rezultātus pēc izlīdzināšanas deformācijas: atbilstoši **galvenā** orientācijai brīdī, kad tie tika
  deformēti tās kadrā (tādējādi komponenti paliek savstarpēji reģistrēti), citādi — atbilstoši pašu kameru orientācijai. Katrs
  fails reģistrē to kadru orientāciju, kurā atrodas tā paša pikseļi — neapstrādāts, atbilstoši savas kameras orientācijai —
  un neapstrādāts fails, kas ietver izlīdzināšanas transformāciju, reģistrē arī `ChlorosAlignedFlipHorizontal` /
  `ChlorosAlignedFlipVertical` / `ChlorosAlignedRotation`: galvenā attēla orientāciju, ja produkts ir deformēts
  galvenā attēla rāmī. `process` pagriež katru produktu par leņķi, kas pieder tā pikseļiem:
  izlīdzināto, ja tas deformē neapstrādāto failu (`--array-alignment`, noklusējums — reāllaikā izlīdzinātā
  uzņemšanas produktiem), kameras paša, ja tā nav (`--no-array-alignment` — reāllaika
  nesaskaņotās uzņemšanas produkti). Tāda pati opcija ir pieejama arī `array-burst`, `project capture` / `burst` /
  `stream`, receptēm un SDK.

### Izlaistie attēli jaukto filtru masīvos

Ja vienā masīvā tiek apvienotas RGB- un multispektrālās kameras, `array-capture --processing radiance` (vai `reflectance`) saglabā multispektrālos kadrus un **izlaiž** RGB-kameru attēlus — starojuma intensitāte uz vienu Bayer elementu nav nozīmīga platjoslas sensoram. CLI izdrukā katru saglabāto failu (ar tā eksporta līmeni), katru uzrakstīto `.daq` un katru izlaišanu atsevišķi, tāpēc failu skaits nav pārsteidzošs:

```
  Saved: …/output/reflectance/sync_…_SN213800234-FRGN.tiff [reflectance] (SN:213800234, fid:1)
  Saved: …/output/reflectance/sync_…_SN214000533-FOCN.tiff [reflectance] (SN:214000533, fid:1)
  Saved: …/output/reflectance/sync_…_SN214701288-FNGB.tiff [reflectance] (SN:214701288, fid:1)
  DAQ:   …/output/daq/daq_data_daq-e-54b5e0_….daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Izlaišanas iemesla simboli atbilst šādam paraugam: `<level>-not-applicable-to-rgb-cam`. Kamera, kuras kalibrēšanas pakete nav ielādēta, izlaiž `radiance` / `reflectance` ar `<level>-needs-calibration (connect the camera so its cal pack loads)`, bet gadījumā, ja nav izmantojama DAQ atstarojuma rādījuma, izlaiž ar `reflectance-skipped-no-dls`. Atstarojumu var izlaist arī ar `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, kā arī ar `dls-uncalibrated-band-<nm>`, ja josla galvenokārt atrodas ārpus DAQ gaismas sensora radiometriski kalibrētā diapazona (~374–974 nm) — starp piegādājamajiem SKU tikai F988, kura atbalstītā darbplūsma ir atstarošanas paneļa darbplūsma.

Izmantojiet `--processing debayered` (vai `display`), lai iekļautu katru kameru neatkarīgi no filtra tipa, vai noklusējuma `all`, lai vienā uzņēmumā iegūtu katru piemērojamo līmeni katrai kamerai.

### DAQ rādījums, kas ir tālu no ekspozīcijas

Katrs `.daq`, ko saglabā uzņemšana, ir DAQ rādījums, kas atbilst šai ekspozīcijai, un `.match.json` blakus tam reģistrē, cik tālu viens no otra atradās abi rādītāji (`gap_ms`) un vai tas atrodas pieļaujamās robežās (`in_tolerance`, 500 ms, ja vien uzņemšanas iestatījumos nav norādīts citādi `--daq-tolerance-ms`). Rādījums, kas atrodas ārpus šīs robežas, joprojām tiek izmantots, tādējādi netiek zaudēts neviens kadrs, un tam tiek piešķirts apzīmējums: atstarošanas faila XMP satur `DlsInTolerance=False` un `DlsMatchGapS`. `lattice capture`, `multi-capture` un `array-capture` to arī izdrukā zem attiecīgā `.daq`, ar DAQ'plūsmas kļūdu, ja to izskaidro:

**Kā rādījums tiek saskaņots ar ekspozīciju.** Uztveršana gaida DAQ pirmo rādījumu pēc ekspozīcijas, lai varētu apvienot rādījumus abās pusēs (`method` `interpolated`). DAQ-E dienasgaismā nosūta rādījumu ik pēc 0,4 s, bet telpās — ik pēc 1–2 s, tādējādi gaidīšanas laiks pievienojas apmēram pusotram no šiem intervāliem komandai, nevis ekspozīcijai. DAQ, kas ir pārtraucis nosūtīšanu, netiek gaidīts. DAQ-E rādījums ir gaismas vidējā vērtība intervālā kopš iepriekšējā rādījuma, tāpēc tas tiek novietots šī intervāla vidū: `gap_ms` tiek mērīts no ekspozīcijas līdz šai viduspunktam, un `bracket_stamps_ns` norāda pašu rādījumu laika zīmogus. `array-capture --continuous` gaida, nepalēninot uzņemšanu: tā neapstrādātie faili tiek uzrakstīti uzreiz, un to `.daq` tiek piešķirts, tiklīdz rādījums ir saņemts. Izmantojot `--interval`, katrs cikls gaida tāpat kā atsevišķa uzņemšana.

```
  DAQ:   …/output/daq/daq_data_daq-e-54b774_….daq
         DAQ reading 114.9 s from the exposure (tolerance 500 ms): reflectance uses it, labelled DlsInTolerance=False
         DAQ stream fault: no frame accepted from daq-e-54b774.local in 25 s, after 1932 good frame(s). …
```

Ja no šī rādījuma netika ierakstīts neviens atstarošanas failu, pirmā rinda beidzas ar `saved anyway; its .match.json says in_tolerance: false`.

Uztveršana joprojām beidzas ar `0`: visi faili tika ierakstīti un norāda to saturu. DAQ, kura datu plūsma ir apstājusies, turpina sūtīt pēdējo nolasījumu, tāpēc pārbaudiet to ar `chloros-cli daq pool-list`, pirms paļaujaties uz šīs uztveršanas reflektances datiem. Aizmugures sistēma atkārtoti savieno apvienoto DAQ-E, kas ir pārtraucis datu piegādi (skatīt [DAQ-E, kas pārtrauc datu sniegšanu](#a-daq-e-that-stops-delivering)). Rādījums, kas neatbilst pielaidei, izdrukā tikai savu `DAQ:` rindu, ja vien tas nav saglabāts rādījums (skatīt zemāk).

**Rādījums, kas saglabāts pēc tam, kad tā DAQ vairs nedarbojās.** Ja neviens DAQ nevar nodrošināt rādījumu konkrētajai ekspozīcijai (saistītais DAQ nav pieejams vai nevienam apvienotajam DAQ nav rādījuma, piemēram, pēc `daq pool-disconnect`), reģistrēšana atkārtoti izmanto pēdējo rādījumu, kas bija pieejams Chloros, un tā `.match.json` apraksta šo rādījumu attiecībā uz šo ekspozīciju: `method`, `retained`, `gap_ms` no šīs ekspozīcijas līdz paraugiem, no kuriem tika veikts rādījums, `in_tolerance` no šīs starpības un `retained_from_epoch_ns` — ekspozīcija, ar kuru rādījums tika sākotnēji saskaņots. Tādējādi uzņemums, kas veikts ilgu laiku pēc DAQ izslēgšanas, izdrukā iepriekš minētās rindas, un zem vēl viena, kas tiek izdrukāta pat tad, ja rādījums joprojām atrodas pieļaujamās robežās:

```
         This is the DAQ's last reading: the DAQ is no longer reporting, so each capture reuses it until a new one arrives
```

Tā `.daq` joprojām norāda DAQ, kas veica mērījumu, kā arī kalibrāciju un vāciņu, ar kuriem šis mērījums tika veikts, tādējādi atkārtotai importēšanai ir zināms, kurš DAQ, kalibrācija un vāciņš to radīja.

### Faili, kurus neizdodas ierakstīt

Kamera, kuras failu nevar ierakstīt — pilns disks, atļauju kļūda — netraucē citu kameru failu saglabāšanu. Katra uzņemšanas komanda (`lattice capture`, `multi-capture`, `array-capture`; `project capture`, `burst`, `stream`; kā arī receptes `capture`, `burst` un `stream`) izdrukā katru failu, kura saglabāšana neizdevās, uz stderr, pēc tam **iziet no programmas `1`**, lai gan pārējie faili tika saglabāti:

```
  Failed: SN:214701292 [reflectance] OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

Ir iekļauti visi faili, kas pieder pie konkrētā ieraksta, ieskaitot vienīgokadrs ar debayeringu `lattice capture --levels debayered`. `.daq` lasījums, kura `.match.json` izcelsmes sidecar failu neizdevās ierakstīt, tiek uzskaitīts kā `[match]` (pats `.daq` tika saglabāts).

Ja disks ir pilns, atkārtots ieraksts (`array-capture --continuous` / `--interval`, `project burst` / `stream`, recepte `burst` / `stream`) **apstājas šajā ciklā**, nevis turpina neko nerakstīt tajā pašā diskā līdz izpildes beigām. Par jebkuru citu kļūdu tiek ziņots katrā ciklā, kurā tā notiek, un izpilde turpinās līdz galam, pēc tam iziet ar kodu `1`. `array-capture --continuous` (aizmugurējās sistēmas nepārtraukta datu ieguve) neuzrāda nevienu failu, ieskaitot kļūdainos: to skaits tiek uzskaitīts progresa un kopsavilkuma rindās (`N failed`, `N file(s) failed to write.`) un iziet no `1` — skatīt [Nepārtrauktais režīms](#continuous-mode---continuous).

Ierakstītāji — `array-burst` un `array-record` — ziņo par savām kļūdām. `array-burst` pats apstājas, kad disks ir piepildīts vai tieši pirms tam. Abi turpina darbu, ignorējot jebkuru citu neveiksmīgu rakstīšanas mēģinājumu. Kad kāds no tiem ziņo par kļūdu, tā tiek izvadīta pēc skaitītājiem, un komanda **iziet no `1`**:

```
  Recorder error: disk-full: OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

`Disk low:` vietā `Disk full:` nozīmē, ka ierakstīšanas sērija tika pārtraukta, pirms disks bija pilns disku. Pirms apstāšanās ierakstītie kadri tiek saglabāti. `array-burst --build` izlaiž kompilāciju, kad beidzas vieta, jo video tiktu saglabāti tajā pašā diskā. Vispirms atbrīvojiet vietu, tad palaidiet `array-build-video --burst-dir …`. Pēc jebkuras citas kļūdas kompilācija turpina darboties.

`array-record`, kas neierakstīja nevienu kadru, arī ir neveiksmīga ierakstīšana (`Recorder error: no-frames: the combined view published no frame to record …`, iziet no `1`) — skatiet [`array-record`](#array-record--combined-index-videogif-monitoring-grade).

---

## Uztveršanas režīmi, ierakstītāji un pārstrāde bezsaistē

Visi šie rīki darbojas ar **pastāvīgu masīvu** (vispirms palaidiet `array-connect`). Tie atspoguļo GUI uztveršanas paneli.

### `array-capture` režīmi

`array-capture` ir viena komanda ar četriem aizslēga režīmiem un eksportēšanas slēdžu kopumu:

| Režīms | Karodziņš | Darbība |
| --- | --- | --- |
| **Vienreizēja** *(noklusējums)* | (nav) | Viena sinhronizēta uzņemšanas grupa, pēc tam iziet. |
| **Nepārtraukts** | `--continuous` | Nepārtraukta uzņemšana, līdz tiek uzņemti `Ctrl+C`, `--count N` attēli, `--duration S` vai līdz diska piepildīšanai. Izpilda aizmugurējās sistēmas paša nepārtraukto uzņemšanu (GUI nepārtrauktais režīms) — skatiet [Nepārtraukto režīmu](#continuous-mode---continuous) zemāk. |
| **Intervāls** | `--interval S` | Viens cikls ik pēc `S` sekundēm (skaitot no katra cikla sākuma), tās pašas robežas. |
| **Ātrākais** | `--fastest` | Tikai neapstrādāti dati+ piešķirtais DAQ rādījums + kombinētā indeksa kompozīts; izlaiž starojuma/atstarošanas/attēlošanas aprēķinus, lai kadrs tiktu parādīts ātri. Nozīmē `--processing raw --force-daq`. Vēlāk pārstrādājiet saglabāto `.daq` kalibrētos produktos. |

Eksportēšanas slēdži (apvienojami ar jebkuru režīmu; visiem ir kopīgs GUI/SDK-galapunkts):

| Karodziņš | Efekts |
| --- | --- |
| `--array-id ID` | Masīvs, no kura veikt uzņemšanu (izlaist, ja ir pieslēgts tikai viens). |
| `-o, --output DIR` | Izvades mape (noklusējums — `output`, tajā mapē, no kuras tiek palaista komanda). |
| `--format {tiff,tif,png,jpg,jpeg}` | Faila formāts (noklusējums — `tiff`). PNG/JPEG formātiem tiek izmantots tikai `display` un indeksa renderējums; skatiet [Ierakstīšanas eksporta līmeņi](#capture-export-levels-the-all-default). |
| `--processing LEVEL` | Vienots eksporta līmenis (`raw`, `debayered`, `radiance`, `reflectance`, `display`) vai `all` (noklusējums). |
| `--levels L1,L2,…` | Eksporta tipu konkrēta apakškopa (piem., `raw,radiance,reflectance`); **pārraksta `--processing`**. |
| `--aligned` / `--no-aligned` | **Noklusējums: izlīdzināts, tāpat kā Chloros lietotnes veiktā tā paša masīva saglabāšana**(tās**Aligned** saglabāšanas iestatījums katram masīvam, kas ir ieslēgts, ja vien to neizslēdzat). Katra locekļa ne-neapstrādātā eksporta dati (`debayered`, `display`, `radiance`, `reflectance`, indeksa renderējums) tiek deformēts atbilstoši galvenās kameras kadram un apgriezts līdz dalībnieku kopīgajai pārklāšanās zonai, tādējādi faili tiek reģistrēti pēc pikseļiem un iegūst tādu pašu izmēru, apgriezumu un `Alignment*` tagus kā lietotnē — mazākus nekā sensora (vai ROI) par dalībnieku nobīdēm; ROI gadījumā transformācija tiek pārrēķināta atbilstoši logam. `XMP-mapir:AlignmentJSON` norāda uz `"applied": true` un sniedz `crop_roi`. Neapstrādātais attēls paliek neizkropļots sākotnējā izmērā, bet transformācija tiek saglabāta metadatos (`"applied": false`). `--no-aligned` saglabā katru failu sākotnējā izmērā, neizkropļotu. `--aligned` ir norādītais noklusējums. Neatkarīgi no tā, vai deformācija tiek piemērota vai nē, katrs sastāvdaļas fails reģistrē savas kameras `Alignment*` transformāciju, tiklīdz masīvam ir pieejams saskaņošanas profils — izņemot kameru, kuru profils neizlīdzina (izlaista vai piespiedu identitātes stāvoklī sakarā ar neveiksmīgu risinājumu), un atsauces kameru, ja nav izlīdzināta neviena cita kamera: tām nav `Alignment*` marķējumu. Ja masīvam nav profila, uzņemums tiek saglabāts neizlīdzināts, un CLI izvada iemeslu `Alignment:` rindā, tāpat kā visos pārējos gadījumos, kad deformācija nevarēja tikt piemērota (elementi ar atšķirīgu izšķirtspēju, masīvs, kas nav sinhronizēts ar kabeli, kadrs, uz kura profilu nevar attēlot). Masīva automātiskā izlīdzināšana izveido profilu, tiklīdz ekspozīcija ir stabilizējusies; `align-calibrate` izveido profilu pēc pieprasījuma. |
| `--orient` | Pārveidojiet katra elementa **produktus**— `display`, `radiance`, `reflectance`, `index/` renderējumu un `composite/` — atbilstoši kameras orientācijai pēc izlīdzināšanas deformācijas: produkts, kas deformēts galvenā kadra (noklusējuma)**galvenā**orientāciju, tādējādi elementi paliek savstarpēji reģistrēti; nesaskaņots attēls (`--no-aligned` vai uzņēmums, kuram deformāciju nevarēja piemērot) — pēc savas kameras orientācijas.**Noklusējums: izslēgts — katrs elementa fails paliek sākotnējā formātā**, kā tas vienmēr ir bijis masīva uzņēmumiem. `raw` un `debayered` jebkurā gadījumā paliek oriģinālajā orientācijā. Katrs fails reģistrē orientāciju, kādā atrodas tā produkti (sk. [Kameras orientācija](#kā-izskatās-uzņemumu-mapes)); `Alignment*` XMP saglabā sākotnējā kadra transformāciju, kas piemērota pirms orientācijas, tādējādi `process` (deformācija, tad orientācija) ataino reālo attēlu. |
| `--index` / `--no-index` | Saglabāt / izlaist katrai kamerai atsevišķo veģetācijas indeksa pārklājumu, ja tāds ir konfigurēts. Noklusējums: renderēt to. |
| `--no-daq` | Izlaist `.daq` papildu failu, ja nevienā izvēlētajā līmenī netiek izmantots DAQ (tikai neapstrādātu datu iegūšana). Pēc noklusējuma rādījums tiek saglabāts, lai kadrus varētu pārstrādāt atstarojuma/indeksa rādītājos bezsaistē. `--force-daq` joprojām tiek pieņemts (noklusējums). |
| `--daq-tolerance-ms MS` | DAQ saskaņošanas pielaide šiem uzņēmumiem (noklusējumā 500 ms). |
| `--smart` | Pirms izraisīšanas jāpagaida, līdz AE stabilizējas visās kamerās (skatīt [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF pikseļu kompresija. `none` (noklusējums kopš 1.3.0; 1.2.0 rakstīja `deflate`) = nesaspiesti — sensora pikseļi tieši tā, kā tie tiek nolasīti, ~6,3 MB uz vienu pilnas izšķirtspējas kadru, un ātrākais ilgstošais pārraides ātrums; `deflate` = bezzaudējumu zlib L1 + horizontālais prediktors, ~4,1 MB uz vienu kadru, lēnāks. Abas metodes ir bezzaudējumu un importēšanas brīdī tiek nolasītas identiski. |

> **Vienreiz rakstāms TIFF + pastāvīgā pārraides ātruma modelis.**Katrs fails tiek ierakstīts**vienreiz**: pikseļi, XMP pakete, IFD0 Make/Model/CameraSerialNumber un EXIF `ExposureTime` / `SerialNumber` tagi vienā darbības ciklā, un pēc tam nekas to nepārraksta. `--continuous` fiksē secīgas sinhronizētas grupas — katrs fiksējums pārņem grupu pēc iepriekšējās, ieskaitot to, kas publicēta, kamēr šis fiksējums vēl tika saglabāts — tādējādi tas darbojas ar masīva ātrumu. DEFLATE aizņem Python GIL, tādēļ saspiestie ieraksti**netiek**paralizēti pa atsevišķiem kameras rakstītāja pavedieniem; nepārtrauktai pilnas izšķirtspējas uzņemšanai ar sensora ātrumu ir nepieciešama nesaspiesta rakstīšana (noklusējums)**un** NVMe klases disku (~600 MB/s ilgstoša ātrdarbība 9 kameru masīvam pie ~10 fps). Tas pats regulētājs ir pieejams kā `compression` uz `POST /api/camera/array/capture`.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (aligned is the default; drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --no-index -o out/

# Each member at its native size, unwarped
chloros-cli lattice array-capture --processing reflectance --no-aligned -o native/

# Continuous raw capture for 60 s, as fast as the array publishes
chloros-cli lattice array-capture --continuous --duration 60 --fastest -o flightline/
```

#### Nepārtrauktais režīms (`--continuous`)

`array-capture --continuous` (bez `--interval` vai `--smart`) nodod vadību **backend pašas nepārtrauktajai uzņemšanai** — sesijai, kurā darbojas GUI nepārtrauktais režīms. Aizmugurējā sistēma pati saglabā katru sinhronizēto grupu, vienlaikus veicot divus ierakstus un nesaņemot pieprasījumus no CLI starp grupām, tādējādi tā spēj tikt līdzi ātrai kameru sistēmai, kurā pieprasījums uz vienu ierakstu atpaliek (izmērīts 73–77 ieraksti/s 9 kameru sistēmā ar 512 pikseļu ROI). Katrs no iepriekš minētajiem eksportēšanas slēdžiem attiecas uz katru uzņemumu.

- **Ierobežojumi:** `--count N` apstājas pēc N uzņemumiem (viens uzņemums = viena sinhronizēta grupa: katra dalībnieka faili); `--duration S` — pēc S sekundēm. Ja nav norādīts ne viens, ne otrs, programma darbojas līdz brīdim, kad tiek sasniegts `Ctrl+C`, disks ir pilns, rodas kļūda vai sistēma tiek izslēgta. `Ctrl+C` aptur aizmugures sesiju, pēc tam izdrukā kopsavilkumu.
- **Progress** ik pēc 2 s, pievienojot `, N failed` un `, N groups skipped`, ja vērtība nav nulle (izlaistā grupa ir tā, kuru masīvs publicējis, bet kuras saglabāšana nav notikusi):

  ```
     12.0s  880 captures (73.3/s), 7920 files
  ```

- **Nav saraksta pa atsevišķiem failiem.** Darbības izpilde beidzas ar vienu kopsavilkuma rindu — saglabātie faili, ātrums, failu skaits un izvades mape — kam seko `N file(s) failed to write.` / `N group(s) skipped.`, ja vērtība nav nulle:

  ```
  Done — 880 captures (73.3/s), 7920 files in /home/user/flightline.
  ```

- **Pārtrauc darbu**, ja disks ir pilns (`Continuous capture stopped: the drive is full.`), kā arī kļūdas gadījumā vai masīva pārtraukšanas gadījumā (`Continuous capture stopped: <reason>.`) (abas parādās stderr).
- **Beigu statuss:** `0`, ja ir saglabāti uzņēmumi un visi faili ir ierakstīti, ieskaitot `Ctrl+C`; `1`, ja kāds fails nav ierakstīts, diska pārpildīšanas gadījumā, kļūdas gadījumā, masīva pārtraukšanas gadījumā vai ja ieraksts vispār netiek veikts.
- **Microsoft Defender:** ja Defender izmanto vienu vai vairākus procesora kodolus, lai skenētu failus, kurus ieraksta programma, izpildes laikā vienu reizi tiek izvadīts stderr: `Note: Microsoft Defender is using N.N CPU cores scanning the capture files, CPU the capture needs. Re-run the Chloros installer with "Exclude the Chloros backend from Microsoft Defender scanning" checked.` Skatīt instalētāja [Sistēmas iestatījumus](#windows-exe).
- Vienlaikus ir iespējama tikai viena nepārtraukta ierakstīšana katram masīvam; otrā tiek noraidīta ar kļūdu `a continuous capture is already running for this array`. HTTP sesija ir `POST /api/camera/array/continuous/start` (`/api/camera/array/capture` ķermenis plus `max_captures` / `duration_s`), `GET /api/camera/array/continuous/status?array_id=ID` un `POST /api/camera/array/continuous/stop`.

**Intervāls**(`--interval S`, ar vai bez `--continuous`) un**`--continuous --smart`** saglabā cikliskumu uz pieprasījumu: viens uztveršanas pieprasījums katrā ciklā — katrā plānotajā brīdī Interval režīmā, pēc tam, kad AE ir stabilizējies `--smart` — ar katra cikla failiem, kas uzskaitīti tāpat kā vienkāršajā režīmā, `Done — N capture pass(es).` rindu beigās un `--count N`, kas skaita ciklus.

### `array-record` — kombinētais indekss video/GIF (uzraudzības līmenī)

Ieraksta masīva **reāllaika kombinēto indeksu skatu** failā `.avi` (un pēc izvēles arī failā `.gif`). Ja ir atvērts GUI kombinētais skats, tas ieraksta šo skatu tā, kā tas tiek parādīts. Ja neviens skats nav atvērts, aizmugurējā sistēma pati renderē skatu: indeksu, ko nododat ar `--expr`, vai pēc noklusējuma masīva NDVI, tiklīdz masīvs ir saskaņots. Noklusējuma NDVI izmanto sarkano un NIR joslas, kas atrodas vistuvāk 660 nm un 850 nm, tāpat kā reāllaika skatā. Ierakstīšanas laikā atvērtais GUI skats tiek ierakstīts, kamēr tas paliek atvērts. Tas ik pēc 2 s pārbauda progresu un apstājas pie `--duration`, `Ctrl+C`, vai tad, kad ierakstītājs pats pārtrauc darbību.

Ieraksts, kurā netiek ierakstīts neviens kadrs, beidzas ar kļūdu: ierakstītājs ziņo par kļūdu `no-frames: …` (masīvs var joprojām būt izlīdzināšanas vai ekspozīcijas stabilizēšanas procesā) un komanda **iziet no programmas `1`**.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF (no GUI needed)
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# Record a chosen index instead of the default NDVI
chloros-cli lattice array-record --duration 30 \
  --expr "(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)" -o monitoring/
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--array-id ID` | tikai matrica | Mērķa matrica (izlaidiet, ja ir pieslēgta tikai viena). |
| `-o, --output DIR` | `output` | Izvades katalogs. Relatīvais ceļš ir no mapes, no kuras izpildāt komandu. |
| `--fps F` | `10` | Ierakstīšanas kadru ātrums. |
| `--duration S` | līdz Ctrl+C | Automātiska apstāšanās pēc `S` sekundēm. |
| `--gif` | izslēgts | Ierakstīt arī animētu GIF failu. |
| `--gif-only` | izslēgts | Saglabāt tikai GIF failu (bez `.avi`). |
| `--expr EXPR` | masīva NDVI | Indeksa izteiksme, kas jāieraksta, ja nav atvērts neviens GUI skats, masīva joslu nosaukumos (piem., `'(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)'`; skatīt [joslu nosaukumus](#index--vegetation-maths)). Īss krāsas nosaukums (`NIR`, `Red`) darbojas tikai tad, ja to satur viena masīva josla. Ja GUI skats ir atvērts, tiek reģistrēts tas, kas tiek parādīts. |

### `array-burst` — neapstrādāta Bayer augstas kadru frekvences sērija (analīzes kvalitāte)

Lasīta tieši no uzņemšanas cikla sinhronizētās grupas bufera — **nav nepieciešama kalibrēšanas ķēde, exiftool vai tiešraides skats** — tādējādi tā darbojas ar kameras pilnu uzņemšanas ātrumu. Raksta neapstrādātus kadrus + manifestu katram kadram + vienu `.daq` par katru atsevišķu DLS nolasījumu saskaņā ar `<output>/bursts/<base>/`. Pārstrādājiet bezsaistē (nākamā komanda) vai nododiet `--build`, lai to izdarītu nekavējoties pēc apstāšanās.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--array-id ID` | tikai masīvs | Mērķa masīvs. |
| `-o, --output DIR` | `output` | Izvades katalogs (dati tiek saglabāti `<DIR>/bursts/<base>/`). |
| `--duration S` | līdz Ctrl+C | Automātiska apstāšanās pēc `S` sekundēm. |
| `--max-frames N` | neierobežots | Automātiskaapstāšanās pēc `N` neapstrādātiem kadriem. |
| `--orient` | izslēgts | No šīs sērijas izveidotie rezultāti tiek pagriezti atbilstoši kameras orientācijai — katras kameras rezultāti tiek pagriezti atbilstoši tās pašas kameras orientācijai, apvienotais indekss — pēc galvenās kameras orientācijas, kā to dara `array-capture --orient`. Neapstrādātie kadri paliek sākotnējā formātā; sērijas `manifest.json` reģistrē katra kadra orientāciju un izvēlēto opciju. |
| `--build` | izslēgts | Pēc apstāšanās nekavējoties atkārtoti apstrādājiet sēriju (tāpat kā `array-build-video`). |
| `--products …` | `combined:index` | Ar `--build`: kurus video veidot (skatīt zemāk). |
| `--fps F` | `10` | Kopā ar `--build`: izejas videoklipa kadru skaits sekundē (fps). |
| `--save-tiffs` | izslēgts | Kopā ar `--build`: saglabā arī katram kadram kalibrētus TIFF failus (katram ar savu [unmix clamp mask](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on), ja pikselis ir ierobežots; kompilācijas rezultātā tie tiek uzskaitīti kā `clamp_masks`). |
| `--gif` | izslēgts | Ar `--build`: raksta arī animētus GIF failus. |

### `array-build-video` — saglabātās sērijas pārstrāde bezsaistē

Katru neapstrādāto kadru laika ziņā saskaņo ar tuvāko saglabāto `.daq` rādījumu un virza to caur **to pašu starojuma / atstarošanas / indeksa ķēdi kā importēšanas procesā**, renderējot vienu vai vairākus video.

`--products` ir ar komatu atdalīts `kind:level` elementu saraksts, kur `kind` ∈ `per_cam` | `combined` un `level` ∈ `radiance` | `reflectance` | `index`. Ja ir norādīts tikai `level` (bez `kind:`), noklusējuma vērtība ir `per_cam`. Noklusējuma vērtība ir `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Karodziņš | Noklusējums | Apraksts |
| --- | --- | --- |
| `--burst-dir DIR` | (obligāts) | Celiņš uz sērijas mapi (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` saraksts, kā iepriekš. |
| `--fps F` | `10` | Izvades video kadru skaits sekundē (fps). |
| `--save-tiffs` | izslēgts | Papildus video failiem saglabāt arī katram kadram kalibrētus TIFF failus. |
| `--gif` | off | Izveido arī animētus GIF failus. |

**Videoklipam nav klampēšanas maskas** (viens attēlu plūsma, 8 biti). Tā vietā izveides rezultātā tiek iekļauts `clamp_fractions`: katram videoklipa produktam, katra kadra pikseļu daļa, ko atdalīšanas process ierobežojis joslā, ko satur tā R / G / B kanāls — `Chloros:Band{R,G,B}UnmixClampFraction`, ar ko tiktu apzīmogots šī kadra starojuma/atstarošanas reizinājums (kopējam indeksam, katras iesaistītās kameras). Komandas izpilde izdrukā sliktāko kadru katram produktam `Clamp:` rindā. `--save-tiffs` pievieno pikseļu maskas TIFF failiem, `clamp_masks/<level>/` blakus TIFF failu mapei `<level>/`.**Pārtraukta kompilācija neatstāj bojātus video failus.** Katrs video tiek ierakstīts kā `<name>.partial.avi` un iegūst galīgo nosaukumu tikai tad, kad ir pabeigts, tādēļ vecāks video ar šādu nosaukumu paliek, līdz tā aizstājējs ir pilnīgs. Palaižiet kompilāciju vēlreiz, lai aizstātu atpalikušo `.partial.avi`.

Komandas izpilde izdrukā katru ierakstīto video, pēc tam jebkuru `Skipped:` rezultātu (pieprasīto kameru/līmeni — vai kombinēto indeksu —, kas neizveidoja video, norādot iemeslu — piemēram, nav kalibrēšanas paketes, nav DLS rādījuma atstarojumam vai nav sarkanā/NIR joslu pāra kombinētajam indeksam) un jebkuru `Note:`. RGB kamera, kurai pieprasīts starojums vai atstarojums, tā vietā saņem savu attēla video, kam veikts baltā līdzsvara korekcija, ja ir pieejams kameras kalibrēšanas komplekts; kamera, kas renderēta bez tā, tiek norādīta `Note:`. Tā **iziet ar rezultātu, kas nav nulle**, ja pieprasītais produkts tika izlaists vai netika izveidots atbilstoši pieprasījumam (konfigurētais indeksa izteiksme netiek izvērtēta bezsaistē); izveidotie video joprojām tiek ierakstīti un uzskaitīti.

> **Izvēlieties pareizo ierakstītāju.** `array-record` ir *uzraudzības līmeņa* — tas ieraksta renderēto tiešraides kompozīciju: GUI apvienoto skatu vai, ja GUI nav atvērts, paša backend’a NDVI (vai `--expr`) skats. `array-burst` → `array-build-video` ir *analīzes līmeņa* — tas saglabā neapstrādātus sensora datus ar pilnu ātrumu un pēc tam rekonstruē kalibrētus starojuma/atstarošanas/indeksa video, kam nav nepieciešams tiešraides skats.

### Mono (M3M) vienfrekvenču kameras

**M3M**sērija ir Bayer**M3C**mono versija: viena šaurjoslas interferences filtra kamera (`M3M-<lens>-F<wavelength>`, piemēram, `M3M-L87-F685`), tādējādi sensors nodrošina**vienu pelēktoņu joslu** bez Bayer mozaīkas. Nav nekas, ko demosaicēt, nav kanālu savstarpējās pārklāšanās, ko atdalīt, un nav jāiestata balansa — visa RGB attēla krāsu apstrādes ķēde vienkārši nav piemērojama.

Ko tas nozīmē CLI vidē:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**atpazīst mono kameru un**izlaiž to, parādot vienrindas ziņojumu**, nevis uzspiežot bezjēdzīgus iestatījumus. Tās joprojām darbojas normāli ar RGB/Bayer M3C kameru tajā pašā sesijā.
- **Bayer pikseļu formāts nozīmē tā dziļumu.** M3M piedāvā tikai mono formātus, tādēļ Bayer formāts no `--pixel-format` vai `--preset` iestata mono formātu ar tādu pašu dziļumu: `BayerRG8` un `--preset high_speed` veido `Mono8`; `BayerRG12`, `high_quality` un `triggered` veido `Mono12`; saspiests nosaukums paliek saspiests (`BayerRG12p` rada `Mono12p`). Faili ar nosaukumu `lattice format` ir Mono formātā. Masīvs, kurā ir sajauktas M3C un M3M kameras, izmanto vienu formātu tāpat: Bayer katrai M3C, tās Mono dziļums katrai M3M. Nosaukums atbilst tā bitu dziļumam: tas pats attiecas arī otrādi, tāpēc M3C nekad netiek iestatīta Mono formātā (`--pixel-format Mono12` ir `BayerRG12` uz M3C, neatkarīgi no tā, kura kamera ir masīva vadītāja), un `lattice format --set` tiek attēlots tāpat un izdrukā formātu, kas tam ir iestatīts.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** joprojām darbojas — starojuma un atstarošanas kartes ir *katrai joslai atsevišķas* radiometriskas kartes un ir pilnīgi precīzi definētas vienai joslai. Mono kadriem ir **identitātes** sensora reakcijas matrica (bez 3×3 atdalīšanas), tādēļ kalibrēšanas aprēķini tos neietekmē.
- **Viena mono kamera nevar ģenerēt veģetācijas indeksu.**NDVI, NDRE un citiem indeksiem ir nepieciešami vismaz divi spektra joslas (piem., Red + NIR). Lai iegūtu indeksu no mono aparatūras, virziet**vairākas** M3M kameras uz dažādiem viļņu garumiem, salieciet tās vienā daudzjoslu kopā un aprēķiniet *to*:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Mono_660 --channel nir=Mono_850 \
  --save-multiband -o output/
```

`index --live` pats atver kameras (kā [pagaidu sesiju](#multi-camera-transient-sessions)), tāpēc sākumā neko nepievienojiet: `--serials`, citādi profila aktivizētās kameras (skatīt [Indekss / Veģetācijas aprēķini](#index--vegetation-maths)). Kamēr Chloros kontrolē jebkuru kameru (GUI, `cam-connect`, `array-connect`) tiek noraidīts ar HTTP 409 kļūdu `CAMERA_HELD` un programma tiek aizvērta `1`; atbrīvojiet tās ar `chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`.

`--channel` simboliem **precīzi** jāsakrīt ar iestatījuma kanālu nosaukumiem (tiek ņemta vērā lielu un mazu burtu atšķirība; NDVI ir ar mazajiem burtiem `red`, `nir` — skatīt `--list-presets`), un joslas nosaukums norāda uz joslu saskaņotajā kopā (bezsaistes režīmā tiek pieņemti arī joslu indeksi, sākot no 0, piemēram, `--channel red=0 --channel nir=1`).

Atšķirības pazīme visā kopā ir `M3M` simbols modeļa virknē (tas nekad neparādās `M3C` virknē), kas tiek parādīts GUI/SDK kā `is_mono`.

---

## Host NIC iestatīšana un optimizācija (LATTICE sistēmas)

LATTICE kameras pārraida GVSP caur uzņēmējdatora Ethernet adapteri, tāpēc daudzkameru sistēmās adaptera **draiveris**un**uzņemšanas gredzena izmērs** ir tikpat svarīgi kā savienojuma ātrums. Nepareizi iestatījumi parādās kā `FRAMES WILL DROP` / `Reduce ROI to enable` vārtiņos paneļa „Array Settings” (kā arī `lattice network-analysis` / SDK `analyze_array_network()`), pat ja pašas kameras darbojas pareizi.

### USB 10GbE adapteri — Realtek RTL8157 („Realtek USB 10GbE Family Controller”)

Zemāk minētie iestatījumi attiecas uz Windows draiveru īpašībām. Linux vidē USB adapterim arī tiek piemērots ierobežojums (400 MB/s x86_64 sistēmās, 200 MB/s uz NVIDIA Jetson) (skatīt [Masīva fps un pārsūtīšanas ātruma modelis](#array-fps--burst-model)), un katram adapterim ir nozīme kodola soketa uztveršanas buferiem — skatīt [Linux serveri](#linux-hosts--socket-receive-buffers).

| Pozīcija | Nepieciešamā vērtība | Kāpēc tas ir svarīgi |
| --- | --- | --- |
| **Draivera versija**|**≥ v10.67 (2026. gada janvāris)**, INF `rtump64x64sta.inf` | Novecojušais **2016. gada** draiveris (v10.65, `rtump64x64.inf`) nepareizi apstrādā izslēgšanu un kļūdu pārbaudes ar**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**sistēmas izslēgšanas, pārstartēšanas vai miega režīma laikā. Pārejas process iestrēdz (~5 minūšu laika limits), lietotājs piespiedu kārtā izslēdz datoru, un atkārtotas nepareizas izslēgšanas**bojā WMI repozitoriju**(PowerShell/rīki sāk nedarboties ar kļūdu `Invalid class`) un**bloķē USB skopu** nākamajā palaišanas reizē (tīkla karte neieslēdzas; USB diski vairs netiek uzskaitīti). Veiciet atjauninājumu no realtek.com (vai USB atslēgas ražotāja), pirms paļaujaties uz pareizu pārstartēšanu. |
| **Uztveršanas buferi**— atslēgvārds `ReceiveBufferLen` |**256**(draivera maksimums) | Tīkla kartes RX gredzens. Draivera noklusējuma vērtība**32**atstāj tikai ~0,26 MB izmantojamā gredzena — pārāk maz, lai apstrādātu vairāku kameru datu plūsmu — tādēļ masīva panelis ziņo par kļūdu `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` un bloķē savienojumus. Pie**256**gredzens ir liels (**~13,5 MB, izmērīts laboratorijas 10GbE serverī**), nodrošinot RX cauruļvadam reālu rezerves kapacitāti daudzkameru GVSP sērijveida datu pārraidei. (To, vai konkrētā konfigurācija faktiski *izveido savienojumu*, nosaka divas pārbaudes — **iztukšošanas apzināšanās**pieņemamības pārbaude un**kopējās pārslogotības** pārbaude — nevis vienkārša salīdzināšana starp sēriju un gredzenu; skatīt [Masīva fps un sēriju modelis](#array-fps--burst-model).) |
| **Saņemamie URB**— atslēgvārds `PendingReceives` |**64** (maks.) | USB pieprasījumu bloki pārraides laikā; palieliniet kopā ar saņemšanas buferiem, lai absorbētu pārsūtīšanas plūsmas. |
| **Jumbo rāmis** — atslēgvārds `*JumboPacket` | **9014** | Nepieciešams 9000 baitu GVSP pakešiem (6 reizes mazāk pakešu/kadrā nekā 1500). |

> ⚠️ **NIC draivera atjaunināšana ATJAUNO šos papildu iestatījumus uz noklusējumiem.**Pēc adaptera draivera atjaunināšanas vai nomaiņas**no jauna piemērojiet** `ReceiveBufferLen=256` un `PendingReceives=64`, citādi masīva panelis atkal bloķēsies, pat ja „aparatūrā nekas nav mainījies”. Tas ir galvenais iemesls, kāpēc iepriekš darbojusies iekārta pēkšņi vairs nevēlas izveidot savienojumu.

Piemērojiet no **administratora tiesībām** ar PowerShell (aizstājiet ar savu adaptera nosaukumu, piemēram, `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` attiecas uz USB 10GbE adapteriem.** Tagad tiek atpazīts adaptera tips un iestatīts pareizais „receive-ring” atslēgvārds: `*ReceiveBuffers`→2048 PCIe tīkla kartēm (Intel I219, utml.), vai `ReceiveBufferLen`→256 + `PendingReceives`→64 Realtek **USB** 10GbE kontrolierim (kurš neizpauž `*ReceiveBuffers`). Mērķvērtības tiek ierobežotas līdz katra draivera ziņotajam maksimumam (`NumericParameterMaxValue`), tādējādi nekad netiek ierakstīta vērtība, kas ir ārpus diapazona. Palaidiet to no **privilēģēta** termināļa; tāpat kā jebkurai reģistra iestatīšanai, izmaiņas stājas spēkā pēc adaptera pārstartēšanas vai datora pārstartēšanas. Iepriekš minētās manuālās `Set-NetAdapterAdvancedProperty` komandas joprojām ir laba alternatīva — tās tiek piemērotas uzreiz (atjaunojot adaptera saistīšanu) bez pārstartēšanas.

### Linux sistēmas — soketa uztveršanas buferi

Linux sistēmās, tostarp NVIDIA Jetson, kodola soketa uztveršanas buferiem ir tikpat liela nozīme kā tīkla kartes uztveršanas gredzenam. Pēc kodola noklusējuma (`net.core.rmem_max` / `net.core.rmem_default` = 212 992 baiti) kameras GVSP pakešu plūsma pārpilda straumes soketu, pirms Chloros to paspēj nolasīt. Paketes tiek izmestas kodolā, lai gan tīkla karte pati neziņo par zaudējumiem, un masīvs zaudē kadru grupas un samazina kadru ātrumu. Veicot mērījumus ar 4 kameru masīvu, kas pieslēgts USB 10 GbE adapterim, abu parametru palielināšana līdz 32 MB pēc savienojuma izveides palielināja masīva kadru ātrumu no 4,96 līdz 7,52 fps, kā arī nepārtrauktas neapstrādātas datu uzņemšanas ātrumu no 4,7 līdz 7,6 uzņēmumiem sekundē, nezaudējot nevienu paketi kodolā.

- **.deb fails tos iestata.** Paketes instalēšana ieraksta `/etc/sysctl.d/60-chloros-gige.conf` (abus uz `33554432`) un uzreiz tos piemēro. Vērtība, kas jau ir iestatīta augstāk, netiek mainīta.
- **`lattice network` tos pārbauda.** Ja kāda no tām ir zemāka, sadaļā *Atklātās problēmas* tiek uzrādīts `socket receive buffers too small for GigE Vision (…)`, un aizmugurējās sistēmas žurnālā parādās tāds pats brīdinājums, kad tiek pievienota pirmā kamera vai masīvs.
- **Lai tos palielinātu manuāli** (līdz nākamajai sistēmas pārstartēšanai; pakotnes fails tos piemēro katrā sistēmas palaišanas reizē):

  ```bash
  sudo sysctl -w net.core.rmem_max=33554432 net.core.rmem_default=33554432
  ```

  `lattice network --fix` arī palielina un saglabā šos iestatījumus, bet tikai tad, ja backend darbojas kā root. Backend, kas darbojas kā jūsu lietotājs (tas, kas palaists ar `chloros-backend` vai [`chloros-backend@<user>`](#running-the-backend-as-a-service-headless)) nevar mainīt kodola iestatījumus un tā vietā izdrukā rindu `sudo sysctl`.

**Automātiskā adapteru optimizācija attiecas tikai uz pieslēgtiem adapteriem.** Pirmoreiz pieslēdzot kameru, kā root lietotājs darbojošais backend optimizē vadu adapterus, kuru uztveršanas gredzens ir mazs. Tagad tiek optimizēti tikai adapteri ar aktīvu savienojumu, tādējādi atvienotais integrētais ports saglabā savu MTU, un saņemšanas buferi netiek mainīti. `lattice network` vairs neuzrāda atvienota adapteraMTU kā problēmu. `lattice network`, `--fix`, `lattice probe` un adaptera optimizācija ņem vērā tikai vadu Ethernet portus, nevis Jetson CAN šķautni (`can0`) vai USB ierīces režīma savienojumus (`usb0`, `usb1`), ko tas piedāvā galvenajam datoram; USB Ethernet adapteris kamerām joprojām tiek ņemts vērā.

### Tīkla pamati (visi LATTICE savienojumi)

- **Adresēšana:** lokāla savienojuma adrese `169.254.0.0/16` (GigE Vision LLA). Galvenais dators izmanto statisku adresi `169.254.x.x/16`; kameras un DAQ-E pašas piešķir sev adreses tajā pašā diapazonā. Nav nepieciešams DHCP/vārteja.
- **Paketes izmērs:**ieteicams izmantot jumbo (9000), bet ļaujiet automātiskajai pārbaudei to noteikt — tā, izmantojot GVSP pārbaudi, ignorē kameras 1500 baitu ICMP ierobežojumu, tādējādi izvēloties jumbo izmēru tur, kur vads to patiešām pārraida.**Katra kamera tiek izmērīta vienu reizi katrā backend sesijā un atkārtoti, ja mainās tās savienojums (cits adapteris, cits savienojuma ātrums vai pārtraukts savienojums, piemēram, komutatora nomaiņas gadījumā); atkārtotas savienošanās gadījumā tiek izmantots iepriekšējais rezultāts. Pēc izmaiņām aiz komutatora, kas palicis ieslēgts, restartējiet backend, lai veiktu mērījumus no jauna.**Pirmā savienošanās šajā pārbaudē reizēm var aizņemt dažas minūtes, un žurnālā tiek norādīts, ja tas notiek. Izmantojiet fiksēšanu ar `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` tikai tad, ja zināt labāk nekā zonde, un dodiet priekšroku fiksēšanai katrai komandai, nevis pastāvīgai fiksēšanai: fiksēšana apiet zondi, tāpēc, ja ceļš faktiski nevar pārraidīt 9000,**katrs** uztveršanas mēģinājums beigsies ar laika limitu, izmantojot `SC_ERR_TIMEOUT -1011` (skatiet [Vides mainīgie](#environment-variables)).
- **RX gredzens mainās atkarībā no `ReceiveBufferLen`:**pēc noklusējuma `32` izmantojamais gredzens ir ~0,26 MB (pārāk mazs jebkurai daudzkameru sērijai); pie maksimālā `256` tas ir liels (~13,5 MB, izmērīts laboratorijas 10GbE serverī), nodrošinot reālu rezerves kapacitāti. To, vai konfigurācija izveido savienojumu, nosaka gan pārslodzes apzināta piekļuves pārbaude,**gan** zemāk aprakstītā kopējā pārslogojuma pārbaude — nevis vienkārša sērijas un gredzena kapacitātes salīdzināšana.

### Masīva kadru skaits sekundē (fps) un sērijas uzņemšanas modelis

Kā izprast masīva iestatījumu paneli (un `lattice analyze-array` / SDK `analyze_array_network`):

- **Burst tiek summēts katrai kamerai atsevišķi, ņemot vērā katras kameras reālo pikseļu formātu.**Mono**M3M**kameras pārraida**Mono12 (2 B/px)**vai, izmantojot `--pixel-format`, savas dziļuma Mono formātu (`BayerRG8` ir `Mono8`, 1 B/px);**M3C**Bayer kameras pārraida 8 vai 12 bitu datus (M3C programmaparatūra klusi izsniedz BayerRG12 pat tad, ja tiek pieprasīts BayerRG8). Tātad 4 kameru pilnas izšķirtspējas kadrs ir**~12,6 MB, ja visas ir 8 bitu, bet ~25 MB, ja trīs kameras ir 12 bitu mono**. Projekcija nosaka katras kameras formātu pēc tās modeļa (identitātes kešatmiņa), tādējādi datu plūsma atbilst tam, ko faktiski pārraida vads — nevis vienādam BayerRG8 pieņēmumam.
- **USB Ethernet adaptera ātrums ir ierobežots līdz 200 MB/s neatkarīgi no tā specifikācijās norādītā.** Efektivitātes tabula, kas pārvērš savienojuma ātrumu ilgtspējīgā rādītājā, ir atvasināta no PCIe; USB tīkla karte norāda savu *Ethernet* savienojuma ātrumu, taču to ierobežo USB šīna un tās draiveris. USB 10GbE adapteris, ko izmantoja, lai sasniegtu ~1063 MB/s „pastāvīgo” ātrumu — rādītāju, kas nekad netika pārbaudīts —, un rezultātā radušais pārraides ātruma ierobežojums sabojāja 6–18 % rāmju, vienlaikus joprojām ziņojot par normālu mērķa kadrādu skaitu sekundē (fps). USB pieslēgtajiem tīkla adapteriem tagad ir noteikts absolūts ierobežojums: **200 MB/s** operētājsistēmā Windows un uz NVIDIA Jetson, **400 MB/s** uz Linux x86_64 (uz Linux adapterim, kas pieslēgts USB 2.0 portam, ir piešķirti **30 MB/s**, un specifikācijā norādīts, ka tas jāpievieno USB 3 portam) (ierobežojumu nosaka datora USB šīna un draiveris, tādēļ tas nav atkarīgs no ražotāja norādītajām specifikācijām; USB 1 GbE adapteris nodrošina ~80 MB/s, un tas netiek ietekmēts). Linux vidē adapteris tiek identificēts pēc sysfs (izmantojot `ethtool -i` un `bus-info` kā rezerves). Katrs rādītājs ir izmērīts: Windows sistēmā tas pats 4 kameru masīvs sabojāja 6–10 % kadru, ja ātrums pārsniedza 200 MB/s; Linux x86_64 sistēmā tas pats adapteris darbojās bez kļūdām pie jebkura maksimālā ātruma līdz 480 MB/s (nodrošināja 273 MB/s, kas ir pašu kameru ierobežojums). Uz NVIDIA Jetson šis pats adapteris darbojās bez kļūdām tikai pie 200 MB/s, bet virs šī ātruma periodiski zaudēja kadrus. `wire_ceiling_source` spēju reģistrā to norāda vārdos, bet `nic_is_usb` to atzīmē ar karodziņu. Abos gadījumos to var pārrakstīt ar `--wire-ceiling-mbps`.
- **Pieeja ņem vērā datu plūsmas slodzi, nevis salīdzina pilnu datu plūsmu ar gredzenu.** Vienlaicīgajam pārsūtīšanas plūsmam ir jāiekļaujas tikai *pārejošajā uzkrājumā* = `max(0, Σ per-cam arrival − host drain) × emit_window`, nevis visā pārsūtīšanas plūsmā. Ātrā servera / lēnas kameras tīklā (**PCIe**10G saimniekdators + 4× 1 GbE kameras: ienākošais datu plūsmas apjoms ≈ 320 MB/s, iztukšošanās ≈ 1063 MB/s) hosta izvadīšanas ātrums pārsniedz kameru piepildīšanās ātrumu, uzkrājums ≈ 0, tādēļ pilnas izšķirtspējas vienlaicīgā pārraide**tiek pieļauta**, lai gan 25 MB pārsūtījums pārsniedz 13,5 MB gredzenu. Ja tās pašas četras kameras pieslēdz pie**USB**10GbE adaptera**Windows**vidē, izvadīšanas ātrums ir 200 MB/s, nevis 1063 — ienākošais datu plūsmas apjoms to pārsniedz, un zudums izpaužas kā bojāti kadri, nevis kā zemāks kadru ātrums. Uz 1 GbE resursdatora kameru 31,25 MB/s DLThr minimālā robeža liek ienākošajam datu plūsmam pārsniegt izplūdes ātrumu → tas pareizi**bloķē** (attiecībā uz *šo* bloķēšanas klasi samaziniet ROI vai izmantojiet binningu ≥ 2). Pieeja ir viena no **divām** savienojuma vārtiem — otra ir zemāk aprakstītā kopējā pārsubskripcijas pārbaude.
- **Prognozētais kadru skaits sekundē (fps) ir lēnākās kameras cikls.**Vienlaicīgas nosūtīšanas režīmā katra kamera nosūta savu kadru vienlaikus, tādējādi masīvs darbojas pēc lēnākā elementa sensora nolasīšanas un nosūtīšanas laika (kadra baitu skaits ÷ tā nosūtīšanas ātrums: kameras**piekļuves savienojums**, 1 GbE ≈ 80 MB/s, vai tās daļa no uzņēmējdatora vadu maksimālās caurlaidspējas, ja tā ir mazāka). Katra ekspozīcija pārklājas ar iepriekšējā kadra nolasīšanu, tādēļ ekspozīcija neiekļūst ciklā: automātiskā ekspozīcija aizpilda laika periodu, ko nosaka pārraides ātrums, pēc tam palielina pastiprinājumu, un vājš apgaismojums vairs nesamazina pārraides ātrumu (versijā 1.2.0 tas tā notika). Tikai manuāla ekspozīcija, kas ilgst ilgāk par šo periodu, palēnina izraisītāju (skatīt [Ekspozīcija masīva elementā](#exposure-on-an-array-member)). Kameru pievienošana nepalēnina masīvu, kamēr to vadu kopējā platība nesamazinās. Sērijveida izstarošanā (`slip-emit-and-capture`) kameru izstarošanas laiki tiek summēti, un katra elementa ekspozīcija tiek iekļauta pakāpeniskajā izstarošanā. Mērīts ar pilnu sensoru, 12 bitu, 9 kameru masīvā ar PCIe 10 GbE saimniekdatoru: 10,7 fps vai 14,0 fps ar [saspiestu datu pārraidi](#packed-12-bit-transport---packed).
- **Pēc nepārtrauktas uzņemšanas projekcija norāda, kāds bija uzņemšanas ātrums.** Uzņemšana, kas ilgst 30 s vai vairāk ar automātisko ātrumu, reģistrē to ātrumu, kas tika noteikts šim kameru masīvam šajā datorā; ātruma kontroliera īslaicīgie mēģinājumi ar ātrāku ātrumu netiek ņemti vērā. No šī brīža `array-connect` rinda un paneļa „Masīva iestatījumi” norāda šo ātrumu, ja tas ir zemāks par modeļa ātrumu, norādot, kas to ierobežoja: `clock-bound` (masīvs darbojās ar ātrumu, pie kura tas bija nostabilizējies, iegūšanas cilpa gaidīja savu trigera takts signālu), `loop-bound` (iegūšanas cilpai vairs nebija laika), `capture-bound` (iegūšana atpalika un izlaida grupas) vai `measured` (nav norādīts, kurš no tiem). Modeļa rādītājs ir `wire_fps_max`. Masīvs joprojām sasniedz to, ko nodrošina vads.
- **Masīvs darbojas jebkurā tīklā — viss ir atkarīgs no kadru ātruma.**Katra kamera saņem vienādu daļu no**sadursmju drošā vadu maksimālā apjoma**(`sustained × sim_emit_factor`), kas tiek sinhronizēts ar GevSCPD, un visas tās joprojām eksponē pēc viena un tā paša izraisītāja. Lēnā savienojumā daļa ir maza, un kadru ātrums samazinās līdz ar to: 8 pilnas izšķirtspējas 12 bitu kameras 1 GbE tīklā bez jumbo rāmjiem saņem 52 / 8 = 6,5 MB/s katra, aptuveni**1 fps**(aptuveni 1,3 kadrs/sek. saspiestā formātā), turpretim 10G serveris nodrošina 11–14 kadrus/sek. Ja kameras nevar sinhronizēt tik lēni — to var noskaidrot, izlasot katras kameras GevSCPD diapazonu —, masīvs pārslēdzas uz**seriālo**izsūtīšanu (`slip-emit-and-capture`): kameras pa kabeļu darbojas pa kārtām ar pilnu savienojuma ātrumu, tiek aktivizētas viena pēc otras, tādējādi katrs kadrs joprojām tiek saņemts, bet elementi**netiek atklāti vienlaicīgi**. `analyze-array` un „connect” izvades dati nosaka režīmu; iestatiet `--force-tier sim-capture-sim-emit`, lai atteiktos no rezerves režīma. Kadru izmērs šajā sadalē netiek ņemts vērā, tādēļ**attēlu apvienošana un mazāks ROI palielina kadru ātrumu, bet nemaina to, vai masīvs ietilpst**. Tikai tāds vads, kas nevar pārraidīt masīvu pat secīgi, tiek**pārslogots**— piemēram, ja kameru skaits ievērojami pārsniedz viena vada pārraides ātrumu vai ja tajā vienlaikus strādā daudzas citas kameras — un šajā gadījumā simptoms ir GVSP pakešu zudums, nevis zemāks ātrums: `analyze-array` iestata sasniedzamo kadru skaitu (fps) uz nulli un izdrukā `** OVER-SUBSCRIBED**`, savienojums tiek izveidots ar skaļu brīdinājumu, un HTTP-savienojums ar `pin_resolution: true` (stingrā režīmā) **tiek noraidīts**. Risinājumi ir mazāks kameru skaits, jumbo rāmji no gala līdz galam vai ātrāks tīkla interfeiss. `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` samazina atteikumu līdz skaļam brīdinājumam testēšanas nolūkos — skatiet [Vides mainīgie](#environment-variables).

### Masīva stāvoklis — kura apakšsistēma zaudē kadrus

Pieslēgtā masīva `GET /api/camera/array/<array_id>/capability` rāda reāllaika
`health` bloku, kas tiek pārvērtēts **10 sekunžu** ilgā nepārtrauktā logā. Tas sadala kadru zudumu
divos cēloņos, kuriem nepieciešami pretēji risinājumi, nevis ziņo par vienu „nepilnīgu”
rādītāju, kas nenorāda ne vienu, ne otru:

| Lauciņš | Kas tas nozīmē | Kura apakšsistēma |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (katram seriālajam portam) | Rāmis **ieradās, bet bija strukturāli bojāts**— GVSP paketes zudums. |**Tīkls**: vadu jauda, sinhronizācija, NIC RX gredzens, MTU |
| `never_arrived_rate_pct` (katram sērijas numuram) | Kadrs **vispār neieradās**— kamera neizšāva vai no tās nekas neizgāja. |**Izraisītājs / sinhronizācija**: M8 kabelis, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Sliktākais kameras rādītājs katram no tiem. | — |
| `per_cam_rate_pct` | Kopējais nepilnīgo rādītāju īpatsvars katrai kamerai (abi iemesli kopā). | — |
| `stable_for_seconds` | Cik ilgi katra kamera ir palikusi zem 0,01 %. | — |

Ja rādītājs pārsniedz 5 %, backend reģistrē `[array-health <id>] WARN` rindu, norādot sadalījumu — pie
pirmā pārkāpuma, mainoties nopietnības līmenim, reizi minūtē, kamēr tas turpinās, un vienu reizi, kad
tas izlīdzinās. Bojātā puse raksta `[gvsp-corrupt <SN>]` backend žurnāla failā par
pirmo reizi katrai kamerai un iemeslam, pēc tam kopsavilkumu ik pēc 60 s; lietotnes žurnāla panelī tiek parādīta
`[array-health <id>]` rinda. Katrs novērtējums joprojām nonāk aizmugures žurnāla failā;
skaitītāji mainās katrā buferī neatkarīgi no tā, kas tiek izdrukāts.

Tajā pašā ierakstā ir norādīts skaitlis, no kura atkarīgs viss piešķīrums:

| Lauks | Kas tas nozīmē |
| --- | --- |
| `wire_ceiling_mbps` | Spēkā esošais uzņēmējdatora ilgstošais datu pārraides budžets, MB/s. |
| `wire_ceiling_source` | No kurienes šis skaitlis ir iegūts, vārdos — piemēram, `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` (Linux x86_64 sistēmā — `USB-capped 400 MB/s (…; sysfs: bus=usb (usb2/2-1/2-1:1.0))`) vai `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, ja to nosaka `--wire-ceiling-mbps` (vai GUI laukā **Vadu budžets**). |
| `nic_is_usb` | `true` USB Ethernet adapterim — skatiet iepriekš minēto USB ierobežojumu. |

**Interpretācija:** `gvsp_corrupt_rate_pct` vērtība nav nulle, bet `never_arrived_rate_pct` ir 0
nozīmē, ka trigera signāls un kabeļa sinhronizācija ir ideāla un 100 % zudumu rada tīkla
ceļš — samaziniet `--wire-ceiling-mbps` un atkārtoti izveidojiet savienojumu. Pretējais modelis norāda uz
sinhronizācijas kabeli vai trigera līniju.

> **`--target-fps` nav rādītājs bojātiem rāmjiem.** GevSCPD ritms tiek iestatīts
> vienreiz savienojuma izveides brīdī, tādēļ trigera frekvences samazināšana maina darba ciklu, nevis
> vienlaicīgās pārraides pārraides ātrumu. Izmērītais 5× pieprasījuma samazinājums nedeva uzlabojumus;
> samazinot vadu maksimālo ātrumu no 240 līdz 200 MB/s, tajā pašā iekārtā bojājumu īpatsvars samazinājās no 10,4 %
> līdz 0,00 %.

> **Darbojošs masīvs nevar pats pārplānot savu darbību ar LATTICE (M3C / M3M) programmaparatūru.** Tas
> nevar šo problēmu atrisināt pats; atvienojiet un atkārtoti pieslēdzieties, lai savienojuma laika izvēlne varētu
> veikt pārplānošanu ar jauno maksimālo ātrumu.

### Simptoms → risinājums

| Simptoms (Masīva iestatījumi / savienošana / `analyze_array_network`) | Cēlonis | Risinājums |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` atiestatīts uz 32 (parasti pēc draivera atjaunināšanas) | Iestatiet `ReceiveBufferLen`→256, `PendingReceives`→64; atveriet paneli no jauna (pārstartējiet backend, ja tas ir saglabājis veco gredzena izmēru kešatmiņā) |
| Pārstartēšana/izslēgšana iestrēdz; vēlāk `Invalid class` WMI kļūdas, NIC neieslēdzas, USB diski nav redzami | Vecais 2016. gada Realtek USB 10GbE draiveris → BSOD `0x9F` → piespiedu izslēgšanās | Atjauniniet adaptera draiveri līdz ≥ v10.67 (2026), pēc tam atkārtoti piemērojiet iepriekš minētos saņemšanas gredzena iestatījumus |
| Masīvs savienojas ar pilnu sensoru, bet kadru ātrums ir zems | Automātiskais režīms saglabā pilnu sensoru un ļauj ātrumam samazināties līdz tam, ko spēj pārvadīt kabelis | Izvēlieties vienu no ātrākajām opcijām, kas uzskaitītas savienojuma izvades sarakstā (`--binning 2` / `4` vai mazāku `--width`/`--height`), ieslēdziet jumbo kadrus vai uzlabojiet savienojumu |
| Masīvs ziņo par pareizu mērķa kadrskaitījumu sekundē, bet nodrošina tikai daļu no tā; `health.gvsp_corrupt_rate_pct` nav nulle, `never_arrived_rate_pct` 0 | No datora atvasinātais vadu budžets pārsniedz to, ko tas faktiski spēj uzturēt (tipiski USB Ethernet adapterim, šaurai PCIe joslai vai koplietotai struktūrai) | Atkārtoti izveidojiet savienojumu ar zemāku `--wire-ceiling-mbps` vērtību un vēlreiz pārbaudiet darbības stāvokļa bloku. **Nav** `--target-fps` — GevSCPD pārraides ātrums tiek fiksēts savienojuma izveides brīdī |
| Linux: masīvs zaudē rāmju grupas un samazina rāmju pārraides ātrumu, kamēr tīkla karte neziņo par datu zudumiem; `lattice network` uzrāda `socket receive buffers too small` | Kodola soketa uztveršanas buferi ir iestatīti uz noklusējuma vērtībām, tāpēc kameras paketes tiek zaudētas kodolā | Palieliniet `net.core.rmem_max` / `rmem_default` līdz 32 MB — skatiet [Linux serverus](#linux-hosts--socket-receive-buffers). .deb fails to veic instalācijas laikā |
| Kameras nav iekļautas publicētajās grupās; `health.never_arrived_rate_pct` nav nulle, `gvsp_corrupt_rate_pct` 0 | Izraisītājs / sinhronizācijas ceļš — kameras nedarbojas, nav tīkla problēma | Pārbaudiet M8 sinhronizācijas kabeli un `--line`; pārliecinieties, ka visi dalībnieki ir ieslēgti (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` pārsniegts `analyze-array`, vai savienojuma atteikums stingrā režīmā (`pin_resolution`; `array over-subscribes the wire`) | Vads nespēj pārraidīt datu masīvu pie jebkuras kameru sasniegtās pārraides ātruma, vienlaikus vai pa kārtām — kameru skaits pārsniedz vadu, kas spēj tās apkalpot, vai uz tā tiek pārraidīti citu kameru dati | Mazāks kameru skaits, jumbo rāmji no viena gala līdz otram vai ātrāks tīkla interfeiss. **ROI/binning NEPALĪDZ** (koplietošana nav atkarīga no rāmja izmēra). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` pārraksta testēšanas vidē (pieņem paketes zudumu) |
| Masīvs savienojas ar `slip-emit-and-capture` (sērijveida) lēnā tīklā | Kameras nevar darboties pietiekami lēni, lai vienlaikus dalītos vadā, tāpēc tās darbojas pa kārtām; kadri netiek eksponēti vienā un tajā pašā brīdī | Jumbo rāmji vai ātrāks tīkla interfeiss atjauno vienlaicīgu uzņemšanu; `--force-tier sim-capture-sim-emit` uzstāj uz to (un tā vietā zaudē paketes) |

---

## `chloros-cli daq`

Spektrālo sensoru komandas. `pool-*` komandas ir vienkārši HTTP-klienti, kas vada sensoru caur backend pastāvīgo pūlu, tāpēc tām ir nepieciešams darbojošs backend (skatīt [Backend palaišana](#start-the-backend)). Aizmugurējam serverim pieder transporta kanāls, tādēļ CLI un SDK skripti izmanto vienu aktīvu rokturi, nevis cīnās par seriālo portu. `pool-discover` ir vienīgais veids, kā uzzināt DAQ-M BLE MAC adresi.

> **`chloros-cli daq --help`** (vai `-h`, vai `daq help`) uzskaita `pool-*` apakškommandas (Linux vidē kopā ar tiešajām aparatūras komandām, kas iekļautas kompilācijā), un `chloros-cli daq <subcommand> --help` (vai `daq help <subcommand>`) norāda katras apakškommandas opcijas. Tās aptver savienošanos, plūsmu, kalibrētu `.daq` failu ierakstīšanu, kapacitātes profilu apmaiņu un DAQ savienošanu ar kameru, kā arī DAQ-E tīkla iestatījumus, PTP, pārstartēšanu un programmaparatūras atjauninājumus. DAQ var vadīt arī no Python, izmantojot `chloros_sdk.connect_daq_sensor()`, kas izmanto to pašu apvienoto ceļu.
>
> **Izmantojiet `pool-*` komandas visās platformās.** Tās darbojas vienādi Windows, Linux un Jetson vidē. Windows vidē `daq discover` un `daq list` ir `pool-discover` aliāsi, un jebkura cita `daq` komanda, kas nav `pool-*`, izsauc `1` ar `DAQ direct-hardware commands require the full daq package`. Linux un Jetson vidē `daq discover`, `daq list` un citas komandas, kas nav `pool-*` un `daq`, ir DAQ rīka tiešās-aparatūras komandas: tās atver sensoru CLI paša procesā, ārpus aizmugurējās sistēmas resursu kopas (tātad ne uz sensoru, ko pārvalda aizmugurējā sistēma, grafiskā lietotāja saskarne vai skripts), un tur tiek pieņemtas komandas `daq discover`, `--only` un `--timeout`, bet ne `--json`. Šajā atsauces dokumentā ir aprakstītas tikai `pool-*` komandas.

### DAQ sensora pirmās savienošanas darba plūsma

```bash
# 1. Scan every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U serial port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq pool-discover
chloros-cli daq pool-discover --only ble           # BLE only
chloros-cli daq pool-discover --json               # machine-readable

# 2. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect                                   # smart-detect
chloros-cli daq pool-connect --port /dev/ttyUSB0               # DAQ-U on a serial port (Windows: COM3)
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 3. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 4. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

#    Bind it to a camera, so that camera's captures use and save its readings
chloros-cli daq pool-bind --camera 213800234 CB-7C-A8-2E-5F

# 5. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 6. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` atsauces materiāls

| Apakškommanda | Mērķis |
| --- | --- |
| `daq pool-connect` (smart-detect) | Atver sensoru aizmugures pūlā. |
| `daq pool-connect --port PORT` (`-p`) | DAQ-U konkrētā seriālajā portā. |
| `daq pool-connect --ble` | DAQ-M pār BLE, automātiski skenēts MAC. |
| `daq pool-connect --mac MAC` (`-m`) | DAQ-M, izmantojot BLE, ar zināmu MAC (nozīmē `--ble`). |
| `daq pool-connect --eth-host HOST` | DAQ-E pār Ethernet ar zināmu uzņēmuma nosaukumu vai IP (nozīmē `--eth`). |
| `daq pool-connect --eth` | DAQ-E pār Ethernet, uzņēmējdators automātiski atklāts (mDNS + ARP rezerves režīms; darbojas no tukša ARP keša sistēmās Windows un Linux). |
| `daq pool-connect --integration-time MS (-t) --frame-avg N (-f) --no-ae` | Integrācijas loga / AE stāvokļa pielāgošana. |
| `daq pool-connect --no-stream` | Izveidot savienojumu, bet vēl nesākt datu plūsmu (turpināt ar `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Cap korekcijas profils. Noklusējuma iestatījums backendā ir `sunshine_cosine`; `none` ir sensors bez apvalka. [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor) to ignorē (fiksēts difuzors). **Sensori tiek piegādāti bez vāciņa, tāpēc nosauciet uzstādīto vāciņu:** noklusējuma vērtība tiek reģistrēta kā pieņēmums (`cap_id_source=auto_default`), un apstrādes procesā par to tiek parādīts brīdinājums, jo neapvalkots DAQ-U saules gaismas korekcijas apstākļos rāda apmēram 30 reizes augstākus rādījumus. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Pārskatiet katru transportu, meklējot sensorus, ar kuriem varētu izveidot savienojumu, neizveidojot to. **Tādā veidā var atrast DAQ-M BLE MAC adresi.** `--timeout` darbojas katram transportam atsevišķi (noklusējums 5 s, 1–20). Sensori, kas jau ir atvērti pūlā, netiek uzskaitīti — pieslēgts DAQ-M pārtrauc reklāmēšanu — tāpēc šiem sensoriem izmantojiet `pool-list`. `usb` uzskaita tikai USB seriālās ierīces: paša datora seriālie porti (`COM1`, Linux `/dev/ttyS*`) nevar būt sensori un tiek izlaisti. |
| `daq pool-list` | Parāda visus sensorus aizmugures pūlā. |
| `daq pool-bind (--camera SERIAL \| --array ARRAY_ID) {SENSOR \| --clear}` | Piesaista kameru vai masīvu DAQ, tāpat kā to dara GUI gaismas sensora izvēle: tās uzņemumi izmanto, saskaņo un saglabā šī DAQ rādījumus. `SENSOR` ir sensora identifikators no `pool-list` vai adrese, pa kuru var sasniegt DAQ. `--clear` (nav `SENSOR`) atceļ saistīšanu, un datu ieguve atgriežas pie jaunākā DAQ. Saistība pastāv līdz brīdim, kad tiek pārstartēts backend, vai līdz brīdim, kad šī kamera, matrica vai DAQ tiek atvienota: atvienošana (šeit — no SDK vai lietotnē) pārtrauc visas saistības ar atvienoto aparatūru, neatkarīgi no tā, kurš tās ir iestatījis, kā arī matricas elementu saistības ar pašu matricu. Kamerai jābūt pieslēgtai atvērtā projektā vai iepriekš atvērtai šajā datorā, un masīvam jābūt pieslēgtam vai atvērtā projektā; jebkurš cits sērijas numurs vai identifikators tiek noraidīts pēc nosaukuma, un nekas netiek saistīts. |
| `daq pool-disconnect {--sensor-id ID \| --all}` | Atbrīvošana — neatkarīgi no tā, kurš atvēra sensoru, tādējādi tiek atbrīvots arī sensors, ko avarējis skripts atstājis pūlā (vispirms tiek pabeigta jebkura ierakstīšana uz tā). Sensoram, kas neatrodas pūlā, nav ko atbrīvot: tad tiek izvadīts `ID: already released (not in the pool)` un programma tiek izbeigta ar `0`; ja atbrīvošanu aizmugurējā sistēma noraidīja vai neapstiprināja, programma tiek izbeigta ar `1`. SDK S: `DAQSensorSession.force_disconnect()`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | Pēdējie N spektra kadri (noklusējums — 1). |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Turpināt / apturēt straumēšanu. |
| `daq pool-record --sensor-id ID [--duration SEC] [-o DIR] [--device-name NAME] [--confirm-after S] [--heartbeat S]` / `--stop` | Sākt / apturēt .daq ierakstu. `-o` noklusējuma vērtība ir `~/Documents/DAQ Live View` aizmugures datorā (relatīvais ceļš atrodas mapē, no kuras izpildāt komandu); `--duration` (`-d`, noklusējums `0` = līdz `--stop`). `--confirm-after S` brīdina, ja S sekundes pēc sākuma nav saņemts neviens rādījums (noklusējums 20; 0 to atslēdz); ar `--duration` `--heartbeat S` ik pēc S sekundēm izdrukā nolasījumu skaitu un brīdina, ja tas vairs nepalielinās (noklusējums 30; 0 to atslēdz). Ieraksts uz DAQ-E, kas pārtrauc datu pārraidi, turpinās tajā pašā failā, tiklīdz aizmugures sistēma ir atjaunojusi savienojumu, un `--stop` uzskaita pārtraukumus ([DAQ-E, kas pārtrauc datu pārraidi](#a-daq-e-that-stops-delivering)). Ctrl+C priekšlaicīgi pārtrauc `--duration` ierakstu un par to ziņo; bez `--duration` ierakstīšana turpinās pēc Ctrl+C (iziet no `130`) līdz `--stop`. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Mainīt kapacitātes korekcijas profilu darbības laikā. Nepieciešams `--cap-id`; `--cap-id none` noņem kapacitātes korekciju (sensors bez vāciņa). DAQ-E-S saglabā `as_recorded` neatkarīgi no iestatījuma. |
| `daq pool-enable-ptp --sensor-id ID` | Atkārtoti ieslēdziet PTP ierīcē DAQ-E, kuras PTP ir izslēgts. Iestatījums tiek saglabāts ierīcē, tāpēc ierīce, kas ir atstāta izslēgta, paliek izslēgta — tās laika zīmogi netiek sinhronizēti ar PTP — līdz brīdim, kad tiek izpildīta šī komanda. `pool-list` atzīmē šādu ierīci (`!! PTP is switched OFF`) un izdrukā šo komandu tai. Chloros nekad pats neieslēdz ierīci atpakaļ, jo izslēgšana var būt apzināta. |
| `daq pool-info --sensor-id ID [--json]` | DAQ-E ierīces paša iestatījumi: tās programmaparatūra (un tas, vai šajā Chloros versijā ir iekļauta jaunāka versija), tās adrese un veids, kā tā tika iegūta (DHCP / link-local / statiska), saglabātie tīkla iestatījumi, kamēr sensors tos izmanto (tostarp tie, kas saglabāti ar `--no-reboot`), datu plūsma ieslēgšanas brīdī un PTP. Ja sensora datu plūsma nesasniedz šo datoru, programma nolasa sensora datus, izveidojot jaunu savienojumu, un par to paziņo. |
| `daq pool-set-network --sensor-id ID {--auto \| --static IP [--mask M] [--gateway G]} [--no-reboot] [--force] [--timeout S]` | DAQ-E iestatīšana uz DHCP (`--auto`) vai statisku adresi (`--mask`, noklusējums `255.255.255.0`). Sensors pārstartējas, lai piemērotu šos iestatījumus, un atkārtoti izveido savienojumu ar iepriekšējiem iestatījumiem, gaidot līdz `--timeout` sekundēm (noklusējums 45); `--no-reboot` to tikai saglabā. Skatīt [DAQ-E ierīces iestatījumi](#daq-e-device-settings). |
| `daq pool-set-auto-stream --sensor-id ID {--on \| --off}` | Vai DAQ-E sāk datu pārraidi ieslēgšanas brīdī, ja nav pieslēgta programmatūra (saglabāts sensora atmiņā). Chloros sāk datu pārraidi jebkurā gadījumā. |
| `daq pool-reboot --sensor-id ID [--timeout S]` | Pārstartējiet DAQ-E un atkārtoti savienojiet to ar iepriekšējiem iestatījumiem (gaida līdz `--timeout`, noklusējums 45 s). |
| `daq pool-update-firmware --sensor-id ID [--timeout S]` | Ierakstiet šajā Chloros izlaidumā iekļauto DAQ-E programmaparatūras versiju sensora atmiņā, kurā darbojas vecāka versija, pēc tam to atkārtoti pieslēdziet (aptuveni minūti; neizslēdziet to; `--timeout` noklusējuma laiks, līdz tas atkal sāk darboties, ir 45 s). Sensoru, kas jau darbojas ar jaunāko versiju, atstāj mierā. |

### DAQ-E, kas pārtrauc datu pārraidi

DAQ-E, kas atrodas pūlā un 20 sekundes nepārraida spektru, tiek atkārtoti pieslēgts ar backend palīdzību. To veic, izslēdzot un atkal ieslēdzot komutatoru, kas baro sensoru: sensors kopā ar to restartējas un nesāk atkārtotu datu pārraidi, kamēr netiek saņemts attiecīgs rīkojums (ja vien nav iestatīts `pool-set-auto-stream --on`).

- **Vispirms tiek pārbaudīts, vai tas ir tas pats sensors.** Tiek nosūtīts pieprasījums uz sensora iepriekšējo adresi, neko nemainot, lai noskaidrotu sensora nosaukumu, MAC adresi un sērijas numuru. Tas nosūta pieprasījumu arī ar sensora `daq-e-<id>.local` nosaukumu, bet uzticas šai atbildei tikai tad, ja tā nāk no paša sensora tīkla adaptera (MAC). Neviens cits atbildētājs netiek atkārtoti savienots vai ierakstīts.
- **Tas atjauno savienojumu, izmantojot sesijas iestatījumus:** integrācijas laiku, kadru vidējošanu, automātisko ekspozīciju un ekspozīcijas profilu tā, kā tie bija iepriekš, nevis sensora ieslēgšanas brīža noklusējumus. Sensors, kuram bija ieslēgts PTP un kurš atgriezās ar to izslēgtu, tiek atkal ieslēgts. Pēc tam tiek atsākta datu plūsma un datora uztvērējs šai plūsmai.
- **Tiek veikti atkārtoti mēģinājumi ar arvien garākiem intervāliem:** uzreiz, tad pēc 5, 10, 20 un 40 sekundēm, pēc tam ik pēc 60 sekundēm, kamēr sensors nav pieejams. Katrā mēģinājumā sensora tiek vaicāts tikai tā nosaukums. Sensors, kas atbild, bet pēc trim atkārtotām savienošanās mēģinājumiem joprojām neko nesūta, tiek atstāts mierā un par to tiek ziņots (`auto-reconnect GAVE UP`). `pool-stream --start` mēģina atkārtoti.
- **`pool-list` parāda pašreizējo stāvokli.** Kamēr sensors klusē, tā `STREAM FAULT` rinda norāda, ko dara atkārtotās savienošanas process. Rinda izdzēšas ar pirmo spektru, un sensors pēc tam rāda `reconnected automatically N time(s)`.
- **Ierakstīšana turpinās tajā pašā `.daq`.** Fails katru pārtraukumu atzīmē ar divām rindām: `event_type` 5 pēdējā spektrā pirms pārtraukuma un 6 pirmajā pēc tā, katrā norādot tikai laika zīmogu. `pool-record --duration` izdrukā `!! STREAM LOST …`, kamēr sensors ir prom, un `stream resumed after …`, kad tas atgriežas. `pool-record --stop` uzskaita katru pārtraukumu un brīdina, tāpat kā tas notiek ar tukša faila gadījumā (ieraksts, kurā nav nekādu rādījumu, beidzas ar `1`).
- **Vienam ierakstam tiek saglabāta viena kalibrācija.** Ja sensora signāls atgriežas ar atšķirīgu kalibrāciju, kamēr notiek ieraksts, pārējā ieraksta daļa saglabā sākotnējo kalibrāciju, un brīdinājums par apstādināšanu to norāda. `pool-disconnect` + `pool-connect` pārņem jauno kalibrāciju.
- **Jūsu komandas ir noteicošās.** Sensors, kuru pārtraucat ar `pool-disconnect` vai apturat ar `pool-stream --stop`, nekad netiek atkārtoti savienots, pat ja atkārtota savienošana jau ir sākusies.
- **`pool-info` klusā sensora gadījumā** nolasa sensoru jaunā savienojumā un pievieno `!! stream NOT reaching this computer`. Par sensoru, kas neatbild, tiek ziņots nekavējoties. `pool-list` vairs neparāda PTP stāvokli sensoram, kas ir pārstājis atbildēt.
- DAQ-U un DAQ-M nav iekļauti. DAQ-M pats atkārtoti izveido savienojumu caur Bluetooth, bet DAQ-U, kas atvienots no USB, atkal ir nepieciešams `pool-connect`.

### DAQ-E-S — fiksēts difuzors un slīpuma sensors

**DAQ-E-S** ir DAQ-E ar divām atšķirībām, kas ir nozīmīgas programmatūrai. Pārējās funkcijas — atklāšana, savienošanās, PTP, ierakstīšana, zemāk minētie ierīces iestatījumi — darbojas tieši tāpat kā DAQ-E gadījumā.**Tās difuzors ir pastāvīgs, tādēļ nav nepieciešama vāciņa korekcija.** „Sunshine” tipa difuzors ir iebūvēts un atradās uz ierīces, kad tika veikta tās rūpnīcas kalibrēšana, tādējādi kalibrēšana jau rāda patieso lejupvērsto starojuma intensitāti. Chloros iestata tās vāciņu uz `as_recorded` (bez profila) neatkarīgi no pieprasījuma: vāciņš, kas nosaukts pēc tās (`sunshine_cosine`, `fov_*`, `none`), tiek ignorēts, parādot ziņojumu, un tās ierakstos tiek saglabāts `cap_id_source=model`, tāpēc brīdinājums par pieņemto ierobežojumu šim sensoram nekad neparādās. Neuzlieciet ierobežojumu tā datiem arī manuāli: `sunshine_cosine` augšpusē rādītu apmēram 11 reizes augstāku vērtību.

**Tam ir iebūvēts 3-asu akselerometrs.** Saules staru difuzora kosinusa reakcija ir spēkā tikai tad, ja tas atrodas horizontālā stāvoklī, tādēļ sasvērtā sensora rādījumi par starojuma intensitāti ir nepareizi — un atstarojums pārņem šo kļūdu — bez jebkādām citām pazīmēm. Tādēļ katrs DAQ-E-S rādījums ietver informāciju par sensora orientāciju:

| Lauciņš | Nozīme |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Leņķi grādos. `None`, ja programmatūra atzīmē leņķi kā nedefinētu (brīvais kritiens, nedarbīga ass). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Paātrinājums miligrāvos. `mag_mg`, kas ir tālu no ~1000, nozīmē, ka ierīce bija kustībā un leņķi šim paraugam nav nozīmīgi. |
| `cal_applied` | Iestatīts: asis un leņķi ir difuzora koordinātu sistēmā (piemērota ierīces montāžas kalibrācija). Neiestatīts: neapstrādāta platnes koordinātu sistēma, kas var rādīt līdz pat ~2,3° slīpumu, kas nav reāls. |
| `sample_age_ms` | Akselerometra parauga vecums brīdī, kad tika nosūtīts spektrs. |
| `temp_raw` | Nekalibrēta mikroshēmas temperatūras **tendence** — tikai atšķirības, nekad ne termometra rādījums. |

`daq pool-latest --json` tos atgriež zem katra rāmja `imu` atslēgas (`null` uz DAQ-U, DAQ-M vai parastajā DAQ-E). No DAQ-E-S reģistrētais `.daq` saglabā tās pašas vērtības savās `imu_*` kolonnās (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) blakus katram spektram, lai lasītājs varētu atšķirt mākoni no apgāztā statīva. Visos pārējos modeļos šīs kolonnas ir tukšas.

Stāvokļa plūsma tiek ziņota atsevišķi: `daq pool-list` izdrukā `!! ATTITUDE STREAM FAULT (spectra unaffected)`, kamēr tas ir izslēgts, un norāda, no cik daudzām kļūdām tas pēc tam atguvās. Kļūda šajā plūsmā nekad nerada pārtraukumu spektrālajā ierakstā. `daq pool-discover` uzskaita ierīci kā `DAQ-E-S` (kā `DAQ-E`, ja tā neatsakās uz identitātes pārbaudi aptuveni 2 s laikā).

### DAQ-E ierīces iestatījumi

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330

# A fixed address on the network this computer is on (192.168.1.x)
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1

# Back to the factory setting: DHCP, falling back to a 169.254.x.x
# link-local address when no DHCP server answers
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto

chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

- **`--static` bez `--gateway` nesaglabā vārteju** (visas iepriekš iestatītās vārtejas tiek dzēstas), kas ir piemērots kameru tīklam bez maršrutētāja.
- **Šim datoram tīklā ir jābūt statiskai adresei.** Chloros noraida tādu, kura apakštīklā nav iekļauta šī datora adrese sensora tīklā: pēc pārstartēšanas nekas nevarētu sasniegt sensoru, lai to mainītu atpakaļ. Izmantojiet `--force` tikai tad, ja šim datoram tiks piešķirta jauna adrese vai tas tiks maršrutēts uz jauno apakštīklu.
- **Pārstartēšana tiek noraidīta, kamēr sensors veic ierakstīšanu.** Vispirms apturiet ierakstīšanu (`pool-record --stop`). Ieraksts, kas sākts brīdī pirms pārstartēšanas, tiek pārtraukts tās rezultātā, un `pool-set-network`, `pool-reboot` un `pool-update-firmware` beidzas ar rindu `Warning:`, kurā norādīts ieraksta faila nosaukums (arī tad, ja sensors pēc tam neatgriežas).
- **Pēc pārstartēšanas Chloros atkārtoti savieno tikai pašu sensoru.** Tas gaida vietā, kur atradās sensors — un pēc `--auto` arī zem tā `daq-e-<id>.local` nosaukuma — atbildi ar sensora nosaukumu, uzņēmuma nosaukumu un modeli, no tīkla adaptera (MAC), kas tam bija, un atkārtoti savieno to ar adresi, kas atbildēja. Atbilde ar šo nosaukumu tiek pieņemta tikai ar šo MAC adresi, tādēļ no datora, kas sasniedz sensoru caur maršrutētāju, vai no WSL2 tā noklusējuma NAT tīklā, sensors, kuru DHCP pārvietoja uz jaunu adresi, netiek atkārtoti savienots: komanda to norāda un izdrukā komandu `pool-connect --eth-host <its address> …`, kas to atjauno ar iepriekšējiem ekspozīcijas un ekspozīcijas ierobežojuma iestatījumiem.
- **`pool-update-firmware` noraida sensoru, kura programmaparatūras versiju tā nevar nolasīt.** Sensors, kuram pēc zibspuldzes iedegšanās atgriešanās aizņem ilgāku laiku nekā gaidīšanas laiks, nav bojāts: tiklīdz tas atbild, tas tiek atkārtoti savienots ar jaunās programmatūras iestatījumiem, un komanda brīdina, ka tas bija lēns.
- **Programmatūras atjaunināšanai ir nepieciešams ienākošs TCP savienojums.** Sensors atkal savienojas ar šo datoru, lai savāktu attēlu. Chloros klausās adapterī, kas maršrutē uz sensoru (katrā adapterī tikai tad, ja nevar noteikt, kurš tas ir), un apkalpo tikai paša sensora adresi; jebkurš cits savienojums tiek slēgts, neapkalpojot to. Windows vidē instalētāja ugunsmūra noteikums `Chloros Backend (TCP-In)` ļauj tam iekļūt (sk. [Windows (.exe)](#windows-exe)); ugunsmūris, kas to bloķē, izraisa atjaunināšanas kļūdu ar kodu `… device never connected back on TCP <port> …`.
- **WSL2 vidē izmantojiet spoguļoto tīklu** (`networkingMode=mirrored` un `%UserProfile%\.wslconfig`). Noklusējuma NAT režīmā šī datora adrese sensora tīklā ir WSL paša adrese, tādēļ visas statiskās adreses tiek noraidītas (izņemot `--force`), tiešraide nesasniedz WSL, un programmaparatūras atjaunināšana nevar tikt pabeigta, jo sensors atkal savienojas ar datoru, lai saņemtu attēlu.
- **Sensora ID un uzņēmuma nosaukums nemainās.** Neatkarīgi no adreses, DAQ-E joprojām ir `daq-e-<id>` un `daq-e-<id>.local`, tāpēc `--eth-host daq-e-<id>.local` to joprojām atrod.
- **GUI iestatījumi paliek nemainīgi**: cilne „Gaismas sensori”, sensora iestatījumi (zobrats), „Tīkls”. GUI saglabā savu savienojumu ar sensoru, tāpēc, ja maināt sensoru, ar kuru GUI jau ir atvērts savienojums, izmantojot GUI: pārstartēšana no CLI pārtrauc GUI sesiju ar to.
- Atbilstošie SDK modeļi ir `DAQSensorSession.device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` un `update_firmware()`.

---

## `chloros-cli project`

Atveriet, izveidojiet savienojumu ar un vadiet saglabātu Chloros projektu (mapes ar `cameras.json` + `sensors.json` + `project.json`). Viss tiek maršrutēts caur aizmuguri, tādējādi GUI un CLI nodrošina identisku aparatūras stāvokli.

### Apakškommandu atsauces

| Apakškommanda | Mērķis |
| --- | --- |
| `project open PATH [--relink FOLDER]` | Izvada projekta ierīču sarakstu (kameras, masīvi, sensori), nolasa to no failiem un pārbauda avota failus (skatīt [Pārvietots projekts](#a-moved-project---relink)). Tā neatver projektu backendā: tur atvērtais projekts, kā arī tā ierīces un ieraksti, paliek nemainīgi. Iziet no `1`, ja nevar atrast nevienu no projekta avota failiem. |
| `project devices PATH [--reconnect]` | Uzskaitīt vai atkārtoti palaist atklāšanu. |
| `project connect PATH [--cameras-only] [--sensors-only] [--packed {auto,on,off}]` | Savienot katru saglabāto kameru / masīvu / sensoru. Katrs masīvs savienojas tāpat kā `lattice array-connect`: līdz 600 s, tā soļi tiek izvadīti uz stderr (`[array <id>]    <s> s: <step>`; stdout ir JSON-ziņojums), un masīvs, kas šajā laikā nav pieslēgts, tiek atcelts — tā kameras tiek atgrieztas sākotnējā stāvoklī — un par to tiek ziņots ar kodu `errors`, nekad netiek atstāts pieslēgts. `--packed` iestata katra masīva saspiesto 12 bitu pārraides veidu (noklusējums `auto`; projekts nesaglabā iestatījumus katram masīvam atsevišķi; skatīt [Saspiests 12-bitu transports](#packed-12-bit-transport---packed)). Katrs gaismas sensors tiek atvērts aizmugurējās sistēmas DAQ pūlā pēc tā saglabātās adreses ar saglabāto integrācijas laiku, kadru vidējošanu, automātisko ekspozīciju un ierobežotāju: sensors, kuram nav izvēlēts ierobežojums, saņem `sunshine_cosine`, sensoru, kas saglabāts bez ierobežojuma (`none`), atstāj bez ierobežojuma, bet DAQ-E-S nekad neizmanto ierobežojumu. GUI DAQ panelis nav daļa no pūla: vispirms aizveriet sensoru tur. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | Viena uzņēmuma no nosauktas kameras vai masīva (`--prefix` pēc noklusējuma, `capture`). `--orient` (masīvi): masīvs saglabā neapstrādātus datus, kas paliek sākotnējā formātā — tas reģistrē, ka produkti, ko `process` no tiem izveido, ir jāorientē, kā `array-capture --orient`. Noklusējums: sākotnējais. `project burst` / `stream` un receptes `capture` / `burst` / `stream` (`orient: true`) izmanto to pašu opciju. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | N kadru sērija no norādītas kameras vai masīva (`-n/--count` noklusējums 5; `-i/--interval` sekundes starp kadriem, noklusējums 0). Masīva sērijas atbrīvojas no atkārtotām sinhronizētām grupām (novecojuma uzraugs), lai daļēji cikliskais masīvs nevarētu atgriezt N kopijas viena kadra; izvada rezultātus pēc katras iterācijas. Ja uzņemšana daļēji neizdodas, sērija tiek pārtraukta un programma iziet no `1`, norādot, cik daudz kadru tika saglabāti pirms tam. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--orient]` | Ierakstīšana uz diska ciklā: `--fps F` ieraksta otro (noklusējumā 3), vienu kadru katrā saglabāšanas reizē no kameras, vienu sinhronizētu kadru grupu no masīva (ar tādu pašu novecojuma uzraugu kā `project burst`), līdz `-n N` saglabā (noklusējums `0` = līdz brīdim, kad tiek nospiests Ctrl+C, kas parāda, cik daudz attēlu tika saglabāti, un iziet no `0`, vai `1`, ja netika saglabāts neviens attēls vai radās kļūda ar failu; ja pirms `-n N` notveršanas tiek nospiests Ctrl+C, programma `130` tiek izbeigta). Ja ierakstīšana neizdodas tās gaitā, plūsma tiek apturēta un tiek izbeigta `1`, norādot, cik daudz kadru tika saglabāti pirms tam. `--poll-interval` ir novecojis un tiek ignorēts: tas joprojām tiek pieņemts, lai vecākie skripti varētu darboties, ar piezīmi stderr. |
| `project sensor read PATH NAME [--json]` | Pēdējais spektra kadrs. |
| `project sensor log PATH NAME [--seconds SEC] [-o DIR] [--device-name NAME]` | Ieraksta .daq failu `--seconds` (noklusējums 10). Ja ieraksts nesatur nekādus mērījumus, par to tiek ziņots stderr un programma tiek izbeigta `1`. |
| `project run PATH RECIPE.yaml` | Izpildīt YAML/JSON datu ieguves recepti. `--dry-run` veic validāciju, neizpildot. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Aprēķina masīva izlīdzinājumu — skatiet [zemāk esošo karogu tabulu](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Izvada pašreizējo saskaņošanas profilu. |
| `project align clear PATH NAME` | Dzēš kešēto profilu. |
| `project align tweak PATH NAME --serial SN [--dx N] [--dy N] [--rotation-deg N] [--scale N]` | Nedaudz maina viena pakļautā procesa transformāciju (noklusējumi: `0`, `0`, `0`, `1.0`: bez izmaiņām). |
| `project align export PATH NAME --to FILE` | Saglabāt profilu JSON formātā. |
| `project align import PATH NAME --from FILE [--no-validate]` | Ielādēt saglabātu profilu. |

#### `project align calibrate` opcijas

| Opcija | Noklusējums | Apraksts |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Izlīdzināšanas metode. **Šie rakstības varianti atšķiras no `lattice align-calibrate`**, kas pieņem saīsinātos variantus `orb` / `akaze` / `phase`; šajā opcijā abas komandas nav savstarpēji aizvietojamas. |
| `--model {translation, rigid, affine, homography}` | `affine` | Pārveido modeli, lai tas atbilstu. |
| `--frames N` | `1` | Sinhronizē kadru momentuzņēmumus līdz vidējo. |
| `--reference SN` | galvenajai | Atskaites kameras sērijas numurs; visi pārējie elementi tiek deformēti, lai atbilstu tai. |
| `--max-features N` | `5000` | ORB iezīmju skaita ierobežojums. |
| `--ratio-threshold F` | `0.75` | Lowe’attiecības tests. |
| `--ransac-threshold-px F` | `3.0` | RANSAC iekšējo punktu slieksnis. |
| `--min-matches N` | `15` | **Kvalitātes slieksnis** — noraidīt risinājumu, ja atbilstošo punktu skaits ir mazāks par šo vērtību. |
| `--max-reproj-err-px F` | `4.0` | **Kvalitātes slieksnis** — noraidīt risinājumu, ja RMS reprojekcijas kļūda pārsniedz šo vērtību. |
| `--checkerboard RxC` | — | Plates ģeometrija `--method checkerboard`, piemēram, `9x6`. |
| `--name PROFILE` | tukšs | Profila nosaukums, kas iekļauts saglabātajā JSON failā. **Ne masīva nosaukums** — tas ir pozicionālais `NAME`. |

Šie divi kvalitātes kritēriji ir iemesls, kāpēc kalibrēšana var veiksmīgi atrisināt problēmu, bet tomēr
atteikties saglabāt: profils, kas neatbilst vienam no tiem, klusi reģistrētu nepareizi katru
turpmāko uzņēmumu, tāpēc tas tiek noraidīts, nevis saglabāts.

### Pārvietots projekts (`--relink`)

Projekta `project.json` katram avota failam — katram attēlam un gaismas sensora
ierakstam — piešķir nosaukumu pēc tā pilnā ceļa. Pārvietojiet projekta mapi vai visu projektu mapi
uz citu disku, un šie ceļi joprojām norādīs uz veco atrašanās vietu.

Atverot projektu, tas pats izlabo to, ko var: failu, kas atradās projekta
mapē, meklē mapē, kurā projekts atrodas tagad, un katrs tur atrastais ceļš
tiek pārvirzīts un saglabāts (`project.json` vispirms tiek dublēts kā
`project.json.before-relink-<date>-<time>`). Tas notiek katrā atvēršanas reizē — gan lietotnes,
`chloros-cli project open`, `chloros-cli process` iekļaušanas esošajā projektā, kā arī SDK un `chloros-mcp`. Ceļš tiek pārvirzīts tikai uz failu, kas tur atrodas.

Failus, kas atradās ārpus projekta mapes — piemēram, „Hub” sesijas vai „Add Folder” importēšanas rezultātā —
šādā veidā nevar atrast. Norādiet mapes nosaukumu, kurā tie atrodas pašlaik:

```bash
chloros-cli project open "N:/Chloros Projects/Field_A" --relink "E:/backup/Hub Projects"
```

`--relink` norāda uz mapi, kurā faili tika pārvietoti, vai jebkuru augšējā līmeņa mapi: katra trūkstošā
faila mape tiek meklēta zem tās, vadoties pēc faila nosaukuma beigu daļas
(`Hub Projects/<session>/raw/<serial>`, tad `<session>/raw/<serial>`, ...), un katras
mapes faili tiek ņemti no tās vietas, kurā tiek atrasts visvairāk failu. `project open` parāda,
kas tika pārvietots, cik avota failu tika atrasti un mapes, kurās atradās pārējie
faili. Daži joprojām trūkst: brīdinājums, iziet no `0` — projekts atveras ar pārējiem. Neviens
nav atrasts: kļūda, nosaucot šīs mapes un `--relink`, iziet no `1`; arī aizmugurējā sistēma
atsakās atvērt šādu projektu `process` vai `project devices --reconnect` (HTTP 409 `PROJECT_SOURCES_MISSING`).

### Piemēri

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Its hub captures moved to another drive: point it at them
chloros-cli project open "/home/user/Chloros Projects/Field_A" --relink "/media/backup/Hub Projects"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### Receptes DSL

`project run RECIPE.yaml` pieņem YAML vai JSON failu, kurā aprakstīta darbību secība:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        auto_exposure: "Off"
        gain_db: 6.0
        auto_gain: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Atbalstītās darbības: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. `apply` izmanto atslēgas `cameras.json`, `settings` — `exposure_us`, `auto_exposure`, `gain_db`, `auto_gain`, `target_brightness`, … (vecākie nosaukumi `exposure_time`, `exposure_auto`, `gain`, `gain_auto` tiek pieņemti kā sinonīmi). Šādā veidā iestatīta ekspozīcija, pastiprinājums vai mērķa vērtība tiek saglabāta kā jūsu izvēle: Chloros automātiskā ekspozīcijas kontrole (ieskaitot DAQ mērīto ekspozīciju) to nemaina. Manuālā ekspozīcija atstāj pastiprinājumu tādu, kāds tas bija: automātiskajā režīmā kamera turpina regulēt pastiprinājumu (visā tā diapazonā) virzienā uz mērķa spilgtumu, līdz jūs iestatāt `gain_db` vai `auto_gain: "Off"`. Vēlāka komanda „`auto_exposure: "Continuous"`” nodod ekspozīciju atpakaļ automātiskajam režīmam, bet komanda „`target_brightness_auto: true`” nodod fiksēto mērķi atpakaļ Chloros automātiskajam mērķim. Darbība `burst` pieņem `name` (obligāts), `count` (noklusējums 5), `interval` (sekundes, noklusējums 0), `output`, `format`, un `settings` (tāds pats kameras iestatījumu veids kā `apply`); masīva sērijas izmanto to pašu nesen sinhronizētās grupas uzraugu kā `project burst`.

A `capture`, `burst` vai `stream` posma `output` noklusējuma vērtība ir `output`. Relatīvais `output` — kas ir iekļauts pēc noklusējuma — tiek noteikts, ņemot vērā mapi, no kuras jūs palaidāt `chloros-cli project run`, nevis receptes faila mapi vai projekta mapi (versijā 1.2.0 tas atradās aizmugurējās sistēmas instalācijas mapē).

Palaidiet to:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

**Katra izpilde vispirms tiek pārbaudīta, salīdzinot ar projektu.**Katrai darbībai kā atslēgai jābūt tieši vienam no iepriekš minētajiem darbības vārdiem — `- capture: {name: FrontLeft}`, nevis `- action: capture` vai `- type: capture` — ja `name` projektā ir `cameras.json` / `sensors.json`, tad kamerai vai masīvam ir `apply` / `capture` / `burst` / `stream` un gaismas sensoru `sensor`; `stream` ir nepieciešams, lai `count` > 0, un `wait` ir nepieciešams ilgums. Recepte ar jebkuru šādu kļūdu**neizpilda** nekādu darbību (`[recipe] error: …`, `[recipe] not run: …`, iziešana `1`), pat ja ir `stop_on_error: false`. Taustiņi, kurus solis neizmanto, ir `[recipe] warning:`, tāpat kā katrs masīvs vai gaismas sensors, ko recepte vada, bet kas pašlaik nav pieslēgts. Recepte nekad nepieslēdz ierīces: vispirms izpildiet `chloros-cli project connect PATH`. `--dry-run` izdrukā recepti, pēc tam tās pašas kļūdas un brīdinājumus, un `[recipe] valid: …` / `NOT valid: …`, neko neizpilda un iziet no `1`, ja receptē ir kļūda.

---

## Vides mainīgie

| Mainīgais | Efekts |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Pārraksta aizmugures URLi (noklusējums `http://127.0.0.1:5000`) — **tiek ņemts vērā tikai `lattice`, `project` un `daq pool-*` komandu grupās** (`lattice network-analysis` izmanto `--backend-url`). Galvenās komandas (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) izmanto `http://127.0.0.1:<port>` un ignorē šo mainīgo (IPv4 literāls apiet Windows `localhost`→`::1` ~2 s sodu par katru pieprasījumu), tādējādi tie vienmēr vēršas uz lokālo datoru. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | Iestatīts aizmugures vides konfigurācijā. `1` samazina atteikumu pārslodzes gadījumā — masīva savienojums ar `pin_resolution` vai [pagaidu daudzkameru komanda](#multi-camera-transient-sessions) (`error_code` `ARRAY_OVERSUBSCRIBED`) — līdz skaļam brīdinājumam, pieņemot GVSP pakešu zudumu. Tikai testēšanai — skatiet [Masīva fps un pārslogojuma modelis](#array-fps--burst-model). |
| `CHLOROS_ARRAY_PACKED12` | Iestatiet aizmugures vides konfigurācijā: `on` vai `off` katram masīvam iestata noklusējuma [saspiestu 12 bitu transportu](#packed-12-bit-transport---packed) (noklusējums `auto`). Tomēr eksplicīts `--packed` joprojām ir prioritārs. |
| `CHLOROS_DISABLE_UPDATE_CHECK` | `1` (vai `true` / `yes`) atspējo Linux CLI [automātisko atjauninājumu pārbaudi](#chloros-cli-update) palaišanas brīdī. |
| `CHLOROS_CLI_MODE` | To iestata pati CLI; norāda backendam, ka jāieslēdz paralēlā apstrāde. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` izlaiž GVSP rezerves pārbaudi (tikai ICMP rezultāti). **Tas atslēdz jumbo, nevis vienkārši samazina žurnāla ierakstu skaitu** — kamera atbild uz DF pingiem tikai līdz 1500 katrā maršrutā, tādēļ šī pārbaude ir vienīgais veids, kā var atklāt jumbo. Ietaupa apmēram 1 s uz katru kameru katrā savienojumā; izmaksā apmēram 1,45× vadu maksimālo caurlaidspēju, ja tīkls *varētu* pārraidīt jumbo. SDK brīdina, kad to iestatāt. |
| `CHLOROS_GVSP_SESSION_REUSE` | Iestatiet aizmugurējās sistēmas vidē: `0` mēra pakešu izmēru katrā savienojumā, nevis vienu reizi katrai kamerai katrā aizmugurējās sistēmas sesijā. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | Fiksē GVSP paketes izmēru uz N baitiem; pilnībā izlaiž pārbaudi. Ieteicams izmantot iestatījumu katrai komandai (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`), nevis to iestatīt pastāvīgi: fiksēts izmērs vairs nepielāgojas tīklam, kas atrodas tā priekšā, un, fiksējot 9000 ceļā, kas nespēj pārraidīt jumbo paketes, **katra** uztveršana beidzas ar laika limitu, izmantojot `SC_ERR_TIMEOUT -1011`. |
| `CHLOROS_TRANSIENT_AE_SETTLE_S` | Iestatīts aizmugurējās sistēmas vidē: maksimālais laiks sekundēs, cik ilgi [pagaidu daudzkameru komanda](#multi-camera-transient-sessions) vai kameras `lattice capture`, kuru nekas neaiztur, var pavadīt, iestatot katras kameras ekspozīciju no ainas pirms tā saglabā kadru (noklusējuma `5`). `0` to atslēdz: katras kameras automātiskā ekspozīcija darbojas no pirmā kadra, sākot no tās ekspozīcijas robežas. |
| `CHLOROS_LOG_DIR` | Iestatāms aizmugurējās sistēmas vidē: mape, kurā tiek saglabāts detalizētais žurnāls. Noklusējums: `%LOCALAPPDATA%\Chloros\logs` operētājsistēmā Windows, `~/.cache/chloros/logs` operētājsistēmā Linux (`$XDG_CACHE_HOME/chloros/logs`, ja tas ir iestatīts). .deb faila saknes pakalpojums iestata `/var/log/chloros`, ko var lasīt `adm` grupa — skatiet [Aizmugurējās daļas palaide kā pakalpojums](#running-the-backend-as-a-service-headless). |
| `TMPDIR` (Linux) | Vieta, kur tiek saglabāti pagaidu faili. CLI automātiski izmanto `/mnt/ssd/tmp`, ja tāds ir pieejams. |

---

## Izvades kodi

| Kods | Nozīme |
| --- | --- |
| `0` | Veiksmīga izpilde. |
| `1` | Vispārīga kļūda (vairums apakškommandu kļūdu). Ierakstīšanas komanda iziet arī ar kodu `1`, ja kāds no failiem, kurus bija jāieraksta, netika ierakstīts, pat ja pārējie tika saglabāti, un ar kodu `array-burst` / `array-record` iziet no programmas, kad ierakstītājs ziņo par kļūdu — skatiet [Faili, kurus neizdevās ierakstīt](#files-that-fail-to-write). `process` arī iziet no programmas `1`, ja nav iespējams pārbaudīt kāda izpildes cikla eksportētos datus vai ja ir pazaudēts attēls, lai gan pārējie produkti ir ierakstīti. |
| `2` | Argumenta kļūda (nezināms vai trūkstošs karodziņš; arī `time-sync set-priority` bez prioritātes). |
| `130` | Pārtraukts ar Ctrl+C: jebkura komanda, ko tas pārtrauc — `lattice`, `project`, `daq` un `process` (`process` pēc tam, kad aizmugurējā programma ir pārtraukusi darbību, skatīt [`process` piezīmes](#notes)). Komanda, kuras parastā pārtraukšanas taustiņa kombinācija ir Ctrl+C — `lattice array-capture --continuous` / `--interval`, `lattice viewer`, `lattice continuous`, `multi-stream`, `align-stream`, `array-record`, `array-burst` un `project stream` ar `-n 0` — iziet no programmas atkarībā no tā, ko tā izdarīja: `0`, kad viss tika ierakstīts, `1`, ja kaut kas neizdevās. |
| `143` / `129` | Pārtraukts ar SIGTERM / SIGHUP (128 + signāls), pēc tam, kad tika izpildīta komandas tīrīšana: darbojošais uzdevums tika apturēts un tā kameras atgriezta sākotnējā stāvoklī, apstrādes cikls tika apturēts fonā. `lattice continuous` vai cita ierakstīšana, kuras normāla apturēšana notiek ar Ctrl+C, iziet arī no `143`, nevis no `0`. |

`lattice network-analysis` izbeidz `1`, ja tiek pieņemti lēmumi par `needs_force_slip` un `error`, un `selftest` izbeidz `1`, ja vien netiek izpildītas visas septiņas pārbaudes.

---

## Problēmu novēršanas norādes

- **„Nepieciešama pieteikšanās”** → Vienreiz palaidiet `chloros-cli login EMAIL PASSWORD` šajā datorā.
- **"Projekta mape šim datoram ir pārāk dziļa"** → Šajā datorā ir atspējoti Windows garie ceļi (Windows noklusējums), tādēļ neviens ceļš nedrīkst pārsniegt 260 rakstzīmes, un produkti atrodas līdz aptuveni 130 rakstzīmēm zem projekta mapes. Nekas netika apstrādāts. Ziņojumā norādīta garākā projekta mape, kas atbilst prasībām: izmantojiet īsāku (`-o` vai `chloros-cli set-project-folder`) vai ieslēdziet Windows garos ceļus un pārstartējiet Chloros. Windows instalētājs to piedāvā kā „Iespējot garos Windows ceļus”, kas pēc noklusējuma ir atzīmēts; citādi izmantojiet grupas politiku: *Datoru konfigurācija > Administratīvie šabloni > Sistēma > Failu sistēma > Iespējot garos Win32 ceļus*.
- **„backend unreachable”** → Palaižiet Chloros darbvirsmas lietotni (Windows) vai paši palaidiet backend: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` Windows vidē (mapes `backend` jūsu instalācijas mapē; tā neatrodas `PATH`), `chloros-backend` uz Linux. Vai arī pārbaudiet `CHLOROS_BACKEND_URL`, ja izmantojat attālinātu piekļuvi.
- **`lattice` komandas neizdodas ar kļūdu „LATTICE kameru draiveri nav atrasti”** → Aizmugurējam procesam neizdevās ielādēt LATTICE kameru izpildes vidi, ar kuru tas ir instalēts. Katra `lattice` komanda tiek izpildīta aizmugurē; pati CLI neietver kameras draiverus. Pāriinstalējiet Chloros: Windows instalētājs un .deb faila instalētājs instalē darbības vidi blakus aizmugurei.
- **„Array connect“ / „Array Settings“ rāda „FRAMES WILL DROP“ vai „Reduce ROI to enable“** → Uzņēmējdatora tīkla kartes (NIC) uztveršanas gredzens ir pārāk mazs (parasti pēc NIC draivera atjaunināšanas tas tiek atiestatīts uz 32). Skatīt [Host NIC Setup & Tuning](#host-nic-setup--tuning-lattice-arrays) — iestatiet `ReceiveBufferLen=256`, `PendingReceives=64`.
- **Dators „iekārtojas” pārstartēšanas/izslēgšanas laikā, pēc tam WMI `Invalid class` / tīkla karte neaktivizējas / trūkst USB disku** → Novecojis USB 10GbE adaptera draiveris izraisa kļūdu `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Atjauniniet adaptera draiveri — skatiet [Host NIC Setup & Tuning](#host-nic-setup--tuning-lattice-arrays).
- **Linux: ieraksti pieder lietotājam root, tāpēc tos nevar dzēst, un backend žurnāls neatrodas jūsu `~/.cache`** → Backend darbojas kā root caur `chloros-backend.service`. Pārejiet uz `chloros-backend@<user>.service`, kurā tas darbojas kā jūs — skatiet [Backend darbība kā pakalpojums](#running-the-backend-as-a-service-headless). Līdz tam brīdim root pakalpojuma žurnāls atrodas `/var/log/chloros` (to var lasīt `adm` grupa).
- **Linux: aizmugurējās sistēmas vienība ir `failed` ar ziņojumu „Jau darbojas cita instance“ (iziešanas statuss 98)** → Cita aizmugurējā sistēma jau aizņem šo portu: vienlaikus darbojas tikai viena. Ja ir ieslēgtas divas aizmugures vienības, paturiet vienu: `sudo systemctl disable --now <unit>` — tai, kuru neizmantojat — skatiet [Aizmugures vienības darbināšana kā pakalpojums](#running-the-backend-as-a-service-headless). Ja ir ieslēgta tikai šī vienība, to aizņem backend, kas palaists ārpus systemd (ar `chloros-backend`, `chloros-cli` vai SDK skriptu): apturiet šo backend, pēc tam izpildiet `sudo systemctl start <unit>`.
- **Jetson swap brīdinājums** → Pievienojiet failu balstītu swap; CLI izvada precīzas `fallocate` / `swapon` komandas.
- **"backend nav sasniedzams" no komandas `lattice`, `project` vai `daq`** → neviens backend nedarbojas: šīs komandas nekad to nepalaiž. Palaidiet komandu `chloros-cli backend status` un skatiet sadaļu [Backend palaišana](#start-the-backend).
- **"`daq net ...` nav pieejams Chloros"** (Linux un Jetson) → Daži no DAQ-E pašas iestatījumiem ir rezervēti MAPIR pakalpojumam. Tīkla iestatījumi, pārstartēšana un programmaparatūras atjauninājumi ir `pool-set-network`, `pool-reboot` un `pool-update-firmware`; par pārējo sazinieties ar MAPIR atbalsta dienestu.
- **"DAQ tiešajām aparatūras komandām ir nepieciešams pilnais daq pakotnes komplekts"** (Windows) → Izmantojiet `pool-*`, kas vadītu to pašu sensoru caur backend, vai `chloros_sdk.connect_daq_sensor()` no Python.

---

## Skatīt arī

- [Python SDK atsauces materiāls](sdk-reference.md) — katras CLI komandas programmatiskais ekvivalents.
- [DAQ gaismas sensori](https://mapir.gitbook.io/chloros/daq-light-sensors) Chloros rokasgrāmatā — sensori, to diapazoni un kalibrētie diapazoni, ierakstīšana, DAQ-E tīkla savienojumi.
- [LATTICE kameras](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) Chloros rokasgrāmatā — pieslēgšana, kameru iestatījumi, uzņemšanas režīmi, masīvi.
- Tiešsaistes dokumentācija: `https://mapir.gitbook.io/chloros/cli`
