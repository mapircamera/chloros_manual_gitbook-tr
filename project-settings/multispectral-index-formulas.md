---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# Çok Spektral Endeks Formülleri

Aşağıdaki endeks formülleri, Survey3 filtre ortalama iletim aralıklarının bir kombinasyonunu kullanır:

<table><thead><tr><th align="center">Survey3 Filtre Rengi</th><th width="196.199951171875" align="center">Survey3 Filtre Adı</th><th width="159.800048828125" align="center">İletim Aralığı (FWHM)</th><th align="center">Ortalama İletim</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483 nm</td><td align="center">475nm</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512 nm</td><td align="center">494 nm</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558nm</td><td align="center">547 nm</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640 nm</td><td align="center">619 nm</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668 nm</td><td align="center">661nm</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735 nm</td><td align="center">724 nm</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848 nm</td><td align="center">823 nm</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865 nm</td><td align="center">850nm</td></tr></tbody></table>

Bu formüller kullanıldığında, adın sonu &quot;\_1&quot; veya &quot;\_2&quot; ile bitebilir; bu, NIR filtresinin, NIR1 veya NIR2&#x27;in hangisinin kullanıldığına karşılık gelir.

***

## EVI - Geliştirilmiş Bitki Örtüsü Endeksi

Bu endeks, yüksek yaprak alanı endeksine (LAI) sahip alanlarda bitki örtüsü sinyalini optimize ederek NDVI&#x27;i geliştirmek amacıyla MODIS verileriyle kullanılmak üzere geliştirilmiştir. NDVI&#x27;in doygunluğa ulaşabileceği yüksek LAI bölgelerinde en yararlıdır. Mavi yansıma bölgesini kullanarak toprak arka plan sinyallerini düzeltir ve aerosol saçılması dahil atmosferik etkileri azaltır.

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

EVI değerleri, bitki örtüsü pikselleri için 0 ile 1 arasında olmalıdır. Bulutlar ve beyaz binalar gibi parlak özellikler ile su gibi koyu özellikler, EVI görüntüsünde anormal piksel değerlerine neden olabilir. EVI görüntüsü oluşturmadan önce, yansıma görüntüsünden bulutları ve parlak özellikleri maskelemeli ve isteğe bağlı olarak piksel değerlerini 0 ile 1 arasında eşiklemelisiniz.

_Referans: Huete, A., et al. &quot;MODIS Bitki Örtüsü Endekslerinin Radyometrik ve Biyofiziksel Performansına Genel Bakış.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - Orman Örtüsü Endeksi 1

Bu endeks, kırmızı kenar bandı içeren multispektral yansıma görüntülerini kullanarak orman örtülerini diğer bitki örtüsü türlerinden ayırır.

$$
FCI1 = Red * RedEdge
$$

Ormanlık alanlar, ağaçların daha düşük yansıtma özelliği ve kanopide gölgelerin varlığı nedeniyle daha düşük FCI1 değerlerine sahip olacaktır.

_Referans: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Çok spektral görüntüler için sağlam orman örtüsü indeksleri.&quot; Fotogrametrik Mühendislik ve Uzaktan Algılama 84.8 (2018): 505-512._

***

## FCI2 - Orman Örtüsü Endeksi 2

Bu endeks, kırmızı kenar bandı içermeyen multispektral yansıma görüntülerini kullanarak orman kanopilerini diğer bitki örtüsü türlerinden ayırır.

$$
FCI2 = Red * NIR
$$

Ormanlık alanlar, ağaçların daha düşük yansıma özelliği ve kanopi içindeki gölgelerin varlığı nedeniyle daha düşük FCI2 değerlerine sahip olacaktır.

_Referans: Becker, Sarah J., Craig S.T. Daughtry ve Andrew L. Russ. &quot;Çok spektral görüntüler için sağlam orman örtüsü endeksleri.&quot; Fotogrametrik Mühendislik ve Uzaktan Algılama 84.8 (2018): 505-512._

***

## GEMI - Küresel Çevre İzleme Endeksi

Bu doğrusal olmayan bitki örtüsü indeksi, uydu görüntülerinden küresel çevre izleme için kullanılır ve atmosferik etkileri düzeltmeye çalışır. NDVI ile benzerdir, ancak atmosferik etkilere daha az duyarlıdır. Çıplak topraktan etkilenir; bu nedenle, seyrek veya orta yoğunlukta bitki örtüsünün bulunduğu alanlarda kullanılması önerilmez.

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

Nerede:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_Referans: Pinty, B. ve M. Verstraete. GEMI: Uydulardan Küresel Bitki Örtüsünü İzlemek için Doğrusal Olmayan Bir Endeks. Bitki Örtüsü 101 (1992): 15-20._

***

## GARI - Green Atmosferik Direnç Endeksi

