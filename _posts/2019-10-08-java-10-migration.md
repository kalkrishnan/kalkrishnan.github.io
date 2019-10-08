---
layout: post
title: "Java 8-10 migration"
description: ""
category: "Java"
---
Below are the steps I took to upgrade an existing legacy application(Voyager) to Java 10.

**1)** Setting up the environment:
 Download the jdk and jre for Java 10 and open up powershell and run the below commands to allow you to run with Java 10 without affecting your entire machine:
 
 `$Env:JAVA_HOME="C:\Program Files\Java\jdk-10.0.2"`
 `$Env:PATH="C:\Program Files\Java\jdk-10.0.2\bin;"+$Env:PATH`
 
**2)** One of the major changes in java 9/10 is the module system. The modules define the public API for any library and prevent access to internal classes. One of the main reasons for doing this was that a lot of legacy systems were built using internal jdk classes which made it very hard for the jdk to evolve and deprecate old APIs. The same concept can be used for products built with java 9 onwards which can be modularized where boundaries are clearly defined and it becomes easier to change internal APIs to handle changing needs. 

  The problems that arose for legacy applications with this change is obviously the need to change the code that uses internal jdk APIs. To anticipate the changes required, Java8 introduced a tool called jdeps(in JAVA_HOME/bin). Use this as below:

 `jdeps.exe --jdk-internals .\VoyagerJava-8.0.0.jar`

  which will produce an output similar to the below:


		VoyagerJava-8.0.0.jar -> java.base
		com.dhs.shared.util.EmailImpl                      -> com.sun.net.ssl.internal.ssl.Provider              JDK internal API (java.base)
		com.dhs.shared.util.MimeWQIMessagePreparatorImpl   -> com.sun.net.ssl.internal.ssl.Provider              JDK internal API (java.base)

		Warning: JDK internal APIs are unsupported and private to JDK implementation that are
		subject to be removed or changed incompatibly and could break your application.
		Please modify your code to eliminate dependence on any JDK internal APIs.
		For the most recent update on JDK internal API replacements, please check:
		https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

		JDK Internal API                         Suggested Replacement
		----------------                         ---------------------
		com.sun.net.ssl.internal.ssl.Provider    Use java.security.Security.getProvider(provider-name) @since 1.3


  The above tells us that two classes use the com.sun.net.ssl.internal.ssl.Provider internal API. The solution for the same is also provided. This was fixed as below:

 `Security.addProvider(Security.getProvider("SunJSSE"));`
	
  jdeps allows to obtain a lot of other information about the projects dependencies, but for the purpose of migration, this is the most important aspect.
	
**3)** Another tool provided by the JDK in Java 9 onwards is jdeprscan. This allows you to scan for deprecated API and plan for API that can get removed in future versions. To make this work accurately, the dependent jars of the project need to be provided in a separate folder accessible to the tool:

 `jdeprscan.exe --class-path ".\lib/*"  .\VoyagerJava-8.0.0.jar `

  This produces output similar to the below:
		
		
		Jar file .\VoyagerJava-8.0.0.jar:
		class com/dhs/billing/delinquency/mapper/DelinquencyMapper uses deprecated method java/lang/Long::<init>(J)V
		class com/dhs/billing/exports/directdebit/util/Formatter uses deprecated method java/lang/Float::<init>(F)V
		class com/dhs/billing/exports/prenote/util/Formatter uses deprecated method java/lang/Float::<init>(F)V
		class com/dhs/billing/premcalc/service/PremiumCalculationServiceImpl uses deprecated method java/math/BigDecimal::setScale(II)Ljava/math/BigDecimal;
		class com/dhs/enroll/imports/trr/service/MmpTrrServiceImpl uses deprecated method java/lang/Long::<init>(J)V
		class com/dhs/jdbc/shared/CrossReferenceDAOImpl uses deprecated method java/lang/Integer::<init>(Ljava/lang/String;)V
		class com/dhs/jdbc/shared/MemberDAOImpl$MemberHistoryLicsLevelRowMapper uses deprecated method java/lang/Double::<init>(D)V
		class com/dhs/jdbc/shared/MemberGroupMemberDAOImpl uses deprecated method java/lang/Integer::<init>(I)V
		class com/dhs/jdbc/shared/report/ReportDAOImpl uses deprecated method java/lang/Integer::<init>(I)V
		class com/dhs/jdbc/shared/WorkQueueDAOImpl uses deprecated method java/lang/Integer::<init>(I)V
		
 If we want to determine what might be removed in future releases:
 `jdeprscan.exe --class-path ".\lib/*" --for-removal .\VoyagerJava-8.0.0.jar`
 
