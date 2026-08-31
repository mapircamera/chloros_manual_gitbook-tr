# Linux Kurulumu

Chloros, Linux adresi üzerinden, CLI ve arka uç sunucusunu kuran `.deb` paketleri olarak dağıtılmaktadır. Python SDK adresindeki dosya ise ayrı bir pip paketidir (aynı zamanda sürüm uyumlu bir wheel dosyası olarak `.deb` paketinin içinde de yer almaktadır).

Paket dosya adları sürüm ve mimari bilgilerini içerir: x86_64 için `chloros_1.2.0_amd64.deb` ve JetPack 6 Jetson derlemeleri için `chloros_1.2.0_arm64_jp6.deb`. Aşağıdaki komutlarda, gerçekte indirdiğiniz dosyayı kullanın.

***

## Linux amd64 (x86_64)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Dağıtım** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **İşlemci** | x86_64 (Intel/AMD) | Intel Core i7 veya üstü |
| **Bellek (RAM)** | 8 GB | 16 GB veya daha fazla |
| **Grafik kartı** | Yok (CPU ile işleme) | 4 GB+ VRAM&#x27;e sahip NVIDIA GPU (12 GB+ ile `GPU_PARALLEL` kilidi açılır, 7 GB+ ile Tek Görüntü yolunda Texture Aware devre dışı kalır) |
| **Depolama** | 2 GB boş alan | 10 GB+ boş alana sahip SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

> **Ubuntu 20.04 ve Debian 11 desteklenmemektedir.** `.deb`&#x27;in bağımlılık listesi,
> Chloros arka ucunun gerçekte hangi kütüphanelere bağlandığından türetilmiştir ve buna
> `libc6 (>= 2.34)` da dahildir. Focal ve bullseye sürümleri glibc 2.31 ile birlikte gelir; bu nedenle `apt`,
> kurulumun daha sonra çalışma zamanında başarısız olmasına izin vermek yerine, kurulumu
> doğrudan reddeder.

### Kurulum

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i`, bağımlılıkları çözmez. Eksik paketler bildirirse, `sudo apt-get install -f` (veya `sudo apt --fix-broken install`) kurulumu tamamlar — bu normal bir süreçtir, bir hata değildir.
{% endhint %}

Kurulumu doğrulayın:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->***

## Linux arm64 (NVIDIA Jetson)

### Sistem Gereksinimleri

| Gereksinim | Minimum | Önerilen |
| --- | --- | --- |
| **Platform** | JetPack 6 yüklü NVIDIA Jetson | Jetson Orin NX 16 GB veya AGX Orin |
| **JetPack** | JetPack 6.x | En yeni JetPack 6 |
| **Bellek (RAM)** | 8 GB (GPU/CPU paylaşımlı) | 16 GB+ paylaşımlı (paralel GPU işleyicileri için eşik değer 12 GB+&#x27;dır) |
| **Depolama** | 2GB boş alan | 10GB+ boş alana sahip NVMe SSD |
| **Python** | Python 3.7+ (SDK için) | Python 3.10+ |

### Kurulum

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

amd64 `.deb` ile aynı yapıya sahiptir; Jetson Orin / Orin NX / Orin Nano için optimize edilmiş bir CUDA derlemesi içerir. Jetson bellek, termal ve sahada kullanım davranışları hakkında bilgi için [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md).

***

## Python SDK Kurulumu (Tüm Linux)

SDK, arka uç için saf Python HTTP istemcisidir; bu nedenle aynı paket hem amd64 hem de arm64 üzerinde çalışır. İki kaynak:**PyPI&#x27;dan** — yayınlanmış kararlı sürüm:

```bash
pip install chloros-sdk
```

**Paket içindeki wheel dosyasından** — az önce kurduğunuz CLI /backend ile uyumlu olduğu garanti edilir (`.deb` sürümünüz PyPI&#x27;dakinden daha yeni ise bunu kullanın):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**PEP 668 dağıtımları** (Ubuntu 23.10+, Debian 12+) sistem genelinde pip kurulumlarını reddeder. `pip install --user …`&#x27;i, bir sanal ortamı veya `sudo pip install --break-system-packages …`&#x27;i kullanın. Paket yükleyici, SDK dosyasını sisteminize Python asla otomatik olarak yüklemez — bu seçim size kalmıştır.
{% endhint %}

İsteğe bağlı ekler:

| Ek | Komut | Eklenenler |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | Canlı ilerleme akışı için `sseclient-py` |
| `camera` | `pip install chloros-sdk[camera]` | BLE (DAQ-M) aktarımı için `bleak` |

SDK&#x27;i doğrulayın:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb`, Chloros CLI ve arka ucu yükler. Python SDK, yerel bir HTTP API (`http://127.0.0.1:5000`) üzerinden bu arka uçla iletişim kurar ve gerektiğinde onu otomatik olarak başlatır. Her zaman `localhost` yerine tam IPv4 adresini kullanın — çünkü `localhost`, `::1` olarak çözümlenebilir ve istek başına yaklaşık iki saniye sürebilir.
{% endhint %}

