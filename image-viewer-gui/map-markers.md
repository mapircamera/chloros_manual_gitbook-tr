# Harita İşaretçileri

Harita sekmesi, görüntülerinizi GPS koordinatlarına göre etkileşimli bir 2D harita üzerinde gösterir. Bu, çekim oturumunuzun coğrafi bir genel görünümünü sunar ve alan kapsamını görselleştirmenize yardımcı olur. Ayrıca, görüntülerinizi ilk kez içe aktarırken, işlenmesine gerek olmayan görüntüleri hızlı bir şekilde ayıklamak için de kullanışlıdır.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Harita Sekmesine Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. GPS meta verileri içeren görüntüleri içe aktarın
3. Sol kenar çubuğundaki **Harita** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> sekmesine tıklayın
4. Harita, her görüntünün GPS konumunda işaretçiler gösterecektir

{% hint style="info" %}
**GPS Gerekli**: Yalnızca EXIF meta verilerinde gömülü GPS koordinatları bulunan görüntüler haritada görünecektir. Çekim sırasında kameranızda GPS&#x27;in etkinleştirildiğinden emin olun.
{% endhint %}

***

## Harita Sekmesinden Görüntüleri Ayarlama**Harita** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> sekmesinde de aynı ekle  <img src="../.gitbook/assets/image.png" alt="" data-size="line">   <img src="../.gitbook/assets/image (1).png" alt="" data-size="line">  ve kaldırma  <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">  düğmelerine sahiptir. Ayrıca aynı proje dosyası tablosu listesini gösterir, ancak sütun başlıkları farklıdır:  ### Dosya Adı  * Kameradan alınan orijinal dosya adı * Kamera adlandırma kuralını korur (ör. IMG\_000001XPROTX) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesindekilerle aynıdır. Ayrıca aynı proje dosyası tablosu listesini gösterir, ancak sütun başlıkları farklıdır:

### Dosya Adı

* Kameradan alınan orijinal dosya adı
* Kamera adlandırma kuralını korur (ör. IMG\_0001.RAW)

### Enlem

* Görüntünün enlemi

### Boylam

* Görüntünün boylamı

### Yükseklik

* Görüntünün yüksekliği

{% hint style="info" %}
Tablo sütun başlıklarına tıklamak, satır verilerini de sıralar
{% endhint %}

***

## Görüntü İşaretçileri

GPS verisi olan her görüntü, harita üzerinde bir işaretçi ile gösterilir:

### İşaretçi Görüntüleme

* İşaretçiler, her görüntünün çekildiği tam GPS koordinatlarını gösterir
* Yakınlaştırma azaltıldığında, kümelenmiş işaretçiler bir araya gelebilir
* Tek tek görüntü konumlarını görmek için yakınlaştırın

{% hint style="success" %}
SÜPER YAKINLAŞTIRMA: Harita döşeme sağlayıcısından maksimum yakınlaştırma düzeyine ulaştığınızda, döşeme daha da yakınlaştırıldığında büyütülür ve birbirine yakın işaretçileri görmenizi sağlar.
{% endhint %}

### Fareyle Üzerine Gelme Önizlemesi

* Herhangi bir işaretçinin üzerine **fareyi getirin**, o görüntünün küçük resim önizlemesini görmek için
* Bu, harita görünümünden ayrılmadan hızlı görsel tanımlama sağlar
* Büyük bir çekim oturumu içinde belirli görüntüleri bulmak için kullanışlıdır

***

## Harita Döşeme Sağlayıcıları

{% hint style="success" %}
**Otomatik Seçim**: Chloros, mevcut harita konumunuz için en iyi yakınlaştırma düzeyini sağlayan döşeme hizmetini otomatik olarak seçer. İsterseniz sağlayıcılar arasında manuel olarak geçiş yapabilirsiniz.
{% endhint %}

Harita sekmesi, arka plan harita görüntüleri için iki döşeme sağlayıcısını destekler:

### Google Haritalar

* Google&#x27;dan standart uydu ve harita görüntüleri
* Genel olarak dünya çapında kapsama için en iyisi

### ESRI

* ESRI ArcGIS&#x27;ten uydu ve hava görüntüleri
* Belirli bölgelerde genellikle daha yüksek çözünürlüklü görüntüler sağlar

***

## Harita Döşeme Türleri

Harita katmanı türünü (soldan sağa doğru) seçebilirsiniz:

 <img src="../.gitbook/assets/image (23).png" alt="" data-size="original">### Arazi

Yükseklik profillerini ve ayrıntıları (yollar vb.) içeren harita döşemelerini gösterir

### Harita

Ayrıntıları (yollar vb.) içeren standart (düşük bant genişliği) harita döşemelerini gösterir

### Uydu

Ayrıntılı (yüksek bant genişliği) uydu harita döşemelerini gösterir

### Hibrit

Ek ayrıntılar (yollar vb.) içeren uydu harita döşemelerini gösterir

***

## Harita Gezinme

### Yakınlaştırma/Uzaklaştırma Kontrolleri

* **Yakınlaştırma/Uzaklaştırma**: Fare tekerleğini veya yakınlaştırma düğmelerini kullanın
* **Tam Ekran**: Haritayı tam ekrana alın

### Kaydırma Kontrolleri

* **Kaydırma**: Tıklayıp sürükleyerek harita üzerinde hareket edin***

## Kullanım Örnekleri

### Uçuş Yolu Görselleştirme

* Drone çekim oturumlarının kapsama alanını görüntüleyin
* Görüntü kapsamındaki boşlukları belirleyin
* Uçuş yolunun uygulanmasını doğrulayın

### Yer Etüdü İncelemesi

* Yer tabanlı çekimlerin uzamsal dağılımını görün
* Etüt alanına göre kalibrasyon hedef görüntülerini bulun
* Ek çekim konumları planlayın

### Kalite Kontrol

* Beklenmedik konumlarda çekilen görüntüleri hızla belirleyin
* Veri kümesi genelinde GPS doğruluğunu doğrulayın
* Görüntü konumlarını saha notlarıyla karşılaştırın

***

## Sorun Giderme

### İşaretçiler Görünmüyor

**Olası nedenler:**

* Görüntüler GPS meta verilerini içermiyor
* Çekim sırasında kamerada GPS devre dışı bırakılmıştı
* EXIF verileri harici bir yazılım tarafından silinmiş

**Çözüm**: Kameranızda GPS&#x27;in etkinleştirildiğini doğrulayın ve orijinal dosyaları yeniden içe aktarın

### İşaretçiler Yanlış Konumda

**Olası nedenler:**

* Kamera GPS&#x27;inin uydu sinyali zayıftı
* Çekim sırasında GPS sapması yaşandı

**Çözüm**: Bu genellikle çekim zamanıyla ilgili bir sorundur; hassas uygulamalar için PPK/RTK GPS kullanmayı düşünün
