---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# SSS

<details>

<summary>Chloros ile MAPIR markası olmayan kameralardan gelen görüntüleri işleyebilir miyim?</summary>

Hayır, Chloros yalnızca MAPIR kamera görüntülerini (Survey3 ve LATTICE serileri) işleyebilir. Daha fazla bilgi için lütfen [desteklenen kamera modelleri](supported-cameras.md) listesine bakın. MAPIR Cloud'da diğer kameraların görüntülerini de işliyoruz; tam listeye [buradan](https://mapir.gitbook.io/mapir-cloud/supported-cameras) ulaşabilirsiniz.

</details>

<details>

<summary>Chloros, LATTICE kameralarını destekliyor mu?</summary>

Evet. Chloros 1.3.0, LATTICE M3C ve M3M kamera modüllerini uçtan uca destekler: **canlı kontrol**— PTP zaman senkronizasyonuna sahip senkronize çoklu kamera dizileri dahil olmak üzere, GUI’deki Kameralar sekmesinden (`chloros-cli lattice`) veya Python SDK’den kameraları keşfedin, bağlayın, önizleyin ve görüntü yakalayın — ve yakalanan görüntülerin**tam radyometrik işlenmesi** (ham → debayering → parlaklık → yansıma → indeks). [Desteklenen Kameralar](supported-cameras.md) ve [LATTICE kılavuzu](lattice/README.md) bölümlerine bakın.

</details>

<details>

<summary>Kalibrasyon hedefi olmadan görüntülerimi yansıma açısından kalibre edebilir miyim?</summary>

**Survey3:** Hayır. Hedef olmayan görüntüler çekilirken aynı anda çekilmiş bir kalibrasyon hedefi görüntüsü olmadan, görüntünün piksel değerlerini bilinen bir yansıma yüzdesiyle ilişkilendiremezsiniz. Ayrıca, bir MAPIR ışık sensöründen alınan kayıtları da eklemezseniz, ortam ışığı spektrumu ölçülmeyecek ve yansıma sonuçları doğru olmayacaktır.

**LATTICE:** Evet. Yansıma, bir panel yerine bir DAQ ışık sensörü tarafından ölçülen aşağıya doğru ışınım yoğunluğuna göre belirlenebilir (ρ = π·L/E). QA'dan geçen bir çerçeve içi hedef *mevcut* olduğunda, hedef ve DAQ zincirleri ölçülen puan konusunda rekabet halindeyken, bir bayrakla geçmediği sürece varsayılan olarak mutlak referans haline gelir (`--reflectance-source auto`). Tek istisna: "F988 yansıma oranı, sahnedeki bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesindedir, bu nedenle Chloros, en son panel yakalamanızı uygular ve panel gözlemleri arasında bunu korur." Bkz. [Kalibrasyon Hedefleri](calibration-targets.md).

</details>

<details>

<summary>DAQ ışık sensörüne ihtiyacım var mı?</summary>

Radyans için gerekmez: LATTICE radyans dışa aktarımları, her kameranın fabrika radyometrik kalibrasyonundan elde edilir ve ne bir DAQ sensörüne ne de bir hedefe ihtiyaç duyar. **Yansıma**için ortam ışığına ilişkin bir referansa ihtiyacınız vardır — bu, bir DAQ ışık sensörünün aşağı doğru ışık ölçümü veya çerçeve içi bir kalibrasyon hedefi olabilir. Bir DAQ sensörü,**sahneye herhangi bir panel yerleştirmeden** kalibre edilmiş yansıma değerleri elde etmenizi sağlar. Kaydedilen `.daq` dosyaları, zaman damgası aracılığıyla görüntülerinizle otomatik olarak eşleştirilir. [Kalibrasyon Hedefleri](calibration-targets.md) ve [CLI Referansı](reference/cli-reference.md) bölümlerine bakın.

</details>

<details>

<summary>Chloros'u bir AI asistanıyla (Claude, ChatGPT vb.) kullanabilir miyim?</summary>

Evet — bu kılavuz ve CLI/SDK bunun için tasarlanmıştır:

* AI asistanlarının her sayfayı bulabilmesi için kılavuzun tam dizinine `https://mapir.gitbook.io/chloros/llms.txt` adresinden erişilebilir.
* Her sayfanın ham markdown kodu, sayfanın küçük harfli URL'sine `.md` eklenmiş haliyle (örneğin `https://mapir.gitbook.io/chloros/reference/cli-reference.md`) erişilebilir.
* [CLI Referansı](reference/cli-reference.md) ve [SDK Referansı](reference/sdk-reference.md), LLM'ler tarafından kullanılmak üzere yazılmıştır: kesin bayraklar, varsayılan ayarlar, çıkış semantiği ve kopyala-yapıştır edilebilir komutlar.

Asistanınızı Chloros'a nasıl yönlendireceğiniz konusunda [AI Asistanları](ai-assistants.md) bölümüne bakın.

</details>

<details>

<summary>İşlenmiş çıktı dosyalarım nereye gider?</summary>

Çıktılar, proje klasörü altında, önce kameraya göre, ardından dosya formatına göre gruplandırılarak kaydedilir:

```
<project>/<camera-folder>/<format-folder>/<Product>_Images/
```

* **kamera-klasörü** — LATTICE için `LATT-<sensor>-<lens>-F<filter>`, Survey3 için `<model>_<filter>` (örn. `Survey3N_RGN`)
* **format-klasörü** — `tiff16`, `tiff8`, `png8`, `jpg8` veya `tiff32`
* **ürün klasörleri** — `Reflectance_Calibrated_Images/`, `Debayered_Images/`, `Preview_Images/`, `Radiance_Images/` (her zaman `tiff32` altında), `<INDEX>_Index_Images/`
* **`clamp_masks/`** — Yalnızca LATTICE multispektral: ürün klasörlerinin yanında piksel başına maskeler, aşağıya bakınız**Dışa aktarılan dosyalar kaynak dosyanın adını korur — ürünü tanımlayan klasördür, dosya adı son eki değildir.**CLI ile, `-o` parametresini belirtmediğiniz sürece proje klasörü varsayılan proje klasörünüzün altında oluşturulur (değiştirmediyseniz `Documents/MAPIR/Chloros Projects`); göreceli bir `-o` değeri, komutu çalıştırdığınız klasöre göre belirlenir. Ürün talep eden ancak hiçbir şey yazmayan bir `chloros-cli process` çalıştırması, `Processing finished but wrote no image products.` çıktısını verir ve**sıfırdan farklı bir değerle sonlanır**; böylece komut dosyaları bunu algılayabilir. [Çıktı Görüntü Biçimleri](output-image-formats.md) ve [CLI Referansı](reference/cli-reference.md) bölümlerine bakın.

</details>

<details>

<summary>clamp_masks klasörleri ve *_clampmask.tif dosyaları nedir?</summary>

1.3.0 sürümünde yeni eklenen özellik. Çok spektral LATTICE M3C kameranın parlaklık ve yansıma ürünleri, 3×3 bantlı bir ayrıştırma işlemi gerçekleştirir ve bir bantta negatif bir değer üretildiğinde, Chloros bu değeri sıfıra kırpar. En az bir kırpılmış piksel içeren bir ürün, hangi piksellerin ve bantların kırpıldığını belirten tek bantlı bir `uint8` maskesi alır (bit 0 = ürünün R kanalı, bit 1 = G, bit 2 = B). Maskeler, ürün klasörlerinin **yanındaki** bir `clamp_masks/` klasörüne kaydedilir — örneğin `tiff16/clamp_masks/Reflectance_Calibrated_Images/<name>_clampmask.tif` — ve ürünün `Chloros:UnmixClampMask` XMP etiketi, maskesinin adını belirtir (veya `none` yazılır).