Bu endeks, NDVI&#x27;e göre geniş bir klorofil konsantrasyonu aralığına daha duyarlıdır ve atmosferik etkilere daha az duyarlıdır.

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

Gama sabiti, atmosferdeki aerosol koşullarına bağlı bir ağırlıklandırma fonksiyonudur. ENVI, Gitelson, Kaufman ve Merzylak (1996, sayfa 296) tarafından önerilen değer olan 1,7 değerini kullanır.

_Referans: Gitelson, A., Y. Kaufman ve M. Merzylak. &quot;EOS-MODIS&#x27;ten Küresel Bitki Örtüsünün Uzaktan Algılanmasında Green Kanalının Kullanımı.&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI - Green Klorofil Endeksi

Bu endeks, çok çeşitli bitki türlerinde yaprak klorofil içeriğini tahmin etmek için kullanılır.

$$
GCI = {NIR \over Green} - 1
$$

Geniş NIR ve yeşil dalga boylarına sahip olmak, klorofil içeriğinin daha iyi tahmin edilmesini sağlarken, daha fazla hassasiyet ve daha yüksek sinyal-gürültü oranı sağlar.

_Referans: Gitelson, A., Y. Gritz ve M. Merzlyak. &quot;Yaprak Klorofil İçeriği ile Spektral Yansıma ve Yüksek Bitki Yapraklarında Tahribatsız Klorofil Değerlendirmesi için Algoritmalar Arasındaki İlişkiler.&quot; Journal of Plant Physiology 160 (2003): 271-282._

***

## GLI - Green Yaprak Endeksi

