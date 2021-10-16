---
layout: post
title: Yapay Sinir Ağları II
slug: artificial-neural-networks-II
author: Bahri ABACI
categories:
- Makine Öğrenmesi
- Nümerik Yöntemler
- Veri Analizi
references: ""
published: false
thumbnail: /assets/post_resources/artificial_neural_networks/thumbnail_2.png
---
Yapay Sinir Ağları serisinin [ilk kısmında]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) incelenen tek katmanlı algılayıcı yapısı doğrusal olmayan sınıflandırma problemlerini çözme yeteneğine sahip değildir. Bu kısıt 1969 yılında yazılan *"Perceptrons: An Introduction to Computational Geometry"* kitabında ele alınmakta ve algılayıcı yapılarının XOR gibi basit bir problemi dahi öğrenemediği gösterilmektedir. Bu gösterim sonrasında doğrusal olmayan yapıların oluşturulabilmesi için çok katmanlı algılayıcı yapıları önerilmiş ancak bu yapıların eğitimi konusunda bir yöntem önerilmediği için sinir ağlarının kullanım alanı 1980' li yıllara kadar ciddi oranda kısıtlı kalmıştır. Yapay sinir ağlarının kullanımına ivme kazandıran ve problemleri çözme yeteneğini keşfettiren en önemli katkı, 1986 yılında Geoffrey E. Hinton ve arkadaşları tarafından önerilen "Geri Yayılım" algoritması ile yapılmıştır. Bu yazımızda tek katmanlı sinir ağlarından başlayarak, çok katmanlı yapay sinir ağlarının matematiksel ifadeleri oluşturulacak ve geri yayılım algoritmasının çalışmasına dair gerekli çıkarımlar yapılacaktır.

<!--more-->

Çok katmanlı algılayıcılar tek katmanlı algılayıcıların kaskat bağlanması ile oluşturulan yapılardır. Bir katmanın çıktılarının bir sonraki katmanın girişine bağlanarak oluşturulan bu yapı ile daha karmaşık problemlerin öğrenilmesi sağlanır. Çok katmanlı ağlarda, çıkışa doğrudan bağlı olmayan katmanlar için algılayıcı sayısı isteğe göre seçilebildiğinden, bir katmanda yer alan algılayıcı sayısının değiştirilmesi ile bir sonraki katmana aktarılan bilgi miktarı kontrol edilebilir ve ağın öğrenme kapasitesi kontrol altında tutulur. Benzer şekilde ağdaki katman sayısı (derinliği) da isteğe bağlı olarak artırılıp azaltılabildiğinden verilerin birbirleri ile olan ilişkileri daha iyi öğrenilebilir.

Aşağıdaki grafikte, iki tek katmanlı algılayıcının kaskat bağlanması ile oluşturulan çok katmanlı bir algılayıcı yapısı gösterilmiştir. Solda yer alan şekilde ilk yazımızda kullandığımız algılayıcı yapısı, sağdaki şekilde ise bu algılayıcıların kaskat bağlanması ile elde edilen iki katmanlı sinir ağı gösterilmiştir.

|Tek Katmanlı Algılayıcı | Çok Katmanlı Algılayıcı |
|:-------:|:----:|
![tek katmanlı algılayıcı][singlelayerperceptron] | ![çok katmanlı algılayıcı][multilayerperceptron]

Grafikten de görüldüğü üzere ilk katmanda girdi vektörlerinden ara bir çıktı ($\mathbf{x}^{(1)}$) elde edilmiş, ikinci katmanda ise bu ara çıktılar kullanılarak ağın çıktısı oluşturulmuştur. [Yapay Sinir Ağları I]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) yazısında incelediğimiz algılayıcı yapısından farklı olarak katman ve nöron sayıları da bir değişken olduğundan, ilk yazımızda kullanılan notasyona bazı eklemeler yapmak gerekmektedir. Aşağıdaki tabloda şekilde verilen matematiksel değişkenlerin tanımı listelenmiştir.

