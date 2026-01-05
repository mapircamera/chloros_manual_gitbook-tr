# İşlemeyi Başlatma

Görüntülerinizi içe aktardıktan, kalibrasyon hedeflerinizi işaretledikten ve proje ayarlarınızı yapılandırdıktan sonra, işlemeyi başlatmaya hazırsınız demektir. Bu sayfa, Chloros işleme sürecini başlatma konusunda size rehberlik eder.

## İşleme Öncesi Kontrol Listesi

Başlat düğmesine tıklamadan önce her şeyin hazır olduğunu doğrulayın:

* [ ] **Dosyalar içe aktarıldı** - Tüm görüntüler Dosya Tarayıcı&#x27;da görünür
* [ ] **Hedef görüntüler işaretlendi** - Kalibrasyon görüntüleri için Hedef sütunu işaretlendi
* [ ] **Kamera modelleri algılandı** - Kamera Modeli sütununda doğru kameralar gösteriliyor
* [ ] **Ayarlar yapılandırıldı** - Proje Ayarları gözden geçirildi ve ayarlandı
* [ ] **Endeksler seçildi** - İstenen multispektral indeksler eklendi (gerekirse)
* [ ] **Dışa aktarım formatı seçildi** - İş akışınıza uygun çıktı formatı

{% hint style=&quot;info&quot; %}
**İpucu**: İşleme başlamadan önce Dosya Tarayıcı&#x27;da birkaç görüntüyü tıklayarak doğru yüklendiklerini kontrol edin.
{% endhint %}

***

## İşleme Başlama

### Başlat Düğmesini Bulma

Başlat/Oynat düğmesi, Chloros&#x27;in üst başlık çubuğunda bulunur:

* Konum: Pencerenin üst orta kısmı
* Simge: **Oynat/Başlat düğmesi** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line">
* Durum: İşleme hazır olduğunda düğme etkinleştirilir (parlak)

### Başlatmak için tıklayın

1. Üst başlıktaki **Oynat/Başlat düğmesine** tıklayın
2. İşleme hemen başlar
3. İşleme sırasında düğme devre dışı bırakılır (gri renge döner)
4. İşleme durumunu gösteren ilerleme çubuğu güncellenir

{% hint style=&quot;success&quot; %}
**İşleme Başladı**: Tıklandığında, Chloros tüm işlem adımlarını otomatik olarak gerçekleştirir - hedef algılama, debayering, kalibrasyon, indeks hesaplama ve dışa aktarma.
{% endhint %}

***

## İşleme Modlarını Anlama

Chloros, lisansınıza bağlı olarak iki farklı işleme modunda çalışır:

### Serbest Mod (Sıralı İşleme)

**Tüm kullanıcılar için kullanılabilir**

**Nasıl çalışır:**

* Görüntüleri tek tek, sıralı olarak işler
* Tek iş parçacıklı çalışma
* Daha düşük bellek kullanımı

**İlerleme çubuğu 2 aşamayı gösterir:**

1.**Hedef Algılama** - Kalibrasyon hedeflerini tarama
2. **İşleme** - Kalibrasyon uygulama ve görüntüleri dışa aktarma**İşleme süresi:**

* Chloros+ paralel modundan çok daha yavaştır
* Küçük ve orta boy veri kümeleri için uygundur (&lt; 200 görüntü)

### Chloros+ Modu (Paralel İşleme)

**Chloros+ lisansı gerektirir**

**Nasıl çalışır:**

* Birden fazla görüntüyü aynı anda işler
* Çok iş parçacıklı çalışma (16 adede kadar paralel işçi)
* Birden fazla CPU çekirdeği kullanır
* NVIDIA grafik kartlarıyla isteğe bağlı GPU (CUDA) hızlandırma

**İlerleme çubuğu 4 aşamayı gösterir:**

1.**Algılama** - Kalibrasyon hedeflerini bulma
2. **Analiz** - Görüntü meta verilerini inceleme ve iş akışını hazırlama
3. **Kalibre etme** - Düzeltmeler ve kalibrasyonlar uygulama
4. **Dışa aktarma** - İşlenmiş görüntüleri ve dizinleri kaydetme**İlerleme çubuğu etkileşimi:*** Çubuğun üzerine **fareyi getirin**, ayrıntılı 4 aşamalı açılır paneli görmek için
* İlerleme çubuğunu **tıklayın**, açılır paneli yerinde dondurmak için
* **Tekrar tıklayın**, paneli çözmek ve gizlemek için**İşleme süresi:**

