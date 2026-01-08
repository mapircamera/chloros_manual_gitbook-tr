# Görüntüyü Tam Ekran Açma

Chloros Görüntü Görüntüleyici, çok spektral görüntülerinizi görüntülemek, analiz etmek ve işlemek için özel bir tam ekran arayüzü sağlar. Orijinal görüntüleri veya işlenmiş çıktıları görüntülerken, Görüntü Görüntüleyici inceleme ve analiz için güçlü araçlar sunar.

## Görüntü Görüntüleyiciye Erişim

### Dosya Tarayıcısından

Görüntü Görüntüleyicide bir görüntüyü açmanın en yaygın yolu:

1. **Dosya Tarayıcısı** sekmesinde olduğunuzdan emin olun <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line">
2. Görüntü ızgarasındaki herhangi bir **görüntü küçük resmini** tıklayın
3. Görüntü **ana önizleme alanında** (ekranın ortasında) açılır
4. Görüntü artık yüklenmiştir ve tam ekran görüntüleme için hazırdır

### Görüntü Görüntüleyici Sekmesini Açma

Görüntü önizleme alanına yüklendikten sonra:

1. Sol kenar çubuğundaki **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> simgesine tıklayın
2. Görüntü Görüntüleyici sekmesi açılır ve seçilen görüntü tam ekran olarak görüntülenir
3. Gelişmiş görüntüleme ve analiz araçları sol kenar çubuğunda kullanılabilir hale gelir

***

## Görüntü Görüntüleyici Arayüzüne Genel Bakış

### Ana Görüntüleme Alanı

Ekranın en büyük kısmı görüntünüzü gösterir:

* **Tam çözünürlük**: Görüntüler doğal çözünürlükte görüntülenir
* **Yakınlaştırılabilir**: Yakınlaştırmak için kontrolleri veya fare tekerleğini kullanın
* **Kaydırılabilir**: Yakınlaştırıldığında tıklayıp sürükleyerek hareket ettirin
* **En boy oranı korunur**: Görüntüler orantılı olarak ölçeklenir

***

## Görüntüleme Seçenekleri

### Temel Görüntü Gezinme

#### Görüntüleri Göz Atma

Klavye kısayolları veya düğmeleri kullanarak görüntü setinizde gezin:

* **Sonraki görüntü**: → düğmesine tıklayın veya **→** (Sağ Ok) tuşuna basın
* **Önceki görüntü**: ← düğmesine tıklayın veya **←** (Sol Ok) tuşuna basın
* **Belirli bir görüntüye atlama**: Dosya Tarayıcıya dönün ve istediğiniz küçük resme tıklayın

#### Yakınlaştırma Kontrolleri

Görüntü ayrıntılarını incelemek için büyütmeyi ayarlayın:

**Yakınlaştırma:**

* **+** (Artı) düğmesini tıklayın
* **+** veya **=** tuşuna basın
* Fare tekerleğini **yukarı** kaydırın

**Uzaklaştırma:**

* **−** (Eksi) düğmesini tıklayın
* **−** (Eksi) tuşuna basın
* Fare tekerleğini **aşağı** kaydırın

#### Yakınlaştırıldığında Kaydırma

Ekran boyutunun ötesine yakınlaştırıldığında:

1. Fare imlecini görüntünün üzerine getirin
2. **Sol fare düğmesini basılı tutun**
3. Görüntüyü hareket ettirmek için **sürükleyin**
4. Kaydırmayı durdurmak için bırakın

**Alternatif**: Küçük artışlarla kaydırmak için ok tuşlarını kullanın

***

## Piksel Değeri İnceleme

### İmleçteki Piksel Değerlerini Görüntüleme

Fare imlecini görüntünün üzerinde hareket ettirdiğinizde, piksel değerleri gerçek zamanlı olarak görüntülenir:

**Değer görüntüleme konumu:**

* **Sağ tarafta kayan sayı ve kırmızı çizgi indeks LUT gradyan açıklaması**
* **Daha fazla yakınlaştırıldığında, imlecin yakınında kayan değer ve vurgulanmış piksel**
* **İmlecin altındaki veya vurgulanmış** pikselin değerlerini gösterir
* Fareyi hareket ettirdikçe güncellenir

***

## Görüntüleyebileceğiniz Görüntü Türleri

### JPG

**Kameradan alınan JPG görüntüleri:**

* JPG verilerini önizleme olarak gösterir
* Düzeltilmemiş orijinal değerleri gösterir
* İşleme öncesinde görüntü kalitesini kontrol etmek için kullanışlıdır

### RAW (Orijinal)

### RAW (Yansıtma)

**İşleme sonrası:**

* Vinyet düzeltildi
* Yansıma kalibre edildi
* Çok bantlı TIFF (Red, Green, NIR, vb.)
* Analiz için hazır bilimsel veriler

### RAW (Endeks)

