---
layout: post
title: Pekiştirmeli Öğrenme (Reinforcement Learning)
author: Bahri ABACI
categories:
- Makine Öğrenmesi
- Nümerik Yöntemler
- Veri Analizi
thumbnail: /assets/post_resources/reinforcement_learning_q_learning/thumbnailr.png
---

Makine öğrenmesi algoritmaları genel olarak üç ana başlık altında toplanır. Bu başlıklardan ilki Eğiticisiz Öğrenme (Unsupervised Learning); bu blogta da değindiğimiz [K-Means]({% post_url 2015-08-28-k-means-kumeleme-algoritmasi %}), [Temel Bileşen Analizi]({% post_url 2019-09-01-temel-bilesen-analizi-principal %}) gibi yöntemleri kapsamaktadır. Bu yöntemlerde öğrenilen bilginin etiket bilgisi kullanılmadan verinin kendi içerisindeki benzerliği veya dağılımı kullanılarak veri sınıflara ayrılmaya çalışılır. İkinci ana başlık Eğiticili Öğrenme (Supervised Learning) ise [Karar Ağaçları]({% post_url 2015-11-01-karar-agaclari-decision-trees %}), [Lojistik Regresyon Analizi]({% post_url 2015-07-23-lojistik-regresyon-analizi %}) ve Destek Vektör Makineleri gibi yöntemleri kapsamaktadır. Bu yöntemlerin hepsi öğrenilecek verinin etiket bilgisine de ihtiyaç duyduklarından, bir eğitici/gözetici tarafından her verinin işaretlenmesi gerekmektedir. Bugün ele alacağımız Pekiştirmeli Öğrenme (Reinforcement Learning) ise üçüncü ana başlığı oluşturmaktadır.

<!--more-->
Girişte bahsettiğimiz ilk iki başlığın aksine Pekiştirmeli Öğrenme ne öğrenilecek veriye ne de verinin etiket bilgisine ihtiyaç duymakatdır. Eğiticisiz Öğrenme (Unsupervised Learning) ve Eğiticili Öğrenme (Supervised Learning) yöntemlerinden farklı olarak bu yöntem canlıların öğrenme yöntemine oldukça benzemektedir. Örnek olarak; yırtıcıların bulunduğu bir ortamda saklanmak hayatta kalmamızı veya yara almamamızı sağlarken, avlanmaya çalışmak ciddi kayıplara sebep olabilir. Ancak yırtıcının olmadığı bir ortamda saklanmak ve avlanmamak, elde edilebilecek bir besinin kaybedilmesine neden olacağından, kötü bir karar olacaktır. Hangi durumda (state) hangi kararın (action) doğru olduğu kimse tarafından etiketlenmese/öğretilmese dahi, yaşanılan ortamın ödülleri veya cezaları ile dolaylı olarak öğrenilebilir.

Pekiştirmeli Öğrenme yöntemi, öğrenme işlemi için sadece kurallar ve ödüllere ihtiyaç duymaktadır. Yöntem verilen kurallar çerçevesinde durum uzayını (state-space) keşfederek her durum için elde edeceği ödülü en büyüklemesini sağlayan hareketi (action) öğrenmeye çalışmaktadır.

![Pekiştirmeli Öğrenme][agent_environment]

Yukarıda verilen grafikte Pekiştirmeli Öğrenme yöntemini kullanan bir ajanın (Agent), çevre (Environment) ile ilişkisi gösterilmiştir. Çevre hakkında herhangi bir ilk bilgisi olmayan ajan, içinde bulunduğu durumu uygun olarak bir hareket seçimi yapar. Bu seçim çevre tarafından değerlendirilir ve ajan yeni bir duruma geçer. Ajan aldığı karar (yaptığı hareket) neticesinde çevre tarafından ödüllendirilir veya cezalandırılır. Ajan bir önceki durumda yaptığı hareketten aldığı ödül veya cezayı kendi karar alma mekanizmasında değerlendirir ve yeni geçtiği durum çin tekrar bir hareket/karar üretir. Ajan öğrenme işlemini tamamlayana kadar bu döngü devam eder.

Bu öğrenme işleminin matematiksel olarak nasıl yapıldığına geçmeden önce, kullanılacak matematiksel sembollerin anlamlarını gösteren aşağıdaki tabloyu inceleyelim.

