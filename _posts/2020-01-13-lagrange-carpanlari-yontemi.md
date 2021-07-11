---
layout: post
title: Lagrange Çarpanları Yöntemi
slug: lagrange-multipliers
author: Bahri ABACI
categories:
- Lineer Cebir
- Nümerik Yöntemler
- Makine Öğrenmesi
references: ""
thumbnail: /assets/post_resources/lagrange_multipliers/thumbnail.png
---
Lagrange Çarpanları (Lagrange Multipliers), Joseph Louis Lagrange tarafından 1770 li yılların başında literatüre kazandırılan ve verilen denklem takımlarının kısıt (constraint) altında çözümünü bulmak için kullanılan önemli bir matematiksel yaklaşımdır. Özellikle Makine Öğrenmesi ve Örüntü Tanıma alanlarında önerilen Doğrusal Regresyon, [Lojistik Regresyon Analizi]({% post_url 2015-07-23-lojistik-regresyon-analizi %}), Destek Vektör Makineleri gibi pek çok algoritmanın matematiksel denklemlerinin çözümü için kullanılmaktadır. Bu yazımızda basit örneklerden başlayarak Kısıtlı Optimizasyon (Constraint Optimization) kavramı üzerinde durulacak ve Lagrange Çarpanları ile bu tip eşitliklerinin nasıl çözüleceği örnekler ile anlatılacaktır.

<!--more-->

### Kısıtlı Optimizasyon

Kısıtlı optimizasyon, hemen hemen tüm mühendislik çalışmalarında karşımıza çıkan bir en iyileme çalışmasıdır. Örnek olarak bir aracın bir litre yakı ile ne kadar yol alabileceğini matematiksel olarak ifade etmeye çalışalım. Bir aracın ne kadar yol alabileceği; aracın ağırlığı ($a$), motor verimliliği ($v$) ve yüzlerce başka değişkene bağlıdır. Basit olması açısından mesafenin sadece $(a,v)$ değişkenlerine bağlı olduğunu düşünelim. Bu durumda aracın bir litre yakıt ile katedebileceği mesafe $D$ kilometre aşağıdaki şekilde hesaplanabildiğini düşünelim.

$$D(a,v)=30v - a^2  \tag{1}$$

Denklem $\eqref{1}$ ile verilen eşitlik kullanılarak $a=1.2$ ton bir aracın $v=0.6$ verimlilik ile $D=16.56$ kilometre yol alabileceğini hesaplayabiliriz. Aracın ağırlığı $a=1.8$ ton olması durumunda da mesafe $D=14.76$ kilometreye düşecektir. 

Şimdi Denklem $\eqref{1}$' in verildiği bir çalışmada aracın alabileceği en uzun mesafe soruluyor olsun. Denkleme bakıldığında aracın ağırlığının $a=0$ seçilmesi durumunda mesafenin $D(v)=30v$ olduğu görülür. Bu durumda verimlilikte sonsuza çıkarılırsa $D=\infty$ kilometre bulunur. Yani aracımızı sıfır ağırlıklı ve sonsuz verimlilikte yaparsak, bir litre benzin ile aracımızın $D=\infty$ kilometre mesafe yol alabileceğini hesaplarız. 

Bu sonuç verilen matematiksel olarak doğru olsa da, mühendislik anlamında gereksiz ve saçma bir çözümdür. Çünkü enerjinin korunumu yasası gereği verimlilik $v \leq 1$ olmak zorundadır. Benzer şekilde aracı oluşturan bileşenler düşünüldüğünde ağırlığının $a \ge 0$ olması gerektiği de barizdir. Matematiksel olarak Denklem $\eqref{1}$' in anlamlı bir şekilde çözülebilmesi için $v \leq 1$ ve $a \gt 0$ koşullarının denkleme kısıt (constraint) olarak eklenmesi gerekmektedir. Bu durumda Denklem $\eqref{1}$ ifadesinin en büyükleme probleminin aşağıdaki şekilde yazılması gerekmektedir.

