---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/output-image-formats
---

# Çıktı Görüntü Biçimleri

Chloros, işlenmiş ürünleri dört farklı dosya biçiminde dışa aktarır. Biçimi, Proje Ayarları&#x27;nda (GUI), `--format` (CLI) veya `export_format` (SDK) ile formatı seçin. CLI ve SDK, aşağıda belirtilen tam dizeleri kabul eder.

| Biçim dizesi | Uzantı | Piksel türü | Piksel aralığı | Notlar |
| --- | --- | --- | --- | --- |
| `TIFF (16-bit)` *(varsayılan)* | `.tif` | uint16 sayısal değer | 0 – 65535 | Fotogrametri / GIS için önerilir. |
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

Tek bir geçişte, bir LATTICE ham karesi istenen tüm ürünlere dağıtılır. Her ürün türünün kendi açma/kapama seçeneği vardır (GUI onay kutuları veya CLI `--debayered` / `--preview` / `--radiance` / `--reflectance`, varsayılan olarak tümü AÇIK):

| Seviye | İçerik | Veri türü |
| --- | --- | --- |
| `raw` | Sensörden doğrudan alınan Bayer verileri (tek renkli kameralar: tek bant). İşleme her zaman ham veriden başlar. | Yakalandığı gibi |
| `debayered` | Doğrusal demosaik — M3C için 3 kanal, M3M için 1 kanallı gri tonlamalı. | Doğrusal DN |
| `radiance` | Tam radyometrik zincirden elde edilen mutlak spektral parlaklık, **W/m²/sr/nm** cinsinden. Seçilen dışa aktarım formatından bağımsız olarak her zaman 32 bit TIFF (`tiff32/Radiance_Images/`) olarak yazılır. | float32 |
| `reflectance` | Yansıma ρ; burada **DN 32768 = ρ 1,0 (%100)** olup, ρ 2,0&#x27;a kadar esneklik payı vardır. Pix4D uyumlu. | uint16 |
| `preview` | Ekrana hazır render: RGB = beyaz dengesi + gama; multispektral = sahte renk genişletme. | 8-bit ekran |

## Yansıma piksel değerlerini okuma

Yansıtma, tamsayı biçiminde bir dijital sayı olarak saklanır ve **ρ = 1,0 (%100 yansıtma) anlamına gelen DN değeri, kaynak kameraya bağlıdır**:

| Kaynak kamera | ρ = 1,0&#x27;ın DN değeri | Nasıl anlaşılır |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ρ 2,0&#x27;a kadar başlık aralığı) | Dosyaya XMP etiketi `Chloros:PixelScale=32768` damgalanmıştır. |
| Survey3 | `65535` (ρ 1,0&#x27;da kırpılmış) | `Chloros:*` XMP etiketi yok — bu yokluk bir işarettir. |

**Sabit bir değer varsaymak yerine, `Chloros:PixelScale` XMP etiketini okuyun ve ona bölün**. Etiket uint16 alanında tanımlandığından, yeniden ölçeklendirme yapılan çıktı formatlarında da `32768` olarak kalır — önce depolanan veri türünü uint16&#x27;ya normalize edin (8 bit&#x27;ten ×257, float32&#x27;den ×65535).

{% hint style="warning" %}
**Tasarım gereği, ölçeklendirme yapılmayan bir durum vardır.** 8-bit kaynaklı bir yakalama (BayerRG8) 8-bit TIFF olarak yazıldığında, işleme hattı yeniden ölçeklendirme yapmak yerine değeri 0–255 aralığına kırpar; dolayısıyla dosyayı tanımlayan bir ölçek yoktur — Chloros burada `Chloros:PixelScale` etiketini kasıtlı olarak atlar. Bir LATTICE yansıma dosyasında bu etiket yoksa, ölçek olduğunu varsaymayın; bunun yerine 16 bit veya 32 bit olarak yeniden dışa aktarın.
{% endhint %}

Tüm kurallar için (MicaSense uyumlu etiketler dahil), [CLI Referansı](reference/cli-reference.md) içindeki **&quot;Yansıma piksellerini okuma&quot;** bölümüne bakın.
