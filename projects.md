# GUI: Projeler

Chloros, ileride yeniden açılabilecek projeler oluşturmanıza olanak tanır. Bir proje, (Proje Klasörünüzün içindeki) aşağıdakileri içeren basit bir klasördür:

* `project.json` — proje ayarları, dosya listesi ve görüntüleme tercihleri
* `cameras.json` — proje açıkken bağlanmış kameralar ve diziler ile bunların ayarları
* `sensors.json` — proje açıkken bağlanmış DAQ ışık sensörleri ve kamera↔sensör eşlemeleri
* yakalamalarınız, `.daq` kayıtlarınız ve işlenmiş çıktı klasörleri

Özel bir proje dosyası biçimi yoktur — klasör ve içindeki JSON dosyaları projeyi oluşturur; bu da projelerin kopyalanmasını, arşivlenmesini ve [CLI](CLI.md) veya [Python SDK](api-python-sdk.md) konumlarına taşınmasını kolaylaştırır.

## Yeni Proje

<figure><img src=".gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>Ana menüden &quot;Yeni Proje&quot; seçeneğini seçin ve projeniz için benzersiz bir ad girin.

Herhangi bir proje şablonu kaydettiyseniz, ad alanının altında **Şablon Seç** açılır menüsü görünür — bunlardan birini seçtiğinizde yeni proje o şablonun ayarlarıyla başlatılır. Şablonlar [Proje Ayarları](project-settings/project-settings.md) bölümünden kaydedilir: &quot;Proje Şablonu Adını Kaydet&quot; alanına bir ad girin ve kaydet simgesine tıklayın.

## Proje Aç

<figure><img src=".gitbook/assets/v120-open-project.jpg" alt=""><figcaption><p>Proje Aç, proje klasörünüzdeki tüm projeleri listeler; alt kısımda ise <strong>Proje Klasörünü Aç</strong> seçeneği bulunur</p></figcaption></figure>&quot;Projeyi Aç&quot; seçeneğini seçerek Proje Klasöründeki mevcut projelerin listesini görüntüleyebilirsiniz. Herhangi bir proje yoksa ikincil yan menü açılmaz. Yukarıdaki fotoğrafta, GUI ile oluşturulmuş bazı projeler (t1, t2, t3) listelenmiştir. DATE\_TIME projeleri, CLI tarafından varsayılan proje adlandırma şeması kullanılarak oluşturulmuştur. Herhangi bir proje adına tıkladığınızda proje açılır.

&quot;Proje Klasörünü Aç&quot; düğmesine tıkladığınızda, bilgisayarınızın dosya gezgini proje yolunda açılır. Proje yolunu [Proje Ayarları](project-settings/project-settings.md) bölümünden ayarlayabilirsiniz.

Proje en son açıldığından bu yana kaynak görüntü dosyalarından herhangi biri taşınmış veya silinmişse, Chloros boş bir ızgara açmak yerine, tam olarak hangi dosyaların eksik olduğunu listeleyen bir iletişim kutusu gösterir.

## Projeyi Çoğalt

Bu seçenek, proje açıldığında kullanılabilir. Mevcut projeyi yeni bir adla kopyalamak için &quot;Projeyi Çoğalt&quot; seçeneğini seçin — Chloros, kullanılabilir bir sonraki adı önerir (örn. &quot;MyProject (2)&quot;) — ve kopyalanmış proje hemen açılır.

## Dosya Ekle

Bir proje açıldıktan sonra, ana menüden &quot;Dosya Ekle&quot; seçeneğini seçerek mevcut projeye tek tek görüntü dosyaları ekleyebilirsiniz. Bu, dosya tarayıcısının ekleme işlevini yansıtır, ancak kolaylık olması açısından doğrudan ana menüden erişilebilir.

## Klasör Ekle

Bir proje açıldıktan sonra, ana menüden &quot;Klasör Ekle&quot; seçeneğini seçerek mevcut projeye görüntü klasörleri ekleyebilirsiniz. Tek seferde birden fazla klasör seçebilirsiniz. Çift dosyalar göz ardı edilir.

## İşlemeyi Başlat / Durdur

