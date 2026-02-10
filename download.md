---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/download
---

# İndir

Multispektral görüntü işlemeyi başlatmak için Chloros&#x27;in en son sürümünü indirin.

### Sistem Gereksinimleri

| Gereksinim          | Minimum                                              | Önerilen                                          |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------- |
| **İşletim Sistemi** | Windows 10 (64 bit)                                  | Windows 11 (64 bit)                                  |
| **İşlemci**        | Intel Core i5 veya eşdeğeri                          | Intel Core i7 veya daha iyisi                              |
| **Bellek (RAM)**     | 8 GB                                                  | 16 GB veya daha fazla                                         |
| **Grafik Kartı**    | DirectX 11 uyumlu                                | 4 GB+ VRAM ile NVIDIA GPU                            |
| **Depolama**          | 6 GB boş alan                                       | 10 GB+ boş alan ile SSD                            |
| **Ekran**          | 1920x1080                                            | 2560x1440 veya daha yüksek                                  |
| **İnternet**         | \[isteğe bağlı] Chloros+ lisans aktivasyonu için gereklidir | \[isteğe bağlı] Chloros+ lisans aktivasyonu için gereklidir |

{% hint style="info" %}
**GPU Hızlandırma**: NVIDIA GPU&#x27;lara sahip Chloros+ kullanıcıları, önemli ölçüde daha hızlı işlem için CUDA hızlandırmayı kullanabilir. Chloros+ kullanıcıları ayrıca maksimum hız için çok iş parçacıklı işlemden yararlanabilir.
{% endhint %}

***

## Chloros&#x27;i indirin

### <a href="https://drive.google.com/file/d/1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4/view?usp=drive_link" class="button primary">Chloros&#x27;i buradan indirin</a>

### En Son Kararlı Sürüm

**Chloros Windows için Yükleyici*** **Sürüm**: 1.0.5
* **Yayın Tarihi**: 10 Şubat 2026
* **Dosya Boyutu (İndirme)**: 1,6 GB
* **Dosya Boyutu (Yüklendiğinde)**: 5,7 GB
* **Dosya Türü**: .exe (Windows Yükleyici)

#### **Yükleme Adımları:**

