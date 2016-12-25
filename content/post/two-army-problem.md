+++
categories = ["Software"]
date = "2015-08-24T20:31:00+02:00"
title = "2 General/Ordu Problemi"
keywords = ["2 General Problemi", "2 Ordu Problemi", "The Two Army Problem"]
+++

Bu yazıda Two Generals' Problem ya da The Two Army Problem olarak anılan problemi anlatmaya çalışacağım.

<!--more-->


Ortada bir savaş var. A1 ve A2 orduları B ordusuna karşı savaşıyor fakat resimdeki gibi ayrı düşmüşler. Ancak elçiler vasıtası ile iletişim kurulabiliyor. Bu iki ordu aynı anda B ordusuna hücum etmek istiyor fakat kesin bir şekilde anlaşmaları lazım. Aksi halde yenilebilirler.


<img src="/img/two-army.png" title="Two Army"/>

Problemimiz şu: Bu iki ordu elçiler göndererek bir mütabakata varabilirler mi? Anlaştıklarından %100 emin olabilirler mi?

Şimdi senaryoları düşünelim.

### Senaryo 1

A1 ordusu taaruzun günü ve saatini elçi vasıtası ile A2 ordusuna iletmek istiyor. Bir elçi gönderiliyor. Elçi dönmüyor. O gün ve saat gelince A1 ordusu hücum eder mi? Etmez. Çünkü elçi yolda ölmüş olabilir ya da A2 ordusu kabul etmeyip elçiyi öldürmüş olabilir.

### Senaryo 2

Elçi A2 ordusuna isteği iletiyor. A2 ordusu kabul ediyor. Elçi bu haberi göndermek için geri yola çıkıyor. A2 ordusu belirtilen gün ve saat gelince hücum eder mi? Etmez. Çünkü elçi geri dönerken ölmüş olabilir. O zaman A1 ordusu hücum etmiycektir ve A2 ordusu yalnız kalacaktır. O nedenle A2 ordusu mesajın geri iletildiğinden emin olmalı.

### Senaryo 3

Elçi gidip teklifi A2'ye iletiyor. Kabul mesajını dönüp A1'e iletiyor. Sonra yeniden gidip A2 ordusuna kabul mesajının iletildiğini iletiyor. Fakat A1 ordusu bu durumda emin olamaz çünkü elçinin ikinci seferde de ulaştığından emin olmalı. Çünkü aksi halde A2 ordusu hücum etmiycektir.

### Sonuç

Senaryolarda da görüldüğü gibi elçinin tur sayısı ne kadar arttırılırsa arttırılsın iki taraf kesin bir şekilde emin olamaz.

### TCP Protokolü ile alakası

TCP protokolünde bilgisayarların IP paketleri vasıtası ile bağlantılarının kapatılması konusunda anlaşmaya çalışmasında da aynı problem yaşanmakta. Sorun timeout kullanarak çözülüyor. Eğer karşı tarafın cevabı belirli bir süre içinde gelmezse bağlantı kapatılıyor.
