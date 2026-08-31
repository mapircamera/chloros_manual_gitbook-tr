# Bir Projeye Dosya Ekleme

Chloros&#x27;te bir proje oluşturduktan veya açtıktan sonra, bir sonraki adım işleme sürecine başlamak için multispektral görüntülerinizi eklemektir. Dosya Tarayıcı (<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">) sekmesi, görüntüleri içe aktarmanızı ve veri kümenizi yönetmenizi kolaylaştırır.

## Dosya Tarayıcısına Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. Sol kenar çubuğundaki **Dosya Tarayıcısı** <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> simgesine tıklayın
3. Dosya Tarayıcısı paneli, projenizin dosya listesini gösterecektir

{% hint style="info" %}
**Desteklenen Dosya Türleri**:

* **Survey3W / Survey3N**: RAW+JPG çiftleri ve JPG görüntüleri (RAW+JPG önerilir)
* **LATTICE**: `.tif` / `.tiff` kayıtları — Chloros kamera kontrolü veya bir LATTICE hub tarafından yazılan
* **Işık sensörü verileri**: `.daq` kayıtları (DAQ-U/M/E) ve DAQ-M `.csv` aşağı doğru ışınım kayıtları — yansıma kalibrasyonunu gerçekleştirmek için görüntülerle birlikte içe aktarılır
{% endhint %}

***

## Projenize Görüntü Ekleme

Projenize görüntü eklemenin iki temel yolu vardır:

### Yöntem 1: Dosya Ekleme

Tek tek görüntü dosyalarını veya az sayıda dosyayı içe aktarmak için bu seçeneği kullanın.

1. Dosya Tarayıcı panelinin üst kısmındaki **&quot;Dosya Ekle&quot;** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> düğmesine tıklayın
2. Görüntülerinizin bulunduğu klasöre gidin
3. Bir veya daha fazla görüntü dosyası seçin (birden fazla dosya seçmek için **Ctrl** tuşunu basılı tutun)
4. Seçilen dosyaları içe aktarmak için **&quot;Aç&quot;** düğmesine tıklayın

### Yöntem 2: Klasör Ekle

Bir klasördeki tüm görüntüleri tek seferde içe aktarmak için bu seçeneği kullanın. Tek bir iletişim kutusunda **birden fazla klasör** seçebilirsiniz.

1. Dosya Tarayıcı panelinin üst kısmında bulunan **&quot;Klasör Ekle&quot;** <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> düğmesine tıklayın
2. Yakalama oturumu resimlerinizi içeren klasör(ler)e gidin ve bunları seçin
3. Desteklenen tüm resimleri içe aktarmak için **&quot;Klasörü Seç&quot;**&#x27;e tıklayın

{% hint style="info" %}
**Yüklenemeyen dosyalar bildirilir.** Bir klasörde Chloros tarafından tanınan ancak yüklenemeyen dosyalar varsa, bir uyarı ile bilgilendirilirsiniz — görüntüler ızgaradan sessizce kaybolmaz.
{% endhint %}

***

## LATTICE Yakalama Klasörlerini İçe Aktarma

LATTICE yakalamaları, **dışa aktarma düzeyi başına bir alt klasör** şeklinde kaydedilir — örneğin `raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/` — kök dizinde eşleşen `.daq` aşağı akış dosyası ile birlikte:

```
output/
├── raw/           capture_<timestamp>_SN<serial>_raw.tif
├── debayered/     capture_<timestamp>_SN<serial>_debayered.tif
├── preview/       capture_<timestamp>_SN<serial>_display.tif
└── *.daq          the downwelling reading matched to the capture
```