* Ücretsiz moddan önemli ölçüde daha hızlıdır
* CPU çekirdek sayısı ile ölçeklenir
* GPU hızlandırma, hızı daha da artırır

{% hint style=&quot;info&quot; %}
**Chloros+ Hız**: Paralel işleme, büyük veri kümeleri için sıralı moddan 5-10 kat daha hızlı olabilir. Ücretsiz modda 2 saat süren 500 görüntülü bir proje, Chloros+ ile 15-20 dakikada tamamlanabilir.
{% endhint %}

***

## İşleme Sırasında Neler Olur?

### Aşama 1: Hedef Algılama

**Chloros&#x27;in yaptığı şeyler:**

* İşaretlenmiş hedef görüntüleri (veya işaretlenmemişse tüm görüntüleri) tarar
* Her hedefte 4 kalibrasyon panelini tanımlar
* Hedef panellerden yansıma değerlerini çıkarır
* Kalibrasyon planlaması için hedef zaman damgalarını kaydeder

**Süre:** 1-30 saniye (işaretlenmiş hedefler ile), 5-30+ dakika (işaretlenmemiş)

### Aşama 2: Debayering (RAW Dönüştürme)

**Chloros&#x27;in yaptığı işlemler:**

* RAW Bayer desen verilerini tam RGB görüntülerine dönüştürür
* Yüksek kaliteli demosaicing algoritması uygular
* Maksimum görüntü kalitesini ve ayrıntıları korur

**Süre:** Görüntü sayısı ve CPU hızına göre değişir

### Aşama 3: Kalibrasyon

**Chloros&#x27;in yaptığı işlemler:*** **Vinyet düzeltme**: Kenarlarda lens karartmasını giderir
* **Yansıma kalibrasyonu**: Hedef yansıma değerlerini kullanarak normalleştirir
* Tüm bantlar/kanallarda düzeltmeler uygular
* Zaman damgasına göre her görüntü için uygun kalibrasyon hedefini kullanır

**Süre:** İşlem süresinin çoğunu oluşturur

### Aşama 4: Dizin Hesaplama

**Chloros&#x27;in yaptığı işlemler:**

* Yapılandırılmış multispektral dizinleri hesaplar (NDVI, NDRE vb.)
* Kalibre edilmiş görüntülere bant matematiği uygular
* Seçilen her endeks için endeks görüntüleri oluşturur

**Süre:** Görüntü başına birkaç saniye

### Aşama 5: Dışa Aktarma

**Chloros&#x27;in yaptığı işlemler:**

* Kalibre edilmiş görüntüleri seçilen formatta kaydeder
* Yapılandırılmış LUT renkleriyle endeks görüntülerini dışa aktarır
* Dosyaları kamera modeli alt klasörlerine yazar
* Son eklerle orijinal dosya adlarını korur

**Süre:** Dışa aktarım formatına ve dosya boyutuna göre değişir***

## İşleme Davranışı

### Otomatik İşleme Boru Hattı

Başlatıldığında, tüm boru hattı otomatik olarak çalışır:

* Kullanıcı etkileşimi gerekmez
* Yapılandırılan tüm adımlar sırayla yürütülür
* İlerleme güncellemeleri gerçek zamanlı olarak gösterilir

### İşleme Sırasında Bilgisayar Kullanımı

**Serbest Mod:**

* Nispeten düşük CPU kullanımı (tek iş parçacıklı)
* Bilgisayar diğer görevler için yanıt vermeye devam eder
* Chloros&#x27;i simge durumuna küçültüp diğer uygulamalarda çalışmak güvenlidir

**Chloros+ Paralel Mod:**

* Yüksek CPU kullanımı (çok iş parçacıklı, 16 çekirdeğe kadar)
* GPU hızlandırma ile: Yüksek GPU kullanımı
* İşlem sırasında bilgisayar daha az yanıt verebilir
* Diğer CPU yoğun görevleri başlatmaktan kaçının

