# Proje Ayarlarını Düzenleme

Görüntülerinizi işlemeden önce, iş akışı gereksinimlerinize uygun şekilde proje ayarlarınızı yapılandırmanız önemlidir. Proje Ayarları <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> paneli, kalibrasyon, işleme seçenekleri, multispektral indeksler ve dışa aktarım formatları üzerinde kapsamlı kontrol sağlar.

## Proje Ayarlarına Erişme

1. Projenizi Chloros&#x27;te açın
2. Sol kenar çubuğundaki **Proje Ayarları** <img src="../.gitbook/assets/icon_project-settings.JPG" alt="" data-size="line"> simgesine tıklayın
3. Proje Ayarları paneli tüm yapılandırma seçeneklerini görüntüler

{% hint style=&quot;info&quot; %}
**Ayarlar, projenizle birlikte otomatik olarak kaydedilir**. Bir projeyi yeniden açtığınızda, tüm ayarlar geri yüklenir.
{% endhint %}

***

## Yaygın İş Akışları için Hızlı Kurulum

### Varsayılan Ayarlar (Çoğu Kullanıcı için Önerilir)

Tipik MAPIR Survey3 kamera iş akışları için varsayılan ayarlar iyi sonuç verir:

* ✅ **Vinyet düzeltme**: Etkin
* ✅ **Yansıtma kalibrasyonu**: Etkin (MAPIR hedeflerinin görüntüleri gerekir)
* ✅ **Debayer yöntemi**: Yüksek Kalite (Daha Hızlı)
* ✅ **Dışa aktarma biçimi**: TIFF (16 bit)

Görüntülerinizi içe aktarın ve bu varsayılan ayarlarla işlemeyi başlatın.

***

## Proje Ayarlarına Genel Bakış

Proje Ayarları paneli birkaç kategoriye ayrılmıştır. Aşağıda her bölümün özeti bulunmaktadır. Tam dokümantasyon için [Proje Ayarları](../project-settings/project-settings.md) bölümüne bakın.

### Hedef Algılama

Chloros&#x27;in görüntülerinizdeki kalibrasyon hedeflerini nasıl tanımladığını kontrol eder.

**Anahtar ayarlar:**

* **Minimum kalibrasyon örnek alanı**: Hedef algılama için boyut eşiği (varsayılan: 25 piksel)
* **Minimum hedef kümeleme**: Hedef bölgeleri gruplandırmak için benzerlik eşiği (varsayılan: 60)

**Ne zaman ayarlanmalı:**

* Yanlış algılamalar varsa örnek alanını artırın.
* Hedefler algılanmıyorsa azaltın.
* Hedefler birden fazla algılamaya bölünüyorsa kümelemeyi ayarlayın.

### İşleme

Ana görüntü işleme ve kalibrasyon seçenekleri.

**Önemli ayarlar:**

* **Vinyet düzeltme**: Kenarlarda lens karartmasını telafi eder ✅ Önerilir
* **Yansıma kalibrasyonu**: Kalibrasyon hedeflerini kullanarak değerleri normalleştirir ✅ Önerilir
* **Debayer yöntemi**: RAW&#x27;ı 3 kanallı multispektral&#x27;e dönüştürmek için algoritma
* **Minimum yeniden kalibrasyon aralığı**: Kalibrasyon hedeflerinin kullanılması arasındaki süre (0 = tümünü kullan)

**Gelişmiş ayarlar:**

* **Işık sensörü saat dilimi ofseti**: PPK zaman senkronizasyonu için (varsayılan: 0)
* **PPK düzeltmelerini uygula**: .daq dosyalarından GPS/pozlama pimi verilerini kullanır
* **Pozlama Pimi 1/2**: Çift kamera kurulumları için kameraları pozlama pimlerine atar

### Endeks (Çok Spektral Endeksler)

Hangi bitki örtüsü endekslerinin hesaplanıp dışa aktarılacağını yapılandırın.

