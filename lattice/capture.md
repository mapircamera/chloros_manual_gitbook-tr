# Çekim Ayarları ve Modları

“Kameralar” sekmesindeki çekim işlemi, tek bir kırmızı **Tümünü Çek**düğmesi ve bu düğmenin ne tür sonuçlar vereceğini belirleyen bir**Çekim Ayarları** bölmesi aracılığıyla gerçekleştirilir: hangi kameraların dahil edileceği, her kameranın hangi dışa aktarma türlerini kaydedeceği ve deklanşörün tek seferlik, sürekli veya aralıklı olarak tetiklenip tetiklenmeyeceği gibi ayarlar bu bölmede belirlenir. Bu sayfa, tüm süreci — yapılandırma, çekim işlemi, dosyaların diskte kaydedileceği konum ve daha sonra bu dosyaların kalibre edilmiş ürünlere nasıl yeniden işleneceği — ayrıntılı olarak açıklamaktadır. Kamera ve dizi kontrolleri ise [Kamera Ayarları](camera-settings.md) sayfasında yer almaktadır.

{% hint style="info" %}
**Çekim yapmak için bir projenin açık olması gerekir.** Bir proje açılana kadar Tümünü Çek ve Çekim Ayarları dişli simgesi devre dışıdır (“Çekimleri kaydetmek için bir proje oluşturun veya açın”). Her çekim, `captures/` içindeki proje klasörü altında kaydedilir.
{% endhint %}

## Yakalama Ayarları bölmesi

Bunu, kenar çubuğundaki kamera listesinde **&quot;Tümünü Yakala&quot;**seçeneğinin yanındaki**dişli simgesiyle**veya herhangi bir kamera ayarları bölmesinin altındaki**&quot;Yakalama Ayarlarını Aç…&quot;** düğmesiyle açabilirsiniz. Başlıkta &quot;Yakalama Ayarları&quot; yazısı ve bir ← geri düğmesi bulunur.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->

Buradaki seçimleriniz — dahil edilen kameralar, tür bazlı onay kutuları ve yakalama modu — **proje bazında** kaydedilir ve projeyi yeniden açtığınızda geri yüklenir.

### Yakalama modları

Pencerenin üst kısmında üç mod düğmesi bulunur:

| Mod | Ne işe yarar | Alt ayarlar (varsayılanlar) |
| --- | --- | --- |
| **Tek** *(varsayılan)* | Seçilen tüm kameralarda tek bir yakalama. | — |
| **Sürekli**| Durdurma koşulu gerçekleşene kadar arka arkaya yakalama. |**Yakalama sayısı** (varsayılan 1) *veya* **Yakalama süresi** (varsayılan 10 s; birimler: saniye / dakika / saat / gün) ile durdurulur. |
| **Aralık**(zaman atlamalı) | Zamanlayıcıya göre seri çekim. |**Çekim sayısı / aralık**(varsayılan 1) ·**Her**N birim (varsayılan 5 saniye) ·**N birim boyunca** (varsayılan 1 dakika). |

Sürekli veya Aralık modunda, çekim devam ederken &quot;Tümünü Çek&quot; düğmesi **Durdur (N)** düğmesine dönüşür ve çekimler gerçekleştiğinde sayar.

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->

### Kameraları ve dışa aktarma türlerini seçme

Pencerenin yardım metni durumu özetler: &quot;Tümünü Yakala&quot;nın hangi kameraları ve dışa aktarma türlerini kullanacağını seçin — varsayılan olarak her şey etkindir ve seçimler bu projeyle birlikte kaydedilir.

* **Tümünü seç / Hiçbirini seçme** düğmeleri, her kameranın dahil et onay kutusunu aynı anda değiştirir.
* **Toplu dışa aktarma türü anahtarları**(iki sıra düğme):**Tüm Raw / Tüm Debayered / Tüm Önizleme / Tüm Radiance / Tüm Yansıma / Tüm İndeks**. Her birinin üç durumlu renk göstergesi vardır: yeşil ✓ = destekleyen tüm kameralar için etkin, sarı – = bazıları için etkin, gri = hiçbiri. Bağlı hiçbir kamera o türü desteklemediğinde anahtar devre dışı kalır. &quot;En Hızlı Yakalama&quot; açıkken hepsi gri renkte görünür.
* **Kamera başına satırlar**: bir dahil et onay kutusu ve o kamera için geçerli dışa aktarım türlerinin ayrı ayrı onay kutuları içeren genişletilebilir (▸/▾) bir liste. Satırda &quot;4/6&quot; gibi bir sayı gösterilir.

