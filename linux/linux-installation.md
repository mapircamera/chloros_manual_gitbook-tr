# Linux Kurulumu

Chloros, Linux için CLI’yi, arka uç sunucusunu ve Python SDK’yi kuran `.deb` paketleri olarak dağıtılmaktadır (`python3` sistemi için, ayrıca virtualenv’ler için sürüm uyumlu bir wheel dosyası da bulunmaktadır).

Paket dosya adları sürüm ve mimari bilgilerini içerir: x86_64 için `chloros_1.3.0_amd64.deb`, ve arm64 (NVIDIA Jetson, JetPack 6) için `chloros_1.3.0_arm64_jp6.deb`. Aşağıdaki komutlarda, gerçekte indirdiğiniz dosyayı kullanın.

{% hint style="info" %}
**

1.3.0 sürümündeki yenilik: programlar sıkıştırılmamış olarak dağıtılmaktadır.** Arka uç ve CLI, tek tek kendiliğinden açılan dosyalar yerine `/usr/lib/chloros/backend/` ve `/usr/lib/chloros/cli/` adlı klasörler olarak kurulur. tek bir kendiliğinden açılan dosya yerine. Artık her başlatıldığında geçici bir klasöre kendiliğinden açılmadıkları için çok daha hızlı başlıyorlar (x86_64'te arka uç eskiden her başlatmada açılmak için yaklaşık 100 saniye harcıyordu) ve artık `/tmp` içinde birkaç GB boş alana ihtiyaç duymuyorlar. amd64 `.deb` de eski boyutunun yaklaşık yarısı kadar.
{% endhint %}

***

## Linux amd64 (x86_64)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Dağıtım** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **İşlemci** | x86_64 (Intel/AMD) | Intel Core i7 veya üstü |
| **Bellek (RAM)** | 8 GB | 16 GB veya daha fazla |
| **Grafik kartı** | Yok (CPU ile işleme) | 4 GB+ VRAM'e sahip NVIDIA GPU (12 GB+ ile `GPU_PARALLEL` kilidi açılır, 7 GB+ ile Tek Görüntü yolunda Texture Aware devre dışı kalır) |
| **Depolama** | 2 GB boş alan | 10 GB+ boş alana sahip SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

> **Ubuntu 20.04 ve Debian 11 desteklenmemektedir.** `.deb`'in bağımlılık listesi,
> Chloros arka ucunun gerçekte bağlandığı öğelerden türetilmiştir ve buna
> focal ve bullseye sürümlerinde bulunan glibc 2.31'den daha yeni bir `libc6` içerir; bu nedenle `apt`,
> kurulumun daha sonra çalışma zamanında başarısız olmasına izin vermek yerine, kurulumu doğrudan reddeder.

### Kurulum

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**`dpkg -i` yerine `apt install ./…`'i kullanın.** `apt`, paketin bağımlılıklarını çözer ve ayrıca önerdiği öğeleri de yükler: Python SDK'nin gereksinimleri, `python3-numpy` ve `python3-requests`. `dpkg -i` ise ikisini de yüklemez. CLI ve arka uç kendi kendine yeterlidir ve bunlar olmadan çalışır, ancak `import chloros_sdk` bunlara ihtiyaç duyar; kurulum çıktısı eksik olanları listeler. `./`'i koruyun, böylece `apt`, bu isimde bir paket aramak yerine dosyayı yükleyebilir. Paket ayrıca, birlikte gelen OpenCV tarafından yüklenen ve minimal bir sunucu görüntüsünde bulunmayabilecek olan `libgl1`'i de bildirir.
{% endhint %}

Kurulumu doğrulayın:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Platform** | JetPack 6 yüklü NVIDIA Jetson | Jetson Orin NX 16 GB veya AGX Orin |
| **JetPack** | JetPack 6.x | En son JetPack 6 |
| **Bellek (RAM)** | 8 GB (GPU/CPU paylaşımlı) | 16 GB+ paylaşımlı (paralel işleme için eşik değer 12 GB+'dır) |
| **Depolama** | 2GB boş alan | 10GB+ boş alana sahip NVMe SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

### Kurulum

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

amd64 `.deb` ile aynı düzen (Python SDK, alım tamponları ve arka uç hizmetleri dahil), **JetPack 6** (L4T R36) üzerinde Jetson Orin / Orin NX / Orin Nano için. PyTorch, JetPack 6'nın Orin için CUDA derlemesidir; bu nedenle GPU işleme ve Texture Aware debayer, Jetson'ın GPU'sunda çalışır. Paket, CUDA'yı kendisi içermez: JetPack 6'nın yüklediği CUDA ve cuDNN kütüphanelerini kullanır (bu kütüphanelerin bulunmadığı bir görüntüde `sudo apt install nvidia-jetpack`). CUDA GPU'sunun kullanılamadığı durumlarda, işleme CPU üzerinde gerçekleştirilir ve `--debayer texture-aware`, Standard debayer'ı kullanır (arka uç, bunun nedenini açıklayan bir `[DEBAYER] … needs a CUDA GPU …` satırı günlüğüne kaydeder). Jetson'un bellek, termal ve sahada kullanım davranışları hakkında bilgi için [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) bölümüne bakın.

***

## Python SDK Kurulumu (Tüm Linux Sürümleri)**`.deb`, SDK'yi sizin için kurar.** Her iki paket de (amd64 ve Jetson) bunu sistem için `python3` kurar; bu sayede `import chloros_sdk`, kurulumdan hemen sonra pip ve ağ bağlantısı olmadan çalışır. Paketleri (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` ve proje yükleyicileri) `/usr/lib/python3/dist-packages`'e eklenir; bu paket, paketin sahibi olarak işlev görür: bir yükseltme bunları değiştirir ve paketin kaldırılmasıyla birlikte bunlar da kaldırılır. Sürüm, her zaman kurduğunuz CLI ve arka uç ile eşleşir.

SDK'nin kendi gereksinimleri olan `python3-numpy` ve `python3-requests`, **önerilir, zorunlu değildir** `sudo apt install ./chloros_<version>_<arch>.deb` bunları yükler, `dpkg -i` ise yüklemez. Yükleme çıktısı, `python3`'in içe aktardığı SDK sürümünü, eksik olan tüm gereksinimleri ve `python3`'in yolunda, paketin kopyasını gölgeleyen başka bir yerdeki `chloros_sdk`'leri (örneğin, daha önce `pip install --user` ile yüklenmiş olanlar gibi) bildirir.

Sistemin paketlerini görmeyen **bir virtualenv** içinde, pakete dahil olan wheel dosyasını buraya yükleyin (tamamen çevrimdışı olarak):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**PyPI'dan** — yalnızca Chloros paketi yüklü olmayan ve SDK'si başka bir makinedeki arka ucu çalıştıran makineler için (bkz. [Uzak Arka Uç Modu](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

Pip yüklemesi için isteğe bağlı ekler (bir virtualenv veya PyPI):

| Ek | Komut | Eklenenler |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | Canlı ilerleme akışı için `sseclient-py` |
| `camera` | `pip install chloros-sdk[camera]` | BLE (DAQ-M) aktarımı için `bleak` |

SDK'yi doğrulayın:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Python SDK, `.deb`'in kurduğu arka uçla yerel bir HTTP API (`http://127.0.0.1:5000`) üzerinden iletişim kurar. `ChlorosLocal` ve bağlantı yardımcıları, gerektiğinde arka ucu otomatik olarak başlatır; kaydedilmiş bir projenin `connect_all()`'i ise bunu asla yapmaz, bu nedenle başsız bir makinede arka ucu bir [hizmet](#always-on-ptp-for-headless-hosts) olarak çalıştırınolarak çalıştırın. Her zaman `localhost` yerine tam IPv4 adresini kullanın — `localhost`, `::1`'e çözümlenebilir ve istek başına yaklaşık iki saniye sürebilir.
{% endhint %}

***

## İlk Kurulum

### 1. Giriş Yapın

CLI ve SDK erişimi, sunucu tarafında zorunlu kılınan ücretli bir Chloros+ kademesi (**Copper** veya üstü) gerektirir: oturumu kapatılmış bir çağrı yapan kullanıcıya `401 AUTH_REQUIRED`, ücretsiz kademedeki (Iron) bir çağrı yapan kullanıcıya ise `403 PLAN_UPGRADE_REQUIRED` verilir.

```bash
chloros-cli login your@email.com 'your-password'
```

Kimlik bilgileri `~/.chloros/user_session.json`'te önbelleğe alınır.

{% hint style="info" %}
**Yükseltme işlemlerinde oturum açma bilgilerinizi korursunuz; kaldırma işlemi ise bunları siler.** Mevcut bir yüklemenin üzerine daha yeni bir `.deb` sürümü yüklemek (`chloros-cli update` aracılığıyla da dahil), `~/.chloros/user_session.json`'i ve önbelleğe alınmış lisansı korur; böylece başlıksız CLI/SDK işleri çalışmaya devam eder. Paketi (`sudo apt remove chloros`) kaldırmak, makinedeki her kullanıcı için kaydedilmiş oturum açma bilgilerini ve lisans önbelleğini siler; bu nedenle yeni bir kurulum için tekrar `chloros-cli login` gerekir.
{% endhint %}

### 2. Lisans Durumunuzu Kontrol Edin

```bash
chloros-cli status
```

`chloros-cli status` her seviyede (ücretsiz dahil) çalışır; böylece erişimin neden mevcut olup olmadığını her zaman görebilirsiniz.

### 3. Sistem Tanılama İşlemini Çalıştırın

```bash
chloros-cli selftest
```

Yedi kontrol sırayla çalıştırılır ve bunlardan herhangi biri başarısız olursa komut sıfırdan farklı bir değerle sonlanır:

| # | Kontrol | Neyi doğrular |
| --- | --- | --- |
| 1 | **Sürüm** | CLI, sürümünü bildirir (`v1.3.0`). |
| 2 | **Bağlantı Noktası Kullanılabilir** | 5000 numaralı bağlantı noktası boş *veya* halihazırda çalışır durumdaki bir Chloros arka ucu tarafından yanıtlanıyor (bu, başarılı sayılır). |
| 3 | **Arka Uç Başlatma** | Arka uç ikili dosyası başlatılır. |
| 4 | **API testi (`/api/test`)** | Arka uç, `status: ok` yanıtını verir. |
| 5 | **Sistem bilgisi** | `/api/system-info`'ten `GPU: <name>, CUDA: <bool>, PyTorch: <version>` çıktısı verir. |
| 6 | **Gürültü giderici modeller** | `*.pth.enc` modellerini bulur (Linux'te: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Gürültü Giderici**| Texture Aware aslında kullanılabilir — CUDA**ve** en az bir model dosyası gerektirir. |

Çalıştırma, `N/7 checks passed` ile sona erer ve hataları adlarına göre listeler.

### 4. İlk Veri Kümenizi İşleyin

```bash
chloros-cli process ~/datasets/flight001
```

***

## Dosyalar ve Dizinler

### Kullanıcı Başına

Chloros, kimlik bilgilerini ve CLI yapılandırmasını tek bir platformlar arası dizinde, **`~/.chloros/`** (Windows'ta `%USERPROFILE%\.chloros\`). Buna karşılık, Linux'a özgü iki önbellek XDG kurallarına uyar — bunlar, ayarlandığında `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` adlarını kullanır.

| Yol | Amaç |
| --- | --- |
| `~/.chloros/user_session.json` | `chloros-cli login` tarafından yazılan oturum önbelleği (paket güncellemeleri sırasında korunur; paket kaldırıldığında temizlenir) |
| `~/.chloros/working_directory.txt` | Varsayılan proje klasörü geçersiz kılma (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI dil tercihi (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Windows GUI ile paylaşılan dil ayarı — buradaki bir `language`, `cli_language.json`'e göre önceliklidir |
| `~/.chloros/update_cache.json` | Linux/Jetson başlangıç güncelleme kontrolü için bir saatlik önbellek |
| `~/.chloros/backend.log` | Arka uç, CLI tarafından başlatıldığında arka uç günlüğü |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Seri numarası ve paket hash'ine göre anahtarlanmış, kamera başına önbelleğe alınmış LATTICE kalibrasyon paketleri |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | DAQ kapak düzeltme profilleri için isteğe bağlı kullanıcı geçersiz kılmaları |
| `~/.config/chloros/system_config.json` | Dinamik Hesaplama Uyumlaştırma'dan önbelleğe alınmış donanım profili — yeni bir donanım algılamasını zorlamak için silin |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Arka uç sunucu günlükleri, her başlatma için bir dosya |
| `~/Documents/MAPIR/Chloros Projects/` | Geçersiz kılma ayarlanmadığında varsayılan proje klasörü |

### Sistem Genelinde

| Yol | Amaç |
| --- | --- |
| `/usr/bin/chloros-cli` | Sarmalayıcı komut dosyası — paketlenmiş yerel kütüphaneler için `LD_LIBRARY_PATH`'i ayarlar (ve bu klasör mevcut olduğunda `TMPDIR` değerini `/mnt/ssd/tmp` olarak ayarlar), ardından gerçek ikili dosyayı çalıştırır |
| `/usr/bin/chloros-backend` | Sarma betiği — arka uç için de aynısı geçerlidir; bunu yüklü bir üretim derlemesi olarak işaretler |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | Derlenmiş CLI ve arka uç, açılmış halde kurulur (1.3.0 sürümünde yeni); programlar `cli/chloros-cli` ve `backend/chloros-backend`'tir |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Yukarıdaki asıl programları başlatan iki satırlık başlatıcı komut dosyaları — SDK, CLI ve eski komut dosyaları bunları burada aradığı için bu yollarda tutulur |
| `/usr/lib/chloros/arena_runtime/` | LATTICE kameralar için gerekli olan Arena SDK çalışma zamanı |
| `/usr/lib/chloros/models/*.pth.enc` | Texture Aware debayer tarafından kullanılan şifreli gürültü giderici modeller |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (ve `lattice_sdk`, `lattice_cam`, `arena_api`, proje yükleyicileri) | Sistem için kurulan ve pakete ait olan Python SDK'si |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Bir virtualenv için, tam olarak bu derlemeye uyan Python SDK wheel'i |
| `/usr/lib/chloros/exiftool` | Birlikte gelen ExifTool başlatıcısı (sadece sistemde exiftool yoksa `/usr/local/bin/exiftool`'e sembolik bağlantı verilir). 1.3.0 sürümünden itibaren ExifTool betiği, `/usr/lib/chloros/exiftool-dist/` içinde kendi Perl kütüphanesiyle birlikte gelir; bu nedenle sistem `libimage-exiftool-perl` paketi olmadan çalışır |
| `/etc/chloros/update.conf` | `chloros-cli update` tarafından okunan güncelleme kanalı yapılandırması |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Arka ucun root izni olmadan PTP bağlantı noktalarını bağlayabilmesi için `net.ipv4.ip_unprivileged_port_start = 319`'i ayarlar |
| `/etc/sysctl.d/60-chloros-gige.conf` | LATTICE kamera akışları için çekirdeğin soket alım tamponlarını (`net.core.rmem_max`, `net.core.rmem_default`) 32 MB'a yükseltir ve bunları kurulum sırasında uygular (daha yüksek bir değer zaten ayarlanmışsa değiştirilmez). Çekirdeğin varsayılan ayarında, kamera paketleri çekirdekte atılır ve dizi, kareleri ve kare hızını kaybeder; `chloros-cli lattice network`, tamponlar azaldığında bunu bildirir. Bkz. [Linux ana bilgisayarları — soket alım tamponları](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Dinamik yükleyiciyi `/usr/lib/chloros/arena_runtime` adresine yönlendirir |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Oturum açmış kullanıcıya DAQ-U USB seri köprüsüne erişim izni verir (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | Arka ucu, belirttiğiniz kullanıcı olarak çalıştıran, isteğe bağlı her zaman açık arka uç hizmeti (yüklü, **etkinleştirilmemiş**) — etkinleştirilmesi gereken hizmet için bkz. [Her Zaman Açık PTP](#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | Arka ucu **root**olarak çalıştıran aynı hizmet (yüklü,**etkinleştirilmemiş**); bunu etkinleştiren bir makinenin çalışmaya devam etmesi için tutulur |
| `/var/log/chloros/` | root hizmetinin ayrıntılı günlüğü; `adm` grubu tarafından okunabilir (paket tamamen kaldırıldığında silinir) |
| `/usr/share/applications/chloros-cli.desktop` | Bir terminal açan "Chloros CLI" uygulama menüsü girişi |

**Güvenlik duvarı (1.3.0 sürümünde yeni eklenmiştir).** Yükleme sırasında `ufw` veya `firewalld` etkinse, paket UDP bağlantı noktalarını açar: `5002` (DAQ-E verileri), `5353` (mDNS keşfi), `319` ve `320` (PTP) UDP bağlantı noktalarını açar ve paket kaldırıldığında bu kuralları siler. Bir güvenlik duvarı kurulu ancak etkin değilse, hiçbir şey eklemez ve daha sonra etkinleştirmeniz durumunda izin verilmesi gereken bağlantı noktalarını görüntüler. Hiçbir TCP bağlantı noktası açmaz: Sensörün görüntüyü toplamak için TCP üzerinden bu bilgisayara yeniden bağlandığı bir DAQ-E ürün yazılımı güncellemesi, etkin bir güvenlik duvarı bu bağlantıyı engellediğinde `… device never connected back on TCP <port> …` hatasıyla başarısız olur.

## Arka Uç Yürütülebilir Dosyasının Konumu

CLI ve SDK, arka ucu otomatik olarak algılar:

| Bileşen | Yol |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Arka Uç | `/usr/lib/chloros/chloros-backend` (`/usr/lib/chloros/backend/chloros-backend` için bir başlatıcı) |

Arka uç yolunu, `--backend-exe` CLI bayrağı veya `backend_exe` SDK oluşturucu parametresi ile geçersiz kılın ve bağlantı noktasını `--port` (varsayılan `5000`) ile geçersiz kılın.

{% hint style="info" %}
`CHLOROS_BACKEND_URL`, uzak bir arka uçta **`lattice`**,**`project`**ve**`daq pool-*`** komut ailelerini uzak bir arka uca yönlendirir. Temel komutlar (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) bunu kasıtlı olarak yok sayar ve her zaman `http://127.0.0.1:<port>`'i hedefler.
{% endhint %}

***

## Linux'te LATTICE Kameraları ve DAQ Işık Sensörleri

live-hardware komut ailelerinin tümü Linux'te (amd64 ve Jetson) çalışır:

* **`chloros-cli lattice`** — LATTICE kameralarını ve senkronize dizileri algılar, bağlanır, yapılandırır ve bunlardan görüntü yakalar. `.deb`, bunların ihtiyaç duyduğu Arena SDK çalışma zamanını bir araya getirir ve dinamik yükleyiciye kaydeder.
* **`chloros-cli daq pool-*`** — DAQ-U/M/E ışık sensörlerini arka uç havuzu üzerinden bağlayın, kalibre edilmiş spektrumları aktarın, `.daq` dosyalarını kaydedin ve kap profilini değiştirin; ayrıca bir DAQ-E için ağ ayarlarını değiştirin, cihazı yeniden başlatın ve donanım yazılımını güncelleyin. `chloros-cli daq discover`, bağlanabileceğiniz sensörleri tarar ve `chloros-cli daq --help`, `pool-*` alt komutlarını listeler.
* **`chloros-cli project`** — kaydedilmiş bir projeyi (kameraları, sensörleri ve işleme ayarlarını) başlıksız olarak çalıştırır.
* **`chloros-cli time-sync`** — Chloros arka ucunun LATTICE kameraları ve DAQ-E sensörleri için çalıştırdığı PTP grandmaster'ı inceler.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id`, `pool-latest`, `pool-stream`, `pool-record` ve `pool-set-cap` tarafından gereklidir; `pool-list`, havuzda bulunan mevcut kimlikleri gösterir.

{% hint style="info" %}
**`--eth-host`, keşif işlemini atlar.** `pool-connect --eth`, mDNS yoluyla bir DAQ-E bulur; bu işlem, boş ARP önbelleğinden çalışan bir ARP yedekleme mekanizmasına sahiptir; sensörün IP adresini veya ana bilgisayar adını `--eth-host` ile ileterek sensöre doğrudan bağlanılır.
{% endhint %}

**DAQ-U seri izinleri**, kurulu udev kuralı (`uaccess` + grup `dialout`) tarafından yönetilir. Zaten takılı olan bir sensöre erişilemiyorsa, kuralları yeniden yükleyin veya sensörü yeniden takın:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Tam komut seti için [CLI referansına](../CLI.md) bakın.

### Başlıksız Ana Bilgisayarlar için Sürekli Çalışan PTP

Paket, arka uç için iki systemd hizmeti yükler; bunların hiçbiri etkinleştirilmez. DAQ-E sensörleri ve LATTICE kameraları için PTP zaman senkronizasyonunu sürekli çalışır durumda tutması gereken başlıksız bir Jetson veya sunucuda ya da SDK komut dosyalarının kaydedilmiş projeleri bağladığı durumlarda (`connect_all()` hiçbir zaman bir arka uç başlatmaz), hizmeti **kullanıcı olarak** etkinleştirin:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service`, arka ucu o kullanıcı olarak çalıştırır: yazdığı yakalamalar o kullanıcıya aittir, ayrıntılı günlüğü o kullanıcının `~/.cache/chloros/logs` dosyasında bulunur ve o kullanıcının `chloros-cli login` (`~/.chloros`) kullanır; bu nedenle önce o kullanıcı olarak bir kez oturum açın. Paketi yüklediğinizde, kullanıcı adınız doldurulmuş olarak bu komut görüntülenir.

Bir hizmet olmadan, arka uç (ve bununla birlikte PTP grandmaster) yalnızca bir şey tarafından başlatıldığında çalışır — bir `chloros-cli` komutu veya bir SDK komut dosyası.

Birim, arka ucu birim içindeki `127.0.0.1:5000` (birim içindeki `CHLOROS_HOST` / `CHLOROS_PORT` ortam ayarları; `sudo systemctl edit chloros-backend@.service` ile geçersiz kılın) ve hata durumunda 5 saniye sonra yeniden başlatır.

**Root hizmeti.**`chloros-backend.service`, arka ucu**root** olarak çalıştırır. Bu hizmet, etkinleştirilen bir makinenin çalışmaya devam etmesi için hala yüklüdür, ancak root arka ucunun yazdığı her şey root'a aittir; bu nedenle bir kullanıcı kendi yakalamalarını silemez; günlüğü `/var/log/chloros`'te bulunur ve `adm` grubu tarafından okunabilir. Kendi kullanıcınıza geçmek için:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**Bir hizmetin arka ucu paylaşımlıdır ve hiçbir zaman bir istemci tarafından durdurulmaz.** `chloros-cli` ve SDK komut dosyaları onu buldukları haliyle kullanır: `process` üzerinde çalışır ve son projenin yerine kendi projesini yükler. CLI'nin normalde arka ucu yeniden başlatacağı durumlarda (`--restart` veya içe aktarma işlemi hiçbir şey bulamayan bir `process`), birim adını belirtir, `sudo systemctl restart <unit>` mesajını yazdırır ve `1`'ten çıkar; çünkü bu şekilde durdurulan bir arka uç çalışmaz durumda kalır. `sudo systemctl restart chloros-backend@$USER.service` komutuyla kendiniz yeniden başlatın. Bir hizmetin arka ucu boşta kaldığında asla kendi kendine durmaz ve `chloros-cli backend stop` bunu reddeder, `systemctl stop` komutunu belirtir.**PTP'nin bağlantı noktalarını nasıl aldığı.** PTP, her ikisi de normal 1024 ayrıcalıklı bağlantı noktası sınırının altında olan UDP 319/320'yi kullanır. Paketin `postinst` komutu, `/etc/sysctl.d/60-chloros-ptp.conf`'i `net.ipv4.ip_unprivileged_port_start = 319` ile yazar; bu da arka ucun sizin kullanıcı kimliğinizle çalışırken bu bağlantı noktalarına bağlanmasına olanak tanır. 1.3.0 sürümünden itibaren paket, arka uçta herhangi bir dosya yeteneği (`setcap`) ayarlamaz ve `libcap2-bin` artık bir bağımlılık değildir: paketi açılmış arka uçta bir dosya yeteneği, arka ucun başlamasını engeller, bu nedenle kendiniz bir tane eklemeyin.

***

## Bash Komut Dosyası Örnekleri

{% hint style="info" %}
**Komut dosyası dostu çıkış kodları.**`chloros-cli process`, başarılı olduğunda `0` ile sonlanır ve**başarısızlık durumunda sıfırdan farklı bir değer döndürür — görüntü ürünleri talep eden ancak hiçbirini yazmayan çalıştırmalar da dahil** (`Processing finished but wrote no image products.` kodunu yazdırır ve proje klasörünün adını ile olağan nedenleri belirtir). Başarılı çalıştırmalarda kaç adet görüntü ürünü yazdırıldığı bildirilir (`Image products written: N`; maskeler yazdırıldığında buna `(plus M clamp masks)` eklenir). Çıkış kodları: `0` başarı, `1` hata, `2` argüman hatası, `130` kesinti.
{% endhint %}

### Birden Fazla Veri Kümesini İşleme

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### Özel Ayarlarla İşleme

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

Geçerli `--format` değerlerinin sayısı tam olarak dörttür ve bu değerler boşluk içerir — bunları her zaman tırnak içine alın:

| `--format` değeri | Çıktı klasörü |
| --- | --- |
| `TIFF (16-bit)` *(varsayılan)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer`, `standard` (varsayılan) veya `texture-aware` (Chloros+) değerlerini kabul eder.

### Cron ile Otomatik İşleme

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK Örneği

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "OSAVI"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## Sorun Giderme

### Kurulumdan Sonra CLI Bulunamadı

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### İzin Reddedildi

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### Dosya Yetkileri Eklendikten Sonra Arka Uç Başlamıyor

Arka uçta `setcap` komutunu çalıştırmayın. 1.3.0 sürümünden itibaren paket herhangi bir dosya yeteneği ayarlamaz ve PTP'nin 319/320 numaralı bağlantı noktaları, [PTP bağlantı noktalarını nasıl alır?](#always-on-ptp-for-headless-hosts) başlığı altında açıklanan `sysctl` ayarından gelir. Bir dosya yetkisi, paketi açılmış arka ucu, kendi kütüphanelerini bulamadığı ve dolayısıyla başlatılamadığı bir güvenli yürütme moduna geçirir. Eğer bir dosya yetkisi eklediyseniz, bunu kaldırın:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP Başlamıyor / 319 Numaralı Bağlantı Noktasına Bağlanamıyor

Ayrıcalıksız bağlantı noktası alt sınırının düşürüldüğünü doğrulayın ve düşürülmemişse mevcut önyükleme için yeniden uygulayın:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Ardından grandmaster'ı kontrol edin:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### `lattice` Komutları "LATTICE kamera sürücüleri bulunamadı" Hatasıyla Başarısız Oluyor

Arka uç, birlikte kurulmuş olan LATTICE kamera çalışma zamanını yükleyemedi. Her `lattice` komutu arka uçta çalışır; CLI'nin kendisi herhangi bir kamera sürücüsü barındırmaz. Chloros'u (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`) yeniden yükleyin: `.deb`, çalışma zamanını arka ucun yanına, `/usr/lib/chloros/arena_runtime` konumuna yükler ve dinamik yükleyiciye kaydeder. Bu kaydı kontrol etmek için:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
ls /usr/lib/chloros/arena_runtime | head
```

### Arka Uç Başlatılamadı

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

Başlatma hatasına ilişkin arka uç günlükleri `~/.cache/chloros/logs/` dosyasında bulunur. 5000 numaralı bağlantı noktası bir arka uç hizmetine aitse (`systemctl status chloros-backend@$USER.service`), işlemini sonlandırmak yerine hizmeti yeniden başlatın.

### CUDA Algılanmadı

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest`, aynı durumu tek bir satırda bildirir: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Eksik Paylaşımlı Kütüphaneler

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### SD Kartlı Sistemlerde Yavaş Başlatma

1.3.0 sürümünden itibaren arka uç ve CLI, sıkıştırılmamış olarak yüklenmektedir; bu nedenle artık her başlatıldığında geçici bir dizine kendiliğinden açılmamaktadır. Geçici dosyalar, `/mnt/ssd/tmp` klasörü mevcutsa otomatik olarak bu klasöre kaydedilir; aksi takdirde, dosyaları hızlı bir dosya sistemine yönlendirebilirsiniz:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Chloros'u Linux'ta

`update` komutu yalnızca Linux/Jetson'da kullanılabilir. Bu komut, `/etc/chloros/update.conf`'te yapılandırılan güncelleme kanalında yayınlanan sürümü kontrol eder ve eşleşen `.deb`'i indirip yüklemeyi önerir:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/Jetson'da CLI, her başlatmada engellemesiz bir güncelleme kontrolü de gerçekleştirir (sonuç, `~/.chloros/update_cache.json`'te bir saat süreyle önbelleğe alınır) ve daha yeni bir sürüm mevcut olduğunda `Update available: vX.Y.Z` mesajını görüntüler. Ayarlarınız, projeleriniz ve oturum açma bilgileriniz güncelleme sonrasında korunur.

Paketin yükseltilmesi sırasında kurulum süresince tüm arka uç hizmetleri durdurulur; bu nedenle çalışan bir komut dosyası kamera ve sensör bağlantılarını kaybeder, ardından etkinleştirilmiş olan her bir hizmet yeniden başlatılır (elle başlatılmış ancak hiçbir zaman etkinleştirilmemiş bir hizmet, yeniden başlatma sonrası olduğu gibi durdurulmuş halde kalır).

## Kaldırma

```bash
sudo apt remove chloros
```

Kaldırma işlemi, tüm arka uç hizmetlerini durdurur ve devre dışı bırakır; varsayılan ayrıcalıksız bağlantı noktası alt sınırını (1024) geri yükler; alıcı tampon ayar dosyasını (çalışan değerler yeniden başlatılana kadar kalır), pakete dahil exiftool sembolik bağlantısını, Arena yükleyici yapılandırmasını, paketin eklediği güvenlik duvarı kurallarını ve yüklediği Python SDK'yi kaldırır ve önbelleğe alınmış kimlik bilgilerini temizler. Projeleriniz ve `~/.chloros/` veri dosyalarınız olduğu gibi kalır. `sudo apt purge chloros` ayrıca `/var/log/chloros`'i de siler.

***

## Sonraki Adımlar

* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson'a özgü optimizasyon ve dağıtım
* [CLI: Komut Satırı](../CLI.md) — CLI kılavuzu
* [API: Python SDK](../api-python-sdk.md) — SDK kılavuzu
* [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) — 1.3.0 sürümü için kapsamlı komut/API listeleri
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros'un donanımınıza nasıl uyum sağladığı
