+++
categories = ["Software","Java"]
date = "2016-02-11T22:02:50+03:00"
title = "How to version a JavaEE Web application with Maven and Git"
keywords = ["Maven","Git","Versioning"]
weight = 1
+++
>In this post, I will show how to version a JavaEE Web Application.
The example project is [here](https://github.com/erkanerol/versioning_javaee_maven_git)

<img src="/img/screenshot.png" />

<!--more-->


##### 1. Create a maven project and add your dependencies.
##### 2. Add your scm url's to pom.xml

<pre><code class="xml">
&lt;scm&gt;
	&lt;url&gt;
		https://github.com/erkanerol/versioning_javaee_maven_git
	&lt;/url&gt;
	&lt;connection&gt;
	   scm:git:git://github.com/erkanerol/versioning_javaee_maven_git.git
	&lt;/connection&gt;
	&lt;developerConnection&gt;
		scm:git:git@github.com:erkanerol/versioning_javaee_maven_git.git
	&lt;/developerConnection&gt;
&lt;/scm&gt;
</code></pre>

##### 3. Define time format in pom.
<pre><code class="xml">
&lt;properties&gt;
		&lt;maven.build.timestamp.format&gt;
			dd.MM.yyyy_HH_mm
		&lt;/maven.build.timestamp.format&gt;
		&lt;buildDate&gt;
			${maven.build.timestamp}
		&lt;/buildDate&gt;
&lt;/properties&gt;
</code></pre>

##### 4. Add mojo plugin  in pom.xml

<pre><code class="xml">
&lt;plugin&gt;
	&lt;groupId&gt;org.codehaus.mojo&lt;/groupId&gt;
	&lt;artifactId&gt;buildnumber-maven-plugin&lt;/artifactId&gt;
	&lt;version&gt;1.4&lt;/version&gt;
	&lt;executions&gt;
		&lt;execution&gt;
			&lt;phase&gt;validate&lt;/phase&gt;
			&lt;goals&gt;
				&lt;goal&gt;create&lt;/goal&gt;
			&lt;/goals&gt;
		&lt;/execution&gt;
	&lt;/executions&gt;
	&lt;configuration&gt;
		&lt;shortRevisionLength&gt;5&lt;/shortRevisionLength&gt;
		&lt;doCheck&gt;false&lt;/doCheck&gt;
		&lt;doUpdate&gt;false&lt;/doUpdate&gt;
	&lt;/configuration&gt;
&lt;/plugin&gt;
</code></pre>

##### 5. Add version.properties to your resource directory

<pre><code>
version=${version}.${buildNumber}
versionLong=${version}.${buildNumber}.${buildDate}
</code></pre>


##### 6. Add your resource folder in pom.xml

<pre><code class="xml">
&lt;resources&gt;
	&lt;resource&gt;
		&lt;directory&gt;src/main/webapp&lt;/directory&gt;
	&lt;/resource&gt;
	&lt;resource&gt;
		&lt;directory&gt;src/main/resources&lt;/directory&gt;
		&lt;filtering&gt;true&lt;/filtering&gt;
	&lt;/resource&gt;
&lt;/resources&gt;
</code></pre>

##### 7. Create an application scoped bean

<pre><code class="java">

package com.erkanerol.example.versioning;

import java.io.IOException;
import java.io.Serializable;
import java.util.Properties;

import javax.annotation.PostConstruct;
import javax.faces.bean.ApplicationScoped;
import javax.faces.bean.ManagedBean;


@ManagedBean
@ApplicationScoped
public class VersionBean implements Serializable {

	private String version;
	private String versionLong;
	
	@PostConstruct
	public void init(){
		
		try {
			Properties properties = new Properties();
			properties
				.load(this
					.getClass()
					.getClassLoader()
					.getResourceAsStream("version.properties"));
		
			version = properties.getProperty("version");
			versionLong = properties.getProperty("versionLong");
			
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	}
		
	public String getVersion() {
		return version;
	}
	public void setVersion(String version) {
		this.version = version;
	}
	public String getVersionLong() {
		return versionLong;
	}
	public void setVersionLong(String versionLong) {
		this.versionLong = versionLong;
	}	
}

</code></pre>


##### 8. Use the bean in your application.

<pre><code>
&lt;h:outputText value="version #{versionBean.version}" /&gt;
&lt;h:outputText value="long version #{versionBean.versionLong}" /&gt;
</code></pre>

## Recommendations

Use both long and short version in your application. Short version can be shown to users and long version can be shown in a spesific page like "/version.jsf".
If your repository is private, you have to put git password in your pom.xml. You can create a different, read-only account.
You can put the version in name of war file. It makes easy to store executable files.