{% hint style=&quot;warning&quot; %}
**Performans İpucu**: En iyi Chloros+ performansı için diğer uygulamaları kapatın ve Chloros&#x27;in tüm sistem kaynaklarını kullanmasına izin verin.
{% endhint %}

### İşleme Duraklatılamaz

**Önemli sınırlamalar:**

* İşleme başladıktan sonra duraklatılamaz.
* İşlemeyi iptal edebilirsiniz, ancak ilerleme kaybolur.
* Kısmi sonuçlar kaydedilmez.
* İptal edildiğinde baştan başlamak gerekir.

**Planlama ipucu:** Çok büyük projeler için, daha iyi kontrol için toplu işlemeyi veya CLI kullanmayı düşünün.***

## İşlemenizi İzleme

İşleme devam ederken şunları yapabilirsiniz:

* **İlerleme çubuğunu izleyin** - Genel tamamlanma yüzdesini görün
* **Mevcut aşamayı görüntüleyin** - Algılama, Analiz, Kalibrasyon veya Dışa Aktarma
* **Günlük sekmesini kontrol edin** - Ayrıntılı işleme mesajlarını ve uyarıları görün
* **Tamamlanan görüntüleri önizleyin** - İşleme sırasında bazı dışa aktarma dosyaları görünebilir

İzleme hakkında ayrıntılı bilgi için, [İşlemi İzleme](monitoring-the-processing.md) bölümüne bakın.

***

## İşlemeyi İptal Etme

İşlemeyi durdurmanız gerekiyorsa:

### İptal Etme

1. **Durdur/İptal düğmesini** bulun (işlem sırasında Başlat düğmesinin yerine geçer)
2. Durdur düğmesine tıklayın
3. İşleme hemen durur
4. Kısmi sonuçlar silinir

### Ne Zaman İptal Etmelisiniz

**İptal etmek için geçerli nedenler:**

* Yanlış ayarların kullanıldığı fark edildi
* Hedef görüntüleri işaretlemeyi unuttunuz
* Yanlış görüntüler içe aktarıldı
* Sistem çok yavaş çalışıyor veya yanıt vermiyor

**İptal ettikten sonra:**

* Sorunları gözden geçirin ve düzeltin
* Ayarları gerektiği gibi yapın
* İşleme baştan yeniden başlayın
* En temiz deneyim için, Chloros&#x27;i tamamen kapatın ve yeniden başlatın

{% hint style=&quot;warning&quot; %}
**Kısmi Sonuç Yok**: İptal etme, tüm ilerlemeyi silinir. Chloros, kısmen işlenmiş görüntüleri kaydetmez.
{% endhint %}

***

## İşlem Süresi Tahminleri

Gerçek işlem süresi aşağıdakilere göre büyük ölçüde değişir:

* Görüntü sayısı
* Görüntü çözünürlüğü
* RAW ve JPG giriş formatı
* İşlem modu (Ücretsiz ve Chloros+)
* CPU hızı ve çekirdek sayısı
* GPU kullanılabilirliği (yalnızca Chloros+)
* Hesaplanacak indeks sayısı
* Dışa aktarım formatının karmaşıklığı

### Yaklaşık Tahminler (Chloros+, 12 MP görüntüler, modern CPU)

| Görüntü Sayısı | Ücretsiz Mod | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 görüntü   | 15-20 dakika | 5-8 dakika        | 3-5 dakika        |
| 100 görüntü  | 30-40 dakika | 10-15 dakika      | 5-8 dakika        |
| 200 görüntü  | 1-1,5 saat | 20-30 dakika      | 10-15 dakika      |
| 500 görüntü  | 2-3 saat   | 45-60 dakika      | 20-30 dakika      |
| 1000 görüntü | 4-6 saat   | 1,5-2 saat      | 40-60 dakika      |

{% hint style=&quot;info&quot; %}
**İlk Çalıştırma**: Chloros önbellekleri ve profilleri oluştururken ilk işleme daha uzun sürebilir. Benzer veri kümelerinin sonraki işlemeleri daha hızlı olacaktır.
{% endhint %}

