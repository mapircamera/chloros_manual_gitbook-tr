# Harita İşaretçileri

Harita sekmesi, görüntülerinizi GPS koordinatlarına göre etkileşimli bir 2D harita üzerinde gösterir. Bu sekme, bir çekim oturumuna ilişkin coğrafi bir genel bakış sunar ve içe aktarımdan hemen sonra, işlemek istemediğiniz görüntüleri elemeyi sağlayan en hızlı yoldur.

<figure><img src="../.gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

## Harita Sekmesine Erişme

1. Chloros&#x27;te bir proje açın veya oluşturun
2. GPS meta verileri içeren görüntüleri içe aktarın
3. Sol kenar çubuğundaki **Harita** <img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> sekmesine tıklayın
4. Harita, her görüntünün GPS konumunda bir işaretçi gösterir

{% hint style="info" %}
**GPS gereklidir**: Yalnızca EXIF meta verilerinde GPS koordinatları bulunan görüntüler haritada görünür. Koordinatı olmayan bir görüntü yine de projede kalır ve normal şekilde işlenir — sadece üzerinde işaretçi bulunmaz.
{% endhint %}

***

## Harita Sekmesinden Görüntüleri Düzenleme**Harita**<img src="../.gitbook/assets/image (3) (1).png" alt="" data-size="line"> sekmesi, [**Dosya Tarayıcı**](../processing-images-gui/adding-files-to-a-project.md) <img src="../.gitbook/assets/icon_file-browser.JPG" alt="" data-size="line"> sekmesindeki ile aynı ekle <img src="../.gitbook/assets/image (3).png" alt="" data-size="line"> <img src="../.gitbook/assets/image (1) (1).png" alt="" data-size="line"> ve kaldır <img src="../.gitbook/assets/image (2) (1).png" alt="" data-size="line"> dosya düğmelerine sahiptir. Coğrafi sütunlar içeren aynı proje dosyası listesini gösterir:

| Sütun        | İçerik                                                           |
| ------------- | ------------------------------------------------------------------ |
| **Ad**      | Kameradan alındığı haliyle dosya adı                             |
| **Enlem**  | Ondalık derece, altı ondalık basamak                                |
| **Boylam**  | Ondalık derece, altı ondalık basamak                                |
| **Rakım**  | Metre, bir ondalık basamak — Görüntüde rakım bilgisi yoksa `-` |

{% hint style="info" %}
Sıralamak istediğiniz sütun başlığını tıklayın; sırayı tersine çevirmek için tekrar tıklayın.
{% endhint %}

{% hint style="warning" %}
**Rakım, yerden yükseklik değil, deniz seviyesinden yüksekliktir.** Bu değer, görüntünün EXIF `GPSAltitude` etiketinden alınır ve bu etiket, ortalama deniz seviyesine göre ifade edilir. Bu, arazi üzerindeki uçuş yüksekliği değildir ve Chloros bu değerden yer örnekleme mesafesini hesaplamaz — deniz seviyesinden 300 m yükseklikteki bir tarlanın üzerinde, yer seviyesinden (AGL) 100 m yükseklikteki bir drone burada yaklaşık 400 m olarak kaydedilir. Bu sütunu, AGL ölçümü olarak değil, aykırı değerleri tespit etmek ve tutarlı bir uçuş irtifasını doğrulamak için kullanın.
{% endhint %}

***

## Görüntü İşaretçileri

GPS verileri içeren her görüntünün koordinatlarına bir işaretçi eklenir.

### İşaretçilerin görüntülenmesi

* İşaretçiler, her çekim için kaydedilen tam koordinatlarda yer alır
* Birbirine yakın işaretçiler, uzaklaştırıldığında görsel olarak üst üste binmiş gibi görünebilir — bunları ayırmak için yakınlaştırın
* Seçili ve vurgulanmış işaretçiler, diğerlerinin üzerinde gösterilir

### Fareyle Üzerine Gelme Önizlemesi

* Herhangi bir işaretçinin üzerine **fareyle gelin**, o görüntünün dosya adıyla birlikte küçük resmini gösteren bir açılır pencere açılır
* Bir işaretçiyi **tıklayın**, görüntüyü seçin ve açılan pencereyi**sabitleyin** — başka bir yere tıklayana kadar açık kalır. Açılan pencere sabitlendiğinde, diğer işaretçilerin üzerine fareyi getirseniz de pencere kaybolmaz
* Bu, haritadan ayrılmadan büyük bir oturumda belirli bir kareyi bulmanın hızlı yoludur

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Harita sekmesi, projedeki coğrafi etiketli tüm görüntüleri gösterir</p></figcaption></figure>### Süper yakınlaştırma

{% hint style="success" %}
**SÜPER YAKINLAŞTIRMA**: Karo sağlayıcısının görüntü sunduğu maksimum yakınlaştırma düzeyine ulaştığınızda, daha fazla yakınlaştırma işlemi durmak yerine karoları büyütür; böylece neredeyse üst üste binen işaretçileri birbirinden ayırabilirsiniz.
{% endhint %}

* Süper yakınlaştırma, yalnızca o konum için sağlayıcının maksimum yakınlaştırma seviyesine **ulaştığınızda** ve döşemelerin yüklenmesi tamamlandığında devreye girer. Bu seviyenin altında, yakınlaştırma normal şekilde çalışır
* Aralık, sağlayıcının kendi maksimum değerinin üzerine **1× ila 32×** arasındadır
* Köşedeki bir gösterge, mevcut süper yakınlaştırma düzeyini yüzde olarak gösterir; yanındaki **×** düğmesine tek bir tıklamayla normal yakınlaştırma düzeyine geri dönebilirsiniz
* Uzaklaştırma işlemi her zaman haritanın kendisine yansıtılır; bu sayede asla süper yakınlaştırma modunda sıkışıp kalmazsınız
* Süper yakınlaştırma durumundayken yakınlaştırma ve kaydırma yapıldığında, ortaya çıkan kayma haritaya yansıtılır; böylece merkezden uzaklaştığınız alan boş kalmak yerine döşeme talep etmeye devam eder
* İşaretçiler rasterleştirilmek yerine vektör öğeleri olarak çizilir; bu sayede her süper yakınlaştırma düzeyinde net kalırlar

