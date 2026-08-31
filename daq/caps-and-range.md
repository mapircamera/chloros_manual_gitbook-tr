# Kapak Profilleri ve Kalibre Edilmiş Aralık

> Kapakların kendileri — hangi sensörle birlikte hangi gemiye takıldıkları, nasıl monte edildikleri ve optik davranışları — **[DAQ kullanım kılavuzu](https://mapir.gitbook.io/daq)**’da belgelenmiştir. Bu sayfa, düzeltmenin doğru olmasını sağlayan, takılı kapağın Chloros&#x27;e *bildirilmesini* ele almaktadır.

Her DAQ ışık sensörünün fabrika radyometrik kalibrasyonu, *çıplak* sensörü tanımlar. Difüzörün üzerine takılan fiziksel kapak, sensörün topladığı ışığı değiştirir; bu nedenle Chloros, kalibrasyon paketinin üzerine fabrika tarafından ölçülmüş bir **kapak düzeltme profili** uygular. Doğru kapağı tanımlamak, kalibre edilmiş verilerin elde edilmesinin bir parçasıdır — bu sayfa, modele göre hangi kapakların mevcut olduğunu, bunların nasıl tanımlanacağını ve sensörün kalibre edilmiş spektral aralığının gerçekte ne olduğunu ele almaktadır.

## Modele göre kapak seçenekleri

| Kapak profili (`cap_id`) | Fiziksel kapak | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | Güneş ışığı kosinüs düzeltici kapak (**her modelde varsayılan**) | Evet | Evet | Evet |
| `fov_15` / `fov_45` / `fov_90` | Görüş Alanı (FOV) sınırlayıcı koniler (15° / 45° / 90°) | Evet | — | Evet |
| `fov_30` / `fov_60` | Görüş Alanını Sınırlayan Koniler (30° / 60°) | Evet | — | — |
| `none` | Kapak takılı değil | — | — | Evet |

Modele özgü notlar:

* **DAQ-M&#x27;nin tek bir kapak profili vardır: `sunshine_cosine`.** &quot;Çıplak + Sunshine kapağı&quot; ürün tanımını oluşturur ve çıplak bir DAQ-M için geometri profiline gerek yoktur.
* **Çıplak bir DAQ-U, tam anlamıyla &quot;çıplak&quot;tır** — hiçbir geometri profiline ihtiyaç duymaz; bu nedenle, bu model için `none` profili mevcut değildir.
* **DAQ-E üzerindeki `none`, &quot;no-op&quot; DEĞİLDİR.** DAQ-E&#x27;nin girintili, cam kaplı difüzörünün kendine özgü gerçek bir geometri düzeltmesi vardır; bu nedenle bu modelde &quot;kapaksız&quot; durumun kendisi ölçülen bir profildir.
* **Çıplak bir DAQ-E, herhangi bir yükseklikte doğrudan güneş ışığını ölçemez** — Sunshine kapağı saha yapılandırmasıdır. Çıplak bir DAQ-E ile açık havada çalışma planlamayın.

GUI’nin sensör başına ayarlarında (Işık Sensörleri sekmesindeki dişli simgesi), **Kapak** açılır menüsü DAQ-U ve DAQ-M modellerinde “Yok (kapaksız sensör)” seçeneğini de sunar — bu iki modelde “kapaksız”, yukarıdaki notlara göre kapak düzeltmesinin uygulanmadığı anlamına gelir. Yalnızca kapak fiziksel olarak çıkarıldığında bu seçeneği seçin.

## Kapağın beyan edilmesi — ve bunun önemi

**Beyan edilen `cap_id`, sensörün üzerinde fiziksel olarak bulunan kapakla eşleşmelidir.** Ne sensör ne de yazılım, takılı kapağı algılayamaz. Bu beyan iki şeyi belirler:

1. Her spektruma uygulanan **canlı düzeltme**.
2. **Her `.daq` kaydına yazılan kapak damgası**; bu damga, sonraki yansıma işleme aşamalarında güvenilir bir referans olarak kullanılır.

Sunshine kapağı, **tasarım gereği yaklaşık 12 kat** zayıflatma sağlar; bu nedenle, yanlış kapak beyanıyla yapılan kayıtlarda spektrumlar bu faktör kadar yanlış ölçeklenir. Kapak değişikliklerini derhal beyan edin.

### Kapak ayarı

GUI: Işık Sensörleri sekmesi → sensör satırındaki dişli simgesi → **Kapak** açılır menüsü. Her model için varsayılan değer `sunshine_cosine`&#x27;tir (tüm DAQ sensörleri kosinüs düzelticisi takılı olarak gönderilir) ve bu seçim proje boyunca korunur.

<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->

CLI (arka uç çalışıyor olmalıdır):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI, sözdizimsel olarak tam `cap_id` listesini kabul eder (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); her profil, bağlantı kurulduğunda sensörün modeline göre doğrulanır; bu nedenle, kullanılamayan bir kapak kimliği (örneğin, bir DAQ-U&#x27;da yalnızca E ile başlayan bir kimlik), yanlış düzeltme yapmak yerine açık bir hata ile reddedilir. Hiçbir şey aktarılmadığında arka uç varsayılanı `sunshine_cosine`&#x27;tir.

Python SDK notu: `cap_id`, **SDK**düğmesi**değildir** — `connect_daq_sensor()` / `DAQSensorSession`, cap parametresini göstermez. Yukarıdaki CLI komutlarını veya GUI açılır menüsünü kullanarak sınır değerini seçin; bkz. [SDK Referansı](../reference/sdk-reference.md).

İleri düzey: Profiller, Chloros kurulumunda `daq/cap_profiles/<u|m|e>/<cap_id>.json` içinde bulunur ve `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json`&#x27;te kullanıcı başına geçersiz kılınabilir.

Sınır değerlerinden ayrı olarak, daha önce hiç yeniden kalibre edilmemiş sensörlere otomatik olarak filo verilerinden türetilmiş küçük bir karanlık ofset düzeltmesi uygulanır — kullanıcı müdahalesi gerekmez.

## Güneş ışığı sınır değeri performansı (dış mekan yapılandırması)

Prosedürler oluştururken temel alabileceğiniz değerler:

| Özellik | Değer |
| --- | --- |
| Görüş alanı | 180° yarım küre |
| Kosinüs tepki hatası | 60°&#x27;ye kadar gelen ışık açısında ≤ ±4 %; 70°&#x27;ye kadar ≤ ±4,5 % |
| Düşük güneş sınırı | Güneş yüksekliği ~15°&#x27;nin altında kullanılması önerilmez |
| Zayıflama | ~12× (tasarım gereği) |
| Başlığın yeniden takılma tekrarlanabilirliği | ≈ %1,5 |
| Nicel ışık şiddeti | Ortalama **≥ 15 s**&#x27;lik okumalar (cihaz özelliği, bir kusur değildir) |

Herhangi bir nicel ışık şiddeti değeri için — yansıma referansları dahil — tek bir kare yerine en az 15 saniyelik okumaların ortalamasını kullanın.

## Kalibre edilmiş spektral aralık

| Özellik | Değer |
| --- | --- |
| Spektral örnekleme | 5 nm&#x27;lik adımlarla 340–1010 nm (135 nokta) |
| Radyometrik olarak kalibre edilmiş aralık | **~374–974 nm** (yazılımda zorunlu tutulur) |

Sensör, 340–1010 nm aralığının tamamını bildirir, ancak NIST&#x27;e izlenebilir radyometrik kazanç ~374–974 nm aralığını kapsar. Chloros, spektral ağırlığının yarısından azı bu aralık içinde bulunan herhangi bir kamera bandı için **mutlak yansıma bölünmesini reddeder** ve kalibre edilmemiş bir çıktı üretmek yerine `dls-uncalibrated-band-<nm>` atlama nedenini bildirir. Piyasada bulunan kamera SKU’ları arasında yalnızca F988 filtresi bu aralığın dışındadır; bu model bunun yerine yansıma paneli iş akışını kullanır — bkz. [Yansıma İş Akışları](reflectance.md).

Sensör modelleri, aktarımlar ve sensör kimlikleri için [DAQ genel bakışına](README.md) bakınız. İşleme sırasında cap damgasının nasıl tüketildiğine ilişkin bilgi için [Kayıt ve .daq Biçimi](recording.md) bölümüne bakınız.
