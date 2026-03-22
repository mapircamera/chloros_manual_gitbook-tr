# Dinamik İşlem Uyumlaştırma

Chloros 1.1.0 sürümü, akıllı donanım algılama ve otomatik işleme stratejisi seçimi özelliklerini sunuyor. İşleme motoru, Jetson Nano&#x27;dan çoklu GPU&#x27;lu iş istasyonlarına kadar her türlü donanıma manuel bir yapılandırma gerektirmeden uyum sağlar.

***

## Nasıl Çalışır

Chloros başlatıldığında, sisteminizin profilini otomatik olarak oluşturur:

1. **İşletim sistemini algılar** — Windows veya Linux
2. **CPU çekirdeklerini ve toplam RAM&#x27;i tanımlar**

3.**GPU varlığını algılar** — NVIDIA CUDA özelliği, VRAM, model
4. **Jetson modelini tanımlar** (varsa) — `/proc/device-tree/model` aracılığıyla
5. **Termal sensörleri kontrol eder** (Jetson) — sıcaklık duyarlı işleme için
6. **En uygun hesaplama stratejisini seçer** — algılanan tüm donanımlara göre
7. **İşçi sayısını, boru hattı türünü ve bellek tahsisini** otomatik olarak yapılandırır

Sonuç önbelleğe alınır, böylece sonraki çalıştırmalar daha hızlı başlar. Donanımda değişiklik olursa (ör. bir GPU eklenirse), Chloros bir sonraki başlatmada yeniden profil oluşturur.

***

## Hesaplama Stratejileri

Chloros, donanımınıza göre üç hesaplama stratejisinden birini seçer:

