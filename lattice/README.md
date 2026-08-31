# LATTICE Kameralar

LATTICE, MAPIR’in tarım ve bilimsel görüntüleme amaçlı modüler multispektral kamera sistemidir. Her LATTICE kamera, Sony IMX265 global deklanşör sensörü (**3,1 MP, 3,45 µm pikseller**) üzerine kurulmuştur ve**GigE Vision** cihazı olarak Ethernet üzerinden bağlanır.

Chloros 1.2.0, LATTICE kameralarını üç arayüz üzerinden canlı olarak kontrol eder — keşif, canlı önizleme, çekim ve senkronize çoklu kamera dizileri —:

| Arayüz    | Nerede                                                          | Platformlar                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Chloros kenar çubuğundaki **Kameralar** sekmesi                         | Windows 10/11 x64                                        |
| CLI        | `chloros-cli lattice` komut ailesi                           | Windows 10/11 x64, Linux x86_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Donanımı mı arıyorsunuz?**Kamera modülleri, lensler, filtreler ve bantlar, çerçeveler ve montaj parçaları, kablolar, PoE ve tetikleme kablolaması [**LATTICE kullanım kılavuzunda**](https://mapir.gitbook.io/lattice-camera) ayrıntılı olarak açıklanmıştır. Bu bölüm, Chloros&#x27;ten kameraların çalıştırılmasını ele almaktadır.

LATTICE çekimleri, standart `.tif`/`.tiff` dosyalarıdır ve Chloros bunları her zaman ham çekimden başlayarak işler. Tam komut ve API yüzeyi hakkında bilgi için [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) bölümlerine bakın.

## İki sensör yapılandırması

| Yapılandırma | Sensör       | Filtre                                | Bir kameranın sağladığı veriler                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| Bayer renkli | üçlü bant geçiren filtre                |**Tek bir pozlamadan elde edilen üç kalibre edilmiş bant**                 |
| **M3M**| Monokrom   | tek dar bantlı girişim filtresi |**Bir kalibre edilmiş bant**; indeksler için birden fazla M3M kamerasını birleştirin |

M3M kamera, tek bir filtrenin arkasında tek renkli çalıştığı için her bant kendi pozlamasına sahiptir. M3C kamera ise tek bir sensör pozlamasıyla üç bandın tamamını kapsar.

## Model dizeleri ve adlandırma

Her kamera, kimliğini GenICam `DeviceUserID` içinde bir model dizesi olarak saklar:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros, bunu `LATT-` önekiyle görüntüler (örneğin `LATT-M3M-L87-F450`). Aynı `LATT-…` dizesi, her dışa aktarımın EXIF `Model` etiketine yazılır ve işlenmiş projelerde kameranın çıktı klasör adı olarak kullanılır.

| Bileşen | Değerler                                                   | Anlam                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Sensör    | `M3C` / `M3M`                                            | Bayer renkli / tek renkli                                                                          |
| Lens      | `L41` / `L87`                                            | Bu sayı, **derece cinsinden yatay görüş açısı**&#x27;dır: L41 = dar (41°), L87 = geniş (87°)    |
| Filtre    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) veya `F<nm>` (M3M) | Bkz. [Filtreler ve Spektral Bantlar](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Model dizesi, sonraki tüm işlemleri yönlendirir: Chloros, `DeviceUserID` + `DeviceSerialNumber` değerlerinden sensör profilini, bant düzenini ve fabrika kalibrasyonunu belirler. Kamera başına yapılandırılması gereken herhangi bir şey yoktur — bkz. [Kameraları Bağlama](connecting.md).

## Filtreler ve bantlar

Bant merkezleri, FWHM kenarları ve 23 SKU&#x27;luk tam M3M kataloğu ürün özellikleridir, bu nedenle donanım kılavuzunda yer alırlar: [**Filtreler ve Spektral Bantlar**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Yazılım açısından önemli olan husus: model dizesindeki filtre kodu, Chloros&#x27;in hangi ürünleri oluşturabileceğini belirler. RGB filtreli kameralar (`FRGB`) yalnızca debayering uygulanmış ve önizleme ürünleri üretir — geniş bantlı bir sensör için bant başına parlaklık ve yansıma değerleri anlamlı değildir, bu nedenle Chloros bunları atlar ve bunu belirtir. Diğer tüm filtreler tam parlaklık → yansıma → indeks zincirini verir.

## Radyometrik kalibrasyona genel bakış

Her LATTICE kamera, fabrikada NIST&#x27;e izlenebilir bir zincir kullanılarak ayrı ayrı kalibre edilir ve kamera başına bir sertifika ile birlikte gönderilir. Bunun kapsamı, nasıl ölçüldüğü ve belirtilebilecek doğruluk derecesi donanım kılavuzunda yer almaktadır: [**Fabrika Radyometrik Kalibrasyonu**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Yazılım açısından önemli olan, bir kamera bağlandığında Chloros&#x27;in doğru kalibrasyonu belirlemesi ve uygulanan katsayıları her dışa aktarımda sabitlemesidir — bkz. [Kameraları Bağlama](connecting.md).

## Bu bölümde

* [Kameraları Bağlama](connecting.md) — otomatik keşif, GUI bağlantı diyalog penceresi, CLI/SDK eşdeğerleri ve bir kamera bağlandığında fabrika kalibrasyonunun nasıl belirlendiği (kamera üzerindeki paket mi, bulut mu).

Diğer LATTICE konuları — kamera ayarları ve canlı kontrol, yakalama modları, çoklu kamera dizileri ile mono (M3M) işleme ve indeksler — bu kılavuzun ilgili bölümlerinde ele alınmaktadır; komutların tam listesi ise [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) bölümlerinde yer almaktadır.
