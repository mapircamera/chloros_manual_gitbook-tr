# Projeye Dosya Ekleme

Chloros&#x27;te bir proje oluşturduktan veya açtıktan sonra, bir sonraki adım işlemeyi başlatmak için çok spektral görüntülerinizi eklemektir. Dosya Tarayıcı<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesi, görüntüleri içe aktarmayı ve veri kümenizi yönetmeyi kolaylaştırır.

## Dosya Tarayıcıya Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. Sol kenar çubuğundaki **Dosya Tarayıcı** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> simgesine tıklayın
3. Dosya Tarayıcı paneli, projenizin dosya listesini görüntüler

{% hint style="info" %}
**Desteklenen Dosya Türleri**: Chloros, MAPIR Survey3W ve Survey3N kameralardan RAW+JPG ve JPG görüntü dosyalarını destekler. Yalnızca RAW+JPG önerilir.
{% endhint %}

***

## Projenize Görüntü Ekleme

Projenize görüntü eklemenin iki temel yolu vardır:

### Yöntem 1: Dosya Ekleme

Bu seçeneği, tek tek görüntü dosyalarını veya az sayıda dosyayı içe aktarmak için kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Dosya Ekle&quot;** <img src="../.gitbook/assets/image.png" alt="" data-size="line"> düğmesine tıklayın.
2. Görüntülerinizin bulunduğu klasöre gidin.
3. Bir veya daha fazla görüntü dosyası seçin (birden fazla dosya seçmek için **Ctrl** tuşunu basılı tutun).
4. Seçilen dosyaları içe aktarmak için **&quot;Aç&quot;** düğmesine tıklayın.

### Yöntem 2: Klasör Ekleme

Bir klasördeki tüm görüntüleri bir kerede içe aktarmak için bu seçeneği kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Klasör Ekle&quot;** <img src="../.gitbook/assets/image (1).png" alt="" data-size="line"> düğmesine tıklayın.
2. Yakalama oturumu resimlerinizi içeren klasöre gidin ve seçin.
3. **&quot;Klasörü Seç&quot;** düğmesine tıklayarak o klasördeki tüm desteklenen resimleri içe aktarın.

***

## Dosya Tarayıcı Tablosunu Anlama

Resimler içe aktarıldıktan sonra, aşağıdaki sütunların bulunduğu bir tabloda görünürler:

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

Tablonun sağ üst köşesindeki geçiş düğmesine tıklamak, seçilen görüntünün meta verilerini görüntü ızgara alanında gösterir.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Projenizdeki Dosyaları Yönetme

### Dosyaları Kaldırma

Projenizden istenmeyen görüntüleri kaldırmak için:

1. Dosya Tarayıcı tablosunda bir veya daha fazla görüntü seçin
2. **&quot;Seçilenleri Kaldır&quot;** <img src="../.gitbook/assets/image (2).png" alt="" data-size="line"> düğmesine tıklayın.
3. Kaldırmayı onaylayın (dosyalar diskten silinmez, yalnızca projeden kaldırılır).

### Sıralama ve Filtreleme

* **Sütuna göre sıralama**: Görüntüleri sıralamak için herhangi bir sütun başlığına tıklayın.
* **Zaman damgasına göre sıralama**: Kronolojik çekim dizilerini düzenlemek için kullanışlıdır.
* **Kamera modeli filtresi**: Birden fazla kamera kullanıyorsanız, görüntüleri kamera türüne göre gruplandırın.

***

## Görüntü Önizleme

### Tam Görüntüyü Görüntüleme

Dosya Tarayıcı&#x27;da herhangi bir görüntü küçük resmini tıklayarak ana önizleme alanında görüntüleyin:

1. Görüntü, orta önizleme panelinde görünür
2. Görüntü ayrıntılarını incelemek için yakınlaştırma kontrollerini kullanın
3. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Hızlı Gezinme

* **Önceki Görüntü**: Sol ok tuşuna tıklayın veya ← tuşuna basın
* **Sonraki Görüntü**: Sağ ok tuşuna tıklayın veya → tuşuna basın
* **Yakınlaştırma/Uzaklaştırma**: Fare tekerleğini veya yakınlaştırma düğmelerini kullanın
* **Kaydırma**: Yakınlaştırıldığında görüntü üzerinde tıklayın ve sürükleyin

***

## Yinelenen Dosya İşleme

Chloros, yinelenen dosyaları otomatik olarak algılar ve yok sayar:

* Aynı dosya adına sahip dosyalar atlanır
* Yanlışlıkla çift işlemeyi önler
* Çoğaltmalar algılandığında uyarı mesajı görüntülenir

{% hint style="warning" %}
**Önemli**: İçe aktarmadan önce orijinal görüntü dosyalarınızı yeniden adlandırmayın veya değiştirmeyin. Chloros, doğru işleme için orijinal dosya adlarına ve meta verilere güvenir.
{% endhint %}

***

## Karışık Kamera Veri Kümeleri

Projeniz birden fazla MAPIR kameradan alınan görüntüleri içeriyorsa:

1. Chloros her kamera modelini otomatik olarak algılar
2. Her kamera türü, uygun kalibrasyon profiliyle işlenir
3. Dosya Tarayıcı, Kamera Modeli sütununda kamera modelini görüntüler
4. İşleme, her kamera türü için doğru ayarları uygular

**Örnek senaryo**: Survey3W RGN + Survey3N OCN çift kamera kurulumu

***

## En İyi Uygulamalar

### İçe Aktarmadan Önce Düzenleme

* Kalibrasyon hedef görüntülerini, anket görüntüleri ile aynı klasörde saklayın.
* Kameranızın/SD kartınızın orijinal klasör yapısını koruyun.
* Farklı oturumlara ait veri kümelerini tek bir projede karıştırmayın.

### Dosya Adlandırma

* Orijinal kamera dosya adlarını koruyun (IMG\_0001.RAW vb.).
* İçe aktarmadan önce dosyaları yeniden adlandırmayın.
* Orijinal adlar önemli meta veriler içerir.

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

* EXIF meta verileri değiştirilmiş
* Görüntüler harici yazılımda düzenlenmiş
* Dosya aktarımı eksik

**Çözüm**: Kamera/SD karttan orijinal, değiştirilmemiş dosyaları yeniden içe aktarın.

### Eksik Zaman Damgaları

**Olası nedenler:**

* Kamera saati doğru ayarlanmamış
* EXIF verileri harici yazılım tarafından silinmiş

**Çözüm**: Çekim sırasında kamera zaman ayarlarının doğru olduğunu doğrulayın

***

## Sonraki Adımlar

Dosyalarınız içe aktarıldıktan sonra:

1. **Dosya listesini inceleyin** - Tüm görüntülerin doğru yüklendiğinden emin olun
2. **Kamera modellerini kontrol edin** - Doğru kamera algılamasını doğrulayın
3. **Hedef görüntüleri işaretleyin** - [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümüne bakın
4. **Ayarları düzenleyin** - [Proje Ayarları](adjusting-project-settings.md) bölümünde işleme seçeneklerini yapılandırın
5. **İşlemeyi başlatın** - [İşlemeyi Başlatma](starting-the-processing.md) bölümüne bakın.

Proje yapılandırması hakkında ayrıntılı bilgi için [Proje Ayarlarını Ayarlama](adjusting-project-settings.md) bölümüne bakın.
