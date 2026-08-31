# İşlemin Tamamlanması

Chloros işlemi tamamlandığında, sonuçlarınızı gözden geçirme, çıktı kalitesini doğrulama ve iş akışınızda kullanmak üzere işlenmiş görüntülerinizi hazırlama zamanı gelmiştir. Bu sayfa, son adımlar ve sonraki işlemler konusunda size rehberlik eder.

## İşlemin Tamamlandığına Dair Göstergeler

İşlem başarıyla tamamlandığında, birkaç gösterge göreceksiniz:

* ✅ **İlerleme çubuğu**: %100&#x27;e ulaşır
* ✅ **Hata Giderme Günlüğü**: `[RUN-SUMMARY]`&#x27;in son satırlarını sayılarla birlikte gösterir (görüntüler, kamera grupları, hedefler, kalibre edilmiş görüntüler, yazılan dosyalar)
* ✅ **Başlat düğmesi**: Tekrar etkin hale gelir (bir sonraki işleme çalışması için hazır)
* ✅ **Çıktı dosyaları**: İşlenen tüm görüntüler projenin çıktı dizinine kaydedilir (aşağıda)

{% hint style="warning" %}
**Görüntü kaydetmeyen bir işleme, başarısızlık olarak değerlendirilir.** Görüntü ürünleri talep ettiyseniz ve işlem hiç görüntü kaydetmediyse, Chloros bunu bir hata olarak bildirir — `[RUN-SUMMARY]`, günlük adında olası nedeni belirtir (hiçbir şey içe aktarılmadı, hedef algılanmadı veya talep edilen tüm ürünler uygulanamaz olduğu için atlandı). CLI eşdeğeri sıfırdan farklı bir değerle sonlanır. Kasıtlı olarak yalnızca meta verilerin işlendiği bir işlem (tüm dışa aktarma ürünleri kapalı, dizin yok) yine de başarılı sayılır. Bkz. [CLI Referansı](../reference/cli-reference.md#a-run-that-writes-no-images-fails).
{% endhint %}

***

## İşlenmiş Görüntülerinizi Bulma

### Çıktı Klasörünü Açma

1. **Ana Menü**&#x27;n<img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt="" data-size="line">simgesine (sol üst) tıklayın
2. **&quot;Proje Klasörünü Aç&quot;**&#x27;ı seçin
3. Dosya gezgininiz proje dizininde açılır
4. Adına göre projenizi bulun

### Çıktı Ağacı

Ürünler **proje klasörünün altında, önce kameraya, ardından dosya biçimine göre gruplandırılmış olarak** kaydedilir:

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

* **Kamera klasörü**: LATTICE için `LATT-<sensor>-<lens>-F<filter>` (çekimin EXIF verisiyle eşleşir: `Model`), `<model>_<filter>`, Survey3 için (örn. `Survey3N_RGN`). Aynı sensörü ve filtreyi paylaşan ancak lensleri farklı olan iki kamera, ayrı dizin ağaçlarına sahip olur — vinyet, görüş alanı ve distorsiyon farklılık gösterir.
* **Biçim klasörü**: dışa aktarma biçimi ayarınıza uyar — `tiff16`, `tiff8`, `png8`, `jpg8`, veya TIFF için `tiff32` (32-bit, Yüzde). Radiance her zaman float32&#x27;dir ve her zaman `tiff32` altında yer alır.
* **Ürün klasörleri**:
  * `Reflectance_Calibrated_Images/` — kalibre edilmiş yansıma
  * `Debayered_Images/` — doğrusal debayering uygulanmış (LATTICE)
  * `Preview_Images/` — ekran önizlemesi (LATTICE)
  * `Radiance_Images/` — float32 spektral radyans, W/m²/sr/nm (LATTICE multispektral)
  * `Vignette_Corrected_Images/` **veya** `Sensor_Response_Images/` — yansıma referansı olmayan kareler için kalibre edilmemiş yedek; her çalıştırma başına bu ikisinden tam olarak biri bulunur ve bu, Vignette düzeltme ayarı tarafından seçilir
  * `<INDEX>_Index_Images/` — seçilen her indeks için bir klasör (örn. `NDVI_Index_Images`)

{% hint style="info" %}
**Dışa aktarılan her ürün, KAYNAK dosyanın adını korur.**`capture_..._raw.tif` dosyasının radyans dışa aktarımı yine `capture_..._raw.tif` olarak adlandırılır — sadece `tiff32/Radiance_Images/` klasöründe bulunur.**Ürünü tanımlayan dosya adı değil, klasördür**; bu nedenle `*radiance*.tif` arandığında hiçbir sonuç bulunmaz; bunun yerine dizini kullanarak arama yapın.
{% endhint %}



<!-- SCREENSHOT-NEEDED: Windows Explorer open on a processed project folder showing the tree: a LATT-… camera folder expanded with tiff16 (Reflectance_Calibrated_Images, Debayered_Images, Preview_Images, NDVI_Index_Images) and tiff32 (Radiance_Images) subfolders visible -->### Kaç Dosya Olmalıdır?

Formüle göre saymayın — çıktı sayısı, hangi ürünlerin etkinleştirildiğine ve her bir kameraya hangilerinin uygulandığına bağlıdır (ör. RGB kameraları parlaklık/yansıtma verisi almaz). Kesin sayı günlüğünde yer alır: son `[RUN-SUMMARY]` satırı tam olarak kaç dosyanın yazıldığını bildirir ve ipucu satırları atlanan her şeyi açıklar.

***

## İşlenmiş Görüntüleri İnceleme

### Dosya Gezgini’nde Hızlı Önizleme

**Windows yerleşik önizleme:**

1. Bir ürün klasörüne gidin (örn. `tiff16/Reflectance_Calibrated_Images/`)
2. Bir görüntü dosyası seçin
3. Önizleme, Windows Gezgini önizleme bölmesinde görüntülenir
4. Ok tuşlarını kullanarak görüntüler arasında gezinin

### Harici Görüntü Görüntüleyicilerde Önizleme

**Önerilen görüntüleyiciler:*** **QGIS** - Ücretsiz GIS yazılımı (coğrafi referanslı multispektral analiz için en iyisi)
* **IrfanView** - Hızlı, hafif bir resim görüntüleyicisi (TIFF&#x27;i destekler)
* **Adobe Photoshop** - Profesyonel düzenleme (TIFF desteği)
* **GIMP** - Photoshop&#x27;a ücretsiz alternatif
* **Windows Photos** - Temel görüntüleme (16 bit TIFF&#x27;i desteklemeyebilir)

### Chloros Görüntü Görüntüleyicide Önizleme

Gelişmiş görüntüleme için Chloros&#x27;in yerleşik Görüntü Görüntüleyicisini kullanın:

1. Dosya Tarayıcısında bir görüntü küçük resmine tıklayın
2. Görüntü ana önizleme alanında açılır
3. Sol kenar çubuğundaki **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> sekmesine tıklayın
4. Etkileşimli analiz için [Dizin/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) özelliğini kullanın

Ayrıntılı talimatlar için [Görüntü Görüntüleyici](../image-viewer-gui/opening-an-image-full-screen.md) bölümüne bakın.

***

## Yansıma Piksel Değerlerini Okuma (GIS / Pix4D / Komut Dosyaları)

Yansıma, tamsayı DN olarak saklanır ve **ρ = 1,0 anlamına gelen DN değeri, kaynak kameraya bağlıdır**:

| Kaynak          | ρ = 1,0 değerine karşılık gelen | Nasıl anlaşılır                                        |
| --------------- | ---------- | -------------------------------------------------- |
| LATTICE (M3C/M3M) | **32768** (ρ 2,0’a kadar başlık aralığı) | Dosyaya XMP etiketi `Chloros:PixelScale=32768` damgalanır |
| Survey3         | **65535** (ρ 1,0&#x27;da kırpılmış)     | `Chloros:*` XMP etiketleri yok — bu yokluk bir işarettir |

**`Chloros:PixelScale` etiketini okuyun ve buna bölün**; genel olarak 65535 değerini varsaymak yerine — LATTICE yansıma değerini 65535&#x27;e bölmek, her değeri sessizce yarıya indirir. Tasarım gereği ölçek içermeyen bir istisnai durum vardır: 8 bit çıktı olarak yazılan 8 bit kaynak yakalama, yeniden ölçeklendirilmez, kırpılır ve kasıtlı olarak ölçek etiketi almaz — bölmek yerine 16 bit veya 32 bit olarak yeniden dışa aktarın. Tüm ayrıntılar için bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md) bölümüne bakın.***

## Dışa Aktarılara Taşınan Meta Veriler

Her ürün, kaynak yakalamanın **GPS bloğunu**ve**EXIF alt-IFD&#x27;sini** korur; bu nedenle bir
dışa aktarımda `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ve
`CameraSerialNumber` değerlerini ve coğrafi referans bilgilerini içerir.

{% hint style="warning" %}
**Bir ortomozaik mantıksız bir ölçekte çıkarsa, önce `FocalLength`&#x27;i kontrol edin.**
Pix4D, odak uzaklığı ve irtifadan zemin örnekleme mesafesini hesaplar. Bu etiket olmadan
son derece hatalı bir ölçeğe düşer — 49 çekimden oluşan bir uçuşta, 411 m × 160 m
büyüklüğündeki bir portakal bahçesi, 47,8 km × 13 km olarak yeniden yapılandırıldı ve çoğunluğu
boş alandan oluşan 455 megapiksel bir ortomozaik ortaya çıktı. Yavaş döşeme ve beklenmedik derecede büyük bir dosya, bunun belirtileridir; ayrı
sorunlar değildir.

```bash
exiftool -FocalLength -GPSLatitude "YourProject/.../some_export.tif"
```
{% endhint %}

*Her* etiket kopyalanmaz. IFD0’un yapısal etiketleri kasıtlı olarak dışarıda bırakılır (bunların kopyalanması
LATTICE çıktısını bozar) ve `ExifImageWidth` / `ExifImageHeight`,
orijinal yakalamayı tanımladıkları için hariç tutulmuştur — aksi takdirde, yeniden boyutlandırılmış bir dışa aktarım,
kendi rasteriyle çelişen boyutlar bildirirdi.

***

## Hata Giderme Günlüğünü İnceleme

### Uyarıları veya Hataları Kontrol Etme

1. **Hata Giderme Günlüğü**&#x27;nu açın <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> sekmesi
2. Mesajları kaydırın
3. Sarı uyarıları veya kırmızı hataları arayın
4. `[RUN-SUMMARY]` satırlarını ve varsa ipuçlarını okuyun
5. Yardım için MAPIR destek ekibiyle iletişime geçin

### Günlüğü Kaydetme

İşleme kaydını tutmak veya MAPIR Destek ekibine göndermek için:

1. **&quot;Kopyala&quot;**veya**&quot;İndir&quot;** düğmesine tıklayın
2. Proje klasörüne metin dosyası olarak kaydedin
3. Proje belgelerine ekleyin
4. Sorunla karşılaşırsanız MAPIR destek ekibine gönderin

***

## Yaygın Çıktı Sorunları ve Çözümleri

### Sorun: Çıktı Dosyaları Eksik

**Olası nedenler:**

* Ürün o kameraya uygun değildir (örn. RGB kameralar için parlaklık/yansıtma — günlük dosyasında bu belirtilmiştir)
* Gerekli bir referans eksik (örn. hedef ve `.daq` aşağı doğru ışınım olmadan yansıma)
* Proje Ayarları&#x27;nda ürünün dışa aktarma onay kutusu devre dışı bırakılmış
* Dışa aktarma sırasında disk alanı doldu

**Çözümler:**

1. Hata Giderme Günlüğü&#x27;ndeki `[RUN-SUMMARY]` ipuçlarını ve `[EXPORT-CHECK]` satırlarını kontrol edin — bunlar kamera başına atlamaları açıklamaktadır
2. [Proje Ayarları](adjusting-project-settings.md) bölümündeki ürün dışa aktarma onay kutularını kontrol edin
3. Yeterli disk alanı olup olmadığını kontrol edin
4. Nedeni giderdikten sonra işlemi yeniden gerçekleştirin

### Sorun: Karanlık veya Parlak Kenarlar (Vinyetleme Hala Görünür)

**Olası nedenler:**

* Vinyet düzeltmesi devre dışı
* Kamera/lens, Chloros profil veritabanında yok
* Düzeltme kapasitesinin ötesinde aşırı vinyet

**Çözümler:**

1. Proje Ayarları&#x27;nda vinyet düzeltmesinin etkinleştirildiğini doğrulayın
2. Kamera modelinin doğru algılandığını kontrol edin
3. Vinyetleme sorunu devam ederse MAPIR destek ekibiyle iletişime geçin

### Sorun: Yanlış Renkler veya Değerler

**Olası nedenler:**

* Kalibrasyon hedefleri algılanmadı
* Yanlış kalibrasyon hedefi modeli seçildi
* Yansıtma kalibrasyonu devre dışı
* Hedef görüntülerin kalitesi düşük

**Çözümler:**

1. Yansıma kalibrasyonunun etkinleştirildiğini doğrulayın
2. Hata Giderme Günlüğü&#x27;ndeki &quot;Hedef bulundu&quot; mesajlarını kontrol edin
3. Hedef görüntü kalitesini inceleyin
4. Uygun hedefler işaretlenerek işlemi yeniden gerçekleştirin

### Sorun: NDVI Değerleri Yanlış Görünüyor

**Beklenen NDVI aralıkları:*** **Su, kayalar, toprak**: -0,1 ila 0,2
* **Seyrek/sağlıksız bitki örtüsü**: 0,2 ile 0,4 arası
* **Orta yoğunlukta bitki örtüsü**: 0,4 ile 0,6 arası
* **Sağlıklı, yoğun bitki örtüsü**: 0,6 ile 0,9 arası**Değerler bu aralıkların dışındaysa:**

1. Yansıma kalibrasyonunun uygulandığını doğrulayın
2. Işık sensörü günlüğünün dahil edildiğini doğrulayın
3. Kalibrasyon hedeflerinin algılandığını kontrol edin
4. Doğru kamera modelinin algılandığından emin olun
5. Hedef görüntü yakalama zamanlamasını ve koşullarını gözden geçirin
6. Yansıtma dosyalarından endeksleri kendiniz hesaplıyorsanız, dosyanın `Chloros:PixelScale` değerine (yukarıya bakın) böldüğünüzden emin olun

***

## İşlenmiş Görüntülerinizi Kullanma

### Fotogrametri / Ortomozaik Oluşturma İçin

**Önerilen iş akışı:**

1.**Kalibre edilmiş yansıma görüntülerini** fotogrametri yazılımına aktarın:
   * Pix4Dmapper
   * Agisoft Metashape
   * DroneDeploy
   * WebODM
2. **EXIF meta verilerini koruyun**: Coğrafi etiketleme için GPS verilerinin korunduğundan emin olun
3. **Kalibre edilmiş iş akışları**: Bilimsel doğruluk için yansıma görüntülerini kullanın — LATTICE yansıma görüntüleri, Pix4D tarafından okunan XMP kalibrasyon etiketlerini içerir
4. **Dizin mozaiklerini işleyin**: Tek tek dizin görüntülerinden NDVI ortomozaikleri oluşturun
5. **Coğrafi referanslı GeoTIFF&#x27;i dışa aktarın**: GIS uygulamalarında kullanım için

### GIS Analizi İçin

**Önerilen iş akışı:**

1.**QGIS, ArcGIS veya benzeri bir programa yükleyin**

2.**Çok bantlı analiz için 16 bitlik TIFF** yansıma görüntülerini kullanın (dosyanın `Chloros:PixelScale` değerine bölün)
3. **İndeks görüntülerini** (NDVI, NDRE) kullanıma hazır bitki örtüsü katmanları olarak kullanın
4. **Raster hesaplayıcı**: Özel analiz için bantları birleştirin
5. **Dışa aktarma**: Sınıflandırma haritaları oluşturun, değişiklik tespiti yapın, bitki örtüsü sağlık haritaları oluşturun

### Doğrudan Analiz / Raporlama için

**Önerilen iş akışı:**

1. Görsel raporlar için**LUT renklerine sahip indeks görüntülerini kullanın**

2.**İstatistikleri çıkarın**: Tarla/parsel başına ortalama NDVI değeri
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

## Sonraki İşleme Çalıştırmaları

### Proje Ayarlarını Yeniden Kullanma

Gelecekte benzer veri kümelerini işleyecekseniz:

1. **Proje Şablonunu Kaydedin** (henüz yapılmadıysa)
2. Kaydedilen şablonu kullanarak **yeni proje oluşturun**

3.**Yeni görüntüleri içe aktarın**

4. Tutarlılık için aynı ayarlarla**işleyin**### Birden Fazla Oturumu Toplu İşleme

Birden fazla oturum/veri kümesi için:**Seçenek 1: GUI - Birden Fazla Proje**

* Her oturum için ayrı bir proje oluşturun
* Tutarlı şablon ayarları kullanın
* Tek tek işleyin

**Seçenek 2: Chloros CLI (yalnızca Chloros+ için)**

* Toplu işlemeyi otomatikleştirin
* Komut dosyalarıyla birden fazla klasörü işleyin
* [CLI Belgeleri](../CLI.md) ve [CLI Referansı](../reference/cli-reference.md) bölümlerine bakın

**Seçenek 3: Python SDK (yalnızca Chloros ve üzeri sürümler)**

* Programlı kontrol
* Analiz iş akışlarıyla entegrasyon
* [API Belgeleri](../api-python-sdk.md) ve [SDK Referansı](../reference/sdk-reference.md) bölümlerine bakın

***

## Son İşleme Sorun Giderme

### Farklı Ayarlarla Yeniden İşleme

Sonuçlar tatmin edici değilse:

1. Orijinal görüntüleri saklayın (asla silmeyin)
2. Aynı projeyi Chloros&#x27;te açın
3. Proje Ayarları panelinde ayarları değiştirin
4. Tekrar işleyin — çıktılar aynı ürün klasörlerine kaydedilir, bu nedenle önceki işlemden kalan aynı isimdeki dosyalar üzerine yazılır

### Görüntülerin Bir Kısmını İşleme

Yalnızca belirli görüntüleri yeniden işlemek için:

1. Yeni bir proje oluşturun
2. Yalnızca yeniden işlenmesi gereken görüntüleri içe aktarın
3. Aynı ayar şablonunu kullanın
4. Daha küçük veri kümesini işleyin

### Yardım Alma

Sorunlarla karşılaşırsanız:

* 📧 **E-posta**: info@mapir.camera (Hata Giderme Günlüğünü ekleyin)
* 🌐 **Destek**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **SSS**: [Sık Sorulan Sorular](../faq.md)
* 📖 **Belgeler**: [Chloros Kılavuzu](../)***

## Özet: Tam İş Akışı

Artık Chloros işleme iş akışının tamamını tamamladınız:

1. ✅ **Proje oluşturuldu** - Bkz. [Projeler](../projects.md)
2. ✅ **Dosyalar eklendi** - Bkz. [Dosya Ekleme](adding-files-to-a-project.md)
3. ✅ **Ayarlar düzenlendi** - Bkz. [Proje Ayarlarını Düzenleme](adjusting-project-settings.md)
4. ✅ **Hedefler işaretlendi** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
5. ✅ **İşleme başlatıldı** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
6. ✅ **İlerleme izlendi** - Bkz. [İşlemi İzleme](monitoring-the-processing.md)
7. ✅ **Sonuçlar incelendi** - Bu sayfa**Kalibre edilmiş, yansıma düzeltmesi yapılmış multispektral görüntüleriniz analiz için hazır!**

***

## Ek Kaynaklar

### Gelişmiş Özellikler

* [**Görüntü Görüntüleyici**](../image-viewer-gui/opening-an-image-full-screen.md) - Etkileşimli görselleştirme ve analiz
* [**İndeks/LUT Test Ortamı**](../image-viewer-gui/index-lut-sandbox.md) - Özel indeks testi
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Kapsamlı indeks referansı

### Otomasyon ve Entegrasyon

* [**CLI Belgeleri**](../CLI.md) - Komut satırı toplu işleme
* [**Python SDK**](../api-python-sdk.md) - Programlı otomasyon
* [**Chloros+ Özellikler**](../#chloros) - Gelişmiş işleme yetenekleri

### Destek ve Eğitim

* [**SSS**](../faq.md) - Sık sorulan soruların yanıtları
* [**Kalibrasyon Hedefleri**](../calibration-targets.md) - Yansıma kalibrasyonunu anlama
* [**Desteklenen Kameralar**](../supported-cameras.md) - Uyumlu donanımlar
