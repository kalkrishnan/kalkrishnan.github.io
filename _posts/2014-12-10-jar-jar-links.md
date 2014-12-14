---
layout: post
title: "Jar Jar Links"
description: ""
category: "random"
---

### Fun with JarJar

So I was deep in jar hell today, trying to manage two different versions of a thirdparty application with same package names and different implementations when I had the bright idea of why don't I change the package names. This initially bright idea seemed like a daunting task given I did not have the source code till I came across an old bumbling friend called <a href="https://code.google.com/p/jarjar/"> Jar Jar Links</a>:

>
	e.g:  java -jar jarjar-1.4.jar process rules.txt google-collections-1.0.jar google-collections-old-1.0.jar
	cat rules.txt : rule com.google.** com.googleold.@1
