# Çekim Ayarları ve Modları

“Kameralar” sekmesindeki çekim işlemi, tek bir kırmızı **Hepsini Çek**düğmesi ve bu düğmenin ne tür sonuçlar vereceğini belirleyen bir**Çekim Ayarları** bölmesi aracılığıyla gerçekleştirilir: hangi kameraların dahil olacağı, her kameranın hangi dışa aktarma türlerini kaydedeceği ve deklanşörün tek seferlik, sürekli veya belirli aralıklarla tetiklenip tetiklenmeyeceği gibi ayarlar bu bölmede belirlenir. Bu sayfa, yapılandırma, çekim işlemi, dosyaların diskte nereye kaydedileceği ve daha sonra bunların kalibre edilmiş ürünlere nasıl yeniden işleneceği dahil olmak üzere tüm akışı açıklamaktadır. Kamera ve dizi kontrolleri ise [Kamera Ayarları](camera-settings.md) sayfasında yer almaktadır.

{% hint style="info" %}
**Çekim yapmak için açık bir proje gerekir.** Bir proje açılana kadar &quot;Tümünü Yakala&quot; ve &quot;Yakalama Ayarları&quot; dişli simgesi devre dışıdır (&quot;Yakalamaları kaydetmek için bir proje oluşturun veya açın&quot;). Her yakalama, `captures/` içindeki proje klasörü altında kaydedilir.
{% endhint %}

## Yakalama Ayarları bölmesi

Bunu, kenar çubuğundaki kamera listesinde **&quot;Tümünü Yakala&quot;**seçeneğinin yanındaki**dişli simgesiyle**veya herhangi bir kamera ayarları bölmesinin altındaki**&quot;Yakalama Ayarlarını Aç…&quot;** düğmesiyle açabilirsiniz. Başlıkta &quot;Yakalama Ayarları&quot; yazmaktadır ve bir ← geri düğmesi bulunmaktadır.

<!-- SCREENSHOT-NEEDED: the full Capture Settings pane — Single/Continuous/Interval mode buttons at top, the bulk export-type toggle rows (All Raw … All Index), the orange Fastest Capture toggle, an array group card with the Aligned checkbox and Record buttons, and an expanded per-camera row showing per-type checkboxes. -->



Buradaki seçimleriniz — dahil edilen kameralar, tür bazlı onay kutuları ve yakalama modu — **proje bazında** kaydedilir ve projeyi yeniden açtığınızda geri yüklenir.

### Yakalama modları

Pencerenin üst kısmında üç mod düğmesi bulunur:

| Mod | Ne işe yarar | Alt ayarlar (varsayılanlar) |
| --- | --- | --- |
| **Tek** *(varsayılan)* | Seçilen tüm kameralarda tek bir çekim. | — |
| **Sürekli**| Durma koşulu gerçekleşene kadar arka arkaya çekimler. |**Yakalama sayısı** (varsayılan 1) *veya* **Yakalama süresi** (varsayılan 10 s; birimler: saniye / dakika / saat / gün) ile durdurulur. |
| **Aralık**(zaman atlamalı) | Zamanlayıcıya göre seri çekim yapar. |**Yakalama sayısı / aralık**(varsayılan 1) ·**Her**N birim (varsayılan 5 s) ·**N birim boyunca** (varsayılan 1 m). |

