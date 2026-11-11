---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/supported-cameras
---

# Desteklenen Kameralar

Chloros
, **tüm platformlarda** (Windows
, Linux
amd64 ve Linux
arm64/Jetson) iki MAPIR
kamera ailesinden gelen görüntüleri işler:

* **Survey3** — Survey3W
(geniş) ve Survey3N
(dar) kameralar. Giriş: `RAW+JPG`.
* **LATTICE** — M3C ve M3M multispektral kamera modülleri. Giriş: `.tif`/`.tiff` çekimleri. LATTICE kameraları, senkronize çoklu kamera dizileri dahil olmak üzere, Chloros
üzerinden — GUI'deki Kameralar sekmesi (Windows
) veya `chloros-cli lattice` / Python
SDK
(Windows
ve Linux
) aracılığıyla — **canlı olarak kontrol edilebilir**. [LATTICE kılavuzuna](lattice/) bakınız.

İşleme boru hattı, `.dng` giriş dosyalarını da kabul eder.

## Survey3

<table data-header-hidden><thead><tr><th width="156">Üretici</th><th width="250">Kamera Modeli</th><th width="138">Filtre Modeli</th><th width="187">Görüntü Türü</th></tr></thead><tbody><tr><td><strong>Üretici</strong></td><td><strong>Kamera Modeli</strong></td><td><strong>Filtre Modeli</strong></td><td><strong>Görüntü Türü</strong></td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RGN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>OCN</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NGB</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>RE</td><td>RAW+JPG, JPG</td></tr><tr><td>MAPIR</td><td>Survey3W, Survey3N</td><td>NIR</td><td>RAW+JPG, JPG</td></tr></tbody></table>## LATTICE

LATTICE serisi, Sony IMX265 global deklanşör sensörü (3,1 MP, 3,45 µm piksel) üzerine kurulu modüler bir multispektral kamera sistemidir. Her kamera, kimliğini bir model dizesi olarak saklar:

```
<sensor>-<lens>-F<filter>        e.g.  M3C-L41-FRGN,  M3M-L87-F550
```

Chloros

, bunu `LATT-` önekiyle görüntüler (örneğin `LATT-M3M-L41-F550`) ve model dizesi, sensör profili, bant düzeni ve kalibrasyon gibi tüm sonraki işlemleri otomatik olarak belirler; kamera başına yapılandırılması gereken hiçbir şey yoktur. Lens numarası, **derece cinsinden yatay görüş açısı**dır: `L41` = dar 41°, `L87` = geniş 87°.

İki sensör yapılandırması mevcuttur:

| Yapılandırma | Sensör      | Filtre türü                           | Kamera başına bant sayısı                                                        |
| ------------- | ----------- | ------------------------------------- | ----------------------------------------------------------------------- |
| **M3C**       | Bayer renkli | Üçlü bant geçidi                       | Tek bir pozlamadan 3 spektral bant                                 |
| **M3M**       | Monokrom  | Tek dar bantlı girişim filtresi | 1 kalibre edilmiş bant — bitki örtüsü indeksleri için birden fazla M3M kamerayı birleştirin |

### M3C (Bayer) filtre seçenekleri

| Filtre | Bantlar (merkez nm'deki adı / FWHM

nm)       |
| ------ | ---------------------------------------- |
| `FRGB` | Blue

475/30 · Green

550/30 · Red

625/30  |
| `FRGN` | Red

660/21 · Green

550/30 · NIR

850/30   |
| `FOCN` | Orange

618,4/46 · Cyan

494,1/40 · NIR

821,6/54 (ölçülmüş — aşağıya bakın) |
| `FNGB` | Blue

475/30 · Green

550/30 · NIR

850/30  |

`FOCN` için değerler, filtrenin **ölçülen** yarı maksimum bant genişliğidir (Chloros

1.3.0 ve sonraki sürümler; önceki sürümlerde teknik özellik tablosundaki değerler 615/21 · 490/38 · 808/14) değerlerini kullanırdı. Bant adları `Orange_615`, `Cyan_490` ve `NIR_808` olarak kalır ve ölçülen merkez ile genişlik, Chloros

'un her kalibre edilmiş dışa aktarımına damgaladığı değerlerdir.

### M3M (mono) filtre kataloğu — 23 SKU

F-numarası, SKU etiketidir; ölçülen bant (kalibre edilmiş her bir ihraç ürününe damgalanır), parti başına filtre taramasıdır:

| SKU    | Merkez (nm, ölçülmüş) | FWHM

kenarları (nm) | Genişlik (nm) |
| ------ | --------------------- | --------------- | ---------- |
| F385   | 379,4                 | 367–392         | 25         |
| F405   | 403,9                 | 390–417         | 27         |
| F450   | 443,7                 | 430–458         | 28         |
| F485   | 489,7                 | 478–502         | 24         |
| F520   | 519,9                 | 504–536         | 32         |
| F550   | 548,4                 | 531–566         | 35         |
| F590   | 589,0                 | 570–608         | 38         |
| F615   | 623,8                 | 614–634         | 20         |
| F632   | 633,4                 | 616–651         | 35         |
| F650   | 651,1                 | 636–666         | 30         |
| F685   | 686,2                 | 675–698         | 23         |
| F715   | — (nominal)           | 706–724         | 18         |
| F725   | 725.2                 | 712–738         | 26         |
| F750   | 746.0                 | 729–763         | 34         |
| F780   | 775.1                 | 754–796         | 42         |
| F808   | 810.3                 | 789–832         | 43         |
| F832   | 826,1                 | 810–843         | 33         |
| F850   | 846,5                 | 828–865         | 37         |
| F880   | — (nominal)           | 867–893         | 26         |
| F905   | — (nominal)           | 892–920         | 28         |
| F940   | 940,6                 | 923–958         | 35         |
| F950   | 945,1                 | 929–961         | 32         |
| F988 † | 985,3                 | 968–1003        | 35         |

_"Bant kenarları, MAPIR

'ın parti bazında yaptığı filtre taramalarından elde edilen yarı maksimum genişlik değerleri olarak ölçülür — bu değerler, Chloros

'ın kalibre edilmiş her dışa aktarımına eklediği değerlerle aynıdır."_ "— (nominal)" = henüz parti taraması yapılmamıştır; bu SKU'lar için belirtilen merkez SKU numarasıdır ve genişlik ise üreticinin verdiği değerdir.

† "F988 yansıma oranı, sahadaki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros

en son panel yakalamanızı uygular ve panel gözlemleri arasında bu değeri korur." Bkz. [Kalibrasyon Hedefleri](calibration-targets.md).

Canlı kamera kontrolü, diziler, ağ kurulumu ve radyometrik işleme zinciri için [LATTICE kılavuzuna](lattice/) bakın.
