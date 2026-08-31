# Proje Ayarları

Chloros’teki “Proje Ayarları” (<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

) kenar çubuğu, projeniz için görüntü işleme, kalibrasyon hedefi algılama, multispektral indeks hesaplamaları ve dışa aktarma seçeneklerinin tüm yönlerini yapılandırmanıza olanak tanır. Bu ayarlar projenizle birlikte kaydedilir ve birden fazla projede yeniden kullanılmak üzere şablon olarak kaydedilebilir.

## Proje Ayarlarına Erişme

Proje Ayarlarına erişmek için:

1. Chloros&#x27;te bir proje açın
2. Sol kenar çubuğundaki **Proje Ayarları**<img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

sekmesine tıklayın
3. Ayarlar panelinde, kategorilere göre düzenlenmiş tüm mevcut yapılandırma seçenekleri görüntülenecektir

<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->

{% hint style="info" %}
**Diğer ayarlara bağlı olan ayarlar gri renkte gösterilir.** Bir üst düzeydeki anahtarın ayarını değiştirmesi bir ayarın yapılmasını imkansız hale getirdiğinde (örneğin, *Yansıma kalibrasyonu / beyaz dengesi* seçeneğinin işaretinin kaldırılması *Yansıma değerini dışa aktar* seçeneğini kullanılamaz hale getirir), bağımlı kontrol devre dışı bırakılır ve araç ipucunda değiştirilmesi gereken anahtarın adı belirtilir.
{% endhint %}

***

## Ekran

### Görüntü Küçük Resim Çözünürlüğü

* **Tür**: Açılır menü seçimi
* **Seçenekler**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Varsayılan**: Varsayılan (512 px)
* **Açıklama**: Resim ızgarasındaki küçük resimlerin görüntüleneceği çözünürlük (en uzun kenar, piksel cinsinden). Daha yüksek değerler, yakınlaştırıldığında daha net görünür ancak yükleme süresi uzar ve daha fazla bellek kullanılır. Tam çözünürlük, orijinal resim boyutunu kullanır.
* **Not**: Yalnızca görüntüleme amaçlıdır — bu ayar, işleme veya dışa aktarılan dosyaları hiçbir şekilde etkilemez.***

## Hedef Algılama

Bu ayarlar, Chloros&#x27;in görüntülerinizdeki kalibrasyon hedeflerini nasıl algıladığını ve işlediğini kontrol eder. Her ikisi de yalnızca **Yansıma kalibrasyonu / beyaz dengesi** etkinleştirildiğinde aktiftir (aksi takdirde, hedef algılama tamamen atlandığından gri renkte görünürler).

### Minimum kalibrasyon örnekleme alanı (px)

* **Tür**: Sayı
* **Aralık**: 0 ila 10.000 piksel
* **Varsayılan**: 25 piksel
* **Açıklama**: Algılanan bir bölgenin geçerli bir kalibrasyon hedefi örneği olarak kabul edilmesi için gereken minimum alanı (piksel cinsinden) belirler. Daha küçük değerler, daha küçük hedefleri algılar ancak yanlış pozitif sonuçları artırabilir. Daha büyük değerler, algılama için daha büyük ve daha net hedef bölgeler gerektirir.
* **Ne zaman ayarlanmalı**:
  * Küçük görüntü kusurlarında yanlış algılamalar alıyorsanız değeri artırın
  * Kalibrasyon hedefleriniz görüntülerinizde küçük görünüyorsa ve algılanmıyorsa değeri azaltın

### Minimum Hedef Kümeleme (0-100)

* **Tür**: Sayı
* **Aralık**: 0 ila 100
* **Varsayılan**: 60
* **Açıklama**: Kalibrasyon hedeflerini algılarken benzer renkli bölgeleri gruplandırmak için kümeleme eşiğini kontrol eder. Daha yüksek değerler, daha fazla benzer rengin bir araya getirilmesini gerektirir; bu da daha ihtiyatlı bir hedef algılama ile sonuçlanır. Daha düşük değerler, bir hedef grubu içinde daha fazla renk çeşitliliğine izin verir.
* **Ne zaman ayarlanmalı**:
  * Kalibrasyon hedefleri birden fazla algılama olarak bölünüyor ise artırın
  * Renk varyasyonu olan kalibrasyon hedefleri tam olarak algılanmıyorsa azaltın

***

## İşleme

Bu ayarlar, Chloros&#x27;in görüntülerinizi nasıl işleyeceğini ve kalibre edeceğini kontrol eder.

### Vinyet düzeltme

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerin kenarlarında lensin neden olduğu kararmayı telafi etmek için vinyet düzeltmesi uygular. Vinyet, lens özellikleri nedeniyle görüntünün köşelerinin ve kenarlarının merkezden daha koyu görünmesine neden olan yaygın bir optik olgudur.
* **Yan etki**: Bu anahtar, bir işleme sırasında hangi *kalibre edilmemiş yedek ürünün* yazılacağını da belirler (aşağıya bakın).

