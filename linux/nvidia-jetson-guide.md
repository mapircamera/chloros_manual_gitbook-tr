# NVIDIA Jetson Kılavuzu

NVIDIA Jetson üzerinde çalışan Chloros, sahada, İHA’larda ve uzak kurulumlarda çok spektral görüntü işlemeyi mümkün kılar. Chloros 1.2.0, başlangıçta Jetson modelinizi algılar ve bulduğu donanıma göre işleme stratejisini optimize eder. **Manuel ayar yapmaya gerek yoktur.**

***

## Desteklenen Jetson Modelleri

| Model                | RAM            | İşleme Stratejisi                                     | Önerilen Kullanım                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64 GB paylaşımlı | `GPU_PARALLEL` (2 işçi)                              | Maksimum performans, büyük veri kümeleri                      |
| **Jetson Orin NX**   | 8-16 GB paylaşımlı  | `GPU_PARALLEL` (2 iş parçacığı, 16 GB) / `GPU_SINGLE` (8 GB)   | Hava ve saha uygulamaları için birincil öneri |
| **Jetson Orin Nano** | 8 GB paylaşımlı     | `GPU_SINGLE` (1 iş parçacığı, sıralı)                     | Giriş seviyesi uç bilgi işlem                                 |

{% hint style="info" %}
Linux arm64 paketi, Jetson Orin ailesinde bulunan **JetPack 6**&#x27;yı gerektirir. Eski modeller (Nano, TX2, Xavier NX) JetPack 6&#x27;yı çalıştıramaz ve mevcut paket tarafından desteklenmez.
{% endhint %}

***

## Gereksinimler

* **JetPack 6.x** (en son sürüm önerilir)
* **NVIDIA CUDA** (JetPack ile birlikte gelir)
* **Ücretli Chloros+ planı** — Copper kademesi veya üstü (tüm CLI/SDK erişimleri için gereklidir; sunucu tarafında zorunlu tutulur)

## Kurulum

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.2.0"

# Install Python SDK (optional) — the bundled wheel always matches this build
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl

# Run system diagnostics
chloros-cli selftest
```

Genel Linux kurulum ayrıntıları, dosya konumları ve sorun giderme için bkz. [Linux Kurulumu](linux-installation.md).

{% hint style="info" %}
**Çıkarma dizinini hızlı bir depolama birimine yerleştirin.** Derlenmiş ikili dosyalar her başlatıldığında kendilerini geçici bir dizine açar — bu işlem SD karttan yapıldığında son derece yavaştır. Chloros, mevcutsa `/mnt/ssd/tmp`&#x27;i otomatik olarak kullanır; aksi takdirde `TMPDIR`&#x27;i NVMe&#x27;nizdeki bir yoluna ayarlayın (`export TMPDIR=/mnt/nvme/tmp`).
{% endhint %}

***

## Jetson&#x27;da Dinamik Hesaplama Uyumlaştırma

### Nasıl Çalışır

Başlangıçta, Chloros sisteminizin profilini çıkarır:

1. **`/proc/device-tree/model` aracılığıyla Jetson modelini algılar**

2.**Kullanılabilir paylaşımlı GPU/CPU belleğini okur** (Jetson, birleşik bellek kullanır)
3. **Bir işleme stratejisi seçer** (`GPU_PARALLEL`, `GPU_SINGLE` veya `CPU_PARALLEL`)
4. **İşçi sayısını, iş akışı türünü ve bellek tahsisini** otomatik olarak ayarlar

Karar, model adına değil **toplam paylaşımlı RAM**&#x27;e göre verilir:

* **Toplam RAM 12 GB&#x27;nin altında**(tüm 8 GB&#x27;lık Jetson&#x27;lar): `GPU_SINGLE`,**1 işçi — kasıtlı sıralı işleme**. Bellek, eşzamanlı işçiler için yetersiz olduğundan görüntüler tek tek işlenir.**8 GB veya daha az** belleğe sahip Jetson&#x27;larda, 3. İş Parçacığı, işçi havuzunu tamamen atlar ve görüntü başına işini işlem içinde yürütür.
* **12 GB veya daha fazla**(Orin NX 16 GB, AGX Orin): birleşik bellek `GPU_PARALLEL` için uygun olsa da, işçi sayısı**Jetson&#x27;da 2 ile sınırlıdır** — GPU, işçi süreçlerinin RAM&#x27;i ve işçi başına CUDA bağlamlarının tümü aynı paylaşılan havuzdan yararlanır; bu nedenle daha fazla işçi kullanılması, bellek yetersizliği hatalarına yol açabilir.

`CHLOROS_STRATEGY` ortam değişkeni ile otomatik seçimi geçersiz kılabilirsiniz — bkz. [Dinamik Hesaplama Uyumlaştırması](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Model Başına Davranış

| Jetson Modeli                | Strateji       | İşçiler | Yürütme                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8 GB**    | `GPU_SINGLE`   | 1       | Sıralı işlem içi döngü (bellek kısıtlaması durumunda `tiled_gpu`) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | Sıralı işlem içi döngü                     |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 2       | Eşzamanlı işçi süreçleri, `fused_gpu` yolu  |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2       | Eşzamanlı işçi süreçleri, `fused_gpu` yolu  |

Platformlar arasındaki temel fark **bellek**tir. 8 GB&#x27;lık bir Jetson, yük yüksek olduğunda bellek verimliliği yüksek döşemeli bir yaklaşım kullanarak görüntüleri tek tek işlemek zorundadır; buna karşın 16 GB ve üzeri Orin, daha yüksek verimli birleştirilmiş iş akışını kullanarak GPU üzerinden aynı anda 2 görüntüyü işleyebilir.

### Model Başına GPU Bütçesi

Her Jetson modeli, paylaşılan havuzdan ne kadar işleme kapasitesi ayrılabileceğini sınırlayan ve parti boyutlarını ölçeklendiren bir donanım profili de içerir:

| Model | GPU bütçe üst sınırı | Parti boyutu çarpanı | Sistem/ekran için ayrılan |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | %70 | ×0,8 | 2,0 GB |
| **Jetson Orin NX** | %75 | ×1,0 | 3,0 GB |
| **Jetson AGX Orin**| %80 | ×1,5 | 4,0 GB |

Algılanan RAM, profili ayarlar:**16 GB veya daha fazla** bildiren bir Jetson&#x27;da toplu iş çarpanı ×1,2 oranında artırılır. Çarpanlar uygulanmadan önceki temel toplu iş boyutu 8 görüntüdür.

Hesaplama uyarlamasına ilişkin eksiksiz referans için bkz. [Dinamik Hesaplama Uyarlaması](../processing-architecture/dynamic-compute-adaptation.md).

***

## Nano ve Orin Nano&#x27;da Texture Aware için GPU Frekans Sınırı

Texture Aware debayer, GPU sinir ağı çıkarımını çalıştırır; bu işlem, düşük güç tüketimli Jetson modellerinde (10-15 W sınıfı) GPU saat hızı tam kapasitede çalıştığında **aşırı akım uyarılarını**tetikleyebilir.**Jetson Nano veya Orin Nano**üzerinde Texture Aware işleme başlamadan önce, Chloros, GPU’nun maksimum frekansını kontrol eder ve mevcut frekans daha yüksekse bunu**510 MHz** (510000000) ile sınırlar:

* CLI komutu GPU frekansı sysfs düğümüne yazabiliyorsa, sınırlama **otomatik olarak uygulanır** ve bir onay mesajı görüntülenir.
* Aksi takdirde (root yetkisi gerekiyorsa), CLI, sınırı manuel olarak uygulamak için tam `sudo` komutunu görüntüler, komutu okuyabilmeniz için bir süre bekler, ardından devam eder — işleme devam eder ancak aşırı akım uyarıları görüntülenebilir.

İşleme başlamadan önce sınırı kendiniz uygulamak için:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Daha yüksek güçteki modeller (Orin NX 25W, AGX Orin 60W) tam GPU hızında çalışır; herhangi bir sınır uygulanmaz. Standart debayer, hiçbir modelde sınırı tetiklemez.

{% hint style="info" %}
**Jetson&#x27;da Texture Aware her zaman tek seferde bir görüntü işler.** Her işleyicinin kendi CUDA bağlamına (~1 GB) ve gürültü giderici modelin kendi kopyasına ihtiyacı vardır; bu da birleşik bellek için mümkün değildir — bu nedenle Jetson’da Texture Aware yolu, GPU erişimi sıralı hale getirilmiş tek bir işleyiciye sabitlenmiştir. Herhangi bir Jetson cihazında Texture Aware’in Standard’a göre belirgin şekilde daha yavaş olacağını bekleyebilirsiniz.
{% endhint %}

***

## Isı Yönetimi

Jetson cihazlarının, özellikle kapalı alanlarda veya havada kullanılan uygulamalarda, sınırlı bir termal marjı vardır. Chloros, SoC sıcaklığını izler ve işleme grubu boyutlarını otomatik olarak sınırlar:

| Sıcaklık         | Eylem                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70°C**          | Normal çalışma — tam işlem hızı          |
| **70°C** (Uyarı)  | İşlem grubu boyutu kademeli olarak küçülür (70°C ile 80°C arasında %100 → %50) |
| **80°C** (Kritik) | Aşırı kısıtlama (80°C ile 90°C arasında %50 → %0) |
| **90°C** (Kapatma) | GPU işleme tamamen durdurulur — soğuma gereklidir |

{% hint style="warning" %}
Özellikle kapalı saha muhafazalarında veya hava taşıtlarında sürekli işlem için **yeterli havalandırma ve ısı emilimi** sağlayın. Termal hız kısıtlaması, donanımı korumak amacıyla işlem verimini düşürecektir.
{% endhint %}

***

## Bellek Yönetimi

Jetson cihazları **birleşik bellek** kullanır — GPU ve CPU aynı fiziksel RAM&#x27;i paylaşır. Bildirilen VRAM (örn. Orin NX 16 GB&#x27;da ~15,3 GB), GPU&#x27;ya ayrılmış bellek değildir; işletim sisteminin ve diğer tüm işlemlerin kullandığı RAM&#x27;in aynısıdır.

### Takas Uyarısı ve Öneriler

Jetson’da işleme başlamadan önce, CLI komutu giriş klasörünüzdeki RAW görüntülerin sayısını sayar (`.tif`, `.tiff`, `.raw`, `.dng` — JPG önizlemeleri sayılmaz), işlemin gerektireceği en yüksek bellek miktarını tahmin eder ve RAM + swap alanının yetersiz kalma ihtimali varsa **işlemi başlatmadan önce uyarı verir**. Uyarı başlığı `LOW MEMORY WARNING - Jetson Detected` şeklindedir; görüntü sayınızı, RAM&#x27;inizi, mevcut takas alanınızı ve tahmini en yüksek değeri gösterir, ardından projenize uygun boyutta (asla 8 GB&#x27;den az olmamak üzere) kesin `fallocate` / `chmod` / `mkswap` / `swapon` komutlarını verir (asla 8 GB&#x27;den az olmaz). Mesajın kaydırma geçmişinde kaybolmaması için birkaç saniye duraklar, ardından işleme devam eder.**Uyarıda kullanılan bellek tahminleri:**

| Debayer modu | Temel | Görüntü başına |
| --- | --- | --- |
| Standart | ~1,5 GB | ~10 MB |
| Doku Duyarlı | ~2,5 GB (model + Python çalışma zamanı) | ~15 MB |

Tahmini tepe değeri, RAM + takas alanı eksi 1 GB&#x27;lık güvenlik marjını aştığında uyarı tetiklenir ve yalnızca **dosya tabanlı** takas alanı hesaba katılır — yalnızca zram içeren bir kurulumda da uyarı verilir.

Takas alanını manuel olarak eklemek için (örnek: 8 GB):



<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->

```bash
# Check current memory and swap
free -h

