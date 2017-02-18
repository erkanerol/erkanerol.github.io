+++
date = "2017-02-11T00:31:42+03:00"
keywords = ["hash functions","Hash Fonksiyonları"]
title = "Hash Fonksiyonları"
categories = ["Software"]
draft=false
weight = 1
+++

Not: İşbu yazı Wikipedia kullanılarak yazılmıştır. Arzu edenler <a href="https://en.wikipedia.org/wiki/Hash_function" target="_blank">şu</a> link üzerinden devam edebilir.

Hash fonksiyonları belirli özellikleri sağlayan fonksiyonlardır. Bu özelliklere değinmeden önce fonksiyonları kısaca hatırlatmak istiyorum.

<!--more-->

### Fonksiyonlar

#### Tanım

Fonksiyonlar tanım kümesindeki her bir elemanı görüntü kümesinde yalnızca bir elemana bağlayan ilişkilerdir.  

* Tanım Kümesi: X
* Görüntü Kümesi: Y


***Örnek 1:*** Tanım kümesindeki bütün elemanlar eşleşmemiş. 4 açıkta kalmış. Onun için bu ilişki bir fonksiyon değil.
<img src="/img/hash/fonksiyon_degil.png"/>

***Örnek 2:*** Tanım kümesindeki bir değer görüntü kümesindeki iki değer ile eşleşmiş. Onun için bu ilişki bir fonksiyon değil.
<img src="/img/hash/fonksiyon_degil_2.png"/>

***Örnek 3:***Tanım kümesindeki bütün elemanlar görüntü kümesinde yalnızca bir elemen ile eşleşmiş. Onun için bu ilişki bir fonksiyon. f(1)=A, f(2)=B, f(3), f(4)=C
<img src="/img/hash/fonksiyon.png"/>

<br><br>
#### Ters Fonksiyon

Dikkatinizi çektiyse ilişkinin fonksiyon olması için görüntü kümesindeki bütün elemanların ilişkide yer almasına gerek yok. Son örnekte D elemanı açıkta kalmış. Görüntü kümesindeki bütün elemanlar eşleşmede yer alır ise bu fonksiyonlara ***örten*** fonksiyonlar diyoruz.

***Örnek 4:*** Örten fonksiyon
<img src="/img/hash/orten.png"/>


Bu örnekteki fonksiyon iyi, güzel, hoş ama tersini alamıyoruz. f(x)=A dendiğinde x değerinin 1 olduğunu tahmin ediyoruz ama f(x)=C dendiğinde x değeri 3 mü 4 mü bilemiyoruz. Fonksiyonların tersini alabilmek için hem birebir hem de örten olmaları gerekiyor.

***Örnek 5:*** Birebir ve örten fonksiyon. f(1)=A, f(4)=D, f<sup>-1</sup>(D)=4
<img src="/img/hash/birebir_ve_orten.png"/>


#### Tek Yönlü Fonksiyonlar

Tek yönlü fonksiyonlar (one-way functions) hesaplaması kolay ancak tersini alması çok zor olan fonksiyonlara verilen genel bir isim. Teorik olarak tersi olması da gerekmiyor. Önceki örneklerde "f(x)=C dendiğinde x değeri 3 mü 4 mü bilemiyoruz" demiştik. Tek yönlü fonksiyonlar öyle fonksiyonlar ki f(x)=C dendiğinde bu koşulu sağlacak herhangi bir x değeri bulmak çok zor. 

Şöyle anlatalım. f(x)=x*x , f(x)=81 dendiğinde x=9 veya x=-9 diyebiliyoruz. Tek yönlü fonksiyonlarda ise fonksiyonun hesaplama formülü ortada olmasına rağmen herhangi bir sonucu çıkarabilecek girdiyi tahmin edemiyoruz. Tersini bu örnekteki gibi "karekökünü alırım" şeklinde bulamıyoruz. Bir çıktıyı üretecek girdiyi bulabilmek için şu andaki işlem gücümüz ile yıllar süren çalışmalar yapmamız gerekiyor. Matematikçilerin bize verdiği altın değerinde ve hayati işler gören bu fonksiyonlara ilerde tekrar değineceğiz.


### Hash Fonksiyonları

Hash fonksiyonları değişken büyüklükteki tanım kümeleri ile sınırlı büyükteki görüntü kümeleri arasında ilişki kuran fonksiyonlardır. Bu anlamda özetleme görevi üstlenirler. Kümeler arasındaki bu genişlik farklılığı nedeniyle çoğu zaman birden fazla değerin sonucu aynıdır. Hash fonksiyonları adı üzerinde fonksiyonlardır ve genel fonksiyon özelliklerini taşırlar: ***Tanım kümesindeki bütün değerler için sonuç verirler ve her değer için her seferinde aynı sonucu verirler.***

***Hash Fonksiyon Örneği***

