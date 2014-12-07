---
layout: post
title: "Working with the Camel choice element (is a bit like Sophie's choice)"
description: ""
category:"camel"
---

Positioning of end() and endchoice() seem to be very transluscent in the JAVA DSL right now.

Code that doesn't compile:
>
	  .when(xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate").namespaces(ns))
             .setProperty("searchDate", xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate/text()").namespaces(ns))
             .setProperty("bestGuess").constant(false)
           .otherwise()

Code that doesn't compile:
>
	  .when(xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate").namespaces(ns))
             .setProperty("searchDate", xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate/text()").namespaces(ns))
             .setProperty("bestGuess").constant(false).end()
           .otherwise()

Code that compiles:

>
	  .when(xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate").namespaces(ns))
             .setProperty("searchDate", xpath("/soapenv:Envelope/soapenv:Body/grp1:GetGroup/grp1:searchDate/text()").namespaces(ns))
             .setProperty("bestGuess").constant(false).endChoice()
           .otherwise()


The above is due to restrictions of Java in terms of DSL. In the first block, we need to indicate where the setProperty ends, but doing that with an end() in java doesn't work properly where it should pop the last type of the stack and return the scope of the CBR. Hence we need to include an endChoice() to make it work correctly.

ref: <a href="http://camel.apache.org/why-can-i-not-use-when-or-otherwise-in-a-java-camel-route.html">Camel Choice</a>
