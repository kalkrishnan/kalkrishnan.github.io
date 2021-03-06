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


	* Providers: Vagrant relies on Providers to interact with software installed on the vms.
	* Boxes: Predefined images to build the environment according to instructions provided
	* Vagrantfile: Set of instructions to customize the environment.
	* Provisioners: Used to install software in the Virtualbox host.
	
Docker:	Docker is an open source project to pack, ship and run any application as a lightweight container.
	
	* Docker leverages LXC (Linux Containers), which encompasses Linux features like cgroups and namespaces for strong process isolation and resource control
	* Docker uses a "plain text" configuration language (I suppose you could think of it as a domain-specific language, or DSL) to control the configuration of an application container, such as what files should (or should not) be included, what network ports should be open, and what processes/applications should be running inside the container
	* Docker leverages a copy-on-write filesystem (currently AUFS, but other filesystems are being investigated). 
	* Docker needs kernel version 3.8.
	* To Transfer files from the Host VM to the Docker container, the files must be in the same directory or a sub-directory as the Dockerfile.

Building a Docker container using Vagrant which will deploy a war to jetty and run it:

>
	* The Docker provider depends entirely on the Dockerfile to specify the image settings and start the container.
	* By Default Vagrant will create a boot2docker host to run the Docker container in unless specified otherwise.
	
	 config.vm.provision "docker" do |d|
		d.build_image "/home/vagrant/PhatStats", args: "-t 'phatstats'"
		d.run "phatstats", args: " -p '8888:8080'"
	 end
	
	* The Dockerfile executed by this:
	
		FROM jetty:9
		VOLUME /var/lib/jetty/webapps
		COPY target/PhatStats-0.0.1-SNAPSHOT.war /var/lib/jetty/webapps/
		EXPOSE 8080
		CMD ["jetty.sh", "run"]	