Dosyalar bir projeye eklendikten sonra, ana menüde &quot;İşlemeyi Başlat&quot; seçeneği kullanılabilir hale gelir. Bu, üst başlıktaki Oynat/Başlat düğmesine tıklamakla aynı işlemdir. İşleme sırasında, iş akışını durdurabilmeniz için menü öğesi &quot;İşlemeyi Durdur&quot; olarak değişir.

## Kameraya Bağlan / Işık Sensörüne Bağlan

Ana menünün alt kısmında, açık bir proje olsun ya da olmasın kullanılabilen iki donanım kısayolu bulunur:

* **Kameraya Bağlan** — bir LATTICE kamerası veya dizisini bağlamak için [Kameralar sekmesini](lattice/) açar.
* **Işık Sensörüne Bağlan** — bir DAQ ışık sensörünü bağlamak için [Işık Sensörleri sekmesini](daq/) açar.

Bir proje açıkken donanım bağlandığında, bu donanım projeye kaydedilir (aşağıya bakın). Proje yoksa, bağlantılar yalnızca oturum süresince geçerlidir.

{% hint style="info" %}
Dosya Ekle, Klasör Ekle ve İşlemeyi Başlat/Durdur menü öğeleri, yalnızca bir proje açıkken ve dosyalar eklenmişse görünür veya etkin hale gelir. Bu öğeler, Dosya Tarayıcı kenar çubuğu ve üst başlık düğmeleri aracılığıyla da erişilebilen eylemlere hızlı erişim sağlar.
{% endhint %}

## Projeler donanımınızı hatırlar

1.2.0 sürümündeki yenilik: Bir proje açıkken bağladığınız donanım, proje kapandığında da korunur. Kameralar ve diziler (kamera başına ayarları, adları, renkleri ve ızgara düzeni ile birlikte) `cameras.json` dosyasına, ışık sensörleri (adları, renkleri ve kamera bağlamaları ile birlikte) ise `sensors.json` dosyasına dosyasına otomatik olarak kaydedilir.

Bir projeyi **yeniden açtığınızda**, Chloros dosyası donanımla hemen etkileşime girmez. Her bir yarısı, kendisine ait sekmeyi ilk kez ziyaret ettiğinizde yeniden bağlanır:

* **Kameralar** sekmesini açtığınızda, kaydedilmiş kameralar ve diziler yeniden bağlanır ve kaydedilmiş ayarları yeniden uygulanır.
* **Işık Sensörleri** sekmesini açmak, kaydedilmiş DAQ sensörlerini yeniden bağlar.

Bu sayede, yalnızca görüntüleri incelemek veya dışa aktarmak amacıyla bir projeyi açmak, kameraları asla akış moduna geçirmez. Bir sekme açıldığında kaydedilmiş bir cihaz bulunamazsa, hangi cihazların kullanılamadığını belirten bir iletişim kutusu görüntülenir; böylece bu cihazları yeniden bağlayabilir veya kaldırabilirsiniz.

## Projedeki DAQ kayıtları ve .daq dosyaları

* Proje açıkken (Işık Sensörleri sekmesinden veya yakalama işlemleri sırasında) yapılan `.daq` kayıtları **otomatik olarak projeye eklenir**.
* İçe aktarılan `.daq` dosyaları ve tüm proje kayıtları, [Proje Ayarları](project-settings/project-settings.md) bölümündeki **DAQ Işık Sensörü** kısmında, her biri kendi parlaklık düzeltme profiliyle birlikte listelenir.
* İşleme sırasında, projenin `.daq` dosyaları, yansıma ürünleri için aşağı doğru ışıklandırma sağlar — bkz. [Çıktı Görüntü Biçimleri](output-image-formats.md).

## Kaydedilmiş bir projeyi GUI olmadan çalıştırma

Kaydedilmiş bir proje, GUI olmadan çalıştırılabilir:

* **CLI**: `chloros-cli project open / connect / capture / sensor / align / run`, bir proje klasörü yolu üzerinde çalışır — bkz. [CLI Referansı](reference/cli-reference.md).
* **SDK**: `chloros_sdk.open_project(path)` bir proje tanıtıcısı döndürür; `connect_all()`, kaydedilmiş tüm kameraları ve sensörleri kaydedilmiş ayarlarıyla birlikte çevrimiçi hale getirir — bkz. [SDK Referansı](reference/sdk-reference.md).