Sürekli veya Aralık modunda, Tümünü Yakala düğmesi çalışma sırasında **Durdur (N)** düğmesine dönüşür ve çekimler geldikçe sayar. Bir sürücünün tamamen dolması, çalışmayı durdurur (bkz. [Tümünü Yakala akışı](#the-capture-all-flow)).**Dizilerdeki Sürekli mod, arka planda çalışır (1.3.0 sürümünde eklendi).** Listedeki her kamera bir diziye ait olduğunda (bağımsız kameralar gizlenmiş veya hariç tutulmuşsa), Sürekli mod artık her yakalama için bir istek göndermez: Chloros

arka ucu yakalama döngüsünü kendisi çalıştırır, bir senkronize grubu yazarken bir sonrakini seçer ve düğme sayısını sorgular. Listede bağımsız kameralar varsa, Chloros

eskisi gibi geçiş bazında yakalama yapar.

{% hint style="warning" %}
**Windows

&#x27;da hızlı yakalama: Chloros

arka ucunu Microsoft Defender&#x27;dan hariç tutun.** MAPIR

&#x27;ın testlerinde, yüksek hızda yakalamanın yazdığı her dosyanın gerçek zamanlı taranması, yakalama hızının yaklaşık %30&#x27;una mal oluyor (küçük bir ROI&#x27;da saniyede yüzlerce dosya). Windows

yükleyicisi, varsayılan olarak işaretli olan **&quot;Chloros

arka ucunu Microsoft Defender taramasından hariç tut (çok daha hızlı kamera yakalama; önerilir)&quot;** seçeneğini sunar ve program kaldırıldığında bu hariç tutmayı kaldırır. Bu seçeneğin işaretini kaldırdıysanız, yükleyiciyi bu seçenek işaretli olarak yeniden çalıştırın veya `chloros-backend.exe` için (Chloros

kurulum klasöründeki `backend` klasöründe bulunan) bir Defender işlem hariç tutma kuralı ekleyin. Defender, bir dizinin Sürekli yakalama işlemi sırasında bir veya daha fazla CPU çekirdeği kullandığında, Chloros

arka ucu bunu günlüğüne bir kez kaydeder ve yine aynı iki çözüm yolunu sunar.
{% endhint %}

<!-- SCREENSHOT-NEEDED: the capture-mode area of Capture Settings with Interval selected — showing the "Captures / interval", "Every N (unit)" and "For N (unit)" rows with their defaults (1, 5 s, 1 m). -->




### Kameraları ve dışa aktarma türlerini seçme

Pencerenin yardım metni durumu şöyle özetler: &quot;Tümünü Yakala&quot; seçeneğinin hangi kameraları ve dışa aktarma türlerini üreteceğini seçin — varsayılan olarak her şey açıktır ve seçimler bu projeyle birlikte kaydedilir.

* **Tümünü seç / Hiçbirini seçme** düğmeleri, her kameranın dahil etme onay kutusunu tek seferde değiştirir.
* **Toplu dışa aktarma türü anahtarları**(iki sıra düğme):**Tüm Raw / Tüm Debayered / Tüm Önizleme / Tüm Radiance / Tüm Yansıma / Tüm İndeks**. Her birinin üç durumlu renk göstergesi vardır: yeşil ✓ = destekleyen tüm kameralar için etkin, sarı – = bazıları için etkin, gri = hiçbiri. Bağlı hiçbir kamera o türü desteklemediğinde anahtar devre dışı kalır. En Hızlı Yakalama açıkken hepsi gri renkte görünür.
* **Kamera başına satırlar**: bir dahil et onay kutusu ve o kameraya uygulanabilir dışa aktarma türlerinin ayrı ayrı onay kutuları içeren genişletilebilir (▸/▾) bir liste. Satırda &quot;4/6&quot; gibi bir etkin durum sayısı gösterilir.

### Dışa aktarım türleri ve bunları destekleyen kameralar

Altı dışa aktarım türü mevcuttur: **Raw, Debayered, Radiance, Reflectance, Preview, Index**. Her kameranın satırında yalnızca geçerli olanlar görünür:

| Dışa aktarma türü | İçerik | RGB

(FRGB) | Bayer multispektral (FRGN/FOCN/FNGB) | Mono (M3M) |
| --- | --- | --- | --- | --- |
| **Raw** | Sensörden doğrudan gelen Bayer mozaiği (mono: tek bant) | ✓ | ✓ | ✓ |
| **Debayered** | Doğrusal demosaik: ham veriden kalibrasyon paketinin karanlık ofseti (DSNU) kaldırılır, ardından kenar duyarlı demosaik uygulanır — doğrusallık düzeltmesi, düz alan veya renk ayrıştırma yapılmaz. 3 kanallı RGB

TIFF

olarak kaydedilir (mono: 1 kanallı gri tonlamalı) | ✓ | ✓ | ✓ |
| **Önizleme** | Tam görüntüleme zinciri (kameranın profiline göre beyaz dengesi + gama, ayrıca RGB

kameranın Vurgular ayarı; multispektral: sahte renk genişletme) | ✓ | ✓ | ✓ |
| **Işınım** | tam radyometrik zincir aracılığıyla float32 W/m²/sr/nm | — (sunulmamaktadır) | ✓ | ✓ |
| **Yansıtma** | uint16 ρ (32768 = 1,0) | — (sunulmamaktadır) | ✓ — yalnızca kamerada bir DAQ ışık sensörü olduğunda gösterilir (kendi sensörü veya dizisinden devralınan) | multispektral ile aynı |
| **Endeks** | Bitki örtüsü endeksi (LUT) işleme | — | ✓ — kamerada etkinleştirilmiş, boş olmayan bir indeks ifadesi gerektirir ve birleşik dizi üyelerine sunulmaz (dizi, tek bir paylaşılan indekse sahiptir) | — (bir indeks için ≥2 bant gerekir; bkz. [Mono Kameralar ve Bitki Örtüsü İndeksleri](mono-indices.md)) |

RGB

kameralar için radyans ve yansıma hiçbir zaman sunulmaz — geniş bantlı fotometrik sensörler için Bayer başına radyans anlamlı değildir.

### En Hızlı Yakalama

**⚡ En Hızlı Yakalama — yalnızca ham**anahtarı (açıkken turuncu), tüm dışa aktarma seçimlerini**yalnızca ham** olarak geçersiz kılar — ayrıca diziler için ücretsiz bir birleşik indeks kompoziti sağlar — böylece kare mümkün olduğunca hızlı bir şekilde elde edilir: parlaklık/yansıtma/görüntüleme hesaplamaları yakalama sırasında tamamen atlanır.

{% hint style="info" %}
**Bir `.daq` dosyası yine de kaydedilir.** Bir ışık sensörü atandığında, En Hızlı Yakalama yine de DAQ aşağı doğru ışık okumasını (yakalamaların `daq/` klasörüne) yazar — böylece parlaklık, yansıma ve indeks ürünleri daha sonra yeniden işleme yoluyla oluşturulabilir (bkz. [Yakalamaları yeniden işleme](#re-processing-captures-into-calibrated-products)). Fastest Capture, onay kutusu seçimlerinizi de etkilemez: bu özelliği kapatırsanız seçimleriniz geri gelir.
{% endhint %}

### Dizi başına kontroller

Bağlı her dizi, bölmede kendine ait bir grup kartına sahiptir:

* **Dahil et** onay kutusu (üye arasında üç durumlu) ve dizinin adı ile görüntüleme modu: &quot;(birleştirilmiş | ayrı)&quot;.
* **Hizala**onay kutusu (varsayılan olarak**açık**): üye dışa aktarımlarını dizinin hizalama profiline göre dönüştürür, böylece dışa aktarımlar kameralar arasında piksel bazında hizalanır. Ham veriler warp işlemine tabi tutulmaz, ancak meta verilerinde dönüşüm bilgisini taşır. (Profilin kendisi [dizi ayarları bölmesinde](camera-settings.md#alignment-co-registration) hesaplanır.) Dizi üye dosyaları, her kameranın doğal yöneliminde kaydedilir; Ayna/Dönüş ayarları uygulanmak yerine dosyaya kaydedilir.
* Üye kamera satırları kartın içine yerleştirilir.

Dizi kartı ayrıca iki kayıt cihazına da ev sahipliği yapar. Bunları **izleme ve analiz** olarak düşünün:

| Kayıt Cihazı | Sınıf | Neyi kaydeder |
| --- | --- | --- |
| **● İndeks videosu kaydet / ■ Kaydı durdur** *(yalnızca birleştirilmiş diziler)* | **İzleme** | 10 fps&#x27;de videoya dönüştürülmüş canlı birleştirilmiş indeks kompoziti — 8 bit, önizleme çözünürlüğü, LUT dahil. Açık bir proje ve canlı akış görünümü gerektirir. Kayıt sırasında kareleri ve geçen süreyi gösterir. |
| **⦿ Ham seri kayıt / ■ Ham seri kaydı durdur** *(herhangi bir dizi)* | **Analiz**| Canlı yakalama hızında ham Bayer kareleri (işlenmemiş) artı kare başına manifest ve `.daq` okumaları, `captures/bursts/` formatında. Seri çekimden sonra, bir**Video Oluştur** düğmesi belirir: bu düğme, seri çekimi çevrimdışı olarak kalibre edilmiş videoya — birleştirilmiş indeks ve/veya kamera başına parlaklık / yansıma / indeks — artı isteğe bağlı TIFF&#x27;ler halinde yeniden işler. Birleştirilmiş indeks oluşturma işlemi, seri çekimi durdurduğunuzda otomatik olarak başlar. |

<!-- SCREENSHOT-NEEDED: an array group card in Capture Settings while a raw burst is recording — the ⦿/■ burst button in its recording state with frame count, and (in a second capture) the Build video button that appears after stopping. -->

## Tümünü Yakala akışı

<!-- SCREENSHOT-NEEDED: the sidebar during a capture — Capture All showing live "Capturing… 3/6" progress text, and (second capture) the result flash "Saved N files". -->



Kenar çubuğundaki kamera listesinde **Tümünü Yakala** düğmesine basın:

1. Dahil edilen, görünür ve duraklatılmamış tüm kameralar, seçilen dışa aktarma türleriyle çekim yapar. **Diziler, tek bir senkronize tetikleyici olarak çalışır** (tüm üyeler arasında tek bir senkronize grup — bkz. [Çoklu Kamera Dizileri](arrays.md)); bağımsız kameralar ise ayrı ayrı çekim yapar.
2. Gizli (göz) veya duraklatılmış kameralar atlanır. Bir dizi, ancak *tüm* üyeleri gizli veya duraklatılmış olduğunda tamamen engellenir.
3. Bir ışık sensörü atandığında, eşleşen DAQ aşağı doğru ışık okuma değeri, yakalamaların `daq/` klasörüne bir `.daq` dosyası olarak kaydedilir — yalnızca ham verili yakalamalar için bile — böylece radyometrik ürünler daha sonra her zaman türetilebilir.
4. Düğme, canlı ilerleme durumunu gösterir — &quot;Yakalanıyor… tamamlandı/toplam&quot; — ve Sürekli/Aralıklı modda **Durdur (N)** haline gelir. Her yakalama öğesinin 300 saniyelik bir zaman aşımı süresi vardır.
5. Geçiş bittiğinde, bir sonuç mesajı **&quot;N dosya kaydedildi&quot;**veya**&quot;N kaydedildi, F başarısız&quot;**şeklinde görüntülenir; ayrıca kameralar atlandığında &quot;(S gizli/duraklatıldı/atlandı)&quot; ifadesi de eklenir. Sürücü dolarsa, işlem durur ve**&quot;Durduruldu: sürücü dolu. N dosya kaydedildi, F başarısız&quot;** mesajı görüntülenir — yazılmayan her dosya başarısız olarak sayılır.

## Yakalamaların kaydedildiği yer

Yakalamalar, açık projenin altında `<project>/captures/` dizininde kaydedilir. Her dışa aktarma türü **kendi alt klasörüne** yerleştirilir, bu nedenle çok seviyeli bir yakalamada türler asla karışmaz:

```
<project>/captures/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when Index is selected
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_clampmask.tif, reflectance/…, index/… — unmix clamp masks,
│                  one subfolder per product folder, only when a band was clamped
├── bursts/        raw-burst recordings (frames + manifest + .daq per burst)
└── daq/           the downwelling reading(s) matched to each capture (.daq), each with a
                   .daq.cameras.json naming the cameras it describes
```

* `<ts>`, yakalama zaman damgasıdır; `<serial>` ise kamera seri numarasıdır. Bağımsız yakalamalar `capture_<ts>_SN<serial>_<level>` olarak adlandırılır; senkronize edilmiş tek bir tetikleyiciden alınan dizi yakalamaları ise `sync_<ts>_SN<serial>_<level>` olarak adlandırılır ve **gruptaki tüm kameralar için tek bir zaman damgası paylaşır** (bir kamera yalnızca tek bir seviye kaydettiğinde seviye soneki atlanır).
* **Bilmeniz gereken bir asimetri:** görüntüleme seviyesi, `preview/` adlı bir klasörde saklanırken, dosyaların adında `_display` kalır — klasör ve sonek yalnızca bu seviye için farklılık gösterir.
* Bilinmeyen seviyeler, kendi adlarını taşıyan bir klasöre kaydedilir; bir alt klasör oluşturulamıyorsa, dosya kaybolmak yerine yakalama kök dizinine yazılır.
* Yakalama TIFF&#x27;leri varsayılan olarak kayıpsız olarak sıkıştırılır (DEFLATE) ve tam kalibrasyon ve işleme meta verilerini **dosyanın XMP&#x27;si içinde** taşır — yakalamalar kendi kendilerini tanımlar; `.daq` dosyası (ve buna ait `.daq.cameras.json` dosyası) dışında herhangi bir yardımcı dosya içermez.
* **Kısma maskeleri (1.3.0 sürümünde yeni).**3×3 spektral ayrıştırmanın bir bandı en az bir pikselde 0&#x27;a kısması gereken durumlarda, parlaklık, yansıma ve indeks ürünleri piksel başına bir maske alır: `clamp_masks/<product folder>/<product name>_clampmask.tif` (bit 0 = ürünün R kanalındaki bant, bit 1 = G, bit 2 = B); ürünün `Chloros:UnmixClampMask` XMP etiketi bu maskeyi işaret eder. Maskeler, ürün klasörlerinin**yanında** bulunur, asla içlerinde değil; bu nedenle `reflectance/` gibi bir ürün klasörü, olduğu gibi fotogrametri yazılımına aktarılabilir. Chloros

’un kendi içe aktarma işlevi maskeleri atlar.
* **Yönlendirme (1.3.0).** Bağımsız bir kameranın Önizleme, Işılma, Yansıtma ve İndeks ürünleri, Ayna/Dönüş ayarıyla döndürülür; Raw ve Debayered dosyaları ise sensörün okuduğu haliyle kalır. Dizi üye dosyalarının tümü orijinal biçiminde kalır. Her dosya, ayarı kendi XMP&#x27;sinde kaydeder; bu sayede yeniden işleme sırasında ayar aynı şekilde uygulanır.
* **Bir `.daq` dosyasının hangi kameraya ait olduğu (1.3.0).** Bir çekimin `.daq` değeri, işleme sırasında yalnızca `.daq.cameras.json` değerinde belirtilen kameralardan gelen görüntülerle eşleştirilir. Bu nedenle, kendi ışık sensörü olmayan bağımsız bir kamera artık başka bir kameranın okuma değerini kullanmaz — bu kameraya bir sensör bağlayın veya Işık Sensörleri sekmesinde bir sensör kaydedin. Bir ışık sensörü kaydı (ve bu dosyaya sahip olmayan herhangi bir `.daq`, eski yakalamalar dahil) hala her kameraya hizmet eder.

Bu, `chloros-cli lattice capture` / `array-capture` dosyalarının `-o` dizinine yazdığı düzenin aynısıdır — [CLI

Referansı § Bir yakalama klasörü nasıl görünür?](../reference/cli-reference.md#what-a-captures-folder-looks-like).

<!-- SCREENSHOT-NEEDED: OS file explorer showing a real <project>/captures/ folder after a multi-level array capture — the raw/debayered/radiance/reflectance/preview subfolders, the daq/ and clamp_masks/ folders, and sync_<ts>_SN<serial>_<level>.tif filenames visible inside one subfolder. -->

## Yakalamaları kalibre edilmiş ürünlere yeniden işleme

Yakalanan ham kareler ve kaydedilmiş `.daq` dosyası, işleme sürecinin ihtiyaç duyduğu her şeydir — bu nedenle Fastest Capture, gerçek işler için güvenilirdir.

* **GUI**: yakalama klasörünü bir projeye ekleyin ([Bir Projeye Dosya Ekleme](../processing-images-gui/adding-files-to-a-project.md)) ve her zamanki gibi işleyin.
* **CLI**: `process` dosyasını **yakalama kök dizinine** yönlendirin:

```bash
chloros-cli process "C:/ChlorosProjects/MyField/captures"
```

`process` normalde yalnızca belirttiğiniz klasörü içe aktarır, ancak bu klasörde görüntü bulunmadığında ve alt klasörler varsa otomatik olarak alt klasörlere iner — böylece seviye alt klasörleri ve `daq/` klasörü tek seferde alınır. Her yakalama, her seviye için ayrı bir resim olarak değil, diğer seviyeleri görüntülenebilir modlar olarak eklenmiş **tek bir resim** olarak içe aktarılır.

Bir seviye alt klasörünü doğrudan adlandırmak (örn. `…/captures/raw/`) da işe yarar, ancak `daq/` klasörünü geride bırakır — yeniden-`raw/`&#x27;ten radyometrik bir ürün türetirken bu klasörü de yanına kopyalayın, aksi takdirde zaman damgası eşleşmesi için karşılaştırılacak bir şey kalmaz.

{% hint style="warning" %}
**İşleme her zaman `raw`&#x27;ten başlar.** Her bir yakalama içinde ham kare, iş akışının kaynağıdır; `debayered`, `radiance`, `reflectance`, ve `preview` görüntülenebilir modlar olarak gelir ancak iş akışına asla geri beslenmez — türetilmiş bir ürünün yeniden işlenmesi, piksellerine zaten işlenmiş olan vinyet, renk ve parlaklık hesaplamalarını yeniden uygulayacaktır; bu nedenle Chloros

, çift işleme yapmak yerine bunu reddeder. `index/` ve `composite/` renderları ise hiçbir şekilde işlenmez (bunlar yakalama değil, çıktıdır). Raw içe aktarmaları **olmadan** kaydedilen bir yakalama klasörü normal şekilde görüntülenir, ancak `process` bunu atlar ve bunu belirtir; `--input-level {raw,debayered,processed}` ise bir giriş noktasını zorlayan kasıtlı bir kaçış yoludur. Kesin atlama mesajları için [CLI

Referansı](../reference/cli-reference.md#what-a-captures-folder-looks-like) bölümüne bakın.
{% endhint %}

Yeniden işleme komut dosyası yazarken bilinmesi gereken iki davranış daha:

* Ürünleri talep eden ancak **hiçbir görüntü ürünü yazmayan**bir `chloros-cli process` çalıştırması, yüksek sesli bir hata verir ve sıfırdan farklı bir değerle sonlanır** — asla sessiz bir boş çalıştırma elde edemezsiniz. Başarılı çalıştırmalar, ürün sayılarını bildirir. (Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma da yine de başarılı sayılır.)
* Yeniden içe aktarılan işlenmiş dışa aktarımlar asla bir yakalamanın ham verisi yuvasını almaz — orijinal ham veri her zaman iş akışının kaynağı olarak kalır.

## CLI

&#x27;ye karşılık gelen komutlar

Bu sayfadaki her şey başsız olarak çalıştırılabilir. GUI yakalama modları doğrudan `chloros-cli lattice array-capture` ile eşleşir:

| GUI | CLI

|
| --- | --- |
| Tekli | `chloros-cli lattice array-capture` |
| Sürekli | `array-capture --continuous [--count N] [--duration S]` |
| Aralık | `array-capture --interval S [--duration S]` |
| En Hızlı Yakalama | `array-capture --fastest` |
| Hizalanmış onay kutusu | `--aligned / --no-aligned` |
| Dışa aktarma türü onay kutuları | `--processing LEVEL` veya `--levels L1,L2,…` (varsayılan `all`) |
| Dizin videosu kaydet | `chloros-cli lattice array-record` |
| Ham seri çekim kaydet / Video oluştur | `chloros-cli lattice array-burst` / `array-build-video` |

Tam bayrak tabloları, akıllı AE sabit çekim seçeneği (`--smart`) ve sabit hız modeli, [CLI

Referansı § Çekim Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess) bölümünde yer almaktadır.
