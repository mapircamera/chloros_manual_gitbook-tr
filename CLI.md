# CLI : Komut Satırı

> **Tam referans:**[CLI Referansı](reference/cli-reference.md)**her alt komutun tüm bayraklarını** belgeler ve yapay zeka asistanları için optimize edilmiştir — URL kodunu asistanınıza yapıştırın ve çalışan bir komut isteyin: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **AI araçları için ipucu:** Bu kılavuzun herhangi bir sayfası, URL (örn. `https://mapir.gitbook.io/chloros/reference/cli-reference.md`) eklenerek ham Markdown biçimi olarak erişilebilir hale gelir; ayrıca `https://mapir.gitbook.io/chloros/llms.txt`, LLM kullanımı için kılavuzun tamamını indeksler.

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.2.0 build so the version line reads "Chloros CLI 1.2.0" -->
## CLI Nedir?

`chloros-cli`, Chloros masaüstü uygulamasının kullandığı işleme motorunun komut satırı ön yüzüdür. Bu, Chloros arka ucunun (`127.0.0.1:5000` üzerindeki yerel bir sunucu) üzerinde çalışan ince bir HTTP istemcisidir — çoğu komut arka ucu otomatik olarak başlatır, bu nedenle bir komut dosyasının tek bir `chloros-cli process …` çağrısına ihtiyacı vardır.

**Windows 10/11 (x64)**ve**Linux (x86_64 ve JetPack 6 üzerindeki NVIDIA Jetson arm64)** üzerinde çalışır, herhangi bir terminalde çalışır ve GUI gerektirmez. Kurulumunuzu şu komutla doğrulayın:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```

Komut gruplarına genel bakış:

* **İşleme ve hesap** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 dil — bkz. [Desteklenen Diller](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (Linux/Yalnızca Jetson)
* **Canlı donanım** — `lattice` (LATTICE kamera kontrolü, 45&#x27;ten fazla alt komut), `daq pool-*` (DAQ ışık sensörleri), `time-sync` (PTP)
* **Otomasyon** — `project` (YAML yakalama tarifleri dahil olmak üzere, kaydedilmiş bir Chloros projesini başlıksız olarak çalıştırma)

Bilmeniz gereken genel seçenekler: `--port N` (arka uç bağlantı noktası, varsayılan `5000`), `-v/--verbose`, `--restart` (arka ucu zorla yeniden başlat), `--backend-exe PATH`. Tam liste için [CLI Referansı](reference/cli-reference.md) sayfasına bakın.

***

## Kurulum

CLI, her platformda **Chloros yükleyicisi içinde** bulunur — ayrı bir CLI indirme dosyası yoktur. Yükleyiciyi [İndir](download.md) sayfasından edinebilirsiniz.

### Windows

Yükleyici, CLI dosyasını şu konuma yerleştirir:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

konumuna yerleştirir ve bu klasörü sisteminize ekler `PATH` — kurulumdan sonra **yeni bir terminal açın**, böylece güncellenmiş `PATH` algılanır. Yükleyici ayrıca kurulum kök dizinine (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) ve**Chloros CLI** Başlat menüsü kısayolunu da yerleştirir; bunların her biri, `chloros-cli`&#x27;in kullanıma hazır olduğu bir terminal açar.

### Linux

Mimarinize uygun `.deb` dosyasını yükleyin:

```bash
# Linux x86_64
sudo dpkg -i chloros-amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

Bu, `chloros-cli`&#x27;i `/usr/bin/chloros-cli` (zaten `PATH` sürümündedir) ve arka uç yazılımını `/usr/lib/chloros/chloros-backend` sürümüne yükseltir; ayrıca LATTICE kameralar için gerekli olan Arena SDK çalışma zamanı dosyası da yüklenir. Ayrıntılar için [Linux Kurulumu](linux/linux-installation.md) sayfasına bakın.

### Doğrulama

