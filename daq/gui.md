# Chloros’taki DAQ Sekmesi

Chloros kenar çubuğunda **Işık Sensörleri** olarak adlandırılan DAQ sekmesi, [DAQ-U, DAQ-M ve DAQ-E ışık sensörleri](README.md) için canlı kontrol arayüzüdür: Herhangi bir aktarım protokolü üzerinden sensörleri bağlayın, kalibre edilmiş spektrumları gerçek zamanlı olarak izleyin, bir sensör çiftinden canlı yansıma oranını hesaplayın ve `.daq` dosyalarını doğrudan projenize kaydedin.

Chloros arka ucunun başlatılması tamamlandığında sekme kullanılabilir hale gelir. Sekmedeki grafikler, kesintiye uğradığında otomatik olarak yeniden bağlanan (2–10 saniye bekleme süresi) canlı bir bağlantı üzerinden Chloros'un DAQ hizmeti tarafından beslenir; hizmete erişilemediğinde, sensörün Durum satırında **Sunucu Yok** yazısı görünür.

Düzen, bir **sensör kenar çubuğu**(bağlı her sensör için bir satır) ve bir**grafik alanı** (her sensör veya grup için bir grafik kutucuğu) şeklindedir.

<!-- SCREENSHOT-NEEDED: full DAQ (Light Sensors) tab in list view with one DAQ-E connected — sensor sidebar on the left (Connect Sensor + Record All buttons, one sensor row), spectrum chart with rainbow fill in the main area, live data table below the chart -->

***

## Sensör bağlama

Kenar çubuğunun üst kısmındaki **Sensörü Bağla** seçeneğine tıklayın. Bağlantı iletişim kutusu ana alanda açılır (veya başka bir sensör eklenirken üst üste binen bir pencere olarak açılır — bu durumda bir İptal düğmesi görünür).

| Kontrol | Davranış |
| --- | --- |
| **Cihaz Türü** | `DAQ-U (USB)` (varsayılan), `DAQ-M (Bluetooth)` veya `DAQ-E (Ethernet)`. Değiştirildiğinde, yeni seçilen aktarım türü için tarama yeniden başlar. |
| **Bağlantı Noktası / BLE Cihazı / Ana Bilgisayar Adı / IP** | Tespit edilen cihazları `device - description` şeklinde listeler; sensör olarak tanınan ilk giriş otomatik olarak seçilir. Tarama sırasında `Scanning...` (USB), 8 saniyelik geri sayımla `Scanning (N)...` (BLE) veya 5 saniyelik geri sayımla `Discovering ethernet sensors (N)...` (Ethernet) olarak görüntülenir. Boş sonuçlarda `No ports` / `No BLE devices` / `No ethernet sensors found` yazılır. |
| **↻ Yenile** | Seçilen aktarım türünü hemen yeniden tarar (BLE/Ethernet taraması sırasında devre dışıdır). |
| **Sensöre Bağlan** | Bir cihaz seçildiğinde etkinleşir; bağlantı kurulurken etiket `Connecting...` olarak değişir. |

Keşif işlemi yalnızca **bağlantı iletişim kutusu ekranda açıkken** çalışır ve yalnızca seçilen aktarım için her 15 saniyede bir tekrarlanır — sekmeyi açmak tek başına tarama yapmaz. Hata durumunda iletişim kutusunda şunlar görüntülenir: *"Bağlantı başarısız oldu. Sensörü çıkarıp tekrar takmayı deneyin, ardından Bağlan'a tekrar tıklayın."*

İlk sensörünüz bağlandığında kenar çubuğu otomatik olarak açılır.

{% hint style="info" %}
**DAQ-E görünmüyor mu?**DAQ-E'de durum LED'i yoktur — cihazın takılı olduğu anahtar veya enjektör bağlantı noktasındaki PoE/bağlantı göstergesini kontrol edin ve açıldıktan sonra önyükleme yapması için birkaç saniye bekleyin. Chloros makinesi aynı yayın etki alanında olmalıdır (mDNS, yönlendiricilerden geçmez). Yükleyicinin**Chloros Arka Ucu (UDP-In)** güvenlik duvarı kuralı, mDNS (UDP 5353), DAQ-E verileri (UDP 5002) ve PTP (UDP 319/320) trafiğini geçirir; üçüncü taraf bir güvenlik duvarında da aynı bağlantı noktalarının izin verilmesi gerekir. Aynı LAN üzerindeki iki DAQ-E birimi, her biri kendi `daq-e-<id>.local` ana bilgisayar adı altında ayrı ayrı algılanır.
{% endhint %}

