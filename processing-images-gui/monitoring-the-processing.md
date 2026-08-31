# İşlemenin İzlenmesi

İşleme başladıktan sonra, Chloros, ilerlemeyi izlemek, sorunları kontrol etmek ve veri kümenizde neler olup bittiğini anlamak için çeşitli yollar sunar. Bu sayfada, işlemenizi nasıl takip edeceğiniz ve Chloros tarafından sağlanan bilgileri nasıl yorumlayacağınız açıklanmaktadır.

## İlerleme Çubuğuna Genel Bakış

Üst başlıktaki ilerleme çubuğu, gerçek zamanlı işleme durumunu ve tamamlanma yüzdesini gösterir. İlerleme durumu, Server-Sent Events (SSE) aracılığıyla arka uçtan canlı olarak aktarılır; bu sayede çubuk, iş akışının o anda ne yaptığını yansıtır.

### Ücretsiz Mod İlerleme Çubuğu

Chloros+ lisansı olmayan kullanıcılar için:

**2 Aşamalı İlerleme Gösterimi:**

1.**Hedef Algılama** - Görüntülerde kalibrasyon hedeflerini bulma
2. **İşleme** - Düzeltmelerin uygulanması ve dışa aktarma**İlerleme çubuğu şunları gösterir:**

* Genel tamamlanma yüzdesi (0-100%)
* Mevcut aşamanın adı
* Basit yatay çubuk görselleştirme

### Chloros+ İlerleme Çubuğu

Chloros+ lisansına sahip kullanıcılar için:

**4 Aşamalı İlerleme Göstergesi:**

1.**Algılama** - Kalibrasyon hedeflerini bulma
2. **Analiz** - Görüntüleri inceleme ve iş akışını hazırlama
3. **Kalibrasyon** - Vinyet ve yansıma düzeltmelerinin uygulanması
4. **Dışa Aktarma** - İşlenmiş dosyaların kaydedilmesi**Etkileşimli Özellikler:*** Genişletilmiş 4 aşamalı paneli görmek için ilerleme çubuğunun **üzerine gelin*** Genişletilmiş paneli dondurmak/sabitlemek için ilerleme çubuğuna **tıklayın*** Dondurmayı kaldırmak ve fareyi uzaklaştırdığınızda otomatik olarak gizlenmesini sağlamak için **tekrar tıklayın*** Her aşama ayrı ayrı ilerlemeyi gösterir (0-100%)

{% hint style="info" %}
**CLI paritesi**: `chloros-cli process` çalışması sırasında aynı dört iş parçacığı &quot;Algılama&quot;, &quot;Analiz&quot;, &quot;İşleme&quot; ve &quot;Dışa Aktarma&quot; olarak rapor edilirken, `chloros-cli export-status` başka bir terminalden İş Parçacığı-4&#x27;ün canlı dışa aktarma ilerlemesini gösterir. [CLI Referans](../reference/cli-reference.md) bölümüne bakın.
{% endhint %}

***

## Her İşleme Aşamasını Anlama

{% hint style="info" %}
**Pipeline Mimarisi**: Bu 4 GUI aşaması, [4 iş parçacıklı işleme boru hattına](../processing-architecture/processing-pipeline.md) karşılık gelir. GPU hızlandırmalı sistemlerde, İş Parçacığı 3 (Kalibrasyon), donanımınıza özel olarak işlemeyi optimize eden [Dinamik Hesaplama Uyumlaştırması](../processing-architecture/dynamic-compute-adaptation.md) özelliğinden yararlanır.
{% endhint %}

### Aşama 1: Algılama (Hedef Algılama)

**Neler oluyor:**

* Chloros, Hedef onay kutusunu işaretlediğiniz görüntüleri tarar (hiçbiri işaretlenmemişse tüm görüntüler)
* Bilgisayar görme algoritmaları, kalibrasyon panellerini tanımlar
* Her panelden yansıma değerleri çıkarılır
* Doğru kalibrasyon planlaması için hedef zaman damgaları kaydedilir

