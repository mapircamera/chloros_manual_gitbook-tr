---
metaLinks: {}
---

# Başlangıç

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>Chloros, görüntüleri ve diğer sensör verilerini işlemek için [MAPIR](https://www.mapir.camera) tarafından geliştirilen bir yazılım uygulamasıdır.

***{% hint style="success" %}**Chloros 1.1.0&#x27;daki Yenilikler**: Yerel Linux desteği (amd64 ve arm64), NVIDIA Jetson kenar bilgi işlem, Dinamik Hesaplama Uyumlaştırma, 4 iş parçacıklı işleme boru hattı, yeni CLI komutları ve seçenekleri. Tam değişiklik günlüğü için [İndir](download.md) bölümüne bakın.
{% endhint %}

Chloros, 3 uygulama modunda kullanılabilir:

## Chloros: Masaüstü GUI uygulaması

Tüm özelliklere sahip bağımsız ayrı pencere. _Yalnızca Windows._

## [Chloros CLI: Komut satırı arayüzü](CLI.md)

Komut satırı toplu işleme. Otomasyon, komut dosyası oluşturma ve başsız çalışma için mükemmeldir. **Windows, Linux amd64 ve Linux arm64 (NVIDIA Jetson)** üzerinde kullanılabilir. _CLI&#x27;ye erişmek için Chloros+ lisansı gereklidir._

## [Chloros API: Python SDK](api-python-sdk.md)

Otomasyon ve özel iş akışları için programlı Python arayüzü. Araştırma süreçleri, mevcut Python uygulamalarıyla entegrasyon ve özel araçlar oluşturmak için mükemmeldir. `pip install chloros-sdk` aracılığıyla **tüm platformlarda** kullanılabilir. _API&#x27;ye erişim için Chloros+ lisansı gereklidir._***

## Desteklenen Platformlar

| Platform | GUI | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11** | Evet | Evet | Evet |
| **Linux amd64 (x86_64)** | Hayır | Evet | Evet |
| **Linux arm64 (NVIDIA Jetson)** | Hayır | Evet | Evet |

Linux kurulum talimatları için [Linux &amp; Edge Computing](linux/linux-overview.md) bölümüne bakın.

***

## Chloros+

Chloros çoğu görev için ücretsiz olarak kullanılabilse de, daha fazlasını isteyebilirsiniz. İşte bu noktada, Chloros+ için ücretli bir lisans size fayda sağlayabilir. Chloros+ lisansıyla aşağıdaki gibi yeni özelliklerin kilidini açabilirsiniz:

* **Çok İş Parçacıklı İşleme**: Görüntüleri işleme hattı üzerinden eşzamanlı olarak işleyerek büyük projelerde görüntü işlemeyi büyük ölçüde hızlandırın.
* **GPU (CUDA) Hızlandırma**: Görüntü işleme boru hattını daha da hızlandırmak için günümüzün daha yüksek GPU bellek seçeneklerinden yararlanın. En iyi sonuçlar için 4 GB veya daha fazla VRAM öneririz.
* **Chloros+**[**CLI**](CLI.md)**Erişim**: Chloros+&#x27;ı komut satırından çalıştırarak kendi yazılımınızla otomatikleştirin ve entegre edin.
* **Chloros+**[**API**](api-python-sdk.md)**Erişim:** programlı kontrol için Python&#x27;ten Chloros+&#x27;ı çalıştırın; böylece araştırma süreçleriniz, veri analizi iş akışlarınız ve özel uygulamalarınızla sorunsuz entegrasyon sağlayın.
* **Birden Fazla Cihaz Kullanımı**: Her Chloros+ lisansı, 2 veya daha fazla cihazın kaydedilmesine izin verir. Kaydedilen cihazları yönetmek için MAPIR Cloud hesabınızı kullanın. Chloros+ lisansınızı yükseltmek suretiyle daha fazla cihaz desteği ekleyin.
* **Gelişmiş Doku Duyarlı Debayer Yöntemi:** neredeyse tüm debayering gürültüsünü ortadan kaldıran bir AI/ML gürültü giderme modeli ile birleştirilmiş, yüksek kaliteli kenar duyarlı bir debayer. 
* **Özel Çok Spektral İndeks Formülleri:** hem işleme hem de görüntü görüntüleme sandbox&#x27;ı için Chloros raster hesaplayıcılara özel çok spektral indeksler girin.
* **Linux ve Kenar Bilişim:** saha ve kenar işleme için NVIDIA Jetson dahil olmak üzere Linux x86\_64 ve ARM64 platformlarında Chloros&#x27;i çalıştırın. Bkz. [Linux Genel Bakış](linux/linux-overview.md).

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ Fiyatlandırma ve Kaydolma</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
