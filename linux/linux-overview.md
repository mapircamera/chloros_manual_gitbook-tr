# Linux Genel Bakış

Chloros 1.1.0 sürümü, **CLI**ve**Python SDK** için yerel Linux desteği sunarak, Linux iş istasyonlarında, sunucularda ve NVIDIA Jetson uç cihazlarında başsız multispektral görüntü işlemeyi mümkün kılar.

{% hint style="info" %}
**Linux&#x27;te GUI yoktur.** Chloros Masaüstü GUI&#x27;si yalnızca Windows&#x27;te kullanılabilir. Linux kullanıcıları, [CLI](../CLI.md) ve [Python SDK](../api-python-sdk.md) aracılığıyla etkileşim kurar.
{% endhint %}

***

## Platform Destek Matrisi

| Özellik | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **Masaüstü GUI** | Evet | Yok | Hayır | Hayır |
| **CLI** | Evet | Evet | Evet | Evet |
| **Python SDK** | Evet | Evet | Evet | Evet |
| **GPU Hızlandırma (CUDA)** | Evet | Evet | Evet | Evet (JetPack 6) |
| **Doku Duyarlı Debayer** | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) | Evet (Chloros+) |
| **Dinamik Hesaplama Uyumlaştırma** | Evet | Evet | Evet | Evet |***

## Desteklenen Mimariler

| Mimari | Açıklama | Kurulum Yöntemi |
| --- | --- | --- |
| **amd64 (x86_64)** | Standart masaüstü/sunucu işlemcileri (Intel, AMD) | `.deb` paketi |
| **arm64 (aarch64)** | ARM tabanlı işlemciler, öncelikle NVIDIA Jetson | `.deb` paketi (JetPack 6) |

## Desteklenen Linux Dağıtımları

* **Ubuntu 20.04+** (amd64)
* **Debian 11+** (amd64)
* **NVIDIA JetPack 6** (arm64 — Jetson platformları)***

## Linux Kullanıcıları Ne Kazanır?

* **Chloros CLI** — Toplu işleme, otomasyon ve komut dosyası oluşturma için tam komut satırı arayüzü
* **Chloros Python SDK** — Araştırma iş akışlarına ve özel araçlara entegrasyon için programlı Python arayüzü (`pip install chloros-sdk`)
* **GPU Hızlandırma** — NVIDIA GPU&#x27;larda (masaüstü ve Jetson) CUDA ile hızlandırılmış işleme
* **Dinamik Hesaplama Uyumlaştırma** — Otomatik donanım algılama ve işleme stratejisi optimizasyonu
* **Tüm İşleme Özellikleri** — Windows ile aynı multispektral işleme boru hattı (kalibrasyon, vinyet düzeltme, bitki örtüsü indeksleri, tüm dışa aktarma formatları)
* **Chloros+ Özellikleri** — Çok iş parçacıklı işleme, Doku Duyarlı debayer, özel indeksler (Chloros+ lisansı ile)

## Linux Kullanıcılarının Elde Edemedikleri Özellikler

* **Masaüstü GUI** — Grafik arayüz yoktur; tüm etkileşim CLI veya Python SDK aracılığıyla gerçekleşir
* **Görüntü Görüntüleyici** — Etkileşimli görüntü görüntüleyici, ızgara görünümü veya harita işaretçileri yoktur
* **Görsel Proje Yönetimi** — Projeler, CLI komutları ve SDK çağrıları aracılığıyla yönetilir***

## Linux&#x27;e Başlarken

1. **Chloros&#x27;i Yükleyin** — `.deb` paketinin yüklenmesi için [Linux Kurulumu](linux-installation.md) bölümüne bakın
2. **Python SDK&#x27;i yükleyin** (isteğe bağlı) — `pip install chloros-sdk`
3. **Lisansınızı etkinleştirin** — `chloros-cli login your@email.com 'password'`
4. **İlk veri setinizi işleyin** — `chloros-cli process ~/datasets/flight001`

NVIDIA Jetson kullanıcıları için, platforma özgü kurulum ve optimizasyon hakkında özel [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) bölümüne bakın.

***

## Sonraki Adımlar

* [Linux Kurulumu](linux-installation.md) — amd64 ve arm64 için ayrıntılı kurulum talimatları
* [NVIDIA Jetson Kılavuzu](nvidia-jetson-guide.md) — Jetson&#x27;a özgü kurulum, termal yönetim ve saha dağıtımı
* [CLI : Komut Satırı](../CLI.md) — Tam CLI referansı
* [API : Python SDK](../api-python-sdk.md) — Tam SDK referansı
* [Dinamik Hesaplama Uyumlaştırma](../processing-architecture/dynamic-compute-adaptation.md) — Chloros&#x27;in donanımınıza nasıl uyum sağladığı
