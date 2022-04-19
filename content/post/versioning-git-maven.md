+++
categories = ["Software","Java","TR"]
date = "2016-02-11T22:02:50+03:00"
title = "How to version a JavaEE Web application with Maven and Git"
keywords = ["Maven","Git","Versioning"]
weight = 1
+++
>In this post, I will show how to version a JavaEE Web Application.
The example project is [here](https://github.com/erkanerol/versioning_javaee_maven_git)

![screenshot](/img/screenshot.png)

<!--more-->


##### 1. Create a maven project and add your dependencies.

##### 2. Add your scm url's to pom.xml

```xml
<scm>
	<url>
		https://github.com/erkanerol/versioning_javaee_maven_git
	</url>
	<connection>
	   scm:git:git://github.com/erkanerol/versioning_javaee_maven_git.git
	</connection>
	<developerConnection>
		scm:git:git@github.com:erkanerol/versioning_javaee_maven_git.git
	</developerConnection>
</scm>
```

##### 3. Define time format in pom.
```xml
<properties>
		<maven.build.timestamp.format>
			dd.MM.yyyy_HH_mm
		</maven.build.timestamp.format>
		<buildDate>
			${maven.build.timestamp}
		</buildDate>
</properties>
```

##### 4. Add mojo plugin  in pom.xml

```xml
<plugin>
	<groupId>org.codehaus.mojo</groupId>
	<artifactId>buildnumber-maven-plugin</artifactId>
	<version>1.4</version>
	<executions>
		<execution>
			<phase>validate</phase>
			<goals>
				<goal>create</goal>
			</goals>
		</execution>
	</executions>
	<configuration>
		<shortRevisionLength>5</shortRevisionLength>
		<doCheck>false</doCheck>
		<doUpdate>false</doUpdate>
	</configuration>
</plugin>
```

##### 5. Add version.properties to your resource directory

```
version=${version}.${buildNumber}
versionLong=${version}.${buildNumber}.${buildDate}
```


##### 6. Add your resource folder in pom.xml

```xml
<resources>
	<resource>
		<directory>src/main/webapp</directory>
	</resource>
	<resource>
		<directory>src/main/resources</directory>
		<filtering>true</filtering>
	</resource>
</resources>
```

##### 7. Create an application scoped bean

```java

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

```


##### 8. Use the bean in your application.

```
<h:outputText value="version #{versionBean.version}" />
<h:outputText value="long version #{versionBean.versionLong}" />
```

## Recommendations

Use both long and short version in your application. Short version can be shown to users and long version can be shown in a spesific page like "/version.jsf".
If your repository is private, you have to put git password in your pom.xml. You can create a different, read-only account.
You can put the version in name of war file. It makes easy to store executable files.
