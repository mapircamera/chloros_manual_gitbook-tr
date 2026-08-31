# İndeks/LUT Sandbox

İndeks/LUT Sandbox, Chloros Görüntü Görüntüleyici’nin kenar çubuğunda yer alan etkileşimli çalışma alanıdır. Bir formül seçin, kameranızın kanallarını bu formüle bağlayın, bir degrade ile renklendirin ve değer aralığını ayarlayın — siz bunları yaparken görüntü anında güncellenir. 1.2.0 sürümünden itibaren, yeniden işleme gerek kalmadan tek bir görüntü veya tüm proje için **oluşturduğunuz içeriği kaydedebilirsiniz**.

## Sandbox&#x27;ın Amacı

| Index/LUT Sandbox (etkileşimli)        | Proje İşleme (toplu)       |
| -------------------------------------- | -------------------------------- |
| Her seferinde tek bir görüntü, anında geri bildirim  | Tek seferde tüm veri kümesi     |
| Deneysel ve yinelemeli             | Önceden yapılandırılmış ayarlar          |
| Canlı olarak işler; yalnızca siz istediğinizde kaydeder  | Her zaman çıktı dosyalarını yazar      |
| Doğru ayarları bulmak için mükemmel | Ayarlar kesinleştiğinde en iyi sonuç |

{% hint style="success" %}
**Alışılmış iş akışı**: Görselleştirme istediğiniz sonucu verene kadar Sandbox’ta ayarlamalar yapın, ardından ya doğrudan Sandbox’tan dışa aktarın ya da aynı indeks ve LUT ayarlarını [Proje Ayarları](../project-settings/project-settings.md) bölümüne kopyalayın; böylece bir sonraki işleme çalışmasında bu ayarlar her görüntüye uygulanır.
{% endhint %}

***

## Sandbox&#x27;ı açma

1. Izgaradaki bir görüntüye tıklayın — görüntü, **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesinde tam ekran olarak açılır
2. Henüz açık değilse, **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> simgesine tıklayarak sol kenar çubuğunu açın
3. Sağ üstteki katman açılır menüsünden çok bantlı bir katman seçin — **RAW (Yansıma)** genellikle tercih edilen seçenektir, çünkü kalibre edilmiş yansıma değerleri üzerinden hesaplanan indeks değerleri görüntüler arasında karşılaştırılabilir

Kenar çubuğunda yukarıdan aşağıya doğru şunlar gösterilir:

* görüntü adı ve kamera modeli
* **Görüntüleri Dışa Aktar/Kaydet**düğmesi —**Endeks**veya**LUT** seçeneği işaretlendiğinde görünür
* **Endeks**ve**LUT** onay kutuları
* indeks yapılandırma paneli
* okuma, histogram ve GSD kontrolünü içeren **İmleç Değerleri** paneli

{% hint style="warning" %}
**Mono kameralar için kullanılamaz.** Tek bantlı bir LATTICE M3M görüntüsünde her iki onay kutusu da devre dışıdır ve araç ipucu olarak _&quot;Mono (M3M) sensörler için kullanılamaz&quot;_ mesajı görüntülenir — tek bir bantta çok bantlı bir indeks tanımlanamaz. M3M kameralardan indeks hesaplamak için, iki veya daha fazla görüntüyü hizalanmış çok bantlı bir yığın halinde birleştirin ve LATTICE indeks motorunu kullanın.
{% endhint %}

***

## İndeks uygulama

1. Kenar çubuğunun üst kısmındaki **İndeks** kutucuğunu işaretleyin
2. Soldaki açılır menüden kameranızın filtresini seçin (`RGN`, `OCN`, `NGB`, `RGB`, `RE`, `NIR`)
3. Sağdaki açılır menüden bir indeks formülü seçin — 27 adet yerleşik formülün yanı sıra kaydettiğiniz özel formüller de mevcuttur
4. Formül, her bant yuvasında boş bir daire ile birlikte aşağıdaki gibi matematiksel olarak görüntülenir. **Renkli bir kanal dairesini bir yuvaya sürükleyerek** onu bağlayın
5. Formülün kullandığı tüm yuvalar bağlandığında, görüntü güncellenir ve indeks değerlerini gösterir
6. Değerleri okumak için imleci görüntünün üzerine getirin; **İmleç Değerleri** paneli, imlecin altındaki değeri içeren bir indeks satırı ekler

