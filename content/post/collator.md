+++
categories = ["Software","Java"]
date = "2016-01-09T21:56:21+03:00"
title = "Farklı dillerin alfabelerine göre sıralama (Collator kullanımı)"
keywords = ["Java","Collator","Dil","Sıralama"]
weight = 1
+++
>Java'da farklı dillerin alfabelerine göre sıralama yapmak mümkün.

[Github Repo](https://github.com/erkanerol/Examples/blob/master/src/com/erkanerol/examples/collator/CollatorExample.java)
<!--more-->
 

<pre>
<code class="language-java">
package com.erkanerol.examples.collator;

import java.text.Collator;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.Locale;

public class CollatorExample {

	public static void main(String[] args) {
		
		ArrayList&lt;City&gt; list = new ArrayList&lt;City&gt;();
		list.add(new City("London"));
		list.add(new City("Newyork"));
		list.add(new City("Ankara"));
		list.add(new City("Paris"));
		list.add(new City("Venice"));
		list.add(new City("Çankırı"));

		Comparator&lt;City&gt; compDef = new Comparator&lt;CollatorExample.City&gt;() {
			@Override
			public int compare(City c0, City c1) {
				return c0.getName().compareTo(c1.getName());
			}
		};
		
		Comparator&lt;City&gt; compTr = new Comparator&lt;CollatorExample.City&gt;() {
			@Override
			public int compare(City c0, City c1) {
				Collator collator = Collator.getInstance(new Locale("tr","TR"));
				return collator.compare(c0.getName(), c1.getName());
			}
		};
		
		Collections.sort(list,compDef);
		printList(list);
		Collections.sort(list,compTr);
		printList(list);
	}
	
	public static void printList(List&lt;City&gt; list){
		for(City c:list){
			System.out.println(c);
		}
		System.out.println("nn");
	}
	
	
	public static class City{
		private String name;
		
		public City(String name){
			this.name = name;					
		}		
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
		@Override
		public String toString(){
			return this.name;
		}
	}
}
</code>
</pre>

## Console

Ankara

London

Newyork

Paris

Venice

Çankırı

<br></br>

Ankara

Çankırı

London

Newyork

Paris

Venice
