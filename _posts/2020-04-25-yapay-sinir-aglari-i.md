---
layout: post
title: Yapay Sinir Ağları I
slug: artificial-neural-networks-I
author: Bahri ABACI
categories:
- Makine Öğrenmesi
- Nümerik Yöntemler
- Veri Analizi
references: ""
published: false
thumbnail: /assets/post_resources/artificial_neural_networks/thumbnail_1.png
---
Yapay Sinir Ağları, sinir hücrelerinin çalışma ilkelerinden ilham alınarak geliştirilmiş bir makine öğrenmesi ve veri analizi yöntemidir. Günümüzde elde ettikleri büyük başarılara bağlı olarak, oldukça büyük bir ivme kazanan Evrişimsel Sinir Ağları (Convolutional Neural Network), Özyinelemeli Sinir Ağları (Recurrent Neural Network) gibi çok katmanlı sinir ağları yöntemlerinin ilk örneği olan yapay sinir ağları günümüzde derin öğrenme yöntemlerinin son katmanında karar verici olarak veya derin öğrenmeye ihtiyaç duyulmayan, görece basit ve doğrusal olmayan problemlerin çözümünde sıklıkla kullanılmaktadır. Bu yazımızda sinir hücresi ve algılayıcı kavramlarından başlayarak tek katmanlı yapay sinir ağlarının matematiksel ifadelerine dair gerekli çıkarımlar yapılacaktır.

<!--more-->

Yapay Sinir Ağları (YSA)' ların girdi ve çıktıya sahip, en küçük işlem birimi algılayıcı (perceptron) veya biyolojideki adı ile nöronlardır. Nöronların sinir sistemindeki görevi dentritler aracılığı ile aldığı elektriksel uyarıyı, akson olarak isimlendirilen bir iletim hattından geçirerek çıktı birimine iletmektir. Vücutlarımızda milyonlarca nöronun birbirine bağlanması sonucunda oluşan karmaşık ağ yapısı ile, karar verme mekanizması, hafıza ve kas kontolü gibi önemli görevleri yerine getiren beyin ve sinir sistemimiz oluşmaktadır.

YSA da bu karmaşık ağ yapısına benzer olarak nöron benzeri algılayıcılar kullanarak makine öğrenmesi yapmaya yönelik geliştirilen bir yöntemdir. Bu yöntemin günümüzde oldukça yaygın bir şekilde kullanılmasını sağlayan önemli farklılıkları aşağıda listelenmiştir.

 - Doğrusal Olmama (Non-linearity) : Biyolojik nöronların tepkisine benzer şekilde, YSA öğrenmesinde kullanılan algılayıcılarında girdi ve çıktıları arasında doğrusal olmayan bir ilişki bulunmaktadır. Bu özellik sayesinde; girdi ve çıktıları arasında doğrusal bir ilişki bulunmayan eğitim kümeleri, doğrusal sınıflandırıcılar ([Lojistik Regresyon Analizi]({% post_url 2015-07-23-lojistik-regresyon-analizi %}), Destek Vektör Makineleri, Doğrusal Regresyon, vd.) ile öğrenilemezken, YSA aracılığıyla öğrenilebilir.

  - İçerik Bilgisi (Contextual Information) : YSA öğrnemsinde bir algılayıcı tüm girdilere bağlı olduğundan, eğitim sonrasında girdi içerisindeki örüntüleri tanımak/çıkartmak/vurgulamak gibi görevleri olan algılayıcılar oluşabilmektedir. YSA öğrenmesinde kullanılan çok katmanlı algılayıcı yapısı ile bir katmandan elde edilen içerik bilgisi sonraki katmanlar tarafından doğrudan kullanıldığından, katmanlar belirli örüntülerin bir arada olup olmadığı bilgisi de öğrenilebilir.

 - Hata Dayanıklılığı (Fault Tolerance) : YSA yapısı gereği dağıtık yapıda bir öğrenme yöntemi olduğundan, her bir algılayıcı bağının üzerinde saklanan bilginin toplam çıktıya etkisi oldukça küçüktür. Bu sayede özellikle fiziksel gerçeklemede karşılaşılacak gürültüler nedeniyle bazı bağların yanlış değerlendirilmesinde dahi ağın çıktısı bozulmadan kalabilecektir.