| Sembol | Açıklama |
:-------------------------:|:-------------------------
| $s_t$ | $t$ anındaki durum (state) |
| $a_t \in A$ | $A$ hareketler uzayından alınan bir hareket (action) |
| $s^\prime_t=s_{t+1}$ | $t$ anında $a$ hareketi ile geçilen yeni durum (new state) |
| $r_t = r(s_t, a_t, s^{\prime}_t)$ | $s_t$ durumunda $a_t$ kararı ile $s^{\prime}_t$ durumuna geçilerek elde edilen ödül (reward) |
| $T(s, a, s^\prime) = P(s^\prime_t=s^\prime \lvert s_t=a, a_t=a)$ | $s_t$ durumunda $a_t$ kararı ile $s_{t+1}$ durumuna geçme olasılığı (transition matrix) |
| $\pi(s, a) = P(a_t=a \lvert s_t=s)$ | ajanın $s_t$ durumunda $a_t$ kararını verme olasılığı (policy) |

$s_t$ yukarıdaki görüntüde verilen durum değişkenini göstermektedir. $A$ ajanın seçebileceği hareketler kümesini, $a_t$ ise ajanın $s_t$ durumu için $A$ kümesinden seçtiği hareketi göstermektedir. Her hareketten sonra zaman bir birim ileri gider ve $s_t=s$ konumundan $s^\prime_t=s_{t+1}=s^\prime$ konumuna geçilir. 

$r_t=r(s, a, s^{\prime})$; $s_t=s$ durumunda $a_t=a$ kararı ile $s^{\prime}_t=s^\prime$ durumuna geçildiğinde elde edilen ödülü göstermektedir. 

$T(s, a, s^\prime)$; $s_t=s$ durumunda $a_t=a$ kararı verildiğinde, $s_{t+1}=s^\prime$ olma olasılığını göstermektedir. Tabloda gösterilen $P$ olasılık sembolüdür. $\pi(s, a)$; $s_t=s$ durumunda iken ajanın $a_t=a$ kararını verme olasılığıdır.

Yazının devamında kullanacağımız matematiksel ifadeleri anlamak için yukarıda grafiksel ve tablo şeklinde verdiğimiz ifadelerin iyice kavranması gerekmektedir. Bu nedenle bu aşamaya kadar öğrendiklerimizi aşağıdaki bir örnek problem üzerinde uygulayarak tabloda verilen açıklamaların ne olduğunu kavramaya çalışalım.

![Pekiştirmeli Öğrenme Örnek][markov_example]

Yukarıda verilen grafta $A$,$B$ ve $C$ olmak üzere üç farklı durum vardır. $s_0=A$ durumu başlangıç durumudur. Belirli bir hareket serisinden sonra ajan $s_t=C$ durumuna ulaştığında döngü bitmiş olur. Bu aşamada kolaylık olması açaısından ajanın yapmasına izin verilen hareket kümesi ($A=\{i\}$) ile sınırlandırılmıştır. Bu durumda ajan her zaman $a_t=i$ hareketini uygulayacak ve ileri gitmeyi tercih edecektir. Bu nedenle herhangi bir $s_t$ durumunda $a_t=i$ olma olasılığı $\pi([A;B],i) = P(a_t=i \lvert s_t=[A;B]) = 1$ dir.

Ancak ajanın aldığı her karar kesin olarak gerçekleşmeyebilir. Bu örnekte ajan $s_t=A$ durumunda $a_t=i$ kararı aldığında, $s^\prime_t=B$ olma olasılığı $T(A,i,B) = P(s^\prime_t=B\lvert s_t=A,a_t=i)=0.8$, $T(A,i,A)=P(s^\prime_t=A\lvert s_t=A,a_t=i)=0.2$ verilmiştir. 

Benzer şekilde $s_t=B$ durumunda $a_t=i$ kararı aldığında, $s^\prime_t=C$ olma olasılığı $T(B,i,C)=P(s^\prime_t=C\lvert s_t=B,a_t=i)=0.8$, $T(B,i,A)=P(s^\prime_t=A\lvert s_t=B,a_t=i)=0.1$ ve $T(B,i,B)=P(s^\prime_t=B\lvert s_t=B,a_t=i)=0.1$ verilmiştir.

Ajan $s_t$ durumunda bir $a_t$ hareketi yaptığında alacağı ödül ajanın gittiği $s^\prime_t$ ile öçülür. Eğer $s^\prime_t=A$ veya $s^\prime_t=B$ ise ajan $r([A;B],i,A)=R(S,i,B)=-1$ ödül alırken, $s^\prime_t=C$ ise ajan $r(B,i,C)=+1$ ödül alır. 

