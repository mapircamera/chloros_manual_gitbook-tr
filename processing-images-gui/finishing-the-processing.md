# İşlemin Tamamlanması

Chloros
işlemeyi tamamladığında, sonuçlarınızı gözden geçirme, çıktı kalitesini doğrulama ve iş akışınızda kullanmak üzere işlenmiş görüntülerinizi hazırlama zamanı gelmiştir. Bu sayfa, son adımlar ve sonraki işlemler konusunda size rehberlik eder.

## İşlemin Tamamlandığına Dair Göstergeler

İşleme başarıyla tamamlandığında, birkaç gösterge göreceksiniz:

* ✅ **İlerleme çubuğu**: %100&#x27;e ulaşır
* ✅ **Hata Giderme Günlüğü**: Son `[RUN-SUMMARY]` satırlarını sayılarla birlikte gösterir (görüntüler, kamera grupları, hedefler, kalibre edilmiş görüntüler, yazılan dosyalar)
* ✅ **Başlat düğmesi**: Tekrar etkin hale gelir (bir sonraki işleme çalışması için hazırdır)
* ✅ **Çıktı dosyaları**: İşlenen tüm görüntüler projenin çıktı dizinine kaydedilir (aşağıda)

{% hint style="warning" %}
**Görüntü yazmayan bir işleme, başarısızlık olarak değerlendirilir.** Görüntü ürünleri talep ettiyseniz ve işleme hiçbir görüntü yazmadıysa, Chloros
bunu bir hata olarak bildirir — `[RUN-SUMMARY]`, günlük adındaki ipuçlarıyla olası nedeni belirtir (hiçbir şey içe aktarılmadı, hedef algılanmadı veya talep edilen tüm ürünler uygulanamaz olduğu için atlandı). CLI
&#x27;deki karşılığı sıfırdan farklı bir değerle sonlanır. Kasıtlı olarak yalnızca meta veriye yönelik bir çalıştırma (tüm dışa aktarma ürünleri kapalı, dizin yok) yine de başarılı sayılır. Bkz. [CLI
Referansı](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## İşlenmiş Görüntülerinizi Bulma

### Çıktı Klasörünü Açma

1. **Ana Menü** <img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">
simgesine (sol üst) tıklayın
2. **&quot;Proje Klasörünü Aç&quot;** seçeneğini seçin
3. Dosya gezgininiz proje dizininde açılır
4. Adına göre projenizi bulun

### Çıktı Ağacı

Ürünler, **proje klasörünün altında, önce kameraya göre, ardından dosya formatına göre gruplandırılmış olarak** kaydedilir:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per selected index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

* **Kamera klasörü**: LATTICE için `LATT-<sensor>-<lens>-F<filter>` (çekimin EXIF verisiyle eşleşir: `Model`), Survey3
için `<model>_<filter>` (örn. `Survey3N_RGN`). Aynı sensörü ve filtreyi paylaşan ancak lensleri farklı olan iki kamera ayrı dizinler oluşturur — vinyet, görüş alanı ve distorsiyon farklılık gösterir.
* **Biçim klasörü**: dışa aktarma biçimi ayarınıza göre belirlenir — `tiff16`, `tiff8`, `png8`, `jpg8`, veya TIFF
(32 bit, Yüzde) için `tiff32`. Parlaklık her zaman float32 türündedir ve her zaman `tiff32` altında yer alır.
* **Ürün klasörleri**:
  * `Reflectance_Calibrated_Images/` — kalibre edilmiş yansıma
  * `Debayered_Images/` — doğrusal debayering uygulanmış (LATTICE): karanlık ofseti giderilmiş ve demosaicing yapılmış, düz alan düzeltmesi veya renk ayrıştırma yapılmamış, sensörün doğal yöneliminde
  * `Preview_Images/` — ekran önizlemesi (LATTICE)
  * `Radiance_Images/` — float32 spektral parlaklık, W/m²/sr/nm (LATTICE multispektral)
  * `Vignette_Corrected_Images/` **veya** `Sensor_Response_Images/` — yansıma referansı olmayan kareler için kalibre edilmemiş yedek; her çalıştırmada bu ikisinden tam olarak biri bulunur ve Vignette düzeltme ayarı tarafından seçilir
  * `<INDEX>_Index_Images/` — seçilen her indeks için bir klasör (örn. `NDVI_Index_Images`)
* **`clamp_masks/`** (1.3.0 sürümünde yeni, yalnızca LATTICE multispektral M3C kameralar için): bir parlaklık veya yansıma ürününde bant ayrıştırma işlemi, negatif değerleri sıfıra kırptığında, ürün klasörlerinin yanında bir format klasöründe görünür. Etkilenen her ürün için piksel başına bir maske içerir, örn. `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif`. Maskeler asla bir ürün klasörünün içinde bulunmaz, bu nedenle bir ürün klasörü olduğu gibi fotogrametri yazılımına aktarılabilir. Bkz. [Sınırlama maskeleri](../output-image-formats.md#clamp-masks).

{% hint style="info" %}
**Dışa aktarılan her ürün, KAYNAK dosyanın adını korur.**`capture_..._raw.tif`&#x27;in radyans dışa aktarımı yine `capture_..._raw.tif` olarak adlandırılır — sadece `tiff32/Radiance_Images/` klasöründe bulunur.**Ürünü tanımlayan dosya adı değil, klasördür**; bu nedenle `*radiance*.tif` arandığında hiçbir sonuç bulunmaz; bunun yerine dizinle eşleştirme yapın.
{% endhint %}

<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-M3C multispectral camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images, clamp_masks) and tiff32 (Radiance_Images, clamp_masks) subfolders visible -->


### Kaç Dosya Olmalıdır?

Formüle göre saymayın — çıktı sayısı, hangi ürünlerin etkinleştirildiğine ve her bir kameraya hangilerinin uygulandığına bağlıdır (ör. RGB
kameralarda parlaklık/yansıtma değeri yoktur). Kesin sayı günlüğünde bulunur: son `[RUN-SUMMARY]` satırı, tam olarak kaç dosya yazıldığını bildirir ve ipucu satırları atlanan her şeyi açıklar.

***

## İşlenmiş Görüntüleri İnceleme

### Dosya Gezgini&#x27;nde Hızlı Önizleme

**Windows
&#x27;un yerleşik önizleme özelliği:**

1. Bir ürün klasörüne gidin (örn. `tiff16/Reflectance_Calibrated_Images/`)
2. Bir görüntü dosyası seçin
3. Önizleme, Windows
Gezgini&#x27;nin önizleme bölmesinde görünür
4. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Harici Görüntü Görüntüleyicilerinde Önizleme

**Önerilen görüntüleyiciler:*** **QGIS** - Ücretsiz GIS yazılımı (coğrafi referanslı multispektral analiz için en iyisi)
* **IrfanView** - Hızlı, hafif görüntü görüntüleyici (TIFF
&#x27;i destekler)
* **Adobe Photoshop** - Profesyonel düzenleme (TIFF
desteği)
* **GIMP** - Photoshop&#x27;a ücretsiz alternatif
* **Windows
Fotoğraflar** - Temel görüntüleme (16 bit TIFF
&#x27;i desteklemeyebilir)

### Chloros
Görüntü Görüntüleyicisinde Önizleme

Gelişmiş görselleştirme için Chloros
&#x27;un yerleşik Görüntü Görüntüleyicisini kullanın:

1. Dosya Tarayıcısında bir görüntü küçük resmine tıklayın
2. Görüntü ana önizleme alanında açılır
3. Sol kenar çubuğundaki **Görüntü Görüntüleyicisi** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line">
sekmesine tıklayın
4. Etkileşimli analiz için [Dizin/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) özelliğini kullanın

Ayrıntılı talimatlar için [Görüntü Görüntüleyicisi](../image-viewer-gui/opening-an-image-full-screen.md) bölümüne bakın.

***

## Yansıma Piksel Değerlerini Okuma (GIS / Pix4D / Komut Dosyaları)

Yansıma, tamsayı DN olarak saklanır ve **ρ = 1,0&#x27;a karşılık gelen DN değeri, kaynak kameraya bağlıdır**:

| Kaynak          | ρ = 1,0 değeridir | Nasıl anlaşılır                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (ρ 2,0&#x27;a kadar marj) | Dosyaya XMP etiketi `Chloros:PixelScale=32768` eklenmiştir |
| Survey3
         | **65535** (ρ 1,0&#x27;da kırpılmış)     | `Chloros:PixelScale` etiketi yok — bu yokluk bir işarettir (Survey3
ürünleri diğer `Chloros:*` işleme etiketlerini taşır) |

**`Chloros:PixelScale` etiketini okuyun ve buna bölün**; genel olarak 65535 değerini varsaymak yerine — LATTICE yansıma değerini 65535&#x27;e bölmek, her değeri sessizce yarıya indirir. 1.3.0 sürümünden önce 8 bit olarak yazılmış bir yansıma dosyası kırpılmış, yeniden ölçeklendirilmemiştir ve ölçek etiketi içermez: bölmek yerine 16 bit veya 32 bit olarak yeniden dışa aktarın. Ayrıntılı bilgi için [Çıktı Görüntü Biçimleri](../output-image-formats.md) bölümüne bakın.***

## Dışa Aktarımlara Aktarılan Meta Veriler

Her ürün, kaynak çekimin **GPS bloğunu**ve**EXIF alt IFD&#x27;sini** korur; bu nedenle bir
dışa aktarım, `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ve
`CameraSerialNumber` değerlerini ve coğrafi referans bilgilerini de içerir.

{% hint style="warning" %}
**Bir ortomozaik mantıksız bir ölçekte çıkarsa, önce `FocalLength` etiketini kontrol edin.**
Pix4D, odak uzaklığı ve irtifadan zemin örnekleme mesafesini hesaplar. Etiket olmadan
son derece yanlış bir ölçeğe düşer — 49 çekimden oluşan bir uçuşta, 411 m × 160 m
portakal bahçesi, 47,8 km × 13 km olarak yeniden oluşturulmuş ve çoğunluğu
boş alandan oluşan 455 megapiksel bir ortomosaik üretilmiştir. Yavaş döşeme ve beklenmedik derecede büyük bir dosya, bunun belirtileridir; ayrı
sorunlar değildir.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

*Her* etiket kopyalanmaz. IFD0&#x27;ın yapısal etiketleri kasıtlı olarak dışarıda bırakılır (bunların kopyalanması
LATTICE çıktısını bozar) ve `ExifImageWidth` / `ExifImageHeight` ise hariç tutulur
çünkü bunlar orijinal yakalamayı tanımlar — boyutları değiştirilmiş bir dışa aktarım, aksi takdirde
kendi rasteriyle çelişen boyutlar talep ederdi.

Chloros
, her ürüne ne yapıldığını açıklayan kendi `Chloros:*` XMP etiketlerini de yazar
(örneğin `Chloros:ProcessingLevel`, `Chloros:CalibrationApplied`,
`Chloros:VignetteCorrected`). 1.3.0 sürümünden itibaren, Survey3
ürünleri bunları her işleme
modunda bu etiketleri taşır; indeks ürünleri, kaynak ham verinin etiketlerini tekrarlamak yerine
indeksin kendisini (`Chloros:ProcessingLevel=index`),
formülü ve yansıma veya parlaklıktan hesaplanıp hesaplanmadığını) açıklar; ayrıca 260 karakterden daha derin klasörlerdeki ürünler de meta verilerini
korur. Bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md#index-products).

***

## Hata Giderme Günlüğünü İnceleme

### Uyarıları veya Hataları Kontrol Etme

1. **Hata Giderme Günlüğü** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">
sekmesini açın
2. Mesajları kaydırarak inceleyin
3. Sarı renkli uyarıları veya kırmızı renkli hataları arayın
4. `[RUN-SUMMARY]` satırlarını ve varsa ipuçlarını okuyun
5. Yardım için MAPIR
destek ekibiyle iletişime geçin

### Günlüğü Kaydetme

İşleme kaydını tutmak veya MAPIR
Destek ekibine göndermek için:

1. **&quot;Kopyala&quot;**veya**&quot;İndir&quot;** düğmesine tıklayın
2. Proje klasörüne metin dosyası olarak kaydedin
3. Proje belgelerine ekleyin
4. Herhangi bir sorunla karşılaşırsanız MAPIR
desteğine gönderin

Bir sorunu doğrudan MAPIR
&#x27;e göndermek için, Hata Giderme Günlüğü sekmesinin üst kısmındaki hata simgesini (**Hata bildir**) kullanın — bkz. [Günlük sekmesinden hata bildirme](monitoring-the-processing.md#reporting-a-bug-from-the-log-tab).***

## Yaygın Çıktı Sorunları ve Çözümleri

### Sorun: Eksik Çıktı Dosyaları

**Olası nedenler:**

* Ürün o kameraya uygun değildir (örn. RGB
kameralar için parlaklık/yansıtma — günlük dosyasında bu belirtilmiştir)
* Gerekli bir referans eksik (örn. hedef ve `.daq` aşağı doğru ışınım olmadan yansıma)
* Proje Ayarları&#x27;nda ürünün dışa aktarma onay kutusu devre dışı bırakılmış
* Dışa aktarma sırasında disk alanı doldu

**Çözümler:**

1. Hata Giderme Günlüğü&#x27;ndeki `[RUN-SUMMARY]` ipuçlarını ve `[EXPORT-CHECK]` satırlarını kontrol edin — bunlar kamera bazında atlamaları açıklar
2. [Proje Ayarları](adjusting-project-settings.md) bölümündeki ürün dışa aktarma onay kutularını kontrol edin
3. Disk alanının yeterli olup olmadığını kontrol edin
4. Nedeni giderdikten sonra işlemi yeniden gerçekleştirin

### Sorun: Karanlık veya Parlak Kenarlar (Vinyetleme Hala Görünür)

**Olası nedenler:**

* Vinyet düzeltmesi devre dışı
* Kamera/lens, Chloros
profil veritabanında yok
* Düzeltme kapasitesinin ötesinde aşırı vinyet

**Çözümler:**

1. Proje Ayarları&#x27;nda vinyet düzeltmesinin etkinleştirildiğini doğrulayın
2. Kamera modelinin doğru algılandığını kontrol edin
3. Vinyet sorunu devam ederse MAPIR
destek ekibiyle iletişime geçin

### Sorun: Yanlış Renkler veya Değerler

**Olası nedenler:**

* Kalibrasyon hedefi algılanmadı
* Yanlış kalibrasyon hedefi modeli seçildi
* Yansıma kalibrasyonu devre dışı
* Hedef görüntülerin kalitesi düşük

**Çözümler:**

1. Yansıma kalibrasyonunun etkinleştirildiğini doğrulayın
2. Hata Giderme Günlüğü&#x27;ndeki &quot;Hedef bulundu&quot; mesajlarını kontrol edin
3. Hedef görüntü kalitesini inceleyin
4. Uygun hedefler işaretlenerek yeniden işleyin

### Sorun: NDVI
Değerleri Yanlış Görünüyor

**Beklenen NDVI
aralıkları:*** **Su, kayalar, toprak**: -0,1 ila 0,2
* **Seyrek/sağlıksız bitki örtüsü**: 0,2 ila 0,4
* **Orta yoğunlukta bitki örtüsü**: 0,4 ila 0,6
* **Sağlıklı, yoğun bitki örtüsü**: 0,6 ile 0,9 arası**Değerler bu aralıkların dışındaysa:**

1. Yansıma kalibrasyonunun uygulandığını doğrulayın
2. Işık sensörü günlüğünün dahil edildiğini doğrulayın
3. Kalibrasyon hedeflerinin algılandığını kontrol edin
4. Doğru kamera modelinin algılandığından emin olun
5. Hedef görüntü yakalama zamanlamasını ve koşullarını gözden geçirin
6. Yansıma dosyalarından endeksleri kendiniz hesaplıyorsanız, dosyanın `Chloros:PixelScale` değerine (yukarıya bakın) böldüğünüzden emin olun

***

## İşlenmiş Görüntülerinizi Kullanma

### Fotogrametri / Ortomozaik Oluşturma İçin

**Önerilen iş akışı:**

1.**Kalibre edilmiş yansıma görüntülerini** fotogrametri yazılımına içe aktarın:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **EXIF meta verilerini koruyun**: Coğrafi etiketleme için GPS verilerinin korunmasını sağlayın
3. **Kalibre edilmiş iş akışları**: Bilimsel doğruluk için yansıma görüntülerini kullanın — LATTICE yansıma görüntüleri, Pix4D tarafından okunan XMP kalibrasyon etiketlerini içerir
4. **İndeks mozaiklerini işleyin**: Tek tek indeks görüntülerinden NDVI
ortomozaikleri oluşturun
5. **Coğrafi referanslı GeoTIFF
dosyasını dışa aktarın**: GIS uygulamalarında kullanım için

### GIS Analizi İçin

**Önerilen iş akışı:**

1.**QGIS, ArcGIS veya benzeri bir programa yükleyin**

2.**Çok bantlı analiz için 16 bit TIFF
** yansıma görüntülerini kullanın (dosyanın `Chloros:PixelScale` değerine bölün)
3. **İndeks görüntülerini** (NDVI
, NDRE
) kullanıma hazır bitki örtüsü katmanları olarak kullanın
4. **Raster hesaplayıcı**: Özel analizler için bantları birleştirin
5. **Dışa aktarın**: Sınıflandırma haritaları, değişiklik tespiti ve bitki örtüsü sağlık haritaları oluşturun

### Doğrudan Analiz / Raporlama İçin

**Önerilen iş akışı:**

1. Görsel raporlar için**LUT renklerine sahip indeks görüntülerini kullanın**

2.**İstatistikleri çıkarın**: Tarla/parsel başına ortalama NDVI

3. **Zaman serisi**: Birden fazla oturumdaki indeksleri karşılaştırın
4. **Raporlar oluşturun**: Haritaları, istatistikleri ve görselleştirmeleri dahil edin***

## Arşivleme ve Yedekleme

### Önerilen Yedekleme Stratejisi

**Neler kaydedilmeli:*** ✅ **Orijinal RAW/JPG görüntüler veya LATTICE ham yakalamaları** - Ayrı bir sürücüde/bulutta arşivleyin; ham veriler işleme sürecinin kaynağıdır ve diğer her şey bunlardan yeniden oluşturulabilir
* ✅ **`.daq` / `.csv` ışık sensörü dosyaları** - Daha sonra yansıma değerlerini yeniden hesaplamak için gereklidir
* ✅ **İşlenmiş çıktılar** - Kalibre edilmiş görüntüleri ve endeksleri saklayın
* ✅ **Proje klasörü** (`project.json` ve ilgili dosyalar) - Gerekirse yeniden işleme için tüm ayarları içerir
* ✅ **Hata Giderme Günlüğü** - İşleme ayrıntılarını belgeler
* ✅ **Kalibrasyon hedef görüntüleri** - Doğrulama ve yeniden işleme için**Depolama önerileri:*** **Hemen yedekleme**: Harici sabit sürücü
* **Uzun vadeli arşivleme**: Bulut depolama (Google Drive, Dropbox vb.)
* **Kritik veriler**: Farklı konumlarda 2-3 kopya saklayın***

## Sonraki İşleme Aşamaları

### Proje Ayarlarını Yeniden Kullanma

Gelecekte benzer veri kümelerini işleyecekseniz:

1. **Proje Şablonunu Kaydedin** (henüz yapılmadıysa)
2. Kaydedilen şablonu kullanarak **yeni bir proje oluşturun**

3.**Yeni görüntüleri içe aktarın**

4. Tutarlılık için aynı ayarlarla**işleyin**### Birden Fazla Oturumun Toplu İşlenmesi

Birden fazla oturum/veri kümesi için:**Seçenek 1: GUI - Birden Fazla Proje**

* Her oturum için ayrı bir proje oluşturun
* Tutarlı şablon ayarları kullanın
* Tek tek işleyin

**Seçenek 2: Chloros
CLI
(yalnızca Chloros
+ için)**

* Toplu işlemeyi otomatikleştirin
* Komut dosyalarıyla birden fazla klasörü işleyin
* [CLI
Belgeleri](../CLI.md) ve [CLI
Referansı](../reference/cli-reference.md) bölümlerine bakın

**Seçenek 3: Python
SDK
(Yalnızca Chloros
+)**

* Programlı kontrol
* Analiz iş akışlarıyla entegrasyon
* [API
Belgeleri](../api-python-sdk.md) ve [SDK
Referansı](../reference/sdk-reference.md) bölümlerine bakın

***

## Son İşleme Sorun Giderme

### Farklı Ayarlarla Yeniden İşleme

Sonuçlar tatmin edici değilse:

1. Orijinal görüntüleri saklayın (asla silmeyin)
2. Aynı projeyi Chloros
&#x27;ta açın 3. Proje Ayarları panelinde ayarları değiştirin
4. Tekrar işleyin — çıktılar aynı ürün klasörlerine kaydedilir, bu nedenle önceki çalıştırmadan kalan aynı adlı dosyalar üzerine yazılır

### Görüntülerin Bir Kısmını İşleme

Yalnızca belirli görüntüleri yeniden işlemek için:

1. Yeni bir proje oluşturun
2. Yalnızca yeniden işlenmesi gereken görüntüleri içe aktarın
3. Aynı ayar şablonunu kullanın
4. Daha küçük veri kümesini işleyin

### Yardım Alma

Sorun yaşarsanız:

* 📧 **E-posta**: info@mapir.camera (Hata Giderme Günlüğünü ekleyin)
* 🌐 **Destek**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **SSS**: [Sık Sorulan Sorular](../faq.md)
* 📖 **Kılavuz**: [Chloros
Kılavuzu](../)

***

## Özet: Tam İş Akışı

Chloros
işleme iş akışının tamamını tamamladınız:

1. ✅ **Proje oluşturuldu** - Bkz. [Projeler](../projects.md)
2. ✅ **Dosyalar eklendi** - Bkz. [Dosya Ekleme](adding-files-to-a-project.md)
3. ✅ **Ayarları düzenlediniz** - Bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md)
4. ✅ **Hedefleri işaretlediniz** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
5. ✅ **İşleme başlatıldı** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
6. ✅ **İlerleme izlendi** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)
7. ✅ **Sonuçlar incelendi** - Bu sayfa**Kalibre edilmiş, yansıma düzeltmesi yapılmış multispektral görüntüleriniz analiz için hazır!**

***

## Ek Kaynaklar

### Gelişmiş Özellikler

* [**Görüntü Görüntüleyici**](../image-viewer-gui/opening-an-image-full-screen.md) - Etkileşimli görselleştirme ve analiz
* [**İndeks/LUT Test Ortamı**](../image-viewer-gui/index-lut-sandbox.md) - Özel indeks testi
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Eksiksiz indeks referansı

### Otomasyon ve Entegrasyon

* [**CLI
Belgeleri**](../CLI.md) - Komut satırı toplu işleme
* [**Python
SDK
**](../api-python-sdk.md) - Programlı otomasyon
* [**Chloros
+ Özellikleri**](../#chloros) - Gelişmiş işleme yetenekleri

### Destek ve Öğrenme

* [**SSS**](../faq.md) - Sık sorulan soruların yanıtları
* [**Kalibrasyon Hedefleri**](../calibration-targets.md) - Yansıma kalibrasyonunu anlama
* [**Desteklenen Kameralar**](../supported-cameras.md) - Uyumlu donanım
