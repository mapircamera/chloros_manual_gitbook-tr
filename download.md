---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# İndirme

Çok spektral görüntü işleme çalışmalarına başlamak için Chloros’un en son sürümünü indirin.

### Sistem Gereksinimleri

#### Windows


| Gereksinim          | Minimum                                              | Önerilen                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **İşletim Sistemi** | Windows 10 (64 bit)                                  | Windows 11 (64 bit)                                  |
| **İşlemci**        | Intel Core i5 veya eşdeğeri                          | Intel Core i7 veya üstü                              |
| **Bellek (RAM)**     | 8 GB                                                  | 16 GB veya daha fazla                                         |
| **Grafik Kartı**    | DirectX 11 uyumlu                                | 4 GB+ VRAM'e sahip NVIDIA GPU                            |
| **Depolama**          | 6 GB boş alan                                       | 10 GB+ boş alana sahip SSD                            |
| **Ekran**          | 1920x1080                                            | 2560x1440 veya daha yüksek                                  |
| **İnternet**         | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir |

#### Linux amd64 (x86\_64)

| Gereksinim       | Minimum                    | Önerilen               |
| ----------------- | -------------------------- | ------------------------- |
| **Dağıtım**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **İşlemci**     | x86\_64 (Intel/AMD)        | Intel Core i7 veya daha üstü   |
| **Bellek (RAM)**  | 8 GB                        | 16 GB veya daha fazla              |
| **Grafik Kartı** | Yok (CPU ile işleme)      | 4 GB+ VRAM'e sahip NVIDIA GPU |
| **Depolama**       | 2 GB boş alan             | 10 GB+ boş alana sahip SSD       |
| **Python**        | Python 3.7+ (SDK için)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Gereksinim      | Minimum                      | Önerilen                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform**     | JetPack 6 yüklü NVIDIA Jetson | Jetson Orin NX 16 GB veya AGX Orin |
| **Bellek (RAM)** | 8 GB (paylaşımlı GPU/CPU)         | 16 GB+ paylaşımlı                    |
| **Depolama**      | 2 GB boş alan               | 10 GB+ boş alana sahip NVMe SSD        |
| **Python**       | Python 3.7+ (SDK için)        | Python 3.10+                    |

{% hint style="info" %}
**GPU Hızlandırma**: NVIDIA GPU'lara sahip Chloros+ kullanıcıları, işleme hızını önemli ölçüde artırmak için CUDA hızlandırmasını kullanabilir. Bu özellik, Windows ve Linux amd64 üzerinde NVIDIA masaüstü GPU'larıyla ve JetPack 6 yüklü NVIDIA Jetson'da çalışır. Chloros+ kullanıcıları ayrıca maksimum hız için çok iş parçacıklı işleme özelliğinden de yararlanır.
{% endhint %}

***

## Chloros'u İndirin

### En Son Kararlı Sürüm: Sürüm 1.3.0 (11 Kasım 2026)

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Windows</a>
<a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">için Chloros'u İndirin (.exe)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Linux amd64 için Chloros'u İndirin (.deb)</a>


### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Linux arm64 / Jetson için Chloros'u indirin (.deb)</a>


#### Windows Yükleyicisi (GUI + CLI + Arka Uç)

* **Dosya Türü**: .exe (Windows Yükleyicisi)

**Kurulum Adımları:**

