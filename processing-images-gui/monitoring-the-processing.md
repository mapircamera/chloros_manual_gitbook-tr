# İşlemenin İzlenmesi

İşleme başladıktan sonra, Chloros, ilerlemeyi izlemek, sorunları kontrol etmek ve veri kümenizde neler olup bittiğini anlamak için çeşitli yöntemler sunar. Bu sayfada, işlemenizi nasıl takip edeceğiniz ve Chloros tarafından sağlanan bilgileri nasıl yorumlayacağınız açıklanmaktadır.

## İlerleme Çubuğuna Genel Bakış

Üst başlıktaki ilerleme çubuğu, gerçek zamanlı işleme durumunu ve tamamlanma yüzdesini gösterir.

### Ücretsiz Mod İlerleme Çubuğu

Chloros+ lisansı olmayan kullanıcılar için:

**2 Aşamalı İlerleme Göstergesi:**

1.**Hedef Algılama** - Görüntülerde kalibrasyon hedeflerini bulma
2. **İşleme** - Düzeltmelerin uygulanması ve dışa aktarma**İlerleme çubuğu şunları gösterir:**

* Genel tamamlanma yüzdesi (0-100%)
* Mevcut aşama adı
* Basit yatay çubuk görselleştirme

### Chloros+ İlerleme Çubuğu

Chloros+ lisansına sahip kullanıcılar için:

**4 Aşamalı İlerleme Göstergesi:**

1.**Algılama** - Kalibrasyon hedeflerini bulma
2. **Analiz** - Görüntüleri inceleme ve iş akışını hazırlama
3. **Kalibrasyon** - Vinyet ve yansıma düzeltmelerini uygulama
4. **Dışa Aktarma** - İşlenmiş dosyaları kaydetme**Etkileşimli Özellikler:*** **İlerleme çubuğunun üzerine gelin**, genişletilmiş 4 aşamalı paneli görmek için
* **İlerleme çubuğuna tıklayın**, genişletilmiş paneli dondurmak/sabitlemek için
* **Tekrar tıklayın**, dondurmayı kaldırmak ve fareyi uzaklaştırdığınızda otomatik olarak gizlemek için
* Her aşama ayrı ayrı ilerlemeyi gösterir (0-100%)

***

## Her İşleme Aşamasını Anlamak

{% hint style="info" %}
**İş Akışı Mimarisi**: Bu 4 GUI aşaması, [4 iş parçacıklı iş akışına](../processing-architecture/processing-pipeline.md) karşılık gelir. GPU hızlandırmalı sistemlerde, İş Parçacığı 3 (Kalibrasyon), belirli donanımınız için işlemeyi optimize eden [Dinamik Hesaplama Uyumlaştırması](../processing-architecture/dynamic-compute-adaptation.md) özelliğinden yararlanır.
{% endhint %}

### Aşama 1: Algılama (Hedef Algılama)

**Neler oluyor:**

* Chloros, Hedef onay kutusu ile işaretlenmiş görüntüleri tarar
* Bilgisayar görme algoritmaları 4 kalibrasyon panelini tanımlar
* Her panelden yansıma değerleri çıkarılır
* Doğru kalibrasyon planlaması için hedef zaman damgaları kaydedilir

**Süre:**

* İşaretlenmiş hedefler varsa: 10-60 saniye
* İşaretlenmiş hedefler yoksa: 5-30+ dakika (tüm görüntüleri tarar)

**İlerleme göstergesi:**

* Algılama: %0 → %100
* Taranan görüntü sayısı
* Bulunan hedef sayısı

**Dikkat edilmesi gerekenler:**

* Hedefler doğru şekilde işaretlenmişse işlem hızlı bir şekilde tamamlanmalıdır
* Çok uzun sürüyorsa, hedefler işaretlenmemiş olabilir
* Hata Giderme Günlüğünde &quot;Hedef bulundu&quot; mesajlarını kontrol edin

### Aşama 2: Analiz

**Neler oluyor:**

* Görüntü EXIF meta verilerinin okunması (zaman damgaları, pozlama ayarları)
* Hedef zaman damgalarına göre kalibrasyon stratejisi belirleme
* Görüntü işleme kuyruğunu düzenleme
* Paralel işleme işçilerini hazırlama (yalnızca Chloros+)

