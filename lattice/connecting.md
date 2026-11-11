# Kameraların Bağlanması

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Herhangi bir şey bağlanmadan önce "Kameralar" sekmesi</p></figcaption></figure>

Chloros, bağlantı üzerindeki LATTICE kameralarını otomatik olarak algılar — GUI'deki Kameralar sekmesinden, `chloros-cli lattice`'ten veya Python SDK'den. Kameranın model dizesi, sonraki tüm işlemleri yönlendirir: Chloros, kameranın `DeviceUserID` + `DeviceSerialNumber` dosyalarından sensör profilini, bant düzenini ve fabrika kalibrasyonunu belirler; bu nedenle **her kamera için ayrı bir yapılandırma yapmaya gerek yoktur**.

Bağlanmadan önce, ana bilgisayar ağının ayarlandığından emin olun — bağlantı-yerel adresleme, jumbo çerçeveler ve diziler için NIC alım arabelleği ayarları. Bu, donanım tarafındaki kurulumdur ve LATTICE kılavuzunda yer alır: [**Ağ Kurulumu**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## GUI'den Bağlanma

Chloros kenar çubuğundaki **Kameralar**sekmesini açın (donanım sekmeleri, arka uçun başlatılması tamamlandığında görünür) veya ana menü →**Kameraya Bağlan**seçeneğini kullanın. Her ikisi de**Kameraya(lara) Bağlan** iletişim kutusunu açar.

### Kamera(lar)na Bağlan iletişim kutusu

İletişim kutusu açıldığı anda ağı tarar ("Ağı tarıyor...") ve bulduğu tüm kameraları listeler. Her satırda kameranın **modeli**(örn. `LATT-M3M-L41-F550`),**seri numarası**ve**IP adresi** gösterilir.

* **Bir satırı tıklayarak seçin**(yeşil renkle vurgulanır).**Birden fazla kamera** seçebilir ve bunları tek seferde bağlayabilirsiniz — Chloros, kameraları sırayla bağlar.
* **"Bağlandı"** etiketine sahip satırlar zaten bağlıdır ve yeniden seçilemez.
* **"Dizide"** etiketine sahip satırlar, şu anda bağlı bir kamera dizisine aittir. O kamerayı tek başına kullanmak için önce dizinin bağlantısını kesin.
* **Bağlan** — seçilen kameraları bağlar; birden fazla kamera seçildiğinde düğmede bir sayı gösterilir, ör. "Bağlan (3)".
* **Yeniden Tara** — keşif işlemini tekrar başlatır.
* **Kapat** — iletişim kutusunu kapatır.
* Tarama sonuçsuz tamamlanırsa, iletişim kutusunda **"Ağda kamera bulunamadı"** mesajı görüntülenir — aşağıdaki [Sorun Giderme](connecting.md#troubleshooting) bölümüne bakın.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Kamera(lar)ı Bağla iletişim kutusu — burada ağda kamera bulunmadığı durum gösterilmektedir</p></figcaption></figure>

### İlk bağlantı: kalibrasyon paketi indirme

Belirli bir kamera bir cihaza **ilk kez** bağlandığında, Chloros, GigE üzerinden kameranın kendisinden kameranın fabrika kalibrasyon paketini (\~3,8 MB) alır. Bu işlem devam ederken, iletişim kutusunda her seri numarası için ayrı bir ilerleme çubuğu içeren yeşil renkli **"Kameradan kalibrasyon verileri indiriliyor"**paneli görüntülenir — kamera başına yaklaşık**70 saniye** sürer. Altıdan fazla kamera olduğunda, ilerleme satırları panel içinde kayar. Paket ana bilgisayarda önbelleğe alınır; bu sayede aynı kameranın sonraki bağlantılarında indirme işlemi tamamen atlanır (ve panel hiçbir zaman gösterilmez).

### Sistemi Analiz Et

Diyalog penceresindeki **Sistemi Analiz Et** düğmesi, ana bilgisayarı ve ağı inceler (işlem sürerken etiket "Analiz ediliyor..." olarak görünür) ve bir tanılama raporu oluşturur:

* **Ana Bilgisayar** — CPU çekirdekleri ve RAM; GPU adı ve belleği veya "GPU: Algılanmadı".
* **Ağ Arabirimleri** — her bir ağ kartının adı, bağlantı hızı, MTU (etkin olduğunda "jumbo" etiketi ile birlikte), yukarı/aşağı durumu ve bir USB veriyolunda yer alıp almadığı.
* **Kameralar**— seri numarası, model, IP ve**her kameranın hangi ağ kartında bulunduğu**.
* **Performans** — piksel formatı için kamera başına mevcut ve ideal fps değerleri; ideal değer mevcut değeri aştığında yeşil renkli "Potansiyel: N× iyileştirme mümkün" satırı görüntülenir.
* **Uyarılar ve numaralı öneriler** — düzeltilmesi gereken bir durum olmadığında "Sistem, mevcut kamera sayısı için uygun görünüyor." mesajı görüntülenir.

Keşif veya akış işlemi beklenmedik bir şekilde davrandığında bu aracı çalıştırın — diyalog penceresinden çıkmanıza gerek kalmadan çoğu NIC tarafındaki sorunu (yanlış MTU, yanlış arayüzde bulunan kamera, USB adaptörü sınırlamaları) tespit eder.

### Dizi Bağlama

İki veya daha fazla kamerayı **senkronize bir dizi**olarak bağlamak için dizi bağlama sihirbazını (**Kamera Dizisini Bağla**) kullanın: bu sihirbaz, ana/yardımcı seçimi (senkronizasyon kablosu denetimi ile önceden doldurulur — kablolu herhangi bir kamera ana olabilir, ve kablolu olduğunda RGB `-FRGB` kamera önceden seçilidir), ekran modu seçimi (Ayrı veya Birleştirilmiş döşemeler) ve onaylamadan önce elde edilebilir fps ve kablo bant genişliğinin canlı projeksiyonunu içeren bir dizi ayarları sahnesini adım adım yönlendirir. Sihirbaz ve dizi iş akışları, bu kılavuzun çoklu kamera dizileri bölümünde ele alınmaktadır; CLI'deki karşılığı ise [CLI Referansı](../reference/cli-reference.md) içindeki "LATTICE Kamera İlk Bağlantı İş Akışı"dır.

## CLI ve SDK'den Bağlanma

CLI ve SDK erişimi, ücretli bir Chloros+ kademesi ve oturum açılmış olmayı gerektirir; bu kural sunucu tarafında uygulanır (oturum açılmadığında `401 AUTH_REQUIRED`, ücretsiz kademede `403 PLAN_UPGRADE_REQUIRED`).

```bash
# List cameras on the network (vendor, model, serial, IP, MAC)
chloros-cli lattice info

# Single-camera smoke test: capture one frame (saves every applicable export type)
chloros-cli lattice capture -o output/

# Connect a synchronized array — same smart-prep flow as the GUI
chloros-cli lattice array-connect --serials 213800234,214000533
```

```python
import chloros_sdk

# Persistent live-camera session through the backend
with chloros_sdk.connect_camera("213800234") as cam:
    ...

# Array session (smart-prep: network probe, tier auto-pick, PTP, AE seeding, trigger config)
with chloros_sdk.connect_array(["213800234", "214000533"]) as array:
    ...
```

Tam imzalar, seçenekler ve yakalama iş akışları: [CLI Referansı](../reference/cli-reference.md) § `chloros-cli lattice`, [SDK Referansı](../reference/sdk-reference.md) § `connect_camera()` / `connect_array()`.

## Bağlantı kurulduğunda kalibrasyon nasıl gerçekleştirilir

Her LATTICE kamera, fabrika kalibrasyon paketini **kamera üzerinde** taşır ve Chloros, kamera bağlandığında MAPIR'ın bulutunu da kontrol eder:

| Durum   | Chloros'un kullandığı                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Çevrimiçi**|**O seri numarası için yayınlanan en yeni kalibrasyon** — buluttaki kopya, kamera üzerindeki kopyaya göre önceliklidir. Bu nedenle, MAPIR tarafından yeniden kalibre edilmiş veya güncellenmiş bir kamera kendini otomatik olarak günceller; kullanıcı tarafından herhangi bir işlem yapılması gerekmez. |
| **Çevrimdışı**|**Kameradaki paket**, olduğu gibi. Tamamen çevrimdışı iş akışları çalışmaya devam eder; kamera bir kez çevrimiçi olana (veya fabrika ayarlarına sıfırlanana) kadar yeni kalibrasyonları almazlar.                                                  |

Çekim anında, fiilen uygulanan katsayılar **her görüntünün XMP meta verilerine sabitlenir**. Daha sonraki bir kalibrasyon güncellemesi, daha önce çekmiş olduğunuz görüntüleri hiçbir zaman sessizce değiştirmez — eski bir çekimin yeniden işlenmesi sırasında, o günün en yeni katsayıları değil, XMP'sinde kayıtlı olan katsayılar kullanılır.

## Sorun Giderme

* **"Ağda kamera bulunamadı"**— [Ağ Kurulumu](https://mapir.gitbook.io/lattice-camera/setup/network-setup) bölümündeki bağlantı-yerel ayarları doğrulayın: ana bilgisayar ağ kartı statik `169.254.x.x/16`, kameralar aynı bağlantıda, DHCP/ağ geçidi beklenmez. Ardından, bağlantı iletişim kutusundaki**Sistemi Analiz Et**seçeneğini kullanarak her bir kameranın hangi ağ kartında görüldüğünü (veya görülmediğini) kontrol edin. Herhangi bir kablolama veya ağ kartı değişikliğinden sonra**Yeniden Tara** seçeneğini kullanın.
* **Daha önce çalışan bir sistem bağlanmıyor** (`FRAMES WILL DROP` / `Reduce ROI to enable` ile dizi paneli kapıları) — bir ağ kartı sürücüsü güncellemesi, alıcı halkası ayarlarını sessizce sıfırlamıştır. Ayarları yeniden uygulayın veya yönetici haklarıyla bir terminalden `chloros-cli lattice network --fix` komutunu çalıştırın; bkz. [Ağ Kurulumu](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Bir kamera "Dizide" mesajını gösteriyor** — bu kamera, bağlı bir dizi oturumuna aittir. Kamerayı bağımsız olarak kullanmak için dizi bağlantısını kesin.