### Dışa aktarım türleri ve bunları destekleyen kameralar

Altı dışa aktarım türü mevcuttur: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Her kameranın satırında yalnızca geçerli olanlar görünür:

| Dışa aktarma türü | İçerik | RGB (FRGB) | Bayer multispektral (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Sensörden doğrudan gelen Bayer mozaik (mono: tek bant) | ✓ | ✓ | ✓ |
| **Debayered** | Doğrusal demosaik (mono: 1 kanallı gri tonlamalı) | ✓ | ✓ | ✓ |
| **Önizleme** | Tam görüntüleme zinciri (kameranın profiline göre beyaz dengesi + gama; multispektral: sahte renk genişletme) | ✓ | ✓ | ✓ |
| **Parlaklık** | tam radyometrik zincir üzerinden float32 W/m²/sr/nm | — (sunulmuyor) | ✓ | ✓ |
| **Yansıtma** | uint16 ρ (32768 = 1,0) | — (sunulmamaktadır) | ✓ — yalnızca kamerada bir DAQ ışık sensörü olduğunda gösterilir (kendi sensörü veya dizisinden devralınmış) | multispektral ile aynı |
| **Endeks** | Bitki örtüsü endeksi (LUT) işleme | — | ✓ — kamerada etkinleştirilmiş, boş olmayan bir endeks ifadesi gerektirir ve birleşik dizi üyelerine sunulmaz (dizi, tek bir ortak endekse sahiptir) | — (bir indeks için ≥2 bant gerekir; bkz. [Mono Kameralar ve Bitki Örtüsü İndeksleri](mono-indices.md)) |

RGB kameralar için radyans ve yansıma hiçbir zaman sunulmaz — geniş bant fotometrik sensör için Bayer başına radyans anlamlı değildir.

### En Hızlı Yakalama

**⚡ En Hızlı Yakalama — yalnızca ham**anahtarı (açıkken turuncu renkte), tüm dışa aktarma seçimlerini**yalnızca ham** olarak geçersiz kılar — ayrıca diziler için ücretsiz bir birleşik endeks kompoziti sağlar — böylece kare mümkün olduğunca hızlı bir şekilde kaydedilir: parlaklık/yansıtma/görüntüleme hesaplamaları, yakalama anında tamamen atlanır.

{% hint style="info" %}
**Bir `.daq` dosyası yine de kaydedilir.** Bir ışık sensörü atandığında, En Hızlı Yakalama yine de ham karelerin yanına DAQ aşağı doğru ışık okumasını kaydeder — böylece parlaklık, yansıma ve indeks ürünleri daha sonra yeniden işleme yoluyla oluşturulabilir (bkz. [Yakalamaların yeniden işlenmesi](#re-processing-captures-into-calibrated-products)). Fastest Capture, işaretlediğiniz seçenekleri de korumaktadır: bu özelliği kapatırsanız, seçimleriniz geri gelir.
{% endhint %}

### Dizi başına kontroller

Bağlı her dizi, bölmede kendine ait bir grup kartına sahiptir:

* **Dahil et** onay kutusu (üye arasında üç durumlu) ve dizinin adı ile görüntüleme modu: &quot;(birleştirilmiş | ayrı)&quot;.
* **Hizala**onay kutusu (varsayılan olarak**açık**): üye dışa aktarımlarını dizinin hizalama profiline göre uyarlar, böylece dışa aktarımlar kameralar arasında piksel bazında hizalanır. Ham veriler düzeltilmemiş halde kalır, ancak meta verilerinde dönüşüm bilgisini taşır. (Profilin kendisi [dizi ayarları bölmesinde](camera-settings.md#alignment-co-registration-combined-only) hesaplanır.)
* Üye kamera satırları kartın içine yerleştirilmiştir.

Dizi kartı ayrıca iki kayıt cihazına ev sahipliği yapar. Bunları **izleme ve analiz** olarak düşünün:

| Kayıt Cihazı | Sınıf | Neyi kaydeder |
| --- | --- | --- |
| **● İndeks videosu kaydet / ■ Kaydı durdur** *(yalnızca birleştirilmiş diziler)* | **İzleme** | 10 fps&#x27;de videoya dönüştürülmüş canlı birleştirilmiş indeks kompoziti — 8 bit, önizleme çözünürlüğü, LUT dahil. Açık bir proje ve canlı akış görünümü gerektirir. Kayıt sırasında kareleri ve geçen süreyi gösterir. |
| **⦿ Ham seri kayıt / ■ Ham seri kaydı durdur** *(herhangi bir dizi)* | **Analiz**| Canlı yakalama hızında ham Bayer kareleri (işlenmemiş) artı kare başına manifest ve `.daq` okumaları, `captures/bursts/` formatında. Seri çekimden sonra, bir**Video oluştur** düğmesi görünür: bu düğme, seri çekimi çevrimdışı olarak kalibre edilmiş videoya yeniden işler — birleştirilmiş indeks ve/veya kamera başına parlaklık / yansıma / indeks — artı isteğe bağlı TIFF&#x27;ler. Birleştirilmiş indeks oluşturma işlemi, seri çekimi durdurduğunuzda otomatik olarak başlar. |##

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

Tümünü Yakala akışı

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->

Kenar çubuğundaki kamera listesinden **Tümünü Yakala**&#x27;ya basın:

1. Dahil edilen, görünür ve duraklatılmamış tüm kameralar, seçilen dışa aktarma türleriyle çekim yapar. **Diziler, tek bir senkronize tetikleyici olarak çalışır** (tüm üyeler arasında tek bir senkronize grup — bkz. [Çoklu Kamera Dizileri](arrays.md)); bağımsız kameralar ise ayrı ayrı çekim yapar.
2. Gizli (göz) veya duraklatılmış kameralar atlanır. Bir dizi, yalnızca *tüm* üyeleri gizli veya duraklatılmış olduğunda tamamen engellenir.
3. Bir ışık sensörü atandığında, eşleşen DAQ aşağı doğru ışık okuma değeri, görüntülerle birlikte — yalnızca ham çekimler için bile — bir `.daq` dosyası olarak kaydedilir; böylece radyometrik ürünler daha sonra her zaman türetilebilir.
4. Düğme, canlı ilerleme durumunu gösterir — &quot;Kaydediliyor… tamamlandı/toplam&quot; — ve Sürekli/Aralıklı modda **Dur (N)** haline gelir. Her kayıt öğesinin 300 saniyelik bir zaman aşımı süresi vardır.
5. Geçiş bittiğinde, bir sonuç penceresi **&quot;N dosya kaydedildi&quot;**veya**&quot;N dosya kaydedildi, F başarısız&quot;** mesajını görüntüler; ayrıca kameralar atlandığında &quot;(S gizli/duraklatıldı/atlandı)&quot; ifadesi de eklenir.

## Yakalamaların Kaydedildiği Yer

Yakalamalar, açık projenin altında `<project>/captures/` adıyla kaydedilir. Her dışa aktarma türü **kendi alt klasörüne** yerleştirilir; bu sayede çok düzeyli bir yakalamada türler asla birbirine karışmaz:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── *.daq          the downwelling reading matched to the capture
```

* `<ts>` yakalama zaman damgası, `<serial>` ise kamera seri numarasıdır. Bağımsız yakalamalar `capture_<ts>_SN<serial>_<level>` olarak adlandırılır; senkronize bir tetikleyiciden alınan dizi yakalamaları ise `sync_<ts>_SN<serial>_<level>` olarak adlandırılır ve **gruptaki tüm kameralar için tek bir zaman damgası paylaşılır** (bir kamera yalnızca tek bir seviye kaydettiğinde seviye son eki kaldırılır).
* **Bilmeniz gereken bir asimetri:** görüntüleme seviyesi, `preview/` adlı bir klasörde saklanırken, dosyaların adında `_display` ifadesi kalır — klasör ve sonek yalnızca bu seviye için farklılık gösterir.
* Bilinmeyen seviyeler, kendi adlarını taşıyan bir klasöre kaydedilir; alt klasör oluşturulamazsa dosya kaybolmak yerine yakalama kök dizinine yazılır.
* Yakalama TIFF&#x27;leri varsayılan olarak kayıpsız bir şekilde (DEFLATE) sıkıştırılır ve tam kalibrasyon ve işleme meta verilerini **dosyanın XMP&#x27;si içinde** taşır — yakalamalar kendi kendilerini tanımlar ve `.daq` okuma dosyası dışında herhangi bir yan dosya içermez.

Bu, `chloros-cli lattice capture` / `array-capture` dosyalarının `-o` dizinine yazdığı düzenle aynıdır — bu konu, [CLI Referansı § Bir yakalama klasörü nasıl görünür?](../reference/cli-reference.md#what-a-captures-folder-looks-like) bölümünde belgelenmiştir.

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, a .daq file at the root, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Yakalamaları kalibre edilmiş ürünlere yeniden işleme

Yakalanan ham kareler ve kaydedilen `.daq` dosyaları, işleme boru hattının ihtiyaç duyduğu her şeydir — bu nedenle Fastest Capture, gerçek işler için güvenilirdir.

* **GUI**: yakalama klasörünü bir projeye ekleyin ([Bir Projeye Dosya Ekleme](../processing-images-gui/adding-files-to-a-project.md)) ve her zamanki gibi işleyin.
* **CLI**: `process` dosyasını**yakalama kök dizinine** yönlendirin:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` normalde yalnızca belirttiğiniz klasörü içe aktarır, ancak bu klasörde görüntü bulunmadığında ve alt klasörleri varsa otomatik olarak alt klasörlere iner — böylece seviye alt klasörleri ve kök `.daq` dosyaları tek seferde alınır. Her yakalama, her seviye için ayrı bir görüntü olarak değil, diğer seviyeleri görüntülenebilir modlar olarak eklenmiş **tek bir görüntü** olarak içe aktarılır.

Bir seviye alt klasörünü doğrudan adlandırmak (örn. `…/captures/raw/`) da işe yarar, ancak kök `.daq` dosyaları geride kalır — `raw/`&#x27;ten radyometrik bir ürün yeniden türetirken bunları da yanına kopyalayın, aksi takdirde zaman damgası eşleşmesi karşılaştırılacak bir şey bulamaz.

{% hint style="warning" %}
**İşleme her zaman `raw`&#x27;ten başlar.**Her bir yakalama içinde ham kare, işleme akışının kaynağıdır; `debayered`, `radiance`, `reflectance` ve `preview` görüntülenebilir modlar olarak gelir, ancak iş akışına asla geri beslenmez — türetilmiş bir ürünün yeniden işlenmesi, piksellerine zaten işlenmiş olan vinyet, renk ve parlaklık hesaplamalarını yeniden uygulayacağından, Chloros çift işleme yapmak yerine reddedilir. `index/` ve `composite/` renderları hiçbir zaman işlenmez (bunlar yakalama değil, çıktılardır). Raw içe aktarmaları**olmadan** kaydedilen bir yakalama klasörü normal şekilde görüntülenir, ancak `process` bunu atlar ve bunu belirtir; `--input-level {raw,debayered,processed}` ise bir giriş noktasını zorlayan kasıtlı bir kaçış yoludur. Kesin atlama mesajları için [CLI Referansı](../reference/cli-reference.md#what-a-captures-folder-looks-like) bölümüne bakın.
{% endhint %}

Yeniden işleme komut dosyası yazarken bilinmesi gereken iki davranış daha vardır:

* Ürünleri talep eden ancak **hiçbir görüntü ürünü yazmayan**bir `chloros-cli process` çalıştırması, yüksek sesle hata verir ve sıfırdan farklı bir değerle sonlanır** — hiçbir zaman sessiz bir boş çalıştırma elde edemezsiniz. Başarılı çalıştırmalar, ürün sayılarını bildirir. (Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma da yine de başarı olarak sayılır.)
* Yeniden içe aktarılan işlenmiş dışa aktarımlar asla bir yakalamanın ham verisi yuvasını kullanmaz — orijinal ham veri her zaman iş akışının kaynağı olarak kalır.

## CLI eşdeğerleri

Bu sayfadaki her şey komut satırı ile çalıştırılabilir. GUI yakalama modları doğrudan `chloros-cli lattice array-capture` ile eşleşir:

| GUI | CLI |
| --- | --- |
| Tekli | `chloros-cli lattice array-capture` |
| Sürekli | `array-capture --continuous [--count N] [--duration S]` |
| Aralık | `array-capture --interval S [--duration S]` |
| En Hızlı Yakalama | `array-capture --fastest` |
| Hizalanmış onay kutusu | `--aligned / --no-aligned` |
| Dışa aktarma türü onay kutuları | `--processing LEVEL` veya `--levels L1,L2,…` (varsayılan `all`) |
| Dizin videosu kaydet | `chloros-cli lattice array-record` |
| Ham seri çekim kaydet / Video oluştur | `chloros-cli lattice array-burst` / `array-build-video` |

Tam bayrak tabloları, akıllı AE sabit çekim seçeneği (`--smart`) ve sabit hız modeli, [CLI Referans § Çekim Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess) bölümünde yer almaktadır.
