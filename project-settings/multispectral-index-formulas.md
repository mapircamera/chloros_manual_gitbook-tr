---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Çok Spektral Endeks Formülleri

Aşağıdaki endeks formülleri, Survey3 filtresinin ortalama geçirgenlik aralıklarının bir kombinasyonunu kullanır:

<table><thead><tr><th align="center">Survey3 Filtre Rengi</th><th width="196.199951171875" align="center">Survey3 Filtre Adı</th><th width="159.800048828125" align="center">Geçiş Aralığı (FWHM)</th><th align="center">Ortalama Geçiş</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483 nm</td><td align="center">475 nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558 nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668 nm</td><td align="center">661 nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865 nm</td><td align="center">850 nm</td></tr></tbody></table>Bu formüller kullanıldığında, adın sonu &quot;\_1&quot; veya &quot;\_2&quot; ile biter; bu, hangi NIR filtresinin, yani NIR1 veya NIR2&#x27;in kullanıldığına karşılık gelir.

***

## EVI - Geliştirilmiş Bitki Örtüsü Endeksi

Bu endeks, yüksek yaprak alanı indeksine sahip bölgelerde bitki örtüsü sinyalini optimize ederek NDVI&#x27;e göre bir iyileştirme sağlamak amacıyla, başlangıçta MODIS verileriyle kullanılmak üzere geliştirilmiştir (LAI). Bu endeks, NDVI&#x27;in doygunluğa ulaşabileceği yüksek LAI bölgelerinde en kullanışlıdır. Mavi yansıma bölgesini kullanarak toprak arka plan sinyallerini düzeltir ve aerosol saçılımı dahil atmosferik etkileri azaltır.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI değerleri, bitki örtüsü pikselleri için 0 ile 1 arasında olmalıdır. Bulutlar ve beyaz binalar gibi parlak özellikler ile su gibi karanlık özellikler, bir EVI görüntüsünde anormal piksel değerlerine neden olabilir. Bir EVI görüntüsü oluşturmadan önce, yansıma görüntüsünden bulutları ve parlak özellikleri maskelemeli ve isteğe bağlı olarak piksel değerlerini 0 ile 1 arasında eşiklemelisiniz.

_Kaynak: Huete, A., et al. &quot;MODIS Bitki Örtüsü Endekslerinin Radyometrik ve Biyofiziksel Performansına Genel Bakış.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Orman Örtüsü Endeksi 1

Bu endeks, kırmızı kenar bandını içeren multispektral yansıma görüntülerini kullanarak orman örtüsünü diğer bitki örtüsü türlerinden ayırır.

$$
FCI1 = Red * RedEdge
$$

Ormanlık alanlar, ağaçların daha düşük yansıma değerleri ve örtü içindeki gölgelerin varlığı nedeniyle daha düşük FCI1 değerlerine sahip olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Multispektral görüntüler için sağlam orman örtüsü endeksleri.&quot; Fotogrametrik Mühendislik ve Uzaktan Algılama 84.8 (2018): 505-512._

***

## FCI2 - Orman Örtüsü Endeksi 2

Bu endeks, kırmızı kenar bandı içermeyen multispektral yansıma görüntülerini kullanarak orman taçlarını diğer bitki örtüsü türlerinden ayırır.

$$
FCI2 = Red * NIR
$$

Ormanlık alanlar, ağaçların daha düşük yansıma değeri ve taç yapısı içindeki gölgelerin varlığı nedeniyle daha düşük FCI2 değerlerine sahip olacaktır.

_Kaynak: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Multispektral görüntüler için sağlam orman örtüsü endeksleri.&quot; Fotogrametrik Mühendislik ve Uzaktan Algılama 84.8 (2018): 505-512._

***

## GEMI - Küresel Çevre İzleme Endeksi

Bu doğrusal olmayan bitki örtüsü endeksi, uydu görüntülerinden küresel çevre izleme için kullanılır ve atmosferik etkileri düzeltmeye çalışır. NDVI&#x27;e benzer, ancak atmosferik etkilere karşı daha az duyarlıdır. Çıplak topraktan etkilenir; bu nedenle, seyrek veya orta yoğunlukta bitki örtüsünün bulunduğu alanlarda kullanılması önerilmez.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Kaynak:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Referans: Pinty, B. ve M. Verstraete. GEMI: Uydulardan Küresel Bitki Örtüsünü İzlemek için Doğrusal Olmayan Bir Endeks. Vegetation 101 (1992): 15-20._

***

## GARI - Green Atmosferik Dirençli Endeks

