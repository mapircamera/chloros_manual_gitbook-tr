# NVIDIA Jetson Kılavuzu NVIDIA Jetson üzerindeki

Chloros
, sahada, İHA’larda ve uzak kurulumlarda uç cihazlarda çok spektral görüntü işlemeyi mümkün kılar. Chloros
1.3.0, başlangıçta Jetson modelinizi algılar ve bulduğu donanıma göre işleme stratejisini optimize eder. **Manuel ayar yapılması gerekmez.**
{% hint style="info" %}
**

1.3.0 Jetson paketi, işlemleri Jetson’un GPU’su üzerinde gerçekleştirir.** `chloros_1.3.0_arm64_jp6.deb`, JetPack 6 için derlenmiştir ve JetPack 6’nın CUDA tabanlı PyTorch sürümünü kullanır; bu nedenle, işleme (Texture Aware debayer dahil) Orin’in GPU’su üzerinde çalışır. Paket, CUDA'yı kendisi içermez: JetPack 6'nın yüklediği CUDA ve cuDNN kütüphanelerini kullanır (bu kütüphanelerin bulunmadığı bir görüntüde `sudo apt install nvidia-jetpack`). Kullanılabilir bir CUDA GPU'nun olmadığı durumlarda, işlemler CPU üzerinde gerçekleştirilir ve Texture Aware, Standard debayer'ı kullanır.
{% endhint %}

***

## Desteklenen Jetson Modelleri

| Model                | RAM            | İşleme Stratejisi                                     | Önerilen Kullanım                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64 GB paylaşımlı | `GPU_PARALLEL` (2 işçi)                              | Maksimum performans, büyük veri kümeleri                      |
| **Jetson Orin NX**   | 8-16 GB paylaşımlı  | `GPU_PARALLEL` (2 işçi, 16 GB) / `GPU_SINGLE` (8 GB)   | Saha dağıtımı için birincil öneri              |
| **Jetson Orin Nano** | 8 GB paylaşımlı     | `GPU_SINGLE` (1 iş parçacığı, sıralı)                     | Giriş seviyesi uç bilgi işlem                                 |

{% hint style="info" %}
Linux
arm64 paketi, Jetson Orin ailesinde bulunan **JetPack 6**'yı gerektirir. Eski modeller (Nano, TX2, Xavier NX) JetPack 6'yı çalıştıramaz ve mevcut paket tarafından desteklenmez.
{% endhint %}

***

## Gereksinimler

* **JetPack 6.x** (en son sürüm önerilir)
* JetPack 6'daki **NVIDIA CUDA ve cuDNN** (görüntünüzde bunlar yoksa `sudo apt install nvidia-jetpack`)
* **Ücretli Chloros
+ planı** — Copper kademesi veya üstü (tüm CLI
/SDK
erişimi için gereklidir; sunucu tarafında zorunludur)

## Kurulum

```bash
# Install the arm64 .deb package (JetPack 6) -- apt, not dpkg -i, so the
# Python SDK's requirements (python3-numpy, python3-requests) come too
sudo apt install ./chloros_1.3.0_arm64_jp6.deb

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.3.0"
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # the .deb installs the SDK

# Run system diagnostics
chloros-cli selftest
```

