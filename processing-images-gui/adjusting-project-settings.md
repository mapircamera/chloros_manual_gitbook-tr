# Proje Ayarlarını Yapma

Görüntülerinizi işlemeden önce, iş akışınızın gereksinimlerine uygun şekilde proje ayarlarınızı yapılandırmanız önemlidir. Proje Ayarları <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> paneli, kalibrasyon, işleme seçenekleri, multispektral indeksler ve dışa aktarma formatları üzerinde kapsamlı kontrol sağlar.

## Proje Ayarlarına Erişme

1. Projenizi Chloros&#x27;te açın
2. Sol kenar çubuğundaki **Proje Ayarları** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> simgesine tıklayın
3. Proje Ayarları paneli tüm yapılandırma seçeneklerini görüntüler

{% hint style="info" %}
**Ayarlar projenizle birlikte otomatik olarak kaydedilir**. Bir projeyi yeniden açtığınızda, tüm ayarlar geri yüklenir.
{% endhint %}

***

## Yaygın İş Akışları için Hızlı Kurulum

### Varsayılan Ayarlar (Çoğu Kullanıcı İçin Önerilir)

Tipik MAPIR Survey3 kamera iş akışları için varsayılan ayarlar iyi sonuç verir:

* ✅ **Vinyet düzeltme**: Etkin
* ✅ **Yansıma kalibrasyonu**: Etkin (MAPIR hedeflerinin görüntüleri gerekir)
* ✅ **Debayer yöntemi**: Standart (Hızlı, Orta Kalite)
* ✅ **Dışa aktarım biçimi**: TIFF (16 bit)

Görüntülerinizi içe aktarın ve bu varsayılan ayarlarla işlemeyi başlatın.

***

## Proje Ayarlarına Genel Bakış

Proje Ayarları paneli birkaç kategoriye ayrılmıştır. Aşağıda her bölümün özeti yer almaktadır. Tam dokümantasyon için [Proje Ayarları](../project-settings/project-settings.md) sayfasına bakın.

### Hedef Algılama

Chloros&#x27;in görüntülerinizdeki kalibrasyon hedeflerini nasıl tanımladığını kontrol eder.

**Önemli ayarlar:*** **Minimum kalibrasyon örnek alanı**: Hedef algılama için boyut eşiği (varsayılan: 25 piksel)
* **Minimum hedef kümelenmesi**: Hedef bölgeleri gruplandırmak için benzerlik eşiği (varsayılan: 60)**Ne zaman ayarlamalısınız:**

* Yanlış algılamalar alıyorsanız örnek alanını artırın
* Hedefler algılanmıyorsa azaltın
* Hedefler birden fazla algılamaya bölünüyor ise kümelemeyi ayarlayın

### İşleme

Ana görüntü işleme ve kalibrasyon seçenekleri.

**Önemli ayarlar:*** **Vinyet düzeltme**: Kenarlardaki lens kararmasını telafi eder ✅ Önerilir
* **Yansıma kalibrasyonu**: Kalibrasyon hedeflerini kullanarak değerleri normalleştirir ✅ Önerilir
* **Debayer yöntemi**: RAW&#x27;ı 3 kanallı multispektral formata dönüştürmek için kullanılan algoritma
* **Minimum yeniden kalibrasyon aralığı**: Kalibrasyon hedeflerinin kullanılması arasındaki süre (0 = tümünü kullan)**Gelişmiş ayarlar:*** **Işık sensörü saat dilimi kayması**: PPK zaman senkronizasyonu için (varsayılan: 0)
* **PPK düzeltmelerini uygula**: .daq dosyalarındaki GPS/pozlama pimi verilerini kullanır
* **Pozlama Pimi 1/2**: Çift kamera kurulumları için kameraları pozlama pimlerine atar

### Debayer Yöntemi

Şu anda Chloros&#x27;te 2 debayering yöntemi sunuyoruz:

#### Standart (Hızlı, Orta Kalite)

Standart debayer işlemi hızlıdır ancak debayering renk gürültüsü gösterir, bu da daha az doğru ve daha gürültülü görüntülerle sonuçlanır.