# Create a swap file
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```### OOM (Bellek Yetersizliği) Yönetimi

İşleme sırasında, Chloros GPU belleğini izler ve çökmek yerine sorunsuz bir şekilde performansı düşürür:

1. GPU bellek kullanımı **%85**’i aştığında, işleme gruplarının boyutları önleyici olarak azaltılır
2. Hala bellek yetersizliği olayı meydana gelirse, işleme grubu boyutu **yarıya indirilir** ve ardışık her OOM durumunda tekrar yarıya indirilir; sonraki her başarılı işleme grubu, bu cezayı bir adım geri alır
3. Sürekli baskı altında, iş akışı `fused_gpu`&#x27;ten bellek verimliliği yüksek `tiled_gpu` yoluna geçer ve son çare olarak CPU işlemeye geçer

***

## Saha Uygulaması

### Güç Hususları

| Jetson Modeli     | Tipik Güç Tüketimi | Notlar                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7-15 W              | DC silindir jakı          |
| Jetson Orin NX   | 10-25 W             | DC silindir jakı          |
| Jetson AGX Orin  | 15-60 W             | USB-C PD veya silindir jakı |

Sürekli işleme için güç bütçenizi planlayın — en yüksek güç tüketimi, GPU yoğunluğu yüksek olan İşlem 3 (İşleme) sırasında gerçekleşir.

### Depolama Önerileri

* **NVMe SSD**, arm64 dağıtımları için şiddetle tavsiye edilir
* SD kartlar işleme için çok yavaştır — yalnızca önyükleme ortamı olarak kullanın
* İşlenmiş çıktı için ham görüntü verisi boyutunun 2-3 katı kadar yer ayırın

### SSH ile Başsız Çalıştırma

Chloros ve CLI, başlıksız Jetson dağıtımları için idealdir:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### LATTICE / DAQ-E Zaman Senkronizasyonu için Sürekli Çalışan Arka Uç

Jetson&#x27;unuz LATTICE kameralarını veya DAQ-E ışık sensörlerini başsız olarak kontrol ediyorsa, PTP grandmaster&#x27;ın kesintisiz çalışması için systemd arka uç hizmetini etkinleştirin (birim yüklenmiştir ancak varsayılan olarak etkin değildir):

```bash
sudo systemctl enable --now chloros-backend.service
chloros-cli time-sync status
```

Paketin, PTP 319/320 numaralı bağlantı noktalarını root erişimi olmadan bağlanabilir hale getirme yöntemi dahil olmak üzere ayrıntılar için [Linux Kurulumu](linux-installation.md#always-on-ptp-for-headless-hosts) bölümüne bakın.

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

`chloros-cli process`, ürün talep eden bir çalıştırma sırasında hiçbir görüntü yazılmadığında sıfırdan farklı bir değerle sonlanır; bu nedenle systemd’nin hata durumu izleme açısından anlamlıdır.

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
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI
```

