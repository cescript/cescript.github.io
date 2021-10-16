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

Örnek modeli verilen algılayıcı, girişine uygulanan $\mathbf{x_n}$ girdi vektörünü, çeşitli matematiksel operasyonlardan geçirerek $\hat{y}_n$ çıktısını üretmektedir. Algılayıcının matematiksel ifadesi Denklem $\eqref{1}$ ile verilmiştir.

$$ \hat{y}_n = f\left( \sum_{d=0}^{D-1} {w_{d} x_d} + b \right) = f \left( \mathbf{w}^\intercal \mathbf{x}_n + b \right) \tag{1}$$

Verilen ifadedenin $\mathbf{x_n}$ girdi vektörünü; ilki doğrusal $a_n=\sum_{d} {w_d x_d} + b = \mathbf{w}^\intercal \mathbf{x}_n + b$, ikincisi doğrusal olmayan $\hat{y}_n=f(a_n)$ iki dönüşümden geçirerek çıktı değerini ürettiği görülmektedir. YSA öğrenmesinde amaç, verilen girdi vektörlerine ve hedef çıktılara en küçük hata ile yakınsamamızı sağlayacak $[\mathbf{w}, b]$ ağırlık vektörünün öğrenilmesidir.

Denklem $\eqref{1}$ incelendiğinde parantez içerisinde kalan kısmın [Doğrusal Regresyon]({% post_url 2020-01-13-lagrange-carpanlari-yontemi %})' a benzer bir şekilde girdi vektörünü doğrusal bir dönüşüme tabi tuttuğu görülmektedir. YSA öğrenmesini farklı kılan önemli nokta denklemde kullanılan $f(a)$ aktivasyon fonksiyonu dönüşümüdür. Bu dönüşümde doğrusal olmayan bir $f$ fonksiyonunun seçilmesi durumunda algılayıcı çıktılarının doğrusal olmayan (non-linear) yapıda olması sağlanmaktadır. Bu sayede birden fazla sayıda nöronun birbirlerine bağlanarak oluşturduğu yapay sinir ağı ile doğrusal olmayan problemlerin de öğrenebilmesi mümkün olmaktadır.

### Tek Katmanlı Algılayıcı Eğitimi {#perceptron_learning}

YSA öğrenmesinde başlangıç noktası algılayıcı eğitimi aşamasıdır. Bu aşamada verilen $\mathbf{X}=[\mathbf{x_1}, \mathbf{x_2}, \dots, \mathbf{x_N}]$ girdi vektörlerine ve $\mathbf{y}=[y_1, y_2, \dots, y_N]$ hedef çıktılara en küçük hata (burada hata etiketlere olan ortalama karesel hata olabileceği gibi farklı bir ölçüt de olabilir) ile yakınsamamızı sağlayacak $[\mathbf{w}, b]$ ağırlık vektörünün öğrenilmesi amaçlanmaktadır. Burada $N$ eğitim kümesindeki eleman sayısıdır.

Seçilen tek bir örnek için $\mathbf{x_n}, y_n$ çifti için  _karesel hata_ aşağıdaki şekilde yazılabilir.

$$ E_n(\mathbf{w},b) = \frac{1}{2} (y_n - \hat{y}_n)^2$$

Yazılan hata fonksiyonunda yer alan $\hat{y}_n$, Denklem $\eqref{1}$ eşitliği göz önüne alındığında, $\mathbf{w}$ ve $b$ değişkenlerine bağlıdır. 

$$ E_n(\mathbf{w},b) = \frac{1}{2} (y_n - f\left( \mathbf{w}^\intercal \mathbf{x}_n + b \right))^2 \tag{2}$$


Bu hata fonksiyonun en küçükleyen $\mathbf{w}$ ve $b$ değerleri sırasıyla $\frac{\partial E_n(\mathbf{w},b)}{\partial \mathbf{w}} = 0$ ve $\frac{\partial E_n(\mathbf{w},b)}{\partial b} = 0$ eşitlikleri çözülerek bulunabilir.