***

## Başlangıçta Sık Karşılaşılan Sorunlar

### Başlat Düğmesi Devre Dışı (Gri renkte)

**Olası nedenler:**

* İçe aktarılan görüntü yok
* Arka uç tam olarak başlatılmadı
* Önceki işlem hala devam ediyor
* Proje tam olarak yüklenmedi

**Çözümler:**

1. Arka planın tamamen başlatılmasını bekleyin (ana menü simgesini kontrol edin)
2. Görüntülerin Dosya Tarayıcıya içe aktarıldığını doğrulayın
3. Düğme devre dışı kalırsa Chloros&#x27;i yeniden başlatın
4. Hata mesajları için Hata Ayıklama Günlüğünü kontrol edin

### İşleme Başlıyor, Ancak Hemen Hata Veriyor

**Olası nedenler:**

* Projede geçerli görüntü yok
* Bozuk görüntü dosyaları
* Yetersiz disk alanı
* Yetersiz bellek (RAM)

**Çözümler:**

1. Hata ayıklama günlüğünü kontrol edin <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> 2. Kullanılabilir disk alanını kontrol edin
3. Daha küçük bir görüntü alt kümesini işlemeyi deneyin
4. Görüntülerin bozuk olmadığını kontrol edin

### &quot;Hedef Algılanmadı&quot; Uyarısı

**Olası nedenler:**

* Hedef görüntüleri işaretlemeyi unutmuş olabilirsiniz
* Hedef görüntülerde görünür hedefler bulunmuyor olabilir
* Hedef algılama ayarları çok katı olabilir

**Çözümler:**

1. [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümünü inceleyin.
2. Hedef sütununda uygun görüntüleri işaretleyin.
3. İşaretlenen görüntülerde hedeflerin görünür olduğunu doğrulayın.
4. Gerekirse hedef algılama ayarlarını değiştirin.

***

## Başarılı İşleme İçin İpuçları

### Başlamadan Önce

1. **Önce küçük bir alt kümeyle test edin** - Ayarları doğrulamak için 10-20 görüntüyü işleyin
2. **Kullanılabilir disk alanını kontrol edin** - Veri kümesinin 2-3 katı kadar boş alan olduğundan emin olun
3. **Gereksiz uygulamaları kapatın** - Sistem kaynaklarını boşaltın
4. **Hedef görüntüleri doğrulayın** - Kaliteyi sağlamak için işaretlenmiş hedefleri önizleyin
5. **Projeyi kaydedin** - Proje otomatik olarak kaydedilir, ancak manuel olarak kaydetmek iyi bir uygulamadır.

### İşleme Sırasında

1. **Sistemin uyku moduna geçmesini önleyin** - Güç tasarrufu modlarını devre dışı bırakın.
2. **Chloros&#x27;i ön planda tutun** - Veya en azından görev çubuğunda görünür durumda tutun.
3. **Ara sıra ilerlemeyi izleyin** - Uyarılar veya hatalar olup olmadığını kontrol edin.
4. **Diğer ağır uygulamaları yüklemeyin** - Özellikle Chloros+ paralel modunda

### Chloros+ GPU Hızlandırma

NVIDIA GPU hızlandırma kullanıyorsanız:

1. NVIDIA sürücülerini en son sürüme güncelleyin
2. GPU&#x27;nun 4 GB+ VRAM&#x27;e sahip olduğundan emin olun
3. GPU&#x27;yu yoğun şekilde kullanan uygulamaları (oyunlar, video düzenleme) kapatın
4. GPU sıcaklığını izleyin (yeterli soğutma sağlandığından emin olun)

***

## Sonraki Adımlar

İşleme başladıktan sonra:

1. **İlerlemeyi izleyin** - [İşlemi İzleme](monitoring-the-processing.md) bölümüne bakın
2. **Tamamlanmasını bekleyin** - İşleme otomatik olarak çalışır
3. **Sonuçları inceleyin** - Bkz. [İşlemeyi Tamamlama](finishing-the-processing.md)

İşleme sırasında ne yapmanız gerektiği hakkında bilgi için bkz. [İşlemeyi İzleme](monitoring-the-processing.md).
