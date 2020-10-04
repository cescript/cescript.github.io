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
[Yapay Sinir Ağları yazısının ilk kısmında]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) incelenen tek katmanlı algılayıcı yapısı doğrusal olmayan sınıflandırma problemlerini çözme yeteneğine sahip değildir. Bu kısıt 1969 yılında yazılan _"Perceptrons: An Introduction to Computational Geometry"_ kitabında ele alınmakta ve algılayıcı yapılarının XOR gibi basit bir problemi dahi öğrenemediği gösterilmektedir. Bu gösterim sonrasında doğrusal olmayan yapıların oluşturulabilmesi için çok katmanlı algılayıcı yapıları önerilmiş ancak bu yapıların eğitimi konusunda bir yöntem önerilmediği için sinir ağlarının kullanım alanı 1980' li yıllara kadar ciddi bir oranda kısıtlı kalmıştır. Yapay sinir ağlarının kullanımına ivme kazandıran ve problemleri çözme yeteneğini keşfettiren en önemli katkı, 1986 yılında Geoffrey E. Hinton ve arkadaşları tarafından önerilen "Geri Yayılım" algoritması ile yapılmıştır. Bu yazımızda tek katmanlı sinir ağlarından başlayarak, çok katmanlı yapay sinir ağlarının matematiksel ifadeleri oluşturulacak ve geri yayılım algoritmasının çalışmasına dair gerekli çıkarımlar yapılacaktır.

<!--more-->

Çok katmanlı algılayıcılar tek katmanlı algılayıcıların kaskat bağlanması ile oluşturulan yapılardır. Bir katmanın çıktılarının bir sonraki katmanın girişine bağlanarak oluşturulan bu yapı ile daha karmaşık problemlerin öğrenilmesi sağlanır. Çok katmanlı ağlarda, çıkışa doğrudan bağlı olmayan katmanlar için algılayıcı sayısı isteğe göre seçilebildiğinden, bir katmanda yer alan algılayıcı sayısının değiştirilmesi ile bir sonraki katmana aktarılan bilgi miktarı kontrol edilebilir ve ağın öğrenme kapasitesi kontrol altında tutulur. Benzer şekilde ağdaki katman sayısı (derinliği) da isteğe bağlı olarak artırılıp azaltılabildiğinden verilerin birbirleri ile olan ilişkileri daha iyi öğrenilebilir.

Aşağıdaki grafikte, iki tek katmanlı algılayıcının kaskat bağlanması ile oluşturulan çok katmanlı bir algılayıcı yapısı gösterilmiştir. Solda yer alan şekilde ilk yazımızda kullandığımız algılayıcı yapısı, sağdaki şekilde ise bu algılayıcıların kaskat bağlanması ile elde edilen iki katmanlı sinir ağı gösterilmiştir.

|Tek Katmanlı Algılayıcı | Çok Katmanlı Algılayıcı |
|:-------:|:----:|
![tek katmanlı algılayıcı][singlelayerperceptron] | ![çok katmanlı algılayıcı][multilayerperceptron]

Grafikten de görüldüğü üzere ilk katmanda girdi vektörlerinden ara bir çıktı ($\mathbf{x}^{(1)}$) elde edilmiş, ikinci katmanda ise bu ara çıktılar kullanılarak ağın çıktısı oluşturulmuştur. [Yapay Sinir Ağları I]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) yazısında incelediğimiz algılayıcı yapısından farklı olarak katman ve nöron sayıları da bir değişken olduğundan, ilk yazımızda kullanılan notasyona bazı eklemeler yapmak gerekmektedir. Aşağıdaki tabloda şekilde verilen matematiksel değişkenlerin tanımı listelenmiştir.

