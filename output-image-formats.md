---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Çıktı Görüntü Biçimleri

Chloros, işlenmiş ürünleri dört dosya biçiminde dışa aktarır. Biçimi Proje Ayarları&#x27;nda (GUI), `--format` (CLI) veya `export_format` (SDK) ile seçin. CLI ve SDK, aşağıdaki tam dizeleri kabul eder.

| Biçim dizesi | Uzantı | Piksel türü | Piksel aralığı | Notlar |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(varsayılan)* | `.tif` | uint16 sayısal sayı | 0 – 65535 | Fotogrametri / GIS için önerilir. |
| `TIFF (8-bit)` | `.tif` | uint8 sayısal sayı | 0 – 255 | Yalnızca 8 bitlik formatlar sunan tüm 8 bitlik çekimler için: Chloros, bit derinliğini yükseltmez. |
| `TIFF (32-bit, Percent)` | `.tif` | float32 | 0,0 – 1,0 | 1,0 = %100 yansıma. Bazı uygulamalar kayan noktalı TIFF dosyalarını okuyamaz; dosyalar daha büyüktür. |
| `PNG (8-bit)` | `.png` | uint8 sayısal değer | 0 – 255 | Kayıpsız sıkıştırma, web&#x27;de görüntüleme ve görselleştirme için uygundur. |
| `JPG (8-bit)` | `.jpg` | uint8 sayısal sayı | 0 – 255 | Kayıplı sıkıştırma, en küçük dosyalar. |

## Çıktı dosyalarının kaydedildiği yer

Ürünler, proje klasörü altına, önce kameraya göre, ardından dosya formatına göre gruplandırılarak kaydedilir:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera (model+lens+filter)
    ├── tiff16/                          # follows --format: tiff16, tiff8, png8, jpg8, or tiff32
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one <INDEX>_Index_Images/ folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

LATTICE için kamera klasörü `LATT-<sensor>-<lens>-F<filter>`, Survey3 için ise `<model>_<filter>`&#x27;tir (ör. `Survey3N_RGN`). **Dışa aktarılan her ürün, kaynak dosyanın adını korur — ürünü tanımlayan klasördür, dosya adı son eki değildir.** Tüm kurallar için CLI Referansı&#x27;ndaki [Çıktıların nereye kaydedildiği](reference/cli-reference.md) bölümüne bakın.

## LATTICE ürünleri (yakalama ve dışa aktarma düzeyleri)

