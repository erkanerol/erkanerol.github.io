+++
Categories = ["Yazılım","Prensipler"]
Description = ""
Tags = ["Development","DRY","Keep it Dry","Principles","Programlama Prensipleri","Software Engineering","Yazılım Mühendisliği"]

date = "2016-08-20T20:23:06+03:00"
title = "Dry Prensibi"
draft = false
+++

>Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.

NOT: Bu yazı The Pragmatic Programmer kitabındaki "The Evils of Duplication" bölümünden yararlanılarak yazılmıştır.

## Programlama Prensibi

DRY

Keep It Dry

Keep Things Dry

<!--more-->

## Açılımlar

Don't repeat yourself

## Açıklama

DRY prensibi yazılımın en temel prensiplerinden biridir. "Kendini tekrar etme" anlamına gelen bu prensip kod tekrarlarından sakınılması gerektiğini savunur. The Pragmatic Programmer kitabında DRY olarak isimlendirilmiş ve genişçe açıklanmıştır.

Programcılar olarak bizler bilgiyi toplar, organize eder, bakımını yapar ve programlarımızda kullanırız. Zaman zaman dökümanlar hazırlarız. Testlerde kontrol amaçlı kullanırız. Fakat maalesef bilgi durağan değildir, zaman içinde sıklıkla değişir. Bizler elimizdeki bilgiler her değiştiğinde yaptığımız işleri gözden geçirmek ve onları bu güncellemeye göre değiştirmek zorundayız.  Bu bakım sırasında bilginin sistem içindeki bütün gösterimlerini bulmamız gerekir. Eğer sistem içerisinde değişen bilgilerin bir çok yerde gösterimleri var ise bu bakım kabusa dönüşür. Bu nedenle yazılımın daha güvenilir olması, bakımının kolay yapılabilmesi ve anlaşılması adına şu prensibe ihtiyacımız vardır:

"Her bir bilgi parçacığı sistem içinde tek, anlaşılabilir ve yetkilendirilmiş bir gösterime sahip olmalıdır."
Aksi halde bir yerde değişiklik yaptığımızda diğer yerlerde de değişiklik yapmayı hatırlamamız gerekir ki bu gereklilik hatalara neden olabilir.  Şimdi bunu iki örnek ile açıklayalım.

Birincisi çok basit bir kod tekrar örneği. [4]

<pre>
<code class="language-js">
// BAD
if ( eventfade.data( "currently" ) !== "showing" ) {
    eventfade.stop();
}
 
if ( eventhover.data( "currently" ) !== "showing" ) {
    eventhover.stop();
}
 
if ( spans.data( "currently" ) !== "showing" ) {
    spans.stop();
}
 
// GOOD!!
var elems = [ eventfade, eventhover, spans ];
 
$.each( elems, function( i, elem ) {
    if ( elem.data( "currently" ) !== "showing" ) {
        elem.stop();
    }
});
</code>
</pre>

Yukarıdaki örnekte bir kontrol ve method çağırma işlemi 3 element içinde ayrı ayrı yapılmış. Diyelim ki biz bu koşulda ikinci bir method çağırma ihtiyacı duyduk. Yapacağımız şey 3 if bloğunun içine de bu çağırma ifadesini eklemek olacaktı. Bu sayının 3 değilde 10 olduğunu düşündüğümüzde bunun hataya ne kadar açık olduğunu görebiliriz.  Halbuki ikinci şekildeki gibi yazılsaydı bir satırda değişikliğimizi yapabilirdik. Bu nedenle kodu başlangıçta ikinci şekildeki gibi yazmalıyız. Eğer elementlere özel değişik koşullar eklenirse refactoring yaparak kodu bölmeli ve yine tekrarlama olmadan işimizi halletmeliyiz.

İkinci olarak daha güzel bir örnek vermek istiyorum. Diyelim ki bir CRUD uygulaması yazıyoruz. Uygulamada 3 tip kullanıcı var: yönetici, kayıtlı kullanıcı ve misafir kullanıcı. Uygulamada 4 tip varlık (entity) ve bunların web sayfaları var.

<pre>
<code class="language-java">
//BAD
HashMap<String,String> pages = new HashMap<String, String>();
pages.put("book","mybooks.xhtml");
pages.put("author","myauthors.xhtml");
pages.put("reader","myreaders.xhtml");
pages.put("library","mylibraries.xhtml");

HashMap<String,String> adminAuthority = new HashMap<String,String>();
adminAuthority.put("book","all");
adminAuthority.put("author","all");
adminAuthority.put("reader","all");
adminAuthority.put("library","all");

HashMap<String,String> registeredUserAuthority = new HashMap<String,String>();
registeredUserAuthority.put("book","create");
registeredUserAuthority.put("author","create");
registeredUserAuthority.put("reader","read");
registeredUserAuthority.put("library","read");

HashMap<String,String> guestUserAuthority = new HashMap<String,String>();
adminAuthority.put("book","read");
adminAuthority.put("author","read");
adminAuthority.put("reader","read");
adminAuthority.put("library","read");



