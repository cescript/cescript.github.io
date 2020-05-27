---
layout: post
title: Otsu Metodu ile Adaptif Eşikleme
slug: otsu-thresholding
author: Bahri ABACI
categories:
- Görüntü İşleme
- Hızlı Algoritmalar
thumbnail: /assets/post_resources/otsu_thresholding/thumbnail.png
---

Eşikleme yada ikili tonlama gri tonlanmış bir resmin siyah-beyaz (ikili) uzaya dönüştürülmesi işlemidir. Siyah-beyaz görüntüler, görüntü üzerindeki renklerin pek önemli olmadığı, görüntü üzerinde belirli şekillerin veya dizilerin arandığı uygulamalarda işlem yükünü hafifletmek ve görüntü üzerinde mantıksal (0-1) işlemleri hızlı bir şekilde yapabilmek için sıklıkla kullanılan görüntülerdir. Basitçe gri seviye bir görüntü üzerinde 0-255 arasında seçilen bir $T$ eşik değerine göre, siyah-beyaz resim aşağıdaki şekilde oluşturulur. 

<!--more-->

$$
I(x,y) = 
\begin{cases}
0 &&, I(x,y) < T\\
1 &&, I(x,y) \geq T
\end{cases}
$$

Burada $T$ değerinin doğru seçilmesi kritik önem taşımaktadır. Eğer $T$ değeri çok büyük seçilirse oluşturulacak yeni görüntüde pek çok piksel beyaz(1), küçük seçilirse de siyah(0) olacağından görüntünün içerdiği bilgi ciddi miktarda azalacaktır. Birkaç resim için ideal eşik değeri deneme yoluyla bulunabilse de farklı ışık ortamlarında çekilmiş çok sayıda görüntü için bu söz konusu olamaz. Bu nedenle girdi resmine karşılık eşik değerini otomatik olarak hesaplayan bir algoritma gerekmektedir. 