| Sembol | Açıklama |
:-------------------------:|:-------------------------
| $$\mathbf{x}_n = \left [ x_0^{n}, x_1^{n}, \dots, x_{D-1}^{n}\right ]$$ | $$\mathbf{x}_n \in \mathbb{R}^D$$ $n$. öznitelik vektörü |
| $$y_n$$ | $$\mathbf{x}_n$$ öznitelik vektörü için verilen etiket bilgisi |
| $$\mathbf{x}_n^{(0)} = \left [ x_0^{(0)}, x_1^{(0)}, \dots, x_{L_0-1}^{(0)}\right ]$$ | $$\mathbf{x}_n \in \mathbb{R}^{L_0}$$ öznitelik vektöründen elde edilen girdi vektörü |
| $$\mathbf{x}_n^{(l)} = \left [ x_0^{(l)}, x_1^{(l)}, \dots, x_{L_l-1}^{(l)}\right ]$$ | $$\mathbf{x}_n^{(l)} \in \mathbb{R}^{L_l}$$ $(l)$. katmanda elde edilen çıktı vektörü |
| $$w_{i,j}^{(l)}$$ | $(l)$. katmandaki $i$. nörondan, $(l+1)$. katmandaki $j$. nörona olan bağlantının ağrılığı |
| $$b_{i}^{(l)}$$ | $(l)$. katmandaki $i$. nöronun bias değeri |
| $$\mathbf{w}_{j}^{(l)} = \left [ w_{0,j}^{(l)}, w_{1,j}^{(l)}, \dots, w_{L_l-1,j}^{(l)}\right ]$$ | $(l)$. katmandan, $(l+1)$. katmandaki $j$. nörona olan tüm bağlantıların ağrılıkları |
| $$\mathbf{W}_{k}^{(l)}$$ | $k$ anında $(l)$. katmanı, $(l+1)$. katmana bağlayan tüm ağırlıklar |
| $$a_j^{(l)} = {\mathbf{w}_{j}^{(l)}}^\intercal \mathbf{x}_n^{(l)} + b_j^{(l)}$$ | $(l)$. katmandaki j. nöronun toplam girdisi |
| $$\hat{y}_n$$ | $$\mathbf{x}_n$$ girdi vektörü için üretilen etiket çıktısı |

<span style="color: yellow;">NOT: </span> Yazının devamında verilen eşitliklerin türetilmesi [Yapay Sinir Ağları I]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) yazısında paylaşıldığından burada eşitlikler doğrudan kullanılacaktır. Bu nedenle öncelikle [ilk yazının]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) okunması önemlidir.

Tabloda verilen notasyon kullanılarak ilk katmanın birinci çıktısı aşağıdaki denklem ile yazılır.

$$x_0^{(1)} = \mathcal{f}\left( \sum_{i=0}^{L_0-1} {w_{i,0}^{(0)} x_{i}^{(0)} + b_{0}^{(0)}} \right)$$

Verilen denklemde üst indisler ($^0$) katman numarasını, alt indisler ($_{i,0}$) ise sırasıyla girdi vektöründeki sırayı ve çıktı vektöründeki sıraları göstermektedir. Bu isimlendirme kuralı ile ilk katmanda yer alan ikinci algılayıcının çıktısı da aşağıdaki şekilde yazılabilir.

$$x_1^{(1)} = \mathcal{f}\left( \sum_{i=0}^{L_0-1} {w_{i,1}^{(0)} x_{i}^{(0)} + b_{1}^{(0)}} \right)$$

İkinci katmanda yer alan algılayıcı ise doğrudan girişe bağlı olmak yerine, kaskat bağlama neticesinde, ilk katmanın çıkışları olan $\mathbf{x}^{(1)}$ giriş vektörüne bağlıdır. İkinci katman için algılayıcı denklemi aşağıdaki şekilde yazılır.

$$\hat{y}_{n} = \mathcal{f}\left( \sum_{i=0}^{L_1-1} {w_{i,0}^{(1)} x_{i}^{(1)} + b_{0}^{(1)}} \right)$$

### Çok Katmanlı Algılayıcı Eğitimi {#multilayer_perceptron_learning}

Tek katmanlı algılayıcıların eğitiminde olduğu gibi, çok katmanlı algılayıcıların eğitimi için de [Doğrusal En Küçük Kareler Yöntemi](#perceptron_learning) ve [Gradyan İniş Yöntemleri]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) kullanılmaktadır. Çok katmanlı algılayıcı eğitimi için en küçüklenmeye çalışılan hata Denklem \ref{perceptron_learning} ile yazılabilir.

$$ E_n(\mathbf{w},b) = \frac{1}{2} (y_n - \hat{y}_n)^2 \label{perceptron_learning} \tag{2}$$

Yazılan hata fonksiyonunda yer alan $\hat{y}_n$, $\mathbf{w}$ ve $b$ değişkenlerine bağlı olduğundan, fonksiyonu en küçükleyen $\mathbf{w}$ ve $b$ değerleri sırasıyla $\frac{\partial E_n(\mathbf{w},b)}{\partial \mathbf{w}} = 0$ ve $\frac{\partial E_n(\mathbf{w},b)}{\partial b} = 0$ eşitlikleri çözülerek bulunabilir.

[Yapay Sinir Ağları yazısının ilk kısmında]({%post_url 2020-04-25-yapay-sinir-aglari-i %}) hesaplanan kısmi türevler ve tanımlanan $\delta_{n}^{(1)} = \left(y_n-\hat{y}_n \right) \mathcal{f}^\prime (a_n)$ değişkeni yardımıyla bu eşitlikler aşağıdaki şekilde tanımlanır.

$$
\nabla E_n(\mathbf{w}^{(1)},b^{(1)}) =
\begin{bmatrix}
\frac{\partial E(\mathbf{w}^{(1)},b^{(1)})}{\partial\mathbf{w}^{(1)}}\\
\frac{\partial E(\mathbf{w}^{(1)},b^{(1)})}{\partial b^{(1)}}
\end{bmatrix}
=
\begin{bmatrix}
{\delta_n^{(1)} \mathbf{x}_{n}^{(1)}}\\
{\delta_n^{(1)}}
\end{bmatrix}
$$

Elde edilen gradyan vektörü [Gradyan İniş Yönteminde]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) kullanılarak son katmanın ağırlıkları şu şekilde hesaplanabilir.