Denklem $\eqref{2}$ ile verilen eşitliğin $\mathbf{w}$ değerine göre kısmi türevi zincir kuralı kullanılarak aşağıdaki şekilde yazılabilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial\mathbf{w}} =  \left( \frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} \right) \left( \frac{\partial \hat{y_n}}{\partial a_n} \right) \left( \frac{\partial a_n}{\partial\mathbf{w}} \right) \tag{3}$$

Denklem $\eqref{3}$ ile verilen üç kısmi türev ifadesinde ilk ifade, hata fonksiyonun $\hat{y}$ kestirilen çıktıya bağlı değişimini ölçmektedir. Bu değer _karesel hata_ ölçütü kullanıldığı için;

$$\frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} = \frac{\partial}{\partial \hat{y_n}} \frac{1}{2} \left( y_n - \hat{y}_n \right )^2 = \hat{y}_n-y_n \tag{3.1}$$

bulunur. İkinci kısmi türev ise $\hat{y}_n$ çıktısının $a_n$ girişine bağlı değişimi ölçmektedir. Tanım gereği $\hat{y}=f(a)$ olduğundan bu ifade;

$$\frac{\partial \hat{y}_n}{\partial a_n} =  \frac{\partial f(a_n)}{\partial a_n} = f^\prime (a_n) \tag{3.2}$$

şeklinde yazılır. Üçüncü kısmi türev ise $a_n$ ara çıktısının $\mathbf{x}_{n}$ girişine bağlı değişimini ölçmektedir. Bu ifadede $a_n=\mathbf{w}^\intercal \mathbf{x}_{n} + b$ tanımı gereği aşağıdaki şekilde hesaplanabilir.

$$\frac{\partial a_n}{\partial\mathbf{w}} =  \frac{\partial \left( \mathbf{w}^\intercal\mathbf{x}_{n} + b \right) }{\mathbf{w}} = \mathbf{x}_{n} \tag{3.3}$$

Burada ileride kolaylık sağlaması açısından önemli bir tanımlama yapmamız gerekir. Denklem $\eqref{3}$ ile verilen eşitlikte $\frac{\partial E_n(\mathbf{w},b)}{\partial a_n} = \left( \frac{\partial E_n(\mathbf{w},b)}{\partial \hat{y_n}} \right) \left( \frac{\partial \hat{y_n}}{\partial a_n} \right)$ çarpımını kısaca $\delta_n$ olarak isimlendirelim. Bu durumda, Denklem $\eqref{3.1}$ ve $\eqref{3.2}$ ifadeleri kullanılarak $\delta_n$ aşağıdaki şekilde tanımlanır.

$$\delta_n = \left(y_n-\hat{y}_n \right) f^\prime (a_n)\tag{4}$$

Hesaplanan $\delta_n$ ve $\frac{\partial a_n}{\partial\mathbf{w}}$ değerleri Denklem $\eqref{3}$ de yerine yazılırsa hesaplanmaya çalışılan kısmi türev ifadesi aşağıdaki şekilde ifade edilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial\mathbf{w}} = {\delta_n \mathbf{x}_{n}} \tag{5}$$

Benzer şekilde Denklem $\eqref{2}$ ile verilen eşitliğin $b$ değişkenine göre kısmi türevi zincir kuralına göre hesaplanırsa Denklem $\eqref{6}$ ile verilen eşitlik elde edilir.

$$\frac{\partial E_n(\mathbf{w},b)}{\partial b} = {\delta_n} \tag{6}$$

Elde edilen bu iki eşitlik [Gradyan İniş Yönteminde]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) kullanılarak hatayı en küçükleyecek olan $\mathbf{w},b$ çifti bulunabilir. 

