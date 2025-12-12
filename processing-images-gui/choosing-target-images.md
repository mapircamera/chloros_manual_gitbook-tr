# Hedef Görüntüleri Seçme

Hangi görüntülerin kalibrasyon hedefleri içerdiğini işaretlemek, Chloros işleme sürecini önemli ölçüde hızlandıran çok önemli bir adımdır. Hedef görüntüleri önceden seçerek, Chloros&#x27;in veri setinizdeki her görüntüyü kalibrasyon hedefleri için taramasına gerek kalmaz.

## Neden Hedef Görüntüleri İşaretlemelisiniz?

### İşlem Hızı

Hedef görüntüleri işaretlemeden, Chloros şunları yapmak zorundadır:

* Projenizdeki her bir görüntüyü taramak
* Her görüntüde hedef algılama algoritmaları çalıştırmak
* Yüzlerce veya binlerce görüntüyü gereksiz yere kontrol etmek

**Sonuç**: İşlem, özellikle büyük veri kümeleri için önemli ölçüde daha uzun sürebilir.

### İşaretlenmiş Hedef Görüntülerle

Belirli görüntüler için Hedef sütununu işaretlediğinizde:

* Chloros yalnızca işaretlenen görüntüleri hedefler için tarar
* Hedef algılama çok daha hızlı tamamlanır
* Genel işlem süresi büyük ölçüde azalır

{% hint style=&quot;success&quot; %}
**Hız Artışı**: 500 görüntülük bir veri kümesinde 2-3 hedef görüntüyü işaretlemek, hedef algılama süresini 30 dakikadan 1 dakikanın altına düşürebilir.
{% endhint %}

***

## Hedef Görüntüleri İşaretleme

### Adım 1: Hedef Görüntülerinizi Belirleyin

Dosya Tarayıcı&#x27;da içe aktardığınız görüntüleri inceleyin ve hangi görüntülerin kalibrasyon hedefleri içerdiğini belirleyin.

**Yaygın senaryolar:**

* **Ön çekim hedefi**: Oturum başlamadan önce çekilen
* **Son çekim hedefi**: Oturum tamamlandıktan sonra çekilen
* **Saha içi hedefler**: Çekim alanına yerleştirilen hedefler
* **Birden fazla hedef**: Oturum başına 2-3 hedef görüntü (önerilir)

### Adım 2: Hedef Sütununu Kontrol Edin

Kalibrasyon hedefi içeren her görüntü için:

1. Dosya Tarayıcı tablosunda görüntüyü bulun.
2. **Hedef** sütununu (en sağdaki sütun) bulun.
3. O görüntünün Hedef sütunundaki onay kutusunu tıklayın.
4. Hedef içeren tüm görüntüler için bu işlemi tekrarlayın.

### Adım 3: Seçiminizi Doğrulayın

İşlemeden önce şunları iki kez kontrol edin:

* [ ] Kalibrasyon hedefleri içeren tüm görüntüler işaretlenmiştir
* [ ] Hedef olmayan görüntüler yanlışlıkla işaretlenmemiştir
* [ ] Hedefler, işaretlenen görüntülerde açıkça görülebilir

***

## Hedef Görüntüler için En İyi Uygulamalar

### Hedef Yakalama Yönergeleri

**Zamanlama:**

* Hedef görüntüleri, yakalama oturumunuzdan hemen önce ve oturum boyunca yakalayın
* DAQ ışık sensörünüzle aynı aydınlatma koşullarında
* En iyi sonuçlar için ideal olarak hedef görüntüleri mümkün olduğunca sık yakalayın. Aksi takdirde, ışık sensörü verileri zaman içinde kalibrasyonu ayarlamak için kullanılacaktır.

**Kamera Konumu:**

* Kamerayı, merkezde olacak ve görüntü merkezinin yaklaşık %40-60&#x27;ını dolduracak şekilde hedefin üzerinde tutun.
* Kamerayı hedef yüzeye paralel/nadir tutun

**Aydınlatma:**

* DAQ ışık sensörünüzle aynı ortam aydınlatması
* Hedef yüzeylerde gölge oluşmasını önleyin
* Işık kaynağını vücudunuz, araç veya bitki örtüsü ile engellemeyin
* Bulutlu hava koşulları en tutarlı sonuçları sağlar

**Hedef Koşulu:**

* Hedef panelleri temiz ve kuru tutun
* 4 panelin tümü açıkça görülebilir ve engelsiz olmalıdır
* Mümkünse hedefler ışık kaynağına dik/nadir olmalıdır

### Kaç Hedef Görüntü?

**Minimum:** Her oturumda 1 hedef görüntü. **Önerilen:** Her oturumda 3-5 hedef görüntü.

**En iyi uygulama programı:**

* Işık sensörü kayıt yapmaya başladıktan kısa bir süre sonra 3-5 görüntü yakalayın.
* En iyi sonuçları elde etmek için çekimler arasında kamerayı döndürün.
* İsteğe bağlı: Aydınlatma koşulları sürekli değişiyorsa, oturumun ortasında periyodik olarak yapın.

***

## Birden Fazla Kamera ile Çalışma

### Çift Kamera Kurulumları

