# Proje Ayarları

Chloros

’taki Proje Ayarları <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

kenar çubuğu, projeniz için görüntü işleme, kalibrasyon hedefi algılama, multispektral indeks hesaplamaları ve dışa aktarma seçeneklerinin tüm yönlerini yapılandırmanıza olanak tanır. Bu ayarlar projenizle birlikte kaydedilir ve birden fazla projede yeniden kullanılmak üzere şablon olarak kaydedilebilir.

## Proje Ayarlarına Erişme

Proje Ayarlarına erişmek için:

1. Chloros

&#x27;ta bir proje açın
2. Sol kenar çubuğundaki **Proje Ayarları** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line">

sekmesine tıklayın
3. Ayarlar paneli, kategorilere göre düzenlenmiş tüm mevcut yapılandırma seçeneklerini gösterecektir



<!-- SCREENSHOT-NEEDED: Full Project Settings sidebar of a LATTICE project, scrolled so the Processing category is visible showing the per-product export checkboxes (Export sensor response, Export vignette corrected, Export debayered, Export preview, Export radiance, Export reflectance) and the Debayer method row. -->

{% hint style="info" %}
**Diğer ayarlara bağlı olan ayarlar gri renkte görüntülenir.** Üst düzeydeki bir ayar, başka bir ayarın yapılmasını engellediğinde (örneğin, *Yansıma kalibrasyonu / beyaz dengesi* seçeneğinin işaretinin kaldırılması *Yansıma değerini dışa aktar* seçeneğini devre dışı bırakır), bağımlı kontrol devre dışı bırakılır ve araç ipucunda değiştirilmesi gereken ayarın adı belirtilir.
{% endhint %}

***

## Ekran

### Görüntü Küçük Resim Çözünürlüğü

* **Tür**: Açılır menü seçimi
* **Seçenekler**: `Default (512 px)`, `1024 px`, `2048 px`, `Full resolution`
* **Varsayılan**: Varsayılan (512 px)
* **Açıklama**: Görüntü ızgarasındaki küçük resimlerin işlendiği çözünürlük (en uzun kenar, piksel cinsinden). Daha yüksek değerler, yakınlaştırıldığında daha net görünür ancak yükleme süresi uzar ve daha fazla bellek kullanılır. Tam çözünürlük, orijinal görüntü boyutunu kullanır.
* **Not**: Yalnızca görüntüleme amaçlıdır — bu ayar, işleme veya dışa aktarılan dosyaları asla etkilemez.***

## Hedef Algılama

Bu ayarlar, Chloros

&#x27;un görüntülerinizdeki kalibrasyon hedeflerini nasıl algıladığını ve işlediğini kontrol eder. Her ikisi de yalnızca **Yansıma kalibrasyonu / beyaz dengesi** etkinleştirildiğinde aktiftir (aksi takdirde, hedef algılama tamamen atlandığından gri renkte görünürler).

### Minimum kalibrasyon örnek alanı (px)

* **Tür**: Sayı
* **Aralık**: 0 ila 10.000 piksel
* **Varsayılan**: 25 piksel
* **Açıklama**: Algılanan bir bölgenin geçerli bir kalibrasyon hedefi örneği olarak kabul edilmesi için gereken minimum alanı (piksel cinsinden) belirler. Daha küçük değerler daha küçük hedefleri algılar, ancak yanlış pozitif sonuçları artırabilir. Daha büyük değerler, algılama için daha büyük ve daha net hedef bölgeler gerektirir.
* **Ne zaman ayarlanmalı**:
  * Küçük görüntü bozukluklarında yanlış algılamalar alıyorsanız değeri artırın
  * Kalibrasyon hedefleriniz görüntülerinizde küçük görünüyorsa ve algılanmıyorsa değeri azaltın

### Minimum Hedef Kümeleme (0-100)

* **Tür**: Sayı
* **Aralık**: 0 ila 100
* **Varsayılan**: 60
* **Açıklama**: Kalibrasyon hedeflerini algılarken benzer renkli bölgeleri gruplandırmak için kümeleme eşiğini kontrol eder. Daha yüksek değerler, daha fazla benzer rengin bir araya gruplandırılmasını gerektirir; bu da daha ihtiyatlı bir hedef algılama ile sonuçlanır. Daha düşük değerler, bir hedef grubu içinde daha fazla renk çeşitliliğine izin verir.
* **Ne zaman ayarlanmalı**:
  * Kalibrasyon hedefleri birden fazla algılamaya bölünüyor ise değeri artırın
  * Renk varyasyonu olan kalibrasyon hedefleri tam olarak algılanmıyorsa değeri azaltın

***

## İşleme

Bu ayarlar, Chloros

&#x27;un görüntülerinizi nasıl işleyip kalibre edeceğini kontrol eder.

### Vinyet düzeltme

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Görüntülerin kenarlarında lens kararmasını telafi etmek için vinyet düzeltmesi uygular. Vinyet, lens özellikleri nedeniyle bir görüntünün köşelerinin ve kenarlarının merkezden daha koyu görünmesine neden olan yaygın bir optik olgudur.
* **Yan etki**: Bu anahtar, bir işleme sırasında hangi *kalibre edilmemiş yedek ürünün* yazılacağını da seçer (aşağıya bakın).

