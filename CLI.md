# CLI : Komandu rinda

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI** nodrošina jaudīgu komandrindas piekļuvi Chloros attēlu apstrādes dzinējam, ļaujot automatizēt, izveidot skriptus un veikt bezgalvas darbības jūsu attēlu apstrādes darba plūsmās.

### Galvenās funkcijas

* 🚀 **Automatizācija** - vairāku datu kopu skriptu partiju apstrāde
* 🔗 **Integrācija** - iekļaušana esošajos darba plūsmās un cauruļvados
* 💻 **Darbība bez galvas** - darbināma bez GUI
* 🌍 **Daudzvalodība** - atbalsts 38 valodām
* ⚡ **Paralēla apstrāde** — dinamiski pielāgojas jūsu CPU (līdz 16 paralēliem darbiniekiem)

### Prasības

| Prasība          | Detalizēta informācija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Operētājsistēma** | Windows 10/11 (64 bitu)                                              |
| **Licence**          | Chloros+ ([nepieciešams maksas plāns](https://cloud.mapir.camera/pricing)) |
| **Atmiņa**           | Vismaz 8 GB RAM (ieteicams 16 GB)                                  |
| **Internets**         | Nepieciešams licences aktivizēšanai                                     |
| **Diska vieta**       | Atšķiras atkarībā no projekta apjoma                                              |

{% hint style=&quot;warning&quot; %}
**Licences prasības**: CLI ir nepieciešama maksas Chloros+ abonementa. Standarta (bezmaksas) plāniem nav piekļuves CLI. Apmeklējiet [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing), lai veiktu uzlabojumus.
{% endhint %}

## Ātrs sākums

### Instalēšana

CLI automātiski tiek iekļauts Chloros instalētājs:

1. Lejupielādējiet un palaidiet **Chloros Installer.exe**
2. Pabeidziet instalēšanas vedni
3. CLI instalēts: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style=&quot;success&quot; %}
Instalētājs automātiski pievieno `chloros-cli` jūsu sistēmas PATH. Pēc instalācijas pārstartējiet termināli.
{% endhint %}

### Pirmā uzstādīšana

Pirms CLI lietošanas aktivizējiet savu Chloros+ licenci:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### Pamata lietošana

Apstrādājiet mapes ar noklusējuma iestatījumiem:

```powershell
chloros-cli process "C:\Images\Dataset001"
```

***

## Komandu atsauces

### Vispārīgā sintakse

```
chloros-cli [global-options] <command> [command-options]
```

***

## Komandas

### `process` - attēlu apstrāde

Apstrādāt attēlus mapē ar kalibrēšanu.

**Sintakse:**

```bash
chloros-cli process <input-folder> [options]
```

**Piemērs:**

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### Apstrādes komandu opcijas

| Opcija                | Tips    | Noklusējums        | Apraksts                                                                            |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>`      | Celiņš    | _Obligāts_     | Mapīte, kas satur RAW/JPG multispektrālos attēlus                                         |
| `-o, --output`        | Celiņš    | Tāds pats kā ievade  | Izvades mapīte apstrādātajiem attēliem                                                     |
| `-n, --project-name`  | String  | Automātiski ģenerēts | Pielāgots projekta nosaukums                                                                    |
| `--vignette`          | Karodziņš    | Iespējots        | Iespējot vinjetes korekciju                                                             |
| `--no-vignette`       | Karodziņš    | -              | Atcelt vinjetes korekciju                                                            |
| `--reflectance`       | Karodziņš    | Iespējots        | Iespējot atstarojuma kalibrēšanu                                                         |
| `--no-reflectance`    | Karodziņš    | -              | Atspējot atstarojuma kalibrēšanu                                                        |
| `--ppk`               | Karodziņš    | Atvienots       | Piemērot PPK korekcijas no .daq gaismas sensora datiem                                      |
| `--format`            | Izvēle  | TIFF (16 bitu)  | Izvades formāts: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Vesels skaitlis | Automātiski           | Minimālais mērķa izmērs pikseļos kalibrēšanas paneļa noteikšanai                          |
| `--target-clustering` | Vesels skaitlis | Automātiski           | Mērķa klasterizācijas slieksnis (0–100)                                                    |
| `--exposure-pin-1`    | String  | Nav           | Ekspozīcijas fiksēšana kameras modelim (1. kontakts)                                                 |
| `--exposure-pin-2`    | String  | Nav           | Ekspozīcijas fiksēšana kameras modelim (2. kontakts)                                                 |
| `--recal-interval`    | Vesels skaitlis | Automātiski           | Pārkalibrēšanas intervāls sekundēs                                                      |
| `--timezone-offset`   | Vesels skaitlis | 0              | Laika zonas nobīde stundās                                                               |

***

### `login` - Kontu autentificēšana

Piesakieties ar savām Chloros+ identifikācijas datiem, lai aktivizētu CLI apstrādi.

**Sintakse:**

```bash
chloros-cli login <email> <password>
```

**Piemērs:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style=&quot;warning&quot; %}
**Īpašie simboli**: Lietojiet vienkāršās pēdiņas ap parolēm, kas satur simbolus, piemēram, `$`, `!` vai atstarpes.
{% endhint %}

**Rezultāts:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Dzēst autentifikācijas datus

Dzēst saglabātos autentifikācijas datus un iziet no sava konta.

**Sintakse:**

```bash
chloros-cli logout
```

**Piemērs:**

```powershell
chloros-cli logout
```

**Rezultāts:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

***

### `status` - Pārbaudīt licences statusu

Parādīt pašreizējo licences un autentifikācijas statusu.

**Sintakse:**

```bash
chloros-cli status
```

**Piemērs:**

```powershell
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

```powershell
chloros-cli export-status
```

**Lietošanas gadījums:** Izsauc šo komandu apstrādes laikā, lai pārbaudītu eksporta gaitu.

***

### `language` — interfeisa valodas pārvaldība

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

```powershell
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

| Kods    | Valoda              | Vietējais nosaukums      |
| ------- | --------------------- | ---------------- |
| `en`    | Angļu               | English          |
| `es`    | Spāņu               | Español          |
| `pt`    | Portugāļu            | Português        |
| `fr`    | Franču                | Français         |
| `de`    | Vācu valoda                | Deutsch          |
| `it`    | Itāļu valoda               | Italiano         |
| `ja`    | Japāņu valoda              | 日本語              |
| `ko`    | Korejiešu valoda                | 한국어              |
| `zh`    | Ķīniešu (vienkāršots)  | 简体中文             |
| `zh-TW` | Ķīniešu (tradicionāls) | 繁體中文             |
| `ru`    | Krievu               | Русский          |
| `nl`    | Holandiešu valoda | Nederlands       |
| `ar`    | Arābu valoda | العربية          |
| `pl`    | Poļu valoda | Polski           |
| `tr`    | Turku valoda | Türkçe           |
| `hi`    | Hindi                 | हिंदी            |
| `id`    | Indonēziešu            | Bahasa Indonesia |
| `vi`    | Vjetnamiešu            | Tiếng Việt       |
| `th`    | Taizemiešu valoda | ไทย              |
| `sv`    | Zviedru valoda | Svenska          |
| `da`    | Dāņu valoda | Dansk            |
| `no`    | Norvēģu valoda | Norsk            |
| `fi`    | Somu               | Suomi            |
| `el`    | Grieķu                 | Ελληνικά         |
| `cs`    | Čehu                 | Čeština          |
| `hu`    | Ungāru valoda             | Magyar           |
| `ro`    | Rumāņu valoda              | Română           |
| `uk`    | Ukraiņu valoda             | Українська       |
| `pt-BR` | Brazīlijas portugāļu valoda  | Português Brasileiro |
| `zh-HK` | Kantoniešu valoda             | 粵語             |
| `ms`    | Malajiešu valoda                 | Bahasa Melayu    |
| `sk`    | Slovāku valoda                | Slovenčina       |
| `bg`    | Bulgāru valoda             | Български        |
| `hr`    | Horvātu valoda              | Hrvatski         |
| `lt`    | Lietuviešu valoda            | Lietuvių         |
| `lv`    | Latviešu               | Latviešu         |
| `et`    | Eesti              | Eesti            |
| `sl`    | Slovenščina             | Slovenščina      |

{% hint style=&quot;success&quot; %}
**Automātiska saglabāšana**: Jūsu valodas izvēle tiek saglabāta `~/.chloros/cli_language.json` un saglabājas visās sesijās.
{% endhint %}

***

### `set-project-folder` - Iestatīt noklusējuma projekta mapi

Mainīt noklusējuma projekta mapes atrašanās vietu (kopīga ar GUI).

**Sintakse:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Piemērs:**

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `get-project-folder` - Rādīt projekta mapes atrašanās vietu

Rāda pašreizējo noklusējuma projekta mapes atrašanās vietu.

**Sintakse:**

```bash
chloros-cli get-project-folder
```

**Piemērs:**

```powershell
chloros-cli get-project-folder
```

**Rezultāts:**

```
ℹ Current project folder: C:\Projects\2025
```

***

### `reset-project-folder` - Atjaunot noklusējuma iestatījumus

Atjauno projekta mapes noklusējuma atrašanās vietu.

**Sintakse:**

```bash
chloros-cli reset-project-folder
```

***

## Globālās opcijas

Šīs opcijas attiecas uz visām komandām:

| Opcija          | Tips    | Noklusējums       | Apraksts                                      |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe` | Celiņš    | Automātiski noteikts | Celiņš uz backend izpildāmo failu                       |
| `--port`        | Vesels skaitlis | 5000          | Backend API porta numurs                          |
| `--restart`     | Karodziņš    | -             | Piespiedu atkārtota aizmugures programmas palaišana (izbeidz esošos procesus) |
| `--version`     | Karodziņš    | -             | Parāda versijas informāciju un iziet                |
| `--help`        | Karodziņš    | -             | Parāda palīdzības informāciju un iziet                   |

**Piemērs ar globālajām opcijām:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## Apstrādes iestatījumu rokasgrāmata

### Paralēlā apstrāde

Chloros+ CLI **automātiski mēro** paralēlo apstrādi, lai tā atbilstu jūsu datora iespējām:

**Kā tas darbojas:**

* Atklāj jūsu CPU kodolus un RAM
* Piešķir darba spēkus: **2× CPU kodoli** (izmanto hiperthreading)
* **Maksimums: 16 paralēli darba spēki** (stabilitātes nodrošināšanai)

**Sistēmas līmeņi:**

| Sistēmas tips   | CPU        | RAM      | Darba spēki  | Veiktspēja     |
| ------------- | ---------- | -------- | -------- | --------------- |
| **Augstākā klase**  | 16+ kodoli  | 32+ GB   | Līdz 16 | Maksimālais ātrums   |
| **Vidēja klase** | 8–15 kodoli | 16–31 GB | 8–16     | Lielisks ātrums |
| **Zema klase**   | 4–7 kodoli  | 8–15 GB  | 4–8      | Labs ātrums      |

{% hint style=&quot;success&quot; %}
**Automātiska optimizācija**: CLI automātiski atpazīst jūsu sistēmas specifikācijas un konfigurē optimālu paralēlo apstrādi. Nav nepieciešama manuāla konfigurācija!
{% endhint %}

### Debayer metodes

CLI izmanto **Augsta kvalitāte (ātrāka)** kā noklusējuma un ieteicamo debayer algoritmu:

| Metode                      | Kvalitāte | Ātrums | Apraksts                                 |
| --------------------------- | ------- | ----- | ------------------------------------------- |
| **Augsta kvalitāte (ātrāka)** ⭐ | ⭐⭐⭐⭐    | ⚡⚡⚡   | Malas atpazīšanas algoritms (noklusējuma, ieteicams) |

### Vignette korekcija

**Funkcija:** Koriģē gaismas zudumu attēla malās (tumšāki stūri, kas bieži sastopami kameru attēlos).

* **Iespējots pēc noklusējuma** — lielākajai daļai lietotāju šī funkcija ir jāatstāj iespējota.
* Lai atspējotu, izmantojiet `--no-vignette`.

{% hint style=&quot;success&quot; %}
**Ieteikums**: vienmēr ieslēdziet vinjetes korekciju, lai nodrošinātu vienādu spilgtumu visā kadrā.
{% endhint %}

### Atstarošanas kalibrēšana

Pārvērš neapstrādātos sensora vērtības standartizētos atstarošanas procentos, izmantojot kalibrēšanas paneļus.

* **Iespējots pēc noklusējuma** — nepieciešams veģetācijas analīzei.
* Nepieciešami kalibrēšanas mērķa paneļi attēlos.
* Lai atspējotu, izmantojiet `--no-reflectance`.

{% hint style=&quot;info&quot; %}
**Prasības**: Lai nodrošinātu precīzu atstarojuma pārveidošanu, pārliecinieties, ka kalibrēšanas paneļi ir pareizi eksponēti un redzami attēlos.
{% endhint %}

### PPK korekcijas

**Funkcijas:** Pielieto pēcapstrādes kinemātiskās korekcijas, izmantojot DAQ-A-SD žurnāla datus, lai uzlabotu GPS precizitāti.

* **Pēc noklusējuma atspējots**
* Lai aktivizētu, izmantojiet `--ppk`
* Nepieciešami .daq faili projekta mapē no MAPIR DAQ-A-SD gaismas sensora.

### Izvades formāti

<table><thead><tr><th width="197">Formāts</th><th width="130.20001220703125">Bitu dziļums</th><th width="116.5999755859375">Faila izmērs</th><th>Vislabāk piemērots</th></tr></thead><tbody><tr><td><strong>TIFF (16 bitu)</strong> ⭐</td><td>16 bitu vesels skaitlis</td><td>Liels</td><td>ĢIS analīze, fotogrammetrija (ieteicams)</td></tr><tr><td><strong>TIFF (32 bitu, procentos)</strong></td><td>32 bitu peldošais</td><td>Ļoti liels</td><td>Zinātniskā analīze, pētījumi</td></tr><tr><td><strong>PNG (8 bitu)</strong></td><td>8 bitu vesels skaitlis</td><td>Vidējs</td><td>Vizuāla pārbaude, tīmekļa koplietošana</td></tr><tr><td><strong>JPG (8 bitu)</strong></td><td>8 bitu vesels skaitlis</td><td>Mazs</td><td>Ātrs priekšskatījums, saspiesta izvade</td></tr></tbody></table>***

## Automatizācija un skripti

### PowerShell partiju apstrāde

Vairāku datu kopu mapju automātiska apstrāde:

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

### Windows partiju skripts

Vienkārša cilpa partiju apstrādei:

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

### Python automatizācijas skripts

Uzlabota automatizācija ar kļūdu apstrādi:

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
4. **Izvade**: Izveido kameras modeļa apakšmapes ar apstrādātiem attēliem

### Izvades struktūras piemērs

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

| Režīms              | Laiks      | Aparatūra                                     |
| ----------------- | --------- | -------------------------------------------- |
| **Paralēlais režīms** | 5–10 min  | i7/Ryzen 7, 16 GB RAM, SSD (līdz 16 darbiniekiem) |
| **Paralēlais režīms** | 10–15 min | i5/Ryzen 5, 8 GB RAM, HDD (līdz 8 darbiniekiem)   |

{% hint style=&quot;info&quot; %}
**Veiktspējas padoms**: Apstrādes laiks atšķiras atkarībā no attēlu skaita, izšķirtspējas un datora specifikācijām.
{% endhint %}

***

## Problēmu novēršana

### CLI nav atrasts

**Kļūda:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Risinājumi:**

1. Pārbaudiet instalācijas vietu:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. Ja nav PATH, izmantojiet pilnu ceļu:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. Manuāli pievienojiet PATH:
   * Atveriet Sistēmas īpašības → Vides mainīgie
   * Rediģējiet PATH mainīgo
   * Pievienojiet: `C:\Program Files\Chloros\resources\cli`
   * Pārstartējiet termināli

***

### Backend neizdevās sākt

**Kļūda:**

```
Backend failed to start within 30 seconds
```

**Risinājumi:**

1. Pārbaudiet, vai backend jau darbojas (vispirms to aizveriet)
2. Pārbaudiet, vai Windows ugunsmūris to neblokē
3. Izmēģiniet citu portu:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Piespiediet backend atkārtotu palaišanu:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### Licences / autentifikācijas problēmas

**Kļūda:**

```
Chloros+ license required for CLI access
```

**Risinājumi:**

1. Pārbaudiet, vai jums ir aktīvs Chloros+ abonements.
2. Piesakieties ar savām identifikācijas ziņām:

```powershell
chloros-cli login user@example.com 'password'
```

3. Pārbaudiet licences statusu:

```powershell
chloros-cli status
```

4. Sazinieties ar atbalsta dienestu: info@mapir.camera

***

### Nav atrastas attēlus

**Kļūda:**

```
No images found in the specified folder
```

**Risinājumi:**

1. Pārbaudiet, vai mapē ir atbalstītie formāti (.RAW, .TIF, .JPG).
2. Pārbaudiet, vai mapes ceļš ir pareizs (ceļiem ar atstarpēm izmantojiet pēdiņas).
3. Pārliecinieties, ka jums ir lasīšanas atļaujas mapē
4. Pārbaudiet, vai failu paplašinājumi ir pareizi

***

### Apstrāde apstājas vai iesaldējas

**Risinājumi:**

1. Pārbaudiet pieejamo diska vietu (pārliecinieties, ka tā ir pietiekama izvadei)
2. Aizveriet citas programmas, lai atbrīvotu atmiņu
3. Samaziniet attēlu skaitu (apstrādājiet partijās)

***

### Ports jau tiek izmantots

**Kļūda:**

```
Port 5000 is already in use
```

**Risinājums:**

Norādiet citu portu:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## Bieži uzdotie jautājumi

### J: Vai man ir nepieciešama licence CLI?

**A:** Jā! CLI ir nepieciešama maksas **Chloros+ licence**.

* ❌ Standarta (bezmaksas) plāns: CLI ir atspējots
* ✅ Chloros+ (maksas) plāni: CLI pilnībā iespējots

Abonējieties: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### J: Vai es varu izmantot CLI serverī bez GUI?

**A:** Jā! CLI darbojas pilnīgi bez galvas. Prasības:

* Windows Server 2016 vai jaunāka versija
* Visual C++ Redistributable instalēta
* Pietiekama RAM (vismaz 8 GB, ieteicams 16 GB)
* Vienreizēja GUI licences aktivizēšana jebkurā datorā

***

### J: Kur tiek saglabāti apstrādātie attēli?

**A:** Pēc noklusējuma apstrādātie attēli tiek saglabāti **tajā pašā mapē, kurā ievadīti**, kameras modeļa apakšmapēs (piemēram, `Survey3N_RGN/`).

Izmantojiet opciju `-o`, lai norādītu citu izvades mapi:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### J: Vai varu apstrādāt vairākas mapes vienlaikus?

**A:** Ne tieši ar vienu komandu, bet varat izmantot skriptu, lai apstrādātu mapes secīgi. Skatīt sadaļu [Automation &amp; Scripting](CLI.md#automation--scripting).

***

### J: Kā saglabāt CLI izvadi žurnāla failā?

**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Partija:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### J: Kas notiek, ja apstrādes laikā nospiežu Ctrl+C?

**A:** CLI:

1. Pārtrauks apstrādi
2. Izslēgs backend
3. Iziet ar kodu 130

Daļēji apstrādāti attēli var palikt izvades mapē.

***

### J: Vai varu automatizēt CLI apstrādi?

**A:** Protams! CLI ir paredzēts automatizācijai. Skatīt [Automation &amp; Scripting](CLI.md#automation--scripting) par PowerShell, Batch un Python piemēriem.

***

### J: Kā pārbaudīt CLI versiju?

**A:**

```powershell
chloros-cli --version
```

**Rezultāts:**

```
Chloros CLI 1.0.2
```

***

## Palīdzības saņemšana

### Komandrindas palīdzība

Palīdzības informāciju var apskatīt tieši CLI:

```powershell
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
* **Cenas**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

## Pilnīgi piemēri

### 1. piemērs: Pamata apstrāde

Apstrāde ar noklusējuma iestatījumiem (vinjete, atstarošanās):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### 2. piemērs: Augstas kvalitātes zinātniski rezultāti

32 bitu peldošais TIFF:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### 3. piemērs: ātra priekšskatīšanas apstrāde

8 bitu PNG bez kalibrēšanas ātrai pārskatīšanai:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### 4. piemērs: PPK koriģēta apstrāde

Piemēro PPK korekcijas ar atstarojumu:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### 5. piemērs: Pielāgota rezultātu atrašanās vieta

Apstrādā uz citu disku ar konkrētu formātu:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### 6. piemērs: autentifikācijas darba plūsma

Pilnīga autentifikācijas plūsma:

```powershell
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
chloros-cli process "C:\Datasets\Field_A"

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### 7. piemērs: daudzvalodu lietošana

Mainiet saskarnes valodu:

```powershell
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
chloros-cli process "C:\Vuelos\Campo_A"

# Change back to English
chloros-cli language en
```
