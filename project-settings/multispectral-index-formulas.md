---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Çok Spektral İndeks Formülleri

Aşağıdaki indeks formülleri, Survey3 filtresinin ortalama geçirgenlik aralıklarının bir kombinasyonunu kullanır:

<table><thead><tr><th align="center">Survey3 Filtresi Rengi</th><th width="196.199951171875" align="center">Survey3 Filtresi Adı</th><th width="159.800048828125" align="center">Geçiş Aralığı (FWHM)</th><th align="center">Ortalama Geçiş</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re-RedEdge</td><td align="center">712-735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865 nm</td><td align="center">850 nm</td></tr></tbody></table>

Bu formüller kullanıldığında, adın sonu &quot;\_1&quot; veya &quot;\_2&quot; ile bitebilir; bu, hangi NIR filtresinin (NIR1 veya NIR2) kullanıldığına karşılık gelir.

LATTICE M3C (Bayer üçlü bant geçidi) kameralar için, aynı indeks motoru M3C filtre bantlarını kullanır:

| M3C filtresi | Bant 1 (merkez/FWHM) | Bant 2 (merkez/FWHM) | Bant 3 (merkez/FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

LATTICE M3M kameraları tek bantlıdır (kamera başına bir dar bant filtresi), bu nedenle tek bir M3M görüntüsü için çok bantlı indeksler hesaplanmaz. M3M ile indeksleri hesaplamak için, iki veya daha fazla kamerayı hizalanmış bir çok bantlı yığın halinde birleştirin ve LATTICE indeks motorunu (`chloros-cli lattice index` veya GUI&#x27;nin canlı İndeks Hesaplayıcısı) kullanın.

***

## Her bir indeks adının nerede çalıştığı

 Chloros&#x27;un **üç** indeks yüzeyi vardır ve bunların ön ayar listeleri birbiriyle aynı değildir. Bu bölümü, bir adın kullanmayı planladığınız yerde çalışıp çalışmayacağını kontrol etmek için kullanın.

| Bulunduğunuz yer | Hangi liste geçerlidir | Sayı |
| --- | --- | --- |
| Proje Ayarları → İndeks → İndeks ekle (GUI) | Yüzey 1 | 27 |
| Görüntü Görüntüleyici [Endeks/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) (GUI) | Yüzey 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Yüzey 2 | 22 |
| SDK `process_folder(indices=[...])` | Yüzey 2 | 22 |
| `chloros-cli lattice index --preset` | Yüzey 3 | 22 (farklı bir 22) |
| Kameralar sekmesi canlı Dizin Hesaplayıcı | Yüzey 3 | 22 (farklı bir 22) |

Surface 1 ve 2, **tek bir kameradan gelen tek bir görüntü üzerinde**çalışır, o kameranın filtre kanallarına bağlı `x`/`y`/`z`(/`a`) sembol yuvalarını kullanır. Yüzey 3,**hizalanmış çok bantlı yığın** üzerinde çalışır — bir küp içinde eşleştirilmiş birkaç LATTICE kamerası — ve kanallara küçük harfli adlarıyla başvurur.

### 1. GUI Proje Ayarları / Görüntü Görüntüleyici sandbox açılır menüsü — 27 formül

Açılır menü bunları şu sırayla listeler (alfabetik sıraya göre değil, eklenme sırasına göredir):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

GUI&#x27;de kameranızın filtre kanallarını formülün bant yuvalarına sürükleyerek, kameranızın desteklediği herhangi bir bant atamasıyla herhangi bir formülü kullanabilirsiniz. Kaydettiğiniz özel formüller bu listenin altına eklenir.

**Yalnızca GUI&#x27;ye özel** beş formül — CLI/SDK&#x27;deki `--indices` listesinin kabul etmediği formüller — şu şekilde uygulanır:

| Yalnızca GUI&#x27;ye özel ön ayar | Formül (uygulandığı şekliyle) | Yuvalar |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (dört yuva) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Her biri için amaçlanan eşleme, bu sayfanın ilerleyen kısımlarında kendi bölümünde verilmiştir (örneğin GARI, x=Green, y=NIR, z=Blue, a=Red değerlerini bekler). GARI, Chloros&#x27;ta dördüncü yuvayı kullanan tek formüldür.

### 2. CLI / SDK `--indices` ad genişletme — 22 ön ayar

`chloros-cli process --indices` seçeneği (ve SDK `indices` parametresi) şu ön ayar adlarını kabul eder:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Bilinmeyen dizin adları, herhangi bir uyarı vermeden atlanır.** Bu listenin dışında kalan bir ad (yalnızca GUI&#x27;ye özgü beş formül olan `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` ve GUI&#x27;de kaydettiğiniz tüm özel formüller dahil) yalnızca bir günlük bildirimi ile atlanır — çalıştırma o dizin olmadan devam eder ve çalıştırma işlemi yine de başarılı olarak raporlanır. Bildirim şu şekilde yazdırılır:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Adlar, boşluklar kaldırıldıktan sonra büyük/küçük harf duyarlılığı gözetilmeden eşleştirilir; bu nedenle `ndvi`, `NDVI` ve ` NDVI ` aynı ön ayardır. Ayrıca, bir ön ayar, kameranızın filtresinin sağlamadığı bir bant gerektiriyorsa da atlanır.
{% endhint %}

Uygulandıkları şekliyle tam formüller (`x`/`y`/`z` sembolleri bant yuvalarıdır; varsayılan eşleme her ön ayar için ayrı ayrı gösterilmiştir):

| Ön Ayar | Formül (uygulandığı şekliyle) | Varsayılan filtre | Yuvalar (x, y, z) |
| --- | --- | --- | --- |
| NDVI | `(y-x)/(y+x)` | RGN | Red, NIR |
| GNDVI | `(y-x)/(y+x)` | RGN | Green, NIR |
| NDRE | `(y-x)/(y+x)` | RE | RE, NIR |
| OSAVI | `(y-x)/(y+x+0.16)` | RGN | Red, NIR |
| SAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Red, NIR |
| MSAVI2 | `(2*y+1-sqrt((2*y+1)*(2*y+1)-8*(y-x)))/2` | RGN | Red, NIR |
| EVI | `2.5*(y-x)/(y+6*x-7.5*z+1)` | RGN | Red, NIR, Blue |
| MSR | `((y/x)-1)/(sqrt(y/x)+1)` | RGN | Red, NIR |
| TDVI | `1.5*(y-x)/sqrt(y*y+x+0.5)` | RGN | Red, NIR |
| LAI | `3.618*(2.5*(y-x)/(y+6*x-7.5*z+1))-0.118` | RGN | Red, NIR, Blue |
| GCI | `(y/x)-1` | RGN | Green, NIR |
| GRVI | `y/x` | RGN | Green, NIR |
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Green, NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Green, NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)*(x/y)` | RGN | Red, Green, NIR |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | NGB | NIR, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### Ön ayar adı nasıl bant konumlarına dönüştürülür

`NDVI` gibi sadece bir ad girdiğinizde, Chloros her bir sembolün hangi dosyanın hangi kanalını okuduğuna karar vermek zorundadır. Bunun için, bir filtre kodunu her kanalın dizi konumuna eşleyen şu tabloyu kullanır:

| Filtre kodu | Kanal → dizi indeksi |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red`, Orange için bir takma ad olarak kabul edilir, aynı zamanda 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

Projede o filtreye sahip görüntüler varsa, ön ayarın **varsayılan filtresi** (yukarıdaki &quot;Varsayılan filtre&quot; sütunu) kullanılır. Eğer içermiyorsa, Chloros, projede fiilen bulunan filtreleri `RGN, OCN, NGB, RGB, RE, NIR` sırasına göre tarar ve ön ayarın ihtiyaç duyduğu her kanalı sağlayabilen ilk filtreyi seçer. Hiçbiri sağlayamıyorsa, ön ayar o çalıştırma için devre dışı bırakılır. Bu nedenle, yalnızca OCN içeren bir veri setinde talep edilen `NDVI` yine de mantıklı bir sonuç üretir — bu filtre, OCN&#x27;nin Orange ve NIR konumlarına bağlanır.

LATTICE M3C model dizeleri, `F` önekine sahip filtreyi taşır (`LATT-M3C-L41-FRGN`), ancak filtre kodu görüntüden okunduğunda önek atılır; bu nedenle bir FRGN kamerası, yukarıdaki `RGN` satırını çözümler ve özel bir işlem gerektirmez.

### 3. LATTICE indeks motoru (`lattice index --preset`, canlı İndeks Hesaplayıcı) — 22 ön ayar

LATTICE motoru, hizalanmış çok bantlı yığınlar (canlı diziler veya dışa aktarılmış çok bantlı TIFF&#x27;ler) üzerinde çalışır ve küçük harfli kanal adları kullanır (`red`, `green`, `blue`, `red_edge`, `nir`). Ön ayar listesi yukarıdaki ikisinden farklıdır:

| Ön Ayar | Formül | Kanallar |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | kırmızı, nir |
| GNDVI | `(nir - green) / (nir + green)` | yeşil, NIR |
| BNDVI | `(nir - blue) / (nir + blue)` | mavi, NIR |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | kırmızı_kenar, NIR |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | mavi, yeşil, yakın kızılötesi |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | kırmızı, yakın kızılötesi |
| OSAVI | `(nir - red) / (nir + red + 0.16)` | kırmızı, nir |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | kırmızı, nir |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | mavi, kırmızı, nir |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | kırmızı, nir |
| CVI | `nir * red / green**2` | kırmızı, yeşil, nir |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | kırmızı, nir |
| TDVI | `1.5 * (nir - red) / sqrt(nir**2 + red + 0.5)` | kırmızı, nir |
| LAI | `3.618 * (2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)) - 0.118` | mavi, kırmızı, NIR |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | kırmızı, yeşil, mavi |
| NGRDI | `(green - red) / (green + red)` | kırmızı, yeşil |
| VARI | `(green - red) / (green + red - blue)` | kırmızı, yeşil, mavi |
| TGI | `green - 0.39*red - 0.61*blue` | kırmızı, yeşil, mavi |
| EXG | `2*green - red - blue` | kırmızı, yeşil, mavi |
| CIRE | `(nir / red_edge) - 1` | kırmızı\_kenar, nir |
| CIGREEN | `(nir / green) - 1` | yeşil, nir |
| NDWI | `(green - nir) / (green + nir)` | yeşil, nir |

Yüklü sürümünüzden bu tabloyu yazdırmak için `chloros-cli lattice index --list-presets` komutunu çalıştırın, ve mevcut renk gradyanlarını yazdırmak için `--list-gradients` komutunu çalıştırın. Kanal sembolleri büyük/küçük harfe duyarlıdır ve ön ayarın küçük harfli adlarıyla eşleşmelidir (örn. `--channel red=Red_660 --channel nir=NIR_850`).

***

## CVI - Klorofil Bitki Örtüsü Endeksi

Bu endeks, geniş bir taç yoğunluğu aralığında yaprak klorofil içeriğine duyarlıdır. NIR, kırmızı ve yeşil bantları kullanır.

$$
CVI = NIR * {Red \over Green^{2}}
$$

GUI ve CLI/SDK ön ayar listesinde sembol formülü `(z/y)*(x/y)` şeklindedir; burada x=Red, y=Green, z=NIR (RGN kameranın bantları); LATTICE endeks motorunun ön ayarı ise `nir * red / green**2`&#x27;tir. Her yüzey aynı endeksi hesaplar.

_Kaynak: Vincini, M., E. Frazzi ve P. D&#x27;Alessio. &quot;A Broad-Band Leaf Chlorophyll Vegetation Index at the Canopy Scale.&quot; Precision Agriculture 9 (2008): 303-319._

***

## ENDVI - Geliştirilmiş Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, NIR ve yeşil kanallara ek olarak mavi kanalı da kullanır ve mavi bandın kırmızı bandın yerini aldığı NGB filtreli kameralarda yaygın olarak kullanılır.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Uygulama, `((x+y)-(2*z))/((x+y)+(2*z))` sembol formülüyle gerçekleştirilir — kameranızın NIR ve Green kanallarını x/y yuvalarına, Blue kanalını ise z yuvasına atayın (NGB kamera için: x=NIR, y=Green, z=Blue).

***

## EVI - Geliştirilmiş Bitki Örtüsü Endeksi

Bu endeks, yüksek yaprak alanı indeksi (LAI) olan bölgelerde bitki örtüsü sinyalini optimize ederek NDVI&#x27;ye bir iyileştirme olarak MODIS verileriyle kullanılmak üzere geliştirilmiştir. NDVI&#x27;nin doygunluğa ulaşabileceği yüksek LAI bölgelerinde en yararlıdır. Mavi yansıma bölgesini, toprak arka plan sinyallerini düzeltmek ve aerosol saçılımı dahil atmosferik etkileri azaltmak için kullanır.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI değerleri, bitki örtüsü pikselleri için 0 ile 1 arasında olmalıdır. Bulutlar ve beyaz binalar gibi parlak özellikler ile su gibi koyu özellikler, bir EVI görüntüsünde anormal piksel değerlerine neden olabilir. Bir EVI görüntüsü oluşturmadan önce, yansıma görüntüsünden bulutları ve parlak özellikleri maskelemeli ve isteğe bağlı olarak piksel değerleri için 0 ile 1 arasında bir eşik değeri belirlemelisiniz.

_Kaynak: Huete, A. ve ark. &quot;MODIS Bitki Örtüsü Endekslerinin Radyometrik ve Biyofiziksel Performansına Genel Bakış.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Orman Örtüsü Endeksi 1

_Yalnızca GUI — CLI/SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, kırmızı kenar bandını içeren multispektral yansıma görüntülerini kullanarak orman taçlarını diğer bitki örtüsü türlerinden ayırır.

$$
FCI1 = Red * RedEdge
$$

Ormanlık alanlar, ağaçların daha düşük yansıma değeri ve taç yapısı içindeki gölgelerin varlığı nedeniyle daha düşük FCI1 değerlerine sahip olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Multispektral görüntüler için sağlam orman örtüsü indeksleri.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Orman Örtüsü İndeksi 2

_Yalnızca GUI — CLI/SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, kırmızı kenar bandı içermeyen multispektral yansıma görüntülerini kullanarak orman taç katmanlarını diğer bitki örtüsü türlerinden ayırır.

$$
FCI2 = Red * NIR
$$

Ormanlık alanlar, ağaçların daha düşük yansıma değeri ve taç yapısı içindeki gölgelerin varlığı nedeniyle daha düşük FCI2 değerlerine sahip olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Multispektral görüntüler için sağlam orman örtüsü indeksleri.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## GEMI - Küresel Çevre İzleme İndeksi

_Yalnızca GUI — CLI/SDK `--indices` ön ayarı olarak mevcut değildir._

Bu doğrusal olmayan bitki örtüsü endeksi, uydu görüntülerinden küresel çevre izleme amacıyla kullanılır ve atmosferik etkileri düzeltmeye çalışır. NDVI’ye benzer, ancak atmosferik etkilere karşı daha az duyarlıdır. Çıplak topraktan etkilenir; bu nedenle, seyrek veya orta yoğunlukta bitki örtüsünün bulunduğu alanlarda kullanılması önerilmez.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Burada:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Kaynak: Pinty, B. ve M. Verstraete. GEMI: Uydulardan Küresel Bitki Örtüsünü İzlemek için Doğrusal Olmayan Bir Endeks. Vegetation 101 (1992): 15-20._

***

## GARI - Atmosferik Etkilere Dayanıklı Yeşil Endeks (Green)

_Yalnızca GUI — CLI/SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, NDVI’ye kıyasla çok çeşitli klorofil konsantrasyonlarına daha duyarlıdır ve atmosferik etkilere karşı daha az duyarlıdır.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama sabiti, atmosferdeki aerosol koşullarına bağlı bir ağırlıklandırma fonksiyonudur. ENVI, Gitelson, Kaufman ve Merzylak’ın önerdiği 1,7 değerini kullanır (1996, sayfa 296) tarafından önerilen değerdir.

_Kaynak: Gitelson, A., Y. Kaufman ve M. Merzylak. &quot;EOS-MODIS&#x27;ten Küresel Bitki Örtüsünün Uzaktan Algılanmasında Yeşil Kanalın Kullanımı.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI - Green Klorofil Endeksi

Bu endeks, çok çeşitli bitki türlerinde yaprak klorofil içeriğini tahmin etmek için kullanılır.

$$
GCI = {NIR \over Green} - 1
$$

Geniş NIR ve yeşil dalga boylarına sahip olmak, klorofil içeriğinin daha iyi tahmin edilmesini sağlarken, daha fazla hassasiyet ve daha yüksek bir sinyal-gürültü oranı sunar.

_Kaynak: Gitelson, A., Y. Gritz ve M. Merzlyak. &quot;Yaprak Klorofil İçeriği ile Spektral Yansıtma Arasındaki İlişkiler ve Yüksek Bitki Yapraklarında Tahribatsız Klorofil Değerlendirmesi için Algoritmalar.&quot; Journal of Plant Physiology 160 (2003): 271-282._

***

## GLI - Yeşil Yaprak Endeksi

Bu endeks, başlangıçta buğday örtüsünü ölçmek üzere dijital RGB kamera ile kullanılmak üzere tasarlanmıştır; burada kırmızı, yeşil ve mavi dijital sayılar (DN&#x27;ler) 0 ile 255 arasında değişmektedir.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI değerleri -1 ile +1 arasında değişir. Negatif değerler toprağı ve cansız unsurları temsil ederken, pozitif değerler yeşil yaprakları ve sapları temsil eder.

_Kaynak: Louhaichi, M., M. Borman ve D. Johnson. &quot;Buğday Üzerindeki Otlatma Etkilerinin Belgelendirilmesi için Mekansal Konumlandırılmış Platform ve Hava Fotoğrafçılığı.&quot; Geocarto International 16, Sayı 1 (2001): 65-70._

***

## GNDVI - Greene Normalize Edilmiş Bitki Örtüsü Endeksi

Bu endeks, kırmızı spektrum yerine 540 ila 570 nm arasındaki yeşil spektrumu ölçmesi dışında NDVI’ye benzerdir. Bu endeks, NDVI’ye kıyasla klorofil konsantrasyonuna daha duyarlıdır.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Kaynak: Gitelson, A. ve M. Merzlyak. &quot;Yüksek Bitki Yapraklarındaki Klorofil Konsantrasyonunun Uzaktan Algılanması.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - Yeşil Optimize Edilmiş Toprak Düzeltmeli Bitki Örtüsü Endeksi (Green Optimized Soil Adjusted Vegetation Index)

Bu endeks, başlangıçta mısırın azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. OSAVI’ye benzemekle birlikte, kırmızı bandın yerine yeşil bandı kullanır.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Kaynak: Sripada, R. ve ark. &quot;Havadan Renkli Kızılötesi Fotoğrafçılık Kullanılarak Mısır İçin Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, Kuzey Carolina Eyalet Üniversitesi, 2005._

***

## GRVI - Yeşil Oranlı Bitki Örtüsü Endeksi

Yeşil ve kırmızı yansıma değerleri, yaprak pigmentlerindeki değişikliklerden güçlü bir şekilde etkilendiği için bu endeks, orman taç katmanlarındaki fotosentetik hızlara duyarlıdır.

$$
GRVI = {NIR \over Green }
$$

_Kaynak: Sripada, R. ve ark. &quot;Mısırda Sezonun Erken Dönemindeki Azot İhtiyaçlarını Belirlemek için Havadan Renkli Kızılötesi Fotoğrafçılık.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Yeşil Toprak Düzeltmeli Bitki Örtüsü Endeksi (Green Soil Adjusted Vegetation Index)

Bu endeks, başlangıçta mısırın azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. SAVI’ye benzemekle birlikte, kırmızı bandın yerine yeşil bandı kullanır.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Kaynak: Sripada, R. ve ark. &quot;Havadan Renkli Kızılötesi Fotoğrafçılık Kullanılarak Mısır İçin Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, Kuzey Carolina Eyalet Üniversitesi, 2005._

***

## LAI - Yaprak Alan İndeksi

Bu indeks, yaprak örtüsünü tahmin etmek ve mahsulün büyümesini ve verimini öngörmek için kullanılır. ENVI, Boegh ve ark. (2002) tarafından geliştirilen aşağıdaki ampirik formülü kullanarak yeşil LAI değerini hesaplar:

$$
LAI = 3.618 * EVI - 0.118
$$

Burada EVI:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Yüksek LAI değerleri genellikle yaklaşık 0 ile 3,5 arasında değişir. Ancak, sahnede doymuş pikseller oluşturan bulutlar ve diğer parlak özellikler bulunduğunda, LAI değerleri 3,5&#x27;i aşabilir. İdeal olarak, bir LAI görüntüsü oluşturmadan önce sahnedeki bulutları ve parlak özellikleri maskelemelisiniz.

_Kaynak: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ve A. Thomsen. &quot;Tarımda Yaprak Alan İndeksi, Azot Konsantrasyonu ve Fotosentetik Verimliliği Ölçmek için Havadan Toplanan Çok Spektral Veriler.&quot; Remote Sensing of Environment 81, no. 2-3 (2002): 179-193._

***

## LCI - Yaprak Klorofil İndeksi

_Yalnızca GUI — CLI/SDK `--indices` ön ayarı olarak mevcut değildir._

Bu indeks, klorofil emiliminin neden olduğu yansıma değişimlerine duyarlı olan yüksek bitkilerdeki klorofil içeriğini tahmin etmek için kullanılır.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Kaynak: Datt, B. &quot;Eucalyptus Yapraklarındaki Su İçeriğinin Uzaktan Algılanması.&quot; Journal of Plant Physiology 154, no. 1 (1999): 30-36._

***

## MNLI - Modifiye Edilmiş Doğrusal Olmayan İndeks

Bu indeks, toprak arka planını hesaba katmak için Toprak Düzeltmeli Bitki Örtüsü İndeksi’ni (SAVI) içeren Doğrusal Olmayan İndeks’in (NLI) geliştirilmiş bir versiyonudur. ENVI, 0,5 değerinde bir bitki örtüsü arka plan düzeltme faktörü (_L_) kullanır.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Kaynak: Yang, Z., P. Willis ve R. Mueller. &quot;Bant Oranı İyileştirilmiş AWIFS Görüntüsünün Mahsul Sınıflandırma Doğruluğuna Etkisi.&quot; Pecora 17 Uzaktan Algılama Sempozyumu Bildirileri (2008), Denver, CO._

***

## MSAVI2 - Modifiye Edilmiş Toprak Düzeltmeli Bitki Örtüsü İndeksi 2

Bu indeks, Qi ve ark. (1994) tarafından önerilen ve Toprağa Uyarlanmış Bitki Örtüsü Endeksi’ni (SAVI) geliştiren MSAVI endeksinin daha basit bir versiyonudur. Toprak gürültüsünü azaltır ve bitki örtüsü sinyalinin dinamik aralığını artırır. MSAVI2, sağlıklı bitki örtüsünü vurgulamak için (SAVI’de olduğu gibi) sabit bir _L_ değeri kullanmayan tümevarımsal bir yönteme dayanır.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Kaynak: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ve S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## MSR - Modifiye Edilmiş Basit Oran

Bu endeks, biyofiziksel parametrelerle olan ilişkisini doğrusallaştırmak üzere tasarlanmış basit NIR/Red oranının bir modifikasyonudur ve yüksek bitki örtüsü yoğunluklarında NDVI’den daha duyarlıdır.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Kaynak: Chen, J. &quot;Boreal Uygulamalar için Bitki Örtüsü Endekslerinin ve Modifiye Edilmiş Basit Oranın Değerlendirilmesi.&quot; Canadian Journal of Remote Sensing 22 (1996): 229-242._

***

## NDRE - Normalleştirilmiş Fark RedEdge

Bu endeks NDVI&#x27;ye benzerdir ancak Red yerine NIR ile RedEdge arasındaki kontrastı karşılaştırır; bu da genellikle bitki örtüsü stresini daha erken tespit eder.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı, yeşil bitki örtüsünün bir ölçüsüdür. Normalleştirilmiş fark formülasyonu ile klorofilin en yüksek emilim ve yansıma bölgelerinin bir araya getirilmesi, endeksi çok çeşitli koşullarda sağlam hale getirir. Ancak, LAI değeri yüksek olduğunda yoğun bitki örtüsü koşullarında doygunluğa ulaşabilir.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Bu endeksin değeri -1 ile 1 arasında değişir. Yeşil bitki örtüsü için genel aralık 0,2 ile 0,8 arasındadır.

_Kaynak: Rouse, J., R. Haas, J. Schell ve D. Deering. ERTS ile Büyük Ovalar’daki Bitki Örtüsü Sistemlerinin İzlenmesi. Üçüncü ERTS Sempozyumu, NASA (1973): 309-317._

***

## NLI - Doğrusal Olmayan İndeks

Bu endeks, birçok bitki örtüsü endeksi ile yüzey biyofiziksel parametreleri arasındaki ilişkinin doğrusal olmadığını varsayar. Doğrusal olma eğiliminde olmayan yüzey parametreleriyle olan ilişkileri doğrusal hale getirir.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Kaynak: Goel, N. ve W. Qin. &quot;Çeşitli Bitki Örtüsü İndeksleri ile LAI ve Fpar Arasındaki İlişkiler Üzerine Taç Yapısının Etkileri: Bir Bilgisayar Simülasyonu.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - Optimize Edilmiş Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi

Bu endeks, Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi’ne (SAVI) dayanmaktadır. Taç örtüsü arka plan düzeltme faktörü için 0,16 standart değerini kullanır. Rondeaux (1996), bu değerin düşük bitki örtüsü için SAVI’ye kıyasla daha fazla toprak varyasyonu sağladığını, aynı zamanda %50’den fazla bitki örtüsüne karşı daha yüksek duyarlılık gösterdiğini belirlemiştir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünün arasından görülebildiği alanlarda en iyi şekilde kullanılır.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Kaynak: Rondeaux, G., M. Steven ve F. Baret. &quot;Toprak Düzeltmeli Bitki Örtüsü Endekslerinin Optimizasyonu.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - Yeniden Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı bitki örtüsünü vurgulamak için NDVI ile birlikte yakın kızılötesi ve kırmızı dalga boyları arasındaki farkı kullanır. Toprak ve güneşin görüş geometrisinin etkilerine karşı duyarsızdır.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Kaynak: Roujean, J. ve F. Breon. &quot;Çift Yönlü Yansıma Ölçümlerinden Bitki Örtüsünün Emdiği PAR&#x27;ın Tahmin Edilmesi.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks NDVI’ye benzerdir, ancak toprak piksellerinin etkilerini ortadan kaldırır. Bitki örtüsü yoğunluğunun bir fonksiyonu olan ve genellikle bitki örtüsü miktarlarına ilişkin ön bilgi gerektiren bir bitki örtüsü arka plan düzeltme faktörü olan _L_ kullanır. Huete (1988), birinci dereceden toprak arka plan değişimlerini hesaba katmak için _L_=0,5 değerinin optimal olduğunu önermektedir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünün arasından görülebildiği alanlarda en iyi şekilde kullanılır.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Kaynak: Huete, A. &quot;A Soil-Adjusted Vegetation Index (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - Dönüştürülmüş Fark Bitki Örtüsü Endeksi

Bu endeks, kentsel ortamlarda bitki örtüsünü izlemek için kullanışlıdır. NDVI ve SAVI gibi doygunluğa ulaşmaz.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Kaynak: Bannari, A., H. Asalhi ve P. Teillet. &quot;Bitki Örtüsü Haritalaması için Dönüştürülmüş Fark Bitki Örtüsü İndeksi (TDVI)&quot; Geoscience and Remote Sensing Symposium Bildirileri, IGARSS &#x27;02, IEEE International, Cilt 5 (2002)._

***

## VARI - Görünür Işınım Atmosferik Direnç Endeksi

Bu endeks, ARVI’ye dayanır ve atmosferik etkilere karşı düşük duyarlılıkla bir sahnedeki bitki örtüsü oranını tahmin etmek için kullanılır.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Kaynak: Gitelson, A., ve diğerleri. &quot;Görünür Spektral Uzayda Bitki Örtüsü ve Toprak Çizgileri: Bitki Örtüsü Oranının Uzaktan Tahminine Yönelik Bir Kavram ve Teknik. Uluslararası Uzaktan Algılama Dergisi 23 (2002): 2537−2562._

***

## WDRVI - Geniş Dinamik Aralık Bitki Örtüsü Endeksi

Bu endeks NDVI’ye benzerdir, ancak NDVI’ye yakın kızılötesi ve kırmızı sinyallerin katkıları arasındaki farkı azaltmak için bir ağırlık katsayısı (_a_) kullanır. WDRVI, NDVI’nin 0,6’yı aştığı orta ila yüksek bitki örtüsü yoğunluğuna sahip sahnelerde özellikle etkilidir. NDVI, bitki örtüsü oranı ve yaprak alanı indeksi (LAI) arttıkça sabit bir seviyeye gelme eğilimindeyken, WDRVI daha geniş bir bitki örtüsü oranı aralığına ve LAI&#x27;deki değişikliklere karşı daha duyarlıdır.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Ağırlık katsayısı (_a_) 0,1 ile 0,2 arasında değişebilir. Henebry, Viña ve Gitelson (2004) tarafından 0,2 değeri önerilmektedir.

_Kaynakça_

_Gitelson, A. &quot;Bitki Örtüsünün Biyofiziksel Özelliklerinin Uzaktan Nicelleştirilmesi için Geniş Dinamik Aralık Bitki Örtüsü Endeksi.&quot; Journal of Plant Physiology 161, Sayı 2 (2004): 165-173._

_Henebry, G., A. Viña ve A. Gitelson. &quot;Geniş Dinamik Aralık Bitki Örtüsü Endeksi ve Boşluk Analizi için Potansiyel Kullanımı.&quot; Gap Analysis Bulletin 12: 50-56._
