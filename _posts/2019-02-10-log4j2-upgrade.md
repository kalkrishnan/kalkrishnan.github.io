---
layout: post
title: "Upgrading to log4j2"
description: ""
category: "Java"
---

Logging is a critical part of any application. Without proper logging, teams resort to debugging in Production to diagnoze mysterious issues, Yikes!! I have been there and continue to live in that hell with legacy software which we all have to work with at some point. 

Great, now everyone is on board with logging! Let's fix this debugging in Production issue by adding logging in all our methods after every variable is set. Having all the information will fix everything right? -_____-. Pretty soon we realize that our excessive need for information and logging is bringing our application down. All that I/O is not cheap. Logging as with all of software development is something that has to be tweaked according to our needs as the code base grows. Too little or too much can have serious consequences. 

Say we have tweaked our logging needs to log the exact amount of information with all the different levels configurable for different needs. Since we are working on a Java application, we use the widely used log4 framework. Everything goes great till we scale to process 10x the load we tested for. We scramble to make our code handle the load but we still run into mysterious bottlenecks. You got it, its logging again! Log4j-1-x logs synchronously, so as we process enormous loads, all that synchronous I/O eventually catches up and slows the application down. Logging is important but it is not critical enough to be the bottleneck. This brings us to Log4j2 and asynch loggers. 

Log4j2 uses asynch loggers which use the [LMAX disruptor](https://lmax-exchange.github.io/disruptor/) to log asynchronously and performs ridiculously better than log4j1. [Please read this for more information on how and why it is so much faster](https://grobmeier.solutions/log4j-2-performance-close-to-insane-20072013.html). 

Here are the steps I followed to upgrade from log4j1 to log4j2:

* Add the below entries to your pom. For log4j2 versions greater than 2.8, use disruptor version greater than 3.3:

{% highlight java %}
		<dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>com.lmax</groupId>
            <artifactId>disruptor</artifactId>
            <version>${disruptor.version}</version>
        </dependency>

{% endhighlight %}

* Since log4j2 changed the logger package from org.apache.log4j.Logger to org.apache.log4j.logging.Logger, I wanted to write a wrapper around this so I did not have to change thousands of class files next time the package changed:

{% highlight java %}
public class CustomLogManager extends LogManager {

	public static CustomLogger getCustomLogger(Class<?> clazz)
	{
		Logger logger = getLogger(clazz);
		return new CustomLogger((org.apache.logging.log4j.core.LoggerContext)getContext(), logger.getName(), logger.getMessageFactory());
	}
}

public class CustomLogger extends org.apache.logging.log4j.core.Logger{

	protected CustomLogger(LoggerContext context, String name, MessageFactory messageFactory) {
		super(context, name, messageFactory);
	}

}

{% endhighlight %}

* Create the below log4j2.xml to configure logging. I have mixed asynch and synch loggers here, but you can just set all loggers to asynch with the following system property:


{% highlight java %}
log4j2.contextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
{% endhighlight %}

{% highlight java %}
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
       <RollingFile name="RollingFileAppender" fileName="d:/logs/WebApplog4j2.log"
      filePattern="d:/logs/WebApplog4j2-$${date:yyyy-MM}/app-%d{MM-dd-yyyy}-%i.log.gz"  immediateFlush="false">
        <PatternLayout>
            <Pattern>%d [%t] %-5p %c :: %m%n</Pattern>
        </PatternLayout>
        <Policies>
            <OnStartupTriggeringPolicy />
            <TimeBasedTriggeringPolicy />
            <SizeBasedTriggeringPolicy size="10 MB" />
        </Policies>
        <DefaultRolloverStrategy max="20" />
    </RollingFile>
    </Appenders>
    <Loggers>
	<AsyncLogger name="org.hibernate" level="ERROR">
		<AppenderRef ref="RollingFileAppender" />
	</AsyncLogger>
	<Root level="error">
			<AppenderRef ref="Console-Appender" />
	</Root>
	<AsyncLogger name="com.app" level="ERROR" additivity="false">
            <AppenderRef ref="RollingFileAppender" />
    </AsyncLogger>
	
    </Loggers>
</Configuration>
{% endhighlight %}

* Pass in JVM argument to provide path to the log4j2.xml file:

{% highlight java %}
-Dlog4j.configurationFile=D:\logs\log4j2.xml
{% endhighlight %}

* This was enough to get most of my logging done asynchronously but I still could not get hibernate logging to work. I had to do some additional configuration for the same:

Add the following slf4j dependency:

{% highlight java %}
	<dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-slf4j-impl</artifactId>
            <version>${log4j2.version}</version>
    </dependency>

{% endhighlight %}

Pass in JVM argument for the jboss logging provider since Hibernate uses jboss logging:
{% highlight java %}
-Dorg.jboss.logging.provider=log4j2
{% endhighlight %}

