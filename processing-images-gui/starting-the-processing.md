# İşlemeyi Başlatma

Görüntülerinizi içe aktardıktan, kalibrasyon hedeflerinizi işaretledikten ve proje ayarlarınızı yapılandırdıktan sonra, işlemeyi başlatmaya hazırsınız demektir. Bu sayfa, Chloros işleme akışını başlatma sürecinde size yol gösterir.

## İşleme Öncesi Kontrol Listesi

Başlat düğmesine tıklamadan önce her şeyin hazır olduğunu doğrulayın:

* [ ] **Dosyalar içe aktarıldı** - Tüm görüntüler Dosya Tarayıcı&#x27;da görünüyor
* [ ] **Hedef görüntüler işaretlendi** - Kalibrasyon görüntüleri için Hedef sütunu işaretlendi
* [ ] **Kamera modelleri algılandı** - Kamera Modeli sütununda doğru kameralar görünüyor
* [ ] **Ayarlar yapılandırıldı** - Proje Ayarları gözden geçirildi ve ayarlandı
* [ ] **İndeksler seçildi** - İstenen multispektral indeksler eklendi (gerekirse)
* [ ] **Dışa aktarma biçimi seçildi** - İş akışınıza uygun çıktı biçimi

{% hint style="info" %}
**İpucu**: İşleme başlamadan önce Dosya Tarayıcı&#x27;daki birkaç görüntüyü tıklayarak doğru yüklendiklerini doğrulayın.
{% endhint %}

***

## İşlemeyi Başlatma

### Başlat Düğmesini Bulun

Başlat/Oynat düğmesi, Chloros&#x27;in üst başlık çubuğunda bulunur:

* Konum: Pencerenin üst orta kısmı
* Simge: **Oynat/Başlat düğmesi** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
* Durum: İşleme hazır olduğunda düğme etkinleştirilir (parlak)

### Başlatmak için Tıklayın

1. Üst başlıktaki **Oynat/Başlat düğmesine** tıklayın
2. İşleme hemen başlar
3. İşleme sırasında düğme devre dışı kalır (gri renkte görünür)
4. İlerleme çubuğu güncellenerek işleme durumunu gösterir

{% hint style="success" %}
**İşleme Başladı**: Tıklandığında, Chloros tüm işleme adımlarını (hedef algılama, debayering, kalibrasyon, indeks hesaplama ve dışa aktarma) otomatik olarak gerçekleştirir.
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

1.**Hedef Algılama** - Kalibrasyon hedefleri için tarama
2. **İşleme** - Kalibrasyonun uygulanması ve görüntülerin dışa aktarılması**İşleme süresi:**

* Chloros+ paralel modundan çok daha yavaştır
* Küçük ve orta ölçekli veri kümeleri için uygundur (&lt; 200 görüntü)

### Chloros+ Modu (Paralel İşleme)

**Chloros+ lisansı gerektirir**

**Nasıl çalışır:**

* [4 iş parçacıklı işleme boru hattı](../processing-architecture/processing-pipeline.md) kullanarak birden fazla görüntüyü aynı anda işler
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md), donanımınız için en uygun stratejiyi otomatik olarak seçer
* NVIDIA grafik kartlarıyla (masaüstü ve Jetson) GPU (CUDA) hızlandırma
* Jetson Nano&#x27;dan (1 işçi) 12 GB+ GPU&#x27;lu bir masaüstü bilgisayara (3-4 işçi) kadar ölçeklenebilir

**İlerleme çubuğu 4 aşamayı gösterir** (4 boru hattı iş parçacığına karşılık gelir):

1. **Algılama** (İş Parçacığı 1) - Kalibrasyon hedeflerini bulma
2. **Analiz** (İş Parçacığı 2) - Görüntü meta verilerini inceleme ve kalibrasyon hesaplama
3. **Kalibrasyon** (İş Parçacığı 3) - GPU debayering, vinyet düzeltme, indeks hesaplama
4. **Dışa aktarma** (İş Parçacığı 4) - İşlenmiş görüntüleri ve indeksleri kaydetme**İlerleme çubuğu etkileşimi:*** Ayrıntılı 4 aşamalı açılır paneli görmek için çubuğun üzerine **fareyi getirin*** Açılır paneli sabitlemek için ilerleme çubuğuna **tıklayın*** Paneli serbest bırakmak ve gizlemek için **tekrar tıklayın**