***

## Harita Karosu Sağlayıcıları

{% hint style="success" %}
**Otomatik seçim**: Chloros, görüntülerinizin bulunduğu yer için en iyi yakınlaştırma düzeyini sunan karo hizmetini seçer. İstediğiniz zaman manuel olarak değiştirebilirsiniz.
{% endhint %}

| Sağlayıcı        | Notlar                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Google Haritalar** | Dünya çapında geniş kapsama alanı; dört döşeme türünün tümünü destekler                                                                                                            |
| **Esri ArcGIS**| Belirli bölgelerde genellikle daha yüksek çözünürlüklü hava görüntüleri sunar.**Arazi** döşeme türü Esri için sunulmaz ve Esri seçiliyken ilgili düğme devre dışıdır |***

## Harita Döşeme Türleri

Düğmeleri kullanarak (soldan sağa doğru) harita katmanı türünü seçin:

![](&lt;../.gitbook/assets/image (14).png&gt;)

| Tür                 | Gösterilenler                                                                |
| -------------------- | -------------------------------------------------------------------- |
| **Arazi**          | Harita ayrıntılarıyla (yollar, etiketler) birlikte yükseklik gölgelendirmesi. Yalnızca Google       |
| **Harita**              | Standart sokak haritası döşemeleri — en düşük bant genişliği seçeneği              |
| **Uydu**        | Ayrıntılı uydu görüntüleri, etiket yok — en yüksek bant genişliği gerektiren seçenek |
| **Hibrit** (varsayılan) | Üzerine yollar ve etiketler çizilmiş uydu görüntüleri                |**Harita**sekmesi**Hibrit** modunda açılır. Seçiminiz, sağlayıcının desteklediği durumlarda sağlayıcı değişikliğine de yansır.***

## Harita Gezinme

* **Yakınlaştırma**: fare tekerleği veya harita üzerindeki yakınlaştırma düğmeleri
* **Kaydırma**: tıklayıp sürükleyin
* **Tam Ekran**: tam ekran kontrolü, haritayı pencerenin tamamını kaplayacak şekilde genişletir***

## Kullanım Örnekleri

### Uçuş rotası incelemesi

* Bir drone seansının kapsama alanını bir bakışta görün
* Geçişin atlandığı boşlukları tespit edin
* Uçuşun planlanan rotayı takip ettiğini doğrulayın

### Yer ölçümü incelemesi

* Yer tabanlı çekimlerin nasıl dağıldığını görün
* Kalibrasyon hedef çerçevelerini ölçüm alanına göre konumlandırın
* Ek çekimlerin nerede gerekli olduğuna karar verin

### Kalite kontrol

* Beklenmedik bir yerde çekilmiş görüntüleri bulun ve işleme öncesinde bunları kaldırın
* Yükseklik&#x27;e göre sıralayarak yanlış yükseklikte çekilmiş veya GPS sinyalinin zayıf olduğu bir kareyi tespit edin
* Görüntü konumlarını saha notlarıyla karşılaştırın

***

## Sorun Giderme

### İşaretçiler görünmüyor

**Olası nedenler**

* Görüntülerde GPS meta verileri bulunmuyor
* Çekim sırasında kamerada GPS devre dışı bırakılmıştı
* İçe aktarımdan önce EXIF verileri başka bir yazılım tarafından silinmiş

**Ne yapmalı**: Kamerada GPS&#x27;in etkin olduğunu doğrulayın ve orijinal dosyaları yeniden içe aktarın. Harita sekmesindeki dosya tablosunda aradığınız dosyayı bularak, belirli bir dosyanın koordinatları olup olmadığını kontrol edebilirsiniz — koordinatları olmayan bir görüntünün bu tabloda satırı yoktur.

### İşaretçiler yanlış yerde

**Olası nedenler**: çekim anında zayıf uydu sinyali veya oturum sırasında GPS sapması.**Ne yapmalı**: Bu, Chloros&#x27;in sonradan düzeltebileceği bir sorun değil, çekim anında ortaya çıkan bir sorundur. Hassas çalışmalar için bir PPK/RTK GPS iş akışı kullanın — [Proje Ayarları](../project-settings/project-settings.md) bölümündeki**PPK düzeltmelerini uygula** ayarına bakın.

### Harita boş veya döşemeler yüklenmiyor

Döşeme sağlayıcıları çevrimiçi hizmetlerdir. Döşemeler gelmezse, cihazın ağ bağlantısını kontrol edin, ardından sağlayıcıyı değiştirmeyi deneyin. Çok fazla yakınlaştırmışsanız, **×** sıfırlama düğmesine basarak normal yakınlaştırma düzeyine dönün ve haritanın döşemeleri yeniden talep etmesini bekleyin.***

## İlgili sayfalar

* [**Görüntü Izgarası**](image-grid.md) — küçük resimler olarak kullanılan aynı görüntü kümesi
* [**Bir Görüntüyü Tam Ekran Olarak Açma**](opening-an-image-full-screen.md) — bir görüntüyü ayrıntılı olarak inceleme
* [**Projeye Dosya Ekleme**](../processing-images-gui/adding-files-to-a-project.md) — bu sekmede bulunan dosya ekle/çıkar düğmeleri