### Yansıma kalibrasyonu / beyaz dengesi

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Açıklama**: Yansıtma kalibrasyonunu etkinleştirir — kameraya ve mevcut olanlara bağlı olarak, çerçeve içinde algılanan kalibrasyon hedeflerinden ve/veya DAQ ışık sensörünün aşağı doğru ışık verilerinden yararlanır. Bu, veri setinizdeki yansıtma değerlerini normalleştirir ve aydınlatma koşullarından bağımsız olarak tutarlı ölçümler sağlar.
* **Devre dışı bırakıldığında**: Hedef algılama tamamen atlanır ve**hiçbir kamera tarafından yansıtma ürünü üretilemez** — hem Survey3

hedef tabanlı hem de LATTICE DAQ tabanlı. Bağımlı ayarlar (*Yansıtmayı dışa aktar*, *Minimum yeniden kalibrasyon aralığı* ve Hedef Algılama eşikleri) gri renkte görünür.

### Kalibre edilmemiş yedek ürünler: Sensör tepkisini dışa aktar / Vinyet düzeltilmiş olarak dışa aktar

* **Tür**: İki onay kutusu
* **Varsayılanlar**: Her ikisi de etkin (işaretli)
* **Açıklama**: Bir karenin yansıma kalibrasyonu yapılamadığında (kalibrasyon hedefi bulunamadığında veya yansıma kalibrasyonu devre dışı bırakıldığında), bunun yerine *kalibre edilmemiş yedek ürün* olarak kaydedilir. **Her kamera modeli için, her çalıştırmada *Vignette düzeltme* anahtarı ile seçilen iki yedek üründen tam olarak biri mevcuttur:
  * Vignette düzeltme **açık**→ `Vignette_Corrected_Images/` (**Vignette düzeltilmiş olarak dışa aktar** seçeneği tarafından belirlenir)
  * Vignette düzeltme **kapalı**→ `Sensor_Response_Images/` (**Sensör tepkisi olarak dışa aktar** seçeneği tarafından belirlenir)
* Kullanılmayan yedek seçenek gri renkte gösterilir. Kullanılan seçeneğin işaretini kaldırmak, o dosyanın yazılmasını tamamen engeller.

### LATTICE dışa aktarım ürünleri

LATTICE çekimleri içeren projelerde, içe aktarılan her LATTICE karesi, tek bir işleme aşamasında etkin **ve uygulanabilir**tüm ürünlere dağıtılır. Dört onay kutusu bu dağıtımı kontrol eder (hepsi varsayılan olarak**açık** durumdadır):