1. `CHLOROS INSTALLER - CURRENT VERSION.exe` dosyasını indirin
2. Yükleyiciyi çift tıklayarak yüklemeyi başlatın
3. Yükleme sihirbazının talimatlarını izleyin
4. Yükleme dizinini seçin (varsayılan: `C:\Program Files\[USER]\Chloros\`)
5. Yüklemeyi tamamlayın ve Chloros veya Chloros CLI&#x27;i başlatın
6. [MAPIR Cloud Chloros+ hesabınızla](https://cloud.mapir.camera/pricing) oturum açın (veya ücretsiz sürümle devam edin)

{% hint style="success" %}
Yükleyici, komut satırı erişimi için `chloros-cli`&#x27;i sistem PATH&#x27;inize otomatik olarak ekler.
{% endhint %}

***

## Ek Kaynaklar

### Python SDK

Geliştiriciler ve otomasyon iş akışları için Chloros Python SDK&#x27;i yükleyin:

```bash
pip install chloros-sdk
```

**Belgeler**: [API: Python SDK](api-python-sdk.md)**Gereksinimler**: Chloros Masaüstü yüklü olmalıdır, Chloros+ lisans girişi gereklidir.***

## İçindekiler

Chloros kurulumu şunları içerir:

* ✅ **Chloros** - Tam özellikli grafik arayüz (GUI)
* ✅ **Chloros CLI** - Komut satırı arayüzü (Chloros+ lisansı gerektirir)
* ✅ **Chloros SDK** - Python API (Chloros+ lisansı gerektirir)
* ✅ **Kamera Profilleri** - Önceden yapılandırılmış MAPIR kamera şablonları***

## Chloros+&#x27;a yükseltin

Chloros+ aboneliği ile gelişmiş özelliklerin kilidini açın:

* 🚀 **Çok İş Parçacıklı İşleme** - Görüntüleri paralel olarak işleyin
* ⚡ **GPU (CUDA) Hızlandırma** - NVIDIA GPU gücünden yararlanın
* 💻 **CLI Erişimi** - Komut satırı araçlarıyla otomatikleştirin
* 🐍 **Python SDK** - Programlı API erişimi
* 📱 **Birden Çok Cihaz** - 2-10+ cihazda kullanın (plana bağlı)
* **🐻 Gelişmiş Doku Duyarlı Debayer Yöntemi** - neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş, yüksek kaliteli kenar duyarlı debayer. 
* 🧮 **Özel Formüller** - Özel multispektral indeksler oluşturun

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary">Chloros+ Planları ve Fiyatları Görüntüleyin</a></p>***

## Kurulum Yardımı

### Sorun Giderme

**Kurulum şu hata mesajıyla başarısız oluyor:**

* Yönetici haklarına sahip olduğunuzdan emin olun
* Antivirüs yazılımını geçici olarak devre dışı bırakın
* Minimum sistem gereksinimlerini karşıladığınızı kontrol edin

**Uygulama başlamıyor:**

* Windows 10/11 (64 bit) yüklü olduğunu doğrulayın
* Grafik sürücülerini güncelleyin
* Hata ayrıntılarını görmek için Windows Olay Görüntüleyicisini kontrol edin
* Hata günlüklerini destek ekibine iletin

**Lisans etkinleştirme sorunları:**

* İnternet bağlantısının aktif olduğundan emin olun
* [https://cloud.mapir.camera](https://cloud.mapir.camera) adresinde kimlik bilgilerinizi doğrulayın
* Güvenlik duvarının Chloros&#x27;i engellemediğini kontrol edin
* Ayrıntılı talimatlar için [Chloros+ Oturum Açma](chloros+-login.md) adresine bakın

### Destek Alma

Yükleme veya kurulum konusunda yardıma mı ihtiyacınız var?

* 📧 **E-posta**: info@mapir.camera
* 🌐 **Web sitesi**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* 📚 **Belgeler**: [Başlangıç Kılavuzu](./)
* ❓ **SSS**: [Sık Sorulan Sorular](faq.md)***

## Değişiklik Günlüğü

<details>

<summary>Sürüm 1.0.5</summary>

#### **Yayın Tarihi**: 10 Şubat 2026**Yeni Özellikler*** **Doku Duyarlı Debayer Yöntemi \[Chloros+ Sadece] -** Doku Duyarlı, neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birlikte yüksek kaliteli kenar duyarlı debayer kullanır.
* **T4P Kalibrasyon Hedefleri için Destek*** **Daha hızlı Chloros+ GPU işleme, daha iyi bellek yönetimi**

**Hata Düzeltmeleri*** Tamamen yeni ön uç (GUI), artık tüm Windows bilgisayarlarda çalışmalıdır.

</details>

<details>

<summary>Sürüm 1.0.4</summary>

#### **Yayın Tarihi**: 5 Ocak 2026**Yeni Özellikler*** **Görüntü/Meta Veri Geçişi**: Seçilen görüntünün meta verilerini görüntü ızgarası yerine bir tabloda görüntülemek için Dosya Tarayıcıya geçiş eklendi
* **Görüntü Izgarası Yakınlaştırma Kaydırıcısı**: Küçük resim boyutunu ayarlamak için yeni UI kaydırıcısı (CTRL + fare tekerleği de desteklenir)
* **Görüntü Izgarası Dışa Aktarma Düğmeleri**: Küçük resimleri JPG&#x27;den işlenmiş dışa aktarmalara (Hedefler, Yansıma, Dizin, LUT) geçirmek için üst satırdaki düğmeler
* **Harita Sekmesi**: Görüntünün GPS konum işaretlerini gösteren yeni etkileşimli 2D harita.
  * Google Haritalar ve ESRI harita döşemelerini destekler (yakınlaştırma düzeyine göre en uygun döşeme hizmetini otomatik olarak seçer).
  * Harita işaretleri üzerinde fareyle küçük resim önizlemesi.

**Hata Düzeltmeleri*** İngilizce olmayan bilgisayarlarda Chloros yükleme desteği iyileştirildi.

</details>

<details>

<summary>Sürüm 1.0.3</summary>

#### **Yayın Tarihi**: 20 Aralık 2025**Yeni Özellikler*** İlk Başlatma

**İyileştirmeler*** İlk Başlatma

**Hata Düzeltmeleri*** İlk Başlatma

**Bilinen Sorunlar*** İlk Başlatma

</details>***

## Lisans Sözleşmesi**Tescilli Yazılım** - Telif Hakkı (c) 2026 MAPIR Inc.

Yetkisiz kullanım, dağıtım veya değişiklik yasaktır.

**Ücretsiz Sürüm**: Özellik sınırlamalarıyla kişisel ve ticari kullanım için mevcuttur.**Chloros+**: Gelişmiş özellikler ve ticari dağıtımlar için abonelik tabanlı lisans.
