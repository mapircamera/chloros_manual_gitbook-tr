# Görüntü Katmanları

Chloros Görüntü Görüntüleyicisi&#x27;ndeki Görüntü Katmanları açılır menüsü, aynı görüntünün farklı sürümleri arasında hızlıca geçiş yapmanızı sağlar - orijinal çekimlerden işlenmiş yansıma çıktılarına ve hesaplanmış indeks görüntülerine kadar.

## Görüntü Katmanları nedir?

Chloros&#x27;te **katmanlar**, tek bir kaynak görüntü için kullanılabilen farklı görüntü çıktılarını ifade eder. Görüntüleri işlediğinizde, Chloros birden fazla sürüm oluşturur:

* **Orijinal görüntüler** (kameranızdan alınan JPG ve RAW dosyaları)
* **Yansıma kalibreli** çıktılar (yansıma kalibrasyonu etkinleştirilmişse)
* **Hedef görüntüler** (görüntü kalibrasyon hedefleri içeriyorsa)
* **Dizin görüntüleri** (NDVI, NDRE, GNDVI vb. dizinler yapılandırılmışsa)

Görüntü Görüntüleyicinin sağ üst köşesindeki **Katman Seçici açılır menüsü**, görüntüleyiciyi terk etmeden bu sürümler arasında anında geçiş yapmanızı sağlar.

***

## Kullanılabilir Katman Türleri

### JPG

* Kameranızdan alınan orijinal JPG önizleme görüntüsü
* Tüm görüntüler için her zaman kullanılabilir
* Kamera tarafından çekildiği gibi işlenmemiş
* Yükleme ve görüntüleme hızı en yüksek

**Ne zaman görüntülenir:**

* Orijinal çekimin hızlı önizlemesi
* Görüntü kompozisyonu ve kadrajı kontrol etmek
* İşleme öncesinde çekim kalitesini doğrulamak

### RAW (Orijinal)

* Kameranızdan alınan orijinal RAW sensör verileri
* Sonrası işleme uygulanmadan debayering yapılmış
* JPG&#x27;den daha yüksek bit derinliği (genellikle 12 bit veya 14 bit sensör verileri)

**Ne zaman görüntülenir:**

* Orijinal sensör verisi kalitesini inceleme
* Sensör sorunlarını veya artefaktları kontrol etme
* İşleme öncesi/sonrası sonuçlarını karşılaştırma

### RAW (Hedef)

* Yalnızca kalibrasyon hedefleri içerdiği belirlenen görüntüler için görünür
* Hedef algılanan orijinal RAW görüntüsünü gösterir
* Hedef algılamanın başarılı olduğunu doğrulamak için kullanılır

**Ne zaman görüntülenir:**

* Kalibrasyon hedeflerinin doğru algılandığını onaylama
* Hedef görüntü kalitesini kontrol etme
* Kalibrasyon sorunlarını giderme

{% hint style=&quot;info&quot; %}
**Hedef Katmanı**: Bu katman, kalibrasyon hedefleri içeren görüntüler için açılır menüde görünür. Normal çekim görüntülerinde bu seçenek yoktur.
{% endhint %}

### RAW (Yansıtma)

* Kalibre edilmiş yansıtma çıktı görüntüsü
* Vinyet düzeltmesi (işlemede etkinleştirilmişse)
* Hedef verileri kullanılarak kalibre edilmiş yansıtma (etkinleştirilmişse)
* Tüm kamera kanallarıyla çok bantlı TIFF
* Piksel değerleri yüzde yansıtmayı temsil eder (yüzde modu kullanıldığında)
* [Dizin/LUT Sandbox] ile işlenmeye hazır(index-lut-sandbox.md)

**Ne zaman görüntülenmeli:**

* Kalibre edilmiş sonuçları inceleme
* Kalibrasyon kalitesini doğrulama
* Bilimsel doğruluk için piksel değerlerini kontrol etme
* Kalibrasyon etkilerini görmek için orijinal ile karşılaştırma

{% hint style=&quot;success&quot; %}
**Önerilen**: Bilimsel ölçümler ve analizler için piksel değerlerini kontrol ederken RAW (Yansıma) katmanını kullanın.
{% endhint %}

### RAW (NDVI Endeksi)... ve benzeri

