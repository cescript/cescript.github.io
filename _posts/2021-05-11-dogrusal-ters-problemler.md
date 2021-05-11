---
layout: post
title: Doğrusal Ters Problemler
slug: linear-inverse-problems
author: Bahri ABACI
categories:
- Görüntü İşleme
- Görüntü İşleme Uygulamaları
thumbnail: /assets/post_resources/linear_inverse_problems/thumbnail.png 
---
Bilimsel ölçümler fiziksel bir gerçekliğin neden olduğu eylemlerin gözlenmesi ve kayıt altına alınması ile oluşturulur. Yapılan gözlem çalışması bir sistem olarak ele alınırsa; fiziksel gerçeklikler sistemin girdisi, yapılan gözlemler ise sistemin çıktısı olacaktır. Sistemin girdisi ile çıktısı arasında *doğrusal* bir ilişki olduğunu varsayarsak; elde edilen ölçümlerden/çıktılardan yola çıkarak fiziksel nedenlerin/girdilerin kestirilmesi **Doğrusal Ters Problem** olarak tanımlanır. Bu yazıda görüntü işleme alanında sıklıkla karşılaşılan doğrusal ters problemler hakkında bilgi verilecek, matematiksel ifadeler incelenecek ve bu problemler hakkında yapılan önemli çalışmalar özetlenecektir.

<!--more-->

Görüntü işleme alanında karşılaşılan problemlere geçmeden önce basit bir örnek ile **İleri Problem** ve **Ters Problem** kavramlarını anlamaya çalışalım.

Bir nesnenin ağırlığını, tabanının büyüklüğünü ve zeminin basınç dinamiklerini bilmemiz durumunda; nesnenin zeminde oluşturacağı izin derinliği, ilgili matematiksel modeller kullanılarak hesaplanabilir. 

Örnek olarak Afrika bölgesinde yaşayan bir fili ele alalım. Bu filin ağırlığı, ayak taban alanları ve bölgedeki toprağın basınç dinamiklerini bilmemiz durumunda bu filin yürürken toprakta oluşturacağı ayak izlerinin derinliği matematiksel modeller kullanılarak öngörebiliriz. 

Burada filin ağırlığı, ayak tabanının büyüklüğü ve toprağın basınç dinamikleri modelin girdisini, oluşan ayak izinin derinliği ise modelin çıktısını oluşturmaktadır. Böyle bir problemde, verilen girdiler ve model kullanılarak çıktı hesaplandığından, bu tip problemlere **İleri Problem** denir.

Aynı örnekte sahada gezen bir araştırmacının toprakta bir file ait ayak izleri gördüğünü düşünelim. Bu araştırmacının bu ayak izlerinden yola çıkarak, filin ağırlığını hesaplamaya çalışması bir **Ters Problem** örneğidir. Ancak farklı ağırlıklardaki iki fil, taban alanları ve toprak dinamiklerine bağlı olarak aynı derinlikte iz bırakabileceğinden, ters problemin çözümü her zaman tek bir sonuç olamayacaktır. 

Olası farklı çözümler içerisinde en iyi tahmini yapabilmek için yetişkin bir filin taban alanının olası değerleri, Afrika bölgesindeki toprağın basınç dinamikleri gibi öncül bilgilerin bilinmesi ters problemin çözümüne önemli katkılar sağlayacaktır.

Problemi matematiksel olarak ifade edecek olursak; ters problemin amacı $\mathbf{y}=F(\mathbf{x}) + \epsilon$ şeklinde modellenen bir problemde, $\mathbf{y}$ gözlemlerini kullanarak $\mathbf{x}$ girdilerini kestirmektedir.  Burada; $\mathbf{y} \in \mathcal{R}^m$ veya $Y \in \mathcal{R}^{m_y \times m_x}$ problemden elde edilen gözlemi, $\mathbf{x} \in \mathcal{R}^n$ veya $X \in \mathcal{R}^{n_y \times n_x}$ bilinmeyen girdiyi ve $\epsilon$ modelleme gürültüsünü göstermektedir.

Burada $\epsilon$ toplamsal Gaussian gürültüsü varsayılırsa; $\mathbf{y}, F$ bilindiğinde, $\mathbf{x}$'in olasılığı aşağıdaki şekilde gösterilir.

