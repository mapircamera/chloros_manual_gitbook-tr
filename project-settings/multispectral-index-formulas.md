---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Çok Spektral İndeks Formülleri

Aşağıdaki indeks formülleri, Survey3 adresindeki filtrelerin ortalama geçirgenlik aralıklarının bir kombinasyonunu kullanır:

<table><thead><tr><th align="center">Survey3 Filtre Rengi</th><th width="196.199951171875" align="center">Survey3 Filtre Adı</th><th width="159.800048828125" align="center">Geçiş Aralığı (FWHM)</th><th align="center">Ortalama Geçirgenlik</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865 nm</td><td align="center">850 nm</td></tr></tbody></table>

Bu formüller kullanıldığında, adın sonu &quot;\_1&quot; veya &quot;\_2&quot; ile bitebilir; bu, hangi NIR filtresinin (NIR1 veya NIR2) kullanıldığına karşılık gelir.

LATTICE M3C (Bayer üçlü bant geçidi) kameralar için, aynı indeks motoru M3C filtre bantlarını kullanır:

| M3C filtresi | Bant 1 (merkez/ FWHM) | Bant 2 (merkez/ FWHM) | Bant 3 (merkez/ FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

LATTICE M3M kameraları tek bantlıdır (her kamera için bir dar bant filtresi), bu nedenle tek bir M3M görüntüsü için çok bantlı indeksler hesaplanmaz. M3M ile indeksleri hesaplamak için, iki veya daha fazla kamerayı hizalanmış bir çok bantlı yığın halinde birleştirin ve LATTICE endeks motorunu (`chloros-cli lattice index` veya GUI&#x27;nin canlı Endeks Hesaplayıcısı) kullanın.

***

## Her endeks adının nerede geçerli olduğu

Chloros&#x27;da **üç** endeks yüzeyi bulunur ve bunların ön ayar listeleri birbiriyle aynı değildir. Bu bölümü, bir adın kullanmayı planladığınız yerde geçerli olup olmadığını kontrol etmek için kullanın.

| Bulunduğunuz yer | Hangi liste geçerlidir | Sayı |
| --- | --- | --- |
| Proje Ayarları → Dizin → Dizin ekle (GUI) | Yüzey 1 | 27 |
| Görüntü Görüntüleyici [İndeks/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) (GUI) | Yüzey 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Yüzey 2 | 22 |
| SDK `process_folder(indices=[...])` | Yüzey 2 | 22 |
| `chloros-cli lattice index --preset` | Yüzey 3 | 22 (farklı bir 22) |
| Kameralar sekmesi canlı Dizin Hesaplayıcı | Surface 3 | 22 (farklı bir 22) |

Surface 1 ve 2, **tek bir kameradan gelen tek bir görüntü üzerinde**çalışır, ve o kameranın filtre kanallarına bağlı `x`/`y`/`z`(/`a`) sembol yuvalarını kullanır. Yüzey 3,**hizalanmış çok bantlı yığın** üzerinde çalışır — bir küp halinde eşleştirilmiş birkaç LATTICE kamerası — ve kanallara küçük harfli adlarıyla başvurur.

### 1. GUI Proje Ayarları / Görüntü Görüntüleyici sandbox açılır menüsü — 27 formül

Açılır menü, formülleri şu sırayla listeler (bu, alfabetik sıraya göre değil, eklenme sırasına göredir):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

GUI&#x27;de kameranızın filtre kanallarını formülün bant yuvalarına sürükleyebilirsiniz; böylece herhangi bir formül, kameranızın desteklediği herhangi bir bant atamasıyla kullanılabilir. Kaydettiğiniz özel formüller bu listenin altına eklenir.

**Yalnızca GUI&#x27;ye özel** beş formül — CLI / SDK `--indices` listesinin kabul etmediği formüller — şu şekilde uygulanır:

| Yalnızca GUI&#x27;ye özel ön ayar | Formül (uygulandığı şekliyle) | Yuvalar |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (dört yuva) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

Her biri için amaçlanan eşleme, bu sayfanın ilerleyen kısımlarında kendi bölümünde verilmiştir (örneğin, GARI, x= Green, y= NIR, z= Blue, a= Red değerlerini bekler). GARI, Chloros içindeki dördüncü yuvayı kullanan tek formüldür.

### 2. CLI / SDK `--indices` ad genişletme — 22 ön ayar

`chloros-cli process --indices` seçeneği (ve SDK `indices` parametresi) şu ön ayar adlarını kabul eder:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**Bilinmeyen dizin adları sessizce atlanır.** Bu listenin dışında kalan bir ad (beş GUIformülleri `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` ve GUI’de kaydettiğiniz herhangi bir özel formül dahil) yalnızca bir günlük bildirimi ile atlanır — çalıştırma o dizin olmadan devam eder ve çalıştırma işlemi yine de başarılı olarak raporlanır. Bildirim şu şekilde yazdırılır:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

Adlar, boşluklar temizlendikten sonra büyük/küçük harf duyarlı olmaksızın eşleştirilir; dolayısıyla `ndvi`, `NDVI` ve ` NDVI ` aynı ön ayardır. Ayrıca, bir ön ayar, kameranızın filtresinin sağlamadığı bir bant gerektiriyorsa da atlanır.
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
| CVI | `(z/y)/(x/y)` | RGB | Red, Green, Blue |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | RGB | Red, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### Ön ayar adı nasıl kanal konumlarına dönüşür?

`NDVI` gibi sadece isim geçtiğinizde, Chloros her bir sembolün hangi dosyanın hangi kanalını okuyacağına karar vermelidir. Bunun için, bir filtre kodunu her kanalın dizi konumuna eşleyen şu tabloyu kullanır:

| Filtre kodu | Kanal → dizi indeksi |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red`, Orange için bir takma ad olarak kabul edilir, aynı zamanda 0) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

Ön ayarın **varsayılan filtresi** (yukarıdaki &quot;Varsayılan filtre&quot; sütunu), projede o filtreye sahip görüntüler olduğunda kullanılır. Eğer yoksa, Chloros, `RGN, OCN, NGB, RGB, RE, NIR` sırasına göre projede bulunan filtreleri tarar ve ön ayarın ihtiyaç duyduğu her kanalı sağlayabilen ilk filtreyi seçer. Hiçbiri karşılayamazsa, ön ayar o çalıştırma için atılır. Bu nedenle, yalnızca OCN verisi seti üzerinde istenen `NDVI` yine de mantıklı bir sonuç üretir — bu, OCN adresindeki Orange ve NIR konumlarına bağlanır.

LATTICE M3C model dizeleri, `F` önekine sahip filtreyi taşır (`LATT-M3C-L41-FRGN`), ancak filtre kodu görüntüden okunduğunda önek atılır; bu nedenle bir FRGN kamerası yukarıdaki `RGN` satırını çözümler ve özel bir işlem gerektirmez.

### 3. LATTICE indeks motoru (`lattice index --preset`, canlı İndeks Hesaplayıcı) — 22 ön ayar

LATTICE motoru, hizalanmış çok bantlı yığınlar (canlı diziler veya dışa aktarılmış çok bantlı TIFF&#x27;ler) üzerinde çalışır ve küçük harfli kanal adları kullanır (`red`, `green`, `blue`, `red_edge`, `nir`). Ön ayar listesi yukarıdaki ikisinden farklıdır:

| Ön Ayar | Formül | Kanallar |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | kırmızı, nir |
| GNDVI | `(nir - green) / (nir + green)` | yeşil, nir |
| BNDVI | `(nir - blue) / (nir + blue)` | mavi, nir |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | kırmızı\_kenar, nir |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | mavi, yeşil, nir |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | kırmızı, nir |
| OSAVI | `1.5 * (nir - red) / (nir + red + 0.16)` | kırmızı, nir |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | kırmızı, nir |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | mavi, kırmızı, nir |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | kırmızı, nir |
| CVI | `(nir / green) - (red / green)` | kırmızı, yeşil, nir |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | kırmızı, nir |
| TDVI | `sqrt((nir - red) / (nir + red) + 0.5)` | kırmızı, nir |
| LAI | `3.618 * ((nir - red) / (nir + 6*red - 7.5*green + 1)) - 0.118` | kırmızı, yeşil, NIR |
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

## CVI

GUI&#x27;de ve CLI / SDK ön ayar listesinde uygulandığı gibi, CVI oranların oranı formülüdür:

$$
CVI = {(z / y) \over (x / y)}
$$

varsayılan RGB kanal eşlemesinde x= Red, y= Green, z= Blue şeklindedir. GUI&#x27;de kameranızın herhangi bir kanalını x/y/z yuvalarına sürükleyebilirsiniz. LATTICE indeks motorunun `CVI` ön ayarının farklı bir formül (`(NIR / Green) - (Red / Green)`) kullandığını unutmayın — kullandığınız yüzey için yukarıdaki tablolara bakın.

***

## ENDVI - Geliştirilmiş Normalleştirilmiş Bitki Örtüsü Fark Endeksi

Bu endeks, NIR ve yeşil kanallara ek olarak mavi kanalı da kullanır ve mavi bandın kırmızı bandın yerini aldığı NGB filtreli kameralar arasında popülerdir.

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

Uygulama, `((x+y)-(2*z))/((x+y)+(2*z))` sembol formülüdür — kameranızın NIR ve Green kanallarını x/y yuvalarına, Blue kanalını ise z yuvasına atayın (NGB kamera için: x= NIR, y= Green, z= Blue).

***

## EVI - Geliştirilmiş Bitki Örtüsü Endeksi

Bu endeks, başlangıçta MODIS verileriyle kullanılmak üzere, yüksek yaprak alanı endeksine (LAI) sahip alanlarda bitki örtüsü sinyalini optimize ederek NDVI endeksini iyileştirmek amacıyla geliştirilmiştir. Bu endeks, NDVI endeksinin doymuş hale gelebileceği yüksek LAI bölgelerinde en yararlıdır. Toprak arka plan sinyallerini düzeltmek ve aerosol saçılımı dahil atmosferik etkileri azaltmak için mavi yansıma bölgesini kullanır.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI değerleri, bitki örtüsü pikselleri için 0 ile 1 arasında olmalıdır. Bulutlar ve beyaz binalar gibi parlak özellikler ile su gibi koyu özellikler, EVI görüntüsünde anormal piksel değerlerine neden olabilir. EVI görüntüsü oluşturmadan önce, yansıma görüntüsünden bulutları ve parlak özellikleri maskelemeli ve isteğe bağlı olarak piksel değerlerini 0 ile 1 arasında eşiklemelisiniz.

_Kaynak: Huete, A. ve ark. “MODIS Bitki Örtüsü Endekslerinin Radyometrik ve Biyofiziksel Performansına Genel Bakış.” Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Orman Örtüsü Endeksi 1

_Yalnızca GUI — CLI / SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, kırmızı kenar bandını içeren multispektral yansıma görüntülerini kullanarak orman taçlarını diğer bitki örtüsü türlerinden ayırır.

$$
FCI1 = Red * RedEdge
$$

Ormanlık alanlar, ağaçların daha düşük yansıma değerine sahip olması ve taç katmanındaki gölgelerin varlığı nedeniyle daha düşük FCI1 değerlerine sahip olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Multispektral görüntüler için sağlam orman örtüsü indeksleri.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - Orman Örtüsü Endeksi 2

_Yalnızca GUI — CLI / SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, kırmızı kenar bandı içermeyen multispektral yansıma görüntülerini kullanarak orman taçlarını diğer bitki örtüsü türlerinden ayırır.

$$
FCI2 = Red * NIR
$$

Ağaçların daha düşük yansıma değeri ve taç katmanındaki gölgelerin varlığı nedeniyle, ormanlık alanların FCI2 değerleri daha düşük olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Çok spektral görüntüler için sağlam orman örtüsü endeksleri.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## GEMI - Küresel Çevre İzleme Endeksi

_Yalnızca GUI — CLI / SDK `--indices` ön ayarı olarak mevcut değildir._

Bu doğrusal olmayan bitki örtüsü endeksi, uydu görüntülerinden küresel çevre izleme amacıyla kullanılır ve atmosferik etkileri düzeltmeye çalışır. NDVI endeksine benzer, ancak atmosferik etkilere karşı daha az duyarlıdır. Çıplak topraktan etkilenir; bu nedenle, bitki örtüsünün seyrek veya orta yoğunlukta olduğu alanlarda kullanılması önerilmez.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Burada:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Kaynak: Pinty, B. ve M. Verstraete. “GEMI: Uydulardan Küresel Bitki Örtüsünü İzlemek İçin Doğrusal Olmayan Bir Endeks.” Vegetation 101 (1992): 15-20._

***

## GARI - Green Atmosferik Etkilere Dayanıklı Endeks

_Yalnızca GUI — CLI / SDK `--indices` ön ayarı olarak mevcut değildir._

Bu endeks, NDVI endeksine kıyasla çok çeşitli klorofil konsantrasyonlarına daha duyarlıdır ve atmosferik etkilere karşı daha az duyarlıdır.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama sabiti, atmosferdeki aerosol koşullarına bağlı bir ağırlıklandırma fonksiyonudur. ENVI, Gitelson, Kaufman ve Merzylak (1996, sayfa 296) tarafından önerilen 1,7 değerini kullanır.

_Kaynak: Gitelson, A., Y. Kaufman ve M. Merzylak. &quot;EOS-MODIS&#x27;dan Küresel Bitki Örtüsünün Uzaktan Algılanmasında &#x27;Green&#x27; Kanalının Kullanımı.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI - Green Klorofil Endeksi

Bu indeks, çok çeşitli bitki türlerinde yaprak klorofil içeriğini tahmin etmek için kullanılır.

$$
GCI = {NIR \over Green} - 1
$$

Geniş NIR ve yeşil dalga boylarına sahip olmak, klorofil içeriğinin daha iyi tahmin edilmesini sağlarken, daha fazla hassasiyet ve daha yüksek sinyal-gürültü oranı sunar.

_Kaynak: Gitelson, A., Y. Gritz ve M. Merzlyak. &quot;Yaprak Klorofil İçeriği ile Spektral Yansıtma Arasındaki İlişkiler ve Yüksek Bitki Yapraklarında Tahribatsız Klorofil Değerlendirmesi için Algoritmalar.&quot; Journal of Plant Physiology 160 (2003): 271-282._

***

## GLI - Green Yaprak İndeksi

Bu indeks, başlangıçta buğday örtüsünü ölçmek üzere dijital RGB kamera ile kullanılmak üzere tasarlanmıştır; burada kırmızı, yeşil ve mavi dijital sayılar (DN) değerlerinin 0 ile 255 arasında değiştiği durumlarda bu indeks tasarlanmıştır.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI değerleri -1 ile +1 arasında değişir. Negatif değerler toprağı ve cansız özellikleri temsil ederken, pozitif değerler yeşil yaprakları ve sapları temsil eder.

_Kaynak: Louhaichi, M., M. Borman ve D. Johnson. &quot;Buğday Üzerindeki Otlatma Etkilerinin Belgelenmesi için Mekansal Konumlandırılmış Platform ve Hava Fotoğrafçılığı.&quot; Geocarto International 16, Sayı 1 (2001): 65-70._

***

## GNDVI - Green Normalleştirilmiş Farklı Bitki Örtüsü Endeksi

Bu endeks, kırmızı spektrum yerine 540 ile 570 nm arasındaki yeşil spektrumu ölçmesi dışında NDVI endeksine benzerdir. Bu endeks, NDVI endeksine kıyasla klorofil konsantrasyonuna karşı daha duyarlıdır.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Kaynak: Gitelson, A. ve M. Merzlyak. &quot;Yüksek Bitki Yapraklarındaki Klorofil Konsantrasyonunun Uzaktan Algılanması.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - Green Optimize Edilmiş Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks, başlangıçta mısırın azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. OSAVI endeksine benzerdir, ancak yeşil bandı kırmızı bandla değiştirir.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Kaynak: Sripada, R. ve ark. &quot;Havadan Renkli Kızılötesi Fotoğrafçılık Kullanılarak Mısır İçin Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, Kuzey Carolina Devlet Üniversitesi, 2005._

***

## GRVI - Green Oranlı Bitki Örtüsü Endeksi

Yeşil ve kırmızı yansıma değerleri, yaprak pigmentlerindeki değişikliklerden güçlü bir şekilde etkilendiğinden, bu endeks orman taç katmanlarındaki fotosentetik hızlara duyarlıdır.

$$
GRVI = {NIR \over Green }
$$

_Kaynak: Sripada, R., ve ark. &quot;Mısırda Sezonun Erken Dönemindeki Azot İhtiyaçlarını Belirlemek için Havadan Renkli Kızılötesi Fotoğrafçılık.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Green Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks, başlangıçta mısırın azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılık kullanılarak tasarlanmıştır. Bu endeks, SAVI ile benzerdir, ancak kırmızı bandın yerine yeşil bandı kullanır.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Kaynak: Sripada, R. ve ark. &quot;Havadan Renkli Kızılötesi Fotoğrafçılık Kullanılarak Mısır İçin Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, Kuzey Carolina Eyalet Üniversitesi, 2005._

***

## LAI - Yaprak Alan Endeksi

Bu indeks, yaprak örtüsünü tahmin etmek ve mahsulün büyümesini ve verimini öngörmek için kullanılır. ENVI, Boegh ve ark. (2002) tarafından önerilen aşağıdaki ampirik formülü kullanarak yeşil LAI değerini hesaplar:

$$
LAI = 3.618 * EVI - 0.118
$$

Burada EVI şu şekildedir:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Yüksek LAI değerleri genellikle yaklaşık 0 ile 3,5 arasında değişir. Ancak, sahnede doymuş pikseller oluşturan bulutlar ve diğer parlak öğeler bulunuyorsa, LAI değerleri 3,5&#x27;i aşabilir. İdeal olarak, LAI görüntüsünü oluşturmadan önce sahnedeki bulutları ve parlak öğeleri maskelemelisiniz.

_Kaynak: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ve A. Thomsen. &quot;Tarımda Yaprak Alan İndeksi, Azot Konsantrasyonu ve Fotosentetik Verimliliğin Nicelendirilmesi için Havadan Toplanan Multispektral Veriler.&quot; Remote Sensing of Environment 81, no. 2-3 (2002): 179-193._

***

## LCI - Yaprak Klorofil İndeksi

_Yalnızca GUI — CLI / SDK `--indices` ön ayarı olarak mevcut değildir._

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

## MSAVI2 - Modifiye Edilmiş Toprak Düzeltmeli Bitki Örtüsü Endeksi 2

Bu endeks, Qi ve ark. (1994) tarafından önerilen ve Toprak Düzeltmeli Bitki Örtüsü Endeksi’ni (SAVI) geliştiren MSAVI endeksinin daha basit bir versiyonudur. Toprak gürültüsünü azaltır ve bitki örtüsü sinyalinin dinamik aralığını artırır. MSAVI2, sağlıklı bitki örtüsünü vurgulamak için (SAVI&#x27;de olduğu gibi) sabit bir _L_ değeri kullanmayan bir tümevarımsal yönteme dayanır.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Kaynak: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ve S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## MSR - Modifiye Edilmiş Basit Oran

Bu endeks, biyofiziksel parametrelerle olan ilişkisini doğrusallaştırmak üzere tasarlanmış basit NIR / Red oranının bir modifikasyonudur ve yüksek bitki örtüsü yoğunluklarında NDVI&#x27;dan daha duyarlıdır.

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_Kaynak: Chen, J. &quot;Boreal Uygulamalar için Bitki Örtüsü Endekslerinin ve Modifiye Edilmiş Basit Oranın Değerlendirilmesi.&quot; Canadian Journal of Remote Sensing 22 (1996): 229-242._

***

## NDRE - Normalleştirilmiş Fark RedEdge

Bu endeks, NDVI endeksine benzerdir ancak NIR ile Red yerine RedEdge arasındaki kontrastı karşılaştırır; bu, bitki örtüsündeki stresi genellikle daha erken tespit eder.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı, yeşil bitki örtüsünün bir ölçüsüdür. Normalleştirilmiş fark formülasyonu ile klorofilin en yüksek emilim ve yansıma bölgelerinin bir araya getirilmesi, endeksi çok çeşitli koşullarda sağlam hale getirir. Ancak, LAI değeri yüksek olduğunda, yoğun bitki örtüsü koşullarında doygunluğa ulaşabilir.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Bu endeksin değeri -1 ile 1 arasında değişir. Yeşil bitki örtüsü için genel aralık 0,2 ile 0,8 arasındadır.

_Kaynak: Rouse, J., R. Haas, J. Schell ve D. Deering. ERTS ile Büyük Ovalar&#x27;da Bitki Örtüsü Sistemlerinin İzlenmesi. Üçüncü ERTS Sempozyumu, NASA (1973): 309-317._

***

## NLI - Doğrusal Olmayan İndeks

Bu endeks, birçok bitki örtüsü endeksi ile yüzey biyofiziksel parametreleri arasındaki ilişkinin doğrusal olmadığını varsayar. Doğrusal olma eğiliminde olmayan yüzey parametreleriyle olan ilişkileri doğrusal hale getirir.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Kaynak: Goel, N. ve W. Qin. &quot;Çeşitli Bitki Örtüsü İndeksleri ile LAI ve Fpar Arasındaki İlişkiler Üzerinde Taç Yapısının Etkileri: Bir Bilgisayar Simülasyonu.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - Optimize Edilmiş Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi

Bu endeks, Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi’ne (SAVI) dayanmaktadır. Taç örtüsü arka plan düzeltme faktörü için 0,16 standart değerini kullanır. Rondeaux (1996), bu değerin düşük bitki örtüsü için SAVI&#x27;dan daha fazla toprak varyasyonu sağladığını, aynı zamanda %50&#x27;den fazla bitki örtüsüne karşı daha yüksek duyarlılık gösterdiğini belirlemiştir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünün arasından görülebildiği alanlarda en iyi şekilde kullanılır.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Kaynak: Rondeaux, G., M. Steven ve F. Baret. &quot;Toprak Düzeltmeli Bitki Örtüsü Endekslerinin Optimizasyonu.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - Yeniden Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı bitki örtüsünü vurgulamak için yakın kızılötesi ve kırmızı dalga boyları arasındaki farkı ve &quot;NDVI&quot; değerini kullanır. Toprak ve güneşin görüş geometrisinin etkilerine karşı duyarsızdır.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Kaynak: Roujean, J. ve F. Breon. &quot;Çift Yönlü Yansıtma Ölçümlerinden Bitki Örtüsünün Emdiği PAR’ın Tahmin Edilmesi.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi

Bu endeks, Bitki Örtüsü İndeksi (NDVI) ile benzerdir, ancak toprak piksellerinin etkilerini ortadan kaldırır. Bitki örtüsü yoğunluğunun bir fonksiyonu olan ve genellikle bitki örtüsü miktarlarına ilişkin ön bilgi gerektiren bir bitki örtüsü arka plan ayarlama faktörü olan _L_ kullanır. Huete (1988), birinci dereceden toprak arka plan varyasyonlarını hesaba katmak için _L_=0,5 değerinin optimal olduğunu önermektedir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünün arasından görülebildiği alanlarda en iyi şekilde kullanılır.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Kaynak: Huete, A. &quot;Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - Dönüştürülmüş Fark Bitki Örtüsü İndeksi

Bu indeks, kentsel ortamlarda bitki örtüsünü izlemek için kullanışlıdır. NDVI ve SAVI gibi doygunluğa uğramaz.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Kaynak: Bannari, A., H. Asalhi ve P. Teillet. &quot;Bitki Örtüsü Haritalaması için Dönüştürülmüş Fark Bitki Örtüsü Endeksi (TDVI)&quot; Geoscience and Remote Sensing Symposium Bildirileri, IGARSS &#x27;02, IEEE International, Cilt 5 (2002)._

***

## VARI - Görünür Işıkta Atmosferik Etkilere Dayanıklı İndeks

Bu indeks, Atmosferik Etkilerden Arındırılmış Bitki Örtüsü İndeksi (ARVI) temel alınarak oluşturulmuştur ve atmosferik etkilere karşı düşük duyarlılıkla bir sahnedeki bitki örtüsü oranını tahmin etmek için kullanılır.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Kaynak: Gitelson, A. ve ark. &quot;Görünür Spektral Uzayda Bitki Örtüsü ve Toprak Çizgileri: Bitki Örtüsü Oranının Uzaktan Tahminine Yönelik Bir Kavram ve Teknik. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI - Geniş Dinamik Aralık Bitki Örtüsü Endeksi

Bu endeks, NDVI endeksine benzerdir, ancak yakın kızılötesi ve kırmızı sinyallerin NDVI&#x27;a yaptığı katkı arasındaki farkı azaltmak için bir ağırlık katsayısı (_a_) kullanır. WDRVI, NDVI değeri 0&#x27;ı aştığında orta ila yüksek bitki örtüsü yoğunluğuna sahip sahnelerde özellikle etkilidir.6. Bitki örtüsü oranı (NDVI) ve yaprak alanı indeksi (LAI) arttıkça bitki örtüsü oranı (ve yaprak alanı indeksi) sabit bir seviyeye yaklaşma eğilimindeyken, bitki örtüsü yoğunluğu (WDRVI) daha geniş bir bitki örtüsü oranı aralığına ve bitki örtüsü yoğunluğundaki (LAI) değişikliklere karşı daha duyarlıdır.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Ağırlık katsayısı (_a_) 0,1 ile 0,2 arasında değişebilir. Henebry, Viña ve Gitelson (2004) tarafından 0,2 değeri önerilmektedir.

_Kaynakça_

_Gitelson, A. &quot;Bitki Örtüsünün Biyofiziksel Özelliklerinin Uzaktan Nicelleştirilmesi için Geniş Dinamik Aralık Bitki Örtüsü Endeksi.&quot; Journal of Plant Physiology 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña ve A. Gitelson. &quot;Geniş Dinamik Aralık Bitki Örtüsü Endeksi ve Boşluk Analizi için Potansiyel Kullanımı.&quot; Gap Analysis Bulletin 12: 50-56._
