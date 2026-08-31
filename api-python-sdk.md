# API : Python SDK

{% hint style="info" %}
**API&#x27;in tam sürümünü mü arıyorsunuz?** Bu sayfa, uygulamalı bir öğreticidir. Her bir genel sınıf, yöntem, tam imza ve kopyala-yapıştır yapılabilir örnek, AI asistanları için optimize edilmiş [SDK Referansı](reference/sdk-reference.md) içinde yer almaktadır.**Bir AI asistanıyla mı çalışıyorsunuz?** Bu URL&#x27;i sohbete yapıştırın, böylece tam ve güncel Chloros 1.2.0 API&#x27;e sahip olun:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

Bu kılavuzun her sayfası, küçük harfli slug adı + `.md` şeklinde ham markdown formatında mevcuttur ve kılavuzun tamamı `https://mapir.gitbook.io/chloros/llms.txt` adresinde indekslenmiştir.
{% endhint %}

**Chloros Python SDK** (PyPI&#x27;de `chloros-sdk`) Python&#x27;ten itibaren masaüstü uygulamasının yapabileceği her şeyi yönetir: toplu görüntü işleme, canlı LATTICE kamera ve dizi kontrolü, DAQ ışık sensörü oturumları ve kaydedilmiş proje otomasyonu. Bu, GUI ve CLI&#x27;in kullandığı aynı yerel arka uç (`127.0.0.1:5000` üzerindeki HTTP) üzerinde ince bir katmandır; dolayısıyla davranış, bu üç arayüzde de aynıdır.

## Kurulum

Kurulum iki adımdan oluşur: önce Chloros masaüstü paketi (işleme arka ucunu ve donanım çalışma zamanlarını sağlar), ardından Python paketi.

**Adım 1 — Chloros&#x27;i kurun.** Windows: [İndir](download.md) sayfasından masaüstü yükleyicisini (varsayılan yol `C:\Program Files\MAPIR\Chloros\`) çalıştırın. Linux: `.deb` paketini yükleyin ([Linux Kurulumu](linux/linux-installation.md)).**Adım 2 — SDK&#x27;i yükleyin** (Python 3.7+):

```bash
pip install chloros-sdk
```

pip&#x27;e bile ihtiyacınız olmayabilir: her yükleyici, uyumlu bir SDK wheel&#x27;i içerir. Windows yükleyicisi bunu sistem Python&#x27;inize otomatik olarak yükler; Linux `.deb` ise onu `/usr/lib/chloros/sdk/` konumuna yerleştirir ve tam olarak `pip install --user` komutunu görüntüler. PyPI, sürüm derlemelerinde güncellenir, bu nedenle `pip install chloros-sdk` en son kararlı sürümle eşleşir.

**

3. Adım — Her makine için bir kez oturum açın:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

Kimlik bilgileri `~/.chloros/`&#x27;te önbelleğe alınır (her iki platformda da). Windows&#x27;te, masaüstü uygulamasının Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> sekmesinden de aynı şekilde oturum açabilirsiniz. SDK için ücretli bir Chloros+ planı gereklidir — aşağıdaki [Lisans gereksinimi](#license-requirement) bölümüne bakın.

| Gereksinim | Ayrıntılar |
| --- | --- |
| **Chloros kurulu** | Windows: masaüstü yükleyicisi; Linux: `.deb` paketi (arka uç ikili dosyasını sağlar) |
| **Python** | 3.7 veya üzeri (3.10 sürümünde geliştirilmiş/test edilmiştir) |
| **İşletim sistemi** | Windows 10/11 64-bit, Ubuntu 22.04 LTS veya daha yeni sürümler, ya da NVIDIA Jetson (JetPack 6) |
| **Lisans** | Etkin Chloros+ oturumu, herhangi bir ücretli seviye (Copper veya üstü) |

## 60 saniyede sonuç

Tek bir çağrı ile proje oluşturulur, klasör içe aktarılır, işleme yapılandırılır ve iş akışı çalıştırılır — arka uç henüz çalışmıyorsa otomatik olarak başlatılır:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(Linux&#x27;te, Linux yollarını kullanın: `/home/user/drone_images/flight001`. SDK her iki platformda da aynı şekilde çalışır.)

Bir LATTICE yakalama klasörünü mi işliyorsunuz? LATTICE uyumlu sarmalayıcıyı kullanın — doğru varsayılan ayarları uygular (panel hedefi algılaması yok, standart debayer):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — tam iş akışı kontrolü

Tek satırlık komutların ötesindeki her şey için `ChlorosLocal`&#x27;i kullanın. İlk kullanımda arka ucu başlatır (`auto_start_backend=True`), projeleri oluşturur ve yapılandırır, ilerlemeyi izler ve işleme sonrası bir özet döndürür.

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
`localhost` ile değiştirmeyin, varsayılan `http://127.0.0.1:5000`&#x27;i kullanın — Windows&#x27;te, `localhost`, önce `::1` olarak çözümlenir ve yalnızca IPv4 destekleyen arka uçta istek başına yaklaşık 2 saniye sürer.
{% endhint %}

Garantili temizleme için bunu bir bağlam yöneticisi olarak kullanın:

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` şu anahtar kelimeleri kabul eder: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation` ve `custom_settings`. Ana değerler:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE&#x27;ye özgü ayar düğmeleri (`input_level`, `radiometric_output`, `array_alignment*` ailesi) tam değer tablolarıyla birlikte [SDK Referansı](reference/sdk-reference.md#supported-values) belgesinde tam değer tablolarıyla birlikte yer almaktadır.

### İşlem ilerlemesini izleme

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

İşlem tamamlandığında, `process()`, arka ucun işleme özetini `result["summary"]` olarak ekler. `summary["hints"]` içindeki her bir giriş, dikkat çeken her şeyi açıklayan tam bir cümledir — örneğin, bir çalıştırmanın neden sıfır çıktı ürettiği — ve her ipucu ayrıca Python `UserWarning` olarak yeniden yayınlanır; böylece, sözlüğü hiç incelemeniz gerekmese bile boş çalıştırmalar kendi kendilerini teşhis eder:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()`, bir çalıştırma hiçbir görüntü üretmediğinde ortaya çıkmaz.** Bu, SDK ile CLI&#x27;in kasıtlı olarak farklılık gösterdiği tek noktadır: `chloros-cli process`, &quot;ürünler istendi, hiçbiri yazılmadı&quot; durumunu bir hata olarak değerlendirir ve sıfırdan farklı bir değerle sonlanır; oysa SDK normal şekilde geri döner ve durumu `summary` / ipuçları aracılığıyla bildirir. Eğer iş akışınız boş bir çalıştırmada durması gerekiyorsa, bunu kendiniz kontrol edin — bir istisnaya güvenmek yerine `summary`&#x27;i inceleyin (veya proje klasörü altındaki dosya sayısını sayın).
{% endhint %}

## Smart Connect — canlı donanım

Üç yardımcı program, arka uç donanım havuzunda kalıcı oturumlar açar — bu havuz, GUI&#x27;nin kullandığı havuzla aynıdır; dolayısıyla SDK komut dosyaları, seri bağlantı noktaları veya ağ bant genişliği konusunda çatışma yaşamadan masaüstü uygulamasıyla bir arada çalışır. Üçü de, çalışmakta olan bir arka uç yoksa yerel bir arka ucu otomatik olarak başlatır.

### Tek LATTICE kamera — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### Senkronize dizi — `connect_array`

`connect_array`, çoklu kamera kurulumları için önerilen başlangıç noktasıdır. GUI ile aynı akıllı hazırlık akışını çalıştırır: ağ analizi, senkronizasyon katmanı otomatik seçimi, PTP zaman senkronizasyonu, kamera başına piksel formatı seçimi, AE tohumlama ve GPIO tetikleme hazırlığı. **İlk seri cihaz ana cihazdır** (donanım tetikleme darbesini ateşler); geri kalanlar ise bağımlı cihazlardır.

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

Tetiklemeden önce tüm kameralarda otomatik pozlamanın sabitlenmesini beklemek için herhangi bir dizi yakalamaya `smart=True`&#x27;i ekleyin. Yakalama modları (Tekli / Sürekli / Aralık / En Hızlı), kaydediciler, seri çekimden videoya dönüştürme ve dizi hizalama için [SDK Referansına](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep) bakın.

### DAQ ışık sensörü — `connect_daq_sensor`

Herhangi bir argüman belirtilmediğinde, `connect_daq_sensor()` aktarım türünü akıllıca algılar (öncelik sırası: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

Her çerçeve, 135 noktalı `spectrum` (kalibre edildiğinde W/m²/nm), bir `is_saturated` bayrağı ve CIE `x`, `y`, `z` değerlerini taşır. Belirli bir sensörü veya aktarım yöntemini sabitlemek için — Ethernet otomatik keşfi ilk denemede çalışır durumdaki bir DAQ-E&#x27;yi gözden kaçırabilen, birden fazla ağ arayüzüne sahip ana bilgisayarlarda güvenilir bir seçimdir — bir açık ipucu iletin:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

Büyük harf düzeltme profillerinin (`cap_id`) **bir SDK ayar düğmesi** olmadığını unutmayın — bunun yerine bunları `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` komutlarını kullanarak seçin.

### Kaydedilmiş projeler — `open_project`

Kaydedilmiş bir Chloros projesi, bağlı donanımlarını (`cameras.json` + `sensors.json` ile birlikte `project.json`) korur; ve `chloros_sdk.open_project(path)`, her şeyi tek seferde yeniden bağlayabilir ve aygıt adına göre yakalama işlemlerini yürütebilir. Referanstaki [Proje Otomasyonu](reference/sdk-reference.md#project-automation--chlorosproject) bölümüne bakın.

## Yalnızca pip ile yapılan kurulumda elde edilenler

Donanım yüzeylerini kullanmadan önce modül düzeyindeki kullanılabilirlik bayraklarını kontrol edin:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

**Yalnızca** `pip install chloros-sdk` bulunan ve Chloros masaüstü paketi bulunmayan bir ana bilgisayarda:

* `ChlorosLocal`, `process_folder` ve `process_lattice_capture` **çalışmaz** — bunlar, masaüstü yükleyicisi içinde bulunan arka uç ikili dosyasına ihtiyaç duyar.
* Smart-connect yardımcı programları (`connect_camera`, `connect_array`, `connect_daq_sensor`) tamamen HTTP istemcileridir; bu nedenle başka bir makinedeki arka uçla çalışırlar — ancak birlikte gelen arka uçlar yalnızca döngü geriye bağlanır; bu nedenle bağlantı noktasını kendiniz yönlendirmelisiniz (örn. `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) ve `backend_url="http://127.0.0.1:5000"`&#x27;i `auto_start_backend=False` ile birlikte aktarmalısınız. Bkz. [Uzak Arka Uç Modu](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* Doğrudan donanımla çalışan LATTICE sınıfları (`LatticeCamera`, `CameraPool`, …) içe aktarılabilir, ancak masaüstü paketindeki Arena SDK çalışma zamanı gereklidir — bu olmadan `CAMERA_AVAILABLE`, `False` olur.
* `daq_sdk` (doğrudan DAQ sınıfları), PyPI paketi ile değil, masaüstü yüklemesi ile birlikte gelir; bu nedenle, yalnızca pip kullanılan bir ana bilgisayarda `DAQ_AVAILABLE`, `False`&#x27;e eşdeğerdir — bunun yerine, (tünellenmiş) bir arka uç üzerinden `connect_daq_sensor()` kullanarak DAQ sensörlerini çalıştırın.

## Lisans gereksinimi

SDK&#x27;e erişim, herhangi bir ücretli kademede — **Copper veya üstü**(Copper / Bronze / Silver / Gold) — aktif bir Chloros+ oturumu gerektirir; ücretsiz Iron kademesinde SDK/CLI erişimi yoktur. Uygulama**sunucu tarafında** yapılır: her SDK isteği hem aktif bir oturum hem de ücretli bir plan içermelidir; aksi takdirde arka uç `403` / `PLAN_UPGRADE_REQUIRED` (`ChlorosLocal` tarafından `ChlorosLicenseError` olarak ve `connect_*` yardımcıları tarafından `ChlorosConnectError` olarak bildirilir) Oturumu kapatılmış bir çağrı yapan kullanıcı, bunun yerine `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) alır — `chloros-cli login` komutunun yeniden çalıştırılması ilk durumu düzeltir ancak ikincisini düzeltmez.

Çevrimdışı kullanım, planın ek süre süresi içinde çalışır: kademesi, sunucu doğrulama önbelleğinden (5 dakika) veya imzalanmış, makineye bağlı lisans önbelleğinden (aylık planlar için 30 gün; yıllık planlar için abonelik süresinin sona ermesine kadar) okunur. Gecikme süresi dolduğunda, plan ücretsiz seviyeye geçer ve SDK erişimi, makine sunucuya bir kez bağlanana kadar durdurulur. `chloros-cli status`, ücretsiz seviyede erişilebilir kalır, böylece neden her zaman görünür olur. Bkz. [Chloros+ Oturum Açma](chloros+-login.md).

## İstisnalar

&quot;Chloros ile ilgili herhangi bir sorun&quot;u işlemek için temel sınıfı yakalayın:

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

Tüm iş akışı istisnaları (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) `ChlorosError`&#x27;ten türetilmiştir. Bir dikkat edilmesi gereken nokta: `ChlorosConnectError` — yalnızca `connect_camera` / `connect_array` / `connect_daq_sensor` tarafından tetiklenir — basit `Exception`&#x27;ten türetilir, **`ChlorosError`&#x27;ten** değil; dolayısıyla `except ChlorosError` bunu yakalayamaz. Tam hiyerarşi [SDK Referansı](reference/sdk-reference.md#exceptions) içinde yer almaktadır.

## Ayrıca bakınız

* [SDK Referansı](reference/sdk-reference.md) — yapay zeka asistanları için optimize edilmiş, eksiksiz API yüzeyi.
* [CLI Referansı](reference/cli-reference.md) — her CLI alt komutu, bir SDK çağrısını yansıtır.
* [İndir](download.md) — Windows ve Linux için kurulum programları.
