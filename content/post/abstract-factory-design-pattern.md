+++
categories = ["Software","Design Patterns"]
date = "2016-06-09T00:00:00+02:00"
title = "Abstract Factory Tasarım Şablonu"

+++

## Amaç

* Elimizde benzer ve ilişkili nesneler var ve bunları somut sınıflarını belirtmeden oluşturmak için bir arayüz istiyoruz
* Platformlara özgü özellikleri ve nesnelerin platformlara göre farklılık gösteren yaratılışını sarmalamak (encapsulation) istiyoruz.
* new operatörü ile yeni nesne yaratmanın sorunlu olduğunu düşünüyoruz

<!--more-->

## Sorun

Yazdığımız uygulamanın değişik platformlarda çalışmasını istediğimizde bu platformlara özgü farklılıklar sorun oluşturur ve nesne yaratımlarında bu farklılıkları ele almamız gerekir. Örneğin, windows işletim sistemindeki arayüz birleşenleri ile ubuntu işletim sistemindekler arasında ciddi farklar vardır ve biz her yeni birleşen oluşturma esnasında platformu kontrol etmek, ona göre farklı şekilde yaratmak istemeyiz. Bir arayüz tasarlamak ve bu ortak arayüz üzerinden bütün nesneleri yaratmak ve kullanmak isteriz. Platforma özgü sınıflar bu arayüzü destekleyecek şekilde kendi birleşenlerini oluşturacak işlevselliği sağlarlar.

## Yöntem

Bu tasarım şablonunda nesnelerin sınıflarını ve nesneleri üreten üretici sınıfları soyutlamamız gerekir. Bu nesneler kullanıcılar (clients) tarafından doğrudan oluşturulmayıp nesne üretici (fabrika) sınıflar vasıtası ile üretilirler.

## Yapı

Abstract Factory her bir ürün için bir Factory Method (makeProductOne,makeProductTwo vb.) tanımlar. Her Factory Method new operatörünü ve platforma özgü ürün sınıflarını sarmalar. Her platform fabrika sınıflarıyla modellenir.

<img src="/img/Abstract_Factory-2x.png" />


## Örnek

Örneğimizde Ubuntu ve Windows platformları için UI birleşenleri üreten bir Abstract Factory şablonu oluşturalım. Bu örnekte platformlardan bağımsız bir şekilde pencere ve buton üreten bir yapı kuralım. Örneğin UML diagramı ve kodu aşağıda mevcuttur.

<img src="/img/abstract-factory-design-patterns-button.png" />
Buton arayüzü ve Windows+Ubuntu Platformları için ürün sınıfları

<br></br><br></br>
<img src="/img/abstract-factory-design-patterns-window.png" />
Pencere arayüzü ve Windows+Ubuntu Platformları için ürün sınıfları
<br></br><br></br>
<img src="/img/abstract-factory-design-patterns-factory.png" />
Abstract Factory ve Windows+Ubuntu Platformları için fabrika sınıfları
<br></br><br></br>

## Kod

<pre><code>
public interface Button {
    public void click();
    public void setLabel(String label);
}
</code></pre>

<pre><code>
public interface Window {
    public void setHeader(String label);
    public void openWindow();
    public void closeWindow();
}
</code></pre>

<pre><code>
public interface AbstractUIFactory {
    public Button createButton();
    public Window createWindow();
}
</code></pre>

<pre><code>
public class UbuntuButton implements Button {

    @Override
    public void click() {
        System.out.println("UbuntuButton is  Clicked");
    }

    @Override
    public void setLabel(String label) {
        System.out.println("UbuntuButton label is set as "+label);
    }
}
</code></pre>

<pre><code>
public class UbuntuWindow implements Window {

    @Override
    public void setHeader(String label) {
        System.out.println("Ubuntu window header is set as "+label);
    }

    @Override
    public void openWindow() {
        System.out.println("Ubuntu window is opened");
    }

    @Override
    public void closeWindow() {
        System.out.println("Ubuntu window is closed");
    }
}
</code></pre>

<pre><code>
public class UbuntuUIFactory implements UIFactory {

    @Override
    public Button createButton() {
        return new UbuntuButton();
    }

    @Override
    public Window createWindow() {
        return new UbuntuWindow();
    }
}
</code></pre>

<pre><code>
public class WindowsButton implements Button {

    @Override
    public void click() {
        System.out.println("Windows Button is  Clicked");
    }

    @Override
    public void setLabel(String label) {
        System.out.println("WindowsButton Label is set as "+label);
    }
}
</code></pre>

<pre><code>
public class WindowsWindow implements Window {

    @Override
    public void setHeader(String label) {
        System.out.println("Windows window header is set as "+label);
    }

    @Override
    public void openWindow() {
        System.out.println("Windows window is opened");
    }