| Sembol | İçerik | Boyut | Açıklama |
:-------:|:------:|:-----:|:--------|
| $\mathbf{x}_n$ | $\left [ x_0, x_1, \dots, x_{D-1}\right ]^\intercal$ | $D \times 1$ |  $n$. öznitelik vektörü |
| $y_n$ | | $1 \times 1$ | $\mathbf{x}_n$ öznitelik vektörü için verilen etiket bilgisi |
| $\mathbf{x}_n^{(0)}$ | $\left [ x_0^{(0)}, x_1^{(0)}, \dots, x_{L_0-1}^{(0)}\right ]^\intercal$ | $L_0 \times 1$ | $\mathbf{x}_n$ öznitelik vektöründen elde edilen girdi vektörü, $L_0 = D$ |
| $\mathbf{x}_n^{(l)}$ | $\left [ x_0^{(l)}, x_1^{(l)}, \dots, x_{L_l-1}^{(l)}\right ]^\intercal$ | $L_l \times 1$ | $(l)$. katmanda elde edilen çıktı vektörü |
| $w_{i,j}^{(l)}$ | | $1 \times 1$ | $(l)$. katmandaki $i$. nörondan, $(l+1)$. katmandaki $j$. nörona olan bağlantının ağrılığı |
| $\mathbf{w}_{j}^{(l)}$ | $\left [ w_{0,j}^{(l)}, w_{1,j}^{(l)}, \dots, w_{L_l-1,j}^{(l)}\right ]^\intercal$ | $L_l \times 1$ | $(l)$. katmandan, $(l+1)$. katmandaki $j$. nörona olan tüm bağlantıların ağrılıkları |
| $\mathbf{W}^{(l)}$ | $$\left[ \mathbf{w}_{0}^{(l)}, \mathbf{w}_{1}^{(l)}, \dots, \mathbf{w}_{L_{l+1}-1}^{(l)} \right] $$ | $L_l \times L_{l+1}$ | $(l)$. katmanı, $(l+1)$. katmana bağlayan tüm ağırlıklar |
| $b_{i}^{(l)}$ | | $1 \times 1$ | $(l+1)$. katmandaki $i$. nöronun bias değeri |
| $\mathbf{b}^{(l)}$ | $\left[b_{0}^{(l)},b_{1}^{(l)}, \dots, b_{L_{l+1}-1}^{(l)}  \right]^\intercal$ | $L_{l+1} \times 1$ | $(l+1)$. katmandaki tüm bias değerleri |
| $a_j^{(l)}$ | ${\mathbf{w}_{j}^{(l)}}^\intercal \mathbf{x}_n^{(l)} + b_j^{(l)}$ | $1 \times 1$ | $(l)$. katmandaki j. nöronun toplam girdisi |
| $\mathbf{a}_n^{(l)}$ | ${\mathbf{W}^{(l)}}^\intercal \mathbf{x}_n^{(l)} + \mathbf{b}^{(l)}$ | $L_l \times 1$ | $(l)$. katmandaki nöronların toplam girdisi |
| $\hat{y}_n$ | | $1 \times 1$ | $\mathbf{x}_n$ girdi vektörü için üretilen etiket çıktısı |


> Yazının devamında verilen eşitliklerin türetilmesi [Yapay Sinir Ağları I]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) yazısında paylaşıldığından burada eşitlikler doğrudan kullanılacaktır. Bu nedenle öncelikle [ilk yazının]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) okunması önemlidir.

Tabloda verilen notasyon kullanılarak ilk katmanın birinci çıktısı aşağıdaki denklem ile yazılır.

$$x_0^{(1)} = f\left( \sum_{i=0}^{L_0-1} {w_{i,0}^{(0)} x_{i}^{(0)} + b_{0}^{(0)}} \right) = f\left( {\mathbf{w}_{0}^{(0)}}^\intercal \mathbf{x}_n^{(0)} + b_0^{(0)} \right)$$

Verilen denklemde üst indisler ($^0$) katman numarasını, alt indisler ($_{i,0}$) ise sırasıyla girdi vektöründeki sırayı ve çıktı vektöründeki sıraları göstermektedir. Bu isimlendirme kuralı ile ilk katmanda yer alan ikinci algılayıcının çıktısı da aşağıdaki şekilde yazılabilir.

$$x_1^{(1)} = f\left( \sum_{i=0}^{L_0-1} {w_{i,1}^{(0)} x_{i}^{(0)} + b_{1}^{(0)}} \right) = f\left( {\mathbf{w}_{1}^{(0)}}^\intercal \mathbf{x}_n^{(0)} + b_1^{(0)} \right)$$

Özet olarak, ilk katmandaki tüm çıktılar vektör biçiminde;

$$
\mathbf{x}_n^{(1)} = f\left( {\mathbf{W}^{(0)}}^\intercal \mathbf{x}_n^{(0)} + \mathbf{b}^{(0)}  \right)
$$

şeklinde yazılabilir.

İkinci katmanda yer alan algılayıcı ise doğrudan girişe bağlı olmak yerine, kaskat bağlama neticesinde, ilk katmanın çıkışları olan $\mathbf{x}_n^{(1)}$ giriş vektörüne bağlıdır. Bu nedenle ikinci katman için algılayıcı denklemi $\mathbf{x}_n^{(1)}$'e bağlı olarak yazılır.

