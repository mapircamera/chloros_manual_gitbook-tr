# Projeye Dosya Ekleme

Chloros&#x27;te bir proje oluşturduktan veya açtıktan sonra, işleme başlamak için çok spektrumlu görüntülerinizi eklemeniz gerekir. Dosya Tarayıcı<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesi, görüntüleri içe aktarmayı ve veri kümenizi yönetmeyi kolaylaştırır.

## Dosya Tarayıcısına Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. Sol kenar çubuğundaki **Dosya Tarayıcı** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> simgesine tıklayın
3. Dosya Tarayıcı paneli, projenizin dosya listesini görüntüler

{% hint style="info" %}
**Desteklenen Dosya Türleri**: Chloros, MAPIR, Survey3W ve Survey3N kameralardan gelen RAW+JPG ve JPG görüntü dosyalarını destekler. Yalnızca RAW+JPG kullanılması önerilir.
{% endhint %}

***

## Projenize Görüntü Ekleme

Projenize görüntü eklemenin iki temel yolu vardır:

### Yöntem 1: Dosya Ekle

Tek tek görüntü dosyalarını veya az sayıda dosyayı içe aktarmak için bu seçeneği kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Dosya Ekle&quot;** <img src="../.gitbook/assets/image.png" alt="" data-size="line"> düğmesine tıklayın
2. Görüntülerinizin bulunduğu klasöre gidin
3. Bir veya daha fazla görüntü dosyası seçin (birden fazla dosya seçmek için **Ctrl** tuşunu basılı tutun)
4. Seçilen dosyaları içe aktarmak için **&quot;Aç&quot;** düğmesine tıklayın

### Yöntem 2: Klasör Ekle

Bir klasördeki tüm görüntüleri tek seferde içe aktarmak için bu seçeneği kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Klasör Ekle&quot;** <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> düğmesine tıklayın
2. Çekim oturumunuzun resimlerini içeren klasöre gidin ve seçin
3. O klasördeki desteklenen tüm resimleri içe aktarmak için **&quot;Klasörü Seç&quot;** düğmesine tıklayın***

## Dosya Tarayıcı Tablosunu Anlama

Resimler içe aktarıldıktan sonra, aşağıdaki sütunları içeren bir tabloda görünürler:

### Dosya Adı

