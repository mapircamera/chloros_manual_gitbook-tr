# Linux Genel Bakış

Chloros 1.3.0, Linux iş istasyonlarında, sunucularda ve NVIDIA Jetson uç cihazlarında **CLI**ve**Python SDK** için yerel Linux desteği sunar — başsız multispektral görüntü işleme ile canlı LATTICE kamera ve DAQ ışık sensörü kontrolü.

{% hint style="info" %}
**Linux&#x27;ta masaüstü GUI yoktur.**Chloros Masaüstü GUI&#x27;si yalnızca Windows&#x27;ta kullanılabilir. Linux kullanıcıları, [CLI](../CLI.md) ve [Python SDK](../api-python-sdk.md) aracılığıyla Chloros ile etkileşim kurar. `.deb`, uygulama menünüze bir**Chloros CLI** girişi ekler — bu giriş, yalnızca `chloros-cli`&#x27;i çalıştıran bir terminal emülatörünü açar.
{% endhint %}

***

## Platform Destek Tablosu

| Özellik | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Masaüstü GUI** | Evet | Yok | Hayır | Hayır |
| **CLI** (`chloros-cli`) | Evet | Evet | Evet | Evet |
| **Python SDK** (`chloros-sdk`) | Evet | Evet | Evet | Evet |
| **Görüntü işleme ardışık düzeni** | Evet | Evet | Evet | Evet |
| **LATTICE kamera kontrolü (canlı)** | Evet (Kameralar sekmesi) | Evet (`chloros-cli lattice`, SDK) | Evet | Evet |
| **DAQ ışık sensörleri (canlı)** | Evet (Işık Sensörleri sekmesi) | Evet (`chloros-cli daq pool-*`, SDK) | Evet | Evet |
| **PTP zaman senkronizasyonu (ana bilgisayar grandmaster&#x27;dır)** | Evet | Evet (`chloros-cli time-sync`) | Evet | Evet |
| **GPU hızlandırma (CUDA)** | Evet | Evet | Evet | Evet (JetPack 6) |
| **Doku Duyarlı debayer** (CUDA GPU gerektirir) | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) |
| **Dinamik Hesaplama Uyumlaştırma** | Evet | Evet | Evet | Evet |
| **Sistem hizmeti olarak arka uç** (`chloros-backend@<user>.service`) | Hayır | Hayır | Evet (isteğe bağlı) | Evet (isteğe bağlı) |
| **Yerinde güncelleyici** (`chloros-cli update`) | Hayır (yükleyiciyi çalıştırın) | Hayır (yükleyiciyi çalıştırın) | Evet | Evet |***

## Desteklenen Mimariler

| Mimari | Açıklama | Paket |
| --- | --- | --- |
| **amd64 (x86_64)** | Standart masaüstü/sunucu işlemcileri (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM işlemcileri — NVIDIA Jetson Orin ailesi (JetPack 6) | `chloros_<version>_arm64_jp6.deb` |

## Desteklenen Linux Dağıtımları

* **Ubuntu 22.04 LTS veya daha yeni** (amd64)
* **Debian 12 veya daha yeni** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson Orin platformları)***

## Linux Kullanıcılarına Sunulanlar

