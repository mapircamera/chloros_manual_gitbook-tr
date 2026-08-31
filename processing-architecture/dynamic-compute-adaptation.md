# Dinamik İşlem Uyumlaştırma

Chloros 1.2.0, donanım algılama ve otomatik işleme stratejisi seçimi özelliklerini kullanır. İşleme motoru, Jetson Orin Nano’dan çoklu GPU’lu bir iş istasyonuna kadar her türlü donanıma, herhangi bir manuel yapılandırma gerektirmeden uyum sağlar.

***

## Nasıl Çalışır?

Chloros başlatıldığında, sisteminizin profilini çıkarır:

1. **İşletim sistemini algılar** — Windows veya Linux
2. **CPU çekirdeklerini ve toplam RAM&#x27;i belirler**

3.**GPU varlığını algılar** — NVIDIA CUDA özelliği, VRAM, model
4. **Jetson modelini belirler** (varsa) — `/proc/device-tree/model` aracılığıyla
5. **Isı sensörlerini kontrol eder** (Jetson) — sıcaklığa duyarlı işleme için
6. **Hesaplama stratejisini seçer** — algılanan tüm donanımlara göre
7. **İşçi sayısını, iş akışı türünü ve bellek tahsisini** otomatik olarak yapılandırır

Algılanan profil, oturum boyunca bellekte ve diskte önbelleğe alınır, böylece sonraki çalıştırmalar daha hızlı başlar:

| Platform | Önbelleğe alınmış profil |
| --- | --- |
| **Linux / Jetson** | `~/.config/chloros/system_config.json` (`XDG_CONFIG_HOME`&#x27;i kabul eder) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Yeni bir algılama işlemini zorlamak için bu dosyayı silin — bu, bir GPU veya daha fazla RAM ekledikten sonra kullanışlıdır. Chloros, önbellek uyumsuz eski bir sürüm tarafından yazıldığında da otomatik olarak yeniden algılama yapar.

***

## Hesaplama Stratejileri

Chloros, donanımınıza göre üç hesaplama stratejisinden birini seçer:

| Strateji | Ne Zaman Seçilir | İşçiler | Yürütücü | İş Akışı |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**|**12 GB+ VRAM**bildiren CUDA GPU (Jetson birleşik bellekte, ayrıca toplam 12 GB+ paylaşımlı RAM gerektirir) | `min(4, VRAM ÷ 4GB)`, en az 2 —**Jetson&#x27;da 2 ile sınırlı** | `ProcessPoolExecutor` (spawn) | `fused_gpu` |
| **`GPU_SINGLE`**|**2-12GB VRAM**&#x27;e sahip CUDA GPU | 3 (G/Ç çakışması; GPU erişimi bir semafor tarafından sıralanır).**12 GB&#x27;nin altında RAM&#x27;e sahip Jetson&#x27;larda 1 (sıralı)** | `ProcessPoolExecutor` (spawn); düşük RAM&#x27;li Jetson&#x27;larda işlem içi sıralı | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | CUDA GPU yok veya 2 GB&#x27;nin altında VRAM | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

`GPU_PARALLEL` işçi formülüne ilişkin örnekler: 12 GB VRAM → 3 işçi, 16 GB ve üzeri → 4 işçi, herhangi bir Jetson → 2 işçi.

Paralellik, Python&#x27;in standart `concurrent.futures`&#x27;i ile uygulanır: GPU stratejileri, **spawn** başlangıç yöntemine sahip bir `ProcessPoolExecutor` kullanır (her işçi, kendi CUDA bağlamına sahip ayrı bir işlemdir — `fork`, önceden başlatılmış bir CUDA durumunu kopyalar ve alt işlemlere zarar verir) ve CPU stratejisi bir `ThreadPoolExecutor` kullanır. Chloros, herhangi bir üçüncü taraf dağıtık çerçeve (Ray gibi) kullanmaz.

### İş Akışı Türleri

* **`fused_gpu`** — Tam GPU işleme yolu. Debayer, düzeltme ve indeks işlemleri, tek bir birleştirilmiş geçişte GPU üzerinde çalıştırılır. En yüksek verim sunar, en fazla VRAM gerektirir.
* **`tiled_gpu`** — Bellek verimliliği yüksek GPU yolu. Görüntüleri, sınırlı GPU belleğine sığacak şekilde döşeme halinde işler. İşlem hacmi daha düşüktür, ancak bellek kısıtlamaları olan cihazlarda çalışır.
* **`cpu_fallback`** — Çok iş parçacıklı paralellik kullanan, yalnızca CPU ile işleme. NVIDIA GPU bulunmadığında ve her iki GPU yolu da başarısız olduğunda son çare olarak kullanılır.

Çalışma zamanı yedekleme zinciri her zaman `fused_gpu` → `tiled_gpu` → `cpu_fallback` şeklindedir.

***

## Manuel Strateji Geçersiz Kılma

Belirli bir stratejiyi zorlamak için `CHLOROS_STRATEGY` ortam değişkenini ayarlayın — bu, otomatik algılama işleminin durumunuza uygun olmayan bir seçenek belirlediği durumlarda (örneğin, GPU&#x27;yu diğer işler için boş tutmak amacıyla) kullanılabilecek uzman düzeyinde bir yedek çözümdür:

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Değişken, büyük/küçük harf duyarlı olmadan eşleştirilir; bu üç isimden herhangi biri olmayanlar göz ardı edilir ve otomatik algılama normal şekilde devam eder. Bir geçersiz kılma durumunda, Chloros yine de sizin için işçi sayısını seçer:

| Geçersiz kılma | Kullanılan işçi sayısı |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Kalıcı olarak ayarlamak yerine komut başına ayarlamayı tercih edin, böylece normal çalıştırmalarda otomatik uyum sağlama devam eder.

***

## Platforma Özgü Davranış

| Platform | Strateji | İşçiler | İş Akışı | Notlar |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sıralı) | Bellek verimli mod, her seferinde tek bir görüntü |
| **Jetson Orin NX 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sıralı) | 12 GB&#x27;nin altındaki paylaşımlı RAM, sıralı işlemeyi zorunlu kılar |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (eşzamanlı) | Önerilen uç cihaz — Jetson&#x27;da 2 işçi ile sınırlı |
| **Jetson AGX Orin 32-64 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (eşzamanlı) | Maksimum uç performansı (ayrıca Jetson ile 2 işçi ile sınırlı) |
| **8 GB GPU&#x27;lu masaüstü bilgisayar** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 işçi, semafor GPU erişimini sıralarken I/O&#x27;da çakışır |
| **12 GB ve üzeri GPU&#x27;lu masaüstü** | `GPU_PARALLEL` | 3-4 | `fused_gpu` (eşzamanlı) | Optimum masaüstü performansı: 12 GB → 3 işçi, 16 GB+ → 4 |
| **Yalnızca CPU&#x27;lu sistem** | `CPU_PARALLEL` | fiziksel çekirdek sayısı − 1 (en az 2) | `cpu_fallback` | GPU gerekmez, iş parçacığı havuzu kullanır |

