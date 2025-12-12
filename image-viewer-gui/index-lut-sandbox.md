# Dizin/LUT Sandbox

Dizin/LUT Sandbox, Chloros Görüntü Görüntüleyicisi içindeki etkileşimli bir çalışma alanıdır ve çok spektral dizin hesaplamaları ve renk görselleştirmelerini gerçek zamanlı olarak denemenizi sağlar. Bu güçlü araç, tüm veri kümenizi yeniden işlemden geçirmeden farklı dizinleri test etmenize, değer aralıklarını iyileştirmenize ve yayınlanmaya hazır görselleştirmeler oluşturmanıza yardımcı olur.

## Dizin/LUT Sandbox nedir?

### Amaç

Sandbox şunları sağlar:

* **Gerçek zamanlı dizin hesaplama** - Herhangi bir bitki örtüsü dizinini anında uygulayın
* **Etkileşimli LUT ayarı** - Renk gradyanlarını ve aralıklarını ince ayarlayın
* **İş akışı optimizasyonu** - Toplu işleme öncesinde en iyi ayarları belirleyin

### Sandbox ve Proje İşleme

**Endeks/LUT Sandbox (Etkileşimli):**

* Her seferinde tek bir görüntü
* Anında geri bildirim
* Deneysel ve yinelemeli
* Dosyalarda kalıcı değişiklik yok
* Keşfetmek ve test etmek için mükemmel

**Proje İşleme (Toplu):**

* Tüm veri kümesi bir seferde
* Önceden yapılandırılmış ayarlar
* Kalıcı çıktı dosyaları
* Zaman alıcı
* Ayarlar kesinleştiğinde en iyisi

{% hint style=&quot;success&quot; %}
**En İyi İş Akışı**: Sandbox&#x27;ı kullanarak denemeler yapın ve en uygun indeks ve LUT ayarlarını bulun, ardından bu ayarları Proje İşleme sırasında tüm veri kümenize uygulayın.
{% endhint %}

***

## İndeks/LUT Sandbox ile Çalışma

### Önceden Hesaplanmış Dizinleri Anlama

Chloros&#x27;te, dizinler proje işleme sırasında uygulanabilir. Dışa aktarmalara hangi dizin ve LUT ayarlarını uygulamak istediğinizi belirlemek için en kolay yol, görüntü görüntüleyici sandbox&#x27;ını kullanmaktır.

Sandbox ile şunları yapabilirsiniz:

* Verileri görselleştirmek için **yeni indeks ve renk gradyanları (LUT&#x27;lar)** uygulayın
* **Görselleştirme ayarlarını** etkileşimli olarak ayarlayın
* Önceden hesaplanmış indeks görüntülerini **görüntüleyin**
* Tüm yakınlaştırma düzeylerinde piksel değerlerini **inceleyin**

### Sandbox&#x27;ı Açma

Dizin/LUT Sandbox&#x27;a **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kenar çubuğu sekmesinden erişilebilir:

1. Dosya tarayıcısının görüntü ızgarasında bir görüntüyü tıklayın, görüntü **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinde açılır
2. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesine tıklayın.

### Dizin/LUT Uygulanacak Görüntü Seçme

Görüntü Görüntüleyici <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox&#x27;ta bir indeksle çalışmak için:

1. Ana görüntü ızgarasından bir görüntüyü tıklayarak **açın**
2. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesi açılır
3. **Katman açılır menüsünü** (görüntüleyicinin sağ üst köşesinde) tıklayın
4. Açılır menüden katmanı seçin:
   * RAW (Yansıtma)

### Görüntüye Dizin Uygulama

Görüntü tam ekran olduğunda ve **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesi kenar çubuğu açıldığında:

1. Kenar çubuğunun üst kısmındaki Dizin kutusunu işaretleyin
2. Sol açılır menüden kameranızın filtresini seçin
3. Sağ açılır menüden istediğiniz dizin formülünü seçin
4. Filtre kanalı renk dairelerini aşağıdaki dizin formülündeki konumlara sürükleyin
5. Formül geçerli olduğunda görüntü güncellenecek ve dizin değerleri gösterilecektir
6. Fare imlecini hareket ettirerek imlecin bulunduğu konumdaki değerleri görebilirsiniz
7. Bireysel pikselleri ve bunlarla ilişkili değerleri görmek için yakınlaştırın.

Her endeksin belirli bir değer aralığı ve anlamı vardır:

#### NDVI Örneği

```
Formula: (NIR - Red) / (NIR + Red)

For Survey3W RGN camera:
NIR = 850nm band
Red = 661nm band

Result range: -1.0 to +1.0
Typical vegetation: 0.4 to 0.9
Stressed vegetation: 0.2 to 0.4
Bare soil: 0.0 to 0.2
Water: -0.1 to 0.1
```

Endeş formülünün tam dokümantasyonu için [Multispektral Endeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümüne bakın.

***

## LUT&#x27;larla (Arama Tabloları) Çalışma

### LUT nedir?

**Arama Tablosu (LUT)**, görselleştirme için sayısal indeks değerlerini renklere eşler:

* **Giriş**: İndeks piksel değeri (ör. NDVI 0,65)
* **Çıktı**: RGB rengi (ör. parlak yeşil)
* **Amaç**: Desenleri daha kolay görülür ve yorumlanabilir hale getirmek

**Gri Tonlama ve Renkli LUT:**

* Gri Tonlama: Bilimsel ve tarafsız, ham verileri gösterir
* Renkli LUT: Sezgisel ve etkili, desenleri ve farklılıkları vurgular

{% hint style=&quot;success&quot; %}
**Görselleştirme Gücü**: Gri tonlamalı bir indeks görüntüsüne renk LUT&#x27;u uygulamak, desenleri, anomalileri ve ilgi alanlarını bir bakışta tanımlamayı önemli ölçüde kolaylaştırır.
{% endhint %}

### Dizin Görüntüsüne LUT Uygulama

Dizin görüntüsünü aldıktan sonra

1. <img src="../.gitbook/assets/image.png" alt="" data-size="line"> &quot;+LUT Ekle&quot; düğmesine tıklayın
2. Renk gradyanını seçin
3. Kırpma min/maks uç noktalarını ayarlayın
4. Kırpma Modunu ayarlayın
5. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinin kenar çubuğundaki **Görüntü Görüntüleyici**

### Renk Gradyanı Seçme

**Gradyan seçme:**

1. LUT panelinde **renkli gradyan çubuğunu** bulun.
2. Mevcut gradyan ön ayarlarını görüntülemek için fareyi üzerine getirin.
3. İstediğiniz gradyanı seçin.
4. Dizin kutusu işaretlendiğinde görüntü **hemen yeni renklerle güncellenir**.

{% hint style=&quot;success&quot; %}
**En İyi Uygulama**: NDVI gibi bitki örtüsü indeksleri için, Red-Sarı-Green gradyanı, doğal renk ilişkilendirmeleriyle (yeşil=sağlıklı, sarı=orta, kırmızı=stresli) uyumlu olduğu için en sezgisel olanıdır.
{% endhint %}

### Renk Sınıflarını Ayarlama

**Sınıf kontrolü**, gradyanınızda kaç ayrı renk adımı görüneceğini belirler:

**Sınıf sayısı seçenekleri:**

* **2-5 sınıf**: Çok geniş kategoriler, belirgin bölgeler
* **6-10 sınıf**: Dengeli, sınıflandırma için uygun
* **11-20 sınıf**: Pürüzsüz gradyanlar, sürekli görünüm
* **20+ sınıf**: Neredeyse sürekli, maksimum pürüzsüzlük

**Nasıl ayarlanır:**

1. LUT panelinde, **gradyan çubuğunun altındaki renk örneği karelerini** bulun
2. + düğmesini kullanarak sınıf sayısını ayarlayın
3. Renk örneğine çift tıklayarak sınıf sayısını kaldırın
4. Gradyan, görüntü üzerinde **gerçek zamanlı** olarak güncellenir

**Görselleştirme üzerindeki etkisi:**

* **Daha az sınıf** (3-5): Belirgin bölgeler oluşturur, sınıflandırmayı basitleştirir, kategorileri ayırt etmeyi kolaylaştırır
* **Orta sınıflar** (6-10): Dengeli yaklaşım, çoğu uygulama için uygundur
* **Daha fazla sınıf** (15-20): Yumuşak geçişler, ayrıntılı varyasyon, fotoğrafik görünüm

**Ne zaman kullanılır:**

* **Az sayıda sınıf (3-5)**: Sunum slaytları, sınıflandırma haritaları, basit raporlar
* **Orta sınıflar (6-10)**: Genel analiz, dengeli ayrıntılar, standart raporlar
* **Çok sayıda sınıf (15-20)**: Bilimsel analiz, ayrıntılı inceleme, yayın kalitesinde çıktılar

### Değer Aralıklarını İnce Ayarlama

**Değer aralığı denetimleri**, gradyanınızda hangi indeks değerlerinin hangi renklere eşleneceğini belirler:

**LUT panelindeki aralık denetimleri:**

* **Minimum değer**: Renk skalasının alt sınırı
* **Maksimum değer**: Renk skalasının üst sınırı
* **Ara değerler**: Min ve max arasında otomatik olarak dağıtılır (sınıf sayısına göre)

#### Min/Maks Değerleri Ayarlama

**Değer aralıklarını ayarlamak için:**

1. LUT panelinde, **Min Değer** ve **Maks Değer** giriş alanlarını bulun
2. **Min Değer** alanını tıklayın
3. İstediğiniz minimum değeri girin (ör. `0.2`)
4. **Enter** tuşuna basın veya alanın dışını tıklayın
5. **Maks Değer** alanı için aynı işlemi tekrarlayın (ör. `0.9`)
6. Görselleştirme **hemen güncellenir**

{% hint style=&quot;info&quot; %}
**Otomatik Ölçeklendirme**: Bir LUT&#x27;u ilk kez uyguladığınızda, Chloros otomatik olarak minimum/maksimum değerleri görüntünün gerçek veri aralığına ayarlar. Ardından, bu aralığı daraltarak ilgilendiğiniz belirli değer aralıklarına odaklanabilirsiniz.
{% endhint %}

**Örnek NDVI aralık ayarlamaları:**

* **Tam aralık**: `-1.0` ila `1.0` (tüm olası değerleri göster)
* **Bitki örtüsüne odaklanmış**: `0.2` ile `0.9` arası (çıplak toprak ve suyu hariç tut)
* **Yalnızca sağlıklı bitki örtüsü**: `0.5` ile `0.9` arası (sadece güçlü bitkileri vurgulayın)
* **Stres algılama**: `0.2` ila `0.5` (sorunlu alanları vurgulayın)
* **Özel aralık**: Gözlemlediğiniz piksel değerlerine göre ayarlayın

**Neden aralıkları ayarlamalıyız?**

* İlgilendiğiniz alanda **kontrastı artırın**
* **Alakasız değerleri hariç tutun** (ör. su kütleleri, çıplak toprak)
* Birden fazla görüntü veya tarih arasında **görselleştirmeyi standartlaştırın**
* Dar bir değer aralığı içindeki **ince farkları vurgulayın**

### Aralık Dışı Değerleri Kırpma

Piksel değerleri tanımladığınız minimum/maksimum aralığın dışına çıktığında, **kırpma modları** kullanarak bunların nasıl görüntüleneceğini kontrol edebilirsiniz.

#### **Kullanılabilir kırpma modu seçenekleri:**

#### 1. Minimum ve Maksimum

* **Minimumun altındaki** pikseller → gradyanın **ilk rengi** (ör. kırmızı) kullanılarak görüntülenir
* **Maksimumun üzerindeki** pikseller → gradyanın **son rengi** kullanılarak görüntülenir (ör. yeşil)
* **Kullanım örneği**: Aşırı değerleri vurgulayın, sınırlarda doygun renklerle tam veri aralığını gösterin
* **Örnek**: 0,2&#x27;nin altındaki NDVI değerleri kırmızı, 0,9&#x27;un üzerindeki değerler yeşil olarak görünür

#### 2. Şeffaf Arka Plan

* **Aralığın dışındaki** pikseller **tamamen şeffaf** hale gelir
* Yalnızca **aralık içindeki** pikseller renk gradyanı gösterir
* **Kullanım örneği**: GIS katmanı, belirli değer aralıklarını izole etme, yalnızca ilgi alanlarını vurgulamak
* **Örnek**: Yalnızca NDVI 0,4-0,7 aralığını renkli göster, diğer her şeyi şeffaf göster

{% hint style=&quot;warning&quot; %}
**Şeffaflık Sınırlaması**: Şeffaf pikseller görüntüleyicide arka plan rengi olarak görünür. İşleme sırasında dışa aktarıldığında, şeffaflık PNG formatında korunur, ancak JPG formatında korunmaz.
{% endhint %}

#### 3. Dizin Arka Planı

* **Aralık dışındaki** pikseller **gri tonlamada** görüntülenir (ham dizin değerlerini gösterir)
* **Aralık içindeki** pikseller **renk gradyanı** gösterir
* **Kullanım örneği**: İnce vurgu, ilgi alanlarını vurgularken bağlamı korur
* **Örnek**: Vurgulanan bitki örtüsünü renkli olarak vurgular (NDVI 0,3-0,5) ve sağlıklı alanları gri olarak gösterir

#### 4. Orijinal Arka Plan

* **Aralık dışındaki** pikseller **orijinal multispektral görüntüyü** gösterir
* **Aralık içindeki** pikseller **renk gradyanı** gösterir
* **Kullanım örneği**: En sezgisel olanı - doğal görüntü bağlamını analitik renk katmanıyla birleştirir
* **Örnek**: Renk kodlu stres alanları katmanıyla gerçek tarla/mahsul görünümünü görün

### Doğru Kırpma Modunu Seçme

| Kırpma Modu              | En Uygun Olduğu Durum                                   | Görselleştirme Stili          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimum ve Maksimum**    | Tam veri gösterimi, bilimsel analiz     | Tüm pikseller renklendirilmiş           |
| **Şeffaf Arka Plan** | GIS katmanları, belirli aralıkları izole etme    | Aralıkta renk, dışında boşluk |
| **Dizin Arka Plan**       | İnce vurgu, veri bağlamını koruma  | Aralıkta renk, dışında gri  |
| **Orijinal Arka Plan**    | Raporlar, sunumlar, sezgisel analiz | Aralıkta renk, dışında fotoğraf |

### Özel LUT Renkleri Oluşturma

Görselleştirmenizi tam olarak kontrol etmek için, tek tek renk duraklarını düzenleyerek **özel renk gradyanları** oluşturabilirsiniz.

**Özel bir geçiş oluşturmak için:**

1. LUT panelinde **geçiş önizleme çubuğunu** bulun
2. Geçişin altında **renk örneği karelerini** bulun
3. **Bir renk durağını tıklayarak** seçin
4. **Renk seçici** açılır
5. Aşağıdakileri kullanarak yeni bir renk seçin:
   * **Renk tekerleği**: Görsel renk seçimi
   * **RGB/HSV kaydırıcıları**: Hassas renk kontrolü
   * **Hex kodu girişi**: Kesin renk belirtimi (ör. kırmızı için `#FF0000`)
6. **Yeni rengi uygulamak için** renk seçicinin dışına tıklayın
7. Degrade **görüntüde hemen güncellenir**

**Renk durakları ekleme veya kaldırma:**

* **Durak ekleme**: Sonuna yeni bir renk örneği eklemek için + simgesine tıklayın
* **Durak kaldırma**: Renk örneğini kaldırmak için renk karesine çift tıklayın

**Özelleştirme stratejileri:**

* **Degradeyi ters çevirin**: Anlamı tersine çevirmek için renk sırasını ters çevirin (ör. yeşil=düşük, kırmızı=yüksek)
* **Marka renkleri**: Raporlar için kuruluşunuzun renk paletiyle eşleştirin
* **Renk körlüğüne uygun**: Turuncu-mavi veya mor-sarı kombinasyonları kullanın
* **Yazdırma optimizasyonu**: Hem renkli hem de gri tonlamalı yazdırmada uygun renkleri seçin
* **Çoklu eşik**: Sınıflandırma için belirli değer eşiklerinde farklı renkler kullanın

{% hint style=&quot;info&quot; %}
**Özel Gradyanları Kaydetme**: Özel gradyanlar kaydedilebilir ve yeniden kullanılabilir. LUT panelindeki kaydet simgesine tıklayarak özel renk şemalarınızı ileride kullanmak üzere saklayabilirsiniz.
{% endhint %}

***

## Etkileşimli İş Akışı

### Gerçek Zamanlı Güncellemeler

Sandbox&#x27;taki tüm LUT ayarlamaları görüntüyü **anında ve etkileşimli olarak** günceller:

* **Katmanı değiştir** → Görüntü hemen değişir
* **Degradeyi seç** → Renkler anında güncellenir
* **Değer aralığını ayarla** → Kontrast gerçek zamanlı olarak değişir
* **Sınıfları değiştir** → Degrade düzgünlüğü hemen güncellenir
* **Kırpma değiştir** → Arka plan görüntüsü hemen değişir
* **Renkleri düzenle** → Özel degrade hemen uygulanır

**&quot;Uygula&quot; düğmesine gerek yok** - tüm değişiklikler canlı ve etkileşimlidir!

{% hint style=&quot;success&quot; %}
**Canlı Geri Bildirim**: Anlık görsel geri bildirim, analiz ihtiyaçlarınız için en uygun görselleştirmeyi bulana kadar farklı ayarları hızla denemenizi sağlar.
{% endhint %}

### Yinelemeli İyileştirme İş Akışı

**Tipik LUT optimizasyon iş akışı:**

1. **Dizin katmanını seçin** (ör. RAW (Yansıtma))
2. **Dizini uygulayın** - Kamera filtresini ve dizin formülünü seçin, renkli daireleri dizin formülündeki uygun konuma sürükleyin
3. **LUT gradyanını uygulayın** - Red-Sarı-Green ön ayarıyla başlayın
4. **Piksel değerlerini inceleyin** - İmleci hareket ettirin, değer aralıklarını not edin
5. **Min/maks değerlerini ayarlayın** - Bitki örtüsüne odaklanmak için daraltın (ör. 0,2 ila 0,9)
6. **Kırpma seçin** - Bağlam için &quot;Orijinal Arka Plan&quot;ı deneyin
7. **Renkleri iyileştirin** - Belirli bir vurgu için gerekirse gradyanı özelleştirin
8. **Ayarları sonlandırın** - Ayarları belgelendirin ve dışa aktarma işlemi için Proje Ayarlarına kopyalayın

### Piksel Değeri İnceleme

Etkili LUT aralıkları ayarlamak için gerçek piksel değerlerini anlamak çok önemlidir:

**Değerleri inceleme:**

1. Piksel değerleri, görüntünün İndeks veya hem İndeks hem de LUT **kutuları işaretli** olduğunda gösterilir.
2. **İmlecinizi** görüntünün farklı alanlarının üzerine getirin
3. İmleci üzerine getirdiğinizde efsanede görüntülenen **piksel değerlerini** gözlemleyin
4. Büyütüp, yüzen bir değerle vurgulanan tek tek pikselleri görün
5. Farklı özellikler için değer aralıklarını **not alın**:
   * **Sağlıklı bitki örtüsü**: ör. NDVI 0,55-0,85
   * **Stresli bitki örtüsü**: ör. NDVI 0,30-0,50
   * **Çıplak toprak**: ör. NDVI 0,05-0,25
   * **Su** (varsa): ör. NDVI -0,05 ila 0,10

**Piksel değerlerini kullanarak LUT aralıklarını ayarlama:**

Piksel değerlerini inceledikten sonra, LUT min/maks değerlerini buna göre ayarlayın:

**Örnek senaryo:**

* **Gözlem**: Toprak değerleri = 0,05-0,25, Stresli = 0,25-0,50, Sağlıklı = 0,50-0,85
* **Hedef**: Yalnızca bitki sağlığını görselleştirme (toprak hariç)
* **LUT ayarları**: Min = `0.25`, Maks = `0.85`
* **Kırpma**: Toprağı doğal renginde görmek için &quot;Orijinal Arka Plan&quot;
* **Sonuç**: Renk gradyanı yalnızca bitki örtüsüne uygulanır, toprak orijinal görüntü olarak gösterilir

{% hint style=&quot;info&quot; %}
**Dinamik Aralık**: Farklı mahsuller, mevsimler ve büyüme aşamaları farklı değer aralıklarına sahip olacaktır. LUT aralıklarını ayarlamadan önce her zaman belirli veri kümenizin piksel değerlerini kontrol edin.
{% endhint %}

***

## Özel Endeksler (Chloros+)

### Özel Dizin Formülleri Oluşturma

{% hint style=&quot;info&quot; %}
**Nerede Oluşturulur**: Özel indeksler, işleme öncesinde **Proje Ayarları**&#x27;nda ve Image Viewer sandbox kenar çubuğunda yapılandırılabilir.
{% endhint %}

**Özel bir indeks oluşturmak için:**

1. **Proje Ayarları**&#x27;nı (işlemden önce) veya Image Viewer sandbox kenar çubuğunu açın.
2. **İndeks formülü açılır menüsüne** gidin.
3. **&quot;Özel&quot;** seçeneğini bulun (Chloros+ lisansıyla oturum açmış olmanız gerekir).
4. Bant değişkenlerini kullanarak **formülünüzü tanımlayın**:
   * Bant adları: `NIR`, `Red`, `Green`, `Blue`, `RedEdge`, vb.
   * İşlemciler: `+`, `-`, `*`, `/`, `^` (üstel)
   * İşlevler: `sqrt()`, `abs()`, vb. (destekleniyorsa)
   * Parantezler: İşlem sırası için `()`
5. **Dizinize bir ad verin** (ör. &quot;MyIndex&quot; veya &quot;CustomNDVI&quot;)
6. **Yapılandırmayı kaydedin**

**Örnek özel formüller:**

```
Modified NDVI with offset:
(NIR - Red) / (NIR + Red + 0.5)

Simple ratio:
NIR / Red

Complex multi-band:
(NIR - Red) / (NIR + Red - Blue)

Exponential index:
(NIR / Red) ^ 2
```

{% hint style=&quot;warning&quot; %}
**Formül Doğrulama**: Formülünüzün kameranızda bulunan bantları kullandığından emin olun. Örneğin, RedEdge yalnızca RedEdge filtresine sahip kameralarda kullanılabilir.
{% endhint %}

***

## Sonraki Adımlar

Artık Endeks/LUT Sandbox&#x27;ı anladığınıza göre:

* **İşlemeye uygulayın**: [Proje Ayarları](../project-settings/project-settings.md) içinde bulunan ayarları kullanın
* **Toplu işleme**: Optimize edilmiş indeksleri tüm veri kümelerine uygulayın
* **Daha fazla bilgi**: [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümünü okuyun

İlgili belgeler:

* [**Görüntü Katmanları**](image-layers.md) - Katman yönetimi ve görselleştirme
* [**Görüntüyü Tam Ekran Açma**](opening-an-image-full-screen.md) - Görüntü Görüntüleyici temel bilgileri
* [**Görüntüleri İşleme (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Tam işleme iş akışı
