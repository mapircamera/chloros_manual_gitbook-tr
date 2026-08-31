# CLI Hızlı Başlangıç (pool-*)

Sevk edilen `chloros-cli` sürücüleri, DAQ sensörlerini **`daq pool-*`** komut ailesi aracılığıyla DAQ sensörlerini kontrol eder — bu, HTTP istemcileri, Chloros arka ucunun kalıcı sensör havuzu üzerinden sensörü çalıştırır. Aktarım arka uç tarafından yönetildiğinden, GUI, CLI ve SDK komut dosyaları, bağlantı noktası için rekabet etmek yerine tek bir canlı tanıtıcıyı paylaşır. Müşterinin ihtiyaç duyduğu her şeye `pool-*` aracılığıyla erişilebilir: bağlantı kurma, veri akışı, kalibre edilmiş `.daq` dosyalarını kaydetme ve kap profilini değiştirme.

`pool-*` ayrıca yayınlanmış sürümlerdeki **tek** DAQ yüzeyidir. `chloros-cli daq --help`, `pool-*` alt komutlarını listeler ve yayınlanmış bir sürümde doğrudan donanımla çalışan bir DAQ alt komutunun çalıştırılması, eksik paketi belirten ve sizi `pool-*`’e yönlendiren açık bir hata mesajıyla sonlanır — hiçbir hata sessizce geçiştirilmez. (Doğrudan donanım komutları yalnızca bir MAPIR kaynak kodundan çalıştırılabilir; `pip install chloros-sdk` de bunları sağlamaz.)

***

## Ön Koşullar

* **Chloros arka ucu çalışıyor olmalıdır** — `pool-*` komutları, donanım sürücüleri değil, HTTP istemcileridir. Windows üzerinde, Chloros masaüstü uygulamasını başlatın (bu, arka ucu başlatır). Headless Linux/Jetson&#x27;da hizmeti etkinleştirin: `sudo systemctl enable --now chloros-backend.service`.
* **Chloros+ (ücretli kademede) oturum açma**: önce `chloros-cli login`&#x27;i çalıştırın. Uygulama sunucu tarafındadır — oturum açılmadığında, komutlar `401 AUTH_REQUIRED` hatasıyla başarısız olur; ücretsiz (Iron) kademede ise `403 PLAN_UPGRADE_REQUIRED` hatasıyla başarısız olur.
* Komutlar varsayılan olarak `http://127.0.0.1:5000`&#x27;i hedefler; arka uç başka bir yerde çalışıyorsa, `daq pool-*` ailesi `CHLOROS_BACKEND_URL` ortam değişkenini dikkate alır.

***

## Beş dakikalık bir oturum

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

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

## `pool-connect` — havuzda bir sensör açar

