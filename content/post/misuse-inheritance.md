+++
categories = ["Software"]
date = "2016-11-23T23:11:42+03:00"
description = ""
keywords = []
title = "Kalıtım Kullanımına Dair Bir Aydınlanma Anı"

+++

Geçenlerde "Composition over Inheritance" ile alakalı bazı okumalar yapmaya karar verdim. Bu prensibi duymuştum ve genel olarak anlamıştım ama biraz derinlere inmek istedim. Yaptığım google aramalarında <a href="https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose">şu</a> makaleye denk geldim. Gerçekten güzel bir makale, herkese tavsiye ederim. Makaleyi okurken bir aydınlanma yaşadım ve paylaşmak istedim.

<!--more-->

Makalede geçen iki kod parçasına bir an bakalım. 

<pre><code>
class Stack extends ArrayList {
    public void push(Object value) { … }
    public Object pop() { … }
}
</code></pre>


<pre><code>
class CustomerGroup extends ArrayList&lt;Customer&gt; {

}
</code></pre>

Açıkçası kodu ben yazsaydım zaten böyle yazmazdım. Fakat yine de bu şekilde yazmanın neden yanlış olduğuna hiç kafa yormamıştım. 

Buradaki sorularımız şunlar:

1. Stack, ArrayList'in uygun bir alt tipi midir? (bkz: Liskov Substitution Principle)
2. CustomerGroup dediğimiz sınıfın domain'i ile ArrayList'in domain'i aynı mıdır? 

Ayrıntılar için makaleyi okumanızı şiddetle öneririm. Yazar gayet güzel anlatmış. Benim için aydınlanma olan kısmı "cross-domain inheritance relationship" olarak ifade edilen kısımdı. Okuduğum kodlardan gelen ezberlerim vardı ama zihnimde bu ayrımı bu kadar net yapmamıştım hiç. 

Linki yeniden <a href="https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose">veriyor</a>  ve iyi okumalar diliyorum.