### Algılayıcılar

Algılayıcılar; yapay sinir ağlarının girdi ve çıktıya sahip en küçük işlem birimleridir. Literatürde bilinen ilk algılayıcı modeli, basitleştirilmiş nöron davranışını matematiksel olarak ifade etmeye çalışan McCulloch-Pitts tarafından 1943 yılında önerilmiştir. Önerilen bu model ile basit mantık devrelerini nöron modelleri ile gerçeklemek ve bu devreleri katmanlar şeklinde bağlayarak çoğu doğruluk tablosunu gerçekleyebilen bir ağ kurmak mümkündü. Ancak McCulloch-Pitts tarafından yazılan makalede oluşturulan yapının öğrenme yöntemine dair herhangi bir algoritma bulunmamakta ve önerilen model sadece mantıksal veri tiplerini (0/1) desteklemekteydi. Bu iki temel problem, Rosenblatt tarafından 1962 yılında önerilen algılayıcı modeli çözüldü. Aşağıda gerçek bir sinir hücresinin ve Rosenblatt tarafından önerilen yapay sinir hücresinin / algılayıcının görünümü verilmiştir.

|Sinir Hücresi Gösterimi | Yapay Sinir Hücresi Modeli |
|:-------:|:----:|
![sinir hücresi gösterimi][neuron] | ![yapay sinir hücresi][perceptron]

Örnek modeli verilen algılayıcı, girişine uygulanan $\mathbf{x_n}$ girdi vektörünü, çeşitli matematiksel operasyonlardan geçirerek $\hat{y}_n$ çıktısını üretmektedir. Algılayıcının matematiksel ifadesi Denklem \ref{perceptron} ile verilmiştir.

$$ \hat{y}_n = \mathcal{f}\left( \sum_{d=0}^{D-1} {w_{d} x_d} + b \right) \label{perceptron} \tag{1}$$

Verilen ifadedenin $\mathbf{x_n}$ girdi vektörünü; ilki doğrusal $a_n=\sum_{d} {w_d x_d} + b$, ikincisi doğrusal olmayan $\hat{y}_n=\mathcal{f}(a_n)$ iki dönüşümden geçirerek çıktı değerini ürettiği görülmektedir. YSA öğrenmesinde amaç, verilen girdi vektörlerine ve hedef çıktılara en küçük hata ile yakınsamamızı sağlayacak $[\mathbf{w}, b]$ ağırlık vektörünün öğrenilmesidir.

Denklem \ref{perceptron} incelendiğinde parantez içerisinde kalan kısmın [Doğrusal Regresyon]({% post_url 2020-01-13-lagrange-carpanlari-yontemi %})' a benzer bir şekilde girdi vektörünü doğrusal bir dönüşüme tabi tuttuğu görülmektedir. YSA öğrenmesini farklı kılan önemli nokta denklemde kullanılan $\mathcal{f}(a)$ aktivasyon fonksiyonu dönüşümüdür. Bu dönüşümde doğrusal olmayan bir $\mathcal{f}$ fonksiyonunun seçilmesi durumunda algılayıcı çıktılarının doğrusal olmayan (non-linear) yapıda olması sağlanmaktadır. Bu sayede birden fazla sayıda nöronun birbirlerine bağlanarak oluşturduğu yapay sinir ağı ile doğrusal olmayan problemlerinde öğrenebilmesi mümkün olmaktadır.

### Tek Katmanlı Algılayıcı Eğitimi {#perceptron_learning}

YSA öğrenmesinde başlangıç noktası algılayıcı eğitimi aşamasıdır. Bu aşamada verilen $\mathbf{X}=[\mathbf{x_1}, \mathbf{x_2}, \dots, \mathbf{x_N}]$ girdi vektörlerine ve $\mathbf{y}=[y_1, y_2, \dots, y_N]$ hedef çıktılara en küçük hata (burada hata etiketlere olan ortalama karesel hata olabileceği gibi farklı bir ölçüt de olabilir) ile yakınsamamızı sağlayacak $[\mathbf{w}, b]$ ağırlık vektörünün öğrenilmesi amaçlanmaktadır. Tek bir $\mathbf{x_n}, y_n$ çifti için  _karesel hata_ aşağıdaki şekilde yazılabilir.

