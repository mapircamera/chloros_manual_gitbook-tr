# API : Python SDK

**Chloros Python SDK** , Chloros görüntü işleme motoruna programlı erişim sağlayarak otomasyon, özel iş akışları ve Python uygulamalarınızla ve araştırma süreçlerinizle sorunsuz entegrasyon imkanı sunar.

### Temel Özellikler

* 🐍 **Yerel Python** - Görüntü işleme için temiz, Python tarzı API
* 🔧 **Tam API Erişimi** - Chloros işleme üzerinde tam kontrol
* 🚀 **Otomasyon** - Özel toplu işleme iş akışları oluşturun
* 🔗 **Entegrasyon** - Chloros&#x27;i mevcut Python uygulamalarına gömün
* 📊 **Araştırmaya Hazır** - Bilimsel analiz iş akışları için mükemmel
* ⚡ **Paralel İşleme** - CPU çekirdeklerinize göre ölçeklenir (Chloros+)

### Gereksinimler

| Gereksinim          | Ayrıntılar                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **Chloros Yüklü** | Windows: Masaüstü yükleyici; Linux: `.deb` paketi                  |
| **Lisans**          | Chloros+ ([ücretli plan gereklidir](https://cloud.mapir.camera/pricing)) |
| **İşletim Sistemi** | Windows 10/11 (64-bit), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Python**           | Python 3.7 veya üstü                                                |
| **Bellek**           | En az 8 GB RAM (16 GB önerilir)                                  |
| **İnternet**         | Lisans etkinleştirme için gereklidir                                     |

{% hint style="warning" %}
**Lisans Gereksinimi**: Python SDK, API erişimi için ücretli bir Chloros+ aboneliği gerektirir. Standart (ücretsiz) planlarda API/SDK erişimi yoktur. Yükseltme yapmak için [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) adresini ziyaret edin.
{% endhint %}

## Hızlı Başlangıç

### Kurulum

pip ile kurun:

```bash
pip install chloros-sdk
```

{% hint style="info" %}
**İlk Kurulum**: SDK&#x27;i kullanmadan önce, Chloros+ lisansınızı etkinleştirmek için Chloros, Chloros (Tarayıcı) veya Chloros CLI&#x27;i açıp kimlik bilgilerinizi kullanarak oturum açarak etkinleştirin. Bu işlem sadece bir kez yapılmalıdır. Linux&#x27;te (GUI yok), şunu kullanın: `chloros-cli login user@example.com 'password'`
{% endhint %}

### Temel Kullanım

Sadece birkaç satırla bir klasörü işleyin:

```python
from chloros_sdk import process_folder

# One-line processing (Windows)
results = process_folder("C:\\DroneImages\\Flight001")

# One-line processing (Linux)
results = process_folder("/home/user/drone_images/flight001")
```

{% hint style="info" %}
**Çapraz Platform Yolları**: Bu sayfadaki kod örnekleri, Windows tarzı yolları kullanır (ör. `C:\\DroneImages\\Flight001`). Linux&#x27;te bunun yerine Linux yollarını kullanın (ör. `/home/user/drone_images/flight001` veya `~/drone_images/flight001`). SDK her iki platformda da aynı şekilde çalışır.
{% endhint %}

### Tam Kontrol

Gelişmiş iş akışları için:

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

## Kurulum Kılavuzu

### Ön Koşullar

SDK&#x27;i kurmadan önce şunlara sahip olduğunuzdan emin olun:

1. **Chloros yüklü** — Windows: Masaüstü yükleyicisi ([indirme](download.md)); Linux: `.deb` paketi ([Linux Kurulumu](linux/linux-installation.md))
2. **Python 3.7+** yüklü ([python.org](https://www.python.org))
3. **Etkin Chloros+ lisansı** ([yükseltme](https://cloud.mapir.camera/pricing))

### pip ile yükleme

**Standart yükleme:**

```bash
pip install chloros-sdk
```

**İlerleme izleme desteği ile:**

```bash
pip install chloros-sdk[progress]
```

**Geliştirme kurulumu:**

```bash
pip install chloros-sdk[dev]
```

### Kurulumu Doğrulama

SDK&#x27;in doğru şekilde kurulduğunu test edin:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## İlk Kurulum

### Lisans Etkinleştirme

SDK, Chloros, Chloros (Tarayıcı) ve Chloros CLI ile aynı lisansı kullanır. GUI veya CLI aracılığıyla bir kez etkinleştirin:**Windows:** **Chloros veya Chloros (Tarayıcı)**&#x27;ı açın ve Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> sekmesinden oturum açın veya CLI&#x27;i kullanın.**Linux:** CLI&#x27;i kullanın (GUI mevcut değildir):

```bash
chloros-cli login user@example.com 'your_password'
```

Lisans yerel olarak önbelleğe alınır ve yeniden başlatmalarda kalıcıdır.

{% hint style="success" %}
**Tek Seferlik Kurulum**: GUI veya CLI aracılığıyla oturum açtıktan sonra, SDK önbelleğe alınmış lisansı otomatik olarak kullanır. Ek kimlik doğrulama gerekmez!
{% endhint %}

{% hint style="info" %}
**Oturumu Kapatma**: SDK kullanıcıları, `logout()` yöntemini kullanarak önbelleğe alınmış kimlik bilgilerini programlı olarak silebilir. API Referansı&#x27;ndaki [logout() yöntemi](#logout) bölümüne bakın.
{% endhint %}

### Bağlantıyı Test Etme

SDK&#x27;in Chloros&#x27;e bağlanabildiğini doğrulayın:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## API Referansı

### ChlorosLocal Sınıfı

Yerel Chloros görüntü işleme için ana sınıf.

#### Yapıcı

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**Parametreler:**

| Parametre                 | Tür | Varsayılan                   | Açıklama                           |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `api_url`                 | str  | `"http://localhost:5000"` | Yerel Chloros arka ucunun URL&#x27;i          |
| `auto_start_backend`      | bool | `True`                    | Gerekirse arka ucu otomatik olarak başlat |
| `backend_exe`             | str  | `None` (otomatik algılama)      | Arka uç yürütülebilir dosyasının yolu            |
| `timeout`                 | int  | `30`                      | İstek zaman aşımı (saniye)            |
| `backend_startup_timeout` | int  | `60`                      | Arka uç başlatma zaman aşımı (saniye) |

**Örnekler:**

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
**Çapraz platform otomatik algılama**: SDK, platformunuz için doğru arka uç yolunu otomatik olarak dener:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (manuel)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

***

### Yöntemler

#### `create_project(project_name, camera=None)`

Yeni bir Chloros projesi oluşturun.

**Parametreler:**

| Parametre      | Tür | Gerekli | Açıklama                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `project_name` | str  | Evet      | Projenin adı                                     |
| `camera`       | str  | Hayır       | Kamera şablonu (ör. &quot;Survey3N\_RGN&quot;, &quot;Survey3W\_OCN&quot;) |

**Dönüş:** `dict` - Proje oluşturma yanıtı**Örnek:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images(folder_path, recursive=False)`

Bir klasörden görüntüleri içe aktarın.

**Parametreler:**

| Parametre     | Tür     | Gerekli | Açıklama                        |
| ------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | str/Yol | Evet      | Görüntülerin bulunduğu klasörün yolu         |
| `recursive`   | bool     | Hayır       | Alt klasörleri ara (varsayılan: False) |

**Döndürür:** `dict` - Dosya sayısı ile birlikte içe aktarma sonuçları**Örnek:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure(**settings)`

İşleme ayarlarını yapılandırır.

**Parametreler:**

| Parametre                 | Tür | Varsayılan                 | Açıklama                     |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `debayer`                 | str  | &quot;Standart (Hızlı, Orta Kalite)&quot; | Debayer yöntemi            |
| `vignette_correction`     | bool | `True`                  | Vinyet düzeltmesini etkinleştir      |
| `reflectance_calibration` | bool | `True`                  | Yansıma kalibrasyonunu etkinleştir  |
| `indices`                 | liste | `None`                  | Hesaplanacak bitki örtüsü indeksleri |
| `export_format`           | str  | &quot;TIFF (16-bit)&quot;         | Çıktı formatı                   |
| `ppk`                     | bool | `False`                 | PPK düzeltmelerini etkinleştir          |
| `custom_settings`         | dict | `None`                  | Gelişmiş özel ayarlar        |

**Dışa Aktarım Biçimleri:**

* `"TIFF (16-bit)"` - GIS/fotogrametri için önerilir
* `"TIFF (32-bit, Percent)"` - Bilimsel analiz
* `"PNG (8-bit)"` - Görsel inceleme
* `"JPG (8-bit)"` - Sıkıştırılmış çıktı

**Kullanılabilir Dizinler:**NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 ve daha fazlası.**Örnek:**

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

Proje görüntülerini işleyin.

**Parametreler:**

| Parametre           | Tür     | Varsayılan      | Açıklama                               |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `mode`              | str      | `"parallel"` | İşleme modu: &quot;parallel&quot; veya &quot;serial&quot;   |
| `wait`              | bool     | `True`       | Tamamlanana kadar bekle                       |
| `progress_callback` | çağrılabilir | `None`       | İlerleme geri arama işlevi (progress, msg) |
| `poll_interval`     | float    | `2.0`        | İlerleme için yoklama aralığı (saniye)   |

**Döndürür:** `dict` - İşleme sonuçları

{% hint style="warning" %}
**Paralel Mod**: Chloros+ lisansı gerektirir. CPU çekirdeklerinize göre otomatik olarak ölçeklenir (en fazla 16 işçi).
{% endhint %}

**Örnek:**

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

Mevcut proje yapılandırmasını alır.

**Döndürür:** `dict` - Mevcut proje yapılandırması**Örnek:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

İş parçacığı başına işleme ilerlemesi dahil olmak üzere arka uç durum bilgilerini alır.

**Döndürür:** `dict` - Aşağıdaki yapıya sahip arka uç durumu:

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

**Örnek:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
print(f"Processing: {status['processing']['percent']}%")
print(f"Export: {status['export']['percent']}% - Active: {status['export']['active']}")
```

***

#### `shutdown_backend()`

Arka ucu kapatır (SDK ile başlatılmışsa).

**Örnek:**

```python
chloros.shutdown_backend()
```

***

#### `logout()`

Yerel sistemden önbelleğe alınmış kimlik bilgilerini temizler.

**Açıklama:**

Önbelleğe alınmış kimlik bilgilerini kaldırarak programlı olarak oturumu kapatır. Bu, aşağıdakiler için kullanışlıdır:
* Farklı Chloros+ hesapları arasında geçiş yapmak
* Otomatik ortamlarda kimlik bilgilerini temizlemek
* Güvenlik amaçları (ör. kaldırma işleminden önce kimlik bilgilerini kaldırmak)

**Döndürür:** `dict` - Oturum kapatma işlemi sonucu**Örnek:**

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
**Yeniden Kimlik Doğrulama Gerekli**: `logout()` çağrısından sonra, Chloros, Chloros (Tarayıcı) veya Chloros CLI aracılığıyla yeniden oturum açmanız gerekir.
{% endhint %}

***

### Kolaylık İşlevleri

#### `process_folder(folder_path, **options)`

Bir klasörü işlemek için tek satırlık kolaylık işlevi.

**Parametreler:**

| Parametre                 | Tür     | Varsayılan         | Açıklama                    |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `folder_path`             | str/Yol | Gerekli        | Görüntülerin bulunduğu klasörün yolu     |
| `project_name`            | str      | Otomatik olarak oluşturulur  | Proje adı                   |
| `camera`                  | str      | `None`          | Kamera şablonu                |
| `indices`                 | list     | `["NDVI"]`      | Hesaplanacak indeksler           |
| `vignette_correction`     | bool     | `True`          | Vinyet düzeltmesini etkinleştir     |
| `reflectance_calibration` | bool     | `True`          | Yansıma kalibrasyonunu etkinleştir |
| `export_format`           | str      | &quot;TIFF (16-bit)&quot; | Çıktı biçimi                  |
| `mode`                    | str      | `"parallel"`    | İşleme modu                |
| `progress_callback`       | çağrılabilir | `None`          | İlerleme geri çağrısı              |

**Döndürür:** `dict` - İşleme sonuçları**Örnek:**

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

## Bağlam Yöneticisi Desteği

SDK, otomatik temizleme için bağlam yöneticilerini destekler:

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

## Tam Örnekler

{% hint style="info" %}
**Linux Kullanıcıları**: Aşağıdaki tüm örneklerde Windows yolları kullanılmaktadır. `C:\\...` yollarını kendi Linux yollarınızla değiştirin (ör. `/home/user/...` veya `~/...`). Tüm SDK işlevleri tüm platformlarda aynıdır.
{% endhint %}

### Örnek 1: Temel İşleme

Bir klasörü varsayılan ayarlarla işleyin:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### Örnek 2: Özel İş Akışı

İşleme boru hattı üzerinde tam kontrol:

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

### Örnek 3: Birden Fazla Klasörü Toplu İşleme

Birden fazla uçuş veri setini işleyin:

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

### Örnek 4: Araştırma Boru Hattı Entegrasyonu

Chloros&#x27;i veri analizi ile entegre etme:

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

### Örnek 5: Özel İlerleme İzleme

Günlük kaydı ile gelişmiş ilerleme takibi:

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

### Örnek 6: Hata Yönetimi

Üretim kullanımı için sağlam hata işleme:

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

### Örnek 7: Hesap Yönetimi ve Oturumu Kapatma

Kimlik bilgilerini programlı olarak yönetme:

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

### Örnek 8: Komut Satırı Aracı

SDK ile özel bir CLI aracı oluşturun:

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

**Kullanım:**

```bash
# Process multiple folders
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI

# Clear cached credentials
python my_processor.py --logout
```

***

## İstisna İşleme

SDK, farklı hata türleri için belirli istisna sınıfları sağlar:

### İstisna Hiyerarşisi

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### İstisna Örnekleri

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

## Gelişmiş Konular

### Özel Arka Uç Yapılandırması

Özel bir arka uç konumu veya yapılandırması kullanın:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### Engellemesiz İşleme

İşlemeyi başlatın ve diğer görevlere devam edin:

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

### Bellek Yönetimi

Büyük veri kümeleri için, toplu işler halinde işleyin:

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

## Sorun Giderme

### Arka Uç Başlamıyor

**Sorun:** SDK arka ucu başlatamıyor**Çözümler:**

1. Chloros&#x27;in kurulu olduğunu doğrulayın:

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

2. Güvenlik duvarını (Windows) veya bağlantı noktası kullanılabilirliğini (Linux: `lsof -i :5000`) kontrol edin
3. Manuel arka uç yolunu deneyin:

```python
# Windows
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")

# Linux
chloros = ChlorosLocal(backend_exe="/opt/mapir/chloros/backend/chloros-backend")
```

***

### Lisans Algılanmadı**Sorun:** SDK, eksik lisans konusunda uyarı veriyor**Çözümler:**

1. Chloros, Chloros (Tarayıcı) veya Chloros CLI&#x27;i açın ve oturum açın.
2. Lisansın önbelleğe alındığını doğrulayın:

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

3. Kimlik bilgisi sorunları yaşıyorsanız, önbelleğe alınmış kimlik bilgilerini temizleyin ve yeniden oturum açın:

```python
from chloros_sdk import ChlorosLocal

# Clear cached credentials
chloros = ChlorosLocal()
chloros.logout()

# Then login again via Chloros, Chloros (Browser), or Chloros CLI
```

4. Destek ekibiyle iletişime geçin: info@mapir.camera

***

### İçe Aktarma Hataları**Sorun:** `ModuleNotFoundError: No module named 'chloros_sdk'`**Çözümler:**

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

### İşlem Zaman Aşımı**Sorun:** İşlem zaman aşımına uğradı**Çözümler:**

1. Zaman aşımını artırın:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Daha küçük gruplar halinde işleyin
3. Kullanılabilir disk alanını kontrol edin
4. Sistem kaynaklarını izleyin

***

### Bağlantı Noktası Zaten Kullanılıyor**Sorun:** Arka uç bağlantı noktası 5000 meşgul**Çözümler:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Veya çakışan işlemi bulun ve kapatın:

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

## Performans İpuçları

### İşlem Hızını Optimize Edin

1. **Paralel Modu Kullanın** (Chloros+ gerektirir)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **Çıktı Çözünürlüğünü Düşürün** (kabul edilebilirse)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **Gereksiz Dizinleri Devre Dışı Bırakın**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **SSD&#x27;de İşleyin** (HDD değil)***

### Bellek Optimizasyonu

Büyük veri kümeleri için:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### Arka Plan İşleme

Diğer görevler için Python&#x27;i boşaltın:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## Entegrasyon Örnekleri

### Django Entegrasyonu

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

## SSS

### S: SDK için internet bağlantısı gerekli mi?

**C:** Yalnızca ilk lisans etkinleştirme için gereklidir. Chloros, Chloros (Tarayıcı) veya Chloros CLI üzerinden oturum açtıktan sonra lisans yerel olarak önbelleğe alınır ve 30 gün boyunca çevrimdışı olarak çalışır.***

### S: SDK&#x27;i GUI&#x27;siz bir sunucuda kullanabilir miyim?**C:** Evet! SDK, hem Windows hem de Linux sunucularında başsız olarak çalışır.**Linux (headless için önerilir):**
* `.deb` paketi ile yükleyin
* Lisansı etkinleştirin: `chloros-cli login user@example.com 'password'`

**Windows Sunucusu:**
* Windows Sunucu 2016 veya üstü
* Chloros yüklü (tek seferlik)
* Lisans, CLI aracılığıyla veya herhangi bir makinede etkinleştirilir

***

### S: Desktop, CLI ve SDK arasındaki fark nedir?

| Özellik         | Desktop GUI | CLI Komut Satırı | Python SDK  |
| --------------- | ----------- | ---------------- | ----------- |
| **Arayüz**   | Nokta-tıklama | Komut          | Python API  |
| **En Uygun Olduğu Alan**    | Görsel çalışma | Komut dosyası oluşturma        | Entegrasyon |
| **Otomasyon**  | Sınırlı     | İyi             | Mükemmel   |
| **Esneklik** | Temel       | İyi             | Maksimum     |
| **Lisans**     | Chloros+    | Chloros+         | Chloros+    |***

### S: SDK ile oluşturulan uygulamaları dağıtabilir miyim?**C:** SDK kodu uygulamalarınıza entegre edilebilir, ancak:

* Son kullanıcıların Chloros&#x27;i yüklemiş olması gerekir
* Son kullanıcıların aktif Chloros+ lisanslarına sahip olması gerekir
* Ticari dağıtım için OEM lisanslaması gerekir

OEM ile ilgili sorularınız için info@mapir.camera ile iletişime geçin.

***

### S: SDK&#x27;i nasıl güncelleyebilirim?

```bash
pip install --upgrade chloros-sdk
```

***

### S: İşlenmiş görüntüler nereye kaydedilir?

Varsayılan olarak, Proje Yolu&#x27;na:

```

Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### S: Zamanlanmış olarak çalışan Python komut dosyalarından görüntüleri işleyebilir miyim?**C:** Evet! Python komut dosyalarıyla işletim sisteminizin zamanlayıcısını kullanın:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("/data/flights/today")  # Linux
# results = process_folder("C:\\Flights\\Today")  # Windows
```

**Windows:** Görev Zamanlayıcı aracılığıyla günlük olarak çalışacak şekilde zamanlayın.**Linux:** Cron aracılığıyla zamanlayın:

```cron
# Run at 2 AM daily
0 2 * ** /usr/bin/python3 /home/user/scheduled_processing.py >> /var/log/chloros.log 2>&1
```

***

### S: SDK async/await&#x27;i destekliyor mu?**C:** Mevcut sürüm senkronize çalışır. Asenkron davranış için `wait=False`&#x27;i kullanın veya ayrı bir iş parçacığında çalıştırın:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

### S: Farklı Chloros+ hesapları arasında nasıl geçiş yapabilirim?**C:** Önbelleğe alınmış kimlik bilgilerini temizlemek için `logout()` yöntemini kullanın, ardından yeni hesapla yeniden oturum açın:

```python
from chloros_sdk import ChlorosLocal

# Clear current credentials
chloros = ChlorosLocal()
chloros.logout()

# Re-login via Chloros, Chloros (Browser), or Chloros CLI with new account
```

Oturumu kapattıktan sonra, SDK&#x27;i tekrar kullanmadan önce GUI, Tarayıcı veya CLI aracılığıyla yeni hesapla kimlik doğrulaması yapın.

***

## Yardım Alma

### Belgeler

* **API Referansı**: Bu sayfa

### Destek Kanalları

* **E-posta**: info@mapir.camera
* **Web Sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Fiyatlandırma**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### Örnek Kod

Burada listelenen tüm örnekler test edilmiş ve kullanıma hazırdır. Bunları kopyalayıp kullanım durumunuza göre uyarlayın.

***

## Lisans**Tescilli Yazılım** - Telif Hakkı (c) 2025 MAPIR Inc.

SDK, aktif bir Chloros+ aboneliği gerektirir. Yetkisiz kullanım, dağıtım veya değişiklik yasaktır.
