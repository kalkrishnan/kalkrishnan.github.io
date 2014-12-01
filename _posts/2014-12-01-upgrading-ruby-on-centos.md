---
layout: post
title: "Upgrading Ruby on CentOS"
description: ""
---

###Upgrading to Ruby 2.1.2:


* sudo yum remove ruby ruby-devel
* $ sudo yum groupinstall "Development Tools"
* $ sudo yum install openssl-devel
* $ wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz
* $ tar xvfvz ruby-2.1.2.tar.gz
* $ cd ruby-2.1.2
* $ ./configure
* $ make
* $ sudo make install