Bağlanmış bir yuvayı silmek için üzerine çift tıklayın. Tamamlanmamış bir formül, bir hata değil, sürükleme işleminin normal bir aşamasıdır — formül tamamlanana kadar görüntü güncellenmez.

Kanal daireleri renk kodludur: kırmızı = Red, yeşil = Green, mavi = Blue, turuncu = Orange, camgöbeği = Cyan, mor = NIR, macenta = RE. Aynı renkler, İmleç Değerleri panelindeki kanal noktaları ve histogram eğrileri için de kullanılır.

### NDVI örneği

```

Formula: (NIR - Red) / (NIR + Red)

For a Survey3W RGN camera:
  NIR = 850 nm band
  Red = 661 nm band

Result range:          -1.0 to +1.0
Typical vegetation:     0.4 to 0.9
Stressed vegetation:    0.2 to 0.4
Bare soil:              0.0 to 0.2
Water:                 -0.1 to 0.1
```

Tam formül referansı için — üç ön ayar listesinin tümü ve hangi isimlerin nerede çalıştığı — bkz. [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md).

### İndeks işaretli ancak LUT yok

Görüntü, iki eşik değeri arasında uzatılmış olarak **gri tonlamalı** olarak çizilir. Bu kasıtlıdır: indeks görüntüsü skaler veridir ve gri tonlamalı görüntü, bunun en doğru şekilde işlenmiş halidir. Renk istiyorsanız bir LUT ekleyin.***

## LUT&#x27;larla (Arama Tabloları) Çalışma

Bir **Arama Tablosu**, indeks değerlerini renklere eşler: NDVI 0,65 girdiğinde, belirli bir yeşil renk çıktısı verir. Verileri değiştirmez — verileri okuma şeklinizi değiştirir.

### LUT Ekleme

1. Formülün altındaki **<img src="../.gitbook/assets/image (1) (1) (1).png" alt="" data-size="line">&quot;+ LUT Ekle&quot;** düğmesine tıklayın
2. Bir renk gradyanı seçin
3. Kırpma minimum ve maksimum değerlerini ayarlayın
4. Bir Kırpma Modu seçin
5. Yan çubuktaki **LUT** kutucuğunu işaretleyerek render işlemini gerçekleştirin

LUT, indeks üzerinde gerçekten yapılandırılana kadar devre dışı kalır.

### Renk gradyanı seçme

**Gradyan çubuğunun**üzerine fareyi getirin ve ön ayar listesini açın — Chloros,**yedi** adet gradyan ön ayarı sunar:

| # | Gradyan                            | Şekil                                                               |
| - | ----------------------------------- | ------------------------------------------------------------------- |
| 1 | Red → Sarı → Green (**varsayılan**)  | Dağılan — genel bitki örtüsü algısıyla uyumludur, yeşil = sağlıklı |
| 2 | Mor → Sarı → Green             | Dağılan, belirgin bir alt uç ile                                  |
| 3 | Kahverengi → Beyaz → Blue                | Açık bir orta nokta etrafında dağılan                                   |
| 4 | Siyah → Mor → Pembe → Soluk sarı | Sıralı, koyudan açıka                                           |
| 5 | Red → Sarı → Blue                 | Açık bir orta nokta etrafında ıraksak                                   |
| 6 | Mor → Blue → Green → Sarı      | Sıralı, koyudan açıka                                           |
| 7 | Orange → Beyaz → Mor             | Açık renkli bir orta nokta etrafında ıraksayan                                   |

**Iraksayan**bir degrade, pencerenizin ortasına nötr bir renk yerleştirir; bu, orta noktanın bir anlam ifade ettiği durumlarda (bir eşik değeri, bir başlangıç tarihi) iyi bir okunabilirlik sağlar.**Sıralı** bir gradyan, monoton bir şekilde koyudan açıka doğru ilerler; bu, yalnızca &quot;daha fazla&quot; ve &quot;daha az&quot; gibi değerlere sahip bir miktar için iyi bir okunabilirlik sağlar.

Her ön ayarın yedi renk durağı vardır. Bir ön ayara tıkladığınızda, LUT kutusu işaretliyse görüntü anında güncellenir.

