# Proje Ayarları

Chloros&#x27;teki Proje Ayarları <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> kenar çubuğu, projeniz için görüntü işleme, kalibrasyon hedefi algılama, multispektral indeks hesaplamaları ve dışa aktarma seçeneklerinin tüm yönlerini yapılandırmanıza olanak tanır. Bu ayarlar projenizle birlikte kaydedilir ve birden fazla projede yeniden kullanılmak üzere şablon olarak kaydedilebilir.

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
* **Açıklama**: Algılanan bir bölgenin geçerli bir kalibrasyon hedef örneği olarak kabul edilmesi için gereken minimum alanı (piksel cinsinden) ayarlar. Daha küçük değerler daha küçük hedefleri algılar, ancak yanlış pozitifleri artırabilir. Daha büyük değerler, algılama için daha büyük ve daha net hedef bölgeler gerektirir.
* **Ne zaman ayarlanmalı**:
  * Küçük görüntü artefaktlarında yanlış algılamalar elde ediyorsanız artırın.
  * Kalibrasyon hedefleriniz görüntülerinizde küçük görünüyorsa ve algılanmıyorsa azaltın.

### Minimum Hedef Kümeleme (0-100)

* **Tür**: Sayı
* **Aralık**: 0 ila 100
* **Varsayılan**: 60
* **Açıklama**: Kalibrasyon hedeflerini algılarken benzer renkli bölgeleri gruplandırmak için kümeleme eşiğini kontrol eder. Daha yüksek değerler, daha benzer renklerin bir araya gruplandırılmasını gerektirir, bu da daha konservatif hedef algılama ile sonuçlanır. Daha düşük değerler, bir hedef grup içinde daha fazla renk varyasyonuna izin verir.
* **Ne zaman ayarlanmalı**:
  * Kalibrasyon hedefleri birden fazla algılamaya bölünüyorsa artırın.
  * Renk varyasyonu olan kalibrasyon hedefleri tam olarak algılanmıyorsa azaltın.

***

## İşleme

Bu ayarlar, Chloros&#x27;in görüntülerinizi nasıl işlediğini ve kalibre ettiğini kontrol eder.

### Vinyet düzeltme

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerin kenarlarında lensin karartmasını telafi etmek için vinyet düzeltmesi uygular. Vinyet, lens özellikleri nedeniyle görüntünün köşeleri ve kenarlarının merkezinden daha koyu görünmesi gibi yaygın bir optik olgudur.
* **Ne zaman devre dışı bırakılmalı**: Yalnızca kamera/lens kombinasyonunuz vinyet düzeltmesini zaten uygulamışsa veya post-processing sırasında vinyeti manuel olarak düzeltmek istiyorsanız devre dışı bırakın.

### Yansıma kalibrasyonu / beyaz dengesi

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerinizde algılanan kalibrasyon hedeflerini kullanarak otomatik yansıma kalibrasyonunu etkinleştirir. Bu, veri kümenizin yansıma değerlerini normalleştirir ve aydınlatma koşullarından bağımsız olarak tutarlı ölçümler sağlar.
* **Ne zaman devre dışı bırakılmalı**: Yalnızca ham, kalibre edilmemiş görüntüleri işlemek istiyorsanız veya farklı bir kalibrasyon iş akışı kullanıyorsanız devre dışı bırakın.

### Debayer yöntemi

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * Yüksek Kalite (Daha Hızlı) - Şu anda mevcut tek seçenek
* **Varsayılan**: Yüksek Kalite (Daha Hızlı)
* **Açıklama**: Ham Bayer desen sensör verilerini tam renkli görüntülere dönüştürmek için kullanılan demosaicing algoritmasını seçer. &quot;Yüksek Kalite (Daha Hızlı)&quot; yöntemi, işleme hızı ve görüntü kalitesi arasında optimum denge sağlar.
* **Not**: Chloros&#x27;in gelecek sürümlerinde ek debayer yöntemleri eklenebilir.

### Minimum yeniden kalibrasyon aralığı

