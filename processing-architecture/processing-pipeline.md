# İşleme Boru Hattı

Chloros 1.1.0, aşamalı bir montaj hattı gibi çalışan 4 iş parçacıklı bir işleme boru hattı kullanır. Her iş parçacığı, işleme akışının farklı bir aşamasını üstlenir; böylece birden fazla görüntü, farklı aşamalarda eşzamanlı olarak işlenebilir.

***

## Boru Hattı Mimarisi

```

Images In → [Thread 1: Detection] → [Thread 2: Calibration] → [Thread 3: Processing] → [Thread 4: Export] → Files Out
```

Her görüntü, dört iş parçacığının tümünden sırayla geçer. Chloros+ çok iş parçacıklı işleme ile, birden fazla görüntü aynı anda farklı iş parçacıklarında bulunabilir — İş Parçacığı 3 bir görüntüyü işlerken, İş Parçacığı 1 bir sonrakini algılayabilir, İş Parçacığı 2 başka bir görüntüyü kalibre edebilir ve İş Parçacığı 4 daha önce işlenmiş bir görüntüyü diske yazabilir.

***

## İş Parçacığı Ayrıntıları

### İş Parçacığı 1: Algılama

**Amaç**: Görüntüleri yüklemek ve kalibrasyon hedeflerini algılamak.

* Diskten görüntü dosyalarını okur (RAW, JPG)
* EXIF meta verilerini çıkarır (GPS, kamera modeli, zaman damgaları, pozlama)
* İşaretli hedef görüntülerde ArUco kalibrasyon hedeflerini algılar
* Çıktılar: görüntü verileri + meta veriler + hedef algılama sonuçları

Bu, öncelikle bir I/O ve CPU bağımlı iş parçacığıdır.

### İş Parçacığı 2: Kalibrasyon

**Amaç**: Algılanan hedeflerden kalibrasyon parametrelerini hesaplamak.

* Hedef görüntülerden yansıma kalibrasyon katsayılarını hesaplar
* Vinyet düzeltme parametrelerini hesaplar
* Bant başına kalibrasyon eğrilerini belirler
* Çıktılar: her görüntü için kalibrasyon parametreleri

Bu, CPU&#x27;ya bağlı bir hesaplama iş parçacığıdır.

### İş Parçacığı 3: İşleme (GPU)

**Amaç**: Düzeltmeleri uygular ve bitki örtüsü indekslerini hesaplar.**Bu, en yoğun hesaplama gerektiren iş parçacığıdır.*** **Debayering**: RAW Bayer desen verilerini çok kanallı görüntülere dönüştürür
  * Standart (Hızlı, Orta Kalite) — varsayılan
  * Doku Duyarlı (Yavaş, En Yüksek Kalite) — yalnızca Chloros+, AI/ML gürültü giderme kullanır
* **Vinyet düzeltme**: Görüntü genelinde lens vinyet düzeltmesi uygular
* **Yansıma kalibrasyonu**: Yansıma değerlerine dönüştürmek için kalibrasyon katsayılarını uygular
* **Endeks hesaplama**: Bitki örtüsü endekslerini hesaplar (NDVI, NDRE, GNDVI, vb.)
* Çıktılar: dışa aktarılmaya hazır işlenmiş görüntü verileri

Bu iş parçacığı, GPU hızlandırmasından en fazla yararlanan iş parçacığıdır. [Dinamik Hesaplama Uyumlaştırma](dynamic-compute-adaptation.md) sistemi, öncelikle bu iş parçacığının davranışını optimize eder.

### İş Parçacığı 4: Dışa Aktarma

**Amaç**: İşlenmiş görüntüleri diske yazmak.

* Çıktı dosyalarını seçilen formatta yazar (TIFF 16 bit, TIFF 32 bit %, PNG, JPG)
* Çıktı dosyalarına EXIF meta verilerini gömer (GPS, zaman damgaları, işleme parametreleri)
* Çıktıları kamera modeli alt klasörlerine düzenler
* Çıktılar: diskteki son dosyalar

