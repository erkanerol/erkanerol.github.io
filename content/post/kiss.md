+++
categories = ["Software","Prensipler"]
date = "2016-05-14T16:15:53+03:00"
title = "KISS Prensibi"
keywords = ["KISS Prensibi","Yazılım Prensipleri","KISS","Software Principles"]
+++

>Basitlik nihai karmaşıklıktır.

<img src="/img/Simplicity.jpg"/>

<!--more-->

## Programlama Prensibi

KISS

## Açılımlar

* "Keep It Simple, Stupid"
* "Keep it Simple, Silly"
* "Keep It Short and Simple"
* "Keep It Simple and Straightforward"
* "Keep It Small and Simple".

## Açıklama

KISS, basitlik için çabalamayı öneren bir prensiptir. 1960'lı yıllarda Amerikan donanmasında ilk olarak ifade edilen bu prensip günümüzde en önemli programlama prensiplerinden biridir. Bir problemi çözerken olabilecek en basit ve yalın çözümü seçmeyi önerir. Hatta o kadar basit olmalıdır ki ilk bakışta "Bunu bir aptal bile yapar ve anlar" demeliyiz.

KISS, karmaşık çözümlerin daha "zekice"  çözümler olduğu fikrini reddeder. Çoğu mühendis/yazılımcı karmaşık çözümler bularak, karmaşık yapılar inşa ederek "zekice" işler yaptığını düşünür. Dışarıdan bakan bir insan ne kadar zor anlar ise o kadar artı değer ürettiğini sanır. Ancak zor olan basitleştirmektir. Yazılım süreçlerinde kod/yazılım mimarisi zaten zaman içinde büyüme ve karmaşıklaşma eğilimindedir. Önemli ve zor olan istenen işlevselliği asgari karmaşıklık düzeyinde sağlayabilmektir.

Basit çözümler daha iyidir çünkü:

1. Basit çözümlerin uygulanması daha kolaydır.
+ Basit çözümler hataya daha az açıktır.
+ Basit çözümlerin bakımını yapmak daha kolaydır. Zaman tasarrufu sağlar.
+ Basit çözümler kolay değiştirilebilir. Çevik olma açısından gereklidir.


## Yöntemler

1. Zekanızı kodun karmaşıklığı üzerinden göstemeyin.
+ Ufak çaplı performans kazançları için basit çözümler yerine karmaşık çözümleri tercih etmeyin.
+ Problemleri alt problemlere ayırın. Alt problemleri az parametreli, kısa methodlar ile çözün.
+ İstisnai durumları çok fazla düşünüp yapıyı karmaşıklaştırmayın.
+ Geleceğin problemlerini bugünden çözmeye çalışmayın. (bkz: YAGNI)
+ Kod silmekten çekinmeyin. En iyi kod en kısa koddur.
+ Kodu yeniden düzenlemekten çekinmeyin. IDE'lerin de yardımıyla kodunuzu sık sık bölüp, sadeleştirin.


## Kaynakça

1. http://principles-wiki.net/principles:keep_it_simple_stupid
+ https://en.wikipedia.org/wiki/KISS_principle
+ https://people.apache.org/~fhanik/kiss.html
+ http://www.pratikprogramci.com/2014/06/01/en-basit-cozumu-olusturma-yetisi-nasil-kazanilir/
