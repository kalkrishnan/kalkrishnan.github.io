---
layout: post
title: "CXF Interceptors"
description: ""
category: "java"
---

 If you ever work with CXF to consume web services and are looking for a way to instrument your code, CXF provides a pretty slick API in the form of <a href="http://cxf.apache.org/docs/interceptors.html">Interceptors</a>.
 These interceptors provide pretty advanced capabilities like changing the SOAP message or headers at runtime but let's take a look at basic logging interceptors:
 
{%  gist 2ba199d41112215784aa %}


The two examples above inherit from two standard logging CXF Interceptors

>
LoggingInInterceptor : For incoming messages (responses)
LoggingOutInterceptor : For outgoing messages(requests)

The formatLoggingMessage method intercepts the message and allows the user to extract various parts of the message and log it. The -1 in the constructor instructs the interceptor to extract the entire message(upto max Integer) and not truncate it.
Logging is implemented by java.utils.logging but can be controlled through a log4j2.xml file as below:

>

    <Logger name="org.apache.cxf.services" level="INFO" additivity="false">
    	<AppenderRef ref="splunk" />
        <AppenderRef ref="applogfile"/>
    </Logger>
