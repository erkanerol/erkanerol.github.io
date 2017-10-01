+++
date = "2017-10-01T17:00:00+03:00"
keywords = ["Distributed Systems"]
description = "Dağıtık Sistemlere Giriş"
title = "Dağıtık Sistemler (Distributed Systems): Giriş "
categories = ["Software", "Distributed Systems"]
weight=1

+++

Önümüzdeki süreçte "Distributed Systems" üzerine çalışmayı planlıyorum. Bu konuda okumalar yapmaya başladım. Okuduklarımı blog yazılarına dökersem daha iyi anlayacağımı düşündüğüm için işbu blog serisini başlatmış bulunmaktayım. Bu sayfayı görüntülediğiniz için teşekkür ederim yalnız sizi asıl kaynak olan <a href="http://book.mixu.net/distsys/" target="_blank">şuraya</a> davet ediyorum. Benim yazdıklarım bu serinin tercümesi, yeniden ifade edilmesi ve birkaç örnek eklenmesidir o kadar.

## Nedir bu dağıtık sistemler?

Bütün bilgisayar sistemlerinde temelde iki iş yaparız: Veri saklama (storage) ve hesaplama (computation). Ve günün sonunda hedefimiz bir problemi çözmektir. Bazen bu problemin doğası ve/veya büyüklüğü bizi birden fazla bilgisayar kullanmaya zorlar. İşte bu noktada "dağıtık programlama" devreye girer.

>Dağıtık programlama, bir bilgisayar ile çözebileceğiniz bir sorunu birden fazla bilgisayar kullanarak çözmeye çalışma sanatıdır.

<br></br>

<!--more-->
## Neden ihtiyaç var?

Burada akla ilk gelen soru "Madem bir bilgisayarla çözebiliyorduk neden birden fazla bilgisayar kullanıyoruz?" olacaktır. Aslında elimizde sonsuz işlem yapma gücünde, sonsuz veri saklama kapasitesinde ve Dünya'nın her noktasından hızlıca erişilebilir bir bilgisayar olsaydı dağıtık sistemler kurmaya gerek kalmazdı. Fakat böyle bir bilgisayar yok. Bizim işimizi görecek kadarını yapsak bize yeter diye düşünebilirsiniz. Orada da şöyle bir sorun devreye giriyor: Dağıtık sistemler kurmak yerine elimizdeki donanımların özelliklerini arttırdıkça bu donanımların maaliyetleri aşırı yükseliyor. Aşağıdaki görselde de görüleceği üzere makina sayısını arttırarak yatayda ölçekleme yapmanın (horizontal scaling/scale out) maaliyeti lineer artarken, makinanın özelliklerini artırarak dikeyde ölçekleme yapmanın (vertical scaling/scale up) maaliyeti üssel şekilde artıyor.

<img src="/img/ds/ScaleUpVsScaleOut.jpg"/>

Kaldı ki donanım öğelerinin de fiziksel limitleri ve bozulma ihtimali var. Milyon dolarlar harcadığınız bir sistemin tek bir donanım öğesinin (disk, memory, cpu vb.) bozulmasıyla çalışamaz hale gelmesini kabul eder miydiniz? Edenleri <a href="http://www.foratoys.com/webkontrol/urun_yonetimi/urunresimalt/urunresimalt_13_06_2013_11_16_501.jpg" target="_blank">şuraya</a> alalım. Etmeyenlerle "Nasıl şu dağıtık sistemleri kurarız?" derdine düşelim hep beraber.

<br></br>
##  Para Çokomel Eğrisi

1 liraya 1 çokomel alabiliyorum. 2 liraya 2 tane alabilirim. Hatta toplu alsam belki 20 liraya 25 tane alabilirim. Peki bu dağıtık sistemlerde de böyle mi? 1 makina ile 100 birim iş yapabiliyor isem 10 makina ile 1000 işlem yapabilir miyim? 

Dağıtık sistemlerde makina adeti ile toplam işlem gücü arasındaki ilişki lineer değil maalesef. Çünkü sisteme eklenen her bir öğe sistemin karmaşıklığını arttırır ve beraberinde iletişim maaliyetleri getirir. Dolayısıyla 10 makina ile 1000 işlem yapmanız neredeyse olanaksızdır. 900 filan olursa öpüp başınıza koyun derim :)

<br></br>
## Yüksek özellikli makinalar her zaman verimli mi?

<img src="/img/ds/barroso_holzle.png"/>

Yazıyı bitirirken yukarıdaki grafiği de açıklamak isterim. Barroso, Clidaras ve Hölzle isimli 3 Google çalışanı bir <a href="http://www.morganclaypool.com/doi/abs/10.2200/S00516ED2V01Y201306CAC024" target="_blank">araştırma</a> yapıyor. Araştırmada şöyle bir soru mevcut: 4 çekirdekli sıradan makinalarla mı çalışsak daha performanslı olur, yoksa high-end dedikleri 128 çekirdekli makinalarla mı çalışsak daha iyi olur? Kurulan kümenin (cluster) boyutu küçükken (toplamda 512 çekirdekli iken mesela) 128'lik makinalı küme çok daha iyi. Çünkü iletişimin büyük bölümü makina içlerinde ve tahmin edersiniz ki belleğe ulaşmak (memory access) ağ üzerinden haberleşmeye göre oldukça ucuz/hızlı. Fakat kümenin boyutu büyüdükçe bu performans artışı gitgide azalıyor. Sonlara doğru %1-2 düzeylerinde gördüğünüz üzere. Yani dağıtık sistemlerde ölçek büyüdükçe sistem üzerindeki birimlerin büyüklüklerinin önemi ciddi ölçüde azalıyor. Problemi&çözümü kümeye nasıl dağıttığımıza kalıyor bütün mesele.

<br></br>
## Kapanış

Bu yazıda dağıtık sistemler üzerine genel bir bakış attık. Önümüzdeki yazılarda hedeflerden söz edeceğiz.