`.deb`, bu derlemeye uygun olarak `python3` sistemi için Python
SDK
'yi yükler. Yalnızca bir virtualenv'e, paket içinde bulunan wheel dosyasının yüklenmesi gerekir: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` (tamamen çevrimdışı). Genel Linux
kurulum ayrıntıları, dosya konumları ve sorun giderme için [Linux
Kurulumu](linux-installation.md) bölümüne bakın.

{% hint style="info" %}
**Başlatma sırasında artık dosya açma işlemi yapılmaz.**

1.3.0 sürümünden itibaren arka uç ve CLI
, `/usr/lib/chloros/` altında sıkıştırılmamış olarak kurulur; bu nedenle artık her başlatıldıklarında kendilerini geçici bir dizine açmazlar. Geçici dosyalar, bu klasör mevcut olduğunda hâlâ otomatik olarak `/mnt/ssd/tmp` klasörüne kaydedilir; aksi takdirde, `TMPDIR`'i NVMe'nizdeki bir yola (`export TMPDIR=/mnt/nvme/tmp`) ayarlayabilirsiniz.
{% endhint %}

***

## Jetson'da Dinamik Hesaplama Uyumlaştırması

### Nasıl Çalışır?

Başlangıçta, Chloros
sisteminizin profilini oluşturur:

1. `/proc/device-tree/model` aracılığıyla **Jetson modelini algılar**

2.**Kullanılabilir paylaşımlı GPU/CPU belleğini okur** (Jetson, birleşik bellek kullanır)
3. **PyTorch'a CUDA'yı kullanıp kullanamayacağını sorar** — JetPack 6'nın CUDA ve cuDNN'si yüklü olduğunda, kullanabilir
4. **Bir işleme stratejisi** (`GPU_PARALLEL`, `GPU_SINGLE` veya `CPU_PARALLEL`) ve işçi sayısını otomatik olarak seçer

Bu karar, model adına değil, **toplam paylaşımlı RAM**'e göre belirlenir:

* **Toplam RAM 12 GB'nin altında**(tüm 8 GB Jetson'lar):**1 işçi — kasıtlı sıralı işleme**. Bellek, eşzamanlı işçiler için yetersiz olduğundan görüntüler tek tek işlenir.**8 GB veya daha az** RAM'e sahip Jetson'larda, Thread 3 işçi havuzunu tamamen atlar ve görüntü başına işlerini işlem içinde yürütür. Arka uç günlüğü bu stratejiye `GPU_SINGLE` adını verir.
* **12 GB veya daha fazla**(Orin NX 16 GB, AGX Orin): Birleşik bellek, `GPU_PARALLEL` için uygun olmakla birlikte, işçi sayısı**Jetson'da 2 ile sınırlıdır** — GPU, işçi süreçlerinin RAM'i ve işçi başına CUDA bağlamları aynı paylaşımlı havuzdan yararlanır; bu nedenle daha fazla işçi kullanılması, bellek yetersizliği hatalarına yol açma riski taşır.
* **Kullanılabilir CUDA yok** (JetPack'in CUDA kütüphaneleri eksik): 12 GB veya üzeri kapasitede `CPU_PARALLEL`; 12 GB'nin altındaki kapasiteler için her seferinde tek bir görüntü.

`CHLOROS_STRATEGY` ortam değişkeniyle otomatik seçimi geçersiz kılabilirsiniz — bkz. [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### Modele Göre Davranış

| Jetson Modeli                | Strateji       | İşçiler | Yürütme                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8 GB**    | `GPU_SINGLE`   | 1       | Sıralı işlem içi döngü (bellek baskısı altında `tiled_gpu`) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | Sıralı işlem içi döngü                     |
| **Jetson Orin NX 16 GB**     | `GPU_PARALLEL` | 2       | Eşzamanlı işçi süreçleri, `fused_gpu` yolu  |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2       | Eşzamanlı işçi süreçleri, `fused_gpu` yolu  |

Platformlar arasındaki temel fark **bellek**tir. 8 GB'lık bir Jetson, yük yüksek olduğunda bellek verimliliği sağlayan döşemeli bir yaklaşım kullanarak görüntüleri tek tek işlemek zorundadır; buna karşılık 16 GB ve üzeri Orin modelleri, daha yüksek verimli birleştirilmiş iş akışını kullanarak GPU üzerinden aynı anda 2 görüntüyü işleyebilir.

### Model Başına GPU Bütçesi

Her Jetson modeli, paylaşılan havuzdan ne kadar işlem kapasitesi ayrılabileceğini sınırlayan ve parti boyutlarını ölçeklendiren bir donanım profiline sahiptir:

| Model | GPU bütçe üst sınırı | Parti boyutu çarpanı | Sistem/ekran için ayrılan |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | %70 | ×0,8 | 2,0 GB |
| **Jetson Orin NX** | %75 | ×1,0 | 3,0 GB |
| **Jetson AGX Orin**| %80 | ×1,5 | 4,0 GB |

Algılanan RAM, profili ayarlar:**16 GB veya daha fazla** RAM bildiren bir Jetson cihazında, toplu iş çarpanı ×1,2 oranında artırılır. Çarpanlar uygulanmadan önceki temel toplu iş boyutu 8 görüntüdür.

Hesaplama uyarlama kılavuzunun tamamı için bkz. [Dinamik Hesaplama Uyarlama](../processing-architecture/dynamic-compute-adaptation.md).

***

## Nano ve Orin Nano'da Texture Aware için GPU Frekans Sınırı

Texture Aware debayer, GPU sinir ağı çıkarımını çalıştırır; bu işlem, düşük güç tüketimli Jetson modellerinde (10-15 W sınıfı) GPU saat hızı tam kapasitedeyken **aşırı akım uyarılarını**tetikleyebilir.**Jetson Nano veya Orin Nano**'da**`--debayer texture-aware`** komutunu verdiğinizde, CLI
, GPU'nun maksimum frekansını kontrol eder ve mevcut frekans daha yüksekse bunu **510 MHz** (510000000) ile sınırlar:

* Eğer CLI
, GPU frekansı sysfs düğümüne yazabiliyorsa, sınır **otomatik olarak uygulanır** ve bir onay mesajı görüntülenir.
* Aksi takdirde (root erişimi gerekiyorsa), CLI
, sınırı manuel olarak uygulamak için tam `sudo` komutunu görüntüler, okumanız için bir süre bekler, ardından devam eder — işlemler devam eder ancak aşırı akım uyarıları görüntülenebilir.

İşleme başlamadan önce sınırı kendiniz uygulamak için:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

Daha yüksek güçteki modeller (Orin NX 25W, AGX Orin 60W) tam GPU hızında çalışır; herhangi bir sınır uygulanmaz. Standart debayer, hiçbir modelde sınırı tetiklemez.

{% hint style="info" %}
**Jetson'da Texture Aware her zaman tek seferde bir görüntü işler.** Her işleyicinin kendi CUDA bağlamına (~1 GB) ve gürültü giderici modelinin kendi kopyasına ihtiyacı vardır; bu da birleşik bellek için yeterince kapasiteye sahip değildir — bu nedenle Jetson'da Texture Aware yolu, GPU erişimi sıralı hale getirilmiş tek bir işleyiciye sabitlenmiştir. Herhangi bir Jetson cihazında Texture Aware'in Standard'dan belirgin şekilde daha yavaş olacağını bekleyin. Texture Aware, Survey3
görüntülerine uygulanır: LATTICE yakalamaları her zaman Standard debayer'ı kullanır. Kullanılabilir bir CUDA GPU olmadığında işlem Standard'ı kullanır ve arka uç, bunun nedenini açıklayan bir `[DEBAYER] … needs a CUDA GPU …` satırı günlüğe kaydeder (`chloros-cli --verbose` ile gösterilmiştir).
{% endhint %}

***

## Isı Yönetimi

Jetson cihazlarının, özellikle kapalı ortamlarda veya havada kullanılan uygulamalarda, sınırlı bir ısıl marjı vardır. Chloros
, SoC sıcaklığını izler ve işleme gruplarının boyutlarını otomatik olarak sınırlar:

| Sıcaklık         | Eylem                                            |
| ------------------- | ------------------------------------------------- |
| **< 70°C**          | Normal çalışma — tam işlem hızı          |
| **70°C** (Uyarı)  | İşlem grubu boyutu kademeli olarak küçülür (70°C ile 80°C arasında %100 → %50) |
| **80°C** (Kritik) | Aşırı kısıtlama (80°C ile 90°C arasında %50 → %0) |
| **90°C** (Kapatma) | GPU işleme tamamen durdurulur — soğuma gereklidir |

{% hint style="warning" %}
Özellikle kapalı saha muhafazalarında veya havadan taşınan sistemlerde, sürekli işlem için **yeterli havalandırma ve ısı emilimi** sağlayın. Termal hız kısıtlaması, donanımı korumak amacıyla işlem verimini düşürecektir.
{% endhint %}

***

## Bellek Yönetimi

Jetson cihazları **birleşik bellek** kullanır — GPU ve CPU aynı fiziksel RAM'i paylaşır. Bildirilen VRAM (ör. Orin NX 16 GB'da ~15,3 GB), GPU'ya özel bir bellek değildir; işletim sisteminin ve diğer tüm işlemlerin kullandığı RAM'in aynısıdır.

### Takas Uyarısı ve Öneriler

Jetson'da işleme başlamadan önce, CLI
giriş klasörünüzdeki RAW görüntülerin sayısını sayar (`.tif`, `.tiff`, `.raw`, `.dng` — JPG önizlemeleri sayılmaz), işlemin gerektireceği en yüksek bellek miktarını tahmin eder ve RAM + takas alanı yetersiz kalma ihtimali varsa **işlemi başlatmadan önce uyarı verir**. Uyarı başlığı `LOW MEMORY WARNING - Jetson Detected` şeklindedir; görüntü sayınızı, RAM, mevcut takas alanı ve tahmini en yüksek değeri gösterir, ardından projenize göre boyutlandırılmış kesin `fallocate` / `chmod` / `mkswap` / `swapon` komutlarını verir (asla 8 GB'den küçük olmaz). Mesajın kaydırma geçmişinde kaybolmaması için birkaç saniye duraklar, ardından işleme devam eder.**Uyarıda kullanılan bellek tahminleri:**

| Debayer modu | Temel | Görüntü başına |
| --- | --- | --- |
| Standart | ~1,5 GB | ~10 MB |
| Doku Duyarlı | ~2,5 GB (model + Python çalışma zamanı) | ~15 MB |

Tahmini tepe değeri, RAM + takas alanı eksi 1 GB'lık güvenlik marjını aştığında uyarı tetiklenir ve yalnızca **dosya destekli** takas alanı hesaba katılır — yalnızca zram içeren bir kurulumda da uyarı verilir.

Takas alanını manuel olarak eklemek için (örnek: 8 GB):

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
```

