# Kayıt ve .daq Biçimi

`.daq` dosyası, Chloros

’un ışık sensörü kayıt biçimidir: tek bir DAQ sensöründen alınan, kalibre edilmiş spektral karelerden oluşan bir **SQLite veritabanı**. Bir yakalama oturumu sırasında bir dosya kaydedildiğinde, yansıma işleme zinciri daha sonra her görüntüyü tam o anda ölçülen aşağıya doğru ışık şiddetine göre bölebilir.

## Bir .daq dosyasının içeriği

| Özellik | Değer |
| --- | --- |
| Konteyner | SQLite veritabanı, her kayıt için her sensöre ait bir dosya |
| Dosya Adı | **Sensör kimliği**ve bir**zaman damgası** içerir, örn. `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Kare başına spektrum | 135 nokta, 5 nm&#x27;lik adımlarla 340–1010 nm aralığında, artı CIE XYZ üç uyarıcı |
| Birimler | Kalibre edilmiş spektral ışık şiddeti, **W/m²/nm** (fabrika kalibrasyon paketi + kapak profili uygulanmış) |
| Damgalı meta veriler | Sensör kimliği (o ünitenin fabrika kalibrasyonunu almak için anahtar) ve geçerli kapak profili — bkz. [Kapak Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md) |

Biçim DAQ-U, DAQ-M, DAQ-E ve DAQ-E-S arasında aynıdır, bu nedenle sonraki işlemler sırasında kaydın hangi aktarım yoluyla yapıldığı önemli değildir. Bir DAQ-E-S kaydı ayrıca her spektrumun yanında ünitenin eğim, yuvarlanma ve dikleme açılarını da saklar.

**Kayıtlar elektrik kesintisinden etkilenmez (1.3.0).**Canlı bir kayıt, her kaydetme işleminde bir günlük ve tam disk senkronizasyonu ile yazılır; bu nedenle, bir elektrik kesintisi veya sonlandırılan bir işlem, dosyanın tamamını değil, yalnızca son 3 saniyeyi kaybetmenize neden olur. Yazma işlemi yarıda kesilen bir kayıt, `.daq` dosyasının yanında bir `<name>.daq-journal` dosyası bırakabilir:**bu iki dosyayı birlikte saklayın ve kopyalayın.** Chloros

, dosyayı ilk okuduğunda günlüğü geri alır. Disk meşgul olduğunda dosya gecikir, ancak sensör okumaları asla gecikmez.

**Veri göndermeyi durduran bir DAQ-E yeniden bağlanır (1.3.0).** Bir DAQ-E&#x27;nin akışı kesildiğinde (anahtarının gücü kapatılıp açıldığında veya bağlantı kesildiğinde), arka uç onu önceki ayarlarıyla yeniden bağlar ve üzerindeki kayıt, dosyada boşluk işaretlenmiş olarak aynı `.daq` dosyasında devam eder.

Kalibre edilmiş kayıt için sensörün fabrika kalibrasyon paketine ihtiyaç vardır. DAQ-U ve DAQ-M için arka uç, sensör kimliğine göre MAPIR

&#x27;ın bulutundan paketi alır (bunu yapamazsa kayıt reddedilir); DAQ-E üniteleri, kalibrasyonlarını cihazda tuttukları için bu kuralın dışındadır.

## GUI&#x27;den Kayıt

GUI&#x27;de kayıt yapmak için **açık bir proje** gerekir (aksi takdirde Kayıt düğmeleri devre dışıdır):

* **Tümünü Kaydet / Tümünü Durdur** — Işık Sensörleri kenar çubuğunun üst kısmında bulunur; bağlı tüm sensörlerde `.daq` kaydını aynı anda başlatır veya durdurur.
* **Kaydet / Kaydı Durdur** — sensör başına, dişli ayarları penceresinde bulunur. Kayıt sırasında sensörün canlı bilgi satırlarında kırmızı bir &quot;REC&quot; göstergesi görünür.

Dosyalar `<project>/light_sensor/` dosyasına yazılır ve kayıt durduğunda — ister Durdur, Hepsi Durdur seçeneği ile ister kayıt sensörünün bağlantısının kesilmesi yoluyla olsun — tamamlanan `.daq` dosyası **açık projeye otomatik olarak eklenir**. Dosya, manuel ekleme adımına gerek kalmadan projenin dosya listesinde görünür ve yansıma işleme için hazır hale gelir.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->



## CLI

&#x27;den Kayıt

CLI

, arka ucun sensör havuzu aracılığıyla kayıt yapar (arka uç çalışıyor olmalıdır — bu komutlar ince HTTP

istemcileridir):

```bash
# Connect the sensor into the backend pool
chloros-cli daq pool-connect --eth-host daq-e-def330.local

