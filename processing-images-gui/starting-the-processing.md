# İşlemeyi Başlatma

Görüntülerinizi içe aktardıktan, kalibrasyon hedeflerinizi işaretledikten ve proje ayarlarınızı yapılandırdıktan sonra, işlemeyi başlatmaya hazırsınız. Bu sayfa, Chloros işleme akışını başlatma sürecinde size rehberlik eder.

## İşleme Öncesi Kontrol Listesi

Başlat düğmesine tıklamadan önce her şeyin hazır olduğunu doğrulayın:

* [ ] **Dosyalar içe aktarıldı** - Tüm görüntüler Dosya Tarayıcısı'nda görünüyor
* [ ] **Hedef görüntüler işaretlendi** - Kalibrasyon görüntüleri için Hedef sütunu işaretlendi (veya LATTICE için içe aktarılan bir `.daq` kaydı)
* [ ] **Kamera modelleri algılandı** - Kamera Modeli sütununda doğru kameralar gösteriliyor
* [ ] **Ayarlar yapılandırıldı** - Proje Ayarları gözden geçirildi ve ayarlandı
* [ ] **İndeksler seçildi** - İstenen multispektral indeksler eklendi (gerekirse)
* [ ] **Dışa aktarım biçimi seçildi** - İş akışınıza uygun çıktı biçimi

{% hint style="info" %}
**İpucu**: İşlemeye başlamadan önce Dosya Tarayıcısı'ndaki birkaç görüntüyü tıklayarak doğru şekilde yüklendiklerini doğrulayın.
{% endhint %}

***

## İşlemeyi Başlatma

### Başlat Düğmesini Bulma

Başlat/Oynat düğmesi, Chloros'un üst başlık çubuğunda yer alır:

* Konum: Pencerenin üst orta kısmı
* Simge: **Oynat/Başlat düğmesi** <img src="../.gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">
* Durum: İşlemeye hazır olduğunda düğme etkinleştirilir (parlak)

### Başlatmak için Tıklayın

1. Üst başlıktaki **Oynat/Başlat düğmesine** tıklayın
2. İşleme hemen başlar
3. İşleme sırasında düğme bir **Durdur** düğmesine dönüşür
4. İlerleme çubuğu güncellenerek işleme durumunu gösterir

{% hint style="success" %}
**İşleme Başladı**: Tıklandığında, Chloros tüm işleme adımlarını (hedef algılama, debayering, kalibrasyon, indeks hesaplama ve dışa aktarma) otomatik olarak gerçekleştirir. Projenizin Survey3, LATTICE veya bunların bir karışımı olup olmadığını otomatik olarak algılar ve her kameraya uygun iş akışını uygular.
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

1.**Hedef Algılama** - Kalibrasyon hedeflerini tarama
2. **İşleme** - Kalibrasyonu uygulama ve görüntüleri dışa aktarma**İşleme süresi:**

* Chloros+ paralel moduna göre çok daha yavaştır
* Küçük ve orta ölçekli veri kümeleri için uygundur (< 200 görüntü)

### Chloros+ Modu (Paralel İşleme)

**Chloros+ lisansı gerektirir**

**Nasıl çalışır:**

* [4 iş parçacıklı işleme boru hattı](../processing-architecture/processing-pipeline.md) kullanarak birden fazla görüntüyü aynı anda işler
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md), çalıştırma başlangıcında donanımınız için en uygun stratejiyi otomatik olarak seçer
* NVIDIA grafik kartlarıyla GPU (CUDA) hızlandırması
* **İşçi sayısı donanıma göre uyarlanır**: GPU stratejileri**2-4 eşzamanlı işçi** çalıştırır (VRAM'e göre ölçeklenir — 2-12 GB'lık bir GPU'da 3, 12 GB ve üzeri bir GPU'da 2 ila 4); Yalnızca CPU içeren sistemlerde fiziksel çekirdek başına bir iş parçacığı çalıştırılır, ancak bir tanesi düşülür**İlerleme çubuğu 4 aşamayı gösterir** (4 iş akışı iş parçacığına karşılık gelir):