<figure><img src="../.gitbook/assets/v120-daq-device-type.png" alt=""><figcaption>Cihaz Türü seçenekleri arasında DAQ-U (USB), DAQ-M (Bluetooth) ve DAQ-E (Ethernet) bulunur</figcaption></figure>

***

## Sensör kenar çubuğu

Bağlı her sensör için bir satır ayrılır (ayrıca her Ambient+Object grubu için bir satır daha). Satırlar sürükleyerek yeniden sıralanabilir ve sıralamaları grafik karolarının sıralamasını da değiştirir. Bir satırı tıklayarak o sensörü/grubu liste görünümünde aktif grafik haline getirebilirsiniz.

| Öğe | Anlam |
| --- | --- |
| Renkli sol kenarlık | Sensörün grafik rengi. |
| Aktarım rozeti | `DAQ-U` / `DAQ-M` / `DAQ-E` veya Ambient+Object yansıma grubu için yeşil bir `REF` rozeti. |
| Cihaz adı | Varsayılan olarak sensörün seri numarasıdır (kalibrasyon, `.daq` dosya adları ve içe aktarma eşleşmesi için sabit kimliği); özel adlar proje bazında korunur. |
| **Kalibre Edildi** simgesi (yeşil) | Sensörün fabrika kalibrasyon paketi yüklendiğinde gösterilir; yani spektrumlar gerçek W/m²/nm değerlerindedir. |
| **Güncelleme Mevcut** simgesi (kehribar rengi, yalnızca DAQ-E) | Çalışan ürün yazılımı, bu Chloros derlemesiyle birlikte gelen görüntüden daha eskidir. Güncelleme sırasında canlı ilerleme gösterir (`Flashing… N%`, `Restarting sensor…`, ardından `Updated X → Y` veya `Failed`). |
| Göz | Bu sensörün grafikteki görünürlüğünü değiştirir. |
| Dişli | Sensör başına ayarlar penceresini açar (aşağıda). |
| ✕ (kırmızı) | Sensörün bağlantısını keser veya bir Ortam+Nesne grubunu kaldırır. |

Satırların üzerinde iki düğme bulunur:

* **Sensörü Bağla** — bağlanma iletişim kutusunu açar (meşgulken `Connecting...` olarak yeniden etiketlenir).
* **Tümünü Kaydet / Tümünü Durdur**—**tüm**bağlı sensörlerde bir `.daq` kaydını başlatır veya durdurur. En az bir sensör**ve açık bir proje** gerektirir (araç ipucu: "Kaydetmek için bir proje açın"); herhangi bir kayıt devam ederken düğme kırmızıya döner.

Boş durumda "Bağlı sensör yok" yazısı görünür.

<!-- SCREENSHOT-NEEDED: sensor sidebar with three rows — a DAQ-E showing both the green Calibrated pill and the amber Update Available pill, a DAQ-U row, and a green REF group row — plus the Connect Sensor and Record All buttons -->

***

## Sensör başına ayarlar (dişli simgesi penceresi)

Bir sensör satırındaki dişli simgesiyle açın. İçerik sırasıyla:

* **Bilgi satırları** — Cihaz Türü (DAQ-U/M/E), Bağlantı (`Serial (USB)` / `Bluetooth` / `Ethernet`), Bağlantı Noktası (COM bağlantı noktası, BLE adresi veya ana bilgisayar) ve Seri.
* **Kalibrasyon Raporu: İndir** — bu ünitenin NIST izlenebilir kalibrasyon sertifikasını (PDF) alır ve PDF görüntüleyicinizde açar. Seri numarası bilindiğinde kullanılabilir; sertifika ilk bağlantıda önbelleğe alınır.
* **Cihaz Adı** — yeniden adlandırmak için kalem simgesine tıklayın; proje başına kalıcıdır.
* **Grafik Çizgisi Rengi** — renk örneği; proje başına kalıcıdır.
* **Entegrasyon Süresi (ms)**— kaydırıcı + sayı,**1–500 ms**, varsayılan**32 ms**. AE AÇIK iken devre dışıdır.
* **Kare Ortalaması**— kaydırıcı + sayı,**1–50 kare**, varsayılan**20**.
* **AE: AÇIK/KAPALI**— otomatik pozlama anahtarı; bağlandığında**varsayılan olarak AÇIK**. Entegrasyon süresini manuel olarak ayarlamak için kapatın.
* **Akışı Durdur / Akışı Başlat** — canlı akışı duraklatın veya devam ettirin.
* **Kaydet / Kaydı Durdur** — sensör başına `.daq` kaydı (açık bir proje gerektirir).
* **Cap** — cap düzeltme profili (sonraki bölüm).
* **Canlı bilgi satırları** — Entegrasyon Süresi (ms), FPS, Örnekler, Kayıt (kırmızı `REC` veya `Off`) ve Durum (`Streaming` / `Paused` / `SATURATED` / `No Server`).

