# Kamera Ayarları

**Kameralar** sekmesi, Chloros’un LATTICE kameraları için canlı kontrol arayüzüdür: bağlı tüm kameraları canlı kutucuklar halinde gösteren bir ana görüntü alanı ve üç sayfa arasında geçiş yapabilen bir kenar çubuğu — **kamera listesi**,**ayarlar paneli**(kamera başına, dizi veya yakalama ayarları — her seferinde bir tanesi) ve**Dizin Hesaplayıcı**. Bu sayfa, kamera listesindeki, kamera başına ayarlar panelindeki ve dizi ayarları panelindeki her bir kontrolü açıklamaktadır. Yakalama modları, dışa aktarma türü seçimi ve Tümünü Yakala akışı ise [Yakalama Ayarları ve Modları](capture.md) adlı ilgili sayfada yer almaktadır.

Chloros arka ucu hazır olduğunda Kameralar sekmesi kenar çubuğunda görünür. Aşağıdaki tüm kontroller, `127.0.0.1:5000` üzerinden yerel arka uçla iletişim kurar; aksi belirtilmedikçe değişiklikler canlı kamera görüntüsüne anında yansıtılır.

## Bu sayfada kullanılan kamera türleri

Kontroller, seçilen kamera türüne göre görünür veya gizlenir. Kılavuzda genel olarak şu terimler kullanılmaktadır:

| Terim | Anlam | Filtre kanalları |
| --- | --- | --- |
| **RGB kamera** | FRGB filtreli LATTICE M3C (model numarası `-FRGB` içerir) | Red / Green / Blue |
| **Bayer multispektral** | FRGN, FOCN veya FNGB özellikli LATTICE M3C | FRGN: Red / Green / NIR · FOCN: Orange / Cyan / NIR · FNGB: NIR / Green / Blue |
| **Mono (M3M)** | LATTICE M3M — bir dar bant filtre, bir kalibre edilmiş bant | Tek bant |
| **Dizi üyesi** | Senkronize bir dizinin parçası olarak bağlanmış bir kamera (birleşik veya ayrı ekran) | Filtresine göre |

 RGB kameralar fotometrik işleme tabi tutulur (beyaz dengesi, renk profilleri, gama); multispektral ve mono kameralar radyometrik işleme zincirinden geçer ve fotometrik kontrolleri atlar. Dizi üyeleri, akış düzeyindeki ayarları (piksel biçimi, çözünürlük, binning, tetikleme, kare hızı) ayarlarını diziye devreder — bu satırlar kamera başına bölmede salt okunur hale gelir ve bunun yerine dizi ayarları bölmesine taşınır.

## Ana besleme alanı



<!-- SCREENSHOT-NEEDED: Cameras tab with 2+ cameras connected in grid view — live tiles visible with name and fps overlays, sidebar camera list open on the right. -->

Bağlı kamera olmadığında, besleme alanında iki düğme içeren **"Başlamak için bir kamera bağlayın"**açılış ekranı görüntülenir:**Kamera Bağla**(yeşil, tek kamera bağlantı iletişim kutusunu açar) ve**Diziyi Bağla** (mavi, dizi bağlantı iletişim kutusunu açar). Bağlantı iletişim kutuları [Kameraları Bağlama](connecting.md) bölümünde; dizi kavramları (senkronizasyon, katmanlar, bant genişliği) ise [Çoklu Kamera Dizileri](arrays.md) bölümünde açıklanmıştır. İçinde kamera bulunan kaydedilmiş bir projeyi açtığınızda, Chloros son oturumdan akışları geri yüklerken açılış ekranında "N adet kaydedilmiş kamera yeniden açılıyor…" yazan bir yükleme simgesi görüntülenir.

<!-- SCREENSHOT-NEEDED: Cameras tab empty state — the "Connect a camera to get started" splash with the green Connect Camera and blue Connect Array buttons. -->

### Üst çubuk

| Kontrol | Ne işe yarar |
| --- | --- |
| **Görünüm modu geçişi**|**Izgara görünümü**(tüm döşemeler hücre olarak) ile**liste görünümü**(tam genişlikte satırlar: her bir birleşik dizi tek bir bileşik satır olarak,**ayrı** bir dizinin her bir üyesi kendi akışı ve Kalibrasyon Hedefi tablosuyla kendi satırında, ardından altında TEK bir bağımsız kamera — etkin olan —) arasında geçiş yapar. Araç ipuçları: "Izgara görünümüne geç" / "Liste görünümüne geç". |
| **Izgara kilidi**(asma kilit) | Varsayılan olarak**kilitli** — döşemeler yerinde sabitlenir. Kilidi açarak döşemeleri herhangi bir yuvaya sürükleyip yeniden sıralayabilirsiniz (boşluklar korunur). Her yeni kamera bağlandığında ızgara otomatik olarak yeniden kilitlenir. Araç ipuçları: "Izgarayı kilidini aç (karo sürüklemeyi etkinleştir)" / "Izgarayı kilitle (karoları yerinde sabitle)". |
| **Yayın Yakınlaştırma** kaydırıcısı | Karo boyutu, 60 px'ten tam konteyner genişliğine kadar. Hücreler 4:3 en-boy oranını korur. Hücre genişliği 200 px'in altında olduğunda, döşemeyi temiz tutmak için ad ve fps katmanları gizlenir. |

### Akış döşemeleri

