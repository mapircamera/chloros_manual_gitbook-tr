# İşlemeyi Başlatma

Görüntülerinizi içe aktardıktan, kalibrasyon hedeflerinizi işaretledikten ve proje ayarlarınızı yapılandırdıktan sonra, işlemeyi başlatmaya hazırsınız. Bu sayfa, Chloros işleme akışını başlatma sürecinde size rehberlik eder.

## İşleme Öncesi Kontrol Listesi

Başlat düğmesine tıklamadan önce her şeyin hazır olduğunu kontrol edin:

* [ ] **Dosyalar içe aktarıldı** - Tüm görüntüler Dosya Tarayıcısında görünür
* [ ] **Hedef görüntüler işaretlendi** - Kalibrasyon görüntüleri için Hedef sütunu işaretlendi (veya LATTICE için içe aktarılan bir `.daq` kaydı)
* [ ] **Kamera modelleri algılandı** - Kamera Modeli sütununda doğru kameralar gösteriliyor
* [ ] **Ayarlar yapılandırıldı** - Proje Ayarları gözden geçirildi ve ayarlandı
* [ ] **İndeksler seçildi** - İstenen multispektral indeksler eklendi (gerekirse)
* [ ] **Dışa aktarım biçimi seçildi** - İş akışınıza uygun çıktı biçimi

{% hint style="info" %}
**İpucu**: İşlemeye başlamadan önce Dosya Tarayıcısı&#x27;nda birkaç görüntüye tıklayarak bunların doğru şekilde yüklendiğini doğrulayın.
{% endhint %}

***

## İşlemeyi Başlatma

### Başlat Düğmesini Bulma

Başlat/Oynat düğmesi, Chloros&#x27;in üst başlık çubuğunda yer alır:

* Konum: Pencerenin üst orta kısmı
* Simge: **Oynat/Başlat düğmesi** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Durum: İşlemeye hazır olduğunda düğme etkin hale gelir (parlak)

### Başlatmak için Tıklayın

1. Üst başlıktaki **Oynat/Başlat düğmesine** tıklayın
2. İşleme hemen başlar
3. İşleme sırasında düğme bir **Durdur** düğmesine dönüşür
4. İlerleme çubuğu güncellenerek işleme durumunu gösterir

{% hint style="success" %}
**İşleme Başladı**: Tıklandığında, Chloros tüm işleme adımlarını (hedef algılama, debayering, kalibrasyon, indeks hesaplama ve dışa aktarma) otomatik olarak gerçekleştirir. Projenizin Survey3, LATTICE veya bunların bir karışımı olup olmadığını otomatik olarak algılar ve her kameraya uygun işleme akışını uygular.
{% endhint %}

***

## İşleme Modlarını Anlama

Chloros, lisansınıza bağlı olarak iki farklı işleme modunda çalışır:

### Ücretsiz Mod (Sıralı İşleme)

**Tüm kullanıcılar için kullanılabilir**

**Nasıl çalışır:**

* Görüntüleri tek tek, sırayla işler
* Tek iş parçacıklı çalışma
* Daha düşük bellek kullanımı

**İlerleme çubuğu 2 aşamayı gösterir:**

1.**Hedef Algılama** - Kalibrasyon hedeflerini tarar
2. **İşleme** - Kalibrasyonu uygular ve görüntüleri dışa aktarır**İşleme süresi:**

* Chloros+ paralel moduna göre çok daha yavaş
* Küçük ve orta ölçekli veri kümeleri için uygundur (&lt; 200 görüntü)

### Chloros+ Modu (Paralel İşleme)

**Chloros+ lisansı gerektirir**

**Nasıl çalışır:**

