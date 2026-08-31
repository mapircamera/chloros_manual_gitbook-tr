# Kameraların Bağlanması

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Herhangi bir şey bağlanmadan önce Kameralar sekmesi</p></figcaption></figure>Chloros, bağlantı üzerindeki LATTICE kameralarını otomatik olarak algılar — GUI&#x27;deki Kameralar sekmesinden, `chloros-cli lattice`&#x27;ten veya Python SDK&#x27;ten. Kameranın model dizesi, sonraki tüm işlemleri yönlendirir: Chloros, kameranın `DeviceUserID` + `DeviceSerialNumber` değerlerinden sensör profilini, bant düzenini ve fabrika kalibrasyonunu belirler; bu nedenle **her kamera için ayrı bir yapılandırma yapmaya gerek yoktur**.

Bağlanmadan önce, ana bilgisayar ağının ayarlandığından emin olun — bağlantı-yerel adresleme, jumbo çerçeveler ve diziler için NIC alım arabelleği ayarları. Bu, donanım tarafındaki kurulumdur ve LATTICE kılavuzunda yer almaktadır: [**Ağ Kurulumu**](https://mapir.gitbook.io/lattice-camera/setup/network-setup).

## GUI&#x27;den Bağlanma

Chloros kenar çubuğundaki **Kameralar**sekmesini açın (donanım sekmeleri, arka uçun başlatılması tamamlandığında görünür) veya ana menü →**Kameraya Bağlan**seçeneğini kullanın. Her iki seçenek de**Kameraya(lara) Bağlan** iletişim kutusunu açar.

### Kamera(lar)a Bağlan iletişim kutusu

İletişim kutusu açılır açılmaz ağı tarar (&quot;Ağı tarıyor...&quot;) ve bulduğu tüm kameraları listeler. Her satırda kameranın **modeli**(örn. `LATT-M3M-L41-F550`),**seri numarası**ve**IP adresi** gösterilir.

* **Bir satırı seçmek için üzerine tıklayın**(yeşil renkle vurgulanır).**Birden fazla kamera** seçebilir ve bunları tek seferde bağlayabilirsiniz — Chloros, kameraları sırayla bağlar.
* **&quot;Bağlı&quot;** etiketine sahip satırlar zaten bağlıdır ve yeniden seçilemez.
* **&quot;Dizide&quot;** etiketine sahip satırlar, şu anda bağlı bir kamera dizisine aittir. O kamerayı tek başına kullanmak için önce dizinin bağlantısını kesin.
* **Bağlan** — seçilen kameraları bağlar; birden fazla kamera seçildiğinde düğmede bir sayı gösterilir, örneğin &quot;Bağlan (3)&quot;.
* **Yeniden Tara** — keşif işlemini tekrar başlatır.
* **Kapat** — iletişim kutusunu kapatır.
* Tarama sonuçsuz tamamlanırsa, iletişim kutusunda **&quot;Ağda kamera bulunamadı&quot;** mesajı görüntülenir — aşağıdaki [Sorun Giderme](connecting.md#troubleshooting) bölümüne bakın.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Kamera(lar)ı Bağla iletişim kutusu — burada ağda kamera bulunmadığı durum gösterilmektedir</p></figcaption></figure>### İlk bağlantı: kalibrasyon paketi indirme

Belirli bir kamera bir cihaza **ilk kez**bağlandığında, Chloros, GigE üzerinden kameranın kendisinden kameranın fabrika kalibrasyon paketini (\~3,8 MB) alır. Bu işlem devam ederken, iletişim kutusunda seri numarası başına bir ilerleme çubuğu içeren yeşil renkli**&quot;Kameradan kalibrasyon verileri indiriliyor&quot;**paneli görüntülenir — kamera başına yaklaşık**70 saniye** sürer. Paket ana bilgisayarda önbelleğe alınır, bu nedenle aynı kameranın sonraki bağlantılarında indirme işlemi tamamen atlanır (ve panel asla gösterilmez).

### Sistemi Analiz Et

Diyalog penceresindeki **Sistemi Analiz Et** düğmesi, ana bilgisayarı ve ağı inceler (işlem sürerken etiket &quot;Analiz ediliyor...&quot; olarak görünür) ve bir tanılama raporu oluşturur:

* **Ana Bilgisayar** — CPU çekirdekleri ve RAM; GPU adı ve bellek, veya &quot;GPU: Algılanmadı&quot;.
* **Ağ Arabirimleri** — her bir ağ kartının adı, bağlantı hızı, MTU (etkin olduğunda &quot;jumbo&quot; etiketiyle), yukarı/aşağı durumu ve bir USB veriyolunda yer alıp almadığı.
* **Kameralar**— seri numarası, model, IP ve**her kameranın hangi ağ kartı üzerinde bulunduğu**.
* **Performans** — piksel formatı için kamera başına mevcut ve ideal fps değerleri; ideal değer mevcut değeri aştığında yeşil renkli &quot;Potansiyel: N kat iyileştirme mümkün&quot; satırı görüntülenir.
* **Uyarılar ve numaralı öneriler** — düzeltilmesi gereken bir durum olmadığında &quot;Sistem, mevcut kamera sayısı için uygun görünüyor.&quot; mesajı görüntülenir.

Keşif veya akış işlemi beklenmedik bir şekilde davrandığında bu aracı çalıştırın — diyalog penceresinden çıkmanıza gerek kalmadan, NIC tarafındaki sorunların çoğunu (yanlış MTU, kameranın yanlış arayüzde olması, USB adaptörü sınırlamaları) tespit eder.

### Bir dizi bağlama

İki veya daha fazla kamerayı **senkronize bir dizi**olarak bağlamak için dizi bağlama sihirbazını (**Kamera Dizisini Bağla**) kullanın: Bu sihirbaz, onay vermeden önce ana/yardımcı seçimi (GPIO kablolama probu tarafından önceden doldurulur), görüntüleme modu seçimi (Ayrı veya Birleştirilmiş döşemeler) ve elde edilebilir fps ile kablo bant genişliğinin canlı bir projeksiyonunu içeren bir dizi ayarları ekranını adım adım yönlendirir. Sihirbaz ve dizi iş akışları, bu kılavuzun çoklu kamera dizileri bölümünde ele alınmaktadır; CLI için karşılığı, [CLI Referansı](../reference/cli-reference.md) içindeki &quot;LATTICE Kamera İlk Bağlantı İş Akışı&quot;dır.

## CLI ve SDK&#x27;ten Bağlanma

CLI ve SDK&#x27;e erişim için ücretli bir Chloros+ kademesi ve oturum açılmış olma şartı gereklidir; bu kural sunucu tarafında uygulanır (giriş yapılmadığında `401 AUTH_REQUIRED`, ücretsiz kademede ise `403 PLAN_UPGRADE_REQUIRED`).

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

Her LATTICE kamera, fabrika kalibrasyon paketini **kamera üzerinde** barındırır ve Chloros, kamera bağlandığında MAPIR&#x27;in bulutunu da kontrol eder:

| Durum   | Chloros&#x27;in kullandığı                                                                                                                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Çevrimiçi**|**O seri numarası için yayınlanan en yeni kalibrasyon** — buluttaki kopya, kameradaki kopyaya göre önceliklidir. Dolayısıyla, MAPIR tarafından yeniden kalibre edilmiş veya güncellenmiş bir kamera, kullanıcı müdahalesine gerek kalmaksızın otomatik olarak kendini günceller. |
| **Çevrimdışı**|**Kameradaki paket**, olduğu gibi. Tamamen çevrimdışı iş akışları çalışmaya devam eder; kamera bir kez çevrimiçi olana (veya fabrika ayarlarına sıfırlanana) kadar yeni kalibrasyonları almazlar.                                                  |

Çekim anında, fiilen uygulanan katsayılar **her görüntünün XMP meta verilerine sabitlenir**. Daha sonraki bir kalibrasyon güncellemesi, daha önce çekmiş olduğunuz görüntüleri asla sessizce değiştirmez — eski bir çekimin yeniden işlenmesinde, o günün en yeni değerleri değil, XMP&#x27;sine kaydedilmiş katsayılar kullanılır.

## Sorun Giderme

* **&quot;Ağda kamera bulunamadı&quot;**— [Ağ Kurulumu](https://mapir.gitbook.io/lattice-camera/setup/network-setup) bölümündeki bağlantı-yerel ayarları doğrulayın: ana bilgisayar ağ kartı statik `169.254.x.x/16`, kameralar aynı bağlantıda, DHCP/ağ geçidi beklenmez. Ardından, bağlantı iletişim kutusundaki**Sistemi Analiz Et**seçeneğini kullanarak her bir kameranın hangi ağ kartında görüldüğünü (veya görülmediğini) kontrol edin. Herhangi bir kablolama veya ağ kartı değişikliğinden sonra**Yeniden Tarama** yapın.
* **Daha önce çalışan bir sistem bağlanmıyor** (`FRAMES WILL DROP` / `Reduce ROI to enable` ile dizi paneli kapıları) — bir ağ kartı sürücüsü güncellemesi, alıcı halkası ayarlarını sessizce sıfırlamıştır. Bu ayarları yeniden uygulayın veya yönetici haklarıyla bir terminalden `chloros-cli lattice network --fix` komutunu çalıştırın; bkz. [Ağ Kurulumu](https://mapir.gitbook.io/lattice-camera/setup/network-setup).
* **Bir kamera &quot;Dizide&quot; mesajını gösteriyor** — bu kamera, bağlı bir dizi oturumuna aittir. Kamerayı bağımsız olarak kullanmak için dizi bağlantısını kesin.
