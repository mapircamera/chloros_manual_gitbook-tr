# Görüntü Katmanları

Görüntü Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsü**, görüntüleyiciden çıkmanıza gerek kalmadan, incelediğiniz görüntünün tüm sürümleri arasında — kaynak çekiminden, işlenmiş her bir ürün üzerinden hesaplanmış dizin görüntülerine kadar — geçiş yapmanızı sağlar.

## Görüntü Katmanları Nedir?

Chloros&#x27;te bir &quot;katman&quot;, bir kaynak görüntüye kayıtlı tek bir **ürün dosyası**dır. İçe aktarma işlemi size kaynak dosyaları sağlar; işleme ise çalıştırma sırasında oluşturulan her ürün için bir katman ekler. Dışa aktarılan dosyalar kaynak dosya adını korur — ürünü tanımlayan**klasör** budur ve katman adı, Chloros&#x27;un o klasör için kullandığı etikettir.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Katman listesi

### Her zaman mevcut olanlar

| Katman | Nedir |
| --- | --- |
| **JPG**(veya**PNG**/**TIFF**) | Yakalama işlemiyle birlikte gelen temel dosya. Survey3, her `.RAW`&#x27;in yanına bir `.JPG` içe aktarır; LATTICE yakalamaları, bir PNG veya TIFF görüntü önizlemesi getirir. Gerçekte neyin içe aktarıldığına göre etiketlenir |
| **RAW (Orijinal)** | Herhangi bir düzeltme uygulanmadan görüntülenmek üzere debayering işlemi uygulanmış kaynak ham kare. İçe aktarıldığı andan itibaren kullanılabilir — işleme tabi tutulması gerekmez |

Temel dosyası **raw** karesi olan bir LATTICE yakalaması için ayrı bir temel girişi yoktur: `RAW (Original)` bunu zaten kapsamaktadır.

### Survey3 işleme ürünleri

| Katman | Kaydedildiği yer | Ne zaman mevcut olur |
| --- | --- | --- |
| **RAW (Hedef)** | — | Karenin bir kalibrasyon hedefi içerdiği tespit edildi |
| **RAW (Yansıma)** | `Reflectance_Calibrated_Images/` | Bu karede yansıma kalibrasyonu başarıyla gerçekleştirildi |
| **Vinyet Düzeltmesi Yapılmış**| `Vignette_Corrected_Images/` | Çerçeve yansıma kalibrasyonuna tabi tutulamadı**ve** *Vinyet düzeltmesi* açıktı |
| **Sensör Tepkisi**| `Sensor_Response_Images/` | Çerçeve yansıma kalibrasyonuna tabi tutulamadı**ve** *Vinyet düzeltme* kapalıydı |
| **Beyaz Dengesi Ayarlandı** | `White_Balanced_Images/` | Beyaz dengesi ayarlanmış bir ürün kaydedildi |

{% hint style="info" %}
**Vinyet Düzeltme ve Sensör Tepkisi birbirinin alternatifi olup, asla ikisi birden etkin olamaz.** Her kamera modeli için her işleme turunda tam olarak bir adet kalibre edilmemiş yedek ürün bulunur ve *Vinyet düzeltme* seçeneği hangisinin kullanılacağını belirler. Bkz. [Proje Ayarları](../project-settings/project-settings.md).
{% endhint %}

### LATTICE seviyeleri

LATTICE, tek bir işleme aşamasında bunları fan out olarak yakalar. Hangi seviyelerin mevcut olduğu, Proje Ayarları&#x27;ndaki ürün başına dışa aktarma seçeneklerine ve kameraya neyin geçerli olduğuna bağlıdır.

| Katman | Kaydedildiği yer | Uygulanacağı |
| --- | --- | --- |
| **RAW (Debayered)** | `Debayered_Images/` | RGB ve multispektral |
| **RAW (Önizleme)** | `Preview_Images/` | Multispektral (yanlış renk uzantısı) |
| **Beyaz Dengeli** | `Preview_Images/` | RGB ana kameralar — RGB önizlemesi, aynı adlı Survey3 katmanıyla hizalanması için bu ad altında kaydedilmiştir |
| **RAW (Parlaklık)** | `Radiance_Images/` | Yalnızca multispektral |
| **RAW (Yansıma)** | `Reflectance_Calibrated_Images/` | Yalnızca multispektral ve yalnızca eşleşen bir `.daq` aşağı doğru ışınım kaydı veya kalite kontrolünden geçmiş bir çerçeve içi hedef çerçeveyi kapladığında |

RGB ana kameralarda bant başına radyometri bulunmadığından, parlaklık ve yansıma değerleri **uygulanamaz** olarak atlanır — günlük dosyasında bu durum, sessizce hata vermeden açıkça belirtilir.

### Dizin, LUT ve sandbox katmanları

| Katman şeması | Örnek | Kaynağı |
| --- | --- | --- |
| **RAW (`<INDEX>` Dizin)** | `RAW (NDVI Index)` | Proje Ayarlarında yapılandırılan dizin başına bir tane, işleme sırasında hesaplanır |
| **`<INDEX>` LUT** | `NDVI LUT` | Bir indeksin renk eşlemeli versiyonu |
| **Sandbox (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Her [Dizin/LUT Sandbox](index-lut-sandbox.md) dışa aktarma işlemi başına bir tane |

Aynı dizin adı farklı ayarlarla birden fazla kez yapılandırılırsa, katmanların birbirinden ayırt edilebilmesi için ikinci ve sonraki dizinlerin adına bir sayı eklenir (`RAW (NDVI2 Index)`).

***

## Katman seçiciyi kullanma

1. Izgaradaki bir küçük resme tıklayarak görüntüyü tam ekran olarak açın
2. Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsüne** tıklayın
3. Bir katman seçin — görüntü anında güncellenir

Açılır menüde **JPG, RAW (Orijinal), RAW (Hedef), RAW (Yansıma)** seçenekleri bu sırayla en başta yer alır ve bunların ardından diğer tüm seçenekler ürünlerin kaydedilme sırasına göre listelenir.

### Gezinirken katman tercihi

**←**/**→** tuşlarına basmak sizi bir sonraki resme götürür ve aynı katmanda kalmanızı sağlamaya çalışır:

1. **Önce tam eşleşme** — bir sonraki resimde aynı isimde bir katman varsa, o katman açılır. Bu, tüm seti tek tek incelerken sizi `RAW (NDVI Index)` katmanında tutan özelliktir
2. **Ardından türe göre eşleşme** — bir indeks katmanı herhangi bir indeks katmanını, bir LUT herhangi bir LUT&#x27;u, yansıma herhangi bir yansıma katmanını, hedef herhangi bir hedef katmanını, orijinal herhangi bir orijinal katmanını, temel herhangi bir temel katmanını arar
3. **Ardından, yalnızca dışa aktarma katmanları için** — katman listesi henüz güncellenmemiş olsa bile isim korunur, çünkü dosya diskte zaten mevcuttur. Bu sayede, bir işleme hâlâ ürünleri yazarken bunları inceleyebilirsiniz
4. **Aksi takdirde** — mevcut ilk katman, ki bu genellikle temel görüntüdür

Projedeki `.daq` ve `.csv` sidecar dosyaları, ok tuşlarıyla gezinirken atlanır; böylece görüntüler arasında ilerlerken hiçbir zaman ışık sensörü kaydına gelinmez.

Yakınlaştırma ve kaydırma işlemleri görüntüler arasında da geçerlidir; bu sayede aynı alan konumunun öncesi ve sonrası karşılaştırması kolayca yapılabilir.

***

## Katmanlara göre piksel değerlerini anlama

[İmleç Değerleri paneli](opening-an-image-full-screen.md#cursor-values), imlecinizin bulunduğu kanalın gerçek değerini, o katmanın depolandığı birimde gösterir. Sütunlar, katmana göre değişir:

| Katman | Gösterilen birim | Notlar |
| --- | --- | --- |
| Temel (JPG / PNG / TIFF önizleme) | DN, 0–255 | RGB&#x27;te gama düzeltmesi yapılmış görüntü değerleri. Yalnızca görsel inceleme amaçlı |
| RAW (Orijinal) | DN | Ham sensör dijital değerleri. Histogram ekseni derinliği gösterir: 255 (8 bit), 4095 (12 bit) veya 65535 (16 bit) |
| RAW (Debayered) | DN | Doğrusal, görüntü genişletmesi yok |
| RAW (Önizleme) / Beyaz Dengeli | DN | Görüntü ürünü — genişletilmiş veya gama düzeltmeli. Ölçüm amaçlı değildir |
| RAW (Parlaklık) | **W/m²/sr/nm** | Float32 fiziksel parlaklık. DN sütunu yoktur |
| RAW (Yansıtma) | DN **ve %** | O dosyanın kendi ölçeğiyle hesaplanan yüzde — aşağıya bakın |
| İndeks / LUT / sandbox dışa aktarımları | İndeks değeri veya RGB bileşenleri | Tek kanallı bir indeks dosyası indeks değerini bildirir; renk eşlemeli bir LUT dosyası ise Red/Green/Blue bileşenlerini bildirir |

### Yansıma: ölçek dosyaya özeldir

{% hint style="warning" %}
**&quot;65.535&#x27;e böl&quot; seçeneği yalnızca Survey3 için doğrudur.** LATTICE yansıma oranı farklı bir ölçekte saklanır ve iki bölücüyü karıştırmak, yansıma değerlerinin olması gerekenin tam yarısı olarak hesaplanmasına yol açan en yaygın hatadır.
{% endhint %}

| Kaynak | Yansıma 1,0&#x27;a eşit DN | Tanımlayan |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | Her LATTICE yansıma dışa aktarımına damgalanmış XMP etiketi `Chloros:PixelScale=32768`. 2×&#x27;lik başlık aralığı, 1,0&#x27;ın üzerindeki ρ değerlerinin kırpılmak yerine temsil edilebileceği anlamına gelir |
| **Survey3**|**65535** | Chloros XMP ölçek etiketi yoksa — Survey3 kalibrasyonu, ρ × dtype-max değerini yazar ve 1,0&#x27;da kırpar |

GIS ve komut dosyası oluşturma için: dosyadan `Chloros:PixelScale` değerini okuyun ve buna bölün. Etiket yoksa, dosya Survey3 ölçeğindedir (65535). Görüntüleyici, dizin/LUT sanal alanı ve dizin dışa aktarımı, ölçeği aynı şekilde çözer; bu nedenle imlecin üzerinde okuduğunuz sayı, dizin hesaplamasında kullanılan sayıdır.

Bu ölçeğin üzerine eklenmiş biçime özgü depolama:

* **TIFF (32 bit, Yüzde)**, DN / 65535 değerini float olarak depolar
* **PNG (8 bit)**ve**JPG (8 bit)**, DN × 255 / 65535 değerini depolar
* **8 bit kaynaklı bir yakalamanın 8 bit TIFF olarak dışa aktarılması**, yeniden ölçeklendirilmek yerine 0–255 aralığına kırpılır ve kasıtlı olarak ölçek etiketi taşımaz. Panel, bu dosyalar için yalnızca DN değerini yazdırır; yüzde sütunu yoktur

### İndeks değer aralıkları

| İndeks ailesi | Tipik aralık | Okuma |
| --- | --- | --- |
| Normalleştirilmiş fark (NDVI, GNDVI, NDRE, ENDVI…) | −1 ile +1 arası | Sağlıklı bitki örtüsü genellikle 0,4–0,9; çıplak toprak 0&#x27;a yakın; su negatif |
| Toprağa göre ayarlanmış (SAVI, OSAVI, MSAVI2…) | yaklaşık −1 ile +1,5 arası | Toprak arka planı bastırılmış halde NDVI ile benzer değer |
| Oran (GRVI, GCI, MSR, CIRE…) | üst sınırsız | Payda bandı sıfıra yaklaştıkça oranlar sınırsızca artar |
| EVI / LAI | 0 ila ~1, 0 ila ~3,5 | Bulutlar ve diğer doymuş pikseller her ikisini de aralık dışına çıkarır — önce bunları maskeleyin |

Her ön ayarın arkasındaki kesin formül için [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümüne bakın.

***

## Yaygın iş akışları

### Öncesi / sonrası karşılaştırması

1. **RAW (Orijinal)**&#x27;i seçin ve vinyetlemeyi ve kalibre edilmemiş değerleri not edin
2. **RAW (Yansıma)**&#x27;ye geçin
3. Karşılaştırın — vinyetleme giderildi, değerler kalibre edildi. Yakınlaştırma ve kaydırma sabit kalır, böylece aynı zemine bakarsınız

### Tüm set boyunca bir indeksi inceleyin

1. İşlenmiş ilk görüntüyü açın ve indeks katmanını seçin
2. **→** tuşuna art arda basın — indeks katmanı görüntüden görüntüye sizi takip eder
3. İlerlerken kenar çubuğundaki histogramı izleyin: dağılımında ani sıçrama olan bir kareye daha yakından bakmaya değer

### Kalibrasyon hedeflerini doğrulayın

1. Bir hedef karede **RAW (Hedef)**&#x27;i seçin
2. Hedefin net bir şekilde görülebilir ve algılanabilir olduğunu doğrulayın
3. Bir sonraki hedef kareye geçin — hedef katmanı sizi takip eder

### Yansıma değerlerinin doğruluğunu kontrol edin

1. **RAW (Yansıma)**&#x27;i seçin
2. İmleç Değerleri panelindeki **%** sütununu okuyun — bu sütun, o dosya için zaten doğru şekilde ölçeklendirilmiştir
3. Karedeki bilinen malzemelerle karşılaştırarak doğruluğu kontrol edin: sağlıklı bitki örtüsünde NIR değeri yüksek, kırmızı değeri düşüktür; bir kalibrasyon hedefinin değeri, yayınlanan yansıma değerine yakın olmalıdır

***

## Sorun Giderme

### Beklediğim bir katman açılır menüde yok

**Olası nedenler**

* Görüntü hiç işlenmemiş — sadece temel katman ve `RAW (Original)` mevcut
* Proje Ayarları&#x27;nda ürünün dışa aktarma seçeneği işaretlenmemiş
* Ürün o kameraya uygulanmıyor (RGB ana kamerada radyans ve yansıma; tek bantlı M3M mono kamerada herhangi bir indeks)
* Yansıma kalibrasyonu için kullanılacak veri yoktu — `.daq` aşağı yönlü kapsama alanı yoktu ve QA&#x27;dan geçen çerçeve içi hedef yoktu — bu nedenle çerçeve, Vignette Corrected veya Sensor Response moduna geri döndü

**Ne Yapmalı**

1. İşlemin günlüğünü kontrol edin: Chloros, istenen bir dışa aktarma ürününün ne zaman ve neden imkansız olduğunu belirtir
2. [Proje Ayarları](../project-settings/project-settings.md) içindeki ürün bazında dışa aktarma seçeneklerini kontrol edin
3. Ürün klasörünün proje çıktı ağacında mevcut olduğunu doğrulayın
4. Ürünü etkinleştirerek işlemi yeniden gerçekleştirin

### Katman listesi güncel görünmüyor

Chloros, bir işleme devam ederken projenin ürün klasörlerini yeniden tarar ve eksik katman kayıtlarını diskteki gerçek verilerden düzeltir; bu nedenle, dışa aktarımı normal şekilde tamamlanan bir katman, bir tarama sırasında kendiliğinden görünür. Görüntüden başka bir yere geçip geri dönmek, yeni bir çözümleme yapılmasını sağlar.

### Yansıma değerleri olması gerekenin yarısı kadar görünüyor

Neredeyse kesin olarak bir LATTICE dosyasını 65535&#x27;e bölüyorsunuz. `Chloros:PixelScale` (32768) komutunu kullanın veya bu işlem zaten uygulanmış olan **%** sütununu okuyun.

### Dizin katmanı mevcut ancak görüntü boş

Dizin, katmanınızda bulunmayan bantlara ihtiyaç duyar — örneğin, tek veya iki kanallı bir dosyaya uygulanan üçüncü bir kanalı okuyan bir dizin. Çok bantlı bir katmana (yansıma veya debayered) geçin veya kameranın filtresine uygun bir dizin seçin.

***

## Sonraki Adımlar

* [**Görüntüyü Tam Ekran Olarak Açma**](opening-an-image-full-screen.md) — imleç okuması, histogram ve GSD kontrolü
* [**İndeks/LUT Test Ortamı**](index-lut-sandbox.md) — etkileşimli indeks görselleştirme ve dışa aktarma
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) — indeks referansı
* [**İşlemin Tamamlanması**](../processing-images-gui/finishing-the-processing.md) — bu katmanların işaret ettiği çıktı klasör ağacı