İlk olarak ikinci katmanın toplam girdisi aşağıdaki şekilde yazılır.

$$
\mathbf{a}_n^{(1)} = \sum_{i=0}^{L_1-1} {w_{i,0}^{(1)} x_{i}^{(1)} + b_{0}^{(1)}} = {\mathbf{w}_{0}^{(1)}}^\intercal \mathbf{x}_n^{(1)} + b_0^{(1)} = {\mathbf{W}^{(1)}}^\intercal \mathbf{x}_n^{(1)} + \mathbf{b}^{(1)}
$$

Ardından girdi vektörü kullanılarak, katmanın çıktısı aşağıdaki şekilde yazılır.

$$\hat{y}_{n} = f\left( \mathbf{a}_n^{(1)}\right)$$

### Çok Katmanlı Algılayıcı Eğitimi {#multilayer_perceptron_learning}

Tek katmanlı algılayıcıların eğitiminde olduğu gibi, çok katmanlı algılayıcıların eğitimi için de [Karesel Hata](#perceptron_learning) ve [Gradyan İniş Yöntemleri]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) kullanılmaktadır. Çok katmanlı algılayıcı eğitimi için en küçüklenmeye çalışılan hata Denklem $\eqref{1}$ ile yazılabilir.

$$ E_n(\mathbf{W},\mathbf{b}) = \frac{1}{2} (y_n - \hat{y}_n)^2 \tag{1}$$

Yazılan hata fonksiyonunda yer alan $\hat{y}_n$, $\mathbf{W}^{(1)}$ ve $\mathbf{b}^{(1)}$ değişkenlerine bağlı olduğundan, fonksiyonu en küçükleyen $\mathbf{W}^{(1)}$ ve $\mathbf{b}^{(1)}$ değerleri sırasıyla $\frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{W}^{(1)}} = 0$ ve $\frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{b}^{(1)}} = 0$ eşitlikleri çözülerek bulunabilir.

[Yapay Sinir Ağları yazısının ilk kısmında]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) detaylı şekilde hesaplanan kısmi türev ifadeleri bu problem için aşağıdaki şekilde yazılabilir.

$$\frac{\partial E_n(\mathbf{W},b)}{\partial\mathbf{W}^{(1)}} = \left( \frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{a}_n^{(1)}} \right) \left( \frac{\partial \mathbf{a}_n^{(1)}}{\partial\mathbf{W}^{(1)}} \right) \tag{2}$$

Denklem $\eqref{2}$ ile oluşturulan ifadedenin ilk kısmı önceki yazımızda tanımlanan $\delta$ vektörüdür. Bu vektör kısmi türevler kullanılarak aşağıdaki şekilde hesaplanır.

$$
\mathbf{\delta}_n^{(1)} = \frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{a}_n^{(1)}} = \left(y_n-\hat{y}_n \right) \mathbf{J}(f) (\mathbf{a}_n^{(1)}) \tag{2.1}
$$

Burada dikkat edilmesi gereken önemli bir nokta, ilk yazımızdan farklı olarak, $f: \mathbb{R}^{L_l} \to \mathbb{R}^{L_l}$ çok boyutlu uzaydan bir başka çok boyutlu uzaya bir dönüşüm işlemi gerçekleştirmektedir. Bu nedenle $f^\prime(\mathbf{a}_n^{(1)})$ ifadesi yerine $f$ fonksiyonun Jacobain ifadesi $\mathbf{J}(f)$ kullanılmıştır. Bu problem için $\mathbf{a}_n^{(1)}$ vektörü $1 \times 1$ boyutlu bir skaler olduğundan; $\mathbf{J}(f) = f^\prime$ olacaktır.

Denklem $\eqref{2}$ ifadesindeki ikinci kısmi trüev ise $\mathbf{a}_n^{(1)} = {\mathbf{W}^{(1)}}^\intercal \mathbf{x}_n^{(1)} + \mathbf{b}^{(1)}$ tanımı kullanılarak;

$$
\frac{\partial \mathbf{a}_n^{(1)}}{\partial\mathbf{W}^{(1)}} = \mathbf{x}_n^{(1)}\tag{2.2}
$$

şeklinde hesaplanır.

Denklem $\eqref{2.1}$ ve $\eqref{2.2}$ ile hesaplanan vektörler kullanılarak; $\mathbf{W}^{(1)}$ ve $\mathbf{b}^{(1)}$ için gradyan iniş yöntemi uygulanırsa;