| Ayar | Çıkış klasörü | Neyi dışa aktarır |
| --- | --- | --- |
| **Debayered olarak dışa aktar** | `Debayered_Images/` | Doğrusal debayered görüntü: kalibrasyon paketinin karanlık ofseti kaldırılmış, ardından kenar duyarlı demosaik uygulanmış, doğrusallık düzeltmesi, düz alan veya renk ayrıştırma yapılmamış — bir çekimin debayered düzeyinde kaydettiği dosyanın aynısı. Sensörün doğal yönelimi korunur (XMP&#x27;de kaydedilir). RGB

ve multispektral kameralar için geçerlidir. |
| **Önizlemeyi dışa aktar** | `Preview_Images/` | Ekran önizlemesi. RGB

= beyaz dengesi (varsa DAQ ışık kaynağı, yoksa gri dünya) + gama; multispektral = sahte renk genişletme. |
| **Işınım dışa aktarma** | `Radiance_Images/` | W/m²/sr/nm biriminde Float32 spektral ışınım. Yalnızca multispektral (M3C/M3M) — RGB

ana görüntüler için geçerli değildir. *Kalibre edilmiş görüntü formatı* ayarından bağımsız olarak her zaman 32 bit TIFF

olarak yazılır. |
| **Yansıma değerini dışa aktar**| `Reflectance_Calibrated_Images/` | Uint16 yansıma değeri,**32768 = yansıma 1,0** olacak şekilde ölçeklenmiştir (XMP `Chloros:PixelScale` olarak damgalanır). Yalnızca multispektral; eşleşen bir `.daq` aşağı yönlü kayıt (veya kalite kontrolünden geçen çerçeve içi hedef) çerçeveyi kapladığında yazılır. |

* RGB

ana kameralar, debayering uygulanmış + önizleme verileri yayar; parlaklık/yansıtma değerleri bunlar için geçerli olmadığından atlanır.
* Debayering uygulanmış/önizleme verilerinin bit derinliği, *Kalibre edilmiş görüntü formatı* ayarına göre belirlenir; parlaklık her zaman float32&#x27;dir.
* Survey3

işleme, bu dört anahtarın etkisinde kalmaz.
* Çok spektral M3C kameraların parlaklık ve yansıma ürünleri de piksel başına **sıkıştırma maskesi** yazabilir; bu, bant ayrıştırma işleminin negatif bir değeri sıfıra kırpıldığı yerleri kaydeder. Bkz. [Çıktı Görüntü Formatları](../output-image-formats.md#clamp-masks).

Aynı dört anahtar, `chloros-cli process --debayered / --preview / --radiance / --reflectance` olarak başlıksız da mevcuttur. Bunlar, artık mevcut olmayan eski CLI

`--radiometric-output` bayrağının yerini almıştır.

{% hint style="warning" %}
**Uygulanabilir tüm ürünleri devre dışı bırakmak, işlemin başarısız olmasına neden olur.**

1.2.0 sürümünden itibaren, ürün talep edilen ancak hiçbir görüntü ürünü yazılmayan bir işleme, sessiz bir başarı bildirimi yerine hata bildirir ve CLI

, sıfırdan farklı bir değerle sonlanır. Günlük dosyasında, yazılamayan ürünün adı ve nedeni belirtilir. Kasıtlı olarak yalnızca meta veriler içeren bir işleme (hiçbir şey talep edilmedi) yine de başarılı sayılır.
{% endhint %}

### Yansıma kaynağı (proje ayarı, CLI

üzerinden ayarlanır)

Proje ayrıca, LATTICE yansıma ürününün hangi **yansıma referansını** kullandığını da saklar. Ayarlar panelinde buna özel bir kontrol yoktur; değer, proje yapılandırmasında `Processing → "Target reflectance source"` olarak saklanır ve `chloros-cli process --reflectance-source {auto,target,daq}` ile ayarlanır (SDK

&#x27;daki `configure()`&#x27;te bunun için bir anahtar kelime yoktur):

* **`auto`** (varsayılan): Kalite kontrolünden (QA) geçen çerçeve içi kalibrasyon hedefi mutlak referans olur; hedef bulunmadığında veya QA başarısız olduğunda DAQ aşağı doğru yayılım bölünmesine (ρ = πL/E) geri dönülür. Bir bayrakla (düşen bir panel veya arızalı bir bant) QA&#x27;yı geçen bir hedef otomatik olarak tercih edilmez: hedef, hedef+DAQ ve sadece DAQ zincirleri, ayrılmış panellerde ölçülen puanlarına göre sırayla denenir.
* **`target`**: katı hedef odaklı yansıma — DAQ ikamesi yoktur.
* **`daq`**: DAQ&#x27;ya dayalı yansıma; çerçeve içi hedefler referans olarak kullanılmaz.

Kaydedilen değer, büyük/küçük harf duyarlılığı gözetilmeden eşleştirilir ve birkaç farklı yazım şekli takma ad olarak kabul edilir: `target`, `target_image`, `empirical` ve `empirical_line` hepsi **hedef**anlamına gelir; `daq`, `dls`, `light_sensor` ve `sensor` hepsi**daq**anlamına gelir. Bunlar dışındaki her şey — mevcut olmayan bir anahtar dahil —**auto** olarak çözümlenir.

Birim başına **ölçülen** hedef taramaları, hedef birimin seri numarası/QR kodu ile (`<serial>.csv` gibi) üç yerde aranır: `--target-reflectance-dir` ile belirtilen dizin (`Processing → "Target reflectance dir"` olarak saklanır), projenin kendi `target_reflectance/` klasörü ve `CHLOROS_TARGET_REFLECTANCE_DIR` ortam değişkenindeki yol. O birim için ölçülmüş tarama bulunmadığında, bunun yerine hedef model için yayınlanmış nominal eğri kullanılır.

### Debayer yöntemi

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * Standart (Hızlı, Orta Kalite)
  * Dokuya Duyarlı (Yavaş, En Yüksek Kalite) \[Chloros

+]
* **Varsayılan**: Standart (Hızlı, Orta Kalite)
* **Açıklama**: Ham Bayer desenli sensör verilerini tam renkli görüntülere dönüştürmek için kullanılan demosaicing algoritmasını seçer. &quot;Standart (Hızlı, Orta Kalite)&quot; yöntemi, işleme hızı ile görüntü kalitesi arasında optimum bir denge sağlar. &quot;Doku Duyarlı (Yavaş, En Yüksek Kalite)&quot; \[Chloros

+] yöntemi, neredeyse tüm demosaicing gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli kenar duyarlı bir demosaicing algoritması kullanır. Doku Duyarlı modelin çalışması için GPU belleği (VRAM) gereklidir. Daha hızlı işleme için 4 GB&#x27;den fazla VRAM&#x27;iniz olduğunda bu modeli kullanmanızı öneririz.
* **Satır bir açılır menü olduğunda**: iki seçenekli açılır menü yalnızca**her ikisi** de geçerli olduğunda görünür — uygun bir Chloros

+ aboneliğiyle oturum açmış olmanız **ve** projenin LATTICE çekimleri içermemesi gerekir. Aksi takdirde satır, seçilebilecek hiçbir seçenek olmadan `Standard (Fast, Medium Quality)` şeklinde düz metin olarak görüntülenir.
* **LATTICE notu**: LATTICE ile eğitilmiş Texture Aware modeli bulunmamaktadır ve iş akışı, depolanan değerden bağımsız olarak LATTICE kareleri için standart demosaik işlemini zorlar. Zaten Texture Aware seçeneğinin seçili olduğu bir projeye bir LATTICE klasörü eklerseniz, Chloros

, `project.json` içinde eski bir değer bırakmak yerine ayarı tekrar &quot;Standart&quot; olarak yazar.

### Minimum yeniden kalibrasyon aralığı

* **Tür**: Sayı
* **Aralık**: 0 ila 3.600 saniye
* **Varsayılan**: 0 saniye
* **Açıklama**: Kalibrasyon hedeflerinin kullanılması arasındaki minimum zaman aralığını (saniye cinsinden) belirler. 0 olarak ayarlandığında, Chloros

algılanan her kalibrasyon hedefini kullanır. Daha yüksek bir değere ayarlandığında, Chloros

yalnızca aralarında en az bu kadar saniye olan kalibrasyon hedeflerini kullanır; bu da sık sık kalibrasyon hedefi yakalamaları içeren veri kümeleri için işlem süresini azaltır.
* **Ne zaman ayarlanmalı**:
  * Işık koşulları değişken olduğunda maksimum kalibrasyon doğruluğu için 0 olarak ayarlayın
  * Işık koşulları sabit olduğunda ve sık sık kalibrasyon hedefi görüntüleri elde ettiğinizde daha hızlı işleme için değeri artırın (örneğin, 60-300 saniyeye)

### Işık sensörü saat dilimi farkı

* **Tür**: Sayı
* **Aralık**: -12 ila +12 saat
* **Varsayılan**: 0 saat
* **Açıklama**: Işık sensörü verilerinin zaman damgaları için saat dilimi kaymasını (UTC&#x27;ye göre saat cinsinden) belirtir; bu ayar, ışık sensörü günlüklerini görüntü yakalama zamanlarıyla eşleştirirken kullanılır. Daha yeni `.daq` kayıtları kendi saat dilimi bilgilerini içerir; bu nedenle bu ayar esas olarak yerel saatle kaydedilmiş eski günlükler için gereklidir.

### PPK düzeltmelerini uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Devre dışı (işaretli değil)
* **Açıklama**: GPS (GNSS) içeren MAPIR

DAQ kayıt cihazlarından alınan Sonradan İşlenmiş Kinematik (PPK) düzeltmelerinin kullanımını etkinleştirir. Etkinleştirildiğinde, Chloros

, proje dizininizde pozlama pini verileri içeren tüm .daq günlük dosyalarını kullanacak ve görüntülere hassas coğrafi konum düzeltmeleri uygulayacaktır.
* **Gereksinim**: Proje dizininizde pozlama pimi girişleri içeren bir .daq kayıt dosyası bulunmalıdır
* **Ne zaman etkinleştirilmeli**: .daq kayıt dosyanızda pozlama geri bildirim girişleri varsa, PPK düzeltmesini her zaman etkinleştirmeniz önerilir.

### Pozlama Pini 1

* **Tür**: Açılır menü seçimi
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; seçeneği etkinleştirildiğinde VE Pin 1 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera modeli adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: PPK zaman senkronizasyonu için Pozlama Pini 1&#x27;e belirli bir kamera atar. Pozlama pini, kamera deklanşörünün tetiklendiği tam zamanlamayı kaydeder; bu, doğru PPK coğrafi konum belirleme için çok önemlidir.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Kamerayı otomatik olarak seçer
  * Tek kamera + iki pin: Pin 1, kameraya otomatik olarak atanır
  * Birden fazla kamera: Manuel seçim gereklidir

### Pozlama Pini 2

* **Tür**: Açılır menüden seçim
* **Görünürlük**: Yalnızca &quot;PPK düzeltmelerini uygula&quot; seçeneği etkinleştirildiğinde VE Pin 2 için pozlama verisi mevcut olduğunda görünür
* **Seçenekler**:
  * Projede algılanan kamera model adları
  * &quot;Kullanma&quot; - Bu pozlama pimini yok say
* **Varsayılan**: Proje yapılandırmasına göre otomatik olarak seçilir
* **Açıklama**: Çift kamera kurulumunda PPK zaman senkronizasyonu için Pozlama Pini 2&#x27;ye belirli bir kamera atar.
* **Otomatik seçim davranışı**:
  * Tek kamera + tek pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Tek kamera + iki pin: Pin 2 otomatik olarak &quot;Kullanma&quot; olarak ayarlanır
  * Birden fazla kamera: Manuel seçim gereklidir
* **Not**: Aynı kamera, Pin 1 ve Pin 2&#x27;ye aynı anda atanamaz.***

## DAQ Işık Sensörü

Bu bölüm, Proje Ayarları&#x27;nda yer alır ve projedeki tüm DAQ aşağı doğru ışık dosyalarını listeler — `.daq` kayıtları ve DAQ-M `.csv` aşağı doğru ışık kayıtları. Işık Sensörleri sekmesinde yapılan kayıtlar, açık projeye otomatik olarak eklenir.

<!-- SCREENSHOT-NEEDED: Project Settings "DAQ Light Sensor" section of a project containing at least one .daq file, showing the "Cap override (all files)" dropdown and a per-file row with its resolved cap. -->



Her satırda dosya, sensör modeli ve o dosya için geçerli olan difüzör kapağı düzeltmesi gösterilir. Satırların üstünde, proje genelinde geçerli tek bir kontrol bulunur:

### Kapak geçersiz kılma (tüm dosyalar)

* **Tür**: Açılır menü seçimi
* **Seçenekler**: `Auto` artı projede bulunan sensör türleri için geçerli kapak düzeltme profilleri
* **Varsayılan**: Otomatik
* **Kaydedilme şekli**: `Processing → "DAQ cap id"` (varsayılan `auto`)
* **Açıklama**: `Auto`, her dosyanın kaydedilmiş kapağını kullanır (hiçbir şey kaydedilmemişse Sunshine kapağı varsayılır, ancak DAQ-U, DAQ-M ve DAQ-E, kapaksız olarak gönderilir ve Chloros

, takılan kapağı göremez, bu nedenle işleme sırasında varsayılan sınır için uyarı verilir: takılan sınırı seçin). Belirli bir sınır seçilmesi, difüzörü kalibrasyonunun bir parçası olan DAQ-E-S&#x27;ler hariç, projedeki **tüm** aşağı yönlü dosyaları geçersiz kılar; yalnızca DAQ-E-S&#x27;lerden oluşan bir projede açılır menü görünmez: ham kayıtlar bununla düzeltilir, ve halihazırda bir kapak içeren kayıtlar yeniden referanslanır (kaydedilen düzeltme geri alınır ve seçilen düzeltme uygulanır).
* **Önemli**: Seçilen kapak, kayıt sırasında fiziksel olarak takılan kapakla eşleşmelidir. Ne sensör ne de yazılım fiziksel kapağı algılayamaz — uyumsuz bir kapak kimliği, spektrumları yanlış düzeltir.

Dosya başına açılır menüler yerine kasıtlı olarak proje genelinde **tek** bir kontrol bulunur: bu ayar, projedeki her aşağı doğru yayılan kaynağa uygulanır.***

## Dizi Hizalama

Bu bölüm, **yalnızca** projedeki en az bir görüntü, LATTICE dizilerinin yakalama sırasında eklediği modülden modüle hizalama dönüşümünü (XMP `Chloros:Alignment*` etiketleri) taşıdığında görünür. Bu bölüm, kaç görüntünün hizalama etiketleri taşıdığını, hangi kameranın referans olduğunu (`REF` rozeti) ve kamera başına görüntü sayılarını gösteren bir tabloyu gösterir.

<!-- SCREENSHOT-NEEDED: Project Settings "Array Alignment" section for an imported LATTICE array capture set, showing the tagged-image count, the per-camera rows with the REF badge, and the three controls (Apply array alignment, Crop to common overlap, Resampling). -->

### Dizi hizalamasını uygula

* **Tür**: Onay kutusu
* **Varsayılan**: Etkin (işaretli)
* **Kaydedilme biçimi**: `Processing → "Array alignment"`
* **Açıklama**: İşlenmiş her ürünü (debayering / önizleme / parlaklık / yansıma / indeks), çekim sırasında damgalanan dönüşümü kullanarak dizinin ortak referans geometrisine dönüştürür. Kapalı = sensör başına özgün geometride dışa aktar.

### Ortak örtüşme alanına kırp

* **Tür**: Onay kutusu (*Dizi hizalaması uygula* etkinken çalışır)
* **Varsayılan**: Etkin (işaretli)
* **Kaydedilme şekli**: `Processing → "Array alignment crop"`
* **Açıklama**: Hizalanmış dışa aktarımları, tüm kamera modüllerinin paylaştığı bölgeye kırpar; böylece her bant aynı kaplama alanına sahip olur. Kapalı seçeneği, sensörün tam tuvalini korur (kaynağın dışını siyahla doldurur).

### Yeniden Örnekleme

* **Tür**: Açılır menü (yalnızca *Dizi hizalaması uygula* seçeneği açıkken etkin)
* **Seçenekler**: `Bilinear (smooth, default)`, `Nearest (preserve exact values)`, `Cubic (sharpest)`
* **Varsayılan**: Bilineer
* **Kaydedilme biçimi**: `Processing → "Array alignment interpolation"`
* **Açıklama**: Hizalama çarpıtmasında kullanılan enterpolasyon. En yakın değer, sıkı radyometrik analiz için kaynak değerlerini tam olarak korur (pikseller arası karıştırma yoktur); Çift doğrusal, haritalama ve görsel kullanım için en uygun seçenektir.

Aynı üç seçenek, başlıksız olarak `chloros-cli process --array-alignment`, `--array-alignment-crop` ve `--array-alignment-interp {bilinear,nearest,cubic}` şeklinde de mevcuttur.

***

## İndeks

Bu ayarlar, analiz ve görselleştirme amacıyla multispektral indeksleri yapılandırmanıza olanak tanır.

### İndeks ekle

* **Tür**: Özel indeks yapılandırma paneli
* **Açıklama**: Görüntü işleme sırasında hesaplanacak multispektral bitki örtüsü indekslerini (NDVI

, NDRE

, EVI

vb.) seçip yapılandırabileceğiniz etkileşimli bir panel açar. Her biri kendi görselleştirme ayarlarına sahip birden fazla indeks ekleyebilirsiniz.
* **Kullanılabilir indeksler**: GUI açılır menüsünde**27** adet önceden tanımlanmış multispektral indeks formülü bulunur (CLI

/SDK

`--indices` seçeneği) dahil olmak üzere tam liste için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) bölümüne bakın).
* **Özellikler**:
  * Önceden tanımlanmış indeks formüllerinden seçim yapın
  * Kameranızın filtre kanallarını formülün bant yuvalarına sürükleyin
  * Görselleştirme renk gradyanlarını yapılandırın (LUT - Arama Tabloları)
  * Eşik değerlerini ve kırpma modlarını ayarlayın
  * Özel indeks formülleri oluşturun
* **Not**: Tek bantlı LATTICE M3M mono kameralar için indeksler hesaplanmaz — tek bantta çok bantlı indeksler tanımlanmamıştır. Survey3

ve LATTICE M3C bu durumdan etkilenmez.



<!-- SCREENSHOT-NEEDED: Project Settings > Index section with one index added and expanded: the filter dropdown, the formula dropdown open showing preset names, the coloured channel circles above the rendered formula, and the "+ Add LUT" button below it. -->

Eklediğiniz her endeks, formülünü matematiksel olarak işler ve her bant yuvası için renkli bir daire gösterir: kırmızı = Red

, yeşil = Green

, mavi = Blue

, turuncu = Orange

, camgöbeği = Cyan

, mor = NIR

, macenta = RE. Formülün üstündeki satırdan bir daireyi bir yuvaya sürükleyerek bağlayın; bağlı bir yuvayı temizlemek için üzerine çift tıklayın. Endeks, formülün kullandığı her yuvada bir kanal olduğunda yalnızca bir kez hesaplanır.

### Özel Formüller (Chloros

+ Özelliği)