$$ E_n(\mathbf{w},b) = \frac{1}{2} (y_n - \hat{y}_n)^2 \label{perceptron_learning} \tag{2}$$

Yazılan hata fonksiyonunda yer alan $\hat{y}_n$, Denklem \ref{perceptron} eşitliği göz önüne alındığında, $\mathbf{w}$ ve $b$ değişkenlerine bağlıdır. Bu hata fonksiyonun en küçükleyen $\mathbf{w}$ ve $b$ değerleri sırasıyla $\frac{\partial E_n(\mathbf{w},b)}{\partial \mathbf{w}} = 0$ ve $\frac{\partial E_n(\mathbf{w},b)}{\partial b} = 0$ eşitlikleri çözülerek bulunabilir.

Denklem \ref{perceptron_learning} ile verilen eşitliğin $\mathbf{w}$ değerine göre kısmi türevi zincir kuralı kullanılarak aşağıdaki şekilde yazılabilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial\mathbf{w}} = \frac{1}{2} \left( \frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} \right) \left( \frac{\partial \hat{y_n}}{\partial a_n} \right) \left( \frac{\partial a_n}{\partial\mathbf{w}} \right) \label{partials} \tag{3}$$

<span style="color: yellow;">NOT: </span> Burada ileride kolaylık sağlaması açısından önemli bir tanımlama yapmamız gerekir. Denklem \ref{partials} ile verilen eşitlikte $\frac{\partial E_n(\mathbf{w},b)}{\partial a_n} = \left( \frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} \right) \left( \frac{\partial \hat{y_n}}{\partial a_n} \right)$ çarpımına kısaca $\delta_n$ adı verilir.

Denklem \ref{partials} ile verilen üç kısmi türev ifadesinde ilk ifade, hata fonksiyonun $\hat{y}$ kestirilen çıktıya bağlı değişimini ölçmektedir. Bu değer _karesel hata_ ölçütü kullanıldığı için;

$$\frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} = \frac{\partial}{\partial \hat{y_n}} \frac{1}{2} \left( y_n - \hat{y}_n \right )^2 = \hat{y}_n-y_n$$

bulunur. İkinci kısmi türev ise $\hat{y}_n$ çıktısının $a_n$ girişine bağlı değişimi ölçmektedir. Tanım gereği $\hat{y}=\mathcal{f}(a)$ olduğundan bu ifade;

$$\frac{\partial \hat{y}_n}{\partial a_n} =  \frac{\partial \mathcal{f}(a_n)}{\partial a_n} = \mathcal{f}^\prime (a_n)$$

şeklinde yazılır. Üçüncü kısmi türev ise $a_n$ ara çıktısının $\mathbf{x}\_{n}$ girişine bağlı değişimini ölçmektedir. Bu ifadede $a_n=\mathbf{w}^\intercal \mathbf{x}_{n} + b$ tanımı gereği aşağıdaki şekilde hesaplanabilir.

$$\frac{\partial a_n}{\partial\mathbf{w}} =  \frac{\partial \left( \mathbf{w}^\intercal\mathbf{x}_{n} + b \right) }{\mathbf{w}} = \mathbf{x}_{n}$$

Hesaplanan ilk iki türev ifadesi kullanılarak $\delta_n$ aşağıdaki şekilde tanımlanır.

$$\delta_n = \left(y_n-\hat{y}_n \right) \mathcal{f}^\prime (a_n) \label{delta} \tag{4}$$

Hesaplanan $\delta_n$ ve $\frac{\partial a_n}{\partial\mathbf{w}}$ değerleri Denklem \ref{partials} de yerine yazılırsa Denklem \ref{partials_solution_w} ile verilen eşitlik elde edilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial\mathbf{w}} = {\delta_n \mathbf{x}_{n}} \label{partials_solution_w} \tag{5}$$

