+++
date = "2016-05-25T22:46:50+03:00"
title = "YAGNI Prensibi"
tags = ["Turkish", "Software", "Yazılım Prensipleri"]
+++

Not: Bu yazı Martin Fowler'ın YAGNI yazısının özeti ve tercümesi niteliğindedir.

## Programlama Prensibi

YAGNI

## Açılımlar

You Aren’t Gonna Need It
You Aint Gonna Need It

## Açıklama

YAGNI, Extreme programlamanın prensiplerinden biridir. Gelecekte lazım olacak düşüncesi ile bir takım özelliklerin geliştirilmemesi gerektiğini savunur. Şimdi bir örnek üzerinden bu prensibi anlamaya çalışalım.

<!--more-->

Diyelim ki projemize bir "oturum açma" özelliği ekliyoruz. O anda bizden istenen şey kullanıcıların kullanıcı adı ve şifreleri ile giriş yapabilmelerini sağlayan bir özellik. Fakat biz "ilerde lazım olur" düşüncesi ile facebook ile de oturum açılmasını sağlayabilecek şekilde geliştiriyoruz arka taraftaki methodlarımızı. İşte tam bu noktada YAGNI diyor ki "YAPMA".  Peki neden?

Yazılımcıların o an kullanmayacakları özellikleri geliştirmesinin en yaygın sebebi o özelliği o an geliştirmenin daha ucuz olacağını düşünmeleridir. Örneğimizden gidecek olursak geliştirici şöyle düşünür: "Şimdiden ben arka tarafta facebook entegrasyonunu yapayım. Yarın bir gün zaten istenecek bu benden. O zaman bu yazdıklarımı değiştirmem gerekmez." Fakat bu özellik yazıldığı an itibari ile "varsayımsal özellik" kategorisindedir ve hiç bir zaman sizden talep edilmeyecek olabilir. Bu noktada bazıları bunun planlama olduğunu ve iyi analizler sonucu bu tip geliştirmeler yapılabileceğini iddia edebilir. Ne var ki gerçek hayat tecrübeleri bunun çok uzağındadır ve geliştiriciler yanlış öngördükleri varsayımsal özelliklerin geliştirme maliyetine katlanmak zorunda kalır. Bu özellik için harcanan analiz, programlama ve test etme süreleri boşa harcanmış olur.

Diyelim ki biz doğru tahmin ettik ve o özellik gerçekten bize lazım olacaktı. Bu durumda bile bu varsayımsal özelliğin bize getirdiği ciddi maliyetler vardır. Birincisi biz gelecekte lazım olacağını düşündüğümüz bu özelliği yaparken bugün yapmamız gereken işlerin bir kısmını geciktiririz. Örneğin, oturum açma özelliğinden sonra bir arama özelliği eklememiz gerekiyordu ve biz facebook entegrasyonunu da yaptığımız için bu özellik 2 gün gecikti. Yani varsayımsal bu özellik bize gecikme maliyeti getirdi. Yapılan araştırmalara göre geliştirilen özelliklerin ancak 1/3'ünün gerçekten faydalı olduğu da düşünülürse hali hazırda gerekli olan bir özelliği varsayımlar üzerine geciktirmek yeniden düşünülmesi gereken bir eylemdir.

Doğru tahmin ettiğimiz durumdaki tek maliyet geciktirme maliyeti değildir. Taşıma maliyeti de en önemli sorunlardan biridir. Biz gelecekte işe yarayacağımız bu özelliğin kodunu projemizde taşımak zorunda kalırız. Bütün değişikliklerde kodun o kısmını da değiştirmemiz gerekir. O an hiç kullanmadığımız bu kod parçacığı hatalara sebep olabilir. Debug sırasında o kısmı da debug etmemiz gerekir. Özetle projeye eklenen her bir satır kodun bedellerini bu özellik için eklenen satırlar için de öderiz. Halbuki bu özelliği sonradan projeye ekleseydik bu süre içinde bu maliyete katlanmamız gerekmeyecekti.

Şu ana kadar varsayımımızın doğru ve yanlış olduğu durumları ele aldık. Fakat daha gerçekçi olan olasılık tahminimizin doğru olması ama geliştirmenin tam doğru olmamasıdır. Yazılımcıların sürekli öğrendiğini ve gereksinimlerin sürekli değiştiğini düşünürsek eklediğimiz bu özelliği ilerleyen dönemlerde kullanmak istesek bile bazı değişikler yapmamız gerekir. Örneğin facabook API'da bir değişiklik yapmış olabilir. Kullandığımız kütüphaneden daha güzel bir kütüphane çıkmış olabilir ve onu değiştirmek isteyebiliriz. Bu durumlarda da bakım maliyetine katlanmak zorunda kalırız. Geliştirdiğimizi düşündüğümüz özelliği projeye gerçekten dahil etmek için fazladan efor harcarız.

Aşağıda şu ana kadar anlattıklarımı özetleyen bir grafik mevcut.

![YAGNI](/img/yagni.png)

## Ölçü

YAGNI prensibinin ne zaman geçerli olduğu konusu biraz göreceli fakat kısaca şöyle denebilir: Varsaydığımız özelliğin büyüklüğü arttıkça YAGNI prensibinin önemi artar. 1-2 saatlik bir iş yapılacaksa ve bu ilerde bir kaç günlük bir geliştirmeyi karşılayacaksa bunda çok sakınca olmayabilir fakat bu süre uzadıkça riskleriniz artar.

Ayrıca YAGNI kodun kolay değiştirilebilecek şekilde yazılmaması anlamına gelmez. Aksine YAGNI bir Extreme Programming prensibidir ve XP'nin en önemli pratiklerinden biri Refactoring'dir. XP kodun kolay değiştirilebilir olmasını öğütler. Bol bol refactoring yaparak kodumuzu temiz tuttuğumuzda, methodlarımızı parametrik yazdığımızda zaten özellikleri sonradan eklemek bize çok maliyet getirmez. Bu anlamda YAGNI esnek kod yazma ile çatışmaz, uyum içerisindedir.

## Kaynakça

1. http://martinfowler.com/bliki/Yagni.html
+ http://webpro.github.io/programming-principles/#yagni
+ http://www.extremeprogramming.org/rules/early.html
+ http://c2.com/xp/YouArentGonnaNeedIt.html