* **Chloros CLI** — toplu işleme, otomasyon ve komut dosyası oluşturma için eksiksiz komut satırı arayüzü
* **Chloros Python SDK** — araştırma iş akışları ve özel araçlar için programlama amaçlı Python arayüzü; sistem `python3` için `.deb` tarafından yüklenir (virtualenvs için sürüm uyumlu bir wheel dosyası `/usr/lib/chloros/sdk/`&#x27;te bulunur; PyPI, Chloros paketi bulunmayan makineler içindir)
* **LATTICE kamera kontrolü** — `chloros-cli lattice` ve SDK aracılığıyla LATTICE kameralarını ve senkronize çoklu kamera dizilerini keşfedin, bağlayın, yapılandırın ve görüntü yakalayın; `.deb`, kameraların gerektirdiği Arena SDK çalışma zamanını içerir
* **DAQ ışık sensörü kontrolü** — `chloros-cli daq pool-*` ve SDK aracılığıyla DAQ-U/M/E sensörlerini bağlayın, kalibre edilmiş spektrumları aktarın ve `.daq` dosyalarını kaydedin
* **PTP zaman senkronizasyonu** — Chloros arka ucu, LATTICE kameralarının ve DAQ-E sensörlerinin bağlı olduğu PTP grandmaster&#x27;ı çalıştırır; `chloros-cli time-sync` ile kontrol edin ve `chloros-backend@<user>.service` systemd birimi ile başsız olarak çalışır durumda tutun (bkz. [Linux Kurulumu](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Proje otomasyonu** — kaydedilmiş projeleri `chloros-cli project` ve SDK&#x27;nin `open_project`&#x27;i ile başsız olarak çalıştırın
* **GPU hızlandırma** — NVIDIA GPU&#x27;larda CUDA ile hızlandırılmış işleme (amd64&#x27;te masaüstü GPU&#x27;lar ve JetPack 6 ile Jetson&#x27;un GPU&#x27;su)
* **Dinamik Hesaplama Uyumlaştırma** — otomatik donanım algılama ve işleme stratejisi seçimi; uzmanlar için bir kaçış yolu olarak `CHLOROS_STRATEGY` geçersiz kılma seçeneği
* **Tüm işleme özellikleri** — Windows ile aynı iş akışı: kalibrasyon, vinyet düzeltme, bitki örtüsü indeksleri ve tüm dışa aktarma formatları
* **Chloros+ özellikleri** — çok iş parçacıklı (pipeline) işleme, Texture Aware debayer (CUDA GPU&#x27;da) ve özel indeksler; ücretli Chloros+ planı ile

## Linux Kullanıcılarının Elde Edemedikleri Özellikler

* **Masaüstü GUI** — grafik arayüz yoktur; tüm etkileşim CLI veya Python SDK aracılığıyla gerçekleşir
* **Görüntü Görüntüleyici** — etkileşimli görüntü görüntüleyici, ızgara görünümü veya harita işaretçileri yoktur
* **Görsel proje yönetimi** — projeler, CLI komutları ve SDK çağrıları aracılığıyla oluşturulur ve yürütülür (donanımın kendisi — kameralar, sensörler, yakalama — terminal üzerinden tamamen kontrol edilebilir)***

## Lisans Gereksinimi

CLI ve SDK erişimi için **ücretli Chloros+ kademesi — Copper veya üstü**(Copper, Bronze, Silver, Gold) gereklidir. Ücretsiz**Iron** kademesinde CLI/SDK erişimi yoktur. Bu sınırlama sadece CLI tarafından değil, arka uç tarafından da uygulanır:

| Durum | Arka uç yanıtı |
| --- | --- |
| Oturum açılmamış | `401` ile `error_code: AUTH_REQUIRED` |
| Ücretsiz Iron kademesinde oturum açılmış | `403` ile `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` tüm katmanlarda çalışır — bu, geçitten muaf olan tek yoldur — bu nedenle reddedilme nedeni her zaman görülebilir.

***

## Linux&#x27;ta Başlangıç Linux&#x27;ta

1. **Chloros&#x27;u kurun** — `.deb` kurulumu için [Linux Kurulumu](linux-installation.md) sayfasına bakın
2. **Doğrulama** — `chloros-cli --version`, `Chloros CLI 1.3.0` çıktısını verir; `chloros-cli selftest`, 7 adımlı tanılama işlemini çalıştırır
3. **Python SDK&#x27;yi kontrol edin** — `.deb` bunu yükler: `python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"` (virtualenv içinde, `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`)
4. **Oturum açın** — `chloros-cli login your@email.com 'your-password'` (her makine için bir kez; paket güncellemeleri oturum bilgilerini korur, paketin kaldırılması ise bu bilgileri siler)
5. **İlk veri kümenizi işleyin** — `chloros-cli process ~/datasets/flight001`

NVIDIA Jetson için, platforma özgü kurulum, termal davranış ve sahada dağıtım hakkında bilgi almak üzere özel [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) bölümüne bakın.

***

## Sonraki Adımlar

* [Linux Kurulumu](linux-installation.md) — amd64 ve arm64 için ayrıntılı kurulum, dosya konumları ve sorun giderme
* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson&#x27;a özgü kurulum, bellek ve termal davranış, sahada dağıtım
* [CLI: Komut Satırı](../CLI.md) — CLI kılavuzu
* [API: Python SDK](../api-python-sdk.md) — SDK kılavuzu
* [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) — 1.3.0 sürümü için kapsamlı komut/API listeleri
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros&#x27;un donanımınıza nasıl uyum sağladığı

{% hint style="info" %}
**Bu kılavuzu programlı olarak okuma.** Her sayfa, kendi URL&#x27;sine `.md` eklenmiş şekilde ham Markdown biçimi olarak da sunulur (örneğin `https://mapir.gitbook.io/chloros/linux/linux-installation.md`) ve kılavuzun tamamının içindekiler sayfası [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt) adresinde yayınlanmaktadır.
{% endhint %}
