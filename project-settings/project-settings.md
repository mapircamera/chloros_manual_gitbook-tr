# Proje Ayarları

Chloros&#x27;teki <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> yan çubuğu, projeniz için görüntü işleme, kalibrasyon hedefi algılama, multispektral indeks hesaplamaları ve dışa aktarma seçeneklerinin tüm yönlerini yapılandırmanıza olanak tanır. Bu ayarlar projenizle birlikte kaydedilir ve birden fazla projede yeniden kullanılmak üzere şablon olarak kaydedilebilir.

## Proje Ayarlarına Erişme

Proje Ayarlarına erişmek için:

1. Chloros&#x27;te bir proje açın
2. Sol kenar çubuğundaki **Proje Ayarları**  <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> sekmesine tıklayın
3. Ayarlar paneli, kategorilere göre düzenlenmiş tüm mevcut yapılandırma seçeneklerini görüntüler

***

## Hedef Algılama

Bu ayarlar, Chloros&#x27;in görüntülerinizdeki kalibrasyon hedeflerini nasıl algıladığını ve işlediğini kontrol eder.

### Minimum kalibrasyon örnek alanı (px)

* **Tür**: Sayı
* **Aralık**: 0 ila 10.000 piksel
* **Varsayılan**: 25 piksel
* **Açıklama**: Algılanan bir bölgenin geçerli bir kalibrasyon hedefi örneği olarak kabul edilmesi için gereken minimum alanı (piksel cinsinden) belirler. Daha küçük değerler daha küçük hedefleri algılar, ancak yanlış pozitifleri artırabilir. Daha büyük değerler, algılama için daha büyük ve daha net hedef bölgeleri gerektirir.
* **Ne zaman ayarlanmalı**:
  * Küçük görüntü kusurlarında yanlış algılamalar alıyorsanız artırın
  * Kalibrasyon hedefleriniz görüntülerinizde küçük görünüyorsa ve algılanmıyorsa azaltın

### Minimum Hedef Kümeleme (0-100)

* **Tür**: Sayı
* **Aralık**: 0 ila 100
* **Varsayılan**: 60
* **Açıklama**: Kalibrasyon hedeflerini algılarken benzer renkli bölgeleri gruplandırmak için kümeleme eşiğini kontrol eder. Daha yüksek değerler, daha benzer renklerin bir araya getirilmesini gerektirir ve bu da daha muhafazakar bir hedef algılama ile sonuçlanır. Daha düşük değerler, bir hedef grubu içinde daha fazla renk varyasyonuna izin verir.
* **Ne zaman ayarlanmalı**:
  * Kalibrasyon hedefleri birden fazla algılamaya bölünüyor ise artırın
  * Renk varyasyonu olan kalibrasyon hedefleri tam olarak algılanmıyorsa azaltın

***

## İşleme

Bu ayarlar, Chloros&#x27;in görüntülerinizi nasıl işleyip kalibre edeceğini kontrol eder.

### Vinyet düzeltme

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerin kenarlarında lens karartmasını telafi etmek için vinyet düzeltmesi uygular. Vinyet, lens özellikleri nedeniyle görüntünün köşelerinin ve kenarlarının merkezden daha koyu görünmesine neden olan yaygın bir optik olgudur.
* **Ne zaman devre dışı bırakılmalı**: Yalnızca kamera/lens kombinasyonunuz zaten vinyet düzeltmesi uyguladıysa veya post-işlemde vinyeti manuel olarak düzeltmek istiyorsanız devre dışı bırakın.

### Yansıma kalibrasyonu / beyaz dengesi

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerinizde algılanan kalibrasyon hedeflerini kullanarak otomatik yansıma kalibrasyonunu etkinleştirir. Bu, veri kümenizde yansıma değerlerini normalleştirir ve aydınlatma koşullarından bağımsız olarak tutarlı ölçümler sağlar.
* **Ne zaman devre dışı bırakılmalı**: Yalnızca kalibre edilmemiş ham görüntüleri işlemek istiyorsanız veya farklı bir kalibrasyon iş akışı kullanıyorsanız devre dışı bırakın.

### Debayer yöntemi

* **Tür**: Açılır menü
* **Seçenekler**:
  * Standart (Hızlı, Orta Kalite)
  * Doku Duyarlı (Yavaş, En Yüksek Kalite) \[Chloros+]