Hatırlayacak olursak, *Gradyan İniş Yöntemi* yöntemi herhangi bir fonksiyonun yerel en küçük/büyük noktasının; fonksiyonun herhangi bir noktasındaki gradyanının tersi yönüne hareket edilerek bulunacağı fikrine dayanmaktadır. Yukarıda tanımlanan problem için gradyan iniş yöntemi;

$$
\begin{aligned}
\mathbf{w}_{k+1} &=\mathbf{w}_k-\eta \nabla_w E(\mathbf{w},b) &&= \mathbf{w}_k - \eta {\delta_n \mathbf{x}_{n}}\\
b_{k+1} & =b_k-\eta \nabla_b E(\mathbf{w},b) & &= b_k - \eta \delta_n\\
\end{aligned}
$$

şeklinde yazılır. Verilen ifadede $\mathbf{w}_0, b_0$ rastgele seçilen ilk değerlerden başlayarak iteratif bir şekilde fonksiyonu en küçükleyecek ağırlıklar hesaplanır. Burada $\eta$ gradyan inişinin hızının ayarlanması için kullanılan sabit bir terimdir. Özellikle iterasyonun başlarında en küçük noktaya hızlı varmak için büyük bir $\eta$ değeri seçilirken, iterasyonun ilerleyen adımlarında kritik noktayı geçmemek için kısılır.

### Aktivasyon Fonksiyonları