# Record for 150 seconds, with a human-friendly device label
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
    -o ./out --device-name "rooftop-A"

# Or run open-ended and stop explicitly
chloros-cli daq pool-record --sensor-id daq-e-def330            # --duration defaults to 0 = run until --stop
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

`--sensor-id` değerini `chloros-cli daq pool-list`&#x27;ten alın. Bilinmesi gereken iki varsayılan ayar:

| Seçenek | Varsayılan |
| --- | --- |
| `--duration` | `0` — `pool-record --stop` değerine kadar kayıt yap |
| `--output` / `-o` | CLI

&#x27;nin değil, **arka uçun** dosya sistemindeki `~/Documents/DAQ Live View/` |

CLI

&#x27;nin başka bir makinedeki bir arka uca yöneldiği durumlarda çıktı dizini ayrımı önemlidir: dosya, arka ucun çalıştığı yere kaydedilir. Arka uç aynı bilgisayarda ise, göreceli bir `-o` (yukarıdaki `./out` gibi) komutu çalıştırdığınız klasöre göre çözümlenir.

## Python

&#x27;dan kayıt `DAQSensorSession` (`chloros_sdk.connect_daq_sensor()` tarafından döndürülür) aynı havuzlanmış kaydı gösterir: `record_start(output_dir=None, device_name=None)` dosya yolunu, `record_stop()` ise `{path, rows}`&#x27;i döndürür. Tam oturum API

&#x27;si için [SDK

Referansı](../reference/sdk-reference.md) bölümüne bakın. SDK

&#x27;nin doğrudan donanım sınıfları (yalnızca masaüstü kurulumları), kayıtları varsayılan olarak `~/Documents/DAQ/`&#x27;e yazar; yayınlanmış sürümler için yukarıdaki havuzlanmış yol desteklenen yoldur.

## İşleme sırasında bir .daq dosyası kullanma

Görüntülerden yansıma değeri elde etmek için Chloros

&#x27;un her pozlamaya uygun aşağı doğru ışınım değerine ihtiyacı vardır:

* **`.daq` dosyasını görüntülerle birlikte saklayın.** İşleme sırasında iş akışı, kaydedilmiş bir `.daq` dosyasından (herhangi bir DAQ modeli) — veya görüntülerle birlikte bulunan bir DAQ-M yerel `.csv` — dosyasından otomatik olarak çözer. GUI kayıtları, durdukları anda projeye eklendikleri için bu koşulu otomatik olarak karşılar.
* **Kalibrasyon, talep üzerine alınır.** Kamera başına veya DAQ başına fabrika kalibrasyon paketi henüz yerel olarak önbelleğe alınmamışsa, Chloros

ilk kullanımda bunu MAPIR

&#x27;ın bulutundan otomatik olarak alır (bir kez internet bağlantısı gerekir; `~/.chloros/` altında önbelleğe alınır).
* **Canlı yakalamalar kendi sidecar dosyalarını yazar.** Canlı olarak yakalanan herhangi bir yansıma karesi için, fiilen kullanılan DAQ okuma değeri, yakalamaların `daq/` klasöründe bir `.daq` dosyası olarak kaydedilir; böylece yakalama, orijinal kayıt olmadan daha sonra yeniden işlenebilir.
* **`chloros_scripts` kayıt cihazlarından alınan kayıtlar (1.3.0).**Genel [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) kayıt cihazları tarafından yazılan bir `.daq` dosyası, onu oluşturan DAQ modeli olarak listelenir ve sınırı, diğer kayıtlar gibi**Proje Ayarları → DAQ Işık Sensörü** altında diğer kayıtlar gibi ayarlanabilir. Bu kayıt cihazları ham sensör sayımlarını yazar; Chloros

, sensörün fabrika kalibrasyonunu seri bağlantı yoluyla alır ve uygular.
* **Okumanın kime ait olduğu (1.3.0).**Her bir `.daq` yakalamasının yanında, `.daq.cameras.json` adı, o okumanın hangi kameranın/kameraların karelerini tanımladığını belirtir ve işleme**yalnızca bu kameralardan gelen görüntülerle** eşleştirilir. Bu nedenle, kendi ışık sensörü olmayan bağımsız bir kamera artık başka bir kameranın okuma değerini kullanmaz — bu kameraya bir sensör bağlayın veya bir sensör kaydı yapın. DAQ ile yapılan bir kayıt (GUI Record, `daq pool-record`) ve bu dosya içermeyen herhangi bir `.daq` (eski yakalamalar dahil), her kameraya hizmet etmeye devam eder.