<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->


### OOM (Bellek Yetersizliği) Yönetimi

İşleme sırasında Chloros
, GPU belleğini izler ve çökmek yerine performansı kademeli olarak düşürür:

1. GPU bellek kullanımı **%85**'i aştığında, iş parçacığı boyutları önleyici olarak küçültülür
2. Hala bir bellek yetersizliği olayı meydana gelirse, iş parçacığı boyutu **yarıya indirilir** ve ardışık her OOM durumunda tekrar yarıya indirilir; sonraki her başarılı iş parçacığı bu cezayı bir adım geri alır
3. Sürekli baskı altında, iş akışı `fused_gpu`'ten bellek verimliliği yüksek `tiled_gpu` yoluna geçer ve son çare olarak CPU işlemeye geçer

GPU, Jetson’un RAM’ini paylaştığı için, RAM artı takas alanı hâlâ çalıştırma süresini sınırlamaktadır — yukarıdaki takas uyarısı, dikkat edilmesi gereken kontrol noktasıdır.

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
* İşlenmiş çıktı için ham görüntü verisi boyutunuzun 2-3 katını planlayın

### SSH
Üzerinden Başsız Çalıştırma Chloros için

Chloros
, başsız Jetson dağıtımları için idealdir:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### LATTICE / DAQ-E Zaman Senkronizasyonu için Sürekli Çalışan Arka Uç