* **Varsayılan**: Standart (Hızlı, Orta Kalite)
* **Açıklama**: Ham Bayer desenli sensör verilerini tam renkli görüntülere dönüştürmek için kullanılan demosaicing algoritmasını seçer. &quot;Standart (Hızlı, Orta Kalite)&quot; yöntemi, işleme hızı ile görüntü kalitesi arasında optimum bir denge sağlar. &quot;Dokuya Duyarlı (Yavaş, En Yüksek Kalite)&quot; \[Chloros+] seçeneği, neredeyse tüm demosaicing gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş yüksek kaliteli, kenar duyarlı bir demosaicing algoritması kullanır. Dokuya Duyarlı modelin çalışması için GPU belleği (VRAM) gereklidir. Daha hızlı işleme için 4 GB&#x27;den fazla VRAM&#x27;iniz varsa bu seçeneği kullanmanızı öneririz.
* **Not**: Chloros&#x27;in gelecek sürümlerinde ek debayer yöntemleri eklenebilir.

### Minimum yeniden kalibrasyon aralığı

* **Tür**: Sayı
* **Aralık**: 0 ila 3.600 saniye
* **Varsayılan**: 0 saniye
* **Açıklama**: Kalibrasyon hedeflerinin kullanımı arasındaki minimum zaman aralığını (saniye cinsinden) ayarlar. 0 olarak ayarlandığında, Chloros algılanan her kalibrasyon hedefini kullanır. Daha yüksek bir değere ayarlandığında, Chloros yalnızca bu saniye kadar aralıklarla ayrılmış kalibrasyon hedeflerini kullanır, bu da sık kalibrasyon hedefi yakalamaları olan veri kümeleri için işleme süresini azaltır.
* **Ne zaman ayarlanmalı**:
  * Işık koşulları değişken olduğunda maksimum kalibrasyon doğruluğu için 0 olarak ayarlayın
  * Işık sabit olduğunda ve sık kalibrasyon hedefi görüntüleriniz olduğunda daha hızlı işleme için artırın (ör. 60-300 saniyeye)

### Işık sensörü saat dilimi farkı

