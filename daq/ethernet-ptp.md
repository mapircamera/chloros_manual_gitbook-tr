# DAQ-E Ağ ve Zaman Senkronizasyonu

> Sensörün fiziksel ağ kurulumu — kablolama, PoE, IP ataması ve cihazın kendi ağ ayarları — **[DAQ kullanım kılavuzunda](https://mapir.gitbook.io/daq/daq-e/network-setup)** yer almaktadır. Bu sayfa, Chloros tarafını ele almaktadır: bağlantı, zaman senkronizasyonu ve keşif işlemi sonuçsuz kaldığında ne yapılması gerektiği.

DAQ-E, DAQ ailesinin Ethernet üyesidir: PoE üzerinden güç alır, mDNS (`_daq-e._tcp` hizmeti) aracılığıyla keşfedilir ve sensör kimliğinden türetilen bir ana bilgisayar adıyla adreslenebilir — `daq-e-<6 hex>.local`, örneğin `daq-e-def330.local`. Bu sayfada, cihazın ağ üzerinden verileri nasıl aktardığı ve PTP zaman senkronizasyonuna nasıl katıldığı ele alınmaktadır.

## Aktarım modları

| Mod | Uç Nokta | Alıcılar | Notlar |
| --- | --- | --- | --- |
| **Çoklu Yayın** (varsayılan) | UDP `239.10.10.10:5002` | Aynı LAN üzerindeki herhangi bir cihaz aynı akışı alır | Her datagram CRC-16/CCITT ile doğrulanır |
| **Ham** | TCP bağlantı noktası `5000` | Tam olarak bir istemci (münhasır) | DAQ-U ile bayt düzeyinde uyumlu |

Chloros varsayılan olarak çoklu yayını kullanır; bu sayede GUI, CLI ve SDK hepsi aynı anda tek bir sensörü izleyebilir.

## Ağ gereksinimleri

* **Aynı yayın etki alanı.** Chloros&#x27;i çalıştıran makine, sensörle aynı L2 ağ segmentinde olmalıdır — mDNS keşfi yönlendiricileri geçmez.
* **Windows güvenlik duvarı uyarısı: kabul edin.** Chloros, çoklu yayın soketlerini ilk kez bağladığında, Windows Defender bir kez sorar. Buna izin vermek, DAQ-E verilerini (UDP 5002), mDNS&#x27;yi (UDP 5353) ve PTP&#x27;yi (UDP 319/320) kapsar. Linux&#x27;te bu işlem sessizce gerçekleşir.
* **PoE güç kaynağı, durum LED&#x27;i yoktur.** DAQ-E&#x27;nin kendine ait bir LED&#x27;i yoktur — gücü, anahtar veya enjektör bağlantı noktasındaki bağlantı/PoE göstergesinden kontrol edin ve cihazın açıldıktan sonra önyükleme yapıp ağa bağlanması için birkaç saniye bekleyin.

## Bağlantı

**GUI:** Işık Sensörleri sekmesi → Sensörü Bağla → Cihaz Türü &quot;DAQ-E (Ethernet)&quot;. Keşif işlemi yalnızca bağlantı iletişim kutusu ekranda açıkken çalışır (mDNS taraması artı Windows üzerinde ARP taraması) ve her 15 saniyede bir tekrarlanır; Yenile düğmesi basıldığında tarama hemen yeniden yapılır. Keşfedilen sensörler açılır menüde görünür; algılanan ilk sensör otomatik olarak seçilir.

<!-- SCREENSHOT-NEEDED: DAQ connect dialog with Device Type set to "DAQ-E (Ethernet)" and at least one discovered sensor listed in the Hostname/IP dropdown (e.g. daq-e-xxxxxx.local), Connect button enabled. -->

**CLI** (arka uç çalışıyor):

```bash
chloros-cli daq pool-connect --eth                              # auto-discover on the LAN
chloros-cli daq pool-connect --eth-host daq-e-def330.local      # explicit host — the reliable form
chloros-cli daq pool-connect --eth-host 192.168.1.57            # a plain IP works too
```

### Çoklu NIC&#x27;li ana bilgisayarlar ve önyüklemeden sonraki ilk bağlantı

Birden fazla aktif ağ arabirimi bulunan ana bilgisayarlarda, önyüklemeden sonraki **ilk** `pool-connect --eth`, sensör çalışır durumda olsa bile boş çıkabilir — ARP önbelleği henüz soğukken, keşif taraması sensörün bulunduğu arabirimi gözden kaçırabilir. Bundan güvenilir bir şekilde kurtulmanın yolu, keşif işlemini atlayıp adresi açıkça belirtmektir:

```bash
chloros-cli daq pool-connect --eth-host daq-e-def330.local
```

`--eth-host`, mDNS ana bilgisayar adını veya IP adresini kabul eder, her zaman doğru sensörü hedefler ve komut dosyaları ile başlıksız kurulumlar için önerilen biçimdir. GUI&#x27;de, bağlantı iletişim kutusundaki Yenile düğmesini kullanın ve yeniden tarama döngüsüne izin verin.

## Aygıt ayarları ve ürün yazılımı

Ağ ayarları (statik IP veya DHCP + bağlantı yerel adresleme, aygıt adı, önyükleme sırasında otomatik akış, OTA şifresi) sensörün kendisinde bulunur. Bu cihaz tarafındaki ayarlar, sevk edilen CLI sürümünde komut olarak sunulmaz; bunlar, görüntülendikleri Chloros GUI aracılığıyla veya MAPIR desteği ile yönetilir.

**Firmware güncellemeleri GUI&#x27;ye entegre edilmiştir.**Bağlı bir DAQ-E, Chloros derlemenizle birlikte gelen görüntüden daha eski bir donanım yazılımı çalıştırıyorsa, sensör satırında turuncu renkli**Güncelleme Mevcut** simgesi görünür ve dişli ayarları penceresinde &quot;Şu sürüme güncelle<version>

&quot; düğmesi</version> sunulur<version>

. Güncelleme, ağ üzerinden yaklaşık 30 saniye içinde yüklenir; sensör otomatik olarak yeniden başlatılır ve yeniden bağlanır; aktarımın kesilmesi durumunda mevcut firmware olduğu gibi kalır.

<!-- SCREENSHOT-NEEDED: DAQ-E per-sensor settings modal showing the DAQ-E-only rows: Hostname/IP, Firmware row with the "Update to <ver>" button (or "Up to date"), and the PTP Sync row with a live state value. -->

## PTP zaman senkronizasyonu

DAQ-E ürün yazılımı v1.2.0+, IEEE 1588 PTPv2&#x27;ye sıradan (yalnızca bağımlı) bir saat olarak katılır. **Chloros ana bilgisayarının arka ucu, PTP grandmaster&#x27;dır** — LAN üzerindeki her DAQ-E ve her LATTICE kamera, etki alanı 0&#x27;da buna bağlanır ve tüm cihaz zaman damgalarını ~1 ms tolerans aralığında tutar. Bu paylaşılan saat, DAQ okumalarının kamera pozlamalarıyla zaman damgası açısından eşleştirilebilmesini sağlar (bkz. [Kayıt ve .daq Biçimi](recording.md)).

CLI&#x27;ten senkronizasyonu inceleyin:

| Komut | Gösterir |
| --- | --- |
| `chloros-cli time-sync status` | Ana bilgisayar grandmaster durumu, BMCA öncelikleri, saat kimliği |
| `chloros-cli time-sync peers` | Algılanan tüm bağımlı cihazlar (DAQ-E sensörleri + LATTICE kameralar) |
| `chloros-cli time-sync cameras` | Kamera başına PTP durumu (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`) |
| `chloros-cli time-sync restart` | Grandmaster sürecini yeniden başlat |

GUI&#x27;de, DAQ-E ayarları modali, sensörün mevcut PTP durumunu gösteren canlı bir **PTP Senkronizasyonu** satırı gösterir.

Sıkı hizalama gerektiren tüketiciler için ayrıntılar:

* Aktarılan her datagram bir bayrak alanı içerir; **zaman damgası PTP ile senkronize olan çerçevelerde 2. bit ayarlanır**. Sıkı kamera/DAQ hizalaması gerektiren iş akışları bu bite göre kontrol edilmelidir.
* Senkronize bir yakalama işleminden önce, sensörün `chloros-cli time-sync peers`&#x27;te göründüğünü doğrulayın. (MAPIR’in dahili doğrudan donanım araçları, sensörün SLAVE durumuna ulaşması için en fazla 15 saniye bekleyen bir `--wait-ptp` bayrağıyla PTP kilidi üzerine kaydı da kontrol edebilir; bu araç, sevk edilen CLI&#x27;in bir parçası değildir.)
* PTP aktif olarak slave modundayken, sensör manuel saat sinyallerini reddeder (&quot;PTP saat sinyali sağlıyor&quot;). Bu tasarım gereğidir — PTP&#x27;ye güvenin.

## Linux notları

* **PTP, kurulum sırasında `libcap2-bin`&#x27;e ihtiyaç duyar.** `.deb` postinst komutu, `cap_net_bind_service=+ep`&#x27;e `/usr/lib/chloros/chloros-backend` üzerinde izin verir, böylece root olmaksızın PTP 319/320 numaralı bağlantı noktalarını bağlayabilir. `libcap2-bin` dosyası eksikse, bu adım atlanır ve PTP başlatılamaz. Düzeltme:

  ```bash
  sudo apt install libcap2-bin
  sudo apt reinstall chloros
  ```

* **Görüntü ekranı olmayan Jetson / Raspberry Pi:** İlk kurulumda systemd birimi `chloros-backend.service` oluşturulur ancak etkinleştirilmez. GUI olmadan sürekli çalışır durumda PTP (ve DAQ erişilebilirliği) için:

  ```bash
  sudo systemctl enable --now chloros-backend.service
  ```

  Bu birim olmadan PTP, yalnızca Chloros GUI&#x27;si açıkken çalışır.

## Sorun Giderme: &quot;DAQ-E aygıtı bulunamadı&quot;

| Kontrol | Ayrıntı |
| --- | --- |
| Güç | Sensörde LED yanmıyor — anahtar/enjektör bağlantı noktasının PoE ve bağlantı göstergelerini kontrol edin; güç açıldıktan sonra birkaç saniye bekleyin |
| Yayın alanı | Ana bilgisayar ve sensör aynı L2 segmentinde; mDNS yönlendirme yapmıyor |
| Windows güvenlik duvarı | İlk çalıştırmada Defender uyarısını kabul edin (UDP 5002, 5353, 319/320) |
| Çoklu NIC&#x27;li ana bilgisayar | Önyüklemeden sonraki ilk keşif sırasında sensör algılanmayabilir — `--eth-host <ip-or-hostname>` ile bağlanın |
| GUI yeniden taraması | Keşif işlemi yalnızca bağlantı iletişim kutusu açıkken çalışır; Yenile düğmesini kullanın |</version>
