---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrasyon Hedefleri

MAPIR, çeşitli uygulamaları kapsayan farklı kalibrasyon hedefleri sunmaktadır. Aşağıda görülen kompakt T4-R50 modeli, 250 - 2.500 nm aralığında ışık yansıtma değeri ölçülmüş 4 panel içermektedir.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 dağınık referans hedefleri aşağıdaki yansıma eğrilerine sahiptir, [verileri buradan indirebilirsiniz](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Yansıma :: 250-2.500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Yansıma :: 400-1000 nm</p></figcaption></figure>T4P dağınık referans hedefleri aşağıdaki yansıma eğrilerine sahiptir, [verileri buradan indirebilirsiniz](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P Yansıma :: 250-2500 nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P Yansıtma Oranı :: 400-1000 nm</p></figcaption></figure>Yansıma grafiğine baktığınızda, değerlerin dalga boyu (x ekseni) ile yansıma yüzdesi (y ekseni) arasında bir ilişki olduğunu görebilirsiniz. Kalibrasyon hedefinin bir görüntüsünü yakaladığımızda, kameranın her bir sensör bandının duyarlı olduğu spektrum içinde piksel değeri ile yansıma yüzdesi arasında bir ilişki kurarız.

Bu, kameralarımızla çektiğiniz her görüntüde, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) veya [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) gibi yansıtma hedeflerimizin fotoğraflarını kullanarak görüntüleri yansıtma açısından kalibre edebilirsiniz. Kalibrasyon tamamlandıktan sonra, görüntüdeki her piksel yüzde yansıtma değerine eşittir.

**Survey3** çıktıları için, Chloros

&#x27;ta kalibre edilmiş görüntüleri standart JPG veya TIFF

formatında dışa aktarırsanız, yansıma yüzdesi piksel değerinin görüntü formatının bit derinliğine bölünmesiyle hesaplanır. Yani JPG için 255&#x27;e, TIFF

için ise 65.535&#x27;e bölünür. Chloros

&#x27;ta PERCENT formatında çıktı seçeneğini de tercih edebilirsiniz; bu durumda her piksel, 0,0 ile 1,0 arasındaki bir yüzde değerinde (yüzde 0 ile yüzde 100 yansıtma) olacaktır. Bazı görüntü uygulamalarının yüzde (kayan nokta) formatındaki görüntüleri kabul edemediğini ve bu tür dosyaların depolama açısından büyük boyutlu olduğunu unutmayın.

{% hint style="info" %}
**LATTICE yansıtma değeri farklı bir piksel ölçeği kullanır.** LATTICE yansıtma değeri, DN 32768 = %100 yansıtma (65535 değil) olarak kaydedilir ve her dosya, ölçeğini belirten bir XMP `Chloros:PixelScale` etiketi taşır. Sabit bir değer varsaymak yerine etiketi okuyun ve bu değere bölün — bkz. [Çıktı Görüntü Formatları](output-image-formats.md).
{% endhint %}

## LATTICE kameralarıyla kalibrasyon hedefleri

LATTICE kameralarında yansıma oranı için bir kalibrasyon hedefi **isteğe bağlıdır**: Chloros

, bunun yerine yansıma oranını bir DAQ ışık sensörü tarafından ölçülen aşağı doğru ışınım yoğunluğuna göre hesaplayabilir (ρ = π·L/E). Referans, `chloros-cli process --reflectance-source` ile ayarlanan projenin yansıma kaynağı ayarıyla seçilir (GUI&#x27;de bununla ilgili bir kontrol yoktur ve projenin kaydedilmiş değeri kullanılır; varsayılan olarak `auto` — bkz. [Proje Ayarları](project-settings/project-settings.md)):

| Değer | Davranış |
| --- | --- |
| `auto` *(varsayılan)* | QA&#x27;yı geçen çerçeve içi hedef, **mutlak referans** olarak kabul edilir; hedef bulunmadığında veya QA başarısız olduğunda, Chloros

, DAQ aşağı yönlü bölünmesine geri döner. Bir bayrakla (bir panelin düşmesi veya bir bandın arızalı olması) QA&#x27;yı geçen bir hedef otomatik olarak tercih edilmez: hedef, hedef+DAQ ve sadece DAQ zincirleri, ayrılmış panellerde ölçülen puanlarına göre sırayla denenir. |
| `target` | Yalnızca hedef odaklı — DAQ ikamesi yok. |
| `daq` | DAQ öncelikli — aşağı doğru ölçüm her zaman referans olarak alınır. |

LATTICE için ek hedef davranışları:

* **Hedef geometrileri** — ArUco ile işaretlenmiş paneller, sabit ROI panelleri ve şerit hedeflerin tümü desteklenir; geometri, projenin hedef yapılandırmasından alınır.
* **Birim başına ölçülen hedef verileri** — `--target-reflectance-dir DIR`, birim başına ölçülen hedef yansıma taramalarının bulunduğu bir dizine işaret eder (`<serial>.csv`, hedef birimin seri numarası/QR kodu ile aranır). Hedef ıskalandığında, Chloros

nominal T3/T4P spektrumlarına geri döner.
* **Zamansal sabitleme** — algılanan bir hedef, çevresindeki kareleri kalibre eder ve hedef gözlemleri arasında sabit tutulur.

Bayrakların tam anlamları ve örnekleri [CLI

Referansı](reference/cli-reference.md) içinde bulunur (bkz. &quot;Ürün Bazında Dışa Aktarma Anahtarları&quot;).

### F988

&quot;F988 yansıma değeri, sahne içi bir yansıma paneli kullanılarak kalibre edilir: bant, DAQ ışık sensörünün kalibre edilmiş aralığının ötesinde yer aldığından, Chloros

en son panel yakalamanızı uygular ve panel gözlemleri arasında bunu korur.&quot;

F988, yalnızca DAQ kalibrasyonu ile çalıştırılırsa, Chloros

o bant için DAQ tabanlı yansıma değerini reddeder ve nedenini belirtir (atlama nedeni `dls-uncalibrated-band-988`); panel iş akışı desteklenen yoldur.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