Bu kısa örnek üzerinde temel kavramları anladıktan sonra pekiştirmeli öğrenmenin nasıl yapılacağına geçebiliriz.

### Pekiştirmeli Öğrenme Algoritması

Pekiştirmeli öğrenmede amaç herhangi bir durumda hangi hareketi yapmamız gerektiğini öğrenmektir. Bunu öğrenmek için de önce herhangi bir durumda alınan $a$ kararının kalitesini hesaplamak gereklidir. Bir $s$ durumunda alınan $a$ kararının kalitesi o durumdan itibaren $\pi$ karar alma fonksiyonu olasılıkları çerçevesinde hareket edildiğinde alınması beklenen toplam ödül olarak tanımlanmıştır. Bu ifade matematiksel olarak şu şekilde gösterilir.

$$Q^\pi (s,a) = \mathbb{E}^\pi\left [ R_t \lvert s_t=s, a_t = a \right ] \label{quality} \tag{1}$$

Bu ifade $s_t=s$ durumunda, $a_t=a$ kararını verdiğimizde bu aşamadan sonra elde edilmesi beklenen geliri ifade etmektedir. Burada $R_t$, $t$ anından itibaren elde edilebilecek tüm ödüllerin toplamı olmalıdır.

$$R_t = \sum_{k=0}^{\infty} r_{t+k}$$

Ancak toplam sonsuz uzunlukta olduğundan $R_t$ toplamının sonsuza gitme olasılığı söz konusu olacaktır. Böyle bir durumda hangi durumda ne karar alınırsa alınsın ödüllerin toplamı $R_t = \infty$ olacağından hangi kararın daha doğru olduğu öğrenilemeyecektir. Bunun önünde geçmek için denklemin aşağıdaki şekilde değiştirilmesi gerekmektedir.

$$R_t = \sum_{k=0}^{\infty} \gamma^k r_{t+k} \label{return} \tag{2}$$

Burada $\gamma$ azaltma (discount) katsayısı olarak bilinir ve $0 \leq \gamma < 1$ seçilmesi durumunda $R_t$ ifadesini $\frac{R_{max}}{1-\gamma}$ ile üstten sınırlı yapmaktadır. $\gamma$ değerinin azaltma katsayısı olarak isimlendirilmesinin nedeni, $k$ küçük seçildiğinde durum geçişinden elde edilen ödüller tam olarak büyük oranda toplama eklenebilirken, durumlar ilerledikçe $k$ nin etkisi ile $\gamma^k$ sıfıra yaklaşmakta ve elde edilen ödüllerin toplama etkisini azaltmasıdır. $\gamma=0$ seçildiğinde her adımda sadece o adımda elde edilen ödül düşünülerek karar verilirken, $\gamma>0$ olması durumunda sadece o anki duruma değil, bu karar ile geçilecek durumunda elde edilecek ödüllere de bakılarak getiri hesaplanır.

Denklem \ref{return} ile verilen ifadeyi Denklem \ref{quality} de yerine koyarsak;

$$Q^\pi (s,a) = \mathbb{E}^\pi\left [  \sum_{k=0}^{\infty} \gamma^k r_{t+k} \lvert s_t=s, a_t = a \right ] \label{qualityExp} \tag{3}$$

elde edilir. Burada $k=0$ toplam formulünün dışına alınırsa;

$$Q^\pi (s,a) = \mathbb{E}^\pi\left [  r_{t} + \sum_{k=1}^{\infty} \gamma^k r_{t+k} \lvert s_t=s, a_t = a \right ]$$

bulunur. Çıkan ifade açık bir şekilde yazılırsa:

$$Q^\pi (s,a) = \mathbb{E}^\pi\left [r_{t} + \gamma^1 r_{t+1} + \gamma^2 r_{t+2} + \gamma^3 r_{t+3} + \dots\right ] $$

elde edilir. Bu denklem $\gamma$ parantezine alınır, $s^\prime_{t} = s_{t+1}$ ve $a^\prime_{t} = a_{t+1}$ değişken dönüşümü yapılırsa aşağıdaki biçime dönüşür.

$$Q^\pi (s, a) = \mathbb{E}^\pi\left [r_{t} + \gamma \sum_{k=0}^{\infty} \gamma^k r_{t+k+1} \lvert s_{t+1}=s^\prime, a_{t+1}=a^\prime) \right ] \label{qualityRec} \tag{4}$$