### Yalnızca DAQ-E: ağ, ürün yazılımı ve PTP satırları

* **Ana Bilgisayar Adı / IP** — ünitenin geçerli adresi.
* **Yazılım** — canlı yazılım sürümü ve bir eylem hücresi: Bu Chloros derlemesi daha yeni bir DAQ-E yazılım görüntüsü içeriyorsa, **\<version\>'ne güncelle** düğmesi görünür. Güncelleme, ağ üzerinden yaklaşık 30 saniye içinde gerçekleştirilir; sensör otomatik olarak yeniden başlatılır ve yeniden bağlanır; aktarımın kesilmesi durumunda mevcut yazılım değişmeden kalır. İlerleme durumu canlı olarak aktarılır (`Flashing… N%` → `Restarting sensor…` → `Updated X → Y`) ve hücre, güncel olduğunda `Up to date` değerini gösterir.
* **PTP Senkronizasyonu** — canlı PTP durumu (`unknown` değerine geri döner). DAQ-E ürün yazılımı v1.2.0+ sürümü, IEEE 1588 PTPv2 standardında yalnızca bağımlı saat olarak yer alır; Chloros ana bilgisayarının arka ucu PTP grandmaster'dır ve LAN üzerindeki her DAQ-E ve LATTICE kamera, etki alanı 0'da buna bağlanır ve zaman damgalarını yaklaşık 1 ms içinde tutar.
* **Ağ**— ünitenin çalıştığı adres ve bu adresi nasıl aldığı (DHCP, bağlantı-yerel veya statik) ile yeniden başlatma sonrasında geçerli olacak kaydedilmiş bir ayar.**Değiştir…**seçeneği bir form açar:**Otomatik (DHCP)**veya**Statik IP**seçenekleri ile bir IP adresi, alt ağ maskesi ve isteğe bağlı bir ağ geçidi girilebilir.**Uygula ve yeniden başlat** seçeneği ayarları kaydeder, sensörü yeniden başlatır ve yeniden bağlar. Bu bilgisayarın ağı dışındaki statik bir adres reddedilir, çünkü Chloros yeniden başlatma sonrasında sensöre erişemez; adresi yeniden belirlenecek veya o ağa yönlendirilecek bir bilgisayar için, buna yine de izin veren bir kutucuk görüntülenir. **Güç açıldığında akış**seçeneği, sensörün herhangi bir yazılım bağlanmadan, güç verilir verilmez akışa başlayıp başlamayacağını belirler.**Sensörü yeniden başlat** seçeneği, sensörü yeniden başlatır ve mevcut ayarlarıyla yeniden bağlar. Sensör kayıt yaparken yeniden başlatma reddedilir. 1.3.0 sürümündeki yenilik.

Bir Ambient+Object grubu için, dişli simgesine tıklandığında açılan pencerede yalnızca grubun kaynak sensörleri, Cihaz Adı ve Grafik Çizgi Rengi gösterilir.

<!-- SCREENSHOT-NEEDED: per-sensor settings modal for a DAQ-E — info rows, Calibration Report Download, Hostname/IP + Firmware row with an "Update to <ver>" button, PTP Sync row, Integration Time / Frame Average sliders, AE ON toggle, and the Cap dropdown all visible (scrolled composite acceptable) -->



### Kapak seçimi

**Kapak** açılır menüsü, Chloros'a sensörün difüzörünün üzerine hangi fiziksel kapağın takılı olduğunu bildirir ve bu kapağın fabrikada ölçülen düzeltme profilini her spektruma uygular. Seçenekler modele göre değişir:

| Model | Kapak seçenekleri |
| --- | --- |
| DAQ-U | Yok (çıplak sensör), FOV 15°, FOV 30°, FOV 45°, FOV 60°, FOV 90°, Sunshine (kosinüs düzeltici) |
| DAQ-M | Yok (çıplak sensör), Sunshine (kosinüs düzeltici) |
| DAQ-E | Yok (çıplak sensör), FOV 15°, FOV 45°, FOV 90°, Sunshine (kosinüs düzeltici) |

**Her model için varsayılan ayar Güneş Işığı (kosinüs düzeltici)**'dir — MAPIR, her DAQ'yı Güneş Işığı kapağı takılı olarak gönderir ve bu, standart dış mekan yapılandırmasıdır: 60°'ye kadar kosinüs hatası ≤ ±4 % ve 70°'ye kadar ≤ ±4,5 % olan 180°'lik yarım küre görüş açısı (güneş yüksekliği ~15°'nin altında kullanılması önerilmez), tasarım gereği zayıflatma (~12×). Seçiminiz projede kalıcı olarak kaydedilir.

{% hint style="warning" %}
**Kapak seçimi, fiziksel kapakla eşleşmelidir.**Ne sensör ne de yazılım, hangi kapağın takılı olduğunu algılayamaz. Bu seçim, hem canlı düzeltmeyi hem de her `.daq` dosyasına yazılan damgayı belirler — Sunshine kapağının ~12× zayıflatma oranıyla, bildirilmemiş bir kapak değişikliği, spektrumları yaklaşık olarak bu faktör kadar yanlış düzeltir. (Aynı kapağın çıkarılıp yeniden takılması, yaklaşık %1,5'lik bir sapmaya neden olur.) Yalnızca kapak fiziksel olarak çıkarıldığında**Yok (çıplak sensör)** seçeneğini seçin; DAQ-E'de "None" seçeneği, gömülü cam difüzör için yine de fabrika geometri profilini uygular — bu bir "hiçbir işlem yapmama" durumu değildir — ve çıplak bir DAQ-E, desteklenen bir saha yapılandırması değil, laboratuvar yapılandırmasıdır.
{% endhint %}

{% hint style="info" %}
Daha önceki bir kılavuzdan güncelleme: 1.1.0 sürümündeki tarayıcı tarafındaki "Güneş Işığı Difüzörü Takılı" seçeneği kaldırılmıştır. Kapak yönetimi artık sunucu tarafında uygulanan, sensör başına bu Kapak profiline göre yapılır.
{% endhint %}

***

## Grafik alanı

Sabit üst çubukta bir **liste ⇄ ızgara görünümü anahtarı**ve bir**Grafik Yakınlaştırma** kaydırıcısı (karo boyutu 200–2000 px) bulunur. Birden fazla grafik grubu olduğunda görünüm otomatik olarak ızgaraya geçer; bir veya daha az grup olduğunda ise listeye geri döner. Görünüm modu ve grafik boyutu proje bazında korunur.

Her sensör için **spektrum grafiği** şunları gösterir:

* **X ekseni** — Dalga boyu (nm). Sensör ızgarası 5 nm aralıklarla 340–1010 nm aralığındadır (135 nokta) ve görüntüleme için 1 nm'ye enterpolasyon yapılmıştır.
* **Y ekseni**— kalibre edilmiş bir sensör için (yeşil**Kalibre Edildi**etiketi),**Güç (W/m²/nm)**— üç taşıyıcıda da radyometrik olarak kalibre edilmiş spektral ışınım — tepe noktasından seçilen otomatik SI öneki (m/µ/n) ile. Kalibrasyon paketi yüklenmemiş bir sensör, ham okumalarını ölçeklenmemiş olarak**Yoğunluk (sayım)** şeklinde çizer. Bir grafik her iki türü de üst üste gösterdiğinde, güç sol eksende, sayımlar ise sağ eksende yer alır.
* Tek bir iz altında gökkuşağı spektral dolgusu; tek bir grafikte üst üste binen birden fazla sensör, soluk dolgulu renkli çizgiler olarak gösterilir.
* **Fareyi üzerine getirin**— dalga boyu ve sensör başına değeri gösteren dikey bir imleç (kalibre edilmiş bir sensör için W/m²/nm, aksi takdirde toplam sayım);**sürükleyerek** yakınlaştırın (yakınlaştırıldığında bir uzaklaştırma düğmesi görünür).
* Bu grafiğe bir sensör eklemek veya bir grup oluşturmak için **+** düğmesi (yalnızca ızgara görünümü).
* En üstte ortalanmış cihaz adı ve ilk kare gelene kadar dönen bir simge.

**Doygunluk**, grafiğin üzerinde işaretlenmez: doymuş bir sensör, canlı veri tablosunda kırmızı `SATURATED` durum metni ve kırmızı `Saturated: Yes` satırı gösterir. Bunu temizlemek için entegrasyon süresini kısaltın veya AE'yi yeniden etkinleştirin.

<!-- SCREENSHOT-NEEDED: grid view with at least two chart tiles visible, the Chart Zoom slider and list/grid toggle in the top bar, and the "+" add-sensor button visible on one tile -->



***

## Canlı veri tablosu (liste görünümü)

Liste görünümündeki grafiğin altında, her 500 ms'de bir yenilenir:

* **Tüm modeller**: Işık Rengi örneği (CIE XYZ'den sRGB), Doymuş (Evet/Hayır), CIE 1931 X/Y/Z, Renklik x/y, CIE u′/v′, CCT (K), CRI (Ra), Baskın Dalga Boyu (nm), Tepe Dalga Boyu (nm), Uyarma Saflığı, Duv, CIE L\*/a\*/b\* ve Munsell H/V/C.
* **Yalnızca kalibre edilmiş sensörler**(fabrika kalibrasyon paketi yüklendikten sonra DAQ-U / DAQ-M / DAQ-E modellerinden herhangi biri — sensör satırındaki yeşil**Kalibre Edildi** etiketi bunu gösterir): Toplam Güç (W/m²), Fotopik Lüks (lx), Skotopik Lüks (lx), S/P Oranı, PPFD artı PPFD Red /Green /Blue (µmol/m²/s) ve opik ışık şiddetleri — S-konisi, Melanopik, Rodopik, M-konisi, L-konisi (tümü W/m²).

<!-- SCREENSHOT-NEEDED: list view live data table for a DAQ-E showing both the colorimetric rows and the power-calibrated rows (Total Power, Photopic/Scotopic Lux, PPFD, opic irradiances) -->

***

## Yansıtma grupları (Ortam + Nesne)

Bağlı iki sensör, kamera kullanılmadan canlı bir yansıma göstergesinde birleştirilebilir:

1. Izgara görünümünde, bir grafik kutucuğundaki **+**simgesine tıklayın ve**Ortam + Nesne'yi Birleştir** seçeneğini seçin.
2. Bir **Ortam Işık Kaynağı**sensörü ve bir**Nesne Tarayıcı**sensörü (iki farklı sensör) seçin, ardından**Oluştur**'a tıklayın.

Chloros, iki canlı akıştan dalga boyu başına R(λ) = nesne(λ) / ortam(λ) değerini hesaplar (ortam ≤ 0 olduğunda 0). Grubun etiketi, sensörlerin kalibrasyon sınıfına göre belirlenir:

* Her iki sensör de kalibre edilmiş (paket yüklenmiş) → **"Görünür Yansıtma"**.
* Herhangi bir sensör kalibre edilmemişse → **"Göreceli Yansıtma"**.

Grup, kenar çubuğunda yeşil renkli bir `REF` satırı olarak ve kendi grafiğinde (gökkuşağı dolgulu, fareyi üzerine getirdiğinizde 4 ondalık basamağa kadar değerler gösterilir, sürükleyerek yakınlaştırma) görünür.

**+**menüsü ayrıca üç farklı yerleştirme seçeneği sunan**Yeni Sensör Ekle** seçeneğini de içerir: *Yeni Sensörü Birleştir* (bu grafiğe ekle), *Mevcut Sensörü Buraya Taşı* veya *Yeni Sensörü Görüntüle* (kendi grafiği).

<!-- SCREENSHOT-NEEDED: the "+" add-sensor overlay open on a chart tile showing the menu (Add New Sensor / Combine Ambient + Object / Cancel), and the Ambient + Object sub-dialog with its two sensor selects -->



### Bitki örtüsü indeksi tablosu

Liste görünümünde, bir bitki örtüsü indeksi tablosu, yansıma grubunun grafiğinin altında yer alır; bu tablo, bant merkezlerindeki canlı yansıma değerlerinden hesaplanır: **mavi 450 / yeşil 550 / kırmızı 670 / NIR 800 nm** bant merkezlerindeki canlı yansıma değerlerinden hesaplanır (değerler 4 ondalık basamağa kadar gösterilir; hesaplanamadığında `---` olarak görünür; tam adı görmek için bir endeks adının üzerine fareyi getirin):

* **Her zaman gösterilir** (ölçekten bağımsız, herhangi bir sensör kombinasyonu): NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR.
* **Yalnızca her iki sensör de güç kalibrasyonundan geçmişse** (her iki paket de yüklenmişse): EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI.

<!-- SCREENSHOT-NEEDED: an Ambient+Object reflectance group in list view — reflectance chart labeled "Apparent Reflectance" with the vegetation index table below it showing live NDVI etc. -->

***

## `.daq` dosyalarını kaydetme

* Kayıt için **açık bir proje** gereklidir — aksi takdirde hem "Tümünü Kaydet" (yan çubuk) hem de sensör başına "Kaydet" düğmesi devre dışıdır.
* Dosyalar **`<project folder>/light_sensor/`** konumuna yazılır; dosya adları sensör kimliğini ve zaman damgasını içerir ve cihaz adı kayıtla birlikte saklanır.
* Kayıt durduğunda (Durdur, Tümünü Durdur veya kayıt sırasında bağlantı kesildiğinde), tamamlanan `.daq` dosyası **açık projeye otomatik olarak eklenir** — manuel olarak eklenmesine gerek kalmadan projenin dosya listesinde görünür ve [yansıma işleme](README.md) için aşağı doğru ışınım verisi olarak kullanılmaya hazırdır.
* Kayıt sırasında ayarlar penceresinin canlı satırlarında kırmızı bir `REC` göstergesi görünür.

Nicel ışık şiddeti değerleri için en az 15 saniyelik verilerin ortalamasını alın — bu, bir cihaz özelliğidir, bir kusur değildir.

<!-- SCREENSHOT-NEEDED: recording in progress — sidebar Stop All button in its red state and the settings modal live rows showing Recording: REC -->



***

## Çoklu sensör düzenleri ve proje kalıcılığı

* Bir grafikte birkaç sensörü birleştirin (ortak eksenler), ayrı grafikler tutun (otomatik ızgara düzeni), sensörleri grafikler arasında taşıyın, satırları/kutucukları sürükleyerek yeniden sıralayın ve göz simgesini kullanarak tek tek sensörleri gizleyin.
* Chloros, proje bazında şu bilgileri kalıcı olarak saklar: cihaz adları, grafik renkleri, grafik boyutu, görüntüleme modu ve her sensörün ayarları (entegrasyon süresi, kare ortalaması, AE durumu, kapak seçimi).
* **Bir proje yeniden açıldığında, sensörler adreslerine göre otomatik olarak yeniden bağlanır** — DAQ-U için COM bağlantı noktası, DAQ-M için BLE cihazı, DAQ-E için mDNS ana bilgisayar adı (cihazın IP adresi değişse bile çözümlenir)) — ve her sensörün kaydedilmiş kapak profili, kare ortalaması, AE durumu ve manuel entegrasyon süresini yeniden uygular.

***

## Kamera eşleştirme (DLS)

Eşleştirilecek bir şey yoktur. Işık sensörünü önceden kameraya bağlayan drone DLS iş akışlarının aksine, Chloros, DAQ verilerini görüntüyle son aşamada eşleştirir: içe aktarma/işleme sırasında, `.daq` okumaları her çekimin pozlama zaman damgasına göre enterpolasyon yoluyla hesaplanır. Bağlı herhangi bir sensörle kayıt yapın (`.daq`, projeye otomatik olarak eklenir) ve yansıma işleme, zamana göre doğru okumaları bulur — aşağı doğru gelen verilerin nasıl kullanıldığına ilişkin bilgi için [DAQ Işık Sensörleri](README.md) bölümüne bakın. **Canlı**LATTICE yakalamaları için, bunun yerine Kameralar sekmesindeki**Işık Sensörü** açılır menüsünden sensörü seçin; böylece her yakalama, yalnızca o yakalamanın kameralarıyla eşleşen, kullandığı okuma değerini kaydeder ([Yansıma İş Akışları](reflectance.md) bölümüne bakın).
