# Proje Ayarlarını Yapma

Görüntülerinizi işlemeden önce, proje ayarlarınızı iş akışınızın gereksinimlerine uygun şekilde yapılandırmanız önemlidir. Proje Ayarları <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> paneli, kalibrasyon, işleme seçenekleri, multispektral indeksler ve dışa aktarma formatları üzerinde kapsamlı kontrol sağlar.

## Proje Ayarlarına Erişme

1. Projenizi Chloros'ta açın
2. Sol kenar çubuğundaki **Proje Ayarları** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> simgesine tıklayın
3. Proje Ayarları paneli tüm yapılandırma seçeneklerini görüntüler

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption><p>Proje Ayarları paneli — Görüntüleme, Hedef Algılama ve İşleme</p></figcaption></figure>

{% hint style="info" %}
**Ayarlar, projenizle birlikte otomatik olarak kaydedilir**. Bir projeyi yeniden açtığınızda tüm ayarlar geri yüklenir.
{% endhint %}

***

## Yaygın İş Akışları için Hızlı Kurulum

### Varsayılan Ayarlar (Çoğu Kullanıcı İçin Önerilir)

Varsayılan ayarlar, tipik Survey3 ve LATTICE iş akışları için uygundur:

* ✅ **Vignette düzeltmesi**: Etkin
* ✅ **Yansıma kalibrasyonu / beyaz dengesi**: Etkin (MAPIR hedefleri ve/veya DAQ ışık sensörü verilerini kullanır)
* ✅ **Debayer yöntemi**: Standart (Hızlı, Orta Kalite)
* ✅ **Dışa aktarım biçimi**: TIFF (16 bit) (tamamen 8 bitlik bir proje için TIFF (8 bit))
* ✅ **Tüm dışa aktarma ürünleri**: Etkin (LATTICE, fan out görüntülerini otomatik olarak debayered, önizleme, parlaklık ve yansıma olarak yakalar)

Görüntülerinizi içe aktarın ve bu varsayılan ayarlarla işlemeyi başlatın.

***

## Proje Ayarlarına Genel Bakış

Proje Ayarları paneli aşağıdaki bölümlere ayrılmıştır. Projeniz ilgili dosyaları içerdiğinde, **DAQ Işık Sensörü**ve**Dizi Hizalama** adlı iki ek bölüm otomatik olarak görünür. Tam dokümantasyon için [Proje Ayarları](../project-settings/project-settings.md) bölümüne bakın.

### Ekran

* **Görüntü Küçük Resim Çözünürlüğü**: Görüntü ızgarasındaki küçük resimlerin çözünürlüğü. Seçenekler:**Varsayılan (512 px)**,**1024 px**,**2048 px**,**Tam çözünürlük**. Yalnızca görüntüleme amaçlıdır — işlemeyi asla etkilemez. Daha yüksek değerler, yakınlaştırıldığında daha net görünür ancak yükleme süresi uzar.

### Hedef Algılama

Chloros'un görüntülerinizdeki kalibrasyon hedeflerini nasıl tanımladığını kontrol eder.

**Önemli ayarlar:*** **Minimum kalibrasyon örnek alanı (px)**: Hedef algılama için boyut eşiği (varsayılan:**25**, aralık 0–10000)
* **Minimum Hedef Kümeleme (0-100)**: Hedef bölgeleri gruplandırmak için benzerlik eşiği (varsayılan:**60**)**Ne zaman ayarlamalısınız:**

* Yanlış algılamalar alıyorsanız örnekleme alanını artırın
* Hedefler algılanmıyorsa azaltın
* Hedefler birden fazla algılamaya bölünüyor ise kümeleme ayarını değiştirin

{% hint style="info" %}
**Yansıma kalibrasyonu / beyaz dengesi** kapalıyken bu ayarlar gri renkte görünür — bu özellik kapalıyken hedef algılama hiçbir zaman çalışmaz.
{% endhint %}

### İşleme

