# Görüntü Katmanları

Chloros Görüntü Görüntüleyicisi&#x27;ndeki Görüntü Katmanları açılır menüsü, aynı görüntünün farklı sürümleri arasında – orijinal çekimlerden işlenmiş yansıma çıktılarına ve hesaplanmış indeks görüntülerine kadar – hızlıca geçiş yapmanızı sağlar.

## Görüntü Katmanları Nedir?

Chloros&#x27;te **katmanlar**, tek bir kaynak görüntü için mevcut olan farklı görüntü çıktılarını ifade eder. Görüntüleri işlediğinizde, Chloros birden fazla sürüm oluşturur:

* **Orijinal görüntüler** (kameranızdan alınan JPG ve RAW dosyaları)
* **Yansıma kalibre edilmiş** çıktılar (yansıma kalibrasyonu etkinleştirilmişse)
* **Hedef görüntüler** (görüntü kalibrasyon hedefleri içeriyorsa)
* **İndeks görüntüleri** (NDVI, NDRE, GNDVI vb., indeksler yapılandırılmışsa)

Görüntü Görüntüleyicinin sağ üst köşesindeki **Katman Seçici açılır menüsü**, görüntüleyiciden çıkmadan bu sürümler arasında anında geçiş yapmanızı sağlar.***

## Kullanılabilir Katman Türleri

### JPG

* Kameranızdan gelen orijinal JPG önizleme görüntüsü
* Tüm görüntüler için her zaman kullanılabilir
* Kamera tarafından yakalandığı haliyle işlenmemiş
* Yükleme ve görüntüleme süresi en kısa olanı

**Ne zaman görüntülenmeli:**

* Orijinal çekimin hızlı önizlemesi
* Görüntü kompozisyonunu ve kadrajı kontrol etmek
* İşleme öncesinde çekim kalitesini doğrulamak

### RAW (Orijinal)

* Kameranızdan gelen orijinal RAW sensör verileri
* Sonradan işleme uygulanmadan debayering yapılmış
* JPG&#x27;den daha yüksek bit derinliği (genellikle 12 bit veya 14 bit sensör verileri)

**Ne zaman görüntülenmeli:**

* Orijinal sensör verisi kalitesini inceleme
* Sensör sorunlarını veya artefaktları kontrol etme
* İşleme öncesi/sonrası sonuçlarını karşılaştırma

### RAW (Hedef)

* Yalnızca kalibrasyon hedefleri içerdiği belirlenen görüntülerde görünür
* Hedef algılandığında orijinal RAW görüntüsünü gösterir
* Hedef algılamasının başarılı olduğunu doğrulamak için kullanılır

**Ne zaman görüntülenmeli:**

* Kalibrasyon hedeflerinin doğru algılandığını onaylama
* Hedef görüntü kalitesini kontrol etme
* Kalibrasyon sorunlarını giderme

{% hint style="info" %}
**Hedef Katmanı**: Bu katman, yalnızca kalibrasyon hedefleri içeren görüntüler için açılır menüde görünür. Normal çekim görüntülerinde bu seçenek bulunmaz.
{% endhint %}

### RAW (Yansıma)

* Kalibre edilmiş yansıma çıktı görüntüsü
* Vinyet düzeltmesi yapılmış (işlem sırasında etkinleştirilmişse)
* Hedef verileri kullanılarak kalibre edilmiş yansıma (etkinleştirilmişse)
* Tüm kamera kanallarıyla çok bantlı TIFF
* Piksel değerleri yüzde yansıma oranını temsil eder (yüzde modu kullanıldığında)
* [Dizin/LUT Sandbox](index-lut-sandbox.md) ile işlenmeye hazır

**Ne zaman görüntülenmeli:**

* Kalibre edilmiş sonuçları inceleme
* Kalibrasyon kalitesini doğrulama
* Bilimsel doğruluk için piksel değerlerini kontrol etme
* Kalibrasyon etkilerini görmek için orijinal ile karşılaştırma

{% hint style="success" %}
**Önerilen**: Bilimsel ölçümler ve analizler için piksel değerlerini kontrol ederken RAW (Yansıma) katmanını kullanın.
{% endhint %}

### RAW (NDVI Endeksi)... ve benzerleri

* Hesaplanmış bitki örtüsü endeksi görüntüsü (bu örnekte NDVI)
* Endeks adı, işleme sırasında hangi endeksin yapılandırıldığına göre değişir
* Örnekler: RAW (NDVI Endeksi), RAW (NDRE Endeksi), RAW (GNDVI Endeksi) vb.
* Endeks hesaplama sonuçlarını gösteren tek bantlı gri tonlamalı görüntü
* Proje Ayarları&#x27;nda yapılandırılan her endeks için bir katman görünür

