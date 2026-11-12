# DAQ-E Ağ Bağlantısı ve Zaman Senkronizasyonu

> Sensörün fiziksel ağ kurulumu — kablolama, PoE, IP ataması ve cihazın kendi ağ ayarları — **[DAQ kullanım kılavuzunda](https://mapir.gitbook.io/daq/daq-e/network-setup)** yer almaktadır. Bu sayfa, Chloros tarafını ele almaktadır: bağlantı, zaman senkronizasyonu ve keşif işlemi sonuçsuz kaldığında ne yapılması gerektiği.

DAQ-E, DAQ ailesinin Ethernet üyesidir: PoE üzerinden güç alır, mDNS (`_daq-e._tcp` hizmeti) aracılığıyla keşfedilir ve sensör kimliğinden türetilen bir ana bilgisayar adıyla adreslenebilir — `daq-e-<6 hex>.local`, örneğin `daq-e-def330.local`. Bu sayfa, cihazın ağ üzerinden verileri nasıl aktardığını ve PTP zaman senkronizasyonuna nasıl katıldığını ele almaktadır.

## Aktarım modları

| Mod | Uç Nokta | Alıcılar | Notlar |
| --- | --- | --- | --- |
| **Çoklu Yayın** (varsayılan) | UDP `239.10.10.10:5002` | Aynı LAN üzerindeki herhangi bir cihaz aynı akışı alır | Her datagram CRC-16/CCITT ile doğrulanır |
| **Ham** | TCP bağlantı noktası `5000` | Tam olarak bir istemci (münhasır) | DAQ-U ile bayt düzeyinde uyumlu |

Chloros, varsayılan olarak çoklu yayını kullanır; bu sayede GUI, CLI ve SDK, tek bir sensörü aynı anda izleyebilir.

## Ağ gereksinimleri

* **Aynı yayın etki alanı.** Chloros'u çalıştıran makine, sensörle aynı L2 ağ segmentinde olmalıdır — mDNS keşfi yönlendiricilerden geçmez.
* **Güvenlik duvarı.** Windows'da yükleyici, her ağ profiline arka uç için **Chloros Backend (UDP-In)**güvenlik duvarı kuralını ekler; bu kural DAQ-E verilerini (UDP 5002), mDNS (UDP 5353) ve PTP (UDP 319/320) verilerini kapsar; ayrıca, bir DAQ-E cihazının ürün yazılımı güncellemesi sırasında bilgisayara geri bağlanması için yerel alt ağla sınırlı bir**Chloros Arka Ucu (TCP-In)** kuralı da eklenir. Linux'ta, kurulum sırasında bu güvenlik duvarı etkinse, `.deb`, `ufw` veya `firewalld`'teki UDP bağlantı noktalarını açar. Üçüncü taraf bir güvenlik duvarı veya daha sonra etkinleştirilen bir güvenlik duvarında da aynı bağlantı noktalarının izin verilmesi gerekir.
* **PoE güç kaynağı, durum LED'i yoktur.** DAQ-E'nin kendine ait bir LED'i yoktur — gücü, anahtar veya enjektör bağlantı noktasındaki bağlantı/PoE göstergesinden kontrol edin ve cihazın açıldıktan sonra önyükleme yapıp ağa katılabilmesi için birkaç saniye bekleyin.

## Bağlantı Kurma

**GUI:** Işık Sensörleri sekmesi → Sensörü Bağla → Cihaz Türü "DAQ-E (Ethernet)". Keşif işlemi yalnızca bağlantı iletişim kutusu ekranda açıkken (Windows'te mDNS taraması ve ARP taraması) çalışır ve her 15 saniyede bir tekrarlanır; Yenile düğmesi basıldığında tarama hemen yeniden yapılır. Tespit edilen sensörler açılır menüde görünür; algılanan ilk sensör otomatik olarak seçilir.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (arka uç çalışıyor):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

Havuzda zaten bulunan bir DAQ-E, hem ana bilgisayar adıyla hem de IP adresiyle tanınır: başka bir adres üzerinden yeniden bağlanıldığında, ünite ikinci kez açılmak yerine mevcut havuz girdisi döndürülür.

### Çoklu NIC'li ana bilgisayarlar ve önyüklemeden sonraki ilk bağlantı

Birden fazla aktif ağ arabirimi bulunan ana bilgisayarlarda, önyüklemeden sonraki **ilk** `pool-connect --eth`, sensör çalışır durumda olsa bile boş çıkabilir — ARP önbelleği henüz ısınmamışken, keşif taraması sensörün bulunduğu arabirimi gözden kaçırabilir. Bundan güvenilir bir şekilde kurtulmanın yolu, keşif işlemini atlayıp adresi açıkça belirtmektir:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host`, mDNS ana bilgisayar adını veya IP adresini kabul eder, her zaman doğru sensörü hedefler ve komut dosyaları ile başlıksız kurulumlar için önerilen biçimdir. Grafik kullanıcı arayüzünde (GUI), bağlantı iletişim kutusundaki Yenile düğmesini kullanın ve yeniden tarama döngüsüne izin verin.

## Cihaz ayarları ve ürün yazılımı

Sensörün kendisi ağ ayarını tutar: DHCP (fabrika ayarı; DHCP sunucusu yanıt vermediğinde 169.254.x.x bağlantı yerel adresine geri döner) veya statik bir adres. Ayrıca, açıldığında akışa başlayıp başlamayacağını da belirler. Bu ayarları GUI'de (sensörün dişli simgesindeki ayarlar, **Ağ**:**Değiştir…**,**Açıldığında akış başlat**,**Sensörü yeniden başlat**; bkz. [DAQ Sekmesi](gui.md)) veya CLI ve SDK üzerinden `pool-set-network`, `pool-set-auto-stream` ve `pool-reboot` (`DAQSensorSession.set_network()`, `set_auto_stream()`, `reboot()`; bkz. [CLI Referansı](../reference/cli-reference.md#daq-e-device-settings)). Sensör, ağ değişikliğini uygulamak için yeniden başlatılır ve Chloros sensörü yeniden bağlar. Chloros'un çalıştığı bilgisayarın ağında statik bir adres bulunmalıdır: Chloros, yeniden başlatma sonrasında erişemediği bir adresi reddeder. Sensörün kimliği ve `daq-e-<id>.local` ana bilgisayar adı asla değişmez. 1.3.0 sürümündeki yenilikler.

**Firmware güncellemeleri GUI'ye entegre edilmiştir.** Bağlı bir DAQ-E, Chloros derlemenizle birlikte gelen görüntüden daha eski bir firmware çalıştırıyorsa, sensör satırında turuncu renkli bir **Güncelleme Mevcut** simgesi görünür ve dişli ayarları penceresinde "Şu sürüme güncelle<version>" düğmesi</version>

sunulur<version>. Güncelleme, ağ üzerinden yaklaşık 30 saniye içinde yüklenir; sensör otomatik olarak yeniden başlatılır ve yeniden bağlanır; aktarımın kesilmesi durumunda mevcut donanım yazılımı değişmeden kalır. CLI veya SDK üzerinden ve GUI'si olmayan Linux'de `chloros-cli daq pool-update-firmware --sensor-id <id>` (`DAQSensorSession.update_firmware()`) komutunu çalıştırın.

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP zaman senkronizasyonu

DAQ-E ürün yazılımı v1.2.0+, IEEE 1588 PTPv2'ye sıradan (sadece bağımlı) bir saat olarak katılır. **Chloros ana bilgisayarının arka ucu, PTP grandmaster'dır** — LAN üzerindeki her DAQ-E ve her LATTICE kamera, etki alanı 0'da buna bağlanır ve tüm cihaz zaman damgalarını ~1 ms tolerans aralığında tutar. Bu paylaşılan saat, DAQ okumalarının kamera pozlamalarıyla zaman damgası açısından eşleştirilebilmesini sağlar (bkz. [Kayıt ve .daq Biçimi](recording.md)).

CLI'den senkronizasyonu kontrol edin:

| Komut | Gösterir |
| --- | --- |
| `chloros-cli time-sync status` | Ana bilgisayar grandmaster durumu, BMCA öncelikleri, saat kimliği ve `health`: `OK` veya `SENDS_FAILING` / `DEGRADED` / `NO_SOCKETS` / `PEERS_SILENT` ile birlikte, paketlerinin ağa neden ulaşmadığını açıklayan bir `health_detail` |
| `chloros-cli time-sync peers` | Görülen tüm bağımlı cihazlar (DAQ-E sensörleri + LATTICE kameralar) |
| `chloros-cli time-sync cameras` | Kamera başına PTP durumu (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Grandmaster işlemini yeniden başlat |

GUI'de, DAQ-E ayarları modali, sensörün mevcut PTP durumunu gösteren canlı bir **PTP Senkronizasyonu** satırı gösterir.**PTP'si kapalı olan bir DAQ-E, kapalı kalır.** Ünite, PTP açık/kapalı ayarını kendi belleğinde saklar; bu nedenle, PTP'si kapatılmış bir ünite, yeniden bağlandıktan veya güç döngüsünden sonra da kapalı kalır ve zaman damgaları PTP ile senkronize edilmez. `chloros-cli daq pool-list`, böyle bir üniteyi işaretler (`!! PTP is switched OFF on this unit …`) ve sorunu gideren komutu yazdırır:

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

Chloros, üniteyi asla kendi kendine yeniden açmaz; çünkü kapalı durum kasıtlı olabilir.

Sıkı hizalama gerektiren kullanıcılar için ayrıntılar:

* Aktarılan her datagram bir bayrak alanı taşır; **zaman damgası PTP ile senkronize olan çerçevelerde 2. bit ayarlanır**. Sıkı kamera/DAQ hizalaması gerektiren iş akışları bu bite göre kontrol edilmelidir.
* Senkronize bir yakalama işleminden önce, sensörün `chloros-cli time-sync peers`'te göründüğünü ve `chloros-cli daq pool-list`'in PTP'sini kapalı olarak işaretlemediğini doğrulayın. (MAPIR’ın dahili doğrudan donanım araçları, sensörün SLAVE durumuna ulaşması için 15 saniyeye kadar bekleyen bir `--wait-ptp` bayrağıyla PTP kilidi üzerine kaydı kontrol edebilir; bu araçlar, dağıtılan CLI’nin bir parçası değildir.)
* PTP aktif olarak slave modundayken, sensör manuel saat gönderimlerini reddeder ("PTP saat sağlıyor"). Bu tasarım gereğidir — PTP'ye güvenin.

## Linux notları

* **Root erişimi gerektirmeyen PTP bağlantı noktaları.** PTP, UDP 319/320 bağlantı noktalarını kullanır. `.deb`, `/etc/sysctl.d/60-chloros-ptp.conf` (`net.ipv4.ip_unprivileged_port_start = 319`) değerini yazar; bu nedenle arka uç, bunları sizin kullanıcı kimliğinizle çalışacak şekilde bağlar. 1.3.0 sürümünden itibaren paket, arka uçta herhangi bir dosya yeteneği (`setcap`) ayarlamaz ve `libcap2-bin`'e ihtiyaç duymaz; yeteneği kendiniz eklemeyin, çünkü aksi takdirde paketi açılmış arka uç çalışmaz. PTP, 319 numaralı bağlantı noktasına bağlanamıyorsa, [Linux Kurulumu](../linux/linux-installation.md#ptp-will-not-start--cannot-bind-port-319) bölümüne bakın.

* **Headless Jetson / Linux ana bilgisayarı:** Paket, arka uç hizmetlerini yükler ancak hiçbirini etkinleştirmez. Kimsenin bir komut çalıştırmadığı durumlarda PTP'nin (ve DAQ'nın) sürekli çalışır durumda olması için, hizmetinizi kendi kullanıcı adınızla etkinleştirin:

  ```bash
  sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
  ```

  Bu yapılmazsa, PTP yalnızca arka uç hizmetini başlatan bir işlem (`chloros-cli` komutu veya bir SDK komut dosyası) varken çalışır. Kök `chloros-backend.service` hesabı ve bu hesaptan nasıl çıkılacağı hakkında bilgi için [Linux Kurulumu](../linux/linux-installation.md#always-on-ptp-for-headless-hosts) bölümüne bakın.

## Sorun Giderme: "DAQ-E cihazı bulunamadı"

| Kontrol | Ayrıntı |
| --- | --- |
| Güç | Sensörde LED yanmıyor — anahtar/enjektör bağlantı noktasının PoE ve bağlantı göstergelerini kontrol edin; güç açıldıktan sonra birkaç saniye bekleyin |
| Yayın alanı | Ana bilgisayar ve sensör aynı L2 segmentinde; mDNS yönlendirme yapmıyor |
| Güvenlik duvarı | Windows: Yükleyicinin **Chloros Backend (UDP-In)** kuralı, DAQ-E akışının (UDP 5002), mDNS (5353) ve PTP (319/320) bağlantılarının içeri girmesine izin verir; üçüncü taraf bir güvenlik duvarında da aynısı gereklidir. Linux: `.deb`, bu bağlantı noktalarını yalnızca kurulum sırasında güvenlik duvarı etkinse `ufw` / `firewalld`'te açar — daha sonra etkinleştirilen bir güvenlik duvarında bunları kendiniz izin verin |
| Çoklu ağ kartı (Multi-NIC) ana bilgisayar | Keşif işlemi, ARP yedeği ile mDNS'yi tarar; `--eth-host <ip-or-hostname>` keşif işlemini atlar ve doğrudan bağlanır |
| GUI yeniden taraması | Keşif işlemi yalnızca bağlantı iletişim kutusu açıkken çalışır; Yenile seçeneğini kullanın |