* Hesaplanan bitki örtüsü endeksi görüntüsü (bu örnekte NDVI)
* Endeks adı, işleme sırasında hangi endeksin yapılandırıldığına göre değişir
* Örnekler: RAW (NDVI Endeksi), RAW (NDRE Endeksi), RAW (GNDVI Endeksi) vb.
* Endeks hesaplama sonuçlarını gösteren tek bantlı gri tonlamalı görüntü
* Proje Ayarlarında yapılandırılan her endeks için bir katman görünür

**Olası endeks adları:**

* RAW (NDVI Endeksi)
* RAW (NDRE Endeksi)
* RAW (GNDVI Endeksi)
* RAW (OSAVI Endeksi)
* RAW (EVI Endeksi)
* RAW (SAVI Endeksi)
* Ve daha fazlası... (bkz. [Çok Spektral Endeks Formülleri](../project-settings/multispectral-index-formulas.md))

**Ne zaman görüntülenir:**

* Endeks hesaplama sonuçlarını incelemek
* Endeks değer aralıklarını kontrol etmek
* İlgi alanlarını belirlemek
* GIS veya analizde kullanmadan önce endeks görüntülerini doğrulamak

***

## Katman Seçiciyi Kullanma

### Açılır Menüyü Açma

1. Bir görüntüyü tam ekran modunda açın (Görüntü Görüntüleyicide herhangi bir küçük resme tıklayın)
2. Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsünü** bulun
3. Açılır menüde şu anda seçili olan katman (ör. &quot;JPG&quot;) gösterilir.
4. Mevcut tüm katmanları görmek için açılır menüyü tıklayın.

### Katmanları Değiştirme

1. Katman açılır menüsünü tıklayarak listeyi açın.
2. Mevcut görüntü için mevcut tüm katmanlar gösterilir.
3. Herhangi bir katman adını tıklayarak o sürüme geçin.
4. Görüntü, seçilen katmanı göstermek için hemen güncellenir.

**Hızlı geçiş:**

* Açılır menü son seçiminizi hatırlar
* Bir sonraki görüntüye geçerken, Chloros aynı katman türünü göstermeye çalışır
* Bu katman bir sonraki görüntüde yoksa, varsayılan olarak JPG kullanılır

### Katman Kullanılabilirliği

Tüm katmanlar her görüntü için kullanılabilir değildir:

**Her zaman kullanılabilir:**

* ✅ JPG (her görüntünün bir JPG önizlemesi vardır)

**Koşullu olarak kullanılabilir:**

* ⚠️ RAW (Orijinal) - Yalnızca görüntü RAW veya RAW+JPG modunda çekilmişse
* ⚠️ RAW (Hedef) - Yalnızca görüntü algılanan kalibrasyon hedefleri içeriyorsa
* ⚠️ RAW (Yansıtma) - Yalnızca yansıtma kalibrasyonu etkinleştirilerek işlendikten sonra
* ⚠️ RAW (\[Dizin] Dizin) - Yalnızca dizinler yapılandırılarak işlendikten sonra

***

## Katman Kalıcılığı

### Görüntüler Arasında Gezinme

Farklı bir görüntüye geçtiğinizde (ok tuşlarını kullanarak veya küçük resimleri tıklayarak):

**Katman tercihi korunur:**

* &quot;RAW (Yansıtma)&quot; görüntüleniyorsa, sonraki görüntü &quot;RAW (Yansıtma)&quot; gösterir (varsa)
* &quot;RAW (NDVI Dizini)&quot; görüntüleniyorsa, sonraki görüntü &quot;RAW (NDVI Dizini)&quot; gösterir (varsa)
* Aynı katman mevcut değilse, varsayılan olarak JPG kullanılır.

**Örnek iş akışı:**

1. Görüntü 1&#x27;i açın, RAW (NDVI Endeksi) moduna geçin.
2. Görüntü 2&#x27;yi görüntülemek için → tuşuna basın.
3. Görüntü 2, otomatik olarak RAW (NDVI Endeksi) katmanını görüntüler.
4. Gezinmeye devam edin - tüm görüntüler NDVI katmanını gösterir
5. Birçok görüntüdeki indeks sonuçlarını incelemek için çok verimlidir

***

## Yaygın İş Akışları

### İş Akışı 1: Öncesi/Sonrası Karşılaştırması

**Amaç**: Orijinal ve kalibre edilmiş görüntüyü karşılaştırmak

1. İşlenmiş görüntüyü Görüntü Görüntüleyicide açın
2. Açılır menüden **RAW (Orijinal)** seçeneğini seçin
3. Vinyetleme ve kalibre edilmemiş değerleri not edin
4. Açılır menüden **RAW (Yansıtma)** seçeneğine geçin
5. Karşılaştırın - vinyetleme kaldırıldı, değerler kalibre edildi