Tanım kümesi: En fazla 1000 karakter(ASCII) uzunluğundaki bütün karakter dizileri. Kümenin eleman sayısı 128<sup>1001</sup>/127, yaklaşık 1.63*10<sup>2107</sup>.

Görüntü kümesi: [0,10<sup>7</sup>) arasındaki doğal sayılar. Kümenin eleman sayısı 10<sup>7</sup>. 

Örnekler:

f("erkan")=23, f("hash değeri alınan string")=133453, f("h")=101112, f("a")=23

Bu örnekte ortalama olarak tanım kümesindeki her  1.63*<sup>2100</sup> değer görüntü kümesindeki bir eleman ile ilişkili oluyor. Buna ***collision yani çakışma/çarpışma*** adı veriliyor.Yukarıdaki örnekte "erkan" ve "a" girdilerinin hash değerleri çakışmış örneğin.

### Nerelerde Kullanılır?


1. Bir sitemiz var ve üyelerimizin bilgilerini bir yerde tutmak istiyoruz. Her bir üye için bir üye nesnesi oluşturuyoruz. Üyeleri (dolayısıyla nesneleri) birbirinden ayırt edebilmemizi sağlayan şey email adresleri. Bu üye nesnelerini nasıl saklayacağız?
 * Birinci alternatif: Düz bir listeye sırasız koyalım. Gerektiğinde listenin başından sonuna tarayalım ve bulalım. En kötü ihtimalde complexity: O(N)
 * İkinci alternatif: Düz bir listeye email adreslerine göre sıralı koyalım. Arama yaparken ikili arama (binary search) uygulayalım. En kötü ihtimalde complexity: O(logN)
 * Üçüncü alternatif: Hash fonksiyonları kullanalım :) Öyle bir fonksiyon olsun ki herhangi bir email adresini alsın ve sonuç olarak listeden bir index dönsün. Elimdeki üyeyi o index'e yerleştireyim. Sonradan lazım olduğunda hash değerini üretip doğrudan index'e gideyim. En kötü ihtimal ile complexity: O(1). Evet yanlış duymadınız O(1). Yani üye sayımız ne kadar artarsa artsın sabit(constant) bir sürede üye bilgilerini şıp diye bulabiliyoruz. Buna ***hash tablosu*** diyorlar. Lakin farketti iseniz burada hash fonksiyonumuz iki farklı email değeri için aynı index'i üretirse batıyoruz :) O zaman hash fonksiyonunu güncellememiz ve tabloyu genişletmemiz gerekiyor. ***İyi bir hash fonksiyonu collision'ı olabildiğince azaltır (collision-resistant) ve görüntü kümesi üzerine eşit bir dağılım hedefler (uniformity).***  Bu örnekte kullanıcı emaillerinin alabileceği değerler çok fazla olmasına rağmen, index olarak tutabileceğimiz listenin uzunluğu sınırlı. İstesek bütün email adreslerini sayısal değerler çevirip o şekilde de indeksleriz ama yerimiz dar :)  ***Onun için hash fonksiyonu tanımlanan bir aralıkta dönmeli (defined range).***

2. İnternetten 10TB'lık program indirdim. Program ile nükleer bomba atacağım. Lakin ağ üzerinden indirdiğim dosyasının içinde bir şeyler değişmişse Allah muhafaza bombayı yanlış yere atabilirim. Onun için dosyanın birebir aynı şekilde indiğinden emin olmam lazım. Dosyayı bir kaç kere indirip bit bit kontrol etsem işimi görür aslında. Aynı yerlerde hata yaşanma olasılığı çok düşük. Ama dosya çok büyük yahu. Şunun kolay bir yolu olmalı. Mesela elimde bir fonksiyon olsa, kaynak tarafında dosyayı bit bit okuyup bir özet çıkarsa, sonra indirdikten sonra bilgisayarımda ben de çıkarsam ve özetler aynıysa dosyalar aynıdır diyebilsem ve gönül rahatlığıyla(!) bombamı atabilsem süper olurdu değil mi? Hash fonksiyonları ayağınıza geldi :) Dosya boyutu ne kadar uzun olursa olsun hash fonksiyonu onu özetler ve size kısa bir özet verebilir. Örneğin 256 karakterlik bir anahtar. Ve bu fonksiyon öyle maharetlidir ki dosyanın bir biti değişmiş ise bu anahtarda değişir. Dosyanın değişmiş olmasına rağmen aynı anahtarı üretmesi o kadar küçük bir ihtimaldir ki ona güvenip nükleer bomba atabilirsiniz. (Ya da <a href="https://coinmarketcap.com/" target="_blank">milyar dolarlarınızı</a> ona emanet edebilirsiniz. Nasıl diye sorduğunuzu duyar gibiyim sevgili okuyucu. Oraya da geleceğiz. Takipte kalın :) )