{% hint style="info" %}
**Jetson birleşik bellek**: Jetson cihazları, GPU ve CPU belleğini paylaşır. Bir Jetson Orin NX 16 GB, ~15,3 GB VRAM bildirir, ancak bu, işletim sistemi ve CPU işlemleri tarafından kullanılan fiziksel RAM ile aynıdır. Bu nedenle 16 GB+ Jetson&#x27;lar, 12 GB+ masaüstü GPU&#x27;lar gibi `GPU_PARALLEL` için uygundur, ancak 2 işleyici ile sınırlandırılır — GPU, işleyici süreçleri ve işleyici başına CUDA bağlamları, hepsi aynı paylaşılan havuzdan yararlanır.
{% endhint %}

### VRAM&#x27;e Göre GPU Bütçesi (ayrı GPU&#x27;lar)

Ayrı bir NVIDIA GPU’ya sahip x86_64 ana bilgisayarlarda, algılanan VRAM, kartın ne kadar işlem kapasitesini kullanabileceğini ve yığınların ne kadar büyüyebileceğini de belirler:

| Algılanan VRAM | GPU bütçe tavanı | Yığın boyutu çarpanı |
| --- | --- | --- |
| **8 GB ve üzeri** | %90 | ×2,0 |
| **6-8 GB** | %85 | ×1,75 |
| **3,5-6 GB** | %80 | ×1,5 |
| **2-3,5 GB** | %75 | ×1,25 |
| **2 GB&#x27;nin altında** | %70 | ×1,0 |

Ayrı GPU&#x27;lar, sistem RAM&#x27;ini paylaşmadıkları için sistem için yalnızca 0,5 GB ayırır. Jetson profilleri ise çok daha fazla alan ayırır ve daha düşük bir sınır uygular — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Dinamik GPU Bellek Tahsisi