Benzer şekilde Denklem \ref{perceptron_learning} ile verilen eşitliğin $b$ değişkenine göre kısmi türevi zincir kuralına göre hesaplanırsa Denklem \ref{partials_solution_b} ile verilen eşitlik elde edilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial b} = {\delta_n} \label{partials_solution_b} \tag{6}$$

Elde edilen bu iki eşitlik [Gradyan İniş Yönteminde]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) kullanılarak denklemleri sağlayan $\mathbf{w},b$ çifti bulunabilir. Bu yöntem herhangi bir fonksiyonun yerel en küçük noktasının o fonksiyonun o noktadaki gradyanı tersi yönüne hareket edilerek bulunacağı fikrine dayanmaktadır. Matematiksel olarak gradyan iniş yöntemi;

$$\mathbf{w}_{k+1}=\mathbf{w}_k-\eta \nabla E(\mathbf{w},b)$$

$\mathbf{w}_k=0$ (veya rasgele) ilk değerinden başlayarak iteratif bir şekilde fonksiyonu en küçükleyecek ağırlıkları bulmaya çalışan bir yöntemdir. Burada $\eta$ gradyan inişinin hızının ayarlanması için kullanılan sabit bir terimdir. Özellikle iterasyonun başlarında en küçük noktaya hızlı varmak için büyük bir $\eta$ değeri seçilirken, iterasyonun ilerleyen adımlarında kritik noktayı geçmemek için kısılır. $\nabla = \left[  \frac{\partial E_n(\mathbf{w},b)}{\partial\mathbf{w}}, \frac{\partial E_n(\mathbf{w},b)}{\partial b} \right]$ ise gradyan operatörüdür.

### Aktivasyon Fonksiyonları

