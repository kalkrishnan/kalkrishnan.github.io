---
layout: post
title: "spring boot repository event handler"
description: ""
category: "Spring Boot"
---

Spring boot is an awesome "wrapper" which allows Spring projects to be created and run with minimal configuration and it makes development fun again. While exploring Spring boot repositories, I needed to perform certain actions based on entities being created or deleted:


Defining a Spring Repository:

{% highlight java %}

	public interface UserRepository extends CrudRepository<User, Long>
	{
	}
{% endhighlight %}

	
To capture events emitted when entities are created or destroyed:

{% highlight java %}
@RepositoryEventHandler(User.class)
@RestController
public class UserEventHandler {


	@HandleAfterCreate(User.class)
	public void handleUserSave(User user) {
		/* to do */
	}
}
{% endhighlight %}

The above class will execute the method whenever a User object is created. Similar annotations for deletions and updates exist.