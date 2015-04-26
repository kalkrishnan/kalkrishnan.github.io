---
layout: post
title: "Docker with Vagrant"
description: ""
category: "tools"
---

After having dealt with enough Production deployment issues, I started looking at docker which is a lightweight container to run applications in. 
Being familiar with Vagrant, I decided to see how the two tie in together.
For the unfamiliar:

Vagrant is an automation tool with a domain-specific language (DSL) that is used 
to automate the creation of VMs and VM environments.
Using Vagrant's DSL, we can define a base set of instructions to create a VM .
This allows for automated creation of VMs based on said script. 
This helps in creation of consistent environments across all regions(Dev, QA, Production).

>

	Vagrant itself doesn’t provide any virtualization functionality,
	it relies on other products to do the heavy lifting. 
	Providers are how Vagrant interacts with the products that will do the actual virtualization work.
