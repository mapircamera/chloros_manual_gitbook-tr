# Bir Görüntüyü Tam Ekran Olarak Açma

Chloros Görüntü Görüntüleyici, multispektral görüntülerinizi görüntülemek, analiz etmek ve işlemek için özel bir tam ekran arayüzü sunar. İster orijinal görüntüleri ister işlenmiş çıktıları görüntülüyor olun, Görüntü Görüntüleyici inceleme ve analiz için güçlü araçlar sunar.

## Görüntü Görüntüleyicisine Erişme

### Dosya Tarayıcısından

Görüntü Görüntüleyicisinde bir görüntüyü açmanın en yaygın yolu:

1. **Dosya Tarayıcısı** sekmesinde olduğunuzdan emin olun <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Görüntü ızgarasındaki herhangi bir **görüntü küçük resmine** tıklayın
3. Görüntü, **ana önizleme alanında** (ekranın ortasında) açılır
4. Görüntü artık yüklenmiştir ve tam ekran görüntülemeye hazırdır

### Görüntü Görüntüleyici Sekmesini Açma

Bir görüntü önizleme alanına yüklendikten sonra:

1. Sol kenar çubuğundaki **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> simgesine tıklayın
2. Görüntü Görüntüleyici sekmesi açılır ve seçilen görüntü tam ekran olarak görüntülenir
3. Sol kenar çubuğunda gelişmiş görüntüleme ve analiz araçları kullanılabilir hale gelir

***

## Görüntü Görüntüleyici Arayüzüne Genel Bakış

### Ana Görüntüleme Alanı

Ekranın en büyük kısmı görüntünüzü gösterir:

* **Tam çözünürlük**: Görüntüler doğal çözünürlükte görüntülenir
* **Yakınlaştırılabilir**: Yakınlaştırmak için kontrolleri veya fare tekerleğini kullanın
* **Kaydırılabilir**: Yakınlaştırıldığında tıklayıp sürükleyerek hareket ettirin
* **En boy oranı korunur**: Görüntüler orantılı olarak ölçeklenir***

## Görüntüleme Seçenekleri

### Temel Görüntü Gezinme

#### Görüntüler Arasında Gezinme

Klavye kısayollarını veya düğmeleri kullanarak görüntü setinizde gezin:

* **Sonraki görüntü**: → düğmesine tıklayın veya**→** (Sağ Ok) tuşuna basın
* **Önceki görüntü**: ← düğmesine tıklayın veya**←** (Sol Ok) tuşuna basın
* **Belirli bir görüntüye atla**: Dosya Tarayıcısına dönün ve istediğiniz küçük resme tıklayın

#### Yakınlaştırma Kontrolleri

Görüntü ayrıntılarını incelemek için büyütme oranını ayarlayın:

**Yakınlaştırma:*** **+** (Artı) düğmesine tıklayın
* **+**veya**=** tuşuna basın
* Fare tekerleğini **yukarı** kaydırın**Uzaklaştırma:*** **−** (Eksi) düğmesine tıklayın
* **−** (Eksi) tuşuna basın
* Fare tekerleğini **aşağı** kaydırın

#### Yakınlaştırıldığında Kaydırma

Ekran boyutunun ötesine yakınlaştırıldığında:

1. Fare imlecini görüntünün üzerine getirin
2. **Sol fare düğmesini tıklayın ve basılı tutun**

3. Görüntüyü hareket ettirmek için**sürükleyin**

4. Kaydırmayı durdurmak için düğmeyi bırakın**Alternatif**: Ok tuşlarını kullanarak küçük adımlarla kaydırın***

## Piksel Değeri İnceleme

### İmlecin Bulunduğu Yerdeki Piksel Değerlerini Görüntüleme

Fare imlecini görüntünün üzerine getirdiğinizde, piksel değerleri gerçek zamanlı olarak görüntülenir:**Değer görüntüleme konumu:*** **Sağ taraftaki LUT gradyan göstergesinde yüzen sayı ve kırmızı çizgi*** **Daha fazla yakınlaştırıldığında, imlecin yakınında ve vurgulanan pikselin yanında yüzen değer*** **İmlecin altında veya vurgulanan** pikselin değerlerini gösterir
* Fareyi hareket ettirdikçe güncellenir

***

## Görüntüleyebileceğiniz Görüntü Türleri

### JPG

**Kameradan alınan JPG görüntüleri:**

* JPG verilerini önizleme olarak görüntüler
* Orijinal, düzeltilmemiş değerleri gösterir
* İşleme öncesinde görüntü kalitesini kontrol etmek için kullanışlıdır

### RAW (Orijinal)

### RAW (Yansıma)

**İşlemden sonra:**

* Vinyet düzeltildi
* Yansıma kalibre edildi
* Çok bantlı TIFF (Red, Green, NIR, vb.)
* Analiz için hazır bilimsel veriler