| Varyant | Anlam |
| --- | --- |
| `daq pool-connect` | Akıllı algılama: bu makinedeki herhangi bir DAQ&#x27;yi bul. |
| `daq pool-connect --port PORT` | Belirli bir seri bağlantı noktasındaki DAQ-U (örn. `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | BLE üzerinden DAQ-M, MAC otomatik olarak taranır. |
| `daq pool-connect --mac MAC` | Bilinen bir BLE MAC adresindeki DAQ-M (`--ble`&#x27;i ima eder). |
| `daq pool-connect --eth-host HOST` | Bilinen bir ana bilgisayar adı veya IP adresinde DAQ-E — **güvenilir yol**. |
| `daq pool-connect --eth` | Otomatik keşif özelliğine sahip DAQ-E (mDNS, ARP yedekleme ile). Aşağıdaki uyarıya bakınız. |

Ayarlama bayrakları, tümü isteğe bağlı:

| Bayrak | Anlam |
| --- | --- |
| `--integration-time MS` / `-t MS` | Milisaniye cinsinden manuel entegrasyon süresi. |
| `--frame-avg N` / `-f N` | Raporlanan spektrum başına ortalamalanan çerçeve sayısı. |
| `--no-ae` | Otomatik pozlamayı devre dışı bırak (AE varsayılan olarak açıktır). |
| `--no-stream` | Akışı başlatmadan bağlan (daha sonra `pool-stream --start` ile devam et). |
| `--cap-id CAP` | Cap düzeltme profili; arka uç varsayılanı `sunshine_cosine`&#x27;tir. Bkz. [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

{% hint style="warning" %}
**`--eth` otomatik keşif uyarısı.** Çoklu ağ bağlantılı bir ana bilgisayarda (birden fazla etkin ağ arabirimi), önyüklemeden sonraki *ilk* `pool-connect --eth` komutu, sensör çalışır durumda olsa bile boş sonuç verebilir — ARP önbelleği boşken keşif taraması sensörün arabirimini gözden kaçırabilir. `--eth` hiçbir şey bulamazsa, işlemi tekrarlayın veya `--eth-host <ip-or-hostname>` ile keşif işlemini tamamen atlayın; bu, çoklu ağ bağlantılı makinelerde güvenilir yoldur. DAQ-E&#x27;nin ana bilgisayar adı `daq-e-<id>.local`&#x27;tir (örn. `daq-e-def330.local`); düz IP adresi de işe yarar.
{% endhint %}

## `pool-list` — bağlı olanları görüntüle

Arka uç havuzundaki tüm sensörleri gösterir; buna, diğer tüm komutların ihtiyaç duyduğu `sensor_id` de dahildir:

| Model | `sensor_id` biçimi | Örnek |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5 oktetlik tireli | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — spektrum çerçevelerini okuma

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

En son çerçeveyi veya en son `--recent N` çerçevelerini döndürür; `--json`, komut dosyası oluşturma için makine tarafından okunabilir çıktı üretir. Çerçeveler, sensörün kapak profili zaten uygulanmış haldeyken, 135 noktalı, 340–1010 nm ızgarada radyometrik olarak kalibre edilmiş spektral ışık şiddetidir (W/m²/nm). Nicel ışınım değerleri için, en az 15 saniyelik karelerin ortalamasını alın — bu bir cihaz özelliğidir, bir kusur değildir.

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
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | Çıktı dizini, **arka ucu çalıştıran makinede** çözümlenir. |
| `--device-name NAME` | — | Kayıtla birlikte saklanan etiket. |
| `--stop` | — | Devam eden bir kaydı durdurur. |

{% hint style="info" %}
Kayıt arka uçta gerçekleşir, bu nedenle `.daq` dosyası **arka uç makinesinin** dosya sistemine kaydedilir — varsayılan olarak `~/Documents/DAQ Live View/` orada, CLI&#x27;i çalıştırdığınız yerde olması gerekmez. Dosya adları, sensör kimliğini ve bir zaman damgasını içerir.
{% endhint %}

## `pool-set-cap` — takılı kapağı bildir

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

Kapak kimliği, her spektruma uygulanan fabrika ölçümlü düzeltme profilini seçer ve **sensöre fiziksel olarak takılan kapakla eşleşmelidir** — ne sensör ne de yazılım, kapağı kendi başına algılayamaz ve seçim her `.daq` dosyasına damgalanır. Her yerde varsayılan ayar `sunshine_cosine`&#x27;tir (her DAQ, Sunshine kosinüs düzeltici kapağı takılı olarak gönderilir; tasarım gereği ~12× zayıflama sağlar — beyan edilmemiş bir kapak değişikliği, spektrumları yaklaşık olarak bu faktör kadar yanlış düzeltir).

| `--cap-id` | Kullanılabilir |
| --- | --- |
| `sunshine_cosine` (varsayılan) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | Yalnızca DAQ-U |
| `none` | Yalnızca DAQ-E — nota bakın |

Sensörün ayar aralığı dışındaki bir kapak kimliği, bağlantı sırasında açık bir hata ile reddedilir. `none` (DAQ-E), kapağın fiziksel olarak çıkarıldığı anlamına gelir — DAQ-E&#x27;nin gömme cam difüzörü için hala fabrika geometri profili uygulanır, bu nedenle bu bir &quot;no-op&quot; değildir ve çıplak bir DAQ-E, desteklenen bir saha yapılandırması değil, laboratuvar yapılandırmasıdır. (Çıplak bir DAQ-U, gerçek anlamda çıplaktır ve hiçbir düzeltme profiline ihtiyaç duymaz; DAQ-M ise Sunshine kapağıyla birlikte kullanılır.)

## `pool-disconnect` — sensörleri serbest bırak

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## Komut özeti

| Komut | Amaç |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | Arka uç havuzundaki bir sensörü açar. |
| `daq pool-list` | Havuzdaki her sensörü, ilgili `sensor_id` değeriyle birlikte gösterir. |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | En son N adet kalibre edilmiş spektrum karesi. |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | Akışı devam ettir / duraklat. |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | Bir `.daq` kaydını başlat / durdur (arka uç tarafında). |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | Çalışma sırasında cap düzeltme profilini değiştirin. |
| `daq pool-disconnect --sensor-id ID [--all]` | Bir sensörü veya tüm sensörleri serbest bırakın. |

***

## İlk DAQ-E bağlantısında sorun giderme

1. DAQ-E&#x27;de durum LED&#x27;i yoktur — anahtardaki veya enjektör bağlantı noktasındaki PoE/bağlantı göstergesinden güç bağlantısını kontrol edin ve cihazın açıldıktan sonra önyükleme yapıp ağa katılabilmesi için birkaç saniye bekleyin.
2. Arka uç makinesi, sensörle **aynı yayın etki alanında** olmalıdır — mDNS, yönlendiricileri geçemez.
3. Windows&#x27;te, ilk çalıştırmada Defender güvenlik duvarı uyarısını kabul edin (mDNS UDP 5353, DAQ-E verileri UDP 5002, PTP UDP 319/320).
4. `--eth`&#x27;ten hâlâ yanıt gelmiyor mu? Ünitenin ana bilgisayar adı (`daq-e-<id>.local`) veya IP adresi ile `--eth-host`&#x27;i kullanın — bu, özellikle çoklu ağ bağlantılı ana bilgisayarlarda güvenilir bir yoldur.

***{% hint style="info" %}**AI asistanları için ipucu.** Bu kılavuzun her sayfası ham Markdown olarak sunulur — bir sayfanın küçük harfli URL slug&#x27;ına `.md` ekleyin (bu sayfa: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); makine tarafından okunabilir dizin `https://mapir.gitbook.io/chloros/llms.txt` şeklindedir. `chloros-cli daq` ve diğer tüm komut ailelerinin tam bayrak düzeyinde belgeleri için [CLI Referansı](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); Python yolu, [SDK Referansı](../reference/sdk-reference.md) içinde `chloros_sdk.connect_daq_sensor()`&#x27;tir.
{% endhint %}
