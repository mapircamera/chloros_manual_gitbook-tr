# Yansıma İş Akışları

Bir DAQ ışık sensörü, radyometrik görüntüleri yansıma değerlerine dönüştürür. İki farklı iş akışı vardır:

1. **Tek sensör** — bir DAQ, kamera çekim yaparken aşağıya doğru ışık şiddetini ölçer ve Chloros, kamera parlaklığını bu referans değere böler.
2. **Çift sensör** — biri gökyüzünü, diğeri bir nesneyi izleyen iki DAQ sensörü, kamera kullanılmadan canlı bir spektral yansıma eğrisi oluşturur.

## Tek sensör + kamera (aşağı doğru ışınım referansı)

DAQ, aşağı doğru ışık sensörü (DLS) olarak işlev görür: kamera yukarı doğru yayılan parlaklığı **L**(W/m²/sr/nm) ölçer, DAQ aşağı doğru ışık şiddetini**E** (W/m²/nm) ölçer ve Chloros, bant başına yansıma oranını şu şekilde hesaplar:

> ρ = π · L / E

DAQ okuması her zaman **pozlama zaman damgasıyla eşleştirilir** — bu nedenle DAQ ve kameralar PTP ile senkronize edilmiş bir saati paylaşır (bkz. [DAQ-E Ağ ve Zaman Senkronizasyonu](ethernet-ptp.md)). Dış mekan çalışmaları için Sunshine kosinüs sınırını ayarlayın ve doğru şekilde tanımlayın; sınır tanımı, E değerini doğrudan ölçeklendirir (bkz. [Sınır Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md)). Nicel çalışmalar için cihazın karakteristiğini unutmayın: nicel ışık şiddeti, en az 15 saniyelik okumaların ortalamasından elde edilir.

### Canlı yakalama

DAQ'ı Kameralar sekmesinden bir kameraya bağlayın: her kameranın ayarlar panelinde, Işık Sensörleri sekmesinden bağlı tüm DAQ'ları (DAQ-U/M/E) listeleyen bir **Işık Sensörü** açılır menüsü bulunur; senkronize bir dizi için, dizi genelinde yapılan Işık Sensörü seçimi her üyeye yayılır (tek tek kameralar yine de bu ayarı geçersiz kılabilir). Bağlandıktan sonra, sensörün spektrumları kameranın DLS yuvasına beslenir ve yansıma dışa aktarımları eşleşen okuma değerine bölünür. Bir dizide, kendi sensörüne sahip bir üye kendi okumasını kullanır — canlı kompozit içindeki bantları, dışa aktarımları ve otomatik pozlaması için — diğer üyeler ise dizinin okumasını kullanır.



<!-- SCREENSHOT-NEEDED: Cameras tab per-camera settings panel showing the "Light Sensor" dropdown open, with a connected DAQ sensor listed and selected. -->
Bilmeniz gereken iki davranış:

* **DAQ bağlanmamışsa → yansıma reddedilir, sahte değer üretilmez.** Chloros, yansıma sonucunu reddeder ve daha düşük bir sonucu sessizce geri vermek yerine atlama nedenini kaydeder.
* **Kullanılan okuma değeri korunur.** Her yansıma çerçevesi için, fiilen uygulanan DAQ okuma değeri, yakalamaların `daq/` klasörüne bir `.daq` dosyası olarak yazılır; böylece yakalama daha sonra yeniden işlenebilir ([Kayıt ve .daq Biçimi](recording.md)). Yanındaki bir `.daq.cameras.json` dosyası, tanımladığı kameraların adlarını belirtir ve yeniden işleme işlemi bu dosyayı yalnızca söz konusu kameraların görüntüleriyle eşleştirir.

### Kaydedilen Görüntülerin İşlenmesi

Uçuş sonrası işleme için, oturum sırasında bir `.daq` kaydı yapın ve bunu görüntülerle birlikte saklayın — işleme akışı, zaman damgası eşleşen aşağı doğru ışınımı otomatik olarak çözümler ve ilk kullanımda MAPIR'in bulutundan eksik fabrika kalibrasyonlarını alır. GUI kayıtları, durduklarında açık projeye otomatik olarak eklenir.

Yansıtma referansı, işleme sırasında seçilebilir — `--reflectance-source` üzerinde `chloros-cli process` veya GUI’deki Proje Ayarları’ndaki yansıtma kaynağı ayarı:

| Değer | Davranış |
| --- | --- |
| `auto` (varsayılan) | Kalite kontrolünden geçen çerçeve içi kalibrasyon hedefi mutlak referanstır; DAQ aşağı doğru ışınlama (ρ = π·L/E) yedek seçenektir. Bir bayrakla (düşen bir panel veya arızalı bir bant) QA'yı geçen bir hedef otomatik olarak tercih edilmez: hedef, hedef+DAQ ve yalnızca DAQ zincirleri, ayrılmış panellerde ölçülen puanlarına göre sırayla denenir. |
| `daq` | DAQ yetkili |
| `target` | Katı çerçeve içi hedef; DAQ ikamesi yok |

Hedef iş akışları için [Kalibrasyon Hedefleri](../calibration-targets.md) bölümüne ve [LATTICE bölümü](../lattice/README.md) ile [CLI Referansı](../reference/cli-reference.md) bölümlerine bakınız. Dışa aktarılan yansıma piksellerini okurken, damgalı ölçeği kullanın (LATTICE: 32768 = ρ 1,0, XMP `Chloros:PixelScale`; Survey3: 65535) kullanın — bkz. [Çıktı Görüntü Biçimleri](../output-image-formats.md).