1. **Algılama** (İş Parçacığı 1) - Kalibrasyon hedeflerinin bulunması
2. **Analiz** (İş Parçacığı 2) - Görüntü meta verilerinin incelenmesi ve kalibrasyon hesaplanması
3. **Kalibrasyon** (İş Parçacığı 3) - Debayering, vinyet düzeltme, kalibrasyon, indeks hesaplama
4. **Dışa Aktarma** (İş Parçacığı 4) - İşlenmiş görüntülerin ve indekslerin kaydedilmesi**İlerleme çubuğu etkileşimi:*** Ayrıntılı 4 aşamalı açılır paneli görmek için fareyi çubuğun üzerine **getirin*** Açılır paneli sabitlemek için ilerleme çubuğuna **tıklayın*** Sabitlemeyi kaldırmak ve paneli gizlemek için **tekrar tıklayın**

**İşleme süresi:**

* Ücretsiz moddan önemli ölçüde daha hızlı
* GPU hızlandırması, hızı daha da artırır

{% hint style="info" %}
**Chloros+ Hızı**: Büyük veri kümeleri için paralel işleme, sıralı moddan 5-10 kat daha hızlı olabilir. Ücretsiz modda 2 saat süren 500 görüntülü bir proje, Chloros+ ile 15-20 dakikada tamamlanabilir.
{% endhint %}

***

## İşleme Sırasında Neler Olur?

### Aşama 1: Hedef Algılama

**Chloros'un yaptığı işlemler:**

* Hedef sütununda işaretlediğiniz görüntüleri tarar (hiçbiri işaretlenmemişse tüm görüntüleri tarar)
* Her hedefteki kalibrasyon panellerini tanımlar
* Hedef panellerden yansıma değerlerini çıkarır
* Kalibrasyon planlaması için hedef zaman damgalarını kaydeder

**Süre:** 1-30 saniye (işaretli hedefler için), 5-30+ dakika (işaretlenmemiş hedefler için)

### Aşama 2: Debayering (RAW Dönüştürme)

**Chloros'un yaptığı işlemler:**

* RAW Bayer desenli verileri tam 3 kanallı görüntülere dönüştürür (LATTICE mono modülleri tek bantlı kalır — bunlar için debayering atlanır ve günlüğe bir not eklenir)
* Seçilen demosaicing algoritmasını uygular
* Maksimum görüntü kalitesini ve ayrıntıyı korur

**Süre:** Görüntü sayısına ve CPU/GPU hızına göre değişir

### Aşama 3: Kalibrasyon

**Chloros'un yaptığı işlemler:*** **Vinyet düzeltme**: Kenarlardaki lens kararmasını giderir
* **Yansıma kalibrasyonu**: Hedef yansıma değerleri ve/veya DAQ aşağı doğru ışınım verileri kullanılarak normalleştirir
* Tüm bantlara/kanallara düzeltmeler uygular
* Zaman damgasına göre her görüntü için uygun kalibrasyon referansını kullanır

**Süre:** İşlem süresinin büyük kısmı

### Aşama 4: İndeks Hesaplama

**Chloros'un yaptığı işlemler:**

* Yapılandırılmış multispektral indeksleri (NDVI, NDRE vb.) hesaplar
* Kalibre edilmiş görüntülere bant matematik işlemleri uygular
* Seçilen her indeks için indeks görüntüleri oluşturur

**Süre:** Görüntü başına birkaç saniye

### Aşama 5: Dışa Aktarma

**Chloros'un yaptığı işlemler:**

* İşlenmiş görüntüleri seçilen formatta kaydeder
* **LATTICE fan-out**: Her bir ham LATTICE karesi, tek geçişte etkinleştirilmiş tüm ürünler olarak dışa aktarılır — debayering, önizleme, parlaklık (her zaman float32), yansıma
* Dosyaları proje çıktı dizinine yazar: `<project>/<camera>/<format>/<Product>_Images/`
* **Kaynak dosya adını korur** — ürün, klasör adıyla tanımlanır; son ek eklenmez**Süre:** Dışa aktarım formatına ve dosya boyutuna göre değişir***

## İşleme Davranışı

### Otomatik İşleme Boru Hattı

Başlatıldığında, tüm boru hattı otomatik olarak çalışır:

* Kullanıcı müdahalesi gerekmez
* Yapılandırılan tüm adımlar sırayla yürütülür
* İlerleme güncellemeleri gerçek zamanlı olarak gösterilir
* Dışa aktarılan dosyalar tamamlandıkça diske yazılır — işlem devam ederken tamamlanan çıktıları açabilirsiniz

### İşleme Sırasında Bilgisayar Kullanımı

**Serbest Mod:**

* Nispeten düşük CPU kullanımı (tek iş parçacıklı)
* Bilgisayar diğer görevler için yanıt vermeye devam eder
* Chloros'u simge durumuna küçültüp diğer uygulamalarda çalışmak güvenlidir

**Chloros+ Paralel Mod:**

* Stratejinin işçi havuzunda yüksek CPU kullanımı
* GPU hızlandırması ile: Yüksek GPU kullanımı
* İşlem sırasında bilgisayarın yanıt verme hızı düşebilir
* CPU'yu yoğun şekilde kullanan diğer görevleri başlatmaktan kaçının

{% hint style="warning" %}
**Performans İpucu**: En iyi Chloros+ performansı için diğer uygulamaları kapatın ve Chloros'un sistem kaynaklarını tam olarak kullanmasına izin verin.
{% endhint %}

### İşlem Duraklatılamaz (Ancak Durdurma İşlemi Sorunsuz Gerçekleşir)

* İşlem bir kez başlatıldığında duraklatılamaz ve daha sonra devam ettirilemez
* **Durdur** düğmesine tıklamak, ilk tıklamada işlemi temiz bir şekilde durdurur
* Durdurma işleminden önce zaten dışa aktarılmış ürünler diskte kalır
* Durdurulan bir işlem, tamamlanan kısımları doğru bir şekilde raporlar (günlükteki `[RUN-SUMMARY]` satırlarına bakın)
* Yeni bir işlem, iş akışını baştan başlatır

**Planlama ipucu:** Çok büyük projelerde, daha iyi kontrol için işlemleri gruplar halinde gerçekleştirmeyi veya CLI'yi kullanmayı düşünün.

***

## İşleminizi İzleme

İşlem devam ederken şunları yapabilirsiniz:

* **İlerleme çubuğunu izleyin** - Genel tamamlanma yüzdesini görün
* **Mevcut aşamayı görüntüleyin** - Algılama, Analiz, Kalibrasyon veya Dışa Aktarma
* **Günlük sekmesini kontrol edin** - Ayrıntılı işleme mesajlarını ve uyarıları görün
* **Tamamlanan görüntüleri önizleyin** - Dışa aktarılan dosyalar işleme sırasında diskte görünür

İzleme hakkında ayrıntılı bilgi için bkz. [İşlemeyi İzleme](monitoring-the-processing.md).

***

## İşlemeyi Durdurma

İşlemeyi durdurmanız gerekiyorsa:

### Nasıl Durdurulur

1. **Durdur düğmesini** bulun (işleme sırasında Başlat düğmesinin yerini alır)
2. Düğmeye bir kez tıklayın — işlenmekte olan görüntü tamamlanana kadar çubukta **"Durduruluyor..."** yazısı görünür
3. İşlem kesin olarak durdurulmuş durumda sona erer ve günlüğe tamamlanan işlemlerin doğru bir özeti olan `[RUN-SUMMARY]` yazdırılır

### Ne Zaman Durdurulmalı

**Durdurmak için geçerli nedenler:**

* Yanlış ayarların kullanıldığı fark edildiğinde
* Hedef görüntüleri işaretlemeyi unuttuğunuzda
* Yanlış görüntüler içe aktarıldığında
* Sistem çok yavaş çalışıyor veya yanıt vermiyorsa

**Durdurduktan sonra:**

* Durdurma işleminden önce dışa aktarılan ürünler diskte kalır
* Sorunları gözden geçirip düzeltin, gerektiği şekilde ayarları değiştirin
* İşlemeyi yeniden başlatın — işlem baştan başlar

***

## İşleme Süresi Tahminleri

Gerçek işleme süresi aşağıdakilere bağlı olarak büyük ölçüde değişir:

* Görüntü sayısı
* Görüntü çözünürlüğü
* RAW ve JPG giriş formatları
* İşleme modu (Ücretsiz ve Chloros+)
* CPU hızı ve çekirdek sayısı
* GPU kullanılabilirliği (yalnızca Chloros+)
* Hesaplanacak dizin sayısı
* Etkinleştirilmiş dışa aktarma ürünlerinin sayısı (LATTICE)

### Yaklaşık Tahminler (Chloros+, 12 MP görüntüler, modern CPU)

| Görüntü Sayısı | Ücretsiz Mod | Chloros+ (CPU) | Chloros+ (GPU) |
| ----------- | --------- | -------------- | -------------- |
| 50 görüntü   | 15-20 dak. | 5-8 dak.        | 3-5 dak.        |
| 100 görüntü  | 30-40 dak. | 10-15 dak.      | 5-8 dak.        |
| 200 görüntü  | 1-1,5 saat | 20-30 dakika      | 10-15 dakika      |
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
* Önceki işleme hâlâ devam ediyor
* Proje tam olarak yüklenmemiş

**Çözümler:**

1. Arka ucun tamamen başlatılmasını bekleyin (ana menü simgesini kontrol edin)
2. Dosya Tarayıcı'da görüntülerin içe aktarıldığını doğrulayın
3. Düğme devre dışı kalmaya devam ederse Chloros'u yeniden başlatın
4. Hata mesajları için Hata Günülüğü'nü kontrol edin

### İşleme Başlıyor, Ancak Hemen Hata Veriyor

**Olası nedenler:**

* Projede geçerli görüntü yok
* Bozuk görüntü dosyaları
* Yetersiz disk alanı
* Yetersiz bellek (RAM)

**Çözümler:**

1. Hata mesajları için Hata Günülüğünü <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> kontrol edin
2. Kullanılabilir disk alanını kontrol edin
3. Daha küçük bir görüntü alt kümesini işlemeyi deneyin
4. Görüntülerin bozuk olmadığını doğrulayın

### İşlem Tamamlandı Ancak Hiçbir Görüntü Yazılmadı

Görüntü ürünleri talep eden ancak hiçbirini yazmayan bir işlem, **başarı değil, hata** olarak değerlendirilir — Chloros bunu açıkça bildirir:

* GUI günlüğünde, olası nedeni belirten `[RUN-SUMMARY]` uyarıları görüntülenir — görüntü içe aktarılmamış, hedef algılanmamış veya istenen tüm ürünler uygulanamaz olduğu için atlanmış (ör. yalnızca RGB kameralardan parlaklık/yansıtma değeri istenmesi)
* CLI'deki karşılığı (`chloros-cli process`) ise `Processing finished but wrote no image products.` mesajını yazdırır ve **sıfırdan farklı bir değerle sonlanır**, böylece komut dosyaları bunu algılayabilir
* Kasıtlı olarak yalnızca meta veriye yönelik bir çalıştırma (tüm dışa aktarma ürünleri devre dışı bırakılmış, indeks yok) yine de başarılı olarak kabul edilir