### Renk duraklarını düzenleme

Degrade çubuğunun altında, her durak için birer tane olmak üzere bir dizi renk örneği bulunur:

* **Rengi değiştirme**: Bir renk örneğine tıklayarak renk seçiciyi açın (renk çarkı, RGB/HSV kaydırıcıları veya `#FF0000` gibi bir onaltılık kod)
* **Bir durak ekleyin**: satırın sonundaki**+** düğmesine tıklayın — beyaz bir durak eklenir
* **Bir durağı kaldırın**: renk örneğine**çift tıklayın*** **Düzenlenmiş gradyanı saklayın**: Düzenlediğiniz gradyanı ön ayar listesine eklemek ve daha sonra tekrar seçebilmek için gradyan çubuğunun yanındaki kaydet simgesine tıklayın

Bir indeks üzerinde yapılandırdığınız gradyan, projenin ayarlarında o indeksle birlikte saklanır; böylece proje kapatılıp yeniden açıldığında da korunur.

**Daha az durak**, bir sınıflandırma gibi okunan belirgin bölgeler oluşturur;**daha fazla durak** ise pürüzsüz, neredeyse fotoğraf kalitesinde geçişler sağlar. Üç ila beş durak, sunum slaytları ve sınıflandırma haritaları için uygundur; altı ila on durak genel analizler için uygundur; on beş veya daha fazla durak ise ayrıntılı inceleme ve yayın şekilleri için uygundur.

### Değer aralığını ayarlama

Eşik kontrolü, −1 ile +1 arasında değişen, her iki ucunda kesin değerler için düzenlenebilir metin kutuları ve bir **AUTO**düğmesi bulunan**çift tutamaklı bir kaydırıcı**dır.

* Herhangi bir tutamağı sürükleyin veya kutucuğuna bir sayı yazıp Enter tuşuna basın
* **AUTO**, aralığı görüntünün geçerli indeks değerlerinin**

2. ve 98. persentillerine** ayarlar — bu, uç değerleri göz ardı eden iyi bir başlangıç noktasıdır. Chloros, sonucu uyarlanabilir şekilde yuvarlar: çok dar bir aralık için 4 ondalık basamağa, dar bir aralık için 3 ondalık basamağa, diğer durumlarda ise 2 ondalık basamağa
* AUTO düğmesine tekrar basana kadar, manuel olarak yapılan herhangi bir ayar AUTO ayarından önceliklidir

Örnek NDVI pencereleri:

| Hedef                                    | Min  | Maks |
| --------------------------------------- | ---- | --- |
| Her şeyi göster                         | −1,0 | 1,0 |
| Yalnızca bitki örtüsü, toprak ve suyu hariç tut | 0,2  | 0,9 |
| Yalnızca sağlıklı bitki örtüsü                 | 0,5  | 0,9 |
| Stresi vurgula                        | 0,2  | 0,5 |

Aralığı daraltmak, ilgilendiğiniz alanın içindeki kontrastı artırır ve geri kalan her şeyi aralığın dışına iter — burada **Kırpma Modu**, bu öğelere ne olacağına karar verir.***

## Kırpma modları

Bir pikselin indeks değeri minimum/maksimum aralığının dışına çıktığında, Kırpma Modu bu pikselin nasıl çizileceğini belirler.

| Açılır menü etiketi                  | Kaydedilen değer      | Aralık dışı pikseller şu şekilde çizilir                                                                                                |
| ------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Minimum ve Maksimum** (varsayılan) | `clip`            | Degradenin en yakın uç rengi — minimumun altındaki değerler ilk rengi, maksimumun üzerindeki değerler son rengi alır |
| **Şeffaf Arka Plan**      | `transparent`     | Tamamen şeffaf (gerçek alfa)                                                                                                  |
| **Dizin Arka Planı**| `indexColor`      | Gri tonlamalı, görüntünün**tam** dizin aralığı boyunca uzanır; böylece aralık dışındaki yapılar gri renkte görünür kalır                |
| **Orijinal Arka Plan**         | `backgroundColor` | Altta yatan görüntünün kendisi; böylece renk katmanı gerçek sahnenin üzerine yerleştirilir                                                |