$$
\begin{aligned}
\mathbf{W}^{(1)}_{k+1} &=\mathbf{W}^{(1)}_k-\eta \nabla_w E(\mathbf{W}^{(1)},\mathbf{b}^{(1)}) &&= \mathbf{W}^{(1)}_k - \eta {\delta^{(1)}_n \mathbf{x}^{(1)}_{n}}\\
\mathbf{b}^{(1)}_{k+1} & =\mathbf{b}^{(1)}_k-\eta \nabla_b E(\mathbf{W}^{(1)},\mathbf{b}^{(1)}) & &= \mathbf{b}_k - \eta \delta^{(1)}_n\\
\end{aligned}
$$

iterasyonları elde edilir.

Elde edilen bu iterasyonlar $1.$ katman ağırlık ve bias vektörlerini güncellemek için yeterlidir. Ancak hata ağdaki tüm ağırlıklara bağlı olduğundan, gradyan iniş yönteminin önceki katmanlara da uygulanması gereklidir.

Ancak son katmandan farklı olarak ağın ara katmanları için hedef çıktılar bilinmediğinden, ara katmanın ağrılıklarını hesaplamada kullanılacak, Denklem $\eqref{1}$ ile verilen hata fonksiyonu yazılamayacaktır. Bu durumda her bir algılayıcıdan kaynaklanan hata miktarı belirlenemeyeceği için ağırlık güncellemesinde kullanılacak olan $\nabla E(\mathbf{W}^{(0)},\mathbf{b}^{(0)})$ vektörü bulunamayacak ve dolayısıyla $\mathbf{W}^{(0)}$ ağırlık vektörü hesaplanamayacaktır.

#### Geri Yayılım Algoritması
Yukarıda bahsedilen problem Geri Yayılım (Back Propagation) algoritması ile çözülmektedir. Geri yayılım algoritmasında en son katmanda hesaplanan hata önceki katmanlara paylaştırılmaktadır. Bu paylaştırma işlemi bir katmanın çıkışında görülen toplam hatanın o katmanın girişlerine, giriş ağırlıkları oranında, pay edilmesi ile yapılmaktdır. Bu sayede herhangi bir ara katmanın çıktıya olan etkisi (ağırlığı) düşükse, çıkışta meydana gelen hatadan daha küçük pay alırken, çıktıya olan etkisi (ağırlığı) yüksekse, çıkışta meydana gelen hatadan daha büyük bir pay alması sağlanır.

Algoritmanın türetilmesi için ilk olarak çıkışta görülen hatanın giriş ağırlıklarına bağlı değişimini zincir kuralı ile yazalım.

$$
\frac{\partial E_n(\mathbf{W},b)}{\partial\mathbf{W}^{(0)}} = \left( \frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{a}_n^{(0)}} \right) 
\left( \frac{\partial \mathbf{a}_n^{(0)}}{\partial \mathbf{W}^{(0)}} \right) \tag{3}
$$

Yazılan ifadede ilk kısım tanım gereği $\delta_n^{(0)}$ dir. Bu vektör farklı kısmi türevlerin çarpımı ile aşağıdaki şekilde hesaplanır. 

$$
\mathbf{\delta}_{n}^{(0)} = \frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{a}_n^{(0)}} = \left(\frac{\partial E_n(\mathbf{W},\mathbf{b})}{\partial \mathbf{a}_n^{(1)}} \right) \left(\frac{\partial \mathbf{a}_n^{(1)}}{\partial \mathbf{x}_n^{(1)}} \right) \left(\frac{\partial \mathbf{x}_n^{(1)}}{\partial \mathbf{a}_n^{(0)}} \right)
$$

Yazılan ifadede yer alan birinci kısım Denklem $\eqref{2.1}$'de hesapladığımız $\delta_{n}^{(1)}$ değerine eşittir. $l$. katmanın girdisi $\mathbf{a}_n^{(l)} = {\mathbf{W}^{(l)}}^\intercal \mathbf{x}_n^{(l)} + \mathbf{b}^{(l)}$ şeklinde tanımlandığından, ikinci kısmın türevinden ise 

$$
\frac{\partial \mathbf{a}_n^{(1)}}{\partial \mathbf{x}_n^{(1)}} = \mathbf{W}^{(1)}
$$

bulunur. Son olarak üçüncü kısmın türevi ise $\mathbf{x}_n^{(l)} = f(\mathbf{a}_n^{(l-1)})$ tanımı kullanılarak;

