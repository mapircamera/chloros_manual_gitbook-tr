# Hedef Görüntülerin Seçilmesi

Hangi görüntülerde kalibrasyon hedefleri bulunduğunu işaretlemek, Chloros işleme akışını önemli ölçüde hızlandıran hayati bir adımdır. Hedef görüntüleri önceden seçerek, Chloros&#x27;in veri kümenizde bulunan her görüntüyü kalibrasyon hedefleri açısından taramasına gerek kalmaz.

## Neden Hedef Görüntüleri İşaretlemeli?

### İşleme Hızı

Hedef görüntüler işaretlenmezse, Chloros şunları yapmak zorundadır:

* Projenizdeki her bir görüntüyü taramak
* Her görüntü üzerinde hedef algılama algoritmalarını çalıştırmak
* Yüzlerce veya binlerce görüntüyü gereksiz yere kontrol etmek

**Sonuç**: İşleme süresi, özellikle büyük veri kümeleri için önemli ölçüde uzayabilir.

### Hedef Görüntüler İşaretlendiğinde

Belirli görüntüler için Hedef sütununu işaretlediğinizde:

* Chloros, yalnızca işaretlenen görüntülerde hedef taraması yapar
* Hedef algılama çok daha hızlı tamamlanır
* Genel işlem süresi büyük ölçüde azalır

{% hint style="success" %}
**Hız Artışı**: 500 görüntülük bir veri kümesinde 2-3 hedef görüntüyü işaretlemek, hedef algılama süresini 30 dakikadan fazla süren bir işlemden 1 dakikanın altına düşürebilir.
{% endhint %}

***

## Hedef Görüntüleri İşaretleme

### Adım 1: Hedef Görüntülerinizi Belirleyin

Dosya Tarayıcı&#x27;da içe aktarılan görüntülerinizi inceleyin ve hangi görüntülerin kalibrasyon hedefleri içerdiğini belirleyin.

**Yaygın senaryolar:*** **Çekim öncesi hedef**: Oturum başlamadan önce çekilen
* **Çekim sonrası hedef**: Oturum tamamlandıktan sonra çekilen
* **Saha içi hedefler**: Çekim alanı içine yerleştirilen hedefler
* **Birden fazla hedef**: Oturum başına 2-3 hedef görüntü (önerilir)

### Adım 2: Hedef Sütununu Kontrol Edin

Kalibrasyon hedefi içeren her görüntü için:

1. Dosya Tarayıcı tablosunda görüntüyü bulun
2. **Hedef** sütununu (en sağdaki sütun) bulun
3. O görüntünün Hedef sütunundaki onay kutusunu tıklayın
4. Hedef içeren tüm görüntüler için bu işlemi tekrarlayın

### Adım 3: Seçiminizi Doğrulayın

İşlemeye başlamadan önce şunları iki kez kontrol edin:

* [ ] Kalibrasyon hedefleri içeren tüm görüntüler işaretlenmiştir
* [ ] Hedef içermeyen görüntüler yanlışlıkla işaretlenmemiştir
* [ ] İşaretlenen görüntülerde hedefler açıkça görülebilir

***

## Hedef Görüntüler için En İyi Uygulamalar

### Hedef Yakalama Yönergeleri

**Zamanlama:**

* Yakalama oturumunuzdan hemen önce ve oturum boyunca hedef görüntüleri yakalayın
* DAQ ışık sensörünüzle aynı aydınlatma koşullarında
* En iyi sonuçlar için ideal olarak hedef görüntüleri mümkün olduğunca sık yakalayın. Aksi takdirde, zaman içinde kalibrasyonu ayarlamak için ışık sensörü verileri kullanılacaktır.

**Kamera Konumu:**

* Kamerayı, hedef üzerinde ortalanacak ve görüntünün merkezinin yaklaşık %40-60&#x27;ını kaplayacak şekilde tutun.
* Kamerayı hedef yüzeye paralel/nadir konumda tutun

**Aydınlatma:**

* DAQ ışık sensörünüzle aynı ortam aydınlatması
* Hedef yüzeylerde gölgelerden kaçının
* Işık kaynağını vücudunuz, aracınız veya bitki örtüsüyle engellemeyin
* Bulutlu hava koşulları en tutarlı sonuçları sağlar

**Hedef Durumu:**

* Hedef panelleri temiz ve kuru tutun
* 4 panelin tümü açıkça görülebilir ve engelsiz olmalıdır
* Mümkünse hedefler ışık kaynağına dik/nadir konumda olmalıdır

### Kaç Adet Hedef Görüntü?

**Minimum:**Her oturumda 1 hedef görüntüsü.**Önerilen:** Her oturumda 3-5 hedef görüntüsü.**En iyi uygulama programı:**

* Işık sensörü kayda başladıktan kısa bir süre sonra 3-5 görüntü yakalayın
* En iyi sonuçlar için çekimler arasında kamerayı döndürün
* İsteğe bağlı: Aydınlatma koşulları sürekli değişiyorsa, oturum ortasında periyodik olarak

***