$$p(\mathbf{x} \lvert \mathbf{y}, F) \propto p(\mathbf{y} \lvert \mathbf{x}, F) p(\mathbf{x})$$

Bu olasılık fonksiyonunu en büyükleyen $\mathbf{\hat{x}}$ değeri, olası tüm $\mathbf{x}$ değerleri içerisindeki en iyi kestirim (MAP) olacaktır. Yazılan olasılık fonksiyonunun logaritması alınarak çarpım ifadesi toplam ifadesine dönüştürülürse problem;

$$ \mathbf{\hat{x}} = \arg\min_\mathbf{x} \lVert F(\mathbf{x}) - \mathbf{y} \lVert_2^2 + \lambda P(\mathbf{x})$$

maliyet fonksiyonunu en küçükleyen $\mathbf{\hat{x}}$ değerini bulma problemi olarak yeniden yazılabilecektir. 

Burada $P(\mathbf{x}) = -\log{p(\mathbf{x})}$, $\mathbf{x}$ girdisi hakkındaki bir öncül bilgiyi göstermektedir. Çözüm sırasında elde edilen işaret $\mathbf{x}$, istenilen öncül şartları sağlamazsa $p(\mathbf{x}) \approx 0$ olacağından $P(\mathbf{x}) \gg 1$ olacak ve bu çözüm maliyet fonksiyonunun değerini artırdığından olası bir çözüm olmayacaktır. 

[Makine Öğrenmesi]({% post_url 2019-07-14-makine-ogrenmesi-performans-olcutleri %}) konusunda çalışanlar bu formulün makine öğrenmesi problemine oldukça benzediğini fark edecektir. Burada iki problem arasındaki temel fark; makine öğrenmesinde en küçükleme verilen $(\mathbf{x},\mathbf{y})$ için $F$ fonksiyonu üzerinden yapılırken, doğrusal ters problemde $(F,\mathbf{y})$ bilindiği durumda $\mathbf{x}$ değeri bulunmaya çalışılmaktadır.

Yukarıda tanımlanan matematiksel ifadenin sadeleşmesi ve problemin daha kolay çözülebilmesi için modelin $F(\mathbf{x}) = A\mathbf{x}$ şeklinde doğrusal olduğu kabul edilirse;

$$
    \mathbf{\hat{x}} = \arg\min_\mathbf{x} \lVert A\mathbf{x} - \mathbf{y} \lVert_2^2 + \lambda P(\mathbf{x})
$$

problemi elde edilir. Bu problem **Doğrusal Ters Problem** olarak adlandırılmaktadır. $F(\mathbf{x}) = A\mathbf{x}$ ifadesi oldukça sade bir ifade olsa da $A$ matrisinin seçimine bağlı olarak aşağıda detayları verilen farklı görüntü işleme problemleri ifade edilebilmektedir.

### Gürültü Giderme
Gürültü gidermede amaç verilen gürültülü bir ölçümden gürültüsüz işareti bulmaktır. İmgeler kayıt sırasında kamera sensörlerinin elektriksel gürültülerine, sıkıştırma sırasında oluşan bozucu etkilere veya iletim sırasında sinyal üzerine etki eden girişimlerin oluşturduğu gürültülere maruz kalabilirler. 

Aşağıda kamera sensörlerinin elektriksel gürültülerinden kaynaklandığı ve toplamsal olduğu varsayılan gürültülü ölçüm verilmiştir. Gürültü gidermede amaç verilen ölçümden yola çıkarak gürültüsü azaltılmış işareti bulmaktır.