Denklem \ref{perceptron} ile verilen ifadede yer alan $\mathcal{f}$ fonksiyonu literatürde __aktivasyon fonksiyonu__ olarak bilinmektedir. Yazımızın ilk başlarında değindiğimiz gibi doğrusal olmayan problemlerin öğrenilebilmesi için aktivasyon fonksiyonunun doğrusal olmaması gerekmektedir. [Tek Katmanlı Algılayıcı Eğitimi](#perceptron_learning) başlığında da hesapladığımız üzere seçilen aktivasyon fonksiyonunun türevlenebilir de olması gerekmektedir. Bu iki temel özellik göz önünde bulundurularak literatürde farklı aktivasyon fonksiyonları önerilmiştir.

* __Adım Aktivasyon Fonksiyonu:__ 

    Adım Aktivasyon Fonksiyonu'nun en önemli özeliği tüm girdi değerlerine karşılık sadece iki farklı çıktı üretmesidir. McCulloch-Pitts nöronu olarak da bilinen bu aktivasyon fonksiyonu, Warren MuCulloch ve Walter Pitts tarafından 1943 yılında önerilen ilk yapay sinir modelidir. Önerilen modelin matematiksel ifadesi aşağıda verilmiştir.

    $$\mathcal{f}(a) = 
    \begin{cases}
    0 & a < 0 \\
    1 & a \geq 0
    \end{cases}
    $$

    Bu modelde algılayıcı çıktaları sıfır yada bir şeklinde sadece iki çıkış verebildiğinden genellikle sınıflandırma problemlerinde tercih edilmektedir.

* __Lojistik Aktivasyon Fonksiyonu__

    [Lojistik Regresyon Analizi]({% post_url 2015-07-23-lojistik-regresyon-analizi %}) yazımızda incelenen lojistik (sigmoid) aktivasyon fonksiyonu sürekli ve türevi alınabilir bir fonksiyondur. Doğrusal olmayan ve kolay türevlenebilen bir fonskiyon olması nedeniyle yapay sinir ağı uygulamalarında en sık kullanılan aktivasyon fonksiyondur. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.
    
    $$\mathcal{f}(a) = \frac{1}{1+e^{-a}}$$

    Bu fonksiyon girdi değerlerinin her biri için sıfır ile bir aralığında bir değer üretmektedir.

* __Tanjant Hiperbolik Aktivasyon Fonksiyonu__

    Tanjant hiperbolik fonksiyonu, sigmoid fonksiyonuna benzer bir fonksiyondur. Sigmoid fonksiyonunda çıkış değerleri $[0,1]$ aralığında değişirken hiperbolik tanjant fonksiyonunun çıkış değerleri$[-1,1]$ aralığında değişmektedir. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.

    $$\mathcal{f}(a) = \frac{e^{a}-e^{-a}}{e^{a}+e^{-a}}$$

* __Rampa (ReLu) Aktivasyon Fonksiyonu__

    Literatürde Rectified Linear Unit (ReLu) olarak da bilinen rampa aktivasyon fonksiyonu parçalı bir aktivasyon fonksiyonudur. Yukarıda verilen aktivasyon fonksiyonlarından farklı olarak rampa aktivasyon fonksiyonunun çıkışında bir sınır bulunmamaktadır. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.
    
    $$\mathcal{f}(a) = 
    \begin{cases}
    0 & a < 0 \\
    a & a \geq 0
    \end{cases}
    $$

### Yapay Sinir Ağlarının Eğitilmesi
 
Her makine öğrenmesi yönteminde olduğu gibi YSA' nın da bir öğrenme sürecinden geçmesi gerekmektedir. [Tek Katmanlı Algılayıcı Eğitimi](#perceptron_learning) başlığında tek bir algılayıcının hatasının nasıl en küçükleneceğine dair matematiksel çıkarımları yapmıştık. Burada bahsedilen [Gradyan İniş Yöntemi]({post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon}) iteratif bir algoritma olduğundan YSA eğitiminin _epoch_ adı verilen belirli bir iterasyon süresince devam etmesi gerekmektedir.

Her _epoch_ kendi içerisinde iki ana adımdan oluşmaktadır. Bunlardan ilki İleri Besleme (Feed Forward), ikincisi Geri Yayılım (Back Propagation) adımlarıdır. İleri Besleme adımında bir önceki iterasyondan bulunan $\mathcal{w},b$ ağrılıkları ve Denklem \ref{perceptron} kullanılarak ağın çıktısı hesaplanır. Geri Yayılım adımında Denklem \ref{partials_solution_w} ve Denklem \ref{partials_solution_b} denklemleri kullanılarak bulunan ağırlık değişimi güncel ağırlıklara eklenerek ağırlıkların güncellenir. Veri setindeki tüm örnekler için İleri Besleme ve Geri Yayılım adımları tamamlandığında bir _epoch_ tamamlanmış olur. Öğrenmenin iyileştirilmesi için belirli bir başarı kriteri sağlanana veya sabit bir _epoch_ sayısına ulaşana kadar eğitim adımları devam ettirilir.

Çok Katlı Yapay Sinir Ağlarının matematiksel ifadeleri ve Geri Yayılım Algoritması'nın anlatıldığı serinin ikinci yazısına [Yapay bağlantıdan]({%post_url 2020-04-25-yapay-sinir-aglari-ii %}) ulaşabilirsiniz.

Yazıda yer alan analizlerin yapıldığı kod parçaları, görseller ve kullanılan veri setlerine [artificial_neural_networks](https://github.com/cescript/artificial_neural_networks) GitHub sayfası üzerinden erişilebilirsiniz.

**Referanslar**
* Alpaydin, Ethem. [Introduction to machine learning](https://www.cmpe.boun.edu.tr/~ethem/i2ml3e/). MIT press, 2014.

* Bishop, Christopher M. Neural networks for pattern recognition. Oxford university press, 1995.

* Haykin, Simon. Neural networks: a comprehensive foundation. Prentice Hall PTR, 1994.

* Jain, Anil K., Jianchang Mao, and K. Moidin Mohiuddin. "Artificial neural networks: A tutorial." Computer 29.3 (1996): 31-44.

[RESOURCES]: # (List of the resources used by the blog post)
[neuron]: /assets/post_resources/artificial_neural_networks/neuron.svg
[perceptron]: /assets/post_resources/artificial_neural_networks/perceptron.svg
