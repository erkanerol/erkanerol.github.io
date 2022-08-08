+++
date = "2017-03-25T00:37:43+03:00"
keywords = ["Primitive Obsession"]
description = ""
title = "Primitive Takıntısı ve Değer Nesneleri"
categories = ["Software","TR"]
weight=1

+++





Primitive Obsession ünlü <a href="https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672" target="_blank">Refactoring</a> kitabındaki kod kokularından biridir. Yazılımcıların karmaşık şeyleri ilkel veri türleri ile ifade etmeye çalışmasını ifade eder. Bu yazıda bu sorunu ve çözümünü ele alacağız.

![primitive](/img/primitive.jpg)

<center>
<a href="https://dzone.com/articles/primitive-obsession" target="_blank">Görselin Kaynağı</a>
</center>
<!--more-->

<br>

## Tanımlar

Programlama dilleri genellikle ilkel veri türlerini temel olarak verirler. int, char, boolean ve -bazı dillerde- string gibi. Bunlara primitives yani ilkeller diyoruz. Bir de bunları kullanarak daha karmaşık veri türleri oluşturmaya olanak tanırlar. Class, struct, object gibi. Yalnız bu ikinci grup ek dosya oluşturma, tanımlama yapma gibi ek maaliyetler getirdiği için yazılımcılar bunları kullanmayı çok istemezler. Genellikle tembellikten ve biraz da yanlış düşünmekten (karmaşıklığın artacağını düşünürler) bunlar yerine olabildiğince ilkel veri türlerini kullanırlar. Bu "takıntı" zamanla çeşitli problemlere neden olur.

## Problemler

```java
public class Notebook	{

		private long id;
		private Date productionDate;
		private int status; // 0=PRODUCED, 1=SOLD, 2=RETURN
		private String model; // XSI-Q2TU-17-1
		

		public String getSeries(){
			return this.model.split("-")[0];
		}

		public String getModelYear(){
			return this.model.split("-")[2];
		}
}
```

* Karmaşık bir şeyi ilkel bir veri türü ile göstermeye çalıştığınızda değerleri sınırlandırmakta zorlanırsınız. Örneğin ürünün durumu için PRODUCED, SOLD, RETURN gibi bir enum oluşturmak yerine bir int değeri (0=PRODUCED, 1=SOLD, 2=RETURN) kullanırsanız, bu alana yanlışlıkla anlamsız bir değer (örneğin 25) verilmesini engellemek için fazladan çaba sarf edersiniz.

* İlkel veri türleri için gereken bir takım özel işlemleri içinde bulundukları karmaşık veri türlerinde yapmanız gerekir. Buradaki örnekte Notebook'un model numarası string olarak tutulmuş. Fakat görüldüğü üzere bu alan aslında düz bir metinden daha karmaşık bir bilgiyi içeriyor. Model serisini ve yılını almak için eklenen özel metodlar burada Notebook sınıfının üzerinde bir yük. Bu tip alanların sayısı artıkça ana sınıf çok uzun ve karmaşık bir hale gelecektir.

* Yine bu örnekteki gibi kullanımlarda bilginin önce birleştirilmesi ve beraber tutulması, sonra her seferinde bölünüp kullanılması da sorunlu bir durum.

## Çare Değer Nesneleri!

Bu sorunun çözümü değer nesnelerinin (value objects) kullanımıdır. Bu kavramı Eric Evans <a href="https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215" target="_blank">Domanin-Driven Design</a> isimli kitabında oldukça ayrıntılı bir şekilde açıklamıştır. Evvela bu kavramı kısaca anlamaya çalışalım.

Kitapta bütün nesneler üçe ayrılmış: Services, Entities, Value Objects. 

1. ***Services:*** Bu nesneler günlük hayattaki varlıklara ve değerlere işaret etmeyen işçi nesnelerdir. Parser, formatter, controller gibi sınıflara ait nesneler bu gruba girer. 
2. ***Entities:*** Üzerinde çalıştığımız alan üzerindeki varlıklardır. Örneğin, bir teknoloji mağazası envanteri ile ilgili yazılım geliştiriyorsak ürün, bölüm, raf gibi sınıflarımız olur. Buradaki her bir nesne gerçekte bir varlığa karşılık gelir. Genellikle bunları ayırmak için id'ler kullanırız. İki nesnenin bütün özellikleri aynı olsa bile id'leri farklı ise bunların farklı varlıkları işaret ettiğini bilir, bu nesneler eşit değildir deriz.
3. ***Value Object:*** Değer nesneleri ise adı üstünde değer tutmak için kullanılır. Özelliklerindeki değerleri aynı olan nesneler eşit kabul edilir. Örneğin tarih sınıfını ele alalım. İki tarih nesnesinin bütün özellikleri (yıl, ay, gün) eşitse bu iki nesne aynı tarihi ifade eder diyebilir miyiz? Evet diyebiliriz.


## Değer Nesnelerinin Kullanımı

```java
public class NotebookModel {
	final String series;
	final String subseries;
	final Year year;
	final Month month;
		
	public NotebookModel(String series, String subseries, Year year, Month month) {
		super();
		this.series = series;
		this.subseries = subseries;
		this.year = year;
		this.month = month;
	}
	
}

public enum NotebookStatus {
	PRODUCED, SOLD, RETURN;
}

public class Notebook {
	long id;
	Date productionDate;
	NotebookStatus status;
	NotebookModel model;
}
```