Ana görüntü işleme ve kalibrasyon seçenekleri.

**Önemli ayarlar:*** **Vinyet düzeltme**: Kenarlardaki lens kararmasını telafi eder ✅ Önerilir
* **Yansıma kalibrasyonu / beyaz dengesi**: Algılanan hedefleri (Survey3) ve/veya DAQ ışık sensörü verilerini (LATTICE) kullanarak görüntüleri kalibre eder ✅ Önerilir
* **Debayer yöntemi**: RAW'ı 3 kanallı multispektral formata dönüştürmek için kullanılan algoritma
* **Minimum yeniden kalibrasyon aralığı**: Kalibrasyon hedefinin kullanımı arasındaki minimum süre (saniye cinsinden) (varsayılan:**0** = tümünü kullan, aralık 0–3600)**Kalibre edilmemiş yedek ürünler:**Bir karenin yansıma kalibrasyonu yapılamadığında (hedef mevcut değilse veya kalibrasyon devre dışıysa), Vignette düzeltme anahtarı tarafından seçilen iki yedek üründen biri olarak dışa aktarılır —**her çalıştırma için bu ikiliden tam olarak biri mevcuttur**:

* **Sensör tepkisini dışa aktar**: `Sensor_Response_Images` dosyasını yazar — Vignette düzeltmesi**kapalı** olduğunda kullanılır
* **Vignette düzeltmeli olarak dışa aktar**: `Vignette_Corrected_Images` dosyasını yazar — Vignette düzeltmesi**açık** olduğunda kullanılır

Etkin olmayan onay kutusu gri renkte görünür. Etkin olanın işaretini kaldırmak, o dosyanın yazılmasını tamamen durdurur.

**LATTICE dışa aktarma ürünleri** (her proje için gösterilir; LATTICE yakalamaları için geçerlidir):

* **Deyayering uygulanmış olarak dışa aktar**: doğrusal debayered görüntü (`Debayered_Images`): kalibrasyon paketinin karanlık ofseti kaldırılır, ardından demosaicing işlemi uygulanır; düz alan veya unmix işlemi yapılmaz — bir yakalamanın debayered seviyesinde kaydettiği dosya ile aynıdır — sensörün doğal yöneliminde. RGB ve multispektral modüllere uygulanır.
* **Önizlemeyi dışa aktar**: ekran önizlemesi (`Preview_Images`). RGB = beyaz dengesi (varsa DAQ ışık kaynağı, yoksa gri dünya) + gama; multispektral = sahte renk genişletme.
* **Işınım dışa aktarma**: float32 spektral ışınım (`Radiance_Images`, W/m²/sr/nm). Yalnızca multispektral modüller için geçerlidir — RGB ana dosyalarına uygulanmaz.
* **Yansıma Dışa Aktar**: uint16 yansıma (`Reflectance_Calibrated_Images`, DN 32768 = ρ 1,0); bir `.daq` aşağı doğru ışık okuma değeri veya çerçeve içi hedef, çerçeveyi kapladığında. Yalnızca multispektral modüller için geçerlidir.

Dördünün de **varsayılan olarak açık**durumdadır — içe aktarılan bir LATTICE ham çerçevesi, tek bir işleme aşamasında etkinleştirilmiş ve geçerli tüm ürünlere dağıtılır.**Yansıma değerini dışa aktar** onay kutusu, Yansıma kalibrasyonu kapalıyken gri renkte görünür. Üst düğme nedeniyle kullanılamayan ayarlar her zaman gri renkte görünür ve değiştirilmesi gereken düğmenin adı bir araç ipucu olarak gösterilir.**Gelişmiş ayarlar:*** **Işık sensörü saat dilimi kayması**: Işık sensörü saat eşleştirmesi için UTC'den saat farkı (varsayılan: 0, aralık −12 ila +12)
* **PPK düzeltmelerini uygula**: `.daq` dosyalarındaki GPS/pozlama pimi verilerini kullanır (varsayılan: kapalı)
* **Pozlama Pimi 1/2**: Çift kamera kurulumları için kameraları pozlama pimlerine atar

