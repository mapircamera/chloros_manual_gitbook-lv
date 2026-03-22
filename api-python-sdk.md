# API : Python SDK

**Chloros Python SDK** nodrošina programmatisku piekļuvi attēlu apstrādes dzinējam Chloros, ļaujot automatizēt, izveidot pielāgotas darba plūsmas un vienkārši integrēt ar jūsu Python lietojumprogrammām un pētniecības procesiem.

### Galvenās funkcijas

* 🐍 **Native Python** - Tīrs, Python stilā rakstīts API attēlu apstrādei
* 🔧 **Pilnīga API piekļuve** - Pilnīga kontrole pār Chloros apstrādi
* 🚀 **Automatizācija** — izveidojiet pielāgotas partiju apstrādes darbplūsmas
* 🔗 **Integrācija** — iekļaujiet Chloros esošajās Python lietojumprogrammās
* 📊 **Pētniecībai gatavs** — ideāli piemērots zinātniskās analīzes procesiem
* ⚡ **Paralēlā apstrāde** - Mērogojas atbilstoši jūsu procesora kodoliem (Chloros+)

### Prasības

| Prasība          | Detalizēta informācija                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Chloros instalēts** | Windows: instalētājs darbvirsmai; Linux: `.deb` pakete                  |
| **Licence**          | Chloros+ ([nepieciešams maksas plāns](https://cloud.mapir.camera/pricing)) |
| **Operētājsistēma** | Windows 10/11 (64 bitu), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Python**           | Python 3.7 vai jaunāka versija                                                |
| **Atmiņa**           | Vismaz 8 GB RAM (ieteicams 16 GB)                                  |
| **Internets**         | Nepieciešams licences aktivizēšanai                                     |

{% hint style="warning" %}
**Licences prasības**: Python SDK prasa maksas Chloros+ abonementu, lai piekļūtu API. Standarta (bezmaksas) plāniem nav piekļuves API/SDK. Apmeklējiet [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing), lai veiktu uzlabojumu.
{% endhint %}

## Ātrs sākums

### Instalēšana

Instalējiet, izmantojot pip:

```bash
pip install chloros-sdk
```

{% hint style="info" %}
**Pirmā uzstādīšana**: Pirms SDK lietošanas aktivizējiet savu Chloros+ licenci, atverot Chloros, Chloros (pārlūks) vai Chloros CLI un pieteicoties ar savām lietotājvārda un paroles datiem. To ir nepieciešams izdarīt tikai vienu reizi. Linux (bez grafiskās lietotāja saskarnes) izmantojiet: `chloros-cli login user@example.com 'password'`
{% endhint %}

### Pamata lietošana

Apstrādājiet mapi ar tikai dažām rindām:

```python
from chloros_sdk import process_folder

# One-line processing (Windows)
results = process_folder("C:\\DroneImages\\Flight001")

# One-line processing (Linux)
results = process_folder("/home/user/drone_images/flight001")
```

{% hint style="info" %}
**Daudzplatformas ceļi**: Šīs lapas koda piemēros izmantoti Windows stila ceļi (piem., `C:\\DroneImages\\Flight001`). Linux vietā izmantojiet Linux ceļus (piem., `/home/user/drone_images/flight001` vai `~/drone_images/flight001`). SDK darbojas identiski abās platformās.
{% endhint %}

### Pilnīga kontrole

Papildu darbplūsmām:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")  # Windows
# chloros.import_images("/home/user/drone_images/flight001")  # Linux

# Configure settings
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE", "GNDVI"]
)

# Process images
chloros.process(mode="parallel", wait=True)
```

***

## Instalācijas rokasgrāmata

### Priekšnosacījumi

Pirms SDK instalēšanas pārliecinieties, ka jums ir:

1. **Chloros ir instalēts** — Windows: instalācijas programma darbvirsmai ([lejupielāde](download.md)); Linux: `.deb` pakete ([Linux uzstādīšana](linux/linux-installation.md))
2. **Python 3.7+** instalēts ([python.org](https://www.python.org))
3. **Aktīva Chloros+ licence** ([atjauninājums](https://cloud.mapir.camera/pricing))

### Instalēšana ar pip

**Standarta instalēšana:**

```bash
pip install chloros-sdk
```

**Ar progresa uzraudzības atbalstu:**

```bash
pip install chloros-sdk[progress]
```

**Izstrādes instalācija:**

```bash
pip install chloros-sdk[dev]
```

### Instalācijas pārbaude

Pārbaudiet, vai SDK ir instalēts pareizi:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## Pirmā uzstādīšana

### Licences aktivizēšana

SDK izmanto to pašu licenci kā Chloros, Chloros (pārlūks) un Chloros CLI. Aktivizējiet vienreiz, izmantojot GUI vai CLI:**Windows:**Atveriet**Chloros vai Chloros (pārlūks)** un pieteikties lietotāja <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> vai izmantojiet CLI.**Linux:** Izmantojiet CLI (GUI nav pieejams):

```bash
chloros-cli login user@example.com 'your_password'
```

Licence tiek saglabāta vietējā cache un saglabājas pēc sistēmas pārstartēšanas.

{% hint style="success" %}
**Vienreizēja konfigurācija**: Pēc pieteikšanās caur GUI vai CLI, SDK automātiski izmanto cache saglabāto licenci. Nav nepieciešama papildu autentifikācija!
{% endhint %}

{% hint style="info" %}
**Iziet**: SDK lietotāji var programmatiski dzēst saglabātās autentifikācijas datus, izmantojot `logout()` metodi. Skatīt [logout() metode](#logout) API atsauces dokumentā.
{% endhint %}

### Savienojuma pārbaude

Pārbaudiet, vai SDK var izveidot savienojumu ar Chloros:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## API atsauce

### ChlorosLocal klase

Galvenā klase vietējai Chloros attēlu apstrādei.

#### Konstruktors

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**Parametri:**

| Parametrs                 | Tips | Noklusējums                   | Apraksts                           |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `api_url`                 | str  | `"http://localhost:5000"` | URL vietējā Chloros aizmugurējā procesa          |
| `auto_start_backend`      | bool | `True`                    | Automātiski sākt backend, ja nepieciešams |
| `backend_exe`             | str  | `None` (automātiska noteikšana)      | Ceļš uz backend izpildāmo failu            |
| `timeout`                 | int  | `30`                      | Pieprasījuma laika limits sekundēs            |
| `backend_startup_timeout` | int  | `60`                      | Laika limits backend palaišanai (sekundēs) |

**Piemēri:**

```python
# Default (auto-start backend, auto-detect path on Windows and Linux)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path (Windows)
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom backend path (Linux)
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")

# Custom timeout with longer startup (e.g., for Jetson)
chloros = ChlorosLocal(timeout=60, backend_startup_timeout=120)
```

{% hint style="info" %}
**Daudzplatformas automātiskā noteikšana**: SDK automātiski mēģina atrast pareizo backend ceļu jūsu platformai:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (manuāli)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

***

### Metodes

#### `create_project(project_name, camera=None)`

Izveidojiet jaunu Chloros projektu.

**Parametri:**

| Parametrs      | Tips | Obligāts | Apraksts                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str  | Jā      | Projekta nosaukums                                     |
| `camera`       | str  | Nē       | Kameras veidne (piem., &quot;Survey3N\_RGN&quot;, &quot;Survey3W\_OCN&quot;) |

**Atgriež:** `dict` - Atbilde par projekta izveidi**Piemērs:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images(folder_path, recursive=False)`

Importē attēlus no mapes.

**Parametri:**

| Parametrs     | Tips     | Obligāts | Apraksts                        |
| ------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | str/Path | Jā      | Ceļš uz mapi ar attēliem         |
| `recursive`   | bool     | Nē       | Meklēt apakšmapes (noklusējums: False) |

**Atgriež:** `dict` - Importēšanas rezultāti ar failu skaitu**Piemērs:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure(**settings)`

Konfigurējiet apstrādes iestatījumus.

**Parametri:**

| Parametrs                 | Tips | Noklusējums                 | Apraksts                     |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `debayer`                 | str  | &quot;Standarta (ātrs, vidēja kvalitāte)&quot; | Debayer metode            |
| `vignette_correction`     | bool | `True`                  | Iespējot vinjetes korekciju      |
| `reflectance_calibration` | bool | `True`                  | Iespējot atstarojuma kalibrēšanu  |
| `indices`                 | list | `None`                  | Aprēķināmie veģetācijas indeksi |
| `export_format`           | str  | &quot;TIFF (16-bit)&quot;         | Izvades formāts                   |
| `ppk`                     | bool | `False`                 | Iespējot PPK korekcijas          |
| `custom_settings`         | dict | `None`                  | Papildu pielāgotie iestatījumi        |

**Eksporta formāti:**

* `"TIFF (16-bit)"` - Ieteicams GIS/fotogrammetrijai
* `"TIFF (32-bit, Percent)"` - Zinātniskā analīze
* `"PNG (8-bit)"` - Vizuālā pārbaude
* `"JPG (8-bit)"` - Saspiesta izvade

**Pieejamie indeksi:**NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 un citi.**Piemērs:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="Standard (Fast, Medium Quality)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```

***

#### `process(mode="parallel", wait=True, progress_callback=None)`

Apstrādājiet projekta attēlus.

**Parametri:**

| Parametrs           | Tips     | Noklusējums      | Apraksts                               |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `mode`              | str      | `"parallel"` | Apstrādes režīms: &quot;parallel&quot; vai &quot;serial&quot;   |
| `wait`              | bool     | `True`       | Gaidīt pabeigšanu                       |
| `progress_callback` | izsaucams | `None`       | Progresa atgriezeniskās saites funkcija (progress, msg) |
| `poll_interval`     | float    | `2.0`        | Progresa aptaujas intervāls (sekundes)   |

**Atgriež:** `dict` - Apstrādes rezultāti

{% hint style="warning" %}
**Paralēlais režīms**: Nepieciešama Chloros+ licence. Automātiski pielāgojas jūsu procesora kodoliem (līdz 16 darba procesiem).
{% endhint %}

**Piemērs:**

```python
# Simple processing
results = chloros.process()

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

# Fire-and-forget (non-blocking)
chloros.process(wait=False)
```

***

#### `get_config()`

Iegūst pašreizējo projekta konfigurāciju.

**Atgriež:** `dict` - Pašreizējā projekta konfigurācija**Piemērs:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

Iegūst informāciju par backend statusu, ieskaitot apstrādes gaitu katrā pavedienā.

**Atgriež:** `dict` - Backend statuss ar šādu struktūru:

```python
{
    "running": True,
    "url": "http://localhost:5000",
    "processing": {
        "percent": 75.0,
        "phase": "processing"
    },
    "export": {
        "percent": 50.0,
        "phase": "exporting",
        "active": True
    }
}
```

**Piemērs:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
print(f"Processing: {status['processing']['percent']}%")
print(f"Export: {status['export']['percent']}% - Active: {status['export']['active']}")
```

***

#### `shutdown_backend()`

Aizver backend (ja tas ir palaists ar SDK).

**Piemērs:**

```python
chloros.shutdown_backend()
```

***

#### `logout()`

Dzēš no vietējās sistēmas kešatmiņā saglabātās autentifikācijas datus.

**Apraksts:**

Programmatiski iziet no sistēmas, dzēšot kešatmiņā saglabātos autentifikācijas datus. Tas ir noderīgi:
* Pārslēgšanās starp dažādiem Chloros+ kontiem
* Pieslēgšanās datu dzēšanai automatizētās vidēs
* Drošības nolūkos (piem., noņemot pieslēgšanās datus pirms atinstalēšanas)

**Atgriež:** `dict` - Izslēgšanās darbības rezultāts**Piemērs:**

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Clear cached credentials
result = chloros.logout()
print(f"Logout successful: {result}")

# After logout, login required via GUI/CLI/Browser before next SDK use
```

{% hint style="info" %}
**Nepieciešama atkārtota autentifikācija**: Pēc `logout()` izsaukšanas jums ir jāpiesakās atkārtoti, izmantojot Chloros, Chloros (pārlūkprogramma) vai Chloros CLI, pirms izmantojat SDK.
{% endhint %}

***

### Ērtības funkcijas

#### `process_folder(folder_path, **options)`

Vienrindas ērtības funkcija mapes apstrādei.

**Parametri:**

| Parametrs                 | Tips     | Noklusējums         | Apraksts                    |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path`             | str/Path | Obligāts        | Ceļš uz mapi ar attēliem     |
| `project_name`            | str      | Automātiski ģenerēts  | Projekta nosaukums                   |
| `camera`                  | str      | `None`          | Kameras šablons                |
| `indices`                 | saraksts     | `["NDVI"]`      | Aprēķināmi indeksi           |
| `vignette_correction`     | bool     | `True`          | Iespējot vinjetes korekciju     |
| `reflectance_calibration` | bool     | `True`          | Iespējot atstarošanas kalibrēšanu |
| `export_format`           | str      | &quot;TIFF (16-bit)&quot; | Izvades formāts                  |
| `mode`                    | str      | `"parallel"`    | Apstrādes režīms                |
| `progress_callback`       | izsaucams | `None`          | Progresa atgriezeniskā saite              |

**Atgriež:** `dict` - Apstrādes rezultāti**Piemērs:**

```python
from chloros_sdk import process_folder

# Simple one-liner
results = process_folder("C:\\DroneImages\\Flight001")

# With custom settings
results = process_folder(
    "C:\\DroneImages\\Flight001",
    project_name="Field_A_Survey",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    mode="parallel"
)

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

results = process_folder(
    "C:\\DroneImages\\Flight001",
    progress_callback=show_progress
)
```

***

## Konteksta pārvaldnieka atbalsts

SDK atbalsta konteksta pārvaldniekus automātiskai tīrīšanai:

```python
from chloros_sdk import ChlorosLocal

# Auto-cleanup when done
with ChlorosLocal() as chloros:
    chloros.create_project("MyProject")
    chloros.import_images("C:\\Images")
    chloros.configure(indices=["NDVI"])
    chloros.process()
# Backend automatically shut down here
```

***

## Pilni piemēri

{% hint style="info" %}
**Linux lietotāji**: Visi zemāk minētie piemēri izmanto Windows ceļus. Aizstājiet `C:\\...` ceļus ar saviem Linux ceļiem (piem., `/home/user/...` vai `~/...`). Visas SDK funkcijas ir identiskas visās platformās.
{% endhint %}

### 1. piemērs: Pamata apstrāde

Apstrādājiet mapi ar noklusējuma iestatījumiem:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### 2. piemērs: Pielāgota darba plūsma

Pilnīga kontrole pār apstrādes procesu:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project with camera template
chloros.create_project("Research_Plot_A", camera="Survey3N_RGN")

# Import images
import_results = chloros.import_images("C:\\Research\\PlotA")
print(f"Imported {len(import_results.get('files', []))} images")

# Configure advanced settings
chloros.configure(
    debayer="Standard (Fast, Medium Quality)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=False,
    export_format="TIFF (16-bit)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI"]
)

# Process with progress monitoring
def show_progress(progress, message):
    print(f"Progress: {progress}% - {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

print("Processing complete!")
```

***

### 3. piemērs: vairāku mapju partiju apstrāde

Apstrādājiet vairākus lidojumu datu kopumus:

```python
from chloros_sdk import ChlorosLocal
from pathlib import Path

# Initialize SDK once
chloros = ChlorosLocal()

# List of flight folders
flights = [
    "C:\\Datasets\\Flight_001",
    "C:\\Datasets\\Flight_002",
    "C:\\Datasets\\Flight_003"
]

for flight_path in flights:
    flight_name = Path(flight_path).name
    print(f"\n{'='*60}")
    print(f"Processing: {flight_name}")
    print('='*60)
    
    try:
        # Create project
        chloros.create_project(flight_name, camera="Survey3N_RGN")
        
        # Import images
        chloros.import_images(flight_path)
        
        # Configure
        chloros.configure(
            vignette_correction=True,
            reflectance_calibration=True,
            indices=["NDVI", "NDRE", "GNDVI"]
        )
        
        # Process
        chloros.process(mode="parallel", wait=True)
        
        print(f"✓ {flight_name} completed successfully")
    
    except Exception as e:
        print(f"✗ {flight_name} failed: {e}")

print("\n" + "="*60)
print("All flights processed!")
```

***

### 4. piemērs: integrācija pētniecības procesā

Integrējiet Chloros ar datu analīzi:

```python
from chloros_sdk import ChlorosLocal
import pandas as pd
import matplotlib.pyplot as plt

# Initialize Chloros
chloros = ChlorosLocal()

# Field survey data
surveys = [
    {"name": "Plot_A", "folder": "C:\\Research\\PlotA", "biomass": 4500},
    {"name": "Plot_B", "folder": "C:\\Research\\PlotB", "biomass": 3800},
    {"name": "Plot_C", "folder": "C:\\Research\\PlotC", "biomass": 5200}
]

results = []

for survey in surveys:
    # Process with Chloros
    chloros.create_project(survey['name'])
    chloros.import_images(survey['folder'])
    chloros.configure(indices=["NDVI", "NDRE"])
    chloros.process(mode="parallel", wait=True)
    
    # Get results
    config = chloros.get_config()
    
    # Extract NDVI values (example - adjust based on your needs)
    # In real implementation, you would read the processed TIFF files
    
    results.append({
        'plot': survey['name'],
        'biomass': survey['biomass'],
        # Add your NDVI extraction here
    })

# Statistical analysis
df = pd.DataFrame(results)
print("\nResults:")
print(df)

# Create correlation plot
# plt.scatter(df['ndvi'], df['biomass'])
# plt.xlabel('NDVI')
# plt.ylabel('Biomass (kg/ha)')
# plt.title('NDVI vs Biomass Correlation')
# plt.show()
```

***

### 5. piemērs: Pielāgota procesa gaitu uzraudzība

Uzlabota procesa gaitu uzraudzība ar žurnāla reģistrēšanu:

```python
from chloros_sdk import ChlorosLocal
from datetime import datetime
import logging

# Setup logging
logging.basicConfig(
    filename=f'processing_{datetime.now():%Y%m%d_%H%M%S}.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)

# Progress callback with logging
def log_progress(progress, message):
    log_msg = f"[{progress}%] {message}"
    logging.info(log_msg)
    print(log_msg)

# Process with logging
chloros = ChlorosLocal()
chloros.create_project("LoggedProcess")
chloros.import_images("C:\\DroneImages")
chloros.configure(indices=["NDVI", "NDRE"])

logging.info("Starting processing...")
chloros.process(
    mode="parallel",
    progress_callback=log_progress,
    wait=True
)
logging.info("Processing complete!")
```

***

### 6. piemērs: Kļūdu apstrāde

Robusta kļūdu apstrāde ražošanas vajadzībām:

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import (
    ChlorosError,
    ChlorosBackendError,
    ChlorosLicenseError,
    ChlorosProcessingError
)

def process_safely(folder_path):
    """Process with comprehensive error handling"""
    try:
        with ChlorosLocal() as chloros:
            chloros.create_project("SafeProcess")
            chloros.import_images(folder_path)
            chloros.configure(indices=["NDVI"])
            chloros.process()
            
        return True, "Success"
    
    except ChlorosLicenseError as e:
        return False, f"License error: {e}. Upgrade to Chloros+ at cloud.mapir.camera/pricing"
    
    except ChlorosBackendError as e:
        return False, f"Backend error: {e}. Ensure Chloros is installed (Windows installer or Linux .deb package)."
    
    except ChlorosProcessingError as e:
        return False, f"Processing error: {e}"
    
    except FileNotFoundError as e:
        return False, f"Folder not found: {e}"
    
    except ChlorosError as e:
        return False, f"Chloros error: {e}"
    
    except Exception as e:
        return False, f"Unexpected error: {e}"

# Use the safe function
success, message = process_safely("C:\\DroneImages\\Flight001")
if success:
    print(f"✓ {message}")
else:
    print(f"✗ {message}")
```

***

### 7. piemērs: Kontu pārvaldība un iziet no sistēmas

Pārvaldiet autentifikācijas datus ar programmēšanas palīdzību:

```python
from chloros_sdk import ChlorosLocal

def switch_account():
    """Clear credentials to switch to a different account"""
    try:
        chloros = ChlorosLocal()
        
        # Clear current credentials
        result = chloros.logout()
        print("✓ Credentials cleared successfully")
        print("Please log in with new account via Chloros, Chloros (Browser), or CLI")
        
        return True
    
    except Exception as e:
        print(f"✗ Logout failed: {e}")
        return False

def secure_cleanup():
    """Remove credentials for security purposes"""
    try:
        chloros = ChlorosLocal()
        chloros.logout()
        print("✓ Credentials removed for security")
        
    except Exception as e:
        print(f"Warning: Cleanup error: {e}")

# Switch accounts
if switch_account():
    print("\nRe-authenticate via Chloros GUI/CLI/Browser before next SDK use")

# Or perform secure cleanup
# secure_cleanup()
```

***

### 8. piemērs: Komandrindas rīks

Izveidojiet pielāgotu CLI rīku ar SDK:

```python
#!/usr/bin/env python
"""
Custom Chloros CLI Tool
Process multiple folders from command line
"""

import sys
import argparse
from pathlib import Path
from chloros_sdk import process_folder

def main():
    parser = argparse.ArgumentParser(description='Custom Chloros Processor')
    parser.add_argument('folders', nargs='+', help='Folders to process')
    parser.add_argument('--indices', nargs='+', default=['NDVI'],
                       help='Indices to calculate (default: NDVI)')
    parser.add_argument('--camera', default=None,
                       help='Camera template')
    parser.add_argument('--format', default='TIFF (16-bit)',
                       help='Export format')
    parser.add_argument('--logout', action='store_true',
                       help='Clear cached credentials before processing')
    
    args = parser.parse_args()
    
    # Handle logout if requested
    if args.logout:
        from chloros_sdk import ChlorosLocal
        chloros = ChlorosLocal()
        chloros.logout()
        print("Credentials cleared. Please re-login via Chloros GUI/CLI/Browser.")
        return 0
    
    successful = []
    failed = []
    
    for folder in args.folders:
        folder_path = Path(folder)
        
        if not folder_path.exists():
            print(f"✗ Skipping {folder}: not found")
            failed.append(folder)
            continue
        
        print(f"\nProcessing: {folder_path.name}...")
        
        try:
            process_folder(
                folder_path,
                camera=args.camera,
                indices=args.indices,
                export_format=args.format
            )
            print(f"✓ {folder_path.name} complete")
            successful.append(folder)
        
        except Exception as e:
            print(f"✗ {folder_path.name} failed: {e}")
            failed.append(folder)
    
    # Summary
    print(f"\n{'='*60}")
    print(f"Summary: {len(successful)} successful, {len(failed)} failed")
    
    return 0 if not failed else 1

if __name__ == '__main__':
    sys.exit(main())
```

**Lietošana:**

```bash
# Process multiple folders
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI

# Clear cached credentials
python my_processor.py --logout
```

***

## Izņēmumu apstrāde

SDK nodrošina specifiskas izņēmumu klases dažādiem kļūdu veidiem:

### Izņēmumu hierarhija

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### Izņēmumu piemēri

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros is installed (Windows installer or Linux .deb package).")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## Papildu tēmas

### Pielāgota aizmugures konfigurācija

Izmantojiet pielāgotu aizmugures atrašanās vietu vai konfigurāciju:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### Ne bloķējoša apstrāde

Sāciet apstrādi un turpiniet ar citām uzdevumiem:

```python
# Start processing (non-blocking)
chloros.process(wait=False)

# Do other work here...
print("Processing started in background...")

# Check status later
import time
while True:
    status = chloros.get_config()
    if status.get('processing_complete'):
        break
    time.sleep(5)

print("Processing complete!")
```

### Atmiņas pārvaldība

Lieliem datu kopumiem apstrādājiet pa daļām:

```python
from pathlib import Path

base_folder = Path("C:\\LargeDataset")
batch_size = 100

# Get all image files
images = list(base_folder.glob("*.RAW"))

# Process in batches
for i in range(0, len(images), batch_size):
    batch = images[i:i+batch_size]
    batch_folder = base_folder / f"batch_{i//batch_size}"
    
    # Create batch folder and move images
    # ... (implementation details)
    
    # Process batch
    process_folder(batch_folder)
```

***

## Problēmu novēršana

### Backend nepalaižas

**Problēma:** SDK nevar palaist backend**Risinājumi:**

1. Pārbaudiet, vai ir instalēts Chloros:

```python
import os
import platform

# Auto-detect backend path
if platform.system() == "Windows":
    backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
else:
    backend_path = "/usr/lib/chloros/chloros-backend"

print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Pārbaudiet ugunsmūri (Windows) vai porta pieejamību (Linux: `lsof -i :5000`)
3. Izmēģiniet manuālo backend ceļu:

```python
# Windows
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")

# Linux
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")
```

***

### Licence nav atpazīta**Problēma:** SDK brīdina par trūkstošu licenci**Risinājumi:**

1. Atveriet Chloros, Chloros (pārlūks) vai Chloros CLI un piesakieties.
2. Pārbaudiet, vai licence ir saglabāta kešatmiņā:

```python
from pathlib import Path
import os
import platform

# Check cache location
if platform.system() == "Windows":
    cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
else:
    cache_path = Path.home() / '.cache' / 'chloros'

print(f"Cache exists: {cache_path.exists()}")
```

3. Ja rodas problēmas ar autentifikācijas datiem, izdzēsiet kešatmiņā saglabātos autentifikācijas datus un pieteikties atkārtoti:

```python
from chloros_sdk import ChlorosLocal

# Clear cached credentials
chloros = ChlorosLocal()
chloros.logout()

# Then login again via Chloros, Chloros (Browser), or Chloros CLI
```

4. Sazinieties ar atbalsta dienestu: info@mapir.camera

***

### Importēšanas kļūdas**Problēma:** `ModuleNotFoundError: No module named 'chloros_sdk'`**Risinājumi:**

```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```

***

### Apstrādes laika limits**Problēma:** Apstrādes laika limits ir beidzies**Risinājumi:**

1. Palieliniet laika limitu:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Apstrādājiet mazākas partijas
3. Pārbaudiet pieejamo diska vietu
4. Uzraugiet sistēmas resursus

***

### Ports jau tiek izmantots**Problēma:** Aizmugures ports 5000 ir aizņemts**Risinājumi:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Vai atrodiet un aizveriet konfliktējošo procesu:

```powershell
# Windows PowerShell
Get-NetTCPConnection -LocalPort 5000
```

```bash
# Linux
lsof -i :5000
kill $(lsof -t -i :5000)
```

***

## Padomi par veiktspēju

### Optimizējiet apstrādes ātrumu

1. **Izmantojiet paralēlo režīmu** (nepieciešams Chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **Samaziniet izvades izšķirtspēju** (ja tas ir pieņemami)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **Atvienojiet nevajadzīgos indeksus**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **Apstrādājiet uz SSD** (nevis HDD)***

### Atmiņas optimizācija

Lieliem datu kopumiem:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### Apstrāde fonā

Atbrīvojiet Python citiem uzdevumiem:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## Integrācijas piemēri

### Django integrācija

```python
# views.py
from django.http import JsonResponse
from chloros_sdk import process_folder

def process_images_view(request):
    if request.method == 'POST':
        folder_path = request.POST.get('folder_path')
        
        try:
            results = process_folder(folder_path)
            return JsonResponse({'success': True, 'results': results})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})
```

### Flask API

```python
# app.py
from flask import Flask, request, jsonify
from chloros_sdk import process_folder

app = Flask(__name__)

@app.route('/api/process', methods=['POST'])
def process():
    data = request.get_json()
    folder_path = data.get('folder_path')
    
    try:
        results = process_folder(folder_path)
        return jsonify({'success': True, 'results': results})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

if __name__ == '__main__':
    app.run()
```

### Jupyter Notebook

```python
# notebook.ipynb
from chloros_sdk import ChlorosLocal
import matplotlib.pyplot as plt

# Initialize
chloros = ChlorosLocal()

# Process
chloros.create_project("JupyterTest")
chloros.import_images("C:\\Data")
chloros.configure(indices=["NDVI"])

# Progress in notebook
from IPython.display import clear_output

def notebook_progress(progress, message):
    clear_output(wait=True)
    print(f"Progress: {progress}%")
    print(message)

chloros.process(progress_callback=notebook_progress)

# Visualize results
# ... (your visualization code)
```

***

## Bieži uzdotie jautājumi

### J: Vai SDK ir nepieciešams interneta savienojums?

**A:** Tikai sākotnējai licences aktivizēšanai. Pēc ieejas sistēmā, izmantojot Chloros, Chloros (pārlūks) vai Chloros CLI, licence tiek saglabāta vietējā cache un darbojas bezsaistē 30 dienas.***

### J: Vai varu izmantot SDK serverī bez grafiskās lietotāja saskarnes?**A:** Jā! SDK darbojas bez grafiskās saskarnes gan Windows, gan Linux serveros.**Linux (ieteicams bez grafiskās saskarnes):**
* Instalējiet, izmantojot `.deb` paketi
* Aktivizējiet licenci: `chloros-cli login user@example.com 'password'`

**Windows Server:**
* Windows Server 2016 vai jaunāka versija
* Chloros instalēts (vienreiz)
* Licence aktivizēta ar CLI vai jebkurā datorā

***

### J: Kāda ir atšķirība starp Desktop, CLI un SDK?

| Funkcija         | Desktop GUI | CLI Komandu rinda | Python SDK  |
| --------------- | ----------- | ---------------- | ----------- |
| **Saskarnes veids**   | Punktu-klikšķis | Komandas          | Python API  |
| **Vispiemērotākais**    | Vizuāls darbs | Skriptēšana        | Integrācija |
| **Automatizācija**  | Ierobežota     | Laba             | Izcila   |
| **Elastība** | Pamata       | Laba             | Maksimāla     |
| **Licence**     | Chloros+    | Chloros+         | Chloros+    |***

### J: Vai es varu izplatīt lietotnes, kas izstrādātas ar SDK?**A:** SDK kodu var integrēt jūsu lietotnēs, bet:

* Galalietotājiem ir jābūt instalētam Chloros
* Galalietotājiem ir nepieciešamas aktīvas Chloros+ licences
* Komerciālai izplatīšanai ir nepieciešama OEM licence

Sazinieties ar info@mapir.camera, ja Jums ir jautājumi par OEM.

***

### J: Kā atjaunināt SDK?

```bash
pip install --upgrade chloros-sdk
```

***

### J: Kur tiek saglabāti apstrādātie attēli?

Pēc noklusējuma — projekta ceļā:

```

Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### J: Vai varu apstrādāt attēlus no Python skriptiem, kas darbojas saskaņā ar grafiku?**A:** Jā! Izmantojiet savas operētājsistēmas plānotāju ar Python skriptiem:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("/data/flights/today")  # Linux
# results = process_folder("C:\\Flights\\Today")  # Windows
```

**Windows:** Plānojiet izpildi katru dienu, izmantojot uzdevumu plānotāju.**Linux:** Plānojiet izpildi, izmantojot cron:

```cron
# Run at 2 AM daily
0 2 * ** /usr/bin/python3 /home/user/scheduled_processing.py >> /var/log/chloros.log 2>&1
```

***

### J: Vai SDK atbalsta async/await?**A:** Pašreizējā versija ir sinhronā. Lai izmantotu asinhrono darbību, izmantojiet `wait=False` vai palaidiet atsevišķā pavedienā:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

### J: Kā pārslēgties starp dažādiem Chloros+ kontiem?**A:** Izmantojiet `logout()` metodi, lai dzēstu kešēto autentifikācijas informāciju, pēc tam atkārtoti piesakieties ar jauno kontu:

```python
from chloros_sdk import ChlorosLocal

# Clear current credentials
chloros = ChlorosLocal()
chloros.logout()

# Re-login via Chloros, Chloros (Browser), or Chloros CLI with new account
```

Pēc iziet no sistēmas, autentificējieties ar jauno kontu, izmantojot GUI, pārlūku vai CLI, pirms atkārtoti izmantojat SDK.

***

## Palīdzības saņemšana

### Dokumentācija

* **API atsauces**: Šī lapa

### Atbalsta kanāli

* **E-pasts**: info@mapir.camera
* **Tīmekļa vietne**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Cenas**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Parauga kods

Visi šeit uzskaitītie piemēri ir pārbaudīti un gatavi lietošanai. Kopējiet un pielāgojiet tos savām vajadzībām.

***

## Licence**Autortiesību aizsargāta programmatūra** - Autortiesības (c) 2025 MAPIR Inc.

SDK prasa aktīvu Chloros+ abonementu. Neatļauta izmantošana, izplatīšana vai modificēšana ir aizliegta.
