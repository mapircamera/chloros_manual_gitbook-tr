# Chloros’u Yapay Zeka Asistanlarıyla Kullanma

Bu kılavuz iki hedef kitleye yönelik olarak yazılmıştır: insanlar ve insanların giderek daha fazla kullandığı AI asistanları. Her sayfada kesin değerler, varsayılan ayarlar ve kopyala-yapıştır edilebilir komutlar yer almaktadır; böylece bir asistan (Claude, ChatGPT, Copilot, bir kodlama ajanı, …) ilk denemede çalışan bir Chloros otomasyonu yazabilir.

Chloros sürümü: **

1.3.0**. CLI/SDK platformları: Windows 10/11 x64 ve Linux (x86\_64 / Jetson aarch64).

## Asistanınıza ne vermelisiniz?

| Kaynak | URL | Ne için kullanılır |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | Bu kılavuzdaki her sayfanın makine tarafından okunabilir dizini. |
| **CLI Kılavuzu** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | `chloros-cli` komut arayüzünün tamamı: tüm komutlar, bayraklar, varsayılan değerler, çıkış kodları ve çıktı klasörü kuralları. LLM kullanımı için yazılmıştır. |
| **SDK Referansı** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Tam `chloros_sdk` Python API'si: sınıflar, imzalar, istisnalar ve uygulamalı örnekler. LLM kullanımı için yazılmıştır. |
| **Herhangi bir sayfa ham Markdown olarak** | sayfanın URL'sine `.md` ekleyin | örn. `https://mapir.gitbook.io/chloros/reference/sdk-reference.md`, sayfayı ham Markdown olarak döndürür — bir bağlam penceresine yapıştırmak veya bir ajandan almak için idealdir. |

Kılavuz içi bağlantılar: [CLI Referansı](reference/cli-reference.md) · [SDK Referansı](reference/sdk-reference.md).

{% hint style="info" %}
Bu iki referans sayfası tek başına yeterlidir: bunlardan birini okumuş bir asistan, doğru bir komut dosyası yazmak için kılavuzun geri kalanına ihtiyaç duymaz.
{% endhint %}

## Asistanınızın Chloros'u doğrudan çalıştırmasını sağlayın: `chloros-mcp`

