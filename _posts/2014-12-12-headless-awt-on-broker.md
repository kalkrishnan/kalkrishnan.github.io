---
layout: post
title: "AWT Issue on IBM Message Broker on LINUX"
description: ""
category: "ibm"
---

### Message Broken

Trying to run some apache poi stuff on the Message Broker gave issues while trying to run the broker on a LINUX vm. :

>
	java.lang.NoClassDefFoundError: java.awt.Toolkit (initialization failure)
	
Solution was to set the awt=headless property on the jvm as below:

>
	mqsichangeproperties Broker -e EG -o ComIbmJVMManager -n jvmSystemProperty -v \"\-Djava\.awt\.headless\=true\"
