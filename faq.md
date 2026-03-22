---
description: Frequently Asked Questions
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/faq
---

# SSS

<details>

<summary>MAPIR markası olmayan kameralardan gelen görüntüleri Chloros ile işleyebilir miyim?</summary>

Hayır, Chloros yalnızca MAPIR kamera görüntülerini işlemeyi destekler. Daha fazla bilgi için lütfen [desteklenen kamera modelleri](supported-cameras.md) listesine bakın. MAPIR Cloud&#x27;da diğer kameraların görüntülerini de işliyoruz, tam listeye [buradan](https://mapir.gitbook.io/mapir-cloud/supported-cameras) ulaşabilirsiniz.

</details>

<details>

<summary>Kalibrasyon hedefi olmadan görüntülerimi yansıtma açısından kalibre edebilir miyim?</summary>

Hayır. Hedef olmayan görüntüler çekilirken kalibrasyon hedefinin görüntüsü de çekilmezse, görüntünün piksel değerlerini bilinen bir yansıma yüzdesiyle ilişkilendiremezsiniz. Ayrıca, bir MAPIR ışık sensöründen alınan günlüğü de eklemezseniz, ortam ışığı spektrumu ölçülmez ve yansıma sonuçları doğru olmaz.

</details>

<details>

<summary>Chloros&#x27;te işleme tabi tutmadan önce görüntülerimi düzenleyebilir miyim?</summary>

Hayır. Chloros, giriş verilerinin değiştirilmediğini varsayar. Dosya adlarını değiştirmeyin.

</details>

<details>

<summary>MAPIR ve Survey3 kameralarımı otomatik pozlamaya ayarlayıp görüntüleri Chloros&#x27;te işleyebilir miyim?</summary>

Hayır. Survey3 görüntü veri kümelerinin pozlaması sabit/kilitli olmalıdır, bu nedenle otomatik enstantane veya otomatik ISO kullanılamaz. Aynı kamera modeline ait tüm görüntülerde enstantane ve ISO (pozlama) değerleri aynı olmalıdır.

</details>

<details>

<summary>Chloros, ortomozaik görüntüleri işleyebilir veya analiz edebilir mi?</summary>

Hayır. Yalnızca tek tek MAPIR kamera görüntüleri desteklenir, ortomozaik harita gibi birleştirilmiş görüntüler desteklenmez.

</details>

<details>

<summary>Chloros&#x27;in hedef algılama adımını nasıl hızlandırabilirim?</summary>

Dosya tarayıcı tablosunda sağ sütunda hedef görüntüleri önceden seçmek, Chloros&#x27;e kalibrasyon hedeflerini yalnızca bu görüntülerde aramasını söyler ve işlemeyi büyük ölçüde hızlandırır.

</details>

<details>

<summary>Görüntülerimi <a href="https://www.mapir.camera/collections/software/products/mapir-cloud-subscription">MAPIR Cloud</a>&#x27;a yükleyeceksem, yüklemeden önce Chloros&#x27;te işlem yapmalı mıyım?</summary>

Çevrimiçi işleme platformumuz [MAPIR Cloud](https://www.mapir.camera/collections/software/products/mapir-cloud-subscription) &#x27;a yüklemeyi planlıyorsanız, yüklemeden önce görüntüleri düzenlemeyin. Cloud, aynı işlemleri ve daha fazlasını gerçekleştirecektir.

</details>

<details>

<summary>MAPIR, X özelliğini destekleyecek mi? MAPIR&#x27;in X özelliğini sunmasını gerçekten çok isterim.</summary>

Ürünlerimizle ilgili geri bildirimleri her zaman memnuniyetle karşılıyoruz. Ürünlerimizle ilgili bir sorunla karşılaşırsanız veya ürünlerimizi nasıl iyileştirebileceğimiz konusunda bir öneriniz varsa, lütfen [BİZE ULAŞIN](https://www.mapir.camera/community/contact) ve düşüncelerinizi paylaşın. Ar-Ge çalışmalarımızın çoğu, müşterilerimizin en büyük ihtiyaçlarını dinleyerek yönlendirilmektedir.

</details>

<details>

<summary>Chloros, Linux için kullanılabilir mi?</summary>

Evet! Chloros 1.1.0, `.deb` paketleri aracılığıyla Linux amd64 (x86\_64) ve arm64 (NVIDIA Jetson JetPack 6) sürümlerini desteklemektedir. CLI ve Python SDK, Linux üzerinde tam olarak desteklenmektedir. Linux için bir GUI yoktur — tüm etkileşim [CLI](CLI.md) veya [Python SDK](api-python-sdk.md) aracılığıyla gerçekleştirilir. Ayrıntılar için [Linux Genel Bakış](linux/linux-overview.md) bölümüne bakın.

</details>

<details>

<summary>Chloros&#x27;i NVIDIA Jetson&#x27;da çalıştırabilir miyim?</summary>

Evet! Chloros 1.1.0, JetPack 6 çalıştıran Jetson Nano, Orin Nano, Orin NX ve AGX Orin dahil olmak üzere NVIDIA Jetson platformlarını destekler. Chloros, Jetson modelinizi otomatik olarak algılar ve işleme stratejisini optimize eder. Kurulum ve dağıtım talimatları için [NVIDIA Jetson Kılavuzu](linux/nvidia-jetson-guide.md) bölümüne bakın.

</details>

<details>

<summary>Chloros donanımım için otomatik olarak optimizasyon yapar mı?</summary>

Evet! Chloros 1.1.0, CPU, GPU, RAM ve (Jetson&#x27;da) termal sensörlerinizi otomatik olarak algılayan [Dinamik Hesaplama Uyumlaştırma](processing-architecture/dynamic-compute-adaptation.md) özelliğini içerir. Ardından, yüksek bellekli sistemlerde `GPU_PARALLEL`&#x27;ten, kısıtlı cihazlarda `GPU_SINGLE`&#x27;e ve NVIDIA GPU&#x27;su olmayan sistemlerde `CPU_PARALLEL`&#x27;e kadar en uygun işleme stratejisini seçer. Manuel yapılandırma gerekmez.

</details>

<details>

<summary>4 iş parçacıklı işleme boru hattı nedir?</summary>

Chloros 1.1.0, Chloros+ kullanıcıları için 4 iş parçacıklı bir ardışık mimari kullanır: İş parçacığı 1 (Algılama) görüntüleri yükler ve kalibrasyon hedeflerini algılar, İş parçacığı 2 (Kalibrasyon) yansıma kalibrasyonunu hesaplar, İş parçacığı 3 (İşleme) GPU hızlandırmalı debayering ve indeks hesaplamasını gerçekleştirir ve İş parçacığı 4 (Dışa Aktarma) çıktı dosyalarını yazar. Maksimum verim için birden fazla görüntü aynı anda farklı iş parçacıklarında bulunabilir. Ayrıntılar için [İşleme Boru Hattı](processing-architecture/processing-pipeline.md) bölümüne bakın.

</details>

<details>

<summary>Chloros kurulumumda tanılama işlemini nasıl çalıştırabilirim?</summary>

`selftest` komutunu kullanarak sürüm kontrolü, bağlantı noktası kullanılabilirliği, arka uç başlatma, API bağlantısı, sistem bilgileri, gürültü giderici modelleri ve CUDA kullanılabilirliği dahil olmak üzere 7 sistem tanılama işlemini çalıştırın:

```bash
chloros-cli selftest
```

Bu, özellikle Linux/Jetson sistemlerinde GPU ve CUDA kurulumunu doğrulamak için kullanışlıdır.

</details>
