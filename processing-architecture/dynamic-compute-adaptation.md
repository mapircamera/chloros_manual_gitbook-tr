# Dinamik İşlem Uyumlaştırma

Chloros 1.3.0, donanım algılama ve otomatik işlem stratejisi seçimi özelliklerini kullanır. İşlem motoru, Jetson Orin Nano’dan çoklu GPU’lu bir iş istasyonuna kadar her türlü donanıma, herhangi bir manuel yapılandırma gerektirmeden uyum sağlar.

***

## Nasıl Çalışır?

Chloros başlatıldığında, sisteminizin profilini çıkarır:

1. **İşletim sistemini algılar** — Windows veya Linux
2. **CPU çekirdeklerini ve toplam RAM'i belirler**

3.**GPU varlığını algılar** — NVIDIA CUDA özelliği, VRAM, model
4. **Jetson modelini belirler** (varsa) — `/proc/device-tree/model` aracılığıyla
5. **Isı sensörlerini kontrol eder** (Jetson) — sıcaklığa duyarlı işleme için
6. **İşleme stratejisini seçer** — algılanan tüm donanımlara göre
7. **İşçi sayısını, iş akışı türünü ve bellek tahsisini** otomatik olarak yapılandırır

Algılanan profil, oturum için bellekte ve diskte önbelleğe alınır, böylece sonraki çalıştırmalar daha hızlı başlar:

| Platform | Önbelleğe alınmış profil |
| --- | --- |
| **Linux / Jetson** | `~/.config/chloros/system_config.json` (`XDG_CONFIG_HOME`'i de destekler) |
| **Windows** | `%LOCALAPPDATA%\Chloros\config\system_config.json` |

Yeni bir algılama işlemini zorlamak için bu dosyayı silin — bu, bir GPU veya daha fazla RAM ekledikten sonra yararlıdır. Chloros, önbellek uyumsuz bir eski sürüm tarafından yazıldığında da otomatik olarak yeniden algılama yapar.

***

## Hesaplama Stratejileri

Chloros, donanımınıza göre üç hesaplama stratejisinden birini seçer:

| Strateji | Ne Zaman Seçilir | İşçiler | Yürütücü | İş Akışı |
| --- | --- | --- | --- | --- |
| **`GPU_PARALLEL`**|**12 GB+ VRAM**bildiren CUDA GPU (Jetson birleşik belleğinde, ayrıca toplam 12 GB+ paylaşımlı RAM gerektirir) | `min(4, ⌊(VRAM − 0.5GB) × 0.9 ÷ 3.5GB⌋)`, en az 2 —**Jetson'da 2 ile sınırlı** | İşçi süreçleri (ayrı CUDA bağlamları) | `fused_gpu` |
| **`GPU_SINGLE`**|**2-12GB VRAM**'e sahip CUDA GPU | 3 (G/Ç çakışması; GPU erişimi semaforla sıralanır).**12 GB'nin altında RAM'e sahip Jetson'larda 1 (sıralı)** | İşçi süreçleri (ayrı CUDA bağlamları); düşük RAM'li Jetson'larda süreç içi sıralı | `fused_gpu` / `tiled_gpu` |
| **`CPU_PARALLEL`** | CUDA GPU yok veya 2 GB'nin altında VRAM | `max(2, physical cores − 1)` | `ThreadPoolExecutor` | `cpu_fallback` |

`GPU_PARALLEL` işçi formülüne ilişkin örnekler: 12 GB VRAM → 2 işçi, 16 GB → 3 işçi, 20 GB+ → 4 işçi. Her işleyiciye, 0,5 GB'lık bir yedek ayrıldıktan sonra kalan VRAM'ın %90'ı dahilinde, Texture Aware görevinin ölçülen ihtiyacı olan 3,5 GB tahsis edilir.

{% hint style="info" %}
**GPU stratejileri, CUDA kullanabilen bir PyTorch gerektirir.** PyTorch'unda kullanılabilir CUDA aygıtı bulunmayan bir makine, GPU'su ne olursa olsun `CPU_PARALLEL` değerini alır. 1.3.0 Jetson paketi, JetPack 6’nın CUDA sürümündeki PyTorch’u kullanır; bu nedenle, JetPack’in CUDA ve cuDNN kütüphaneleri yüklendiğinde bir Jetson uygun hale gelir. 12 GB’nin altında RAM’e sahip bir Jetson, işlem sırasında her zaman tek seferde bir görüntüyü işler (`GPU_SINGLE` olarak kaydedilir).
{% endhint %}

GPU stratejileri, her biri kendi CUDA bağlamına sahip ayrı bir işletim sistemi süreci olan bir işçi süreci havuzu çalıştırır; Chloros, bunu kendi kurulu arka uç programını yeniden başlatarak gerçekleştirir (Python'un `multiprocessing`'i aracılığıyla değil; çünkü bu programın işçi başlatma işlevi, paketlenmiş uygulama içinde çalışmaz). CPU stratejisi bir iş parçacığı havuzu kullanır (Python'un `ThreadPoolExecutor`'i). Chloros, herhangi bir üçüncü taraf dağıtık çerçeve (Ray gibi) kullanmaz.

### İş Akışı Türleri

* **`fused_gpu`** — Tam GPU işleme yolu. Debayer, düzeltme ve indeks işlemleri, tek bir birleştirilmiş geçişte GPU üzerinde çalıştırılır. En yüksek verim sunar, en fazla VRAM gerektirir.
* **`tiled_gpu`** — Bellek verimliliği yüksek GPU yolu. Görüntüleri, sınırlı GPU belleğine sığacak şekilde döşemeler halinde işler. Verim daha düşüktür ancak bellek kısıtlı cihazlarda çalışır.
* **`cpu_fallback`** — Çok iş parçacıklı paralellik kullanan, yalnızca CPU tabanlı işleme. NVIDIA GPU bulunmadığında ve her iki GPU yolu da başarısız olduğunda son çare olarak kullanılır.

Çalışma zamanı yedekleme zinciri her zaman `fused_gpu` → `tiled_gpu` → `cpu_fallback` şeklindedir.

***

## Manuel Strateji Geçersiz Kılma

Belirli bir stratejiyi zorlamak için `CHLOROS_STRATEGY` ortam değişkenini ayarlayın — bu, otomatik algılama işleminin durumunuza uygun olmayan bir seçenek seçtiği durumlarda (örneğin, GPU’yu diğer işler için boş tutmak amacıyla) kullanılabilecek uzman düzeyinde bir kaçış yoludur:

```bash
# Valid values: CPU_PARALLEL, GPU_SINGLE, GPU_PARALLEL
CHLOROS_STRATEGY=CPU_PARALLEL chloros-cli process ~/datasets/flight001
```

Değişken, büyük/küçük harf duyarlı olmayan bir şekilde eşleştirilir; bu üç isimden herhangi biri olmayanlar yok sayılır ve otomatik algılama normal şekilde devam eder. Bir geçersiz kılma durumunda bile Chloros, işçi sayısını sizin için seçmeye devam eder:

| Geçersiz kılma | Kullanılan işçi sayısı |
| --- | --- |
| `CPU_PARALLEL` | `max(2, physical cores − 1)` |
| `GPU_SINGLE` | 3 |
| `GPU_PARALLEL` | `min(4, physical cores)` |

Kullanılabilir CUDA aygıtı bulunmayan bir makinede `GPU_SINGLE` veya `GPU_PARALLEL` geçersiz kılma işlemi, günlüğünde `[STRATEGY]` satırı ile reddedilir ve çalıştırma işlemi `CPU_PARALLEL` değerini kullanır.

Kalıcı olarak ayarlamak yerine komut bazında ayarlamayı tercih edin, böylece normal çalıştırmalar otomatik olarak uyum sağlamaya devam eder.

***

## Platforma Özgü Davranış

| Platform | Strateji | İşçiler | İş Akışı | Notlar |
| --- | --- | --- | --- | --- |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sıralı) | Bellek verimli mod, her seferinde tek görüntü |
| **Jetson Orin NX 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (sıralı) | 12 GB'nin altındaki paylaşımlı RAM, sıralı işlemeyi zorunlu kılar |
| **Jetson Orin NX 16 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (eşzamanlı) | Önerilen uç cihaz — Jetson'da 2 işçi ile sınırlı |
| **Jetson AGX Orin 32-64 GB** | `GPU_PARALLEL` | 2 | `fused_gpu` (eşzamanlı) | Maksimum uç performansı (Jetson'da da 2 işçi ile sınırlı) |
| **8 GB GPU'lu masaüstü** | `GPU_SINGLE` | 3 | `fused_gpu` / `tiled_gpu` | 3 işçi, semafor GPU erişimini sıralarken I/O işlemlerini çakıştırır |
| **12 GB ve üzeri GPU'lu masaüstü** | `GPU_PARALLEL` | 2-4 | `fused_gpu` (eşzamanlı) | Optimum masaüstü performansı: 12 GB → 2 iş parçacığı, 16 GB → 3, 20 GB+ → 4 |
| **Yalnızca CPU'lu sistem** | `CPU_PARALLEL` | fiziksel çekirdek sayısı − 1 (en az 2) | `cpu_fallback` | GPU gerekmez, iş parçacığı havuzu kullanır |

{% hint style="info" %}
**Jetson birleşik bellek**: Jetson cihazları, GPU ve CPU belleğini paylaşır. 16 GB'lık bir Jetson Orin NX, ~15,3 GB VRAM bildirir, ancak bu, işletim sistemi ve CPU işlemleri tarafından kullanılan fiziksel RAM ile aynıdır. Bu nedenle 16 GB ve üzeri Jetson'lar, 12 GB ve üzeri masaüstü GPU'lar gibi `GPU_PARALLEL` için uygun olurken, işçi sayısı 2 ile sınırlıdır — GPU, işçi süreçleri ve işçi başına CUDA bağlamları, hepsi aynı paylaşılan havuzdan yararlanır.
{% endhint %}

### VRAM'e Göre GPU Bütçesi (ayrı GPU'lar)

Ayrı bir NVIDIA GPU'ya sahip x86_64 ana bilgisayarlarda, algılanan VRAM, kartın ne kadar işlem kapasitesini kullanabileceğini ve iş parçacıklarının ne kadar büyüyebileceğini de belirler:

| Algılanan VRAM | GPU bütçe üst sınırı | Yığın boyutu çarpanı |
| --- | --- | --- |
| **8 GB ve üzeri** | %90 | ×2,0 |
| **6-8 GB** | %85 | ×1,75 |
| **3,5-6 GB** | %80 | ×1,5 |
| **2-3,5 GB** | %75 | ×1,25 |
| **2 GB'nin altında** | %70 | ×1,0 |

Ayrı GPU'lar, sistem RAM'ini paylaşmadıkları için sistem için yalnızca 0,5 GB ayırır. Jetson profilleri ise çok daha fazla bellek ayırır ve sınırı daha düşük tutar — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#per-model-gpu-budget).

***

## Dinamik GPU Bellek Tahsisi

Chloros, [4 iş parçacıklı bir işleme boru hattı](processing-pipeline.md) kullanır:

* **İş Parçacığı 1** (Algılama) — Görüntü yükleme, EXIF ayrıştırma, hedef algılama
* **İş Parçacığı 2** (Kalibrasyon) — Yansıma kalibrasyonu hesaplaması
* **İş Parçacığı 3** (İşleme) — GPU debayer, vinyet düzeltme, indeks hesaplama
* **İş Parçacığı 4** (Dışa Aktarma) — Dosya yazma, meta veri gömme

İş parçacıkları 1, 2 ve 4, GPU kaynaklarını az tüketir; 3. İş Parçacığı ise en fazla kaynak tüketen iş parçacığıdır. Önceki iş akışı iş parçacıkları tamamlandıkça, bunların GPU kaynakları **kalan aktif iş parçacıklarına yeniden dağıtılır**; böylece iş ilerledikçe 3. İş Parçacığı giderek daha fazla bellek alır.

### Tahsis Aşamaları

| Aşama | Aktif İş Parçacıkları | GPU Bellek Dağılımı |
| --- | --- | --- |
| **Erken** | 1, 2, 3, 4 | Tüm iş parçacıkları arasında bölünür, çoğu İş Parçacığı 3’e ayrılır |
| **Erken-Orta** | 2, 3, 4 | İş Parçacığı 1’in payı yeniden dağıtılır |
| **Orta-Geç** | 3, 4 | İş parçacıkları 1 ve 2'nin payları 3 ve 4'e aktarılır |
| **Geç** | 3 veya 4 | Son aktif iş parçacığı, profilinin maksimum tahsis miktarını alır |

Bu rakamları belirleyen iki kural vardır:

* **Tek** aktif olan iş parçacığına, profilinin maksimum tahsisi verilir.
* Birden fazla *ağır* GPU görevi aktif olduğunda, her bir ağır görevin temel tahsisi bunlar arasında bölüşülür (asla yapılandırılmış minimum değerin altına düşmez).

Çalışma sırasında fiilen kullanılan değer, platform profilindeki tahsis ile GPU bellek monitöründen gelen canlı öneri arasında **daha düşük** olanıdır; bu nedenle, yoğun çalışan bir kart her zaman iyimser bir profilden önceliklidir.

***

## Doku Duyarlı İşleme

Texture Aware debayer (**yalnızca Chloros+** — `--debayer texture-aware`), kopya başına FP16'da yaklaşık 1,75 GB VRAM gerektiren bir AI/ML gürültü giderme modeli çalıştırır; bu nedenle Standart yöntemden çok daha fazla GPU belleği kullanır:

* **7 GB'nin altında VRAM**'e sahip sistemler, Doku Duyarlı işlemeyi**senkron bir döngüde, her seferinde tek bir görüntü** olarak gerçekleştirir — birden fazla model kopyası sığmaz ve bir işçi havuzu sadece çekişmeye neden olur
* **7 GB ve üzeri VRAM**'e sahip sistemler, Texture Aware'i eşzamanlı olarak işleyebilir, ancak bu işlem Standart yönteme kıyasla daha az işçi sayısıyla gerçekleştirilir
* **Jetson**'da Texture Aware her zaman tek bir işleyiciye sabitlenir
* **Kullanılabilir CUDA GPU** olmadığında, işlem Standart debayer'ı kullanır ve arka uç, bunun nedenini belirten bir `[DEBAYER] … needs a CUDA GPU …` satırı günlüğe kaydeder
* Düşük güç tüketimli Jetson'larda (Nano, Orin Nano), Texture Aware talep edildiğinde CLI, GPU frekans sınırlaması uygular — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#gpu-frequency-cap-for-texture-aware-on-nano-and-orin-nano)

***

## Isı Yönetimi (Jetson)

Jetson cihazlarında, özellikle kapalı ortamlarda veya havada kullanılan kurulumlarda termal kısıtlamalar vardır. Chloros, Jetson’un yerleşik sıcaklık sensörlerini izler ve toplu iş boyutlarını otomatik olarak ölçeklendirir:

| Sıcaklık | Tepki |
| --- | --- |
| **< 70°C** | Normal çalışma — tam hız |
| **70°C** (Uyarı) | İşlem grubu boyutu kademeli olarak küçülür (70°C ile 80°C arasında %100 → %50) |
| **80°C** (Kritik) | Aşırı hız kısıtlaması (80°C ile 90°C arasında %50 → %0) |
| **90°C** (Kapatma) | GPU işlemeyi tamamen durdurur |

Yeterli soğutmaya sahip masaüstü sistemlerde, termal hız kısıtlaması nadiren tetiklenir.

***

## Bellek Baskısı Yönetimi

Chloros, işleme sırasında GPU belleğini sürekli izler ve üç seviyede tepki verir.

**Parti boyutlandırma.** Bir parti, yukarıdaki tablolardaki platform çarpanının 8 görüntü ile çarpımıyla başlar. Chloros daha sonra boş VRAM'i kontrol eder, bunun %20'sini PyTorch'un kendi ek yükü için ayırır ve 12 MP'lik bir görüntü başına yaklaşık 100 MB GPU belleği varsayar — parti, bellekten türetilen sınır ile platform taban değeri arasında hangisi daha küçükse o olur. Asla 1'in altına düşmez.

**Önleyici azaltma.** **%85 VRAM kullanım** oranının üzerine çıkıldığında, herhangi bir hata oluşmadan önce parti boyutları küçültülür.**İş parçacığı başına ayırma kırpma.** Canlı kullanım oranı yükseldikçe, her iş parçacığının GPU bütçesi azaltılır: %80'in üzerindeki kullanımda ×0,75, %90'ın üzerindeki kullanımda ×0,5. İzleme aralıkları %70 (ihtiyatlı), %85 (normal çalışma sınırı) ve %95’tir (OOM riski).**OOM geri çekilme ve kurtarma.** Yine de bir bellek yetersizliği olayı meydana gelirse:

* toplu iş boyutu **yarıya indirilir** ve ardışık her OOM durumunda tekrar yarıya indirilir — sonraki her başarılı toplu iş, bu cezayı bir adım geri alır
* aktif iş parçacığı tahsisleri mevcut değerlerinin %70'ine düşürülür ve tahsis edici, ihtiyatlı stratejisine geçer; başarılı tahsislerin ardından tekrar esnekleşir
* Aşırı yük altında, iş akışı `fused_gpu`'ten `tiled_gpu`'e, son çare olarak da `cpu_fallback`'e geri döner

**Ana Bilgisayar RAM'i (Jetson).** İşleme başlamadan önce, CLI, görüntü sayınız ve debayer moduna göre en yüksek ana bilgisayar belleği değerini tahmin eder ve RAM ile dosya tabanlı takas alanının yetersiz kalma ihtimali varsa uyarı verir; takas alanı eklemek için gerekli komutları tam olarak görüntüler — bkz. [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md#swap-warning-and-recommendations).

***

## Hesaplama Uyumunun İzlenmesi

### Sistem Tanılama

`chloros-cli selftest`, hesaplama katmanının ne gördüğünü doğrulamanın en hızlı yoludur:

```bash
chloros-cli selftest
```

Bu komutun 7 kontrolü, sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, `/api/test`, sistem bilgileri, gürültü giderici modelinin varlığı ve CUDA + gürültü giderici hazırlık durumunu kapsar. 5 numaralı kontrol, donanım satırını doğrudan görüntüler:

```
      GPU: NVIDIA RTX A4000, CUDA: True, PyTorch: 2.7.0
```

7. kontrol, `CUDA: <bool>, Denoiser: <bool>` değerini yazdırır — Texture Aware'in kullanılabilmesi için her ikisinin de doğru olması gerekir.

### Arka Uç Günlükleri

Strateji ve işçi sayısı, her çalıştırmanın başında arka uçta seçilir — bunları bildiren bir CLI başlığı yoktur. Bir şey beklenmedik şekilde davranırsa (bir GPU yolunun geri dönüşü, bir OOM, yüklenmeyen bir gürültü giderici), bu durum o oturumun arka uç günlüğünde görünür:

| Platform | Günlük konumu |
| --- | --- |
| **Linux / Jetson** | `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` (her başlatma için bir dosya) |
| **Linux, CLI ile başlatılan arka uç** | ayrıca `~/.chloros/backend.log` |
| **Windows** | `%LOCALAPPDATA%\Chloros\logs\` |

### Canlı İlerleme

Çalıştırma sırasında, CLI, Server-Sent Events üzerinden aktarılan iş parçacığı başına canlı ilerleme durumunu (Algılama, Analiz, İşleme, Dışa Aktarma) gösterir — bu, İş Parçacığı 3’ün darboğaz olup olmadığını anlamak için pratik bir göstergedir. Bkz. [İşleme Boru Hattı](processing-pipeline.md).

***

## Sonraki Adımlar

* [İşleme Boru Hattı](processing-pipeline.md) — 4 iş parçacıklı boru hattı mimarisini anlama
* [NVIDIA Jetson Kılavuzu](../linux/nvidia-jetson-guide.md) — Jetson'a özgü dağıtım ve optimizasyon
* [CLI: Komut Satırı](../CLI.md) — CLI kılavuzu
* [CLI Referansı](../reference/cli-reference.md) — 1.3.0 sürümü için kapsamlı komut listesi
