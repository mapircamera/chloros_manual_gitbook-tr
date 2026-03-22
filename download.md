---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# İndir

Çok spektrumlu görüntü işleme çalışmalarına başlamak için Chloros&#x27;in en son sürümünü indirin.

### Sistem Gereksinimleri

#### Windows

| Gereksinim          | Minimum                                              | Önerilen                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **İşletim Sistemi** | Windows 10 (64-bit)                                  | Windows 11 (64-bit)                                  |
| **İşlemci**        | Intel Core i5 veya eşdeğeri                          | Intel Core i7 veya üstü                              |
| **Bellek (RAM)**     | 8 GB                                                  | 16 GB veya daha fazla                                         |
| **Grafik Kartı**    | DirectX 11 uyumlu                                | 4 GB+ VRAM&#x27;e sahip NVIDIA GPU                            |
| **Depolama**          | 6 GB boş alan                                       | 10 GB+ boş alana sahip SSD                            |
| **Ekran**          | 1920x1080                                            | 2560x1440 veya daha yüksek                                  |
| **İnternet**         | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir |

#### Linux amd64 (x86\_64)

| Gereksinim       | Minimum                    | Önerilen               |
| ----------------- | -------------------------- | ------------------------- |
| **Dağıtım**  | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04+             |
| **İşlemci**     | x86\_64 (Intel/AMD)        | Intel Core i7 veya üstü   |
| **Bellek (RAM)**  | 8 GB                        | 16 GB veya daha fazla              |
| **Grafik Kartı** | Yok (CPU işleme)      | 4 GB+ VRAM&#x27;e sahip NVIDIA GPU |
| **Depolama**       | 2 GB boş alan             | 10 GB+ boş alana sahip SSD       |
| **Python**        | Python 3.7+ (SDK için)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Gereksinim      | Minimum                      | Önerilen                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform**     | JetPack 6 ile NVIDIA Jetson | Jetson Orin NX 16GB veya AGX Orin |
| **Bellek (RAM)** | 8GB (paylaşımlı GPU/CPU)         | 16GB+ paylaşımlı                    |
| **Depolama**      | 2GB boş alan               | 10GB+ boş alana sahip NVMe SSD        |
| **Python**       | Python 3.7+ (SDK için)        | Python 3.10+                    |

{% hint style="info" %}
**GPU Hızlandırma**: NVIDIA GPU&#x27;lara sahip Chloros+ kullanıcıları, işlemeyi önemli ölçüde hızlandırmak için CUDA hızlandırmasını kullanabilir. Bu özellik hem Windows (masaüstü GPU&#x27;lar) hem de Linux (masaüstü GPU&#x27;lar ve NVIDIA Jetson) üzerinde çalışır. Chloros+ kullanıcıları ayrıca maksimum hız için çok iş parçacıklı işleme özelliğinden yararlanabilir.
{% endhint %}

***

## Chloros&#x27;i İndirin

### En Son Kararlı Sürüm (23 Mart 2026): Sürüm 1.1.0

### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Windows (.exe) için Chloros&#x27;i indirin</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Linux amd64 (.deb) için Chloros&#x27;i indirin</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Linux arm64 / Jetson (.deb) için Chloros&#x27;i indirin####</a>

Windows Yükleyici (GUI + CLI + Arka Uç)

* **Dosya Türü**: .exe (Windows Yükleyici)**Kurulum Adımları:**

