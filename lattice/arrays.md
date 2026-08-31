# Çoklu Kamera Dizileri

Bir LATTICE **dizisi**, tek bir senkronize birim olarak bağlanmış iki veya daha fazla LATTICE kameradan oluşur. Bir kamera**ana**kameradır: paylaşılan bir senkronizasyon hattında (varsayılan**Line2**) bir donanım GPIO tetikleme darbesi gönderir, böylece her üye aynı anı yakalar. Chloros, PTP zaman senkronizasyonu, canlı önizleme (kamera başına döşemeler veya tek bir hizalanmış çok bantlı kompozit) ve senkronize çekim özelliği ekler — her çekim geçişi, tüm kameraların aynı zaman damgası ve kare kimliğini paylaştığı bir**kare grubu** oluşturur (çekim çıktısında `fid:N` olarak bildirilir).

Diziler, mono (M3M) kameraların bitki örtüsü indekslerini üretme yöntemidir — bir kamera bir bant sağlar ve dizi bunları çok bantlı bir yığın halinde hizalar. Bkz. [Mono Kameralar ve Bitki Örtüsü İndeksleri](mono-indices.md).

Bir diziyi bağlamanın üç eşdeğer yolu vardır ve hepsi aynı &quot;smart-prep&quot; akışını çalıştırır:

| Yüzey | Giriş noktası |
| --- | --- |
| GUI | Kameralar sekmesi → **Diziyi Bağla** (mavi düğme) |
| CLI | `chloros-cli lattice array-connect --serials SN1,SN2,…` (ilk seri numarası = ana kamera) |
| Python SDK | `connect_array(serials=[…])` → `ArraySession` (ilk seri numarası = ana cihaz) |

Smart-prep, sırasıyla şunları gerçekleştirir: ağ yetenek testi (ICMP DF ping + GVSP testi), senkronizasyon katmanı seçimi, kabloya sığması için çerçeve boyutunun otomatik olarak küçültülmesi, PTP etkinleştirme, kamera başına piksel formatının otomatik seçimi, her kameranın kaydedilmiş durumundan otomatik pozlama başlangıç değeri belirleme ve Line2 üzerinde GPIO tetikleyici yapılandırması.

