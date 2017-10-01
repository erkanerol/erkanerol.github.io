+++
date = "2017-10-01T19:00:00+03:00"
keywords = ["Distributed Systems"]
description = ""
title = "Dağıtık Sistemler: Ölçeklenebilirlik "
categories = ["Software", "Distributed Systems"]
weight=1

+++

***NOT:*** Bu sayfayı görüntülediğiniz için teşekkür ederim yalnız sizi asıl kaynak olan <a href="http://book.mixu.net/distsys/" target="_blank">şuraya</a> davet ediyorum. Benim yazdıklarım bu serinin tercümesi, yeniden ifade edilmesi ve birkaç örnek eklenmesidir o kadar.


<a href="/post/distributed-systems-1/" target="_blank">Önceki</a> yazımızda dağıtık sistemlere genel bir giriş yapmıştık. Bu yazımızda ölçeklenebilirlik üzerine eğileceğiz.

## Tanım

>Ölçeklenebilirlik, bir sistemin artan iş yüküne rağmen işlevselliğini düzgün bir şekilde devam ettirebilmesidir.  

<!--more-->

Örnek vermemiz gerekirse diyelim ki bir web siteniz var. Sitede anlık olarak gördüğünüz kullanıcı sayısı 100 olduğunda da, 1000 olduğunda da, 10000 olduğunda da kullanıcılar siteyi düzgün şekilde kullanabiliyor ve sitenin üzerinde çalıştığı sistemler buna adapte olacak şekilde büyüyebiliyor ise sitenin üzerindeki sisteme "ölçeklenebilir" diyebiliriz. 

## Peki hangi açılardan ölçeklenebilir?

Bir sistemin ölçeklenilebilirliğini farklı açılardan değerlendirebiliriz.

#### 1. Büyüklükte Ölçeklenebilirlik

Sisteme yeni makinalar ekledikçe sistemin işlevselliğinde bozulma olmuyor ve sistem güç kazanıyorsa büyüklükte ölçeklenebilir bir sistemdir diyebiliriz. Aksi bir örnek vererek neyi kastettiğimizi açıklayalım. Diyelim ki bir mesajlaşma uygulaması yazdık. 3 makinada dağıtık şekilde çalışırken sorunsuz çalışıyor. Bir kullanıcı bir mesajı bir makinaya attığında diğer bir kullanıcı başka bir makina üzerinden bunu 10ms içinde okuyabiliyor ki bu süre bir insan için farkedilebilecek bir süre bile değil. Ancak 30 makinaya çıkardığımızda bir makinaya atılan mesaj bir diğer makinadan ancak 3sn sonra okunabiliyor ise uygulamamızın kullanımında ciddi problemler ortaya çıkmaya başlar. Bu durumda bu sistem büyüklük açısından ölçeklenebilir değildir.

Artan yüke karşı cevap verebilen sistemler inşa etmek için sistemlerimizin büyüklükte ölçeklenebilir olması şarttır. Burada farkettiğiniz üzere büyüklük ile gecikme arasında bir orantı vardır. Bu dağıtık sistemlerin başlıca sorunlarından biridir. İlerleyen yazılarda bu konu üzerine ayrıca eğileceğiz.

#### 2. Coğrafi Ölçeklenebilirlik

Sistemin coğrafi olarak dağıtılabilirliği onun coğrafi ölçeklenebilirliğini ifade eder. Yine bir örnek vererek açıklamaya çalışalım. Diyelim ki çok yoğun kullanılan bir sosyal medya platformumuz var ve dünyanın her yerinden kullanıcılarımız var. Hızlı bir hizmet verebilmek adına bütün sunucularımızı Amerika'da tutmak yerine dünyanın çeşitli noktalarına dağıtmak isteriz. Fakat sistemi coğrafi olarak dağıttıkça bazı sorunlarla karşılaşmamız muhtemeldir. Önceden aynı ağ üzerinde çalışan makinalar ağ içinde çok hızlı iletişim kurup, senkronize olabilirken artık mesafeler uzadıkça senkronize olmaları zorlaşır. Türkiye'den atılan bir tweet'in Çin'den ancak 1 dakika sonra okunabiliyor olması kabul edilebilir değildir. İşte bu noktada "coğrafi ölçeklenebilirlik" kavramı anlam kazanır. Çok geniş coğrafyalarda, yüksek miktarlarda isteğe hızlı ve doğru yanıt dönebilmemiz için sistemlerimizin coğrafi ölçeklenebilir olması şarttır.

#### 3. Yönetimsel Ölçeklenebilirlik

Dağıtık sistemlerde sistem büyüdükçe sistemin yönetim maaliyetinin çok fazla artmaması gerekir. İdeal olan, sistemin büyüklüğünün yönetenler açısından basit bir parametre olmasıdır. Şöyle ifade edelim: Dağıtık sistemimizi ayağa kaldırıyoruz. Bir parametre var. İstersek sistem 10 makina ile kalkıyor, istersek 100 makina ile. Arada bir miktar süre farkı olabilir. Bu sistem yönetimsel açıdan ölçeklenebilirdir diyebiliriz. Ancak 100 makina çalıştırmak istediğimizde bu makinalara tek tek konfigürasyon yapmamız gerekiyor ve yönetim maaliyeti her bir makina için ciddi miktarda artıyorsa bu sistem yönetim açısından ölçeklenebilir değildir.

Canlı sistemlerin artan/azalan yük ihtiyacına göre adaptive bir şekilde kendi büyüklüğünü değiştirebilmesi için yönetimsel açıdan ölçeklenilebilirlik kritik önem arz eder.

## Kapanış

Bu yazıda ölçeklenilebilirlik üzerinde durduk. Önümüzdeki yazılarda ölçeklenen dağıtık sistemlerde neleri arttırmaya, neleri makul miktarlarda tutmaya, neleri azaltmaya çalışmalıyız konularına değineceğiz.