1. Yukarıdaki .exe dosyasını indirin
2. Kurulumu başlatmak için yükleyiciye çift tıklayın
3. Kurulum sihirbazının talimatlarını izleyin
4. Kurulum dizinini seçin (varsayılan: `C:\Program Files\[USER]\Chloros\`)
5. Kurulumu tamamlayın ve Chloros veya Chloros CLI&#x27;i başlatın
6. [MAPIR Cloud Chloros+ hesabınızla](https://cloud.mapir.camera/pricing) oturum açın (veya ücretsiz sürümle devam edin)

{% hint style="success" %}
Yükleyici, komut satırı erişimi için `chloros-cli`&#x27;i sistem PATH&#x27;inize otomatik olarak ekler.
{% endhint %}

#### Linux amd64 (.deb Paketi — CLI + Arka Uç)

* **Dosya Türü**: .deb (Debian/Ubuntu paketi)
* **Mimari**: x86\_64 (amd64)

```bash
sudo dpkg -i chloros-amd64.deb
chloros-cli --version  # Verify installation
```

#### Linux arm64 — NVIDIA Jetson (.deb Paketi — CLI + Arka Uç)

* **Dosya Türü**: .deb (JetPack 6)
* **Mimari**: aarch64 (arm64)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
chloros-cli --version  # Verify installation
```

Ayrıntılı kurulum talimatları için [Linux Kurulumu](linux/linux-installation.md) ve Jetson&#x27;a özgü kılavuz için [NVIDIA Jetson Kılavuzu](linux/nvidia-jetson-guide.md) bölümlerine bakın.

#### Python SDK (Tüm Platformlar)

```bash
pip install chloros-sdk
```

Belgeler için [API : Python SDK](api-python-sdk.md) bölümüne bakın.

{% hint style="info" %}
**Linux kullanıcıları**: `.deb` paketi, CLI ve arka ucu yükler. Python SDK, pip aracılığıyla ayrı olarak yüklenir. Linux için bir GUI yoktur — tüm etkileşim CLI veya SDK aracılığıyla gerçekleşir.
{% endhint %}

***

## Ek Kaynaklar

### Python SDK

Geliştiriciler ve otomasyon iş akışları için Chloros Python SDK&#x27;i yükleyin:

```bash
pip install chloros-sdk
```

**Belgeler**: [API: Python SDK](api-python-sdk.md)**Gereksinimler**: Chloros kurulmuş olmalıdır (Windows yükleyici veya Linux `.deb` paketi), Chloros+ lisans girişi gereklidir***

## İçindekiler

### Windows Yükleyici

* ✅ **Chloros GUI** - Tam özellikli grafik arayüz
* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Arka Uç** - İşleme motoru
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları

### Linux .deb Paketi

* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Arka Uç** - İşleme motoru
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları
* ❌ GUI yok — Linux, yalnızca başsız CLI/SDK&#x27;tir

### Python SDK (pip, tüm platformlar)

* ✅ **Chloros SDK** - Python API (Chloros+ lisansı gerektirir)***

## Chloros+&#x27;a Yükseltin

Chloros+ aboneliği ile gelişmiş özelliklerin kilidini açın:

* 🚀 **Çok İş Parçacıklı İşleme** - Görüntüleri paralel olarak işleyin
* ⚡ **GPU (CUDA) Hızlandırma** - NVIDIA GPU gücünden yararlanın
* 💻 **CLI Erişimi** - Komut satırı araçlarıyla otomatikleştirin
* 🐍 **Python SDK** - Programlı API erişimi
* 📱 **Birden Fazla Cihaz** - 2-10+ cihazda kullanın (plana bağlı)
* **🐻 Gelişmiş Doku Duyarlı Debayer Yöntemi** - neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş, yüksek kaliteli kenar duyarlı bir debayer.
* 🧮 **Özel Formüller** - Özel multispektral indeksler oluşturun

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Chloros+ Planlarını ve Fiyatlarını Görüntüle</a></p>***

## Kurulum Yardımı

### Sorun Giderme

**Kurulum şu hata mesajıyla başarısız oluyor:**

* Yönetici haklarına sahip olduğunuzdan emin olun
* Antivirüs yazılımını geçici olarak devre dışı bırakın
* Minimum sistem gereksinimlerini karşıladığınızı kontrol edin

**Uygulama başlamıyor (Windows):**

* Windows 10/11 (64-bit) sürümünün yüklü olduğunu doğrulayın
* Grafik sürücülerini güncelleyin
* Hata ayrıntılarını görmek için Windows Olay Görüntüleyicisi&#x27;ni kontrol edin
* Hata günlükleriyle destek ekibine başvurun

**CLI başlamıyor (Linux):**

* `.deb` paketinin doğru şekilde yüklendiğini doğrulayın: `dpkg -l | grep chloros`
* İzinleri kontrol edin: `sudo chmod +x /usr/bin/chloros-cli`
* Tanılama çalıştırın: `chloros-cli selftest`
* Eksik kütüphaneler olup olmadığını kontrol edin: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Lisans etkinleştirme sorunları:**

* İnternet bağlantısının aktif olduğundan emin olun
* [https://cloud.mapir.camera](https://cloud.mapir.camera) adresinden kimlik bilgilerini doğrulayın
* Güvenlik duvarının Chloros&#x27;i engellemediğinden emin olun
* Ayrıntılı talimatlar için [Chloros+ Giriş](chloros+-login.md) sayfasına bakın

### Destek Alma

Kurulum veya ayarlama konusunda yardıma mı ihtiyacınız var?

* 📧 **E-posta**: info@mapir.camera
* 🌐 **Web sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Belgeler**: [Başlangıç](./)
* ❓ **SSS**: [Sık Sorulan Sorular](faq.md)***

## Değişiklik Günlüğü

<details>

<summary>Sürüm 1.1.0 (En Son)</summary>

**Yayın Tarihi: Mart 2026**

**Yeni Özellikler*** **Linux Desteği** — Linux amd64 (x86\_64) ve arm64 (NVIDIA Jetson JetPack 6) için yerel CLI ve SDK. `.deb` paketleri aracılığıyla yükleyin.
* **NVIDIA Jetson Desteği** — Jetson Nano, Orin Nano, Orin NX ve AGX Orin uç cihazları için optimize edilmiş işleme.
* **Dinamik Hesaplama Uyumlaştırma** — Otomatik donanım algılama ve işleme stratejisi optimizasyonu. Chloros, Jetson Nano&#x27;dan çoklu GPU iş istasyonuna kadar donanımınıza uyum sağlar.
* **4 İş Parçacıklı İşleme Boru Hattı** — Dinamik GPU bellek tahsisi ile eşzamanlı Algılama, Kalibrasyon, İşleme ve Dışa Aktarma iş parçacıkları.
* **Yeni CLI Komutları** — `selftest` (sistem tanılama) ve `update` (Linux güncelleme yönetimi).
* **Yeni CLI İşlem Bayrakları** — `--debayer` (standart/doku duyarlı), `--indices` (indeksleri belirt), `--target` (daha hızlı algılama için önce hedef alt klasörü ara).
* **Yeni GUI Menü Öğeleri** — Dosya Ekle, Klasör Ekle ve İşlemeyi Başlat/Durdur seçeneklerine artık ana menü açılır menüsünden erişilebilir.**İyileştirmeler**

* Çapraz platform arka uç otomatik algılama (Windows ve Linux yolları)
* İş parçacığı başına ilerleme takibi ile geliştirilmiş SDK `get_status()`
* Yeni SDK istisnaları: `ChlorosConfigurationError`, `ChlorosAuthenticationError`
* NVIDIA Jetson için termal yönetim ve uyarlanabilir hız sınırlama
* Döşemeli GPU işlemeye OOM yedeklemesi ile otomatik bellek yönetimi

</details>

<details>

<summary>Sürüm 1.0.5</summary>

**Yayın Tarihi: 10 Şubat 2026**

**Yeni Özellikler*** **Texture Aware Debayer Yöntemi \[Yalnızca Chloros+] -** Texture Aware, neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş yüksek kaliteli kenar farkındalıklı bir debayer kullanır.
* **T4P Kalibrasyon Hedefleri için destek*** **Daha hızlı Chloros+ GPU işleme, daha iyi bellek yönetimi**

**Hata Düzeltmeleri*** Tamamen yeni ön uç (GUI), artık tüm Windows bilgisayarlarda çalışmalıdır.

</details>

<details>

<summary>Sürüm 1.0.4</summary>

**Yayın Tarihi: 5 Ocak 2026**

**Yeni Özellikler*** **Görüntü/Meta Veri Geçişi**: Seçilen görüntünün meta verilerini görüntü ızgarası yerine bir tabloda görüntülemek için Dosya Tarayıcısına geçiş düğmesi eklendi
* **Görüntü Izgarası Yakınlaştırma Kaydırıcısı**: Küçük resim boyutunu ayarlamak için yeni UI kaydırıcısı (ayrıca CTRL + fare tekerleği de desteklenir)
* **Görüntü Izgarası Dışa Aktarma Düğmeleri**: Küçük resimleri JPG&#x27;den işlenmiş dışa aktarmalara (Hedefler, Yansıma, Dizin, LUT) geçirmek için üst satırdaki düğmeler
* **Harita Sekmesi**: Görüntülerin GPS konum işaretlerini gösteren yeni etkileşimli 2D harita
  * Google Haritalar ve ESRI harita döşemelerini destekler (yakınlaştırma düzeyinin kullanılabilirliğine göre en iyi döşeme hizmetini otomatik olarak seçer)
  * Harita işaretleri üzerinde fareyi gezdirerek küçük resim önizlemesi

**Hata Düzeltmeleri*** İngilizce olmayan bilgisayarlara Chloros yükleme desteği iyileştirildi

</details>

<details>

<summary>Sürüm 1.0.3</summary>

**Yayın Tarihi: 20 Aralık 2025**

**Yeni Özellikler*** İlk Sürüm

**İyileştirmeler*** İlk Sürüm

**Hata Düzeltmeleri*** İlk Sürüm

**Bilinen Sorunlar*** İlk Sürüm

</details>***

## Lisans Sözleşmesi**Tescilli Yazılım** - Telif Hakkı (c) 2026 MAPIR Inc.

Yetkisiz kullanım, dağıtım veya değişiklik yasaktır.

**Ücretsiz Sürüm**: Özellik kısıtlamalarıyla kişisel ve ticari kullanım için mevcuttur**Chloros+**: Gelişmiş özellikler ve ticari dağıtımlar için abonelik tabanlı lisans