Denklem $\eqref{1}$ ile verilen ifadede yer alan $f$ fonksiyonu literatürde **aktivasyon fonksiyonu** olarak bilinmektedir. Yazımızın ilk başlarında değindiğimiz gibi doğrusal olmayan problemlerin öğrenilebilmesi için aktivasyon fonksiyonunun doğrusal olmaması gerekmektedir. [Tek Katmanlı Algılayıcı Eğitimi](#perceptron_learning) başlığında da hesapladığımız üzere seçilen aktivasyon fonksiyonunun türevlenebilir de olması gerekmektedir. Bu iki temel özellik göz önünde bulundurularak literatürde farklı aktivasyon fonksiyonları önerilmiştir.

* **Adım Aktivasyon Fonksiyonu:** 

    Adım Aktivasyon Fonksiyonu'nun en önemli özeliği tüm girdi değerlerine karşılık sadece iki farklı çıktı üretmesidir. McCulloch-Pitts nöronu olarak da bilinen bu aktivasyon fonksiyonu, Warren MuCulloch ve Walter Pitts tarafından 1943 yılında önerilen ilk yapay sinir modelidir. Önerilen modelin matematiksel ifadesi aşağıda verilmiştir.

    $$f(a) = 
    \begin{cases}
    0 & a < 0 \\
    1 & a \geq 0
    \end{cases}
    $$

    Bu modelde algılayıcı çıktaları sıfır yada bir şeklinde sadece iki çıkış verebildiğinden genellikle sınıflandırma problemlerinde tercih edilmektedir.

* **Lojistik Aktivasyon Fonksiyonu**

    [Lojistik Regresyon Analizi]({% post_url 2015-07-23-lojistik-regresyon-analizi %}) yazımızda incelenen lojistik (sigmoid) aktivasyon fonksiyonu sürekli ve türevi alınabilir bir fonksiyondur. Doğrusal olmayan ve kolay türevlenebilen bir fonskiyon olması nedeniyle yapay sinir ağı uygulamalarında en sık kullanılan aktivasyon fonksiyondur. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.
    
    $$f(a) = \frac{1}{1+e^{-a}}$$

    Bu fonksiyon girdi değerlerinin her biri için sıfır ile bir aralığında bir değer üretmektedir.

* **Tanjant Hiperbolik Aktivasyon Fonksiyonu**

    Tanjant hiperbolik fonksiyonu, sigmoid fonksiyonuna benzer bir fonksiyondur. Sigmoid fonksiyonunda çıkış değerleri $[0,1]$ aralığında değişirken hiperbolik tanjant fonksiyonunun çıkış değerleri $[-1,1]$ aralığında değişmektedir. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.

    $$f(a) = \frac{e^{a}-e^{-a}}{e^{a}+e^{-a}}$$

* **Rampa (ReLu) Aktivasyon Fonksiyonu**

    Literatürde Rectified Linear Unit (ReLu) olarak da bilinen rampa aktivasyon fonksiyonu parçalı bir aktivasyon fonksiyonudur. Yukarıda verilen aktivasyon fonksiyonlarından farklı olarak rampa aktivasyon fonksiyonunun çıkışında bir sınır bulunmamaktadır. Fonksyionun matematiksel ifadesi aşağıdaki denklemde verilmiştir.
    
    $$f(a) = 
    \begin{cases}
    0 & a < 0 \\
    a & a \geq 0
    \end{cases}
    $$

### Yapay Sinir Ağlarının Eğitilmesi
 
Her makine öğrenmesi yönteminde olduğu gibi YSA' nın da bir öğrenme sürecinden geçmesi gerekmektedir. [Tek Katmanlı Algılayıcı Eğitimi](#perceptron_learning) başlığında tek bir algılayıcının hatasının nasıl en küçükleneceğine dair matematiksel çıkarımları yapmıştık. Burada bahsedilen [Gradyan İniş Yöntemi]({post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon}) iteratif bir algoritma olduğundan YSA eğitiminin *epoch* adı verilen belirli bir iterasyon süresince devam etmesi gerekmektedir.

Her *epoch* kendi içerisinde iki ana adımdan oluşmaktadır. Bunlardan ilki İleri Besleme (Feed Forward), ikincisi Geri Yayılım (Back Propagation) adımlarıdır. 

**İleri Besleme** adımında bir önceki iterasyondan bulunan $\mathbf{w},b$ ağrılıkları ve Denklem $\eqref{1}$ kullanılarak ağın çıktısı hesaplanır. 

**Geri Yayılım** adımında Denklem $\eqref{5}$ ve Denklem $\eqref{6}$ denklemleri kullanılarak bulunan ağırlık değişimi güncel ağırlıklara eklenerek ağırlıklar güncellenir. Veri setindeki tüm örnekler için İleri Besleme ve Geri Yayılım adımları tamamlandığında bir *epoch* tamamlanmış olur. Öğrenmenin iyileştirilmesi için belirli bir başarı kriteri sağlanana veya sabit bir *epoch* sayısına ulaşana kadar eğitim adımları devam ettirilir.

---
Çok Katlı Yapay Sinir Ağlarının matematiksel ifadeleri ve Geri Yayılım Algoritması'nın anlatıldığı serinin ikinci yazısına [Yapay Sinir Ağları II]({%post_url 2020-04-25-yapay-sinir-aglari-ii %}) bağlantısından ulaşabilirsiniz.

Yazıda yer alan analizlerin yapıldığı kod parçaları, görseller ve kullanılan veri setlerine [artificial_neural_networks](https://github.com/cescript/artificial_neural_networks) GitHub sayfası üzerinden erişilebilirsiniz.

**Referanslar**
* Alpaydin, Ethem. [Introduction to machine learning](https://www.cmpe.boun.edu.tr/~ethem/i2ml3e/). MIT press, 2014.

* Bishop, Christopher M. Neural networks for pattern recognition. Oxford university press, 1995.

* Haykin, Simon. Neural networks: a comprehensive foundation. Prentice Hall PTR, 1994.

* Jain, Anil K., Jianchang Mao, and K. Moidin Mohiuddin. "Artificial neural networks: A tutorial." Computer 29.3 (1996): 31-44.

[RESOURCES]: # (List of the resources used by the blog post)
[neuron]: /assets/post_resources/artificial_neural_networks/neuron.svg
[perceptron]: /assets/post_resources/artificial_neural_networks/perceptron.svg
