---
layout: post
title: "Permission errors while installing with yo"
description: ""
category: "yo"
---

To start working with <a href="http://yeoman.io/codelab.html">yo + angular</a>, we first install it using npm:

>
sudo npm i -g yo

Then install angular:

>
sudo npm i -g generator-angular


and scaffold the app as below:

>
yo angular

But you realize that this fails with a lot of permission errors. To resolve these, run:

>
sudo chown -R `whoami` ~/.npm

If you continue to get a lot of warnings, you can ignore these using:

>
npm WARN unmet dependency

If you still get errors running grunt tasks:

>
npm update npm -g
sudo npm update npm -g (i used this command on my mac)

Then type this command

>
npm install