**İşleme süresi:**

* Ücretsiz moddan önemli ölçüde daha hızlı
* CPU çekirdek sayısına göre ölçeklenir
* GPU hızlandırma, hızı daha da artırır

{% hint style="info" %}
**Chloros+ Hızı**: Büyük veri kümeleri için paralel işleme, sıralı moddan 5-10 kat daha hızlı olabilir. Ücretsiz modda 2 saat süren 500 görüntülü bir proje, Chloros+ ile 15-20 dakikada tamamlanabilir.
{% endhint %}

***

## İşleme Sırasında Neler Olur

### Aşama 1: Hedef Algılama

**Chloros&#x27;in yaptığı:**

* İşaretli hedef görüntüleri tarar (işaretli yoksa tüm görüntüleri tarar)
* Her hedefteki 4 kalibrasyon panelini tanımlar
* Hedef panellerden yansıma değerlerini çıkarır
* Kalibrasyon planlaması için hedef zaman damgalarını kaydeder

**Süre:** 1-30 saniye (işaretli hedefler ile), 5-30+ dakika (işaretsiz)

### Aşama 2: Debayering (RAW Dönüştürme)

**Chloros&#x27;in yaptığı işlemler:**

* RAW Bayer desen verilerini tam RGB görüntülerine dönüştürür
* Yüksek kaliteli demosaicing algoritması uygular
* Maksimum görüntü kalitesini ve ayrıntıyı korur

**Süre:** Görüntü sayısına ve CPU hızına göre değişir

### Aşama 3: Kalibrasyon

**Chloros&#x27;in yaptığı şey:*** **Vinyet düzeltme**: Kenarlardaki lens kararmasını giderir
* **Yansıma kalibrasyonu**: Hedef yansıma değerlerini kullanarak normalleştirir
* Tüm bantlara/kanallara düzeltmeler uygular
* Zaman damgasına göre her görüntü için uygun kalibrasyon hedefini kullanır

**Süre:** İşlem süresinin büyük kısmı

### Aşama 4: İndeks Hesaplama

**Chloros ne yapar:**

* Yapılandırılmış multispektral indeksleri hesaplar (NDVI, NDRE, vb.)
* Kalibre edilmiş görüntülere bant matematiği uygular
* Seçilen her indeks için indeks görüntüleri oluşturur

**Süre:** Görüntü başına birkaç saniye

### Aşama 5: Dışa Aktarma

**Chloros&#x27;in yaptığı işlemler:**

* Kalibre edilmiş görüntüleri seçilen formatta kaydeder
* Yapılandırılmış LUT renkleriyle indeks görüntülerini dışa aktarır
* Dosyaları kamera modeli alt klasörlerine yazar
* Son eklerle birlikte orijinal dosya adlarını korur

**Süre:** Dışa aktarım formatına ve dosya boyutuna göre değişir***

## İşleme Davranışı

### Otomatik İşleme Süreci

Başlatıldığında, tüm süreç otomatik olarak çalışır:

* Kullanıcı müdahalesi gerekmez
* Yapılandırılan tüm adımlar sırayla yürütülür
* İlerleme güncellemeleri gerçek zamanlı olarak gösterilir

### İşleme Sırasında Bilgisayar Kullanımı

**Serbest Mod:**

* Nispeten düşük CPU kullanımı (tek iş parçacıklı)
* Bilgisayar diğer görevler için yanıt vermeye devam eder
* Chloros&#x27;i simge durumuna küçültüp diğer uygulamalarda çalışmak güvenlidir

**Chloros+ Paralel Mod:**

* Yüksek CPU kullanımı (çok iş parçacıklı, 16 çekirdeğe kadar)
* GPU hızlandırmasıyla: Yüksek GPU kullanımı
* İşlem sırasında bilgisayarın yanıt verme hızı düşebilir
* CPU&#x27;yu yoğun şekilde kullanan diğer görevleri başlatmaktan kaçının