Tam anlamı için [CLI Referansı](../reference/cli-reference.md#a-run-that-writes-no-images-fails) bölümüne bakın.

### Proje Klasörü Bu Bilgisayar İçin Çok Derin

**Yalnızca Windows, 1.3.0 sürümünde eklendi.** Windows, uzun yollar devre dışı bırakılmış olarak gelir ve bu durumda hiçbir dosya yolu 260 karakterden uzun olamaz. Chloros ürünleri, proje klasörünün yaklaşık 130 karakter altında yer alır; bu nedenle, bundan daha derin bir proje klasörü, ürünlerini alamaz. Chloros artık hiçbir şey yazmadan çalıştırmak yerine, işleme başlamadan önce bir kontrol yapar:

* Başlat düğmesine tıklamak çalışmayı başlatmaz: ilerleme çubuğu nedeni gösterir ve Hata Ayıklama Günlüğü'nde `[PROCESS] refused: The project folder is too deep for this computer…` mesajı görünür. Mesaj, proje klasörünün uzunluğunu ve sığabilecek en uzun proje klasörünü belirtir.
* Projenin kendi dosyaları için bile çok derin bir klasörde proje oluşturmak veya açmak da reddedilir. Bir mesaj kutusu, bunun nedenini, sığabilecek en uzun proje klasör adını ve uzun yol seçeneğini nasıl etkinleştireceğinizi belirtir; hiçbir şey oluşturulmaz veya açılmaz; Hata Giderme Günlüğü bunu `[PROJECT] refused: …` olarak kaydeder. Bir projeyi daha uzun bir adla kopyalamak da sınırı aşabilir: kopyalama yapılır ancak proje açılmaz ve aynı mesaj bunun nedenini belirtir.

**Çözümler:**

1. Projeleri daha kısa bir klasörde tutun — [Proje Ayarları](../project-settings/project-settings.md) içindeki**Çalışma Dizini**'ni değiştirin ve projeyi yeniden oluşturun, veya
2. Windows uzun yollarını etkinleştirin ve Chloros'u yeniden başlatın:**Windows uzun yollarını etkinleştir** seçeneği işaretli olarak (varsayılan olarak işaretlidir) Chloros kurulum programını yeniden çalıştırın veya Grup İlkesi'ni kullanın: *Bilgisayar Yapılandırması > Yönetim Şablonları > Sistem > Dosya Sistemi > Win32 uzun yollarını etkinleştir*.

### "Hedef Tespit Edilmedi" Uyarısı

**Olası nedenler:**

* Hedef görüntüleri işaretlemeyi unutmuş olabilirsiniz
* Hedef görüntülerde görünür hedefler bulunmuyor
* Hedef algılama ayarları çok katı

**Çözümler:**

1. [Hedef Görüntüleri Seçme](choosing-target-images.md)
2. Hedef sütununda uygun görüntüleri işaretleyin
3. İşaretlenen görüntülerde hedeflerin görünür olduğunu doğrulayın
4. Gerekirse hedef algılama ayarlarını düzenleyin

***

## Başarılı İşleme İçin İpuçları

### Başlamadan Önce

1. **Önce küçük bir alt kümeyle test edin** - Ayarları doğrulamak için 10-20 görüntüyü işleyin
2. **Kullanılabilir disk alanını kontrol edin** - Veri kümesinin 2-3 katı kadar boş alan olduğundan emin olun (tüm LATTICE ürünleri etkinleştirilmişse daha fazla)
3. **Gereksiz uygulamaları kapatın** - Sistem kaynaklarını boşaltın
4. **Hedef görüntüleri doğrulayın** - Kaliteyi sağlamak için işaretli hedeflerin önizlemesini yapın
5. **Projeyi kaydedin** - Proje otomatik olarak kaydedilir, ancak manuel olarak kaydetmek iyi bir uygulamadır

### İşleme Sırasında

1. **Sistemin uyku moduna geçmesini önleyin** - Güç tasarrufu modlarını devre dışı bırakın
2. **Chloros'u ön planda tutun** - Ya da en azından görev çubuğunda görünür olsun
3. **İlerlemeyi ara sıra izleyin** - Uyarı veya hata olup olmadığını kontrol edin
4. **Başka ağır uygulamaları yüklemeyin** - Özellikle Chloros+ paralel modunda

### Chloros+ GPU Hızlandırma

NVIDIA GPU hızlandırması kullanıyorsanız:

1. NVIDIA sürücülerini en son sürüme güncelleyin
2. GPU'nun 4 GB+ VRAM'e sahip olduğundan emin olun (eşzamanlı Texture Aware debayering için 7 GB+)
3. GPU'yu yoğun şekilde kullanan uygulamaları kapatın (oyunlar, video düzenleme)
4. GPU sıcaklığını izleyin (yeterli soğutma sağlandığından emin olun)

***

## Sonraki Adımlar

İşleme başladıktan sonra:

1. **İşlemin ilerleyişini izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
2. **İşlemin tamamlanmasını bekleyin** - İşleme otomatik olarak yürütülür
3. **Sonuçları inceleyin** - Bkz. [İşlemi Sonlandırma](finishing-the-processing.md)

İşlem sırasında ne yapmanız gerektiği hakkında bilgi için bkz. [İşlemi İzleme](monitoring-the-processing.md).
