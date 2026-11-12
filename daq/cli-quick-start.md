# CLI Hızlı Başlangıç (pool-*)

Ürünle birlikte gelen `chloros-cli`, DAQ sensörlerini **`daq pool-*`** komut ailesi aracılığıyla kontrol eder — bu komutlar, Chloros arka ucunun kalıcı sensör havuzu üzerinden sensörü çalıştıran hafif HTTP istemcileridir. Aktarım arka uç tarafından yönetildiğinden, GUI, CLI ve SDK komut dosyaları, bağlantı noktası için rekabet etmek yerine tek bir canlı tanıtıcıyı paylaşır. Müşterinin ihtiyaç duyduğu her şeye `pool-*` aracılığıyla erişilebilir: bağlanma, veri akışı, kalibre edilmiş `.daq` dosyalarını kaydetme ve kap profilini değiştirme.

`pool-*`, yayınlanmış sürümlerde desteklenen DAQ arayüzüdür. `chloros-cli daq --help`, `pool-*` alt komutlarını listeler ve `daq discover` / `daq list`, `pool-discover` ile aynı taramayı gerçekleştirir. Doğrudan donanım DAQ alt komutunun mevcut olmadığı durumlarda, eksik paketi belirten ve sizi `pool-*`'e yönlendiren açık bir hata mesajıyla program sonlandırılır — hiçbir hata sessizce geçiştirilmez. (`pip install chloros-sdk` de doğrudan donanım komutları sağlamaz.)

***

## Ön Koşullar

