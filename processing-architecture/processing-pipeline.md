# İşleme Boru Hattı

Chloros1.2.0, aşamalı bir montaj hattı gibi çalışan 4 iş parçacıklı bir işleme boru hattı kullanır. Her iş parçacığı, iş akışının farklı bir aşamasını üstlenir; böylece aynı anda birden fazla görüntü, farklı aşamalarda işlenebilir.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## İş Akışı Mimarisi

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Her görüntü, dört iş parçacığının tümünden sırayla geçer. Chloros+ çok iş parçacıklı işleme sayesinde, birden fazla görüntü aynı anda farklı iş parçacıklarını işgal eder — İş Parçacığı 3 bir görüntüyü işlerken, İş Parçacığı 1 bir sonrakini algılayabilir, İş Parçacığı 2 bir başkasını kalibre edebilir ve İş Parçacığı 4 tamamlanmış bir görüntüyü diske yazabilir.

İlerleme durumu her iş parçacığı için ayrı ayrı raporlanır ve Server-Sent Events üzerinden aktarılır (arka uç bunları `/api/events` üzerinde yayınlar). CLI adresindeki canlı ilerleme ekranında dört aşama **Algılama, Analiz, İşleme, Dışa Aktarma** olarak etiketlenmiştir.***

## İş Parçacığı Ayrıntıları

### İş Parçacığı 1: Algılama

**Amaç**: Görüntüleri yüklemek ve kalibrasyon hedeflerini algılamak.

* Diskten görüntü dosyalarını okur — Survey3 `.raw`+`.jpg` çiftleri, LATTICE `.tif`/`.tiff` çekimleri ve `.dng`
* EXIF meta verilerini (GPS, kamera modeli, zaman damgaları, pozlama) çıkarır
* Kalibrasyon hedeflerini algılar: LATTICE çekimleri için ArUco işaretli hedef geometrileri ve Survey3 kalibrasyon hedefi fotoğrafları için klasik panel dedektörü
* Çıktılar: görüntü verileri + meta veriler + hedef algılama sonuçları

Öncelikle I/O ve CPU&#x27;ya bağlı bir iş parçacığıdır.

### İş Parçacığı 2: Kalibrasyon

**Amaç**: Algılanan hedeflerden kalibrasyon parametrelerini hesaplamak.

* Hedef görüntülerden yansıma kalibrasyon katsayılarını hesaplar
* Vinyet düzeltme parametrelerini hesaplar
* Bant başına kalibrasyon eğrilerini belirler
* Çıktılar: her görüntü için kalibrasyon parametreleri

CPU&#x27;ya bağlı bir hesaplama iş parçacığıdır. Yansıma kalibrasyonu etkinleştirildiğinde İş Parçacığı 3 bu iş parçacığını bekler; böylece herhangi bir görüntü işlenmeden önce katsayılar hazır hale gelir.

### İş Parçacığı 3: İşleme (GPU)

**Amaç**: Düzeltmeleri uygulamak ve bitki örtüsü indekslerini hesaplamak.**Bu, en yoğun hesaplama gerektiren iş parçacığıdır.*** **Debayering**: RAW Bayer verilerini çok kanallı görüntülere dönüştürür
  * Standart (Hızlı, Orta Kalite) — varsayılan, `--debayer standard`
  * Doku Duyarlı (Yavaş, En Yüksek Kalite) — yalnızca Chloros+ için, `--debayer texture-aware`, bir AI/ML gürültü giderme modeli kullanır
  * LATTICE mono (M3M) çekimleri tek bantlıdır: bu çekimlerde demosaik ve beyaz dengesi adımları atlanır (tek satırlık bir günlük mesajıyla), ancak aynı işlemdeki M3C/Bayer görüntüleri bu işlemlerden geçmeye devam eder
* **Vinyet düzeltmesi**: Görüntü genelinde lens vinyet düzeltmesi uygular
* **Yansıma kalibrasyonu**: Yansıma değerlerine dönüştürmek için kalibrasyon katsayılarını uygular
* **Endeks hesaplaması**: Bitki örtüsü endekslerini hesaplar (NDVI, NDRE, GNDVI, …)
* Çıktılar: Dışa aktarılmaya hazır işlenmiş görüntü verileri

Bu iş parçacığı, GPU hızlandırmasından en fazla yararlanan iş parçacığıdır ve [Dinamik Hesaplama Uyumlaştırması](dynamic-compute-adaptation.md) tarafından ayarlanan iş parçacığıdır.

### İş Parçacığı 4: Dışa Aktarma

**Amaç**: İşlenmiş görüntüleri diske yazmak.

* Çıktı dosyalarını seçilen formatta yazar — `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Çıktı dosyalarına meta verileri (GPS, zaman damgaları, işleme parametreleri) gömer
* Çıktıları proje klasörü altında `<camera>/<format>/<Product>_Images/` şeklinde düzenler — örneğin `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Dışa aktarılan dosyalar kaynak dosyanın adını korur; ürün, klasör adıyla tanımlanır.**
* LATTICE çekimleri için, bir kaynak kare birkaç ürüne (Debayered, Preview, Radiance, Reflectance, Index) ayrılabilir; her biri kendi ürün klasöründe bulunur
* Çıktılar: diskteki nihai dosyalar

Öncelikle bir G/Ç sınırlı iş parçacığıdır — SSD depolama, performansı belirgin şekilde artırır.

***

## Arka Plan: Yürütücüler

İş Parçacığı 3 içinde, görüntü başına işler Python&#x27;in standart `concurrent.futures` ile paralel hale getirilir:

* **GPU stratejileri**(`GPU_SINGLE`, `GPU_PARALLEL`),**spawn** başlatma yöntemini kullanır — her işçi, kendi CUDA bağlamına sahip ayrı bir işlemdir (`fork`, üst işlemin başlatılmış CUDA durumunu devralır ve alt işlemleri bozar)
* **`CPU_PARALLEL`**, bir `ThreadPoolExecutor` kullanır — NumPy ve OpenCV, GIL&#x27;i serbest bırakır, bu nedenle iş parçacıkları yeterlidir
* 8 GB veya daha az paylaşımlı RAM’e sahip Jetson cihazları, yürütücüyü tamamen atlar ve işlem içinde, sıralı olarak işler
* 7 GB’nin altında VRAM’e sahip bir GPU’da Texture Aware de sıralı olarak çalışır — gürültü giderici model birden fazla kez sığamaz

Chloros, herhangi bir üçüncü taraf dağıtık çerçeve (Ray gibi) kullanmaz. Stratejinin ve işçi sayısının nasıl seçildiğini öğrenmek için [Dinamik Hesaplama Uyumlaştırması](dynamic-compute-adaptation.md) bölümüne bakın.

***

## Sıralı İşleme ve Boru Hattı İşleme

### Serbest Mod (Sıralı)

Chloros&#x27;ın ücretsiz sürümünde, görüntüler **tek tek**, dört aşamanın tümünden sırayla işlenir:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI, ücretsiz modda basitleştirilmiş bir ilerleme çubuğu gösterir; sıralı aşamalar **Hedef Algılama**ve ardından**İşleme** olarak bildirilir.

### Chloros+ Modu (Pipelined)

Chloros+ lisansıyla, dört iş parçacığının tümü farklı görüntüler üzerinde **eşzamanlı olarak** çalışır:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI ilerleme çubuğu dört aşamayı gösterir; üzerine fareyi getirdiğinizde iş parçacığı başına ilerlemeyi görebilirsiniz. CLI&#x27;da aynı dört aşama **Algılama, Analiz, İşleme, Dışa Aktarma** olarak canlı olarak yayınlanır.

{% hint style="info" %}
**Tek etiket, iki isim.** CLI adresinde 3. aşama _İşleme_ olarak adlandırılır. Arka uçtaki premium mod ilerleme akışında — GUI ilerleme çubuğunun görüntülediği akış — aynı aşama _Kalibre Ediyor_ olarak etiketlenir. Bunlar, aynı işi yapan aynı iş parçacığıdır (İş Parçacığı 3: debayer, düzeltmeler, indeksler).
{% endhint %}

{% hint style="success" %}
**Chloros+ ile ardışık işleme**, donanımınıza ve veri kümenizin boyutuna bağlı olarak sıralı işlemeye göre 3-5 kat daha hızlı olabilir. Hız artışı, hızlı GPU&#x27;lara ve SSD&#x27;lere sahip sistemlerde en yüksek seviyededir.
{% endhint %}

***

## İş Parçacığı 4 Dışa Aktarım İlerleme Durumu

Dışa aktarım iş parçacığının kendi ilerleme izleme sistemi vardır; bunu ayrı olarak sorgulayabilirsiniz:**CLI:**

```bash
chloros-cli export-status
```

**SDK:**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

İşlem, İş Parçacığı 4 %100&#x27;e ulaştığında tamamlanır.

{% hint style="info" %}
**Hiçbir görüntü yazmayan bir çalıştırma, başarısızlık olarak değerlendirilir.**Başarılı bir işlemde, `chloros-cli process` kaç tane görüntü ürünü yazdığını bildirir (`Image products written: N`). Ürünler talep edildiği halde**hiçbiri**yazılmadıysa — yalnızca `project.json` ve `calibration_data.json` — CLI, `Processing finished but wrote no image products.` değerini yazdırır ve**sıfırdan farklı bir değerle** sonlanır, proje klasörünün adını ve olağan nedenleri belirtir (giriş klasörü bir yakalama olarak tanınmadı — düzeni kontrol edin ve `--input-level` — veya talep edilen tüm ürünler bu kameralar için uygun değildi). Komut dosyaları çıkış koduna güvenebilir.
{% endhint %}

***

## Dinamik Hesaplama Uyumuyla İlişkisi

[Dinamik Hesaplama Uyumu](dynamic-compute-adaptation.md) öncelikle **İş Parçacığı 3 (İşleme)**&#x27;ü etkiler:

* **`GPU_PARALLEL`**: İş Parçacığı 3, `fused_gpu` iş akışını kullanarak GPU üzerinden birden fazla görüntüyü eşzamanlı olarak işler
* **`GPU_SINGLE`**: İş Parçacığı 3, `fused_gpu` veya bellek verimliliği yüksek `tiled_gpu` iş akışını kullanarak, işçi süreçleri I/O işlemlerini çakıştırırken GPU erişimini bir semaforla sıralı hale getirir
* **`CPU_PARALLEL`**: İş parçacığı 3, çok iş parçacıklı paralellik ile CPU tabanlı işleme kullanır

İş parçacığı 1 ve 2 tamamlandıkça iş parçacığı 3&#x27;ün GPU bellek tahsisi de artar — bkz. [Dinamik GPU Bellek Tahsisi](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Sonraki Adımlar

* [Dinamik Hesaplama Uyumlaştırma](dynamic-compute-adaptation.md) — Chloros&#x27;ın donanımınız için en uygun stratejiyi nasıl seçtiği
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson&#x27;da platforma özgü iş akışı davranışı
* [İşlemeyi İzleme](../processing-images-gui/monitoring-the-processing.md) — GUI ilerleme izleme
* [CLI Referansı](../reference/cli-reference.md) — `process`, `export-status`, çıkış kodları ve çıktı düzeni
