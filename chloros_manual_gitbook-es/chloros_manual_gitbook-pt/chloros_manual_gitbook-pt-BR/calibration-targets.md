---
description: Yakalanan verileri sonradan işlemede kalibre etmek için kullanılan laboratuvarda ölçülen paneller
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# Kalibrasyon Hedefleri

MAPIR bir dizi uygulamayı kapsayacak çeşitli kalibrasyon hedefleri sunar. Aşağıda görülen kompakt T4-R50, 250 - 2.500 nm arasındaki ışık yansıması açısından ölçülen 4 panel içerir.

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>

T4 dağınık referans hedefleri aşağıdaki yansıma eğrilerine sahiptir: [verileri buradan indirin](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Yansıma :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Yansıma :: 400-1000nm</p></figcaption></figure>

Yansıma grafiğine baktığınızda değerlerin dalga boyu (x ekseni) ile yansıma yüzdesi (y ekseni) olduğunu görebilirsiniz. Kalibrasyon hedefinin bir görüntüsünü yakaladığımızda, kameranın sensör bantlarının her birinin duyarlı olduğu spektrum dahilinde piksel değeri ile yansıma yüzdesi arasında bir ilişki yaratırız.

Bu, kameralarımızla yakaladığınız her görüntüde, görüntüleri yansıma açısından kalibre etmek için [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) veya [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) gibi yansıma hedeflerimizin bir fotoğrafını kullanabileceğiniz anlamına gelir. Kalibre edildikten sonra görüntüdeki her piksel yansıma yüzdesine eşittir.

Kalibre edilmiş görüntüleri Chloros'ta tipik JPG veya TIFF olarak çıkarırsanız yansıma yüzdesi, piksel değerinin görüntü formatının bit derinliğine bölünmesiyle hesaplanır. Yani JPG için 255'e, TIFF için ise 65.535'e bölün. Ayrıca Chloros'ta YÜZDE formatı çıktısını da seçebilirsiniz; ardından her piksel 0,0 ila 1,0 (%0 ila %100 yansıma) arasında bir yüzde değeri aralığında olacaktır. Bazı görüntü uygulamalarının yüzde (kayan nokta) görüntüleri kabul edemediğini ve depolama açısından büyük boyutlara sahip olduklarını unutmayın.

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