    @Override
    public void closeWindow() {
        System.out.println("Windows window is closed");
    }
}
</code></pre>

<pre><code>
public class WindowsUIFactory implements UIFactory {

    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Window createWindow() {
        return new WindowsWindow();
    }

}
</code></pre>

<pre><code>
public class Client {

    public static void main(String[] args) {

        /**
         *  buradan sonra abstractUiFactory üzerinden nesneleri olusturuyoruz.
         *  windows mu ubuntu mu umrumuzda değil.
         */
        AbstractUIFactory abstractUiFactory =  createUIFactoryAccordingToOS();

        doButtonOperations(abstractUiFactory);
        doWindowOperations(abstractUiFactory);
    }

    private static void doWindowOperations(AbstractUIFactory abstractUiFactory) {
        Window window = abstractUiFactory.createWindow();
        window.setHeader("Header of Window");
        window.openWindow();
        window.closeWindow();
    }

    private static void doButtonOperations(AbstractUIFactory abstractUiFactory) {
        Button button = abstractUiFactory.createButton();
        button.setLabel("Click me");
        button.click();
    }

    private static AbstractUIFactory createUIFactoryAccordingToOS() {
        if (System.getProperty("os.name").startsWith("Windows")) {
            return new WindowsUIFactory();
        } else {
            return new UbuntuUIFactory();
        }
    }
}
</code></pre>

## Kontrol Listesi

* Platformdan bağımsızlık ve nesnelerin oluşturulması sorun oluşturuyor ise bu şablonu kullanın
* Platform vs. nesne matrisi oluşturun
* Her bir nesnenin yaratma metodlarını içeren  bir fabrika arayüzü tanımlayın
* Her bir platform için new operatörünü kapsülleyen fabrika sınıfları tanımlayın
* Kullanıcı sınıflar (clients) bu nesneleri new  operatörü ile oluşturmamalı ve fabrika sınıflarının arayüzlerini kullanmalı

## Genel Kabul Görmüş Kurallar

* Zaman zaman aynı sorunu çözmek için farklı oluştucu tasarım şablonları kullanılabilir. Prototype veya Abstract Factory şablonlarından hangisi daha uygunsa o kullanılır. Diğer zamanlarda oluşturucu tasarım şablonları birbirlerinin tamamlayıcısıdırlar. Abstract Factory şablonunda nesne yaratmak için Prototype şablonu kullanılabilir. Builder şablonu hangi birleşenlerin yaratılacağına karar verme kısmında kullanılabilir. Abstract Factory, Builder ve Prototype şablonları kendi uygulamalarında (implementation) Singleton şablonunu kullanabilirler.
* Abstract Factory, Builder ve Prototype şablonları ürün nesnelerini bilen ve yaratan, sistem içinde parametrik bir şekilde yaratılmasını sağlayan fabrika nesneleri tanımlarlar. Benzer gözükmekle beraber aralarında önemli farklar vardır. * Abstract Factory bir çok sınıfa ait nesne yaratan fabrika nesnelerine sahiptir. Builder şablonu karmışık bir ürünü, ilişkili karmaşık bir protokolü takip ederek oluşturan fabrika nesnesine sahiptir. Prototype şablonu ise prototip objeden kopyalarak yeni nesne yaratan fabrika nesnesine sahiptir.
* Abstract Factory sınıfları genellikle Factory Methods kullanılarak uygulanır (implemented). Ancak Prototype kullanılarak da uygulanabilir.
* Abstract Factory şablonu platforma özgü sınıfları gizlemek amacıyla Facade şablonuna alternatif olarak da kullanılabilir.
* Builder şablonu karmaşık bir nesneyi adım adım oluşturmaya odaklanır. Abstract Factory şablonu ise benzer ürün ailelerin* i karmaşık veya basit farketmeksizin oluşturmak için kullanılır. Builder şablonu ürünü son adımda kullanıcıya teslim ederken Abstract Factory şablonu anında ürünü verir.
* Tasarımlar genelde Factory Method (daha basit, daha özelleştilebilir) kullanılarak başlasa da tasarımcılar daha fazla esnekliğe*  ihtiyaç duyduklarını farkettiklerinde tasarım zamanla Abstract Factory, Prototype veya Builder (daha esnek ve daha karmaşık) şablonlarına evrilir.

 

## Repo

[github](https://github.com/erkanerol/design-patterns/tree/master/src/com/erkanerol/examples/abstractfactory)

## Kaynakça

* https://sourcemaking.com

**Önemli Not 1:** sourcemaking.com sitesinde  About Us sayfasında "We also welcome translation." ibaresi yer almaktadır. Bu yazının yazılmasında/çevrilmesinde herhangi bir ticari amaç bulunmamaktadır.

**Önemli Not 2:** Yazıda gördüğünüz hataları bana bildirirseniz sevinirim.
