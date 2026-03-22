# İndeks/LUT Sandbox

İndeks/LUT Sandbox, Chloros Görüntü Görüntüleyicisi içinde yer alan ve çok spektral indeks hesaplamaları ile renk görselleştirmelerini gerçek zamanlı olarak denemenize olanak tanıyan etkileşimli bir çalışma alanıdır. Bu güçlü araç, tüm veri kümenizi yeniden işlemeye gerek kalmadan farklı indeksleri test etmenize, değer aralıklarını daraltmanıza ve yayına hazır görselleştirmeler oluşturmanıza yardımcı olur.

## İndeks/LUT Sandbox nedir?

### Amaç

Sandbox şunları sağlar:

* **Gerçek zamanlı indeks hesaplama** - Herhangi bir bitki örtüsü indeksini anında uygulayın
* **Etkileşimli LUT ayarı** - Renk gradyanlarını ve aralıklarını ince ayarlayın
* **İş akışı optimizasyonu** - Toplu işleme öncesinde en iyi ayarları belirleyin

### Sandbox ve Proje İşleme Karşılaştırması

**Endeks/LUT Sandbox (Etkileşimli):**

* Her seferinde tek bir görüntü
* Anında geri bildirim
* Deneysel ve yinelemeli
* Dosyalarda kalıcı değişiklik yok
* Keşfetme ve test etme için mükemmel

**Proje İşleme (Toplu):**

* Tüm veri kümesi tek seferde
* Önceden yapılandırılmış ayarlar
* Kalıcı çıktı dosyaları
* Zaman alıcı
* Ayarlar kesinleştiğinde en iyi sonuç verir

{% hint style="success" %}
**En İyi İş Akışı**: Sandbox&#x27;ı kullanarak denemeler yapın ve en uygun indeks ve LUT ayarlarını bulun, ardından bu ayarları tüm veri kümeniz için Proje İşleme sırasında uygulayın.
{% endhint %}

***

## Dizin/LUT Sandbox ile Çalışma

### Önceden Hesaplanmış Dizinleri Anlama

Chloros&#x27;te, dizinler proje işleme sırasında uygulanabilir. Dışa aktarmalara hangi dizin ve LUT ayarlarını uygulamak istediğinizi belirlemek için en kolay yol, görüntü görüntüleyici sandbox&#x27;ını kullanmaktır.

Sandbox size şunları yapma imkanı sunar:

* Verileri görselleştirmek için **yeni indeks ve renk gradyanları (LUT&#x27;lar)** uygulayın
* Görselleştirme ayarlarını etkileşimli olarak **ayarlayın*** Önceden hesaplanmış indeks görüntülerini **görüntüleyin*** Tüm yakınlaştırma düzeylerinde piksel değerlerini **inceleyin**

### Sandbox&#x27;ı Açma

İndeks/LUT Sandbox&#x27;a **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kenar çubuğu sekmesinden erişilir:

1. Dosya tarayıcısındaki görüntü ızgarasından bir görüntüye tıklayın; görüntü **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinde açılır
2. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesine tıklayın

### İndeks/LUT Uygulanacak Görüntüyü Seçme

Görüntü Görüntüleyicisi <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sandbox&#x27;ta çalışmak için:

1. Ana görüntü ızgarasından bir **görüntüyü** tıklayarak açın
2. Ardından **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesi açılır
3. **Katman açılır menüsüne** (görüntüleyicinin sağ üst köşesi) tıklayın
4. Açılır menüden katmanı seçin:
   * RAW (Yansıma)

### Bir Görüntüye İndeks Uygulama

Görüntü tam ekran olduğunda ve **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinin kenar çubuğu açıldığında:

1. Kenar çubuğunun üst kısmındaki Dizin kutusunu işaretleyin
2. Soldaki açılır menüden kameranızın filtresini seçin
3. Sağdaki açılır menüden istediğiniz dizin formülünü seçin
4. Filtre kanalı renk dairelerini aşağıdaki dizin formülündeki konumlara sürükleyin
5. Formül geçerli olduğunda görüntü güncellenecek ve dizin değerlerini gösterecektir
6. Fare imlecini hareket ettirerek imlecin bulunduğu konumdaki değerleri görün
7. Tek tek pikselleri ve bunlara ait değerleri görmek için yakınlaştırın

Her indeksin belirli bir değer aralığı ve anlamı vardır:

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

İndeks formülleri ile ilgili eksiksiz belgeler için bkz. [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md).

***

## LUT&#x27;larla (Arama Tabloları) Çalışma

### LUT nedir?

Bir **Arama Tablosu (LUT)**, görselleştirme amacıyla sayısal indeks değerlerini renklere eşler:

* **Giriş**: İndeks piksel değeri (örn., NDVI 0,65)
* **Çıktı**: RGB rengi (örn. parlak yeşil)
* **Amaç**: Desenlerin görülmesini ve yorumlanmasını kolaylaştırmak**Gri Tonlama ve Renkli LUT:**

* Gri Tonlama: Bilimsel ve tarafsızdır, ham verileri gösterir
* Renkli LUT: Sezgisel ve etkileyicidir, desenleri ve farklılıkları vurgular

{% hint style="success" %}
**Görselleştirme Gücü**: Gri tonlamalı bir dizin görüntüsüne renkli LUT uygulanması, desenleri, anomalileri ve ilgi alanlarını bir bakışta tanımayı önemli ölçüde kolaylaştırır.
{% endhint %}

### Dizin Görüntüsüne LUT Uygulama

Dizin görüntünüz hazır olduğunda

1. <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> &quot;+LUT Ekle&quot; düğmesine tıklayın
2. Renk gradyanını seçin
3. Kırpma min/maks uç noktalarını ayarlayın
4. Kırpma Modunu ayarlayın
5. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinin kenar çubuğundaki Dizin kutusunu işaretleyin

### Renk Gradyanı Seçme

**Gradyan seçme:**

1. LUT panelinde**renkli gradyan çubuğunu** bulun
2. Kullanılabilir gradyan ön ayarlarını görüntülemek için fareyi üzerine getirin
3. İstediğiniz gradyanı seçin
4. Dizin kutusu işaretlendiğinde görüntü **hemen** yeni renklerle güncellenir

{% hint style="success" %}
**En İyi Uygulama**: NDVI gibi bitki örtüsü indeksleri için, Red-Sarı-Green gradyanı, doğal renk ilişkilendirmeleriyle (yeşil=sağlıklı, sarı=orta, kırmızı=stresli) uyumlu olduğu için en sezgisel seçenektir.
{% endhint %}

### Renk Sınıflarını Ayarlama

**Sınıflar kontrolü**, gradyanınızda kaç ayrı renk basamağının görüneceğini belirler:**Sınıf sayısı seçenekleri:*** **2-5 sınıf**: Çok geniş kategoriler, belirgin bölgeler
* **6-10 sınıf**: Dengeli, sınıflandırma için uygun
* **11-20 sınıf**: Pürüzsüz gradyanlar, kesintisiz görünüm
* **20+ sınıf**: Neredeyse sürekli, maksimum pürüzsüzlük**Nasıl ayarlanır:**

1. LUT panelinde,**gradyan çubuğunun altındaki renk örneği karelerini** bulun
2. + düğmesini kullanarak sınıf sayısını artırın
3. Bir renk örneğine çift tıklayarak sınıf sayısını azaltın
4. Gradyan, görüntü üzerinde **gerçek zamanlı** olarak güncellenir**Görselleştirme üzerindeki etkisi:*** **Daha az sınıf** (3-5): Belirgin bölgeler oluşturur, sınıflandırmayı basitleştirir, kategorileri ayırt etmeyi kolaylaştırır
* **Orta sınıf** (6-10): Dengeli bir yaklaşım, çoğu uygulama için uygundur
* **Daha fazla sınıf** (15-20): Pürüzsüz geçişler, ayrıntılı varyasyon, fotoğrafik görünüm**Ne zaman kullanılır:*** **Az sınıf (3-5)**: Sunum slaytları, sınıflandırma haritaları, basit raporlar
* **Orta sınıf (6-10)**: Genel analiz, dengeli ayrıntı, standart raporlar
* **Çok sınıf (15-20)**: Bilimsel analiz, ayrıntılı inceleme, yayın kalitesinde çıktılar

### Değer Aralıklarını İnce Ayarlama

**Değer aralığı denetimleri**, gradyanınızdaki hangi indeks değerlerinin hangi renklere eşleneceğini belirler:**LUT panelindeki aralık denetimleri:*** **Minimum değer**: Renk skalasının alt sınırı
* **Maksimum değer**: Renk skalasının üst sınırı
* **Ara değerler**: Min ve maks arasında otomatik olarak dağıtılır (sınıf sayısına göre)

#### Min/Maks Değerlerini Ayarlama

**Değer aralıklarını ayarlamak için:**

1. LUT panelinde**Min Değer**ve**Maks Değer** giriş alanlarını bulun
2. **Min Değer** alanına tıklayın
3. İstediğiniz minimum değeri yazın (ör. `0.2`)
4. **Enter** tuşuna basın veya alanın dışına tıklayın
5. **Maksimum Değer** alanı için de aynısını tekrarlayın (ör. `0.9`)
6. Görselleştirme **hemen güncellenir**{% hint style="info" %}**Otomatik Ölçeklendirme**: Bir LUT&#x27;u ilk uyguladığınızda, Chloros minimum/maksimum değerleri otomatik olarak görüntüdeki gerçek veri aralığına ayarlar. Daha sonra bu aralığı daraltarak ilgilendiğiniz belirli değer aralıklarına odaklanabilirsiniz.
{% endhint %}

**Örnek NDVI aralık ayarlamaları:*** **Tam aralık**: `-1.0` ile `1.0` arası (tüm olası değerleri göster)
* **Bitki örtüsüne odaklı**: `0.2` ile `0.9` arası (çıplak toprak ve suyu hariç tut)
* **Yalnızca sağlıklı bitki örtüsü**: `0.5` ile `0.9` arası (yalnızca canlı bitkileri vurgulayın)
* **Stres tespiti**: `0.2` ile `0.5` arası (sorunlu alanları vurgulayın)
* **Özel aralık**: Gözlemlediğiniz piksel değerlerine göre ayarlayın**Aralıkları neden ayarlamalısınız?*** İlgilendiğiniz alanda **kontrastı artırın*** **Alakasız değerleri hariç tutun** (ör. su kütleleri, çıplak toprak)
* Birden fazla görüntü veya tarih arasında **görselleştirmeyi standartlaştırın*** Dar bir değer aralığı içindeki **ince farkları vurgulayın**

### Aralık Dışı Değerleri Kırpma

Piksel değerleri tanımladığınız min/maks aralığının dışına düştüğünde, **kırpma modlarını** kullanarak bunların nasıl görüntüleneceğini kontrol edebilirsiniz.

#### **Kullanılabilir kırpma modu seçenekleri:**

#### 1. Minimum ve Maksimum

* **Minimumun altındaki**pikseller → gradyandaki**ilk renk** kullanılarak görüntülenir (örn. kırmızı)
* **Maksimumun üzerindeki**pikseller → gradyandaki**son renk** kullanılarak görüntülenir (örn. yeşil)
* **Kullanım örneği**: Uç değerleri vurgulayın, sınırlarda doygun renklerle tam veri aralığını gösterin
* **Örnek**: 0,2&#x27;nin altındaki tüm NDVI değerleri kırmızı, 0,9&#x27;un üzerindeki tüm değerler yeşil görünür

#### 2. Şeffaf Arka Plan

* **Aralığın dışındaki**pikseller**tamamen şeffaf** hale gelir
* Yalnızca **aralık içindeki** pikseller renk gradyanını gösterir
* **Kullanım örneği**: GIS katmanı, belirli değer aralıklarını izole etme, yalnızca ilgi alanlarını vurgulama
* **Örnek**: Yalnızca 0,4-0,7 aralığındaki NDVI değerlerini renkli göster, geri kalan her şeyi şeffaf

{% hint style="warning" %}
**Şeffaflık Sınırlaması**: Şeffaf pikseller görüntüleyicide arka plan rengi olarak görünür. İşleme sırasında dışa aktarıldığında, şeffaflık PNG formatında korunur ancak JPG formatında korunmaz.
{% endhint %}

#### 3. Dizin Arka Planı

* **Aralığın dışındaki**pikseller**gri tonlamalı** olarak görüntülenir (ham dizin değerlerini gösterir)
* **Aralık içindeki**pikseller**renk gradyanı** gösterir
* **Kullanım örneği**: İnce vurgular, ilgi alanlarını vurgularken bağlamı korur
* **Örnek**: Stres altındaki bitki örtüsünü renkli olarak vurgulayın (NDVI 0,3-0,5) ve sağlıklı alanları gri olarak gösterin

#### 4. Orijinal Arka Plan

* **Aralığın dışındaki**pikseller**orijinal multispektral görüntüyü** gösterir
* **Aralık içindeki**pikseller**renk gradyanını** gösterir
* **Kullanım örneği**: En sezgisel olanıdır - doğal görüntü bağlamını analitik renk katmanıyla birleştirir
* **Örnek**: Üzerine renk kodlu stresli alanlar eklenmiş olarak tarlanın/mahsulün gerçek görünümünü görün

### Doğru Kırpma Modunu Seçme

| Kırpma Modu              | En Uygun Olduğu Durum                                   | Görselleştirme Stili          |
| -------------------------- | ------------------------------------------ | ---------------------------- |
| **Minimum ve Maksimum**    | Tam veri gösterimi, bilimsel analiz     | Tüm pikseller renklidir           |
| **Şeffaf Arka Plan** | GIS katmanları, belirli aralıkları izole etme    | Aralık içinde renk, dışında boş |
| **Dizin Arka Planı**       | Hafif vurgu, veri bağlamını koruma  | Aralıkta renk, dışında gri  |
| **Orijinal Arka Plan**    | Raporlar, sunumlar, sezgisel analiz | Aralıkta renk, dışında fotoğraf |

### Özel LUT Renkleri Oluşturma

Görselleştirmenizi tam olarak kontrol etmek için, tek tek renk duraklarını düzenleyerek **özel renk gradyanları** oluşturabilirsiniz.**Özel bir gradyan oluşturmak için:**

1. LUT panelinde**gradyan önizleme çubuğunu** bulun
2. Gradyanın altındaki **renk örneği karelerini** arayın
3. Seçmek için **bir renk durağına tıklayın**

4. Bir**renk seçici** açılır
5. Aşağıdakileri kullanarak yeni bir renk seçin:
   * **Renk çarkı**: Görsel renk seçimi
   * **RGB/HSV kaydırıcıları**: Hassas renk kontrolü
   * **Hex kodu girişi**: Kesin renk belirtimi (ör. kırmızı için `#FF0000`)
6. **Yeni rengi uygulamak** için renk seçicinin dışına tıklayın
7. Degrade, görüntü üzerinde **anında güncellenir**

**Renk durakları ekleme veya kaldırma:*** **Durak ekleme**: Sonuna yeni bir renk örneği eklemek için + simgesine tıklayın
* **Durak kaldırma**: Renk örneğini kaldırmak için renk karesine çift tıklayın**Özelleştirme stratejileri:*** **Degradeyi ters çevirme**: Anlamı tersine çevirmek için renk sırasını değiştirin (ör. yeşil=düşük, kırmızı=yüksek)
* **Marka renkleri**: Raporlar için kuruluşunuzun renk paletiyle eşleştirin
* **Renk körü dostu**: Turuncu-mavi veya mor-sarı kombinasyonları kullanın
* **Baskı optimizasyonu**: Hem renkli hem de gri tonlamalı baskıda uygun renkleri seçin
* **Çoklu eşik**: Sınıflandırma için belirli değer eşiklerinde farklı renkler kullanın

{% hint style="info" %}
**Özel Gradyanları Kaydetme**: Özel gradyanlar kaydedilebilir ve yeniden kullanılabilir. Gelecekte kullanmak üzere özel renk şemalarınızı korumak için LUT panelindeki kaydet simgesine tıklayın.
{% endhint %}

***

## Etkileşimli İş Akışı

### Gerçek Zamanlı Güncellemeler

Sandbox&#x27;taki tüm LUT ayarlamaları görüntüyü **anında ve etkileşimli olarak** günceller:

* **Katmanı değiştirin** → Görüntü hemen değişir
* **Degradeyi seçin** → Renkler anında güncellenir
* **Değer aralığını ayarlayın** → Kontrast gerçek zamanlı olarak değişir
* **Sınıfları değiştirin** → Degrade düzgünlüğü hemen güncellenir
* **Kırpmayı değiştirin** → Arka plan görüntüsü anında değişir
* **Renkleri düzenle** → Özel gradyan anında uygulanır**&quot;Uygula&quot; düğmesine gerek yok** - tüm değişiklikler canlı ve etkileşimlidir!

{% hint style="success" %}
**Canlı Geri Bildirim**: Anlık görsel geri bildirim, analiz ihtiyaçlarınız için en uygun görselleştirmeyi bulana kadar farklı ayarları hızla denemenizi sağlar.
{% endhint %}

### İteratif İyileştirme İş Akışı

**Tipik LUT optimizasyon iş akışı:**

1.**İndeks katmanını seçin** (ör. RAW (Yansıma))
2. **İndeksi uygulayın** - Kamera filtresini ve indeks formülünü seçin, renkli daireleri indeks formülündeki uygun konuma sürükleyin
3. **LUT gradyanını uygulayın** - Red-Yellow-Green ön ayarıyla başlayın
4. **Piksel değerlerini inceleyin** - İmleci hareket ettirin, değer aralıklarını not edin
5. **Min/maks değerlerini ayarlayın** - Bitki örtüsüne odaklanmak için aralığı daraltın (örn. 0,2 ila 0,9)
6. **Kırpma seçin** - Bağlam için &quot;Orijinal Arka Plan&quot;ı deneyin
7. **Renkleri iyileştirin** - Belirli bir vurgu için gerekirse gradyanı özelleştirin
8. **Ayarları sonlandırın**- Ayarları belgelendirin ve dışa aktarma işlemi için Proje Ayarlarına kopyalayın

### Piksel Değeri İncelemesi

Etkili LUT aralıkları belirlemek için gerçek piksel değerlerini anlamak çok önemlidir:**Değerleri inceleme:**

1. Piksel değerleri, görüntünün İndeks veya hem İndeks hem de LUT**kutuları işaretlendiğinde** gösterilir.
2. **İmlecinizi** görüntünün farklı alanlarının üzerine getirin
3. İmlecinizi üzerine getirdiğinizde gösterge penceresinde görüntülenen **piksel değerlerini** gözlemleyin
4. Yakınlaştırarak, değişken bir değerle vurgulanan tek tek pikselleri görün
5. Farklı özellikler için değer aralıklarını **not alın**:
   * **Sağlıklı bitki örtüsü**: ör. NDVI 0,55-0,85
   * **Stresli bitki örtüsü**: ör. NDVI 0,30-0,50
   * **Çıplak toprak**: ör. NDVI 0,05-0,25
   * **Su** (varsa): ör. NDVI -0,05 ila 0,10**Piksel değerlerini kullanarak LUT aralıklarını ayarlama:**Piksel değerlerini inceledikten sonra, LUT min/maks değerlerinizi buna göre ayarlayın:**Örnek senaryo:*** **Gözlem**: Toprak değerleri = 0,05-0,25, Stresli = 0,25-0,50, Sağlıklı = 0,50-0,85
* **Hedef**: Yalnızca bitki sağlığını görselleştirin (toprağı hariç tutun)
* **LUT ayarları**: Min = `0.25`, Max = `0.85`
* **Kırpma**: Toprağı doğal renginde görmek için &quot;Orijinal Arka Plan&quot;
* **Sonuç**: Renk gradyanı yalnızca bitki örtüsüne uygulanır, toprak orijinal görüntü olarak gösterilir

{% hint style="info" %}
**Dinamik Aralık**: Farklı mahsuller, mevsimler ve büyüme aşamaları farklı değer aralıklarına sahip olacaktır. LUT aralıklarını ayarlamadan önce her zaman belirli veri kümenizde piksel değerlerini inceleyin.
{% endhint %}

***

## Özel İndeksler (Chloros+)

### Özel İndeks Formülleri Oluşturma

{% hint style="info" %}
**Nerede Oluşturulur**: Özel indeksler, işleme öncesinde**Proje Ayarları**&#x27;nda ve ayrıca Görüntü Görüntüleyici sandbox kenar çubuğunda yapılandırılabilir.
{% endhint %}

**Özel bir indeks oluşturmak için:**

1.**Proje Ayarları&#x27;nı** (işleme öncesinde) veya Görüntü Görüntüleyici sanal alan kenar çubuğunu açın
2. **İndeks formülü açılır menüsüne** gidin
3. **&quot;Özel&quot;** seçeneğini bulun (Chloros+ lisansıyla oturum açmış olmanız gerekir)
4. Bant değişkenlerini kullanarak **formülünüzü tanımlayın**:
   * Bant adları: `NIR`, `Red`, `Green`, `Blue`, `RedEdge`, vb.
   * İşlemciler: `+`, `-`, `*`, `/`, `^` (üstel)
   * Fonksiyonlar: `sqrt()`, `abs()`, vb. (destekleniyorsa)
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

{% hint style="warning" %}
**Formül Doğrulama**: Formülünüzün kameranızda mevcut bantları kullandığından emin olun. Örneğin, RedEdge yalnızca RedEdge filtresine sahip kameralarda kullanılabilir.
{% endhint %}

***

## Sonraki Adımlar

Artık Endeks/LUT Sandbox&#x27;ı anladığınıza göre:

* **İşlemeye uygulayın**: [Proje Ayarları](../project-settings/project-settings.md) bölümünde keşfedilen ayarları kullanın
* **Toplu işleme**: Optimize edilmiş indeksleri tüm veri kümelerine uygulayın
* **Daha fazla bilgi**: [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümünü okuyun

İlgili belgeler:

* [**Görüntü Katmanları**](image-layers.md) - Katman yönetimi ve görselleştirme
* [**Görüntüyü Tam Ekran Olarak Açma**](opening-an-image-full-screen.md) - Görüntü Görüntüleyicinin temelleri
* [**Görüntüleri İşleme (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Tam işleme iş akışı
