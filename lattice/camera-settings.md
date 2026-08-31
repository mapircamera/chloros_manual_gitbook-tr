# Kamera Ayarları

**Kameralar**sekmesi, Chloros’in LATTICE kameraları için canlı kontrol paneli: bağlı tüm kameraları canlı kutucuklar halinde gösteren bir ana görüntü alanı ve üç sayfa arasında kaydırılabilen bir kenar çubuğu —**kamera listesi**,**ayar paneli**(kamera başına, dizi veya yakalama ayarları — teker teker) ve**Dizin Hesaplayıcı**. Bu sayfa, kamera listesindeki, kamera başına ayarlar panelindeki ve dizi ayarları panelindeki tüm kontrolleri açıklamaktadır. Yakalama modları, dışa aktarma türü seçimi ve &quot;Tümünü Yakala&quot; akışı, ilgili [Yakalama Ayarları ve Modları](capture.md) sayfasında yer almaktadır.

Chloros arka ucu hazır olduğunda, yan çubukta **Kameralar** sekmesi görünür. Aşağıdaki tüm denetimler, `127.0.0.1:5000` üzerinden yerel arka uçla iletişim kurar; aksi belirtilmedikçe, yapılan değişiklikler canlı kameraya anında uygulanır.

## Bu sayfada kullanılan kamera türleri

Kontroller, seçilen kamera türüne göre görünür veya gizlenir. Kılavuzda genel olarak şu terimler kullanılmaktadır:

| Terim | Anlam | Filtre kanalları |
| --- | --- | --- |
| **RGB kamera** | FRGB filtreli LATTICE M3C (model `-FRGB` içerir) | Red / Green / Blue |
| **Bayer multispektral** | FRGN, FOCN veya FNGB özellikli LATTICE M3C | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — bir dar bant filtresi, bir kalibre edilmiş bant | Tek bant |
| **Dizi üyesi** | Senkronize bir dizinin parçası olarak bağlanmış bir kamera (birleşik veya ayrı ekran) | Filtresine göre |

RGB kameralar fotometrik işleme tabi tutulur (beyaz dengesi, renk profilleri, gama); multispektral ve mono kameralar radyometrik zinciri alır ve fotometrik kontrolleri atlar. Dizi üyeleri, akış düzeyindeki ayarları (piksel biçimi, çözünürlük, birleştirme, tetikleme, kare hızı) diziye aktarır — bu satırlar, kamera başına bölmede salt okunur hale gelir ve bunun yerine dizi ayarları bölmesine taşınır.

## Ana besleme alanı

<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Bağlı kamera olmadığında, besleme alanında **&quot;Başlamak için bir kamera bağlayın&quot;**başlıklı bir açılış ekranı ve iki düğme görüntülenir:**Kamera Bağla**(yeşil, tek kamera bağlama iletişim kutusunu açar) ve**Dizi Bağla** (mavi, dizi bağlama iletişim kutusunu açar). Bağlantı diyalog pencereleri [Kameraları Bağlama](connecting.md) bölümünde; dizi kavramları (senkronizasyon, katmanlar, bant genişliği) ise [Çoklu Kamera Dizileri](arrays.md) bölümünde açıklanmıştır. İçinde kamera bulunan kaydedilmiş bir projeyi açtığınızda, Chloros son oturumdaki akışları geri yüklerken açılış ekranında &quot;N adet kaydedilmiş kamera yeniden açılıyor…&quot; yazan bir yükleme simgesi görüntülenir.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Üst çubuk

| Kontrol | Ne işe yarar |
| --- | --- |
| **Görünüm modu geçişi**|**Izgara görünümü**(tüm kutucuklar hücre şeklinde) ile**liste görünümü** (üstte tam genişlikte diziler, altında TEK aktif kamera) arasında geçiş yapar. Araç ipuçları: &quot;Izgara görünümüne geç&quot; / &quot;Liste görünümüne geç&quot;. |
| **Izgara kilidi**(asma kilit) | Varsayılan olarak**kilitli** — karolar yerinde sabitlenir. Kilidi açarak karoları herhangi bir yuvaya sürükleyip yeniden sıralayabilirsiniz (boşluklar korunur). Yeni bir kamera bağlandığında ızgara otomatik olarak yeniden kilitlenir. Araç ipuçları: &quot;Izgaranın kilidini aç (karoları sürüklemeye izin ver)&quot; / &quot;Izgarayı kilitle (karoları yerinde sabitle)&quot;. |
| **Akış Yakınlaştırma** kaydırıcısı | Döşeme boyutu, 60 px&#x27;ten tam kapsayıcı genişliğine kadar. Hücreler 4:3 en-boy oranını korur. Hücre genişliği 200 px&#x27;in altında olduğunda, döşemeyi temiz tutmak için ad ve fps üst katmanları gizlenir. |

### Akış döşemeleri