$$\mathbf{w}^{(1)}_{k+1}=\mathbf{w}^{(1)}_k-\eta \nabla E(\mathbf{w}^{(1)},b^{(1)})$$

Ancak tek katmanlı algılayıcılardan farklı olarak ağın ara katmanları için hedef çıktılar bilinmediğinden, ara katmanın ağrılıklarını hesaplamada kullanılacak, Denklem \ref{perceptron_learning} ile verilen hata fonksiyonu yazılamayacaktır. Bu durumda her bir algılayıcıdan kaynaklanan hata miktarı belirlenemeyeceği için ağırlık güncellemesinde kullanılacak olan $\nabla E(\mathbf{w}^{(0)},b^{(0)})$ vektörü bulunamayacak ve dolayısıyla $\mathbf{w}^{(0)}$ ağırlık vektörü hesaplanamayacaktır.

Burada devreye Geri Yayılım (Back Propagation) algoritması girmektedir. Geri yayılım algoritması ile en son katmanda hesaplanan hata önceki katmanlara yansıtılır. Bu yansıtılma işlemi bir katmanın çıkışında görülen toplam hatanın o katmanın girişlerine, giriş ağırlıkları oranında, pay edilmesi ile yapılır. Bu sayede herhangi bir ara katmanın çıktıya olan etkisi (ağırlığı) düşükse, çıkışta meydana gelen hatadan daha küçük pay alırken, çıktıya olan etkisi (ağırlığı) yüksekse, çıkışta meydana gelen hatadan daha büyük bir pay alması sağlanır.

Geri Yayılım algoritmasını türetebilmek için ilk yazımızda tanımladığımız $\delta_n$ değişkenini ilk katman için de yazmamız gerekmektedir. Tanım gereği $\delta_{n}^{(0)}$ aşağıdaki şekilde yazılabilir.

$$
\delta_{n}^{(0)} = \frac{\partial E_n(\mathbf{w},b)}{\partial a_n^{(0)}} = \sum_{j=0}^{L_l-1} \left(\frac{\partial E_n(\mathbf{w},b)}{\partial a_j^{(1)}} \right) \left(\frac{\partial a_j^{(1)}}{\partial \mathbf{x}_n^{(1)}} \right) \left(\frac{\partial \mathbf{x}_n^{(1)}}{\partial a_j^{(0)}} \right)
\label{backpropagation} \tag{3}
$$

Yazılan ifadede yer alan birinci kısım ilk kısımda yazdığımız $\delta_{n}^{(1)}$ değerine eşittir. $l$. katmanın girdisi $a_j^{(l)} = {\mathbf{w}_{j}^{(l)}}^\intercal \mathbf{x}_n^{(l)} + b_j^{(l)}$ şeklinde tanımlandığından, ikinci kısmın türevinden ise 

$$
\frac{\partial a_j^{(1)}}{\partial \mathbf{x}_n^{(1)}} = \mathbf{w}_{j}^{(1)}
$$

bulunur. Üçüncü kısmın türevi ise $\mathbf{x}_n^{(l)} = \mathcal{f}(a_j^{(l-1)})$ tanımı kullanılarak;

$$
\frac{\partial \mathbf{x}_n^{(1)}}{\partial a_j^{(0)}} = \mathcal{f}^\prime (a_j^{(0)})
$$

şeklinde hesaplanır. Bulunan değerler Denklem \ref{backpropagation} de yerine yazılırsa;

$$\delta_{n}^{(0)} = \sum_{j=0}^{L_l-1} \delta_{n}^{(1)} \mathbf{w}_{j}^{(1)} \mathcal{f}^\prime (a_j^{(0)}) $$ elde edilir.

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