**4)** Now we are ready to start the migration. The first step is to upgrade maven compiler plugins to work with Java 10:

    <plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.7.0</version>
				<configuration>
					<release>10</release>
				</configuration>
				<dependencies>
					<dependency>
						<groupId>org.ow2.asm</groupId>
						<artifactId>asm</artifactId>
						<version>6.2</version> 
					</dependency>
				</dependencies>
	</plugin>

 Let's try to compile now:
 
`D:\VoyagerGitLab\VoyagerFramework> mvn clean install "-Dmaven.test.skip=true"`

 This will produce errors like below:
 ```
 [ERROR] /D:/VoyagerGitLab/VoyagerFramework/src/com/dhs/framework/messaging/MessageHandlerRepository.java:[6,24] cannot find symbol
  symbol:   class Resource
  location: package javax.annotation
[ERROR] /D:/VoyagerGitLab/VoyagerFramework/src/com/dynamichealthsys/common/error/_1/VoyagerSoapFault.java:[4,17] package javax.xml.ws is not visible
  (package javax.xml.ws is declared in module java.xml.ws, which is not in the module graph)
  ```
  
  This points to modules in the jdk that are not accessible directly and need to be included. The jdk provides command line argument --add-modules for the same:
  
  ```
  <plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.7.0</version>
				<configuration>
					<release>10</release>
					<compilerArgs>
						<arg>--add-modules</arg>
						<arg>java.xml.ws</arg>
						<arg>--add-modules</arg>
						<arg>java.xml.ws.annotation</arg>
					</compilerArgs>
				</configuration>
  ```
  
  Now when we try to compile the jar it compiles successfully.
  
**5)** Though compilation might succeed, some errors are discovered during startup. If we build Voyager and start it now, we see the below during startup:

```
Caused by: org.springframework.beans.factory.BeanDefinitionStoreException: Unexpected exception parsing XML document from URL [jar:file:/C:/Users/kkrishnan/Software/apache-tomcat-9.0.10-windows-x64/ap
ache-tomcat-9.0.10/webapps/Voyager-8.0.0/WEB-INF/lib/VoyagerJava-8.0.0.jar!/applicationContext-webservice.xml]; nested exception is java.lang.NoClassDefFoundError: javax/xml/ws/WebServiceException
```

This is because certain classes need to be included through 3rd party jars and are not available in the jdk modules:

```
	<dependency>
    		<groupId>org.wso2.orbit.sun.xml.ws</groupId>
    		<artifactId>jaxws-ri</artifactId>
    		<version>2.3.0</version>
	</dependency>
```		

All Voyager jars need to be upgraded similarly and tested after startup. There might be other runtime issues which will have to be resolved similarly.

***Based on Spring documentation, the minimum supported version for Java 9/10 is Spring 5.0. However, based on stackoverflow and my testing, Spring 4.3 seems to be work on it. Spring 5.0 will be a major upgrade and will have to be deferred to a later time***

***Java 11 introduces more depcrecated APIs and the Spring supported version is 5.1. It also removed the command line option and all the modules have to be included using third party jars. This will also be deferred to a later release***