Örnekteki gibi karmaşık bilgileri değer nesneleri olarak tuttuğumuzda ana sınıfı temiz tutmuş oluruz. Ayrıca bu karmaşık bilginin içindeki alt bilgilere de daha doğru yoldan ulaşma fırsatımız olur.

Değer nesneleri <a href="http://wiki.c2.com/?ValueObjectsShouldBeImmutable" target="_blank">genellikle</a>  NotebookModel örneğindeki gibi immutable olarak kullanılırlar. Nesnenin ilk oluşturulma anında değerler verilir ve sonradan bu değerler değişmez. Böylece herhangi bir anda eşit olan iki nesnenin her daim eşit olacağını varsayabilir ve ortak olarak kullanabiliriz.
Ancak bu zorunluluk <a href="http://wiki.c2.com/?ValueObjectsCanBeMutable" target="_blank">değildir</a> .

<a href="http://www.jbrains.ca/" target="_blank">JbRainsberger</a> değer nesneleri ile ilgili önemli bir özelliğe dikkat çeker. Değer nesneleri için kullandığımız sınıflar "çekici" olmaya adaydırlar. Kendilerine ilişkin kodları ana sınıfın üzerinden çekerler. Önceki örnekte kullandığımız yardımcı metodları üzerine alır örneğin. Bu kodun odaklı olması açısından kritik önem arzeder.

## Veritabanlarında tutma problemi

Varlıklar genellikle veri tabanlarında saklanırlar. Veri tabanı işlemlerinde değer nesnelerinin kullanımı zorluk çıkarabilir. İlişkisel veri tabanı kullandığımızda bunları ayrı tablolara koymak istemeyiz çünkü değerleri varlık nesnesinin içine taşımak için ekstra JOIN yapmamız gerekir ki bu da performans açısından istemediğimiz bir durumdur.

Veritabanına saklama işlemlerinde değer nesnelerinin içindeki alanları ana sınıfın alanları olarak tabloda saklayabiliriz. Yaygın kullanılan ORM kütüphaneleri buna olanak tanımaktadır. Bir JPA örneği ile açıklayalım. 

```java

@Embeddable
public class NotebookModel {
  
  @Column(name = "MODEL_SERIES")
  String series;
  
  @Column(name = "MODEL_SUBSERIES")
  String subseries;
  
  @Column(name = "MODEL_YEAR")
  Year year;
  
  @Column(name = "MODEL_MONTH")
  Month month;
  
}

@Entity
public class Notebook {
  
  @Id
  @GeneratedValue(strategy=GenerationType.AUTO)
  Long id;
  
  @Temporal(TemporalType.TIMESTAMP)
  @Column(name="PRODUCTION_DATE")
  Date productionDate;
  
  @Enumerated(EnumType.STRING)
  @Column(name="STATUS")
  NotebookStatus status;
  
  @Embedded
  NotebookModel model;
}

public enum NotebookStatus {
	PRODUCED, SOLD, RETURN;
}

```

Bu örnekte NotebookModel nesnesinin alanları Notebook nesnesinin tablosuna gömülür. Böylece veri tabanı işlemlerinde performans kaybı yaşamadan, kodumuzu temiz tutmuş oluruz.

>JPA ile immutable bir sınıfı embeddable yapmak biraz zor olabilir çünkü JPA default constructor ve setter kullanıyor. Ayrıntılı bilgiye <a href="https://www.google.com.tr/search?q=immutable+embeddable+in+jpa" target="_blank">buradan</a> erişebilirsiniz.



## Eklemeler

* Liste olarak tutulması gereken bilgileri bir arada tutmak da bu kod kokusu kapsamındadır. Örneğin yetkili kullanıcıların id listesini "1,4,5,6,7" gibi virgüllerle ayrılmış string olarak tutmamalısınız. Bu tip kullanımlar başlangıçta performans getirir gibi gözükse de zaman içinde ciddi sıkıntılara neden olurlar.

* Metod çağırırken kullanılan parametrelerde de ilkellik takıntısından kurtulmak gerekir. Parametreler anlamlı bir bütünü ifade ediyor ise değer nesneleri kullanılmalıdır. Aşağıdaki örnekteki tarih sınıfı aslında bir çok dilde primitive değildir. Fakat örnekte görüleceği üzere aktarılmak istenen bilgi için "ilkel" kalmıştır. Anlamı tek sınıfta toparlamakta fayda vardır.

  ```java
  amountInvoicedIn(Date start, Date end);
  amountReceviedIn(Date start, Date end);
  amountOverdueIn(Date start, Date end);

  amountInvoicedIn(DateRange range);
  amountReceviedIn(DateRange range);
  amountOverdueIn(DateRange range);
  ```


## Sonuç

Örneklerin hepsini bir arada düşündüğümüzde sorunun sadece dildeki primitive veri türlerini kullanma takıntısı olmadığını anlarız. Asıl sorun karmaşık bir bilgiyi görece ilkel veri türleri ile ifade etmeye çalışmaktır. Dediğimiz gibi. Çare değer nesneleri!