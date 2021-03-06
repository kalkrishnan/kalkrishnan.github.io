---
layout: post
title: "spring boot websocket"
description: ""
category: "Spring Boot"
---

Spring boot is an awesome "wrapper" which allows Spring projects to be created and run with minimal configuration and it makes development fun again. I was looking for a way to emit events from my Spring repositories to topics that consumers could subscribe to and came across spring boot websockets.

Add the below to your build.gradle:

{% highlight java %}
    compile("org.springframework.boot:spring-boot-starter-websocket")
    compile("org.springframework:spring-messaging")
{% endhighlight %}

Defining a Spring Repository:

{% highlight java %}

	public interface UserRepository extends CrudRepository<User, Long>
	{
	}
{% endhighlight %}

	
To capture events emitted when entities are created and send them to a socket endpoint:

{% highlight java %}
@RepositoryEventHandler(User.class)
@RestController
public class UserEventHandler {

	@Autowired
	private SimpleMessagingTemplate pushClient;

	@HandleAfterCreate(User.class)
	public void handleUserSave(User user) {
		this.pushClient.convertAndSend("/topic/user", user.toString());
	}
}
{% endhighlight %}

The above code will use the Spring SimpleMessagingTemplate to push the created user to a /topic/user topic.
Expose the above event handler through your Spring configuration:

{% highlight java %}
@RepositoryEventHandler(User.class)
@RestController
@Configuration
public class UserConfiguration extends RepositoryRestConfigurerAdapter {


	@Bean
	UserEventHandler userEventHandler() {
		return new UserEventHandler();
	}
}

{% endhighlight %}

Finally create a websocket configuration to define the broker and endpoint:

{% highlight java %}
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {

	@Override
	public void configureMessageBroker(MessageBrokerRegistry config) {
		config.enableSimpleBroker("/topic");

	}

	@Override
	public void registerStompEndpoints(StompEndpointRegistry registry) {

		registry.addEndpoint("/messages").setAllowedOrigins("*").withSockJS();

	}

}

{% endhighlight %}

The above code does the following things:

>

	1. Creates a simple broker.
	2. Creates an endpoint /messages which accepts cross origin requests from all domains.
	3. Enable SockJS fallback options.


Now you can connect to this websocket and subscribe to the topic as below:
{% highlight javascript %}
function connect() {
    var socket = new SockJS("http://localhost:8080/messages");
    stompClient = Stomp.over(socket);
    stompClient.connect({}, function (frame) {
        setConnected(true);
        console.log('Connected: ' + frame);
        stompClient.subscribe('/topic/user', function (greeting) {
            showGreeting(JSON.parse(greeting.body).content);
        });
    });
}
{% endhighlight %}
