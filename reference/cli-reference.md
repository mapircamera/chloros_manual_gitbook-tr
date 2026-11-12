# Chloros CLI Kılavuzu

**Sürüm:**

1.3.0**Oluşturulma Tarihi:**29 Temmuz 2026 19:19 ·**Güncelleme Tarihi:** 6 Kasım 2026**Hedef Kitle:** LLM kullanımı için optimize edilmiştir; insanlar tarafından okunabilir.**Kapsam:** `chloros-cli`'in kullanıcıya yönelik tüm alt komutları, seçenekleri ve kopyala-yapıştır yapılabilen örnekleri ile birlikte.

Bu belge, MAPIR Chloros ile birlikte gelen `chloros-cli` komut satırı aracı için eksiksiz bir referanstır. LLM'nin (veya bir insanın) kaynak kodunu incelemeden aşağıdaki listelerden desteklenen herhangi bir iş akışını oluşturabilmesi için kasıtlı olarak kapsamlı bir şekilde hazırlanmıştır.

Yalnızca önemli noktalara göz atmak istiyorsanız, şu bölümlere geçin:
- [Beş Dakikalık Hızlı Başlangıç](#five-minute-quickstart)
- [LATTICE Kamera İlk Bağlantı İş Akışı](#lattice-camera-first-connect-workflow)
- [DAQ Sensör İlk Bağlantı İş Akışı](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](#capture-modes-recorders--offline-reprocess)

> **Chloros'u bir AI asistanından mı çalıştırıyorsunuz?** Claude Desktop, Claude Code, Cursor ve diğer MCP istemcileri, Python SDK ile birlikte kurulan (ve Linux .deb paketiyle `/usr/bin` içine gelen) MCP sunucusu `chloros-mcp` aracılığıyla işleme, LATTICE kameraları ve dizileri ile DAQ ışık sensörlerini çalıştırabilir: `claude mcp add chloros -- chloros-mcp`. Kurulum, seçenekler ve araçlar, SDK referansındaki [MCP Sunucusu (AI Asistanları)](sdk-reference.md#mcp-server-ai-assistants) bölümünde yer almaktadır. Kabuk (shell) içeren bir asistan da bu sayfadaki her komutu doğrudan çalıştırabilir.

> **1.3.0 sürümünde değiştirilenler** (mevcut bir 1.2.0 komut dosyası veya iş akışının fark edeceği değişiklikler).
>
> **Bir 1.2.0 komut dosyasının ele alması gerekebilecek değişiklikler:**
>
> - **`--preset` geçerlidir.** `lattice capture`, `continuous`, `calibrate`, `multi-capture`, `multi-stream`, `align-calibrate`, `align-apply` ve `align-stream` komutları 1.2.0 sürümünde bu seçeneği yok sayıyor ve kameranın kendi ayarlarıyla çalışıyordu. Artık ön ayarın formatını, pozlamasını, kazancını ve kare hızını belirliyor: `--preset high_speed`, 5 ms, 6 dB ve 60 fps ile 8 bit çekim yapar. `lattice cam-connect --preset` de ön ayarın formatını korur (1.2.0 sürümünde ön ayar uygulandıktan sonra kamera 12 bit'e ayarlanıyordu). Bkz. [Ortak Seçenekler](#common-options-shared-by-most-subcommands).
> - **M3M (mono) bir kameradaki Bayer piksel formatı, o kameranın derinliğidir.** Ön ayarlar, mono sensörün çekemeyeceği Bayer formatlarını adlandırır: 1.2.0 sürümünde kamera bu formatı reddetti ve mevcut formatını korudu, bu nedenle `--preset high_speed` orada 8-bit değildi. Artık aynı derinlikteki Mono formatındadır: `high_speed` ve `--pixel-format BayerRG8`, `Mono8` değerini verir; `high_quality`, `triggered` ve `BayerRG12`, `Mono12` sonucunu verir. Karışık bir M3C + M3M dizisinde her kamera kendi ailesindeki ortak formatı alır ve `network-analysis` / `analyze-array --pixel-format`, her kamerayı o derinlikte yansıtır. Bunun tersi de geçerlidir: Mono formatı istenen bir M3C, o derinliğin Bayer formatını alır; dolayısıyla ana kamerası M3M olan bir dizi, M3C kameralarını artık Mono modunda yeniden açmaz. Bkz. [Mono (M3M) Tek Bantlı Kameralar](#mono-m3m-single-band-cameras).
> - **`lattice array-capture` dışa aktarımları, uygulamadakiler gibi varsayılan olarak hizalanır.** Her üyenin `debayered`, `display`, `radiance`, `reflectance` ve dizin dosyaları, ana kameranın karesine uyarlanır ve üyelerin örtüşme alanına göre kırpılır (kameraların ofsetleri nedeniyle sensörden daha küçük, `XMP-mapir:AlignmentJSON` `"applied": true`) göre kırpılır; dolayısıyla bir CLI yakalaması, aynı sahnenin Chloros uygulaması tarafından yakalanan dosyalarla aynıdır; `raw` ise orijinal boyutunda kalır. Önceden, `--aligned` kullanılmayan bir yakalama işleminde her dosya, düzeltilmemiş olarak orijinal boyutunda kaydedilirdi. `--no-aligned` bu özelliği korur. Aynı varsayılan ayar, `--smart`, `--continuous` ve `--interval` için de geçerlidir; ayrıca SDK ve `chloros-mcp` için de geçerlidir. Bkz. [`array-capture` modları](#array-capture-modes).
> - **`lattice array-capture --compression` varsayılan olarak `none`'e ayarlanır.** En yüksek sürekli kayıt hızı için dosyalar sıkıştırılmadan yazılır (tam çözünürlüklü her kare için yaklaşık 6,3 MB); 1.2.0 sürümü `deflate` (yaklaşık 4,1 MB) formatında yazıyordu. Daha küçük dosyalar için `--compression deflate` değerini kullanın. Her ikisi de kayıpsızdır.
> - **PNG ve JPEG, yalnızca `display` / `preview` ve indeksli ürünleri kabul eder.** 8 bitlik bir dosyanın barındıramayacağı bir seviyeye sahip bir `--format png` veya `jpg` yakalama işlemi, herhangi bir yakalama yapılmadan reddedilir (HTTP 400, `1`'ten çık), aynı şekilde `--format png`/`jpg` ile birlikte kullanılan varsayılan `--processing all` de reddedilir. Bkz. [Yakalama Dışa Aktarma Düzeyleri](#capture-export-levels-the-all-default).
> - **`lattice dls` için `--mac` gereklidir.**Artık varsayılan MAC adresi yoktur (daha önce bir ünitenin adresiydi); `daq pool-discover --only ble` bir sensörün adresini bulur.**`lattice dls --camera SERIAL`** yenidir.
> - **Daha fazla hata, `1`'ten çıkar.** Bir çalışmanın dışa aktarımları doğrulanamadığında veya bir görüntü kaybolduğunda, diğer ürünler yazılmış olsa bile `process`; yazamadığı herhangi bir dosyayla bir yakalama komutu (diğer dosyalar yine de kaydedilir); `lattice continuous` ve `multi-stream`, sürücünün dolması durumunda; `array-burst` / `array-record`, kaydedici bir hata bildirdiğinde; İstenen bir ürün atlandığında veya istenen şekilde oluşturulmadığında: `array-build-video`; Yakalama işlemi yarıda başarısız olduğunda: `project burst` / `stream`; `project sensor log`, kayıtta okunacak veri bulunmadığında; `lattice exposure --set`, `gain --set` ve `configure --set`, kamera tarafından reddedilen bir değer olduğunda. `lattice network-analysis --json`, özet gibi sonlandırılır (`1`, `needs_force_slip` için). Tersine: Artık havuzda bulunmayan bir sensörün `daq pool-disconnect` kodu, `0`'ten çıkarX'ten çıkar. Bkz. [Çıkış Kodları](#exit-codes).
> - **Yavaş çalışan bir `lattice array-connect`, dizi bağlıyken asla hata vermez.** 600 saniyeye kadar bekler (1.2.0 sürümünde 180 saniye; `project connect` 90 saniye), bu sırada arka ucun adımlarını yazdırır ve o zamana kadar tamamlanmayan bir bağlantı, arka uç tarafından — her kamera bulunduğu duruma geri getirilir — dolayısıyla bir `Error:` (çıkış `1`) hatası, hiçbir dizinin bağlı olmadığı anlamına gelir. 1.2.0 sürümünde, kameraların açılmasından hemen sonra gerçekleşen bir bağlantı, bekleme süresini aşabilir ve komut başarısız olduktan sonra diziyi bağlayabilirdi. Artık Ctrl+C tuşları bağlantıyı iptal eder. Bkz. [Kalıcı Bağlantılar](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-connect`, sensörün tamamını korur.** `--width` / `--height` / `--binning` ayarları olmadığında, her kamera tam sensöre ayarlanır ve kare hızı bu bilgisayarın ve ağın sağlayabildiği düzeye düşer; bağlantı çıktısı, öngörülen hızlarıyla birlikte daha hızlı seçenekleri (binning veya daha küçük bir ROI) ve tahmini hızları listelenir. 1.2.0 sürümü, hızı yüksek tutmak için kareleri küçültür veya birleştirirdi. Bkz. [`array-connect` Seçenekleri](#array-connect-options).
> - **Yavaş bir ağ artık diziyi reddetmez.** Her kamera, kablo kapasitesindeki payına göre hızlandırılır ve kare hızı buna bağlı olarak düşer. Kameraların bu kadar yavaş hızlandırılamadığı durumlarda, dizi seri yayın modunda çalışır (`slip-emit-and-capture`) modunda çalışır: her kare gelir, ancak kameralar aynı anda pozlanmaz. `--force-tier sim-capture-sim-emit` ise eşzamanlı pozlamayı zorunlu kılar. Bkz. [Dizi fps ve seri çekim modeli](#array-fps--burst-model) bölümüne bakın.
> - **`lattice network-analysis` / `analyze-array`, connect'in neyi uyguladığını gösterir**: `--pixel-format` olmadan, connect'in belirlediği formatlar (Mono12 / BayerRG12, eskiden BayerRG8 idi) ve [sıkıştırılmış 12-bit](#packed-12-bit-transport---packed) seçimi. Bu nedenle aynı komut, 1.2.0 sürümünden farklı bir kare hızı gösterebilir.
> - **Göreceli yollar, komutu çalıştırdığınız konuma göre belirlenir.** `-o` ve arka ucun açtığı diğer yollar (`--input`, `--burst-dir`, `--profile`, `--save`, `--mask`), bir tarif adımının `output`, ve `lattice index --live`'in varsayılan `output/`'i, `chloros-cli`'i çalıştırdığınız klasöre göre çözümlenir. 1.2.0 sürümünde arka uç bunları kendi kurulum klasöründe çözümlüyordu; bu da kurulu bir derlemede "Erişim reddedildi" hatasıyla sonuçlanıyordu. Bkz. [Kural ve Gelenekler](#conventions).
> - **`lattice index`, diğer `lattice` komutları gibi her durumda arka uçta çalışır**, bu nedenle önce arka ucu başlatın. Çevrimdışı dizin (`--input`), arka uç tarafından hesaplanır; arka uç, TIFF dosyasını okur ve sonuçları dosyanın yanına veya `-o` konumuna yazar, ve `--list-presets` / `--list-gradients` komutları arka uç tablolarını yazdırır. Windows'te bunlar artık çalışıyor: önceden, Windows CLI'si `lattice index`'in her biçimine, `--live` dahil, `Error: lattice_sdk not available` ile yanıt veriyordu. Linux'da, çevrimdışı indeks eskiden arka uç ve oturum açma bilgisi olmadan CLI içinde çalışırdı; artık diğer tüm CLI komutları gibi her ikisine de ihtiyaç duyar. Bkz. [İndeks / Bitki Örtüsü Matematiği](#index--vegetation-maths).
> - **Ürünlerin yanında yeni dosyalar.** Bant ayrıştırma işlemi sırasında bir pikselin değeri sınırlandırılmış olan her radyans, yansıma ve indeks ürünü, ürün klasörünün yanındaki `clamp_masks/` klasöründe bir maskeye sahip olur ve çıktıda bir `clamp mask:` satırı yer alır. Bkz. [Unmix sınırlama maskesi](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on).
> - **Kamera yönelimi.** Tek kameralı bir çekim, `raw` ve `debayered` dosyalarını sensörün okuduğu şekilde kaydeder ve kameranın ters çevirme/dönme bilgilerini XMP'ye kaydeder; `process` dosyası ise bunu ürettiği ürünlere uygular. Dizi üye dosyaları, yeni `--orient` (`array-capture`, `array-burst`, `project capture` / `burst` / `stream`) değerini geçirmediğiniz sürece yerel kalır. Bkz. [Kamera yönü](#what-a-captures-folder-looks-like).
> - **Bir olmayan bir dışa aktarma düzeyi adı reddedilir.** `lattice capture --processing` ve tüm `--levels` listeleri, `raw`, `debayered`, `radiance`, `reflectance` veya `display` (ya da `preview` gibi bir takma ad; `--processing` ve ayrıca `all` gibi bir takma ad). Kameraya dokunulmadan önce başka herhangi bir isim reddedilir (HTTP 400 `UNKNOWN_LEVEL`, çıkış `1`); 1.2.0 bunun için `raw`'i kaydetti ve `0` ile çıktı.
> - **`daq pool-set-cap`, `--cap-id`'e ihtiyaç duyar.** Bu olmadan komut sonlandırılır `2`; 1.2.0, kapak düzeltmesini kaldırarak çıplak sensörü bıraktı. `--cap-id none`, çıplak sensörü ister.
> - **`lattice resolution --binning-mode` kaldırılmıştır.** Binning her zaman Ortalama'dır; bayrak hâlâ kabul edilir, bir uyarı yazdırılır ve hiçbir işlem yapılmaz. Bkz. [Sensör Ayarlaması](#sensor-tuning).
> - **`process --debayer texture-aware`, LATTICE yakalamaları için reddedilir.** LATTICE çekimlerini içeren bir klasör için, `process`, içe aktarımdan sonra `1`'ten çıkar ve herhangi bir işlem yapmadan önce kaç tane olduğunu belirtir. LATTICE çekimleri her zaman standart demosaic işleminden geçer, bu nedenle 1.2.0 sürümü bu seçeneği kabul edip yok saymıştır; Chloros uygulaması da bu çekimler için bu seçeneği sunmamaktadır. Bkz. [`chloros-cli process`](#chloros-cli-process).
> - **Kameralar, `--keep-settings`.**— `--keep-settings` hariç.** `lattice array-disconnect`, her geçici komut (dizi içermeyen `multi-capture`, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`) ve `lattice continuous`, her kamerayı buldukları duruma geri getirir: tetikleyici ve senkronizasyon hattı kablolaması, PTP ayarları ve oturum sırasında değiştirilen tüm kamera ayarları (pozlama ve kazanç, AE hedefi, sınırlar ve sönümleme, beyaz dengesi, siyah seviyesi, piksel formatı, birleştirme ve ROI, çevirmeler, kare hızı). `--keep-settings` ise her kameranınayarlarını kameranın kendi belleğine kaydeder, böylece kamera açıldığında bu ayarlarla çalışır; `--clear-settings` ise bu belleği fabrika ayarlarına sıfırlar. Her iki durumda da kameralar serbest çalışır durumda bırakılır. Bir proje, kameraları bağladığında kendi kaydedilmiş ayarlarından kameralarını yapılandırır. Bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings).
> - **Her mod, bir projeyi aynı şekilde kaydeder.** Bir projeye bağlı bir kamerada, dizide veya ışık sensöründe yapılan herhangi bir değişiklik — uygulama, CLI, SDK veya MCP sunucusu aracılığıyla — o projenin `cameras.json` / `sensors.json` klasörüne kaydedilir ve proje herhangi bir modda açıldığında cihazlar bu ayarlara göre yapılandırılır. Önceden, projeyi yalnızca uygulama kaydederdi ve bir sonraki kaydetme işlemi diğer tüm kayıtların üzerine yazardı. Bkz. [Tek proje kaydı, her mod](#one-project-record-every-mode).
> - **Birden fazla kameradan oluşturulan dosyalar her birini ayrı ayrı adlandırır.** Bir çok bantlı yığın (`align-apply`, `align-stream`, `index --live --save-multiband`), birkaç kameranın bantlarından oluşturulan canlı bir dizin ve dizi yakalamadan elde edilen `composite/` görüntüsü, her bir kaynak kameranın seri numarasını, modelini ve kalibrasyon paketini listeler halinde taşır (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …) listeler halinde taşır ve EXIF `SerialNumber`, seri numaralarını virgülle ayrılmış olarak listeler. Eskiden hiç kamera bilgisi taşımazlardı ya da yalnızca ilk kameranın seri numarasını içerirlerdi. Bkz. [Birden fazla kameradan oluşturulan dosyalar](#files-made-from-several-cameras).
> - **Yalnızca bir `raw` dosyası kameranın kalibrasyon etiketlerini içerir.** MicaSense uyumlu set (`BlackLevel`, `RadiometricCalibration`, kanal başına `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` setleri) ve `CalibrationJSON` yükü, sensörün ham değerlerini tanımlar; bu nedenle bunlar yalnızca `raw` yakalama dosyasında bulunur. Diğer tüm dosyalar — bir yakalamanın `debayered`, `display`, `radiance`, `reflectance` ve dizin dosyaları, `lattice continuous` ve `multi-stream` çerçeveleri, ve her bir `process` ürünü — kameranın kimliğini taşır (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) ve bir çekimde çerçevenin kendi kayıtlarını (`XMP-mapir:FrameJSON`: pozlama, kazanç, sıcaklık, piksel formatı, çekim zamanı) taşır. 1.2.0 sürümünde ham veri seti de taşınmıştı; bu set, ham değerler olmayan piksellere uygulanan bir araç (Pix4D, Metashape) ham değer olmayan piksellere uyguluyordu. `process`, bu tür bir dosyanın kalibrasyonunu seri numarası ve `CalBundleSha256` aracılığıyla bulur.
>
> **Diğer değişiklikler:**
>
> - **Bir dizinin otomatik kare hızı, dizinin sağladığı değere göre belirlenir.** Tetiklemelerinin %1’inden fazlasını kaybeden bir hız, tek bir inceleme yerine birkaç uzun süreli inceleme sonucunda değerlendirilir ve düşürüldüğü hız ara sıra yeniden denenir. Kendiniz ayarladığınız bir hız ise olduğu gibi bırakılır.
> - **Otomatik pozlama daha istikrarlıdır.** Sabit ışıkta sabit kalır, bir değişiklikten sonra saniyeler içinde dengelenir, küçük parlak noktaları göz ardı eder ve parlak güneş ışığında yanıt vermeyi durduran bir kamerayı tekrar çalışır hale getirir. Bkz. [Otomatik pozlamanın nasıl çalıştığı](#how-auto-exposure-behaves).
> - **`process` yalnızca görüntülerinizi ve kayıtlarınızı sayar.** Bir projenin `camera_cal/` dosyasını içeren bir klasör üzerinde yeniden çalıştırıldığında, bu kalibrasyon dosyaları artık sayılmaz (90 görüntülük bir sette "96 görüntü bulundu" mesajı verilir) ve bunları zaten almış olan bir proje ise bu dosyaları siler.
> - **`lattice multi-test`, kameraların açılma durumundan devam eder.** Çalıştırma için kameraların PTP'sini açar, ilk tetiklemeden önce saatlerinin kilitlenip sabitlenmesini bekler — her kamera, açıldıktan sonra yaklaşık 2 dakika boyunca 120 saniye boyunca kilitlenir (`--ptp-settle SECONDS` bunu ayarlar), çünkü yeni kilitlenmiş bir saat yaklaşık 2 dakika boyunca ~200 us'ye kadar sapma gösterir — ve her kameranın PTP'sini bulduğu gibi geri ayarlar. Daha önceki bir testten beri hala kilitli olan bir kamera için tekrar beklenmez. Eskiden, hiçbir DAQ-E'nin yanıt vermediği tek anahtarlı bir ağda PTP'yi devre dışı bırakır ve her kameranın serbest çalışan saatini, kilitlenemeyecek bir PTP saati olarak okurdu (`PTP is not locked`, her çalıştırmada). Bkz. [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions).
> - **Bir `.daq` kaydı, kapağının seçilmiş mi yoksa varsayılmış mı olduğunu belirtir.** DAQ-U, DAQ-M ve DAQ-E, kapaksız olarak gönderilir ve Chloros, bir kapağın takılı olup olmadığını göremez; bu nedenle, adı belirtilmemiş bir kapak olmadan bağlanan bir sensöre varsayılan olarak `sunshine_cosine` atanır; bu bir *varsayım*. Artık her Chloros kaydı (şema 1.30), `cap_id`'in yanında `cap_id_source`'i de depolar: Bir kapağa isim verdiğinizde (`none` dahil) `operator`, varsayılan olarak ayarlandığında ise `auto_default`. Kapağın varsayıldığı bir kaydı işlerken, projenin DAQ kapağı Otomatik olarak bırakıldığında, dosyaya `[daq-dls] WARNING` adını veren bir çıktı verir: Güneş ışığı düzeltmesi yapılmamış bir DAQ-U, yaklaşık 30 kat daha yüksek değerler okur. Bağlandığınızda **uyguladığınız sınırlamaya bir ad verin** veya projenin DAQ sınırlamasını ayarlayın; uyarı ortadan kalkacaktır.
> - **Windows'da artık pip konsol pencereleri açılmıyor.** Python'un kurulu olduğu bir bilgisayarda, arka uç ve her bir görüntü işleyicisi başlangıçta bir kez `pip` komutunu çalıştırırdı ve her biri bir konsol penceresi açıyordu. Artık açmıyorlar.
> - **Canlı `.daq` kayıtları elektrik kesintisine dayanır.**Bir kayıt, sensörün arkasında her commit işleminde bir günlük ve tam disk senkronizasyonu ile yazılır, böylece yavaş bir disk dosyayı geciktirir, ancak okumaları asla geciktirmez. Bir commit işleminin ortasında durdurulan bir kayıt (elektrik kesintisi, sonlandırma), yanında bir `<name>.daq-journal` dosyası bırakabilir:**ikisini bir araya kopyalayın.** Chloros, dosyayı ilk okuduğunda günlüğü geri alır; böylece kayıt, durma öncesinde işleme alınmış tüm verilerle açılır (yaklaşık son 3 saniye kaybolur); 1.2.0 sürümünde, yanlış bir anda meydana gelen bir elektrik kesintisi, dosyanın tamamını okunamaz hale getirebilirdi (“veritabanı disk görüntüsü hatalı”).
> - **Meşgul bir diskteki kayıt artık sensörü durdurmaz.** `daq pool-record`, okumaları kuyruğa alır ve bunları kendi iş parçacığında yazar; yazılamayan bir okuma, (`daq pool-list`, son hatayı içeren `N write error(s)`'i gösterir) sensörü dondurmak yerine sayılır (dört kamera TIFF dosyalarını kaydederken bir yazma işlemi 8,4 saniye sürdü).
> - **Dizisi olmayan `lattice multi-capture`, tetikleyici zaman aşımına uğradığında kaydettiği kareleri artık kaybetmez.** Zaman aşımına uğrayan bir kameranın tetikleyicisi (`SC_ERR_TIMEOUT -1011`, tek bir 1 GbE anahtardaki dört kamerada görüldü), çalışmayı o kare grubunda sonlandırır: önceden kaydedilmiş gruplar `Saved:` ile listelenir, ardından hata mesajı gelir ve çıkış `1`. 1.2.0 sürümünde, dosyalar diskte olmasına rağmen liste içermeyen bir HTTP 500 hatası veriliyordu.
> - **Yazılımla tetiklenen kameralar artık her iki tetiklemeden birini atlamıyor.** Yazılımla tetikleme modundaki bir kamera, artık okuma işlemi sırasında gelen bir tetiklemeyi kuyruğa alıyor (veya Chloros, kameranın hazır olmasını bekliyor), tıpkı kablolu senkronize kameraların zaten yaptığı gibi.
> - **`daq pool-discover`, DAQ-E-S'yi `DAQ-E-S`** sütununda bir DAQ-E-S'ye ad verir (ve `MODEL`'in `--json`'i). Her Ethernet sensörü için `DAQ-E` yazıyordu; yaklaşık 2 saniye içinde kimlik kontrolüne yanıt vermeyen sensörler, `DAQ-E` olarak listede yer almaya devam ediyor.
> - **Geliştirilmiş LATTICE indeks ürünleri** (NDVI ve diğer indeksler), her işleme modunda `process`'ten itibaren.
> - **OCN kameraları: güneş ışığı altında parlaklık, yansıma ve indeks ürünleri iyileştirildi.** Bant ayrıştırma işleminden sonra bir filo düzeltmesi uygulanır; RGN kameralarında değişiklik yoktur ve OCN değerleri 1.2.0 sürümünden farklıdır. Bu, daha önceki bir sürümle çekilmiş bir OCN görüntüsü için de geçerlidir; ancak görüntünün çekildiği kalibrasyonun bulunduğu bir bilgisayarda işlendiği durumlarda (Kamera bağlandıktan sonra Chloros, kameranın kalibrasyonunu saklar).
> - **`lattice continuous`, ham kareyi kaydeder.** Bir `tiff` çalıştırması, demosaic işlemi uygulanmış 3-düzlemli çerçeveler yazdı; `process` ise bunlardan başlayamaz. Bu dosyaların çerçeveleri artık bir dizi yakalamanın yazdığı `raw` dosyasındadır: sensörün kendi değerlerinde, sıkıştırılmamış ve kalibrasyon paketi içeren bir mozaik düzlem. Bu dosyalardan üç düzlemi okuyan bir komut dosyası, sadece birini okur.
>
> - **Her yakalama işlemi, GUI'nin yaptığı gibi, kullandığı DAQ okumasını kaydeder.** `lattice capture`, `lattice continuous` (her kare için bir adet `.daq`), `array-capture` ve `multi-capture`, bağlı bir dizi (ve SDK) ile `.daq` ve `.match.json`'i yazar; 1.2 sürümünde ise0 sürümünde, ham veya debayered yakalama yalnızca `--force-daq` ile kaydedilir. `--no-daq` bunu devre dışı bırakır, `--daq-tolerance-ms MS` o yakalama için eşleşme toleransını ayarlar ve yeni `daq pool-bind`, bir kamerayı veya diziyi CLI üzerinden bir DAQ'ya bağlar. `.daq` ile kaydedilen bir yakalama, `process`'te yalnızca adı belirtilen kameralarla eşleştirilir. Bir yakalama artık DAQ’nın bir sonraki okumasını bekler; böylece pozlamanın her iki tarafındaki okumaları birleştirebilir. Bkz. [Bir yakalamanın kaydettiği DAQ okuması](#the-daq-reading-a-capture-saves) ve [Pozlamadan Uzak Bir DAQ Okuması](#a-daq-reading-far-from-the-exposure).
> - **`process --indices NAME`**, her kameranın bantları için projeye bir dizin ekler; yeni bir proje de dahil edilmiştir. 1.2.0 sürümünde, projenin henüz listelemediği bir ad, `Unknown index name(s) ignored` ile göz ardı ediliyordu.
> - **Canlı bir dizinin yanındaki**`process`**artık kameraları veya DAQ'ları barındıran bir arka ucu yeniden başlatmaz ve sona ermekte olan bir çalıştırma için en fazla 15 saniye bekler.**Ctrl+C**, yalnızca CLI'yi değil, arka uçtaki çalıştırmayı da durdurur. Bkz. [`process` Notları](#notes).
> - **Windows'un 260 karakterlik yol sınırını aşan çok derin bir proje klasörü**, hiçbir şey yazmayan bir çalıştırma yerine, sığabilecek en uzun klasörle önceden reddedilir. Windows yükleyicisi artık "Windows uzun yollarını etkinleştir" seçeneği ve arka uç için bir Microsoft Defender hariç tutma seçeneği sunar; her ikisi de varsayılan olarak işaretlidir. Bkz. [Windows (.exe)](#windows-exe).
> - **`lattice array-capture --continuous`**, arka ucun kendi sürekli yakalama işlevini çalıştırır: her 2 saniyede bir ilerleme satırı, dosya başına liste yerine bir özet ve yazılmamış herhangi bir dosya veya dolu bir sürücü durumunda sıfırdan farklı bir çıkış kodu. Bkz. [`array-capture` modları](#array-capture-modes).
> - **`array-connect`'ten sonraki ilk `array-capture`**, HTTP 503 "Dizi hala ısınma aşamasında" hatasıyla sonlanmak yerine, dizinin ilk kare grubunu en fazla 20 saniye bekler.
> - **ROI altındaki dizi pozlaması:** Küçük bir ROI artık bir dizi elemanının pozlamasını sensörün serbest çalışma süresi (768 × 304'te 4,9 ms) ile sınırlamaz. Bkz. [Bir dizi elemanında pozlama](#exposure-on-an-array-member).
> - **Otomatik pozlama artık bir dizinin otomatik kare hızını düşürmez.** Kablo ile senkronize edilmiş bir dizi, ağının ve kameraların okuma hızının izin verdiği hızda çalışır; otomatik pozlama, bu hızın sağladığı süreyi doldurur, ardından kazancı artırır. Süreden daha uzun bir manuel pozlama (`lattice exposure --set`, `configure --set`), reddedilmek yerine buna uyum sağlamak için tetiklemeyi yavaşlatır ve üye otomatik moda döndüğünde hız eski haline gelir. Bkz. [Dizi üyesinde pozlama](#exposure-on-an-array-member).
> - **Diziler daha az eksik kare grubu** sunar ve daha az 503 hatası (“Yeni senkronize kare grubu yok”) yakalar: her bir bağımlı kameranın tetik girişi artık parazitlerden arındırılmıştır.
> - **Sıkıştırılmış 12 bit aktarım** (`--packed`, varsayılan `auto`), tam sensörlü dizi kare hızlarını yaklaşık üçte bir oranında artırır. `array-connect --packet-size` artık uygulanmaktadır (1.2.0 sürümü bunu kabul etmiş ancak göz ardı etmişti). Bkz. [Sıkıştırılmış 12 bit aktarım](#packed-12-bit-transport---packed).
> - **Kameralarının güç döngüsü sonrasında kendiliğinden kurtulan bir dizi.** Anahtarın güç döngüsünden sonra, `array-connect`'in dizisi-kameralar yanıt verdiğinde kameraları yeniden açar ve PTP yeniden etkinleştirildiğinde akışa devam eder; 1.2.0 sürümünde bu işlev tamamen durmuştu ve yeni bir `array-connect` gerekiyordu. `array-status`, bir kamera PTP'yi okurken `ptp_enabled: False` (ve `ptp_off:`) rapor eder; oysa eskiden dizinin bağlı olduğu ayarı rapor ederdi. Bkz. [Kalıcı Bağlantılar](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **`lattice array-record`, GUI açık olmadan kayıt yapar.** GUI'nin birleştirilmiş görünümü kapalıyken, arka uç görünümü kendisi oluşturur: dizininNDVI'si veya yeni `--expr` ile ilettiğiniz indeks. Artık hiçbir kare yazmayan bir kayıt başarısız olur (`no-frames: …`, çıkış `1`). Önceden, yalnızca GUI'nin birleştirilmiş görünümü açıkken kayıt yapıyordu, ve hiçbir şey kaydetmeyen bir kayıt yine de `0`'ten çıkıyordu. Bkz. [`array-record`](#array-record--combined-index-videogif-monitoring-grade).
> - **Chloros'un halihazırda elinde tuttuğu bir kamera ikinci kez açılmaz.** Kamerayı açmaya yönelik bir komut, bunun yerine HTTP 409 `CAMERA_HELD` yanıtını verir ve kamerayı elinde tutan öğenin adını ve kamerayı serbest bırakan komutu belirtir. Buna, başka bir komutun şu anda kullandığı bir kamera da dahildir (kameralarını açan bir `multi-test` veya `multi-capture`, bir `lattice capture`): ilk komut tamamlanana kadar ikinci komut reddedilir. Kamerayı tekrar açmak, tutucunun akışını durdurabilir veya arka ucu çökertebilir. Bkz. [Kalıcı Bağlantılar](#persistent-connections-smart-prep-gui-equivalent-flow).
> - **Geçici çoklu kamera komutları, tüm kameraları iletir veya başarısız olur.** Dizi bağlı olmayan `lattice multi-capture`, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream` ve `index --live`, kare göndermeyen bir kameranın adını belirterek `1`'ten çıkar. Akışlar, bir kameranın eksik olduğu kare grubunu atlar ve arka arkaya üç kez tekrarladıktan sonra durur; `multi-test`, her tetikleme için bir satır yazdırır; `(no frame)`, bir kameranın kaçırdığı yeri gösterir; `align-stream` ayrıca bir iş hatası durumunda `1`'ten bir iş hatası nedeniyle çıkar. Her çalıştırma, ardından her kamerayı bulduğu gibi geri getirir: tetikleyici ve senkronizasyon hattı kablolaması, PTP (`PtpEnable`) ve çalıştırma sırasında değiştirilen tüm kamera ayarları — pozlama ve kazanç, AE hedefi, sınırlar ve sönümleme, beyaz dengesi, siyah seviyesi, piksel formatı, birleştirme ve ROI, çevirmeler, kare hızı — `--keep-settings` / `--clear-settings` komutları hariç (bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings)). Daha önce, bu komutlar gelen tüm kareleri saklıyordu ve `0`'ten çıkıyordu (`align-stream`, `Note:` olarak bir iş hatası mesajı veriyordu), ve her kamerayı `TriggerMode` `On` içinde bırakırdı. Ayrıca, bir kareyi kaydetmeden önce (en fazla birkaç saniye) her kameranın pozlamasını, bağlı bir dizinin her kamerayı başlattığı hedef değerlere göre ayarlardı: 40 multispektral, 60 RGB (`--brightness N` ayarlar) olarak ayarladı ve çekim süresince bu ayarları korudu; Chloros'un elinde olmayan tek bir kameranın `lattice capture` komutu da aynı şekilde çalışır. Önceden, her kameranın ilk karesi, ışık koşulları ne olursa olsun, kameranın kendi pozlama sınırında (yaklaşık 100 ms) çekilirdi ve sıklıkla kırpılırdı. Bkz. [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions).
> - **Geçici çoklu kamera komutları, `array-connect`'in yaptığı gibi ağı paylaşır ve yalnızca `--serials`'i açar.** Her kamera, ana bağlantıdaki payına göre hızlandırılır; bu sayede dört kamera 1 Gbps bağlantıda çalışır; önceden ise dört kamera aralarında toplam 125 MB/s talep ederdi (tamamı 1 GbE üzerinden) ve her karede paket kaybı yaşanırdı. Ağın taşıyamayacağı bir dizi, herhangi bir kamera akışı başlamadan önce reddedilir. `--serials` artık kameraları seçer; ilk tetikleyiciyi ateşleyen kamera belirlenir; önceden ise, `--serials` ne derse desin, her komut ağdaki tüm kameraları açardı. GVSP paket kaybı olan bir arıza, artık M8 senkronizasyon kablosunu işaret etmek yerine bunu açıkça belirtir. Bkz. [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions).
> - **`lattice array-disconnect`, kameraları bulunduğu gibi serbest çalışır durumda bırakır.** `TriggerMode`, `Off` ve bağlantı sırasında tespit edilen diğer tüm unsurlar — tetikleyici ve senkronizasyon hattı ayarları (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`), PTP ve oturum sırasında değiştirilen kamera ayarları — böylece eski bir üyeye tek bireski bir üyeye verilen tek bir kamera komutu, kareleri tek seferde alır ve ana kamera artık `TriggerSource`, `Software` üzerinde kalmaz. `--keep-settings`, oturumun ayarlarını her kameranın belleğine kaydeder, `--clear-settings` ise bu belleği sıfırlar (bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings)). `lattice continuous` ayrıca, etkin durumda bulduğu bir kamerayı koşu süresince serbest moduna ayarlar (ve koşu bittikten sonra eski durumuna geri getirir); çerçeve içermeyen bir koşu ise `1` ile sonlanır.
> - **`lattice configure --get` / `lattice exposure`**, otomatik mod çalışırken kameranınotomatik modu çalışırken kameranın gerçek pozlama veya kazanç değerini yazdırır. Reddedilen bir `lattice exposure --set` / `configure --set`, kameranın kabul ettiği aralığı belirtir ve otomatik pozlamayı kapalı bırakmak yerine otomatik modu geri yükler. Bkz. [Sensör Ayarı](#sensor-tuning).
> - **Yeni kamera kontrolleri:**`lattice highlights` (RGB ekran görüntüsünde parlak beyaz yüzeyleri aşağıya çek) ve `lattice exposure --brightness-auto`.**`lattice multi-detect`**, tek bir tarama turunda kablolu kameraları bulur ve ana kamerayı varsayılan olarak RGB kameraya ayarlar.
> - **Yeni DAQ komutları:** `daq pool-bind`; `pool-enable-ptp`; ve DAQ-E'nin kendi ayarları için `pool-info`, `pool-set-network`, `pool-set-auto-stream`, `pool-reboot` ve `pool-update-firmware`. Bkz. [DAQ-E Aygıt Ayarları](#daq-e-device-settings).
> - **Veri aktarımını durduran bir DAQ-E, arka uç tarafından yeniden bağlanır.** Havuzlanmış bir DAQ-E'nin akışı kesildiğinde (güç kaynağı olan anahtarın yeniden başlatılması, kamera bağlantısının kesilmesi), arka uç onu önceki ayarlarıyla yeniden bağlar ve bağlantının kesik olduğu süre boyunca artan aralıklarla yeniden deneme yapar. Cihazdaki kayıt, dosyada boşluk işaretlenmiş olarak aynı `.daq` dosyasında devam eder, ve `pool-record --stop` dosyası her bir kesintiyi listeler. 1.2.0 sürümünde sensör, `pool-disconnect` + `pool-connect` dosyalarına kadar sessiz kalırdı ve bu da kaydın sonlanmasına neden olurdu. Bkz. [Verim Vermeyi Durduran Bir DAQ-E](#a-daq-e-that-stops-delivering).
> - **`time-sync status`**, PTP grandmaster'ın paketlerinin ağa ulaşıp ulaşmadığını bildirir (`health`); ulaşmadığında ise stderr'a bir `WARNING:` yazılır. Bkz. [`time-sync`](#chloros-cli-time-sync).
> - **Linux .deb:** Kamera akışlarının ihtiyaç duyduğu çekirdek soket alım tamponlarını artırır (çekirdek varsayılanında, diziler kayıp kareleri ve kare hızını içerir), kullanıcı kimliğinizle çalışan bir arka uç hizmeti ekler ve `lattice network` tamponları kontrol eder. CLI, bir hizmetin çalıştırdığı arka uç hizmetini asla durdurmaz ve bir güncelleme, etkin arka uç hizmetlerini yeniden başlatır. Bkz. [Arka ucu hizmet olarak çalıştırma](#running-the-backend-as-a-service-headless) ve [Linux ana bilgisayarlar — soket alım tamponları](#linux-hosts--socket-receive-buffers) bölümlerine bakın.
> - **`lattice array-capture`, hizalama yapamadığı durumlarda şunları bildirir:**bir yakalamanın hizalanmamış olarak kaydedilmesinin her nedeni (hizalama profili yok, farklı çözünürlükteki üyeler, kabloyla senkronize edilmemiş bir dizi, profilin eşleştirilemediği bir çerçeve) bir `Alignment:` satırı olarak yazdırılır.**`chloros-cli daq help`**, `daq --help`'tir (`2`'ten çıkmıştır) ve Linux'ta yardım bölümünde, o derlemenin içerdiği doğrudan donanım `daq` komutları listelenir.
> - **Jetson:** JetPack 6 için paket `chloros_1.3.0_arm64_jp6.deb`'tir ve GPU işleme, JetPack'in CUDA PyTorch'u ile Orin üzerinde çalışır. Bkz. [Linux arm64 — Jetson](#linux-arm64--jetson-jetpack-6) bölümüne bakın.

---

## Kurallar

- Tüm komutların başında `chloros-cli` önekleri bulunur. Windows'ta ikili dosya `chloros-cli.exe`; Linux/Jetson'da ise `chloros-cli` şeklindedir.
- İsteğe bağlı argümanlar `--flag` şeklinde gösterilir. Zorunlu konumsal argümanlar köşeli parantezler olmadan gösterilir.
- Varsayılan bir değer belirtilmişse, bayrağın atlanması durumunda bu değer kullanılır.
- CLI, Chloros arka ucunda çalışan hafif bir HTTP istemcisidir (masaüstü uygulamasının da kullandığı `127.0.0.1:5000` üzerindeki sunucu). **Hangi komutlar bunu başlatır:**
  - Temel komutlar — `process`, `login`, `logout`, `status`, `export-status`, `time-sync` ve `selftest` — çalışan bir arka uç kullanır; çalışan bir arka uç yoksa bir tane başlatır ve işleri bittiğinde tekrar durdurur.
  - **`lattice`, `project` ve `daq` komutları hiçbir zaman bir arka uç başlatmaz.** Bu komutlar için halihazırda çalışmakta olan bir arka uç gereklidir: Windows masaüstü uygulamasının arka ucu, Linux [arka uç hizmeti](#running-the-backend-as-a-service-headless) veya sizin başlattığınız bir arka uç (bkz. [Arka Ucu Başlatma](#start-the-backend)). Arka uç olmadan, `Error: Chloros backend not reachable at http://127.0.0.1:5000` mesajını, onu başlatan komutla birlikte yazdırır ve `1` ile sonlanır.
- `CHLOROS_BACKEND_URL=<url>`, **`lattice`**,**`project`**ve**`daq pool-*`** komut ailelerini başka bir arka uca yönlendirir (`lattice network-analysis` ise bunun yerine `--backend-url`'i alır). Çekirdek komutlar, `http://127.0.0.1:<port>`'i kasıtlı olarak sabitler ve onu yok sayar (IPv4 sabit değeri, Windows'ta `localhost`→`::1`'in istek başına yaklaşık 2 saniyelik gecikme sorununu önler). Bkz. [Ortam Değişkenleri](#environment-variables).
- **Göreceli yollar, `chloros-cli`'i çalıştırdığınız klasöre göre çözümlenir.** Arka uç, çıktı ve girdi klasörlerini açan işlemdir; bu nedenle, bir yol gönderilmeden önce CLI, yolu mutlak hale getirir: `-o` / `--output` (varsayılanı olan `output` dahil), `--input`, `--burst-dir`, `--profile`, `--save` ve `--mask`, bir [tarif](#recipe-dsl) adımının `output` (varsayılan `output`)ve `lattice index --live`'in varsayılan `output` klasörü. Dolayısıyla `-o captures/`, `<current folder>/captures/`'e yazar ve yazdırılan yollar mutlak yollardır. `~` genişletilir. Uzak bir arka uçta (başka bir makineyi belirten `CHLOROS_BACKEND_URL`), yollar yazıldığı gibi gönderilir ve o makinede çözümlenir.
- Tüm SDK/CLI çağrıları için bir Chloros+ hesabı ile oturum açılması gerekir (her makine için bir kez `chloros-cli login` komutunu çalıştırın; `~/.chloros/`'te önbelleğe alınır).
- Örneklerde Linux yolları kullanılmaktadır; Windows'ta `/home/user/...`'i `C:/Users/.../...` ile değiştirin. Bir DAQ-U'seri bağlantı noktası, Windows'ta `COM3` biçimindeyken, Linux'ta `/dev/ttyUSB0` / `/dev/ttyACM0` biçimindedir.
- Özetlerde, birbirini dışlayan bayraklar `{--a | --b}` olarak, isteğe bağlı bayraklar ise `[...]` olarak yazılır.

---

## Üst Düzey Özet

```
chloros-cli [global options] COMMAND [command options]
```

### Genel Seçenekler

| Bayrak | Açıklama |
| --- | --- |
| `--backend-exe PATH` | Otomatik olarak algılanan arka uç yürütülebilir dosyasını geçersiz kılar. |
| `--port N` | Arka uç HTTP bağlantı noktası (varsayılan: `5000`). Yalnızca çekirdek komutlar tarafından kullanılır (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`); `lattice`, `project` ve `daq`, `CHLOROS_BACKEND_URL`'i kullanır (varsayılan `http://127.0.0.1:5000`). |
| `-v, --verbose` | Ayrıntılı çıktıyı etkinleştirir. |
| `--restart` | Arka ucu zorla yeniden başlatır (çalışmakta olan tüm `backend_server.py`'leri sonlandırır). Bir systemd hizmeti tarafından çalıştırılan bir arka uç (Linux .deb) tarafından çalıştırılan bir arka uç asla durdurulmaz: komut bunun yerine `sudo systemctl restart <unit>` mesajını yazdırır ve `1`'ten çıkar. |
| `--version` | Sürümü gösterir (`Chloros CLI 1.3.0`). |
| `--help` | Üst düzey yardım gösterir. |

### Komut Dizini

| Komut | Amaç |
| --- | --- |
| [`process`](#chloros-cli-process) | Bir klasördeki Survey3 veya LATTICE yakalamalarını uçtan uca işler. |
| [`login`](#chloros-cli-login) | Bu makineyi bir Chloros+ hesabıyla kimlik doğrulaması yapın. |
| [`logout`](#chloros-cli-logout) | Önbelleğe alınmış kimlik bilgilerini temizleyin. |
| [`status`](#chloros-cli-status) | Mevcut lisans / kimlik doğrulama durumunu gösterir. |
| [`export-status`](#chloros-cli-export-status) | `process` çalışması sırasında Thread-4 dışa aktarımının gerçek zamanlı ilerleme durumu. |
| [`language`](#chloros-cli-language) | CLI'nin görüntüleme dilini ayarla veya listele (38 dil desteklenir). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Varsayılan proje klasörü (GUI ile paylaşılır). |
| [`update`](#chloros-cli-update) | Chloros güncellemelerini kontrol et ve yükle (Linux/Jetson). |
| [`selftest`](#chloros-cli-selftest) | Sistem tanılama + işlevsellik testleri. |
| [`time-sync`](#chloros-cli-time-sync) | PTP grandmaster durumu / kontrolü. |
| [`lattice`](#chloros-cli-lattice) | LATTICE kamera kontrolü ve görüntü yakalama (45'ten fazla alt komut). |
| [`daq`](#chloros-cli-daq) | DAQ spektral sensör kontrolü (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Kaydedilmiş bir Chloros projesini (kameralar + DAQ'lar) açma ve çalıştırma. |

---

## Kurulum

`chloros-cli`, desteklenen her platformda Chloros masaüstü yükleyicisi içinde bulunur — ayrı bir CLI indirme dosyası yoktur. Platform paketini yüklediğinizde, `chloros-cli`, masaüstü uygulaması ve onu çalıştıran arka uç ikili dosyası ile birlikte `PATH`'e eklenir.

En son indirmeler: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Yükleyici ayrıca, kullanıma hazır bir CLI kabuğu açan kullanışlı başlatıcı komut dosyaları (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) içerir; bu komut dosyaları, kullanıma hazır bir CLI kabuğunu açar.

### Windows (.exe)

1. İndirme sayfasından Windows yükleyicisini indirin.
2. `Chloros Installer 1.3.0.exe` dosyasını çalıştırın ve sihirbazı izleyin. Varsayılan kurulum yolu `C:\Program Files\Chloros\`'tir (CLI, kurulum programının PATH değişkenine eklediği `C:\Program Files\Chloros\cli\` dizinine yerleştirilir).
3. Güncellenen `PATH`'in algılanması için yeni bir terminal (`cmd.exe`, PowerShell veya Windows Terminali) açın.

```powershell
chloros-cli --version
```

Yükleyici, `chloros-cli.exe` dosyasını sisteminizin `PATH` dizinine otomatik olarak ekler ve LATTICE kamera sürücülerini bir paket halinde sunar. Arka uç, `C:\Program Files\Chloros\backend\chloros-backend.exe`'tir (kurulum klasörünüzdeki `backend` klasörü); bu, `PATH`'te bulunmaz.

**Sistem ayarları.** Yükleyicinin *Ek Görevleri Seç* sayfasında **Sistem ayarları**altında iki seçenek bulunur ve her ikisi de**varsayılan olarak işaretlidir**:

| Seçenek (yükleyicideki ifadesiyle) | Ne işe yarar | Sessiz yüklemeyi devre dışı bırak |
| --- | --- | --- |
| **Windows uzun yollarını etkinleştir** (proje klasörlerinin 260 karakterden daha derinde bulunmasına izin verir; önerilir) | Windows'un sistem genelindeki `LongPathsEnabled` ayarını 1 olarak belirler. Windows bu ayar kapalı olarak gelir, bu durumda hiçbir yol 260 karakteri aşamaz; Chloros ürünleri proje klasörünün yaklaşık 130 karakter altında yer alır, bu nedenle daha derin bir proje klasörü reddedilir (bkz. [Sorun Giderme](#sorun-giderme-ipuçları)). Yüklemeden sonra başlatılan programlar için geçerli olur. Diğer programlar bu ayara bağlı olabileceğinden, program kaldırıldığında açık bırakılmalıdır. | `/MERGETASKS="!longpaths"` |
| **Chloros arka ucunu Microsoft Defender taramasından hariç tut**(çok daha hızlı kamera yakalama; önerilir) | Yükleme klasöründeki `chloros-backend.exe` için bir Defender**işlemi** hariç tutma ekler; böylece gerçek zamanlı koruma, arka ucun yazdığı her dosyayı, nereye yazarsa yazsın taramaz. Başka hiçbir program hariç tutulmaz; kaldırma işlemi sırasında hariç tutma kaldırılır. Defender'ın bulunmadığı veya ilke nedeniyle kilitli olduğu durumlarda bu adım sessizce başarısız olur ve kurulum devam eder. | `/MERGETASKS="!defenderexclusion"` |

**Defender seçeneği neden önemlidir:** Yüksek yakalama hızlarında, yakalama dosyalarının gerçek zamanlı taranması, yakalama hızının yaklaşık %30’una mal olur (9 kameralı sürekli yakalama üzerinde ölçülmüştür: 77,7 yakalama/sn hariç tutulur, 52,7'si taranır). `lattice array-capture --continuous`, Defender'ın yakalama sırasında bir veya daha fazla CPU çekirdeği kullandığını tespit ettiğinde bir not görüntüler; seçeneği işaretleyerek yükleyiciyi yeniden çalıştırın.

Bir sessiz kurulum (`/SILENT` veya `/VERYSILENT`), örneğin `"Chloros Installer 1.3.0.exe" /VERYSILENT /MERGETASKS="!defenderexclusion"` gibi bir seçenekle devre dışı bırakılmadıkça her ikisini de uygular.

**Windows Güvenlik Duvarı.** Yükleyici, her ağ profilinde `chloros-backend.exe` için her zaman iki gelen kural ekler ve kaldırıcı bunları siler: kamera akışları, PTP, mDNS ve DAQ-E veri akışı için; `Chloros Backend (TCP-In)` ise, sensörün görüntüyü toplamak için bu bilgisayara yeniden bağlandığı DAQ-E ürün yazılımı güncellemesi için yerel alt ağla sınırlıdır (bkz. [DAQ-E Aygıt Ayarları](#daq-e-device-settings)). Arka ucun HTTP API'si `127.0.0.1`'e bağlı kalır.

### Linux amd64 (.deb)

Ubuntu 22.04 LTS veya daha yeni sürümler / Debian tabanlı x86_64 iş istasyonları için.

> **Ubuntu 20.04 desteklenmemektedir.** Paketin bağımlılık listesi,
> arka ucun gerçekte hangi kütüphanelere bağlandığından türetilmiştir ve buna `libc6 (>= 2.35)` da dahildir;
> focal, glibc 2.31 sürümünü içerir. `apt`, yüklemenin çalışma zamanında başarısız olmasına izin vermek yerine
> yüklemeyi reddeder. Listede ayrıca, pakete dahil OpenCV tarafından yüklenen ve minimal bir
> sunucu görüntüsünde bulunmayabilecek olan `libgl1` da yer almaktadır; `apt install` bunu sağlar.

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
```

.deb dosyası şunları yükler:
- `chloros-cli`'ten `/usr/bin/chloros-cli`'e ve `chloros-backend`'e (arka ucu) dosyasını `/usr/bin/chloros-backend` dosyasına
- Derlenmiş arka uç ve CLI, açılmış halde, `/usr/lib/chloros/backend/` ve `/usr/lib/chloros/cli/` dosyalarına
- LATTICE kamera sürücüleri
- Gürültü giderici modelleri, kalibrasyon paketleri ve güncelleme kanalı yapılandırması
- Sistem için Python SDK'si `python3` (`import chloros_sdk`). `apt install` ayrıca kendi gereksinimlerini de içerir: `python3-numpy` ve `python3-requests` (önerilir, zorunlu değildir: yukarıdaki programlar bağımsızdır; `dpkg -i` bunları atlar ve kurulum çıktısı eksik olanları belirtir). Bir virtualenv ortamında: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`
- `/etc/sysctl.d/60-chloros-gige.conf`, bu paket kameranın akışları için çekirdeğin soket alım tamponlarını 32 MB'a yükseltir ve bunları tek seferde uygular (zaten daha yüksek ayarlanmış bir değer varsa değiştirilmez). Paketin kaldırılması, dosyanın da kaldırılmasına neden olur. Bkz. [Linux ana bilgisayarlar — soket alım tamponları](#linux-hosts--socket-receive-buffers).
- Arka uç için iki isteğe bağlı systemd hizmeti; ikisi de etkinleştirilmemiştir (sonraki bölüm)

#### Arka ucu bir hizmet olarak çalıştırma (başsız)

Bir hizmet olmadan, arka uç (ve bununla birlikte PTP grandmaster) yalnızca bir şey tarafından başlatıldığında çalışır: Terminalde bir ``chloros-backend`` komutu, bir SDK betiği veya bir ``chloros-cli`` komutu, çalışması süresince arka ucu çalıştırır. Başsız bir makinede her zaman açık bir arka uç için, hizmeti **kendi kullanıcı adınızla** etkinleştirin:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service`, arka ucu o kullanıcı olarak çalıştırır. Yazdığı yakalamalar o kullanıcıya aittir, ayrıntılı günlüğü o kullanıcının `~/.cache/chloros/logs` dosyasında bulunur, ve o kullanıcının `chloros-cli login` (`~/.chloros`) dosyasını kullanır; bu nedenle önce o kullanıcı olarak bir kez oturum açın. PTP için root erişimi gerekmez. Yüklü her arka uç gibi, yalnızca `127.0.0.1:5000` adresinde dinleme yapar.

`chloros-backend.service`, arka ucu **root** olarak çalıştırır. Bu, özelliği etkinleştiren bir makinenin çalışmaya devam etmesi için hala yüklü durumdadır; ancak root arka ucunun yazdığı her şey root'a aittir, bu nedenle bir kullanıcı kendi yakalamalarını silemez. Kendi kullanıcınıza geçmek için:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

Root hizmetinin ayrıntılı günlüğü `/var/log/chloros` dosyasında bulunur ve bu dosya, `adm` grubunun üyeleri tarafından okunabilir (hizmet, `CHLOROS_LOG_DIR` değerini ayarlar; bkz. [Ortam Değişkenleri](#environment-variables)). Paketi yüklediğinizde, kullanıcı adınızın girilmiş olduğu `enable` komutu ve root hizmeti etkinleştirildiğinde geçiş komutu da görüntülenir.

**Bir arka uç birimini etkinleştirin.** Aynı anda yalnızca bir arka uç çalışır. Etkinleştirilmiş ikinci bir birim (`chloros-backend@<user>.service`'in yanı sıra `chloros-backend.service` veya iki kullanıcının `@` birimleri) başlamayı reddeder ve çıkış durumu 98 olarak `failed` durumunda kalır; systemd bunu yeniden başlatmaz. `systemctl status` dosyası nedenini belirtir: `Another instance is already running. Exiting.`, ardından arka ucu tutan şey (5000 numaralı bağlantı noktası veya kilit dosyası ve ona ait kullanıcı), ardından `systemd will not restart <unit> (exit status 98)`. Yeniden başlatmanın ardından, her iki birimden herhangi biri çalışabilir. Değiştirmek için, yukarıdaki gibi kullanmadığınız birimi devre dışı bırakın.**CLI, bir hizmetin arka ucunu asla durdurmaz.** `chloros-cli`, GUI ve SDK'nin yaptığı gibi onu olduğu gibi kullanır: `process` bunun üzerinde çalışır ve son projenin yerine kendi projesini yükler. CLI'nin normalde arka ucu yeniden başlatacağı durumlarda (`--restart` veya içe aktarma işlemi hiçbir şey bulamayan bir `process`), birim adını belirtir, `sudo systemctl restart <unit>` yazdırır ve `1`'ten çıkar, çünkü bu şekilde durdurulan bir arka uç çalışmaz halde kalır. `GET /api/status`, birimi `service` olarak adlandırır (hiçbir hizmetin çalışmadığı bir arka uç için `null`).**Yükseltme ve kaldırma.** Paketin yükseltilmesi her arka uç hizmetini durdurur, ardından etkinleştirilmiş olan her birini yeniden başlatır; önce `@<user>` birimleri (elle başlatılmış ancak hiçbir zaman etkinleştirilmemiş olanlar, yeniden başlatma sonrası olduğu gibi durdurulmuş halde kalır). Birden fazla hizmet etkinleştirilmişse, yalnızca biri çalışır ve diğerleri yukarıda belirtildiği gibi hatalı durumda kalır; yükseltme işlemi sırasında `Chloros: N backend units are enabled: …` ve `Keep one enabled: sudo systemctl disable --now <unit>` mesajları görüntülenir. Paketin kaldırılması, bu hizmetleri durdurur ve devre dışı bırakır. Temizleme işlemi, `/var/log/chloros`'i de siler.

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

amd64 .deb dosyasıyla aynı yapıya sahiptir; **JetPack 6** (L4T R36) üzerinde çalışan Jetson Orin / Orin NX / Orin Nano için alıcı tamponları ve [arka uç hizmetleri](#running-the-backend-as-a-service-headless) dahildir. PyTorch, JetPack 6’nın Orin için CUDA derlemesidir; bu nedenle, doku duyarlı debayer dahil olmak üzere GPU işleme, Jetson’un GPU’su üzerinde çalışır. Paket, CUDA'yı kendisi içermez: JetPack 6'nın yüklediği CUDA ve cuDNN kütüphanelerini kullanır (bunların bulunmadığı bir görüntüde `sudo apt install nvidia-jetpack`). CUDA GPU'sunun kullanılamadığı durumlarda, işleme CPU üzerinde gerçekleştirilir ve doku farkındalığı özelliği standart debayer'ı kullanır (bkz. [`process`](#chloros-cli-process)).

### Her Makine İçin Tek Seferlik Kimlik Doğrulama

SDK/CLI çağrılarının çalışabilmesi için her platformda Chloros+ ile tek seferlik bir oturum açma işlemi gerekir:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Kimlik bilgileri `~/.chloros/user_session.json`'te önbelleğe alınır.

### Arka Ucu Başlatma

`lattice`, `project` ve `daq` komutları, halihazırda çalışmakta olan bir arka uçla iletişim kurar; asla yeni bir arka uç başlatmazlar (bkz. [Kural ve Kurallar](#kural)). Bu komutları kullanırken arka uçtan birini çalışır durumda tutun:

| Platform | Nasıl |
| --- | --- |
| Windows | Chloros masaüstü uygulamasını açın (bu uygulamanın arka ucu CLI’ye de hizmet verir) veya `"C:\Program Files\Chloros\backend\chloros-backend.exe"` komutunu ayrı bir terminalde çalıştırın (kurulum klasörünüzdeki `backend` klasörü) ve açık bırakın. |
| Linux / Jetson | `chloros-backend` komutunu ayrı bir terminalde çalıştırın veya [arka uç hizmeti](#running-the-backend-as-a-service-headless) ile bir tanesini her zaman çalışır durumda tutun. |

Aynı anda yalnızca bir arka uç çalışır; tüm istemciler (GUI, CLI, SDK komut dosyaları) bunu paylaşır. Temel komutlar (`process`, `login`, `status`, …) de bunu kullanır ve çalışmakta olan bir arka uç yoksa bir tane başlatır.

**Bir arka uç, komutlar arasında çalışmaya devam eder**; böylece bir sonraki komut, arka ucun yeniden başlatılmasını gerektirmez. Oturumunuzu kapattığınızda (Chloros uygulaması açık değilse veya bir çalıştırma ya da çevrimdışı derleme devam etmiyorsa, `chloros-cli logout`), `chloros-cli backend stop` komutunu çalıştırdığınızda veya 30 dakika boyunca onu kullanan hiçbir şey olmadığında kendiliğinden durur: komut, komut dosyası veya uygulama yok, çalıştırma veya çevrimdışı derleme yok, bağlı kamera veya ışık sensörü yok. `backend stop`, bir çalıştırma veya çevrimdışı derleme devam ederken bunu reddeder ve durumunu bildirir; `--force` onu hemen durdurur ve o çalıştırma veya derleme kaybolur (bir seri çekim saklanır ve `array-build-video` bunu yeniden derler). Arka uç başlamadan önce `CHLOROS_BACKEND_IDLE_MIN`'i ayarlayarak 30 dakikalık süreyi değiştirin (`0`: asla); [arka uç hizmeti](#running-the-backend-as-a-service-headless) asla kendi kendine durmaz. `chloros-cli backend status`, bir arka ucun çalışıp çalışmadığını, onu neyin kullandığını ve çalışmakta olan herhangi bir çevrimdışı derleme olup olmadığını gösterir. `process`, Chloros uygulaması bir projeyle açık değilse açılmışsa: bu durumda işlemi reddeder ve önce uygulamadan işlem yapmanız veya o projeyi kapatmanız gerekir. Daha önceki bir `process` veya bir SDK komut dosyası tarafından açık bırakılan bir proje, basitçe değiştirilir.

### Kurulumu Doğrulama

```bash
chloros-cli --version           # prints "Chloros CLI 1.3.0"
chloros-cli selftest            # 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

`selftest`, yalnızca yedi kontrolün tamamı başarılı olduğunda `0`'ten çıkar. Son kontrol için bir CUDA GPU (bir NVIDIA GPU, veya bir Jetson Orin) ve gürültü giderici modeller gerektirir; bu nedenle, bunlardan birine sahip olmayan bir bilgisayarda `Failed: CUDA + Denoiser` mesajı görüntülenir ve program `1` ile sonlandırılır; burada, yalnızca GPU'ya özgü doku duyarlı debayer hariç her şey çalışmaya devam eder.

> **Chloros+ aboneliği gereklidir.**CLI için aktif bir Chloros+ planı gereklidir.**Copper**, Chloros+’ın giriş seviyesidir — ücretli tüm Chloros+ seviyeleri CLI/SDK erişimine sahiptir; yalnızca ücretsiz**Iron** seviyesi erişim sağlamaz. (Plan-kimlik eşlemesi: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing) adresinden yükseltme yapın.
>
> Bu alt sınır sadece CLI tarafından değil, arka uç tarafından da uygulanır: Ücretli bir planı olmayan, SDK ile işaretlenmiş bir istek, ister `chloros-cli`'ten, ister Python SDK'den, ister elle yazılmış bir HTTP istemcisinden gelsin, `403 PLAN_UPGRADE_REQUIRED` hatası ile reddedilir. Oturumu kapatılmış bir çağrı yapan kullanıcı ise bunun yerine `401 AUTH_REQUIRED` kodunu alır. Erişim, planın ödemesiz süresi boyunca (aylık planlarda 30 gün, yıllık planlarda son kullanma tarihine kadar) çevrimdışı olarak çalışır ve bu süre dolduğunda sona erer; `chloros-cli status`, nedenin görülebilir olması için çalışmaya devam eder (bu, kademelendirme sınırlamasından muaf olan tek SDK/CLI yoludur — `GET /api/license-status`).

---

## Beş Dakikalık Hızlı Başlangıç

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI GNDVI OSAVI

# 3. The lattice / daq commands need a running backend: on Windows open the
#    Chloros desktop app; on Linux start one (or see "Start the Backend")
chloros-backend &

# 4. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 5. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 6. Read a spectrum from a connected DAQ-U (Windows: --port COM3)
chloros-cli daq pool-connect --port /dev/ttyUSB0
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

`process` (2. adım), çalışmakta olan bir arka uç yoksa kendi çalışması için bir arka uç başlatır ve iş bittiğinde durdurur; bu nedenle 4. adımdan itibaren bir arka uç başlatın. Bkz. [Arka Ucu Başlatma](#start-the-backend).

---

## `chloros-cli process`

Bir resim klasörünü tam Chloros iş akışı üzerinden işleyin (hedef algılama → kalibrasyon → vinyet → yansıma → indeks dışa aktarma).

### Özet

```
chloros-cli process INPUT [OPTIONS]
```

### Konumsal Argümanlar

| Argüman | Açıklama |
| --- | --- |
| `INPUT` | `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE) veya `.dng` dosyalarını içeren giriş klasörünün yolu. |

### Genel Seçenekler

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `-o, --output PATH` | Varsayılan proje klasöründe `-n` adında yeni bir klasör | Proje klasörü. Proje, bu klasörün adını alır ve halihazırda bir `project.json` dosyası bulunan bir klasör, üzerine yazılmak yerine bir `_1` / `_2` adlı bir alt klasör eklenir, üzerine yazılmaz. Varsayılan proje klasörü, `set-project-folder` (veya GUI) tarafından seçilen klasördür; aksi takdirde `Documents/MAPIR/Chloros Projects` kullanılır. |
| `-n, --project-name NAME` | otomatik (zaman damgası) | Proje adı, yalnızca `-o` **olmadan** kullanılır (`-o` ile proje, klasörün adını alır). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware`, Survey3 görüntüler için Chloros+ sinir ağı tabanlı demosaic işlemi kullanır; daha yavaştır ancak daha yüksek kalitededir. Yalnızca Survey3 içindir: LATTICE çekimleri her zaman standart demosaic yöntemini kullanır; bu nedenle, LATTICE çekimlerini içeren bir klasör için `process`, içe aktarımdan sonra `texture-aware`'i, kaç tane olduğunu belirten bir hata mesajıyla reddeder ve herhangi bir işleme başlamadan önce `1`'i herhangi bir işlem yapmadan kapatır (Chloros uygulaması da bunlar için Texture Aware özelliği sunmaz). Bir CUDA GPU'ya ihtiyaç duyar (Windows / Linux x64'te bir NVIDIA GPU, Jetson'da Orin'in GPU'su): bunun yokluğunda işlem `standard`'i kullanır ve arka uç, nedenini açıklayan bir `[DEBAYER] … needs a CUDA GPU …` satırı günlüğe kaydeder (`--verbose` ile gösterilmiştir). |
| `--vignette / --no-vignette` | `--vignette` | Vinyet düzeltmesi. |
| `--reflectance / --no-reflectance` | `--reflectance` | Yansıtma kalibrasyonu (bulunursa panel hedefi kullanılır, LATTICE için NIST seri başına kalibrasyonu). LATTICE multispektral için bu, yansıtma **ürünü** olarak da işlev görür — bkz. [Ürün Bazında Dışa Aktarma Seçenekleri](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | kapalı | Sidecar dosyalarından PPK GNSS düzeltmelerini uygular. |
| `--exposure-pin-1 CAMERA_MODEL` | yok | PPK zaman senkronizasyonu için pozlama pini 1'e bağlanan kamera modeli. |
| `--exposure-pin-2 CAMERA_MODEL` | yok | PPK zaman senkronizasyonu için pozlama pini 2'ye bağlanan kamera modeli. |
| `--recal-interval SECONDS` | `0` | Saniye cinsinden minimum yeniden kalibrasyon aralığı (projenin *Minimum yeniden kalibrasyon aralığı*). |
| `--timezone-offset HOURS` | `0` | Işık sensörünün UTC'ye göre saat dilimi farkı, saat cinsinden (projenin *Işık sensörü saat dilimi farkı*). |
| `--format FORMAT` | `TIFF (16-bit)` | Aşağıdakilerden biri: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)` değerlerinden biri. Chloros, bir görüntünün bit derinliğini asla artırmaz: tamamen 8 bitlik görüntülerde daha derin bir format `TIFF (8-bit)` olarak yazılır ve komut dizisi bunu bir kez belirtir. 8 bitlik ve 12/16 bitlik yakalamaların bir arada bulunduğu bir projede, her yakalama kendi derinliğinde veya daha düşük bir derinlikte dışa aktarılır. Radiance her zaman 32 bitlik kayan noktalıdır. |
| `--indices NAME [NAME ...]` | yok | Ön ayar adına göre bitki örtüsü indeksleri (`NDVI`, `GNDVI`, `OSAVI`, `SAVI`, `EVI`, `GCI`, …), büyük/küçük harf duyarlı değildir. Her biri, kamerasının bantları için projeye eklenir; yeni bir proje dahil edilir. Ön ayar olmayan veya kameranın bantlarıyla oluşturulamayan bir ad (örneğin, `NDRE` için RE ve NIR bantları gerekir, ancak bunları tek bir kamera sağlayamaz), bir uyarı mesajında belirtilir ve indeks görüntüsü oluşturulmaz. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFF’leri için iş akışı giriş noktasını zorla (Survey3.raw bundan etkilenmez). Ayrıca, **raw dosya içermeyen** bir yakalama işleminin tamamen işlenmesine olanak tanıyan bir kaçış yolu — bkz. [Bir yakalama klasörü nasıl görünür](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | açık | Doğrusal debayering uygulanmış ürünü gönderir (`Debayered_Images`). Bkz. [Ürün Bazında Dışa Aktarma Anahtarları](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | açık | Ekran önizlemesini (`Preview_Images`) verir: RGB = beyaz dengesi (varsa DAQ ışık kaynağı, yoksa gri dünya) + gama; multispektral = sahte renk genişletme. |
| `--radiance / --no-radiance` | açık | float32 parlaklık değerini gönderir (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | LATTICE yansıma ürünü için referans: `auto` = Kalite Kontrolü (QA) testini geçen çerçeve içi hedef mutlak referanstır (QA bayrağı olan bir hedef, ölçülen puan konusunda DAQ ile rekabet eder), DAQ aşağı doğru yayılımı (ρ = π·L/E) yedek seçeneği; `target` = katı (DAQ ikamesi yok); `daq` = DAQ-yetkili. Bkz. [Ürün Başına Dışa Aktarma Anahtarları](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | yok | Birim başına **ölçülen** hedef yansıma taramalarının dizini (`<serial>.csv`); bulunamadığında nominal T3/T4P spektrumlarına geri döner. |
| `--array-alignment / --no-array-alignment` | açık | LATTICE dizileri: her bir yakalamanın `Chloros:Alignment*` XMP dosyasında damgalanan modül-modül hizalamayı işlenmiş tüm ürünlere (debayering / önizleme / parlaklık / yansıma / indeks) uygular. Etiketleri olmayan görüntülerde işlem yapılmaz. |
| `--array-alignment-crop / --no-array-alignment-crop` | kırpma | Hizalanmış dışa aktarımları, dizinin ortak örtüşme bölgesine kırpın; böylece tüm modüller tek bir ayak izini paylaşır; `--no-…`, sensörün tam tuvalini korur (kaynağın dışını siyahla doldurur). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Hizalama çarpıklığı için yeniden örnekleme. `nearest`, kaynak DN'lerini tam olarak korur (pikseller arası radyometrik değer karışımı olmaz). |

### Hedef Algılama Seçenekleri

| Bayrak | Açıklama |
| --- | --- |
| `--min-target-size PIXELS` | Dedektör için minimum kalibrasyon hedefi boyutu (piksel cinsinden). |
| `--target-clustering 0-100` | Hedef kümelenme yüzdesi (varsayılan `60`). |
| `--target / --targets` | Kalibrasyon hedeflerini yalnızca giriş dosyasının `target` veya `targets` alt klasörlerinde ara (daha hızlı). |

### Örnekler

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a project folder of your choice (the project is named after it)
chloros-cli process "C:/input" -o "C:/projects/Field_A_2026-05-26"

# Name the project and let it go to the default project folder
chloros-cli process "C:/input" -n "Field_A_2026-05-26"
```

### Ürün Bazında Dışa Aktarma Seçenekleri (LATTICE multispektral)

LATTICE işleme, **tek geçişte geçerli tüm ürünlere**yayılır. Tür bazında dört seçenek — `--debayered`, `--preview`, `--radiance`, `--reflectance` —**varsayılan olarak AÇIK** durumdadır; birini devre dışı bırakmak için `--no-<type>` formunu kullanın. RGB ana kameralar her zaman yalnızca debayering uygulanmış + önizleme verisi yayar (bant başına parlaklık/yansıtma yoktur), bu nedenle `--radiance`/`--reflectance` bu kameralar için hiçbir işlem yapmaz. Bu anahtarlar, Survey3 `.raw` için (standart yansıma/hedef yolunu izleyen) göz ardı edilir. *(Eski `--radiometric-output {reflectance,radiance,sensor-response}` bayrağı **kaldırıldı** ve bu anahtarlarla değiştirildi; artık `sensor-response` seviyesi yoktur.)*

| Ürün | Çıktı | DAQ aşağı doğru akışı gerekli mi? |
| --- | --- | --- |
| `--debayered` | Doğrusal demosaik (`Debayered_Images`): bir yakalama işleminin `debayered` seviyesinde kaydettiği dosyanın aynısı — kalibrasyon paketinin ham veriden alınan karanlık ofseti (DSNU), ardından OpenCV'nin kenar duyarlı demosaic işlemi; doğrusallık LUT'u, düz alan veya renk ayrıştırma yok. | Hayır. |
| `--preview` | Ekran önizlemesi (`Preview_Images`): RGB = WB + gama; multispektral = sahte renk genişletme. | Hayır. |
| `--radiance` | tam radyometrik zincirden (`Radiance_Images`) elde edilen float32 W/m²/sr/nm. | Hayır. |
| `--reflectance` | uint16 yansıma katsayısı ρ (`32768` = 1,0), Pix4D uyumlu. | **Evet**, QA'dan geçen çerçeve içi bir hedef tarafından sabitlenmediği sürece (aşağıya bakın). |

`--reflectance-source`, yansıma referansını seçer:**`auto`**(varsayılan), kalite kontrolünden geçen çerçeve içi bir hedefi**mutlak referans**haline getirir — hedefle sabitlenmiş ampirik çizgi zincirleri, ayrılmış paneller üzerinde çapraz puanlanır ve ölçülen en iyi sonuç bant bant uygulanır: DAQ'nın kalibre edemediği veya DAQ eğim uyumu başarısız olan bir bant, hedefin kendi çizgisini alır (hangisi olduğu `ReflectanceElBandMethods` tarafından belirtilir) — bir bayrakla (bir panelin düşürülmesi, bir bandın arızalı olması) QA'yı geçen bir hedef de o ölçülen puan üzerinden DAQ bölünmesiyle rekabet eder ve kaybettiği çerçeveye `ReflectanceChain=daq` damgası vurulur — Hedef bulunmadığında veya kalite kontrol başarısız olduğunda DAQ aşağı doğru akım eşiğine (ρ = π·L/E) geri dönülür;**`target`**katıdır (DAQ ikamesi yoktur);**`daq`**, DAQ'nın yetkili davranışına geçer. Hedef geometrisi (ArUco / sabit ROI / şerit) proje hedef yapılandırmasından gelir; `--target-reflectance-dir DIR`, birim başına**ölçülen** taramaları (`<serial>.csv`) tutar; bu taramalar, hedef birimin seri numarası/QR kodu ile aranır ve yedek olarak nominal T3/T4P spektrumları kullanılır.

DAQ yansıma yolu, kaydedilmiş bir **`.daq`**(DAQ-U/M/E)**veya görüntülerle birlikte bulunan DAQ-M yerel `.csv`**dosyasından otomatik olarak çözümler. Kamera başına veya DAQ kalibrasyon paketi yerel olarak önbellekte bulunmuyorsa, iş akışı ilk kullanımda**bunu AWS'den otomatik olarak alır** (bir kez internet bağlantısı gerektirir; `~/.chloros/` altında önbelleğe alınır).**Bu okuma kime aittir?**Bir yakalama işleminin kaydettiği `.daq`, tanımladığı kamera(lar)ın okumasıdır — yanında bulunan bir `<file>.daq.cameras.json` yan dosyasında adı belirtilir (`lattice capture`, `multi-capture`, `array-capture` ve GUI'de de aynı şekilde; bir ham seri, manifestosunda her bir üyenin okuma değerini belirtir) — ve `process` bunu**yalnızca bu kameralardan gelen görüntülerle**. Kendi okuma verisi olmayan bağımsız bir kamera, bu nedenle artık başka bir kameranın DAQ'sını ödünç almaz; ona bir DAQ bağlayın veya bir kayıt oluşturun. Bir DAQ'nın kendi kaydı (`daq pool-record` veya GUI'de başlatılan bir kayıt) ya da o sidecar'ı içermeyen herhangi bir `.daq` (eski yakalamalar dahil), her kameraya hizmet etmeye devam eder.

#### Yansıma piksellerini okuma (Pix4D / Metashape / kendi komut dosyalarınız)

Yansıma, tamsayı DN olarak saklanır ve **ρ = 1,0 anlamına gelen DN değeri kaynak kameraya bağlıdır**:

| Kaynak | ρ = 1,0 değeri | Nasıl anlaşılır |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ρ 2,0'a kadar marj) | Dosyada XMP `Chloros:PixelScale=32768` damgası bulunur. |
| Survey3 | `65535` (ρ 1,0'da kırpılmış) | `Chloros:*` XMP etiketi yok — bu yokluk *işarettir*. |

**Sabit bir değer varsaymak yerine `Chloros:PixelScale` değerini okuyun ve ona bölün**. Etiket uint16 alanında tanımlandığından, yeniden ölçeklendirme yapılan çıktı formatlarında da `32768` olarak kalır — `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)`, `JPG (8-bit)` ve `TIFF (32-bit, Percent)`'in hepsi kendi kendini tanımlayıcıdır (önce depolanan veri türünü uint16'ya normalleştirin: 8-bit'ten ×257, float'tan ×65535).

> **Eski sürümlerden gelen dosyalar ölçek bilgisi içermeyebilir.** 8-bit yansıma TIFF dosyası yeniden ölçeklendirilir ve diğerleri gibi `Chloros:PixelScale` olarak etiketlenir. 1.3.0 sürümlerinden önceki sürümler ise bunu 0..255 aralığına *kırpıyordu*, bu nedenle ρ≈0,008'in üzerindeki tüm değerler 255'e düzleştiriliyordu ayrıca hem `Chloros:PixelScale` hem de `MicaSense:RadiometricCalibration` ikilisi atlanmıştı. **Bir LATTICE yansıma dosyasında etiket yoksa, ölçek olduğunu varsaymayın — bölünemeyen pikselleri bölmek yerine dosyayı yeniden işleyin**.

#### İndeks piksellerini okuma (NDVI ve diğer bitki örtüsü indeksleri)

**Bir tamsayı indeks rasteri, görüntü başına bir kontrast genişletmesidir, −1…1 aralığındaki bir harita değildir.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` ve `JPG (8-bit)` indeks ürünleri — 8 bitlik bir yakalama işleminin 8 bitlik TIFF olarak kaydedildiği durum dahil —

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

burada `IndexMin` / `IndexMax`, **o karenin kendi en düşük ve en yüksek sonlu dizin değerleridir**. DN 0, −1 değildir ve tam ölçek +1 değildir.

Eşlemenin tersine çevrilebilir kalması için Chloros, her iki uç noktaya da damga vurur:

| Etiket | Anlam |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — aşağıdaki kural |
| `Chloros:IndexMin` | DN 0 olarak depolanan indeks değeri |
| `Chloros:IndexMax` | tam ölçek olarak depolanan indeks değeri |

Bunları okuyun ve ters çevirin; depolanan DN'yi önce depolanan veri türünün maksimum değerine göre 0…1 aralığına normalize edin, böylece tek bir ifade tüm tamsayı formatlarını kapsasın:

```python
import json, subprocess
import numpy as np, tifffile

tags = json.loads(subprocess.run(
    ["exiftool", "-j", "-XMP:all", path],
    capture_output=True, text=True).stdout)[0]

# float(): exiftool hands back a negative real as a quoted string, so
# IndexMin arrives as str on most real NDVI data while IndexMax is a number.
lo, hi = float(tags["IndexMin"]), float(tags["IndexMax"])

raw = tifffile.imread(path)                    # uint16 for TIFF (16-bit)
ndvi = lo + raw.astype(np.float64) / np.iinfo(raw.dtype).max * (hi - lo)
```

Okuma işlemi için `-config pix4d.config` gerekmez — exiftool bu XMP özelliklerini kendi başına okur. Yapılandırma yalnızca bunları *kaydetmek* için gereklidir.

> **`IndexMin == IndexMax`**, o karenin her pikselinin aynı indeks değerine sahip olduğu anlamına gelir. Raster orta ölçekte doldurulmuştur ve bölünecek bir değer yoktur — uç noktayı alın.

> **`Chloros:Index*` etiketlerinin olmaması, belirtilen bir eşleme olmadığı anlamına gelir.** LUT dışa aktarımı, pikselleri LUT eşik pencerenize göre ayarlanmış gradyan renklerden oluşan renklendirilmiş bir renderdır ve sonlu indeks pikseli olmayan bir karenin belirtilecek bir aralığı yoktur. Her iki durumda da bir eşleme olduğunu varsaymayın — bu, eksik bir `PixelScale` ile aynı kuraldır.**Gerilme görüntü başına uygulandığı için, bir uçuşa ait iki kare DN açısından karşılaştırılamaz**— her biri kendi uç noktalarını taşır, bu nedenle aynı NDVI değeri farklı görüntülerde farklı DN değerlerine karşılık gelir. Bir uçuş boyunca nicel çalışmalar (bölgesel istatistikler, sabit eşikler, zaman serileri) için**`TIFF (32-bit, Percent)`**dosyasını dışa aktarın: indeks rasterleri buraya float32**indeks değerleri olarak** yazılır; yeniden ölçeklendirme veya etiket gerekmez. Biçimin adına rağmen, indeks ürünleri ×100 değildir — bu ölçeklendirme yalnızca tamsayı veri türündeki yansıma değerleri için geçerlidir.

#### Karıştırma sınırlama maskesi (bir bandın hangi piksellerde sınırlandığı)

3×3 spektral karıştırma, bir bandı negatif hale getirebilir — ışığın o bandı neredeyse hiç içermediği durumlarda veya sahnenin kameranın karıştırma matrisinin tanımladığı alanın dışında kaldığı durumlarda. Bu gerçek bir değer olmadığı için dışa aktarım işlemi bu değeri 0 olarak sabitler — canlı görüntüde de aynı 0 değeri gösterilir. Bir bant, birkaç pikselde veya neredeyse tüm piksellerde sınırlanabilir; maske her iki durumu da kaydeder. `Chloros:Band{R,G,B}UnmixClampFraction`, bunun bir karenin ne kadarında meydana geldiğini belirtir; kısıtlama maskesi ise **hangi piksellerde** olduğunu gösterir:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— ürün klasörünün**yanında** bulunan bir `clamp_masks/` klasöründe, asla içinde değil (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`; bir `process` dışa aktarımının `tiff16/Reflectance_Calibrated_Images/x.tif` → `tiff16/clamp_masks/Reflectance_Calibrated_Images/x_clampmask.tif`) — her bir parlaklık, yansıma ve indeks ürünü için — en az bir pikselin sınırlandırıldığı her bir ürün için: hem yakalama hem de `process` dışa aktarımları (ve `array-build-video --save-tiffs`'in kare başına TIFF'leri). Tek bantlı `uint8`, ürünün kendi piksel ızgarası üzerinde (yönlendirme, ROI, binning ve dizi hizalaması dahil). Her maske, ürününün kamera adını (`Make` / `Model` / seri numarası), yakalama zamanını ve lens etiketlerini belirtir ve bitlerinin temsil ettiği bantları listeler (`XMP-Camera:BandName`, önce bit 0); `ImageDescription` ve `Software` etiketleri onu bir kelepçe maskesi olarak işaretler ve kalibrasyon içermez.
- **Bitler**: bit 0 = ürünün R kanalındaki bant (`Chloros:BandRName`), bit 1 = G (`BandGName`), bit 2 = B (`BandBName`). Bir bitin ayarlanmış olması, o banttaki pikselin değerinin 0'a sınırlandırıldığı anlamına gelir — bunu ve bundan oluşturulan herhangi bir indeksi, güvenilirliği düşük olarak değerlendirin.
- **`Chloros:UnmixClampMask`**, unmix işleminin uygulandığı her ürüne damgalanır: maskenin, ürünün kendi klasörüne göre göreceli yolu (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, her zaman `/` ile ayrılır — maskeyi açmak için bunu görüntünün klasörüne ekleyin) veya hiçbir piksel sınırlandırılmadığında `none` olarak belirtilir. Etiket olmaması, ürünün hiçbir zaman unmix işlemini çalıştırmadığı anlamına gelir (RGB veya tek renkli kameralar, ham/debayered seviyeler).
- `lattice capture` / `array-capture`, maskenin yolunu kaydedilen dosyanın altına yazdırır (`clamp mask: …`) yazdırır ve yakalama sonuçları listesinde bu, karenin `clamp_mask`'i olarak gösterilir.
- `lattice index --live --save-multiband`, çok bantlı yığın için kaynak kamera başına bir maske yazar — bkz. [`lattice index` — çok bantlı yığın maskeleri](#lattice-index--multiband-stack-masks). Bir videoda maske yoktur: `array-build-video` bunun yerine her karenin sınırlama oranlarını bildirir (bkz. [`array-build-video`](#array-build-video--offline-reprocess-a-saved-burst)).

Hiçbir ürün klasörü, herhangi bir derinlikte maske içermediğinden, bir ürün klasörü (`radiance/`, `reflectance/`, bir `process` dışa aktarımının `*_Images/` klasörü) olduğu gibi Pix4D, Metashape veya diğer fotogrametri yazılımlarına aktarılabilir — alt klasörler dahil — ve bu yazılımlar yalnızca görüntüleri kabul eder. Üstündeki klasörü değil, ürün klasörünü aktarın (çünkü o klasörde `clamp_masks/` ve diğer tüm dışa aktarım türleri de bulunur). Chloros’un kendi içe aktarma işlevi maskeleri atlar. Bir maskenin okunması: `(mask & 1) != 0`, "R kanalı bandı burada sınırlandırıldı" anlamına gelir; `& 2` G, `& 4` B.

#### Dışa aktarıma aktarılan EXIF

`process`, kaynak çekimin **GPS bloğunu ve ExifIFD'sini** kopyalar; dolayısıyla bir
dışa aktarımda coğrafi referanslamanın yanı sıra `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ve
`CameraSerialNumber` etiketlerini coğrafi referanslamayla birlikte içerir.

**`FocalLength`, fotogrametri için isteğe bağlı değildir.** Pix4D, yer örnekleme mesafesini
odak uzaklığı artı irtifadan hesaplar; etiket eksik olduğunda, son derece hatalı bir ölçeğe geri döner. 49
çekimlik bir portakal bahçesi uçuşunda, eksik etiket 411 m × 160 m'lik bir alanı yeniden yapılandırılmış
47,8 km × 13 km'lik bir alana dönüştürdü — çoğunluğu "nodata" olan 455 MP'lik bir ortofoto; bu durum, GSD kontrol edilmeden önce bir döşeme sorunu ve
bir BigTIFF sorunu olarak yorumlandı. Ortofoto'nuz mantıksız bir
ölçekte çıkarsa, önce dışa aktarılan ürün üzerinde `exiftool -FocalLength` komutunu çalıştırın.

Bu kopya kasıtlı olarak **`-all:all`** **değildir**: IFD0'yapısal etiketleri kopyalandığında LATTICE çıktısını bozuyor
ve `ExifImageWidth` / `ExifImageHeight`,
*kaynak* çekimi tanımladıkları için hariç tutulmuştur — aksi takdirde, boyutu değiştirilmiş bir dışa aktarım,
kendi rasteriyle çelişen boyutlar taşırdı. XMP, kopyalanmak yerine doğrudan yazılır; çünkü ExifTool,
XMP bloğu kopyalandığında aynı çağrıya ait XMP etiketlerini atar (bu da MAPIR kalibrasyon etiketlerinin kaybolmasına neden olur).

### Çıktıların yerleştirildiği yer

Ürünler **proje klasörünün altına, önce kameraya, ardından dosya formatına göre gruplandırılmış olarak** yazılır:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

LATTICE için kamera klasörü `LATT-<sensor>-<lens>-F<filter>`'tir (çekimin EXIF
`Model`) ve Survey3 için `<model>_<filter>`’tir — aynı sensörü ve filtreyi paylaşan ancak
lensleri farklı olan iki kamera, vinyet, görüş alanı ve distorsiyon farklılıkları nedeniyle ayrı dizin ağaçlarında tutulur. Biçim
şu şekildedir: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32` şeklinde
`TIFF (32-bit, Percent)` için.

> **Dışa aktarılan her ürün, KAYNAK dosyasının adını korur.** `capture_…_raw.tif` dosyasının
> radiance dışa aktarımı hâlâ `capture_…_raw.tif` olarak adlandırılır — yalnızca
> `tiff32/Radiance_Images/` klasöründe bulunur. **Ürünü tanımlayan dosya adı değil, klasördür**, bu nedenle
> `*radiance*.tif` için genel arama yapıldığında hiçbir şey bulunmaz; bunun yerine dizine göre eşleştirme yapın.

### Işık sensörü kayıtları — kalibre edilmiş `.daq` + `.csv`

`process`, giriş klasörünüzdeki `.daq` kayıtlarını da işler ve bunu yapmak için **herhangi bir**
bunu yapmak için herhangi bir görüntüye ihtiyaç duymaz: tek başına uçurulan bir DAQ-U / DAQ-M / DAQ-E tam bir
kayıttır ve yalnızca `.daq` dosyalarını içeren bir klasör geçerli bir girdidir.

Bir DAQ, kalibrasyonu **olmadan** kaydedilebilir — bu, halka açık
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) kayıt cihazlarının
(`record_daq.py`) varsayılan olarak yaptığı şeydir: ham sensör sayımlarını yazar ve dosyaya damga vururlar, böylece
Chloros o sensörün fabrika kalibrasyonunu **seri numarasına göre** (önce yerel önbellek,
ardından MAPIR Bulut) alır ve uygular. `process` sonucu geri yazar:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv`, her okuma için bir satır içerir: UTC zaman damgası, entegrasyon süresi, toplam güç,
fotopik/skotopik lüks, PPFD (ve mavi/yeşil/kırmızı dağılımı), tepe dalga boyu, ardından
sensörün kendi dalga boyu ızgarasındaki tam spektrum. `.daq` dosyası,
ikinci kez kalibre edilmeden yeniden içe aktarılır.

`<name>`, kaydın kendi dosya adıdır. Farklı klasörlerdeki iki kayıt,
iki kamera tarafından aynı milisaniyede kaydedilen
tek bir "yakalama başına okuma" değerini (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`) paylaşabilir ve her biri yine de kendi çıktılarını alır: ilki
`<name>` adını korur, ikincisi ise adın **sonrasına** onu ayıran klasörleri ekler
(`<name>__<capture>_daq_calibrated.daq`), böylece kaydın tarihi ve saati yine başta yer alır.

İşlem başarılı olduğunda, çalışma `Light-sensor products written: N (calibrated .daq + .csv)` raporunu verir.
Parantez içindeki ifade, gerçekte ne yazıldığını açıklar; dolayısıyla
Paketsiz bir sensör için `(RAW COUNTS — this sensor has no calibration bundle)` ve
her ikisini de içeren bir klasör için `(N calibrated, M raw counts)` şeklinde okunur. Arka ucun kendi
`[DAQ-EXPORT]` ve `[RUN-SUMMARY]` başlıklarının ifadeleri de aynı şekilde türetilir — bu
üçünden hiçbiri, kalibre edilmemiş bir ham dışa aktarmayı kalibre edilmiş olarak nitelendiremez.

Kalibrasyon paketi alınamayan bir DAQ-U / DAQ-M / DAQ-E kaydı — çevrimdışıysanız
veya o sensörün dosyasında kalibrasyon yoksa — **bir gerekçeyle atlanır** ve
`[DAQ-EXPORT]` satırında gösterilir; ham sayıları içeren "kalibre edilmiş" bir dosya olarak asla yazılmaz.
İnternete bağlanın ve işlemi yeniden çalıştırın. Neden, okuyucunun o dosya için
gerçekten belirlediği nedendir (okunamayan şema, paket yok, yazma hatası) ve çalışma
özeti **farklı** nedenleri listeler — tek bir nedenden dolayı atlanan yirmi dosya, yirmi kez tekrarlanan
aynı neden olarak değil, tek bir neden olarak okunur. CLI, çalıştırmanın sonunda atlanan her kaydı
nedeniyle birlikte şu şekilde adlandırır:

```
⚠ Light-sensor recording not exported: <file>.daq: no calibration bundle available for …
```

Kayıtların **hiçbirini** dışa aktaramayan bir klasör başarısız olur: bkz.
[Görüntü yazmayan bir çalıştırma başarısız olur](#a-run-that-writes-no-images-fails).

Adı `_calibrated` veya `_raw` (`field_raw.daq`) ile biten bir kayıt **ithal edilmez**: bunlar, `process`'in kendi ürünlerine verdiği adlardır ve bir ürünün kayıt olarak geri okunması durumunda iki kez sayılır.
ithal edilmez**: bunlar, `process`'in kendi çıktılarına verdiği adlardır ve bir çıktı,
kayıt olarak geri okunduğunda iki kez sayılır. CLI'de bu durum belirtilir ve boş bir klasör olduğunu bildirmek yerine dosyanın adı —
`Not imported as a light-sensor recording: field_raw.daq … Rename the file to import it.`
— gösterilir. Bu tür kayıtları işlemeden önce yeniden adlandırın.

#### DAQ-A kayıtları ham sayımlar olarak dışa aktarılır

**DAQ-A** ailesi, seri numarası bazlı paket sisteminden önce ortaya çıkmıştır ve alınacak bir kalibrasyon paketi
bulunmamaktadır — bunun yerine sahada bir yansıma hedefi kullanılarak kalibre edilir; işte
bu nedenle hiçbir zaman buna ihtiyaç duymamıştır. Bu kayıtların reddedilmesi, verilerin
hiçbir şekilde dışa aktarılmamasına neden olacağından, **farklı bir ad** altında dışa aktarılırlar:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Dosya içindeki bir işaret yerine farklı bir dosya adı kullanılır, çünkü bu bilginin
sadece dosya adı olarak e-posta yoluyla gönderildiğinde de korunması gerekir. `.csv` başlığı,
`raw spectral sensor counts (NOT irradiance)` değerini belirtir ve değerlerin dosya **içinde** karşılaştırılabilir
olduğunu belirtir — ki bu tam olarak hedef tabanlı kalibrasyonun bunları kullanma amacıdır — ve
sensörler arasında değil. Gücü yansıtan fotometrik sütunlar (toplam güç, fotopik ve
skotopik lüks, PPFD), sayımlardan entegre edilmeyip **NULL** olarak yazılır ve çalışma
özetinde `RAW COUNTS` yazdığı için, bir günlüğe "dışa aktarılan" ifadesi ışık şiddeti olarak okunamaz.

Eski **v1.01 / v1.02** kayıtları (bunlar bir DAQ-A-SD tarafından yazılır) okuma başına zaman bilgisi taşımaz,
sadece dosyanın yazılma zamanını içerir. Görüntü↔aşağıya doğru ışın eşleştirici bunları hâlâ reddediyor — bir
kareyi yazma zamanıyla eşleştirmek görünmez bir şekilde yanlış olur — ancak dışa aktarıcı bunları okur ve
CSV dosyası `clock=daq_created_on` olarak yazdırılır, böylece ürün hangi saatte olduğunu belirtir.

### Notlar

- `process`, klasörünüzün Survey3, LATTICE veya karışık olup olmadığını otomatik olarak algılar.
- İlerleme durumu, Sunucu Tarafından Gönderilen Olaylar (Server-Sent Events) üzerinden aktarılır; CLI, iş parçacığı başına canlı ilerleme durumunu gösterir (Algılama, Analiz, İşleme, Dışa Aktarma).
- Linux/Jetson için, CLI takas alanını kontrol eder ve büyük klasörleri işlemeden önce uyarı verebilir. Doku duyarlı debayer ayrıca düşük güç tüketimli Jetson'larda (Nano, Orin Nano) GPU frekans sınırını otomatik olarak uygular.
- İşlem başarılı olduğunda, çalıştırma kaç adet görüntü ürünü yazdığını bildirir (`Image products written: N`; unmix işlemi herhangi bir pikseli kısıtladığında ise `(plus M clamp masks)`).
- **`process`, çalışan bir arka ucu güvenli bir şekilde paylaşır.** Zaten çalışmakta olan bir arka ucu, yalnızca o arka uç önceki bir proje yüklü halde boşta olduğunda ve hiçbir kamera, dizi veya DAQ tutmadığında otomatik olarak yeniden başlatır. Bunlardan herhangi birini barındıran bir arka uç (bir diziyi aktaran GUI, bir SDK komut dosyası, bir DAQ kaydı), olduğu gibi kullanılır ve asla yeniden başlatılmaz; bu sayede `process`, canlı bir dizinin yanında çalışabilir. Arka uç, devam eden bir çalışma bildirdiğinde, `process`, işlemin bitmesi için en fazla 15 saniye bekler (yeni tamamlanmış bir işlemin silinmesi yaklaşık bir saniye sürer), ardından `Backend is mid-run (processing). Wait for it to finish, or pass --restart to force a fresh backend (this kills the in-flight run).` hatasıyla reddedilir. Global `--restart` bayrağı yine de yeni bir arka uç başlatır ve üzerindeki tüm kamera, dizi veya DAQ oturumlarını sonlandırır. Bir Linux systemd hizmeti tarafından çalıştırılan bir arka uç asla yeniden başlatılmaz: `process` onu kullanır ve `--restart` bunun yerine `sudo systemctl restart <unit>` mesajını yazdırır (bkz. [Arka ucu hizmet olarak çalıştırma](#running-the-backend-as-a-service-headless)). Chloros uygulamasının kendi arka ucu da öyle değildir: uygulama açıkken, `process` onu kullanır ve uygulamanın projesi yerine kendi projesini yükler (onun yerine başlatılan bir arka uç, açık uygulamayı kilitlerdi).
- **Çalışmakta olan bir işlem, kendi projesini korur.** Bir işleme çalışması devam ederken — uygulama, `process`, bir SDK komut dosyası veya `chloros-mcp` tarafından başlatılmış olsun — arka uç, bir projenin dosyalarını oluşturmayı, açmayı, içe aktarmayı, eklemeyi veya kaldırmayı, taşıma, silme veya bir projenin işleme ayarlarını değiştirme işlemlerini reddeder; bu durumda HTTP 409 `PROCESSING_IN_PROGRESS` ve `A processing run is in progress on project '<name>', so ... now is refused` hataları verilir; bunu alan bir `process`, `1`'ten çıkar. Görüntüleme ayarları (küçük resim boyutu) yine de kaydedilir. İşleme modu ve projenin hedef yansıma ayarları da aynı şekilde reddedilir (bir çalıştırma, başladığı modda kalır). Çalışma sırasında projeye kaydedilen bir yakalama (uygulamadan, CLI'den, SDK'den veya `chloros-mcp`'ten), her zamanki gibi kaydedilir, ancak çalışma sona erdiğinde projenin dosya listesine eklenir; buna ilişkin yanıtta da belirtilmiştir (`project_add`); projeye eklenen bir ışık sensörü kaydı da aynı şekilde davranır. Önceden, ikinci bir istemci çalışma sırasında açık projeyi değiştirebiliyordu ve devam eden çalışma diğer projeye aktarılıyordu.
- **Ctrl+C, yalnızca CLI'yi değil, arka plandaki çalışmayı da durdurur.** CLI, arka uçtan işlemeyi durdurmasını ister, `Waiting for the backend to stop (Ctrl+C again to stop waiting)...` mesajını görüntüler ve `Processing stopped.` mesajını almak için en fazla 120 saniye bekler; böylece `chloros-cli process … && next`, hâlâ çalıştırma işlemiyle meşgul olan bir arka uçta `next` komutunu başlatmaz. İkinci bir Ctrl+C tuşuna basılması beklemeyi sonlandırır. Ardından, kesintiye uğrayan tüm komutlarda olduğu gibi, `process` komutu `130`'ten çıkar.

#### Görüntü yazmayan bir çalıştırma başarısız olur

Ürünler talep ettiyseniz ve çalıştırma **hiçbir** şey yazmadıysa — sadece `project.json` ve
`calibration_data.json` — `process` bunu bir hata olarak değerlendirir:
`Processing finished but wrote no image products.` mesajını yazdırır ve **sıfırdan farklı bir değerle sonlanır**, böylece bir komut dosyası
bunu algılayabilir. Mesajda proje klasörü ve olası nedenler belirtilir:

- giriş klasörü bir yakalama olarak tanınmadı (düzeni ve `--input-level`'i kontrol edin) veya
- istenen tüm ürünler, söz konusu kameralar için geçerli olmadığı gerekçesiyle atlandı (ör.
  RGB'den ibaret kameralardan radyans/yansıtma istenmesi).

`--verbose` ile yeniden çalıştırın ve arka uç günlüğünde `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` satırlarını kontrol edin;
bu satırlar, CLI çıktısında başka türlü görünmeyen kamera bazındaki atlamaları açıklar.

Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma — tüm ürünler devre dışı bırakılmış ve `--indices` kullanılmamış — yine de
**başarılı** sayılır, çünkü bu durumda boş bir görüntü çıktısı doğru sonuçtur.

#### Dışa aktarımları doğrulanamayan bir çalıştırma başarısız olur

Bir çalıştırma sonrasında, `process`, ExifTool ile dışa aktarılan dosyaların etiketlerini kontrol eder. ExifTool çalıştırılamazsa,
`Dışa aktarma doğrulaması BAŞARISIZ: ExifTool çalıştırılamadı, bu nedenle M adet dışa aktarılan dosyadan N'sinin etiketleri
kontrol edilemedi (arka uç günlüğündeki [EXIFTOOL] satırlarına bakın)` and ` Ürünler yazılır ve
yerlerinde kalır; doğrulamak için işlemi yeniden çalıştırın.`, does not print ` İşlem tamamlandı!` mesajını yazdırır ve **sıfırdan farklı bir değerle sonlanır**.
Ürünler korunur.

#### Bir çalıştırmanın hangi çekimleri içerdiği

`process` çalıştırılmadan önce tamamlanan çekimler dahil edilir. Çalıştırma sırasında kaydedilen çekimler dahil edilmez; `process`
kaç tanesinin dışarıda kaldığını bildirir ve programı tekrar çalıştırdığınızda bunlar dahil edilir.

#### Görüntü kaybı yaşanan bir çalıştırma başarısız olur

Bir çalıştırma sırasında bir görüntü başarısız olursa, `process`, en
yaygın nedeni içeren `N image(s) failed and were not exported` mesajını yazdırır, `Processing complete!` mesajını yazdırmaz ve **sıfırdan farklı bir değerle sonlanır**. Diğer ürünler
yazılır ve yerinde kalır; arka uç günlüğünde ise her bir hata kaydedilir.

Aynı durum **sadece ışık sensörüne dayalı bir çalıştırma** için de geçerlidir: `.daq` kayıtlarından oluşan bir klasörde, tanım gereği dışa aktarılacak görüntü yoktur
ve çalıştırma, bunun yerine yazdığı kalibre edilmiş `.daq` / `.csv` dosyalarına göre değerlendirilir.
Eğer **hiçbir şey** yazmadıysa — tüm kayıtlar atlandı —
`Processing finished but wrote no light-sensor products.` mesajını yazdırır, her kayda atlanma
nedenini ad olarak verir ve **sıfırdan farklı bir değerle sonlanır**.

---

## `chloros-cli login`

Bu makineyi bir Chloros+ bulut hesabıyla doğrulayın. Kimlik bilgileri, `~/.chloros/user_session.json` dosyasında güvenli bir şekilde önbelleğe alınır.

```
chloros-cli login EMAIL PASSWORD
```

### Örnekler

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell'in `$$` bozulması otomatik olarak düzeltilir.** Çift tırnak içinde PowerShell `$$` ifadesini genişletir (onu şifreden çıkarır ya da şifrenin bazı kısımlarını çoğaltır). 401 hatası alındığında CLI otomatik olarak önce `$$` yeniden eklenmiş hâliyle, ardından şifrenin yinelenmeyen yarısıyla yeniden dener; bir deneme başarılı olursa oturumunuzu açar ve bir dahaki sefere kullanmanız gereken doğru tek tırnaklı sözdizimini yazdırır.

> **Başlıksız/komut dosyası kullanımı: önbelleğe alınmış oturum olmaması, oturum açma isteminin görüntülenmesi anlamına gelir.** Önbelleğe alınmış oturum olmadan çalıştırılan bir çekirdek komut (`process`, `status`, `export-status`, `time-sync`, …) önbelleğe alınmış oturum olmadan çalıştırıldığında, devam etmeden önce stdin üzerinden `Email:` / `Password:` değerini ister. stdin kapalıyken (cron, bir systemd birimi) hemen `login cancelled` hatasıyla başarısız olur; stdin açık bir terminal veya boru ise girişi bekler. Başsız işleri planlamadan önce her makine için bir kez `chloros-cli login EMAIL PASSWORD` komutunu çalıştırın.

---

## `chloros-cli logout`

Önbelleğe alınmış oturumu temizler ve bir sonraki çağrıda yeni bir oturum açılmasını zorlar.

İşleme çalışması devam ederken oturumdan çıkmak, çalışmayı durdurmaz: çalışma başladığı işleme modunda tamamlanır ve bir sonraki çalışma standart işleme modunu kullanır. `logout` bunu belirtir (`The processing run in progress finishes in <mode> mode; the next run uses standard`), ayrıca oturumdan çıktığınızda uygulama da bunu belirtir .

```bash
chloros-cli logout
```

---

## `chloros-cli status`

Mevcut lisans kademesini (Iron/Copper/Bronze/Silver/Gold), kimliği doğrulanmış kullanıcıyı ve cihaz bağlama sayısını gösterir.

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

Canlı Thread-4 dışa aktarma ilerlemesini sorgular. Başka bir kabuktan `process` komutu **çalışırken** güvenli bir şekilde çağrılabilir.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

CLI'nin görüntüleme dilini ayarlar (CJK, RTL ve Hint dilleri dahil olmak üzere 38 dil desteklenir). Komut dosyasını görüntüleyemeyen eski konsollarda sorunsuz bir şekilde İngilizceye geçer.

```
chloros-cli language [LANG_CODE] [--list]
```

### Örnekler

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## Proje Klasörü Komutları

Bunlar, varsayılan proje klasörü konumunu yönetir (GUI ile paylaşılır).

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

`get-project-folder`, yeni projelerin kaydedileceği klasörü yazdırır. Yapılandırılan klasör artık mevcut değilse (silinmiş, yeniden adlandırılmış veya bağlı olmayan bir sürücüde bulunuyorsa), CLI, GUI'de olduğu gibi varsayılan klasöre geri döner: varsayılan klasör stdout'a yazdırılır, eksik klasörü belirten bir uyarı stderr'e gönderilir ve çıkış kodu `0` olur.

---

## `chloros-cli update`

Yalnızca Linux / Jetson. `version_url` ile `/etc/chloros/update.conf`'i karşılaştırır ve daha yeni bir sürüm varsa, uygun `.deb` sürümünü indirir ve hemen `sudo dpkg -i` ile yükler (onay istemesi yoktur; `sudo` şifrenizi isteyebilir). Jetson'da yalnızca JetPack 6 sistemine güncelleme sunulur. Windows'da komut hiçbir işlem yapmaz ve "`up to date`: yeni bir yükleyiciyle güncelleyin" mesajını verir.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linux/Jetson'da CLI, **her başlatmada otomatik güncelleme kontrolü** (engellemez, komutu asla geciktirmez): `/etc/chloros/update.conf` mesajını okur, sonucu 1 saat boyunca `~/.chloros/update_cache.json`'te önbelleğe alır ve daha yeni bir sürüm mevcut olduğunda `Update available: vX.Y.Z / Run: chloros-cli update` mesajını görüntüler. Herhangi bir hata durumunda ve Windows'ta sessizce atlanır; `CHLOROS_DISABLE_UPDATE_CHECK=1` komutu bunu devre dışı bırakır. 1 saatlik önbellek süresi nedeniyle, bir saat içinde yayınlanan bir sürüm hala güncel olarak okunabilir.

---

## `chloros-cli selftest`

7 adımlı bir hızlı test gerçekleştirir: sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), gürültü giderici modelin varlığı, CUDA+gürültü giderici hazırlığı. Çalışmakta olan bir arka uç kullanır veya test için bir arka uç başlatır ve testten sonra durdurur. `N/7 checks passed` ve başarısız olan kontrolleri yazdırır ve yalnızca yedi kontrolün tamamı başarılı olduğunda `0`'ten çıkar; CUDA GPU'su olmayan bir bilgisayarda CUDA+gürültü giderici kontrolü başarısız olur (bkz. [Kurulumu Doğrulama](#verify-the-install)).

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP grandmaster durumu ve kontrolü. Chloros ana bilgisayarı PTP grandmaster'ı çalıştırır; LATTICE kameraları ve DAQ-E birimleri, cihazlar arası zaman damgaları için buna bağlı çalışır (cihazlar arasında yaklaşık 1 ms). PTP, **zaman damgalarını** hizalar: bir dizideki kameralar, PTP nedeniyle değil, M8 donanım tetikleyicisinin onları ateşlemesi nedeniyle (modüller arasında 100 µs'den az bir sürede) aynı anda poz verir. Alt komut içermeyen `chloros-cli time-sync`, `status`'i çalıştırır.

| Alt komut | Açıklama |
| --- | --- |
| `status` | Grandmaster durumunu, BMCA önceliklerini, saat kimliğini ve paketlerinin ağa ulaşıp ulaşmadığını gösterir: `health`, grandmaster çalışmadığında `OK` (grandmaster çalışmıyorsa `STOPPED`) veya `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` ve bir `health_detail` ile, ve ardından stderr'a bir `WARNING:` satırı yazdırılır (stdout JSON olarak kalır). `sync_sent` / `announce_sent` sadece gönderilen mesajları sayar; `*_send_errors` ve `last_send_error` ise gönderilmeyenleri sayar. Bir kamera bağlantısı kesilip yeniden kurulduktan sonra, grandmaster soketlerini kendi kendine yeniden oluşturur (`rebinds`, `last_rebind`). |
| `peers` | Delay_Req aracılığıyla görülen slave'leri listeler (kameralar + DAQ-E sensörleri). |
| `cameras` | Kamera başına PTP durumu (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Grandmaster sürecini yeniden başlat. |
| `set-priority --priority1 N --priority2 N` | BMCA önceliklerini geçersiz kıl. |

Grandmaster, kamera ağ kartlarında (`169.254.x.x` adresi içeren herhangi bir adaptör). Çalışır durumda olan adaptör bulunmadığında, yalnızca kablolu bağlantılı olduğu durumlarda varsayılan yolun adaptörüne geri döner; Wi-Fi veya sanal adaptörlere (Tailscale, WSL) asla dönmez, çünkü hiçbir kamera veya DAQ-E, bunların arkasında bulunmaz. Duyurulacak bir şey olmadığında, soketler olmadan çalışır (`health` `NO_SOCKETS`) ve bir sonraki kamera veya DAQ bağlantısı, o anda aktif olan ağ kartında bunu yeniden oluşturur; `time-sync restart` bunu anında yapar.

Grandmaster, arka uçtan ayrı olarak kendi sürecinde çalışır (`own_process` / `process_pid`, `time-sync status` içinde), Windows’ta yüksek öncelikli olarak çalışır (`process_priority`); böylece meşgul bir arka uç (bir işleme koşusu, bir dışa aktarma, aynı anda birkaç komut) damgalama zamanlarını geciktiremez; önceden, böyle bir gecikme tüm kameraların saatini aynı anda kaydırabilirdi. Bu işlem arka uçla sona erer; başlatılamazsa grandmaster eskisi gibi arka uç içinde çalışır (`own_process` false ve arka uç günlüğünde bu belirtilir).

### Örnekler

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

LATTICE kamera kontrolü. Her alt komut, Chloros arka ucundan geçer; arka uç kamera havuzuna sahiptir, bu nedenle sonraki CLI çağrıları aynı açık tanıtıcıyı yeniden kullanır.

### Ortak Seçenekler (çoğu alt komut tarafından paylaşılır)

| Bayrak | Açıklama |
| --- | --- |
| `-d, --device N` | Kamera dizini (varsayılan: 0). |
| `-s, --serial SN` | Belirli seri numarası; `--device`'i geçersiz kılar. |
| `--serials SN1,SN2,…` | Çoklu kamera çalışması için virgülle ayrılmış seri numaraları. [Geçici çoklu kamera komutları](#multi-camera-transient-sessions) yalnızca bu kameraları açar; ilki tetikleyiciyi çalıştırır. |
| `--all` | Tespit edilen her kamerada çalıştır. |
| `--exposure US` | Mikro saniye cinsinden pozlama süresi. |
| `--gain DB` | dB cinsinden kazanç. |
| `--pixel-format FMT` | örn. `BayerRG8`, `BayerRG12`; `Mono8`, M3M'de `Mono12`. Ad, kameranın kendi ailesinde bit derinliğini belirler: M3M'de bir Bayer adı, derinliğinin Mono formatını belirler (`BayerRG8`, `Mono8`'i belirler), M3C'de ise bir Mono adı, Bayer adını (`Mono12`, `BayerRG12`'i ayarlar). |
| `--width N` / `--height N` | Görüntü boyutları. |
| `--preset {default,high_quality,high_speed,triggered}` | Ayar ön ayarını uygula: piksel formatı, pozlama, kazanç ve kare hızı (`high_speed`: `BayerRG8`, 5 ms, 6 dB, 60 fps; `high_quality`: `BayerRG12`, 20 ms, 0 dB; `lattice presets` bunları listeler). M3M'de format, aynı derinlikteki Mono formatıdır: `high_speed`, `Mono8`'i yakalar, `high_quality` ve `triggered`'i yakalar; `Mono12`. Bununla birlikte verilen bir bayrak (`--exposure`, `--gain`, `--pixel-format`) ön ayara üstün gelir ve ön ayarda adı geçmeyen bir ayar, kameranın kendi ayarını korur. Tüm serbestçalışır; ancak `triggered` hariçtir — bu ayar, kamerayı 2. Hattaki bir donanım kenarı için tetikler; bu hattı tetikleyen bir şey olmadığında, kamera çekim yapmak yerine sonsuza kadar bekler. |
| `-o, --output DIR` | Çıkış dizini (varsayılan: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paket boyutu. `auto`, ICMP+GVSP sondaları çalıştırır; `jumbo` = 9000; `standard` = 1500. |

### LATTICE Kamera İlk Bağlantı İş Akışı

```bash
# 0. A backend must be running: the Chloros desktop app (Windows),
#    `chloros-backend`, or the Linux service (see "Start the Backend")

# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Full sensor (no binning, no ROI); the frame rate falls to
#        what the host and wire deliver, and the faster options are
#        printed
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### Alt Komut Referansı

#### Keşif ve Bilgi

| Alt Komut | Amaç |
| --- | --- |
| `lattice info` | Bağlı kameraları listeler (üretici, model, seri numarası, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Optimum kamera yapılandırması için ana bilgisayar sistemini analiz eder. `--no-discover`, kamera keşfini atlar (daha hızlı, yalnızca NIC analizi). |
| `lattice network [--fix] [--estimate] [--cameras N] [--pixel-format FMT]` | NIC ayarlarını kontrol eder/düzeltir (Linux'te, ayrıca çekirdeğin soket alım tamponlarını da — bkz. [Linux ana bilgisayarları](#linux-hosts--socket-receive-buffers)); bant genişliğini/FPS'yi tahmin eder. |
| `lattice network-analysis --master SN [--slaves SN1,SN2,…] [--width N] [--height N] [--pixel-format FMT] [--binning {1,2,4}] [--force-tier TIER] [--packed {auto,on,off}] [--backend-url URL] [--json]` | Kararlı şemalı arka uç ağ-yeteneği + dizi önerisi (`status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `--width` / `--height` varsayılan olarak tam sensöre ayarlanır, `2048` × `1536`. `0`'ten çıkıp `ok`, `auto_capped_fps` ve `auto_shrunk`'e, `1` ise `needs_force_slip` ve `error`'e, `--json` olsun ya da olmasın. Arka uca `--backend-url` (varsayılan `http://127.0.0.1:5000`) adresinden ulaşır, `CHLOROS_BACKEND_URL` adresinden değil. `auto_capped_fps`, istenen çözünürlüğü korur ancak hedef fps'yi sınırlar — `recommended.recommended_target_fps`'i okuyun ve bunu bağlantı hedefi olarak iletin; bunu bir başarı olarak değerlendirin, bir hata olarak değerlendirmeyin. `auto_shrunk`, istenen kare herhangi bir kare hızında iletilemediğinde ortaya çıkar; connect, kareleri asla kendi başına küçültmez. |
| `lattice analyze-array [--models M1,M2,…] [--binning {1,2,4}] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--packed {auto,on,off}] [--json]` | Kameraları açmadan "ne olurdu" analizi (`--width` / `--height` varsayılan `2048` × `1536`). Hem bu hem de `network-analysis`, connect'in neyi aktaracağını gösterir: `--pixel-format` olmadığında, bağlantının uyguladığı kamera başına formatlar (Mono12 / BayerRG12) kullanılır; `--packed auto` (varsayılan) kullanıldığında ise [sıkıştırılmış 12 bit](#packed-12-bit-transport---packed) kararını yansıtır — dolayısıyla belirtilen fps, dizinin çalıştığı hızdır. Her ikisi de paketleme kararını gösterir. **`--n-active`, sadece bu dizinin değil, kablodaki toplam kamera sayısını gösterir**— bağımsız kameralar eşzamanlı olarak akış yaptığında veya kablo bütçesi, kameraların sayısını eksik hesaplayan bir talebe göre hesaplandığında bu değeri artırın (varsayılan: `len(--models)`). Her zaman toplam `Wire budget:` (talep edilen MB/s ile çarpışma güvenliği tavanı) ve `Max cameras:` satırlarını yazdırır ve dizi kabloya aşırı yükleme yaptığında `** OVER-SUBSCRIBED**` uyarısını gösterir — bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model). |
| `lattice gpu` | GPU durumunu gösterir. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Kamera donanım yazılımını kontrol eder veya günceller. Yerel `.fwa` seçimi sabitlenmiştir: `firmware/<MODEL_PREFIX>/` içindeki, derlemenin `MIN_FIRMWARE_VERSION` ile eşleşen dosya, mevcutsa (yalnızca yedek olarak en yüksek sürüm), bu nedenle diskte hazır bulundurulan daha yeni bir üretici görüntüsü, bu sabitleme kaldırılana kadar etkin değildir — kasıtlı olarak daha yeni sürümler, imzalı AWS manifestosu aracılığıyla cihazlara ulaşır; bu, daha yeni sürümler mevcut olduğunda tercih edilir. |
| `lattice presets [--apply NAME]` | Kamera ön ayarlarını listeler veya uygular. `--apply`, ön ayarların adlarını (piksel formatı, pozlama, kazanç, kare hızı, tetikleme) belirler ve kamera bunları okuduktan sonra bu ayarları şu şekilde yazdırır: M3M'de, ön ayarın derinliğinin Mono formatı; çalıştığı kare hızı, bu ön ayarın talep ettiğinden daha düşük olabilir (`high_speed` 60 fps ister; 1 Gbps bağlantısındaki bir tam kare kamera yaklaşık 35 fps'de çalışır). "open" komutunun yazdığı diğer tüm ayarlar, bulunduğu haliyle geri yüklenir. `--apply default`, kamerayı fabrika varsayılanlarına sıfırlar: Line2'de serbest çalışan otomatik pozlama, bağlantı verim sınırı ve kusur düzeltme kapalı, bir RGB kameranın kalibre edilmiş beyaz dengesi korunur (diğerlerinde birim değer). Kameranın belleğine hiçbir şey kaydedilmez; bu nedenle bir sonraki açılışında, daha önce olduğu gibi kendi belleğinden başlar. Bir yakalama veya bağlantı komutu olarak `--preset` kullanıldığında, `default` bir ön ayar değildir. |
| `lattice status` | Canlı kamera durumu. |

#### Yakalama

| Alt komut | Amaç |
| --- | --- |
| `lattice capture [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--no-daq] [--daq-tolerance-ms MS]` | Tek kare. **Varsayılan olarak her dışa aktarma türünü kaydeder**(`--processing all`); bkz. [Yakalama Dışa Aktarma Düzeyleri](#capture-export-levels-the-all-default). `--processing`, `all`, `raw`, `debayered`, `radiance`, `reflectance` veya `display` değerlerini alır (`preview`, `display` için kabul edilir); kameraya dokunulmadan önce başka herhangi bir ad reddedilir (`1`'ten çıkılır). `--levels`, açık bir alt kümeyi kaydeder (`--processing`'i geçersiz kılar). Chloros'un elinde olmayan bir kamera çekim için açılır ve pozlaması, [geçici komutlar](#multi-camera-transient-sessions) gibi önce sahneden ayarlanır (en fazla birkaç saniye); çekim bittiğinde, nasıl biterse bitsin, o kamera bulunduğu haline geri getirilir (format, pozlama, renk işleme akışı, çekim sırasında değiştirilen her ayar — [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions) altındaki liste). Kullanılan çekimi okuyan DAQ, `.daq` sidecar dosyası olarak kaydedilir; bu dosyaya yalnızca ham veriler içeren bir yakalama eklenir; `--no-daq` / `--daq-tolerance-ms` dosyaları bunu ayarlar ([Bir yakalamayı okuyan DAQ, şunu kaydeder](#the-daq-reading-a-capture-saves)). `--force-daq` hâlâ kabul edilir. `--jpeg-quality` = JPEG kalitesi 1–100 (varsayılan 95).**Açık projeye kaydedilen dosyalar bu projeye eklenir.** Uygulamada olduğu gibi, dosyaları Chloros'ta açık olan projenin klasörüne düşen bir yakalama, bu dosyaları o projenin dosya listesine ekler; başka bir yere kaydedilen dosyalar ise olduğu gibi bırakılır. Komut, bunu belirten bir satır yazdırır (`Project: ...`) yazdırır ya da bir işleme işlemi devam ederken, dosyaların bekletildiğini ve işlemin bitiminde projeye ekleneceğini bildirir. Aynı şekilde `multi-capture`, `array-capture` (`--smart` de dahil) ve `project capture` komutları da aynı şekilde çalışır. |
| `lattice continuous [--format tiff\|tif\|png\|jpg\|jpeg] [--jpeg-quality N] [--queue-depth N] [--no-daq] [--daq-tolerance-ms MS]` | Ctrl+C tuşuna basılana kadar diske aktarım yapar. Bir `tiff` çalışmasınınçerçeveleri, o kameranın yakaladığı verileri içeren bir diziyi oluşturan `raw` dosyasıdır: sensörün mozaiği, her bir düzlem kendi değerleriyle, sıkıştırılmamış, kalibrasyon paketi ile birlikte (2048 x 1536 boyutunda 12 bitlik bir kare, sahne ne olursa olsun 6,0 MiB artı pakettir), dolayısıyla `process` bunlardan başlar. `png` ve `jpg` 8 bit içerir; dolayısıyla bu seriler, görüntüleme için demosaik işlemi uygulanmış karelerdir ve `process` bunlardan başlamaz. DAQ'ya bağlı (`daq pool-bind`) veya havuzlanmışsa, her kare, serinin `daq/` klasöründe kendine ait `.daq` ve `.match.json` dosyalarına sahip olur; çünkü `lattice capture` bir tane kaydeder; `--no-daq` bunu devre dışı bırakır ve durdurma işlemi kaç tane olduğunu yazdırır. Her kare, tam meta verileriyle birlikte bir kez yazılır; Ctrl+C tuşlarına basıldığında, kamera serbest bırakılmadan önce halihazırda yakalanmış kareler yazılır. Sürücü dolduğunda program kendini durdurur, `Failed:` / `Disk full:` yazdırılır ve program sonlandırılır; yazılmayan herhangi bir kare de programı sonlandırır. Donanım tetikleyicisi için hazır durumda bırakılan bir kamera (`TriggerMode` `On`) olan kamera, çekim süresince serbest çalışma moduna ayarlanır. Çekim bittiğinde kamera bulunduğu duruma geri getirilir — tetikleme ayarları ve çekim sırasında değiştirilen her türlü ayar (pozlama, kazanç, format, …) — `--keep-settings` (çalışma ayarlarını kameranın kendi belleğine kaydeden [serbest çalışma]) veya `--clear-settings` (bu belleği fabrika ayarlarına sıfırlayan) durumları hariç. Hiçbir kare kaydetmeyen bir çalıştırma başarısız sayılır: 10 saniye boyunca kare alınmazsa kendi kendine durur, nedeni ile birlikte `No frames:` yazdırır ve `1` ile çıkar. Yazıcıların yetişemediği kareler kayıp olarak sayılır ve durdurma işlemi, nedenini (kameranın kare hızı, yazıcıların kare hızı ve kuyruk) içeren `Dropped:` mesajını yazdırır; daha düşük bir kare hızı veya 8 bitlik piksel formatı kullanıldığında tüm kareler korunur. Komut, her 2 saniyede bir arka uçla durum kontrolü yapar; kayıt durdurulmadan komut sonlanırsa (`kill -9`, bellek yetersizliği), arka uç son durum kontrolünden 30 saniye sonra kaydı durdurur ve kamerayı (`stop_reason` `client_gone` ve arka uç günlüğünde bir satır). `multi-stream` ve `align-stream` de aynısını yapar. Kameranın açılması, komutun bekleme süresinden (60 saniye; `multi-stream` / `align-stream` için 180 saniye) daha uzun süren bir başlatma — kamera hala açılma aşamasındaysa — arka uç tarafından iptal edilir: kamera geri alınır ve kapatılır; hiçbir kayıt devam etmez (HTTP 504 `CLIENT_DEADLINE_PASSED`). |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Tarayıcı tabanlı canlı MJPEG önizleme: kameraya bağlanır, akış URL'sini gösterir ve tarayıcınızda açar. `--ae-damping`, otomatik pozlama sönümlemesini (0,4–100) ayarlar. |

##### Canlı akışlar (MJPEG; RTSP yok)

Chloros, canlı videoyu **HTTP üzerinden MJPEG**(`multipart/x-mixed-replace`, sınır `frame`) olarak sunar.**RTSP desteklenmez.** Bir URL'yi tarayıcıda açın veya herhangi bir MJPEG istemcisiyle (OpenCV'nin `cv2.VideoCapture(url)`, ffmpeg, VLC) okuyun. Kameranın önce arka uçta bağlanmış olması gerekir (`lattice cam-connect`, `lattice array-connect`, `lattice viewer` veya GUI).

| URL (`http://127.0.0.1:5000` + …) | Akış |
| --- | --- |
| `/api/camera/stream/<serial>` | Ekran önizlemesi, yaklaşık 30 fps'ye kadar. Bir dizi elemanı için, `?max_h=` (varsayılan `720`) ve `?jpeg_q=` (varsayılan `80`) yüksekliği ve JPEG kalitesini ayarlar. Bu, `lattice viewer`'in açtığı URL'dir. |
| `/api/camera/<serial>/stream-annotated` | GUI'nin katmanlarının çizildiği aynı önizleme: `zebra=1`, `crosshair=1`, `grid=3x3` (veya `9x9`), `histogram=1`, `peaking=1`, `spot=1`; sorgu yapılmadığında, projenin o kamera için kaydedilmiş üst katman ayarları. |
| `/api/camera/<serial>/stream-reflectance` | Bant başına 8 bitlik yansıma: ρ = piksel ÷`X-Chloros-Pixel-Scale` başlığı (`refl_max` etkinleştirilmedikçe 255). `fps` (varsayılan `10`), `jpeg_q` (varsayılan `95`), `max_h` (varsayılan `0` = yerel), `band=<name>`, `align=1`. JPEG, dolayısıyla kayıplı. Kamerada yansıma değeri olmadığında (HTTP2phrase kamera, kalibrasyon paketi yok, DAQ okuması yok) HTTP 409 ile reddedilir. |

- **Kameralar arasında karelerin eşleştirilmesi.**Her bir**dizi elemanı**'nın üç akışın tümünde gönderdiği her parça, `X-Chloros-Group-Id` (dizinin senkronize grup sayacı), `X-Chloros-Frame-Id` ve `X-Chloros-Timestamp-Ns` başlıklarını taşır. Aynı `X-Chloros-Group-Id` değerine sahip farklı kameralardan gelen parçalar, aynı tetikleyiciden kaynaklanır. Bağımsız bir kameranın grubu yoktur ve grup kimliği göndermez. Tarayıcılar parça başına başlıkları yok sayar; çok parçalı akışı kendisi ayrıştıran bir istemci bunları okur.
- **Dizi görünümü açıkken duraklatılır.** Bir dizi üyesinin `/api/camera/stream/<serial>`'i, dizinin birleştirilmiş canlı akışı açıkken (örneğin, GUI'nin dizi görünümü) statik bir "duraklatılmış" kare gönderir ve akış kapandığında devam eder.

#### Sensör Ayarlaması

| Alt komut | Amaç |
| --- | --- |
| `lattice configure [{--get N1 N2… \| --set N=V N=V… \| --dump}] [--json]` | Herhangi bir GenICam düğümünü okur/yazar. `--set ExposureTime=…` (`Gain=…`), önce otomatik modu Kapalı konuma getiren `ExposureAuto` (`GainAuto`) anahtarları olmadan kullanıldığında, `lattice exposure --set`'in yaptığı gibi; kameranın reddettiği bir değer stderr'a `Failed to set <node>: …` yazdırılır ve program sonlandırılır `1`. `--get` ve varsayılan gruplandırılmış görünüm, otomatik mod çalışırken `note:`'i `ExposureTime` / `Gain` altında yazdırır — bkz. [Otomatik modda pozlama ve kazanç okuma](#reading-exposure-and-gain-under-auto-mode). |
| `lattice exposure [{--auto \| --auto-once \| --off \| --set US}] [--brightness 0-255] [--brightness-auto] [--damping 0.4-100] [--upper-limit US]` | Pozlama ve AE. Bir `--set` pozlama veya `--brightness` hedefi sizin değeriniz olarak tutulur (Chloros'un otomatik pozlama kontrolü buna müdahale etmez); kazanç olduğu gibi kalır, bu nedenle otomatik modda siz `lattice gain`'i ayarlayana kadar hedefe doğru ayarlamaya devam eder. `--auto` pozlamayı geri verir, `--brightness-auto` hedefi Chloros'un otomatik hedefine geri verir. Bayrak olmadığında pozlama düğümlerini okur, `note:` ile aynı şekilde `configure --get` olarak çalışır. Kameranın reddettiği bir `--set` değeri, stderr'a `Failed to set ExposureTime: ExposureTime=<value> refused (<camera error>); the camera accepts <min>..<max> now (ExposureAuto put back to <mode>)` yazdırır — yazma işleminin kapatıldığı otomatik mod, Kapalı olarak bırakılmak yerine geri yüklenir — `Exposure: … us (auto OFF)` satırını yazdırmaz ve `1`'ten çıkar. |
| `lattice gain [{--auto \| --off \| --set DB}]` | Kazanç ve otomatik kazanç. Bir `--set` değeri, kamera stderr'a `Failed to set Gain: …` yazdırmayı reddediyor, `Gain: … dB (auto OFF)` satırı yok ve program `1`'ten çıkıyor. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N]` | Sensör ROI ve binning. Binning her zaman **Ortalama**'dır — aşağıya bakınız. |
| `lattice format [--set FMT] [--list]` | Piksel biçimi. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Donanım/yazılım tetikleyicisi. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (bayrak yok = tek çekim WB) | WB işlemleri. Yalnızca RGB/Bayer kameralar; mono M3M'de hiçbir işlem yapılmaz (atlanır). Tek seferlik işlem, hesapladığı beyaz dengesini (`BalanceWhiteAuto` ve kazançlar) korur; açık yazma yaptığı diğer tüm ayarlar bulunduğu gibi geri yüklenir ve tek seferlik işlem çalıştırılamadığında tüm ayarlar geri yüklenir. |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct 2000-10000] [--get]` | RGB ekran renk işleme zinciri. `natural` (varsayılan) ucuz canlı işleme seçeneğidir; `enhanced`, tam hub-parity görünümü için defringe + canlılık + CLAHE yerel kontrastı ekler; bu işlem, kare başına işleme maliyetinin yaklaşık 2 katıdır, dolayısıyla **canlı** kare hızı daha düşüktür — kaydedilen görüntüler her durumda tam işlemeyi alır. Yalnızca RGB/Bayer kameralar için; mono M3M'de atlanır. |
| `lattice color [--saturation 0-200] [--contrast 0-200] [--reset] [--get]` | Canlı önizleme doygunluğu/kontrastı (RGB filtreli kameralar); `100` nötrdür, `--reset` her ikisini de 100'e ayarlar. Kaydedilen görüntüler bundan etkilenmez. Mono M3M'de atlanır. |
| `lattice highlights [{--set -100..0 \| --reset \| --get}]` (bayrak yok = okuma) | **Vurgulu alanları** göster (RGB filtreli kameralar): en parlak, en beyaz pikselleri aşağı çekin — örneğin, düz beyaz bir leke olarak görüntülenen parlak beyaz bir yüzey. `0` = kapalı, `-100` = en güçlü. Canlı önizleme ve görüntüleme düzeyindeki çekimler bu ayara uyar; parlaklık/yansıtma değeri asla uymaz ve kırpılmış sensör verilerini kurtaramaz (bunun için pozlamayı azaltın). Mono M3M'de atlanır. |
| `lattice filter [--list]` | Chloros'un bildiği filtre modellerini listeler (`RGN-IMX265`, `OCN`, `NGB`, …). Bir kameranın filtresi model dizesinden okunur ve ayarlanamaz: `--set NAME` yalnızca bir modeli arar, ardından `1` ile `setting the filter model is not supported`'ten çıkar. |
| `lattice power [--sleep]` | Güç/termal düğümleri kontrol eder; düşük güçte bekleme modunu etkinleştirir/devre dışı bırakır. `--sleep`, kamerayı uyku modunda bırakır (bağlantı verim sınırı etkinleştirilir (`DeviceLinkThroughputLimitMode` `On`) en düşük seviyede tutulur, kamerada varsa güç tasarrufu modu etkinleştirilir) diğer tüm ayarlar ilk bulundukları gibi geri yüklenir; başarısız olan bir uyku modu, tamamen geri yüklenir. Uyanma işlemi, bağlantıyı uyku modunda bulunduğu haliyle geri getirir (sınır, modu ve paket gecikmesi `GevSCPD`; sınır açıkken kamera bunu 0 olarak tutar; kayıt bu bilgisayarda tutulur, böylece arka uç yeniden başlatıldıktan sonra gerçekleşen bir uyanma işlemi de bunu bulur), ya da bu bilgisayarın uyku moduna almadığı ancak uyku modunda gibi görünen bir kamera için (sınırı taban seviyesinde), sınır devre dışı bırakılır ve `GevSCPD` değeri 80'de 0 olarak bırakılır (fabrika ayarları); taban değerinin üzerine ayarlanmış bir sınır olduğu gibi bırakılır. Prob ve uyandırma işlemi, açık durumdayken yazdıklarını geri yükler. |

> **Binning her zaman Ortalama’dır.** `lattice resolution --binning N`, Ortalama binning’i ayarlar ve
> mod değiştirme seçeneği sunmaz. Toplam (Sum) birleştirme, her DN değerini N² ile çarpar ve her birleştirme adımı başına yaklaşık iki stop
> parlaklık marjına mal olur; Chloros, bu faktörü
> radyometrik zincirden önce tekrar böler, ancak Toplam (Sum) modu nedeniyle sensörün tavan değerine ulaşan pikseller kırpılır ve
> kurtarılamaz. Ortalama'nın sağlamadığı hiçbir avantajı sağlamaz — bir indeks ölçekten bağımsızdır ve parlaklık
> skalerleri Ortalama ölçeğinde kalibre edilmiştir. Bir kamera zaten Toplam moduna ayarlanmış olarak gelirse (başka
> bir araç tarafından veya fabrika ayarlarına sıfırlanarak bu şekilde bırakılmışsa), Chloros bunu bir düğüm geri okumasından algılar ve
> telafi eder; `lattice resolution --binning N` komutunun çalıştırılması ayarı tekrar Average'a getirir. 1.2.0
> `--binning-mode` bayrağı hâlâ kabul edildiğinden eski komut dosyaları çalışır, ancak yalnızca bir uyarı yazdırılır:
> birleştirme Ortalama olarak kalır.

> **Kamerada binning, piksel formatı ve tetikleyiciyi değiştirseniz de hiçbir ayar kalıcı olmaz.** Kamerada
> arka uç açık kalır ve akış yapar (`cam-connect`, GUI), `lattice resolution --binning`
> geçerli olmaz (hala geçerli olan binning'i gösterir, `Binning: 1x1`) ve
> `lattice format --set` ile `lattice trigger --mode` komutları kamera tarafından reddedilir
> (`SC_ERR_ERROR -1001`). Önce serbest bırakın (`lattice cam-disconnect -s <SN>`) veya
> bağlantı kurulurken ayarlayın (`cam-connect --pixel-format …`, `array-connect --binning …`).

##### Otomatik modda pozlama ve kazanç okuma

Bir kameranın `ExposureAuto` (`GainAuto`) çalışırken, `ExposureTime` (`Gain`) düğümü, kameranın şu anda yaptığı işlemi değil, **en son yazılan değeri** tutar. Arka uç tarafından açık tutulan bir kamera için (`cam-connect`, `array-connect`, GUI), `lattice configure --get`, varsayılan `lattice configure` görünümü ve `lattice exposure` (bayrak yok) bu nedenle düğüm değerini ve bunun altında kameranın en yeni karesini içeren bir notu yazdırır:

```

ExposureTime = 2000.448
  note: ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448); the camera's newest frame was at 4900 us.
```

Nottan kare değerini okuyun; düğüm değeri yalnızca son yazılan değerdir. HTTP üzerinden aynı `POST /api/camera/<serial>/read-nodes` yanıtı, `nodes` değerini değiştirmez ve `frame_values` (`{"ExposureTime": <us>, "Gain": <dB>}`, en yeni karenin) ve `notes` (yukarıdaki cümle) ekler. Her ikisi de yalnızca otomatik mod `Off` değilken ve bir kare biliniyorken mevcuttur; sadece okuma için açılan bir kamerada kare yoktur ve ikisi de alınmaz. `lattice gain` (bayrak yok) yalnızca düğümü yazdırır — not için `lattice configure --get Gain`'i kullanın.

##### Dizi elemanında pozlama

Bir dizi akış yaparken, her eleman **`AcquisitionFrameRateEnable`**ile çalışır; bu, dizinin tetikleme hızının biraz üzerinde bir kare hızındadır: periyodu, tetikleme periyodundan %15 eksiltilmiş (en az 3 ms) veya tetikleme hızı zaten buna yakınsa kameranın kendi maksimum hızıdır. Bu nedenle, bir eleman üzerindeki**`configure --get`**, bu ayarda**`AcquisitionFrameRateEnable`**'i**`True`**ve**`AcquisitionFrameRate`**olarak okur; dizi durduğunda etkinleştirme kapalı konuma geri döner durduğunda devre dışı kalır.**Bir dizi elemanına bu iki düğümü yazmayın** — dizi, bunları tetikleyicisiyle senkronize tutar ve yeniden yazar.

Bu ayar aynı zamanda elemanın pozlama üst sınırıdır: 10 fps tetiklemede (100 ms periyot) yaklaşık 85 ms, **herhangi bir ROI veya binning** durumunda. 1.3.0 sürümünden önce, küçük bir ROI, dizi ne kadar yavaş tetiklenirse tetiklensin, bir elemanın pozlama süresini sensörün serbest çalışma periyoduyla sınırlıyordu (768 × 304'te 4,9 ms).**Üst sınıftan daha uzun bir manuel `ExposureTime` değeri, `lattice exposure --set` veya `configure --set` ile (veya GUI'den) yazıldığında, tetikleme hızını bu değere uyacak şekilde yavaşlatır**. Dizi önce o üyenin tavan değerini açar ve daha yavaş hızın birkaç tetikleme periyodu boyunca çalışmasına izin verir; bu nedenle yazma işlemi birkaç saniye daha uzun sürebilir; ardından tetikleme, pozlamanın izin verdiği hızdan daha hızlı gerçekleşmez. Otomatik hız dizisinde, manuel pozlama nedeniyle yavaşlatılan her üye otomatik pozlamaya geri döndüğünde, dizinin önceki hızı yeniden devreye girer. Kameranın kendisinin çekemeyeceği bir değer yine reddedilir; hatada kameranın kabul ettiği aralık belirtilir ve üyenin otomatik pozlaması, yalnızca o yazma işlemi için Kapalı konuma getirilmişse yeniden etkinleştirilir. `apply` pozlamalı bir reçete, tetiği yavaşlatmaz: üst sınıra sabitlenir. Chloros, daha hızlı bir tetik hızına uymak için manuel pozlamayı asla kısaltmaz; bir pozlamanın izin verdiğinden daha hızlı sabitlenmiş bir hız (`array-connect --target-fps F`), o üyeye bunun için yeterince uzun bir tavan bırakır ve o üye, pozlamasının izin verdiğinden daha hızlı tetik almaz.

#### Kalibrasyon ve Sensörler

| Alt komut | Amaç |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Yansıma hedefinden kalibrasyon yapar. Bunun için en fazla `--attempts` kare (varsayılan `30`) aranır; eksik bir çerçeve (GVSP paket kaybı) bir deneme hakkını tüketir, çalışmayı değil. `no target detected in N attempts` değerinde başarısız olur, ya da hiçbir çerçeve tam olarak ulaşmadığında `no complete frame in N attempts: <last error>` değerinde başarısız olur. |
| `lattice dls {--connect \| --spectrum \| --irradiance} --mac MAC [--filter NAME] [--camera SERIAL] [--json]` | Bir DAQ-M'nin spektrumu ve bant büyüklükleri, arka uç üzerinden Bluetooth (BLE) ile okunur. `--mac`, sensörün kendi BLE MAC adresidir ve aşağıdaki üç eylemden herhangi biri için gereklidir: her birimin kendine ait bir adresi olduğundan varsayılan bir değer yoktur (`daq pool-discover --only ble` bunları listeler); bu değer olmadan komut, `1` ile sonlanır. Herhangi bir eylem belirtilmezse yardım mesajını görüntüler. `--camera SERIAL`, filtre modelinin filo eğrisi yerine o kameranın kendi ölçülen spektral yanıtı aracılığıyla görüntülenen bant büyüklüklerini entegre eder — RGB kameralar için, kameranın kalibrasyon paketi bu bilgisayara yüklenmiş (bağlanmış) veya önbelleğe alınmışsa. Başlık, kimin eğrisinin kullanıldığını belirtir: `Band magnitudes (camera <SERIAL>):` veya kamerada eğri bulunmadığında (RGB kamera değilse, ya da yüklü veya önbelleğe alınmış bir paket yoksa) ve bunun yerine filo eğrisi kullanıldığında `Band magnitudes (model <filter>):` yazılır. HTTP üzerinden, `POST /api/dls/cli-read`, `camera_serial`'i alır ve `band_magnitudes_source`'i döndürür. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Mevcut görüntülere, her birinin kendi lensinin düz alan (EXIF Modelinden). `--lens-model`, Model adında kamera belirtilmeyen görüntülere uygulanır; Model adıyla çelişen bir görüntü atlanır. |

#### Çoklu Kamera (Geçici Oturumlar)

Geçici bir komut, kameraları tek bir çalıştırma için açar ve bittiğinde kapatır: Kalıcı bir dizi bağlı olmayan `multi-capture`, buradaki `multi-stream` ve `multi-test`, ve aşağıda `align-calibrate`, `align-apply`, `align-stream` ve `index --live`. Aşağıdaki kurallar hepsi için geçerlidir:

- **`--serials SN1,SN2,…` kameraları seçer.** Yalnızca bunlar açılır ve ilki tetiği tetikler (`align-calibrate` ve `--reference` verildiğinde). Ağda bulunmayan bir seri numarası, herhangi bir kamera açılmadan önce çalıştırmayı başarısız kılar. `--serials` olmadan, ağdaki tüm kameralar açılır. Chloros'un halihazırda elinde tuttuğu bir kamera (GUI, `cam-connect`, `array-connect`), `--serials` tarafından adlandırılsa da adlandırılmasa da çalışmayı reddeder: çalıştırmanın ayarlandığı ağı paylaşır. (`--serials` olmadan çalıştırılan `index --live`, bunun yerine profilinde etkinleştirilmiş kameraları açar: bkz. [Dizin / Bitki Örtüsü Matematiği](#index--vegetation-maths).)
- **Kameralar, `array-connect`'in paylaştığı şekilde ağı paylaşır.** Her kamera, ana bağlantının taşıdığı verinin kendisine düşen payına göre hızlandırılır (bu, paketler arası gecikmedir; kameranın veri aktarım hızı sınırı değildir; 1 GbE üzerindeki dört kamera, 31,25 MB/s'lik alt sınırı zaten aşmaktadır), böylece dört kamera 1 Gbps bağlantı üzerinde çalışır. Kameraların ulaştıkları en düşük hızda hepsi aynı anda veri gönderemediği durumlarda, her biri kendine ait bağlantıyla sırayla tetiklenir, ve komut `Serial emit: the wire cannot carry these cameras at once, so each is triggered <ms> ms after the one before; their frames are not exposed together` çıktısını verir. Bir seferde tek bir kamera bile sığmadığında, herhangi bir kamera akışına başlamadan önce işlem reddedilir: `array over-subscribes the wire: <n> cameras demand <x> MB/s but the collision-safe ceiling is <y> MB/s. …`, HTTP 409, çıkış `1`.
- **Ya tüm kameralar veri gönderir ya da işlem başarısız olur.** Hiç kare göndermeyen bir kamera bir hatadır: komut, `Error: … no frame from <n> of <total> cameras: SN:<serial> (<why>), …` mesajını görüntüler; burada `<why>`, o kameranın veri alma hatasıdır, ardından hatanın nedenleri belirtilir ve işlem `1` ile sonlandırılır. GVSP paket kaybıyla gelen bir kare (`IncompleteBufferError: GVSP packet loss …`), ağın kameraların toplam bant genişliğini (`Every missing frame arrived with GVSP packet loss: …`) taşıyamadığı anlamına gelir: ana bilgisayarın ve anahtarın yukarı bağlantı hızını kontrol edin (`lattice network`), jumbo çerçeveler kullanın, veya `--serials` ile daha az kamera açın. Hiçbir şey iletmeyen bir kamera genellikle ana kameranın tetik sinyalini almamaktadır: M8 senkronizasyon kablosunu kontrol edin, ardından `lattice multi-detect` komutunu çalıştırın. Bir kamera eksik olan çerçeve grubu için her komutun ne yaptığı, ilgili satırda belirtilmiştir.
- **Her kamera bulunduğu gibi geri yerleştirilir.** İşlem sona erdiğinde, her kamera işlem başladığında sahip olduğu duruma geri döner: `TriggerMode`, `TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, ve `LineMode` / `LineSource`, `Line2` ve `Line3` üzerinde (ve bir dizinin tetikleyici girişi, bir bağımlı hattın parazitini giderir: `LineFilterSelector` / `LineFilterWidth`); bununla birlikte `PtpEnable` (ve bununla birlikte ayarlanan `PtpSlaveOnly` / `PtpDomainNumber`); `ExposureAuto` / `GainAuto` (ve manuel değer olarak ayarlanmış olan `ExposureTime` / `Gain`); `TargetBrightness`, `ExposureAutoLimitAuto` / `ExposureAutoUpperLimit` / `ExposureAutoLowerLimit`, `GainAutoUpperLimit`, `ExposureAutoDamping`; `BalanceWhiteAuto` (ve beyaz dengesi manuel ayarlanmış olan her bir `BalanceRatio`); `BlackLevelAuto` / `BlackLevel`; `PixelFormat`, binning, `Width` / `Height` / `OffsetX` / `OffsetY`; `GammaEnable` (ve gama ayarının açık olduğu `Gamma`), `LUTEnable`, `ColorTransformationEnable`; `ReverseX` / `ReverseY`; `UserSetSelector`; paket boyutu (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`); hız ayarı, `DeviceLinkThroughputLimitMode` (ve modun `On` olduğu durumlarda `DeviceLinkThroughputLimit`),paketler arası gecikme `GevSCPD` ve başlangıç kayması `GevSCFTD`; bir dizi, her kameraya; her akışın etkinleştirdiği yığın verilerini (`ChunkModeActive`, her bir `ChunkEnable` kendi `ChunkSelector`'i altında); `AcquisitionFrameRateEnable` (ve `AcquisitionFrameRate` etkinleştirme açıkken — kapalıyken hız salt okunurdur ve hız ayarının izin verdiği en yüksek değeri okur, bu nedenle bir ayar değildir). Yalnızca farklı olan kısımlar yazılır; kamera önce durur, ardından çerçeve periyoduna ihtiyaç duyan bir pozlamadan önceki pacing ve kare hızı (bir kamera, `ExposureTime`'i bir çerçeve periyoduyla sınırlar). `--keep-settings` ile tetikleyici ve senkronizasyon hattı kabloları geri çekilir, kamera serbest çalışır ve geri kalan kısım, çalışma ayarlarına göre kameranın kendi belleğine kaydedilir; `--clear-settings` ise buraya fabrika ayarlarını yükler.
- **Pozlama, ilk kare kaydedilmeden önceki sahneden ayarlanır.** Çalışma, senkronize kare gruplarını alır ve bunları atarken her kameranın pozlamasını ve kazancını bu karelerden ayarlar. Amaç, kameranın hedefinde, karenin %1'inden fazlası kırpılmadan ortalama bir parlaklık elde etmektir; parlak vurgu noktaları olan bir sahne, hedefe ulaşamadan durur. Bu işlem genellikle 2-6 kare grubu sürer ve en fazla 5 saniye sürer.
  - Her kamera, bağlı bir dizinin başlangıç noktası olan hedefi, kendi filtresine göre hedefler: Multispektral bir kamerada (RGN, OCN, NGB) TargetBrightness **40**ve RGB bir kamerada**60**. Bu, 80'in verdiği pozlama hedefinin yarısıdır (RGB'de dörtte üçü), dolayısıyla parlak bir yansıma panelinin kırpılma olasılığı daha düşüktür. `--brightness N` (0-255) ise her kameranın hedef değerini ayrı ayrı belirler (`multi-test`'te `--brightness` yoktur: otomatik pozlama özelliği kapalıdır). Arka uç, her kameranın hedef değerini bir adla kaydeder: `[transient-ae] AE target per camera (filter default): SN:<serial> <target>, …`.
  - Pozlama önceliklidir ve kameranın kendi otomatik pozlama sınırına kadar geçerlidir. Kazanç, yalnızca kalan kısmı kapsar.
  - Belirlenen pozlama ve kazanç değerleri işlem süresince korunur; bu nedenle, bir `multi-stream`'in her karesi aynı pozlamada çekilir. Değişen ışık koşullarını takip etmesi gereken bir çekim, otomatik pozlaması sürekli ayarlanan bağlı bir diziye (`array-connect`) aittir, çünkü bu dizide otomatik pozlama sürekli olarak ayarlanır.
  - `--exposure` ve `--gain`, tam olarak verildiği gibi korunur. Her ikisi de verildiğinde, hiçbir kare atılmaz.
  - İşlem sona erdiğinde, her kameranın otomatik pozlaması yeniden etkinleştirilir.
  - Arka uç günlüğü sonucu bildirir: `[array-ae] exposure solved in <s> s (<n> warm-up groups): SN:<serial> <ms> ms <dB> dB mean <m>/<target>, …` veya nedeni içeren `exposure NOT solved after …` ve bu durumun geçerli olmadığı her kamera için `(not settled)`. Bu durumda kareler yine de çekilir.
  - `CHLOROS_TRANSIENT_AE_SETTLE_S`, 5 saniyelik sınırı değiştirir; `0` ise bu özelliği devre dışı bırakır (bkz. [Ortam Değişkenleri](#environment-variables)).

**Chloros herhangi bir kamerayı** (GUI, `cam-connect` veya bağlı bir dizi) tutarken, `multi-stream`, `multi-test`, `multi-detect`, `align-apply`, `align-stream` ve `index --live` komutları çalışmayı reddeder; çünkü bunlar ağdaki her kamerayı açar ve tutulan bir kameranın ikinci kez açılması tutulan bir kameranın ikinci kez açılması arka ucu çökertir. (`index --live`, açacağı kamera tutulmuşsa reddedilir: bu, `--serials` veya profilindeki kameralar olabilir; ayrıca, hiçbir kamera belirtilmemiş bir profile ait herhangi bir kamera da olabilir.) Arka uç, HTTP 409 `CAMERA_HELD` yanıtını verir; bu yanıtta, kilitli her kamera ve serbest bırakma komutları (`chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all`) listeler ve komut sonlandırılır (`1`). Bir dizi bağlandığında, `array-capture --continuous` bunu `multi-stream` yerine kaydeder ve `array-capture` (varsayılan olarak hizalanmış) komutu, `align-apply` yerine hizalanmış bir kümeyi kaydeder; `multi-capture` ve `align-calibrate` komutları, kameralarını açmak yerine bunları barındıran bağlı bir diziyi kullanır.

| Alt komut | Amaç |
| --- | --- |
| `lattice multi-info [--serials SN1,SN2,…]` | Senkronizasyon rolüne sahip tüm kameraları listeler; `--serials` ile yalnızca bunlar, `MASTER` ile ise ilk kameralar listelenir. |
| `lattice multi-capture [--serials SN1,SN2,…] [--format FMT] [--jpeg-quality N] [--processing LEVEL] [--brightness N] [--no-daq] [--daq-tolerance-ms MS]` | Her kameradan bir senkronize kare. Kalıcı bir dizi bağlıyken **varsayılan olarak tüm dışa aktarma türlerini**kaydeder ve kullanılan DAQ okumasını ([Bir yakalama işlemini okuyan DAQ neyi kaydeder](#the-daq-reading-a-capture-saves)); geçici dizi olmayan yedekleme ise**sadece debayering işleminden geçirilir** (geri kalanı için önce `array-connect`'i çalıştırın). `--brightness` yalnızca yedekleme için geçerlidir: bağlı bir dizi kendi otomatik pozlamasını korur. Yedekleme, `.daq`'i kaydetmez (`--no-daq` / `--daq-tolerance-ms` burada hiçbir etkiye sahip değildir) ve `multi-capture`, `--force-daq`'i almaz. Yedeklemede, kare göndermeyen bir kamera, yakalama işlemini başarısız kılar (`Error: multi-capture failed: no frame from …`, `1`'ten çık) ve o kare grubuna ait hiçbir dosya kaydedilmez. |
| `lattice multi-stream [--serials SN1,SN2,…] [--fps F] [--count N] [--format FMT] [--jpeg-quality N] [--brightness N]` | Akışla senkronize edilmiş kareler (geçici), `--fps` varsayılan `3`, Ctrl+C veya `--count` gruplarına kadar. Bir kameranın hiçbir şey iletmediği bir kare grubu yazılmaz, ve arka arkaya üç grup akışı sonlandırır. Herhangi bir grup atlanırsa, işlem `Incomplete: <n> frame group(s) not written, a camera delivered nothing (SN:<serial> x<count>, …)` yazdırılarak sona erer; ayrıca akışı sonlandıran kameranın adı `Error: the stream stopped: …` olarak belirtilir ve program `1`X ile sonlandırılır. Sürücü dolduğunda kendini durdurur, `Failed:` / `Disk full:` mesajlarını yazdırır ve `1` ile sonlandırılır; yazamadığı herhangi bir dosya da `1`'ten çıkar. |
| `lattice multi-test [--serials SN1,SN2,…] [--count N] [--ptp-settle SECONDS]` | GPIO senkronizasyon zamanlama testi (geçici), `--count` varsayılan `10` tetikleyicileri. Ağın nasıl paylaşıldığını yazdırır (`Wire: each camera paced to <x> MB/s (<n> on a <y> MB/s ceiling, <link> Mbps link)` ve kameralar arka arkaya tetiklendiğinde, zaman damgalarının zaman kaymasını içeren `Serial emit:` satırı), her senkronizasyon grubu için bir `Sync group: master SN:<serial> -> SN:<serial>, …` satırı (senkronizasyon kablosu olmayan ve kendi başına tetiklenen bir kamera için `(no slaves: not synced)`), ardından her tetikleme için bir satır: `[  0] SN:<serial>=<timestamp> \| …`; o tetikleme için hiçbir veri göndermeyen kamera için `SN:<serial>=(no frame)`. Ardından her kamera için birer `Offsets from master SN:<serial> (<PTP / device (PTP off)> clock, <n> trigger(s)):` ve bir `SN:<serial>: mean <±x> us, jitter <y> us` satırı; her kamera, bir saat döngüsü içinde ana kameraya göre: damgaları kameraların cihaz saatleri ile PTP (PTP kapalı veya hala kilitleniyor) dışarıda bırakılır; bunları `Note:` sayar. Kaçırılan herhangi bir kare testi başarısız kılar (`Error: multi-test failed: <k> of <n> frame groups incomplete (SN:<serial> missed <k>); …`, çıkış `1`); arka arkaya üç eksik tetikleyici testi erken sonlandırır. Ayrıca, her kameranın bir saatte senkronize olduğu tetikleyici sayısı ikiden az olduğunda (`1`) veya saatler hiçbir zaman uyuşmadığında da (`… the clocks cannot be compared`) test başarısız olur (çıkış `… PTP is not locked`); PTP saatleri, ana saatten 1 ms'den fazla (artı seri katman kayması) sapma gösterirse (`… PTP is not locked`) veya cihaz saatlerinin ofseti, tetiklemeler arasında bu değerden daha fazla değişirse. PTP grandmaster çalışmıyorsa onu başlatır, her kameranın PTP'sini etkinleştirir (ağın durumu ne olursa olsun, `array-connect`'in yaptığı gibi) ve ilk tetiklemeden önce her kameranın PTP saatinin grandmaster'a kilitlenip sabitlenmesini bekler: `PtpStatus` Bir grandmaster üzerinde Slave, grandmaster'a 100 us mesafede ve 3 saniye boyunca sabit, ve her kamera bu şekilde, kesintisiz olarak 120 saniye boyunca — güç açıldıktan yaklaşık 2 dakika sonra. Kilitlenmiş bir saat, Slave değerini okuduktan sonra yaklaşık 2 dakika boyunca ~200 us'ye kadar sapma gösterir ve kameranın kendi `PtpOffsetFromMaster` değeri bunu göstermez; 120 saniye sonunda kameralar, sabitlenmiş bir saate ~10 us'ye kadar yaklaşır. Bir kamera bu arka uçun algıladığı üzere, PTP'yi açık bırakan önceki bir testten beri hala kilitli durumdadır; o kilitlenme anından itibaren saymaya devam eder; bu nedenle arka arkaya yapılan testlerde tekrar bekleme olmaz; test sırasında açılan bir kamera veya bu arka uç tarafından kilitlendiği görülmemiş, PTP açık halde bulunan bir kamera (başka bir yerde açılmış ya da arka uç yeniden başlatılmadan önce açılmış), 120 saniye bekler ve her iki kameranın da kilitlenme süresini beklediği bir dizide yer alır. Slave durumundan çıkan, 100 us'yi geçen veya bekleme sırasında arka arkaya 3 okuma (1,5 s) boyunca başka bir grandmaster'ı takip eden bir kamera, sayımına yeniden başlar; bu tür bir veya iki okuma (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`) ve sayım devam eder; okunamayan bir grandmaster kimliği, değişiklik olarak değil, bilinmeyen olarak kabul edilir. Her kameranın sayımının aynı anda (bir saniye içinde) yeniden başlaması, kameraların değil, grandmaster'ın zamanının ilerlemesinden kaynaklanır: arka uç günlüğünde `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …` yazıyor, `ptp_lock` bunu `grandmaster_steps`'te listeler ve ardından süresi dolan bir bekleme, hatasını `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …` ile başlatır. Bu durum için sınır uzatılmaz. `--ptp-settle SECONDS`, çalıştırma için bekleme süresini ayarlar (0-600; `0` ise bekleme süresi olmadan). Bekleme süresi, yerleşme süresine 90 saniye eklenerek sınırlandırılır (varsayılan olarak 210 saniye, en az 90 saniye) ve komut, arka uçtan 240 saniye artı yerleşme süresi (varsayılan olarak 360 saniye) bekler. `PTP: locked after <s> s (each camera locked <settle> s, already locked: SN:<serial>, …; from the grandmaster: SN:<serial> <±x> us, …)` mesajını yazdırır ve arka ucun günlüğünde her 5 saniyede bir, her kameranın durumu ve ofseti, ne kadar süredir kilitli olduğu ve ne kadar süre kaldığı bilgilerini içeren bir satır bulunur: `[array-sync] PTP at <s> s: SN:<serial> Slave <±x> us for <s> s (<s> s left), … -- <s> s left, the wait gives up at <s> s`. Her kamera PTP kapalıyken açılır; bu noktadan sonra saatler yaklaşık 10 saniye içinde kilitlenir. Ardından her kamera, test sırasında tespit edildiği duruma geri getirilir — PTP'si, otomatik pozlama ve kazanç ayarları test süresince Kapalı tutulur; geri kalan ayarlar (`--keep-settings` durumu hariç) — yani PTP açık bırakılan bir kamera (`lattice configure -s <serial> --set PtpEnable=True` veya `--keep-settings` ile yapılan bir çalıştırma) kilitli kalır ve bu arka uç, kameranın yerleşme süresi boyunca kilitli olduğunu gördüğünde, sonraki testler onu beklemez. Saatler bekleme süresince sabitlenmezse, tetiklenmeden önce hata verir (`PTP: NOT locked …`, çıkış `1`) ve ne yapılması gerektiğini belirtir: hiçbir zaman kilitlenmemiş bir kamera (`PtpStatus Listening`), grandmaster'ın ona ulaşamadığı anlamına gelir (`time-sync status`); sürekli bağlantısı kopan bir kamera, ne kadar süreyle kilitli kaldığına göre adlandırılır (`… had not been locked for the 120 s settle time … (SN:<serial> locked <s> s, <s> s to go)`); saatler hala uyum sağlıyor: `time-sync status`'i kontrol edin, ve testten önce kilitlenmelerini sağlamak için PTP'yi açın ve açık bırakın (her kamera için `lattice configure -s <serial> --set PtpEnable=True`, veya testi `--keep-settings` ile çalıştırın; test bulduklarını geri yükler, bu yüzden açık kalır), ardından testi çalıştırın. |
| `lattice multi-detect [--line LINE] [--json]` | M8 senkronizasyon hattındaki kameraları ve varsayılan ana kamerayı bulun: kablolu ise RGB kamera (modeli `-FRGB` ile biten), aksi takdirde seri numarasına göre ilk kablolu kamera. M8 kabloları tek bir bağlantı noktasında birleşir, bu nedenle **kablolanmış herhangi bir kamera ana kamera olabilir** — üzerinde yazılı olan `MASTER` varsayılan bir değerdir, kablolama durumunu yansıtmaz. Her kamera sırayla hattı çalıştırır ve diğer kameralarda sinyal olup olmadığı kontrol edilir; kablolu grupta zaten bulunmuş bir kamera tekrar taranmaz, bu nedenle tamamen kablolu bir dizi bir tur sürer. `--line` yoksa, `Line2`'i tarar, ardından `Line3`'i tarar. `MASTER: SN:…`'i, diğer her kablolanmış kamera için birer tane `SLAVE: SN:…` ve `(probed on <line>)`'i yazdırır; `--json` tam sonucu ekler (`master`, `slaves`, `no_cable` — darbenin ulaşmadığı kameralar — `line` ve kamera başına `results`). İşlem bittiğinde, her kamera sahip olduğu tetikleme ve senkronizasyon hattı ayarlarına geri döner (yukarıdakilerle aynı düğümler). |

#### Hizalama

| Alt komut | Amaç |
| --- | --- |
| `lattice align-calibrate [--serials SN1,SN2,…] [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N] [--brightness N]` — artı dedektör/eşleştirici düğmeleri `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC ayar düğmeleri `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, çoklu kare birleştirme `[--averaging mean\|median\|inlier_weighted]`, geometrik kısıtlamalar `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, uzamsal kısıtlama `[--roi X0,Y0,X1,Y1] [--mask PATH]` ve-slave geçersiz kılmaları `[--per-cam-override SN:KEY=VALUE]` (tekrar edilebilir) | Canlı kameralardan hizalama profilini hesaplayın. Varsayılanlar: `--method orb`, `--model rigid`, `--frames 3`, `--save alignment_profile.json` (komutu çalıştırdığınız klasörde), `--max-features 2000`, `--ratio-threshold 0.75`, `--matcher bf`, `--knn-k 2`, `--ransac-threshold-px 3.0`, `--ransac-iters 2000`, `--ransac-confidence 0.99`, `--averaging mean`. (`project align calibrate`'in kendi varsayılan ayarları vardır; bkz. [tablosu](#project-align-calibrate-options).) `--prefilter`'in varsayılan ayarı `gradient` (kenar haritası; GUI/dizi hizalayıcıyla uyumludur — kenarlar spektral bantlar arasında korunur). `--matcher flann`, ~5000'den fazla özellik olduğunda verimli sonuç verir; `--averaging median`, tek bir hatalı yakalamaya karşı dayanıklıdır; `inlier_weighted`, eşleşme sayısına göre ağırlıklandırır; `--lock-scale`, en yakın dönme açısına (ölçek olmadan) yansıtır; `--lock-axis` bir öteleme bileşenini sıfırlar; `--mask` her kameraya uygulanır (kamera başına ayarlar için `--per-cam-override`'i kullanın, örn. `--per-cam-override 214701292:method=phase`). `--rms-threshold-px`, yeniden projeksiyon RMS değeri eşik değerini aşan bir kalibrasyonu kaydetmeyi reddeder. `--method manual`, noktalarını `--points PATH`'ten alır: JSON nesnesi olan `{"<serial>": [[x, y], ...], ...}`, her kameranın aynı özelliklere ait piksel koordinatlarını verir; referans kamera da buna dahildir (kamera başına en az 2); `--frames`'in her biri bu noktalardan hesaplanır. Kameralar birbirine bağlı bir dizide tutulduğunda, kalibrasyon bu dizinin canlı karelerini kullanır ve profil de dizinin çekimleri için hizalaması haline gelir. Aksi takdirde bir [geçici dizi](#multi-camera-transient-sessions) açar: `--frames`'lerin hiçbirinde kare göndermeyen bir kamera kalibrasyonu geçemez (`1`'ten çıkılır ve ona bir ad verilir) ve hiçbir profil kaydedilmez. |
| `lattice align-apply --profile PATH [--serials SN1,SN2,…] [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N] [--brightness N]` | Hizalanmış bir çok bantlı kare yakalar. `--bit-depth` varsayılan olarak kamerayı eşleştirir; `--no-crop` tam kareyi korur (siyahla doldurur); `--interpolation` (varsayılan `linear`) ve `--border-mode`/`--border-value` (varsayılan `constant`/0) CPU warp'ını kontrol eder — GPU yolu her durumda bilineer olur. Bir [geçici dizi] açar(#multi-camera-transient-sessions): kare göndermeyen bir kamera, yakalama işlemini başarısız kılar (`1`'ten çıkarak ona bir ad verir) ve hiçbir şey yazılmaz. |
| `lattice align-stream --profile PATH [--serials SN1,SN2,…] [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N] [--brightness N]` | Akışa hizalanmış çok bantlı kareler (`align-apply` ile aynı warp ayarları), `--fps` varsayılan `3`. Bir [geçici dizi] açar (#multi-camera-transient-sessions): bir kameranın hiçbir şey göndermediği bir kare grubu kaydedilmez ve arka arkaya üç tane olması akışı sonlandırır. Herhangi bir grup atlanırsa, işlem `Incomplete: <n> frame group(s) not saved, a camera delivered nothing (SN:<serial> x<count>, …)` ve `Error: …` mesajlarını yazdırarak sona erer ve `1` ile sonlanır. Başka herhangi bir hatayla sonlanan bir akış da `Error: …` mesajını yazdırır ve `1` ile sonlanır. |
| `lattice align-info --profile PATH [--json]` | Profil ayrıntılarını gösterir. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Katman sıralamasını değiştirir. |

#### Dizin / Bitki Örtüsü Matematiği

```bash
# Offline: compute NDVI from an aligned multi-band TIFF (an RGN camera's bands)
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: open the cameras, capture one aligned set with a profile made by
# `lattice align-calibrate`, and compute the index from it
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --channel red=Red_660_RGN --channel nir=NIR_850_RGN \
  --save-multiband -o output/
```

**`--channel`, bir ön ayarın sembollerini bantlara eşler** (NDVI: `red`, `nir`), her biri için birer `--channel SYMBOL=BAND`.

- **`--live`: yalnızca yığın tarafından yanıtlanamayan bir sembol için kullanılır.** `--input`'te olduğu gibi, dışarıda bıraktığınız bir ön ayarın sembolü, yığının (profilin etkin kameralarının bantları) adı onunla başlayan tek bandını alır. Bu, herhangi bir kamera açılmadan önce kontrol edilir: hiçbir bantın veya birkaç bandın yanıt vermediği bir sembol reddedilir ve bu sembollerin adları belirtilerek `1`'ten çıkılır. Özel bir `--formula`, her sembol için bir `--channel` gerektirir.
- **`--live` ürünleri, kime ait olduklarını belirtir.** `index.tif` ve `index_LUT.png`, yığının kamerasını (Marka, Model, seri numarası), çekim zamanını (referans kameranın pozlama süresi, UTC) ve lens bilgilerini taşır; her ikisi de indeksi tek bir bant olarak adlandırır (`XMP-Camera:BandName` `NDVI`; renkli PNG dosyası, renk kanallarını değil indeksi adlandırır), tıpkı bir `--input` ürününün yaptığı gibi. Birkaç kameranın bantlarından oluşan bir dizin ise her birini ayrı ayrı adlandırır ve bu durumda `Model`, `LATTICE` olur — bkz. [Birkaç kameradan oluşturulan dosyalar](#files-made-from-several-cameras).
- **`--input`: Yalnızca dosyanın yanıt veremediği bir sembol için kullanılır.** Dışarıda bıraktığınız bir sembol, dosyanın adı o sembolle başlayan tek bandını alır: `red`, `Red_660` veya `Red_660_RGN`'i alır; `nir` ise `NIR_850`'i alır. Adlar dosyanın kendisinden gelir: yığının kendi bant listesinden veya Chloros radyans / yansıma yakalama veya `process` ürününde, `Chloros:Band{R,G,B}Name` / `XMP-Camera:BandName` etiketlerinden gelir. Dolayısıyla `lattice index --input <reflectance capture> --preset NDVI` için `--channel`'e gerek yoktur.
- **Bir sembole karşılık gelen iki bant arasında tahminde bulunulmaz** (9 kameralı bir yığın iki NIR bandına sahiptir): buna bir isim verin. Hiçbir şeye karşılık gelmeyen bir sembol, `channel_map missing entries` ile reddedilir, `1`'ten çıkın.**Bir yığındaki bant adları** kameranın filtresini taşır: bir M3C kameranın bantları `<band>_<filter>` — `NIR_850_RGN`, `Green_550_RGN`, RGN'de `Red_660_RGN`; `NIR_808_OCN`, `Cyan_490_OCN`, OCN'de `Orange_615_OCN`; RGB'de `Blue_475_RGB`, `Green_550_RGB`, `Red_625_RGB`; NGB'de `Blue_475_NGB`, `Green_550_NGB`, `NIR_850_NGB` bir NGB'de — ve bir M3M kameranıntek bandı `Mono_<filter>`'tir (`Mono_660`, `Mono_850`). `lattice align-info --profile PATH`, bir profilin bantlarını listeler.

Tam bayrak kümesi: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI/NDRE/EVI/SAVI/GNDVI/…), `--formula EXPR`, `--channel SYM=BAND` (tekrarlanabilir), `--capture-level raw|debayered|radiance|reflectance|unknown` (kaynak TIFF dosyasında kaydedilen yakalama seviyesini geçersiz kılar; varsayılan: TIFF meta verilerinden okunur, Chloros dosyasının `Chloros:ProcessingLevel` değerine geri dönülür, böylece bir yansıma yakalama işlemi `reflectance` değerini bildirir), `--output PATH` / `-o` (çevrimdışı: bir yol; bir görüntü uzantısı varsa dosyalar, kökünden — `-o ndvi_FRGN.png`, `ndvi_FRGN.tif` ve `ndvi_FRGN_LUT.png` olarak yazar — ve uzantı yoksa bir önek olur, `-o ndvi` -> `ndvi.tif`, `ndvi_LUT.png`; girişin yanında varsayılan olarak, `<input name>_<preset>`; `--live` varsa ve `-o` yoksa, komutu çalıştırdığınız klasörün altında bir `output/` klasörü bulunur), `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. `--live` ile hizalama çarpıtma düğmeleri de geçerlidir: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`, `--brightness N` (kameraların otomatik pozlama hedefi, geçici kurallara bakınız), `--serials SN1,SN2,…`. `--live` bir [geçici dizi] açar(#multi-camera-transient-sessions): kare göndermeyen bir kamera, çalışmayı başarısız kılar (`1`'ten çıkılır ve ona bir ad verilir)ve yığına veya dizine hiçbir şey yazılmaz. `--serials`'i açar (ilki tetiği ateşler), aksi takdirde `layer_order` girişi etkinleştirilmiş olan profilin kameraları, önce referans olarak — diğerleri buna hizalandığı için bantları devre dışı olsa bile referans ve tetiği tetikler. Bantları devre dışı bırakılmış bir kamera asla kalibre edilmez, bu nedenle profilde devre dışı bırakılmış bir RGB kamera artık yansıma indeksinde (`no-absolute-radiance-scale`) hata vermez. Hiçbir kamerayı adlandırmayan bir profil, eskisi gibi ağdaki tüm kameraları açar.

Her form arka uçta çalışır, bu nedenle arka ucun çalışıyor olması gerekir: Chloros GUI'sinden başlatın ya da CLI, arka uca ulaşamadığında görüntülediği komutla başlatın. Çevrimdışı modda, arka uç `--input` dosyasını okur ve float TIFF ile renklendirilmiş PNG dosyalarını kendisi yazar; bu nedenle, komutu çalıştırdığınız klasöre göre göreceli bir `--input` veya `-o`, komutu çalıştırdığınız klasöre göre çözümlenir (bkz. [Kural ve Kısaltmalar](#conventions)). Her iki çevrimdışı ürün de, bir `process` dizin dışa aktarımında olduğu gibi, girdinin kamera kayıt bilgilerini içerir: Marka / Model / seri numarası, GPS, lens etiketleri, çekim zamanı (girdinin kendi çekim zamanı; EXIF'te bu bilgi yoksa, kaydedilen pozlama anı veya dosya adındaki zaman damgası, UTC cinsinden) ve MAPIR kalibrasyon kimliği — dizin için geçerli olmayan ham-DN kalibrasyonu asla dahil edilmez — ayrıca `Chloros:ProcessingLevel=index` ve dizin ifadesi; float TIFF, bir bant olan ön ayarı belirtir. `--list-presets` ve `--list-gradients`, arka ucun ön ayar ve gradyan tablolarını yazdırır. Arka uç tarafından reddedilen bir formül veya TIFF'te bulunmayan bir `--channel` bandı, arka ucun gerekçesiyle birlikte `1`'ten çıkar.

> **`--channel` sembollerinde büyük/küçük harf duyarlılığı vardır.** Sembol tarafı, ön ayarın kanal adlarıyla tam olarak eşleşmelidir (ön ayarlar küçük harf kullanır, ör. NDVI = `red`,`nir` — `--list-presets`'i kontrol edin)ve bant tarafı, hizalanmış yığındaki bir bant adıyla eşleşmelidir (veya çevrimdışı modda 0 tabanlı bir bant indeksi olmalıdır). `--channel red=Red_660_RGN --channel nir=NIR_850_RGN` çalışır; `--channel RED=660` ise bir `channel_map missing entries` hatasıyla başarısız olur.

#### `lattice index` — çok bantlı yığın maskeleri

`--capture-level radiance` veya `reflectance`'te her kameranın 3×3 ayrıştırma işlemi bir bandı 0'a sınırlayabilir değerine sınırlayabilir (bkz. [Unmix sınırlama maskesi](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on)). Çok bantlı bir yığın, birkaç kameranın bantlarını içerir ve her kamera kendi bandını sınırlar; bu nedenle `--live --save-multiband`, yığının yanına **kaynak kamera başına bir maske** yazar:

```
output/
├── index_multiband.tif
└── clamp_masks/
    ├── index_multiband_SN<serial A>-<filter A>_clampmask.tif
    └── index_multiband_SN<serial B>-<filter B>_clampmask.tif
```

- Her maske, **yığın**ın piksel ızgarası üzerinde (aynı hizalama, deforme etme ve kırpma işlemleriyle) tek bantlı bir `uint8` olup, o kameranın kendi bitlerini içerir: bit 0 = R kanalının bandı, bit 1 = G, bit 2 = B. Bir kameranın *c* kanalından alınan yığın bandı, o kameranın *c* bitinin (R = 0, G = 1, B = 2) ayarlandığı yerde sınırlandırılır.
- Her bandın gömülü köken bilgisi, kamerasının maskesinin adını belirtir (`unmix_clamp_mask`, yığının klasörüne göre, veya `none`) ve okunacak biti (`unmix_clamp_mask_bit`) belirtir.
- Unmix işlemi hiçbir şeyi sınırlamayan bir kamera için dosya oluşturulmaz; mono veya RGB kamera ise unmix işlemi yapmaz ve dosya da oluşturulmaz. CLI, yığının altındaki her maskeyi (`clamp mask SN…: …`) yazdırır; yanıt ise bunları `clamp_masks` olarak listeler.
- Aynı işlemin indeksli TIFF / PNG dosyası, aynı yığın üzerinden ve aynı ızgara üzerinde hesaplanır; dolayısıyla maskeler bunu da tanımlar. `--capture-level raw`, unmix işlemi yapmaz ve maske yazmaz.
- Yığın ve dizin doğrudan `--output` içinde yer alır; dolayısıyla maskeler, `--output`'in kendi `clamp_masks/` klasörüne gider — asla sizin adlandırdığınız klasörün üstüne yerleştirilmez. Bu klasörde farklı ürün türleri (yığın, indeks, renklendirilmiş render) bir arada bulunur: içindeki dosyaları seçin, klasörün tamamını fotogrametri yazılımına aktarmayın.

#### Kalıcı Bağlantılar (Smart-Prep, GUI'ye Eşdeğer Akış)

Bu komutlar, CLI çağrıları arasında arka uç havuzunda kameraları açık tutar.

| Alt komut | Amaç |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Havuza bir kamera ekler (tek kamera, dizi yok). [Ortak seçenekler](#common-options-shared-by-most-subcommands) `--exposure`, `--gain`, `--preset`, `--pixel-format`, `--width` / `--height` ve `--packet-size` (`standard` = 1500, `jumbo` = 9000 veya bayt; `auto` probları) bağlantı sırasında uygulanır (otomatik modu kapatan bir `--exposure` veya `--gain` anahtarı). Kameranın akışı başladığında geri döner (bunun için yaklaşık 15 saniye, ardından ilk tam tamponun gelmesi için 10 saniye bekler) ve akış hiçbir şey iletmediğinde `stream: streaming` veya `stream: no_frames -- connected, no frames yet: …` yazdırır — jumbo paketler kullanan bir kamera için, `--packet-size standard` ile yeniden bağlanmanız tavsiye edilir. Bir kamera bağlanırken (ilk bağlantı, kalibrasyonunu indirir, yaklaşık 70 saniye sürer), kameraya ihtiyaç duyan başka bir komut en fazla 5 saniye bekler, ardından HTTP 503 hatasıyla başarısız olur (`CAMERA_POOL_BUSY`, bağlanan kameranın adını belirtir); bir süre sonra komutu tekrar çalıştırın. `lattice status` ve kamera listesi asla beklemez: bağlanma aşamasında olan bir kamera, bulunduğu aşamayla birlikte "bağlanıyor" olarak gösterilir. |
| `lattice cam-disconnect [--serial SN] [--all] [--keep-settings \| --clear-settings]` | Serbest bırakma. Kamera, bağlanma sırasında bulunduğu duruma geri getirilir (tetikleyici kablolaması, PTP, ayarlar); `--keep-settings` ise ayarlarını kendi belleğine kaydeder, `--clear-settings` bu belleği fabrika ayarlarına sıfırlar (bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings)). Bir dizinin üyesi olan kamera, tüm diziyi serbest bırakır. |
| `lattice cam-list` | Havuzdaki kameraları listeler. |
| **`lattice array-connect`**|**Kalıcı senkronize bir diziye bağlanın (ÖNERİLEN giriş noktası).** Tam GUI akıllı hazırlık akışını çalıştırır. 9 kamera için yaklaşık bir dakika sürer; kameralar açıldıktan hemen sonra birkaç dakika sürebilir. Bekleme sırasında arka uçun ulaştığı her adımı `    <s> s: <step>` (örneğin `    41 s: open SN:214000535 (4 of 9)`) şeklinde yazdırır, ve arka ucun günlüğünde her adım için bir zamanlama satırı (`[array-connect <id>] <step>: <s> s …`) ile en yavaş adımların yer aldığı bir son satır bulunur. 600 saniyeye kadar bekler. Bu süre içinde tamamlanmayan bir bağlantı, arka uç tarafından iptal edilir: açtığı her kamera, bulunduğu ve kapatıldığı duruma geri getirilir, komut, ulaştığı adımı içeren `Error:` satırını yazdırır ve `1` satırında sonlanır; hiçbir dizi bağlanmamıştır (HTTP 504 `CLIENT_DEADLINE_PASSED`). Ctrl+C tuşları da aynı şekilde bağlantıyı iptal eder (`Interrupted: …`); dizi zaten bağlanmışsa, satırda bu durum belirtilir ve bağlantıyı sonlandıran komutun adı (`array-disconnect`) verilir. Bekleme sırasında sonlandırılan bir komut (`kill -9`), arka uç 30 saniye boyunca komuttan haber almazsa bağlantının geri alınması gerekir. Çalışır durumda olan ancak henüz senkronize edilmiş çerçeve grubu bulunmayan bir dizi, akışı `no_frames` olarak belirtilerek yine de bağlı olarak rapor edilir. |
| `lattice array-disconnect [--array-id ID] [--all] [--keep-settings \| --clear-settings]` | Bir diziyi serbest bırakır. Her kamera serbest çalışır durumda bırakılır (`TriggerMode` `Off`); diğer tüm ayarlar, bağlantı kurulduğunda olduğu gibi kalır — tetikleyici ve senkronizasyon hattı ayarları (ana`TriggerSource` eski haline, senkronizasyon hattı bir girişe geri döndürülür), PTP ve oturumun değiştirdiği kamera ayarları — böylece bir üyeye verilen tek kameralı komut anında çalışır. `--keep-settings`, her kameranınayarlarını oturumun bıraktığı haliyle kameranın kendi belleğine kaydeder; `--clear-settings` ise bu belleği fabrika ayarlarına sıfırlar. Bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings). |
| `lattice array-list` | Bağlı dizileri listeler. |
| `lattice array-status [--array-id ID]` | Canlı fps, PTP, son hata. `ptp_enabled`, yalnızca tüm üyeler PTP'yi açık olarak okuduğunda `True` olur; `ptp_off:` ise bunu yapmayan bir üyenin adını belirtir. `state:` satırı, ayarların yeniden başlatılması sırasında `restarting (applying settings)` değerini, güç kaybı yaşayan bir üye yeniden açılırken (aşağıdaki nota bakın) `recovering (…)` değerini, `stalled (…)`, yakalama döngüsü çalışırken ancak hiçbir çerçeve grubu yayınlamadığında ve `stopped (…)`, yakalama döngüsü pes ettiğinde (diziyi yeniden bağlayın). |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--orient] [--index\|--no-index] [--no-daq] [--daq-tolerance-ms MS] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Canlı diziden bir senkronize yakalama — Tekli / Sürekli / Aralık / En Hızlı. **Varsayılan olarak `all`**(her kamera için geçerli dışa aktarım türü başına bir dosya), uygulamanın yakalaması gibi**hizalanmış** (yerel dosyalar için `--no-aligned`). Atlanan kameralar (örn. RGB, parlaklık/yansıtma değerlerinden hariç tutulur) `Skipped: SN:<serial> (<reason>)` ile raporlanır; kullanılan yakalamayı okuyan DAQ verisi birlikte kaydedilir, yalnızca ham veriden oluşan bir yakalama dahil edilir ve `DAQ: <path>` ile raporlanır ([Bir yakalamayı okuyan DAQ kaydeder](#yakalamayı-okuyan-daq-kaydediyor)). Bkz. [Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](#yakalama-modları-kaydediciler--çevrimdışı-yeniden-işleme). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only] [--expr EXPR]` | Canlı birleştirilmiş indeks görünümünü video/GIF olarak kaydedin (izleme sınıfı; GUI görünümü açık değilken, arka uç dizinin NDVI değerini işler veya `--expr`). |
| `lattice array-burst [--duration S] [--max-frames N] [--orient] [--build] [--products …]` | Yüksek kare hızında ham Bayer seri çekimi (analiz sınıfı; çevrimdışı yeniden işleme). `array-record` ve `array-capture --continuous` gibi, her yoklamada arka uçla durum kontrolü yapar: durdurulmadan sonlandırılır (`kill -9`), arka uç son durum kontrolünden 30 saniye sonra işlemi durdurur (`stop_reason` `client_gone`; durdurma işleminde olduğu gibi, seri çekimin manifestosu sonlandırılır ve birleştirilmiş dizin oluşturma işlemi başlatılır) ve dizi bağlı kalır. |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Kaydedilmiş ham seri verileri kalibre edilmiş videolara dönüştürür. |

> **Güç kaybeden veya bağlantısı kesilen bir kamera kendiliğinden geri gelir.** Bir üye yanıt vermemeye başladığında (bir anahtarın güç döngüsü, beslediği tüm kameraları yeniden başlatır), dizi onu bekler, tekrar yanıt verdiğinde bağlantıyı yeniden açar ve `array-connect` tarafından ayarlananları geri verir: PTP, tetikleme ve senkronizasyon hattı, ağ hızı, piksel formatı ve kare boyutu ile otomatik pozlama. Ardından yeni bir `array-connect` olmadan tekrar akışa devam eder. Bu sırada `array-status`, `state: recovering (...)` değerini gösterir ve `last_error` hangi kamerayı beklediğini belirtir; ardından `re-opened:` değerini yazdırır. Yeniden başlatılan bir kamera PTP kapalı olarak geri döner; bu nedenle yeniden açılana kadar `ptp_enabled`, `False` olur ve `ptp_off:` ona bir ad verir.

> **Tutulan bir kamera asla ikinci kez açılmaz.** İkinci bir açma işlemi, tutan tarafın akışını durdurur ve ayarlarını yeniden yazar; ayrıca arka uçta çökmeye neden olabilir. Dolayısıyla, havuzun tuttuğu bir kamera (GUI veya `cam-connect`), bir dizi elemanı veya bir arka plan işinin tuttuğu bir kamera (çalışan bir `lattice continuous`, `multi-stream` veya `align-stream`; kendi veya başka bir istemcinin), `lattice continuous`, `lattice capture`, `cam-connect`, `power --sleep`, `calibrate`, tektek çekimlik `white-balance` ve `presets --apply`, HTTP 409 hatasıyla reddedilir; `CAMERA_HELD`, sahibini ve serbest bırakma komutunu (`chloros-cli lattice cam-disconnect -s <SN>`, `chloros-cli lattice array-disconnect --array-id <ID>` veya bir iş için iş kimliği ve başlatan kişi) belirtir ve çıkılır `1`. Aynı şekilde, şu anda başka bir komutun kullandığı bir kamera da (kameralarını açan bir `multi-test` veya `multi-capture`, başka bir tek seferlik komut) buradaki her komut için geçerlidir: hata mesajı, o komutun bitmesini beklemenizi söyler. `lattice power`, `--sleep` olmadan, varsa tutucunun kendi tanıtıcısını (havuz, dizi, bir işin açık kamerası) okur ve başka bir komutun kullandığı bir kamera için reddedilir. Her `CAMERA_HELD` yanıtı, hem `code` hem de `error_code`'te kodu taşır. Her kamerayı açan komutlar için bkz. [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions).

##### `array-connect` Seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--serials SN1,SN2,…` | tüm LATTICE kameralarını otomatik olarak algıla (en az 2 tane gerekir) | İlk seri numarası MASTER'dır. Belirtilmediğinde, algılama işlemi LATTICE (M3C / M3M) modelleriyle sınırlandırılır ve hepsi bağlanır. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO senkronizasyon hattı. |
| `--target-fps F` | otomatik | Master tetikleme atış hızı. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | otomatik | Katman seçiciyi geçersiz kılar. |
| `--wire-ceiling-mbps MB_PER_S` | otomatik algılama | **Ana bilgisayarın sürekli kablo bütçesi, MB/s cinsinden — tüm dizi tahsisinin dayandığı sayı.** Dizi, GVSP bozuk çerçeveler bildirdiğinde bu değeri düşürün: otomatik değer, NIC'nin bildirilen bağlantı hızından türetilir; bu değer, USB adaptörlerini, dar PCIe şeritlerini ve yoğun paylaşımlı yapıları olduğundan fazla gösterir. Projenin dizi yakalama bloğunda kalıcı olarak saklanır; bu nedenle, yeniden açma / CLI / SDK yeniden bağlanma işlemlerinde bu değer geri yüklenir. Bkz. [Dizi durumu](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | yok (tam sensör) | Donanım binningi (her zaman Ortalama). |
| `--width N` / `--height N` / `--pixel-format FMT` | tam sensör; kamera başına format | Tam sensör ve kamera başına format seçimi (Mono12 / BayerRG12) yerine, her kamera için açık bir kare boyutu veya piksel formatı. |
| `--no-recommend` | kapalı | Ağ analizi adımını atla (böylece her kamera, tam sensöre ayarlanmak yerine mevcut boyutunu korur). |
| `--no-ptp` | kapalı | PTP'yi devre dışı bırak (bu durumda kameralar arası zaman damgaları **karşılaştırılamaz**). |
| `--packed {auto,on,off}` | `auto` | 12 bitlik kareleri paketlenmiş olarak aktarın ve bu bilgisayarda açın — tam sensörde saniye başına daha fazla kare, aynı veriler. `auto` yalnızca ve bu bilgisayarın kapasitesi elverdiğinde paketler; `off` hiçbir zaman paketlemez. Bkz. [Paketlenmiş 12 bit aktarım](#packed-12-bit-transport---packed). |
| `--packet-size {auto,jumbo,standard,N}` | `auto` | Dizideki her kamera için GVSP paket boyutu. `auto`, her kameranın ağı taramasına izin verir; `jumbo` = 9000, `standard` = 1500 veya bayt cinsinden bir boyut, bunu sabitler. 1.3.0 sürümünden önce `array-connect` bu bayrağı kabul eder ve yok sayardı. |

> **Varsayılan olarak tam sensör.** `--width` / `--height` / `--binning` /
> `--pixel-format`, `array-connect`, her kamerayı tam sensöre ayarlar —
> birleştirme yok, ROI kırpma yapılmaz — ve kare hızını yüksek tutmak için kareleri küçültmek yerine, kare hızının bu bilgisayar ve
> ağın sağlayabileceği seviyeye düşmesine izin verir.
> Ağ analizi satırı öngörülen hızı gösterir ve bir sonraki satırda
> öngörülen hızlarıyla birlikte daha hızlı seçenekler (binning veya daha küçük bir ROI) listelenir; bir kare çekmek için
> `--binning` veya `--width`/`--height` komutunu girerek bunlardan birini seçin. Connect, kendi başına asla binning yapmaz veya kırpma
> işlemi gerçekleştirmez.

##### Sıkıştırılmış 12 bit aktarım (`--packed`)

Tam sensör modunda, bir dizinin kare hızı, her bir kameranın karelerini ne kadar hızlı
gönderebileceğine göre belirlenir. 12 bitlik formatlar (`BayerRG12`, `Mono12`) normalde 16 bitlik
kelimeler halinde aktarılır; **sıkıştırılmış** aktarım, aynı 12 biti piksel başına 1,5 bayt olarak gönderir ve
bu bilgisayarda açar; böylece her karenin gönderilmesi %25 daha az zaman alır —
**tam sensörde saniyede yaklaşık %33 daha fazla kare** (ölçülen değer: 10,7 → 14,0 fps
9 kameralı bir dizide). **Kaydedilen veriler tamamen aynıdır**: aynı değerler, aynı
16 bitlik dosyalar, aynı meta veriler.

- `auto` (varsayılan), yalnızca faydalı olduğunda **ve** bu bilgisayarın
  açma işlemine gücü yettiğinde paketler. Bağlantı kurulduğunda arka uç, kendi açma hızını, CPU sayısını,
  mevcut CPU yükünü ve ağ kapasitesini ölçer; böylece yedek çekirdekleri olan bir masaüstü bilgisayar
  tam sensörde sıkıştırma yaparken, meşgul veya düşük güçte çalışan bir bilgisayar sıkıştırılmamış veriyi aktarır. Küçük ROI’lar ve birleştirilmiş kareler
  az kazanç sağlar.
- `off` asla sıkıştırmaz — bunu, tanılama sırasında sıkıştırmayı devre dışı bırakmak için veya
  CPU’sunun tamamını diğer işler için ayırması gereken bir bilgisayarda kullanın.
- `on` her zaman 12 bit formatları paketler.

Connect çıktısı, kararı ve nedenini gösterir (`paketlenmiş 12 bit: (otomatik:
tahmin edilen 11,0 -> 14,4 fps, …)`); `/api/camera/array/durum` raporları
`packed_transport` ve `packed_decision`. Her seferinde bayrağı aktarmak yerine
sistem genelinde bir varsayılan ayar belirlemek için, arka uç ortamında `CHLOROS_ARRAY_PACKED12=off` (veya
`on`) ayarlayın — açık bir `--packed on|off` ayarı yine de önceliklidir.
8 bit ve 16 bit formatları asla sıkıştırılmaz.

### Tek bir proje kaydı, tüm modlar

Bir proje klasörü, her cihazın ayarlarını içerir: `cameras.json` — her kameranın (`cameras[].settings`, tek bir kamera için ve her dizi üyesi için) ve her dizinin dizi genelindeki ayarları (`arrays[].capture`: format, ROI, binning, tetikleme hızı, kablo bütçesi) — ve `sensors.json`, her bir ışık sensörüne ait ayarlar (entegrasyon süresi, kare ortalaması, sınır). Uygulama, CLI, SDK ve MCP sunucusu, aynı dosyaları aynı şekilde okur ve yazar:

- **Bir projeye bağlı bir cihaz, o projeye kaydedilir ve her değişikliği o projeye kaydeder.** "Bir proje kapsamında" ifadesi, arka uçta (uygulamanın) bir projenin açık olması anlamına gelir veya — SDK'den — kendi klasörünü belirleyen bir `ChlorosProject` aracılığıyla gerçekleşir. Projenin henüz içermediği bir kamera, dizi veya ışık sensörü, bağlandığında projenin dosyalarına eklenir (`lattice cam-connect`, `array-connect`, `daq pool-connect`, uygulama, SDK, `chloros-mcp`), ayarlarıyla birlikte. Herhangi bir moddan yapılan değişiklik (`lattice exposure`, `gain`, `configure --set`, `resolution`, bir dizininayarları ve tetikleme hızı, bir ışık sensörünün sınırı, uygulamanın kontrolleri) bir an sonra o projenin dosyalarına kaydedilir. Bir kamerada ayarlanan pozlama, kazanç veya AE hedefi “saklı” olarak kaydedilir; böylece proje yeniden açıldığında bu ayarlar tekrar korunur.
- **Bir proje açıldığında, cihazlar o projenin ayarlarına göre yapılandırılır**; her modda: kendi ayarları olmayan bir kamera, dizi veya ışık sensörü, projenin kaydedilmiş ayarlarını alır (bir proje açıkken `lattice cam-connect` / `array-connect`, `chloros-cli project connect`, `open_project().connect_all()`, uygulamanın yeniden açılması). Yeni bir kimlik altında bağlanan bir dizi, projenin aynı kameralarla kaydettiği diziyi günceller; diğer kameralara sahip bir dizi ise bunun yanına eklenir.
- **Uygulamanın kendi kaydetme işlemi, bir cihazın canlı ayarlarını** bunların eski kopyasıyla asla üzerine yazmaz ve CLI veya SDK çağrısı yapanların kaydettiği üye başına geçersiz kılma ayarlarını korur.
- **Ayarladığınız piksel formatı, projenin koruduğu formattır.** Dizi Ayarları'nda, `--pixel-format` veya `connect_array(pixel_format=)` ile ayarlanan format (8 bit dahil), aynen kaydedilir ve yeniden bağlanırken aynen uygulanır. Herhangi bir ayar yapılmadığında, her kamera 12 bitlik varsayılan ayarlarıyla bağlanır (mono sensörde `BayerRG12`, `Mono12`). Bir format adı, bit derinliğini ifade eder; aile, sensöre aittir; bu nedenle bir renkli kamera asla Mono formatına, bir mono kamera da Bayer formatına ayarlanmaz ve bir format eşleştirildiğinde `[PixelFormat <serial>] ... written` satırı bunu belirtir. 8-bitlik bir yakalamanın dışa aktarımları, yansıma dahil olmak üzere 8-bitliktir: yeniden ölçeklendirilmiş ve `PixelScale` etiketiyle damgalanmıştır.
- Herhangi bir projeye bağlı olmayan bir cihaz hiçbir şey kaydetmez.
- **Bir kayıt, kendisinden sonra oluşturulan bir projeyi takip eder; bir proje kapatıldığında kayıt, o projeyle birlikte kalır.**Bir proje oluşturulduğunda — bir SDK `create_project()`, `chloros-mcp`'in `create_project`'i veya uygulamanın**Yeni Proje**— veya çoğaltıldığında (uygulamanın**Çoğalt**seçeneği, bu işlem kopyayı açar), kayıt hâlâ daha önce açık olan projeye yazmaya devam ediyorsa, kayıt devam eder ve yazılanlar yeni projenin içinde aynı yere (aşağıya) kaydedilir. Mevcut bir projenin açılması — `chloros-cli process`, `project devices --reconnect`, `ChlorosProject.process()`, uygulamanın**Projeyi Aç** seçeneği — kayıt, kayıt yaptığı projeyle birlikte devam eder: kayıt orada biter ve dosyaları o projenin dosya listesine eklenir (uygulama, bir projeyi açmadan önce her kaydı durdurur).
  - `array-record` ve `array-burst`: Kayıt durduğunda video veya her kareyi içeren seri çekim klasörü, bir bütün olarak buraya taşınır (bunun `--duration`, Ctrl+C). Yazdırdığı `Burst dir` dosyası yenisidir; `--build` ve `array-build-video` dosyaları ise bu dosyadan çalışır. Sürücünün taşıma işlemini reddetmesi durumunda kayıt, yazıldığı yerde kalır;sonundaki günlük dosyası bunun nedenini belirtir; durdurma işleminin HTTP yanıtı (`relocation_error`) da aynı bilgiyi içerir. Durdurulan bir seri çekimin ham kareleri ve `.daq`, sonlandığı projenin dosya listesine eklenir; bir video (bir `array-record`, bir patlamadan oluşturulan video) ise projenin dosya listesine eklenmez.
  - `array-capture --continuous`: Değişiklikten sonraki her yakalama yeni projeye kaydedilir; değişiklikten önce kaydedilenler olduğu yerde kalır.
  - `daq pool-record` ve `project sensor log` (ve uygulamanın kendi ışık sensörü kaydı): `.daq`, kayıt durduğunda yeni projenin`light_sensor` klasörüne taşınır ve o projenin dosya listesine eklenir.

  Açık projenin dışındaki bir klasöre yapılan kayıt (başka bir yerdeki bir `-o`) olduğu yerde kalır; `lattice continuous` ve `multi-stream` ise kendilerine atanan klasörde kalır.
- **Uygulamada, yeni bir proje açıldıktan sonra yapılan yakalamalar bu projeye eklenir.** Bir proje oluşturulduğunda veya kopyalandığında, tüm yakalama işlemleri ve kayıtlar devam eder; bundan sonra yapılan her yakalama (Tümünü Yakala, Yakalamaya Başla) yeni projeye kaydedilir ve dosya listesine eklenir. Uygulamada mevcut bir projenin açılması, önce tüm işlemlerin durdurulmasına neden olur.
- **Bir proje, kayıt yaparken taşınmaz.**Açık projeyi başka bir çalışma dizinine taşıma (uygulamanın**Ayarlar → Çalışma Dizini**; CLI komutuyla proje taşınamaz; `/api/move-project` adresine gönderim yapan bir HTTP istemcisi bunu yapabilir) işlem, dizi kaydı veya seri çekim, dizi sürekli yakalama, bir `lattice continuous` / `multi-stream` işi veya bir ışık sensörü kaydı bu dizine yazıyorken: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.` Uygulama, kendi "Tümünü Yakala", "Yakalamaya Başla" veya ışık sensörü kaydı çalışırken bir klasör istemeden önce reddedilir.

Bu, bağlantı kesildiğinde kameraların hangi durumda kaldığından ayrı bir durumdur (bkz. [Kamera belleği](#camera-memory----keep-settings----clear-settings)): bir proje açıldığında kameralarının ayarlarını kendisi belirler.

### Kamera belleği — `--keep-settings` / `--clear-settings`

Bir dizi bağlandığında, kameralar dizinin ayarlarını alır ve siz de bunları değiştirebilirsiniz. Chloros'un kameraları serbest bırakması — `array-disconnect`, geçici bir komutun sonu (dizi içermeyen `multi-capture`, `multi-stream`, `multi-test`, `align-calibrate`, `align-apply`, `align-stream`, `index --live`), `lattice continuous`'in durdurulması, sahip olmadığı bir kameranın `lattice capture`'inin sonu — her birini bağlantı öncesinde bulunduğu duruma geri getirir: tetikleyici ve senkronizasyon hattı kablolaması, PTP ve kamera ayarları (tam liste [Çoklu Kamera (Geçici Oturumlar)](#multi-camera-transient-sessions) altında bulunur).

Kameraları bir projenin dışında kullanmak için, bu komutlardan herhangi birindeki iki bayrak, kameranın **kendi belleğine** — herhangi bir yazılımda açıldığında sahip olduğu ayarlara — yazma işlemi gerçekleştirir:

- **`--keep-settings`**, her kameranın ayarlarını oturumun bittiği andaki haliyle kameranın belleğine kaydeder (UserSet2, önyükleme seti olarak belirlenir). Kameralar hala serbest çalışır durumda bırakılır (`TriggerMode` `Off`), senkronizasyon hattı kablolaması olduğu gibi kalır. UserSet2'si olmayan bir kamera, ayarlarını UserSet1'e kaydeder; bu bilgisayar bunu hatırlar, böylece Chloros'un dizisi kendi kendini onarır — bu işlem, Chloros'un temel ayarlarından sapmış bir UserSet1'i sıfırlar — onu olduğu gibi bırakır.
- **`--clear-settings`**, her kameranın belleğini fabrika ayarlarına sıfırlar: Chloros'un temel ayarları, yani dizisinin kendi kendini onarma işleminin — `TriggerMode`, `Off`, `TriggerSource` ve `Line2` ile fabrika varsayılanları, otomatik pozlama, bir RGB kameranın kalibre edilmiş beyaz dengesi korunur — UserSet1'de, önyükleme seti oluşturulur.
- İkisi birleştirilemez. Her kamera bir satır alır: `SN:…: settings saved to the camera's memory (UserSet2); it powers up with them.` veya `SN:…: camera memory reset to the factory settings.`; yanıt olan `camera_memory`, kamera başına aynıdır.

Uygulama bu bayrakları sunmaz.

**Her çıkış, kameraları geri yerleştirir.**Yalnızca yukarıdaki komutlar değil, her çıkış yolunda bulunan haliyle geri yerleştirme işlemi gerçekleştirilir: `cam-disconnect` (ve uygulamanın**X**numaralı kamerası); uygulamanın**X** komutu bir dizide veya herhangi bir dizi elemanının satırında (bir dizi eleman olmadan çalışamaz, bu nedenle tüm dizi serbest bırakılır); uygulamayı kapatmak (arka uca her kamerayı, diziyi ve çalışan `lattice continuous` komutunu geri yerleştirmesi için en fazla 20 saniye süre tanır; o sırada hâlâ çalışmakta olan bir komut — kameralarını açan veya PTP saatlerini bekleyen geçici bir komut, tek seferlik bir `lattice capture`, `power`, `white-balance` veya `presets --apply` — bir sonraki güvenli noktada durur, önce kameralarını geri yerleştirir (en fazla 8 saniye sürer), ve HTTP 503 `SHUTTING_DOWN` yanıtını verir; hâlâ çalışmakta olan bir dizi bağlantı — `lattice array-connect`, uygulamanın bağlantısı — bir sonraki kamerasından önce durur ve açtığı kameraları en fazla 10 saniye boyunca geri getirir, ve HTTP 503 yanıtını verir: `SHUTTING_DOWN`; kapatma sırasında gelen bir bağlantı ise hiçbir şeyi açmaz: HTTP 503 `SHUTTING_DOWN`); ve sonlandırılan veya terminali kapanan bir CLI komutu (SIGTERM / SIGHUP işini durdurur — ve kameralarını geri koyar — çıkmadan önce `143` / `129`, tıpkı Ctrl+C gibi). Doğrudan sonlandırılan (SIGKILL) bir `lattice continuous`, `multi-stream` veya `align-stream` — ki bu hiçbir şeyi durduramaz — son kontrolünden 30 saniye son kontrolünden 30 saniye sonra geri yerleştirilir. Çöken veya zorla sonlandırılan bir arka uç hiçbir şeyi geri yerleştiremez; güç döngüsü her kamerayı kendi belleğine geri döndürür ve bir sonraki Chloros bağlantısı, hazır bulduğu bir kamerayı yeniden bağlar.**Bir proje, kameralarını kendisi ayarlar.** Bir dizi, açık bir projeyle bağlandığında, her üye, projenin kendisi için kaydettiği ayarları alır — kamera belleğinde ne varsa, pozlama, kazanç veya AE hedefi (sizin ayarınız olarak tutulur, böylece otomatik pozlama bu ayarı değiştirmez) ve beyaz dengesi modu (Chloros’un düzeltmesi; kameranın kendi beyaz dengesi devre dışı kalır).

### Akıllı AE / Akıllı Çekim

LATTICE dizileri, bağlanır bağlanmaz arka planda sürekli AE çalıştırır, ancak yeni hedeflenen bir sahnenin odaklanması biraz zaman alır. `array-capture --smart`, **kullanım kolaylığı** sunan bir özelliktir: dizideki tüm kameralarda AE'nin sabitlenmesini bekler, ardından çekimi tetikler. Oturum ortasında sahneyi değiştirdiğinizde bunu kullanın.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Varsayılan olarak, sabitlenme politikası muhafazakârdır: 5 saniye zaman aşımı, 1,5 saniye kararlılık aralığı, ±%5 pozlama dağılım toleransı. Otomasyondan farklı bir davranış istiyorsanız, SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) aracılığıyla ayarlamalar yapın.

#### Otomatik pozlamanın işleyişi

- **Önce pozlama, sonra kazanç.** Kazanç Otomatik konumdayken, pozlama izin verilen en uzun değere ulaşana kadar kazanç 0 dB'de kalır.
- **Manuel pozlama yapıldığında, kazancı değiştirene veya manuel moda ayarlayana kadar kazanç Otomatik modda kalır.** Otomatik moduna geri döndüğünüzde, kazanç sınırı önceki haline döner.
- **Küçük parlak noktalar sahneyi karartmaz.** Çerçevenin %1'ine kadar olan parlak noktalar göz ardı edilir; bilinen bir kalibrasyon hedefinin panellerinde ise panelin %0,5'ine kadar olan kısımlar göz ardı edilir.
- **DAQ pozlama üst sınırı yalnızca kalibrasyon hedefi görüş alanında iken geçerlidir.** Bu, beyaz panelin kırpılmasını önler; panel görünmediğinde, kamera sahneye göre pozlama yapar.
- **Sabit ışıkta pozlama sabit kalır, değişen ışık ise saniyeler içinde dengelenir.** Sabit ışıkta pozlama ve sınırı sabit kalır; ışıkta büyük bir değişiklikten sonra görüntü birkaç saniye içinde dengelenir. Bir kırpma artık pozlamayı kalıcı olarak sınırlamaz ve ROI değişikliği durumunda pozlama yeniden ayarlanır.
- **Parlaklık hedefi ulaşılabilir aralıkta kalır.** Kamera pozlama sınırında iken parlaklık artmaz ve kazancı yükselemez; bu sayede geri gelen bir ışık, karede parlama oluşturmaz.
- **Otomatik pozlaması yanıt vermeyi durduran bir kamera, bulunduğu ışık seviyesinde tutulur.** Parlak güneş ışığı altında bir kamera en kısa pozlama süresine düşebilir ve tüm ayarları normal değerlerdeyken karanlık bir kareyle bu durumda kalabilir, ancak tüm ayarları normal değerlerde olabilir. Chloros, kadrajı izler, kamerayı o ışık seviyesi için koruduğu seviyede tutar ve ışık değiştiğinde serbest bırakır. Karanlık veya örtülü bir ışık sensörü bunu etkilemez.

### Yakalama Dışa Aktarma Seviyeleri (`all` varsayılanı)

Bu sürümden itibaren, `lattice capture`, `lattice multi-capture` ve `lattice array-capture` **varsayılan olarak `--processing all`** olarak ayarlanmıştır — her kamera için geçerli olan ve GUI'nin "Tümünü Yakala" davranışına uyan, her dışa aktarma türü başına bir kaydedilmiş dosya. Seviyeler şunlardır:

| Seviye | Çıktı | Uygulanır |
| --- | --- | --- |
| `raw` | Sensörden doğrudan alınan tek kanallı Bayer (tek renkli kameralar: tek bant). | Tüm kameralar. |
| `debayered` | 3 kanallı renkli, **RGB**kanal sırasına göre kaydedilir (RGB fotometrik yorumlamalı bir TIFF; PNG/JPEG de aynı şekilde RGB olarak açılır): ham veriden kalibrasyon paketinin karanlık ofseti (DSNU) çıkarılır, ardından OpenCV'nin kenarduyarlı demosaik işlemi uygulanır — doğrusallık LUT'u, düz alan veya renk ayrıştırma yoktur (tek renkli kameralar: 1 kanallı gri tonlamalı). Python SDK'nin döndürdüğü bellek içi demosaiklenmiş kareler (`grab()`, `frame_stream()`)**BGR**, yani OpenCV sırasındadır; sadece kaydedilen dosya RGB biçimindedir. | Tüm kameralar. |
| `radiance` | tam radyometrik zincir aracılığıyla float32 W/m²/sr/nm. | Yalnızca multispektral (M3C/M3M) — **RGB filtreli kameralar için atlanır**RGB filtreli kameralar için atlanır**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), Pix4D uyumlu. | Yalnızca multispektral ve **yalnızca bir DAQ bağlandığında + kamera kalibre edildiğinde**; aksi takdirde atlanır. |
| `display` | Tam GUI önizleme zinciri (kameranın profiline göre CCM + WB + gama). `preview/` klasörüne kaydedilir. `lattice capture`, bunun için `preview` adını da kabul eder; `array-capture` / `multi-capture` yalnızca `display` adını kabul eder. | Tüm kameralar. |

Sadece o seviyeyi kaydetmek için tek bir seviye belirtin (`--processing debayered`). `all` talep ettiğinizde, belirli bir kam için geçerli olmayan seviyeler atlanır (ve rapor edilir), hata olarak değerlendirilmez — bağlı olmayan veya kalibre edilmemiş bir kamera yine de `raw` / `debayered` / `preview` değerlerini alır.

#### Bir yakalama işlemini kaydeden DAQ

Bir DAQ, kameraya veya diziye bağlandığında ya da havuza eklendiğinde, yakalama işlemi sırasında kullanılan aşağı doğru okuma değeri, yakalama klasörünün `daq/` alt klasöründeki **`.daq`** dosyasına yazılır; bu dosya, yakalama klasörünün `daq/` alt klasöründe bulunur ve dosyanın `.match.json` (okumanın pozlamadan ne kadar uzak olduğu) ve `.cameras.json` (okumanın kime ait olduğu) bilgileri eklenir ve bir `DAQ:` satırında raporlanır. Bu, ham veya debayered yakalamalar dahil olmak üzere her seviye için geçerlidir; dolayısıyla herhangi bir yakalama daha sonra parlaklık, yansıma veya bir indekse dönüştürülerek yeniden işlenebilir. Görüntü, GUI, CLI veya SDK'den alınmış olsun, aynı isim ve yan dosyalara sahip aynı dosyadır. Hiçbir DAQ okuma değeri olmadığında `.daq` yoktur ve bağlı bir dizi olmayan bir `multi-capture` (geçici yedekleme) hiçbir şeyi kaydetmez.

| Bayrak | Etki |
| --- | --- |
| `--no-daq` | Seviyeleri bunu kullanmayan bir yakalamadaki okumayı kaydetme (`raw`, `debayered`). Bir `radiance`, `reflectance` veya `display` seviyesi yine de bunu yazar, çünkü yeniden içe aktarma işlemi, ürünün üretilmesinde kullanılan okuma değerine ihtiyaç duyar. |
| `--daq-tolerance-ms MS` | Okuma değerinin maruz kalma değerinden ne kadar uzak olabileceği ve yine de tolerans dahilinde sayılabileceği (varsayılan 500). Yalnızca sonuç değişir (`.match.json`'in `in_tolerance` ve `tolerance_ms` değerleri, yansıma değerinin `DlsInTolerance` değeri), kullanılan okuma değeri asla değişmez. Bkz. [Pozlamadan Uzak Bir DAQ Okuma Değeri](#a-daq-reading-far-from-the-exposure). |
| `--force-daq` | Yalnızca ham verili bir yakalamada `.daq` değerini istemenin eski yöntemi. Artık bu varsayılan ayardır; `lattice capture` ve `array-capture` bu bayrağı hâlâ kabul eder (`multi-capture` kabul etmez). `--no-daq` bu bayrağa önceliklidir. |

Hangi DAQ: kameraya veya diziye bağlı olan (GUI’deki ışık sensörü seçimi, bir projenin kaydedilmiş bağlaması veya `chloros-cli daq pool-bind`), aksi takdirde arka uçtaki en güncel olan. Bir ham seri çekim (`array-burst`), daha önce olduğu gibi seri klasörü altında her bir ayrı okuma için kendi `.daq` dosyasını tutar.

**PNG ve JPEG, kanal başına 8 bit içerir; bu nedenle yalnızca `display` / `preview` ve indeks ürünlerini alırlar.** Tutamayacakları bir seviyeye sahip olan `--format png` veya `jpg`, herhangi bir şey yakalanmadan önce reddedilir; bu durumda seviye ve onu barındıran format olarak TIFF belirtilir:

- `radiance` (float32) ve `reflectance` (uint16, `32768` = 1,0) — her zaman; dolayısıyla `--processing all` (varsayılan) ile birlikte `--format png`/`jpg` de reddedilir: `--format tiff` veya `--levels display` kullanın.
- 8 bitten fazla veri aktaran bir kameradan gelen `raw` ve `debayered` (12 bitlik M3C / Mono12 M3M kareleri) — CLI, kameranın bit derinliğini göremez, bu nedenle arka uç bunları reddeder (HTTP 400). Tekçerçeveli `lattice capture --processing debayered`, PNG/JPEG için 8 bitlik bir çerçeve yakalar ve reddedilmez; kalıcı dizisi olmayan (8 bitlik debayered) bir `multi-capture` de reddedilmez.

Her yakalama yolu aynı şekilde yanıt verir (`error_code` `FORMAT_CANNOT_HOLD_LEVEL`): `lattice capture`, `multi-capture`, `array-capture`, `project capture` / `burst` ve bunların arkasındaki HTTP rotaları.

### Yakalama klasörünün yapısı

Her dışa aktarım türü, `-o` altındaki **kendi alt klasörüne**yerleştirilir; böylece çok düzeyli bir yakalama işleminde türler asla birbirine karışmaz. Çizimde,**`all` tarafından bir kameranın `lattice capture` ile yapılan varsayılan yakalama** gösterilmektedir — kamera başına birkaç seviye olduğundan, her dosya bir `_<level>` soneki taşır:

```
output/
├── raw/           capture_<ts>_SN<serial>-<filter>_raw.tiff
├── debayered/     capture_<ts>_SN<serial>-<filter>_debayered.tiff
├── radiance/      capture_<ts>_SN<serial>-<filter>_radiance.tiff
├── reflectance/   capture_<ts>_SN<serial>-<filter>_reflectance.tiff
├── preview/       capture_<ts>_SN<serial>-<filter>_display.tiff
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
├── clamp_masks/   radiance/…_radiance_clampmask.tif, reflectance/…, index/… — the unmix clamp
│                  masks, one subfolder per product folder, only when a band clamped
└── daq/           daq_data_<sensor id>_<ts>.daq: the downwelling reading matched to the capture
                   (+ .daq.cameras.json: whose)
```

Dosya adlarının nasıl değiştiği:

- **Kamera alanı — seri numarası, ardından filtre:** her yakalama dosyası, çekimi yapan kameranın adını `SN<serial>-<filter>` şeklinde taşır; burada `<filter>`, kamera modelinin filtre kısmını temsil eder: `SN261902142-F450`, `M3M-L87-F450` için, `SN213800234-FRGN`, `M3C-L87-FRGN` için; `SN214701292-FRGB`, bir RGB kamera için. Filtre, seri numarasını `-` ile birleştirir, `_` ile birleştirilmez; bu nedenle ad, eskisi gibi `_` ile ayrılmış alanlara (önek, tarih, saat, milisaniye, kamera, seviye) sahiptir. Modeli okunamayan bir kamera (silinmiş DeviceUserID), yalnızca `SN<serial>` olarak adlandırılır. Önceki sürümlerden alınan görüntülerde adda filtre yoktur; Chloros her ikisini de okur.
- **Önek:** `lattice capture`, dosyaları `capture_…`; `array-capture`, `multi-capture` olarak adlandırır; bağlı bir diziye sahip olanlara ise `project capture` / `burst` / `stream` dizisi olan dosyalar ise `sync_<ts>_SN<serial>-<filter>…` olarak adlandırılır.
- **Seviye son eki yalnızca**birkaç**seviye olduğunda kullanılır.** Tek bir çekimde**birkaç**seviye kaydeden bir kamera (`all` veya `--levels a,b`), `_<level>` ekler.**Tek** seviye kaydeden kamera ise sonek olmadan sade adını korur — `--processing reflectance`, `reflectance/sync_<ts>_SN<serial>-<filter>.tiff` olarak yazılır — böylece tek seviyeli çekimlere yönelik yazılmış komut dosyaları çalışmaya devam eder. Seviye hâlâ klasör tarafından belirlenir.
- **Kısıtlama maskeleri ürün adını takip eder:** `<product name>_clampmask.tif`. Tek seviyeli bir yansıma yakalama işlemi `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif` dosyasını yazar; bir `all` yakalama işlemi ise `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif` dosyasını yazar.
- **Uzantı**, `--format`'in ardından gelir: varsayılan `tiff`, `.tiff`'i verir; `--format tif` ise `.tif` değerini verir (sıkıştırma maskeleri her zaman `.tif` şeklindedir).
- **Tek bir istisna:** `lattice capture --processing debayered` (veya `--levels debayered`), hızlı tekçerçevelik bir yol izler ve `capture_<ts>_SN<serial>-<filter>.tiff`'i doğrudan `-o`'e yazar — alt klasör yoktur.
- **Akışlar ve patlamalar** aynı alanı taşır: `lattice continuous`, `frame_<seq>_SN<serial>-<filter>.tiff`, `lattice multi-stream`, `stream_<seq>_SN<serial>-<filter>_<frame id>.tiff` ve ham bir seri olan `<burst>_f<frame>_SN<serial>-<filter>_raw.tiff` adında bir ham patlama yazılır.

`<ts>` yakalama zaman damgasıdır ve `<serial>` kamera seri numarasıdır; dolayısıyla senkronize edilmiş bir grup,
kameralar arasında ortak bir zaman damgasını paylaşır. **Tek bir asimetriye dikkat edin:** `display` düzeyi,
`preview/` adlı bir klasörde saklanırken, çok düzeyli dosyalar `_display` adını — klasör ve sonek
sadece bu seviye için farklılık gösterir. Bilinmeyen seviyeler kendi adlarını taşıyan bir klasöre yönlendirilir ve alt klasör
oluşturulamazsa dosya kaybolmak yerine çıktı kök dizinine yazılır.

**Bir yakalama klasörünü yeniden işleme:**`chloros-cli process` dosyasını**yakalama kök dizinine**
(`output/`) yönlendirin. `process` normalde yalnızca belirttiğiniz klasörü içe aktarır, ancak bu klasörde
ve alt klasörleri varsa otomatik olarak alt klasörlere iner — böylece aynı seviyedeki alt klasörler ve
`daq/` alt klasörü tek seferde alınır. Bir yakalamanın her seviyesi, seviye başına tek bir görüntü yerine,
diğer seviyeler mod olarak kullanılabilir halde tek bir görüntü olarak içe aktarılır.

Bir **katman alt klasörünü** doğrudan adlandırmak (örn. `output/raw/`) da işe yarar. Bunu yaptığınızda `daq/`
geride kalır; bu nedenle, yeniden`raw/`'ten bir radyometrik
ürün türetirken DAQ okumasını da buraya kopyalayın veya yönlendirin — aksi takdirde zaman damgası eşleşmesi karşılaştırılacak bir öğe bulamaz.

**İşleme her zaman `raw`'ten başlar.** Her yakalama içinde ham kare, işleme hattının kaynağıdır;
`debayered`, `radiance`, `reflectance` ve `preview` görüntülenebilir modlar olarak gelir, ancak asla
işleme hattına geri beslenmez. Türetilmiş bir ürünün yeniden işlenmesi, piksellerine zaten işlenmiş olan vinyet, CCM ve
parlaklık hesaplamalarını yeniden uygulayacağından, Chloros bunu yapmak yerine
işlemi iki kez yapmaktan kaçınır. Bilinmesi gereken iki sonuç:

- `index/` ve `composite/` renderları **asla** işlenmez. Bunlar yakalama değil, çıktılardır —
  bir NDVI LUT renderında anlamlı bir radyans yorumu yoktur.
- `raw` **olmadan** dışa aktarılan bir yakalama klasörü (örn. `array-capture --processing reflectance`)
  geçerli bir iş akışı kaynağına sahip değildir. Bu yakalamalar normal şekilde içe aktarılır ve görüntülenir, ancak `process` bunları
  atlar ve bunu şöyle bildirir:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Eğer türetilmiş bir ürünü gerçekten geçirmek istiyorsanız —
  `demosaic` açıkken yakalanan bir hub oturumu veya eski bir klasör — `--input-level {raw,debayered,processed}` giriş
  noktasını zorlar ve atlama ayarını geçersiz kılar. Bu bayrak, kasıtlı olarak bırakılmış bir kaçış yoludur; `auto` (varsayılan)
  ham verisi olmayan bir yakalamayı asla işlemez.

**Kalibrasyon etiketleri: yalnızca `raw`'te.** Bir yakalamanın `raw` dosyası, kameranın kalibrasyon paketini içerir: `CalibrationJSON` yükü ve MicaSense uyumlu etiketler (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), sensörün ham değerlerini tanımlar. Diğer tüm seviyelerdeki dosyalar (`debayered`, `display`, `radiance`, `reflectance`, `index/` render) ve her bir `lattice continuous` / `multi-stream` karesi, kameranın kimliğini taşır (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) ve karenin kendi kaydını, `XMP-mapir:FrameJSON` (pozlama, kazanç, sıcaklık, piksel formatı, çekim zamanı, DAQ eşleşmesi), çünkü her `process` ürünü bu kimliği taşır. `process`, bu tür bir dosyanın kalibrasyonunu seri numarasına göre bulur ve `CalBundleSha256`, Chloros'un kalibrasyon önbelleğinde arar; bilgisayarda bu bilgisayarda bulunmuyorsa paketi bir kez indirir.

#### Birden fazla kameradan oluşturulan dosyalar

Birden fazla kameranın bantlarından oluşturulan bir dosya — çok bantlı yığın (`align-apply`, `align-stream`, `index --live --save-multiband`), birden fazla kameradan oluşturulan canlı dizin (`index.tif`, `index_LUT.png`), dizi yakalamadan elde edilen `composite/` görüntüsü — bunlara dahil olan her kameranın adını belirtir:

- **Kamera başına listeler, bant sırasına göre** (bileşik görüntü: arka plan katmanı, ardından ön plan; bir dizin katmanı, bantlarının geldiği kameralar): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (her kameranın bantlarının geçtiği kalibrasyon paketi, aksi takdirde — bantları hiçbir kalibrasyondan geçmeyen bir `align-apply` / `align-stream` yığını — Chloros'un önbelleğe aldığı, yakalama dosyalarının adını taşıyan kamera paketi), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — paralel listeler; her birinin *n* numaralı öğesi aynı kamerayı tanımlar; `-` ise bir kameranın değeri olmadığı durumdur (kalibrasyon paketi yok, bilinmeyen bir lens). Her kamera **tek** bir öğedir: paketinde bir alan için birden fazla değer varsa — örneğin bir paketin halojen lamba sertifikaları — bunlar `;` (`F-1479-1;F-1480-1;F-1481-1`) ile birleştirilir, asla virgülle değil, böylece liste, nasıl yazdırılırsa yazdırılsın kamera başına bir öğe içerir.
- **EXIF `SerialNumber`** aynı seri numaralarını virgülle birleştirilmiş olarak içerir (`214000535,214000551`).
- Tek bir kameraya ait **hiçbir** kimlik bilgisi taşımaz (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): bu değerler bir kamerayı ve paketini belirtir; `process` ise bunlardan dosyanın kalibrasyonunu belirler. Birkaç kameranın bantlarından hesaplanan bir dizin (`index.tif`, `index_LUT.png`) EXIF'te `Model` `LATTICE` (sistem) içerir; tek bir kameranın `LATT-…` modelini değil.**Tek** bir kameranın bantlarından oluşan bir dosya — bant başına TIFF (`--per-band`), bir `align-apply --per-camera` karesi, tek bir kameranın yığını veya canlı dizini — diğer tüm yakalama dosyaları gibi o kameranın kendi kimlik etiketlerini taşır. `align-apply`'in yığını, bant başına ve kamera başına dosyaları ile her bir `align-stream` yığını ve bant başına dosyaları, grubunun yakalama zamanını (referans kameranın pozlama süresi, UTC) taşır; ve bir mono kameranın `--per-camera` karesi, yığın gibi `--bit-depth`'te tek bir düzlemdir.**Kamera yönelimi (ters çevirme / döndürme).** Bir kameranın yönelim ayarı (GUI'deki Ayna H /
Mirror V / döndürme) çekimin nasıl kaydedileceğini belirler:

- **`raw` ve `debayered`, ayar ne olursa olsun tam olarak sensörün okuduğu şekilde kaydedilir**.
  Döndürülmüş veya ters çevrilmiş bir Bayer mozaiği farklı bir renk filtresi deseni oluşturur ve kalibrasyon
  haritaları (karanlık ofset, düz alan) sensörün kendi ızgarası üzerindedir.
- **`preview`/`display`, `radiance`, `reflectance` ve `index/` renderları yönlendirilmiştir** — tüm kalibrasyon hesaplamaları tamamlandıktan sonra
  bir kez döndürülür, böylece kamera kadrajlandığı şekilde açılırlar. Bu
  `clamp_masks/…_clampmask.tif` dosyaları aynı ızgarada yer alır.
- **Her çekim dosyası, ayarları** XMP'de kaydeder: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, saat yönünde,
  dönüşlerden sonra) ve `ChlorosOrientationApplied` — `false` ham ve bayer filtreden arındırılmış (doğal pikseller)
  `true` yönlendirilmiş ürünlerde. `ChlorosProcessing`, o dosyanın
  piksellerine uygulanan işlemleri listeler (doğal olduğunda `none`). Buna, `lattice continuous` akışındaki her kare (
  kameranın kendi ayarı, uygulanmamış) ve `lattice multi-stream`'in her karesi (kimlik:
  içindeki kareler, bir dizi yakalamadaki gibi doğal piksellerdir) dahildir. `lattice align-stream` / `align-apply`
  yığınları, ana kameranın ayarını uygulanmamış olarak kaydeder: bantları ana kameranın
  karesine uyarlanır ancak döndürülmez.
- **`process`, bir ham veriyi işlerken kaydedilen yönelimi uygular**: önce sensör koordinatlarında karanlık ofset, düz
  alan, demosaik ve ayrıştırma, ardından dizi hizalama çarpıtması,
  son olarak yönlendirme uygulanır — böylece önizleme, parlaklık, yansıma ve indeks ürünleri tam olarak
  canlı çekimdekiler gibi çıkar ve `ChlorosOrientationApplied=true` olarak kaydedilir. Kalibre edilmemiş yedek
  ürünler (`Sensor_Response_Images`, `Vignette_Corrected_Images`), düz alan işleminden sonra
  düz alan ayarından sonra aynı şekilde yönlendirilir. Bayes dönüşümü yapılmış ürün ise orijinal haliyle kalır.
- **Chloros görüntü görüntüleyici, kayıt sırasına göre döndürülmüş bir ham veya bayes dönüşümü yapılmış dosyayı gösterir** (sadece görüntüleme amaçlı —
  dosya değiştirilmez), böylece ürünlerine benzer; küçük resim, tam boyutlu görünüm,
  imleç okuması ve Dizin/LUT sanal alanı hepsi aynı döndürme açısını kullanır. Kayıt içermeyen bir dosya ve
  (zaten yönlendirilmiş) bir ürün, depolandığı şekilde gösterilir. Bir sanal alan dışa aktarımı,
  piksellerin taşıdığı yönelimi kaydeder (`ChlorosOrientationApplied=true`; kayıt içermeyen bir katmanın kimliği).
- **Kayıt içermeyen çekimler, orijinal haliyle işlenir** (değişmeyen davranış). 1.3.0 sürümünden önce,
  dönüş veya döndürme ayarı yapılmış tek kameralı bir çekim, ham ve debayered dosyalarını kendisi döndürürdü;
  Chloros, doğal bir dosyadan zaten-döndürülmüş ham dosyayı doğal bir dosyadan ayırt edemez ve tahminde bulunmaz, bu nedenle
  dosyayı depolandığı haliyle işler. Kalibrasyon haritaları sensörün kendi ızgarasındadır, bu nedenle kalibrasyonun önemli olduğu durumlarda bu tür bir
  çekim için 1.3.0 sürümüyle çekilmiş bir çekim kullanın.
- **Diziler varsayılan olarak hiçbir üye dosyayı döndürmez** (hizalama çarpıtması ve `Alignment*` XMP dosyaları,
  kameraların kendi, döndürülmemiş karelerindedir) ve kimliği kaydeder. `array-capture --orient`, hizalama çarpıtmasından sonra her bir
  üyenin çıktılarını döndürür: **ana** çerçevenin, üyeler
  bu çerçeveye çarpıtıldıkları andaki yönüne göre (böylece üyeler eş kayıtlı kalır), aksi takdirde kendi kameralarının yönüne göre. Her
  dosya, kendi piksellerinin bulunduğu çerçevenin yönelimini kaydeder — bir ham dosya, kendi kamerasının yönelimini —
  ve hizalama dönüşümünü içeren bir ham dosya da `ChlorosAlignedFlipHorizontal` /
  `ChlorosAlignedFlipVertical` / `ChlorosAlignedRotation` değerlerini de kaydeder: ana çerçeveye hizalanmış bir ürün için
  ana çerçeveye uyarlanmış bir ürün için. `process`, her ürünü kendi piksellerine ait olan değer kadar döndürür:
  ham dosyayı dönüştürürken hizalanmış olanı (`--array-alignment`, varsayılan — canlı hizalanmış
  yakalama ürünlerinin), hizalanmadığında ise kameranın kendi ürünlerine göre döndürür (`--no-array-alignment` — canlı
  hizalanmamış yakalamanın ürünleri). Aynı seçenek `array-burst`, `project capture` / `burst` /
  `stream`, tarifler ve SDK için de geçerlidir.

### Karışık Filtre Dizilerinde Atlanan Çekimler

RGB ve multispektral kameraları tek bir dizide birleştirdiğinizde, `array-capture --processing radiance` (veya `reflectance`) multispektral kareleri kaydeder ve **RGB kameraları atlar** RGB'yi atlar — geniş bant sensörler için Bayer başına parlaklık anlamlı değildir. CLI, kaydedilen her dosyayı (dışa aktarma seviyesiyle birlikte), yazılan her `.daq`'i ve her atlamayı açıkça gösterir; bu nedenle dosya sayısı şaşırtıcı değildir:

```
  Saved: …/output/reflectance/sync_…_SN213800234-FRGN.tiff [reflectance] (SN:213800234, fid:1)
  Saved: …/output/reflectance/sync_…_SN214000533-FOCN.tiff [reflectance] (SN:214000533, fid:1)
  Saved: …/output/reflectance/sync_…_SN214701288-FNGB.tiff [reflectance] (SN:214701288, fid:1)
  DAQ:   …/output/daq/daq_data_daq-e-54b5e0_….daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

Atlama nedeni belirteçleri `<level>-not-applicable-to-rgb-cam` biçimini izler. Kalibrasyon paketi yüklenmemiş bir kamera, `radiance` / `reflectance` komutlarını atlar; `<level>-needs-calibration (connect the camera so its cal pack loads)` ile atlar; kullanılabilir DAQ okuma yansıma değeri olmadığında ise `reflectance-skipped-no-dls` ile atlanır. Yansıma değeri ayrıca `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)` ile atlanabilir, ve bant büyük ölçüde DAQ ışık sensörünün radyometrik olarak kalibre edilmiş aralığının (~374–974 nm) dışında kaldığında `dls-uncalibrated-band-<nm>` ile de atlanabilir — sevk edilen SKU'lar arasında yalnızca F988, desteklenen yol yansıma paneli iş akışıdır.

Filtre türünden bağımsız olarak her kamerayı dahil etmek için `--processing debayered` (veya `display`) kullanın ya da her kamera için geçerli tüm seviyeleri tek seferde elde etmek için varsayılan `all`'i kullanın.

### Pozlamadan Uzak Bir DAQ Okuması

Her bir `.daq` yakalama, o pozlamaya eşleşen DAQ okumasıdır ve yanındaki `.match.json`, ikisi arasındaki mesafeyi (`gap_ms`) ve bunun tolerans aralığı içinde olup olmadığını (`in_tolerance`, yakalama ayarı `--daq-tolerance-ms` olarak belirlenmedikçe 500 ms) kaydeder. Tolerans sınırının dışındaki bir değer yine de kullanılır, böylece hiçbir kare kaybolmaz ve bu değer etiketlenir: yansıma dosyasının XMP'sinde `DlsInTolerance=False` ve `DlsMatchGapS` değerleri bulunur. `lattice capture`, `multi-capture` ve `array-capture` de bunu ilgili `.daq` altında yazdırır; bunu açıkladığınızda DAQ'akış hatası ile birlikte:

**Bir okuma değerinin pozlama ile nasıl eşleştirildiği.** Bir yakalama işlemi, pozlamadan sonra DAQ'nın ilk okuma değerini bekler; böylece bu değerin her iki yanındaki okuma değerlerini birleştirebilir (`method` `interpolated`). Bir DAQ-E, gün ışığında her 0,4 saniyede bir, iç mekanlarda ise her 1-2 saniyede bir okuma gönderir; bu nedenle bekleme süresi, pozlamaya değil, komuta bu aralıkların yaklaşık bir buçuk katı kadar eklenir. Göndermeyi durdurmuş bir DAQ için beklenmez. Bir DAQ-E okuma değeri, bir önceki okumadan bu yana geçen aralık boyunca ortalama ışık değeridir; bu nedenle o aralığın ortasına yerleştirilir: `gap_ms`, pozlamadan o orta noktaya kadar ölçülür ve `bracket_stamps_ns`, okumaların kendi zaman damgalarını verir. `array-capture --continuous`, yakalamayı yavaşlatmadan bekler: ham dosyaları hemen yazılır ve okuma geldiğinde `.daq` değerleri eklenir. `--interval` ile her geçiş, tek bir yakalama gibi bekler.

```
  DAQ:   …/output/daq/daq_data_daq-e-54b774_….daq
         DAQ reading 114.9 s from the exposure (tolerance 500 ms): reflectance uses it, labelled DlsInTolerance=False
         DAQ stream fault: no frame accepted from daq-e-54b774.local in 25 s, after 1932 good frame(s). …
```

O okumadan herhangi bir yansıma dosyası yazılmadığında, ilk satır bunun yerine `saved anyway; its .match.json says in_tolerance: false` ile biter.

Yakalama yine de `0` ile sona erer: her dosya yazılmıştır ve içeriğini belirtir. Akışı durmuş bir DAQ, son okumasını vermeye devam eder; bu nedenle, o yakalamanın yansıma değerine güvenmeden önce bunu `chloros-cli daq pool-list` ile kontrol edin. Arka uç, veri göndermeyi durdurmuş bir havuzlanmış DAQ-E’yi yeniden bağlar (bkz. [Verim Vermeyi Durduran Bir DAQ-E](#a-daq-e-that-stops-delivering)). Tolerans dışı bir okuma, saklanan bir okuma olmadığı sürece (aşağıda açıklanmıştır) yalnızca `DAQ:` satırını yazdırır.

**DAQ'si devre dışı kaldıktan sonra saklanan bir okuma.** Bir pozlama için hiçbir DAQ okuma sağlayamadığında (bağlı DAQ kullanılamıyorsa veya havuzlanmış hiçbir DAQ'da okuma yoksa, örneğin `daq pool-disconnect`'ten sonra), yakalama işlemi Chloros'un sahip olduğu son okuma değerini yeniden kullanır ve bu okuma değerini bu pozlamaya göre `.match.json` satırında belirtir: `method`, `retained`, `gap_ms`'ten bu pozlamaya, okumanın yapıldığı numunelere, o aralıktan gelen `in_tolerance`'e ve okumanın ilk olarak eşleştirildiği pozlamaya ait `retained_from_epoch_ns`'e kadar. Dolayısıyla, DAQ devre dışı kaldıktan çok sonra alınan bir yakalama, yukarıdaki satırları yazdırır ve bunların bir satır daha yazdırılır; bu satır, okuma değeri hala tolerans aralığı içindeyken bile yazdırılır:

```
         This is the DAQ's last reading: the DAQ is no longer reporting, so each capture reuses it until a new one arrives
```

`.daq`, okumanın alındığı DAQ'yi, o okumanın alındığı kalibrasyon ve kapak bilgilerini hâlâ belirtir; böylece yeniden içe aktarma işlemi sırasında hangi DAQ, kalibrasyon ve kapağın bunu ürettiği bilinir.

### Yazılamayan Dosyalar

Dosyası yazılamayan bir kamera — dolu bir sürücü, izin hatası — diğer kameraların dosyalarının kaydedilmesini engellemez. Her yakalama komutu (`lattice capture`, `multi-capture`, `array-capture`; `project capture`, `burst`, `stream`; ve bir tarifin `capture`, `burst` ve `stream` komutları), kaydedilemeyen her dosyayı stderr'a yazdırır, ardından, geri kalanlar kaydedilmiş olsa bile **`1`'ten çıkar**:

```
  Failed: SN:214701292 [reflectance] OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

Bir yakalamanın içermesi gereken her dosya kapsanmıştır; tekkare debayering işleminden geçirilmiş `lattice capture --levels debayered` dahil. `.daq` okuma işlemi sırasında, `.match.json` kaynak bilgisi sidecar dosyası yazılamayan dosya, `[match]` olarak listelenir (`.daq` dosyasının kendisi kaydedilmiştir).

Dolu bir sürücüde, tekrarlanan bir yakalama (`array-capture --continuous` / `--interval`, `project burst` / `stream`, bir tarif olan `burst` / `stream`) **çalışmanın geri kalanında aynı sürücüye hiçbir şey yazmak yerine o geçişte durur**. Diğer tüm hatalar, meydana geldikleri her geçişte rapor edilir ve çalışma sonuna kadar devam eder, ardından `1` ile sonlanır. `array-capture --continuous` (arka ucun sürekli yakalama işlemi) başarısız olanlar da dahil olmak üzere hiçbir dosyayı listelemez: bunları ilerleme ve özet satırlarında sayar (`N failed`, `N file(s) failed to write.`) ve `1`'ten çıkar — bkz. [Sürekli mod](#continuous-mode---continuous).

Kaydediciler — `array-burst` ve `array-record` — kendi hatalarını bildirir. `array-burst`, sürücü dolduğunda veya dolmadan hemen önce kendi kendine durur. Her ikisi de diğer yazma hatalarını atlayarak devam eder. Herhangi biri bir hata bildirdiğinde, bu hata sayaçların ardından yazdırılır ve **`1`'ten çıkar**:

```
  Recorder error: disk-full: OSError: [Errno 28] No space left on device
  Disk full: the drive the backend captures to has no space left. Free space on it (or capture to another drive) before capturing again.
```

`Disk low:`'in `Disk full:` yerine kullanılması, patlamanın sürücüyü doldurmadan durduğu anlamına gelir sürücüyü doldurmadan durdurulduğu anlamına gelir. Durdurulmadan önce kaydedilen çerçeveler korunur. `array-burst --build`, alan dolduğunda derlemeyi atlar; çünkü videolar aynı sürücüye kaydedilecektir. Önce boş alan açın, ardından `array-build-video --burst-dir …` komutunu çalıştırın. Diğer hatalardan sonra derleme yine de devam eder.

Hiçbir kare kaydetmemiş bir `array-record` de başarısız bir kayıttır (`Recorder error: no-frames: the combined view published no frame to record …`, `1`'ten çıkın) — bkz. [`array-record`](#array-record--combined-index-videogif-monitoring-grade).

---

## Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme

Bunların tümü **kalıcı bir dizi** üzerinde çalışır (önce `array-connect` komutunu çalıştırın). Bunlar, GUI yakalama panelini yansıtır.

### `array-capture` modları

`array-capture`, dört deklanşör modu ve bir dizi dışa aktarma seçeneği içeren tek bir komuttur:

| Mod | Bayrak | Davranış |
| --- | --- | --- |
| **Tek** *(varsayılan)* | (yok) | Bir senkronize yakalama grubu, ardından çıkılır. |
| **Sürekli** | `--continuous` | `Ctrl+C`, `--count N` yakalamaları veya tam bir sürücü tamamlanana kadar arka arkaya yakalamalar yapar. `--duration S` yakalamaları veya sürücünün tamamı tamamlanana kadar arka arkaya yakalama yapılır. Arka ucun kendi sürekli yakalama işlevini çalıştırır (GUI'nin Sürekli modu) — aşağıdaki [Sürekli mod](#continuous-mode---continuous) bölümüne bakın. |
| **Aralık** | `--interval S` | Her `S` saniyede bir geçiş (her geçişin başlangıcından itibaren ölçülür), aynı sınırlar. |
| **En Hızlı** | `--fastest` | Yalnızca ham-sadece + atanan DAQ okuma değeri + birleştirilmiş endeks bileşimi; parlaklık/yansıtma/ekran hesaplamalarını atlar, böylece kare hızlı bir şekilde görüntülenir. `--processing raw --force-daq`'i gerektirir. Kaydedilen `.daq`'i daha sonra kalibre edilmiş ürünlere yeniden işleyin. |

Dışa aktarma seçenekleri (herhangi bir modla birleştirilebilir; hepsi aynı GUI/SDK uç noktasını paylaşır):

| Bayrak | Etki |
| --- | --- |
| `--array-id ID` | Yakalanacak dizi (sadece bir tane bağlıysa atlayın). |
| `-o, --output DIR` | Çıkış klasörü (varsayılan `output`, komutu çalıştırdığınız klasörün altında). |
| `--format {tiff,tif,png,jpg,jpeg}` | Dosya biçimi (varsayılan `tiff`). PNG / JPEG yalnızca `display` ve indeks renderını alır; bkz. [Yakalama Dışa Aktarma Düzeyleri](#capture-export-levels-the-all-default). |
| `--processing LEVEL` | Tek dışa aktarma düzeyi (`raw`, `debayered`, `radiance`, `reflectance`, `display`) veya `all` (varsayılan). |
| `--levels L1,L2,…` | Dışa aktarma türlerinin açık alt kümesi (örn. `raw,radiance,reflectance`); **`--processing`'i geçersiz kılar**. |
| `--aligned` / `--no-aligned` | **Varsayılan: hizalı, tıpkı Chloros uygulamasının aynı diziyi yakaladığı gibi**(dizi başına**Hizalı** Yakalama Ayarı, siz kapatmadığınız sürece açıktır). Her üyenin ham olmayan dışa aktarımı (`debayered`, `display`, `radiance`, `reflectance`, dizin işleme) ana kameranın karesine uyarlanır ve üyelerin ortak örtüşme alanına göre kırpılır; böylece dosyalar piksel bazında hizalanır ve uygulamanınkilerle aynı boyut, kırpma ve `Alignment*` etiketleriyle çıkar — sensörden (veya ROI) boyutundan daha küçük olur; bir ROI kapsamında dönüştürme, pencereye göre yeniden ayarlanır. `XMP-mapir:AlignmentJSON`, `"applied": true`'i belirtir ve `crop_roi`'i verir. Ham dosya, doğal boyutunda bozulmamış olarak kalır ancak dönüşümü meta verilerde (`"applied": false`) taşır. `--no-aligned`, her dosyayı doğal boyutunda, bozulmamış olarak kaydeder. `--aligned`, açıkça belirtilen varsayılan ayardır. Düzeltme uygulanmış olsun ya da olmasın, dizi bir hizalama profiline sahip olduğunda her bir üye dosya, kamerasının `Alignment*` dönüşümünü kaydeder — profilin hizalamadığı bir kamera (dışarıda bırakılmış veya başarısız bir çözümleme sonucu kimlik konumuna zorlanmış) ve başka hiçbir kamera hizalanmadığında referans kamera hariç: bunlar `Alignment*` etiketlerini taşımaz. Dizide profil olmadığında çekim hizalanmamış olarak kaydedilir ve CLI, `Alignment:` satırında nedenini yazdırır; bu, çarpıtmanın uygulanamadığı diğer tüm durumlarda olduğu gibi (farklı çözünürlükteki üyeler, kablo ile senkronize edilmemiş bir dizi, profilin eşleştirilemediği bir kare) geçerlidir. Dizinin otomatik hizalaması, pozlama sabitlendiğinde profili oluşturur; `align-calibrate` ise talep üzerine bir profil oluşturur. |
| `--orient` | Her bir elemanın **ürünlerini**— `display`, `radiance`, `reflectance`, `index/` render ve `composite/` — hizalama çarpıtmasından sonra kamera yönelimine göre: bir ürün,**ana**yönüne göre ana çerçevenin çerçevesine kaydırılmış bir ürün; böylece elemanlar eş kayıtlı kalır; hizalanmamış bir ürün (`--no-aligned` veya kaydırma işleminin uygulanamadığı bir yakalama) ise kendi kamerasının yönüne göre kaydırılır.**Varsayılan: kapalı — her eleman dosyası yerel kalır**, tıpkı dizi yakalamalarında her zaman olduğu gibi. `raw` ve `debayered` her iki durumda da orijinal kalır. Her dosya, ürünlerinin taşıdığı yönelimi kaydeder (bkz. [Kamera yönelimi](#what-a-captures-folder-looks-like)); `Alignment*` XMP dosyası, yönelimden önce uygulanan yerel çerçeve dönüşümü olarak kalır; böylece `process` (warp, ardından yönlendirme) canlı ürünü yeniden üretir. |
| `--index` / `--no-index` | Yapılandırılmışsa, kamera başına bitki örtüsü endeksi katmanını kaydet / atla. Varsayılan: işlesin. |
| `--no-daq` | Seçilen hiçbir seviyede DAQ kullanılmadığında (sadece ham veri yakalama) `.daq` sidecar dosyasını atlar. Varsayılan olarak okuma kaydedilir, böylece kareler çevrimdışı olarak yansıma/indeks olarak yeniden işlenebilir. `--force-daq` hâlâ kabul edilir (varsayılan). |
| `--daq-tolerance-ms MS` | Bu yakalamalar için DAQ eşleşme toleransı (varsayılan 500 ms). |
| `--smart` | Tetiklemeden önce tüm kameralarda AE'nin sabitlenmesini bekleyin (bkz. [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF piksel sıkıştırması. `none` (1.3.0'dan beri varsayılan; 1.2.0'da `deflate` yazılırdı) = sıkıştırılmamış — sensör pikselleri okunduğu gibi, tam çözünürlüklü kare başına ~6,3 MB ve en hızlı sürekli çekim hızı; `deflate` = kayıpsız zlib L1 + yatay tahminci, kare başına ~4,1 MB, daha yavaş. Her ikisi de kayıpsızdır ve içe aktarımda aynı şekilde okunur. |

> **Tek yazma TIFF + sürekli aktarım hızı modeli.**Her dosya**bir kez**yazılır: pikseller, XMP paketi, IFD0 Marka/Model/Kamera Seri Numarası ve EXIF `ExposureTime` / `SerialNumber` etiketleri tek geçişte yazılır ve sonrasında hiçbir şey yeniden yazılmaz. `--continuous`X, ardışık senkronize grupları yakalar — her yakalama, bir önceki grubun ardından gelen grubu alır; buna, o yakalama hâlâ kaydedilirken yayınlanan grup da dahildir — böylece dizi hızında çalışır. DEFLATE, Python GIL’i tutar, bu nedenle sıkıştırılmış yazma işlemleri kamera başına yazar iş parçacıkları arasında**paralelleştirilmez**; sensör hızında sürekli tam çözünürlüklü yakalama için sıkıştırılmamış yazma işlemleri (varsayılan)**ve** NVMe sınıfı bir disk (~10 fps'de 9 kameralı bir dizi için ~600 MB/s sürekli hız) gerektirir. Aynı ayar, `POST /api/camera/array/capture` üzerinde `compression` olarak sunulur.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (aligned is the default; drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --no-index -o out/

# Each member at its native size, unwarped
chloros-cli lattice array-capture --processing reflectance --no-aligned -o native/

# Continuous raw capture for 60 s, as fast as the array publishes
chloros-cli lattice array-capture --continuous --duration 60 --fastest -o flightline/
```

#### Sürekli mod (`--continuous`)

`array-capture --continuous` (`--interval` veya `--smart` olmadan) işin yürütülmesini **arka ucun kendi sürekli yakalama işlevine** devreder — bu, GUI'nin Sürekli modunun çalıştığı oturumdur. Arka uç, her senkronize grubu kendisi kaydeder; bu sırada iki yakalama işlemi devam eder ve gruplar arasında CLI'den herhangi bir istek gelmez; böylece, yakalama başına bir istek geride kaldığı hızlı bir diziyle ayak uydurur (512 piksel ROI'de 9 kameralı bir dizide 73–77 yakalama/saniye olarak ölçülmüştür). Yukarıdaki her dışa aktarma seçeneği, her bir yakalama için geçerlidir.

- **Sınırlar:** `--count N`, N yakalamadan sonra durur (bir yakalama = bir senkronize grup: her üyenin dosyaları); `--duration S` ise S saniye sonra durur. Her ikisi de belirtilmezse, `Ctrl+C`'e ulaşana, sürücü dolana, bir hata oluşana veya dizi ortadan kalkana kadar çalışır. `Ctrl+C`, arka uç oturumunu durdurur, ardından özeti yazdırır.
- **İlerleme** her 2 saniyede bir, sıfırdan farklı olduğunda `, N failed` ve `, N groups skipped` eklenir (atlanan grup, dizinin yayınladığı ancak hiçbir yakalama işleminin gerçekleşmediği gruptur):

  ```
     12.0s  880 captures (73.3/s), 7920 files
  ```

- **Dosya bazında liste oluşturulmaz.** Çalışma, bir özet satırıyla sona erer — yakalamalar, hız, dosyalar ve çıktı klasörü — ardından sıfırdan farklı olduğunda `N file(s) failed to write.` / `N group(s) skipped.` eklenir:

  ```
  Done — 880 captures (73.3/s), 7920 files in /home/user/flightline.
  ```

- **Durur**: sürücü dolduğunda `Continuous capture stopped: the drive is full.` mesajıyla, bir hata oluştuğunda veya dizi ortadan kalktığında ise `Continuous capture stopped: <reason>.` mesajıyla (her ikisi de stderr'de).
- **Çıkış durumu:** Yakalamalar kaydedildiğinde ve her dosya yazıldığında (`Ctrl+C` dahil) `0`; yazılması başarısız olan herhangi bir dosya, sürücünün dolu olması, bir hata, dizinin ortadan kalkması veya hiç yakalama olmaması durumunda.
- **Microsoft Defender:** Defender, yakalamanın yazdığı dosyaları taramak için bir CPU çekirdeği veya daha fazlasını kullandığında, çalıştırma stderr'de bir kez şu mesajı yazdırır: `Note: Microsoft Defender is using N.N CPU cores scanning the capture files, CPU the capture needs. Re-run the Chloros installer with "Exclude the Chloros backend from Microsoft Defender scanning" checked.` Yükleyicinin [Sistem ayarları](#windows-exe) bölümüne bakın.
- Her dizide bir seferde tek bir sürekli yakalama yapılır; ikincisi `a continuous capture is already running for this array` hatasıyla reddedilir. HTTP üzerinden oturum `POST /api/camera/array/continuous/start` şeklindedir (`/api/camera/array/capture` gövdesi artı `max_captures` / `duration_s`), `GET /api/camera/array/continuous/status?array_id=ID` ve `POST /api/camera/array/continuous/stop`'tir.

**Interval**(`--interval S`, `--continuous` olsun ya da olmasın) ve**`--continuous --smart`**istek başına döngüyü korur: her geçiş için bir yakalama isteği —**Aralık**için planlanan her anda,**`--smart`** için AE'nin sabitlenmesinden sonra — her geçişin dosyaları Tekli modda olduğu gibi listelenir, sonunda bir `Done — N capture pass(es).` satırı bulunur ve `--count N` geçiş sayısını gösterir.

### `array-record` — birleştirilmiş dizinli video/GIF (izleme sınıfı)

Dizinin **canlı birleştirilmiş indeks görünümünü** bir `.avi` dosyasına (ve isteğe bağlı olarak bir `.gif` dosyasına) kaydeder. GUI'nin birleştirilmiş görünümü açıkken, bu görünümü görüntülendiği şekilde kaydeder. Hiçbiri açık değilse, arka uç görünümü kendisi oluşturur: `--expr` ile ilettiğiniz indeks veya dizi hizalandıktan sonra varsayılan olarak dizinin NDVI'si. Varsayılan NDVI, canlı görünümde olduğu gibi 660 nm ve 850 nm'ye en yakın kırmızı ve NIR bantlarını kullanır. Kayıt sırasında açılan bir GUI görünümü, açık kaldığı sürece kaydedilir. Her 2 saniyede bir ilerlemeyi kontrol eder ve `--duration`, `Ctrl+C` kodunda veya kaydedici kendi kendine sona erdiğinde durur.

Hiçbir kare yazmayan bir kayıt başarısız olur: kaydedici `no-frames: …` hatasını bildirir (dizi hala hizalanıyor veya pozlamasını ayarlıyor olabilir) ve komut **`1` ile sonlanır**.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF (no GUI needed)
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/

# Record a chosen index instead of the default NDVI
chloros-cli lattice array-record --duration 30 \
  --expr "(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)" -o monitoring/
```

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--array-id ID` | sadece dizi | Hedef dizi (sadece bir tane bağlıysa atlayın). |
| `-o, --output DIR` | `output` | Çıkış dizini. Göreli yol, komutu komutu çalıştırdığınız klasörün altındadır. |
| `--fps F` | `10` | Kayıt kare hızı. |
| `--duration S` | Ctrl+C'ye kadar | `S` saniye sonra otomatik olarak durdur. |
| `--gif` | kapalı | Ayrıca animasyonlu bir GIF dosyası da yaz. |
| `--gif-only` | kapalı | Yalnızca GIF kaydet (`.avi` yok). |
| `--expr EXPR` | dizinin NDVI'si | GUI görünümü açık olmadığında kaydedilecek, dizinin bant adlarında yer alan indeks ifadesi (örn. `'(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)'`; bkz. [bant adları](#index--vegetation-maths)). Kısa renk adı (`NIR`, `Red`) yalnızca dizinin bir bandında bu ad varsa çalışır. Açık olan bir GUI görünümü ise görüntülenildiği şekilde kaydedilir. |

### `array-burst` — ham Bayer yüksek kare hızlı seri çekim (analiz sınıfı)

Yakalama döngüsünün senkronize grup tamponunu doğrudan okur — **kalibrasyon zinciri, exiftool veya canlı görüntü gerekmez** — böylece kameranın tam çekim hızında çalışır. Ham kareleri + kare başına bir manifest + `<output>/bursts/<base>/` altında her bir farklı DLS okuması için bir `.daq` yazar. Çevrimdışı olarak yeniden işleyin (sonraki komut) veya durdurulduğunda hemen yapmak için `--build` komutunu verin.

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--array-id ID` | sadece dizi | Hedef dizi. |
| `-o, --output DIR` | `output` | Çıktı dizini (patlama `<DIR>/bursts/<base>/`'e düşer). |
| `--duration S` | Ctrl+C'ye kadar | `S` saniye sonra otomatik durdurma. |
| `--max-frames N` | sınırsız | `N` ham kareden sonra otomatik`N` ham kare sayısından sonra otomatik durdurma. |
| `--orient` | kapalı | Bir derlemenin bu veri akışından oluşturduğu ürünler, kamera yönüne göre döndürülür — kamera başına ürünler kendi kameralarının yönüne göre, birleştirilmiş dizin ise ana kameranın yönüne göre döndürülür — tıpkı `array-capture --orient`'te olduğu gibi. Ham kareler orijinal kalır; serideki `manifest.json`, her bir üyenin yönünü ve seçeneği kaydeder. |
| `--build` | kapalı | Durdurulduktan sonra, seriyi hemen yeniden işleyin (`array-build-video` ile aynı). |
| `--products …` | `combined:index` | `--build` ile: hangi videoların oluşturulacağı (aşağıya bakın). |
| `--fps F` | `10` | `--build` ile birlikte: çıktı videosunun kare hızı (fps). |
| `--save-tiffs` | kapalı | `--build` ile: ayrıca kare başına kalibre edilmiş TIFF'leri de kaydedin (her biri, bir piksel sınırlandığında [unmix sınırlama maskesi](#unmix-clamp-mask-which-pixels-a-band-was-clamped-on) bir piksel sınırlandığında; derleme sonucu bunları `clamp_masks` olarak listeler). |
| `--gif` | kapalı | `--build` ile birlikte: ayrıca animasyonlu GIF'ler de yazın. |

### `array-build-video` — kaydedilmiş bir seri çekimi çevrimdışı olarak yeniden işleyin

Her bir ham kareyi en yakın kaydedilmiş `.daq` okumasına zaman açısından eşleştirir ve onu **ithalat iş akışıyla aynı parlaklık / yansıma / indeks zincirinden** geçirerek bir veya daha fazla video oluşturur.

`--products`, `kind:level` öğelerinden oluşan virgülle ayrılmış bir listedir; burada `kind` ∈ `per_cam` | `combined` ve `level` ∈ `radiance` | `reflectance` | `index`'tir. Tek başına bir `level` (`kind:` olmadan) varsayılan olarak `per_cam` değerine ayarlanır. Varsayılan değer `combined:index`'tir.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--burst-dir DIR` | (gerekli) | Seri çekim klasörünün yolu (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` listesi, yukarıdaki gibi. |
| `--fps F` | `10` | Çıkış videosunun kare hızı (fps). |
| `--save-tiffs` | kapalı | Videoların yanı sıra kare başına kalibre edilmiş TIFF dosyalarını da kaydedin. |
| `--gif` | kapalı | Ayrıca animasyonlu GIF'ler de yazın. |

**Bir videoda sınırlama maskesi yoktur** (tek görüntü akışı, 8 bit). Bunun yerine derleme sonucu `clamp_fractions` değerini içerir: her video ürünü için, her karenin, R / G / B kanalının içerdiği bantta sınırlandırılmış olarak ayrıştırılan piksellerin payını içerir — o karenin parlaklık/yansıtma çarpımının damgalanacağı `Chloros:Band{R,G,B}UnmixClampFraction` değeri (birleşik indeks için, her bir üye kameranın). Çalıştırma, her ürün için en kötü kareyi bir `Clamp:` satırına yazdırır. `--save-tiffs`, TIFF'ler için piksel başına maskeleri ekler; `clamp_masks/<level>/`'te TIFF dosyalarının `<level>/` klasörünün yanına eklenir.**Yarı kalan bir derleme işlemi, bozuk video bırakmaz.** Her video `<name>.partial.avi` olarak yazılır ve yalnızca tamamlandığında nihai adını alır; bu nedenle, o isimdeki eski bir video, yerine geçecek video tam olana kadar kalır. Geride kalan bir `.partial.avi` dosyasını değiştirmek için derlemeyi tekrar çalıştırın.

İşlem, kaydettiği her videoyu yazdırır, ardından herhangi bir `Skipped:` çıktısını (video üretmeyen istenen kamera/seviye — veya birleşik indeks — ile birlikte nedenini — ör. kalibrasyon paketi yok, yansıtma için DLS okuması yok veya birleşik indeks için kırmızı/NIR bant çifti yok) ve tüm `Note:` öğelerini yazdırır. Radyans veya yansıtma istenen bir RGB kamera, bunun yerine görüntü videosunu alır; kameranın kalibrasyon paketi mevcutsa beyaz dengesi ayarlanır; kalibrasyon paketi olmadan işlenen bir kamera ise bir `Note:` içinde belirtilir. İstenen bir ürün atlandığında veya istenildiği gibi oluşturulmadığında (yapılandırılmış bir indeks ifadesi çevrimdışı olarak değerlendirilmez) **sıfırdan farklı bir değerle** sonlanır; oluşturulan videolar yine de yazılır ve listelenir.

> **Doğru kaydediciyi seçin.** `array-record` *izleme sınıfı*dır — işlenmiş canlı kompoziti kaydeder: GUI’nin birleştirilmiş görünümü veya GUI açık değilse arka ucun kendi NDVI (veya `--expr`) görünümünü kaydeder. `array-burst` → `array-build-video` *analiz sınıfı*dır — ham sensör verilerini tam hızda kaydeder ve daha sonra kalibre edilmiş parlaklık/yansıtma/indeks videolarını yeniden oluşturur; canlı görüntüye gerek yoktur.

### Mono (M3M) Tek Bantlı Kameralar

**M3M**serisi, Bayer**M3C**'nin mono versiyonudur: kamera başına bir dar bantlı girişim filtresi (`M3M-<lens>-F<wavelength>`, örn. `M3M-L87-F685`), bu sayede sensör, Bayer mozaiği olmadan**tek bir gri tonlama bandı** sunar. Demosaikleme yapılacak bir şey yoktur, ayrıştırılması gereken kanallar arası parazit yoktur ve ayarlanması gereken beyaz dengesi yoktur — tüm RGB görüntüleme renk iş akışı burada geçerli değildir.

Bunun CLI'de anlamı şudur:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**, tek renkli bir kamera algılar ve anlamsız ayarlar zorlamak yerine**tek satırlık bir mesajla atlar**. Aynı oturumda bir RGB/Bayer M3C kamera ile çalıştırıldıklarında ise normal şekilde çalışırlar.
- **Bayer piksel formatı, derinliğini ifade eder.** M3M yalnızca Mono formatları sunar; bu nedenle `--pixel-format` veya `--preset`'ten gelen bir Bayer formatı, aynı derinlikteki Mono formatını ayarlar: `BayerRG8` ve `--preset high_speed`, `Mono8`'i verir; `BayerRG12`, `high_quality` ve `triggered` ise `Mono12`'i verir; paketlenmiş bir ad paketlenmiş olarak kalır (`BayerRG12p`, `Mono12p`'i verir). Dosyalar ve `lattice format`, Mono formatını adlandırır. M3C ve M3M kameralarını bir arada kullanan bir dizi, aynı aynı şekilde tek bir format alır: her M3C'de Bayer, her M3M'de ise Mono derinliği. Bir ad, bit derinliğidir: bunun tersi de geçerlidir, bu nedenle bir M3C asla Mono formatına yerleştirilmez (`--pixel-format Mono12`, dizinin ana kamerası hangisi olursa olsun, bir M3C'de `BayerRG12` olur), ve `lattice format --set` de aynı şekilde eşlenir ve ayarlanan formatı yazdırır.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** hala çalışır — radyans ve yansıma, *bant başına* radyometrik haritalardır ve tek bir bant için mükemmel şekilde tanımlanmıştır. Mono kareler, bir **kimlik** sensör tepki matrisi taşır (3×3 ayrıştırma yoktur), bu nedenle düzlem kalibrasyon hesaplamalarından etkilenmeden geçer.
- **Tek bir mono kamera, bitki örtüsü indeksi üretemez.**NDVI/NDRE/vb. için en az iki bant gerekir (örn. Red + NIR). Mono donanımdan bir indeks elde etmek için, farklı dalga boylarında**birkaç** M3M kamerasını yönlendirin, bunları tek bir çok bantlı yığın halinde hizalayın ve *bunu* indeksleyin:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Mono_660 --channel nir=Mono_850 \
  --save-multiband -o output/
```

`index --live` kameraları kendisi açar ([geçici oturum](#multi-camera-transient-sessions)), bu nedenle önce hiçbir şey bağlamayın: `--serials`, aksi takdirde profilin etkinleştirilmiş kameraları (bkz. [Dizin / Bitki Örtüsü Matematiği](#index--vegetation-maths)). Chloros herhangi bir kamerayı tutarken (GUI, `cam-connect`, `array-connect`) HTTP 409 `CAMERA_HELD` hatasıyla reddedilir ve program sonlandırılır `1`; bunları `chloros-cli lattice cam-disconnect --all` / `chloros-cli lattice array-disconnect --all` ile serbest bırakın.

`--channel` sembolleri, ön ayarın kanal adlarıyla **tam olarak** eşleşmelidir (büyük/küçük harfe duyarlı; NDVI'ler küçük harfli `red`, `nir` — bkz. `--list-presets`) ve bant tarafı, hizalanmış yığındaki bir bandı adlandırmalıdır (çevrimdışı modda 0 tabanlı bant indeksleri de kabul edilir, örn. `--channel red=0 --channel nir=1`).

Yığın genelinde ayırt edici unsur, model dizesindeki `M3M` belirtecidir (bu, `M3C` dizesinde asla görünmez) ve GUI/SDK'ye `is_mono` olarak yansıtılır.

---

## Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması (LATTICE dizileri)

LATTICE kameralar, GVSP akışını ana bilgisayarın Ethernet adaptörü üzerinden gerçekleştirir; bu nedenle çoklu kamera dizilerinde adaptörün **sürücüsü**ve**alma halkası boyutu**, bağlantı hızı kadar önemlidir. Yanlış ayarlar, Dizi Ayarları panelinde (ve `lattice network-analysis` / SDK’daki `Reduce ROI to enable` kapısında) `FRAMES WILL DROP` / `Reduce ROI to enable` hatası olarak ortaya çıkar (ve `lattice network-analysis` / SDK'daki `analyze_array_network()`'te), kameraların kendisi sağlıklı olsa bile.

### USB 10GbE adaptörleri — Realtek RTL8157 ("Realtek USB 10GbE Family Controller")

Aşağıdaki ayarlar, Windows sürücü özellikleridir. Linux'te bir USB adaptörüne de bir sınır uygulanır (x86_64'te 400 MB/s, NVIDIA Jetson'da 200 MB/s) (bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model)), ayrıca çekirdeğin soket alım tamponları her adaptör için önemlidir — bkz. [Linux ana bilgisayarları](#linux-hosts--socket-receive-buffers).

| Öğe | Gerekli değer | Neden önemlidir |
| --- | --- | --- |
| **Sürücü sürümü**|**≥ v10.67 (Ocak 2026)**, INF `rtump64x64sta.inf` | Eski**2016**sürücüsü (v10.65, `rtump64x64.inf`), kapatma/yeniden başlatma/uyku modunda**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**ile güç kapatma ve hata denetimlerini yanlış yönetir. Geçiş işlemi takılır (~5 dakikalık zaman aşımı), kullanıcı cihazı zorla kapatır ve tekrarlanan düzgün olmayan kapatma işlemleri**WMI deposunu bozar**(PowerShell/araçlar `Invalid class` hatasıyla çalışmaz hale gelir) ve**bir sonraki açılışta USB yığınını kilitler** (NIC etkinleşmez; USB sürücüler numaralandırılmayı durdurur). Düzgün yeniden başlatmalara güvenmeden önce realtek.com'dan (veya dongle satıcısından) güncelleme yapın. |
| **Alım Tamponları**— anahtar kelime `ReceiveBufferLen` |**256**(sürücü maks.) | NIC RX halkası. Sürücünün varsayılan değeri olan**32**, yalnızca ~0,26 MB kullanılabilir halka bırakır — bu, çoklu kamera patlamaları için çok küçüktür — bu nedenle dizi paneli `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` hatasını bildirir ve bağlantıların kurulmasını engeller.**256**halka büyüktür (**laboratuvardaki 10GbE ana bilgisayarda ölçülen ~13,5 MB**), bu da RX boru hattına çoklu kamera GVSP patlamaları için gerçek bir marj sağlar. (Belirli bir yapılandırmanın gerçekten *bağlanıp bağlanmayacağı*, iki kontrol tarafından belirlenir — **boşaltma duyarlı**kabul kontrolü ve**toplam aşırı abonelik** kontrolü — ham patlama-halka karşılaştırması değil; bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model).) |
| **Alınan URB’ler**— anahtar kelime `PendingReceives` |**64** (maks.) | Aktarım halindeki USB istek blokları; patlama emilimi için Alım Tamponları ile birlikte artırın. |
| **Jumbo Çerçeve** — anahtar kelime `*JumboPacket` | **9014** | 9000 baytlık GVSP paketleri için gereklidir (1500'e kıyasla çerçeve başına 6 kat daha az paket). |

> ⚠️ **Bir NIC sürücüsü güncellemesi, bu gelişmiş özellikleri varsayılan değerlere SIFIRLAR.**Adaptör sürücüsünü güncelledikten veya değiştirdikten sonra,**`ReceiveBufferLen=256` ve `PendingReceives=64`'i yeniden uygulayın**`ReceiveBufferLen=256` ve `PendingReceives=64` ayarlarını**yeniden uygulayın**; aksi takdirde, "donanımda hiçbir değişiklik olmamasına" rağmen dizi paneli tekrar kilitlenecektir. Bu, daha önce çalışan bir sistemin aniden bağlanmayı reddetmesinin en önemli nedenidir.**Yönetici** PowerShell'den uygulayın (adaptör adınızı girin, ör. `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix`, USB 10GbE adaptörlerini kapsar.** Artık adaptör türünü algılar ve doğru alıcı halkası anahtar sözcüğünü ayarlar: PCIe NIC'ler için `*ReceiveBuffers`→2048 (Intel I219, vb.) için `*ReceiveBuffers`→2048 veya Realtek **USB** 10GbE denetleyicisi (`*ReceiveBuffers` değerini sunmayan) için `ReceiveBufferLen`→256 + `PendingReceives`→64. Hedef değerler, her sürücünün bildirdiği maksimum değere (`NumericParameterMaxValue`) sınırlandırılır, böylece aralık dışı bir değer asla yazılmaz. Bunu **yönetici ayrıcalıklı** bir terminalden çalıştırın; kayıt defteri tabanlı tüm ayarlamalarda olduğu gibi, değişiklik adaptörün yeniden başlatılması veya sistemin yeniden başlatılmasından sonra yürürlüğe girer. Yukarıdaki manuel `Set-NetAdapterAdvancedProperty` komutları da iyi bir alternatif olmaya devam eder — bunlar yeniden başlatma gerektirmeden anında uygulanır (adaptörü yeniden bağlar).

### Linux ana bilgisayarlar — soket alım tamponları

Linux'te (NVIDIA Jetson dahil), çekirdeğin soket alım tamponları, ağ kartının (NIC) alım halkası kadar önemlidir. Çekirdeğin varsayılan ayarında (`net.core.rmem_max` / `net.core.rmem_default` = 212.992 bayt), bir kameradan gelen GVSP paketleri akışı, Chloros tarafından okunmadan önce akış soketini taşır. NIC'nin kendisi herhangi bir kayıp bildirmezken paketler çekirdekte düşürülür ve dizi, kare gruplarını kaybeder ve kare hızını düşürür. USB 10 GbE adaptörüne bağlı 4 kameralı bir dizide yapılan ölçümlerde, her ikisini de 32 MB'a yükseltmek, bağlantı kurulduktan sonra dizinin kare hızını 4,96'dan 7,52 fps'ye, sürekli ham yakalama hızını ise 4,7'den 7,6 yakalama/sn'ye çıkardı; bu sırada çekirdekte hiçbir paket düşürülmedi.

- **.deb dosyası bu değerleri ayarlar.** Paketin yüklenmesi, `/etc/sysctl.d/60-chloros-gige.conf` değerini (her ikisi de `33554432` olarak) yazar ve hemen uygular. Zaten daha yüksek bir değere ayarlanmış olanlar değiştirilmez.
- **`lattice network` bunları kontrol eder.** Herhangi biri daha düşükse, *Tespit edilen sorunlar* altında `socket receive buffers too small for GigE Vision (…)`'i listeler ve arka uç günlüğü, ilk kamera veya dizi bağlantısında aynı uyarıyı içerir.
- **Bunları manuel olarak yükseltmek için** (bir sonraki yeniden başlatmaya kadar; paketin dosyası her önyüklemede bunları uygular):

  ```bash
  sudo sysctl -w net.core.rmem_max=33554432 net.core.rmem_default=33554432
  ```

  `lattice network --fix` de bu değerleri yükseltir ve kalıcı hale getirir, ancak yalnızca arka uç root olarak çalıştığında. Kullanıcınız olarak çalışan bir arka uç (`chloros-backend` ile başlatılan veya [`chloros-backend@<user>`](#running-the-backend-as-a-service-headless)) ile başlatılan bir arka uç, çekirdek ayarlarını değiştiremez ve bunun yerine `sudo sysctl` satırını yazdırır.

**Otomatik adaptör ayarlaması yalnızca bağlı adaptörleri etkiler.** İlk kamera bağlantısında, root olarak çalışan bir arka uç, alım halkası küçük olan kablolu adaptörleri ayarlar. Artık yalnızca bağlantısı olan adaptörleri ayarlar; bu nedenle, fişi çekilmiş yerleşik bir bağlantı noktası MTU değerini korur ve alım tamponlarına dokunulmaz. `lattice network`, artık fişi çekilmiş bir adaptörünMTU'sunu bir sorun olarak listelemiyor. `lattice network`, `--fix`, `lattice probe` ve adaptör ayarlaması yalnızca kablolu Ethernet bağlantı noktalarını dikkate alır; Jetson’un CAN veriyolu (`can0`) veya bir ana bilgisayar PC’ye sunduğu USB aygıt modu bağlantıları (`usb0`, `usb1`) dikkate alınmaz; ancak kameralar için kullanılan bir USB Ethernet adaptörü hala dikkate alınır.

### Ağ temelleri (tüm LATTICE bağlantıları)

- **Adresleme:** bağlantı-yerel `169.254.0.0/16` (GigE Vision LLA). Ana bilgisayar statik bir `169.254.x.x/16` alır; kameralar ve DAQ-E aynı aralıkta kendiliğinden adres atar. DHCP/ağ geçidi gerekmez.
- **Paket boyutu:**jumbo (9000) tercih edilir, ancak otomatik algılama işleminin bunu bulmasına izin verin — GVSP algılama yoluyla kameranın 1500 baytlık ICMP sınırını aşar, böylece kablo gerçekten taşıyabildiği her yerde jumbo boyutuna ulaşır.**Her kamera, her arka uç oturumu başına bir kez ölçülür ve bağlantısında bir değişiklik olduğunda (başka bir adaptör, farklı bir bağlantı hızı veya bir anahtarın değiştirilmesi gibi bir bağlantının kesilmesi durumunda) tekrar ölçülür; yeniden bağlanmalarda sonuç yeniden kullanılır. Çalışır durumda kalan bir anahtarın arkasındaki bir değişiklikten sonra, tekrar ölçüm yapmak için arka ucu yeniden başlatın.**Bu kontrol sırasında ilk bağlantı bazen birkaç dakika sürebilir ve böyle bir durumda günlüğe bu durum kaydedilir. Yalnızca sondadan daha iyi bildiğiniz durumlarda `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` ile sabitleme yapın ve kalıcı ayarlara göre komut bazlı ayarları tercih edin: sabitleme, sondayı atlar, bu nedenle yol 9000'i gerçekten taşıyamıyorsa**her** yakalama işlemi `SC_ERR_TIMEOUT -1011` ile zaman aşımına uğrar (bkz. [Ortam Değişkenleri](#environment-variables)).
- **RX halkası, `ReceiveBufferLen` ile orantılıdır:**varsayılan `32` değerinde kullanılabilir halka ~0,26 MB'dir (herhangi bir çoklu kamera patlaması için çok küçük); maksimum `256` değerinde ise büyüktür (laboratuvardaki 10GbE ana bilgisayarda ölçülen ~13,5 MB) ve gerçek bir yedek kapasite sağlar. Bir yapılandırmanın bağlanıp bağlanmayacağı, ham seri çekim ile halka kapasitesinin karşılaştırılmasıyla değil, drenaj duyarlı kabul kontrolü**ve** aşağıdaki toplam aşırı abonelik kontrolü ile belirlenir.

### Dizi fps ve seri çekim modeli

Dizi Ayarları panelini (ve `lattice analyze-array` / SDK'daki `analyze_array_network`) nasıl okumalı:

- **Burst, her kameranın gerçek piksel formatında kamera başına toplanır.**Mono**M3M**kameralar**Mono12 (2 B/px)**akışı sağlar veya `--pixel-format` ile derinlik bilgisinin Mono formatını kullanır (`BayerRG8`, `Mono8`'tir, 1 B/px);**M3C**Bayer kameralar 8 veya 12 bit akış sağlar (M3C donanım yazılımı, BayerRG8 talep edildiğinde bile sessizce BayerRG12 yayar). Dolayısıyla, 4 kameralı tam çözünürlüklü bir kare,**hepsi 8 bit ise ~12,6 MB, ancak üç adet 12 bit mono kamera ile ~25 MB** olur. Projeksiyon, her kameranın formatını modelinden (kimlik önbelleği) belirler; böylece veri akışı, kablonun gerçekte taşıdığı veriyle eşleşir — tek tip bir BayerRG8 varsayımı değildir.
- **Bir USB Ethernet adaptörünün hızı, teknik özelliklerinde belirtilen değer ne olursa olsun 200 MB/s ile sınırlıdır.** Bağlantı hızını sürekli bir değere dönüştüren verimlilik tablosu PCIe'den türetilmiştir; bir USB ağ kartı *Ethernet* bağlantı hızını bildirir, ancak USB veriyolu ve sürücüsü tarafından sınırlandırılır. ~1063 MB/s "sürekli" hız elde ediyordu — bu rakam hiçbir zaman test edilmedi — ve ortaya çıkan hız ayarlaması, sağlıklı bir hedef fps değeri bildirmeye devam ederken çerçevelerin %6–18'ini bozuyordu. USB'ye bağlı NIC'ler artık mutlak bir sınırla kısıtlanmıştır: Windows ve NVIDIA Jetson'da **200 MB/s**, Linux x86_64'te **400 MB/s** (Linux'te, USB 2.0 bağlantı noktasına takılan bir adaptör için **30 MB/s**'lik bir sınır belirlenmiştir ve özellik bilgisinde USB 3 bağlantı noktasına takılması önerilmektedir) (sınır, ana bilgisayarın USB veriyolu ve sürücüsüne bağlıdır; bu nedenle teknik özelliklerle orantılı değildir; bir USB 1 GbE adaptörü ~80 MB/s hız sağlar ve bu sınırdan etkilenmez). Linux'te adaptör sysfs üzerinden tanımlanır (`ethtool -i`'ten `bus-info`'e geri dönülür). Her rakam ölçülmüştür: Windows'da aynı 4 kameralı dizi, 200 MB/s'nin üzerindeki hızlarda karelerin %6–10'unu bozmuştur; Linux x86_64'te aynı adaptör, 480 MB/s'ye kadar her hız sınırında hatasız çalışmıştır (273 MB/s aktarım hızı, kameraların kendi sınırı). NVIDIA Jetson'da aynı adaptör yalnızca 200 MB/s hızında hatasız çalıştı ve bu hızın üzerinde aralıklı olarak kare kaybı yaşadı. `wire_ceiling_source`, kapasite kaydında bunu açıkça belirtir ve `nic_is_usb` bunu işaretler. Her iki durumda da `--wire-ceiling-mbps` ile geçersiz kılabilirsiniz.
- **Giriş, tüm patlama-halka karşı değil, drenaj farkındalıdır.** Eşzamanlı bir patlama, tüm patlamaya değil, yalnızca *geçici birikime* = `max(0, Σ per-cam arrival − host drain) × emit_window` uymak zorundadır. Hızlı ana bilgisayar / yavaş kamera yapısında (bir **PCIe**10G ana bilgisayar + 4× 1 GbE kamera: gelen veri ≈ 320 MB/s, boşaltma ≈ 1063 MB/s) konak, kameraların doldurma hızından daha hızlı boşaltır; birikim ≈ 0'dır; bu nedenle, 25 MB'lık patlama 13,5 MB'lık halkayı aşsa bile tam çözünürlüklü sim-emit**kabul eder**. Aynı dört kamerayı Windows'ta bir **USB**10GbE adaptörüne bağladığınızda boşaltma hızı 1063 değil, 200 MB/s olur — gelen veri boşaltma hızını aşar ve kayıp, daha düşük kare hızı yerine bozuk kareler olarak görünür. 1 GbE ana bilgisayarda, kameraların 31,25 MB/s DLThr taban değeri, gelen verinin boşaltma hızını aşmasına neden olur → sistem doğru bir şekilde**engeller** (*bu* engelleme sınıfı için ROI'yi azaltın veya ≥ 2 binning kullanın). Giriş, **iki** bağlantı kapısından biridir — diğeri ise aşağıdaki toplu aşırı abonelik kontrolüdür.
- **Tahmini fps, en yavaş kameranın döngüsüdür.**Eşzamanlı gönderimde her kamera karesini aynı anda gönderir; bu nedenle dizi, en yavaş üyesinin sensör okuma süresi ile gönderim süresi (kare baytları ÷ gönderim hızı: kameranın**erişim bağlantısı**, 1 GbE ≈ 80 MB/s veya bu değer daha küçükse ana bilgisayarın kablo tavanından aldığı pay). Her pozlama, önceki karenin okuma süresiyle çakışır; bu nedenle pozlama döngüye girmez: otomatik pozlama, hızın belirlediği süreyi doldurur, ardından kazancı artırır ve loş bir sahne artık hızı düşürmez (1.2.0 sürümünde düşürürdü). Yalnızca bu süreden daha uzun süren manuel pozlamalar tetiklemeyi yavaşlatır (bkz. [Dizideki bir eleman üzerinde pozlama](#exposure-on-an-array-member)). Kameraların eklenmesi, tel paylaşımları azalana kadar diziyi yavaşlatmaz. Seri yayımda (`slip-emit-and-capture`) ise kameraların yayın süreleri toplanır ve her bir elemanın pozlama süresi kademelendirme hesaplamasına dahil edilir. PCIe 10 GbE ana bilgisayara bağlı 9 kameralı bir dizide, tam sensör, 12 bit ayarlarında ölçülen değer: 10,7 fps veya [sıkıştırılmış aktarım](#packed-12-bit-transport---packed) ile 14,0 fps.
- **Sürekli bir yakalama işleminden sonra, projeksiyon neyi kaydettiğini gösterir.** Otomatik hızda 30 saniye veya daha uzun süren bir yakalama, bu bilgisayarda o dizi boyutunda sabitlenen hızı kaydeder; hız denetleyicisinin daha yüksek bir hızda yaptığı kısa denemeler hariç tutulur. O andan itibaren, `array-connect` satırı ve Dizi Ayarları paneli, bu hız modelin hızının altında olduğunda, onu sınırlayan faktörü de belirterek bu hızı gösterir: `clock-bound` (dizi, sabitlendiği hızda çalıştı, yakalama döngüsü tetik saatini bekliyordu), `loop-bound` (yakalama döngüsünün süresi doldu), `capture-bound` (yakalama geride kaldı ve grupları atladı) veya `measured` (hangisi olduğu belli değil). Modelin değeri `wire_fps_max`'tedir. Dizi, hattın taşıyabildiği değere kadar hala yükselir.
- **Bir dizi herhangi bir ağda çalışır — belirleyici olan kare hızıdır.**Her kamera, GevSCPD ile senkronize olarak**çarpışma emniyetli kablo tavanının**(`sustained × sim_emit_factor`) eşit bir payını alır ve hepsi yine de aynı tetiklemede pozlanır. Yavaş bir kabloda pay küçülür ve kare hızı da buna bağlı olarak düşer: Jumbo çerçeveler kullanılmayan 1 GbE üzerinde 8 adet tam çözünürlüklü 12 bit kamera, her biri 52 / 8 = 6,5 MB/s alır; bu da**1 fps**(sıkıştırılmış halde yaklaşık 1,3 fps) elde eder; oysa 10G ana bilgisayar bunları 11-14 fps hızında çalıştırır. Kameralar bu kadar yavaş senkronize edilemiyorsa — bunu öğrenmek için her kameranın GevSCPD aralığını okuyun — dizi,**seri**yayın moduna geçer (`slip-emit-and-capture`) moduna geçer: kameralar tam bağlantı hızında sırayla devreye girer ve birbiri ardına tetiklenir; böylece her çerçeve yine ulaşır ancak üyeler**aynı anda maruz kalmaz**. `analyze-array` ve connect çıktısı bu modu belirtir; geri dönüşü reddetmek için `--force-tier sim-capture-sim-emit`'i sabitleyin. Kare boyutu bu paylaşım sürecine dahil değildir; dolayısıyla**binning ve daha küçük bir ROI, kare hızını artırır ancak dizinin sığıp sığmayacağını değiştirmez**. Yalnızca diziyi seri olarak bile taşıyamayan bir kablo**aşırı yüklenmiş**sayılır — örneğin, bir kablonun işleyebileceğinden çok daha fazla kamera olması veya o kablo üzerinden akış yapan çok sayıda başka kamera olması gibi — ve bu durumda görülen belirti, daha düşük bir hız değil, GVSP paket kaybıdır: `analyze-array`, ulaşılabilir fps değerlerini sıfırlar ve `** OVER-SUBSCRIBED**` mesajını yazdırır; bağlantı, yüksek sesli bir uyarıyla devam eder ve `pin_resolution: true` (katı mod) ile bir HTTP bağlantısı **reddedilir**. Çözümler arasında kamera sayısını azaltmak, uçtan uca jumbo çerçeveler kullanmak veya daha hızlı bir ağ kartı (NIC) kullanmak yer alır. `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`, test çalışmaları için reddetmeyi yüksek sesli bir uyarıya indirger — bkz. [Ortam Değişkenleri](#environment-variables).

### Dizi sağlığı — hangi alt sistem çerçeve kaybediyor

Bağlı bir dizinin `GET /api/camera/array/<array_id>/capability` değeri, **10 saniyelik** bir döngüsel pencerede yeniden değerlendirilen canlı bir
`health` bloğu taşır. Çerçeve kaybını,
her ikisi için de zıt çözümler gerektiren iki nedene ayırır;
hiçbirini belirtmeyen tek bir “eksik”
oranı bildirmek yerine:

| Alan | Anlamı | Hangi alt sistem |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (seri başına) | Çerçeve **ulaştı ancak yapısal olarak hatalıydı**— GVSP paket kaybı. |**Ağ**: kablo bütçesi, hız ayarı, NIC RX halkası, MTU |
| `never_arrived_rate_pct` (seri başına) | Çerçeve **hiç gelmedi**— kamera tetiklenmedi veya kamera tarafından hiçbir şey gönderilmedi. |**Tetikleme / senkronizasyon**: M8 kablosu, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Her biri için en kötü kamera oranı. | — |
| `per_cam_rate_pct` | Kamera başına birleşik eksiklik oranı (her iki neden bir arada). | — |
| `stable_for_seconds` | Her kameranın %0,01'in altında kaldığı süre. | — |

%5'in üzerinde olduğunda arka uç, bölünmeyi belirten bir `[array-health <id>] WARN` satırı kaydeder —
ilk ihlalde, ciddiyet aralığı değiştiğinde, durum devam ettiği sürece dakikada bir kez ve
durum düzeldiğinde bir kez. Bozuk yarısı, kamera ve neden başına ilk vuruşta arka uç günlük dosyasına
`[gvsp-corrupt <SN>]` satırını yazar, ardından her 60 saniyede bir toplama yapar; uygulamanın günlük paneli bunun yerine
`[array-health <id>]` satırını gösterir. Her değerlendirme yine de arka uç günlük dosyasına kaydedilir;
yazdırılan değerden bağımsız olarak sayaçlar her tamponla birlikte ilerler.

Aynı kayıt, tüm tahsisin dayandığı sayıyı bildirir:

| Alan | Anlamı |
| --- | --- |
| `wire_ceiling_mbps` | Ana bilgisayarın geçerli olan sürekli kablo bütçesi, MB/s. |
| `wire_ceiling_source` | Bu sayının kaynağı, kelimelerle — ör. `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` (Linux x86_64'te, `USB-capped 400 MB/s (…; sysfs: bus=usb (usb2/2-1/2-1:1.0))`) veya `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, `--wire-ceiling-mbps` (veya GUI'deki **Kablo Bütçesi** alanı) tarafından ayarlandığında. |
| `nic_is_usb` | Bir USB Ethernet adaptörü için `true` — yukarıdaki USB sınırına bakın. |

**Yorumlama:** `gvsp_corrupt_rate_pct` değeri sıfırdan farklı ve `never_arrived_rate_pct` değeri 0 ise, tetikleme ve kablo senkronizasyonunun mükemmel olduğu ve kaybın %100'ünün ağ
yolunda olduğu anlamına gelir — `--wire-ceiling-mbps` değerini düşürün ve yeniden bağlanın. Tersine bir durum ise
senkronizasyon kablosuna veya tetikleme hattına işaret eder.

> **`--target-fps`, bozuk çerçeveler için bir ayar değeri değildir.** GevSCPD hız ayarı
> bağlantı kurulduğunda bir kez yazılır; bu nedenle tetikleme hızını düşürmek, görev döngüsünü değiştirir,
> eşzamanlı yayın patlama hızını değiştirmez. Ölçülen 5 kat talep kesintisi herhangi bir iyileşme sağlamadı;
> kablo tavanını 240'tan 200 MB/s'ye düşürmek, aynı donanımın bozukluk oranını %10,4'ten
> %0,00'a indirdi.

> **Çalışır durumdaki bir dizi, LATTICE (M3C / M3M) ürün yazılımında kendi kendini yeniden planlayamaz.** Bu
> sorunu kendi başına düzeltemez; bağlantıyı kesip yeniden bağlayın, böylece bağlantı süresi seçici
> yeni tavan değeriyle yeniden planlama yapabilir.

### Belirti → çözüm

| Belirti (Dizi Ayarları / bağlantı / `analyze_array_network`) | Neden | Çözüm |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` 32'ye sıfırlanır (genellikle sürücü güncellemesinden sonra) | `ReceiveBufferLen`→256, `PendingReceives`→64 olarak ayarlayın; paneli yeniden açın (arka uç eski halka boyutunu önbelleğe almışsa yeniden başlatın) |
| Yeniden başlatma/kapatma sırasında takılma; daha sonra `Invalid class` WMI hataları, NIC etkinleştirilemiyor, USB sürücüler kayıp | Eski 2016 Realtek USB 10GbE sürücüsü → BSOD `0x9F` → zorla kapatma | Adaptör sürücüsünü ≥ v10.67 (2026) sürümüne güncelleyin, ardından yukarıdaki alıcı halkası ayarlarını yeniden uygulayın |
| Dizi, tam sensörde bağlanıyor ancak kare hızı düşük | Otomatik mod, tam sensörü korur ve kare hızının kablonun taşıyabileceği düzeye düşmesine izin verir | Bağlantı çıktısı listesindeki daha hızlı seçeneklerden birini seçin (`--binning 2` / `4` veya daha küçük bir `--width`/`--height`) seçin, jumbo çerçeveleri etkinleştirin veya bağlantıyı yükseltin |
| Dizi, hedef fps değerinin sağlıklı olduğunu bildiriyor ancak bunun sadece bir kısmını sağlıyor; `health.gvsp_corrupt_rate_pct` sıfırdan farklı, `never_arrived_rate_pct` 0 | Ana bilgisayarın tahmin edilen kablo bütçesi, gerçekte sürdürebileceğinden daha yüksek gösteriliyor (USB Ethernet adaptöründe, dar bir PCIe şeridinde veya paylaşılan bir yapıda tipik bir durumdur) | Daha düşük bir `--wire-ceiling-mbps` değeriyle yeniden bağlanın ve durum bloğunu tekrar kontrol edin. **Değil** `--target-fps` — GevSCPD hız ayarı bağlantı sırasında sabitlenmiştir |
| Linux: bir dizi çerçeve gruplarını kaybediyor ve çerçeve hızını düşürüyor, ancak NIC herhangi bir kayıp bildirmiyor; `lattice network`, `socket receive buffers too small` değerini listeler | Çekirdeğin soket alım tamponları varsayılan değerlerindedir, bu nedenle kamera paketleri çekirdekte düşürülür | `net.core.rmem_max` değerini / `rmem_default` değerini 32 MB'a yükseltin — bkz. [Linux ana bilgisayarları](#linux-hosts--socket-receive-buffers). .deb dosyası bunu kurulum sırasında yapar |
| Yayınlanan gruplarda kameralar eksik; `health.never_arrived_rate_pct` değeri sıfırdan farklı, `gvsp_corrupt_rate_pct` 0 | Tetikleme / senkronizasyon yolu — kameralar çalışmıyor, ağ sorunu değil | M8 senkronizasyon kablosunu ve `--line` değerini kontrol edin; her üyenin devrede olduğunu doğrulayın (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget`, `analyze-array` değerini aştı veya sıkı modda bağlantı reddi (`pin_resolution`; `array over-subscribes the wire`) | Kablo, kameraların ulaştığı herhangi bir hızda, eşzamanlı veya sırayla diziyi taşıyamıyor — kablonun paylaşabileceğinden çok daha fazla kamera var ya da üzerinde akış yapan başka kameralar var | Daha az kamera, uçtan uca jumbo çerçeveler veya daha hızlı bir ağ kartı. **ROI/binning yardımcı OLMAYACAKTIR** (paylaşım, çerçeve boyutundan bağımsızdır). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`, test ortamında geçersiz kılınır (paket kaybını kabul eder) |
| Dizi, yavaş bir ağda `slip-emit-and-capture` (seri) üzerinden bağlanır | Kameralar, kabloyu eşzamanlı olarak paylaşacak kadar yavaş bir hızda çalıştırılamaz, bu nedenle sırayla çalışırlar; çerçeveler aynı anda pozlanmaz | Jumbo çerçeveler veya daha hızlı bir NIC, eşzamanlı yakalamayı geri kazandırır; `--force-tier sim-capture-sim-emit` bunu ısrarla sürdürür (ve bunun yerine paket kaybına uğrar) |

---

## `chloros-cli daq`

Spektral sensör komutları. `pool-*` komutları, sensörü arka ucun kalıcı havuzundan yönlendiren ince HTTP istemcileridir; bu nedenle çalışan bir arka uca ihtiyaç duyarlar (bkz. [Arka Ucu Başlat](#start-the-backend)). Aktarım arkasındaki sistemin sorumluluğundadır; bu nedenle CLI ve SDK komut dosyaları, seri bağlantı noktası için rekabet etmek yerine tek bir canlı tanıtıcıyı paylaşır. `pool-discover`, bir DAQ-M'nin BLE MAC adresini öğrenmenin tek yoludur.

> **`chloros-cli daq --help`** (veya `-h` ya da `daq help`) komutu, `pool-*` alt komutlarını listeler (Linux'ta, o derlemenin içerdiği doğrudan donanım komutlarıyla birlikte); `chloros-cli daq <subcommand> --help` (veya `daq help <subcommand>`) ise her birinin seçeneklerini gösterir. Bunlar bağlanma, akış, kalibre edilmiş `.daq` dosyalarını kaydetme, kapak profillerini değiştirme ve bir DAQ'yi kameraya bağlama işlemlerini, DAQ-E için ise ağ ayarlarını, PTP'yi, yeniden başlatmayı ve ürün yazılımı güncellemelerini kapsar. DAQ, aynı havuzlanmış yolu kullanan `chloros_sdk.connect_daq_sensor()` ile Python'dan da çalıştırılabilir.
>
> **Her platformda `pool-*` komutlarını kullanın.** Bu komutlar Windows, Linux ve Jetson'da aynı şekilde çalışır. Windows'ta `daq discover` ve `daq list`, `pool-discover` komutunun takma adlarıdır; `pool-*` olmayan diğer tüm `daq` komutları `DAQ direct-hardware commands require the full daq package` mesajını vererek `1` çıkış koduyla sonlanır. Linux ve Jetson'da `daq discover`, `daq list` ve `pool-*` olmayan diğer `daq` komutları, DAQ aracının doğrudan donanım komutlarıdır: sensörü CLI'nin kendi sürecinde, arka uç havuzunun dışında açarlar (yani arka ucun, GUI'nin veya bir komut dosyasının tuttuğu bir sensörde değil) ve orada `daq discover`, `--only` ile `--timeout` seçeneklerini kabul eder, ancak `--json` seçeneğini kabul etmez. Bu referans yalnızca `pool-*` komutlarını belgeler.

### DAQ Sensörüne İlk Bağlantı İş Akışı

```bash
# 1. Scan every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U serial port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq pool-discover
chloros-cli daq pool-discover --only ble           # BLE only
chloros-cli daq pool-discover --json               # machine-readable

# 2. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect                                   # smart-detect
chloros-cli daq pool-connect --port /dev/ttyUSB0               # DAQ-U on a serial port (Windows: COM3)
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 3. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 4. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

#    Bind it to a camera, so that camera's captures use and save its readings
chloros-cli daq pool-bind --camera 213800234 CB-7C-A8-2E-5F

# 5. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 6. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` Referansı

| Alt komut | Amaç |
| --- | --- |
| `daq pool-connect` (smart-detect) | Arka uç havuzunda bir sensör açar. |
| `daq pool-connect --port PORT` (`-p`) | Belirli bir seri bağlantı noktasında DAQ-U. |
| `daq pool-connect --ble` | BLE üzerinden DAQ-M, MAC otomatik olarak taranır. |
| `daq pool-connect --mac MAC` (`-m`) | Bilinen bir MAC adresinde BLE üzerinden DAQ-M (`--ble`'i gerektirir). |
| `daq pool-connect --eth-host HOST` | Bilinen bir ana bilgisayar adı veya IP adresi üzerinden Ethernet üzerinden DAQ-E (`--eth`'i ima eder). |
| `daq pool-connect --eth` | Ethernet üzerinden DAQ-E, ana bilgisayar otomatik olarak algılanır (mDNS + ARP yedekleme; Windows ve Linux'ta boş ARP önbelleğinden çalışır). |
| `daq pool-connect --integration-time MS (-t) --frame-avg N (-f) --no-ae` | Entegrasyon penceresi / AE durumunu ayarla. |
| `daq pool-connect --no-stream` | Bağlan, ancak akışı henüz başlatma (`pool-stream --start` ile devam edin). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Cap düzeltme profili. Arka uçtaki varsayılan ayar `sunshine_cosine`'tir; `none` çıplak bir sensördür. Bir [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor) bunu yok sayar (sabit difüzör). **Sensörler çıplak olarak gönderilir, bu nedenle taktığınız kapağa bir isim verin:** varsayılan değer, varsayıldığı gibi kaydedilir (`cap_id_source=auto_default`) olarak kaydedilir ve işleme sırasında bununla ilgili bir uyarı verilir; çünkü güneş ışığı düzeltmesi altında çıplak bir DAQ-U, ~30 kat daha yüksek değerler okur. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Bağlanmadan, bağlanabileceğiniz sensörler için her taşıyıcıyı tarayın. **DAQ-M'nin BLE MAC adresini bu şekilde bulabilirsiniz.** `--timeout`, her aktarım için geçerlidir (varsayılan 5 saniye, 1-20). Havuzda zaten açık olan sensörler listelenmez — bağlı bir DAQ-M reklam yayınlamayı durdurur — bu nedenle bunlar için `pool-list` komutunu kullanın. `usb` yalnızca USB seri aygıtlarını listeler: bir bilgisayarın kendi seri bağlantı noktaları (`COM1`, Linux'ta `/dev/ttyS*`) sensör olamaz ve listeye dahil edilmez. |
| `daq pool-list` | Arka uç havuzundaki tüm sensörleri gösterir. |
| `daq pool-bind (--camera SERIAL \| --array ARRAY_ID) {SENSOR \| --clear}` | GUI’deki ışık sensörü seçimi gibi, bir kamerayı veya diziyi bir DAQ’a bağlar: yakalama işlemleri bu DAQ'nın okumalarını kullanır, eşleştirir ve kaydeder. `SENSOR`, `pool-list`'ten alınan bir sensör kimliği veya DAQ'ya erişilen adrestir. `--clear` (`SENSOR` yok) bağlamayı sonlandırır ve yakalamalar en güncel DAQ'ya geri döner. Bağlantı, arka uç yeniden başlatılana kadar veya o kamera, dizi ya da DAQ'nın bağlantısı kesilene kadar sürer: bir bağlantı kesilmesi (burada, SDK'dan veya uygulamadan), kimin ayarladığına bakılmaksızın, bağlantısı kesilen donanımın tüm bağlantılarını ve dizinin elemanlarının diziyle olan bağlantılarını sonlandırır. Kamera, açık projede bağlı olmalı veya daha önce bu bilgisayarda açılmış olmalı ve dizi de bağlı olmalı ya da açık projede bulunmalıdır; adından dolayı başka herhangi bir seri numarası veya kimlik reddedilir ve hiçbir şey bağlanmaz. |
| `daq pool-disconnect {--sensor-id ID \| --all}` | Serbest Bırakma — sensörü kim açmış olursa olsun, bu işlem çökmüş bir komut dosyasının havuzda bıraktığı sensörü de serbest bırakır (üzerindeki tüm kayıtlar önce tamamlanır). Havuzda bulunmayan bir sensörün serbest bırakılacak bir şeyi yoktur: `ID: already released (not in the pool)` mesajını yazdırır ve `0` ile sonlanır; arka uç tarafından reddedilen veya onaylanmayan bir serbest bırakma işlemi `1` ile sonlanır. SDK: `DAQSensorSession.force_disconnect()`. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | En son N spektrum karesi (varsayılan 1). |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Akışı devam ettir / duraklat. |
| `daq pool-record --sensor-id ID [--duration SEC] [-o DIR] [--device-name NAME] [--confirm-after S] [--heartbeat S]` / `--stop` | Bir .daq kaydını başlat / durdur. `-o`, arka uç bilgisayarda varsayılan olarak `~/Documents/DAQ Live View`'e ayarlanır (komutu çalıştırdığınız klasörün altındaki göreceli bir yoldur); `--duration` (`-d`, varsayılan `0` = `--stop`'e kadar). `--confirm-after S`, başlangıçtan S saniye sonra herhangi bir okuma gelmediğinde uyarı verir (varsayılan 20; 0 bu özelliği devre dışı bırakır); `--duration` ile, `--heartbeat S`, her S saniyede bir okuma sayısını yazdırır ve sayı artmayı bıraktığında uyarı verir (varsayılan 30; 0 değeri bu özelliği devre dışı bırakır). Sessiz kalan bir DAQ-E cihazındaki bir kayıt, arka uç yeniden bağlandığında aynı dosyada devam eder ve `--stop` boşlukları listeler ([Verim Vermeyi Durduran Bir DAQ-E](#a-daq-e-that-stops-delivering)). Ctrl+C tuşları, bir `--duration` kaydını erken sonlandırır ve bunu bildirir; `--duration` olmadan kayıt, Ctrl+C (`130`'ten çık) tuşuna basıldıktan sonra `--stop`'e kadar devam eder. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Çalışma sırasında kapasitans düzeltme profilini değiştirir. `--cap-id` gereklidir; `--cap-id none`, kapasitans düzeltmesini (çıplak sensör). Bir DAQ-E-S, ayar ne olursa olsun `as_recorded` değerini korur. |
| `daq pool-enable-ptp --sensor-id ID` | PTP'si kapalı olan bir DAQ-E için PTP'yi tekrar açar. Ayar üniteye kaydedilir, bu nedenle kapalı bırakılan bir ünite, bu komut çalıştırılana kadar kapalı kalır — zaman damgaları PTP ile senkronize edilmez. `pool-list`, böyle bir üniteyi (`!! PTP is switched OFF`) işaretler ve onun için bu komutu yazdırır. Chloros, bir üniteyi asla kendi başına tekrar açmaz, çünkü kapalı olması kasıtlı olabilir. |
| `daq pool-info --sensor-id ID [--json]` | Bir DAQ-E'nin kendi ayarları: donanım yazılımı (ve bu Chloros sürümünün daha yeni bir sürüm içerip içermediği), adresi ve bu adresi nasıl aldığı (DHCP / link-local / statik), sensör bunu çalıştırana kadar kaydedilmiş ağ ayarı (`--no-reboot` ile birlikte kaydedilmiş olan), açılışta akış ve PTP. Akışı bu bilgisayara ulaşmayan bir sensörde, yeni bir bağlantı üzerinden sensörü okur ve bunu bildirir. |
| `daq pool-set-network --sensor-id ID {--auto \| --static IP [--mask M] [--gateway G]} [--no-reboot] [--force] [--timeout S]` | Bir DAQ-E'yi DHCP (`--auto`) veya statik bir adrese (`--mask` varsayılan `255.255.255.0`) ayarlayın. Sensör, ayarı uygulamak için yeniden başlatılır ve önceki ayarlarıyla yeniden bağlanır; bu işlem için `--timeout` saniyeye kadar (varsayılan 45) bekler; `--no-reboot` yalnızca ayarı kaydeder. Bkz. [DAQ-E Cihaz Ayarları](#daq-e-device-settings). |
| `daq pool-set-auto-stream --sensor-id ID {--on \| --off}` | DAQ-E'nin, güç açıldığında herhangi bir yazılım bağlı olmadan veri akışına başlayıp başlamayacağı (sensöre kaydedilir). Chloros, her iki durumda da veri akışını kendisi başlatır. |
| `daq pool-reboot --sensor-id ID [--timeout S]` | DAQ-E'yi yeniden başlatır ve önceki ayarlarıyla yeniden bağlar (`--timeout`'e kadar bekler, varsayılan 45 saniye). |
| `daq pool-update-firmware --sensor-id ID [--timeout S]` | Bu Chloros sürümüyle birlikte gelen DAQ-E ürün yazılımını, daha eski bir sürümü çalıştıran bir sensöre yükleyin, ardından yeniden bağlayın (yaklaşık bir dakika; cihazın gücünü kapatmayın; `--timeout` geri gelmesi için varsayılan süre 45 saniyedir). Zaten güncel olan bir sensörde herhangi bir işlem yapılmaz. |

### Veri Göndermeyi Durduran Bir DAQ-E

Havuzdaki bir DAQ-E, 20 saniye boyunca spektrum vermezse, arka uç tarafından yeniden bağlanır. Sensöre güç sağlayan anahtarın güç döngüsü bunu gerçekleştirir: sensör bununla birlikte yeniden başlar ve talimat verilene kadar (`pool-set-auto-stream --on` hariç).

- **Öncelikle aynı sensör olup olmadığını kontrol eder.** Hiçbir şeyi değiştirmeden, sensörün eski adresinden sensörün adını, MAC adresini ve seri numarasını sorgular. Ayrıca sensörün `daq-e-<id>.local` adı altında da sorgulama yapar, ancak bu cevaba yalnızca sensörün kendi ağ bağdaştırıcısından (MAC) geldiğinde güvenir. Cevap veren başka hiçbir cihaza yeniden bağlanılmaz veya veri yazılmaz.
- **Oturum ayarlarıyla yeniden bağlanır:** entegrasyon süresi, kare ortalamalama, otomatik pozlama ve kapak profili, sensörün açılış varsayılanları değil, önceki halleriyle kullanılır. PTP açıkken devreye giren ve kapalı olarak geri dönen bir sensör, tekrar açılır. Ardından akışı ve bilgisayarın ilgili alıcısını yeniden başlatır.
- **Artan aralıklarla yeniden dener:** hemen, ardından 5, 10, 20 ve 40 saniye sonra, daha sonra sensör çevrimdışı olduğu sürece her 60 saniyede bir. Her denemede sensöre yalnızca kim olduğu sorulur. Yanıt veren ancak üç yeniden bağlanma denemesinden sonra hâlâ hiçbir şey göndermeyen bir sensör, olduğu gibi bırakılır ve rapor edilir (`auto-reconnect GAVE UP`). `pool-stream --start` tekrar dener.
- **`pool-list`, durumunu gösterir.** Sensör sessizken, `STREAM FAULT` satırı yeniden bağlanma işleminin ne durumda olduğunu belirtir. Satır, ilk spektrumla silinir ve sensör daha sonra `reconnected automatically N time(s)` değerini okur.
- **Kayıt aynı `.daq` içinde devam eder.** Dosya, her boşluğu iki satırla işaretler: `event_type` boşluktan önceki son spektrumda 5 ve boşluktan sonraki ilk spektrumda 6; her ikisi de yalnızca zaman damgası içerir. `pool-record --duration`, sensör yokken `!! STREAM LOST …`'i, geri döndüğünde ise `stream resumed after …`'i yazdırır. `pool-record --stop`, her boşluğu listeler ve boş bir dosya durumunda olduğu gibi (hiçbir okuma içermeyen bir kayıt, `1` ile sonlandırılır).
- **Bir kayıt, tek bir kalibrasyonu korur.** Kayıt devam ederken sensör farklı bir kalibrasyonla geri gelirse, kaydın geri kalanı başladığı kalibrasyonu korur ve durdurma uyarısında bu durum belirtilir. `pool-disconnect` + `pool-connect` komutları ise yeni kalibrasyonu alır.
- **Komutlarınız önceliklidir.** `pool-disconnect` komutuyla durdurduğunuz veya `pool-stream --stop` komutuyla duraklattığınız bir sensör, yeniden bağlanma işlemi halihazırda devam ediyor olsa bile asla yeniden bağlanmaz.
- **Sessiz bir sensörde `pool-info`**, yeni bir bağlantı üzerinden sensörü okur ve `!! stream NOT reaching this computer`'i ekler. Yanıt vermeyen bir sensör hemen rapor edilir. `pool-list`, yanıt vermeyi durduran bir sensör için artık PTP durumunu göstermez.
- DAQ-U ve DAQ-M bu kapsamda değildir. Bir DAQ-M, Bluetooth üzerinden kendi kendine yeniden bağlanır; USB'den çıkarılan bir DAQ-U ise yeniden `pool-connect`'e ihtiyaç duyar.

### DAQ-E-S — sabit difüzör ve eğim sensörü

**DAQ-E-S**, yazılım açısından önemli iki farkı olan bir DAQ-E modelidir. Diğer her şey — keşif, bağlanma, PTP, kayıt ve aşağıdaki cihaz ayarları — tam olarak bir DAQ-E'de olduğu gibi çalışır.**Difüzörü sabittir, bu nedenle kapak düzeltmesi gerektirmez.** Sunshine tarzı difüzör yerleşiktir ve fabrika kalibrasyonu ölçüldüğünde cihazın üzerindeydi; dolayısıyla bu kalibrasyon, aşağı doğru ışınım şiddetini zaten doğru şekilde okumaktadır. Chloros, ne istenirse istensin kapağını `as_recorded` (profil yok) olarak ayarlar: cihazın adını taşıyan bir sınır (`sunshine_cosine`, `fov_*`, `none`) bir uyarı mesajıyla göz ardı edilir ve kayıtlarında `cap_id_source=model`, dolayısıyla varsayılan sınır uyarısı bu sensör için asla tetiklenmez. Verilerine manuel olarak da sınır uygulamayın: Üstüne `sunshine_cosine` uygulanırsa değerler yaklaşık 11 kat yüksek okunur.

**Bu sensör, 3 eksenli bir ivmeölçer içerir.** Güneş ışığı difüzörünün kosinüs tepkisi yalnızca düz konumdayken geçerlidir; bu nedenle eğik bir sensör, başka hiçbir belirti olmaksızın yanlış bir ışık şiddeti bildirir — ve yansıma da bu hatayı devralır. Dolayısıyla her DAQ-E-S okuması kendi konum bilgisini taşır:

| Alan | Anlam |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Derece cinsinden açılar. `None`, donanım yazılımı açıyı tanımlanmamış olarak işaretlediğinde (serbest düşüş, ölü eksen). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Milig olarak ifade edilen ivme. `mag_mg` değeri ~1000'den çok uzaksa, bu, ünitenin hareket halinde olduğu ve o örnek için açıların anlamlı olmadığı anlamına gelir. |
| `cal_applied` | Ayarlı: eksenler ve açılar difüzörün koordinat sistemindedir (cihazın montaj kalibrasyonu uygulanmıştır). Ayarsız: ham kart koordinat sistemi; bu durumda ~2,3°'ye kadar gerçek olmayan bir eğim gösterilebilir. |
| `sample_age_ms` | Spektrum gönderildiğinde ivmeölçer örneğinin yaşı. |
| `temp_raw` | Kalibre edilmemiş yonga sıcaklığı **eğilimi** — yalnızca farklar, asla termometre okuma değeri değildir. |

`daq pool-latest --json`, bunları her çerçevenin `imu` anahtarı altında döndürür (DAQ-U, DAQ-M veya standart DAQ-E'de `null`). Bir DAQ-E-S'den kaydedilen bir `.daq`, aynı değerleri `imu_*` sütunlarında (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) sütunlarına aynı değerleri kaydeder; böylece okuyucu, bir bulut ile devrilmiş bir tripodu birbirinden ayırt edebilir. Diğer tüm modellerde bu sütunlar boştur.

Konum akışı ayrı olarak raporlanır: `daq pool-list`, cihaz kapalıyken `!! ATTITUDE STREAM FAULT (spectra unaffected)` değerini yazdırır ve sonrasında kaç arızadan kurtulduğunu belirtir. Buradaki bir arıza, spektral kayıtta asla bir boşluk oluşturmaz. `daq pool-discover`, üniteyi `DAQ-E-S` olarak listeler (yaklaşık 2 saniye içinde kimlik kontrolüne yanıt vermezse `DAQ-E` olarak).

### DAQ-E Cihaz Ayarları

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330

# A fixed address on the network this computer is on (192.168.1.x)
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1

# Back to the factory setting: DHCP, falling back to a 169.254.x.x
# link-local address when no DHCP server answers
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto

chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

- **`--static`, `--gateway` olmadan ağ geçidi kaydetmez** (daha önce ayarlanmış herhangi bir ağ geçidi silinir); bu, yönlendiricisi olmayan bir kamera ağı için uygundur.
- **Bu bilgisayarda statik bir adres bulunmalıdır.** Chloros, sensörün ağında alt ağında bu bilgisayarın adresi bulunmayan bir adresi reddeder: yeniden başlatmanın ardından sensöre ulaşarak ayarı geri değiştirmek mümkün olmaz. `--force` komutunu yalnızca bu bilgisayarın yeni alt ağa yeniden adreslendirileceği veya yönlendirileceği durumlarda kullanın.
- **Sensör kayıt yaparken yeniden başlatma reddedilir.** Önce kaydı durdurun (`pool-record --stop`). Yeniden başlatma işleminden hemen önce başlatılan bir kayıt, yeniden başlatma işlemiyle sonlandırılır; ve `pool-set-network`, `pool-reboot` ve `pool-update-firmware`, kaydın dosya adını belirten bir `Warning:` satırıyla sona erer (sensör daha sonra geri dönmese bile).
- **Yeniden başlatmanın ardından Chloros yalnızca sensörün kendisini yeniden bağlar.** Sensörün bulunduğu yerde — ve `--auto`'ten sonra `daq-e-<id>.local` adı altında da — sensörün adı, ana bilgisayar adı ve modelini içeren bir yanıt bekler ve yanıt veren adrese yeniden bağlanır. Ad altında gelen bir yanıt yalnızca o MAC adresiyle kabul edilir; bu nedenle, bir yönlendirici aracılığıyla sensöre ulaşan bir bilgisayardan veya varsayılan NAT ağındaki WSL2'den, DHCP tarafından yeni bir adrese taşınan bir sensör yeniden bağlanmaz: komut bunu bildirir ve sensörü sahip olduğu kapak ve pozlama ayarlarıyla geri getiren `pool-connect --eth-host <its address> …` komutunu görüntüler.
- **`pool-update-firmware`, firmware sürümünü okuyamadığı bir sensörü reddeder.** Flaşın ardından geri dönmesi için bekleme süresinden daha uzun süren bir sensör arızalı değildir: yanıt verdiğinde, yeni firmware ayarları girilmiş olarak yeniden bağlanır ve komut, sensörün yavaş olduğu konusunda uyarı verir.
- **Bir firmware güncellemesi için gelen TCP bağlantısı gerekir.** Sensör, görüntüyü almak için bu bilgisayara yeniden bağlanır. Chloros, sensöre yönlendiren adaptörde dinleme yapar (hangi adaptör olduğunu belirleyemediği durumlarda tüm adaptörlerde) ve yalnızca sensörün kendi adresine hizmet verir; diğer tüm bağlantılar hizmet verilmeden kapatılır. Windows’ta, yükleyicinin `Chloros Backend (TCP-In)` güvenlik duvarı kuralı bağlantıya izin verir (bkz. [Windows (.exe)](#windows-exe)); bunu engelleyen bir güvenlik duvarı, `… device never connected back on TCP <port> …` hatasıyla güncellemenin başarısız olmasına neden olur.
- **WSL2 altında, yansıtmalı ağ bağlantısını kullanın** (`networkingMode=mirrored` ve `%UserProfile%\.wslconfig`). Varsayılan NAT modunda, sensörün ağındaki bu bilgisayarın adresi WSL'nin kendi adresidir; bu nedenle her statik adres reddedilir (`--force` hariç), canlı akış WSL'ye ulaşmaz ve sensör görüntüyü almak için bilgisayara yeniden bağlandığından.
- **Sensör kimliği ve ana bilgisayar adı değişmez.** Adresi ne olursa olsun, bir DAQ-E yine de `daq-e-<id>` ve `daq-e-<id>.local` olarak kalır; dolayısıyla `--eth-host daq-e-<id>.local` onu bulmaya devam eder.
- **GUI'de ayarlar aynıdır**: Işık Sensörleri sekmesi, sensörün ayarları (dişli simgesi), Ağ. GUI, sensörle kendi bağlantısını sürdürür; bu nedenle, GUI'nin açık olduğu bir sensörü GUI üzerinden değiştirin: CLI'den yeniden başlatma, GUI'nin sensörle olan oturumunu sonlandırır.
- SDK'deki karşılıkları `DAQSensorSession.device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` ve `update_firmware()`'tir.

---

## `chloros-cli project`

Kaydedilmiş bir Chloros projesini açın, bağlanın ve çalıştırın (`cameras.json` + `sensors.json` + `project.json` içeren bir klasör). Her şey arka uç üzerinden yönlendirilir, böylece GUI ve CLI aynı donanım durumunu gösterir.

### Alt Komut Referansı

| Alt Komut | Amaç |
| --- | --- |
| `project open PATH [--relink FOLDER]` | Projenin cihaz listesini (kameralar, diziler, sensörler) yazdırır, dosyalarından okur ve kaynak dosyalarını kontrol eder (bkz. [Taşınan bir proje](#a-moved-project---relink)). Bu komut, projeyi arka uçta açmaz: proje, cihazları ve kayıtları olduğu gibi kalır. Projenin kaynak dosyalarının hiçbiri bulunamadığında `1`'ten çıkar. |
| `project devices PATH [--reconnect]` | Keşif işlemini listeler veya yeniden çalıştırır. |
| `project connect PATH [--cameras-only] [--sensors-only] [--packed {auto,on,off}]` | Kaydedilmiş tüm kameraları / dizileri / sensörleri bağlar. Her dizi, `lattice array-connect`'in yaptığı gibi bağlanır: en fazla 600 saniye sürer, adımları stderr'a yazdırılır (`[array <id>]    <s> s: <step>`; stdout ise JSON raporudur), ve bu süre içinde bağlanamayan bir dizi geri alınır — kameraları bulunduğu gibi geri yerleştirilir — ve `errors` altında raporlanır, asla bağlı bırakılmaz. `--packed`, her dizinin sıkıştırılmış 12-bit aktarımını ayarlar (varsayılan `auto`; bir proje, dizi başına ayar kaydetmez; bkz. [Sıkıştırılmış 12-bit aktarım](#packed-12-bit-transport---packed)). Her bir ışık sensörü, arka uçtaki DAQ havuzunda kaydedilmiş adresinde, kaydedilmiş entegrasyon süresi, kare ortalamalama, otomatik pozlama ve sınır değerleriyle açılır: sınır değeri seçilmemiş bir sensöre `sunshine_cosine` atanır, çıplak olarak kaydedilmiş bir sensör (`none`) çıplak kalır ve bir DAQ-E-S asla sınır değeri almaz. GUI'nin DAQ paneli havuzun bir parçası değildir: önce sensörü buradan kapatın. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | Adlandırılmış bir kamera veya diziden tek bir yakalama (`--prefix` varsayılan, `capture`). `--orient` (diziler): dizi ham veriyi kaydeder ve bu veri doğal formatında kalır — bu, `process`'in bundan ürettiği ürünlerin, `array-capture --orient` gibi yönlendirilmesi gerektiğini belirtir. Varsayılan: doğal. `project burst` / `stream` ve bir tarifin `capture` / `burst` / `stream` (`orient: true`) aynı seçeneği alır. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P] [--orient]` | Adı belirtilen bir kameradan veya diziden N karelik seri çekim (`-n/--count` varsayılan 5; `-i/--interval` kareler arası saniye, varsayılan 0). Dizi seri çekimleri, tekrarlanan senkronize grupları tekilleştirir (eski veri izleme mekanizması), böylece kısmi döngüye sahip bir dizi, bir karenin N kopyasını geri veremez; her yineleme için sonuçları yazdırır. Yarı yolda başarısız olan bir yakalama, seri yakalamayı durdurur ve `1`'ten, o ana kadar kaç tanesinin kaydedildiğini belirterek çıkar. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--orient]` | Döngü içinde diske yakalama: `--fps F`, bir saniyeyi yakalar (varsayılan 3), bir kameradan yakalama başına bir kare, bir diziden senkronize bir kare grubu yakalar (`project burst` ile aynı eskime izleme mekanizmasına sahip), ta ki `-n N` (varsayılan `0` = Ctrl+C tuşuna basılana kadar; bu durumda kaç tane kaydedildi yazdırılır ve `0`'ten çıkılır, ya da hiçbiri kaydedilmediğinde veya bir dosyada hata oluştuğunda `1`'ten çıkılır; `-n N` yakalama işlemini gerçekleştirmeden önce Ctrl+C tuşuna basılması durumunda `130`'ten çıkar). Yarı yolda başarısız olan bir yakalama işlemi, akışı durdurur ve `1`'ten çıkar; bu sırada, o ana kadar kaç kare kaydedildiğini bildirir. `--poll-interval` artık kullanılmamaktadır ve göz ardı edilir: ancak eski komut dosyalarının çalışabilmesi için hâlâ kabul edilir; bu durumda stderr'e bir not yazılır. |
| `project sensor read PATH NAME [--json]` | En son spektrum karesi. |
| `project sensor log PATH NAME [--seconds SEC] [-o DIR] [--device-name NAME]` | `--seconds` için bir .daq dosyası kaydeder için bir .daq dosyası kaydedin (varsayılan 10). Okuma içermeyen bir kayıt stderr'de bildirilir ve `1` sonlandırılır. |
| `project run PATH RECIPE.yaml` | Bir YAML/JSON yakalama reçetesini çalıştırın. `--dry-run`, çalıştırmadan doğrulama yapar. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Bir dizi için hizalama hesaplayın — bkz. [aşağıdaki bayrak tablosu](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Mevcut hizalama profilini yazdır. |
| `project align clear PATH NAME` | Önbelleğe alınmış profili sil. |
| `project align tweak PATH NAME --serial SN [--dx N] [--dy N] [--rotation-deg N] [--scale N]` | Bir bağımlı sunucunun dönüşümünü (varsayılanlar: `0`, `0`, `0`, `1.0`: değişiklik yok). |
| `project align export PATH NAME --to FILE` | Profili JSON olarak kaydet. |
| `project align import PATH NAME --from FILE [--no-validate]` | Kaydedilmiş bir profili yükle. |

#### `project align calibrate` Seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Hizalama yöntemi. **Bu yazımlar, `lattice align-calibrate`**'ten farklıdır; bu komut, `orb` / `akaze` / `phase` kısaltmalarını kullanır; bu bayrakta bu iki komut birbirinin yerine kullanılamaz. |
| `--model {translation, rigid, affine, homography}` | `affine` | Modeli sığacak şekilde dönüştür. |
| `--frames N` | `1` | Senkronize edilmiş kare anlık görüntülerini ortalamaya. |
| `--reference SN` | ana | Referans kamera serisi; diğer tüm üyeler buna göre çarpıtılır. |
| `--max-features N` | `5000` | ORB özellik sayısı sınırı. |
| `--ratio-threshold F` | `0.75` | Lowe'oran testi. |
| `--ransac-threshold-px F` | `3.0` | RANSAC iç nokta eşiği. |
| `--min-matches N` | `15` | **Kalite eşiği** — bu sayıda iç nokta eşleşmesinin altındaki çözümleri reddet. |
| `--max-reproj-err-px F` | `4.0` | **Kalite eşiği** — bu RMS yeniden projeksiyon hatası değerinin üzerindeki çözümleri reddet. |
| `--checkerboard RxC` | — | `--method checkerboard` için kart geometrisi, ör. `9x6`. |
| `--name PROFILE` | boş | Kaydedilen JSON dosyasına gömülü profil adı. **Dizi adı değil** — bu, konumsal `NAME`'tir. |

İki kalite kontrolü, bir kalibrasyonun çözümde başarılı olmasına rağmen yine de
kaydedilmeyi reddetmesinin sebebidir: bu kontrollerden herhangi birinde başarısız olan bir profil, sonraki her
yakalamayı sessizce yanlış kaydeder, bu nedenle kalıcı hale getirilmek yerine reddedilir.

### Taşınan bir proje (`--relink`)

Bir projenin `project.json`, her kaynak dosyayı — her görüntüyü ve ışık sensörü
kaydını — tam yoluyla adlandırır. Proje klasörünü veya tüm projeler klasörünü
başka bir sürücüye taşırsanız, bu yollar hala eski konumu gösterir.

Bir projeyi açmak, kendi başına düzeltebileceği sorunları giderir: projenin
klasörü içinde bulunan bir dosya, projenin şu anda bulunduğu klasörde aranır ve orada bulunan her yol
yeniden yönlendirilir ve kaydedilir (`project.json`, önce
`project.json.before-relink-<date>-<time>` olarak yedeklenir). Bu işlem her açılışta gerçekleşir — uygulamanın,
`chloros-cli project open`, `chloros-cli process`'in mevcut bir projeye eklenmesi, SDK'ler
ve `chloros-mcp`'ler. Bir yol, yalnızca orada bulunan bir dosyaya yeniden yönlendirilir.

Proje klasörünün dışında bulunan dosyalar — bir hub oturumu, "Klasör Ekle" içe aktarma işlemi —
bu şekilde bulunamaz. Dosyaların şu anda bulunduğu klasörün adını belirtin:

```bash
chloros-cli project open "N:/Chloros Projects/Field_A" --relink "E:/backup/Hub Projects"
```

`--relink`, dosyaların taşındığı klasörü veya onun üstündeki herhangi bir klasörü alır: eksik
her dosyanın klasörü, sonundaki klasör adlarına göre
(`Hub Projects/<session>/raw/<serial>`, ardından `<session>/raw/<serial>`, ...) altında aranır ve her
klasörün dosyaları, en fazla dosyanın bulunduğu tek yerden alınır. `project open`,
yeniden yönlendirilenleri, kaç kaynak dosya bulunduğunu ve geri kalanların hangi
klasörlerde olduğunu yazdırır. Hâlâ eksik olanlar varsa: bir uyarı, `0`'ten çıkın — proje geri kalanlarla birlikte açılır. Hiçbiri
bulunmadıysa: bu klasörlerin adlarını belirten bir hata ve `--relink`, `1`'ten çıkın; arka uç da aynı şekilde
`process` veya `project devices --reconnect` için böyle bir projeyi açmayı reddediyor (HTTP 409 `PROJECT_SOURCES_MISSING`).

### Örnekler

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Its hub captures moved to another drive: point it at them
chloros-cli project open "/home/user/Chloros Projects/Field_A" --relink "/media/backup/Hub Projects"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### Tarif DSL'si

`project run RECIPE.yaml`, bir dizi eylemi tanımlayan bir YAML veya JSON dosyasını kabul eder:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        auto_exposure: "Off"
        gain_db: 6.0
        auto_gain: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

Desteklenen eylemler: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. `apply`, `cameras.json`, `settings` anahtarlarını alır — `exposure_us`, `auto_exposure`, `gain_db`, `auto_gain`, `target_brightness`, … (eski adlar olan `exposure_time`, `exposure_auto`, `gain`, `gain_auto` takma ad olarak kabul edilir). Bu şekilde ayarlanan bir pozlama, kazanç veya hedef size ait olarak korunur: Chloros'un otomatik pozlama kontrolü (DAQ ile ölçülen pozlama dahil) bu ayarları değiştirmez. Manuel pozlama, kazancı olduğu gibi bırakır: otomatik modda, `gain_db` veya `auto_gain: "Off"` ayarını yapana kadar kamera, hedef parlaklığa doğru kazancı (tam aralığı boyunca) ayarlamaya devam eder. Daha sonra `auto_exposure: "Continuous"` komutu pozlamayı tekrar otomatik moda devreder ve `target_brightness_auto: true` komutu sabitlenmiş hedefi tekrar Chloros'un otomatik hedefine devreder. `burst` eylemi, `name` (gerekli), `count` (varsayılan 5), `interval` (saniye, varsayılan 0), `output`, `format`, ve `settings` (`apply` ile aynı kamera başına ayar yapısı) değerlerini alır; dizi patlamaları, `project burst` ile aynı yeni senkronize edilmiş grup izleme mekanizmasını kullanır.

`capture`, `burst` veya `stream` adımının `output` değeri varsayılan olarak `output`'e ayarlanır. Göreli bir `output` — varsayılan olarak dahil edilmiştir — `chloros-cli project run`'i çalıştırdığınız klasöre göre çözümlenir; tarif dosyasının klasörüne veya proje klasörüne göre değil (1.2.0 sürümünde arka uçun kurulum klasörüne yerleştirilmiştir).

Çalıştırın:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

**Her çalıştırma önce projeye göre kontrol edilir.**Her eylemin anahtarı, yukarıdaki fiillerden tam olarak biri olmalıdır — `- capture: {name: FrontLeft}`, `- action: capture` veya `- type: capture` değil — örneğin, `name` komutunda projenin `cameras.json` / `sensors.json` değerleri, `apply` / `capture` / `burst` / `stream` için bir ışık sensörü; bir `stream` için `count` > 0 ve bir `wait` için bir süre gereklidir. Bu tür bir hata içeren bir tarif**hiçbir** eylemi çalıştırmaz (`[recipe] error: …`, `[recipe] not run: …`, çıkış `1`), `stop_on_error: false` olsa bile. Bir adımın kullanmadığı anahtarlar bir `[recipe] warning:`'tir; aynı şekilde, tarifin kontrol ettiği ancak şu anda bağlı olmayan her dizi veya ışık sensörü de buna dahildir. Bir tarif asla cihazları bağlamaz: önce `chloros-cli project connect PATH`'i çalıştırın. `--dry-run` tarifi yazdırır, ardından aynı hataları ve uyarıları gösterir ve `[recipe] valid: …` / `NOT valid: …`, hiçbir şey çalıştırmaz ve tarifte bir hata olduğunda `1`'ten çıkar.

---

## Ortam Değişkenleri

| Değişken | Etki |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Arka uç URL'sini geçersiz kılar (varsayılan `http://127.0.0.1:5000`) — **yalnızca `lattice`, `project` ve `daq pool-*` komut aileleri tarafından desteklenir** (`lattice network-analysis`, bunun yerine `--backend-url`'i kullanır). Temel komutlar (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) `http://127.0.0.1:<port>`'i sabitler ve bu değişkeni yok sayar (IPv4 sabit değeri, Windows'un `localhost`→`::1` istek başına ~2 saniyelik cezayı atlatır), böylece her zaman yerel makineyi hedeflerler. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | Arka uç ortamında ayarlanır. `1`, aşırı abonelik reddini — `pin_resolution` ile bir dizi bağlantısı veya bir [geçici çoklu kamera komutu](#multi-camera-transient-sessions) (`error_code` `ARRAY_OVERSUBSCRIBED`) — ile bir dizi bağlantısı — yüksek sesli bir uyarıya indirger ve GVSP paket kaybını kabul eder. Yalnızca test amaçlı — bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model). |
| `CHLOROS_ARRAY_PACKED12` | Arka uç ortamında ayarlanır: `on` veya `off`, her dizi için [sıkıştırılmış 12-bit aktarım](#packed-12-bit-transport---packed) varsayılanını ayarlar için varsayılan olarak [sıkıştırılmış 12-bit aktarım](#packed-12-bit-transport---packed) ayarını belirler (varsayılan `auto`). Açıkça belirtilen bir `--packed` ayarı yine de önceliklidir. |
| `CHLOROS_DISABLE_UPDATE_CHECK` | `1` (veya `true` / `yes`) komutu, başlangıçta Linux CLI'sinin [otomatik güncelleme kontrolünü](#chloros-cli-update) devre dışı bırakır. |
| `CHLOROS_CLI_MODE` | CLI'nin kendisi tarafından ayarlanır; arka uca paralel işlemeyi etkinleştirmesini söyler. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0`, GVSP yedekleme testini atlar (yalnızca ICMP sonuçları). **Bu, jumbo'yu devre dışı bırakır, sadece günlüğü sessize almaz** — kamera her yolda yalnızca 1500'e kadar DF ping'lerine yanıt verir, bu nedenle jumbo'yu algılayabilen tek şey bu sondadır. Her kamera ve her bağlantı başına ~1 saniye tasarruf sağlar; ağ jumbo paketleri taşıyabilseydi, kablo kapasitesinin ~1,45 katı kadar maliyet oluşturur. Ayarladığınızda SDK bir uyarı verir. |
| `CHLOROS_GVSP_SESSION_REUSE` | Arka uç ortamında ayarlanır: `0`, kamera başına arka uç oturumu başına bir kez yerine her bağlantıda paket boyutunu ölçer. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | GVSP paket boyutunu N bayta sabitler; ölçüm işlemini tamamen atlar. Kalıcı olarak ayarlamak yerine komut başına ayarlamayı (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) tercih edin: sabitlenmiş bir boyut, önündeki ağa uyum sağlamayı durdurur ve jumbo paketleri taşıyamayan bir yolda 9000'e sabitlemek, `SC_ERR_TIMEOUT -1011` ile **her** yakalama işleminin zaman aşımına uğramasına neden olur. |
| `CHLOROS_TRANSIENT_AE_SETTLE_S` | Arka uç ortamında ayarlanır: [geçici çoklu kamera komutu](#multi-camera-transient-sessions) veya hiçbir şeyin tutmadığı bir kameranın `lattice capture`'i, bir kareyi saklamadan önce sahneden her kameranın pozlamasını ayarlamak için harcadığı en fazla saniye (varsayılan `5`). `0` bu özelliği devre dışı bırakır: her kameranın kendi otomatik pozlaması, pozlama sınırından başlayarak ilk kareden itibaren çalışır. |
| `CHLOROS_LOG_DIR` | Arka uç ortamında ayarlanır: ayrıntılı günlüğün kaydedileceği klasör. Windows'ta varsayılan `%LOCALAPPDATA%\Chloros\logs`, Linux'ta `~/.cache/chloros/logs` (bu ayar yapıldığında `$XDG_CACHE_HOME/chloros/logs`). .deb dosyasının kök hizmeti, `/var/log/chloros` değerini ayarlar; bu değer, `adm` grubu tarafından okunabilir — bkz. [Arka ucu hizmet olarak çalıştırma](#running-the-backend-as-a-service-headless). |
| `TMPDIR` (Linux) | Geçici dosyaların depolandığı konum. CLI, varsa `/mnt/ssd/tmp`'i otomatik olarak kullanır. |

---

## Çıkış Kodları

| Kod | Anlam |
| --- | --- |
| `0` | Başarılı. |
| `1` | Genel hata (çoğu alt komut hatası). Bir yakalama komutu, diğer dosyalar kaydedilmiş olsa bile yazılması istenen herhangi bir dosyanın yazılmasında hata oluştuğunda da `1` ile sonlanır ve `array-burst` / `array-record`, kaydedici bir hata bildirdiğinde `1`'ten çıkar — bkz. [Yazılmayan Dosyalar](#files-that-fail-to-write). `process`, bir çalıştırmanın dışa aktarımları doğrulanamadığında veya bir görüntü kaybolduğunda da sonlandırılır; ancak diğer ürünler yazılır. |
| `2` | Argüman hatası (bilinmeyen veya eksik bir bayrak; ayrıca her iki öncelik düzeyinde de `time-sync set-priority`). |
| `130` | Ctrl+C ile kesildi: kesintiye uğratılan herhangi bir komut — `lattice`, `project`, `daq` ve `process` gibi (arka uç çalışmayı durdurduktan sonra `process`, bkz. [`process` Notları](#notes)). Normal olarak Ctrl+C ile durdurulan bir komut — `lattice array-capture --continuous` / `--interval`, `lattice viewer`, `lattice continuous`, `multi-stream`, `align-stream`, `array-record`, `array-burst` ve `project stream` ile `-n 0` — bunun yerine yaptığı şeye göre sonlandırılır: `0` her şey yazıldığında, `1` herhangi bir hata oluştuğunda. |
| `143` / `129` | Komutun temizleme işlemi tamamlandıktan sonra SIGTERM / SIGHUP (128 + sinyal) ile sonlandırıldı: çalışan bir iş durduruldu ve kameraları geri yerleştirildi, arka planda bir işleme işlemi durduruldu. `lattice continuous` veya normal olarak Ctrl+C ile durdurulan diğer kayıtlar da `143`'ten çıkar, `0`'ten değil. |

`lattice network-analysis`, `1`'i `needs_force_slip` ve `error` kararları için sonlandırır, ve `selftest`, yedi kontrolün tamamı başarılı olmadıkça `1`'ten çıkar.

---

## Sorun Giderme İpuçları

- **"Giriş gerekli"** → Bu bilgisayarda `chloros-cli login EMAIL PASSWORD` komutunu bir kez çalıştırın.
- **"Proje klasörü bu bilgisayar için çok derin"** → Bu makinede Windows uzun yolları devre dışıdır (Windows varsayılan ayarı), bu nedenle hiçbir yol 260 karakteri aşamaz ve ürünler proje klasörünün yaklaşık 130 karakter altında yer alır. Hiçbir işlem gerçekleştirilmedi. Mesaj, sığabilecek en uzun proje klasörünü gösterir: daha kısa bir klasör kullanın (`-o` veya `chloros-cli set-project-folder`) ya da Windows uzun yollarını etkinleştirin ve Chloros'u yeniden başlatın. Windows yükleyicisi bunu "Windows uzun yollarını etkinleştir" seçeneği olarak sunar; bu seçenek varsayılan olarak işaretlidir; aksi takdirde Grup İlkesi'ni kullanın: *Bilgisayar Yapılandırması > Yönetim Şablonları > Sistem > Dosya Sistemi > Win32 uzun yollarını etkinleştir*.
- **"arka uç erişilemiyor"** → Chloros masaüstü uygulamasını (Windows) başlatın veya arka ucu kendiniz başlatın: Windows'ta `"C:\Program Files\Chloros\backend\chloros-backend.exe"` (kurulum klasörünüzdeki `backend` klasörü; `PATH`'te bulunmaz), Linux'ta `chloros-backend`. Uzak erişim durumunda `CHLOROS_BACKEND_URL`'i kontrol edin.
- **`lattice` komutları "LATTICE kamera sürücüleri bulunamadı" hatasıyla başarısız oluyor** → Arka uç, birlikte kurulu olan LATTICE kamera çalışma zamanını yükleyemedi. Her `lattice` komutu arka uçta çalışır; CLI'nin kendisi kamera sürücülerini barındırmaz. Chloros'u yeniden yükleyin: Windows yükleyicisi ve .deb dosyası, çalışma zamanını arka ucun yanına yükler.
- **Dizi bağlantısı / Dizi Ayarları'nda "FRAMES WILL DROP" veya "Etkinleştirmek için ROI'yi azaltın" mesajı görüntüleniyor** → Ana bilgisayar ağ kartı (NIC) alım halkası çok küçük (genellikle bir NIC sürücüsü güncellemesinden sonra 32'ye sıfırlanır). [Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması](#host-nic-setup--tuning-lattice-arrays) bölümüne bakın — `ReceiveBufferLen=256` ve `PendingReceives=64` değerlerini ayarlayın.
- **Makine yeniden başlatma/kapatma sırasında donuyor, ardından WMI `Invalid class` hatası veriyor / NIC etkinleştirilmiyor / USB sürücüler kayboluyor** → Güncel olmayan USB 10GbE adaptör sürücüsü, `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). Adaptör sürücüsünü güncelleyin — bkz. [Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması](#host-nic-setup--tuning-lattice-arrays).
- **Linux: yakalamalar root'a aittir, bu nedenle bunları silemezsiniz ve arka ucun günlüğü `~/.cache` dizininde bulunmaz** → Arka uç, `chloros-backend.service` aracılığıyla root olarak çalışır. Arka ucu sizin adınıza çalıştıran `chloros-backend@<user>.service`'e geçin — bkz. [Arka ucu hizmet olarak çalıştırma](#running-the-backend-as-a-service-headless). O zamana kadar root hizmetinin günlüğü `/var/log/chloros`'te bulunur (`adm` grubu tarafından okunabilir).
- **Linux: "Başka bir örnek zaten çalışıyor" (çıkış durumu 98) mesajını veren bir arka uç birimi `failed`'tir** → Başka bir arka uç zaten bu bağlantı noktasını tutuyor: aynı anda yalnızca biri çalışabilir. İki arka uç birimi etkinleştirilmişse, birini devre dışı bırakın: Kullanmadığınız birim için `sudo systemctl disable --now <unit>` — bkz. [Arka ucu hizmet olarak çalıştırma](#running-the-backend-as-a-service-headless). Yalnızca bu birim etkinleştirilmişse, sistemd dışında başlatılmış bir arka uç (`chloros-backend`, `chloros-cli` veya bir SDK komut dosyası tarafından) bağlantı noktasını tutuyor demektir: o arka ucu durdurun, ardından `sudo systemctl start <unit>` komutunu çalıştırın.
- **Jetson takas uyarısı** → Dosya tabanlı takas ekleyin; CLI, tam olarak `fallocate` / `swapon` komutlarını görüntüler.
- **`lattice`, `project` veya `daq` komutlarından gelen**"arka uca ulaşılamıyor"**hatası** → Çalışan bir arka uç yok: bu komutlar hiçbir zaman bir arka uç başlatmaz. `chloros-cli backend status` komutunu çalıştırın ve [Arka Ucu Başlatma](#start-the-backend) bölümüne bakın.
- **"Chloros'ta `daq net ...` kullanılamıyor"** (Linux ve Jetson) → DAQ-E'nin bazı ayarları MAPIR hizmeti için ayrılmıştır. Ağ ayarları, yeniden başlatma ve ürün yazılımı güncellemeleri için `pool-set-network`, `pool-reboot` ve `pool-update-firmware` komutlarını kullanın; diğer konular için MAPIR destek ekibiyle iletişime geçin.
- **"DAQ doğrudan donanım komutları için tam daq paketi gereklidir"** (Windows) → Aynı sensörü arka uç üzerinden çalıştıran `pool-*`'i veya Python'dan `chloros_sdk.connect_daq_sensor()`'i kullanın.

---

## Ayrıca Bakınız

- [Python SDK Referansı](sdk-reference.md) — her CLI komutunun programlama dilindeki karşılığı.
- [DAQ Işık Sensörleri](https://mapir.gitbook.io/chloros/daq-light-sensors) (Chloros kılavuzunda) — sensörler, kapasiteleri ve kalibre edilmiş aralıkları, kayıt, DAQ-E ağ bağlantısı.
- [LATTICE Kameralar](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) (Chloros kılavuzunda) — bağlantı, kamera ayarları, çekim modları, diziler.
- Çevrimiçi belgeler: `https://mapir.gitbook.io/chloros/cli`