### Yansıma kalibrasyonu / beyaz dengesi

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Yansıma kalibrasyonunu etkinleştirir — kameraya ve mevcut olanlara bağlı olarak, çerçeve içinde algılanan kalibrasyon hedeflerinden ve/veya DAQ ışık sensörünün aşağıya doğru ışık verisi kullanılarak gerçekleştirilir. Bu, veri kümenizde yansıma değerlerini normalleştirir ve aydınlatma koşullarından bağımsız olarak tutarlı ölçümler sağlar.
* **Devre dışı bırakıldığında**: Hedef algılama tamamen atlanır ve**hiçbir kamera tarafından yansıma ürünü üretilemez** — hem Survey3 hedef odaklı hem de LATTICE DAQ odaklı modellerde. Bağımlı ayarlar (*Yansıtma değerini dışa aktar*, *Minimum yeniden kalibrasyon aralığı* ve Hedef Algılama eşikleri) gri renkte görünür.

### Kalibre edilmemiş yedek ürünler: Sensör yanıtını dışa aktar / Vinyet düzeltilmiş olarak dışa aktar

* **Tür**: İki onay kutusu
* **Varsayılanlar**: Her ikisi de etkin (işaretli)
* **Açıklama**: Bir karenin yansıma kalibrasyonu yapılamadığında (kalibrasyon hedefi bulunamadı veya yansıma kalibrasyonu kapalıysa), bunun yerine *kalibre edilmemiş yedek ürün* olarak kaydedilir. **Her kamera modeli için, her çalıştırmada *Vignette düzeltme* anahtarıyla seçilen iki yedek üründen tam olarak biri mevcuttur**:
  * Vignette düzeltme **açık**→ `Vignette_Corrected_Images/` (**Vignette düzeltilmiş olarak dışa aktar** seçeneğiyle belirlenir)
  * Vinyet düzeltme **kapalı**→ `Sensor_Response_Images/` (**Vinyet düzeltilmiş olarak dışa aktar** seçeneği tarafından belirlenir)
* Kullanılmayan yedek ürün gri renkte gösterilir. Kullanılan ürünün işaretini kaldırmak, o dosyanın yazılmasını tamamen engeller.

### LATTICE dışa aktarma ürünleri

LATTICE çekimleri içeren projelerde, içe aktarılan her LATTICE karesi tek bir işleme aşamasında etkin **ve uygun**tüm ürünlere dağıtılır. Dağıtımı dört onay kutusu kontrol eder (hepsi varsayılan olarak**açık**):