$$
\begin{array}{ll}
\text{maximize}  & D(a,v)=30v - a^2 \\
\text{subject to}& v \leq 1 \\
&a \gt 0
\end{array}
\tag{2}
$$

Bu yazımla optimizasyon problemi, kısıtlı optimizasyon problemi olarak doğru bir şekilde ifade edilmiş olur. Bu kısıtlar altında problemin çözülmesi durumunda $v=1$ ve $a = \epsilon$ seçilmesi durumunda alınacak en uzun mesafe $D \simeq 30$ kilometre bulunur. Burada $\epsilon$ sonsuz küçüklükte bir sayıyı göstermektedir.

### Lagrange Çarpanları Yöntemi

Lagrange Çarpanları, Denklem $\eqref{2}$ şeklinde verilen kısıtlı optimizasyon problemlerinin yardımcı değişkenler kullanılarak kısıtsız optimizasyon problemine dönüştürülerek çözülmesini sağlayan değişkenlerdir. Yöntemin nasıl çalıştığını anlamak için tek kısıtlı basit bir problemi ele alalım. 

Elimizde bulunan $L=100$ metre uzunluğundaki bir tel örgü ile çevirebileceğimiz dikdörtgen bir bahçenin en büyük alanını bulmaya çalışalım. Bu problem kısıtlı optimizasyon problemi olarak aşağıdaki şekilde ifade edilir.

$$
\begin{array}{ll}
\text{maximize}  & A(x,y)=xy \\
\text{subject to}& 2x+2y=100 
\end{array}
\tag{3}
$$

Yukarıda verilen denklemde $A$ bahçenin alanını, $x$ ve $y$ dikdörtgenin kenar uzunluklarını göstermektedir. $2x+2y=100$ kısıtı ise oluşturulacak dikdörtgenin çevresinin uzunluğunu göstermektedir. Lagrange çarpanları kullanılarak Denklem $\eqref{3}$ ile verilen ifade aşağıdaki şekilde yazılır.

$$\text{maximize} \quad L(x,y,\lambda) = xy - \lambda (2x+2y-100) \tag{4}$$

Burada $\lambda$ Lagrange çarpanı olarak bilinir ve Denklem $\eqref{3}$' de verilen kısıtlı optimizasyon probleminin, Denklem $\eqref{4}$ ile verilen kısıtsız optimizasyon yöntemine dönüştürülmesinde kullanılır. Bu problemin çözülmesi için $L$ fonksiyonunun $x$,$y$ ve $\lambda$ değişkenlerine göre türevi alınıp sıfıra eşitlenirse;

$$
\begin{array}{lll}
\frac{\partial L(x,y,\lambda)}{\partial x} & = y - 2\lambda & = 0\\ 
\frac{\partial L(x,y,\lambda)}{\partial y} & = x - 2\lambda & = 0 \\
\frac{\partial L(x,y,\lambda)}{\partial \lambda} & = 2x+2y-100 & = 0
\end{array}
\tag{5}
$$

bulunur. Burada ilk denklemin çözümünden $y = 2\lambda$, ikinci denklemin çözümünden $x=2\lambda$ bulunur. Bu denklemler son denklemde yerine yazılırsa; $2(2\lambda)+2(2\lambda)-100$ elde edilir. Buradan da $\lambda=12.5$ sonucuna varılır. Bu sonuç ile de bahçenin kenarları $x=25$ metre ve $y=25$ metre olarak hesaplanır. Bulunan sonuçların Denklem $\eqref{3}$' de yerine yazılması ile de $A=625$ metrekare elde edilir.

Bu örnek üzerinden Lagrange teoremini genelleştirmek istersek, $f(x,y)$ şeklinde verilen fonksiyonun $g(x,y)=c$ kısıtı altında en küçük veya en büyük değeri bulunmak istenirse aşağıdaki Lagrange denkleminin en küçük veya en büyük noktalarının bulunması gerekmektedir.