## Işınım değerini geri alma

Bir projenin işlenmesi, içerdiği tüm ışık sensörü kayıtlarını da görüntü ürünlerinin yanındaki bir
`Light Sensor/` klasörüne aktarır. Bunun için görüntü **gerekmez**:
tek başına kaydedilmiş bir ışık sensörü kaydı tam bir yakalamadır ve yalnızca `.daq`
dosyalarını içeren bir klasör geçerli bir girdidir. Yalnızca ışık sensörü kayıtlarını içeren bir proje, bunları listeler ve
hem uygulamadan hem de CLI

&#x27;den işlenir; ayrıca çalıştırma sonrasında da listede kalır. Çalıştırma,
kaç adet ışık sensörü ürünü yazdığını bildirir.

| Ürün | Nedir |
| --- | --- |
| `<name>_calibrated.daq` | Canlı kayıtla aynı şemaya sahip, yeniden işlenebilir bir arşivdir; artık onu üreten kalibrasyon paketini de belirtir. Tekrar içe aktarılması, onu ikinci kez kalibre etmez. |
| `<name>_calibrated.csv` | Sensörün kendi dalga boyu ızgarasında W/m²/nm cinsinden spektral ışık şiddeti; her okuma için bir satır ve ayrıca fotometrik sütunlar: toplam güç, fotopik ve skotopik lüks, mavi/yeşil/kırmızı ayrımına sahip PPFD ve tepe dalga boyu. |

Kalibrasyon paketi alınamayan bir DAQ-U veya DAQ-M — çevrimdışıysanız veya
o sensörün dosyasında kalibrasyon yoksa — **bir neden belirtilerek atlanır**, asla ham sayıları içeren
&quot;kalibre edilmiş&quot; bir dosya olarak yazılmaz. İnternete bağlanın ve işlemi yeniden çalıştırın. Bir DAQ-E
kendi kalibrasyonunu taşır, bu nedenle buna yalnızca ünite bağlı olmadığında ve
yerel olarak önbelleğe alınmış hiçbir şey olmadığında ihtiyaç duyar.

### DAQ-A: ham sayımlar ve bunun neden doğru cevap olduğu

**DAQ-A**, seri numarasına göre kalibrasyon paketi sisteminden daha eskidir ve alınacak
bir paketi yoktur. Bu bir ihmal değildir: bir DAQ-A, sahada bir
yansıtma hedefi kullanılarak kalibre edilir ve hedef tabanlı kalibrasyon için yalnızca sensörün *göreceli*
tepkisi gereklidir — ki bu da tam olarak ham sayım değerleridir. Chloros

, günümüzde bu değerlerle kalibrasyon yapmaktadır.

Dolayısıyla bir DAQ-A kaydı dışa aktarılabilir, ancak farklı bir adla:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, `_calibrated` değil — dosya içindeki bir işaret yerine farklı bir dosya adı kullanılır,
çünkü bu bilgi, dosyanın sadece dosya adı olarak e-posta ile gönderilmesi durumunda da korunmalıdır. `.csv`
başlığı, `raw spectral sensor counts (NOT irradiance)` yazıyor ve değerlerin
sensörler arasında değil, dosya **içinde** karşılaştırılabilir olduğu konusunda uyarıyor. Yalnızca gerçek ışık şiddeti için
anlam ifade eden sütunlar — toplam güç, lüks, PPFD — sayımlardan hesaplanmak yerine
boş bırakılır.

Eski DAQ-A-SD kayıtları (şema v1.01 / v1.02), her okuma için ayrı bir
zaman damgası değil, yalnızca dosyanın yazılma zamanını kaydeder. Chloros

, görüntüleri bunlarla eşleştirmez — bir kareyi bir
yazma zamanıyla eşleştirmek, yanlış görünmese de yanlış olur — ancak dışa aktarım bunları sorunsuz okur ve
CSV

dosyası hangi saatte olduğunu belirtir.

Yansıma ile ilgili tüm ayrıntılar için — kamera ile tek sensör ve ortam/nesne için çift sensör — bkz. [Yansıma İş Akışları](reflectance.md).