| Ayar | Çıkış klasörü | Dışa aktardığı içerik |
| --- | --- | --- |
| **Debayered olarak dışa aktar** | `Debayered_Images/` | Doğrusal debayered görüntü. RGB ve multispektral kameralar için geçerlidir. |
| **Önizlemeyi dışa aktar** | `Preview_Images/` | Ekran önizlemesi. RGB = beyaz dengesi (varsa DAQ ışık kaynağı, yoksa gri dünya) + gama; multispektral = sahte renk genişletme. |
| **Işılma dışa aktarma** | `Radiance_Images/` | W/m²/sr/nm biriminde Float32 spektral ışılma. Yalnızca multispektral (M3C/M3M) — RGB ana dosyalara uygulanmaz. *Kalibre edilmiş görüntü formatı* ayarından bağımsız olarak her zaman 32 bit TIFF olarak yazılır. |
| **Yansıtma oranını dışa aktar**| `Reflectance_Calibrated_Images/` | Uint16 yansıtma oranı,**32768 = yansıtma oranı 1,0** olacak şekilde ölçeklendirilmiştir (XMP `Chloros:PixelScale` olarak damgalanır). Yalnızca multispektral; eşleşen bir `.daq` aşağı yönlü kayıt (veya QA&#x27;dan geçen çerçeve içi hedef) çerçeveyi kapladığında yazılır. |

* RGB ana kameralar, debayering işleminden geçirilmiş + önizleme verileri yayar; parlaklık/yansıtma değerleri, bunlar için geçerli olmadığı için atlanır.
* Debayering işleminden geçirilmiş/önizleme verilerinin bit derinliği, *Kalibre edilmiş görüntü formatı* ayarına göre belirlenir; parlaklık her zaman float32&#x27;dir.
* Survey3 işleme, bu dört anahtardan etkilenmez.

Aynı dört anahtar, başlıksız olarak `chloros-cli process --debayered / --preview / --radiance / --reflectance` ve SDK&#x27;in eşleşen parametreleri olarak da mevcuttur. Bunlar, artık mevcut olmayan eski `--radiometric-output` bayrağının yerini almıştır.

{% hint style="warning" %}
**Uygulanabilir tüm ürünleri devre dışı bırakmak, işlemin başarısız olmasına neden olur.**

1.2.0 sürümünden itibaren, ürün talep eden ancak hiçbir görüntü ürünü yazmayan bir işleme çalışması, sessiz bir başarı bildirimi yerine hata bildirir ve CLI sıfırdan farklı bir değerle sonlanır. Günlük dosyası, yazılmayan ürünü ve bunun nedenini belirtir. Kasıtlı olarak yalnızca meta veriler içeren bir işleme (hiçbir şey talep edilmeyen), yine de başarılı olarak kabul edilir.
{% endhint %}

### Yansıma kaynağı (proje ayarı, CLI/SDK aracılığıyla ayarlanır)

Proje ayrıca, LATTICE yansıma ürününün hangi **yansıma referansını** kullandığını da kaydeder. Ayarlar panelinde buna özel bir kontrol yoktur; değer, proje yapılandırmasında `Processing → "Target reflectance source"` olarak saklanır ve `chloros-cli process --reflectance-source {auto,target,daq}` ile veya SDK&#x27;in `reflectance_source` parametresiyle ayarlanır:

* **`auto`** (varsayılan): QA&#x27;dan geçen çerçeve içi kalibrasyon hedefi mutlak referans haline gelir; hedef bulunmadığında veya QA başarısız olduğunda DAQ aşağı doğru yayılma bölünmesine (ρ = πL/E) geri dönülür.
* **`target`**: katı hedef odaklı yansıma — DAQ ikamesi yoktur.
* **`daq`**: DAQ&#x27;ya dayalı yansıma; çerçeve içi hedefler referans olarak kullanılmaz.

Kaydedilen değer, büyük/küçük harfbüyük/küçük harf ayırt edilmeden eşleştirilir ve birkaç farklı yazım biçimi eşanlamlı olarak kabul edilir: `target`, `target_image`, `empirical` ve `empirical_line` hepsi **hedef**anlamına gelir; `daq`, `dls`, `light_sensor` ve `sensor` hepsi**daq**anlamına gelir. Bunların dışında kalan her şey — mevcut olmayan bir anahtar dahil —**auto** olarak çözümlenir.

Birim başına **ölçülen** hedef taramaları, hedef birimin seri numarası/QR kodu ile aranır; `<serial>.csv` şeklinde, üç yerde aranır: `--target-reflectance-dir` ile belirtilen dizin (`Processing → "Target reflectance dir"` olarak saklanır), projenin kendi `target_reflectance/`X klasörü ve `CHLOROS_TARGET_REFLECTANCE_DIR` ortam değişkenindeki yol. O birim için ölçülmüş tarama bulunmadığında, bunun yerine hedef model için yayınlanmış nominal eğri kullanılır.

### Debayer yöntemi

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * Standart (Hızlı, Orta Kalite)
  * Dokuya Duyarlı (Yavaş, En Yüksek Kalite) \[Chloros+]
* **Varsayılan**: Standart (Hızlı, Orta Kalite)
* **Açıklama**: Ham Bayer desenli sensör verilerini tam renkli görüntülere dönüştürmek için kullanılan demosaicing algoritmasını seçer. &quot;Standart (Hızlı, Orta Kalite)&quot; yöntemi, işleme hızı ile görüntü kalitesi arasında optimum bir denge sağlar. &quot;Doku Duyarlı (Yavaş, En Yüksek Kalite)&quot; \[Chloros+] seçeneği, neredeyse tüm demosaicing gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli kenar duyarlı bir demosaicing algoritması kullanır. Doku Duyarlı modelin çalışması için GPU belleği (VRAM) gerektirir. Daha hızlı işleme için 4 GB&#x27;tan fazla VRAM&#x27;iniz olduğunda bu yöntemi kullanmanızı öneririz.
* **Satır bir açılır menü olduğunda**: iki seçenekli açılır menü, yalnızca**her ikisi de**doğru olduğunda görünür — uygun bir Chloros+ aboneliğiyle oturum açmış olmanız**ve** proje LATTICE çekimleri içermiyorsa. Aksi takdirde satır, seçilecek hiçbir seçenek olmadan `Standard (Fast, Medium Quality)` şeklinde düz metin olarak görüntülenir.
* **LATTICE notu**: LATTICE ile eğitilmiş Texture Aware modeli yoktur ve iş akışı, depolanan değer ne olursa olsun LATTICE kareleri için standart demosaic işlemini zorlar. Zaten Texture Aware seçeneğinin seçili olduğu bir projeye bir LATTICE klasörü eklerseniz, Chloros, `project.json`&#x27;te eski bir değer bırakmak yerine ayarı tekrar Standard olarak yeniden yazar.

### Minimum yeniden kalibrasyon aralığı

* **Tür**: Sayı
* **Aralık**: 0 ila 3.600 saniye
* **Varsayılan**: 0 saniye
* **Açıklama**: Kalibrasyon hedeflerinin kullanılması arasındaki minimum zaman aralığını (saniye cinsinden) belirler. 0 olarak ayarlandığında, Chloros algılanan her kalibrasyon hedefini kullanır. Daha yüksek bir değere ayarlandığında, Chloros yalnızca aralarında en az bu kadar saniye olan kalibrasyon hedeflerini kullanır; bu da sık sık kalibrasyon hedefi yakalamalarının olduğu veri kümeleri için işleme süresini azaltır.
* **Ne zaman ayarlanmalı**:
  * Işık koşulları değişken olduğunda maksimum kalibrasyon doğruluğu için 0 olarak ayarlayın
  * Işık koşulları sabit olduğunda ve sık sık kalibrasyon hedefi görüntüleri elde ettiğinizde daha hızlı işleme için değeri artırın (örneğin, 60-300 saniyeye)

### Işık sensörü saat dilimi farkı

* **Tür**: Sayı
* **Aralık**: -12 ila +12 saat
* **Varsayılan**: 0 saat
* **Açıklama**: Işık sensörü verilerinin zaman damgaları için saat dilimi kaymasını (UTC&#x27;ye göre saat cinsinden) belirtir; bu ayar, ışık sensörü günlüklerini görüntü yakalama zamanlarıyla eşleştirirken kullanılır. Daha yeni `.daq` kayıtları kendi saat dilimi bilgilerini içerir, bu nedenle bu ayar esas olarak yerel saatle kaydedilmiş eski günlükler için gereklidir.

### PPK düzeltmelerini uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Devre dışı (işaretlenmemiş)
* **Açıklama**: GPS (GNSS) içeren MAPIR DAQ kayıt cihazlarından Post-Processed Kinematic (PPK) düzeltmelerinin kullanılmasını etkinleştirir. Etkinleştirildiğinde, Chloros, proje dizininizde pozlama pimi verileri içeren tüm .daq günlük dosyalarını kullanır ve görüntülere hassas coğrafi konum düzeltmeleri uygular.
* **Gereksinim**: Proje dizininizde pozlama pini girişleri içeren bir .daq günlük dosyası bulunmalıdır
* **Ne zaman etkinleştirilmeli**: .daq günlük dosyanızda pozlama geri bildirim girişleri varsa, PPK düzeltmesini her zaman etkinleştirmeniz önerilir.

### Pozlama Pini 1

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; seçeneği etkinleştirildiğinde VE Pin 1 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: PPK zaman senkronizasyonu için Pozlama Pini 1&#x27;e belirli bir kamera atar. Pozlama pini, kamera deklanşörünün tetiklendiği tam zamanlamayı kaydeder; bu, doğru PPK coğrafi konum belirleme için çok önemlidir.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Kamerayı otomatik olarak seçer
  * Tek kamera + iki pin: Pin 1, kameraya otomatik olarak atanır
  * Birden fazla kamera: Manuel seçim gereklidir

### Pozlama Pini 2

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; seçeneği etkinleştirildiğinde VE Pin 2 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pini yok sayılır
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: Çift kamera kurulumunda PPK zaman senkronizasyonu için Pozlama Pini 2&#x27;ye belirli bir kamera atar.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Tek kamera + iki pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Birden fazla kamera: Manuel seçim gereklidir
* **Not**: Aynı kamera, Pin 1 ve Pin 2&#x27;ye aynı anda atanamaz.***

## DAQ Işık Sensörü

Bu bölüm, Proje Ayarları&#x27;nda görünür ve projedeki tüm DAQ aşağı doğru ışık dosyalarını listeler — `.daq` kayıtları ve DAQ-M `.csv` aşağı yönlü ışık kayıtları. Işık Sensörleri sekmesinde yapılan kayıtlar, açık projeye otomatik olarak eklenir.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->

Her satırda dosya, sensör modeli ve o dosya için geçerli olan difüzör kapağı düzeltmesi gösterilir. Satırların üzerinde, proje genelinde geçerli tek bir kontrol bulunur:

### Kapak geçersiz kılma (tüm dosyalar)

* **Tür**: Açılır menü seçimi
* **Seçenekler**: `Auto` artı projede bulunan sensör türleri için geçerli olan kapak düzeltme profilleri
* **Varsayılan**: Otomatik
* **Şu şekilde kaydedilir**: `Processing → "DAQ cap id"` (varsayılan `auto`)
* **Açıklama**: `Auto`, her dosyanın kaydedilmiş kap değerini kullanır (hiçbir şey kaydedilmemişse Güneş ışığı kap değeri varsayılır — tüm MAPIR DAQ&#x27;lar güneş ışığı düzelticisiyle birlikte gönderilir). Belirli bir kap seçilmesi,**tüm** aşağı doğru yönlü dosyalar üzerinde öncelik kazanır: ham kayıtlar bu kapakla düzeltilir ve halihazırda bir kapak içeren kayıtlar yeniden referanslanır (kaydedilen düzeltme geri alınır ve seçilen düzeltme uygulanır).
* **Önemli**: Seçilen kapak, kayıt sırasında fiziksel olarak takılan kapakla eşleşmelidir. Ne sensör ne de yazılım fiziksel kapağı algılayamaz — uyumsuz bir kapak kimliği, spektrumları yanlış düzeltir.

Dosya başına açılır menüler yerine, kasıtlı olarak **tek** bir proje genelinde geçerli kontrol bulunur: bu ayar, projedeki her aşağı yönlü kaynağa uygulanır.***

## Dizi Hizalama

Bu bölüm, **yalnızca** projede en az bir görüntünün, LATTICE dizilerinin çekim sırasında eklediği modülden modüle hizalama dönüşümünü (XMP `Chloros:Alignment*` etiketleri) taşıması durumunda görünür. Bu bölüm, kaç görüntünün hizalama etiketleri taşıdığını, hangi kameranın referans olduğunu (`REF` rozeti) ve kamera başına görüntü sayılarını gösteren bir tabloyu gösterir.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Dizi hizalamasını uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Kaydedilme biçimi**: `Processing → "Array alignment"`
* **Açıklama**: İşlenmiş her ürünü (debayering / önizleme / parlaklık / yansıma / indeks), çekim sırasında damgalanan dönüşümü kullanarak dizinin ortak referans geometrisine uyarlar. Kapalı = sensör başına özgün geometride dışa aktar.

### Ortak örtüşme alanına kırp

* **Tür**: Onay kutusu (*Dizi hizalaması uygula* etkinken aktif olur)
* **Varsayılan**: Etkin (işaretli)
* **Şu şekilde kaydedilir**: `Processing → "Array alignment crop"`
* **Açıklama**: Hizalanmış dışa aktarımları, tüm kamera modüllerinin paylaştığı bölgeye kırpar; böylece her bant aynı kaplama alanına sahip olur. Kapalı seçeneği, tam sensör tuvalini korur (kaynağın dışını siyahla doldurur).

### Yeniden Örnekleme

* **Tür**: Açılır menü (yalnızca *Dizi hizalaması uygula* seçeneği açıkken etkin)
* **Seçenekler**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Varsayılan**: Bilineer
* **Şu şekilde kaydedilir**: `Processing → "Array alignment interpolation"`
* **Açıklama**: Hizalama çarpıtmasında kullanılan enterpolasyon. “En yakın” seçeneği, kesin radyometrik analiz için kaynak değerlerini tam olarak korur (pikseller arası karıştırma yoktur); Bilineer ise haritalama ve görsel kullanım için en uygun seçenektir.

Aynı üç seçenek, başlıksız olarak `chloros-cli process --array-alignment`, `--array-alignment-crop` ve `--array-alignment-interp {bilinear,nearest,cubic}` adlarıyla da mevcuttur.

***

## İndeks

Bu ayarlar, analiz ve görselleştirme için multispektral indeksleri yapılandırmanıza olanak tanır.

### İndeks ekle

* **Tür**: Özel indeks yapılandırma paneli
* **Açıklama**: Multispektral bitki örtüsü indekslerini seçip yapılandırabileceğiniz etkileşimli bir panel açar (NDVI, NDRE, EVI vb.) seçip yapılandırabileceğiniz etkileşimli bir panel açar. Her biri kendi görselleştirme ayarlarına sahip birden fazla indeks ekleyebilirsiniz.
* **Kullanılabilir indeksler**: Grafik kullanıcı arayüzündeki açılır menüde**27** adet önceden tanımlanmış multispektral indeks formülü bulunur (multispectral-index-formulas.md&#x27;teki [Multispektral İndeks Formülleri](multispectral-index-formulas.md) CLI/SDK ve `--indices` seçenekleri tarafından kabul edilen isimler de dahil olmak üzere tam liste için).
* **Özellikler**:
  * Önceden tanımlanmış indeks formüllerinden seçim yapın
  * Kameranızın filtre kanallarını formülün bant yuvalarına sürükleyin
  * Görselleştirme renk gradyanlarını yapılandırın (LUT - Arama Tabloları)
  * Eşik değerlerini ve kırpma modlarını ayarlayın
  * Özel indeks formülleri oluşturun
* **Not**: Tek bantlı LATTICE M3M mono kameralar için indeksler hesaplanmaz — tek bantta çok bantlı indeksler tanımlanmamıştır. Survey3 ve LATTICE M3C bundan etkilenmez.

<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->

Eklediğiniz her endeks, formülünü matematiksel olarak görüntüler ve her bant yuvası için renkli bir daire gösterir: kırmızı = Red, yeşil = Green, mavi = Blue, turuncu = Orange, camgöbeği = Cyan, mor = NIR, macenta = RE. Formülün üstündeki satırdan bir daireyi bir yuvaya sürükleyerek bağlayın; bağlı bir yuvayı temizlemek için üzerine çift tıklayın. Dizin, formülün kullandığı her yuvada bir kanal varsa hesaplanır.

### Özel Formüller (Chloros+ Özelliği)

* **Tür**: Özel formül tanımları dizisi
* **Kullanılabilirlik**: Uygun bir Chloros+ aboneliği ile oturum açılması gerekir.
* **Açıklama**: Bant matematiğini kullanarak özel multispektral indeks formülleri oluşturmanıza ve kaydetmenize olanak tanır. Özel formüller proje ayarlarınızla birlikte kaydedilir ve yerleşik indeksler gibi kullanılabilir.
* **Nasıl oluşturulur**:
  1. İndeks yapılandırma panelinde, özel formül hesaplayıcısını açın
  2. Formülü, bant adlarını değil **bant yuvası sembollerini** kullanarak yazın
  3. Formülü açıklayıcı bir adla kaydedin — formül, formül açılır menüsünün en altında görünür ve kameranızın kanal dairelerini, yerleşik ön ayarlarda olduğu gibi yuvalarına sürükleyebilirsiniz
* **Formül sözdizimi**:
  * Bant yuvaları: `x`, `y`, `z`, `a`, `b`, `c` — sürükleyerek gerçek kanallara eşleştireceğiniz altı konum
  * Operatörler: `+`, `-`, `*`, `/`, `^` ve `()` gruplama için
  * İşlevler: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Neden grup isimleri değil de semboller**: `(y-x)/(y+x)` şeklinde yazılmış bir formül herhangi bir kamerada çalışır, çünkü sürükle ve-bırak eşleme, `y`&#x27;in bir RGN filtresinin 850 nm&#x27;lik NIR&#x27;i mi yoksa 808 nm&#x27;lik NIR olup olmadığını belirler. Yerleşik ön ayarlar da aynı şekilde saklanır — 27 tanesinin tümünün tam sembol biçimi için [Multispektral Dizin Formülleri](multispectral-index-formulas.md) bölümüne bakın.
* **Nerede çalışırlar**: Özel formüller proje ayarlarıyla birlikte kaydedilir ve hem [Dizin/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) hem de işleme aşamasında kullanılabilir. Bu formüller, yalnızca 22 adet yerleşik ön ayar adını genişleten CLI/SDK `--indices` ad listesi tarafından**kabul edilmez**.***

## Dışa Aktarma

Bu ayarlar, işlenmiş görüntülerin dışa aktarılma biçimini ve kalitesini kontrol eder.

### Kalibre edilmiş görüntü biçimi

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * **TIFF (16 bit)** - Sıkıştırılmamış 16 bit TIFF biçimi
  * **TIFF (32 bit, Yüzde)** - Yansıma değerleri yüzde olarak verilen 32 bitlik kayan noktalı TIFF
  * **PNG (8 bit)** - Sıkıştırılmış 8 bit PNG biçimi
  * **JPG (8 bit)** - Sıkıştırılmış 8 bit JPEG formatı
* **Varsayılan**: TIFF (16 bit)
* **Açıklama**: İşlenmiş ve kalibre edilmiş görüntülerin kaydedileceği dosya biçimini seçer. Dışa aktarılan dosyalar, her kameranın klasörü içindeki biçime göre ayrılmış alt klasörlere (`tiff16`, `tiff32`, `png8`, `jpg8`) ve her ürün için bir adet `<Product>_Images/` klasörü oluşturulur. Dışa aktarılan dosyalar kaynak dosya adını korur — ürünü tanımlayan, dosya adı eki değil, klasördür.
* **Biçim önerileri**:
  * **TIFF (16-bit)**: Bilimsel analizler ve profesyonel iş akışları için önerilir. Sıkıştırma kaynaklı bozulmalar olmadan maksimum veri kalitesini korur. Multispektral analiz ve GIS yazılımında ileri işleme için en uygun seçenektir.
  * **TIFF (32-bit, Yüzde)**: Yansıma değerlerinin yüzde olarak (0-100%) verilmesi gereken iş akışları için en uygun seçenektir. Radyometrik ölçümler için maksimum hassasiyet sunar.
  * **PNG (8-bit)**: Web görüntüleme ve genel görselleştirme için uygundur. Kayıpsız sıkıştırma ile daha küçük dosya boyutları sunar, ancak dinamik aralık azalır.
  * **JPG (8 bit)**: En küçük dosya boyutlarına sahiptir; yalnızca önizlemeler ve web görüntülemesi için idealdir. Bilimsel analizler için uygun olmayan kayıplı sıkıştırma kullanır.
* **Not**: LATTICE parlaklığı, bu ayardan bağımsız olarak her zaman 32 bit kayan noktalı TIFF olarak dışa aktarılır.***

## Proje Şablonunu Kaydet

Bu özellik, mevcut proje ayarlarınızı yeniden kullanılabilir bir şablon olarak kaydetmenizi sağlar.

* **Tür**: Metin girişi + Kaydet düğmesi
* **Açıklama**: Ayar şablonunuz için açıklayıcı bir ad girin ve kaydet simgesine tıklayın. Şablon, gelecekteki projelerde kolayca yeniden kullanılabilmesi için mevcut tüm proje ayarlarınızı (hedef algılama, işleme seçenekleri, indeksler ve dışa aktarım biçimi) saklayacaktır. Şablonlar, proje kaydetme klasörünüzün içindeki `Project Templates/` klasöründe saklanır ve ana menüden (*Şablon Seç* / *Şablonu Kaydet* / *Şablonu Dışa Aktar*).
* **Kullanım örnekleri**:
  * Farklı kamera sistemleri için şablonlar oluşturun (RGB, multispektral, NIR)
  * Belirli ürün türleri veya analiz iş akışları için standart yapılandırmaları kaydedin
  * Ekip genelinde tutarlı ayarları paylaşın
* **Nasıl kullanılır**:
  1. İstediğiniz tüm proje ayarlarını yapılandırın
  2. Bir şablon adı girin (örn., &quot;RedEdge Survey3 NDVI Standart&quot;)
  3. Kaydet simgesine tıklayın
  4. Artık yeni projeler oluşturulurken bu şablon yüklenebilir

***

## Proje Klasörünü Kaydet

Bu ayar, yeni projelerin varsayılan olarak nereye kaydedileceğini belirler.

* **Tür**: Dizin yolu gösterimi + Düzenle düğmesi
* **Varsayılan (Windows)**: `C:\Users\[Username]\Chloros Projects`
* **Varsayılan (Linux)**: `~/Chloros Projects`
* **Açıklama**: Yeni Chloros projelerinin oluşturulduğu mevcut varsayılan dizini gösterir. Farklı bir dizin seçmek için düzenle simgesine tıklayın. Bu geçersiz kılma, `~/.chloros/working_directory.txt` dosyasında tek bir metin satırı olarak saklanır — Windows dosyasında bu, `C:\Users\<Username>\.chloros\working_directory.txt`&#x27;tir. Bu dosya yoksa veya artık mevcut olmayan bir yolu gösteriyorsa, Chloros yukarıdaki varsayılana geri döner. CLI aynı dosyayı okur ve yazar, bu nedenle `chloros-cli` ve GUI, projelerin bulunduğu konum konusunda her zaman aynı görüşte olur.
* **Proje Şablonları**, bu dizinin `Project Templates/` alt klasöründe bulunur.
* **Ne zaman değiştirilmeli**:
  * Ekip işbirliği için bir ağ sürücüsüne ayarlayın
  * Büyük veri kümeleri için daha fazla depolama alanına sahip bir sürücüye değiştirin
  * Projeleri yıl, müşteri veya proje türüne göre farklı klasörlerde düzenleyin
* **Not**: Bu ayarın değiştirilmesi yalnızca YENİ projeleri etkiler. Mevcut projeler orijinal konumlarında kalır.***

## Ayarların Kalıcılığı

Bir Chloros projesi bir **klasördür**. Tüm proje ayarları, içindeki `project.json` klasörüne kaydedilir; bağlı donanımlar da `cameras.json` ve `sensors.json` içinde bu ayarlarla birlikte hatırlanır; böylece bir projeyi yeniden açtığınızda kameralar ve ışık sensörleri de yeniden bağlanır. Bir projeyi yeniden açtığınızda, tüm ayarlar tam olarak bıraktığınız gibi geri yüklenir. Kaydedilen projeler ayrıca `chloros-cli project` veya SDK&#x27;in `open_project` dosyası kullanılarak &quot;headless&quot; modda çalıştırılabilir.

### Ayar Hiyerarşisi

Ayarlar aşağıdaki sırayla uygulanır:

1. **Sistem varsayılanları** - Chloros tarafından tanımlanan yerleşik varsayılanlar
2. **Şablon ayarları** - Bir proje oluştururken bir şablon yüklerseniz
3. **Kaydedilmiş proje ayarları** - Proje dosyasıyla birlikte kaydedilen ayarlar
4. **Manuel ayarlamalar** - Mevcut oturum sırasında yaptığınız tüm değişiklikler

### Ayarlar ve Görüntü İşleme

İşleme ayarları, bir işleme çalışması başladığında okunur. Bir ayarın değiştirilmesi, diskte zaten bulunan ürünleri geriye dönük olarak değiştirmez — yeni ayarları uygulamak için işlemeyi yeniden çalıştırın. Bazı ayarlar işlemeyi hiçbir şekilde etkilemez:

* Görüntü Küçük Resim Çözünürlüğü (sadece görüntüleme amaçlı)
* Proje Şablonunu Kaydet
* Proje Klasörünü Kaydet

***

## Yapılandırma anahtarı referansı

Otomasyon için (CLI `--config`, SDK `configure`, veya doğrudan `project.json` okuma işlemleri için), `Project Settings` altında bulunan tam anahtarlar şunlardır:

| Anahtar yolu | Tür | Varsayılan |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | 0-10000 | `25` |
| `Target Detection → Minimum Target Clustering (0-100)` | 0-100 arası sayı | `60` |
| `Processing → Vignette correction` | bool | `true` |
| `Processing → Reflectance calibration / white balance` | bool | `true` |
| `Processing → Export sensor response` | bool | `true` |
| `Processing → Export vignette corrected` | bool | `true` |
| `Processing → Export debayered` | bool | `true` |
| `Processing → Export preview` | bool | `true` |
| `Processing → Export radiance` | bool | `true` |
| `Processing → Export reflectance` | bool | `true` |
| `Processing → Array alignment` | bool | `true` |
| `Processing → Array alignment crop` | bool | `true` |
| `Processing → Array alignment interpolation` | `"Bilinear" \| "Nearest" \| "Cubic"` | `"Bilinear"` |
| `Processing → Debayer method` | `"Standard (Fast, Medium Quality)" \| "Texture Aware (Slow, Highest Quality)"` | Standart |
| `Processing → Minimum recalibration interval` | 0-3600 arası bir sayı | `0` |
| `Processing → Light sensor timezone offset` | -12..12 | `0` |
| `Processing → Apply PPK corrections` | boole | `false` |
| `Processing → DAQ cap id` | sınır profili kimliği veya `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | dizin yapılandırmaları listesi | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

`Array alignment` anahtarları, &quot;Array Alignment&quot; bölümü ilk kez işlendiğinde veya bir otomasyon çağrısı bunları ayarladığında yazılır. Bunlar mevcut olmadığında, iş akışı yukarıda gösterilen değerlerin aynısını kullanır (`true`, `true`, bilineer) kullanır; dolayısıyla bu anahtarların bulunmadığı bir proje, bu anahtarların bulunduğu bir projeyle aynı şekilde davranır.

### Ayarlar panelinde kontrolü bulunmayan, `project.json` altında depolanan anahtarlar

Bunlar aynı `Project Settings` ağacı altında bulunur ve işleme sırasında okunur, ancak kenar çubuğunda bunlara ait bir widget bulamazsınız:

| Anahtar yolu | Tür | Varsayılan | Ayarlayan |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK `input_level=`. LATTICE giriş TIFF&#x27;lerinin nasıl yorumlandığını geçersiz kılar; `auto`, her dosyanın `Chloros:ProcessingLevel` XMP etiketinden ve kanal sayısından çıkarımda bulunur. Survey3 `.raw` yakalamaları için göz ardı edilir. Kasıtlı olarak bir GUI ayarı değildir — normal her durumda “auto” doğru ayardır. |
| `Processing → Target reflectance dir` | yol dizesi | `""` | `chloros-cli process --target-reflectance-dir` veya proje hedefi API |
| `Processing → Target reflectance config` | kamera seri numarasına göre sıralanmış sözlük | `{}` | Çerçeve içi hedefin kaydedilmesi (`fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | yol dizesi | `""` | SDK `process_folder(daq_log_path=…)`. Bir `.daq` kaydına veya bunların bulunduğu bir klasöre işaret eder |
| `Target Detection → Minimum calibration target squares` | sayı | `4` | Eski varsayılan; kontrol yok ve CLI bayrağı |
| `UI → Grid thumbnail size` | sayı | `160` | Görüntü ızgarasının kendi küçük resim yakınlaştırma kaydırıcısı |

İki görüntüleyici tercihi **`project.json`** içinde en üst düzeyde, `Project Settings`&#x27;in tamamen dışında saklanır; çünkü bunlar işleme ayarları değil, görüntüleme durumudur:

| Anahtar yolu | Tür | Varsayılan | Ayarlayan |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | 1–256 arası tamsayı | `1` | Görüntü sekmesinin GSD (px) kontrolü — bkz. [Bir Görüntüyü Tam Ekran Olarak Açma](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## En İyi Uygulamalar

1. **Varsayılan ayarlarla başlayın**: Varsayılan ayarlar, çoğu MAPIR kamera sistemi ve tipik iş akışları için iyi sonuç verir.
2. **Şablonlar oluşturun**: Belirli bir iş akışı veya kamera için ayarları optimize ettikten sonra, projeler arasında tutarlılığı sağlamak amacıyla bunları bir şablon olarak kaydedin.
3. **Tam işleme öncesinde test edin**: Yeni ayarları denerken, tüm veri kümenizi işlemeden önce küçük bir resim alt kümesinde test edin.
4. **Ayarlarınızı belgelendirin**: Kamera sistemini, işleme türünü ve kullanım amacını belirten açıklayıcı şablon adları kullanın (örn., &quot;Survey3\_RGB\_NDVI\_Tarım&quot;).
5. **Dışa aktarma biçimi seçimi**: Son kullanım amacınıza göre dışa aktarma biçiminizi seçin:
   * Bilimsel analiz → TIFF (16 bit veya 32 bit)
   * GIS işleme → TIFF (16 bit)
   * Hızlı görselleştirme → PNG (8 bit)
   * Web paylaşımı → JPG (8 bit)

***

Chloros&#x27;teki multispektral indeksler hakkında daha fazla bilgi için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) sayfasına bakın.
