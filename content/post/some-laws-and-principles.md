+++
categories = ["Genel","TR"]
date = "2017-01-07T13:42:18+03:00"
title = "Bir takım teoriler, kanunlar ve ilkeler"
keywords = ["Conway's Law",
            "Conway'in Kanunu",
            "Broken Windows Theory",
            "Kırık Camlar Teorisi",
            "Peter İlkesi",
            "Peter Principle",
            "Pareto İlkesi",
            "Pareto Principle",
            "Parkinson Kanunu",
            "Parkinson's Law"]
weight = 1
+++

Bir şekilde öğrendiğim bir takım teoriler, kanunlar, ilkeler var. Bunları çevremdeki insanlarla paylaştığımda ilgi çektiklerini ve çok bilinmediklerini farkettim. Ben de bir blog yazısı ile bunları kısa kısa ve basit cümleler ile paylaşayım dedim. Bu yazıda 5 tanesine değineceğim. Başlıklara wikipedia linklerini ekledim. Formal şekillerini okumak isteyenler oradan devam edebilir.


<!--more-->

### Conway'in Kanunu ( [Conway's Law](https://en.wikipedia.org/wiki/Conway's_law))

**"Organizasyonların kendi iletişim yapılarını yansıtan tasarımlar yapması kaçınılmazdır."**

Başka bir ifade ile

**"Bir üründeki problemler onu yaratan organizasyonun problemlerini yansıtır."**

Örneğin;

* Bir yazılım takımında iletişim problemleri ve uyumsuzluklar varsa çıkan ürünün parçaları arasında da uyumsuzluk gözlenir. Hatta şöyle düşünebiliriz. Birbirinden kopuk 3 takımın geliştirdiği yazılımda birbiriyle uyumsuz 3 parça gözlemlemek mümkündür.
* Hiyerarşik yapısı güçlü ve çok geniş takımlardan oluşan organizasyonlarda geliştirilen yazılımların monolithic'e kayması kaçınılmaz olur. Mikroservis geliştirmek için takımları küçültmek ve onlara belli başlı bağımsızlıklar vermek gerekir.

Konu ile ilişkili makaleler:

* [Demystifying Conway's Law by Sam Newman](https://www.thoughtworks.com/insights/blog/demystifying-conways-law)
* [Agile: Türk Kaşığıyla Amerikan Çikolatası](http://www.safkan.org/agile-turk-kasigiyla-amerikan-cikolatasi/)
* [How to Design With Conway’s Law in Mind](http://itrevolution.com/conways-law/)


### Kırık Camlar Teorisi ([Broken Windows Theory](https://en.wikipedia.org/wiki/Broken_windows_theory))

Kırık Camlar Teorisi kısaca şunu söyler: 

"Bir ev düşünün. Yolun kenarında. Camları sapasağlam. Yoldan geçen hiç kimse onun camlarını kırmayı düşünmez, düşünen varsa da kırmaktan çekinir. Fakat camların çoğu kırık ise yoldan geçen herhangi biri kalan camlardan birini indirmek konusunda çok tereddüt etmez. Çünkü bunun suç olduğunu ya da zarar verici bir eylem olduğunu düşünmez. Eğer birisi gelip sağlam evin bir camını kırarsa hızla müdahale etmelisin. Yoksa diğerleri de hızla kırılır."

Teorinin çıkışı ABD'deki bazı suçların işlenmesi üzerine yapılan araştırmalar olmakla beraber günümüzde yazılım alanında da sıklıkla kullanılıyor. Şöyle ki;

 Bir yazılım geliştiricisi hata yapıp standartların dışında kod ekleme teşebbüsünde bulunabilir. Eğer buna müsamaha edersek diğer geliştiriciler de benzer davranışı sergiler ve kalite hızla düşer. Bu nedenle gözden geçirme süreçleri ve çeşitli araçlarla sürekli kalite takibi yapılması, hatalara çabuk müdahale edilmesi zaruridir. 

Konu ile ilgili makaleler:

* [Software Entropy](https://pragprog.com/the-pragmatic-programmer/extracts/software-entropy)
* [The Broken Windows Principle](https://alexandrebrisebois.wordpress.com/2013/06/08/the-broken-windows-principle/)
* [Always Fix Broken Windows](http://blog.smartbear.com/lean-software-development/always-fix-broken-windows/) 

Ayrıca yazar abimizden bir dörtlük:

>I keep a close watch on these tests of mine<br>I keep my Jenkins open all the time<br>I see a defect coming down the line<br>Because you’re mine, I stop the line

### Peter İlkesi ([Peter Principle](https://en.wikipedia.org/wiki/Peter_principle))

Peter ilkesi şöyle diyor:

1. Bir pozisyona seçim yapılırken adayların o pozisyonlar için yeteneklerine bakılmaz, daha ziyade adayların hali hazırdaki pozisyonlarındaki başarılarına bakılır. Örneğin, yönetici seçeceğimiz zaman en iyi kim yönetici olabilir diye bakmıyoruz da en iyi geliştirici kim diye bakıyoruz. En iyi geliştiricinin yönetici yetenekleri çok iyi olmasa da onu seçiyoruz.
* Bir çalışan başarılı oldukça yükselir ta ki başarısız olduğu bir posizyona gelene kadar. O pozisyonda başarısız olduğu için yükselmez ve hep o pozisyonu işgal eder.

Sonuç olarak zaman içinde yeteneklerine göre seçilmediği için başarısız olan, başarısız olduğu için o pozisyonu sürekli işgal eden "yetersiz üst yönetici" grubu oluşur. 


### Parkinson Kanunu ([Parkinson's Law](https://en.wikipedia.org/wiki/Parkinson%27s_law))
   
**"Bir iş, daima, bitirilmesi için kendisine ayrılan sürenin hepsini kapsıyacak şekilde uzar."**   

Yani diyor ki;
"Sen bu işi 4 günde bitiririm dedin ve bitirdin fakat belki de 3 günde bitirebilirdin. İşi uzatıp 4 günde bitirmiş olman muhtemel. 3 günlük efor verseydin belki de 3 güne biterdi. Hatta fazla verip 5 gün verseydin eminim 5. günde bitirirdin."

Bu kanun ile ilişkili bazı durumlar/çıkarımlar var. Mesela;

* Uygulamalar verilen bant genişliğinin tamamını kullanma eğilimindeler.
* Veri miktarı saklama kapasitesi miktarına göre büyüme eğiliminde.
* Donanım çok hızlanmış olmasına rağmen yazılımların hızları çok hızlanmıyor. Çünkü yazılımcılar ona göre karmaşıklık ekliyor. (bkz: 10 saatte yüklenen lunapark gibi web siteleri)

Bu kanunun ışığında şöyle bir soru çıkıyor: Ne kadar ihtiyacımız var? Yapacağımız işler için zaman tahmini yaparken [şunu](https://www.youtube.com/watch?v=ptSZkp0y0p4) kullabiliriz diye düşünüyorum. 

**Not:** Wikipedia'da da görüleceği üzere bu kanun daha sonraları bürokrasinin kendine çalışmasını ve işlerden bağımsız şekilde büyümesini ifade etmek için kullanılmış.

### Pareto İlkesi ([Pareto Principle](https://en.wikipedia.org/wiki/Pareto_principle))

80/20 kuralı  olarak da bilinen bu ilke bazı tecrübeler üzerine öne sürülmüş. Çok formal tanımlara girmektense örnekler ile açıklamayı tercih ediyorum.

* Müşterilerin ihtiyaçlarının yüzde seksenini bütün ihtiyaçlar için gerekli toplam eforun yüzde yirmisini harcayarak giderebiliriz. Başka bir ifadeyle düzgün bir önceliklendirme ile yüzde yirmilik bir efor yüzde seksenlik bir geri dönüş getirir.
* Hata üreten sebeplerin yüzde yirmisi hataların yüzde sekseninin sebebidir. Yani doğru önceliklendirme ve yüzde yirmilik bir çalışma bug sayımızı yüzde seksen azaltır. 
* Satışların yüzde sekseni müşterilerin yüzde yirmisinden gelir.
* Uygulamaya gelen trafiğin yüzde sekseni yüzde yirmilik zaman diliminde gerçekleşir.

Nüfus, ekonomi, matematik gibi alanlarda da bu ilkeye uyduğu düşünülen çeşitli örnekler Wikipedia sayfasında mevcut. 