Nobuyuki Otsu (1979) tarafından geliştirilen [Otsu metodu](http://en.wikipedia.org/wiki/Otsu%27s_method) ile eşik değeri görüntü üzerinden hesaplanmaktadır. Metod görüntü üzerinde iki ayrı sınıf(plan) olduğunu kabul ederek, bu iki sınıf arasındaki varyansı maksimum yapacak değeri bulmaya çalışır. Varyans bir dizinin elemanlarının dizinin ortalamasına olan uzaklıklarının karelerinin ortalamasıdır. Bu değere bakarak dizi içerisindeki değerlerin ortalamaya ne derece yakın olduğu görülebilir. 

Aşağıda $N$ uzunluklu bir dizi için varyans hesaplama formülü verilmiştir.  

$$\sigma^2=\sum_{i=0}^{N-1} (x_i-\mu_x)Pr\{x_i\}$$  

Yukarıdaki ifadede $Pr\{x_i\}$ ifadesi $x_i$' nin gelme olasılığıdır. Düzgün dağılımlı $N$ uzunluklu bir dizi için bu değer $1/N$ dir. $M\times N$ bir görüntü için konuşacak olursak histogram dizisi hesaplandıktan sonra $i$ tonunun gelme olasılığı `histogram[i]/(MxN)` dir.  
  
Sınıflar arası varyans ( **s**iyah-**b**eyaz sınıfları ) aşağıdaki formül ile bulunur. 

$$\sigma_s=\sigma-\sigma_b=\omega_1(t)\omega_2(t)(\mu_1(t)-\mu_2(t))^2$$  

Burada $\omega$ değişkenleri sınıf yoğunluklarını göstermektedir ve aşağıdaki formül ile hesaplanır.

$$
\begin{aligned}
\omega_1(t) =  \sum_{i=0}^tPr\{i\}\\
\omega_2(t) =  \sum_{i=t+1}^{255}Pr\{i\}\\
\end{aligned}
$$

$\mu$ değişkeleri ise ağırlıklı sınıf ortalamarını göstermektedir.$H(i)$ $i.$ renk seviyesine ait histogram değerini ($i$ninci pikselden resimde kaç tane olduğunu) göstermek üzere, sınıf ağırlıklı ortalamaları aşağıdaki formüller ile hesaplanır. 

Bu değerler aşağıdaki formüller yardımıyla hesaplanır. 

$$
\begin{aligned}
\mu_1(t)    =  \sum_{i=0}^tPr\{i\}H(i) \\
\mu_1(t)    =  \sum_{i=t+1}^{255}Pr\{i\}H(i)
\end{aligned}
$$

Yazılan program, `t=0` ilk değer ile başlayarak 255 e kadar her değer için sınıflar arası varyansı hesaplamakta ve en son maksimum varyans değerini veren `t` değerini $T$ (eşik değeri) olarak döndürmektedir. Algoritmanın çalışması için $H$ histogram dizisine ihtiyaç duyulduğundan verilen bir görüntü için önce histogram dizisi oluşturulmuş ardından bu dizi aşağıda verilen `otsu` fonksiyonu kullanılarak Otsu eşik değeri hesaplanmıştır.

```c
// otsu algorithm for threshold value detection
uint32_t otsu(uint32_t *histogram, uint32_t hist_length)
{
    uint32_t t = 0, thr = 0;

    float sum = 0, sum1 = 0, sum2 = 0;
    float wsum = 0, wsum1 = 0, wsum2 = 0;
    float sigma = 0;

    for (t = 0; t < hist_length; t++)
    {
        sum += histogram[t];
        wsum += t * histogram[t];
    }

    for (t = 0; t < 256; t++)
    {
        sum1 += histogram[t];
        sum2 = (sum - sum1);

        // if the weighted sum under threshold t is 0, continue to the next threshold
        if (sum1 == 0)
        {
            continue;
        }

        // if the weighted sum over threshold t is 0, break the threshold search
        if (sum2 == 0)
        {
            break;
        }

        wsum1 += t * histogram[t];
        wsum2 = (wsum - wsum1);

        // find the average
        float avg1 = wsum1 / sum1;
        float avg2 = wsum2 / wsum2;

        // compute the standart deviation
        float std = sum1 * sum2 * (avg1 - avg2) * (avg1 - avg2);

        // if the current value of the std is higher, choose it as the best threshold
        if (std > sigma)
        {
            sigma = std;
            thr = t;
        }
    }

    return thr;
}
```

IMLAB görüntü işleme kütüphanesinde verilen bir gri seviye görüntü üzerinden Otsu eşik değerini hesaplamak için `imotsu` isimli bir fonksiyon bulunmaktadır. Bu fonksiyon kullanılarak bir girdi resminin nasıl eşiklenebileceği aşağıdaki örnekte gösterilmiştir.

```c
matrix_t *input = imread("..//data//lena.bmp");

// convert input to grayscale
matrix_t *grayImg = matrix_create(uint8_t, rows(input), cols(input), 1);
rgb2gray(input, grayImg);

// create output images
matrix_t *bw_0075 = matrix_create(uint8_t, rows(input), cols(input), 1);
matrix_t *bw_0180 = matrix_create(uint8_t, rows(input), cols(input), 1);
matrix_t *bw_otsu = matrix_create(uint8_t, rows(input), cols(input), 1);

// binarize the grayscale image with different thresholds
imthreshold(grayImg, 75, bw_0075);
imthreshold(grayImg, 180, bw_0180);

uint32_t otsuthr = imotsu(grayImg);
printf("Otsu threshold is %d\n", otsuthr);
imthreshold(grayImg, otsuthr, bw_otsu);

imwrite(grayImg, "..//data//lena_gray.bmp");
imwrite(bw_0075, "..//data//lena_0075.bmp");
imwrite(bw_0180, "..//data//lena_0180.bmp");
imwrite(bw_otsu, "..//data//lena_otsu.bmp");
```

Yukarıda bahsettiğim üzere, aşağıda seçilen eşik değerinin işlemin sonucuna olan etkisi görünmektedir. İkinci resim $T=75$ eşik değeri ile eşiklendiğinden yüz kısmında bulunan pek çok veri kaybolmuş, üçüncü resim yüksek bir eşik değeri $T=180$ ile eşiklendiğinden neredeyse tüm bilgiler kaybolmuştur. Son resim ise adaptif eşik değeri bulunarak $T=115$ eşiklenmiştir ve bu sayede resim üzerindeki pek çok bilgi korunmuştur.  
  
| Gri İmge   |  $T=75$ ile Eşikleme Sonucu | $T=180$ ile Eşikleme Sonucu | $T=147$(Otsu) ile Eşikleme Sonucu  |
|:----------:|:-----------------:||:----------------------:|:-----------:|
![Gri İmge][lena_otsu_threshold] | ![T=75 ile Eşikleme Sonucu][lena_otsu_threshold_75] | ![T=180 ile Eşikleme Sonucu][lena_otsu_threshold_180] | ![T=147(Otsu) ile Eşikleme Sonucu][lena_otsu_threshold_otsu]

  
**Referanslar**

* Di Stefano, Luigi, and Andrea Bulgarelli. "A simple and efficient connected components labeling algorithm." Proceedings 10th International Conference on Image Analysis and Processing. IEEE, 1999.

* He, Lifeng, et al. "Fast connected-component labeling." Pattern recognition 42.9 (2009): 1977-1987.

[RESOURCES]: # (List of the resources used by the blog post)
[lena_otsu_threshold]: /assets/post_resources/otsu_thresholding/lena_gray.png
[lena_otsu_threshold_75]: /assets/post_resources/otsu_thresholding/lena_0075.png
[lena_otsu_threshold_180]: /assets/post_resources/otsu_thresholding/lena_0180.png
[lena_otsu_threshold_otsu]: /assets/post_resources/otsu_thresholding/lena_otsu.png