Her kamera, birleştirilmiş bir canlı döşeme oluşturur; bir kamera ayrıca üç adet gri tonlamalı **kanal başına bölünmüş** döşeme gösterebilir (bkz. [Kanal Bölünmeleri](#canlı akışın üzerine çizilen görüntü katmanları)), diziler ise birleştirilmiş bir döşeme oluşturur. Etkin döşeme, kameranın (veya dizinin) renginde bir seçim halkası taşır.

Döşemenin üzerine fareyi getirdiğinizde bir **X** kapatma düğmesi görünür:

* Kanal bölünmeleri hala açıkken **bileşik** bir kutucuğu kapatmak, yalnızca bileşik görüntüyü gizler.
* **Tek başına çalışan bir kameranın son görünür kutucuğunu** kapatmak, o kameranın bağlantısını keser.
* **Birleştirilmiş dizi üyesi bölünmüş kutucuklar kameranın bağlantısını asla kesmez** — yalnızca gizler.

Izgara kilidi açıldığında, herhangi bir döşemeyi herhangi bir yuvaya sürükleyin; düzen, projeyle birlikte kaydedilir.

## Kenar Çubuğu — kamera listesi

<!-- SCREENSHOT-NEEDED: sidebar camera list pane showing a standalone camera row and an ARRAY group with indented member rows, the DAQ on/off pill visible on the array row, plus the Connect Camera / Connect Array / Capture All buttons at the top. -->

İlk kenar çubuğu sayfasında, bağlı tüm kameralar ve diziler listelenir:

* **Kamera Bağla**(yeşil) /**Dizi Bağla** (mavi, tarama sırasında "Algılanıyor..." mesajını gösterir). Bağlantı iletişim kutusu açıkken her ikisi de devre dışıdır.
* **Tümünü Yakala** (kırmızı) — listelenen her kamerayı, Yakalama Ayarları'nda seçilen dışa aktarma türleriyle yakalar. Açık bir proje gerektirir. [Yakalama Ayarları ve Modları](capture.md) sayfasında ayrıntılı olarak açıklanmıştır.
* **Yakalama Ayarları dişli simgesi** (Tümünü Yakala'nın yanında) — [Yakalama Ayarları bölmesini](capture.md#the-capture-settings-pane) açar. Proje yoksa veya yakalama işlemi devam ederken devre dışıdır.

### Kamera satırları

Her kamera satırı, renk kodlu bir kenarlık (kameranın özel rengi), mavi **M**(ana) veya yeşil**S** (bağımlı) rol harfi ile birlikte — ve görüntü adını gösterir. Varsayılan ad `LATTICE-MODEL (serial)`'tir; kamera başına ayarlar panelinden yeniden adlandırabilirsiniz. Satır düğmeleri:

| Düğme | Etki |
| --- | --- |
| **Göz**| Görünürlüğü değiştirir. Gizlenen kameralar ızgaradan çıkar ve**"Tümünü Yakala"** işleminden hariç tutulur. |
| **Dişli** | Kamera başına ayarlar panelini açar (sonraki bölüm). |
| **Duraklat / Oynat**| Canlı önizlemeyi**yalnızca ekran tarafında** dondurur — arka uç yakalama işlemi devam eder. Duraklatılmış kameralar yakalama yapamaz. |
| **X** | Bağlantıyı keser. Kullanıcı arayüzü anında güncellenir (ideal durumda); arka uçtaki bağlantı kesilmesinin tamamlanması 10–30 saniye sürebilir. |

### Dizi satırları

Bir dizi satırı, dizinin renginde bir "ARRAY" rozeti, dizi adı (dizi ayarlarında yeniden adlandırılabilir) ve bir **DAQ · açık/kapalı** düğmesi gösterir — dizi düzeyindeki Işık Sensörü ayarlandığında *veya* herhangi bir üye kamera başına sensöre sahip olduğunda **açık**durumdadır; araç ipucunda hangi sensörün neyi beslediği tam olarak listelenir. Üye kameralar, kendi satırlarıyla birlikte altta girintili olarak listelenir. Dizi satırı düğmeleri:**göz**(TÜM üyeleri birden gizler/gösterir),**dişli**(dizi ayarları paneli),**X**(tüm dizinin bağlantısını keser).

Dizi satırlarında ve dizi ayarları panelinde kullanılan ışık sensörü (DLS) durumunun dört durumu vardır:**kapalı**,**bekleme**(henüz spektrum yok),**aktif**(son 3 saniye içinde bir spektrum geldi) ve**eski** — 3 saniye içinde yeni spektrum yok, ancak son okuma *hala kullanılıyor* (DAQ okumaları yakalama yolunda asla geçerliliğini yitirmez).

Sıralamayı değiştirene kadar, liste önce dizileri, ardından bağımsız kameraları gösterir; kameralar (ve her dizinin üyeleri) **kamera sırasına** göre sıralanır: M3C (Bayer) M3M (mono) önündedir; M3C, FRGB, FRGN, FOCN arasında, ardından FNGB; M3M'ler filtre dalga boyuna göre, en düşükten başlayarak sıralanır. Izgaranın varsayılan düzeni de aynı sırayı izler. Listeyi yeniden sıralamak için bağımsız kameraları ve tüm dizi gruplarını kenar çubuğunda birbirlerinin üzerine sürükleyebilirsiniz; dizi elemanları bağımsız olarak sürüklenemez.

## Kamera başına ayarlar paneli

Bir kamera satırındaki **dişli** simgesine tıklayarak açın. Panel, kamera listesinin üzerine kayar.

<!-- SCREENSHOT-NEEDED: per-camera settings pane, top portion — header with color swatch, camera name, rename pencil and close X; live histogram with the orange dashed AE-target line and green mean-luma line; the RGB per-band toggle button visible top-right of the histogram. -->



**Başlık**: kameranın**renk örneği**(tıklayarak yerel renk seçiciyi açın — kenar çubuğunun kenarlığı ve döşeme seçim halkasının rengini ayarlar),**ad**ve yanında kalem simgeli**Yeniden Adlandır**düğmesi (boş bir ad kaydedildiğinde varsayılan `MODEL (serial)` adına geri döner) ve kapatmak için**×**.

### Canlı histogram

Pencerenin üst kısmında, ~8 Hz frekansında JPEG önizlemesinden hesaplanan canlı luma histogramı bulunur. Ortalama, kameranın kendi AE ölçümüne uyması için Bayer ağırlıklıdır — (R+2G+B)/4.

* **Turuncu (Orange) kesikli çizgi**= AE hedefi.**Yeniden hedeflemek için yatay olarak sürükleyin** — bırakıldığında bir komut gönderilir ve sürükleme işlemi AE hedef modunu Manuel'e geçirir.
* **Yeşil (Green) kesintisiz çizgi** = gerçek ortalama luma (AE'nin şu anda sağladığı değer).
* **RGB düğmesi** (sağ üst): kameranın filtresine göre renklendirilmiş bant başına üst üste bindirilmiş histogramları gösterir (örn. FRGN'de: gri NIR, yeşil, kırmızı). Mono (M3M) kameralarda düğme "MONO" olarak görünür ve devre dışıdır — mono modunda her zaman tek bantlı luma histogramı gösterilir.
* X ekseni etiketleri, geçerli piksel formatının sensör bit derinliğine göre değişir: 0..255, 0..1023, 0..4095 veya 0..65535.

### Kamera bilgisi satırları

<!-- SCREENSHOT-NEEDED: per-camera settings info rows — Model, Radiometric Calibration "Active" badge with the tier/sha/date caption, Calibration Report Download button, Serial, Firmware row showing the "Up to date" state, IP, Temperature readout, Calibration Target checkbox, Light Sensor dropdown. -->



| Satır | Davranış |
| --- | --- |
| **Model** | Salt okunur (örn. `LATT-M3C-L87-FRGN`). |
| **Radyometrik Kalibrasyon** | Kalibrasyon kademesini, hash değerini, kalibrasyon tarihini ve kamera kalibrasyon paketinden yüklenen bant listesini gösteren bir başlık içeren Green**"Aktif"**rozeti (bkz. [Fabrika Radyometrik Kalibrasyon](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration)).**RGB kameralar için gizlenmiştir** — bu kameralar, bant başına parlaklık değil, fotometrik beyaz dengesi kalibrasyonuna sahiptir. |
| **Kalibrasyon Raporu**|**İndir** düğmesi — kameranın seri numarasına özgü NIST kalibrasyon sertifikasının PDF dosyasını işletim sisteminizin görüntüleyicisinde açar. Sertifika henüz önbelleğe alınmamışsa, Chloros bunun yerine bir ipucu gösterir. |
| **Seri Numarası** | Salt okunur. |
| **Firmware**| Mevcut sürümü gösterir, ardından bu model için mevcut sürümü belirler (modele göre önbelleğe alınır — N kameralı bir dizi, sunucuyu bir kez kontrol eder). Durumlar: "Kontrol ediliyor…" →**"X'e güncelle"**düğmesi → "Yazılıyor…" → "A → B güncellendi" / "Başarısız: …" / "Atlandı: …" / yeşil**"Güncel"**. Güncelleme düğmesinin araç ipucu: "Fabrika ayarlarına sıfırla + yazılım yükle + UserSet1'i yeniden programla. ~2–3 dakika; bağlantıyı kesmeyin." |
| **IP** | Salt okunur. |
| **Sıcaklık** | Salt okunur, her 3 saniyede bir yenilenir. ≥65 °C'de turuncu, ≥75 °C'de ⚠ işaretiyle kırmızıya döner. |
| **Kalibrasyon Hedefi** onay kutusu | Canlı akışın (liste görünümü) altındaki panel başına NDVI doğrulama tablosu ile ArUco yansıma hedefi algılamasını etkinleştirir — bkz. [Kalibrasyon Hedefi tablosu](#the-calibration-target-table). Yalnızca oturum süresince geçerlidir — her zaman kapalı olarak açılır. |
| **Işık Sensörü** açılır menüsü | Aşağıya doğru ışık (DLS) aydınlatma düzeltmesi ve tahmini otomatik pozlama için bir DAQ ışık sensörünü (DAQ-E/M/U, Işık Sensörleri sekmesindeki listeden) bu kameraya bağlar. ""Yok" seçeneği, bağlamayı siler. Bağlı sensör yoksa açılır menüde "(bağlı sensör yok — DAQ sekmesini açın)". Bağlantı, projeyle birlikte kaydedilir. |

#### Kalibrasyon Hedefi tablosu

Bu tablo **yalnızca doğrulama amaçlıdır** ("yalnızca doğrulama — kalibrasyonu değiştirmez"). Başlığında kameranın seri numarası, filtresi, hedef numarası ve piksel cinsinden işaretleyici boyutu belirtilir; her panel için bir satır bulunur:

* **Panel**, ardından panelin değerini içeren her bant için bir sütun — bir Bayer kamera, üç bandını ad ve dalga boyu ile gösterir (örn. FRGN: Red 660 nm / Green 550 nm / NIR 850 nm); bir **mono (M3M) kamera tek bandını** gösterir (örn. `F750`) ve NDVI sütunları yoktur.
* **Sat** — panelin doymuş piksellerinin oranı (%1'den itibaren kehribar, %5'ten itibaren kırmızı).
* **NDVI Ölçüm**,**NDVI Teorik** (panelin referans yansıma NDVI değeri) ve **Δ**— yalnızca Bayer multispektral kameralar için geçerlidir. "NDVI Ölçüm" ifadesinden sonra yer alan küçük bir işaret, ölçülen NDVI değerinin neye göre hesaplandığını belirtir:**ρ**(yansıma — ışık sensörü okuması mevcuttur),**L**(radyans — ışık sensörü okuması yok) veya**DN**(ham kamera değerleri). Yalnızca**ρ** NDVI'si NDVI Theo ile karşılaştırılabilir: L ve DN için Δ sütunu gri renkte görünür ve bir notta şu açıklama yer alır ("Işık sensörü okuması yok: NDVI radyans değerinden hesaplandığından Δ, kalibrasyon kontrolü değildir" / "NDVI ham kamera değerlerinden hesaplandığından Δ, kalibrasyon kontrolü değildir"). ρ değerinde Δ, 0,02'nin altında yeşil, 0,05'in altında sarı, diğer durumlarda kırmızı renktedir.

<!-- SCREENSHOT-NEEDED: a 1.3.0 Calibration Target table under a live feed in list view — a Bayer camera's table showing the band columns, Sat, and "NDVI Meas ρ" / NDVI Theo / Δ; ideally next to a mono camera's single-band table with no NDVI columns. -->

### Pozlama ve Kazanç

<!-- SCREENSHOT-NEEDED: per-camera Exposure & Gain section — Exposure (us) and Gain (dB) rows with Auto/Manual toggles, AE Target Brightness, AE Smoothing slider, AE Region of Interest row with the Aim button, and (on an array camera) AE Tune Speed and Highlight Protection rows. -->

Buradaki tüm sayısal girişler, basılı tutularak hızlandırılan ayar çarklarını kullanır: dokunma = ±1, 1,5 saniyeden fazla basılı tutma = ±10, 3 saniyeden fazla basılı tutma = ±100. Parmaklarınızı bıraktığınızda değer kameraya gönderilir.

| Kontrol | Aralık / seçenekler | Varsayılan | Uygulandığı yer | Ne işe yarar |
| --- | --- | --- | --- | --- |
| **Pozlama (us)**| Kameranın canlı min/maks | Otomatik | Tümü | Mikro saniye cinsinden pozlama süresi,**Otomatik/Manuel**seçeneği bulunur. Otomatik = kamera tarafında sürekli otomatik pozlama. Manuel pozlama ayarlandığında,**Kazanç** ayarı Otomatik konumda kalır ve kazancı düzenleyene veya Manuel'e ayarlayana kadar kameranın tüm kazanç aralığını serbestçe kullanabilirsiniz. |
| **Kazanç (dB)**| Kameranın canlı min/maks değerleri (örn. 48 dB'ye kadar) | Manuel (kapalı) | Tüm | Kendi**Otomatik/Manuel** anahtarına sahip analog/dijital kazanç. |
| **AE Hedef Parlaklığı**| 0–255 | 80,**Otomatik**modu | Tüm (AE veya otomatik kazanç açıkken düzenlenebilir) | AE'nin hedeflediği parlaklık.**Otomatik**(varsayılan) modunda, histogram tabanlı bir arka uç denetleyicisi hedefi kendisi seçer ve pozlamayı sensör maksimumunun %60–75'inde tutar. Bir değer girerek veya histogramın turuncu çizgisini sürükleyerek modu**Manuel**'e geçirebilirsiniz. |
| **AE Yumuşatma** | 0,5–40, adım 0,1 | 8,0 | Tüm | AE sönümleme. Araç ipucu: "Daha düşük = AE daha hızlı tepki verir (yüksek fps'de titreme yapabilir). Daha yüksek = daha yumuşak / daha yavaş." Varsayılan değerin çok altındaki değerler, yüksek kare hızlarında AE'nin titremesine ve akışın dengesizleşmesine neden olabilir; 8,0, kararlı varsayılan değerdir. |
| **AE İlgi Alanı**| Etkinleştirme onay kutusu +**Nişan Al**düğmesi | Kapalı | Tüm | Etkin olduğunda, AE tüm kare yerine yalnızca yeşil kesikli bölgeyi ölçer.**Nişan**, canlı yayında "tıkla ve yerleştir" özelliğini etkinleştirir: bir tıklama, bölgenin çerçevenin %30'una ortalanmasını sağlar; tıklayıp sürüklemek ise özel bir dikdörtgen (en az %5 × %5) oluşturur. *Aim*, bir kez yerleştirildikten sonra kendiliğinden devre dışı kalır. Bölge, ayarladığınız herhangi bir döndürme/yansıtma altında kameranın kendi koordinatlarına eşlenir ve projeyle birlikte kaydedilir. |
| **AE Ayar Hızı** | 0,1–5, 0,1'lik adımlarla | 1,0 | Yalnızca dizi üyeleri | Otomatik AE hedefinin sahne parlaklığı değişikliklerini ne kadar hızlı takip edeceği; 1,0× ayarında her 2,5 saniyede bir yeniden kontrol edilir. |
| **Vurgu Koruması** | Sıkı (1 %) / Normal (5 %) / Esnek (15 %) | Sıkı | Ayarı destekleyen kameralar | AE görüntüyü karartmadan önce karenin ne kadarının beyaza kırpılabileceğini belirler. |

{% hint style="info" %}
**Bayer multispektral kameralar (RGN / OCN / NGB) için ışık gereksinimi:** sahnenin üç kanalda da yeterli ışığa sahip olması gerekir, aksi takdirde kalibrasyon düzgün çalışmaz — tek bir sensör pozlaması üç spektrumu da kapsar. Işığı ölçmek için bir DAQ ışık sensörü kullanın veya her bandın kendi pozlamasını alması için tamamen mono (M3M) moduna geçin. Bir çekim bu kuralı ihlal ederse, Chloros bunu algılar ve sizi uyarır (unmix-clamp bildirimi). Canlı görüntüde, bir kanal bölünmüş döşeme, unmix o bandın piksellerinin %25'inde veya daha fazlasında kısıtlama uyguladığında **"Düşük güvenilirlikli bant verisi · N%"** mesajını gösterir.
{% endhint %}

{% hint style="info" %}
**Otomatik pozlamanın pozlama süresini ve kazancı nasıl kullandığı (1.3.0).** Kazanç Otomatik ayarında, Chloros, pozlama izin verilen en uzun değere (AE üst sınırı, bir dizinin tetikleme süresi veya DAQ pozlama tavanı) ulaşana kadar kazancı 0 dB'de tutar; ancak o zaman kazanç artar. Bir dizide, tetikleme hızı önceliklidir: otomatik pozlama, hızın belirlediği süreyi doldurur, ardından kazancı artırır; böylece kararan bir sahne artık Otomatik tetikleme hızını düşürmez (1.2.0 sürümünde düşürürdü). Bir dizi elemanında tetikleme süresinden daha uzun süreli bir **manuel**pozlama, dizinin tetikleme hızını buna uyacak şekilde yavaşlatır; otomatik hız, onu yavaşlatan her üye tekrar otomatik pozlamaya döndüğünde geri gelir.**Pozlama**ve**AE Hedef Parlaklığı**'nın her ikisi de Otomatik konumda ve bir DAQ ışık sensörü okuma sağladığında, ölçülen ışık yaklaşık ±%25 aralığında kaldığı sürece, otomatik pozlama sabitlendiğinde her kameranın pozlaması sabit tutulur; daha büyük ve sürekli bir ışık değişimi, ayarların yeniden başlatılması (örneğin bir ROI değişikliği) veya sizin yaptığınız pozlama değişikliği, sistemi yeniden etkinleştirir.
{% endhint %}

{% hint style="info" %}
**Otomatik pozlamanın çalışma şekli (1.3.0).**

* Manuel pozlama yapıldığında, kazanç değerini değiştirene kadar kazanç Otomatik modda kalır.
* Küçük parlak noktalar sahneyi karartmaz: karenin %1'ine kadar olan yansıma göz ardı edilir; bilinen bir kalibrasyon hedefinin panellerinde ise panelin %0,5'ine kadar olan kısım göz ardı edilir.
* DAQ pozlama üst sınırı yalnızca kalibrasyon hedefi görüş alanında iken geçerlidir; panel görünmediğinde kamera sahneye göre pozlama yapar.
* Pozlama, sabit ışıkta sabit kalır ve büyük bir değişiklikten sonra birkaç saniye içinde dengelenir. Bir kırpma artık pozlamayı kalıcı olarak sınırlamaz ve bir ROI değişikliği pozlamayı yeniden ayarlar.
* Parlaklık hedefi kameranınkapsamının dışına çıkmaz; bu sayede geri dönen ışık, karede parlama oluşturmaz.
* Parlak güneş ışığı altında otomatik pozlama özelliği yanıt vermeyi durduran bir kamera, ışık sensörü tarafından okunan veya sensör bağlı değilse kameranın kendi kareleri tarafından belirlenen mevcut ışık seviyesinde tutulur ve ışık değiştiğinde bu seviyeden çıkar. Işık sensörünün karanlık olması veya örtülü olması bunu etkilemez.
{% endhint %}

### Piksel Formatı ve Çözünürlük

<!-- SCREENSHOT-NEEDED: per-camera Pixel Format & Resolution section on a STANDALONE camera — Pixel Format, Resolution, and Binning dropdowns plus the Current WxH readout. A second capture on an array member showing the read-only "Set in array settings" state would also be useful. -->

**Dizi üyeleri**, "Dizi ayarlarında ayarlanır" notuyla birlikte salt okunur "Mevcut" (format + GxY) ve "Binning" satırlarını gösterir — bir üye üzerinde akışın yeniden başlatılması senkronizasyonu bozacağından, bunlar [dizi ayarları bölmesinde](#array-settings-pane) içinde yönetilir.**Bağımsız kameralar** şunları alır:

| Kontrol | Seçenekler | Ne Yapar |
| --- | --- | --- |
| **Piksel Formatı** | 8-bit / 10-bit / 12-bit / 16 bit | Sensör bit derinliği; format ailesi sensöre aittir. Varsayılan değer 12 bittir ve bir proje, 8 bit dahil olmak üzere ayarladığınız değeri kaydeder ve bu değere göre yeniden bağlanır. |
| **Çözünürlük** | Tam / Yarım / Çeyrek | Mevcut birleştirme oranına göre: N×N binning için Tam = 2048/N × 1536/N. |
| **Binning** | 1x1 (yok) / 2x2 / 4x4 | Donanım N×N binning — daha büyük değerler çözünürlüğü düşürür ancak SNR ve kare hızını artırır. Değiştirildiğinde akış yeniden başlatılır ve tüm ROI'ler yeni tam görüş alanına sıfırlanır. |
| **Mevcut** | salt okunur | Etkili olan gerçek GxY ve (x, y) ofseti. |

### Canlı Önizleme

Bu bölümdeki her şey **yalnızca görüntüleme tarafındadır**— canlı yayında gördüklerinizi değiştirir, ancak kaydedilen görüntüler doğrusal ve değiştirilmemiş kalır — tek bir istisna dışında:**Vignette** radyometrik olup dışa aktarımları da etkiler (aşağıda belirtilmiştir).

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on an RGB (FRGB) camera — Render resolution, White Balance mode, Gamma, Denoise, Sharpness, Vignette, Color Profile dropdown open showing Raw/Linear/Natural/Enhanced/Custom Temperature, Saturation, Contrast, Mirror H/V and Rotation. -->

<!-- SCREENSHOT-NEEDED: per-camera Live Preview section on a Bayer multispectral (e.g. FRGN) camera — showing the Index row with its gear button (and the absence of the RGB-only White Balance / Gamma / Color Profile / Saturation / Contrast rows). -->



| Kontrol | Aralık / seçenekler | Varsayılan | Uygulandığı yer | Ne yapar |
| --- | --- | --- | --- | --- |
| **Render çözünürlüğü** | 360p (en hızlı) / 480p / 720p / 1080p / Orijinal sensör çözünürlüğü (en yavaş) | 720p | Tümü | Arka uçun radyometrik önizleme zincirini çalıştırdığı yükseklik. Daha düşük değer, görüş alanını değiştirmeden kare hızını artırır. |
| **Endeks**| Etkinleştirme onay kutusu + dişli simgesi | Kapalı | Yalnızca Bayer multispektral,**kombine dizi üyeleri hariç** | Canlı bitki örtüsü endeksi önizlemesi. Dişli simgesi, kameranın filtre doğal bantlarıyla (örn. `Red_660_RGN`, `Green_550_RGN`, `NIR_850_RGN`). Özel ifade ve LUT (açık/kapalı, varsayılan seviye 7-stop, varsayılan min 0,2, varsayılan maks 1) her önizleme karesinde hesaplanır. Birleşik dizi üyeleri bu satırı gizler — dizi tek bir paylaşılan indekse sahiptir. |
| **Beyaz Dengesi** | Kapalı / Tek Seferlik / Sürekli + yeniden yakalama düğmesi | Sürekli | Yalnızca RGB | Canlı beyaz dengesi. Yenile düğmesi, mevcut DLS spektrumundan beyaz dengesini yeniden yakalar (mod Kapalı olduğunda devre dışıdır). |
| **Gama** | Açık / Kapalı | Açık | Yalnızca RGB | Canlı önizlemede gama (γ = 2,2 LUT) gösterilir. Kaydedilen çekimler doğrusal kalır. |
| **Gürültü Giderme** | Onay kutusu + güç 0–100 | Kapalı / 50 | Tüm (kamera başına, diziler içinde bile) | Canlı önizlemede iki yönlü filtre. Daha yüksek değer = daha pürüzsüz ancak daha yumuşak ayrıntılar. |
| **Keskinlik** | Onay kutusu + güç 0–100 | Kapalı / 30 | Tüm | Canlı önizlemede keskinleştirme maskesi, en son uygulanır. Gürültüyü artırabilir. Yalnızca önizleme. |
| **Vinyet**| Onay kutusu + güç 0–100 | Kapalı / 0 | Tüm | Manuel artık vinyet giderme (köşeleri aydınlatır), dizinin Akıllı Vinyet tahmininin üzerine eklenir.**Radyometrik — Gürültü Giderme/Keskinlik'ten farklı olarak canlı görüntüyü VE dışa aktarımları etkiler**. |
| **Renk Profili** | Raw / Doğrusal / Doğal / Geliştirilmiş / Özel Sıcaklık | Doğal | Yalnızca RGB | Aşağıya bakın. |
| **Renk Sıcaklığı** | 2000–10000 K, 100'lük adımlarla | 5500 K | Yalnızca RGB, Özel Sıcaklık profili | Beyaz dengesini sabit bir ilişkili renk sıcaklığına sabitler (DLS girişi yok sayılır). En son seçilen Kelvin değeri, profil değişiklikleri arasında hatırlanır. |
| **Vurgular** | −100 ila 0, 1'lik adımlarla | 0 (kapalı) | Yalnızca RGB | 1.3.0 sürümünde yeni. Resmin en parlak, en beyaz alanlarını karartır (0 = değişmez, −100 = en güçlü). Canlı önizlemeye **ve ayarı kaydeden ekran düzeyindeki (Önizleme) yakalamalara**; bu yakalamalar ayarı kaydeder (`Chloros:DisplayHighlights`); parlaklık ve yansıma dışa aktarımları hiçbir zaman etkilenmez. Kırpılmış sensör verilerini kurtaramaz — bunun için pozlamayı azaltın. Zebra katmanı, karartılmış görüntüyü takip eder. Raw renk profiliyle devre dışı bırakılır ile devre dışı bırakılır ("Raw renk profiliyle uygulanmaz."). |
| **Doygunluk** | 0–200 (100 = nötr) | 100 | Yalnızca RGB | Canlı önizlemede HSV doygunluğu. |
| **Kontrast** | 0–200 (100 = nötr) | 100 | Yalnızca RGB | Canlı önizlemede orta gri civarında doğrusal kontrast. |
| **Yatay Yansıtma / Dikey Yansıtma** | Onay kutuları | Kapalı | Tümü | Önizlemeyi yatay / dikey olarak çevirir. |
| **Dönüş**| 0° / 90° / 180° / 270° | 0° | Tümü | Önizlemeyi döndürür. Yönlendirme, arka uç önizleme zincirinin sonunda uygulanır.**Kaydedilen çekimler (1.3.0):**bağımsız bir kameranın Önizleme, Parlaklık, Yansıma ve İndeks ürünleri Ayna/Dönüş ile döndürülürken,**Raw ve Debayered, kameranın kendi yönelimini korur**; her çekim dosyası bu ayarı XMP'sine kaydeder, böylece daha sonra bir raw dosyası işlendiğinde ayar aynı şekilde uygulanır.**Dizi üye dosyaları kendi yönelimlerini korur** (ayar kaydedilir, uygulanmaz) ve dizi birleştirilmiş görünümleri bunu göz ardı eder. |

**Renk profili semantiği** (RGB kameralar):

* **Raw** — işleme zincirini tamamen atlar.
* **Doğrusal** — karanlık sinyal + düz alan + beyaz dengesi; renk matrisi yok, gama yok.
* **Doğal** *(varsayılan)* — doğrusal artı ölçülen renk düzeltme matrisi ve sahneye uyarlanabilir ton eğrisi.
* **Geliştirilmiş**— Doğal artı canlılık ve CLAHE yerel kontrastı. Ek maliyet**yalnızca canlı önizleme** için geçerlidir — kaydedilen çekimler, profilden bağımsız olarak her zaman tam son halini alır.
* **Özel Sıcaklık** — Beyaz dengesi seçtiğiniz Kelvin değerine sabitlenmiş Doğal modu.

{% hint style="warning" %}
Doğal, Geliştirilmiş ve Özel Sıcaklık modlarında panelde bir ton notu gösterilir: kareler kendi sahnelerine göre aydınlatılır, bu nedenle kaydedilen *ekran* görüntüleri kare bazında karşılaştırılamaz. **Ölçümler için parlaklık veya yansıma değerlerini dışa aktarın.**
{% endhint %}

### Ekran üstü katmanlar (canlı yayının üzerine çizilir)

Bunlar yalnızca ön uçtadır — videonun üzerine çizilir, akışa veya yakalanan görüntülere asla dokunmaz.

<!-- SCREENSHOT-NEEDED: a live feed tile with overlays active — zebra stripes on clipped sky, 3x3 grid, focus peaking in the default orange, and the on-feed histogram strip; the overlays section of the settings pane visible alongside. -->

| Katman | Kontroller | Varsayılan | Ne yapar |
| --- | --- | --- | --- |
| **Zebra** | Onay kutusu + eşik 200–255 | Kapalı / 250 | Kırpılmış pikseller üzerinde macenta renkli diyagonal şeritler. |
| **Çapraz İşaret** | Onay kutusu | Kapalı | Kare merkezi işareti. |
| **Izgara** | Kapalı / 3 × 3 / 9 × 9 | Kapalı | Kompozisyon ızgarası. |
| **Histogram** | Onay kutusu + kare genişliğinin 0,10–0,90'ı | Kapalı / 0,25 | Akış üzerinde bir histogram şeridi. |
| **Odak Zirvesi** | Onay kutusu + eşik 20–200 + renk örneği | Kapalı / 80 / `#ff5722` | Odaklama için Sobel kenar vurgusu. |
| **Kanal Bölünmeleri** | "Bölünmeleri Göster (Red / Green / NIR)" / "Bölümleri Gizle" düğmesi | Gizli | Kompozit görüntünün yanına kanal başına üç bağımsız gri tonlamalı döşeme ekler (düğme etiketi, kameranın filtre kanallarını takip eder). Her bir bölünmüş döşeme sürüklenebilir ve kameranın kenarlık rengini alır. Monokrom kameralarda kullanılamaz. Projeyle birlikte kaydedilir. |

### Nokta Ölçer

* **Tıklayarak Örnek Al**onay kutusu: tek bir pikseli örneklemek için canlı görüntüyü tıklayın (bir artı işareti ile işaretlenir) veya piksel ortalaması almak için bir bölgeyi tıklayıp sürükleyin.**Temizle**, örneği ve artı işaretini siler. AE-ROI**Nişan** modu ile birbirini dışlar.
* **Göster** açılır menüsü:**Ham (bit derinliği)**— sensörün bit derinliğindeki doğal dijital sayılar (örn. 12 bit → 0..4095) — veya**Ekran (8 bit)** (varsayılan). Canlı indeks etkin olduğunda, Ekran yerine hesaplanan indeks değerini (örn. NDVI) gösterir.
* Okuma paneli, piksel koordinatlarını, kare boyutunu, piksel formatını, bit derinliğini ve bant etiketleri ile dalga boylarını içeren bir kanal tablosunu (Chan / Değer / %) listeler; Bayer yeşil çiftlerinin ortalaması alınır; bölge örnekleri "N px avg" olarak gösterilir.

Nokta ölçer durumu yalnızca oturum süresince geçerlidir.

<!-- SCREENSHOT-NEEDED: Spot Meter in use — reticle placed on the live feed, readout panel showing the per-channel value table with band wavelength labels. -->

### Tahmine Dayalı Otomatik Pozlama (DLS tabanlı)

Bu bölüm, yalnızca **en az bir DAQ ışık sensörü bağlı olduğunda** görünür — çözücü, bunu çalıştırmak için canlı bir aşağı doğru ışınım spektrumuna ihtiyaç duyar.

<!-- SCREENSHOT-NEEDED: Predictive Auto-Exposure (DLS-driven) section with a DAQ connected — Enable checkbox, Smoothing (α) slider at 0.30, and the "Recalibrate ρ" button. -->



| Kontrol | Aralık | Varsayılan | Ne yapar |
| --- | --- | --- | --- |
| **Etkinleştir** | Onay kutusu | Açık (bağımsız kameralar) | Kapalı form çözücü, DLS spektrumunu ve kameranın kalibrasyon paketi skalerlerini kullanarak en parlak bandı doygunluğa yakın bir seviyeye getirirken, en sönük bandı SNR taban seviyesinin üzerinde tutar — her çözümleme için tek bir pozlama yazımı, sabitleme döngüsü yoktur. Her çekimin doğru pozlanmasının gerekli olduğu güneş enerjili zaman atlamalı çekimler için tasarlanmıştır. DLS okuması güncel değilse/eksikse veya kalibrasyon paketi yüklenmemişse, arka uç sessizce reaktif AE'ye geri döner. |
| **Düzgünleştirme (α)** | 0,05–1,0, adım 0,05 | 0,3 | Ardışık tahmin çözümlerinin düzgünleştirilmesi (düşük değer = daha yumuşak). |
| **Sahne yansıma oranı**|**ρ'yi Yeniden Kalibre Et** düğmesi | — | Çözücünün kullandığı sahne yansıma oranını yeniden tahmin eder. |

{% hint style="info" %}
**Dizi bağlantısı, varsayılan olarak öngörücü AE'yi devre dışı bırakır** — diziler için, Chloros'un akıllı AE'si ve kamera tarafındaki otomatik pozlama, pozlamayı (doygunluk korumasıyla) yönetir; öngörücü AE'nin tek sahneyansıma tahmini, karışık sahnelerde güvenilir değildir. Özellikle DLS tabanlı radyometrik pozlama istiyorsanız, buradan kamera bazında bu özelliği yeniden etkinleştirebilirsiniz.
{% endhint %}

**DAQ tabanlı pozlama tavanı ve gelen ışığa sabitlenmiş AE.** Yukarıdaki onay kutusundan bağımsız olarak, bir DAQ ışık sensörü bir RGB kameraya atandığında, Chloros — ölçülen mutlak aşağıya doğru ışınım yoğunluğundan — %100 yansıtma oranına sahip bir yüzeyin kırpılma sınırının altında kalacağı maksimum pozlama×kazanç değerini hesaplar ve bunu otomatik pozlamada bir **sınır**olarak otomatik pozlamaya uygular. 1.3.0 sürümünden itibaren bu sınır, yalnızca bir kalibrasyon hedefi o kameranın görüş alanında (son 10 saniye içinde karesinde görülmüşse) bulunduğu sürece geçerlidir; görüş alanında hedef olmadığında ise, kameranın kendi parlaklık korumasına sahip normal otomatik pozlaması devreye girer; böylece sahnenin geri kalanı gerekenden daha karanlık kalmaz. Üst sınır aktifken kamera**gelen ışığa sabitlenir** modunda çalışır: kazanç 0 dB'deyken gelen ışık ölçümüne dayalı pozlamada açık döngüde çalışır — pozlama, sahnenin içeriğini değil, ölçülen ışığı takip eder. Üst sınır yalnızca pozlama süresini kısaltabileceğinden, kendi başına kırpma oluşturmaz. 1.3.0 sürümünden itibaren tavan değeri, kameranın kalibrasyon paketindeki kendi pozlama sabiti kullanılarak hesaplanır (paket bir değer içermediğinde yalnızca filo ortalaması kullanılır). Kalibrasyon hedefi görüş alanında olmadığında, DAQ okuması eksik, eski (>30 s) veya karanlıksa ya da karenin ≥%15’i sabitlenmiş pozlamada kırpılıyorsa (yani sensör ve kamera farklı aydınlatma görüyorlarsa). GUI’de bir anahtar yoktur; bu, bir RGB kameranın DAQ’ya bağlı olduğu her durumda standart davranıştır.

### Veri Toplama ve Tetikleme

<!-- SCREENSHOT-NEEDED: Acquisition & Trigger section on a standalone camera — Trigger Mode, Trigger Source, and the Frame Rate row in Auto mode showing live fps; ideally a second capture on an array member showing the read-only Role/Sync Line/Peers rows. -->

Dizi üyeleri ayrıca salt okunur **Rol**(Mavi renkli Ana / Yeşil renkli Bağımlı),**Senkronizasyon Hattı**ve**Eşler** satırlarını gösterir.

| Kontrol | Seçenekler | Varsayılan | Notlar |
| --- | --- | --- | --- |
| **Tetikleme Modu** | Kapalı / Açık | Açık | Dizi üyeleri için devre dışıdır (tetiklemeyi dizi yönetir). |
| **Tetik Kaynağı** | Yazılım / Hat0 (M8) / Hat1 / Hat2 | Hat0 | Tetikleme Modu Kapalıyken gizlenir; dizi üyeleri için devre dışıdır. Hat0, M8 opto-izole harici tetikleme girişidir. |
| **Kare Hızı**| Otomatik / Manuel + değer | Otomatik |**Otomatik**: kameranın kare hızı sınırlaması kapalıdır — pozlama fps'yi belirler ve kutuda canlı gerçek hız gösterilir.**Manuel**: fps değerini, mevcut gerçek hızdan yola çıkılarak bir kaydırıcıyla sınırlarsınız (1'den bant genişliği sınırlı maksimum değere kadar). Dizi üyeleri, "Dizi ayarlarında ayarlanır" ibaresiyle birlikte salt okunur "N fps (canlı)" değerini görür. |

### Ağ / Aktarım

| Satır | Davranış |
| --- | --- |
| **Paket Boyutu**| 1500 (Standart) / 9000 (Jumbo) — varsayılan**Jumbo**. |
| **Verim** | MB/s cinsinden salt okunur bağlantı verim sınırı. Arka uç, her bağlantı kurulduğunda veya kesildiğinde bunu bağlı tüm kameralar arasında yeniden dengeler. |
| **Tampon İşleme** | Salt okunur tampon işleme modu. |

### Yakalama

Pencere, [Yakalama Ayarları penceresine](capture.md#the-capture-settings-pane) yönlendiren **"Yakalama Ayarlarını Aç…"** düğmesiyle sona erer (bir proje açılana kadar devre dışıdır — "Yakalamaları kaydetmek için bir proje oluşturun veya açın"). Kamera gizlenmiş veya duraklatılmışsa, bir ipucu size yakalama işleminden önce kameranın gizliliğini kaldırmanızı/devam ettirmenizi hatırlatır.

## Dizi ayarları paneli

Bir DİZİ satırındaki **dişli**simgesine tıklayarak açın. Başlık: yeniden adlandırma kalemi simgesi bulunan dizi adı ve kapatmak için**×** simgesi. Aşağıda *yalnızca birleştirilmiş* olarak işaretlenen bölümler, yalnızca birleştirilmiş görüntüleme modunda bağlanmış diziler için görünür.

<!-- SCREENSHOT-NEEDED: array settings pane, top portion — array name header, Sync section (Master/Slaves/Sync Line), and Ambient Light Sensor section with the Light Sensor dropdown and the green "Active — all cameras in the array are illumination-corrected" status line. -->



### Senkronizasyon

Salt okunur **Ana**,**Bağımlı**ve**Senkronizasyon Hattı** satırları.

### Ortam Işığı Sensörü

Hem birleştirilmiş hem de ayrı diziler için gösterilir:

* **Işık Sensörü** açılır menüsü — bir DAQ'yı tüm diziye bağlar. Seçim anında geçerli olur, her üye kameranın kendi Işık Sensörü açılır menüsüne yayılır (yine de kamera bazında geçersiz kılabilirsiniz) ve spektrumları diziye iletmeye başlar.
* Canlı **Durum** satırı: Kapalı · "İlk spektrum bekleniyor…" · "Etkin — dizideki tüm kameraların aydınlatma düzeltmesi yapılmıştır" · "Son 3 saniye içinde yeni spektrum yok — hala son okuma kullanılıyor (eski veri zaman aşımı yok)…".
* Bölmedeki not: "Dizi genelinde radyometrik düzeltme. Kamera başına ayarlar bunu geçersiz kılar."

### Kalibrasyon Hedefi

Hem birleştirilmiş hem de ayrı diziler için, Ortam Işık Sensörü'nün hemen ardından kendi grubu bulunur:

* **Etkinleştir** onay kutusu — "MAPIR ArUco hedefini algıla ve NDVI ile panel yansıma LUT'unu doğrula"; birleştirilmiş döşemenin hedef katmanını ve doğrulama tablolarını yönlendirir. **Ayrı** bir dizide ise her üyenin kendi [Kalibrasyon Hedefi tablosunu](#the-calibration-target-table) açar (liste görünümünde her kamera satırı için bir tane).

### Yakalama — tek tip sensör ayarları

Hem birleştirilmiş hem de ayrı diziler için gösterilir (grup başlığı "Yakalama (kameralar arasında tek tip)" şeklindedir). Bu ayarlar her üyeye tek tip olarak uygulanır (üye başına değişiklikler senkronizasyonu bozar). Düzenlemeler aşamalı olarak yapılır ve **Uygula**ile birlikte uygulanır — anında uygulanan**ROI açık / ROI kapalı** anahtarı hariç.

<!-- SCREENSHOT-NEEDED: array settings Capture section (1.3.0) — Pixel Format, Binning, Resolution preset, the ROI crop row with the "ROI on" toggle, the "Select" button, the "max WxH" hint and Reset button, the W/H/X/Y fields, the full-sensor ROI map below them with the orange crop box and its "W×H @ X,Y" label above the box, Trigger Rate row in Auto showing the derived fps, and the Apply/Cancel buttons. -->

| Kontrol | Seçenekler / aralık | Ne yapar |
| --- | --- | --- |
| **Piksel Biçimi** | 8 bit / 10 bit / 12 bit / 16 bit | Tüm üyeler için tek tip bit derinliği; her kamera, kendi sensörü için bu derinlikteki biçimi alır. |
| **Binning** | 1x1 / 2x2 / 4x4 | Donanım binningi — SNR ve kare hızını artırırken tam görüş alanını korur. Değiştirildiğinde ROI alanları yeni tam görüş alanına göre sıfırlanır. |
| **Çözünürlük** ön ayarı | Tam / Yarım / Çeyrek | Binning'e göre; ROI alanlarını ortalanmış bir kırpma ile doldurur. |
| **ROI kırpma (px)**|**ROI açık / ROI kapalı**anahtarı,**Seç**düğmesi, G / Y / X / Y alanları, tam sensör haritası | Sensör kırpma — bkz. [ROI düzenleyicisi](#the-roi-editor). Genişlik/yükseklik, 16'nın katlarına (en az 64); ofsetler 4'ün katlarına hizalanır. "Maks. GxY" ipucu üst sınırı gösterir ve**Sıfırla**, alanları tam görüş alanına geri döndürür. Düzenleme sırasında turuncu kırpma kutusu, harita ve dizi döşemesi üzerindeki her alan değişikliğini takip eder. |
| **Tetikleme Hızı**| Otomatik / Manuel geçiş + fps 0,5–10, adım 0,5 |**Otomatik**(varsayılan): arka uç, tetikleme hızını çözünürlük ve bant genişliğinden hesaplar — giriş devre dışı bırakılır ve hesaplanan değer gösterilir.**Manuel**:**Uygula**'da değerinizi sabitler. Yeni bir kare boyutu tarafından desteklenemeyen sabitlenmiş bir hız, o boyutun izin verdiği en yüksek hıza ayarlanır ve kare boyutu tekrar izin verdiğinde eski hızına döner. |

Pencerede not: "Biçim/çözünürlük değişiklikleri tüm kameraları kısa süreliğine yeniden başlatır. Tetikleme hızı canlı olarak uygulanır." **Uygula / İptal** düğmeleri pencerenin altında yer alır.

#### ROI düzenleyicisi

1.3.0 sürümündeki yenilik. ROI kırpma satırı ve altındaki harita birlikte çalışır:

* **ROI açık / ROI kapalı**— sensör kırpmasını**anında**açar veya kapatır (kameralar kısa bir süre yeniden başlatılır; dizi döşemesi "Ayarlar uygulanıyor…" mesajını gösterir).**Kapalı**seçeneği sensörün tamamını okur ve**kırpma ayarınızı hatırlar**— alanlar bunu göstermeye devam eder;**Açık** seçeneği ise kırpmayı yeniden uygular. Kırpma ayarı yapılmadan ROI'yi açmak, ortalanmış yarı boyutlu bir kırpma uygular ("Kırpma ayarı yapılmadı, bu nedenle ortalanmış yarı boyutlu bir kırpma uygulandı. Alanları kullanarak ayarlayın.").
* **Seç**— harita üzerinde kırpma alanını çizer: üzerine sürükleyin veya zıt iki köşeyi tıklayın (Esc tuşu ile iptal edilir). Dikdörtgen, W/H/X/Y alanlarına yerleştirilir, yukarıdaki adımlara göre hizalanır;**Uygula**'ya basana kadar hiçbir şey uygulanmaz, böylece önce alan oklarıyla ince ayar yapabilirsiniz. Seç tuşuna her basışta bir dikdörtgen oluşur.
* **Harita**, kırpmayı tüm sensör üzerinde gösterir, böylece kırpma yapılmışken onu tekrar genişletebilirsiniz: Kameralar tam sensörü okurken "Tam sensör, canlı", veya "{zaman} tarihinden tam kare anlık görüntü. Kameralar yalnızca kırpılmış alanı okur." seçeneği, kırpma yapıldığında geçerlidir. Dizi şimdiye kadar yalnızca kırpılmış olarak akışa alınmışsa, haritada "Henüz tam kare görüntü yok: bu dizi yalnızca kırpılmış olarak akışa alınmıştır." yazısı ve ROI'yi devre dışı bırakan bir**Tam kare göster** düğmesi görünür. Kırpma alanının `W×H @ X,Y` etiketi, haritada ve canlı kutucukta kutunun dışında (kutunun üstünde veya kutu üst kenara değdiğinde altında) yer alır.
* **Uygula**, alanları kaydetir: bir kırpma işlemi ROI'yi etkinleştirir; tam kare (**Sıfırla** işleminden sonra) ROI'yi devre dışı bırakır ve önceki kırpma hâlâ hatırlanır; devre dışı olan ve alanlarında hâlâ hatırlanan kırpma gösterilen bir ROI, başka bir değişiklik (format, birleştirme, hız) uygulandığında devre dışı kalır.
* **Hizalanmış bir dizide, her üyenin kırpma penceresi hizalama ile yerleştirilir**, böylece her kamera sahnenin aynı bölümünü kadraja alır. Hizalanmadan önce kırpılmış bir dizi, önce tam sensör üzerinde hizalanır (kırpma kaldırılır, hizalama çözülür ve kırpma kamera başına pencerelerle geri yüklenir).
* Kırpma değişikliğinden sonra, otomatik pozlama yeni kadraj için yeniden uyumlanır. Kırpma altında, bir üyenin pozlaması tetikleme süresine kadar devam edebilir.
* ROI açık/kapalı durumu ve hatırlanan kırpma, projeyle birlikte kaydedilir.

### Hizalama (ortak kayıt)

**Hem birleştirilmiş hem de ayrı diziler** için gösterilir (1.2.0 sürümünde yalnızca birleştirilmiş diziler için gösterilirdi); grup başlığı "Hizalama" olarak görünür.

<!-- SCREENSHOT-NEEDED: array settings Alignment section after a successful calibration — green "RMS x.xx px" residual pill, "✓ All cameras aligned (N)" summary, the per-camera table with px error / match count / NCC columns, the Recalibrate alignment button and the "Auto-expose cameras for alignment" checkbox. -->



* **Kalan** kutucuğu: "RMS x.xx px" — 1 px'in altında yeşil, 3 px'in altında sarı, aksi durumda veya herhangi bir kamerada hata olması durumunda kırmızı; ilk çözümlemeden önce "profil yok".
* Özet satırı: "✓ Tüm kameralar hizalandı (N)" / "⚠ p/N kamera hizalandı —  <serial (filter)="">başarısız" / "Kırpma etkin — Hizalamak için yeniden kalibre et (tam sensör kullanılır)" / "Pozlamanın sabitlenmesini bekliyor…" / "Ayarlar uygulanıyor…" (kameralar yeniden başlatılıyor) / "Dizi kare göndermeyi durdurdu — diziyi yeniden bağlayın".
* Kamera başına tablo: kamera (seri numarasının son 4 hanesi + filtre), px cinsinden yeniden projeksiyon hatası ve eşleşme sayısı (ana kamera için "ref") ile 0,35'lik geçme eşiğine göre normalleştirilmiş çapraz korelasyon çakışma puanı.
* **Hizalamayı yeniden kalibre et** düğmesi (ilk profilden önce "Hizlemeyi kalibre et" yazısı görünür) — yeni karelerde eşleştirmeyi yeniden çalıştırır.
* **"Hizalama için kameraları otomatik pozla"** onay kutusu (varsayılan olarak işaretlidir) — karanlık veya düz kameraları geçici olarak aydınlatır (önce pozlama, sonra kazanç), böylece eşleşecek dokuya sahip olurlar, ardından AE'yi geri yükler.

Chloros, her iki görüntüleme modunda da, üyelerin otomatik pozlaması sabitlendiğinde (5 dakikaya kadar denemeye devam eder) bağlandıktan sonra her diziyi otomatik olarak hizalar; birleştirilmiş önizleme de açıldığında hizalanır. Ayrı bir dizide birleştirilmiş döşeme bulunmadığından, bu bölüm hizalama durumunu kontrol eder ve Recalibrate işlevini kendisi çalıştırır. Odak veya sahne derinliği değiştiyse yeniden kalibre edin. Hizalama **tasarım gereği yalnızca oturum süresince geçerlidir** — o anki sahne mesafesine bağlı olduğundan hiçbir zaman bir profile kaydedilmez. Yakalamalar yine de piksel kayıtlı olarak dışa aktarılabilir (bkz. [Hizalanmış dışa aktarmalar](capture.md#per-array-controls)).

### Akıllı Vinyet

* **Düzeltmeyi etkinleştir**onay kutusu — kamera başına vinyet tahminini radyometrik zincire uygular (canlı**ve** dışa aktarmalar).
* **Mevcut görünümden kalibre et**— önce diziyi tekdüze bir hedefe (düz panel, duvar veya gökyüzü) yöneltin; her kamera ayrı ayrı düzleştirilir ve durum raporu "n/N kamera · −x,x %" düzlük kazancı gösterir.**Temizle** seçeneği tahmini kaldırır.
* Durum: "Kalibre edildi", "Kalibre edilmedi" veya "Kısmen kalibre edildi — yeniden çalıştır" (sadece bazı üyelerin tahminleri mevcut kadrajlarında hala geçerliyse — örneğin, Yeniden Kalibre etme işlemi bir üyenin kırpma penceresini değiştirdikten sonra) — Kalibre Et'i tekrar çalıştırın.
* [Canlı Önizleme](#live-preview) bölümündeki kamera başına **Vignette** kaydırma çubuğu ile kamera başına ince ayar yapın.

### Canlı Önizleme

* **Histogram**onay kutusu + genişlik kaydırıcısı (karenin 0,10–0,90'ı) — her üyenin döşemesinde (bölünmüş kanal döşemeleri dahil) aynı anda görüntü akışında histogram şeridi, tüm dizi için ayarlanan kamera başına [Histogram katmanı](#display-overlays-drawn-over-the-live-feed) tüm dizi için ayarlanır. Her üyenin zaten buna sahip olması durumunda açık olarak gösterilir.**Ayrı** bir dizide bu, gruptaki tek satırdır.
* *Yalnızca birleştirilmiş:* **Dizin**: etkinleştirme onay kutusu + dişli simgesi —**tüm** üye kameralardan alınan bantlarla paylaşılan [Dizin Hesaplayıcı](#index-calculator-pane) penceresi açılır. Altındaki ifade önizleme satırı, her saniye yenilenen mevcut ifadeyi gösterir ("İfade ayarlanmadı — bir ifade oluşturmak için hesaplayıcıyı açın").
* *Yalnızca birleştirilmiş modda:* **Render çözünürlüğü**açılır menüsü (kamera başına ayarlarla aynı ön ayarlar, varsayılan 720p): canlı görüntü akışının yüksekliği**ve** kaydedilen birleştirilmiş dışa aktarım boyutu. Paneldeki not: "Önizleme + kaydedilen birleştirilmiş boyut. Kamera başına görüntüler her zaman tam çözünürlükte dışa aktarılır."

### Görüntü Katmanları *(yalnızca birleştirilmiş modda)** **Etkinleştir** onay kutusu (varsayılan olarak kapalı — ana kamera düz olarak gösterilir; açık = katmanlı birleştirilmiş görüntü).
* **Ön Plan**/**Arka Plan**açılır menüleri: her bir üye kamera (adına göre) veya**Dizin**. Ön Plan seçeneği Dizin olarak ayarlandığında, LUT Min/Maks değerlerinin dışındaki pikseller Arka Plan katmanını gösterir.

### Bölünmüş Görünüm *(yalnızca birleştirilmiş)*

**"Üye kameraları göster"**— her bir üyenin kendi canlı görüntüsünü bileşikin yanına ayrı ızgara kareleri olarak ekleyen bir**Üye kameraları böl / gizle** düğmesi. Kareler, dizinin mevcut kare tamponunu okur (ekstra kamera bağlantısı gerekmez). Yalnızca ızgara görünümü; projeyle birlikte dizi başına kaydedilir.

### Özellikler

Her 5 saniyede bir yenilenen, salt okunur bir panel:

* **Katman etiketi**: "Eşzamanlı yakalama" (yeşil) · "Eşzamanlı yakalama (FTD-kademeli yayılım)" (yeşil) · "Kademeli yakalama (100 ms sapma)" (sarı) · "Yapılandırma çok büyük" (kırmızı).
* **Çerçeve durumu**: "x.xx % eksik" — %1'in altında yeşil, %5'in altında sarı, %5 ve üzeri kırmızı.
* **Bağlantı hattı**: "NIC {mbps} Mbps - sürekli {MB/s} MB/s".

Bu, dizinin anlık bant genişliği bütçesidir. Temel fps ve ağ modeli hakkında — ve katman sarı veya kırmızıya döndüğünde nelerin değiştirilmesi gerektiği konusunda — bkz. [Çoklu Kamera Dizileri](arrays.md) ve [CLI Referansı](../reference/cli-reference.md).

<!-- SCREENSHOT-NEEDED: array settings Capabilities panel showing a green "Simultaneous capture" tier, the frame-health percentage, and the NIC/sustained-throughput line. -->

## Dizin Hesaplayıcı bölmesi

Üçüncü kenar çubuğu sayfası, kamera başına Dizin ayar dişlisi ile birleştirilmiş dizi Dizin ayar dişlisi tarafından paylaşılır (her seferinde biri — başlıkta "Dizin Hesaplayıcı — <camera name="">" veya "Dizin Hesaplayıcı — <array name="">"</array></camera> yazmaktadır<camera name=""><array name="">). Bu bölme, bant listesini (kameranın filtredoğal bantları veya dizinin tüm üyelerindeki tüm bantlar), geçerli ifadeyi ve LUT yapılandırmasını (açık/kapalı, seviye — varsayılan 7 durak, min — varsayılan 0,2, maks — varsayılan 1) ve ayrıca canlı bir indeks histogramını alır. **Uygula** düğmesi ifadeyi uygular; LUT değişiklikleri önizlemeye anında yansıtılır.

<!-- SCREENSHOT-NEEDED: Index Calculator pane open for a combined array — band buttons for all member cameras, an NDVI-style expression in the editor, LUT controls, and the live index histogram. -->

## Kamera başına ayarlar ve dizi tarafından yönetilen ayarlar

Bir kamera bir dizinin üyesi olduğunda, hangi ayarların nerede bulunduğu konusunda hızlı başvuru:

| Dizi tarafından yönetilen (kamera panelinde salt okunur) | Dizi içinde hala kamera başına |
| --- | --- |
| Piksel biçimi, çözünürlük, birleştirme | Otomatik pozlama (pozlama, kazanç, hedef, yumuşatma, ROI) |
| Tetikleme modu/kaynağı, kare hızı | Gürültü giderme, keskinlik, vinyet |
| | Yönlendirme (ayna/dönüş), ekran üstü katmanlar (dizi ayarları → Canlı Önizleme, her üye için histogramı aynı anda ayarlar), nokta ölçer |
| | Dizin (ayrı ekranlı diziler), ışık sensörü bağlama |

Diğer genel davranışlar:

* **Birleştirilmiş ve ayrı görüntüleme**, dizi bağlanırken seçilir: birleştirilmiş = tek bir hizalanmış bileşik döşeme (üye akışları yalnızca Bölünmüş Görünüm üzerinden sağlanır); ayrı = her üye kendi senkronize döşemesini oluşturur. Bir kamera asla hem bağımsız bir akışı hem de bir dizi döşemesini aynı anda göstermez.
* **Otomatik yeniden bağlanma**: Kaydedilmiş bir projenin açılması, kameralarını ve dizilerini geri yükler ve akışlar yeniden başlamadan önce kaydedilmiş tüm ayarları arka uca yeniden uygular.
* **Yakalama filtreleme**: Gizlenmiş veya duraklatılmış kameralar "Tümünü Yakala"dan hariç tutulur; bir dizi, yalnızca TÜM üyeler gizlendiğinde/duraklatıldığında tamamen engellenir. Bkz. [Yakalama Ayarları ve Modları](capture.md).

## Ayarların Kalıcılığı

Kamera sekmesinin durumu, tarayıcıda değil **projeyle birlikte** kaydedilir:

* Her reaktif değişiklik, kameraların ve dizilerin anlık görüntülerini projenin `cameras.json` dosyasına kaydeder (500 ms gecikmeli). Bu, kamera adlarını ve renklerini, pozlama/kazanç/AE ayarlarını, piksel formatını/çözünürlüğünü/binning'i, tetikleme hızını, önizleme ayarlarını (render çözünürlüğü, gürültü giderme, keskinlik, vinyet, renk profili, Vurgular, doygunluk/kontrast), yönlendirmeyi, üst katmanları, kanal bölünmelerini, dizin yapılandırmasını, öngörücü AE ayarlarını, AE ROI'yi, dizi adları, görüntüleme modu, dizi yakalama ayarları (ROI kırpma, ROI'nin açık olup olmadığı ve kapalıyken hatırlanan kırpma dahil), dizinin Tel Bütçesi ve ızgara bloğu (besleme yakınlaştırma, görüntüleme modu, ızgara kilidi, manuel döşeme sırası, gizli kameralar, kapalı döşemeler, aktif kamera).
* Işık sensörü bağlamaları, projenin `sensors.json` dosyasına kaydedilir.
* Projenin yeniden açılması, donanımı yeniden bağlar ve tüm ayarları yeniden uygular.
* **Açık proje yok = yalnızca oturum**: proje olmadığında, Chloros kapatıldığında hiçbir ayar korunmaz.
* Projeden bağımsız olarak yalnızca oturum: duraklatma durumu, spot ölçüm örnekleri, kamera başına Kalibrasyon Hedefi onay kutusu (her zaman kapalı olarak açılır) ve dizi hizalama profili (tasarım gereği oturum başına yeniden hesaplanır).
* Tek istisna: **Çekim Ayarları** dışa aktarma seçimleri ve çekim modu, `cameras.json` yerine yerel uygulama depolama alanında proje bazında korunur — bkz. [Çekim Ayarları ve Modları](capture.md).</array></camera></serial>