{% hint style="info" %}
Bunların herhangi birinin çalışabilmesi için kameralara bağlantı üzerinden erişilebilir olması gerekir — keşif, adresleme ve ilk bağlantı kalibrasyon indirme işlemleri için [Kameraları Bağlama](connecting.md) bölümüne bakın. Çoklu kamera kurulumlarında, ana bilgisayarın ağ kartının (NIC) alıcı halkası ayarları, bağlantı hızı kadar önemlidir; tam semptom→çözüm tablosu [CLI Referans § Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması](../reference/cli-reference.md#host-nic-setup--tuning-lattice-arrays) bölümünde yer almaktadır.
{% endhint %}

## Dizi Bağlantısı iletişim kutusu

Kameralar sekmesi → **Diziyi Bağla**seçeneği, üç adımlı bir sihirbaz açar:**Seç → Görüntüleme Modu → Ayarlar**.

### Adım 1 — Ana ve bağımlı

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Select scene, with 3-4 LATTICE cameras discovered. Table showing Camera / Serial / IP / Master radio / Slave checkbox columns, with the green "GPIO master detected — selections pre-populated" probe banner visible above the table. -->

kameraları seçin İletişim kutusu açılır açılmaz ağı tarar (&quot;Ağı tarıyor...&quot;), ardından GPIO tetikleme kablolamasını kontrol eder (&quot;GPIO kablolamasını kontrol ediyor...&quot;). Bir dizi oluşturmak için en az **2 kamera** gerekir.

Kablolama denetimi, mümkün olduğunda rol seçimini önceden doldurur ve üç başlıktan birini bildirir:

| Başlık | Anlam |
| --- | --- |
| &quot;GPIO ana kamera algılandı — seçimler önceden dolduruldu&quot; (yeşil) | Tarama, tetikleme topolojisini buldu; ana kamera ve bağımlı kamera onay kutuları zaten işaretlenmiştir. |
| &quot;Ana birim algılanmadı - GPIO kablosunu kontrol edin&quot; (turuncu) | Hiçbir kamera tetikleme darbesi algılamadı; senkronizasyon kablolarını kontrol edin. Rolleri yine de manuel olarak seçebilirsiniz. |
| &quot;Senkronizasyon Kablosu Yok: {seri numaraları}&quot; (turuncu) | Listelenen kameraların hiçbirine senkronizasyon kablosu bağlı değil. |

Kamera tablosunda **Kamera / Seri Numarası / IP / Master (radyo) / Slave (onay kutusu)** sütunları bulunur:

* Tam olarak **bir master**ve**bir veya daha fazla slave** seçin. Mevcut master&#x27;ın radyo seçeneğine tekrar tıklandığında seçim silinir.
* **&quot;Senkronizasyon Kablosu Yok&quot;** olarak işaretlenmiş bir kamera asla bağımlı olarak seçilemez — tetikleme kablosu olmayan bir bağımlı kamera, senkronizasyon hattında sonsuza kadar bekler ve bozuk bir görüntü sağlar. Bu kamerayı bunun yerine bağımsız bir kamera olarak bağlayın.
* Zaten bağımsız olarak bağlanmış kameralar *devre dışı bırakılmaz*: dizi bağlantısı, bağımsız oturumu sonlandırır ve kamerayı dizi içinde yeniden açar.

**Sonraki: Görüntüleme Modu →**seçeneği, bir ana kamera ve en az bir bağımlı kamera seçildiğinde etkinleşir.**Yeniden Tara** seçeneği, keşif ve kablolama taramasını yeniden çalıştırır.

{% hint style="warning" %}
Taraması veya denetimi devam ederken **İptal** seçeneği devre dışıdır — denetim sırasında iptal etmek, LATTICE kamera donanım yazılımında SDK kamerasının çökmesine neden olabilir. Dönen simgenin tamamlanmasını bekleyin.
{% endhint %}

### Adım 2 — Görüntüleme Modu

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Display Mode scene, showing the two selectable cards ("Separate Cameras" and "Combined Cameras") with Combined selected/highlighted as the default. -->

| Mod | Ne elde edersiniz |
| --- | --- |
| **Ayrı Kameralar** | Kamera başına bir canlı döşeme; tümü aynı anda tetiklenir, böylece kareler senkronize kalır. Her kamera kendi rengini ve ayarlarını korur. |
| **Birleştirilmiş Kameralar** *(varsayılan)* | Hizalanmış çok bantlı NDVI/index kompozitini gösteren tek bir kutucuk. Kameralar dizi rengini paylaşır. |

Ekran modu yalnızca canlı önizleme sunumunu değiştirir — her iki modda da yakalama davranışı aynıdır.

### Adım 3 — Dizi Ayarları ve öngörülen sonuç

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, healthy state: left column with ROI / Binning / Pin resolution / Trigger Rate controls, right "Projected Outcome" column showing green "Simultaneous capture" tier, an fps range, the NIC line, the "Sim-emit burst" line, and the "Wire budget" line with a checkmark. -->

Bu sahneye girildiğinde Chloros, arka uçtan bir **öneri**ister ve NIC&#x27;nizin alım halkasına uyan bir ROI + binning kombinasyonunu otomatik olarak uygular (binning, tam görüş alanını koruduğu için ROI kırpma yerine tercih edilir). Yaptığınız her değişiklik, analizi canlı olarak yeniden çalıştırır ve sağ taraftaki**Öngörülen Sonuç** panelini günceller.

Sol sütun — ayarlar:

| Kontrol | Seçenekler | Varsayılan | Notlar |
| --- | --- | --- | --- |
| **ROI (Görüş Alanı)** | Tam (2048×1536) / Yarım (1024×768) / Çeyrek (512×384) | Tam | Sensör kırpma: Yarım/Çeyrek kırpma, doğal piksel aralığında daha küçük bir bölgeye yapılır. |
| **Binning** | 1× / 2× (toplam 2×2) / 4× (toplam 4×4) | 1× | Donanım binning: 2×2 = kablo maliyetinin dörtte birinde tam FoV; 4×4 = 1/16&#x27;da tam FoV. Kameralar binning&#x27;i desteklemiyorsa gizlenir. |
| **Kablo tarafı görüntü** (okuma) | — | — | Binning işleminden sonra kablo üzerinden fiilen gönderilen genişlik×yükseklik, 16&#x27;nın katlarına (en az 64) yuvarlanır. |
| **Pin çözünürlüğü**| onay kutusu | kapalı | Chloros, normalde bağlantı kurulduğunda öngörülen hız**1,5 fps**&#x27;nin altına düştüğünde binning&#x27;i otomatik olarak artırır. Pinning, seçtiğiniz kare boyutunu korur ve daha düşük hızı kabul eder — ayrıca aşırı yüklenmiş bir yapılandırmayı otomatik hız düşürme yerine sert bir bağlantı reddi haline getirir. |
| **Tetikleme Hızı** | 0,5–60 fps, adım 0,1 | boş = otomatik | Ana cihazın tetikleme atış hızı. Chloros&#x27;in bunu hesaplamasına izin vermek için boş bırakın. |
| **Kablo Bütçesi**| 20–2000 MB/s, adım 10 | boş = otomatik | Ana bilgisayarın gerçekte ne kadarını MB/s cinsinden alabileceği —**tüm dizi tahsisinin dayandığı tek sayı.** Ağ adaptöründen otomatik olarak algılanır. Dizi bozuk çerçeveler bildiriyorsa bu değeri düşürün: algılanan değer, USB adaptörleri ve paylaşımlı anahtarları olduğundan daha yüksek gösterir. Değiştirildiğinde projeksiyon canlı olarak yeniden çalıştırılır. |

Sağ sütun — **Tahmin Edilen Sonuç**:

* **Senkronizasyon kademesi** — &quot;Eşzamanlı yakalama&quot; (yeşil), &quot;Eşzamanlı yakalama (FTD-kademeli yayın)&quot; (yeşil), &quot;Kademeli yakalama (100 ms sapma)&quot; (sarı) veya &quot;Yapılandırma çok büyük&quot; (kırmızı).
* **fps projeksiyonu** — bir aralık olarak gösterilir (&quot;sönük → parlak&quot;), çünkü senkronize bir dizinin hızı en yavaş kameranın pozlama süresine bağlıdır.
* **NIC satırı** — bağlantı hızı ve sürekli veri aktarım hızı (&quot;NIC {mbps} Mbps · sürekli {N} MB/s&quot;).
* **Sim-emit patlama kontrolü** — ana bilgisayarın NIC alıcı halkası, tüm kameralardan gelen eşzamanlı bir patlamayı alabilir mi (&quot;Sim-emit patlama: X MB · kullanılabilir NIC halkası: Y MB ✓/✗&quot;).
* **Kablo bütçesi kontrolü** — kararlı durumdaki toplam talep ile çarpışma güvenliği sağlayan kablo tavanı karşılaştırması (&quot;Kablo bütçesi: {n} kamera tarafından talep edilen {talep} MB/s · tavan {tavan} MB/s ✓/✗ aşırı talep&quot;).
* **&quot;Bu kablodaki maksimum kamera sayısı: {n} — kamera başına bant genişliği alt sınırı tarafından belirlenir, bu nedenle gruplama bu sayıyı artırmaz.&quot;** — kamera sayısı üst sınırına yaklaştığınızda (veya aştığınızda) gösterilir.
* **&quot;Bu ayarlarda KARELER DÜŞECEK.&quot;**— arka uçtaki nedeni içeren kırmızı uyarı, ayrıca engelleyiciler listesi ve mavi**düzeltme önerileri** (&quot;Bu diziyi ağa sığdırmak için&quot; / &quot;Eşzamanlı yakalamayı etkinleştirmek için&quot;).**Uygula ve Bağlan** seçeneği, bir tahmin oluşturulana kadar devre dışı kalır ve düğme etiketi reddedilme nedenini belirtir:

| Düğme etiketi | Anlam | Gerçekten yardımcı olan |
| --- | --- | --- |
| &quot;Analiz ediliyor...&quot; | Analiz hâlâ devam ediyor. | Bekleyin. |
| **&quot;Bu ağ için çok fazla kamera var&quot;**| Dizi, kablo kapasitesini aşıyor (toplam kontrol başarısız). | Daha az kamera, uçtan uca jumbo çerçeveler veya daha hızlı bir NIC.**Daha küçük bir ROI yardımcı OLMAYACAKTIR** — aşağıya bakın. |
| **&quot;Etkinleştirmek için ROI&#x27;yi azaltın&quot;** | Bu ayarlarda çerçeveler düşer (patlama/halka kontrolü başarısız). | ROI&#x27;yi azaltın, binning&#x27;i artırın veya NIC alım halkasını düzeltin. |

<!-- SCREENSHOT-NEEDED: Array Connect dialog, Settings scene, over-subscribed state: red "Wire budget ... over-subscribed" line, the "Max cameras on this wire" hint, and the Apply button reading "Too many cameras for this network". Reproduce by configuring more cameras than the 1 GbE ceiling (e.g. 7+ cams at 1500 MTU) or with CHLOROS-simulated models via `lattice analyze-array`. -->

Bağlantı kurulurken, seri numarası başına bir ilerleme çubuğu içeren yeşil bir **kalibrasyon indirme paneli** görünebilir: Bir makineye ilk kez kamera bağlandığında, Chloros, GigE üzerinden kameradan yaklaşık 3,8 MB&#x27;lık fabrika kalibrasyon paketini indirir (kamera başına yaklaşık 70 saniye). Önbelleğe alınmış kameralarda bu panel asla görünmez. Bkz. [Kameraları Bağlama](connecting.md).

## Bant Genişliği: kaç kamera sığar

Bir dizinin taşıyabileceği kapasite, Chloros’in değil, hattın bir özelliğidir; bu nedenle planlama rakamları donanım kılavuzunda yer alır: **[Dizi Bant Genişliği Planlaması](https://mapir.gitbook.io/lattice-camera/setup/array-bandwidth-planning)**.

Chloros&#x27;in bunları nasıl kullandığı: bağlantı iletişim kutusu bir ağ denetimi gerçekleştirir, ulaşılabilir kare hızını tahmin eder ve uygun bir kademe seçer. Dizi, kablo kapasitesini aşarsa, paketleri sessizce atmak yerine bağlantıyı reddeder — yukarıda açıklanan tahmini sonuç paneline bakınız.

## Çerçeveler kaybolduğunda

Bir kamera, yayınlanan bir grupta iki tamamen farklı nedenden dolayı yer almayabilir
ve bu durumların çözümleri birbirinin tam tersidir. Chloros, her ikisini de belirtmeyen tek bir
&quot;eksik&quot; sayı bildirmek yerine bunları ayrı ayrı sayar:

| Ne oldu | Ne anlama geliyor | Nereye bakmalı |
| --- | --- | --- |
| **Bozuk**— çerçeve ulaştı ancak yapısal olarak hatalıydı | Ağ yolunda GVSP paket kaybı |**Hattın kapasitesi**, NIC alım halkası, jumbo çerçeveler, anahtar |
| **Hiç ulaşmadı**— hiç çerçeve gelmedi | Kamera tetiklenmedi veya kamera çıkışından hiçbir şey gelmedi |**M8 senkronizasyon kablosu**, senkronizasyon hattı, tüm üyelerin devrede olup olmadığı |

Dizi akış halindeyken bölünme her 10 saniyede bir yeniden değerlendirilir. %5&#x27;in üzerinde ise
her iki sayı da belirtilerek günlüğe kaydedilir ve her bozuk tampon, kamera başına ilk kez
olduğunda raporlanır, ardından uzun bir oturumun okunabilir kalması için dakikada bir toplanır.

**&quot;Hiç ulaşmadı&quot; değeri sıfır olan bozuk kareler, tetikleme ve kablo senkronizasyonunun mükemmel olduğunu gösterir**ve her kayıp kare ağ yolunda yer alır. Çözüm,**Tel Bütçesi**&#x27;ni düşürmek ve
yeniden bağlanmaktır.

{% hint style="warning" %}
**Tetikleme hızını düşürmek, bozuk kareler sorununu çözmez.** Kameranın paket
hızı, bağlantı kurulduğunda bir kez belirlenir. Tetikleme hızını düşürmek, patlamanın ne sıklıkta
gerçekleştiğini değiştirir; patlamanın kabloya ne kadar hızlı aktarıldığını değil. Ölçüm yapılan 4 kameralı bir kurulumda
tetikleme hızının 5 kat azaltılması hiçbir şeyi değiştirmezken, kablo bütçesinin 240&#x27;tan
200 MB/s&#x27;ye düşürülmesi, aynı kurulumdaki bozukluk oranını %10,4&#x27;ten sıfıra indirdi.
{% endhint %}

Çalışmakta olan bir dizi kendini yeniden planlayamaz — bağlantıyı kesip yeniden bağlayın, böylece bağlantı zamanı
seçici yeni bant genişliği sınırına göre çalışabilir.

### USB ağ adaptörleri 200 MB/s ile sınırlıdır

Bir USB Ethernet adaptörü, *Ethernet* bağlantı hızını belirtir, ancak gerçekte
sürdürebileceği hız, USB veriyolu ve sürücüsü tarafından sınırlanır. Bir USB 10GbE dongle&#x27;a eskiden
yaklaşık 1000 MB/s verim atfedilirdi — ki bu rakam hiçbir zaman ölçülmemişti — ve dört kamerayı bu hayali kapasiteye göre ayarlamak, karelerin %6–18&#x27;ini bozarken, dizi
hala sağlıklı bir hedef kare hızı bildiriyordu. USB&#x27;ye bağlı adaptörler artık
hala sağlıklı bir hedef kare hızı bildiriyordu. USB ile bağlanan adaptörler artık
**200 MB/s** ile sınırlandırılmıştırile sınırlandırılmıştır. Bu sınır, yüzde değil mutlak bir değerdir; çünkü sınırlayıcı unsur
veriyoludur: Bir USB 1 GbE adaptörü yaklaşık 80 MB/s hız sağlar ve bu sınırdan etkilenmez.

Eğer ana bilgisayarınız bu sınırdan gerçekten daha hızlıysa, **Wire Budget** değerini buna göre artırın.

## PTP zaman senkronizasyonu

Kare *senkronizasyonu* donanım tetikleyicisinden gelir; **PTP** (IEEE 1588 PTPv2) tüm cihazlar arasında karşılaştırılabilir *zaman damgaları* sağlar. Dizi bağlandığında varsayılan olarak etkindir:

* **Chloros ana bilgisayar arka ucu, PTP grandmaster&#x27;ı çalıştırır**. LATTICE kameraları ve DAQ-E ışık sensörleri, etki alanı 0&#x27;da buna bağlanır; böylece görüntü zaman damgaları ve DAQ spektrumları tek bir saat frekansında (~1 ms) eşleşir.
* `--no-ptp` (CLI), laboratuvar çalışmaları için bunu devre dışı bırakır — bu durumda kameralar arası zaman damgaları **karşılaştırılamaz**.
* CLI ile senkronizasyon durumunu kontrol edin:

```bash
chloros-cli time-sync status     # grandmaster state, clock identity
chloros-cli time-sync peers      # slaves seen (cameras + DAQ-E sensors)
chloros-cli time-sync cameras    # per-camera PtpStatus / PtpOffsetFromMaster / PtpMeanPathDelay
```

Kameralar sekmesinde PTP göstergesi bulunmaz; burada kamera başına senkronizasyon bilgileri olarak salt okunur **Rol**(Ana/Bağımlı),**Senkronizasyon Hattı** ve dizinin Yetenekler katmanı gösterilir. DAQ-E PTP durumu, Işık Sensörleri sekmesindeki sensör ayrıntılarında gösterilir.

## Canlı dizi görünümü

<!-- SCREENSHOT-NEEDED: Cameras tab with a connected combined array: sidebar showing the ARRAY row (color badge, array name, "DAQ · on" pill) with indented member camera rows, and the main area showing the combined index composite tile with the LUT-colored NDVI render, top-left array name pill, and top-right fps readout. -->

Ana besleme alanı iki düzen sunar (üst çubuktan geçiş yapabilirsiniz): **ızgara görünümü**(her döşeme bir hücredir; ızgara kilidi açıldığında sürükleyerek yeniden sıralayabilirsiniz) ve**liste görünümü**(üstte tam genişlikte diziler, altında bir aktif kamera).**Besleme Yakınlaştırma** kaydırma çubuğu kutucukların boyutunu ayarlar; hücre genişliği 200 px&#x27;in altında olduğunda ad/fps üst yazıları otomatik olarak gizlenir.**Ayrı mod**, kamera başına bir kutucuk gösterir. Her kutucukta şunlar yer alır:

* kamera adı (sol üst),
* bir **fps göstergesi** (sağ üst) — bu, önizleme sorgulama hızı değil, arka uç tarafından bildirilen kameranın *gerçek yakalama hızı*dır (canlı önizleme, yakalama hızından bağımsız olarak 30 fps ile sınırlıdır),
* bir durum noktası — yeşil (akış halinde) / sarı (yükleniyor) / kırmızı (hata),
* 2 saniye boyunca yeni bir kare gelmediğinde bir **eski kare dönen simgesi** — arka uç, kameralar arasında veri aktarım bütçesini yeniden dengelerken, herhangi bir bağlantı kurulması veya kesilmesinden sonra yaklaşık 5 saniye boyunca bu durum normaldir.**Birleştirilmiş mod**, tek bir bileşik döşeme gösterir: arka uç, debayering işlemini gerçekleştirir, ölçeklendirir, hizalar, gürültüyü giderir, bant başına parlaklığa dönüştürür (bir ışık sensörü bağlandığında DLS yansıma değeri de eklenir), dizinin indeks ifadesini değerlendirir, LUT&#x27;u uygular ve sonucu MJPEG olarak aktarır. İlk hizalanmış kare işlenene kadar, döşeme durumunu şu şekilde açıklar: &quot;Dizi hazırlanıyor…&quot;, &quot;Hizalama kalibre ediliyor…&quot;, &quot;İlk kare bekleniyor…&quot; veya — otomatik hizalama yeniden deneme süresi (~30 saniye) dolduğunda — &quot;Hizalama gerekli&quot; mesajı ile birlikte bir**Hizalamayı kalibre et** düğmesi görüntülenir.

Kombine modla ilgili faydalı bilgiler:

* Kompozit, **ana**kameranın karesine kayıtlıdır. Kompozit üzerindeki AE-ROI hedefleme ve spot ölçüm, ana kamera için tam, bağımlı kameralar için ise yaklaşık değerlerdir; ekstra kamera bağlantıları açmadan kamera başına piksel hassasiyetinde döşemeler için**Bölünmüş Görünüm**&#x27;ü (dizi ayarları → &quot;Üye kameraları göster&quot;) kullanın.
* **Katmanları Göster**(dizi ayarları; varsayılan olarak kapalı) özelliği, ön plan ve arka plan katmanını seçmenize olanak tanır — herhangi bir üye kamera veya**Dizin**. Ön plan = Dizin olduğunda, LUT Min/Maks değerlerinin dışındaki pikseller arka plan katmanını gösterir.
* **Render çözünürlüğü** (varsayılan 720p), canlı akış yüksekliğini *ve* kaydedilen kompozit dışa aktarım boyutunu belirler. Kamera başına görüntüler her zaman tam çözünürlükte dışa aktarılır.
* Hizalama, oturum başına hesaplanır ve asla kalıcı hale getirilmez — RMS kalıntıları ve Yeniden Kalibre Et düğmesi için dizi ayarları penceresinin hizalama bölümüne bakın.

## Yakalama: izleme ve analiz

Dizi yakalama yüzeyleri, **izleme sınıfı**(gördüğünüzü kaydedin) ve**analiz sınıfı** (ham verileri kaydedin, daha sonra kalibre edin) olarak net bir şekilde ayrılır:

| İş Akışı | Sınıf | Kaydedilenler | GUI | CLI |
| --- | --- | --- | --- | --- |
| **Yakalama**(sabit görüntüler) | Analiz | Her geçiş için bir senkronize kare grubu; seçilen her dışa aktarma düzeyinde kamera başına dosyalar (ham/debayered/parlaklık/yansıma/önizleme/indeks) + `.daq` sidecar |**Tümünü Yakala** düğmesi + Yakalama Ayarları | `lattice array-capture` |
| **Dizin videosu kaydet** | İzleme | Görüntülenen canlı birleştirilmiş dizin kompoziti — 8 bit, önizleme çözünürlüğü, LUT dahil; canlı akışın açık olması gerekir | ● Dizin videosu kaydet (birleştirilmiş diziler) | `lattice array-record` |
| **Ham seri çekim → video oluşturma**| Analiz | Tam yakalama hızında ham sensör kareleri + manifest + `.daq`, ardından DAQ okumalarıyla zaman uyumlu, kalibre edilmiş parlaklık / yansıma / indeks videosuna çevrimdışı yeniden yapılandırma | ⦿ Ham seri çekim kaydet →**Video oluştur** | `lattice array-burst` → `lattice array-build-video` |

Genel kural: pikseller *ölçümleri* besleyecekse, yakalama veya seri çekim modunu kullanın (analiz sınıfı); dizinin gördüklerini sadece *izlemek veya göstermek* istiyorsanız, indeks videosunu kaydedin (izleme sınıfı).

### Yakalama Ayarları (GUI)

<!-- SCREENSHOT-NEEDED: Capture Settings pane (gear next to Capture All) with a connected array: capture-mode buttons (Single/Continuous/Interval), the bulk export-type toggle row, the Fastest Capture toggle, and the per-array group card showing the Aligned checkbox and the "Record index video" / "Record raw burst" buttons. -->

**Tümünü Yakala** seçeneğinin yanındaki dişli simgesi, Yakalama Ayarları panelini açar (açık bir proje gerektirir — yakalamalar bu projeye kaydedilir):

* **Yakalama modu**:**Tek**(tek geçiş) /**Sürekli**(arka arkaya; yakalama sayısı [varsayılan 1] veya süre [varsayılan 10 s] ile sınırlı) /**Aralıklı** (zaman atlamalı: X aralıklarla toplam Y yakalama; varsayılan olarak 5 saniyede bir, 1 dakika boyunca).
* **Kamera başına dışa aktarma türleri**: Raw, Debayered, Radiance, Reflectance, Preview, Index — geçerli olan tüm seçenekler varsayılan olarak etkindir. Radiance/Reflectance, RGB-filtreli kameralar için gizlenmiştir;**Yansıma, yalnızca kamerada bir DAQ ışık sensörü olduğunda** görünür (kendi sensörü veya diziden devralınan); Dizin, yapılandırılmış bir dizin ifadesi gerektirir.
* **Hizalanmış**(dizi başına, varsayılan**açık**): üye dışa aktarımlarını dizinin hizalama profiline göre dönüştürür, böylece dışa aktarımlar piksel bazında hizalanır. Raw her zaman dönüştürülmemiş olarak kalır, ancak dönüşümü meta verilerde taşır.
* **En Hızlı Yakalama** (açma/kapama): yalnızca ham veriler + atanan DAQ okuma değeri + ücretsiz birleştirilmiş indeks kompoziti; maksimum hız için yakalama sırasında kalibrasyon hesaplamaları atlanır — parlaklık/yansıtma/indeks daha sonra kaydedilen `.daq`&#x27;ten yeniden oluşturulur.
* Seçimler projeyle birlikte korunur. Gizlenmiş veya duraklatılmış kameralar atlanır.

Eşdeğer CLI (aynı arka uç, aynı anlambilim):

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

Yakalamalar için TIFF sıkıştırması, `deflate` (kayıpsız, varsayılan) veya `none`&#x27;tir — tam bayrak tabloları, yakalama klasörü yapısı ve yeniden işleme kuralları [CLI Referansı](../reference/cli-reference.md#capture-modes-recorders--offline-reprocess).

## Bir DAQ ışık sensörünü eşleştirme

Yansıma ve aydınlatma düzeltmeli önizlemeler için, **Işık Sensörleri** sekmesinden bağlanmış bir DAQ sensöründen gelen aşağı doğru ışık verileri gereklidir:

* Kenar çubuğundaki **dizi satırı**, bir**&quot;DAQ · açık/kapalı&quot; düğmesi**gösterir — dizi düzeyinde bir ışık sensörü ayarlandığında**veya** üye kameralardan herhangi birinin kendi sensörü olduğunda *açık* durumdadır; araç ipucunda hangi sensörün hangi kameraya veri sağladığı tam olarak listelenir.
* Dizi ayarlarında → **Ortam Işığı Sensörü**→**Işık Sensörü** açılır menüsünden dizi genelinde atama yapın. Bu seçim projeyle birlikte kalır, her üye kameraya yayılır ve tek tek kameralar kendi sensörleriyle bu ayarı geçersiz kılabilir.
* Altındaki durum satırı canlı durumu bildirir: **Kapalı**→ &quot;İlk spektrum bekleniyor…&quot; →**&quot;Etkin — dizideki tüm kameraların aydınlatma düzeltmesi yapılmıştır&quot;** → veya son 3 saniye içinde yeni bir spektrum gelmediyse, eski bir uyarı — son okuma değeri kullanılmaya devam eder (okuma değerleri yakalama yolunda hiçbir zaman geçerliliğini yitirmez).

Bir sensör atandığında: Yansıtma (Reflectance) dışa aktarma türü kullanılabilir hale gelir, canlı önizlemeler aydınlatma düzeltmesine tabi tutulur, tahmini otomatik pozlama spektrumu kullanabilir ve her yansıtma yakalama işlemi, daha sonra yakalamanın yeniden işlenebilmesi için gerçekten kullanılan DAQ okumasını görüntünün **`.daq` sidecar** olarak görüntülerin yanına yazılır; böylece yakalama işlemi daha sonra yeniden işlenebilir.

## `array-connect` CLI seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--serials SN1,SN2,…` | tüm LATTICE kameralarını otomatik olarak algıla (en az 2 tane gerekir) | **İlk seri numarası MASTER’dır.** |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO senkronizasyon hattı. |
| `--target-fps F` | otomatik | Master tetikleme atış hızı. |
| `--binning {1,2,4}` | otomatik | Donanım binningi. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | otomatik | Senkronizasyon kademesi seçicisinin uzman tarafından geçersiz kılınması. |
| `--wire-ceiling-mbps MB_PER_S` | otomatik algılanır | MB/s cinsinden ana bilgisayar kablo bütçesi — **Kablo Bütçesi** alanının biçimi. Dizi bozuk çerçeveler bildiriyorsa bu değeri düşürün. Projeyle birlikte kaydedilir, böylece daha sonra yeniden bağlandığında geri yüklenir. |
| `--no-recommend` | kapalı | Ağ analizi adımını atlayın. |
| `--no-ptp` | kapalı | PTP&#x27;yi devre dışı bırakın (bu durumda kameralar arası zaman damgaları karşılaştırılamaz). |

`lattice array-list`, `array-status` ve `array-disconnect`, kalıcı oturumu yönetir. Hizalama (`align-calibrate` / `align-apply`) ve ağ araçları dahil olmak üzere tam alt komut referansı, [CLI Referansı § chloros-cli lattice](../reference/cli-reference.md#chloros-cli-lattice); SDK eşdeğerleri (`connect_array`, `ArraySession`, `attach_array`, `analyze_array_network`) [SDK Referansı](../reference/sdk-reference.md) içindedir. Python&#x27;ten itibaren kablo bütçesi `connect_array(..., wire_ceiling_mbps=120)`&#x27;tir ve canlı bozuk/hiç ulaşmamış ayrımı [`/api/camera/array/<id>/capability`](../reference/sdk-reference.md#array-health--which-subsystem-is-losing-frames) adresinde yer almaktadır.
