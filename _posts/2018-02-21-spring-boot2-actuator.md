---
layout: post
title: "spring actuator"
description: ""
category: "Spring Boot"
---

Spring Boot comes with tools for building production ready microservices. One of the more important features Boot provides is Spring actuator.
Spring actuator provides information about your microservice. Once the service is up and running, the actuator endpoints can be accessed using:

{% highlight java %}
http://localhost:<port>/<application_name>/actuator/endpoint

e.g:

http://localhost:8080/applicationTest/actuator/info

{
  "build" : {
    "version" : "0.0.1-SNAPSHOT",
    "artifact" : "applicationTest",
    "name" : "applicationTest",
    "group" : "com.test",
    "time" : "2018-03-09T21:50:26.591Z"
  }
}
{% endhighlight %}

By default only the info and health endpoints are enabled and exposed. The other endpoints are enabled but need to be exposed over http:

{% highlight java %}
management.endpoints.web.exposure.include=*
{% endhighlight %}

To enable pretty print like above:

{% highlight java %}
spring.jackson.serialization.indent_output=true
{% endhighlight %}