//GOOD
public class MyEntity {
    String page;
    HashMap<String,String> authorities;
}
</code>
</pre>


İlk şekilde bir bilginin dörde ayrılıp 4 farklı yerde saklandığını görüyoruz. Bir varlığın web sayfası ve 3 kullanıcı için yetkileri 4 farklı yerde. Yeni bir varlık eklememiz gerektiğinde 4 yerde değişiklik yapmamız zorunlu. Halbuki ikinci şekildeki gibi bir nesne kullansak, uygulamanın her bileşeni bu bilgi içinden kendine lazım olan kısmı alır ve kullanır. Böylece bilginin bir gösterimi olur ki takibi ve bakımı çok daha kolay olur.

DRY prensibinin açıklamasında sistem kelimesinin kullanılmasının bir anlamı vardır. Tekrarlamanın sadece kodda değil bütün bir sistemde olmaması gerektiğini ifade eder. Yorumlar, testler, dökümanlar vb. bu sisteme dahildir.

Tipler, Nedenler ve Çözüm Önerileri

### 1. Zorla Tekrarlama

Bu tekrarlama tipinde geliştiriciler tekrarlama yapmaktan başka çarelerinin olmadığını düşünürler.

a) Bilginin birden fazla gösteriminin zorunlu olması: Bazen yazılımın mimarisi tekrarlamayı sorunlu kılar. Örneğin, server-client yapısı kullandığımızda iki tarafta da aynı bilgiyi kullanmamız gerekir. Bu gibi durumlarda dilin metadata özelliklerini kullanarak kod üreten kodlar geliştirmeye, tekrarlamaları otomotik olarak üretmeye çalışmalıyız.
b) Kodun içinde dökümantasyon: Geliştiriciler zaman zaman iyi kodun çok yorum eklenen kod olduğunu düşünse de iyi kod çok yorum gerektirmez. Özellikle alt düzeylerde çok açık işlemleri yorumlamak tekrarlama olarak düşünülebilir. Bu nedenle sadece gerekli yerlerde yorumlar eklenmelidir.
c)Kod ve Dökümantasyon: Dökümanlar ve kod  çoğu zaman aynı bilginin farklı gösterimlerini içerir ve ikisinden birinde değişiklik yaptığımızda diğerinde de yapmamız gerekir. Bu tip durumlarda değişimleri otomatik olarak diğer tarafa yansıtacak otomasyonlar kullanmalıyız. Javadoc buna güzel bir örnektir.
d)Dil ile ilgili meseleler: Bazı diller tekrarlamayı zorunlu kılar. Aynı bilgiyi farklı yerlerde kullanmamızı bizden talep eder. C ve C++ dillerindeki başlık(header) dosyaları buna örnektir. Bu gibi durumlarla başa çıkmak kolay değildir. Dile özgü çözümleri araştırmamız ve tekrarlamadan kaynaklı sorunları nasıl hafifleteceğimizi bulmamız gerekir.

### 2. Yanlışlıkla Tekrarlama

Bu tekrarlama tipinde geliştiriciler tekrarlama yaptığının farkında değildirler. Yukarıda verdiğimiz örnekler bu kategoride değerlendirilebilir. Yazılımın genel prensiplerine uyarak ve takım içinde birbirimizin kodlarını gözden geçirerek bu sorunu çözebiliriz.

### 3. Sabırsızlıktan Tekrarlama

Bu tekrarlama tipinde geliştiriciler tembellik eder ve tekrarlamayı daha kolay bulurlar. Bu tekrarlamaların kısa vadede zaman kazandırsa da uzun vadede büyük sorunlara yol açtığı açıktır. Bilinçlenme, disiplinli kod geliştirme ve takım içinde kodların gözden geçirilmesi ile çözülebilir. (Aptallığın lüzumu yok :D )

### 4. Geliştiriciler Arası Tekrarlama

Bu tekrarlama tipinde farklı geliştiriciler bir bilgiyi farklı yerlerde tekrarlarlar. Geliştiriciler arasındaki iletişim yeterince kuvvetli olmadığında bu sorun ortaya çıkabilir. İletişimi kuvvetlendirerek, birbirimizin kodlarını gözden geçirerek bu sorunu çözebiliriz. Ayrıca yazılım mimarisinin hatalı olması da geliştiricileri buna zorlayabilir. Anlaşılabilir tasarım, iyi bir teknik lider, görevlerin iyi bir şekilde ayrıştırılması bu sorunun başlıca çözümlerindendir.

### Kaynakça

1. Hunt, Andrew, and David Thomas. "7. The Evils of Duplication." The Pragmatic Programmer: From Journeyman to Master. Reading, MA: Addison-Wesley, 2000. N. pag. Print.
+ http://www.kurumsaljava.com/2009/11/17/dry/
+ https://en.wikipedia.org/wiki/Don%27t_repeat_yourself
+ https://learn.jquery.com/code-organization/dont-repeat-yourself/