**Süre:**

* İşaretlenmiş hedefler varsa: 10-60 saniye
* İşaretlenmiş hedefler yoksa: 5-30+ dakika (tüm görüntüleri tarar)

**İlerleme göstergesi:**

* Algılama: 0% → 100%
* Taranan görüntü sayısı (yalnızca gerçekten taranan görüntüleri sayar)
* Bulunan hedef sayısı

**Dikkat edilmesi gerekenler:**

* Hedefler doğru şekilde işaretlenmişse işlem hızlı bir şekilde tamamlanmalıdır
* İşlem çok uzun sürüyorsa, hedefler işaretlenmemiş olabilir
* Hata Giderme Günlüğü&#x27;nde &quot;Hedef bulundu&quot; mesajlarını kontrol edin

### Aşama 2: Analiz

**Neler oluyor:**

* Görüntü EXIF meta verilerinin okunması (zaman damgaları, pozlama ayarları)
* Hedef zaman damgalarına ve mevcut DAQ aşağı yönlü verilere dayalı kalibrasyon stratejisinin belirlenmesi
* Görüntü işleme kuyruğunu düzenleme
* Paralel işleme işçilerini hazırlama (yalnızca Chloros+)

**Süre:** 5-30 saniye**İlerleme göstergesi:**

* Analiz ediliyor: %0 → %100
* Hızlı aşama, genellikle kısa sürede tamamlanır

**Dikkat edilmesi gerekenler:**

* Duraklama olmadan istikrarlı bir şekilde ilerlemelidir
* Eksik meta verilerle ilgili uyarılar Hata Giderme Günlüğünde görünecektir

### Aşama 3: Kalibrasyon

**Neler oluyor:*** **Debayering**: RAW Bayer desenini 3 kanala dönüştürme (LATTICE mono modülleri için atlanır, bir not ile belirtilir)
* **Vinyet düzeltme**: Lens kenarlarında oluşan kararmayı giderme
* **Yansıma kalibrasyonu**: Hedef değerler ve/veya DAQ aşağı akışı ile normalleştirme
* **İndeks hesaplama**: Çok spektral indekslerin hesaplanması
* Her görüntünün tam işleme zincirinden geçirilmesi

**Süre:** Toplam işleme süresinin büyük kısmı (%60-80)**İlerleme göstergesi:**

* Kalibrasyon: 0% → 100%
* İşlenmekte olan görüntü
* İşlem tamamlanan görüntüler / Toplam görüntü sayısı

**İşleme davranışı:*** **Serbest mod**: Her seferinde tek bir görüntüyü sırayla işler
* **Chloros+ modu**: Donanıma uyarlanabilir bir işçi havuzu çalıştırır — GPU sistemlerinde (VRAM&#x27;e göre) 1-4 eşzamanlı işçi, yalnızca CPU&#x27;lu sistemlerde fiziksel çekirdek başına bir işçi (eksi bir). Bkz. [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU hızlandırma**: Bu aşamayı önemli ölçüde hızlandırır**Dikkat edilmesi gerekenler:**

* Görüntü sayısı boyunca istikrarlı ilerleme
* Görüntü başına tamamlanma mesajları için Hata Ayıklama Günlüğünü kontrol edin
* Görüntü kalitesi veya kalibrasyon sorunlarıyla ilgili uyarılar

### Aşama 4: Dışa Aktarma

**Neler oluyor:**

* İşlenmiş görüntüler tamamlandıkça seçilen formatta diske yazılır
* **LATTICE**: her kare, etkinleştirilmiş tüm ürünlere (debayered / önizleme / parlaklık / yansıma) dağıtılır
* LUT renkleriyle multispektral indeks görüntüleri dışa aktarılıyor
* `<project>/<camera>/<format>/<Product>_Images/` çıktı ağacı oluşturuluyor — dışa aktarılan dosyalar kaynak dosya adını korur; klasör ürünü tanımlar

**Süre:** Toplam işleme süresinin %10-20&#x27;si**İlerleme göstergesi:**

* Dışa aktarma: %0 → %100
* Dosyalar yazılıyor
* Dışa aktarma biçimi ve hedefi

**Dikkat edilmesi gerekenler:**

* Disk alanı uyarıları
* Dosya yazma hataları
* Yapılandırılan tüm çıkışların tamamlanması

***

## Hata Ayıklama Günlüğü Sekmesi

Hata Ayıklama Günlüğü, işleme süreci ve karşılaşılan sorunlar hakkında ayrıntılı bilgi sağlar. Arka uç başlatma mesajları da günlük konsoluna aktarılır; bu sayede, günlüğü geç açsanız bile tüm süreci takip edebilirsiniz.

### Hata Ayıklama Günlüğüne Erişme

1. Sol kenar çubuğundaki **Hata Ayıklama Günlüğü**<img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

simgesine tıklayın
2. Gerçek zamanlı işleme mesajlarını gösteren günlük paneli açılır
3. En son mesajları göstermek için otomatik olarak kaydırılır

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written) -->