Chloros, [4 iş parçacıklı işleme boru hattı](processing-pipeline.md) kullanır:

* **İş parçacığı 1** (Algılama) — Görüntü yükleme, EXIF ayrıştırma, hedef algılama
* **İş parçacığı 2** (Kalibrasyon) — Yansıtma kalibrasyonu hesaplaması
* **İş parçacığı 3** (İşleme) — GPU debayer, vinyet düzeltme, indeks hesaplama
* **İş Parçacığı 4** (Dışa Aktarma) — Dosya yazma, meta veri gömme

İş parçacıkları 1, 2 ve 4, GPU kaynaklarını az tüketir; İş parçacığı 3 ise en fazla kaynak tüketen iş parçacığıdır. Önceki iş akışı iş parçacıkları tamamlandıkça, GPU kaynakları **kalan aktif iş parçacıklarına yeniden dağıtılır**; böylece İş Parçacığı 3, işlem ilerledikçe giderek daha fazla bellek alır.

### Tahsis Aşamaları

| Aşama | Aktif İş Parçacıkları | GPU Bellek Dağılımı |
| --- | --- | --- |
| **Erken** | 1, 2, 3, 4 | Tüm iş parçacıkları arasında bölünür, çoğu İş Parçacığı 3’e ayrılır |
| **Erken-Orta** | 2, 3, 4 | İş Parçacığı 1’in payı yeniden dağıtılır |
| **Orta-Geç** | 3, 4 | İş parçacıkları 1 ve 2’nin payları 3 ve 4’e aktarılır |
| **Geç** | 3 veya 4 | Son aktif iş parçacığı, profilinin maksimum tahsis miktarını alır |

Bu rakamları belirleyen iki kural vardır:

* **Tek** aktif olan iş parçacığına, profilinin maksimum tahsisi verilir.
* Birden fazla *ağır* GPU görevi aktif olduğunda, her bir ağır görevin temel tahsisi bunlar arasında bölüşülür (asla yapılandırılmış minimum değerin altına düşmez).

Çalışma zamanında fiilen kullanılan değer, platform profilinin tahsisi ile GPU bellek izleyicisinden gelen canlı öneri arasında **daha düşük** olanıdır; bu nedenle, yoğun çalışan bir kart her zaman iyimser bir profilden önceliklidir.***

## Doku Duyarlı İşleme

Texture Aware debayer (**yalnızca Chloros+** — `--debayer texture-aware`), kopya başına FP16&#x27;da yaklaşık 1,75 GB VRAM gerektiren bir AI/ML gürültü giderme modeli çalıştırır; bu nedenle Standart yöntemden çok daha fazla GPU belleği kullanır:

* **7 GB&#x27;den az VRAM**&#x27;a sahip sistemler, Doku Duyarlı işlemeyi**senkron bir döngüde, her seferinde tek bir görüntü** olarak gerçekleştirir — birden fazla model kopyası sığmaz ve bir işçi havuzu sadece çekişmeye yol açar
* **7 GB ve üzeri VRAM**&#x27;e sahip sistemler, Texture Aware&#x27;i eşzamanlı olarak işleyebilir; ancak bu durumda işçi sayısı Standart yönteme kıyasla daha azdır
* **Jetson**&#x27;da, Texture Aware her zaman tek bir işçiye sabitlenir ve düşük güç tüketimli modellerde (Nano, Orin Nano) ayrıca bir GPU frekans sınırı otomatik olarak uygulanır — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)***

## Isı Yönetimi (Jetson)

Jetson cihazlarında, özellikle kapalı alanlarda veya havada kullanılan kurulumlarda termal kısıtlamalar vardır. Chloros, Jetson’un yerleşik sıcaklık sensörlerini izler ve toplu iş boyutlarını otomatik olarak ölçeklendirir:

| Sıcaklık | Tepki |
| --- | --- |
| **&lt; 70°C** | Normal çalışma — tam hız |
| **70°C** (Uyarı) | İşlem grubu boyutu kademeli olarak küçülür (70°C ile 80°C arasında %100 → %50) |
| **80°C** (Kritik) | Aşırı hız kısıtlaması (80°C ile 90°C arasında %50 → %0) |
| **90°C** (Kapatma) | GPU işleme tamamen durdurulur |

Yeterli soğutmaya sahip masaüstü sistemlerde, termal hız kısıtlaması nadiren tetiklenir.

***

## Bellek Baskısı Yönetimi

Chloros, işleme sırasında GPU belleğini sürekli olarak izler ve üç seviyede tepki verir.