Yukarıdaki tüm örneklerde asistanınızın bir komut dosyası veya komut *yazması* söz konusudur. `chloros-mcp` ile, Claude Desktop, Claude Code veya Cursor gibi [Model Bağlam Protokolü](https://modelcontextprotocol.io) (MCP) ile iletişim kuran bir asistan, Chloros'u kendisi çalıştırır. Siz sade bir dille talimat verirsiniz ve asistan, sunucunun araçlarını çağırır:

* **Görüntüleri işleme:** bir proje oluşturur, yakalanan görüntülerin bulunduğu bir klasörü içe aktarır, ayarları seçer, işlemeyi gerçekleştirir ve işlemin sonucunda elde edilen verileri raporlar.
* **Donanımı çalıştırma:** LATTICE kameralarını, dizilerini ve DAQ ışık sensörlerini bağlayın, önizleme yapın, pozlama ve rengi ayarlayın, yakalayın, kaydedin ve ürün yazılımını güncelleyin.

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

Sunucunun araçları, [Python SDK](api-python-sdk.md) tarafından yapılan işlemleri kapsar. SDK ile birlikte kurulur, bu nedenle genellikle başka bir şey yüklemeye gerek yoktur: Windows'da Chloros yükleyicisi, `chloros-mcp.exe` dosyasını Python'unuzun `Scripts` klasörüne yerleştirir, Linux'ta ise `.deb`, `chloros-mcp` dosyasını `/usr/bin` klasörüne yerleştirir.

**Claude Kodu:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Claude Desktop:**bunu `claude_desktop_config.json` dosyasına ekleyin (**Ayarlar > Geliştirici > Yapılandırmayı Düzenle**seçeneği ile açabilirsiniz), ardından Claude Desktop'ı yeniden başlatın.**Cursor** için de `~/.cursor/mcp.json` dosyasına aynı girişi yapın.

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

İstemci, sunucunun başlatılamadığını söylüyorsa, `chloros-mcp` muhtemelen gördüğü `PATH`'te yer almıyordur: bunun yerine tam yolu girin (Windows'ta `where chloros-mcp`, Linux'ta `which chloros-mcp`).

Aynı bilgisayarda Chloros'un kurulu olması ve orada, uygulamada veya `chloros-cli login` ile herhangi bir ücretli Chloros+ kademesinde (Copper veya üstü) oturum açılmış olması gerekir. Sunucu sizin adınıza oturum açamaz.

{% hint style="info" %}
Her uygulama, seçenekler, her araç ve bir çalıştırmanın nasıl davrandığına ilişkin istemci kurulumu: SDK Referansı'ndaki [MCP Sunucusu (AI Asistanları)](reference/sdk-reference.md#mcp-server-ai-assistants).
{% endhint %}

## Komut şablonları

`<placeholders>`'i kopyalayın, gerekli bilgileri girin ve asistanınıza yapıştırın.

### 1. Bir uçuş klasörünü NDVI'ye dönüştürün

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

### 3. LATTICE dizisini bağlama ve yakalama

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
Komut satırından DAQ komut dosyası çalıştırma işlemi her zaman `daq pool-*` ailesi (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`) üzerinden gerçekleştirilir. Asistanınızın uydurabileceği diğer `daq` alt komutları, dağıtılan sürümlerde mevcut değildir ve bir hata ile sonlanır.
{% endhint %}

## AI tarafından yazılan komut dosyaları neden Chloros ile iyi çalışıyor?

Bunların her biri, Chloros 1.3.0'ın gerçek ve doğrulanmış davranışlarıdır — bunlar, makine tarafından yazılan otomasyonların klasik hata modlarını ortadan kaldırır:

* **Kurulumla uğraşmaya gerek yok.**SDK’nın akıllı bağlantı yardımcıları (`connect_camera`, `connect_array`, `connect_daq_sensor`) ve işleme giriş noktaları (`ChlorosLocal`, `process_folder`)**yerel arka ucu otomatik olarak başlatır**. Oluşturulan bir komut dosyasının GUI'nin açık olması veya sunucunun manuel olarak başlatılması gerekmez — yalnızca masaüstü/CLI paketinin kurulu olması yeterlidir.
* **Tüm iş akışı tek bir çağrıdır.** `chloros_sdk.process_folder("path", indices=["NDVI"])`, içe aktarma → kalibrasyon → yansıma → indeks dışa aktarma işlemlerini uçtan uca yürütür. Yüzey alanı daha az olduğundan, oluşturulan komut dosyasının hata verebileceği noktalar da daha azdır.
* **Çıktı alınamayan çalıştırmalar kendi kendini teşhis eder.** `process()`'ten sonra, çalıştırmanın özeti sonuca eklenir ve her işleme ipucu (örn. *neden* bir işlem çıktısı üretmedi) de bir Python `UserWarning` olarak yeniden gönderilir — böylece sonuç sözlüğünü asla incelemeyen bir komut dosyası bile tanıyı ortaya çıkarır.
* **CLI, açıkça hata verir.**Ürün talep eden ancak hiçbir şey yazmayan bir `chloros-cli process` çalışması, `Processing finished but wrote no image products.` mesajını yazdırır ve**sıfırdan farklı bir kodla sonlanır**; böylece kabuk betikleri ve CI, basit bir çıkış kodu kontrolüyle bunu algılar. Başarılı çalıştırmalar `Image products written: N` kodunu bildirir; maskeler yazıldığında ise buna ek olarak `(plus M clamp masks)` kodu da eklenir. Yakalama komutları da aynı şekilde yazamadıkları her dosyanın adını belirtir ve `1` koduyla sonlanır.
* **Göreceli yollar, betiğin beklediği anlamı taşır.** Yerel bir arka uç kullanıldığında, göreceli bir çıktı yolu (CLI'de `-o out/`, SDK'de `output_dir="out/"`), komutun veya betiğin çalıştırıldığı klasöre göre çözümlenir.

Bir asistanın bilmesi gereken bir asimetri: SDK'nin `process()` komutu, sıfır çıktı veren bir çalıştırmada kasıtlı olarak **hata** vermez — bunun yerine özet/ipuçları aracılığıyla raporlar. Bir Python ardışık düzeni boş bir çalıştırmada durmak zorundaysa, özeti kontrol edin (2. tarif bunu yapar).

## Dikkat Edilmesi Gerekenler

* **Chloros+ oturumu gereklidir.**CLI ve SDK, sunucu tarafında uygulanan**ücretli** bir Chloros+ kademesi gerektirir: oturum açılmadığında istekler `401 AUTH_REQUIRED` hatasıyla, ücretsiz kademede ise `403 PLAN_UPGRADE_REQUIRED` hatasıyla başarısız olur. Oluşturulan komut dosyalarını çalıştırmadan önce her makine için bir kez `chloros-cli login` komutunu çalıştırın. Bkz. [Chloros+ Oturum Açma](chloros+-login.md).
* **Yakalama komutları gerçek donanımı kontrol eder.** `lattice` / `daq` / `project` komutları, SDK oturum nesneleri ve `chloros-mcp` araçları, fiziksel kameralara ve sensörlere bağlanır, bunlardan veri akışı sağlar ve bunları tetikler. Oluşturulan komut dosyasını ilk çalıştırmadan önce gözden geçirin ve donanım başında olarak çalıştırın.
* **Çıktıları rastgele kontrol edin.** Sonuçları yayınlamadan önce ürün klasörlerini ve birkaç piksel değerini doğrulayın. Özellikle, yansıma TIFF'leri kaynağa göre ölçeklenir — `Chloros:PixelScale` XMP etiketini okuyun (LATTICE dosyaları bunu içerir: 32768 = 1,0 yansıma; Survey3 dosyaları taşımaz ve 65535 = 1,0 değerini kullanır) yerine bir bölücü varsaymayın. Her iki referans sayfası da bunu "Yansıma pikselini okuma" başlığı altında belgelemektedir.
* **Oluşturulan kodu aksatan küçük tuzaklar:**`pool-record`,**arka uç ana bilgisayarın** dosya sistemine yazar (varsayılan `~/Documents/DAQ Live View/`); birden fazla ağ arabirimine sahip makinelerde, otomatik keşif yerine `daq pool-connect --eth-host <ip-or-hostname>`'i tercih edin; arka uç URL'sinin geçtiği her yerde `http://127.0.0.1:5000`'i kullanın (asla `localhost`'i kullanmayın); bir projenin çıktı ağacını tarayan bir komut dosyası, `clamp_masks/` klasörlerini atlamalıdır (bu klasörlerdeki `*_clampmask.tif` dosyaları görüntüler değil, maskeleridir — bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md#clamp-masks)); ve uzun yol özelliği devre dışı bırakılmış Windows sistemlerinde proje klasörlerini kısa tutun — yaklaşık 130 karakterden daha derin bir proje klasörü, işleme başlamadan önce reddedilir.
