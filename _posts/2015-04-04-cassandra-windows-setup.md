---
layout: post
title: "Cassandra Windows Setup"
description: ""
category: "java"
---

 Setting up cassandra on Windows:

>
 * Extract Cassandra source files. e.g. to D:\cassandra
 * Change conf/cassandra.yaml as below:
	- /var/lib/cassandra/commitlog to d:/cassandra/commitlog and create the folder
	- /var/lib/cassandra/data to d:/cassandra/data and create the folder
 * Go to directory d:\cassandra\bin\ and start cassandra.bat 
 