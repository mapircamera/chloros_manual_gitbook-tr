# İşlemin Tamamlanması

Chloros işlemi tamamlandığında, sonuçlarınızı gözden geçirme, çıktı kalitesini doğrulama ve iş akışınızda kullanmak üzere işlenmiş görüntülerinizi hazırlama zamanı gelmiştir. Bu sayfa, son adımlar ve sonraki işlemler konusunda size rehberlik eder.

## İşleme Tamamlandı Göstergesi

İşleme başarıyla tamamlandığında, birkaç gösterge göreceksiniz:

* ✅ **İlerleme çubuğu**: %100 tamamlanma oranına ulaşır
* ✅ **Hata Giderme Günlüğü**: &quot;İşleme Tamamlandı&quot; mesajını gösterir
* ✅ **Başlat düğmesi**: Tekrar etkin hale gelir (bir sonraki işleme çalışması için hazır)
* ✅ **Çıktı dosyaları**: İşlenmiş tüm görüntüler kamera modeli alt klasörüne kaydedilir***

## İşlenmiş Görüntülerinizi Bulma

### Çıktı Klasörünü Açma

1. **Ana Menü** <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> simgesine (sol üst) tıklayın
2. **&quot;Proje Klasörünü Aç&quot;** seçeneğini seçin
3. Dosya gezgini, proje dizininde açılır
4. Adına göre projenizi bulun

***

## İşlenmiş Görüntüleri İnceleme

### Dosya Gezgini&#x27;nde Hızlı Önizleme

**Windows yerleşik önizleme:**

1. Kamera modeli alt klasörüne gidin
2. Bir görüntü dosyası seçin
3. Önizleme, Windows Explorer önizleme bölmesinde görünür
4. Görüntüler arasında gezinmek için ok tuşlarını kullanın

### Harici Görüntü Görüntüleyicilerde Önizleme

