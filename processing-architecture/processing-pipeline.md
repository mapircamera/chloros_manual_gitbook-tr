# İşleme Boru Hattı

Chloros 1.3.0, aşamalı bir montaj hattı gibi çalışan 4 iş parçacıklı bir işleme boru hattı kullanır. Her iş parçacığı iş akışının farklı bir aşamasını yürütür; böylece aynı anda birden fazla görüntü farklı aşamalarda işlenebilir.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

***

## İşleme Hattı Mimarisi

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Her görüntü, dört iş parçacığının tümünden sırayla geçer. Chloros+ çok iş parçacıklı işleme özelliği sayesinde, birden fazla görüntü aynı anda farklı iş parçacıklarında işlenir — 3. iş parçacığı bir görüntüyü işlerken, 1. iş parçacığı bir sonrakini algılayabilir, 2. iş parçacığı başka bir görüntüyü kalibre edebilir ve 4. iş parçacığı tamamlanmış bir görüntüyü diske yazabilir.

İlerleme durumu her iş parçacığı için ayrı ayrı raporlanır ve Server-Sent Events üzerinden aktarılır (arka uç bunları `/api/events` üzerinde yayınlar). CLI’deki canlı ilerleme ekranında dört aşama **Algılama, Analiz, İşleme, Dışa Aktarma** olarak etiketlenmiştir.***

## İş Parçacığı Ayrıntıları

### İş Parçacığı 1: Algılama

**Amaç**: Görüntüleri yüklemek ve kalibrasyon hedeflerini algılamak.

* Görüntü dosyalarını diskten okur — Survey3 `.raw`+`.jpg` çiftleri, LATTICE `.tif`/`.tiff` çekimleri ve `.dng`
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

CPU&#x27;ya bağlı bir hesaplama iş parçacığıdır. Survey3 veya karma bir çalışmada, yansıma kalibrasyonu etkinleştirildiğinde İş Parçacığı 3 bu iş parçacığını bekler; böylece katsayılar herhangi bir görüntü işlenmeden önce hazır hale gelir. Yalnızca LATTICE çalışmasında, İş Parçacığı 3 algılama sırasında çalışır: her kare, hedef bağlantı noktası kesinleştiğinde başlar. Bir kare, zaman açısından her iki tarafta da en yakın hedefi alır; bu nedenle, kamerasının mevcut en yakın hedefinden zaman açısından daha yakın olan her karesi taranana kadar bekler. Sonuçlar aynıdır, sadece daha erken elde edilir.

### İş Parçacığı 3: İşleme (GPU)

**Amaç**: Düzeltmeleri uygulamak ve bitki örtüsü indekslerini hesaplamak.**Bu, en fazla hesaplama gerektiren iş parçacığıdır.*** **Debayering**: RAW Bayer verilerini çok kanallı görüntülere dönüştürür
  * Standart (Hızlı, Orta Kalite) — varsayılan, `--debayer standard`
  * Doku Duyarlı (Yavaş, En Yüksek Kalite) — yalnızca Chloros+, `--debayer texture-aware`, bir AI/ML gürültü giderme modeli kullanır
  * LATTICE mono (M3M) çekimleri tek bantlıdır: bunlar için demosaik ve beyaz dengesi adımları atlanır (tek satırlık bir günlük mesajıyla), ancak aynı işleme dahil olan M3C/Bayer görüntüleri bu işlemlerden geçmeye devam eder
* **Vinyet düzeltmesi**: Görüntü genelinde lens vinyet düzeltmesi uygular
* **Yansıma kalibrasyonu**: Yansıma değerlerine dönüştürmek için kalibrasyon katsayılarını uygular
* **Endeks hesaplaması**: Bitki örtüsü endekslerini (NDVI, NDRE, GNDVI, …) hesaplar
* Çıktılar: dışa aktarılmaya hazır işlenmiş görüntü verileri

Bu iş parçacığı, GPU hızlandırmasından en fazla fayda sağlar ve [Dinamik Hesaplama Uyumlaştırması](dynamic-compute-adaptation.md) tarafından ayarlanan iş parçacığıdır.

