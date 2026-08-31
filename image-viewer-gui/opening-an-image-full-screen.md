# Bir Görüntüyü Tam Ekran Olarak Açma

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>Sağ üst köşede katman seçici bulunan, tam ekran açılmış bir görüntü</p></figcaption></figure>

Chloros Görüntü Görüntüleyici, görüntülerinizi görüntülemek, incelemek ve ölçmek için kullanılan tam ekran arayüzüdür. Bu arayüzde, ekranın gösterdiği uzatılmış önizleme yerine **gerçek piksel değerlerini** — kanal başına DN, yansıma yüzdesi veya W/m²/sr/nm cinsinden parlaklık — okuyabileceğiniz yerdir; ekranın gösterdiği uzatılmış önizleme yerine.

## Görüntü Görüntüleyicisine Erişme

### Dosya Tarayıcısından

1. **Dosya Tarayıcı** sekmesin<img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> açın
2. [Görüntü ızgarası](image-grid.md) içindeki herhangi bir **küçük resme** tıklayın
3. Görüntü, **Görüntü Görüntüleyici** sekmesinde tam ekran olarak açılır

Görüntü, ızgaranın gösterdiği üründe açılır. Izgara `RAW (Reflectance)` olarak ayarlanmışsa, o katmana yönlendirilirsiniz.

### Görüntü Görüntüleyicisi kenar çubuğunu açma

Sol kenar çubuğundaki **Görüntü Görüntüleyicisi** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> simgesine tıklayarak analiz panelini açın. Panel, yukarıdan aşağıya doğru şunları içerir:

* görüntü adı ve kamera modeli
* **Görüntüleri Dışa Aktar/Kaydet** düğmesi (yalnızca bir Dizin veya LUT etkin olduğunda)
* **İndeks**ve**LUT** onay kutuları ile indeks yapılandırma paneli — bkz. [İndeks/LUT Sandbox](index-lut-sandbox.md)
* **İmleç Değerleri** paneli: kanal başına okuma, katman histogramı ve GSD kontrolü***

## Gezinme ve yakınlaştırma

### Görüntüler arasında gezinme

* **Sonraki görüntü**: → düğmesi veya**→** (Sağ Ok) tuşu
* **Önceki görüntü**: ← düğmesi veya**←** (Sol Ok) tuşu
* **Belirli bir görüntüye atlama**: ızgaraya geri dönün ve ilgili küçük resme tıklayın

Görüntüler arasında geçiş yaparken yakınlaştırma ve kaydırma ayarları korunur; böylece karenin aynı bölümünde kalarak bir dizi görüntüyü tek tek inceleyebilirsiniz.

### Yakınlaştırma

Yakınlaştırma, **fare tekerleği** ile 15&#x27;lik adımlarla gerçekleştirilir ve imleç merkezli çalışır — imlecin altındaki nokta, imlecin altında kalır. Aralık, görüntü ve pencere boyutuyla sınırlıdır: pencereye sığdırma durumunun ötesine kadar uzaklaştırma yapamazsınız ve üst sınır, görüntünün doğal çözünürlüğüyle belirlenir.

Tam ekran görüntüleyicide özel yakınlaştırma tuşları yoktur. (Izgarada, **Ctrl + `+` / `−`** tuşları küçük resimleri yeniden boyutlandırır — bu farklı bir işlevdir.)

### Yakınlaştırıldığında kaydırma

Görüntünün üzerinde sol fare düğmesini tıklayıp basılı tutun ve sürükleyin. Kaydırma sınırlıdır, bu nedenle görüntü ekranın dışına sürüklenemez.

### Yüksek yakınlaştırmada piksel bazında inceleme

Etkili büyütme oranı **60×**&#x27;i aştığında, Chloros, imlecin altındaki görüntülenen tek tek piksellerin etrafına bir vurgu kutusu çizer ve yanına bir değer gösterir.

&quot;Etkin&quot; büyütme, GSD blok boyutunu dikkate alır: blok boyutu 8 olduğunda, vurgulama 60× yerine 7,5× yakınlaştırmada görünür; çünkü görüntülenen bir piksel zaten 8 × 8 kaynak piksele karşılık gelir. Eşik değerinin altına geri yakınlaştırdığınızda vurgu kaybolur.

