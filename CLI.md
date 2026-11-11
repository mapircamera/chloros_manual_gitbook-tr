# CLI: Komut Satırı

> **Tam referans:**[CLI Referansı](reference/cli-reference.md),**her alt komutun tüm bayraklarını** belgeler ve AI asistanları için optimize edilmiştir — URL'sini asistanınıza yapıştırın ve çalışan bir komut isteyin: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **AI araçları için ipucu:** Bu kılavuzun herhangi bir sayfası, URL'sine `.md` eklenerek ham Markdown biçiminde erişilebilir (örn. `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), ve `https://mapir.gitbook.io/chloros/llms.txt`, LLM kullanımı için kılavuzun tamamını indeksler.

> **Asistanınızın Chloros'u kendisi çalıştırmasını mı istiyorsunuz?** Python SDK ile kurulan bir MCP sunucusu olan `chloros-mcp`, Claude Desktop, Claude Code, Cursor ve diğer MCP istemcileriyle çalışır: bkz. [Chloros'u AI Asistanlarıyla Kullanma](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## CLI
Nedir?

`chloros-cli`, Chloros
masaüstü uygulamasının kullandığı işleme motorunun komut satırı ön yüzüdür. Chloros
arka ucu (`127.0.0.1:5000` üzerindeki yerel bir sunucu) üzerinden çalışan hafif bir HTTP
istemcisidir — çoğu komut arka ucu otomatik olarak başlatır, bu nedenle bir komut dosyasının tek ihtiyacı tek bir `chloros-cli process …` çağrısıdır. Arka uç daha sonra bir sonraki komut için çalışmaya devam eder: `chloros-cli logout` komutunda (uygulama açık değilse veya bir çalıştırma ya da derleme işlemi devam etmiyorsa), `chloros-cli backend stop` komutunda ya da 30 dakika boyunca istemci, çalıştırma veya bağlı cihaz olmadığında kendi kendine durur. `chloros-cli backend status` bunu gösterir.

**Windows
10/11 (x64)**ve**Linux
(x86_64 ve JetPack 6 üzerinde NVIDIA Jetson arm64)** üzerinde, herhangi bir terminalde, GUI gerektirmeden çalışır. Yüklemenizi şu komutla doğrulayın:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

Komut gruplarına genel bakış:

* **İşleme ve hesap** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 dil — bkz. [Desteklenen Diller](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (Yalnızca Linux
/Jetson)
* **Canlı donanım** — `lattice` (LATTICE kamera kontrolü, 45'ten fazla alt komut), `daq pool-*` (DAQ ışık sensörleri), `time-sync` (PTP)
* **Otomasyon** — `project` (YAML yakalama tarifleri dahil olmak üzere, kaydedilmiş bir Chloros
projesini başsız olarak çalıştırma)

Bilmeniz gereken genel seçenekler: `--port N` (arka uç bağlantı noktası, varsayılan `5000`), `-v/--verbose`, `--restart` (arka ucu zorla yeniden başlat), `--backend-exe PATH`. Tam liste için [CLI
Referansı](reference/cli-reference.md) sayfasına bakın.

***

## Kurulum

CLI
, her platformda **Chloros
kurulum programının içinde** bulunur — ayrı bir CLI
indirme dosyası yoktur. Kurulum programını [İndirme](download.md) sayfasından edinebilirsiniz.

### Windows


Yükleyici, CLI
'yi şu konuma yerleştirir:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

konumuna yerleştirir ve bu klasörü sisteminize `PATH` bir kez ekler (yükseltme işlemi, önceki yükleyiciler tarafından bırakılan yinelenen girdileri de kaldırır) — kurulumdan sonra **yeni bir terminal açın**, böylece güncellenmiş `PATH` algılanır. Yükleyici ayrıca kurulum kök dizinine başlatıcı komut dosyalarını (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) ve**Chloros
CLI
** Başlat menüsü kısayolu yerleştirir; bunların her biri, `chloros-cli`'in kullanıma hazır olduğu bir terminal açar.

### Linux


Mimarinize uygun `.deb` dosyasını, `apt` ile yükleyin (Python
SDK
'nin önerilen gereksinimlerini atlayan `dpkg -i` dosyasını kullanmayın):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

Bu, `chloros-cli`'i `/usr/bin/chloros-cli`'e (zaten `PATH`'te mevcuttur), arka uç ve CLI
programları `/usr/lib/chloros/backend/` ve `/usr/lib/chloros/cli/`'e açılır (`/usr/lib/chloros/chloros-backend`, arka ucu başlatan bir başlatıcıdır), arka ucun LATTICE kameraları için ihtiyaç duyduğu Arena SDK
çalışma zamanı ve sistem için Python
SDK
'si `python3`'e yüklenir. Ayrıntılar için [Linux
Kurulumu](linux/linux-installation.md) bölümüne bakın.

### Doğrulama

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## Oturum Açma ve Lisanslama

CLI
(ve Python
SDK
) erişimi için **ücretli bir Chloros
+ planı** gereklidir — tüm ücretli kademelerde bu erişim mevcuttur; ücretsiz kademede ise yoktur. Bu sınırlama, CLI
ikili dosyası tarafından değil, arka uç tarafından **sunucu tarafında** uygulanır: oturumu kapatılmış bir çağrı `401 AUTH_REQUIRED` hatasıyla reddedilir, ücretsiz kademede oturum açılmış bir çağrı ise `403 PLAN_UPGRADE_REQUIRED` hatası ile reddedilir; bu durum, çağrının `chloros-cli`, SDK
veya elle yazılmış bir HTTP
istemcisinden gelmesine bakılmaksızın geçerlidir. [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) adresinden yükseltme yapın.

**Her makineden bir kez** oturum açın:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**Özel karakterler içeren şifreler**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$`, kabuk tarafından bozulur (CLI
, 401 hatasında bunu algılar ve otomatik olarak yeniden dener, ancak tek tırnak işaretleri bu sorunu tamamen ortadan kaldırır).
{% endhint %}

Oturum, `~/.chloros/user_session.json`'te önbelleğe alınır ve planın ek süre boyunca çevrimdışı olarak çalışmaya devam eder (aylık planlar için 30 gün, yıllık planlar için son kullanma tarihine kadar). `chloros-cli status`, ücretli bir plan olmasa bile çalışır; bu nedenle reddedilme nedeni her zaman görülebilir.

{% hint style="danger" %}
**Headless iş mi planlıyorsunuz? Önce oturum açın.**Arka uç oluşturma komutları (`process`, `status`, `export-status`, …)**önbelleğe alınmış oturum olmadan**çalıştırıldığında hızlı bir şekilde hata vermez — stdin üzerinde etkileşimli bir `Email:` / `Password:` komut istemine geçer. Bu nedenle, gözetimsiz bir cron işi veya CI adımı**giriş beklerken takılır**. Herhangi bir şey planlamadan önce makinede `chloros-cli login EMAIL 'PASSWORD'` komutunu bir kez çalıştırın.
{% endhint %}

***

## İlk İşleme Çalıştırmanız

`process` komutunu yakalama dosyalarının bulunduğu bir klasöre yönlendirin — Survey3
'ü (`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` veya bunların bir karışımını otomatik olarak algılar:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

İlerleme akışları, her bir iş parçacığı için ayrı ayrı canlı olarak izlenir (Algılama, Analiz, İşleme, Dışa Aktarma) ve başarılı bir çalıştırma, kaç adet görüntü ürünü yazıldığını bildirerek sona erer: `Image products written: N`; çalıştırma sırasında [clamp maskeleri](output-image-formats.md#clamp-masks) de yazılmışsa bunu `(plus M clamp masks)` izler (bunlar ürün olarak sayılmaz).



Chloros<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->
arka ucu zaten çalışıyorsa (örneğin masaüstü uygulamasınınki), `process` bunu kullanır ve projesini bu arka uçta açar. Uygulama, kendi açtığı bir proje üzerindeyken işlem yapmayı reddeder: uygulamadan işlemi başlatın veya o projeyi kapatın. Önceki bir çalıştırma hâlâ tamamlanıyorsa, reddetmeden önce 15 saniyeye kadar bekler. `--restart` yeni bir arka uç başlatır. Linux
'ta, bir systemd hizmeti tarafından çalıştırılan arka uç asla yeniden başlatılmaz: `process` onu olduğu gibi kullanır ve normalde yeniden başlatması gereken durumlarda (`--restart` veya hiçbir şey bulamayan bir içe aktarma), `sudo systemctl restart <unit>` mesajını yazdırır ve `1`'ten çıkar.

### Çıktıların nereye kaydedildiği

`process`, girdi klasörünüze değil, bir **proje klasörüne** yazar:

* `-o` belirtilmediğinde: proje, varsayılan proje klasörünüzün altında oluşturulur (GUI ile paylaşılır; `get-project-folder` / `set-project-folder` ile yönetilir; hiçbiri ayarlanmadığında, kullanıcı klasörünüzdeki `Documents/MAPIR/Chloros Projects`), adı `-n/--project-name` olarak belirlenir veya belirtilmezse bir zaman damgası (`YYYYMMDD_HHMMSS`) kullanılır.
* `-o PATH` ile: bu klasör **proje klasörüdür**. Eğer içinde zaten bir `project.json` varsa, üzerine yazmak yerine `_1`/`_2`… sonekli bir alt klasör oluşturulur. Göreli bir `PATH`, `chloros-cli`'i çalıştırdığınız klasöre göre belirlenir.

{% hint style="info" %}
**Göreceli yollar (1.3.0 sürümünde değiştirilmiştir).** Arka uç tarafından açılan her yol — herhangi bir komutta (yakalama komutlarının varsayılan `output` komutu dahil) `-o`/`--output`, `--input`, `--burst-dir`, `--profile`, `--save` ve `--mask` — komutunun çözümlenmesi, `chloros-cli` komutunu çalıştırdığınız klasöre göre yapılır; dolayısıyla `-o captures/`, `<current folder>/captures/` dosyasına yazma işlemi gerçekleştirir. 1.2.0 sürümünde arka uç, bunları kendi kurulum klasöründe çözümlüyordu; bu da "Erişim reddedildi" hatasıyla sonuçlanıyordu. Uzak bir arka uca (`CHLOROS_BACKEND_URL`) karşı yollar, yazıldığı gibi gönderilir ve o makinede çözümlenir.
{% endhint %}

{% hint style="warning" %}
**Windows
: Uzun yol seçeneği etkin değilse proje klasörünü kısa tutun.** Windows
'ta uzun yol seçeneği kapalıyken (Windows
'ın varsayılan ayarı), hiçbir dosya yolu 260 karakterden uzun olamaz ve ürünler, proje klasörünün yaklaşık 130 karakter altına yerleştirilir. `process`, başlamadan önce bunu kontrol eder: çok derin bir proje klasörü, **"Proje klasörü bu bilgisayar için çok derin"** hatasıyla reddedilir; sığabilecek en uzun proje klasörü belirlenir ve uzun yolların nasıl etkinleştirileceği gösterilir — hiçbir işlem gerçekleştirilmez. Daha kısa bir `-o` (veya `set-project-folder`) kullanın ya da Windows
uzun yollarını etkinleştirin (yükleyicideki **Windows
uzun yollarını etkinleştir** seçeneği, varsayılan olarak işaretlidir) ve Chloros
'u yeniden başlatın.
{% endhint %}

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

LATTICE için kamera klasörü `LATT-<sensor>-<lens>-F<filter>`'tir (çekimin EXIF verisiyle eşleşir: `Model`) ve Survey3
için `<model>_<filter>` (örn. `Survey3N_RGN`) şeklindedir. Biçim klasörü ise `--format`'ten sonra şu şekilde devam eder: `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32`; `TIFF (32-bit, Percent)` için.

Çok spektrumlu bir M3C kamera için, bir format klasöründe ürün klasörlerinin yanında bir `clamp_masks/` klasörü de görünebilir (örneğin, `tiff16/clamp_masks/Reflectance_Calibrated_Images/` ve `tiff32/clamp_masks/Radiance_Images/`). Bu klasör, bant ayrıştırma işleminin negatif bir değeri sıfıra sabitlediği parlaklık ve yansıma ürünlerinin piksel başına maskelerini içerir — bkz. [Sabitleme maskeleri](output-image-formats.md#clamp-masks). Hiçbir ürün klasörü maske içermez.

{% hint style="info" %}
**Dışa aktarılan her ürün, KAYNAK dosyanın adını korur.**`capture_..._raw.tif` dosyasının parlaklık dışa aktarımı yine `capture_..._raw.tif` olarak adlandırılır — sadece `tiff32/Radiance_Images/` dizininde bulunur.**Ürünü tanımlayan dosya adı değil, klasördür**; bu nedenle, `*radiance*` sonekini değil, dizini genel arama (glob) ile tarayın.
{% endhint %}

### Gerçekte kullanacağınız seçenekler

| Bayrak | Varsayılan | Ne işe yarar |
| --- | --- | --- |
| `-o, --output PATH` | varsayılan proje klasörü | Proje klasörünün konumu (yukarıya bakın). |
| `-n, --project-name NAME` | zaman damgası | Proje adı. |
| `--format FMT` | `TIFF (16-bit)` | `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`'ten biri. Chloros
, görüntüleri büyütmez: tüm 8 bitlik görüntülerde daha derin bir format `TIFF (8-bit)` olarak yazılır ve bu durum bir kez belirtilir. |
| `--indices NAME [NAME ...]` | yok | Dışa aktarılacak bitki örtüsü indeksleri (bkz. [Bitki Örtüsü İndeksleri](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = sinir ağı tabanlı debayer, daha yavaş, en yüksek kalite (Chloros
+, CUDA GPU gerektirir: CUDA GPU yoksa işlemde `standard` kullanılır; her zaman `standard` kullanılan LATTICE yakalamaları için reddedilir). |
| `--vignette / --no-vignette` | açık | Vinyet düzeltmesi. |
| `--reflectance / --no-reflectance` | açık | Yansıma kalibrasyonu; LATTICE için bu aynı zamanda yansıma ürünü anahtarıdır. |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFF'leri için iş akışı giriş noktasını zorla. |

Diğer tüm öğeler için — hedef algılama ayarlaması, PPK, pozlama pimleri, dizi hizalama bayrakları — [CLI
Referansı'ndaki `process` bölümüne](reference/cli-reference.md) bakın.

***

## Dışa Aktarılacakların Seçilmesi (LATTICE Ürünleri)

LATTICE işleme, **tek bir geçişte tüm uygun ürünlere**yayılır. Ürün başına dört anahtar**varsayılan olarak AÇIK** durumdadır; birini kapatmak için `--no-` formunu kullanın:

| Anahtar | Ürün |
| --- | --- |
| `--debayered` | Doğrusal demosaik → `Debayered_Images/`: kalibrasyon paketindeki karanlık ofseti kaldırılır, ardından kenar duyarlı demosaik uygulanır; doğrusallık düzeltmesi, düz alan veya renk ayrıştırma yapılmaz — bu, bir çekimin `debayered` seviyesinde kaydettiği dosya ile aynıdır. Sensörün doğal yönelimi korunur (XMP'ye kaydedilir). |
| `--preview` | Ekran önizlemesi (beyaz dengesi + gama; multispektral için sahte renk genişletme) → `Preview_Images/` |
| `--radiance` | float32 parlaklık, W/m²/sr/nm → `Radiance_Images/` (her zaman `tiff32/`) |
| `--reflectance` | uint16 yansıma, Pix4D uyumlu → `Reflectance_Calibrated_Images/` |

RGB
ana kameralar her zaman yalnızca debayering işleminden geçirilmiş + önizleme verisi yayar — geniş bantlı bir sensör için bant başına parlaklık/yansıtma değeri anlamlı değildir, bu nedenle bu anahtarlar bu kameralar için hiçbir işlem yapmaz. Survey3
`.raw`, anahtarları yok sayar ve standart yansıtma/hedef yolunu izler.

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (varsayılan `auto`) yansıma referansını seçer: `auto`, QA'yı geçen çerçeve içi [kalibrasyon hedefi](calibration-targets.md)'ni mutlak referans olarak belirler ve hedef bulunmadığında DAQ ışık sensörünün aşağı doğru ışık bölünmesine (ρ = π·L/E) değerine geri döner; `target` katı bir kuraldır (DAQ ile değiştirme yapılmaz); `daq`, DAQ'ya göre geçerlidir. Birim başına ölçülen hedef taramaları, `--target-reflectance-dir` ile sağlanabilir.

{% hint style="info" %}
**Yansıma piksellerinin okunması:**ρ = 1,0 anlamına gelen DN**kaynak başına**dır — LATTICE dosyaları, XMP'ye `Chloros:PixelScale=32768` damgasını vurur; Survey3
dosyaları 65535 değerini kullanır ve `Chloros:PixelScale` etiketini içermez (ancak diğer `Chloros:*` işleme etiketlerini içerir). Sabit bir değer varsaymak yerine etiketi okuyun ve ona bölün. Ayrıntılar ve kasıtlı olarak ölçeklendirilmemiş tek kenar durumu [CLI
Referansı](reference/cli-reference.md) içinde yer almaktadır.
{% endhint %}

**İşleme her zaman `raw`'ten başlar.** Türetilmiş ürünler (debayering/parlaklık/yansıtma dışa aktarımları) asla işleme hattına geri beslenmez — bunların yeniden içe aktarılması ve işlenmesi, kalibrasyon hesaplamalarının iki kez uygulanmasına neden olur; bu nedenle Chloros
bunları atlar ve bunu belirtir. `--input-level`, gerçekten bir giriş noktasını zorla belirlemeniz gerektiğinde kullanılacak kasıtlı bir kaçış yoludur.

***

## Bir Çalıştırma Başarısız Olduğunda

1.2.0 sürümünden itibaren, `process`, hiçbir sonuç göstermeden “başarılı” olmak yerine açıkça hata verir:

* **Ürün talep eden ancak hiçbir ürün yazmayan**bir çalıştırma — yalnızca `project.json` ve `calibration_data.json` — `Processing finished but wrote no image products.` mesajını yazdırır ve**sıfırdan farklı bir değerle sonlanır**, böylece komut dosyaları bunu algılayabilir. Yaygın nedenler: giriş klasörü bir yakalama olarak tanınmadı (düzeni ve `--input-level`'i kontrol edin) veya istenen tüm ürünler o kameralar için uygun değildi (ör. yalnızca RGB
kameralardan parlaklık/yansıtma değeri istenmesi).
* **Kasıtlı olarak yalnızca meta verilerle yapılan bir çalıştırma** (tüm ürünler devre dışı, `--indices` yok) yine de başarılı sayılır — bu durumda boş bir görüntü çıktısı doğru sonuçtur.
* `--verbose` ile yeniden çalıştırın ve arka uç günlüğünde, kamera başına atlamaları açıklayan `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` satırlarını kontrol edin.
* Uzun yolların devre dışı bırakıldığı Windows
'da, 260 karakterlik yol sınırını aşan çok derin bir proje klasörü, işleme başlamadan **önce** reddedilir ve sığabilecek en uzun proje klasörü kullanılır (bkz. [Çıktıların nereye kaydedildiği](#where-the-outputs-land)).
* Yakalama komutları (`lattice capture`, `lattice array-capture` ve diğer yakalama aileleri), yazamadıkları her dosyayı listeler ve `1` ile sonlandırılır; sürücü dolduğunda tekrarlanan yakalama işlemleri durur.

Çıkış kodları: `0` başarı · `1` genel hata · `2` argüman hatası · `130` Ctrl+C ile kesildi.

***

## Bitki Örtüsü Endeksleri

`--indices` komutunu bir veya daha fazla ön ayar adıyla çalıştırın; her endeks kendi `<INDEX>_Index_Images/` klasörüne yerleştirilir:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

`process --indices`'in kabul ettiği 22 ön ayar adı:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

Projede henüz listelenmemiş bir ad projeye eklenir. Önceden ayarlanmış olmayan veya kameraların bantlarının oluşturamayacağı bir ad, adını belirten bir uyarı ile atlanır. CLI
, projenin mevcut dizin listesini okuyamazsa, listeyi değiştirmek yerine olduğu gibi bırakır ve bir uyarı verir.

{% hint style="warning" %}
**Üç dizin listesi mevcuttur — bunları karıştırmayın.**GUI'nin Proje Ayarları açılır menüsünde 27 formül bulunur (`FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — bu beş formül yalnızca GUI’ye özeldir ve `--indices` için**geçerli değildir**). Canlı/çevrimdışı `lattice index --preset` komutu, kendine ait ayrı bir 22 ön ayar listesi kullanır. Formüller ve bant hesaplamaları [Multispektral İndeks Formülleri](project-settings/multispectral-index-formulas.md) bölümünde belgelenmiştir.
{% endhint %}

***

## DAQ Işık Sensörleri: Hızlı Bir Tanıtım

`daq pool-*` ailesi, arka ucun kalıcı havuzu aracılığıyla MAPIR
DAQ spektral sensörlerini (USB üzerinden DAQ-U, BLE üzerinden DAQ-M, Ethernet üzerinden DAQ-E) çalıştırır — GUI, CLI
ve SDK
hepsi tek bir canlı tanıtıcıyı paylaşır. **`pool-*`, ürünle birlikte gelen CLI
'de desteklenen DAQ yoludur**; referans olarak görebileceğiniz diğer `daq` alt komutları, MAPIR
'in dahili, yalnızca kaynak amaçlı bir yüzeydir ve sizi `pool-*`'e yönlendiren açık bir hata mesajıyla sonlanır.

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

`pool-record`, `--duration` olmadan `pool-record --stop`'e kadar çalışır; varsayılan çıktı dizini **arka uç makinesinde** `~/Documents/DAQ Live View/`'tir. Kapak düzeltme profili bağlantı anında seçilir (`--cap-id`, arka uç varsayılanı `sunshine_cosine`) ve canlı olarak `pool-set-cap` ile canlı olarak değiştirilebilir — kap profil ve sensörün kalibre edilmiş aralığı bu kılavuzun DAQ bölümlerinde ele alınmaktadır.

{% hint style="warning" %}
**Çoklu NIC'li bir ana bilgisayarda DAQ-E:** Önyüklemeden sonraki ilk `pool-connect --eth` otomatik keşif işlemi, sensör sağlam olsa bile başarısız olabilir. `--eth-host <ip-or-hostname>` güvenilir seçenektir — keşif işlemi sonuçsuz kaldığında bunu kullanın.
{% endhint %}

***

## LATTICE Kameralar, PTP ve Proje Otomasyonu

`lattice` ailesi (45'ten fazla alt komut), LATTICE kameralarla ilgili işleri uçtan uca kapsar: keşif, tekli çekimler, GUI'nin akıllı hazırlık bağlantı akışıyla kalıcı senkronize diziler, canlı tarayıcı önizlemesi, hizalama, indeks hesaplamaları ve ana bilgisayar-NIC tanılama. Bir örnek:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

Bunun yanı sıra: `chloros-cli time-sync`, Chloros
ana bilgisayarının çalıştırdığı PTP grandmaster hakkında rapor verir (LATTICE kameralar ve DAQ-E sensörleri, cihazlar arası zaman damgaları için bu grandmaster'a bağlıdır), ve `chloros-cli project`, kaydedilmiş bir Chloros
projesini açar ve komut dosyası ile yazılmış YAML yakalama tarifleri de dahil olmak üzere kameralarını, dizilerini ve sensörlerini başlıksız olarak çalıştırır.

Bu üç grup (`lattice`, `project`, `daq pool-*`) aynı zamanda **uzak** bir arka ucu çalıştırmak için `CHLOROS_BACKEND_URL` komutunu destekleyen tek ailelerdir; temel komutlar her zaman yerel makineyi hedefler.

Ayrıntılı adım adım kılavuzlar bu kılavuzun LATTICE bölümlerinde yer almaktadır; tüm bayraklar [CLI
Referansı](reference/cli-reference.md) bölümünde bulunur.

***

## Sorun Giderme: En Sık Karşılaşılan 5 Sorun

| Belirti | Çözüm |
| --- | --- |
| `Login required` veya zamanlanmış bir iş, `Email:` isteminde takılır | Bu makinede `chloros-cli login EMAIL 'PASSWORD'` komutunu bir kez çalıştırın — önbelleğe alınmış oturum olmayan komutlar, hızlı bir şekilde hata vermek yerine etkileşimli olarak çalışır. |
| `backend unreachable` | Chloros
masaüstü uygulamasını başlatın veya arka ucu kendiniz başlatın: Windows
'ta `"C:\Program Files\Chloros\backend\chloros-backend.exe"` (kurulum klasörünüzün `backend` klasörü; `PATH`'te yoktur), Linux
'ta `chloros-backend`. `chloros-cli backend status`, birinin çalışıp çalışmadığını gösterir; `lattice`/`project`/`daq pool-*` hiçbir zaman bir arka uç başlatmaz. Bunları uzak bir arka uca yönlendirirseniz, `CHLOROS_BACKEND_URL` dosyasını kontrol edin. |
| Dizi bağlantısı engellendi: `FRAMES WILL DROP` / `Reduce ROI to enable` | Ana bilgisayarın ağ kartı (NIC) alım halkası varsayılan ayarlara sıfırlandı — bu, genellikle bir ağ kartı sürücüsü güncellemesinden sonra, daha önce çalışan bir sistemin bağlanmayı reddetmesinin en yaygın nedenidir. `chloros-cli lattice network --fix` komutunu **yükseltilmiş** bir terminalden çalıştırın (veya `ReceiveBufferLen=256`, `PendingReceives=64` ayarlarını yapın); referans kılavuzundaki *Ana Bilgisayar Ağ Kartı Kurulumu ve Ayarlaması* bölümüne bakın. |
| `daq` alt komutu sonlandırılıyor: "tam DAQ paketi gerektirir…" | Arka uç üzerinden aynı sensörü yöneten `daq pool-*` komutlarını kullanın: bağlanma, akış, kayıt, kap seçimi ve bir DAQ-E için ağ ayarları, yeniden başlatma ve ürün yazılımı güncellemeleri (veya Python
'dan `chloros_sdk.connect_daq_sensor()`). |
| Jetson, büyük klasörler öncesinde bir takas uyarısı görüntüler | Dosya tabanlı takas ekleyin — CLI
, çalıştırılması gereken tam `fallocate`/`swapon` komutlarını görüntüler. |

***

## Yardım Alma

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **Tüm bayraklar, tüm alt komutlar:** [CLI
Referansı](reference/cli-reference.md)
* **Python
eşdeğeri:** [Python
SDK
](api-python-sdk.md) ve [SDK
Referansı](reference/sdk-reference.md)
* **Destek:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
