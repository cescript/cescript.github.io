---
layout: post
title: Newton Yöntemi
slug: newton-method
author: Bahri ABACI
categories:
- Nümerik Yöntemler
- Lineer Cebir
thumbnail: /assets/post_resources/newton_method/thumbnail.png 
---
1643-1727 yılları arasında yaşayan İngiliz bilim insanı Sir Isaac Newton; astronomi, matematik, mekanik ve optik gibi alanlarda yaptığı çalışmalarla bilim dünyasının en önemli kişilerinden biri olmuştur. Bir cismin hareketi ve uygulanan kuvvet arasındaki ilişkiyi gösteren hareket yasaları, iki kütle arasında oluşan çekim kuvvetini gösteren evrensel kütle çekim yasası ve ışığın kırılması, dağılması ve bükülmesi konularında yayınladığı *opticks* kitabı fizik alanında öne çıkan katkılarındadır. Newton, fizik alanında yaptığı çalışmalaralara ek olarak matematiğin birçok alanında da önemli çalışmalara imza atmıştır. Bu yazıda Newton' un nümerik analiz alanında geliştirdiği yöntemler incelenecek ve örnek problemlerin çözümü yapılacaktır.

<!--more-->

Nümerik yöntemler matematiksel problemleri doğrudan çözmek yerine, daha kolay çözülebilir yan problemleri kullanarak ana problemin çözümüne yaklaşmayı hedefleyen yöntemlerdir. Bu yöntemler genellikle iteratif yapıda tanımlanan algoritmalar olduklarından karmaşık matematiksel problemlerin bilgisayarla çözümünde sıklıkla kullanılmaktadır. Bu blogda daha önce işlediğimiz [Jacobi Özdeğer Algoritması]({% post_url 2019-03-26-ozdeger-ve-ozvektorler %}), [Gradyan İniş Yöntemi]({% post_url 2020-04-08-gradyan-yontemleri-ile-optimizasyon %}) ve [Successive Over-Relaxation]({% post_url 2020-09-20-poisson-denklemi-yardimiyla-goruntu-duzenleme %}) gibi yöntemler bu yöntemlere örnek olarak verilebilir. Bu çalışmada doğrusal olmayan denklemlerin çözümü ve optimizasyonunda kullanılan Newton yöntemi incelenecektir. 

$F: \mathcal{R}^n \to \mathcal{R}^m$ şeklinde modellenen bir sistemde, $\mathbf{x} \in \mathcal{R}^n$ sistemin girdilerini, $\mathbf{y} \in \mathcal{R}^m$ ise sistemin çıktılarını göstersin. Bu durumda girdi ile çıktı arasındaki ilişki $\mathbf{y}=F(\mathbf{x})$ şeklinde gösterilebilir.

[Bir önceki yazımızda]({% post_url 2021-05-11-dogrusal-ters-problemler %}), $F(\mathbf{x}) = \mathbf{A}\mathbf{x}$ şeklinde doğrusal bir fonksiyon seçildiğinde elde edilen problemleri ve çözümlerinden bahsetmiştik. Bu yazımızda $F$ fonksiyonunun doğrusal olmaması durumunda nasıl bir yol izleneceği anlatılacaktır.

Anlatım sırasında problemi daha kolay açıklayabilmek için hesaplamalar $n=m=1$ kabul edilerek skaler uzayda yapılacaktır. Bu durumda çözülen problem $y=f(x)$ şeklinde gösterilecektir. Problemin çözümü bulunduktan sonra elde edilen yöntem değerlerin vektör olması durumuna genellenecektir.

## Newton Yöntemi

Newton yöntemi temelde çok basit bir fikre dayanmaktadır. Newton yönteminde doğrusal olmayan matematiksel problem, Taylor serisi kullanılarak bir doğrusal probleme dönüştürülür. Ardından bu doğrusal problem iteratif olarak çözülerek doğrusal olmayan problemin çözümü elde edilir.

### Newton Yöntemi ile Kök Bulma

