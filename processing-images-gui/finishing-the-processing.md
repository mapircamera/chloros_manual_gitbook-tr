# İşlemi Tamamlama

Chloros işlemi tamamlandığında, sonuçlarınızı gözden geçirme, çıktı kalitesini doğrulama ve iş akışınızda kullanmak üzere işlenmiş görüntülerinizi hazırlama zamanı gelmiştir. Bu sayfa, son adımları ve sonraki eylemleri size gösterir.

## İşleme Tamamlandı Göstergesi

İşleme başarıyla tamamlandığında, birkaç gösterge göreceksiniz:

* ✅ **İlerleme çubuğu**: %100 tamamlanma oranına ulaşır
* ✅ **Hata Ayıklama Günlüğü**: &quot;İşleme Tamamlandı&quot; mesajını gösterir
* ✅ **Başlat düğmesi**: Tekrar etkin hale gelir (bir sonraki işleme çalıştırması için hazır)
* ✅ **Çıktı dosyaları**: İşlenen tüm görüntüler kamera modeli alt klasörüne kaydedilir

***

## İşlenen Görüntülerinizi Bulma

### Çıktı Klasörünü Açma

1. **Ana Menü** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> simgesine tıklayın (sol üst)
2. **&quot;Proje Klasörünü Aç&quot;** seçeneğini seçin
3. Dosya gezgininiz proje dizininde açılır
4. Projenizi adına göre bulun

***

## İşlenmiş Görüntüleri İnceleme

### Dosya Gezgini&#x27;nde Hızlı Önizleme

**Windows yerleşik önizleme:**

1. Kamera modeli alt klasörüne gidin
2. Bir görüntü dosyası seçin
3. Önizleme, Windows Explorer önizleme bölmesinde görünür
4. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Harici Görüntü Görüntüleyicilerinde Önizleme

**Önerilen görüntüleyiciler:**