{% hint style="info" %}
**LATTICE giriş seviyesi otomatiktir.** LATTICE çekimleri, işleme seviyelerini XMP meta verilerinde taşır ve işleme her zaman ham kare düzeyinde iş akışına girer — GUI'de yapılandırılması gereken hiçbir şey yoktur. (CLI bayrağı `--input-level`, meta verileri kaybolmuş çekimler için ileri düzey kullanıcılar tarafından kullanılabilecek bir geçersiz kılma seçeneği olarak mevcuttur; bkz. [CLI Referansı](../reference/cli-reference.md).)
{% endhint %}

### Debayer Yöntemi

Chloros'ta şu anda 2 debayering yöntemi sunuyoruz:

#### Standart (Hızlı, Orta Kalite)

Standart debayer hızlı işler ancak debayering kaynaklı renk gürültüsü gösterir; bu da daha az doğru ve daha gürültülü görüntülere neden olur.

#### Doku Duyarlı (Yavaş, En Yüksek Kalite) \[Yalnızca Chloros+]

Doku Duyarlı, yüksek kaliteli kenar algılayıcı bir debayer ile bir AI/ML gürültü giderme modelini birleştirir ve debayering gürültüsünün neredeyse tamamını ortadan kaldırır. Modelin çalışması için GPU belleği (VRAM) gereklidir: **7 GB veya daha fazla VRAM** ile birden fazla görüntüyü aynı anda işleyebilir; 7 GB'nin altında ise her seferinde tek bir görüntüyü işler (belirgin şekilde daha yavaştır). Bkz. [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md).

{% hint style="info" %}
**LATTICE çekimleri her zaman Standart demosaiklemeyi kullanır.** LATTICE için eğitilmiş Doku Duyarlı model bulunmadığından, bu seçenek LATTICE görüntülerinde sunulmaz — ancak aynı projedeki Survey3 görüntüleri bu özelliği kullanabilir.
{% endhint %}

### İndeks (Çok Spektral İndeksler)

Hangi bitki örtüsü indekslerinin hesaplanıp dışa aktarılacağını yapılandırın. GUI açılır menüsü **27 önceden tanımlanmış indeks formülü** sunar.**İndeksler nasıl eklenir:**

1.**"İndeks ekle"** düğmesine tıklayın
2. Açılır menüden bir endeks seçin (NDVI, NDRE, GNDVI vb.)
3. Görselleştirme ayarlarını yapılandırın (LUT renkleri, değer aralıkları)
4. Gerektiği kadar birden fazla endeks ekleyin

**Popüler endeksler:*** **NDVI**: Genel bitki örtüsü sağlığı (en yaygın)
* **NDRE**: RedEdge ile erken stres tespiti* **GNDVI**: Klorofil konsantrasyonuna duyarlı
* **OSAVI**: Görünür toprakta iyi sonuç verir
* **EVI**: Yüksek yaprak alanı indeksi (LAI) bölgeleri

**Özel formüller:**

* Tüm görüntü kanalları üzerinde bant matematiği kullanarak özel multispektral indeks formülleri oluşturun
* Tekrar kullanmak üzere özel formülleri kaydedin
* Özel formüller, Chloros+'ın bir özelliğidir; kullanılabilirliği, plan seviyenize bağlıdır

Mevcut tüm indeksler ve formüller için — hangilerinin yalnızca GUI'de, hangilerinin ise CLI/SDK'de de çalıştığı dahil — bkz. [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md).

### Dışa Aktarma

Çıktı dosya biçimini kontrol eder.

**Kullanılabilir biçimler**(ayar:**Kalibre edilmiş görüntü biçimi**, varsayılan**TIFF (16-bit)**):