**Süre:** 5-30 saniye**İlerleme göstergesi:**

* Analiz: %0 → %100
* Hızlı aşama, genellikle çabuk tamamlanır

**Dikkat edilmesi gerekenler:**

* Duraklamalar olmadan istikrarlı bir şekilde ilerlemelidir
* Eksik meta verilerle ilgili uyarılar Hata Giderme Günlüğünde görünecektir

### Aşama 3: Kalibrasyon

**Neler oluyor:*** **Debayering**: RAW Bayer desenini 3 kanala dönüştürme
* **Vinyet düzeltme**: Lens kenarındaki kararmayı giderme
* **Yansıma kalibrasyonu**: Hedef değerlerle normalleştirme
* **İndeks hesaplama**: Çok spektral indeksleri hesaplama
* Her görüntüyü tam iş akışı üzerinden işleme

**Süre:** Toplam işlem süresinin büyük kısmı (60-80%)**İlerleme göstergesi:**

* Kalibrasyon: %0 → %100
* İşlenmekte olan görüntü
* Tamamlanan görüntüler / Toplam görüntü sayısı

**İşleme davranışı:*** **Serbest mod**: Görüntüleri tek tek sırayla işler
* **Chloros+ modu**: Aynı anda en fazla 16 görüntüyü işler
* **GPU hızlandırma**: Bu aşamayı önemli ölçüde hızlandırır**Dikkat edilmesi gerekenler:**

* Görüntü sayısında istikrarlı ilerleme
* Görüntü başına tamamlanma mesajları için Hata Giderme Günlüğünü kontrol edin
* Görüntü kalitesi veya kalibrasyon sorunları hakkında uyarılar

### Aşama 4: Dışa Aktarma

**Neler oluyor:**

* Kalibre edilmiş görüntüleri seçilen formatta diske yazma
* LUT renkleriyle multispektral indeks görüntülerini dışa aktarma
* Kamera modeli alt klasörleri oluşturma
* Uygun son eklerle orijinal dosya adlarını koruma

**Süre:** Toplam işlem süresinin %10-20&#x27;si**İlerleme göstergesi:**

* Dışa aktarma: %0 → %100
* Yazılan dosyalar
* Dışa aktarma biçimi ve hedefi

**Dikkat edilmesi gerekenler:**

* Disk alanı uyarıları
* Dosya yazma hataları
* Yapılandırılan tüm çıktıların tamamlanması

***

## Hata Ayıklama Günlüğü Sekmesi

Hata Ayıklama Günlüğü, işleme ilerlemesi ve karşılaşılan sorunlar hakkında ayrıntılı bilgi sağlar.

### Hata Ayıklama Günlüğüne Erişme

1. Sol kenar çubuğundaki **Hata Ayıklama Günlüğü** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line"> simgesine tıklayın
2. Gerçek zamanlı işleme mesajlarını gösteren günlük paneli açılır
3. En son mesajları göstermek için otomatik olarak kaydırılır

### Günlük Mesajlarını Anlama

#### Bilgi Mesajları (Beyaz/Gri)

Normal işleme güncellemeleri:

```
[INFO] Processing started
[INFO] Target detected in IMG_0015.RAW - 4 panels found
[INFO] Calibrating IMG_0234.RAW
[INFO] Exported NDVI image: IMG_0234_NDVI.tif
[INFO] Processing complete
```

#### Uyarı Mesajları (Sarı)

İşlemeyi durdurmayan kritik olmayan sorunlar:

```
[WARN] No GPS data found in IMG_0145.RAW
[WARN] Target image timestamp gap > 30 minutes
[WARN] Low contrast in calibration panel - results may vary
```

**Eylem:** İşlemden sonra uyarıları inceleyin, ancak işlemi kesintiye uğratmayın

#### Hata Mesajları (Red)

İşlemin başarısız olmasına neden olabilecek kritik sorunlar:

```
[ERROR] Cannot write file - disk full
[ERROR] Corrupted image file: IMG_0299.RAW
[ERROR] No targets detected - enable reflectance calibration or mark target images
```

**Eylem:** İşlemeyi durdurun, hatayı giderin, yeniden başlatın

### Yaygın Günlük Mesajları