**Endeks ekleme:**

1. **&quot;Endeks ekle&quot;** düğmesini tıklayın
2. Açılır menüden bir endeks seçin (NDVI, NDRE, GNDVI vb.)
3. Görselleştirme ayarlarını yapılandırın (LUT renkleri, değer aralıkları)
4. Gerektiği kadar birden fazla endeks ekleyin

**Popüler endeksler:**

* **NDVI**: Genel bitki sağlığı (en yaygın)
* **NDRE**: RedEdge ile erken stres tespiti
* **GNDVI**: Klorofil konsantrasyonuna duyarlı
* **OSAVI**: Görünür toprakla iyi çalışır
* **EVI**: Yüksek yaprak alanı indeksi (LAI) bölgeleri

**Özel formüller (yalnızca Chloros+):**

* Özel multispektral indeks formülleri oluşturun
* Tüm görüntü kanallarında bant matematiği kullanın
* Yeniden kullanım için özel formülleri kaydedin

Mevcut tüm indeksler ve formüller için [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md) bölümüne bakın.

### Dışa Aktarma

Çıktı dosyası formatını ve kalitesini kontrol eder.

**Mevcut formatlar:**

* **TIFF (16 bit)**: GIS ve bilimsel analiz için önerilir (0-65.535 aralığı)
* **TIFF (32 bit, Yüzde)**: Kayan nokta yansıma değerleri (0,0-1,0 aralığı)
* **PNG (8 bit)**: Görselleştirme için kayıpsız sıkıştırma (0-255 aralığı)
* **JPG (8 bit)**: En küçük dosyalar, kayıplı sıkıştırma (0-255 aralığı)

***

## Ayarları Kaydetme ve Yükleme

### Proje Şablonunu Kaydetme

Tutarlı iş akışları için yeniden kullanılabilir şablonlar oluşturun:

1. Proje Ayarları panelinde istediğiniz tüm ayarları yapılandırın.
2. Alt kısımdaki **&quot;Proje Şablonunu Kaydet&quot;** bölümüne gidin.
3. Açıklayıcı bir şablon adı girin (ör. &quot;Survey3N\_RGN\_Agriculture&quot;).
4. Kaydet simgesine tıklayın.

**Avantajlar:**

* Birden fazla projeye aynı ayarları uygulayın.
* Yapılandırmaları ekip üyeleriyle paylaşın.
* Tekrarlanan anketlerde tutarlılığı koruyun.

### Yeni Projeye Şablon Yükleme

Yeni bir proje oluştururken:

1. Ana menüden **&quot;Yeni Proje&quot;** seçeneğini seçin.
2. **&quot;Şablondan yükle&quot;** seçeneğini seçin.
3. Kaydettiğiniz şablonu seçin.
4. Tüm ayarlar otomatik olarak uygulanır.

### Çalışma Dizini

**&quot;Proje Klasörünü Kaydet&quot;** ayarı, yeni projelerin varsayılan olarak nerede oluşturulacağını belirler:

* **Varsayılan konum**: `C:\Users\[Username]\Chloros Projects`
* **Konumu değiştir**: Düzenle simgesine tıklayın ve yeni klasörü seçin
* **Ne zaman değiştirilir**:
  * Ekip işbirliği için ağ sürücüsü
  * Daha fazla depolama alanına sahip farklı bir sürücü
  * Yıllara/müşterilere göre düzenlenmiş klasör yapısı

***

## PPK (Sonrası İşlenmiş Kinematik) Kurulumu

Hassas coğrafi konum belirleme için GPS özellikli MAPIR DAQ kaydediciler kullanılıyorsa:

### Ön Koşullar

* GPS (GNSS) modüllü MAPIR DAQ
* Pozlama pimi girişleri içeren .daq günlük dosyası
* Yakalama oturumu sırasında DAQ pozlama pinlerine bağlı kamera

### Yapılandırma Adımları

