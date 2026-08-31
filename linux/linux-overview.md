# Linux Genel Bakış

Chloros 1.2.0, **CLI**ve**Python SDK** — başsız multispektral görüntü işleme, ayrıca canlı LATTICE kamera ve DAQ ışık sensörü kontrolü — için Linux iş istasyonları, sunucular ve NVIDIA Jetson kenar cihazlarında yerel destek sağlar.

{% hint style="info" %}
**Linux&#x27;te masaüstü GUI yoktur.**Chloros Masaüstü GUI&#x27;si yalnızca Windows&#x27;te mevcuttur. Linux kullanıcıları, [CLI](../CLI.md) ve [Python SDK](../api-python-sdk.md) aracılığıyla etkileşim kururlar. `.deb`, uygulama menünüze**Chloros CLI** girişi eklemez — sadece `chloros-cli` çalıştıran bir terminal emülatörünü açar.
{% endhint %}

***

## Platform Destek Matrisi

| Özellik | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Masaüstü GUI** | Evet | Yok | Hayır | Hayır |
| **CLI** (`chloros-cli`) | Evet | Evet | Evet | Evet |
| **Python SDK** (`chloros-sdk`) | Evet | Evet | Evet | Evet |
| **Görüntü işleme boru hattı** | Evet | Evet | Evet | Evet |
| **LATTICE kamera kontrolü (canlı)** | Evet (Kameralar sekmesi) | Evet (`chloros-cli lattice`, SDK) | Evet | Evet |
| **DAQ ışık sensörleri (canlı)** | Evet (Işık Sensörleri sekmesi) | Evet (`chloros-cli daq pool-*`, SDK) | Evet | Evet |
| **PTP zaman senkronizasyonu (ana bilgisayar grandmaster)** | Evet | Evet (`chloros-cli time-sync`) | Evet | Evet |
| **GPU hızlandırma (CUDA)** | Evet | Evet | Evet | Evet (JetPack 6) |
| **Doku Duyarlı debayer** | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) |
| **Dinamik Hesaplama Uyumlaştırma** | Evet | Evet | Evet | Evet |
| **Sistem hizmeti olarak arka uç** (`chloros-backend.service`) | Hayır | Hayır | Evet (isteğe bağlı) | Evet (isteğe bağlı) |
| **Yerinde güncelleyici** (`chloros-cli update`) | Hayır (kurulum programını çalıştırın) | Hayır (kurulum programını çalıştırın) | Evet | Evet |***

## Desteklenen Mimariler

| Mimari | Açıklama | Paket |
| --- | --- | --- |
| **amd64 (x86_64)** | Standart masaüstü/sunucu işlemcileri (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | ARM işlemciler — NVIDIA Jetson Orin ailesi | `chloros_<version>_arm64_jp6.deb` (JetPack 6 derlemesi) |

## Desteklenen Linux Dağıtımları

* **Ubuntu 22.04 LTS veya daha yeni** (amd64)
* **Debian 12 veya daha yeni** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson Orin platformları)***

## Linux Kullanıcıları Nelerden Yararlanır?

