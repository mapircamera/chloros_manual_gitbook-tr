# Hedef Görüntülerin Seçilmesi

Hangi görüntülerde kalibrasyon hedefleri bulunduğunu işaretlemek, Chloros sitesine bunların tam olarak nerede aranması gerektiğini bildirir. “Hedef” sütununda en az bir görüntü işaretlendiğinde, Chloros **sadece işaretli görüntüleri** tarar — bu nedenle hedefleri işaretlemek, hem işleme sürecini hızlandırmanın hem de anket görüntülerinin yanlışlıkla hedef olarak algılanmasını önlemenin yoludur.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Neden Hedef Görüntüleri İşaretlemeli?

### İşaretleme, Taramayı Kontrol Eder

Hedef sütununda belirli görüntüleri işaretlediğinizde:

* Chloros, hedefler için yalnızca işaretli görüntüleri tarar
* Hedef algılama çok daha hızlı tamamlanır
* Harita görüntüleri, yanlış hedef algılamalarına yol açmaz

**Hiçbir** görüntü işaretlenmezse, Chloros projedeki her görüntüyü taramaya geri döner:

* Hedef algılama algoritmaları her görüntü üzerinde çalıştırılır
* Yüzlerce veya binlerce görüntü gereksiz yere kontrol edilir
* İşleme süresi, özellikle büyük veri kümeleri için önemli ölçüde uzar

{% hint style="success" %}
**Hız Artışı**: 500 görüntülük bir veri kümesinde 2-3 hedef görüntüyü işaretlemek, hedef algılama süresini 30 dakikadan fazla süren bir işlemden 1 dakikanın altına indirebilir.
{% endhint %}

***

## Hedef Görüntüleri Nasıl İşaretlersiniz?

### Adım 1: Hedef Görüntülerinizi Belirleyin

Dosya Tarayıcısı&#x27;nda içe aktardığınız görüntüleri inceleyin ve hangi görüntülerde kalibrasyon hedefleri bulunduğunu belirleyin.

**Yaygın senaryolar:*** **Çekim öncesi hedef**: Oturum başlamadan önce çekilen
* **Çekim sonrası hedef**: Oturum tamamlandıktan sonra çekilen
* **Sahada bulunan hedefler**: Çekim alanı içine yerleştirilen hedefler
* **Birden fazla hedef**: Oturum başına 2-3 hedef görüntü (önerilir)

### Adım 2: <img src="../.gitbook/assets/image (33).png" alt="" data-size="original">&#x27;de Hedef Sütununu Kontrol Edin

Kalibrasyon hedefi içeren her görüntü için:

1. Dosya Tarayıcısı tablosunda görüntüyü bulun
2. **Hedef** sütununu (en sağdaki sütun) bulun
3. O görüntünün Hedef sütunundaki onay kutusunu tıklayın
4. Hedef içeren tüm görüntüler için bu işlemi tekrarlayın

### Adım 3: Seçiminizi Doğrulayın

İşleme başlamadan önce aşağıdakileri iki kez kontrol edin:

* [ ] Kalibrasyon hedefleri içeren tüm görüntüler işaretlenmiş mi?
* [ ] Hedef olmayan hiçbir görüntü yanlışlıkla işaretlenmemiş mi?
* [ ] İşaretlenen görüntülerde hedefler net bir şekilde görünüyor mu?

***

## LATTICE: DAQ Kayıt Yaparken Hedefler İsteğe Bağlıdır

LATTICE multispektral kameralar için, kare içi kalibrasyon hedefi **iki**olası yansıma referansından**biridir**:

* **Çerçeve içi hedef**: İşaretlenmiş bir hedef görüntü, Chloros adresindeki kalite (QA) kontrollerinden geçtiğinde, hedef, çevresindeki görüntüler için**mutlak yansıma referansı** haline gelir.
* **DAQ aşağı doğru ışınımı**: Hedef bulunmadığında (veya QA başarısız olduğunda), Chloros bunun yerine DAQ ışık sensörünün aşağı doğru ışınım yoğunluğundan yansıma oranını hesaplar (ρ = π·L/E). `.daq` veya DAQ-M `.csv` kaydı, yakaladığınız görüntüleri kapsıyorsa,**hiçbir hedef görüntü olmadan** kalibre edilmiş yansıma değeri elde edersiniz.

Bu otomatik davranış varsayılan ayardır. CLI / SDK dosyasında bu, `--reflectance-source auto`&#x27;e karşılık gelir; ayrıca `target` (katı — DAQ ikamesi yok) veya `daq` (DAQ öncelikli) seçeneklerini de zorlayabilirsiniz. [CLI Referansı](../reference/cli-reference.md#per-product-export-toggles-lattice-multispectral) bölümüne bakınız.

