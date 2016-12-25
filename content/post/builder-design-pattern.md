+++
categories = ["Software","Design Patterns"]
date = "2016-08-28T23:22:45+03:00"
title = "Builder Tasarım Şablonu"
draft = true
keywords = ["Software","Design Patterns","Tasarım Şablonları","Builder Tasarım Şablonu"]
+++

## Amaç

* Karmaşık nesneleri basit nesneler kullanarak adım adım oluşturmak
* Karmaşık nesnelerin yaratım süreçlerini gösterimlerinden ayırarak aynı yaratım sürecini farklı gösterimler için de kullanmak ve nesnenin boyutunu düşürmek


## Sorun

Uygulamalarımızın içinde bir çok birleşeni olan karmaşık nesneler üretmek isteriz. Nesneler karmaşıklaştıkça bunu bir hamlede yapmak zorlaşır. Hele ki birden fazla construction methoduna ihtiyacımız var ise yapı modülerliğini yitirir ve yönetilemez hale gelir.

## Yöntem

Bu tasarım şablonunda nesnenin oluşturulmasını sınıfın dışına çıkararak builder sınıf denen başka bir sınıfa aktarırız. Birden fazla builder sınıfı da olabilir. Her bir sınıfın kendine ait farklı uygulaması olabilir ve nesnenin farklı bir gösterimini üretebilir.

Diğer oluşturucu tasarım şablonlarının aksine Builder şablonu nesneyi bir kere de oluşturmaz, adım adım oluşturur ve en sonunda teslim eder.

## Yapı

O



## Örnek

Örneğimizde bir çocuk menüsü oluşturmayı ele alacağız. Menüde bir ana yiyecek,  bir içecek, bir atıştırmalık ve bir oyuncak olduğunu düşünelim. Bu menünün içeriğini değiştirerek farklı farklı menüler oluşturabiliriz. Ama oluşturma işlemi aynıdır. Gösterim ve kod aşağıdaki gibidir.

<img src="/img/Builder_example1-2x.png" />


## Kod

## Kontrol Listesi

Platformdan bağımsızlık ve nesnelerin oluşturulması sorun oluşturuyor ise bu şablonu kullanın
Platform vs. nesne matrisi oluşturun
Her bir nesnenin yaratma metodlarını içeren  bir fabrika arayüzü tanımlayın
Her bir platform için new operatörünü kapsülleyen fabrika sınıfları tanımlayın
Kullanıcı sınıflar (clients) bu nesneleri new  operatörü ile oluşturmamalı ve fabrika sınıflarının arayüzlerini kullanmalı

## Genel Kabul Görmüş Kurallar

* Zaman zaman aynı sorunu çözmek için farklı oluştucu tasarım şablonları kullanılabilir. Prototype veya Abstract Factory şablonlarından hangisi daha uygunsa o kullanılır. Diğer zamanlarda oluşturucu tasarım şablonları birbirlerinin tamamlayıcısıdırlar. Abstract Factory şablonunda nesne yaratmak için Prototype şablonu kullanılabilir. Builder şablonu hangi birleşenlerin yaratılacağına karar verme kısmında kullanılabilir. Abstract Factory, Builder ve Prototype şablonları kendi uygulamalarında (implementation) Singleton şablonunu kullanabilirler.
* Abstract Factory, Builder ve Prototype şablonları ürün nesnelerini bilen ve yaratan, sistem içinde parametrik bir şekilde yaratılmasını sağlayan fabrika nesneleri tanımlarlar. Benzer gözükmekle beraber aralarında önemli farklar vardır. Abstract * Factory bir çok sınıfa ait nesne yaratan fabrika nesnelerine sahiptir. Builder şablonu karmışık bir ürünü, ilişkili karmaşık bir protokolü takip ederek oluşturan fabrika nesnesine sahiptir. Prototype şablonu ise prototip objeden kopyalarak yeni nesne yaratan fabrika nesnesine sahiptir.
* Abstract Factory sınıfları genellikle Factory Methods kullanılarak uygulanır (implemented). Ancak Prototype kullanılarak da uygulanabilir.
* Abstract Factory şablonu platforma özgü sınıfları gizlemek amacıyla Facade şablonuna alternatif olarak da kullanılabilir.
* Builder şablonu karmaşık bir nesneyi adım adım oluşturmaya odaklanır. Abstract Factory şablonu ise benzer ürün ailelerini karmaşık veya basit farketmeksizin oluşturmak için kullanılır. Builder şablonu ürünü son adımda kullanıcıya teslim ederken * Abstract Factory şablonu anında ürünü verir.
* Tasarımlar genelde Factory Method (daha basit, daha özelleştilebilir) kullanılarak başlasa da tasarımcılar daha fazla esnekliğe ihtiyaç duyduklarını farkettiklerinde tasarım zamanla Abstract Factory, Prototype veya Builder (daha esnek ve daha karmaşık) şablonlarına evrilir.

## Repo

[github](https://github.com/erkanerol/design-patterns/tree/master/src/com/erkanerol/examples/abstractfactory)

## Kaynakça
* https://sourcemaking.com

**Önemli Not 1:** sourcemaking.com sitesinde  About Us sayfasında "We also welcome translation." ibaresi yer almaktadır. Bu yazının yazılmasında/çevrilmesinde herhangi bir ticari amaç bulunmamaktadır.

**Önemli Not 2:** Yazıda gördüğünüz hataları bana bildirirseniz sevinirim.


