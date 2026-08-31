---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# SSS

<details>

<summary>Chloros ile MAPIR markası dışındaki kameralardan gelen görüntüleri işleyebilir miyim?</summary>

Hayır, Chloros yalnızca MAPIR kamera görüntülerini (Survey3 ve LATTICE serileri) işleyebilir. Daha fazla bilgi için lütfen [desteklenen kamera modelleri](supported-cameras.md) listesine bakın. MAPIR Cloud&#x27;da diğer kameraların işlenmesini de sunuyoruz; tam listeye [buradan](https://mapir.gitbook.io/mapir-cloud/supported-cameras) ulaşabilirsiniz.

</details>

<details>

<summary>Chloros, LATTICE kameralarını destekliyor mu?</summary>

Evet. Chloros 1.2.0, LATTICE M3C ve M3M kamera modüllerini uçtan uca destekler: **canlı kontrol**— GUI&#x27;nin Kameralar sekmesinden kameraları keşfedin, bağlayın, önizleyin ve görüntü yakalayın, `chloros-cli lattice` veya Python SDK aracılığıyla, PTP zaman senkronizasyonlu senkronize çoklu kamera dizileri dahil — ve yakalanan görüntüler üzerinde**tam radyometrik işleme** (ham → debayered → parlaklık → yansıma → indeks). [Desteklenen Kameralar](supported-cameras.md) ve [LATTICE kılavuzu](lattice/README.md) bölümlerine bakın.

</details>

<details>

<summary>Kalibrasyon hedefi olmadan görüntülerimi yansıtma açısından kalibre edebilir miyim?</summary>

**Survey3:** Hayır. Hedef olmayan görüntüler çekilirken aynı anda çekilmiş bir kalibrasyon hedefi görüntüsü olmadan, görüntünün piksel değerlerini bilinen bir yansıma yüzdesiyle ilişkilendiremezsiniz. Ayrıca, bir MAPIR ışık sensöründen alınan verileri de dahil etmezseniz, ortam ışığı spektrumu ölçülmeyecek ve yansıma sonuçları doğru olmayacaktır.**LATTICE:** Evet. Yansıtma, bir panel yerine bir DAQ ışık sensörü tarafından ölçülen aşağı doğru ışınım yoğunluğuna göre referanslanabilir (ρ = π·L/E). QA&#x27;dan geçen bir çerçeve içi hedef *varsa*, varsayılan olarak mutlak referans haline gelir (`--reflectance-source auto`). Tek istisna: &quot;F988 yansıma değeri, sahnedeki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesindedir, bu nedenle Chloros, en son panel yakalamanızı uygular ve panel gözlemleri arasında bunu korur.&quot; Bkz. [Kalibrasyon Hedefleri](calibration-targets.md).

</details>

<details>

<summary>DAQ ışık sensörüne ihtiyacım var mı?</summary>

Radyans için gerekmez: LATTICE radyans dışa aktarımları, her kameranın fabrika radyometrik kalibrasyonundan elde edilir ve ne bir DAQ sensörüne ne de bir hedefe ihtiyaç duyar. **Yansıma**için ortam ışığına ilişkin bir referansa ihtiyacınız vardır — bu, bir DAQ ışık sensörünün aşağı doğru ışık ölçümü veya çerçeve içi bir kalibrasyon hedefi olabilir. Bir DAQ sensörü,**sahneye herhangi bir panel yerleştirmeden** kalibre edilmiş yansıma değeri elde etmenizi sağlar. Kaydedilen `.daq` dosyaları, zaman damgası aracılığıyla görüntülerinizle otomatik olarak eşleştirilir. [Kalibrasyon Hedefleri](calibration-targets.md) ve [CLI Referansı](reference/cli-reference.md) bölümlerine bakınız.

</details>

<details>

<summary>Chloros&#x27;i bir AI asistanıyla (Claude, ChatGPT vb.) kullanabilir miyim?</summary>

Evet — bu kılavuz ve CLI/SDK dosyaları bunun için hazırlanmıştır:

* AI asistanlarının her sayfayı bulabilmesi için kılavuzun tam dizinine `https://mapir.gitbook.io/chloros/llms.txt` adresinden erişilebilir.
* Her sayfanın ham Markdown kodu, sayfanın küçük harfli adının sonuna `.md` eklenmiş haliyle (örneğin `https://mapir.gitbook.io/chloros/reference/cli-reference.md`) URL adresinde mevcuttur.
* [CLI Referansı](reference/cli-reference.md) ve [SDK Referansı](reference/sdk-reference.md), LLM kullanımı için yazılmıştır: kesin bayraklar, varsayılanlar, çıkış semantiği ve kopyala-yapıştır edilebilir komutlar.

Asistanınızı Chloros&#x27;e nasıl yönlendireceğiniz konusunda [AI Asistanları](ai-assistants.md) bölümüne bakın.

</details>

<details>

<summary>İşlenmiş çıktı dosyalarım nereye gider?</summary>

Çıktılar, proje klasörü altında, önce kameraya göre, ardından dosya formatına göre gruplandırılmış olarak kaydedilir:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kamera-klasörü** — LATTICE için `LATT-<sensor>-<lens>-F<filter>`, Survey3 için `<model>_<filter>` (örn. `Survey3N_RGN`)
* **format-klasörü** — `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32`
* **ürün klasörleri** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (her zaman `tiff32` altında), `<INDEX>_Index_Images/`**Dışa aktarılan dosyalar kaynak dosyanın adını korur — klasör, dosya adı sonekini değil ürünü tanımlar.**CLI ile, `-o` parametresini belirtmediğiniz sürece proje klasörü giriş klasörünün yanına oluşturulur. Ürün talep eden ancak hiçbir şey yazmayan bir `chloros-cli process` çalıştırması, `Processing finished but wrote no image products.` çıktısını verir ve**sıfırdan farklı bir değerle sonlanır**; böylece komut dosyaları bunu algılayabilir. [Çıktı Görüntü Biçimleri](output-image-formats.md) ve [CLI Referansı](reference/cli-reference.md) bölümlerine bakınız.

</details>

<details>

<summary>Chloros&#x27;te işleme öncesinde görüntülerimi düzenleyebilir miyim?</summary>

Hayır. Chloros, giriş verilerinin değiştirilmediğini varsayar. Dosya adlarını değiştirmeyin.

</details>

<details>

<summary>MAPIR ve Survey3 kameralarımı otomatik pozlamaya ayarlayıp görüntüleri Chloros&#x27;te işleyebilir miyim?</summary>

Hayır. Survey3 görüntü veri kümelerinin sabit/kilitli bir pozlamaya sahip olması gerekir; bu nedenle otomatik enstantane hızı veya otomatik ISO kullanılamaz. Aynı kamera modeline ait tüm görüntülerde enstantane hızı ve ISO (pozlama) değerleri aynı olmalıdır.

LATTICE kameralarında bu kısıtlama yoktur: Chloros, pozlamayı canlı olarak kontrol eder (Akıllı AE) ve her çekimde gerçekte kullanılan pozlama ve kazanç kaydedilir; radyometrik işleme süreci bu değerleri hesaba katar.

</details>

<details>

<summary>Chloros, ortomozaik görüntüleri işleyebilir veya analiz edebilir mi?</summary>

Hayır. Yalnızca tek tek MAPIR kamera görüntüleri desteklenir; ortomozaik harita gibi birleştirilmiş görüntüler desteklenmez.

</details>

<details>

<summary>Chloros&#x27;in hedef algılama adımını nasıl hızlandırabilirim?</summary>

Dosya tarayıcı tablosunda sağ sütundaki hedef görüntüleri önceden seçmek, Chloros&#x27;e kalibrasyon hedeflerini yalnızca bu görüntülerde aramasını söyler ve işlemeyi büyük ölçüde hızlandırır.

</details>

<details>

<summary>Görüntülerimi <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud</a>&#x27;a yükleyeceksem, yüklemeden önce Chloros&#x27;te işlem yapmam gerekir mi?</summary>

Çevrimiçi işleme platformumuz [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription) &#x27;a yüklemeyi planlıyorsanız, yüklemeden önce görüntüleri düzenlemeyin. Cloud, aynı işlemleri ve daha fazlasını gerçekleştirecektir.

</details>

<details>

