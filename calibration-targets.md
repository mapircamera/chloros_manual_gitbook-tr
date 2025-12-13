---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrasyon Hedefleri

MAPIR, çeşitli uygulamaları kapsayan çeşitli kalibrasyon hedefleri sunar. Aşağıda görülen kompakt T4-R50, 250 - 2.500 nm arasında ışık yansıtma oranı ölçülmüş 4 panel içerir.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 dağınık referans hedefleri aşağıdaki yansıma eğrilerine sahiptir, [verileri buradan indirin](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Yansıtma :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Yansıma :: 400-1000 nm</p></figcaption></figure>Yansıma grafiğine baktığınızda, değerlerin dalga boyu (x ekseni) ve yansıma yüzdesi (y ekseni) olduğunu görebilirsiniz. Kalibrasyon hedefinin görüntüsünü yakaladığımızda, kameranın her bir sensör bandının duyarlı olduğu spektrum içinde piksel değeri ve yansıma yüzdesi arasında bir ilişki oluştururuz.

Bu, kameralarımızla çektiğiniz her görüntüde, [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) veya [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) gibi yansıma hedeflerimizin fotoğrafını kullanarak görüntüleri yansıma açısından kalibre edebileceğiniz anlamına gelir. Kalibre edildikten sonra, görüntüdeki her piksel yansıma yüzdesine eşittir.

Kalibre edilmiş görüntüleri Chloros&#x27;te tipik JPG veya TIFF olarak çıktı alırsanız, yansıma yüzdesi piksel değerinin görüntü formatının bit derinliğine bölünmesiyle hesaplanır. Yani JPG için 255&#x27;e, TIFF için 65.535&#x27;e bölünür. Chloros&#x27;te PERCENT formatında çıktı seçeneğini de tercih edebilirsiniz; bu durumda her piksel 0,0 ile 1,0 arasında bir yüzde değerine sahip olacaktır (yüzde 0 ile 100 yansıma). Bazı görüntü uygulamalarının yüzde (kayan nokta) görüntüleri kabul etmediğini ve bu görüntülerin depolama açısından büyük boyutlu olduğunu unutmayın.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