Bunlar sahnenin görüntüleri değildir: Chloros’un kendi içe aktarma işlemi bunları atlar ve hiçbir zaman bir ürün klasörünün içinde bulunmadıkları için bir ürün klasörünü olduğu gibi Pix4D veya Metashape’e aktarabilirsiniz. Bkz. [Kelepçe maskeleri](output-image-formats.md#clamp-masks).

</details>

<details>

<summary>Chloros neden "Proje klasörü bu bilgisayar için çok derin" uyarısını veriyor?</summary>

Bu durum yalnızca Windows'ta geçerlidir. Windows, uzun yollar devre dışı bırakılmış olarak gelir ve hiçbir dosya yolu 260 karakterden uzun olamaz. Chloros ürünleri, proje klasörünün yaklaşık 130 karakter aşağısında yer alır; bu nedenle Chloros, işleme başlamadan önce daha derin bir proje klasörünü reddeder — 1.3.0 sürümünden önce olabileceği gibi, hiçbir şey yazmadan çalışmak yerine. Mesaj, sığabilecek en uzun proje klasörünü gösterir.

Bu sorunu şu iki yoldan biriyle çözebilirsiniz:

* Daha kısa bir proje klasörü kullanın (Proje Ayarları'ndaki **Çalışma Dizini** veya CLI için `-o` / `chloros-cli set-project-folder`).
* Windows uzun yol özelliğini etkinleştirin ve Chloros'u yeniden başlatın. Chloros yükleyicisindeki **Windows uzun yollarını etkinleştir** seçeneği (varsayılan olarak işaretlidir) bunu sağlar; bu seçeneğin işaretini kaldırdıysanız yükleyiciyi yeniden çalıştırın.

[İşlemeyi Başlatma](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer) bölümüne bakın.

</details>

<details>

<summary>Chloros'ta işleme başlamadan önce görüntülerimi düzenleyebilir miyim?</summary>

Hayır. Chloros, girdi verilerinin değiştirilmediğini varsayar. Dosya adlarını değiştirmeyin.

</details>

<details>

<summary>MAPIR Survey3 kameralarımı otomatik pozlamaya ayarlayıp görüntüleri Chloros'ta işleyebilir miyim?</summary>

Hayır. Survey3 görüntü veri kümelerinin sabit/kilitli bir pozlamaya sahip olması gerekir; bu nedenle otomatik enstantane hızı veya otomatik ISO kullanılamaz. Aynı kamera modeline ait tüm görüntülerde enstantane hızı ve ISO (pozlama) değerleri aynı olmalıdır.

LATTICE kameralarında bu kısıtlama yoktur: Chloros, pozlamayı canlı olarak kontrol eder (Smart AE) ve her çekimde gerçekte kullanılan pozlama ve kazanç kaydedilir; radyometrik işleme süreci bu değerleri hesaba katar.

</details>

<details>

<summary>Chloros, ortomozaik görüntüleri işleyebilir veya analiz edebilir mi?</summary>

Hayır. Yalnızca tek tek MAPIR kamera görüntüleri desteklenir; ortomozaik harita gibi birleştirilmiş görüntüler desteklenmez.

</details>

<details>

<summary>Chloros'un hedef algılama adımını nasıl hızlandırabilirim?</summary>

Dosya tarayıcı tablosunda sağ sütunda hedef görüntüleri önceden seçmek, Chloros'a kalibrasyon hedeflerini yalnızca bu görüntülerde aramasını söyler ve işlemeyi büyük ölçüde hızlandırır.

</details>

<details>

<summary>Görüntülerimi <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud</a>'a yükleyeceksem, yüklemeden önce Chloros'ta işlem yapmalı mıyım?</summary>

Çevrimiçi işleme platformumuz [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription)'a yüklemeyi planlıyorsanız, yüklemeden önce görüntüleri düzenlemeyin. Cloud, aynı işlemleri ve daha fazlasını gerçekleştirecektir.

</details>

<details>

<summary>MAPIR, X özelliğini destekleyecek mi? MAPIR'ın X özelliğini sunmasını gerçekten çok isterim.</summary>

Ürünlerimizle ilgili geri bildirimleri her zaman memnuniyetle karşılıyoruz. Ürünlerimizle ilgili bir sorunla karşılaşırsanız veya ürünlerimizi nasıl iyileştirebileceğimize dair bir öneriniz varsa, lütfen [BİZE ULAŞIN](https://www.mapir.camera/community/contact) ve düşüncelerinizi bizimle paylaşın. Ar-Ge çalışmalarımızın çoğu, müşterilerimizin en önemli ihtiyaçlarını dinlemeye dayalıdır.

</details>

<details>

<summary>Chloros, Linux için kullanılabilir mi?</summary>

Evet! Chloros 1.3.0, `.deb` paketleri aracılığıyla Linux amd64 (x86\_64) ve arm64 (NVIDIA Jetson JetPack 6) platformlarını desteklemektedir. CLI ve Python SDK, canlı LATTICE kamera ve DAQ sensör kontrolü dahil olmak üzere Linux'te tam olarak desteklenmektedir. Linux için bir GUI bulunmamaktadır — tüm etkileşim [CLI](CLI.md) veya [Python SDK](api-python-sdk.md) aracılığıyla gerçekleşir. Ayrıntılar için [Linux Genel Bakış](linux/linux-overview.md) bölümüne bakın.

</details>

<details>

<summary>Chloros'u NVIDIA Jetson üzerinde çalıştırabilir miyim?</summary>

Evet! Chloros, JetPack 6 çalıştıran NVIDIA Jetson **Orin ailesini** — Orin Nano, Orin NX ve AGX Orin — destekler. Eski modeller (Jetson Nano, TX2, Xavier NX) JetPack 6'yı çalıştıramaz ve mevcut paket tarafından desteklenmez. Chloros, Jetson modelinizi otomatik olarak algılar ve işleme stratejisini optimize eder. Jetson paketi, JetPack 6’nın CUDA sürümündeki PyTorch’u kullanır; bu sayede işlemler Jetson’un GPU’su üzerinde gerçekleştirilir. Kurulum ve dağıtım talimatları için [NVIDIA Jetson Kılavuzu](linux/nvidia-jetson-guide.md) sayfasına bakın.

</details>

<details>

<summary>Chloros, donanımım için otomatik olarak optimizasyon yapar mı?</summary>

Evet! Chloros, CPU'nuzu, GPU'nuzu, RAM'inizi ve (Jetson'da) termal sensörlerinizi otomatik olarak algılayan [Dinamik İşlem Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) özelliğini içerir. Ardından, yüksek bellekli GPU'larda `GPU_PARALLEL`'ten daha küçük GPU'larda `GPU_SINGLE`'e kadar en uygun işleme stratejisini seçer — yüksek bellekli GPU'larda `GPU_PARALLEL`'ten, daha küçük GPU'larda `GPU_SINGLE`'e ve PyTorch'un kullanabileceği bir CUDA GPU'su olmayan sistemlerde `CPU_PARALLEL`'e kadar. Manuel yapılandırma gerekmez.

</details>

<details>

<summary>4 iş parçacıklı işleme boru hattı nedir?</summary>

Chloros, Chloros+ kullanıcıları için 4 iş parçacıklı bir ardışık mimari kullanır: İş parçacığı 1 (Algılama) görüntüleri yükler ve kalibrasyon hedeflerini algılar; İş parçacığı 2 (Kalibrasyon) yansıma kalibrasyonunu hesaplar; İş parçacığı 3 (İşleme) GPU hızlandırmalı debayering ve indeks hesaplamasını gerçekleştirir; İş parçacığı 4 (Dışa Aktarma) ise çıktı dosyalarını yazar. Maksimum verim için birden fazla görüntü aynı anda farklı iş parçacıklarında bulunabilir. Ayrıntılar için [İşleme Boru Hattı](processing-architecture/processing-pipeline.md) bölümüne bakın.

</details>

<details>

<summary>Chloros kurulumumda tanılama işlemini nasıl çalıştırabilirim?</summary>

`selftest` komutunu kullanarak 7 adımlı bir temel test (smoke test) çalıştırın: sürüm, bağlantı noktası kullanılabilirliği, arka uç başlatma, API bağlantısı (`/api/test`), sistem bilgileri (`/api/system-info` — GPU/CUDA/PyTorch), gürültü giderici modelin varlığı ve CUDA + gürültü giderici hazırlığı:

```bash
chloros-cli selftest
```

Bu, özellikle Linux sistemlerinde GPU ve CUDA kurulumunu doğrulamak için kullanışlıdır. Jetson'da, `CUDA: False` genellikle JetPack 6'nın CUDA ve cuDNN kütüphanelerinin eksik olduğu anlamına gelir: bunları `sudo apt install nvidia-jetpack` komutuyla yükleyin.

</details>