| Strateji | GPU Gerekliliği | İşçiler | İş Akışı | En Uygun Olduğu Durum |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`** | Evet (12 GB+ VRAM veya 16 GB+ paylaşımlı) | 3-4 | `fused_gpu` | 12 GB+ masaüstü GPU&#x27;lar, Jetson Orin NX 16 GB, AGX Orin |
| **`GPU_SINGLE`** | Evet (&lt; 12 GB VRAM) | 1-3 | `tiled_gpu` | Giriş seviyesi GPU&#x27;lar, Jetson Nano, Orin Nano |
| **`CPU_PARALLEL`** | Hayır | çekirdekler - 1 | `cpu_fallback` | NVIDIA GPU&#x27;suz sistemler |

### İşlem Hattı Türleri

* **`fused_gpu`** — Tam GPU işleme yolu. Tüm debayer, düzeltme ve indeks işlemleri GPU üzerinde tek bir birleştirilmiş geçişte çalışır. En yüksek verim sağlar ancak daha fazla VRAM gerektirir.
* **`tiled_gpu`** — Bellek verimli GPU yolu. Sınırlı GPU belleğine sığması için görüntüleri döşemeler halinde işler. Daha düşük verim sağlar ancak bellek kısıtlı cihazlarda çalışır.
* **`cpu_fallback`** — Çok iş parçacıklı paralellik kullanan, yalnızca CPU ile işleme. NVIDIA GPU bulunmadığında kullanılır.***

## Platforma Özgü Davranış

| Platform | Strateji | İşçiler | İş Akışı | Notlar |
| --- | --- | --- | --- | --- |
| **Jetson Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (seri) | Bellek verimli mod, her seferinde tek bir görüntüyü işler |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (eşzamanlı) | Önerilen uç cihaz — gerçek paralel GPU işleme |
| **Jetson AGX Orin 64 GB** | `GPU_PARALLEL` | 4 | `fused_gpu` (eşzamanlı) | Maksimum uç performans |
| **8 GB GPU&#x27;lu masaüstü** | `GPU_SINGLE` | 3 | `tiled_gpu` | Bellek verimliliği yüksek döşemelerle iyi masaüstü performansı |
| **12 GB+ GPU&#x27;lu masaüstü** | `GPU_PARALLEL` | 3-4 | `fused_gpu` | Optimum masaüstü performansı |
| **Yalnızca CPU içeren sistem** | `CPU_PARALLEL` | çekirdek - 1 | `cpu_fallback` | GPU gerekmez, ThreadPool kullanır |

{% hint style="info" %}
**Jetson birleşik bellek**: Jetson cihazları GPU ve CPU belleğini paylaşır. Bir Jetson Orin NX 16GB, ~15,3 GB VRAM bildirir, ancak bu, işletim sistemi ve CPU işlemleri tarafından kullanılan fiziksel RAM ile aynıdır. Chloros, bellek tahsis eşiklerini belirlerken bunu hesaba katar.
{% endhint %}

***

## Dinamik GPU Bellek Tahsisi

Chloros, bir [4 iş parçacıklı işleme boru hattı](processing-pipeline.md) kullanır:

* **İş Parçacığı 1** (Algılama) — Görüntü yükleme, EXIF ayrıştırma, hedef algılama
* **İş Parçacığı 2** (Kalibrasyon) — Yansıma kalibrasyonu hesaplaması
* **İş Parçacığı 3** (İşleme) — GPU debayer, vinyet düzeltme, indeks hesaplama
* **İş Parçacığı 4** (Dışa Aktarma) — Dosya yazma, meta veri gömme

Önceki iş akışı iş parçacıkları işlerini tamamladıkça (ör. tüm görüntüler algılandı), GPU bellek tahsisleri serbest bırakılır ve **kalan aktif iş parçacıklarına yeniden dağıtılır**. Bu, iş akışı ilerledikçe İş Parçacığı 3&#x27;ün (GPU yoğun aşama) giderek daha fazla bellek elde ettiği ve en hesaplama yoğun işler için verimi artırdığı anlamına gelir.

### Tahsis Aşamaları

| Aşama | Etkin İş Parçacıkları | GPU Bellek Dağılımı |
| --- | --- | --- |
| **Erken** | 1, 2, 3, 4 | Tüm iş parçacıkları arasında bölünür |
| **Erken-Orta** | 2, 3, 4 | İş parçacığı 1&#x27;in belleği yeniden dağıtılır |
| **Orta-Geç** | 3, 4 | İş parçacıkları 1+2&#x27;nin belleği 3+4&#x27;e aktarılır |
| **Geç** | 3 veya 4 | Kalan iş parçacığı için maksimum bellek |***

## Doku Duyarlı İşleme

Doku Duyarlı debayer yöntemi (yalnızca Chloros+), AI/ML gürültü giderme modeli nedeniyle Standart yönteme göre önemli ölçüde daha fazla GPU belleği kullanır:

* **&lt; 7 GB VRAM**&#x27;e sahip sistemler, Doku Duyarlı mod için senkron işleme döngüsüne zorlanır (her seferinde tek bir görüntü)
* **7 GB+ VRAM**&#x27;e sahip sistemler, Standart yönteme kıyasla daha az işçi sayısıyla da olsa Doku Duyarlı işlemeyi eşzamanlı olarak gerçekleştirebilir***

## Isı Yönetimi (Jetson)

Jetson cihazları, özellikle kapalı veya havada kullanılan kurulumlarda termal kısıtlamalara sahiptir. Chloros, GPU ve CPU sıcaklıklarını izler ve işlemeyi otomatik olarak ayarlar:

| Sıcaklık | Tepki |
| --- | --- |
| **&lt; 70°C** | Normal çalışma — tam hız |
| **70°C** (Uyarı) | İşlem grubu boyutunu azalt |
| **80°C** (Kritik) | Agresif hız düşürme — eşzamanlılık ve işçi sayısını azalt |
| **90°C** (Kapatma) | GPU işlemeyi tamamen durdurun |

Sıcaklık izleme, Jetson platformlarında `tegrastats` kullanır. Yeterli soğutmaya sahip masaüstü sistemlerde, termal hız kesme nadiren tetiklenir.

***

## Bellek Baskısı Yönetimi

Chloros, işleme sırasında sistem bellek baskısını izler:

* **Bellek eşiği**: %85 kullanım, ihtiyatlı davranışı tetikler
* **OOM azaltma**: Bellek yetersizliği olayı meydana gelirse, tahsis %25 oranında azaltılır (0,75x çarpanı)
* **Pipeline geri dönüşü**: Ciddi bellek baskısı altında, boru hattı otomatik olarak `fused_gpu`&#x27;ten `tiled_gpu`&#x27;e geri döner
* **Takas önerileri**: Jetson&#x27;da, veri kümenizin boyutu için takas alanı yetersizse Chloros sizi uyarır***

## Hesaplama Uyumunun İzlenmesi

### CLI Durum Çıktısı

İşleme başladığında, CLI algılanan donanım profilini görüntüler:

```

Chloros CLI 1.1.0
Platform: Linux aarch64 (Jetson Orin NX 16GB)
Strategy: GPU_PARALLEL | Workers: 3 | Pipeline: fused_gpu
CUDA: Available | GPU Memory: 15.3 GB (shared)
```

### Sistem Tanılama

Tam donanım profilini görmek ve hesaplama yeteneklerini doğrulamak için `chloros-cli selftest`&#x27;i çalıştırın:

```bash
chloros-cli selftest
```

Bu, CUDA kullanılabilirliğini, GPU belleğini, gürültü giderici modelleri ve arka uç bağlantısını kontrol eder.

***

## Sonraki Adımlar

* [İşleme Boru Hattı](processing-pipeline.md) — 4 iş parçacıklı boru hattı mimarisini anlama
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson&#x27;a özgü dağıtım ve optimizasyon
* [CLI : Komut Satırı](../CLI.md) — Tam CLI referansı
