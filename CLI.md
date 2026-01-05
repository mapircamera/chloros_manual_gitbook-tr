# CLI : Komut Satırı

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>**Chloros CLI**, Chloros görüntü işleme motoruna güçlü bir komut satırı erişimi sağlayarak görüntüleme iş akışlarınız için otomasyon, komut dosyası oluşturma ve başsız çalışma imkanı sunar.

### Temel Özellikler

* 🚀 **Otomasyon** - Birden fazla veri kümesinin komut dosyası toplu işleme
* 🔗 **Entegrasyon** - Mevcut iş akışlarına ve boru hatlarına gömme
* 💻 **Başsız Çalışma** - GUI olmadan çalıştırma
* 🌍 **Çok Dilli** - 38 dil desteği
* ⚡ **Paralel İşleme** - CPU&#x27;nuzla dinamik olarak ölçeklenir (16 adede kadar paralel işçi)

### Gereksinimler

| Gereksinim          | Ayrıntılar                                                             |
| -------------------- | ------------------------------------------------------------------- |
| **İşletim Sistemi** | Windows 10/11 (64 bit)                                              |
| **Lisans**          | Chloros+ ([ücretli plan gereklidir](https://cloud.mapir.camera/pricing)) |
| **Bellek**           | Minimum 8 GB RAM (16 GB önerilir)                                  |
| **İnternet**         | Lisans etkinleştirme için gereklidir                                     |
| **Disk Alanı**       | Proje boyutuna göre değişir                                              |

{% hint style=&quot;warning&quot; %}
**Lisans Gereksinimi**: CLI için ücretli Chloros+ aboneliği gerekir. Standart (ücretsiz) planlarda CLI erişimi yoktur. Yükseltmek için [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) adresini ziyaret edin.
{% endhint %}

## Hızlı Başlangıç

### Kurulum

CLI, Chloros yükleyicisine otomatik olarak dahildir:

1. **Chloros Yükleyici.exe**&#x27;i indirin ve çalıştırın
2. Kurulum sihirbazını tamamlayın
3. CLI şuraya yüklenir: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style=&quot;success&quot; %}
Yükleyici, `chloros-cli`&#x27;i sistem PATH&#x27;inize otomatik olarak ekler. Yüklemeden sonra terminalinizi yeniden başlatın.
{% endhint %}

### İlk Kurulum

CLI&#x27;i kullanmadan önce, Chloros+ lisansınızı etkinleştirin:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### Temel Kullanım

Varsayılan ayarlarla bir klasörü işleyin:

```powershell
chloros-cli process "C:\Images\Dataset001"
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

**Örnek:**

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### İşlem Komutu Seçenekleri

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
| `--format`            | Seçim  | TIFF (16 bit)  | Çıktı biçimi: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size`   | Tamsayı | Otomatik           | Kalibrasyon paneli algılama için minimum hedef boyut (piksel)                          |
| `--target-clustering` | Tamsayı | Otomatik           | Hedef kümeleme eşiği (0-100)                                                    |
| `--exposure-pin-1`    | Dize  | Yok           | Kamera modeli için pozlamayı kilitle (Pin 1)                                                 |
| `--exposure-pin-2`    | Dize  | Yok           | Kamera modeli için pozlamayı kilitle (Pin 2)                                                 |
| `--recal-interval`    | Tamsayı | Otomatik           | Yeniden kalibrasyon aralığı (saniye)                                                      |
| `--timezone-offset`   | Tamsayı | 0              | Saat dilimi farkı (saat)                                                               |

***

### `login` - Hesabı Doğrulama

Chloros+ kimlik bilgilerinizi kullanarak oturum açın ve CLI işlemini etkinleştirin.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

**Örnek:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style=&quot;warning&quot; %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluklar içeren şifrelerin etrafına tek tırnak işareti koyun.
{% endhint %}

**Çıktı:**<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - Kimlik Bilgilerini Temizle

Saklanan kimlik bilgilerini temizleyin ve hesabınızdan çıkış yapın.

**Sözdizimi:**

```bash
chloros-cli logout
```

**Örnek:**

```powershell
chloros-cli logout
```

**Çıktı:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style=&quot;info&quot; %}
**SDK Kullanıcıları**: Python SDK ayrıca Python komut dosyalarında kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi sağlar. Ayrıntılar için [Python SDK belgelerine](api-python-sdk.md#logout) bakın.
{% endhint %}

***

### `status` - Lisans Durumunu Kontrol Et

Mevcut lisans ve kimlik doğrulama durumunu görüntüler.

**Sözdizimi:**

```bash
chloros-cli status
```

**Örnek:**

```powershell
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

### `export-status` - Dışa Aktarım İlerlemesini Kontrol Et

İşlem sırasında veya sonrasında Thread 4 dışa aktarım ilerlemesini izler.

**Sözdizimi:**

```bash
chloros-cli export-status
```

**Örnek:**

```powershell
chloros-cli export-status
```

**Kullanım Örneği:** İşlem devam ederken bu komutu çağırarak dışa aktarım ilerlemesini kontrol edin.***

### `language` - Arayüz Dilini Yönetme

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

#### Desteklenen Diller (Toplam 38)

| Kod    | Dil              | Yerel Ad      |
| ------- | --------------------- | ---------------- |
| `en`    | İngilizce               | İngilizce          |
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
| `nl`    | Felemenkçe                 | Nederlands       |
| `ar`    | Arapça                | العربية          |
| `pl`    | Lehçe                | Polski           |
| `tr`    | Türkçe               | Türkçe           |
| `hi`    | Hintçe                 | हिंदी            |
| `id`    | Endonezyaca            | Bahasa Indonesia |
| `vi`    | Vietnamca            | Tiếng Việt       |
| `th`    | Tayca                  | ไทย              |
| `sv`    | İsveççe               | Svenska          |
| `da`    | Danca                | Dansk            |
| `no`    | Norveççe             | Norsk            |
| `fi`    | Fince               | Suomi            |
| `el`    | Yunanca                 | Ελληνικά         |
| `cs`    | Çekçe                 | Čeština          |
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

{% hint style=&quot;success&quot; %}
**Otomatik Kalıcılık**: Dil tercihiniz `~/.chloros/cli_language.json` dosyasına kaydedilir ve tüm oturumlar boyunca kalıcıdır.
{% endhint %}

***

### `set-project-folder` - Varsayılan Proje Klasörünü Ayarla

Varsayılan proje klasörünün konumunu değiştirin (GUI ile paylaşılır).

**Sözdizimi:**

```bash
chloros-cli set-project-folder <folder-path>
```

**Örnek:**

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `get-project-folder` - Proje Klasörünü Göster

Geçerli varsayılan proje klasörü konumunu görüntüler.

**Sözdizimi:**

```bash
chloros-cli get-project-folder
```

**Örnek:**

```powershell
chloros-cli get-project-folder
```

**Çıktı:**

```
ℹ Current project folder: C:\Projects\2025
```

***

### `reset-project-folder` - Varsayılana Sıfırla

Proje klasörünü varsayılan konuma sıfırlar.

**Sözdizimi:**

```bash
chloros-cli reset-project-folder
```

***

## Genel Seçenekler

Bu seçenekler tüm komutlar için geçerlidir:

| Seçenek          | Tür    | Varsayılan       | Açıklama                                      |
| --------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe` | Yol    | Otomatik algılanan | Arka uç yürütülebilir dosyasının yolu                       |
| `--port`        | Tamsayı | 5000          | Arka uç API bağlantı noktası numarası                          |
| `--restart`     | Bayrak    | -             | Arka ucu yeniden başlat (mevcut işlemleri sonlandır) |
| `--version`     | Bayrak    | -             | Sürüm bilgilerini göster ve çık                |
| `--help`        | Bayrak    | -             | Yardım bilgilerini göster ve çık                   |

**Global Seçeneklerle Örnek:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## İşleme Ayarları Kılavuzu

### Paralel İşleme

Chloros+ CLI **otomatik olarak ölçeklendirir**paralel işlemeyi bilgisayarınızın kapasitesine uyacak şekilde:**Nasıl Çalışır:**

* CPU çekirdeklerinizi ve RAM&#x27;inizi algılar
* İşçileri tahsis eder: **2× CPU çekirdeği** (hiper iş parçacığı kullanır)
* **Maksimum: 16 paralel işçi** (kararlılık için)**Sistem Katmanları:**

| Sistem Türü   | CPU        | RAM      | İşçiler  | Performans     |
| ------------- | ---------- | -------- | -------- | --------------- |
| **Üst Düzey**  | 16+ çekirdek  | 32+ GB   | 16&#x27;ya kadar | Maksimum hız   |
| **Orta Sınıf** | 8-15 çekirdek | 16-31 GB | 8-16     | Mükemmel hız |
| **Düşük Sınıf**   | 4-7 çekirdek  | 8-15 GB  | 4-8      | İyi hız      |

{% hint style=&quot;success&quot; %}
**Otomatik Optimizasyon**: CLI, sistem özelliklerinizi otomatik olarak algılar ve optimum paralel işlemeyi yapılandırır. Manuel yapılandırma gerekmez!
{% endhint %}

### Debayer Yöntemleri

CLI, varsayılan ve önerilen debayer algoritması olarak **Yüksek Kalite (Daha Hızlı)**&#x27;yi kullanır:

| Yöntem                      | Kalite | Hız | Açıklama                                 |
| --------------------------- | ------- | ----- | ------------------------------------------- |
| **Yüksek Kalite (Daha Hızlı)** ⭐ | ⭐⭐⭐⭐    | ⚡⚡⚡   | Kenar algılama algoritması (varsayılan, önerilen) |

### Vinyet Düzeltme

**Ne yapar:** Görüntü kenarlarında ışık düşüşünü düzeltir (kamera görüntülerinde sık görülen daha koyu köşeler).

* **Varsayılan olarak etkindir** - Çoğu kullanıcı bunu etkin tutmalıdır
* Devre dışı bırakmak için `--no-vignette` kullanın

{% hint style=&quot;success&quot; %}
**Öneri**: Çerçeve genelinde eşit parlaklık sağlamak için vinyet düzeltmeyi her zaman etkinleştirin.
{% endhint %}

### Yansıma Kalibrasyonu

Kalibrasyon panelleri kullanarak ham sensör değerlerini standartlaştırılmış yansıma yüzdelerine dönüştürür.

* **Varsayılan olarak etkin** - Bitki örtüsü analizi için gereklidir.
* Görüntülerde kalibrasyon hedef panelleri gerektirir.
* Devre dışı bırakmak için `--no-reflectance` kullanın.

{% hint style=&quot;info&quot; %}
**Gereksinimler**: Doğru yansıma dönüşümü için kalibrasyon panellerinin görüntülerinizde düzgün bir şekilde pozlanmış ve görünür olduğundan emin olun.
{% endhint %}

### PPK Düzeltmeleri

**Ne yapar:** GPS doğruluğunu artırmak için DAQ-A-SD günlük verilerini kullanarak Sonrası İşlenmiş Kinematik düzeltmeleri uygular.

* **Varsayılan olarak devre dışıdır**
* Etkinleştirmek için `--ppk` kullanın
* MAPIR DAQ-A-SD ışık sensöründen proje klasöründeki .daq dosyaları gerekir.

### Çıkış Biçimleri

<table><thead><tr><th width="197">Biçim</th><th width="130.20001220703125">Bit Derinliği</th><th width="116.5999755859375">Dosya Boyutu</th><th>En Uygun</th></tr></thead><tbody><tr><td><strong>TIFF (16 bit)</strong> ⭐</td><td>16 bit tamsayı</td><td>Büyük</td><td>GIS analizi, fotogrametri (önerilir)</td></tr><tr><td><strong>TIFF (32 bit, Yüzde)</strong></td><td>32 bit kayan nokta</td><td>Çok büyük</td><td>Bilimsel analiz, araştırma</td></tr><tr><td><strong>PNG (8 bit)</strong></td><td>8 bitlik tamsayı</td><td>Orta</td><td>Görsel inceleme, web paylaşımı</td></tr><tr><td><strong>JPG (8 bit)</strong></td><td>8 bitlik tamsayı</td><td>Küçük</td><td>Hızlı önizleme, sıkıştırılmış çıktı</td></tr></tbody></table>***

## Otomasyon ve Komut Dosyası Oluşturma

### PowerShell Toplu İşleme

Birden fazla veri kümesi klasörünü otomatik olarak işleyin:

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

### Windows Toplu İşleme Komut Dosyası

Toplu işleme için basit döngü:

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

### Python Otomasyon Komut Dosyası

Hata işleme özelliğine sahip gelişmiş otomasyon:

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

## İşleme İş Akışı

### Standart İş Akışı

1. **Giriş**: RAW/JPG görüntü çiftlerini içeren klasör
2. **Keşif**: CLI desteklenen görüntü dosyalarını otomatik olarak tarar
3. **İşleme**: Paralel mod CPU çekirdeklerinize göre ölçeklenir (Chloros+)
4. **Çıktı**: İşlenmiş görüntülerle kamera modeli alt klasörleri oluşturur

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

| Mod              | Süre      | Donanım                                     |
| ----------------- | --------- | -------------------------------------------- |
| **Paralel Mod** | 5-10 dakika  | i7/Ryzen 7, 16 GB RAM, SSD (16 çalışana kadar) |
| **Paralel Mod** | 10-15 dakika | i5/Ryzen 5, 8 GB RAM, HDD (en fazla 8 işçi)   |

{% hint style=&quot;info&quot; %}
**Performans İpucu**: İşlem süresi, görüntü sayısı, çözünürlük ve bilgisayar özelliklerine göre değişir.
{% endhint %}

***

## Sorun Giderme

### CLI Bulunamadı

**Hata:**

```
'chloros-cli' is not recognized as an internal or external command
```

**Çözümler:**

1. Kurulum konumunu doğrulayın:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. PATH&#x27;te yoksa tam yolu kullanın:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. PATH&#x27;e manuel olarak ekleyin:
   * Sistem Özellikleri → Ortam Değişkenleri&#x27;ni açın.
   * PATH değişkenini düzenleyin.
   * Ekle: `C:\Program Files\Chloros\resources\cli`
   * Terminali yeniden başlatın.

***

### Arka Uç Başlatılamadı**Hata:**

```

Backend failed to start within 30 seconds
```

**Çözümler:**

1. Arka ucun zaten çalışıp çalışmadığını kontrol edin (önce kapatın).
2. Windows Güvenlik Duvarı&#x27;nın engellemediğini kontrol edin.
3. Farklı bir bağlantı noktası deneyin:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. Arka ucu zorla yeniden başlatın:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### Lisans / Kimlik Doğrulama Sorunları**Hata:**

```

Chloros+ license required for CLI access
```

**Çözümler:**

1. Etkin bir Chloros+ aboneliğiniz olduğunu doğrulayın
2. Kimlik bilgilerinizle oturum açın:

```powershell
chloros-cli login user@example.com 'password'
```

3. Lisans durumunu kontrol edin:

```powershell
chloros-cli status
```

4. Desteğe başvurun: info@mapir.camera

***

### Görüntü Bulunamadı**Hata:**

```

No images found in the specified folder
```

**Çözümler:**

1. Klasörün desteklenen formatları (.RAW, .TIF, .JPG) içerdiğini doğrulayın.
2. Klasör yolunun doğru olduğunu kontrol edin (boşluk içeren yollar için tırnak işaretleri kullanın).
3. Klasör için okuma izinlerine sahip olduğunuzdan emin olun.
4. Dosya uzantılarının doğru olup olmadığını kontrol edin.

***

### İşlem Duruyor veya Takılıyor**Çözümler:**

1. Kullanılabilir disk alanını kontrol edin (çıktı için yeterli olduğundan emin olun).
2. Belleği boşaltmak için diğer uygulamaları kapatın.
3. Görüntü sayısını azaltın (toplu olarak işleyin).

***

### Bağlantı Noktası Zaten Kullanılıyor**Hata:**

```

Port 5000 is already in use
```

**Çözüm:**

Farklı bir bağlantı noktası belirtin:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## SSS

### S: CLI için lisans gerekir mi?

**C:**Evet! CLI için ücretli**Chloros+ lisansı** gerekir.

* ❌ Standart (ücretsiz) plan: CLI devre dışı
* ✅ Chloros+ (ücretli) planlar: CLI tamamen etkinleştirilmiş

Abone olun: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### S: CLI&#x27;i GUI&#x27;siz bir sunucuda kullanabilir miyim?**C:** Evet! CLI tamamen başsız çalışır. Gereksinimler:

* Windows Server 2016 veya üstü
* Visual C++ Redistributable yüklü
* Yeterli RAM (minimum 8 GB, önerilen 16 GB)
* Herhangi bir makinede tek seferlik GUI lisans aktivasyonu

***

### S: İşlenen görüntüler nereye kaydedilir?**C:**Varsayılan olarak, işlenen görüntüler kamera modeli alt klasörlerinde (ör. `Survey3N_RGN/`)**girişle aynı klasöre** kaydedilir.

Farklı bir çıktı klasörü belirtmek için `-o` seçeneğini kullanın:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### S: Birden fazla klasörü aynı anda işleyebilir miyim?**C:** Tek bir komutla doğrudan yapamazsınız, ancak komut dosyası kullanarak klasörleri sırayla işleyebilirsiniz. [Otomasyon ve Komut Dosyası](CLI.md#automation--scripting) bölümüne bakın.***

### S: CLI çıktısını bir günlük dosyasına nasıl kaydedebilirim?**PowerShell:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**Toplu iş:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### S: İşlem sırasında Ctrl+C tuşlarına basarsam ne olur?**C:** CLI şunları yapar:

1. İşleme düzgün bir şekilde son verir
2. Arka ucu kapatır
3. 130 koduyla çıkar

Kısmen işlenmiş görüntüler çıktı klasöründe kalabilir.

***

### S: CLI işlemesini otomatikleştirebilir miyim?**C:** Elbette! CLI otomasyon için tasarlanmıştır. PowerShell, Batch ve Python örnekleri için [Otomasyon ve Komut Dosyası Oluşturma](CLI.md#automation--scripting) bölümüne bakın.***

### S: CLI sürümünü nasıl kontrol edebilirim?**C:**

```powershell
chloros-cli --version
```

**Çıktı:**

```

Chloros CLI 1.0.2
```

***

## Yardım Alma

### Komut Satırı Yardımı

Yardım bilgilerini doğrudan CLI&#x27;te görüntüleyin:

```powershell
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

Varsayılan ayarlarla işleme (vinyet, yansıtma):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### Örnek 2: Yüksek Kaliteli Bilimsel Çıktı

32 bit float TIFF:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### Örnek 3: Hızlı Önizleme İşleme

Hızlı inceleme için kalibrasyon olmadan 8 bit PNG:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### Örnek 4: PPK Düzeltmeli İşleme

Yansıtma ile PPK düzeltmeleri uygulayın:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### Örnek 5: Özel Çıktı Konumu

Belirli bir formatla farklı sürücüye işleme:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### Örnek 6: Kimlik Doğrulama İş Akışı

Kimlik doğrulama akışını tamamlayın:

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

### Örnek 7: Çok Dilli Kullanım

Arayüz dilini değiştirin:

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
