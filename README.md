---
metaLinks: {}
---

# Başlangıç

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>Chloros, [MAPIR](https://www.mapir.camera) tarafından geliştirilen, multispektral görüntüleri işlemek, MAPIR donanımını canlı olarak kontrol etmek ve sensör verilerini kaydetmek için tasarlanmış bir yazılım uygulamasıdır. Chloros 1.2.0, tüm MAPIR ürün ailesini destekler:

* **Survey3 kameralar** — RAW+JPG çekimlerini kalibre edilmiş yansıma ve bitki örtüsü indeksi haritalarına dönüştürür. [Desteklenen Kameralar](supported-cameras.md) bölümüne bakın.
* **LATTICE kameralar** — GigE multispektral kamera modüllerini tek tek veya senkronize çoklu kamera dizileri olarak canlı olarak bağlayın: önizleme yapın, çekim gerçekleştirin ve kalibre edilmiş radyans ve yansıma ürünlerine dönüştürün. [LATTICE bölümüne](lattice/README.md) bakın.
* **DAQ ışık sensörleri** — DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E (Ethernet) spektral sensörleri: canlı kalibre edilmiş spektrumlar, `.daq` kayıtları ve yansıma işleme için aşağıya doğru ışıklandırma. [DAQ bölümüne](daq/README.md) bakın.

{% hint style="success" %}
**Chloros 1.2.0&#x27;daki Yenilikler**: canlı LATTICE kamera ve dizi kontrolü, DAQ ışık sensörü entegrasyonu, yakalama modları ve kayıt cihazları, eksiksiz bir LATTICE radyometrik işleme boru hattı, CLI/SDK&#x27;ten proje otomasyonu ve çok daha fazlası. Aşağıdaki Yenilikler listesine göz atın ve değişiklik günlüğü için [İndirin](download.md).
{% endhint %}

{% hint style="info" %}
**Chloros&#x27;i bir AI asistanıyla mı kullanıyorsunuz?** Bu kılavuz tam da bunun için hazırlanmıştır. Asistanınızı şu sayfalara yönlendirin:

* `https://mapir.gitbook.io/chloros/llms.txt` — her sayfanın makine tarafından okunabilir dizini.
* Ham Markdown biçimindeki herhangi bir sayfa — `.md`&#x27;e URL ekleyin (ör. `https://mapir.gitbook.io/chloros/reference/cli-reference.md`).
* [CLI Referansı](reference/cli-reference.md) ve [SDK Referansı](reference/sdk-reference.md) — LLM tarafından kullanılmak üzere yazılmış, eksiksiz ve tam değer içeren referans sayfaları.

Örnek komut: *&quot;https://mapir.gitbook.io/chloros/reference/cli-reference.md,&#x27;i okuyun, ardından ~/flights/flight_001 klasörüne giriş yapıp bu klasörü reflectance + NDVI GeoTIFF&#x27;lere dönüştüren bir komut dosyası yazın.&quot;*

Tam kılavuz: [AI Asistanlarıyla Chloros&#x27;i Kullanma](ai-assistants.md).
{% endhint %}

***

## Chloros 1.2.0&#x27;daki Yenilikler

* **Canlı kamera kontrolü — yeni Kameralar sekmesi.** LATTICE kameralarını tek tek veya senkronize çoklu kamera dizileri olarak (PTP zaman senkronizasyonu, donanım tetiklemeli çekim) bağlayın; canlı önizleme katmanları, bant başına histogramlar, akıllı otomatik pozlama, canlı indeks hesaplayıcı ve uygulama içi kamera yazılım güncellemelerinden yararlanın.
* **Işık sensörleri — yeni Işık Sensörleri sekmesi.** DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E (Ethernet) sensörlerini bağlayın; kalibre edilmiş canlı spektrumları (W/m²/nm) görüntüleyin, `.daq` dosyalarını projenize kaydedin, kapak düzeltme profillerini seçin ve DAQ-E donanım yazılımını ağ üzerinden güncelleyin.
* **Yakalama modları ve kaydediciler.** Tekli / Sürekli / Aralıklı yakalama ile yalnızca ham veriye dayalı En Hızlı Yakalama modu; &quot;Tümünü Yakala&quot; komutunun hangi kameraları ve dışa aktarma türlerini üreteceğinin proje bazında seçimi; izleme sınıfı indeksli video ve analiz sınıfı ham veri patlamaları için dizi kaydediciler ile çevrimdışı video derlemeleri.
* **LATTICE işleme boru hattı.** LATTICE yakalama klasörlerini içe aktarın ve her ham kareyi, ürün başına açma seçenekleriyle bayersizleştirilmiş, önizleme, float32 parlaklık (W/m²/sr/nm) ve yansıma ürünlerine dönüştürün. Yansıtma değeri, kare içi kalibrasyon hedefinden veya DAQ aşağı yönlü ışık akımından elde edilebilir; dışa aktarımlara dizi hizalaması uygulanır; eksik fabrika kalibrasyonu, kamera seri numarasına göre otomatik olarak indirilir.
* **Projeler donanımı hatırlar.** Bağlı kameralar ve ışık sensörleri projeyle birlikte kaydedilir (`cameras.json` / `sensors.json`) ve projeyi yeniden açtığınızda kaydedilmiş ayarlarıyla yeniden bağlanırlar. Bkz. [GUI: Projeler](projects.md).
* **Görüntü görüntüleyici güncellemeleri.** Dosya başına doğru yansıma ölçeklendirmesine sahip imleç pikseli/indeks okuma, katman histogramları, bir GSD birleştirme kaydırıcısı, Tetik Başına / Kamera Başına ızgara modları, LATTICE ürün görünümleri ve diske İndeks/LUT sanal alan dışa aktarımları.
* **CLI ve SDK, büyük ölçüde genişletildi.** Yeni `lattice`, `daq pool-*`, `project` ve `time-sync` komut aileleri; yeni `process` seçenekleri (`--input-level`, ürün başına anahtarlar, `--reflectance-source`, dizi hizalama bayrakları); arka ucu otomatik olarak başlatan SDK akıllı bağlantı tanıtıcıları (`connect_camera` / `connect_array` / `connect_daq_sensor`); `open_project()` otomasyonu; SDK tekerleği, yükleyicilerle birlikte paketlenmiştir ve PyPI&#x27;da `chloros-sdk` olarak yayınlanmıştır.
* **Dürüst hata semantiği.** Ürünleri talep eden ancak hiçbirini yazmayan bir `chloros-cli process` çalıştırması artık açıkça hata verir ve sıfırdan farklı bir değerle sonlanır; başarılı çalıştırmalar ise kaç tane görüntü ürünü yazdıklarını bildirir.
* **Yeni çıktı düzeni.** Ürünler, `<project>/<camera>/<format>/<Product>_Images/` klasörlerine yerleştirilir ve kaynak dosya adını korur — ürünü tanımlayan, dosya adı son eki değil, klasördür. Bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md).
* **Daha fazla girdi, plan ve dil desteği.** `.dng` girdi desteği; 38 arayüz dilinin tamamı eksiksiz olarak eklenmiştir; plan başına donanım sınırları kapsamında, ücretsiz (giriş gerektirmeyen) kullanımda en fazla 4 kamera ve 2 ışık sensörü desteklenir.
* **Güvenilirlik.** İşlemeyi Durdur seçeneği, doğru bir çalışma özetiyle sorunsuz bir şekilde sonlandırılır; çoklu kamera projelerinde her kamera dışa aktarılır ve yükleyici güncellemeleri artık oturumunuzu kapatmaz.***

Chloros, 3 farklı uygulama arayüzünde mevcuttur:

## Chloros: Masaüstü GUI uygulaması

Canlı Kameralar ve Işık Sensörleri sekmeleri dahil tüm özelliklere sahip, bağımsız bir pencere. _Yalnızca Windows._

## [Chloros CLI: Komut satırı arayüzü](CLI.md)

Komut satırı toplu işleme ve canlı `lattice`, `daq pool-*`, `project` ve `time-sync` komutları. Otomasyon, komut dosyası oluşturma ve başlıksız çalışma için mükemmeldir. **Windows, Linux amd64 ve Linux arm64 (NVIDIA Jetson)** üzerinde kullanılabilir. _CLI&#x27;ye erişim için ücretli bir Chloros+ kademesi gereklidir._

## [Chloros API: Python SDK](api-python-sdk.md)

Otomasyon ve özel iş akışları için programlama odaklı Python arayüzü: tam iş akışı işleme, canlı kamera/dizi oturumları, DAQ sensör oturumları ve kaydedilmiş proje otomasyonu. Masaüstü/CLI paketi ile kurulur ve ayrıca `pip install chloros-sdk` olarak yayınlanır. _API&#x27;ye erişim için ücretli bir Chloros+ kademesi gereklidir._

***

## Desteklenen Platformlar

| Platform | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11 (x64)** | Evet | Evet | Evet |
| **Linux amd64 (x86_64)** | Hayır | Evet | Evet |
| **Linux arm64 (NVIDIA Jetson)** | Hayır | Evet | Evet |

Linux kurulum talimatları için [Linux ve Kenar Bilişim](linux/linux-overview.md) bölümüne bakın.

***

## Üç Adımda Başlayın

1. **Kurulum** — platformunuza uygun kurulum programını indirin ve çalıştırın. Bkz. [İndirme](download.md).
2. **Giriş yapın (GUI için isteğe bağlı)** — GUI, hesap olmadan da görüntüleri ücretsiz olarak işler. [Chloros+ girişi](chloros+-login.md) ile paralel işleme, GPU hızlandırması, daha yüksek cihaz limitleri ve CLI/SDK erişimini etkinleştirir.
3. **İlk projenizi oluşturun** — Chloros&#x27;i açın, bir [Yeni Proje](projects.md) oluşturun, [görüntülerinizi ekleyin](processing-images-gui/adding-files-to-a-project.md) ve [işlemeye başlayın](processing-images-gui/starting-the-processing.md). Bunun yerine canlı donanımı kontrol etmek için Kameralar veya Işık Sensörleri sekmesini açın — bkz. [GUI: Gezinme](navigation.md).***

## Chloros+

Chloros çoğu görev için ücretsiz olarak kullanılabilse de, daha fazlasına ihtiyaç duyabilirsiniz. İşte bu noktada Chloros+ için ücretli bir lisans size fayda sağlayabilir. Chloros+ lisansıyla aşağıdaki gibi yeni özelliklerin kilidini açabilirsiniz:

* **Çok İş Parçacıklı İşleme**: Görüntüleri işleme hattı üzerinden eşzamanlı olarak işleyerek büyük projelerde görüntü işlemeyi önemli ölçüde hızlandırın.
* **GPU (CUDA) Hızlandırma**: Görüntü işleme iş akışını daha da hızlandırmak için günümüzün daha yüksek GPU bellek seçeneklerinden yararlanın. En iyi sonuçlar için 4 GB veya daha fazla VRAM öneririz.
* **Chloros+**[**CLI**](CLI.md)**Erişim**: Chloros+ komutunu komut satırından çalıştırarak işlemi otomatikleştirin ve kendi yazılımınıza entegre edin. Herhangi bir ücretli pakette mevcuttur; sunucu tarafında uygulanır.
* **Chloros+**[**API**](api-python-sdk.md)**Erişim:** Programlı kontrol için Python üzerinden Chloros+ komutunu çalıştırın; bu sayede araştırma süreçleriniz, veri analizi iş akışlarınız ve özel uygulamalarınızla sorunsuz entegrasyon sağlayın. Herhangi bir ücretli pakette mevcuttur; sunucu tarafında uygulanır.
* **Daha Yüksek Donanım Sınırları**: Aynı anda daha fazla kamera ve ışık sensörü bağlayın. Oturum açmadan GUI, en fazla 4 kamera ve 2 DAQ ışık sensörüne bağlanabilir; ücretli planlarda her iki sınır da yükseltilir:

| Plan | Kameralar | DAQ ışık sensörleri |
| --- | --- | --- |
| Iron (ücretsiz, oturum açma gerekmez) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

* **Birden Fazla Cihaz Kullanımı**: Her bir Chloros+ lisansı, 2 veya daha fazla cihazın kaydedilmesine izin verir. Kaydedilen cihazları yönetmek için MAPIR Cloud hesabınızı kullanın. Chloros+ lisansınızı yükseltmek suretiyle daha fazla cihaz desteği ekleyin.
* **Gelişmiş Doku Duyarlı Debayer Yöntemi:** neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli, kenar duyarlı bir debayer.
* **Özel Multispektral İndeks Formülleri:** hem işleme hem de görüntü görüntüleme sanal ortamı için Chloros raster hesaplayıcılarına özel multispektral indeksler girin.
* **Linux ve Kenar Bilişim:** Saha ve kenar işleme için NVIDIA Jetson dahil olmak üzere Linux x86_64 ve ARM64 platformlarında Chloros&#x27;i çalıştırın. [Linux Genel Bakış](linux/linux-overview.md) sayfasına bakın.

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Fiyatlandırma ve Kaydolma</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: cli.JPG shows the 1.1.0 CLI banner. Re-shoot a terminal running `chloros-cli --version` + `chloros-cli status` on the 1.2.0 build so the banner prints "Chloros CLI 1.2.0". -->
