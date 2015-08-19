---
layout: post
title: "Java 8 primer"
description: ""
category: "java"
---
### JAVA 8

Went digging around in Java8 and found a few interesting features which I have tried to explain below:

* Default Methods for Interfaces: Enables us to add non abstract method implementations to interfaces by utilizing default keyword. If a class implements two interfaces which define the same default method, then a compilation error will be thrown.

>
	
	interface Formula {
		double calculate(int a);

		default double sqrt(int a) {
			return Math.sqrt(a);
		}
	}	
	
* LAMBDAS: Anonymous methods which do not need creation of anonymous classes.
			- Separation between how to and what to .
			- returns nothing
			- Lambdas are not converted to a new function types but to "functional interface" types.
			- Local variables are accessible but implicitly final.
			- Fields of class accessible but not implicitly final.
			- Lambda body(fields etc) scoped by enclosing method as opposed to inner classes.


>

	(Type element) -> { body of function }
	e.g: 
		Collections.sort(names, (a, b) -> {
			return b.compareTo(a);
		});


* Method References: Concise method of invoking existing methods.
	
	- Static references
	- Constructor
	- non-static method
	- non-static method for a specific object

>
	Account::addInterest

* Streams: The name suggests something akin to Input or OutputStream which have existed in Java forever, but Streams are different. They are monads, and bring functional programming in Java

>
	In functional programming, a monad is a structure that represents computations defined as sequence of steps. A type with a monad structure defines what it means
	to chain operations, or nest functions of that type together.

	

	- collect is a terminal function to create a collection from stream
	- collect(Collector)
	- boxed: function to perform autoboxing
	- Collector.joining : for concatenating characters
