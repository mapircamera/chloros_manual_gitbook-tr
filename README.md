---
metaLinks: {}
---

# Başlangıç

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros, [MAPIR](https://www.mapir.camera) tarafından geliştirilen, multispektral görüntüleri işleyen, MAPIR donanımını canlı olarak kontrol eden ve sensör verilerini kaydeden bir yazılım uygulamasıdır. Chloros 1.3.0, tüm MAPIR ürün ailesini destekler:

* **Survey3 kameralar** — RAW+JPG çekimlerini kalibre edilmiş yansıma ve bitki örtüsü indeksi haritalarına dönüştürür. Bkz. [Desteklenen Kameralar](supported-cameras.md).
* **LATTICE kameralar** — GigE multispektral kamera modüllerini tek tek veya senkronize çoklu kamera dizileri halinde canlı olarak bağlayın: önizleme yapın, çekim gerçekleştirin ve kalibre edilmiş parlaklık ve yansıma ürünlerine dönüştürün. [LATTICE bölümüne](lattice/README.md) bakın.
* **DAQ ışık sensörleri** — DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E ile DAQ-E-S (Ethernet) spektral sensörleri: canlı kalibre edilmiş spektrumlar, `.daq` kayıtları ve yansıma işleme için aşağı doğru ışıklandırma. [DAQ bölümüne](daq/README.md) bakın.

{% hint style="success" %}
**Chloros 1.3.0'daki Yenilikler**: AI asistanları, `chloros-mcp` aracılığıyla Chloros'u çalıştırabilir; ayrıca CLI, SDK ve `chloros-mcp` de aynı özellikleri sunar. LATTICE indeks ürünleri iyileştirildi, tam sensör dizileri daha hızlı hale geldi ve her türlü ağda çalışıyor; ayrıca arka uç, komutlar arasında çalışmaya devam ediyor. Aşağıdaki Yenilikler listesine göz atın ve yükleyicileri [İndirin](download.md).
{% endhint %}

{% hint style="info" %}
**Chloros'u bir AI asistanıyla mı kullanıyorsunuz?** Bu kılavuz tam da bunun için hazırlanmıştır. Asistanınıza şu komutları verin:

* `https://mapir.gitbook.io/chloros/llms.txt` — her sayfanın makine tarafından okunabilir dizini.
* Herhangi bir sayfa ham Markdown biçiminde — URL'sine `.md` ekleyin (örn. `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI Referansı](reference/cli-reference.md) ve [SDK Referansı](reference/sdk-reference.md) — LLM kullanımı için yazılmış, eksiksiz ve kesin değerler içeren referans sayfaları.

Örnek komut: *"https://mapir.gitbook.io/chloros/reference/cli-reference.md, dosyasını okuyun, ardından oturum açıp ~/flights/flight_001 klasörünü reflectance + NDVI GeoTIFF'lere dönüştüren bir komut dosyası yazın."*

Ya da Chloros'u doğrudan çalıştırın: SDK ile kurulan bir MCP sunucusu olan `chloros-mcp`, Claude Desktop, Claude Code, Cursor ve diğer MCP istemcileriyle uyumludur.

Tam kılavuz: [Chloros'u AI Asistanlarıyla Kullanma](ai-assistants.md).
{% endhint %}

***

## Chloros 1.3.0'daki Yenilikler

* **AI asistanları Chloros'u çalıştırabilir.** Python SDK ile kurulan bir MCP sunucusu olan `chloros-mcp`, Claude Desktop, Claude Code, Cursor ve diğer MCP istemcilerinin görüntüleri işlemesine ve LATTICE kameralarını, dizilerini ve DAQ ışık sensörlerini çalıştırmasına olanak tanır. Bkz. [AI Asistanlarıyla Chloros'u Kullanma](ai-assistants.md).
* **CLI, SDK ve `chloros-mcp` aynı özellikleri sunar.** Güvenlik nedeniyle yalnızca oturum açma, DAQ-E yapılandırması ve doğrudan DAQ sunucuları CLI'de kalır. Bazı 1.2.0 komut dosyalarında değişiklik yapılması gerekir: [CLI Referansı](reference/cli-reference.md) ve [SDK Referansı](reference/sdk-reference.md) sayfalarının en üstündeki **

1.3.0'da Değişiklikler** bölümüne bakınız.
* **Geliştirilmiş LATTICE endeks ürünleri.** LATTICE yakalamalarından dışa aktarılan NDVI ve diğer endeks ürünleri, tüm işleme modlarında iyileştirilmiştir.
* **Windows yükleyici seçenekleri.**Yükleyici,**Sistem ayarları**altında**Windows uzun yollarını etkinleştir**ve**Chloros arka ucunu Microsoft Defender taramasından hariç tut** seçeneklerini sunar; her ikisi de varsayılan olarak işaretlidir. İlki, proje klasörlerinin 260 karakterden daha uzun adlara sahip olmasına izin verir; ikincisi ise gerçek zamanlı taramanın kamera yakalama hızını yavaşlatmasını engeller (yüksek hızlarda yakalama hızının yaklaşık %30'u). Yükleyici artık CLI klasörünü `PATH`'e yalnızca bir kez ekler ve önceki yükleyicilerin bıraktığı yinelenen girdileri kaldırır. Bkz. [İndir](download.md).
* **Derin proje klasörleri önceden reddedilir.** Windows uzun yol özelliği kapalıyken (Windows varsayılanı), 260 karakterlik yol sınırını aşan çok derin bir proje klasörü, herhangi bir işlem çalıştırılmadan önce reddedilir; bu durumda, sığabilecek en uzun proje klasörü gösterilir ve uzun yol özelliğinin nasıl etkinleştirileceği açıklanır. Önceden, böyle bir çalıştırmada hiçbir ürün kaydedilmezdi. Uzun yollar etkinleştirildiğinde, çok derin klasörlerdeki ürünler artık meta verilerini (GPS ve XMP etiketleri) korur.
* **Maske sınırlaması.** Bir LATTICE parlaklık veya yansıma ürününün bant ayrıştırması sırasında negatif bir bant değeri ortaya çıkarsa, bu değer sıfıra sınırlandırılır ve ürün klasörlerinin yanındaki `clamp_masks/` klasöründe bulunan piksel bazlı bir maske, hangi piksellerin ve bantların etkilendiğini kaydeder. Ürün, maskesine `Chloros:UnmixClampMask` etiketinde ad verir. Canlı görünümler artık dışa aktarımda olduğu gibi tam olarak kırpılır. Bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md).
* **Ürünler kendilerini tanımlar.** Dizin ürünleri, kaynak ham verilerin yerine kendi işleme etiketlerini (`Chloros:ProcessingLevel=index`, dizin ifadesi ve radyans mı yoksa yansıma mı temel alınarak hesaplandıkları bilgisi) taşır. Survey3 ürünleri, her işleme modunda Chloros işleme etiketlerini taşır. LATTICE ham ve debayered dosyaları, sensörün orijinal yöneliminde kalır ve yönelimlerini XMP'ye kaydeder; Görüntü Görüntüleyici, bunları yalnızca görüntüleme amacıyla döndürür.
* **Linux paketleri açılmış olarak dağıtılır.** Arka uç ve CLI, `/usr/lib/chloros/` altında klasörler olarak kurulur; böylece her başlatıldığında kendilerini açmazlar. x86_64'te arka uç artık başlatma için yaklaşık 100 saniye harcamaz ve `.deb`, eski boyutunun yaklaşık yarısı kadardır. Pakete dahil edilen ExifTool artık temiz bir Ubuntu kurulumunda çalışmaktadır. `.deb` artık sistem için Python SDK'yi de yüklemektedir (`python3` (`sudo apt install ./chloros_1.3.0_<arch>.deb` ile yükleyin), kamera akışlarının ihtiyaç duyduğu çekirdek soket alım tamponlarını artırır ve kullanıcı kimliğinizle çalışan bir arka uç hizmeti ekler. Jetson paketi olan `chloros_1.3.0_arm64_jp6.deb`, JetPack 6 için derlenmiştir ve Jetson’un GPU’sunda JetPack’in CUDA sürümü PyTorch ile işlem yapar. Linux'ta dışa aktarılan her görüntü artık diske bir kez ulaşır (bellekte yazılır ve etiketlenir, ardından yerine kopyalanır): diskin hızı belirlediği Jetson'da, 288 görüntülük bir LATTICE çalıştırması 24 dakika yerine 14 dakika sürer. Bkz. [Linux Kurulumu](linux/linux-installation.md).
* **LATTICE OCN kameraları güneş ışığı altında daha doğru okuma yapar.** Bant ayrıştırma işleminden sonra parlaklık, yansıma ve indeksler hızlı bir düzeltme işleminden geçer; böylece gri bir hedef, güneş ışığı altında nötr NDVI değerine yakın bir okuma verir. RGN kameralarında değişiklik yoktur.
* **Uygulama içi hata raporları.** Hata Güzelleştirme Günlüğü sekmesinin üst kısmındaki hata simgesi, MAPIR'e bir açıklama, en fazla 10 görüntü, günlükler ve sistem ayrıntılarını gönderir. Bkz. [GUI : Gezinme](navigation.md).
* **LATTICE dizileri.** Diziler varsayılan olarak tam sensör verisini korur ve sıkıştırılmış 12-bit aktarım (`--packed`, varsayılan `auto`) tam sensör kare hızlarını yaklaşık üçte bir oranında artırır; bir dizi, izin verdiği kare hızında herhangi bir ağda çalışır (yavaş bir bağlantı, diziyi reddetmek veya karelerini küçültmek yerine kare hızını düşürür); kameralarının gücü kapatılıp açıldığında, PTP yeniden etkinleştirilerek dizi kendiliğinden toparlanır; otomatik pozlama artık loş ışıkta bu hızı düşürmez (süreyi doldurur, ardından kazancı artırır); Sürekli çekim, hızlı dizilerin hızına ayak uydurur; RGB kameralar için bir **Vurgular** kontrolü; ve SDK'nın `ArraySession`, `grab()`, `frame_stream()` ve `continuous()` değerlerini kazanır. [LATTICE bölümüne](lattice/README.md) bakınız.
* **Daha istikrarlı otomatik pozlama.** Pozlama sabit ışıkta sabit kalır ve bir değişiklikten sonra birkaç saniye içinde dengelenir; küçük parlak noktalar artık sahneyi karartmaz; manuel pozlamada kazanç Otomatik modda kalır; parlak güneş ışığında yanıt vermeyi durduran kamera, ışık sensörü tarafından okunan ışık seviyesinde tutulur veya sensör bağlı değilse kameranın kendi kareleri kullanılarak ayarlanır. Bkz. [Kamera Ayarları](lattice/camera-settings.md).
* **Işık sensörleri.** **DAQ-E-S desteği:** Sabit difüzörü kapak düzeltmesi gerektirmez ve her okuma, ünitenin eğim, yuvarlanma ve dikleme değerlerini içerir. Bir kayıt, kapağın seçilmiş mi yoksa varsayılmış mı olduğunu belirtir ve işleme sırasında varsayılan bir kapak olması durumunda uyarı verir: DAQ-U, DAQ-M ve DAQ-E, kapaksız olarak gönderilir; bu nedenle takılan kapağın adını belirtin. Canlı `.daq` kayıtları elektrik kesintisine dayanır (`.daq-journal` dosyasını `.daq` ile birlikte saklayın), ve veri göndermeyi durduran bir DAQ-E, kaydında işaretlenen boşlukla yeniden bağlanır. Yalnızca ışık sensörü kayıtlarını içeren bir proje, bunları listeler ve uygulama ile CLI üzerinden işler; `chloros_scripts` ile yapılan bir kayıt, ayarlayabileceğiniz bir sınırla birlikte onu oluşturan DAQ olarak listelenir. Her yakalama, kullandığı DAQ okumasını kaydeder. [DAQ bölümüne](daq/README.md) bakınız bölümüne bakın.
* **İşleme.**LATTICE çerçeveleri, hedef algılama sırasında işleme başlar. Hedef ve DAQ referansları bant başına belirlenir. Taşınan bir proje, dosyalarını yeniden bağlar. 8 bitlik bir yakalamanın dışa aktarımları, yeni**TIFF (8 bit)** formatında 8 bitlik olur. Kalibrasyon hedefi olmadan çalıştırılan bir Survey3, yansıma değeri olarak değil, yedek ürünü (`Vignette_Corrected_Images` veya `Sensor_Response_Images`) olarak kaydedilir. Bir yansıma görüntüsünün bant başına DLS etiketleri her zaman kendi çerçevesine aittir. LATTICE yakalamaları için Texture Aware reddedilir; bu yakalamalar her zaman standart debayer işleminden geçer.
* **CLI ve SDK.** Göreceli çıktı yolları artık komutu veya komut dosyasını çalıştırdığınız klasöre yönlendirilir (yüklü bir arka uç, bunları kendi kurulum klasöründe çözümlemeye çalışır ve hata verirdi). Arka uç, komutlar ve komut dosyaları arasında çalışmaya devam eder ve 30 dakika kullanılmadığında kendiliğinden durur; `chloros-cli backend status` ve `backend stop` bunu yönetir. Yakalama komutları, yazamadıkları her dosyanın adını bildirir ve kod 1 ile sonlanır; tekrarlanan yakalama işlemleri ise sürücü dolduğunda durur. Başarılı bir `process` çalıştırması, `Image products written: N (plus M clamp masks)` raporunu verir. Windows'da SDK'nın `ChlorosLocal` / `process_folder` komutları, kurulu arka ucu bulur (ve halihazırda çalışan bir arka ucu kullanır); `read_image_audit_tags` ise basit bir `pip install` dosyasından çalışır.

***

## Chloros 1.2.0'daki Yenilikler

* **Canlı kamera kontrolü — yeni Kameralar sekmesi.** LATTICE kameralarını tek tek veya senkronize çoklu kamera dizileri olarak (PTP zaman senkronizasyonu, donanım tetiklemeli çekim) bağlayın; canlı önizleme katmanları, bant başına histogramlar, akıllı otomatik pozlama, canlı indeks hesaplayıcı ve uygulama içi kamera yazılım güncellemeleri mevcuttur.
* **Işık sensörleri — yeni Işık Sensörleri sekmesi.** DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E (Ethernet) sensörlerini bağlayın; kalibre edilmiş canlı spektrumları (W/m²/nm) görüntüleyin, `.daq` dosyalarını projenize kaydedin, kapak düzeltme profillerini seçin ve DAQ-E donanım yazılımını ağ üzerinden güncelleyin.
* **Yakalama modları ve kaydediciler.** Tekli / Sürekli / Aralıklı yakalama ile yalnızca ham veriye dayalı En Hızlı Yakalama modu; "Tümünü Yakala" komutunun hangi kameraları ve dışa aktarma türlerini üreteceğinin proje bazında seçimi; izleme sınıfı indeksli video ve analiz sınıfı ham veri serileri için dizi kaydediciler ile çevrimdışı video derlemeleri.
* **LATTICE işleme boru hattı.** LATTICE yakalama klasörlerini içe aktarın ve her ham kareyi, ürün başına açma seçenekleriyle debayered, önizleme, float32 parlaklık (W/m²/sr/nm) ve yansıma ürünlerine dönüştürün. Yansıtma değeri, kare içi kalibrasyon hedefinden veya DAQ aşağı yönlü ışık akışından elde edilebilir; dışa aktarımlara dizi hizalaması uygulanır; eksik fabrika kalibrasyonu, kamera seri numarasına göre otomatik olarak indirilir.
* **Projeler donanımı hatırlar.** Bağlı kameralar ve ışık sensörleri projeyle birlikte kaydedilir (`cameras.json` / `sensors.json`) ve projeyi yeniden açtığınızda kaydedilmiş ayarlarıyla yeniden bağlanırlar. Bkz. [GUI: Projeler](projects.md).
* **Görüntü görüntüleyici güncellemeleri.** Dosya başına doğru yansıma ölçeklendirmesine sahip imleç pikseli/indeksi okuma, katman histogramları, bir GSD birleştirme kaydırıcısı, Tetik Başına / Kamera Başına ızgara modları, LATTICE ürün görünümleri ve diske İndeks/LUT sanal alan dışa aktarımları.
* **CLI ve SDK, büyük ölçüde genişletildi.** Yeni `lattice`, `daq pool-*`, `project` ve `time-sync` komut aileleri; yeni `process` seçenekleri (`--input-level`, ürün başına anahtarlar, `--reflectance-source`, dizi hizalama bayrakları); Arka ucu otomatik olarak başlatan SDK akıllı bağlantı tanıtıcıları (`connect_camera` / `connect_array` / `connect_daq_sensor`); `open_project()` otomasyonu; SDK tekerleği yükleyicilerle birlikte paketlenmiştir ve PyPI'ye `chloros-sdk` olarak yayınlanmıştır.
* **Dürüst hata semantiği.** Ürünleri talep eden ancak hiçbirini yazmayan bir `chloros-cli process` çalıştırması artık açıkça hata verir ve sıfırdan farklı bir değerle sonlanır; başarılı çalıştırmalar ise kaç tane görüntü ürünü yazdıklarını bildirir.
* **Yeni çıktı düzeni.** Ürünler `<project>/<camera>/<format>/<Product>_Images/` klasörlerine yerleştirilir ve kaynak dosya adını korur — ürünü tanımlayan, dosya adı son eki değil, klasördür. Bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md).
* **Daha fazla giriş, plan ve dil.** `.dng` giriş desteği; 38 arayüz dilinin tamamı eksiksiz olarak eklenmiştir; plan başına donanım sınırları kapsamında, ücretsiz (giriş gerektirmeyen) kullanımda en fazla 4 kamera ve 2 ışık sensörü desteklenir.
* **Güvenilirlik.** İşlemeyi Durdur seçeneği, doğru bir çalışma özetiyle sorunsuz bir şekilde sonlandırılır; çoklu kamera projelerinde her kamera dışa aktarılır ve yükleyici güncellemeleri artık oturumunuzu kapatmaz.

***

Chloros, 3 farklı uygulama arayüzünde kullanılabilir:

## Chloros: Masaüstü GUI uygulaması

Canlı Kameralar ve Işık Sensörleri sekmeleri dahil tüm özelliklere sahip, bağımsız ayrı pencere. _Yalnızca Windows._

## [Chloros CLI: Komut satırı arayüzü](CLI.md)

Komut satırı toplu işleme özelliği ile canlı `lattice`, `daq pool-*`, `project` ve `time-sync` komutları. Otomasyon, komut dosyası oluşturma ve başsız çalışma için mükemmeldir. **Windows, Linux amd64 ve Linux arm64 (NVIDIA Jetson)** platformlarında kullanılabilir. _CLI'ye erişmek için ücretli bir Chloros+ kademesi gereklidir._

## [Chloros API: Python SDK](api-python-sdk.md)

Otomasyon ve özel iş akışları için programlı Python arayüzü: tam iş akışı işleme, canlı kamera/dizi oturumları, DAQ sensör oturumları ve kaydedilmiş proje otomasyonu. Masaüstü/CLI paketi ile birlikte yüklenir ve ayrıca `pip install chloros-sdk` olarak yayınlanmıştır. _API'ye erişim için ücretli bir Chloros+ aboneliği gereklidir._

***

## Desteklenen Platformlar

| Platform | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11 (x64)** | Evet | Evet | Evet |
| **Linux amd64 (x86_64)** | Hayır | Evet | Evet |
| **Linux arm64 (NVIDIA Jetson)** | Hayır | Evet | Evet |

Linux kurulum talimatları için [Linux ve Uç Bilişim](linux/linux-overview.md) bölümüne bakın.

***

## Üç Adımda Başlayın

1. **Kurulum** — platformunuza uygun yükleyiciyi indirin ve çalıştırın. Bkz. [İndirme](download.md).
2. **Giriş yapın (GUI için isteğe bağlı)** — GUI, hesap olmadan da görüntüleri ücretsiz olarak işler. Bir [Chloros+ girişi](chloros+-login.md) ile paralel işleme, GPU hızlandırma, daha yüksek cihaz limitleri ve CLI/SDK erişimi etkinleştirilir.
3. **İlk projenizi oluşturun** — Chloros'u açın, [Yeni Proje](projects.md) oluşturun, [görüntülerinizi ekleyin](processing-images-gui/adding-files-to-a-project.md) ve [işlemeye başlayın](processing-images-gui/starting-the-processing.md). Bunun yerine canlı donanımı kontrol etmek için Kameralar veya Işık Sensörleri sekmesini açın — bkz. [GUI: Gezinme](navigation.md).

***

## Chloros


Chloros, çoğu görev için ücretsiz olarak kullanılabilse de, daha fazlasını isteyebilirsiniz. İşte bu noktada Chloros+ için ücretli bir lisans size fayda sağlayabilir. Bir Chloros+ lisansıyla aşağıdaki gibi yeni özelliklerin kilidini açabilirsiniz:

* **Çok İş Parçacıklı İşleme**: Görüntüleri işleme hattı üzerinden eşzamanlı olarak işleyerek, büyük projelerde görüntü işlemeyi önemli ölçüde hızlandırın.
* **GPU (CUDA) Hızlandırma**: Günümüzün daha yüksek GPU bellek seçeneklerinden yararlanarak görüntü işleme hattını daha da hızlandırın. En iyi sonuçlar için 4 GB veya daha fazla VRAM öneririz.
* **Chloros+**[**CLI**](CLI.md) **Erişimi**: Chloros+'ı komut satırından çalıştırarak işlemleri otomatikleştirin ve kendi yazılımınıza entegre edin. Herhangi bir ücretli kademede kullanılabilir; sunucu tarafında uygulanır.
* **Chloros+**[**API**](api-python-sdk.md) **Erişim:** Programlı kontrol için Chloros+'ı Python'tan çalıştırın; böylece araştırma süreçleriniz, veri analizi iş akışlarınız ve özel uygulamalarınızla sorunsuz entegrasyon sağlayın. Tüm ücretli planlarda mevcuttur; sunucu tarafında uygulanır.
* **Daha Yüksek Donanım Sınırları**: Aynı anda daha fazla kamera ve ışık sensörü bağlayın. Oturum açmadan GUI, en fazla 4 kamera ve 2 DAQ ışık sensörüne bağlanabilir; ücretli planlarda her iki sınır da yükseltilir:

| Plan | Kameralar | DAQ ışık sensörleri |
| --- | --- | --- |
| Iron (ücretsiz, oturum açma gerekmez) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Birden Fazla Cihaz Kullanımı**: Her Chloros+ lisansı, 2 veya daha fazla cihazın kaydedilmesine izin verir. Kaydedilen cihazları yönetmek için MAPIR Cloud hesabınızı kullanın. Chloros+ lisansınızı yükseltarak daha fazla cihaz desteği ekleyin.
* **Gelişmiş Doku Duyarlı Debayer Yöntemi:** neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli, kenar duyarlı bir debayer.
* **Özel Çok Spektral İndeks Formülleri:** Hem işleme hem de görüntü görüntüleme sanal ortamı için Chloros raster hesaplayıcılarına özel çok spektral indeksler girin.
* **Linux ve Kenar Bilişim:** Saha ve kenar işleme için NVIDIA Jetson dahil olmak üzere Linux x86_64 ve ARM64 platformlarında Chloros'u çalıştırın. [Linux Genel Bakış](linux/linux-overview.md) sayfasına bakın.

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Fiyatlandırma ve Kaydolma</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.3.0 build so the banner prints "Chloros CLI 1.3.0". -->
