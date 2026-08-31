# Resim Izgarası

Resimleri bir projeye aktardıktan sonra, bunların ana alanda bir ızgara halinde düzenlendiğini göreceksiniz. Izgara, **her resmin hangi sürümünü görüntülediğinizi** seçtiğiniz yerdir — üstündeki düğmeler, her bir küçük resmi aynı anda kaynak dosyalar ile işlenmiş ürünler arasında değiştirir.

## Küçük Resim Boyutu

Görüntü küçük resimlerinin boyutunu ayarlamak için sağ üstteki yakınlaştırma kaydırma çubuğunu kullanın. Kaydırma çubuğu **64 px ile 1200 px** arasında hareket eder.

* **Ctrl + fare tekerleği** de küçük resimleri ölçeklendirir.
* **Ctrl + `+`**/**Ctrl + `=`**ve**Ctrl + `−`** tuşları, her basışta boyutu 4 px artırır. Klavye kısayolu, küçük uçta 64 px&#x27;te, büyük uçta ise mevcut pencerede her satıra tam olarak iki küçük resim sığacak boyutta durur.
* Seçtiğiniz boyut projeyle birlikte kaydedilir (`UI → Grid thumbnail size`, `project.json` içinde; varsayılan: `160`), böylece projeyi yeniden açtığınızda bu ayar geri yüklenir.

<figure><img src="../.gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>Küçük resim *çözünürlüğü*, küçük resim *boyutu*ndan ayrı bir ayardır: [Proje Ayarları](../project-settings/project-settings.md) içindeki **Ekran → Görüntü Küçük Resim Çözünürlüğü** bölümüne bakın (varsayılan olarak uzun kenarda 512 px). Boyut, döşemenin ne kadar büyük çizileceğini gösterir; çözünürlük ise döşemeyi doldurmak için ne kadar ayrıntı alınacağını belirler.***

## Izgara araç çubuğu

Izgara üzerindeki düğme sırası, soldan sağa doğru en fazla üç gruba ayrılır:

1. **Tetikleyici Başına / Kamera Başına** — gruplama modu. Yalnızca LATTICE yakalamaları içeren projelerde görünür.
2. **Kamera filtre düğmeleri** — her LATTICE kamerası için bir tane. Yalnızca Kamera Başına modunda görünür.
3. **Dışa aktarma/görüntüleme modu düğmeleri** — her küçük resmin hangi ürünü gösterdiğini belirler.

Pencere hepsini gösterecek kadar geniş olmadığında, gruplar sağdan sola doğru fareyle üzerine gelindiğinde açılan açılır menülere katlanır: önce dışa aktarma/görüntüleme düğmeleri, ardından kamera düğmeleri katlanır. Katlanan grup, o anda etkin olan seçeneğin adını taşıyan tek bir düğme bırakır ve üzerine fareyle gelindiğinde tüm set aşağı kaydırılır. **Tetikleyici Başına / Kamera Başına seçenekleri asla daraltılmaz.

<!-- SCREENSHOT-NEEDED: Image grid toolbar of a LATTICE array project at full width, showing all three button groups inline: Per Trigger / Per Camera, three camera filter buttons labelled "LATT-M3M (serial)", and the export/view buttons including TIFF, RAW (Original), RAW (Radiance), RAW (Reflectance). -->

*****

## Dışa Aktarma Görüntüleme Düğmeleri

Bu düğmeler, ızgara küçük resimlerini görüntü türleri arasında değiştirir. **Bir düğme, adını taşıyan ürün mevcut olur olmaz görünür** — bu, kaynak dosyalar için işleme sonrasında değil, içe aktarma anında gerçekleşir. Chloros, bir işleme devam ederken projenin ürünlerini yeniden tarar; bu nedenle, her ürün diske kaydedilmeye başladıkça işleme sırasında düğmeler görünür.

### Temel düğme

En soldaki dışa aktarma düğmesi, **gerçekte neyi içe aktardığınız** ile etiketlenir:

| İçe aktardığınız şey | Düğme etiketi |
| --- | --- |
| Survey3 RAW+JPG | `JPG` |
| LATTICE, RAW karenin yanında bir ekran önizlemesi ile çekim yapar | `PNG` veya `TIFF`, önizlemeler hangisiyse |
| Temel dosyanın **RAW**kare**olduğu** LATTICE çekimleri | *düğme yok* — `RAW (Original)` zaten o dosyayı gösterir |

Karışık bir projede etiket, en çok görüntüde kullanılan uzantıyı takip eder.

### Ürün düğmeleri

| Düğme | Neyi gösterir | Ne zaman görünür |
| --- | --- | --- |
| **Hedefler** | Kalibrasyon hedefi tespit edilen görüntüler | Hedeflerin tespit edildiği bir işlemin ardından |
| **Yansıtma** | Kalibre edilmiş yansıtma görüntüleri | Yalnızca Survey3 projeleri — LATTICE projeleri bunun yerine `RAW (Reflectance)` kullanır, bu nedenle ızgarada asla iki yansıtma düğmesi gösterilmez |
| **Beyaz Dengeli** | Beyaz dengesi ayarlanmış ürün (RGB kameralar) | İşlemden sonra |
| **Vinyet Düzeltmeli** | Kalibre edilmemiş, vinyet düzeltmeli yedek | Yansıma kalibrasyonunun uygulanamadığı ve *Vinyet düzeltme* özelliğinin açık olduğu bir çalışmadan sonra |
| **Sensör Tepkisi** | Kalibre edilmemiş sensör tepkisi yedeği | Aynı, ancak *Vinyet düzeltme* kapalıyken |
| **`RAW (<INDEX> Index)`** | Hesaplanan her indeks için bir düğme | İndeksler yapılandırılmış bir çalışmadan sonra |
| **`<INDEX> LUT`** | Renk eşlemeli her indeks için bir düğme | LUT yapılandırılmış bir çalıştırma sonrasında |
| **`<Index> <Index\|LUT> <NNN>`** | Her [Endeks/LUT Sandbox](index-lut-sandbox.md) dışa aktarma çalışması için bir düğme | Sandbox dışa aktarma işlemi bittiği anda |

### LATTICE seviye düğmeleri

LATTICE çekimleri içeren projelerde, ürün adı yerine seviye adıyla etiketlenmiş şu düğmeler eklenir:

| Düğme | Seviye |
| --- | --- |
| **RAW (Orijinal)** | İçe aktarılan kaynak ham kare |
| **RAW (Radyans)** | Float32 spektral radyans, W/m²/sr/nm |
| **RAW (Yansıtma)** | uint16 yansıtma, 32768 = ρ 1,0 |

`RAW (Original)`, içe aktarma anından itibaren kullanılabilir — herhangi bir işleme tabi tutulması gerekmez. Bir LATTICE içe aktarımında hiç temel düğme bulunmadığında (her yakalamanın temel dosyası ham karedir), ızgara kendiliğinden kullanılabilir ilk seviye düğmesine geçer, böylece araç çubuğundaki vurgu, gördüğünüzle eşleşir.

İki seviyeli Chloros dışa aktarımlarında **kendi ızgara düğmesi bulunmaz**:

* **Debayered** — `RAW (Original)` görünümü zaten debayered olarak işlenmiştir; bu nedenle, görsel olarak aynı bir görüntüye ikinci bir düğme eklenmesi gereksiz olur. `RAW (Debayered)` ürünü yine de diske yazılır ve tam ekran katman açılır menüsünden seçilebilir.
* **Önizleme** — RGB kameralarda önizleme, bir düğmesi bulunan `White Balanced` katmanı olarak kaydedilir. Multispektral kameralarda ise `RAW (Preview)` olarak kaydedilir ve tam ekran katman açılır menüsünden erişilebilir.

{% hint style="info" %}
Bu seviye düğmeleri, yalnızca gerçekten LATTICE kareleri içeren projeler için görüntülenir. Survey3 projeleri, bazı aynı dahili katman adlarını kaydeder ve düğmeler bu projeler için filtrelenir; böylece bir Survey3 ızgarası, tanıdık `JPG / Targets / Reflectance` setini korur.
{% endhint %}

Bir ızgara küçük resmine tıklandığında, **ızgaranın gösterdiği ürünün** üzerinde tam ekran [Görüntü Görüntüleyicisi](opening-an-image-full-screen.md) açılır — ızgara `Targets` olarak ayarlanmışsa, küçük resim dışa aktarılan hedef görüntüyü açar.

<figure><img src="../.gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: This GIF predates the LATTICE level buttons and the toolbar group separators. Reshoot on a LATTICE project cycling base -> RAW (Original) -> RAW (Radiance) -> RAW (Reflectance) -> an index button, so the new button set and the level names are visible. -->

***

## Bir LATTICE projesini gruplandırma: Tetikleyiciye Göre mi, Kameraya Göre mi

Dizi çekimleri, farklı kamera modüllerinden aynı anın birkaç görüntüsünü üretir. Gruplandırma, ızgaranın bunları nasıl istifleyeceğini belirler. Her iki modda da tam genişlikte daraltılabilir başlık çubukları görüntülenir; **her grup açılmış olarak başlar** ve Chloros, kapattığınız grupları hatırlar. Daraltma durumu mod başına ayrı olarak izlenir; bu nedenle Kamera Başına modunda bir grubu kapatmak, Tetikleyici Başına modunda hiçbir şeyi kapatmaz.

### Kamera Başına (varsayılan)

Her kamera modülü için bir grup. Başlıkta kamera modeli ve seri numarası (`LATT-M3M — <serial>`) ile fotoğraf sayısı gösterilir. Bir grup içindeki döşemeler, çekim olayına göre kronolojik sırayla düzenlenir.

Bu modda araç çubuğunda ayrıca **her kamera için bir kamera filtre düğmesi** bulunur; bu düğmenin etiketi `MODEL (SERIAL)` şeklindedir. Tüm kameralar başlangıçta seçilidir; bir düğmeye tıklandığında o kameranın seçimi kaldırılır ve grubu ızgaradan çıkarılır. Bu, tüm uçuş boyunca tek bir bandı gözden geçirmenin hızlı yoludur.

### Tetikleyici Başına

Her çekim olayı için bir grup — tüm modüllerin aynı tetikleyiciyle çektiği kareler kümesi. Başlıkta çekim zamanı, katkıda bulunan kamera sayısı ve gruptaki her kamera modeli için bir rozet gösterilir. Bir grup içindeki döşemeler kamera seri numarasına göre sıralanır; böylece aynı bant, her tetikleme için aynı sütunda yer alır.

<!-- SCREENSHOT-NEEDED: Image grid in Per Trigger mode for a 3-camera LATTICE array, showing two consecutive trigger groups with their header bars (chevron, capture timestamp, "3 cameras", and the three model badges) and one group collapsed to show the closed state. -->
Karışık bir projedeki LATTICE dışı görüntüler gruplandırılmaz — grupların ardından düz döşemeler olarak görüntülenir.

***

## Izgara küçük resimleri GSD blok boyutuna uyar

Görüntü sekmesinin kenar çubuğunda bir **GSD (px)** blok boyutu ayarladıysanız, ızgara küçük resimleri de tam ekran görünümde olduğu gibi aynı zemin çözünürlüğünde sunulur. 8 blok boyutu, görüntünün gösterildiği uygulamanın her yerinde görüntülenen her pikselin, 8 × 8 boyutundaki bir kaynak piksel bloğunun ortalaması olduğu anlamına gelir.

Bir döşemenin genişliği başlangıçta sadece birkaç yüz piksel olduğu için, kaba blok boyutları tam ekran görünümünde olduğu kadar erken olmasa da ızgarada görünür bir fark yaratmayı bırakır: 160 piksel genişliğindeki bir döşemeye çizilen 4000 piksel çerçeve, görüntülenen her piksel başına zaten yaklaşık 25 kaynak piksele karşılık gelir. Kontrolün kendisi için [Görüntüyü Tam Ekranda Açma](opening-an-image-full-screen.md#gsd-block-size) sayfasına bakın.

***

## İlgili sayfalar

* [**Görüntüyü Tam Ekranda Açma**](opening-an-image-full-screen.md) — tam ekran görüntüleyici, imleç değerleri ve histogram
* [**Görüntü Katmanları**](image-layers.md) — tam ekran görüntüleyicinin içindeki katman açılır menüsü
* [**Dizin/LUT Sandbox**](index-lut-sandbox.md) — dizin görselleştirmelerinin oluşturulması ve dışa aktarılması
* [**Proje Ayarları**](../project-settings/project-settings.md) — hangi ürünlerin mevcut olacağını belirleyen dışa aktarma seçenekleri
