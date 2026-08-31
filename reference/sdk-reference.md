# Chloros Python SDK Referans

**Sürüm:**

1.2.0**Oluşturulma Tarihi:**29 Temmuz 2026 19:19 ·**Güncelleme:** 30 Ağustos 2026**Paket:** `chloros-sdk` (PyPI)**Hedef Kitle:** LLM kullanımı için optimize edilmiştir; insan tarafından okunabilir.**Kapsam:** `import chloros_sdk` tarafından sunulan tüm genel sınıflar, işlevler ve yardımcı işlevler; görüntü işleme, tek kamera kontrolü, senkronize diziler, DAQ sensörleri ve proje otomasyonunu kapsayan, kopyala-yapıştır yapılabilen örnekler.

Yalnızca önemli noktalara göz atmak istiyorsanız, şu bölümlere geçin:
- [Kurulum ve Hızlı Başlangıç](#installation)
- [LATTICE Dizileri için Smart-Connect](#smart-connect-for-lattice-cameras)
- [DAQ Sensör Oturumları](#daq-sensor-sessions)
- [Proje Otomasyonu](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)

---

## 60 Saniyede Mimari

SDK, Chloros arka ucunun (masaüstü GUI ve CLI tarafından kullanılan Flask sunucusuyla aynı) üzerinde yer alan ince bir Python katmanıdır. Otomasyon için `chloros_sdk`&#x27;i içe aktarır ve üst düzey yöntemleri çağırırsınız; arka planda her çağrı, 5000 numaralı bağlantı noktası üzerindeki yerel arka uca yapılan bir HTTP isteğine dönüşür — `http://127.0.0.1:5000/api/...` (kasıtlı olarak `localhost` değil; bu, Windows adresinde önce `::1`&#x27;e yönlendirilir ve yalnızca IPv4 destekleyen bir arka uca karşı istek başına yaklaşık 2 saniye sürer). Arka uç, donanım havuzuna sahiptir — kameralar, DAQ sensörleri, hizalama profilleri, çerçeve tamponları — bu sayede SDK komut dosyaları, seri bağlantı noktaları veya ağ kartı bant genişliği için rekabet etmeden GUI ile bir arada çalışabilir.

Kullanacağınız üç arayüz vardır:

1. **`ChlorosLocal` + serbest işlevler** (`process_folder`, `process_lattice_capture`) — Görüntü işleme boru hattı. Tek bir Python çağrısıyla tüm bir klasörü kalibrasyon / debayer / indeks dışa aktarma işlemlerinden geçirir.
2. **Akıllı bağlantı tanıtıcıları** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — Canlı donanım için kalıcı bir arka uç oturumu açın. GUI ile aynı &quot;akıllı hazırlık&quot; akışı: ağ denetimi, katman otomatik seçimi, PTP, AE tohumlama, GPIO tetikleyici yapılandırması.
3. **`ChlorosProject` / `open_project`** — Kaydedilmiş bir projeyi yükler (`cameras.json` + `sensors.json` + `project.json` dosyalarının bulunduğu klasör), her şeyi tek seferde bağlayın ve adlandırılmış tanıtıcılarla yakalamaları gerçekleştirin.

Yüzey 1 ve 2, halihazırda dinleme halinde bir arka uç yoksa **yerel bir arka ucu otomatik olarak başlatır** (GUI/CLI&#x27;in oluşturduğu aynı paketlenmiş ikili dosya) — böylece, önce bir arka uç başlatmanıza gerek kalmadan yeni bir kabukta basit bir komut dosyası çalışır. Devre dışı bırakmak için `auto_start_backend=False` parametresini geçin (örneğin, hiçbir zaman başlatılmayan uzak bir arka uca yönlendirirken). Bkz. [Arka Uç Otomatik Başlatma](#backend-auto-start). Surface 3 farklı davranır: `open_project()`, `auto_start_backend` parametresini kabul etmez ve `connect_all()` hiçbir zaman bir arka uç başlatmaz — `http://127.0.0.1:5000`&#x27;i bir kez dener ve yanıt gelmezse, sessizce doğrudan (arka uçsuz) `lattice_sdk` aygıt kontrolüne geri döner. Yalnızca `proj.process()` ve `stream(..., overlays=True)`, bir `ChlorosLocal()`&#x27;i (otomatik başlatma özelliğine sahip) gecikmeli olarak oluşturur.

Üçü de kimlik doğrulama gerektirir: makinede `chloros-cli login`&#x27;i bir kez çalıştırın veya masaüstü GUI üzerinden oturum açın. Geçerli bir oturum olmadan yapılan SDK çağrıları, `ChlorosAuthenticationError` hatasını verir.

Gereksinimler:
- Python 3.7+ (pakette belirtildiği gibi; 3.10 sürümünde geliştirilmiş/test edilmiştir)
- Yerel olarak yüklenmiş Chloros Desktop (arka uç ikili dosyası yükleyici içinde bulunur)
- Aktif Chloros+ oturumu. SDK / CLI erişimi için minimum seviye **Copper**kademesi veya üstüdür (Copper / Bronze / Silver / Gold); ücretsiz**Iron**kademesinde SDK / CLI erişimi yoktur. Bu kural**sunucu tarafında** uygulanır: her SDK / CLI bayrağı taşıyan her istek, hem aktif bir oturum hem de ücretli bir plana sahip olmalıdır; aksi takdirde arka uç, `403` ile birlikte `error_code: PLAN_UPGRADE_REQUIRED` kodunu döndürür (bu, `ChlorosLocal`, ve `connect_*` yardımcıları tarafından `ChlorosConnectError` olarak gösterilir). Oturumu kapatılmış bir çağrı yapan, `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) alır — bu ikisi birbirinden farklıdır çünkü `chloros-cli login`&#x27;in yeniden çalıştırılması ilk sorunu düzeltir ancak ikincisini düzeltemez.
- Planın ödemesiz süresi içinde çevrimdışı kullanım desteklenir: kademesi, sunucu doğrulama önbelleğinden (5 dakika) veya imzalanmış, makineye bağlı lisans önbelleğinden (aylık planlar için 30 gün, yıllık planlar için abonelik süresinin sonuna kadar) okunur. Bu ek süre sona erdiğinde plan ücretsiz plana geçer ve SDK / CLI erişimi, makine sunucuya bir kez erişene kadar durdurulur. `chloros-cli status` (`GET /api/license-status`) ücretsiz kademede erişilebilir kalır, bu nedenle nedeni açıktır — bu, kademelendirme kısıtlamasından muaf olan tek SDK / CLI yoludur.
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS veya daha yeni**, ya da Jetson (JetPack 6). Ubuntu 20.04**desteklenmez**: `.deb`&#x27;in bağımlılıkları, `libc6 (>= 2.34)` dahil olmak üzere arka uçun bağlandığı bileşenlerden türetilir ve focal, glibc 2.31&#x27;i içerir.

---

## Kurulum

Python SDK, Chloros arka ucunun üzerinde çalışan ince bir Python katmanıdır. Yalnızca veri toplama (DAQ) amaçlı birkaç iş akışının ötesindeki her şey için, **Chloros masaüstü paketinin yerel olarak kurulu** olması gerekir (Windows yükleyicisi veya Linux `.deb`) — buArka uç ikili dosyasını, LATTICE kameralar için Arena SDK çalışma zamanını ve kalibrasyon paketlerini sağlayan budur.

En son indirmeler: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### Adım 1 — Chloros platform paketini yükleyin

#### Windows (.exe)

1. İndirme sayfasından `Chloros-Setup-x.y.z.exe` dosyasını indirin.
2. Yükleyiciyi çalıştırın ve sihirbazı takip edin. Varsayılan kurulum yolu `C:\Program Files\MAPIR\Chloros\`&#x27;tir.
3. Chloros adresini en az bir kez açın ve Chloros+ hesabınızla oturum açın.

#### Linux amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### Adım 2 — Python&#x27;ı yükleyin SDK

**Chloros yükleyicisi, uyumlu bir SDK wheel dosyası içerir.** Her Windows yükleyicisi ve Linux .deb dosyası, GUI / CLI / arka uç sürümüyle tam olarak eşleşen bir `chloros_sdk-X.Y.Z-py3-none-any.whl` dosyasını diske yerleştirir. Senkronizasyonu sağlamak için PyPI&#x27;yi takip etmenize gerek yoktur.

#### Windows

Yükleyici, sisteminizdeki Python dosyasını kullanarak pakete dahil edilen wheel dosyasına karşı `pip install` dosyasını otomatik olarak çalıştırır (`py.exe` başlatıcısı tercih edilir, aksi takdirde `python -m pip` kullanılır). Herhangi bir işlem yapmanız gerekmez — `import chloros_sdk`, kurulumun başarıyla tamamlanmasının ardından Python ortamında çalışır. Sistemde Python dosyası bulunmuyorsa, yükleyici bu adımı sessizce atlar ve GUI ile CLI çalışmaya devam eder.

#### Linux (.deb)

.deb dosyası, wheel dosyasını `/usr/lib/chloros/sdk/` konumuna yerleştirir. `postinst`, tam komutu yazdırır — PEP 668 dağıtımları varsayılan olarak global pip yazma işlemlerini reddeder, bu nedenle otomatik-yükleme yapmıyoruz:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Air-gapped Jetson dağıtımları için bu işlem tamamen çevrimdışıdır — wheel dosyası zaten diskte bulunmaktadır.

#### Genel PyPI

Yalnızca pip kullanan ana bilgisayarlar için (Chloros masaüstü paketi yüklü değil; uzak arka uç veya yalnızca DAQ iş akışları):

```bash
pip install chloros-sdk
```

PyPI, sürüm sürümüne göre güncellenir; bu nedenle yayınlanan wheel dosyası en son kararlı sürümle eşleşir. Geliştirme sürümleri (örn. `1.1.4.dev1`) yalnızca paketlenmiş yükleyici wheel dosyası aracılığıyla dağıtılır.

#### Doğrulama

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Chloros+ aboneliği gereklidir.** Tüm SDK çağrıları için aktif bir Chloros+ oturumu gerekir. Her makine için bir kez `chloros-cli login user@example.com 'YourPassword'` komutunu çalıştırın; kimlik bilgileri `~/.chloros/` içinde önbelleğe alınır.

### Masaüstü Paketine İhtiyacım Var mı?

Çoğu iş akışı için sadece pip paketi **yeterli değildir**. Her bir SDK yüzeyinin ihtiyaçları şunlardır:

| SDK Yüzeyi | Masaüstü Paketi Gerekiyor mu? | Neden |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **Evet** | `/usr/lib/chloros/chloros-backend` (Linux) veya `C:\Program Files\MAPIR\Chloros\…` (Windows) adreslerinde arka uç ikili dosyasını otomatik olarak başlatır. |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **Evet**(yerel)**/ Hayır**(uzak) | Arka uç üzerinden saf HTTP istemcileri. Yerel arka uç → masaüstü paketi gereklidir. Uzak arka uç →**tünel aracılığıyla** `backend_url=` (bkz. Uzak Arka Uç Modu — ürünle birlikte gelen arka uçlar yalnızca loopback&#x27;e bağlanır). |
| `ChlorosProject` / `open_project` | **Evet** | Arka uç üzerinden kaydedilmiş projeleri çalıştırır. |
| Doğrudan LATTICE sınıfları (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **Evet** | Masaüstü paketiyle birlikte gelen Arena SDK yerel çalışma zamanına ihtiyaç duyar. Aksi takdirde, `CAMERA_AVAILABLE`, içe aktarım sırasında `False` olur. |
| Doğrudan DAQ sınıfları (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **Hayır** | pyserial/bleak/zeroconf üzerinden saf Python. Yalnızca pip içeren bir ortam, DAQ&#x27;ları uçtan uca çalıştırabilir. |

### Uzak Arka Uç Modu (sadece pip kullanan ana bilgisayar, tünel üzerinden)

> **Birlikte gelen arka uç, LAN üzerinden erişilemez.** Üretim
> sürümleri, yalnızca loopback (her iki loopback ailesi) ve
> loopback dışı tek modu (`CHLOROS_CLOUD_MODE`) kesin olarak reddeder, bu nedenle
> `backend_url="http://<lan-ip>:5000"` **yüklü bir
> Chloros** ile çalışamaz — bu model yalnızca source/dev
> arka uçta çalışmıştır. Başka bir makinedeki arka ucu çalıştırmak için, loopback
> bağlantı noktasını kendiniz yönlendirin ve SDK dosyasını tünele yönlendirin:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

Headless / CI / robotik ana bilgisayarlar, tam masaüstü kurulumuna sahip bir makineyi “Chloros sunucusu” olarak tutabilir ve diğer her yerde `pip install chloros-sdk` kullanabilir — ancak bunlar arasındaki aktarım, yukarıda belirtilen ve kullanıcı tarafından ayarlanan tüneldir, doğrudan LAN URL bağlantısı değildir.

> **Bilinen sınırlama — `ChlorosLocal` yalnızca pip ile çalışamaz.** `ChlorosLocal(backend_url=BACKEND)` şu anda, URL adresini sorgulamadan *önce* yapıcı işlevinde yerel bir arka uç ikili dosyasını çözümler ve masaüstü paketi yüklü olmadığında — erişilebilir bir uzak arka uç olsa bile — `ChlorosBackendError` (&quot;Chloros arka ucu bulunamadı…&quot;) hatasını verir. Yalnızca yukarıdaki akıllı-connect yüzeyi (`connect_camera` / `connect_array` / `connect_daq_sensor`, ayrıca `analyze_array_network` ve `list_*` / `discover_*` yardımcıları) pip-only bir ana bilgisayardan çalışır.

### Yalnızca DAQ İş Akışı (pip-only ana bilgisayar)

Yalnızca DAQ sensörlerine ihtiyacınız varsa ve LATTICE kameraları veya görüntü işlemeyle ilgilenmiyorsanız, pip paketi tek başına yeterlidir:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

Doğrudan donanım üzerinden DAQ çalışması için arka uç, .deb dosyası veya Chloros+ adresinden oturum açma gerekmez.

---

## Hızlı Başlangıç

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
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
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## Görüntü İşleme — `ChlorosLocal`

Ana boru hattı sınıfı. İlk kullanımda arka ucu başlatır, projeler oluşturur/yapılandırır, ilerlemeyi izler ve çalışma sonrası özetleri döndürür.

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
| `import_images(folder_path, recursive=False)` | RAW/TIF/JPG/DNG görüntülerini **ve `.daq` ışık sensörü kayıtlarını** içe aktarır. `count` (görüntüler) ve `scan_count` (kayıtlar) değerlerini döndürür. Yalnızca klasörde ikisi de bulunmuyorsa uyarı verir. |
| `export_light_sensor(daq=True, csv=True)` | Projedeki her ışık sensörü kaydı için kalibre edilmiş `.daq` + `.csv` değerlerini `<project>/Light Sensor/` dosyasına yazın. Bkz. [Işık Sensörü Kayıtları](#light-sensor-recordings--calibrated-daq--csv) bölümüne bakın. |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | İşleme ayarlarını belirleyin. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | İşleme zincirini çalıştırın. `{"status": "complete", "async": False}` değerini ve arka uç tarafından sağlanıyorsa bir `summary` anahtarı döndürür — bkz. [Çalışma Sonrası Özet ve İpuçları](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | Arka uç durumunu inceleyin. |
| `logout()` | Önbelleğe alınmış kimlik bilgilerini temizleyin. |
| `shutdown_backend()` | Arka ucu sonlandırın (SDK -started ile başlatılmışsa). |
| `discover_cameras()` | **Bu örneğin arka ucu** (`/api/camera/discover`). Bir dizi sözlük listesi döndürür (`serial`, `model`, `ip`, …) — GUI/CLI&#x27;de görülenle aynı yapıdadır. Hiçbiri bulunmazsa veya arka uca ulaşılamazsa liste boştur. |
| `camera_capture(output_dir, format="tiff", **settings)` |**Arka uç aracılığıyla**tek bir kare yakalar (bu tanıtıcı tarafından otomatik olarak başlatılır), böylece GUI/CLI ile aynı hazırlık aşamasından geçer (varsayılan 12 bit, havuz yeniden kullanımı, gömülü kalibrasyon meta verileri). Hedefi `serial=` veya `device_index=` ile belirleyin; `exposure`/`gain`/`pixel_format`/`preset` değerlerini `**settings` olarak aktarın. Eski meta veri sözlüğünü döndürür (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`) döndürür. |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | Birleştirilmiş bir kameradan üst üste bindirilmiş önizleme kareleri üretir — arka ucun `/api/camera/<serial>/stream-annotated` yolu üzerinden ince MJPEG istemcisi (zebra / ızgara / artı işareti / histogram / tepe noktası / nokta sunucu tarafında çizilir). `decode=True`, BGR dizileri üretir; `False`, ham JPEG baytları üretir. Ayrıca proje başına `ChlorosProject.stream(overlays=True)`olarak erişilebilir. |

Garantili temizleme için bağlam yöneticisi olarak kullanın:

```python
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

### Işık-Sensör Kayıtları — kalibre edilmiş `.daq` + `.csv`

Bir DAQ-U / DAQ-M / DAQ-E, kalibrasyon paketinden **yoksun** olarak kaydedilebilir. İşte
halka açık [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
kaydediciler (`record_daq.py`) varsayılan olarak bunu yapar: ham sensör sayımlarını yazar ve
dosyaya damga vurur; böylece Chloros o sensörün fabrika kalibrasyonunu **seri numarasına göre** alır — önce yerel önbellekten,
ardından MAPIR Bulut&#x27;tan — ve içe aktarma sırasında uygular.

Chloros sonucu, kayıt başına iki ürün olarak
`<project>/Light Sensor/` altında geri yazar:

| Ürün | Nedir |
| --- | --- |
| `<name>_calibrated.daq` | Yeniden işlenebilir arşiv — canlı kayıtla aynı şema, artık onu üreten paketi bildiriyor. Yeniden içe aktarmak, **kalibre etmez**. |
| `<name>_calibrated.csv` | Sensörün kendi dalga boyu ızgarasında W/m²/nm cinsinden spektral ışık şiddeti; her okuma için bir satır, ayrıca fotometrik sütunlar (toplam güç, fotopik/skotopik lüks, PPFD ve mavi/yeşil/kırmızı ayrımı, tepe dalga boyu). |
| `<name>_raw.daq` / `<name>_raw.csv` | **Yalnızca paket içermeyen sensörler (DAQ-A).** Ham spektral sensör sayıları — *ışık şiddeti değil*. Aşağıya bakınız. |

`process()`, bu dışa aktarmayı aşamalarından biri olarak gerçekleştirir. Görüntü gerektirmez:
tek başına uçurulan bir ışık sensörü birinci sınıf bir iş akışıdır ve böyle bir projede yapı gereği
hiçbir görüntü bulunmaz.

**DAQ-A kayıtları ham sayımlar olarak dışa aktarılır.** DAQ-A ailesi, seri numarası başına
paket sisteminden daha eskiye dayanır ve alınacak bir paketi yoktur — bunun yerine sahada bir
yansıtma hedefi kullanılarak kalibre edilir; bu yüzden hiçbir zaman bir pakete ihtiyaç duymamıştır. Bu kayıtlar,
`_calibrated` yerine `_raw` kökü altında dışa aktarılır: dosya içindeki bir bayrak yerine farklı bir dosya adı
kullanılır, çünkü bu bilginin e-posta ile gönderilirken sadece bir isim olarak e-posta ile gönderilse bile geçerliliğini korumalıdır.
`.csv` başlığı, `raw spectral sensor counts (NOT irradiance)` değerini belirtir ve
değerlerin dosya **içinde** karşılaştırılabilir olduğu konusunda uyarır — bu, hedef tabanlı kalibrasyonun tam olarak
kullandığı gibi — sensörler arasında değil. Güce bağlı fotometrik sütunlar (toplam güç,
fotopik/skotopik lüks, PPFD) sayımlardan entegre edilmeyip **NULL** olarak geri döner.

Paketi alınamayan bir DAQ-U / DAQ-M / DAQ-E yine de **atlanır**,
ham olarak yazılmaz: bu durumda paket mevcuttur ve &quot;yeniden bağlanıp yeniden işleme&quot; gerçek bir tavsiyedir.

Eski **v1.01 / v1.02** kayıtları (bunlar bir DAQ-A-SD tarafından yazılır) okuma başına zaman dilimi bilgisi taşımaz,
sadece dosyanın yazma zamanı bulunur. Görüntü↔aşağı akış eşleştiricisi bunları hâlâ reddediyor — bir
bir kareyi yazma zamanıyla eşleştirmek görünmez bir şekilde yanlış olur — ancak dışa aktarıcı bunları okur ve
CSV, `clock=daq_created_on` mesajını yazdırır; böylece ürün hangi saatte olduğunu belirtir.

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
sensör), **nedeni ile birlikte** `skipped` altında rapor edilir. Bu kayıt asla
ham sayıları içeren &quot;kalibre edilmiş&quot; bir dosya olarak yazılmaz — internete bağlanın ve
işlemi yeniden çalıştırın; dışa aktarma işlemi tamamlanır.

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

İşlem tamamlandığında, `process()`, `GET /api/processing-summary`&#x27;i alır ve gövdeyi `result["summary"]` olarak ekler. Bu alma işlemi en azçaba gerektirmez ve başarılı bir dönüşü asla engellemez — özet mevcut değilse, `process()` basit `{"status": "complete", "async": False}` biçimine geri döner. `summary["hints"]`’teki her giriş — önerilen düzeltme(örneğin, bir çalıştırmanın neden sıfır çıktı ürettiği gibi) — de Python `UserWarning` olarak yeniden gönderilir; böylece, sözlüğü hiç incelemezseniz bile sıfır çıktılı çalıştırmalar kendi kendini teşhis eder:

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
| `images_calibrated` | Çalışma sırasında kalibre edilen görüntüler. |
| `exported_files` | **Çalışma sırasında yazılan görüntü çıktı dosyaları.** |
| `daq_recordings_exported` / `daq_recordings_skipped` | Işık sensörü kayıtları, kasıtlı olarak ayrı sayılmıştır — bunlar farklı bir aşamadan gelir ve hiç görüntü içermeyen çalıştırmalar için de mevcuttur; bu nedenle bunları dahil etmek, yalnızca DAQ içeren bir çalıştırmanın görüntü dışa aktarmış gibi görünmesine neden olur. |

Bunların yanı sıra: `summary["output_dirs"]` (yazılan her dizin),
`summary["light_sensor_export"]`, `summary["stopped"]` (kullanıcı çalışmayı
kesintiye uğrattığında geçerlidir; böylece kısmi sayımlar, beklenenin altında çıktı veren tamamlanmış bir çalışma olarak yorumlanmaz) ve
`summary["groups"]` (grup bazında döküm).

`exported_files`, iş akışı tarafından **yazarken** kaydedilir; daha sonra
projenin görüntü nesnelerinden taranarak alınmaz. Paralel ve GPU stratejileri kendi görüntü
nesnelerini oluşturur (GPU yolları için işçi alt süreçlerinde), bu nedenle eski tarama,
her böyle bir çalıştırma için `0 file(s) written` değerini bildiriyordu ve ardından — her şeyin
sorunsuz çalıştığı çalıştırmalarda — sıfır dışa aktarım ipucunu veriyordu. Bu sayıya göre komut dosyası yazarsanız, sorunsuz bir paralel çalıştırma artık
sıfırdan farklı bir sayı bildirir.

Hafif sensör atlamaları, okuyucunun her dosya için fiilen belirlediği nedeni bildirir —
okunamayan şema, eksik paket, yazma hatası — **tekilleştirilmiş**; böylece tek bir nedenden dolayı atlanan yirmi dosya,
yirmi tekrar yerine tek bir neden olarak okunur.

> **`process()`, bir çalıştırma hiçbir görüntü üretmediğinde ortaya çıkmaz.** Bu, SDK ve
> CLI arasında kasıtlı olarak farklılık gösterilen tek noktadır: `chloros-cli process`, &quot;ürünler talep edildi, hiçbiri
> yazılmadı&quot; durumunu bir hata olarak değerlendirir ve sıfırdan farklı bir değerle sonlanırken, SDK normal şekilde sonlanır ve
> durumu `summary` / hints aracılığıyla bildirir. İş akışınızın boş bir çalıştırmada durması gerekiyorsa, bunu
> kendiniz kontrol edin — bir istisna olmamasına güvenmek yerine
> `summary`&#x27;i inceleyin (veya proje klasörü altındaki dosya sayısını sayın). Bunun olağan nedenleri, bir giriş klasörünün
> yakalama olarak tanınmaması ve mevcut kameralar için uygun olmadığı gerekçesiyle ürünlerin atlanmasıdır (örn. yalnızca RGB
> kameralardan gelen radyans).

### Kolaylık İşlevleri

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
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

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### Desteklenen Değerler

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
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

#### Radyometrik Çıktı (LATTICE multispektral iş akışı)

`process` iş akışının LATTICE multispektral (M3C/M3M) dışa aktarma düzeyi — `reflectance` (varsayılan), `radiance`, `sensor-response` veya `all` (görüntü başına geçerli tüm modlar) — projenin **&quot;Radiometrik çıktı&quot;** işleme ayarına eşlenir. `configure()` için özel bir anahtar sözcük vardır:

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

Gelişmiş kaçış yöntemi — projenin `"Radiometric output"` anahtarını `custom_settings` aracılığıyla yazmak — hâlâ işe yarar, ancak bunun tüm ayar bloğunu değiştirdiğini unutmayın (aşağıdaki uyarıya bakın):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (varsayılan), kamera parlaklığını **zaman damgası eşleşen DAQ aşağı doğru akışı**ile böler; bu, kaydedilmiş bir `.daq` (DAQ-U/M/E)**veya görüntülerin yanında bulunan DAQ-M yerel `.csv`**ile eşleştirilmiş zaman damgası**; yerel olarak eksik olan kamera başına veya DAQ kalibrasyon paketleri,**ilk kullanımda AWS’den otomatik olarak alınır** ilk kullanımda otomatik olarak alınır. CLI, bunu `chloros-cli process` üzerinde ürün türüne göre anahtarlar olarak sunar: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`&#x27;te tür bazında ürün anahtarları olarak sunar.

> `custom_settings`, hesaplanan ayarlar bloğunun tamamını **hesaplanan ayarlar bloğunun tamamını**değiştirir** (tasarım gereği, `configure()`&#x27;in diğer anahtar kelimelerini ve doğrulama işlemlerini atlar). Bunu kullandığınızda, yukarıdaki örnekte olduğu gibi, önem verdiğiniz her `Project Settings` anahtarını dahil edin.

---

## LATTICE Kameralar için Smart-Connect

Canlı donanım için kalıcı arka uç oturumları. GUI’nin kullandığı uç noktalarla aynı olduğundan, SDK / CLI / GUI arasında davranış aynıdır.

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

#### `connect_camera()` İmzası

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

#### `CameraSession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | GenICam düğümlerini okur; `{nodes, errors, enums, device}` değerini döndürür. |
| `set_settings(**kwargs)` | Düğümleri kolay adla yazar (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | **Tek** bir kare yakalar. Kare meta veri sözlüklerinden oluşan tek elemanlı bir liste döndürür. (Seri/çoklu kare yakalama kaldırılmıştır — bir seriye ihtiyacınız varsa döngü içinde `capture()` işlevini çağırın.) |
| `disconnect()` | Havuzdan serbest bırakır. Zaten açık bir oturuma bağlıyızsa hiçbir işlem yapmaz. |

`capture()` dışa aktarma denetimleri (dizi + GUI ile aynı model):

- `processing` / `levels` — `processing="all"`, geçerli tüm dışa aktarma türlerini kaydeder; `levels=["raw","radiance"]` ise yalnızca bunları kaydeder ( `processing`). Arka uç varsayılanı için her ikisini de atlayın.
- `force_daq=True` — atanan DAQ/DLS okumasını, yalnızca ham verinin alındığı durumlarda bile bir `.daq` yan dosyası olarak kaydeder, böylece kare daha sonra yansıma/indeks olarak yeniden işlenebilir. Bağlı bir DAQ yoksa hiçbir işlem yapılmaz.

### Senkronize Dizi — `ArraySession` (Smart-Prep)

`connect_array`, çoklu kamera kurulumları için **önerilen başlangıç noktasıdır**. Arka planda tam GUI smart-prep akışını çalıştırır:

1. **Ağ analizi** (`/api/camera/array/recommend`) — kare kaybı olmadan sim-emit kademesine uyan en büyük kare boyutunu bulur.
2. **Katman otomatik seçimi** — kablo bunu destekliyorsa `sim-capture-sim-emit`; aksi takdirde `sim-capture-ftd-stagger` veya `slip-emit-and-capture`.
3. **Otomatik küçültme**— kablo istenen çözünürlüğü destekleyemediğinde çerçeve boyutunu sessizce küçültür / birleştirmeyi artırır.**Bu güvenlik ağı, toplam aşırı aboneliği kapsamaz**: kablo için çok fazla kamera olması durumu, çerçeveleri küçültmekle düzeltilemez — bkz. [Aşırı Abonelik](#over-subscription-the-per-cam-floor).
4. Varsayılan olarak **PTP etkin**— kameralar arası zaman damgaları,**~1 ms**hassasiyetinde tek bir paylaşılan saat üzerine hizalanır. Eşzamanlı pozlama, PTP&#x27;den değil, M8 donanım tetikleyicisinden (modüller arası**&lt; 100 µs**) sağlanır: PTP, pozlamaları değil *zaman damgalarını* hizalar.
5. **Kamera başına piksel formatı otomatikseçimi** — RGB kameralar → `BayerRG8`, çok spektrumlu → `BayerRG12`.
6. **AE tohumlama** — her kameranın mevcut AE durumunun anlık görüntüsünü alır, böylece bağlantı sırasında pozlama sıfırlanmaz.
7. **GPIO tetikleme yapılandırması** — `connect_array` her kamerayı (`TriggerMode=On`, `TriggerSource=Line2`) devreye alır böylece ana kameranın sinyali, M8 kablosu üzerinden bağımlı kameraları kontrol eder. Bu adım yalnızca dizi modunda geçerlidir: `LatticeCamera` ile açılan tek bir kamera ise serbest çalışma modunda çalışır.

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### `connect_array()` İmza

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`force_tier` değerleri:
- `"sim-capture-sim-emit"` — gerçek eşzamanlı (tüm kameralar aynı saat kenarında tetiklenir).
- `"sim-capture-ftd-stagger"` — esnek zaman dilimi kademelendirme (kamlar hafifçe kaydırılmış zamanlarda sinyal yayar, böylece paketler kabloda sıralı hale gelir).
- `"slip-emit-and-capture"` — kam başına sıralı yakalama (zamansal senkronizasyon yok; simülasyona uyan çerçeve boyutu olmadığında tek seçenek).

`wire_ceiling_mbps`, **ana bilgisayarın sürekli kablo bütçesini** MB/s cinsinden geçersiz kılar — tüm
dizi tahsisinin dayandığı tek sayıdır. Otomatik olarak algılanan
değeri kullanmak için `None` olarak bırakın. Dizi, GVSP bozuk çerçeveleri bildirdiğinde bu değeri düşürün: otomatik değer,
NIC’nin bildirdiği bağlantı hızından türetilir; bu değer, USB adaptörlerini, dar PCIe şeritlerini ve
yoğun paylaşımlı yapıları olduğundan fazla gösterir — ve bu abartı,
görünürde yavaş bir bağlantı olarak ortaya çıkar. Değer, projenin dizi yakalama bloğunda kalıcı olarak kaydedilir; bu nedenle,
yeniden açma veya daha sonraki bir `connect_array` komutu, diğer dizi ayarları gibi bu değeri de geri yükler.
Bkz. [Dizi Sağlığı](#array-health--which-subsystem-is-losing-frames).

#### Aşırı Abonelik (kamera başına alt sınır)

Sim-emit hız ayarlaması, her kameraya çarpışma güvenliği sağlayan kablo bütçesinden bir pay tahsis eder; bu payın alt sınırı **kamera başına 8 MB/s**(`per_cam_floor_bps`). `N × floor`, çarpışma güvenliği tavanını aştığında, dizi**kabloyu aşırı tahsis eder**— arıza modu, daha düşük kare hızı değil, GVSP paket kaybıdır — ve kare boyutu ile ilgili herhangi bir çözüm yoktur:**toplam kontrolün karşılaştırdığı saniye başına hızlandırılmış baytlar değil, kare başına birleştirme ve ROI alt baytları**. 1 GbE ana bilgisayarda pratik tam çözünürlük üst sınırları:**1500 MTU&#x27;da 6 kamera, jumbo çerçevelerde 9 kamera** (analiz yanıtındaki `max_cams_collision_safe`, kablo için üst sınırı bildirir). Çözümler: daha az kamera, uçtan uca jumbo çerçeveler veya daha hızlı bir ağ kartı.

- `analyze_array_network()` ve `/api/camera/array/connect` yanıtları, `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` ve `per_cam_floor_bps` değerlerini içerir. `oversubscribed` değeri true olduğunda, projeksiyon **fps alanlarını sıfırlar** (`achievable_fps_max` / `fps_bright` / `fps_dark`) sıfırlar; yanıltıcı bir şekilde yavaş-ama-çalışan bir hız bildirmez.
- `POST /api/camera/array/connect`, bir `pin_resolution` gövde parametresini kabul eder (**sadece HTTP — bir SDK kwarg değildir**; `connect_array` bunu sunmaz). Sabitleme, gruplandırma ve aşamalı düşürme güvenlik ağını ortadan kaldırır, bu nedenle `pin_resolution` ayarlandığında aşırı yüklenmiş bir bağlantı, her çözüm yolunu belirten bir hata mesajıyla**kesinlikle reddedilir**. Sabitleme olmadan, bağlantı kademeli azaltma işlemine devam eder ancak küçültmenin toplam değeri sıfırlayamayacağı konusunda uyarı verir.
- Test ortamı için acil çıkış yolu: Reddetmeyi yüksek sesli bir uyarıya düşürmek için arka ucun ortamında `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1`&#x27;i ayarlayın — bu durumda yine de bağlanırsınız ve paket kaybını kabul edersiniz.

#### Dizi Sağlığı — hangi alt sistem çerçeve kaybediyor

`GET /api/camera/array/<array_id>/capability`, bağlı bir dizide canlı bir `health` bloğu taşır;
bu, **10 saniyelik** bir döngüsel pencerede yeniden değerlendirilir. Çerçeve kaybını,
her ikisini de belirtmeyen tek bir “eksik” oran yerine,
zıt çözümler gerektiren iki nedene ayırır:

| Alan | Anlamı | Hangi alt sistem |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (seri başına) | Çerçeve **ulaştı ancak yapısal olarak hatalıydı**— GVSP paket kaybı. |**Ağ**: kablo bütçesi, hız ayarı, NIC RX halkası, MTU |
| `never_arrived_rate_pct` (seri numarası başına) | Çerçeve **hiç gelmedi**— kamera tetiklenmedi veya kamera çıkışından hiçbir şey gönderilmedi. |**Tetikleme / senkronizasyon**: M8 kablosu, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | Her biri için en kötü kamera oranı. | — |
| `per_cam_rate_pct` | Kamera başına birleşik eksiklik oranı (her iki neden bir arada). | — |
| `stable_for_seconds` | Her kameranın %0,01&#x27;in altında ne kadar süre kaldığı. | — |

`health` ile birlikte, aynı kayıt tüm tahsisatın ne kadar süreyle askıda kaldığını da bildirir:

| Alan | Anlamı |
| --- | --- |
| `wire_ceiling_mbps` | Ana bilgisayarın geçerli olan sürekli kablo bütçesi, MB/s. |
| `wire_ceiling_source` | Bu sayının kaynağı, kelimelerle — örn. `USB-capped 200 MB/s (was theoretical 1062; …)` veya `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true`, bu değer `wire_ceiling_mbps=` tarafından ayarlandığında. |
| `nic_is_usb` | Bir USB Ethernet adaptörü için `true`. |

Bu uç nokta için SDK sarmalayıcısı yoktur — doğrudan okuyun:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**Okuma:** sıfırdan farklı `gvsp_corrupt_rate_pct` değeri ve 0 olması,
tetikleme ve kablo senkronizasyonunun mükemmel olduğunu ve kaybın %100’ünün ağ yolunda olduğunu gösterir —
`wire_ceiling_mbps` değerini düşürün ve yeniden bağlanın. Tersine bir durum ise senkronizasyon kablosunu veya
tetikleme hattını işaret eder.

> **`target_fps`, bozuk çerçeveler için bir çözüm değildir.** GevSCPD hız ayarı,
> bağlantı kurulduğunda bir kez yazılır; bu nedenle tetikleme hızını düşürmek görev döngüsünü değiştirir,
> eşzamanlı yayın patlama hızını değiştirmez. Ölçülen 5 kat talep kesintisi herhangi bir iyileşme sağlamazken,
> kablo sınırını 240&#x27;tan 200 MB/s&#x27;ye düşürmek, aynı donanımın bozukluk oranını %10,4&#x27;ten
> %0,00&#x27;a indirdi.

> **TRI032S donanım yazılımında akış ortasında otomatik daraltma özelliği mevcut değildir.** Çalışan bir dizi bunu
> kendi başına düzeltemez; bağlantıyı kesip yeniden bağlayarak bağlantı süresi seçicisinin
> yeni üst sınıra göre yeniden planlama yapmasını sağlayın.

Bir **USB Ethernet adaptörü, etiketinde yazan değer ne olursa olsun,**
prob tarafından 200 MB/s ile sınırlandırılır: bağlantı hızını sürekli bir değere dönüştüren verimlilik tablosu
PCIe&#x27;den türetilmiştir ve bir USB NIC, Ethernet bağlantı hızını bildirirken
USB veriyolu ve sürücüsü tarafından sınırlandırılır. Bu sınır mutlak bir değerdir, kesirli bir değer değildir — bir USB 1 GbE adaptörü
~80 MB/s hız sağlar ve bu durumdan etkilenmez.

#### `ArraySession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `status(timeout=10.0)` | Canlı `{fps, ptp, frame_count, last_error, …}`. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | Bir senkronize yakalama grubu. Bir `CaptureResult` (çerçeve sözlükleri listesi + `.skipped`) döndürür. Aşağıdaki dışa aktarma denetimleri. |
| `capture(..., smart=True)` | **Akıllı yakalama** — tüm kameralarda AE&#x27;nin sabitlenmesini bekler, ardından tetikler. |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | En hızlı yakalama: yalnızca ham veriler + atanan DAQ okuması (+ serbest birleştirilmiş indeks). GUI&#x27;deki &quot;En Hızlı Yakalama&quot; düğmesini yansıtıyor. |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | Tekli / Sürekli / Aralıklı yakalama işlemlerini tek bir sınırlı döngüde gerçekleştirir. `list[CaptureResult]` değerini döndürür.**`count` ve/veya `duration_s`** gerektirir; aksi takdirde sonlandırılmaz (SDK&#x27;de Ctrl+C yoktur). |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | Canlı birleştirilmiş indeks görünümünün video/GIF olarak kaydedilmesini başlatır → `RecorderHandle`. Dizi başına bir bileşik kaydedici. |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | Yüksek kare hızında ham Bayer seri çekimi başlat → `RecorderHandle`. `build_video()` ile çevrimdışı olarak yeniden işleyin. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | Kaydedilmiş ham seri çekimi, kalibre edilmiş videolara çevrimdışı olarak yeniden işleyin. İşlem tamamlanana kadar bekler (`wait=True`) ve `{outputs, errors, combined}` değerini döndürür. |
| `build_video_status(job_id, timeout=15.0)` | Çevrimdışı derleme işini sorgular: `{running, result, error, burst_dir}`. |
| `disconnect()` | Tüm diziyi serbest bırakır. |

`capture()` dışa aktarma denetimleri (GUI/CLI&#x27;ın kullandığı uç nokta ile aynı):

- `processing` / `levels` — `processing="all"` (veya `levels=["raw","radiance",…]`), her kamera için geçerli tüm dışa aktarma türlerini kaydeder; tek bir `processing` değeri ise sadece o seviyeyi kaydeder.
- `aligned=True` — her üyenin ham olmayan dışa aktarımını dizinin [hizalama profiline](#dizi hizalaması) (eşzamanlı olarak kaydedilmiş) olarak hizalar; ham veriler hizalanmaz ancak meta verilerde dönüşümü taşır. Dizinin profili yoksa, hizalanmamış duruma geri döner (sonucun `alignment` değerinde bir uyarı görüntülenir).
- `render_index=False` — kamera başına bitki örtüsü indeksi katmanını atlar; varsayılan olarak yapılandırıldığı yerde işler.
- `force_daq=True` — seçilen hiçbir seviyenin gerektirmediği durumlarda bile, atanan DAQ/DLS okumasını bir `.daq` sidecar dosyası olarak kaydeder.

**TIFF sıkıştırma (yalnızca HTTP düğmesi):**`ArraySession.capture()`, `compression` anahtarı göndermez, bu nedenle arka uç varsayılanı geçerlidir — `POST /api/camera/array/capture`, bir `compression` gövde parametresini okur, `"deflate"` varsayılan olarak (kayıpsız zlib L1 + yatay tahminci, tam çözünürlüklü kare başına ~4,1 MB). `"none"`,**~5 kat daha hızlı yazma** ile sıkıştırılmamış (~6,3 MB/kare) olarak yazar — her ikisi de kayıpsızdır ve içe aktarılırken aynı şekilde okunur. SDK&#x27;i bunun için herhangi bir kwarg sunmaz; alternatif çözüm `chloros-cli lattice array-capture --compression none` veya ham HTTP&#x27;dir. DEFLATE ayrıca Python GIL&#x27;i tutar, bu nedenle sıkıştırılmış yazma işlemleri kamera başına yazar iş parçacıkları arasında paralelleştirilmez — sensör hızında 8 kameralı tam çözünürlüklü sürekli yakalama için `compression: "none"` gerekir. Ayrıntılar: [CLI Referans → array-capture](cli-reference.md).**Üye bazında dışa aktarma geçersiz kılmaları (yalnızca HTTP):**aynı uç nokta, `exclude_serials`&#x27;i de kabul eder (liste — kaydedilen kümeden üyeleri çıkarır; dizi yine de tek bir senkronize grup olarak tetiklenir ve hariç tutulan üyeler `excluded`&#x27;te döndürülür), `serial_levels` (`{serial: [level tokens]}` kamera başına düzeyde geçersiz kılmalar) ve `serial_index` (`{serial: bool}` kamera başına dizin-üst üste bindirme geçersiz kılmaları) komutlarını da kabul eder. Bunlar GUI ile eşdeğer gövde parametreleridir ve**henüz SDK kwargs değildir**; haritalarda bulunmayan üyeler, dizi genelindeki `levels` / `render_index` değerlerine geri döner.

##### Atlanan Kameraların İncelenmesi — `CaptureResult.skipped`

`ArraySession.capture()`, bir `CaptureResult` döndürür; ki bu bir `list` alt sınıfıdır: bunu döngüye alın, indeksleyin, `len()` uygulayın — mevcut tüm kalıplar çalışmaya devam eder. Yeni kod, `.skipped` özniteliğini inceleyerek hangi kamların hariç tutulduğunu ve nedenini görebilir. En yaygın durum, `processing="radiance"` veya RGB0417 talep ettiğinizde, karışık filtre dizisindeki  kameralardır — geniş bantlı bir sensör için Bayer başına parlaklık anlamsızdır, bu nedenle arka uç anlamsız veriler üretmek yerine bu kameraları atlar.

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

Neden belirteçleri, `<level>-not-applicable-to-rgb-cam` kalıbını izler (atlanan her seviye için bir giriş, her biri `level`). Yansıma-spesifik atlamalar şunlardır: `reflectance-skipped-no-fresh-dls` (yeni aşağı yönlü okuma mevcut değil), `reflectance-skipped-bound-daq-unavailable (…)` (bağlı DAQ’a ulaşılamadı) ve `dls-uncalibrated-band-<nm>` — bant, büyük ölçüde DAQ ışık sensörünün radyometrik olarak kalibre edilmiş aralığının (~374–974 nm) dışında kaldığından, DAQ tabanlı mutlak yansıma ayrımı reddedilir ve kare, sensör tepkisine geri döner. Satıştaki SKU&#x27;lar arasında yalnızca F988 bunu tetikler; bu kameranındesteklenen yol, yansıma paneli iş akışıdır.

`processing` seviyeleri:

| Seviye | Çıktı |
| --- | --- |
| `"raw"` | Sensörden doğrudan gelen tek kanallı Bayer (monokrom kameralar: tek bant). |
| `"debayered"` *(SDK varsayılan)* | Bilineer demosaik yoluyla 3 kanallı BGR (tek renkli kameralar: 1 kanallı gri tonlamalı). |
| `"radiance"` | Tam radyometrik zincir yoluyla float32 W/m²/sr/nm. Yalnızca multispektral — RGB kameraları atlanır. |
| `"reflectance"` | uint16 0..32768 (Pix4D uyumlu); mutlak referans için canlı DAQ eşleştirmesi gerektirir. Yalnızca multispektral. |
| `"display"` | GUI önizlemesiyle eşleşen tam zincir (kameranın profiline göre CCM + WB + gama). |
| `"all"` | Her kamera için **uygulanabilir her seviye başına bir dosya** (GUI &quot;Tümünü Yakala&quot; / CLI varsayılan ayarına uygun). Dönüşte alınan `CaptureResult` dosyası, her bir `(cam, level)` için bir kare sözlüğü içerir, her bir sözlükte seviye bulunur; geçerli olmayan seviyeler ise `.skipped`&#x27;te görünür. Herhangi bir yansıma çerçevesi için kullanılan DAQ okuma değeri, bir `.daq` yan dosyası olarak kaydedilir. |

> **Not — varsayılan ayar, CLI adresindekinden farklıdır.** `ArraySession.capture()`&#x27;in varsayılan değeri `processing="debayered"`&#x27;tir; `chloros-cli lattice array-capture` komutunun varsayılan değeri ise `processing="all"`&#x27;tir. CLI&#x27;daki /GUI çok seviyeli kaydetme işlevini yansıtmak için SDK dosyasından `processing="all"` komutunu açıkça geçirin.

### Yakalama Modları ve Kaydediciler

Dizi yüzeyi, GUI yakalama panelini yansıtır: Tek / Sürekli / Aralık / En hızlı deklanşör modları, ayrıca iki kayıt cihazı (canlı kompozit video ve ham seri çekim → çevrimdışı yeniden işleme).

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

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**SDK&#x27;in Sürekli/Aralıklı döngüsüdür. Bir komut dosyasından bu döngüyü kesmek için `Ctrl+C` olmadığı için,**mutlaka** `count` ve/veya `duration_s` değerlerini geçmelisiniz (ikisinden birine ulaşıldığında durur). `interval_s`, her geçişin başlangıcından itibaren ölçülür (GUI ile uyumludur). Kalan kwarg&#x27;lar doğrudan `capture()`&#x27;e aktarılır.
- **`record`** *izleme sınıfı*dır: görüntülenen canlı birleşik endeks bileşimini yakalar; bu nedenle karelerin gelmesi için birleşik akışın açık olması gerekir. Her dizi için bir bileşik kaydedici (zaten çalışan bir tane varsa hata verir).
- **`burst` → `build_video`** *analiz sınıfı*dır: `burst`, ham kareleri + kare başına bir manifest + her bir `.daq` dosyası yazar; bu, yakalama döngüsünün tam hızında (zincir yok, exiftool yok, canlı görüntüleme yok) `<output>/bursts/<base>/` altında her bir farklı DLS okuması için gerçekleştirilir. `build_video`, her kareyi en yakın `.daq` ile zaman olarak eşleştirir ve içe aktarma boru hattının parlaklık/yansıma/indeks zincirini yeniden çalıştırır. `products`, `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` (varsayılan: birleştirilmiş indeks) listesidir. `burst().stop()` ayrıca en iyi sonuç elde edilecek şekilde birleştirilmiş indeks oluşturma işlemini otomatik olarak başlatır; bu işlem, durdurma sonucunda `build_job` olarak döndürülür.

#### `RecorderHandle`

`ArraySession.record()` ve `ArraySession.burst()` tarafından döndürülür. Kapsamdan çıkıldığında otomatik olarak durdurmak için bir bağlam yöneticisi olarak kullanın veya manuel olarak çalıştırın.

| Üye | Açıklama |
| --- | --- |
| `job_id` | Arka uç iş kimliği (str). |
| `kind` | `"composite"` (`record`&#x27;ten) veya `"raw"` (`burst`&#x27;ten). |
| `start_stats` | `start` çağrısı tarafından döndürülen sözlük. |
| `result` | Çalışma sırasında `None`; durdurulduktan sonra elde edilen nihai durdurma sonucu sözlüğü. |
| `stats(timeout=10.0)` | Canlı iş istatistikleri (yazılan kareler, gerçekleşen fps, geçen süre). |
| `stop(timeout=60.0)` | Kaydediciyi durdurur; son sonucu döndürür ve önbelleğe alır. İdempotent (ikinci bir çağrı, önbelleğe alınmış sonucu döndürür). |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### Zaten Bağlı Olan Bir Diziye Katılma — `attach_array`

Dizi halihazırda açıksa (GUI tarafından açılmışsa veya önceki bir SDK oturumu `connect_array`&#x27;i çağırmışsa), yeniden bağlanmak yerine `attach_array`&#x27;i kullanarak diziye bir tanıtıcı alın. `connect_array`<sn><id>, bu durumda</id></sn> her zaman &quot;Kamera <sn>zaten dizide bulunuyor<id>&quot;</id></sn> hatasını verir<sn><id>, çünkü havuzdaki bir üye için `/array/connect`&#x27;e POST gönderimi idempotent değildir; `attach_array`, `/api/camera/array/list`&#x27;i okur ve array_id veya serials ile eşleştirir.

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

Örnek: Masaüstü GUI ile aynı kiracıya ait SDK komut dosyaları, önce `attach_array`&#x27;i denemeli ve havuzda henüz bir dizi yoksa `connect_array`&#x27;e geçmelidir.

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **Önemli — context-manager&#x27;ın çıkması bağlantıyı KESER.**`ArraySession.disconnect()` her zaman `/array/disconnect`&#x27;e POST gönderir; `CameraSession` / `DAQSensorSession` için olduğu gibi bir &quot;bağlı-sahibi-olmayan&quot; koruma yoktur. EğerGUI ile birlikte çalışıyorsanız ve kapsamdan çıktığınızda diziyi yıkmak istemiyorsanız,**`with` bloğunu KULLANMAYIN** — tanıtıcıyı normal bir değişkende tutun ve açık `disconnect()` komutunu atlayın:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### Ağ Analizi Yardımcı Programı

Diziyi açmadan önce, — önerdiğiniz ayarların uygun olup olmayacağını öngörür:

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
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status`, `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (aksi takdirde `error`) arasındadır. `auto_capped_fps`, istenen çözünürlüğün RX halkasına yalnızca sınırlandırılmış bir tetikleme hızında uyduğunu belirtir — çözünürlüğü koruyun ve `target_fps=result["recommended"]["recommended_target_fps"]`&#x27;ten `connect_array`&#x27;e geçin (bkz. [Örnek 6](#6-4-kamera-dizisini-bağlamadan-önce-yetenek-tespiti)).

**Projeksiyonun nasıl okunacağı** (GUI Dizi Ayarları paneli ile aynı model):

- **Burst (`frame_bytes_total`), her kameranın gerçek piksel formatında kamera başına toplanır.**Mono**M3M**kameralar, ilettiğiniz `pixel_format` değerinden bağımsız olarak Mono12 (2 B/piksel) akışı sağlar; dolayısıyla 4kamera tam çözünürlüklü kare, 8 bit varsayımının verdiği ~12,6 MB değil, üç mono kamera ile**~25 MB** olur. Arka uç, her kameranın formatını modelinden belirler.
- **Admittance (`burst_fits_nic_ring`),**ring-vs-burst**değil, boşaltma farkındalıdır: ana bilgisayar, RX ringini kameraların doldurma hızından daha hızlı boşalttığında sim-emit uygundur. 10G ana bilgisayar + 1 GbE kameralar**burst, ringi aşsa bile tam çözünürlüğü kabul eder; 1 GbE ana bilgisayar ise engeller (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max`, ihtiyatlı bir seri veri alma üst sınırıdır** — `max(readout+emit, N×emit)`, kamera başına veri gönderimi 1 GbE kamera bağlantısıyla sınırlandırılmış, pozlamadan bağımsızdır. Örneğin, 4 kameralı tam çözünürlüklü 12-bitlik bir dizi için ~2,8 fps (çalışma zamanında ölçülen ~2,7–3,0 ile uyumludur). Tam model: [CLI Referans → Dizi fps ve patlama modeli](cli-reference.md#array-fps--burst-model).
- **Aşırı abonelik (`oversubscribed: true`), kamera başına N × alt sınırın, çarpışma güvenli üst sınırı aştığı anlamına gelir** — fps alanları (`achievable_fps_max` / `fps_bright` / `fps_dark`) 0 değerini gösterir ve otomatik küçültme/birleştirme bu sorunu çözemez (bu işlemler, saniye başına sabit hızda iletilen bayt sayısını değil, kare başına bayt sayısını azaltır). Çözüm olarak kamera sayısının azaltılması, jumbo çerçeveler veya daha hızlı bir ağ kartı kullanılabilir; `max_cams_collision_safe` , sınır değeri (1 GbE üzerinde 1500 MTU ile 6 tam çözünürlüklü kamera, jumbo ile 9 kamera). Yanıtta ayrıca `aggregate_demand_bps`, `collision_safe_ceiling_bps` ve `per_cam_floor_bps` (8 MB/s) değerlerini de içerir. Bkz. [Aşırı Abonelik](#over-subscription-the-per-cam-floor).

### Keşif ve Listeleme

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## Akıllı AE / Akıllı Yakalama

LATTICE dizileri, bağlanır bağlanmaz arka planda sürekli AE çalıştırır, ancak yeni yönlendirilmiş bir sahnenin yakınsaması biraz zaman alır. **Smart-Capture**, bu işlemi kolaylaştıran bir özelliktir: her kameranın pozlamasını sorgular, dizinin bir pencere boyunca kararlı hale gelmesini bekler, ardından çekimi tetikler. Bu, GUI ile eşdeğerdir: masaüstü uygulamasındaki &quot;smart&quot; çekim düğmesi, aynı arka uç uç noktasını çağırır.

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

`ChlorosProject` (sonraki bölüm) üzerinden çalıştırıldığında daha fazla ayar seçeneği elde edersiniz:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

Akıllı AE politikası varsayılan olarak muhafazakârdır. Hassas radyometrik çalışmalar için `exposure_tolerance_pct` değerini daraltın; sadece &quot;yeterince yakın&quot; sonuç istediğiniz, hızla değişen sahneler için genişletin.&quot;

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

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### `DAQSensorSession` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `status(timeout=10.0)` | Havuz girişi özeti (akış/kayıt durumu, dalga boyu aralığı, kalibrasyon sha, entegrasyon süresi, frame_avg, AE durumu). |
| `latest(n=1, timeout=10.0)` | En son N spektrum çerçevesini döndürür. |
| `stream_start()` / `stream_stop()` | Akışı devam ettir / duraklat (bağlantı açık kalır). |
| `record_start(output_dir=None, device_name=None)` | Bir .daq dosyasının kaydını başlatır. Dosya yolunu döndürür. AWS kalibrasyon paketi olmayan DAQ-U/M cihazlarında reddedilir (DAQ-E hariçtir). |
| `record_stop()` | Kaydı durdurur. `{path, rows}` değerini döndürür. |
| `disconnect()` | Havuzdan serbest bırakır. Bağlı ancak sahip olunmayan tanıtıcılar için hiçbir işlem yapmaz. |

> **Kap-düzeltme profilleri (`cap_id`), SDK düğmesi değildir.** `connect_daq_sensor()` / `DAQSensorSession`, `cap_id` parametresini veya `set_cap` yöntemini sunmaz. CLI (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) veya arka ucun `/api/daq` HTTP rotaları (`/api/daq/connect` ve `/api/daq/<id>/cap-id`, `cap_id`&#x27;i kabul eder).

### Keşif — bağlanılacak bir adres bulma

`discover_daq_sensors()`, USB / BLE / ETH&#x27;yi *açabileceğiniz* sensörler için tarar. Bu, `discover_lattice_cameras()`&#x27;in DAQ karşılığıdır ve **DAQ-M&#x27;nin BLE MAC adresini** elde etmenin tek yoludur — bir DAQ-E’nin bir ana bilgisayar adı, bir DAQ-U’nun ise bir COM bağlantı noktası vardır, ancak MAC adresi ne cihazda yazdırılır ne de işletim sistemi tarafından listelenir.

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

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
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` veya tarama işleminin tanımlayamadığı bir bağlantı noktası için `None` (USB seri adaptörleriayırt edilemez; bu nedenle bilinmeyenler gizlenmek yerine gösterilir). |
| `extra` | Aktarım başına ayrıntılar (BLE ilan edilen adı, USB üreticisi, DAQ-E ip/fw/…). Boş değerler atlanır. |

| Parametre | Varsayılan | Açıklama |
| --- | --- | --- |
| `transports` | üçü de | Taramayı sınırlayan sıra (veya csv dizesi). Ne istediğinizi biliyorsanız bu değeri belirtmeye değer — BLE en yavaş kısımdır. |
| `scan_timeout` | 5 | Aktarım başına tarama süresi saniye cinsinden; arka uç, değeri 1–20 aralığına sınırlar. |
| `timeout` | 60,0 | Tüm çağrı için HTTP üst sınırı (SDK’deki diğer yerlerde olduğu gibi). |
| `auto_start_backend` | `True` | Çalışan bir arka uç yoksa yerel bir arka uç başlatır. Uzak bir `backend_url` için asla başlatılmaz. |

> **Havuzda zaten açık olan sensörler görünmez.** Bağlı bir BLE aygıtı reklam yayınlamayı durdurur ve açık bir COM bağlantı noktası taranamaz; bu nedenle keşif, *bağlanmaya hazır* olanları listeler. Bir cihaza bağlandıktan hemen sonra boş bir sonuç alınması beklenen bir durumdur — halihazırda elinizde olan cihazlar için `list_daq_sensors()`&#x27;i kullanın. Taraması yapılamayan aktarımlar (bleak / zeroconf yüklü değil), hata vermeden atlanır; böylece Bluetooth&#x27;u olmayan bir makine de USB ve ETH yanıtlarını alır.

### Liste

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### GUI / CLI ile Ortak Kullanım

GUI&#x27;de halihazırda açık bir sensör varsa, Python adresinden `connect_daq_sensor(port="COM3")` çağrıldığında `already_connected=True` olarak işaretlenmiş bir tanıtıcı döndürülür. Bu durumda oturumun `disconnect()`&#x27;i hiçbir işlem yapmaz; böylece SDK komut dosyanız, kapsamdan çıkıldığında sensörü GUI&#x27;den koparmaz.

### Doğrudan Donanım Sınıfları (Arka Uç Yok)

`daq_sdk`, `chloros_sdk` tarafından yeniden dışa aktarılır; böylece arka uç olmadan da sensörleri süreç içinde uçtan uca kontrol edebilirsiniz:

> **Kullanılabilirlik:**`daq_sdk`, Chloros masaüstü yüklemesiyle birlikte gelir,**PyPI paketi**ile birlikte**gelmez** — `pip install chloros-sdk` size `lattice_sdk`&#x27;i sağlar ancak `chloros_sdk.DAQ_AVAILABLE == False`&#x27;i dışarıda bırakır. Bu sınıfları kullanmadan önce bu bayrağı kontrol edin; yalnızca pip kullanılan bir ana bilgisayarda, sensörü bunun yerine [`connect_daq_sensor()`](#daq-sensor-sessions) sensörünü kullanın; bu, yerel aktarım kütüphanelerine ihtiyaç duymaz.

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

GUI ile paylaşımlı sahiplik istediğinizde smart-connect yolunu (`connect_daq_sensor`) yolunu tercih edin; sensöre münhasıran sahip olan başlıksız komut dosyaları için doğrudan sınıfları kullanın.

---

## Proje Otomasyonu — `ChlorosProject`

Kaydedilmiş bir Chloros projesi, `cameras.json` + `sensors.json` + `project.json` dosyalarını içeren bir klasördür. `open_project`, manifest dosyasını yükler ve `connect_all`, kaydedilmiş ayarlarıyla birlikte her kaydedilmiş cihazı çevrimiçi hale getirir — bu, GUI’nin oluşturacağı donanım durumuyla aynıdır.

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

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

Veya bağlam yöneticisi olarak:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` Yöntemleri

| Yöntem | Açıklama |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | Kaydedilmiş tüm cihazları bulur ve bağlar. Sınıf başına bir bağlantı raporu döndürür. `127.0.0.1:5000` üzerinde dinleme yapan bir arka uç varsa onu kullanır; aksi takdirde sessizce doğrudan (arka uç-free) `lattice_sdk` cihaz kontrolüne geri döner — hiçbir zaman bir arka uç oluşturmaz. |
| `disconnect_all()` | Her şeyi sonlandırır. |
| `capture_all(output_dir=".")` | Her kameradan bir kare + her sensörden bir dizi + spektrum. |
| `stream(camera, overlays=False, fps=10.0)` | Adlandırılmış bir kameradan (veya diziden) BGR `numpy` kareleri üreten jeneratör. `overlays=False`, doğrudan bir `lattice_sdk` yakalama döngüsüdür (diziler `{serial: frame}` sözlükleri üretir). `overlays=True`, `ChlorosLocal.camera_stream()` → arka ucun `/api/camera/<serial>/stream-annotated` MJPEG akışı üzerinden yönlendirilir; bu sırada kameranın kaydedilmiş `ui.overlay` sorgu parametreleri olarak iletilir. Arka uç modu ve bir **bağımsız kamera** gerektirir: doğrudan moddaki bir kamera, `RuntimeError` hatası verir (arka uç, bu işlemin sahip olduğu bir kamerayı alamaz) ve bir dizi ise `NotImplementedError` hatasını verir (kamera başına bileşik görüntü katmanları — bir üyeyi adına göre akışa alır). Tektek çekim eşdeğeri: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | Şu anda bağlı olan her dizide hizalama işlemini çalıştırır. |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | Projenin görüntülerinde kalibrasyon / indeks iş akışını çalıştır (`ChlorosLocal.process`&#x27;i sarar; bu dördü **kabul edilen tek** anahtar-değer çiftleridir — `indices=` vb. `TypeError` hatası verir; indeksleri `ChlorosLocal.configure()` aracılığıyla ayarlar). Bir `ChlorosLocal()`&#x27;i gecikmeli olarak oluşturur; bu da bir arka ucu otomatik olarak başlatır. |

Özellikler:
- `proj.cameras` — `Dict[str, CameraHandle]`, ad ve seri numarasına göre indekslenir.
- `proj.arrays` — `Dict[str, ArrayHandle]`, isim VE array_id ile indekslenir.
- `proj.sensors` — `Dict[str, SensorHandle]`, isim ve slot_id&#x27;ye göre sıralanmıştır.
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

**İşleme seviyeleri.** `capture()`, `grab()` ve `frame_stream()` hepsi aynı `processing`
token&#x27;ını kullanır ve zincir birikimlidir — her seviye, kendisinden yukarıdaki her şeyi çalıştırır:

| Seviye | Çıktı | Notlar |
| --- | --- | --- |
| `raw` | 1 kanallı Bayer, sensör özgü | Demosaik yok. Bu seviyede katmanlama kullanılamaz. |
| `debayered` | 3 kanallı BGR (**varsayılan**) | Bilineer demosaik. Arka uç modu olmadan çalışan tek seviye. |
| `radiance` | float32, W/m²/sr/nm | Tam radyometrik zincir: demosaic + 3×3 ayrıştırma (multispec) + DSNU + düz alan + NIST ölçeği; pozlama × kazanç bölünerek değerler mutlak hale getirilmiştir. |
| `reflectance` | uint16, 32768 = 1,0 | Parlaklık, aşağı doğru ışınım yoğunluğuna bölünür (ρ = π·L/E). Bir DLS/DAQ okuması gerektirir — aşağıdaki nota bakınız. |
| `display` | 8 bit sRGB benzeri | GUI&#x27;ye eşdeğer işleme: Kameranın aktif renk profili aracılığıyla CCM + beyaz dengesi + gama. |

`debayered` dışındaki her şey arka uç modunu gerektirir; doğrudan modda çalışan bir kamera
`NotImplementedError` hatasını verir. `reflectance` için kullanılabilir bir aşağı doğru ışınım okuma değeri gerekir — kare son noktası,
toplanan DAQ verilerini otomatik olarak kameranın DLS yuvasına aktarır, ancak DAQ bağlı değilse zincir
yansıma çıkışını reddeder ve daha düşük kaliteli bir ürünü sessizce
geri vermek yerine, döndürülen meta verilerde bu düşürme durumunu açıkça belirtir.

> **Yansıma DN ölçeği — bunu sabit kodlamayın.** LATTICE yansıma, `32768` = ρ 1.0 değerini kullanır ve
> XMP `Chloros:PixelScale=32768` etiketini ekler; Survey3 yansıma, `65535` = ρ 1.0 değerini kullanır ve
> `Chloros:*` etiketleri içermez. Etiketi okuyun ve buna bölün. uint16 alanında tanımlandığından, yeniden ölçeklendirme yapılan her formatta (16-bit TIFF, 8-bit PNG /JPG,
> `32768` olarak kalır (32-bit yüzde) — depolanan veri türünü önce uint16’ya normalleştirin (8-bit’ten ×257, float’tan ×65535). Tek istisna: 32-bit yüzde) — önce
> depolanan veri türünü uint16&#x27;ya geri normalleştirin (8-bit&#x27;ten ×257, float&#x27;tan ×65535). Tek istisna:
> 8-bit kaynaklı ve 8-bit TIFF olarak yazılmış bir kayıt *kırpılır*, yeniden ölçeklendirilmez; dolayısıyla onu tanımlayan bir ölçek yoktur
> — bu durumda Chloros, `PixelScale` ve MicaSense tuple&#x27;ını tamamen atlar. Bir LATTICE yansıma dosyasında eksik
> bir etiket, varsayılan olarak değil, &quot;geçerli ölçek yok&quot; olarak değerlendirin, varsayılan olarak değil.

> **EXIF, dışa aktarıma aktarılır.** `process()`, kaynak çekimin GPS bloğunu
> **ve ExifIFD&#x27;sini** her ürüne kopyalar; bu nedenle dışa aktarımlar `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` ve `CameraSerialNumber`&#x27;i ve
> coğrafi referanslamayı da içerir. `FocalLength`, Pix4D&#x27;nin yer örnekleme mesafesini hesapladığı veridir — bu veri olmadan
> yeniden yapılandırma son derece hatalı bir ölçeğe düşer (ölçülen bir örnekte 411 m&#x27;lik bir alan
> 47,8 km&#x27;lik bir alana dönüşmüştür). Kopyada kasıtlı olarak `-all:all` kullanılmamıştır: IFD0’ın yapısal etiketleri
> LATTICE çıktısını bozmaktadır ve `ExifImageWidth`/`Height`, dışa aktarılan raster yerine
> yakalamayı tanımladıkları için hariç tutulmuştur.

Yakalama aşaması alt bayrakları (radyometrik seviyelere uygulanır — `radiance`, `reflectance`, `display`):

| Bayrak | Varsayılan | Anlam |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + düz alan + 3x3 ayrıştırma + NIST radyometrik ölçeği. |
| `apply_white_balance` | `True` | WB LUT. Bir DAQ kameraya bağlandığında DLS uyumlu. |
| `apply_index` | `False` | Bitki örtüsü indeksi değerlendirmesi. |
| `index_expression` | `None` | Formülü geçersiz kılma. Boş değilse → endeksi otomatik olarak etkinleştirir. |
| `annotated` | `False` | GUI süslemelerini (zebra/ızgara/peaking) üstüne yerleştir. `raw` için kullanılamaz. |

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

> **Dönüş türü `CapturePathMap`&#x27;tir, `Dict[str, str]` değildir.**
> `chloros_sdk.CapturePathMap`, XPR&#x27;dirOTX000656&#x27;tir: tek seviyeli bir
> `processing` her seriye tek bir yol atarken, çok seviyeli bir yapı (`"all"`, veya
> açık bir `levels` listesi) ise o
> kamera için kaydedilmiş her ürünün **sıralı listesini** verir. Canlı bir birleşik kompozit, eğer akış yapılıyorsa, bir seri numarası altında değil, ekstra
> `"combined"` anahtarı altında gelir. `str`&#x27;i varsayan kod,
> liste biçiminde herhangi bir tip denetleyicisi itiraz etmeden hata verir — `Dict[str, str]`
> yazıyordu; bu nedenle takma ad mevcuttur. Düz biçimi
> istediğinizde normalleştirin:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
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

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## Doğrudan Donanım (Arka Uçsuz)

Arka uca (CI, başsız robotlar, gömülü sistemler) sıfır bağımlılık istediğinizde, `lattice_sdk` ve `daq_sdk`&#x27;i doğrudan içe aktarın — her ikisi de `chloros_sdk` tarafından yeniden dışa aktarılır. `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk`, PyPI paketinde (ancak Arena SDK çalışma zamanının mevcut olması gerekir), oysa `daq_sdk` yalnızca masaüstü kurulumuyla birlikte gelir.

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

Dört ön ayardan üçü **serbest çalışma** modundadır: kamera sürekli pozlama yapar ve bir
`capture()` bir sonraki kareyi döndürür. `triggered` bir istisnadır — 2. Hattaki bir donanım kenarı için
kamerayı hazır hale getirir, bu nedenle bir sinyal gelene kadar hiçbir şey yakalamaz.

| Ön Ayar | Tetikleyici | Ne zaman kullanılır |
| --- | --- | --- |
| `default` | serbest çalışma | genel kullanım |
| `high_speed` | serbest çalışma | 8 bit, 60 fps sınırı, kısa pozlama |
| `high_quality` | serbest çalışma | 12 bit, fps sınırı yok — fotoğraflar için genel tercih |
| `triggered` | **hazır, Hat 2** | kamera bir M8 senkronizasyon kablosuna bağlanmıştır ve başka bir şey tarafından tetiklenir |

`triggered`&#x27;i seçerseniz (veya `trigger_mode="On"`&#x27;i kendiniz ayarlarsanız) ve Hat 2&#x27;yi
tetikleyen hiçbir şey yoksa, her X000683 zaman aşımına uğrayacaktır — bu doğru bir durumdur, çünkü kameradan
beklemesini istemişsinizdir. Bu durum meydana geldiğinde &quot;SDK&quot; bunu açıklar; bkz.
[Çekim sırasında SC_ERR_TIMEOUT](#direct-hardware-backend-free).

> **Not — Bağlantı sırasında görülen &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` mesajları hata değildir.**&gt; Bağlantı kurulduğunda SDK, daha yüksek verim için**jumbo çerçeveler** (9000 baytlık GVSP paketleri) üzerinde anlaşmaya çalışır. Doğrudan noktadan noktaya NIC bağlantısında (örn. bağlantı-yerel bir `169.254.x.x` adresi) ağ genellikle jumbo çerçeveleri taşıyamaz, bu nedenle bu deneme zaman aşımına uğrar ve aşağıdaki gibi satırlar günlüğe kaydedilir:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> Bu, **tasarlanmış yedek çözümdür**: SDK otomatik olarak standart 1500 baytlık paketlere geri döner ve kamera normal şekilde bağlanmaya devam eder (ardından gelen `[chunk-enable …]` satırları, normal bağlantı dizisinin bir parçasıdır). Yakalama işlemi hâlâ çalışır.
>
> Bu denemeyi atlayabilirsiniz, ancak **bu sadece bir günlük-engelleyicisi değildir — jumbo çerçeveleri devre dışı bırakır.** Kamera, ağınız ne kadar iyi olursa olsun Don&#x27;t-Fragment pinglerine yalnızca 1500 bayta kadar yanıt verir, bu nedenle ping testi tek başına jumbo çerçeveleri asla tespit edemez; bunu yapabilen tek şey bu sondadır. Bunu devre dışı bırakırsanız, kamera herhangi bir ağda, sonsuza kadar standart 1500baytlık paketleri kullanır:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> Yalnızca jumbo çerçeveleri taşıyamayacağını *bildiğiniz* bir ağda kullanmaya değer; bu durumda kamera başına yaklaşık bir saniye bağlantı süresinden tasarruf sağlar. Bu, sadece görünüşte değil, gerçek bir ödün verme durumu olduğu için, SDK artık bu özelliği kullandığınızda bunu belirtir:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **Bir nedeniniz olmadıkça bu ayarı değiştirmeyin.** Etkin bırakıldığında, her bağlantı sırasında mevcut ağınız yeniden ölçülür: jumbo paketlerini destekleyen bir anahtara bağlandığınızda, bir sonraki bağlantıda jumbo paketleri otomatik olarak algılanır; herhangi bir yapılandırma veya yeniden başlatma gerekmez.
>
> Jumbo paket aktarım hızını *istiyorsanız*, jumbo uçtan uca-end (NIC MTU 9000 + bunları ileten bir anahtar) özelliğini etkinleştirin veya bağlantının bunu desteklediğini bildiğinizde `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` ile sabitleyin — ancak kalıcı olarak ayarlamaktansa komut başına `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` kullanmayı tercih edin, çünkü sabitlenmiş boyut sondalamayı atlar ve önündeki ağa uyum sağlamayı durdurur. Yoldaki **her** cihaz jumbo paketlerini iletebilmelidir — PoE ayırıcı veya enjektörler de dahil; aksi takdirde jumbo desteğine sahip bir kurulumun bunları taşıyamamasının en yaygın nedeni budur.

> **`SC_ERR_TIMEOUT -1011` sırasında `capture()` / `grab*()`, farklı bir sorundur — bu, gerçek bir hatadır.**&gt; Yukarıdaki not, yalnızca**bağlanma süresi probu**tarafından kaydedilen `-1011` hatasıyla ilgilidir. Bir**yakalama** işleminden kaynaklanan aynı hata, kameranın sorunsuz bir şekilde bağlandığını ancak herhangi bir görüntü göndermediğini gösterir:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> Bunun en belirgin göstergesi, *kontrol* kanalı sağlam olan bir kameradır — keşif işlemi çalışır, ayarlar ve `[chunk-enable …]` yazma işlemleri başarıyla gerçekleşir — ancak *her* karede zaman aşımı yaşanmasıdır.
>
> **Bunun en yaygın nedeni, kameranın donanım tetikleyicisi için etkinleştirilmiş olmasıdır.** `trigger_mode="On"` ve `trigger_source="Line2"` hatalarında, M8 senkronizasyon kablosuna bir elektrik sinyali gelene kadar kamera hiçbir şey göndermez. Bu hattı çalıştıran bir kablonuz yoksa, her veri yakalama işlemi sonsuza kadar bekler. Kamera bozuk değildir ve ağda bir sorun yoktur — tam olarak kendisine söyleneni yapmaktadır.
>
> `CameraSettings()` ile `default` / `high_speed` / `high_quality` ön ayarları serbestçalışır ve tetiklenmişken zaman aşımına uğrayan bir yakalama işlemi, sadece `-1011` yazmak yerine durumu açıklar. `PRESETS["triggered"]`, tasarım gereği Line2&#x27;yi tetikler.
>
> Herhangi bir kamerayı serbest çalışmaya zorlamak için:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> `trigger_mode="Off"` ile hala zaman aşımına uğruyorsa, kamera gerçekten veri göndermiyor demektir — bize günlüğü ve `ip link show`&#x27;i gönderin.

#### Renk Profilleri (RGB canlı önizleme) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)`, RGB kameralarında **canlı önizleme** için ekran renk profilini seçer (multispec kameralar bu ayarı dikkate almaz):

| Profil | Anlam |
| --- | --- |
| `raw` | Radyometrik zinciri tamamen atla. |
| `linear` | DSNU + düz + WB, CCM yok, gama yok. |
| `natural` | Doğrusal + ölçülen CCM + sRGB gama, sadece basit sonlandırma ile (kroma yumuşatma + parlak alanlarda doygunluk azaltma) — gerçekçi varsayılan ayar. |
| `enhanced` | `natural` artı tam hub-parity sonlandırma (renk kenarları giderme, canlılık, CLAHE yerel kontrast). Yaklaşık **kare başına iki kat daha yüksek sonlandırma maliyeti** karşılığında daha zengin bir görünüm, dolayısıyla daha düşük CANLI kare hızı. |
| `custom_temp` | `natural` ancak WB, `custom_cct_k` Kelvin&#x27;e sabitlenmiştir (DLS yok sayılır; arka uç tarafında 2000–10000 K aralığına sabitlenir). |

Profil, **yalnızca canlı önizleme** için bir hız/görünüm ayar düğmesidir: kaydedilen çekimler, seçilen profilden bağımsız olarak her zaman tam ve zengin bir son hal alır, bu nedenle kare süresini geri kazanmak için `natural`&#x27;i seçmek, diske kaydedilen görüntünün kalitesini düşürmez. Bilinmeyen bir profil `ValueError` değerini yükseltir; bir chloros arka ucuna erişilebildiğinde, değişiklik bu arka uca da POST ile gönderilir, böylece bir sonraki önizleme karesi bunu yansıtır (arka ucu olmayan direct-SDK kullanıcıları da ayar değişikliğini alır).

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### Mono (M3M) Kameralar ve `Calibration`

Bir mono **M3M** kamera (`M3M-<lens>-F<wavelength>`) tek bantlıdır: tek bir gri tonlama düzlemi, Bayer mozaiği yok, 3×3 spektral-çapraz etkileşim matrisi yoktur. `Calibration` bunu tanır ve bir `is_mono` bayrağı sunar. Yansıma, bant başına radyometrik harita olarak hâlâ geçerlidir (ayırma işlemi, birim matrisidir), ancak tek bir kamerada yapılan çok bantlı matematiksel işlemler anlamsız sonuçlar vermek yerine doğru sonuçlar verir:

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

Mono donanımdan bir bitki örtüsü indeksi oluşturmak için, farklı dalga boylarındaki birkaç M3M kamerasını hizalanmış bir çok bantlı yığın halinde birleştirin (bkz. [Dizi Hizalama](#array-alignment)) bölümüne bakın) ve endeksi tek bir kamera yerine bu yığın üzerinde hesaplayın.

DAQ doğrudan modu:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` kabul edilen anahtarlar**— tam olarak `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; `cap_id` lehine kullanımdan kaldırılmıştır), `filter_model` (DAQ-M) ve `cap_id` (tüm DAQ türleri; `None`/`""`/`"none"` = çıplak sensör, kapasitans düzeltmesi yok). Bilinmeyen anahtarlar**sessizce göz ardı edilir** — örneğin, `{"integration_time": 64}` hiçbir işlem yapmaz (`integration_time_ms` olması gerekir). `{"applied": [...], "errors": {...}}` değerini döndürür ve asla hata vermez.

`chloros_sdk`, yalnızca yukarıda kullanılan temel yüzeyi yeniden dışa aktarır. Tam `daq_sdk` genel API (22 ad) aşağıdakileri ekler — bunları doğrudan `daq_sdk`&#x27;ten içe aktarın:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

## İstisnalar

&quot;Chloros&#x27;ta herhangi bir sorun&quot; durumunu işlemek için temel sınıfı yakalayın:

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

> `ChlorosAuthenticationError` ve `ChlorosConfigurationError`, diğerleriyle birlikte en üst düzeyde dışa aktarılır; ayrıca gösterildiği gibi `chloros_sdk.exceptions`&#x27;ten de içe aktarılabilirlerX&#x27;ten de içe aktarılabilir.

Hiyerarşi:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

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
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
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
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. Yakalama Reçetesine Eşdeğer (Tamamen Python)

CLI’in reçete DSL’sinin Python adresinde doğrudan bir eşdeğeri bulunmaktadır:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

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

## Arka Uç Otomatik Başlatma

Smart-Connect giriş noktaları — `connect_camera`, `connect_array`, `connect_daq_sensor` ve `discover_lattice_cameras` — bir arka ucun bir arka ucun `127.0.0.1:5000` adresinde (Smart-Connect arayüzünün varsayılan URL) dinleme halinde olduğunu varsayar. GUI veya CLI zaten çalışıyorsa, arka uç zaten çalışır durumdadır. Yalnızca bir komut dosyası kullanıldığında ise arka uç çalışmıyor olabilir; bu nedenle bu işlevler, **pakete dahil arka uç ikili dosyasını otomatik olarak başlatır** (penceresiz olarak, tıpkı `ChlorosLocal`&#x27;in yaptığı gibi) ve ardından arka ucun hazır hale gelmesi için `backend_startup_timeout` kadar bekler.

Kurallar:

- **Yalnızca yerel bir URL başlatılır.** `backend_url`&#x27;in `localhost` / `127.0.0.1` / `[::1]`&#x27;e işaret etmesi uygundur; diğer herhangi bir ana bilgisayar, başka birine ait olduğu varsayılır ve asla oluşturulmaz.
- **Arka uç, yeniden kullanılmak üzere çalışır durumda bırakılır** (CLI ile aynı) — komut dosyanız sona erdiğinde otomatik olarak kapatılmaz. Komut dosyasını yeniden çalıştırdığınızda, çalışır durumdaki arka uç yeniden kullanılır.
- **Bu çağrılardan herhangi birinde**`auto_start_backend=False`** ile devre dışı bırakın (ör. uzak bir arka uca yönlendirdiğinizde veya arka ucun yaşam döngüsünü kendiniz yönettiğinizde).

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

Paket içindeki ikili dosya bulunamazsa veya başlatılamazsa, sonraki HTTP çağrısı, sadece &quot;bağlantı reddedildi&quot; izlemesinden ziyade, eyleme geçirilebilir, **platforma özgü** `ChlorosConnectError` hatası oluşturur — Windows adresinde sizi masaüstü uygulamasına veya bir `chloros-cli` komutuna yönlendirir; Linux adresinde (GUI yok) ise sizi bir `chloros-cli` komutuna veya `.deb` komutuna yönlendirir.

---

## Ortam ve Başlıklar

SDK, her arka uç HTTP çağrısını `X-Chloros-Client: sdk` ile işaretler. Arka uç, GUI ücretsiz kademesi yolu yerine SDK / CLI lisans kurallarını (giriş **ve** ücretli Chloros+ planı gereklidir) lisans kurallarını uygular, GUI ücretsiz kademesi yolunu kullanmaz. Bu ayar, içe aktarma sırasında otomatik olarak yapılır — sizin herhangi bir işlem yapmanız gerekmez.

`http://localhost` ve `http://127.0.0.1`, yerel arka uç olarak algılanır. Diğer ana bilgisayarlara (ör. kendi analiz hizmetinize) yapılan çağrılar etkilenmez.

URL&#x27;i, `backend_url=` (veya `api_url=` üzerinde `ChlorosLocal`) değerini geçirerek arka ucu geçersiz kılın:

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(Döngüsel olmayan bir `backend_url` yalnızca bir source/dev arka ucuna ulaşır — birlikte gelen arka uçlar yalnızca döngüsel bağlantıya bağlanır; tünel şeması için Uzak Arka Uç Modu&#x27;na bakın.)

---

## Sürümleme ve Uyumluluk

- SDK sürümü, `chloros_sdk.__version__` olarak sunulur.
- SDK, davranışı paketlenmiş arka uç sürümüne bağlar. Eski bir SDK ile yeni bir arka ucu bir arada kullanmak genellikle işe yarar (ileri uyumlu uç noktalar), ancak yeni bir SDK ile eski bir arka ucu bir arada kullanmak, yeni uç noktalarda `404` hatalarına yol açabilir — masaüstü uygulamasını buna uygun şekilde güncelleyin.
- Akıllı bağlantı arayüzü (`connect_camera` / `connect_array` / `connect_daq_sensor`) ve ağ analizi uç noktası, kararlı JSON şemaları döndürür; yeni alanlar ek niteliğindedir.

---

## Sorun Giderme İpuçları

- **`ChlorosAuthenticationError: Login required`** → Bu makinede `chloros-cli login EMAIL PASSWORD` komutunu bir kez çalıştırın veya Chloros masaüstü uygulaması üzerinden oturum açın.
- **`ChlorosConnectError: No Chloros backend is running …`** → smart-connect çağrıları yerel bir arka ucu otomatik olarak başlatır; bu nedenle bu hata, pakete dahil edilen ikili dosya bulunamadığında veya başlatılamadığında (örneğin, masaüstü paketi olmayan bir pip-only ana bilgisayar gibi). Mesaj, platforma göre değişir: Windows üzerinde masaüstü uygulamasını açın veya herhangi bir `chloros-cli` komutunu çalıştırın; Linux&#x27;da bir `chloros-cli` komutu çalıştırın (GUI yoktur) veya `.deb`&#x27;i yükleyin. Uzak bir arka uç için `backend_url=` (ve `auto_start_backend=False`) komutunu geçirin.
- **`CAMERA_AVAILABLE == False`** içe aktarma sırasında → `lattice_sdk` yüklenemedi (genellikle Arena SDK çalışma zamanı DLL&#x27;leri yüklenmemiştir). Kamera dışı yüzey hâlâ çalışmaktadır.
- **Dizi bağlantısı, yerel çözünürlükten daha düşük bir çözünürlük döndürüyor**→ Arka ucun akıllı hazırlık özelliği, kabloya sığması için kare boyutunu otomatik olarak küçültür. Nedenini görmek için `analyze_array_network()` komutunu kullanın, ardından bağlantıyı yükseltin, küçültmeyi kabul edin veya sıralı yakalama için `force_tier="slip-emit-and-capture"` komutunu verin. Küçültme güvenlik ağı,**toplam aşırı aboneliği** toplam aşırı abonelik durumunu kapsamaz (`oversubscribed: true`, fps alanları 0): kablo için çok fazla kamera olması durumu, birleştirme/ROI ile düzeltilemez — kamera sayısını azaltın, jumbo çerçeveleri etkinleştirin veya daha hızlı bir NIC&#x27;ye geçin (bkz. [Aşırı Abonelik](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()`, NIC RX halkasının çok küçük olduğunu (~0,26 MB) bildirir / bağlantı kapıları &quot;FRAMES WILL DROP&quot;** → Ana bilgisayar NIC&#x27;sinin alım halkası varsayılan değerindedir (genellikle bir NIC sürücüsü güncellemesinden sonra 32&#x27;ye sıfırlanır). Bir Realtek USB 10GbE adaptöründe `ReceiveBufferLen=256` ve `PendingReceives=64` (yükseltilmiş) olarak ayarlayın, ardından arka ucu yeniden başlatarak halkayı yeniden okumasını sağlayın. Tam prosedür: [CLI Referans → Ana Bilgisayar NIC Kurulumu ve Ayarlaması](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Ana bilgisayar yeniden başlatma/kapatma sırasında donuyor, daha sonra WMI `Invalid class` hataları / NIC etkinleştirilemiyor** → Güncel olmayan USB 10GbE sürücüsü, `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`) hatasına neden oluyor. Adaptör sürücüsünü güncel bir sürüme (≥ 2026) güncelleyin ve alıcı halkası ayarlarını yeniden uygulayın. Bkz. [CLI Referans → Ana Bilgisayar NIC Kurulumu ve Ayarlaması](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Yansıma reddedildi** → Mutlak ölçekli yansıma için canlı bir DAQ&#x27;nın kameraya (veya diziye) bağlanması gerekir. GUI aracılığıyla bağlayın veya eşleştirilmiş sensör gerektirmeyen `processing="radiance"` (W/m²/sr/nm) kullanın.
- **`smart=True` yakalama işlemi beklenenden uzun sürüyor** → AE yakınsaması sahne dinamiklerine bağlıdır; daha hızlı (daha az kararlı) bir tetikleme istiyorsanız `exposure_tolerance_pct` değerini sıkılaştırın veya `stability_window_s` değerini kısaltın.

---

## Ayrıca Bkz.

- [CLI Referansı](cli-reference.md) — her CLI alt komutu, bir SDK çağrısını yansıtır.
- [DAQ Sensör Kılavuzu](../daq/README.md) — sensöre özgü kablolama, kalibrasyon ve kayıt kuralları.
- Çevrimiçi belgeler: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