### Günlük Mesajlarını Anlama

Chloros günlük satırlarının başında, alt sistemi belirten köşeli parantez içindeki etiketler bulunur — örneğin `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Bilinmesi gereken en önemli şey, her çalıştırmanın sonunda (durdurulan çalıştırmalar dahil) yazdırılan **çalıştırma özetidir**:

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Açıklama gerektiren durumlarda — örneğin hiçbir sonuç üretmeyen bir çalıştırma veya istenen ürününün uygulanamaz olduğu için atlanan bir kamera — ek `[RUN-SUMMARY]` ipucu satırları gelir. `[EXPORT-CHECK]` satırları, kamera bazında atlamaları açıklar (örneğin, bir RGB kamerasının neden radyans ürünü alamadığını).

Genel mesaj ciddiyet seviyeleri (aşağıdaki örnekler açıklayıcı niteliktedir, kelimesi kelimesine alıntı değildir):

#### Bilgi Mesajları (Beyaz/Gri)

Normal işleme güncellemeleri: işleme başladı, hedefler algılandı (panel sayıları ile birlikte), görüntü başına kalibrasyon ilerlemesi, dosyalar dışa aktarıldı, işleme tamamlandı.

#### Uyarı Mesajları (Sarı)

İşlemeyi durdurmayan, kritik olmayan sorunlar — örneğin, bir karede eksik GPS verileri, hedef görüntüler arasında büyük bir zaman damgası farkı veya bir kalibrasyon panelinde düşük kontrast.

**Eylem:** İşleme tamamlandıktan sonra uyarıları inceleyin, ancak işlemi kesmeyin

#### Hata Mesajları (Red)

İşlemin başarısız olmasına neden olabilecek kritik sorunlar — ör. disk dolu, bozuk bir görüntü dosyası veya yansıma kalibrasyonu talep edilirken hedef algılanmaması.

**Eylem:** İşlemi durdurun, hatayı giderin, yeniden başlatın

### Yaygın Günlük Durumları

| Durum                             | Anlam                                       | Gerekli Eylem                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| \[dosya adı\] içinde hedef algılandı        | Kalibrasyon hedefi başarıyla bulundu         | Yok - normal                                         |
| Görüntü başına ilerleme çizgileri              | Mevcut ilerleme güncellemesi                       | Yok - normal                                         |
| Hedef bulunamadı                      | Kalibrasyon hedefi algılanmadı               | Hedef görüntüleri işaretleyin veya yansıma kalibrasyonunu devre dışı bırakın |
| Yetersiz disk alanı               | Çıktı için yeterli depolama alanı yok                 | Disk alanını boşaltın                                    |
| Bozuk dosya atlanıyor               | Görüntü dosyası hasarlı                         | Dosyayı SD karttan yeniden kopyalayın                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Ham kare içermeyen bir çekim işlenemez | Ham kare ile yeniden çekim yapın veya CLI `--input-level` komutunu kullanın  |
| `[RUN-SUMMARY] ... 0 file(s) written` | İşlem sonucunda görüntü ürünü elde edilemedi — ipuçlarıyla birlikte bir hata olarak bildirildi | İpucu satırlarını okuyun; nelerin atlandığını ve nedenini kontrol edin |

### Günlük Verilerini Kopyalama

Sorun giderme veya destek amacıyla günlüğü kopyalamak için:

1. Hata Ayıklama Günlüğü panelini açın
2. **&quot;Günlüğü Kopyala&quot;** düğmesine tıklayın (veya sağ tıklayın → Tümünü Seç)
3. Metin dosyasına veya e-postaya yapıştırın
4. Gerekirse MAPIR destek ekibine gönderin

***

## Sistem Kaynakları İzleme

### CPU Kullanımı

**Serbest Mod:**

* 1 CPU çekirdeği ~%100 seviyesinde
* Diğer çekirdekler boşta veya kullanılabilir durumda
* Sistem yanıt vermeye devam eder

**Chloros+ Paralel Mod:**

* Birden fazla çekirdek yüksek kullanım oranında — kaç tane olduğu, [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) tarafından seçilen stratejiye bağlıdır
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

* NVIDIA GPU&#x27;da yüksek kullanım oranı görülür (%60-90)
* VRAM kullanımı artar (4 GB+ VRAM gerektirir; eşzamanlı Texture Aware debayering için 7 GB+)
* Kalibrasyon aşaması önemli ölçüde daha hızlıdır

**İzlemek için:**

* NVIDIA Sistem Tepsisi simgesi
* Görev Yöneticisi → Performans → GPU
* GPU-Z veya benzeri izleme aracı

### Disk G/Ç

**Ne beklemeli:**

* Analiz aşamasında yüksek disk okuma
* Dışa aktarma aşamasında yüksek disk yazma
* SSD, HDD&#x27;den önemli ölçüde daha hızlıdır

**Performans ipucu:**

* Mümkün olduğunda proje klasörü için SSD kullanın
* Büyük veri kümeleri için ağ sürücülerinden kaçının
* Disk kapasitesinin dolmak üzere olmadığından emin olun (yazma hızını etkiler)

***

## İşleme Sırasında Sorunları Tespit Etme

### Uyarı İşaretleri

**İlerleme durur (5 dakikadan fazla süre boyunca değişiklik olmaz):**

* Hata olup olmadığını görmek için Hata Giderme Günlüğünü kontrol edin
* Kullanılabilir disk alanını doğrulayın
* Görev Yöneticisi&#x27;ni kontrol ederek Chloros&#x27;in çalıştığından emin olun

**Hata mesajları sık sık görünüyor:**

* İşlemeyi durdurun ve hataları inceleyin
* Yaygın nedenler: disk alanı, bozuk dosyalar, bellek sorunları
* Aşağıdaki Sorun Giderme bölümüne bakın

**Sistem yanıt vermiyor:**

* Chloros+ paralel modu çok fazla kaynak tüketiyor
* Eşzamanlı görev sayısını azaltmayı veya donanımı yükseltmeyi düşünün
* Serbest mod, daha az kaynak tüketir

### İşlemi Ne Zaman Durdurmalısınız?

Aşağıdakileri görürseniz işlemi durdurun:

* ❌ &quot;Disk dolu&quot; veya &quot;Dosyaya yazılamıyor&quot; hataları
* ❌ Tekrarlanan görüntü dosyası bozulma hataları
* ❌ Sistem tamamen donmuş (yanıt vermiyor)
* ❌ Yanlış ayarların yapıldığını fark ettiyseniz
* ❌ Yanlış görüntüler içe aktarıldıysa

**Nasıl durdurulur:**

1.**Durdur düğmesine** tıklayın (Başlat düğmesinin yerine geçer) — bir kez tıklamak yeterlidir
2. İşlemdeki görüntü tamamlanana kadar çubukta &quot;Durduruluyor...&quot; yazısı görünür, ardından işlem durdurulmuş durumda sona erer
3. Zaten dışa aktarılmış ürünler diskte kalır; günlük dosyasında tamamlanan işlemlerin doğru bir özeti (`[RUN-SUMMARY]`) yazdırılır
4. Sorunları giderin ve yeniden başlatın — işlem baştan başlar

***

## İşleme Sırasında Sorun Giderme

### İşleme Çok Yavaş

**Olası nedenler:**

* İşlenecek hedef görüntüler işaretlenmemiş (tüm görüntüler taranıyor)
* SSD yerine HDD depolama kullanılıyor
* Yetersiz sistem kaynakları
* Çok sayıda dizin yapılandırılmış
* Ağ sürücüsüne erişim

**Çözümler:**

1. İşlem yeni başlamışsa ve &quot;Algılama&quot; aşamasındaysa: Durdurun, hedefleri işaretleyin, yeniden başlatın
2. Gelecekte: SSD kullanın, dizin sayısını azaltın, donanımı yükseltin
3. Büyük veri kümelerini toplu olarak işlemek için CLI&#x27;i değerlendirin

### &quot;Disk Alanı&quot; Uyarıları

**Çözümler:**

1. Hemen disk alanı boşaltın
2. Projeyi daha fazla alana sahip bir sürücüye taşıyın
3. Dışa aktarılacak dizin sayısını azaltın
4. İhtiyacınız olmayan LATTICE dışa aktarma ürünlerini devre dışı bırakın (Proje Ayarları → İşleme)
5. TIFF yerine JPG formatını kullanın (dosyalar daha küçük olur)

### Sık Görülen &quot;Bozuk Dosya&quot; Mesajları

**Çözümler:**

1. Bütünlüğü sağlamak için görüntüleri SD karttan yeniden kopyalayın
2. SD kartta hata olup olmadığını kontrol edin
3. Bozuk dosyaları projeden kaldırın
4. Kalan görüntülerin işlenmesine devam edin

### Sistemin Aşırı Isınması / Hız Kısıtlaması

**Çözümler:**

1. Yeterli havalandırma sağlayın
2. Bilgisayar havalandırma deliklerinden tozu temizleyin
3. İşleme yükünü azaltın (Chloros+ yerine Serbest modu kullanın)
4. Günün daha serin saatlerinde işlemeyi gerçekleştirin

***

## İşleme Tamamlandı Bildirimi

İşleme bittiğinde:

* İlerleme çubuğu %100&#x27;e ulaşır
* Hata Ayıklama Günlüğünde son sayılarla birlikte `[RUN-SUMMARY]` satırları görünür
* Başlat düğmesi tekrar etkin hale gelir
* Tüm çıktı dosyaları, projenin kamera başına çıktı ağacında bulunur: `<project>/<camera>/<format>/<Product>_Images/`

***

## Sonraki Adımlar

İşleme tamamlandığında:

1. **Sonuçları inceleyin** - Bkz. [İşlemeyi Tamamlama](finishing-the-processing.md)
2. **Çıkış klasörünü kontrol edin** - Tüm dosyaların doğru şekilde dışa aktarıldığını doğrulayın
3. **Hata Ayıklama Günlüğünü inceleyin** - Herhangi bir uyarı veya hata olup olmadığını kontrol edin
4. **İşlenmiş görüntüleri önizleyin** - Görüntü Görüntüleyicisini veya harici bir yazılımı kullanın

İşlenmiş sonuçlarınızı inceleme ve kullanma hakkında bilgi için [İşlemeyi Tamamlama](finishing-the-processing.md) bölümüne bakın.