| $\mathbf{y}$: ölçüm | $\mathbf{\hat{x}}$: çıktı imge |
:-------:|:----:|
![Image Noise Remove #half][smurf_hsv_noise] | ![Image Noise Remove #half][smurf]|

Problemi bir doğrusal ters problem olarak göstermek istersek; sensör gürültüleri toplamsal varsayıldığından, tek bozucu $\epsilon$ gürültüsü olacaktır. Bu durumda girdi sinyali ile çarpım durumunda herhangi bir terim bulunmadığından, $\mathbf{A}=\mathbf{I}$ olacak ve $F(\mathbf{x}) = \mathbf{x}$ şeklinde bir model kullanılacaktır.

Öyleyse gürültü giderme için çözülmesi gereken problem:

$$
    \mathbf{\hat{x}} = \arg\min_\mathbf{x} \lVert \mathbf{x} - \mathbf{y} \lVert_2^2 + \lambda P(\mathbf{x})
$$

şeklinde olacaktır.

Elde edilen denklemden görüldüğü gibi öncül bir bilgi olmadığı durumda $(P(\mathbf{x})=0)$; elimizde bulunan ölçümü sistemin girdisi olarak kabul etmek $(\mathbf{x} = \mathbf{y})$ en küçük hatayı üretecektir. Ancak bu çözüm gereksiz bir çözümdür. Gerçek anlamda gürültüsüz bir imge elde edebilmek için gürültüsüz imgeler hakkında bir ön bilgiye ihtiyaç duyulmaktadır.

Rudin ve arkadaşları tarafından 1992 yılında yayınlanan makalede yazarlar imgenin toplam değişintisinin (Total Variation) bir öncül bilgi olarak kullanılabileceğini öne sürmüşlerdir. 

$$
P(\mathbf{x}) = \sum_{\Omega} \sqrt{\left(\nabla_x \mathbf{x}\right)^2 + \left(\nabla_y \mathbf{x}\right)^2}
$$

Toplam değişinti bir imgenin piksellerinin komşu piksellerden olan farkının toplamı alınarak bulunan bir ölçüttür. Gürültülü imgelerde komşu pikseller arasındaki renk farkı artacağından toplam değişintinin de artması beklenmektedir. Bu öncül bilgi kullanılarak problem çözüldüğünde komşu piksellerin renklerinin de benzer olması zorlandığından daha gürültüsüz bir sonuç elde edilmektedir.

### Ters Evrişim
Evrişim işlemi görüntü işlemede sıklıkla kullanılan doğrusal bir operasyondur. $\mathbf{k}$ evrişim çekirdeğini göstermek üzere, ileri problem $\mathbf{Y}=\mathbf{k} \ast \mathbf{X}$ ifadesi ile gösterilir.

Evrişim her ne kadar basit bir operasyon olsa da bu işlemin tersinin bulunması (ters problemin çözümü) her zaman mümkün değildir. Burada $\mathbf{k}$ bilinse dahi, $\mathbf{k}$ çekirdeğinin yüksek frekansları sönümleme karakteristiği, yaşanılan veri kaybından dolayı, geri alınabilir bir özellik değildir.

| $\mathbf{y}$: ölçüm | $\mathbf{k}$: çekirdek | $\mathbf{\hat{x}}$: çıktı imge |
|:-------:|:----:|:----:|
![Deconvolution #half][smurf_gblur] | ![Deconvolution #half][gaussian]| ![Deconvolution #half][smurf]|

$\mathbf{k}$ çekirdek matrisi vektör çarpımı ile ifade edilecek şekilde (Toeplitz biçiminde) $K$ olarak yeniden yazılırsa; ters evrişim problemi aşağıdaki şekilde bir ters problem olarak ifade edilebilir.

$$
\mathbf{\hat{x}} = \arg\min_{\mathbf{x}} \lVert K\mathbf{x} - \mathbf{y} \lVert_2^2 + \lambda P(\mathbf{x})
$$

Dilip ve arkadaşları tarafından 2009 yılında yayınlanan bir çalışmada yazarlar problemin çözümü için Hiper [Laplacian]({% post_url 2020-09-20-poisson-denklemi-yardimiyla-goruntu-duzenleme %}) imge öncüllerini önermişlerdir.

$$
P(\mathbf{x}) = \lvert \nabla_x \mathbf{x} + \nabla_y \mathbf{x}\lvert^\alpha
$$

Önerilen öncül fonksiyon dışbükey olmadığından optimizasyon probleminin çözümü problemi yarı dış bükey biçiminde iteratif bir yaklaşımla çözmeye çalışan Half Quadratic Splitting yöntemi kullanılarak hesaplanmıştır.

### Süper Çözünürlük

Süper çözünürlük düşük çözünürlükteki imgelerin yüksek çözünürlüğe çıkarılması işlemidir. İmgeler; dalga boyunun getirdiği kırılım limitleri, optik kaynaklı bulanıklıklar, sensör büyüklüğünün getirdiği mühendislik sorunları gibi çeşitli nedenlerle düşük çözünürlükte elde edilmiş olabilir.

| $\mathbf{y}$: ölçüm | $\mathbf{\hat{x}}$: çıktı imge |
:-------:|:----:|
![Image Super Resolution #half][smurf_lowres] | ![Image Super Resolution #half][smurf]|

Süper çözünürlük problemi; düşük çözünürlüklü imgenin, yüksek çözünürlüklü bir imgenin alçak geçiren süzgeç ile evrişiminden oluştuğu düşünülerek, ters evrişim problemi olarak ele alınabilir. Yada küçük imge büyük imgenin boyuna ölçeklendirildiğinde, iki imge arasında kalan hata gürültü olarak değerlendirilip, gürültü giderme problemi olarak çözülebilir.

Qi Shan ve arkadaşları tarafından 2008 yılında yapılan bir çalışmada problem gürültü giderme problemi olarak ele alınmış ve çözülmeye çalışılmıştır.

Önerilen yöntem tek bir düşük çözünürlüklü imgeden yüksek çözünürlüklü imge kestirimi yapmaktadır. Çalışmada; $S$ küçültme matrisi, $K$ bulanıklık çekirdeği olmak üzere, düşük çözünürlüklü imge ile yüksek çözünürlüklü imge arasındaki ilişki aşağıdaki şekilde tanımlanmıştır.

$$
    \mathbf{y} = SK\mathbf{x} + \epsilon
$$

İfadeden görüldüğü üzere, $\mathbf{y}$ ölçümünün, $\mathbf{x}$ işaretinin önce alçak geçiren bir süzgeç ile filtrelenip, ardından $S$ ölçekleme matrisi ile ölçeklenerek elde edildiği varsayılmaktadır.

Bu durumda çözülmesi gereken ters problem;

$$
    \mathbf{\hat{x}} = \arg\min_{\mathbf{x}} \lVert K\mathbf{x} - S^{-1}\mathbf{y} \lVert_2^2 + \lambda P(\mathbf{x})
$$

olarak tanımlanmıştır. 

Yazarlar çalışmada öncül bilgi fonksiyonu olarak doğal imgelerin gradyanlarının dağılım istatistiğini kullanmışlardır. Gürültü giderme problemine benzer olarak gürültüsüz imgelerde gradyan imgenin histogramı, renkler birbirine yakın olduğundan, sıfır noktası etrafında keskin bir tepe noktaya sahip olacaktır. Gürültülü imgelerin renkleri birbirlerinden daha farklı olduğundan gradyan imgenin histogramı sıfır etrafında keskin bir tepe yerine daha yayvan bir dağılıma sahip olacaktır. Yazarlar bu bilgiyi amaç fonksiyonunda kullanarak elde edilen imgenin doğallığını da sağlamışlardır.

### Seyrek Kodlama

Seyrek kodlama bir verinin en az sayıda vektör kullanılarak ifade edilmesi işlemidir. [K-means Kümeleme]({% post_url 2015-08-28-k-means-kumeleme-algoritmasi %}), [Temel Bileşen Analizi]({% post_url 2019-09-01-temel-bilesen-analizi %}) gibi önceki yazılarımızda değindiğimiz bazı yöntemler bu işlem için kullanılabilmektedir.

Seyrek kodlama problemi iki alt problemden oluşmaktır.

- İlk alt problem **sözlük öğrenme** problemidir. Sözlük; seyrek kodlama probleminde veriyi ifade etmekte kullanılan vektörlerin oluşturduğu kümedir. Sözlük öğrenmede amaç veriyi en az sayıda sözlük elemanı (vektör) ile ifade edebilecek bir sözlük de bulmaktır.
- İkinci alt problem **eşleştirme** problemidir. Eşleştirme probleminde en az sayıda sözlük elemanı kullanılarak girdi işaretine yakın bir işaret elde edilmeye çalılşılmaktadır.

$D$ bilinmeyen vektörlerden oluşan bir sözlük olmak üzere seyrek kodlama problemi aşağıdaki şekilde tanımlanır.

$$
    \mathbf{\hat{x}},\hat{D} = \arg\min_{\mathbf{x},D} \lVert D\mathbf{x} - \mathbf{y} \lVert_2^2 + \lambda \lVert \mathbf{x} \lVert_0
$$

Denklemden görüldüğü üzere verilen optimizasyon problemi, diğer denklemlerden farklı olarak iki değişken üzerinden en küçüklenmeye çalışılmaktadır. Burada $D$ üzerinden yapılan en küçükleme **sözlük öğrenme** problemini çözerken, $\mathbf{x}$ üzerinden yapılan en küçükleme **eşleştirme** problemini çözmektedir.

| $\mathbf{y}$: ölçüm | $\hat{D}$: sözlük | $\hat{D}\mathbf{\hat{x}}$: seyrek kodlanmış imge |
|:-------:|:----:|:----:|
![Image Spare Coding #half][smurf] | ![Image Spare Coding #half][dictionary]| ![Image Spare Coding #half][smurf_9color]|

Seyrek kodlama konusunda literatürde yer alan önemli bir çalışma Michal ve arkadaşları tarafından 2006 yılında yayınlanmıştır. Çalışmada seyrek kodlama problemi için K-SVD adı verilen bir yöntem önerilmiştir. Önerilen çalışma yukarıda formüle edilen problemi iteratif olarak çözmeye çalışmaktadır. 

Elimizde $\mathbf{y}_i$ lerden oluşan bir veri seti olduğunu varsayalım. Bu durumda, her bir iterasyon adımında, önce bilinen bir $D$ sözlüğü için $\mathbf{x}_i$ gösterimleri bulunmaya çalışılır. 

Ardından bulunan $\mathbf{x}_i$ gösterimleri [k-ortalama]({% post_url 2015-08-28-k-means-kumeleme-algoritmasi %}) benzeri bir yaklaşımla kümelenerek $D$ sözlüğü hesaplanır. Bu işlemler belirlenen bir iterasyon sayısınca tekrarlanarak sözlük öğrenme ve eşleştirme problemleri birlikte çözülür.

-----
Görüntü işleme problemlerinin büyük bir çoğunluğu doğrusal ters problem olarak ifade edilmektedir. Burada listelenen problemlere ek olarak, Kör Bulanıklık Giderme, İmgeden Sis Kaldırma, İmge Yeniden Oluşturma gibi farklı çalışma alanlarında da benzer amaç fonksiyonları en küçüklenerek problemler çözülmeye çalışılmaktadır.

Bu yazıda, her biri ayrı çalışma alanı olan farklı görüntü işleme problemlerinin, doğrusal ters problem çatısı altında, tek bir maliyet fonksiyonu ile ifade edilebileceği gösterilmiştir. Buna ek olarak ilgili konularda yapılan ve önemli sayıda referans gösterilen çalışmalarda ne gibi bir yaklaşım yapıldığı anlatılıp, problemin matematiksel gösterimleri verilmiştir.

**Referanslar**
* Rudin, Leonid I., Stanley Osher, and Emad Fatemi. "Nonlinear total variation based noise removal algorithms." Physica D: nonlinear phenomena 60.1-4 (1992): 259-268.
* Krishnan, Dilip, and Rob Fergus. "Fast image deconvolution using hyper-Laplacian priors." Advances in neural information processing systems 22 (2009): 1033-1041.
* Shan, Qi, et al. "Fast image/video upsampling." ACM Transactions on Graphics (TOG) 27.5 (2008): 1-7.
* Aharon, Michal, Michael Elad, and Alfred Bruckstein. "K-SVD: An algorithm for designing overcomplete dictionaries for sparse representation." IEEE Transactions on signal processing 54.11 (2006): 4311-4322.

[RESOURCES]: # (List of the resources used by the blog post)
[smurf]: /assets/post_resources/linear_inverse_problems/smurf.png
[smurf_9color]: /assets/post_resources/linear_inverse_problems/smurf_9color.png
[smurf_blur]: /assets/post_resources/linear_inverse_problems/smurf_blur.png
[smurf_grad]: /assets/post_resources/linear_inverse_problems/smurf_grad.png
[smurf_hsv_noise]: /assets/post_resources/linear_inverse_problems/smurf_hsv_noise.png
[smurf_lowres]: /assets/post_resources/linear_inverse_problems/smurf_lowres.png
[smurf_gblur]: /assets/post_resources/linear_inverse_problems/smurf_gblur.png
[gaussian]: /assets/post_resources/linear_inverse_problems/gaussian.png
[dictionary]: /assets/post_resources/linear_inverse_problems/dictionary.png