Denklemi çözmeye başlamadan önce biraz beklenen değer kavramına değinelim. Beklenen değer $\mathbb{E}[X] = \sum_i x_iP(X=x_i)$, bir olayın olma olasılığı ile o olay olduğunda elde edilecek değerin çarpımlarının, tüm olasılıklar üzerinden toplamıdır. Denklemden de görüldüğü üzere beklenen değer işleci doğrusal bir fonksiyondur. Doğrusal fonksiyonların dağılma özelliği kullanılarak Denklem \ref{qualityRec} ile verilen ifade; $\mathbb{E_1}^\pi = \mathbb{E}^\pi\left [r_{t} \right ]$, $\mathbb{E_2}^\pi = \mathbb{E}^\pi\left [r_{t+k+1} \lvert s_{t+1}=s^\prime, a_{t+1}=a^\prime) \right ]$ olmak üzere aşağıdaki biçimde yazılabilir.

$$Q^\pi (s, a) = \mathbb{E_1}^\pi + \sum_{k=0}^{\infty} \gamma^k \mathbb{E_2}^\pi$$

Şimdi bu sadeleştirmeyi kullanarak denklemi iki parçada çözmeye çalışalım.

$\mathbb{E_1}^\pi$ ifadesini hesaplamak için $s_t=s$ durumunda $a_t=a$ ile gidebileceğimiz tüm $s^\prime_t=s^\prime$ durumları için bir olasılığa ihtiyacımız vardır. Bu olasılık tabloda $T(s, a, s^\prime)$ ismi ile verilen olasılıktır. Bu olasılığı kullanarak olası tüm $s^\prime_t$' ler için beklenen ödül aşağıdaki şekilde bulunur.

$$\mathbb{E_1}^\pi = \sum_{s^\prime} P(s^\prime \lvert s, a) r(s, a, s^\prime)$$ 

$\mathbb{E_2}^\pi$ değerini bulmak istediğimizde ise bir noktaya dikkat edilmesi gerekir. İlk durumda $s_t=s$ durumunda olduğumuzu ve $a_t = a$ hareketini yaptığımız verilmişti. Ancak bir sonraki adımda hangi $s^\prime_t=s^\prime$ durumunda olduğumuzu ve hangi $a^\prime_t=a^\prime$ kararını aldığımız belirli değildir. Bu yüzden ikinci kısımda olası tüm $s^\prime$ durumlarında, olası tüm $a^\prime$ hareketlerini yaptığımızda elde edeceğimiz ödülü hesaplamamız gereklidir. Yukarıda da yazdığımız gibi herhangi bir $s$ durumunda $a$ hareketini yaptığımızda $s^\prime$ durumunda olma olasılığımız $P(s^\prime \lvert s, a)$ dir. Bu durumda $a^\prime$ kararını alma olasılığımızda tablo incelenerek $\pi(s^\prime,a^\prime)$ olduğu görülür. Bu iki olasılığın çarpımı $s,a,s^\prime,a^\prime$ durum-hareket serisinin oluşması olasılığını verir. Bu olasılık beklenen değer işlecinde yerine yazılırsa;

$$\mathbb{E_2}^\pi = \gamma \sum_{s^{\prime}} P(s^{\prime} \lvert s, a) \sum_{a^\prime}\pi(s^\prime,a^\prime) r(s^\prime, a^\prime, s^{\prime\prime})$$

eşitliği elde edilir. Bu bilgiler birleştirilerek $Q^\pi (s, a)$ ifadesi tekrar yazılırsa;

$$Q^\pi (s,a) = \sum_{s^\prime} P(s^\prime \lvert s, a) \left ( r(s, a, s^\prime) + \gamma \sum_{a^\prime}\pi(s^\prime,a^\prime) \sum_{k=0}^{\infty} \gamma^k r(s^\prime, a^\prime, s^{\prime\prime}) \right )$$

elde edilir.

Burada $\gamma^k$ ile çarpım durumunda bulunan toplam ile Denklem \ref{qualityExp} incelendiğinde bu ifadenin $Q^\pi (s^\prime, a^\prime)$ olduğu görülür. Yukarıdaki denkleme bu son değişken dönüşümü de uygulanırsa aşağıdaki önemli formül elde edilir.

$$Q^\pi (s,a) = \sum_{s^\prime} T(s, a, s^\prime) \left ( r(s, a, s^\prime) + \gamma \sum_{a^\prime}\pi(s^\prime,a^\prime) Q^\pi (s^\prime, a^\prime) \right ) \label{bellmanQuality} \tag{5}$$