**Önerilen görüntüleyiciler:*** **QGIS** - Ücretsiz GIS yazılımı (coğrafi referanslı multispektral analiz için en iyisi)
* **IrfanView** - Hızlı, hafif görüntü görüntüleyici (TIFF&#x27;i destekler)
* **Adobe Photoshop** - Profesyonel düzenleme (TIFF desteği)
* **GIMP** - Photoshop&#x27;a ücretsiz alternatif
* **Windows Photos** - Temel görüntüleme (16 bit TIFF&#x27;i desteklemeyebilir)

### Chloros Görüntü Görüntüleyicisinde Önizleme

Gelişmiş görselleştirme için Chloros&#x27;in yerleşik Görüntü Görüntüleyicisini kullanın:

1. Dosya Tarayıcısında bir görüntü küçük resmine tıklayın
2. Görüntü ana önizleme alanında açılır
3. Sol kenar çubuğundaki **Görüntü Görüntüleyicisi** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesine tıklayın
4. Etkileşimli analiz için [Dizin/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) öğesini kullanın

Ayrıntılı talimatlar için [Görüntü Görüntüleyicisi](../image-viewer-gui/opening-an-image-full-screen.md) bölümüne bakın.

***

## Hata Ayıklama Günlüğünü İnceleme

### Uyarıları veya Hataları Kontrol Etme

1. **Hata Giderme Günlüğü** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> sekmesini açın
2. Mesajları kaydırın
3. Sarı uyarıları veya kırmızı hataları arayın
4. Not edilen sorunları inceleyin
5. Yardım için MAPIR desteğine başvurun

### Günlüğü Kaydetme

İşleme kaydını tutmak veya MAPIR Desteğine göndermek için:

1. **&quot;Kopyala&quot;**veya**&quot;İndir&quot;** düğmesine tıklayın
2. Proje klasörüne metin dosyası olarak kaydedin
3. Proje belgelerine ekleyin
4. Sorunlarla karşılaşırsanız MAPIR desteğine gönderin

***

## Yaygın Çıktı Sorunları ve Çözümleri

### Sorun: Eksik Çıktı Dosyaları

**Olası nedenler:**

* Dosyalar işleme kriterlerini karşılamadı
* Yalnızca hedef görüntüler (dışa aktarımdan hariç tutuldu)
* Dışa aktarım sırasında disk alanı doldu
* İşleme sırasında dosya bozulması

**Çözümler:**

1. Hata ayıklama günlüğünde atlama/hata mesajlarını kontrol edin
2. Disk alanının yeterli olup olmadığını doğrulayın
3. Dosya sayısını sayın: (orijinal sayı - hedef sayı) × (indeksler + 1)
4. Eksik dosyaları yeniden içe aktarın ve yeniden işleyin

### Sorun: Karanlık veya Parlak Kenarlar (Vinyet Hala Görünür)

**Olası nedenler:**

* Vinyet düzeltmesi devre dışı
* Kamera/lens Chloros profil veritabanında yok
* Düzeltme kapasitesinin ötesinde aşırı vinyet

**Çözümler:**

1. Proje Ayarları&#x27;nda vinyet düzeltmesinin etkinleştirildiğini doğrulayın
2. Kamera modelinin doğru algılandığını kontrol edin
3. Vinyet sorunu devam ederse MAPIR destek ekibiyle iletişime geçin

### Sorun: Yanlış Renkler veya Değerler

**Olası nedenler:**

* Kalibrasyon hedefi algılanmadı
* Yanlış kalibrasyon hedefi modeli seçildi
* Yansıma kalibrasyonu devre dışı
* Hedef görüntülerin kalitesi düşük

**Çözümler:**

1. Yansıma kalibrasyonunun etkinleştirildiğini doğrulayın
2. Hata Giderme Günlüğü&#x27;ndeki &quot;Hedef bulundu&quot; mesajlarını kontrol edin
3. Hedef görüntü kalitesini inceleyin
4. Uygun hedefler işaretlenerek yeniden işleyin

### Sorun: NDVI Değerleri Yanlış Görünüyor

**Beklenen NDVI aralıkları:*** **Su, kayalar, toprak**: -0,1 ila 0,2
* **Seyrek/sağlıksız bitki örtüsü**: 0,2 ila 0,4
* **Orta derecede bitki örtüsü**: 0,4 ila 0,6
* **Sağlıklı, yoğun bitki örtüsü**: 0,6 ila 0,9**Değerler bu aralıkların dışındaysa:**

1. Yansıma kalibrasyonunun uygulandığını doğrulayın
2. Işık sensörü günlüğünün dahil edildiğini doğrulayın
3. Kalibrasyon hedeflerinin algılandığını kontrol edin
4. Doğru kamera modelinin algılandığından emin olun
5. Hedef görüntü yakalama zamanlamasını ve koşullarını gözden geçirin

***

## İşlenmiş Görüntülerinizi Kullanma

### Fotogrametri / Ortomozaik Oluşturma İçin

**Önerilen iş akışı:**

1.**Kalibre edilmiş yansıma görüntülerini** fotogrametri yazılımına aktarın:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **EXIF meta verilerini koruyun**: Coğrafi etiketleme için GPS verilerinin korunduğundan emin olun
3. **Kalibre edilmiş iş akışları**: Bilimsel doğruluk için yansıma görüntülerini kullanın
4. **Dizin mozaiklerini işleyin**: Tek tek indeks görüntülerinden NDVI ortomozaikler oluşturun
5. **Coğrafi referanslı GeoTIFF**&#x27;i dışa aktarın: GIS uygulamalarında kullanım için

### GIS Analizi için

**Önerilen iş akışı:**

1.**QGIS, ArcGIS veya benzeri bir programa yükleyin**

2.**Çok bantlı analiz için 16 bit TIFF** yansıma görüntülerini kullanın
3. **Endeks görüntülerini** (NDVI, NDRE) kullanıma hazır bitki örtüsü katmanları olarak kullanın
4. **Raster hesaplayıcı**: Özel analiz için bantları birleştirin
5. **Dışa aktarma**: Sınıflandırma haritaları, değişiklik tespiti, bitki örtüsü sağlık haritaları oluşturun

### Doğrudan Analiz / Raporlama için

**Önerilen iş akışı:**

1. Görsel raporlar için**LUT renklerine sahip indeks görüntülerini** kullanın
2. **İstatistikleri çıkarın**: Tarla/parsel başına ortalama NDVI
3. **Zaman serisi**: Birden fazla oturumdaki indeksleri karşılaştırın
4. **Raporlar oluşturun**: Haritaları, istatistikleri ve görselleştirmeleri dahil edin***

## Arşivleme ve Yedekleme

### Önerilen Yedekleme Stratejisi

**Neler kaydedilmeli:*** ✅ **Orijinal RAW/JPG görüntüler** - Ayrı bir sürücüde/bulutta arşivleyin
* ✅ **İşlenmiş çıktılar** - Kalibre edilmiş görüntüleri ve endeksleri saklayın
* ✅ **Proje dosyası** - Gerekirse yeniden işleme için tüm ayarları içerir
* ✅ **Hata Giderme Günlüğü** - İşleme ayrıntılarını belgeler
* ✅ **Kalibrasyon hedef görüntüleri** - Doğrulama ve yeniden işleme için**Depolama önerileri:*** **Hemen yedekleme**: Harici sabit sürücü
* **Uzun vadeli arşivleme**: Bulut depolama (Google Drive, Dropbox vb.)
* **Kritik veriler**: Farklı konumlarda 2-3 kopya saklayın***

## Sonraki İşleme Çalışmaları

### Proje Ayarlarını Yeniden Kullanma

Gelecekte benzer veri kümelerini işleyecekseniz:

1. **Proje Şablonunu Kaydedin** (henüz yapılmadıysa)
2. Kaydedilen şablonu kullanarak **yeni proje oluşturun**

3.**Yeni görüntüleri içe aktarın**

4. Tutarlılık için aynı ayarlarla**işleyin**### Birden Fazla Oturumu Toplu İşleme

Birden fazla oturum/veri kümesi için:**Seçenek 1: GUI - Birden Fazla Proje**

* Her oturum için ayrı bir proje oluşturun
* Tutarlı şablon ayarları kullanın
* Tek tek işleyin

**Seçenek 2: Chloros CLI (yalnızca Chloros+)**

* Toplu işlemeyi otomatikleştirin
* Komut dosyalarıyla birden fazla klasörü işleyin
* Bkz. [CLI Belgeleri](../CLI.md)

**Seçenek 3: Python SDK (Yalnızca Chloros ve üstü)**

* Programlı kontrol
* Analiz boru hatlarıyla entegrasyon
* Bkz. [API Belgeleri](../api-python-sdk.md)

***

## Son İşlemeyle İlgili Sorun Giderme

### Farklı Ayarlarla Yeniden İşleme

Sonuçlar tatmin edici değilse:

1. Orijinal görüntüleri saklayın (asla silmeyin)
2. Aynı projeyi Chloros&#x27;te açın
3. Proje Ayarları panelinde ayarları değiştirin
4. Tekrar işleyin - çıktılar önceki sonuçların üzerine yazacaktır

### Görüntülerin Bir Kısmını İşleme

Yalnızca belirli görüntüleri yeniden işlemek için:

1. Yeni bir proje oluşturun
2. Yalnızca yeniden işlenmesi gereken görüntüleri içe aktarın
3. Aynı ayar şablonunu kullanın
4. Daha küçük veri kümesini işleyin

### Yardım Alma

Sorun yaşarsanız:

* 📧 **E-posta**: info@mapir.camera (Hata Giderme Günlüğünü ekleyin)
* 🌐 **Destek**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **SSS**: [Sık Sorulan Sorular](../faq.md)
* 📖 **Belgeler**: [Chloros Kılavuzu](../)***

## Özet: İş Akışını Tamamlama

Chloros işleme iş akışını tamamladınız:

1. ✅ **Proje oluşturuldu** - Bkz. [Projeler](../projects.md)
2. ✅ **Dosyalar eklendi** - Bkz. [Dosya Ekleme](adding-files-to-a-project.md)
3. ✅ **Ayarlar düzenlendi** - Bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md)
4. ✅ **Hedefler işaretlendi** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
5. ✅ **İşleme başlatıldı** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
6. ✅ **İlerleme izlendi** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
7. ✅ **Sonuçlar incelendi** - Bu sayfa**Kalibre edilmiş, yansıma düzeltmesi yapılmış multispektral görüntüleriniz analiz için hazır!**

***

## Ek Kaynaklar

### Gelişmiş Özellikler

* [**Görüntü Görüntüleyici**](../image-viewer-gui/opening-an-image-full-screen.md) - Etkileşimli görselleştirme ve analiz
* [**İndeks/LUT Sandbox**](../image-viewer-gui/index-lut-sandbox.md) - Özel indeks testi
* [**Multispektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Eksiksiz indeks referansı

### Otomasyon ve Entegrasyon

* [**CLI Belgeleri**](../CLI.md) - Komut satırı toplu işleme
* [**Python SDK**](../api-python-sdk.md) - Programlı otomasyon
* [**Chloros+ Özellikler**](../#chloros) - Gelişmiş işleme yetenekleri

### Destek ve Öğrenme

* [**SSS**](../faq.md) - Sık sorulan soruların yanıtları
* [**Kalibrasyon Hedefleri**](../calibration-targets.md) - Yansıma kalibrasyonunu anlama
* [**Desteklenen Kameralar**](../supported-cameras.md) - Uyumlu donanım
