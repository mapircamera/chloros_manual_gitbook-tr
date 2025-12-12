# Projeye Dosya Ekleme

Chloros&#x27;te bir proje oluşturduktan veya açtıktan sonra, bir sonraki adım işleme başlamak için çok spektrumlu görüntülerinizi eklemektir. Dosya Tarayıcı<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesi, görüntüleri içe aktarmayı ve veri kümenizi yönetmeyi kolaylaştırır.

## Dosya Tarayıcıya Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. Sol kenar çubuğundaki **Dosya Tarayıcı** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> simgesine tıklayın
3. Dosya Tarayıcı paneli, projenizin dosya listesini görüntüler

{% ipucu style=&quot;info&quot; %}
**Desteklenen Dosya Türleri**: Chloros, MAPIR Survey3W ve Survey3N kameralardan RAW+JPG ve JPG görüntü dosyalarını destekler. Yalnızca RAW+JPG önerilir.
{% endhint %}

***

## Projenize Görüntü Ekleme

Projenize görüntü eklemenin iki temel yolu vardır:

### Yöntem 1: Dosya Ekleme

Bu seçeneği, tek tek görüntü dosyalarını veya küçük bir dosya seçimini içe aktarmak için kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Dosya Ekle&quot;** düğmesini tıklayın.
2. Görüntülerinizin bulunduğu klasöre gidin.
3. Bir veya daha fazla görüntü dosyası seçin (birden fazla dosya seçmek için **Ctrl** tuşunu basılı tutun).
4. Seçilen dosyaları içe aktarmak için **&quot;Aç&quot;** düğmesini tıklayın.

### Yöntem 2: Klasör Ekleme

Bir klasördeki tüm görüntüleri bir kerede içe aktarmak için bu seçeneği kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Klasör Ekle&quot;** düğmesini tıklayın.
2. Yakalama oturumu resimlerinizi içeren klasöre gidin ve seçin.
3. **&quot;Klasörü Seç&quot;** düğmesini tıklayarak o klasördeki tüm desteklenen resimleri içe aktarın.

***

## Dosya Tarayıcı Tablosunu Anlama

Resimler içe aktarıldıktan sonra, aşağıdaki sütunların bulunduğu bir tabloda görünürler:

### Küçük Resim

* Her görüntünün küçük önizlemesi
* Ana önizleme alanında görüntünün tamamını görüntülemek için küçük resme tıklayın.

### Dosya Adı

* Kameradan alınan orijinal dosya adı
* Kameranın adlandırma kuralını korur (ör. IMG\_0001.RAW)

### Zaman Damgası

* Görüntünün çekildiği tarih ve saat
* Görüntünün EXIF meta verilerinden çıkarılır
* PPK senkronizasyonu ve kalibrasyon hedefi algılama için kullanılır

### Kamera Modeli

* Otomatik olarak algılanan kamera ve filtre yapılandırması
* Örnekler: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* Doğru işleme profillerini uygulamak için kullanılır

### Hedef Sütunu (Onay Kutusu)

