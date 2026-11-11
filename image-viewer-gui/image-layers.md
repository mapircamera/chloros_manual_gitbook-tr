# Görüntü Katmanları

Görüntü Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsü**, görüntüleyiciden çıkmanıza gerek kalmadan, incelediğiniz görüntünün tüm sürümleri arasında geçiş yapmanızı sağlar — kaynak çekimden, işlenmiş her bir ürün üzerinden, hesaplanmış indeks görüntülere kadar.

## Görüntü Katmanları Nedir?

Chloros'ta bir "katman", bir kaynak görüntüye kayıtlı bir **ürün dosyası**dır. İçe aktarma işlemi size kaynak dosyaları sağlar; işleme ise çalıştırma sırasında oluşturulan her ürün için bir katman ekler. Dışa aktarılan dosyalar kaynak dosya adını korur — ürünü tanımlayan**klasör** budur ve katman adı, Chloros'un o klasör için verdiği etikettir.

<!-- SCREENSHOT-NEEDED: Image Viewer full screen with the layer dropdown open on a processed LATTICE multispectral image, showing the full list: TIFF base, RAW (Original), RAW (Debayered), RAW (Preview), RAW (Radiance), RAW (Reflectance), and one RAW (NDVI Index) entry. -->

***

## Katman listesi

### Her zaman mevcut