### Klavye kısayolları

| Tuş                             | Nerede       | Eylem                              |
| ------------------------------- | ----------- | ----------------------------------- |
| **→**                           | Tam ekran | Sonraki görüntü                          |
| **←**                           | Tam ekran | Önceki görüntü                      |
| **Ctrl + R**                    | Tam ekran | Dizin/LUT sanal alanını sıfırla         |
| **Ctrl + `+`**/**Ctrl + `=`** | Izgara        | Daha büyük küçük resimler (her basışta 4 piksel)  |
| **Ctrl + `−`**                  | Izgara        | Daha küçük küçük resimler (her basışta 4 px) |***

## İmleç Değerleri

İmleci görüntünün üzerine getirdiğinizde, **İmleç Değerleri** paneli altındaki her kanalın değerini gösterir.

{% hint style="success" %}
**Bunlar dosyanın gerçek sayılarıdır.** Ekrandaki tuval, 8 bitlik uzatılmış bir önizlemedir ve bu değerleri sağlayamaz; bu nedenle Chloros, okuma işlemi için gerçek ürün dosyasından örnek alır. Bu nedenle 12 bitlik ham kare 255&#x27;in üzerindeki değerleri gösterir ve float32 radiance katmanı fiziksel birimleri gösterir.
{% endhint %}

### Sütunların Anlamı

Panel, görüntülediğiniz katmana göre uyum sağlar:

| Görüntülediğiniz katman              | Görüntülenen sütunlar    | Notlar                                                                                           |
| ---------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------- |
| Yansıma                        | **DN**ve**%** | Yüzde, dosyanın kendi ölçeği kullanılarak hesaplanır — aşağıya bakın                                      |
| Parlaklık                           | **W/m²/sr/nm**   | Kayan noktalı fiziksel değerler; burada DN&#x27;nin bir anlamı olmadığı için DN sütunu yoktur                           |
| Ham / Debayered / önizleme / JPG    | **DN**           | Tamsayı dijital değerler                                                                         |
| 32-bit yüzde yansıtma dışa aktarımları | Yalnızca **%**       | Depolanan kayan noktalı sayı bir DN değildir; bu nedenle tamsayıya yuvarlanması, anlamsız bir `0` veya `1` çıktısı verecektir |

Her satır, kameranızın filtresinin kanal adıyla etiketlenmiştir — RGN için `Red / Green / NIR`, OCN için `Orange / Cyan / NIR`, NGB için `NIR / Green / Blue`, OCN için `Red / Green / Blue` RGB için ve RE, NIR ile mono M3M kameralar için tek bant adı. Her etiket, dizin formül düzenleyicisinde kullanılan kanal daireleriyle eşleşen renkli bir nokta taşır.

Kaydedilmiş **dizin ve LUT** görüntüleri özel bir durumdur: spektral bantlar yerine renk haritası bileşenlerini içerirler, bu nedenle satırları kameranın filtre adlarıyla değil, `Red / Green / Blue` (veya tek kanallı bir dizin dosyası için `Index`) şeklinde etiketlenir.

Sandbox&#x27;ta bir indeks etkin olduğunda, kanalların altında imlecin bulunduğu yerdeki **indeks değerini** gösteren ek bir satır belirir; bu satırda indeksin adı ve histogramdaki işaretçisiyle eşleşen beyaz bir nokta bulunur.

### Yansıma yüzdesi, her dosyanın kendi ölçeğini kullanır

{% hint style="warning" %}
**65535 = %100 olduğunu varsaymayın.** Chloros, yansıma değerini hangi kameranın ürettiğine bağlı olarak farklı ölçeklerde depolar ve görüntüleyici, her dosya için doğru olanı belirler.
{% endhint %}

| Kaynak                  | Yansıma oranı 1,0&#x27;a eşit DN | Nasıl tanımlanır                                                                                                                               |
| ----------------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LATTICE**(M3C / M3M) |**32768**                      | Her LATTICE yansıma dışa aktarımına yazılan XMP etiketi `Chloros:PixelScale=32768`. 2×&#x27;lik başlık payı, dosyanın kırpılma olmadan 1,0&#x27;ın üzerindeki ρ değerlerini taşımasına olanak tanır |
| **Survey3**|**65535**                      | Chloros XMP ölçek etiketi yoktur — Survey3 kalibrasyonu, ρ × dtype-max değerini yazar ve 1,0&#x27;da kırpma yapar                                                               |

Görüntüleyici, dizin/LUT test ortamı ve dizin dışa aktarımı, ölçeği aynı tek bir uygulama aracılığıyla çözer; bu nedenle imleçte okuduğunuz değer, dizin hesaplamalarında kullanılan değerle aynıdır.

Bilmeniz gereken iki sonuç:

* **32 bitlik yüzde**TIFF, DN/65535&#x27;i float olarak depolar;**8 bitlik** PNG/JPG dışa aktarımı ise DN × 255/65535&#x27;i depolar olarak depolar; görüntüleyici, yüzde değerini yazdırmadan önce her ikisini de geri dönüştürür.
* Kurtarılamayan bir durum vardır: **8-bit kaynaklı bir yakalamanın 8-bit TIFF dışa aktarımı**, yeniden ölçeklendirilmek yerine 0–255 aralığına kırpılır ve kasıtlı olarak ölçek etiketi taşımaz. Bu dosyalar için panel, yüzde sütunu olmadan yalnızca DN değerini yazdırır. Bu, bir hata değil, gerçek durumdur.***

## Katman histogramı

İmleç satırlarının altında, görüntülediğiniz katmanın **256 bölmeli**canlı bir histogramı bulunur. Varsayılan olarak, LATTICE kamera histogramlarının kullandığı ölçüm alanıyla aynı olan, ağırlıklı tek bir birleşik eğri çizilir — `(R + 2G + B) / 4`.**RGB** seçeneği etkinleştirildiğinde, bu eğri, kanal renklerinde kanal başına eğrilerle değiştirilir; üst üste binmeler okunabilir kalacak şekilde bu eğriler eklemeli olarak harmanlanır. Mono katmanlar her zaman tek bir eğri çizer.

Yatay eksen, katmanın kendi birimindedir:

| Katman       | Eksen birimi  | Eksen maksimum değeri                                               |
| ----------- | ---------- | ---------------------------------------------------------- |
| Yansıtma | yüzde    | %125 — ürünün başlık aralığı, ρ değerinin 1,0&#x27;ın üzerine çıkmasına izin verir           |
| Parlaklık    | W/m²/sr/nm | Karenin kendi tepe değeri, iki anlamlı basamağa yuvarlanmış |
| 8 bit veri  | DN         | 255                                                        |
| 12 bit veri | DN         | 4095                                                       |
| 16 bit veri | DN         | 65535                                                      |

Eksen DN modundayken ve bu üç üst sınırdan birine denk geldiğinde, Chloros, baktığınız görüntünün bit derinliğini de bilir.

Histogramın üzerinde üç düğme bulunur:

| Düğme     | Varsayılan | Etki                                                                                                                                                                                                                                                                                                           |
| ---------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **İMLEÇ** | Açık      | Histogram üzerinde, yukarıdaki satırlarda gösterilen tam değerlere göre işaret çizgileri çizer; böylece imlecinizin altındaki pikselin karenin dağılımında nerede yer aldığını görebilirsiniz. RGB modunda, her kanal için kendi rengine sahip bir işaret bulunur; diğer durumlarda ise birleştirilmiş değer üzerinde tek bir beyaz işaret bulunur |
| **INDEX**| Açık      | Yalnızca bir indeks etkinken görünür. Histogramı kaynak bantlardan**indeks değeri dağılımına** geçirir; iki kırpma eşiği turuncu kesikli çizgilerle, imlecin indeks değeri ise beyaz bir çizgiyle gösterilir                                                          |
| **RGB**| Kapalı     | Birleştirilmiş eğriden kanal başına eğrilere geçer. Mono sensörde bu düğme**MONO** olarak görünür ve devre dışıdır — gösterilecek tek bir kanal vardır                                                                                                                                  |

Histogram, arkasındaki kaynak pikseller üzerinden değil, **gördüğünüz bloklar** üzerinden hesaplanır: GSD blok boyutunu değiştirdiğinizde dağılım yeniden hesaplanır; böylece histogram, imleç işareti ve görüntülenen resim her zaman birbiriyle uyumlu olur.***

## GSD blok boyutu

Panelin altında **GSD (px)**kontrolü bulunur: bir sayı kutusu,**1 ile 256**arasında değişen bir kaydırıcı ve bir**RESET** düğmesi.

Bu kontrol, N × N boyutundaki bir kaynak piksel bloğunun ortalamasını alarak _görüntülenen_ görüntüyü bir pikselde birleştirir. `1`, doğal çözünürlüktür.

* Bu ayar, **tam ekran görünümünü, ızgara küçük resimlerini, imleç gösterimini ve her iki histogramı** etkiler — görüntüyü gösteren her şey aynı temel çözünürlükte uyumlu hale gelir.
* Bu ayar **sadece görüntüleme** ile ilgilidir. İşleme ve dışa aktarma işlemleri etkilenmez. Tek istisna kasıtlı olarak yapılmıştır: bir [Dizin/LUT Sandbox](index-lut-sandbox.md) dışa aktarımı, baktığınız görüntüyü kaydeder; bu nedenle mevcut blok boyutunu taşır ve blok boyutu 1&#x27;in üzerine çıktığında dışa aktarma paneli sizi uyarır.
* Değer, `viewer_display.gsd_bin` dosyasında **proje başına** `project.json` olarak saklanır; bu sayede programın kapatılıp yeniden açılması durumunda da korunur.
* Blok boyutu 1&#x27;in üzerinde olduğunda imleç göstergesi kaynak pikseli değil, bloğu bildirir — gösterilen değer, imlecinizin altındaki bloğun ortalamasıdır.

{% hint style="info" %}
**Neden &quot;blok boyutu&quot; da piksel başına santimetre değil?** cm/px değeri için yerden yükseklik bilgisi gereklidir. Tek bir karenin EXIF verileri, kameranın yöneldiği arazinin değil, ortalama deniz seviyesinin üzerindeki GPS rakımını içerir; bu nedenle Chloros, doğru bir şekilde hesaplayamadığı yer mesafesini yazdırmaz. Kaynak pikseller cinsinden blok boyutu, yer örnekleme mesafesi bilinmediğinde MAPIR bulut araçlarının kullandığı yedek yöntemdir.
{% endhint %}

***

## Görüntüleyebileceğiniz görüntü türleri

Görüntüleyicinin sağ üst köşesindeki katman açılır menüsü, mevcut görüntünün tüm sürümlerini listeler. Hangi girişlerin görüneceği, kameraya ve işlenen verilere bağlıdır — tam liste ve açılır menünün nasıl çalıştığı hakkında bilgi için [Görüntü Katmanları](image-layers.md) bölümüne bakın.

### Survey3

* **JPG** — kameranın kendi önizleme dosyası
* **RAW (Orijinal)** — kaynak `.RAW` dosyası; görüntüleme için debayering işlemi uygulanmış, düzeltme yapılmamış
* **RAW (Hedef)** — kalibrasyon hedefi içerdiği belirlenen bir kare
* **RAW (Yansıtma)** — kalibre edilmiş yansıtma ürünü (65535 = ρ 1,0)
* **Vinyet Düzeltmeli**/**Sensör Tepkisi** — kalibre edilmemiş yedek ürün
* **Beyaz Dengeli** — beyaz dengesi ayarlanmış ürün
* **RAW (`<INDEX>` İndeksi)**ve**`<INDEX>` LUT** — hesaplanmış indeks görüntüleri

