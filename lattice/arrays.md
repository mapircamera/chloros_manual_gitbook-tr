# Çoklu Kamera Dizileri

Bir LATTICE **dizisi**, tek bir senkronize birim olarak bağlanmış iki veya daha fazla LATTICE kameradan oluşur. Kameralardan biri**ana**kameradır: bu kamera, paylaşılan bir senkronizasyon hattı (varsayılan**Line2**) üzerinden bir donanım GPIO tetikleme darbesi gönderir, böylece her üye aynı anı yakalar. Chloros

, PTP zaman senkronizasyonu, canlı önizleme (kamera başına döşemeler veya tek bir hizalanmış çok bantlı bileşik) ve senkronize çekim özelliği ekler — her çekim geçişi, tüm kameraların aynı zaman damgası ve kare kimliğini paylaştığı bir **kare grubu** oluşturur (çekim çıktısında `fid:N` olarak bildirilir).

Diziler, mono (M3M) kameraların bitki örtüsü indekslerini oluşturma yöntemidir — bir kamera bir bant sağlar ve dizi bunları çok bantlı bir yığın halinde hizalar. Bkz. [Tek Kanallı Kameralar ve Bitki Örtüsü Endeksleri](mono-indices.md).

Bir diziyi bağlamanın üç eşdeğer yolu vardır ve hepsi aynı "smart-prep" akışını çalıştırır:

| Yüzey | Giriş noktası |
| --- | --- |
| GUI | Kameralar sekmesi → **Diziyi Bağla** (mavi düğme) |
| CLI

| `chloros-cli lattice array-connect --serials SN1,SN2,…` (ilk seri numarası = ana) |
| Python

SDK

| `connect_array(serials=[…])` → `ArraySession` (ilk seri numarası = ana cihaz) |