1. .daq günlük dosyasını proje klasörünüze yerleştirin.
2. Proje Ayarları&#x27;nda **&quot;PPK düzeltmelerini uygula&quot;** onay kutusunu etkinleştirin.
3. Gerekirse **&quot;Işık sensörü saat dilimi farkı&quot;** ayarını yapın (varsayılan: UTC için 0).
4. Kameraları pozlama pinlerine atayın:
   * **Tek kamera**: Otomatik olarak Pin 1&#x27;e atanır
   * **Çift kamera**: Her kamerayı doğru pime manuel olarak atayın

**Pozlama Pimi Ataması:**

* **Pozlama Pimi 1**: Açılır menüden kamera modelini seçin
* **Pozlama Pimi 2**: İkinci kamerayı veya &quot;Kullanma&quot; seçeneğini seçin
* Aynı kamera her iki pime de atanamaz

{% hint style=&quot;warning&quot; %}
**Önemli**: Pozlama pimleri, ilgili kameralara doğru şekilde atanmalıdır. Yanlış atama, yanlış coğrafi konum verilerine neden olur.
{% endhint %}

***

## Gelişmiş Senaryolar

### Çoklu Kamera Projeleri

Bir projede birden fazla MAPIR kameradan gelen görüntüleri işlerken:

1. Chloros her kamera modelini otomatik olarak algılar
2. Her kamera uygun işleme profilini alır
3. PPK: Her kamerayı doğru pozlama pimine manuel olarak atayın
4. Tüm kameralar aynı dışa aktarma formatını ve indeksleri kullanır

**Örnek**: Survey3W RGN + Survey3N OCN çift kamera donanımı

### Zaman Aralıklı veya Çoklu Tarihli Anketler

Aynı alanın zaman içinde tekrar tekrar anketleri için:

1. Standart ayarlarınızla bir şablon oluşturun
2. Her oturumda tutarlı kalibrasyon hedefi kurulumu kullanın
3. Her tarihi ayrı bir proje olarak işleyin
4. Karşılaştırılabilir sonuçlar için aynı ayarları kullanın
5. Zamansal analiz için aynı formatta dışa aktarın

### Büyük Veri Kümeleri

Çok sayıda görüntü içeren projeler için (500+):

* Tarihe veya alana göre daha küçük projelere bölmeyi düşünün.
* Daha hızlı sonuçlar için Chloros+ paralel işlemeyi kullanın.
* Toplu otomasyon için CLI veya API&#x27;i düşünün.
* Hedef algılama süresini azaltmak için minimum yeniden kalibrasyon aralığını ayarlayın.

***

## Ayarlarınızı Doğrulama

İşlemeye başlamadan önce şu önemli ayarları gözden geçirin:

* [ ] Dosya Tarayıcı&#x27;da kamera modeli doğru algılanmış
* [ ] Vinyet düzeltme etkinleştirilmiş
* [ ] Yansıma kalibrasyonu etkinleştirilmiş
* [ ] En az bir kalibrasyon hedef görüntüsü içe aktarılmış
* [ ] İstenen multispektral indeksler eklenmiş
* [ ] İş akışınıza uygun dışa aktarma biçimi
* [ ] PPK ayarları yapılandırılmış (pozlama olaylarıyla .daq kullanılıyorsa)

***

## Sonraki Adımlar

Ayarlarınız yapılandırıldıktan sonra:

1. **Kalibrasyon hedef görüntülerini işaretleyin** - Bkz. [Hedef Görüntüleri Seçme](choosing-target-images.md)
2. **İşlemeye başlayın** - Bkz. [İşlemeyi Başlatma](starting-the-processing.md)
3. **İlerlemeyi izleyin** - Bkz. [İşlemeyi İzleme](monitoring-the-processing.md)

Kullanılabilir tüm ayarlar hakkında ayrıntılı bilgi için, [Proje Ayarları](../project-settings/project-settings.md) referans belgesine bakın.
