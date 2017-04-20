---
layout: post
title: "Jenkins Pipeline"
description: ""
category: "Devops"
---

Ahh the jenkins pipeline. The cure for all evils. They entice you in with images like the below, but getting a pipeline up and running involves a lot of soul searching and googling sometimes even venturing into the dark place known as the second page of google results.

![screenshot 1]({{ site.url }}/assets/pipeline1.png)
![screenshot 2]({{ site.url }}/assets/pipeline2.jpg)


Below is my JPA repository:

{% highlight java %}
    @RepositoryRestResource(collectionResourceRel = "user", path = "user", excerptProjection = UserInlineFavorites.class)
	public interface UserRepository extends CrudRepository<User, String> {

		User findByEmailAndPassword(@Param("email") String email, @Param("password") String password);

	}

{% endhighlight %}

This resulted in the following JSON:

{% highlight java %}

{
  "name" : "test",
  "email" : "test@gmail.com",
  "password" : "test",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/user/test"
    },
    "user" : {
      "href" : "http://localhost:8080/user/test@gmail.com",
      "templated" : true
    },
    "favorites" : {
      "href" : "http://localhost:8080/user/test@gmail.com/favorites"
    }
  }
}
{% endhighlight %}

	
The problem with this is the nested favorites entities have to obtained using another REST call which seemed like a waste to me. After digging around for a while, I came across the concept of Projections to obtain fully resolved entities:

Create a projection interface to define the structure of the resolved entity:

{% highlight java %}
package com.kkrishna.gymtime.dao;

import java.util.List;

import org.springframework.data.rest.core.config.Projection;

@Projection(name = "inlineFavorites", types = { User.class })
public interface UserInlineFavorites {

	public String getName() ;

	public String getEmail();

	public String getPassword() ;

	public List<Gym> getFavorites() ;
}

{% endhighlight %}

Add the below to the Spring configuration to force it to always use the projection:

{% highlight java %}
config.getProjectionConfiguration().addProjection(UserInlineFavorites.class, User.class);

{% endhighlight %}

Finally change the repository to add the projection:

{% highlight java %}
@RepositoryRestResource(collectionResourceRel = "user", path = "user", excerptProjection = UserInlineFavorites.class)
public interface UserRepository extends CrudRepository<User, String> {

	User findByEmailAndPassword(@Param("email") String email, @Param("password") String password);

}

{% endhighlight %}

Now the resulting json is:

{% highlight javascript %}
{
  "name" : "test",
  "favorites" : [ {
    "latLong" : "33.64_-117.86",
    "name" : "Pilates Plus Newport Beach",
    "address" : "1220 Bison Ave, Newport Beach, CA 92660"

  }, {
    "latLong" : "33.65_-117.84",
    "name" : "Yoga Shakti Wellness Center",
    "address" : "4249 Campus Dr B140, Irvine, CA 92612"
  }, {
    "latLong" : "33.66_-117.86",
    "name" : "Equinox Newport Beach",
    "address" : "19540 Jamboree Rd, Irvine, CA 92612"
  }, {
    "latLong" : "33.63_-117.83",
    "name" : "Merage Jewish Community Center of Orange County",
    "address" : "1 Federation Way #200, Irvine, CA 92603"
  } ],
  "email" : "test@gmail.com",
  "password" : "mirage2000",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/user/test@gmail.com"
    },
    "user" : {
      "href" : "http://localhost:8080/user/test@gmail.com{?projection}",
      "templated" : true
    },
    "favorites" : {
      "href" : "http://localhost:8080/user/test@gmail.com/favorites"
    }
  }
}
{% endhighlight %}

The caveat to this is that to retrieve single resources you have to add the projection to the URL:

{% highlight java %}
http://localhost:8080/user/test@gmail.com?projection=inlineFavorites

{% endhighlight %}

more discussion on [StackOverflow](http://stackoverflow.com/questions/30220333/why-is-an-excerpt-projection-not-applied-automatically-for-a-spring-data-rest-it)