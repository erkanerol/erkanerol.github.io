+++
categories = ["Software","Kavramlar"]
date = "2016-09-29T22:27:32+03:00"
description = ""
title = "Yazılım Bağlamında Cohesion ve Coupling Kavramları Üzerine"
draft=false
keywords = ["Cohesion","Coupling","High Cohesion","Low Coupling"]
+++


## Giriş

Bu aralar Software Craftsmanship alanında okumalar yapmaya ve vakit buldukça konuşmalar izlemeye çalışıyorum. Yazılımın temel prensipleri ve kavramları bu alanın ana başlıklarından. Ben de okuduğum, anlamaya çalıştığım bu 2 kavramı çok çok basit şekilde,  kendimce açıklamaya çalışacağım.

<!--more-->

### Diyelim ki...

Diyelim ki bir yazılım geliştiriyoruz. Yazılımımızın yapması gereken belirli işler var. Bu işleri yapabilmek için irili ufaklı onlarca/yüzlerce/binlerce fonksiyona ihtiyacımız var. Bunların rastgele bir fonksiyon uzayında yer aldığını düşünelim.

<img src="/img/cohesion-coupling/functionSpace.png" title="Function Space"/>

Bu fonksiyonlar arasındaki elbette ilişkiler var. Birbirlerini çağırıyor olabilirler, aynı veri yapılarını kullanıyor olabilirler, aynı fonsiyonlara bağımlılıkları olabilir vs. Şimdilik o kısmı bir kenara bırakıyoruz ve şu soruya odaklanıyoruz. Bunlar yazılımımızda bu şekilde mi bulunur? Böyle dağınık, başıboş, düzensiz :( Elbette bunları gruplara ayırır, modüllerin içine koyarız değil mi? Java açısından baktığımızda bunları sınıfların(classes) içine koyarız, paketleme yaparız. Olmadı bunları modüllere/projelere ayırır, build araçları ile birbirine bağlarız.


### Deneme bir ki...

Kabaca fonksiyonları modüllere ayırmaya çalışalım.

<img src="/img/cohesion-coupling/modules1.png" title="Modules1"/>

"Ya şimdi writeLog ile sendMail ne alaka" cümlesini duyar gibiyim sevgili okuyucu :) Maalesef ki bizim Utils/Helpers gibi isimler verdiğimiz nesnelerin içi de biraz böyle. Bu neden böyle olmamalı konusuna geliyorum hemen.

Modüllere ayırırken nasıl ayırırız? Neyi amaçlarız? Hedefimiz nedir? Ne kadar büyük olmalılar? Aradaki ilişkiler nasıl olmalı?

### Amaç

**Öğelerimizi modüllere ayırırken genel olarak izlediğimiz prensip şu: Bir değişiklik yapmamız gerekiyorsa sadece bir yerde yapmamız gerekmeli, bir yeri değiştirmemiz gerekiyorsa bunun sadece bir nedeni olmalı.** 

Burada biraz duralım. Şimdi şöyle düşünelim. Elimizde öğeler var. Herhangi bir modülün parçaları bunlar. Bir de olası değişiklik nedenleri var.

Eğer alakasız şeyleri bir araya koyarsak birden fazla sebep orayı değiştirmemizi gerektirebilir. Yani modülümüz **odaklı** olmaz, ne iş yaptığı kolay anlaşılamaz, bakımı zorlaşır. Bu istediğimiz bir durum değil. Öğeler olabildiğince birbirine yapışık olmalı. Tabi ki bunun için öğeleri gereksiz yere birbirine yapıştırmıyoruz :) Yapışık olmayanları ayırmamız lazım.
<img src="/img/cohesion-coupling/lowCohesion.png" title="lowCohesion"/>

<br></br>

Eğer ayırdığımız modüller birbirleriyle çok bağlantılı ise bir nedenden dolayı birden fazla yeri değiştirmemiz gerekebilir ki bunu istemiyoruz. Çünkü bu durumu yönetmek zaman içinde zorlaşır. 
<img src="/img/cohesion-coupling/highCoupling.png" title="highCoupling"/>


<br></br>
İdeal olan, istediğimiz bu ilişkinin bire bir olmasıdır. Bunun için tek sorumluluk prensibini ( <a href="http://webpro.github.io/programming-principles/#single-responsibility-principle" >Single Responsibility Principle - SRP </a>) iyi uygulamamız gerekir. 

bkz: <a href="https://en.wikipedia.org/wiki/Unix_philosophy#Do_One_Thing_and_Do_It_Well"> Do One Thing and Do It Well. </a> 

<img src="/img/cohesion-coupling/ideal.png" title="ideal"/>