* **TIFF (16-bit)**: GIS ve bilimsel analizler için önerilir
* **TIFF (8 bit)**: 8 bit TIFF
* **TIFF (32 bit, Yüzde)**: Kayan nokta değerleri
* **PNG (8 bit)**: Görselleştirme için kayıpsız sıkıştırma
* **JPG (8 bit)**: En küçük dosyalar, kayıplı sıkıştırma

Liste, projenin görüntülerinin destekleyebileceği özellikleri gösterir: Chloros, bit derinliğini yükseltmez. Tamamen 8-bit görüntüler yalnızca TIFF (8-bit), PNG (8-bit) ve JPG (8-bit) formatlarını sunar ve varsayılan olarak TIFF (8-bit) kullanılır. Karışık bir projede her bir çekim, kendi derinliğinde veya daha düşük bir derinlikte dışa aktarılır. Radiance, 32-bit float olarak kalır.

Çıktılar, kamera ve formatına göre gruplandırılmış olarak proje klasörü altına yazılır: `<project>/<camera>/<format>/<Product>_Images/`. Radiance, bu ayardan bağımsız olarak **her zaman** float32 olarak `tiff32` klasörüne yazılır. Dışa aktarılan dosyalar kaynak dosya adını korur — ürünü tanımlayan klasördür. LATTICE multispektral dışa aktarımları, ürün klasörlerinin yanındaki bir `clamp_masks/` klasörüne piksel başına sınırlama maskeleri de yazabilir. Tam çıktı ağacı için [İşlemenin Tamamlanması](finishing-the-processing.md) bölümüne bakın.

{% hint style="warning" %}
**Yansıma değerlerini okuma**: ρ = 1,0 anlamına gelen DN, kaynak kameraya bağlıdır — LATTICE 32768'i (XMP `Chloros:PixelScale` olarak işaretlenmiştir) kullanır, Survey3 ise 65535'i kullanır. Sabit bir değer varsaymak yerine etiketi okuyun. Bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md).
{% endhint %}

### DAQ Işık Sensörü

Bu bölüm, projenizdeki tüm DAQ aşağı yönlü ışık dosyalarını (`.daq` / `.csv`) listeler; her dosya için bir satır bulunur ve sensör modeli, dosya adı ve o dosya için geçerli olan difüzör **kapak** düzeltmesini gösterir.

* **Kapak geçersiz kılma (tüm dosyalar)**: proje genelinde tek bir açılır menü.**Otomatik** (varsayılan) seçeneği, her dosyanın kaydedilmiş sınır değerini kullanır — hiçbir şey kaydedilmemişse güneş ışığı varsayılır; ancak DAQ-U, DAQ-M ve DAQ-E, sınır değeri olmadan gelir ve Chloros, hangi değerin ayarlandığını göremez; bu nedenle işleme, varsayılan sınır değeri konusunda uyarı verir: ayarlanmış olan sınır değerini seçin. Bir sınır seçilmesi, difüzörü kalibrasyonunun bir parçası olan DAQ-E-S'ler hariç tüm dosyaları geçersiz kılar: ham kayıtlar bununla düzeltilir ve halihazırda bir sınır içeren kayıtlar yeniden referanslanır (kaydedilen düzeltme geri alınır, seçilen sınır uygulanır).
* Satırlar, kaydedilmiş bir kapak değerinin operatör tarafından onaylanmamış, hub'ın varsayılan değeri olması durumunda ve seçilen kapak değerinin o cihaz modeli için profili bulunmaması durumunda uyarı verir (bu dosya için geçersiz kılma reddedilir).

Işık Sensörleri sekmesinde yapılan DAQ kayıtları, açık projeye otomatik olarak eklenir ve içe aktarılan `.daq` / `.csv` dosyaları, eklenir eklenmez burada görünür.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p>Alt Proje Ayarları — Dizin, Dışa aktarma biçimi, DAQ Işık Sensörü bölümü ve proje şablonu/klasör denetimleri</p></figcaption></figure>### Dizi Hizalama