**LATTICE hedef geometrileri**: Survey3 için kullanılan klasik panel algılamanın yanı sıra, LATTICE işleme, projeye göre yapılandırılan**ArUco işaretli hedefleri**,**sabit ROI hedefleri**ve**şerit hedefleri**destekler. Birim başına**ölçülen** hedef yansıma taramaları, seri numarasıyla (CLI: `--target-reflectance-dir`, her hedef birimi için bir adet `<serial>.csv`) sağlanabilir; yedek olarak nominal T3/T4P spektrumları kullanılır.

{% hint style="info" %}
**F988 modülü**: F988 yansıma değeri, sahadaki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros en son panel yakalama verinizi uygular ve panel gözlemleri arasında bu değeri korur. Bir F988 modülü yalnızca DAQ ile işlenirse, Chloros o bant için DAQ tabanlı yansıma değerini reddeder (atlama nedeni `dls-uncalibrated-band-988`) — panel iş akışı desteklenen yoldur.
{% endhint %}

***

## Hedef Görüntüler için En İyi Uygulamalar

### Hedef Yakalama Yönergeleri

**Zamanlama:**

* Yakalama oturumunuzdan hemen önce ve oturum boyunca hedef görüntüleri yakalayın
* DAQ ışık sensörünüzle aynı aydınlatma koşullarında
* En iyi sonuçlar için ideal olarak hedef görüntülerini mümkün olduğunca sık çekin. Aksi takdirde, zaman içinde kalibrasyonu ayarlamak için ışık sensörü verileri kullanılacaktır.

**Kamera Konumu:**

* Kamerayı, hedefin ortalanmış ve görüntü merkezinin yaklaşık %40-60&#x27;ını kaplayacak şekilde tutun.
* Kamerayı hedef yüzeye paralel/nadir konumda tutun

**Aydınlatma:**

* DAQ ışık sensörünüzle aynı ortam aydınlatması
* Hedef yüzeylerde gölge oluşmasını önleyin
* Işık kaynağını vücudunuz, aracınız veya bitki örtüsüyle engellemeyin
* Bulutlu hava koşulları en tutarlı sonuçları sağlar

**Hedef Durumu:**