#### Doku Duyarlı (Yavaş, En Yüksek Kalite) \[Yalnızca Chloros+]

Doku Duyarlı, neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş yüksek kaliteli kenar duyarlı bir debayer kullanır. Tekstür Duyarlı modelin çalışması için GPU belleği (VRAM) gerekir. Daha hızlı işleme için 4 GB&#x27;den fazla VRAM&#x27;iniz olduğunda bu modeli kullanmanızı öneririz.

### İndeks (Çok Spektral İndeksler)

Hangi bitki örtüsü indekslerinin hesaplanıp dışa aktarılacağını yapılandırın.

**İndekslerin eklenmesi:**

1.**&quot;İndeks ekle&quot;** düğmesine tıklayın
2. Açılır menüden bir endeks seçin (NDVI, NDRE, GNDVI vb.)
3. Görselleştirme ayarlarını yapılandırın (LUT renkleri, değer aralıkları)
4. Gerektiği kadar birden fazla endeks ekleyin

**Popüler endeksler:*** **NDVI**: Genel bitki sağlığı (en yaygın)
* **NDRE**: RedEdge ile erken stres tespiti
* **GNDVI**: Klorofil konsantrasyonuna duyarlı
* **OSAVI**: Görünür toprakla iyi sonuç verir
* **EVI**: Yüksek yaprak alanı indeksi (LAI) bölgeleri**Özel formüller (yalnızca Chloros+):**

* Özel multispektral indeks formülleri oluşturun
* Tüm görüntü kanallarıyla bant matematiği kullanın
* Tekrar kullanmak üzere özel formülleri kaydedin

Mevcut tüm indeksler ve formüller için bkz. [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md).

### Dışa Aktar

Çıktı dosya formatını ve kalitesini kontrol eder.

**Mevcut formatlar:*** **TIFF (16 bit)**: GIS ve bilimsel analizler için önerilir (0-65.535 aralığı)
* **TIFF (32 bit, Yüzde)**: Kayan noktalı yansıma değerleri (0,0-1,0 aralığı)
* **PNG (8 bit)**: Görselleştirme için kayıpsız sıkıştırma (0-255 aralığı)
* **JPG (8 bit)**: En küçük dosyalar, kayıplı sıkıştırma (0-255 aralığı)***

## Ayarları Kaydetme ve Yükleme

### Proje Şablonunu Kaydet

Tutarlı iş akışları için yeniden kullanılabilir şablonlar oluşturun:

1. Proje Ayarları panelinde istediğiniz tüm ayarları yapılandırın
2. Sayfanın altındaki **&quot;Proje Şablonunu Kaydet&quot;** bölümüne gidin
3. Açıklayıcı bir şablon adı girin (ör. &quot;Survey3N\_RGN\_Agriculture&quot;)
4. Kaydet simgesine tıklayın

**Avantajlar:**

* Birden fazla projede aynı ayarları uygulayın
* Yapılandırmaları ekip üyeleriyle paylaşın
* Tekrarlanan anketlerde tutarlılığı koruyun

### Yeni Projeye Şablon Yükleme

Yeni bir proje oluştururken:

1. Ana menüden **&quot;Yeni Proje&quot;**&#x27;yi seçin
2. **&quot;Şablondan yükle&quot;** seçeneğini seçin
3. Kaydettiğiniz şablonu seçin
4. Tüm ayarlar otomatik olarak uygulanır

### Çalışma Dizini

**&quot;Proje Klasörünü Kaydet&quot;** ayarı, yeni projelerin varsayılan olarak nerede oluşturulacağını belirler:

* **Varsayılan konum**: `C:\Users\[Username]\Chloros Projects`
* **Konumu değiştir**: Düzenle simgesine tıklayın ve yeni klasörü seçin
* **Ne zaman değiştirilmeli**:
  * Ekip işbirliği için ağ sürücüsü
  * Daha fazla depolama alanına sahip farklı bir sürücü
  * Yıl/müşteri bazında düzenlenmiş klasör yapısı

***