* Kalibrasyon hedefleri içeren görüntüler için bu kutuyu işaretleyin
* İşleme sırasında hedef algılamayı büyük ölçüde hızlandırır
* Ayrıntılar için [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümüne bakın.

***

## Projenizdeki Dosyaları Yönetme

### Dosyaları Kaldırma

Projenizden istenmeyen görüntüleri kaldırmak için:

1. Dosya Tarayıcı tablosunda bir veya daha fazla görüntü seçin
2. **&quot;Seçilenleri Kaldır&quot;** düğmesini tıklayın
3. Kaldırmayı onaylayın (dosyalar diskten silinmez, yalnızca projeden kaldırılır)

### Sıralama ve Filtreleme

* **Sütuna göre sıralama**: Görüntüleri sıralamak için herhangi bir sütun başlığını tıklayın
* **Zaman damgasına göre sıralama**: Kronolojik çekim dizilerini düzenlemek için kullanışlıdır.
* **Kamera modeli filtresi**: Birden fazla kamera kullanıyorsanız, görüntüleri kamera türüne göre gruplandırın.

***

## Görüntü Önizleme

### Tam Görüntüyü Görüntüleme

Dosya Tarayıcı&#x27;da herhangi bir görüntü küçük resmini tıklayarak ana önizleme alanında görüntüleyin:

1. Görüntü, orta önizleme panelinde görünür.
2. Görüntü ayrıntılarını incelemek için yakınlaştırma kontrollerini kullanın.
3. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Hızlı Gezinme

* **Önceki Görüntü**: Sol oka tıklayın veya ← tuşuna basın
* **Sonraki Görüntü**: Sağ oka tıklayın veya → tuşuna basın
* **Yakınlaştırma/Uzaklaştırma**: Fare tekerleğini veya yakınlaştırma düğmelerini kullanın
* **Kaydırma**: Yakınlaştırıldığında görüntü üzerinde tıklayın ve sürükleyin

***

## Yinelenen Dosya İşleme

Chloros, yinelenen dosyaları otomatik olarak algılar ve yok sayar:

* Aynı dosya adına sahip dosyalar atlanır
* Yanlışlıkla çift işlemeyi önler
* Yinelenen dosyalar algılandığında uyarı mesajı görüntülenir

{% hint style=&quot;warning&quot; %}
**Önemli**: İçe aktarmadan önce orijinal görüntü dosyalarınızı yeniden adlandırmayın veya değiştirmeyin. Chloros, doğru işleme için orijinal dosya adlarına ve meta verilere güvenir.
{% endhint %}

***

## Karışık Kamera Veri Kümeleri

Projeniz birden fazla MAPIR kameradan alınan görüntüler içeriyorsa:

1. Chloros her kamera modelini otomatik olarak algılar
2. Her kamera türü, uygun kalibrasyon profiliyle işlenir
3. Dosya Tarayıcı, Kamera Modeli sütununda kamera modelini görüntüler
4. İşleme, her kamera türü için doğru ayarları uygular

**Örnek senaryo**: Survey3W RGN + Survey3N OCN çift kamera kurulumu

***

## En İyi Uygulamalar

### İçe Aktarmadan Önce Düzenleyin

* Kalibrasyon hedef görüntülerini anket görüntüleri ile aynı klasörde saklayın
* Kameranızın/SD kartınızın orijinal klasör yapısını koruyun
* Farklı oturumlara ait veri kümelerini tek bir projede karıştırmayın

### Dosya Adlandırma

* Orijinal kamera dosya adlarını koruyun (IMG\_0001.RAW, vb.)
* İçe aktarmadan önce dosya adlarını değiştirmeyin
* Orijinal adlar önemli meta veriler içerir

### Kalibrasyon Hedef Görüntüleri

* Her oturumda her zaman 1-2 kalibrasyon hedef görüntüsü ekleyin.
* Yakalama oturumundan önce ve sonra hedefleri yakalayın.
* Hedefleri, yakalama alanıyla aynı aydınlatma koşullarına yerleştirin.
* İşlemeyi hızlandırmak için Hedef onay kutusunu kullanarak hedef görüntüleri işaretleyin.

***

## Sık Karşılaşılan Sorunlar ve Çözümleri

### İçe Aktardıktan Sonra Görüntüler Görünmüyor

**Olası nedenler:**

* Dosya biçimi desteklenmiyor (yalnızca MAPIR kameralardan RAW+JPG ve JPG)
* Görüntüler MAPIR kameralardan alınmamış (bkz. [Desteklenen Kameralar](../supported-cameras.md))
* Dosya bozuk veya SD karttan aktarım eksik

**Çözüm**: Dosya formatını ve kamera modelinin uyumluluğunu kontrol edin.

### Kamera Modeli Algılanmıyor

**Olası nedenler:**

* Değiştirilmiş EXIF meta verileri
* Harici yazılımda düzenlenmiş görüntüler
* Eksik dosya aktarımı

**Çözüm**: Kameradan/SD karttan orijinal, değiştirilmemiş dosyaları yeniden içe aktarın.

### Eksik Zaman Damgaları

**Olası nedenler:**

* Kamera saati doğru ayarlanmamış
* EXIF verileri harici yazılım tarafından silinmiş

**Çözüm**: Çekim sırasında kamera saat ayarlarının doğru olduğunu doğrulayın

***

## Sonraki Adımlar

Dosyalarınız içe aktarıldıktan sonra:

1. **Dosya listesini inceleyin** - Tüm görüntülerin doğru yüklendiğinden emin olun
2. **Kamera modellerini kontrol edin** - Doğru kamera algılamasını doğrulayın
3. **Hedef görüntüleri işaretleyin** - [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümüne bakın.
4. **Ayarları düzenleyin** - [Proje Ayarları](adjusting-project-settings.md) bölümünde işleme seçeneklerini yapılandırın.
5. **İşlemeyi başlatın** - [İşlemeyi Başlatma](starting-the-processing.md) bölümüne bakın.

Proje yapılandırması hakkında ayrıntılı bilgi için [Proje Ayarlarını Ayarlama](adjusting-project-settings.md) bölümüne bakın.
