+++
categories = ["Software"]
date = "2016-01-09T22:44:45+03:00"
title = "\"Best Practice\" Üzerine Etkinlik Önerisi"
keywords = ["Best Practices","Etkinlik"]
weight = 1
+++

>TL;DR
<br></br>
Bu yazıda "şöyle bir etkinlik olsa çok faydalı olur" dediğim bir etkinlik fikrini anlatacağım.
<br></br>
Özet: Belli senaryolar üzerinden, 3-4 uzmanın kendi en iyi uygulamalarını anlattığı, katılımcıların sorularıyla desteklediği etkinlikler olsa çok faydalı olabilir.

<!--more-->


Yazılım Mühendisliği adı üzerinde "soft" olan bir şey. Sınırlandırmaları diğer mühendislik dallarına göre oldukça az. Aynı proje binlerce farklı yöntemle yapılabiliyor. Fakat bütün yöntemlerin/uygulamaların aynı kalitede sonuç verdiğini söylemek zor.  Yazılım Mühendisliği'nin temelleri ile ilgili okuduğum bir kitapta şöyle bir şey okumuştum: "Hiç bir  alanda iyiyle kötü arasındaki fark yazılımdaki kadar geniş değildir." Bu noktada best practice diye nitelenen kavram işin içine giriyor."Hangi uygulamayı hangi durumlarda kullanmak gerekir, avantajları ve dezavantajları nelerdir?" bilgisi bu farkı oluşturan başlıca unsurlardan biri. Maalesef bu tip bilgileri doğrudan kitaplardan öğrenmek o kadar kolay değil. (Varsa önerisi olan beklerim :) ) Zaman içinde çalışma hayatında tecrübe ederek öğrenilmesi bekleniyor. Bu da uzun bir zaman gerektiriyor.Bu süreci biraz hızlandırmalıyız diye düşünüyorum. Ayrıca tecrübelerin paylaşılması yeni bakış açıları kazanma açısından da çok önemli. Kendi bilgilerimizle rahatça çözdüğümüzü düşündüğümüz bir problemin çok daha kolay/iyi çözümleri olabilir.

Öncelikle şunu belirteyim böyle bir şey Dünya'nın herhangi bir yerinde böyle bir şey yapılıyor mu bilmiyorum, araştırmadım. Yapılıyordur muhtemelen ama Türkiye'de hiç duymadım. (Oooo Erkan internet bağlatmışsın tarzı bir durum varsa bir haber edin plz.)

Gelelim etkinliğe. Şöyle olsa süper olur:

**Düzenleyici**

Bir topluluk olabilir, bir organizasyon olabilir. Bu etkinlikler için kurulabilir. Ama süreklilik açısından 2-3 kişiye bağlı olmaması gerekir.

**Katılımcılar**

1 moderatör, 3 uzman yazılımcı (uzman derken öyle çok da abartmayalım gözümüzde),  20-30 katılımcı

**Mekan**

Uzmanların ekranlarını rahatça yansıtabilecekleri, internet bağlantı probleminin yaşanmadığı bir mekan.

**Zaman ve Süre**

Hafta sonu, yarım günü geçmeyecek şekilde

**Konu**

Konu çok genel olmamalıdır. Yazılım dili, framework'ler ve kullanılan teknolojiler olabildiğince tanımlanmalıdır. Mümkünse belirli senaryolar ve kısıtlamalar içermelidir.
Örnek vermek gerekirse: Java+Spring+Hibernate kullanılan bir uygulamada "soft delete" uygulamaları, dosya yönetimi (saklama, db ilişkilendirmeleri vs).

**Format** 

Önceden açıklanan konuyu moderatör katılımcılara açıklar.
Her uzman kendine göre "best practice" olan uygulamayı anlatır. Avantajlarını ve dezavantajlarını belirtir. Gerekli gördüğü yerlerde kod düzeyinde sunumlar yapar.
Uzmanlar diğer uzmanların uygulamaları hakkında eleştirilerde bulunur. Gerekirse geri vites yapar. "Doğru aslında böyle olsa daha iyi olabilir" der.
Katılımcılar çeşitli senaryolar üreterek uzmanların uygulamalarının eksik taraflarını çıkarmaya çalışır. Bu noktada uygulamaların tartıştıkça geliştirilmesi hedeflenmektedir.

**Önemli Noktalar**

Günün sonunda katılımcılar ve uzmanlar aynı "best practice" üzerinde anlaşmak zorunda değildir. Önemli olan farklı yöntemler ve bunların iyi/kötü yönlerini daha ayrıntılı öğrenmektir.
Edinilen tecrübeleri blog&repo olarak yayınlanmaya çalışılmalıdır.
Çeşitli parametrelere göre karşılaştırmalar yapılmalıdır. Kullanıcı sayısı, geliştirme süresi,istenen güvenlik seviyesi, ekleme-güncelleme-silme sıklığı vs okuma sıklığı...


Not: JavaEE, Spring, Hibernate, Jsf, Primefaces ile ilgili but tip etkinlere katılmak isterim. 