1. Yukarıdaki .exe dosyasını indirin
2. Kurulumu başlatmak için yükleyiciye çift tıklayın
3. Kurulum sihirbazının talimatlarını izleyin
4. Kurulum dizinini seçin (varsayılan: `C:\Program Files\Chloros\`)
5. **Ek Görevleri Seç**sayfasında, her iki**Sistem ayarları** seçeneğinin de işaretli kalmasını sağlayın (önerilir — aşağıya bakın)
6. Kurulumu tamamlayın ve Chloros veya Chloros CLI'yi başlatın 7. [MAPIR Cloud Chloros+ hesabınızla](https://cloud.mapir.camera/pricing) oturum açın (veya ücretsiz sürümüyle devam edin)

**Sistem ayarları seçenekleri** (her ikisi de varsayılan olarak işaretlidir):

| Yükleyicide belirtilen seçenek | Ne işe yarar | Sessiz kurulumdan vazgeçme |
| --- | --- | --- |
| **Windows uzun yollarını etkinleştir** (proje klasörlerinin 260 karakterden daha derin konumlarda bulunmasına izin verir; önerilir) | Windows'un sistem genelindeki `LongPathsEnabled` ayarını 1 olarak ayarlar. Windows bu ayar kapalı olarak gelir ve bu durumda hiçbir dosya yolu 260 karakterden uzun olamaz; Chloros ürünleri proje klasörünün yaklaşık 130 karakter altına kadar yer alır, bu nedenle uzun yollar devre dışı bırakıldığında daha derin bir proje klasörü kabul edilmez. Yüklemeden sonra başlatılan programlar için geçerli olur. Diğer programlar bu ayara bağlı olabileceğinden, program kaldırıldıktan sonra da ayar etkin kalır. | `/MERGETASKS="!longpaths"` |
| **Chloros arka ucunu Microsoft Defender taramasından hariç tut** (çok daha hızlı kamera yakalama; önerilir) | Kurulum klasöründeki `chloros-backend.exe` için bir Microsoft Defender işlem hariç tutma kuralı ekler; böylece gerçek zamanlı koruma, arka ucun yazdığı her dosyayı, nereye yazarsa yazsın taramaz. Yüksek yakalama hızlarında, tarama işlemi yakalama hızının yaklaşık %30'u kadar zaman alır (9 kameralı sürekli yakalamada saniyede 77,7'ye karşı 52,7 yakalama). Başka hiçbir program hariç tutulmaz ve bu hariç tutma, programın kaldırılmasıyla birlikte kaldırılır. Defender'ın bulunmadığı veya bir ilke tarafından kilitlendiği durumlarda, bu adım sessizce başarısız olur ve kurulum devam eder. | `/MERGETASKS="!defenderexclusion"` |

Gizli bir kurulum (`/SILENT` veya `/VERYSILENT`), gösterilen `/MERGETASKS` değeriyle devre dışı bırakmadığınız sürece her iki seçeneği de uygular.

{% hint style="success" %}
Yükleyici, CLI klasörünü (`C:\Program Files\Chloros\cli\`) sisteminize bir kez ekler `PATH` — yeniden yükleme veya yükseltme işlemlerinde artık yinelenen bir giriş eklenmez ve önceki yükleyicilerin eklediği yinelenen girişler kaldırılır. `chloros-cli`'i kullanmak için kurulumdan sonra yeni bir terminal açın. Yükleyici ayrıca, her ağ profilinde arka uç için iki adet Windows Güvenlik Duvarı kuralı ekler: **Chloros Arka Ucu (UDP-In)**, kamera akışları, PTP, mDNS ve DAQ-E veri akışı için;**Chloros Arka Ucu (TCP-In)** ise yerel alt ağla sınırlı olup, sensörün görüntüyü toplamak için bu bilgisayara yeniden bağlandığı DAQ-E ürün yazılımı güncellemesi içindir. Her ikisi de program kaldırıldığında silinir.
{% endhint %}

#### Linux amd64 (.deb Paketi — CLI + Arka Uç)

* **Dosya Türü**: .deb (Debian/Ubuntu paketi)
* **Mimari**: x86\_64 (amd64)

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

#### Linux arm64 — NVIDIA Jetson (.deb Paketi — CLI + Arka Uç)

* **Dosya Türü**: .deb (JetPack 6)
* **Mimari**: aarch64 (arm64)

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version     # Verify installation: "Chloros CLI 1.3.0"
```

Gerçekten indirdiğiniz dosya adını kullanın ve `./` kısmını koruyun; böylece `apt`, adıyla bir paket aramak yerine o dosyayı yükler. `apt install` ile yükleyin, `dpkg -i` ile değil: apt, paketin zorunlu kıldığı değil, önerdiği Python SDK gereksinimlerini de yükler; `dpkg -i` ise bunları atlar. 1.3.0 sürümünden itibaren arka uç ve CLI, `/usr/lib/chloros/` altında sıkıştırılmamış olarak kurulur; bu sayede önce kendilerini açmaya gerek kalmadan çalışmaya başlarlar.

Jetson paketi, JetPack 6'nın CUDA sürümündeki PyTorch'u kullanır; bu nedenle, dahil edilen Texture Aware debayer de dahil olmak üzere işleme, Jetson'un GPU'sunda çalışır. JetPack 6'nın yüklediği CUDA ve cuDNN kütüphanelerini kullanır (bu kütüphanelerin bulunmadığı bir görüntüde `sudo apt install nvidia-jetpack`).

Ayrıntılı kurulum talimatları için [Linux Kurulumu](linux/linux-installation.md) bölümüne, Jetson'a özgü kılavuz için ise [NVIDIA Jetson Kılavuzu](linux/nvidia-jetson-guide.md) bölümüne bakın.

#### Python SDK (Tüm Platformlar)

Her yükleyici, uygun bir `chloros_sdk` wheel'i içerir; bu sayede SDK sürümü her zaman yüklü olan GUI/CLI/arka uç ile uyumludur. Windows'da yükleyici, bunu sistem Python'inize otomatik olarak yükler. Linux'ta `.deb`, sistem `python3` için SDK'yi (`/usr/lib/python3/dist-packages` içine) yükler, böylece `import chloros_sdk`, kurulumdan hemen sonra pip ve ağ bağlantısı olmadan çalışır. Yalnızca sistem paketlerini görmeyen bir virtualenv için, pakete dahil olan wheel dosyasının içine yüklenmesi gerekir:

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Yalnızca pip kullanan ana bilgisayarlarda (Chloros paketi yüklü değil; bu durumda SDK başka bir makinedeki arka ucu çalıştırır), SDK PyPI'da da mevcuttur:

```bash
pip install chloros-sdk
```

Belgeler için [API: Python SDK](api-python-sdk.md) ve [SDK Referansı](reference/sdk-reference.md) sayfalarına bakın.

{% hint style="info" %}
**Linux kullanıcıları**: `.deb` paketi, CLI'yi, arka ucu ve Python SDK'yi yükler. Linux için bir GUI yoktur — tüm etkileşim CLI veya SDK aracılığıyla gerçekleşir.
{% endhint %}

***

## Ek Kaynaklar

### Python SDK


Geliştiriciler ve otomasyon iş akışları için Chloros Python SDK her yükleyiciyle birlikte gelir: Windows yükleyicisi bunu Python'unuza yükler; Linux `.deb` ise sistem `python3` için yükler. Chloros paketi bulunmayan bir makinede (başka bir makinede bir arka ucu çalıştıran bir makine), PyPI'dan yükleyin:

```bash
pip install chloros-sdk
```

**Belgeler**: [API: Python SDK](api-python-sdk.md)

**Gereksinimler**: çalıştırılacak bir Chloros arka ucu (Windows yükleyicisi veya Linux `.deb` paketi bunu yükler) ve ücretli bir kademede Chloros+ oturum açma bilgisi

***

## Neler Dahildir

### Windows Yükleyicisi

* ✅ **Chloros GUI** - Tam özellikli grafik arayüz
* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Arka Ucu** - İşleme motoru
* ✅ **Python SDK** - Python yüklü ise buna kurulur (Chloros+ lisansı gerektirir)
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları

### Linux.deb Paketi

* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Backend** - İşleme motoru
* ✅ **Python SDK** - `python3` sistemi için yüklenir (Chloros+ lisansı gerektirir)
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları
* ❌ GUI yok — Linux, sadece başsız CLI/SDK modundadır

### Python SDK (PyPI, Chloros paketi olmayan makineler için)

* ✅ **Chloros SDK** - Python API'si (Chloros+ lisansı gerektirir)

***

## Chloros+'ya Yükseltme

Chloros+ aboneliği ile gelişmiş özelliklerin kilidini açın:

* 🚀 **Çok İş Parçacıklı İşleme** - Görüntüleri paralel olarak işleyin
* ⚡ **GPU (CUDA) Hızlandırma** - NVIDIA GPU gücünden yararlanın
* 💻 **CLI Erişimi** - Komut satırı araçlarıyla otomatikleştirin
* 🐍 **Python SDK** - Programlı API erişimi
* 📱 **Birden Fazla Cihaz** - 2-10+ cihazda kullanın (plana bağlı)
* **🐻 Gelişmiş Doku Duyarlı Debayer Yöntemi** - Neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli, kenar duyarlı bir debayer.
* 🧮 **Özel Formüller** - Özel multispektral indeksler oluşturun

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Chloros+ Planlarını ve Fiyatlarını Görüntüleyin</a></p>***

## Kurulum Yardımı

### Sorun Giderme

**Kurulum şu hata mesajıyla başarısız oluyor:**

* Yönetici haklarına sahip olduğunuzdan emin olun
* Antivirüs yazılımını geçici olarak devre dışı bırakın
* Minimum sistem gereksinimlerini karşıladığınızı kontrol edin

**Uygulama başlamıyor (Windows):**

* Windows 10/11 (64-bit) sürümünün yüklü olduğunu doğrulayın
* Grafik sürücülerini güncelleyin
* Hata ayrıntıları için Windows Olay Görüntüleyicisi'ni kontrol edin
* Hata günlükleriyle birlikte destek ekibine başvurun

**CLI başlatılamıyor (Linux):**

* `.deb` paketinin doğru şekilde yüklendiğini doğrulayın: `dpkg -l | grep chloros`
* İzinleri kontrol edin: `sudo chmod +x /usr/bin/chloros-cli`
* Tanılama çalıştırın: `chloros-cli selftest`
* Eksik kütüphaneler olup olmadığını kontrol edin: `ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"` (1.3.0 sürümünden itibaren, `/usr/lib/chloros/chloros-backend` küçük bir başlatıcı betiğidir; programın kendisi `/usr/lib/chloros/backend/` dosyasındadır)

**Lisans etkinleştirme sorunları:**

* İnternet bağlantısının aktif olduğundan emin olun
* [https://cloud.mapir.camera](https://cloud.mapir.camera) adresinden kimlik bilgilerinizi doğrulayın
* Güvenlik duvarının Chloros'u engellemediğinden emin olun* Ayrıntılı talimatlar için [Chloros+ Giriş](chloros+-login.md) sayfasına bakın

### Destek Alma

Kurulum veya ayarlama konusunda yardıma mı ihtiyacınız var?

* 📧 **E-posta**: info@mapir.camera
* 🌐 **Web sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Kılavuz**: [Başlangıç Kılavuzu](./)
* ❓ **SSS**: [Sık Sorulan Sorular](faq.md)***

## Yazılım Güncellemeleri

Chloros, güncellemeleri kontrol eder, yeni bir sürüm çıktığında bunu bildirir ve bu indirme sayfasına bağlantı verir — yeni imzalı yükleyiciyi çalıştırarak güncelleme yapabilirsiniz. Ayarlarınız, projeleriniz ve oturum açma bilgileriniz güncellemelerden sonra da korunur. Linux ve Jetson'da, `chloros-cli update` daha yeni bir sürüm olup olmadığını kontrol eder ve uygun olan `.deb`'i indirip yüklemeyi önerir (bu komut yalnızca Linux'ta kullanılabilir).

***

## Değişiklik Günlüğü**Sürüm 1.3.0 (En Son, 11 Kasım 2026)**— tam liste için [Başlangıç](./) sayfasındaki**Chloros

1.3.0'daki Yenilikler** bölümüne bakın.**Sürüm 1.2.0**— tam liste için [Başlangıç](./) sayfasındaki**Chloros 1.2.0'daki Yenilikler** bölümüne bakın.

<details>

<summary>Sürüm 1.0.5</summary>

**Yayın Tarihi: 10 Şubat 2026**

**Yeni Özellikler*** **Dokuya Duyarlı Debayer Yöntemi \[Yalnızca Chloros+] -** Dokuya Duyarlı, neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli, kenar farkındalıklı bir debayer kullanır.
* **T4P Kalibrasyon Hedefleri desteği*** **Daha hızlı Chloros+ GPU işleme, daha iyi bellek yönetimi**

**Hata Düzeltmeleri*** Tamamen yeni bir ön yüz (GUI); artık tüm Windows bilgisayarlarda çalışmalıdır.

</details>

<details>

<summary>Sürüm 1.0.4</summary>

**Yayın Tarihi: 5 Ocak 2026**

**Yeni Özellikler*** **Görüntü/Meta Veri Geçişi**: Seçilen görüntünün meta verilerini görüntü ızgarası yerine bir tabloda görüntülemek için Dosya Tarayıcısına bir geçiş seçeneği eklendi
* **Görüntü Izgarası Yakınlaştırma Kaydırıcısı**: Küçük resim boyutunu ayarlamak için yeni bir kullanıcı arayüzü kaydırıcısı (ayrıca CTRL + fare tekerleği de desteklenir)
* **Görüntü Izgarası Dışa Aktarma Düğmeleri**: Küçük resimleri JPG formatından işlenmiş dışa aktarma formatlarına (Hedefler, Yansıma, İndeks, LUT) geçirmek için üst satırda bulunan düğmeler
* **Harita Sekmesi**: Görüntülerin GPS konum işaretlerini gösteren yeni etkileşimli 2D harita
  * Google Haritalar ve ESRI harita döşemelerini destekler (yakınlaştırma düzeyine göre en uygun döşeme hizmetini otomatik olarak seçer)
  * Harita işaretçilerinin üzerine fareyi getirdiğinizde küçük resim önizlemesi görüntülenir

**Hata Düzeltmeleri*** İngilizce dışı dillerde çalışan bilgisayarlarda Chloros'un kurulumuna yönelik destek iyileştirildi

</details>

<details>

<summary>Sürüm 1.0.3</summary>

**Yayın Tarihi: 20 Aralık 2025**

**Yeni Özellikler*** İlk Sürüm

**İyileştirmeler*** İlk Sürüm

**Hata Düzeltmeleri*** İlk Sürüm

**Bilinen Sorunlar*** İlk Sürüm

</details>

***

## Lisans Sözleşmesi**Tescilli Yazılım** - Telif Hakkı (c) 2026 MAPIR Inc.

İzinsiz kullanım, dağıtım veya değişiklik yasaktır.

**Ücretsiz Sürüm**: Özellik kısıtlamalarıyla kişisel ve ticari kullanım için mevcuttur**Chloros+**: Gelişmiş özellikler ve ticari kullanımlar için abonelik tabanlı lisans
