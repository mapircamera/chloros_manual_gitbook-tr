# Görüntüyü Tam Ekran Açma

Chloros Görüntü Görüntüleyici, çok spektrumlu görüntülerinizi görüntülemek, analiz etmek ve işlemek için özel bir tam ekran arayüzü sağlar. Orijinal görüntüleri veya işlenmiş çıktıları görüntülerken, Görüntü Görüntüleyici inceleme ve analiz için güçlü araçlar sunar.

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

* **Sonraki görüntü**: → düğmesini tıklayın veya **→** (Sağ Ok) tuşuna basın
* **Önceki görüntü**: ← düğmesini tıklayın veya **←** (Sol Ok) tuşuna basın
* **Belirli bir görüntüye atlama**: Dosya Tarayıcıya dönün ve istediğiniz küçük resmi tıklayın

#### Yakınlaştırma Kontrolleri

Görüntü ayrıntılarını incelemek için büyütmeyi ayarlayın:

**Yakınlaştırma:**

* **+** (Artı) düğmesine tıklayın
* **+** veya **=** tuşuna basın
* Fare tekerleğini **yukarı** kaydırın

**Uzaklaştırma:**

* **−** (Eksi) düğmesine tıklayın
* **−** (Eksi) tuşuna basın
* Fare tekerleğini **aşağı** kaydırın

**Ekrana Sığdır:**

* **↔** (Sığdır) düğmesini tıklayın.
* **0** (Sıfır) tuşuna basın.
* Görüntüyü çift tıklayın.

#### Yakınlaştırıldığında Kaydırma

Ekran boyutunun ötesine yakınlaştırıldığında:

1. Fare imlecini görüntünün üzerine getirin.
2. **Farenin sol tuşunu basılı tutun**.
3. Görüntüyü hareket ettirmek için **sürükleyin**
4. Kaydırmayı durdurmak için bırakın

**Alternatif**: Küçük artışlarla kaydırmak için ok tuşlarını kullanın

***

## Piksel Değeri İnceleme

### İmleçteki Piksel Değerlerini Görüntüleme

Fare imlecini görüntünün üzerine getirdiğinizde, piksel değerleri gerçek zamanlı olarak görüntülenir:

**Değer görüntüleme konumu:**

* **Sağ tarafta kayan sayı ve kırmızı çizgi LUT gradyanı göstergesi**
* **Daha fazla yakınlaştırıldığında, imlecin yakınında kayan değer ve vurgulanmış piksel**
* **İmlecin altındaki veya vurgulanmış** pikselin değerlerini gösterir
* Fareyi hareket ettirdikçe güncellenir

***

## Görüntüleyebileceğiniz Görüntü Türleri

### Orijinal Görüntüler (Ön İşleme)

**Kameradan alınan RAW + JPG görüntüler:**

* RAW verilerini önizleme olarak gösterir
* Orijinal, düzeltilmemiş değerleri gösterir
* İşleme öncesinde görüntü kalitesini kontrol etmek için kullanışlıdır

### Kalibre Edilmiş Yansıma Görüntüleri

**İşleme sonrası:**

* Vinyet düzeltildi
* Yansıma kalibre edildi
* Çok bantlı TIFF (Red, Green, NIR, vb.)
* Analiz için hazır bilimsel veriler

### Dizin Görüntüler

**NDVI, NDRE, GNDVI, vb. (\_NDVI.tif dosyaları):**

* Tek bantlı gri tonlamalı görüntüler
* Piksel değerleri indeks hesaplama sonuçlarını temsil eder
* Normalize edilmiş indeksler için aralık genellikle -1 ile +1 arasındadır
* Görselleştirme için renk LUT&#x27;ları uygulanabilir

***

## İndeks ve LUT Uygulaması

Çok spektral indeksleri ve renk Arama Tablolarını uygulayın:

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

* **+** veya **=**: Yakınlaştır
* **−**: Uzaklaştır
* **0** (Sıfır): Ekrana sığdır
* **Fare Tekerleği**: Yakınlaştır/uzaklaştır

### Görüntüleme Denetimleri

* **P**: Piksel yüzdesi modunu aç/kapat
* **L**: Katmanlar panelini aç/kapat
* **Esc**: Tam ekranı kapat veya Dosya Tarayıcıya dön

### Diğer

* **Ctrl+S**: Mevcut görüntüyü kaydet
* **F**: Tam ekran modu (varsa)

***

### Endeks Hesaplamalarını Doğrulama

Endekslerin doğru hesaplandığını kontrol edin:

1. NDVI veya başka bir endeks görüntüsünü açın
2. Bitki örtüsü alanlarını kontrol edin:
   * **NDVI**: Sağlıklı bitkiler için 0,4-0,9 arasında bir değer göstermelidir
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

1. Dosya bütünlüğünü doğrulamak için harici görüntüleyicide açmayı deneyin.
2. Dosya formatının beklenen türle eşleştiğini kontrol edin.
3. Belleği boşaltmak için diğer uygulamaları kapatın.
4. Daha küçük/farklı bir görüntü deneyin.

### Siyah veya Beyaz Görüntü Gösterimi

**Olası nedenler:**

* Değer aralığı görüntüleme kapasitesinin dışında.
* Olağandışı değerlere sahip 32 bitlik kayan noktalı görüntü.
* Dizin hesaplama hatası.

**Çözümler:**

1. Piksel değerlerini kontrol edin - hepsi çok düşük veya çok yüksekse, görüntüleme aralığını ayarlayın.
2. Otomatik aralık ayarı ile QGIS veya benzeri bir programda açmayı deneyin.
3. İşlemden gelen Hata Ayıklama Günlüğünü kontrol edin.

### Piksel Değerleri Yanlış Görünüyor

**Olası nedenler:**

* Yanlış görüntü görüntüleniyor (orijinal ve işlenmiş)
* Kalibrasyon doğru uygulanmadı
* Işık sensörü verileri girişe dahil edilmedi
* Yüzde modu yanlış değiştirildi

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
