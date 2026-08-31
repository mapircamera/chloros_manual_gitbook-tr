# Chloros CLI Referans

**Sürüm:**

1.2.0**Oluşturulma Tarihi:**29 Temmuz 2026 19:19 ·**Güncelleme Tarihi:** 30 Ağustos 2026**Hedef Kitle:** LLM kullanımı için optimize edilmiştir; insanokunabilir.**Kapsam:** `chloros-cli`&#x27;in kullanıcıya yönelik tüm alt komutları, seçenekleri ve kopyala-yapıştır edilebilir örnekleri ile birlikte.

Bu belge, `chloros-cli` komut satırı aracı için eksiksiz bir referanstır ve MAPIR Chloros ile birlikte gelen `chloros-cli` komut satırı aracı için eksiksiz bir referans kılavuzdur. Bir LLM’nin (veya insanın) kaynak kodunu incelemeden aşağıdaki listelerden desteklenen herhangi bir iş akışını oluşturabilmesi için kasıtlı olarak kapsamlı bir şekilde hazırlanmıştır.

Yalnızca önemli noktalara göz atmak istiyorsanız, şu bölümlere geçin:
- [Beş Dakikalık Hızlı Başlangıç](#five-minute-quickstart)
- [LATTICE Kamera İlk Bağlantı İş Akışı](#lattice-camera-first-connect-workflow)
- [DAQ Sensörü İlk Bağlantı İş Akışı](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](#capture-modes-recorders--offline-reprocess)

---

## Kural ve Kısaltmalar

- Tüm komutların başında `chloros-cli` önekleri bulunur. Windows sürümünde ikili dosya `chloros-cli.exe`; Linux/Jetson&#x27;da ise `chloros-cli` şeklindedir.
- İsteğe bağlı argümanlar `--flag` şeklinde gösterilir. Zorunlu konumsal argümanlar parantez olmadan gösterilir.
- Varsayılan bir değer belirtilmişse, bayrağın atlanması durumunda bu değer kullanılır.
- CLI, Chloros arka ucunda (`127.0.0.1:5000` üzerindeki Flask sunucusu) çalışan ince bir HTTP istemcisidir. Arka uç, çoğu komut tarafından otomatik olarak başlatılır. `CHLOROS_BACKEND_URL=<url>`, uzak bir arka uçta **`lattice`**,**`project`**ve**`daq pool-*`** komut ailelerini uzak bir arka uca yönlendirir — temel komutlar (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) komut aileleri, `http://127.0.0.1:<port>` komutunu kasıtlı olarak sabitler ve yok sayar (IPv4 sabit değeri, Windows&#x27;in `localhost`→`::1` ~2 saniye-istek başına cezayı önler). Bkz. [Ortam Değişkenleri](#environment-variables).
- Tüm SDK/CLI çağrıları için bir Chloros+ hesabıyla oturum açılması gerekir (her makine başına bir kez `chloros-cli login` komutunu çalıştırın; `~/.chloros/` içinde önbelleğe alınır).
- Örneklerde Linux yolları kullanılmaktadır; Windows&#x27;te `/home/user/...`&#x27;i `C:/Users/.../...` ile değiştirin.

---

## Üst Düzey Özet

```
chloros-cli [global options] COMMAND [command options]
```

### Genel Seçenekler

| Bayrak | Açıklama |
| --- | --- |
| `--backend-exe PATH` | Otomatik olarak algılanan arka uç yürütülebilir dosyasını geçersiz kılar. |
| `--port N` | Arka uç HTTP bağlantı noktası (varsayılan: `5000`). |
| `-v, --verbose` | Ayrıntılı çıktıyı etkinleştir. |
| `--restart` | Arka uç programını zorla yeniden başlat (çalışmakta olan tüm `backend_server.py` işlemlerini sonlandırır). |
| `--version` | Sürümü göster (`Chloros CLI 1.2.0`). |
| `--help` | Üst düzey yardım göster. |

### Komut Dizini

| Komut | Amaç |
| --- | --- |
| [`process`](#chloros-cli-process) | Bir klasördeki Survey3 veya LATTICE yakalamalarını uçtan uca işleyin. |
| [`login`](#chloros-cli-login) | Bu makineyi bir Chloros+ hesabıyla kimlik doğrulaması yapar. |
| [`logout`](#chloros-cli-logout) | Önbelleğe alınmış kimlik bilgilerini temizle. |
| [`status`](#chloros-cli-status) | Mevcut lisans / kimlik doğrulama durumunu göster. |
| [`export-status`](#chloros-cli-export-status) | `process` çalışması sırasında Live Thread-4 dışa aktarma ilerlemesi. |
| [`language`](#chloros-cli-language) | CLI görüntüleme dilini ayarla veya listele (38 desteklenir). |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | Varsayılan proje klasörü (GUI ile paylaşılır). |
| [`update`](#chloros-cli-update) | CLI güncellemelerini kontrol edin ve yükleyin (Linux/Jetson). |
| [`selftest`](#chloros-cli-selftest) | Sistem tanılama + işlevsellik testleri. |
| [`time-sync`](#chloros-cli-time-sync) | PTP grandmaster durumu / kontrolü. |
| [`lattice`](#chloros-cli-lattice) | LATTICE kamera kontrolü ve görüntü yakalama (45&#x27;ten fazla alt komut). |
| [`daq`](#chloros-cli-daq) | DAQ spektral sensör kontrolü (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | Kaydedilmiş bir Chloros projesini (kameralar + DAQ&#x27;lar) açma ve çalıştırma. |

---

## Kurulum

`chloros-cli`, desteklenen tüm platformlarda Chloros masaüstü yükleyicisinin içinde bulunur — ayrı bir CLI indirme dosyası yoktur. Platform paketini kurduğunuzda, `chloros-cli`, masaüstü uygulaması ve onu çalıştıran arka uç ikili dosyasıyla birlikte `PATH`&#x27;inize eklenir.

En son indirmeler: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> Yükleyici ayrıca, hazır uygulamaları açan kullanışlı başlatıcı komut dosyaları (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) içerir; bu komut dosyaları [CLI Kullanıcı Kılavuzu](../CLI.md) içinde ele alınmıştır ve burada tekrar edilmeyecektir.

### Windows (.exe)

1. İndirme sayfasından Windows kurulum dosyasını indirin.
2. `Chloros-Setup-x.y.z.exe` dosyasını çalıştırın ve sihirbazı izleyin. Varsayılan kurulum yolu `C:\Program Files\Chloros\`&#x27;tir (CLI dosyası, yükleyici tarafından PATH&#x27;e eklenen `C:\Program Files\Chloros\cli\` klasörüne yerleştirilir yükleyici bunu PATH değişkenine ekler).
3. Güncellenen `PATH`&#x27;in algılanması için yeni bir terminal açın (`cmd.exe`, PowerShell veya Windows Terminal).

```powershell
chloros-cli --version
```

Yükleyici, `chloros-cli.exe` dosyasını sisteminizin `PATH` dizinine otomatik olarak ekler ve LATTICE kameraları için gerekli olan Arena SDK çalışma zamanını da bir araya getirir.

### Linux amd64 (.deb)

Ubuntu 22.04 LTS veya daha yeni sürümler / Debian tabanlı x86_64 iş istasyonları için.

> **Ubuntu 20.04 desteklenmemektedir.** Paketin bağımlılık listesi,
> arka ucun gerçekte hangi kütüphanelere bağlandığından türetilmiştir ve buna `libc6 (>= 2.34)` da dahildir;
> focal, glibc 2.31 ile birlikte gelir. `apt`, yüklemenin
> çalışma zamanında başarısız olmasına izin vermek yerine yüklemeyi reddeder.

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

.deb dosyası şunları yükler:
- `chloros-cli`&#x27;ten `/usr/bin/chloros-cli`&#x27;e
- Derlenmiş arka uçtan `/usr/lib/chloros/chloros-backend`&#x27;e
- Arena SDK çalışma zamanı (LATTICE kameralar için)
- Gürültü giderici modelleri, kalibrasyon paketleri ve güncelleme kanalı yapılandırması

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

amd64 .deb dosyasıyla aynı yapıya sahiptir; Jetson Orin / Orin NX / Orin Nano için optimize edilmiş bir CUDA derlemesi içerir.

### Her Makine İçin Tek Seferlik Kimlik Doğrulama

SDK/CLI çağrılarının çalışabilmesi için her platformda tek seferlik bir Chloros+ oturum açma işlemi gereklidir:

```bash
chloros-cli login user@example.com 'YourPassword'
```

Kimlik bilgileri `~/.chloros/user_session.json` dosyasında önbelleğe alınır.

### Kurulumu Doğrulama

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Chloros+ aboneliği gereklidir.**CLI için etkin bir Chloros+ planı gereklidir.**Copper**, giriş seviyesi Chloros+ kademesidir — ücretli tüm Chloros+ kademeleri CLI/SDK erişimine sahiptir; yalnızca ücretsiz**Iron** kademesi erişime sahip değildir. (Plan-kimlik eşlemesi: `0`=Iron/ücretsiz, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing) adresinden yükseltme yapabilirsiniz.
>
> Bu alt sınır, yalnızca CLI tarafından değil, arka uç tarafından da uygulanır: Ücretli bir planı olmayan ve SDK/CLI etiketli bir istek, `403 PLAN_UPGRADE_REQUIRED` hatasıyla reddedilir; bu istek ister `chloros-cli`&#x27;ten, Python, SDK veya elle oluşturulmuş bir HTTP istemcisinden gelse de reddedilir. Oturumu kapatılmış bir çağrı yapan kişiye ise bunun yerine `401 AUTH_REQUIRED` kodu verilir. Erişim, planın ödemesiz süresi boyunca (aylık planlarda 30 gün, yıllık planlarda son kullanma tarihine kadar) çevrimdışı olarak devam eder ve bu süre dolduğunda durur; `chloros-cli status` çalışmaya devam eder, böylece neden görünür (bu, kademeli sınırlamadan muaf olan SDK/CLI rotasıdır — `GET /api/license-status`).

---

## Beş Dakikalık Hızlı Başlangıç

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

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
| `-o, --output PATH` | varsayılan proje yolunuzun altında zaman damgası içeren yeni bir klasör (yapılandırılmadıkça `~/Chloros Projects`) | Oluşturulacak veya yeniden kullanılacak proje klasörü. Klasörde halihazırda bir `project.json` dosyası varsa, üzerine yazmak yerine bir `_1`/`_2` adlı bir kardeş klasör oluşturulur. |
| `-n, --project-name NAME` | otomatik (zaman damgası) | Proje adı. |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware`, Chloros+ sinir ağı tabanlı debayer kullanır; daha yavaştır ancak daha yüksek kalitedir. |
| `--vignette / --no-vignette` | `--vignette` | Vinyet düzeltmesi. |
| `--reflectance / --no-reflectance` | `--reflectance` | Yansıma kalibrasyonu (bulunursa panel hedefi kullanılır, LATTICE için seri başına NIST kalibrasyonu). LATTICE multispektral için bu, yansıma **ürünü** anahtarı olarak da işlev görür — bkz. [Ürün Bazında Dışa Aktarma Anahtarları](#ürün-başına-dışa-aktarma-anahtarları-lattice-multispektral). |
| `--ppk` | kapalı | Sidecar dosyalarından PPK GNSS düzeltmelerini uygula. |
| `--exposure-pin-1 MODEL` | kapalı | Bir Survey3 çift kameralı donanımın &quot;pin-1&quot; modelini sabitleyin. |
| `--exposure-pin-2 MODEL` | kapalı | &quot;pin-2&quot; modelini sabitleyin. |
| `--recal-interval SECONDS` | 0 | Yakalama süresinin her N saniyesinde kalibrasyon hesaplamalarının yeniden çalıştırılmasını zorlayın. |
| `--timezone-offset HOURS` | yerel | Çıktı meta verilerine kaydedilmiş saat dilimi farkını geçersiz kıl. |
| `--format FORMAT` | `TIFF (16-bit)` | `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | yok | Bitki örtüsü indeksleri (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFF’ler için iş akışı giriş noktasını zorla (Survey3 .raw bundan etkilenmez). Ayrıca, **raw içermeyen** yakalamaların tamamen işlenmesini sağlayan kaçış yolu — bkz. [Bir yakalama klasörü nasıl görünür](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | açık | Doğrusal debayering ürünü (`Debayered_Images`). Bkz. [Ürün Başına Dışa Aktarma Seçenekleri](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | açık | Ekran önizlemesini (`Preview_Images`): RGB = beyaz dengesi (varsa DAQ ışık kaynağı, yoksa gri dünya) + gama; multispec = sahte renk genişletme. |
| `--radiance / --no-radiance` | açık | float32 parlaklık değeri gönder (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | LATTICE yansıma ürünü için referans: `auto` = QA&#x27;dan geçen çerçeve içi hedef mutlak referanstır, DAQ aşağı yönlü (ρ = π·L/E) yedek; `target` = katı (DAQ ikamesi yok); `daq` = DAQ-yetkili. Bkz. [Ürün Bazında Dışa Aktarma Anahtarları](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | yok | Birim başına **ölçülen** hedef yansıma taramalarının dizini (`<serial>.csv`); bulunamadığında nominal T3/T4P spektrumlarına geri dönülür. |
| `--array-alignment / --no-array-alignment` | açık | LATTICE dizileri: Her yakalamanın `Chloros:Alignment*` XMP dosyasında damgalanan modül-modül hizalamasını, işlenmiş tüm ürünlere (debayering / önizleme / parlaklık / yansıma / indeks) uygular. Etiketleri olmayan görüntüler içinEtiketleri olmayan görüntüler için işlem yapılmaz. |
| `--array-alignment-crop / --no-array-alignment-crop` | kırpma | Hizalanmış dışa aktarımları, dizinin ortak örtüşme bölgesine kırpın, böylece tüm modüller tek bir ayak izini paylaşsın; `--no-…`, sensörün tam tuvalini korur (kaynağın dışında siyah dolgu). |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | Hizalama çarpıklığı için yeniden örnekleme. `nearest`, kaynak DN&#x27;lerini tam olarak korur (pikseller arası radyometrik değer karışımı olmaz). |

### Hedef Algılama Seçenekleri

| Bayrak | Açıklama |
| --- | --- |
| `--min-target-size PIXELS` | Dedektör için minimum panel-hedef boyutu (px). |
| `--target-clustering 0-100` | Kümeleme hassasiyeti. |
| `--target / --targets` | Giriş klasörünü yalnızca hedef panel olarak değerlendir (araştırma algılamasını atla). |

### Örnekler

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

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

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### Ürün Bazında Dışa Aktarma Seçenekleri (LATTICE multispektral)

LATTICE işleme, **tek geçişte geçerli tüm ürünlere**yayılır. Tür bazında dört anahtar — `--debayered`, `--preview`, `--radiance`, `--reflectance` —**varsayılan olarak AÇIK** durumdadır; birini devre dışı bırakmak için `--no-<type>` formunu kullanın. RGB ana kameralar her zaman yalnızca debayering uygulanmış + önizleme verisi yayar (bant başına parlaklık/yansıtma yok), bu nedenle `--radiance`/`--reflectance` bu kameralar için hiçbir işlem yapmaz. Survey3 ve `.raw` (standart yansıma/hedef yolunu izleyen) için bu anahtarlar göz ardı edilir. *(Eski `--radiometric-output {reflectance,radiance,sensor-response}` bayrağı **kaldırıldı** ve bu anahtarlarla değiştirildi; artık `sensor-response` seviyesi yoktur.)*

| Ürün | Çıktı | DAQ aşağı doğru akışı gerekli mi? |
| --- | --- | --- |
| `--debayered` | Doğrusal demosaik (`Debayered_Images`). | Hayır. |
| `--preview` | Önizleme gösterimi (`Preview_Images`): RGB = WB + gama; multispek = sahte renk genişletme. | Hayır. |
| `--radiance` | Tam radyometrik zincirden elde edilen float32 W/m²/sr/nm (`Radiance_Images`). | No. |
| `--reflectance` | uint16 yansıma ρ (`32768` = 1,0), Pix4D uyumlu. | **Evet**, QA&#x27;yı geçen çerçeve içi bir hedef onu sabitlemiyorsa (aşağıya bakın). |

`--reflectance-source`, yansıma referansını seçer:**`auto`**(varsayılan) QA&#x27;yı geçen çerçeve içi bir hedefi**mutlak referans**yapar — hedefle sabitlenmiş ampirik çizgi zincirleri, ayrılmış panellerde çapraz değerlendirmeye tabi tutulur ve ölçülen kazanan uygulanır — hedef bulunmadığında veya QA başarısız olduğunda DAQ aşağı doğru bölünmesine (ρ = π·L/E) geri dönülür;**`target`**katı bir ayardır (DAQ ikamesi yoktur);**`daq`**, DAQ’ın belirleyici olduğu davranışa geçer. Hedef geometrisi (ArUco / sabit ROI / şerit) proje hedef yapılandırmasından alınır; `--target-reflectance-dir DIR`, hedef birimin seri numarası/QR kodu ile aranan birim başına**ölçülen** taramaları (`<serial>.csv`) tutar ve yedek olarak nominal T3/T4P spektrumları yedek olarak kullanılır.

DAQ yansıma yolu, kaydedilmiş bir **`.daq`**(DAQ-U/M/E)**veya görüntülerle birlikte bulunan bir DAQ-M yerel `.csv`**dosyasından**zaman damgası eşleşen aşağı doğru ışınımı**otomatik olarak çözümler. Kamera başına veya DAQ kalibrasyon paketi yerel olarak önbelleğe alınmamışsa, iş akışı ilk kullanımda**ilk kullanımda AWS&#x27;den otomatik olarak alır** (bir kez internet bağlantısı gerektirir; `~/.chloros/` altında önbelleğe alınır).

#### Yansıma piksel değerlerini okuma (Pix4D / Metashape / kendi komut dosyalarınız)

Yansıma, tamsayı DN olarak saklanır ve **ρ = 1,0 anlamına gelen DN değeri, kaynak kameraya bağlıdır**:

| Kaynak | ρ = 1,0 değeri | Nasıl anlaşılır |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ρ 2,0&#x27;a kadar boşluk) | Dosyaya XMP `Chloros:PixelScale=32768` damgası basılmıştır. |
| Survey3 | `65535` (ρ 1,0&#x27;da kırpılmış) | `Chloros:*` XMP etiketleri yok — bu yokluk *işarettir*. |

**`Chloros:PixelScale` değerini okuyun ve ona bölün**, sabit bir değer varsaymak yerine. Etiket uint16 alanında tanımlandığından, çıkış formatları arasında `32768` olarak kalır — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` ve `TIFF (32-bit, Percent)`&#x27;in hepsi kendi-tanımlayıcıdır (önce depolanan veri türünü uint16&#x27;ya normalize edin: 8-bit&#x27;ten ×257, float&#x27;tan ×65535).

> **Tasarım gereği, ölçeklendirilmeyen bir durum vardır.** 8-bit kaynaklı bir yakalama (BayerRG8) 8-bitlik TIFF olarak yazıldığında, işleme hattı yeniden ölçeklendirme yapmak yerine değeri 0..255 aralığına *kırpar*, dolayısıyla ρ≈0,008&#x27;in üzerindeki tüm değerler 255&#x27;e düzleştirilir ve dosyada ölçek bilgisi bulunmaz. Chloros, buradaki hem `Chloros:PixelScale` hem de `MicaSense:RadiometricCalibration` ikilisini kasıtlı olarak atlar ve bunun nedenini günlüğe kaydeder. **Bir LATTICE yansıma dosyasında etiket yoksa, ölçek olduğunu varsaymayın —-hiç bölünemeyen pikselleri bölmek yerine 16 bit veya 32 bit olarak** dışa aktarın.

#### Dışa aktarıma aktarılan EXIF

`process`, kaynak çekimin **GPS bloğunu ve ExifIFD&#x27;sini** her ürüne kopyalar; bu nedenle bir
dışa aktarım, coğrafi referanslamanın yanı sıra `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` ve
`CameraSerialNumber` etiketlerini coğrafi referanslamanın yanında taşır.

**`FocalLength`, fotogrametri için zorunludur.** Pix4D,
odak uzaklığı artı irtifa değerinden hesaplar; etiket eksik olduğunda son derece hatalı bir ölçeğe başvurur. 49
çekimlik bir portakal bahçesi uçuşunda, eksik etiket 411 m × 160 m’lik bir alanı yeniden yapılandırılmış
47,8 km × 13 km&#x27;lik bir alana dönüştürdü — çoğunluğu “nodata” olan 455 MP&#x27;lik bir ortofoto, bu da GSD kontrol edilmeden önce bir döşeme sorunu ve
bir BigTIFF sorunu olarak algılandı. Ortofoto&#x27;nuz mantıksız bir
ölçekte çıkarsa, önce dışa aktarılan ürün üzerinde çalıştırın.

Kopyada kasıtlı olarak **değil** `-all:all` ile kopyalanmamıştır: IFD0’ın yapısal etiketleri, kopyalandığında LATTICE çıktısını bozar
ve `ExifImageWidth` / `ExifImageHeight`,
*kaynak* çekimi tanımladıkları için hariç tutulmuştur — boyutu değiştirilmiş bir dışa aktarım, aksi takdirde kendi rasteriyle çelişen boyutlar
taşırdı. XMP, kopyalanmak yerine doğrudan yazılır; çünkü ExifTool
, XMP bloğu kopyalandığında aynı çağrıya ait XMP etiketlerini siler (bu da MAPIR
kalibrasyon etiketlerinin kaybolmasına neden olur).

### Çıktıların kaydedildiği yer

Ürünler **proje klasörünün altına, önce kameraya göre, ardından dosya formatına göre gruplandırılmış olarak** yazılır:

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

LATTICE için kamera klasörü `LATT-<sensor>-<lens>-F<filter>`’tir (çekimin EXIF
`Model` değeriyle eşleşir) ve `<model>_<filter>`&#x27;tir; aynı sensörü ve filtreyi paylaşan ancak
lens açısından farklılık gösteren iki kamera (vinyet, görüş alanı ve distorsiyon farklılık gösterir.
klasör yapısı `--format`&#x27;i takip eder: `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32` ve
`TIFF (32-bit, Percent)` şeklindedir.

> **Dışa aktarılan her ürün, KAYNAK dosyanın adını korur.**
> `capture_…_raw.tif` dosyasının radiance formatında dışa aktarımı yine `capture_…_raw.tif` olarak adlandırılır — sadece
> `tiff32/Radiance_Images/` konumunda bulunur. **Ürünü tanımlayan dosya adı değil, klasördür**; bu nedenle
> `*radiance*.tif` için genel arama yapıldığında hiçbir şey bulunmaz; bunun yerine dizinle eşleştirme yapın.

### Işık sensörü kayıtları — kalibre edilmiş `.daq` + `.csv`

`process`, giriş klasörünüzdeki `.daq` kayıtlarını da işler ve bunu yapmak için **hiçbir**
bunu yapmak için herhangi bir görüntüye ihtiyaç duymaz: tek başına uçurulan bir DAQ-U / DAQ-M / DAQ-E tam bir
kayıttır ve yalnızca `.daq` dosyalarını içeren bir klasör geçerli bir girdidir.

Bir DAQ, kalibrasyonu **olmadan** kaydedilebilir — halka açık
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) kayıt cihazları
(`record_daq.py`) varsayılan olarak bunu yapar: ham sensör sayımlarını yazar ve dosyaya damga vurur, böylece
Chloros, o sensörün fabrika kalibrasyonunu **seri numarasına göre** alır (önce yerel önbellek,
ardından MAPIR Bulut) ve uygular. `process` sonucu geri yazar:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv`, her okuma için bir satır içerir: UTC zaman damgası, entegrasyon süresi, toplam güç,
fotopik/skotopik lüks, PPFD (ve mavi/yeşil/kırmızı ayrımı), tepe dalga boyu, ardından
sensörün kendi dalga boyu ızgarasındaki tam spektrum. `.daq`,
ikinci kez kalibre edilmeden yeniden içe aktarılır.

Başarılı olduğunda işlem, `Light-sensor products written: N (calibrated .daq + .csv)` raporunu verir.
Parantez içindeki ifade, gerçekte ne yazıldığını açıklar; dolayısıyla şu şekilde okunur:
`(RAW COUNTS — this sensor has no calibration bundle)` (paket içermeyen sensör için) ve
`(N calibrated, M raw counts)` (her ikisini de içeren klasör için). Arka ucun kendi
`[DAQ-EXPORT]` ve `[RUN-SUMMARY]` başlıklarının ifadeleri de aynı şekilde türetilir — bu
üçünden hiçbiri kalibre edilmemiş ham veriyi kalibre edilmiş olarak adlandıramaz.

Kalibrasyon paketi alınamayan bir DAQ-U / DAQ-M / DAQ-E kaydı — çevrimdışı olduğunuzda veya o sensörün dosyada kalibrasyonu bulunmadığında — **bir neden belirtilerek**`[DAQ-EXPORT]` satırında**bir gerekçeyle atlanır** ve ham sayıları içeren &quot;kalibre edilmiş&quot; bir dosya olarak asla yazılmaz.
İnternete bağlanın ve işlemi yeniden çalıştırın. Gerekçe, okuyucunun o dosya için
gerçekten belirlediği gerekçedir (okunamayan şema, paket yok, yazma hatası) ve çalıştırma
özeti **farklı** nedenleri listeler — tek bir nedenden dolayı atlanan yirmi dosya, yirmi kez tekrarlanmış
olarak değil, tek bir neden olarak okunur.

#### DAQ-A kayıtları ham sayımlar olarak dışa aktarılır

**DAQ-A** ailesi, seri başına paket sisteminden daha eskidir ve alınacak bir kalibrasyon paketi
bulunmaz — bunun yerine sahada bir yansıma hedefi kullanılarak kalibre edilir; işte
bu yüzden hiçbir zaman bir pakete ihtiyaç duymamıştır. Bu kayıtların reddedilmesi,
sayıları elde etmenin hiçbir yolunu bırakmadı, bu yüzden **farklı bir ad** altında dışa aktarılırlar:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

Dosya içindeki bir işaret yerine farklı bir dosya adı kullanılır, çünkü bu bilginin
sadece dosya adı olarak e-posta yoluyla gönderildiğinde de korunması gerekir. `.csv` başlığı
`raw spectral sensor counts (NOT irradiance)` değerini gösterir ve değerlerin dosya **içinde** karşılaştırılabilir
olduğu konusunda uyarır — ki bu, hedef tabanlı kalibrasyonun bunları tam olarak kullandığı amaçtır — ve
sensörler arasında değil. Güce bağlı fotometrik sütunlar (toplam güç, fotopik ve
skotopik lüks, PPFD), sayımlardan entegre edilmek yerine **NULL** olarak yazılır ve çalışma
özetinde `RAW COUNTS` yazdığı için, bir günlüğe &quot;dışa aktarılan&quot; bu değerler ışık şiddeti olarak okunamaz.

Eski **v1.01 / v1.02** kayıtları (bunlar bir DAQ-A-SD tarafından yazılır) okuma başına bir zaman dilimi içermez,
sadece dosyanın yazılma zamanını içerir. Görüntü↔aşağı doğru ışın eşleştirici bunları hâlâ reddediyor — bir
kareyi yazma zamanıyla eşleştirmek görünmez bir şekilde yanlış olur — ancak dışa aktarıcı bunları okur ve
CSV, `clock=daq_created_on` olarak yazdırılır; bu nedenle ürün, hangi saatin belirtir.

### Notlar

- `process`, klasörünüzün Survey3, LATTICE veya karışık olup olmadığını otomatik olarak algılar.
- İlerleme durumu, Sunucu Tarafından Gönderilen Olaylar (Server-Sent Events) üzerinden aktarılır; CLI,iş parçacığı bazında canlı ilerleme bilgisini (Algılama, Analiz, İşleme, Dışa Aktarma) gösterir.
- Linux/Jetson için, CLI takas alanını kontrol eder ve büyük klasörleri işlemeden önce uyarı verebilir. Dokuya duyarlı debayer, düşük güç tüketimli Jetson&#x27;larda (Nano, Orin Nano) için otomatik olarak bir GPU frekans sınırı uygular.
- İşlem başarılı olduğunda, çalıştırma kaç adet görüntü ürünü yazdığını bildirir (`Image products written: N`).

#### Görüntü yazmayan bir çalıştırma başarısız olur

Ürün talep ettiyseniz ve çalıştırma **hiçbiri** — yalnızca `project.json` ve
`calibration_data.json` — `process` bunu bir hata olarak değerlendirir:
`Processing finished but wrote no image products.` mesajını yazdırır ve **sıfırdan farklı sıfırdan farklı** bir değerle sonlandırır, böylece bir komut dosyası bunu
tespit edebilir. Mesajda proje klasörü ve olası nedenler belirtilir:

- giriş klasörü bir yakalama olarak tanınmadı (düzeni ve `--input-level`&#x27;i kontrol edin) veya
- istenen tüm ürünler, söz konusu kameralar için uygun olmadığı gerekçesiyle atlandı (ör.
  sadece RGB kameralardan radyans/yansıtma değeri istenmesi).

`--verbose` ile yeniden çalıştırın ve arka uç günlüğünde `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` satırlarını kontrol edin;
bu satırlar, CLI çıktısına başka şekilde ulaşmayan kamera bazındaki atlamaları açıklar.

Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma — tüm ürünler kapalı ve `--indices` yok — yine de bir
**başarı** sayılır, çünkü bu durumda boş bir görüntü çıktısı doğru sonuçtur.

Aynı şekilde **sadece ışık sensörüyle yapılan bir çalıştırma** da başarılıdır: `.daq` kayıtlarının bulunduğu bir klasörde,
tanım gereği dışa aktarılacak görüntü yoktur ve çalıştırma, bunun yerine yazdığı kalibre edilmiş `.daq` / `.csv` dosyaları üzerinden değerlendirilir.

---

## `chloros-cli login`

Bu makineyi bir Chloros+ bulut hesabıyla doğrulayın. Kimlik bilgileri, `~/.chloros/user_session.json`&#x27;te güvenli bir şekilde önbelleğe alınır.

```
chloros-cli login EMAIL PASSWORD
```

### Örnekler

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (şifreden bazı kısımları çıkararak veya çoğaltarak). 401 hatası durumunda, CLI otomatik olarak `$$` resonuna eklenerek otomatik olarak yeniden dener, ardından şifrenin yinelenmeyen yarısı ile dener; yeniden deneme başarılı olursa sizi oturum açar ve bir dahaki sefere kullanmanız gereken doğru tek tırnak işareti sözdizimini görüntüler.

> **Başlıksız/komut dosyası kullanımı: önbelleğe alınmış oturum olmaması, hızlı bir hata değil, etkileşimli bir komut istemi anlamına gelir.** Arka uçta işlem başlatan herhangi bir komut (`process`, `status`, `export-status`, `time-sync`, …) önbelleğe alınmış lisans/oturum olmadan çalıştırıldığında etkileşimli bir `Email:` / `Password:` komut istemine düşer. Bu nedenle, önbellekte oturum bulunmayan bir gözetimsiz iş, girdi beklerken askıda kalır — başsız işleri planlamadan önce her makine için bir kez `chloros-cli login EMAIL PASSWORD` komutunu çalıştırın.

---

## `chloros-cli logout`

Önbelleğe alınmış oturumu temizler ve bir sonraki çağrıda yeni bir oturum açılmasını zorlar.

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

Canlı Thread-4 dışa aktarma ilerlemesini sorgular. Başka bir kabuktan `process` çalıştırılırken **bu komutu** çağırmak güvenlidir.

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

CLI&#x27;in görüntüleme dilini ayarlar (CJK, RTL ve Hint dilleri dahil olmak üzere 38 dil desteklenir). Komut dosyasını görüntüleyemeyen eski konsollarda sorunsuz bir şekilde İngilizceye geri döner.

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

---

## `chloros-cli update`

Linux / Yalnızca Jetson. `version_url` dosyasını `/etc/chloros/update.conf` ile karşılaştırır ve eşleşen `.deb` dosyasını indirip yüklemeyi önerir.

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linux/Jetson&#x27;da CLI ayrıca her başlatmada **otomatik güncelleme kontrolü** gerçekleştirir (engellemez, komutu asla geciktirmez): `/etc/chloros/update.conf` dosyasını okur, sonucu 1 saat boyunca `~/.chloros/update_cache.json`&#x27;te önbelleğe alır ve daha yeni bir sürüm mevcut olduğunda `Update available: vX.Y.Z / Run: chloros-cli update` yazdırır. Herhangi bir hata durumunda ve Windows durumunda sessizce atlanır.

---

## `chloros-cli selftest`

7 adımlı bir hızlı test çalıştırır: sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), gürültü giderici modelin varlığı, CUDA+gürültü giderici hazırlığı.

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP grandmaster durumu ve kontrolü. Chloros ana bilgisayarı PTP grandmaster&#x27;ı çalıştırır; LATTICE kameraları ve DAQ-E üniteleri, cihazlar arası zaman damgaları için bu grandmaster&#x27;a bağlı çalışır.

| Alt komut | Açıklama |
| --- | --- |
| `status` | Grandmaster durumunu, BMCA önceliklerini ve saat kimliğini gösterir. |
| `peers` | Delay_Req aracılığıyla görülen slave&#x27;leri listele (kameralar + DAQ-E sensörleri). |
| `cameras` | Kamera başına PTP durumu (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | Grandmaster sürecini yeniden başlat. |
| `set-priority --priority1 N --priority2 N` | BMCA önceliklerini geçersiz kıl. |

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
| `-s, --serial SN` | Belirli seri numarası; `--device` seçeneğini geçersiz kılar. |
| `--serials SN1,SN2,…` | Çoklu kamera çalışması için virgülle ayrılmış seri numaraları. |
| `--all` | Tespit edilen her kamerada çalıştır. |
| `--exposure US` | Mikrosaniye cinsinden pozlama süresi. |
| `--gain DB` | dB cinsinden kazanç. |
| `--pixel-format FMT` | örn. `BayerRG8`, `BayerRG12`. |
| `--width N` / `--height N` | Görüntü boyutları. |
| `--preset {default,high_quality,high_speed,triggered}` | Önceden ayarlanmış bir ayar setini uygula. `triggered` hariç tümü serbest çalışmadır; bu ayar, 2. hattaki bir donanım kenarı için kamerayı tetikler — bu hattı tetikleyen bir şey olmadığında, kamera yakalama yapmak yerine sonsuza kadar bekler, yakalama yapmaz. |
| `-o, --output DIR` | Çıkış dizini (varsayılan: `output`). |
| `--packet-size {auto,jumbo,standard,N}` | GVSP paket boyutu. `auto`, ICMP+GVSP sondaları çalıştırır; `jumbo` = 9000; `standard` = 1500. |

### LATTICE Kamera İlk Bağlantı İş Akışı

```bash
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
#      - Auto-shrink frame size to fit the wire
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
| `lattice info` | Bağlı kameraları listele (üretici, model, seri numarası, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | Optimum kamera yapılandırması için ana sistem analizini gerçekleştirir. `--no-discover`, kamera keşif aşamasını atlar (daha hızlı, yalnızca NIC analizi). |
| `lattice network [--fix] [--estimate] [--cameras N]` | NIC ayarlarını kontrol et/düzelt; bant genişliği/FPS&#x27;yi tahmin et. |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | Kararlı şemalı arka uç ağ yeteneği + dizi önerisi (`status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error` değerlerini döndürür). `auto_capped_fps`, istenen çözünürlüğü korur ancak hedef kare hızını sınırlar — `recommended.recommended_target_fps` değerini okuyun ve bunu bağlantı hedefi olarak iletin; bunu hata olarak değil, başarı olarak değerlendirin. |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | Kameraları açmadan &quot;ne olurdu&quot; analizi. **`--n-active`, sadece bu dizideki değil, ağdaki toplam kamera sayısını ifade eder**— bağımsız kameralar eşzamanlı olarak akış yaparken veya ağ bütçesi, kamera sayısını eksik hesaplayan bir talebe göre hesaplandığında (varsayılan: `len(--models)`). Her zaman toplam `Wire budget:` (talep edilen MB/s ile çarpışma güvenli tavan karşılaştırması) ve `Max cameras:` satırlarını her zaman yazdırır ve dizi kablo kapasitesini aşarsa `** OVER-SUBSCRIBED**` uyarısını verir — bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model). |
| `lattice gpu` | GPU durumunu gösterir. |
| `lattice firmware [--update] [--force] [-y\|--yes]` | Kamera donanım yazılımını kontrol eder veya günceller. Yerel `.fwa` seçimi sabitlenmiştir: `firmware/<MODEL_PREFIX>/` içindeki, derlemenin `MIN_FIRMWARE_VERSION`&#x27;siyle eşleşen dosya, mevcutsa yüklenir (yalnızca en yüksek sürüm yedek olarak kullanılır); bu nedenle, diskte hazır bulundurulan daha yeni bir satıcı görüntüsü, o sabitleme kaldırılana kadar etkin değildir — kasıtlı olarak daha yeni sürümler, imzalı AWS manifestosu aracılığıyla cihazlara ulaşır; bu, daha yeni sürümler mevcut olduğunda tercih edilir. |
| `lattice presets [--apply NAME]` | Kamera ön ayarlarını listele veya uygula. |
| `lattice status` | Canlı kamera durumu. |

#### Yakalama

| Alt komut | Amaç |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | Tek kare. **Varsayılan olarak her dışa aktarım türünü kaydeder** (`--processing all`); bkz. [Yakalama Dışa Aktarım Düzeyleri](#capture-export-levels-the-all-default). `--levels`, belirli bir alt kümeyi kaydeder (`--processing`&#x27;i geçersiz kılar); `--force-daq`, atanan DAQ okumasını, yalnızca ham verinin yakalandığı durumlarda bile bir `.daq` sidecar dosyası olarak yazar. `--jpeg-quality` = JPEG kalitesi 1–100 (varsayılan 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | Ctrl+C tuşuna basılana kadar diske aktarım. |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | Tarayıcı tabanlı canlı MJPEG önizleme. `--ae-damping` otomatik pozlama sönümlemesini(0,4–100). |

#### Sensör Ayarı

| Alt komut | Amaç |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | Herhangi bir GenICam düğümünü okur/yazar. |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | Pozlama ve AE. |
| `lattice gain [--auto] [--off] [--set DB]` | Kazanç ve otomatik kazanç. |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | Sensör ROI ve binning. |
| `lattice format [--set FMT] [--list]` | Piksel biçimi. |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | Donanım/yazılım tetikleyicisi. |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (bayrak yok = tek seferlik WB) | WB işlemleri. Yalnızca RGB/Bayer kameralar; mono M3M&#x27;de no-op (atlanır). |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | RGB ekran renk işleme zinciri. `natural` (varsayılan) ucuz canlı işleme seçeneğidir; `enhanced`, tam hub-parity görünümü için defringe + canlılık + CLAHE yerel kontrastı ekler; bu işlem, kare başına işleme maliyetinin yaklaşık 2 katıdır, dolayısıyla daha düşük bir **canlı** kare hızı — kaydedilen görüntüler her durumda tam sonlandırma kalitesinde olur. Yalnızca RGB/Bayer kameralar; mono M3M&#x27;de atlanır. |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | Doygunluk/kontrastı göster (RGB filtreli kameralar). Mono M3M&#x27;de atlanır. |
| `lattice filter [--set NAME] [--list]` | Kameranın filtre modelini ayarla (`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | Güç/termal düğümleri ölçer; düşük güçte bekleme modunu etkinleştirir/devre dışı bırakır. |

#### Kalibrasyon ve Sensörler

| Alt komut | Amaç |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | Yansıma hedefinden kalibrasyon yapın. |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` |-aşağı doğru ışık sensörü komutları. |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | Mevcut görüntülere vinyet düzeltmesi uygula. |

#### Çoklu Kamera (Geçici Oturumlar)

| Alt komut | Amaç |
| --- | --- |
| `lattice multi-info` | Senkronizasyon rolüne sahip tüm kameraları listele. |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | Her kameradan bir senkronize kare. Kalıcı bir dizi bağlıyken **varsayılan olarak tüm dışa aktarma türlerini**kaydeder; geçici dizi olmayan yedekleme**sadece debayering işleminden geçirilir** (geri kalanı için önce `array-connect` komutunu çalıştırın). |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | Senkronize kareleri (geçici). |
| `lattice multi-test [--count N]` | GPIO senkronizasyon zamanlama testi. |
| `lattice multi-detect [--line LINE] [--json]` | GPIO ana/yardımcı kablolamasını otomatik olarak algılama. |

#### Hizalama

| Alt komut | Amaç |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — ayrıca dedektör/eşleştirici ayar düğmeleri `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC ayar düğmeleri `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, çoklu kare birleştirme `[--averaging mean\|median\|inlier_weighted]`, geometrik kısıtlamalar `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, uzamsal kısıtlama `[--roi X0,Y0,X1,Y1] [--mask PATH]` ve her slave için geçersiz kılma ayarları `[--per-cam-override SN:KEY=VALUE]` (tekrarlanabilir) | Canlı kameralardan hizalama profilini hesaplayın. `--prefilter` varsayılan olarak `gradient`&#x27;e ayarlanır (kenar haritası; GUI/dizi hizalayıcıyla uyumludur — kenarlar spektral bantlar arasında korunur). `--matcher flann`, ~5000&#x27;den fazla özellik olduğunda etkili olur; `--averaging median`, tek bir hatalı yakalamaya karşı dayanıklıdır; `inlier_weighted`, eşleşme sayısına göre ağırlıklandırır; `--lock-scale` en yakın dönme açısına yansıtma yapar (ölçekleme yok), `--lock-axis` bir öteleme bileşenini sıfırlar; `--mask` her kameraya uygulanır (kamera başına ayarlar için `--per-cam-override`’i kullanın, örn.örn. `--per-cam-override 214701292:method=phase`). `--rms-threshold-px`, yeniden projeksiyon RMS değeri eşik değerini aşan bir kalibrasyonu kaydetmeyi reddeder. |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | Hizalanmış bir çokkayıt yapın. `--bit-depth` varsayılan olarak kameraya uyum sağlar; `--no-crop` tam kareyi korur (siyahla doldurur); `--interpolation` (varsayılan `linear`) ve `--border-mode`/`--border-value` (varsayılan `constant`/0) CPU warp&#x27;ını kontrol eder — GPU yolu her durumda bilineer olur. |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | Akışa hizalanmış çok bantlı çerçeveler (`align-apply` ile aynı warp ayarları). |
| `lattice align-info --profile PATH [--json]` | Profil ayrıntılarını gösterir. |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | Katman sıralamasını değiştirir. |

#### Dizin / Bitki Örtüsü Matematiği

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

Tam bayrak seti: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI/NDRE/EVI/SAVI/GNDVI/…), `--formula EXPR`, `--channel SYM=BAND` (tekrarlanabilir), `--capture-level raw|debayered|radiance|reflectance|unknown` (kaynak TIFF&#x27;te kaydedilen yakalama düzeyini geçersiz kılar; varsayılan: TIFF meta verilerinden okunur), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. `--live` ile birlikte hizalama büküm düğmeleri de geçerlidir: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **`--channel` sembollerinde büyük/küçük harf ayrımı yapılır.** Sembol tarafı, ön ayarın kanal adlarıyla tam olarak eşleşmelidir (ön ayarlarda küçük harf kullanılır; ör. NDVI = `red`, `nir` — `--list-presets`&#x27;i kontrol edin) ve bant tarafı, hizalanmış yığındaki bir bant adıyla eşleşmelidir (veya çevrimdışı modda 0 tabanlı bir bant dizini olmalıdır). `--channel red=Red_660 --channel nir=NIR_850` çalışır; `--channel RED=660` ise bir `channel_map missing entries` hatasıyla başarısız olur.

#### Kalıcı Bağlantılar (Smart-Prep, GUI&#x27;ye Eşdeğer Akış)

Bu komutlar, CLI çağrıları boyunca arka uç havuzunda kameraları açık tutar.

| Alt komut | Amaç |
| --- | --- |
| `lattice cam-connect [--serial SN]` | Havuza bir kamera ekler (tek kamera, dizi yok). |
| `lattice cam-disconnect [--serial SN] [--all]` | Serbest bırakır. |
| `lattice cam-list` | Havuzdaki kameraları listeler. |
| **`lattice array-connect`**|**Kalıcı senkronize bir diziyi bağlar (ÖNERİLEN giriş noktası).** Tam GUI akıllı hazırlık akışını çalıştırır. |
| `lattice array-disconnect [--array-id ID] [--all]` | Bir diziyi serbest bırak. |
| `lattice array-list` | Bağlı dizileri listele. |
| `lattice array-status [--array-id ID]` | Canlı fps, PTP, son hata. |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | Canlı diziden bir senkronize yakalama — Tekli / Sürekli / Aralık / En Hızlı. **Varsayılan ayar `all`&#x27;tir** (her kamera için geçerli dışa aktarım türü başına bir dosya). Atlanan kameralar (örn. RGB, parlaklık/yansıtma değerlerinden hariç tutulur) `Skipped: SN:<serial> (<reason>)` kodu ile raporlanır; yansıma için kullanılan DAQ okuma değeri de yanına kaydedilir ve `DAQ: <path>` kodu ile raporlanır. Bkz. [Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | Canlı birleşik indeks görünümünü video/GIF olarak kaydedin (izleme sınıfı; birleşik akışın açık olması gerekir). |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | Yüksek kare hızında ham Bayer seri çekimi (analiz sınıfı; çevrimdışı yeniden işleme). |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | Kaydedilmiş ham seri çekimi, kalibre edilmiş videolara dönüştürür. |

##### `array-connect` Seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--serials SN1,SN2,…` | tüm LATTICE kameralarını otomatik olarak algıla (en az 2 tane gerekir) | İlk seri numarası MASTER&#x27;dır. Belirtilmezse, algılama işlemi LATTICE (`TRI032*`) modelleriyle sınırlandırılır ve bunların tümüne bağlanır. |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO senkronizasyon hattı. |
| `--target-fps F` | otomatik | Master tetikleme atış hızı. |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | otomatik | Katman seçiciyi geçersiz kılar. |
| `--wire-ceiling-mbps MB_PER_S` | otomatik algılanır | **Ana bilgisayarın sürekli kablo bütçesi, MB/s cinsinden — tüm dizi tahsisinin dayandığı sayı.** Dizi, GVSP bozuk çerçeveleri bildirdiğinde bu değeri düşürün: otomatik değer, NIC’nin bildirilen bağlantı hızından türetilir; bu değer, USB adaptörleri, dar PCIe şeritleri ve yoğun paylaşımlı yapıları olduğundan daha yüksek gösterir. Projenin dizi yakalama bloğunda kalıcı olarak saklanır; bu nedenle, yeniden açma / CLI / SDK yeniden bağlanma işlemiyle geri yüklenir. Bkz. [Dizi sağlığı](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | otomatik | Donanım gruplandırması. |
| `--no-recommend` | kapalı | Ağ analizi adımını atla. |
| `--no-ptp` | kapalı | PTP&#x27;yi devre dışı bırakın (bu durumda kameralar arası zaman damgaları **karşılaştırılamaz**). |

### Akıllı AE / Akıllı Yakalama

LATTICE dizileri, bağlanır bağlanmaz arka planda sürekli AE çalıştırır, ancak yeni ayarlanan bir sahnenin yakınsaması biraz zaman alır. `array-capture --smart`, **kullanışlı bir paket**: dizideki her kamerada AE&#x27;nin sabitlenmesini bekler, ardından çekimi başlatır. Oturum ortasında sahne değiştirdiğinizde bunu kullanın.

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

Kararlılık politikası varsayılan olarak muhafazakârdır: 5 saniye zaman aşımı, 1,5 saniye kararlılık aralığı, ±%5 pozlama dağılım toleransı. Otomasyondan farklı bir davranış istiyorsanız, SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) aracılığıyla ayarlayın.

### Çekim Dışa Aktarım Seviyeleri (`all` varsayılanı)

Bu sürümden itibaren, `lattice capture`, `lattice multi-capture` ve `lattice array-capture` **varsayılan olarak `--processing all`** — her kamera için geçerli olan ve GUI&#x27;deki &quot;Tümünü Yakala&quot; davranışına uygun, dışa aktarım türü başına bir kayıt dosyası. Düzeyler şunlardır:

| Seviye | Çıktı | Uygulanır |
| --- | --- | --- |
| `raw` | Sensörden doğrudan tek kanallı Bayer (tek renkli kameralar: tek bant). | Tüm kameralar. |
| `debayered` | 3 kanallı BGR demosaik (tek renkli kameralar: 1 kanallı gri tonlamalı). | Tüm kameralar. |
| `radiance` | Tam radyometrik zincir üzerinden float32 W/m²/sr/nm. | Yalnızca multispektral (M3C/M3M) yalnızca — **RGB filtreli kameralar için atlanır**. |
| `reflectance` | uint16 ρ (`32768` = 1,0), Pix4D uyumlu. | Yalnızca multispektral ve **yalnızca bir DAQ bağlandığında + kamera kalibre edildiğinde**; aksi takdirde atlanır. |
| `preview` / `display` | Tam GUI önizleme zinciri (kamera profiline göre CCM + WB + gamaprofiline göre gama). `lattice capture` bunu `preview` olarak adlandırır; `array-capture`/`multi-capture`, `display` kullanır. | Tüm kameralar. |

Sadece o seviyeyi kaydetmek için tek bir seviyeyi geçirin (`--processing debayered`). `all`&#x27;i istediğinizde, belirli bir kamera için geçerli olmayan seviyeler atlanır (ve raporlanır), hata verilmez — bağlantısız veya kalibre edilmemiş bir kamera yine de `raw` / `debayered` / `preview` değerlerini alır.

Herhangi bir yansıma karesi için, fiilen kullanılan DAQ aşağı doğru ışık okuma değeri, görüntünün yanındaki **`.daq`** ek dosyasına yazılır (böylece yakalama daha sonra yeniden işlenebilir) ve bir `DAQ:` satırında raporlanır.

### Bir yakalama klasörü nasıl görünür?

Her dışa aktarma türü, `-o` altındaki **kendi alt klasörüne** yerleştirilir; bu sayede çok seviyeli bir yakalama işleminde türler asla karışmaz:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` yakalama zaman damgasıdır ve `<serial>` kamera seri numarasıdır; bu sayede senkronize edilmiş bir grup,
kameralar arasında ortak bir zaman damgasını paylaşır. **Tek bir asimetriye dikkat edin:** `display` düzeyi, `preview/` adlı bir klasörde
saklanırken, dosyaların adlarında `_display` kalır — klasör ve sonek yalnızca
bu düzey için farklılık gösterir. Bilinmeyen düzeyler, kendi adlarını taşıyan bir klasöre yönlendirilir ve eğer alt klasör
oluşturulamıyorsa, dosya kaybolmak yerine çıktı kök dizinine yazılır.

**Yakalama klasörünü yeniden işleme:**`chloros-cli process` dosyasını**yakalama kök dizinine**
(`output/`) yönlendirin. `process` normalde yalnızca belirttiğiniz klasörü içe aktarır, ancak bu klasörde
görüntü bulunmadığında ve alt klasörler varsa otomatik olarak alt klasörlere iner — böylece kök seviyesindeki alt klasörler ve
kök `.daq` tek seferde alınır. Bir yakalamanın her seviyesi, seviye başına bir görüntü yerine,
diğer seviyeler mod olarak kullanılabilir halde tek bir görüntü olarak içe aktarılır.

Bir **seviye alt klasörünü** doğrudan adlandırmak (örn. `output/raw/`) da işe yarar. Bunu yaparsanız kök
`.daq` geride kalır; bu nedenle, yeniden`raw/`&#x27;ten bir radyometrik
ürün türetirken DAQ okumasını da buraya kopyalayın veya yönlendirin — aksi takdirde zaman damgası eşleşmesi karşılaştırılacak bir referans bulamaz.

**İşleme her zaman `raw`&#x27;ten başlar.** Her yakalamada ham kare iş akışının kaynağıdır;
`debayered`, `radiance`, `reflectance` ve `preview` görüntülenebilir modlar olarak gelir, ancak işleme hattına
asla geri beslenmez. Türetilmiş bir ürünün yeniden işlenmesi, piksellerine zaten işlenmiş olan vinyet, CCM ve
parlaklık hesaplamalarını yeniden uygulayacağından, Chloros çift işleme yapmak yerine
işlemi reddeder. Bilinmesi gereken iki sonuç:

- `index/` ve `composite/` renderları **asla** işlenmez. Bunlar yakalama değil, çıktılardır —
  bir NDVI LUT renderinin anlamlı bir parlaklık yorumu yoktur.
- `raw` **olmadan** dışa aktarılan bir yakalama klasörü (örn. `array-capture --processing reflectance`)
  geçerli bir iş akışı kaynağına sahip değildir. Bu yakalamalar normal şekilde içe aktarılır ve görüntülenir, ancak `process` bunları
  atlar ve bunu belirtir:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  Eğer türetilmiş bir ürünü gerçekten geçirmek istiyorsanız — `demosaic`
  X açıkken yakalanan bir hub oturumuX ile yakalanmış bir hub oturumu veya eski bir klasör gibi — `--input-level {raw,debayered,processed}` giriş
  noktasını zorlar ve atlama ayarını geçersiz kılar. Bu bayrak, kasıtlı olarak bırakılmış bir kaçış yoludur; `auto` (varsayılan)
  ham verisi olmayan bir yakalamayı asla işleme almaz.

### Karışık Filtre Dizilerinde Atlanan Yakalamalar

Tek bir dizide RGB ve multispektral kameraları karıştırdığınızda, `array-capture --processing radiance` (veya `reflectance`), multispektral kareleri kaydeder ve RGB kameralarını **atlar** — geniş bant sensörler için Bayer başına parlaklık anlamlı değildir. CLI, kaydedilen her dosyayı (dışa aktarma seviyesiyle birlikte), yazılan her `.daq`&#x27;i ve her atlamayı açıkça yazdırır, bu nedenle dosya sayısı şaşırtıcı değildir:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

SkAtlama nedeni belirteçleri `<level>-not-applicable-to-rgb-cam` biçimini izler. Yansıtma da `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)` ile atlanabilir; ayrıca bant büyük ölçüde DAQ ışık sensörünün radyometrik olarak kalibre edilmiş aralığının (~374–974 nm) — sevk edilen SKU’lar arasında yalnızca F988, bunun desteklediği yol ise yansıma-panel iş akışıdır.

Filtre türünden bağımsız olarak her kamerayı dahil etmek için `--processing debayered` (veya `display`) kullanarak filtre türünden bağımsız olarak tüm kameraları dahil edin ya da varsayılan `all`&#x27;i kullanarak her kamera için geçerli tüm seviyeleri tek seferde alın.

---

## Yakalama Modları, Kaydediciler ve Çevrimdışı Yeniden İşleme

Bunların tümü bir **kalıcı dizi** üzerinde çalışır (önce `array-connect` komutunu çalıştırın). Bunlar, GUI yakalama panelini yansıtır.

### `array-capture` modları

`array-capture`, dört deklanşör modu ve bir dizi dışa aktarma seçeneği içeren tek bir komuttur:

| Mod | Bayrak | Davranış |
| --- | --- | --- |
| **Tek** *(varsayılan)* | (yok) | Bir senkronize yakalama grubu, ardından çıkış. |
| **Sürekli** | `--continuous` | `Ctrl+C`, `--count N` veya `--duration S`&#x27;e kadar arka arkaya geçişler. |
| **Aralıklı** | `--interval S` | Her `S` saniyede bir geçiş (her geçişin başlangıcından itibaren ölçülür), aynı sınırlar. |
| **En Hızlı** | `--fastest` | Yalnızca ham veriler + atanan DAQ okuma değeri + birleştirilmiş endeks bileşimi; parlaklık/yansıtma/ekran hesaplamalarını atlayarak karenin hızlı bir şekilde işlenmesini sağlar. `--processing raw --force-daq`&#x27;i ima eder. Kaydedilen `.daq` dosyasını daha sonra kalibre edilmiş ürünlere dönüştürün. |

Dışa aktarma seçenekleri (herhangi bir modla birleştirilebilir; tümü aynı GUI/SDK uç noktasını paylaşır):

| Bayrak | Etki |
| --- | --- |
| `--processing LEVEL` | Tek dışa aktarma düzeyi veya `all` (varsayılan). |
| `--levels L1,L2,…` | Dışa aktarma türlerinin açık alt kümesi (örn. `raw,radiance,reflectance`); **`--processing`&#x27;i geçersiz kılar**. |
| `--aligned` / `--no-aligned` | Her üyenin ham olmayan dışa aktarımını, dizinin [hizalama profiline](#alignment) göre hizalar (birlikte kaydedilmiş). Ham veriler hizalanmaz, ancak meta verilerde dönüşümü taşır. Dizinin profili yoksa, hizalanmamış duruma geri döner (uyarı ile). |
| `--index` / `--no-index` | Yapılandırılmışsa, kamera başına bitki örtüsü indeksi katmanını kaydeder veya atlar. Varsayılan: işle. |
| `--force-daq` | Seçilen hiçbir seviyenin gerektirmediği durumlarda bile (örn. yalnızca ham veri içeren bir yakalama) atanan DAQ/DLS okumasını bir `.daq` sidecar dosyası olarak kaydeder, böylece kareler çevrimdışı olarak yansıma/indeks olarak yeniden işlenebilir. |
| `--smart` | Tetiklemeden önce tüm kameralarda AE&#x27;nin sabitlenmesini bekleyin (bkz. [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF piksel sıkıştırması. `deflate` (varsayılan) = kayıpsız zlib L1 + yatay tahminci, tam çözünürlüklü kare başına ~tam çözünürlüklü kare başına ~4,1 MB; `none` = sıkıştırılmamış, kare başına ~6,3 MB ile ~5 kat daha hızlı yazma — disk izin verdiğinde maksimum sürekli hız için kullanın. Her ikisi de kayıpsızdır ve içe aktarma sırasında aynı şekilde okunur. |

> **Tekyazma TIFF + sürekli hız modeli.**Çekimler, pikseller + XMP + IFD0 Marka/Model bilgilerini içeren**tek**bir TIFF dosyası geçişinde yazılır (tamçözünürlüklü Mono12&#x27;de ölçülmüştür: sıkıştırılmış 36 ms / sıkıştırılmamış 6,5 ms; eski &quot;yaz-sonra-ExifTool ile yeniden yaz&quot; yönteminde ise ~148 ms); geriye kalan tek ExifTool işi (EXIF alt-IFD düzeltmesi) asenkron bir arka plan işleyicisinde çalışır ve bu iş hiç çalışmasa bile bir kare tamamlanmış ve içe aktarılmaya hazır hale gelir. DEFLATE sıkıştırmasının Python GIL&#x27;ini tuttuğunu unutmayın; bu nedenle sıkıştırılmış yazma işlemleri**her bir**kamera yazma iş parçacıkları arasında**paralelleştirilmez**— sensör hızında (~10,4 fps) 8 kameralı sürekli tam çözünürlüklü çekim için `--compression none`**ve** NVMe sınıfı disk (~500 MB/s sürekli yazma) gerektirir. Aynı ayar, `POST /api/camera/array/capture` üzerinde `compression` olarak sunulur.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — birleştirilmiş dizinli video/GIF (izleme sınıfı)

**canlı birleştirilmiş dizin görünümü** ne gösteriyorsa onu bir `.avi`&#x27;e (ve isteğe bağlı olarak bir `.gif`&#x27;e) kaydeder. Canlı bileşik akıştan veri aldığı için, birleştirilmiş akış açık olmalıdır (örn. dizi GUI&#x27;de önizleniyorsa) açık olmalıdır. Her 2 saniyede bir ilerlemeyi kontrol eder ve `--duration`, `Ctrl+C`&#x27;te veya kaydedici kendi kendine sona erdiğinde durur.

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--array-id ID` | sadece dizi | Hedef dizi (sadece bir tane bağlıysa atlayın). |
| `-o, --output DIR` | `output` | Çıkış dizini (arka uç yerel). |
| `--fps F` | `10` | Kayıt kare hızı. |
| `--duration S` | Ctrl+C&#x27;ye kadar | `S` saniye sonra otomatik durdurma. |
| `--gif` | kapalı | Ayrıca animasyonlu bir GIF yaz. |
| `--gif-only` | kapalı | Yalnızca GIF yaz (`.avi` yok). |

### `array-burst` — ham Bayer yüksek kare hızında seri çekim (analiz sınıfı)

Yakalama döngüsünün senkronize grup tamponunu doğrudan okur — **kalibrasyon zinciri yok, exiftool yok, canlı görüntüleme gerekmez** — böylece kameranın tam yakalama hızında çalışır. Ham kareleri + kare başına bir manifest + `<output>/bursts/<base>/` altında her bir farklı DLS okuması için bir `.daq` dosyası yazar. Çevrimdışı olarak yeniden işleyin (sonraki komut) veya hemen yapmak için `--build` parametresini .

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--array-id ID` | sadece dizi | Hedef dizi. |
| `-o, --output DIR` | `output` | Çıktı dizini (patlama `<DIR>/bursts/<base>/`&#x27;e kaydedilir). |
| `--duration S` | Ctrl+C&#x27;ye kadar | `S` saniye sonra otomatik olarak durur. |
| `--max-frames N` | sınırsız | `N` ham karelerden sonra `N` ham kare sayısından sonra otomatik durdurma. |
| `--build` | kapalı | Durdurulduktan sonra, seri veriyi hemen yeniden işleyin (`array-build-video` ile aynı). |
| `--products …` | `combined:index` | `--build` ile: hangi videoların oluşturulacağı (aşağıya bakın). |
| `--fps F` | `10` | `--build` ile birlikte: çıktı videosunun kare hızı. |
| `--save-tiffs` | kapalı | `--build` ile: ayrıca kare başına kalibre edilmiş TIFF&#x27;leri de kaydedin. |
| `--gif` | kapalı | `--build` ile: ayrıca animasyonlu GIF&#x27;ler yazın. |

### `array-build-video` — kaydedilmiş bir seri çekimi çevrimdışı olarak yeniden işleyin

Her bir ham kareyi, en yakın kaydedilmiş `.daq` değeriyle zaman açısından eşleştirir ve onu **ithalat boru hattıyla aynı parlaklık / yansıma / indeks zincirinden** geçirerek bir veya daha fazla video oluşturur.

`--products`, `kind:level` öğelerinden oluşan virgülle ayrılmış bir listedir; burada `kind` ∈ `per_cam` | `combined` ve `level` ∈ `radiance` | `reflectance` | `index`&#x27;tir. Çıplak bir `level` (`kind:` yoksa) varsayılan olarak `per_cam` olur. Varsayılan değer `combined:index`&#x27;tir.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| Bayrak | Hata | Açıklama |
| --- | --- | --- |
| `--burst-dir DIR` | (gerekli) | Seri çekim klasörünün yolu (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` listesi, yukarıdaki gibi. |
| `--fps F` | `10` | Çıkış videosunun kare hızı (fps). |
| `--save-tiffs` | kapalı | Videonun yanı sıra her kare için kalibre edilmiş TIFF dosyalarını da kaydedin(). |
| `--gif` | kapalı | Ayrıca animasyonlu GIF&#x27;ler de yazın. |

> **Doğru kayıt cihazını seçin.** `array-record` *izleme sınıfı*dır — ekranda görüntülenen canlı kompoziti yakalar ve akışın açık olması gerekir. `array-burst` → `array-build-video` *analiz sınıfı*dır — ham sensör verilerini tam hızda kaydeder ve daha sonra kalibre edilmiş parlaklık/yansıtma/indeks videolarını yeniden oluşturur; canlı görüntüleme gerektirmez.

### Mono (M3M) Tek Bantlı Kameralar

**M3M**serisi, Bayer**M3C**&#x27;nin mono versiyonudur: kamera başına bir dar bantlı girişim filtresi bulunur (`M3M-<lens>-F<wavelength>`, ör.örn. `M3M-L87-F685`), bu sayede sensör, Bayer mozaiği olmadan**tek bir gri tonlama bandı** sunar. Demosaikleme gerektiren bir şey yoktur, ayrılması gereken kanallar arası parazit yoktur ve ayarlanması gereken beyaz dengesi yoktur — tüm RGB ekran renk işleme süreci burada geçerli değildir.

Bunun CLI için anlamı şudur:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**tek renkli bir kamera algılar ve anlamsız ayarları zorlamak yerine**tek satırlık bir mesajla atlar**. Aynı oturumda bir RGB/Bayer M3C kamera ile çalışırken hala normal şekilde çalışırlar.
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** hala çalışır — parlaklık ve yansıma, *bant başına* radyometrik haritalardır ve tek bir bant için mükemmel şekilde tanımlanmıştır. Mono kareler, **kimlik** sensör tepki matrisi taşır (3×3 ayrıştırma yoktur), bu nedenle düzlem kalibrasyon hesaplamalarından hiç etkilenmeden geçer.
- **Tek bir mono kamera, bitki örtüsü indeksi üretemez.**NDVI/NDRE/vb. en az iki banda ihtiyaç duyar (örn. Red + NIR). Mono donanımdan bir indeks elde etmek için, farklı dalga boylarında**birkaç** M3M kamerayı yönlendirin, bunları tek bir çok bantlı yığın halinde hizalayın ve *bunu* indeksleyin:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` sembolleri, ön ayarın kanal adlarıyla **tam olarak** eşleşmelidir (büyük/küçük harfe duyarlı; NDVI, küçük harfli `red`, `nir` — bkz. `--list-presets`) ve bant tarafı, hizalanmış yığındaki bir bandı adlandırmalıdır (çevrimdışı modda 0 tabanlı bant indeksleri de kabul edilir, ör. `--channel red=0 --channel nir=1`).

Yığın boyunca ayırt edici unsur, model dizesindeki `M3M` belirtecidir (bu, bir `M3C` dizesinde asla görünmez) ve GUI/SDK&#x27;e `is_mono` olarak yansıtılır.

---

## Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması (LATTICE dizileri)

LATTICE kameralar, GVSP verisini ana bilgisayarın Ethernet adaptörü üzerinden aktarır; bu nedenle çoklu kamera dizilerinde adaptörün **sürücüsü**ve**alma halkası boyutu**, bağlantı hızı kadar önemlidir. Yanlış ayarlar, Dizi Ayarları panelinde (ve `lattice network-analysis` / SDK&#x27;in `analyze_array_network()`&#x27;inde) `FRAMES WILL DROP` / `Reduce ROI to enable` hatası olarak görünür; bu durum, kameraların kendileri sorunsuz çalışsa bile geçerlidir.

### USB 10GbE adaptörleri — Realtek RTL8157 (&quot;Realtek USB 10GbE Family Controller&quot;)

| Öğe | Gerekli değer | Neden önemlidir |
| --- | --- | --- |
| **Sürücü sürümü**|**≥ v10.67 (Ocak 2026)**, INF `rtump64x64sta.inf` | Eski**2016**sürücüsü (v10.65, `rtump64x64.inf`) kapatma ve**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**ile kapatma/yeniden başlatma/uyku modunda hatalı bir şekilde yönetir. Geçiş işlemi askıda kalır (~5 dakikalık zaman aşımı), kullanıcı zorla kapatma yapar ve tekrarlanan düzensiz kapatmalar**WMI deposunu bozar**(PowerShell/araçlar `Invalid class`) ile çalışmaz hale gelir ve bir sonraki açılışta**USB yığını kilitlenir** (ağ kartı etkinleşmez; USB sürücüler sıralanmayı durdurur). Düzgün yeniden başlatmalara güvenmeden önce realtek.com&#x27;dan (veya dongle satıcısından) güncelleme alın. |
| **Alım Tamponları**— anahtar kelime `ReceiveBufferLen` |**256**(sürücü maksimum değeri) | NIC RX halkası. Sürücünün varsayılan değeri olan**32**, yalnızca ~0,26 MB kullanılabilir halka bırakır — bu, çoklu kamera patlamaları için çok yetersizdir — bu nedenle dizi paneli `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` hatasını bildirir ve bağlantıların kurulmasını engeller.**256**halka büyüktür (**laboratuvardaki 10GbE ana bilgisayarda ölçülen ~13,5 MB**), bu da RX boru hattına çoklu kamera GVSP patlamaları için gerçek bir marj sağlar. (Belirli bir yapılandırmanın gerçekten *bağlanıp bağlanmayacağı* , ham patlama-halka karşılaştırmasıyla değil, iki kontrol — **boşaltma duyarlı**kabul kontrolü ve**toplam aşırı abonelik** kontrolü — ile belirlenir; bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model).) |
| **Alım URB&#x27;leri**— anahtar kelime `PendingReceives` |**64** (maks.) | Aktarım halindeki USB istek blokları; patlama emilimi için Alım Tamponları ile birlikte artırın. |
| **Jumbo Çerçeve** — anahtar kelime `*JumboPacket` | **9014** | 9000 baytlık GVSP paketleri için gereklidir (1500&#x27;e kıyasla çerçeve başına 6 kat daha az paket). |

> ⚠️ **Bir NIC sürücüsü güncellemesi, bu gelişmiş özellikleri varsayılan değerlere SIFIRLAR.**Adaptör sürücüsünü güncelledikten veya değiştirdikten sonra, `ReceiveBufferLen=256` ve `PendingReceives=64` değerlerini**yeniden uygulayın**; aksi takdirde, &quot;donanımda hiçbir değişiklik olmamasına&quot; rağmen dizi paneli tekrar kilitlenecektir. Bu, daha önce çalışan bir sistemin aniden bağlanmayı reddetmesinin en önemli nedenidir.**Yönetici ayrıcalıklı** bir PowerShell penceresinden uygulayın (adaptör adınızı girin, örn. `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix`, USB 10GbE adaptörlerini kapsar.** Artık adaptör türünü algılar ve doğru alıcı halkası anahtar sözcüğünü ayarlar: PCIe NIC&#x27;ler için `*ReceiveBuffers`→2048 (Intel I219, vb.) için 2048&#x27;e ayarlanır; Realtek **USB** 10GbE denetleyicisi (`*ReceiveBuffers` değerini sunmayan) için ise `ReceiveBufferLen`→256 + `PendingReceives`→64&#x27;e ayarlanır. Hedefler, her sürücünün bildirdiği maksimum değere (`NumericParameterMaxValue`) sınırlandırılır, böylece aralık dışı bir değer asla yazılmaz. Bunu **yönetici** terminali üzerinden çalıştırın; kayıt defterideğişiklik, adaptörün yeniden başlatılması veya sistemin yeniden başlatılmasından sonra yürürlüğe girer. Yukarıdaki manuel `Set-NetAdapterAdvancedProperty` komutları da iyi bir alternatif olmaya devam eder — bunlar yeniden başlatma gerektirmeden anında uygulanır (adaptörü yeniden bağlar).

### Ağ temel bilgileri (tüm LATTICE bağlantıları)

- **Adresleme:** bağlantı-yerel `169.254.0.0/16` (GigE Vision LLA). Ana bilgisayar statik bir `169.254.x.x/16` alır; kameralar ve DAQ-E aynı aralıkta kendiliğinden adres atar. DHCP/ağ geçidi gerekmez.
- **Paket boyutu:**jumbo (9000) tercih edilir, ancak otomatik sonda tarafından belirlenmesine izin verin — her bağlantıda yeniden ölçüm yapar ve GVSP sondası aracılığıyla kameranın 1500 baytlık ICMP sınırını zaten aşar, böylece kablonun gerçekten taşıyabildiği her yerde jumbo boyutuna ulaşır. Yalnızca sondadan daha iyi bildiğiniz durumlarda `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` ile sabitleme yapın ve kalıcı ayarlara göre komut başına ayarları tercih edin: sabitleme, sondayı atlar; bu nedenle, yol 9000**her** yakalama, `SC_ERR_TIMEOUT -1011` ile zaman aşımına uğrar (bkz. [Ortam Değişkenleri](#environment-variables)).
- **RX halkası, `ReceiveBufferLen` ile ölçeklenir:**varsayılan `32` değerinde kullanılabilir halka ~0,26 MB&#x27;dir (herhangi bir çoklu kamera seri çekimi için çok küçüktür); maksimum `256` değerinde ise büyüktür (laboratuvardaki 10GbE ana bilgisayarda ölçülen ~13,5 MB), bu da gerçek bir yedek kapasite sağlar. Bir yapılandırmanın bağlanıp bağlanmayacağı, daha sonra aşağıda belirtilen drenaj duyarlı kabul kontrolü**ve** toplam aşırı abonelik kontrolü tarafından belirlenir — ham seri çekim ile halka boyutu karşılaştırmasına göre değil.

### Dizi fps ve seri çekim modeli

Dizi Ayarları panelini (ve `lattice analyze-array` / SDK&#x27;in `analyze_array_network`&#x27;ini) nasıl okumalı:

- **Burst, her kameranın gerçek piksel formatında kamera başına toplanır.**Mono**M3M**kameralar**Mono12 (2 B/px)**akışı sağlar;**M3C**Bayer kameralar 8 veya 12-bit akış sağlar (TRI032S, BayerRG8 talep edildiğinde bile sessizce BayerRG12 yayar). Dolayısıyla 4 kameralı tam çözünürlüklü bir kare,**hepsi 8-bit ise ~12,6 MB, ancak üç adet 12-bit mono kamera varsa ~25 MB** olur. Projeksiyon, her kameranın formatını modeline göre (kimlik önbelleği) belirler; böylece veri akışı, tek tip bir BayerRG8 varsayımı yerine kablonun gerçekte taşıdığı veriyle eşleşir.
- **Bir USB Ethernet adaptörünün hızı, teknik özelliklerinde belirtilen değerden bağımsız olarak 200 MB/s ile sınırlıdır.** Bağlantı hızını sürekli bir değere dönüştüren verimlilik tablosu PCIe’den türetilmiştir; bir USB NIC, *Ethernet* bağlantı hızını bildirir ancak USB veriyolu ve sürücüsü tarafından sınırlandırılır. Yaklaşık 1063 MB/s &quot;sürekli&quot; hız elde etmişti — bu rakam hiçbir zaman test edilmedi — ve ortaya çıkan hız ayarlaması, sağlıklı bir hedef fps değeri bildirmeye devam ederken çerçevelerin %6–18’ini bozdu. USB ile bağlanan ağ kartlarının hızı artık **200 MB/s** mutlak bir sınırla sınırlandırılmıştır (sınır veri yolundadır, bu nedenle teknik özelliklerle orantılı değildir; bir USB 1 GbE adaptörü ~80 MB/s hız sağlar ve bundan etkilenmez). Yetenek kaydındaki `wire_ceiling_source` bunu açıkça belirtir ve `nic_is_usb` bunu işaretler. Her iki durumda da `--wire-ceiling-mbps` ile geçersiz kılabilirsiniz.
- **Giriş, tüm patlama-halka karşı değil, drenaj farkındalıdır.** Eşzamanlı bir patlama, tüm patlamaya değil, yalnızca *geçici birikime* = `max(0, Σ per-cam arrival − host drain) × emit_window` sığmalıdır. Hızlı ana bilgisayar / yavaş kamera yapısında (bir **PCIe**10G ana bilgisayar + 4× 1 GbE kamera: geliş ≈ 320 MB/s, boşaltma ≈ 1063 MB/s) ana bilgisayar, kameraların doldurma hızından daha hızlı boşaltır; birikim ≈ 0 olduğundan, 25 MB&#x27;lık burst 13,5 MB&#x27;lık halkayı aşsa bile tam-res sim-emit, 25 MB&#x27;lık patlama 13,5 MB&#x27;lık halkayı aşsa bile**kabul eder**. Aynı dört kamerayı bir**USB**10GbE adaptörüne bağladığınızda boşaltma hızı 200 MB/s&#x27;dir, 1063 değil — gelen veriler boşaltma hızını aşıyor ve kayıp, daha düşük kare hızı yerine bozuk kareler olarak ortaya çıkıyor. 1 GbE ana bilgisayarda, kameraların 31,25 MB/s DLThr alt sınırı, gelen verilerin boşaltma hızını aşmasına neden olur → sistem,**doğru bir şekilde engeller**(**bu**sınıfta bir engelleme için, ROI’yi azaltın veya ≥ 2 binning kullanın). Geçiş izni,**iki** bağlantı kapısından biridir — diğeri ise aşağıdaki toplam aşırı abonelik kontrolüdür.
- **Tahmin edilen fps, ihtiyatlı bir seri alım üst sınırıdır.**Ana bilgisayar alma döngüsü şu anda her kameranın tamponunu**seri olarak**(her kamera için yaklaşık birer yayın penceresi), dolayısıyla döngü, kamera başına yayın hızı kameranın**erişim bağlantısı**(1 GbE ≈ 80 MB/s) ile sınırlandırılır, ana bilgisayar yukarı bağlantısıyla değil. 4 kameralı tam çözünürlüklü 12 bitlik bir dizi için bu değer**~2,8 fps**’dir ve ölçülen ~2,7–3,0 değeriyle uyumludur. fps değeri kasıtlı olarak**pozlamadan bağımsız** tutulmuştur; bu nedenle loş sahnelerde pozlama süresi uzadıkça gerçek değer tavan değerinin biraz altına düşebilir. Seri veri alımı, gerçek fps sınırlayıcısıdır; bunun paralel hale getirilmesi, tavan değeri tekli veri gönderim hızına doğru yükseltecektir.
- **Toplam aşırı abonelik, bağlantı kurulmasını engelleyen kesin bir faktördür.**Kamera başına bant genişliği tahsisi**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`)olduğundan, alt sınır sabitlendiğinde toplam talep (`per_cam × N`)**çarpışma güvenliği sağlayan kablo üst sınırını**(`sustained × sim_emit_factor`) değerini aşabilir. 1 GbE üzerinde pratik tam çözünürlük tavanları:**1500 MTU’da 6 kamera, jumbo modunda 9 kamera**. Bu tavan, yalnızca hattın ve alt sınırın bir özelliğidir —**çerçeve boyutundan bağımsızdır**, dolayısıyla**binning ve daha küçük ROI&#x27;lar YARDIMCI OLMAZ** (bunlar *çerçeve* başına bayt sayısını düşürür, GevSCPD hızındaki *saniye* başına bayt sayısını değil); tek çözüm, kamera sayısını azaltmak, uçtan uca jumbo çerçeve kullanmak veya daha hızlı bir ağ kartı (NIC) kullanmaktır. Belirtisi, kademeli bir fps düşüşü değil, GVSP paket kaybı olacaktır; bu nedenle `analyze-array`, elde edilebilir fps değerlerini sıfırlar ve sabit çözünürlüklü `**OVER-SUBSCRIBED**` değerini yazdırır; sabitlenmiş çözünürlükte ise `array-connect` **bağlantı kurmayı reddeder** (aksi takdirde walk-down, kareleri daha düşük çözünürlüğe indirger, bu da bu tür blokları ortadan kaldırmaz). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`, bu reddi test çalışmaları için yüksek sesli bir uyarıya indirger — bkz. [Ortam Değişkenleri](#environment-variables).

### Dizi sağlığı — hangi alt sistem çerçeve kaybediyor

Bağlı bir dizinin `GET /api/camera/array/<array_id>/capability` değeri, **10 saniyelik** bir kayan pencerede yeniden değerlendirilen canlı bir
`health` bloğu taşır. Çerçeve kaybını,
her ikisi için de zıt çözümler gerektiren iki nedene ayırır; her ikisini de belirtmeyen tek bir “eksik”
oranı bildirmek yerine:

| Alan | Anlamı | Hangi alt sistem |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (seri başına) | Çerçeve **ulaştı ancak yapısal olarak hatalıydı**— GVSP paket kaybı. |**Ağ**: kablo bütçesi, hız ayarı, NIC RX halkası, MTU |
| `never_arrived_rate_pct` (seri numarası başına) | Çerçeve **hiç gelmedi**— kamera tetiklenmedi veya kamera çıkışından hiçbir şey gönderilmedi. |**Tetik / senkronizasyon**: M8 kablosu, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Her biri için en kötü kamera oranı. | — |
| `per_cam_rate_pct` | Kamera başına birleşik eksiklik oranı (her iki neden birlikte). | — |
| `stable_for_seconds` | Her kameranın %0,01&#x27;in altında kaldığı süre. | — |

%5&#x27;in üzerinde olduğunda arka uç, bölünmeyi belirten bir `[array-health <id>] WARN` satırını günlüğe kaydeder —
ilk ihlalde, ciddiyet aralığı değiştiğinde, durum devam ettiği sürece dakikada bir kez ve
durum düzeldiğinde bir kez. Bozuk yarısı, kamera ve
neden bazında ilk vuruşta `[gvsp-corrupt <SN>]` yazdırır, ardından her 60 saniyede bir toplama yapar. Her değerlendirme yine de arka uç günlük dosyasına kaydedilir;
sayıcılar, ne yazdırılırsa yazdırılsın her tampon için ilerler.

Aynı kayıt, tüm tahsisin bağlı olduğu sayıyı bildirir:

| Alan | Anlamı |
| --- | --- |
| `wire_ceiling_mbps` | Ana bilgisayarın yürürlükteki sürekli kablo bütçesi, MB/s. |
| `wire_ceiling_source` | Bu sayının kaynağı, kelimelerle — örn. `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` veya `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, `--wire-ceiling-mbps` (veya GUI&#x27;deki **Kablo Bütçesi** alanı) tarafından ayarlandığında. |
| `nic_is_usb` | Bir USB Ethernet adaptörü için `true` — yukarıdaki 200 MB/s sınırına bakınız. |

**Yorumlama:** `gvsp_corrupt_rate_pct` değeri sıfırdan farklı ve `never_arrived_rate_pct` değeri 0 ise
bu, tetikleme ve kablo senkronizasyonunun mükemmel olduğu ve kaybın %100&#x27;ünün ağ
yolunda olduğu anlamına gelir — `--wire-ceiling-mbps` değerini düşürün ve yeniden bağlanın. Tersine bir durum ise
senkronizasyon kablosuna veya tetikleme hattına işaret eder.

> **`--target-fps`, bozuk çerçeveler için bir gösterge değildir.** GevSCPD hız ayarı,
> bağlantı kurulduğunda bir kez yazılır, bu nedenle tetikleme hızını düşürmek görev döngüsünü değiştirir,
> eşzamanlı yayın patlama hızını değil. Ölçülen 5 kat talep kesintisi herhangi bir iyileşme sağlamadı;
> kablo tavanını 240&#x27;tan 200 MB/s&#x27;ye düşürmek, aynı donanımın bozukluk oranını %10,4&#x27;ten
> 0,00 %&#x27;a düşürdü.

> **TRI032S ürün yazılımında akış ortasında otomatik daraltma özelliği mevcut değildir.** Çalışmakta olan bir dizi
> bu sorunu kendi başına düzeltemez; bağlantıyı kesip yeniden bağlayın, böylece bağlantı zamanı seçici
> yeni tavan değeriyle yeniden planlama yapabilir.

### Belirti → çözüm

| Belirti (Dizi Ayarları / bağlantı / `analyze_array_network`) | Neden | Çözüm |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` 32&#x27;ye sıfırlanıyor (genellikle sürücü güncellemesinden sonra) | `ReceiveBufferLen`→256, `PendingReceives`→64 olarak ayarlayın; paneli yeniden açın (arka uç eski halka boyutunu önbelleğe aldıysa yeniden başlatın) |
| Yeniden başlatma/kapatma sırasında donma; daha sonra `Invalid class` WMI hataları, NIC etkinleştirilemiyor, USB sürücüler kayboluyor | Eski 2016 Realtek USB 10GbE sürücüsü → BSOD `0x9F` → zorla kapatma | Adaptör sürücüsünü ≥ v10.67 (2026) sürümüne güncelleyin, ardından yukarıdaki alıcı halkası ayarlarını yeniden uygulayın |
| Bağlantı başarılı oluyor ancak alt-doğal çözünürlük döndürüyor | Smart-prep, kabloya sığması için çerçeveyi otomatik olarak küçültür | Bağlantıyı yükseltin / küçültmeyi kabul edin / `--force-tier slip-emit-and-capture` |
| Dizi, sağlıklı bir hedef fps bildiriyor ancak bunun sadece bir kısmını sağlıyor; `health.gvsp_corrupt_rate_pct` sıfırdan farklı, `never_arrived_rate_pct` 0 | Ana bilgisayarın tahmin edilen kablo bütçesi, gerçekte destekleyebileceğinden daha yüksek gösteriliyor (USB Ethernet adaptöründe, dar bir PCIe şeridinde veya paylaşılan bir yapıda tipik bir durumdur) | Daha düşük bir `--wire-ceiling-mbps` değeri ile yeniden bağlanın ve durum bloğunu tekrar kontrol edin. **`--target-fps` hariç** — GevSCPD hız ayarı bağlantı sırasında sabitlenmiştir |
| Yayınlanan gruplarda kameralar eksik; `health.never_arrived_rate_pct` sıfırdan farklı, `gvsp_corrupt_rate_pct` 0 | Tetikleme / senkronizasyon yolu — kameralar çalışmıyor, ağ sorunu değil | M8 senkronizasyon kablosunu ve `--line` değerini kontrol edin; her üyenin devrede olduğunu (`TriggerMode=On`) doğrulayın |
| `**OVER-SUBSCRIBED**` / `Wire budget`, `analyze-array` değerini aştı veya sabitlenmiş çözünürlükle bağlantı reddedildi (`array over-subscribes the wire`) | Toplam kamera başına talep (8 MB/s minimum × N kamera), çarpışmaçarpışma güvenliği kablo üst sınırını aşıyor — 1 GbE @1500 MTU&#x27;da 6 kamera tam çözünürlükte, jumbo çerçeve ile 9 kamera | Daha az kamera, uçtan uca jumbo çerçeveler veya daha hızlı bir ağ kartı. **ROI/binning yardımcı OLMAYACAKTIR** (sınır, çerçeve boyutundan bağımsızdır). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` test ortamında öncelik kazanır (paket kaybını kabul eder) |

---

## `chloros-cli daq`

Spektral sensör komutları. İki sınıf:
- **`pool-*`**— sensörü arka ucun kalıcı havuzundan yönlendiren hafif HTTP istemcileri.**Bu, desteklenen yoldur ve sevk edilen CLI&#x27;te bulunan tek yoldur.** Aktarım arkasındaki sistemin sorumluluğundadır; bu nedenle GUI, CLI ve SDK komut dosyaları, seri bağlantı noktası için rekabet etmek yerine tek bir canlı tanıtıcıyı paylaşır.
- **Diğer her şey**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — doğrudan donanım erişimi, eksiksizlik amacıyla aşağıda belgelenmiştir. Bunlar,**hiçbir dağıtım paketinde bulunmayan** `daq` Python paketine ihtiyaç duyar: derlenmiş CLI bu paketi içermez (`scripts/Build-CLI.ps1`, `--nofollow-import-to=daq`&#x27;i ayarlar ve aktarımlar `pyserial` / `bleak` / `zeroconf` ile birlikte gelir), ayrıca PyPI&#x27;daki SDK paketi de bunu içermez. Bunlar yalnızca kaynak kodundan derlendiğinde çalışır, bu nedenle bunları ulaşılabilecek bir şeyden ziyade bir MAPIR-iç geliştirme yolu olarak değerlendirin, kullanıma açık bir şey olarak değil.
- **`discover` / `list`** ise ikisinin arasında yer alır: kaynak kodundan derlendiğinde doğrudan donanım komutları olarak çalışırlar, ancak piyasaya sürülen bir derlemede `pool-discover`&#x27;e geri dönerler ve tarama işlemi arka uç tarafından gerçekleştirilir. Böylece tarama her yerde çalışır — bu önemlidir çünkü DAQ-M&#x27;nin BLE MAC adresini öğrenmenin tek yolu budur.

> **`chloros-cli daq --help`** (ve `-h` / `help`), `pool-*` alt komutlarını listeler — yardım, gerçekte çalışan komutları yansıtması için kasıtlı olarak havuz istemcisine yönlendirilir. Dağıtılmış bir derlemede doğrudan donanım alt komutunu çalıştırırsanız, eksik paketi belirten ve sizi `pool-*`’e yönlendiren açık bir hata mesajıyla sonlanır; hiçbir hata sessizce geçiştirilmez. (`discover` / `list` istisnadır — bunlar `pool-discover`’e yeniden yönlendirilir ve sorunsuz çalışır.)
>
> **Müşterinin ihtiyaç duyduğu her şeye `pool-*`** üzerinden erişilebilir — bağlantı kurma, akış, kalibre edilmiş `.daq` dosyalarını kaydetme ve kap profilini değiştirme. DAQ ayrıca, aynı havuzlu yolu kullanan Python aynı havuzlu yolu kullanan `chloros_sdk.connect_daq_sensor()` ile de çalıştırılabilir.

### DAQ Sensörüne İlk Bağlantı İş Akışı

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` Referansı

| Alt komut | Amaç |
| --- | --- |
| `daq pool-connect` (akıllı algılama) | Arka uç havuzunda bir sensör açar. |
| `daq pool-connect --port PORT` | Belirli bir seri bağlantı noktasında DAQ-U. |
| `daq pool-connect --ble` | BLE üzerinden DAQ-M, MAC otomatik olarak taranır. |
| `daq pool-connect --mac MAC` | Bilinen bir MAC adresinde BLE üzerinden DAQ-M (`--ble`&#x27;i gerektirir). |
| `daq pool-connect --eth-host HOST` | Bilinen bir ana bilgisayarda Ethernet üzerinden DAQ-E, Ethernet üzerinden bilinen bir ana bilgisayarda. |
| `daq pool-connect --eth` | DAQ-E, Ethernet üzerinden, ana bilgisayar otomatik olarak keşfedildi (mDNS + ARP yedekleme; Windows ve Linux&#x27;te soğuk ARP önbelleğinden çalışır). |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | Entegrasyon penceresi / AE durumunu ayarla. |
| `daq pool-connect --no-stream` | Bağlan, ancak henüz akışa başlamayın (`pool-stream --start` ile devam edin). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | Cap düzeltme profili. Arka uçtaki varsayılan ayar `sunshine_cosine`&#x27;tir. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Bağlanmadan, bağlanabileceğiniz sensörler için her aktarım kanalını tarayın. **DAQ-M&#x27;nin BLE MAC adresini bu şekilde bulabilirsiniz.** `daq discover` / `daq list`, piyasaya sürülen sürümlerde otomatik olarak buraya yönlendirilir. Havuzda zaten açık olan sensörler listelenmez — bağlı bir DAQ-M reklam yayınlamayı durdurur — bu nedenle bunlar için `pool-list`&#x27;i kullanın. |
| `daq pool-list` | Arka uç havuzundaki tüm sensörleri göster. |
| `daq pool-disconnect --sensor-id ID [--all]` | Serbest bırak. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | En son N spektrum çerçeveleri. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Akışı devam ettir / duraklat. |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | Bir .daq kaydını başlat / durdur. |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Çalışma sırasında kap-düzeltme profilini değiştir. |

### Doğrudan Donanım Alt Komutları (yalnızca kaynak kodunda mevcuttur — dağıtılan sürümlerde yoktur)

> Tamlık amacıyla listelenmiştir. Bunlar, `daq` Python paketinin yanı sıra `pyserial` / `bleak` / `zeroconf` paketleri gerektirir; bunların hiçbiri derlenmiş CLI veya PyPI&#x27;daki SDK sürümlerinde bulunmaz — yalnızca MAPIR kaynak kodundan çalıştırıldığında çalışırlar. **Yayınlanmış bir Chloros derlemesi kullanıyorsanız, bunun yerine yukarıdaki `pool-*` komutlarını kullanın**; bu komutlar bağlantı, akış, kayıt ve sınırlama seçimlerini kapsar.

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

Kaydedilmiş bir Chloros projesini (`cameras.json` + `sensors.json` + `project.json` içeren bir klasör) açın, bağlanın ve çalıştırın. Her şey arka uç üzerinden yönlendirilir, böylece GUI ve CLI aynı donanım durumunu üretir.

### Alt Komut Referansı

| Alt Komut | Amaç |
| --- | --- |
| `project open PATH` | Projenin cihaz manifestosunu (kameralar, diziler, sensörler) yazdırır. |
| `project devices PATH [--reconnect]` | Keşif işlemini listeler veya yeniden çalıştırır. |
| `project connect PATH [--cameras-only] [--sensors-only]` | Kaydedilmiş tüm kameraları / dizileri / sensörleri bağlar. |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Adı belirtilen bir kameradan veya diziden tek bir çekim yapar. |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | Adı belirtilen bir kameradan veya diziden N karelik seri çekim yapar (`-n/--count` varsayılan 5; `-i/--interval` kareler arası saniye, varsayılan 0). Dizi seri çekimleri, tekrarlanan senkronize grupları (eski veri denetleyicisi) tekilleştirir; böylece kısmi döngüye sahip bir dizi, bir karenin N kopyasını geri veremez; her yineleme için sonuçları yazdırır. |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | Arka uç işi aracılığıyla akıştan diske aktarım. `--poll-interval` = `/stats` sorguları arasındaki saniye sayısı (varsayılan 2,0). |
| `project sensor read PATH NAME [--json]` | En son spektrum çerçevesi. |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | .daq dosyasını kaydeder. |
| `project run PATH RECIPE.yaml` | Bir YAML/JSON yakalama reçetesini çalıştır. `--dry-run`, çalıştırmadan doğrulama yapar. |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | Bir dizi için hizalamayı hesaplar — bkz. [aşağıdaki bayrak tablosu](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | Mevcut hizalama profilini yazdırın. |
| `project align clear PATH NAME` | Önbelleğe alınmış profili silin. |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | Bir bağımlının dönüşümünü hafifçe kaydırır. |
| `project align export PATH NAME --to FILE` | Profili JSON dosyasına kaydeder. |
| `project align import PATH NAME --from FILE [--no-validate]` | Kaydedilmiş bir profili yükle. |

#### `project align calibrate` Seçenekleri

| Bayrak | Varsayılan | Açıklama |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | Hizalama yöntemi. **Bu yazımlar, `lattice align-calibrate`**&#x27;ten farklıdır; bu bayrak, `orb` / `akaze` / `phase` kısaltmalarını kullanır; bu bayrakta bu bayrakta birbirinin yerine kullanılamaz. |
| `--model {translation, rigid, affine, homography}` | `affine` | Modeli sığacak şekilde dönüştür. |
| `--frames N` | `1` | Çerçeve anlık görüntülerini ortalamaya senkronize et. |
| `--reference SN` | ana | Referans kamera serisi; diğer tüm üyeler buna göre çarpıtılır. |
| `--max-features N` | `5000` | ORB özellik sayısı sınırı. |
| `--ratio-threshold F` | `0.75` | Lowe oranı testi. |
| `--ransac-threshold-px F` | `3.0` | RANSAC iç nokta eşiği. |
| `--min-matches N` | `15` | **Kalite kontrolü** — bu kadar iç nokta eşleşmesinin altında kalan çözümleri reddet. |
| `--max-reproj-err-px F` | `4.0` | **Kalite kontrolü** — bu RMS yeniden projeksiyon hatasının üzerinde kalan çözümleri reddet. |
| `--checkerboard RxC` | — | `--method checkerboard` için kart geometrisi, ör. `9x6`. |
| `--name PROFILE` | boş | Kaydedilen JSON dosyasına gömülü profil adı. **Dizi adı değil** — bu, konumsal `NAME`&#x27;tir. |

İki kalite kontrolü, bir kalibrasyonun çözümde başarılı olmasına rağmen yine de
kaydedilmeyi reddetmesinin nedenidir: bu kontrollerden herhangi birinde başarısız olan bir profil, sonraki her
yakalamada sessizce yanlış kayıt yapacağından, kaydedilmez.

### Örnekler

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

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

### Tarif DSL

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
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
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

Desteklenen eylemler: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. `burst` eylemi, `name` (gerekli), `count` (varsayılan 5), `interval` (saniye, varsayılan 0), `output`, `format` ve `settings` (`apply` ile aynı kamera başına ayar yapısı); dizi patlamaları, `project burst` ile aynı yeni senkronize edilmiş grup izleme mekanizmasını kullanır.

Çalıştırın:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## Ortam Değişkenleri

| Değişken | Etki |
| --- | --- |
| `CHLOROS_BACKEND_URL` | Arka uç URL&#x27;i geçersiz kılar (varsayılan `http://127.0.0.1:5000`) — **yalnızca `lattice`, `project` ve `daq pool-*` komut aileleri tarafından desteklenir.** Temel komutlar (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) `http://127.0.0.1:<port>`&#x27;i sabitler ve bu değişkeni yok sayar (IPv4 sabit değeri, Windows `localhost`→`::1` ~2 s-per-request cezası), bu nedenle her zaman yerel makineyi hedeflerler. |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1`, dizinin aşırı abonelik bağlantı reddi durumunu (`pin_resolution` ile kamera başına toplam talep &gt; çarpışma güvenli kablo tavanı) durumunu, GVSP paket kaybını kabul ederek yüksek sesli uyarı ve devam etme durumuna indirger. Yalnızca test ortamında kullanın — bkz. [Dizi fps ve patlama modeli](#array-fps--burst-model). |
| `CHLOROS_CLI_MODE` | CLI tarafından ayarlanır; arka uca paralel işlemeyi etkinleştirmesini söyler. |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0`, GVSP yedekleme probunu atlar (yalnızca ICMP sonuçları). **Bu, jumbo modunu devre dışı bırakır; sadece günlüğü sessize almaz** — kamera her yolda yalnızca 1500&#x27;e kadar DF pinglerine yanıt verir, dolayısıyla jumbo&#x27;yu algılayabilen tek şey bu sondadır. Bağlantı başına kamera başına ~1 saniye tasarruf sağlar; ağ jumbo paketleri taşıyabilseydi, kablo kapasitesinin ~1,45 katı kadar maliyet getirir. SDK, ayarlandığında uyarı verir. |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | GVSP paket boyutunu N bayta sabitler; sondalamayı tamamen atlar. Kalıcı ayarlamaya kıyasla komut bazlı (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) ayarını kalıcı olarak yapmaktan: sabitlenmiş bir boyut, önündeki ağa uyum sağlamayı durdurur ve jumbo paketleri taşıyamayan bir yolda 9000&#x27;i sabitlemek, `SC_ERR_TIMEOUT -1011`hatasıyla **her** yakalama işleminin zaman aşımına uğramasına neden olur. |
| `TMPDIR` (Linux) | Nuitka tek dosya çıkarma dizinini geçersiz kılar. CLI, mevcutsa `/mnt/ssd/tmp`&#x27;i otomatik olarak kullanır. |

---

## Çıkış Kodları

| Kod | Anlam |
| --- | --- |
| `0` | Başarılı. |
| `1` | Genel hata (çoğu alt komut hatası). |
| `2` | Argüman hatası. |
| `130` | Ctrl+C ile kesildi. |

---

## Sorun Giderme İpuçları

- **&quot;Giriş gerekli&quot;** → Bu makinede `chloros-cli login EMAIL PASSWORD` komutunu bir kez çalıştırın.
- **&quot;Arka uca ulaşılamıyor&quot;** → Chloros masaüstü uygulamasını başlatın veya arka uç ikili dosyasını doğrudan çalıştırın (`chloros-backend`), veya uzaktan erişim durumunda `CHLOROS_BACKEND_URL` dosyasını kontrol edin.
- **`lattice` komutları &quot;LATTICE kamera sürücüleri bulunamadı&quot; hatasıyla başarısız oluyor** → Arena SDK çalışma zamanı ortamı yüklü değil; CLI, `win32api` ile birlikte Windows üzerinde paketlenmiş olarak gelir, ancak C çalışma zamanı GUI yükleyicisinin bir parçasıdır.
- **Dizi bağlantısı / Dizi Ayarları&#x27;nda &quot;ÇERÇEVELER DÜŞECEK&quot; veya &quot;Etkinleştirmek için ROI&#x27;yi azaltın&quot;** → Ana bilgisayar NIC alıcı halkası çok küçük (genellikle bir NIC sürücüsü güncellemesinden sonra 32&#x27;ye sıfırlanır). Bkz. [Ana Bilgisayar NIC Kurulumu ve Ayarlaması](#host-nic-setup--tuning-lattice-arrays) — `ReceiveBufferLen=256` ve `PendingReceives=64` değerlerini ayarlayın.
- **Makine yeniden başlatma/kapatma sırasında donuyor, ardından WMI `Invalid class` / NIC etkinleştirilemiyor / USB sürücüler kayıp** → `DRIVER_POWER_STATE_FAILURE` hatasına neden olan eski USB 10GbE adaptör sürücüsü (BSOD `0x9F`). Adaptör sürücüsünü güncelleyin — bkz. [Ana Bilgisayar NIC Kurulumu ve Ayarlama](#host-nic-setup--tuning-lattice-arrays).
- **Jetson takas uyarısı** → Dosyadestekli takas alanı ekleyin; CLI, tam olarak `fallocate` / `swapon` komutlarını yazdırır.
- **DAQ doğrudan komutları eksik** → Beklenen: sevk edilen `chloros-cli`, `daq` paketini kasıtlı olarak hariç tutar, bu nedenle yalnızca `pool-*` mevcuttur (PyPI&#x27;daki SDK de bunu içermemektedir). Arka uç üzerinden aynı sensörü çalıştıran `pool-*`&#x27;i bu paket arka uç üzerinden aynı sensörü çalıştırır, ya da Python&#x27;ten gelen `chloros_sdk.connect_daq_sensor()`&#x27;i kullanın.

---

## Ayrıca bakınız

- [Python SDK Referansı](sdk-reference.md) — her CLI komutunun programlama açısından eşdeğeri.
- [DAQ Sensör Kılavuzu](../daq/README.md) — sensöre özgü kablolama + kalibrasyon.
- Çevrimiçi belgeler: `https://mapir.gitbook.io/chloros/cli`