| Mod                       | En uygun olduğu durum                               | Görünüm                                      |
| -------------------------- | -------------------------------------- | ----------------------------------------- |
| **Minimum ve Maksimum**      | Tam veri gösterimi, bilimsel analiz | Her piksel renklidir                      |
| **Şeffaf Arka Plan** | GIS katmanları, bir değer aralığını izole etme   | Pencere içindeki alan renkli, dışı boş |
| **Endeks Arka Plan**       | Veri bağlamını korurken vurgu    | İçeride renkli, dışarıda gri               |
| **Orijinal Arka Plan**    | Raporlar ve sunumlar              | İçeride renkli, dışarıda fotoğraf         |

{% hint style="info" %}
**Verisiz pikseller her modda her zaman şeffaftır.** İndeksi sonlu olmayan (0/0 bölünmesi) veya tam olarak −1,0 ya da +1,0 olan (doygunluk işaretçileri; bir aralık sıfır okurken diğeri okumadığında) bir piksel, uç değer olarak değil, veri içermeyen piksel olarak değerlendirilir. Bu, aşırı parlak alanları ve karanlık gölgeleri, karedeki en uç değerler olarak gösterilmek yerine renk skalasından uzak tutar. Aynı kural, hangi piksellerin AUTO eşiklerine ve indeks histogramına dahil edileceğini belirler; böylece üçü de birbiriyle uyumludur.
{% endhint %}

Dışa aktarım PNG olarak kaydedildiğinde şeffaflık korunur. Bu, JPG formatında gösterilemez.

***

## Ayarlamalar sırasında değerleri okuma

Yapılandırma panelinin altındaki **İmleç Değerleri** paneli, Sandbox için bir ölçüm aracıdır:

* İmleci görüntünün üzerine getirin ve kanal başına kaynak değerleri ile kendi satırındaki indeks değerini okuyun
* Histogramın üstündeki **INDEX** düğmesini etkinleştirerek karedeki indeks değerlerinin dağılımını görüntüleyin; iki klip eşik değeri turuncu kesikli çizgilerle, imlecin değeri ise beyaz bir çizgiyle gösterilir — bu, verilerinizi gerçekten içeren bir pencere seçmenin en hızlı yoludur
* **CURSOR** seçeneğini etkinleştirerek imlecin altındaki değerlerde işaret çizgilerini görüntüleyin
* 60×&#x27;ten fazla yakınlaştırın (GSD blok boyutu ayarlanmışsa daha az) ve değişken değere sahip tek tek görüntülenen pikselleri vurgulayın

Pratik bir yöntem:

1. Sağlıklı bitki örtüsü, stres altındaki bitki örtüsü, çıplak toprak ve su üzerindeki değerleri not edin
2. Bu kümelerin indeks histogramında nerede yer aldıklarına bakın
3. İlgilendiğiniz kümeyi kapsayacak şekilde min/maks değerlerini ayarlayın
4. Bir kırpma modu seçin — _Orijinal Arka Plan_, etrafındaki sahneyi görünür tutar

***

## Sandbox&#x27;tan Dışa Aktarma

Yukarıdaki her şey, kaydetene kadar canlı bir önizlemedir. Kenar çubuğunun üst kısmındaki **Görüntüleri Dışa Aktar/Kaydet** düğmesi, kenar çubuğunun üzerine kayan bir pencere açar (görüntüyü kapatmaz, böylece karar verdiğiniz şeyi hâlâ görebilirsiniz).

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>### Seçenekler

| Seçenek                          | Etki                                                                                                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mevcut resme uygula**      | Gösterilen resmi, bu ayarlarla aynen kaydeder                                                                                                |
| **Tüm proje resimlerine uygula** | Projedeki her resim üzerinde aynı yapılandırmayı yeniden uygular. Bu dizin için gerekli bantlara sahip olmayan resimler atlanır, hata olarak değerlendirilmez |
| **Dizin/LUT gradyan çubuğu**      | Ayrıca, her dışa aktarım için değer aralığı etiketlenmiş ayrı bir açıklama görüntüsü yazar                                                                     |
| **Dizin histogramı**             | Ayrıca, her dışa aktarım için verilerin minimum/maksimum değerlerini ve kırpma eşiklerini gösteren ayrı bir histogram görüntüsü yazar                                               |