### İş Akışı 2: Dizin İnceleme

**Amaç**: Veri kümesindeki NDVI sonuçlarını hızlıca inceleyin

1. İlk işlenmiş görüntüyü açın
2. Açılır menüden **RAW (NDVI Dizini)** seçeneğini seçin
3. → ok tuşunu kullanarak bir sonraki görüntüye geçin
4. NDVI katmanı otomatik olarak kalır
5. Tüm görüntülerde devam edin ve NDVI desenlerini kontrol edin
6. Karşılaştırmak için **RAW (NDRE Endeksi)** seçeneğine geçin.

### İş Akışı 3: Hedef Doğrulama

**Amaç**: Tüm hedef görüntülerin doğru şekilde algılandığını doğrulayın.

1. Bir hedef görüntüye gidin.
2. Açılır menüden **RAW (Hedef)** seçeneğini seçin.
3. Kalibrasyon hedeflerinin açıkça görülebilir ve algılanabilir olduğunu doğrulayın.
4. Bir sonraki hedef görüntüye gidin
5. Tüm hedefler için doğrulamayı tekrarlayın

### İş Akışı 4: Piksel Değeri Denetimi

**Amaç**: Bilimsel doğruluk için yansıma değerlerini kontrol edin

1. İşlenmiş görüntüyü açın
2. **RAW (Yansıma)** katmanını seçin
3. **Piksel Yüzdesi** modunu etkinleştirin (sağ üst araç çubuğundaki düğme)
4. İmleci bitki örtüsü alanlarının üzerine getirin
5. Piksel değerlerinin beklenen aralıklarda olduğunu doğrulayın (NIR için %30-70, Red için %5-15)
6. Toprak ve su alanlarının uygun değerlerde olup olmadığını kontrol edin

***

## Katmanlara Göre Piksel Değerlerini Anlama

Farklı katmanlar farklı piksel değer aralıkları gösterir:

### JPG Katmanı

* **Aralık**: 0-255 (8 bit)
* **Anlam**: Görüntüleme değerleri, gama düzeltmeli
* **Kullanım**: Yalnızca görsel inceleme, bilimsel ölçüm için değil

### RAW (Orijinal)

* **Aralık**: 0-65535 (16 bit)
* **Anlamı**: Ham sensör dijital sayıları
* **Kullanım**: Sensör performansını kontrol etmek için, kalibre edilmemiştir

### RAW (Yansıtma)

* **Aralık**: 0-65.535 (16 bit TIFF) veya 0,0-1,0 (32 bit Yüzde)
* **Anlamı**: Kalibre edilmiş yüzde yansıma
* **Kullanım**: Bilimsel ölçümler ve analizler

**16 bit TIFF için:** Yüzde yansıma elde etmek için 65.535&#x27;e bölün **32 bit Yüzde için:** Değerler doğrudan yüzdeyi temsil eder (0,5 = %50 yansıma)

### RAW (Dizin Görüntüleri)

* **Aralık**: Dizine göre değişir (normalleştirilmiş dizinler için genellikle -1,0 ila +1,0)
* **Anlam**: Dizin hesaplama sonucu
* **Örnekler**:
  * NDVI: -1 ila +1 (bitki örtüsü genellikle 0,4 ila 0,9)
  * NDRE: -1 ila +1 (stres algılama)
  * EVI: 0 ila 1 (geliştirilmiş bitki örtüsü)

***

## İpuçları ve En İyi Uygulamalar

### Verimli Katman Değiştirme

* **Klavye kısayolları hakkında bilgi**: Katmanlar için klavye kısayolu olmasa da, gezinme okları (←/→) tüm katmanlarda çalışır
* **Tutarlı iş akışları**: Bir katman seçin (ör. NDVI) ve başka bir katmana geçmeden önce tüm veri kümesini inceleyin
* **Hızlı karşılaştırmalar**: İşleme kalitesini doğrulamak için Orijinal ve Yansıma arasında geçiş yapın

### Performansla İlgili Hususlar

* **JPG en hızlı yüklenir**: Birçok görüntü arasında hızlı gezinmek için kullanın.
* **RAW katmanları daha yavaş yüklenir**: Daha yüksek çözünürlük ve bit derinliği.
* **Dizin katmanları**: Yansıma katmanlarına benzer hız.
* **İlk yükleme en yavaştır**: Aynı katmanın sonraki görüntülemeleri önbelleğe alınır ve daha hızlıdır.