### LATTICE

LATTICE çekimleri, iş akışının seviye adlarını içeren aynı açılır menüyü kullanır:

| Katman                 | İçeriği                                                        |
| --------------------- | -------------------------------------------------------------------- |
| **RAW (Orijinal)**    | Yakalanan kaynak ham kare                                     |
| **RAW (Debayered)**   | Doğrusal debayered görüntü                                           |
| **RAW (Önizleme)**     | Ekran önizlemesi — multispektral kameralar için sahte renk uzantısı |
| **Beyaz Dengeli**    | RGB ana kameralar için ekran önizlemesi (beyaz dengesi + gama)   |
| **RAW (Parlaklık)**    | W/m²/sr/nm cinsinden Float32 spektral parlaklık                              |
| **RAW (Yansıtma)** | uint16 yansıtma, 32768 = ρ 1,0                                    |

Işınım ve yansıma değerleri yalnızca multispektral görüntüler içindir: RGB ana kamerada bant başına radyometri bulunmadığından, bu katmanlar bu kamera için oluşturulmaz.

***

## İndeks ve LUT uygulaması

Yan çubuktan multispektral indeksleri ve renk Arama Tablolarını (LUT) uygulayın:

1. **Görüntü Görüntüleyici**&#x27;n<img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> yan çubuğunu açın
2. **İndeks** seçeneğini işaretleyin
3. Kameranızın filtresini ve bir indeks formülünü seçin, ardından kanal dairelerini formülün yuvalarına sürükleyin
4. Bir LUT ekleyin ve bir gradyan, eşik değerleri ve kırpma modu seçin
5. İmlecin üzerindeki değerleri okuyun ve sonucu **Görüntüleri Dışa Aktar/Kaydet** seçeneğiyle kaydedin

