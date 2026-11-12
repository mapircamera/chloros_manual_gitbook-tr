# İşlemenin İzlenmesi

İşleme başladıktan sonra, Chloros, ilerlemeyi izlemek, sorunları kontrol etmek ve veri kümenizde neler olup bittiğini anlamak için çeşitli yollar sunar. Bu sayfada, işlemenizi nasıl takip edeceğiniz ve Chloros’un sağladığı bilgileri nasıl yorumlayacağınız açıklanmaktadır.

## İlerleme Çubuğuna Genel Bakış

Üst başlıktaki ilerleme çubuğu, gerçek zamanlı işleme durumunu ve tamamlanma yüzdesini gösterir. İlerleme, Server-Sent Events (SSE) aracılığıyla arka uçtan canlı olarak aktarılır; bu sayede çubuk, iş akışının o anda ne yaptığını yansıtır.

### Ücretsiz Mod İlerleme Çubuğu

Chloros+ lisansı olmayan kullanıcılar için:

**2 Aşamalı İlerleme Göstergesi:**

1.**Hedef Algılama** - Görüntülerde kalibrasyon hedeflerini bulma
2. **İşleme** - Düzeltmelerin uygulanması ve dışa aktarma**İlerleme çubuğu şunları gösterir:**

* Genel tamamlanma yüzdesi (0-100%)
* Mevcut aşama adı
* Basit yatay çubuk görselleştirmesi

### Chloros+ İlerleme Çubuğu

Chloros+ lisansına sahip kullanıcılar için:

**4 Aşamalı İlerleme Göstergesi:**

1.**Algılama** - Kalibrasyon hedeflerini bulma
2. **Analiz** - Görüntüleri inceleme ve iş akışını hazırlama
3. **Kalibrasyon** - Vinyet ve yansıma düzeltmelerinin uygulanması
4. **Dışa Aktarma** - İşlenmiş dosyaların kaydedilmesi

**Etkileşimli Özellikler:**

* Genişletilmiş 4 aşamalı paneli görmek için ilerleme çubuğunun **üzerine gelin**
* Genişletilmiş paneli dondurmak/sabitlemek için ilerleme çubuğuna **tıklayın**
* **Tekrar tıklayın**; dondurmayı kaldırın ve fareyi uzaklaştırdığınızda panel otomatik olarak gizlensin
* Her aşama ayrı ayrı ilerlemeyi gösterir (0-100%)

{% hint style="info" %}
**CLI'si uyumu**: `chloros-cli process` çalıştırması sırasında aynı dört iş parçacığı "Algılama", "Analiz", "İşleme" ve "Dışa Aktarma" olarak raporlanır; `chloros-cli export-status` ise başka bir terminalden İş Parçacığı-4'ün dışa aktarma ilerlemesini canlı olarak gösterir. [CLI Kılavuzu](../reference/cli-reference.md) bölümüne bakın.
{% endhint %}

***

## Her İşleme Aşamasını Anlama

{% hint style="info" %}
**Pipeline Mimarisi**: Bu 4 GUI aşaması, [4 iş parçacıklı işleme boru hattına](../processing-architecture/processing-pipeline.md) karşılık gelir. GPU hızlandırması olan sistemlerde, 3. İş Parçacığı (Kalibrasyon), donanımınıza özel olarak işlemeyi optimize eden [Dinamik Hesaplama Uyumlaştırması](../processing-architecture/dynamic-compute-adaptation.md) özelliğinden yararlanır.
{% endhint %}

### Aşama 1: Algılama (Hedef Algılama)

**Neler oluyor:**

* Chloros, Hedef onay kutusunu işaretlediğiniz görüntüleri tarar (hiçbiri işaretlenmemişse tüm görüntüler taranır)
* Bilgisayar görme algoritmaları, kalibrasyon panellerini tanımlar
* Her panelden yansıma değerleri çıkarılır
* Doğru kalibrasyon planlaması için hedef zaman damgaları kaydedilir