### Tanımlar

Bir modülün içindeki öğelerin birlikteliğini ve modülün odaklılığını ifade etmek için kullandığımız kavram **cohesion**. Düşük cohesion dediğimizde bu birlikteliğin zayıflığını, yüksek cohesion dediğimizde bu birlikteliğin yüksekliğini ifade ederiz. Az önce anlattığım üzere hedef olabildiğince yüksek (**high cohesion**) olmasıdır. 

Modüllerin arasındaki irtibatı ifade etmek için kullandığımız kavram da **coupling**. Düşük coupling modüllerin daha bağımsız olduğunu, yüksek coupling birbirlerine daha bağlı olduğunu ifade eder. Hedef olabildiğince düşük (**low coupling**) olmasıdır. İlk şekildeki modüllerin arasındaki okların olabildiğinca az ve zayıf olmasıdır.



### Deneme bir ki...

<img src="/img/cohesion-coupling/modules2.png" title="Modules2"/>

Şimdi biraz daha iyi oldu gibi. Ama içlerini görmeden tam olarak emin olamayız :) 


<br></br>
### Cohesion Çeşitleri

Sevgili büyüklerimiz, yazılımcılar modülleri nasıl oluşturuyor, ne tür cohesion oluşturma tipleri var gibi bir araştırma yapmış ve şu tipleri bulmuşlar.

**Tesadüfi (Coincidental):**
Modüldeki öğelerin rastgele seçilmesi durumu. Yukarıdaki writeLog ile sendMail'in aynı modülde olması gibi. Bu daha çok Utils, Helpers gibi isimlendirilen modüllerde görülüyor. **En kötüsü budur.**

**Mantıksal (Logical):**
Aynı kategoride olduğu düşünülen öğelerin aynı modüle konulması durumu. Mesela bütün veri tabanı işlemlerinin ya da bütün input girişlerine ilişkin işlemlerin aynı modüle konulması.


**Geçici (Temporal):**
Zaman içinde lazım oldukça eklenen öğelerin modül oluşturması durumu. Bu işlemler birbirleriyle alakasız olabilir. Gereksinimler değiştiğinde bambaşka yerlere de gidebilir ama o an için oradalar işte.

**Prosedürel (Procedural):**
Bir akışın içindeki işlemlerin beraberce modüller oluşturması durumu. Örnek olarak dosyanın varlığının kontrolü, dosyanın açılması, yazılacak içeriğin oluşturulması, verinin yazılması, dosyanın kapatılması gibi işlemlerden oluşan bir modül düşünebilirsiniz.

**İletişimsel(Communication):**
Aynı veri/girdi/çıktı üzerinde çalışan işlemlerin aynı modüllere konulması durumu. Örneğin bir nesneniz var ve bu nesneye ilişkin bütün herşey bir arada. Kontroller, hesaplamalar, güncellemeler vs.

**Sıralı (Sequential):**
Birbirleriyle girdi-çıktı ilişkisi olan, sıralı işlemlerin aynı modüllere konulması durumu. Prosedürel olan tipe oldukça benziyor. Farkı şöyle. Bu tipteki işlemlerden birinin çıktısı, diğerinin girdisi durumunda. Dosyanın okunması işlemi ile okunan verinin işlenmesi işlemi bu tipte aynı modülde oluyor. Prosedürel de böyle bir girdi-çıktı ilişkisi gerekli değil. Ard arda olması yeterli. 

**Fonksiyonel (Functional):**
Yalnızca iyi tanımlanmış bir adet işi yapmak üzere işlemlerin bir araya getirilmesi ile modüllerin oluşturulmasıdır. XML formatındaki bir string'i parse eden bir modül örneğin. **En iyisi budur.**  


### Şunlara bakılabilir


Bu konu epey uzun. Daha çok okumak ve öğrenmek isteyenlere aşağıdaki linkleri verip konuyu kapatalım.

1. <a href="https://codurance.com/software-creation/2016/03/03/cohesion-cornerstone-software-design/">Cohesion - Sandro Mancuso</a>
* <a href="http://webpro.github.io/programming-principles/#maximise-cohesion">Maximise Cohesion</a>
* <a href="http://webpro.github.io/programming-principles/#minimise-coupling">Minimise Coupling</a>
* <a href="http://www.cs.toronto.edu/~penny/teaching/csc407-02s/lectures/04structured-design.pdf">Structured Design - CSC407</a>
* <a href="https://www.amazon.com/Structured-Design-Fundamentals-Discipline-Computer/dp/0138544719">Structured Design: Fundamentals of a Discipline of Computer Program and Systems Design - Yourdon Press , Larry L. Constantine</a>
