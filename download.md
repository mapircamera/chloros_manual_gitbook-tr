---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# İndirme

Çok spektral görüntü işlemeye başlamak için Chloros’in en son sürümünü indirin.

### Sistem Gereksinimleri

#### Windows

| Gereksinim          | Minimum                                              | Önerilen                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **İşletim Sistemi** | Windows 10 (64 bit)                                  | Windows 11 (64 bit)                                  |
| **İşlemci**        | Intel Core i5 veya eşdeğeri                          | Intel Core i7 veya üstü                              |
| **Bellek (RAM)**     | 8 GB                                                  | 16 GB veya daha fazla                                         |
| **Grafik Kartı**    | DirectX 11 uyumlu                                | 4 GB+ VRAM&#x27;e sahip NVIDIA GPU                            |
| **Depolama**          | 6 GB boş alan                                       | 10 GB veya daha fazla boş alana sahip SSD                            |
| **Ekran**          | 1920x1080                                            | 2560x1440 veya daha yüksek                                  |
| **İnternet**         | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir | \[isteğe bağlı] Chloros+ lisans etkinleştirme için gereklidir |

#### Linux amd64 (x86\_64)

| Gereksinim       | Minimum                    | Önerilen               |
| ----------------- | -------------------------- | ------------------------- |
| **Dağıtım**  | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS      |
| **İşlemci**     | x86\_64 (Intel/AMD)        | Intel Core i7 veya üstü   |
| **Bellek (RAM)**  | 8 GB                        | 16 GB veya daha fazla              |
| **Grafik Kartı** | Yok (CPU ile işleme)      | 4GB+ VRAM&#x27;e sahip NVIDIA GPU |
| **Depolama**       | 2GB boş alan             | 10GB+ boş alana sahip SSD       |
| **Python**        | Python 3.7+ (SDK için)      | Python 3.10+              |

#### Linux arm64 (NVIDIA Jetson)

| Gereksinim      | Minimum                      | Önerilen                     |
| ---------------- | ---------------------------- | ------------------------------- |
| **Platform**     | JetPack 6 yüklü NVIDIA Jetson | Jetson Orin NX 16 GB veya AGX Orin |
| **Bellek (RAM)** | 8 GB (GPU/CPU paylaşımlı)         | 16 GB+ paylaşımlı                    |
| **Depolama**      | 2GB boş alan               | 10GB+ boş alana sahip NVMe SSD        |
| **Python**       | Python 3.7+ (SDK için)        | Python 3.10+                    |

{% hint style="info" %}
**GPU Hızlandırma**: NVIDIA GPU&#x27;lara sahip Chloros+ kullanıcıları, işleme hızını önemli ölçüde artırmak için CUDA hızlandırmasını kullanabilir. Bu özellik hem Windows (masaüstü GPU&#x27;lar) hem de Linux (masaüstü GPU&#x27;lar ve NVIDIA Jetson) üzerinde çalışır. Chloros+ kullanıcıları ayrıca maksimum hız için çok iş parçacıklı işleme özelliğinden yararlanabilir.
{% endhint %}

***

## Chloros&#x27;i İndirin

### En Son Kararlı Sürüm: Sürüm 1.2.0

<!-- NOLAN: replace installer links + release date for 1.2.0 — the three download buttons below still point at the 1.1.0 Google Drive files, and the release date needs to be added to the heading above. -->



### <a href="https://drive.google.com/uc?export=download&#x26;id=1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4" class="button primary">Windows (.exe) için Chloros&#x27;i indirin</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1dB8-ke3wxNXpw_e1qJ4BhwBpCoNd4kLS" class="button primary">Linux amd64 (.deb) için Chloros&#x27;i indirin</a>



### <a href="https://drive.google.com/uc?export=download&#x26;id=1d1OwdcYA4Rf4jkuPi2IBeWT2772_HnyO" class="button primary">Linux arm64 / Jetson için Chloros&#x27;i indirin (.deb)</a>

#### Windows Yükleyici (GUI + CLI + Arka Uç)

