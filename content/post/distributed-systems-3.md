+++
date = "2017-10-04T22:30:00+03:00"
keywords = ["Distributed Systems"]
description = ""
title = "Dağıtık Sistemler: Performans ve Gecikme (Latency)"
categories = ["Software", "Distributed Systems"]
weight=1
draft = true
+++

***NOT 1:*** Bu sayfayı görüntülediğiniz için teşekkür ederim yalnız sizi asıl kaynak olan <a href="http://book.mixu.net/distsys/" target="_blank">şuraya</a> davet ediyorum. Benim yazdıklarım bu serinin tercümesi, yeniden ifade edilmesi ve birkaç örnek eklenmesidir o kadar.

***NOT 2:*** Bu blogda yer alan bu konu hakkındaki bütün içeriğe <a href="/post/distributed-systems-0/" target="_blank">şuradan</a> erişebilirsiniz.

## Perfomans nedir?

>Bir bilgisayar sisteminin tükettiği kaynaklara ve zamana kıyasla çıkardığı faydalı iş miktarı o sistemin performansını tanımlar.

Performanslı sistemlerden

* Cevapları kısa sürede üretebilmesini
* Verilen iş için düşük gecikme miktarlarına sahip olmasını
* Birim zamanda yaptığı iş sayısının fazla olmasını
* Kaynakları az kullanmasını

bekleriz.

<!--more-->

Ancak bu hedefler arasında tradeoff'lar vardır. Yani bir parametrede sistemi iyileştirmeye çalıştıkça diğerinden tavizler verirsiniz. Söz gelimi birim zamanda çok iş yapmak isterseniz kaynakları çok kullanmaya başlarsınız. Veya yapılan iş miktarını arttırmak için işleri tek tek değil, grup (batch) olarak yaparsanız, bir iş için gecikme süresi artacaktır

## Gecikmenin tanımı

> Bir değişikliğin gerçekleşme anından sistemde kendini gösterdiği ana kadar olan periyota gecikme diyoruz. 

Gecikme kavramını örnek üzerinden açıklamaya çalışalım. Diyelim ki bir Java uygulamamız var ve 100 instance ile çalışıyor. Uygulamamızda Hazelcast'in Distributed Hash Map'ini kullandık. Yani uygulamanın bir instance'ından put/delete yaptığımızda, dağıtık sistemdeki diğer instance'lardan bu değişikliği görebiliyoruz. Sanki ortada tek bir map varmış gibi çalışabiliyoruz. Bu sistemde bir yerden yapılan bir put işleminin bilgisi mesajlar yoluyla diğerlerine dağıtılır ve bu mesajı alan herkes ilgili değişikliği kendi elindeki veri üzerinde gerçekleştirir. Ancak bu işlemlerden sonra bu değişikliği diğer instance'lar üzerinde görebiliriz. İşte arada geçen bu süreye gecikme (latency) diyoruz. 