| Mesaj                          | Anlam                                | Gerekli Eylem                                         |
| -------------------------------- | -------------------------------------- | ----------------------------------------------------- |
| &quot;\[dosya adı\] içinde hedef algılandı&quot; | Kalibrasyon hedefi başarıyla bulundu  | Yok - normal                                         |
| &quot;Y&#x27;nin X&#x27;inci görüntüsü işleniyor&quot;        | Mevcut ilerleme güncellemesi                | Yok - normal                                         |
| &quot;Hedef bulunamadı&quot;               | Kalibrasyon hedefi algılanmadı        | Hedef görüntüleri işaretleyin veya yansıma kalibrasyonunu devre dışı bırakın |
| &quot;Yeterli disk alanı yok&quot;        | Çıktı için yeterli depolama alanı yok          | Disk alanını boşaltın                                    |
| &quot;Bozuk dosya atlanıyor&quot;        | Görüntü dosyası hasarlı                  | Dosyayı SD karttan yeniden kopyalayın                             |
| &quot;PPK verileri uygulandı&quot;               | .daq dosyasından GPS düzeltmeleri uygulandı | Yok - normal                                         |

### Günlük Verilerini Kopyalama

Sorun giderme veya destek için günlüğü kopyalamak için:

1. Hata Giderme Günlüğü panelini açın
2. **&quot;Günlüğü Kopyala&quot;** düğmesine tıklayın (veya sağ tıklayın → Tümünü Seç)
3. Metin dosyasına veya e-postaya yapıştırın
4. Gerekirse MAPIR destek ekibine gönderin

***

## Sistem Kaynakları İzleme

### CPU Kullanımı

**Serbest Mod:**

* 1 CPU çekirdeği ~%100
* Diğer çekirdekler boşta veya kullanılabilir
* Sistem yanıt vermeye devam eder

**Chloros+ Paralel Mod:**

* Birden fazla çekirdek %80-100 (16 çekirdeğe kadar)
* Genel CPU kullanımı yüksek
* Sistem daha az duyarlı hissedilebilir

**İzlemek için:**

* Windows Görev Yöneticisi (Ctrl+Shift+Esc)
* Performans sekmesi → CPU bölümü
* &quot;Chloros&quot; veya &quot;chloros-backend&quot; işlemlerini arayın

### Bellek (RAM) Kullanımı

**Tipik kullanım:**

* Küçük projeler (&lt; 100 görüntü): 2-4 GB
* Orta ölçekli projeler (100-500 görüntü): 4-8 GB
* Büyük projeler (500+ görüntü): 8-16 GB
* Chloros+ paralel modu daha fazla RAM kullanır

**Bellek yetersizse:**

* Daha küçük gruplar halinde işleyin
* Diğer uygulamaları kapatın
* Düzenli olarak büyük veri kümelerini işliyorsanız RAM&#x27;i yükseltin

### GPU Kullanımı (CUDA ile Chloros+)

GPU hızlandırma etkinleştirildiğinde:

* NVIDIA GPU yüksek kullanım gösterir (60-90%)
* VRAM kullanımı artar (4 GB+ VRAM gerektirir)
* Kalibrasyon aşaması önemli ölçüde daha hızlıdır

**İzlemek için:**

* NVIDIA Sistem Tepsisi simgesi
* Görev Yöneticisi → Performans → GPU
* GPU-Z veya benzer bir izleme aracı

### Disk G/Ç

**Ne beklemeli:**

* Analiz aşamasında yüksek disk okuma
* Dışa aktarma aşamasında yüksek disk yazma
* SSD, HDD&#x27;den önemli ölçüde daha hızlıdır

**Performans ipucu:**

* Mümkün olduğunda proje klasörü için SSD kullanın
* Büyük veri kümeleri için ağ sürücülerinden kaçının
* Diskin kapasitesinin dolmak üzere olmadığından emin olun (yazma hızını etkiler)

***

## İşleme Sırasında Sorunları Tespit Etme

### Uyarı İşaretleri

**İşlem durur (5 dakikadan fazla bir süre boyunca değişiklik olmaz):**

* Hata olup olmadığını görmek için Hata Giderme Günlüğünü kontrol edin
* Kullanılabilir disk alanını kontrol edin
* Chloros&#x27;in çalıştığından emin olmak için Görev Yöneticisi&#x27;ni kontrol edin

