# GUI: Gezinme

Chloros uygulamasını ilk kez başlattığınızda, işleme arka ucu çalışmaya başlar. Arka uç hazır hale geldiğinde, sol üst köşedeki ana menü simgesi görünür hale gelir <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> ve sol kenar çubuğundaki “Kameralar” ile “Işık Sensörleri” sekmeleri etkinleşir (o ana kadar bu sekmeler gri renkte görünür).

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Üst başlıkta soldan sağa doğru şunlar yer alır:

### <img src=".gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line"> Ana Menüsü

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Ana menüden şunları yapabilirsiniz:

* **Yeni Proje**— yeni bir proje oluşturun. Kaydedilmiş proje şablonlarınız varsa,**Şablon Seç** açılır menüsü görünür ve yeni proje bir şablonun ayarlarından başlar.
* **Projeyi Aç**— mevcut bir projeyi açın. Listede, dosya gezgininizde proje klasörünü açan bir**Proje Klasörünü Aç** düğmesi bulunur.
* **Projeyi Çoğalt** — şu anda açık olan projeyi yeni bir adla kopyalar (örneğin &quot;MyProject (2)&quot; gibi serbest bir ad önerilir) ve kopyayı açar. _(proje açıldıktan sonra görünür)_
* **Dosya Ekle** — mevcut projeye tek tek görüntü dosyaları ekler _(proje açıldıktan sonra görünür)_
* **Klasör Ekle** — mevcut projeye bir veya daha fazla görüntü klasörü ekler _(proje açıldıktan sonra görünür)_
* **İşlemeyi Başlat / İşlemeyi Durdur** — görüntü işleme akışını başlatır veya durdurur _(dosyalar eklendikten sonra etkinleştirilir)_
* **Kameraya Bağlan** — bir LATTICE kamerası veya dizisine bağlanmak için [Kameralar sekmesine](lattice/) atlar. Açık bir proje olmadan da çalışır.
* **Işık Sensörüne Bağlan** — bir DAQ ışık sensörüne bağlanmak için [Işık Sensörleri sekmesine](daq/) atlar. Açık bir proje olmadan da çalışır.

{% hint style="info" %}
**Yalnızca Windows**: Chloros Masaüstü GUI&#x27;si, Windows&#x27;te kullanılabilir. Linux kullanıcıları, [CLI](CLI.md) ve [Python SDK](api-python-sdk.md) belgelerine bakmalıdır.
{% endhint %}

###<img src=".gitbook/assets/image (2) (1) (1).png" alt="" data-size="line">

Oynat/Başlat Düğmesi

Etkinleştirildiğinde, işlemeyi başlat düğmesi görüntü işleme boru hattını başlatır.

###<img src=".gitbook/assets/image (4).png" alt="" data-size="line">

İlerleme Çubuğu<img src=".gitbook/assets/image (5).png" alt="" data-size="line">

Tüm dosyaları sırayla işleyen ücretsiz Chloros modunda, ilerleme çubuğu 2 aşamayı gösterir: Hedef Algılama ve İşleme.

Tüm dosyaları eşzamanlı olarak işleyen ücretli Chloros+ lisanslı modunda, ilerleme çubuğu 4 aşamayı gösterir: Algılama, Analiz, Kalibrasyon, Dışa Aktarma. Fare imlecinizi Chloros+ ilerleme çubuğunun üzerine getirdiğinizde, süreci takip edebilmeniz için genişletilmiş 4 aşamalı ilerleme çubuğu paneli açılır. Üstteki ilerleme çubuğuna tıkladığınızda açılır panel dondurulur, tekrar tıkladığınızda ise dondurma kaldırılır.

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

## Yan Menü

Sol kenar çubuğu menüsü, etkileşim kurabileceğiniz çeşitli simgeler içerir; bunlar yukarıdan aşağıya doğru şu sırayla yer alır:

#### <img src=".gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> [Proje Ayarları](project-settings/project-settings.md)

Proje Ayarları sekmesi, proje genel ayarlarını ve proje işleme ayarlarını düzenlemenizi sağlar. Dosyalarınızı işlemeye başlamadan önce bu ayarları yapın.

#### <img src=".gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> Dosya Tarayıcısı

Projeye dosya/klasör ekleyin ve projeden dosya kaldırın. Yinelenen dosyalar göz ardı edilir. Hedef görüntü için hedef sütunundaki kutuyu işaretleyin; işleme, hedefler için yalnızca işaretli görüntüleri dikkate alır ve bu da işleme sürenizi büyük ölçüde hızlandırır. Görüntü/Meta Veriler düğmesini kullanarak, seçilen görüntünün küçük resim ızgarasını görüntülemek ile ayrıntılı meta veri tablosunu görüntülemek arasında geçiş yapabilirsiniz.

#### <img src=".gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> [Görüntü Görüntüleyici](image-viewer-gui/opening-an-image-full-screen.md)

Ana görüntü görüntüleyicide bir görüntüye tıklandığında, görüntü Görüntü Görüntüleyicisi sekmesinde tam ekran olarak açılır.

#### <img src=".gitbook/assets/image (3) (1).png" alt="" data-size="line"> [Harita Görüntüleyicisi](image-viewer-gui/map-markers.md)

Görüntülerinizi GPS koordinatlarına göre etkileşimli bir 2D harita üzerinde görüntüleyin. Google Maps ve ESRI kiremit sağlayıcılarını destekler; konumunuz için en uygun hizmeti otomatik olarak seçer. İşaretçilerin üzerine fareyi getirdiğinizde görüntü küçük resim önizlemelerini görebilirsiniz.

#### <img src=".gitbook/assets/image (17).png" alt="" data-size="line"> [Kameralar](lattice/)

LATTICE kameralarını canlı olarak bağlayın ve kontrol edin — tek tek veya senkronize çoklu kamera dizileri olarak. Bu sekme, üst üste bindirmeler ve histogramlar içeren canlı önizleme döşemelerini, kamera başına ve dizi başına ayarları ile “Tümünü Yakala” seçeneğinin hangi kameraları ve dışa aktarma türlerini seçeceğini belirleyen Yakalama Ayarlarını gösterir. Arka uç hazır olduğunda kullanılabilir; tam kılavuz için [LATTICE bölümüne](lattice/) bakın.

#### <img src=".gitbook/assets/image (23).png" alt="" data-size="line"> [Işık Sensörleri](daq/)

DAQ ışık sensörlerini — DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E (Ethernet) — bağlayın ve W/m²/nm cinsinden kalibre edilmiş canlı spektrum grafiklerini görüntüleyin. Buradan, açık projeye `.daq` dosyalarını kaydedebilir, sensörleri yeniden adlandırabilir, kapak düzeltme profillerini seçebilir ve DAQ-E donanım yazılımını güncelleyebilirsiniz. Arka uç hazır olduğunda kullanılabilir; tam adım adım kılavuz için [DAQ bölümüne](daq/) bakın.

#### <img src=".gitbook/assets/icon_log.JPG" alt="" data-size="line"> Hata Giderme Günlüğü

Sorun yaşandığında hata giderme çıktılarını içeren günlüğü inceleyin. Günlüğü kopyalayın/indirin ve yardım almak için [MAPIR Destek](https://www.mapir.camera/community/contact) ekibine gönderin.

#### <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> [Kullanıcı Girişi](chloros+-login.md)

Kullanıcı girişi kenar çubuğu, Chloros+ hesabınıza giriş yapmanızı ve gelişmiş özelliklerin kilidini açmanızı sağlar. Ayrıca, mevcut uygulama sürümünü görüntüleyebilir ve Chloros GUI ile CLI&#x27;te görüntülenen metnin dilini ayarlayabilirsiniz.
