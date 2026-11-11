# API: Python SDK

{% hint style="info" %}
**API&#x27;nin tamamını mı arıyorsunuz?** Bu sayfa, uygulamalı bir öğreticidir. Her bir genel sınıf, yöntem, tam imza ve kopyala-yapıştır yapılabilen örnek, AI asistanları için optimize edilmiş [SDK Referansı](reference/sdk-reference.md) içinde yer almaktadır.**Bir AI asistanıyla mı çalışıyorsunuz?** Bu URL&#x27;yi sohbete yapıştırın, böylece Chloros&#x27;nin 1.3.0 sürümüne ait güncel ve eksiksiz API&#x27;ye erişebilirsiniz:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Bu kılavuzun her sayfası, küçük harfli slug + `.md` adresinde ham markdown formatında mevcuttur ve kılavuzun tamamı `https://mapir.gitbook.io/chloros/llms.txt` adresinde indekslenmiştir.
{% endhint %}

**Chloros Python SDK** (PyPI&#x27;da `chloros-sdk`), masaüstü uygulamasının yapabileceği her şeyi Python üzerinden yönetir: toplu görüntü işleme, canlı LATTICE kamera ve dizi kontrolü, DAQ ışık sensörü oturumları ve kaydedilmiş proje otomasyonu. Bu, GUI ve CLI&#x27;nin kullandığı yerel arka uç (`127.0.0.1:5000`&#x27;te HTTP) üzerinde ince bir katmandır; dolayısıyla davranış, üç arayüzde de aynıdır.

## Kurulum

SDK, işleme arka ucunu ve donanım çalışma zamanlarını da sağlayan Chloros masaüstü paketi ile birlikte gelir ve sürümleri her zaman birbiriyle uyumludur.

**Adım 1 — Chloros&#x27;u kurun.** Windows: [İndirme](download.md) sayfasından masaüstü yükleyicisini (varsayılan yol `C:\Program Files\Chloros\`) çalıştırın. Linux: `.deb` paketini `sudo apt install ./chloros_1.3.0_<arch>.deb` ile kurun ([Linux Kurulumu](linux/linux-installation.md)).**Adım 2 — SDK&#x27;yi kontrol edin** (Python 3.7+). Genellikle kurulacak bir şey yoktur:

* **Windows:** Yükleyici, pakete dahil olan SDK tekerleğini sisteminizdeki Python&#x27;a yükler (Python yüklü değilse, bu adımı atlar).
* **Linux:** `.deb`, sistem SDK&#x27;sini `python3`&#x27;e yükler. Gereksinimleri olan `python3-numpy` ve `python3-requests`, paket tarafından önerilmektedir: `apt install` bunları içerirken, `dpkg -i` içermez.
* **Bir virtualenv**, sistem paketlerini görmez; pakete dahil olan wheel dosyasını içine yükleyin: Linux&#x27;ta `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`&#x27;i veya Windows kurulum klasörünün `sdk` klasöründeki `chloros_sdk-*.whl`&#x27;i (varsayılan olarak `C:\Program Files\Chloros\sdk\`) yükleyin.

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

Chloros paketi bulunmayan ve SDK&#x27;si başka bir makinedeki arka ucu çalıştıran bir makinede ([Yalnızca pip ile kurulumda neler elde edilir](#what-a-pip-only-install-gets) bölümüne bakın), PyPI&#x27;dan şunu yükleyin: `pip install chloros-sdk`. PyPI, sürüm derlemelerinde güncellenir, böylece en son kararlı sürümle eşleşir.

**Adım 3 — Her makinede bir kez oturum açın:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Kimlik bilgileri `~/.chloros/`&#x27;te önbelleğe alınır (her iki platformda da). Windows&#x27;ta, masaüstü uygulamasının Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> sekmesinden de aynı şekilde oturum açabilirsiniz. SDK, ücretli bir Chloros+ planı gerektirir — aşağıdaki [Lisans gereksinimi](#license-requirement) bölümüne bakın.

| Gereksinim | Ayrıntılar |
| --- | --- |
| **Chloros&#x27;un kurulu olması** | Windows: masaüstü yükleyicisi; Linux: `.deb` paketi (arka uç ikili dosyasını ve SDK&#x27;yi sağlar) |
| **Python** | 3.7 veya üzeri (3.10 sürümünde geliştirilmiş/test edilmiştir) |
| **İşletim sistemi** | Windows 10/11 64-bit, Ubuntu 22.04 LTS veya daha yeni bir sürüm, ya da NVIDIA Jetson (JetPack 6) |
| **Lisans** | Aktif Chloros+ oturumu, herhangi bir ücretli plan (Copper veya üstü) |

## 60 saniyede sonuç

Tek bir çağrı ile proje oluşturulur, klasör içe aktarılır, işleme yapılandırılır ve iş akışı çalıştırılır — arka uç henüz çalışmıyorsa otomatik olarak başlatılır:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(Linux&#x27;te Linux yollarını kullanın: `/home/user/drone_images/flight001`. SDK her iki platformda da aynı şekilde çalışır.)

Bir LATTICE yakalama klasörünü mi işliyorsunuz? LATTICE ile uyumlu sarmalayıcıyı kullanın — doğru varsayılan ayarları uygular (panel hedefi algılaması yok, standart debayer):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — tam iş akışı kontrolü

Tek satırlık komutların ötesinde herhangi bir işlem için `ChlorosLocal`&#x27;i kullanın. Bu, halihazırda çalışmakta olan bir arka uç (masaüstü uygulamasınınki) kullanır veya ilk kullanımda yüklü olanı başlatır (`auto_start_backend=True`; Windows&#x27;ta Chloros kurulum klasörünün `backend` klasöründen, varsayılan olarak `C:\Program Files\Chloros\backend\`), projeleri oluşturur ve yapılandırır, ilerlemeyi izler ve işlem sonrası bir özet sunar.

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

{% hint style="info" %}
`localhost` ile değiştirmeyin, varsayılan `http://127.0.0.1:5000`&#x27;i koruyun — Windows&#x27;ta, `localhost` önce `::1` olarak çözümlenir ve yalnızca IPv4 destekleyen arka uçta istek başına yaklaşık 2 saniye sürer.
{% endhint %}

Bunu bir bağlam yöneticisi olarak kullanın. Bloktan çıkmak, bu örneğin arka uç kullanımını sonlandırır, arka ucun kendisini değil: arka uç bir sonraki komut dosyası için çalışmaya devam eder ve 30 dakika kullanılmadığında kendi kendine durur. `shutdown_backend()`, bu örneğin başlatılmasını durdurur.

```python
import chloros_sdk

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

`configure()` şu anahtar kelimeleri kabul eder: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` ve `custom_settings`. Ana değerler:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE&#x27;ye özgü ayar düğmeleri (`input_level`, `radiometric_output`, `array_alignment*` ailesi), tam değer tablolarıyla birlikte [SDK Referansı](reference/sdk-reference.md#supported-values) bölümünde tam değer tablolarıyla birlikte belgelenmiştir.

### İlerlemeyi izleme

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### Çalışma sonrası özeti okuma — ve boş çalıştırmaları yakalama

İşlem tamamlandığında, `process()`, arka ucun işleme özetini `result["summary"]` olarak ekler. `summary["hints"]` içindeki her bir giriş, dikkat çekici her şeyi açıklayan tam bir cümledir — örneğin, bir çalıştırmanın neden sıfır çıktı ürettiği — ve her ipucu ayrıca bir Python `UserWarning` olarak yeniden yayınlanır; böylece, sözlüğü hiç incelemeniz gerekmese bile boş çalıştırmalar kendi kendilerini teşhis eder:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**Bir çalıştırma hiçbir görüntü üretmediğinde `process()` hatası ortaya çıkmaz.** Bu, SDK ile CLI’nin kasıtlı olarak farklılık gösterdiği tek noktadır: `chloros-cli process`, &quot;ürünler talep edildi, hiçbiri yazılmadı&quot; durumunu bir hata olarak değerlendirir ve sıfırdan farklı bir değerle sonlanır; oysa SDK normal şekilde geri döner ve durumu `summary` / ipuçları aracılığıyla bildirir. İş akışınızın boş bir çalıştırmada durması gerekiyorsa, bunu kendiniz kontrol edin — bir istisnaya güvenmek yerine `summary`&#x27;i inceleyin (veya proje klasörü altındaki dosya sayısını sayın).
{% endhint %}

## Smart Connect — canlı donanım

Üç yardımcı program, arka uç donanım havuzunda kalıcı oturumlar açar — bu havuz, CLI, diğer komut dosyaları ve GUI’nin kamera sekmelerinin kullandığı havuzla aynıdır; böylece SDK komut dosyaları, seri bağlantı noktaları veya ağ bant genişliği için rekabet etmeden bunlarla bir arada çalışabilir. Üçü de, çalışmakta olan bir arka uç yoksa yerel bir arka ucu otomatik olarak başlatır. GUI’nin DAQ paneli havuzun bir parçası değildir: bir komut dosyasından açmadan önce GUI’de bir ışık sensörünü kapatın.

### Tek bir LATTICE kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**Göreceli yollar, komut dosyanızın çalıştığı konuma göre belirlenir (1.3.0 sürümünde değiştirilmiştir).** Yerel bir arka uç kullanıldığında, göreceli bir `output_dir` (varsayılan `"output"` dahil) — ve arka ucun açtığı diğer yollar (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — komut dosyanızın çalışma dizinine göre çözümlenir; bu nedenle `cam.capture("output/")`, `<current folder>/output/` dosyasına yazma işlemi gerçekleştirir. 1.2.0 sürümünde, kurulu bir arka uç bunları kendi kurulum klasörüne göre çözümlüyor ve &quot;Erişim reddedildi&quot; hatasıyla sonuçlanıyordu. Uzak bir arka uç söz konusu olduğunda, yollar yazıldığı gibi gönderilir.
{% endhint %}

### Senkronize dizi — `connect_array`

`connect_array`, çoklu kamera kurulumları için önerilen giriş noktasıdır. GUI ile aynı akıllı hazırlık akışını çalıştırır: ağ analizi, senkronizasyon katmanı otomatik seçimi, PTP zaman senkronizasyonu, kamera başına piksel formatı seçimi, AE tohumlama ve GPIO tetikleme hazırlığı. **İlk seri numarası ana birimdir** (donanım tetikleme darbesini tetikler); geri kalanlar ise bağımlı birimlerdir.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Tetiklemeden önce tüm kameralarda otomatik pozlamanın sabitlenmesini beklemek için herhangi bir dizi yakalamaya `smart=True` ekleyin.

1.3.0 sürümündeki yenilik olarak, `ArraySession` de dosya yazmadan size senkronize kareleri doğrudan sunar:

* `arr.grab()`, tek bir tetiklemeden elde edilen her kamera için birer kareyi numpy dizileri (`{serial: array}`) olarak döndürür; `.group_id` ortak tetikleyicidir ve `.errors`, dışarıda kalan kameraları belirtir.
* `arr.frame_stream(fps=5.0)`, kendi iş akışınızda canlı işleme için her tik başına böyle bir set üretir.
* `arr.continuous("output/")`, arka ucun kendi sürekli yakalama işlevini (GUI&#x27;nin Sürekli modu) çalıştırır ve `ContinuousHandle` ile `status()`&#x27;i döndürür; `wait()` ve `stop()` ile birlikte bir `ContinuousHandle` döndürür — her tetiklemenin yüksek bir hızda kaydedilmesi gerektiğinde, yakalama döngüsü yerine bunu kullanın.

Yakalama modları (Tekli / Sürekli / Aralık / En Hızlı), kaydediciler, videoya patlama ve dizi hizalama için [SDK Referansı](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep) bölümüne bakın.

### DAQ ışık sensörü — `connect_daq_sensor`

Herhangi bir argüman olmadan, `connect_daq_sensor()` aktarım türünü akıllıca algılar (öncelik sırası: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Her çerçeve, 135 noktalı `spectrum` (kalibre edildiğinde W/m²/nm), bir `is_saturated` bayrağı ve CIE `x`, `y`, `z` değerlerini taşır. Belirli bir sensörü veya aktarım yöntemini sabitlemek için tek bir açık ipucu geçirin:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Büyük harf düzeltme profillerinin (`cap_id`) **SDK**ayar düğmesi**olmadığını** unutmayın — bunları bunun yerine `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` aracılığıyla seçin.

### Kaydedilmiş projeler — `open_project`

Kaydedilmiş bir Chloros projesi, bağlı donanımlarını (`cameras.json` + `sensors.json` ile birlikte `project.json`) korur ve `chloros_sdk.open_project(path)`, kameralarını, dizilerini ve ışık sensörlerini tek seferde yeniden bağlayabilir ve cihaz adına göre yakalama işlemlerini yönetebilir. Işık sensörleri, kaydedilmiş ayarları ve çekim sınırlarıyla birlikte çalışan bir Chloros arka ucu üzerinden yeniden bağlanır. Referansta [Proje Otomasyonu](reference/sdk-reference.md#project-automation--chlorosproject) bölümüne bakın.

## Yalnızca pip içeren bir kurulumda elde edilenler

Donanım yüzeylerini kullanmadan önce modül düzeyindeki kullanılabilirlik bayraklarını kontrol edin:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

**Yalnızca** `pip install chloros-sdk` bulunan ve Chloros masaüstü paketi olmayan bir ana bilgisayarda:

* `ChlorosLocal`, `process_folder` ve `process_lattice_capture` **çalışmaz** — bunlar, masaüstü yükleyicisi içinde bulunan arka uç ikili dosyasına ihtiyaç duyar.
* Smart-connect yardımcıları (`connect_camera`, `connect_array`, `connect_daq_sensor`) tamamen HTTP istemcileridir, bu nedenle başka bir makinedeki arka uçla çalışırlar — ancak birlikte gelen arka uçlar yalnızca döngü geriye bağlanır; bu nedenle bağlantı noktasını kendiniz yönlendirmelisiniz (örn. `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) ve `backend_url="http://127.0.0.1:5000"` ile `auto_start_backend=False`&#x27;i birlikte kullanmalısınız. Bkz. [Uzak Arka Uç Modu](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Doğrudan donanımla çalışan LATTICE sınıfları (`LatticeCamera`, `CameraPool`, …) içe aktarılabilir, ancak masaüstü paketindeki Arena SDK çalışma zamanı gerektirir — bu olmadan `CAMERA_AVAILABLE`, `False` olur.
* DAQ sensörleri yalnızca bir (tünellenmiş) arka uç — `connect_daq_sensor()` veya bir projenin `proj.sensors`&#x27;i — aracılığıyla çalıştırılır; arka uç, sensörü kendi makinesinde açar.

## AI asistanları — `chloros-mcp`

SDK, Claude Desktop, Claude Code, Cursor ve diğer MCP istemcilerinin Chloros&#x27;u çalıştırmasına olanak tanıyan bir MCP sunucusu olan `chloros-mcp`&#x27;i içerir: görüntüleri işler ve LATTICE kameralarını, dizilerini ve DAQ ışık sensörlerini çalıştırır. Araçları, bu SDK&#x27;nin işlevlerini kapsar. SDK ile birlikte kurulur; bu nedenle Claude Code için kurulum tek satırlık bir komutla gerçekleştirilir:

```bash
claude mcp add chloros -- chloros-mcp
```

Diğer istemciler: [Chloros&#x27;u AI Asistanlarıyla Kullanma](ai-assistants.md). Seçenekler ve tüm araçlar: [MCP Sunucusu (AI Asistanları)](reference/sdk-reference.md#mcp-server-ai-assistants).

## Lisans gerekliliği

SDK&#x27;ye erişim, herhangi bir ücretli kademede (**Copper veya üstü**; Copper / Bronze / Silver / Gold) aktif bir Chloros+ oturumu gerektirir; ücretsiz Iron kademesinde SDK/CLI erişimi yoktur. Uygulama**sunucu tarafında** gerçekleştirilir: her SDK isteği hem aktif bir oturum hem de ücretli bir plan içermelidir; aksi takdirde arka uç `403` / `PLAN_UPGRADE_REQUIRED` (`ChlorosLicenseError` kodu, `ChlorosLocal` tarafından; `ChlorosConnectError` kodu ise `connect_*` yardımcıları tarafından oluşturulur) hatası döndürür. Oturumu kapatılmış bir çağrı yapan kullanıcı, bunun yerine `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) alır — `chloros-cli login` komutunun yeniden çalıştırılması ilk durumu düzeltir ancak ikincisini düzeltmez.

Çevrimdışı kullanım, planın ek süre içinde çalışır: kademesi, sunucu doğrulama önbelleğinden (5 dakika) veya imzalanmış, cihaza bağlı lisans önbelleğinden (aylık planlar için 30 gün; yıllık planlar için abonelik süresinin sonuna kadar) okunur. Gecikme süresi dolduğunda, plan ücretsiz seviyeye geçer ve makine sunucuya bir kez bağlanana kadar SDK&#x27;ye erişim durur. `chloros-cli status`, ücretsiz seviyede erişilebilir kalır, böylece neden her zaman görülebilir. Bkz. [Chloros+ Giriş](chloros+-login.md).

## İstisnalar

&quot;Chloros&#x27;ta meydana gelen her türlü hata&quot;yı işlemek için temel sınıfı yakalayın:

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

Tüm boru hattı istisnaları (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) `ChlorosError`&#x27;ten türetilir. Bir dikkat edilmesi gereken nokta: `ChlorosConnectError` — yalnızca `connect_camera` / `connect_array` / `connect_daq_sensor` tarafından tetiklenir — basit `Exception`&#x27;ten türetilir, **`ChlorosError`&#x27;ten** **değil**; dolayısıyla `except ChlorosError` bunu yakalayamaz. Tam hiyerarşi [SDK Referansı](reference/sdk-reference.md#exceptions) bölümünde yer almaktadır.

## Ayrıca bakınız

* [SDK Referansı](reference/sdk-reference.md) — AI asistanları için optimize edilmiş eksiksiz API yüzeyi.
* [CLI Referansı](reference/cli-reference.md) — her CLI alt komutu, bir SDK çağrısını yansıtır.
* [Chloros&#x27;u AI Asistanlarıyla Kullanma](ai-assistants.md) — `chloros-mcp` kurulum ve komut istemi örnekleri.
* [İndir](download.md) — Windows ve Linux için yükleyiciler.