* Kameradan alınan orijinal dosya adı
* Kamera adlandırma kuralını korur (ör. IMG\_0001.RAW)

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
* Ayrıntılar için [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümüne bakın

### Görüntü Meta Verilerini Görüntüleme

Tablonun sağ üst köşesindeki geçiş düğmesine tıkladığınızda, seçilen görüntünün meta verileri görüntü ızgarası alanında gösterilir.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Projenizdeki Dosyaları Yönetme

### Dosyaları Kaldırma

Projenizden istenmeyen görüntüleri kaldırmak için:

1. Dosya Tarayıcı tablosunda bir veya daha fazla görüntü seçin
2. **&quot;Seçilenleri Kaldır&quot;** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line"> düğmesine tıklayın
3. Kaldırma işlemini onaylayın (dosyalar diskten silinmez, yalnızca projeden kaldırılır)

### Sıralama ve Filtreleme

* **Sütuna göre sıralama**: Görüntüleri sıralamak için herhangi bir sütun başlığına tıklayın
* **Zaman damgası sıralaması**: Kronolojik çekim dizilerini düzenlemek için kullanışlıdır
* **Kamera modeli filtresi**: Birden fazla kamera kullanıyorsanız, görüntüleri kamera türüne göre gruplandırın***

## Görüntü Önizlemesi

### Tam Görüntüyü Görüntüleme

Dosya Tarayıcıdaki herhangi bir görüntü küçük resmine tıklayarak ana önizleme alanında görüntüleyin:

1. Görüntü, ortadaki önizleme panelinde görünür
2. Görüntü ayrıntılarını incelemek için yakınlaştırma kontrollerini kullanın
3. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Hızlı Gezinme

* **Önceki Görüntü**: Sol oka tıklayın veya ← tuşuna basın
* **Sonraki Görüntü**: Sağ ok tuşuna tıklayın veya → tuşuna basın
* **Yakınlaştırma/Uzaklaştırma**: Fare tekerleğini veya yakınlaştırma düğmelerini kullanın
* **Kaydırma**: Yakınlaştırıldığında görüntü üzerinde tıklayıp sürükleyin***

## Yinelenen Dosyaların İşlenmesi

Chloros, yinelenen dosyaları otomatik olarak algılar ve yok sayar:

* Dosya adları aynı olan dosyalar atlanır
* Yanlışlıkla iki kez işlenmesini önler
* Yinelenenler algılandığında uyarı mesajı görüntülenir

{% hint style="warning" %}
**Önemli**: İçe aktarmadan önce orijinal görüntü dosyalarınızı yeniden adlandırmayın veya değiştirmeyin. Chloros, doğru işleme için orijinal dosya adlarına ve meta verilere güvenir.
{% endhint %}

***

## Karışık Kamera Veri Kümeleri

Projeniz birden fazla MAPIR kameradan alınan görüntüler içeriyorsa:

1. Chloros her kamera modelini otomatik olarak algılar
2. Her kamera türü, uygun kalibrasyon profiliyle işlenir
3. Dosya Tarayıcı, Kamera Modeli sütununda kamera modelini görüntüler
4. İşleme, her kamera türü için doğru ayarları uygular

**Örnek senaryo**: Survey3W RGN + Survey3N OCN çift kamera kurulumu***

## En İyi Uygulamalar

### İçe Aktarmadan Önce Düzenleme

* Kalibrasyon hedef görüntülerini, anket görüntüleriyle aynı klasörde tutun
* Kameranızdan/SD kartınızdan gelen orijinal klasör yapısını koruyun
* Farklı oturumlara ait veri kümelerini tek bir projede karıştırmayın

### Dosya Adlandırma

* Orijinal kamera dosya adlarını koruyun (IMG\_0001.RAW, vb.)
* İçe aktarmadan önce dosyaları yeniden adlandırmayın
* Orijinal isimler önemli meta veriler içerir

### Kalibrasyon Hedef Görüntüleri

* Her oturumda mutlaka 1-2 kalibrasyon hedef görüntüsü ekleyin
* Çekim oturumundan önce ve sonra hedefleri çekin
* Hedefleri çekim alanıyla aynı aydınlatma koşullarına yerleştirin
* İşlemeyi hızlandırmak için Hedef onay kutusunu kullanarak hedef görüntüleri işaretleyin

***

## Sık Karşılaşılan Sorunlar ve Çözümleri

### İçe Aktarımdan Sonra Görüntüler Görünmüyor

**Olası nedenler:**

* Dosya biçimi desteklenmiyor (yalnızca MAPIR kameralardan RAW+JPG ve JPG)
* Görüntüler MAPIR kameralardan değil (bkz. [Desteklenen Kameralar](../supported-cameras.md))
* Dosya bozukluğu veya SD karttan aktarımın eksik olması

**Çözüm**: Dosya formatını ve kamera modelinin uyumluluğunu kontrol edin

### Kamera Modeli Algılanmıyor

**Olası nedenler:**

* Değiştirilmiş EXIF meta verileri
* Harici yazılımda düzenlenmiş görüntüler
* Eksik dosya aktarımı

**Çözüm**: Kamera/SD karttan orijinal, değiştirilmemiş dosyaları yeniden içe aktarın

### Eksik Zaman Damgaları

**Olası nedenler:**

* Kamera saati doğru ayarlanmamış
* EXIF verileri harici yazılım tarafından silinmiş

**Çözüm**: Çekim sırasında kamera saat ayarlarının doğru olduğunu doğrulayın***

## Sonraki Adımlar

Dosyalarınız içe aktarıldıktan sonra:

1. **Dosya listesini inceleyin** - Tüm görüntülerin doğru yüklendiğinden emin olun
2. **Kamera modellerini kontrol edin** - Kameranın doğru algılandığını doğrulayın
3. **Hedef görüntüleri işaretleyin** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
4. **Ayarları düzenleyin** - [Proje Ayarları](adjusting-project-settings.md) bölümünden işleme seçeneklerini yapılandırın
5. **İşlemeyi başlatın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)

Proje yapılandırması hakkında ayrıntılı bilgi için bkz. [Proje Ayarlarını Yapılandırma](adjusting-project-settings.md).