İki MAPIR kamerayı aynı anda kullanıyorsanız (ör. Survey3W RGN + Survey3N OCN):

1. **Her iki kamera** ile aynı anda hedef görüntüleri yakalayın
2. Her iki kamera için **aynı fiziksel hedef** kullanın
3. Dosya Tarayıcı&#x27;da **her iki kamera türü** için hedef görüntüleri işaretleyin
4. Chloros, her kameranın kalibrasyonu için uygun hedefleri kullanacaktır

### Kamera Modeli Sütunu

**Kamera Modeli** sütunu, hangi görüntünün hangi kameradan geldiğini belirlemeye yardımcı olur:

* Survey3W\_RGN
* Survey3N\_OCN
* Survey3W\_RGB
* vb.

Bu sütunu, projenizdeki her kamera türü için hedefleri işaretlediğinizi doğrulamak için kullanın.

***

## Hedef Algılama Ayarları

### Algılama Hassasiyetini Ayarlama

Chloros hedeflerinizi doğru algılamıyorsa, [Proje Ayarları](adjusting-project-settings.md) içindeki bu ayarları değiştirin:

**Minimum kalibrasyon örnek alanı:**

* **Varsayılan**: 25 piksel
* Küçük nesnelerde yanlış algılamalar varsa **artırın**.
* Hedefler algılanmıyorsa **azaltın**.

**Minimum hedef kümeleme:**

* **Varsayılan**: 60
* Hedefler birden fazla algılamaya bölünüyorsa **artırın**.
* Renk varyasyonu olan hedefler tam olarak algılanmıyorsa **azaltın**.

***

## Yaygın Hedef Görüntü Sorunları

### Sorun: Hedef Algılanmadı

**Olası nedenler:**

* Hedef görüntüler Dosya Tarayıcı&#x27;da işaretlenmemiş
* Hedef çerçevede çok küçük (&lt; görüntünün %30&#x27;u)
* Yetersiz aydınlatma (gölgeler, parlama)
* Hedef algılama ayarları çok katı

**Çözümler:**

1. Hedef sütununun doğru görüntüler için işaretli olduğunu doğrulayın
2. Önizlemede hedef görüntü kalitesini inceleyin
3. Kalite düşükse hedefleri yeniden yakalayın
4. Gerekirse hedef algılama ayarlarını değiştirin

### Sorun: Yanlış Hedef Algılamaları

**Olası nedenler:**

* Beyaz binalar, araçlar veya zemin kaplaması hedeflerle karıştırılıyor
* Bitki örtüsünde parlak lekeler var
* Algılama hassasiyeti çok düşük

**Çözümler:**

1. Algılama kapsamını sınırlamak için yalnızca gerçek hedef görüntüleri işaretleyin
2. Minimum kalibrasyon örnek alanını artırın
3. Minimum hedef kümeleme değerini artırın
4. Hedef görüntülerin yalnızca hedefi gösterdiğinden emin olun (minimum arka plan karmaşası)

***

## Doğrulama Kontrol Listesi

İşlemeye başlamadan önce hedef görüntü seçiminizi doğrulayın:

* [ ] Her oturumda en az 1 hedef görüntü işaretlendi
* [ ] Tüm hedef görüntüler için hedef sütunu onay kutuları işaretlendi
* [ ] Hedef görüntüler, anketle aynı zaman diliminde yakalandı
* [ ] Tıklandığında hedefler önizlemede açıkça görünüyor
* [ ] Her hedef görüntüde 4 kalibrasyon paneli de görünüyor
* [ ] Hedeflerde gölge veya engel yok
* [ ] Çift kamera için: Her iki kamera türü için de hedefler işaretlendi

***

## Hedefsiz İşleme

### Kalibrasyon Hedefleri Olmadan İşleme

Bilimsel çalışmalar için önerilmese de, hedefler olmadan işleme yapabilirsiniz:

1. Tüm Hedef sütunu onay kutularını işaretlemeyin
2. Proje Ayarlarında &quot;Yansıma kalibrasyonu&quot;nu **devre dışı bırakın**
3. Vinyet düzeltmesi yine de uygulanacaktır
4. Çıktı, mutlak yansıma için kalibre edilmeyecektir

{% hint style=&quot;warning&quot; %}
**Önerilmez**: Yansıma kalibrasyonu olmadan, piksel değerleri yalnızca göreceli parlaklığı temsil eder, bilimsel yansıma ölçümlerini temsil etmez. Doğru ve tekrarlanabilir sonuçlar için kalibrasyon hedeflerini kullanın.
{% endhint %}

***

## Sonraki Adımlar

Hedef görüntülerinizi işaretledikten sonra:

1. **Ayarlarınızı gözden geçirin** - Bkz. [Proje Ayarlarını Ayarlama](adjusting-project-settings.md)
2. **İşlemeyi başlatın** - [İşleme Başlama](starting-the-processing.md) bölümüne bakın.
3. **İlerlemeyi izleyin** - [İşlemeyi İzleme](monitoring-the-processing.md) bölümüne bakın.

Kalibrasyon hedefleri hakkında daha fazla bilgi için [Kalibrasyon Hedefleri](../calibration-targets.md) bölümüne bakın.