$$L(x,y,\lambda) = f(x,y) - \lambda (g(x,y)-c) \tag{6}$$

Bu genel ifadenin çözümü için de $L$ fonksiyonunun türevi alınıp sıfıra eşitlenirse, $\nabla L=0$, aşağıdaki denklem takımları elde edilir.

$$
\begin{array}{ll}
\frac{\partial f}{\partial x} &= \lambda \frac{\partial g}{\partial x}\\ 
\frac{\partial f}{\partial y} &= \lambda \frac{\partial g}{\partial y}\\ 
g(x,y) & = c
\end{array}
\tag{7}
$$

Elde edilen üç denklem takımı, birbirinden bağımsız ise, bilinmeyen üç ($x,y,\lambda)$ değişkenin çözülmesi için yeterlidir. Burada belirtilen bağımsızlık ifadesi Lagrange yöntemlerinin kullanılıp kullanılamayacağını belirleyen en önemli koşuldur. 

**<span style="color: yellow;">Not:</span> $\lambda$ değişkeninin önünde bulunan işaretin $+$ veya $-$ olması sadece $\lambda$ değişkeninin tanımını değiştireceğinden sonucu etkilemeyecektir. Yazının devamında yazı içerisinde tutarlı olması adına aradaki işaret $-$ seçilmiştir.**

Şimdi başka örnek problemler ile yöntemin farklı kısıtlar ile nasıl kullanıldığına bir bakalım.

### Örnek Problem: Çembere Olan Uzaklık

Kartezyen koordinatlarda verilen $P(1,0.5)$ noktasının, $x^2+y^2=5$ parametrik denklemi ile verilen çembere olan en küçük uzaklığı nedir?

Öncelikle problemi kısıtlı optimizasyon tipinde yazmaya çalışalım. Soruda en küçük uzaklık sorulduğundan optimizasyon problemi şu şekilde gösterilebilir.

$$
\begin{array}{ll}
\text{maximize}  & D(x,y) = (x-1)^2+(y-0.5)^2 \\
\text{subject to}& x^2+y^2=5
\end{array}
\tag{8}
$$

Burada $D$, çember üzerinde seçilen bir $x,y$ noktasının, $P(1,0.5)$ noktasına olan Öklid uzaklığıdır (aslında karesi ama en küçük ve en büyük işlemini etkilemediğinden kolaylık olması adına bu şekilde yazılmıştır).

İfadenin çözümü için $\lambda$ Lagrange çarpanı kullanılarak problem tekrar yazılırsa;

$$L(x,y,\lambda) = x^2-2x+y^2-y+1.25 - \lambda \left( x^2+y^2-5 \right )$$

denklemi elde edilir. Gerekli türev işlemleri yapıldığı takdirde aşağıdaki denklem takımı elde edilir.

$$
\begin{array}{lll}
\frac{\partial L(x,y,\lambda)}{\partial x} & = 2x -2 - 2\lambda x & = 0\\ 
\frac{\partial L(x,y,\lambda)}{\partial y} & = 2y -1 - 2\lambda y & = 0\\ 
\frac{\partial L(x,y,\lambda)}{\partial \lambda} & = x^2+y^2-5 & = 0
\end{array}
\tag{9}
$$

Denklem $\eqref{9}$ da verilen ilk denklemden $x=\frac{1}{1-\lambda}$, ikinci denklemden ise $y=\frac{0.5}{1-\lambda}$ elde edilir. Bu bulgular üçüncü denklemde yerine yazılırsa;

$$\frac{1}{(1-\lambda)^2}+\frac{0.25}{(1-\lambda)^2} = 5$$ 

denklemi elde edilir. Bu denklemin çözümü ile de $\lambda_1=0.5$ ve $\lambda_2=1.5$ sonuçları bulunur. Bulunan $\lambda$ değerleri kullanılarak aranan noktalar $P_1(2,1)$ ve $P_2=(-2, -1)$ şeklinde hesaplanır. 