* **Chloros CLI** — toplu işleme, otomasyon ve komut dosyası oluşturma için eksiksiz komut satırı arayüzü
* **Chloros Python SDK** — araştırma iş akışları ve özel araçlar için programlama tabanlı Python arayüzü (PyPI&#x27;den yüklenebilir ve ayrıca sürüm uyumlu bir wheel dosyası olarak `.deb` paketinin içinde bulunur)
* **LATTICE kamera kontrolü** — `chloros-cli lattice` ve SDK aracılığıyla LATTICE kameralarını ve senkronize çoklu kamera dizilerini keşfedin, bağlayın, yapılandırın ve görüntü yakalayın; `.deb`, kameraların gerektirdiği Arena SDK çalışma zamanını bir paket halinde sunar
* **DAQ ışık sensörü kontrolü** — `chloros-cli daq pool-*` ve SDK aracılığıyla DAQ-U/M/E sensörlerini bağlayın, kalibre edilmiş spektrumları aktarın ve `.daq` dosyalarını kaydedin
* **PTP zaman senkronizasyonu** — Chloros arka ucu, LATTICE kameraların ve DAQ-E sensörlerinin bağlı olduğu PTP grandmaster’ı çalıştırır; bunu `chloros-cli time-sync` ile inceleyin ve `chloros-backend.service` systemd birimi ile başsız olarak çalışır durumda tutun (bkz. [Linux Kurulumu](linux-installation.md#always-on-ptp-for-headless-hosts))
* **Proje otomasyonu** — kaydedilmiş projeleri `chloros-cli project` ve SDK&#x27;in `open_project`&#x27;i ile başsız olarak çalıştırın
* **GPU hızlandırma** — NVIDIA GPU&#x27;larda (masaüstü ve Jetson) CUDA ile hızlandırılmış işleme
* **Dinamik Hesaplama Uyumlaştırma** — otomatik donanım algılama ve işleme stratejisi seçimi; uzmanlar için bir kaçış yolu olarak `CHLOROS_STRATEGY` geçersiz kılma seçeneği
* **Tüm işleme özellikleri** — Windows ile aynı iş akışı: kalibrasyon, vinyet düzeltme, bitki örtüsü indeksleri ve tüm dışa aktarım formatları
* **Chloros+ özellikleri** — çok iş parçacıklı (pipeline) işleme, Texture Aware debayer ve özel indeksler; ücretli Chloros+ planı ile

## Linux Kullanıcılarının Elde Edemedikleri Özellikler

* **Masaüstü GUI** — grafik arayüz yoktur; tüm etkileşim CLI veya Python SDK üzerinden gerçekleşir
* **Görüntü Görüntüleyici** — etkileşimli görüntü görüntüleyici, ızgara görünümü veya harita işaretçileri yoktur
* **Görsel proje yönetimi** — projeler, CLI komutları ve SDK çağrıları aracılığıyla oluşturulur ve yürütülür (donanımın kendisi — kameralar, sensörler, yakalama — terminalden tamamen kontrol edilebilir)***

## Lisans Gereksinimi

CLI ve SDK erişimi için **ücretli bir Chloros+ kademesi — Copper veya üstü**(Copper, Bronze, Silver, Gold) gereklidir. Ücretsiz**Iron** kademesinde CLI/SDK erişimi yoktur. Bu sınırlama, yalnızca CLI tarafından değil, arka uç tarafından da uygulanır:

| Durum | Arka uç yanıtı |
| --- | --- |
| Oturum açılmamış | `401` ile `error_code: AUTH_REQUIRED` |
| Ücretsiz Iron kademesinde oturum açılmış | `403` ile `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` her kademede çalışır — bu, geçitten muaf olan tek yoldur — bu nedenle reddedilme nedeni her zaman görülebilir.

***

## Linux ile Başlangıç

1. **Chloros&#x27;i kurun** — `.deb` kurulumu için [Linux Kurulumu](linux-installation.md) sayfasına bakın
2. **Doğrulama** — `chloros-cli --version`, `Chloros CLI 1.2.0` yazdırır; `chloros-cli selftest`, 7 adımlı tanılama işlemini çalıştırır
3. **Python ve SDK&#x27;i kurun** (isteğe bağlı) — `pip install chloros-sdk`
4. **Oturum açın** — `chloros-cli login your@email.com 'your-password'` (her makine için bir kez ve her paket güncellemesinden sonra tekrar)
5. **İlk veri kümenizi işleyin** — `chloros-cli process ~/datasets/flight001`

NVIDIA Jetson için, platforma özgü kurulum, termal davranış ve saha dağıtımı hakkında bilgi almak üzere [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) bölümüne bakın.

***

## Sonraki Adımlar

* [Linux Kurulumu](linux-installation.md) — amd64 ve arm64 için ayrıntılı kurulum, dosya konumları ve sorun giderme
* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson&#x27;a özgü kurulum, bellek ve termal davranış, sahada kullanıma alma
* [CLI : Komut Satırı](../CLI.md) — CLI kılavuzu
* [API : Python SDK](../api-python-sdk.md) — SDK kılavuzu
* [CLI Referansı](../reference/cli-reference.md) ve [SDK Referansı](../reference/sdk-reference.md) — 1.2.0 sürümü için kapsamlı komut/API listeleri
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros&#x27;in donanımınıza nasıl uyum sağladığı

{% hint style="info" %}
**Bu kılavuzu programlı olarak okuma.** Her sayfa, kendi URL artı `.md` (örneğin `https://mapir.gitbook.io/chloros/linux/linux-installation.md`) adreslerinde ham Markdown biçimi olarak sunulur ve kılavuzun tamamının içindekiler sayfası [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt) adresinde yayınlanmaktadır.
{% endhint %}
