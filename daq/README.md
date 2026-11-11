# DAQ Işık Sensörleri

> **Donanımı mı arıyorsunuz?**Sensörlerin kendileri — modeller, montaj, kapaklar, bağlantı noktaları, güç ve SCANNER uygulaması —**[DAQ kullanım kılavuzu](https://mapir.gitbook.io/daq)**&#x27;nda ayrıntılı olarak açıklanmıştır. Bu bölümde, Chloros&#x27;tan bu sensörlerin kullanımı ele alınmaktadır.

MAPIR&#x27;ın **DAQ** ışık sensörleri, ortam ışığını radyometrik olarak kalibre edilmiş spektrumlar halinde ölçer. Chloros&#x27;ta bu sensörler iki işlevi yerine getirir:

* **Bağımsız bir spektral cihaz** — canlı spektrum grafikleri, kolorimetrik veriler ve `.daq` kayıtları; bunların tümü [Işık Sensörleri sekmesi](gui.md), [CLI](cli-quick-start.md) veya Python SDK aracılığıyla elde edilir.
* **Yansıtma için aşağı doğru ışınım kaynağı** — işleme sırasında, Chloros, `.daq` okumalarınızı her bir çekimin pozlama zaman damgasına göre enterpolasyon yapar ve ölçülen aşağı doğru ışık yoğunluğunu kullanarak kameranın parlaklığını yansıma oranına (`--reflectance-source daq`) dönüştürür; kalibre edilmiş bantlar için sahne içi panel gerekmez.

<!-- SCREENSHOT-NEEDED: product photo of the DAQ-U, DAQ-M, and DAQ-E units side by side, each with its Sunshine cosine-corrector cap fitted (request from hardware team — no repo asset exists) -->

***

## Üç model, tek veri formatı

| Model | Aktarım | Keşif |
| --- | --- | --- |
| **DAQ-U** | USB (seri) | seri bağlantı noktası taraması |
| **DAQ-M** | Bluetooth Low Energy | isme göre BLE taraması |
| **DAQ-E** | Ethernet (IPv4, PoE ile beslenir) | mDNS `_daq-e._tcp` (ana bilgisayar adı `daq-e-<id>.local`) |**DAQ-E-S (1.3.0 sürümünde yeni).** Kalıcı difüzör ve eğim sensörüne sahip bir DAQ-E. Chloros, bunu `DAQ-E-S` olarak listeler ve DAQ-E ile tamamen aynı şekilde çalıştırır; ancak kapak düzeltmesi yapmaz ve her okumada ünitenin eğim, yuvarlanma ve dikleme değerleri yer alır. Bkz. [DAQ-E-S: sabit difüzör ve eğim](caps-and-range.md#daq-e-s-fixed-diffuser-and-tilt).

Üçü de aynı kablo protokolünü kullanır ve aynı verileri sağlar:

* Her karede **5 nm&#x27;lik adımlarla 340 ila 1010 nm aralığında 135 noktalı spektrum** ve CIE XYZ üç uyarıcı değerleri.
* **W/m²/nm cinsinden radyometrik olarak kalibre edilmiş spektral ışınım** — veriler size ulaşmadan önce her birimin fabrika kalibrasyon paketi (artı aktif kapak düzeltme profili) uygulanır.
* Aynı **`.daq` kayıt formatı** (bir SQLite dosyası). Dosyayı hangi aktarım yönteminin ürettiğine bakılmaksızın, sonraki aşamadaki işleme süreci aynıdır.

Aktarım yığınları (USB seri, BLE, mDNS/zeroconf), Chloros arka ucunda bir araya getirilmiştir — GUI veya CLI&#x27;deki `pool-*` komutları aracılığıyla bu üç modelden herhangi biriyle iletişim kurmak için kurulacak hiçbir şey yoktur.

***

## Kalibre edilmiş aralık: Raporlanan 340–1010 nm, kalibre edilmiş ~374–974 nm

Sensör, 340–1010 nm aralığının tamamını rapor eder, ancak NIST&#x27;e izlenebilir radyometrik kazanç yaklaşık **374–974 nm** aralığını kapsar. Chloros, spektral ağırlığının yarısından azı bu kalibre edilmiş aralık içinde bulunan herhangi bir kamera bandı için mutlak yansıma bölünmesini reddeder; atlanan bant, atlama nedeni `dls-uncalibrated-band-<nm>` ile rapor edilir.

Piyasada bulunan LATTICE filtre SKU&#x27;ları arasında yalnızca **F988** etkilenmektedir:

F988 yansıma değeri, sahadaki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros en son panel yakalama verinizi uygular ve panel gözlemleri arasında bu değeri korur.

Bir F988 yakalama işlemi yalnızca DAQ verileri mevcutken işlenirse, Chloros o bant için DAQ tabanlı yansıma değerini atlar ve atlama nedeni olarak `dls-uncalibrated-band-988`&#x27;i belirtir — [yansıtma paneli iş akışı](../calibration-targets.md), F988 için desteklenen yoldur.

***

## Sensör Kimlikleri

Her DAQ, sabit bir sensör kimliği bildirir. Kimliğin biçimi modele göre farklılık gösterir:

| Model | Kimlik biçimi | Örnek |
| --- | --- | --- |
| DAQ-U | 5 oktetlik, tire ile ayrılmış | `CB-7C-A8-2E-5F` |
| DAQ-M | 5 oktet, tire ile ayrılmış | `CB-74-02-30-6B` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

Sensör kimliği:

* kaydettiği her `.daq` dosyasına damgalanır,
* Chloros&#x27;un o ünitenin fabrika kalibrasyon paketini almak için kullandığı anahtardır,
* CLI&#x27;deki `pool-*` komutlarında `--sensor-id`&#x27;e aktardığınız değer ve
* DAQ-E için ayrıca mDNS ana bilgisayar adı (`daq-e-def330.local`) — `--eth-host`&#x27;in kabul ettiği değerdir.

***

## Fabrika kalibrasyonu ve bulut

Her DAQ ünitesi, NIST&#x27;e izlenebilir bir radyometrik zincir kullanılarak ayrı ayrı fabrikada kalibre edilir ve Chloros, her ünitenin sensör kimliğine göre eşleştirilmiş kalibrasyon paketini yükler. Ünite başına kalibrasyon raporu (PDF), [Işık Sensörleri sekmesi](gui.md) içindeki sensör ayarlarından indirilebilir.

{% hint style="warning" %}
**DAQ-U ve DAQ-M modellerinin kalibrasyonu için bulut erişimi gereklidir.**Her iki model de cihaz üzerinde herhangi bir veri depolamaz: fabrika kalibrasyon paketleri MAPIR&#x27;ın bulutunda bulunur ve sensör kimliği ile alınır (ardından yerel olarak önbelleğe alınır). Chloros, DAQ-U veya DAQ-M&#x27;den kalibre edilmiş W/m²/nm verilerini iletmek için internet bağlantısına ihtiyaç duyar.**DAQ-E bir istisnadır** — kalibrasyon verilerini cihazda barındırır.
{% endhint %}
<!-- PRE-PUBLISH-CHECK: LAUNCH item 3 (DAQ-M end-to-end connect smoke) was still unverified as of 2026-08-16 — re-confirm the DAQ-M cloud-calibration flow on the release build before publishing this page. -->

***

## Kayıtların kaydedildiği yerler

| Yüzey | Varsayılan `.daq` hedefi |
| --- | --- |
| GUI — Işık Sensörleri sekmesi | `<project folder>/light_sensor/` (tamamlanan kayıtlar projeye otomatik olarak eklenir) |
| CLI — `daq pool-record` | Arka ucu çalıştıran makinede `~/Documents/DAQ Live View/` (arka uç aynı bilgisayarda olduğunda, göreceli bir `-o`, komutu çalıştırdığınız klasöre göre çözümlenir) |

Her `.daq` dosya adı, sensör kimliğini ve bir zaman damgasını içerir.

***

## Bu bölümde

* [**Chloros&#x27;taki DAQ Sekmesi**](gui.md) — tam GUI kılavuzu: her modelin bağlanması, sensör başına ayarlar, spektrum grafikleri, canlı kolorimetrik veriler, çift sensör yansıma ölçümü ve kayıt.
* [**Hızlı Başlangıç: CLI (pool-\*)**](cli-quick-start.md) — desteklenen komut satırı yolu olan `chloros-cli daq pool-*`&#x27;ten DAQ sensörlerini çalıştırma.
* [**Sınır Profilleri ve Kalibre Edilmiş Aralık**](caps-and-range.md) — her model için hangi sınırların mevcut olduğu, bunların nasıl tanımlanacağı ve kalibre edilmiş spektral aralığın ayrıntılı açıklaması.
* [**Kayıt ve .daq Biçimi**](recording.md) — `.daq` SQLite biçimi ve kayıt iş akışları.
* [**DAQ-E Ağ İletişimi ve Zaman Senkronizasyonu**](ethernet-ptp.md) — DAQ-E aktarım modları ve PTP zaman senkronizasyonu.
* [**Yansıma İş Akışları**](reflectance.md) — DAQ aşağı yönlü verilerini kullanarak yansıma değerleri elde etme.
* Tam bayrak düzeyi belgeleri için [CLI Referansı](../reference/cli-reference.md) (`chloros-cli daq` bölümü) ve [SDK Referansı](../reference/sdk-reference.md) (`chloros_sdk.connect_daq_sensor()`) bölümlerine bakın; her ikisi de AI asistanları tarafından doğrudan kullanılabilecek şekilde yazılmıştır.
