---
layout: post
title: Tesla Yapay Zeka Günü 2021
slug: tesla-aiday-2021
author: Bahri ABACI
categories:
- Makine Öğrenmesi
thumbnail: /assets/post_resources/tesla_aiday_2021/thumbnail.png 
---
Tesla, elektrikli araçlar ve temiz enerji alanlarında ürünler geliştiren, bir Amerikan şirketidir. 2003 yılında kurulan şirket, yüksek kapasiteli enerji saklama çözümleri, çatıya kurulabilen güneş panelleri ve elektrikli araçları ile birçok alanda başarılı bir şekilde faaliyetlerini sürdürmektedir. Şirket faaliyet alanlarında oldukça başarılı olsa da bu blogda yer alma nedeni; şirket tarafından geliştirilen ve ürünlerinde kullanılan, bu başarının sağlanmasında çok önemli bir paya sahip, yapay zeka teknolojileridir. Bu yazımızda şirketin geçtiğimiz haftalarda *Tesla Yapay Zeka Günü*' nde sunumunu yaptığı **otopilot** *(autopilot)* sisteminin yapısına değinilecek ve bilgisayarlı görü ile çözülen problemler incelenecektir.

<!--more-->

İlk olarak bu yazımızında odak noktası olacak olan *otopilot* sistemini tanıyalım. Otopilot; araç çevresinde bulunan sensörler yardımıyla, sürücü desteği olmadan, araçla seyahat edebilmemizi sağlayan bir sistemdir. Tesla tarafından *sürüşün geleceği* olarak tanımlanan bu teknolojide, fren yapma, gaza bazma ve direksiyon çevirme gibi ihtiyaçların tamamı otopilot tarafından kontrol edilmektedir. 

## Otopilot Sistemi
Otopilot sisteminin çalışabilmesi için aracın, kendi çevresini 360 derece algılama yeteneğine sahip olması gereklidir. Bu algılama, ultrasonik, radar ve kamera gibi sensörler ile yapılabilmektedir. 

Tesla tarafından yapılan sunumun ilk kısmında, kamera sensörlerinden gelen veriler ile üç boyutlu vektör uzayının nasıl oluşturulduğu anlatılmıştır. Tesla bu görev için 8 kamera modülü kullanmaktadır. Bu kameralardan 3 tanesi aracın önünü (dar, orta, geniş), 2 tanesi ön-yanları (sağ, sol), 2 tanesi arka-yanları (sağ, sol) ve 1 tanesi de arkayı görmek için kullanılmaktadır. Bu kameralar ile en az 50m, en fazla 250m ye kadar çevresel görüş sağlanmaktadır. Aşağıda 8 kameranın yerleşimi ve araç çevresindeki kapsama alanları oransal olarak verilmiştir. *İmgede araç görsel amaçla olduğundan büyük çizilmiştir.*

![Tesla Kamera Yerleşimi][tesla_cameras]

Tesla bu kameralardan gelen görüntüleri bir **omurga** olarak nitelendirdiği bir derin öğrenme ağından geçirerek öznitelik uzayına taşımaktadır. Bu ağın omurga olarak isimlendirilmesinin nedeni, sistemin tüm görüntü işleme görevleri (araç tanıma, derinlik çıkarma, trafik levhası tanıma, vb.) için aynı ağı kullanmasıdır. Tesla bu sayede işlem süreleri oldukça uzun olan derin öğrenme ağlarının her kamera için yalnızca bir kere çalışmasını sağlamakta ve gereksiz işlem yükünden kurtulmaktadır.

Aşağıda Tesla tarafından tanıtımı yapılan bilgisyarlı görü sisteminin detaylı çizimi verilmiştir. 

![Tesla Derin Öğrenme Ağı][tesla_backbone]

Verilen görselde, RegNet ve BiFPN bloklarından oluşan yapı birimin omurgasını oluşturmaktadır. RegNet; 2020 yılında düzenlenen CVPR konferansında, Facebook Yapay Zeka Araştırma grubu (FAIR) tarafından duyurulan bir ağ tasarım modelidir. Bu modelin en önemli avantajı basit ve hızlı ağ yapıları oluşturmasıdır. BiFPN ise RegNet ile aynı konferansta, Google Araştırma grubu tarafından yayınlanan *"EfficientDet: Scalable and Efficient Object Detection"* bildirisi ile duyrulmuştur. Bu ağların en önemli özelliği, ağın bir kez çalışması ile farklı boyutlardaki nesnelerin tespitine imkan sağlayan Çok Ölçekli Özniteliklerin (Multi-scale Features) hesaplanabilmesidir.

