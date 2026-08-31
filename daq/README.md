# DAQ Işık Sensörleri

> **Donanım hakkında bilgi mi arıyorsunuz?**Sensörlerin kendileri — modeller, montaj, kapaklar, bağlantı noktaları, güç ve SCANNER uygulaması —**[DAQ kullanım kılavuzunda](https://mapir.gitbook.io/daq)** ayrıntılı olarak açıklanmıştır. Bu bölümde, Chloros&#x27;ten itibaren bu sensörlerin kullanımı ele alınmaktadır.

MAPIR&#x27;teki **DAQ** ışık sensörleri, ortam ışığını radyometrik olarak kalibre edilmiş spektrumlar halinde ölçer. Chloros&#x27;te bu sensörler iki işlevi yerine getirir:

* **Bağımsız bir spektral cihaz** — canlı spektrum grafikleri, kolorimetrik veriler ve `.daq` kayıtları; bunların tümü [Işık Sensörleri sekmesinden](gui.md), [CLI](cli-quick-start.md) veya Python SDK aracılığıyla.
* **Yansıtma için aşağı doğru ışık şiddeti kaynağı** — işleme sırasında, Chloros, `.daq` okumalarınızı her bir çekiminpozlama zaman damgasına interpolasyon yapar ve ölçülen aşağıya doğru ışık yoğunluğunu kullanarak kameranın parlaklığını yansıma değerine dönüştürür (`--reflectance-source daq`); kalibre edilmiş bantlar için sahne içi panele gerek yoktur.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Üç model, tek veri formatı

| Model | Aktarım | Keşif |
| --- | --- | --- |
| **DAQ-U** | USB (seri) | seri bağlantı noktası taraması |
| **DAQ-M** | Bluetooth Low Energy | isme göre BLE taraması |
| **DAQ-E** | Ethernet (IPv4, PoE ile çalıştırılır) | mDNS `_daq-e._tcp` (ana bilgisayar adı `daq-e-<id>.local`) |

Üçü de aynı kablo protokolünü kullanır ve aynı verileri sağlar:

* Her karede **340 ila 1010 nm aralığında 5 nm&#x27;lik adımlarla 135 noktalı spektrum** ve CIE XYZ üç uyarıcı değerleri.
* **W/m²/nm cinsinden radyometrik olarak kalibre edilmiş spektral ışık şiddeti** — veriler size ulaşmadan önce her birimin fabrika kalibrasyon paketi (artı aktif kapak düzeltme profili) uygulanır.
* Aynı **`.daq` kayıt biçimi** (bir SQLite dosyası). Dosyayı hangi aktarım yönteminin oluşturduğuna bakılmaksızın, sonraki aşamadaki işleme süreci aynıdır.

Aktarım yığınları (USB seri, BLE, mDNS/zeroconf) Chloros arka ucunda bir araya getirilmiştir — GUI&#x27;den veya CLI&#x27;in `pool-*` komutlarından bu üç modelden herhangi biriyle iletişim kurmak için kurulacak hiçbir şey yoktur.

***

## Kalibre edilmiş aralık: Raporlanan 340–1010 nm, kalibre edilmiş ~374–974 nm

Sensör, 340–1010 nm aralığının tamamını rapor eder, ancak NIST&#x27;e izlenebilir radyometrik kazanç yaklaşık **374–974 nm** aralığını kapsar. Chloros, spektral ağırlığının yarısından azı bu kalibre edilmiş aralık içinde bulunan herhangi bir kamera bandı için mutlak yansıma bölünmesini reddeder; atlanan bant, atlama nedeni `dls-uncalibrated-band-<nm>` ile bildirilir.

Piyasada bulunan LATTICE filtre SKU&#x27;ları arasında yalnızca **F988** etkilenir:

F988 yansıma değeri, sahadaki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros en son panel yakalama verilerinizi uygular ve panel gözlemleri arasında bu değeri korur.

Bir F988 yakalama işlemi yalnızca DAQ verileri mevcutken işlenirse, Chloros, o bant için DAQ tabanlı yansıma değerini `dls-uncalibrated-band-988` atlama nedeni ile reddeder — [yansıma paneli iş akışı](../calibration-targets.md) F988 için desteklenen yoldur.

***

## Sensör Kimlikleri

Her DAQ, sabit bir sensör kimliği bildirir. Biçimi modele göre farklılık gösterir:

| Model | Kimlik biçimi | Örnek |
| --- | --- | --- |
| DAQ-U | 5 oktetlik, tire ile ayrılmış | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktetlik, tireli | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Sensör kimliği:

* kaydettiği her `.daq` dosyasına damgalanır,
* Chloros anahtarının o ünitenin fabrika kalibrasyon paketini almak için kullandığı,
* CLI ve `pool-*` komutlarında `--sensor-id`&#x27;e ilettiğiniz değer ve
* DAQ-E için ayrıca mDNS ana bilgisayar adı (`daq-e-def330.local`) — `--eth-host`&#x27;in kabul ettiği değer.

***

## Fabrika kalibrasyonu ve bulut

Her DAQ ünitesi, NIST&#x27;e izlenebilir bir radyometrik zincir kullanılarak ayrı ayrı fabrikada kalibre edilir ve Chloros, her ünitenin sensör kimliğine göre eşleştirilmiş kalibrasyon paketini yükler. Ünite başına kalibrasyon raporu (PDF), [Işık Sensörleri sekmesi](gui.md) içindeki sensör ayarlarından indirilebilir.

{% hint style="warning" %}
**DAQ-U ve DAQ-M modellerinin kalibrasyonu için bulut erişimi gereklidir.**Her iki model de cihaz üzerinde hiçbir veri depolamaz: fabrika kalibrasyon paketleri MAPIR&#x27;in bulutunda bulunur ve sensör kimliği ile alınır (daha sonra yerel olarak önbelleğe alınır). Chloros, DAQ-U veya DAQ-M&#x27;den kalibre edilmiş W/m²/nm verilerini iletmek için internet bağlantısına ihtiyaç duyar.**DAQ-E bir istisnadır** — kalibrasyon verilerini cihazda barındırır.

<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

{% endhint %}***

## Kayıtların depolandığı yer

| Yüzey | Varsayılan `.daq` hedef konumu |
| --- | --- |
| GUI — Işık Sensörleri sekmesi | `<project folder>/light_sensor/` (tamamlanan kayıtlar projeye otomatik olarak eklenir) |
| CLI — `daq pool-record` | Arka ucu çalıştıran makinede `~/Documents/DAQ Live View/` |

Her `.daq` dosya adı, sensör kimliğini ve bir zaman damgasını içerir.

***

## Bu bölümde

* [**Chloros&#x27;teki DAQ Sekmesi**](gui.md) — tam GUI kılavuzu: her bir modelin bağlanması, sensör başına ayarlar, spektrum grafikleri, canlı kolorimetrik veriler, çift sensör yansıma ölçümü ve kayıt.
* [**CLI Hızlı Başlangıç (pool-\*)**](cli-quick-start.md) — `chloros-cli daq pool-*`&#x27;ten DAQ sensörlerini çalıştırma, desteklenen komut satırı yolu.
* [**Sınır Profilleri ve Kalibre Edilmiş Aralık**](caps-and-range.md) — her model için hangi sınırların mevcut olduğu, bunların nasıl tanımlanacağı ve kalibre edilmiş spektral aralık hakkında ayrıntılı bilgiler.
* [**Kayıt ve .daq Formatı**](recording.md) — `.daq` SQLite formatı ve kayıt iş akışları.
* [**DAQ-E Ağ İletişimi ve Zaman Senkronizasyonu**](ethernet-ptp.md) — DAQ-E aktarım modları ve PTP zaman senkronizasyonu.
* [**Yansıtma İş Akışları**](reflectance.md) — DAQ aşağı yönlü verilerini kullanarak yansıtma değerleri elde etme.
* Bayrak düzeyinde eksiksiz belgeler için bkz. [CLI Referansı](../reference/cli-reference.md) (`chloros-cli daq` bölümü) ve [SDK Referansı](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`) bölümlerine bakın; her ikisi de yapay zeka asistanları tarafından doğrudan kullanılabilecek şekilde yazılmıştır.
