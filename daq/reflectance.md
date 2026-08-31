# Yansıma Değeri İş Akışları

Bir DAQ ışık sensörü, radyometrik görüntüleri yansıma değerine dönüştürür. İki farklı iş akışı vardır:

1. **Tek sensör** — bir DAQ, kamera çekim yaparken aşağıya doğru ışık şiddetini ölçer ve Chloros, kamera parlaklığını bu referans değere böler.
2. **Çift sensör** — biri gökyüzünü, diğeri bir nesneyi izleyen iki DAQ sensörü, kamera kullanılmadan canlı bir spektral yansıma eğrisi oluşturur.

## Tek sensör + kamera (aşağı doğru ışınım referansı)

DAQ, aşağı doğru ışık sensörü (DLS) olarak işlev görür: kamera yukarı doğru yayılan parlaklığı **L**(W/m²/sr/nm) ölçer, DAQ aşağı doğru ışık şiddetini**E** (W/m²/nm) ölçer ve Chloros, her bant için yansıtma oranını şu şekilde hesaplar:

> ρ = π · L / E

DAQ okuması her zaman **pozlama zaman damgasıyla eşleştirilir** — bu nedenle DAQ ve kameralar, PTP ile senkronize edilmiş bir saati paylaşır (bkz. [DAQ-E Ağ ve Zaman Senkronizasyonu](ethernet-ptp.md)). Dış mekan çalışmaları için Sunshine kosinüs başlığını takın ve doğru şekilde tanımlayın; başlık tanımı, E değerini doğrudan ölçeklendirir (bkz. [Başlık Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md)). Nicel çalışmalar için cihazın karakteristiğini unutmayın: nicel ışık şiddeti, en az 15 saniyelik okumaların ortalamasından elde edilir.

### Canlı yakalama

DAQ&#x27;ı Kameralar sekmesinden bir kameraya bağlayın: her kameranın ayarlar panelinde, Işık Sensörleri sekmesinden bağlı tüm DAQ&#x27;ları (DAQ-U/M/E) listeleyen bir **Işık Sensörü** açılır menüsü bulunur; senkronize bir dizi için, dizi genelinde yapılan bir Işık Sensörü seçimi her üyeye yayılır (tek tek kameralar yine de bu ayarı geçersiz kılabilir). Bağlandıktan sonra, sensörün spektrumları kameranın DLS yuvasına beslenir ve yansıma dışa aktarımları eşleşen okuma değerine bölünür.

<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->

Bilinmesi gereken iki davranış:

* **Bağlı DAQ yok → yansıma reddedilir, sahte değer üretilmez.** Chloros, yansıma sonucunu reddeder ve daha düşük bir sonucu sessizce geri vermek yerine atlama nedenini kaydeder.
* **Kullanılan okuma değeri korunur.** Her yansıma karesi için, fiilen uygulanan DAQ okuma değeri, görüntünün yanına bir `.daq` ek dosyası olarak yazılır; böylece çekim daha sonra yeniden işlenebilir ([Kayıt ve .daq Biçimi](recording.md)).

### Kaydedilen görüntilerin işlenmesi

Uçuş sonrası işleme için, oturum sırasında bir `.daq` kaydı yapın ve bunu görüntünün yanında saklayın — işleme akışı, zaman damgası eşleşen aşağı doğru ışınlamayı otomatik olarak çözer ve ilk kullanımda eksik fabrika kalibrasyonlarını MAPIR&#x27;in bulutundan alır. GUI kayıtları, durduklarında açık projeye otomatik olarak eklenir.

Yansıtma referansı işleme sırasında seçilebilir — `--reflectance-source` üzerinde `chloros-cli process` veya GUI’deki Proje Ayarları’ndaki yansıtma kaynağı ayarı:

| Değer | Davranış |
| --- | --- |
| `auto` (varsayılan) | Kalite kontrolünden geçen çerçeve içi kalibrasyon hedefi mutlak referanstır; DAQ aşağı doğru ışınımı (ρ = π·L/E) yedek referanstır |
| `daq` | DAQ&#x27;ya göre geçerlidir |
| `target` | Kesin çerçeve içi hedef; DAQ ikamesi yoktur |

Hedef iş akışları için [Kalibrasyon Hedefleri](../calibration-targets.md) bölümüne ve [LATTICE bölümü](../lattice/README.md) ve tam işleme boru hattı için [CLI Referansı](../reference/cli-reference.md) bölümlerine bakınız. Dışa aktarılan yansıma piksellerini okurken, damgalı ölçeği kullanın (LATTICE: 32768 = ρ 1,0, XMP `Chloros:PixelScale`; Survey3: 65535) kullanın — bkz. [Çıkış Görüntü Biçimleri](../output-image-formats.md).