Görüntü sekmesindeki **GSD blok boyutu** 1&#x27;den büyükse, işlemi onaylamadan önce panelde bu durum belirtilir: dışa aktarma, blok ortalaması dahil olmak üzere, o anda gördüğünüz görüntüyü kaydeder. Tam çözünürlük istiyorsanız, önce GSD ayarını tekrar 1&#x27;e getirin.

### Dosyalar nereye kaydedilir?

**Dışa Aktar**düğmesine her tıklandığında,**yeni ve bir daha asla kullanılmayacak bir klasör** ayrılır:

```
<project folder>/Sandbox_Exports/<IndexName>_<Index|LUT>_<NNN>/
```

Örnekler: `Sandbox_Exports/NDVI_LUT_001/`, ardından bir sonraki çalıştırma için `Sandbox_Exports/NDVI_LUT_002/`. Numaralandırma, diskte halihazırda bulunanları tarayarak belirlenir; bu sayede yeniden başlatma işlemlerinden ve elle sildiğiniz klasörlerden etkilenmez. Hiçbir şeyin üzerine yazılmaz — Sandbox’ın asıl amacı, bir denemeyi bir öncekiyle karşılaştırmaktır.

Klasörün içinde, her görüntü için:

| Dosya                                                   | İçerik                                                   |
| ------------------------------------------------------ | ---------------------------------------------------------- |
| `<source name>_<IndexName>_<Index\|LUT>.png`           | Görüntüleyicide gösterilenin piksel piksel aynısı olan işlenmiş görüntü |
| `<source name>_<IndexName>_<Index\|LUT>_legend.png`    | İstenirse, gradyan çubuğu yan dosyası                     |
| `<source name>_<IndexName>_<Index\|LUT>_histogram.png` | İstenirse, indeks histogramı yan dosyası                  |

Bu iki yan dosya, ana görüntü blok ortalaması alınmış olsa bile her zaman **tam çözünürlükte** yazılır: blok boyutu ekran çözünürlüğüdür ve her iki yan dosya da piksel başına gerçek indeks değerlerini okur. Ayrıca, ekran versiyonlarından daha fazla bilgi yazdırırlar — her ikisi de uzatma penceresini _ve_ gerçek veri minimum/maksimum değerlerini gösterir; böylece kaydedilen açıklama, proje açılmadan aylar sonra bile okunabilir kalır.

### İlerleme ve sonuçlar

Tüm projenin dışa aktarımı birkaç dakika sürer; bu nedenle işlem, sistemi bloke etmek yerine canlı bir ilerleme kanalı üzerinden durum raporu verir:

* Bir ilerleme çubuğu, `current / total` yazısını ve yazılmakta olan dosyayı gösterir
* İşlem bittiğinde, bölmede kaç adet görüntünün dışa aktarıldığı, kaç tanesinin atlandığı ve çıktı klasörünün yolu bildirilir
* Atlanan görüntüler, nedenleriyle birlikte listelenir (en fazla beş tane gösterilir, ardından &quot;+N daha fazla&quot; satırı gelir). Genellikle bunun nedeni, bu indeksin ihtiyaç duyduğu kanallara sahip olmayan bir katmandır
* Projedeki **hiçbir** görüntü dizinini kullanamıyorsa, işlem size boş bir klasör bırakmak yerine hata bildirir

Aynı anda yalnızca bir sandbox dışa aktarma işlemi çalışabilir. Bir işlem devam ederken ikincisinin başlatılması, iki işlemin aynı proje dosyası üzerinde çakışmasına izin vermek yerine net bir mesajla reddedilir.

### Izgara, çalışmayı seçer

Tamamlanan her çalışma, [görüntü ızgarası](image-grid.md) araç çubuğunda `<IndexName> <Index|LUT> <NNN>` etiketli kendi düğmesi olarak görünür. İşlemleri bu şekilde karşılaştırabilirsiniz: farklı gradyanlar veya eşik değerleriyle iki kez dışa aktarım yapın, ardından ızgaradaki iki düğme arasında geçiş yapın.

***

## Özel indeks formülleri (Chloros+)

