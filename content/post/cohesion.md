+++
categories = ["Software","Kavramlar"]
date = "2016-09-29T22:27:32+03:00"
description = ""
keywords = []
title = "Yazılım bağlamında 'Kohezyon'"
draft=true
+++

Not: Bu yazı büyük oranda Sandro Mancuso'nun <a href="https://codurance.com/software-creation/2016/03/03/cohesion-cornerstone-software-design/">şu</a> yazısının çeviri niteliğindedir.

## Giriş ve Tarihçe

Kohezyon yazılım tasarımında önemli kavramlardan biridir. Bir çok tasarım prensibinin ve kalıbının çekirdeğini oluşturur. Kohezyon kavramı ilk kez <a href="https://en.wikipedia.org/wiki/Larry_Constantine">Larry Constantine</a> tarafından 60ların sonlarına doğru <a href="http://www.cs.toronto.edu/~penny/teaching/csc407-02s/lectures/04structured-design.pdf">Structured Design</a>'ın bir parçası olarak ortaya konmuş, sonraları ayrıntılı olarak W. Stevens ve  G. Myers'ın da katılımıyla 1974 yılında yayınlanmış. 60larda,70lerde,80lerde yazılımın karmaşıklığının ve maliyetinin artması yazılım tasarımı ve sürdürülebilirlik (bakım) üzerine bir çok çalışmayı ve araştırmayı beraberinde getirmiş. Bugün hala bu çalışmaların bir kısmına erişebiliyor olsak da bu çalışmaların çoğu ya kaybolmuş ya da kolayca erişilebilir durumda değiller. 

<br/>
## Tanım

*Sözlük Tanımı*

İsim hali: Bir grup ya da topluluğun üyelerinin bir arada olması

Sıfat hali: Birleşik ve beraber etkili bir şekilde çalışan, yapışkan, bağlı,yapışık

*Yazılım Bağlamındaki Tanımı*

Bir yazılım modülünün çeşitli sorumluluklarının ne kadar odaklı olduğuna ve güçlü ilişkilileri olduğuna dair ölçüye **kohezyon** denir.



## Grinin Elli Tonu
Bir modülün sorumlulukları arasında kohezyon ya vardır ya da yoktur diye düşünmek sıkça yapılan hatalardan biridir. Bu nitelik ikili (binary) olmaktan çok bir skala içinde çeşitli değerler alabilen bir özelliktedir. Yani siyah yada beyaz olmak zorunda değil, grinin çeşitli tonlarında da olabilir. 70lerin başlarındaki orijinal çalışmada 7 seviye kohezyon tanımlanmış ve bunlar sonraları SMS Cohesion olarak bilinegelmiş.

Orijinal çalışmalar çok akademik olduğundan dolayı burada kavramları basitleştirelim.

1. Modül: nesneler(classes) ve fonksiyon grupları.
* İşlem elemanları: metodlar ya da fonksiyonlar

Önceden de söylediğimiz gibi kohezyon kabaca bir nesnenin(modül) metodlarının(işlem elemanları) birbirleriyle ne kadar ilişkili olduğudur.

## Kohezyonun Yedi Tonu

* **Tesadüfi**: İşlem elemanları keyfi olarak gruplanmıştır ve birbirleriyle belirgin bir ilişkileri yoktur. Örneğin: müşteri kaydını güncelleme, rapor yazdırma, borç hesaplama. Bu tip Utils veya Helpers adı verilen her derde deva nesnelerde sıklıkla görülür.

* **Mantıksal**:
* Geçici
* Prosedürel (Usuli)
* İletişimsel
* Ardışık
* Fonksiyonel (**EN İYİSİ**)