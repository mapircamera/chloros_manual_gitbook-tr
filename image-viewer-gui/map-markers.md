# Harita İşaretleyicileri

Harita sekmesi, görüntülerinizi GPS koordinatlarına göre etkileşimli bir 2D harita üzerinde görüntüler. Bu, çekim oturumunuzun coğrafi bir özetini sunar ve uzamsal kapsama alanını görselleştirmenize yardımcı olur. Ayrıca, görüntülerinizi ilk kez içe aktarırken, işlemek istemediğiniz görüntüleri hızlı bir şekilde kaldırmak için de kullanışlıdır.

## Harita Sekmesine Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun.
2. GPS meta verileri içeren görüntüleri içe aktarın.
3. Sol kenar çubuğundaki **Harita** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> sekmesine tıklayın
4. Harita, her görüntünün GPS konumunda işaretler gösterecektir

{% hint style=&quot;info&quot; %}
**GPS Gerekli**: Yalnızca EXIF meta verilerinde gömülü GPS koordinatları bulunan görüntüler haritada görünecektir. Çekim sırasında kameranızın GPS özelliğinin etkin olduğundan emin olun.
{% endhint %}

***

## Harita Sekmesinden Görüntüleri Ayarlama**Harita** <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> sekmesi, ekleme  <img src="../.gitbook/assets/image.png" alt="" data-size="line">   <img src="../.gitbook/assets/image (1).png" alt="" data-size="line">  ve kaldırma  <img src="../.gitbook/assets/image (2).png" alt="" data-size="line">  dosya düğmelerine sahiptir. Ayrıca, aynı proje dosyası tablo listesini gösterir, ancak sütun başlıkları farklıdır: <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesi ile aynı ekle

### Dosya Adı

* Kameradan alınan orijinal dosya adı
* Kamera adlandırma kuralını korur (ör. IMG\_0001.RAW)

### Enlem

* Görüntünün enlemi

### Boylam

* Görüntünün boylamı

### Rakım

* Görüntünün rakımı

{% hint style=&quot;info&quot; %}
Tablo sütun başlıklarına tıklandığında satır verileri de sıralanır.
{% endhint %}

***

## Görüntü İşaretçileri

GPS verileri içeren her görüntü, harita üzerinde bir işaretçi ile gösterilir:

### İşaretçi Görüntüleme

* İşaretçiler, her görüntünün çekildiği tam GPS koordinatlarını gösterir.
* Yakınlaştırıldığında, kümelenmiş işaretçiler bir araya gelebilir.
* Tek tek görüntü konumlarını görmek için yakınlaştırın.

{% hint style=&quot;success&quot; %}
SÜPER YAKINLAŞTIRMA: Harita döşeme sağlayıcısından maksimum yakınlaştırma düzeyine ulaştığınızda, döşeme daha da yakınlaştırıldığında büyütülür ve birbirine yakın işaretleri görebilirsiniz.
{% endhint %}

### Fareyle Önizleme

* **Farenizi** herhangi bir işaretin üzerine getirin, o görüntünün küçük bir önizlemesini görmek için.
* Bu, harita görünümünden ayrılmadan hızlı görsel tanımlama sağlar.
* Büyük bir yakalama oturumu içinde belirli görüntüleri bulmak için kullanışlıdır.

***

## Harita Döşeme Sağlayıcıları

{% hint style=&quot;success&quot; %}
**Otomatik Seçim**: Chloros, mevcut harita konumunuz için en iyi yakınlaştırma düzeyini sağlayan döşeme hizmetini otomatik olarak seçer. İsterseniz sağlayıcılar arasında manuel olarak geçiş yapabilirsiniz.
{% endhint %}

Harita sekmesi, arka plan harita görüntüleri için iki döşeme sağlayıcısını destekler:

### Google Haritalar

* Google&#x27;dan standart uydu ve harita görüntüleri
* Genel dünya kapsamı için en iyisi

### ESRI

* ESRI ArcGIS&#x27;ten uydu ve hava görüntüleri
* Genellikle belirli bölgelerde daha yüksek çözünürlüklü görüntüler sağlar

***

## Harita Döşeme Türleri

Harita katmanı türünü (soldan sağa) seçebilirsiniz:

 <img src="../.gitbook/assets/image (23).png" alt="" data-size="original">### Arazi

Yükseklik profillerini ve ayrıntıları (yollar vb.) içeren harita döşemelerini gösterir.

### Harita

Ayrıntıları (yollar vb.) içeren standart (daha düşük bant genişliği) harita döşemelerini gösterir.

### Uydu

Ayrıntılı (daha yüksek bant genişliği) uydu harita döşemelerini gösterir.

### Hibrit

Ek ayrıntılar (yollar vb.) içeren uydu harita döşemelerini gösterir.

***

## Harita Navigasyonu

### Yakınlaştırma Kontrolleri

* **Yakınlaştırma/Uzaklaştırma**: Fare kaydırma tekerleğini veya yakınlaştırma düğmelerini kullanın.
* **Tam Ekran**: Haritayı tam ekrana getirin.

### Kaydırma Kontrolleri

* **Kaydırma**: Harita üzerinde hareket etmek için tıklayın ve sürükleyin.***

## Kullanım Örnekleri

### Uçuş Rotası Görselleştirme

* Drone çekim oturumlarının kapsama alanını görüntüleyin
* Görüntü kapsamındaki boşlukları belirleyin
* Uçuş rotasının uygulanmasını doğrulayın

### Yer Ölçümü İncelemesi

* Yer tabanlı çekimlerin uzamsal dağılımını görün
* Ölçüm alanına göre kalibrasyon hedef görüntülerini bulun
* Ek çekim konumları planlayın

### Kalite Kontrol

* Beklenmedik konumlarda çekilen görüntüleri hızlıca belirleyin.
* Veri kümesinde GPS doğruluğunu doğrulayın.
* Görüntü konumlarını saha notlarıyla çapraz referanslayın.

***

## Sorun Giderme

### İşaretler Görünmüyor

**Olası nedenler:**

* Görüntüler GPS meta verileri içermiyor.
* Çekim sırasında kamerada GPS devre dışı bırakılmış.
* EXIF verileri harici yazılım tarafından silinmiş.

**Çözüm**: Kameranızda GPS&#x27;in etkinleştirildiğini doğrulayın ve orijinal dosyaları yeniden içe aktarın.

### İşaretler Yanlış Konumda

**Olası nedenler:**

* Kamera GPS&#x27;inin uydu sabitlemesi zayıftı.
* Çekim sırasında GPS kayması oldu.

**Çözüm**: Bu genellikle çekim zamanı ile ilgili bir sorundur; hassas uygulamalar için PPK/RTK GPS kullanmayı düşünün.