Şekilde verilen sistemin yapısı incelenirse; kameradan elde edilen ham veriler, omurga ağından geçerek çok ölçekli özniteliklere dönüştürülmektedir. Ardından bu öznitelikler farklı öğrenme sistemlerine girdi olarak kullanılmakta ve göreve özel ağların öğrenilmesi sağlanmaktadır. 

Tesla göreve özel ağların öğrenimi sırasında omurga ağının parametreleri dondurmakta ve her görev ağının kendi içerisinde parametre optimizasyonunu yapmaktadır. Bu sayede milyonlarca parametreden oluşan omurga ağının eğitimi ile vakit kaybedilmeden, göreve özel ağların parametreleri en iyi noktaya taşınabilmektedir. Göreve özel ağların parametre optimizasyonu tamamlandıktan sonra, omurga ağının eğitimi tekrar yapılmaktadır. 

## Üç Boyutlu Vektör Uzayı Kestirimi

Otopilot sisteminin temel ihtiyacı aracın gerçek dünyada çevresinde bulunan nesnelerin doğru koordinatlarla tespit edilmesidir. Ardından sürüş birimi bu nesneler arasından nasıl bir yol izlenerek hedefe gidileceğine karar verecek ve uygun kontrolleri sağlayacaktır. 

Üç boyutlu uzay gösterimi, kamera görüntülerindeki yüksek seviyeli bilgilerin (nesneler, trafik ışıkları, şerit tahminleri), kameranın iç (intrinsic) ve dış (extrinsic) dönüşüm matrisleri kullanılarak üç boyutlu uzaya dönüştürülmesi ile elde edilebilir. Bu işlem her kamera görüntüsü için yapıldığında aracın tüm çevresinin üç boyutlu vektör uzayı *elde edilmiş olur!*

Tesla yaptığı sunumda bu yaklaşımın üç boyutlu harita oluşturulmasında **büyük hatalara** neden olduğunu göstermiştir. Bu hatanın en temel kaynağı; imge uzayındaki tahminlerin üç boyutlu uzaya dönüştürülürken tüm piksellere ait hassas derinlik bilgisine ihtiyaç duyulmasıdır. Bu haritanın oluşturulmasındaki küçük hatalar üç boyutlu uzayın oluşturulmasında büyük hatalara neden olmaktadır.

Buna ek olarak her kamera sadece belirli bir açıyı görebildiğinden, çevredeki nesneler her zaman bir bütün olarak değil, zaman zaman kısmi olarak görülecektir. Bu durumda nesneler imge üzerinde ya bulunamayacak yada küçük sapmalar ile bulunacaktır. Bu hatalar da nesnelerin üç boyutlu haritadaki konumlarını yanlış hesaplamaya neden olacaktır.

Bu nedenle, Tesla imge üzerindeki nesne konumlarını üç boyutlu uzaya taşımak yerine, doğrudan üç boyutlu vektör uzayını öğrenmeye çalışmıştır. Bu işlem için tasarlanan ağın yapısı aşağıda görselleştirilmiştir.

![Tesla Attention Network][tesla_multicam]

Bu işlem için ilk olarak tüm kameraların çıktıları referans bir kameraya göre normalize edilmiştir. Bu sayede tüm kamera görüntülerinin benzer bir yapıda olması sağlanmıştır. Ardından tüm normalize kamera görüntüleri omurga ağına girdi olarak verilmiş ve çıktı öznitelikleri elde edilmiştir. Elde edilen bu öznitelikler Dikkat Ağları (Attention Network) ile dönüştürülerek doğrudan üç boyutlu vektör gösterimi elde edilmiştir. 

## Zamansal Bilginin Kazanılması
İmgeye dayalı vektör uzayı oluşturmada pek çok sorun tasarlanan bu ağ yapısı ile çözülse de, otopilot sisteminde hala önemli bazı sorunlar bulunmaktadır. Bu sorunlardan en önemlisi zamana bağlı bilginin tutulmamasıdır. 

Bu bilgi kaybı nedeniyle bir aracın hareket halinde mi yoksa durağan mı olduğu gibi en temel bilgi bile anlaşılamamaktadır. Buna ek olarak, araç çevresinde bulunan bir nesne kısmi süreli görüşü engellendiğinde bu nesnenin hala orada olduğunun da hatırlanması gereklidir.

Tesla bu yeteneklerin kazanılması için tüm görüntülerin zamanda saklanması yerine, omurga ağının çıktılarının saklanmasını sağlamıştır. Bu ağların çıktıları üç boyutlu uzayı oluşturacak güçte olduğundan herhangi bir bilgi kaybı olmadan geçmiş bilgilerin hatırlanabilmesi sağlanmıştır.