{% hint style="warning" %}
**Performans İpucu**: En iyi Chloros+ performansı için diğer uygulamaları kapatın ve Chloros&#x27;in tüm sistem kaynaklarını kullanmasına izin verin.
{% endhint %}

### İşlem Duraklatılamaz

**Önemli sınırlamalar:**

* İşlem bir kez başlatıldığında duraklatılamaz
* İşlemi iptal edebilirsiniz, ancak ilerleme kaybolur
* Kısmi sonuçlar kaydedilmez
* İptal edilirse baştan başlatılmalıdır

**Planlama ipucu:** Çok büyük projeler için, daha iyi kontrol sağlamak amacıyla toplu işlemeyi veya CLI&#x27;i kullanmayı düşünün.***

## İşleminizi İzleme

İşlem devam ederken şunları yapabilirsiniz:

* **İlerleme çubuğunu izleyin** - Genel tamamlanma yüzdesini görün
* **Mevcut aşamayı görüntüleyin** - Algılama, Analiz, Kalibrasyon veya Dışa Aktarma
* **Günlük sekmesini kontrol edin** - Ayrıntılı işlem mesajlarını ve uyarıları görün
* **Tamamlanan görüntüleri önizleyin** - Bazı dışa aktarma dosyaları işlem sırasında görünebilir

İzleme hakkında ayrıntılı bilgi için bkz. [İşlemeyi İzleme](monitoring-the-processing.md).

***

## İşlemeyi İptal Etme

İşlemeyi durdurmanız gerekiyorsa:

### Nasıl İptal Edilir

1. **Durdur/İptal düğmesini** bulun (işleme sırasında Başlat düğmesinin yerini alır)
2. Durdur düğmesine tıklayın
3. İşlem hemen durur
4. Kısmi sonuçlar silinir

### Ne Zaman İptal Etmelisiniz

**İptal etmek için geçerli nedenler:**

* Yanlış ayarların kullanıldığı fark edildi
* Hedef görüntüleri işaretlemeyi unuttunuz
* Yanlış görüntüler içe aktarıldı
* Sistem çok yavaş çalışıyor veya yanıt vermiyor

**İptal ettikten sonra:**

* Sorunları gözden geçirin ve düzeltin
* Ayarları gerektiği gibi düzenleyin
* İşlemi baştan başlatın
* En sorunsuz deneyim için, Chloros&#x27;i tamamen kapatın ve yeniden başlatın

{% hint style="warning" %}
**Kısmi Sonuç Yok**: İptal etme işlemi tüm ilerlemeyi siler. Chloros, kısmen işlenmiş görüntüleri kaydetmez.
{% endhint %}

***

## İşleme Süresi Tahminleri

Gerçek işleme süresi aşağıdakilere göre büyük ölçüde değişir:

* Görüntü sayısı
* Görüntü çözünürlüğü
* RAW ve JPG giriş formatları
* İşleme modu (Ücretsiz ve Chloros+)
* CPU hızı ve çekirdek sayısı
* GPU kullanılabilirliği (sadece Chloros+)
* Hesaplanacak dizin sayısı
* Dışa aktarma formatının karmaşıklığı

### Yaklaşık Tahminler (Chloros+, 12 MP görüntüler, modern CPU)

| Görüntü Sayısı | Ücretsiz Mod | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 görüntü   | 15-20 dak. | 5-8 dak.        | 3-5 dak.        |
| 100 görüntü  | 30-40 dak. | 10-15 dak.      | 5-8 dak.        |
| 200 görüntü  | 1-1,5 saat | 20-30 dak.      | 10-15 dak.      |
| 500 görüntü  | 2-3 saat   | 45-60 dakika      | 20-30 dakika      |
| 1000 görüntü | 4-6 saat   | 1,5-2 saat      | 40-60 dakika      |

{% hint style="info" %}
**İlk Çalıştırma**: Chloros önbellekleri ve profilleri oluştururken ilk işleme daha uzun sürebilir. Benzer veri kümelerinin sonraki işlemleri daha hızlı olacaktır.
{% endhint %}