* **QGIS** - Ücretsiz GIS yazılımı (coğrafi referanslı multispektral analiz için en iyisi)
* **IrfanView** - Hızlı, hafif görüntü görüntüleyici (TIFF&#x27;i destekler)
* **Adobe Photoshop** - Profesyonel düzenleme (TIFF desteği)
* **GIMP** - Photoshop&#x27;a ücretsiz alternatif
* **Windows Photos** - Temel görüntüleme (16 bit TIFF&#x27;i desteklemeyebilir)

### Chloros Görüntü Görüntüleyicide Önizleme

Gelişmiş görselleştirme için Chloros&#x27;in yerleşik Görüntü Görüntüleyicisini kullanın:

1. Dosya Tarayıcıda bir görüntü küçük resmini tıklayın
2. Görüntü ana önizleme alanında açılır
3. Sol kenar çubuğundaki **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesine tıklayın.
4. Etkileşimli analiz için [Dizin/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) kullanın.

Ayrıntılı talimatlar için [Görüntü Görüntüleyici](../image-viewer-gui/opening-an-image-full-screen.md) bölümüne bakın.

***

## Hata Ayıklama Günlüğünü İnceleme

### Uyarıları veya Hataları Kontrol Edin

1. **Hata Ayıklama Günlüğü** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> sekmesini açın
2. Mesajları kaydırın
3. Sarı uyarıları veya kırmızı hataları arayın
4. Belirtilen sorunları inceleyin
5. Yardım için MAPIR desteğine başvurun

### Günlüğü Kaydetme

İşleme kaydını saklamak veya MAPIR Desteğine göndermek için:

1. **&quot;Kopyala&quot;** veya **&quot;İndir&quot;** düğmesini tıklayın
2. Proje klasörüne metin dosyası olarak kaydedin
3. Proje belgelerine ekleyin
4. Sorunla karşılaşırsanız MAPIR desteğine gönderin

***

## Yaygın Çıktı Sorunları ve Çözümleri

### Sorun: Eksik Çıktı Dosyaları

**Olası nedenler:**

* Dosyalar işleme kriterlerini karşılamadı
* Yalnızca hedef görüntüler (dışa aktarımdan hariç tutuldu)
* Dışa aktarım sırasında disk alanı doldu
* İşleme sırasında dosya bozuldu

**Çözümler:**

1. Hata ayıklama günlüğünde atlama/hata mesajlarını kontrol edin
2. Disk alanının yeterli olduğunu doğrulayın
3. Dosyaları sayın: (orijinal sayı - hedef sayı) × (endeksler + 1)
4. Eksik dosyaları yeniden içe aktarın ve yeniden işleyin

### Sorun: Koyu veya Parlak Kenarlar (Vinyet Hala Görünür)

**Olası nedenler:**

* Vinyet düzeltme devre dışı bırakılmış
* Kamera/lens Chloros profil veritabanında yok
* Düzeltme kapasitesinin ötesinde aşırı vinyet

**Çözümler:**

1. Proje Ayarları&#x27;nda vinyet düzeltmesinin etkinleştirildiğini doğrulayın.
2. Kamera modelinin doğru algılandığını kontrol edin.
3. Vinyet devam ederse MAPIR desteğine başvurun.

### Sorun: Yanlış Renkler veya Değerler

**Olası nedenler:**

* Kalibrasyon hedefi algılanmadı.
* Yanlış kalibrasyon hedefi modeli seçildi.
* Yansıma kalibrasyonu devre dışı bırakıldı.
* Hedef görüntülerin kalitesi düşük.

**Çözümler:**

1. Yansıma kalibrasyonunun etkinleştirildiğini doğrulayın.
2. Hata Ayıklama Günlüğünde &quot;Hedef bulundu&quot; mesajlarını kontrol edin.
3. Hedef görüntü kalitesini gözden geçirin.
4. Uygun hedefler işaretlenerek yeniden işleyin.

### Sorun: NDVI Değerleri Yanlış Görünüyor

**Beklenen NDVI aralıkları:**

* **Su, kayalar, toprak**: -0,1 ila 0,2
* **Seyrek/sağlıksız bitki örtüsü**: 0,2 ila 0,4
* **Orta derecede bitki örtüsü**: 0,4 ila 0,6
* **Sağlıklı, yoğun bitki örtüsü**: 0,6 ila 0,9

**Değerler bu aralıkların dışındaysa:**

1. Yansıma kalibrasyonunun uygulandığını doğrulayın.
2. Işık sensörü günlüğünün dahil edildiğini doğrulayın.
3. Kalibrasyon hedeflerinin algılandığını kontrol edin.
4. Doğru kamera modelinin algılandığından emin olun.
5. Hedef görüntü yakalama zamanlamasını ve koşullarını gözden geçirin.

***

## İşlenmiş Görüntülerinizi Kullanma

### Fotogrametri / Orto-mozaik Oluşturma İçin

**Önerilen iş akışı:**

1. **Kalibre edilmiş yansıma görüntülerini** fotogrametri yazılımına içe aktarın:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **EXIF meta verilerini saklayın**: Coğrafi etiketleme için GPS verilerinin korunmasını sağlayın
3. **Kalibre edilmiş iş akışları**: Bilimsel doğruluk için yansıma görüntülerini kullanın
4. **Dizin mozaiklerini işleyin**: Tek tek indeks görüntülerinden NDVI ortomozaikler oluşturun
5. **Coğrafi referanslı GeoTIFF&#x27;i dışa aktarın**: GIS uygulamalarında kullanmak için

### GIS Analizi için

**Önerilen iş akışı:**

1. **QGIS, ArcGIS veya benzeri bir programa yükleyin**
2. **16 bit TIFF** yansıma görüntülerini çok bantlı analiz için kullanın
3. **Dizin görüntülerini** (NDVI, NDRE) kullanıma hazır bitki örtüsü katmanları olarak kullanın
4. **Raster hesaplayıcı**: Özel analiz için bantları birleştirin
5. **Dışa aktarma**: Sınıflandırma haritaları oluşturun, değişiklik algılama, bitki örtüsü sağlık haritaları

### Doğrudan Analiz / Raporlama için

**Önerilen iş akışı:**

1. Görsel raporlar için **LUT renkli indeks görüntüleri kullanın**
2. **İstatistikleri çıkarın**: Alan/parsel başına ortalama NDVI
3. **Zaman serisi**: Birden fazla oturumda indeksleri karşılaştırın
4. **Raporlar oluşturun**: Haritalar, istatistikler ve görselleştirmeler ekleyin

***

## Arşivleme ve Yedekleme

### Önerilen Yedekleme Stratejisi

**Kaydedilecekler:**

* ✅ **Orijinal RAW/JPG görüntüler** - Ayrı bir sürücüde/bulutta arşivleyin
* ✅ **İşlenmiş çıktılar** - Kalibre edilmiş görüntüleri ve endeksleri saklayın
* ✅ **Proje dosyası** - Gerekirse yeniden işleme için tüm ayarları içerir
* ✅ **Hata ayıklama günlüğü** - İşleme ayrıntılarını belgeler
* ✅ **Kalibrasyon hedef görüntüleri** - Doğrulama ve yeniden işleme için

**Depolama önerileri:**

* **Anında yedekleme**: Harici sabit sürücü
* **Uzun vadeli arşiv**: Bulut depolama (Google Drive, Dropbox vb.)
* **Önemli veriler**: Farklı konumlarda 2-3 kopya saklayın

***

## Sonraki İşleme Çalıştırmaları

### Proje Ayarlarını Yeniden Kullanma

Gelecekte benzer veri kümelerini işleyecekseniz:

1. **Proje Şablonunu Kaydedin** (henüz yapılmadıysa)
2. Kaydedilen şablonu kullanarak **yeni proje oluşturun**
3. **Yeni görüntüleri içe aktarın**
4. Tutarlılık için aynı ayarlarla **işleyin**

### Birden Çok Oturumu Toplu İşleme

Birden çok oturum/veri kümesi için:

**Seçenek 1: GUI - Birden Çok Proje**

* Her oturum için ayrı bir proje oluşturun
* Tutarlı şablon ayarları kullanın
* Tek tek işleyin

**Seçenek 2: Chloros CLI (yalnızca Chloros+)**

* Toplu işlemeyi otomatikleştirin
* Komut dosyalarıyla birden fazla klasörü işleyin
* [CLI Belgeleri](../CLI.md) bölümüne bakın.

**Seçenek 3: Python SDK (yalnızca Chloros+)**

* Programlı kontrol
* Analiz boru hatlarıyla entegrasyon
* Bkz. [API Belgeleri](../api-python-sdk.md)

***

## Son İşleme Sorun Giderme

### Farklı Ayarlarla Yeniden İşleme

Sonuçlar tatmin edici değilse:

1. Orijinal görüntüleri saklayın (asla silmeyin)
2. Chloros&#x27;te aynı projeyi açın
3. Proje Ayarları panelinde ayarları değiştirin
4. Tekrar işleyin - çıktılar önceki sonuçların üzerine yazacaktır

### Görüntülerin Alt Kümesi İşleme

Yalnızca belirli görüntüleri yeniden işlemek için:

1. Yeni proje oluşturun
2. Yalnızca yeniden işlenmesi gereken görüntüleri içe aktarın
3. Aynı ayar şablonunu kullanın
4. Daha küçük veri kümesini işleyin

### Yardım Alma

Sorunla karşılaşırsanız:

* 📧 **E-posta**: info@mapir.camera (Hata Ayıklama Günlüğünü ekleyin)
* 🌐 **Destek**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **SSS**: [Sık Sorulan Sorular](../faq.md)
* 📖 **Belgeler**: [Chloros Kılavuzu](../)

***

## Özet: Tam İş Akışı

Chloros işleme iş akışını tamamladınız:

1. ✅ **Proje oluşturuldu** - Bkz. [Projeler](../projects.md)
2. ✅ **Dosya ekleme** - Bkz. [Dosya Ekleme](adding-files-to-a-project.md)
3. ✅ **Ayarları düzenleme** - Bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md)
4. ✅ **Hedefleri işaretleme** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
5. ✅ **İşleme başlandı** - Bkz. [İşleme Başlatma](starting-the-processing.md)
6. ✅ **İlerleme izlendi** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
7. ✅ **Sonuçlar incelendi** - Bu sayfa

**Kalibre edilmiş, yansıma düzeltmeli multispektral görüntüleriniz analiz için hazır!**

***

## Ek Kaynaklar

### Gelişmiş Özellikler

* [**Görüntü Görüntüleyici**](../image-viewer-gui/opening-an-image-full-screen.md) - Etkileşimli görselleştirme ve analiz
* [**Dizin/LUT Sandbox**](../image-viewer-gui/index-lut-sandbox.md) - Özel dizin testi
* [**Çok Spektral Dizin Formülleri**](../project-settings/multispectral-index-formulas.md) - Tam dizin referansı

### Otomasyon ve Entegrasyon

* [**CLI Belgeleri**](../CLI.md) - Komut satırı toplu işleme
* [**Python SDK**](../api-python-sdk.md) - Programlı otomasyon
* [**Chloros+ Özellikleri**](../#chloros) - Gelişmiş işleme yetenekleri

### Destek ve Öğrenme

* [**SSS**](../faq.md) - Sık sorulan soruların yanıtları
* [**Kalibrasyon Hedefleri**](../calibration-targets.md) - Yansıma kalibrasyonunu anlama
* [**Desteklenen Kameralar**](../supported-cameras.md) - Uyumlu donanım