```bash
chloros-cli --version    # "Chloros CLI 1.2.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Oturum Açma ve Lisanslama

CLI (ve Python ile SDK) erişimi için **ücretli bir Chloros+ planı**gereklidir — herhangi bir ücretli kademede bu özellik mevcuttur; ücretsiz kademede ise yoktur. Bu sınırlama, CLI ikili dosyası tarafından değil, arka uç tarafından**sunucu tarafında** uygulanır: oturumu kapatılmış bir çağrı `401 AUTH_REQUIRED` hatasıyla reddedilir; ücretsiz kademede oturumu açık bir çağrı ise, ister `chloros-cli`, SDK&#x27;ten veya elle geliştirilmiş bir HTTP istemcisinden gelse de reddedilir. [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) adresinden yükseltme yapın.**Her makine için bir kez** oturum açın:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.2.0 build showing the license tier line -->
{% hint style="warning" %}
**Özel karakterler içeren şifreler**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$`, kabuk tarafından bozulur (CLI bunu 401 hatasında algılar ve otomatik olarak yeniden dener, ancak tek tırnak işaretleri bu sorunu tamamen ortadan kaldırır).
{% endhint %}

Oturum, `~/.chloros/user_session.json`&#x27;te önbelleğe alınır ve planın ek süre boyunca çevrimdışı olarak çalışmaya devam eder (aylık planlar için 30 gün, yıllık planlar için son kullanma tarihine kadar). `chloros-cli status`, ücretli bir plan olmasa bile çalışır; bu nedenle reddedilme nedeni her zaman görünür durumdadır.

{% hint style="danger" %}
**Headless iş mi planlıyorsunuz? Önce oturum açın.**Arka uç oluşturma komutları (`process`, `status`, `export-status`, …)**önbelleğe alınmış oturum olmadan**çalıştırıldığında hızlı bir şekilde hata vermez — stdin üzerinde etkileşimli bir `Email:` / `Password:` komut istemine geçer. Bu nedenle, gözetimsiz bir cron işi veya CI adımı**giriş beklerken takılır**. Herhangi bir şey planlamadan önce makinede `chloros-cli login EMAIL 'PASSWORD'` komutunu bir kez çalıştırın.
{% endhint %}

***

## İlk İşleme Çalıştırmanız