{% hint style="info" %}
**Nerede oluşturulur**: Sandbox kenar çubuğunda veya işleme öncesinde**Proje Ayarları**&#x27;nda. Her ikisi de aynı proje düzeyindeki listeye yazılır.
{% endhint %}

1. Dizin formülü açılır menüsünden özel formül hesaplayıcısını açın (uygun bir Chloros+ aboneliği ile oturum açmanız gerekir)
2. **Bant-slot sembollerini** kullanarak formülü yazın: `x`, `y`, `z`, `a`, `b`, `c` sembollerini kullanarak formülü yazın — bunlar bant adları değildir
3. Kullanılabilir operatörler: `+`, `-`, `*`, `/`, `^` ve `()` (gruplama için)
4. Kullanılabilir fonksiyonlar: `sqrt()`, `log()`, `ln()`, `abs()`, `sign()`, `log1p()`, `log2()`
5. Adını verin ve kaydedin — formül açılır menüsünün en altında görünür ve tıpkı yerleşik bir ön ayar gibi kanal dairelerini sürükleyerek yuvalarını bağlayabilirsiniz

```

Modified NDVI with an offset:   (y-x)/(y+x+0.5)
Simple ratio:                   y/x
Three-band difference:          (y-x)/(y+x-z)
Squared ratio:                  (y/x)^2
```

{% hint style="warning" %}
**Özel formüller yalnızca GUI&#x27;de kullanılabilir.** CLI/SDK `--indices` seçeneği, 22 yerleşik ön ayar adını genişletir ve özel formülleriniz dahil olmak üzere diğer her şeyi sessizce atlar. Özel bir formülü toplu olarak işlemek için, bunu Proje Ayarları&#x27;nda yapılandırıp işlemeyi çalıştırın veya Sandbox&#x27;ın &quot;Tüm proje görüntülerine uygula&quot; dışa aktarma özelliğini kullanın.
{% endhint %}

***

## Sorun Giderme

### &quot;Bu katmanda bu indeksin ihtiyaç duyduğu kanallar yok&quot;

Formül, geçerli katmanda bulunmayan bir kanal konumunu okur — örneğin, tek veya iki kanallı bir dosyada üç yuvalı bir indeks. Çok bantlı bir katmana (yansıma veya debayered) geçin veya kameranızın filtresine uygun bir indeks seçin.

### &quot;Görüntü işleme arka ucuna ulaşılamadı&quot;

Arka uç yanıt vermiyor. Günlük sekmesini kontrol edin; arka uç yeniden başlatılıyorsa, geri döndüğünde Sandbox kendi kendine düzelir.

### Bir daireyi sürüklediğimde görüntü değişmedi

Formül henüz tamamlanmamıştır. Tamamlanmamış bir formül, normal bir sürükleme durumu olarak değerlendirilir — hiçbir şey işlenmez ve hata olarak rapor edilmez. Formülün kullandığı her alanı doldurun.

### Görüntünün tamamı tek renkte

Klip pencereniz muhtemelen verilerin çok dışında. **AUTO**tuşuna basarak pencereyi 2. veya 98. persantile hizalayın ya da**INDEX** histogramını etkinleştirerek verilerin gerçekte nerede olduğunu görün.

### Dışa aktarılan renkler gördüğümle uyuşmuyor

Uyuşması gerekir — dışa aktarım yolu, kırpma modu alfa değeri dahil olmak üzere canlı önizlemenin kasıtlı olarak aynısıdır ve blok ortalamalama işlemi, tam olarak görüntüleyicinin yaptığı gibi renklendirme _sonrasında_ uygulanır. Eğer farklılık varsa, görüntüleme ile dışa aktarma arasında GSD blok boyutunun değişmediğini kontrol edin.

***

## Sonraki Adımlar

* [**Görüntü Katmanları**](image-layers.md) — hangi katman üzerinde indeks çalıştırılacağı ve değerlerinin ne anlama geldiği
* [**Görüntüyü Tam Ekranda Açma**](opening-an-image-full-screen.md) — imleç okuması, histogram ve GSD kontrolü hakkında ayrıntılı bilgi
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) — her yüzeyde bulunan tüm ön ayarlar
* [**Proje Ayarları**](../project-settings/project-settings.md) — belirlediğiniz ayarları bir işleme döngüsüne aktarma