İlk olarak bu yöntemi $f(x^\ast) = 0$ şartını sağlayan $x^\ast$ köklerini bulmak için kullanalım. $f(x)$ fonksiyonu, Taylor serisi kullanılarak 

$$f(x+h) \approx f(x) + f'(x)h$$

şeklinde yazılabilir. Yazılan bu ifade $h$ değişkenine bağlı olarak doğrusa bir ifadedir. İfadeyi sıfıra eşit yapan $h$ değeri çözülürse;

$$h = -\frac{f(x)}{f'(x)}$$

olması gerektiği görülür. Yani $f$ fonksiyonunu yaklaşık sıfır yapan $x+h$ değeri $x-f(x) / f'(x)$ ile bulunur. Yazılan ifade bir yaklaşım olduğundan, $x$ iteratif olarak hesaplanarak gerçek sıfıra doğru yaklaşılabilir. Bu durumda iterasyonlar 

$$x_{k+1} = x_k - \frac{f(x_k)}{f'(x_k)}$$ 

şeklinde hesaplanır. Burada dikkat edilmesi gereken bir nokta başlangıç noktası $x_0$ ile $x^\ast$ arasında herhangi bir noktada $f'(x_k)=0$ olması durumunda Newton yönteminin çalışmayacağıdır.

Problemi kodlamadan önce bir örnek ile anlamaya çalışalım. Köklerini bulmak istediğimiz örnek fonksiyonumuz $f(x) = x^3 -3x + 2$ olsun. Bu durumda fonksiyonun türevi $f'(x) = 3x^2-3$ olacaktır. $x_0 = 2$ seçerek Newton iterasyonlarını uygularsak;

- $x_1 = x_0 - \frac{f(x_0)}{f'(x_0)} = 2.00 - \frac{2.00^3-3 \cdot 2.00 + 2}{3 \cdot 2.00^2 - 3} = 1.55$
- $x_2 = x_1 - \frac{f(x_1)}{f'(x_1)} = 1.55 - \frac{1.55^3-3 \cdot 1.55 + 2}{3 \cdot 1.55^2 - 3} = 1.29$
- $x_3 = x_2 - \frac{f(x_2)}{f'(x_2)} = 1.29 - \frac{1.29^3-3 \cdot 1.29 + 2}{3 \cdot 1.29^2 - 3} = 1.16$
- $x_4 = x_3 - \frac{f(x_3)}{f'(x_3)} = 1.16 - \frac{1.16^3-3 \cdot 1.16 + 2}{3 \cdot 1.16^2 - 3} = 1.08$
- $x_5 = x_4 - \frac{f(x_4)}{f'(x_4)} = 1.08 - \frac{1.08^3-3 \cdot 1.08 + 2}{3 \cdot 1.08^2 - 3} = 1.04$

değerleri elde edilir. Görüldüğü üzere yöntem $x_0=2$ noktasından başlayarak $x^\ast=1.00$ noktasına doğru yakınsamaktadır. Bu nokta $f(x)$ fonksiyonun iki kökünden biridir. Fonksiyonun diğer kökü $x^\ast=-2.00$ noktasındadır. Bu noktanın bulunması için Newton iterasyonlarının $x^\ast=-2.00$ noktasına daha yakın bir noktadan başlatılması yeterlidir.

Yukarıda verilen matematiksel ifade C programlama dilinde aşağıdaki şekilde kodlanabilir.


```c
// define function and its derivative
float f(float x) { return powf(x,3) - 3*x + 2; }
float fd(float x) { return 3*powf(x,2) - 3; }

int main(int argc, char *argv[]) 
{
    // get the starting point from the user
    float x = argc > 1 ? atof(argv[1]) : 0.0f;

    // choose approximate zero to stop iterations
    float eps = 1e-6;

    // try to find approximate zero
    while(fabs(f(x)) > eps && fd(x) != 0.0f)
    {
        // single step newton iteration
        x = x - f(x) / fd(x);
    }

    // print the result
    printf("f(%5.3f) = %5.3f\n", x, f(x));

    return 0;
}
```


Yazılan kod parçası $f(x), f'(x)$ ve $x_0$ değerlerini girdi olarak almakta ve $\lvert f(x_k) \lvert < \epsilon$ yeteri kadar küçük olana kadar iterasyonlara devam etmektedir.

Verilen yöntem $x_0 = 2$ seçilerek başlatıldığında $k=12$ iterasyon sonucunda $x^\ast=1.00$ noktasına, $x^\ast=-5.00$ seçildiğinde $k=6$ iterasyon sonucunda $x=-2.00$ noktasına yakınsamaktadır.

Newton yöntemi oldukça basit olmasına rağmen kökü bulunmak istenilen fonksiyonun türevine de ihtiyaç duyduğundan pratik kullanımda bazı zorluklar yaratmaktadır. Ancak bu dezavantaj sonlu farklar yöntemi kullanılarak kolayca aşılabilir. Sonlu farklar yönteminde türev fonksiyonuna;

$$f'(x_k) \approx \frac{f(x_k) - f(x_{k-1})}{x_k - x_{k-1}}$$

şeklinde bir yaklaşım yapılır. Bu yaklaşım Newton yönteminde elde edilen iterasyonlara uygulanırsa;

$$x_{k+1} = x_k - f(x_k) \left( \frac{x_k - x_{k-1}}{f(x_k) - f(x_{k-1})} \right)$$ 

iterasyonları elde edilir. Bu yöntem literatürde **Secant** yöntemi olarak bilinmektedir. Bu yöntem Newton yöntemine göre hesaplaması daha kolay olsa da türev fonksiyonuna da bir yaklaşım yapıldığından yakınsama hızı daha düşüktür.

#### Çok Boyutlu Uzayda Kök Bulma

$\mathbf{y}=F(\mathbf{x})$ şeklinde vektör-vektör uzayında tanımlanan bir fonksiyon için Taylor serisi kullanılarak fonksiyona bir yaklaşımda bulunmak istersek;

$$
F(\mathbf{x} + \mathbf{h}) \approx F(\mathbf{x}) + \mathbf{J}_F(\mathbf{x}) \mathbf{h}
$$

eşitliği yazılır. Burada $\mathbf{J}_F(\mathbf{x})$, $F$ fonksiyonunun kısmi türevlerini içerenen Jacobi matrisidir.

$$
\mathbf{J}_F(\mathbf{x}) =
\begin{bmatrix}
\nabla^\intercal f_1(\mathbf{x})\\
\nabla^\intercal f_2(\mathbf{x})\\
\vdots\\
\nabla^\intercal f_m(\mathbf{x})\\
\end{bmatrix}
$$

Bu durumda $F(\mathbf{x} + \mathbf{h}) = \mathbf{0}$ yapacak olan $\mathbf{h}$ değeri; $\mathbf{J}_F(\mathbf{x}) \mathbf{h} = -F(\mathbf{x})$ denklem setinin çözülmesi ile elde edilecektir.



### Newton Yöntemi ile Optimizasyon

Bir önceki adımda verilen bir $f$ fonksiyonu için $f(x^\ast)=0$ şartını sağlayan $x^\ast$ noktası bulunmuştu. Bu bölümde fonksiyonun sıfır noktası yerine, yerel en küçük/büyük noktası bulunmaya çalışılacaktır.

Bir fonksiyonun yerel en küçük/büyük noktasında türevi varsa bu türev değeri sıfıra eşittir. Türevi sıfır yapan bu noktaya fonksiyonun kritik noktası denir. Buradan hareketle kritik noktaların bulunması için $f'(x)=0$ denkleminin çözülmesi gerektiği görülür.

Problemi Newton yöntemi ile çözmek için $g(x) = f'(x)$ tanımı yapar ve $g(x^\ast) = 0$ şartını sağlayan $x^\ast$ noktası için Newton iterasyonlarını yazarsak;

$$x_{k+1} = x_k - \frac{g(x_k)}{g'(x_k)} = x_k - \frac{f'(x_k)}{f''(x_k)}$$

iterasyonları elde edilir. İlk iterasyona benzer şekilde başlangıç noktası $x_0$ ile $x^\ast$ arasında herhangi bir noktada $f''(x_k)=0$ olması durumunda Newton yönteminin çalışmayacağına dikkat edilmelidir.

Yöntemi daha iyi anlamak için $f(x)=x^4 -2x + 5$ şeklinde tanımlanan bir fonksiyonun kritik noktasını, $x_0 = 2$ noktasından başlayarak bulmaya çalışalım. 

Verilen fonksiyonun türevi $f'(x)=4x^3-2$ ve ikinci türevi $f''(x)=12x^2$ şeklinde hesaplanmaktadır. Bu fonksiyonlar kullanılarak Newton iterasyonları uygulanırsa;

- $x_1 = x_0 - \frac{f'(x_0)}{f''(x_0)} = 2.00 - \frac{4 \cdot 2.00^3 - 2}{12 \cdot 2.00^2} = 1.38$
- $x_2 = x_1 - \frac{f'(x_1)}{f''(x_1)} = 1.38 - \frac{4 \cdot 1.38^3 - 2}{12 \cdot 1.38^2} = 1.00$
- $x_3 = x_2 - \frac{f'(x_2)}{f''(x_2)} = 1.00 - \frac{4 \cdot 1.00^3 - 2}{12 \cdot 1.00^2} = 0.83$
- $x_4 = x_3 - \frac{f'(x_3)}{f''(x_3)} = 0.83 - \frac{4 \cdot 0.83^3 - 2}{12 \cdot 0.83^2} = 0.79$

adımları elde edilir. Görüldüğü üzere iterasyonlar fonksiyonun en küçük noktası olan $x^\ast = 1/\sqrt[3]{2} \approx 0.794$ değerine yakınsamaktadır. Yukarıda yazılan C kodu kullanılarak iterasyonlar hesaplandığında, $x_k$ değerinin $k=6$ adımda $x^\ast = 0.794$ noktasına yakınsadığı görülmektedir.

Bu noktanın fonksiyonun en küçük/büyük noktası olduğunda karar vermek için ikinci türevin değerine bakılmalıdır. $f''(x^\ast) > 0$ olduğundan bu nokta verilen fonksiyonun en küçük noktasıdır.

#### Çok Boyutlu Uzayda Optimizasyon

Bir boyutlu uzayda yaptığımız $g(x) = f'(x)$ tanımını çok boyutlu uzayda $G(\mathbf{x}) = \nabla F(\mathbf{x})$ şeklinde yaparsak; $G(\mathbf{x})$ fonksiyonunun kökleri $F(\mathbf{x})$ fonksiyonunun kritik noktaları olacaktır.

Bu durumda Taylor serisi kullanılarak;

$$
G(\mathbf{x} + \mathbf{h}) \approx G(\mathbf{x}) + \mathbf{J}_G(\mathbf{x}) \mathbf{h}
$$

eşitliği yazılır. Burada $\mathbf{J}_G(\mathbf{x})$, $G$ fonksiyonunun kısmi türevlerini içerenen Jacobi matrisidir. $G(\mathbf{x}) = \nabla F(\mathbf{x})$ şeklinde tanımlandığından, 

$$
\mathbf{J}_G(\mathbf{x}) =
\begin{bmatrix}
\nabla^\intercal \nabla f_1(\mathbf{x})\\
\nabla^\intercal \nabla f_2(\mathbf{x})\\
\vdots\\
\nabla^\intercal \nabla f_m(\mathbf{x})\\
\end{bmatrix}= \mathbf{H}_F(\mathbf{x})
$$

$\mathbf{J}_G$ matrisi $F$ fonksiyonunun Hessian matrisi olarak elde edilecektir.

Bu durumda $G(\mathbf{x} + \mathbf{h}) = \mathbf{0}$ yapacak olan $\mathbf{h}$ değeri; $\mathbf{H}_F(\mathbf{x}) \mathbf{h} = -\nabla F(\mathbf{x})$ denklem setinin çözülmesi ile elde edilecektir.

**Referanslar**
* Heath, Michael T. Scientific Computing: An Introductory Survey, Revised Second Edition. Society for Industrial and Applied Mathematics, 2018.