`process` komutunu yakalama dosyalarının bulunduğu bir klasöre yönlendirin — Survey3 (`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` veya bunların bir karışımını otomatik olarak algılar:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

İlerleme akışları, her bir iş akışı iş parçacığı için canlı olarak görüntülenir (Algılama, Analiz, İşleme, Dışa Aktarma) ve başarılı bir çalıştırma, kaç adet görüntü ürününün yazıldığını bildirerek sona erer (`Image products written: N`).

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N" summary line -->
### Çıktıların nereye kaydedildiği

`process`, giriş klasörünüze değil, bir **proje klasörüne** yazar:

* `-o` belirtilmediğinde: proje, varsayılan proje klasörünüzün altında oluşturulur (GUI ile paylaşılır; bunu `get-project-folder` / `set-project-folder` ile yönetin, yedek olarak `~/Chloros Projects` kullanılır), adı `-n/--project-name` olarak belirlenir veya belirtilmediğinde bir zaman damgası (`YYYYMMDD_HHMMSS`) kullanılır.
* `-o PATH` ile: bu klasör **proje klasörüdür**. Eğer klasörde zaten bir `project.json` varsa, üzerine yazmak yerine sonuna `_1`/`_2`… eki eklenmiş bir alt klasör oluşturulur.

Proje içinde ürünler **önce kameraya, ardından dosya formatına göre** gruplandırılır:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

LATTICE için kamera klasörü `LATT-<sensor>-<lens>-F<filter>`&#x27;tir (çekimin EXIF verisiyle eşleşir: `Model`) ve `<model>_<filter>` (örn. `Survey3N_RGN`) şeklindedir. Biçim klasörü şu şekilde devam eder: `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32` (`TIFF (32-bit, Percent)` için).

{% hint style="info" %}
**Dışa aktarılan her ürün, KAYNAK dosyanın adını korur.**`capture_..._raw.tif` dosyasının radiance formatında dışa aktarımı yine `capture_..._raw.tif` olarak adlandırılır — sadece `tiff32/Radiance_Images/` klasöründe bulunur.**Ürünü tanımlayan dosya adı değil, klasördür**; bu nedenle `*radiance*` sonekine değil, dizine göre genel arama yapın.
{% endhint %}

### Gerçekte kullanacağınız seçenekler

| Bayrak | Varsayılan | Ne işe yarar |
| --- | --- | --- |
| `-o, --output PATH` | varsayılan proje klasörü | Proje klasörünün konumu (yukarıya bakın). |
| `-n, --project-name NAME` | zaman damgası | Proje adı. |
| `--format FMT` | `TIFF (16-bit)` | `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`&#x27;ten biri. |
| `--indices NAME [NAME ...]` | yok | Dışa aktarılacak bitki örtüsü endeksleri (bkz. [Bitki Örtüsü Endeksleri](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = sinir ağı tabanlı debayering, daha yavaş, en yüksek kalite (Chloros+, NVIDIA GPU). |
| `--vignette / --no-vignette` | açık | Vinyet düzeltmesi. |
| `--reflectance / --no-reflectance` | açık | Yansıma kalibrasyonu; LATTICE için bu aynı zamanda yansıma ürünü anahtarıdır. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFF&#x27;leri için iş akışı giriş noktasını zorla. |

Diğer tüm öğeler için — hedef algılama ayarlaması, PPK, pozlama pimleri, dizi hizalama bayrakları — [CLI Referansı’nın `process` bölümüne](reference/cli-reference.md) bölümüne bakın.

***

## Dışa Aktarılacakların Seçilmesi (LATTICE Ürünleri)

LATTICE işleme, **tek geçişte tüm uygun ürünlere**yayılır. Ürün başına dört anahtar**varsayılan olarak AÇIK** durumdadır; birini kapatmak için `--no-` formunu kullanın:

| Anahtar | Ürün |
| --- | --- |
| `--debayered` | Doğrusal demosaik → `Debayered_Images/` |
| `--preview` | Önizleme göster (beyaz dengesi + gama; multispektral için sahte renk genişletme) → `Preview_Images/` |
| `--radiance` | float32 parlaklık, W/m²/sr/nm → `Radiance_Images/` (her zaman `tiff32/`) |
| `--reflectance` | uint16 yansıma, Pix4D uyumlu → `Reflectance_Calibrated_Images/` |

RGB ana kameralar her zaman sadece debayering uygulanmış + önizleme verileri yayar — geniş bantlı bir sensör için bant başına parlaklık/yansıma değeri anlamlı değildir, bu nedenle bu anahtarlar bu kameralar için hiçbir işlev görmez. Survey3 `.raw`, anahtarları yok sayar ve standart yansıma/hedef yolunu izler.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (varsayılan `auto`) yansıma referansını seçer: `auto`, QA&#x27;dan geçen çerçeve içi bir [kalibrasyon hedefi](calibration-targets.md) mutlak referans olarak kullanır ve hedef bulunmadığında DAQ ışık sensörünün aşağı doğru ışık bölünmesine (ρ = π·L/E) geri döner; `target` katıdır (DAQ ikamesi yoktur); `daq`, DAQ&#x27;ya göre karar verir. Birim başına ölçülen hedef taramaları, `--target-reflectance-dir` ile sağlanabilir.

{% hint style="info" %}
**Yansıma piksellerinin okunması:**ρ = 1,0 anlamına gelen DN**kaynak başına**dır — LATTICE dosyaları, XMP&#x27;ye `Chloros:PixelScale=32768` damgasını vurur; Survey3 dosyaları 65535 değerini kullanır (ve `Chloros:*` etiketleri içermez). Sabit bir değer varsaymak yerine etiketi okuyun ve buna bölün. Ayrıntılar ve kasıtlı olarak ölçeklendirilmemiş tek kenar durumu [CLI Referansı](reference/cli-reference.md) içinde yer almaktadır.
{% endhint %}

**İşleme her zaman `raw`&#x27;ten başlar.** Türetilmiş ürünler (debayering/parlaklık/yansıtma dışa aktarımları) asla işleme hattına geri beslenmez — bunların yeniden içe aktarılması ve işlenmesi, kalibrasyon hesaplamalarının iki kez uygulanmasına neden olur; bu nedenle Chloros bunları atlar ve bunu belirtir. `--input-level`, gerçekten bir giriş noktasını zorla belirlemeniz gerektiğinde kullanılacak, kasıtlı olarak bırakılmış bir kaçış yoludur.***

## Bir Çalıştırma Başarısız Olduğunda

1.2.0 sürümünden itibaren, `process` hiçbir sonuç göstermeden &quot;başarılı&quot; olmak yerine açıkça hata verir:

* **Ürün talep eden ancak hiçbirini yazmayan**bir çalıştırma — yalnızca `project.json` ve `calibration_data.json` — `Processing finished but wrote no image products.` mesajını yazdırır ve**sıfırdan farklı bir değerle sonlanır**, böylece komut dosyaları bunu algılayabilir. Yaygın nedenler: giriş klasörü bir yakalama olarak tanınmadı (düzeni ve `--input-level`&#x27;i kontrol edin) veya istenen tüm ürünler bu kameralar için uygun değildi (ör. yalnızca RGB kameralardan parlaklık/yansıtma değeri istenmesi).
* **Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma** (tüm ürünler kapalı, `--indices` yok) yine de başarılı sayılır — bu durumda boş bir görüntü çıktısı doğru sonuçtur.
* `--verbose` ile işlemi yeniden çalıştırın ve arka uç günlüğünde, kamera bazında atlamaları açıklayan `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` satırlarını kontrol edin.

Çıkış kodları: `0` başarı · `1` genel hata · `2` argüman hatası · `130` Ctrl+C ile kesildi.

***

## Bitki Örtüsü Endeksleri

`--indices` komutunu bir veya daha fazla ön ayar adıyla çalıştırın; her endeks kendi `<INDEX>_Index_Images/` klasörüne yerleştirilir:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI NDRE GNDVI
```

`process --indices`&#x27;in kabul ettiği 22 ön ayar adı:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

{% hint style="warning" %}
**Üç dizin listesi mevcuttur — bunları karıştırmayın.**GUI’nin Proje Ayarları açılır menüsünde 27 formül bulunmaktadır (`FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — bu beş formül yalnızca GUI’ye özeldir ve `--indices` için**geçerli değildir**). Canlı/çevrimdışı `lattice index --preset` komutu, kendine ait ayrı bir 22 ön ayar listesi kullanır. Formüller ve bant hesaplamaları [Multispektral İndeks Formülleri](project-settings/multispectral-index-formulas.md) belgesinde açıklanmıştır.
{% endhint %}

***

## DAQ Işık Sensörleri: Hızlı Bir Tanıtım

`daq pool-*` ailesi, arka ucun kalıcı havuzu (GUI, CLI ve SDK) aracılığıyla MAPIR DAQ spektral sensörlerini (USB üzerinden DAQ-U, BLE üzerinden DAQ-M, Ethernet üzerinden DAQ-E) arka uçtaki kalıcı havuz aracılığıyla çalıştırır — GUI, CLI ve SDK hepsi tek bir canlı tanıtıcıyı paylaşır. **`pool-*`, ürünle birlikte gelen CLI&#x27;te desteklenen DAQ yoludur**; bahsedilebilecek diğer `daq` alt komutları, MAPIR&#x27;e ait dahili, yalnızca kaynak amaçlı bir yüzeydir ve sizi `pool-*`&#x27;e yönlendiren açık bir hata mesajıyla sonlanır.

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record`, `--duration` olmadan `pool-record --stop`&#x27;e kadar çalışır; varsayılan çıktı dizini **arka uç makinesinde** `~/Documents/DAQ Live View/`&#x27;tir. Kapak düzeltme profili bağlantı anında seçilir (`--cap-id`, arka uç varsayılanı `sunshine_cosine`) ve canlı olarak `pool-set-cap` ile canlı olarak değiştirilebilir — kap profil ve sensörün kalibre edilmiş aralığı bu kılavuzun DAQ bölümlerinde ele alınmaktadır.

{% hint style="warning" %}
**Çoklu NIC&#x27;li bir ana bilgisayarda DAQ-E:** Önyüklemeden sonraki ilk `pool-connect --eth` otomatik keşif işlemi, sensör çalışır durumda olsa bile başarısız olabilir. `--eth-host <ip-or-hostname>` güvenilir seçenektir — keşif işlemi sonuçsuz kaldığında bunu kullanın.
{% endhint %}

***

## LATTICE Kameralar, PTP ve Proje Otomasyonu

`lattice` ailesi (45&#x27;ten fazla alt komut), LATTICE kameraların uçtan uca çalışmasını kapsar: keşif, tekli çekimler, GUI&#x27;nin akıllı hazırlık bağlantı akışıyla kalıcı senkronize diziler, canlı tarayıcı önizlemesi, hizalama, dizin hesaplamaları ve ana bilgisayar-NIC tanılama. Bir örnek:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Bununla birlikte: `chloros-cli time-sync`, Chloros ana bilgisayarının çalıştırdığı PTP grandmaster&#x27;ı raporlar (LATTICE kameralar ve DAQ-E sensörleri, cihazlar arası zaman damgası için buna bağlıdır), ve `chloros-cli project`, kaydedilmiş bir Chloros projesini açar ve komut dosyası ile yazılmış YAML yakalama tarifleri dahil olmak üzere kameralarını, dizilerini ve sensörlerini başlıksız olarak çalıştırır.

Bu üç ürün ailesi (`lattice`, `project`, `daq pool-*`) aynı zamanda **uzak** bir arka ucu çalıştırmak için `CHLOROS_BACKEND_URL` komutunu destekleyen tek ailelerdir; temel komutlar her zaman yerel makineyi hedefler.

Tam adım adım kılavuzlar bu kılavuzun LATTICE bölümlerinde yer almaktadır; her bayrak [CLI Referansı](reference/cli-reference.md) içinde bulunur.

***

## Sorun Giderme: En Önemli 5 Sorun

| Belirti | Çözüm |
| --- | --- |
| `Login required` veya zamanlanmış bir iş, `Email:` isteminde takılır | Bu makinede `chloros-cli login EMAIL 'PASSWORD'` komutunu bir kez çalıştırın — önbelleğe alınmış oturum istemi olmayan komutlar, hızlı bir şekilde hata vermek yerine etkileşimli olarak çalışır. |
| `backend unreachable` | Chloros masaüstü uygulamasını başlatın veya arka uç ikili dosyasını doğrudan çalıştırın (`chloros-backend`). `lattice`/`project`/`daq pool-*` komutlarını uzak bir arka uçta kullanıyorsanız, `CHLOROS_BACKEND_URL`&#x27;i kontrol edin. |
| Dizi bağlantısı engellendi: `FRAMES WILL DROP` / `Reduce ROI to enable` | Ana bilgisayarın ağ kartı (NIC) alım halkası varsayılan ayarlara sıfırlandı — bu, genellikle bir ağ kartı sürücüsü güncellemesinden sonra, daha önce çalışan bir sistemin bağlanmayı reddetmesinin en yaygın nedenidir. `chloros-cli lattice network --fix` komutunu **yükseltilmiş** bir terminalden çalıştırın (veya `ReceiveBufferLen=256`, `PendingReceives=64` ayarlarını yapın); referans kılavuzundaki *Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması* bölümüne bakın. |
| `daq` alt komutu sonlandırılır: &quot;tam DAQ paketi gerektirir…&quot; | Dağıtılan sürümlerde beklenen bir durumdur — derlenmiş CLI, yalnızca bağlantı, akış, kayıt ve cap seçimini kapsayan `daq pool-*` ailesini içerir. `pool-*`&#x27;i (veya Python&#x27;ten gelen `chloros_sdk.connect_daq_sensor()`&#x27;i) kullanın. |
| Jetson, büyük klasörler öncesinde bir takas uyarısı görüntüler | Dosya destekli takas ekleyin — CLI, çalıştırılması gereken tam `fallocate`/`swapon` komutlarını görüntüler. |

***

## Yardım Alma

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Her bayrak, her alt komut:** [CLI Referansı](reference/cli-reference.md)
* **Python eşdeğeri:** [Python SDK](api-python-sdk.md) ve [SDK Referansı](reference/sdk-reference.md)
* **Destek:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
