---
layout: post
title: "Docker issue with Vagrant"
description: ""
category: "tools"
---
### Docker 

Started up my docker project again and ran into some docker install problems with the below error:

>
W: Failed to fetch http://get.docker.io/ubuntu/dists/docker/main/binary-amd64/Packages  Undetermined Error [IP: 52.4.48.36 80]

>
W: Failed to fetch http://get.docker.io/ubuntu/dists/docker/main/binary-i386/Packages  Undetermined Error [IP: 52.4.48.36 80]

This was due to the fact that the Docker apt-get url for Vagrant had changed from http to https. Resolved this by changing the below:

>
vagrant/embedded/gems/gems/vagrant-1.7.2/plugins/provisioners/docker/cap/debian/docker_install.rb:

Change the line

>
comm.sudo("echo deb http://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list") 
 
to:

>
comm.sudo("echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list")  

Had to destroy and restart my vm after this.