## Birden Fazla Kamera ile Çalışma

### Çift Kamera Kurulumları

İki MAPIR kamerasını aynı anda kullanıyorsanız (ör. Survey3W RGN + Survey3N OCN):

1. Hedef görüntüleri **her iki kamera** ile aynı anda yakalayın
2. Her iki kamera için **aynı fiziksel hedefi** kullanın
3. Dosya Tarayıcı&#x27;da **her iki kamera türü** için hedef görüntüleri işaretleyin
4. Chloros, her kameranın kalibrasyonu için uygun hedefleri kullanacaktır

### Kamera Modeli Sütunu

**Kamera Modeli** sütunu, hangi görüntünün hangi kameradan geldiğini belirlemenize yardımcı olur:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* vb.

Bu sütunu, projenizdeki her kamera türü için hedefleri işaretlediğinizi doğrulamak için kullanın.

***

## Hedef Algılama Ayarları

### Algılama Hassasiyetini Ayarlama

Chloros hedeflerinizi doğru şekilde algılamıyorsa, [Proje Ayarları](adjusting-project-settings.md) bölümündeki bu ayarları değiştirin:**Minimum kalibrasyon örnek alanı:*** **Varsayılan**: 25 piksel
* Küçük nesnelerde yanlış algılamalar alıyorsanız **artırın*** Hedefler algılanmıyorsa **azaltın**

**Minimum hedef kümelenmesi:*** **Varsayılan**: 60
* Hedefler birden fazla algılamaya bölünüyor ise **artırın*** Renk farklılığı olan hedefler tam olarak algılanmıyorsa **azaltın**

***

## Yaygın Hedef Görüntü Sorunları

### Sorun: Hedef Algılanmıyor

**Olası nedenler:**

* Dosya Tarayıcısında hedef görüntüler işaretlenmemiş
* Çerçeve içinde hedef çok küçük (&lt; görüntünün %30&#x27;u)
* Yetersiz aydınlatma (gölgeler, parlama)
* Hedef algılama ayarları çok katı

**Çözümler:**

1. Doğru görüntüler için Hedef sütununun işaretli olduğunu doğrulayın
2. Önizlemede hedef görüntü kalitesini inceleyin
3. Kalite düşükse hedefleri yeniden yakalayın
4. Gerekirse hedef algılama ayarlarını düzenleyin

### Sorun: Yanlış Hedef Algılamaları

**Olası nedenler:**

* Hedeflerle karıştırılan beyaz binalar, araçlar veya zemin kaplamaları
* Bitki örtüsündeki parlak lekeler
* Algılama hassasiyetinin çok düşük olması

**Çözümler:**

1. Algılama kapsamını sınırlamak için yalnızca gerçek hedef görüntülerini işaretleyin
2. Minimum kalibrasyon örnekleme alanını artırın
3. Minimum hedef kümelenme değerini artırın
4. Hedef görüntülerinde yalnızca hedefin göründüğünden emin olun (arka planda en az karışıklık)

***

## Doğrulama Kontrol Listesi

İşlemeye başlamadan önce hedef görüntü seçiminizi doğrulayın:

* [ ] Her oturumda en az 1 hedef görüntüsü işaretlendi
* [ ] Tüm hedef görüntüler için Hedef sütunundaki onay kutuları işaretlendi
* [ ] Hedef görüntüler, anketle aynı zaman aralığında yakalandı
* [ ] Tıklandığında önizlemede hedefler net bir şekilde görünüyor
* [ ] Her hedef görüntüde 4 kalibrasyon panelinin tümü görünüyor
* [ ] Hedeflerin üzerinde gölge veya engel yok
* [ ] Çift kamera için: Her iki kamera türü için de hedefler işaretlendi

***

## Hedefsiz İşleme

### Kalibrasyon Hedefleri Olmadan İşleme

Bilimsel çalışmalar için önerilmese de, hedefler olmadan işleme yapabilirsiniz:

1. Tüm Hedef sütunundaki onay kutularını işaretlemeyin
2. Proje Ayarları&#x27;nda &quot;Yansıma kalibrasyonu&quot;nu **devre dışı bırakın**

3. Vinyet düzeltmesi yine de uygulanacaktır
4. Çıktı, mutlak yansıma için kalibre edilmeyecektir

{% hint style="warning" %}
**Önerilmez**: Yansıma kalibrasyonu olmadan, piksel değerleri yalnızca göreceli parlaklığı temsil eder, bilimsel yansıma ölçümlerini değil. Doğru ve tekrarlanabilir sonuçlar için kalibrasyon hedeflerini kullanın.
{% endhint %}

***

## Sonraki Adımlar

Hedef görüntülerinizi işaretledikten sonra:

1. **Ayarlarınızı gözden geçirin** - Bkz. [Proje Ayarlarını Ayarlama](adjusting-project-settings.md)
2. **İşlemeyi başlatın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
3. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)

Kalibrasyon hedefleri hakkında daha fazla bilgi için bkz. [Kalibrasyon Hedefleri](../calibration-targets.md).