* **Tür**: Özel formül tanımlarından oluşan dizi
* **Kullanılabilirlik**: Uygun bir Chloros

+ aboneliği ile oturum açılması gerekir.
* **Açıklama**: Bant matematiğini kullanarak özel multispektral indeks formülleri oluşturmanıza ve kaydetmenize olanak tanır. Özel formüller proje ayarlarınızla birlikte kaydedilir ve yerleşik indeksler gibi kullanılabilir.
* **Nasıl oluşturulur**:
  1. Dizin yapılandırma panelinde, özel formül hesaplayıcısını açın
  2. Formülü, bant adlarını değil **bant-slot sembollerini** kullanarak yazın
  3. Formülü açıklayıcı bir adla kaydedin — formül, formül açılır menüsünün en altında görünür ve kameranızın kanal dairelerini, yerleşik ön ayarlarda olduğu gibi yuvalarına sürükleyebilirsiniz
* **Formül sözdizimi**:
  * Bant yuvaları: `x`, `y`, `z`, `a`, `b`, `c` — sürükleyerek gerçek kanallara eşleştirebileceğiniz altı konum
  * Operatörler: `+`, `-`, `*`, `/`, `^` ve `()` (gruplama için)
  * İşlevler: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
* **Neden grup isimleri değil de semboller?**: `(y-x)/(y+x)` şeklinde yazılmış bir formül her kamerada çalışır; çünkü sürükle-bırak eşlemesi, `y`&#x27;in bir RGN