**NDVI, NDRE, GNDVI, vb. (\_NDVI.tif dosyaları):**

* Tek bantlı gri tonlamalı görüntüler
* Piksel değerleri, endeks hesaplama sonuçlarını temsil eder
* Normalize edilmiş endeksler için aralık genellikle -1 ile +1 arasındadır
* Görselleştirme için renk LUT&#x27;ları uygulanabilir

***

## Endeks ve LUT Uygulaması

Çok spektral endeksleri ve renk Arama Tablolarını uygulayın:

1. **Görüntü Görüntüleyici** <img src="../.gitbook/assets/icon_image-viewer.JPG" alt="" data-size="line"> kenar çubuğunda
2. Bitki örtüsü endeksini seçin (NDVI, NDRE, vb.)
3. Çok spektral formülü seçin veya kendi özel formülünüzü oluşturun (yalnızca Chloros+)
4. Görselleştirme için renk LUT gradyanı uygulayın
5. Değer aralıklarını ve eşikleri ayarlayın

Ayrıntılı talimatlar için [Endeks/LUT Sandbox](index-lut-sandbox.md) bölümüne bakın.

***

## Klavye Kısayolları

### Gezinme

* **→** (Sağ Ok): Sonraki görüntü
* **←** (Sol Ok): Önceki görüntü
* **Home**: Listedeki ilk görüntü
* **End**: Listedeki son görüntü

### Yakınlaştırma

* **+** veya **=**: Yakınlaştırma
* **−**: Uzaklaştırma
* **Fare Tekerleği**: Yakınlaştırma/uzaklaştırma

***

### Dizin Hesaplamalarını Doğrulama

Dizinlerin doğru hesaplandığını kontrol edin:

1. NDVI veya başka bir indeks görüntüsünü açın
2. Bitki örtüsü alanlarını kontrol edin:
   * **NDVI**: Sağlıklı bitkiler için 0,4-0,9 değerini göstermelidir
   * **NDRE**: Güçlü büyüme için daha yüksek değerler
   * **GNDVI**: NDVI&#x27;e benzer, ancak klorofil duyarlıdır
3. Bitki örtüsü olmayan alanları kontrol edin:
   * **Toprak**: 0&#x27;a yakın veya hafif negatif
   * **Su**: Negatif değerler (-0,5 ila 0)

***

## Görüntüleme Sorunlarını Giderme

### Görüntü Açılmıyor

**Olası nedenler:**

* İşleme sırasında dosya bozulmuş
* Desteklenmeyen dosya biçimi
* Büyük görüntü için yeterli bellek yok

**Çözümler:**

1. Dosyanın bütünlüğünü doğrulamak için harici görüntüleyicide açmayı deneyin.
2. Dosya formatının beklenen türle eşleştiğini kontrol edin.
3. Belleği boşaltmak için diğer uygulamaları kapatın.
4. Daha küçük/farklı bir görüntü deneyin.

### Siyah veya Beyaz Görüntü Gösterimi

**Olası nedenler:**

* Değer aralığı görüntüleme kapasitesinin dışında.
* Olağandışı değerlere sahip 32 bitlik kayan noktalı görüntü.
* Dizin hesaplama hatası.

**Çözümler:**

1. Piksel değerlerini kontrol edin - hepsi çok düşük veya çok yüksekse, görüntüleme aralığını ayarlayın
2. Otomatik aralık ayarı ile QGIS veya benzeri bir programda açmayı deneyin
3. İşlemden gelen Hata Ayıklama Günlüğünü kontrol edin

### Piksel Değerleri Yanlış Görünüyor

**Olası nedenler:**

* Yanlış görüntü görüntüleniyor (orijinal ve işlenmiş)
* Kalibrasyon doğru uygulanmadı
* Işık sensörü verileri girişe dahil edilmedi
* Yüzde modu yanlış ayarlandı

**Çözümler:**

1. İşlenmiş çıktıyı görüntülediğinizi doğrulayın (dosya adı sonekini kontrol edin)
2. Yüzde modu düğmesinin durumunu kontrol edin
3. Aynı veri kümesinden bilinen iyi görüntülerle karşılaştırın

***

## Sonraki Adımlar

Artık görüntüleri tam ekran görüntüleyebildiğinize göre:

* [**Görüntü Katmanları**](image-layers.md) - Çok bantlı görselleştirme hakkında bilgi edinin
* [**Dizin/LUT Sandbox**](index-lut-sandbox.md) - Özel dizinler ve renk eşlemesi uygulayın
* [**Çok Spektral Dizin Formülleri**](../project-settings/multispectral-index-formulas.md) - Kullanılabilir dizinleri anlayın

İşleme iş akışı için bkz.:

* [**Görüntüleri İşleme (GUI)**](../processing-images-gui/adding-files-to-a-project.md) - Tam işleme kılavuzu