**Süre:**

* İşaretlenmiş hedefler varsa: 10-60 saniye
* İşaretlenmiş hedefler yoksa: 5-30+ dakika (tüm görüntüleri tarar)

**İlerleme göstergesi:**

* Algılama: %0 → %100
* Taranan görüntü sayısı (yalnızca gerçekten taranan görüntüleri sayar)
* Bulunan hedef sayısı

**Dikkat edilmesi gerekenler:**

* Hedefler doğru şekilde işaretlenmişse işlem hızlı bir şekilde tamamlanmalıdır
* İşlem çok uzun sürüyorsa, hedefler işaretlenmemiş olabilir
* Hata Giderme Günlüğü'nde "Hedef bulundu" mesajlarını kontrol edin

### Aşama 2: Analiz

**Neler oluyor:**

* Görüntü EXIF meta verilerinin okunması (zaman damgaları, pozlama ayarları)
* Hedef zaman damgaları ve mevcut DAQ aşağı doğru akış verilerine dayalı olarak kalibrasyon stratejisinin belirlenmesi
* Görüntü işleme kuyruğunun düzenlenmesi
* Paralel işleme işçilerinin hazırlanması (Yalnızca Chloros+)

**Süre:** 5-30 saniye**İlerleme göstergesi:**

* Analiz ediliyor: %0 → %100
* Hızlı bir aşamadır, genellikle kısa sürede tamamlanır

**Dikkat edilmesi gerekenler:**

* Duraklamalar olmadan istikrarlı bir şekilde ilerlemelidir
* Eksik meta verilerle ilgili uyarılar Hata Giderme Günlüğü'nde görünecektir

### Aşama 3: Kalibrasyon

**Neler oluyor:**

* **Debayering**: RAW Bayer desenini 3 kanala dönüştürme (LATTICE mono modülleri için atlanır, bir not ile birlikte)
* **Vinyet düzeltme**: Lens kenarlarında oluşan kararmaların giderilmesi
* **Yansıtma kalibrasyonu**: Hedef değerler ve/veya DAQ aşağı yönlü akışı ile normalleştirme
* **İndeks hesaplama**: Multispektral indekslerin hesaplanması
* Her görüntünün tam işleme zinciri üzerinden işlenmesi

**Süre:** Toplam işleme süresinin büyük kısmı (%60-80)**İlerleme göstergesi:**

* Kalibrasyon: %0 → %100
* İşlenmekte olan görüntü
* İşlem tamamlanan görüntüler / Toplam görüntü sayısı

**İşleme davranışı:**