### DAQ'nın kalibre edilmiş aralığı dışındaki bantlar

DAQ'ın radyometrik olarak kalibre edilmiş aralığı ~374–974 nm'dir. Chloros, spektral ağırlığının yarısından azı bu aralıkta bulunan herhangi bir kamera bandı için DAQ tabanlı yansıma değerlerini reddeder ve atlama nedeni olarak `dls-uncalibrated-band-<nm>`'i bildirir. Satışta olan ürün kodları arasında bu durum yalnızca F988'i etkiler: F988'in yansıma değeri, sahne içi yansıma paneli kullanılarak kalibre edilir; bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros en son panel yakalama verinizi uygular ve panel gözlemleri arasında bu değeri korur. Bir F988 kamerası yalnızca DAQ modunda çalıştırılırsa, Chloros o bant için DAQ tabanlı yansıma değerini `dls-uncalibrated-band-988` atlama nedeni ile reddeder — panel iş akışı desteklenen yoldur.

## Çift sensör (ortam + nesne)

İki DAQ sensörü — herhangi bir çift, herhangi bir aktarım yolunda — kamera olmadan canlı bir yansıma spektrumu sağlar: bir sensör gökyüzüne (**Ortam Işık Kaynağı**), diğeri nesneye (**Nesne Tarayıcısı**) yönelir ve Chloros, dalga boyu başına şu hesaplamayı yapar:

> R(λ) = nesne(λ) / ortam(λ)

(ortam ≤ 0 olduğunda sıfır).

### GUI'de

Her iki sensör de Işık Sensörleri sekmesine bağlandıktan sonra, sensör ekleme katmanını açın (ızgara görünümündeki bir grafik kutucuğundaki "+" düğmesi) ve **Ortam + Nesne'yi Birleştir**seçeneğini seçin. Ortam Işık Kaynağı ve Nesne Tarayıcı açılır menülerinden iki sensörü seçin ve Oluştur'a tıklayın. Grup, kendi grafiği olarak ve yeşil**REF** rozetine sahip bir kenar çubuğu satırı olarak görünür.



<!-- SCREENSHOT-NEEDED: The add-sensor overlay's "Combine Ambient + Object" panel with two connected DAQ sensors selected in the Ambient Light Source and Object Scanner dropdowns, Create button enabled. -->

<!-- SCREENSHOT-NEEDED: A live Apparent Reflectance chart from an Ambient+Object DAQ pair in list view, with the vegetation-index table visible below the chart (NDVI etc. showing live values). -->
Yansıma grafiğinin altında (liste görünümü), canlı bir **bitki örtüsü indeksi tablosu**, mavi 450 / yeşil 550 / kırmızı 670 / NIR 800 nm bant merkezlerini kullanarak eğriden indeksleri hesaplar. Mutlak ölçeği ortadan kaldıran oran tabanlı indeksler (NDVI, GNDVI, ENDVI, WDRVI, GRVI, CVI, GCI, MSR) her zaman gösterilir; mutlak yansıma gerektiren indeksler (EVI, SAVI, OSAVI, GSAVI, GOSAVI, MSAVI2, RDVI, TDVI, LAI, NLI, MNLI, FCI, GEMI) ise yalnızca her iki sensörün de güç kalibreli modeller olması durumunda görünür.

### Görünür Yansıtma ve Göreceli Yansıtma — etiketleme kuralı

Chloros, çift sensör çıktısını sensör çiftinin gerçekte sağlayabildiği değere göre etiketler:

| Sensör çifti | Etiket |
| --- | --- |
| Her iki sensör de kalibre edilmiş — fabrika kalibrasyon paketi yüklenmiş | **Görünür Yansıtma** |
| Herhangi bir sensör kalibre edilmemiş | **Göreceli Yansıtma** |

Üç modelin tümü radyometrik niteliktedir: Bir sensörün fabrika kalibrasyon paketi yüklendiğinde spektrumları mutlak W/m²/nm değerindedir; dolayısıyla kalibre edilmiş bir sensör çifti, mutlak bir görünür yansıtma değerine oranlanır — bu değer, aktarım tarafından belirlenmez. Hala ham sayım verisi aktaran bir sensör (pakete erişilemiyorsa), sonucu göreceli bir eğriye indirger (spektral şekil yine de geçerlidir). Her iki sensör de doğru şekilde tanımlanmış sınırlara sahip olmalıdır ([Sınır Profilleri ve Kalibre Edilmiş Aralık](caps-and-range.md)).

### Python'dan

Birleştirilmiş SDK arayüzünde özel bir çift sensör çağrısı yoktur: `chloros_sdk.connect_daq_sensor()` ile iki oturum açın ve aynı etiketleme kuralını uygulayarak `latest()` spektrumlarını kendiniz oranlayın. (MAPIR'in dahili doğrudan donanım arayüzünde de bir çift sensör kayıt aracı mevcuttur; eksiksizlik amacıyla [CLI Referansı](../reference/cli-reference.md) içinde listelenmiştir — bu, gönderilen CLI'nin bir parçası değildir; yukarıdaki GUI iş akışı desteklenen canlı yoldur.)