Tam adım adım kılavuz için [İndeks/LUT Sandbox](index-lut-sandbox.md) sayfasına bakın.

***

## Sorun Giderme

### Görüntü açılmıyor

**Olası nedenler**: dosya içe aktarıldıktan sonra taşınmış veya silinmiş; ürün hiç yazılmamış; çok büyük bir görüntü için yeterli bellek yok.**Ne yapılmalı**:

1. Katmanın dosyasının projenin çıktı ağacında hâlâ mevcut olup olmadığını kontrol edin
2. Dosyayı harici bir görüntüleyicide açarak dosyanın bozulmamış olduğunu doğrulayın
3. Bellek boşaltmak için diğer uygulamaları kapatın

### Görüntü siyah, beyaz veya aşırı renkli

**Olası nedenler**: Ekran uzantısının işleyebileceği bir veri yok (neredeyse sabit bir kare); olağandışı değerlere sahip bir float32 katmanı; geçerli veri üretmeyen bir indeks.**Ne yapmalı**:

1. İmleç değerlerini okuyun — her kanal sıfırda veya sıfıra yakınsa, sorun verilerde, görüntüde değil
2. Histogramı kontrol edin: bir uçta tek bir ani artış, karenin kırpılmış veya boş olduğunu gösterir
3. Katmanı oluşturan çalıştırma için işleme günlüğünü kontrol edin