### DAQ&#x27;nın kalibre edilmiş aralığı dışındaki bantlar

DAQ&#x27;nın radyometrik olarak kalibre edilmiş aralığı ~374–974 nm&#x27;dir. Chloros, spektral ağırlığının yarısından azı bu aralıkta bulunan herhangi bir kamera bandı için DAQ tabanlı yansıma değerini reddeder ve atlama nedeni olarak `dls-uncalibrated-band-<nm>`&#x27;i bildirir. Satışta olan SKU&#x27;lar arasında bu durum yalnızca F988&#x27;i etkiler: F988 yansıma değeri, sahne içi bir yansıma paneli kullanılarak kalibre edilir; bant, DAQ ışık sensörünün kalibre edilmiş aralığının dışındadır, bu nedenle Chloros, en son panel yakalama verinizi kullanır ve panel gözlemleri arasında bu değeri korur. Bir F988 kamera yalnızca DAQ modunda çalıştırılırsa, Chloros, o bant için DAQ tabanlı yansıma değerini, atlama nedeni `dls-uncalibrated-band-988` olarak reddediyor — panel iş akışı desteklenen yoldur.

## Çift sensör (ortam + nesne)

Herhangi bir aktarım yönteminde, herhangi bir çift halinde kullanılan iki DAQ sensörü, kamera olmadan canlı bir yansıma spektrumu sağlar: bir sensör gökyüzüne (**Ortam Işık Kaynağı**), diğeri ise nesneye (**Nesne Tarayıcısı**) yönelir ve Chloros, dalga boyu başına şu hesaplamayı yapar:

> R(λ) = nesne(λ) / ortam(λ)

(ortam ≤ 0 olduğunda sıfır).

### GUI&#x27;de

Her iki sensör de Işık Sensörleri sekmesine bağlıyken, sensör ekleme katmanını açın (ızgara görünümündeki grafik kutucuğundaki &quot;+&quot; düğmesi) ve **Ortam + Nesne Birleştir**seçeneğini seçin. Ortam Işık Kaynağı ve Nesne Tarayıcı açılır menülerinden iki sensörü seçin ve Oluştur&#x27;a tıklayın. Grup, kendi grafiği olarak ve yeşil**REF** rozetine sahip bir kenar çubuğu satırı olarak görünür.

<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->

Yansıma grafiğinin (liste görünümü) altında, canlı bir **bitki örtüsü indeksi tablosu**, mavi 450 / yeşil 550 / kırmızı 670 / NIR 800 nm bant merkezlerini kullanarak eğriden indeksleri hesaplar. Mutlak ölçeği ortadan kaldıran oran tabanlı indeksler (NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR) her zaman gösterilir; mutlak yansıma değeri gerektiren indeksler (EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) ise yalnızca her iki sensörün de güç kalibreli modeller olması durumunda görünür.

### Görünür ve Göreceli — etiketleme kuralı

Chloros, çift sensör çıkışını sensör çiftinin gerçekte sunabildiği değere göre etiketler:

| Sensör çifti | Etiket |
| --- | --- |
| Her iki sensör de kalibre edilmiş — fabrika kalibrasyon paketi yüklenmiş | **Görünür Yansıtma** |
| Herhangi bir sensör kalibre edilmemiş | **Bağıl Yansıtma** |

Üç modelin tümü radyometriktir: bir sensörün fabrika kalibrasyon paketi yüklendiğinde spektrumları mutlak W/m²/nm cinsindendir; dolayısıyla kalibre edilmiş bir sensör çifti, mutlak bir görünür yansıtma değerine oranlanır — bu değer, aktarım tarafından belirlenmez. Hala ham sayım verilerini aktaran bir sensör (pakete erişilemiyorsa), sonucu göreceli bir eğriye indirger (spektral şekil yine de geçerlidir). Her iki sensör de doğru şekilde tanımlanmış sınırlara sahip olmalıdır ([Sınır Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md)).

### Python&#x27;ten

Birleştirilmiş SDK yüzeyinde özel bir çift sensör çağrısı yoktur: `chloros_sdk.connect_daq_sensor()` ile iki oturum açın ve aynı etiketleme kuralını uygulayarak `latest()` spektrumlarını kendiniz karşılaştırın. (MAPIR&#x27;in dahili doğrudan donanım yüzeyinde de bir çift sensör kayıt aracı mevcuttur; bu, eksiksizlik amacıyla [CLI Referansı](../reference/cli-reference.md) — bu araç, sevk edilen CLI’in bir parçası değildir; yukarıdaki GUI iş akışı desteklenen canlı yoldur.)
