# CLI : Komandrinda

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI** nodrošina jaudīgu komandrindas piekļuvi attēlu apstrādes dzinējam Chloros, ļaujot automatizēt, veidot skriptus un izmantot bezgalvas režīmu jūsu attēlu apstrādes darba plūsmās.

### Galvenās funkcijas

* 🚀 **Automatizācija** — vairāku datu kopu skriptu pakotņu apstrāde
* 🔗 **Integrācija** — iekļaušana esošajos darba plūsmās un procesa posmos
* 💻 **Darbība bez grafiskās saskarnes** — darbināšana bez GUI
* 🌍 **Daudzvalodība** — atbalsts 38 valodām
* ⚡ **Paralēlā apstrāde** — [Dinamiskā aprēķinu pielāgošana](processing-architecture/dynamic-compute-adaptation.md) automātiski optimizējas jūsu aparatūrai

### Prasības

| Prasība          | Sīkāka informācija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Operētājsistēma** | Windows 10/11 (64-bit), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Licence**          | Chloros+ ([nepieciešams maksas plāns](https://cloud.mapir.camera/pricing)) |
| **Atmiņa**           | Vismaz 8 GB RAM (ieteicams 16 GB)                                  |
| **Internets**         | Nepieciešams licences aktivizēšanai                                     |
| **Diska vieta**       | Atšķiras atkarībā no projekta lieluma                                              |

{% hint style="warning" %}
**Licences prasības**: CLI prasa maksas Chloros+ abonementu. Standarta (bezmaksas) plāniem nav piekļuves CLI. Lai veiktu uzlabojumu, apmeklējiet [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).
{% endhint %}

## Ātrs sākums

### Instalēšana

#### Windows

CLI automātiski ir iekļauts Chloros instalētājā:

1. Lejupielādējiet un palaidiet **Chloros Installer.exe**

2. Pabeidziet instalācijas vedni
3. CLI instalēts: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style="success" %}
Instalētājs automātiski pievieno `chloros-cli` jūsu sistēmas PATH. Pēc instalācijas pārstartējiet termināli.
{% endhint %}

#### Linux

Instalējiet `.deb` paketi jūsu arhitektūrai:

```bash
# Linux amd64
sudo dpkg -i chloros-amd64.deb

# Linux arm64 (NVIDIA Jetson, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Sīkāku informāciju par Linux konfigurēšanu skatiet sadaļā [Linux instalēšana](linux/linux-installation.md).

### Pirmā konfigurēšana

Pirms CLI lietošanas aktivizējiet savu Chloros+ licenci:

**Windows:**

```powershell
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process ~/images/dataset001
```

### Pamata lietošana

Apstrādājiet mapi ar noklusējuma iestatījumiem:

**Windows:**

```powershell
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
chloros-cli process ~/images/dataset001
```

***

## Komandu atsauces

### Vispārīgā sintakse

```
chloros-cli [global-options] <command> [command-options]
```

***

## Komandas

### `process` - Apstrādāt attēlus

Apstrādā attēlus mapē ar kalibrēšanu.

**Sintakse:**

```bash
chloros-cli process <input-folder> [options]
```

**Piemēri:**

```bash
# Windows
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance

# Linux
chloros-cli process ~/datasets/survey_001 --vignette --reflectance
```

#### Komandas apstrādes opcijas

| Opcija                | Tips    | Noklusējums        | Apraksts                                                                            |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>`      | Celiņš    | _Obligāts_     | Mapes, kas satur RAW/JPG multispektrālos attēlus                                         |
| `-o, --output`        | Celiņš    | Tāds pats kā ievade  | Izvades mape apstrādātajiem attēliem                                                     |
| `-n, --project-name`  | Virkne  | Automātiski ģenerēts | Pielāgots projekta nosaukums                                                                    |
| `--vignette`          | Karodziņš    | Iespējots        | Iespējot vinjetes korekciju                                                             |
| `--no-vignette`       | Karodziņš    | -              | Atspējot vinjetes korekciju                                                            |
| `--reflectance`       | Karodziņš    | Iespējots        | Iespējot atstarojuma kalibrēšanu                                                         |
| `--no-reflectance`    | Karodziņš    | -              | Atspējot atstarojuma kalibrēšanu                                                        |
| `--ppk`               | Karodziņš    | Atspējots       | Piemērot PPK korekcijas no .daq gaismas sensora datiem                                      |
| `--format`            | Izvēle  | TIFF (16 bitu)  | Izvades formāts: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Vesels skaitlis | Automātiski           | Minimālais mērķa izmērs pikseļos kalibrēšanas paneļa noteikšanai                          |
| `--target-clustering` | Vesels skaitlis | Automātiski           | Mērķa klasterizācijas slieksnis (0–100)                                                    |
| `--debayer`           | Izvēle  | `standard`     | Debayer metode: `standard` vai `texture-aware` (tikai Chloros+)                          |
| `--target`, `--targets` | Karodziņš  | Atspējots       | Meklēt kalibrēšanas mērķus tikai apakšmapē „target” vai „targets” (paātrina apstrādi) |
| `--indices`           | Saraksts    | Nav           | Aprēķināmie veģetācijas indeksi (piem., `--indices NDVI NDRE GNDVI`)                    |
| `--exposure-pin-1`    | Virkne  | Nav           | Fiksē ekspozīciju kameras modelim (1. kontakts)                                                 |
| `--exposure-pin-2`    | Virkne  | Nav           | Ekspozīcijas fiksēšana kameras modelim (2. kontakts)                                                 |
| `--recal-interval`    | Vesels skaitlis | Automātiski           | Pārkalibrēšanas intervāls sekundēs                                                      |
| `--timezone-offset`   | Vesels skaitlis | 0              | Laika zonas nobīde stundās                                                               |

***

### `login` - Konta autentifikācija

Piesakieties ar savām Chloros+ autentifikācijas datiem, lai aktivizētu CLI apstrādi.

**Sintakse:**

```bash
chloros-cli login <email> <password>
```

**Piemērs:**

```bash
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Īpašie simboli**: Lietojiet vienkāršās pēdiņas ap parolēm, kas satur simbolus, piemēram, `$`, `!` vai atstarpes.
{% endhint %}

**Rezultāts:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Dzēst autentifikācijas datus

Dzēst saglabātos autentifikācijas datus un iziet no sava konta.

**Sintakse:**

```bash
chloros-cli logout
```

**Piemērs:**

```bash
chloros-cli logout
```

**Rezultāts:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style="info" %}
**SDK lietotāji**: Python SDK nodrošina arī programmatisku `logout()` metodi, lai dzēstu autentifikācijas datus Python skriptos. Sīkāku informāciju skatiet [Python SDK dokumentācijā](api-python-sdk.md#logout).
{% endhint %}

***

### `status` - Licences statusa pārbaude

Parāda pašreizējo licences un autentifikācijas statusu.

**Sintakse:**

```bash
chloros-cli status
```

**Piemērs:**

```bash
chloros-cli status
```

**Rezultāts:**

```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```

***

### `export-status` — eksporta gaitu pārbaude

Uzrauga 4. pavediena eksporta gaitu apstrādes laikā vai pēc tās.

**Sintakse:**

```bash
chloros-cli export-status
```

**Piemērs:**

```bash
chloros-cli export-status
```

**Lietošanas gadījums:** Izsauciet šo komandu apstrādes laikā, lai pārbaudītu eksporta gaitu.***

### `language` - Pārvaldīt saskarnes valodu

Skatīt vai mainīt CLI saskarnes valodu.

**Sintakse:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**Piemēri:**

```bash
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### Atbalstītās valodas (kopā 38)

| Kods    | Valoda              | Nativais nosaukums      |
| ------- | --------------------- | ---------------- |
| `en`    | Angļu               | English          |
| `es`    | Spāņu               | Español          |
| `pt`    | Portugāļu            | Português        |
| `fr`    | Franču                | Français         |
| `de`    | Vācu                | Deutsch          |
| `it`    | Itāļu               | Italiano         |
| `ja`    | Japāņu              | 日本語              |
| `ko`    | Korejiešu                | 한국어              |
| `zh`    | Ķīniešu (vienkāršotā)  | 简体中文             |
| `zh-TW` | Ķīniešu (tradicionālā) | 繁體中文             |
| `ru`    | Krievu               | Русский          |
| `nl`    | Holandiešu                | Nederlands       |
| `ar`    | Arābu                | العربية          |
| `pl`    | Poļu                | Polski           |
| `tr`    | Turku valoda               | Türkçe           |
| `hi`    | Hindi                 | हिंदी            |
| `id`    | Indonēziešu valoda            | Bahasa Indonesia |
| `vi`    | Vjetnamiešu            | Tiếng Việt       |
| `th`    | Taizemiešu                  | ไทย              |
| `sv`    | Zviedru               | Svenska          |
| `da`    | Dāņu                | Dansk            |
| `no`    | Norvēģu             | Norsk            |
| `fi`    | Somu               | Suomi            |
| `el`    | Grieķu                 | Ελληνικά         |
| `cs`    | Čehu                | Čeština          |
| `hu`    | Ungāru             | Magyar           |
| `ro`    | Rumāņu              | Română           |
| `uk`    | Ukraiņu             | Українська       |
| `pt-BR` | Brazīlijas portugāļu valoda  | Português Brasileiro |
| `zh-HK` | Kantoniešu valoda             | 粵語             |
| `ms`    | Malajiešu valoda                 | Bahasa Melayu    |
| `sk`    | Slovāku                | Slovenčina       |
| `bg`    | Bulgāru             | Български        |
| `hr`    | Horvātu              | Hrvatski         |
| `lt`    | Lietuviešu            | Lietuvių         |
| `lv`    | Latviešu               | Latviešu         |
| `et`    | Igauniešu              | Eesti            |
| `sl`    | Slovēņu             | Slovenščina      |

{% hint style="success" %}
**Automātiska saglabāšana**: Jūsu valodas iestatījumi tiek saglabāti `~/.chloros/cli_language.json` un saglabājas visās sesijās.
{% endhint %}

***

### `set-project-folder` - Noklusējuma projekta mapes iestatīšana

Mainiet noklusējuma projekta mapes atrašanās vietu (kopīga ar GUI Windows).

**Sintakse:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Piemēri:**

```bash
# Windows
chloros-cli set-project-folder "C:\Projects\2025"

# Linux
chloros-cli set-project-folder ~/projects/2025
```

***

### `get-project-folder` - Parādīt projekta mapi

Parāda pašreizējo noklusējuma projekta mapes atrašanās vietu.

**Sintakse:**

```bash
chloros-cli get-project-folder
```

**Piemērs:**

```bash
chloros-cli get-project-folder
```

**Rezultāts:**

```

# Windows
ℹ Current project folder: C:\Projects\2025

# Linux
ℹ Current project folder: /home/user/.local/share/chloros/projects
```

***

### `reset-project-folder` - Atjaunot noklusējuma iestatījumus

Atjaunot projekta mapes noklusējuma atrašanās vietu.

**Sintakse:**

```bash
chloros-cli reset-project-folder
```

***

### `selftest` - Sistēmas diagnostikas palaide

Palaidiet 7 diagnostikas pārbaudes, lai pārbaudītu sistēmas konfigurāciju.

**Sintakse:**

```bash
chloros-cli selftest
```

**Veiktās diagnostikas:**

1. Versijas pārbaude
2. Porta pieejamība (5000)
3. Backend palaišana
4. API savienojamības tests
5. Sistēmas informācija un GPU noteikšana
6. Denoiser modeļu pārbaude
7. CUDA pieejamības pārbaude

{% hint style="info" %}
**Noderīgi problēmu novēršanai**: Pēc instalēšanas palaidiet `selftest`, lai pārbaudītu, vai sistēma ir konfigurēta pareizi, īpaši Linux/Jetson, kur var būt nepieciešama GPU un CUDA konfigurācijas pārbaude.
{% endhint %}

***

### `update` - Pārbaudiet, vai ir pieejami atjauninājumi (tikai Linux)

Pārbaudiet un instalējiet CLI atjauninājumus Linux sistēmās.

**Sintakse:**

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

| Opcija    | Apraksts                        |
| --------- | ---------------------------------- |
| `--check` | Vienīgi pārbaudīt atjauninājumus, neinstalēt |

{% hint style="info" %}
Šī komanda ir pieejama tikai Linux. Windows sistēmās atjauninājumi tiek piegādāti ar instalētāja starpniecību.
{% endhint %}

***

## Vispārējās opcijas

Šīs opcijas attiecas uz visām komandām:

| Opcija            | Tips    | Noklusējums       | Apraksts                                      |
| ----------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe`   | Celiņš    | Automātiski noteikts | Celiņš uz aizmugures izpildāmo failu                       |
| `--port`          | Vesels skaitlis | 5000          | Aizmugures API porta numurs                          |
| `--restart`       | Karodziņš    | -             | Piespiedu atkārtota aizmugurējās programmas palaišana (izbeidz esošos procesus) |
| `--version`       | Karodziņš    | -             | Parādīt versijas informāciju un iziet                |
| `--help`          | Karodziņš    | -             | Parādīt palīdzības informāciju un iziet                   |

{% hint style="info" %}
**Aizmugures automātiskā noteikšana**: `--backend-exe` ceļš tiek automātiski noteikts atbilstoši platformai:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (manuāli)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

**Piemērs ar globālajām opcijām:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

**Linux:**

```bash
chloros-cli --port 5001 process ~/datasets/survey_001
```

***

## Apstrādes iestatījumu rokasgrāmata

### Paralēlā apstrāde un dinamiskā aprēķinu pielāgošana

Chloros 1.1.0 ietver [dinamisko aprēķinu pielāgošanu](processing-architecture/dynamic-compute-adaptation.md) — apstrādes dzinējs **automātiski atpazīst jūsu aparatūru** un izvēlas optimālo stratēģiju:

| Platforma | Stratēģija | Darbinieki | Pieslēgums | Piezīmes |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | Atmiņai efektīva, serializēta |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` | Vienlaicīga GPU apstrāde |
| **Galddators ar 8 GB GPU** | `GPU_SINGLE` | 3 | `tiled_gpu` | Laba galddatora veiktspēja |
| **Datoru ar 12 GB+ GPU** | `GPU_PARALLEL` | 3–4 | `fused_gpu` | Optimāla datora veiktspēja |
| **Tikai CPU sistēma** | `CPU_PARALLEL` | kodoli - 1 | `cpu_fallback` | GPU nav nepieciešams |

{% hint style="success" %}
**Nav nepieciešama manuāla konfigurācija!** Chloros automātiski atpazīst jūsu CPU, GPU, RAM un (uz Jetson) termiskos sensorus, pēc tam automātiski konfigurē optimālo apstrādes plūsmu.
{% endhint %}

### Debayer metodes

| Metode | CLI Karodziņš | Kvalitāte | Ātrums | Licence |
| --- | --- | --- | --- | --- |
| **Standarta (ātra, vidēja kvalitāte)** | `--debayer standard` | Laba | Ātra | Bezmaksas / Chloros+ |
| **Tekstūras apzināta (lēna, augstākā kvalitāte)** | `--debayer texture-aware` | Augstākā | Lēna | Tikai Chloros+ |

Noklusējuma debayer metode ir **Standarta**.**Tekstūras apzināšanās** metode izmanto AI/ML trokšņu samazināšanas modeli, lai nodrošinātu augstāko izvades kvalitāti, taču tai ir nepieciešama Chloros+ licence un NVIDIA GPU.

```bash
# Use Texture Aware debayer (Chloros+ only)
chloros-cli process ~/datasets/field_a --debayer texture-aware
```

### Vignette korekcija

**Funkcija:** Korekcija attēla malu izgaismojuma samazināšanās (tumšāki stūri, kas bieži sastopami kameru attēlos).

* **Pēc noklusējuma ieslēgts** - Lielākajai daļai lietotāju šo funkciju vajadzētu atstāt ieslēgtu
* Lai atslēgtu, izmantojiet `--no-vignette`

{% hint style="success" %}
**Ieteikums**: Vienmēr ieslēdziet vinjetes korekciju, lai nodrošinātu vienmērīgu spilgtumu visā kadrā.
{% endhint %}

### Atstarošanas kalibrēšana

Pārvērš neapstrādātos sensora rādījumus standartizētos atstarošanas procentos, izmantojot kalibrēšanas paneļus.

* **Pēc noklusējuma ieslēgts** — nepieciešams veģetācijas analīzei
* Attēlos nepieciešami kalibrēšanas mērķa paneļi
* Lai atslēgtu, izmantojiet `--no-reflectance`

{% hint style="info" %}
**Prasības**: Lai nodrošinātu precīzu atstarojuma pārrēķinu, pārliecinieties, ka kalibrēšanas paneļi attēlos ir pareizi eksponēti un redzami.
{% endhint %}

### PPK korekcijas

**Funkcija:** Piemēro pēcapstrādes kinemātiskās korekcijas, izmantojot DAQ-A-SD žurnāla datus, lai uzlabotu GPS precizitāti.

* **Pēc noklusējuma atspējots**
* Lai ieslēgtu, izmantojiet `--ppk`
* Nepieciešami .daq faili projekta mapē no MAPIR DAQ-A-SD gaismas sensora.

### Izvades formāti

<table><thead><tr><th width="197">Formāts</th><th width="130.20001220703125">Bitu dziļums</th><th width="116.5999755859375">Faila izmērs</th><th>Vispiemērotākais</th></tr></thead><tbody><tr><td><strong>TIFF (16 bitu)</strong> ⭐</td><td>16 bitu vesels skaitlis</td><td>Liels</td><td>ĢIS analīze, fotogrammetrija (ieteicams)</td></tr><tr><td><strong>TIFF (32 bitu, procenti)</strong></td><td>32 bitu peldošā komata</td><td>Ļoti liels</td><td>Zinātniskā analīze, pētniecība</td></tr><tr><td><strong>PNG (8 bitu)</strong></td><td>8 bitu vesels skaitlis</td><td>Vidējs</td><td>Vizuāla pārbaude, koplietošana tīmeklī</td></tr><tr><td><strong>JPG (8 bitu)</strong></td><td>8 bitu vesels skaitlis</td><td>Maz</td><td>Ātrā priekšskatīšana, saspiesta izvade</td></tr></tbody></table>***

## Automatizācija un skripti

### PowerShell partiju apstrāde (Windows)

Automātiski apstrādājiet vairākas datu kopu mapes, izmantojot Windows:

```powershell
# process_all_datasets.ps1

$datasets = Get-ChildItem "C:\Datasets\2025" -Directory

foreach ($dataset in $datasets) {
    Write-Host "Processing $($dataset.Name)..." -ForegroundColor Cyan
    
    chloros-cli process $dataset.FullName `
        --vignette `
        --reflectance
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✓ $($dataset.Name) complete" -ForegroundColor Green
    } else {
        Write-Host "✗ $($dataset.Name) failed" -ForegroundColor Red
    }
}

Write-Host "All datasets processed!" -ForegroundColor Green
```

### Windows partijas skripts (Windows)

Vienkārša cilpa partijas apstrādei Windows:

```batch
@echo off
echo Starting batch processing...

for /d %%i in (C:\Datasets\2025\*) do (
    echo.
    echo ========================================
    echo Processing: %%i
    echo ========================================
    chloros-cli process "%%i"
    
    if %ERRORLEVEL% EQU 0 (
        echo SUCCESS: %%i processed
    ) else (
        echo ERROR: %%i failed
    )
)

echo.
echo All datasets processed!
pause
```

### Bash partiju apstrāde (Linux)

Apstrādājiet vairākas datu kopu mapes uz Linux:

```bash
#!/bin/bash
# process_all_datasets.sh

for dataset in ~/datasets/2026/*/; do
    name=$(basename "$dataset")
    echo "Processing $name..."

    chloros-cli process "$dataset" \
        --vignette \
        --reflectance

    if [ $? -eq 0 ]; then
        echo "✓ $name complete"
    else
        echo "✗ $name failed"
    fi
done

echo "All datasets processed!"
```

### Python automatizācijas skripts (Daudzplatformas)

Uzlabota automatizācija ar kļūdu apstrādi (darbojas ar Windows un Linux):

```python
import subprocess
import os
import sys
from pathlib import Path
from datetime import datetime

def process_dataset(input_folder):
    """Process a folder using Chloros CLI"""
    cmd = ['chloros-cli', 'process', str(input_folder)]
    
    # Execute command
    result = subprocess.run(
        cmd, 
        capture_output=True, 
        text=True,
        encoding='utf-8'
    )
    
    return result.returncode == 0, result.stdout, result.stderr

def main():
    """Process all datasets in a directory"""
    # Adjust path for your platform
    # Windows: Path('C:/Datasets/2025')
    # Linux:   Path.home() / 'datasets' / '2025'
    datasets_dir = Path('C:/Datasets/2025')
    log_file = Path('processing_log.txt')
    
    successful = []
    failed = []
    
    # Start processing
    print(f"Starting batch processing: {datetime.now()}")
    print(f"Scanning: {datasets_dir}")
    print("=" * 60)
    
    for dataset_folder in sorted(datasets_dir.iterdir()):
        if not dataset_folder.is_dir():
            continue
        
        print(f"\nProcessing: {dataset_folder.name}")
        
        success, stdout, stderr = process_dataset(dataset_folder)
        
        if success:
            print(f"✓ {dataset_folder.name} - SUCCESS")
            successful.append(dataset_folder.name)
        else:
            print(f"✗ {dataset_folder.name} - FAILED")
            failed.append(dataset_folder.name)
            
            # Log error details
            with open(log_file, 'a', encoding='utf-8') as f:
                f.write(f"\n=== {dataset_folder.name} - {datetime.now()} ===\n")
                f.write(f"STDOUT:\n{stdout}\n")
                f.write(f"STDERR:\n{stderr}\n")
    
    # Print summary
    print("\n" + "=" * 60)
    print(f"SUMMARY - Completed: {datetime.now()}")
    print(f"  Successful: {len(successful)}")
    print(f"  Failed: {len(failed)}")
    
    if failed:
        print(f"\nFailed folders:")
        for folder in failed:
            print(f"  - {folder}")
        print(f"\nCheck {log_file} for error details")
        sys.exit(1)
    else:
        print("\nAll datasets processed successfully!")
        sys.exit(0)

if __name__ == '__main__':
    main()
```

***

## Apstrādes darba plūsma

### Standarta darba plūsma

1. **Ievade**: Mapes, kas satur RAW/JPG attēlu pārus
2. **Atklāšana**: CLI automātiski skenē atbalstītos attēlu failus
3. **Apstrāde**: Paralēlais režīms pielāgojas jūsu procesora kodoliem (Chloros+)
4. **Rezultāts**: Izveido kameras modeļa apakšmapes ar apstrādātajiem attēliem

### Rezultāta struktūras piemērs

```

MyProject/
├── project.json                             # Project metadata
├── 2025_0203_193056_008.JPG                # Original JPG
├── 2025_0203_193055_007.RAW                # Original RAW
└── Survey3N_RGN/                           # Processed outputs ✓
    ├── 2025_0203_193056_008_Reflectance.tif   # Calibrated reflectance
    ├── 2025_0203_193056_008_Target.tif        # Target detection
    └── ...
```

### Apstrādes laika aprēķini

Tipisks apstrādes laiks 100 attēliem (katrs 12 MP):

| Platforma | Režīms | Aplēstais laiks | Piezīmes |
| --- | --- | --- | --- |
| **Datoru 12 GB+ GPU** | `GPU_PARALLEL` | 5–10 min | Ātrākā opcija |
| **Datoru 8 GB GPU** | `GPU_SINGLE` | 10–15 min | Laba veiktspēja |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 15–25 min | Malas aprēķini |
| **Jetson Nano 8 GB** | `GPU_SINGLE` | 30–60 min | Ierobežota atmiņa |
| **Tikai CPU** | `CPU_PARALLEL` | 20–40 min | Nav nepieciešams GPU |

{% hint style="info" %}
**Padoms par veiktspēju**: Apstrādes laiks atšķiras atkarībā no attēlu skaita, izšķirtspējas, debayer metodes un aparatūras. Tekstūras apzinātais debayer aizņem ievērojami ilgāku laiku nekā standarta. Sīkāku informāciju skatiet sadaļā [Dinamiskā aprēķinu pielāgošana](processing-architecture/dynamic-compute-adaptation.md).
{% endhint %}

***

## Problēmu novēršana

### CLI nav atrasts

**Windows Kļūda:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Windows Risinājumi:**

1. Pārbaudiet instalācijas vietu:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Ja nav PATH, izmantojiet pilnu ceļu:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Pievienojiet PATH manuāli:
   * Atveriet Sistēmas īpašības → Vides mainīgie
   * Rediģējiet PATH mainīgo
   * Pievienojiet: `C:\Program Files\Chloros\resources\cli`
   * Pārstartējiet termināli

**Linux Kļūda:**

```
chloros-cli: command not found
```

**Linux Risinājumi:**

1. Pārbaudiet instalāciju:

```bash
which chloros-cli
dpkg -L chloros-amd64  # or chloros-arm64-jp6
```

2. Atjauniniet savu apvalku:

```bash
source ~/.bashrc
```

3. Pārbaudiet atļaujas:

```bash
sudo chmod +x /usr/bin/chloros-cli
```

***

### Backend neizdevās sākt**Kļūda:**

```

Backend failed to start within 30 seconds
```

**Risinājumi:**

1. Pārbaudiet, vai backend jau darbojas (vispirms to aizveriet)
2. Pārbaudiet, vai ugunsmūris to neblokē (Windows) vai pārbaudiet porta pieejamību (Linux: `lsof -i :5000`)
3. Izmēģiniet citu portu:

```bash
# Windows
chloros-cli --port 5001 process "C:\Datasets\Field_A"

# Linux
chloros-cli --port 5001 process ~/datasets/field_a
```

4. Piespiediet backend pārstartēšanu:

```bash
# Windows
chloros-cli --restart process "C:\Datasets\Field_A"

# Linux
chloros-cli --restart process ~/datasets/field_a
```

5. Uz Linux pārbaudiet, vai backend izpildāmais fails pastāv:

```bash
ls -la /usr/lib/chloros/chloros-backend
```

***

### Licences / autentifikācijas problēmas**Kļūda:**

```

Chloros+ license required for CLI access
```

**Risinājumi:**

1. Pārbaudiet, vai jums ir aktīvs Chloros+ abonements
2. Piesakieties ar savām lietotājvārda un paroles datiem:

```bash
chloros-cli login user@example.com 'password'
```

3. Pārbaudiet licences statusu:

```bash
chloros-cli status
```

4. Sazinieties ar atbalsta dienestu: info@mapir.camera

***

### Nav atrastas attēlu**Kļūda:**

```

No images found in the specified folder
```

**Risinājumi:**

1. Pārbaudiet, vai mapē ir atbalstītie formāti (.RAW, .TIF, .JPG)
2. Pārbaudiet, vai mapes ceļš ir pareizs (ceļiem ar atstarpēm izmantojiet pēdiņas)
3. Pārliecinieties, ka jums ir lasīšanas tiesības uz mapi
4. Pārbaudiet, vai failu paplašinājumi ir pareizi

***

### Apstrāde apstājas vai iesaldējas**Risinājumi:**

1. Pārbaudiet pieejamo diska vietu (pārliecinieties, ka tā ir pietiekama izvadei)
2. Aizveriet citas programmas, lai atbrīvotu atmiņu
3. Samaziniet attēlu skaitu (apstrādājiet partijās)

***

### Ports jau tiek izmantots**Kļūda:**

```

Port 5000 is already in use
```

**Risinājumi:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

**Linux:**

```bash
# Find what's using port 5000
lsof -i :5000

# Use a different port
chloros-cli --port 5001 process ~/datasets/field_a
```

***

## Bieži uzdotie jautājumi

### J: Vai man ir nepieciešama licence CLI?

**A:**Jā! CLI prasa maksas**Chloros+ licenci**.

* ❌ Standarta (bezmaksas) plāns: CLI ir atspējots
* ✅ Chloros+ (maksas) plāni: CLI ir pilnībā iespējots

Abonējieties: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### J: Vai es varu izmantot CLI serverī bez GUI?**A:** Jā! CLI darbojas pilnīgi bez grafiskās saskarnes. Tas ir galvenais lietošanas gadījums Linux.**Windows serveris:**
* Windows Server 2016 vai jaunāka versija
* Instalēta Visual C++ Redistributable

**Linux serveris:**
* Ubuntu 20.04+ / Debian 11+ (amd64) vai JetPack 6 (arm64)
* Instalējiet, izmantojot `.deb` paketi

**Abas platformas:**
* Vismaz 8 GB RAM (ieteicams 16 GB)
* Vienreizēja licences aktivizēšana: `chloros-cli login user@example.com 'password'`

***

### J: Kur tiek saglabāti apstrādātie attēli?**A:**Pēc noklusējuma apstrādātie attēli tiek saglabāti**tajā pašā mapē, kurā atrodas ievades faili**, kameras modeļa apakšmapēs (piem., `Survey3N_RGN/`).

Izmantojiet opciju `-o`, lai norādītu citu izvades mapi:

```bash
# Windows
chloros-cli process "C:\Input" -o "D:\Output"

# Linux
chloros-cli process ~/input -o ~/output
```

***

### J: Vai varu apstrādāt vairākas mapes vienlaikus?**A:** Tieši ar vienu komandu nē, bet varat izmantot skriptus, lai apstrādātu mapes secīgi. Skatīt sadaļu [Automatizācija un skripti](CLI.md#automation--scripting).***

### J: Kā saglabāt CLI izvadi žurnāla failā?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Batch:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

**Linux Bash:**

```bash
chloros-cli process ~/datasets/field_a 2>&1 | tee processing.log
```

***

### J: Kas notiek, ja apstrādes laikā nospiežu Ctrl+C?**A:** CLI:

1. Pārtrauks apstrādi pareizi
2. Izslēgs backend
3. Iziet ar kodu 130

Daļēji apstrādāti attēli var palikt izvades mapē.

***

### J: Vai varu automatizēt CLI apstrādi?**A:** Protams! CLI ir paredzēts automatizācijai. Skatīt [Automatizācija un skripti](CLI.md#automation--scripting) par PowerShell (Windows), Batch (Windows), Bash (Linux) un Python (vairāku platformu) piemērus.***

### J: Kā pārbaudīt CLI versiju?**A:**

```bash
chloros-cli --version
```

**Rezultāts:**

```

Chloros CLI 1.1.0
```

***

## Palīdzības saņemšana

### Palīdzība komandrindā

Skatīt palīdzības informāciju tieši CLI:

```bash
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### Atbalsta kanāli

* **E-pasts**: info@mapir.camera
* **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Cenas**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## Pilnīgi piemēri

### 1. piemērs: Pamata apstrāde

Apstrāde ar noklusējuma iestatījumiem (vignette, reflectance):

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a_2025_01_15
```

***

### 2. piemērs: Augstas kvalitātes zinātniskie rezultāti

32 bitu peldošā komata TIFF:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "TIFF (32-bit, Percent)" \
  --vignette \
  --reflectance
```

***

### 3. piemērs: Ātra priekšskatīšanas apstrāde

8 bitu PNG bez kalibrēšanas ātrai pārskatīšanai:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "PNG (8-bit)" \
  --no-vignette \
  --no-reflectance
```

***

### 4. piemērs: Apstrāde ar PPK korekcijām

Piemērojiet PPK korekcijas ar atstarojumu:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --ppk \
  --reflectance
```

***

### 5. piemērs: Pielāgota izvades vieta

Apstrādājiet citā vietā ar konkrētu formātu:

**Windows:**

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

**Linux:**

```bash
chloros-cli process ~/input/raw_images \
  -o ~/output/processed \
  --format "TIFF (16-bit)"
```

***

### 6. piemērs: Autentifikācijas darbplūsma

Pilnīga autentifikācijas darbplūsma (vienāda visās platformās):

```bash
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
# Windows: chloros-cli process "C:\Datasets\Field_A"
# Linux:   chloros-cli process ~/datasets/field_a
chloros-cli process ~/datasets/field_a

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### 7. piemērs: Daudzvalodu lietošana

Saskarnes valodas maiņa (vienāda visās platformās):

```bash
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
# Windows: chloros-cli process "C:\Vuelos\Campo_A"
# Linux:   chloros-cli process ~/vuelos/campo_a
chloros-cli process ~/vuelos/campo_a

# Change back to English
chloros-cli language en
```