***

## Başlangıçta Sık Karşılaşılan Sorunlar

### Başlat Düğmesi Devre Dışı (Gri Renkli)

**Olası nedenler:**

* İçe aktarılan görüntü yok
* Arka uç tam olarak başlatılmamış
* Önceki işleme hala devam ediyor
* Proje tam olarak yüklenmemiş

**Çözümler:**

1. Arka ucun tamamen başlatılmasını bekleyin (ana menü simgesini kontrol edin)
2. Görüntülerin Dosya Tarayıcı&#x27;ya içe aktarıldığını doğrulayın
3. Düğme devre dışı kalmaya devam ederse Chloros&#x27;i yeniden başlatın
4. Hata mesajları için Hata Giderme Günlüğünü kontrol edin

### İşleme Başlıyor, Ancak Hemen Hata Veriyor

**Olası nedenler:**

* Projede geçerli görüntü yok
* Bozuk görüntü dosyaları
* Yetersiz disk alanı
* Yetersiz bellek (RAM)

**Çözümler:**

1. Hata mesajları için Hata Giderme Günlüğünü kontrol edin <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> 2. Kullanılabilir disk alanını kontrol edin
3. Daha küçük bir görüntü alt kümesini işlemeyi deneyin
4. Görüntülerin bozuk olmadığını doğrulayın

### &quot;Hedef Algılanmadı&quot; Uyarısı

**Olası nedenler:**

* Hedef görüntüleri işaretlemeyi unuttunuz
* Hedef görüntülerde görünür hedefler yok
* Hedef algılama ayarları çok katı

**Çözümler:**

1. [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümünü inceleyin
2. Hedef sütununda uygun görüntüleri işaretleyin
3. İşaretlenen görüntülerde hedeflerin görünür olduğunu doğrulayın
4. Gerekirse hedef algılama ayarlarını değiştirin

***

## Başarılı İşleme İçin İpuçları

### Başlamadan Önce

1. **Önce küçük bir alt küme ile test edin** - Ayarları doğrulamak için 10-20 görüntüyü işleyin
2. **Kullanılabilir disk alanını kontrol edin** - Veri kümesinin 2-3 katı kadar boş alan olduğundan emin olun
3. **Gereksiz uygulamaları kapatın** - Sistem kaynaklarını boşaltın
4. **Hedef görüntüleri doğrulayın** - Kaliteyi sağlamak için işaretlenen hedefleri önizleyin
5. **Projeyi kaydedin** - Proje otomatik olarak kaydedilir, ancak manuel olarak kaydetmek iyi bir uygulamadır

### İşleme Sırasında

1. **Sistemin uyku moduna geçmesini önleyin** - Güç tasarrufu modlarını devre dışı bırakın
2. **Chloros&#x27;i ön planda tutun** - Ya da en azından görev çubuğunda görünür olsun
3. **İlerlemeyi ara sıra izleyin** - Uyarı veya hata olup olmadığını kontrol edin
4. **Diğer ağır uygulamaları yüklemeyin** - Özellikle Chloros+ paralel modunda

### Chloros+ GPU Hızlandırma

NVIDIA GPU hızlandırması kullanıyorsanız:

1. NVIDIA sürücülerini en son sürüme güncelleyin
2. GPU&#x27;nun 4 GB+ VRAM&#x27;e sahip olduğundan emin olun
3. GPU&#x27;yu yoğun kullanan uygulamaları (oyunlar, video düzenleme) kapatın
4. GPU sıcaklığını izleyin (yeterli soğutma olduğundan emin olun)

***

## Sonraki Adımlar

İşleme başladıktan sonra:

1. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
2. **İşlemin tamamlanmasını bekleyin** - İşlem otomatik olarak çalışır
3. **Sonuçları inceleyin** - Bkz. [İşlemi Tamamlama](finishing-the-processing.md)

İşlem sırasında ne yapmanız gerektiği hakkında bilgi için bkz. [İşlemi İzleme](monitoring-the-processing.md).
