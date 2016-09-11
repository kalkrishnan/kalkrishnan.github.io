---
layout: post
title: "spring boot repository rest interface"
description: ""
category: "Spring Boot"
---

Spring boot is an awesome "wrapper" which allows Spring projects to be created and run with minimal configuration and it makes development fun again. Recently I revisited Spring JPA with Spring Boot and Hibernate and discovered the wonders of exposing a Spring repository using REST.

Spring Repository
--

Defining a Spring Repository:

{% highlight java %}

	public interface UserRepository extends CrudRepository<User, Long>
	{
	}
{% endhighlight %}

	
	By Default defining a repository as above will create the following REST URL: http://localhost:8080/users
	
If we want to change the path of the default url created:

{% highlight java %}
	@RepositoryRestResource(collectionResourceRel = "user", path = "user")
	public interface UserRepository extends CrudRepository<User, Long> {
		}
		
{% endhighlight %}

		
	Now the URL will be: http://localhost:8080/users
	
This URl will allow CRUD actions using POST, PATCH, DELETE.

Sometimes the need arises to define custom methods for the JPA repository:

{% highlight java %}

	@RepositoryRestResource(collectionResourceRel = "user", path = "user")
	public interface UserRepository extends CrudRepository<User, Long> {

		User findByEmailAndPassword(@Param("email") String email, @Param("password") String password);

	}
	
{% endhighlight %}

	This will expose the following URL: http://localhost:8080/user/search/findByEmailAndPassword{?email,password}
	
Now let's define the User Entity:

{% highlight java %}

public class User {

	private String name;
	@Id
	private String email;
	private String password;


	public String getName() {
		return name;
	}

	public String getEmail() {
		return email;
	}

	public String getPassword() {
		return password;
	}
	
	}
{% endhighlight %}
	
	
Now a GET for this entity will by default not return the id for the entity, in this case the email. To enable this create a configuration class as below:

{% highlight java %}

@Configuration
public class UserConfiguration extends RepositoryRestConfigurerAdapter {

	@Override
	public void configureRepositoryRestConfiguration(RepositoryRestConfiguration config) {
		config.exposeIdsFor(User.class);
	}
	
}
{% endhighlight %}

And presto, everything just works as promised.