***

## İlk Kurulum

### 1. Giriş Yapın

CLI ve SDK adreslerine erişim, sunucu tarafında uygulanan ücretli bir Chloros+ kademesi (**Copper** veya üstü) gerektirir: oturumu kapatılmış bir çağrı yapan kişiye `401 AUTH_REQUIRED`, ücretsiz kademedeki (Iron) bir çağrı yapan kişiye ise `403 PLAN_UPGRADE_REQUIRED` verilir.

```bash
chloros-cli login your@email.com 'your-password'
```

Kimlik bilgileri `~/.chloros/user_session.json`&#x27;te önbelleğe alınır.

{% hint style="warning" %}
**Her kurulum veya yükseltme işleminden sonra yeniden oturum açmanız gerekir.** Paketin `prerm` komut dosyası, makinedeki her kullanıcı için `~/.chloros/user_session.json` dosyasını ve önbelleğe alınmış lisansı kasıtlı olarak temizler; böylece yeni bir derleme, eski bir önbelleğe güvenmek yerine lisansı her zaman yeniden doğrular.
{% endhint %}

### 2. Lisans Durumunuzu Kontrol Edin

```bash
chloros-cli status
```

`chloros-cli status`, tüm kademelerde (ücretsiz dahil) çalışır; böylece erişimin neden mevcut olup olmadığını her zaman görebilirsiniz.

### 3. Sistem Tanılama İşlemini Çalıştırın

```bash
chloros-cli selftest
```

Yedi kontrol sırayla çalıştırılır ve bunlardan herhangi biri başarısız olursa komut sıfırdan farklı bir değerle sonlanır:

| # | Kontrol | Neyi doğrular |
| --- | --- | --- |
| 1 | **Sürüm** | CLI, sürümünü bildirir (`v1.2.0`). |
| 2 | **Bağlantı noktası kullanılabilir** | 5000 numaralı bağlantı noktası boş *veya* halihazırda çalışır durumdaki bir Chloros arka ucu tarafından yanıtlanıyor (bu, testin başarılı olduğu anlamına gelir). |
| 3 | **Arka uç başlatma** | Arka uç ikili dosyası başlatılır. |
| 4 | **API testi (`/api/test`)** | Arka uç, `status: ok` yanıtını verir. |
| 5 | **Sistem bilgisi** | `/api/system-info`&#x27;ten `GPU: <name>, CUDA: <bool>, PyTorch: <version>`&#x27;i yazdırır. |
| 6 | **Gürültü giderici modeller** | `*.pth.enc` modellerini bulur (Linux&#x27;da: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Gürültü Giderici**| Texture Aware aslında kullanılabilir — CUDA**ve** en az bir model dosyası gerektirir. |

Çalıştırma, `N/7 checks passed` ile sona erer ve tüm hataları adlarına göre listeler.

### 4. İlk Veri Kümenizi İşleyin

```bash
chloros-cli process ~/datasets/flight001
```

***

## Dosyalar ve Dizinler

### Kullanıcı Başına

Chloros, kimlik bilgilerini ve CLI yapılandırmasını tek bir platformlar arası dizinde, **`~/.chloros/`** (Windows&#x27;da `%USERPROFILE%\.chloros\`) saklar. Buna karşılık, Linux&#x27;a özgü iki önbellek XDG kurallarına uyar — bunlar, ayarlandığında `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` adlarını kullanır.

| Yol | Amaç |
| --- | --- |
| `~/.chloros/user_session.json` | `chloros-cli login` tarafından yazılan oturum önbelleği (her paket yüklemesi/güncellemesinde temizlenir) |
| `~/.chloros/working_directory.txt` | Varsayılan proje klasörü geçersiz kılma (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI dil tercihi (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Windows GUI ile paylaşılan dil ayarı — buradaki bir `language`, `cli_language.json`&#x27;e göre önceliklidir |
| `~/.chloros/update_cache.json` | Linux /Jetson başlangıç güncelleme kontrolü için bir saatlik önbellek |
| `~/.chloros/backend.log` | Arka uç, CLI tarafından başlatıldığında arka uç günlüğü |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | Seri numarası ve paket hash&#x27;ine göre anahtarlanmış, kamera başına önbelleğe alınmış LATTICE kalibrasyon paketleri |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | DAQ kapasite düzeltme profilleri için isteğe bağlı kullanıcı geçersiz kılmaları |
| `~/.config/chloros/system_config.json` | Dinamik Hesaplama Uyumlaştırma&#x27;dan önbelleğe alınmış donanım profili — yeni bir donanım algılamasını zorlamak için silin |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | Arka uç sunucu günlükleri, her başlatma için bir dosya |
| `~/Chloros Projects/` | Herhangi bir geçersiz kılma ayarlanmadığında varsayılan proje klasörü |

### Sistem Genelinde

| Yol | Amaç |
| --- | --- |
| `/usr/bin/chloros-cli` | Sarma betiği — paketlenmiş yerel kütüphaneler için `LD_LIBRARY_PATH`&#x27;i ayarlar, ardından asıl ikili dosyayı çalıştırır |
| `/usr/bin/chloros-backend` | Sarma betiği — aynı, ayrıca `CHLOROS_PRODUCTION=1` değerini de içerir; böylece arka ucun kimlik doğrulama kapısı hiçbir zaman sessizce kendini devre dışı bırakamaz |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | Derlenmiş ikili dosyalar |
| `/usr/lib/chloros/arena_runtime/` | LATTICE kameralar için gerekli olan Arena SDK çalışma zamanı |
| `/usr/lib/chloros/models/*.pth.enc` | Texture Aware debayer tarafından kullanılan şifreli gürültü giderici modeller |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Bu derlemeyle tam olarak eşleşen Python SDK tekerleği |
| `/usr/lib/chloros/exiftool` | Paket içinde bulunan exiftool (sadece sistemde exiftool yoksa `/usr/local/bin/exiftool`&#x27;e sembolik bağlantı verilir) |
| `/etc/chloros/update.conf` | `chloros-cli update` tarafından okunan güncelleme kanalı yapılandırması |
| `/etc/sysctl.d/60-chloros-ptp.conf` | Arka uçun root izni olmadan PTP bağlantı noktalarını bağlayabilmesi için `net.ipv4.ip_unprivileged_port_start = 319`&#x27;i ayarlar |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | Dinamik yükleyiciyi `/usr/lib/chloros/arena_runtime`&#x27;e yönlendirir |
| `/lib/udev/rules.d/70-chloros-daq.rules` | Oturum açmış kullanıcıya DAQ-U USB seri köprüsüne (CP2102N, `10c4:ea60`) erişim izni verir |
| `/lib/systemd/system/chloros-backend.service` | Her zaman açık arka uç hizmetini etkinleştir (yüklü, **etkinleştirilmemiş**) |
| `/usr/share/applications/chloros-cli.desktop` | Terminali açan &quot;Chloros CLI&quot; uygulama menüsü girişi |

## Arka Uç Yürütülebilir Dosyasının Konumu

CLIu ve SDK, arka ucu otomatik olarak algılar:

| Bileşen | Yol |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Arka Uç | `/usr/lib/chloros/chloros-backend` |

Arka uç yolunu `--backend-exe` CLI bayrağıyla veya `backend_exe` SDK yapıcı parametresiyle, bağlantı noktasını ise `--port` (varsayılan `5000`) ile geçersiz kılın.

{% hint style="info" %}
`CHLOROS_BACKEND_URL`, uzak bir arka uçta **`lattice`**,**`project`**ve**`daq pool-*`** komut ailelerine yönlendirir. Temel komutlar (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) bunu kasıtlı olarak göz ardı eder ve her zaman `http://127.0.0.1:<port>`&#x27;i hedefler.
{% endhint %}

***

## Linux Üzerindeki LATTICE Kameraları ve DAQ Işık Sensörleri

Tüm canlı donanım komut aileleri Linux (amd64 ve Jetson) üzerinde çalışır:

* **`chloros-cli lattice`** — LATTICE kameralarını ve senkronize dizileri keşfedin, bağlayın, yapılandırın ve bunlardan görüntü yakalayın. `.deb`, bunların gerektirdiği Arena SDK çalışma zamanını bir araya getirir ve bunu dinamik yükleyiciye kaydeder.
* **`chloros-cli daq pool-*`** — DAQ-U/M/E ışık sensörlerini arka uç havuzu üzerinden bağlayın, kalibre edilmiş spektrumları aktarın ve `.daq` dosyalarını kaydedin. Derlenmiş CLI, yalnızca `pool-*` ailesini içerir: `pool-connect`, `pool-disconnect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — kaydedilmiş bir projeyi (kameralarını, sensörlerini ve işleme ayarlarını) başsız olarak çalıştırır.
* **`chloros-cli time-sync`** — Chloros arka ucunun LATTICE kameraları ve DAQ-E sensörleri için çalıştırdığı PTP grandmaster&#x27;ı inceleyin.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id`, `pool-latest`, `pool-stream`, `pool-record` ve `pool-set-cap` tarafından gereklidir; `pool-list`, havuzda şu anda bulunan kimlikleri gösterir.

{% hint style="info" %}
**Çoklu ağ bağlantılı bir makinede ilk DAQ-E bağlantısı için `--eth-host`&#x27;i tercih edin.** Otomatik keşif, mDNS&#x27;yi tarar ve boş bir ARP önbelleği nedeniyle sensörün arayüzünü gözden kaçırabilir; bu nedenle, sensör tamamen sağlam olsa bile önyüklemeden sonraki ilk `pool-connect --eth` denemesi başarısız olabilir. Sensörün IP adresini veya ana bilgisayar adını belirtmek, keşif işlemini tamamen atlar.
{% endhint %}

**DAQ-U seri izinleri**, kurulu udev kuralı (`uaccess` + `dialout` grubu) tarafından yönetilir. Daha önce takılmış olan bir sensöre erişilemiyorsa, kuralları yeniden yükleyin veya sensörü yeniden takın:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

Komutların tam listesi için [CLI referansına](../CLI.md) bakın.

### Başlıksız Ana Bilgisayarlar için Sürekli Etkin PTP

İlk kurulumda systemd birimi `chloros-backend.service` oluşturulur ancak **etkinleştirilmez**. DAQ-E sensörleri ve LATTICE kameralar için PTP zaman senkronizasyonunun sürekli çalışmasını sağlaması gereken başlıksız bir Jetson veya sunucuda, bunu etkinleştirin:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

Bu birim olmadan, PTP yalnızca Chloros arka ucu çalışırken — yani aktif bir CLI / SDK oturumu sırasında — çalışır.

Ünite, arka ucu `127.0.0.1:5000`&#x27;e bağlar (ünite içindeki `CHLOROS_HOST` / `CHLOROS_PORT` ortam ayarları; `sudo systemctl edit chloros-backend.service` ile geçersiz kılınabilir) ve hata durumunda 5 saniye sonra yeniden başlatır.

**PTP&#x27;nin bağlantı noktalarını nasıl aldığı.** PTP, her ikisi de normal 1024 ayrıcalıklı bağlantı noktası sınırının altında olan UDP 319/320&#x27;yi kullanır. Paketin `postinst` ayarı, `/etc/sysctl.d/60-chloros-ptp.conf`&#x27;i `net.ipv4.ip_unprivileged_port_start = 319` ile yazar; bu da arka uçun sizin kullanıcı kimliğinizle çalışırken bu bağlantı noktalarına bağlanmasına olanak tanır. Ayrıca, ek bir güvenlik önlemi olarak arka uç ikili dosyasına `setcap cap_net_bind_service,cap_net_raw=+ep`&#x27;i de uygular — işte bu nedenle `libcap2-bin`, paketin beyan edilmiş bir bağımlılığıdır.***

## Bash Komut Dosyası Örnekleri

{% hint style="info" %}
**Komut dosyası ile uyumlu çıkış kodları.**`chloros-cli process`, başarılı olduğunda `0` ile sonlanır ve**başarısızlık durumunda sıfırdan farklı bir değer döndürür — görüntü ürünleri talep eden ancak hiçbirini yazmayan çalıştırmalar da dahil** (`Processing finished but wrote no image products.` kodunu yazdırır ve proje klasörünün adını ile olağan nedenleri belirtir). Başarılı çalıştırmalarda kaç adet görüntü ürününün yazdırıldığı bildirilir (`Image products written: N`). Çıkış kodları: `0` başarı, `1` hata, `2` argüman hatası, `130` kesinti.
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
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

Geçerli `--format` değerlerinin sayısı tam olarak dörttür ve bu değerler boşluk içerir — bunları her zaman tırnak içine alın:

| `--format` değeri | Çıktı klasörü |
| --- | --- |
| `TIFF (16-bit)` *(varsayılan)* | `tiff16` |
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
    indices=["NDVI", "NDRE"],
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
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### Kurulum Sırasında &quot;setcap başarısız&quot; Hatası

`.deb`, root izni olmadan PTP 319/320 numaralı bağlantı noktalarını bağlayabilmek için `cap_net_bind_service`&#x27;i `/usr/lib/chloros/chloros-backend`&#x27;e uygular. Yükleme sırasında `libcap2-bin` eksikse, çağrı atlanır. Bunu yükleyin ve paketi yeniden yükleyin:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP Başlamıyor / 319 Numaralı Bağlantı Noktasına Bağlanamıyor

Ayrıcalıksız bağlantı noktası alt sınırının düşürüldüğünü doğrulayın ve düşürülmemişse mevcut önyükleme için yeniden uygulayın:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

Ardından grandmaster&#x27;ı kontrol edin:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### &quot;LATTICE kamera sürücüleri bulunamadı&quot;

Arena SDK çalışma zamanı çözülemiyor. Paketin yazdığı yükleyici yapılandırmasının mevcut ve güncellenmiş olduğunu doğrulayın:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
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

Başlatma hatasına ilişkin arka uç günlükleri `~/.cache/chloros/logs/` dosyasında bulunur.

### CUDA Algılanmadı

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest`, aynı durumu tek bir satırda bildirir: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### Eksik Paylaşımlı Kütüphaneler

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### SD Kartlı Sistemlerde Yavaş Başlatma

Derlenmiş ikili dosyalar, her başlatmada kendilerini geçici bir dizine çıkarır. `/mnt/ssd/tmp` varsa, Chloros bunu otomatik olarak kullanır; aksi takdirde `TMPDIR`&#x27;i hızlı bir dosya sistemine ayarlayın:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Linux adresinde Chloros dosyasını güncelleme

`update` komutu yalnızca Linux /Jetson için geçerlidir. Bu komut, `/etc/chloros/update.conf`&#x27;te yapılandırılan güncelleme kanalında yayınlanan sürümü kontrol eder ve eşleşen `.deb`&#x27;i indirip yüklemeyi önerir:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux /Jetson&#x27;da CLI adresi, her başlatmada engellemesiz bir güncelleme kontrolü de gerçekleştirir (sonuç, `~/.chloros/update_cache.json`&#x27;te bir saat süreyle önbelleğe alınır) ve daha yeni bir sürüm mevcut olduğunda `Update available: vX.Y.Z` mesajını görüntüler. Ayarlarınız ve projeleriniz güncelleme sonrasında korunur; güncelleme sonrasında tekrar oturum açmanız gerekecektir.

## Kaldırma

```bash
sudo apt remove chloros
```

Kaldırma işlemi, `chloros-backend.service`&#x27;i durdurur, varsayılan ayrıcalıksız bağlantı noktası alt sınırını (1024) geri yükler, pakete dahil edilen exiftool sembolik bağlantısını ve Arena yükleyici yapılandırmasını kaldırır ve önbelleğe alınmış kimlik bilgilerini siler. Projeleriniz ve `~/.chloros/` veri dosyalarınız etkilenmez.

***

## Sonraki Adımlar

* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson&#x27;a özgü optimizasyon ve dağıtım
* [CLI : Komut Satırı](../CLI.md) — CLI kılavuzu
* [API : Python SDK](../api-python-sdk.md) — SDK kılavuzu
* [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) — 1.2.0 sürümü için kapsamlı komut/API listeleri
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros&#x27;un donanımınıza nasıl uyum sağladığı