## PPK (Post-Processed Kinematic) Kurulumu

Hassas coğrafi konum belirleme için GPS özellikli MAPIR DAQ kayıt cihazları kullanılıyorsa:

### Ön Koşullar

* GPS (GNSS) modülüne sahip MAPIR DAQ
* Pozlama pini girişleri içeren .daq günlük dosyası
* Yakalama oturumu sırasında DAQ pozlama pinlerine bağlı kamera

### Yapılandırma Adımları

1. .daq günlük dosyasını proje klasörünüze yerleştirin
2. Proje Ayarları&#x27;nda **&quot;PPK düzeltmelerini uygula&quot;** onay kutusunu etkinleştirin
3. Gerekirse **&quot;Işık sensörü saat dilimi kayması&quot;** ayarını yapın (varsayılan: UTC için 0)
4. Kameraları pozlama pinlerine atayın:
   * **Tek kamera**: Otomatik olarak Pin 1&#x27;e atanır
   * **Çift kamera**: Her kamerayı doğru pime manuel olarak atayın**Pozlama Pini Ataması:*** **Pozlama Pini 1**: Açılır menüden kamera modelini seçin
* **Pozlama Pini 2**: İkinci kamerayı veya &quot;Kullanma&quot; seçeneğini seçin
* Aynı kamera her iki pime de atanamaz

{% hint style="warning" %}
**Önemli**: Pozlama pinleri, ilgili kameralara doğru şekilde atanmalıdır. Yanlış atama, hatalı coğrafi konum verilerine neden olur.
{% endhint %}

***

## Gelişmiş Senaryolar

### Çoklu Kamera Projeleri

Tek bir projede birden fazla MAPIR kameradan gelen görüntüleri işlerken:

1. Chloros her kamera modelini otomatik olarak algılar
2. Her kamera uygun işleme profilini alır
3. PPK: Her kamerayı doğru pozlama pimine manuel olarak atayın
4. Tüm kameralar aynı dışa aktarım formatını ve indeksleri kullanır

**Örnek**: Survey3W RGN + Survey3N OCN çift kameralı kurulum

### Zaman Atlamalı veya Çok Tarihli Ölçümler

Aynı alanın zaman içinde tekrarlanan ölçümleri için:

1. Standart ayarlarınızla bir şablon oluşturun
2. Her oturumda tutarlı kalibrasyon hedefi kurulumunu kullanın
3. Her tarihi ayrı bir proje olarak işleyin
4. Karşılaştırılabilir sonuçlar için aynı ayarları kullanın
5. Zamansal analiz için aynı formatta dışa aktarın

### Büyük Veri Kümeleri

Çok sayıda görüntü içeren projeler için (500+):

* Tarihe veya alana göre daha küçük projelere bölmeyi düşünün
* Daha hızlı sonuçlar için Chloros+ paralel işlemeyi kullanın
* Toplu iş otomasyonu için CLI veya API&#x27;i düşünün
* Hedef algılama süresini azaltmak için minimum yeniden kalibrasyon aralığını ayarlayın

***

## Ayarlarınızı Doğrulama

İşleme başlamadan önce şu temel ayarları gözden geçirin:

* [ ] Dosya Tarayıcı&#x27;da kamera modeli doğru şekilde algılandı
* [ ] Vinyet düzeltmesi etkinleştirildi
* [ ] Yansıma kalibrasyonu etkinleştirildi
* [ ] En az bir kalibrasyon hedef görüntüsü içe aktarıldı
* [ ] İstenen multispektral indeksler eklendi
* [ ] İş akışınıza uygun dışa aktarma biçimi
* [ ] PPK ayarları yapılandırıldı (pozlama olaylarıyla .daq kullanılıyorsa)

***

## Sonraki Adımlar

Ayarlarınız yapılandırıldıktan sonra:

1. **Kalibrasyon hedef görüntülerini işaretleyin** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
2. **İşlemeyi başlatın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
3. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)

Mevcut tüm ayarlarla ilgili ayrıntılı bilgi için [Proje Ayarları](../project-settings/project-settings.md) referans belgelerine bakın.