Bu, esas olarak I/O&#x27;ya bağlı bir iş parçacığıdır. SSD depolama, İş Parçacığı 4&#x27;ün performansını önemli ölçüde artırır.

***

## Sıralı İşleme ve Boru Hattı İşleme

### Ücretsiz Mod (Sıralı)

Chloros&#x27;in ücretsiz sürümünde, görüntüler dört aşamanın tümünden **tek tek**, sırayla işlenir:

```

Image 1: [Detect] → [Calibrate] → [Process] → [Export]
                                                         Image 2: [Detect] → [Calibrate] → [Process] → [Export]
```

GUI ilerleme çubuğu 2 aşamayı gösterir: Hedef Algılama ve İşleme.

### Chloros+ Modu (Pipelined)

Chloros+ lisansıyla, dört iş parçacığının tümü farklı görüntüler üzerinde **eşzamanlı** olarak çalışır:

```

Thread 1: [Image 1] [Image 2] [Image 3] [Image 4] ...
Thread 2:           [Image 1] [Image 2] [Image 3] ...
Thread 3:                     [Image 1] [Image 2] ...
Thread 4:                               [Image 1] ...
```

GUI ilerleme çubuğu 4 aşamayı gösterir: Algılama, Analiz, Kalibrasyon, Dışa Aktarma. İş parçacığı başına ilerlemeyi görmek için ilerleme çubuğunun üzerine gelin.

{% hint style="success" %}
**Chloros+ ile ardışık işleme**, donanımınıza ve veri kümesi boyutuna bağlı olarak sıralı işlemeden 3-5 kat daha hızlı olabilir. Hız artışı, hızlı GPU&#x27;lara ve SSD&#x27;lere sahip sistemlerde en yüksek seviyededir.
{% endhint %}

***

## İş Parçacığı 4 Dışa Aktarma İlerleme Durumu

Chloros 1.1.0 sürümünde, dışa aktarma iş parçacığı (İş Parçacığı 4) kendi özel ilerleme izleme sistemine sahiptir. Dışa aktarma ilerlemesini ayrı olarak izleyebilirsiniz:**CLI:**
```bash
chloros-cli export-status
```

**SDK:**
```python
status = chloros.get_status()
print(f"Export: {status['export']['percent']}% - Phase: {status['export']['phase']}")
```

İşlem, İş Parçacığı 4 %100&#x27;e ulaştığında tamamlanır.

***

## Dinamik Hesaplama Uyumuyla İlişkisi

[Dinamik Hesaplama Uyumlaştırma](dynamic-compute-adaptation.md) sistemi öncelikle **İş Parçacığı 3 (İşleme)**&#x27;ü etkiler:

* **`GPU_PARALLEL`** stratejisi: İş Parçacığı 3, `fused_gpu` boru hattını kullanarak GPU üzerinden aynı anda birden fazla görüntüyü işler
* **`GPU_SINGLE`** stratejisi: İş Parçacığı 3, bellek verimliliği yüksek `tiled_gpu` ardışık düzenini kullanarak bir seferde tek bir görüntüyü işler
* **`CPU_PARALLEL`** stratejisi: İş Parçacığı 3, çok iş parçacıklı paralellik ile CPU tabanlı işleme kullanır

İş parçacığı 3&#x27;ün GPU bellek tahsisi de iş parçacıkları 1 ve 2 tamamlandıkça dinamik olarak değişir — bkz. [Dinamik GPU Bellek Tahsisi](dynamic-compute-adaptation.md#dynamic-gpu-memory-allocation).

***

## Sonraki Adımlar

* [Dinamik Hesaplama Uyumlaştırma](dynamic-compute-adaptation.md) — Chloros&#x27;in donanımınız için en uygun stratejiyi nasıl seçtiği
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson&#x27;da platforma özgü iş akışı davranışı
* [İşlemenin İzlenmesi](../processing-images-gui/monitoring-the-processing.md) — GUI ilerleme izleme