* **Tür**: Sayı
* **Aralık**: -12 ila +12 saat
* **Varsayılan**: 0 saat
* **Açıklama**: Işık sensörü veri zaman damgaları için saat dilimi kaymasını (UTC&#x27;den saat cinsinden) belirtir. Bu, görüntü yakalamaları ile GPS verileri arasında doğru zaman senkronizasyonunu sağlamak için PPK (Post-Processed Kinematic) veri dosyalarını işlerken kullanılır.
* **Ne zaman ayarlanmalı**: PPK verileriniz UTC yerine yerel saati kullanıyorsa, bunu yerel saat dilimi kaymanıza ayarlayın. Örneğin:
  * Pasifik Saati: -8 veya -7 (DST&#x27;ye bağlı olarak)
  * Doğu Saati: -5 veya -4 (DST&#x27;ye bağlı olarak)
  * Orta Avrupa Saati: +1 veya +2 (DST&#x27;ye bağlı olarak)

### PPK düzeltmelerini uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Devre dışı (işaretli değil)
* **Açıklama**: GPS (GNSS) içeren MAPIR DAQ kayıt cihazlarından Post-Processed Kinematic (PPK) düzeltmelerinin kullanımını etkinleştirir. Etkinleştirildiğinde, Chloros, proje dizininizde pozlama pimi verileri içeren tüm .daq günlük dosyalarını kullanacak ve görüntülere hassas coğrafi konum düzeltmeleri uygulayacaktır.
* **Gereksinim**: Proje dizininizde pozlama pini girişleri içeren bir .daq günlük dosyası bulunmalıdır
* **Ne zaman etkinleştirilmeli**: .daq günlük dosyanızda pozlama geri bildirim girişleri varsa, PPK düzeltmesini her zaman etkinleştirmeniz önerilir.

### Pozlama Pini 1

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; etkinleştirildiğinde VE Pin 1 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera modeli adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: PPK zaman senkronizasyonu için Pozlama Pini 1&#x27;e belirli bir kamera atar. Pozlama pini, kamera deklanşörünün tetiklendiği tam zamanlamayı kaydeder; bu, doğru PPK coğrafi konum belirleme için çok önemlidir.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Kamerayı otomatik olarak seçer
  * Tek kamera + iki pin: Pin 1 otomatik olarak kameraya atanır
  * Birden fazla kamera: Manuel seçim gereklidir

### Pozlama Pini 2

* **Tür**: Açılır menüden seçim
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; seçeneği etkinleştirildiğinde VE Pin 2 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: Çift kamera kurulumu kullanılırken PPK zaman senkronizasyonu için Pozlama Pini 2&#x27;ye belirli bir kamera atar.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Tek kamera + iki pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Birden fazla kamera: Manuel seçim gereklidir
* **Not**: Aynı kamera aynı anda hem Pin 1&#x27;e hem de Pin 2&#x27;ye atanamaz.***

## Endeks

Bu ayarlar, analiz ve görselleştirme için multispektral endeksleri yapılandırmanıza olanak tanır.

### Endeks ekle

* **Tür**: Özel endeks yapılandırma paneli
* **Açıklama**: Görüntü işleme sırasında hesaplanacak multispektral bitki örtüsü indekslerini (NDVI, NDRE, EVI vb.) seçip yapılandırabileceğiniz etkileşimli bir panel açar. Her biri kendi görselleştirme ayarlarına sahip birden fazla indeks ekleyebilirsiniz.
* **Kullanılabilir indeksler**: Sistem, aşağıdakiler dahil 30&#x27;dan fazla önceden tanımlanmış multispektral indeks içerir:
  * NDVI (Normalleştirilmiş Fark Bitki Örtüsü İndeksi)
  * NDRE (Normalleştirilmiş Fark RedEdge)
  * EVI (Geliştirilmiş Bitki Örtüsü Endeksi)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Ve daha fazlası (tam liste için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) bölümüne bakın)
* **Özellikler**:
  * Önceden tanımlanmış indeks formüllerinden seçim yapın
  * Görselleştirme renk gradyanlarını yapılandırın (LUT - Arama Tabloları)
  * Analiz için eşik değerleri ayarlayın
  * Özel indeks formülleri oluşturun

### Özel Formüller (Chloros+ Özelliği)

* **Tür**: Özel formül tanımları dizisi
* **Açıklama**: Bant matematiğini kullanarak özel multispektral indeks formülleri oluşturmanıza ve kaydetmenize olanak tanır. Özel formüller proje ayarlarınızla birlikte kaydedilir ve yerleşik indeksler gibi kullanılabilir.
* **Nasıl oluşturulur**:
  1. Endeks yapılandırma panelinde, özel formül seçeneğini bulun
  2. Bant tanımlayıcılarını kullanarak formülünüzü tanımlayın (ör. NIR, Red, Green, Blue)
  3. Formülü açıklayıcı bir adla kaydedin
* **Formül sözdizimi**: Aşağıdakiler dahil olmak üzere standart matematiksel işlemler desteklenir:
  * Aritmetik: `+`, `-`, `*`, `/`
  * İşlem sırası için parantezler
  * Bant referansları: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Dışa Aktar

Bu ayarlar, işlenmiş görüntülerin dışa aktarılma biçimini ve kalitesini kontrol eder.

### Kalibre edilmiş görüntü formatı

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * **TIFF (16 bit)** - Sıkıştırılmamış 16 bit TIFF formatı
  * **TIFF (32 bit, Yüzde)** - Yansıtma değerleri yüzde olarak verilen 32 bit kayan noktalı TIFF
  * **PNG (8 bit)** - Sıkıştırılmış 8 bit PNG formatı
  * **JPG (8 bit)** - Sıkıştırılmış 8 bit JPEG formatı
* **Varsayılan**: TIFF (16 bit)
* **Açıklama**: İşlenmiş ve kalibre edilmiş görüntüleri kaydetmek için dosya formatını seçer.
* **Format önerileri**:
  * **TIFF (16 bit)**: Bilimsel analizler ve profesyonel iş akışları için önerilir. Sıkıştırma bozulmaları olmadan maksimum veri kalitesini korur. Multispektral analiz ve GIS yazılımında ileri işleme için en iyisidir.
  * **TIFF (32 bit, Yüzde)**: Yansıma değerlerinin yüzde olarak (0-100%) gerekli olduğu iş akışları için en iyisidir. Radyometrik ölçümler için maksimum hassasiyet sunar.
  * **PNG (8 bit)**: Web görüntüleme ve genel görselleştirme için uygundur. Kayıpsız sıkıştırma ile daha küçük dosya boyutları sunar, ancak dinamik aralık azalır.
  * **JPG (8 bit)**: En küçük dosya boyutları, yalnızca önizlemeler ve web görüntülemesi için en iyisidir. Bilimsel analizler için uygun olmayan kayıplı sıkıştırma kullanır.***

## Proje Şablonunu Kaydet

Bu özellik, mevcut proje ayarlarınızı yeniden kullanılabilir bir şablon olarak kaydetmenizi sağlar.

* **Tür**: Metin girişi + Kaydet düğmesi
* **Açıklama**: Ayar şablonunuz için açıklayıcı bir ad girin ve kaydet simgesine tıklayın. Şablon, gelecekteki projelerde kolayca yeniden kullanılabilmesi için mevcut tüm proje ayarlarınızı (hedef algılama, işleme seçenekleri, indeksler ve dışa aktarma biçimi) saklayacaktır.
* **Kullanım örnekleri**:
  * Farklı kamera sistemleri için şablonlar oluşturun (RGB, multispektral, NIR)
  * Belirli ürün türleri veya analiz iş akışları için standart yapılandırmaları kaydedin
  * Ekip içinde tutarlı ayarları paylaşın
* **Nasıl kullanılır**:
  1. İstediğiniz tüm proje ayarlarını yapılandırın
  2. Bir şablon adı girin (ör. &quot;RedEdge Survey3 NDVI Standart&quot;)
  3. Kaydet simgesine tıklayın
  4. Artık yeni projeler oluştururken şablon yüklenebilir

***

## Proje Klasörünü Kaydet

Bu ayar, yeni projelerin varsayılan olarak nereye kaydedileceğini belirler.

* **Tür**: Dizin yolu gösterimi + Düzenle düğmesi
* **Varsayılan (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Varsayılan (Linux)**: `~/.local/share/chloros/projects`
* **Açıklama**: Yeni Chloros projelerinin oluşturulduğu mevcut varsayılan dizini gösterir. Farklı bir dizin seçmek için düzenle simgesine tıklayın.
* **Ne zaman değiştirilmeli**:
  * Ekip işbirliği için bir ağ sürücüsüne ayarlayın
  * Büyük veri kümeleri için daha fazla depolama alanına sahip bir sürücüye değiştirin
  * Projeleri yıl, müşteri veya proje türüne göre farklı klasörlerde düzenleyin
* **Not**: Bu ayarın değiştirilmesi yalnızca YENİ projeleri etkiler. Mevcut projeler orijinal konumlarında kalır.***

## Ayarların Kalıcılığı

Tüm proje ayarları, proje dosyanızla (`.mapir` proje biçimi) otomatik olarak kaydedilir. Bir projeyi yeniden açtığınızda, tüm ayarlar tam olarak bıraktığınız şekilde geri yüklenir.

### Ayar Hiyerarşisi

Ayarlar aşağıdaki sırayla uygulanır:

1. **Sistem varsayılanları** - Chloros tarafından tanımlanan yerleşik varsayılanlar
2. **Şablon ayarları** - Proje oluştururken bir şablon yüklerseniz
3. **Kaydedilmiş proje ayarları** - Proje dosyasıyla birlikte kaydedilen ayarlar
4. **Manuel ayarlamalar** - Mevcut oturum sırasında yaptığınız tüm değişiklikler

### Ayarlar ve Görüntü İşleme

Çoğu ayar değişikliği (özellikle İşleme ve Dışa Aktarma kategorilerinde), yeni ayarları yansıtmak için görüntülerin yeniden işlenmesini tetikler. Ancak, bazı ayarlar &quot;sadece dışa aktarma&quot; amaçlıdır ve hemen yeniden işleme gerektirmez:

* Proje Şablonunu Kaydet
* Çalışma Dizini
* Kalibre edilmiş görüntü formatı (dışa aktarırken geçerlidir)

***

## En İyi Uygulamalar

1. **Varsayılanlarla başlayın**: Varsayılan ayarlar, çoğu MAPIR kamera sistemi ve tipik iş akışları için iyi sonuç verir.
2. **Şablonlar oluşturun**: Belirli bir iş akışı veya kamera için ayarları optimize ettikten sonra, projeler arasında tutarlılığı sağlamak için bunları bir şablon olarak kaydedin.
3. **Tam işleme öncesinde test edin**: Yeni ayarları denerken, tüm veri kümenizi işlemeden önce küçük bir görüntü alt kümesinde test edin.
4. **Ayarlarınızı belgelendirin**: Kamera sistemini, işleme türünü ve kullanım amacını belirten açıklayıcı şablon adları kullanın (ör. &quot;Survey3\_RGB\_NDVI\_Agriculture&quot;).
5. **Dışa aktarma formatı seçimi**: Son kullanım amacınıza göre dışa aktarma formatınızı seçin:
   * Bilimsel analiz → TIFF (16 bit veya 32 bit)
   * GIS işleme → TIFF (16 bit)
   * Hızlı görselleştirme → PNG (8 bit)
   * Web paylaşımı → JPG (8 bit)

***

Chloros&#x27;teki multispektral indeksler hakkında daha fazla bilgi için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) sayfasına bakın.
