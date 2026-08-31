# Chloros’i Yapay Zeka Asistanlarıyla Kullanma

Bu kılavuz iki hedef kitleye yönelik olarak hazırlanmıştır: insanlar ve insanların giderek daha fazla işlerini yürütmek için kullandığı yapay zeka asistanları. Her sayfada kesin değerler, varsayılan ayarlar ve kopyala-yapıştır yapılabilen komutlar yer almaktadır; böylece bir asistan (Claude, ChatGPT, Copilot, bir kodlama ajanı vb.) ilk denemede çalışan bir Chloros otomasyonu yazabilir.

Chloros sürümü: **

1.2.0**. CLI/SDK platformları: Windows 10/11 x64 ve Linux (x86_64 / Jetson aarch64).

## Asistanınıza ne vermeniz gerekir

| Kaynak | URL | Ne için kullanılır |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Bu kılavuzdaki her sayfanın makine tarafından okunabilir dizini. |
| **CLI Referansı** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | Tam `chloros-cli` komut yüzeyi: her komut, bayrak, varsayılan değer, çıkış kodu ve çıktı klasörü kuralı. LLM kullanımı için yazılmıştır. |
| **SDK Referansı** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Tam `chloros_sdk` Python API: sınıflar, imzalar, istisnalar ve uygulamalı örnekler. LLM kullanımı için yazılmıştır. |
| **Herhangi bir sayfa ham Markdown olarak** | `.md`&#x27;i URL sayfasına ekleyin | ör. `https://mapir.gitbook.io/chloros/reference/sdk-reference.md`, sayfayı ham Markdown olarak döndürür — bir bağlam penceresine yapıştırmak veya bir ajandan almak için idealdir. |

Kılavuz içi bağlantılar: [CLI Referansı](reference/cli-reference.md) · [SDK Referansı](reference/sdk-reference.md).

{% hint style="info" %}
Bu iki referans sayfası kendi başına eksiksizdir: bunlardan birini okumuş bir asistan, doğru bir komut dosyası yazmak için kılavuzun geri kalanına ihtiyaç duymaz.
{% endhint %}

## Hazır komut örnekleri

`<placeholders>`&#x27;i kopyalayın, gerekli bilgileri girin ve asistanınıza yapıştırın.

### 1. Bir uçuş klasörünü NDVI&#x27;e işleyin

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. Yakalama dizinini toplu olarak izleme

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. Bir LATTICE dizisini bağlayın ve yakalama yapın

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. DAQ ışık sensörü spektrumlarını kaydedin

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
Komut satırından DAQ komut dosyası çalıştırma işlemi her zaman `daq pool-*` ailesi (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`) üzerinden gerçekleştirilir. Asistanınızın uydurabileceği diğer `daq` alt komutları, piyasaya sürülen sürümlerde mevcut değildir ve bir hata ile sonlanır.
{% endhint %}

## AI tarafından yazılan komut dosyaları neden Chloros ile iyi çalışıyor?

Bunların her biri, Chloros 1.2.0 sürümünün gerçek ve doğrulanmış davranışlarıdır — bunlar, makine tarafından yazılan otomasyonların klasik hata modlarını ortadan kaldırır:

* **Kurulum karmaşası yok.**SDK&#x27;in akıllı bağlantı yardımcıları (`connect_camera`, `connect_array`, `connect_daq_sensor`) ve işleme giriş noktaları (`ChlorosLocal`, `process_folder`)**yerel arka ucu otomatik olarak başlatır**. Oluşturulan bir komut dosyasının çalışması için GUI&#x27;nin açık olması veya sunucunun manuel olarak başlatılması gerekmez — yalnızca desktop/CLI paketinin yüklü olması yeterlidir.
* **Tüm iş akışı tek bir çağrıdır.** `chloros_sdk.process_folder("path", indices=["NDVI"])`, içe aktarma → kalibrasyon → yansıma → indeks dışa aktarma işlemlerini uçtan uca yürütür. Yüzey alanı daha az olduğundan, oluşturulan komut dosyasının hata verebileceği noktalar da daha azdır.
* **Çıktı alınmayan çalıştırmalar kendi kendini teşhis eder.** `process()`&#x27;ten sonra, çalıştırmanın özeti sonuca eklenir ve her işleme ipucu (örn. *neden* bir işlem çıktısı üretmedi) de bir Python `UserWarning` olarak yeniden gönderilir — böylece sonucu asla incelemese bile bir komut dosyası teşhisi ortaya çıkarır.
* **CLI açıkça hata verir.**Ürün talep eden ancak hiçbir şey yazmayan bir `chloros-cli process` çalıştırması, `Processing finished but wrote no image products.` kodunu yazdırır ve**sıfırdan farklı bir kodla sonlanır**; böylece kabuk komut dosyaları ve CI, basit bir çıkış kodu kontrolüyle bunu algılar. Başarılı çalıştırmalar `Image products written: N` kodunu bildirir.

Bir asistanın bilmesi gereken bir asimetri: SDK&#x27;in `process()`&#x27;i, sıfır ürün içeren bir çalışmada kasıtlı olarak **hata** vermez — bunun yerine özet/ipuçları yoluyla rapor eder. Bir Python iş akışı boş bir çalıştırmada durmak zorunda kalırsa, özeti kontrol edin (reçete 2 bunu yapar).

## Dikkat Edilmesi Gerekenler

* **Chloros+ için oturum açılması gerekir.**CLI ve SDK,**ücretli** Chloros+ kademesi gerektirir; bu kısıtlama sunucu tarafında uygulanır: oturum açılmadığında istekler `401 AUTH_REQUIRED` hatasıyla, ücretsiz kademede ise `403 PLAN_UPGRADE_REQUIRED` hatasıyla sonuçlanır. Oluşturulan komut dosyalarını çalıştırmadan önce her makine için bir kez `chloros-cli login` komutunu çalıştırın. Bkz. [Chloros+ Oturum Açma](chloros+-login.md).
* **Yakalama komutları gerçek donanımı kontrol eder.** `lattice` / `daq` / `project` komutları ve SDK oturum nesneleri, fiziksel kameralara ve sensörlere bağlanır, bunlardan veri akışı alır ve bunları tetikler. Oluşturulan komut dosyasını ilk çalıştırmadan önce gözden geçirin ve donanım başında olarak çalıştırın.
* **Çıktıları rastgele kontrol edin.** Sonuçları yayınlamadan önce ürün klasörlerini ve birkaç piksel değerini doğrulayın. Özellikle, yansıma TIFF&#x27;leri kaynağa göre ölçeklenir — bir bölücü varsaymak yerine `Chloros:PixelScale` XMP etiketini (LATTICE: 32768 = 1,0 yansıma; Survey3: 65535) bir bölücü varsaymak yerine. Her iki referans sayfası da bunu &quot;Yansıma piksellerini okuma&quot; başlığı altında belgelemektedir.
* **Oluşturulan kodu aksatan küçük tuzaklar:**`pool-record`,**arka uç ana bilgisayarın** dosya sistemine yazar (varsayılan `~/Documents/DAQ Live View/`); birden fazla ağ arabirimine sahip makinelerde, otomatik keşif yerine `daq pool-connect --eth-host <ip-or-hostname>`&#x27;i tercih edin; ve arka uç URL&#x27;in geçtiği her yerde `http://127.0.0.1:5000`&#x27;i (asla `localhost`&#x27;i) kullanın.