Bir LATTICE ham karesi, tek bir geçişte istenen her ürüne ayrılır. Her ürün türünün kendine ait bir açma/kapama seçeneği vardır (GUI onay kutuları veya CLI&#x27;de `--debayered` / `--preview` / `--radiance` / `--reflectance`, varsayılan olarak tümü AÇIK):

| Seviye | İçerik | Veri türü |
| --- | --- | --- |
| `raw` | Sensörden doğrudan alınan Bayer verileri (tek renkli kameralar: tek bant). İşleme her zaman ham verilerden başlar. | Yakalandığı gibi |
| `debayered` | Doğrusal demosaik — M3C için 3 kanal, M3M için 1 kanal gri tonlamalı: kalibrasyon paketinin karanlık ofseti kaldırılır, ardından doğrusallık düzeltmesi, düz alan veya ayrıştırma yapılmadan kenar duyarlı bir demosaik uygulanır. `process` dışa aktarımı, her işleme modunda, bu seviyede bir çekimin kaydettiği dosyanın aynısını yazar. | Doğrusal DN |
| `radiance` | Tam radyometrik zincirden elde edilen mutlak spektral parlaklık, **W/m²/sr/nm** cinsinden. Seçilen dışa aktarma formatından bağımsız olarak her zaman 32 bit TIFF (`tiff32/Radiance_Images/`) olarak yazılır. | float32 |
| `reflectance` | Yansıma oranı ρ; burada **DN 32768 = ρ 1,0 (%100)** olup, ρ 2,0&#x27;a kadar esneklik payı vardır. Pix4D uyumlu. | uint16 |
| `preview` | Ekrana hazır render: RGB = beyaz dengesi + gama; multispektral = sahte renk uzaması. | 8-bit ekran |

### Yönlendirme

`raw` ve `debayered` dosyaları her zaman sensörün **doğal** yöneliminde kalır; kameranın çevirme/dönme ayarı yalnızca diğer ürünlere (önizleme, parlaklık, yansıma, indeks) uygulanır. Dizi yakalamalarında, yönlendirme talebinde bulunmadığınız sürece her bir elemanın dosyaları doğal yönlendirmede kalır (CLI&#x27;de `--orient`, SDK&#x27;de `orient=True`). Her dosya, XMP&#x27;de kendi piksellerine ait yönelimi kaydeder — `ChlorosRotation`, `ChlorosFlipHorizontal`, `ChlorosFlipVertical` — ve `ChlorosOrientationApplied`, kendi piksellerinin bu bilgiyi taşıyıp taşımadığını belirtir (ham ve debayered dosyalarda `false`). Chloros Görüntü Görüntüleyicisi, yalnızca görüntüleme amacıyla ham ve debayered dosyaları bu kayda göre dönüştürür; dosyaların kendisi asla yeniden yazılmaz. Kayıt içermeyen bir dosya (1.3.0 sürümünden önce veya başka bir araçla yazılmış), saklandığı haliyle gösterilir.

## Klip maskeleri

Çok spektral bir M3C kameranın parlaklık ve yansıma ürünleri, 3×3 bantlı bir ayrıştırma işleminden geçer. Ayrıştırma işlemi bir bantta negatif bir değer ürettiğinde, bu değer sıfıra kırpılır (canlı görüntülerde de tam olarak aynı şekilde kırpma yapılır). 1.3.0 sürümündeki yenilik olarak, en az bir pikseli kırpılmış bir ürün, hangi piksellerin ve bantların kırpıldığını tam olarak belirten bir **kırpma maskesi** ile birlikte sunulur:

* **Yer:**ürün klasörlerinin**yanında** bulunan bir `clamp_masks/` klasöründe, asla bir ürün klasörünün içinde değil: `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif` ve `tiff32/Radiance_Images/x.tif` → `tiff32/clamp_masks/Radiance_Images/x_clampmask.tif`.
* **Ne:** ürünün kendi piksel ızgarası üzerinde tek bantlı bir `uint8` TIFF dosyası (yönlendirme, ROI, binning ve dizi hizalaması dahil). Bit 0 (`mask & 1`), ürünün R kanalındaki bandın kırpıldığı yerde ayarlanır; bit 1 (`& 2`) G kanalında, bit 2 (`& 4`) ise B kanalında kırpma yapıldığı yerleri gösterir. Kırpılan piksellerin bant başına payı da üründe `Chloros:BandRUnmixClampFraction`, `Chloros:BandGUnmixClampFraction` ve `Chloros:BandBUnmixClampFraction` olarak işaretlenir.
* **Üründe nasıl belirtilir:** XMP etiketi `Chloros:UnmixClampMask`, maskenin ürün klasörüne göre yolunu içerir (`../clamp_masks/<product folder>/<name>_clampmask.tif`, her zaman `/` ile ayrılır), hiçbir piksel kırpılmadığında (maske yazılmaz) `none` veya maske yazılamadığında `unavailable` değerini alır. Etiketi olmayan bir ürün, unmix işlemini hiç çalıştırmamıştır (RGB ve mono kameralar, ham ve debayered seviyeler).

Hiçbir ürün klasörü maske içermediğinden, `Reflectance_Calibrated_Images/` gibi bir ürün klasörünü alt klasörleri de dahil olmak üzere olduğu gibi Pix4D, Metashape veya diğer fotogrametri yazılımlarına aktarabilirsiniz — üstündeki format klasörünü değil, ürün klasörünü aktarın. Chloros&#x27;un kendi içe aktarma işlevi, sınırlama maskelerini atlar. CLI, bunları ayrı olarak sayar: `Image products written: N (plus M clamp masks)`.

## Dizin ürünleri

Her dizin, kendi `<INDEX>_Index_Images/` klasörüne yerleştirilir (örn. `NDVI_Index_Images/`).

* **Tamsayı formatları**(`TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)`) −1…1 eşlemesi değil,**görüntü başına doğrusal uzatma** değerini saklar: DN 0, o karenin en düşük dizin değeridir ve tam ölçek ise en yüksek değeridir. Dosya, `Chloros:IndexScaling=linear_minmax`, `Chloros:IndexMin` ve `Chloros:IndexMax` değerlerini içerir; dolayısıyla `value = IndexMin + DN / full_scale × (IndexMax − IndexMin)` da vardır. Her karenin kendi uç noktaları olduğundan, kareleri DN olarak değil, değerler olarak karşılaştırın.
* **`TIFF (32-bit, Percent)`**, indeks değerlerini yeniden ölçeklendirme yapmadan float32 olarak depolar — bu, bir uçuş boyunca nicel çalışmalar için en iyi seçimdir.

1.3.0 sürümündeki yenilik olarak, bir indeks ürünü kaynak ham verinin etiketlerini tekrarlamak yerine **kendisini** tanımlar. GPS ve kamera etiketleri, hesaplamanın yapıldığı üründen (varsa yansıma ürünü) kopyalanır ve ardından:

| Etiket | Değer |
| --- | --- |
| `Chloros:ProcessingLevel` | `index` (böylece dosya hiçbir zaman bir çekim olarak yeniden içe aktarılmaz) |
| `Chloros:IndexApplied` / `Chloros:IndexExpression` | `True` / kullanılan formül |
| `Chloros:IndexLevel` | `reflectance` veya `radiance` — endeksin neye göre hesaplandığı (ikisi de yoksa bu bilgi yoktur) |
| `Chloros:IlluminationInvariant` | Yansıma değerinden hesaplanan bir endeks için `True`, parlaklık veya sensör değerlerinden hesaplanan bir endeks için `False` |
| `Chloros:ChlorosPipelineVersion` | indeksi oluşturan Chloros sürümü |

`Chloros:PixelScale`, indeks ürünlerinden kaldırılmıştır — yukarıdaki `IndexMin` / `IndexMax` değerlerini kullanın. Tam geri okuma reçetesi, [CLI Referansı](reference/cli-reference.md) içindeki &quot;Bir tamsayı indeksli raster, görüntü başına kontrast genişletmesidir&quot; başlığı altında yer almaktadır.

## Yansıma piksel değerlerini okuma

Yansıtma, tamsayı bir dijital sayı olarak saklanır ve **ρ = 1,0 (%100 yansıtma) anlamına gelen DN, kaynak kameraya bağlıdır**:

| Kaynak kamera | ρ = 1,0&#x27;ın DN değeri | Nasıl anlaşılır |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ρ 2,0&#x27;a kadar başlık aralığı) | Dosyaya XMP etiketi `Chloros:PixelScale=32768` damgalanmıştır. |
| Survey3 | `65535` (ρ 1,0&#x27;da kırpılmış) | `Chloros:PixelScale` etiketi yoktur — bu eksiklik bir işarettir. (1.3.0 sürümünden itibaren Survey3, her işleme modunda `Chloros:*` gibi diğer `Chloros:CalibrationApplied` işleme etiketlerini de taşır.) |

**Sabit bir değer varsaymak yerine, `Chloros:PixelScale` XMP etiketini okuyun ve ona bölün**. Etiket uint16 alanında tanımlandığından, yeniden ölçeklendirme yapılan çıktı formatlarında da `32768` olarak kalır — önce depolanan veri türünü uint16&#x27;ya normalize edin (8-bit&#x27;ten ×257, float32&#x27;den ×65535).

{% hint style="warning" %}
**8-bit yakalamalar.** Bir 8-bit yakalamanın dışa aktarılan verileri 8-bit&#x27;tir. Yansıma değeri yeniden ölçeklenir ve `Chloros:PixelScale=32768` etiketiyle işaretlenir, böylece ölçek korunur; 8-bit DN ürünleri ve 1.3.0 sürümünden önce yazılmış yansıma dosyaları, 0–255 aralığına kırpılır ve ölçek etiketi taşımaz. Bir LATTICE yansıma dosyasında etiket yoksa, ölçek olduğunu varsaymayın; bunun yerine 16-bit veya 32-bit olarak yeniden dışa aktarın.
{% endhint %}

Kuralların tamamı (MicaSense uyumlu etiketler dahil) için [CLI Referansı](reference/cli-reference.md) içindeki **&quot;Yansıma pikselini okuma&quot;** bölümüne bakın.