Smart-prep şunları gerçekleştirir: ağ yetenek testi (ICMP DF ping + GVSP testi), senkronizasyon katmanı seçimi, istenen kare boyutunda kare hızı tahmini, paketlenmiş 12 bit aktarım kararı (bkz. [Sıkıştırılmış 12-bit aktarım](#packed-12-bit-transport)), PTP etkinleştirme, kamera başına piksel formatı otomatik seçimi, her kameranın kaydedilmiş durumundan otomatik pozlama başlangıç değeri belirleme ve Line2 üzerinde GPIO tetikleyici yapılandırması.

**Varsayılan olarak tam sensör (1.3.0 sürümünde yeni).** Connect, talep ettiğiniz kare boyutunu korur — aksi bir seçim yapmadığınız sürece, tam 2048×1536 sensör, binning ve kırpma yapılmaz — ve kare hızının ana bilgisayar ile ağın sağladığı seviyeye düşmesine izin verir. Asla kendi başına birleştirme veya kırpma yapmaz (1.2.0 sürümünde 1,5 fps’nin altında otomatik birleştirme devreye giriyordu). Binning veya daha küçük bir ROI seçimi size kalmıştır: Connect iletişim kutusunda birini seçin; Projected Outcome, bunun sağlayacağı kare hızını gösterir; `array-connect`, daha hızlı seçenekleri öngörülen hızlarıyla birlikte yazdırır.

{% hint style="info" %}
Bunların herhangi birinin çalışabilmesi için kameralara bağlantı üzerinden erişilebilmesi gerekir — bkz. [Kameraları Bağlama](connecting.md) bölümüne bakın. Çoklu kamera kurulumlarında, ana bilgisayarın ağ kartının (NIC) alıcı halkası ayarları, bağlantı hızı kadar önemlidir; tam sorun→çözüm tablosu [CLI

Referansı § Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays) bölümünde yer almaktadır.
{% endhint %}

## Dizi Bağlantısı iletişim kutusu

Kameralar sekmesi → **Diziyi Bağla**seçeneği, üç adımlı bir sihirbazı açar:**Seç → Görüntüleme Modu → Ayarlar**.

### Adım 1 — Ana ve bağımlı kameraları seçin



<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered (an FRGB among them). Table showing Camera / Serial / IP / Master radio / Slave checkbox columns in camera order (M3C first, FRGB at the top), with the FRGB pre-selected as master and the green "Sync cable found on N cameras. Any of them can be master; the RGB camera (else the first) is pre-selected." probe banner visible above the table. -->

İletişim kutusu açılır açılmaz ağı tarar ("Ağı tarıyor..."), ardından hangi kameraların M8 senkronizasyon hattında olduğunu bulmak için senkronizasyon kablolarını kontrol eder ("Senkronizasyon kablolarını kontrol ediyor..."). Bir dizi oluşturmak için en az **2 kamera** gerekir.

M8 senkronizasyon kabloları tek bir bağlantı noktasında birleşir, bu nedenle **kablolanmış herhangi bir kamera ana kamera olabilir** olabilir. Tarama işlemi rolleri önceden doldurur — kablolu olduğunda RGB

(`-FRGB`) kamera ana kamera olarak, aksi takdirde ilk kablolu kamera ana kamera olarak ve diğer kablolu kameralar da bağımlı kamera olarak belirlenir — ve üç başlıktan birini gösterir:

| Başlık | Anlam |
| --- | --- |
| "{n} kamerada senkronizasyon kablosu bulundu. Bunlardan herhangi biri ana kamera olabilir; RGB

kamera (aksi takdirde listedeki ilk kamera) önceden seçilmiştir." (yeşil) | Prob, senkronizasyon hattındaki kameraları buldu; ana radyo ve bağımlı kamera onay kutuları zaten işaretlenmiştir. |
| "Ana kamera algılanmadı - GPIO kablosunu kontrol edin" (turuncu) | Hiçbir kamera tetikleme darbesi almadı; senkronizasyon kablolarını kontrol edin. Roller hâlâ önceden doldurulmuştur (RGB

kamera, yoksa listedeki ilk kamera ana kamera olarak) ve bunları değiştirebilirsiniz. |
| "Senkronizasyon Kablosu Yok: {seri numaraları}" (turuncu) | Listelenen kameralara senkronizasyon kablosu bağlı değil. |

Kamera tablosunda **Kamera / Seri Numarası / IP / Ana (radyo) / Bağımlı (onay kutusu)**sütunları bulunur. Satırlar**kamera sırasına** göre listelenir — M3C (Bayer) kameralar M3M (mono) kameralardan önce gelir; M3C, FRGB, FRGN, FOCN ve ardından FNGB sırasıyla; M3M kameralar ise filtre dalga boyuna göre, en düşükten başlayarak sıralanır (F450, F808'den önce). Kenar çubuğundaki kamera listesi ve tablonun varsayılan düzeni de aynı sırayı kullanır.

* Tam olarak **bir ana**ve**bir veya daha fazla bağımlı** seçin. Mevcut ana kameranın radyo düğmesine tekrar tıklamak, seçimi kaldırır. Farklı bir ana kamera seçmek tek bir tıklamayla gerçekleşir: yeni ana kamera, bağımlı kameralardan ayrılır ve önceki ana kamera (senkronizasyon kablosu yoksa) bir bağımlı kamera haline gelir.
* **"Senkronizasyon Kablosu Yok"** olarak işaretlenmiş bir kamera asla bağımlı olarak seçilemez — tetikleme kablosu olmayan bir bağımlı, senkronizasyon hattında sonsuza kadar bekler ve bozuk bir görüntü sağlar. Bu kamerayı bunun yerine bağımsız bir kamera olarak bağlayın.
* Zaten bağımsız olarak bağlanmış kameralar *devre dışı bırakılmaz*: dizi bağlantısı, bağımsız oturumu sonlandırır ve kamerayı dizi içinde yeniden açar.

**Sonraki: Görüntüleme Modu →**, bir ana kamera ve en az bir bağımlı kamera seçildiğinde etkinleşir.**Yeniden Tarama**, keşif ve kablolama taramasını yeniden çalıştırır.

{% hint style="warning" %}
**İptal**, bir tarama veya test devam ederken devre dışıdır — testin ortasında iptal etmek, LATTICE kamera donanım yazılımındaki kamera SDK

'sinin çökmesine neden olabilir. Dönen simgenin tamamlanmasını bekleyin.
{% endhint %}

### Adım 2 — Görüntüleme Modu

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->



| Mod | Ne elde edersiniz |
| --- | --- |
| **Ayrı Kameralar** | Kamera başına bir canlı döşeme; tümü aynı anda tetiklenir, böylece kareler senkronize kalır. Her kamera kendi rengini ve ayarlarını korur. |
| **Birleştirilmiş Kameralar** *(varsayılan)* | Hizalanmış çok bantlı NDVI

/indeks bileşimini gösteren tek bir kutucuk. Kameralar dizi rengini paylaşır. |

Görüntüleme modu yalnızca canlı önizleme sunumunu değiştirir — her iki modda da çekim davranışı aynıdır.

### Adım 3 — Dizi Ayarları ve Öngörülen Sonuç

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Trigger Rate / Wire Budget controls (1.3.0 has no "Pin resolution" checkbox), right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->



Bu sahneye girildiğinde Chloros

, arka uçtan bir **öneri**ister ve bunu uygular:**tam sensör — binning yok, ROI kırpma yok — herhangi bir kare hızı bunu destekleyebiliyorsa**, ana bilgisayar ve kablo ne hızda iletirse o hızda. Yalnızca hiçbir hız tam sensörü iletemediğinde öneri, kareyi küçültür ve bu durumda ROI kırpma yerine birleştirmeyi tercih eder, çünkü birleştirme tam görüş alanını korur. Bu noktadan itibaren çözünürlük ile kare hızı arasında bir denge kurarsınız. Yaptığınız her değişiklik, analizi canlı olarak yeniden çalıştırır ve sağ taraftaki**Öngörülen Sonuç** panelini günceller.

Sol sütun — ayarlar:

| Kontrol | Seçenekler | Varsayılan | Notlar |
| --- | --- | --- | --- |
| **ROI (Görüş Alanı)** | Tam (2048×1536) / Yarım (1024×768) / Çeyrek (512×384) | Tam | Sensör kırpma: Yarım/Çeyrek kırpma, doğal piksel aralığında daha küçük bir bölgeye yapılır. |
| **Binning** | 1× / 2× (toplam 2×2) / 4× (toplam 4×4) | 1× | Donanım binningi: 2×2 = kablo maliyetinin dörtte birinde tam FoV; 4×4 = 1/16'sında tam FoV. Kameralar binning'i desteklemiyorsa gizlenir. |
| **Kablo tarafı görüntü** (okuma) | — | — | Binning sonrası kablo üzerinden fiilen gönderilen genişlik×yükseklik, 16'nın katlarına (en az 64) yuvarlanır. |
| **Tetikleme Hızı** | 0,5–60 fps, adım 0,1 | boş = otomatik | Ana cihazın tetikleme hızı. Chloros

'un bunu hesaplamasına izin vermek için boş bırakın. |
| **Kablo Bütçesi**| 20–2000 MB/s, adım 10 | boş = otomatik | Ana bilgisayarın gerçekte ne kadarını alabileceği, MB/s cinsinden —**tüm dizi tahsisinin dayandığı tek sayı.** Ağ adaptöründen otomatik olarak algılanır. Dizi bozuk çerçeveler bildiriyorsa bu değeri düşürün: algılanan değer, USB adaptörleri ve paylaşımlı anahtarları olduğundan fazla gösterir. Değiştirildiğinde tahmin canlı olarak yeniden çalıştırılır. Alan, dizinin en son bağlandığı bütçeyle açılır (projeyle birlikte kaydedilir), böylece bir kez düzelttiğiniz bütçe korunur. |

1.2.0 sürümündeki **Pin çözünürlüğü** onay kutusu kaldırılmıştır: Bağlantı, seçtiğiniz çerçeve boyutunu her zaman korur ve bunun yerine kare hızını düşürür.

Sağ sütun — **Tahmin Edilen Sonuç**:

* **Senkronizasyon kademesi** — "Eşzamanlı yakalama" (yeşil), "Eşzamanlı yakalama (FTD-kademeli yayın)" (yeşil), "Kademeli yakalama (100 ms sapma)" (sarı) veya "Yapılandırma çok büyük" (kırmızı). Chloros

, kademeyi seçer: kablonun, kameraların hızına uyarlanabilecek bir hızda paylaşılabildiği durumlarda eşzamanlı yakalama (her kameranın payı azaldıkça kare hızı düşer) ve paylaşılamadığı durumlarda ise kademeli yakalama — böylece bir dizi, herhangi bir ağa, o ağın izin verdiği kare hızında bağlanır.
* **fps projeksiyonu** — dizinin çalışması beklenen kare hızı. Eşzamanlı yakalamada bu tek bir sayıdır: önce kare hızı gelir ve otomatik pozlama bunun sağladığı süreyi doldurur, ardından kazancı artırır; böylece loş bir sahne kare hızını düşürmez (bkz. [Otomatik pozlamanın pozlama ve kazancı nasıl kullandığı](camera-settings.md)). Kademeli çekimde ise bu, bir aralık olarak gösterilir (“loş → parlak”), çünkü loş bir sahnede her kameranın pozlama süresi kademelendirmeyi uzatır.
* **NIC hattı** — bağlantı hızı ve sürekli veri aktarım hızı (“NIC {mbps} Mbps · sürekli {N} MB/s”).
* **Sim-emit patlama kontrolü** — ana bilgisayarın NIC alıcı halkası, tüm kameralardan gelen eşzamanlı bir patlamayı alabilir mi ("Sim-emit patlama: X MB · kullanılabilir NIC halkası: Y MB ✓/✗").
* **Kablo bütçesi kontrolü** — kararlı durumdaki toplam talep ile çarpışma güvenliği sağlayan kablo tavanı karşılaştırması ("Kablo bütçesi: {n} kamera tarafından talep edilen {talep} MB/s · tavan {tavan} MB/s ✓/✗ aşırı talep").
* **"Bu kablodaki maksimum kamera sayısı: {n} — kamera başına bant genişliği alt sınırı tarafından belirlenir, bu nedenle gruplama bu sayıyı artırmaz."** — kamera sayısı üst sınırına yaklaştığınızda (veya aştığınızda) gösterilir.
* **"Bu ayarlarda KARELER KAYBOLACAK."**— arka uçtaki nedeni içeren kırmızı uyarı, ayrıca engelleyiciler listesi ve mavi**düzeltme önerileri** ("Bu diziyi ağa sığdırmak için" / "Eşzamanlı yakalamayı etkinleştirmek için").**Uygula ve Bağlan**, bir tahmin oluşana kadar devre dışı kalır ve etiketinde reddedilme nedeni belirtilir:

| Düğme etiketi | Anlamı | Aslında neyin yardımcı olacağı |
| --- | --- | --- |
| "Analiz ediliyor..." | Analiz hâlâ devam ediyor. | Bekleyin. |
| **"Bu ağ için çok fazla kamera var"**| Kablo, diziyi tek seferde bir kamera bile taşıyamıyor (toplam kontrol başarısız): ağın işleyebileceğinden çok daha fazla kamera var veya ağda başka kameraların akışı devam ediyor. | Daha az kamera, uçtan uca jumbo çerçeveler veya daha hızlı bir ağ kartı.**Daha küçük bir ROI yardımcı OLMAYACAK** — aşağıya bakın. |
| **"Etkinleştirmek için ROI'yi azaltın"** | Bu ayarlarda çerçeveler düşecektir (patlama/halka kontrolü başarısız). | ROI'yi azaltın, binning'i artırın veya NIC alım halkasını düzeltin. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->



Bağlantı kurulurken, seri numarası başına bir ilerleme çubuğu içeren yeşil bir **kalibrasyon indirme paneli** görünebilir: Bir makineye ilk kez kamera bağlandığında, Chloros

, GigE üzerinden kameradan ~3,8 MB'lık fabrika kalibrasyon paketini alır (kamera başına yaklaşık 70 saniye). Önbelleğe alınmış kameralarda bu panel asla görünmez; altıdan fazla kamera olduğunda satırlar kayar. Bkz. [Kameraları Bağlama](connecting.md).

### Sıkıştırılmış 12 bit aktarım

12 bit formatlar (`BayerRG12`, `Mono12`) normalde 16 bitlik kelimeler halinde aktarılır. **Sıkıştırılmış**aktarım, aynı 12 biti piksel başına 1,5 bayt olarak gönderir ve bunları ana bilgisayarda açar; böylece her karenin gönderilmesi %25 daha az zaman alır — tam sensör kapasitesinde, saniyede yaklaşık üçte bir daha fazla kare elde edilir.**Kaydedilen veriler aynıdır.** Array Connect bunu otomatik olarak belirler (`auto`): yalnızca bunun faydalı olduğu ve ana bilgisayarda açma işlemi için yeterli CPU gücü olduğu durumlarda sıkıştırma yapar. GUI'de bunun için bir anahtar yoktur; CLI

'den `array-connect --packed {auto,on,off}` komutunu kullanın (varsayılan `auto`). 8-bit ve 16-bit formatlar asla paketlenmez.

## Bant Genişliği: Kaç kamera sığar

Bir dizinin taşıyabileceği kapasite, Chloros

'un değil, hattın bir özelliğidir; bu nedenle planlama rakamları donanım kılavuzunda yer alır: **[Dizi Bant Genişliği Planlaması](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Chloros

'un bunlarla yaptığı şey: bağlantı iletişim kutusu bir ağ taraması gerçekleştirir, ulaşılabilir kare hızını tahmin eder ve uygun bir seviye seçer. Bir dizi herhangi bir ağa bağlanabilir: yavaş bir bağlantıda, dizinin reddedilmesi veya karelerinin küçültülmesi yerine, her kamera kablo kapasitesindeki payına göre hızlandırılır ve kare hızı düşer. Yalnızca, hattın bir seferde tek bir kamerayı bile taşıyamayacağı bir dizi reddedilir (**Bu ağ için çok fazla kamera var**); paketler sessizce atılmaz — yukarıda açıklanan tahmini sonuç paneline bakınız.

## Kareler kaybolduğunda

Bir kamera, tamamen farklı iki nedenden dolayı yayınlanan bir grupta yer almayabilir
ve bu durumların çözümü birbirinin tam tersidir. Chloros

, her ikisini de belirtmeyen tek bir
"eksik" sayı bildirmek yerine bunları ayrı ayrı sayar:

| Ne oldu | Ne anlama geliyor | Nereye bakmalı |
| --- | --- | --- |
| **Bozuk**— çerçeve ulaştı ancak yapısal olarak hatalıydı | Ağ yolunda GVSP paket kaybı |**Kablo bütçesi**, NIC alım halkası, jumbo çerçeveler, anahtar |
| **Hiç ulaşmadı**— hiç çerçeve gelmedi | Kamera tetiklenmedi veya kameradan hiçbir şey çıkmadı |**M8 senkronizasyon kablosu**, senkronizasyon hattı, tüm üyelerin devrede olup olmadığı |

Dizi akış halindeyken bölünme her 10 saniyede bir yeniden değerlendirilir. %5'in üzerinde ise
her iki sayı da belirtilerek günlüğe kaydedilir ve her bozuk tampon, kamera başına ilk kez
olduğunda raporlanır, ardından uzun bir oturumun okunabilir kalması için dakikada bir toplanır.

**"Hiç ulaşmadı" değeri sıfır olan bozuk kareler, tetikleme ve kablo senkronizasyonunun mükemmel olduğu anlamına gelir**ve kaybolan her kare ağ yolundadır. Çözüm,**Tel Bütçesi**'ni düşürmek ve
yeniden bağlanmaktır.

{% hint style="warning" %}
**Tetikleme hızını düşürmek, bozuk kareler sorununa çözüm olmaz.** Kameranın paket
hızı, bağlantı kurulduğunda bir kez belirlenir. Tetikleme hızını düşürmek, patlamanın ne sıklıkta
gerçekleştiğini değiştirir; patlamanın kabloya ne kadar hızlı aktarıldığını değil. Ölçüm yapılan 4 kameralı bir sistemde,
tetikleme hızının 5 kat azaltılması hiçbir değişiklik yaratmazken, kablo bütçesinin 240'tan
200 MB/s'ye düşürmek, aynı sistemdeki bozuk kare oranını %10,4'ten sıfıra indirdi.
{% endhint %}

Çalışmakta olan bir dizi kendini yeniden planlayamaz — bağlantıyı kesip yeniden bağlayın, böylece bağlantı süresi
seçici yeni bant genişliği sınırına göre çalışabilir.

### USB ağ adaptörleri sınırlandırılmıştır

Bir USB Ethernet adaptörü, *Ethernet* bağlantı hızını bildirir, ancak gerçekte
sürdürebileceği hız, USB veriyolu ve sürücüsü tarafından sınırlandırılır. Bir USB 10GbE dongle’a eskiden
yaklaşık 1000 MB/s veri aktarım hızı — ki bu rakam daha önce hiç ölçülmemişti — ve dört kamerayı bu hayali kapasiteye göre
ayarlamak, karelerin %6–18’ini bozarken, dizi
hala sağlıklı bir hedef kare hızı bildiriyordu. USB’ye bağlı adaptörler artık Windows ve NVIDIA Jetson’da
Windows

ve NVIDIA Jetson’da **200 MB/s**, Linux

x86_64’te ise **400 MB/s** ile sınırlandırılmıştır. Bu sınır, yüzde değil mutlak bir değerdir; çünkü sınırlayıcı unsur
veriyoludur: Bir USB 1 GbE adaptörü yaklaşık 80 MB/s hız sağlar ve bu sınırdan etkilenmez.

Eğer ana bilgisayarınız sınırdan gerçekten daha hızlıysa, **Wire Budget** değerini buna göre artırın.

## PTP zaman senkronizasyonu

Kare *senkronizasyonu* donanım tetikleyicisinden sağlanır; **PTP** (IEEE 1588 PTPv2) tüm cihazlar arasında karşılaştırılabilir *zaman damgaları* sağlar. Dizi bağlandığında varsayılan olarak etkindir:

* **PTP grandmaster'ı Chloros

ana bilgisayar arka ucu çalıştırır**. LATTICE kameralar ve DAQ-E ışık sensörleri, etki alanı 0'da buna bağlanır; böylece görüntü zaman damgaları ve DAQ spektrumları tek bir saat üzerinde (~1 ms) toplanır.
* `--no-ptp` (CLI

) komutu, laboratuvar çalışmaları için bunu devre dışı bırakır — bu durumda kameralar arası zaman damgaları **karşılaştırılamaz**.
* CLI

ile senkronizasyon durumunu kontrol edin:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity, health (OK, or why its packets are not reaching the network)
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Kameralar sekmesinde PTP göstergesi bulunmaz; burada kamera başına senkronizasyon bilgileri olarak salt okunur **Rol**(Ana/Bağımlı),**Senkronizasyon Hattı** ve dizinin Yetenekler katmanı gösterilir. DAQ-E PTP durumu, Işık Sensörleri sekmesindeki sensör ayrıntılarında gösterilir.

## Canlı dizi görünümü

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows in camera order (M3C before M3M, monos by wavelength), and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

Ana besleme alanı iki düzen sunar (üst çubuktan geçiş yapabilirsiniz): **ızgara görünümü**(her döşeme bir hücredir; ızgara kilidi açıldığında sürükleyerek yeniden sıralayabilirsiniz) ve**liste görünümü**(tam genişlikte satırlar:**birleştirilmiş**bir dizi tek bir satırdır — bileşik karesidir — oysa**ayrı**bir dizi,**her üyeye kendi satırını**verir; her birinin kendi canlı akışı ve kendi Kalibrasyon Hedefi tablosu vardır; dizilerin altında, tek bir bağımsız kamera — aktif olan).**Akış Yakınlaştırma** kaydırıcısı kutucukların boyutunu ayarlar; hücre genişliği 200 px'in altında olduğunda isim/fps üst yazıları otomatik olarak gizlenir. Üye kameralar kenar çubuğunda ve ızgaranın varsayılan düzeninde kamera sırasına göre görünür (M3C, M3M'den önce; önce FRGB, ardından dalga boyuna göre monokrom kameralar); ızgaranın kilidini açıp sürükleme işlemi, ızgara sıralamasını yine de geçersiz kılar.

<!-- SCREENSHOT-NEEDED: list view of a connected SEPARATE array (1.3.0): one full-width row per member camera, each with its live feed and its own Calibration Target table below it. -->



**Ayrı mod**, kamera başına bir döşeme gösterir. Her döşeme şunları içerir:

* kamera adı (sol üst),
* bir **fps göstergesi** (sağ üst) — bu, önizleme sorgulama hızı değil, arka uç tarafından bildirilen kameranın *gerçek yakalama hızı*dır (canlı önizleme, yakalama hızından bağımsız olarak 30 fps ile sınırlıdır),
* bir durum noktası — yeşil (akış) / sarı (yükleniyor) / kırmızı (hata),
* 2 saniye boyunca yeni bir kare gelmediğinde bir **eski kare dönen simgesi** — arka uç, kameralar arasında veri aktarım bütçesini yeniden dengelerken, herhangi bir bağlantı kurulması veya kesilmesinden sonra yaklaşık 5 saniye boyunca bu durum normaldir.**Birleştirilmiş mod**, tek bir bileşik döşeme gösterir: arka uç, bayes giderme işlemini gerçekleştirir, ölçeklendirir, hizalar, gürültüyü giderir, bant başına parlaklığa dönüştürür (bir ışık sensörü bağlandığında DLS yansıtma değeri de eklenir), dizinin indeks ifadesini değerlendirir, LUT'u uygular ve sonucu MJPEG olarak aktarır. İlk hizalanmış kare işlenene kadar, döşeme durumunu şu şekilde açıklar: "Dizi hazırlanıyor…", "Hizalama kalibrasyonu yapılıyor…", "İlk kare bekleniyor…" veya — döşemenin otomatik hizalama denemeleri (yaklaşık bir dakika) sona erdiğinde —**Hizalamayı kalibre et** düğmesi ile birlikte "Hizalama gerekli".

Dizi döşemesinin fps göstergesi de akışın durumunu bildirir: ayar değişikliği (format, binning, ROI) sırasında kameralar yeniden başlatılırken **"Ayarlar uygulanıyor…"**, dizi çalışır durumda ancak kare gruplarını iletmeyi durdurduğunda**"Durdu"**ve akış durduğunda**"Durduruldu"**akış durduğunda — bu durumda diziyi yeniden bağlayın. Spektral ayrıştırma işlemi, kompozitte gösterilen bir bandı piksellerinin büyük bir kısmında (%25 veya daha fazla) sınırlamak zorunda kaldığında, döşeme**"Düşük güvenilirlikli bant verisi · N%"** mesajını gösterir: o bant üzerine oluşturulan indeks değerleri şüphelidir (genellikle bir bant çok az ışık almıştır).

Kombine modla ilgili faydalı bilgiler:

* Kompozit, **ana**kameranın karesine kayıtlıdır. Kompozit üzerindeki AE-ROI hedefleme ve nokta ölçümü, ana kamera için tam, bağımlı kameralar için ise yaklaşık değerlerdir; ekstra kamera bağlantıları açmadan kamera başına piksel hassasiyetinde döşemeler için**Bölünmüş Görünüm**'ü (dizi ayarları → "Üye kameraları göster") kullanın.
* **Katmanları Göster**(dizi ayarları; varsayılan olarak kapalı) özelliği, ön plan ve arka plan katmanını seçmenize olanak tanır — herhangi bir üye kamera veya**Dizin**. Ön plan = Dizin olduğunda, LUT Min/Maks değerlerinin dışındaki pikseller arka plan katmanını gösterir.
* **Render çözünürlüğü** (varsayılan 720p), canlı akış yüksekliğini *ve* kaydedilen kompozit dışa aktarım boyutunu belirler. Kamera başına görüntüler her zaman tam çözünürlükte dışa aktarılır.
* Hizalama, oturum başına hesaplanır ve asla kalıcı olarak kaydedilmez. Chloros

, üyelerin otomatik pozlaması sabitlendiğinde (5 dakikaya kadar denemeye devam eder), **her diziyi bağlandıktan sonra her iki görüntüleme modunda da otomatik olarak hizalar**. Dizi ayarları panelindeki**Hizalama**bölümü, RMS kalıntılarını ve**Hizalamayı yeniden kalibre et** düğmesini gösterir — bu, kompozit döşeme içermeyen ayrı diziler için de geçerlidir.

## Yakalama: izleme ve analiz

Dizi yakalama yüzeyleri, **izleme sınıfı**(gördüğünüzü kaydedin) ve**analiz sınıfı** (ham verileri kaydedin, daha sonra kalibre edin) olarak net bir şekilde ayrılır:

| İş Akışı | Sınıf | Neyi kaydeder | GUI | CLI

|
| --- | --- | --- | --- | --- |
| **Yakalama**(hareketsiz görüntüler) | Analiz | Her geçiş için senkronize bir kare grubu; seçilen her dışa aktarma düzeyinde kamera başına dosyalar (ham/debayered/parlaklık/yansıtma/önizleme/indeks) + `.daq` sidecar |**Tümünü Yakala** düğmesi + Yakalama Ayarları | `lattice array-capture` |
| **Dizin videosu kaydet** | İzleme | Görüntülenen canlı birleştirilmiş dizin kompoziti — 8 bit, önizleme çözünürlüğü, LUT dahil. GUI görünümü açık olmadan CLI

'den, dizi hizalandıktan sonra arka uç dizinin NDVI

'sini (veya `--expr`) işler; kare yazmayan bir kayıt başarısız olur | ● İndeks videosu kaydet (birleştirilmiş diziler) | `lattice array-record` |
| **Ham seri çekim → video oluşturma**| Analiz | Tam yakalama hızında ham sensör kareleri + manifest + `.daq`, ardından DAQ okumalarıyla zaman uyumlu hale getirilmiş, kalibre edilmiş parlaklık / yansıma / indeks videosuna çevrimdışı yeniden yapılandırma | ⦿ Ham seri çekim kaydet →**Video oluştur** | `lattice array-burst` → `lattice array-build-video` |

Genel kural: pikseller *ölçümlere* kaynak olacaksa, yakalama veya seri çekim (analiz sınıfı) kullanın; dizinin gördüklerini sadece *izlemek veya göstermek* istiyorsanız, indeks videosunu kaydedin (izleme sınıfı).

### Yakalama Ayarları (GUI)



<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->

**Tümünü Yakala** seçeneğinin yanındaki dişli simgesi, Yakalama Ayarları penceresini açar (açık bir proje gerektirir — yakalamalar bu projeye kaydedilir):

* **Yakalama modu**:**Tek**(tek geçiş) /**Sürekli**(arka arkaya; yakalama sayısı [varsayılan 1] veya süre [varsayılan 10 s] ile sınırlı) /**Aralıklı** (zaman atlamalı: X aralıklarla toplam Y çekim yapılır; varsayılan ayar 1 dakika boyunca her 5 saniyede bir).
* **Kamera başına dışa aktarma türleri**: Ham, Debayered, Işılma, Yansıma, Önizleme, Dizin — geçerli olan tüm seçenekler varsayılan olarak etkindir. Işılma/Yansıma, RGB

filtreli kameralar için gizlenmiştir; **Yansıma, yalnızca kamerada bir DAQ ışık sensörü olduğunda görünür** (kendi sensörü veya diziden devralınan); Dizin, yapılandırılmış bir dizin ifadesi gerektirir.
* **Hizalanmış**(dizi başına, varsayılan**açık**): üye dışa aktarımlarını dizinin hizalama profiline göre dönüştürür, böylece dışa aktarımlar piksel bazında hizalanır. Ham veriler her zaman hizalanmamış olarak kalır, ancak dönüşüm metaverdelerde taşınır. Dizi üye dosyaları, her kameranın kendi orijinal yöneliminde kaydedilir (herhangi bir Ayna/Dönüş ayarı dosyaya kaydedilir, uygulanmaz).
* **En Hızlı Yakalama** (açma/kapama): yalnızca ham veriler + atanan DAQ okuma değeri + ücretsiz birleştirilmiş indeks kompoziti; maksimum hız için yakalama sırasında kalibrasyon hesaplamaları atlanır — parlaklık/yansıtma/indeks değerleri daha sonra kaydedilen `.daq` dosyasından yeniden oluşturulur.
* Seçimler projeyle birlikte korunur. Gizlenmiş veya duraklatılmış kameralar atlanır.
* Dizilerdeki **Sürekli** modu, Chloros

arka ucunda çalışır (1.3.0 sürümünde yeni): listedeki her kamera bir diziye ait olduğunda, arka uç yakalama döngüsünü kendisi çalıştırır; her yakalama için ayrı bir istek göndermeden bir grubu yazarken bir sonrakini seçer. Sürücünün dolması işlemi durdurur ("Durduruldu: sürücü dolu. N kaydedildi, F başarısız"). Yüksek yakalama hızlarıyla ilgili Microsoft Defender notu için [Yakalama Ayarları ve Modları](capture.md#capture-modes) bölümüne bakın.

Eşdeğer CLI

: (aynı arka uç, aynı anlam):

```bash
# One synced group, every applicable export level per camera (the default)
chloros-cli lattice array-capture -o output/

# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# 30-second monitoring clip of the combined index view, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# 5-second analysis-grade raw burst, then build the combined index video
chloros-cli lattice array-burst --duration 5 --build --products combined:index --fps 10 -o capture/
```

Yakalamalar için TIFF

sıkıştırması `deflate` (kayıpsız, varsayılan) veya `none`'tir — tam bayrak tabloları, yakalama klasörü düzeni ve yeniden işleme kuralları [CLI

Referansı](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess) içinde yer almaktadır.

## Bir DAQ ışık sensörünü eşleştirme

Yansıtma ve aydınlatma düzeltmeli önizlemeler için, bir DAQ sensöründen (**Işık Sensörleri** sekmesinde bağlı olan) gelen aşağı doğru ışık verileri gereklidir:

* Kenar çubuğundaki **dizi satırı**, bir**"DAQ · açık/kapalı"**düğmesi gösterir — dizi düzeyinde bir ışık sensörü ayarlandığında**veya** üye kameralardan herhangi birinin kendi sensörü olduğunda *açık* durumdadır; araç ipucunda hangi sensörün hangi kameraya veri sağladığı tam olarak listelenir.
* Dizi ayarlarında → **Ortam Işığı Sensörü**→**Işık Sensörü** açılır menüsünden dizi genelinde atama yapın. Seçim projeyle birlikte kalır, her üye kameraya yayılır ve tek tek kameralar yine de kendi sensörleriyle bu ayarı geçersiz kılabilir.
* Altındaki durum satırı canlı durumu bildirir: **Kapalı**→ "İlk spektrum bekleniyor…" →**"Etkin — dizideki tüm kameraların aydınlatma düzeltmesi yapılmıştır"** → veya son 3 saniye içinde yeni bir spektrum gelmediyse, eski bir bildirim — son okuma değeri kullanılmaya devam eder (okuma değerleri yakalama yolunda asla geçerliliğini yitirmez).

Bir sensör atandığında: Yansıtma (Reflectance) dışa aktarma türü kullanılabilir hale gelir, canlı önizlemeler aydınlatma düzeltmesine tabi tutulur, tahmini otomatik pozlama spektrumu kullanabilir ve her yansıtma yakalama işlemi, yakalamaların `daq/` klasörüne kaydedilir; böylece yakalama daha sonra yeniden işlenebilir. Bunun yanında, bir `.daq.cameras.json` dosyası, o okumanın hangi kameraların karelerini tanımladığını belirtir; yeniden işleme sırasında Chloros

, okumayı yalnızca bu kameralardan gelen görüntülerle eşleştirir.

## `array-connect` CLI

seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--serials SN1,SN2,…` | tüm LATTICE kameralarını otomatik olarak algıla (en az 2 tane gerekir) | **İlk seri numarası MASTER'dır.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO senkronizasyon hattı. |
| `--target-fps F` | otomatik | Master tetikleme atış hızı. |
| `--binning {1,2,4}` | otomatik | Donanım binningi. "Otomatik" seçeneği, herhangi bir kare hızında desteklendiğinde tam sensör (binning yok) anlamına gelir; hızı yüksek tutmak için "asla binleme" seçeneğini kullanın. |
| `--packed {auto,on,off}` | `auto` | [Sıkıştırılmış 12-bit aktarım](#packed-12-bit-transport): `auto`, yalnızca faydalı olduğu ve ana bilgisayarın açma işlemini kaldırabileceği durumlarda paketler. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | otomatik | Senkronizasyon katmanı seçicisinin uzman tarafından geçersiz kılınması. |
| `--wire-ceiling-mbps MB_PER_S` | otomatik algılama | Ana bilgisayar kablo bütçesi (MB/s) — **Kablo Bütçesi** alanının CLI

biçimi. Dizi bozuk çerçeveler bildiriyorsa bu değeri düşürün. Projeyle birlikte kaydedilir, böylece daha sonra yeniden bağlandığında bu değer geri yüklenir. |
| `--no-recommend` | kapalı | Ağ analizi adımını atlayın. |
| `--no-ptp` | kapalı | PTP'yi devre dışı bırakın (bu durumda kameralar arası zaman damgaları karşılaştırılamaz). |

`lattice array-list`, `array-status` ve `array-disconnect`, kalıcı oturumu yönetir. Hizalama (`align-calibrate` / `align-apply`) ve ağ araçları dahil olmak üzere tam alt komut referansı, [CLI

Referansı § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice)bölümünde yer almaktadır; SDK

'daki karşılıkları (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) ise [SDK

Referansı](../reference/sdk-reference.md) bölümünde yer almaktadır. Python

'da kablo bütçesi `connect_array(..., wire_ceiling_mbps=120)`'tir ve canlı bozuk/hiç ulaşmamış ayrımı [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames) adresindedir.