filtresinin 850 nm NIR

&#x27;si mi yoksa bir OCN

filtresinin 808 nm NIR

&#x27;si mi olduğuna karar verir. Yerleşik ön ayarlar da aynı şekilde saklanır — 27 formülün tümünün tam sembol biçimleri için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) bölümüne bakın.
* **Nerede çalışırlar**: Özel formüller proje ayarlarıyla birlikte kaydedilir ve [İndeks/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) ile işleme sırasında kullanılabilir. CLI

/SDK

`--indices` ad listesi tarafından **kabul edilmez**; bu liste yalnızca 22 yerleşik ön ayar adını gösterir.***

## Dışa Aktarma

Bu ayarlar, işlenmiş görüntülerin dışa aktarılma biçimini ve kalitesini kontrol eder.

### Kalibre edilmiş görüntü biçimi

* **Tür**: Açılır menü seçimi
* **Seçenekler**:
  * **TIFF

(16 bit)** - Sıkıştırılmamış 16 bit TIFF

biçimi
  * **TIFF

(8 bit)** - Sıkıştırılmamış 8 bit TIFF

biçimi
  * **TIFF

(32 bit, Yüzde)** - Yansıma değerleri yüzde olarak verilen 32 bit kayan noktalı TIFF


  * **PNG

(8 bit)** - Sıkıştırılmış 8 bit PNG