* **Serbest mod**: Görüntüleri teker teker sırayla işler
* **Chloros+ modu**: Donanıma uyarlanabilir bir işçi havuzu çalıştırır — GPU sistemlerinde (VRAM'e göre) 1-4 eşzamanlı işçi, yalnızca CPU'lu sistemlerde fiziksel çekirdek başına bir işçi (eksi bir). Bkz. [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md)
* **GPU hızlandırma**: Bu aşamayı önemli ölçüde hızlandırır**Dikkat edilmesi gerekenler:**

* Görüntü sayısında istikrarlı ilerleme
* Görüntü başına tamamlanma mesajları için Hata Ayıklama Günlüğünü kontrol edin
* Görüntü kalitesi veya kalibrasyon sorunlarına ilişkin uyarılar

### Aşama 4: Dışa Aktarma

**Neler oluyor:**

* İşlenmiş görüntüler tamamlandıkça seçilen formatta diske yazılır
* **LATTICE**: her kare, etkinleştirilmiş tüm ürünlere (debayering / önizleme / parlaklık / yansıma) dağıtılır
* LUT renkleriyle multispektral indeks görüntüleri dışa aktarılıyor
* `<project>/<camera>/<format>/<Product>_Images/` çıktı ağacı oluşturuluyor — dışa aktarılan dosyalar kaynak dosya adını korur; klasör ürünü tanımlar

**Süre:** Toplam işleme süresinin %10-20'si**İlerleme göstergesi:**

* Dışa aktarma: %0 → %100
* Dosyalar yazılıyor
* Dışa aktarma biçimi ve hedefi

**Dikkat edilmesi gerekenler:**

* Disk alanı uyarıları
* Dosya yazma hataları
* Yapılandırılan tüm çıktıların tamamlanması

***

## Hata Ayıklama Günlüğü Sekmesi

Hata Ayıklama Günlüğü, işleme ilerlemesi ve karşılaşılan sorunlar hakkında ayrıntılı bilgi sağlar. Arka uç başlatma mesajları da günlük konsoluna aktarılır; bu sayede, günlüğü geç açsanız bile tüm süreci takip edebilirsiniz.

### Hata Ayıklama Günlüğüne Erişme

1. Sol kenar çubuğundaki **Hata Ayıklama Günlüğü** <img src="../.gitbook/assets/icon_log.JPG" alt="" data-size="line">

simgesine tıklayın
2. Gerçek zamanlı işleme mesajlarını gösteren günlük paneli açılır
3. En son mesajları göstermek için otomatik olarak kaydırılır

<!-- SCREENSHOT-NEEDED: Debug Log tab open at the end of a completed run, showing real backend log lines including the [RUN-SUMMARY] lines (images / camera groups / targets / calibrated / files written), with the 1.3.0 bug icon (Report a bug) visible at the top of the tab -->

### Günlük Mesajlarını Anlama

Chloros'un günlük satırlarının başında, alt sistemi belirten köşeli parantez içindeki etiketler bulunur — örneğin `[PROCESSING]`, `[RUN-SUMMARY]`, `[LATTICE-EXPORT]`, `[EXPORT-CHECK]`, `[IMPORT-LEVEL]`. Bilinmesi gereken en önemli şey, her çalıştırmanın sonunda (durdurulan çalıştırmalar dahil) yazdırılan **çalıştırma özeti**'dir:

```
[RUN-SUMMARY] 49 image(s) in 2 camera group(s); 4 target(s) detected; 45 image(s) calibrated; 180 file(s) written.
```

Açıklama gerektiren durumlarda (örneğin, hiçbir sonuç üretmeyen bir çalıştırma veya ya da istenen ürünün uygun olmadığı için atlandığı bir kamera gibi. `[EXPORT-CHECK]` satırları, kamera bazında atlamaları açıklar (örneğin, bir RGB kameranın neden radyans ürünü alamadığını).

Genel mesaj ciddiyet seviyeleri (aşağıdaki örnekler açıklayıcı niteliktedir, kelimesi kelimesine alıntı değildir):

#### Bilgi Mesajları (Beyaz/Gri)

Normal işleme güncellemeleri: işleme başladı, hedefler algılandı (panel sayıları ile birlikte), görüntü başına kalibrasyon ilerlemesi, dosyalar dışa aktarıldı, işleme tamamlandı.

#### Uyarı Mesajları (Sarı)

İşlemeyi durdurmayan, kritik olmayan sorunlar — ör. bir karede eksik GPS verileri, hedef görüntüler arasında büyük zaman damgası farkı veya bir kalibrasyon panelinde düşük kontrast.

**Eylem:** İşleme tamamlandıktan sonra uyarıları inceleyin, ancak işlemi kesmeyin

#### Hata Mesajları (RedRed

)

İşlemin başarısız olmasına neden olabilecek kritik sorunlar — örneğin, disk dolu, bozuk bir görüntü dosyası veya yansıma kalibrasyonu talep edilirken hiçbir hedef algılanmaması.

**Eylem:** İşlemi durdurun, hatayı giderin, yeniden başlatın

### Yaygın Günlük Durumları

| Durum                             | Anlam                                       | Gerekli Eylem                                         |
| ------------------------------------- | --------------------------------------------- | ----------------------------------------------------- |
| \[dosya adı] içinde hedef algılandı        | Kalibrasyon hedefi başarıyla bulundu         | Yok - normal                                         |
| Görüntü başına ilerleme çizgileri              | Mevcut ilerleme güncellemesi                       | Yok - normal                                         |
| Hedef bulunamadı                      | Kalibrasyon hedefi algılanmadı               | Hedef görüntüleri işaretleyin veya yansıma kalibrasyonunu devre dışı bırakın |
| Yetersiz disk alanı               | Çıktı için yeterli depolama alanı yok                 | Disk alanını boşaltın                                    |
| Bozuk dosya atlanıyor               | Görüntü dosyası hasarlı                         | Dosyayı SD karttan yeniden kopyalayın                             |
| `[IMPORT-LEVEL] Skipping ... no raw source` | Raw karesi olmayan bir çekim işlenemez | Raw ile yeniden çekim yapın, veya CLI kullanın `--input-level`  |
| `[RUN-SUMMARY] ... 0 file(s) written` | İşlem sonucunda görüntü ürünü üretilmedi — ipuçlarıyla birlikte hata olarak bildirildi | İpucu satırlarını okuyun; nelerin atlandığını ve nedenini kontrol edin |
| `[PROCESS] refused: The project folder is too deep for this computer…` | Windows'ta uzun yol özelliği kapalı ve proje klasörü 260 karakterlik yol sınırı için çok derin; hiçbir şey işlenmedi | Daha kısa bir proje klasörü kullanın (mesajda sığabilecek en uzun yol belirtilmiştir) veya Windows'ta uzun yol özelliğini etkinleştirin ve Chloros'u yeniden başlatın — bkz. [İşlemeyi Başlatma](starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer) |
| `[unmix-clamp-mask] …` | Bir LATTICE ürününün kelepçe maskesi yazılamadı; ürünün kendisi sorunsuz ve şunu belirtiyor: `Chloros:UnmixClampMask=unavailable` | Disk alanını ve klasör izinlerini kontrol edin, ardından maskeye ihtiyacınız varsa işlemi yeniden gerçekleştirin |

### Günlük Verilerini Kopyalama

Sorun giderme veya destek amacıyla günlüğü kopyalamak için:

1. Hata Ayıklama Günlüğü panelini açın
2. **Kopyala**düğmesine tıklayın (günlüğü panoya kopyalar) veya**İndir** düğmesine tıklayarak metin dosyası olarak kaydedin
3. Metin dosyasına veya e-postaya yapıştırın
4. Gerekirse MAPIR destek ekibine gönderin

### Günlük sekmesinden hata bildirme

1.3.0 sürümündeki yenilik: Hata Giderme Günlüğü sekmesinin üst kısmındaki hata simgesi (**Hata bildir**), raporu doğrudan MAPIR'e gönderir.

1. Hata Giderme Günlüğü sekmesinin üst kısmındaki hata simgesine tıklayın
2. Sorunu açıklayın: ne yapıyordunuz, ne bekliyordunuz ve bunun yerine ne oldu (gerekli)
3. İsteğe bağlı olarak **Birkaç resim ekle (en fazla 10)**seçeneğini işaretleyin ve açık projeden resimler seçin veya diğer resim dosyaları için**Gözat...** seçeneğini kullanın
4. **Gönder**'e tıklayın

Rapor, açıklamanızı, eklediğiniz resimleri, Chloros günlüklerini ve sistem ayrıntılarını (Chloros sürümü, işletim sistemi, GPU ve proje ayarları) içerir. Rapor göndermek için Chloros'ta (Kullanıcı sekmesi) oturum açmış olmanız gerekir. Gönderim sırasında pencereyi kapatabilirsiniz; işlem bittiğinde, günlüğe `Bug report sent. Reference: BR-…` gibi bir referans yazılır — MAPIR ile iletişime geçtiğinizde bu referansı belirtin. Rapor yüklenemezse (örneğin, internet bağlantısı yoksa), Chloros raporu bilgisayarınıza kaydeder ve yolu gösterir; böylece raporu support@mapir.camera adresine e-posta ile gönderebilirsiniz.

***

## Sistem Kaynakları İzleme

### CPU Kullanımı

**Serbest Mod:**

* 1 CPU çekirdeği ~%100 kullanımda
* Diğer çekirdekler boşta veya kullanılabilir durumda
* Sistem yanıt vermeye devam eder

**Chloros+ Paralel Mod:**

* Birden fazla çekirdek yüksek kullanımda — kaç tane olacağı [Dinamik Hesaplama Uyumlaştırması](../processing-architecture/dynamic-compute-adaptation.md) tarafından seçilen stratejiye bağlıdır
* Sistem daha az duyarlı hissedilebilir

**İzlemek için:**

* Windows Görev Yöneticisi (Ctrl+Shift+Esc)
* Performans sekmesi → CPU bölümü
* "Chloros" veya "chloros-backend" işlemlerini arayın

### Bellek (RAM) Kullanımı

**Tipik kullanım:**

* Küçük projeler (< 100 görüntü): 2-4 GB
* Orta ölçekli projeler (100-500 görüntü): 4-8 GB
* Büyük projeler (500+ görüntü): 8-16 GB
* Chloros+ paralel modu daha fazla RAM kullanır

**Bellek yetersizse:**

* Daha küçük gruplar halinde işleyin
* Diğer uygulamaları kapatın
* Düzenli olarak büyük veri kümelerini işliyorsanız RAM'i yükseltin

### GPU Kullanımı (CUDA ile Chloros+)

GPU hızlandırma etkinleştirildiğinde:

* NVIDIA GPU'da yüksek kullanım oranı görülür (%60-90)
* VRAM kullanımı artar (4 GB+ VRAM gerektirir; eşzamanlı Texture Aware debayering için 7 GB+)
* Kalibrasyon aşaması önemli ölçüde daha hızlıdır

**İzlemek için:**

* NVIDIA Sistem Tepsisi simgesi
* Görev Yöneticisi → Performans → GPU
* GPU-Z veya benzeri bir izleme aracı

### Disk G/Ç

**Ne beklemeli:**

* Analiz aşamasında yüksek disk okuma
* Dışa aktarma aşamasında yüksek disk yazma
* SSD, HDD'den önemli ölçüde daha hızlıdır

**Performans ipucu:**

* Mümkün olduğunda proje klasörü için SSD kullanın
* Büyük veri kümeleri için ağ sürücülerinden kaçının
* Diskin kapasitesinin dolmak üzere olmadığından emin olun (yazma hızını etkiler)

***

## İşleme Sırasında Sorunları Tespit Etme

### Uyarı İşaretleri

**İlerleme durur (5 dakikadan fazla süre boyunca değişiklik olmaz):**

* Hata olup olmadığını görmek için Hata Güzelleme Günlüğünü kontrol edin
* Kullanılabilir disk alanını doğrulayın
* Chloros'un çalıştığından emin olmak için Görev Yöneticisi'ni kontrol edin

**Hata mesajları sık sık görünüyor:**

* İşlemeyi durdurun ve hataları inceleyin
* Yaygın nedenler: disk alanı, bozuk dosyalar, bellek sorunları
* Aşağıdaki Sorun Giderme bölümüne bakın

**Sistem yanıt vermiyor:**

* Chloros'un paralel modu çok fazla kaynak tüketiyor
* Eşzamanlı görev sayısını azaltmayı veya donanımı yükseltmeyi düşünün
* Serbest mod, daha az kaynak tüketir

### İşlemi Ne Zaman Durdurmalısınız?

Aşağıdakileri görürseniz işlemi durdurun:

* ❌ "Disk dolu" veya "Dosya yazılmıyor" hataları
* ❌ Tekrarlanan görüntü dosyası bozulma hataları
* ❌ Sistem tamamen donmuş (yanıt vermiyor)
* ❌ Yanlış ayarların yapılandırıldığını fark ettiyseniz
* ❌ Yanlış görüntüler içe aktarıldıysa

**Nasıl durdurulur:**

1.**Durdur düğmesine** tıklayın (Başlat düğmesinin yerine geçer) — bir kez tıklamak yeterlidir
2. İşlem halindeki görüntü tamamlanana kadar çubukta "Durduruluyor..." yazısı görünür, ardından işlem durdurulmuş durumda sona erer
3. Zaten dışa aktarılmış ürünler diskte kalır; günlük dosyasında tamamlanan işlemlerin doğru bir özeti olan `[RUN-SUMMARY]` yazısı görüntülenir
4. Sorunları giderin ve yeniden başlatın — işlem baştan başlar

***

## İşleme Sırasında Sorun Giderme

### İşleme Çok Yavaş

**Olası nedenler:**

* İşaretlenmemiş hedef görüntüler (tüm görüntüler taranıyor)
* SSD yerine HDD depolama
* Yetersiz sistem kaynakları
* Çok sayıda dizin yapılandırılmış
* Ağ sürücüsüne erişim

**Çözümler:**

1. İşlem yeni başlamışsa ve Algılama aşamasındaysa: Durdurun, hedefleri işaretleyin, yeniden başlatın
2. Gelecekte: SSD kullanın, dizin sayısını azaltın, donanımı yükseltin
3. Büyük veri kümelerini toplu olarak işlemek için CLI'yi kullanmayı düşünün

### "Disk Alanı" Uyarıları

**Çözümler:**

1. Hemen disk alanını boşaltın
2. Projeyi daha fazla alana sahip bir sürücüye taşıyın
3. Dışa aktarılacak dizin sayısını azaltın
4. İhtiyacınız olmayan LATTICE dışa aktarma ürünlerini devre dışı bırakın (Proje Ayarları → İşleme)
5. TIFF yerine JPG formatını kullanın (daha küçük dosyalar)

### Sık Görülen "Bozuk Dosya" Mesajları

**Çözümler:**

1. Bütünlüğü sağlamak için görüntüleri SD karttan yeniden kopyalayın
2. SD kartta hata olup olmadığını kontrol edin
3. Bozuk dosyaları projeden kaldırın
4. Kalan görüntülerin işlenmesine devam edin

### Sistemin Aşırı Isınması / Hız Kısıtlaması

**Çözümler:**

1. Yeterli havalandırma sağlayın
2. Bilgisayar havalandırma deliklerindeki tozu temizleyin
3. İşleme yükünü azaltın (Chloros+ yerine Free modunu kullanın)
4. Günün daha serin saatlerinde işlemeyi gerçekleştirin

***

## İşleme Tamamlandı Bildirimi

İşleme bittiğinde:

* İlerleme çubuğu %100'e ulaşır
* Hata Ayıklama Günlüğünde son sayılarla birlikte `[RUN-SUMMARY]` satırları görünür
* Başlat düğmesi tekrar etkin hale gelir
* Tüm çıktı dosyaları, projenin kamera başına çıktı ağacında bulunur: `<project>/<camera>/<format>/<Product>_Images/`

***

## Sonraki Adımlar

İşleme tamamlandığında:

1. **Sonuçları inceleyin** - Bkz. [İşlemeyi Tamamlama](finishing-the-processing.md)
2. **Çıktı klasörünü kontrol edin** - Tüm dosyaların doğru şekilde dışa aktarıldığını doğrulayın
3. **Hata Giderme Günlüğünü inceleyin** - Herhangi bir uyarı veya hata olup olmadığını kontrol edin
4. **İşlenmiş görüntüleri önizleyin** - Görüntü Görüntüleyicisini veya harici bir yazılımı kullanın

İşlenmiş sonuçlarınızı inceleme ve kullanma hakkında bilgi için [İşlemeyi Tamamlama](finishing-the-processing.md) bölümüne bakın.