* Hedef panellerini temiz ve kuru tutun.
* Hedefinizin tüm panelleri (ör. bir T4&#x27;teki 4 panelin tamamı) net bir şekilde görünür ve engelsiz olmalıdır.
* Mümkünse hedefleri ışık kaynağına dik/nadir konumda tutun.

### Kaç Adet Hedef Görüntüsü?

**Minimum:**Oturum başına 1 hedef görüntüsü.**Önerilen:** Oturum başına 3-5 hedef görüntüsü.**En iyi uygulama programı:**

* Işık sensörü kayda başladıktan kısa bir süre sonra 3-5 görüntü çekin
* En iyi sonuçlar için çekimler arasında kamerayı döndürün
* İsteğe bağlı: Işık koşulları sürekli değişiyorsa, oturum ortasında periyodik olarak çekim yapın

***

## Birden Fazla Kamera ile Çalışma

### Çift Kamera Kurulumları

İki MAPIR kamerasını aynı anda kullanıyorsanız (örn., Survey3W RGN + Survey3N OCN):

1. Hedef görüntüleri **her iki kamerayla** aynı anda çekin
2. Her iki kamera için de **aynı fiziksel hedefi** kullanın
3. Dosya Tarayıcı&#x27;da **her iki kamera türü** için hedef görüntüleri işaretleyin
4. Chloros, her kameranın kalibrasyonu için uygun hedefleri kullanacaktır

### Kamera Modeli Sütunu

**Kamera Modeli** sütunu, hangi görüntülerin hangi kameradan geldiğini belirlemenize yardımcı olur:

* Survey3W\_RGN
* Survey3N\_OCN
* LATT-M3M-L41-F550
* LATT-M3C-L87-FRGN
* vb.

Projenizdeki her kamera türü için hedefleri işaretlediğinizi doğrulamak üzere bu sütunu kullanın.

***

## Hedef Algılama Ayarları

### Algılama Hassasiyetini Ayarlama

Chloros, hedeflerinizi doğru şekilde algılamıyorsa, [Proje Ayarları](adjusting-project-settings.md) bölümünden şu ayarları değiştirin:**Minimum kalibrasyon örnek alanı (px):*** **Varsayılan**: 25 piksel
* Küçük nesnelerde yanlış algılamalar alıyorsanız **artırın*** Hedefler algılanmıyorsa **azaltın**

**Minimum Hedef Kümelenmesi (0-100):*** **Varsayılan**: 60
* Hedefler birden fazla algılamaya bölünüyor ise **artırın*** Renk farklılığı olan hedefler tam olarak algılanmıyorsa **azaltın**{% hint style="info" %}**CLI ipucu**: `chloros-cli process` aynı ayarları (`--min-target-size`, `--target-clustering`) kabul eder ve `--target`/`--targets` bayrağı, tüm bir giriş klasörünü &quot;sadece hedef paneli&quot; olarak işaretler. [CLI Referansı](../reference/cli-reference.md) bölümüne bakın.
{% endhint %}

***

## Yaygın Hedef Görüntü Sorunları

### Sorun: Hedef Algılanmadı

**Olası nedenler:**

* Hedef görüntüler Dosya Tarayıcı&#x27;da işaretlenmemiş
* Çerçeve içindeki hedef çok küçük (&lt; görüntünün %30&#x27;u)
* Yetersiz aydınlatma (gölgeler, parlama)
* Hedef algılama ayarları çok katı

**Çözümler:**

1. Doğru görüntüler için Hedef sütununun işaretli olduğunu doğrulayın
2. Önizlemede hedef görüntünün kalitesini kontrol edin
3. Kalite düşükse hedefleri yeniden yakalayın
4. Gerekirse hedef algılama ayarlarını düzenleyin

### Sorun: Yanlış Hedef Algılamaları

**Olası nedenler:**

* Beyaz binalar, araçlar veya zemin kaplamasının hedeflerle karıştırılması
* Bitki örtüsündeki parlak bölgeler
* Algılama hassasiyetinin çok düşük olması

**Çözümler:**

1. Yalnızca gerçek hedef görüntülerini işaretleyin — yalnızca işaretlenen görüntüler taranır
2. Minimum kalibrasyon örnekleme alanını artırın
3. Minimum hedef kümelenme değerini artırın
4. Hedef görüntülerinde yalnızca hedefin göründüğünden emin olun (arka planda en az karışıklık)

***

## Doğrulama Kontrol Listesi

İşlemeye başlamadan önce hedef görüntü seçiminizi doğrulayın:

* [ ] Her oturumda en az 1 hedef görüntü işaretlenmiş (veya LATTICE için, oturumu kapsayan bir `.daq`/`.csv` kaydı)
* [ ] Tüm hedef görüntüler için hedef sütunundaki onay kutuları işaretlenmiş
* [ ] Hedef görüntüler, anketle aynı zaman aralığında yakalanmış
* [ ] Tıklandığında önizlemede hedefler net bir şekilde görünüyor
* [ ] Her hedef görüntüde tüm kalibrasyon panelleri görünüyor
* [ ] Hedeflerin üzerinde gölge veya engel yok
* [ ] Çift kamera için: Hedefler her iki kamera türü için de işaretlenmiştir

***

## Hedefsiz İşleme

### LATTICE: DAQ Kaydı ile

LATTICE çekimleriniz sırasında bir DAQ ışık sensörü aşağıya doğru ışık şiddetini kaydetmişse, hedefe gerek yoktur:

1. Görüntüleri içeren `.daq` (veya DAQ-M `.csv`) dosyasını içe aktarın
2. Hedef sütununu işaretlemeyin
3. Yansıtma, DAQ aşağı yönlü referansından otomatik olarak hesaplanır
4. Işınım değeri için hiçbir zaman hedef veya DAQ gerekmez — bu değer yalnızca kameranın fabrika radyometrik kalibrasyonundan elde edilir

### Herhangi Bir Referans Olmadan İşleme

Hedefler ve DAQ olmadan da işleme yapabilirsiniz:

1. Hedef sütunundaki tüm onay kutularını işaretlemeyin
2. Proje Ayarları&#x27;nda &quot;Yansıtma kalibrasyonu / beyaz dengesi&quot; seçeneğini **devre dışı bırakın** — bu durumda hedef algılama işlemi tamamen atlanır
3. Vinyet düzeltmesi yine de uygulanacaktır
4. Çıktı, mutlak yansıtma değeri açısından kalibre edilmeyecektir (LATTICE multispektral, yine de debayering uygulanmış, önizleme ve radyans ürünlerini dışa aktarır)

{% hint style="warning" %}
**Survey3&#x27;in bilimsel çalışmaları için önerilmez**: Yansıma kalibrasyonu olmadan, Survey3 piksel değerleri yalnızca göreceli parlaklığı temsil eder, bilimsel yansıma ölçümlerini değil. Doğru ve tekrarlanabilir sonuçlar için kalibrasyon hedeflerini (veya LATTICE için bir DAQ ışık sensörünü) kullanın.
{% endhint %}

***

## Sonraki Adımlar

Hedef görüntülerinizi işaretledikten sonra:

1. **Ayarlarınızı gözden geçirin** - Bkz. [Proje Ayarlarını Ayarlama](adjusting-project-settings.md)
2. **İşlemeye başlayın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
3. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)

Kalibrasyon hedefleri hakkında daha fazla bilgi için bkz. [Kalibrasyon Hedefleri](../calibration-targets.md).