### RAW (İndeks)

**NDVI, NDRE, GNDVI, vb. (\_NDVI.tif dosyaları):**

* Tek bantlı gri tonlamalı görüntüler
* Piksel değerleri endeks hesaplama sonuçlarını temsil eder
* Normalleştirilmiş endeksler için aralık genellikle -1 ile +1 arasındadır
* Görselleştirme için renk LUT&#x27;ları uygulanabilir

***

## Endeks ve LUT Uygulaması

Çok spektral endeksleri ve renk Arama Tablolarını uygulayın:

1. **Görüntü Görüntüleyicisi**&#x27;nde**Endeks/LUT Sandbox**&#x27;ı bulun <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kenar çubuğunda**Index/LUT Sandbox**&#x27;ı bulun
2. Bitki örtüsü indeksini seçin (NDVI, NDRE, vb.)
3. Çok spektral formülü seçin veya kendi özel formülünüzü oluşturun (sadece Chloros+)
4. Görselleştirme için renk LUT gradyanı uygulayın
5. Değer aralıklarını ve eşik değerlerini ayarlayın

Ayrıntılı talimatlar için [Endeks/LUT Sandbox](index-lut-sandbox.md) sayfasına bakın.

***

## Klavye Kısayolları

### Gezinme

* **→** (Sağ Ok): Sonraki görüntü
* **←** (Sol Ok): Önceki görüntü
* **Home**: Listedeki ilk görüntü
* **End**: Listedeki son görüntü

### Yakınlaştırma

* **+**veya**=**: Yakınlaştır
* **−**: Uzaklaştır
* **Fare Tekerleği**: Yakınlaştır/Uzaklaştır***

### İndeks Hesaplamalarını Doğrulama

İndekslerin doğru hesaplandığını kontrol edin:

1. NDVI veya başka bir endeks görüntüsünü açın
2. Bitki örtüsü alanlarını kontrol edin:
   * **NDVI**: Sağlıklı bitkiler için 0,4-0,9 değerini göstermelidir
   * **NDRE**: Güçlü büyüme için daha yüksek değerler
   * **GNDVI**: NDVI&#x27;e benzer ancak klorofil duyarlıdır
3. Bitki örtüsü dışındaki alanları kontrol edin:
   * **Toprak**: 0&#x27;a yakın veya hafif negatif
   * **Su**: Negatif değerler (-0,5 ila 0)***

## Görüntüleme Sorunlarını Giderme

### Görüntü Açılmıyor

**Olası nedenler:**

* İşleme sırasında dosya bozulmuş
* Desteklenmeyen dosya biçimi
* Büyük görüntü için yetersiz bellek

**Çözümler:**

1. Dosyanın bütünlüğünü doğrulamak için harici bir görüntüleyicide açmayı deneyin
2. Dosya formatının beklenen türle eşleşip eşleşmediğini kontrol edin
3. Bellek boşaltmak için diğer uygulamaları kapatın
4. Daha küçük/farklı bir görüntü deneyin

### Siyah veya Beyaz Görüntü Görüntülenmesi

**Olası nedenler:**

* Görüntüleme kapasitesinin dışındaki değer aralığı
* Olağandışı değerlere sahip 32 bit kayan noktalı görüntü
* Dizin hesaplama hatası

**Çözümler:**

1. Piksel değerlerini kontrol edin - hepsi çok düşük veya çok yüksekse, görüntüleme aralığını ayarlayın
2. Otomatik aralık ayarlaması olan QGIS veya benzeri bir programda açmayı deneyin
3. İşlemden kaynaklanan hatalar için Hata Giderme Günlüğünü kontrol edin

### Piksel Değerleri Yanlış Görünüyor

**Olası nedenler:**

* Yanlış görüntü görüntüleniyor (orijinal mi, işlenmiş mi)
* Kalibrasyon doğru uygulanmadı
* Işık sensörü verileri girişe dahil edilmedi
* Yüzde modu yanlış ayarlandı

**Çözümler:**

1. İşlenmiş çıktıyı görüntülediğinizden emin olun (dosya adı sonekini kontrol edin)
2. Yüzde modu düğmesinin durumunu kontrol edin
3. Aynı veri kümesinden bilinen iyi görüntülerle karşılaştırın

***

## Sonraki Adımlar

Artık görüntüleri tam ekran görüntüleyebildiğinize göre:

* [**Görüntü Katmanları**](image-layers.md) - Çok bantlı görselleştirme hakkında bilgi edinin
* [**Dizin/LUT Sandbox**](index-lut-sandbox.md) - Özel dizinler ve renk eşlemesi uygulayın
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Kullanılabilir indeksleri anlayın

İşleme akışı için bkz.:

* [**Görüntüleri İşleme (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Tam işleme kılavuzu