Bu endeks, NDVI&#x27;e kıyasla çok çeşitli klorofil konsantrasyonlarına daha duyarlıdır ve atmosferik etkilere karşı daha az duyarlıdır.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama sabiti, atmosferdeki aerosol koşullarına bağlı bir ağırlıklandırma fonksiyonudur. ENVI, Gitelson, Kaufman ve Merzylak (1996, sayfa 296) tarafından önerilen değer olan 1,7&#x27;yi kullanır.

_Kaynak: Gitelson, A., Y. Kaufman ve M. Merzylak. &quot;EOS-MODIS&#x27;ten Küresel Bitki Örtüsünün Uzaktan Algılanmasında bir Green Kanalının Kullanımı.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI - Green Klorofil Endeksi

Bu endeks, çok çeşitli bitki türlerinde yaprak klorofil içeriğini tahmin etmek için kullanılır.

$$
GCI = {NIR \over Green} - 1
$$

Geniş NIR ve yeşil dalga boylarına sahip olmak, klorofil içeriğinin daha iyi tahmin edilmesini sağlarken, daha fazla hassasiyet ve daha yüksek bir sinyal-gürültü oranı sunar.

_Kaynak: Gitelson, A., Y. Gritz ve M. Merzlyak. &quot;Yaprak Klorofil İçeriği ile Spektral Yansıma Arasındaki İlişkiler ve Yüksek Bitki Yapraklarında Tahribatsız Klorofil Değerlendirmesi için Algoritmalar.&quot; Journal of Plant Physiology 160 (2003): 271-282._

***

## GLI - Green Yaprak Endeksi