**Olası endeks adları:**

* RAW (NDVI Endeksi)
* RAW (NDRE Endeksi)
* RAW (GNDVI Endeksi)
* RAW (OSAVI Endeksi)
* RAW (EVI Endeksi)
* RAW (SAVI Endeksi)
* Ve daha fazlası... (bkz. [Çok Spektral Endeks Formülleri](../project-settings/multispectral-index-formulas.md))

**Ne zaman görüntülenmeli:**

* Endeks hesaplama sonuçlarını incelemek
* Endeks değer aralıklarını kontrol etmek
* İlgi alanlarını belirlemek
* GIS veya analizde kullanmadan önce endeks görüntülerini doğrulamak

***

## Katman Seçiciyi Kullanma

### Açılır Menüyü Açma

1. Bir görüntüyü tam ekran modunda açın (Görüntü Görüntüleyicisi&#x27;ndeki herhangi bir küçük resme tıklayın)
2. Görüntüleyicinin sağ üst köşesindeki **katman açılır menüsünü** bulun
3. Açılır menü, o anda seçili olan katmanı gösterir (ör. &quot;JPG&quot;)
4. Mevcut tüm katmanları görmek için açılır menüye tıklayın

### Katmanlar Arasında Geçiş Yapma

1. Listeyi açmak için katman açılır menüsüne tıklayın
2. Mevcut görüntü için mevcut tüm katmanlar gösterilir
3. Herhangi bir katman adına tıklayarak o sürüme geçin
4. Görüntü, seçilen katmanı göstermek üzere anında güncellenir

**Hızlı geçiş:**

* Açılır menü son seçiminizi hatırlar
* Bir sonraki görüntüye geçerken, Chloros aynı katman türünü göstermeye çalışır
* Bu katman bir sonraki görüntüde mevcut değilse, varsayılan olarak JPG kullanılır

### Katman Kullanılabilirliği

Her görüntü için tüm katmanlar mevcut değildir:

**Her zaman mevcut:*** ✅ JPG (her görüntünün bir JPG önizlemesi vardır)

**Koşullu olarak mevcut:**

* ⚠️ RAW (Orijinal) - Yalnızca görüntü RAW veya RAW+JPG modunda çekilmişse
* ⚠️ RAW (Hedef) - Yalnızca görüntü algılanan kalibrasyon hedefleri içeriyorsa
* ⚠️ RAW (Yansıma) - Yalnızca yansıma kalibrasyonu etkinleştirilmiş olarak işlendikten sonra
* ⚠️ RAW (\[İndeks] İndeks) - Yalnızca indeksler yapılandırılmış olarak işlendikten sonra

***

## Katman Kalıcılığı

### Görüntüler Arasında Gezinme

Farklı bir görüntüye geçtiğinizde (ok tuşlarını kullanarak veya küçük resimleri tıklayarak):**Katman tercihi korunur:**

* &quot;RAW (Yansıma)&quot; görüntüleniyorsa, sonraki görüntü &quot;RAW (Yansıma)&quot; olarak gösterilir (varsa)
* &quot;RAW (NDVI İndeks)&quot; görüntüleniyorsa, sonraki görüntü &quot;RAW (NDVI İndeks)&quot; olarak gösterilir (varsa)
* Aynı katman mevcut değilse, varsayılan olarak JPG kullanılır

**Örnek iş akışı:**

1. Görüntü 1&#x27;i açın, RAW (NDVI Endeksi) moduna geçin
2. → tuşuna basarak Görüntü 2&#x27;yi görüntüleyin
3. Görüntü 2 otomatik olarak RAW (NDVI Endeksi) katmanını gösterir
4. Gezinmeye devam edin - tüm görüntülerde NDVI katmanı gösterilir
5. Birçok görüntüde indeks sonuçlarını incelemek için çok verimlidir

***

## Yaygın İş Akışları

### İş Akışı 1: Öncesi/Sonrası Karşılaştırması

**Amaç**: Orijinal ile kalibre edilmiş görüntüyü karşılaştırmak

1. İşlenmiş görüntüyü Görüntü Görüntüleyicide açın
2. Açılır menüden **RAW (Orijinal)**&#x27;i seçin
3. Vinyetlemeyi ve kalibre edilmemiş değerleri not edin
4. Açılır menüden **RAW (Yansıma)**&#x27;e geçin
5. Karşılaştırın - vinyetleme kaldırıldı, değerler kalibre edildi

### İş Akışı 2: Dizin İncelemesi

**Amaç**: Veri kümesindeki NDVI sonuçlarını hızlıca incelemek

1. İlk işlenmiş görüntüyü açın
2. Açılır menüden **RAW (NDVI Dizin)**&#x27;i seçin
3. → ok tuşunu kullanarak bir sonraki görüntüye geçin
4. NDVI katmanı otomatik olarak kalır
5. Tüm görüntülere devam edin ve NDVI desenlerini kontrol edin
6. Karşılaştırmak için **RAW (NDRE Index)**&#x27;e geçin

### İş Akışı 3: Hedef Doğrulama

**Amaç**: Tüm hedef görüntülerinin doğru şekilde algılandığını doğrulayın

1. Bir hedef görüntüye gidin
2. Açılır menüden **RAW (Target)**&#x27;i seçin
3. Kalibrasyon hedeflerinin net bir şekilde görülebilir ve algılanabilir olduğunu doğrulayın
4. Bir sonraki hedef görüntüye gidin
5. Tüm hedefler için doğrulamayı tekrarlayın

### İş Akışı 4: Piksel Değeri Denetimi

**Amaç**: Bilimsel doğruluk açısından yansıma değerlerini kontrol etmek

1. İşlenmiş görüntüyü açın
2. **RAW (Yansıma)** katmanını seçin
3. **Piksel Yüzdesi** modunu etkinleştirin (sağ üst araç çubuğundaki düğme)
4. İmleci bitki örtüsü alanlarının üzerine getirin
5. Piksel değerlerinin beklenen aralıklarda olduğunu doğrulayın (NIR için %30-70, Red için %5-15)
6. Toprak ve su alanlarında değerlerin uygun olup olmadığını kontrol edin

***

## Katmanlara Göre Piksel Değerlerini Anlama

Farklı katmanlar farklı piksel değer aralıkları gösterir:

### JPG Katmanı

* **Aralık**: 0-255 (8 bit)
* **Anlam**: Görüntü değerleri, gama düzeltmeli
* **Kullanım**: Yalnızca görsel inceleme için, bilimsel ölçüm için değil

### RAW (Orijinal)

* **Aralık**: 0-65535 (16 bit)
* **Anlam**: Ham sensör dijital sayıları
* **Kullanım**: Sensör performansını kontrol etmek için, kalibre edilmemiştir

### RAW (Yansıma)

* **Aralık**: 0-65.535 (16 bit TIFF) veya 0,0-1,0 (32 bit Yüzde)
* **Anlam**: Kalibre edilmiş yüzde yansıma
* **Kullanım**: Bilimsel ölçümler ve analizler**16 bit TIFF için:**Yüzde yansıma değerini elde etmek için 65.535&#x27;e bölün**32 bit Yüzde için:** Değerler doğrudan yüzdeyi temsil eder (0,5 = %50 yansıma)

### RAW (İndeks Görüntüleri)

* **Aralık**: İndekse göre değişir (normalize edilmiş indeksler için genellikle -1,0 ila +1,0)
* **Anlam**: İndeks hesaplama sonucu
* **Örnekler**:
  * NDVI: -1 ila +1 (bitki örtüsü genellikle 0,4 ila 0,9)
  * NDRE: -1 ile +1 arası (stres tespiti)
  * EVI: 0 ile 1 arası (geliştirilmiş bitki örtüsü)

***

## İpuçları ve En İyi Uygulamalar

### Verimli Katman Değiştirme

* **Klavye kısayolları hakkında bilgi**: Katmanlar için klavye kısayolu bulunmamakla birlikte, gezinme okları (←/→) tüm katmanlarda çalışır
* **Tutarlı iş akışları**: Bir katman seçin (örn. NDVI) ve başka bir katmana geçmeden önce tüm veri setini inceleyin
* **Hızlı karşılaştırmalar**: İşleme kalitesini doğrulamak için Orijinal ve Yansıma arasında geçiş yapın

### Performansla İlgili Hususlar

* **JPG en hızlı yüklenir**: Çok sayıda görüntü arasında hızlı gezinmek için kullanın
* **RAW katmanları daha yavaş yüklenir**: Daha yüksek çözünürlük ve bit derinliği
* **Dizin katmanları**: Yansıma katmanlarına benzer hız
* **İlk yükleme en yavaştır**: Aynı katmanın sonraki görüntülemeleri önbelleğe alınır ve daha hızlıdır

### Kalite Doğrulama

* **Her zaman RAW&#x27;ı (Orijinal) kontrol edin**: İşlenmiş çıktılara güvenmeden önce kaynak veri kalitesini doğrulayın
* **Katmanları karşılaştırın**: İşlemenin doğru çalıştığını doğrulamak için katman değiştirme özelliğini kullanın
* **Dizin aralıklarını kontrol edin**: Değerlerin makul olduğunu doğrulamak için dizin katmanlarıyla Piksel Yüzde modunu kullanın***

## Sorun Giderme

### Katman Kullanılamıyor

**Sorun**: Beklenen katman açılır menüde görünmüyor**Olası nedenler:**

* Görüntü işlenmedi (sadece JPG ve RAW (Orijinal) kullanılabilir)
* İşleme sırasında yansıma kalibrasyonu devre dışı bırakıldı
* Proje Ayarlarında belirli bir indeks yapılandırılmadı
* Görüntü sadece hedef içeren bir görüntüdür (hedefler için indeks oluşturulmaz)

**Çözümler:**

1. Görüntünün işlendiğini doğrulayın (işlenmiş dosyalar için çıktı klasörünü kontrol edin)
2. İndekslerin yapılandırıldığını doğrulamak için Proje Ayarlarını kontrol edin
3. İstenen indeksler etkinleştirilmiş olarak yeniden işleyin

### Yanlış Katman Gösteriliyor

**Sorun**: Görüntü beklenmedik bir katmanda açılıyor**Neden**: Önceki görüntünün katman tercihi devralındı, ancak o katman mevcut görüntüde mevcut değil**Çözüm**: Chloros, tercih edilen katman mevcut olmadığında otomatik olarak JPG&#x27;ye geri döner - bu normal bir davranıştır

### Kalibrasyon Hedefleri Görünmüyor

**Sorun**: RAW (Hedef) katmanı hedef algılamasını göstermiyor**Olası nedenler:**

* İşleme sırasında hedefler algılanmadı
* Görüntü aslında hedefler içermiyor
* Hedef algılama ayarları çok katı

**Çözümler:**

1. Hata Giderme Günlüğünde &quot;Hedef bulundu&quot; mesajlarını kontrol edin
2. Görüntünün gerçekten görünür kalibrasyon hedefleri içerdiğini doğrulayın
3. Proje Ayarları&#x27;nda hedef algılama ayarlarını değiştirin
4. [Hedef Görüntüleri Seçme](../processing-images-gui/choosing-target-images.md) bölümüne bakın

***

## İlgili Özellikler

### Görüntü Görüntüleyici Araçları

Herhangi bir katmanı görüntülerken şunları kullanabilirsiniz:

* **Yakınlaştırma kontrolleri**: Ayrıntıları incelemek için yakınlaştırın
* **Kaydırma**: Yakınlaştırılmış görüntüyü hareket ettirmek için tıklayın ve sürükleyin
* **Piksel değeri inceleme**: İmlecin bulunduğu konumdaki değerleri görün
* **Gezinme okları**: Katmanı koruyarak görüntüler arasında geçiş yapın
* **Piksel Yüzde modu**: DN ve yüzde gösterimi arasında geçiş yapın

Görüntü Görüntüleyicisi ile ilgili tüm belgeler için [Görüntüyü Tam Ekran Olarak Açma](opening-an-image-full-screen.md) bölümüne bakın.

### Dizin/LUT Sandbox

Etkileşimli dizin testi ve görselleştirme için:

* **Gerçek zamanlı dizin hesaplama**: Farklı dizin formüllerini test edin
* **LUT renk eşleme**: Gri tonlamalı dizinlere renk gradyanları uygulayın
* **Görselleştirmeleri dışa aktarın**: Renkli dizin görüntülerini kaydedin

Ayrıntılar için [Dizin/LUT Sandbox](index-lut-sandbox.md) bölümüne bakın.

***

## Sonraki Adımlar

Artık görüntü katmanlarını anladığınıza göre:

* [**Bir Görüntüyü Tam Ekran Olarak Açma**](opening-an-image-full-screen.md) - Tam Image Viewer kılavuzu
* [**İndeks/LUT Sandbox**](index-lut-sandbox.md) - Etkileşimli indeks görselleştirme
* [**Çok Spektral İndeks Formülleri**](../project-settings/multispectral-index-formulas.md) - Kullanılabilir indeksler referansı
* [**İşlemeyi Tamamlama**](../processing-images-gui/finishing-the-processing.md) - İşlenmiş çıktıları anlama