### Kalite Doğrulama

* **Her zaman RAW (Orijinal) kontrol edin**: İşlenmiş çıktıları güvenmeden önce kaynak veri kalitesini doğrulayın
* **Katmanları karşılaştırın**: Katman değiştirmeyi kullanarak işlemenin doğru çalıştığını doğrulayın
* **Dizin aralıklarını kontrol edin**: Dizin katmanlarıyla Piksel Yüzde modunu kullanarak değerlerin makul olduğunu doğrulayın

***

## Sorun Giderme

### Katman Kullanılamıyor

**Sorun**: Beklenen katman açılır menüde görünmüyor

**Olası nedenler:**

* Görüntü işlenmemiştir (yalnızca JPG ve RAW (Orijinal) kullanılabilir)
* İşleme sırasında yansıma kalibrasyonu devre dışı bırakılmıştır
* Proje Ayarlarında belirli bir indeks yapılandırılmamıştır
* Görüntü yalnızca hedef içeren bir görüntüdür (hedefler için indeks oluşturulmamıştır)

**Çözümler:**

1. Görüntünün işlendiğini doğrulayın (işlenmiş dosyalar için çıktı klasörünü kontrol edin)
2. Proje Ayarlarını kontrol ederek indekslerin yapılandırıldığını doğrulayın
3. İstenen indeksler etkinleştirilerek yeniden işleyin

### Yanlış Katman Gösteriliyor

**Sorun**: Görüntü beklenmedik bir katmanda açılıyor

**Neden**: Önceki görüntünün katman tercihi devralındı, ancak bu katman mevcut görüntüde mevcut değil

**Çözüm**: Chloros, tercih edilen katman mevcut olmadığında otomatik olarak JPG&#x27;ye geri döner - bu normal bir davranıştır

### Kalibrasyon Hedefleri Görünmüyor

**Sorun**: RAW (Hedef) katmanı hedef algılamayı göstermiyor.

**Olası nedenler:**

* İşleme sırasında hedefler algılanmadı.
* Görüntü aslında hedefler içermiyor.
* Hedef algılama ayarları çok katı.

**Çözümler:**

1. Hata Ayıklama Günlüğünde &quot;Hedef bulundu&quot; mesajlarını kontrol edin.
2. Görüntünün gerçekten görünür kalibrasyon hedefleri içerdiğini doğrulayın
3. Proje Ayarları&#x27;nda hedef algılama ayarlarını düzenleyin
4. [Hedef Görüntüleri Seçme](../processing-images-gui/choosing-target-images.md) bölümüne bakın

***

## İlgili Özellikler

### Görüntü Görüntüleyici Araçları

Herhangi bir katmanı görüntülerken şunları kullanabilirsiniz:

* **Yakınlaştırma kontrolleri**: Ayrıntıları incelemek için büyütün
* **Kaydırma**: Tıklayıp sürükleyerek yakınlaştırılmış görüntüde hareket edin
* **Piksel değeri inceleme**: İmlecin bulunduğu konumdaki değerleri görün
* **Gezinme okları**: Katmanı koruyarak görüntüler arasında hareket edin
* **Piksel Yüzde modu**: DN ve yüzde gösterimi arasında geçiş yapın

Görüntü Görüntüleyici belgelerinin tamamı için [Görüntüyü Tam Ekran Açma](opening-an-image-full-screen.md) bölümüne bakın.

### Dizin/LUT Sandbox

Etkileşimli dizin testi ve görselleştirme için:

* **Gerçek zamanlı dizin hesaplama**: Farklı dizin formüllerini test edin
* **LUT renk eşlemesi**: Gri tonlamalı dizinlere renk gradyanları uygulayın
* **Görselleştirmeleri dışa aktarın**: Renkli dizin görüntülerini kaydedin

Ayrıntılar için [Dizin/LUT Sandbox](index-lut-sandbox.md) için ayrıntılara bakın.

***

## Sonraki Adımlar

Artık görüntü katmanlarını anladığınıza göre:

* [**Görüntüyü Tam Ekran Açma**](opening-an-image-full-screen.md) - Tam Görüntü Görüntüleyici kılavuzu
* [**Dizin/LUT Sandbox**](index-lut-sandbox.md) - Etkileşimli indeks görselleştirme
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Kullanılabilir indeks referansı
* [**İşlemeyi Tamamlama**](../processing-images-gui/finishing-the-processing.md) - İşlenmiş çıktıları anlama
