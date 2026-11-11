# Chloros Python SDK Referansı

**Sürüm:**

1.3.0**Oluşturulma Tarihi:**29 Temmuz 2026 19:19 ·**Güncelleme Tarihi:** 6 Kasım 2026**Paket:** `chloros-sdk` (PyPI)**Hedef Kitle:** LLM kullanımı için optimize edilmiştir; insan tarafından okunabilir.**Kapsam:** `import chloros_sdk` tarafından sunulan tüm genel sınıflar, işlevler ve yardımcı işlevler ile görüntü işleme, tek kamera kontrolü, senkronize diziler, DAQ sensörleri ve proje otomasyonunu kapsayan kopyala-yapıştır edilebilir örnekler ile AI asistanları için `chloros-mcp` sunucusu.

Yalnızca önemli noktalara göz atmak istiyorsanız, şuraya atlayın:
- [Kurulum ve Hızlı Başlangıç](#kurulum)
- [LATTICE Dizileri için Smart-Connect](#smart-connect-for-lattice-cameras)
- [DAQ Sensör Oturumları](#daq-sensor-sessions)
- [Kamera Yardımcı Programları](#camera-utilities)
- [Geçici Diziler, Hizalama Profilleri ve Dizinler](#transient-arrays-alignment-profiles--indices)
- [Proje Klasörü ve Tarifler](#project-folder--recipes)
- [Proje Otomasyonu](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Kendi İş Akışınızda Canlı Kareler](#live-frames-in-your-own-pipeline)
- [MCP Sunucusu (AI Asistanları)](#mcp-server-ai-assistants)

> **

1.3.0&#x27;da Değişiklikler** (mevcut bir 1.2.0 komut dosyasının algıladığı değişiklikler).
>
> **Bir 1.2.0 komut dosyasının ele alması gerekebilecek değişiklikler:**
>
> - **Arka uç tarafından onaylanmayan bir sürüm artık hata veriyor.** Oturumun açtığı bir dizide `CameraSession.disconnect()`, `ArraySession.disconnect()` / `force_disconnect()` hataları, `DAQSensorSession.disconnect()` / `force_disconnect()` açtığı bir sensörde ve `ChlorosProject.disconnect_all()`, arka uç serbest bırakmayı reddettiğinde veya zamanında onaylamadığında `ChlorosConnectError` hatası ortaya çıkar; çünkü cihaz hala tutuluyor olabilir. 1.2.0 sürümünde sessizce geri döndüler. Bir `with` bloğunun çıkışı ise hâlâ hiçbir zaman bir hata oluşturmaz.
> - **Ödünç alınan bir cihaz asla kaldırılmaz.** `with attach_array(...)` ve bunun üzerindeki açık bir `disconnect()`, dizinin çalışmaya devam etmesini sağlar (1.2.0 sürümünde, bloktan çıkmak cihazı kaldırırdı; GUI&#x27;nin sahip olduğu durumlarda bile); sonlandırmak istediğinizde `force_disconnect()`&#x27;i çağırın. Benzer şekilde, sensörün zaten havuzda olduğunu tespit eden bir `connect_daq_sensor()` (sıfır bayraklı akıllı algılama dahil), `disconnect()`&#x27;te ve `with` bloğundan ayrıldığında açık bırakır.
> - **PNG ve JPEG yalnızca `display` ve dizin ürünlerini alır.** `capture()` ile `format="png"` / `"jpg"` (`ext=".png"` / `".jpg"`) ve bir `radiance` veya `reflectance` seviyesindeyken, veya `processing="all"`, herhangi bir istekten önce `ValueError`&#x27;i yükseltir; 12 bitlik bir `raw` / `debayered`, arka uç tarafından reddedilir. Bkz. [PNG / JPEG](#png--jpeg-take-the-display-and-index-products-only).
> - **`connect_daq_sensor(sensor_id=...)`, o sensörü açar veya** bulduklarını listeleyen bir `ChlorosConnectError` hatası oluşturur; asla başka bir sensöre geri dönmez. Bkz. [Sabitlenmiş Aktarım](#pinned-transport).
> - **`frame_stream()` sessiz kalmak yerine hata sinyali verir:** `FrameFetchError`, reddedilme durumunda bir sonraki tikte (hiçbir DAQ içermeyen bir yansıma okuma), 60 saniye boyunca çerçeve gelmemesi durumunda ve 60 saniye boyunca yeni tetikleyici olmaması durumunda. Bkz. [Okuma başına bir tetikleyici](#one-trigger-per-read--group_id).
> - **Bir okuma zaman aşımı veya bağlantı kopması, her smart-connect çağrısından `ChlorosConnectError`** ve `analyze_array_network()` hatalarını tetikler; burada 1.2.0 sürümü, altta yatan zaman aşımı veya bağlantı hatasını tetiklemiştir.
> - **`connect_array()` varsayılan olarak 600 saniye bekler (`timeout`, 1.2.0 sürümünde 180 saniye) ve vazgeçtiği bir bağlantı geri alınır.** Çağrı, `timeout` kodunu arka uca gönderir; bu da her kamerayı bulunduğu duruma geri getirir ve bağlantı zamanında kurulamadığında hiçbir bağlantıyı kurmaz; çağrı, `ChlorosConnectError` (HTTP 504 `CLIENT_DEADLINE_PASSED`) hatasını tetikler. 1.2.0 sürümünde, kameraların açılmasından hemen sonra gerçekleşen bir bağlantı, çağrı süresini aşabilir ve çağrı hatası tetiklendikten sonra diziyi bağlayabilirdi. `connect_all()`, kaydedilen her dizi için aynı şekilde (1.2.0 sürümünde 120 saniye). Bkz. [`connect_array()` İmza](#connect_array-signature).
> - **`connect_array()`, sensörün tamamını korur.** `width` / `height` / `binning` olmadan her kamera tam sensöre ayarlanır ve kare hızı, ana bilgisayar ile ağın sağladığı seviyeye düşer; 1.2.0, kare hızını yüksek tutmak için kareleri küçültür veya birleştirir. Bkz. [Senkronize Dizi](#synchronized-array--arraysession-smart-prep).
> - **M3M (mono) kameradaki bir Bayer piksel formatı, o kameranın derinlik formatıdır.** M3M yalnızca Mono formatları sunar ve ön ayarların adları Bayer formatlarını ifade eder: 1.2.0 sürümünde kamera bu formatı reddetti ve kendi formatını korurken, `settings.pixel_format` Bayer formatını adlandırdı. `LatticeCamera(preset=...)`, `apply_preset()`, `set_format()`, bir `CameraSettings(pixel_format=...)`, `connect_camera(preset=...)` ve bir dizi&#x27;şimdi M3M üzerinde aynı derinlikte Mono formatını oluşturur: `"BayerRG8"` ve `high_speed`, `Mono8`&#x27;i verir; `"BayerRG12"`, `high_quality` ve `triggered`, `Mono12`&#x27;i verir; `cam.settings.pixel_format` ise ona ad verir. Aynı durum tersi için de geçerlidir: bir M3C asla Mono formatına dönüştürülmez, bu nedenle `"Mono12"` — bunlardan herhangi biri, `connect_array(pixel_format=...)`, `set_settings(pixel_format=...)` veya bir dizinin kaydedilmiş formatı — M3C&#x27;de, dizinin ana kamerası hangisi olursa olsun, `BayerRG12`&#x27;i verir. Aktardığınız bir `CameraSettings`, adını korur; bu nedenle aynı nesne, her kameradan kendi ailesindeki o derinliği ister. `connect_camera(preset=...)`, ön ayarın formatını korur (1.2.0 sürümünde, ön ayar uygulandıktan sonra kamera 12 bit olarak ayarlanır) ve bir ön ayarda açılan kamera, çektiği verileri artık `PRESETS`&#x27;e yazmaz. Bir kameranın reddettiği (`PixelFormat` listesinde bulunmayan, örneğin bir M3M&#x27;deki `RGB8` veya bu özelliğin bulunmadığı bir donanım yazılımındaki `BayerRG16` gibi) kendi adı olarak da adlandırılmaz: bir `LatticeCamera` bağlantısından sonra, `apply_settings()` veya `set_format()`, `cam.settings.pixel_format` ve `packed_transport`, kameranın aktardığı formatı belirtir ve yazdırılan bir satır, reddedilen formatı belirtir.
> - **`analyze_array_network()`, hangi bağlantının geçerli olduğunu gösterir:** `pixel_format=None` (eski adıyla `"BayerRG8"`), bağlantının ayarladığı formatları (Mono12 / BayerRG12) anlamına gelir ve `packed="auto"`, paketlenmiş aktarım kararını gösterir; bu nedenle, belirtilen fps değeri 1.2.0&#x27;&#x27;ten farklı olabilir. Bir `pixel_format`, o derinlikte bir M3M dosyasını yansıtır. Bkz. [Ağ Analizi Yardımcısı](#network-analysis-helper).
> - **`CameraSession.capture()`, bir `CaptureResult` döndürür** (hala bir çerçeve sözlükleri listesi) ile `.skipped`, `.failed`, `.daq_files` ve `.daq_matches` değerlerini içerir. Bir `ArraySession.capture()` sonucu da `.alignment`&#x27;i (hizalama çarpıtmasının uygulanıp uygulanmadığını ve uygulanmadıysa nedenini) ve `.oriented`&#x27;i içerir.
> - **Dizi yakalamaları, uygulamalarda olduğu gibi varsayılan olarak hizalanır.** `ArraySession.capture()`, `continuous()`, `capture_repeated()` ve `ArrayHandle.capture()` / `capture_smart()`, her üyenin `debayered`, `display`, `radiance`, `reflectance` ve dizin dosyalarını ana kameranın karesine yerleştirir ve bunları üyelerin örtüşme (kameraların ofsetleri kadar sensörden daha küçük, `XMP-mapir:AlignmentJSON` `"applied": true`) olacak şekilde kırpın; böylece bu dosyalar, Chloros uygulamasının aynı sahneyi yakaladığı dosyalarla aynı olur; `raw` ise orijinal boyutunda kalır. Daha önce, `aligned=True` kullanılmadan yapılan bir yakalama, her dosyayı orijinal boyutunda ve düzeltilmemiş olarak kaydederdi. `aligned=False` bu özelliği korur. `chloros-mcp`&#x27;`capture_array` ve `start_capture_run` aynı varsayılan ayarı kullanır. Bkz. [`ArraySession` Yöntemleri](#arraysession-methods).
> - **`connect_all()`, SDK&#x27;yi reddeden bir arka ucu belirtir.** Oturumu kapatılmış veya Copper&#x27;dan daha düşük bir plana sahip olan çalışan bir arka uç, &quot;Chloros arka ucu çalışmıyor&quot; şeklinde değil, kendi nedeni (HTTP 401 / 403) ile rapor edilir, &quot;çalışan Chloros arka ucu yok&quot; şeklinde değil; ayrıca her `connect_all()` çağrısı arka ucunu yeniden kontrol eder, bu nedenle oturum açtıktan sonra aynı proje nesnesi üzerinde çağrıldığında çalışır.
> - **Göreceli bir `output_dir` çağrısı, yerel bir arka uçta çalışır.** Göreceli yollar, gönderilmeden önce komut dosyanızın çalışma dizinine göre mutlak hale getirilir. 1.2.0 sürümünde arka uç, bunları kendi kurulum klasöründe çözümlemeye çalışıyordu ve bu işlem &quot;Erişim reddedildi&quot; hatasıyla sonuçlanıyordu.
> - **`numpy` artık SDK&#x27;nin bir gerekliliğidir** (live-frame çağrıları numpy dizileri döndürür).
> - **Bir&#x27;den farklı bir dışa aktarma düzeyi adı reddedilir.** `processing=` ve `levels=`, `raw`, `debayered`, `radiance`, `reflectance` veya `display` (veya `preview` gibi bir takma ad; `processing=` ve ayrıca `"all"`) seçin. Kameraya dokunulmadan önce başka herhangi bir isim reddedilir (HTTP 400 `UNKNOWN_LEVEL`: `capture()`, `ChlorosConnectError`, `grab()` / `frame_stream()` `FrameFetchError`); 1.2.0 sürümü bunun için `raw` değerini kaydetti veya döndürdü.
> - **LATTICE yakalamaları için Texture Aware reddedilir.** LATTICE yakalamaları içeren bir projede, `configure(debayer="Texture Aware (Slow, Highest Quality)")`, `ChlorosConfigurationError` hatasını tetikler; aynı şekilde, içe aktarımdan önce daha önce bu ayarı talep etmişti. Projenin daha önce kaydettiği bir Texture Aware değeri, `UserWarning` hatasıyla birlikte &quot;Standart&quot; olarak ayarlanır. LATTICE çekimleri her zaman standart demosaic işlemine tabi tutulur, bu nedenle 1.2.0 sürümü bu ayarı kabul edip göz ardı etmiştir; Chloros uygulaması da bu çekimler için bu seçeneği sunmamaktadır. Bkz. [Desteklenen Değerler](#supported-values).
> - **Kameralar, `keep_settings=True` hatası olmadığı sürece, bulundukları duruma geri döner.** `ArraySession.disconnect()` / `force_disconnect()`, her geçici işlevin sonunda (`capture_cameras_transient()`, `start_multi_camera_stream()`, `test_array_sync()`, `calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index()`) ve `start_camera_continuous()`&#x27;in durdurulması, her bir kamerayı bulunduğu duruma geri getirir: tetikleyici ve senkronizasyon hattı kablolaması, PTP ve oturum sırasında değiştirilen tüm kamera ayarları (pozlama ve kazanç, AE hedefi, sınırlar ve sönümleme, beyaz dengesi, siyah seviyesi, piksel formatı, birleştirme ve ROI, ters çevirme, kare hızı). `keep_settings=True` ise her kameranın ayarlarını kameranın kendi belleğine kaydeder, böylece kamera bu ayarlarla açılır; `clear_settings=True` ise bu belleği fabrika ayarlarına sıfırlar. Her iki durumda da kameralar serbest çalışır durumda bırakılır. Bir proje, kameraları bağladığında kendi kaydedilmiş ayarlarından kameralarını yapılandırır. Bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings).
> - **Her mod, bir projeyi aynı şekilde kaydeder.** Bir projeye bağlı bir kamera, dizi veya ışık sensöründe yapılan herhangi bir değişiklik — ister SDK, ister uygulama, ister CLI ister MCP sunucusu üzerinden yapılmış olsun — o projenin `cameras.json` / `sensors.json` klasörüne kaydedilir ve proje herhangi bir modda açıldığında cihazlar buradan ayarlanır; bir `ChlorosProject` ise arka uçta kendi klasörüne ad verir. Önceden, projeyi yalnızca uygulama kaydederdi. Bkz. [Tek proje kaydı, her mod](#one-project-record-every-mode).
> - **Birden fazla kameradan oluşturulan dosyalar her birini adlandırır.** Çok bantlı bir yığın, birkaç kameranın bantlarından oluşturulan bir canlı dizin ve bir dizi yakalama işleminin `composite/` görüntüsü, her bir kaynak kameranın seri numarasını, modelini ve kalibrasyon paketini listeler halinde taşır (`XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceCalBundleSha256s`, …) olarak listeler; EXIF `SerialNumber` ise seri numaralarını virgülle ayrılmış olarak listeler. Eskiden bu dosyalar kamera bilgisi taşımazdı ya da yalnızca ilk kameranın seri numarasını içerirdi. Bkz. [Birden fazla kameradan oluşturulan dosyalar](#files-made-from-several-cameras).
> - **Yalnızca bir `raw` dosyası kameranın kalibrasyon etiketlerini içerir.** MicaSense uyumlu set (`BlackLevel`, `RadiometricCalibration`,kanallı `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*` setleri) ve `CalibrationJSON` yükü, sensörün ham değerlerini tanımlar; bu nedenle bunlar yalnızca `raw` yakalama dosyasında yer alır. Diğer tüm dosyalar — bir yakalama`debayered`, `display`, `radiance`, `reflectance` ve dizin dosyaları, sürekli kayıt ve çoklu kameraakış kareleri ve her bir `process()` ürünü — kameranın kimliğini taşır (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) ve bir çekimde karenin kendi kayıt bilgilerini (`XMP-mapir:FrameJSON`: pozlama, kazanç, sıcaklık, piksel formatı, çekim zamanı). 1.2.0 sürümünde ham veri seti de taşınmaktaydı; bunu uygulayan bir araç (Pix4D, Metashape), ham değer olmayan piksellere bu seti uyguluyordu. Processing, bu tür bir dosyanın kalibrasyonunu seri numarası ve `CalBundleSha256` değerine göre bulur.
>
> **Diğer değişiklikler:**
>
> - **Bir dizinin otomatik kare hızı, dizinin sağladığı değere göre belirlenir.** Tetiklemelerinin %1’inden fazlasını kaybeden bir hız, tek bir değerlendirme yerine birkaç uzun gözlem üzerinden değerlendirilerek düşürülür ve düşürüldüğü hız ara sıra yeniden denenir. Kendiniz ayarladığınız bir hız ise değiştirilmez.
> - **Otomatik pozlama daha istikrarlıdır.** Sabit ışıkta sabit kalır, bir değişiklikten sonra saniyeler içinde dengelenir, küçük parlak noktaları göz ardı eder ve parlak güneş ışığında yanıt vermeyi durduran bir kamerayı yeniden çalışır hale getirir. Bkz. [Otomatik Pozlamanın Çalışma Şekli](#how-auto-exposure-behaves).
> - **`import_images(recursive=True)` yalnızca görüntülerinizi ve kayıtlarınızı sayar.** Bir projenin `camera_cal/` dosyasını içeren bir klasördeki kalibrasyon dosyaları artık görüntü ve kayıt olarak sayılmaz; bunları zaten içeren bir proje ise bu dosyaları çıkarır.
> - **`configure(radiometric_output=...)` geçerlidir.** Artık projenin `Export radiance` / `Export reflectance` ürünlerini ayarlar; önceden, her projenin kendi ürün ayarları bu ayarlara öncelik ederdi, bu nedenle `radiance`, `sensor-response` ve `all`&#x27;in tümü parlaklık ve yansıma değerlerini dışa aktarıyordu.
> - **`test_array_sync()`, kameraların açılma durumundan geçer.** Çalışma süresince kameraların PTP&#x27;sini açar, ilk tetiklemeden (`ptp_lock`) önce saatlerinin kilitlenip sabitlenmesini bekler — her kamera, açıldıktan yaklaşık 2 dakika sonra 120 saniye boyunca kilitlenir (`ptp_settle_s=` bunu ayarlar), çünkü yeni kilitlenmiş bir saat yaklaşık 2 dakika boyunca ~200 us&#x27;ye kadar sapma gösterir — ve her kameranın PTP&#x27;sini bulduğu gibi geri ayarlar. Daha önceki bir testten beri hala senkronize durumda olan bir kamera için tekrar beklenmez. Eskiden, hiçbir DAQ-E&#x27;nin yanıt vermediği tek anahtarlı bir ağda PTP&#x27;yi kapalı bırakır ve her kameranın serbest çalışan saatini, senkronize olamayacak bir PTP saati olarak okurdu (&quot;PTP kilitlenmedi&quot;, her çalıştırmada).
> - **Bir `.daq` kaydı, kapağının seçilip seçilmediğini veya varsayıldığını belirtir.** DAQ-U, DAQ-M ve DAQ-E, kapaksız olarak gönderilir ve <sn><id>Chloros</id></sn>, bir kapağın takılı olup olmadığını göremez; bu nedenle, adı belirtilmemiş bir kapak olmadan bağlanan bir sensöre varsayılan olarak *varsayım* niteliğinde `sunshine_cosine` adı verilir. Her Chloros kaydı (şema 1.30), artık `cap_id`&#x27;in yanında `cap_id_source`&#x27;i de depolar: Bir kapağa ad verdiğinizde (`none` dahil) `operator`, varsayılan değer olduğunda ise `auto_default`. Kapak değeri varsayılan olarak belirlenen bir kaydı işlerken, projenin DAQ kapağı Otomatik olarak bırakıldığında, dosyaya `[daq-dls] WARNING` adı verilir: Güneş ışığı düzeltmesi uygulanmamış bir DAQ-U, yaklaşık 30 kat daha yüksek değer gösterir. Bağlantı kurduğunuzda **takmış olduğunuz kapak adını girin** (`connect_daq_sensor(cap_id=...)` veya `DAQSensorSession.set_cap()`, `chloros-cli daq pool-connect --cap-id` / `daq pool-set-cap` veya GUI&#x27;deki kondansatör açılır menüsü), ya da projenin DAQ kondansatörünü ayarlayın; uyarı kaybolacaktır.
> - **Windows&#x27;da artık pip konsol pencereleri açılmıyor.** Python&#x27;un kurulu olduğu bir bilgisayarda, arka uç ve her bir görüntü işleyicisi, başlangıçta bir kez `pip` komutunu çalıştırır ve her biri bir konsol penceresi açardı. Artık bunu yapmıyorlar.
> - **Canlı `.daq` kayıtları, elektrik kesintisine rağmen korunur.**Bir kayıt, sensörün arkasında her commit işleminde bir günlük ve tam disk senkronizasyonu ile yazılır; bu sayede yavaş bir disk, dosyayı geciktirebilir ancak okumaları asla geciktirmez. Bir commit işleminin ortasında durdurulan (elektrik kesintisi, zorla sonlandırma) bir kayıt, yanında `<name>.daq-journal` dosyasını bırakabilir:**ikisini birleştirin.** Chloros, dosyayı ilk okuduğunda günlüğü geri alır; bu sayede kayıt, durdurulmadan önce kaydedilmiş tüm verilerle açılır (yaklaşık son 3 saniye kaybolur); 1.2.0 sürümünde, yanlış bir anda meydana gelen bir elektrik kesintisi, dosyanın tamamını okunamaz hale getirebilirdi (&quot;veritabanı disk görüntüsü hatalı&quot;).
> - **Yoğun bir diskteki `DAQSensorSession.record_start()` artık sensörü durdurmaz**: okumalar kuyruğa alınır ve kendi iş parçacıklarında yazılır; yazılmayan bir okuma, `status()`&#x27;in `record_errors`&#x27;inde (`record_last_error` ile birlikte) sayılır.
> - **Doğrudan `LatticeCamera`:**Parça pozlama/kazanç içermeyen bir kare artık kameranın**gerçek** pozlamasını ve kazancını bildirir (yedekleme, tüm otomatik pozlama çalışması boyunca sabit kalabilecek önbelleğe alınmış bir değeri okur)ve `apply_settings(trigger_source='LineN')` / `set_trigger_source('LineN')`, Chloros&#x27;un kablo bağımlı kameralarının aldığı aynı 5 µs giriş parazit giderme işlemini uygular (filtrelenmemiş, bir bağımlı kamera tetik sinyali gönderilmeden kareleri tetikler).
> - **Yazılımla tetiklenen kameralar artık her iki tetiklemeden birini atlamıyor.** Yazılım tetikleme modundaki bir kamera artık, okuma işlemi sırasında gelen bir tetiklemeyi kuyruğa alıyor (veya Chloros, kameranın hazır olmasını bekliyor); bu, kablo senkronizasyonlu kameralarda zaten uygulanan bir davranıştı.
> - **`discover_daq_sensors()`, bir DAQ-E-S&#x27;ye `DAQ-E-S` adını verir.** Her Ethernet sensörü için `model`, `DAQ-E` idi; yaklaşık 2 saniye içinde kimlik kontrolüne yanıt vermeyen sensörler, `DAQ-E` olarak listede kalmaya devam eder.
> - **Yeni: `chloros-mcp`, AI asistanları için bir MCP sunucusu.** Claude Desktop, Claude Code, Cursor ve diğer MCP istemcileri, görüntüleri işleyebilir (proje oluşturma, yakalamaları içe aktarma, ayarları seçme, işleme, çalıştırmanın dışa aktardıklarını raporlama) ve LATTICE kameralarını, dizilerini ve DAQ ışık sensörlerini çalıştırabilir: bağlanma, önizleme, pozlama ve renk ayarlama, yakalama, kayıt. Araçları, bu SDK&#x27;nin yaptıklarını kapsar. SDK ile birlikte kurulur ve Linux .deb dosyası onu `/usr/bin` klasörüne yerleştirir. Bkz. [MCP Sunucusu (AI Asistanları)](#mcp-server-ai-assistants).
> - **Yeni: kamera yardımcı programları**, SDK&#x27;nin çağırdığı `chloros-cli lattice` komutları: renk profili, doygunluk / kontrast, parlak alanlar, beyaz dengesi (tek çekim, otomatik, sabit oranlar), ön ayarlar, filtre modelleri, ürün yazılımı durumu ve güncelleme, güç (durum kontrolü, uyku, uyandırma), hedef kalibrasyonu, ağ bağdaştırıcıları (kontrol, tahmin, düzeltme), dosyalarda vinyet düzeltmesi, DLS okuma ve ana bilgisayar durum kontrolü. Bkz. [Kamera Yardımcı Programları](#camera-utilities).
> - **Yeni: CLI&#x27;nin geri kalanı SDK çağrıları olarak**; böylece CLI, SDK ve `chloros-mcp` aynı özellikleri sunar (CLI, güvenlik nedeniyle yalnızca oturum açma, bir DAQ-E&#x27;nin yapılandırılması ve doğrudan DAQ sunucularını elinde tutar): tek bir komutla açılan kameralar (`start_camera_continuous()`, `start_multi_camera_stream()`, `capture_cameras_transient()`, `test_array_sync()`, `detect_array_master()`, `analyze_array_models()`), hizalama profili dosyaları (`calibrate_alignment_profile()`, `capture_aligned_multiband()`, `start_aligned_stream()`, `alignment_profile_info()`, `reorder_alignment_profile()`), dizinler (`index_catalog()`, `compute_index()`, `capture_live_index()`), PTP grandmaster (`time_sync_status()` ve benzerleri), proje klasörü (`project_folder()`, `set_project_folder()`, `reset_project_folder()`) ve tarifler (`run_project_recipe()`, CLI’nin kendi çalıştırıcısı). Işık sensörleri için: `connect_daq_sensor(cap_id=...)`, `DAQSensorSession.set_cap()`, `daq_bindings()` / `set_daq_bindings()`, `daq_band_presets()` ve `light_sensor_reflectance()` (iki sensörün Ortam + Nesne yansıma katsayıları ve indeksleri); kamera için: `CameraSession.status()` ve `capture_repeated()`; bir dizi için: `ArraySession.capability()` ve `set_member_settings()` (bir elemanın pozlaması, kazancı, ...). Bkz. [Geçici Diziler, Hizalama Profilleri ve İndeksler](#transient-arrays-alignment-profiles--indices), [Proje Klasörü ve Tarifler](#project-folder--recipes) ve [DAQ Sensör Oturumları](#daq-sensor-sessions).
> - **Geliştirilmiş LATTICE indeks ürünleri** (NDVI ve diğer indeksler), `process()`&#x27;ten, her işleme modunda.
> - **LATTICE OCN kameraları: güneş ışığı altında parlaklık, yansıma ve endeks ürünlerinde iyileştirme.** Bant ayrıştırma işleminden sonra filo düzeltmesi uygulanır — canlı olarak, yakalamalarda ve `process()`&#x27;te; RGN kameralarında değişiklik yoktur. Bkz. [Radyometrik Çıktı](#radiometric-output-lattice-multispectral-pipeline).
> - **Her yakalama, GUI&#x27;de olduğu gibi kullandığı DAQ okumasını kaydeder.** `CameraSession.capture()` ve `ArraySession.capture()` / `continuous()`, bir DAQ bağlandığında veya havuza alındığında, `.daq` ve onun `.match.json` dosyalarını yazar; 1.2 sürümünde, DAQ bağlandığında veya havuza alındığında, ham verili bir yakalama dahil edilirdi;0 sürümünde, ham veya debayered yakalama yalnızca `force_daq=True` ile kaydedilirdi. `daq=False` bunu devre dışı bırakır ve `daq_tolerance_ms=` eşleşme toleransını ayarlar; her iki sonuç da `.daq_files` ve `.daq_matches`&#x27;te neyin kaydedildiğini belirtir. Bir yakalama artık, pozlamanın her iki tarafındaki okumaları birleştirebilmek için DAQ&#x27;nın bir sonraki okumasını bekler. Bkz. [Bir yakalamanın kaydettiği DAQ okuması](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
> - **`ArraySession`&#x27;te canlı kareler ve sunucu tarafında sürekli bir yakalama vardır.** `grab()` ve `frame_stream()`, tek bir tetiklemeden numpy kareleri döndürür (proje API&#x27;sindeki `ArrayHandle.grab()` / `frame_stream()` ile aynı okuma) ve `continuous()`, senkronize edilen her grubu arka uçta kaydeder ve bir [`ContinuousHandle`](#continuoushandle) döndürür. Bkz. [Kendi İş Akışınızda Canlı Çerçeveler](#live-frames-in-your-own-pipeline).
> - **Bir dizi `grab()`, bir tetikleyiciye sabitlenmiştir** (`ArrayHandle.grab()` ve yeni `ArraySession.grab()`). 1.2.0 sürümünde, 20 adet dört-kamera `ArrayHandle.grab()` okumalarının 12&#x27;si iki tetikleyiciyi birden kapsıyordu. Artık her kare `group_id`&#x27;i taşıyor ve bir dizi `frame_stream()` her tetikleyiciyi bir kez veriyor. Bkz. [Okuma başına bir tetikleyici](#one-trigger-per-read--group_id).
> - **Bant başına ve hizalanmış kareler:** `grab()` / `frame_stream()`, kamera ve dizi tanıtıcılarında `band=`, `align=`, `bit_depth=` ve `refl_max=` değerlerini alır. Bkz. [Tek bir bandın dışa aktarılması](#exporting-a-single-band--band).
> - **Yeni `ArraySession` yöntemleri ve argümanları:** `set_target_fps()`, `force_disconnect()`, `capture(after_seq=..., orient=...)` (`CaptureResult.group_seq` ve `.failed` ile birlikte), `continuous()`, `connect_array(packed=...)`; `capture_repeated()`, ardışık grupları zincirler ve sürücü dolduğunda durur.
> - **`ArraySession.record()`, GUI açık olmadan kayıt yapar.** GUI&#x27;nin birleştirilmiş görünümü kapalıyken, arka uç görünümü kendisi oluşturur: dizininNDVI&#x27;si veya `expr=` olarak ilettiğiniz indeks. `RecorderHandle.stop()`, başarısız olan, hiçbir kare yazılmamış bir kayıt için `ChlorosConnectError` hatasını tetikler ve durdurma sonucunu `.result`&#x27;te tutar (`with` bloğunda bırakılması durumunda hata atılmaz: `rec.result`&#x27;i kontrol edin). Bkz. [`RecorderHandle`](#recorderhandle).
> - **Yeni `DAQSensorSession` yöntemleri:** `enable_ptp()`, `force_disconnect()` ve DAQ-E&#x27;nin kendi ayarları için `device_info()`, `set_network()`, `set_auto_stream()`, `reboot()` ve `update_firmware()`. `record_stop()`, `warning` ve `gaps` değerlerini döndürür. Bir kaydı kapatan yeniden başlatma işlemi, yanıtında bu kayda bir ad verir veya yeniden başlatma başarısız olduğunda, `ChlorosConnectError`&#x27;te (`recording_closed`; bkz. [DAQ-E Aygıt Ayarları](#daq-e-device-settings)) belirtilir. Bkz. [`DAQSensorSession` Yöntemleri](#daqsensorsession-methods).
> - **Veri aktarımını durduran bir havuzlanmış DAQ-E, arka uç tarafından** sahip olduğu ayarlarla yeniden bağlanır ve üzerindeki bir kayıt, dosyada boşluk işaretlenmiş olarak aynı `.daq` altında devam eder; `record_stop()`, boşlukları `gaps` olarak döndürür. 1.2.0 sürümünde sensör, bağlantısı kesilip yeniden bağlanana kadar sessiz kalırdı ve bu da kaydı sonlandırırdı. CLI referansındaki [Veri Göndermeyi Durduran Bir DAQ-E](cli-reference.md#a-daq-e-that-stops-delivering) bölümüne bakın.
> - **`open_project()`, kurulu bir SDK ile çalışır.** Wheel (yükleyicininve PyPI&#x27;ler) artık ihtiyaç duyduğu proje yükleyicilerini içeriyor; önceden, `open_project()`, `ImportError` hatasını tetikleyerek `project_cameras` / `project_sensors` hatası veriyordu. `connect_all()`, kaydedilmiş kameraları, dizileri ve ışık sensörlerini çalışan bir arka uç (ışık sensörleri ise DAQ havuzu aracılığıyla) bağlar ve aramasının imkânı olmayan bir kaydedilmiş cihaz, hiçbir açıklama yapılmadan atlanmak yerine, nedeni belirtilerek `errors` altında listelenir. Arka uçta (GUI’lerin, veya `chloros-cli project connect`&#x27;ler) bağlı ve çalışır durumda bırakılır; oysa daha önce bu durum bir hata olarak rapor ediliyordu (&quot;Kamera  <sn>zaten dizide </sn>mevcut<sn><id>&quot;). Bkz. [`ChlorosProject` Yöntemleri](#chlorosproject-methods).
> - **`ChlorosLocal()`, `process_folder()` ve `process_lattice_capture()`, Windows&#x27;ta Chloros&#x27;u bulur.** Yükleyicinin arka ucu yerleştirdiği yere bakarlar (varsayılan olarak `<install folder>\backend\`, `C:\Program Files\Chloros\backend\`, yükleyicinin kendi kaydındaki özel bir klasör) ve hiç aramaya gerek kalmadan halihazırda çalışmakta olan bir arka uca bağlanırlar. 1.2.0 sürümünde, `backend_exe=` parametresini belirtmediğiniz sürece, kurulu bir makinede &quot;Chloros arka ucu bulunamadı&quot; hatası veriliyordu.
> - **Başlangıçta kapanan bir arka uç hemen rapor edilir.** `ChlorosLocal()` veya bir akıllı bağlantı çağrısı, yanıt vermeden kapanan bir arka ucu başlattığında, çağrı saniyeler içinde sonlandırılır; `ChlorosBackendError` hatası `ChlorosLocal`&#x27;ten, `ChlorosConnectError` hatası ise smart-connect-connect çağrılarından `ChlorosBackendError` ve `ChlorosLocal` ile `ChlorosConnectError` hataları, çıkış kodu, çalıştırdığı komut ve arka ucun yazdırdığı son satırlarla birlikte tetiklenir. 1.2.0 sürümü, 60 saniyelik başlatma zaman aşımını bekledi ve nedenini belirtmedi. Bkz. [Arka Uç Otomatik Başlatma](#backend-auto-start) bölümüne bakınız.
> - **Arka uç, komut dosyaları arasında çalışmaya devam ediyor.** Bir `with ChlorosLocal(...)` bloğundan çıkıldığında, `process_folder()` / `process_lattice_capture()` geri dönüşü ve `chloros-mcp` çıkışı, SDK&#x27;nin başlattığı arka ucu artık durdurmaz; bir sonraki komut dosyası onu yeniden kullanır. Arka uç, `logout()`&#x27;te (Chloros uygulaması açık değilse veya bir çalıştırma devam etmiyorsa), `shutdown_backend()`&#x27;te veya arka uç ortamında onu kullanan hiçbir şey olmadığında 30 dakika sonra kendiliğinden durur (Arka uç ortamındaki `CHLOROS_BACKEND_IDLE_MIN` bunu değiştirir; `0`: asla).
> - **SDK&#x27;nin başlattığı bir arka uçtan kapatılması istenir, zorla sonlandırılmaz.** `shutdown_backend()`, arka ucun kameralarını ve ışık sensörlerini serbest bırakmasına ve kayıtları tamamlamasına izin verir; yalnızca 10 saniye sonra hala çalışıyorsa sonlandırılır. 1.2.0 sürümü onu anında sonlandırıyordu (Windows&#x27;da zorla sonlandırma), bu da o arka uca bağlı bir DAQ ışık sensörünün yanıt vermemesine neden olabilirdi. SDK&#x27;nin çalışmakta olduğunu tespit ettiği bir arka uç ise hâlâ olduğu gibi bırakılır.
> - **Linux&#x27;da, otomatik başlatma asla geçerli klasörden bir `backend_server.py` çalıştırmaz.** Yüklü bir arka uç olmadığında, bir arka uç içeren klasörde başlatılan bir komut dosyası onu çalıştırırdı; artık yalnızca yüklü arka uç başlatılır (veya `ChlorosLocal` için, `backend_exe=` tarafından belirtilen arka uç).
> - **`configure(indices=...)`**, her bir ön ayarı kamerasının bantları için projeye ekler, yeni bir proje dahil edilir ve eklenemeyen bir ad için uyarı verir (`UserWarning`).**`import_images()`**, `.daq` kayıtlarını `scan_count` olarak bildirir ve artık LATTICE yakalama klasörü hakkında uyarı vermez.
> - **`read_image_audit_tags()`, pip kurulumuyla çalışır.** Görüntünün meta verilerini kendisi okur; 1.2.0 sürümünde, Python yolunda masaüstü kurulumuna ait iç bileşenler olmadan `{}` sonucunu döndürürdü.
> - **`capture()`, hemen ardından gelen `connect_array()`**, &quot;HTTP 503 &quot;Array still warming up&quot; (Dizi hala ısınma aşamasında)&quot; hatasıyla sonlanmak yerine, dizinin ilk kare grubu için 20 saniyeye kadar bekler.
> - **`status()` bir durma durumu bildiriyor.** Yeni `stalled` alanı, yakalama döngüsü çalışırken hiçbir şey yayınlamadığında True olur ve `actual_fps` değeri 0 olur. 1.2.0 sürümünde `actual_fps`, durma sırasında son değerini koruyordu son değerini koruyordu; bu nedenle, durma durumundaki bir dizi `actual_fps > 0` değerini gösterirken, yakalama işlemleri 503 &quot;Yeni senkronize kare grubu yok&quot; hatasıyla başarısız oluyordu.
> - **Daha az eksik çerçeve seti ve 503 hatalı yakalama:** Artık her bir bağımlı kameranın tetikleme girişi parazitlerden arındırılmıştır.
> - **Sıkıştırılmış 12-bit aktarım** (`connect_array(packed="auto")`), tam sensör dizisi kare hızlarını yaklaşık üçte bir oranında artırır. Bkz. [Sıkıştırılmış 12-bit Aktarım](#packed-12-bit-transport-packed).
> - **ROI altındaki dizi pozlaması:** Küçük bir ROI artık bir elemanın pozlamasını sensörün serbest çalışma süresi (768 × 304&#x27;te 4,9 ms) ile sınırlamaz. Bkz. [Dizi Elemanlarında Pozlama](#dizi-elemanlarında-pozlama).
> - **Otomatik pozlama artık bir dizinin otomatik kare hızını düşürmez.** Kablo ile senkronize edilmiş bir dizi, ağının ve kameraların okuma hızının izin verdiği hızda çalışır; otomatik pozlama, bu hızın sağladığı süreyi doldurur, ardından kazancı artırır. Süreden daha uzun süren manuel pozlama, reddedilmek yerine bu süreye uyum sağlamak için tetiklemeyi yavaşlatır. Bkz. [Dizi Üyelerinde Pozlama](#exposure-on-array-members).
> - **`read_nodes()`**, otomatik pozlama çalışırken `frame_values` ve `notes` değerlerini ekler (bkz. [Otomatik pozlama altında `read_nodes()`](#read_nodes-under-auto-exposure--frame_values-and-notes)). Kamera bir pozlamayı veya kazancı reddettiğinde,**`set_settings()`** kabul ettiği aralığı belirtir ve o değer için kapatmış olduğu otomatik modu yeniden etkinleştirir; `set_settings(target_brightness_auto=True)` yenidir.
> - **`orient=`** `ArraySession.capture()` / `capture_fastest()` / `continuous()` / `burst()` ve `ArrayHandle.capture()` / `capture_smart()`, ürünleri kameranın yönüne göre döndürür. Bkz. [`ArraySession` Yöntemleri](#arraysession-methods).
> - **`DLS()`, sensörün kendi `mac_address` değerine ihtiyaç duyar.** Artık varsayılan MAC adresi yoktur (daha önce bir birimin adresiydi); MAC adresi olmayan `DLS()`, `DLSError`&#x27;i tetikler. Bkz. [DAQ-M bant büyüklükleri](#daq-m-band-magnitudes--dlsband_magnitudes).
> - **Windows yükleyicisi**, &quot;Windows uzun yollarını etkinleştir&quot; ve &quot;Chloros arka ucunu Microsoft Defender taramasından hariç tut&quot; seçeneklerini sunar; her ikisi de varsayılan olarak işaretlidir. Bkz. [Adım 1](#step-1--install-the-chloros-platform-package).
> - **Linux .deb dosyası**, sistem için Python SDK&#x27;yi yükler (`python3` (önceden sadece wheel dosyasını diske yerleştiriyordu), kamera akışlarının ihtiyaç duyduğu çekirdek soket alım tamponlarını artırır ve kullanıcı kimliğinizle çalışan bir arka uç hizmeti ekler. Jetson paketi, JetPack 6 için `chloros_1.3.0_arm64_jp6.deb`&#x27;tir, ve JetPack&#x27;in CUDA PyTorch&#x27;unu içerir. Bkz. [Adım 1](#step-1--install-the-chloros-platform-package) ve [Adım 2](#linux-deb).

---

## 60 Saniyede Mimari

SDK, Chloros arka ucu üzerinde ince bir Python katmanıdır (masaüstü GUI ve CLI&#x27;nin kullandığı Flask sunucusuyla aynıdır). Otomasyon için `chloros_sdk`&#x27;i içe aktarır ve üst düzey yöntemleri çağırırsınız; arka planda, her çağrı, 5000 numaralı bağlantı noktası üzerinden yerel arka uca yapılan bir HTTP isteğine dönüşür — `http://127.0.0.1:5000/api/...` (kasıtlı olarak `localhost` değil; bu, Windows’ta önce `::1`’e yönlendirilir ve yalnızca IPv4 destekleyen bir arka uca yapılan her istek için yaklaşık 2 saniye sürer). Arka uç, donanım havuzuna — kameralar, DAQ sensörleri, hizalama profilleri, kare tamponları — sahiptir; böylece SDK komut dosyaları, seri bağlantı noktaları veya ağ kartı bant genişliği için rekabet etmeden GUI ile bir arada çalışabilir.

Kullanacağınız üç arayüz vardır:

1. **`ChlorosLocal` + serbest işlevler** (`process_folder`, `process_lattice_capture`) — Görüntü işleme boru hattı. Tek bir Python çağrısıyla tüm bir klasörü kalibrasyon / debayer / indeks dışa aktarma işlemlerinden geçirin.
2. **Akıllı bağlantı işleyicileri** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Canlı donanım için kalıcı bir arka uç oturumu açın. GUI ile aynı &quot;akıllı hazırlık&quot; akışı: ağ sondası, katman otomatik seçimi, PTP, AE tohumlama, GPIO tetikleyici yapılandırması.
3. **`ChlorosProject` / `open_project`** — Kaydedilmiş bir projeyi yükleyin (`cameras.json` + `sensors.json` + `project.json` içeren klasör), kameralarını, dizilerini ve ışık sensörlerini tek seferde bağlayın ve adlandırılmış tanıtıcılarla yakalamaları çalıştırın.

Yüzeyler 1 ve 2, henüz dinleme durumunda olan bir arka uç yoksa **yerel bir arka ucu otomatik olarak başlatır** (GUI/CLI&#x27;nin oluşturduğu aynı paketlenmiş ikili dosya) — böylece, önce bir arka uç başlatmanıza gerek kalmadan, yeni bir kabukta basit bir komut dosyası çalışır: `ChlorosLocal` (ve `process_folder()` / `process_lattice_capture()`), `connect_camera()`, `connect_array()`, `connect_daq_sensor()`, `discover_lattice_cameras()` ve `discover_daq_sensors()`. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` ve `analyze_array_network()` hiçbir zaman tek başına başlamaz: çalışan bir arka uç gerektirir. Devre dışı bırakmak için `auto_start_backend=False` parametresini geçirin (örneğin, hiçbir zaman başlatılmayan uzak bir arka uca yönlendirildiğinde). Bkz. [Arka Uç Otomatik Başlatma](#backend-auto-start). Surface 3 farklı davranır: `open_project()`, `auto_start_backend` parametresini kabul etmez ve `connect_all()` hiçbir zaman bir arka uç başlatmaz — `http://127.0.0.1:5000`&#x27;i bir kez yoklar ve yanıt gelmezse, doğrudan (arka uçsuz) `lattice_sdk` cihaz kontrolüne geri döner. Bunun için Arena SDK çalışma zamanı ve OpenCV (`cv2`) kütüphanesinin Python&#x27;a içe aktarılabilir olması gerekir; ancak SDK tekerleği bunları yüklemez; bunlar olmadan, kaydedilen her kamera ve dizi, nedeni belirtilerek hata olarak rapor edilir. Bir projenin ışık sensörleri için arka uçsuz bir yol yoktur: çalışan bir arka uç olmadığında, kaydedilen her sensör, nedeni belirtilerek hata olarak rapor edilir (bkz. [`SensorHandle`](#sensorhandle) bakınız). Yalnızca `proj.process()` ve `stream(..., overlays=True)`, bir `ChlorosLocal()`&#x27;i (otomatik başlatma yapar) gecikmeli olarak oluşturur.

Üçü de auth: `chloros-cli login`&#x27;i makinede bir kez çalıştırın veya masaüstü GUI&#x27;si üzerinden oturum açın. Geçerli bir oturum olmadan yapılan bir çağrının neye yol açacağı, arayüze bağlıdır (bkz. [İstisnalar](#exceptions)): `ChlorosAuthenticationError`, `ChlorosLocal`&#x27;ten (ve `process_folder()` / `process_lattice_capture()`), akıllı-connect çağrıları ve bunların oturumlarından kaynaklanan `ChlorosConnectError`, `status: "error"` ile `error_code`&#x27;ten kaynaklanan ve `analyze_array_network()`&#x27;ten kaynaklanan, ayrıca `connect_all()` raporunda yer alan bir giriş.

Gereksinimler:
- Python 3.7+ (paket tarafından belirtildiği şekilde; 3.10 sürümünde geliştirilmiş/test edilmiştir)
- Yerel olarak kurulmuş Chloros Desktop (arka uç ikili dosyası yükleyici içinde bulunur)
- **Herhangi bir ücretli Chloros+ kademesinde (Copper veya üstü)**aktif Chloros+ oturumu; ücretsiz**Iron**kademesinde SDK/CLI erişimi yoktur. Bu kural**sunucutarafında** uygulanır: SDK/CLI etiketli her istek, hem aktif bir oturum hem de ücretli bir plan içermelidir. Copper&#x27;ın altındaki planlar, `403` hatası ile birlikte `error_code: PLAN_UPGRADE_REQUIRED` (`ChlorosLicenseError`, `ChlorosLocal`, smart-connect çağrılarından `ChlorosConnectError`). Oturumu kapatmış bir çağrı yapan kişiye bunun yerine `401` / `AUTH_REQUIRED` verilir (`ChlorosAuthenticationError`, `ChlorosLocal`&#x27;ten; `ChlorosConnectError`, smart-connect aramalarından) — bu ikisi birbirinden farklıdır çünkü `chloros-cli login`&#x27;in yeniden çalıştırılması komutunu yeniden çalıştırmak, `401` hatasını düzeltir ancak plan yükseltmesi gerektiren `403` hatasını düzeltemez.
- Planın ödemesiz süresi içinde çevrimdışı kullanım desteklenir: kademesi, sunucu doğrulama önbelleğinden (5 dakika) veya imzalanmış, cihaza bağlı lisans önbelleğinden (aylık planlar için 30 gün, yıllık planlar için abonelik süresinin sonuna kadar). Bu süre dolduğunda plan ücretsiz seviyeye geçer ve makine sunucuya bir kez erişene kadar SDK/CLI erişimi durur. `chloros-cli status` (`GET /api/license-status`) hatası, nedenin görülebilmesi için ücretsiz seviyede erişilebilir kalır.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS veya daha yeni sürümler**ya da Jetson (JetPack 6). Ubuntu 20.04**desteklenmemektedir**: `.deb`&#x27;in bağımlılıkları, `libc6 (>= 2.35)` dahil olmak üzere arka ucun bağlandığı bileşenlerden türetilir ve Focal, glibc 2.31 sürümünü içerir.

---

## Kurulum

Python SDK, Chloros arka ucu üzerinde çalışan ince bir Python katmanıdır. Arka ucu başka bir makinede çalıştırmıyorsanız ([Uzaktan Arka Uç Modu](#remote-backend-mode-pip-only-host-via-tunnel) bölümüne bakın), **Chloros masaüstü paketinin yerel olarak kurulu** (Windows yükleyicisi veya Linux `.deb`) yüklemeniz gerekir; bu paket, DAQ ışık sensörlerini de içerir. Paket, arka uç ikili dosyasını, LATTICE kameralar için Arena SDK çalışma zamanını ve kalibrasyon paketlerini sağlar.

En son indirmeler: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### Adım 1 — Chloros platform paketini yükleyin

#### Windows (.exe)

1. İndirme sayfasından `Chloros Installer 1.3.0.exe` dosyasını indirin.
2. Yükleyiciyi çalıştırın ve sihirbazı takip edin. Varsayılan kurulum yolu `C:\Program Files\Chloros\`&#x27;tir.
3. Chloros&#x27;u en az bir kez başlatın ve Chloros+ hesabınızla oturum açın.

Yükleyici, her ağ profilinde Windows Güvenlik Duvarı üzerinden arka uç (`chloros-backend.exe`, başka hiçbir şey) için her ağ profilinde Windows Güvenlik Duvarı&#x27;ndan geçiş izni verir: kamera akışları, PTP, mDNS ve DAQ-E akışı için UDP girişi; ayrıca, bir DAQ-E cihazının yazılım güncellemesi sırasında bilgisayara geri bağlanması için yalnızca yerel alt ağdan TCP girişi ([`update_firmware()`](#daq-e-device-settings)). Kaldırıcı, her iki kuralı da kaldırır. Arka ucun HTTP API&#x27;si her durumda `127.0.0.1` üzerinde kalır.

Sihirbazın **Sistem ayarları** grubunda iki seçenek bulunur ve her ikisi de varsayılan olarak işaretlidir:

| Seçenek | Ne işe yarar | Sessiz kurulumdan vazgeçme |
| --- | --- | --- |
| **Windows uzun yollarını etkinleştir** | Proje klasörlerinin, Windows&#x27;un 260 karakterlik yol sınırından daha derinde bulunmasına izin verir. Uzun yollar devre dışı bırakıldığında (Windows&#x27;un varsayılan ayarı), ürünleri için çok derin olan bir proje klasörü, herhangi bir işlem yapılmadan reddedilir — bkz. [Sorun Giderme](#sorun-giderme-ipuçları). | `/MERGETASKS="!longpaths"` |
| **Chloros arka ucunu Microsoft Defender taramasından hariç tut** | Defender&#x27;ın, kamera tarafından yakalanan her dosyanın gerçektaramasını durdurur. Yüksek yakalama hızlarında tarama, hızın yaklaşık %30&#x27;unu tüketir: 9 kameralı bir dizide ROI 512&#x27;de ölçüldüğünde, sürekli ham yakalama tarama ile 52,7 yakalama/sn, arka uç hariç tutulduğunda 77,7 yakalama/sn hızında çalıştı. | `/MERGETASKS="!defenderexclusion"` |

Sürekli bir yakalama işlemi, Defender&#x27;ın yükünü `defender_cores` olarak bildirir (bkz. [`ContinuousHandle`](#continuoushandle)).

#### Linux amd64 (.deb)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

`dpkg -i` yerine `apt install ./…` ile yükleyin: apt, paketin zorunlu kıldığı değil, önerdiği Python SDK gereksinimlerini de yükler (bkz. [Adım 2](#linux-deb)).

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

JetPack 6 (L4T R36) üzerinde çalışan Jetson Orin / Orin NX / Orin Nano için. Buradaki PyTorch, JetPack 6&#x27;nın CUDA derlemesidir; bu nedenle GPU işleme, Orin&#x27;in GPU&#x27;su üzerinde çalışır; JetPack 6&#x27;nın yüklediği CUDA ve cuDNN kütüphanelerini kullanır (bu kütüphanelerin bulunmadığı bir görüntüde `sudo apt install nvidia-jetpack`).

#### Linux: alıcı tamponları ve arka uç hizmeti

Her iki .deb paketi de, LATTICE kamera akışlarının ihtiyaç duyduğu çekirdek soket alıcı tamponlarını 32 MB&#x27;a yükseltir (`/etc/sysctl.d/60-chloros-gige.conf`). Çekirdeğin varsayılan ayarında, kamera paketleri çekirdekte atılır ve dizi, kareleri ve kare hızını kaybeder; `chloros-cli lattice network`, tamponlar azaldığında bunu bildirir. CLI referansındaki [Linux ana bilgisayarlar — soket alım tamponları](cli-reference.md#linux-hosts--socket-receive-buffers) bölümüne bakın.

Başlıksız bir makinede, bir komut dosyasının çalışan bir arka uç bulabilmesi için arka ucu **kendi kullanıcı adınızla** bir hizmet olarak çalıştırın (`connect_all()` hiçbir zaman bir arka uç başlatmaz):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
```

Arka uç daha sonra sizin adınıza çalışır: yazdığı yakalamalar size aittir ve sizin `chloros-cli login`&#x27;inizi kullanır. Bu özelliği etkinleştiren makineler için root `chloros-backend.service` hâlâ yüklüdür, ancak root arka ucunun yazdığı her dosya root&#x27;a aittir. CLI referansındaki [Arka ucu hizmet olarak çalıştırma](cli-reference.md#running-the-backend-as-a-service-headless) bölümüne bakın.

Hizmetin çalıştırdığı bir arka uç paylaşımlıdır ve hiçbir zaman bir istemci tarafından durdurulmaz: SDK komut dosyaları ve `chloros-cli` onu buldukları şekilde kullanır (`ChlorosLocal.shutdown_backend()`, yalnızca `ChlorosLocal`&#x27;in kendisi tarafından başlatılan bir arka ucu durdurur) ve `GET /api/status`, birimi `service` olarak adlandırır. `sudo systemctl restart chloros-backend@$USER.service` ile yeniden başlatın. Paketin yükseltilmesi, kurulum sırasında arka uç hizmetlerini durdurur, bu nedenle çalışan bir komut dosyası kameralarını ve sensörlerini kaybeder ve ardından etkin olan her birini yeniden başlatır; paketi kaldırmak ise bunları durdurur ve etkin olanları devre dışı bırakır. Aynı anda yalnızca bir arka uç çalışır, bu nedenle bir birimi etkinleştirin: ikincisi başlatılmayı reddeder ve `failed` (çıkış durumu 98, yeniden başlatılmadı) durumunda kalır ve bunun nedeni `systemctl status` mesajında belirtilir. Üniteler arasında geçiş yapmak için, kullanmadığınız üniteyi devre dışı bırakın.</id></sn>

### 2. Adım — Python SDK&#x27;yi Yükleyin

**Chloros yükleyicisi, uyumlu bir SDK wheel dosyası içerir.** Her Windows yükleyicisi ve Linux .deb dosyası, GUI / CLI / arka uç sürümüyle tam olarak eşleşen bir `chloros_sdk-X.Y.Z-py3-none-any.whl` dosyasını diske yerleştirir. Senkronizasyonu sağlamak için PyPI&#x27;yi takip etmenize gerek yoktur.

#### Windows

Yükleyici, sisteminizdeki Python&#x27;ı kullanarak pakete dahil edilmiş wheel dosyasına karşı `pip install`&#x27;i otomatik olarak çalıştırır (`py.exe` başlatıcısı tercih edilir, aksi takdirde `python -m pip`&#x27;e geri dönülür). Herhangi bir işlem yapmanız gerekmez — `import chloros_sdk`, kurulumun başarıyla tamamlanmasının ardından Python ortamınızda çalışır. Sistemde Python yoksa, yükleyici bu adımı sessizce atlar ve GUI + CLI çalışmaya devam eder.

#### Linux (.deb)

Her iki .deb paketi de (x86_64 ve Jetson) sistem `python3` için SDK&#x27;yi yükler; bu sayede `import chloros_sdk`, kurulumdan sonra pip ve ağ bağlantısı olmadan çalışır. Paketler (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` ve proje yükleyicileri) `/usr/lib/python3/dist-packages`&#x27;e, SDK&#x27;nın `chloros-mcp` komutu ([MCP sunucusu](#mcp-server-ai-assistants)) ise `/usr/bin`&#x27;e yerleştirilir; bunların tümü paketin mülkiyetindedir: bir yükseltme bunları değiştirir ve paketin kaldırılmasıyla birlikte bunlar da kaldırılır.

SDK&#x27;nin kendi gereksinimleri olan `python3-numpy` ve `python3-requests`, **zorunlu değil, önerilen** bileşenlerdir (GUI, CLI ve arka uç kendi kendine yeterlidir): `sudo apt install ./chloros_<version>_amd64.deb` (Jetson&#x27;da `…_arm64_jp6.deb`) bunları yükler, `dpkg -i` ise yüklemez. Yükleme çıktısı, `python3`&#x27;in içe aktardığı SDK sürümünü, eksik olan tüm gereksinimleri, ve `python3`&#x27;in yolunda, paketin kopyasını gölgeleyen başka bir yerde bulunan `chloros_sdk`&#x27;i (örneğin, daha önce `pip install --user` ile yüklenmiş olan) bildirir.

Sistemin paketlerini görmeyen bir virtualenv ortamında, pakete dahil olan wheel dosyasını bu ortama yükleyin:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

İnternetten izole edilmiş Jetson dağıtımları için bu işlem tamamen çevrimdışıdır — SDK ve wheel dosyası zaten diskte bulunmaktadır.

#### Genel PyPI

Yalnızca pip kullanan ana bilgisayarlar için (Chloros masaüstü paketi yüklü değil; bu durumda SDK, başka bir makinedeki arka ucu çalıştırır, bkz. [Uzak Arka Uç Modu](#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

PyPI, en son kararlı sürümü barındırır. SDK&#x27;yi, çalıştırdığı Chloros arka ucuyla aynı sürümde tutun ([Sürümleme ve Uyumluluk](#versioning--compatibility) bölümüne bakın). DAQ-M yardımcı programı `DLS` için ayrıca `bleak`: `pip install "chloros-sdk[camera]"` (veya `pip install bleak`) gereklidir.

#### Doğrulama

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Chloros+ aboneliği gereklidir.** Tüm SDK çağrıları için aktif bir Chloros+ oturumu gereklidir. Her makine için bir kez `chloros-cli login user@example.com 'YourPassword'` komutunu çalıştırın; kimlik bilgileri `~/.chloros/` içinde önbelleğe alınır.

### Masaüstü Paketi&#x27;ne ihtiyacım var mı?

Çoğu iş akışı için pip paketi tek başına **yeterli değildir**. Her bir SDK yüzeyinin ihtiyaçları şunlardır:

| SDK Yüzeyi | Masaüstü Paketi Gerekiyor mu? | Neden |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Evet**(bir tane başlatmak için)**/ Hayır** (zaten erişilebilir bir arka uç varsa) | Çalışan bir arka uç kullanın (masaüstü uygulamasınınki veya başlatılmış başka bir komut dosyası); aksi takdirde yüklü olanı otomatik olarak başlatın: `/usr/lib/chloros/chloros-backend` (Linux) veya `<install folder>\backend\chloros-backend.exe` (varsayılan olarak Windows; `C:\Program Files\Chloros\backend\`, özel bir kurulum klasörü yükleyicinin kaydından okunur). Adını kendiniz belirlemek için `backend_exe=` değerini geçirin. |
| `chloros-mcp` ([MCP sunucusu](#mcp-server-ai-assistants)) | **Evet**(bir tane başlatmak için)**/ Hayır** (zaten erişilebilir bir arka uç varsa) | `ChlorosLocal` üzerine kurulmuştur, dolayısıyla yukarıdaki satırla aynıdır. |
| `read_image_audit_tags` | **Hayır** | İşlenmiş görüntünün kendi meta verilerini okur (yalnızca Python standart kütüphanesi). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Evet**(yerel)**/ Hayır**(uzak) | Arka uç üzerinden saf HTTP istemcileri. Yerel arka uç → masaüstü paketi gereklidir. Uzak arka uç →**tünel aracılığıyla** `backend_url=` (bkz. Uzak Arka Uç Modu — birlikte gelen arka uçlar yalnızca loopback&#x27;e bağlanır). |
| `ChlorosProject` / `open_project` | **Evet** | Arka uç üzerinden kaydedilmiş projeleri çalıştırır. |
| Doğrudan LATTICE sınıfları (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Evet** | Masaüstü paketinin içinde bulunan Arena SDK çalışma zamanı ortamına ve Python ortamınızda OpenCV&#x27;ye (`cv2`) ihtiyaç duyar; ancak ne masaüstü paketi ne de SDK bunları yükler (`pip install opencv-python`). Aksi takdirde, `CAMERA_AVAILABLE`, içe aktarıldığında `False` olur. `DLS` için ayrıca `bleak` gereklidir. |

### Uzak Arka Uç Modu (sadece pip ana bilgisayarı, tünel aracılığıyla)

> **Yüklü arka uç LAN üzerinden erişilemez.** Yalnızca
> loopback üzerinde dinlemektedir (`127.0.0.1` / `::1`), bu nedenle `backend_url="http://<lan-ip>:5000"`
> **yüklü bir Chloros&#x27;a** erişemez. Başka bir
> makinede bir arka ucu çalıştırmak için, loopback bağlantı noktasını kendiniz yönlendirin ve SDK&#x27;yi
> tünele yönlendirin:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"     # the tunnel endpoint
serials = ["213800234", "214000533"]   # master first

# auto_start_backend=False: the tunnel URL looks local, so without it a tunnel
# that is down would start this machine's own backend, if one is installed.
arr = chloros_sdk.connect_array(serials, backend_url=BACKEND,
                                auto_start_backend=False)
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                     backend_url=BACKEND,
                                     auto_start_backend=False)

# The backend opens every path on ITS machine: pass absolute paths valid there.
# (A relative path would be made absolute against this machine's working folder.)
arr.capture("/home/chloros/captures/run1", processing="raw")
```

Headless / CI / robotik ana bilgisayarlar, tam masaüstü kurulumuna sahip bir makineyi &quot;Chloros sunucusu&quot; olarak tutabilir ve diğer her yerde `pip install chloros-sdk` kullanabilir — ancak bunlar arasındaki aktarım, yukarıda belirtilen ve kullanıcı tarafından ayarlanan tüneldir, doğrudan bir LAN URL&#x27;si değildir.

> **Yalnızca pip içeren bir ana bilgisayarda `ChlorosLocal`.** `ChlorosLocal(backend_url=BACKEND)`, masaüstü kurulumu olmadan erişebileceği bir arka uca bağlanır: yalnızca bir arka ucu BAŞLATması gerektiğinde (yanıt veren yok ve `auto_start_backend=True`) yerel bir arka uç ikili dosyası arar. 1.2.0 sürümüne kadar, öncelikle ikili dosyayı arardı ve arka uca erişilebilse bile, yalnızca pip içeren bir ana bilgisayarda `ChlorosBackendError` (&quot;Chloros arka ucu bulunamadı…&quot;) hatasını verirdi. Bu makinede (`process_folder`, `import_images`) bu makinede kontrol edilir ve arka uç tarafından okunur; bu nedenle, uzak bir arka uç kullanıldığında, her ikisinde de aynı klasörün adı olmalıdır (örneğin, paylaşılan bir sürücü).

### Yalnızca DAQ İş Akışı (sadece pip barındıran ana bilgisayar)

`pip install chloros-sdk` tek başına bir DAQ sensörünü çalıştıramaz: bir DAQ sensörü her zaman `connect_daq_sensor()` ile bir Chloros arka ucu aracılığıyla çalıştırılır:

- **Bu makinedeki sensör:** Buraya Chloros masaüstü paketini yükleyin. `connect_daq_sensor()`, çalışmakta olan bir arka uç yoksa kendi arka ucunu başlatır.
- **Başka bir makinedeki sensör:** o makineye masaüstü paketi gereklidir; [Uzak Arka Uç Modu](#remote-backend-mode-pip-only-host-via-tunnel) içindeki tünel aracılığıyla arka ucuna erişin. Arka uç, kendi makinesindeki sensörü açar, bu nedenle o makineye bir DAQ-U takılı, bir DAQ-M onun Bluetooth menzilinde ve bir DAQ-E de ulaşabileceği bir ağda olmalıdır.

```python
import chloros_sdk

# http://127.0.0.1:5000 is the local desktop install's backend, or the tunnel.
with chloros_sdk.connect_daq_sensor() as daq:   # smart-detect USB / BLE / ETH
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Her iki durumda da arka uç, Chloros+ planını uygular: arka ucu çalıştıran makineye (`chloros-cli login` veya masaüstü uygulaması) herhangi bir ücretli seviyeyle (Copper veya üstü) giriş yapın. Oturumu kapatılmış bir arka uç ve Copper&#x27;ın altındaki bir plan, her ikisi de arka ucun nedenini (`AUTH_REQUIRED` / `PLAN_UPGRADE_REQUIRED`) içeren bir `ChlorosConnectError` hatası oluşturur.

---

## Hızlı Başlangıç

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## Üst Düzey API Dizini

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by CameraSession.capture / ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job
chloros_sdk.ContinuousHandle                      # handle for an array continuous() capture
chloros_sdk.GrabResult                            # dict returned by grab() / yielded by frame_stream()
chloros_sdk.FrameFetchError                       # raised by grab()/frame_stream() on a refusal

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()
chloros_sdk.daq_bindings() / set_daq_bindings(cameras=, arrays=)   # which DAQ feeds which camera
chloros_sdk.daq_band_presets(filter_code=None)    # band sets for Ambient + Object reflectance
chloros_sdk.light_sensor_reflectance(ambient_id, object_id, ...)   # two DAQs -> reflectance + indices
chloros_sdk.time_sync_status() / time_sync_peers() / time_sync_cameras()   # PTP grandmaster
chloros_sdk.restart_time_sync() / set_time_sync_priority(priority1=, priority2=)

# === Camera utilities (the CLI's lattice commands; by serial) ===
chloros_sdk.camera_color_profile(serial) / set_camera_color_profile(serial, ...)
chloros_sdk.camera_color_adjust(serial) / set_camera_color_adjust(serial, ...)
chloros_sdk.camera_highlights(serial) / set_camera_highlights(serial, value)
chloros_sdk.camera_white_balance_once(serial) / set_camera_white_balance(serial, ...)
chloros_sdk.camera_presets() / apply_camera_preset(serial, preset)
chloros_sdk.camera_filter_models() / camera_filter_model_info(name)
chloros_sdk.camera_firmware_status() / update_camera_firmware(serial)
chloros_sdk.camera_power_probe(serial) / sleep_camera(serial) / wake_camera(serial)
chloros_sdk.calibrate_camera_with_target(serial, ...)
chloros_sdk.network_adapters(...) / fix_network_settings()
chloros_sdk.apply_vignette_correction(input_dir, output_dir, ...)
chloros_sdk.read_dls(mac, ...) / probe_system(...) / system_info()

# === Transient cameras, alignment profiles, indices (cameras opened for one command) ===
chloros_sdk.start_camera_continuous(serial, ..., daq=None, daq_tolerance_ms=None)  # → CliJob; a DAQ bound to the camera records one .daq per frame (daq=False: none)
chloros_sdk.start_multi_camera_stream(serials, ...)   # → CliJob
chloros_sdk.capture_cameras_transient(serials, ...)
chloros_sdk.test_array_sync(serials, count, ptp_settle_s=None) / detect_array_master(line) / analyze_array_models(models)
chloros_sdk.calibrate_alignment_profile(serials, save_path, ...)
chloros_sdk.capture_aligned_multiband(profile_path, ...) / start_aligned_stream(profile_path, ...)  # → dict / CliJob
chloros_sdk.alignment_profile_info(path) / reorder_alignment_profile(path, ...)
chloros_sdk.index_catalog() / compute_index(input_path, ...) / capture_live_index(profile_path, ...)
chloros_sdk.CliJob                                # stats() / stop() / wait()

# === Project lifecycle ===
chloros_sdk.open_project(path, relink=None)       # → ChlorosProject
chloros_sdk.project_folder() / set_project_folder(path) / reset_project_folder()
chloros_sdk.run_project_recipe(recipe, project_path)  # `chloros-cli project run`
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics               # DLS: DAQ-M over Bluetooth; needs bleak

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosProjectFilesMissingError      # a ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True when chloros_sdk.project imported; open_project()
                                 # still raises ImportError without the project loaders
```

---

## Görüntü İşleme — `ChlorosLocal`

Ana boru hattı sınıfı. Oluşturulduğunda arka ucu başlatır (eğer yanıt veren yoksa ve `auto_start_backend=True` ise), projeleri oluşturur/yapılandırır, ilerlemeyi izler, çalıştırma sonrası özetleri döndürür.

### Oluşturucu

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

### Yöntemler

| Yöntem | Açıklama |
| --- | --- |
| `create_project(project_name, camera=None)` | Yeni bir proje oluşturur (isteğe bağlı olarak `"Survey3N_RGN"` gibi bir kamera şablonu ile). |
| `open_project(project_path, relink=None)` | Kaydedilmiş bir projeyi (klasörünü veya projeler klasöründeki adını) açar ve onu açık proje yapar. Taşınan bir projenin yolları, projenin şu anda bulunduğu klasöre yeniden yönlendirilir; `relink`, diğer eksik dosyaların şu anda bulunduğu klasörün adını verir. Arka ucun yanıtını `source_files` ile döndürür; hiçbiri bulunamadığında `ChlorosProjectFilesMissingError` istisnasını tetikler. Aşağıdaki **Taşınan proje** bölümüne bakın. |
| `import_images(folder_path, recursive=False)` | RAW/TIF/JPG/DNG görüntülerini **ve `.daq` ışık sensörü kayıtlarını** içe aktarır. `count` (görüntüler) ve `scan_count` (kayıtlar) değerlerini döndürür. Yalnızca klasörde ikisi de bulunmuyorsa uyarı verir. |
| `export_light_sensor(daq=True, csv=True)` | Projedeki her ışık sensörü kaydı için kalibre edilmiş `.daq` + `.csv` dosyalarını `<project>/Light Sensor/` dosyasına yazın. Bkz. [Işık Sensörü Kayıtları](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | İşleme düğmelerini ayarlayın. `indices`, ön ayar adlarıdır (`NDVI`, `GNDVI`, `OSAVI`, …) olup, her biri kamerasının bantları için projeye eklenmiştir; ön ayar olmayan veya bantların oluşturamayacağı bir ad (`NDRE`, tek bir kameranın sağlayamadığı RE ve NIR&#x27;ye ihtiyaç duyar), `UserWarning` adını alır ve indeks görüntüsü oluşturulmaz. `daq_log_path`, her karenin eşleştirildiği bir ışık sensörü kaydını (bir `.daq` veya `.csv` dosyası ya da bunların bulunduğu bir klasör) adını alır; bu kayıt, görüntülerle birlikte içe aktarılan kayıtlar yerine, her karenin LATTICE ve Survey3 ile eşleştirildiği kayıttır; yolun eksik olduğu veya kayıt içermediği belirtilirse, içe aktarılanlar kullanılır. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | İş akışını çalıştırır. `{"status": "complete", "async": False}` dosyasını döndürür, ayrıca arka uç tarafından sağlanıyorsa bir `summary` anahtarı döndürür — bkz. [Çalışma Sonrası Özet ve İpuçları](#post-run-summary--hints) — Çalışmanın dışa aktarımları doğrulanamadığında `export_check_failure` ve bir görüntü kaybolduğunda `image_failure` değerini döndürür. `wait=False`, `{"status": "started", "async": True}` değerini aynı anda döndürür. |
| `get_config()` / `get_status()` / `status()` | Arka uç durumunu inceleyin. |
| `logout()` | Oturumu kapat: önbelleğe alınmış kimlik bilgilerini temizler ve bu makinenin aygıt yuvasını serbest bırakır (sonuçta `device_deregistration`). Devam eden bir işleme çalışması durdurulmaz: başladığı modda tamamlanır (sonuçta `run_in_progress: true` ve `run_mode`) ve bir sonraki çalışma standart işlemeyi kullanır. |
| `shutdown_backend()` | Bu örnekte başlatılmışsa arka ucu durdurur; çalışmakta olduğu tespit edilen bir arka uç ise olduğu gibi bırakılır. Bir `with` bloğunun bırakılması veya örneğin çöp toplama işlemine tabi tutulması, arka ucu durdurmaz: arka uç, bir sonraki komut dosyası için çalışmaya devam eder ve 30 dakika boyunca hiçbir şey tarafından kullanılmadığında kendi kendine durur. Arka uçtan kapatılması istenir, bu nedenle önce kameralarını ve ışık sensörlerini serbest bırakır ve tüm kayıtları tamamlar; ancak 10 saniye sonra hala çalışıyorsa sonlandırılır. |
| `discover_cameras()` | **Bu örneğin arka ucu aracılığıyla** LATTICE kameralarını keşfet (`/api/camera/discover`). Bir dizi sözlük döndürür (`serial`, `model`, `ip`, …) — GUI/CLI&#x27;nin gördüğüyle aynı yapıdadır. Hiçbiri bulunmazsa veya arka uca ulaşılamazsa liste boştur. |
| `camera_capture(output_dir, format="tiff", **settings)` |**Arka uç aracılığıyla**(bu tanıtıcı tarafından otomatik olarak başlatılır) tek bir kare yakalar, böylece GUI/CLI ile aynı hazırlık aşamasından geçer (varsayılan 12 bit, havuzun yeniden kullanımı, gömülü kalibrasyon meta verileri). Hedefi `serial=` veya `device_index=` ile belirleyin; `exposure`/`gain`/`pixel_format`/`preset` değerlerini `**settings` olarak aktarın. Eski meta veri sözlüğünü döndürür (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Birleştirilmiş bir kameradan üst üste bindirilmiş önizleme kareleri üretir — arka ucun `/api/camera/<serial>/stream-annotated` yolu üzerinden ince bir MJPEG istemcisi üzerinde (zebra / ızgara / artı işareti / histogram / tepe noktası / sunucu tarafında çizilen nokta). `decode=True`, BGR dizileri üretir ve SDK&#x27;nın yüklemediği OpenCV&#x27;ye (`cv2`)gerektirir; ancak SDK bunu yüklemez; `False`, ham JPEG baytları üretir. Ayrıca proje bazında `ChlorosProject.stream(overlays=True)` olarak da erişilebilir. |

**Çalışmakta olan bir işlem, projesini korur.** Bir işleme çalışması devam ederken — bu komut dosyasının, uygulamanın, `chloros-cli process`&#x27;in veya `chloros-mcp`&#x27;in — arka uç, bir projenin oluşturulmasını, açılmasını, içe aktarılmasını veya işleme ayarlarının değiştirilmesini reddeder (HTTP 409 `PROCESSING_IN_PROGRESS`): `create_project()` ve `import_images()`, `ChlorosProcessingError` hatasını tetikler, ve `configure()`, `ChlorosConfigurationError`&#x27;i tetikliyor; çalışan moddan farklı bir modda olan `process()` ise `ChlorosProcessingError`&#x27;i tetikliyor (bir çalıştırma, başladığı modu korur), her biri arka ucun mesajıyla birlikte. Projeye kaydedilen bir yakalama, çalıştırma bittiğinde projeye eklenir (yanıtındaki `project_add` bunu belirtir). Çalışmanın bitmesini bekleyin (`get_status()`), ardından devam edin.**Taşınan bir proje.** Bir projenin `project.json`&#x27;i, her kaynak dosyasını (her görüntü ve ışık sensörü kaydını) tam yoluyla adlandırır; bu nedenle proje klasörünü — veya tüm projeler klasörünü başka bir sürücüye — taşımak, dosyaların eski konumu ile adlandırılmaya devam etmesine neden olur. Her açılışta sistem kendi başına düzeltebileceği hataları düzeltir: projenin klasörü içinde bulunan bir dosya, projenin şu anda bulunduğu klasörde aranır ve orada bulunan her yol yeniden yönlendirilir ve kaydedilir (`project.json`, önce `project.json.before-relink-<date>-<time>` olarak yedeklenir). Bir yol, yalnızca o konumda bulunan bir dosyaya yeniden yönlendirilir. Proje klasörünün dışında bulunan dosyalar — bir hub oturumu, bir Klasör Ekle içe aktarma işlemi — yalnızca `relink=<the folder they are in now>` (veya bunun üstündeki herhangi bir klasör) ile bulunur. `open_project()`, `source_files` sonucunu verir: `total`, `found`, `missing`, `missing_roots` (eksik dosyaların bulunduğu klasörler), `missing_outside_project` ve `relink` (nereden nereye yeniden yönlendirildi). Bazı dosyaları eksik olan bir proje, geri kalan dosyalarla birlikte açılır; hiçbir dosya bulunmayan bir proje ise açılmaz, ve `open_project()`, `ChlorosProjectFilesMissingError` hatasını tetikler (`.missing_roots`, `.audit`; HTTP 409 `PROJECT_SOURCES_MISSING`). CLI&#x27;deki `project open --relink` ve `chloros-mcp`&#x27;`open_project` (`relink_folder`) de aynı işlevi görür.

Garantili temizlik için bağlam yöneticisi olarak kullanın:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

### Işık Sensörü Kayıtları — kalibre edilmiş `.daq` + `.csv`

Bir DAQ-U / DAQ-M / DAQ-E, kalibrasyon paketi **olmadan** kaydedilebilir. Bu,
halka açık [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
kayıt cihazları (`record_daq.py`) varsayılan olarak yaptıkları şeydir: ham sensör sayımlarını yazar ve
dosyaya damga vururlar, böylece Chloros o sensörün fabrika kalibrasyonunu **seri numarasına göre** — önce yerel önbellek
sonra MAPIR Bulut&#x27;tan — ve içe aktarım sırasında uygular.

Chloros, sonucu kayıt başına iki ürün olarak
`<project>/Light Sensor/` altında geri yazar:

| Ürün | Nedir |
| --- | --- |
| `<name>_calibrated.daq` | Yeniden işlenebilir arşiv — canlı kayıtla aynı şemaya sahiptir, ancak artık onu üreten paketi belirtir. Yeniden içe aktarılması, **ikinci kez** kalibrasyon yapılmasına neden olmaz. |
| `<name>_calibrated.csv` | Sensörün kendi dalga boyu ızgarasında W/m²/nm cinsinden spektral ışık şiddeti; her okuma için bir satır, ayrıca fotometrik sütunlar (toplam güç, fotopik/skotopik lüks, PPFD ve bunun mavi/yeşil/kırmızı ayrımı, tepe dalga boyu). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Yalnızca paket içermeyen sensörler (DAQ-A).** Ham spektral sensör sayıları — *ışık şiddeti değil*. Aşağıya bakınız. |

`<name>`, kaydın kendi dosya adıdır. Farklı klasörlerdeki iki kayıt,
tek bir — iki kamera tarafından
aynı milisaniyede kaydedilen yakalama başına okuma değerini (`<capture>/daq/daq_data_<sensor id>_<ts>.daq`) paylaşabilir — ve her biri yine de kendi çıktılarını alır: ilki
`<name>` adını korur, sonraki ise adı **sonrasında** onu ayıran klasörleri ekler
(`<name>__<capture>_daq_calibrated.daq`), böylece kaydın tarihi ve saati yine başta yer alır.

`process()`, bu dışa aktarmayı aşamalarından biri olarak gerçekleştirir. Görüntü gerektirmez:
tek başına uçurulan bir ışık sensörü birinci sınıf bir iş akışıdır ve böyle bir projede yapı gereği
hiçbir görüntü bulunmaz.

**DAQ-A kayıtları ham sayımlar olarak dışa aktarılır.** DAQ-A ailesi, seri numarasına göre
paket sisteminden daha eskidir ve alınacak bir paketi yoktur — bunun yerine sahada bir
yansıtma hedefi kullanılarak kalibre edilir; bu yüzden hiçbir zaman bir pakete ihtiyaç duymamıştır. Bu kayıtlar,
`_calibrated` yerine `_raw` kökü altında dışa aktarılır: dosya içindeki bir bayrak yerine farklı bir dosya adı kullanılır,
çünkü bu bilginin, sadece dosya adı olarak e-posta ile gönderildiğinde de korunması gerekir.
`.csv` başlığı, `raw spectral sensor counts (NOT irradiance)` değerini belirtir ve
değerlerin dosya **içinde** karşılaştırılabilir olduğu konusunda uyarır — bu tam olarak hedef tabanlı kalibrasyonun
kullanıldığı gibi — sensörler arasında değil. Güce bağlı fotometrik sütunlar (toplam güç,
fotopik/skotopik lüks, PPFD), sayımlardan entegre edilmek yerine **NULL** olarak geri döner.

Paketi hiçbir şekilde alınamayan bir DAQ-U / DAQ-M / DAQ-E yine de **atlanır**,
ham olarak yazılmaz: bu durumda paket mevcuttur ve &quot;yeniden bağlanıp yeniden işleme&quot; gerçek bir tavsiyedir.

Eski **v1.01 / v1.02** kayıtları (bunlar bir DAQ-A-SD tarafından yazılır) okuma başına bir zaman dilimi taşımaz,
sadece dosyanın yazılma zamanını içerir. Görüntü↔aşağı doğru akış eşleştiricisi bunları hâlâ reddeder — bir
bir kareyi yazma zamanıyla eşleştirmek görünmez bir şekilde hatalı olur — ancak dışa aktarıcı bunları okur ve
`clock=daq_created_on` şeklinde CSV&#x27;ye yazdırılır, böylece ürün hangi saatte olduğunu belirtir.

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

Kalibrasyon paketi alınamayan bir kayıt (çevrimdışı veya dosyada kalibrasyonu olmayan bir
sensör), **nedeni ile birlikte** `skipped` altında rapor edilir. Bu kayıt, ham sayıları içeren bir &quot;kalibre edilmiş&quot; dosya olarak asla
yazılmaz — internete bağlanıp
işlemi yeniden çalıştırdığınızda dışa aktarma tamamlanır.

### İlerleme Geri Çağırmaları

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### İşlem Sonrası Özet ve İpuçları

İşlem tamamlandığında, `process()`, `GET /api/processing-summary` dosyasını alır ve gövdeyi `result["summary"]` olarak ekler. Dosya alma işlemi elinden gelenin en iyisi esasına göre yapılır ve başarılı bir dönüşü asla engellemez — özet mevcut değilse, `process()` basit `{"status": "complete", "async": False}` biçimine geri döner. `summary["hints"]` içindeki her bir giriş — önerilen düzeltmeyle birlikte tam cümleler, örneğin, bir çalıştırmanın neden sıfır çıktı ürettiği — de bir Python `UserWarning` olarak yeniden gönderilir; böylece, sözlüğü hiç incelemeseniz bile sıfır çıktı üreten çalıştırmalar kendi kendini teşhis eder:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]`, makine tarafından okunabilir kısımdır:

| Anahtar | Neyi sayar |
| --- | --- |
| `models` | Çalışmadaki kamera grupları. |
| `images_in_groups` | Bu gruplardaki kaynak görüntüler. |
| `targets_found` | Algılanan yansıma hedefleri. |
| `images_calibrated` | Çalışma sırasında yansıma ürünü oluşturulan görüntüler. |
| `images_failed` | Hata veren ve dışa aktarılmayan görüntüler. |
| `exported_files` | **Çalışma sırasında oluşturulan görüntü ürün dosyaları**çalışma tarafından yazılan**: her formattaki tüm ürünler (debayering, önizleme, parlaklık, yansıma, indeks, hedef renderlar). |
| `reflectance_files` | Bunlar arasındaki yansıma ürünleri (`Reflectance_Calibrated_Images`), ister hedef ister ışık sensörünün (DAQ) ışık şiddeti ile kalibre edilmiş olsun. Hedef olmamasına rağmen yine de yansıma yazan bir çalıştırmada bir ipucu olarak bu belirtilir; yalnızca hiç yazmayan bir çalıştırmada yansıma çıktılarının eksik olduğu bildirilir. |
| `clamp_masks_written` | Yanlarına yazılan [unmix klempleme maskeleri](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on) yanlarına yazılmıştır — bunlar ürün değil, eşlik eden öğelerdir, dolayısıyla `exported_files`&#x27;te yer almazlar (CLI&#x27;de de aynı şekilde ayrı olarak sayılırlar). |
| `captures_left_out` | Bu çalıştırmanın almadığı, projenin `captures` klasöründeki ham kareler. Bir çalıştırma, başlamadan önce önceki yakalama işleminin sonuçlarını bekler; bu nedenle, çalıştırma sırasında bir yakalama kaydedilmedikçe veya bekleme süresi dolmadıkça bu değer 0&#x27;dır. `summary["captures_left_out_note"]`, bunlarla ne yapılacağını belirtir. |
| `daq_recordings_exported` / `daq_recordings_skipped` | Işık sensörü kayıtları; kasıtlı olarak ayrı sayılır — bunlar farklı bir aşamadan gelir ve hiç görüntü içermeyen çalıştırmalar için de mevcuttur; dolayısıyla bunları dahil etmek, yalnızca DAQ içeren bir çalıştırmanın görüntü dışa aktarmış gibi görünmesine neden olur. |

Bunların yanı sıra: `summary["output_dirs"]` (yazılan her dizin),
`summary["light_sensor_export"]`, `summary["stopped"]` (kullanıcı çalışmayı
kesintiye uğrattığında geçerlidir, böylece kısmi sayımlar, beklenenin altında sonuç veren tamamlanmış bir çalıştırma olarak yorumlanmaz) ve
`summary["groups"]` (grup bazında dağılım).

`exported_files`, iş akışı tarafından **yazılırken** kaydedilir; daha sonra
projenin görüntü nesnelerinden taranmaz. Paralel ve GPU stratejileri kendi görüntü
nesnelerini oluşturur (GPU yolları için işçi alt süreçlerinde), bu nedenle eski tarama,
her böyle bir çalıştırma için `0 file(s) written` rapor ediyordu ve ardından — her şeyin
sorunsuz çalıştığı çalıştırmalarda — sıfır dışa aktarım ipucunu veriyordu. Bu sayıya göre komut dosyası yazarsanız, sağlıklı bir paralel çalıştırma artık
sıfırdan farklı bir sayı rapor eder.

Atlanan dosya raporları, okuyucunun her dosya için belirlediği nedeni —
okunamayan şema, eksik paket, yazma hatası — **tekilleştirilmiş** olarak bildirir; böylece tek bir nedenden dolayı atlanan yirmi dosya,
yirmi kez tekrarlanmak yerine tek bir neden olarak okunur.

> **Bir çalıştırma hiçbir görüntü üretmediğinde `process()` hatası ortaya çıkmaz.** Bu, SDK ile
> CLI&#x27;nin kasıtlı olarak farklılık gösterdiği iki noktadan biridir: `chloros-cli process`, &quot;ürünler talep edildi, hiçbiri
> yazılmadı&quot; durumunu bir hata olarak değerlendirir ve sıfırdan farklı bir değerle sonlandırılır; oysa SDK normal şekilde geri döner ve
> durumu bildirir. İş akışınızın boş bir çalıştırmada durması gerekiyorsa, bunu
> kendiniz kontrol edin — istisnanın olmamasına güvenmek yerine
> `summary` (veya proje klasörü altındaki dosyaları sayın),
> istisna oluşmamasına güvenmek yerine. Bunun genel nedenleri, bir giriş klasörünün
> yakalama olarak tanınmaması ve mevcut kameralar için geçerli olmadığı gerekçesiyle atlanan ürünlerdir (ör. yalnızca RGB
> kameralardan gelen parlaklık).
>
> Bir çalışmanın dışa aktarımları doğrulanamadığında da `process()` hatası ortaya çıkmaz: uyarı verir ve
> `result["export_check_failure"]` değerini ayarlar. Ürünler yazılır. Bu durumda durdurmak için o anahtarı kontrol edin. Bir görüntüyü
> kaybeden bir çalıştırma da aynıdır: bir uyarı verir ve `result["image_failure"]`&#x27;i ayarlar; bu durumda `chloros-cli process`
> sıfırdan farklı bir değerle sonlanır.

### Kullanışlı İşlevler

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "GNDVI", "OSAVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# process_folder() / process_lattice_capture() also take input_level=,
# array_alignment=, array_alignment_crop=, array_alignment_interpolation=,
# progress_callback=, poll_interval=, timeout=, processing_stuck_timeout=,
# backend_exe= and api_url= (NOT backend_url=). indices defaults to ["NDVI"].

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

Her biri kendi `with ChlorosLocal(...)`&#x27;i içinde çalışır; başlatılması gereken bir arka uç, geri döndüğünde çalışmaya devam eder, bir sonraki çağrı için çalışmaya devam eder ve 30 dakika boyunca hiç kullanılmadığında kendi kendine durur. Hiçbiri `auto_start_backend`, `radiometric_output` veya `daq_log_path` almaz; bunlar için `ChlorosLocal` ve `configure()` kodlarını kullanın. `process_lattice_capture()`, `camera`, `debayer` veya `ppk` değerlerini kabul etmez: panel algılamasını atlar ve standart debayer&#x27;ı kullanır.

### Desteklenen Değerler

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"TIFF (8-bit)"            # all-8-bit images are written as this; never raised
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer for Survey3 imagery, Chloros+ only.
                                         # Survey3 only: on a project holding LATTICE captures
                                         # (which always use the standard demosaic) configure()
                                         # or process() raises ChlorosConfigurationError; a
                                         # Texture Aware value the project saved earlier is set
                                         # to Standard with a UserWarning, as the app does. Needs a
                                         # CUDA GPU the backend's PyTorch can use (an NVIDIA
                                         # GPU, or a Jetson Orin's). Without one, process()
                                         # uses the standard debayer, and the backend prints
                                         # one "[DEBAYER] ... needs a CUDA GPU" line saying why.
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as Chloros's saved debayered (3-channel) product
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### Radyometrik Çıktı (LATTICE multispektral işleme hattı)

`process` iş akışının LATTICE multispektral (M3C/M3M) dışa aktarma seviyesi — `reflectance` (varsayılan), `radiance`, `sensor-response` veya `all` (görüntü başına geçerli tüm modlar) — projenin **&quot;Işınım dışa aktarımı&quot;**ve**&quot;Yansıma dışa aktarımı&quot;**** ürünlerini ayarlar: `radiance` yalnızca parlaklığı, `reflectance` yalnızca yansıma derecesini, `sensor-response` ise ikisini de dışa aktarmaz (yalnızca debayered ve önizleme ürünleri), `all` her ikisini de dışa aktarır. Debayering uygulanmış ve önizleme ürünleri bu seviyede değiştirilemez. `configure()` için özel bir anahtar kelime vardır:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

Gelişmiş kaçış yolu — ürünleri `custom_settings` aracılığıyla yazmak — hâlâ işe yarar, ancak bunun tüm ayarlar bloğunun yerini aldığını unutmayın (aşağıdaki uyarıya bakın). `"Export <product>"` anahtarlarını yazın: bir projenin kendi `"Export radiance"` / `"Export reflectance"` ayarları, eski `"Radiometric output"` anahtarına göre önceliklidir; dolayısıyla bu anahtar tek başına hiçbir şeyi değiştirmez.

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Export radiance": True, "Export reflectance": False},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (varsayılan) kamera parlaklığını, **zaman damgası eşleşen DAQ aşağı doğru ışınımı**ile böler; bu değer, kaydedilmiş bir `.daq` (DAQ-U/M/E)**veya görüntülerle birlikte bulunan DAQ-M özgün `.csv`**değerinden otomatik olarak hesaplanır; yerel olarak eksik olan kamera başına veya DAQ kalibrasyon paketleri, ilk kullanımda**AWS&#x27;den otomatik olarak alınır**. CLI, bunu-tür ürün anahtarları olarak sunar: `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.**Bu okuma kime ait?**Bir `.daq` yakalamasının kaydettiği değer, tanımladığı kamera(lar)ın okumasıdır — bu kamera(lar), yanındaki bir `<file>.daq.cameras.json` sidecar&#x27;da adlandırılır (tekli, senkronize ve dizi yakalamalar; ham seri çekimlerde her bir elemanın okuma değeri manifestosunda belirtilir) — ve**yalnızca bu kameralardan gelen görüntülerle** eşleştirilir. Kendi okuma değeri olmayan bağımsız bir kamera, bu nedenle artık başka bir kameranın DAQ&#x27;sını ödünç almaz; ona bir DAQ bağlayın veya bir kayıt yapın. Bir DAQ&#x27;nın kendi kaydı (`chloros-cli daq pool-record` veya GUI&#x27;de başlatılan bir kayıt) ya da o sidecar dosyası olmayan herhangi bir `.daq` (eski yakalamalar dahil), her kameraya hizmet etmeye devam eder.**Güneş ışığı altındaki OCN kameraları.** Her OCN kalibrasyon paketi, dar bantlı ışık altında (her bant için bir LED) oluşturulur; oysa güneş ışığı bantları karıştırır. Chloros 1.3.0, bant ayrıştırma işleminden sonra toplu bir güneş bazlı düzeltme uygular; bu da güneş ışığı altındaki OCN parlaklık, yansıma ve indeks ürünlerini iyileştirir. Bu düzeltme, bant ayrıştırmasının yapıldığı her yerde — `process()`, çekimler, `grab()` / `frame_stream()` ve canlı görüntü — bunu destekleyen OCN paketleri için çalışır; RGN kameralarda ise herhangi bir değişiklik yoktur. Daha önceki bir sürümle yakalanan OCN görüntüleri, çekildikleri kalibrasyonu barındıran bir bilgisayarda işlendiğinde bu düzeltmeyi de alır (Chloros, kamera bağlandıktan sonra kameranın kalibrasyonunu saklar); aksi takdirde eskisi gibi işlenir. Bu nedenle OCN parlaklık, yansıma ve indeks değerleri 1.2.0 sürümündekinden farklıdır.

> `custom_settings`, hesaplanan ayarlar bloğunun tamamını **değiştirir** (tasarım gereği, `configure()`&#x27;in diğer anahtar kelimelerini ve doğrulama işlemlerini atlar). Bunu kullandığınızda, yukarıdaki örnekte olduğu gibi, önem verdiğiniz her `Project Settings` anahtarını dahil edin.

#### İndeks piksellerini okuma ( NDVI ve diğer bitki örtüsü indeksleri)

**Bir tamsayı indeks rasteri, −1…1 aralığındaki bir harita değil, görüntü başına kontrast genişletmesidir.** `TIFF (16-bit)`, `TIFF (8-bit)`, `PNG (8-bit)` ve `JPG (8-bit)` endeks ürünleri — 8 bitlik bir yakalama işleminin yazıldığı 8 bitlik TIFF dahil —

```

DN = (value − IndexMin) / (IndexMax − IndexMin) × full_scale
```

burada `IndexMin` / `IndexMax`, **o karenin kendi en düşük ve en yüksek sonlu dizin değerleridir**. DN 0, −1 değildir ve tam ölçek +1 değildir.

Eşlemenin tersinir kalması için Chloros, her iki uç noktayı da damgalar:

| Etiket | Anlam |
| --- | --- |
| `Chloros:IndexScaling` | `linear_minmax` — aşağıdaki kural |
| `Chloros:IndexMin` | DN 0 olarak depolanan indeks değeri |
| `Chloros:IndexMax` | tam ölçek olarak depolanan dizin değeri |

Bunları okuyun ve ters çevirin; depolanan DN&#x27;yi önce depolanan veri türünün maksimum değerine göre 0…1 aralığına normalize edin, böylece tek bir ifade tüm tamsayı biçimlerini kapsasın:

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

> **`IndexMin == IndexMax`**, o karenin her pikselinin aynı dizin değerine sahip olduğu anlamına gelir. Raster orta ölçekte doldurulmuştur ve bölünecek bir değer yoktur — uç noktayı alın.

> **`Chloros:Index*` etiketlerinin olmaması, belirtilen bir eşlemenin olmadığı anlamına gelir.** LUT dışa aktarımı, pikselleri LUT eşik pencerenize göre ayarlanmış gradyan renklerden oluşan renklendirilmiş bir renderdır ve sonlu indeks pikseli olmayan bir karenin belirtilecek bir aralığı yoktur. Her iki durumda da bir eşleme olduğunu varsaymayın — bu, eksik bir `PixelScale` ile aynı kuraldır.**Genişletme işlemi görüntü bazında olduğundan, bir uçuşa ait iki kare DN açısından karşılaştırılamaz**— her biri kendi uç noktalarını taşır, bu nedenle aynı NDVI değeri farklı görüntülerde farklı DN değerlerine karşılık gelir. Bir uçuş genelinde nicel çalışmalar (bölgesel istatistikler, sabit eşikler, zaman serileri) için**`TIFF (32-bit, Percent)`**dosyasını dışa aktarın: indeks rasterleri, yeniden ölçeklendirme veya etiket gerektirmeden, float32**indeks değerleri olarak** buraya yazılır. Biçimin adına rağmen, indeks ürünleri ×100 değildir — bu ölçeklendirme yalnızca tamsayı veri türündeki yansıma değerleri için geçerlidir.

#### Karıştırma sınırlama maskesi — bir bandın hangi piksellerde sınırlandığı

3×3 spektral karıştırma işlemi, bir bandı negatif hale getirebilir — ışığın o bandı neredeyse hiç içermediği veya sahnenin kameranın karıştırma matrisinin tanımladığı alanın dışında kaldığı durumlarda. Bu gerçek bir değer değildir, bu nedenle dışa aktarım işlemi bu değeri 0&#x27;a sabitler — ve canlı görüntüde de aynı 0 değeri gösterilir. Bir bant, birkaç pikselde veya neredeyse tüm piksellerde sınırlandırılabilir; maske her iki durumu da kaydeder. `Chloros:Band{R,G,B}UnmixClampFraction`, bunun bir karenin ne kadarında meydana geldiğini belirtir; sınırlandırma maskesi ise **hangi piksellerde** olduğunu gösterir:

- **`clamp_masks/<product folder>/<product name>_clampmask.tif`**— ürün klasörünün**yanındaki** bir `clamp_masks/` klasöründe, asla içinde değil (`radiance/x_radiance.tif` → `clamp_masks/radiance/x_radiance_clampmask.tif`) — en az bir pikselin sınırlandırıldığı her parlaklık, yansıma ve indeks ürünü için. Tek bantlı `uint8`, ürünün kendi piksel ızgarası üzerinde (yönelim, ROI, binning ve dizi hizalama çarpıtması dahil). Her maske, ürününün kamera (`Make` / `Model` / seri numarası), çekim zamanı ve lens etiketlerini belirtir ve bitlerinin temsil ettiği bantları listeler (`XMP-Camera:BandName`, önce bit 0); `ImageDescription` ve `Software`, onu bir sabitleme maskesi olarak işaretler ve kalibrasyon içermez.
- **Bitler**: bit 0 = ürünün R kanalındaki bant (`Chloros:BandRName`), bit 1 = G (`BandGName`), bit 2 = B (`BandBName`). Bir bitin ayarlanmış olması, o banttaki pikselin değerinin 0&#x27;a sınırlandırıldığı anlamına gelir — bunu ve bundan oluşturulan herhangi bir indeksi, güvenilirliği düşük olarak değerlendirin.
- **`Chloros:UnmixClampMask`**, unmix işlemini çalıştıran her ürüne damgalanır: maskenin, ürünün kendi klasörüne göre göreceli yolu (`../clamp_masks/<product folder>/<product name>_clampmask.tif`, her zaman `/`- ile ayrılmıştır — maskeyi açmak için bunu görüntünün klasörüne ekleyin) veya hiçbir piksel sınırlandırılmadığında `none` şeklindedir. Etiket olmaması, ürünün unmix işlemini hiç çalıştırmadığı anlamına gelir (RGB veya tek renkli kameralar, ham/debayered seviyeler).
- Yakalama sonuçları bunu karenin **`clamp_mask`** olarak taşır (maskenin yolu veya hiçbir şey sınırlanmadığında `None`) — `CameraSession.capture()` sonuçlarında, `ArraySession.capture()`&#x27;`CaptureResult` karelerinde ve HTTP yakalama yanıtlarında yer alır. Çevrimdışı derleme, maskelerini `clamp_masks` olarak listeler.
- Canlı bir dizinin çok bantlı yığını (`/api/camera/cli-array-index` ile `save_multiband`), çıkış klasörünün kendi `clamp_masks/` dosyasında kaynak kamera başına bir maske alır — bkz. CLI referansı&#x27;`lattice index`&#x27;e bakınız. Bir videoda maske yoktur: bunun yerine, çevrimdışı derlemenin sonucu her karenin sınırlama oranını bildirir.

Hiçbir ürün klasörü, herhangi bir derinlikte maske içermediğinden, bir ürün klasörü alt klasörleri de dahil olmak üzere olduğu gibi fotogrametri yazılımına aktarılabilir ve yazılım yalnızca görüntüleri alır. Ürün klasörünü aktarın, üzerindeki klasörü değil. Chloros’un kendi içe aktarma işlevi maskeleri atlar.

```python
import os, numpy as np, tifffile

mask = tifffile.imread(
    "clamp_masks/radiance/capture_<ts>_SN<serial>-<filter>_radiance_clampmask.tif")
# or, from the image: os.path.join(os.path.dirname(image_path), tag), with
# tag = the image's Chloros:UnmixClampMask
r_clamped = (mask & 1) != 0      # the band Chloros:BandRName names
g_clamped = (mask & 2) != 0
b_clamped = (mask & 4) != 0
```

---

## LATTICE Kameralar için Smart-Connect

Canlı donanım için kalıcı arka uç oturumları. GUI&#x27;nin kullandığı uç noktalarla aynı olduğundan, davranış SDK, CLI ve GUI arasında aynıdır.

### Tek Kamera — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

Burada ayarlanan pozlama, kazanç ve hedef değerler sizin ayarlarınız olarak korunur: Chloros’un otomatik pozlama kontrolü (DAQ ile ölçülen pozlama dahil) bu ayarları değiştirmez. `cam.set_settings(exposure_auto="Continuous")` (veya `gain_auto=`) pozlamayı (kazancı) geri verir, ve `cam.set_settings(target_brightness_auto=True)`, hedefi Chloros&#x27;un otomatik hedef ayarına geri devreder.

#### `connect_camera()` İmza

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

- **Akış başlatıldığında geri döner.** Arka uç, sınırlı bir süre bekler (akışın başlaması için yaklaşık 15 saniye, ardından ilk tam tamponun gelmesi için 10 saniye — yaklaşık 1,5 saniye sonra gelen ilk önizleme karesi için değil), bu nedenle hemen ardından `status()` gelir ve ardından `streaming` gelir. Oturumun `connect_stream`&#x27;i, yanıtın `stream` bloğudur: `state` `streaming`, `no_frames` (akış başladı ancak hiçbir şey iletilmedi — `note` bunu belirtir ve jumbo paketler kullanan bir kamera için 1500 baytlık paket boyutuyla yeniden bağlanılması gerekir), `starting` veya `start_failed`, `waited_s`. Zaten bağlı olan bir kamera için `None`. Bir önizleme (`get_camera_preview`, `/api/camera/jpeg/<serial>`) isteği, yanıt vermek yerine kameranın ilk önizleme karesini bekler (4 saniye sınırlı) &quot;Henüz kare yok&quot; yanıtı vermek yerine, kameranın ilk önizleme karesi için sınırlı bir süre (4 saniye) bekler; akışından hiçbir şey iletmemiş bir kamera (`no_frames` veya donanım tetikleyicisi için etkinleştirilmiş) yine de anında yanıt verir.
- **Paket boyutunu siz belirleyebilirsiniz.** `settings={"gev_packet_size": 1500, "gev_packet_size_auto": False}`, kamerayı 1500 baytlık paketlerle açar; sondalama yapılmaz (`chloros-cli lattice cam-connect --packet-size standard` de aynısını gönderir).
- **Aynı anda tek bir bağlantı kamera havuzunu meşgul eder**: açma işlemi, paket boyutu testi, ilk kalibrasyon indirme işlemi (bu bilgisayarda yeni bir kamera için yaklaşık 70 saniye sürer), akışın başlatılması. Bu sırada bir kameraya ihtiyaç duyan bir istek (`read_nodes`, `set_settings`, bir yakalama, ikinci bir bağlantı) en fazla 5 saniye bekler, ardından HTTP 503 hatasıyla başarısız olur (`CAMERA_POOL_BUSY`), bağlanan kameranın adını belirtir (`ChlorosConnectError`); bir süre sonra tekrar deneyin. Listeler asla beklemez: `list_cameras()` (`GET /api/camera/pool`), `list_arrays()` ve `CameraSession.status()` anında yanıt verir. Bağlantı kurulmakta olan bir kamera, havuz yanıtının altında `connecting` (`serial`, `phase` — `opening the camera`, `rebalancing the pool bandwidth`, `loading its calibration`, `starting its stream`, `waiting for its stream` — ve `elapsed_s`)ve `status()`, `connecting: True`&#x27;e, bu da `phase`&#x27;e karşılık gelir; diğer kameralar, havuz boş olduğunda canlı olarak okunur, aksi takdirde son okuma değerlerinden yanıt verilir (`live: False`).

#### `CameraSession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `status(timeout=15.0)` | Kameranın canlı durumu, `chloros-cli lattice status` tarafından okunduğu şekliyle: `streaming`, `stream_state` (`starting`, `no_frames`, `streaming`, `start_failed` veya `stopped`), `device` (model, seri numarası, ürün yazılımı, sıcaklık, `is_mono`) ve mevcut ayarlarını döndürür. |
| `read_nodes(names, enum_names=(), timeout=30.0)` | GenICam düğümlerini okur; otomatik pozlama çalışırken `{nodes, errors, enums, device, was_in_pool, in_array_id}`, ayrıca otomatik pozlama çalışırken `frame_values` ve `notes` değerlerini döndürür (aşağıya bakın). |
| `set_settings(**kwargs)` | Düğümleri kolay adlarıyla yazar (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `target_brightness_auto`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). Bir `exposure_time` (`gain`) manuel pozlamadır (kazanç): `exposure_auto` (`gain_auto`) yazılmadan girilir; bu durumda otomatik mod önce kapatılır, böylece değer çalışan bir otomatik mod tarafından reddedilmez. Sonuç olarak ortaya çıkan `applied`, kameranın çektiği değeri içerir; kameranın reddettiği bir yazma işlemi `errors`&#x27;te bulunur ve saklanmaz. Hata, kameranın şu anda kabul ettiği aralığı belirtir (`ExposureTime` maksimum değeri kameranın kare hızına bağlıdır), ve yalnızca reddedilen değer için kapatılmış olan otomatik mod yeniden etkinleştirilir; `restored` bunu listeler (örn. `{"ExposureAuto": "Continuous"}`). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, daq=None, daq_tolerance_ms=None, settings=None, timeout=None)` | **Tek** kare yakalar. `CaptureResult` değerini döndürür: kare meta veri sözlüklerinin bir listesi (kaydedilen her seviye için bir tane), `.skipped`, `.failed`, `.daq_files` ve `.daq_matches` içerir. (Seri/çoklu kare yakalama kaldırılmıştır — bir seriye ihtiyacınız varsa, bir döngü içinde `capture()`&#x27;i çağırın.) |
| `capture_repeated(output_dir="output", *, count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Bu kameradan arka arkaya (`interval_s=0`) veya aralıklı çekimler, `count` yakalamaları ve/veya `duration_s` (en az bir tane, böylece sonlanır) için, tıpkı `ArraySession.capture_repeated()`&#x27;in bir dizi için yaptığı gibi. `CaptureResult`&#x27;lerin listesini döndürür; sürücünün dolu olduğunu bildiren bir yakalama sonrasında durur. `on_capture(i, result)` her birinden sonra çalışır; diğer anahtar kelimeler `capture()`&#x27;e yönlendirilir. |
| `disconnect(keep_settings=False, clear_settings=False)` | Havuzdan serbest bırakma; kamera, bağlandığında bulunduğu duruma geri getirilir (tetikleme kablolaması, PTP, ayarlar); `keep_settings=True` ise ayarlarını kendi belleğine kaydeder, `clear_settings=True` bu belleği sıfırlar (bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings)); her ikisi de istendiğinde `camera_memory` değerini döndürür. Bir dizi elemanı olan kamera, dizinin tamamını serbest bırakır. Zaten açık bir oturuma bağlandıysak hiçbir işlem yapılmaz. Arka uç serbest bırakmayı onayladığında veya serbest bırakılacak bir şey kalmadığında (kamera zaten kaldırılmışsa veya çalışan bir arka uç yoksa); zaman aşımına uğrayan veya arka uç tarafından reddedilen bir serbest bırakma işlemi, `ChlorosConnectError` hatasını verir; çünkü kamera hâlâ tutuluyor olabilir ve bir sonraki bağlantı bununla çakışabilir. `with` bloğunun çıkışı hiçbir zaman hata vermez. |

Özellikler: `serial`, `device` (kameranın keşif kaydı) ve `backend_url`.

`capture()` dışa aktarma denetimleri (diziyle aynı model + GUI):

- `processing` / `levels` — `processing="all"`, geçerli tüm dışa aktarma türlerini kaydeder; `levels=["raw","radiance"]` ise yalnızca bunları kaydeder (`processing`&#x27;i geçersiz kılar). Arka uç varsayılanı için her ikisini de atlayın.
- `daq` — kullanılan yakalama verisini okuyan DAQ, varsayılan olarak `.daq` yan dosyası olarak kaydedilir; bu dosyaya yalnızca ham veriler içeren bir yakalama eklenir, böylece kare daha sonra yansıma/indeks olarak yeniden işlenebilir. `daq=False`, hiçbir seviye bunu kullanmadığında bunu atlar. `force_daq=True`, varsayılan ayarın eski yazılış şeklidir. DAQ yoksa, `.daq` yoktur.
- `daq_tolerance_ms` — okumanın pozlamadan ne kadar uzak olabileceği ve yine de tolerans sınırları içinde kalabileceği (varsayılan 500). Yalnızca sonuç değişir.

#### Otomatik pozlamada `read_nodes()` — `frame_values` ve `notes`

`ExposureAuto` (`GainAuto`) çalışırken, `ExposureTime` (`Gain`) düğümü, kameranın o anda yaptığı işlemi değil, **en son yazılan değeri** geri okur. `nodes`, bu düğüm değerini hâlâ tutar. Otomatik modu `Off` olmayan bağlı bir kamerada `ExposureTime` veya `Gain` sorgulandığında, yanıtta şunlar eklenir:

- `frame_values` — düğümün adı altında, kameranın en yeni karesinin taşıdığı değer (`ExposureTime` µs cinsinden, `Gain` dB cinsinden);
- `notes` — bu tür her düğüm için her iki değeri de belirten bir cümle.

```python
r = cam.read_nodes(["ExposureTime", "Gain"])
print(r["nodes"]["ExposureTime"])                  # last value written, e.g. 2000.448
print(r.get("frame_values", {}).get("ExposureTime"))  # what the newest frame used, e.g. 19600.0
for name, note in r.get("notes", {}).items():
    print(note)
# ExposureAuto is Continuous: ExposureTime holds the last value written (2000.448);
# the camera's newest frame was at 19600 us.
```

Otomatik mod `Off` olduğunda her iki anahtar da yoktur (bu durumda düğüm, kameranıngerçek değeri olur) ve arka uca bağlı olmayan bir kamera söz konusu olduğunda her iki anahtar da mevcut değildir. Bir dizi için, `ArraySession.status()["member_ae"]` her bir elemanın kare değerlerini tek seferde verir.

#### DAQ ile ölçülen pozlama üst sınırı (HTTP)

Bir DAQ ışık sensörü bağlıyken, Chloros, beyaz referans panelin kırpılmaması için kameranın pozlamasını × kazancını ölçülen gün ışığına göre sınırlar. Bu sınır, yalnızca bir kalibrasyon hedefi o kameranın görüş alanında olduğunda (son 10 saniye içinde karesinde görüldüğünde) geçerlidir; görüş alanında hedef olmadığında, kameranın kendi parlaklık korumasına sahip normal otomatik pozlaması devreye girer, böylece sahnenin geri kalanı gerekenden daha karanlık kalmaz. SDK sarmalayıcısı yoktur; bu özelliği kameraya göre `GET` / `POST /api/camera/<serial>/daq-exposure-cap` ile okuyun veya değiştirin (POST gövdesi `{"enabled": false}`, o kamera için bu özelliği devre dışı bırakır). Yanıtta `enabled`, `cap_us` (canlı üst sınır, `None` ise devre dışı durumdayken, kalibrasyon hedefi görüş alanında olmadığında da dahil), `k_exp_g` (tavanın hesaplanmasında kullanılan sabit) ve:

| Alan | Anlam |
| --- | --- |
| `k_exp_g_source` | Hangi sabitin kullanıldığı: `camera` — bu kameranın kendi sabiti, kalibrasyon paketindeki laboratuvar noktasından hesaplanır (RGB kameralar); `fleet` — pakette laboratuvar noktası bulunmadığında kullanılan filo sabiti; `override` — arka uçta ayarlanan bir alan geçersiz kılma. |
| `k_exp_g_fleet` | Karşılaştırma amacıyla kullanılan filo sabiti. |

### Senkronize Dizi — `ArraySession` (Smart-Prep)

`connect_array`, çoklu kamera kurulumları için **önerilen başlangıç noktasıdır**. Arka planda tam GUI Smart-Prep akışını yürütür:

1. **Ağ analizi**(`/api/camera/array/recommend`) —**tam sensör** (binning yok, ROI kırpma yok) ayarlar ve kare hızının, Connect&#x27;in kullanacağı piksel formatları ve paketleme ile öngörülen, bu ana bilgisayar ve ağın sağlayabileceği düzeye düşmesine izin verir. Kare hızını yüksek tutmak için kareler asla küçültülmez: daha küçük bir `width` / `height` veya `binning=2` / `4` değerleri, çözünürlüğü kare hızı ile takas etmek için kullanılır (`alternatives`, bu seçenekleri öngörülen fps değerleriyle birlikte listeler).
2. **Katmanlı otomatik seçim** — `sim-capture-sim-emit` (her kamera aynı tetikleyicide pozlanır, kabloyu paylaşmak için hız ayarlanır, paylaşım azaldıkça kare hızı düşer) kameraların kabloyu paylaşacak şekilde hızlandırılabildiği herhangi bir ağda; `slip-emit-and-capture` (kameralar kabloda sırayla çalışır, aynı anda pozlanmaz), yalnızca bu mümkün olmadığında.
3. Varsayılan olarak **PTP etkin**— kameralar arası zaman damgaları,**~1 ms**hassasiyetle tek bir paylaşılan saat üzerinde eşlenir; arka uçtaki [PTP grandmaster](#ptp-grandmaster--time_sync_status)ile senkronize olur. Eşzamanlı pozlama, PTP&#x27;den değil, M8 donanım tetikleyicisinden (modüller arası**&lt; 100 µs**) sağlanır: PTP, pozlamaları değil *zaman damgalarını* hizalar.
4. **Kamera başına piksel formatı otomatik seçimi** — tek renkli → `Mono12`, renkli → `BayerRG12`.
5. **AE başlangıç değeri** — her kameranın mevcut AE durumunun anlık görüntüsünü alır, böylece bağlantı sırasında pozlama sıfırlanmaz.
6. **GPIO tetikleyici yapılandırması** — `connect_array` her kamerayı (`TriggerMode=On`, `TriggerSource=Line2`) etkinleştirir, böylece ana ünitenindarbesinin M8 kablosu üzerinden bağımlı kameraları çalıştırmasını sağlar. Bu, yalnızca dizi modunda geçerli bir adımdır: `LatticeCamera` ile açılan tek bir kamera bunun yerine serbest çalışma modunda çalışır. Diziyi serbest bırakmak (`disconnect()` / `force_disconnect()`, `chloros-cli lattice array-disconnect`) komutuyla her kamera serbest çalışma moduna (`TriggerMode=Off`) geri döner; diğer tüm ayarlar, bağlantı kurulduğunda olduğu gibi kalır: tetikleme ve senkronizasyon hattı ayarları (`TriggerSource`, `TriggerActivation`, `TriggerOverlap`, `TriggerDelay`, `LineMode` / `LineSource`; ana kameranın senkronizasyon hattı tekrar bir girişe ayarlandı), PTP ve oturum sırasında değiştirilen kamera ayarları — `keep_settings=True` / `clear_settings=True` (bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings)).

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

Özellikler: `array_id`, `master_serial`, `slave_serials`, `ptp_enabled`, `sync_mode`, `packed_transport` ve `packed_reason` (son dördü, `attach_array`&#x27;ten gelen bir tutamağın üzerindeki `None`&#x27;tir), `connect_stream` ve `backend_url`. `connect_stream`, bağlantı yanıtının `stream` bloğudur; tekli bağlantının şekli artı `members` (`{serial: {state, why?}}`): Dizinin ilk senkronize grubu geldiğinde (arka uç, sınırlı olarak yaklaşık 10 saniye boyunca bunu bekler), ya da her üyenin son getirme hatasını `why` olarak içeren `no_frames` ve jumbo paketler için `note`&#x27;teki 1500 baytlık yedek değer. `None`, bir `attach_array` tanıtıcısında.

#### `connect_array()` İmzası

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # pin the master trigger rate (auto if None); see set_target_fps
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    packed="auto",                        # packed 12-bit transport: "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=600.0,                        # s; also the backend's deadline (see below)
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`timeout`, çağrının bağlantı için bekleme süresidir ve bu süre,
son tarih olarak arka uca gönderilir. Bu süre içinde bağlantı kurulmazsa işlem iptal edilir: açılmış olan tüm kameralar
bulundukları duruma geri döndürülür ve kapatılır, hiçbir dizi bağlanmaz ve
çağrı, `ChlorosConnectError` hatasını verir (HTTP 504 `CLIENT_DEADLINE_PASSED`; bu,
bağlantının ulaştığı aşamayı belirtir). Dolayısıyla, bu hatayı veren bir çağrı, arkasında hiçbir dizi
bağlı bırakmaz. 9 kameralı bir dizi yaklaşık bir dakika içinde bağlanır; kameraların
açılmasından hemen sonra bu işlem birkaç dakika sürebilir. Çalışır durumda olan ancak henüz
senkronize edilmiş kare grubu bulunmayan bir dizi, `connect_stream["state"]`
`no_frames` koduyla döndürülür. Arka ucun günlüğünde her bağlantı adımı için bir zamanlama satırı
(`[array-connect <id>] <step>: <s> s …`) ve en yavaş olanı belirten bir son satır bulunur.

`force_tier` değerleri:
- `"sim-capture-sim-emit"` — gerçek eşzamanlılık (tüm kameralar aynı saat kenarında ateşlenir).
- `"sim-capture-ftd-stagger"` — esnek zaman alanı kademelendirme (kameralar hafifçe kaydırılmış zamanlarda sinyal gönderir, böylece paketler kabloda sıralı hale gelir).
- `"slip-emit-and-capture"` — kamera başına sıralı yakalama (zamansal senkronizasyon yok). Yalnızca kameraların kabloyu eşzamanlı olarak paylaşacak kadar yavaş hızlandırılamadığı durumlarda otomatik olarak seçilir.

`wire_ceiling_mbps`, **ana bilgisayarın sürekli kablo bütçesini** MB/s cinsinden geçersiz kılar — tüm
dizi tahsisi bu tek sayıya bağlıdır. Otomatik olarak algılanan değeri kullanmak için `None` olarak bırakın.
Dizi, GVSP bozuk çerçeveler bildirdiğinde bu değeri düşürün: otomatik değer,
NIC&#x27;nin bildirdiği bağlantı hızından türetilir; bu değer, USB adaptörlerini, dar PCIe şeritlerini ve
yoğun paylaşımlı yapıları olduğundan fazla gösterir — ve bu fazla tahmin,
gözle görülür şekilde yavaş bir bağlantı yerine bozuk çerçeveler olarak ortaya çıkar. Değer, projenin dizi yakalama bloğunda kalıcı olarak saklanır; bu nedenle,
dosyanın yeniden açılması veya daha sonra `connect_array` komutunun kullanılması, diğer dizi ayarları gibi bu değeri de geri yükler.
Bkz. [Dizi Sağlığı](#array-health--which-subsystem-is-losing-frames).

#### Sıkıştırılmış 12-bit Aktarım (`packed=`)

Tam sensör kapasitesinde, bir dizinin kare hızı, her bir kameranın
karelerini ne kadar hızlı gönderebileceğine göre belirlenir. 12-bit formatlar (`BayerRG12`, `Mono12`) normalde 16-bit
kelimeler halinde iletilir; sıkıştırılmış aktarım ise aynı 12 biti piksel başına 1,5 bayt olarak gönderir ve
bunları ana bilgisayarda açar, böylece her karenin gönderilmesi %25 daha az zaman alır — yaklaşık
**tam sensör kapasitesinde saniyede %33 daha fazla kare** (ölçülen değer: 9 kameralı bir
dizide 10,7 → 14,0 fps). **Kaydedilen veriler tamamen aynıdır**: aynı değerler, aynı 16-bit
dosyalar, aynı meta veriler.

- `packed="auto"` (varsayılan), yalnızca faydalı olduğu **ve** ana bilgisayarın
  paketi açmaya gücü yettiği durumlarda paketler: bağlantı kurulduğunda arka uç kendi paket açma hızını,
  CPU sayısını, mevcut yükü ve ağ bütçesini ölçer. Yedek çekirdekleri olan bir masaüstü bilgisayar
  tam sensör hızında paketler; yoğun veya düşük güç tüketen bir ana bilgisayar (tek kartlı bilgisayarlar,
  gömülü denetleyiciler) paketsiz olarak veri akışı sağlar. Küçük ROI&#x27;ler ve gruplandırılmış kareler çok az kazanç sağlar.
- `packed="off"` (veya `False`) hiçbir zaman paketlemez — tanılama sırasında bunu elemek için
  veya CPU&#x27;sunu başka işler için ayırması gereken bir ana bilgisayarda.
- `packed="on"` (veya `True`) her zaman 12 bitlik formatları paketler.
- Diğer tüm değerler `ValueError`&#x27;i tetikler. 8-bit ve 16-bit formatlar asla
  paketlenmez.

`analyze_array_network(..., packed="auto")` de aynı kararı yansıtır;
bu nedenle fps değeri, dizinin çalışacağı hızdır (yanıtta
`packed_transport` ve `packed_reason` değerleri bulunur); `pixel_format` değeri, 8 bit yerine
connect&#x27;in uyguladığı değere (Mono12 / BayerRG12) ayarlanır.

Karar ve gerekçesi oturumda yer alır:

```python
arr = chloros_sdk.connect_array(serials)          # packed="auto"
print(arr.packed_transport, arr.packed_reason)
# True  auto: projected 11.0 -> 14.4 fps, unpack 0.16 of 1.50 budgeted cores

arr = chloros_sdk.connect_array(serials, packed="off")
```

`ChlorosProject.connect_all(packed=...)`, bir projedeki her
diziye aynı geçersiz kılmayı uygular (varsayılan `None`, yani `"auto"`: bir proje, dizi başına
sıkıştırılmış ayar kaydetmez).
Makine genelinde bir varsayılan ayar için, arka ucun ortamında `CHLOROS_ARRAY_PACKED12=off` (veya `on`) değerini ayarlayın; ancak açıkça belirtilen bir `packed="on"` / `"off"` ayarı yine de önceliklidir.
`/api/camera/array/status`, `packed_transport` ve `packed_decision` değerlerini bildirir.

#### Beklenen Kare Hızları

Ölçülen dizi kare hızları (donanım senkronlu, 12 bit; tam sensör = 2048 × 1536). Bunları, kendi ana bilgisayarınız ve ağınız için kare hızını tahmin etmek amacıyla `status()["actual_fps"]`; `analyze_array_network()` projelerini doğrulamak için kullanın.

| Kurulum | Dizi kare hızı |
| --- | --- |
| Tam sensör, PCIe 10 GbE ana bilgisayar ağ kartı, paketlenmiş aktarım (masaüstü bilgisayarda `packed="auto"`) | ~14 fps |
| Tam sensör, PCIe 10 GbE ana bilgisayar ağ kartı, sıkıştırılmamış (`packed="off"`) | ~10,5 fps |
| Tam sensör, USB 10 GbE adaptöründe 8 kamera | ~3,8 fps |
| Tam sensör, 1 GbE ana bilgisayar bağlantısı | 1,2–1,7 fps |
| 2 × 2 birleştirme (`binning=2`), 10 GbE ana bilgisayar ağ kartı | ~30–33 fps |

- PCIe 10 GbE bağlantısında, tam sensör modunda sınırı belirleyen **kameralar**dır, ağ değildir: 4 ve 8 kamera aynı hızda çalışır.
- **`target_fps` tetikleme hızıdır ve dizi asla bu hızdan daha hızlı çalışmaz.** `target_fps=1`, diziyi 1 fps&#x27;de çalıştırır; `None` (varsayılan ayar) ise ana bilgisayar ve ağın desteklediği en yüksek hızda çalıştırır. Bu hızın üzerindeki bir değer, bu hıza düşürülür (bkz. [`set_target_fps`](#interval-capture-for-drone-surveys)).
- **`link_speed_mbps` ile `4294` arasındaki fark, bir bağlantı hızı değildir.** Bu, Windows&#x27;un adaptörün hızını okuyamadığı anlamına gelir (sürücü, en büyük 32 bitlik değeri bildirmiştir); bu durum, USB Ethernet adaptörlerinde sıkça görülür. Bir USB adaptörünün Windows&#x27;ta kullanılabilir hızı, her halükarda 200 MB/s ile sınırlıdır (bkz. [Dizi Sağlığı](#array-health--which-subsystem-is-losing-frames) bakınız); ayarlamak için [Host NIC Kurulumu ve Ayarlama](cli-reference.md#host-nic-setup--tuning-lattice-arrays) altındaki CLI referansının &quot;USB 10GbE adaptörleri&quot; bölümüne bakınız.
- **Linux&#x27;ta da bir USB sınırı geçerlidir: x86_64&#x27;te 400 MB/s, NVIDIA Jetson&#x27;da 200 MB/s.** Adaptör sysfs üzerinden tanımlanır (`ethtool -i`&#x27;e geri dönülür), bu nedenle bir USB 5 GbE veya 10 GbE adaptör, 500 / ~1060 MB/s&#x27;lik nominal hızı yerine bu sınır değerine göre değerlendirilir. x86_64 değeri Linux üzerinde ölçülmüştür: Windows&#x27;ta 200 MB/s&#x27;nin üzerindeki hızlarda kareleri bozan aynı adaptör, 480 MB/s&#x27;ye kadar her sınır değerinde 4 kameralı bir diziyi hatasız çalıştırmıştır. NVIDIA Jetson&#x27;da aynı adaptör yalnızca 200 MB/s hızında hatasız çalıştı ve bu hızın üzerinde aralıklı olarak çerçeve kaybı yaşadı. Eğer ana bilgisayarınız `gvsp_corrupt` çerçeveleri olmadan daha fazla veri işliyorsa, `wire_ceiling_mbps` ile bu değeri artırın. Linux&#x27;ta USB 2.0 bağlantı noktasına takılan bir adaptörün hızı 30 MB/s ile sınırlıdır; adaptörü bir USB 3 bağlantı noktasına takın.

#### Aşırı Abonelik (kamera başına alt sınır)

Bir dizi herhangi bir ağda çalışır; önemli olan kare hızıdır. Sim-emit hız ayarı, her kameraya çarpışma güvenli kablo bütçesinden eşit bir pay tahsis eder ve hepsi yine de aynı tetiklemede görüntü verir; bu nedenle yavaş bir kabloda pay — ve kare hızı — düşüktür: 1 GbE üzerinde jumbo çerçeveler olmadan 8 tamçözünürlüklü 12 bit kamera, jumbo çerçeveler olmadan 1 GbE üzerinde her biri 6,5 MB/s alır; bu da yaklaşık **1 fps**&#x27;ye (sıkıştırılmış halde yaklaşık 1,3 fps) karşılık gelir. Pay, kameraların ayarlanabileceği en düşük hızda sınırlandırılır (`per_cam_floor_bps`: bağlantı kurulduğunda her kameranın GevSCPD aralığından okunur ve asla 1 MB/s&#x27;nin altına düşmez). Eğer kablo bağlantısı bundan daha yavaş bir hız gerektiriyorsa, bağlantı**seri**verime geri döner (`slip-emit-and-capture`): kameralar tam bağlantı hızında sırayla çalışır, böylece her çerçeve ulaşır ancak üyeler**aynı anda maruz kalmaz**— yanıtın kademesi ve engelleyiciler (`SERIAL: ...`) bunu belirtir; geri dönüşü reddetmek için `force_tier="sim-capture-sim-emit"` değerini geçirin. Kare boyutu paylaşım hesaplamasına dahil edilmez, bu nedenle**binning ve ROI kare hızını artırır ancak bir dizinin sığıp sığmayacağını değiştirmez**. Yalnızca diziyi hiçbir hızda, ister eşzamanlı ister sırayla taşıyamayan bir bağlantı**aşırı yükleme** yapar — arıza modu, daha düşük kare hızı değil, GVSP paket kaybıdır. Çözümler: daha az kamera, uçtan uca jumbo kareler veya daha hızlı bir ağ kartı.

- `analyze_array_network()` ve `/api/camera/array/connect` yanıtları, `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` ve `per_cam_floor_bps` değerlerini içerir. `oversubscribed` değeri true olduğunda, projeksiyon **fps alanlarını sıfırlar** (`achievable_fps_max` / `fps_bright` / `fps_dark`) yanıltıcı bir &quot;yavaş ama çalışıyor&quot; hızı bildirmek yerine sıfırlar.
- `POST /api/camera/array/connect`, bir `pin_resolution` gövde parametresini kabul eder (**yalnızca HTTP — bir SDK kwarg değildir**; `connect_array` bunu açığa çıkarmaz). Bu, katı moddur: `pin_resolution` ayarıyla aşırı abone alınmış bir bağlantı, her bir çözüm yolunu belirten bir hata mesajıyla**kesinlikle reddedilir**. Bu ayar olmadan, bağlantı, dizinin paket kaybedeceği uyarısıyla devam eder. Her iki durumda da bağlantı, istenen çerçeve boyutunu korur — asla kendi kendine birleştirmez veya kırpmaz.
- Yalnızca test amaçlı: reddetmeyi yüksek sesli bir uyarıya düşürmek için arka uç ortamında `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`&#x27;i ayarlayın — yine de bağlanırsınız ve paket kaybını kabul edersiniz.

#### Dizi Sağlığı — hangi alt sistem çerçeve kaybediyor

`ArraySession.capability()` (`GET /api/camera/array/<array_id>/capability`), bağlı bir dizide canlı bir `health` bloğunu taşır;
bu, **10 saniyelik** aralıkta yeniden değerlendirilir. Çerçeve kaybını,
her ikisi için de zıt çözümler gerektiren iki nedene ayırır;
hiçbirini belirtmeyen tek bir “eksik” oran yerine:

| Alan | Anlamı | Hangi alt sistem |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (seri başına) | Çerçeve **ulaştı ancak yapısal olarak hatalıydı**— GVSP paket kaybı. |**Ağ**: kablo bütçesi, hız ayarı, NIC RX halkası, MTU |
| `never_arrived_rate_pct` (seri numarası başına) | Çerçeve **hiç gelmedi**— kamera tetiklenmedi veya kameradan hiçbir şey çıkmadı. |**Tetik / senkronizasyon**: M8 kablosu, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Her bir kamera için en kötü aktarım hızı her biri için. | — |
| `per_cam_rate_pct` | Kamera başına birleşik eksiklik oranı (her iki neden birlikte). | — |
| `stable_for_seconds` | Her kameranın %0,01&#x27;in altında kaldığı süre. | — |

`health` ile birlikte, aynı kayıt tüm tahsisatın askıda kaldığı süreyi de bildirir:

| Alan | Anlamı |
| --- | --- |
| `wire_ceiling_mbps` | Ana bilgisayarın geçerli olan sürekli kablo bütçesi, MB/s. |
| `wire_ceiling_source` | Bu sayının kaynağını kelimelerle ifade eder — ör. `USB-capped 200 MB/s (was theoretical 1062; …)` veya `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, `wire_ceiling_mbps=` tarafından ayarlandığında. |
| `nic_is_usb` | Bir USB Ethernet adaptörü için `true`. |

```python
import chloros_sdk

serials = ["213800234", "214000533"]           # master first
arr = chloros_sdk.attach_array(serials)
h = arr.capability()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    # force_disconnect(), not disconnect(): this handle came from attach_array,
    # and disconnect() never tears down an array the session only attached to.
    # It returns only once the backend confirms the release; a teardown that
    # timed out or was refused raises ChlorosConnectError instead, so the
    # reconnect below never races a half-released array.
    arr.force_disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Yoklama:** sıfırdan farklı bir `gvsp_corrupt_rate_pct` değeri ve 0 olan `never_arrived_rate_pct` değeri,
tetikleme ve kablo senkronizasyonunun mükemmel olduğunu ve kaybın %100&#x27;ünün ağ yolunda olduğunu gösterir —
`wire_ceiling_mbps` değerini ayarlayın ve yeniden bağlanın. Tersine bir durum ise senkronizasyon kablosunu veya
tetikleme hattını işaret eder.

> **`target_fps`, bozuk çerçeveler için bir ayar kolu değildir.** GevSCPD hız ayarı,
> bağlantı kurulduğunda bir kez yazılır; bu nedenle tetikleme hızını düşürmek, görev döngüsünü değiştirir,
> eşzamanlı yayın patlama hızını değiştirmez. Ölçülen 5 kat talep kesintisi herhangi bir iyileşme sağlamazken,
> kablo tavanını 240&#x27;tan 200 MB/s&#x27;ye düşürmek, aynı donanımın bozukluk oranını %10,4&#x27;ten
> %0,00&#x27;a indirdi.

> **Çalışan bir dizi, LATTICE (M3C / M3M) ürün yazılımında kendi kendini yeniden planlayamaz.** Bu sorunu
> kendi başına düzeltemez; bağlantıyı kesip yeniden bağlayın, böylece bağlantı zamanı seçici yeni
> tavan değerine göre yeniden planlama yapsın.

Bir **USB Ethernet adaptörü, isim etiketinden bağımsız olarak** prob tarafından 200 MB/s ile sınırlandırılır
: bağlantı hızını sürekli bir değere dönüştüren verimlilik tablosu
PCIe&#x27;den türetilmiştir ve bir USB NIC, Ethernet bağlantı hızını bildirirken
USB veriyolu ve sürücüsü tarafından sınırlandırılır. Bu sınır mutlak bir değerdir, kesirli bir değer değildir — bir USB 1 GbE adaptörü
~80 MB/s hız sağlar ve bu sınırdan etkilenmez. Bu sınır, Windows ve Linux’ta (Jetson dahil) geçerlidir;
ölçümlerin yapıldığı yerler için [Beklenen Kare Hızları](#expected-frame-rates) bölümüne bakın.

#### `ArraySession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `status(timeout=10.0)` | Çalışan dizinin canlı durumu: `target_fps` / `target_fps_auto` (tetikleme hızı ve hız denetleyicisinin buna sahip olup olmadığı), `target_fps_clamped_reason` (sabitlenmiş bir hızın neden daha düşük tutulduğu, aksi takdirde `None`), `actual_fps` (ölçülen; yakalama döngüsü pes ettiğinde veya durduğunda 0 olur), `recommended_fps`, `streaming` (yakalama döngüsü pes ettiğinde False olur — sadece `last_error`&#x27;i değil, bunu da kontrol edin; ayarların yeniden başlatılması sırasında True olarak kalır; bunu `restarting` işaretler), `stalled` (yakalama döngüsü çalışırken ancak yaklaşık on kare periyodu boyunca, 2–30 saniye, hiçbir kare grubu yayınlamamışsa True olur; `last_error` bunu ancak 60 saniye sonra bildirir), `last_frame_age_s`, `last_error`, `serials`, `ptp_enabled` (`True` yalnızca dizi PTP ile bağlandığında **ve** her üyenin en son `PtpEnable` değerini okuduğu durumda; `ptp_epoch`&#x27;in ölçtüğü &quot;kilitli&quot; durumuyla aynı değildir), `ptp_configured` (bağlantının talep ettiği değer), `ptp_off_members` / `ptp_unread_members` (en son `PtpEnable` kapalı olarak okunan / yanıt vermeyen üyeler), `member_recovery` (`None` veya güç kaynağını ya da bağlantısını kaybetmiş bir üye: `state`, yanıt vermediği halde `waiting`, `reopening`, ardından `recovered`, `members` ve `why` ile birlikte — dizi, ünite tekrar yanıt verdiğinde onu yeniden açar ve ona bağlantı zamanı PTP&#x27;sini, tetikleyici ve senkronizasyon hattı kablolaması, hız ayarı ve kamera ayarlarını geri yükler, ardından yeni bir `connect_array` olmadan tekrar akışa geçer), ve `ptp_epoch` (ölçülen), `sync_mode` / `sync_tier`, `packed_transport` / `packed_decision`, `per_cam_emit_mbps`, `link_speed_mbps` (`4294`, Windows&#x27;un adaptörün hızını okuyamadığı anlamına gelir — bkz. [Beklenen Kare Hızları](#expected-frame-rates)), `trigger_pipeline` / `pipeline_tune`, `radiometric_chain` (bant başına parlaklık zincirinin kullanılabilir olup olmadığı ve hangi elemanın zinciri bozduğu) ve `band_clamp` — canlı görüntü diziyi işlerken: unmix işleminin her bir eleman için 0&#x27;a sınırladığı aydınlatılmış piksellerin oranı (`members`: kameranın R, G, B bant sırasına göre `{serial: [band0, band1, band2]}`) ve kompozitin gösterdiği her bant için (`composite`: `{band name: share}`), ayrıca `warn`, GUI&#x27;nin &quot;Düşük güvenilirlikli bant verisi&quot; olarak işaretlediği pay (saniyede yaklaşık bir kez ölçülür; canlı görüntüleme çalışmadığında `None`) ve `member_ae` — her bir üye için, son yayınlanan karede (`exposure_us`) bulunan pozlama ve kazanç (`gain_db`) değerlerini, en son yayınlanan kareden alır; bu değerler karenin kendisinden elde edilir (`read_nodes` veya `ExposureTime` / `Gain`, otomatik pozlama çalışırken yazılan son değeri döndürür, o anda ne yaptığını değil). |
| `set_member_settings(serial, **kwargs)` | Bir üye kameranın ayarlarını yazın; `CameraSession.set_settings()` bunları (pozlama, kazanç, hedef parlaklık, ...); üye dizide kalır. Üye olmayan bir seri için `ChlorosConnectError` istisnasını tetikler. Bkz. [Dizi Üyelerinde Pozlama](#exposure-on-array-members). |
| `capability(timeout=10.0)` | Smart-prep&#x27;in belirlediği değerler (yakalama katmanı, kare boyutu, `pixel_format`), canlı `health` bloğu ve ana bilgisayarın kablo bütçesi. Bkz. [Dizi Sağlığı](#array-health--which-subsystem-is-losing-frames). |
| `set_target_fps(fps, timeout=10.0)` | Dizi çalışırken, yeniden bağlanmadan tetikleme hızını değiştirir. Bir sayı ile sabitlenir (hız denetleyicisi bu ayarı değiştirmez); `None`, ayarı otomatik moduna geri döndürür. `{requested, target_fps, target_fps_auto, clamped}` — `clamped`, hızın ana bilgisayar ve ağın taşıyabileceği düzeye düşürüldüğünde True değerini döndürür. Bkz. [Drone Etütleri için Aralıklı Yakalama](#interval-capture-for-drone-surveys). |
| `grab(processing="debayered", with_metadata=False, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, bit_depth=None, band=None, align=False, refl_max=None)` | Her kameradan bir kare, **hepsi tek bir tetiklemeden**, numpy dizileri olarak (dosya yazılmaz). Bir `GrabResult` (bir `dict`): `{serial: array}` veya `{serial: (array, metadata)}` ile `with_metadata=True`. `.group_id`, karelerin paylaştığı tetikleyicidir; `.errors`, dışarıda bırakılan her kamerayı nedene eşler (o tetikleyicide hizmet verilemeyen bir kamera atlanır, asla başka bir kameradan doldurulmaz). `processing`, `raw` / `debayered` / `radiance` / `reflectance` / `display`&#x27;tir. Bu, proje API&#x27;sindeki [`ArrayHandle.grab()`](#one-trigger-per-read--group_id) — tek bir ortak uygulama — ile aynıdır; dolayısıyla buradaki tüm anahtar kelimeler (`band`, `align`, `bit_depth`, `refl_max`) burada çalışır. `numpy` gereklidir. |
| `frame_stream(*, processing="debayered", fps=5.0, duration=None, count=None, apply_calibration=True, apply_white_balance=True, apply_index=False, index_expression=None, with_metadata=False, bit_depth=None, band=None, align=False, refl_max=None)` | Her tik başına bir `grab()` sonucu veren yineleyici (yalnızca anahtar kelime argümanları), `fps` hızında, `duration` (saniye) ve/veya `count` ile sınırlıdır (`None` = sınırsız). **Verilen her küme yeni bir tetikleyicidir:** en yeni tetikleyicisi zaten ürettiği küme olan bir tick, bunu tekrarlamak yerine bir sonraki tetikleyiciyi bekler; böylece akış, `fps` ile dizinin kendi kare hızından daha yavaş olan hızda çalışır. Sessiz kalmak yerine `FrameFetchError`&#x27;i tetikler: her kamera, bir nedenden dolayı bir sonraki tikin tekrarlanmasını reddettiğinde, 60 saniye boyunca kare içermeyen tikler olduğunda ve 60 saniye boyunca yeni tetikleyici olmadığında (“60 saniye boyunca yeni tetikleyici yok”). `ArrayHandle.frame_stream()` ile aynı uygulama. Bkz. [Kendi İş Akışınızda Canlı Kareler](#live-frames-in-your-own-pipeline). |
| `continuous(output_dir="output", *, format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, max_captures=None, duration_s=None, daq=None, daq_tolerance_ms=None, timeout=30.0)` | Arka ucun kendi sürekli yakalama işlevini başlatır (GUI&#x27;nin Sürekli modu) → [`ContinuousHandle`](#continuoushandle). Dizi tarafından yayınlanan **her** senkronize grubu, `capture()`&#x27;in yapacağı gibi (aynı dışa aktarma denetimleri), durdurulana kadar veya `max_captures` / `duration_s` (`None` = durdurulana kadar) komutları çalıştırılana kadar. Bu dizi için halihazırda bir işlem çalışıyorsa `ChlorosConnectError` hatası verir. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, orient=None, smart=False, after_seq=None, daq=None, daq_tolerance_ms=None, timeout=300.0)` | Senkronize edilmiş bir yakalama grubu. Bir `CaptureResult` (çerçeve sözlüklerinin listesi + `.skipped` + `.failed` + `.group_seq` + `.daq_files` + `.daq_matches` + `.alignment` + `.oriented`). Bir önceki sonucu`group_seq` sonucunu `after_seq` olarak aktarın; böylece yeni bir grubu beklemek yerine, ondan sonraki senkronize grubu elde edersiniz. Aşağıdaki dışa aktarma denetimleri. |
| `capture(..., smart=True)` | **Akıllı yakalama** — AE&#x27;nin tüm kameralarda sabitlenmesini bekler, ardından tetiklenir. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, orient=None, timeout=120.0)` | En hızlı yakalama: yalnızca ham veriler + atanan DAQ okuma değeri (+ serbest birleştirilmiş indeks). GUI&#x27;deki &quot;En Hızlı Yakalama&quot; düğmesini yansıtır. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Tekli / Sürekli / Aralık, tek bir sınırlı döngü içinde. `list[CaptureResult]` değerini döndürür.**`count` ve/veya `duration_s` gerektirir**, böylece sonlandırılır (SDK&#x27;da Ctrl+C yoktur). Ayrıca, bir geçiş tam sürücü bildirdiğinde (SDK&#x27;da `.failed` girişi ile `disk_full`), geçişten sonra erken sona erer; bir geçiş sürücünün dolduğunu bildirdiğinde (`.failed` ile `disk_full` içeren bir giriş); bu geçiş son sonuçtur. |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, expr=None, timeout=30.0)` | Canlı birleştirilmiş dizin görünümünün video/GIF olarak kaydedilmesine başla → `RecorderHandle`. Video, Motion-JPEG formatındadır (`.avi` (ffmpeg gerekmez). Açık bir GUI görünümü olmadığında arka uç bunu işler: `expr` (dizinin bant adlarındaki bir dizin ifadesi, örn. `"(NIR_850_RGN-Red_660_RGN)/(NIR_850_RGN+Red_660_RGN)"`) veya varsayılan olarak dizinin NDVI değeri. Dizi başına bir bileşik kaydedici. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, orient=None, timeout=30.0)` | Yüksek kare hızında ham Bayer seri çekimi başlat → `RecorderHandle`. `build_video()` ile çevrimdışı olarak yeniden işleyin. `orient=True`: `build_video()`&#x27;in ürettiği ürünler, kamera yönüne göre döndürülür (kamera başına ürünler kendi kameralarının yönüne göre, birleştirilmiş dizin ise ana kameranın yönüne göre); ham kareler orijinal haliyle kalır ve seri çekim manifestosu her bir üyenin yönünü kaydeder. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Kaydedilmiş bir ham seri çekimi, kalibre edilmiş videolara çevrimdışı olarak yeniden işleyin. İşlem tamamlanana kadar bekler (`wait=True`) ve derleme sonucunu döndürür: `{success, outputs, errors, skipped, substitutions, notes, degraded, combined}` — bkz. [derleme sonucu](#the-build_video-result) aşağıda. |
| `build_video_status(job_id, timeout=15.0)` | Çevrimdışı bir derleme işini sorgular: `{running, result, error, burst_dir}`. |
| `disconnect(keep_settings=False, clear_settings=False)` | Tüm diziyi serbest bırakır — **eğer bu oturum onu açmışsa** (`connect_array`): bu durumda `force_disconnect()` olur ve serbest bırakma onaylanmadığında `ChlorosConnectError` hatası verir (`with` bloğunun çıkışı asla hata vermez). `attach_array`&#x27;ten gelen bir tanıtıcıda, diziyi çalışır durumda bırakır ve bunu belirten bir satırı stderr&#x27;a yazdırır: dizi, GUI&#x27;ye veya başka bir komut dosyasına ait olabilir. Her kamera serbest çalışır durumda bırakılır ve bağlantı kurulduğunda olduğu gibi kalır; `keep_settings=True`, ayarlarını kameranın kendi belleğine kaydeder, `clear_settings=True` ise bu belleği fabrika ayarlarına sıfırlar (bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings)). |
| `force_disconnect(timeout=60.0, keep_settings=False, clear_settings=False)` | Diziyi kimin açtığına bakılmaksızın tüm diziyi serbest bırakır. Diziyi sonlandırmak istediğinizde (örneğin, farklı ayarlarla yeniden bağlamak için) bağlı bir tanıtıcı üzerinde kullanın. Arka uç serbest bırakmayı onayladığında veya serbest bırakılacak bir şey kalmadığında (dizi zaten kaldırılmışsa veya çalışan bir arka uç yoksa) geri döner; zaman aşımına uğrayan veya arka uç tarafından reddedilen bir sökme işlemi `ChlorosConnectError` hatasını verir. Her iki bayrakta da, her kameranın belleğinde ne olduğunu döndürür (`camera_memory`), aksi takdirde `None` değerini döndürür. |

`capture()` dışa aktarma denetimleri (GUI/CLI’nin kullandığı uç nokta ile aynı):

- `processing` / `levels` — `processing="all"` (veya `levels=["raw","radiance",…]`), kamera başına geçerli tüm dışa aktarma türlerini kaydeder; tek bir `processing` değeri ise sadece o seviyeyi kaydeder.
- `aligned` — **varsayılan olarak etkindir (`None`), çünkü Chloros uygulamasının aynı diziyi yakalaması**(dizi başına**Hizalanmış** Yakalama Ayarı). Her üyenin ham olmayan dışa aktarımı, dizinin [hizalama profili](#array-alignment) ile ana kameranın karesine hizalanır ve elemanların ortak örtüşme alanına göre kırpılır; böylece dosyalar piksel bazında hizalanır ve uygulamanınkiyle aynı boyut, kırpma ve `Alignment*` etiketleriyle çıkar (bir ROI altında dönüşüm, pencereye göre yeniden ayarlanır); `XMP-mapir:AlignmentJSON`, `"applied": true` ile birlikte `crop_roi`&#x27;i belirtir. Raw dosyaları, orijinal boyutlarında bozulmadan kalır ancak meta verilerinde dönüşüm bilgisini taşır. `aligned=False`, her dosyayı orijinal boyutunda, bozulmamış olarak kaydeder; `aligned=True`, açıkça belirtilen varsayılan ayardır. `continuous()`, `capture(smart=True)`, `capture_repeated()` ve proje düzeyindeki `ArrayHandle.capture()` / `capture_smart()` de aynı varsayılan ayarı kullanır. Warp uygulanmış olsun ya da olmasın, diziye bir hizalama profili atandığında her bir üye dosya, kamerasının `Alignment*` dönüşümünü kaydeder — profilin hizalamadığı bir kamera hariç (dışarıda bırakılmış veya başarısız bir çözümleme sonucu özdeşliğe zorlanmış) ve başka hiçbir kamera hizalanmadığında referans kamera hariç; bunlar `Alignment*` etiketlerini taşımaz. Dizide profil bulunmadığında çekim hizalanmamış olarak kaydedilir ve sonuçta bu durum belirtilir: `result.alignment`, `{"requested": True, "applied": False, "warnings": [...]}`&#x27;tir; warp&#x27;ın uygulanamamasının her bir nedeni için bir uyarı bulunur (profil yok, farklı çözünürlükteki üyeler, senkronize edilemeyen bir dizisenkronize değil, profilin eşleştirilemediği bir kare). `applied`, yalnızca warp talep eden her dışa aktarımın bunu elde etmesi durumunda `True` olur. Dizinin otomatik hizalaması, pozlama sabitlendiğinde profili oluşturur; `ArrayHandle.calibrate_alignment()` / CLI&#x27;nin `align-calibrate` komutu ise talep üzerine bir profil oluşturur. `result.oriented`, `orient=True`&#x27;in ürünleri döndürüp döndürmediğini belirtir.
- `render_index=False` — kamera başına bitki örtüsü indeksi katmanını atlar; varsayılan ayarda yapılandırıldığı yerde işlenir.
- `daq` / `daq_tolerance_ms` — DAQ okuma değeri, seçilen hiçbir katman için gerekli olmasa bile varsayılan olarak bir `.daq` sidecar dosyası olarak kaydedilir; `daq=False` bu durumda bunu atlar, ve `daq_tolerance_ms` eşleşme toleransını ayarlar (varsayılan 500). `force_daq=True`, varsayılanın eski yazılış şeklidir. Bkz. [Bir yakalamanın kaydettiği DAQ okuma değeri](#the-daq-reading-a-capture-saves--daq_files-and-daq_matches).
- `orient=True` — her üyenin **ürünlerini**(`display`, `radiance`, `reflectance`, kamera başına indeks ve bileşik) hizalama çarpıtmasından sonra kamera yönüne göre döndürür:**ana kameranın**kendi çerçevesine kaydırıldıkları sıradaki yönüne göre (`aligned` varsayılanı — üyeler eş kayıtlı kalır), aksi takdirde kendi kameralarının yönüne göre.**Varsayılan: her üye dosya kendi formatında**, dizi yakalamalarında her zaman olduğu gibi; `raw` ve `debayered` her durumda kendi formatında kalır. Her dosya, piksellerinin bulunduğu çerçevenin yönelimini kaydeder (`XMP-Camera:ChlorosRotation` / `ChlorosFlip*` / `ChlorosOrientationApplied` — bir ham dosya, kendi kamerasının), ve `Alignment*` XMP dosyası, yönlendirilmeden önce uygulanan orijinal çerçeve dönüşümünü korur; bu dönüşümü taşıyan bir RAW dosyası, aynı zamanda `ChlorosAligned*`&#x27;i (ana çerçevenin yönelimini) de kaydeder; bu, ana çerçeveye uyarlanmış bir ürünün yönelimidir. İşleme, her ürünü pikselerine ait olan yönelime göre döndürür: RAW dosyasını uyarlarken hizalanmış olanı (Dizi hizalaması açık), dönüşüm yapmazsa kameranın kendi dönüşümü kullanılır. `capture_repeated()` ve `capture_fastest()` bunu olduğu gibi geçirir; proje düzeyindeki `ArrayHandle.capture()` / `capture_smart()` de bunu alır.

**TIFF sıkıştırma (Yalnızca HTTP ayarı):** `ArraySession.capture()`, `compression` anahtarı göndermez, bu nedenle arka uç varsayılanı geçerlidir — `POST /api/camera/array/capture`, varsayılan olarak bir `compression` gövde parametresini okur: sıkıştırılmamış, sensörün pikselleri okunduğu gibi (~tam çözünürlüklü kare başına ~6,3 MB). `"deflate"` (kayıpsız zlib L1 + yatay tahminci, ~4,1 MB/kare) daha küçük dosyaları daha yavaş yazar — her ikisi de kayıpsızdır ve içe aktarımda aynı şekilde okunur. SDK bunun için herhangi bir kwarg sunmaz; alternatif çözüm `chloros-cli lattice array-capture --compression deflate` veya ham HTTP&#x27;dir. DEFLATE, Python GIL&#x27;ini tutar, bu nedenle sıkıştırılmış yazma işlemleri kamera başına yazar iş parçacıkları arasında paralelize edilmez — sensör hızında sürekli tam çözünürlüklü yakalama için sıkıştırılmamış varsayılan ayar gereklidir. Ayrıntılar: [CLI Referansı → dizi yakalama](cli-reference.md).**Ardışık gruplar (HTTP):** uç nokta `group_seq` değerini döndürür; bunu bir sonraki isteğinde `after_seq` olarak geri gönderen bir döngü, bir sonraki senkronize grubu alır — bu, önceki yakalama işlemi kaydedilirken yayınlanan grubu da içerir — istekten sonra yayınlanan bir grubu beklemek yerine; `skipped_groups` ise döngünün kaçırdığı grupları bildirir. `capture_repeated`, CLI’deki `--continuous` ve GUI’deki Sürekli modun tümü bunu yapar; aralık geçişleri her planlanan anda güncel kalır.**Üye başına dışa aktarma geçersiz kılmaları (yalnızca HTTP):**aynı uç nokta ayrıca `exclude_serials`’i de kabul eder (liste — kaydedilmiş kümeden üyeleri çıkar; dizi yine de tek bir senkronize grup olarak tetiklenir ve hariç tutulan üyeler `excluded`’te döndürülür), `serial_levels` (`{serial: [level tokens]}` kamera başına düzeyde geçersiz kılmalar) ve `serial_index` (`{serial: bool}` kamera başına dizin-üst katman geçersiz kılmaları) komutlarını da kabul eder. Bunlar GUI ile eşleşen gövde parametreleridir ve**SDK&#x27;de anahtar-değer çifti olarak henüz mevcut değildir**; haritalarda bulunmayan üyeler, dizi genelindeki `levels` / `render_index` değerlerine geri döner.

##### Atlanan Kameraları İnceleme — `CaptureResult.skipped`

`ArraySession.capture()`, bir `CaptureResult` döndürür; bu da bir `list` alt sınıfıdır: bunu yineleyin, indeksleyin, `len()` — mevcut tüm kalıplar çalışmaya devam eder. Yeni kod, `.skipped` özniteliğini inceleyerek hangi kameraların hariç tutulduğunu ve nedenini görebilir. En yaygın durum, `processing="radiance"` veya `"reflectance"` talep ettiğinizde karışık filtre dizisindeki RGB&#x27;leridir — geniş bantlı bir sensör için Bayer başına parlaklık anlamsızdır, bu nedenle arka uç anlamsız sonuçlar üretmek yerine bu kameraları atlar.

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

Neden belirticileri, `<level>-not-applicable-to-rgb-cam` biçimini izler (atlanan her seviye için bir giriş, her biri `level` değerini taşır). Kalibrasyon paketi yüklenmemiş bir kamera, `radiance` / `reflectance`&#x27;i `<level>-needs-calibration (connect the camera so its cal pack loads)` ile atlar ve kullanılabilir DAQ yansıma okuma değeri olmadığında `reflectance-skipped-no-dls` ile atlar. Diğer yansıma ile ilgili atlamalar şunlardır: `reflectance-skipped-no-fresh-dls` (yeni aşağı yönlü okuma mevcut değil), `reflectance-skipped-bound-daq-unavailable (…)` (bağlı DAQ&#x27;ya ulaşılamadı) ve `dls-uncalibrated-band-<nm>` — bant çoğunlukla DAQ ışık sensörünün radyometrik olarak kalibre edilmiş aralığının (~374–974 nm), bu nedenle DAQ tabanlı mutlak yansıma ayrımı reddedilir ve kare, sensör tepkisine düşürülür. Satıştaki SKU&#x27;lar arasında yalnızca F988 bunu tetikler; bu kameranın desteklediği yol, yansıma paneli iş akışıdır.

##### Yazılamayan Dosyalar — `CaptureResult.failed`

Dosyalarının yalnızca bir kısmını yazabilen bir yakalama yine de şu sonucu verir: liste, **yazılmış** olan tüm dosyaları içerir ve `.failed` geri kalanları listeler; her dosya için bir sözlük ve `serial` (birleştirilmiş kompozit ve bir dizinin `.daq` sidecar&#x27;ı için `None`), `level` (`raw`, `radiance`, …, `index`, `composite`, `daq`, veya `match` — `.daq`&#x27;in `.match.json` kökeni; bu durumda `.daq` yazılmış, ancak yan dosyası yazılmamıştır) ve `error`. Dolu bir sürücüde bu giriş aynı zamanda `disk_full: True`&#x27;i de içerir; o sürücüye daha sonra yapılacak her yakalama işlemi aynı şekilde başarısız olur, bu yüzden `capture_repeated()` burada durur. Kısmi bir yakalama için herhangi bir istisna oluşturulmaz ve `.failed` içindeki bir dosya diskte yarı yazılmış olarak bırakılmaz. Temiz bir yakalamada `.failed` boştur.

```python
result = arr.capture("output/", processing="all")
for f in result.failed:
    print(f"SN:{f['serial']} {f['level']} not written: {f['error']}")
if any(f.get("disk_full") for f in result.failed):
    raise SystemExit("drive full -- free space and capture again")
```

##### Yakalamayı okuyan DAQ, `.daq_files` ve `.daq_matches` dosyalarını kaydeder

Bir DAQ, kameraya veya diziye bağlandığında ya da havuza eklendiğinde, her yakalama işlemi kullandığı aşağı doğru okuma değerini kaydeder: çekim klasörünün `daq/` alt klasöründe bir `.daq` dosyası, bununla birlikte `.match.json` ve `.cameras.json` (okumanın ait olduğu). Bu durum ham veya debayered yakalamalar için de geçerlidir ve yakalama GUI, CLI veya SDK&#x27;den gelmiş olsun, dosya aynıdır. `daq=False`, seviyeleri DAQ (bir `radiance`, `reflectance` veya `display` seviyesi hala bunu yazar); `daq_tolerance_ms` ise o yakalama için tolerans sınırını kaydırır, kullanılan okuma değerini asla değiştirmez. Hangi DAQ: kameraya veya diziye bağlı olan (GUI’deki ışık sensörü seçimi, bir projenin bağlaması veya `chloros-cli daq pool-bind`), aksi takdirde arka uçtaki en güncel olan. CLI, kontrolleri `--no-daq` ve `--daq-tolerance-ms` olarak belirtir ([CLI Referansı](cli-reference.md#the-daq-reading-a-capture-saves)).

```python
r = cam.capture("out", levels=["raw"])      # a CameraSession; ArraySession results match
for m in r.daq_matches:                      # one per .daq in r.daq_files
    print(m["daq_file"], m["gap_ms"], m["in_tolerance"])
```

Bir yakalama, kullandığı ışık sensörü okumasını bir `.daq` olarak kaydeder ve yanında, okumanın pozlamaya nasıl bağlandığını belirten bir `<file>.daq.match.json` bulunur: `method`, `gap_ms` (pozlamadan en yakın DAQ okumasına) ve `in_tolerance` (500 ms içinde veya yakalamanın `daq_tolerance_ms`&#x27;i). Yansıma dosyasının XMP&#x27;si, `Chloros:DlsMatchMethod`, `Chloros:DlsMatchGapS` ve `Chloros:DlsInTolerance` ile aynı sonucu taşır.

Bir yakalama, pozlamadan sonra DAQ&#x27;nın ilk okumasını bekler; böylece onun her iki yanındaki okumaları birleştirebilir (`method` `interpolated`). Bir DAQ-E, gün ışığında her 0,4 saniyede bir, kapalı mekanlarda ise her 1-2 saniyede bir okuma gönderir; bu nedenle `capture()`, bu aralıkların yaklaşık bir buçuk katı kadar bir süre sonra geri döner; pozlamanın kendisi gecikmez. Gönderim yapmayı durdurmuş bir DAQ için bekleme yapılmaz. Bir DAQ-E okuma değeri, bir önceki okumadan bu yana geçen aralık boyunca ışığın ortalamasıdır; bu nedenle o aralığın ortasına yerleştirilir: `gap_ms` bu ortada ölçülür ve `.match.json`&#x27;`bracket_stamps_ns`, okumaların kendi zaman damgalarını verir. `ArraySession.continuous()`, yakalamayı yavaşlatmadan bekler. `capture_repeated()`&#x27;in geri-geriye doğru geçişleri beklemez, bu nedenle hızlarını korurlar ve pozlamadan önceki en yakın okuma değeriyle eşleştirilirler.

Bir okuma değeri asla geçerliliğini yitirmez, bu nedenle geç gelen bir değer nedeniyle hiçbir kare kaybolmaz — bunun yerine şu şekilde etiketlenir:

- **DAQ akışı durdu** — en yeni okuma değeri kullanılır ve `gap_ms` bunun ne kadar eski olduğunu belirtir (`in_tolerance: false` 500 ms&#x27;yi aştı).
- **Okunacak DAQ yok**— bağlantı kesildi (`disconnect()`, `chloros-cli daq pool-disconnect`) veya bağlı DAQ&#x27;ya ulaşılamıyor — kameranın son okuma değeri yeniden kullanılır ve**bu** pozlamaya göre açıklanır: `method`, `retained`, `gap_ms` bu pozlamadan alınır, `in_tolerance` yeniden hesaplanır ve `retained_from_epoch_ns` ilk eşleştirildiği pozlamanın adını alır. Hiç okuma değeri almamış bir kamera yine de yansıma değeri almaz ve sonuç bunun nedenini belirtir (`reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, ya da çekimin reddedilmesi).

Chloros&#x27;ta açık olan projenin klasörü içine bir çekimin kaydettiği dosyalar, uygulamadaki bir çekim gibi o projenin dosya listesine eklenir (başka bir yere kaydedilen dosyalar olduğu gibi kalır): `CameraSession.capture()`, `ArraySession.capture()` (`smart=True` de) ve `capture_cameras_transient()`. Sonuç, `.project_add`&#x27;te belirtilmiştir (`capture_cameras_transient()`: sözlüğün `project_add`&#x27;i) -- `held`, `files` ve arka ucun `message`&#x27;i; `held`, işleme süreci devam ederken `True`&#x27;tir; işleme süreci devam ederken dosyalar birleştirilir ve işlem bittiğinde birleştirilir. Projeye hiçbir dosya eklenmediğinde `None` değerini alır.

`CameraSession.capture()` ve `ArraySession.capture()`, sonuç olarak `.daq_files` ve `.daq_matches` olarak döndürülür; HTTP yakalama yanıtları (`/api/camera/<serial>/cli-capture`, `/api/camera/capture`, `/api/camera/capture-all-synced`, `/api/camera/array/capture`) `daq_files` ve `daq_matches` değerlerini taşır; her biri `.daq`: `daq_file`, `cameras`, `method`, `gap_ms`, `in_tolerance`, `tolerance_ms` ve `stream_fault` — DAQ&#x27;nın kendi akış hatası; yalnızca tolerans dışı bir okuma için kontrol edilir. Bir yakalama işleminin yansıma değerine güvenmeden önce, `in_tolerance` değerini `.match.json` veya XMP dosyasında ya da DAQ&#x27;nın [`status()`](#daqsensorsession-methods) değerini kontrol edin.

`processing` seviyeleri:

| Seviye | Çıkış |
| --- | --- |
| `"raw"` | Sensörden doğrudan gelen tek kanallı Bayer (tek renkli kameralar: tek bant). |
| `"debayered"` *(SDK varsayılanı)* | 3 kanallı renkli: ham veriden kalibrasyon paketinin karanlık ofseti (DSNU) çıkarılır, ardından OpenCV&#x27;nin kenar duyarlı demosaik işlemi uygulanır — doğrusallık LUT&#x27;u, düz alan veya renk ayrıştırma (tek renkli kameralar: 1 kanallı gri tonlamalı). Kaydedilen TIFF dosyası **RGB**kanal sırasına sahiptir (fotometrik RGB, böylece herhangi bir görüntü görüntüleyici gerçek renkleri gösterir); `grab()` / `frame_stream()`&#x27;in döndürdüğü bellek içi diziler OpenCV&#x27;nin**BGR** sırasındadır — kanalları tersine çevirin (`frame[..., ::-1]`) ve kaydedilmiş bir dosyayla karşılaştırın. `chloros-cli process`, `Debayered_Images` ile aynı sonucu yazar. |
| `"radiance"` | tam radyometrik zincir aracılığıyla float32 W/m²/sr/nm. Yalnızca multispektral — RGBkamera verileri atlanır. |
| `"reflectance"` | uint16 0..32768 (Pix4D uyumlu); mutlak referans için canlı DAQ eşleştirmesi gerektirir. Yalnızca multispektral. |
| `"display"` | GUI önizlemesiyle eşleşen tam zincir (kameranın profiline göre CCM + WB + gama). |
| `"all"` | **Her kamera için geçerli seviye başına bir dosya** (GUI&#x27;deki &quot;Tümünü Yakala&quot; / CLI varsayılan ayarıyla eşleşir). Dönüşte elde edilen `CaptureResult` dosyası, her `(cam, level)` için bir kare sözlüğü içerir; her sözlükte seviye bilgisi bulunur; uygulanamayan seviyeler ise `.skipped`&#x27;te görünür. Yakalamayı okuyan DAQ, bir `.daq` sidecar dosyası olarak kaydedilir. |

> **Not — varsayılan ayar, CLI&#x27;den farklıdır.** `ArraySession.capture()`&#x27;in varsayılan değeri `processing="debayered"`&#x27;tir; `chloros-cli lattice array-capture` komutunun varsayılan değeri ise `processing="all"`&#x27;tir. SDK&#x27;den `processing="all"` komutunu açıkça geçirerek CLI/GUI&#x27;deki çok düzeyli kaydetme işlemini yansıtın.

##### Dizi yakalama dosya adları

Her seviye, `output_dir`&#x27;in kendi alt klasörüne kaydedilir (`raw/`, `debayered/`, `radiance/`, `reflectance/`, `preview/`, `display`, `index/`, `composite/`). Her dosya, kameranın adını `SN<serial>-<filter>` şeklinde adlandırılır: önce seri numarası, ardından kameranın modelinin filtre kısmı — örneğin, bir `M3C-L87-FOCN` için `SN214000533-FOCN`, `SN261902142-F450`, `M3M-L87-F450` için — `-` ile birleştirilmiştir, böylece ad, daha önce olduğu gibi `_` ile ayrılmış alanlara sahiptir. Modeli okunamayan bir kamera sadece `SN<serial>` şeklindedir ve önceki sürümlerden alınan görüntülerde adda filtre bulunmaz. Dosya adı, üyeden kaç seviye istendiğine bağlıdır:

| Üyeden istenen | Dosya adı | Örnek (`reflectance`, varsayılan `format="tiff"`) |
| --- | --- | --- |
| **Bir seviye** (`processing="reflectance"` veya tek öğeli `levels`) | `sync_<timestamp>_SN<serial>-<filter>.<format>` — önceki sürümlerde olduğu gibi seviye soneki yoktur, böylece mevcut komut dosyaları çalışmaya devam eder | `reflectance/sync_<ts>_SN214000533-FOCN.tiff` |
| **Birkaç seviye** (`processing="all"` veya birden fazla öğe içeren `levels`) | `sync_<timestamp>_SN<serial>-<filter>_<level>.<format>` | `reflectance/sync_<ts>_SN214000533-FOCN_reflectance.tiff` |

[Kısma maskesi](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on), ürün adının ardından gelir; örneğin, `clamp_masks/<product folder>/<product name>_clampmask.tif`: tekseviyeli yansıma yakalama işlemi `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_clampmask.tif` olarak yazılırken, `processing="all"` ise `clamp_masks/reflectance/sync_<ts>_SN<serial>-<filter>_reflectance_clampmask.tif` olarak yazılır. Sonek kuralı, istenen seviyelere göre her üye için ayrı ayrı belirlenir; bu nedenle atlanan bir seviye (örn. bir RGB kameradaki yansıma) kuralı değiştirmez. Yolları oluşturmak yerine, döndürülen karelerden okuyun. Tek kameralı bir yakalama (`CameraSession.capture()`) her zaman şu seviyeyi ekler: `capture_<ts>_SN<serial>-<filter>_<level>.tiff`.

##### PNG / JPEG: Yalnızca görüntü ve dizin ürünlerini alır

PNG veya JPEG, kanal başına 8 bit tutar. Tutamayacağı bir seviyeye sahip bir dosya istenirse, herhangi bir şey yakalanmadan önce istek reddedilir ve seviyenin adı ile bunu destekleyen format olarak TIFF&#x27;in adı içeren bir mesaj görüntülenir:

- **`radiance`**(float32) ve**`reflectance`**(uint16, `32768` = 1.0) — ve dolayısıyla bunları içeren `processing="all"`. SDK, argümanlarda bunu kontrol eder: `capture()`, istek olmadan**`ValueError`** hatasını (`CameraSession`, `ArraySession`, `CameraHandle`, `ArrayHandle`, `capture_smart`).
- **8 bitten fazla veri akışı yapan bir kameradan gelen `raw` / `debayered`** (12-bit M3C, Mono12 M3M) — arka uç, her kameranın bit derinliğini bildiği için bunları reddeder: `error_code` ve `FORMAT_CANNOT_HOLD_LEVEL` ile HTTP 400 hatası, `ChlorosConnectError` (`CameraSession` / `ArraySession`) veya `RuntimeError` (`CameraHandle` / `ArrayHandle`). Bir kameranın tek seviyeli `debayered` yakalama işlemi, PNG/JPEG için 8 bitlik bir kare yakalar ve reddedilmez.

Radyometrik verileri `format="tiff"` (`ext=".tiff"`) olarak kaydedin; `display` ve indeksli renderlar için PNG/JPEG kullanın.

#### Otomatik Pozlamanın Çalışma Şekli

- **Önce pozlama, sonra kazanç.** Kazanç Otomatik konumdayken, pozlama izin verilen en uzun değere ulaşana kadar kazanç 0 dB&#x27;de kalır.
- **Manuel pozlama yapıldığında, kazancı değiştirene veya manuel moda ayarlayana kadar kazanç Otomatik modda kalır.** Otomatik moda geri döndüğünüzde, kazancın sınırı önceki durumuna döner.
- **Küçük parlak noktalar sahneyi karartmaz.** Çerçevenin %1&#x27;ine kadar olan speküler ışık göz ardı edilir; bilinen bir kalibrasyon hedefinin panellerinde ise panelin %0,5&#x27;ine kadar olan kısım göz ardı edilir.
- **DAQ pozlama üst sınırı, yalnızca kalibrasyon hedefi görüş alanında iken geçerlidir.** Bu, beyaz bir panelin kırpılmasını önler; panel görünmediğinde kamera sahneye göre pozlama yapar.
- **Sabit ışık değişmez ve değişen ışık saniyeler içinde dengelenir.** Sabit ışıkta pozlama ve sınırı sabit kalır; ışıkta büyük bir değişiklikten sonra görüntü birkaç saniye içinde dengelenir. Bir kırpılma artık pozlamayı kalıcı olarak sınırlamaz ve ilgi alanı (ROI) değişikliği durumunda pozlama yeniden ayarlanır.
- **Parlaklık hedefi ulaşılabilir aralıkta kalır.** Kamera pozlama sınırında iken bu hedef yükselmez ve kazancı artamaz; bu sayede geri gelen ışık, karede parlama oluşturmaz.
- **Otomatik pozlaması yanıt vermeyi durduran bir kamera, bulunduğu ışık seviyesinde tutulur.** Parlak güneş ışığı altında bir kamera en kısa pozlama süresine düşebilir ve tüm ayarları normal görünürken karanlık bir kareyle bu durumda kalabilir. Chloros, kareyi izler, o kamerayı o ışık için koruduğu seviyede tutar ve ışık değiştiğinde serbest bırakır. Karanlık veya örtülü bir ışık sensörü bunu etkilemez.

#### Dizi Üyelerinde Pozlama

Bir LATTICE kamerası, `ExposureTime` değerini kare hızının bir periyoduyla sınırlar. Dizi akış yaparken, Chloros her üyeyi `AcquisitionFrameRateEnable` açık olarak çalıştırır, tetikleme hızının biraz üzerinde bir hızda çalıştırır; böylece bir elemanın pozlama üst sınırı, **herhangi bir ROI&#x27;de** tetikleme periyodunun biraz altında kalır:

- Bir elemanın kare periyodu, tetikleme periyodundan periyodun %15&#x27;i kadar bir gevşeklik çıkarıldıktan sonra kalan süredir (en az 3 ms, en fazla periyodun yarısı) ve pozlama üst sınırı, bu kare periyodundan yaklaşık 0,1 ms çıkarıldıktan sonra kalan süredir. Örnek: 10 fps&#x27;lik bir tetikleme (100 ms), yaklaşık 11,8 fps&#x27;lik bir üye hızı ve yaklaşık 85 ms&#x27;lik bir maruz kalma tavanı sağlar.
- **Bu üst sınırdan daha uzun süren manuel bir `ExposureTime`, tetikleme hızını bu sınıra uyacak şekilde yavaşlatır.** Chloros, önce o üyenin üst sınırını bu değere açar ve daha yavaş hızın birkaç tetikleme periyodu boyunca çalışmasına izin verir (yazma işlemi birkaç saniye daha uzun sürebilir), ardından değeri yazar ve tetik, pozlamanın izin verdiği hızdan daha hızlı ateşlenmez. Otomatik hız dizisinde, manuel pozlama nedeniyle yavaşlatılan her üye tekrar otomatik pozlamaya döndüğünde, önceki hızı geri kazanılır. Kameranın kendisinin alamayacağı bir değer yine reddedilir; bu durumda hata mesajında kameranın kabul ettiği aralık belirtilir ve üyenin otomatik pozlaması, yalnızca o yazma işlemi için Kapalı konuma getirilmişse yeniden etkinleştirilir. 1.3.0 sürümünden önce, küçük bir ROI, her üyeyi sensörün kendi serbest çalışma periyoduyla sınırlıyordu, tetikleme hızı ne olursa olsun: 768 × 304 çözünürlükte 4,9 ms; bu nedenle hiçbir üyenin parlaklığı artırılamazdı.
- Chloros, daha hızlı bir tetiklemeye uydurmak için manuel pozlamayı asla kısaltmaz. Otomatik hız, manuel pozlamaların izin verdiği sınırlar içinde kalır; bir pozlamanın izin verdiğinden daha hızlı sabitlenmiş bir hız, o üyeye buna yetecek kadar uzun bir tavan bırakır ve pozlamanın izin verdiğinden daha hızlı tetiklemeler almaz. Her üyenin her tetiklemeyi gerçekleştirmesi gerektiğinde, pozlamayı veya sabitlenmiş hızı düşürün.
- **Geri okuma:** Bir elemanın düğümleri okunduğunda, elemanın hızında `AcquisitionFrameRateEnable` True ve `AcquisitionFrameRate` değerleri gösterilir; dizi durduğunda etkinleştirme durumu tekrar kapalı hale gelir.
- **Bu iki düğüm diziye aittir. Bir dizi üyesine `AcquisitionFrameRateEnable` veya `AcquisitionFrameRate` yazmayın** — bunun yerine, dizininhızını `target_fps` / `set_target_fps()` ile ayarlayın.

Bir üyenin maruz kalma değerini `arr.set_member_settings(serial, exposure_time=20000)` ile yazın (`CameraSession.set_settings()` anahtar kelimeleri; `chloros-cli lattice exposure --serial` de aynı işlevi görür). Üye, dizide kalır. Bir dizi üyesini `connect_camera()` ile açmayın: connect, halihazırda bir dizide bulunan bir kamerayı reddeder ve döndüreceği `CameraSession`, bağlantı kesildiğinde kamerayı dizisinden kaldırır. `ArraySession.status()["member_ae"]`, her üyeninen yeni karesinden pozlama ve kazanç değerlerini bildirir.

### Her mod için tek bir proje kaydı

Bir proje klasörü, her cihazın ayarlarını içerir: `cameras.json` — her kameranın ayarları (`cameras[].settings`, tek bir kamera ve her dizi üyesi için) ve her dizinin dizi genelindeki ayarları (`arrays[].capture`: format, ROI, binning, tetikleme hızı, kablo bütçesi) — ve `sensors.json`, her bir ışık sensörünün ayarları (entegrasyon süresi, kare ortalaması, sınır). Uygulama, CLI, SDK ve MCP sunucusu, aynı dosyaları aynı şekilde okur ve yazar:

- **Bir projeye bağlı bir cihaz, o projeye kaydedilir ve her değişiklik o projeye kaydedilir.** &quot;Bir proje altında&quot; ifadesi, arka uçta (uygulamanın) bir projenin açık olması veya — arka uca kendi klasör adını veren bir `ChlorosProject` (`open_project()`) aracılığıyla (`project_path`). Projenin henüz içermediği bir kamera, dizi veya ışık sensörü, bağlandığında ayarlarıyla birlikte dosyalarına eklenir (`connect_camera()`, `connect_array()`, ışık sensörü bağlantısı, CLI, uygulama, `chloros-mcp`), ayarlarıyla birlikte eklenir. Herhangi bir moddan yapılan değişiklik (`set_settings()`, `ArraySession.set_member_settings()`, `set_target_fps()`, dizi ayarları, `set_cap()`, CLI, uygulamanın denetimleri) bir an sonra o projenin dosyalarına kaydedilir. Bir kamerada ayarlanan pozlama, kazanç veya AE hedefi &quot;saklı&quot; olarak kaydedilir; böylece proje yeniden açıldığında bu ayarlar tekrar korunur.
- **Bir proje açıldığında, cihazlar o projeden ayarlanır**; her modda: kendi ayarları olmayan bir kamera, dizi veya ışık sensörü, projeninkaydedilmiş ayarlarını alır (bir proje açıkken `lattice cam-connect` / `array-connect`, `chloros-cli project connect`, `open_project().connect_all()`, uygulamanın yeniden açılması). Yeni bir kimlik altında bağlanan bir dizi, projenin aynı kameralarla kaydettiği diziyi günceller; diğer kameralara sahip bir dizi ise bunun yanına eklenir.
- **Uygulamanın kendi kaydetme işlemi, bir cihazın canlı ayarlarını** bunların eski kopyasıyla asla üzerine yazmaz ve CLI veya SDK çağrısı yapanların kaydettiği üye bazındaki geçersiz kılmaları korur.
- **Ayarladığınız piksel formatı, projenin koruduğu formattır.** Dizi Ayarları&#x27;nda, `--pixel-format` veya `connect_array(pixel_format=)` ile ayarlanan format (8 bit dahil), aynen kaydedilir ve yeniden bağlanırken aynen uygulanır. Herhangi bir ayar yapılmadığında, her kamera 12 bitlik varsayılan ayarlarıyla bağlanır (mono sensörde `BayerRG12`, `Mono12`). Biçimin adı, bit derinliğini ifade eder; aile ise sensöre aittir; bu nedenle bir renkli kamera asla Mono formatına, bir mono kamera da Bayer formatına ayarlanmaz, ve `[PixelFormat <serial>] ... written` satırı, bir formatın eşleştirildiğini belirtir. 8-bit bir çekimin dışa aktarımları, yansıma dahil olmak üzere 8-bit&#x27;tir: yeniden ölçeklenir ve `PixelScale` etiketiyle damgalanır.
- Herhangi bir projeye bağlı olmayan bir cihaz hiçbir şey kaydetmez.
- **Bir kayıt, kendisinden sonra oluşturulan bir projeye bağlıdır; kapatıldığında projeyle birlikte kalır.**Bir proje oluşturulduğunda — `create_project()`, `chloros-mcp`&#x27;in `create_project`&#x27;i veya uygulamanın**Yeni Proje**— veya çoğaltıldığında (uygulamanın**Çoğalt**seçeneği, bu işlem kopyayı açar), kayıt hâlâ daha önce açık olan projeye yazmaya devam ederse, kayıt devam eder ve yazılanlar yeni projenin içinde, aynı yere (aşağıda). Mevcut bir projenin açılması — `ChlorosProject.process()` (bu, arka planda projesini açar), `chloros-cli process`, uygulamanın**Projeyi Aç** seçeneği — kayıt, kayıt yaptığı projeyle birlikte devam eder: kayıt orada biter ve dosyaları o projenin dosya listesine eklenir (uygulama, bir projeyi açmadan önce her kaydı durdurur).
  - `ArraySession.record()` ve `ArraySession.burst()`: kayıt durduğunda video veya her kareyi içeren seri çekim klasörü, bütün olarak buraya taşınır. Durdurma işlemi sonucunda yeni yol bildirilir (`out_dir`, `video_path`) bildirilir ve seri çekimden oluşturulan bir derleme buradan çalışır. Sürücünün taşıma işlemini reddetmesi durumunda kayıt, yazıldığı yerde kalır ve sonuçta bu durum belirtilir (`relocation_error`). Durdurulan bir seri çekimin ham kareleri ve `.daq`, sonlandığı projenin dosya listesine eklenir; video ise dosya listesine eklenmez.
  - `ArraySession.continuous()`: değişiklikten sonraki her yakalama yeni projeye kaydedilir; daha önce kaydedilenler olduğu yerde kalır.
  - `DAQSensorSession.record_start()` (ve uygulamanın kendi ışık sensörü kaydı): kayıt durduğunda `.daq`, yeni projenin `light_sensor` klasörüne taşınır; `record_stop()` yeni yolu döndürür ve dosya o projenin dosya listesine eklenir.

  Açık projenin dışındaki bir klasöre yapılan kayıt olduğu yerde kalır ve `start_camera_continuous()` / çoklu akış işleri kendilerine atanan klasörü korur.
- **Uygulamada, yeni bir proje açıldıktan sonra yapılan yakalamalar bu projeye eklenir.** Bir proje oluşturmak veya kopyalamak, tüm yakalama işlemlerini ve kayıtları devam ettirir; bundan sonra yapılan her yakalama (Tümünü Yakala, Yakalamaya Başla), yeni projeye kaydedilir ve dosya listesine eklenir. Uygulamada mevcut bir projenin açılması, önce tüm işlemlerin durdurulmasına neden olur.
- **Bir proje, kayıt yaparken taşınmaz.**Açık projeyi başka bir çalışma dizinine taşımak (uygulamanın**Ayarlar → Çalışma Dizini**; hiçbir SDK çağrısı projeyi taşımaz, `/api/move-project` adresine gönderim yapan bir HTTP istemcisi bunu yapar) reddedilir ve HTTP 409 `PROJECT_RECORDING` hatası verilir; bu sırada bir dizi kaydı veya seri çekim, bir dizi sürekli yakalama, bir kamera sürekli / çoklu akış işi veya bir ışık sensörü kaydı bu dizine yazıyorsa: `Project '<name>' is recording (<what>), so moving the project now is refused. Stop the recording, then try again.`

Bu durum, bağlantı kesildiğinde kameraların hangi ayarlarda kaldığından ayrıdır (bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings)): bir proje açıldığında kameralarının ayarlarını kendisi belirler.

### Kamera belleği — `keep_settings` / `clear_settings`

Bir dizi bağlandığında, kameralar dizinin ayarlarını alır ve siz de bunları değiştirebilirsiniz. Chloros kameraların bağlantısını kestiğinde — `ArraySession.disconnect()` / `force_disconnect()`, geçici bir işlevin sonu, `start_camera_continuous()`&#x27;in durdurulması — her birini, bağlanmadan önce bulunduğu duruma geri getirir: tetikleyici ve senkronizasyon hattı kablolaması, PTP ve kamera ayarları — `ExposureAuto` / `GainAuto` (ve manuel `ExposureTime` / `Gain`), `TargetBrightness` ile AE sınırları ve sönümleme, beyaz dengesi, siyah seviyesi, `PixelFormat`, binning ve ROI, renk işleme süreci (`GammaEnable` / `Gamma`, `LUTEnable`, `ColorTransformationEnable`), `ReverseX` / `ReverseY`, `UserSetSelector`, PTP&#x27;nin `PtpSlaveOnly` / `PtpDomainNumber`, paket boyutu (`GevSCPSPacketSize` / `DeviceStreamChannelPacketSize`), pacing (`DeviceLinkThroughputLimitMode` / `DeviceLinkThroughputLimit`, `GevSCPD`, `GevSCFTD`), parça verisi (`ChunkModeActive`, her bir `ChunkEnable`&#x27;in kendi `ChunkSelector`&#x27;i altında), bir dizi tetikleyici girişi parazit giderme işlemi, bir bağımlı cihazın senkronizasyon hattını sağlar (`LineFilterSelector` / `LineFilterWidth`) ve görüntü alma kare hızı (`AcquisitionFrameRate` yalnızca etkinleştirme açıkken: kapalıyken hız, bir ayar değil, zamanlamanın izin verdiği en yüksek değeri okur). Yalnızca farklı olan şeyler yazılır: kameranın ilk duruşu, zamanlama ve kare periyoduna ihtiyaç duyan bir pozlamadan önceki kare hızı.

Kameraları bir proje dışında kullanmak için, iki argüman kameranın **kendi belleğine** yazılır — herhangi bir yazılımda (`disconnect()` / `force_disconnect()`, tüm geçici işlevler, `start_camera_continuous()` ve `ChlorosProject.disconnect_all()`; MCP: `disconnect_device` ve geçici araçların aynı argümanları; CLI&#x27;de: `--keep-settings` / `--clear-settings`):

- **`keep_settings=True`**, her kameranın ayarlarını oturumun bittiği andaki haliyle belleğine kaydeder (UserSet2, önyükleme seti oluşturulur). Kameralar hala serbest çalışır durumda bırakılır (`TriggerMode` `Off`), senkronizasyon hattı kablolaması olduğu gibi kalır. UserSet2&#x27;si olmayan bir kamera, ayarlarını UserSet1&#x27;e kaydeder; bu bilgisayar bunu hatırlar, bu nedenle Chloros&#x27;un dizi kendi kendini onarma özelliği — Chloros&#x27;un temel ayarından sapmış bir UserSet1&#x27;i sıfırlayan — bu kamerayı olduğu gibi bırakır.
- **`clear_settings=True`**, her kameranın belleğini fabrika ayarlarına sıfırlar: Chloros&#x27;un temel değeri, yani dizisinin kendi kendini onarma işlevinin temel aldığı değer — `TriggerMode`, `Off`, `TriggerSource` ve `Line2` ile fabrika varsayılanları, otomatik pozlama ve bir RGB kameranın kalibre edilmiş beyaz dengesi korunur — UserSet1&#x27;de, önyükleme seti oluşturulur.
- İkisi birden değil (`ValueError`; arka uç 400 yanıtını verir). Yanıtta `camera_memory` bulunur; kamera başına `{"saved_to": "UserSet2" | "UserSet1" | None, "errors": [...]}` veya `{"cleared": bool, "errors": [...]}` — `force_disconnect()` bunu döndürür; bir iş için `stop()` ile gelir.

Uygulama bunları sunmaz.

**Her çıkışta kameralar geri yerleştirilir.**Bulunduğu haliyle geri yerleştirme işlemi her çıkış yolunda çalışır: `CameraSession.disconnect()` (uygulamanın**X**numaralı kamerası), uygulamanın**X** bir dizide veya herhangi bir dizi elemanında (dizinin tamamı serbest bırakılır), uygulamanın kapatılması (arka uç, her kamerayı, diziyi ve çalışmakta olan sürekli kaydı geri yerleştirmek için en fazla 20 saniye süre alır; hâlâ çalışmakta olan geçici bir işlev veya tek seferlik çağrı, bir sonraki güvenli noktada durur, önce kameralarını geri yerleştirir, en fazla 8 saniye sürer ve HTTP 503 `SHUTTING_DOWN` yanıtını verir; hâlâ çalışmakta olan bir `connect_array()`, bir sonraki kamerasından önce durur ve açtığı kameraları geri yerleştirir, bu işlem en fazla 10 saniye sürer ve `ChlorosConnectError` (HTTP 503 `SHUTTING_DOWN`) hatasını verir; kapatma sırasında gelen bir iş ise hiçbir şeyi açmaz: HTTP 503 `SHUTTING_DOWN`)ve sonlandırılan veya terminali kapanan bir `chloros-cli` komutu (işi durdurulur ve kameraları ilk olarak geri yerleştirilir). Çöken veya zorla sonlandırılan bir arka uç hiçbir şeyi geri yerleştiremez; güç döngüsü her kamerayı belleğine geri döndürür.**Bir proje, kameralarını kendisi ayarlar.** Bir dizi, bir proje aracılığıyla bağlandığında — uygulamanın açık projesi veya kendini arka uca tanıtan bir `ChlorosProject`, ve bu dizi kendini arka uca (`project_path`) bildirirse — her üye, projenin kendisi için kaydettiği ayarları alır: üzerinde tutulan bir pozlama, kazanç veya AE hedefi (sizin ayarınız olarak tutulur, bu nedenle otomatik pozlama bu ayarı değiştirmez) ve beyaz dengesi modu (Chloros düzeltmesi; kameranın kendi beyaz dengesi kapalı kalır), kameranın belleğinde ne varsa.

### Çekim Modları ve Kaydediciler

Dizi yüzeyi, GUI çekim panelini yansıtır: Tekli / Sürekli / Aralık / En hızlı deklanşör modları, ayrıca iki kaydedici (canlı kompozit video ve ham seri çekim → çevrimdışı yeniden işleme).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Continuous — every synced group for 60 s, saved by the backend itself
    with arr.continuous("run1/", processing="raw", duration_s=60) as run:
        run.wait()
    print(run.result["captures"], run.result["captures_per_s"],
          run.result["stop_reason"])

    # Combined-index video/GIF recorder: the GUI's combined view, or with no
    # GUI open the array's NDVI (pass expr="..." for another index)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    if rec.result and rec.result.get("error"):   # leaving the block does not raise
        print("recording failed:", rec.result["error"])
    else:
        print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**, SDK’nin Sürekli/Aralıklı döngüsüdür. Bir komut dosyasından bu döngüyü durduracak `Ctrl+C` olmadığı için, `count` ve/veya `duration_s` değerlerini**mutlaka** geçmelisiniz (ikisinden birine ulaşıldığında durur). `interval_s`, her geçişin başlangıcından itibaren ölçülür (GUI ile uyumludur). Sürekli geçişler (`interval_s=0`), `group_seq` → `after_seq` şeklinde zincirlenir; dolayısıyla bunlar, dizininKalan kwarg&#x27;lar doğrudan `capture()`&#x27;e aktarılır.
- **`continuous`**, arka ucun kendi sürekli yakalama özelliğidir — GUI’deki Sürekli moddur.**Yüksek kare hızında her tetiklemenin diske kaydedilmesini istediğinizde `capture_repeated()` yerine bunu kullanın:** `capture_repeated()`, komut dosyanızdan grup başına bir HTTP isteği gönderirken, `continuous()`, gruplar arasında gidiş-dönüş olmadan arka uçta iki yakalama işlemini aynı anda sürdürür. 9 kameralı bir dizide ROI 512&#x27;de ölçülen değer: 73–77 yakalama/sn; buna karşılık, yaklaşık 78 yayınlayan bir dizi. Aralıklı yakalama (`interval_s`) için `capture_repeated()`&#x27;i kullanın ve tek tek incelediğiniz geçişler için kullanın. Windows&#x27;ta, yüksek hızlar için arka ucu Microsoft Defender&#x27;dan hariç tutun (bkz. [Adım 1](#step-1--install-the-chloros-platform-package)).
- **`record`** *izleme sınıfındadır*: işlenmiş canlı birleşik dizin bileşimini kaydeder. GUI&#x27;nin birleştirilmiş görünümü açıkken, bu görünümü görüntülendiği şekilde kaydeder. Hiçbiri açık değilse, arka uç görünümü kendisi oluşturur: `expr` eğer bir tane geçirirseniz, aksi takdirde dizinin NDVI&#x27;si (NDVI ve NIR bantları 660 nm ve 850 nm&#x27;ye en yakın olanlar, canlı görünümde seçildikleri şekilde), dizi hizalandıktan sonra. `expr`&#x27;teki bantları, dizinin onlara verdiği adlarla adlandırın: M3C kamera için `<band>_<filter>` (`NIR_850_RGN`, `Red_660_RGN`, `Orange_615_OCN`), M3M için `Mono_<filter>` (`Mono_850`); kısa bir renk adı (`NIR`, `Red`) yalnızca dizinin bir bandında bulunuyorsa çalışır. Kayıt sırasında açılan bir GUI görünümü, açık kaldığı sürece kaydedilir. Hiçbir kare yazmayan bir kayıt, `error` ve `"no-frames: …"` hatalarıyla sonuçlanır (dizi hala hizalanıyor veya pozlamasını ayarlıyor olabilir), ve `stop()` hatası verilir (bkz. [`RecorderHandle`](#recorderhandle)). Dizi başına bir bileşik kaydedici (zaten bir tane çalışıyorsa hata verir).
- **`burst` → `build_video`** *analiz sınıfı*dır: `burst`, ham kareleri + kare başına bir manifest + `<output>/bursts/<base>/` altında her bir farklı DLS okuması için bir `.daq`&#x27;i, yakalama döngüsünün tam hızında yazar (zincir yok, exiftool yok, canlı görüntüleme yok). `build_video`, her kareyi en yakın `.daq` ile zaman açısından eşleştirir ve içe aktarma boru hattının parlaklık/yansıma/indeks zincirini yeniden çalıştırır. `products`, `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}`&#x27;in bir listesidir (varsayılan: birleştirilmiş indeks). `burst().stop()` ayrıca, en iyi sonuç elde edilmeye çalışılan birleştirilmiş-indeks derlemesini otomatik olarak başlatır; bu, durdurma sonucunda `build_job` olarak döndürülür.

##### `build_video` sonucu

| Anahtar | Anlam |
| --- | --- |
| `success` | `True` yalnızca istenen her ürün talep edildiği gibi oluşturulduğunda: en az bir çıktı, `skipped`&#x27;te hiçbir şey yok, ve `degraded`&#x27;te hiçbir şey olmaması. |
| `outputs` | Yazılan videoların (ve GIF&#x27;lerin) yolları. |
| `errors` | Sorunlar, metin olarak. |
| `skipped` | Video üretmeyen her talep edilen ürün için bir adet `{"serial", "level", "reason"}` — bir kameranın (o kamera için kalibrasyon paketi yok, yansıma için DLS okuması yok, işleme zincirinde hata) ya da `"kind": "combined"`, `serial` ve `None` değerlerini içeren birleşik endeksler (kameralar arasında kırmızı/NIR bant çifti yok, her iki bant için de radyans içeren grup yok). Herhangi bir giriş, `success` ve `False`&#x27;i oluşturur; diğer videolar yine de yazılır ve listelenir. |
| `substitutions` | Kameralar istenenden farklı bir seviyede işlendi ve nedeni — radyans/yansıtma değeri istenen bir RGB kamera, kameranın kalibrasyon paketi mevcut olduğunda beyaz dengesi ayarlanmış görüntü videosunu alır. Bir hata değildir. |
| `notes` | Sorun değil, bilgilendirme metni — örneğin, kamera başına indeks ifadesi olmayan bir kameranın indeks videosu yoktur veya bir RGB kameranın görüntü videosunda beyaz dengesi ayarlanmamıştır (kalibrasyon paketi yok veya fabrika beyaz dengesi içermeyen bir paket var), burada kameranın adı belirtilir. Hata değildir. |
| `degraded` | Bir ürün yazılmış ancak istenen ürün değilse `True`: çevrimdışı birleştirilmiş indeks, yerleşik kırmızı/NIR normalize farkıdır, bu nedenle yapılandırılmış indeks ifadesi değerlendirilmez. `success` ve `False` hatalarını oluşturur. |
| `combined` | Birleştirilmiş indeksin özeti: kullanılan bantlar, LUT aralığı, oluşturulan kareler. |
| `clamp_masks` | Herkare bazında kalibre edilmiş TIFF&#x27;lerin [karıştırma sınırlama maskeleri](#unmix-clamp-mask--which-pixels-a-band-was-clamped-on), TIFF&#x27;lerin `<level>/` klasörünün yanındaki `clamp_masks/<level>/` içinde, bir pikselin sınırlandığı her TIFF için bir tane bulunur. Bir videoda bu maskeler bulunmaz. |
| `clamp_fractions` | Bir videonun maske yerine içerdiği bilgi: her video ürünü için bir `{"kind", "serial", "level", "frames"}` (`kind` `"combined"` birleşik dizindeki üye kameralar için), `frames` bir `[frame, R, G, B]` listesi — o karenin, R / G / B kanalının tuttuğu bantta unmix tarafından sınırlandırılan piksellerin payı (`Chloros:Band{R,G,B}UnmixClampFraction`). |

#### `RecorderHandle`

`ArraySession.record()` ve `ArraySession.burst()` tarafından döndürülür. Kapsamdan çıkıldığında otomatik olarak durmak üzere bir bağlam yöneticisi olarak kullanın veya manuel olarak çalıştırın.

| Üye | Açıklama |
| --- | --- |
| `job_id` | Arka uç iş kimliği (str). |
| `kind` | `"composite"` (`record`&#x27;ten) veya `"raw"` (`burst`&#x27;ten). |
| `start_stats` | `start` çağrısı tarafından döndürülen sözlük. |
| `result` | Çalışma sırasında `None`; durdurulduktan sonra nihai durdurma sonucu sözlüğü. |
| `stats(timeout=10.0)` | Canlı iş istatistikleri (yazılan kareler, gerçekleşen fps, geçen). |
| `stop(timeout=60.0)` | Kaydediciyi durdurur; son sonucu döndürür ve önbelleğe alır (durdurma isteğinin kendisi başarısız olursa, `.result`, `None` olarak kalır). Başarısız olan bir `record()` kaydı için — hiçbir kare yazılmamış olan (`error` `"no-frames: …"`) — sonucu `.result`&#x27;te önbelleğe alır, ardından `ChlorosConnectError` istisnasını tetikler. İdempotenttir (ikinci çağrı, istisna tetiklemeden önbelleğe alınmış sonucu döndürür). `with` bloğunda kalmak, hata oluşturmadan kaydediciyi durdurur; bu nedenle, bundan sonra `rec.result.get("error")`&#x27;i kontrol edin. |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

#### `ContinuousHandle`

`ArraySession.continuous()` tarafından döndürülür. Bunu bir bağlam yöneticisi olarak kullanın — `with` bloğundan çıkmak, yakalama işlemi hala devam ediyorsa onu durdurur — veya `status()` / `wait()` / `stop()` ile çalıştırınkomutlarıyla çalıştırın. Her bir dizi için aynı anda yalnızca bir sürekli yakalama çalışır.

| Üye | Açıklama |
| --- | --- |
| `start_status` | Başlatma çağrısı tarafından döndürülen durum sözlüğü. |
| `result` | Çalışma sırasında `None`; yakalama işlemi sona erdiğinde son durum sözlüğü (`stop()` tarafından ayarlanır, ya da sona erdiğini algılayan bir `status()` / `wait()` tarafından ayarlanır). |
| `status(timeout=10.0)` | Canlı durum sözlüğü (aşağıdaki alanlar). |
| `wait(timeout=None, poll_s=0.5)` | Yakalama kendiliğinden sona erene kadar (`max_captures` veya `duration_s` değerine ulaşılması, dizinin silinmesi, sürücünün dolması veya bir hata oluşması) bekle ve son durumu döndür. `timeout` ile, işlem hala devam ediyor olsa bile o kadar saniye sonra en son durumu döndürür: `running`&#x27;i kontrol edin. |
| `stop(timeout=30.0)` | Yakalamayı durdurur ve son durumunu döndürür. İdempotent (ikinci bir çağrı, önbelleğe alınmış sonucu döndürür). |

Durum alanları:

| Alan | Anlam |
| --- | --- |
| `running` | Yakalama çalışırken True değerindedir. |
| `captures` | Senkronize edilen gruplar kaydedildi. |
| `files` | Dosyalar yazıldı. |
| `failed_files` | Yazılamayan dosyalar ([`CaptureResult.failed`](#yazılamayan-dosyalar--yakalama-sonucu-başarısız) gibi). |
| `failed_captures` | Yakalama işleminin yeniden denendiği durumlar da dahil olmak üzere, tamamen başarısız olan yakalama denemeleri (dizide zamanında yeni grup bulunmazken HTTP 503 hatası). |
| `skipped_groups` | Dizinin yayınladığı ancak kaydedilemeyen gruplar (yakalama işlemi geride kaldı). |
| `captures_per_s` | Başlangıçtan bu yana ortalama kaydetme oranı. |
| `elapsed_s` / `started_at` | Şu ana kadar geçen süre (saniye); başlangıç zamanı (Unix epoch saniyeleri). |
| `last_group_seq` | Kaydedilen en yeni senkronize grup. |
| `last_error` | En son hata veya `None`. |
| `stop_reason` | Bir kez sona erdiğinde: `count` (`max_captures`&#x27;e ulaşıldı), `duration` (`duration_s`&#x27;e ulaşıldı), `stopped` (`stop()` çağrıldı), `array_gone` (dizi bağlantısı kesildi), `error` (ard arda 20 hata veya arka uç tarafından reddedilen bir istek), `disk_full` (sürücüde yer kalmayan bir dosya). `None` çalışırken. |
| `max_captures` / `duration_s` | Başlatıldığında sahip olduğu sınırlar (`0` = yok). |
| `defender_cores` | Son 10 saniyelik aralıkta Microsoft Defender tarafından kullanılan CPU çekirdekleri; `None` bir değer ölçülene kadar ve Defender&#x27;ın bulunmadığı bir ana bilgisayarda her zaman `None`. |
| `defender_warned` | Yakalama, Defender&#x27;ın onu yavaşlattığını (1 çekirdek veya daha fazla) kaydettiğinde True olur. Arka ucu Defender taramasından hariç tutun — bkz. [Adım 1](#step-1--install-the-chloros-platform-package). |

```python
run = arr.continuous("captures/run1", processing="raw", max_captures=5000)
try:
    while True:
        st = run.wait(timeout=5)              # returns every 5 s while running
        print(f"{st['captures']} captures, {st['captures_per_s']:.1f}/s, "
              f"{st['skipped_groups']} skipped")
        if not st["running"]:
            break
finally:
    final = run.stop()                        # no-op once it has ended
if final["failed_files"] or final["stop_reason"] in ("disk_full", "error"):
    raise SystemExit(f"continuous capture: {final['stop_reason']}: "
                     f"{final['last_error']}")
```

### Kendi İş Akışınızda Canlı Kareler

Senkronize kareleri kendi kodunuza (makine öğrenimi, özel bir görüntüleyici, kendi kayıt cihazınız) aktarmak için `ArraySession.frame_stream()`&#x27;i kullanın. Her tick, her kamera için bir `GrabResult` üretir: Her kamera için bir `{serial: numpy array}`, **hepsi tek bir tetikleyiciden**, ve o tetikleyicinin kimliği `.group_id`&#x27;te yer alır. Bu, proje API&#x27;sindeki `ArrayHandle.frame_stream()` ile aynı uygulamadır, bu nedenle [Okuma başına tek tetikleyici](#one-trigger-per-read--group_id), [`band`](#exporting-a-single-band--band), [`align`](#aligning-the-streams--align) ve [`bit_depth`](#bit_depth--trading-precision-for-bandwidth) başlıkları altında yer alan her şey her ikisi için de geçerlidir.

```python
import chloros_sdk
from chloros_sdk import FrameFetchError

serials = ["213800234", "214000533", "214701288", "214701292"]   # master first

with chloros_sdk.connect_array(serials) as arr:
    try:
        for frames in arr.frame_stream(processing="raw", fps=10, duration=300,
                                       with_metadata=True):
            if not frames:              # no camera answered this tick
                continue
            if frames.errors:           # cameras missing from this trigger
                print("left out:", {s: str(e) for s, e in frames.errors.items()})
            for serial, (img, hdr) in frames.items():
                # img: numpy array (raw: one Bayer or mono plane)
                # hdr: group_id, frame_id, timestamp_ns, exposure_us, gain_db, ...
                my_pipeline(frames.group_id, serial, img, hdr["timestamp_ns"])
    except FrameFetchError as exc:      # the stream cannot continue
        print("stream ended:", exc.status, exc)
```

- **Üretilen her küme yeni bir tetikleyicidir.** `frames.group_id`, bir akış içinde asla tekrarlanmaz; dizinin çalıştığı hızdan daha fazla `fps` talep ettiğinizde, akış bir sonraki tetikleyiciyi bekler, böylece ikisinden daha yavaş olan hızda çalışır.
- **Eksik kameralar isimlendirilir, boşluk doldurulmaz.** O tetikleyicide sunulamayan bir kamera, sözlükten çıkarılır ve `frames.errors`&#x27;te listelenir.
- **Gürültülü bir şekilde sona erer.** Reddedilme durumunda, bir sonraki tikte, 60 saniye boyunca kare gelmemesi durumunda veya 60 saniye boyunca yeni bir tetikleyici olmaması durumunda `FrameFetchError` tekrarlanır.**Ne sağlar?**`frame_stream()`, numpy çerçevelerinin tamamını yerel HTTP bağlantısı üzerinden aktarır ve**170 MB/s**civarında bir hıza ulaşır. Bu, ham karelerin, birleştirilmiş veya ROI dizisinin canlı işlenmesi için yeterlidir, ancak tam çözünürlükte her tetikleme için yeterli değildir —**her** tetiklemeyi saklamak için, [`continuous()`](#continuoushandle) kullanın. Sıkıştırılmış aktarım ile dört kameralı bir dizide (FOCN, FRGN, FRGB, F450) ölçülmüştür; her tik, tam bir tetik setiydi:

| | Tam sensör (dizi 14,2–14,4 fps) | 2 × 2 birleştirme (dizi ~33 fps) |
| --- | --- | --- |
| `frame_stream(processing="raw")` | 6,7 set/s | 22,2 set/s |
| `frame_stream(processing="debayered")` | 2,3 set/sn | 9,3 set/sn |
| `frame_stream(processing="display")` | 0,8 set/sn | 3,0 set/sn |
| Kamera başına MJPEG, 720p&#x27;de 4 akış | Her biri 13,5 fps; her kare tetikleyiciyle eşleştirilir | Her biri ~25 fps; tetikleyicilerin ~%45&#x27;i 4&#x27;ünün hepsinde mevcut |

Kendi tarafınızda veri aktarım hızı ve demosaik için `processing="raw"`&#x27;i kullanın; `debayered` seviyesi ayrıca kalibrasyon paketinin karanlık ofsetini de kaldırır, ancak `raw` demosaic işlemi bunu yapmaz.

#### Kamera başına MJPEG akışları (RTSP yok)

**RTSP desteklenmemektedir.** Akış arayüzü, HTTP üzerinden MJPEG&#x27;dir (`multipart/x-mixed-replace`, her parça için bir JPEG, her parçada bir `Content-Length`), kamera başına bir URL:

| URL | Ne sunar |
| --- | --- |
| `GET /api/camera/stream/<serial>` | 8 bit önizleme, ~30 fps&#x27;ye kadar. Bir dizi üyesi için, `max_h` (varsayılan 720, 64–2160) ve `jpeg_q` (varsayılan 80) boyut ve kaliteyi belirler. |
| `GET /api/camera/<serial>/stream-annotated` | Üzerine GUI katmanları (zebra, ızgara, artı işareti, histogram, tepe noktası, spot ölçer) çizilmiş aynı önizleme. `ChlorosLocal.camera_stream()` bunu sizin için okur. |
| `GET /api/camera/<serial>/stream-reflectance` | Bant başına 8 bit yansıma — bkz. [Canlı yansıma akışı](#live-reflectance-stream-mjpeg). |

- **Tetikleyici başlıkları.** Bir dizi elemanı için, üç akışın her birinin her parçası `X-Chloros-Group-Id`, `X-Chloros-Frame-Id` ve `X-Chloros-Timestamp-Ns` değerlerini taşır. Aynı `X-Chloros-Group-Id` değerine sahip farklı kameralardan gelen parçalar aynı tetikleyicidir: parçaları toplayın ve bu başlığa göre gruplandırarak bir seti yeniden birleştirin. Bağımsız bir kameranın grubu yoktur: önizleme parçaları bu başlıkların hiçbirini taşımaz (yansıma parçaları ise boş bir `X-Chloros-Group-Id` başlığını taşır).
- **Önizleme akışı, yük altında kareleri atlar.** En yeni kareyi sunar ve zamanında gönderemediği kareleri atlar; bu nedenle yüksek dizi hızlarında her tetikleyici her kameranın akışına ulaşmaz (yukarıdaki tabloya bakın). Her tetikleyici için `frame_stream()` veya `continuous()` kullanın.
- **`/api/camera/stream/<serial>`, dizinin birleştirilmiş canlı akışı açıkken duraklatılır** (`/api/camera/array/stream/<array_id>` — GUI&#x27;nin dizi görünümü bunu açar). Ardından her üyenin akışı, `X-Chloros-*` başlıkları içermeyen bir adet &quot;Duraklatıldı (dizi akışı etkin)&quot; yer tutucu karesi gönderir ve birleştirilmiş akış kapanana kadar başka hiçbir şey göndermez; ardından kendiliğinden devam eder. `stream-annotated` ve `stream-reflectance` duraklamaz. Bir komut dosyası kamera başına önizleme akışlarını okuduğunda GUI&#x27;nin dizi görünümünü kapatın.
- **Resimler yerine sayılar için `frame_stream()`&#x27;i kullanın.**JPEG, kayıplı ve 8 bitlik bir format; `frame_stream()`, yolunda kodek olmadan aynı sonuçları döndürür.

### Drone Ölçümleri için Aralıklı Yakalama

Bir ölçüm uçuşu, dizinin üretebileceği her kareyi değil, her birkaç metrede bir senkronize edilmiş bir görüntü grubuna ihtiyaç duyar. Bunu iki ayar kontrol eder:**`target_fps`**, kameraların ne sıklıkta kare çekeceğini belirler;**`capture_repeated(interval_s=…)`** ise bir karenin ne sıklıkla kaydedileceğini belirler.**Kameralar, kaydetme işlemleri arasında akışa devam eder.** Her aralıkta en yeni senkronize grup kaydedilir; aradaki kareler kaydedilmez. Otomatik pozlama her karede ayarlanır, bu nedenle kaydetme işlemi gerçekleştiğinde pozlama zaten sabitlenmiş olur. Kameraların çekimler arasında durup her çekim için uyandırıldığı bir mod yoktur: aksi takdirde her çekim, akışın başlamasını ve pozlamanın sabitlenmesini beklemek zorunda kalır.**Kare hızını aralığın gerektirdiği değere sabitleyin.**Otomatik olarak bırakıldığında, dizi ana bilgisayar ve ağın desteklediği en yüksek hızda çalışır. Hızı daha düşük bir değere sabitlemek, kaydedilen içeriği değiştirmeden kameranın ısınmasını, ağ yükünü ve ana bilgisayarın CPU yükünü azaltır.**Her aralıkta 2–4 kare** sağlayacak bir hız seçin:

- Her kaydetme işlemi, talep edildikten sonra bir sonraki kareyi bekler; bu nedenle kaydedilen grup, planlanan andan en fazla bir kare süresi sonra elde edilir. Daha yüksek bir hız, bu aralığı küçük tutar.
- Otomatik pozlama her kare başına bir kez ayarlanır; bu nedenle çok düşük bir hız, geçen bir bulutu takip etme hızını yavaşlatır.

**Aralığı uçuş planından hesaplayın:**

```
spacing    = along-track footprint × (1 − forward overlap)
interval_s = spacing / ground speed
```

Örneğin, %80 ileri örtüşme oranında 60 m’lik bir kapsama alanı, 12 m’lik bir aralık anlamına gelir; 10 m/s hızında bu değer `interval_s=1.2` olurken, ve sabitlenmiş bir `target_fps=3` değeri, aralık başına 3 ile 4 kare arasında bir sonuç verir. Çekim zamana dayalıdır: SDK&#x27;da mesafeye veya GPS&#x27;e bağlı tetikleme modu bulunmadığından, ölçüm hatlarında sabit bir yer hızı koruyun.

**Ham verileri hava aracında kaydedin ve daha sonra kalibre edin.** `interval_s`, her kaydetme işleminin başlangıcından itibaren ölçülür; bu nedenle, aralıktan daha uzun süren bir kaydetme işlemi, bir sonraki işlemin geç başlamasına ve aralıkların kaymasına neden olur. Ham veriyi ışık sensörü okumasıyla birlikte kaydetmek (`processing="raw"`; okuma varsayılan olarak kaydedilir), her kaydetme işlemini kısa tutar ve her karenin daha sonra yansıma değerine dönüştürülebilir olmasını sağlar.

```python
import chloros_sdk

INTERVAL_S = 1.2      # from the flight plan
FLIGHT_S = 20 * 60    # bounds the loop; it also stops at count= if given

with chloros_sdk.connect_array(serials, target_fps=3.0) as arr:
    groups = arr.capture_repeated(
        "survey/", interval_s=INTERVAL_S, duration_s=FLIGHT_S,
        processing="raw", render_index=False,
        on_capture=lambda i, r: print(f"group {i}: {len(r)} frames"))
```

- **`connect_array(target_fps=…)`**hızı başlangıçtan itibaren sabitler;**`arr.set_target_fps(fps)`**yeniden bağlanmaya gerek kalmadan uçuş sırasında hızı değiştirir ve**`arr.set_target_fps(None)`** hızı otomatik moda geri döndürür. Ana bilgisayar ve ağın taşıyabileceğinin üzerindeki hızlar bu üst sınıra düşürülür — sonuçta `clamped` değerini kontrol edin — ve en düşük hız 0,1 fps&#x27;dir.
- **Kareleri azaltmak için `burst()`&#x27;i kullanmayın.** Bu komut, dizinin ürettiği her kareyi yazar; sabitlenmiş bir `target_fps`&#x27;i takip eder, bu nedenle yalnızca her karenin istenmesi durumunda doğru araçtır.
- **`target_fps`, bozuk kareleri düzeltmez.** Bu değeri düşürmek, üretilen kare sayısını azaltır; her bir karenin gönderilme hızını etkilemez; GVSP hatalı kareler için bunun yerine `wire_ceiling_mbps` değerini düşürün (bkz. [Dizi Sağlığı](#array-health--which-subsystem-is-losing-frames)).

```python
r = arr.set_target_fps(5.0)
if r["clamped"]:
    print(f"asked for {r['requested']} fps, running at {r['target_fps']}")
```

### Zaten Bağlı Olan Bir Diziye Bağlanma — `attach_array`

Dizi zaten çalışır durumdaysa (GUI tarafından açılmışsa veya önceki bir SDK oturumu `connect_array`&#x27;i çağırmışsa), yeniden bağlanmak yerine `attach_array`&#x27;i kullanarak diziye bir tanıtıcı alın. Bu durumda `connect_array` her zaman &quot;Kamera  <sn>zaten dizide var<id>&quot; hatasını verir, çünkü havuzdaki bir üye için `/array/connect` komutunu POST etmek idempotent değildir; `attach_array`, `/api/camera/array/list`&#x27;i okur ve array_id veya serials değerlerine göre eşleştirir.

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

Örnek: Masaüstü GUI ile ortak kiracı olan SDK komut dosyaları, önce `attach_array`&#x27;i denemeli ve havuzda henüz bir dizi yoksa `connect_array`&#x27;e geri dönmelidir.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Eklenmiş bir dizi ödünç alınmıştır, sahip olunmamaktadır.** `attach_array`&#x27;ten alınan bir tanıtıcı, diziyi asla sonlandırmaz: ne `with` bloğunun çıkışı ne de açık bir `disconnect()` çağrısı onu serbest bırakır (açık çağrı, stderr&#x27;a dizinin çalışmaya devam ettiğini belirten bir satır yazdırır dizinin çalışır durumda kaldığını belirten bir satır yazdırır). `CameraSession` / `DAQSensorSession` ile aynı kural geçerlidir. Dolayısıyla, `with` olsun ya da olmasın, GUI ile ortak kullanım güvenlidir:
>
> ```python
> with chloros_sdk.attach_array(serials) as arr:
>     arr.capture("output/", processing="reflectance")
> # … the array stays up for the GUI
> ```
>
> Gerçekten sonlandırmak istediğinizde — örneğin farklı bir `wire_ceiling_mbps` ile yeniden bağlanmak için — `arr.force_disconnect()`&#x27;i çağırın. Bağlı bir tanıtıcı üzerinde basit bir `disconnect()`&#x27;ten sonra yeniden bağlanmaya çalışmak, hiçbir şey serbest bırakılmadığı için &quot;Kamera <sn>zaten dizide yer </sn>alıyor<sn><id>&quot; hatasıyla sonuçlanır, çünkü hiçbir şey serbest bırakılmamıştır.

### Ağ Analizi Yardımcısı

Diziyi açmadan önce kullanışlıdır — önerdiğiniz ayarların uygun olup olmayacağını öngörür:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    # width / height are sensor pixels BEFORE binning (what connect_array
    # takes with binning=); out_width / out_height is what is streamed.
    print(f"Connect with width={r['width']} height={r['height']} "
          f"binning={r['binning']} ({r['out_width']}x{r['out_height']} streamed)")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

```python
analyze_array_network(
    master_serial,
    slave_serials=None,
    width=2048, height=1536,              # sensor pixels before binning
    pixel_format=None,                    # None = what connect applies (Mono12 / BayerRG12)
    binning=1,                            # 1 | 2 | 4
    force_tier=None,
    packed="auto",                        # "auto" | "on" | "off"
    backend_url="http://127.0.0.1:5000",
    timeout=30.0,
) -> dict
```

Asla bir arka uç başlatmaz: yanıt vermeyen bir arka uç `ChlorosConnectError` hatasını verirken, reddedilen bir istek sonuç olarak geri döner. Hatalı bir `packed`, `ValueError` hatasını verir.

`status`, `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (aksi takdirde `error` — reddedilen bir istek dahil: oturum açılmamış, `error_code: AUTH_REQUIRED` veya SDK alt sınırının altındaki bir plan, `PLAN_UPGRADE_REQUIRED`). `auto_capped_fps`, istenen çözünürlüğün yalnızca sınırlı bir tetikleme hızında RX halkasına uyduğu anlamına gelir — çözünürlüğü koruyun ve `target_fps=result["recommended"]["recommended_target_fps"]`&#x27;ten `connect_array`&#x27;e geçin (bkz. [Örnek 6](#6-bağlanmadan-önce-yetenek-tespiti-a-4-cam-array)). `auto_shrunk`, istenen kare herhangi bir kare hızında iletilemediğinde ortaya çıkar; connect, kareleri kendi başına asla küçültmez, bu nedenle `recommended`&#x27;i kendiniz uygulayın.

**Projeksiyon nasıl okunur** (GUI Dizi Ayarları paneli ile aynı model):

- **Burst (`frame_bytes_total`), her kameranın gerçek piksel formatında kamera başına toplanır.**Mono**M3M**kameralar Mono12 (2 B/piksel) olarak yayın yapar veya bir `pixel_format` ile derinliklerinin Mono formatında yayın yapar (`"BayerRG8"`, `Mono8`&#x27;tir, 1 B/px) olarak yayınlar; dolayısıyla bu özellik olmadan 4 kameralı tam çözünürlüklü bir kare, 8 bitlik varsayımın verdiği ~12,6 MB değil, üç mono kamera ile**~25 MB** olur. Arka uç, her kameranın formatını modelinden belirler.
- **Admittance (`burst_fits_nic_ring`) boşaltma hızını dikkate alır**, tüm patlama-halka karşılaştırması değil: sim-emit, ana bilgisayarın RX halkasını kameraların doldurma hızından daha hızlı boşalttığı durumlarda devreye girer. 10G ana bilgisayar + 1 GbE kameralar, patlama halkayı aşsa bile tam çözünürlüğü**kabul eder**; 1 GbE ana bilgisayar ise engeller (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max`, seri veri alımı için muhafazakar bir üst sınırdır** — `max(readout+emit, N×emit)`&#x27;te kamera başına veri gönderimi, pozlamadan bağımsız olarak 1 GbE kamera bağlantısına sınırlandırılmıştır. Örneğin, 4 kameralı tam çözünürlüklü 12 bit dizide ~2,8 fps (çalışma zamanında ölçülen ~2,7–3,0 değerleriyle uyumludur). Tam model: [CLI Referansı → Dizi fps ve patlama modeli](cli-reference.md#array-fps--burst-model).
- **Sürekli bir yakalama işleminden sonra, projeksiyon ne kadar veri tuttuğunu gösterir.** Otomatik hızda 30 saniye veya daha uzun süren bir yakalama, bu bilgisayarda o dizi boyutunda sabitlenen hızı kaydeder; hız denetleyicisinin daha yüksek bir hızda yaptığı kısa denemeler dikkate alınmaz. O andan itibaren `array-connect`&#x27;satırından itibaren, `connect_array()`&#x27;in analizi ve Dizi Ayarları paneli, bu hız modelinkinden düşük olduğunda söz konusu hızı belirtir; `cycle_limiter`&#x27;te bunu sınırlayan unsur şudur: `"clock"` (dizi kararlaştırılan hızda çalıştı, yakalama döngüsü tetik saatini bekledi), `"loop"` (yakalama döngüsünün zamanı kalmamıştı), `"capture"` (yakalama geride kaldı ve grupları atladı) veya `"measured"` (hangisi olduğu belirtilmemiştir). Modelin değeri `wire_fps_max`&#x27;te yer almaktadır. Dizi hala kablonun taşıyabildiği değere kadar yükseliyor.
- **Aşırı abonelik (`oversubscribed: true`), kablonun kameraların ulaştığı herhangi bir hızda diziyi taşıyamayacağı anlamına gelir** — fps alanları (`achievable_fps_max` / `fps_bright` / `fps_dark`) 0 değerini gösterir ve binning bu sorunu çözemez (bu, saniye başına hızlandırılmış bayt sayısını değil, kare başına bayt sayısını düşürür). Yalnızca yavaş bir kablo bu sorunu yaratmaz: bunun yerine kare hızı düşer ve kameraların hızı yeterince yavaşlatılamazsa seviye `slip-emit-and-capture` (seri) olur. Çözümler arasında kamera sayısını azaltmak, jumbo çerçeveler kullanmak veya daha hızlı bir ağ kartı (NIC) kullanmak yer alır; `max_cams_collision_safe`, kablo ve kameralarınız için üst sınırı bildirir. Yanıtta ayrıca `aggregate_demand_bps`, `collision_safe_ceiling_bps` ve `per_cam_floor_bps` kodları da yer alır. Bkz. [Aşırı Abonelik](#over-subscription-the-per-cam-floor).

### Keşif ve Listeleme

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

Her biri yalnızca anahtar kelime içeren `backend_url=` ve `timeout=30.0` komutlarını alır ve bir sözlük listesi döndürür. `discover_lattice_cameras()` (`serial`, `ip`, `mac`, `model`, `lattice_model`, `vendor`, `connected`, `array_id`, …) ayrıca `auto_start_backend=True`&#x27;i de alır; `list_*` çağrıları hiçbir zaman bir arka uç başlatmaz.

---

## Akıllı AE / Akıllı Yakalama

LATTICE dizileri, bağlanır bağlanmaz arka planda sürekli AE çalıştırır, ancak yeni hedeflenen bir sahnenin yakınsaması biraz zaman alır. **Smart-capture**, bu işlevi kolaylaştıran bir özelliktir: her kameranın pozlamasını sorgular, dizi bir pencere boyunca kararlı hale gelene kadar bekler, ardından yakalamayı tetikler. Bu, GUI ile eşdeğerdir: masaüstü uygulamasındaki &quot;smart&quot; yakalama düğmesi, aynı arka uç uç noktasını çağırır.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

`ChlorosProject` (sonraki bölüm) üzerinden çalıştırdığınızda daha fazla ayar seçeneği elde edersiniz:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Akıllı AE politikası varsayılan olarak muhafazakârdır. Hassas radyometrik çalışmalar için `exposure_tolerance_pct` değerini daraltın; sadece &quot;yeterince yakın&quot; sonuç istediğiniz, hızla değişen sahneler için genişletin.

---

## DAQ Sensör Oturumları

Spektral sensörler için kalıcı arka uç havuzu (USB üzerinden DAQ-U, BLE üzerinden DAQ-M, Ethernet üzerinden DAQ-E). Kamera yüzeyini yansıtır: akıllı algılama, havuzun yeniden kullanımı, idempotent bağlanma.

### Akıllı Algılama (Sıfır Yapılandırma)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

Öncelik: Ethernet → BLE → USB. Aktarım yöntemini sabitlemek için herhangi bir açık ipucu verin.

### Sabitlenmiş Aktarım

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# A specific sensor by id (as list_daq_sensors() shows it; ':' or '-' alike)
daq = chloros_sdk.connect_daq_sensor(sensor_id="CB-7C-A7-5A-73")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

`sensor_id=` bir sensörü adlandırır ve yalnızca o sensör açılır. Zaten açık olan sensörlerle eşleştirilir; ardından bir DAQ-E&#x27;nin ana bilgisayar adı veya IP adresi (ağ taraması tarafından yalnızca IP adresiyle görülen bir DAQ-E&#x27;den adı, MAC adresi ve seri numarası okunur-sadece, böylece bunlardan herhangi biri de onu bulabilir), bir DAQ-M&#x27;nin BLE MAC adresi ve bir DAQ-U veya DAQ-M&#x27;nin açıldığında bildirdiği kimlik (kontrol için açılan diğer herhangi biri tekrar kapatılır) ile eşleştirilir. Bulunamazsa, `connect_daq_sensor`, bulunanları listeleyen bir `ChlorosConnectError` sinyali tetikler; buna bir adres de eklenir (farklı bir sensörü barındıran `port=` / `mac=` / `eth_host=`) farklı bir sensörü barındırıyorsa, o da bir hata mesajı oluşturur. Asla görünür olan sensöre geri dönmez — sıfır bayrağı çağrısı işte bunun içindir.</id></sn></id></sn>

### `connect_daq_sensor()` İmza

```python
connect_daq_sensor(
    *,                                    # every argument is keyword-only
    sensor_id=None,                       # one named sensor (see above)
    transport=None,                       # "usb" | "ble" | "eth"; None = smart-detect
    port=None,                            # DAQ-U serial port ("COM3", "/dev/ttyUSB0")
    mac=None,                             # DAQ-M BLE MAC (implies transport="ble")
    eth_host=None,                        # DAQ-E hostname or IP (implies transport="eth")
    integration_time=None,                # ms; None = the sensor's own
    frame_avg=None,
    enable_ae=None,
    cap_id=None,                          # cap correction: "sunshine_cosine", "fov_30", ..., "none" (bare)
    start_streaming=True,
    backend_url="http://127.0.0.1:5000",
    timeout=60.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> DAQSensorSession
```

Havuzda zaten bulunan bir sensör olduğu gibi iade edilir (ödünç alınmış): `integration_time`, `frame_avg` ve `enable_ae` bu sensöre uygulanmaz. Ayarlarınızla açmak için önce bağlantısını kesin. `cap_id` bir istisnadır: ödünç alınmış bir sensörde bu, `set_cap()` ile ayarlanır. Belirtilmezse, arka ucun varsayılan ayarı geçerlidir (yöntemler tablosunun altındaki nota bakın).

### `DAQSensorSession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `status(timeout=10.0)` | Havuz girişi özeti (akış/kayıt durumu, dalga boyu aralığı, kalibrasyon sha, entegrasyon süresi, frame_avg, AE durumu). Akışı kesilen bir DAQ-E için: `reconnect` (arka ucun otomatik yeniden bağlanma durumuna göre: `state`, `attempts`, `next_attempt_in_s`, `last_error`; akış devam ederken `None` akış devam ederken), `stream_lost_s`, `reconnects` ve `last_reconnect`, ve devam eden devam eden kayıt için; `rx_stream_fault`, ilk spektrum gelene kadar yeniden bağlanma durumunu belirtir. |
| `latest(*, n=1, timeout=10.0)` | En son N spektrum çerçevesini döndür (her biri `ts_ns`, `spectrum`, `is_saturated`, `integration_time`, `x`, `y`, `z`). |
| `stream_start()` / `stream_stop()` | Akışı devam ettir / duraklat (işlem kimliği açık kalır). |
| `record_start(*, output_dir=None, device_name=None, timeout=15.0)` | Bir .daq dosyasının kaydını başlatır. Dosya yolunu döndürür. AWS kalibrasyon paketi olmayan DAQ-U/M cihazlarında reddedilir (DAQ-E hariç). |
| `record_stop()` | Kaydı durdurur. `{path, rows, warning, gaps}` değerini döndürür; `warning`, hatasız bir kayıt için `None` değerini alır, aksi takdirde arka ucun tanılama bilgisi (örneğin boş bir dosya veya akışta bir boşluk) döndürülür; bu bilgi aynı zamanda stderr&#x27;a da yazdırılır. `gaps`, akışın hiçbir şey iletmediği her bir aralığı listeler: `start_utc`, `end_utc` (`None`: durdurma sırasında hala kayıp), `duration_s`, `reconnected`, `marked_in_file`. Sürekli kayıt durumunda bu satır boştur. |
| `set_cap(cap_id, timeout=15.0)` | Bir sonraki okumadan itibaren uygulanacak üst sınır düzeltme profilini ayarlayın: `"sunshine_cosine"`, `"fov_15"` ... `"fov_90"` veya çıplak sensör için `"none"` (veya `None`). Sensörünözetini döndürür; bu özet, `cap_id` tarafından geri okunur. Bir DAQ-E-S, ne istenirse istensin `as_recorded` değerini korur (difüzörü dahili olarak bulunur). Yükleme yapılmayan bir profil için `ChlorosConnectError` değerini yükseltir CLI komutu: `chloros-cli daq pool-set-cap`. |
| `enable_ptp(timeout=15.0)` | Yalnızca DAQ-E: PTP&#x27;si kapalı olan bir ünite için PTP&#x27;yi tekrar açar (`status()["rx_ptp_enabled"]`, `False`&#x27;tir; ayar üniteye kaydedilir, bu nedenle tekrar açılana kadar kapalı kalır). Güncellenen durumu döndürür. DAQ-U / DAQ-M için veya ünite onay vermediğinde `ChlorosConnectError` hatasını tetikler. Chloros, kapatma işleminin kasıtlı olabileceği için bir üniteyi asla kendi başına yeniden açmaz. CLI&#x27;si: `chloros-cli daq pool-enable-ptp`. |
| `device_info(timeout=15.0)` | Yalnızca DAQ-E: ünitenin kendi ayarları. `firmware` (`running`, `bundled`, `update_available`), `ip` ve `ip_mode` (`dhcp` / `linklocal` / `static`: şu anda çalışan ayar), `network` (kaydedilen ayar: `mode`, ayrıca `ip` / `mask` / `gateway` statik durumdayken), `network_change_pending` (ünite, `set_network(..., reboot=False)`&#x27;i de içeren kaydedilmiş ayarı çalıştırana kadar geçerlidir), `auto_stream`, `ptp`. `firmware["update_available"]`, üniteninsürümü okunamadığında `firmware["update_available"]` olur. Ünitenin akışı bu bilgisayara ulaşmıyorken, yeni bir bağlantıda okunur ve sözlük `stream_silent` (`True`), `stream_fault` ve `reconnect` değerlerini ekler; yanıt vermeyen bir ünite ise CLI&#x27;de şu hatayı verir: `pool-info`. |
| `set_network(mode="auto", *, ip=None, mask=None, gateway=None, reboot=True, force=False, comeback_timeout=45.0)` | DAQ-E yalnızca: DHCP (`"auto"`) veya statik adres. Ayarı uygulamak için birimi yeniden başlatır ve yeniden bağlar; bu oturum çalışmaya devam eder ve `address` birimi takip eder. `reboot=False` yalnızca ayarı kaydeder. Bkz. [DAQ-E Cihaz Ayarları](#daq-e-device-settings). CLI komutu: `pool-set-network`. |
| `set_auto_stream(enabled, timeout=15.0)` | Yalnızca DAQ-E: Yazılım bağlı olmadan güç açıldığında veri akışı (cihaza kaydedilir). Onaylanan ayarı döndürür. CLI: `pool-set-auto-stream`. |
| `reboot(*, comeback_timeout=45.0)` | Yalnızca DAQ-E: Üniteyi yeniden başlatır ve önceki ayarlarıyla yeniden bağlar. CLI: `pool-reboot`. |
| `update_firmware(*, comeback_timeout=45.0)` | Yalnızca DAQ-E: Bu Chloros sürümüyle birlikte gelen ürün yazılımını, daha eski bir sürümü çalıştıran bir üniteye yükleyin, ardından yeniden bağlayın (yaklaşık bir dakika sürer; gücü kapatmayın). `updated`, `fw_before`, `fw_after`, `bundled` ve bir `warning` kodu; `updated` kodu, sürümü zaten güncel olan bir ünite için `False` koduna karşılık gelir. Sürümü okunamayan bir birim için `ChlorosConnectError` hatası verilir. CLI&#x27;si: `pool-update-firmware`. |
| `disconnect()` | Havuzdan serbest bırakma.-op. Bu oturumun açtığı bir sensörde bu komut `force_disconnect()`&#x27;tir ve serbest bırakma işlemi onaylanmadığında `ChlorosConnectError` hatası verir (`with` bloğunun çıkışı asla hata vermez). |
| `force_disconnect(timeout=30.0)` | Sensörü kimin açtığına bakılmaksızın serbest bırakın — örneğin, çökmüş bir komut dosyasının havuzda bıraktığı ve yeni bir komut dosyasının yalnızca `disconnect()`&#x27;in hiçbir işlem yapmadığı ekli bir tanıtıcı olarak erişebildiği bir sensör. Başka bir istemcinin sensörünü sonlandırmak, sensörün yaptığı tüm kayıtları bitirir. Arka uç serbest bırakmayı onayladığında veya serbest bırakılacak bir şey olmadığında (sensör havuzda değilse veya çalışan arka uç yoksa); zaman aşımına uğrayan veya arka uç tarafından reddedilen bir serbest bırakma işlemi `ChlorosConnectError` hatasını verir. CLI: `chloros-cli daq pool-disconnect --sensor-id ID`. |

Özellikler: `sensor_id`, `model`, `transport`, `address` (`set_network()` tarafından yeniden adreslenen bir DAQ-E&#x27;yi takip eder), `info` ve `backend_url`.

> **Kapağın adını belirtin.** DAQ-U, DAQ-M ve DAQ-E kapaksız olarak gönderilir ve Chloros, bir kapak takılı olup olmadığını göremez: `cap_id` olmadan bağlanan bir sensör, kayıtlarının yaptığını belirttiği bir varsayım olan `sunshine_cosine` değerini alır (`cap_id_source=auto_default`). Bu düzeltme uygulandığında, kapaksız bir DAQ-U yaklaşık 30 kat yüksek değer okur; bu nedenle, kapaksız bir sensör için `cap_id="none"` değerini (veya `set_cap("none")`) değerini, aksi takdirde taktığınız kapağın profilini kullanın.

### DAQ-E-S — sabit difüzör ve eğim sensörü

**DAQ-E-S**, yazılım açısından önemli iki farkı olan bir DAQ-E modelidir. Diğer her şey — keşif, bağlantı, PTP, kayıt ve aşağıdaki cihaz ayarları — tam olarak bir DAQ-E&#x27;de olduğu gibi çalışır.**Difüzörü sabittir, bu nedenle kapak düzeltmesi gerektirmez.** Sunshine tarzı difüzör yerleşiktir ve fabrika kalibrasyonu ölçüldüğünde ünite üzerinde bulunmaktaydı; bu nedenle kalibrasyon, aşağı doğru ışınım şiddetini zaten doğru bir şekilde okumaktadır. Chloros, ne istenirse istensin sınır değerini `as_recorded` (profil yok) olarak ayarlar: bu modele özel bir sınır değeri (`sunshine_cosine`, `fov_*`, `none`) bir uyarı mesajıyla göz ardı edilir ve kayıtlarında `cap_id_source=model` değeri saklanır; bu nedenle, varsayılan sınır uyarısı bu cihaz için asla tetiklenmez. Verilerine elle de bir sınır uygulamayın: En üstte `sunshine_cosine` bulunursa, değer yaklaşık 11 kat yüksek okunur.

**3 eksenli bir ivmeölçer barındırır.** Güneş ışığı difüzörünün kosinüs tepkisi yalnızca düz konumdayken geçerlidir; bu nedenle eğik bir sensör, başka hiçbir belirti olmaksızın yanlış bir ışınım değeri bildirir — ve yansıma da bu hatayı devralır. Dolayısıyla her DAQ-E-S okuması, cihazın konum bilgisini de içerir:

| Alan | Anlam |
| --- | --- |
| `tilt_deg`, `roll_deg`, `pitch_deg` | Derece cinsinden açılar. `None`, donanım yazılımı açıyı tanımlanmamış olarak işaretlediğinde (serbest düşüş, çalışmayan eksen). |
| `x_mg`, `y_mg`, `z_mg`, `mag_mg` | Milig cinsinden ivme. `mag_mg` değeri ~1000&#x27;den uzaksa, bu, ünitenin hareket halinde olduğu ve açıların o örnek için anlamlı olmadığı anlamına gelir. |
| `cal_applied` | Ayarlı: eksenler ve açılar difüzörün koordinat sistemindedir (cihazın montaj kalibrasyonu uygulanmıştır). Ayarsız: ham kart koordinat sistemi; bu durumda ~2,3°&#x27;ye kadar gerçek olmayan bir eğim gösterilebilir. |
| `sample_age_ms` | Spektrum gönderildiğinde ivmeölçer örneğinin yaşı. |
| `temp_raw` | Kalibre edilmemiş yonga sıcaklığı **eğilimi** — yalnızca farklar, asla termometre okuma değeri değildir. |

`DAQSensorSession.latest()`, bunları her çerçevenin `imu` anahtarı altında döndürür (DAQ-U, DAQ-M veya standart DAQ-E&#x27;de `None`). Bir DAQ-E-S&#x27;den kaydedilen bir `.daq`, aynı değerleri `imu_*` sütunlarında (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, `imu_mag_mg`, `imu_cal_applied`, …) sütunlarında aynı değerleri depolar; böylece okuyucu, bir bulut ile devrilmiş bir tripodu birbirinden ayırt edebilir. Diğer tüm modellerde bu sütunlar boştur.

Konum akışı, kendi alanlarını `status()` (`rx_imu_stream_fault`, `rx_imu_stream_faults`, `rx_last_imu_stream_fault`) alanlarında kendi verilerini raporlar; bu alanlar spektral verilerden ayrı tutulur, böylece bir ivmeölçer arızası asla kayıtta bir boşluk olarak algılanmaz. `discover_daq_sensors()` ve oturumun `model` dosyaları, `DAQ-E-S` değerini gösterir.

### DAQ-E Aygıt Ayarları

```python
with chloros_sdk.connect_daq_sensor(eth_host="daq-e-def330.local") as daq:
    info = daq.device_info()
    print(info["ip"], info["ip_mode"], info["firmware"])

    # A fixed address on the network this computer is on
    daq.set_network("static", ip="192.168.1.50", gateway="192.168.1.1")
    print(daq.address)              # 192.168.1.50: the session follows it

    if info["firmware"]["update_available"]:
        daq.update_firmware()
```

- **Bu bilgisayarın ağında statik bir adres bulunmalıdır.** `set_network`, alt ağında bu bilgisayarın: yeniden başlatmanın ardından sensöre ulaşarak ayarı geri döndürebilecek hiçbir şey kalmaz. `force=True` kodunu yalnızca bu bilgisayarın yeni alt ağa yeniden adreslendirileceği veya yönlendirileceği durumlarda geçirin.
- **`set_network`, `reboot` ve `update_firmware`, sensörü yeniden başlatır** ve sensör kayıt yaparken `ChlorosConnectError` hatasını tetikler, ve `comeback_timeout` saniye içinde geri dönmezse. Sensör kimliği ve `daq-e-<id>.local` ana bilgisayar adı asla değişmez.
- **Yeniden başlatmadan hemen önce başlayan bir kayıt, yeniden başlatma işlemi tarafından kapatılır ve bir ad verilir.** Sensör geri döndüğünde, döndürülen sözlük `recording_closed` (dosyanın yolu) ve bunu belirten bir `warning` içerir. Geri dönmezse veya ürün yazılımı aktarımı başarısız olursa, bunun yerine `ChlorosConnectError` bunu belirtir: mesajı uyarı ile biter ve `e.recording_closed` / `e.warning` ayarlanır (kayıt kapatılmamışsa her ikisi de `None` olur). Sensör geri geldiğinde kaydı yeniden başlatın.
- **Yalnızca sensörün kendisi yeniden bağlanır.** Yeniden başlatmanın ardından ünite, ağ adaptöründen -- ve `set_network("auto")`&#x27;ten sonra ayrıca `.local` adıyla da -- sahip olduğu ağ bağdaştırıcısından (MAC) adını, ana bilgisayar adını ve modelini bildirmesi gerekir; `address`, yanıt veren IP adresi olur. Bir ad altında verilen yanıt yalnızca o MAC adresiyle geçerlidir; bu nedenle, bir yönlendirici aracılığıyla sensöre ulaşan bir bilgisayardan veya WSL2&#x27;nin varsayılan NAT ağından, DHCP tarafından taşınan bir sensör yeniden bağlanmaz (`ChlorosConnectError`; yeni adresinden yeniden bağlayın).
- **Bir ürün yazılımı güncellemesi için sensörün yeniden bağlanması gerekir.** `update_firmware()`, üniteye davet gönderir ve ünite, işletim sisteminin seçtiği bir bağlantı noktası üzerinden görüntü için bu bilgisayara geri bir TCP bağlantısı açar. Arka uç, üniteye ulaşan adaptörde dinleme yapar (hangisi olduğunu belirleyemediği durumlarda her adaptörde) ve yalnızca o üniteye hizmet verir: bekleme devam ederken diğer tüm bağlantılar reddedilir (arka ucun çıktısında `[DAQ-FW] … refused a firmware connection from <ip>`). Bağlantıyı engelleyen bir güvenlik duvarı, `device never connected back on TCP <port>` hatasıyla güncellemeyi başarısız kılar. Windows yükleyicisi bunun için bir kural ekler (bkz. [Adım 1](#windows-exe)); başka bir güvenlik duvarında, sensörün alt ağından arka uca gelen TCP trafiğine izin verin.
- **WSL2 altında, yansıtmalı ağ bağlantısı kullanın** (`networkingMode=mirrored` ve `.wslconfig`): varsayılan NAT modunda her statik adres reddedilir (WSL&#x27;nin kendi adresi sensörün ağında değildir), veri akışı WSL&#x27;ye ulaşmaz ve ürün yazılımı güncellemesi tamamlanamaz.
- GUI, sensörle kendi bağlantısını sürdürür: bir komut dosyası aracılığıyla yeniden başlatma, GUI&#x27;nin sensörle olan oturumunu sonlandırır. GUI&#x27;de aynı ayarlar bulunur (Işık Sensörleri sekmesi, sensörün ayarları, Ağ).

### Keşif — bağlanılacak bir adres bulma

`discover_daq_sensors()`, *açılabilecek* sensörleri bulmak için USB / BLE / ETH&#x27;yi tarar. Bu, `discover_lattice_cameras()`&#x27;in DAQ karşılığıdır ve **DAQ-M&#x27;nin BLE MAC adresini** elde etmenin tek yoludur — bir DAQ-E’nin bir ana bilgisayar adı, bir DAQ-U’nun ise bir COM bağlantı noktası vardır, ancak MAC adresi ne cihaz üzerinde basılıdır ne de işletim sistemi tarafından listelenir.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               DAQ-U  {'vid': '0403', 'pid': '6001', 'manufacturer': 'FTDI'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| Alan | Açıklama |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM bağlantı noktası / BLE MAC / ana bilgisayar adı — `connect_daq_sensor`&#x27;e `port=` olarak aktarılır / `mac=` / `eth_host=`. |
| `display` | İnsan tarafından okunabilir etiket. |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` \| `DAQ-E-S`, veya `None`. Bir Ethernet sensörü, sorgulandığında kendisini `DAQ-E-S` veya `DAQ-E` olarak bildirir; tarama sırasında salt okunurdur (zamanında yanıt vermediğinde `DAQ-E`). Bir USB seri bağlantı noktası, köprü yongası DAQ-U&#x27;nun kullandığı yongalardan biri olduğunda (FTDI, CH340, CP210x, Prolific) `DAQ-U` olarak okunur — bağlantı noktasının açıklamasından anlaşılacağı üzere, prob değil — ve diğer tüm USB aygıtları için `None` değerini okur; bu aygıtlar yine de listede yer alır, bu yüzden bunları `port=` olarak iletebilirsiniz. USB satıcı kimliği ve bu tür bir köprü yongası olmayan bir seri bağlantı noktası (Linux&#x27;teki `/dev/ttyS*` gibi bir anakart UART&#x27;ı, bir Bluetooth seri bağlantısı, Intel AMT) DAQ-U olamaz ve listede yer almaz. |
| `extra` | Aktarım başına ayrıntılar (BLE ilan adı; USB `vid`, `pid`, `manufacturer`, `serial_number`; DAQ-E ip/fw/…). Boş değerler atlanır. |

| Parametre | Varsayılan | Açıklama |
| --- | --- | --- |
| `transports` | üçü de | Taramayı sınırlayan dizi (veya csv dizesi). Ne istediğinizi biliyorsanız bu değeri belirtmeniz faydalıdır — BLE en yavaş bileşendir. |
| `scan_timeout` | 5 | Saniye cinsinden aktarım başına tarama aralığı; arka uç bunu 1–20 aralığına sınırlar. |
| `timeout` | 60,0 | Tüm çağrı için HTTP üst sınırı (SDK&#x27;nin diğer yerlerinde olduğu gibi). |
| `auto_start_backend` | `True` | Çalışan bir arka uç yoksa yerel bir arka uç başlatır. Uzak bir `backend_url` için asla başlatılmaz. |

> **Havuzda zaten açık olan sensörler görünmez.** Bağlı bir BLE çevre birimi reklam yayınlamayı durdurur ve açık bir COM bağlantı noktası taranamaz; bu nedenle keşif, *bağlanmaya hazır* olanları listeler. Bir cihaza bağlandıktan hemen sonra sonucun boş olması normaldir — halihazırda elinizde olan cihazlar için `list_daq_sensors()` komutunu kullanın. Taraması yapılamayan aktarım protokolleri (bleak / zeroconf yüklü değilse) hata vermeden atlanır; böylece Bluetooth&#x27;u olmayan bir makine de USB ve ETH yanıtlarını alır.

### Liste

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### Hangi DAQ Hangi Kameraya Besleme Yapıyor — `daq_bindings()`

Bir yakalamanın yansıma değeri ve kaydettiği `.daq`, o kameraya veya diziye bağlı DAQ&#x27;yi kullanır; aksi takdirde arka uçtaki en güncel DAQ kullanılır. GUI&#x27;deki ışık sensörü seçimi ve `chloros-cli daq pool-bind` bunu ayarlar; ayrıca şunlar da ayarları belirler:

```python
chloros_sdk.set_daq_bindings(cameras={"214701281": "COM7"},          # a sensor id or an address
                             arrays={"array-1779862544497": "daq-e-def330"})
chloros_sdk.set_daq_bindings(cameras={"214701281": None})            # None clears it
for serial, b in chloros_sdk.daq_bindings()["cameras"].items():
    print(serial, b["binding"], b["source"], b["resolved"]["in_pool"])
```

`daq_bindings()`, `{"cameras": {serial: b}, "arrays": {array_id: b}}` değerini döndürür: her bir `b`, `binding` (ayarlanan değer), `source` (`project`, projenin `sensors.json`&#x27;inden veya `runtime`&#x27;inden), `known` / `known_as` (Chloros&#x27;un bildiği bir kamera veya dizi anahtar adı olup olmadığı ve nasıl olduğu: `connected`, `project` — açık projenin `cameras.json` — ya da bir kamera için `seen`: daha önce bu bilgisayarda açılmış; hiçbir şeyi adlandırmayan bir anahtar için `known: False`) ve `resolved` (şu anda eşlendiği havuz sensörü: `in_pool`, `sensor_id`, `address`, `model`, `streaming`). Çalışma zamanı bağlaması, oturum için proje; proje dosyası değiştirilmez. Bir kameranın, dizinin veya DAQ&#x27;nın bağlantısı kesildiğinde, o donanımın tüm bağlamaları, kim ayarlamış olursa olsun (bir dizinin elemanları ile birlikte) düşer.

`set_daq_bindings()`, yalnızca bağlı olan, açık projede bulunan veya daha önce bu bilgisayarda açılmış bir kamerayı ve bağlı olan veya açık projede bulunan bir diziyi (ya da zaten bağlanmış bir tuşu) bağlar. Diğer herhangi bir tuş, `ChlorosConnectError` hatasını, (`400`, `code`, `UNKNOWN_BINDING_TARGET` ve `unknown` tuşları ile) bir hata oluşturur ve bu çağrıda **hiçbir şey** bağlanmaz. `None`, tüm anahtarları temizler.

### Ortam + Nesne Yansıtma Oranı — `light_sensor_reflectance()`

Biri gökyüzüne (ortam), diğeri hedefe (nesne) bakan iki birleştirilmiş DAQ, en son okumalarından hedefin yansıma ve bitki örtüsü endekslerini verir: GUI’deki Ortam + Nesne çifti ve `chloros-cli daq reflectance`, sensörleri ikinci kez açmaya gerek kalmadan. Her ikisi de bağlı ve veri akışı yapıyor olmalıdır.

```python
r = chloros_sdk.light_sensor_reflectance("daq-e-def330", "daq-e-54b774")
print(r["reflectance_type"])                       # "Apparent Reflectance" when both are calibrated
for name, band in r["bands"].items():
    print(name, band["rho"] if band["valid"] else band["reason"])
for idx in r["indices"]:
    print(idx["name"], idx["value"])
```

```python
light_sensor_reflectance(
    ambient_sensor_id, object_sensor_id, *,
    bands=None,          # [{"name", "center_nm", "fwhm_nm", "role"}]; role blue / green / red / nir
    preset=None,         # a preset id from daq_band_presets(); default: the suggested one, else "GENERIC"
    expressions=None,    # custom indices: [{"name": "MyNDVI", "expr": "(NIR_850 - Red_660) / (NIR_850 + Red_660)"}]
    builtins=True,       # include the built-in indices
    backend_url="http://127.0.0.1:5000", timeout=30.0,
) -> dict
```

- **Bantlar.** Her bir bandın yansıma değeri, ortamın üzerindeki nesnenin bantüzerinde entegre edilmiş ışık şiddetidir. `daq_band_presets(filter_code=None)`, bant kümelerini listeler: her LATTICE M3C filtresi için bir tane (`RGN`, `OCN`, `NGB`, kameranın kendi bant modeli; dolayısıyla bir DAQ NDVI ile bir kamera NDVI&#x27;si aynı değerdir) ve `GENERIC` geniş bant. `suggested`, `filter_code` ile eşleşen ön ayardır; aksi takdirde ilk bağlı kameranın filtresi (hangisi olduğu `suggested_from` ile belirtilir).
- **Döndürdüğü değer** `bands` (her bant için `rho`, `valid`, `reason`, `e_ambient`, `e_object`), `indices` (yerleşikekler), `custom` (her biri `value` veya `valid: False` ve `error` ile birlikte), `invalid_bands`, `reflectance_type`, `preset` ve iki okumanın `timestamps` (`ts_ns`). Ortam sensörünün ölçemediği bir aralık (ölçüm aralığı dışında veya gürültü tabanında) geçersiz sayılır; bunun nedeni belirtilir ve buna ihtiyaç duyan tüm endeksler hariç tutulur, asla sıfıra göre hesaplanmaz.
- **Görünür veya göreceli.** Ölçeğe bağlı endeksler (SAVI, OSAVI, EVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) mutlak bir orana ihtiyaç duyar; bu nedenle, yalnızca her iki sensörün de güç kalibrasyonu yapılmış olması durumunda hesaplanır (her birinin bir `calibration_bundle_sha`&#x27;i vardır): `"Apparent Reflectance"`. Aksi takdirde sonuç, yalnızca ölçekten bağımsız endeksleri içeren `"Relative Reflectance"` olur.
- **İki farklı dalga boyu ızgarası**, örtüşme alanları açısından karşılaştırılır: ortam sensörünün noktaları nesnenin menzilindedir; nesnenin spektrumu ise bu noktalara enterpolasyon yoluyla yerleştirilir.
- **İki sensör, asla tek sensör değil.** Bir sensörü her ikisi olarak adlandırmak (havuzun kabul ettiği herhangi bir yazımla aynı kimlik), `ChlorosConnectError` hatasını verir: bir sensörün kendi üzerine yaptığı okuma her bantta 1,0&#x27;dır ve bu da hiçbir şey ölçmez. Arka uç da bunu reddeder (`POST /api/daq/indices`, `400`&#x27;e yanıt verir, `code` `SAME_SENSOR`; oysa SDK&#x27;nın gönderdiği `ambient_sensor_id` ve `object_sensor_id`, tek bir havuzlanmış sensördür, örneğin kimliği ve bağlantı noktası).

### CLI ve Diğer Komut Dosyalarıyla Ortak Kullanım

Arka ucun DAQ sensör havuzu, makinedeki her CLI ve SDK istemcisi tarafından paylaşılır. Bir sensör halihazırda havuzda bulunuyorsa — `chloros-cli daq pool-connect` veya başka bir komut dosyası tarafından açılmışsa — Python&#x27;dan `connect_daq_sensor(port="COM3")` çağrıldığında ödünç alınmış bir tanıtıcı döndürülür: `disconnect()` işlevi hiçbir işlem yapmaz; böylece komut dosyanız, kapsamdan çıkıldığında sensörü onu açan istemciden koparmaz. Aynı durum, `sensor_id=` için de geçerlidir (`:` veya `-` ile yazılır; ikisi de eşleşir); bu, bir DAQ-E için, açıldığında IP adresiyle adlandırılmışsa (veya tam tersi) ve arka uç zaten elinde tuttuğu bir sensörle yanıt verdiğinde sıfır bayraklı akıllı algılama için de geçerlidir — bir `with` bloğu yalnızca kendi açtığı sensörü serbest bırakır.

Chloros GUI&#x27;si bu havuzun bir parçası **değildir**: DAQ paneli sensöre kendi bağlantısını kurar, bu nedenle `connect_daq_sensor`, GUI&#x27;nin açık tuttuğu bir sensörü asla ödünç almaz. Aynı sensörü hem GUI’den hem de bir komut dosyasından aynı anda kullanmayın. GUI’nin tuttuğu bir DAQ-U veya DAQ-M normalde açılmayacaktır, ancak bir DAQ-E ikinci bir oturumu kabul eder: komut dosyasının connect komutu, GUI altındaki ünitenin veri toplama ayarlarını değiştirir ve `disconnect()` komutu ünitenin akışını durdurur; bu da GUI&#x27;nin canlı görüntüsünü ve yaptığı tüm kayıtları sonlandırır. Önce GUI&#x27;de sensörü kapatın.

### PTP Grandmaster — `time_sync_status()`

Arka uç, LATTICE kameralarının ve DAQ-E birimlerinin zaman damgalarını aldıkları PTP grandmaster&#x27;ı çalıştırır. İsteğe bağlı olarak başlatılır: `connect_camera()`, `connect_array()`, `connect_daq_sensor()` ve `connect_all()`, çalışmıyorsa onu başlatır — örneğin, `connect_all()`&#x27;in mevcut olmayan bir DAQ-E&#x27;ye bağlanmaya çalışması gibi, daha sonra başarısız olan bir bağlantı da buna dahildir — ve aynı şekilde `test_array_sync()` ile tek bir komut için kameraları açan her çağrı da bunu yapar (`capture_cameras_transient()`, `start_multi_camera_stream()`, hizalama çağrıları, `capture_live_index()`).

- **Nerede duyurulur?** Kameralara bağlı kablolu ağ adaptörlerinde (bir `169.254.x.x` bağlantı-yerel adresi dahil), aksi takdirde varsayılan yolun adaptöründe (eğer o da kabloluysa). Asla Wi-Fi veya sanal bir adaptörde (Tailscale, WSL): bunların arkasında kamera veya DAQ-E bulunmaz. Böyle bir adaptör çalışmıyorsa, hiçbir şeye duyuru yapmadan çalışır (`health` `NO_SOCKETS`) ve bir sonraki kamera veya DAQ bağlantısı kurulduğunda o adaptöre bağlanır.
- **Bağlantı kesintisinden sonra.** Kamera adaptörünün bağlantısı kesilip yeniden kurulduğunda (kablonun çekilmesi, anahtarın değiştirilmesi, USB adaptörünün yeniden numaralandırılması gibi durumlarda), grandmaster yeniden başlatmaya gerek kalmadan soketlerini kendi kendine yeniden oluşturur, ve bağlı tüm DAQ-E&#x27;leri yeniden kontrol eder, takılıp kalan boşluğu yeniden etkinleştirir. Ayrıca, hizmet verdiği tüm kamera ve DAQ-E&#x27;ler 15 saniye boyunca sessiz kaldığında da yeniden oluşturur.
- **Kabloya ulaşıyor mu?** `time_sync_status()` (`GET /api/time-sync/status`) değerini okuyun. `state` yalnızca grandmaster iş parçacığının çalıştığını gösterir; `health` ise paketlerinin gönderilip gönderilmediğini belirtir: `OK` (hata görülmüyor), `STOPPED`, `NO_SOCKETS`, `SENDS_FAILING`, `DEGRADED` (bazı adaptörlerde arıza) veya `PEERS_SILENT` (hizmet verdiği kameralar ve DAQ-E&#x27;ler yanıt vermeyi durdurdu), `health_detail`&#x27;te bir cümle ile. `announce_sent` / `sync_sent` / `delay_resps_sent` yalnızca gönderilen mesajları sayar; `announce_send_errors`, `sync_send_errors`, `follow_up_send_errors`, `delay_resp_send_errors`, `last_send_error`, `failing_interfaces` ve `last_delay_req_age_s`, gönderilemeyenleri, `rebinds` / `last_rebind` ise yeniden oluşturulanları tanımlar. `sync_sends_slow`, gönderimi 200 us&#x27;yi aşan senkronizasyonları sayar ve `sync_send_max_us` en uzun olanıdır; her bir Follow_Up&#x27;ın kaynak zamanı, gönderiminin en fazla 200 us&#x27;sini sayar. Grandmaster, kendi sürecinde (`own_process`, `process_pid`) çalışır ve Windows&#x27;ta yüksek önceliğe sahiptir (`process_priority`), böylece yoğun bir arka uç, damgalama zamanlarını geciktiremez ve tüm kameraların saatlerini aynı anda ayarlayamaz; bu işlem başlatılamazsa, eskisi gibi arka uç içinde çalışır (`own_process` false).

```python
st = chloros_sdk.time_sync_status()
if st.get("health") not in (None, "OK", "STOPPED"):
    print("PTP:", st["health"], "-", st.get("health_detail"))
    chloros_sdk.restart_time_sync()
```

| İşlev | Ne yapar |
| --- | --- |
| `time_sync_status()` | Grandmaster&#x27;ın durumu, sağlığı, saat kimliği, öncelikleri ve mesaj sayaçları (yukarıda). `chloros-cli time-sync status`. |
| `time_sync_peers()` | Algıladığı PTP bağımlıları (kameralar, DAQ-E&#x27;ler): `peers`, her bir `port_identity`, `last_seen_ip`, `last_delay_req_age_ms`, `delay_req_count`. `time-sync peers`. |
| `time_sync_cameras()` | Bağlı her dizi kameranın `PtpStatus`, `PtpOffsetFromMaster` ve `PtpMeanPathDelay` (donanım yazılımında PTP düğümleri bulunmayan bir kameranın yalnızca seri numarası vardır). `time-sync cameras`. |
| `restart_time_sync()` | Grandmaster&#x27;ı durdurun ve yeniden başlatın; her havuzlanmış DAQ-E&#x27;nin PTP&#x27;si (`daq_ptp_rechecked`) sonrasında yeniden kontrol edilir. Geri dönmediğinde `ChlorosConnectError`&#x27;i tetikler geri dönmezse `ChlorosConnectError`&#x27;i tetikler. |
| `set_time_sync_priority(priority1=None, priority2=None)` | Başka bir PTP master&#x27;ının bulunduğu bir ağ için PTP öncelikleri (0-255; daha düşük olan best-master seçiminde kazanır). Çalışır durumda olması gerekir. `time-sync set-priority`. |

Her biri `backend_url=` ve `timeout=`&#x27;i alır ve çalışmayan bir yerel arka uç başlatır.

---

## Kamera Yardımcı Programları

`chloros-cli lattice`&#x27;in, `chloros_sdk`&#x27;teki (`chloros_sdk.camera_utils` modülü) basit işlevler olarak bağlantı kurma ve yakalama dışında yaptığı şey. Her biri, CLI komutunun kullandığı arka uç yolunu, CLI&#x27;nin kurallarıyla çağırır, bu nedenle ikisi de benzer şekilde davranır. Oturum değil, kamera **seri numarası**&#x27;nı alırlar ve akıllı bağlantı çağrılarıyla aynı `backend_url=` / `timeout=` anahtar kelimelerini kullanırlar; çalışmayan bir arka uç, `connect_camera()`&#x27;in yaptığı gibi başlatılır. Bir reddetme durumunda, arka ucun nedeni ile birlikte `ChlorosConnectError` hatası verilir.

```python
import chloros_sdk as c

c.set_camera_color_profile("214701281", profile="natural")
c.set_camera_highlights("214701281", -40)
print(c.camera_firmware_status()["cameras"])
st = c.network_adapters(estimate=True, cameras=4, pixel_format="BayerRG12")
```

| İşlev | Ne yapar |
| --- | --- |
| `camera_color_profile(serial)` / `set_camera_color_profile(serial, profile=, custom_cct_k=)` | Ekran renk profili: `raw`, `linear`, `natural`, `enhanced` veya `custom_temp` ve renk sıcaklığı (2000-10000 K aralığında sınırlandırılmıştır; profil `custom_temp` olduğunda okunur). |
| `camera_color_adjust(serial)` / `set_camera_color_adjust(serial, saturation=, contrast=, reset=False)` | Ekran doygunluğu ve kontrastı, her biri %0-200 aralığında (100 = değişmez); `reset=True`, her ikisini de 100&#x27;e geri getirir. |
| `camera_highlights(serial)` / `set_camera_highlights(serial, value)` | Ekran görüntüsündeki en parlak, en beyaz pikselleri aşağı çekin: -100..0 (sıfıra doğru kesilir, ardından sınırlandırılır; 0 = kapalı). |
| `camera_white_balance_once(serial, settle_s=None)` | Tek seferlik beyaz dengesi: kamera kısa süreli akış yapar, yakınsama sağlar ve `red_ratio` / `blue_ratio` değerini döndürür (raporlayamadığı durumlarda `ratios_read` False). Çözdüğü beyaz dengesini korur; open işlevinin yazdığı diğer tüm ayarlar olduğu gibi geri gönderilir. |
| `set_camera_white_balance(serial, mode=None, red=None, blue=None)` | `mode="auto"` (sürekli) veya `"off"`; ya da sabit `red` / `blue` denge oranları, bunlar otomatik modu ilk olarak kapatır. Kameranın reddettiği bir oran, `errors` altında adlandırılır. |
| `camera_presets()` / `apply_camera_preset(serial, preset)` | Kameranın ön ayarları ve bunlardan birinin uygulanması: kamera, ön ayar adlarında belirtilen ayarları korur (yanıt `settings`&#x27;tir; çünkü kamera bunları daha sonra okur, örneğin çalıştığı kare hızı; `None` ise ön ayarın adını vermediği bir alan içindir) ve açık olarak yazılan diğer tüm ayarlar bulunduğu gibi geri gelir. `"default"`, kamerayı fabrika varsayılanlarına sıfırlar (serbest çalışan otomatik pozlama, bağlantı verim sınırı ve kusur düzeltmesi kapalı, bir RGB kameranın kalibre edilmiş beyaz dengesi korunmuş olarak fabrika ayarları), belleğine kaydedilmez: yanıtta `factory_defaults: True` ve `settings`&#x27;in tüm alanları bulunur. `camera_presets()`&#x27;te, `default`, `{factory_defaults: True, description}`&#x27;tir. |
| `camera_filter_models()` / `camera_filter_model_info(name)` | Filtre modelleri ve bir modelin ayrıntıları (bantlar, spektral tepki koşul sayısı). Bir kameranın filtre modeli, model dizesinden okunur; elle ayarlanmaz. |
| `camera_firmware_status(serial=None)` | Her kameranındonanım yazılımı: `current_version`, `available_version`, `update_offered`, `needs_minimum_update` ve `minimum_firmware`. |
| `update_camera_firmware(serial, force=False)` | Bu Chloros sürümünün donanım yazılımını bir kameraya yükleyin: fabrika ayarlarına sıfırlama, yükleme, yeniden başlatma; Chloros temel ayarları yeniden yazılır; kameranın kalibrasyonu korunur. Birkaç dakika sürer; kamera açık kalmalıdır. `force=True`, mevcut bir kameraya yeniden yazılır. |
| `camera_power_probe(serial)` | Kameranın güç ve termal düğümleri ile sıcaklığı. |
| `sleep_camera(serial)` / `wake_camera(serial)` | Kameranın düşük güçte bekleme modu (uyandırılana kadar uyku modunda kalır: bağlantı verim sınırı etkinleştirildiğinde (`DeviceLinkThroughputLimitMode` `On`) en düşük seviyede, kamerada varsa güç tasarrufu modu açıkken), uyandırma öncesi/sonrası sıcaklık; uyandırma işlemi, bağlantıyı uyku modunda bulunduğu haliyle geri getirir (sınır, modu ve paket gecikmesi `GevSCPD`; sınır açıkken kamera bu değeri 0 olarak tutar; kayıt bu bilgisayarda tutulur, böylece daha sonra bir arka uçtan gelen uyandırma işlemi de bunu bulabilir), veya, bu bilgisayar tarafından uyku moduna geçirilmemiş ancak uyku modunda gibi görünen bir kamera için (sınırı taban değerinde), sınır devre dışı bırakılır ve `GevSCPD` değeri 80&#x27;de 0 olarak bırakılır (fabrika ayarları); taban değerinin üzerinde ayarlanmış bir sınır olduğu durumda, bu ayar olduğu gibi bırakılır. Yalnızca güç durumu korunur; açık yazma işleminin yapıldığı diğer tüm ayarlar, bulunduğu haliyle geri yüklenir. |
| `calibrate_camera_with_target(serial, filter=None, attempts=30, settings=None)` | Bir MAPIR kalibrasyon hedefi görüş alanına girene kadar kareler yakalayın ve panellerinden kanal başına katsayıları ayarlayın: `target_id`, `panel_dns`, `coefficients`. |
| `network_adapters(estimate=False, cameras=1, pixel_format="BayerRG8")` | Bu bilgisayarın GigE Vision için Ethernet adaptörleri (MTU, bağlantı hızı, jumbo çerçeveler) ve herhangi bir `issues`; `estimate=True`, `cameras` kameralarının alacağı kare hızı ve bant genişliğini ekler. |
| `fix_network_settings()` | GigE Vision adaptör ayarlarını bu bilgisayarın. Bu, adaptörün ayarlarını değiştirir ve yalnızca Chloros&#x27;un Yönetici olarak çalıştırılması durumunda çalışır; aksi takdirde, bulduğu bilgilerin günlüğünü içeren bir hata mesajı verir. |
| `apply_vignette_correction(input_dir, output_dir, lens_model=None)` | Bir klasördeki görüntülerde vinyet düzeltmesi yapın; her bir görüntü için kendi lensinin düz alan haritası kullanılır: `processed`, `lens_models` (her dosyanın düzeltme işlemi için kullanılan lens), `skipped` (nedenleriyle birlikte). `lens_model` belirtilmezse, her dosyanın lensi EXIF Model bilgisine göre belirlenir; `lens_model`, Model bilgisinde kamera adı belirtilmeyen dosyalara uygulanır; Model bilgisi bununla çelişen dosyalar ise atlanır. Yalnızca dosyalar. |
| `read_dls(mac, filter=None, camera_serial=None)` | Bir DLS’den (Bluetooth aşağı yönlü ışık sensörü) alınan bir okuma: spektrum, bant başına ışık şiddeti ve bant büyüklükleri, `camera_serial` verildiğinde kameranın kendi tepkisi aracılığıyla elde edilir. `discover_daq_sensors(transports=["ble"])`, MAC adresini bulur. |
| `probe_system(pixel_format="BayerRG12", discover=True)` / `system_info()` | Ana bilgisayarın kamera donanımı ve önerdiği yapılandırma (`report_text`) ile sistemin CPU / GPU / PyTorch çalışma zamanı. |

- **Bazıları kamerayı kendilerine ayırmak ister.** `camera_white_balance_once()`, `apply_camera_preset()`, `sleep_camera()` / `wake_camera()`, `calibrate_camera_with_target()` ve `update_camera_firmware()` kamerayı açar . Chloros kamerayı tutarken (uygulamada bağlıyken, `connect_camera()` ile bir dizi üyesi olarak veya şu anda başka bir komut tarafından kullanılıyorken, örneğin bir dizi testi veya kameralarını açan bir yakalama komutu gibi) arka uç, HTTP 409 `CAMERA_HELD` hatasıyla reddediyor (hem `code` hem de `error_code`&#x27;te)hatası ile reddedilir ve serbest bırakma yöntemi belirtilir. Renk ayarları, vurgu ayarları, `camera_power_probe()` ve okuma işlemleri bağlı bir kamerada çalışır.
- **Renk, vurgu ve beyaz dengesi ayarları RGB kameralar içindir.** Tek bantlı mono (M3M) bir kamerada, ayarlayıcılar hiçbir şeyi değiştirmez ve CLI&#x27;de olduğu gibi `{"skipped": True, "reason": ...}` değerini döndürür. Bunlar ekran ayarlarıdır: canlı görüntü ve ekran düzeyinde yakalamalar; asla parlaklık veya yansıma değeri değildir.

---

## Geçici Diziler, Hizalama Profilleri ve Dizinler

`chloros-cli lattice`, kalıcı bir bağlantı olmadan ne yapar? `chloros_sdk` (`chloros_sdk.array_utils` modülü) içindeki işlevler ise: `continuous`, `multi-capture`, `multi-stream`, `multi-test`, `multi-detect`, `analyze-array`, `align-calibrate`, `align-apply`, `align-stream`, `align-info`, `align-reorder` ve `index`. Her biri, CLI komutunun gönderdiği isteği, CLI&#x27;nin varsayılan ayarlarıyla birlikte gönderir.

```python
import chloros_sdk as c

prof = c.calibrate_alignment_profile(["214000535", "214000551", "214000556"],
                                     "D:/rig/alignment_profile.json", method="orb", model="rigid")
print({sn: t["reprojection_error"] for sn, t in prof["transforms"].items()})

c.capture_aligned_multiband("D:/rig/alignment_profile.json", "D:/rig/out")
idx = c.capture_live_index("D:/rig/alignment_profile.json", preset="NDVI",
                           output_dir="D:/rig/ndvi", capture_level="reflectance")

with c.start_multi_camera_stream(["214000535", "214000551"], "D:/rig/stream", fps=5, count=50) as job:
    print(job.wait()["final_stats"])          # waits for the 50 groups, then closes the cameras
```

- **Kameraları kendileri açarlar.** Her komut kendi kameralarını açar, işini yapar ve kapatır; bu nedenle Chloros&#x27;un elinde olmayan (uygulamada bağlı, `connect_camera()` ile veya bir dizi elemanı olarak) kameralara ihtiyaç duyar; arka uç, HTTP 409 `CAMERA_HELD` hatasıyla reddederek bir kameranın nasıl serbest bırakılacağını belirtir. Geçici bir açma işlemi biraz zaman alır (birkaç kamera için yaklaşık 40 saniye). Tekrarlanan yakalamalar için, `connect_array()` komutunu bir kez çalıştırmak en hızlı yoldur ve geçici yakalama işleminin debayered bir görüntü kaydettiği her dışa aktarma türünde tasarruf sağlar. `calibrate_alignment_profile()` ise bir istisnadır: bu kameralardan oluşan bağlı bir dizide, dizinin kendi karelerinden (`used_live_array`).
- **`serials`**, açılacak kameralardır; ilki tetiği tetikler; `None` ise ağdaki tüm kameraları açar.
- **`settings`**, kamera ayarlarının geçersiz kılınmasını içeren bir sözlük olup, CLI bayraklarının ayarladığı alanları içerir: `exposure_time` (µs), `gain` (dB), `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `frame_rate`, `gev_packet_size` ve `preset` (adından da anlaşılacağı üzere bir kamera ön ayarı). Belirtilen bir pozlama veya kazanç değeri, bayraklar gibi bu otomatik kontrolü devre dışı bırakır.
- **`gpu`**: `True`, GPU warp&#x27;ı zorlar; `False` ise CPU (`--gpu` / `--no-gpu`); `None` ise arka uçun seçim yapmasına izin verir.
- **`keep_settings`**/**`clear_settings`** (kameraları açan her işlev, varsayılan `False`): işlem sonunda her kamera bulunduğu duruma geri getirilir; `keep_settings=True` ise çalıştırma ayarlarını kameranın kendi belleğine kaydeder (serbest çalışma), `clear_settings=True` bu belleği fabrika ayarlarına sıfırlar — bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings). Yanıt (bir iş için, `stop()`&#x27;in), `camera_memory`&#x27;i içerir.
- **Yollar, arka ucun**: arka uç bu makinedeyken, göreceli yollar çalışma dizininize göre mutlak hale getirilir. Hizalama profili dosyası işlevleri (`alignment_profile_info()`, `reorder_alignment_profile()`), CLI&#x27;nin yaptığı gibi dosyayı bu bilgisayarda okur.

| İşlev | Ne yapar |
| --- | --- |
| `start_camera_continuous(serial, output_dir="output", *, format="tiff", jpeg_quality=95, queue_depth=64, settings=None)` | Durdurulana kadar bir kamerayı sürekli olarak kaydeder. Bir `CliJob` döndürür. Bir `tiff` çalıştırmasının kareleri, o kameradan bir dizi yakalama işleminin yazdığı `raw` dosyasıdır (sensörünkendi değerleri, sıkıştırılmamış); `png` ve `jpg` 8 bit içerir, dolayısıyla bu çalıştırmalar görüntüleme için demosaiklenmiş karelerdir. Bağlı veya havuzlanmış bir DAQ, çalıştırmadaki her kare için bir adet `.daq` dosyasını`daq/` klasörüne (istatistiklerinde `daq_files` olarak görünür) bir `.daq` dosyası yazar. Her kare, tam meta verileriyle (Model, yakalama zamanı, lens, bantlar, ham kare üzerinde kalibrasyon) bir kez yazılır; `stop()`, yakalanan her kare yazıldıktan sonra bir kez geri döner (en fazla 25 saniye) ve ancak o zaman kamera kapatılır. Donanım tetikleyicisi için hazır durumda bırakılan bir kamera (`TriggerMode` `On`), çalışma süresince serbest çalışma moduna ayarlanır (başlangıç yanıtındaki `trigger`, neyin bulunduğunu belirtir). Durdurma anında kamera, bulunduğu duruma geri getirilir — tetikleme ayarları ve çalıştırma sırasında değiştirilen tüm ayarlar — ancak çalışmanın ayarlarını kameranın kendi belleğine kaydeden `keep_settings=True` (serbest çalışma) ya da o belleği sıfırlayan `clear_settings=True` komutu kullanılmadıkça. Hiç kare kaydetmeyen bir çalışma, 10 saniye sonra `stop_reason` ve `no_frames` komutlarıyla sona erer ve nedeni `last_error`&#x27;te belirtilir ve `stop()` komutu bu nedeni `error` olarak döndürür. Yazıcıların yetişememesi nedeniyle atlanan kareler sayılır; `drop_reason` komutu bunun nedenini belirtir. Chloros&#x27;un tuttuğu bir kamera için `CameraSession.capture_repeated()`&#x27;i kullanın. `lattice continuous`. |
| `start_multi_camera_stream(serials=None, output_dir="output", *, fps=3.0, count=None, format="tiff", jpeg_quality=95, settings=None)` | `fps`adresinden, `count` grupları için veya durdurulana kadar senkronize edilmiş kare gruplarını akış olarak gönderir. Bir `CliJob` (`start_reply`, `n_cams` ve kablo `pacing` değerlerini içerir); son istatistikleri, yakalanan, yazılan ve atılan grupları, yazıcının yazamadığı dosyaları ve bir kameranın hiçbir şey iletmediği grupları sayar. `lattice multi-stream`. |
| `capture_cameras_transient(serials=None, output_dir="output", *, format="tiff", jpeg_quality=95, settings=None)` | Bir senkronize yakalama, kamera başına bir debayered görüntü: `results` (`filepath`, `serial`, `frame_id`) ve `pacing`. Bir kameranın eksik olduğu bir grup, önce kaç dosyanın kaydedildiğini belirterek uyarı verir. Dizi bağlı olmayan `lattice multi-capture`. |
| `test_array_sync(serials=None, count=10, ptp_settle_s=None, timeout=None)` | `count`, sabit pozlamada senkronize çekimler: `sync_groups`, `master`, her tetikleme başına bir `group_timestamps` satırı (bir çekimi kaçıran kamera, ilgili satırda yer almaz) ve `sync`: her iki kameranın ana kameradan sapması, tetikleme bazında (`offsets_us`: kamera başına `mean_us`, `min_us`, `max_us`, `jitter_us`, `groups`), TEK bir saat üzerinde (`clock_domain`: `ptp` veya `device` — PTP kapalı, her kameranın kendi çalışma süresi saati; dolayısıyla ofseti, kameranın açıldığı zamandır ve yalnızca jitter senkronizasyonu ölçer). Zaman damgalarında cihaz saati ile PTP&#x27;nin karıştığı bir tetikleyici (PTP kapalı veya hala kilitlenme aşamasında) dışarıda bırakılır ve sayılır (`groups_mixed_domain`); aynı şekilde, PTP saatinin ana saatten hala `agree_within_us`&#x27;ten fazla sapma gösterdiği bir tetikleyici de (`groups_clocks_disagree`); `notes` bunu belirtir. `clock_offset_mean_us` / `clock_offset_jitter_us`, kamera başına en büyük ortalama ve jitter değerleridir. `success`, bir tetikleme kaçırıldığında, bir saatte her kamerada iki tetiklemeden az olduğunda veya saatler hiçbir zaman uyuşmadığında (`sync.problems`); satırlar her iki durumda da döndürülür. PTP grandmaster çalışmıyorsa başlatılır (`time_sync`, `grandmaster_running` ve `grandmaster_started_by_this_test` değerlerini verir), her kameranın PTP&#x27;sini açar (`connect_array()`&#x27;in yaptığı gibi) ve ilk tetiklemeden önce her kameranın PTP saatinin grandmaster&#x27;a kilitlenip sabitlenmesini bekler: `PtpStatus` Bir grandmaster üzerinde Slave, ondan 100 us içinde ve 3 saniye boyunca sabit, ve her kamera bu şekilde kilitlenmiş, kesintisiz olarak 120 saniye boyunca — güç açıldıktan yaklaşık 2 dakika sonra. Kilitlenmiş bir saat, Slave okunduktan sonra yaklaşık 2 dakika boyunca ~200 us&#x27;ye kadar sapma gösterir ve kameranın kendi `PtpOffsetFromMaster`&#x27;i bunu göstermez; 120 saniye, kameraları kararlı bir saatten ~10 us&#x27;ye yaklaştırır. Bu arka uç tarafından görüldüğü üzere, PTP&#x27;yi açık bırakan önceki bir testten beri hala kilitli olan bir kamera, o kilitlenme anından itibaren sayar, bu nedenle arka arkaya yapılan testler arasında tekrar bekleme olmaz; test sırasında açılan bir kamera veya bu arka uç tarafından kilitlenmemiş (başka bir yerde açılmış ya da arka uç yeniden başlatılmadan önce) PTP açık olarak bulunan bir kamera, 120 saniyesini bekler ve her iki kameranın da kilitlenme süresini beklediği bir dizide yer alır. Slave durumundan çıkan, 100 us&#x27;yi geçen veya bekleme sırasında arka arkaya 3 okuma boyunca başka bir grandmaster&#x27;ı takip eden (1,5 saniye) bekleme süresince takip eden bir kamera, sayımına yeniden başlar; bu tür bir veya iki okuma günlüğe kaydedilir (`[array-sync] PTP SN:<serial> off lock at <s> s (<n> of 3): <status> <±x> us gm <id> -- locked <s> s, counting on`) ve sayım devam eder; okunamayan bir grandmaster kimliği, bir değişiklik olarak değil, bilinmeyen olarak kabul edilir. Her kameranın sayımının aynı anda (bir saniye içinde) yeniden başlaması, kameraların değil, grandmaster&#x27;ın zamanının ilerlemesinden kaynaklanır: arka uçgünlüğünde `[array-sync] PTP grandmaster step: all <n> cameras restarted at <s> s …` yazıyor, `ptp_lock` bunu `grandmaster_steps`&#x27;te listeliyor ve ardından süresi dolan bir bekleme, hatasını `every camera's count restarted together at <s> s -- the PTP grandmaster's time moved under them …` ile başlatıyor. Bu durum için sınır uzatılmaz. `ptp_settle_s` (0-600; yerleşme beklemesi yoksa `0`) çalıştırma için yerleşme süresini belirler; başka bir değer reddedilir (HTTP 400). Bekleme süresi, yerleşme süresine 90 saniye eklenerek sınırlandırılır (varsayılan olarak 210 saniye, en az 90 saniye) ve çağrı, arka uç `timeout` saniye kadar bekler: varsayılan olarak 240 artı yerleşme süresi (360 saniye), tıpkı `chloros-mcp`&#x27;in `test_array_sync`&#x27;inde olduğu gibi. `ptp_lock`&#x27;te `converged`, `waited_s`, `timeout_s`, `settle_s`, `locked_s` (her kameranın ne kadar süredir kilitli ve sabit kaldığı), `found_settled` (bulundukları anda zaten sabitleme süresi boyunca kilitli olan kameralar) ve kamera başına `status`, `offsets_ns`, `slave_s` (ne kadar süredir kilitli olduğu; Yok: kilitli değil) ve `remaining_s` (hiçbir kameranın PTP&#x27;si açılmadığında &quot;Yok&quot; olarak gösterilir). Arka uç günlüğünde her 5 saniyede bir, her kameranın durumu, ne kadar süredir kilitli olduğu ve ne kadar süre kaldığına dair bir satır bulunur. Her kamera PTP kapalıyken açılır; bu noktadan itibaren saatler yaklaşık 10 saniye içinde kilitlenir. Daha sonra her kamera, test sırasında tespit edildiği duruma geri getirilir — PTP&#x27;si, otomatik pozlama ve kazanç ayarları, test süresince test tarafından Kapalı konumda tutulur (`ExposureAuto` / `GainAuto` ve manuel olarak ayarlanan `ExposureTime` / `Gain`), ve geri kalanlar, `keep_settings=True` hariç — yani PTP açık bırakılan bir kamera (`PtpEnable=True` veya `keep_settings=True` ile yapılan bir çalıştırma) kilitli kalır ve bu arka uç, kameranın yerleşme süresi boyunca kilitli olduğunu gördüğünde, sonraki testler onu beklemez. Saatler yerleşmezse, herhangi bir tetiklemeden önce `success` yanlış olur ve `error` ne yapılması gerektiğini belirtir: hiç kilitlenmemiş bir kamera (`not_slave`), grandmaster&#x27;ın ona ulaşamadığı anlamına gelir (`time_sync_status()`); sürekli bağlantısı kesilen bir kamera ise ne kadar süredir kilitli olduğuna göre adlandırılır (`remaining_s`); saatler hala yakınlaşıyor (`far`): `time_sync_status()`&#x27;i kontrol edin ve testten önce kilitlenmelerini sağlamak için PTP&#x27;yi açın ve açık bırakın (her kamerada `PtpEnable=True` veya `keep_settings=True`; test, bulduğu ayarları geri yükler, bu yüzden açık kalır), ardından testi çalıştırın. `lattice multi-test`. |
| `detect_array_master(line=None)` | Senkronizasyon hattındaki kameralar ve varsayılan ana kamera: `master_serial`, `slave_serials`, `line` tarandı. `lattice multi-detect`. |
| `analyze_array_models(camera_models=None, *, width=2048, height=1536, n_active=None, force_tier=None, binning=1, packed="auto", pixel_format=None)` | Bu kamera modellerinin (ürün adları, `"LATT-M3C-L87-FOCN"`) bu bilgisayarın ağına bağlanacak: `tier`, `achievable_fps_max`, kablo bütçesi (`max_cams_collision_safe`, `oversubscribed`), `cam_caps` ve `fix_suggestions`. Varsayılan olarak, tespit edilen kamera modelleri; hiçbir kamera açılmaz. Bağlantı için kullanılacak ayarlara sahip kameralarınız için bkz. [`analyze_array_network()`](#network-analysis-helper). `lattice analyze-array`. |
| `calibrate_alignment_profile(serials=None, save_path="alignment_profile.json", *, frames=3, method="orb", model="rigid", reference_serial=None, checkerboard=None, control_points=None, preview=False, vignette=False, roi=None, mask_path=None, rms_threshold_px=None, per_cam_overrides=None, settings=None, ...)` | Canlı karelerden bir hizalama profili oluşturun ve kaydedin. `method`: `orb`, `akaze`, `phase`, `checkerboard` (`checkerboard="9x6"` ile, iç köşeler) veya `manual` (`control_points={serial: [[x, y], ...]}` ile, referans kameranınkiler de dahil). `model`: `translation`, `rigid`, `affine` veya `homography`. Daha ayrıntılı seçenekler ise CLI&#x27;nin bayraklarıdır: `max_features`, `ratio_threshold`, `matcher` (`bf` / `flann`), `knn_k`, `prefilter` (`none` / `gradient` / `clahe` / `blur` / `hist_match`), `ransac_threshold_px`, `ransac_max_iters`, `ransac_confidence`, `averaging`, `lock_rotation`, `lock_scale`, `lock_axis`; `per_cam_overrides`, `{serial: {option: value}}`&#x27;tir. `rms_threshold_px`, daha kötü bir çözümü kaydetmeyi reddeder. `save_path`, `output_width` / `output_height`, `crop_roi`, `reference_serial`, `transforms` (kamera başına `reprojection_error`, `num_matches`) ve `preview_path`. `lattice align-calibrate`. |
| `capture_aligned_multiband(profile_path, output_dir="output", *, serials=None, format="tiff", bit_depth=None, gpu=None, no_crop=False, bands=None, order=None, vignette=False, per_band=False, per_camera=False, interpolation="linear", border_mode="constant", border_value=0, settings=None)` | Profil boyunca hizalanmış tek bir çok bantlı görüntü: `files` (TIFF boyutuna sahip çok bantlı TIFF dosyası, her bant için birer tane `per_band` ve her kamera için birer tane `per_camera`) ve `vignette_corrected`. `bands` bantları seçer ve `order` bantları ada göre sıralar (bir liste veya virgülle ayrılmış dize). `lattice align-apply`. |
| `start_aligned_stream(profile_path, output_dir="output", *, serials=None, fps=3.0, count=None, ...)` | Akışa hizalanmış çok bantlı görüntüler (`capture_aligned_multiband()` ile aynı warp seçenekleri), `count` için veya durdurulana kadar. Bir `CliJob` döndürür; son istatistikleri `frames_written` ve herhangi bir `frame_groups_incomplete`&#x27;tir. `lattice align-stream`. |
| `alignment_profile_info(path)` | `align-info` olarak özetlenen bir profil dosyası bunu yazdırır: `name`, `created`, `method`, `reference_serial`, `sensor_resolution`, `output_width` / `output_height`, `crop_roi`, `camera_serials`, `transforms` (kamera başına `model`, `reprojection_error`, `num_matches`, `reference`), `camera_models`, `layer_order` (kamera başına `band_names`, `wavelengths_nm`, `enabled`); `profile` ise dosyanın tamamıdır. |
| `reorder_alignment_profile(path, order=None, enable=None, disable=None)` | Profil dosyasını yerinde değiştirin: `order`, bant adlarıdır (her biri kendi kamerasının katmanını bu sırayla yukarı taşır), `enable` / `disable` seri numaralarıdır. Yeni `layer_order` değerini döndürür. `lattice align-reorder`. |
| `index_catalog()` | Dizin ön ayarları (`presets`: her biri `name`, `channels`, `formula`) ve renk `gradients`. `lattice index --list-presets` / `--list-gradients`. |
| `compute_index(input_path, *, preset=None, formula=None, channel_map=None, output=None, capture_level="unknown", output_format="all", colorize=False, gradient="RdYlGn", vmin=None, vmax=None, percentile=None, bg_mode="clip")` | Hizalanmış çok bantlı bir TIFF dosyası üzerindeki bir dizin: bir `preset` (`NDVI`, `NDRE`, ...) veya `formula` sembolleri üzerinden bir `channel_map`, bantlara (`{"NIR": "NIR_850", "Red": 0}`, ada veya dizine göre) eşlenir. Bir ön ayar&#x27;sembolü `channel_map`, adıyla başlayan dosyanın tek bandını dışarıda bırakır (`red` -&gt; `Red_660`, `nir` -&gt; `NIR_850_RGN`), dosyanın kendisi tarafından adlandırılır (kendi bant listesi veya bir Chloros yakalamasının / ürününün `Chloros:Band{R,G,B}Name`), bu nedenle bir Chloros yansıma görüntüsündeki NDVI&#x27;ye `channel_map` gerekmez; bir sembole karşılık gelen iki bant reddedilir, tahmin edilmez. `capture_level`, bantların ne içerdiğini belirtir: `reflectance` veya `radiance`, değerleri nicel hale getirir; kalibre edilmemiş bir yığın (`align-apply`&#x27;ler) nitel değerler verir; `"unknown"` bunu dosyadan okur; bir Chloros dosyasının `Chloros:ProcessingLevel`&#x27;i dahildir. `stats` (şekil, yüzde 1-99 aralığı, LUT aralığı, kullanılan `capture_level`) ve girişin yanına yazılan `files` (`<input stem>_<preset>.tif`, `..._LUT.png`) veya `output` konumunda yazılan bir yol: dosya adı, görüntü uzantısı ile birlikte kökünden türetilir (`output="D:/out/ndvi_FRGN.png"`, `ndvi_FRGN.tif` ve `ndvi_FRGN_LUT.png` olarak yazılır), uzantı yoksa önek olarak kullanılır (`D:/out/ndvi` -&gt; `ndvi.tif`, `ndvi_LUT.png`); `files`&#x27;ten yolları okuyun. Bu dosyalar, girdinin kamera kayıtlarını (Marka / Model / seri numarası, GPS, lens, UTC cinsinden çekim zamanı, kalibrasyon kimliği ancak asla ham-DN kalibrasyonu hariç), `Chloros:ProcessingLevel=index` ve tek bant olarak indeksi (`XMP-Camera:BandName`, TIFF ve LUT PNG&#x27;ler gibi) içerir. Yalnızca dosyalar; kamera yok. `lattice index --input`. |
| `capture_live_index(profile_path, *, preset=None, formula=None, channel_map=None, serials=None, output_dir="output", output_prefix="index", capture_level="reflectance", save_multiband=False, ...)` | Çekim, kalibrasyon (`reflectance` veya `radiance`; `raw` bunu atlar), hizalama ve indeks hesaplamasını tek adımda gerçekleştirir; `save_multiband=True` kalibre edilmiş yığını saklar; bu yığını `compute_index()` daha sonra alabilir. `stats`&#x27;i (kullandığı bantlara sahip `serials` ile birlikte) ve `files`&#x27;i döndürür. `serials`&#x27;i açar (ilki tetiği ateşler), aksi takdirde `layer_order` girişi etkinleştirilmiş olan profilin kameralarını açar; önce referans (bantları devre dışı olsa bile açılır); bantları devre dışı olan bir kamera asla kalibre edilmez. `channel_map`, yalnızca yığının yanıt veremediği bir sembol için gereklidir, `compute_index()`&#x27;te olduğu gibi: bir ön ayarın sembolü, adı onunla başlayan etkin tek bandı alır; bu, herhangi bir kamera açılmadan önce kontrol edilir (yanıt vermeyenler reddedilir ve isimleri belirtilir); her sembol için özel bir `formula` gereklidir. `index.tif` ve LUT, yığının kamerasını, çekim zamanını (referans kameranın pozlama süresi, UTC) ve lensini taşır; her ikisi de indeksi tek bantları olarak adlandırır (`XMP-Camera:BandName`, ör. `NDVI`: renkli PNG dosyası, renk kanallarını değil, indeksi belirtir). `lattice index --live`. |

### `CliJob`

Üç akışın döndürdüğü tanıtıcı. İş, arka planda çalışır ve durdurulana kadar kameraların kontrolü bu işte kalır.

| Üye | Açıklama |
| --- | --- |
| `stats(timeout=10.0)` | `running`, `state`, `stats` (kaydedilen / yazılan, atılan, `fps`, `elapsed`, yazma hataları, `stop_reason`; sürekli bir işte ayrıca `frames_grabbed` ve `frames_queued` — yakalanan her kare yazılır, kuyruğa alınır, atılır veya hata verir — ve `drop_reason`, `writers` ve `write_ms`, bir karenin yazılması için geçen ortalama süre) ve `output_dir`. `state`, `running`, `ended` (kendi kendine durdu ve henüz durdurulmadı), `stopping` (bir durdurma işlemi kuyruğunu yazıyor: canlı istatistikler) ve `stopped_by`) veya `finished` (son istatistikleri, durdurma işleminin `error` ve `stopped_by`). Durdurulan bir işin kaydı, her istemci için 10 dakika boyunca (en fazla son 32) saklanır: başka bir komut dosyası veya `chloros-mcp` aynı durumu ve son istatistikleri okur. Bir iş `chloros-cli` başlatıldı (`lattice continuous`, `multi-stream`, `align-stream`) CLI&#x27;nin 30 saniye boyunca durum kontrolü yapmaması durumunda — sonlandırılmış, makinesi uyku modunda — arka uç tarafından durdurulur; arka uçta `stop_reason`, `client_gone` ve `stopped_by` bulunur; buradan istatistiklerini okumak onu çalışır durumda tutmaz. SDK&#x27;dan başlatılan bir iş, durdurulana kadar çalışır. Her başlatma, arka ucun son süresi (`client_deadline_s`) olarak `timeout`&#x27;i gönderir: bundan sonra açılan bir kamera (hala önyükleme aşamasında olan), geri yerleştirilip kapatılır ve hiçbir iş başlatılmaz; bu durumda çağrı, `ChlorosConnectError` (`CLIENT_DEADLINE_PASSED`) değerini döndürür. Bir işin tuttuğu kamera, diğer tüm açılışlara (`connect_camera()`, başka bir başlatma, geçici yakalama) HTTP 409 `CAMERA_HELD` hatasıyla reddedilir; bu hatada işin adı ve onu başlatan kişi belirtilir. |
| `stop(timeout=30.0)` | Durdurun ve kameralarını kapatın: `final_stats`, `output_dir` ve durdurma işlemi sorunsuz gerçekleşmediğinde `error`. İkinci bir çağrı da aynı yanıtı verir, başka bir istemciden gelen durdurma emri de aynı sonucu verir: ilki kuyruğunu yazmaya devam ederken gönderilen emir, ilkinin bitmesini bekler (en fazla 25 saniye; bu süre geçtikten sonra hata verir ve son istatistikler `stats()`&#x27;te gösterilir). |
| `wait(timeout=None, poll_s=0.5)` | Kendiliğinden sona erene kadar bekleyin (`count` değerine ulaşması, sürücünün dolması veya bir hata), ardından `stop()` komutunu vererek son sayıları toplayın. `timeout`, iş hala çalışırken `TimeoutError`&#x27;i tetikler; `count`&#x27;i olmayan bir iş, ancak durdurulduğunda sona erer. |
| `job_id`, `kind`, `output_dir`, `start_reply`, `stopped` | Kimliği ve türü (`continuous`, `multi-stream`, `align-stream`), yazma konumu, başlangıç yolunun yanıtı ve `stop()`&#x27;in çalışıp çalışmadığı. |

Kapsamdan çıkıldığında durdurmak için bunu bir bağlam yöneticisi olarak kullanın.

---

## Proje Klasörü ve Tarifler

**Proje klasörü**, yeni projelerin oluşturulduğu, Chloros uygulaması, CLI ve SDK (`chloros_sdk.system_utils`; `chloros-cli project-folder` modülü) tarafından paylaşılan klasördür:

```python
print(chloros_sdk.project_folder())                # the configured folder, else the default
chloros_sdk.set_project_folder("D:/Flights")       # created if missing
chloros_sdk.reset_project_folder()                 # -> {"path": <default>, "was_set": True}
```

Artık mevcut olmayan yapılandırılmış bir klasör, uygulama gibi varsayılana (`Documents/MAPIR/Chloros Projects`) geri döner. `set_project_folder()`&#x27;e verilen göreceli yol, arka uç bu makinedeyken çalışma dizininizden alınır.

**Bir reçete**, `chloros-cli project run PATH RECIPE`&#x27;in kaydedilmiş bir proje üzerinde çalıştırdığı YAML / JSON eylem listesidir. `run_project_recipe()` bunu CLI&#x27;kendi çalıştırıcısıyla (SDK ile birlikte yüklenir) çalıştırır; bu nedenle bir tarif, her iki durumda da aynı şekilde çalışır:

```python
r = chloros_sdk.run_project_recipe("D:/campaign/morning.yaml", "D:/Projects/Field_A")
print(r["ok"], r["actions"])
print(r["log"])                                    # what the CLI would have printed

chloros_sdk.run_project_recipe({"actions": [{"apply": {"name": "FrontLeft", "settings": {"exposure_us": 5000}}},
                                            {"wait": "2s"},
                                            {"capture": {"name": "FrontLeft", "output": "pose_a/", "format": "tiff"}}]},
                               "D:/Projects/Field_A")
```

```python
run_project_recipe(recipe, project_path, *, dry_run=False, echo=False,
                   backend_url="http://127.0.0.1:5000") -> dict
```

- **`recipe`**, bir `.yaml` / `.yml` / `.json` dosyasıdır ya da tarifin kendisidir: `actions` (`apply`, `capture`, `burst`, `stream`, `sensor`, `wait`) ve isteğe bağlı olarak `overrides` ve `stop_on_error` (varsayılan değer true: çalıştırma, başarısız olan ilk eylemde durur). Eylemler, CLI referansındaki [Recipe DSL](cli-reference.md#recipe-dsl) ile aynıdır; ilgili `output`, komut dosyanızın çalışma dizinine göre çözümlenir.
- **Döndürdükleri** `ok` (tüm eylemler başarılı), `actions` (kaç tane), `log` (çalıştırıcının çıktısı; `echo=True` de çalışırken bunu yazdırır) ve `validation`. Eksik olan veya ayrıştırılamayan bir tarif dosyası, `ChlorosConnectError` hatasını verir.
- **Çalıştırılmadan önce kontrol edilir.**`validation`, `{"ok", "errors", "warnings", "steps"}`&#x27;tir: her eylemin anahtarı olarak tam olarak bir adet bilinen fiil olmalıdır (`{"capture": {"name": ...}}`, `{"action": "capture"}` veya `{"type": "capture"}` değil) olmalı ve projenin sahip olduğu doğru türde bir kamera, dizi veya sensörü adlandırmalıdır. Hata içeren bir reçete,**hiçbir** eylemi çalıştırmaz (`ok` false, `log`&#x27;teki hatalar), `stop_on_error` ne derse desin. Kullanılmayan anahtarlar, ve tarifin kontrol ettiği ancak şu anda bağlı olmayan her dizi veya ışık sensörü, `warnings`&#x27;tir. Bir tarif, cihazları bağlamaz: projenin ilk cihazını (`ChlorosProject.connect_all()`, `chloros-cli project connect`, veya MCP aracı `connect_project_devices`). `dry_run=True` hiçbir şeyi çalıştırmaz ve `{"recipe": ..., "validation": ...}` değerini döndürür.
- Bir işlemde aynı anda yalnızca bir reçete çalışır. Bir YAML reçetesi için PyYAML gereklidir.

---

## Proje Otomasyonu — `ChlorosProject`

Kaydedilmiş bir Chloros projesi, `cameras.json` + `sensors.json` + `project.json` dosyalarını içeren bir klasördür. `open_project(path, relink=None)`, manifest dosyasını yükler (`relink`: taşınan bir projenin eksik kaynak dosyalarının şu anda bulunduğu klasör; arka uç bunu yüklediğinde kullanılır — `process()` — bkz. **Taşınan bir proje** bölümüne bakınız) ve `connect_all`, kaydedilmiş ayarlarıyla birlikte kaydedilmiş tüm kamera, dizi ve ışık sensörlerini çevrimiçi hale getirir — GUI&#x27;nin oluşturacağı donanım durumuyla aynıdır. Işık sensörleri, arka ucun [DAQ sensör havuzuna](#daq-sensor-sessions) bağlanır; bu nedenle çalışan bir arka uç gerektirirler ([`SensorHandle`](#sensorhandle) bölümüne bakınız).

### Basit Örnek

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Light sensors are addressable by name OR slot_id
reading = proj.sensors["Sky"].read()   # (spectrum, is_saturated, ...) or None

# One frame from every camera and array, one spectrum from every sensor
proj.capture_all("./out")

proj.disconnect_all()
```

Veya bir bağlam yöneticisi olarak:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `connect_all(*, cameras=True, arrays=True, sensors=True, verbose=False, align=None, packed=None)` | Kaydedilmiş tüm kamera, dizi ve ışık sensörlerini bulur ve bağlar. Sınıf başına bir bağlantı raporu döndürür (`{"cameras": {...}, "arrays": {...}, "sensors": {...}}`; her biri `connected`, `missing` ve `errors` ile birlikte; `arrays` ayrıca `alignment`&#x27;i de içerir). `127.0.0.1:5000`&#x27;te dinleme yapan bir arka uç varken, kaydedilmiş kameralar ve diziler bu arka uç aracılığıyla bulunur ve bağlanır. Arka uç yoksa, bu işlem onları kendisi bulur ve `lattice_sdk` üzerinden bulur ve çalıştırır; bu işlem için Arena SDK çalışma zamanı ve OpenCV (`cv2`) gerektirir — SDK tekerleği ikisini de yüklemez; yükleyemediğinde, kaydedilmiş her kamera ve dizi, nedeni ile birlikte `errors` altında listelenir ve `missing` boş kalır. Çalışıyor ancak SDK&#x27;yi reddeden bir arka uç (oturum kapatıldı: HTTP 401; Copper&#x27;ın altındaki bir plan: 403), &quot;arka uç yok&quot; durumu değildir: kaydedilmiş her kamera, dizi ve ışık sensörü, arka ucun nedeniyle birlikte `errors` altında listelenir ve bu işlemde hiçbir şey açılmaz. Her çağrı arka ucu yeniden kontrol eder; bu nedenle, oturum açıldıktan sonra veya Chloros başlatıldıktan sonra `connect_all()`, aynı proje nesnesine bağlanır. Aynı kameralara sahip (GUI&#x27;ninkiler veya `chloros-cli project connect`&#x27;inkiler) ve arka uçta zaten aktif olan kaydedilmiş bir dizi, kaydedilmiş ayarları yeniden uygulanmadan olduğu gibi eklenir, ve `disconnect_all()` onu çalışır durumda bırakır. Işık sensörleri her zaman arka uç üzerinden geçer (bkz. [`SensorHandle`](#sensorhandle)). Asla bir arka uç oluşturmaz. |
| `disconnect_all()` | Her şeyi sonlandırır. Önce tüm aygıtlar serbest bırakılır; ardından, arka uç bir kameranın, dizinin veya ışık sensörünün serbest bırakılmasını onaylamamışsa (sonlandırma işlemi zaman aşımına uğramışsa veya reddedilmişse), her biri için bir `ChlorosConnectError` adı atanır. Zaten kaldırılmış bir aygıt veya çalışan arka uç yoksa, sessiz kalır. Başka bir istemcinin açık tuttuğu bir dizi veya ışık sensörü çalışmaya devam eder. Projenin `with` çıkışı asla tetiklenmez. |
| `capture_all(output_dir=".")` | Bağlı her kamera ve diziden bir kare, ve bağlı her ışık sensöründen bir spektrum (`SensorHandle.read()`), tek bir anda değil, sırayla (önce kameralar, sonra diziler, sonra sensörler) alınır. |
| `stream(camera, overlays=False, fps=10.0)` | Adlandırılmış bir kameradan (veya diziden) BGR `numpy` kareleri üreten jeneratör. `overlays=False`, `grab()` seviyesindeki`grab()`&#x27;i `debayered` düzeyinde okur: proje bir arka uç üzerinden bağlandığında arka uç üzerinden, aksi takdirde doğrudan bir `lattice_sdk` yakalama döngüsü (diziler `{serial: frame}` sözlükleri üretir). `overlays=True`, `ChlorosLocal.camera_stream()` → arka ucun `/api/camera/<serial>/stream-annotated` MJPEG beslemesi üzerinden yönlendirilir, kameranın kaydedilmiş `ui.overlay` bloğu sorgu parametreleri olarak iletilir. Arka uç modu, OpenCV (`cv2`) ve bir **bağımsız kamera** gerektirir: doğrudan moddaki bir kamera `RuntimeError` hatasını verir (arka uç, bu işlemin sahip olduğu bir kamerayı alamaz) ve bir dizi `NotImplementedError` hatasını verir (kamera başına bileşik görüntü katmanları — bir üyeyi adına göre akışa alır). Tek seferlik eşdeğeri: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Şu anda bağlı olan her dizi üzerinde hizalama işlemini çalıştırır. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Projenin görüntülerinde kalibrasyon / indeks iş akışını çalıştır (`ChlorosLocal.process`&#x27;i sarar; bu dördü **kabul edilen tek** anahtar-değer çiftleridir — `indices=` vb. `TypeError` hatası verir; indeksleri `ChlorosLocal.configure()` aracılığıyla ayarlar). Bir `ChlorosLocal()`&#x27;i gecikmeli olarak oluşturur; bu da bir arka ucu otomatik olarak başlatır. |

Özellikler:
- `proj.cameras` — `Dict[str, CameraHandle]`, ad ve seri numarasına göre anahtarlanır.
- `proj.arrays` — `Dict[str, ArrayHandle]`, ad ve array_id ile anahtarlanır.
- `proj.sensors` — `Dict[str, SensorHandle]`, ad ve slot_id ile anahtarlanır.
- `proj.config` — `project.json["config"]` sözlüğü.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**İşleme düzeyleri.** `capture()`, `grab()` ve `frame_stream()` hepsi aynı `processing`
token&#x27;ını kullanır ve zincir birikimlidir — her seviye, kendisinden yukarıdaki her şeyi çalıştırır:

| Seviye | Çıktı | Notlar |
| --- | --- | --- |
| `raw` | 1 kanallı Bayer, sensörün kendi formatında | Demosaik yok. Bu seviyede üst üste bindirme özelliği mevcut değildir. |
| `debayered` | 3 kanallı renkli (`CameraHandle` ve tüm `grab()` / `frame_stream()` için; `ArrayHandle.capture()`, `capture_smart()` ve `stream()` varsayılan olarak `raw`&#x27;e ayarlanır): Bellekte BGR (`grab()` / `frame_stream()`), `capture()` tarafından RGB olarak kaydedilir | Arka uç modunda, yakalama olarak: kalibrasyon paketinin ham veriden alınan karanlık ofseti (DSNU), ardından OpenCV&#x27;nin kenar duyarlı demosaik işlemi — doğrusallık LUT&#x27;u, düz alan veya renk ayrıştırma yok. Arka uç modu olmadan çalışan tek seviye, burada kamera kütüphanesinin düz bilineer demosaik işlemi kullanılır (karanlık ofset yok). Kaydedilen TIFF dosyası fotometrik RGB olduğundan, bellek içi bir karenin kanallarını ters çevirin (`frame[..., ::-1]`) bir dosyayla karşılaştırmak için ters çevirin. |
| `radiance` | float32, W/m²/sr/nm, **her bant için bir düzlem** | Tam radyometrik zincir: demosaik + 3×3 ayrıştırma (multispektral) + DSNU + düz alan + NIST ölçeği; pozlama × kazanç bölünerek değerlerin mutlak olması sağlanır. |
| `reflectance` | uint16, 32768 = 1,0 (veya uint8, 255 = 1,0 — bkz. `bit_depth`), **her bant için bir düzlem** | Parlaklık, aşağıya doğru ışınım yoğunluğuna bölünür (ρ = π·L/E). Bir DLS/DAQ okuması gerektirir — aşağıdaki nota bakınız. |
| `display` | 8 bit sRGB benzeri | GUI&#x27;ye eşdeğer işleme: Kameranın etkin renk profili aracılığıyla CCM + beyaz dengesi + gama. |

Doğrudan (arka uçsuz) modda yalnızca `debayered` kullanılabilir: `grab()` başka bir seviyede
`NotImplementedError`&#x27;i yükseltir, `capture()`, `processing`&#x27;i yok sayar ve `frame_stream()`,
`RuntimeError`&#x27;i tetikler. `reflectance`&#x27;in kullanılabilir bir aşağı doğru okuma değerine ihtiyacı vardır — çerçeve uç noktası,
birleştirilmiş DAQ&#x27;yi otomatik olarak kameranın DLS yuvasına çeker, ancak DAQ bağlı olmadığında zincir,
yansıtma çıkışını reddeder ve sessizce
daha düşük kaliteli bir ürünü sessizce iade etmek yerine,

> **Yansıma DN ölçeği — bunu sabit kodlamayın.** LATTICE yansıma, `32768` = ρ 1,0 değerini kullanır ve
> XMP `Chloros:PixelScale=32768` olarak işaretler; Survey3 yansıma, `65535` = ρ 1,0 değerini kullanır ve
> `Chloros:*` etiketleri içermez. Etiketi okuyun ve ona bölün. uint16 alanında tanımlandığından, yeniden ölçeklendirme yapılan her formatta (16-bit TIFF, 8-bit TIFF,
> 8-bit PNG/JPG, 32-bit yüzde) `32768` olarak kalır
> — önce depolanan veri türünü uint16&#x27;ya normalize edin (8-bit&#x27;ten ×257, float&#x27;tan ×65535). Tek
> istisna, 1.3.0&#x27;dan önceki bir derlemeden gelen dosyalardır: o zamanlar 8-bit TIFF dosyaları yeniden ölçeklendirilmez, *kırpılırdı*, bu nedenle
> bu dosyayı tanımlayan bir ölçek yoktur ve `PixelScale` veya MicaSense tuple&#x27;ı içermez. Bir
> LATTICE yansıma dosyasında eksik bir etiket varsa bunu varsayılan değer olarak değil, &quot;geçerli ölçek yok&quot; olarak değerlendirin.

**Kalibrasyon etiketleri: yalnızca `raw`&#x27;te.** Bir yakalamanın `raw` dosyası, kameranın kalibrasyon paketini içerir: `CalibrationJSON` yükü ve sensörün ham değerlerini açıklayan MicaSense uyumlu etiketler (`BlackLevel`, `RadiometricCalibration`, `Band{R,G,B}RadiometricCalibration`, `MicaSenseVignetting*`), sensörün ham değerlerini tanımlar. Diğer her seviyedeki dosya (`debayered`, `display`, `radiance`, `reflectance`, `index/` render) ve her sürekli kayıt / çoklu kamera akışı karesi, kameranın kimliğini taşır (`XMP-mapir:CameraSerial`, `CameraModel`, `FilterType`, `LensModel`, `CalBundleSha256`, ...) ve karenin kendi kayıt bilgilerini (`XMP-mapir:FrameJSON`; pozlama, kazanç, sıcaklık, piksel formatı, çekim zamanı, DAQ eşleşmesi), çünkü her `process()` ürünü bu kimliği taşır. İşleme, Chloros&#x27;un kalibrasyon önbelleğinde bu tür bir dosyanın kalibrasyonunu seri numarası ve `CalBundleSha256`&#x27;e göre bulur; bu bilgisayarda yoksa paketi bir kez indirir.

#### Birden fazla kameradan oluşturulan dosyalar

Birden fazla kameranın bantlarından oluşturulan bir dosya — çok bantlı yığın (`capture_aligned_multiband()`, `start_aligned_stream()`, `capture_live_index(save_multiband=True)`, `Registration.save_multiband()`), birkaç kameradan oluşturulan canlı bir dizin (`index.tif`, `index_LUT.png`), bir dizi yakalamadan elde edilen `composite/` görüntüsü — bunlara dahil olan her kameranın adını belirtir:

- **Her-kamera listeleri, bant sırasına göre** (bileşik görüntü: arka plan katmanı, ardından ön plan; bir dizin katmanı, bantlarının geldiği kameralar): `XMP-mapir:SourceCameraSerials`, `SourceCameraModels`, `SourceSensorProfiles`, `SourceProductLines`, `SourceFilterTypes`, `SourceLensModels`, `SourceCalDateUTCs`, `SourceCalBundleSha256s` (her kameranın bantlarının geçtiği kalibrasyon paketi; aksi takdirde — bantları hiçbirinden geçmeyen bir `capture_aligned_multiband()` / `start_aligned_stream()` yığını — kameranın Chloros&#x27;un önbelleğe aldığı paketi, çekim dosyalarının adıyla aynı olan paket), `SourceLightboxCertIds`, `SourceHalogenLampCertIds`, `SourcePackTiers` — paralel listeler; her birinin *n* numaralı öğesi aynı kamerayı tanımlar; `-` ise bir kameranın değeri olmadığı durumdur (kalibrasyon paketi yok, bilinmeyen bir lens). Her kamera **tek** bir öğedir: paketinin bir alan için birkaç değeri varsa — bir paketin halojen lamba sertifikaları — bunlar `;` (`F-1479-1;F-1480-1;F-1481-1`) ile birleştirilir, asla virgülle değil; böylece liste, nasıl yazdırılırsa yazdırılsın kamera başına bir öğe olarak okunur.
- **EXIF `SerialNumber`**, virgülle birleştirilmiş aynı seri numaralarını içerir (`214000535,214000551`).
- Tek bir kamera kimliği **içermez** (`XMP-mapir:CameraSerial`, `CalBundleSha256`, …): bunlar bir kamerayı ve paketini belirtir ve işleme sırasında dosyanın kalibrasyonu bunlardan belirlenir. Birkaç kameranın bantlarından hesaplanan bir dizin (`index.tif`, `index_LUT.png`) EXIF `Model` `LATTICE` (sistem) değerini içerir, tek bir kameranın `LATT-…` modelini içermez.**Tek** bir kameranın bantlarından oluşan bir dosya — bant başına bir TIFF dosyası, bir `capture_aligned_multiband(per_camera=True)` karesi, tek bir kameranın yığını veya canlı indeksi — diğer tüm yakalama dosyaları gibi o kameranın kendi kimlik etiketlerini taşır, diğer tüm yakalama dosyalarında olduğu gibi. `capture_aligned_multiband()`&#x27;in yığını, bant başına ve kamera başına dosyaları ile her bir `start_aligned_stream()` yığını ve bant başına dosyaları, grubunun yakalama zamanını (referans kameranın pozlama süresi, UTC) ve bir mono kameranın `per_camera` karesi, yığın gibi `bit_depth`&#x27;te tek bir düzlemdir.**Kamera yönelimi (ters çevirme / döndürme).** `grab()` / `frame_stream()`&#x27;ten gelen kareler her seviyede her zaman
kameranın doğal yönelimindedir. Kaydedilen çekimler, kameranın yönelim
ayarını (GUI&#x27;deki Ayna H / Ayna V / döndürme) takip eder:

- **`raw` ve `debayered` dosyaları, ayar ne olursa olsun tam olarak sensörün okuduğu gibidir** — döndürülmüş veya ters çevrilmiş bir Bayer
  mozaik, farklı bir renk filtresi deseni oluşturur ve kalibrasyon haritaları
  sensörün kendi ızgarası üzerindedir.
- **`display`, `radiance`, `reflectance` ve dizin dosyaları yönlendirilmiştir** — tüm
  kalibrasyon hesaplamaları tamamlandıktan sonra bir kez döndürülür. Bunların kelepçe maskeleri aynı ızgarada yer alır.
- **Her çekim dosyası ayarı** XMP&#x27;de kaydeder: `XMP-Camera:ChlorosFlipHorizontal` /
  `ChlorosFlipVertical` (`true`/`false`), `ChlorosRotation` (0 / 90 / 180 / 270, saat yönünde,
  dönüşlerden sonra) ve `ChlorosOrientationApplied` — `false` ham ve bayar giderilmiş verilerde, `true` yönlendirilmiş
  ürünlerde. `ChlorosProcessing`, o dosyanın piksellerine uygulanan işlemleri listeler (`none` ise
  doğal formatta olduğunda). Buna, kesintisiz bir kaydın her karesi (kameranın kendi ayarı,
  uygulanmamış) ve çoklu kamera akışının her karesi (kimlik: üye kareleri, bir
  dizi yakalaması gibi doğal haldedir) dahildir.
- **Bir projenin işlenmesi, kaydedilen yönelimi** ham veriden oluşturduğu ürünlere uygular
  (önce sensör koordinatlarında kalibrasyon, ardından dizi hizalama çarpıtması, en son olarak yönlendirme),
  böylece canlı yakalamanın ürünleriyle eşleşirler — kalibre edilmemiş sensör tepkisi / vinyet düzeltmeli
  yedek ürünler dahil; debayered ürünü ise doğal kalır.
- **Chloros görüntü görüntüleyicisi, kayıt bilgisine göre döndürülmüş bir ham veya debayered dosyayı gösterir** — bu yalnızca görüntüleme amaçlıdır,
  dosya değiştirilmez; kayıt bilgisi olmayan bir dosya, depolandığı haliyle gösterilir.
- **Kayıt bilgisi olmayan yakalamalar, doğal olarak işlenir.**

1.3.0 sürümünden önce, tek kameralı bir yakalama
  ham ve debayered dosyalarını kendisi dönüştürürdü; Chloros, böyle bir ham dosyayı orijinal bir dosyadan ayırt edemez ve
  tahminde bulunmaz.
- **Dizi yakalamaları, varsayılan olarak her üye dosyayı orijinal haliyle tutar** ve kimliğini kaydeder;
  `ArraySession.capture(orient=True)`, çıktılarını dönüştürür (dizi yakalama denetimlerine bakın).

#### Bant başına çıktı ve karışık diziler

`radiance` ve `reflectance` seviyelerinde bir kamera, **o kameranın her bandı için bir düzlem** döndürür — bir M3C için üç
düzlem (karıştırılmamış üç bandı, BGR kanal sırası), M3M için bir tane (
tek dar bant filtresi). Mono kameranın çoğaltılmış düzlemleri daraltılır, bu nedenle **karışık
M3C/M3M dizisi, seri başına farklı dizi şekilleri verir**. Her düzlemin ne olduğunu öğrenmek için kare
meta verilerinden `bands` anahtarını okuyun; bunu kanal sayısından çıkarsamayın:

```python
for serial, (arr, hdr) in arr_handle.grab(processing="reflectance",
                                          with_metadata=True).items():
    print(serial, arr.shape, hdr["bands"])   # ('…', (1536, 2048, 3), ['nir','green','red'])
                                             # ('…', (1536, 2048, 1), ['F685'])
    rho = arr.astype("float32") / hdr["pixel_scale"]
```

Değerleri kullanmadan önce daima `hdr["processing_level"] == "reflectance"`&#x27;i kontrol edin. Bir düşürme
(DAQ okuması yok, kalibrasyon paketi yok, bilinmeyen bant düzeni) `processing_level` ve
`radiometric_refused`&#x27;te yükseltme yerine rapor edilir — geri aldığınız dizi hala geçerli bir çerçevedir, ancak
istediğiniz ürün değildir. Düşürülmüş bir kaydedilmiş yakalama, XMP&#x27;sinde
`Chloros:RadiometricRefused` (örn. `no-band-layout`, `no-dls-snapshot`).

#### Okuma başına bir tetikleme — `group_id`

**Dizi üyeleri, her kameranın kendi
yakalama tamponundan değil, dizinin senkronize edilmiş kare grubundan sunulur.** Donanımla senkronize edilmiş bir dizinin kameraları birlikte pozlanır, ancak kamera başına N adet okuma
birlikte *örnekleme* yapmaz — her kameranın en son tamponlanmış karesinin okunması, tetikleme
sınırlarını aşar ve tek bir düşen kare, kimse fark etmeden grubun senkronizasyonunu bozar.

Gruptan sunum gerekli olmakla birlikte tek başına yeterli değildir: her istek yine de
kendi anında *en son* grubun anlık görüntüsünü alır ve yakalama döngüsü bu arada yayın yapabilir.
Canlı bir 4 kameralı dizide yapılan ölçümlere göre, **20 paralel okumadan 12&#x27;si iki grubu kapsıyordu** — her zaman
tam olarak bitişikti. Dolayısıyla okuma **sabitlenir**:

1. Her kare, dizinin monoton yayın sayacı olan `group_id`&#x27;i taşır.
2. `grab()`, ana kamerayı okur (veya ana kameraya hizmet verilemiyorsa, hizmet verebilen bir sonraki kamerayı okur),
   ardından kalan kameraları ulaştığı grup kimliğine sabitler (HTTP
   uç noktasında `?group_id=N`).
3. Arka uç, son grupları **en az yarım saniye**boyunca tutar; bu sayede, daha sonraki yayınlar tarafından
  zaten geçilmiş bir kardeş grup, o tetikleyiciye hâlâ hizmet verilir — bu süre, 9 kameralı tam sensörlü bir dizide bile
   yansıtma verisi aktarımının gidiş-dönüş süresi için yeterlidir. Arka uç tarafından artık
   tutulmayan bir sabitleme, sessizce farklı bir tetikleyiciyle yanıtlanmak yerine
   farklı bir tetikleyiciyle sessizce yanıtlanmak yerine reddedilir — `grab()`, kümeyi yeniden okur.

Sabitleme ile yeniden ölçüldü:**20 okumadan 20&#x27;si tek bir tetikleyici döndürdü.** `grab()` bunu sizin için yapar;
HTTP uç noktasını doğrudan çalıştırırken yalnızca `group_id=`&#x27;e ihtiyacınız vardır.

- Sabitlenmiş grupta hizmet verilemeyen bir kamera — grupta yer almıyor veya isteği reddediyorsa
  (sahip olmadığı bir `band`, üretemediği bir seviye) — **farklı bir tetikleyiciden geri doldurulmak yerine atlanır**
  ve diğer kameralar yine de döndürülür.
- `grab()`&#x27;in döndürdüğü sözlük, bir `GrabResult`&#x27;tir &#x27;dir (düz bir `dict` alt sınıfı;
  `from chloros_sdk import GrabResult`): `.errors`, her bir
  çıkarılan seri numarasını, okuma işlemi sırasında ortaya çıkan istisnaya eşler ve `.group_id`, paylaşılan gruptur. Boş bir
  `.errors`, tüm kameraların bu grubu oluşturduğu anlamına gelir.
- Bağımsız bir kameranın grubu yoktur, `group_id: None` değerini bildirir ve bir `group_id=`
  pinini yok saymak yerine **reddedir**.

```python
frames = arr_handle.grab(processing="reflectance")
for serial, err in frames.errors.items():
    print(f"SN:{serial} omitted: {err}")       # e.g. status=400 ... band 'F685' not in [...]

frames = arr_handle.grab(processing="reflectance", with_metadata=True)
ids = {hdr["group_id"] for _a, hdr in frames.values()}
assert len(ids) == 1, f"straddled triggers: {ids}"     # belt and braces
```

Aynı sayaç, bir dizi üyesinin **her kamera başına MJPEG akışında** yayınlanır —
önizleme (`/api/camera/stream/<serial>`), açıklamalı önizleme (`/stream-annotated`) ve
yansıma akışı (`/stream-reflectance`) — her bir parçada `X-Chloros-Group-Id` olarak, yanında
`X-Chloros-Frame-Id` ve `X-Chloros-Timestamp-Ns`. Akışlar belirli bir
zaman anına ait değil, süreklidir; bu nedenle parçalar toplanarak ve bu başlığa göre gruplandırılarak eşleştirilir — ayarlanacak
bir pin yoktur. Bağımsız bir kameranın grubu yoktur. Bkz.
[Kamera başına MJPEG akışları](#per-camera-mjpeg-streams-no-rtsp).

**`frame_stream()` asla sessiz kalmaz.** Kendi kendine çözülebilecek bir nedenden dolayı başarısız olan bir
tick atlanır — bir kamera akışı bunun için hiçbir şey vermez, bir dizi akışı ise yanıt veren
kameraları (muhtemelen hiçbiri). Bu nedenler şunlardır: henüz kare yok; kameranın yakalama iş parçacığı bir
saniye veya iki süreyle durakladı (`404 not streaming`; bu durum, başka bir kamera bağlandığında
veya bağlantısı kesildiğinde ya da bir ayar değişikliği havuzu yeniden dengelediğinde arka uç tarafından yanıtlanır); arka ucun yeniden başlatılması veya `5xx`; hizalama
hala oturuyor (`409 … retry shortly`); ve DAQ’ın henüz bir okuma değeri olmaması nedeniyle yansıma reddi
(`no-dls-snapshot`, eski veya boş bir okuma). Bir sonraki tiktaki bir reddedilme sadece tekrarlanırdı —
bilinmeyen bir `band`, parlaklıkta `bit_depth=8`, parlaklık ölçeği olmayan bir kalibrasyon paketi, **hiç DAQ olmaması*
nedeniyle bir yansıma reddi* (kamera veya dizisine bağlı olan yok, arka uca bağlı olan yok
: reddedilmenin `dls_source`&#x27;i, `"none"`&#x27;tir), diğer herhangi bir `4xx` —
`chloros_sdk.FrameFetchError`&#x27;i tetikler (`RuntimeError`, `.status` ve `.body`&#x27;i taşıyan)
anında. Aynı şekilde, aralarında kare bulunmayan **60 saniyelik** atlanan tikler de, `fps` ne olursa olsun — hizalama
sabitleşmesini ve arka uçun yeniden başlatılmasını atlatacak kadar uzun, gerçekten
akışı sonlandırması için yeterince kısa bir süre. Bu vazgeçme hatası da bir `FrameFetchError`&#x27;tir; hem kamera hem de
dizi için geçerlidir; son okumanın kendi istisnası ise `.errors` (ve bir kamera için `__cause__` olarak). Bir
dizi için &quot;reddedildi&quot;, her kameranın reddedildiği anlamına gelir; istisnanın `.errors` kodu her kameranın
nedenini içerir.

**Bir dizi olan `frame_stream()`, her bir tetikleyiciyi bir kez verir** (`ArrayHandle` ve `ArraySession` de aynı şekilde). En
yeni kümesi zaten vermiş olduğu tetikleyici olan bir tik, aynı kümeyi tekrar vermek yerine bir sonraki tetikleyiciyi bekler,
böylece akış, `fps` ile dizinin kendi kare
hızından daha yavaş olan hızda çalışır ve ardışık kümeler asla bir `group_id`&#x27;i paylaşmaz. Yayınlamayı durduran bir dizi, son kümesini tekrarlamak yerine
son kümesini tekrarlamak yerine `FrameFetchError` (&quot;60 saniye boyunca yeni tetikleyici yok&quot;) sinyalini gönderir. Bu olmadan,
dizinin yayınladığından fazlasını talep eden — veya bağlanmanın hemen ardından başlayan — bir akış,
tekrarlar dağıtır: ölçülen bir çalışmada, 20 set 11 farklı grup kimliği içeriyordu.

```python
from chloros_sdk import FrameFetchError

try:
    for arr in cam.frame_stream(processing="radiance", bit_depth=8):
        my_analysis(arr)
except FrameFetchError as exc:
    print(exc.status, exc.body)        # the HTTP status and the backend's reason
```

#### Tek bir bandın dışa aktarılması — `band`

`grab()` / `frame_stream()`, bir kameranın bantlarından sadece birini
tek kanallı bir `(H, W, 1)` dizisi olarak dışa aktarmak için kullanılır — bu, tek renkli bir kameranın ürettiği şekle benzer; dolayısıyla tek bantlı çıktı,
ister bir M3M&#x27;den ister bir M3C&#x27;nin bölünmesinden gelmiş olsun, aynı şekilde işlenir. Bir FRGN&#x27;nin kırmızı bandı:

```python
red = cam.grab(processing="reflectance", band="red")        # (1536, 2048, 1)
```

Kabul edilen yazım biçimleri, tam düzen adı (`Red_660`, `NIR_850`, `Green_550`) veya kısa
başlığıdır (`red`, `nir`, `green`) olup, büyük/küçük harf duyarlı değildir. Bilinmeyen bir ad, mevcut
adların listesi gösterilerek reddedilir.

> **Seçim, kanal numarasına göre değil, isme göre yapılır — bu kasıtlıdır.** &quot;Kanal 1&quot;, dizinin BGR kanal sıralamasında
> `Green_550`&#x27;tir, ancak filtre kodundaki *kırmızı*harf sıralamasında *kırmızı*dır (`RGN`) ve bu ikisi
> birbiriyle uyuşmaz. Yanlış olanın seçilmesi, yanlış banttan tamamen makul görünen bir çerçeve ile sonuçlanır ve bu
> aşağı akışta hiçbir şey tarafından algılanamaz. Bu nedenle sayısal bir dizin bulunmamaktadır.

#### Akışları hizalama — `align`

`align=True`, kareyi dizisinin ortak referans karesine göre dönüştürür. Her kamera,
**aynı** profil çözünürlüğü ve kırpma ROI&#x27;si üzerinden dönüştürülür, böylece bağımsız olarak açılan kamera başına akışlar**birbirleriyle eşleştirilmiş** olarak çıkar — bunları birlikte talep etmenize gerek yoktur:

```python
frames = arr_handle.grab(processing="reflectance", align=True)   # {serial: array}
# or one MJPEG stream per camera, each co-registered on its own:
#   GET /api/camera/<serial>/stream-reflectance?align=1
```

Gereksinimler ve davranış:

- Kamera, hizalama
  profiline sahip **donanım senkronize** bir dizide (M8 senkronizasyon kablosu) olmalıdır — eş kayıt, üyelerin eşzamanlı olarak pozlandığını varsayar; bu, bir yazılım veya
  serbest çalışma dizisi bu özelliği sağlamaz. Aksi takdirde `409` ile reddedilir.
- **Reddedilir, asla sessizce hizasız bırakılmaz.** Yakalama yolu, bir uyarı ile hizasız olarak kaydedilebilir
  çünkü dosya köken etiketlerini korur ve daha sonra yeniden kaydedilebilir; canlı akışta ise
  böyle bir ikinci şans yoktur, bu nedenle eksik profil bir yedekleme değil, bir hatadır.
- **Hizalama talebi, süreci başlatır.** Profili olmayan bir dizide `align` talebinde bulunmak,
  kalibrasyonu başlatır ve beklediği durumu (örn. *&quot;214000551&#x27;de (1/3)
  pozlamanın sabitlenmesini bekliyor&quot;*) — yeniden denediğinizde, genellikle birkaç on saniye içinde başarılı olur.
  Bu, başsız modda önemlidir: bağlantı anındaki otomatik hizalama 30 saniye sonra vazgeçer ve
  bu süre içinde yapılan her deneme atlanır; bu nedenle pozlaması
  yavaş yakınsayan bir dizi, eskiden tüm oturum boyunca profilsiz kalırdı ve masaüstü birleşik görünümünü açmadan
  profil elde etmenin bir yolu yoktu. `alignment_status` ayrıca
  `ae_align_holdouts` kodunu bildirir ve bu kodda, henüz ayarlaması devam eden kameraların adları ve ilerleme durumları belirtilir.
- Bir dizinin her akışına **aynı `max_h`** değerini verin, aksi takdirde kırpma alanları farklı piksel
  ızgaralarına denk gelir.
- Hizalama, bit derinliği kodlamasından *önce* uygulanır, bu nedenle çarpıtma işlemi tam hassasiyetli
  değerleri enterpolasyon yoluyla hesaplar.

#### `bit_depth` — hassasiyet ile bant genişliği arasında denge

`grab()` ve `frame_stream()`, `bit_depth=16` (varsayılan) veya `bit_depth=8` değerlerini kabul eder:

| `bit_depth` | Yansıma kodlaması | Maliyet |
| --- | --- | --- |
| `16` | uint16, 32768 = ρ 1,0, ρ 2,0&#x27;a kadar başlık aralığı | Tam hassasiyet. |
| `8` | uint8, `0..refl_max`&#x27;i kapsar (varsayılan ρ 1,0) | Baytların yarısı; varsayılan üst sınırda ~0,004 ρ niceleme. Üst sınırın üzerindeki değerler kırpılır — aşağıya bakınız. |

##### 8-bit tavan — `refl_max`

`refl_max`, 8-bit aralığın kapsadığı ρ değerini belirler, bu nedenle `pixel_scale = 255 / refl_max` ve ρ değeri hala
`pixel / pixel_scale`&#x27;tir. `grab()` ve `frame_stream()`, hem kamera hem de
dizi tanıtıcılarında (HTTP uç noktasında `?refl_max=`) bir anahtar kelime olarak kabul eder. Speküler
parlaklıklar için daha geniş bir aralık elde etmek amacıyla, orantılı olarak daha kaba adımlarla bu değeri artırın:

```python
frame = cam.grab(processing="reflectance", bit_depth=8, refl_max=1.5)   # 0..1.5 in 0..255
```

| `refl_max` | `pixel_scale` | adım |
| --- | --- | --- |
| `1.0` (varsayılan) | 255 | 0,0039 ρ |
| `1.5` | 170 | 0,0059 ρ |
| `2.0` | 127,5 | 0,0078 ρ |

> **Otomatik pozlama, 8 bitlik bir kırpılmayı düzeltemez ve bunu denemek verileri daha da kötüleştirir.**
> ρ = π·L/E formülünde pozlama ve kazanç bölünmüş olduğundan, ρ sahnenin bir özelliğidir,
> kamera ayarının değil — canlı bir dizide ölçüldüğünde, pozlamanın yarıya indirilmesi ortalama ρ değerini %1,3 oranında değiştirmiştir.
> aralığa “uyum sağlamak” için pozlamayı artırmak ise pikselleri **sensör** doygunluğuna sürükler; bu da
> L değerini düşük gösterir ve herhangi bir hata mesajı vermeden ρ değerini **düşük** yönde sapmaya uğratır (ölçüm: maksimum ρ 1,203 → 0,985,
> 2 kat pozlamada, ortalama −%23). `refl_max` ile aralığı seçin; sensörü
> doygunluktan uzak tutmak zaten AE’nin işidir ve bu, ρ’nin aralığından ziyade *geçerliliğini* korur.

**`reflectance_clipped_fraction`’i okuyun, bayrağı değil.** `reflectance_clipped_at_1_0`,
tek bir pikselde tetikleniyor — gerçek bir sahnede, oran `0.000001` iken (bir milyon piksel
. Üst sınırı yükseltip yükseltmeyeceğinizi belirleyen şey bu kesirdir.

**`bit_depth=8` yalnızca yansıma değerini gösterir.** `radiance` için talep edildiğinde, bu istek karşılanmak yerine
reddedilir: Yansıma, 8 bitlik kodlamada korunabilen sabit bir 0..1 aralığına sahipken, parlaklığın
sabit bir aralığı yoktur; bu nedenle onu 8 bite sığdırmak için her kareye göre yüzde diliminde genişletme gerekir ve bu eşleme
her karede değişir — bu bir ölçüm değil, bir görüntüdür.

#### Canlı yansıma akışı (MJPEG)

`GET /api/camera/<serial>/stream-reflectance`, bant başına 8 bitlik yansıma değerini
`multipart/x-mixed-replace` olarak sunar — 3 kanallı JPEG olarak M3C, gri tonlamalı olarak M3M. Sorgu parametreleri:
`fps` (varsayılan 10), `jpeg_q` (varsayılan 95), `max_h` (0 = yerel), `band` (yukarıdaki gibi
adlandırılmış bir bant), `align` (`1` ile birlikte kaydedilir — SDK&#x27;daki `align` ile aynı kurallar geçerlidir), `refl_max` (8 bitlik
kodlama üst sınırı, yukarıdaki gibi). Her bir parça şunları içerir:
`X-Chloros-Group-Id`, `X-Chloros-Frame-Id`, `X-Chloros-Timestamp-Ns`, `X-Chloros-Bands`,
`X-Chloros-Pixel-Scale`, `X-Chloros-Aligned` ve `X-Chloros-Reflectance-Clipped` değerlerini taşır.
**Farklı kameralardan gelen ve aynı `X-Chloros-Group-Id` değerini taşıyan parçalar aynı tetikleyicidir** —
bir dizinin kamera başına birkaç akışı bu şekilde yeniden birleştirilir ve bu işlem,
dizi üyeleri senkronize edilmiş çerçeve grubundan sunulduğu için sunucu tarafında garanti edilir (bkz.
[Okuma başına bir tetikleyici](#one-trigger-per-read--group_id)). Bağlantı anında yansıma değeri
mevcut değilse `409` değerini döndürür ve işlem sırasında yansıma değeri kaybolursa akışı sonlandırır; dolayısıyla durmuş bir
akış, “hala iyi, sadece statik” anlamına gelmez, “artık yansıma yok” anlamına gelir.

> **JPEG kayıplı bir formatıdır — bu pikseller bir ölçümdür.** Sıkıştırma hatası doğrudan
> yansıma değerlerine yansır ve malzeme sınırlarının bulunduğu kenarlarda en kötü şekilde görülür. Kalite
> bu nedenle varsayılan olarak 95&#x27;e ayarlanmıştır. Sayıların bozulmadan kalmasını istiyorsanız,
> `grab(processing="reflectance")` / `frame_stream(...)` komutlarını kullanın; bunlar, yolunda kodek bulunmayan
> aynı sonucu verir (kodek değil de kablo maliyeti kısıtlayıcı faktörse, `bit_depth=8` komutunu kullanın).

> **EXIF verileri dışa aktarıma aktarılır.** `process()`, kaynak çekimin GPS bloğunu
> **ve ExifIFD&#x27;sini** her ürüne kopyalar; bu nedenle dışa aktarılanlar `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` ve `CameraSerialNumber`&#x27;i ve ayrıca
> coğrafi referanslama da içerir. `FocalLength`, Pix4D&#x27;nin yer örnekleme mesafesini hesapladığı veridir — bu veri olmadan
> yeniden yapılandırma son derece yanlış bir ölçeğe düşer (ölçülen bir örnekte 411 m&#x27;lik bir alan
> 47,8 km&#x27;lik bir alana dönüştü). Kopya kasıtlı olarak `-all:all` değildir: IFD0’un yapısal etiketleri
> LATTICE çıktısını bozuyor ve `ExifImageWidth`/`Height`, dışa aktarılan raster yerine kaynak
> çekimi tanımladıkları için hariç tutulmuştur.

Yakalama aşaması alt bayrakları (radyometrik seviyelere uygulanır — `radiance`, `reflectance`, `display`):

| Bayrak | Varsayılan | Anlam |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + düz alan + 3x3 ayrıştırma + NIST radyometrik ölçeği. |
| `apply_white_balance` | `True` | WB LUT. Bir DAQ kameraya bağlandığında DLS uyumlu. |
| `apply_index` | `False` | Bitki örtüsü indeksi değerlendirmesi. |
| `index_expression` | `None` | Formülü geçersiz kılma. Boşboş değilse → endeksi otomatik olarak etkinleştirir. |
| `annotated` | `False` | GUI süslemelerini (zebra/ızgara/tepe noktası) üstüne yerleştirme. `raw` için kullanılamaz. |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **Dönüş değeri bir `chloros_sdk.CapturePaths`&#x27;tir, türü `CapturePathMap`&#x27;tir, `Dict[str, str]` değildir.**
> `chloros_sdk.CapturePathMap`, `Dict[str, Union[str, List[str]]]`&#x27;tir: tek seviyeli
> `processing` her seriye tek bir yol verirken, `processing="all"` o kamera için kaydedilmiş her ürünün
> **sıralı listesini** verir. `ArrayHandle.capture()`
> `levels=`&#x27;i kabul etmez ve tanımadığı anahtar kelime argümanlarını (`levels`, `daq`,
> `render_index`, `force_daq`), bu nedenle yalnızca burada gösterilenleri geçirin (`aligned=False`
> `ArraySession.capture()`&#x27;te olduğu gibi yerel dosyaları kaydeder); bu denetimler için
> `ArraySession.capture()`&#x27;i kullanın. Canlı bir birleşik kompozit, eğer akış yapılıyorsa, bir seri numarası altında değil, ekstra
> `"combined"` anahtarı altında gelir, seri numarası altında değil. `str`&#x27;i varsayan kod,
> liste biçiminde herhangi bir tip denetleyicisi itiraz etmeden hata verir — açıklama, `Dict[str, str]`&#x27;in
> yazıyordu; bu yüzden bu takma ad mevcuttur. Düz biçimi
> istediğinizde normalleştirin:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

> **Yazılmayan şey: `.failed`.** `capture()`&#x27;in döndürdüğü harita,
> `chloros_sdk.CapturePaths` — basit bir `dict` alt sınıfıdır, bu nedenle yukarıdaki her şey
> değiştirilmeden çalışır — bu aynı zamanda `.failed`&#x27;i de içerir: arka uç tarafından yazılamayan dosyalar,
> [`CaptureResult.failed`](#yazılmayan-dosyalar--yakalama-sonucu-başarısız)
> (`serial`, `level`, `error` ve `disk_full: True`). Kısmi bir
> yakalama işlemi yine de yazılan tüm yolları döndürür; dolu bir sürücüde harita
> boştur ve `.failed` bunun nedenini belirtir. `.skipped`, kamera başına atlananları listeler ve
> `.group_seq` senkronize grubu, ve `.alignment` / `.oriented` ise
> arka uçtaki veriler olup, `CaptureResult`&#x27;te olduğu gibidir. `capture_smart()` bunları
> `result["frames"].failed` ve `result["failed"]` olarak döndürür.
>
> ```python
> paths = arr.capture("./out", processing="all")
> if any(f.get("disk_full") for f in paths.failed):
>     raise SystemExit("drive full -- free space and capture again")
> ```

### Dizi Hizalama

`ArrayHandle`, tam hizalama yüzeyini ortaya çıkarır. Profiller varsayılan olarak yalnızca oturum süresince geçerlidir — kalıcı hale getirmek için `export_alignment()`&#x27;i açıkça çağırın.

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Manual control points: each camera's pixel coordinates of the same
# features, the reference camera's included (>= 2 per camera). Every one
# of num_frames is solved from these points.
arr.calibrate_alignment(method="manual", model="rigid", control_points={
    "214701173": [[412, 300], [1630, 310], [1020, 1180]],   # reference (master)
    "214701292": [[418, 297], [1637, 306], [1027, 1177]],
})

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### Bağlantı Anında Hizalama

`connect_all(align=...)`, bağlantı anında her diziyi otomatik olarak hizalayabilir:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

Belirtilmediğinde `project.json["config"]["auto_align_on_connect"]`&#x27;e geri döner.

### `SensorHandle`

Arka uçtaki [DAQ sensör havuzunda](#daq-sensor-sessions) — `connect_daq_sensor()` ve `chloros-cli daq pool-connect`&#x27;in kullandığı havuzun aynısı — açılan kaydedilmiş bir ışık sensörü.

```python
reading = proj.sensors["Sky"].read()
if reading is not None:
    spectrum, is_saturated, integration_time, x, y, z = reading
```

- `read()`, sensörün **bir sonraki** spektrumunu bekler — çağrı yapıldığında halihazırda arabelleğe alınmış olan spektrum sayılmaz — ve `(spectrum, is_saturated, integration_time, x, y, z)` değerini döndürür; 5 saniye içinde hiçbir spektrum gelmezse `None` değerini döndürür. Duraklatılmış bir akış yeniden başlatılır. Sensör kalibre edildiğinde `spectrum` değeri W/m²/nm cinsindendir.
- `.sensor`, sensörün [`DAQSensorSession`](#daq-sensor-sessions) değeridir; diğer tüm durumlarda: `latest(n=...)`, `status()`, `record_start()` / `record_stop()`.
- `.slot_id`, `.model`, `.serial`, `.name` ve `.settings`, `sensors.json`&#x27;ten kaydedilen girdilerdir.

**`connect_all`&#x27;in geri yüklediği şey.** Her sensör, kaydedilmiş adresinde (COM bağlantı noktası, Bluetooth adresi veya DAQ-E ana bilgisayarı) açılır ve proje sensörün kimliğini kaydetmişse, bu sensör olmalıdır: o adreste bulunan farklı bir birim reddedilir, bağlantı kurulmaz. Kaydedilmiş entegrasyon süresi, kare ortalamalama ve otomatik pozlama ayarları uygulanır ve kapak düzeltmesi yapılır:

| Kaydedilen kapak | Bağlandığı |
| --- | --- |
| Bir kapak profili (örn. `fov_30`) | O profil. |
| Hiçbiri seçilmedi | `sunshine_cosine`, bağlantı varsayılanı, varsayıldığı gibi kaydedildi (`cap_id_source=auto_default`), bu nedenle işleme sırasında uyarı verilir. Sensörler kapaksız olarak gönderilir: takılı olan kapağı seçin. |
| `none` — GUI&#x27;de **Yok (çıplak sensör)** olarak seçildi | Kapak düzeltmesi yok. |

Bir DAQ-E-S, ne kaydedilmiş olursa olsun kapak profili kabul etmez: difüzörü, kalibrasyonunun bir parçasıdır (bkz. [DAQ-E-S](#daq-e-s--fixed-diffuser-and-tilt-sensor)). Daha önceki bir Chloros tarafından kaydedilen bir proje, çıplak sensörü bir seçenek olarak kaydetmemiştir; bu nedenle `sunshine_cosine` ile yeniden bağlanır; GUI&#x27;de bir kez daha **Yok (çıplak sensör)** seçeneğini seçin ve projeyi kaydedin.

Başka bir istemcinin havuzda (CLI, başka bir komut dosyası) halihazırda açık tuttuğu bir sensör, ayarlar yeniden uygulanmadan olduğu gibi eklenir ve `disconnect_all()` onu çalışır durumda bırakır; `connect_all()` tarafından açılan bir sensör serbest bırakılır. `connect_daq_sensor()`&#x27;te olduğu gibi, Chloros GUI&#x27;si havuzun bir parçası **değildir**: bir komut dosyasından projeyi bağlamadan önce GUI&#x27;de sensörü kapatın.**Gereksinimler ve rapor.** Işık sensörleri, ücretli bir Chloros+ kademesine (Copper veya üstü) giriş yapılmış, çalışır durumda bir Chloros arka ucuna ihtiyaç duyar ve `connect_all()` hiçbir zaman bir arka uç başlatmaz. Çalışan bir arka uç olmadığında, kaydedilmiş tüm sensörler `report["sensors"]["errors"]` altında listelenir ve bu durum belirtilir. Arka ucun açamadığı bir sensör de, arka ucun gerekçesiyle birlikte burada listelenir; `report["sensors"]["missing"]`, ışık sensörleri için boş kalır.

---

## Doğrudan Donanım (Arka Uçsuz)

LATTICE kameraları (CI, başsız robotlar, gömülü sistemler) için arka uca hiçbir şekilde bağımlı olmak istemiyorsanız, `lattice_sdk`&#x27;i doğrudan içe aktarın — bu, `chloros_sdk` tarafından yeniden dışa aktarılır. `CAMERA_AVAILABLE` ile ilgili uyarı: `lattice_sdk`, SDK tekerleğinde bulunur, ancak Arena SDK çalışma zamanı ve OpenCV (`cv2`) gerektirir; tekerlek ise bunların hiçbirini yüklemez. Işık sensörleri arka uç üzerinden çalışır — [`connect_daq_sensor()`](#daq-sensor-sessions) veya bir projenin `proj.sensors`&#x27;i — üzerinden geçer; tek istisna, DAQ-M bant büyüklüğü yardımcı programı [`DLS`](#daq-m-band-magnitudes--dlsband_magnitudes) istisnasıdır; bu yardımcı programın da `bleak`&#x27;e ihtiyacı vardır.

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### Ön ayarlar ve tetikleyici

Dört ön ayardan üçü **serbest çalışma** modundadır: kamera sürekli pozlama yapar ve
`capture()` bir sonraki kareyi döndürür. `triggered` ise istisnadır — bu ayar,
2. hattaki bir donanım kenarı sinyali için kamerayı hazır hale getirir; dolayısıyla bir sinyal gelene kadar hiçbir şey yakalamaz.

| Ön Ayar | Tetikleyici | Ne zaman kullanılır |
| --- | --- | --- |
| `default` | serbest çalışma | genel kullanım |
| `high_speed` | serbest çalışma | 8 bit, 60 fps sınırı, kısa pozlama |
| `high_quality` | serbest çalışma | 12 bit, fps sınırı yok — fotoğraf çekimi için genel tercih |
| `triggered` | **hazır, Hat 2** | kamera bir M8 senkronizasyon kablosuna bağlıdır ve başka bir şey tarafından tetiklenir |

`triggered`&#x27;i seçerseniz (veya `trigger_mode="On"`&#x27;i kendiniz ayarlarsanız) ve
Hat 2&#x27;yi tetikleyen hiçbir şey yoksa, her `capture()` zaman aşımına uğrayacaktır — çünkü kameradan
beklemesini istediniz. SDK, bu durum meydana geldiğinde bunu açıklar; bkz.
[Yakalama sırasında SC_ERR_TIMEOUT](#direct-hardware-backend-free).

> **Not — Bağlantı sırasında görülen &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` mesajları hata değildir.**
> Bağlantı kurulduğunda SDK, daha yüksek verim için**jumbo çerçeveler** (9000 baytlık GVSP paketleri) üzerinde anlaşmaya çalışır. Doğrudan noktadan noktaya NIC bağlantısında (örn. bağlantı-yerel bir `169.254.x.x` adresi), ağ genellikle jumbo çerçeveleri taşıyamaz; bu nedenle bu deneme zaman aşımına uğrar ve aşağıdaki gibi satırlar günlüğe kaydedilir:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Bu, **tasarlanmış yedek çözümdür**: SDK otomatik olarak standart 1500 baytlık paketlere geri döner ve kamera normal şekilde bağlanmaya devam eder (ardından gelen `[chunk-enable …]` satırları, normal bağlantı dizisinin bir parçasıdır). Görüntü yakalama işlemi hâlâ çalışır.
>
> Bu sondayı atlayabilirsiniz, ancak **bu sadece günlük kayıtlarını susturmakla kalmaz — jumbo çerçeveleri de devre dışı bırakır.** Kamera, ağınız ne kadar iyi olursa olsun Don&#x27;t-Fragment pinglerine yalnızca 1500 bayta kadar yanıt verir; bu nedenle ping testi tek başına jumbo çerçeveleri asla tespit edemez; bunu yapabilen tek şey bu sondadır. Bunu devre dışı bırakırsanız, kamera herhangi bir ağda sonsuza kadar standart 1500 baytlık paketler kullanır: herhangi bir ağda:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Yalnızca jumbo çerçeveleri taşıyamayacağını *bildiğiniz* bir ağda kullanmaya değer; bu durumda kamera başına bağlantı süresinden yaklaşık bir saniye tasarruf sağlar. Bu, sadece görünüşte değil, gerçek bir ödün verme durumu olduğu için, SDK&#x27;da bu özelliği kullandığınızda artık şu uyarı görüntülenir:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Bir nedeniniz yoksa bu ayarı değiştirmeyin.**Etkin bırakıldığında, her kamera arka uç oturumu başına bir kez ölçülür ve bağlantısı değiştiğinde (başka bir adaptör, başka bir bağlantı hızı veya bir anahtarın değiştirilmesi gibi bağlantının kesilmesi durumunda) tekrar ölçülür; yeniden bağlanmalarda sonuç yeniden kullanılır.**Çalışır durumda kalan bir anahtarın arkasındaki bir değişiklikten sonra, yeniden ölçüm yapmak için arka ucu yeniden başlatın.** İlk bağlantı sırasında bu kontrol bazen birkaç dakika sürebilir; böyle bir durumda günlüğe ilgili bilgi kaydedilir.
>
> Jumbo veri aktarım hızını *istiyorsanız*, uçtan uca jumbo özelliğini etkinleştirin (NIC MTU 9000 + bunları ileten bir anahtar) özelliğini etkinleştirin veya bağlantının bunu taşıdığını bildiğinizde `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` ile sabitleyin — ancak kalıcı olarak ayarlamak yerine komut başına `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` kullanmayı tercih edin, çünkü sabitlenmiş bir boyut, sondalamayı atlar ve önündeki ağa uyum sağlamayı durdurur. Yoldaki **her** cihazın jumbo paketleri iletmesi gerekir — PoE ayırıcı veya enjektörler de dahil; aksi takdirde jumbo desteğine sahip bir kurulumun bunları taşıyamamasının en yaygın nedeni budur.

> **`SC_ERR_TIMEOUT -1011` sırasında `capture()` / `grab*()` farklı bir sorundur — bu, gerçek bir hatadır.**
> Yukarıdaki not, yalnızca**bağlantı süresi probu**tarafından kaydedilen `-1011` ile ilgilidir. Bir**yakalama** işleminden kaynaklanan aynı hata, kameranın sorunsuz bir şekilde bağlandığını ancak herhangi bir görüntü göndermediğini gösterir:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Bunun en belirgin göstergesi, *kontrol* kanalı sağlıklı olan — keşif işlemi çalışıyor, ayarlar ve `[chunk-enable …]` yazma işlemleri başarıyla tamamlanıyor — ancak *her* karede zaman aşımı yaşanan bir kameradır.
>
> **Bunun en yaygın nedeni, kameranın donanım tetikleyicisi için etkinleştirilmiş olmasıdır.** `trigger_mode="On"` ve `trigger_source="Line2"` hatalarında, M8 senkronizasyon kablosuna bir elektrik sinyali gelene kadar kamera hiçbir şey göndermez. Bu hattı çalıştıran bir kablonuz yoksa, her veri alımı sonsuza kadar bekler. Kamera bozuk değildir ve ağda bir sorun yoktur — tam olarak kendisine söyleneni yapıyor.
>
> `CameraSettings()` ve `default` / `high_speed` / `high_quality` ön ayarları serbest çalışmaya geçer ve hazır durumdayken zaman aşımına uğrayan bir yakalama işlemi, sadece `-1011` yazmak yerine durumu açıklar. `PRESETS["triggered"]`, tasarım gereği Line2&#x27;yi hazır duruma getirir.
>
> Herhangi bir kamerayı serbest çalışmaya zorlamak için:
>
> ```python
> import copy
> settings = copy.deepcopy(PRESETS["high_quality"])   # PRESETS entries are shared
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> `trigger_mode="Off"` ile hala zaman aşımına uğruyorsa, kamera gerçekten veri göndermiyor demektir — bize günlüğü ve `ip link show`&#x27;i gönderin.

#### Renk Profilleri (RGB canlı önizleme) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)`, RGB kameralarda **canlı önizleme** için ekran renk profilini seçer (çok spektrumlu kameralar bu ayarı dikkate almaz):

| Profil | Anlam |
| --- | --- |
| `raw` | Radyometrik zinciri tamamen atlar. |
| `linear` | DSNU + düzleme + WB, CCM yok, gama yok. |
| `natural` | Doğrusal + ölçülmüş CCM + sRGB gama, yalnızca basit sonlandırma ile (kroma yumuşatma + parlak alanlarda doygunluk azaltma) — gerçekçi varsayılan ayar. |
| `enhanced` | `natural` artı tam hub-parity sonlandırma (renk kenarları giderme, canlılık, CLAHE yerel kontrast). Kare başına sonlandırma maliyeti yaklaşık **iki katına**, dolayısıyla daha düşük CANLI kare hızı. |
| `custom_temp` | `natural` ancak WB, `custom_cct_k` Kelvin (DLS yok sayılır; arka uç tarafında 2000–10000 K aralığına sabitlenir). |

Profil, **yalnızca canlı önizleme** için bir hız/görünüm ayar düğmesidir: kaydedilen görüntüler, seçilen profilden bağımsız olarak her zaman tam ve zengin son işlemeyi alır; bu nedenle, kare süresini geri kazanmak için `natural`&#x27;i seçmek, diske kaydedilen içeriğin kalitesini düşürmez. Bilinmeyen bir profil `ValueError` değerini yükseltir; bir chloros arka ucuna erişilebildiğinde, değişiklik bu arka uca da POST ile gönderilir, böylece bir sonraki önizleme karesi bunu yansıtır (arka ucu olmayan doğrudan SDK kullanıcıları da ayar değişikliklerini alır).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Öne Çıkanlar (RGB ekran) — `set_highlights`

`LatticeCamera.set_highlights(value)`, **ekran** RGB&#x27;de düz beyaz bir leke olarak görüntülenen parlak beyaz yüzey sorunu için düzeltmedir. `value`, `-100`..`0` (sınırlandırılmış); `0` kapalıdır ve bu durumda görüntü, ayar yapılmadığında olduğu gibi kalır. Daha yüksek ayarlar ekran beyazını düşürür (`-100`&#x27;te yaklaşık %60&#x27;a kadar) ve ton eğrisini genişletir, böylece önizlemenin otomatik parlaklığı nedeniyle saf beyaza itilen bir yüzeyin ayrıntıları geri kazanılır.

Bu bir ekran kontrolüdür, ancak doygunluk ve kontrasttan farklı olarak (ki bunlar yalnızca canlı önizlemeyi değiştirir), kaydettiğiniz içeriği de değiştirir: canlı önizleme ve **ekran düzeyindeki** çekimler bu ayara uyar, dolayısıyla bu dosyalar da daha karanlık çıkar (`Chloros:DisplayHighlights` değerini taşırlar); parlaklık ve yansıma dışa aktarımlarında bu asla olmaz ve kırpılmış sensör verilerini geri getiremez — bunun için pozlamayı azaltın. Multispec ve mono kameralar bunu yok sayar. Değer, `settings.highlights`&#x27;te saklanır ve erişilebilir bir chloros arka ucuna POST edilir, tıpkı `set_color_profile` gibi; CLI&#x27;deki karşılığı `lattice highlights --set N`&#x27;tir.

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_highlights(-60)                      # 0 = off, -100 = strongest
```

#### Mono (M3M) Kameralar ve `Calibration`

Bir mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) tek bantlıdır: tek bir gri tonlama düzlemi, Bayer mozaiği yok, 3×3 spektral çapraz konuşma matrisi yok. `Calibration` bunu tanır ve bir `is_mono` bayrağı gösterir. Yansıma, bant başına radyometrik harita olarak hâlâ geçerlidir (ayırma işlemi, kimlik matrisidir), ancak tek bir kamera üzerinde yapılan çok bantlı matematiksel işlemler, anlamsız sonuçlar vermek yerine verimliliği artırır:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

Mono donanımdan bir bitki örtüsü indeksi oluşturmak için, farklı dalga boylarındaki birkaç M3M kamerasını hizalanmış bir çok bantlı yığın halinde birleştirin ([Dizi Hizalama](#array-alignment) bölümüne bakın) ve indeksi tek bir kamera üzerinde değil, bu yığın üzerinde hesaplayın.

#### DAQ-M bant büyüklükleri — `DLS.band_magnitudes`

`DLS(mac_address, filter_model="RGN-IMX265")`, kendi BLE MAC adresi aracılığıyla Bluetooth üzerinden bir DAQ-M&#x27;ye bağlanır. Bu, arka uç üzerinden değil sensörle doğrudan iletişim kuran tek ışık sensörü sınıfıdır; bu nedenle, doğrudan kamera sınıflarının ihtiyaç duyduğu unsurların (`CAMERA_AVAILABLE`: Arena SDK çalışma zamanı ve OpenCV) yanı sıra Bluetooth için `bleak`’e de ihtiyaç duyar (`pip install "chloros-sdk[camera]"` veya `pip install bleak`; bu olmadan `DLS`, `DLSError` hatasını verir). Başka bir yere bağlı bir DAQ-M (arka ucun havuzu, bir proje, GUI) reklam vermeyi durdurur; bu nedenle önce orada serbest bırakın. **`mac_address` gereklidir**: Her birimin kendine ait bir değeri olduğundan varsayılan bir değer yoktur ve bu değer olmadan `DLS()`, herhangi bir bağlantı denemesi yapılmadan önce `DLSError`&#x27;i tetikler. XPR ile MAC adresini bulunOTX003351 (bkz. [Keşif](#discovery--finding-an-address-to-connect-with)) veya `chloros-cli daq pool-discover --only ble` ile MAC adresini bulun.

`DLS.band_magnitudes(spectrum=None, *, spectral_response=None)`, bir DAQ-M spektrumunu entegre eder (`spectrum` değeri `None` olduğunda en son okunan değer; henüz yoksa `DLSError`) bir kameranın üç kanal yanıtı aracılığıyla entegre eder ve `{"red": …, "green": …, "nir": …}` — anahtarlar kanalları bu sırayla adlandırır; bir RGB kameranın eğrilerinde üçüncü (`nir`) mavi kanaldır.

- `spectral_response` olmadan, tek bir ünitede ölçülmüş olan filtre modelinin filo eğrisini (`filter_model`&#x27;in `DLS` ile oluşturulduğu) kullanır.
- `spectral_response` ise bunun yerine kameranın **kendi** ölçülmüş eğrilerini kullanır: o kameranın kalibrasyon paketinde (`color_correction.spectral_response_smoothed` — RGB kameralar; multispektral paketlerde böyle bir eğri bulunmaz) depolanan bir dict `{"wavelengths": [...], "R": [...], "G": [...], "B": [...]}`. Kullanamadığı bir dict durumunda, filo eğrisine geri döner.

```python
from chloros_sdk import DLS

# camera_curves: {"wavelengths": [...], "R": [...], "G": [...], "B": [...]}
with DLS(mac_address="AA:BB:CC:DD:EE:FF") as dls:
    reading = dls.acquire()
    fleet = dls.band_magnitudes(reading)                         # fleet curve
    own = dls.band_magnitudes(reading,
                              spectral_response=camera_curves)   # the camera's own
```

CLI&#x27;deki `chloros-cli lattice dls --camera SERIAL`, arka uç aracılığıyla aynı işlemi gerçekleştirir; yüklü veya önbelleğe alınmış kalibrasyon paketinde kameranın eğrisini bulur ve hangi eğriyi kullandığını belirtir. HTTP üzerinden bu işlem, `POST /api/dls/cli-read` ile `mac` (zorunludur: bu olmadan yanıt HTTP 400 olur, `code` `MAC_REQUIRED`) ve gövdede `camera_serial` bulunur; yanıtta yer alan `band_magnitudes_source`, o kameranın kendi eğrisi kullanıldığında `camera <serial>` olur; filo eğrisi kullanıldığında ise `model <filter>` olur (eğrisi olmayan kamera: RGB değil veya paket yüklenmemiş veya önbellekte yok).

---

## İstisnalar

&quot;Chloros&#x27;ta ters giden her şeyi&quot; işlemek için temel sınıfı yakalayın:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

> `ChlorosAuthenticationError` ve `ChlorosConfigurationError`, `chloros_sdk` (ve `chloros_sdk.exceptions`&#x27;in) öznitelikleridir ancak `__all__`&#x27;te bulunmadıkları için `from chloros_sdk import *` bunları getirmez: bunları adlarına göre içe aktarın. `ChlorosError` ailesi, `ChlorosLocal` ve bunun üzerine inşa edilen işlevler (`process_folder()`, `process_lattice_capture()`) tarafından oluşturulur; akıllı bağlantı yüzeyi ise `ChlorosConnectError`&#x27;i devreye sokar.

Hiyerarşi:

```

ChlorosError
├── ChlorosBackendError           (backend binary not found / failed to start)
├── ChlorosConnectionError        (HTTP transport failure; no backend running
│                                  with auto_start_backend=False)
├── ChlorosLicenseError           (403 PLAN_UPGRADE_REQUIRED: plan below Copper)
├── ChlorosAuthenticationError    (401 AUTH_REQUIRED: not logged in)
├── ChlorosConfigurationError     (bad configure() inputs)
└── ChlorosProcessingError        (pipeline failed; create/import refused)
    └── ChlorosProjectFilesMissingError  (open_project(): none of the
                                  project's source files found, even after
                                  the relink; .missing_roots, .audit)

ChlorosConnectError                (the smart-connect surface: connect_*, attach_array,
                                    list_*, discover_*, every CameraSession /
                                    ArraySession / DAQSensorSession method,
                                    RecorderHandle, ContinuousHandle,
                                    disconnect_all(), and analyze_array_network()
                                    when no backend answers; 401 and 403 included.
                                    Derives from plain Exception, NOT from
                                    ChlorosError, so `except ChlorosError` will
                                    not catch it. .warning: a warning the
                                    backend sent beside its error (also at the
                                    end of the message); .recording_closed: the
                                    recording a failed DAQ-E restart closed;
                                    both None otherwise)

FrameFetchError (RuntimeError)     (grab() / frame_stream() refusals; .status,
                                    .body, .errors)
ValueError                         (capture() arguments: PNG/JPEG with a level they
                                    cannot hold; a bad packed=)
FileNotFoundError / ImportError    (open_project(): no such folder / no project
                                    loaders)
RuntimeError                       (CameraHandle / ArrayHandle: a backend refusal)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## Uçtan Uca Örnekler

### 1. Özel İlerleme Çubuğu ile Bir Klasörü İşleme

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. Canlı LATTICE Dizisi → Yansıma + DAQ Referansı

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. Proje Odaklı Yakalama Kampanyası

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. Çoklu Kamera Kare Akışı → NumPy İş Akışı

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. Başsız Doğrudan Donanım (Arka Uç Yok) Yakalama Komut Dosyası

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. 4 Kameralı Diziyi Bağlamadan Önce Yetenek Tespiti

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["width"], height=r["height"],     # before binning
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Yakalama Reçetesine Eşdeğer (Saf Python)

`run_project_recipe()`, bir CLI reçetesini olduğu gibi çalıştırır (bkz. [Proje Klasörü ve Reçeteler](#project-folder--recipes)). Bunun yerine Python ile yazıldığında, aynı adımlar şunlardır:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply: connect_all() applied each camera's saved settings. CameraHandle
    # has no settings method; a CameraSession on the same pooled camera does
    # (it is borrowed, so it never releases the camera)
    chloros_sdk.connect_camera(cam.serial).set_settings(
        exposure_time=5000, gain=6.0)

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## MCP Sunucusu (AI Asistanları)

`chloros-mcp`, Claude Desktop, Claude Code veya Cursor gibi [Model Bağlam Protokolü](https://modelcontextprotocol.io) (MCP) dilini konuşan bir AI asistanının Chloros&#x27;u yönetmesine olanak tanır: görüntüleri işler (proje oluşturur, yakalama içeren bir klasörü içe aktarır, ayarları seçme, işleme, işlemin sonucunda dışa aktarılan verileri raporlama) ve LATTICE kameralarını, dizilerini ve DAQ ışık sensörlerini çalıştırmasına (bağlanma, önizleme, pozlama ve renk ayarlama, yakalama, kayıt, ürün yazılımını güncelleme) olanak tanır. Araçları, bu SDK&#x27;nın yaptıklarını kapsar. Siz sade bir dille talimat verirsiniz ve asistan sunucunun araçlarını çağırır:

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

The assistant calls:
  create_project          {"project_name": "Flight_2026-10-17"}
  import_images           {"folder_path": "D:\\Flights\\2026-10-17"}
  configure_processing    {"indices": ["NDVI"]}
  start_processing        {}
  get_processing_status   {}      (every 30-60 s until its state is "finished")

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.

The assistant calls:
  list_devices            {}
  get_camera_preview      {"array_id": "array-1779862544497"}
  start_capture_run       {"array_id": "array-1779862544497", "mode": "interval",
                           "interval_s": 10, "duration_s": 300,
                           "processing": "reflectance"}
  get_job_status          {"job_id": "..."}   (until its state is "finished")
```

Sunucu bu SDK üzerine kurulmuştur: işleme araçları [`ChlorosLocal`](#image-processing--chloroslocal) ve donanım araçları ise [smart-connect çağrıları](#smart-connect-for-lattice-cameras), [DAQ oturumları](#daq-sensor-sessions), `ChlorosProject` ve [kamera yardımcı programları](#camera-utilities) çağırır; bunların tümü, Chloros uygulamasının kullandığı aynı arka uçla iletişim kurar. İstekleri `X-Chloros-Client: sdk`&#x27;i taşır, bu sayede arka uç, SDK’nın oturum açma ve plan kontrollerini uygular; SDK’nın kuralları da buna dahildir (örneğin, LATTICE çekimleri için Texture Aware reddedilir).

### Kurulum

Sunucu, `chloros-sdk` paketinde gelir; kurulacak başka bir şey yoktur. SDK, `chloros-mcp` komutunu yükler; bu komut, `python -m chloros_sdk.mcp_server` ile aynıdır:

```bash
pip install chloros-sdk        # not needed where the Chloros installer set up the SDK (Step 2)
chloros-mcp --help
```

- **Windows:** Chloros yükleyicisi, SDK&#x27;unuza SDK&#x27;u pip ile yükler; bu işlem, `chloros-mcp.exe` dosyasını o Python&#x27;un `Scripts` klasörüne yerleştirir.
- **Linux (.deb):** paket, sistemin `python3` klasörüne SDK&#x27;yi yükler ve `chloros-mcp` komutunu `/usr/bin` klasörüne yerleştirir; böylece bu komut her kullanıcının `PATH`&#x27;te bulunur. Bir virtualenv ortamında, `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`, virtualenv&#x27;in `bin` dizinine `chloros-mcp` dosyasını ekler.

Sunucu, SDK&#x27;nin (Python 3.7+) üzerine yalnızca standart kütüphaneyi kullanır. Bir MCP istemcisi bunu başlatır ve stdin/stdout üzerinden iletişim kurar; `--help` hariç, bunu kendiniz çalıştırmazsınız.

### İstemci Kurulumu

**Claude Desktop:**sunucuyu `claude_desktop_config.json`&#x27;e ekleyin (Windows&#x27;ta `%APPDATA%\Claude\claude_desktop_config.json`;**Ayarlar &gt; Geliştirici &gt; Yapılandırmayı Düzenle** seçeneği ile açılır), ardından Claude Desktop&#x27;ı yeniden başlatın:

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

**Claude Kodu:**

```bash
claude mcp add chloros -- chloros-mcp
```

**İmleç:** Claude Desktop ile aynı `mcpServers` girdisi, `~/.cursor/mcp.json`&#x27;te (her proje; Windows&#x27;ta `%USERPROFILE%\.cursor\mcp.json`) veya `.cursor/mcp.json`&#x27;te bulunur.

Seçenekler `args`&#x27;e veya Claude Code komutunun (`claude mcp add chloros -- chloros-mcp --no-auto-start`) arkasına yazılır. `chloros-mcp` yerine belirli bir Python SDK&#x27;sini kullanmak için, modülü o Python sürümüyle çalıştırın (`command`&#x27;te ilk `python3` değilse, tam yolu `PATH` şeklinde olmalıdır):

```json
{
  "mcpServers": {
    "chloros": {"command": "python3", "args": ["-m", "chloros_sdk.mcp_server"]}
  }
}
```

Bir istemci, sunucuyu sizin kabuğunuz olmadan başlatır; bu nedenle, sunucunun başlatılamadığı bildiriliyorsa, `chloros-mcp` muhtemelen istemcinin gördüğü `PATH` üzerinde değildir. Bunun yerine tam yolu belirtin (Windows&#x27;da `where chloros-mcp`, Linux&#x27;ta `which chloros-mcp`; burada .deb dosyalarının bulunduğu konum `/usr/bin/chloros-mcp`&#x27;tir) veya modülü SDK&#x27;yi içeren Python ile çalıştırın.

### Seçenekler

| Seçenek | Varsayılan | Ne işe yarar |
| --- | --- | --- |
| `--backend-url URL` | `http://127.0.0.1:5000` | Kullanılacak Chloros arka ucu. |
| `--no-auto-start` | kapalı | Asla bir arka uç başlatmaz: Chloros uygulamasınınki, Linux arka uç hizmeti veya başka bir makinenin (bkz. [Uzak Arka Uç Modu](#remote-backend-mode-pip-only-host-via-tunnel); bu durumda klasör yolları her iki makinede de aynı klasörü belirtmelidir). |
| `--backend-exe PATH` | Chloros&#x27;un kurduğu yer | SDK&#x27;nin aramadığı bir yere kurulmuş Chloros için, çalışır durumda arka uç olmadığında başlatılacak arka uç. |
| `--log-level LEVEL` | `WARNING` | `DEBUG`, `INFO`, `WARNING` veya `ERROR`. Günlükler stderr&#x27;e gönderilir; çoğu istemci bunu MCP günlüğünde gösterir; stdout ise yalnızca protokol mesajlarını taşır. |

### Araçlar

Her aracın argümanları, sarılmış olduğu SDK çağrısının argümanlarıdır; istemci her birini tam şemasıyla birlikte listeler. Yollar mutlak yollardır.

**İşleme**

| Araç | SDK çağrısı | Ne yapar |
| --- | --- | --- |
| `create_project` | `ChlorosLocal.create_project` | Bir proje oluşturur ve bunu, işleme araçlarının üzerinde çalışacağı açık proje yapar. Basit bir ad, varsayılan proje klasöründe bir klasör oluşturur; mutlak bir yol ise klasörü o konuma oluşturur. |
| `open_project` | `ChlorosLocal.open_project` | Kaydedilmiş bir projeyi açar ve onu açık proje yapar. Taşınan bir projenin yolları, projenin şu anda bulunduğu klasöre yeniden yönlendirilir; `relink_folder` (mutlak) adı, diğer eksik dosyaların şu anda bulunduğu klasörü belirtir. `source_files` ve `image_count` değerlerini döndürür. Hiçbiri bulunamadı: dosyaların bulunduğu yerleri listeleyen ve `relink_folder` adını veren bir hata; hiçbir şey açılmamıştır. |
| `import_images` | `import_images` | Survey3 `.RAW`+`.JPG` çiftlerini ve &quot;LATTICE&quot; `.tif` yakalamaları ve DAQ `.daq` kayıtlarını içe aktarır; `count` (görüntüler) ve `scan_count` (kayıtlar) değerlerini döndürür. |
| `configure_processing` | `configure` | Aktarılan ayarları değiştirir, diğerlerini değiştirmez (değerler: [Desteklenen Değerler](#supported-values)); `custom_settings`, verilen yapılandırmanın tamamını gönderir. |
| `get_project_settings` | `get_config` | Açık projenin kaydedilmiş işleme, dışa aktarma ve dizin ayarlarını okur. |
| `start_processing` | `process(wait=False)` | Açık projenin işlenmesini başlatır ve hemen geri döner. |
| `get_processing_status` | `get_status` | `state` (`idle`, `starting`, `processing` veya `finished`) ve ilerleme durumunu bildirir; işlem tamamlandığında, dışa aktarımlar doğrulanamadığında `summary`, `export_check_failure` ve görüntüler kaybolduğunda `image_failure` yazılır: diğer ürünler kaydedilir; çalıştırma başarısız olarak raporlanır. |
| `process_folder` | `process_folder` / `process_lattice_capture` | Bir klasör için proje oluşturur, içe aktarır, yapılandırır ve tek bir çağrıda çalıştırmayı başlatır (LATTICE yakalamaları için `lattice: true`). Bir çalıştırma devam ederken, herhangi bir şey oluşturmadan önce reddedilir ve arka uç da o sırada herhangi bir araçtan gelen proje değişikliğini reddeder. |
| `export_light_sensor` | `export_light_sensor` | Projenin ışık sensörü kayıtları için kalibre edilmiş `.daq` ve `.csv` dosyalarını yazar kayıtları için kalibre edilmiş `.daq` ve `.csv` dosyalarını yazar; görüntüleri işlemeden, önbellekte bulunmadığında MAPIR Cloud&#x27;dan fabrika kalibrasyonunu alır (bkz. [Işık Sensörü Kayıtları](#light-sensor-recordings--calibrated-daq--csv)). |
| `read_image_audit_tags` | `read_image_audit_tags` | İşlenmiş bir görüntünün Chloros denetim meta verilerini (kaynak, kalibrasyon, uygulanan aşamalar) okur. |

**Kameralar ve diziler**

| Araç | SDK çağrısı | Ne yapar |
| --- | --- | --- |
| `list_devices` | `list_cameras`, `list_arrays`, `list_daq_sensors` | Herhangi bir istemcide şu anda neyin bağlı olduğunu gösterir; her biri `connected_here` ile; ayrıca sunucuda çalışan işler. |
| `discover_devices` | `discover_lattice_cameras`, `discover_daq_sensors` | Bağlanabilecek kameralar ve ışık sensörleri. |
| `get_device_status` | `CameraSession.status`, `ArraySession.status` / `capability`, `DAQSensorSession.status` | Bir kameranın, dizinin (yetenekleri ve durumu dahil) veya ışık sensörünün canlı durumu. |
| `analyze_array_network` | `analyze_array_network` | Bu ana bilgisayar ve ağın önerilen bir dizi için taşıyabileceği yük. |
| `connect_camera` / `connect_array` | `connect_camera` / `connect_array` | Bir kamerayı veya senkronize bir diziyi (önce ana cihaz) bağlayın. |
| `connect_project_devices` | `open_project(...).connect_all` | Kaydedilmiş bir projenin hatırladığı her cihazı, ayarlarıyla birlikte bağlayın. |
| `disconnect_device` | `disconnect` / `force_disconnect` | Bir kamerayı, diziyi veya ışık sensörünü serbest bırakın (bkz. Davranış). Bir dizideki `keep_settings: true`, her kameranın ayarlarını kendi belleğine kaydeder; `clear_settings: true` ise bu belleği sıfırlar (sonucu `camera_memory`&#x27;i taşır). |
| `disconnect_project_devices`, `align_project_arrays`, `capture_project_devices` | `disconnect_all`, `align_arrays`, `capture_all` | Projenin cihazları, bir arada. |
| `read_camera_settings` / `set_camera_settings` | `CameraSession.read_nodes` / `set_settings` | Kamera ayarlarını, kolay ad veya GenICam düğüm adı ile kamera ayarlarını okuma veya yazma. |
| `set_array_frame_rate` | `ArraySession.set_target_fps` | Bir dizinin hızını sabitleme veya otomatik hıza geri döndürme. |
| `capture_camera` / `capture_array` | `CameraSession.capture` / `ArraySession.capture` | Tek bir çekim (`fastest` ve `wait_for_exposure`, `capture_fastest` ve sabit pozlamalı çekimdir). `output_dir` yoksa, açık projenin`captures` klasörüne kaydedilir ve dosyalar projeye eklenir (işleme işlemi sona erene kadar bekletilir); yanıtta `project_add` bunu belirtir. |
| `start_capture_run` | `ArraySession.continuous`, `capture_repeated` | Bir iş olarak sürekli veya aralıklı çekimler. |
| `start_array_recording` | `ArraySession.record` / `burst` | Birleştirilmiş dizinli video veya ham seri çekim, bir iş olarak. |
| `build_burst_video` | `ArraySession.build_video` | Bir seri çekimi, bir iş olarak kalibre edilmiş videoya yeniden işleyin. |
| `get_job_status` / `stop_job` | | Bir işi takip etme veya durdurma. |
| `get_camera_preview` | | Her kameranın canlı önizlemesi, bir görüntü olarak döndürülür. |
| `grab_frames` | `ArraySession.grab`, `CameraHandle.grab` | Kaydetmeden, bir işleme düzeyinde bir kare (veya senkronize bir set): düzlem başına istatistikler ve her birinin PNG dosyası. |
| `calibrate_array_alignment`, `get_array_alignment`, `clear_array_alignment`, `nudge_array_alignment`, `export_array_alignment`, `import_array_alignment` | `ArrayHandle.calibrate_alignment`, `alignment_status`, `clear_alignment`, `tweak_alignment`, `export_alignment`, `import_alignment` | Bir dizinin hizalaması. |

**Işık sensörleri**

| Araç | SDK çağrısı | Ne işe yarar |
| --- | --- | --- |
| `connect_light_sensor` | `connect_daq_sensor` | Bir DAQ-U, DAQ-M veya DAQ-E bağlayın (`cap_id`, kapasitans düzeltmesini ayarlar). |
| `get_light_sensor_reading` | `DAQSensorSession.latest` | En son okumalar: zaman damgası, doygunluk, spektrum aralığı ve tepe değeri ile dalga boyu aralıklarına göre ortalaması alınmış spektrum (her nokta için `full_spectrum`). |
| `set_light_sensor_streaming` | `stream_start` / `stream_stop` | Akışı başlatır veya durdurur. |
| `start_light_sensor_recording` / `stop_light_sensor_recording` | `record_start` / `record_stop` | Bir `.daq` dosyasını iş olarak kaydeder; `duration_s` bunu kendiliğinden durdurur. |
| `set_light_sensor_cap` | `DAQSensorSession.set_cap` | Kapak düzeltmesi (çıplak sensör için `none`). |
| `get_light_sensor_bindings` / `set_light_sensor_bindings` | `daq_bindings` / `set_daq_bindings` | Her bir kameranın ve dizinin yansıma değerini hangi sensör sağlar. |
| `get_light_sensor_band_presets` / `get_light_sensor_reflectance` | `daq_band_presets` / `light_sensor_reflectance` | Bant kümeleri ve iki akış sensörünün Ortam + Nesne yansıma değerleri ve indeksleri. |
| `get_light_sensor_device_info`, `set_light_sensor_network`, `set_light_sensor_auto_stream`, `enable_light_sensor_ptp`, `reboot_light_sensor`, `update_light_sensor_firmware` | `device_info`, `set_network`, `set_auto_stream`, `enable_ptp`, `reboot`, `update_firmware` | Bir DAQ-E&#x27;nin kendi ayarları, üniteye kaydedilir. |

**Kamera yardımcı programları** ([Kamera Yardımcı Programları](#camera-utilities))

| Araç | SDK çağrısı | Ne işe yarar |
| --- | --- | --- |
| `get_camera_color_settings` / `set_camera_color_settings` | `camera_color_profile`, `camera_color_adjust`, `camera_highlights` ve bunların ayarlayıcıları | Bir RGB kameranın ekran rengi: profil ve renk sıcaklığı, doygunluk / kontrast, parlak noktalar. |
| `run_camera_white_balance` / `set_camera_white_balance` | `camera_white_balance_once` / `set_camera_white_balance` | Tek çekim beyaz dengesi veya otomatik / kapalı / sabit oranlar. |
| `list_camera_presets` / `apply_camera_preset` | `camera_presets` / `apply_camera_preset` | Kamera ön ayarları. |
| `list_filter_models` | `camera_filter_models`, `camera_filter_model_info` | Filtre modelleri veya adlarına göre tek tek. |
| `get_camera_firmware_status` / `update_camera_firmware` | `camera_firmware_status` / `update_camera_firmware` | Yazılım sürümleri ve bir flaş (varsayılan olarak bir iş). |
| `get_camera_power_info` / `set_camera_power` | `camera_power_probe`, `sleep_camera`, `wake_camera` | Güç ve termal durum; uyku veya uyanık. |
| `calibrate_camera_with_target` | `calibrate_camera_with_target` | Görüntüdeki kalibrasyon hedefinden alınan katsayılar. |
| `get_network_adapters` / `fix_network_settings` | `network_adapters` / `fix_network_settings` | Kamera ağ bağdaştırıcıları, bir tahmin ve düzeltme (Yalnızca yönetici). |
| `apply_vignette_correction` | `apply_vignette_correction` | Bir resim klasöründe vinyet düzeltmesi. |
| `read_dls` | `read_dls` | Bir DLS okuması. |
| `probe_system` / `get_system_info` | `probe_system` / `system_info` | Ana bilgisayarın donanımı ve çalışma zamanı. |

**Geçici kameralar, hizalama profilleri ve indeksler** ([Geçici Diziler, Hizalama Profilleri ve İndeksler](#transient-arrays-alignment-profiles--indices)). Burada kameraları açan her araç, `keep_settings` / `clear_settings` (varsayılan değer false) değerini kullanır: bkz. [Kamera belleği](#camera-memory--keep_settings--clear_settings).

| Araç | SDK çağrısı | Ne yapar |
| --- | --- | --- |
| `start_camera_continuous` / `start_multi_camera_stream` | `start_camera_continuous` / `start_multi_camera_stream` | Bir kamerayı kaydeder veya bir iş olarak birkaç kamerayı senkronize olarak yayınlar (`duration_s` bunu kendiliğinden durdurur). |
| `capture_cameras_transient` | `capture_cameras_transient` | Bir dizi bağlamadan tek bir senkronize yakalama. |
| `test_array_sync` / `detect_array_master` | `test_array_sync` / `detect_array_master` | Tetikleyici senkronizasyon kontrolü ve senkronizasyon hattında hangi kameraların bulunduğu. |
| `analyze_array_models` | `analyze_array_models` | Bu modellerden oluşan bir dizinin bu ağda ne elde edeceği. |
| `calibrate_alignment_profile` | `calibrate_alignment_profile` | Bir hizalama profili dosyasını çözün ve kaydedin (`options` dosyası daha ayrıntılı çözücü seçeneklerini içerir). |
| `capture_aligned_multiband` / `start_aligned_stream` | `capture_aligned_multiband` / `start_aligned_stream` | Hizalanmış çok bantlı bir görüntü veya bir iş olarak bu görüntülerden oluşan bir akış. |
| `read_alignment_profile` / `reorder_alignment_profile` | `alignment_profile_info` / `reorder_alignment_profile` | Bir profil dosyasının içeriği; bant sırası ve etkinleştirilmiş kameralar. |
| `list_index_presets`, `compute_index`, `capture_live_index` | `index_catalog`, `compute_index`, `capture_live_index` | Dönüşüm ön ayarları ve gradyanlar; çok bantlı bir TIFF dosyası üzerinde bir dizin; tek adımda çekimden dizine aktarma. |

**Zaman senkronizasyonu, proje klasörü ve tarifler** ([PTP Grandmaster](#ptp-grandmaster--time_sync_status), [Proje Klasörü ve Tarifler](#project-folder--recipes))

| Araç | SDK çağrısı | Ne yapar |
| --- | --- | --- |
| `get_time_sync_status` | `time_sync_status`, `time_sync_peers`, `time_sync_cameras` | PTP grandmaster&#x27;ın durumu, eşleri ve her bir dizi kameranın PTP durumu. |
| `restart_time_sync` / `set_time_sync_priority` | `restart_time_sync` / `set_time_sync_priority` | Yeniden başlatın; önceliklerini ayarlayın. |
| `get_project_folder` / `set_project_folder` / `reset_project_folder` | `project_folder` / `set_project_folder` / `reset_project_folder` | Yeni projelerin oluşturulduğu klasör. |
| `run_project_recipe` | `run_project_recipe` | Kaydedilmiş bir projenin tarifini, günlüğüyle birlikte bir iş olarak çalıştırın. İlk olarak kontrol edilir: hatalı bir reçete reddedilir ve hiçbir işlem yapılmaz; bağlı olmayan diziler / sensörler hakkında uyarı verilir (bunları bağlamaz: önce `connect_project_devices`). `dry_run` kontrol sonucunu döndürür ve hiçbir işlem yapmaz. |

İşleme için kaydedilmiş bir projeyi açacak bir araç yoktur: her işleme işi `create_project` (veya `process_folder`) ile başlar.

### Gereksinimler

- **Aynı bilgisayara Chloros&#x27;un kurulu olması.** İşlemeyi Chloros arka ucu gerçekleştirir. Sunucu bunun yerine bir tünel üzerinden erişilen bir arka ucu da kullanabilir (`--backend-url` ile `--no-auto-start`, bkz. [Seçenekler](#options)).
- **O bilgisayarda oturum açılmış olması** gerekir; bu, Chloros uygulamasında veya `chloros-cli login` ile yapılabilir. Sunucu sizin adınıza oturum açamaz: oturum kapalıyken çağrılan bir araç, arka ucun gerekçesiyle birlikte &quot;Chloros&#x27;ta oturum açılmadı&quot; yanıtını verir ve asistan size bunu bildirir.
- **Herhangi bir ücretli Chloros+ kademesi (Copper veya üstü).** Sunucu SDK erişimine sahiptir ve ücretsiz Iron kademesinde bu erişim yoktur: ücretsiz kademede çağrılan bir araç, arka uçtaki nedeni belirterek &quot;Bu Chloros planında mevcut değil&quot; yanıtını verir.
- SDK ile birlikte Python 3.7 veya daha yeni bir sürüm ve yerel (stdio) sunucuları başlatan bir MCP istemcisi.

### Çalışma Şekli

- **Arka uç.** Sunucuyu başlatmak veya araçlarını listelemek hiçbir şeyi başlatmaz; ilk araç çağrısı bağlantı kurar. Chloros uygulaması (veya Linux arka uç hizmeti) çalışıyorsa, sunucu bu arka ucu kullanır ve çalışır durumda bırakır. Çalışan bir arka uç yoksa, sunucu yüklü olanı penceresiz olarak başlatır. Sunucu kapandığında (istemciden çıktığınızda veya sunucu yeniden başlatıldığında), başlattığı yakalama işlerini ve ışık sensörü kayıtlarını durdurur ve bağlandığı cihazların bağlantısını keser; arka uç çalışmaya devam eder, böylece tamamlanmamış bir işleme süreci sonuna kadar devam eder ve 30 dakika boyunca hiç kimse kullanmadığında kendi kendine durur. `--no-auto-start` ile sunucu hiçbir zaman bir arka uç başlatmaz: çalışır durumda olmayan bir arka uç varken yapılan bir araç çağrısı, sizden Chloros uygulamasını açmanızı ister ve bunu yaptıktan sonraki çağrı bağlantı kurar.
- **Tek bir açık proje.** Arka uçta tek bir açık proje bulunur; bu proje, Chloros uygulaması açıkken uygulama ile paylaşılır; bu nedenle, asistan çalışırken uygulamada başka bir proje üzerinde çalışmayın.
- **Yalnızca aktarılan ayarlar değişir.** `configure_processing`, kendisine verilmeyen her ayarı kaydedilmiş değerinde bırakır (SDK&#x27;nın `configure()` komutu, debayer, vinyet düzeltme, yansıma kalibrasyonu, PPK ve dışa aktarma biçimini, bunlar aktarılmadığında varsayılan değerlerine sıfırlar; sunucu bunları projeden doldurur). Projenin kaydedilmiş ayarlarını okuyamazsa, beş ayarın tümü belirtilmedikçe hiçbir şeyi değiştirmez ve bunu bildirir. `indices`, projenin dizin listesini değiştirir. Sonuç, `settings_sent` olarak gönderilenleri listeler ve `warnings` altında, kameranın bantlarının oluşturamadığı dizinleri adlandırır.
- **İşleme, &quot;başlat-sonra-sor&quot; şeklindedir.** `start_processing`, işlem başlar başlamaz geri döner; bu işlem dakikalar ila saatler sürer, ve yardımcı komut bunu `get_processing_status` ile takip eder. `state`, `idle` (işleme yok), `starting` (arka uç, çalışmayı henüz raporlamamıştır; bu durum 20 saniyeye kadar sürebilir), `processing` veya `finished` olabilir; bunlara çalışmanın [özet](#post-run-summary--hints): neyin dışa aktarıldığı ve beklenenden daha az veri aktarıldığında (görüntü yok, kalibrasyon hedefi bulunamadı) ipuçları. Ürünler, uygulamada olduğu gibi proje klasörünün altına yazılır. `start_processing`, bir çalıştırma devam ederken reddedilir ve yanıt vermeyi durduran bir arka uç, tamamlanmış bir çalıştırma olarak değil, bir hata olarak rapor edilir.
- **Asistanın gördükleri.** SDK&#x27;nın uyarıları, `warnings` altında araç sonucuna yansıtılır; hatalar (oturum kapatıldı, plan, geçersiz yol) ise okunup size iletilebilecek hata metni olarak görünür. 20 öğeden uzun bir liste ilk 20 öğeye kısaltılır; tam uzunluğu ise `<key>_total` olarak gösterilir. Yollar mutlak olmalıdır.
- **Cihazlar paylaşılır.** Kameralar, diziler ve ışık sensörleri arka uçta bulunur; bu sayede asistan, Chloros uygulamasına bağlı bir cihazı kontrol edebilir ve yaptığı değişiklikler uygulamada gösterilir. `disconnect_device`, sunucunun bağlandığı cihazı bildirir; başka bir istemcinin (uygulama, `chloros-cli`, bir komut dosyası, başka bir `chloros-mcp`) yalnızca `force: true` ile serbest bırakılır; bu, SDK&#x27;nın `force_disconnect`&#x27;sidir ve arka uç bunu kimin bağladığını biliyorsa reddetme isimleri belirtilir; arka uçta hiç bulunmayan bir cihaz, bağlı değil olarak rapor edilir (`released: false`, `connected: false`, serbest bırakılacak bir şey yoktur). Arka uç tarafından çalıştırılan bir kayıt veya akış, `get_job_status` görünümünü `counts` — `frames_written`, `frames_dropped`, `frames_failed`, `drop_reason` (çoklu kamera akışı için kare grupları) — ve tek satırlık bir `summary` (&quot;220 kare yazıldı, 55 atıldı, 0 hata: ...&quot;) mesajı; işlem devam ederken ve bittiğinde nihai rakamlar; başka bir istemcinin listelediği (`other_clients_jobs`) de aynı verileri içerir (`list_cameras()`, `list_arrays()` ve `list_daq_sensors()` ise her bir cihazın`connected_by`: `client`, `label`, `name`, `at`). Her bir `chloros-mcp`, arka uca kendisini `chloros-mcp (pid N)` olarak bildirir (`chloros_sdk.connect.CLIENT_LABEL`, `X-Chloros-Client-Label` olarak gönderilir; herhangi bir SDK komut dosyası bunu ayarlayabilir). Başka bir istemcinin ışık sensörü kaydını durdurmak da `force`&#x27;i gerektirir.
- **Uzun süreli iş, bir görevdir.** `start_capture_run`, `start_array_recording`, `build_burst_video`, `start_light_sensor_recording`, `start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`, `run_project_recipe`, ve `background` için istenen herhangi bir yakalama, çözüm veya indeks, hemen bir `job_id` döndürür; `get_job_status` bunu takip eder ve `stop_job` . Bir çalıştırma, `max_captures`, `duration_s` (veya bir patlama için `max_frames`) komutunu vermediğiniz sürece durdurulana kadar devam ederkomutunu vermedikçe devam eder; sürücüde yeterli boş alan kalmadığında `min_free_gb` başlatılmayı reddeder. Siz bir sınır belirlemedikçe herhangi bir sınır uygulanmaz. Arka uç tarafından yürütülen bir kayıt veya akış (`start_camera_continuous`, `start_multi_camera_stream`, `start_aligned_stream`) arka uçun iş kimliğini saklar; böylece daha sonra başlatılan başka bir `chloros-mcp` bu kimliğe göre onu bulur: `get_job_status` onu takip eder (ve `other_clients_jobs`, `GET /api/camera/cli-jobs` gibi işleri listeler; her biri `started_by` ile devam eder), ve `stop_job`, onu yalnızca `force: true` ile durdurur. Diğer tüm işler (bir yakalama çalışması, bir dizi kaydı veya patlama, bir arka plan yakalaması) onu başlatan `chloros-mcp`&#x27;te çalışır ve bunu yalnızca o sunucu bilir: bunu isteyen başka bir sunucu bunu belirtir. Durdurulan bir iş, kuyruğunu yazarken `stopping` değerini okur, ardından son sayımlarıyla birlikte `finished` değerini okur, 10 dakika boyunca ve herhangi bir `chloros-mcp`&#x27;ten: işlemi başlatan sunucu, başka bir sunucunun zorla durdurulmasını da aynı şekilde görür. `get_job_status`, bir durdurma işlemini asla beklemez: bir durdurma sırasında (kendi `stop_job`&#x27;i, başka bir istemcininya da kendi kendine sona eren bir işin kameralarını serbest bırakan durdurma) hemen `stopping` ve canlı sayımlarla yanıt verir; zaten durdurulmakta olan bir işin `stop_job`&#x27;i de bunu bildirir. Sona eren bir iş, nedenini `stop_reason`&#x27;te nedenini belirtir: kendi nedeni (`no_frames`, `disk_full`, `member_missing`, veya bir dizi sürekli çalıştırma için `count`, `duration`, `array_gone`), `limit reached` ise yalnızca sayımları `count` (veya bir patlamanın `max_frames`, bir kaydın`duration_s`) değerine ulaştığında, `error`, `stopped` (`stopped_by` ile) bir istemci tarafından durdurulduğunda, aksi takdirde `finished`.
- **Yakalamaların nereye gittiği.** `output_dir` belirtilmedikçe, uygulamanın bunları kaydettiği açık projenin `captures` klasörüne.
- **Görüntüler.** `get_camera_preview` ve `grab_frames`, JSON verilerinin yanı sıra asistanın inceleyebileceği görüntüler de döndürür. `grab_frames` görüntüleri, görüntüleme amacıyla kontrastı ayarlanmıştır; istatistikleri ise gerçek değerlerdir.
- **Sunucu kapandığında**, başlattığı işler durdurulur ve başlattığı ışık sensörü kaydı kapatılır. Sunucu arka ucu başlatmışsa, arka ucu durdurmadan önce bağlandığı cihazların bağlantısını da keser; uygulamanın arka ucunda ise bu cihazlar bağlı kalır.
- **Protokol.** Yalnızca stdio (HTTP uç noktası yok), yalnızca araçlar (kaynaklar veya istemler yok), metin ve görüntü sonuçları, MCP protokol sürümleri 2024-11-05 ile 2025-11-25 arası.**Neler hariç tutulmuştur.** Oturum açma veya kapatma (hesap size aittir; sunucu oturum açamaz) ve sunucunun başlatmadığı bir arka ucun durdurulması. Bu SDK&#x27;nın işleme ve donanımla ilgili yaptığı diğer her şey için bir araç mevcuttur. (Bir DAQ-E&#x27;nin provizyonu — adı, kimlik doğrulama jetonu, OTA şifresi, imza gereksinimi, fabrika ayarlarına sıfırlama — ve doğrudan DAQ sunucuları yalnızca `chloros-cli`&#x27;e aittir; bunlar da SDK&#x27;da mevcuttur: yanlış bir ad veya belirteç, her Chloros kurulumunu cihazdan kilitler.)

---

## Arka Uç Otomatik Başlatma

Akıllı bağlantı giriş noktaları — `connect_camera`, `connect_array`, `connect_daq_sensor`, `discover_lattice_cameras` ve `discover_daq_sensors` — arka ucun `127.0.0.1:5000` (smart-connect arayüzünün varsayılan URL&#x27;si) üzerinde dinleme halinde olduğunu varsayar. GUI veya CLI zaten çalışıyorsa, arka uç zaten çalışır durumdadır. Basit bir komut dosyasından çalıştırıldığında, bu istemci çalışmıyor olabilir — bu nedenle bu işlevler, ilk çağrılmadan önce **paket içindeki arka uç ikili dosyasını otomatik olarak başlatır** (`ChlorosLocal`&#x27;in yaptığı gibi penceresiz olarak) ve ardından arka ucun çalışır hale gelmesi için en fazla 60 saniye bekler. `list_cameras()`, `list_arrays()`, `list_daq_sensors()`, `attach_array()` ve `analyze_array_network()` ise hiçbir zaman bir arka uç başlatmaz.

Kurallar:

- **Yalnızca yerel bir URL&#x27;si oluşturulur.** `backend_url`&#x27;in `localhost` / `127.0.0.1` / `[::1]` adreslerine yönlendiren bir `backend_url` uygun kabul edilir; diğer tüm ana bilgisayarlar başka birine ait olduğu varsayılır ve asla başlatılmaz.
- **Yanıt vermeden önce sonlanan bir arka uç, beklemeyi sonlandırır.** Çağrı, 60 saniyeyi beklemek yerine, çıkış kodu, çalıştırdığı komut ve arka ucun yazdırdığı son satırlarla birlikte anında sonlandırılır. Çıkış kodu 98 bir istisnadır: başka bir arka uç, bağlantı noktasını veya kilit dosyasını (Chloros uygulamasının, arka uç hizmetinin veya hâlâ başlatılmakta olan başka bir komut dosyasının) zaten elinde tutmaktadır; bu nedenle çağrı, o arka ucun yanıt vermesini beklemeye devam eder. `ChlorosLocal` de aynı şekilde davranır.
- **Arka uç, yeniden kullanılmak üzere çalışır durumda bırakılır** (CLI ile aynı) — komut dosyanız sonlandığında otomatik olarak kapatılmaz. Komut dosyasını yeniden çalıştırdığınızda, çalışır durumdaki arka uç yeniden kullanılır.
- Bu çağrılardan herhangi birinde **`auto_start_backend=False`** ile devre dışı bırakın (ör. uzak bir arka uca yönlendirdiğinizde veya arka ucun yaşam döngüsünü kendiniz yönettiğinizde).

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

Paket içindeki ikili dosya bulunamazsa veya başlatılamazsa, sonraki HTTP çağrısı, basit bir bağlantı reddi izi yerine, eyleme geçirilebilir, **platform farkındalıklı** bir `ChlorosConnectError` hatası verir — Windows’ta sizi masaüstü uygulamasına veya bir `chloros-cli` komutuna yönlendirir; Linux’ta (GUI yok) sizi bir `chloros-cli` komutuna veya `.deb`’e yönlendirir.

---

## Ortam ve Başlıklar

SDK, her arka uç HTTP çağrısını `X-Chloros-Client: sdk` ile işaretler. Arka uç, GUI ücretsiz kademesi yolunu kullanmak yerine SDK/CLI lisans kurallarını uygular (giriş **ve** ücretli bir Chloros+ planı gereklidir). Bu, içe aktarma sırasında otomatik olarak ayarlanır — sizin herhangi bir işlem yapmanız gerekmez.

Başlık, URL&#x27;sinde `localhost` veya `127.0.0.1` bulunan her `requests` / `urllib` çağrısına eklenir; URL&#x27;sinde `localhost` veya `127.0.0.1` bulunan, herhangi bir bağlantı noktasında — kendi yerel hizmetleriniz dahil — ve sizin kendiniz belirlediğiniz değeri değiştirir. Diğer ana bilgisayarlara yapılan çağrılar etkilenmez.

`backend_url=` değerini geçirerek arka uç URL&#x27;sini geçersiz kılın (`ChlorosLocal`, `backend_url=` veya `api_url=` değerlerini kabul eder; `process_folder()` ve `process_lattice_capture()` ise yalnızca `api_url=` değerini kabul eder):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(Yüklü bir arka uç yalnızca döngü geri bağlantısında dinleme yapar, bu nedenle bir LAN `backend_url` ona erişemez; tünel şablonu için bkz. [Uzak Arka Uç Modu](#remote-backend-mode-pip-only-host-via-tunnel) bölümüne bakın.)

---

## Sürümleme ve Uyumluluk

- SDK sürümü, `chloros_sdk.__version__` olarak sunulur.
- SDK, davranışını pakete dahil arka uç sürümüne bağlar. Eski bir SDK ile yeni bir arka ucun bir arada kullanılması genellikle sorunsuz çalışır (ileriye dönük uyumlu uç noktalar), ancak yeni bir SDK ile eski bir arka ucun bir arada kullanılması, yeni uç noktalarda `404` hatalarına yol açabilir — masaüstü uygulamasını buna uygun şekilde güncelleyin.
- Smart-connect arayüzü (`connect_camera` / `connect_array` / `connect_daq_sensor`) ve ağ analizi uç noktası, sabit JSON şemaları döndürür; yeni alanlar ek olarak eklenir.

---

## Sorun Giderme İpuçları

- **`ChlorosAuthenticationError: Login required`** (`ChlorosLocal`&#x27;ten kaynaklanır; Smart-Connect çağrıları aynı nedenden dolayı `ChlorosConnectError: Login required. …` hatasını verir) → Bu makinede `chloros-cli login EMAIL PASSWORD` komutunu bir kez çalıştırın veya Chloros masaüstü uygulaması üzerinden oturum açın.
- **`ChlorosProcessingError: Failed to start processing: … The project folder is too deep for this computer …`** (veya `create_project()`&#x27;ten kaynaklanan `Failed to create project: …`) → Bu bilgisayarda Windows uzun yol özelliği devre dışıdır (Windows varsayılan ayarı), bu nedenle hiçbir yol 260 karakteri aşamaz ve ürünler proje klasörünün yaklaşık 130 karakter altında kalır. Hiçbir işlem gerçekleştirilemedi. Mesaj, sığabilecek en uzun proje klasörünü gösterir: daha kısa bir klasör kullanın veya Windows uzun yollarını etkinleştirip Chloros&#x27;u yeniden başlatın. Windows yükleyicisi bunu &quot;Windows uzun yollarını etkinleştir&quot; seçeneği olarak sunar; bu seçenek varsayılan olarak işaretlidir; aksi takdirde Grup İlkesi&#x27;ni kullanın: *Bilgisayar Yapılandırması &gt; Yönetim Şablonları &gt; Sistem &gt; Dosya Sistemi &gt; Win32 uzun yollarını etkinleştir*.
- **`ChlorosConnectError: No Chloros backend is running …`** → Akıllı bağlantı, yerel bir arka ucun otomatik olarak başlatılmasını çağırır; bu nedenle bu hata, yalnızca pakete dahil edilmiş ikili dosya bulunamadığında veya başlatılamadığında (ör. masaüstü paketi olmayan, yalnızca pip-only ana bilgisayar gibi). Mesaj platforma özeldir: Windows&#x27;da masaüstü uygulamasını açın veya herhangi bir `chloros-cli` komutunu çalıştırın; Linux&#x27;ta bir `chloros-cli` komutu çalıştırın (GUI yoktur) veya `.deb`&#x27;i yükleyin. Uzak arka uç için `backend_url=` (ve `auto_start_backend=False`) değerlerini aktarın.
- **`CAMERA_AVAILABLE == False`** içe aktarma sırasında → `lattice_sdk` yüklenemedi: Arena SDK çalışma zamanı veya OpenCV (`cv2`), bu Python sürümünde içe aktarılamaz. Arka uç tabanlı yüzey (`connect_*`, bir arka uç üzerinden yansıtma yapar, `ChlorosLocal`) hâlâ çalışır.
- **Dizi, tam sensörde bağlanıyor ancak kare hızı düşük**→ Tasarım gereği: otomatik mod, tam sensörü korur ve kare hızının kablonun taşıyabileceği düzeye düşmesine izin verir. Daha fazla kare sayısı için `binning=2` / `4` veya daha küçük bir `width` / `height` kullanın ya da jumbo çerçeveleri etkinleştirin / bağlantıyı yükseltin. Binning ve ROI, toplam aşırı abonelik sorununu**çözmez** (`oversubscribed: true`, fps alanları 0): kablo için çok fazla kamera olması sorunu, binning/ROI ile çözülemez — kamera sayısını azaltın, jumbo çerçeveleri etkinleştirin veya daha hızlı bir NIC&#x27;ye geçin ([Aşırı Abonelik](#over-subscription-the-per-cam-floor) bölümüne bakın).
- **`analyze_array_network()`, NIC alıcı halkasının çok küçük olduğunu (~0,26 MB) bildiriyor / &quot;FRAMES WILL DROP&quot; uyarısıyla bağlantı kapıları** → Ana bilgisayar NIC&#x27;sinin alıcı halkası varsayılan değerindedir (genellikle bir NIC sürücüsü güncellemesinden sonra 32&#x27;ye sıfırlanır). Realtek USB 10GbE adaptöründe `ReceiveBufferLen=256` ve `PendingReceives=64` (yükseltilmiş) ayarlarını yapın, ardından arka ucunu yeniden başlatarak halkayı yeniden okumasını sağlayın. Tam prosedür: [CLI Referansı → Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Ana bilgisayar yeniden başlatma/kapatma sırasında donuyor, daha sonra WMI&#x27;da `Invalid class` hataları oluşuyor / Ağ kartı etkinleştirilmiyor** → Güncel olmayan USB 10GbE sürücüsü, `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`) hatasına neden oluyor. Adaptör sürücüsünü güncel bir sürüme (≥ 2026) güncelleyin ve alıcı halka ayarlarını yeniden uygulayın. Bkz. [CLI Referansı → Ana Bilgisayar NIC Kurulumu ve Ayarlaması](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **HTTP 503 &quot;Yeni senkronize çerçeve grubu yok&quot; hatası nedeniyle dizi yakalama başarısız oluyor** → Dizi, bekleme süresi içinde yeni bir çerçeve grubu yayınlamadı. `arr.status()`: `stalled: True` (veya `streaming: False`) ile karşılaştırın; burada `actual_fps` değeri 0 ise, yakalama döngüsünün yayınlamayı durdurduğu anlamına gelir — diziyle bağlantıyı kesip yeniden bağlanın. `stalled` False ve `actual_fps` 0&#x27;dan büyükse, yeniden deneyin. `connect_array()`&#x27;ten hemen sonra, bir yakalama işlemi ilk grup için zaten 20 saniyeye kadar beklemektedir.
- **`status()`, `link_speed_mbps: 4294` değerini gösterir** → Windows, adaptörün hızını okuyamadı; bu durum USB Ethernet adaptörlerinde yaygındır ve bir hata değildir. Bkz. [Beklenen Çerçeve Hızları](#expected-frame-rates).
- **`update_firmware()`, `device never connected back on TCP <port>` ile başarısız olur** → Bir güvenlik duvarı, DAQ-E&#x27;nin bu bilgisayara geri bağlantısını engelledi. Windows yükleyicisi bu kuralı ekler (bkz. [DAQ-E Aygıt Ayarları](#daq-e-device-settings)); başka bir güvenlik duvarı kullanılıyorsa, sensörün alt ağından arka uca gelen TCP trafiğine izin verin.
- **Yansıma reddedildi** → Mutlak ölçekli yansıma değeri için canlı bir DAQ&#x27;nın kameraya (veya diziye) bağlanması gerekir. GUI aracılığıyla bağlayın veya eşleştirilmiş sensör gerektirmeyen `processing="radiance"` (W/m²/sr/nm) komutunu kullanın.
- **`smart=True` yakalama işlemi beklenenden uzun sürüyor** → AE yakınsaması, sahnenin dinamiklerine bağlıdır. `ArrayHandle.capture_smart()` ile, `exposure_tolerance_pct`&#x27;i genişletin, `stability_window_s`&#x27;i kısaltın veya `settle_timeout_s`&#x27;i düşürün; böylece daha hızlı ve daha az sabit bir tetikleme elde edersiniz.

---

## Ayrıca bakınız

- [CLI Referansı](cli-reference.md) — her CLI alt komutu, bir SDK çağrısına karşılık gelir.
- [DAQ Işık Sensörleri](https://mapir.gitbook.io/chloros/daq-light-sensors) — Chloros kılavuzunda — sensörler, sınırları ve kalibre edilmiş aralıkları, kayıt, DAQ-E ağ bağlantısı.
- [LATTICE Kameralar](https://mapir.gitbook.io/chloros/lattice-cameras/lattice) (Chloros kılavuzunda) — bağlama, kamera ayarları, çekim modları, diziler.
- Çevrimiçi belgeler: `https://mapir.gitbook.io/chloros/api-python-sdk`
