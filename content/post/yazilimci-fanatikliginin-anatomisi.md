+++
date = "2017-02-25T20:00:00+03:00"
keywords = ["Yazılım","Fanatik"]
title = "Yazılımcı Fanatikliğinin Anatomisi"
categories = ["Software","TR"]
draft=false
weight = 1
+++

***Feragatname (Disclaimer):*** İşbu blog yazısındaki her şey şahsi görüşlerim ve çıkarımlarımdan ibaret olup, herhangi bir kaynağa dayanmamaktadır. Yazıdaki ölçü vurgusuna dikkat edilmesi önemle rica olunur.

### Nereden çıktı bu yazı?
<a href="https://www.meetup.com/Software-Craftsmanship-Turkey/" target="_blank">Türkiye Yazılım Ustalığı </a> topluluğumuz için <a href="https://www.meetup.com/Software-Craftsmanship-Turkey/pages/20984357/Topluluk_Davran%C4%B1%C5%9F_Kurallar%C4%B1/" target="_blank">Topluluk Davranış Kuralları'nı </a> İngilizce örneğinden çevirirken bir ifadeye rastladım.

>Topluluğumuz cinsiyet, cinsel kimlik, cinsel tercih, yaş, engellilik hali, fiziksel görünüm, vücut ölçüleri, ırk ve din (veya dinsizlik), ***teknolojik tercihler*** ayrımı yapmaksızın herkese rahatsızlıktan uzak bir deneyim sunmaya adanmıştır.

Teknolojik tercihleri yüzünden ayrımcılığa maruz kalan var mıdır bilmiyorum ama bazı yazılımcıların ***"fanatik"*** olarak nitelendirilebileceğini biliyorum. Bu yazılım camiasında da bilinen bir durum zaten. Belirli bir ölçüye kadar güzel ve sevimli olan bu fan olma hali bir yerden sonra büyük sorunlara neden oluyor. Bu konu hakkında biraz kafa yordum ve düşüncelerimi bu yazıda ifade etmeye çalışacağım.

<!--more-->

### Neden?

Her mesleğin kendine göre zorlukları vardır. Yazılım geliştirme mesleğinin en zorlu yanlarından biri -aynı zamanda en güzel yanlarından biri olmakla beraber- sürekli bir değişimin/gelişimin olması. Öyle tarih öğretmenliği gibi öğrenilecekleri bir kez öğrenip 40 sene **yalnızca** onlarla işi götüremezsiniz. Her daim sürekli bir öğrenme sürecinde olmak zorundasınız. İşin kötü yanı 7/24 çalışsanız bile her şeyi öğrenmeniz imkansız. Her şeyi öğrenmeye çalışırsanız geriye dönüp baktığınızda hiçbir şeyi tam anlamıyla öğrenemediğinizi fark edersiniz. Bu nedenle yazılımcılar kariyerleri boyunca belirli alanları seçip, onlara odaklanırlar. Ömürlerini bu alanlarda gelişmek için harcarlar. Hikaye de tam burada başlar.

#### 1. Yatırımlarını koruma çabası

Bu bahsettiğimiz değişim/gelişim öylesine yakıcıdır ki hayatınızdan, sevdiklerinizden feragat ederek gece gündüz öğrendiğiniz, ustası olduğunuz bir dili piyasadan silebilir. "Oldum" diye düşündüğünüz an o çok bildiğiniz framework çöp muamelesi görebilir. Kullandıkları teknolojiler yükselişte diye düşünerek girdiğiniz işten çıkana kadar o teknolojiler (bkz: js frameworks) eskiyebilir. ***Yazılımcılar alanlarını seçerken aslında bir nevi yatırım yaparlar.***

Ne diyordu Primefaces ile JavaEE dünyasına damgasını vurmuş Çağatay Çivici Angular2 için çıkardığı PrimeNg'yi tanıtırken?