Bulunan noktaların $P(1,0.5)$ noktasına olan uzaklıkları; $D_1=\lVert{P-P_1}\lVert=1.11$ birim, $D_2=\lVert{P-P_2}\lVert=3.35$ birim hesaplanır. 

Burada dikkat edilirse en küçük uzaklığı aramamıza rağmen $3.35$ birim gibi bir uzaklık da çözüm sonucunda elde edilmiştir. Bu uzaklık her ne kadar sihirli bir sayı gibi görünse de aslında $P$ noktasının çembere olan en uzak mesafesine eşittir. Bu, $\nabla L=0$ çözümü ile extremum (en büyük veya en küçük) noktaların bulunduğunu göz önüne aldığımızda makul bir sonuçtur. Bu nedenle, çözülen problemde bulunan ilgisiz sonuçlar göz ardı edilerek doğru çözümün seçilmesi gerekmektedir. Bulunan örnekte $D_2 \gt D_1$ olduğundan en küçük mesafe $D= 1.11$ birim bulunur.

Aşağıda matematiksel olarak ifade edilen problemin grafik üzerinde gösterimi de verilmiştir.

![Lagrange Çarpanları ile Örnek Problem#half][lagrange_circle]

Grafikten de görüldüğü üzere bulunan $P_1$ ve $P_2$ noktaları, hem $x^2+y^2=5$ çember denklemini sağlamakta, hem de verilen $P$ noktasına sırasıyla en yakın ve en uzak noktaları göstermektedir.

### Örnek Problem: 

$$\text{maximize} \quad xAx^\intercal \quad \text{s.t} \quad \left \lVert x \right \lVert ^2=1 \tag{10}$$

Denklem $\eqref{10}$ ile verilen optimizasyon problemi makine öğrenmesi alanında sıklıkla karşımıza çıkan bir problemdir. Denklemin çözümü için Lagrange çarpanı kullanılarak Denklem $\eqref{10}$ ile verilen fonksiyon aşağıdaki şekilde ifade edilir: 

$$L\left (x,\lambda \right ) = x^\intercal A x - \lambda \left ( x^\intercal x - 1 \right ) \tag{11}$$ 

Denklem $\eqref{11}$ ile verilen maliyet fonksiyonun çözümü için $L$ fonksiyonunun $\lambda$ değişkenine göre türevi alınıp sıfıra eşitlenirse $x^\intercal x=1$ bulunur.

$L$ fonksiyonunun $x$ değişkenine göre türevi alınıp sıfıra eşitlenirse de $$\frac{\partial{L\left (x,\lambda \right ) }}{\partial{x}}=2Ax-2\lambda x = 0$$ elde edilir. Bu denklem düzenlenerek

$$Ax = \lambda x \tag{12}$$

sonucuna ulaşılır. Bu eşitlik [Özdeğer ve Özvektörler]({% post_url 2019-03-26-ozdeger-ve-ozvektorler %}) yazımızı hatırlayanlar için tanıdık bir ifadedir. Denklem $\eqref{12}$ şeklinde verilen bir problemin çözümünü sağlayan $x$ vektörleri $A$ matrisinin özvektörleri, $\lambda$ değerleri ise $A$ matrisinin özdeğerleri olarak bilinir.


### Örnek Problem: Doğrusal En Küçük Kareler Yöntemi

Doğrusal en küçük kareler yöntemi verilen bir $y$ verisini, $\hat{y}=xA$ ifadesi ile modelleyerek $(y-\hat{y})^2$ karesel hatasını en küçükleyen $A$ matrisini bulmaya çalışmaktadır. Hata değerimizi $J$ ile gösterirsek bu hata;

$$
\begin{array}{ll}
J(A)=(y-\hat{y})^2 &=\left ( y - xA\right )^\intercal\left ( y - xA\right )\\
&= y^\intercal y - y^\intercal x A - A^\intercal x^\intercal y + A^\intercal x^\intercal x A 
\end{array}
$$

olacaktır. Herhangi bir kısıt olmadan denklemi çözmeye çalışırsak;

$$
\begin{array}{ll}
\frac{\partial J(A)}{\partial A} &= 0\\
&= 0 - x^\intercal y -  x^\intercal y + 2 x^\intercal x A\\
&= 2 \left( x^\intercal y - x^\intercal x A \right )
\end{array}
$$

eşitliği elde edilir. Bu eşitliğin çözümü için her iki taraf $x^\intercal x$ değerine bölünürse $A$ matrisi aşağıdaki şekilde bulunur.

$$A= \left( x^\intercal x \right )^{-1} x^\intercal y \tag{13}$$

Denklem \eqref{13} ile verilen eşitlik doğrusal en küçük kareler kestirimi olarak bilinir. Ancak pek çok uygulamada bulunan sonucun belirli şartları da sağlaması beklenmektedir. Örnek olarak burada bulunan $A$ matrisinin normunun verilen bir $C$ sabitinden küçük olması gerektiği koşulunu eklemeye çalışalım.

$$\text{minimize} \quad J(A) \quad \text{s.t} \quad A^\intercal A \lt C \tag{14}$$

Bu durumda Denklem \eqref{14} ile verilen problemin, kısıtlı optimizasyon denklemi Lagrange çarpanı kullanılarak aşağıdaki şekilde yazılır.

$$
\begin{array}{ll}
J(A)= (y-\hat{y})^2 - \lambda \left( A^\intercal A - C \right)\\
\end{array}
$$

Yukarıdaki denkleme bir önceki adımda yapılan gerekli türev işlemleri uygulandığı takdirde aşağıdaki denklem takımı elde edilir.

$$
\begin{array}{ll}
\frac{\partial J}{\partial A} &= 2 \left( x^\intercal y - x^\intercal x A \right ) - 2\lambda \left( A \right) = 0\\ 
y & = xA
\end{array}
\tag{15}
$$

eşitliklikleri elde edilir. Bu eşitliğin çözümü için ortak terim $A$ yalnız bırakılacak şekilde denklem düzenlenirse $A$ matrisi aşağıdaki şekilde bulunur.

$$A= \left( \lambda I + x^\intercal x \right )^{-1} x^\intercal y \tag{16}$$

Denklem \eqref{16} ile verilen bu sonuç literatürde regülarize doğrusal en küçük kareler yöntemi (regularized linear least squares) veya çok daha yaygın adı ile Ridge Regresyonu (Ridge Regression) olarak bilinir. 

Denklem \eqref{14} ile sunulan kısıt ise regülarizasyon olarak bilinmektedir ve makine öğrenmesinde makinenin veriye aşırı uyum (overfit) sağlamasını engellemek için sıklıkla kullanılmaktadır. Bu kısıt eklendiğinde makinenin öğrendiği $A$ katsayılarının küçük olması zorlanmakta (gereksiz yere büyümesi engellenir) ve makinenin bulacağı çözümün veri setinde olmayan örnekler için de genelleştirilmesi sağlanmaktadır.

**Referanslar**
* [Joseph Louis Lagrange hakkında yazılan wikiwand sayfası](https://www.wikiwand.com/tr/Joseph-Louis_Lagrange)
* Bishop, Christopher M. Pattern recognition and machine learning. springer, 2006.
* Shalev-Shwartz, Shai, and Shai Ben-David. [Understanding machine learning: From theory to algorithms](https://www.cse.huji.ac.il/~shais/UnderstandingMachineLearning/understanding-machine-learning-theory-algorithms.pdf). Cambridge university press, 2014.


[RESOURCES]: # (List of the resources used by the blog post)
[lagrange_circle]: /assets/post_resources/lagrange_multipliers/lagrange_circle.svg