Omurga ağlarının çıktıları aracın farklı konumları (örneğin; her 1m yer değişiminde) için ve yer değişimi olmasa dahi belirli periyotlarla (örneğin; her 100ms) saklanmıştır. Saklanan bu veriler video modülü adı verilen bir modül yardımıyla birleştirilerek uzam-zaman bilgisi içeren bir öznitelik vektörü elde edilmiştir.

## Kurulan Ağın Eğitilmesi

Yazıda bu noktaya kadar verilen bilgiler, Tesla'nın otopilot sistemi için kurduğu derin öğrenme sistemini genel yapısıyla göstermektedir. Bu sistemin kurulması kadar bir diğer önemli konuysa, oluşturulan bu sistemin eğitilebilmesidir. Tasarlanan ağ yapısı milyonlarca parametreden oluştuğundan, ağın etkin bir şekilde kullanılabilmesi için çok sayıda etikelnemiş veriye ihtiyaç duyulmaktadır.

Tesla, eğitim için ihtiyaç duyduğu verileri halihazırda yollarda bulunan 1 milyon aracından toplamaktadır. Bu sayede dünyadaki en çeşitli ve en karmaşık verileri toplama gücüne sahiptir. Ancak bu veriler doğru ve sistematik bir şekilde etiketlenmediği durumda tasarlanan ağın gerçek gücüne hiçbir zaman ulaşılamayacaktır.

Tesla verilerin doğru bir şekilde etiketlendiğinden emin olmak için, etiketleme işini şirket içine aldığını ve bu kapsamda 1000'den fazla kişiden oluşan bir çalışma ekibi bulunduğunu açıklamıştır. İşaretleme ekibi şirketin çalışanları olduğundan, mühendislik ekibi ile daha yakın çalışabilmekte ve hata görülen etiketlemeler hızlı bir şekilde düzeltilebilmektedir.

İşaretlemelerin hızlı ve doğru bir şekilde yapılabilmesi için, etiketlemelerin imge uzayı yerine doğrudan üç boyutlu vektör uzayında yapılabilmesini sağlayan bir işaretleme yöntemi geliştirilmiştir. Üç boyutlu uzayda yapılan işaretlemeler, 8 kameradan gelen imge üzerine de yansıtılarak imgelerin hızlı bir şekilde etiketlenmesi sağlanmıştır.

Ancak bu hızlanma dahi bu seviyedeki bir ağın eğitilmesi için yeterli veriyi üretmekte yavaş kalabilmektedir. Bu nedenle Tesla, istenilen verilerin hızlı bir şekilde üretilebilmesi için bir simülasyon motoru kullanmıştır. Simülasyon dünyasında tüm nesnelerin konumu, oryantasyonu ve görünümü modellenebildiğinden, etiketlemeye ihtiyaç kalmadan istenilen veriler üretilmektedir. Bu tip bir veri toplamada temel sorun, sahnelerin gerçekçi bir şekilde oluşturulabilmesidir. 

Tesla gerçeğe yakın bir görüntü oluşturabilmek için; sensör gürültüsü, güneş ve gökyüzünün yaydığı enerji,  ışık yansımaları, hareket bulanıklığı gibi onlarca parametreyi ayarlayarak, oluşturulan sahnelerin kameralarda algılandığına benzer görünmesini sağlamıştır.

---

Bu yazımızda Tesla tarafından düzenlenen [Tesla Yapay Zeka Günü](https://www.tesla.com/AI)'nde anlatımı yapılan otopilot teknolojisinin bilgisayarlı görü ile ilgili kısımları özetlenmiştir. Etkinliğin tamamını [Tesla YouTube](https://www.youtube.com/watch?v=j0z4FweCy4M) sayfası üzerinden izleyebilirsiniz.

**Referanslar**
* Radosavovic, Ilija, et al. "Designing network design spaces." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2020.

* Tan, Mingxing, Ruoming Pang, and Quoc V. Le. "Efficientdet: Scalable and efficient object detection." Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 2020.

* Vaswani, Ashish, et al. "Attention is all you need." Advances in neural information processing systems. 2017.

[RESOURCES]: # (List of the resources used by the blog post)
[tesla_cameras]: /assets/post_resources/tesla_aiday_2021/tesla_cameras.svg
[tesla_backbone]: /assets/post_resources/tesla_aiday_2021/tesla_backbone.svg
[tesla_multicam]: /assets/post_resources/tesla_aiday_2021/tesla_multicam.svg