**&quot;Klasör Ekle&quot; seçeneğini yakalama klasörünün kök dizinine yönlendirin** (yukarıdaki `output/`). Seçilen klasörün içinde görüntü bulunmuyor ancak alt klasörleri varsa, Chloros bu alt klasörlere otomatik olarak iner — aynı seviyedeki alt klasörler ve kök klasör olan `.daq` tek seferde alınır.**Yakalamaların içe aktarılma şekli:*** Her yakalama, yakalama bazında gruplandırılmış **tek bir görüntü** olarak içe aktarılır (her seviye için ayrı bir giriş değildir). Aynı yakalamanın diğer seviyeleri, o tek görüntünün görüntüleme modları olarak görünür.
* **İşleme her zaman ham kareden başlar.** Diğer seviyeler görüntülenebilir, ancak işleme sürecine yalnızca `raw` gönderilir — halihazırda işlenmiş bir ürünü yeniden işlemek, düzeltmelerin iki kez uygulanmasına neden olur; bu nedenle Chloros reddedilir. Yeniden içe aktarılan bir dışa aktarım, hiçbir zaman bir çekimin ham verisi yuvasını kaplayamaz.
* Ham veriler **olmadan** kaydedilen bir çekim klasörü normal şekilde görüntülenir, ancak işleme bu klasörü atlar ve bunu günlüğe kaydeder. (CLI bayrağı `--input-level`, bu durum için bir giriş noktasını zorlayabilir — bkz. [CLI Referansı](../reference/cli-reference.md#what-a-captures-folder-looks-like).)**LATTICE hub oturumları** da aynı şekilde içe aktarılır: hub&#x27;dan kopyalanan oturum klasörüne (bu klasörde `raw/` ve `previews/` bulunur) ve varsa herhangi bir DAQ-M `.csv` aşağı akış günlüğüne &quot;Klasör Ekle&quot; seçeneğini işaretleyin. Kameranın veya DAQ&#x27;nın kalibrasyonu henüz bilgisayarınızda önbelleğe alınmamışsa, Chloros, içe aktarma sırasında seri numarasına göre bunu otomatik olarak alır (bir kez internet bağlantısı gerektirir).***

## Dosya Tarayıcı Tablosunu Anlama

Görüntüler içe aktarıldıktan sonra, aşağıdaki sütunları içeren bir tabloda görünür:

### Dosya Adı

* Kameradan gelen orijinal dosya adı
* Kameranın adlandırma kuralını korur (örn., IMG\_0001.RAW veya capture\_20260816\_101500\_SN213800234\_raw.tif)

### Zaman Damgası

* Görüntünün çekildiği tarih ve saat
* Görüntünün EXIF meta verilerinden alınır
* Işık sensörü eşleştirme, PPK senkronizasyonu ve kalibrasyon hedefi planlaması için kullanılır

### Kamera Modeli

* Otomatik olarak algılanan kamera ve filtre yapılandırması
* Survey3 örnekleri: Survey3W\_RGN, Survey3N\_OCN, Survey3W\_RGB
* LATTICE örnekleri: LATT-M3M-L41-F550, LATT-M3C-L87-FRGN
* Doğru işleme profillerini uygulamak için kullanılır

### Hedef Sütunu (Onay Kutusu)

* Kalibrasyon hedefleri içeren görüntüler için bu kutuyu işaretleyin
* En az bir görüntü işaretlendiğinde, hedefler için **yalnızca işaretlenen görüntüler taranır*** Ayrıntılar için [Hedef Görüntüleri Seçme](choosing-target-images.md) bölümüne bakın

### Görüntü Meta Verilerini Görüntüleme

Tablonun sağ üst köşesindeki geçiş düğmesine tıkladığınızda, seçilen görüntünün meta verileri görüntü ızgarası alanında gösterilir.

<figure><img src="../.gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

***

## Projenizdeki Işık Sensörü Dosyaları

* `.daq` ve `.csv` dosyaları Dosya Tarayıcı listesinde görünür, ancak tıklanabilir görüntüler değildir — bu dosyalar, yansıma kalibrasyonu için aşağı doğru ışık şiddetini sağlar.
* İçe aktarılan her `.daq`/`.csv` dosyası, **Proje Ayarları → DAQ Işık Sensörü** bölümünde listelenir; burada her dosya için geçerli olan difüzör kapağı düzeltmesini inceleyebilirsiniz. Bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md).
* **Işık Sensörleri** sekmesinde yaptığınız kayıtlar, açık projeye otomatik olarak eklenir — manuel içe aktarma gerekmez.***

## Projenizdeki Dosyaları Yönetme

### Dosyaları Kaldırma

Projenizden istenmeyen görüntüleri kaldırmak için:

1. Dosya Tarayıcı tablosunda bir veya daha fazla görüntü seçin
2. **&quot;Seçilenleri Kaldır&quot;** <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line"> düğmesine tıklayın
3. Kaldırma işlemini onaylayın (dosyalar diskten silinmez, yalnızca projeden kaldırılır)

### Sıralama ve Filtreleme

* **Sütuna göre sıralama**: Görüntüleri sıralamak için herhangi bir sütun başlığına tıklayın
* **Zaman damgası sıralaması**: Kronolojik çekim dizilerini düzenlemek için kullanışlıdır
* **Kamera modeli filtresi**: Birden fazla kamera kullanıyorsanız, görüntüleri kamera türüne göre gruplandırın***

## Görüntü Önizlemesi

### Tam Görüntüyü Görüntüleme

Dosya Tarayıcısındaki herhangi bir görüntü küçük resmine tıklayarak ana önizleme alanında görüntüleyin:

1. Görüntü, ortadaki önizleme panelinde görünür
2. Görüntü ayrıntılarını incelemek için yakınlaştırma kontrollerini kullanın
3. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Hızlı Gezinme

* **Önceki Görüntü**: Sol oka tıklayın veya ← tuşuna basın
* **Sonraki Görüntü**: Sağ oka tıklayın veya → tuşuna basın
* **Yakınlaştırma/Uzaklaştırma**: Fare tekerleğini veya yakınlaştırma düğmelerini kullanın
* **Kaydırma**: Yakınlaştırıldığında görüntü üzerinde tıklayıp sürükleyin***

## Yinelenen Dosyaların İşlenmesi

Chloros, yinelenen dosyaları otomatik olarak algılar ve göz ardı eder:

* Dosya adları aynı olan dosyalar atlanır
* Yanlışlıkla iki kez işlenmesini önler
* Yinelenen dosyalar algılandığında uyarı mesajı görüntülenir

{% hint style="warning" %}
**Önemli**: İçe aktarmadan önce orijinal görüntü dosyalarınızı yeniden adlandırmayın veya değiştirmeyin. Chloros, doğru işleme için orijinal dosya adlarına ve meta verilere güvenir.
{% endhint %}

***

## Karışık Kamera Veri Kümeleri

Projeniz birden fazla MAPIR kameradan alınan görüntüler içeriyorsa:

1. Chloros, her kamera modelini (Survey3, LATTICE veya bunların karışımı) otomatik olarak algılar
2. Her kamera türü, kendisine uygun kalibrasyon profiliyle işlenir
3. Dosya Tarayıcı, Kamera Modeli sütununda kamera modelini gösterir
4. Her kamera, işlendiğinde kendine ait bir çıktı klasör ağacına sahip olur

**Örnek senaryolar**: Survey3W RGN + Survey3N OCN çift kamera kurulumu, veya bir RGB ana kamera ve birkaç dar bant modülünden oluşan bir LATTICE dizisi***

## En İyi Uygulamalar

### İçe Aktarmadan Önce Düzenleme

* Kalibrasyon hedef görüntülerini, çekim görüntüleriyle aynı klasörde tutun
* Her çekim oturumuna ait `.daq` / `.csv` ışık sensörü dosyalarını o oturumun görüntüleriyle birlikte saklayın
* Kameranızdan/SD kartınızdan/hub&#x27;ınızdan gelen orijinal klasör yapısını koruyun
* Farklı oturumlara ait veri kümelerini tek bir projede karıştırmayın

### Dosya Adlandırma

* Orijinal kamera dosya adlarını koruyun (IMG\_0001.RAW, capture\_..., vb.)
* İçe aktarmadan önce dosyaların adını değiştirmeyin
* Orijinal adlar önemli meta veriler içerir

### Kalibrasyon Hedef Görüntüleri

* Her oturum için daima 1-2 adet kalibrasyon hedef görüntüsü ekleyin (Survey3; LATTICE için bir DAQ kaydı bunun yerine kullanılabilir — bkz. [Hedef Görüntülerin Seçilmesi](choosing-target-images.md))
* Çekim oturumundan önce ve sonra hedefleri çekin
* Hedefleri, çekim alanıyla aynı aydınlatma koşullarına yerleştirin
* Hedef görüntülerini “Hedef” onay kutusunu kullanarak işaretleyin

***

## Sık Karşılaşılan Sorunlar ve Çözümleri

### İçe Aktarımdan Sonra Görüntüler Görünmüyor

**Olası nedenler:**

* Dosya biçimi desteklenmiyor (bu sayfanın üst kısmındaki desteklenen türler listesine bakın)
* Görüntüler, MAPIR dışındaki kameralardan alınmış (bkz. [Desteklenen Kameralar](../supported-cameras.md))
* Dosya bozukluğu veya SD karttan aktarımın eksik olması

**Çözüm**: Dosya biçimi ve kamera modeli uyumluluğunu doğrulayın ve yükleme başarısızlığı yaşanan dosyalar için dosya yükleme uyarısını kontrol edin

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

**Çözüm**: Çekim sırasında kamera saat ayarlarının doğru olup olmadığını kontrol edin

### Yeniden Açılan Projede Eksik Dosyalar Bildiriliyor

Proje en son açıldığından beri kaynak dosyalar taşınmış veya silinmişse, Chloros, boş bir ızgaraya açılmak yerine **hangi** dosyaların kaybolduğunu size bildirir. Dosyaları orijinal yollarına geri yükleyin veya eksik girişleri kaldırıp yeniden içe aktarın.***

## Sonraki Adımlar

Dosyalarınız içe aktarıldıktan sonra:

1. **Dosya listesini inceleyin** - Tüm görüntülerinin doğru şekilde yüklendiğinden emin olun
2. **Kamera modellerini kontrol edin** - Kameranın doğru şekilde algılandığını doğrulayın
3. **Hedef görüntüleri işaretleyin** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
4. **Ayarları düzenleyin** - [Proje Ayarları](adjusting-project-settings.md) bölümünden işleme seçeneklerini yapılandırın
5. **İşlemeyi başlatın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)

Proje yapılandırması hakkında ayrıntılı bilgi için bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md).
