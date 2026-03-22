# NVIDIA Jetson Kılavuzu

XPROTX000047NVIDIA Jetson üzerinde çalışan XPROTX, sahada, İHA’larda ve uzak kurulumlarda uç cihazlarda çok spektrumlu görüntü işleme olanağı sunar. Chloros, Jetson modelinizi otomatik olarak algılar ve donanımınıza uygun işleme stratejisini optimize eder.

***

## Desteklenen Jetson Modelleri

| Model                | RAM            | İşleme Stratejisi                                   | Önerilen Kullanım                                          |
| -------------------- | -------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64 GB paylaşımlı | `GPU_PARALLEL` (4 işçi)                            | Maksimum performans, büyük veri kümeleri                      |
| **Jetson Orin NX**   | 8-16 GB paylaşımlı  | `GPU_PARALLEL` (3 işçi, 16 GB) / `GPU_SINGLE` (8 GB) | Hava ve saha dağıtımı için birincil öneri |
| **Jetson Orin Nano** | 8 GB paylaşımlı     | `GPU_SINGLE` (1 işçi)                               | Giriş seviyesi uç bilgi işlem                                 |
| **Jetson Nano**      | 4-8 GB paylaşımlı   | `GPU_SINGLE` (1 işçi)                               | Giriş seviyesi, bellek kısıtlı                          |

{% hint style="info" %}
**Eski Jetson modelleri** (TX2, TX1, Xavier NX) desteklenmeyebilir. Performans, kullanılabilir GPU belleği ve CUDA özelliklerine göre değişiklik gösterecektir.
{% endhint %}

***

## Gereksinimler

* **JetPack 6.x** (en son sürüm önerilir)
* **NVIDIA CUDA** (JetPack ile birlikte gelir)
* **Chloros+ lisansı** (CLI/SDK erişimi için gereklidir)

## Kurulum

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

Genel Linux kurulum ayrıntıları için bkz. [Linux Kurulumu](linux-installation.md).

***

## Jetson&#x27;da Dinamik Hesaplama Uyumlaştırma

Chloros, Jetson modelinizi otomatik olarak algılar ve en uygun işleme stratejisini seçer. **Manuel ayar gerekmez.**

### Nasıl Çalışır?

Başlangıçta, Chloros sisteminizin profilini çıkarır:

1. `/proc/device-tree/model` aracılığıyla **Jetson modelini algılar**

2.**Kullanılabilir GPU/paylaşımlı belleği okur**

3.**Bir işleme stratejisi seçer** (`GPU_PARALLEL`, `GPU_SINGLE` veya `CPU_PARALLEL`)
4. **İşçi sayısını, boru hattı türünü ve bellek tahsisini** otomatik olarak ayarlar

### Modele Göre Davranış

| Jetson Modeli                | Strateji       | İşçiler | İş Akışı                       | Eşzamanlılık |
| --------------------------- | -------------- | ------- | ------------------------------ | ----------- |
| **Jetson Nano 8GB**         | `GPU_SINGLE`   | 1       | `tiled_gpu` (bellek verimli) | Sıralı  |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | `tiled_gpu`                    | Seri  |
| **Jetson Orin NX 8 GB**      | `GPU_SINGLE`   | 2       | `tiled_gpu`                    | Seri hale getirilmiş  |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 3       | `fused_gpu` (tam GPU yolu)    | Eşzamanlı  |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 4       | `fused_gpu`                    | Eşzamanlı  |

{% hint style="success" %}
**Jetson Orin NX 16GB**, uç dağıtım için ideal bir seçenektir — 3 eşzamanlı işçi ile `GPU_PARALLEL` stratejisini alır ve kompakt bir form faktöründe gerçek paralel GPU işleme sunar.
{% endhint %}

Platformlar arasındaki temel fark **bellek**tir. 8 GB paylaşımlı belleğe sahip bir Jetson Nano, bellek verimliliği yüksek döşeme yaklaşımını kullanarak görüntüleri tek tek işlemek zorundadır; buna karşılık 16 GB belleğe sahip bir Orin NX, daha yüksek verimli birleştirilmiş boru hattını kullanarak GPU üzerinden 3 görüntüyü aynı anda çalıştırabilir.

Tam hesaplama uyarlama referansı için bkz. [Dinamik Hesaplama Uyarlama](../processing-architecture/dynamic-compute-adaptation.md).

***

## Isı Yönetimi

Jetson cihazları, özellikle kapalı veya havada kullanılan kurulumlarda sınırlı bir termal marjına sahiptir. Chloros, otomatik termal izleme ve hız sınırlama özelliklerini içerir:

