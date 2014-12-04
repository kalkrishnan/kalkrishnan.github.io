---
layout: post
title: "Git index lock issue"
description: ""
category: "Git"
---

While trying to run git add . kept getting the below exception:

>

	fatal: Unable to create '.git/index.lock': File exists.

	If no other git process is currently running, this probably means a
	git process crashed in this repository earlier. Make sure no other git
	process is running and remove the file manually to continue.
	
	
To fix this delete the file in question as below:

>
	rm  ./.git/index.lock