3. Şifre ile girilebilen bir sitemiz var. Kullanıcıların şifrelerini veritabanında kabak gibi saklamak istemiyoruz. Kendimize bile güvenmiyoruz, o derece bir güvenlik lazım. Öyle bir şey olmalı ki kullanıcının şifresini alsın, bir şeye dönüştürsün, ben elimde şifre varsa onu tekrar üretebileyim ama geriye döndürerek o şeyden şifreyi bulamayayım. Yüz yılın icadı "Cryptographic Hash Functions" imdadınıza yetişti. Bu fonksiyonlar yukarıda bahsettiğimiz tek yönlü fonksiyonları kullanır. Elinizde şifrenin hash değeri olsa bile şifreyi bulamazsınız. Lakin bir şifreyi bu fonksiyonlara soktuğumuzda çat diye aynı sonucu elde ederiz her seferinde (determinism). 

Sistemi şöyle kurduğumuzu düşünelim: Şifreyi kullanıcıdan al, hash değerini hesapla, veri tabanındaki değer ile karşılaştır. Aynıysa kullanıcıyı içeri al, değilse reddet. Burada önemli noktalar var.

* Kullanıcı başka bir şifre girmiş olabilir. Aynı hash değerini üretmişsek onu içeri alıyoruz. Demek ki görüntü kümemiz o kadar büyük olmalı ki çakışmalar imkansıza yakın olmalı. Şu sıralar kullanılan SHA-512 için görüntü kümesi büyüklüğü 2<sup>512</sup>~1.34*10<sup>154</sup>
* Önceki örnekte de olduğu gibi hash fonksiyonumuz görüntü kümesi üzerine eşit dağılmaz ve hep yakın hash değerleri üretirse çakışma artar ve verimsizleşir. Onun için uniformity şart!
* Diyelim ki sitenin veri tabanını patlattık ve bütün hash değerleri elimizde. Sitenin de kullandığı hash fonksiyonunu biliyoruz. Örneğin MD5. Oturup rastgele şifreler ile hash üretiyoruz. O kadar çok üretiyoruz ki bütün hash değerlerini üretecek en az bir girdi var elimizde. Bu şekilde kullanıcının şifresini bilmesek de onun yerine giriş yapabiliriz çünkü onun hash değerini üretebiliyoruz. Site şifrenin kendisine değil hash değerine bakıyor sonuçta. Buna "dictionary attack" yani sözlük saldırısı diyorlar. Korunmak için görüntü kümesi büyük fonksiyonlar tercih edilmeli. Ayrıca ***salt*** denen ek bir parametre ile fonksiyonların sonuçlarını değiştirebiliyoruz. Yani kendimize özel bir SHA 512 hash fonksiyonumuz oluyor. Böylece bu atağı yapmak isteyenler benim bu salt değerimi bilerek sözlüklerini oluşturmalılar. Bu da işlerini oldukça zorlaştırıyor.
* Burada kullandığımız hash fonksiyonları benzer girdiler için benzer değerler üretmemeli. Girdide 1 karakter bile değişse çıktı bambaşka bir şey olmalı. Bu şekilde tersine mühendisliğe bütünüyle kapalı olmalı. Burada olmamalı dediğimiz özelliğin literatürdeki karşılığı ***continuity*** yani süreklilik. Bunu şöyle düşünebilirsiniz. Bir fonksiyon çizdik x-y düzlemine. f(1)=4 olduğunu biliyoruz. Bu fonksiyon sürekli ise f(1+epsilon) yaklaşık 4 civarı olmalı. ***Biz Cryptographic hash fonksiyonlarında bunu istemiyoruz.*** Tamamen alakasız olmalı. Yukarıdaki dosya özet örneğinde de istediğimiz bir şey bu. Bir bit bile değişse hash değeri farklı hatta alakasız olmalı.
* Kullanıcılarımıza ek bir hizmet sunmak isteyebiliriz. Caps lock yüzünden yanlış şifre girilmesini istemiyoruz mesela. Şifre "zOrŞifRe123" ise kullanıcı "ZoRşİFRE123" girse de kabul etmek istiyoruz örneğin. Bu tip ihtiyaçlar için bazı hash fonksiyonlarına ***"Data Normalization"*** denen özellik de eklenmiş. Bazı farklılıkları göz ardı ederek yani veriyi önce "normalize" ederek aynı sonucu veriyor. 

<br><br>
Hash fonksiyonları ile ilgili benim anlatacaklarım bu kadar. Metinde bir hata gördüyseniz lütfen aşağıya yorum olarak belirtin. Bir sonraki yazıda ***Public Key Infrastructure (PKI)*** anlatacağım. <a href="https://twitter.com/erkan_erol_" target="_blank">Twitter</a> üzerinden takipte kalabilirsiniz :)