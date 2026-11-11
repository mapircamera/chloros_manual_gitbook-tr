# GUI: Projeler

Chloros, ileride yeniden açılabilecek projeler oluşturmanıza olanak tanır. Bir proje, (Proje Klasörünüzün içindeki) aşağıdakileri içeren basit bir klasördür:

* `project.json` — proje ayarları, dosya listesi ve görüntüleme tercihleri
* `cameras.json` — proje açıkken bağlanmış kameralar ve diziler ile bunların ayarları
* `sensors.json` — proje açıkken bağlı olan DAQ ışık sensörleri ve kamera↔sensör eşlemeleri
* yakalamalarınız, `.daq` kayıtlarınız ve işlenmiş çıktı klasörleri

Özel bir proje dosyası biçimi yoktur — klasör ve içindeki JSON dosyaları projenin kendisini oluşturur; bu da projelerin kopyalanmasını, arşivlenmesini ve [CLI](CLI.md) veya [Python SDK](api-python-sdk.md) üzerinden çalıştırılmasını kolaylaştırır.

[Proje Ayarları](project-settings/project-settings.md) bölümünden değiştirmediğiniz sürece, Proje Klasörü Belgeler klasörünüzde `Documents\MAPIR\Chloros Projects` adıyla bulunur (Linux&#x27;te `~/Documents/MAPIR/Chloros Projects`).

{% hint style="warning" %}
**Windows: Uzun yollar özelliği etkin değilse, proje klasör adlarını kısa tutun.**Windows, uzun yollar özelliği devre dışı olarak gelir; bu durumda hiçbir dosya yolu 260 karakterden uzun olamaz. İşlenmiş ürünler, proje klasörünün yaklaşık 130 karakter altına yerleştirilir; bu nedenle 1.3.0 sürümünden itibaren Chloros, çok derin bir proje klasörünü reddeder — bunu oluştururken, açarken veya işlemeyi başlatırken — hiçbir şey yazmadan çalışmak yerine. Mesajda, sığabilecek en uzun proje klasörü belirtilir. Daha kısa bir Proje Klasörü kullanın veya Windows&#x27;ta uzun yol özelliğini etkinleştirin (kurulum programındaki**Windows&#x27;ta uzun yolları etkinleştir** seçeneği, varsayılan olarak işaretlidir) ve Chloros&#x27;u yeniden başlatın. Bkz. [İşlemeyi Başlatma](processing-images-gui/starting-the-processing.md#the-project-folder-is-too-deep-for-this-computer).
{% endhint %}

## Yeni Proje

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Ana menüden &quot;Yeni Proje&quot;yi seçin ve projeniz için benzersiz bir ad girin.

Herhangi bir proje şablonu kaydettiyseniz, ad alanının altında **Şablon Seç** açılır menüsü görünür — bunlardan birini seçtiğinizde yeni proje o şablonun ayarlarıyla başlatılır. Şablonlar [Proje Ayarları](project-settings/project-settings.md) bölümünden kaydedilir: &quot;Proje Şablonu Adını Kaydet&quot; alanına bir ad girin ve kaydet simgesine tıklayın.

## Projeyi Aç

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Proje Aç, proje klasörünüzdeki tüm projeleri listeler; alt kısımda ise <strong>Proje Klasörünü Aç</strong> seçeneği bulunur</p></figcaption></figure>

&quot;Projeyi Aç&quot; seçeneğini seçerek Proje Klasöründeki mevcut projelerin listesini görüntüleyebilirsiniz. Herhangi bir proje yoksa ikincil yan menü açılmaz. Yukarıdaki fotoğrafta GUI ile oluşturulmuş bazı projeler (t1, t2, t3) listelenmiştir. DATE\_TIME projeleri, varsayılan proje adlandırma şeması kullanılarak CLI tarafından oluşturulmuştur. Herhangi bir proje adına tıkladığınızda proje açılır.

&quot;Proje Klasörünü Aç&quot; düğmesine tıkladığınızda, bilgisayarınızın dosya gezgini proje yolunda açılır. Proje yolunu [Proje Ayarları](project-settings/project-settings.md) bölümünden ayarlayabilirsiniz.

Proje en son açıldığından bu yana kaynak görüntü dosyalarından herhangi biri taşınmış veya silinmişse, Chloros boş bir ızgara açmak yerine tam olarak hangi dosyaların eksik olduğunu listeleyen bir iletişim kutusu gösterir.

## Projeyi Çoğalt

Bu seçenek, proje açıldığında kullanılabilir. Mevcut projeyi yeni bir adla kopyalamak için &quot;Projeyi Çoğalt&quot;ı seçin — Chloros, kullanılabilir bir sonraki adı önerir (örn. &quot;MyProject (2)&quot;) — ve kopyalanan proje hemen açılır.

## Dosya Ekle

Bir proje açıldıktan sonra, ana menüden &quot;Dosya Ekle&quot; seçeneğini seçerek mevcut projeye tek tek görüntü dosyaları ekleyebilirsiniz. Bu işlev, dosya tarayıcısındaki ekleme işlevinin aynısıdır ancak kolaylık sağlamak amacıyla doğrudan ana menüden erişilebilir.

## Klasör Ekle

Proje açıldıktan sonra, mevcut projeye resim içeren klasörler eklemek için ana menüden &quot;Klasör Ekle&quot; seçeneğini seçin. Tek seferde birden fazla klasör seçebilirsiniz. Yinelenen dosyalar göz ardı edilir.

## İşlemeyi Başlat / Durdur

Dosyalar bir projeye eklendikten sonra, ana menüde &quot;İşlemeyi Başlat&quot; seçeneği kullanılabilir hale gelir. Bu, üst başlıktaki Oynat/Başlat düğmesine tıklamakla aynı işlemdir. İşleme sırasında, iş akışını durdurabilmeniz için menü öğesi &quot;İşlemeyi Durdur&quot; olarak değişir.

## Kameraya Bağlan / Işık Sensörüne Bağlan

Ana menünün alt kısmında, açık bir proje olsun ya da olmasın kullanılabilen iki donanım kısayolu bulunur:

* **Kameraya Bağlan** — bir LATTICE kamerası veya dizisini bağlamak için [Kameralar sekmesini](lattice/) açar.
* **Işık Sensörüne Bağlan** — bir DAQ ışık sensörüne bağlanmak için [Işık Sensörleri sekmesini](daq/) açar.

Bir proje açıkken donanım bağlandığında, bu donanım projeye kaydedilir (aşağıya bakın). Proje yoksa, bağlantılar yalnızca oturum süresince geçerlidir.

{% hint style="info" %}
Dosya Ekle, Klasör Ekle ve İşlemeyi Başlat/Durdur menü öğeleri, yalnızca bir proje açıkken ve dosyalar eklenmişse görünür veya etkin hale gelir. Bu öğeler, Dosya Tarayıcı kenar çubuğu ve üst başlık düğmeleri aracılığıyla da erişilebilen eylemlere hızlı erişim sağlar.
{% endhint %}

## Projeler donanımınızı hatırlar

1.2.0 sürümündeki yenilik: Bir proje açıkken bağladığınız donanım, proje kapandığında da korunur. Kameralar ve diziler (kamera başına ayarları, adları, renkleri ve ızgara düzeni ile birlikte) `cameras.json` dosyasına, ışık sensörleri (adları, renkleri ve kamera bağlamaları ile birlikte) ise `sensors.json` dosyasına dosyalarına otomatik olarak kaydedilir.

Bir projeyi **yeniden açtığınızda**, Chloros donanımlara hemen erişmez. Her bir bölüm, kendisine ait sekmeyi ilk kez ziyaret ettiğinizde yeniden bağlanır:

* **Kameralar** sekmesini açtığınızda, kaydedilmiş kameralar ve diziler yeniden bağlanır ve kaydedilmiş ayarları yeniden uygulanır.
* **Işık Sensörleri**sekmesini açtığınızda, kaydedilmiş DAQ sensörleri yeniden bağlanır ve kaydedilmiş ayarları (kapak dahil) yeniden uygulanır. Cap menüsünde**None (çıplak sensör)** olarak ayarlanmış bir sensör, çıplak kalır. Daha önceki bir Chloros sürümüyle kaydedilen bir proje, çıplak sensörü bir seçenek olarak kaydetmemiştir; bu nedenle, güneş ışığı kosinüs düzeltmesi ile geri gelir; bunu bir kez daha None olarak ayarlayın ve projeyi kaydedin.

Bu şekilde, yalnızca görüntüleri incelemek veya dışa aktarmak amacıyla bir proje açıldığında kameralar asla akış moduna geçmez. Bir sekme açıldığında kaydedilmiş bir cihaz bulunamazsa, hangi cihazların kullanılamadığını belirten bir iletişim kutusu görüntülenir; böylece bu cihazları yeniden bağlayabilir veya kaldırabilirsiniz.

## Bir projedeki DAQ kayıtları ve .daq dosyaları

* Proje açıkken (Işık Sensörleri sekmesinden veya yakalama işlemleri sırasında) yapılan `.daq` kayıtları **otomatik olarak projeye eklenir**.
* İçe aktarılan `.daq` dosyaları ve tüm proje kayıtları, [Proje Ayarları](project-settings/project-settings.md) içindeki **DAQ Işık Sensörü** bölümünde, her biri kendi kapak düzeltme profiliyle birlikte listelenir.
* İşleme sırasında, projenin `.daq` dosyaları, yansıma ürünleri için aşağı doğru ışıklandırma sağlar — bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md).

## Kaydedilmiş bir projeyi GUI olmadan çalıştırma

Kaydedilmiş bir proje, GUI olmadan çalıştırılabilir:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run`, bir proje klasör yolunda çalışır — bkz. [CLI Referansı](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` bir proje tanıtıcısı döndürür; `connect_all()`, kaydedilmiş ayarlarıyla birlikte kaydedilmiş tüm kameraları, dizileri ve ışık sensörlerini çevrimiçi hale getirir (ışık sensörleri, çalışan bir Chloros arka ucu aracılığıyla) — bkz. [SDK Referansı](reference/sdk-reference.md).