Bu endeks, başlangıçta buğday örtüsünü ölçmek için dijital RGB kamera ile kullanılmak üzere tasarlanmıştır. Kırmızı, yeşil ve mavi dijital sayılar (DN&#x27;ler) 0 ile 255 arasında değişir.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

GLI değerleri -1 ile +1 arasında değişir. Negatif değerler toprak ve cansız özellikleri temsil ederken, pozitif değerler yeşil yaprakları ve sapları temsil eder.

_Referans: Louhaichi, M., M. Borman ve D. Johnson. &quot;Buğday Üzerindeki Otlatma Etkilerinin Belgelenmesi için Mekansal Konumlandırılmış Platform ve Hava Fotoğrafçılığı.&quot; Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Green Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu indeks, kırmızı spektrum yerine 540 ila 570 nm arasındaki yeşil spektrumu ölçmesi dışında NDVI ile benzerdir. Bu indeks, NDVI&#x27;e göre klorofil konsantrasyonuna daha duyarlıdır.

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_Referans: Gitelson, A., ve M. Merzlyak. &quot;Yüksek Bitki Yapraklarında Klorofil Konsantrasyonunun Uzaktan Algılanması.&quot; Uzay Araştırmalarında Gelişmeler 22 (1998): 689-692._

***

## GOSAVI - Green Optimize Edilmiş Toprak Ayarlı Bitki Örtüsü Endeksi

Bu indeks, mısırın azot ihtiyacını tahmin etmek için renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. OSAVI ile benzerdir, ancak yeşil bandı kırmızı ile değiştirir.

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_Referans: Sripada, R., et al. &quot;Hava Renkli Kızılötesi Fotoğrafçılık Kullanarak Mısırın Sezon İçi Azot İhtiyacının Belirlenmesi.&quot; Doktora tezi, Kuzey Carolina Eyalet Üniversitesi, 2005._

***

## GRVI - Green Oran Bitki Örtüsü Endeksi

Bu endeks, yeşil ve kırmızı yansıma oranları yaprak pigmentlerindeki değişikliklerden güçlü bir şekilde etkilendiği için orman örtüsündeki fotosentetik oranlara duyarlıdır.

$$
GRVI = {NIR \over Green }
$$

_Referans: Sripada, R., et al. &quot;Mısırda Sezon Başında Azot İhtiyacını Belirlemek için Hava Renkli Kızılötesi Fotoğrafçılık.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - Green Toprak Ayarlı Bitki Örtüsü Endeksi

Bu endeks, mısırın azot ihtiyacını tahmin etmek için renkli kızılötesi fotoğrafçılıkla tasarlanmıştır. SAVI&#x27;e benzer, ancak yeşil bandı kırmızı ile değiştirir.

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_Referans: Sripada, R., et al. &quot;Hava Renkli Kızılötesi Fotoğrafçılık Kullanarak Mısırın Sezon İçi Azot İhtiyacının Belirlenmesi.&quot; Doktora tezi, North Carolina State University, 2005._

***

## LAI - Yaprak Alan Endeksi

Bu endeks, yaprak örtüsünü tahmin etmek ve mahsulün büyümesini ve verimini öngörmek için kullanılır. ENVI, Boegh ve diğerleri (2002) tarafından geliştirilen aşağıdaki ampirik formülü kullanarak yeşil LAI değerini hesaplar:

$$
LAI = 3.618 * EVI - 0.118
$$

Burada EVI şudur:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

Yüksek LAI değerleri genellikle yaklaşık 0 ile 3,5 arasında değişir. Ancak, sahnede bulutlar ve doygun pikseller üreten diğer parlak özellikler varsa, LAI değerleri 3,5&#x27;i aşabilir. İdeal olarak, LAI görüntüsü oluşturmadan önce sahnedeki bulutları ve parlak özellikleri maskelemelisiniz.

_Referans: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde ve A. Thomsen. &quot;Tarımda Yaprak Alan İndeksi, Azot Konsantrasyonu ve Fotosentetik Verimliliği Ölçmek için Havadan Çok Spektral Veriler.&quot; Remote Sensing of Environment 81, no. 2-3 (2002): 179-193._

***

## LCI - Yaprak Klorofil İndeksi

Bu indeks, klorofil emiliminin neden olduğu yansıma değişikliklerine duyarlı olan yüksek bitkilerdeki klorofil içeriğini tahmin etmek için kullanılır.

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_Referans: Datt, B. &quot;Okaliptüs Yapraklarındaki Su İçeriğinin Uzaktan Algılanması.&quot; Journal of Plant Physiology 154, no. 1 (1999): 30-36._

***

## MNLI - Modifiye Edilmiş Doğrusal Olmayan Endeks

Bu indeks, toprak arka planını hesaba katmak için Toprak Ayarlı Bitki Örtüsü İndeksi&#x27;ni (SAVI) içeren Doğrusal Olmayan İndeks&#x27;in (NLI) bir geliştirilmesidir. ENVI, 0,5 değerinde bir gölgelik arka plan ayarlama faktörü (_L_) kullanır.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_Referans: Yang, Z., P. Willis ve R. Mueller. &quot;Bant Oranı Geliştirilmiş AWIFS Görüntüsünün Mahsul Sınıflandırma Doğruluğuna Etkisi.&quot; Pecora 17 Uzaktan Algılama Sempozyumu Bildirileri (2008), Denver, CO._

***

## MSAVI2 - Modifiye Edilmiş Toprak Ayarlı Bitki Örtüsü Endeksi 2

Bu endeks, Qi ve ark. (1994) tarafından önerilen MSAVI endeksinin daha basit bir versiyonudur ve Toprak Ayarlı Bitki Örtüsü Endeksi&#x27;ni (SAVI) geliştirir. Toprak gürültüsünü azaltır ve bitki örtüsü sinyalinin dinamik aralığını artırır. MSAVI2, sağlıklı bitki örtüsünü vurgulamak için sabit bir _L_ değeri (SAVI&#x27;te olduğu gibi) kullanmayan endüktif bir yönteme dayanmaktadır.

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_Referans: Qi, J., A. Chehbouni, A. Huete, Y. Kerr ve S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## NDRE- Normalize Edilmiş Fark RedEdge

Bu indeks NDVI ile benzerdir, ancak Red yerine NIR ile RedEdge arasındaki kontrastı karşılaştırır ve bu da genellikle bitki örtüsü stresini daha erken tespit eder.

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - Normalize Edilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, sağlıklı, yeşil bitki örtüsünün bir ölçüsüdür. Normalleştirilmiş fark formülasyonu ve klorofilin en yüksek emilim ve yansıma bölgelerinin kullanımı, onu çok çeşitli koşullarda sağlam hale getirir. Ancak, LAI yüksek olduğunda yoğun bitki örtüsü koşullarında doygun hale gelebilir.

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

Bu endeksin değeri -1 ile 1 arasında değişir. Yeşil bitki örtüsü için genel aralık 0,2 ile 0,8 arasındadır.

_Referans: Rouse, J., R. Haas, J. Schell ve D. Deering. ERTS ile Büyük Ovalarda Bitki Örtüsü Sistemlerinin İzlenmesi. Üçüncü ERTS Sempozyumu, NASA (1973): 309-317._

***

## NLI - Doğrusal Olmayan Endeks

Bu endeks, birçok bitki örtüsü endeksi ile yüzey biyofiziksel parametreleri arasındaki ilişkinin doğrusal olmadığını varsayar. Doğrusal olmayan eğilime sahip yüzey parametreleri ile ilişkileri doğrusal hale getirir.

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_Referans: Goel, N. ve W. Qin. &quot;Çeşitli Bitki Örtüsü Endeksleri ile LAI ve Fpar Arasındaki İlişkiler Üzerine Kanopi Mimarisi Etkileri: Bir Bilgisayar Simülasyonu.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - Optimize Edilmiş Toprak Ayarlı Bitki Örtüsü Endeksi

Bu endeks, Toprak Ayarlı Bitki Örtüsü Endeksi&#x27;ne (SAVI) dayanmaktadır. Kanopi arka plan ayarlama faktörü için 0,16 standart değeri kullanır. Rondeaux (1996), bu değerin düşük bitki örtüsü için SAVI&#x27;ten daha fazla toprak varyasyonu sağladığını, ancak %50&#x27;den fazla bitki örtüsüne karşı daha fazla duyarlılık gösterdiğini belirlemiştir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünden görülebildiği alanlarda en iyi şekilde kullanılır.

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_Referans: Rondeaux, G., M. Steven ve F. Baret. &quot;Toprak Ayarlı Bitki Örtüsü Endekslerinin Optimizasyonu.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - Yeniden Normalleştirilmiş Fark Bitki Örtüsü Endeksi

Bu endeks, NDVI ile birlikte yakın kızılötesi ve kırmızı dalga boyları arasındaki farkı kullanarak sağlıklı bitki örtüsünü vurgular. Toprak ve güneşin görüş geometrisinin etkilerine duyarlı değildir.

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_Referans: Roujean, J. ve F. Breon. &quot;İki Yönlü Yansıma Ölçümlerinden Bitki Örtüsü Tarafından Emilen PAR&#x27;ın Tahmin Edilmesi.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - Toprak Ayarlı Bitki Örtüsü Endeksi

Bu endeks, NDVI&#x27;e benzer, ancak toprak piksellerinin etkilerini bastırır. Bitki örtüsü yoğunluğunun bir fonksiyonu olan ve genellikle bitki örtüsü miktarları hakkında önceden bilgi gerektiren bir gölgelik arka plan ayarlama faktörü olan _L_ kullanır. Huete (1988), birinci dereceden toprak arka plan varyasyonlarını hesaba katmak için _L_=0,5&#x27;in optimal değer olduğunu önerir. Bu endeks, bitki örtüsünün nispeten seyrek olduğu ve toprağın bitki örtüsünden görülebildiği alanlarda en iyi şekilde kullanılır.

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_Referans: Huete, A. &quot;Toprağa Göre Ayarlanmış Bitki Örtüsü Endeksi (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - Dönüştürülmüş Fark Bitki Örtüsü Endeksi

Bu endeks, kentsel ortamlarda bitki örtüsünü izlemek için kullanışlıdır. NDVI ve SAVI gibi doygunluğa ulaşmaz.

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_Referans: Bannari, A., H. Asalhi ve P. Teillet. &quot;Bitki Örtüsü Haritalaması için Dönüştürülmüş Fark Bitki Örtüsü Endeksi (TDVI)&quot; Jeoloji Bilimleri ve Uzaktan Algılama Sempozyumu Bildirileri, IGARSS &#x27;02, IEEE International, Cilt 5 (2002)._

***

## VARI - Görünür Atmosferik Direnç Endeksi

Bu endeks, ARVI&#x27;e dayanır ve atmosferik etkilere karşı düşük duyarlılığa sahip bir sahnedeki bitki örtüsünün oranını tahmin etmek için kullanılır.

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_Referans: Gitelson, A., et al. &quot;Görünür Spektral Uzayda Bitki Örtüsü ve Toprak Çizgileri: Bitki Örtüsü Oranının Uzaktan Tahmin Edilmesi için Bir Kavram ve Teknik. Uluslararası Uzaktan Algılama Dergisi 23 (2002): 2537−2562._

***

## WDRVI - Geniş Dinamik Aralık Bitki Örtüsü Endeksi

Bu endeks, NDVI&#x27;e benzer, ancak NDVI&#x27;e yakın kızılötesi ve kırmızı sinyallerin katkıları arasındaki farkı azaltmak için bir ağırlık katsayısı (_a_) kullanır. WDRVI, NDVI 0,6&#x27;yı aştığında orta ila yüksek bitki örtüsü yoğunluğuna sahip sahnelerde özellikle etkilidir. NDVI, bitki örtüsü oranı ve yaprak alanı indeksi (LAI) arttıkça düzleşme eğilimi gösterirken, WDRVI daha geniş bir bitki örtüsü oranı aralığına ve LAI&#x27;teki değişikliklere daha duyarlıdır.

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

Ağırlık katsayısı (_a_) 0,1 ile 0,2 arasında değişebilir. Henebry, Viña ve Gitelson (2004) tarafından 0,2 değeri önerilmektedir.

_Referanslar_

_Gitelson, A. &quot;Bitki Örtüsünün Biyofiziksel Özelliklerinin Uzaktan Ölçülmesi için Geniş Dinamik Aralık Bitki Örtüsü Endeksi.&quot; Journal of Plant Physiology 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña ve A. Gitelson. &quot;Geniş Dinamik Aralık Bitki Örtüsü Endeksi ve Boşluk Analizi için Potansiyel Yararı.&quot; Boşluk Analizi Bülteni 12: 50-56._