### İş Parçacığı 4: Dışa Aktarma

**Amaç**: İşlenmiş görüntüleri diske yazmak.

* Çıktı dosyalarını seçilen formatta yazar — `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`
* Çıktı dosyalarına meta verileri (GPS, zaman damgaları, işleme parametreleri) gömer — 1.3.0 sürümünden itibaren hem Survey3 hem de LATTICE için her işleme modunda yazılan `Chloros:*` işleme etiketleri dahil
* Çıktıları proje klasörü altında `<camera>/<format>/<Product>_Images/` biçiminde düzenler — örneğin `LATT-M3M-L41-F550/tiff16/Reflectance_Calibrated_Images/`. **Dışa aktarılan dosyalar kaynak dosyanın adını korur; ürünü tanımlayan klasördür.**
* LATTICE çekimleri için, bir kaynak kare birkaç ürüne (Debayered, Preview, Radiance, Reflectance, Index) ayrılabilir ve her biri kendi ürün klasöründe yer alır. Index ürünleri, işleme sırasında yazılan yansıma ürününden hesaplanır (eğer yoksa, işleme sırasında yazılan başka bir üründen, örneğin parlaklık ürününden). Çok spektral M3C kameraların parlaklık ve yansıma ürünleri de `<format>/clamp_masks/` içinde piksel başına bir sınırlama maskesi alabilir (bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md#clamp-masks))
* Çıktılar: diskteki nihai dosyalar

Esas olarak I/O sınırlı bir iş parçacığıdır — SSD depolama, performansını belirgin şekilde artırır.

***

## Arka Plan: Yürütücüler

İş Parçacığı 3 içinde, görüntü başına işler şu şekilde paralel hale getirilir:

* **GPU stratejileri**(`GPU_SINGLE`, `GPU_PARALLEL`), her biri kendi CUDA bağlamına sahip ayrı bir işletim sistemi süreci olan bir**işçi süreçleri** havuzu kullanır. Chloros, her işleyicisini, kendi kurulu arka uç programını yeniden başlatarak çalıştırır — Python&#x27;un `multiprocessing`&#x27;i aracılığıyla değil, çünkü bu programın işleyici başlatma işlevi paketlenmiş uygulama içinde çalışmaz
* **`CPU_PARALLEL`**, bir iş parçacığı havuzu (Python&#x27;un `ThreadPoolExecutor`&#x27;i) kullanır — NumPy ve OpenCV, GIL&#x27;i serbest bıraktığından iş parçacıkları yeterlidir
* 8 GB veya daha az paylaşımlı RAM&#x27;e sahip Jetson cihazları, yürütücüyü tamamen atlar ve işlem içi, sıralı olarak işler
* 7 GB&#x27;nin altında VRAM&#x27;e sahip bir GPU&#x27;da Texture Aware de sıralı olarak çalışır — gürültü giderici model birden fazla sığamaz

Chloros, herhangi bir üçüncü taraf dağıtık çerçeve (Ray gibi) kullanmaz. Stratejinin ve işçi sayısının nasıl seçildiğini öğrenmek için [Dinamik Hesaplama Uyumlaştırması](dynamic-compute-adaptation.md) bölümüne bakın.

***

## Sıralı İşleme ve Boru Hattı İşleme

### Serbest Mod (Sıralı)

Chloros&#x27;un ücretsiz sürümünde, görüntüler **tek tek**, dört aşamanın tümünden sırayla işlenir:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI, ücretsiz modda basitleştirilmiş bir ilerleme çubuğu gösterir; sıralı aşamalar **Hedef Algılama**ve ardından**İşleme** olarak bildirilir.

### Chloros+ Modu (Pipelined)

Chloros+ lisansı ile dört iş parçacığının tümü, farklı görüntüler üzerinde **eşzamanlı olarak** çalışır:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI ilerleme çubuğu dört aşamayı gösterir; üzerine fareyi getirdiğinizde iş parçacığı başına ilerlemeyi görebilirsiniz. CLI&#x27;de aynı dört aşama **Algılama, Analiz, İşleme, Dışa Aktarma** olarak canlı olarak akıtılır.

{% hint style="info" %}
**Tek etiket, iki isim.** CLI, 3. aşamayı _İşleme_ olarak adlandırır. Arka ucun premium mod ilerleme akışı — GUI ilerleme çubuğunun görüntülediği — aynı aşamayı _Kalibrasyon_ olarak etiketler. Bunlar, aynı işi yapan aynı iş parçacığıdır (İş Parçacığı 3: debayer, düzeltmeler, indeksler).
{% endhint %}

{% hint style="success" %}
**Chloros+ ile ardışık işleme**, donanımınıza ve veri kümesi boyutuna bağlı olarak sıralı işlemeye göre 3-5 kat daha hızlı olabilir. Hız artışı, hızlı GPU&#x27;lara ve SSD&#x27;lere sahip sistemlerde en yüksek seviyededir.
{% endhint %}

***

## İş Parçacığı 4 Dışa Aktarma İlerleme Durumu

Dışa aktarma iş parçacığının kendi ilerleme izleme sistemi vardır ve bunu ayrı olarak sorgulayabilirsiniz:**Komut Satırı Arayüzü (CLI):**

```bash
chloros-cli export-status
```

**Yazılım Geliştirme Kiti (SDK):**

```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

İşlem, İş Parçacığı 4 %100&#x27;e ulaştığında tamamlanır.

{% hint style="info" %}
**Görüntü yazmayan bir çalıştırma, başarısızlık olarak kabul edilir.**Başarılı bir işlemde, `chloros-cli process` kaç adet görüntü ürünü yazdığını bildirir (`Image products written: N`). Ürünler talep edildiği halde**hiçbiri**yazılmadıysa — yalnızca `project.json` ve `calibration_data.json` — CLI, `Processing finished but wrote no image products.` mesajını yazdırır ve**sıfırdan farklı bir değerle sonlanır**, proje klasörünün adını ve olağan nedenleri belirtir (giriş klasörü bir yakalama olarak tanınmadı — düzeni kontrol edin ve `--input-level` — veya talep edilen tüm ürünler bu kameralar için uygun değildi). Komut dosyaları çıkış koduna güvenebilir.
{% endhint %}

***

## Dinamik Hesaplama Uyumuyla İlişkisi

[Dinamik Hesaplama Uyumu](dynamic-compute-adaptation.md) öncelikle **İş Parçacığı 3 (İşleme)**&#x27;ü etkiler:

* **`GPU_PARALLEL`**: İş Parçacığı 3, `fused_gpu` iş akışını kullanarak GPU üzerinden aynı anda birden fazla görüntüyü işler
* **`GPU_SINGLE`**: İş Parçacığı 3, `fused_gpu` veya bellek verimliliği yüksek `tiled_gpu` iş akışını kullanarak, işçi süreçleri I/O işlemlerini çakıştırırken semafor ile GPU erişimini sıralı hale getirir
* **`CPU_PARALLEL`**: İş parçacığı 3, çok iş parçacıklı paralellik ile CPU tabanlı işleme kullanır

İş parçacığı 3&#x27;ün GPU bellek tahsisi, iş parçacıkları 1 ve 2 tamamlandıkça da artar — bkz. [Dinamik GPU Bellek Tahsisi](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Sonraki Adımlar

* [Dinamik Hesaplama Uyumlaştırma](dynamic-compute-adaptation.md) — Chloros&#x27;un donanımınız için en uygun stratejiyi nasıl seçtiği
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson&#x27;da platforma özgü iş akışı davranışı
* [İşlemenin İzlenmesi](../processing-images-gui/monitoring-the-processing.md) — GUI üzerinden ilerleme izleme
* [CLI Referansı](../reference/cli-reference.md) — `process`, `export-status`, çıkış kodları ve çıktı düzeni
