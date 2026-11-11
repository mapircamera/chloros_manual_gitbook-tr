# LATTICE Kameralar

LATTICE, MAPIR'ın tarım ve bilimsel görüntüleme amaçlı modüler multispektral kamera sistemidir. Her LATTICE kamera, Sony IMX265 global deklanşör sensörü (**3,1 MP, 3,45 µm pikseller**) üzerine kurulmuştur ve**GigE Vision** cihazı olarak Ethernet üzerinden bağlanır.

Chloros 1.3.0, LATTICE kameralarını üç arayüz üzerinden canlı olarak kontrol eder — keşif, canlı önizleme, çekim ve senkronize çoklu kamera dizileri —:

| Arayüz    | Nerede                                                          | Platformlar                                                |
| ---------- | -------------------------------------------------------------- | -------------------------------------------------------- |
| GUI        | Chloros kenar çubuğundaki **Kameralar** sekmesi                         | Windows 10/11 x64                                        |
| CLI        | `chloros-cli lattice` komut ailesi                           | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |
| Python SDK | `chloros_sdk.connect_camera()` / `chloros_sdk.connect_array()` | Windows 10/11 x64, Linux x86\_64, Linux aarch64 (Jetson) |

> **Donanım mı arıyorsunuz?**Kamera modülleri, lensler, filtreler ve bantlar, çerçeveler ve montaj parçaları, kablolar, PoE ve tetikleme kabloları [**LATTICE kullanım kılavuzunda**](https://mapir.gitbook.io/lattice-camera) belgelenmiştir. Bu bölüm, Chloros'tan kameraların çalıştırılmasını ele almaktadır.

LATTICE kayıtları standart `.tif`/`.tiff` dosyalarıdır ve Chloros bunları her zaman ham kayıt halinden başlayarak işler. Komutların ve API'nin tam listesi için [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) bölümlerine bakınız.

## İki sensör yapılandırması

| Yapılandırma | Sensör       | Filtre                                | Bir kameranın sağladığı veriler                                          |
| ------------- | ------------ | ------------------------------------- | ----------------------------------------------------------------- |
| **M3C**| Bayer renkli | üçlü bant geçiren filtre                |**Tek bir pozlamadan elde edilen üç kalibre edilmiş bant**                 |
| **M3M**| Monokrom   | tek dar bantlı girişim filtresi |**Tek kalibre edilmiş bant**; indeksler için birden fazla M3M kamerasını birleştirin |

M3M kamera, tek bir filtrenin arkasında monokrom olduğundan, her bant kendi pozlamasını alır. Bir M3C kamera ise tek bir sensör pozlamasıyla üç bandın tamamını kapsar.

## Model dizeleri ve adlandırma

Her kamera, kimliğini GenICam `DeviceUserID` içinde bir model dizesi olarak saklar:

```
<sensor>-<lens>-F<filter>       e.g.  M3C-L41-FRGN,  M3M-L87-F450
```

Chloros, bunu `LATT-` önekiyle görüntüler (örneğin `LATT-M3M-L87-F450`). Aynı `LATT-…` dizesi, her dışa aktarımın EXIF `Model` etiketine yazılır ve işlenmiş projelerde kameranın çıktı klasörü adı olarak kullanılır.

| Bileşen | Değerler                                                   | Anlam                                                                                            |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Sensör    | `M3C` / `M3M`                                            | Bayer renkli / tek renkli                                                                          |
| Lens      | `L41` / `L87`                                            | Sayı, **derece cinsinden yatay görüş açısını** gösterir: L41 = dar (41°), L87 = geniş (87°)    |
| Filtre    | `FRGB` / `FRGN` / `FOCN` / `FNGB` (M3C) veya `F<nm>` (M3M) | Bkz. [Filtreler ve Spektral Bantlar](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands) |

Model dizesi, sonraki tüm işlemleri yönlendirir: Chloros, `DeviceUserID` + `DeviceSerialNumber` değerlerinden sensör profilini, bant düzenini ve fabrika kalibrasyonunu belirler. Kamera başına yapılandırılması gereken hiçbir şey yoktur — bkz. [Kameraları Bağlama](connecting.md).

## Filtreler ve bantlar

Bant merkezleri, FWHM kenarları ve 23 SKU'luk tam M3M kataloğu ürün özellikleridir, bu nedenle donanım kılavuzunda yer alırlar: [**Filtreler ve Spektral Bantlar**](https://mapir.gitbook.io/lattice-camera/hardware/filters-and-bands).

Yazılım açısından önemli olan nokta şudur: model dizesindeki filtre kodu, Chloros'un hangi ürünleri oluşturabileceğini belirler. RGB filtreli kameralar (`FRGB`) yalnızca debayering işleminden geçirilmiş ve önizleme ürünleri üretir — geniş bantlı bir sensör için bant başına parlaklık ve yansıma değerleri anlamlı değildir, bu nedenle Chloros bunları atlar ve bunu belirtir. Diğer tüm filtreler tam parlaklık → yansıma → indeks zincirini sağlar.

## Radyometrik kalibrasyona genel bakış

Her LATTICE kamera, fabrikada NIST'e izlenebilir bir zincir kullanılarak ayrı ayrı kalibre edilir ve kamera başına bir sertifika ile birlikte gönderilir. Bunun kapsamı, nasıl ölçüldüğü ve belirtilebilecek doğruluk derecesi donanım kılavuzunda yer almaktadır: [**Fabrika Radyometrik Kalibrasyonu**](https://mapir.gitbook.io/lattice-camera/calibration/factory-radiometric-calibration).

Yazılım açısından önemli olan, bir kamera bağlandığında Chloros'un doğru kalibrasyonu belirlemesi ve uygulanan katsayıları her dışa aktarımda sabitlemesidir — bkz. [Kameraları Bağlama](connecting.md).

## Bu bölümde

* [Kameraları Bağlama](connecting.md) — otomatik keşif, GUI bağlantı iletişim kutusu, CLI/SDK eşdeğerleri ve bir kamera bağlandığında fabrika kalibrasyonunun nasıl belirlendiği (kamera üzerindeki paket mi, bulut mu).
* [Kamera Ayarları](camera-settings.md) — Kameralar sekmesindeki tüm denetimler: kamera listesi, kamera başına ayarlar bölmesi ve dizi ayarları bölmesi (ROI düzenleyicisi ve hizalama dahil).
* [Yakalama Ayarları ve Modları](capture.md) — Tümünü Yakala, Tekli / Sürekli / Aralıklı yakalama, dışa aktarma türleri ve yakalama klasör yapısı.
* [Çoklu Kamera Dizileri](arrays.md) — dizi bağlama sihirbazı, senkronizasyon kablosu probu, bant genişliği, PTP ve canlı dizi görünümü.
* [Tek Renkli Kameralar ve Bitki Örtüsü Endeksleri](mono-indices.md) — M3M kameralar ve hizalama → yığınlama → endeksleme iş akışı.

Komutların tam listesi [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) bölümlerinde yer almaktadır.