* **Chloros arka ucu çalışıyor olmalıdır** — `pool-*` komutları donanım sürücüleri değil, HTTP istemcileridir. Windows'ta Chloros masaüstü uygulamasını başlatın (bu, arka ucu başlatır). Headless Linux/Jetson'da, hizmetinizi kendi kullanıcı hesabınızla etkinleştirin: `sudo systemctl enable --now chloros-backend@$USER.service` (bkz. [Linux Kurulumu](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **Chloros+ (ücretli seviye) oturum açma**: önce `chloros-cli login` komutunu çalıştırın. Uygulama sunucu tarafındadır — oturum açılmadığında, komutlar `401 AUTH_REQUIRED` ile başarısız olur; ücretsiz (Iron) kademesinde ise `403 PLAN_UPGRADE_REQUIRED` ile başarısız olur.
* Komutlar varsayılan olarak `http://127.0.0.1:5000`'i hedefler; arka uçunuz başka bir yerde çalışıyorsa, `daq pool-*` ailesi `CHLOROS_BACKEND_URL` ortam değişkenini dikkate alır.

***

## Beş dakikalık bir oturum

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — havuzda bir sensör aç

| Varyant | Anlam |
| --- | --- |
| `daq pool-connect` | Akıllı algılama: bu makinede herhangi bir DAQ'yi bul. |
| `daq pool-connect --port PORT` | Belirli bir seri bağlantı noktasındaki DAQ-U (örn. `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | BLE üzerinden DAQ-M, MAC otomatik olarak taranır. |
| `daq pool-connect --mac MAC` | Bilinen bir BLE MAC adresindeki DAQ-M (`--ble`'i ima eder). |
| `daq pool-connect --eth-host HOST` | Bilinen bir ana bilgisayar adı veya IP adresinde DAQ-E — **güvenilir yol**. |
| `daq pool-connect --eth` | Otomatik keşif özelliğine sahip DAQ-E (mDNS, ARP yedekleme ile). Aşağıdaki uyarıya bakınız. |

Ayarlama bayrakları, tümü isteğe bağlı:

| Bayrak | Anlam |
| --- | --- |
| `--integration-time MS` / `-t MS` | Milisaniye cinsinden manuel entegrasyon süresi. |
| `--frame-avg N` / `-f N` | Raporlanan spektrum başına ortalama çerçeve sayısı. |
| `--no-ae` | Otomatik pozlamayı devre dışı bırak (AE varsayılan olarak açıktır). |
| `--no-stream` | Akışı başlatmadan bağlan (daha sonra `pool-stream --start` ile devam et). |
| `--cap-id CAP` | Cap düzeltme profili; arka uç varsayılanı `sunshine_cosine`'tir. Bkz. [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

Halihazırda havuzda başka bir adresiyle (`daq-e-<id>.local` ana bilgisayar adı veya IP adresi) bulunan bir DAQ-E'yi bağladığınızda, birim ikinci kez açılmak yerine mevcut havuz girdisi döndürülür.

{% hint style="warning" %}
**`--eth` otomatik keşif uyarısı.** Çoklu ağ bağlantılı bir ana bilgisayarda (birden fazla aktif ağ arabirimi), önyüklemeden sonraki *ilk* `pool-connect --eth` komutu, sensör çalışır durumda olsa bile boş sonuç verebilir — ARP önbelleği boşken keşif taraması sensörün arabirimini gözden kaçırabilir. `--eth` hiçbir şey bulamazsa, işlemi tekrar deneyin veya `--eth-host <ip-or-hostname>` komutuyla keşif işlemini tamamen atlayın; bu, çoklu ağ bağlantılı makinelerde güvenilir yoldur. DAQ-E'nin ana bilgisayar adı `daq-e-<id>.local`'tir (örn. `daq-e-def330.local`); düz IP adresi de kullanılabilir.
{% endhint %}

## `pool-discover` — bağlanmadan tarama

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

Bağlanabileceğiniz sensörleri tarar ve bağlanmadan bağlanılacak adresi gösterir. `--only`, `usb,ble,eth`'in virgülle ayrılmış bir alt kümesini alır; `--timeout SEC`, aktarım başına tarama süresini ayarlar (varsayılan 5 saniye, 1–20 saniye aralığında sınırlandırılır). Havuzda zaten açık olan sensörler listelenmez (bağlı bir DAQ-M, reklam vermeyi durdurur) — bunlar için `pool-list`'i kullanın. Piyasaya sürülen bir derlemede, `chloros-cli daq discover` ve `daq list` aynı taramayı gerçekleştirir.

## `pool-list` — neyin bağlı olduğunu gösterir

Arka uç havuzundaki tüm sensörleri gösterir; diğer tüm komutların ihtiyaç duyduğu `sensor_id` da buna dahildir:

| Model | `sensor_id` biçimi | Örnek |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetlik tireli | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

PTP özelliği kapatılmış bir DAQ-E (ayar cihazda kaydedilir), `pool-list`'te `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` ile işaretlenir ve bu özelliği tekrar etkinleştiren komut — bkz. [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — spektrum çerçevelerini okur

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

En son çerçeveyi veya en son `--recent N` çerçevelerini döndürür; `--json`, komut dosyası oluşturma için makine tarafından okunabilir çıktı üretir. Çerçeveler, sensörün kapak profili zaten uygulanmış haldeyken, 135 noktalı, 340–1010 nm ızgarada radyometrik olarak kalibre edilmiş spektral ışık şiddetidir (W/m²/nm). Nicel ışınım değerleri için, en az 15 saniyelik karelerin ortalamasını alın — bu, bir cihaz özelliğidir, bir kusur değildir.

## `pool-stream` — akışı duraklatma veya devam ettirme

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — bir `.daq` dosyası kaydedin

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| Bayrak | Varsayılan | Anlam |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | Saniye cinsinden kayıt süresi; `0`, `--stop` komutunu verene kadar çalışmaya devam et anlamına gelir. |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Çıktı dizini. Arka uç bu bilgisayarda ise, göreli yol komutu çalıştırdığınız klasöre göre belirlenir; uzak bir arka uçta ise yol **arka ucu çalıştıran makinede** belirlenir. |
| `--device-name NAME` | — | Kayıtla birlikte saklanan etiket. |
| `--stop` | — | Devam eden bir kaydı durdurur. |
| `--confirm-after SEC` | `20` | Başladıktan sonra, ilk satırın yazılması için bu süre kadar bekleyin ve hiçbir şey gelmezse yüksek sesle uyarı verin (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`). `0`, bu kontrolü devre dışı bırakır. |
| `--heartbeat SEC` | `30` | `--duration` ile, her SEC saniyede bir satır sayısını yazdırın ve sayı artmayı durdurursa uyarı verin. `0` bu özelliği devre dışı bırakır. |

{% hint style="info" %}
Kayıt işlemi arka planda gerçekleşir, bu nedenle `.daq` dosyası **arka uç makinesinin** dosya sistemine kaydedilir — varsayılan olarak `~/Documents/DAQ Live View/` orada bulunur, CLI'yi çalıştırdığınız yerde olması gerekmez. Dosya adları, sensör kimliğini ve bir zaman damgasını içerir.
{% endhint %}

## `pool-set-cap` — takılı kapağı bildir

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Kapak kimliği, her spektruma uygulanan fabrikada ölçülmüş düzeltme profilini seçer ve **sensöre fiziksel olarak takılan kapakla eşleşmelidir** — ne sensör ne de yazılım, kapağı kendi başına algılayamaz ve seçim her `.daq` dosyasına damgalanır. Her yerde varsayılan değer `sunshine_cosine`'tir (her DAQ, Sunshine kosinüs düzeltici kapağı takılı olarak gönderilir; tasarım gereği ~12× zayıflama sağlar — beyan edilmemiş bir kapak değişikliği, spektrumları yaklaşık olarak bu faktör kadar yanlış düzeltir).

| `--cap-id` | Kullanılabilir |
| --- | --- |
| `sunshine_cosine` (varsayılan) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | Yalnızca DAQ-U |
| `none` | Yalnızca DAQ-E — nota bakın |

Sensörün ayar aralığı dışındaki bir kapak kimliği, bağlantı sırasında açık bir hata ile reddedilir. `none` (DAQ-E), kapağın fiziksel olarak çıkarıldığı anlamına gelir — DAQ-E'nin gömme cam difüzörü için hala fabrika geometri profili uygulanır, bu nedenle bu bir "no-op" değildir ve çıplak bir DAQ-E, desteklenen bir saha yapılandırması değil, laboratuvar yapılandırmasıdır. (Çıplak bir DAQ-U tamamen çıplaktır ve hiçbir düzeltme profiline ihtiyaç duymaz; DAQ-M ise Sunshine kapağıyla birlikte kullanılır.)

## `pool-disconnect` — sensörleri serbest bırakma

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect`, sensörü kim açmış olursa olsun serbest bırakır; dolayısıyla, çökmüş bir komut dosyasının havuzda bıraktığı bir sensörü de serbest bırakır (üzerindeki kayıt önce tamamlanır). Havuzda bulunmayan bir sensörün serbest bırakılacak bir şeyi yoktur: komut, `<sensor-id>: already released (not in the pool)` mesajını yazdırır ve `0` ile sonlanır. Arka uç tarafından reddedilen veya onaylanmayan bir serbest bırakma işlemi, `1` ile sonlanır.

## `pool-enable-ptp` — bir DAQ-E'nin PTP'sini tekrar açma

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

Bir DAQ-E, PTP açık/kapalı ayarını kendi belleğinde tutar; bu nedenle, PTP'si kapatılmış bir ünite, tekrar açılana kadar kapalı kalır — ve zaman damgaları PTP ile senkronize edilmez. `pool-list`, bu tür bir birimi işaretler ve onun için bu komutu yazdırır. Chloros, birimin kapalı olması kasıtlı olabileceğinden, bir birimi asla kendi başına yeniden açmaz. 1.3.0 sürümünde eklendi.

## DAQ-E cihaz ayarları

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info`, sensörün donanım yazılımını (ve bu Chloros sürümünün daha yeni bir sürüm içerip içermediğini), adresini ve bu adresi nasıl aldığını, çalışmakta olandan farklıysa kaydedilmiş ağ ayarını, açılış akışını ve PTP'yi gösterir. `pool-set-network`, değişikliği uygulamak için sensörü yeniden başlatır ve sensörü önceki ayarlarıyla yeniden bağlar; `--no-reboot` ise yalnızca ayarları kaydeder. Bu bilgisayarın ağında statik bir adres bulunmalıdır, aksi takdirde Chloros bunu reddeder; `--force`, yalnızca yeniden adreslendirilecek veya bu adrese yönlendirilecek bir bilgisayar için bu kuralı geçersiz kılar. Sensör kayıt yaparken yeniden başlatma işlemleri reddedilir. `pool-update-firmware`<version\>, komut satırı için</version\> GUI'deki **\**'e Güncelle** komutunun karşılığıdır <version\>ve GUI'si olmayan Linux üzerinde bir DAQ-E'yi güncellemenin yoludur. 1.3.0 sürümünde eklenmiştir.

***

## Komut özeti

| Komut | Amaç |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Arka uç havuzunda bir sensör açar. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | Bağlanmadan sensörleri tarar (bir DAQ-M'nin BLE MAC adresini bulur). |
| `daq pool-list` | Havuzdaki her sensörü, ilgili `sensor_id` değeriyle birlikte gösterir. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | En son N adet kalibre edilmiş spektrum çerçevesi. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Akışı devam ettir / duraklat. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | Bir `.daq` kaydını başlat / durdur (arka uç tarafında). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Çalışma sırasında cap düzeltme profilini değiştir. |
| `daq pool-disconnect --sensor-id ID [--all]` | Bir sensörü veya tüm sensörleri serbest bırak. |
| `daq pool-enable-ptp --sensor-id ID` | PTP'si kapalı olan bir DAQ-E için PTP'yi yeniden etkinleştirir. |
| `daq pool-info --sensor-id ID [--json]` | Bir DAQ-E'nin ürün yazılımı, adresi, kaydedilmiş ağ ayarları, açılış akışı ve PTP. |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP veya statik adres; sensörü yeniden başlatır ve yeniden bağlar. |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | Açılışta akış. |
| `daq pool-reboot --sensor-id ID` | DAQ-E'yi yeniden başlatır ve yeniden bağlar. |
| `daq pool-update-firmware --sensor-id ID` | Bu sürümün DAQ-E donanım yazılımını eski bir sensöre yükler. |

***

## İlk DAQ-E bağlantısında sorun giderme

1. DAQ-E'de durum LED'i yoktur — anahtar veya enjektör bağlantı noktasındaki PoE/bağlantı göstergesinden güç beslemesini kontrol edin ve açıldıktan sonra cihazın önyükleme yapıp ağa katılabilmesi için birkaç saniye bekleyin.
2. Arka uç makinesi, sensörle **aynı yayın etki alanında** olmalıdır — mDNS, yönlendiricileri geçemez.
3. Güvenlik duvarı, mDNS (UDP 5353), DAQ-E verileri (UDP 5002) ve PTP (UDP 319/320) trafiğini geçirmelidir. Windows yükleyicisi bunun için bir kural ekler; Linux'ta ise `.deb`, bunları kurulum sırasında etkin olan bir `ufw` / `firewalld` içinde açar; üçüncü taraf bir güvenlik duvarı veya daha sonra etkinleştirilen bir güvenlik duvarında bu bağlantıların izin verilmesi gerekir.
4. `--eth`'ten hâlâ bir yanıt gelmiyor mu? Cihazın ana bilgisayar adı (`daq-e-<id>.local`) veya IP adresiyle `--eth-host` kullanın; bu, keşif aşamasını atlar.

***
{% hint style="info" %}
**AI asistanları için ipucu.** Bu kılavuzun her sayfası ham Markdown biçimi olarak sunulur — bir sayfanın küçük harfli URL'sine `.md` ekleyin (bu sayfa: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); makine tarafından okunabilir dizin `https://mapir.gitbook.io/chloros/llms.txt`'tir. `chloros-cli daq` ve diğer tüm komut ailelerinin eksiksiz bayrak düzeyinde belgelerine ulaşmak için [CLI Referansı](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`) sayfasını inceleyin; Python yolu, [SDK Referansı](../reference/sdk-reference.md) içinde `chloros_sdk.connect_daq_sensor()` şeklindedir.
{% endhint %}
</version\>