><a href="http://blog.primefaces.org/?p=4313" target="_blank">***Always bet on Prime!***</a>

Yani diyor ki; biz gelişeceğiz, güçleneceğiz. Piyasa bizi kullanacak, bizi bilen yazılımcı değerli olacak. Gidip de başka kütüphanelerle vakit kaybetme. Bize yatır ömrünü/emeğini ve kazan!

Peki bir dil/framework/araç/mimari/paradigma nasıl güçlenir, eskisinin yerini alır? Elbette bir gelişimi içinde barındırarak, eskisindeki bir takım sorunları çözerek. Peki her zaman birincil etkenin bu olduğunu söyleyebilir miyiz? Ortalıkta ne kadar övüldüğünün hiç etkisi yoktur diyebilir miyiz? Bilginin bu kadar çoğaldığı, odaklanmanın bu kadar zor olduğu bir dönemde derinleşme o kadar zor ki hemen hemen her alanda ciddi bir sığlık ile karşı karşıyayız. Ve bu sığlıktan ötürü bütün alanlarda algı hakikatin yerini almış durumda. Piyasada buna bir miktar teslim olmuş ki bu durumdan yakınan pek çok yazı (bkz: <a href="https://medium.com/tag/hype-driven-development" target="_blank">Hype Driven Development</a>) yayınlandı. 

***Yazılımcılar da bu nedenlerden dolayı yatırım yaptıkları alanlar lehine algı yönetimi yapma eğilimindedirler.*** Herkesin kendi bildiğini fütursuzca övdüğü bir ortamda nasıl sessiz kalınabilir? 10 yıl kazandığınız parayı bir şirketin hisselerine yatırsanır ve geri çekme imkanınız olmasa, şirketin kötü gittiğine dair yorumlara inanır/katılır mısınız yoksa şirketin ne kadar parlak bir geleceğe sahip olduğunu mu anlatırsınız insanlara? İnsanın kendini inandırması bile zordur böyle durumlarda.

#### 2. Duygusal Bağlar 

Yaşanmışlıklar aziz okuyucu yaşanmışlıklar! İnsanı insana, insanı hayvana, insanı nesnelere bağlar. Kusurları görünmez kılar. Leyla'nın kusurları görünür mü hiç Mecnun'un gözüne?

***Yazılımcılar da hayatlarını harcadıkları şeylerin eksikliklerini/kusurlarını görme konusunda sıkıntı yaşarlar, nesnelliklerini yitirirler.*** Kadın (bkz: she instead of he) yıllarca vim kullanmış, bütün kısa yolları tek tek öğrenmiş, kendine özel eklentileri/temaları bulmuş, editörü adeta yuvası yapmış. Visual Studio Code'da yatıya kalmış misafir tedirginliğinde yerini yadırgamaz mı?



#### 3. Özentilik

Öğrenmenin birinci adımı taklit etmektir. Evvela sizden öncekilerin yaptığı gibi yapmaya çalışırsınız. Zamanla su akar, yatağını bulur ve kendinize özgü bir üslup edinirsiniz. Usta-çırak ilişkisi içinde olan yazarlara bakın mesela, çırağın satırlarında ustaya rastlamak olağandır.

***Yazılımcılar da taklit ederek başlarlar. İyi olduğunu düşündüğü büyüklerinin kullandığı araçları, yöntemleri benimserler. Onların aralarındaki tartışmalarda taraf olmak isterler.*** Şöyle düşünür örneğin "Çok iyi yazılımcılar arasında 'Outside In TDD vs Inside Out TDD' tartışması var. Demek ki benim de birini seçip, benimsemem ve sevmem lazım. Ancak bu şekilde iyi olabilirim."