biçimi
  * **JPG (8 bit)** - Sıkıştırılmış 8 bit JPEG

biçimi
* **Varsayılan**: TIFF

(16 bit); tamamen 8 bitlik bir projede TIFF

(8 bit) gösterilir ve yalnızca 8 bitlik biçimler sunulur
* **Açıklama**: İşlenmiş ve kalibre edilmiş görüntülerin kaydedileceği dosya biçimini seçer. Dışa aktarılan dosyalar, her kameranın klasörü içindeki biçime göre ayrılmış alt klasörlere yerleştirilir (`tiff16`, `tiff32`, `png8`, `jpg8`)ve her ürün için bir adet `<Product>_Images/` klasörü bulunur. Dışa aktarılan dosyalar kaynak dosya adını korur — ürünü tanımlayan, dosya adı soneki değil, klasördür.
* **Biçim önerileri**:
  * **TIFF

(16 bit)**: Bilimsel analizler ve profesyonel iş akışları için önerilir. Sıkıştırma kaynaklı bozulmalar olmadan maksimum veri kalitesini korur. Multispektral analizler ve GIS yazılımlarında ileri işleme için en uygun seçenektir.
  * **TIFF

(32 bit, Yüzde)**: Yansıma değerlerinin yüzde olarak (0-100%). Radyometrik ölçümler için maksimum hassasiyet sunar.
  * **PNG

(8 bit)**: Web&#x27;de görüntüleme ve genel görselleştirme için uygundur. Kayıpsız sıkıştırma sayesinde dosya boyutları daha küçüktür, ancak dinamik aralık azalır.
  * **JPG (8 bit)**: En küçük dosya boyutlarına sahiptir; yalnızca önizlemeler ve web görüntülemesi için idealdir. Bilimsel analizler için uygun olmayan kayıplı sıkıştırma kullanır.
* **Not**: LATTICE parlaklığı, bu ayardan bağımsız olarak her zaman 32 bit kayan noktalı TIFF

olarak dışa aktarılır.

***

## Proje Şablonunu Kaydet

Bu özellik, mevcut proje ayarlarınızı yeniden kullanılabilir bir şablon olarak kaydetmenizi sağlar.