**Toplu iş boyutu.** Bir toplu iş, yukarıdaki tablolardaki platform çarpanının 8 görüntü ile çarpımıyla başlar. Chloros daha sonra boş VRAM&#x27;i kontrol eder, bunun %20&#x27;sini PyTorch&#x27;un kendi ek yükü için ayırır ve 12 MP&#x27;lik bir görüntü başına yaklaşık 100 MB GPU belleği varsayar — parti boyutu, bellekten türetilen sınır ile platform temel değeri arasında hangisi daha küçükse o olur. Asla 1&#x27;in altına düşmez.**Önleyici azaltma.** **%85 VRAM kullanım** oranının üzerinde, herhangi bir hata oluşmadan önce parti boyutları azaltılır.**İş parçacığı başına ayırma kısıtlaması.** Canlı kullanım oranı yükseldikçe, her iş parçacığının GPU bütçesi azaltılır: %80&#x27;in üzerindeki kullanımda ×0,75, %90&#x27;ın üzerindeki kullanımda ×0,5. İzleme aralıkları %70 (ihtiyatlı), %85 (normal çalışma sınırı) ve %95 (OOM riski) şeklindedir.**OOM geri çekilme ve kurtarma.** Yine de bir bellek yetersizliği olayı meydana gelirse:

* toplu iş boyutu **yarıya indirilir** ve ardışık her OOM durumunda tekrar yarıya indirilir — sonraki her başarılı toplu iş, bu cezayı bir adım geri alır
* aktif iş parçacığı tahsisleri mevcut değerlerinin %70&#x27;ine düşürülür ve tahsis edici, ihtiyatlı stratejisine geçer; başarılı tahsislerin ardından tekrar esnek hale gelir
* Aşırı baskı altında, iş akışı `fused_gpu`&#x27;ten `tiled_gpu`&#x27;e ve son çare olarak `cpu_fallback`&#x27;e geri döner

**Ana Bilgisayar RAM&#x27;i (Jetson).** İşleme öncesinde, CLI, görüntü sayınız ve debayer modunuzdan en yüksek ana bilgisayar belleği değerini tahmin eder ve RAM ile dosya destekli takas alanının yetersiz kalma ihtimali varsa uyarı verir; takas alanı eklemek için gerekli komutları tam olarak yazdırır — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).***

## Hesaplama Uyumunun İzlenmesi

### Sistem Tanılama

`chloros-cli selftest`, hesaplama katmanının ne gördüğünü doğrulamanın en hızlı yoludur:

```bash
chloros-cli selftest
```

Bu komutun 7 kontrolü, sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, `/api/test`, sistem bilgileri, gürültü giderici modelinin varlığı ve CUDA + gürültü giderici hazırlık durumunu kapsar. 5. kontrol, donanım satırını doğrudan görüntüler:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

7. kontrol, `CUDA: <bool>, Denoiser: <bool>`&#x27;i görüntüler — Texture Aware&#x27;in kullanılabilmesi için her ikisinin de doğru olması gerekir.

### Arka Uç Günlükleri

Strateji ve işçi sayısı, her çalıştırmanın başında arka uçta seçilir — bunları bildiren bir CLI başlığı yoktur. Bir şey beklenmedik bir şekilde davranırsa (bir GPU yolunun geri dönüşü, bir OOM, yüklenmeyen bir gürültü giderici gibi), bu durum o oturumun arka uç günlüğünde görünür:

| Platform | Günlük konumu |
| --- | --- |
| **Linux / Jetson** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (her başlatma için bir dosya) |
| **Linux, CLI-başlatılan arka uç** | ayrıca `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Canlı İlerleme

Çalıştırma sırasında, CLI, Server-Sent Events üzerinden aktarılan iş parçacığı başına canlı ilerleme durumunu (Algılama, Analiz, İşleme, Dışa Aktarma) gösterir — bu, İş Parçacığı 3&#x27;ün darboğaz olup olmadığını anlamak için pratik bir göstergedir. Bkz. [İşleme Boru Hattı](processing-pipeline.md).

***

## Sonraki Adımlar

* [İşleme Boru Hattı](processing-pipeline.md) — 4 iş parçacıklı boru hattı mimarisini anlama
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson&#x27;a özgü dağıtım ve optimizasyon
* [CLI : Komut Satırı](../CLI.md) — CLI kılavuzu
* [CLI Referansı](../reference/cli-reference.md) — 1.2.0 sürümü için kapsamlı komut listesi