### Değerler yanlış görünüyor

**Olası nedenler**: düşündüğünüzden farklı bir katmanda bulunuyorsunuz; bir yüzde değerini ham DN ile karşılaştırıyorsunuz; bir LATTICE dosyasını, aynı bölücü kullanarak bir Survey3 dosyasıyla karşılaştırıyorsunuz.**Ne yapmalı**:

1. Açılır menüden seçilen katmanı doğrulayın — panelin birimleri katmana göre belirlenir
2. Yansıtma değeri için, DN değerini kendiniz bölmek yerine **%** sütununu kullanın; bölmeniz gerekiyorsa, o dosyanın `Chloros:PixelScale` değerini kullanın (LATTICE için 32768; yoksa Survey3 için 65535 anlamına gelir)
3. GSD blok boyutunu tekrar 1&#x27;e ayarlayın — 1&#x27;den büyük değerlerde piksel değil, blok ortalamasını okursunuz
4. O kare için yansıma kalibrasyonunun gerçekten çalıştırıldığını kontrol edin; kalibre edilmemiş yedek ürün (Sensör Tepkisi / Vinyet Düzeltmeli) yansıma değildir

***

## Sonraki Adımlar

* [**Görüntü Katmanları**](image-layers.md) — mevcut olan her katman adı ve değerlerinin anlamı
* [**İndeks/LUT Sandbox**](index-lut-sandbox.md) — indeks görselleştirmelerini oluşturun, ayarlayın ve dışa aktarın
* [**Harita İşaretçileri**](map-markers.md) — aynı görüntü kümesinin harita üzerinde gösterimi
* [**Çok Spektral Endeks Formülleri**](../project-settings/multispectral-index-formulas.md) — endeks referansı

İşleme iş akışı için bkz. [Görüntüleri İşleme (GUI)](../processing-images-gui/adding-files-to-a-project.md).