| Katman | Nedir |
| --- | --- |
| **JPG**(veya**PNG**/**TIFF**) | Yakalama işlemiyle gelen temel dosya. Survey3, her `.RAW` dosyasının yanına bir `.JPG` dosyası içe aktarır; LATTICE yakalamaları ise bir PNG veya TIFF görüntü önizlemesi getirir. Gerçekte neyin içe aktarıldığına göre etiketlenir |
| **RAW (Orijinal)** | Herhangi bir düzeltme uygulanmadan görüntülenmek üzere debayering işlemi uygulanmış kaynak ham kare. İçe aktarım anından itibaren kullanılabilir — işleme gerektirmez |

Temel dosyası ham karesi **olan** bir LATTICE yakalaması için ayrı bir temel girişi yoktur: `RAW (Original)` bunu zaten kapsamaktadır.

### Survey3'ün işleme ürünleri

| Katman | Kaydedildiği yer | Ne zaman mevcut olur |
| --- | --- | --- |
| **RAW (Hedef)** | — | Çerçevenin bir kalibrasyon hedefi içerdiği tespit edildi |
| **RAW (Yansıtma)** | `Reflectance_Calibrated_Images/` | Bu çerçevede yansıtma kalibrasyonu başarıyla gerçekleştirildi |
| **Vinyet Düzeltmeli**| `Vignette_Corrected_Images/` | Kare için yansıma kalibrasyonu yapılamadı**ve** *Vinyet düzeltme* açıktı |
| **Sensör Tepkisi**| `Sensor_Response_Images/` | Kareye yansıma kalibrasyonu yapılamadı**ve** *Vignette düzeltmesi* kapalıydı |
| **Beyaz Dengesi Ayarlandı** | `White_Balanced_Images/` | Beyaz dengesi ayarlanmış bir ürün kaydedildi |

{% hint style="info" %}
**Vignette Düzeltme ve Sensör Tepkisi birbirinin alternatifi olup, asla ikisi birden etkin olamaz.** Her kamera modeli için her işleme turunda tam olarak bir adet kalibre edilmemiş yedek ürün bulunur ve *Vignette düzeltme* anahtarı hangisinin kullanılacağını seçer. Bkz. [Proje Ayarları](../project-settings/project-settings.md).
{% endhint %}

### LATTICE seviyeleri

LATTICE, tek bir işleme aşamasında bunları fan out olarak yakalar. Hangi seviyelerin mevcut olduğu, Proje Ayarları'ndaki ürün başına dışa aktarma seçeneklerine ve kameraya neyin geçerli olduğuna bağlıdır.

| Katman | Kaydedildiği yer | Uygulandığı |
| --- | --- | --- |
| **RAW (Debayered)** | `Debayered_Images/` | RGB ve multispektral |
| **RAW (Önizleme)** | `Preview_Images/` | Multispektral (yanlış renk uzama) |
| **Beyaz Dengeli** | `Preview_Images/` | RGB ana kameralar — RGB önizleme, aynı isimli Survey3 katmanıyla uyumlu olması için bu isim altında kaydedilir |
| **RAW (Parlaklık)** | `Radiance_Images/` | Yalnızca multispektral |
| **RAW (Yansıtma)** | `Reflectance_Calibrated_Images/` | Yalnızca multispektral ve yalnızca eşleşen bir `.daq` aşağı doğru ışınım kaydı veya kalite kontrolünden geçmiş bir çerçeve içi hedef çerçeveyi kapladığında |

RGB ana kameralarda bant başına radyometri bulunmadığından, parlaklık ve yansıma değerleri **uygulanamaz** olarak atlanır — günlük dosyasında bu durum, sessiz bir hata yerine açıkça belirtilir.

{% hint style="info" %}
**Ham ve debayered katmanlar ürünlerle hizalanır (1.3.0 sürümünde yeni).**LATTICE ham ve debayered dosyaları sensörün doğal yöneliminde kaydedilir ve kendilerine ait yönelimi XMP'ye kaydeder; diğer ürünler ise bu yönelim zaten uygulanmış olarak kaydedilir. Görüntüleyici ve görüntü ızgarası, görüntüleme amacıyla**RAW (Orijinal)**ve**RAW (Debayered)** katmanlarını bu kayda göre döndürür; böylece bir ürün katmanı ile ham katman arasında geçiş yaptığınızda kare artık ters dönmez. Bu yalnızca görüntülemeyle ilgilidir — dosyalar asla yeniden yazılmaz. Yön kaydı olmayan dosyalar (1.3.0 sürümünden önce çekilmiş veya başka bir yazılım tarafından yazılmış olanlar) kaydedildikleri şekilde gösterilir. Bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md#orientation).
{% endhint %}

Sıkıştırma maskeleri (`*_clampmask.tif` klasöründeki `clamp_masks/` dosyası) katman değildir: Chloros, içe aktarma sırasında bunları atlar. Bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md#clamp-masks).

### Dizin, LUT ve sandbox katmanları

| Katman türü | Örnek | Kaynağı |
| --- | --- | --- |
| **RAW (`<INDEX>` Dizin)** | `RAW (NDVI Index)` | Proje Ayarları'nda yapılandırılan her dizin için bir tane, işleme sırasında hesaplanır |
| **`<INDEX>` LUT** | `NDVI LUT` | Bir indeksin renk eşlemeli versiyonu |
| **Sandbox (`<Name>` `<Index\|LUT>` `<NNN>`)** | `Sandbox (NDVI LUT 003)` | Her [Dizin/LUT Sandbox](index-lut-sandbox.md) dışa aktarma işlemi |

Aynı dizin adı farklı ayarlarla birden fazla kez yapılandırılırsa, katmanların birbirinden ayırt edilebilmesi için ikinci ve sonraki dizinlerin adına bir sayı eklenir (`RAW (NDVI2 Index)`).

***

## Katman seçiciyi kullanma

1. Izgaradaki bir küçük resme tıklayarak bir görüntüyü tam ekran açın
2. Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsüne** tıklayın
3. Bir katman seçin — görüntü anında güncellenir

Açılır menüde **JPG, RAW (Orijinal), RAW (Hedef), RAW (Yansıma)** seçenekleri bu sırayla ilk sırada yer alır ve bunların ardından diğer tüm seçenekler, ürünlerin kaydedildiği sıraya göre listelenir.

### Gezinirken katman tercihi

**←**/**→** tuşlarına basmak bir sonraki resme geçer ve sizi aynı katmanda tutmaya çalışır:

1. **Önce tam eşleşme** — bir sonraki resimde aynı adlı bir katman varsa, o katman açılır. Bu, tüm seti tek tek incelerken sizi `RAW (NDVI Index)` katmanında tutan özelliktir
2. **Ardından türe göre eşleşme** — bir indeks katmanı herhangi bir indeks katmanını, bir LUT herhangi bir LUT'u, yansıma herhangi bir yansıma katmanını, hedef herhangi bir hedef katmanını, orijinal herhangi bir orijinal katmanını, temel herhangi bir temel katmanını arar
3. **Ardından, yalnızca dışa aktarma katmanları için** — katman listesi henüz güncellenmemiş olsa bile isim korunur, çünkü dosya diskte zaten mevcuttur. Bu sayede, bir işleme hâlâ ürünleri yazarken bunları inceleyebilirsiniz
4. **Aksi takdirde** — mevcut ilk katman, ki bu genellikle temel görüntüdür

Projedeki `.daq` ve `.csv` sidecar dosyaları, ok tuşlarıyla gezinirken atlanır; böylece görüntüler arasında ilerlerken hiçbir zaman bir ışık sensörü kaydına rastlanmaz.

Yakınlaştırma ve kaydırma işlemleri görüntüler arasında da geçerlidir; bu sayede aynı alan konumunun öncesi ve sonrası karşılaştırması kolayca yapılabilir.

***

## Katmanlara göre piksel değerlerini anlama

[İmleç Değerleri paneli](opening-an-image-full-screen.md#cursor-values), imlecinizin altındaki gerçek kanal başına değeri, katmanın depolandığı birimde gösterir. Sütunları katmana göre değişir:

| Katman | Gösterilen birim | Notlar |
| --- | --- | --- |
| Temel (JPG / PNG / TIFF önizleme) | DN, 0–255 | RGB üzerinde gama düzeltmesi yapılmış görüntü değerleri. Yalnızca görsel inceleme amaçlı |
| RAW (Orijinal) | DN | Sensörün ham dijital değerleri. Histogram ekseni derinliği gösterir: 255 (8 bit), 4095 (12 bit) veya 65535 (16 bit) |
| RAW (Debayered) | DN | Doğrusal, görüntü genişletme yok |
| RAW (Önizleme) / Beyaz Dengeli | DN | Görüntü ürünü — genişletilmiş veya gama düzeltmeli. Ölçüm amaçlı değildir |
| RAW (Işınım) | **W/m²/sr/nm** | Float32 fiziksel ışınım. DN sütunu yoktur |
| RAW (Yansıma) | DN **ve %** | O dosyanın kendi ölçeğiyle hesaplanan yüzde — aşağıya bakın |
| Dizin / LUT / sandbox dışa aktarımları | Dizin değeri veya RGB bileşenleri | Tek kanallı bir dizin dosyası dizin değerini bildirir; renk eşlemeli bir LUT dosyası ise Red /Green /Blue bileşenlerini bildirir |

### Yansıtma: ölçek dosya bazındadır

{% hint style="warning" %}
**"65.535'e böl" seçeneği yalnızca Survey3 için doğrudur.** LATTICE yansıtma değeri farklı bir ölçekte saklanır ve bu iki bölücüyü karıştırmak, yansıtma değerlerinin olması gerekenin tam yarısı kadar çıkmasına neden olan en yaygın hatadır.
{% endhint %}

| Kaynak | Yansıma değeri 1,0'a eşit DN | Tanımlayıcı |
| --- | --- | --- |
| **LATTICE**(M3C / M3M) |**32768** | Her LATTICE yansıma dışa aktarımına damgalanan `Chloros:PixelScale=32768` XMP etiketi. 2× başlık payı, 1,0'ın üzerindeki ρ değerlerinin kırpılmak yerine temsil edilebileceği anlamına gelir |
| **Survey3**| **65535** | Chloros XMP ölçek etiketi yoktur — Survey3 kalibrasyonu ρ × dtype-max değerini yazar ve 1,0'da kırpar |

GIS ve komut dosyası oluşturma için: dosyadan `Chloros:PixelScale` değerini okuyun ve buna bölün. Etiket yoksa, dosya Survey3 ölçeğindedir (65535). Görüntüleyici, dizin/LUT sanal alanı ve dizin dışa aktarımı, ölçeği aynı şekilde çözümler; dolayısıyla imlecin üzerinde okuduğunuz sayı, dizin hesaplamasında kullanılan sayıdır.

Bu ölçeğin üzerine eklenmiş biçime özgü depolama:

* **TIFF (32-bit, Yüzde)**, DN / 65535 değerini float olarak depolar
* **PNG (8-bit)**ve**JPG (8-bit)**, DN × 255 / 65535 değerini depolar
* **8 bitlik bir yakalama işleminin 8 bitlik DN ürünü** veya 1.3.0 sürümünden önce yazılmış bir yansıma dosyası, 0–255 aralığına kırpılır ve ölçek etiketi taşımaz. Panel, bu dosyalar için yalnızca DN değerini gösterir; yüzde sütunu bulunmaz

### İndeks değer aralıkları

| İndeks ailesi | Tipik aralık | Okuma değeri |
| --- | --- | --- |
| Normalleştirilmiş fark (NDVI, GNDVI, NDRE, ENDVI…) | −1 ile +1 arası | Sağlıklı bitki örtüsü genellikle 0,4–0,9; çıplak toprak 0 civarında; su negatif |
| Toprağa göre ayarlanmış (SAVI, OSAVI, MSAVI2…) | yaklaşık −1 ile +1,5 arası | Toprak arka planı bastırılmış halde NDVI’ye benzer değerler |
| Oran (GRVI, GCI, MSR, CIRE…) | üst sınırsız | Payda bandı sıfıra yaklaştıkça oranlar sınırsızca artar |
| EVI / LAI | 0 ile ~1, 0 ile ~3,5 arası | Bulutlar ve diğer doymuş pikseller her ikisini de aralık dışına çıkarır — önce bunları maskeleyin |

Her ön ayarın arkasındaki kesin formül için [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümüne bakın.

***

## Yaygın iş akışları

### Öncesi / sonrası karşılaştırması

1. **RAW (Orijinal)**'i seçin ve vinyetlemeyi ve kalibre edilmemiş değerleri not edin
2. **RAW (Yansıtma)** seçeneğine geçin
3. Karşılaştırın — vinyetleme giderildi, değerler kalibre edildi. Yakınlaştırma ve kaydırma sabit kalır, böylece aynı zemine bakarsınız

### Tüm set boyunca bir indeksi inceleyin

1. İşlenmiş ilk görüntüyü açın ve indeks katmanını seçin
2. **→** tuşuna art arda basın — indeks katmanı görüntüden görüntüye sizinle birlikte hareket eder
3. İşlem sırasında kenar çubuğundaki histogramı izleyin: dağılımında ani sıçrama olan bir kareye daha yakından bakmaya değer

### Kalibrasyon hedeflerini doğrulama

1. Bir hedef karede **RAW (Hedef)**'i seçin
2. Hedefin net bir şekilde görülebilir ve algılanabilir olduğunu doğrulayın
3. Bir sonraki hedef kareye geçin — hedef katmanı sizi takip eder

### Yansıma değerlerinin doğruluğunu kontrol edin

1. **RAW (Yansıma)**'i seçin
2. İmleç Değerleri panelindeki **%** sütununu okuyun — bu sütun, söz konusu dosya için zaten doğru şekilde ölçeklendirilmiştir
3. Karedeki bilinen malzemelerle karşılaştırarak doğruluğu kontrol edin: sağlıklı bitki örtüsünde NIR değeri yüksek, kırmızı değeri düşüktür; bir kalibrasyon hedefinin okunan değeri, yayınlanan yansıma değerine yakın olmalıdır

***

## Sorun Giderme

### Beklediğim bir katman açılır menüde yok

**Olası nedenler**

* Görüntü hiç işlenmemiş — sadece temel ve `RAW (Original)` mevcut
* Proje Ayarları'nda ürünün dışa aktarma seçeneği işaretli değil
* Ürün o kameraya uygulanmıyor (RGB ana görüntüde radyans ve yansıma; tek bantlı M3M mono kamerada herhangi bir indeks)
* Yansıma kalibrasyonu için kullanılacak veri yoktu — `.daq` aşağı yönlü kapsama alanı yoktu ve QA'dan geçen çerçeve içi hedef yoktu — bu nedenle çerçeve, Vignette Corrected veya Sensor Response moduna geri döndü

**Ne Yapmalı**

1. İşlemin günlüğünü kontrol edin: Chloros, istenen bir dışa aktarım ürününün ne zaman ve neden imkansız olduğunu belirtir
2. [Proje Ayarları](../project-settings/project-settings.md) bölümündeki ürün bazlı dışa aktarım seçeneklerini kontrol edin
3. Ürün klasörünün proje çıktı ağacında mevcut olduğunu doğrulayın
4. Ürünü etkinleştirerek yeniden işleyin

### Katman listesi güncel görünmüyor

Chloros, bir işleme devam ederken projenin ürün klasörlerini yeniden tarar ve eksik katman kayıtlarını diskteki gerçek verilerden düzeltir; bu nedenle, dışa aktarımı normal şekilde tamamlanan bir katman, bir yoklamada kendiliğinden görünür. Görüntüden başka bir yere geçip geri dönmek, yeni bir çözümleme yapılmasını sağlar.

### Yansıma değerleri olması gerekenin yarısı kadar görünüyor

Neredeyse kesin olarak bir LATTICE dosyasını 65535'e bölüyorsunuz. `Chloros:PixelScale` (32768) kullanın veya bu değerin zaten uygulanmış olduğu **%** sütununu okuyun.

### Dizin katmanı mevcut ancak görüntü boş

Dizin, katmanınızda bulunmayan bantlara ihtiyaç duyar — örneğin, bir veya iki kanallı bir dosyaya uygulanan üçüncü bir kanalı okuyan bir dizin. Çok bantlı bir katmana (yansıma veya debayered) geçin veya kameranın filtresine uygun bir dizin seçin.

***

## Sonraki Adımlar

* [**Görüntüyü Tam Ekranda Açma**](opening-an-image-full-screen.md) — imleç okuma, histogram ve GSD kontrolü
* [**İndeks/LUT Deneme Ortamı**](index-lut-sandbox.md) — etkileşimli indeks görselleştirme ve dışa aktarma
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) — indeks referansı
* [**İşlemeyi Tamamlama**](../processing-images-gui/finishing-the-processing.md) — bu katmanların işaret ettiği çıktı klasör ağacı
