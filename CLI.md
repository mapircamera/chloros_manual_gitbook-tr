# CLI : Komut Satırı

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI**, Chloros görüntü işleme motoruna güçlü bir komut satırı erişimi sağlayarak, görüntüleme iş akışlarınız için otomasyon, komut dosyası oluşturma ve başsız çalışma imkanı sunar.

### Temel Özellikler

* 🚀 **Otomasyon** - Birden fazla veri kümesinin komut dosyası ile toplu işleme
* 🔗 **Entegrasyon** - Mevcut iş akışlarına ve boru hatlarına entegre edin
* 💻 **Görsel Arayüzsüz Çalışma** - GUI olmadan çalıştırın
* 🌍 **Çoklu Dil** - 38 dil desteği
* ⚡ **Paralel İşleme** - [Dinamik Hesaplama Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) donanımınız için otomatik olarak optimize eder

### Gereksinimler

| Gereksinim          | Ayrıntılar                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **İşletim Sistemi** | Windows 10/11 (64-bit), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **Lisans**          | Chloros+ ([ücretli plan gereklidir](https://cloud.mapir.camera/pricing)) |
| **Bellek**           | En az 8 GB RAM (16 GB önerilir)                                  |
| **İnternet**         | Lisans etkinleştirme için gereklidir                                     |
| **Disk Alanı**       | Proje boyutuna göre değişir                                              |

{% hint style="warning" %}
**Lisans Gereksinimi**: CLI için ücretli bir Chloros+ aboneliği gereklidir. Standart (ücretsiz) planlarda CLI erişimi yoktur. Yükseltme yapmak için [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) adresini ziyaret edin.
{% endhint %}

## Hızlı Başlangıç

### Kurulum

#### Windows

CLI, Chloros yükleyicisine otomatik olarak dahil edilmiştir:

1. **Chloros Installer.exe** dosyasını indirin ve çalıştırın
2. Kurulum sihirbazını tamamlayın
3. CLI şu konuma kuruldu: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style="success" %}
Yükleyici, `chloros-cli` dosyasını sistem PATH&#x27;inize otomatik olarak ekler. Yükleme tamamlandıktan sonra terminalinizi yeniden başlatın.
{% endhint %}

#### Linux

Mimarinize uygun `.deb` paketini yükleyin:

```bash
# Linux amd64
sudo dpkg -i chloros-amd64.deb

# Linux arm64 (NVIDIA Jetson, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Ayrıntılı Linux kurulumu için bkz. [Linux Kurulumu](linux/linux-installation.md).

### İlk Kurulum

CLI&#x27;i kullanmadan önce, Chloros+ lisansınızı etkinleştirin:

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

### Temel Kullanım

Bir klasörü varsayılan ayarlarla işleyin:

**Windows:**

```powershell
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
chloros-cli process ~/images/dataset001
```

***

## Komut Referansı

### Genel Sözdizimi

```
chloros-cli [global-options] <command> [command-options]
```

***

## Komutlar

### `process` - Görüntüleri İşleme

Bir klasördeki görüntüleri kalibrasyonla işleyin.

**Sözdizimi:**

```bash
chloros-cli process <input-folder> [options]
```

**Örnekler:**

```bash
# Windows
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance

# Linux
chloros-cli process ~/datasets/survey_001 --vignette --reflectance
```

#### İşleme Komutu Seçenekleri

| Seçenek                | Tür    | Varsayılan        | Açıklama                                                                            |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>`      | Yol    | _Gerekli_     | RAW/JPG multispektral görüntüleri içeren klasör                                         |
| `-o, --output`        | Yol    | Girişle aynı  | İşlenmiş görüntüler için çıktı klasörü                                                     |
| `-n, --project-name`  | Dize  | Otomatik olarak oluşturulur | Özel proje adı                                                                    |
| `--vignette`          | Bayrak    | Etkin        | Vinyet düzeltmesini etkinleştir                                                             |
| `--no-vignette`       | Bayrak    | -              | Vinyet düzeltmesini devre dışı bırak                                                            |
| `--reflectance`       | Bayrak    | Etkin        | Yansıma kalibrasyonunu etkinleştir                                                         |
| `--no-reflectance`    | Bayrak    | -              | Yansıma kalibrasyonunu devre dışı bırak                                                        |
| `--ppk`               | Bayrak    | Devre dışı       | .daq ışık sensörü verilerinden PPK düzeltmelerini uygula                                      |
| `--format`            | Seçim  | TIFF (16-bit)  | Çıktı biçimi: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Tamsayı | Otomatik           | Kalibrasyon paneli algılaması için minimum hedef boyutu (piksel cinsinden)                          |
| `--target-clustering` | Tamsayı | Otomatik           | Hedef kümelenme eşiği (0-100)                                                    |
| `--debayer`           | Seçim  | `standard`     | Debayer yöntemi: `standard` veya `texture-aware` (yalnızca Chloros+)                          |
| `--target`, `--targets` | Bayrak  | Devre dışı       | Yalnızca &quot;target&quot; veya &quot;targets&quot; alt klasörlerinde kalibrasyon hedeflerini ara (işlemeyi hızlandırır) |
| `--indices`           | Liste    | Yok           | Hesaplanacak bitki örtüsü indeksleri (örn., `--indices NDVI NDRE GNDVI`)                    |
| `--exposure-pin-1`    | Dize  | Yok           | Kamera modeli için pozlamayı kilitle (Pin 1)                                                 |
| `--exposure-pin-2`    | Dize  | Yok           | Kamera modeli için pozlamayı kilitle (Pin 2)                                                 |
| `--recal-interval`    | Tamsayı | Otomatik           | Saniye cinsinden yeniden kalibrasyon aralığı                                                      |
| `--timezone-offset`   | Tamsayı | 0              | Saat dilimi farkı (saat cinsinden)                                                               |

***

### `login` - Hesabı Doğrulama

CLI işlemesini etkinleştirmek için Chloros+ kimlik bilgilerinizi kullanarak oturum açın.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

**Örnek:**

```bash
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluklar içeren şifrelerin etrafına tek tırnak işareti koyun.
{% endhint %}

**Çıktı:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Kimlik Bilgilerini Sil

Kaydedilmiş kimlik bilgilerini silin ve hesabınızdan çıkış yapın.

**Sözdizimi:**

```bash
chloros-cli logout
```

**Örnek:**

```bash
chloros-cli logout
```

**Çıktı:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style="info" %}
**SDK Kullanıcıları**: Python SDK ayrıca, Python komut dosyaları içinde kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi de sağlar. Ayrıntılar için [Python SDK belgelerine](api-python-sdk.md#logout) bakın.
{% endhint %}

***

### `status` - Lisans Durumunu Kontrol Et

Mevcut lisans ve kimlik doğrulama durumunu görüntüler.

**Sözdizimi:**

```bash
chloros-cli status
```

**Örnek:**

```bash
chloros-cli status
```

**Çıktı:**

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

### `export-status` - Dışa Aktarım İlerleme Durumunu Kontrol Et

İşleme sırasında veya sonrasında Thread 4 dışa aktarım ilerleme durumunu izleyin.

**Sözdizimi:**

```bash
chloros-cli export-status
```

**Örnek:**

```bash
chloros-cli export-status
```

**Kullanım Durumu:** Dışa aktarma ilerlemesini kontrol etmek için işleme devam ederken bu komutu çağırın.***

### `language` - Arayüz Dilini Yönet

CLI arayüz dilini görüntüleyin veya değiştirin.

**Sözdizimi:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**Örnekler:**

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

#### Desteklenen Diller (Toplam 38)

| Kod    | Dil              | Yerel Adı      |
| ------- | --------------------- | ---------------- |
| `en`    | İngilizce               | English          |
| `es`    | İspanyolca               | Español          |
| `pt`    | Portekizce            | Português        |
| `fr`    | Fransızca                | Français         |
| `de`    | Almanca                | Deutsch          |
| `it`    | İtalyanca               | Italiano         |
| `ja`    | Japonca              | 日本語              |
| `ko`    | Korece                | 한국어              |
| `zh`    | Çince (Basitleştirilmiş)  | 简体中文             |
| `zh-TW` | Çince (Geleneksel) | 繁體中文             |
| `ru`    | Rusça               | Русский          |
| `nl`    | Felemenkçe                | Nederlands       |
| `ar`    | Arapça                | العربية          |
| `pl`    | Lehçe                | Polski           |
| `tr`    | Türkçe               | Türkçe           |
| `hi`    | Hintçe                 | हिंदी            |
| `id`    | Endonezce            | Bahasa Indonesia |
| `vi`    | Vietnamca            | Tiếng Việt       |
| `th`    | Tayca                  | ไทย              |
| `sv`    | İsveççe               | Svenska          |
| `da`    | Danca                | Dansk            |
| `no`    | Norveççe             | Norsk            |
| `fi`    | Fince               | Suomi            |
| `el`    | Yunanca                 | Ελληνικά         |
| `cs`    | Çek                 | Čeština          |
| `hu`    | Macarca             | Magyar           |
| `ro`    | Romence              | Română           |
| `uk`    | Ukraynaca             | Українська       |
| `pt-BR` | Brezilya Portekizcesi  | Português Brasileiro |
| `zh-HK` | Kantonca             | 粵語             |
| `ms`    | Malayca                 | Bahasa Melayu    |
| `sk`    | Slovakça                | Slovenčina       |
| `bg`    | Bulgarca             | Български        |
| `hr`    | Hırvatça              | Hrvatski         |
| `lt`    | Litvanyaca            | Lietuvių         |
| `lv`    | Letonca               | Latviešu         |
| `et`    | Estonca              | Eesti            |
| `sl`    | Slovence             | Slovenščina      |

{% hint style="success" %}
**Otomatik Kalıcılık**: Dil tercihinizi `~/.chloros/cli_language.json` dosyasına kaydedilir ve tüm oturumlarda kalıcıdır.
{% endhint %}

***

### `set-project-folder` - Varsayılan Proje Klasörünü Ayarla

Varsayılan proje klasörünün konumunu değiştirin (Windows&#x27;teki GUI ile paylaşılır).

**Sözdizimi:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Örnekler:**

```bash
# Windows
chloros-cli set-project-folder "C:\Projects\2025"

# Linux
chloros-cli set-project-folder ~/projects/2025
```

***

### `get-project-folder` - Proje Klasörünü Göster

Mevcut varsayılan proje klasörü konumunu görüntüler.

**Sözdizimi:**

```bash
chloros-cli get-project-folder
```

**Örnek:**

```bash
chloros-cli get-project-folder
```

**Çıktı:**

```

# Windows
ℹ Current project folder: C:\Projects\2025

# Linux
ℹ Current project folder: /home/user/.local/share/chloros/projects
```

***

### `reset-project-folder` - Varsayılana Sıfırla

Proje klasörünü varsayılan konuma sıfırlar.

**Sözdizimi:**

```bash
chloros-cli reset-project-folder
```

***

### `selftest` - Sistem Tanılama Çalıştır

Sistem yapılandırmanızı doğrulamak için 7 tanılama kontrolü çalıştırır.

**Sözdizimi:**

```bash
chloros-cli selftest
```

**Gerçekleştirilen tanılama işlemleri:**

1. Sürüm kontrolü
2. Bağlantı noktası kullanılabilirliği (5000)
3. Arka uç başlatma
4. API bağlantı testi
5. Sistem bilgileri ve GPU algılama
6. Gürültü giderici modellerinin doğrulanması
7. CUDA kullanılabilirlik kontrolü

{% hint style="info" %}
**Sorun giderme için yararlı**: Kurulumdan sonra `selftest`&#x27;i çalıştırarak sisteminizin doğru şekilde yapılandırıldığını doğrulayın; özellikle GPU ve CUDA kurulumunun doğrulanması gerekebilecek Linux/Jetson&#x27;da bunu yapın.
{% endhint %}

***

### `update` - Güncellemeleri Kontrol Et (Yalnızca Linux)

Linux sistemlerinde CLI güncellemelerini kontrol edin ve yükleyin.

**Sözdizimi:**

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

| Seçenek    | Açıklama                        |
| --------- | ---------------------------------- |
| `--check` | Yalnızca güncellemeleri kontrol et, yükleme |

{% hint style="info" %}
Bu komut yalnızca Linux&#x27;te kullanılabilir. Windows&#x27;te güncellemeler yükleyici aracılığıyla sağlanır.
{% endhint %}

***

## Genel Seçenekler

Bu seçenekler tüm komutlar için geçerlidir:

| Seçenek            | Tür    | Varsayılan       | Açıklama                                      |
| ----------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe`   | Yol    | Otomatik algılanır | Arka uç yürütülebilir dosyasının yolu                       |
| `--port`          | Tamsayı | 5000          | Arka uç API bağlantı noktası numarası                          |
| `--restart`       | Bayrak    | -             | Arka ucu zorla yeniden başlat (mevcut işlemleri sonlandırır) |
| `--version`       | Bayrak    | -             | Sürüm bilgilerini göster ve çık                |
| `--help`          | Bayrak    | -             | Yardım bilgilerini göster ve çık                   |

{% hint style="info" %}
**Arka uç otomatik algılama**: `--backend-exe` yolu platform başına otomatik olarak algılanır:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (manuel)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

**Global Seçeneklerle Örnek:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

**Linux:**

```bash
chloros-cli --port 5001 process ~/datasets/survey_001
```

***

## İşleme Ayarları Kılavuzu

### Paralel İşleme ve Dinamik Hesaplama Uyumlaştırma

Chloros 1.1.0 sürümü, [Dinamik Hesaplama Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) özelliğini içerir — işleme motoru **donanımınızı otomatik olarak algılar** ve en uygun stratejiyi seçer:

| Platform | Strateji | İşçiler | İş Akışı | Notlar |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | Bellek verimli, serileştirilmiş |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` | Eşzamanlı GPU işleme |
| **8 GB GPU&#x27;lu masaüstü** | `GPU_SINGLE` | 3 | `tiled_gpu` | İyi masaüstü performansı |
| **12 GB+ GPU&#x27;lu masaüstü** | `GPU_PARALLEL` | 3-4 | `fused_gpu` | Optimum masaüstü performansı |
| **Yalnızca CPU içeren sistem** | `CPU_PARALLEL` | çekirdek - 1 | `cpu_fallback` | GPU gerekmez |

{% hint style="success" %}
**Manuel yapılandırma gerekmez!** Chloros, CPU, GPU, RAM ve (Jetson&#x27;da) termal sensörlerinizi otomatik olarak algılar, ardından en uygun işleme boru hattını otomatik olarak yapılandırır.
{% endhint %}

### Debayer Yöntemleri

| Yöntem | CLI Bayrağı | Kalite | Hız | Lisans |
| --- | --- | --- | --- | --- |
| **Standart (Hızlı, Orta Kalite)** | `--debayer standard` | İyi | Hızlı | Ücretsiz / Chloros+ |
| **Doku Duyarlı (Yavaş, En Yüksek Kalite)** | `--debayer texture-aware` | En Yüksek | Yavaş | Yalnızca Chloros+ |

Varsayılan debayer yöntemi **Standart**&#x27;tır.**Tekstür Duyarlı** yöntemi, en yüksek kalitede çıktı elde etmek için bir AI/ML gürültü giderme modeli kullanır, ancak bir Chloros+ lisansı ve bir NVIDIA GPU gerektirir.

```bash
# Use Texture Aware debayer (Chloros+ only)
chloros-cli process ~/datasets/field_a --debayer texture-aware
```

### Vinyet Düzeltme

**Ne yapar:** Görüntü kenarlarında ışık düşüşünü düzeltir (kamera görüntülerinde sık görülen köşelerin koyulaşması).

* **Varsayılan olarak etkindir** - Çoğu kullanıcı bunu etkin tutmalıdır
* Devre dışı bırakmak için `--no-vignette` kullanın

{% hint style="success" %}
**Öneri**: Çerçeve genelinde eşit parlaklık sağlamak için vinyet düzeltmeyi her zaman etkinleştirin.
{% endhint %}

### Yansıma Kalibrasyonu

Kalibrasyon panellerini kullanarak ham sensör değerlerini standartlaştırılmış yansıma yüzdelerine dönüştürür.

* **Varsayılan olarak etkindir** - Bitki örtüsü analizi için gereklidir
* Görüntülerde kalibrasyon hedef panelleri gerektirir
* Devre dışı bırakmak için `--no-reflectance` kullanın

{% hint style="info" %}
**Gereksinimler**: Doğru yansıma dönüşümü için kalibrasyon panellerinin görüntülerinizde uygun şekilde pozlanmış ve görünür olduğundan emin olun.
{% endhint %}

### PPK Düzeltmeleri

**Ne yapar:** GPS doğruluğunu artırmak için DAQ-A-SD kayıt verilerini kullanarak Son İşlemli Kinematik düzeltmeleri uygular.

* **Varsayılan olarak devre dışıdır**
* Etkinleştirmek için `--ppk` kullanın
* MAPIR DAQ-A-SD ışık sensöründen proje klasöründe .daq dosyaları gerektirir.

### Çıktı Biçimleri

<table><thead><tr><th width="197">Biçim</th><th width="130.20001220703125">Bit Derinliği</th><th width="116.5999755859375">Dosya Boyutu</th><th>En Uygun</th></tr></thead><tbody><tr><td><strong>TIFF (16 bit)</strong> ⭐</td><td>16 bitlik tamsayı</td><td>Büyük</td><td>GIS analizi, fotogrametri (önerilir)</td></tr><tr><td><strong>TIFF (32 bit, Yüzde)</strong></td><td>32 bit kayan nokta</td><td>Çok büyük</td><td>Bilimsel analiz, araştırma</td></tr><tr><td><strong>PNG (8 bit)</strong></td><td>8 bitlik tamsayı</td><td>Orta</td><td>Görsel inceleme, web paylaşımı</td></tr><tr><td><strong>JPG (8 bit)</strong></td><td>8 bitlik tamsayı</td><td>Küçük</td><td>Hızlı önizleme, sıkıştırılmış çıktı</td></tr></tbody></table>***

## Otomasyon ve Komut Dosyası Oluşturma

### PowerShell Toplu İşleme (Windows)

Windows üzerinde birden fazla veri kümesi klasörünü otomatik olarak işleyin:

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

### Windows Toplu İşleme Komut Dosyası (Windows)

Windows üzerinde toplu işleme için basit döngü:

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

### Bash Toplu İşleme (Linux)

Linux üzerinde birden fazla veri kümesi klasörünü işleyin:

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

### Python Otomasyon Komut Dosyası (Çapraz Platform)

Hata işleme özelliğine sahip gelişmiş otomasyon (Windows ve Linux üzerinde çalışır):

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

## İşleme İş Akışı

### Standart İş Akışı

1. **Giriş**: RAW/JPG görüntü çiftlerini içeren klasör
2. **Keşif**: CLI, desteklenen görüntü dosyalarını otomatik olarak tarar
3. **İşleme**: Paralel mod, CPU çekirdeklerinize göre ölçeklenir (Chloros+)
4. **Çıktı**: İşlenmiş görüntüleri içeren kamera modeli alt klasörleri oluşturur

### Örnek Çıktı Yapısı

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

### İşleme Süresi Tahminleri

100 görüntü (her biri 12 MP) için tipik işleme süreleri:

| Platform | Mod | Tahmini Süre | Notlar |
| --- | --- | --- | --- |
| **Masaüstü 12 GB+ GPU** | `GPU_PARALLEL` | 5-10 dakika | En hızlı seçenek |
| **Masaüstü 8 GB GPU** | `GPU_SINGLE` | 10-15 dakika | İyi performans |
| **Jetson Orin 16 GB** | `GPU_PARALLEL` | 15-25 dakika | Kenar bilgi işlem |
| **Jetson Nano 8 GB** | `GPU_SINGLE` | 30-60 dakika | Bellek kısıtlı |
| **Yalnızca CPU** | `CPU_PARALLEL` | 20-40 dakika | GPU gerekmez |

{% hint style="info" %}
**Performans İpucu**: İşlem süresi, görüntü sayısı, çözünürlük, debayer yöntemi ve donanıma göre değişir. Texture Aware debayer, Standard&#x27;a göre önemli ölçüde daha uzun sürer. Ayrıntılar için [Dinamik Hesaplama Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) bölümüne bakın.
{% endhint %}

***

## Sorun Giderme

### CLI Bulunamadı

**Windows Hatası:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Windows Çözümler:**

1. Kurulum konumunu doğrulayın:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. PATH&#x27;te yoksa tam yolu kullanın:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. PATH&#x27;e manuel olarak ekleyin:
   * Sistem Özellikleri → Ortam Değişkenleri&#x27;ni açın
   * PATH değişkenini düzenleyin
   * Ekle: `C:\Program Files\Chloros\resources\cli`
   * Terminali yeniden başlatın

**Linux Hatası:**

```
chloros-cli: command not found
```

**Linux Çözümler:**

1. Kurulumu doğrulayın:

```bash
which chloros-cli
dpkg -L chloros-amd64  # or chloros-arm64-jp6
```

2. Kabuğunuzu yeniden yükleyin:

```bash
source ~/.bashrc
```

3. İzinleri kontrol edin:

```bash
sudo chmod +x /usr/bin/chloros-cli
```

***

### Arka Uç Başlatılamadı**Hata:**

```

Backend failed to start within 30 seconds
```

**Çözümler:**

1. Arka uçun zaten çalışıp çalışmadığını kontrol edin (önce kapatın)
2. Güvenlik duvarının engellemediğini kontrol edin (Windows) veya bağlantı noktasının kullanılabilirliğini kontrol edin (Linux: `lsof -i :5000`)
3. Farklı bir bağlantı noktası deneyin:

```bash
# Windows
chloros-cli --port 5001 process "C:\Datasets\Field_A"

# Linux
chloros-cli --port 5001 process ~/datasets/field_a
```

4. Arka ucu zorla yeniden başlatın:

```bash
# Windows
chloros-cli --restart process "C:\Datasets\Field_A"

# Linux
chloros-cli --restart process ~/datasets/field_a
```

5. Linux&#x27;te, arka uç yürütülebilir dosyasının mevcut olup olmadığını kontrol edin:

```bash
ls -la /usr/lib/chloros/chloros-backend
```

***

### Lisans / Kimlik Doğrulama Sorunları**Hata:**

```

Chloros+ license required for CLI access
```

**Çözümler:**

1. Aktif bir Chloros+ aboneliğiniz olduğunu doğrulayın
2. Kimlik bilgilerinizle giriş yapın:

```bash
chloros-cli login user@example.com 'password'
```

3. Lisans durumunu kontrol edin:

```bash
chloros-cli status
```

4. Destek ekibiyle iletişime geçin: info@mapir.camera

***

### Görüntü Bulunamadı**Hata:**

```

No images found in the specified folder
```

**Çözümler:**

1. Klasörün desteklenen formatları (.RAW, .TIF, .JPG) içerdiğini doğrulayın
2. Klasör yolunun doğru olduğunu kontrol edin (boşluk içeren yollar için tırnak işaretleri kullanın)
3. Klasör için okuma izinlerine sahip olduğunuzdan emin olun
4. Dosya uzantılarının doğru olduğunu kontrol edin

***

### İşlem Duruyor veya Takılıyor**Çözümler:**

1. Kullanılabilir disk alanını kontrol edin (çıktı için yeterli olduğundan emin olun)
2. Belleği boşaltmak için diğer uygulamaları kapatın
3. Görüntü sayısını azaltın (toplu olarak işleyin)

***

### Bağlantı Noktası Zaten Kullanılıyor**Hata:**

```

Port 5000 is already in use
```

**Çözümler:**

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

## SSS

### S: CLI için lisansa ihtiyacım var mı?

**C:**Evet! CLI için ücretli**Chloros+ lisansı** gereklidir.

* ❌ Standart (ücretsiz) plan: CLI devre dışı
* ✅ Chloros+ (ücretli) planlar: CLI tamamen etkin

Abone olun: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### S: CLI&#x27;i GUI&#x27;si olmayan bir sunucuda kullanabilir miyim?**C:** Evet! CLI tamamen başsız çalışır. Bu, Linux&#x27;teki başlıca kullanım senaryosudur.**Windows Sunucusu:**
* Windows Sunucusu 2016 veya üstü
* Visual C++ Yeniden Dağıtılabilir Paketi yüklü

**Linux Sunucusu:**
* Ubuntu 20.04+ / Debian 11+ (amd64) veya JetPack 6 (arm64)
* `.deb` paketi ile yükleyin

**Her iki platform:**
* En az 8 GB RAM (16 GB önerilir)
* Tek seferlik lisans etkinleştirme: `chloros-cli login user@example.com 'password'`

***

### S: İşlenmiş görüntüler nereye kaydedilir?**C:**Varsayılan olarak, işlenmiş görüntüler**girişle aynı klasöre** kamera modeli alt klasörlerinde (ör. `Survey3N_RGN/`) kaydedilir.

Farklı bir çıktı klasörü belirtmek için `-o` seçeneğini kullanın:

```bash
# Windows
chloros-cli process "C:\Input" -o "D:\Output"

# Linux
chloros-cli process ~/input -o ~/output
```

***

### S: Birden fazla klasörü aynı anda işleyebilir miyim?**A:** Tek bir komutla doğrudan değil, ancak komut dosyası kullanarak klasörleri sırayla işleyebilirsiniz. [Otomasyon ve Komut Dosyası](CLI.md#automation--scripting) bölümüne bakın.***

### S: CLI çıktısını bir günlük dosyasına nasıl kaydederim?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Toplu İş:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

**Linux Bash:**

```bash
chloros-cli process ~/datasets/field_a 2>&1 | tee processing.log
```

***

### S: İşlem sırasında Ctrl+C tuşlarına basarsam ne olur?**C:** CLI şu işlemleri gerçekleştirir:

1. İşlemeyi düzgün bir şekilde durdurur
2. Arka ucu kapatır
3. 130 koduyla çıkar

Kısmen işlenmiş görüntüler çıktı klasöründe kalabilir.

***

### S: CLI işlemeyi otomatikleştirebilir miyim?**C:** Elbette! CLI otomasyon için tasarlanmıştır. PowerShell (Windows), Batch (Windows), Bash (Linux) ve Python (çapraz platform) örnekleri için [Otomasyon ve Komut Dosyası Oluşturma](CLI.md#automation--scripting) bölümüne bakın.***

### S: CLI sürümünü nasıl kontrol edebilirim?**C:**

```bash
chloros-cli --version
```

**Çıktı:**

```

Chloros CLI 1.1.0
```

***

## Yardım Alma

### Komut Satırı Yardımı

Yardım bilgilerini doğrudan CLI içinde görüntüleyin:

```bash
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### Destek Kanalları

* **E-posta**: info@mapir.camera
* **Web sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **Fiyatlandırma**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## Tam Örnekler

### Örnek 1: Temel İşleme

Varsayılan ayarlarla işleme (vinyet, yansıma):

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a_2025_01_15
```

***

### Örnek 2: Yüksek Kaliteli Bilimsel Çıktı

32 bit kayan nokta TIFF:

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

### Örnek 3: Hızlı Önizleme İşlemesi

Hızlı inceleme için kalibrasyonsuz 8 bit PNG:

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

### Örnek 4: PPK Düzeltmeli İşleme

Yansıma ile PPK düzeltmeleri uygulayın:

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

### Örnek 5: Özel Çıktı Konumu

Belirli bir formatla farklı bir konuma işleyin:

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

### Örnek 6: Kimlik Doğrulama İş Akışı

Kimlik doğrulama akışını tamamlayın (tüm platformlarda aynıdır):

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

### Örnek 7: Çoklu Dil Kullanımı

Arayüz dilini değiştirin (tüm platformlarda aynıdır):

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
