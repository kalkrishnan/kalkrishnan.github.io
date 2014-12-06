---
layout: post
title: "Grunt access from outside"
description: ""
category: "yo"
---

To access a server started by <a href="http://gruntjs.com/">Grunt</a> externally, change the below in the Gruntfile.js
>
connect: {
      options: {
        port: 9000,
      <b>  // Change this to '0.0.0.0' to access the server from outside.</b>
        hostname: 'localhost',
        livereload: 35729
      },