* **Dosya Türü**: .exe (Windows Yükleyici)**Kurulum Adımları:**

1. Yukarıdaki .exe dosyasını indirin
2. Kurulumu başlatmak için yükleyiciye çift tıklayın
3. Kurulum sihirbazının talimatlarını izleyin
4. Kurulum dizinini seçin (varsayılan: `C:\Program Files\MAPIR\Chloros\`)
5. Kurulumu tamamlayın ve Chloros veya Chloros CLI programını başlatın
6. [MAPIR Cloud Chloros+ hesabınızla](https://cloud.mapir.camera/pricing) oturum açın (veya ücretsiz sürümle devam edin)

{% hint style="success" %}
Yükleyici, komut satırı erişimi için sistem PATH&#x27;inize otomatik olarak `chloros-cli`&#x27;i ekler.
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

Her yükleyici, uyumlu bir `chloros_sdk` tekerleğini içerir; bu nedenle SDK sürümü, her zaman yüklü olan GUI/CLI/arka uç ile uyumludur. Windows sürümünde yükleyici, bunu sisteminizdeki Python konumuna otomatik olarak yükler; Linux sürümünde ise `.deb`, tekerleği `/usr/lib/chloros/sdk/` konumuna yerleştirir ve şu kurulum komutunu görüntüler:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

Yalnızca pip kullanan ana bilgisayarlar için (Chloros paketi yüklü değil), SDK da PyPI&#x27;da bulunur:

```bash
pip install chloros-sdk
```

Bkz. [API : Python SDK](api-python-sdk.md) ve [SDK Referansı](reference/sdk-reference.md) belgelerine bakınız.

{% hint style="info" %}
**Linux kullanıcıları**: `.deb` paketi, CLI ve arka uç bileşenini yükler. Linux için bir GUI yoktur — tüm etkileşimler CLI veya SDK aracılığıyla gerçekleşir.
{% endhint %}

***

## Ek Kaynaklar

### Python SDK

Geliştiriciler ve otomasyon iş akışları için Chloros, Python ve SDK&#x27;i kurun:

```bash
pip install chloros-sdk
```

**Belgeler**: [API: Python SDK](api-python-sdk.md)**Gereksinimler**: Chloros&#x27;in kurulu olması gerekir (Windows yükleyicisi veya Linux `.deb` paketi), Chloros+ lisans girişi gereklidir***

## Pakete Dahil Olanlar

### Windows Kurulum Programı

* ✅ **Chloros GUI** - Tam özellikli grafik arayüz
* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Arka Uç** - İşleme motoru
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları

### Linux .deb Paketi

* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros Arka Uç** - İşleme motoru
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları
* ❌ GUI yok — Linux, yalnızca başlıksız CLI/SDK&#x27;tir

### Python SDK (pip, tüm platformlar)

* ✅ **Chloros SDK** - Python API (Chloros+ lisansı gerektirir)***

## Chloros+ sürümüne yükseltin

Chloros+ aboneliği ile gelişmiş özelliklerin kilidini açın:

* 🚀 **Çok İş Parçacıklı İşleme** - Görüntüleri paralel olarak işleyin
* ⚡ **GPU (CUDA) Hızlandırma** - NVIDIA GPU gücünden yararlanın
* 💻 **CLI Erişimi** - Komut satırı araçlarıyla otomatikleştirin
* 🐍 **Python SDK** - Programlı API erişimi
* 📱 **Birden Fazla Cihaz** - 2-10+ cihazda kullanın (plana bağlı olarak)
* **🐻 Gelişmiş Doku Duyarlı Debayer Yöntemi** - neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, kenarları algılayan yüksek kaliteli bir debayer.
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
* Hata ayrıntıları için Windows Olay Görüntüleyicisi&#x27;ni kontrol edin
* Hata günlükleriyle birlikte destek ekibine başvurun

**CLI başlatılamıyor (Linux):**

* `.deb` paketinin doğru şekilde yüklendiğini doğrulayın: `dpkg -l | grep chloros`
* İzinleri kontrol edin: `sudo chmod +x /usr/bin/chloros-cli`
* Tanılama çalıştırın: `chloros-cli selftest`
* Eksik kütüphaneler olup olmadığını kontrol edin: `ldd /usr/lib/chloros/chloros-backend | grep "not found"`

**Lisans etkinleştirme sorunları:**

* İnternet bağlantısının aktif olduğundan emin olun
* [https://cloud.mapir.camera](https://cloud.mapir.camera) adresinden kimlik bilgilerinizi doğrulayın
* Güvenlik duvarının Chloros&#x27;i engellemediğinden emin olun
* Ayrıntılı talimatlar için [Chloros+ Giriş](chloros+-login.md) sayfasına bakın

### Destek Alma

Kurulum veya ayarlama konusunda yardıma mı ihtiyacınız var?

* 📧 **E-posta**: info@mapir.camera
* 🌐 **Web sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Kılavuz**: [Başlangıç Kılavuzu](./)
* ❓ **SSS**: [Sık Sorulan Sorular](faq.md)***

## Yazılım Güncellemeleri

Chloros, güncellemeleri kontrol eder, yeni bir sürüm çıktığında bunu bildirir ve bu indirme sayfasına bağlantı verir — yeni imzalı yükleyiciyi çalıştırarak güncelleme yapabilirsiniz. Ayarlarınız ve projeleriniz güncellemelerden etkilenmez. Linux ve Jetson&#x27;da, `chloros-cli update` daha yeni bir sürüm olup olmadığını kontrol eder ve uygun `.deb` sürümünü indirip yüklemeyi önerir dosyasını indirip yüklemeyi önerir (bu komut yalnızca Linux sürümünde mevcuttur).

***

## Değişiklik Günlüğü**Sürüm 1.2.0 (En Son)**— tüm özellik listesi için [Başlangıç](./) sayfasındaki**Chloros 1.2.0&#x27;daki Yenilikler** bölümüne bakın.

<details>

<summary>Sürüm 1.0.5</summary>

**Yayın Tarihi: 10 Şubat 2026**

**Yeni Özellikler*** **Dokuya Duyarlı Debayer Yöntemi \[Yalnızca Chloros+] -** Dokuya Duyarlı, neredeyse tüm debayer gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeliyle birleştirilmiş, yüksek kaliteli, kenar farkındalıklı bir debayer kullanır.
* **T4P Kalibrasyon Hedefleri Desteği*** **Daha hızlı Chloros+ GPU işleme, daha iyi bellek yönetimi**

**Hata Düzeltmeleri*** Tamamen yeni kullanıcı arayüzü (GUI), artık tüm Windows bilgisayarlarda çalışmalıdır.

</details>

<details>

<summary>Sürüm 1.0.4</summary>

**Yayın Tarihi: 5 Ocak 2026**

**Yeni Özellikler*** **Görüntü/Meta Veri Geçişi**: Seçilen görüntünün meta verilerini görüntü ızgarası yerine bir tabloda görüntülemek için Dosya Tarayıcısına geçiş düğmesi eklendi
* **Görüntü Izgarası Yakınlaştırma Kaydırıcısı**: Küçük resim boyutunu ayarlamak için yeni bir kullanıcı arayüzü kaydırıcısı (ayrıca CTRL + fare tekerleği de desteklenir)
* **Görüntü Izgarası Dışa Aktarma Düğmeleri**: Küçük resimleri JPG formatından işlenmiş dışa aktarma formatlarına (Hedefler, Yansıtma, İndeks, LUT) geçirmek için üst satırda bulunan düğmeler
* **Harita Sekmesi**: Görüntülerin GPS konum işaretlerini gösteren yeni etkileşimli 2D harita
  * Google Haritalar ve ESRI harita döşemelerini destekler (yakınlaştırma düzeyine göre en uygun döşeme hizmetini otomatik olarak seçer)
  * Harita işaretçilerinin üzerine fareyi getirdiğinizde küçük resim önizlemesi görüntülenir

**Hata Düzeltmeleri*** İngilizce dışındaki dillerde çalışan bilgisayarlara Chloros kurulum desteği iyileştirildi

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
