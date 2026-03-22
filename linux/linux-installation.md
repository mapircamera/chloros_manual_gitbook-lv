# Linux instalēšana

Chloros tiek izplatīts Linux vajadzībām kā `.deb` paketes, kas instalē CLI un backend. Python SDK tiek instalēts atsevišķi, izmantojot pip.

***

## Linux amd64 (x86_64)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Distribūcija** | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+ |
| **Procesors** | x86_64 (Intel/AMD) | Intel Core i7 vai labāks |
| **Atmiņa (RAM)** | 8 GB | 16 GB vai vairāk |
| **Grafikas karte** | Nav nepieciešama (apstrāde ar procesoru) | NVIDIA GPU ar 4 GB+ VRAM |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | SSD ar 10 GB+ brīvās vietas |
| **Python** | Python 3.7+ (SDK) | Python 3.10+ |

### Instalēšana

Lejupielādējiet `.deb` paketi un instalējiet:

```bash
sudo dpkg -i chloros-amd64.deb
```

Pārbaudiet instalāciju:

```bash
chloros-cli --version
```

***

## Linux arm64 (NVIDIA Jetson)

### Sistēmas prasības

| Prasība | Minimālā | Ieteicamā |
| --- | --- | --- |
| **Platforma** | NVIDIA Jetson ar JetPack 6 | Jetson Orin NX 16 GB vai AGX Orin |
| **JetPack** | JetPack 6.x | Jaunākā JetPack 6 versija |
| **Atmiņa (RAM)** | 8 GB (kopīga GPU/CPU) | 16 GB+ kopīga |
| **Uzglabāšanas vieta** | 2 GB brīvās vietas | NVMe SSD ar 10 GB+ brīvas vietas |
| **Python** | Python 3.7+ (SDK) | Python 3.10+ |

### Instalēšana

Lejupielādējiet JetPack 6 `.deb` paketi un instalējiet:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

Pārbaudiet instalāciju:

```bash
chloros-cli --version
```

Sīkāku informāciju par Jetson konfigurēšanu, tostarp siltuma vadību un izvietošanu laukā, skatiet [NVIDIA Jetson rokasgrāmatā](nvidia-jetson-guide.md).

***

## Python SDK instalācija (visi Linux)

Python SDK tiek instalēts atsevišķi, izmantojot pip, un darbojas gan amd64, gan arm64 vidē:

```bash
pip install chloros-sdk
```

Lai iekļautu papildu atbalstu progresīvās straumēšanas funkcijai:

```bash
pip install chloros-sdk[progress]
```

Pārbaudiet SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` pakete instalē Chloros, CLI un backend. Python SDK ir atsevišķs pip pakotne, kas sazinās ar backend caur vietējo HTTP API.
{% endhint %}

***

## Konfigurācijas direktoriji

Chloros uz Linux atbilst [XDG pamatkataloga specifikācijai](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html):

| Mērķis | Linux Celiņš | Windows Ekvivalents |
| --- | --- | --- |
| **Konfigurācija** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **Dati / Projekti** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **Cache / Credentials** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## Backend izpildāmo failu atrašanās vietas

`.deb` pakete instalē backend standarta atrašanās vietā. CLI un SDK automātiski nosaka backend ceļu:

| Instalēšanas metode | Backend ceļš |
| --- | --- |
| `.deb` pakete | `/usr/lib/chloros/chloros-backend` |
| Manuāli / pēc izvēles | `/opt/mapir/chloros/backend/chloros-backend` |

Jūs varat pārrakstīt backend ceļu, izmantojot `--backend-exe` CLI karodziņu vai `backend_exe` SDK konstruktora parametru.

***

## Pirmā uzstādīšana

### 1. Aktivizējiet savu licenci

Lai piekļūtu CLI un SDK, ir nepieciešama Chloros+ licence:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. Pārbaudiet savas licences statusu

```bash
chloros-cli status
```

### 3. Apstrādājiet savu pirmo datu kopu

```bash
chloros-cli process ~/datasets/flight001
```

### 4. Palaižiet sistēmas diagnostiku

Pārbaudiet, vai jūsu sistēma ir konfigurēta pareizi:

```bash
chloros-cli selftest
```

Tiek veikti 7 diagnostikas pārbaudes, tostarp versijas, backend palaišanas, API savienojamības un CUDA/GPU pieejamības pārbaudes.

***

## Bash skriptu piemēri

### Apstrādājiet vairākus datu kopumus

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### Apstrādājiet ar pielāgotiem iestatījumiem

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### Automātiska apstrāde ar Cron

Pievienojiet savam crontab (`crontab -e`), lai automātiski apstrādātu jaunus datu kopumus:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK piemērs

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Problēmu novēršana

### CLI nav atrasts pēc instalēšanas

Ja `chloros-cli` nav atrasts pēc `.deb` paketes instalēšanas:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### Atļauja atteikta

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### Neizdevās palaist backend

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### CUDA nav atklāts

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### Trūkst koplietošanas bibliotēku

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## Chloros atjaunināšana uz Linux

Izmantojiet iebūvēto atjaunināšanas komandu, lai pārbaudītu un instalētu atjauninājumus:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## Turpmākie soļi

* [NVIDIA Jetson rokasgrāmata](nvidia-jetson-guide.md) — Jetson specifiska optimizācija un ieviešana
* [CLI : Komandu rinda](../CLI.md) — Pilna CLI komandu atsauce
* [API : Python SDK](../api-python-sdk.md) — Pilna SDK atsauce
* [Dinamiskā aprēķinu pielāgošana](../processing-architecture/dynamic-compute-adaptation.md) — Kā Chloros pielāgojas jūsu aparatūrai
