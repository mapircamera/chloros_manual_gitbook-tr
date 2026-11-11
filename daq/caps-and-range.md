# Kapak Profilleri ve Kalibre Edilmiş Aralık

> Kapakların kendileri — hangi sensörle hangi kapak birlikte gelir, nasıl monte edilirler ve optik davranışları — **[DAQ kullanım kılavuzunda](https://mapir.gitbook.io/daq)** belgelenmiştir. Bu sayfa, takılan kapağın Chloros
'a *bildirilmesini* ele almaktadır; bu, düzeltmenin doğru olmasını sağlayan unsurdur.

Her DAQ ışık sensörünün fabrika radyometrik kalibrasyonu, *çıplak* sensörü tanımlar. Difüzörün üzerine takılan fiziksel kapak, sensörün topladığı ışığı değiştirir; bu nedenle Chloros
, kalibrasyon paketinin üzerine fabrikada ölçülmüş bir **kapak düzeltme profili** uygular. Doğru kapağı bildirmek, kalibre edilmiş veriler elde etmenin bir parçasıdır — bu sayfa, modele göre hangi kapakların mevcut olduğunu, bunların nasıl bildirileceğini ve sensörün kalibre edilmiş spektral aralığının gerçekte ne olduğunu ele almaktadır.

## Modele göre kapak seçenekleri

| Kapak profili (`cap_id`) | Fiziksel kapak | DAQ-U | DAQ-M | DAQ-E |
| --- | --- | --- | --- | --- |
| `sunshine_cosine` | Güneş ışığı kosinüs düzeltici kapak (**her modelde varsayılan**) | Evet | Evet | Evet |
| `fov_15` / `fov_45` / `fov_90` | Görüş Alanı (FOV) sınırlayıcı koniler (15° / 45° / 90°) | Evet | — | Evet |
| `fov_30` / `fov_60` | Görüş Alanını Sınırlayan Koniler (30° / 60°) | Evet | — | — |
| `none` | Kapak takılı değil | — | — | Evet |

Modele özgü notlar:

* **DAQ-M'nin tek bir kapak profili vardır: `sunshine_cosine`.** "Çıplak artı Sunshine kapağı" ürün tanımını oluşturur ve çıplak bir DAQ-M'nin geometri profiline ihtiyacı yoktur.
* **Çıplak bir DAQ-U, tam anlamıyla çıplaktır** — hiçbir geometri profiline ihtiyacı yoktur; bu nedenle, bu model için `none` profili mevcut değildir.
* **DAQ-E üzerindeki `none`, "no-op" DEĞİLDİR.** DAQ-E'nin girintili, cam kaplı difüzörünün kendine özgü gerçek bir geometri düzeltmesi vardır; bu nedenle "kapaksız" durum, bu modelde ölçülen bir profildir.
* **Çıplak bir DAQ-E, hiçbir yükseklikte doğrudan güneş ışığını ölçemez** — Sunshine kapağı saha yapılandırmasıdır. Çıplak bir DAQ-E ile dış mekan çalışması planlamayınız.

GUI'nin sensör başına ayarlarında (Işık Sensörleri sekmesindeki dişli simgesi), **Kapak** açılır menüsü DAQ-U ve DAQ-M modellerinde "Yok (kapaksız sensör)" seçeneğini de sunar — bu iki modelde "kapaksız", yukarıdaki notlara göre kapak düzeltmesinin uygulanmadığı anlamına gelir. Yalnızca kapak fiziksel olarak çıkarıldığında bu seçeneği seçin.

## Kapağın belirtilmesi — ve bunun önemi

**Belirtilen `cap_id`, sensörün üzerinde fiziksel olarak bulunan kapakla eşleşmelidir.** Ne sensör ne de yazılım, takılı kapağı algılayamaz. Bu beyan iki şeyi belirler:

1. Her spektruma uygulanan **canlı düzeltme**.
2. **Her `.daq` kaydına yazılan kapak damgası**; bu damga, sonraki yansıma işleme aşamalarında güvenilir bir referans olarak kullanılır.

Sunshine kapağı, **tasarım gereği yaklaşık 12 kat** zayıflatma sağlar; bu nedenle, yanlış kapak beyanıyla yapılan kayıtlarda spektrumlar yaklaşık bu oranda yanlış ölçeklenir. Kapak değişikliklerini derhal beyan edin.

### Kapak Ayarı

GUI: Işık Sensörleri sekmesi → sensör satırındaki dişli simgesi → **Kapak** açılır menüsü. Her model için varsayılan değer `sunshine_cosine`'tir; bu bir varsayımdır (DAQ-U, DAQ-M ve DAQ-E kapaksız olarak gönderilir ve siz bir kapak seçene kadar kayıtlarda kapak varsayılır) ve seçilen değer proje boyunca korunur.



CLI<!-- SCREENSHOT-NEEDED: DAQ tab per-sensor settings modal (gear icon) scrolled to the Cap dropdown, open to show the per-model choices with "Sunshine (cosine corrector)" selected. Use a connected DAQ-E so the Hostname/Firmware/PTP rows are also visible above it. -->
(arka uç çalışıyor olmalıdır):

```bash
# Declare at connect time
chloros-cli daq pool-connect --eth-host daq-e-def330.local --cap-id sunshine_cosine

# Swap at runtime (after physically changing the cap)
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id fov_45
```

CLI
, sözdizimsel olarak tam `cap_id` listesini kabul eder (`{none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}`); her profil, bağlantı kurulduğunda sensörün modeline göre doğrulanır; bu nedenle, kullanılamayan bir kapak kimliği (örneğin, bir DAQ-U'da yalnızca E için geçerli bir kimlik) yanlış düzeltme yapmak yerine açık bir hata ile reddedilir. Hiçbir şey aktarılmadığında arka uçtaki varsayılan değer `sunshine_cosine`'tir.

Python
SDK
: Bağlantı kurulurken cap'e `connect_daq_sensor(cap_id="sunshine_cosine")` adını verin veya çalışma zamanında `DAQSensorSession.set_cap("fov_45")` ile değiştirin; bkz. [SDK
Referansı](../reference/sdk-reference.md#daq-sensor-sessions).

İleri düzey: Profiller, Chloros
kurulumunda `daq/cap_profiles/<u|m|e>/<cap_id>.json` altında bulunur ve kullanıcı başına `~/.chloros/daq_cap_profiles/<u|m|e>/<cap_id>.json` ile geçersiz kılınabilir.

Sınırlamalardan ayrı olarak, hiç yeniden kalibre edilmemiş sensörler otomatik olarak filodan türetilen küçük bir karanlık ofset düzeltmesi alır — kullanıcı müdahalesi gerekmez.

## DAQ-E-S: sabit difüzör ve eğim

**DAQ-E-S**, Chloros
açısından önemli iki farkı olan bir DAQ-E modelidir. Diğer her şey (keşif, bağlanma, PTP, kayıt, cihaz ayarları) tam olarak bir DAQ-E'de olduğu gibi çalışır.

* **Difüzörü sabittir, bu nedenle kapak düzeltmesi gerektirmez.** Difüzör, cihazın fabrika kalibrasyonu ölçüldüğünde ünitede takılıydı; dolayısıyla bu kalibrasyon zaten gerçek aşağı doğru ışınım yoğunluğunu gösterir. Chloros
, ne talep edilirse edilsin bu cihaza herhangi bir sınır profili uygulamaz: bu cihaz için adlandırılmış bir sınır profili, bir mesajla birlikte yok sayılır; kayıtlarında sınır profilinin modelden geldiği belirtilir ve varsayılan sınır profili uyarısı bu cihaz için asla tetiklenmez. Proje Ayarları → DAQ Işık Sensörü bölümünde, bu sensörün satırında herhangi bir sınır gösterilmez ve proje genelindeki sınır geçersiz kılma ayarı bu sensöre etki etmez. Verilerine manuel olarak sınır uygulamayınız: aksi takdirde, üstteki Güneş Işığı düzeltmesi yaklaşık 11 kat daha yüksek bir değer gösterir.
* **3 eksenli bir ivmeölçer barındırır.** Difüzörün kosinüs tepkisi yalnızca düz konumdayken geçerlidir; bu nedenle eğik bir sensör, başka hiçbir belirti göstermeden yanlış bir ışık şiddeti değeri bildirir. Dolayısıyla her DAQ-E-S okuması, eğim, yuvarlanma ve dikleme açısını içerir ve bunlardan birinden kaydedilen bir `.daq`, bunları her spektrumun yanına kaydeder (`imu_tilt_deg`, `imu_roll_deg`, `imu_pitch_deg`, …) yanında saklar; böylece bir bulutu, sarsılmış bir montajdan ayırt edebilirsiniz.

Her bir konum alanı ve konum akışındaki bir hatanın nasıl raporlandığı hakkında bilgi için bkz. [CLI
Referansında DAQ-E-S](../reference/cli-reference.md#daq-e-s--fixed-diffuser-and-tilt-sensor).

## Sunshine cap performansı (dış mekan konfigürasyonu)

Prosedürler oluştururken kullanabileceğiniz değerler:

| Özellik | Değer |
| --- | --- |
| Görüş alanı | 180° yarım küre |
| Kosinüs tepki hatası | 60°'ye kadar gelen ışık açısında ≤ ±4 %; 70°'ye kadar ≤ ±4,5 % |
| Düşük güneş açısı sınırı | Güneş yüksekliği ~15°'nin altında kullanılması önerilmez |
| Zayıflama | ~12× (tasarım gereği) |
| Kapakın yeniden takılma tekrarlanabilirliği | ≈ %1,5 |
| Nicel ışınım | Ortalama **≥ 15 s**'lik okumalar (cihaz özelliği, bir kusur değildir) |

Herhangi bir nicel ışınım değeri için — yansıma referansları dahil — tek bir kare yerine en az 15 saniyelik okumaların ortalamasını kullanın.

## Kalibre edilmiş spektral aralık

| Özellik | Değer |
| --- | --- |
| Spektral örnekleme | 5 nm'lik adımlarla 340–1010 nm (135 nokta) |
| Radyometrik olarak kalibre edilmiş aralık | **~374–974 nm** (yazılımda zorunlu tutulur) |

Sensör, 340–1010 nm aralığının tamamını rapor eder, ancak NIST'e izlenebilir radyometrik kazanç ~374–974 nm aralığını kapsar. Chloros
, spektral ağırlığının yarısından azı bu aralıkta bulunan herhangi bir kamera bandı için **mutlak yansıma ayrımını reddeder** ve kalibre edilmemiş bir çıktı üretmek yerine `dls-uncalibrated-band-<nm>` atlama nedenini bildirir. Piyasada bulunan kamera SKU’ları arasında yalnızca F988 filtresi bu aralığın dışındadır; bu model bunun yerine yansıma paneli iş akışını kullanır — bkz. [Yansıma İş Akışları](reflectance.md).

Sensör modelleri, aktarımlar ve sensör kimlikleri için [DAQ genel bakışına](README.md) bakınız. İşleme sırasında cap damgasının nasıl tüketildiğine ilişkin bilgi için [Kayıt ve .daq Biçimi](recording.md) bölümüne bakınız.