<summary>MAPIR, X özelliğini destekleyecek mi? MAPIR&#x27;in X özelliğini sunmasını gerçekten çok isterim.</summary>

Ürünlerimizle ilgili geri bildirim almaktan her zaman memnuniyet duyarız. Ürünlerimizde bir sorunla karşılaşırsanız veya ürünlerimizi nasıl iyileştirebileceğimiz konusunda bir öneriniz varsa, lütfen [BİZE ULAŞIN](https://www.mapir.camera/community/contact) ve düşüncelerinizi bizimle paylaşın. Ar-Ge çalışmalarımızın çoğu, müşterilerimizin en önemli ihtiyaçlarını dinlemeye dayalıdır.

</details>

<details>

<summary>Chloros, Linux için kullanılabilir mi?</summary>

Evet! Chloros 1.2.0, Linux amd64 (x86_64) ve arm64 (NVIDIA Jetson JetPack 6) sürümlerini destekler. CLI ve Python SDK, canlı LATTICE kamera ve DAQ sensör kontrolü dahil olmak üzere Linux üzerinde tam olarak desteklenmektedir. Linux için bir GUI bulunmamaktadır — tüm etkileşim [CLI](CLI.md) veya [Python SDK](api-python-sdk.md) aracılığıyla gerçekleşir. Ayrıntılar için [Linux Genel Bakış](linux/linux-overview.md) bölümüne bakınız.

</details>

<details>

<summary>Chloros&#x27;i NVIDIA Jetson üzerinde çalıştırabilir miyim?</summary>

Evet! Chloros, JetPack 6 çalıştıran Jetson Nano, Orin Nano, Orin NX ve AGX Orin dahil olmak üzere NVIDIA Jetson platformlarını destekler. Chloros, Jetson modelinizi otomatik olarak algılar ve işleme stratejisini optimize eder. Kurulum ve dağıtım talimatları için [NVIDIA Jetson Kılavuzu](linux/nvidia-jetson-guide.md) bölümüne bakın.

</details>

<details>

<summary>Chloros, donanımım için otomatik olarak optimizasyon yapar mı?</summary>

Evet! Chloros, CPU&#x27;nuzu, GPU&#x27;nuzu, RAM&#x27;inizi ve (Jetson&#x27;da) termal sensörlerinizi otomatik olarak algılayan [Dinamik Hesaplama Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) özelliğini içerir. Ardından, yüksek bellekli sistemlerde `GPU_PARALLEL`&#x27;ten, kısıtlı cihazlarda `GPU_SINGLE`&#x27;e ve NVIDIA GPU&#x27;su olmayan sistemlerde `CPU_PARALLEL`&#x27;e kadar en uygun işleme stratejisini seçer. Manuel yapılandırma gerekmez.

</details>

<details>

<summary>4 iş parçacıklı işleme boru hattı nedir?</summary>

Chloros, Chloros+ kullanıcıları için 4 iş parçacıklı bir ardışık mimari kullanır: İş parçacığı 1 (Algılama) görüntüleri yükler ve kalibrasyon hedeflerini algılar, İş parçacığı 2 (Kalibrasyon) yansıma kalibrasyonunu hesaplar, İş parçacığı 3 (İşleme) GPU hızlandırmalı debayering ve indeks hesaplamasını gerçekleştirir ve İş parçacığı 4 (Dışa Aktarma) çıktı dosyalarını yazar. Maksimum verim için birden fazla görüntü aynı anda farklı iş parçacıklarında işlenebilir. Ayrıntılar için [İşleme Boru Hattı](processing-architecture/processing-pipeline.md) bölümüne bakın.

</details>

<details>

<summary>Chloros kurulumumda tanılama işlemlerini nasıl çalıştırabilirim?</summary>

`selftest` komutunu kullanarak 7 adımlı bir smoke testi çalıştırın: sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, API bağlantı durumu (`/api/test`), sistem bilgileri (`/api/system-info` — GPU/CUDA/PyTorch), gürültü giderici modelin varlığı ve CUDA + gürültü giderici hazırlığı:

```bash
chloros-cli selftest
```

Bu, özellikle Linux/Jetson sistemlerinde GPU ve CUDA kurulumunu doğrulamak için kullanışlıdır.

</details>