Bu endeks, başlangıçta buğday örtüsünü ölçmek üzere dijital bir RGB kamera ile kullanılmak üzere tasarlanmıştır; burada kırmızı, yeşil ve mavi dijital sayılar (DN&#x27;ler) 0 ile 255 arasında değişmektedir.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI değerleri -1 ile +1 arasında değişmektedir. Negatif değerler toprağı ve cansız özellikleri temsil ederken, pozitif değerler yeşil yaprakları ve sapları temsil eder.

_Kaynak: Louhaichi, M., M. Borman ve D. Johnson. &quot;Buğday Üzerindeki Otlatma Etkilerinin Belgelenmesi için Mekansal Konumlandırılmış Platform ve Hava Fotoğrafçılığı.&quot; Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Green Normalleştirilmiş Farklılık Bitki Örtüsü Endeksi

Bu indeks, kırmızı spektrum yerine 540 ila 570 nm arasındaki yeşil spektrumu ölçmesi dışında NDVI&#x27;e benzerdir. Bu indeks, NDVI&#x27;e göre klorofil konsantrasyonuna daha duyarlıdır.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Kaynak: Gitelson, A. ve M. Merzlyak. &quot;Yüksek Bitki Yapraklarındaki Klorofil Konsantrasyonunun Uzaktan Algılanması.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - Green Optimize Edilmiş Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks, başlangıçta mısırın azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. OSAVI&#x27;e benzerdir, ancak yeşil bandı kırmızı ile değiştirir.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Kaynak: Sripada, R., et al. &quot;Hava Renkli Kızılötesi Fotoğrafçılık Kullanarak Mısır İçin Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, North Carolina State University, 2005._

***

## GRVI - Green Oranlı Bitki Örtüsü Endeksi

Yeşil ve kırmızı yansıma değerleri yaprak pigmentlerindeki değişikliklerden büyük ölçüde etkilendiği için, bu indeks orman örtüsündeki fotosentetik oranlara duyarlıdır.

$$
GRVI = {NIR \over Green }
$$

_Kaynak: Sripada, R., et al. &quot;Mısırda Sezonun Erken Dönemindeki Azot İhtiyacını Belirlemek için Havadan Renkli Kızılötesi Fotoğrafçılık.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Green Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi

Bu endeks, mısır için azot ihtiyacını tahmin etmek amacıyla renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. SAVI&#x27;e benzer, ancak kırmızı bandın yerine yeşil bandı kullanır.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Kaynak: Sripada, R., et al. &quot;Hava Renkli Kızılötesi Fotoğrafçılık Kullanarak Mısır için Sezon İçi Azot İhtiyaçlarının Belirlenmesi.&quot; Doktora tezi, North Carolina State University, 2005._

***

## LAI - Yaprak Alan Endeksi

Bu endeks, yaprak örtüsünü tahmin etmek ve mahsulün büyümesini ve verimini öngörmek için kullanılır. ENVI, Boegh ve ark. (2002) tarafından geliştirilen aşağıdaki ampirik formülü kullanarak yeşil LAI değerini hesaplar:

$$
LAI = 3.618 * EVI - 0.118
$$

Burada EVI:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Yüksek LAI değerleri genellikle yaklaşık 0 ile 3,5 arasında değişir. Ancak, sahnede doymuş pikseller üreten bulutlar ve diğer parlak özellikler varsa, LAI değerleri 3,5&#x27;i aşabilir. İdeal olarak, bir LAI görüntüsü oluşturmadan önce sahnedeki bulutları ve parlak özellikleri maskelemelisiniz.

_Kaynak: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ve A. Thomsen. &quot;Tarımda Yaprak Alan İndeksi, Azot Konsantrasyonu ve Fotosentetik Verimliliği Ölçmek için Havadan Alınan Çok Spektral Veriler.&quot; Remote Sensing of Environment 81, no. 2-3 (2002): 179-193._

***

## LCI - Yaprak Klorofil İndeksi

Bu indeks, klorofil emiliminin neden olduğu yansıma değişimlerine duyarlı olan yüksek bitkilerdeki klorofil içeriğini tahmin etmek için kullanılır.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Kaynak: Datt, B. &quot;Eucalyptus Yapraklarındaki Su İçeriğinin Uzaktan Algılanması.&quot; Journal of Plant Physiology 154, no. 1 (1999): 30-36._

***

## MNLI - Modifiye Edilmiş Doğrusal Olmayan İndeks

Bu indeks, toprak arka planını hesaba katmak için Toprak Düzeltmeli Bitki Örtüsü İndeksi&#x27;ni (SAVI) içeren Doğrusal Olmayan İndeks&#x27;in (NLI) geliştirilmiş bir versiyonudur. ENVI, 0,5 değerinde bir bitki örtüsü arka plan düzeltme faktörü (_L_) kullanır.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Kaynak: Yang, Z., P. Willis ve R. Mueller. &quot;Bant Oranı İyileştirilmiş AWIFS Görüntüsünün Mahsul Sınıflandırma Doğruluğuna Etkisi.&quot; Pecora 17 Uzaktan Algılama Sempozyumu Bildirileri (2008), Denver, CO._

***

## MSAVI2 - Modifiye Edilmiş Toprağa Uyarlanmış Bitki Örtüsü Endeksi 2

Bu endeks, Qi ve ark. (1994) tarafından önerilen ve Toprak Düzeltmeli Bitki Örtüsü Endeksi&#x27;ni (SAVI) geliştiren MSAVI endeksinin daha basit bir versiyonudur. Toprak gürültüsünü azaltır ve bitki örtüsü sinyalinin dinamik aralığını artırır. MSAVI2, sağlıklı bitki örtüsünü vurgulamak için (SAVI&#x27;te olduğu gibi) sabit bir _L_ değeri kullanmayan bir tümevarım yöntemine dayanır.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Kaynak: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ve S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## NDRE- Normalize Edilmiş Fark RedEdge

Bu endeks NDVI&#x27;e benzerdir, ancak Red yerine NIR ile RedEdge arasındaki kontrastı karşılaştırır; bu, bitki örtüsü stresini genellikle daha erken tespit eder.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı, yeşil bitki örtüsünün bir ölçüsüdür. Normalleştirilmiş fark formülasyonu ile klorofilin en yüksek emilim ve yansıma bölgelerinin birleşimi, onu çok çeşitli koşullarda sağlam hale getirir. Ancak, LAI yüksek olduğunda yoğun bitki örtüsü koşullarında doygunluğa ulaşabilir.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Bu endeksin değeri -1 ile 1 arasında değişir. Yeşil bitki örtüsü için genel aralık 0,2 ile 0,8 arasındadır.

_Kaynak: Rouse, J., R. Haas, J. Schell ve D. Deering. ERTS ile Büyük Ovalar&#x27;daki Bitki Örtüsü Sistemlerinin İzlenmesi. Üçüncü ERTS Sempozyumu, NASA (1973): 309-317._

***

## NLI - Doğrusal Olmayan Endeks

Bu endeks, birçok bitki örtüsü endeksi ile yüzey biyofiziksel parametreleri arasındaki ilişkinin doğrusal olmadığını varsayar. Doğrusal olma eğiliminde olmayan yüzey parametreleriyle olan ilişkileri doğrusal hale getirir.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Kaynak: Goel, N. ve W. Qin. &quot;Çeşitli Bitki Örtüsü Endeksleri ile LAI ve Fpar Arasındaki İlişkiler Üzerine Taç Yapısının Etkileri: Bir Bilgisayar Simülasyonu.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - Optimize Edilmiş Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks, Toprak Düzeltmeli Bitki Örtüsü Endeksi&#x27;ne (SAVI) dayanmaktadır. Taç arka plan düzeltme faktörü için 0,16 standart değerini kullanır. Rondeaux (1996), bu değerin düşük bitki örtüsü için SAVI&#x27;ten daha fazla toprak varyasyonu sağladığını, aynı zamanda %50&#x27;den fazla bitki örtüsüne karşı artan bir duyarlılık gösterdiğini belirlemiştir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve bitki örtüsünün arasından toprağın görülebildiği alanlarda en iyi şekilde kullanılır.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Kaynak: Rondeaux, G., M. Steven ve F. Baret. &quot;Optimization of Soil-Adjusted Vegetation Indices.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - Yeniden Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı bitki örtüsünü vurgulamak için NDVI ile birlikte yakın kızılötesi ve kırmızı dalga boyları arasındaki farkı kullanır. Toprak ve güneş görüş geometrisinin etkilerine karşı duyarsızdır.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Kaynak: Roujean, J. ve F. Breon. &quot;Çift Yönlü Yansıma Ölçümlerinden Bitki Örtüsünün Emdiği PAR&#x27;ın Tahmin Edilmesi.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - Toprak Düzeltmeli Bitki Örtüsü Endeksi

Bu endeks, NDVI&#x27;e benzer, ancak toprak piksellerinin etkilerini bastırır. Bitki örtüsü yoğunluğunun bir fonksiyonu olan ve genellikle bitki örtüsü miktarlarına ilişkin ön bilgi gerektiren bir bitki örtüsü arka plan düzeltme faktörü, _L_, kullanır. Huete (1988), birinci dereceden toprak arka plan varyasyonlarını hesaba katmak için _L_=0,5&#x27;lik bir optimal değer önermektedir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünün arasından görülebildiği alanlarda en iyi şekilde kullanılır.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Kaynak: Huete, A. &quot;Toprağa Göre Düzeltilmiş Bitki Örtüsü Endeksi (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - Dönüştürülmüş Fark Bitki Örtüsü Endeksi

Bu endeks, kentsel ortamlarda bitki örtüsünü izlemek için kullanışlıdır. NDVI ve SAVI gibi doygunluğa ulaşmaz.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Kaynak: Bannari, A., H. Asalhi ve P. Teillet. &quot;Bitki Örtüsü Haritalaması için Dönüştürülmüş Fark Bitki Örtüsü Endeksi (TDVI)&quot; Geoscience and Remote Sensing Symposium, IGARSS &#x27;02, IEEE International, Cilt 5 (2002) Bildiriler Kitabı._

***

## VARI - Görünür Atmosferik Direnç Endeksi

Bu endeks, ARVI&#x27;e dayanır ve atmosferik etkilere karşı düşük duyarlılığa sahip bir sahnedeki bitki örtüsü oranını tahmin etmek için kullanılır.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Kaynak: Gitelson, A., et al. &quot;Görünür Spektral Uzayda Bitki Örtüsü ve Toprak Çizgileri: Bitki Örtüsü Oranının Uzaktan Tahminine Yönelik Bir Kavram ve Teknik. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI - Geniş Dinamik Aralık Bitki Örtüsü Endeksi

Bu endeks, NDVI&#x27;e benzerdir, ancak NDVI&#x27;e yakın kızılötesi ve kırmızı sinyallerin katkıları arasındaki farkı azaltmak için bir ağırlık katsayısı (_a_) kullanır. WDRVI, NDVI&#x27;in 0,6&#x27;yı aştığı orta ila yüksek bitki örtüsü yoğunluğuna sahip sahnelerde özellikle etkilidir. NDVI, bitki örtüsü oranı ve yaprak alanı indeksi (LAI) arttıkça sabit bir seviyeye gelme eğilimindeyken, WDRVI daha geniş bir bitki örtüsü oranı aralığına ve LAI&#x27;teki değişikliklere daha duyarlıdır.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Ağırlık katsayısı (_a_) 0,1 ile 0,2 arasında değişebilir. Henebry, Viña ve Gitelson (2004) tarafından 0,2 değeri önerilmektedir.

_Kaynakça_

_Gitelson, A. &quot;Bitki Örtüsünün Biyofiziksel Özelliklerinin Uzaktan Ölçümü için Geniş Dinamik Aralık Bitki Örtüsü Endeksi.&quot; Journal of Plant Physiology 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña ve A. Gitelson. &quot;Geniş Dinamik Aralık Bitki Örtüsü Endeksi ve Boşluk Analizi için Potansiyel Kullanımı.&quot; Gap Analysis Bulletin 12: 50-56._
