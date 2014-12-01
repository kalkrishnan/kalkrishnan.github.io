---
layout: post
title: "Working with the Camel choice element"
description: ""
---

Positioning of end() and endchoice() seem to be very transluscent in the JAVA DSL right now.

Code that doesn't compile:
>
	.otherwise()
             .setProperty("searchDate").constant(Calendar.getInstance())
             .setProperty("bestGuess").constant(true)
           .end()
		   
Code that doesn't compile:
>
	.otherwise()
             .setProperty("searchDate").constant(Calendar.getInstance())
             .setProperty("bestGuess").constant(true).end()
           .end()
		   
Code that compiles:

>
	.otherwise()
             .setProperty("searchDate").constant(Calendar.getInstance())
             .setProperty("bestGuess").constant(true).endChoice()
           .end()
		   
		   
The above is due to restrictions of Java in terms of DSL. In the first block, we need to indicate where the setProperty ends, but doing that with an end() in java doesn't work properly where it should pop the last type of the stack and return the scope of the CBR. Hence we need to include an endChoice() to make it work correctly.