* **Tür**: Metin girişi + Kaydet düğmesi
* **Açıklama**: Ayar şablonunuz için açıklayıcı bir ad girin ve kaydet simgesine tıklayın. Şablon, gelecekteki projelerde kolayca yeniden kullanılabilmesi için tüm mevcut proje ayarlarınızı (hedef algılama, işleme seçenekleri, indeksler, ve dışa aktarma biçimi) gelecekteki projelerde kolayca yeniden kullanılabilmesi için saklayacaktır. Şablonlar, proje kaydetme klasörünüzün içindeki `Project Templates/` klasöründe saklanır ve ana menüden (*Şablon Seç* / *Şablonu Kaydet* / *Şablonu Dışa Aktar*) seçilebilir veya dışa aktarılabilir.
* **Kullanım örnekleri**:
  * Farklı kamera sistemleri için şablonlar oluşturun (RGB

, multispektral, NIR

)
  * Belirli ürün türleri veya analiz iş akışları için standart yapılandırmaları kaydedin
  * Ekip içinde tutarlı ayarları paylaşın
* **Nasıl kullanılır**:
  1. İstediğiniz tüm proje ayarlarını yapılandırın
  2. Bir şablon adı girin (örn., &quot;RedEdge

Survey3

NDVI

Standard&quot;)
  3. Kaydet simgesine tıklayın
  4. Artık yeni projeler oluştururken bu şablon yüklenebilir

***

## Proje Klasörünü Kaydet

Bu ayar, yeni projelerin varsayılan olarak nereye kaydedileceğini belirler.

* **Tür**: Dizin yolu gösterimi + Düzenle düğmesi
* **Varsayılan (Windows

)**: Belgeler klasörünüzdeki `Documents\MAPIR\Chloros Projects` — genellikle `C:\Users\[Username]\Documents\MAPIR\Chloros Projects` veya OneDrive ya da gezici profilin Belgeler klasörünü taşıdığı yer
* **Varsayılan (Linux

)**: `~/Documents/MAPIR/Chloros Projects`
* **Açıklama**: Yeni Chloros

projelerinin oluşturulduğu mevcut varsayılan dizini gösterir. Farklı bir dizin seçmek için düzenle simgesine tıklayın. Bu ayar, `~/.chloros/working_directory.txt` dosyasında tek satırlık bir metin olarak saklanır — Windows

&#x27;ta bu dosya `C:\Users\<Username>\.chloros\working_directory.txt`&#x27;tir. Bu dosya eksikse veya artık mevcut olmayan bir yol belirtiliyorsa, Chloros

yukarıdaki varsayılana geri döner. CLI

aynı dosyayı okur ve yazar, bu nedenle `chloros-cli` ve GUI, projelerin bulunduğu konum konusunda her zaman uyumludur.
* **Proje Şablonları**, bu dizinin `Project Templates/` alt klasöründe bulunur.
* **Ne zaman değiştirilmeli**:
  * Ekip işbirliği için bir ağ sürücüsüne ayarlayın
  * Büyük veri kümeleri için daha fazla depolama alanına sahip bir sürücüye değiştirin
  * Projeleri yıl, müşteri veya proje türüne göre farklı klasörlerde düzenleyin
* **Not**: Bu ayarın değiştirilmesi yalnızca YENİ projeleri etkiler. Mevcut projeler orijinal konumlarında kalır.
* **Windows

yol uzunluğu**: Windows

uzun yolları etkinleştirilmedikçe (yükleyicinin **Windows

uzun yollarını etkinleştir** seçeneği), yaklaşık 130 karakterden daha derin bir proje klasörü, işleme alınmadan reddedilir; çünkü bu klasörün oluşturacağı yollar, Windows

&#x27;ın 260 karakterlik yol sınırını aşacaktır. Bkz. [İşlemeyi Başlatma](../processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).

***

## Ayarların Kalıcılığı

Bir Chloros

projesi bir **klasördür**. Tüm proje ayarları, projenin içindeki `project.json` dosyasına kaydedilir; bağlı donanımlar da `cameras.json` ve `sensors.json` içinde bu klasörle birlikte hatırlanır; böylece bir projeyi yeniden açtığınızda kameralar ve ışık sensörleri de yeniden bağlanır. Bir projeyi yeniden açtığınızda, tüm ayarlar tam olarak bıraktığınız haliyle geri yüklenir. Kaydedilen projeler, `chloros-cli project` veya SDK

&#x27;nın `open_project` dosyası kullanılarak başlıksız olarak da çalıştırılabilir.

### Ayar Hiyerarşisi

Ayarlar aşağıdaki sırayla uygulanır:

1. **Sistem varsayılanları** - Chloros

tarafından tanımlanan yerleşik varsayılanlar 2. **Şablon ayarları** - Bir proje oluştururken bir şablon yüklediğinizde
3. **Kaydedilmiş proje ayarları** - Proje dosyasıyla birlikte kaydedilen ayarlar
4. **Manuel ayarlamalar** - Mevcut oturum sırasında yaptığınız tüm değişiklikler

### Ayarlar ve Görüntü İşleme

İşleme ayarları, bir işleme çalışması başladığında okunur. Bir ayarın değiştirilmesi, diskte halihazırda bulunan ürünleri geriye dönük olarak değiştirmez — yeni ayarları uygulamak için işlemeyi yeniden çalıştırın. Bazı ayarlar işlemeyi hiçbir şekilde etkilemez:

* Görüntü Küçük Resim Çözünürlüğü (sadece görüntüleme amaçlı)
* Proje Şablonunu Kaydet
* Proje Klasörünü Kaydet

***

## Yapılandırma anahtarı referansı

Otomasyon için (CLI

`--config`, SDK

`configure` veya `project.json` dosyasını doğrudan okuma), `Project Settings` dosyası altında bulunan anahtarlar şunlardır:

| Anahtar yolu | Tür | Varsayılan |
| --- | --- | --- |
| `Display → Image Thumbnail Resolution` | `"512" \| "1024" \| "2048" \| "full"` | `"512"` |
| `Target Detection → Minimum calibration sample area (px)` | 0-10000 arası sayı | `25` |
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
| `Processing → Minimum recalibration interval` | sayı 0-3600 | `0` |
| `Processing → Light sensor timezone offset` | sayı -12..12 | `0` |
| `Processing → Apply PPK corrections` | bool | `false` |
| `Processing → DAQ cap id` | sınır profili kimliği veya `"auto"` | `"auto"` |
| `Processing → Target reflectance source` | `"auto" \| "target" \| "daq"` | `"auto"` |
| `Index → Add index` | dizin yapılandırmaları listesi | `[]` |
| `Export → Calibrated image format` | `"TIFF (16-bit)" \| "TIFF (8-bit)" \| "TIFF (32-bit, Percent)" \| "PNG (8-bit)" \| "JPG (8-bit)"` | `"TIFF (16-bit)"` |

`Array alignment` anahtarları, &quot;Dizi Hizalama&quot; bölümü ilk kez işlendiğinde veya bir otomasyon çağrısı bunları ayarladığında yazılır. Bunlar mevcut olmadığında, iş akışı yukarıda gösterilen değerlerin aynısını kullanır (`true`, `true`, bilineer) kullanır; dolayısıyla bu anahtarların bulunmadığı bir proje, bu anahtarların bulunduğu bir projeyle aynı şekilde davranır.

### Ayarlar panelinde kontrolü bulunmayan, `project.json` altında depolanan anahtarlar

Bunlar aynı `Project Settings` ağacı altında bulunur ve işleme sırasında okunur, ancak kenar çubuğunda bunlara ait bir widget bulamazsınız:

| Anahtar yolu | Tür | Varsayılan | Ayarlayan |
| --- | --- | --- | --- |
| `Processing → LATTICE input level` | `"auto" \| "raw" \| "debayered" \| "processed"` | `"auto"` | `chloros-cli process --input-level`, SDK

`input_level=`. LATTICE giriş TIFF

&#x27;lerinin nasıl yorumlandığını geçersiz kılar; `auto`, her dosyanın `Chloros:ProcessingLevel` XMP etiketinden ve kanal sayısından çıkarım yapar. Survey3

`.raw` yakalamaları için göz ardı edilir. Kasıtlı olarak bir GUI ayarı değildir — normal her durumda &quot;auto&quot; doğru ayardır. |
| `Processing → Target reflectance dir` | yol dizesi | `""` | `chloros-cli process --target-reflectance-dir` veya proje hedef API

&#x27;si |
| `Processing → Target reflectance config` | kamera seri numarasına göre sıralanmış sözlük | `{}` | Çerçeve içi bir hedefin kaydedilmesi (mod `fixed_block` / `fixed_strip` / `aruco`) |
| `Processing → DAQ-U log path` | yol dizesi | `""` | SDK

`process_folder(daq_log_path=…)`. Bir `.daq` kaydına veya bunların bulunduğu bir klasöre işaret eder |
| `Target Detection → Minimum calibration target squares` | sayı | `4` | Eski varsayılan; kontrol yok ve CLI

bayrağı yok |
| `UI → Grid thumbnail size` | sayı | `160` | Görüntü ızgarasının kendi küçük resim yakınlaştırma kaydırıcısı |

İki görüntüleyici tercihi, işleme ayarları değil görüntüleme durumu oldukları için **`project.json`&#x27;in en üst düzeyinde**, `Project Settings`&#x27;in tamamen dışında saklanır:

| Anahtar yolu | Tür | Varsayılan | Ayarlayan |
| --- | --- | --- | --- |
| `viewer_display → gsd_bin` | 1–256 arası tamsayı | `1` | Görüntü sekmesinin GSD (px) kontrolü — bkz. [Bir Görüntüyü Tam Ekran Olarak Açma](../image-viewer-gui/opening-an-image-full-screen.md) |

***

## En İyi Uygulamalar

1. **Varsayılan ayarlarla başlayın**: Varsayılan ayarlar, çoğu MAPIR

kamera sistemi ve tipik iş akışları için iyi sonuç verir.
2. **Şablonlar oluşturun**: Belirli bir iş akışı veya kamera için ayarları optimize ettikten sonra, projeler arasında tutarlılığı sağlamak amacıyla bunları bir şablon olarak kaydedin.
3. **Tam işleme öncesinde test edin**: Yeni ayarları denerken, tüm veri kümenizi işlemeden önce küçük bir görüntü alt kümesinde test edin.
4. **Ayarlarınızı belgelendirin**: Kamera sistemini, işleme türünü ve kullanım amacını belirten açıklayıcı şablon adları kullanın (ör. &quot;Survey3

\_RGB\_NDVI\_Agriculture&quot;).
5. **Dışa aktarma biçimi seçimi**: Son kullanım amacınıza göre dışa aktarma biçiminizi seçin:
   * Bilimsel analiz → TIFF (16 bit veya 32 bit)
   * GIS işleme → TIFF

(16 bit)
   * Hızlı görselleştirme → PNG

(8 bit)
   * Web paylaşımı → JPG (8 bit)

***

Chloros

&#x27;taki multispektral indeksler hakkında daha fazla bilgi için [Multispektral İndeks Formülleri](multispectral-index-formulas.md) sayfasına bakın.