Bu bölüm, **yalnızca** projedeki en az bir görüntünün, LATTICE dizilerinin yakalama sırasında eklediği modülden modüle hizalama dönüşümünü (`Chloros:Alignment*` XMP) taşıması durumunda görünür. Bu bölüm, etiket taşıyan görüntü sayısını ve hangi kameranın referans olduğunu gösterir; aşağıdaki denetimler mevcuttur:

* **Dizi hizalaması uygula** (varsayılan: açık): işlenmiş her ürünü (debayering / önizleme / parlaklık / yansıma / indeks) dizinin ortak referans geometrisine göre hizalar. Kapalı = orijinal sensör geometrisinde dışa aktarır.
* **Ortak örtüşme alanına kırp** (varsayılan: açık): hizalanmış dışa aktarımları, tüm modüllerin paylaştığı bölgeye kırpar; böylece her bant aynı kaplama alanına sahip olur. Kapalı seçeneği, sensörün tam tuvalini korur (kaynağın dışını siyahla doldurur).
* **Yeniden örnekleme**:**Bilineer (pürüzsüz, varsayılan)**,**En yakın (kesin değerleri korur)**— sıkı radyometrik analiz için piksel arası karıştırma yok — veya**Kübik (en keskin)**.***

## Ayarları Kaydetme ve Yükleme

### Proje Şablonunu Kaydetme

Tutarlı iş akışları için yeniden kullanılabilir şablonlar oluşturun:

1. Proje Ayarları panelinde istediğiniz tüm ayarları yapılandırın
2. Sayfanın altındaki **"Proje Şablonunu Kaydet"** bölümüne gidin
3. Açıklayıcı bir şablon adı girin (örn., "Survey3N\_RGN\_Agriculture")
4. Kaydet simgesine tıklayın

**Avantajlar:**

* Birden fazla projede aynı ayarları uygulayın
* Yapılandırmaları ekip üyeleriyle paylaşın
* Tekrarlanan araştırmalarda tutarlılığı koruyun

### Yeni Projede Şablonu Yükleme

Yeni bir proje oluştururken:

1. Ana menüden **"Yeni Proje"** seçeneğini seçin
2. İsteğe bağlı şablon seçiciden bir proje şablonu seçin
3. Şablondaki tüm ayarlar otomatik olarak uygulanır

### Çalışma Dizini

**"Çalışma Dizini"** ayarı, yeni projelerin varsayılan olarak nerede oluşturulacağını belirler:

* **Varsayılan konum**: Windows Belgeler klasörünüzdeki `Documents\MAPIR\Chloros Projects` (genellikle `C:\Users\[Username]\Documents\MAPIR\Chloros Projects`; OneDrive veya gezici profil tarafından taşınırsa Belgeler klasörünü takip eder)
* **Konumu değiştir**: Düzenle simgesine tıklayın ve yeni bir klasör seçin
* **CLI ile paylaşılmış**: `chloros-cli`, aynı varsayılan proje klasörü ayarını kullanır
* **Windows'ta kısa tutun**: Windows'ta uzun yol seçeneği etkinleştirilmedikçe, yaklaşık 130 karakterden daha uzun bir proje klasörü işleme alınmadan reddedilir — bkz. [İşlemeyi Başlatma](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer)
* **Ne zaman değiştirilmeli**:
  * Ekip işbirliği için ağ sürücüsü
  * Daha fazla depolama alanına sahip farklı bir sürücü
  * Yıl/müşteri bazında düzenlenmiş klasör yapısı

***

## PPK (Sonradan İşlenmiş Kinematik) Kurulumu

Hassas coğrafi konum belirleme için GPS özellikli MAPIR DAQ kayıt cihazları kullanılıyorsa:

### Ön Koşullar

* GPS (GNSS) modülüne sahip MAPIR DAQ
* Pozlama pini girişleri içeren .daq kayıt dosyası
* Çekim oturumu sırasında DAQ pozlama pinlerine bağlı kamera

### Yapılandırma Adımları