Jetson'unuz LATTICE kameralarını veya DAQ-E ışık sensörlerini başsız olarak kontrol ediyorsa, PTP grandmaster'ın kesintisiz çalışması ve SDK
komut dosyalarının çalışan bir arka uç bulabilmesi için arka uç systemd hizmetini **kullanıcı adınızla** etkinleştirin (birim yüklüdür ancak varsayılan olarak etkin değildir):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
chloros-cli time-sync status
```

Arka uç bundan sonra sizin adınıza çalışır: kaydettiği veriler size aittir ve sizin `chloros-cli login`'inizi kullanır. Kök `chloros-backend.service` ve paketin PTP 319/320 bağlantı noktalarını kök erişimi olmadan bağlanabilir hale getirme yöntemi dahil olmak üzere ayrıntılar için [Linux
Kurulumu](linux-installation.md#always-on-ptp-for-headless-hosts) bölümüne bakın.

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

`chloros-cli process`, ürün talep eden bir çalıştırma sırasında hiçbir görüntü yazmadığında sıfırdan farklı bir değerle sonlanır; bu nedenle systemd'nin hata durumu izleme açısından anlamlıdır.

Zamanlanmış işleme için bir systemd zamanlayıcısıyla eşleştirin:

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
    --indices NDVI GNDVI OSAVI
```

### Jetson'da Python
SDK


```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "GNDVI", "OSAVI"],
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
        --indices NDVI OSAVI
    echo "Completed $name"
done
```

***

## Saha Kullanımı İçin Önerilen Jetson Sistemleri

Saha ve hava uygulamaları için şu Jetson Orin NX 16 GB taşıyıcı kart seçeneklerini değerlendirin:

* **Hava/drone**: Titreşim dayanımı (MIL-STD) sertifikalı, hafif (300 g'dan az), pasif soğutmalı sistemler
* **Zorlu saha koşulları**: PoE GigE kamera bağlantısına sahip IP67/IP69K su geçirmez muhafazalar
* **Minimal/ekonomik**: Ek muhafazalı geliştirici kitleri

Kurulum senaryonuz için özel donanım önerileri almak üzere [MAPIR
Destek](https://www.mapir.camera/community/contact) ile iletişime geçin.

***

## Sonraki Adımlar

* [Linux
Kurulumu](linux-installation.md) — Genel Linux
kurulum ayrıntıları
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Tam hesaplama stratejisi referansı
* [İşleme Boru Hattı](../processing-architecture/processing-pipeline.md) — 4 iş parçacıklı boru hattını anlama
* [CLI
: Komut Satırı](../CLI.md) — CLI
kılavuzu
* [API
: Python
SDK
](../api-python-sdk.md) — SDK
kılavuzu
* [CLI
Kılavuzu](../reference/cli-reference.md) ve [SDK
Kılavuzu](../reference/sdk-reference.md) — 1.3.0 sürümü için kapsamlı komut/API
listeleri