Başlangıçta gayet olağan ve masumca olan bu özenme hali pek çok kez, zaman içinde, yine sığlıktan kaynaklanan bir fanatizme dönüşür. Çaylaklığını atlatanlar, olgunlaşanlar bu tartışmaların yazılım ekosistemini besleyen, ilerleten, renklendiren tartışmalar olduğunu anlarlar. O seviyede takılanlar ise -tıpkı futbol tribünlerindeki holiganlar gibi- olayın aslını unutup, bu çatışmaların olayın kendisi olduğu yanılgısına kapılırlar ve çatışmalar üzerinden var olmanın peşindedirler.


#### 4. Cehalet, Aptallık ve Ahlaksızlık

Aziz okuyucu! 

Bu zamana kadar bahaneler üretmeye, mazur görmeye çalıştık. Lakin itiraf etmenin zamanı geldi. Çıkarlarını koruma iç güdüsü, sevmek, bağlanmak, benimsemek, özenmek... Bunların hepsi olağandır, güzeldir. Lakin ölçüyü kaçırmanın, çatışma üretmenin, insanları kırmanın, insanları yanlışa yönlendirmenin, işvereninin çıkarları aleyhine kararlar almanın mazereti yoktur.

***Şu hakikati kabul etmek farzdır: Her alanda olduğu gibi yazılım alanında da fanatizmin asıl sebebi cehalet, aptallık ve ahlaksızlık üçlüsüdür ki nedense bu üçlü pek çok zaman bir arada gezerler.***

* ***Cehalet diyorum çünkü;*** bunlar kendini biliyor sanır ama çok şey bilmezler,  <a href="https://en.wikipedia.org/wiki/Dunning%E2%80%93Kruger_effect" target="_blank">hatta bilmediklerini de bilmezler. </a> Dikkat edin çok okuyan, araştıran insanlarda bu tip fanatiklikler göremezsiniz. Onlar ancak bu fanatizmden <a href="https://medium.com/@fkadev/tak%C4%B1m-tutar-gibi-teknoloji-tutmak-c4c535247deb#.ffx19xyy0" target="_blank">muzdariptirler,</a> her şeyin iyi ve kötü yanlarını öğrenip, gerekeni gerektiği yerde kullanmaya özen gösterirler.
* ***Aptallık diyorum çünkü;*** bu fanatizm pek çok defa insana ve çevresindekilere zarar verir. Bütün bu zararlarına rağmen fanatizme kapılmak, kaybetmek ancak akılsızlıkla, aptallıkla açıklanabilir. Ancak aptallar bu kadar kör olabilir. 
* ***Ahlaksızlık diyorum çünkü;*** bu davranışlar ne iş ahlakına uyar ne de görev bilincine. Yazılımcı alet çantasındaki araçları tanımak ve onların sayısını arttırmak ile yükümlüdür. Onlardan bir ya da birkaçına körü körüne bağlanıp kendisine, işine ve varsa işverenine zarar vermesi bir nevi ahlaksızlıktır.

### Kapanış

Tarihin her döneminde, her toplulukta kendini gösteren  fanatizm illetinin; bu zamanda, yazılımcılar arasında da kendini göstermesi normaldir. Umut edilir ki sayıları az olsun. 

Sorunların tespiti kolay, çözümleri zordur. Ben bir çırak olarak yazılımcılar arasındaki fanatizm sorununu bir kez daha ifade ettim, kendimce irdeledim. Çözümü var mıdır bilmiyorum. Varsa da bulma, ifade etme ve uygulama işini ustalara bırakmak lazım gelir diye düşünüyorum. 

Sürç-i lisan ettiysek affola!

Not: Aşağıdaki tweet'imi favlayan herkese çok teşekkür ederim❤️
<blockquote class="twitter-tweet" data-lang="en"><p lang="tr" dir="ltr">Unutmuştum. Aklıma geldi.10 fava önümüzdeki hafta &quot;Yazılımcı Fanatikliğinin Anatomisi&quot; başlıklı blog yazarım.</p>&mdash; Erkan Erol (@erkan_erol_) <a href="https://twitter.com/erkan_erol_/status/832337511246606337">February 16, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>