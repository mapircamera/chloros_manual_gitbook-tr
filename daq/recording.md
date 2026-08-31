# Kayıt ve .daq Biçimi

`.daq` dosyası, Chloros&#x27;in ışık sensörü kayıt biçimidir: bir DAQ sensöründen alınan, kalibre edilmiş spektral karelerden oluşan bir **SQLite veritabanı**. Bir yakalama oturumu sırasında bir kayıt yapıldığında, yansıma işleme zinciri daha sonra her görüntüyü tam o anda ölçülen aşağıya doğru ışık şiddetiyle bölebilir.

## Bir .daq dosyasının içeriği

| Özellik | Değer |
| --- | --- |
| Kap | SQLite veritabanı, her kayıt için her sensöre ait bir dosya |
| Dosya adı | **Sensör kimliği**ve bir**zaman damgası** içerir, örn. `daq_data_daq-e-def330_2026_04_13_18h30m00.daq` |
| Kare başına spektrum | 135 nokta, 5 nm&#x27;lik adımlarla 340–1010 nm aralığında, artı CIE XYZ üç uyarıcı |
| Birimler | Kalibre edilmiş spektral ışık şiddeti, **W/m²/nm** (fabrika kalibrasyon paketi + kapak profili uygulanmış) |
| Damgalı meta veriler | Sensör kimliği (o ünitenin fabrika kalibrasyonunu almak için anahtar) ve geçerli kapak profili — bkz. [Kapak Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md) |

Biçim DAQ-U, DAQ-M ve DAQ-E&#x27;de aynıdır, bu nedenle sonraki işlemlerde kaydın hangi aktarım yoluyla yapıldığı önemli değildir.

Kalibre edilmiş kayıt için sensörün fabrika kalibrasyon paketine ihtiyaç vardır. DAQ-U ve DAQ-M için arka uç, sensör kimliği kullanılarak MAPIR&#x27;un bulutundan paketi alır (bu yapılamazsa kayıt reddedilir); DAQ-E üniteleri, kalibrasyonlarını cihazda sakladıkları için bu kuralın dışındadır.

## GUI&#x27;den Kayıt

GUI&#x27;den kayıt yapmak için **açık bir proje** gerekir (aksi takdirde Kayıt düğmeleri devre dışıdır):

* **Tümünü Kaydet / Tümünü Durdur** — Işık Sensörleri kenar çubuğunun üst kısmında bulunur; bağlı tüm sensörlerde `.daq` kaydını aynı anda başlatır veya durdurur.
* **Kaydet / Kaydı Durdur** — sensör başına, dişli ayarları penceresinde bulunur. Kayıt sırasında sensörün canlı bilgi satırlarında kırmızı bir &quot;REC&quot; göstergesi görünür.

Dosyalar `<project>/light_sensor/`&#x27;e yazılır ve kayıt durduğunda — ister Durdur, Hepsi Durdur seçeneği ile ister kayıt sensörünün bağlantısının kesilmesi yoluyla olsun — tamamlanan `.daq` dosyası **açık projeye otomatik olarak eklenir**. Projenin dosya listesinde, yansıma işleme için zaten hazır olan ve manuel ekleme adımı gerektirmeyen bir şekilde görünür.

<!-- SCREENSHOT-NEEDED: Light Sensors tab with one DAQ sensor connected and recording: sidebar showing the red "Stop All" state of the Record All button, the sensor row, and the settings modal open with the red "REC" indicator visible in the live info rows. -->

<!-- SCREENSHOT-NEEDED: File Browser / project file list immediately after stopping a DAQ recording, showing the .daq file auto-added to the open project alongside imagery. -->

## CLI&#x27;ten Kayıt

CLI, arka ucun sensör havuzu aracılığıyla kayıt yapar (arka uçun çalışıyor olması gerekir — bu komutlar, hafif HTTP istemcileridir):

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
| `--output` / `-o` | `~/Documents/DAQ Live View/`, CLI&#x27;in değil, **arka ucun** dosya sisteminde |

CLI başka bir makinedeki bir arka ucu hedeflediğinde çıktı dizini ayrımı önemlidir: dosya, arka ucun çalıştığı yere kaydedilir.

## Python&#x27;ten kayıt

`DAQSensorSession` (`chloros_sdk.connect_daq_sensor()` tarafından döndürülür), aynı havuzlanmış kaydı gösterir: `record_start(output_dir=None, device_name=None)` dosya yolunu döndürür, `record_stop()` ise `{path, rows}`&#x27;i döndürür. Tam oturum API için [SDK Referansı](../reference/sdk-reference.md) bölümüne bakın. SDK&#x27;in doğrudan donanım sınıfları (yalnızca masaüstü kurulumları), kayıtları varsayılan olarak `~/Documents/DAQ/`&#x27;e yazar; yayınlanmış sürümler için yukarıdaki havuzlanmış yol desteklenen yoldur.

## İşleme sırasında bir .daq dosyası kullanma

Görüntülerden yansıma değeri elde etmek için, Chloros&#x27;in her pozlamaya uygun aşağı doğru ışınım değerine ihtiyacı vardır:

* **`.daq` dosyasını görüntülerle birlikte saklayın.** İşleme sırasında iş akışı, kaydedilmiş bir `.daq` dosyasından (herhangi bir DAQ modeli) veya görüntülerle birlikte bulunan bir DAQ-M yerel `.csv` — dosyasından otomatik olarak çözer. GUI kayıtları, durdukları anda projeye eklendikleri için bu koşulu otomatik olarak karşılar.
* **Kalibrasyon talep üzerine alınır.** Kamera başına veya DAQ başına fabrika kalibrasyon paketi henüz yerel olarak önbelleğe alınmamışsa, Chloros ilk kullanımda bunu MAPIR&#x27;in bulutundan otomatik olarak alır (bir kez internet bağlantısı gerekir; `~/.chloros/` altında önbelleğe alınır).
* **Canlı yakalamalar kendi sidecar dosyalarını oluşturur.** Canlı olarak yakalanan herhangi bir yansıma karesi için, fiilen kullanılan DAQ okuma değeri görüntünün yanına bir `.daq` sidecar dosyası olarak kaydedilir; böylece yakalama, orijinal kayıt olmadan daha sonra yeniden işlenebilir.

## Işınım değerini geri alma

Bir projenin işlenmesi, içerdiği tüm ışık sensörü kayıtlarını da görüntü ürünlerinin yanındaki bir
`Light Sensor/` klasörüne aktarır. Bunun için **görüntü** gerekmez:
tek başına uçurulan bir ışık sensörü tam bir yakalama oluşturur ve yalnızca `.daq`
dosyalarını içeren bir klasör geçerli bir girdidir. Çalışma, kaç adet ışık sensörü ürünü yazdığını bildirir.

| Ürün | Nedir |
| --- | --- |
| `<name>_calibrated.daq` | Canlı kayıtla aynı şemaya sahip, yeniden işlenebilir bir arşiv; artık onu üreten kalibrasyon paketini belirtir. Yeniden içe aktarılması, onu ikinci kez kalibre **etmez**. |
| `<name>_calibrated.csv` | Sensörün kendi dalga boyu ızgarasında W/m²/nm cinsinden spektral ışık şiddeti; her okuma için bir satır ve ayrıca fotometrik sütunlar: toplam güç, fotopik ve skotopik lüks, mavi/yeşil/kırmızı ayrımına sahip PPFD ve tepe dalga boyu. |

Kalibrasyon paketi alınamayan bir DAQ-U veya DAQ-M — çevrimdışı olmanız veya
o sensörün dosyada kalibrasyonunun bulunmaması nedeniyle — **bir gerekçe ile atlanır**, asla ham sayıları içeren
&quot;kalibre edilmiş&quot; bir dosya olarak yazılmaz. İnternete bağlanın ve işlemi yeniden çalıştırın. Bir DAQ-E
kendi kalibrasyonunu taşır, bu nedenle buna yalnızca ünite bağlı olmadığında ve
yerel olarak önbellekte hiçbir şey bulunmadığında ihtiyaç duyar.

### DAQ-A: ham sayımlar ve bunun neden doğru cevap olduğu

**DAQ-A**, seri bazlı kalibrasyon paketi sisteminden daha eskidir ve alınacak bir paketi
bulunmaz. Bu bir ihmal değildir: bir DAQ-A, sahada bir
yansıtma hedefi kullanılarak kalibre edilir ve hedef tabanlı kalibrasyon için yalnızca sensörün *göreceli*
tepkisi gereklidir — ki bu da tam olarak ham sayımlarının ifade ettiği şeydir. Chloros bugün bunlarla kalibre edilmektedir.

Dolayısıyla bir DAQ-A kaydı dışa aktarılabilir, ancak farklı bir adla:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq
    └── <name>_raw.csv
```

`_raw`, `_calibrated` değil — dosya içindeki bir işaret yerine farklı bir dosya adı,
çünkü bu bilgi, dosyanın sadece adıyla e-posta yoluyla gönderilmesi durumunda da korunmalıdır. `.csv`
başlığı, `raw spectral sensor counts (NOT irradiance)` yazıyor ve değerlerin
sensörler arasında değil, dosya **içinde** karşılaştırılabilir olduğu konusunda uyarıyor. Yalnızca gerçek ışık şiddeti için
anlam ifade eden sütunlar — toplam güç, lüks, PPFD — sayımlardan hesaplanmak yerine
boş bırakılır.

Eski DAQ-A-SD kayıtları (şema v1.01 / v1.02), dosyanın yazma zamanını kaydeder,
okuma başına bir zaman damgası kaydetmez. Chloros, görüntüleri bunlarla eşleştirmez — bir kareyi bir
yazma zamanıyla eşleştirmek, yanlış görünmese de yanlış olur — ancak dışa aktarım bunları sorunsuz okur ve
CSV, hangi saatte olduğunu belirtir.

Yansıma ölçümleriyle ilgili tüm ayrıntılar için — tek sensörlü kamera ve çift sensörlü ortam/nesne ölçümleri — bkz. [Yansıma İş Akışları](reflectance.md).