$$
\frac{\partial \mathbf{x}_n^{(1)}}{\partial \mathbf{a}_n^{(0)}} = \mathbf{J}(f) (\mathbf{a}_n^{(0)}) = 
\begin{bmatrix} 
f^\prime(a_0^{(0)}) & 0\\
0 & f^\prime(a_1^{(0)})
\end{bmatrix}
$$

şeklinde hesaplanır. Bulunan değerler Denklem $\eqref{3.1}$ de yerine yazılırsa;

$$
\delta_{n}^{(0)} = \delta_{n}^{(1)} \mathbf{W}^{(1)} \begin{bmatrix} 
f^\prime(a_0^{(0)}) & 0\\
0 & f^\prime(a_1^{(0)})
\end{bmatrix} = \sum_{j=0}^{L_{l+1}-1} \delta_{n}^{(1)} \mathbf{w}_j^{(1)} f^\prime (\mathbf{a}_j^{(0)}) \tag{3.1}
$$ 

denklemi elde edilir. 

Denklem $\eqref{3}$ ile verilen eşitliği oluşturmak için gereken ikinci kısmi türev, $\mathbf{a}_n^{(0)} = {\mathbf{W}^{(0)}}^\intercal \mathbf{x}_n^{(0)} + \mathbf{b}^{(0)}$ tanımı kullanılarak;

$$
\frac{\partial \mathbf{a}_n^{(0)}}{\partial\mathbf{W}^{(0)}} = \mathbf{x}_n^{(0)}\tag{3.2}
$$

şeklinde hesaplanır.

Denklem $\eqref{3.1}$ ve $\eqref{3.2}$ vektörleri kullanılarak; $\mathbf{W}^{(0)}$ ve $\mathbf{b}^{(0)}$ için gradyan iniş yöntemi uygulanırsa;

$$
\begin{aligned}
\mathbf{W}^{(0)}_{k+1} &=\mathbf{W}^{(0)}_k-\eta \nabla_w E(\mathbf{W}^{(0)},\mathbf{b}^{(0)}) &&= \mathbf{W}^{(0)}_k - \eta {\delta^{(0)}_n \mathbf{x}^{(0)}_{n}}\\
\mathbf{b}^{(0)}_{k+1} & =\mathbf{b}^{(0)}_k-\eta \nabla_b E(\mathbf{W}^{(0)},\mathbf{b}^{(0)}) & &= \mathbf{b}_k - \eta \delta^{(0)}_n\\
\end{aligned}
$$

iterasyonları elde edilir.

Denklem $\eqref{3.1}$ ilk bakışta sıradan görünse de, $\delta_n^{(0)}$ değerini $\delta_n^{(1)}$ cinsinden hesaplamaya yaradığından, son katmandaki hatanın iteratif olarak önceki katmanlara yayılabilmesini sağlamaktadır. Bu sayede çok katmanlı bir ağın çıktısındaki hata, algoritmik olarak girdilerine yayılabilmektedir. Bu nedenle algoritma **Geri Yayılım** algoritması olarak isimlendirilmektedir.

Çok Katlı Yapay Sinir Ağlarının Geri Yayılım Algoritması kullanılarak eğitiminin kodlandığı serinin üçüncü yazısına [Yapay Sinir Ağları III]({%post_url 2020-04-25-yapay-sinir-aglari-iii %}) bağlantısından ulaşabilirsiniz.

Yazıda yer alan analizlerin yapıldığı kod parçaları, görseller ve kullanılan veri setlerine [artificial_neural_networks](https://github.com/cescript/artificial_neural_networks) GitHub sayfası üzerinden erişilebilirsiniz.

**Referanslar**
* Alpaydin, Ethem. [Introduction to machine learning](https://www.cmpe.boun.edu.tr/~ethem/i2ml3e/). MIT press, 2014.

* Bishop, Christopher M. Neural networks for pattern recognition. Oxford university press, 1995.

* Haykin, Simon. Neural networks: a comprehensive foundation. Prentice Hall PTR, 1994.

* Jain, Anil K., Jianchang Mao, and K. Moidin Mohiuddin. "Artificial neural networks: A tutorial." Computer 29.3 (1996): 31-44.

* Rumelhart, David E., Geoffrey E. Hinton, and Ronald J. Williams. "Learning representations by back-propagating errors." nature 323.6088 (1986): 533-536.

[RESOURCES]: # (List of the resources used by the blog post)
[singlelayerperceptron]: /assets/post_resources/artificial_neural_networks/singlelayerperceptron.svg
[multilayerperceptron]: /assets/post_resources/artificial_neural_networks/multilayerperceptron.svg