Her kamera, birleşik bir canlı döşeme oluşturur; bir kamera ayrıca **kanal başına bölünmüş** üç gri tonlamalı döşeme gösterebilir (bkz. [Kanal Bölünmeleri](#display-overlays-drawn-over-the-live-feed)), diziler ise birleştirilmiş bir döşeme oluşturur. Etkin döşeme, kameranın (veya dizinin) renginde bir seçim halkası içerir.

Döşemenin üzerine fareyi getirdiğinizde bir **X** kapatma düğmesi görünür:

* Kanal bölünmeleri görünür durumdayken **birleştirilmiş** bir döşemeyi kapatmak, yalnızca birleştirilmiş döşemeyi gizler.
* **Tek başına çalışan bir kameranın son görünür döşemesini** kapatmak, o kameranın bağlantısını keser.
* **Birleştirilmiş dizi üyesi bölünmüş döşemeler, kameranın bağlantısını asla kesmez** — yalnızca gizler.

Izgara kilidi açıldığında, herhangi bir döşemeyi herhangi bir yuvaya sürükleyin; düzen, projeyle birlikte kaydedilir.

## Kenar Çubuğu — kamera listesi

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

İlk kenar çubuğu sayfasında, bağlı tüm kameralar ve diziler listelenir:

* **Kamera Bağla**(yeşil) /**Dizi Bağla** (mavi, tarama sırasında &quot;Algılanıyor...&quot; mesajını gösterir). Bağlantı iletişim kutusu açıkken her ikisi de devre dışıdır.
* **Tümünü Yakala** (kırmızı) — listelenen tüm kameraları, Yakalama Ayarları&#x27;nda seçilen dışa aktarma türleriyle yakalar. Açık bir proje gerektirir. [Yakalama Ayarları ve Modları](capture.md) bölümünde ayrıntılı olarak açıklanmıştır.
* **Yakalama Ayarları dişli simgesi** (Tümünü Yakala&#x27;nın yanında) — [Yakalama Ayarları bölmesini](capture.md#the-capture-settings-pane) açar. Proje yoksa veya yakalama işlemi devam ediyorsa devre dışıdır.

### Kamera satırları

Her kamera satırı, renk kodlu bir kenarlık (kameranın özel rengi), bir &quot;CAM&quot; etiketi — dizi üyeleri için mavi **M**(ana) veya yeşil**S** (bağımlı) rol harfi ile birlikte — ve görüntü adını gösterir. Varsayılan ad `LATTICE-MODEL (serial)`&#x27;tir; kamera başına ayarlar panelinden yeniden adlandırabilirsiniz. Satır düğmeleri:

| Düğme | Etki |
| --- | --- |
| **Göz**| Görünürlüğü değiştirir. Gizlenen kameralar ızgaradan çıkar ve**Tümünü Yakala**işleminden**hariç tutulur**. |
| **Dişli** | Kamera başına ayarlar panelini açar (sonraki bölüm). |
| **Duraklat / Oynat**| Canlı önizlemeyi**yalnızca ekran tarafında** dondurur — arka uçtaki yakalama işlemi devam eder. Duraklatılmış kameralar yakalama yapamaz. |
| **X** | Bağlantıyı keser. Kullanıcı arayüzü anında güncellenir (ideal durumda); arka uçtaki bağlantı kesilme işleminin tamamlanması 10–30 saniye sürebilir. |

### Dizi satırları

Bir dizi satırı, dizinin renginde bir &quot;ARRAY&quot; rozeti, dizi adı (dizi ayarlarından yeniden adlandırılabilir) ve bir **DAQ · açık/kapalı** düğmesi gösterir — dizi düzeyindeki Işık Sensörü ayarlandığında *veya* herhangi bir üye kamera başına sensöre sahip olduğunda **açık**durumdadır; araç ipucunda hangi sensörün neyi beslediği tam olarak listelenir. Üye kameralar, kendi satırlarıyla birlikte altta girintili olarak listelenir. Dizi satırı düğmeleri:**göz**(TÜM üyeleri birden gizler/gösterir),**dişli**(dizi ayarları paneli),**X**(tüm dizinin bağlantısını keser).

Dizi satırlarında ve dizi ayarları panelinde kullanılan ışık sensörü (DLS) durumunun dört durumu vardır:**kapalı**,**bekleme**(henüz spektrum yok),**aktif**(son 3 saniye içinde bir spektrum geldi) ve**eski** — 3 saniye içinde yeni spektrum yok, ancak son okuma *hala kullanılıyor* (DAQ okumaları yakalama yolunda asla zaman aşımına uğramaz).

Listeyi yeniden sıralamak için yan çubukta bağımsız kameraları ve tüm dizi gruplarını birbirlerinin üzerine sürükleyebilirsiniz; dizi üyeleri bağımsız olarak sürüklenemez.

## Kamera başına ayarlar paneli

Bir kamera satırındaki **dişli** simgesine tıklayarak açın. Panel, kamera listesinin üzerine kayar.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->

**Başlık**: kameranın**renk örneği**(tıklayarak yerel renk seçiciyi açın — kenar çubuğunun kenarlığı ve döşeme seçim halkasının rengini ayarlar),**ad**ve yanında kalem simgeli**Yeniden Adlandır**düğmesi (boş bir ad kaydedildiğinde varsayılan `MODEL (serial)` adına geri dönülür) ve kapatmak için**×** simgesi.

### Canlı histogram

Pencerenin üst kısmında, ~8 Hz&#x27;de JPEG önizlemesinden hesaplanan canlı luma histogramı bulunur. Ortalama, kameranın kendi AE ölçümüne uyması için Bayer ağırlıklıdır — (R+2G+B)/4 —.

* **Orange kesikli çizgi**= AE hedefi.**Yeniden hedeflemek için yatay olarak sürükleyin** — bırakıldığında bir komut gönderilir ve sürükleme işlemi AE hedef modunu Manuel&#x27;e geçirir.
* **Green kesintisiz çizgi** = gerçek ortalama luma (AE&#x27;nin şu anda sağladığı değer).
* **RGB düğmesi** (sağ üst): kameranın filtresine göre renklendirilmiş bant başına üst üste bindirilmiş histogramları açıp kapatır (örn. FRGN modunda: gri NIR, yeşil, kırmızı). Mono (M3M) kameralarda düğme &quot;MONO&quot; olarak görünür ve devre dışıdır — mono modunda her zaman tek bantlı luma histogramı gösterilir.
* X ekseni etiketleri, geçerli piksel formatının sensör bit derinliğine göre belirlenir: 0..255, 0..1023, 0..4095 veya 0..65535.

### Kamera bilgisi satırları

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->

| Satır | Davranış |
| --- | --- |
| **Model** | Salt okunur (örn. `LATT-M3C-L87-FRGN`). |
| **Radyometrik Kalibrasyon**| Green**&quot;Aktif&quot;**rozeti ile birlikte, kameranın kalibrasyon paketinden yüklenen kalibrasyon seviyesi, hash, kalibrasyon tarihi ve bant listesini gösteren bir açıklama (bkz. [Fabrika Radyometrik Kalibrasyon](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**RGB kameralar için gizli** — bu kameralar bant başına parlaklık yerine fotometrik beyaz dengesi kalibrasyonuna sahiptir. |
| **Kalibrasyon Raporu**|**İndir** düğmesi — kameranın seri numarasına göre düzenlenmiş NIST kalibrasyon sertifikası PDF&#x27;sini işletim sisteminizin görüntüleyicisinde açar. Sertifika henüz önbelleğe alınmamışsa, Chloros bunun yerine bir ipucu gösterir. |
| **Seri Numarası** | Salt okunur. |
| **Firmware**| Mevcut sürümü gösterir, ardından bu model için mevcut sürümü belirler (modele göre önbelleğe alınır — N kameradan oluşan bir dizi, sunucuyu bir kez kontrol eder). Durumlar: &quot;Kontrol ediliyor…&quot; →**&quot;X&#x27;e güncelle&quot;**düğmesi → &quot;Yazılıyor…&quot; → &quot;A → B&#x27;ye güncellendi&quot; / &quot;Başarısız: …&quot; / &quot;Atlandı: …&quot; / yeşil**&quot;Güncel&quot;**. Güncelleme düğmesinin araç ipucu: &quot;Fabrika ayarlarına sıfırlama + yazılım yükleme + UserSet1&#x27;i yeniden programlama. ~2–3 dakika; bağlantıyı kesmeyin.&quot; |
| **IP** | Salt okunur. |
| **Sıcaklık** | Salt okunur, her 3 saniyede bir yenilenir. ≥65 °C&#x27;de turuncuya, ⚠ simgesiyle kırmızıya döner. |
| **Kalibrasyon Hedefi** onay kutusu | Canlı akışın (liste görünümü) altında bulunan panele özel NDVI doğrulama tablosu ile ArUco yansıma hedefi algılamasını etkinleştirir. Yalnızca oturum için geçerlidir — her zaman kapalı olarak açılır. |
| **Işık Sensörü** açılır menüsü | Aşağıdan gelen ışık (DLS) aydınlatma düzeltmesi ve tahmini otomatik pozlama için bu kameraya bir DAQ ışık sensörünü (Light Sensors sekmesindeki listeden bir DAQ-E/M/U, Işık Sensörleri sekmesindeki listeden) bu kameraya bağlayarak aşağıya doğru ışık (DLS) aydınlatma düzeltmesi ve öngörülü otomatik pozlama sağlar. &quot;Yok&quot; seçeneği, bağlamayı kaldırır. Bağlı sensör yoksa açılır menüde &quot;(bağlı sensör yok — DAQ sekmesini açın)&quot; mesajı görüntülenir. Bağlantı, projeyle birlikte kaydedilir. |

### Pozlama ve Kazanç

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Buradaki tüm sayısal girişler, basılı tutularak hızlandırılan ayar çarkları kullanır: dokunma = ±1, 1,5 saniyeden fazla basılı tutma = ±10, 3 saniyeden fazla basılı tutma = ±100. Değeri, parmağınızı bıraktığınızda kameraya gönderilir.

| Kontrol | Aralık / seçenekler | Varsayılan | Uygulandığı yer | Ne yapar |
| --- | --- | --- | --- | --- |
| **Pozlama (us)**| Kameranın canlı min/maks | Otomatik | Tümü | Mikrosaniye cinsinden pozlama süresi;**Otomatik/Manuel** geçiş seçeneği mevcuttur. Otomatik = kamera tarafında sürekli otomatik pozlama. |
| **Kazanç (dB)**| Kameranın canlı min/maks değerleri (örn. 48 dB&#x27;ye kadar) | Manuel (kapalı) | Tümü | Kendi**Otomatik/Manuel** anahtarı bulunan analog/dijital kazanç. |
| **AE Hedef Parlaklığı**| 0–255 | 80,**Otomatik**modu | Tüm (AE veya otomatik kazanç açıkken düzenlenebilir) | AE&#x27;nin hedeflediği parlaklık.**Otomatik**modunda (varsayılan), histogram tabanlı bir arka uç denetleyicisi hedefi kendisi seçer ve pozlamayı sensör maksimumunun %60–75&#x27;inde tutar. Bir değer girildiğinde veya histogramın turuncu çizgisi sürüklendiğinde mod**Manuel**&#x27;e geçer. |
| **AE Yumuşatma** | 0,5–40, adım 0,1 | 8,0 | Tüm | AE sönümlemesi. Araç ipucu: &quot;Daha düşük = AE daha hızlı tepki verir (yüksek fps&#x27;de titreme yapabilir). Daha yüksek = daha yumuşak / daha yavaş.&quot; Varsayılan değerin çok altındaki değerler, yüksek kare hızlarında AE&#x27;nin titremesine ve akışın dengesizleşmesine neden olabilir; 8,0 istikrarlı varsayılan değerdir. |
| **AE İlgi Alanı**| Etkinleştirme onay kutusu +**Hedefle**düğmesi | Kapalı | Tümü | Açık olduğunda, AE tüm karenin yerine yalnızca yeşil kesikli bölgeyi ölçer.**Nişan Al**, canlı yayında tıklayarak yerleştirme özelliğini etkinleştirir: bir tıklama, bölgenin çerçevenin %30&#x27;una ortalanmasını sağlar; tıklayıp sürükleme, özel bir dikdörtgen (en az %5 × %5) oluşturur. Aim, bir kez yerleştirme yapıldıktan sonra kendiliğinden devre dışı kalır. Bölge, ayarladığınız herhangi bir döndürme/yansıtma altında kameranın kendi koordinatlarına eşlenir ve projeyle birlikte kaydedilir. |
| **AE Ayar Hızı** | 0,1–5, 0,1&#x27;lik adımlarla | 1,0 | Yalnızca dizi üyeleri | Otomatik AE hedefinin sahne parlaklığı değişikliklerini ne kadar hızlı takip edeceği; 1,0× ayarında her 2,5 saniyede bir yeniden kontrol edilir. |
| **Vurgu Koruması** | Sıkı (%1) / Normal (%5) / Geniş (%15) | Sıkı | Bu ayarı destekleyen kameralar | AE görüntüyü karartmadan önce karenin ne kadarının beyaza kesilebileceğini belirler. |

{% hint style="info" %}
**Bayer multispektral kameralar için ışık gereksinimi (RGN / OCN / NGB):** sahnenin üç kanalda da yeterli ışığa sahip olması gerekir, aksi takdirde kalibrasyon düzgün çalışmaz — tek bir sensör pozlaması üç spektrumu da kapsar. Işığı ölçmek için bir DAQ ışık sensörü kullanın veya tamamen-mono (M3M) moduna geçerek her bandın kendi pozlamasını almasını sağlayın. Bir çekim bu kuralı ihlal ederse, Chloros bunu algılar ve sizi uyarır (unmix-clamp bildirimi).
{% endhint %}

### Piksel Biçimi ve

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

Çözünürlük**Dizi üyeleri**, &quot;Dizi ayarlarında belirlenir&quot; notuyla birlikte salt okunur &quot;Mevcut&quot; (format + GxY) ve &quot;Binning&quot; satırlarını gösterir — bir üyede akışın yeniden başlatılması senkronizasyonu bozacağından, bunlar [dizi ayarları bölmesinde](#array-settings-pane) yönetilir.**Bağımsız kameralar** şunları alır:

| Kontrol | Seçenekler | Ne işe yarar |
| --- | --- | --- |
| **Piksel Formatı** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Sensör piksel formatı (bit derinliği). |
| **Çözünürlük** | Tam / Yarım / Çeyrek | Mevcut birleştirme oranına göre: N×N birleştirme için Tam = 2048/N × 1536/N. |
| **Binning** | 1x1 (yok) / 2x2 / 4x4 | Donanım N×N binning — daha büyük değerler çözünürlüğü düşürür ancak SNR ve kare hızını artırır. Değiştirildiğinde akış yeniden başlatılır ve tüm ROI&#x27;ler yeni tam görüş alanına sıfırlanır. |
| **Mevcut** | salt okunur | Etkili olan gerçek GxY ve (x, y) ofseti. |

### Canlı Önizleme

Bu bölümdeki her şey **yalnızca görüntüleme tarafındadır**— canlı yayında gördüklerinizi değiştirir, ancak kaydedilen görüntüler doğrusal ve değiştirilmemiş kalır — tek bir istisna dışında:**Vignette** radyometrik olup dışa aktarmaları da etkiler (aşağıda belirtilmiştir).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->

| Kontrol | Aralık / seçenekler | Varsayılan | Uygulandığı yer | Ne yapar |
| --- | --- | --- | --- | --- |
| **Render çözünürlüğü** | 360p (en hızlı) / 480p / 720p / 1080p / Orijinal sensör çözünürlüğü (en yavaş) | 720p | Tüm | Arka uçta radyometrik önizleme zincirinin çalıştığı yükseklik. Daha düşük değerler, görüş alanını değiştirmeden kare hızını artırır. |
| **Endeks**| Etkinleştirme kutucuğu + dişli simgesi | Kapalı | Yalnızca Bayer multispektral,**birleşik dizi** üyeleri hariç | Canlı bitki örtüsü endeksi önizlemesi. Dişli simgesi, kameranın filtre-doğal bantlarıyla önceden yüklenmiş paylaşılan [Endeks Hesaplayıcı](#index-calculator-pane) penceresini açar (örn. `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`)ile önceden yüklenmiş [Endeks Hesaplayıcı](#index-calculator-pane) penceresi açar. Özel ifade artı LUT (açık/kapalı, varsayılan seviye 3, varsayılan minimum 0,2, varsayılan maksimum 1) her önizleme karesinde hesaplanır. Kombine dizi üyeleri bu satırı gizler — dizi tek bir paylaşılan endekse sahiptir. |
| **Beyaz Dengesi** | Kapalı / Tek Seferlik / Sürekli + yeniden yakalama düğmesi | Sürekli | Yalnızca RGB | Canlı beyaz dengesi. Yenileme düğmesi, mevcut DLS spektrumundan beyaz dengesini yeniden yakalar (mod Kapalı olduğunda devre dışıdır). |
| **Gama** | Açık / Kapalı | Açık | Yalnızca RGB | Canlı önizlemede gama (γ = 2,2 LUT) gösterilir. Kaydedilen çekimler doğrusal kalır. |
| **Gürültü Giderme** | Onay kutusu + güç 0–100 | Kapalı / 50 | Tüm (kamera başına, diziler içinde bile) | Canlı önizlemede iki yönlü filtre. Daha yüksek değer = daha pürüzsüz ancak daha yumuşak ayrıntılar. |
| **Keskinlik** | Onay kutusu + güç 0–100 | Kapalı / 30 | Tüm | Canlı önizlemede keskinleştirme maskesi, en son uygulanır. Gürültüyü artırabilir. Yalnızca önizleme. |
| **Vinyet**| Onay kutusu + güç 0–100 | Kapalı / 0 | Tüm | Manuel kalıntı vinyet giderme (köşeleri aydınlatır), dizinin Akıllı Vinyet tahmininin üzerine katmanlanır.**Radyometrik — Gürültü Giderme/Keskinlik&#x27;ten farklı olarak canlı görüntüyü VE dışa aktarmaları etkiler**. |
| **Renk Profili** | Raw / Doğrusal / Doğal / Geliştirilmiş / Özel Sıcaklık | Doğal | Yalnızca RGB | Aşağıya bakın. |
| **Renk Sıcaklığı** | 2000–10000 K, 100&#x27;lük adımlar | 5500 K | Yalnızca RGB, Özel Sıcaklık profili | Beyaz dengesini sabit bir korelasyonlu renk sıcaklığına sabitler (DLS girişi göz ardı edilir). En son seçilen Kelvin değeri, profil değişiklikleri arasında hatırlanır. |
| **Doygunluk** | 0–200 (100 = nötr) | 100 | Yalnızca RGB | Canlı önizlemede HSV doygunluğu. |
| **Kontrast** | 0–200 (100 = nötr) | 100 | Yalnızca RGB | Canlı önizlemede orta gri civarında doğrusal kontrast. |
| **Yatay Yansıtma / Dikey Yansıtma** | Onay kutuları | Kapalı | Tümü | Önizlemeyi yatay / dikey olarak çevirir. |
| **Dönüş**| 0° / 90° / 180° / 270° | 0° | Tümü | Önizlemeyi döndürür. Yönlendirme, arka uç önizleme zincirinin sonunda uygulanır —**kaydedilen çekimler kameranın kendi yöneliminde kalır** ve dizi birleştirme görünümleri bunu göz ardı eder. |**Renk profili semantiği** (RGB kameralar):

* **Raw** — işleme zincirini tamamen atlar.
* **Doğrusal** — karanlık sinyal + düz alan + beyaz dengesi; renk matrisi yok, gama yok.
* **Doğal** *(varsayılan)* — doğrusal artı ölçülen renk düzeltme matrisi ve sahneye uyarlanabilir ton eğrisi.
* **Geliştirilmiş**— Doğal artı canlılık ve CLAHE yerel kontrastı. Ek maliyet**yalnızca canlı önizleme** için geçerlidir — kaydedilen çekimler, profilden bağımsız olarak her zaman tam son halini alır.
* **Özel Sıcaklık** — Doğal, beyaz dengesi seçtiğiniz Kelvin değerine sabitlenmiş.

{% hint style="warning" %}
Doğal, Geliştirilmiş ve Özel Sıcaklık seçeneklerinde panelde bir ton notu gösterilir: kareler kendi sahnelerine göre parlaklaştırılır, bu nedenle kaydedilen *ekran* görüntüleri kare bazında karşılaştırılamaz. **Ölçümler için parlaklık veya yansıma değerlerini dışa aktarın.**
{% endhint %}

### Ekran üstü katmanlar (canlı akışın üzerine çizilir)

Bunlar yalnızca ön uçtadır — videonun üzerine çizilir, akışa veya yakalamalara asla dokunmaz.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Katman | Kontroller | Varsayılan | Ne yapar |
| --- | --- | --- | --- |
| **Zebra** | Onay kutusu + eşik 200–255 | Kapalı / 250 | Kırpılmış pikseller üzerinde macenta renkli diyagonal şeritler. |
| **Hedef Çizgisi** | Onay kutusu | Kapalı | Kare merkezi işareti. |
| **Izgara** | Kapalı / 3 × 3 / 9 × 9 | Kapalı | Kompozisyon ızgarası. |
| **Histogram** | Onay kutusu + karenin genişliğinin 0,10–0,90&#x27;ı | Kapalı / 0,25 | Canlı yayında görüntülenen histogram şeridi. |
| **Odak Zirvesi** | Onay kutusu + eşik 20–200 + renk örneği | Kapalı / 80 / `#ff5722` | Odaklama için Sobel kenar vurgusu. |
| **Kanal Bölünmeleri** | &quot;Bölünmeleri Göster (Red / Green / NIR)&quot; / &quot;Bölümleri Gizle&quot; düğmesi | Gizli | Kompozitin yanına kanal başına üç bağımsız gri tonlamalı döşeme ekler (düğme etiketi, kameranın filtre kanallarını takip eder). Her bir bölünmüş döşeme sürülebilir ve kameranın kenarlık rengini alır. Monokrom kameralarda kullanılamaz. Projeyle birlikte kaydedilir. |

### Nokta Ölçer

* **Tıklayarak Örnek Al**onay kutusu: Canlı görüntüyü tıklayarak tek bir pikselden örnek alın (bir artı işareti ile işaretlenir) veya bir bölgeyi tıklayıp sürükleyerek piksel ortalamasını alın.**Temizle**, örneklemeyi ve artı işaretini siler. AE-ROI**Nişan** modu ile birbirini dışlar.
* **Göster**açılır menüsü:**Ham (bit derinliği)**— sensörün bit derinliğindeki doğal dijital sayılar (örn. 12 bit → 0..4095) — veya**Ekran (8 bit)** (varsayılan). Canlı bir indeks etkin olduğunda, Ekran yerine hesaplanan indeks değerini gösterir (örn. NDVI) gösterir.
* Okuma paneli, piksel koordinatlarını, kare boyutunu, piksel formatını, bit derinliğini ve bant etiketleri ile dalga boylarını içeren bir kanal tablosunu (Chan / Değer / %) listeler; Bayer yeşil çiftlerinin ortalaması alınır; bölge örnekleri &quot;N px avg&quot; olarak gösterilir.

Nokta ölçer durumu yalnızca oturum süresince geçerlidir.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Tahmine Dayalı Otomatik Pozlama (DLS tabanlı)

Bu bölüm, **en az bir DAQ ışık sensörü bağlı olduğunda** görünür — çözücünün bunu çalıştırması için canlı bir aşağı doğru yayılan spektruma ihtiyacı vardır.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->

| Kontrol | Aralık | Varsayılan | Ne yapar |
| --- | --- | --- | --- |
| **Etkinleştir** | Onay kutusu | Açık (bağımsız kameralar) | Kapalı formlu bir çözücü, DLS spektrumunu ve kameranın kalibrasyon paketi skalerlerini kullanarak en parlak bandı doygunluğa yakın bir seviyeye getirirken en sönük bandı SNR tabanının üzerinde tutar — her çözüm için tek bir pozlama yazımı, yerleşme döngüsü yoktur. Her çekimin doğru pozlanmasının gerekli olduğu güneşzaman atlamalı çekimler için tasarlanmıştır; burada her çekimin doğru pozlanması gerekir. DLS okuması eski/eksik olduğunda veya kalibrasyon paketi yüklenmediğinde arka uç, fark edilmeyecek şekilde reaktif AE&#x27;ye geri döner. |
| **Düzgünleştirme (α)** | 0,05–1,0, adım 0,05 | 0,3 | Ardışık tahmin çözümlerinin düzgünleştirilmesi (düşük değer = daha düzgün). |
| **Sahne yansıma oranı**|**ρ&#x27;yi Yeniden Kalibre Et** düğmesi | — | Çözücünün kullandığı sahne yansıma oranını yeniden tahmin eder. |

{% hint style="info" %}
**Dizi bağlantısı, tahminli AE&#x27;yi varsayılan olarak devre dışı bırakır** — diziler için, Chloros&#x27;in akıllı AE ve kamera tarafındaki otomatik pozlama özelliği, pozlamayı (doygunluk korumasıyla birlikte) yönetir ve tahmini AE&#x27;nin tek sahne yansıma katsayısı tahmini, karışık sahnelerde güvenilir değildir. Özellikle DLS tabanlı radyometrik pozlama istiyorsanız, buradan kamera bazında bu özelliği yeniden etkinleştirebilirsiniz.
{% endhint %}

**DAQ tabanlı pozlama tavanı ve gelen ışığa sabitlenmiş AE.**Yukarıdaki onay kutusundan bağımsız olarak, bir DAQ ışık sensörü bir RGB kamerasına atandığında, Chloros — ölçülen mutlak aşağıya doğru ışık şiddetinden — %100 yansıtma oranına sahip bir yüzeyin kırpılma eşiğinin altında kalacağı maksimum pozlama×kazanç değerini hesaplar ve bunu**üst sınır**uygular. Tavan değeri etkinken kamera**gelen ışığa sabitlenir**: 0 dB kazançla gelen ışık ölçümüne göre açık döngüde çalışır — pozlama sahne içeriğini değil, ölçülen ışığı takip eder. Tavan değeri yalnızca pozlamayı kısaltabileceğinden, kendi başına kırpma oluşturmaz. DAQ okuması eksik, eski (&gt;30 s) veya karanlıksa ya da karenin ≥%15’i sabitlenmiş pozlamada kırpılıyorsa (yani sensör ve kamera farklı aydınlatma görüyorlarsa), tavan otomatik olarak devre dışı kalır ve normal sahne AE’si devam eder. GUI’de bir anahtar yoktur; bu, bir RGB kameranın DAQ bağlantısı olduğunda standart davranıştır.

### Toplama ve Tetikleme

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

Dizisi üyeleri ayrıca salt okunur **Rol**(Mavi renkli Ana / Yeşil renkli Bağımlı),**Senkronizasyon Hattı**ve**Eşler** satırlarını gösterir.

| Kontrol | Seçenekler | Varsayılan | Notlar |
| --- | --- | --- | --- |
| **Tetikleme Modu** | Kapalı / Açık | Açık | Dizi üyeleri için devre dışıdır (tetiklemeyi dizi yönetir). |
| **Tetikleme Kaynağı** | Yazılım / Hat0 (M8) / Hat1 / Hat2 | Hat0 | Tetikleme Modu Kapalıyken gizlenir; dizi üyeleri için devre dışıdır. Hat0, M8 opto-izole edilmiş harici tetikleme girişidir. |
| **Kare Hızı**| Otomatik / Manuel + değer | Otomatik |**Otomatik**: Kameranın kare hızı sınırlaması devre dışıdır — pozlama fps&#x27;yi belirler ve kutuda canlı gerçek hız gösterilir.**Manuel**: Bir kaydırıcıyla fps&#x27;yi sınırlarsınız (1&#x27;den bant genişliği sınırlı maksimum değere kadar), mevcut gerçek hızdan yola çıkılarak belirlenir. Dizi üyeleri, “Dizi ayarlarında ayarla” seçeneği ile&quot;N fps (canlı)&quot; değerini görürler ve yanında &quot;Dizi ayarlarında ayarlanır&quot; yazısı bulunur. |

### Ağ / Aktarım

| Satır | Davranış |
| --- | --- |
| **Paket Boyutu**| 1500 (Standart) / 9000 (Jumbo) — varsayılan**Jumbo**. |
| **Aktarım Hızı** | MB/s cinsinden salt okunur bağlantı aktarım hızı sınırı. Arka uç, her bağlantı kurulduğunda veya kesildiğinde bunu bağlı tüm kameralar arasında yeniden dengeler. |
| **Tampon İşleme** | Salt okunur tampon işleme modu. |

### Yakalama

Pencere, [Yakalama Ayarları penceresine](capture.md#the-capture-settings-pane) yönlendiren bir **&quot;Yakalama Ayarlarını Aç…&quot;** düğmesiyle sona erer (bir proje açılana kadar devre dışıdır — &quot;Yakalamaları kaydetmek için bir proje oluşturun veya açın&quot;). Kamera gizlenmiş veya duraklatılmışsa, bir ipucu size yakalama işleminden önce kamerayı görünür hale getirmenizi/devam ettirmenizi hatırlatır.

## Dizi ayarları bölmesi

Bir ARRAY satırındaki **dişli**simgesine tıklayarak açın. Başlık: yeniden adlandırma kalemi simgesi bulunan dizi adı ve kapatmak için**×** simgesi. Aşağıda *yalnızca birleştirilmiş* olarak işaretlenen bölümler, yalnızca birleştirilmiş görüntüleme modunda bağlanmış diziler için görünür.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->

### Senkronizasyon

Salt okunur **Ana**,**Bağımlı**ve**Senkronizasyon Hattı** satırları.

### Ortam Işığı Sensörü

Hem birleştirilmiş hem de ayrı diziler için gösterilir:

* **Kalibrasyon Hedefi** onay kutusu — &quot;MAPIR ArUco hedefini algıla ve NDVI&#x27;i panel yansıma LUT&#x27;siyle karşılaştır&quot;; birleştirilmiş döşemenin hedef katmanını ve doğrulama tablosunu yönlendirir.
* **Işık Sensörü** açılır menüsü — bir DAQ&#x27;yı tüm diziye bağlar. Seçim anında geçerlilik kazanır, her üye kameranın kendi Işık Sensörü açılır menüsüne yayılır (yine de kamera bazında geçersiz kılabilirsiniz) ve spektrumları diziye iletmeye başlar.
* Canlı **Durum** satırı: Kapalı · &quot;İlk spektrum bekleniyor…&quot; · &quot;Etkin — dizideki tüm kameralar aydınlatma düzeltmesinden geçmiştir&quot; · &quot;Son 3 saniyede yeni spektrum yok — hâlâ son okuma kullanılıyor (eski veri zaman aşımı yok)…&quot;.
* Bölmedeki not: &quot;Dizi genelinde radyometrik düzeltme. Kamera başına ayarlar bunu geçersiz kılar.&quot;

### Yakalama — tek tip sensör ayarları *(sadece birleştirilmiş)*

Bu ayarlar her üyeye tek tip olarak uygulanır (üye başına yapılan değişiklikler senkronizasyonu bozar). Düzenlemeler aşamalı olarak yapılır ve toplu olarak uygulanır.

<!-- SCREENSHOT-NEEDED: array settings Capture section — Pixel Format, Binning, Resolution preset, the ROI crop W/H/X/Y fields with the "max WxH" hint and Reset button, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons; ideally with the live orange crop-preview box visible on the array tile. -->

| Kontrol | Seçenekler / aralık | Ne işe yarar |
| --- | --- | --- |
| **Piksel Biçimi** | BayerRG8 / BayerRG10 / BayerRG12 / BayerRG16 / Mono8 | Tüm üyeler için tek tip sensör biçimi. |
| **Binning** | 1x1 / 2x2 / 4x4 | Donanım binning — SNR ve kare hızını artırırken tam görüş alanını korur. Değiştirildiğinde ROI alanları yeni tam görüş alanına göre sıfırlanır. |
| **Çözünürlük** ön ayarı | Tam / Yarım / Çeyrek | Binning&#x27;e göre; ROI alanlarını ortalanmış bir kırpma ile doldurur. |
| **ROI kırpma (px)**| G / Y / X / Y sayısal alanlar | Sensör kırpma. Genişlik/yükseklik, 16&#x27;nın katlarına (en az 64) hizalanır; ofsetler ise 4&#x27;ün katlarına hizalanır. Bir &quot;maks. GxY&quot; ipucu, üst sınırı gösterir ve**Sıfırla** seçeneği tam görüş alanına geri döner. Düzenleme sırasında, dizi karesine canlı turuncu bir kırpma önizleme kutusu çizilir (kırpma alanı dışa doğru genişletildiğinde tam sensör şeması da dahil edilir). |
| **Tetikleme Hızı**| Otomatik / Manuel geçiş + fps 0,5–10, adım 0,5 |**Otomatik**(varsayılan): arka uç, çözünürlük ve bant genişliğinden tetikleme hızını hesaplar — giriş devre dışı bırakılır ve hesaplanan değer gösterilir.**Manuel**:**Uygula**&#x27;ya tıkladığınızda değeri sabitler. |

Pencerede not: &quot;Biçim/çözünürlük değişiklikleri tüm kameraları kısa süreliğine yeniden başlatır. Tetikleme hızı anında uygulanır.&quot; **Uygula / İptal** düğmeleri pencerenin altında yer alır.

### Hizalama (ortak kayıt) *(yalnızca birleştirilmiş)*

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Kalan hata** kutucuğu: &quot;RMS x.xx px&quot; — 1 px&#x27;in altında yeşil, 3 px&#x27;in altında sarı, aksi durumda veya herhangi bir kamera hatası varsa kırmızı; ilk çözümlemeden önce &quot;profil yok&quot;.
* Özet satırı: &quot;✓ Tüm kameralar hizalandı (N)&quot; / &quot;⚠ p/N kamera hizalandı —  <serial (filter)="">başarısız&quot; / &quot;Kırpma etkin — Hizalamak için yeniden kalibre edin (tam sensör kullanılır)&quot; / &quot;Pozlamanın sabitlenmesini bekliyor…&quot;.
* Kamera başına tablo: kamera (seri numarasının son 4 hanesi + filtre), px cinsinden yeniden projeksiyon hatası ve eşleşme sayısı (ana kamera için &quot;ref&quot;) ile 0,35 geçme eşiğine göre normalleştirilmiş çapraz korelasyon çakışma puanı.
* **Hizalamayı yeniden kalibre et** düğmesi (ilk profilden önce &quot;Hizalamayı kalibre et&quot; yazısı görünür) — yeni karelerde eşleştirmeyi yeniden çalıştırır.
* **&quot;Hizalama için kameraları otomatik pozla&quot;** onay kutusu (varsayılan olarak işaretlidir) — karanlık veya düz kameraları geçici olarak aydınlatır (önce pozlama, ardından kazanç), böylece eşleşecek dokuya sahip olmalarını sağlar, ardından AE&#x27;yi geri yükler.

Birleştirilmiş önizleme açıldığında otomatik olarak hizalanır; odak veya sahne derinliği değiştiyse yeniden kalibre edin. Hizalama **tasarım gereği yalnızca oturum içindir** — o anki sahne mesafesine bağlı olduğu için hiçbir zaman bir profile kaydedilmez. Yakalamalar yine de piksel kayıtlı olarak dışa aktarılabilir (bkz. [Hizalanmış dışa aktarmalar](capture.md#per-array-controls)).

### Akıllı Vinyet

* **Düzeltmeyi etkinleştir**onay kutusu — kamera başına vinyet tahminini radyometrik zincire uygular (canlı**ve** dışa aktarmalar).
* **Mevcut görünümden kalibre et**— önce diziyi tekdüze bir hedefe (düz panel, duvar veya gökyüzü) yöneltin; her kamera ayrı ayrı düzleştirilir ve durum raporu &quot;n/N kamera · −x,x %&quot; düzlük kazancı gösterir.**Temizle** seçeneği, tahmini kaldırır.
* [Canlı Önizleme](#live-preview) bölümündeki kamera başına **Vignette** kaydırma çubuğunu kullanarak kamera başına ince ayar yapın.

### Canlı Önizleme *(yalnızca birleştirilmiş)** **Dizin**: onay kutusunu ve dişli simgesini etkinleştirin —**tüm** üye kameralardan alınan bantlarla paylaşılan [Dizin Hesaplayıcı](#index-calculator-pane) açılır. Altındaki ifade önizleme satırı, her saniye yenilenen mevcut ifadeyi gösterir (&quot;İfade ayarlanmadı — bir ifade oluşturmak için hesaplayıcıyı açın&quot;).
* **Render çözünürlüğü**açılır menüsü (kamera başına ayarlarla aynı ön ayarlar, varsayılan 720p): canlı görüntü akışının yüksekliği**ve** kaydedilen birleştirilmiş dışa aktarım boyutu. Paneldeki not: &quot;Önizleme + kaydedilen birleştirilmiş boyut. Kamera başına görüntüler her zaman tam çözünürlükte dışa aktarılır.&quot;

### Görüntü Katmanları *(yalnızca birleştirilmiş)** **Etkinleştir** onay kutusu (varsayılan olarak kapalı — ana kamera düz olarak gösterilir; açık = katmanlı birleştirilmiş görüntü).
* **Ön Plan**/**Arka Plan**açılır menüleri: her üye kamera (adına göre) veya**Dizin**. Ön Plan seçeneği Dizin olarak ayarlandığında, LUT Min/Maks değerlerinin dışındaki pikseller Arka Plan katmanını gösterir.

### Bölünmüş Görünüm *(yalnızca birleştirilmiş)*

**&quot;Üye kameraları göster&quot;**— her üyenin kendi canlı akışını, birleşik görüntünün yanına ayrı ızgara kareleri olarak ekleyen bir**Üye kameraları böl / gizle** düğmesi. Kareler, dizinin mevcut kare tamponunu okur (ekstra kamera bağlantısı gerekmez). Yalnızca ızgara görünümü; projeyle birlikte dizi başına kaydedilir.

### Özellikler

Her 5 saniyede bir yenilenen, salt okunur bir panel:

* **Katman etiketi**: &quot;Eşzamanlı yakalama&quot; (yeşil) · &quot;Eşzamanlı yakalama (FTD-kademeli yayın)&quot; (yeşil) · &quot;Kademeli yakalama (100 ms sapma)&quot; (sarı) · &quot;Yapılandırma çok büyük&quot; (kırmızı).
* **Çerçeve durumu**: &quot;x,xx % eksik&quot; — %1&#x27;in altında yeşil, %5&#x27;in altında sarı, %5 ve üzeri kırmızı.
* **Bağlantı hattı**: &quot;NIC {mbps} Mbps - sürekli {MB/s} MB/s&quot;.

Bu, dizinin anlık bant genişliği bütçesidir. Temel fps ve ağ modeli ile katman sarı veya kırmızıya geçtiğinde neyin değiştirilmesi gerektiği hakkında bilgi için bkz. [Çoklu Kamera Dizileri](arrays.md) ve [CLI Referansı](../reference/cli-reference.md) bölümlerine bakın.



<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->## Dizin Hesaplayıcı bölmesi

Üçüncü kenar çubuğu sayfası, kamera başına Dizin ayar dişlisi ile birleştirilmiş dizi Dizin ayar dişlisi tarafından paylaşılır (her seferinde biri — başlık &quot;Dizin Hesaplayıcı — <camera name="">&quot; veya &quot;Dizin Hesaplayıcı —<array name="">

&quot;</array></camera> şeklinde görünür<camera name=""><array name="">

). Bu sayfa, bant listesini (kameranın filtreye göre doğal bantları veya dizinin tüm üyelerindeki tüm bantlar), geçerli ifade ve LUT yapılandırmasını (açık/kapalı, seviye — varsayılan 3, min — varsayılan 0,2, maks — varsayılan 1) ve ayrıca canlı bir indeks histogramı alır. **Uygula** düğmesi ifadeyi uygular; LUT değişiklikleri önizlemeye anında yansıtılır.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Kamera başına ayarlar ve dizi tarafından yönetilen ayarlar

Bir kamera dizi üyesi olduğunda nelerin nerede yer aldığına dair hızlı başvuru:

| Dizi tarafından yönetilen (kamera panelinde salt okunur) | Dizi içinde hâlâ kamera başına |
| --- | --- |
| Piksel biçimi, çözünürlük, birleştirme | Otomatik pozlama (pozlama, kazanç, hedef, yumuşatma, ROI) |
| Tetikleme modu/kaynağı, kare hızı | Gürültü giderme, keskinlik, vinyet |
| | Yönlendirme (ayna/dönüş), ekran üstü katmanlar, nokta ölçer |
| | Dizin (ayrı ekranlı diziler), ışık sensörü bağlama |

Diğer genel davranışlar:

* **Birleştirilmiş ve ayrı görüntüleme**, dizi bağlantısı sırasında seçilir: birleştirilmiş = tek bir hizalanmış bileşik döşeme (üye, yalnızca Bölünmüş Görünüm aracılığıyla besleme yapar); ayrı = her üye kendi senkronize döşemesini oluşturur. Bir kamera, asla hem bağımsız bir beslemeyi hem de bir dizi döşemesini göstermez.
* **Otomatik yeniden bağlanma**: Kaydedilmiş bir proje açıldığında, kameralar ve diziler geri yüklenir ve akışlar yeniden başlamadan önce kaydedilmiş tüm ayarlar arka uca yeniden uygulanır.
* **Yakalama filtreleme**: Gizlenmiş veya duraklatılmış kameralar, &quot;Tümünü Yakala&quot; seçeneğinden hariç tutulur; bir dizi, yalnızca TÜM üyeler gizlendiğinde veya duraklatıldığında tamamen engellenir. Bkz. [Yakalama Ayarları ve Modları](capture.md) bölümüne bakın.

## Ayarların Kalıcılığı

Kamera sekmesinin durumu, tarayıcıda değil **projeyle birlikte** kaydedilir:

* Her reaktif değişiklik, kameraların ve dizilerin anlık görüntüsünü projenin `cameras.json` dosyasına kaydeder (500 ms gecikmeyle). Bu, kamera adlarını ve renklerini, pozlama/kazanç/AE ayarlarını, piksel formatını/çözünürlüğünü/binning&#x27;i, tetikleme hızını, önizleme ayarlarını (işleme çözünürlüğü, gürültü giderme, keskinlik, vinyet, renk profili, doygunluk/kontrast), yönlendirmeyi, üst üste bindirmeleri, kanal bölünmelerini, dizin yapılandırmasını, öngörülü AE ayarlarını, AE ROI&#x27;yi, dizi adları, görüntüleme modu, dizi yakalama ayarları (ROI kırpma konumu dahil) ve ızgara bloğunu (besleme yakınlaştırma, görüntüleme modu, ızgara kilidi, manuel döşeme sırası, gizli kameralar, kapalı döşemeler, etkin kamera) kapsar.
* Işık sensörü bağlamaları projenin `sensors.json` dosyasına kaydedilir.
* Projenin yeniden açılması, donanımı yeniden bağlar ve tüm ayarları yeniden uygular.
* **Proje açık değil = yalnızca oturum**: proje yoksa, Chloros kapatıldığında hiçbir ayar korunmaz.
* Projeden bağımsız olarak yalnızca oturum: duraklatma durumu, spot ölçüm örnekleri, kamera başına Kalibrasyon Hedefi onay kutusu (her zaman kapalı olarak açılır) ve dizi hizalama profili (tasarım gereği oturum başına yeniden hesaplanır).
* Tek istisna: **Çekim Ayarları** dışa aktarma seçimleri ve çekim modu, `cameras.json` yerine yerel uygulama depolama alanında proje başına korunur — bkz. [Çekim Ayarları ve Modları](capture.md).</array></camera></serial>
