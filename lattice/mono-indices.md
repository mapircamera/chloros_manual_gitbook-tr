# Tek Renkli Kameralar ve Bitki Örtüsü Endeksleri

## Bir kamera = bir bant

**M3M**kamerası, Bayer**M3C**modelinin tek renkli versiyonudur: tek bir dar bantlı girişim filtresinin arkasında yer alan tek renkli bir IMX265 sensörüne sahiptir. Model dizesi, bandın adını belirtir — `M3M-<lens>-F<wavelength>`, ör. `M3M-L87-F685` (Chloros&#x27;te `LATT-M3M-L87-F685` olarak görüntülenir). Sensör, Bayer mozaiği içermeyen**tek bir gri tonlama bandı** sunar: demosaik işlemi gerektiren bir şey yoktur, ayrılması gereken kanal arası parazit yoktur ve ayarlanması gereken beyaz dengesi yoktur.

Mono bir sistem planlamadan önce bilmeniz gereken sonuçlar:

* **Işınım ve yansıma, her bant için tam olarak tanımlanmıştır.**Bunlar bant başına radyometrik haritalardır; dolayısıyla bir M3M kamera, tıpkı bir M3C bandının yaptığı gibi, kalibre edilmiş float32 parlaklık (W/m²/sr/nm) ve uint16 yansıma (`32768` = ρ 1,0) değerleri üretir. Mono kareler, bir**kimlik** sensör tepki matrisi içerir — 3×3 ayrıştırma gerekmez veya uygulanmaz.
* **Tek bir mono kamera, bitki örtüsü indeksi üretemez.** NDVI, NDRE ve benzerleri için en az iki bant gereklidir. Mono donanımdan indeksleri hesaplamak için birkaç M3M kamerayı birleştirmeniz gerekir — aşağıya bakınız.
* M3M kameralar **Mono12** (12 bit, kabloda piksel başına 2 bayt) akışı sağlar; bu, [dizi bant genişliği bütçeleme](arrays.md#bandwidth-the-rules-of-thumb) açısından önemlidir.

## Chloros&#x27;in mono modda atladığı aşamalar — ve bunu size nasıl bildirdiği

Renk işleme aşamaları, tek bantlı bir sensöre uygulanamaz. Chloros, hata vermek yerine **tek satırlık bir mesajla bu aşamaları atlar** ve aynı oturumdaki herhangi bir M3C (Bayer) kamera için bu aşamaları normal şekilde çalıştırmaya devam eder:

| Aşama | Mono (M3M) davranışı | M3C davranışı |
| --- | --- | --- |
| Demosaik / debayer | Atlandı — `debayered` dışa aktarım seviyesi 1 kanallı gri tonlamalı bir görüntüdür. | 3 kanallı demosaik. |
| Beyaz dengesi (`lattice white-balance`) | Tek satırlık bir mesajla atlandı. | Normal şekilde çalışır. |
| Renk profili (`lattice color-profile`) | Tek satırlık bir mesajla atlandı. | Normal şekilde çalışır. |
| Doygunluk/kontrast (`lattice color`) | Tek satırlık bir mesajla atlandı. | Normal çalışıyor. |
| Spektral çapraz konuşma ayrıştırma | Kimlik (3×3 matris yok). | Kamera başına 3×3 matris uygulandı. |
| Parlaklık / yansıma | **Çalışıyor** — bant başına, tamamen kalibre edilmiş. | Bant başına çalışıyor. |

GUI aynı geçitlemeyi uygular: tek renkli bir kamera için kamera başına ayarlar paneli, yalnızca RGB&#x27;e ait satırları (Beyaz Dengesi, Gama, Renk Profili, Doygunluk, Kontrast, kanal bölmeleri) gizler ve canlı histogram tek bir **MONO** izine kilitlenir. Yığın boyunca ayırt edici unsur, model dizesindeki `M3M` belirtecidir; bu, GUI/SDK&#x27;te `is_mono` olarak görünür.

## Dizinler için ≥ 2 bant gerekir: hizalama → yığınlama → dizinleme

Mono dizinleme iş akışı her zaman aynı üç adımdan oluşur:

1. **Hizalama** — farklı dalga boylarında çalışan birkaç M3M kamerasını (ör. bir F650 &quot;Red&quot; ve bir F850 &quot;NIR&quot;), bunları bir [çoklu kamera dizisi](arrays.md) olarak bağlayın ve Chloros&#x27;in kameralar arasındaki eş kayıt deformasyonunu hesaplamasına izin verin.
2. **Yığın** — hizalanmış kareler tek bir çok bantlı görüntü haline gelir (her kamera, adlandırılmış bir bant sağlar).
3. **Dizin** — yığının bantları üzerinde bir dizin formülünü değerlendirin; isteğe bağlı olarak bunu bir LUT aracılığıyla işleyin.

GUI&#x27;de bu zincirin tamamı, **Birleştirilmiş Kameralar**dizi görüntüleme modudur: canlı kompozit zaten hizalanmıştır ve dizinin Dizin Hesaplayıcısı (aşağıda), görüntülendiği formülü tanımlar. Yakalanan dışa aktarımlar,**Hizalanmış** yakalama seçeneği ile aynı hizalamaya göre warplanabilir.

## İndeks Hesaplayıcı

İndeks Hesaplayıcı, canlı görüntü ve kamera başına indeks dışa aktarımlarında kullanılan indeks ifadesini oluşturur. Bu, Kameralar sekmesi kenar çubuğundaki iki yerden açılabilen tek bir paylaşımlı yüzeydir:

* **Kamera Başına**— Canlı Önizleme →**İndeks** dişli simgesi (Yalnızca RGN/OCN/NGB Bayer kameralar; tek başına bir mono kamera, tek bir bantla indeks oluşturulamayacağı için indeks kontrolüne sahip değildir).
* **Dizi bazında**— dizi ayarları → Canlı Önizleme →**İndeks**dişli simgesi. Bu, mono yoldur: bant listesi**tüm üye kameraları** kapsar, dolayısıyla bir mono çift burada iki bandıyla katkıda bulunur.

<!-- SCREENSHOT-NEEDED: Index Calculator pane opened for a combined array of two mono cameras (e.g. F650 + F850): band chips row showing the two bands with wavelength labels, the operator buttons, the expression textarea containing "(NIR - Red) / (NIR + Red)", the green "Valid expression" banner, the LUT controls (Apply LUT checked, Level 7-stop, Min 0.2 / Max 1), and the live histogram with p2/p98 percentile lines. -->

Kontrolleri, yukarıdan aşağıya doğru:

* **Bant çipleri** (&quot;Bantlar — ifadeye eklemek için tıklayın&quot;) — mevcut her bant için bir düğme, üzerinde renk adı + nm cinsinden dalga boyu yazmaktadır (aynı isimdeki renkler, örneğin &quot;Renk 850&quot; şeklinde ayırt edilir). Tıkladığınızda, imlecin bulunduğu yere bant simgesi eklenir. Bant başına parlaklık üretemeyen kameralardan gelen bantlar (RGB/FRGB) filtrelenir.
* **İşlemci ve fonksiyon düğmeleri** — `+ - * / ( ) ^ ,` ve `abs() sqrt() log() log10() exp() min() max() pow()`.
* **İfade metin alanı** — serbest biçimli formül; yer tutucu, klasik NDVI biçimini (`(NIR - Red) / (NIR + Red)`) gösterir. Üstündeki salt okunur, belirteçlere ayrılmış önizleme, bant yongalarını, sayıları ve bayrakları bilinmeyen belirteçler olarak görüntüler.
* **Geçerlilik başlığı**— gri “Boş — indeks uygulanmayacak”; yeşil &quot;Geçerli ifade&quot;; belirli bir ayrıştırma hatası olduğunda kırmızı (bilinmeyen bant, birden fazla kamera tarafından yakalanan belirsiz bant, eksik parantez, …); veya ifade geçerli ancak**sabit** olduğunda (örn. `X/X`, veya `+` yerine `−` ile yazılmış bir NDVI paydası) — bir sabit, tüm kareyi tek bir renge eşler.
* Uygulanan ifade doğru olsa da **canlı kare tekdüze** ise (düz veya doymuş sahne) ayrı bir kehribar rengi uyarı görüntülenir — histogram çökmesi sizin için algılanır.
* **LUT Uygula**(varsayılan olarak açık; kapalı = gri tonlama genişletme),**Seviye**2/3/5/7-stop (varsayılan 7-stop) ve**Min / Maks**girişleri, gradyan çubuğunun iki yanında yer alır. Min varsayılan değeri**0,2**&#x27;dir — bu değer, renk rampasını bitki örtüsüyle ilgili aralığa yakınlaştırırken, bunun altındaki değerler gri tonlamalı olarak geçer; tam indeks aralığı için Min değerini −1 olarak ayarlayın (**Sıfırla** düğmesi −1…+1 aralığını geri yükler). Max varsayılan değeri 1&#x27;dir.
* İndeks dağılımının **canlı histogramı** — karekök ölçekli çubuklar, kehribar renkli p2/p98 persentil çizgileri, beyaz bir medyan çizgisi ve aralık dışı uç değer okumaları (&quot;◀ N% &lt; lo&quot; / &quot;hi &lt; N% ▶&quot;) 1 %&#x27;nin üzerinde kehribar rengine dönerek Min/Max aralığını genişletme uyarısı verir.
* **Uygula**, ifadeyi canlı akışa uygular; LUT ayarlamaları, Uygula düğmesine basılmadan canlı olarak uygulanır. İfadeler kasıtlı olarak**yalnızca oturum içindir** — oturumlar arasında saklanmazlar.

<!-- SCREENSHOT-NEEDED: Combined-array live tile rendering NDVI from a mono pair through the default 7-stop LUT, with the array name pill and fps readout visible — the result of applying the expression from the previous screenshot. -->

## CLI yolu

Aynı hizalama → yığın → dizin zinciri, baştan sona komut dosyası ile kontrol edilebilir:

```bash
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel`, bir ön ayarın sembollerini yığının bant adlarına eşler. İki kural, işlemin başarısız olmasını önler:

* **Semboller büyük/küçük harfe duyarlıdır** ve ön ayarın kanal adlarıyla tam olarak eşleşmelidir — ön ayarlar küçük harf kullanır (NDVI&#x27;ler, `red`,`nir`; `--list-presets`&#x27;i kontrol edin). `--channel red=Red_660` çalışır; `--channel RED=660` ise `channel_map missing entries` hatasıyla başarısız olur.
* Bant tarafı, hizalanmış yığındaki bir bandın adını belirtmelidir (`lattice align-info --profile align.json` bunları listeler). Çevrimdışı modda 0 tabanlı bant indeksleri de kabul edilir, örneğin `--channel red=0 --channel nir=1`.

`lattice index`, kaydedilmiş ve hizalanmış çok bantlı bir TIFF üzerinde tamamen çevrimdışı olarak da çalışır:

```bash
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn
```

### Dizin ön ayarları

`lattice index --preset` (ve aynı motoru kullanan Görüntü sekmesindeki [Dizin/LUT sanal alanı](../image-viewer-gui/index-lut-sandbox.md), şu **22 ön ayarı** içerir:

`NDVI, GNDVI, BNDVI, NDRE, ENDVI, SAVI, OSAVI, MSAVI, EVI, EVI2, CVI, MSR, TDVI, LAI, GLI, NGRDI, VARI, TGI, EXG, CIRE, CIGREEN, NDWI`

Her ön ayarın formülü ve kanal sembolleri için `chloros-cli lattice index --list-presets`&#x27;i, mevcut renk gradyanları için ise `--list-gradients`&#x27;i çalıştırın. Özel formüller, Dizin Hesaplayıcı ile aynı sözdizimini kullanan `--formula EXPR`&#x27;i kullanır. Bu ön ayar listesinin LATTICE indeks motoruna özgü olduğuna dikkat edin — içe aktarılan görüntüler için Proje Ayarları&#x27;ndaki işleme açılır menüsü farklı bir listedir (bkz. [Multispektral İndeks Formülleri](../project-settings/multispectral-index-formulas.md)).

Tam bayrak seti (`--output-format`, `--vmin/--vmax/--percentile`, `--bg-mode`, `--live` için hizalama ve çarpıtma düğmeleri, ve daha fazlası) [CLI Referansı § İndeks / Bitki Örtüsü Matematiği](../reference/cli-reference.md#index--vegetation-maths) bölümünde belgelenmiştir; SDK eşdeğerleri [SDK Referansı](../reference/sdk-reference.md) bölümünde yer almaktadır.

## Tekli bir diziden indeks ürünlerini yakalama

Bir dizi bağlandığında ve bir indeks ifadesi uygulandığında, `array-capture` (veya GUI&#x27;deki **Tümünü Yakala** seçeneği), kamera başına dışa aktarım seviyelerini *ve* indeks renderını kaydeder — `--index`/`--no-index`, bu özelliği CLI üzerinde açıp kapatır ve varsayılan olarak geçerli tüm seviyeleri içerecek şekilde yakalar. Tek bir kameranın her yakalama grubuna katkısı, ham/debayered (gri tonlamalı)/radyans/yansıtma düzeylerindeki tek bandıdır; ayrıca dizi birleştirilmiş modda çalıştığında paylaşılan birleştirilmiş dizin kompozitidir. Bkz. [Çoklu Kamera Dizileri § Yakalama](arrays.md#capturing-monitoring-vs-analysis).