Denklem \ref{bellmanQuality} ile verilen denklem Bellman Eşitliği olarak bilinir ve pekiştirmeli öğrenme yöntemlerinin temelini oluşturmaktadır. 

[Bir sonraki yazımızda]({% post_url 2020-01-05-q-ogrenme-q-learning %}) öğreneceğimiz Q öğrenme algoritması, özel bir karar verme fonksiyonu için, $Q^\pi (s,a) $ fonksiyonunu bulmayı amaçlamaktadır. Bu yazımızın devamında Q öğrenme algoritmasının çözmeye çalıştığı problem denklemlerle ifade edilecek, bir sonraki yazıda ise bu denklemlerin çözümü ve kodlması verilecektir.

Şimdi bir an için elimizde öğrenilmiş $Q^\pi (s,a)$ değerlerinin olduğunu varsayalım. Bu varsayım altında en iyi karar verme fonksiyonu $\pi^\ast$;

$$\pi^\ast (s) = \arg\max_{a} Q^\pi (s,a)$$

kuralına göre tercihler yapılarak elde edilecektir. Bu aç gözlü kural fonksiyonu her durumda, o durum için en kaliteli hareketi seçecektir. Böyle bir karar fonksiyonunda, $a_t=\arg\max_{a} Q^\pi (s,a)$ için $\pi^\ast(s,a) = 1$ iken, diğer tüm durumlar için  $\pi^\ast(s,a) = 0$ olacaktır. 

Bu durumda Denklem \ref{bellmanQuality} de yer alan olası tüm $a^\prime \in A$ toplama işleminin sadece $a_t=\arg\max_{a} Q^\pi (s,a)$ için yapılması yeterli olacaktır. Bu çıkarım sonucu Denklem \ref{bellmanQuality} de yerine yazılırsa;

$$Q^{\pi^\ast} (s,a) = \sum_{s^\prime} T(s, a, s^\prime) \left ( r(s, a, s^\prime) + \gamma \max_{a^\prime} Q^{\pi^\ast} (s^\prime, a^\prime) \right ) \label{bellmanOptimality} \tag{6}$$

literatürde *Bellman en iyi çözüm eşitliği* olarak bilinen denklem elde edilir. Bu denklemin çözümü ile elde edilen $Q^{\pi^\ast} (s,a)$, her durumda en büyük $Q$ değerli kararı seçen bir ajan için en ideal göstergeyi üretecektir.

Bu verilen denklem $T(s, a, s^\prime)$ olasılığını da hesaba katarak en iyi kararı verdiği için, ajan için stokastik anlamda en iyi göstergeyi üretecektir. Ancak bu durum pek çok hesaplamayı oldukça karmaşık hale getirdiğinde, deterministik sistemler için de bu eşitlik tanımlanmıştır. Deterministik bir sistemde $s_t = s$ durumunda alınan $a_t=a$ kararının bizi her zaman $s^\prime$ durumuna götürdüğü varsayılır. Bu durumda Denklem \ref{bellmanOptimality} aşağıdaki şekilde sadeleştirilebilir.

$$Q^{\pi^\ast} (s,a) = r(s, a, s^\prime) + \gamma \max_{a^\prime} Q^{\pi^\ast} (s^\prime, a^\prime) \label{bellmanOptimalityDeterministic} \tag{7}$$

Denklem \ref{bellmanOptimalityDeterministic} ile verilen denklemin çözümü literatürde Q Öğrenmesi olarak bilinen bir yöntem ile yapılmaktadır. Bu yöntemin detayları ve c kodlaması [bir sonraki yazımızda]({% post_url 2020-01-05-q-ogrenme-q-learning %}) paylaşılmıştır.


**Referanslar**
* Sutton, Richard S., and Andrew G. Barto. Reinforcement learning: An introduction. MIT press, 2018.
* Watkins, Christopher JCH, and Peter Dayan. "Q-learning." Machine learning 8.3-4 (1992): 279-292.
* Kumar, Vaibhav. [Reinforcement learning: Temporal-Difference, SARSA, Q-Learning & Expected SARSA in python](https://towardsdatascience.com/reinforcement-learning-temporal-difference-sarsa-q-learning-expected-sarsa-on-python-9fecfda7467e). towardsdatascience, 2019

[RESOURCES]: # (List of the resources used by the blog post)
[agent_environment]: /assets/post_resources/reinforcement_learning_q_learning/agent_environment.svg
[markov_example]: /assets/post_resources/reinforcement_learning_q_learning/markov_example.svg
