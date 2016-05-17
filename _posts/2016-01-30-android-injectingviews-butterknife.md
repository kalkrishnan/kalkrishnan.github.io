---
layout: post
title: "android butterknife injection"
description: ""
category: "android"
---

ref: <a href="http://jakewharton.github.io/butterknife/">Butterknife</a>

Butterknife is used to inject views into layout files. However the setup was a bit of a struggle. The following worked for me:

>
	* Add the below in your project build.gradle:
	
	dependencies {
        
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.4'
    }
	
	* Add the below in your app build.gradle:
	
	apply plugin: 'android-apt'
	
	dependencies {
    
    compile 'com.jakewharton:butterknife:8.0.1'
    apt 'com.jakewharton:butterknife-compiler:8.0.1'
 
	}
	
	* Add the below plugin for easy use of butterknife with Android Studio:
	
	File->Settings->Plugin->Browse Repositories->Android Butterknife Zelezny