* [4 iş parçacıklı işleme boru hattı](../processing-architecture/processing-pipeline.md) kullanarak birden fazla görüntüyü aynı anda işler
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md), çalıştırma başlangıcında donanımınız için en uygun stratejiyi otomatik olarak seçer
* NVIDIA grafik kartlarıyla (masaüstü ve Jetson) GPU (CUDA) hızlandırması
* **İşçi sayısı donanıma göre uyarlanır**: GPU stratejileri**1-4 eşzamanlı işçi** çalıştırır (VRAM&#x27;e göre ölçeklenir — düşük bellekli bir Jetson 1 işçi çalıştırırken, 12 GB ve üzeri masaüstü GPU&#x27;lar 4&#x27;e kadar işçi çalıştırır); Yalnızca CPU&#x27;lu sistemler, fiziksel çekirdek başına bir işçi çalıştırır, ancak bir işçi düşülür**İlerleme çubuğu 4 aşamayı gösterir** (4 boru hattı iş parçacığına karşılık gelir):

1. **Algılama** (İş Parçacığı 1) - Kalibrasyon hedeflerini bulma
2. **Analiz** (İş Parçacığı 2) - Görüntü meta verilerini inceleme ve kalibrasyon hesaplama
3. **Kalibrasyon** (İş Parçacığı 3) - Debayering, vinyet düzeltme, kalibrasyon, indeks hesaplama
4. **Dışa Aktarma** (İş Parçacığı 4) - İşlenmiş görüntüleri ve indeksleri kaydetme**İlerleme çubuğu etkileşimi:*** Ayrıntılı 4 aşamalı açılır paneli görmek için çubuğun üzerine **fareyi getirin*** Açılır paneli sabitlemek için ilerleme çubuğuna **tıklayın*** Paneli serbest bırakmak ve gizlemek için **tekrar tıklayın**

**İşleme süresi:**

* Ücretsiz moddan önemli ölçüde daha hızlı
* GPU hızlandırması, hızı daha da artırır

{% hint style="info" %}
**Chloros+ Hız**: Büyük veri kümeleri için paralel işleme, sıralı moddan 5-10 kat daha hızlı olabilir. Ücretsiz modda 2 saat süren 500 görüntülük bir proje, Chloros+ ile 15-20 dakika içinde tamamlanabilir.
{% endhint %}

***

## İşleme Sırasında Neler Olur?

### Aşama 1: Hedef Algılama

**Chloros&#x27;in yaptığı işlemler:**

* Hedef sütununda işaretlediğiniz görüntüleri tarar (hiçbiri işaretlenmemişse tüm görüntüleri tarar)
* Her hedefteki kalibrasyon panellerini tanımlar
* Hedef panellerden yansıma değerlerini çıkarır
* Kalibrasyon planlaması için hedef zaman damgalarını kaydeder

**Süre:** 1-30 saniye (işaretli hedefler için), 5-30+ dakika (işaretsiz)

### Aşama 2: Debayering (RAW Dönüştürme)

**Chloros&#x27;in yaptığı işlemler:**

* RAW Bayer desenli verileri tam 3 kanallı görüntülere dönüştürür (LATTICE mono modülleri tek bantlı kalır — bunlar için debayering atlanır ve günlüğe bir not eklenir)
* Seçilen demosaicing algoritmasını uygular
* Maksimum görüntü kalitesini ve ayrıntıyı korur

**Süre:** Görüntü sayısına ve CPU/GPU hızına göre değişir

### Aşama 3: Kalibrasyon

**Chloros&#x27;in işlevi:*** **Vinyet düzeltmesi**: Kenarlardaki lens kararmasını giderir
* **Yansıtma kalibrasyonu**: Hedef yansıtma değerlerini ve/veya DAQ aşağı yönlü verilerini kullanarak normalleştirir
* Tüm bantlara/kanallara düzeltmeler uygular
* Zaman damgasına göre her görüntü için uygun kalibrasyon referansını kullanır

**Süre:** İşleme süresinin büyük kısmı

### Aşama 4: İndeks Hesaplama

**Chloros&#x27;in yaptığı işlemler:**

* Yapılandırılmış multispektral indeksleri hesaplar (NDVI, NDRE, vb.)
* Kalibre edilmiş görüntülere bant matematiği uygular
* Seçilen her indeks için indeks görüntüleri oluşturur

**Süre:** Görüntü başına birkaç saniye

### Aşama 5: Dışa Aktarma

**Chloros&#x27;in yaptığı işlem:**

* İşlenmiş görüntüleri seçilen formatta kaydeder
* **LATTICE fan-out**: her bir ham LATTICE karesi, tek geçişte etkinleştirilmiş tüm ürünler olarak dışa aktarılır — debayering, önizleme, parlaklık (her zaman float32), yansıma
* Dosyaları proje çıktı ağacına yazar: `<project>/<camera>/<format>/<Product>_Images/`
* **Kaynak dosya adını korur** — ürün, klasörle tanımlanır; son ek eklenmez**Süre:** Dışa aktarım formatına ve dosya boyutuna göre değişir***

## İşleme Davranışı

### Otomatik İşleme Süreci

Başlatıldığında, tüm süreç otomatik olarak çalışır:

* Kullanıcı müdahalesi gerekmez
* Yapılandırılan tüm adımlar sırayla yürütülür
* İlerleme güncellemeleri gerçek zamanlı olarak gösterilir
* Dışa aktarılan dosyalar tamamlandıkça diske yazılır — işlem devam ederken tamamlanan çıktıları açabilirsiniz

### İşleme Sırasında Bilgisayar Kullanımı

**Serbest Mod:**

* Nispeten düşük CPU kullanımı (tek iş parçacıklı)
* Bilgisayar, diğer görevler için yanıt verebilir durumda kalır
* Chloros&#x27;i simge durumuna küçültüp diğer uygulamalarda çalışmak güvenlidir

**Chloros+ Paralel Mod:**

* Stratejinin işçi havuzunda yüksek CPU kullanımı
* GPU hızlandırmasıyla: Yüksek GPU kullanımı
* İşlem sırasında bilgisayarın tepki süresi uzayabilir
* CPU&#x27;yu yoğun şekilde kullanan diğer görevleri başlatmaktan kaçının

{% hint style="warning" %}
**Performans İpucu**: En iyi Chloros+ performansı için diğer uygulamaları kapatın ve Chloros&#x27;in tüm sistem kaynaklarını kullanmasına izin verin.
{% endhint %}

### İşleme Duraklatılamaz (Ancak Durdurma İşlemi Temiz Bir Şekilde Gerçekleşir)

* İşleme bir kez başlatıldığında, duraklatılamaz ve daha sonra devam ettirilemez
* **Durdur** seçeneğine tıklamak, ilk tıklamada çalışmayı temiz bir şekilde durdurur
* Durdurma işleminden önce zaten dışa aktarılmış ürünler diskte kalır
* Durdurulan bir çalışma, tamamlanan işlemleri doğru bir şekilde raporlar (günlükteki `[RUN-SUMMARY]` satırlarına bakın)
* Yeni bir çalıştırma, iş akışını baştan başlatır

**Planlama ipucu:** Çok büyük projeler için, daha iyi kontrol sağlamak amacıyla işlemeyi toplu olarak yapmayı veya CLI&#x27;i kullanmayı düşünün.***

## İşleminizi İzleme

İşlemler devam ederken şunları yapabilirsiniz:

* **İlerleme çubuğunu izleyin** - Genel tamamlanma yüzdesini görün
* **Mevcut aşamayı görüntüleyin** - Algılama, Analiz, Kalibrasyon veya Dışa Aktarma
* **Günlük sekmesini kontrol edin** - Ayrıntılı işlem mesajlarını ve uyarıları görün
* **Tamamlanan görüntüleri önizleyin** - İşleme sırasında dışa aktarılan dosyalar diskte görünür

İzlemeyle ilgili ayrıntılı bilgi için bkz. [İşlemeyi İzleme](monitoring-the-processing.md).

***

## İşlemeyi Durdurma

İşlemeyi durdurmanız gerekiyorsa:

### Nasıl Durdurulur

1. **Durdur düğmesini** bulun (işleme sırasında Başlat düğmesinin yerine geçer)
2. Bir kez tıklayın — işlenmekte olan görüntü tamamlanırken çubukta **&quot;Durduruluyor...&quot;** yazısı görünür
3. İşlem kesin olarak durdurulur ve günlük dosyasında tamamlanan işlemlere ilişkin doğru bir `[RUN-SUMMARY]` raporu yazdırılır

### Ne Zaman Durdurulmalı

**Durdurmak için geçerli nedenler:**

* Yanlış ayarların kullanıldığı fark edildiğinde
* Hedef görüntüleri işaretlemeyi unuttuğunuzda
* Yanlış görüntüler içe aktarıldıysa
* Sistem çok yavaş çalışıyor veya yanıt vermiyorsa

**Durdurma işleminden sonra:**

* Durdurma işleminden önce dışa aktarılan ürünler diskte kalır
* Sorunları inceleyin ve düzeltin, gerektiğinde ayarları değiştirin
* İşlemeyi yeniden başlatın — işlem baştan başlar

***

## İşleme Süresi Tahminleri

Gerçek işleme süresi aşağıdakilere göre büyük ölçüde değişir:

* Görüntü sayısı
* Görüntü çözünürlüğü
* RAW ve JPG giriş formatları
* İşleme modu (Free ve Chloros+)
* CPU hızı ve çekirdek sayısı
* GPU kullanılabilirliği (yalnızca Chloros+)
* Hesaplanacak dizin sayısı
* Etkinleştirilmiş dışa aktarma ürünlerinin sayısı (LATTICE)

### Yaklaşık Tahminler (Chloros+, 12 MP görüntüler, modern CPU)

| Görüntü Sayısı | Ücretsiz Mod | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 görüntü   | 15-20 dakika | 5-8 dakika        | 3-5 dakika        |
| 100 görüntü  | 30-40 dakika | 10-15 dakika      | 5-8 dakika        |
| 200 görüntü  | 1-1,5 saat | 20-30 dakika      | 10-15 dakika      |
| 500 resim  | 2-3 saat   | 45-60 dakika      | 20-30 dakika      |
| 1000 resim | 4-6 saat   | 1,5-2 saat      | 40-60 dakika      |

{% hint style="info" %}
**İlk Çalıştırma**: Chloros önbellekleri ve profilleri oluştururken ilk işleme daha uzun sürebilir. Benzer veri kümelerinin sonraki işlemleri daha hızlı olacaktır.
{% endhint %}

***

## Başlangıçta Sık Karşılaşılan Sorunlar

### Başlat Düğmesi Devre Dışı (Gri Renkli)

**Olası nedenler:**

* İçe aktarılmış görüntü yok
* Arka uç tam olarak başlatılmamış
* Önceki işleme hâlâ devam ediyor
* Proje tam olarak yüklenmemiş

**Çözümler:**

1. Arka ucun tamamen başlatılmasını bekleyin (ana menü simgesini kontrol edin)
2. Görüntülerin Dosya Tarayıcısı&#x27;na içe aktarıldığını doğrulayın
3. Düğme devre dışı kalmaya devam ederse Chloros&#x27;i yeniden başlatın
4. Hata mesajları için Hata Günülüğü&#x27;nü kontrol edin

### İşleme Başlıyor, Ancak Hemen Hata Veriyor

**Olası nedenler:**

* Projede geçerli görüntü yok
* Bozuk görüntü dosyaları
* Yetersiz disk alanı
* Yetersiz bellek (RAM)

**Çözümler:**

1. Hata mesajları için Hata Güzelleştirme Günlüğü&#x27;nü (<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">) kontrol edin
2. Kullanılabilir disk alanını kontrol edin
3. Daha küçük bir görüntü alt kümesini işlemeyi deneyin
4. Görüntülerin bozuk olmadığını doğrulayın

### İşlem Tamamlandı Ancak Hiçbir Görüntü Yazılmadı

Görüntü ürünleri talep eden ancak hiçbirini yazmayan bir işlem, **başarı değil, hata** olarak değerlendirilir — Chloros bunu açıkça bildirir:

* GUI günlüğü, olası nedeni belirten `[RUN-SUMMARY]` ipuçlarını görüntüler — görüntü içe aktarılmadı, hedef algılanmadı veya istenen tüm ürünler uygulanamaz olduğu için atlandı (örn. yalnızca RGB kameralardan radyans/yansıtma istenmesi)
* CLI&#x27;in eşdeğeri (`chloros-cli process`), `Processing finished but wrote no image products.`&#x27;i yazdırır ve **sıfırdan farklı bir değerle sonlanır**, böylece komut dosyaları bunu algılayabilir
* Kasıtlı olarak yalnızca meta verilerin çalıştırılması (tüm dışa aktarım ürünleri devre dışı bırakılmış, dizin yok) yine de başarılı sayılır

Tam anlamı için [CLI Referansı](../reference/cli-reference.md#a-run-that-writes-no-images-fails) bölümüne bakın.

### &quot;Hedef Algılanmadı&quot; Uyarısı

**Olası nedenler:**

* Hedef görüntüleri işaretlemeyi unutmuş olabilirsiniz
* Hedef görüntülerde görünür hedefler bulunmuyor
* Hedef algılama ayarları çok katı

**Çözümler:**

1. [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümünü inceleyin
2. Hedef sütununda uygun görüntüleri işaretleyin
3. İşaretlenen görüntülerde hedeflerin görünür olduğunu doğrulayın
4. Gerekirse hedef algılama ayarlarını düzenleyin

***

## Başarılı İşleme İçin İpuçları

### Başlamadan Önce

1. **Önce küçük bir alt kümeyle test edin** - Ayarları doğrulamak için 10-20 görüntüyü işleyin
2. **Kullanılabilir disk alanını kontrol edin** - Veri kümesinin 2-3 katı kadar boş alan olduğundan emin olun (tüm LATTICE ürünleri etkinleştirilmişse daha fazla)
3. **Gereksiz uygulamaları kapatın** - Sistem kaynaklarını boşaltın
4. **Hedef görüntüleri kontrol edin** - Kaliteyi sağlamak için işaretli hedefleri önizleyin
5. **Projeyi kaydedin** - Proje otomatik olarak kaydedilir, ancak manuel olarak kaydetmek iyi bir uygulamadır

### İşleme Sırasında

1. **Sistemin uyku moduna geçmesini önleyin** - Güç tasarrufu modlarını devre dışı bırakın
2. **Chloros&#x27;i ön planda tutun** - Ya da en azından görev çubuğunda görünür durumda olsun
3. **İlerlemeyi ara sıra izleyin** - Uyarı veya hata olup olmadığını kontrol edin
4. **Diğer ağır uygulamaları yüklemeyin** - Özellikle Chloros+ paralel modunda

### Chloros+ GPU Hızlandırma

NVIDIA GPU hızlandırması kullanılıyorsa:

1. NVIDIA sürücülerini en son sürüme güncelleyin
2. GPU&#x27;nun 4 GB ve üzeri VRAM&#x27;e sahip olduğundan emin olun (eşzamanlı Texture Aware debayering için 7 GB ve üzeri)
3. GPU&#x27;yu yoğun şekilde kullanan uygulamaları (oyunlar, video düzenleme) kapatın
4. GPU sıcaklığını izleyin (yeterli soğutma sağlandığından emin olun)

***

## Sonraki Adımlar

İşleme başladıktan sonra:

1. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
2. **İşlemin tamamlanmasını bekleyin** - İşleme otomatik olarak yürütülür
3. **Sonuçları inceleyin** - Bkz. [İşlemi Tamamlama](finishing-the-processing.md)

İşlem sırasında ne yapmanız gerektiği hakkında bilgi için bkz. [İşlemi İzleme](monitoring-the-processing.md).