* **Tür**: Sayı
* **Aralık**: 0 ila 3.600 saniye
* **Varsayılan**: 0 saniye
* **Açıklama**: Kalibrasyon hedeflerinin kullanımı arasındaki minimum zaman aralığını (saniye cinsinden) ayarlar. 0 olarak ayarlandığında, Chloros algılanan her kalibrasyon hedefini kullanır. Daha yüksek bir değere ayarlandığında, Chloros yalnızca en az bu kadar saniye aralıklarla ayrılmış kalibrasyon hedeflerini kullanır, böylece sık kalibrasyon hedefi yakalamaları olan veri kümeleri için işlem süresi azalır.
* **Ne zaman ayarlanmalı**:
  * Işık koşulları değiştiğinde maksimum kalibrasyon doğruluğu için 0 olarak ayarlayın
  * Işık koşulları sabit olduğunda ve sık kalibrasyon hedefi görüntüleriniz olduğunda daha hızlı işlem için artırın (örneğin, 60-300 saniyeye)

### Işık sensörü saat dilimi farkı

* **Tür**: Sayı
* **Aralık**: -12 ila +12 saat
* **Varsayılan**: 0 saat
* **Açıklama**: Işık sensörü veri zaman damgaları için saat dilimi farkını (UTC&#x27;den saat cinsinden) belirtir. Bu, PPK (Post-Processed Kinematic) veri dosyalarını işlerken, görüntü yakalamaları ve GPS verileri arasında doğru zaman senkronizasyonunu sağlamak için kullanılır.
* **Ne zaman ayarlanmalı**: PPK verileriniz UTC yerine yerel saati kullanıyorsa, bunu yerel saat dilimi farkına göre ayarlayın. Örneğin:
  * Pasifik Saati: -8 veya -7 (DST&#x27;ye bağlı olarak)
  * Doğu Saati: -5 veya -4 (DST&#x27;ye bağlı olarak)
  * Orta Avrupa Saati: +1 veya +2 (DST&#x27;ye bağlı olarak)

### PPK düzeltmelerini uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Devre dışı (işaretlenmemiş)
* **Açıklama**: GPS (GNSS) içeren MAPIR DAQ kayıt cihazlarından Post-Processed Kinematic (PPK) düzeltmelerinin kullanımını etkinleştirir. Etkinleştirildiğinde, Chloros, proje dizininizde pozlama pimi verilerini içeren tüm .daq günlük dosyalarını kullanır ve görüntülere hassas coğrafi konum düzeltmeleri uygular.
* **Gereklilik**: Pozlama pimi girişleri içeren .daq günlük dosyası proje dizininizde bulunmalıdır
* **Ne zaman etkinleştirilir**: .daq günlük dosyanızda pozlama geri bildirimi girişleri varsa, PPK düzeltmesini her zaman etkinleştirmeniz önerilir.

### Pozlama Pimi 1

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; etkinleştirildiğinde VE Pim 1 için pozlama verileri mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: PPK zaman senkronizasyonu için Pozlama Pimi 1&#x27;e belirli bir kamera atar. Pozlama pimi, kamera deklanşörünün tetiklendiği tam zamanı kaydeder; bu, doğru PPK coğrafi konum belirleme için çok önemlidir.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pim: Kamerayı otomatik olarak seçer
  * Tek kamera + iki pim: Pim 1 otomatik olarak kameraya atanır
  * Birden fazla kamera: Manuel seçim gerekir

### Pozlama Pimi 2

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; etkinleştirildiğinde VE Pim 2 için pozlama verileri mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: Çift kamera kurulumu kullanılırken PPK zaman senkronizasyonu için Pozlama Pimi 2&#x27;ye belirli bir kamera atar.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pim: Pim 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Tek kamera + iki pim: Pim 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Birden fazla kamera: Manuel seçim gereklidir
* **Not**: Aynı kamera aynı anda hem Pim 1 hem de Pim 2&#x27;ye atanamaz.

***

## Dizin

Bu ayarlar, analiz ve görselleştirme için çok spektral dizinleri yapılandırmanıza olanak tanır.

### Dizin ekle

* **Tür**: Özel dizin yapılandırma paneli
* **Açıklama**: Görüntü işleme sırasında hesaplanacak multispektral bitki örtüsü dizinlerini (NDVI, NDRE, EVI vb.) seçip yapılandırabileceğiniz etkileşimli bir panel açar. Her biri kendi görselleştirme ayarlarına sahip birden fazla endeks ekleyebilirsiniz.
* **Kullanılabilir endeksler**: Sistem, aşağıdakiler dahil 30&#x27;dan fazla önceden tanımlanmış multispektral endeks içerir:
* NDVI (Normalleştirilmiş Fark Bitki Örtüsü Endeksi)
  * NDRE (Normalleştirilmiş Fark RedEdge)
  * EVI (Geliştirilmiş Bitki Örtüsü Endeksi)
  * GNDVI, SAVI, OSAVI, MSAVI2
  * Ve daha fazlası (tam liste için [Çok Spektral Endeks Formülleri](multispectral-index-formulas.md) bölümüne bakın)
* **Özellikler**:
  * Önceden tanımlanmış indeks formüllerinden seçim yapın
  * Görselleştirme renk gradyanlarını yapılandırın (LUT - Arama Tabloları)
  * Analiz için eşik değerleri ayarlayın
  * Özel indeks formülleri oluşturun

### Özel Formüller (Chloros+ Özelliği)

* **Tür**: Özel formül tanımlarının dizisi
* **Açıklama**: Bant matematiğini kullanarak özel multispektral indeks formülleri oluşturmanıza ve kaydetmenize olanak tanır. Özel formüller, proje ayarlarınızla birlikte kaydedilir ve yerleşik indeksler gibi kullanılabilir.
* **Nasıl oluşturulur**:
  1. İndeks yapılandırma panelinde, özel formül seçeneğini bulun
  2. Bant tanımlayıcılarını kullanarak formülünüzü tanımlayın (ör. NIR, Red, Green, Blue)
  3. Formülü açıklayıcı bir adla kaydedin
* **Formül sözdizimi**: Aşağıdakiler dahil olmak üzere standart matematiksel işlemler desteklenir:
  * Aritmetik: `+`, `-`, `*`, `/`
  * İşlem sırası için parantezler
  * Bant referansları: NIR, Red, Green, Blue, RedEdge, Cyan, Orange, NIR1, NIR2

***

## Dışa aktarma

Bu ayarlar, dışa aktarılan işlenmiş görüntülerin formatını ve kalitesini kontrol eder.

### Kalibre edilmiş görüntü formatı

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * **TIFF (16 bit)** - Sıkıştırılmamış 16 bit TIFF formatı
  * **TIFF (32 bit, Yüzde)** - Yüzde olarak yansıma değerlerine sahip 32 bit kayan noktalı TIFF
  * **PNG (8 bit)** - Sıkıştırılmış 8 bit PNG formatı
  * **JPG (8 bit)** - Sıkıştırılmış 8 bit JPEG formatı
* **Varsayılan**: TIFF (16 bit)
* **Açıklama**: İşlenmiş ve kalibre edilmiş görüntüleri kaydetmek için dosya formatını seçer.
* **Format önerileri**:
  * **TIFF (16 bit)**: Bilimsel analiz ve profesyonel iş akışları için önerilir. Sıkıştırma artefaktları olmadan maksimum veri kalitesini korur. Çok spektral analiz ve GIS yazılımında ileri işleme için en iyisidir.
  * **TIFF (32 bit, Yüzde)**: Yansıma değerlerinin yüzde olarak (0-100%) verilmesi gereken iş akışları için en uygun seçenektir. Radyometrik ölçümler için maksimum hassasiyet sunar.
  * **PNG (8 bit)**: Web görüntüleme ve genel görselleştirme için uygundur. Kayıpsız sıkıştırma ile daha küçük dosya boyutları, ancak azaltılmış dinamik aralık.
  * **JPG (8 bit)**: En küçük dosya boyutları, yalnızca önizleme ve web görüntüleme için en uygun olanıdır. Bilimsel analiz için uygun olmayan kayıplı sıkıştırma kullanır.

***

## Proje Şablonunu Kaydet

Bu özellik, mevcut proje ayarlarınızı yeniden kullanılabilir bir şablon olarak kaydetmenizi sağlar.

* **Tür**: Metin girişi + Kaydet düğmesi
* **Açıklama**: Ayar şablonunuz için açıklayıcı bir ad girin ve kaydet simgesine tıklayın. Şablon, gelecekteki projelerde kolayca yeniden kullanılabilmesi için mevcut tüm proje ayarlarınızı (hedef algılama, işleme seçenekleri, indeksler ve dışa aktarma biçimi) depolar.
* **Kullanım örnekleri**:
  * Farklı kamera sistemleri için şablonlar oluşturun (RGB, multispektral, NIR)
  * Belirli mahsul türleri veya analiz iş akışları için standart yapılandırmaları kaydedin
  * Ekip genelinde tutarlı ayarları paylaşın
* **Kullanım şekli**:
  1. İstediğiniz tüm proje ayarlarını yapılandırın
  2. Şablon adı girin (ör. &quot;RedEdge Survey3 NDVI Standart&quot;)
  3. Kaydet simgesine tıklayın
  4. Artık yeni projeler oluştururken şablon yüklenebilir

***

## Proje Klasörünü Kaydet

Bu ayar, yeni projelerin varsayılan olarak kaydedileceği yeri belirler.

* **Tür**: Dizin yolu gösterimi + Düzenle düğmesi
* **Varsayılan**: `C:\Users\[Username]\Chloros Projects`
* **Açıklama**: Yeni Chloros projelerinin oluşturulduğu geçerli varsayılan dizini gösterir. Farklı bir dizin seçmek için düzenle simgesine tıklayın.
* **Ne zaman değiştirilir**:
  * Ekip işbirliği için bir ağ sürücüsüne ayarlayın.
  * Büyük veri kümeleri için daha fazla depolama alanına sahip bir sürücüye değiştirin.
  * Projeleri yıl, müşteri veya proje türüne göre farklı klasörlerde düzenleyin.
* **Not**: Bu ayarın değiştirilmesi yalnızca YENİ projeleri etkiler. Mevcut projeler orijinal konumlarında kalır.

***

## Ayarların Kalıcılığı

Tüm proje ayarları, proje dosyanızla (`.mapir` proje formatı) otomatik olarak kaydedilir. Bir projeyi yeniden açtığınızda, tüm ayarlar bıraktığınız şekilde geri yüklenir.

### Ayar Hiyerarşisi

Ayarlar aşağıdaki sırayla uygulanır:

1. **Sistem varsayılanları** - Chloros tarafından tanımlanan yerleşik varsayılanlar
2. **Şablon ayarları** - Bir proje oluştururken bir şablon yüklediğinizde
3. **Kaydedilmiş proje ayarları** - Proje dosyasıyla birlikte kaydedilen ayarlar
4. **Manuel ayarlamalar** - Mevcut oturum sırasında yaptığınız tüm değişiklikler

### Ayarlar ve Görüntü İşleme

Çoğu ayar değişikliği (özellikle İşleme ve Dışa Aktarma kategorilerinde) yeni ayarları yansıtmak için görüntülerin yeniden işlenmesini tetikler. Ancak, bazı ayarlar &quot;sadece dışa aktarma&quot; içindir ve hemen yeniden işleme gerektirmez:

* Proje Şablonunu Kaydet
* Çalışma Dizini
* Kalibre edilmiş görüntü formatı (dışa aktarırken geçerlidir)

***

## En İyi Uygulamalar

1. **Varsayılanlarla başlayın**: Varsayılan ayarlar, çoğu MAPIR kamera sistemi ve tipik iş akışları için iyi sonuç verir.
2. **Şablonlar oluşturun**: Belirli bir iş akışı veya kamera için ayarları optimize ettikten sonra, projeler arasında tutarlılık sağlamak için bunları şablon olarak kaydedin.
3. **Tam işleme öncesinde test edin**: Yeni ayarları denerken, tüm veri kümenizi işlemeden önce küçük bir görüntü alt kümesinde test edin.
4. **Ayarlarınızı belgelendirin**: Kamera sistemini, işleme türünü ve kullanım amacını belirten açıklayıcı şablon adları kullanın (ör. &quot;Survey3\_RGB\_NDVI\_Agriculture&quot;).
5. **Dışa aktarma formatı seçimi**: Son kullanımınıza göre dışa aktarma formatınızı seçin:
   * Bilimsel analiz → TIFF (16 bit veya 32 bit)
   * GIS işleme → TIFF (16 bit)
   * Hızlı görselleştirme → PNG (8 bit)
   * Web paylaşımı → JPG (8 bit)

***

Chloros&#x27;teki multispektral indeksler hakkında daha fazla bilgi için, [Multispektral İndeks Formülleri](multispectral-index-formulas.md) sayfasına bakın.