1. .daq kayıt dosyasını proje klasörünüze yerleştirin
2. Proje Ayarları'nda **"PPK düzeltmelerini uygula"** onay kutusunu etkinleştirin
3. Gerekirse **"Işık sensörü saat dilimi kayması"** ayarını yapın (varsayılan: UTC için 0)
4. Kameraları pozlama pinlerine atayın:
   * **Tek kamera**: Otomatik olarak Pin 1'e atanır
   * **Çift kamera**: Her kamerayı doğru pime manuel olarak atayın**Pozlama Pini Ataması:*** **Pozlama Pini 1**: Açılır menüden kamera modelini seçin
* **Pozlama Pini 2**: İkinci kamerayı veya "Kullanma" seçeneğini seçin
* Aynı kamera her iki pime de atanamaz

{% hint style="warning" %}
**Önemli**: Pozlama pinleri, ilgili kameralara doğru şekilde atanmalıdır. Yanlış atama, hatalı coğrafi konum verilerine neden olur.
{% endhint %}

***

## Gelişmiş Senaryolar

### Çoklu Kamera Projeleri

Tek bir projede birden fazla MAPIR kamerasından gelen görüntüleri işlerken:

1. Chloros, her kamera modelini (Survey3 ve LATTICE dahil) otomatik olarak algılar
2. Her kameraya uygun işleme profilleri atanır ve her kamera kendi çıktı klasör yapısına sahip olur
3. PPK: Her bir Survey3 kamerasını doğru pozlama pimine manuel olarak atayın
4. Tüm kameralar aynı dışa aktarım formatını ve indekslerini kullanır

**Örnekler**: Survey3W RGN + Survey3N OCN çift kamera düzeneği veya bir RGB ana kamerayı dar bant modülleriyle birleştiren bir LATTICE dizisi

### Zaman Atlamalı veya Çok Tarihli Ölçümler

Aynı alanın zaman içinde tekrarlanan ölçümleri için:

1. Standart ayarlarınızı içeren bir şablon oluşturun
2. Her oturumda tutarlı bir kalibrasyon hedefi kurulumu kullanın
3. Her tarihi ayrı bir proje olarak işleyin
4. Karşılaştırılabilir sonuçlar için aynı ayarları kullanın
5. Zaman analizleri için aynı formatta dışa aktarın

### Büyük Veri Kümeleri

Çok sayıda görüntü içeren projeler için (500+):

* Tarihe veya alana göre daha küçük projelere bölmeyi düşünün
* Daha hızlı sonuçlar için Chloros+ paralel işlemeyi kullanın
* Toplu iş otomasyonu için CLI veya API'yi değerlendirin
* Hedef algılama süresini kısaltmak için minimum yeniden kalibrasyon aralığını ayarlayın

***

## Ayarlarınızı Doğrulama

İşlemeye başlamadan önce şu önemli ayarları gözden geçirin:

* [ ] Dosya Tarayıcısında kamera modeli doğru şekilde algılandı
* [ ] Vinyet düzeltmesi etkinleştirildi
* [ ] Yansıma kalibrasyonu etkinleştirildi
* [ ] Survey3 için: en az bir kalibrasyon hedef görüntüsü içe aktarıldı ve kontrol edildi; LATTICE için: bir hedef ve/veya bir `.daq` aşağı yönlü kayıt mevcut
* [ ] İstenen multispektral indeksler eklendi
* [ ] İş akışınıza uygun dışa aktarma biçimi seçildi
* [ ] PPK ayarları yapılandırıldı (pozlama olayları içeren .daq dosyası kullanılıyorsa)

***

## Sonraki Adımlar

Ayarlarınızı yapılandırdıktan sonra:

1. **Kalibrasyon hedef görüntülerini işaretleyin** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
2. **İşlemeye başlayın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
3. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)

Mevcut tüm ayarlarla ilgili ayrıntılı bilgi için [Proje Ayarları](../project-settings/project-settings.md) referans belgesine bakın.