**Hata mesajları sık sık görünür:**

* İşlemeyi durdurun ve hataları inceleyin
* Yaygın nedenler: disk alanı, bozuk dosyalar, bellek sorunları
* Aşağıdaki Sorun Giderme bölümüne bakın

**Sistem yanıt vermiyor:**

* Chloros+ paralel modu çok fazla kaynak kullanıyor
* Eşzamanlı görevleri azaltmayı veya donanımı yükseltmeyi düşünün
* Serbest mod daha az kaynak tüketir

### İşlemi Ne Zaman Durdurmalı

Aşağıdakileri görürseniz işlemi durdurun:

* ❌ &quot;Disk dolu&quot; veya &quot;Dosya yazılmıyor&quot; hataları
* ❌ Tekrarlanan görüntü dosyası bozulma hataları
* ❌ Sistem tamamen donmuş (yanıt vermiyor)
* ❌ Yanlış ayarların yapılandırıldığı fark edildi
* ❌ Yanlış görüntüler içe aktarıldı

**Nasıl durdurulur:**

1.**Durdur/İptal düğmesine** tıklayın (Başlat düğmesinin yerine geçer)
2. İşlem durur, ilerleme kaybolur
3. Sorunları giderin ve baştan başlatın

***

## İşlem Sırasında Sorun Giderme

### İşlem Çok Yavaş

**Olası nedenler:**

* İşaretlenmemiş hedef görüntüler (tüm görüntüler taranıyor)
* SSD depolama yerine HDD kullanılması
* Yetersiz sistem kaynakları
* Çok sayıda dizin yapılandırılmış
* Ağ sürücüsüne erişim

**Çözümler:**

1. İşlem yeni başladıysa ve Algılama aşamasındaysa: İptal edin, hedefleri işaretleyin, yeniden başlatın
2. Gelecekte: SSD kullanın, dizin sayısını azaltın, donanımı yükseltin
3. Büyük veri kümelerini toplu olarak işlemek için CLI&#x27;i kullanmayı düşünün

### &quot;Disk Alanı&quot; Uyarıları

**Çözümler:**

1. Hemen disk alanı boşaltın
2. Projeyi daha fazla alana sahip bir sürücüye taşıyın
3. Dışa aktarılacak dizin sayısını azaltın
4. TIFF yerine JPG formatını kullanın (dosyalar daha küçük olur)

### Sık Görülen &quot;Bozuk Dosya&quot; Mesajları

**Çözümler:**

1. Bütünlüğü sağlamak için görüntüleri SD karttan yeniden kopyalayın
2. SD kartta hata olup olmadığını kontrol edin
3. Bozuk dosyaları projeden kaldırın
4. Kalan görüntülerin işlenmesine devam edin

### Sistem Aşırı Isınması / Yavaşlama

**Çözümler:**

1. Yeterli havalandırma sağlayın
2. Bilgisayar havalandırma deliklerinden tozu temizleyin
3. İşleme yükünü azaltın (Chloros+ yerine Free modunu kullanın)
4. Günün daha serin saatlerinde işlemeyi gerçekleştirin

***

## İşleme Tamamlandı Bildirimi

İşleme bittiğinde:

* İlerleme çubuğu %100&#x27;e ulaşır
* **&quot;İşlem Tamamlandı&quot;** mesajı Hata Giderme Günlüğünde görünür
* Başlat düğmesi tekrar etkin hale gelir
* Tüm çıktı dosyaları kamera modeli alt klasöründedir

***

## Sonraki Adımlar

İşlem tamamlandığında:

1. **Sonuçları inceleyin** - Bkz. [İşlemi Tamamlama](finishing-the-processing.md)
2. **Çıktı klasörünü kontrol edin** - Tüm dosyaların doğru şekilde dışa aktarıldığını doğrulayın
3. **Hata Giderme Günlüğünü inceleyin** - Herhangi bir uyarı veya hata olup olmadığını kontrol edin
4. **İşlenmiş görüntüleri önizleyin** - Görüntü Görüntüleyiciyi veya harici bir yazılımı kullanın

İşlenmiş sonuçlarınızı inceleme ve kullanma hakkında bilgi için bkz. [İşlemeyi Tamamlama](finishing-the-processing.md).
