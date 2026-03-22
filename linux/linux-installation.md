# Linux Kurulumu

Chloros, Linux için `.deb` paketleri olarak dağıtılır; bu paketler CLI ve arka uç bileşenini yükler. Python SDK, pip aracılığıyla ayrı olarak kurulur.

***

## Linux amd64 (x86_64)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Dağıtım** | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+ |
| **İşlemci** | x86_64 (Intel/AMD) | Intel Core i7 veya üstü |
| **Bellek (RAM)** | 8 GB | 16 GB veya daha fazla |
| **Grafik Kartı** | Yok (CPU işleme) | 4 GB+ VRAM&#x27;e sahip NVIDIA GPU |
| **Depolama** | 2 GB boş alan | 10 GB+ boş alana sahip SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

### Kurulum

`.deb` paketini indirin ve kurun:

```bash
sudo dpkg -i chloros-amd64.deb
```

Kurulumu doğrulayın:

```bash
chloros-cli --version
```

***

## Linux arm64 (NVIDIA Jetson)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Platform** | JetPack 6 ile NVIDIA Jetson | Jetson Orin NX 16GB veya AGX Orin |
| **JetPack** | JetPack 6.x | En son JetPack 6 |
| **Bellek (RAM)** | 8 GB (paylaşımlı GPU/CPU) | 16 GB+ paylaşımlı |
| **Depolama** | 2 GB boş alan | 10 GB+ boş alana sahip NVMe SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

### Kurulum

JetPack 6 `.deb` paketini indirin ve kurun:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

Kurulumu doğrulayın:

```bash
chloros-cli --version
```

Isı yönetimi ve saha dağıtımı dahil olmak üzere ayrıntılı Jetson kurulumu için [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) bölümüne bakın.

***

## Python SDK Kurulumu (Tüm Linux)

Python SDK, pip aracılığıyla ayrı olarak kurulur ve hem amd64 hem de arm64 üzerinde çalışır:

```bash
pip install chloros-sdk
```

İsteğe bağlı ilerleme akışı desteğini dahil etmek için:

```bash
pip install chloros-sdk[progress]
```

SDK&#x27;i doğrulayın:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` paketi, Chloros CLI ve arka ucu yükler. Python SDK, yerel bir HTTP API aracılığıyla arka uçla iletişim kuran ayrı bir pip paketidir.
{% endhint %}

***

## Yapılandırma Dizinleri

Chloros, Linux üzerinde [XDG Temel Dizin Spesifikasyonu](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html) &#x27;na uyar:

| Amaç | Linux Yolu | Windows Eşdeğeri |
| --- | --- | --- |
| **Yapılandırma** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **Veriler / Projeler** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **Önbellek / Kimlik Bilgileri** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## Arka Uç Yürütülebilir Dosya Konumları

`.deb` paketi, arka ucu standart bir konuma yükler. CLI ve SDK, arka uç yolunu otomatik olarak algılar:

| Kurulum Yöntemi | Arka Uç Yolu |
| --- | --- |
| `.deb` paketi | `/usr/lib/chloros/chloros-backend` |
| Manuel / özel | `/opt/mapir/chloros/backend/chloros-backend` |

Arka uç yolunu `--backend-exe` CLI bayrağı veya `backend_exe` SDK yapıcı parametresi ile geçersiz kılabilirsiniz.

***

## İlk Kurulum

### 1. Lisansınızı Etkinleştirin

Chloros+ lisansı, CLI ve SDK erişimi için gereklidir:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. Lisans Durumunuzu Kontrol Edin

```bash
chloros-cli status
```

### 3. İlk Veri Kümenizi İşleyin

```bash
chloros-cli process ~/datasets/flight001
```

### 4. Sistem Tanı İşlemlerini Çalıştırın

Sisteminizin doğru şekilde yapılandırıldığını doğrulayın:

```bash
chloros-cli selftest
```

Bu, sürüm, arka uç başlatma, API bağlantısı ve CUDA/GPU kullanılabilirliği dahil olmak üzere 7 tanılama kontrolünü çalıştırır.

***

## Bash Komut Dosyası Örnekleri

### Birden Fazla Veri Setini İşleyin

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### Özel Ayarlarla İşleyin

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### Cron ile Otomatik İşleme

Yeni veri kümelerini otomatik olarak işlemek için crontab&#x27;ınıza (`crontab -e`) ekleyin:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK Örneği

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

## Sorun Giderme

### CLI Kurulumdan Sonra Bulunamadı

`chloros-cli` paketi kurulduktan sonra `.deb` bulunamıyorsa:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### İzin Reddedildi

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### Arka Uç Başlatılamadı

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### CUDA Algılanmadı

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### Eksik Paylaşımlı Kütüphaneler

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## Linux üzerinde Chloros&#x27;i Güncelleme

Güncellemeleri kontrol etmek ve yüklemek için yerleşik güncelleme komutunu kullanın:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## Sonraki Adımlar

* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson&#x27;a özgü optimizasyon ve dağıtım
* [CLI : Komut Satırı](../CLI.md) — Tam CLI komut referansı
* [API : Python SDK](../api-python-sdk.md) — Tam SDK referansı
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros&#x27;in donanımınıza nasıl uyum sağladığı