### Jetson&#x27;da Python ve SDK

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
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## Saha Kullanımı İçin Önerilen Jetson Sistemleri

Saha ve hava uygulamaları için şu Jetson Orin NX 16 GB taşıyıcı kart seçeneklerini değerlendirin:

* **Havada/drone**: Titreşim dayanım derecesine (MIL-STD) sahip, hafif (300 g&#x27;ın altında), pasif soğutmalı sistemler
* **Zorlu saha koşulları**: PoE GigE kamera bağlantısına sahip IP67/IP69K su geçirmez muhafazalar
* **Minimal/ekonomik**: Ek muhafazalı geliştirici kitleri

Kurulum senaryonuz için özel donanım önerileri almak üzere [MAPIR Destek](https://www.mapir.camera/community/contact) ile iletişime geçin.

***

## Sonraki Adımlar

* [Linux Kurulumu](linux-installation.md) — Genel Linux kurulum ayrıntıları
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Tam hesaplama stratejisi referansı
* [İşleme Boru Hattı](../processing-architecture/processing-pipeline.md) — 4 iş parçacıklı boru hattını anlama
* [CLI : Komut Satırı](../CLI.md) — CLI kılavuzu
* [API : Python SDK](../api-python-sdk.md) — SDK kılavuzu
* [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) — 1.2.0 sürümü için kapsamlı komut/API listeleri