| Sıcaklık         | Eylem                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70°C**          | Normal çalışma — tam işlem hızı          |
| **70°C** (Uyarı)  | Toplu iş boyutunu otomatik olarak azaltın                   |
| **80°C** (Kritik) | Agresif hız düşürme — daha düşük eşzamanlılık         |
| **90°C** (Kapatma) | GPU işlemeyi tamamen durdur — soğutma gerekli |

{% hint style="warning" %}
Özellikle kapalı saha muhafazalarında veya hava taşıtlarında, sürekli işleme için **yeterli havalandırma ve ısı emilimi** sağlayın. Termal hız düşürme, donanımı korumak için işlem verimini azaltacaktır.
{% endhint %}

***

## Bellek Yönetimi

Jetson cihazları **birleşik bellek** kullanır — GPU ve CPU aynı fiziksel RAM&#x27;i paylaşır. Bu, bildirilen VRAM&#x27;in (ör. Orin NX 16GB&#x27;de 15,3GB) GPU&#x27;ya özel bellek olmadığı anlamına gelir; işletim sistemi ve diğer işlemlerle paylaşılır.

### Takas Önerileri

Büyük veri kümeleri veya Texture Aware debayer işleme için, Chloros takas alanı oluşturulmasını önerebilir:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Görüntü başına bellek tahminleri:**

* Standart debayer: Görüntü başına ~10 MB
* Texture Aware debayer: Görüntü başına ~15 MB

Chloros, veri kümenizin boyutuna göre gerekli belleği otomatik olarak hesaplar ve takas alanı öneriliyorsa sizi uyarır.

### OOM (Bellek Yetersizliği) Yedekleme

İşleme sırasında bellek yetersizliği durumu tespit edilirse:

1. Chloros, GPU işçi sayısını otomatik olarak azaltır
2. `fused_gpu`&#x27;ten `tiled_gpu` iş akışına geri döner (bellek açısından daha verimli)
3. Çökmek yerine, azaltılmış verimle işleme devam eder

***

## Saha Dağıtımı

### Güçle İlgili Hususlar

| Jetson Modeli     | Tipik Güç Tüketimi | Notlar                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Nano      | 5-10W              | USB-C veya silindir jakı    |
| Jetson Orin Nano | 7-15 W              | DC varil jakı          |
| Jetson Orin NX   | 10-25 W             | DC varil jakı          |
| Jetson AGX Orin  | 15-60 W             | USB-C PD veya varil jakı |

Sürekli işleme için güç bütçenizi planlayın — en yüksek güç tüketimi, GPU yoğunluğu yüksek olan İş Parçacığı 3 (İşleme) sırasında gerçekleşir.

### Depolama Önerileri

* **NVMe SSD**, arm64 dağıtımları için şiddetle tavsiye edilir
* SD kartlar işleme için çok yavaştır — yalnızca önyükleme ortamı olarak kullanın
* İşlenmiş çıktı için ham görüntü verisi boyutunuzun 2-3 katını planlayın

### SSH aracılığıyla Başsız Çalıştırma

Chloros CLI, başsız Jetson dağıtımları için idealdir:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### systemd ile Otomatik İşleme

Otomatik işleme için bir systemd hizmeti oluşturun:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

Zamanlanmış işleme için bir systemd zamanlayıcıyla eşleştirin:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## Örnek İş Akışları

### Temel Jetson İşleme

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI
```

### Jetson&#x27;da Python SDK

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### Birden Fazla Uçuşun Toplu İşlenmesi

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Saha Kullanımı için Önerilen Jetson Sistemleri

Saha ve hava uygulamaları için şu Jetson Orin NX 16 GB taşıyıcı kart seçeneklerini değerlendirin:

* **Hava/drone**: Titreşim derecesine (MIL-STD) sahip, hafif (300 g&#x27;ın altında), pasif soğutmalı sistemler
* **Zorlu saha**: PoE GigE kamera bağlantısına sahip IP67/IP69K su geçirmez muhafazalar
* **Minimal/bütçe**: Ek muhafazalı geliştirici kitleri

Kurulum senaryonuz için özel donanım önerileri almak üzere [MAPIR Destek](https://www.mapir.camera/community/contact) ile iletişime geçin.

***

## Sonraki Adımlar

* [Linux Kurulumu](linux-installation.md) — Genel Linux kurulum ayrıntıları
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Tam hesaplama stratejisi referansı
* [İşleme Boru Hattı](../processing-architecture/processing-pipeline.md) — 4 iş parçacıklı boru hattını anlama
* [CLI : Komut Satırı](../CLI.md) — Tam CLI referansı
* [API : Python SDK](../api-python-sdk.md) — Tam SDK referansı
