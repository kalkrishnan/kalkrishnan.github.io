---
layout: post
title: "spring aop performance monitoring"
description: ""
category: "Spring"
---

Noboby instruments code better than Spring AOP. Generating execution times for methods is made easy with the AbstractMonitoringInterceptor.

Spring AOP configuration:

{% highlight java %}
@Component
@EnableAspectJAutoProxy
@Aspect
public class FileIntAopConfiguration {

	@Pointcut("execution(* com.test.FlatFileProcessor.*(..))")
	public void monitor() {
	}

	@Bean
	public FileIntPerformanceMonitorInterceptor performanceMonitorInterceptor() {
		return new FileIntPerformanceMonitorInterceptor(true);
	}

	@Bean
	public Advisor performanceMonitorAdvisor() {
		AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
		pointcut.setExpression("execution(* com.test.FlatFileProcessor.*(..))");
		return new DefaultPointcutAdvisor(pointcut, performanceMonitorInterceptor());
	}
}

{% endhighlight %}

* Pointcuts are methods to be intercepted by AOP and Advisors link pointcuts to interceptor implementations:

{% highlight java %}
public class FileIntPerformanceMonitorInterceptor extends AbstractMonitoringInterceptor {
    
    /**
	 * 
	 */
	private static final long serialVersionUID = 8725998668272240895L;

	public FileIntPerformanceMonitorInterceptor() {
    }
 
    public FileIntPerformanceMonitorInterceptor(boolean useDynamicLogger) {
            setUseDynamicLogger(useDynamicLogger);
    }
 
    @Override
    protected Object invokeUnderTrace(MethodInvocation invocation, Log log) 
      throws Throwable {
        String name = createInvocationTraceName(invocation);
        long start = System.currentTimeMillis();
        log.info("Method " + name + " execution started at:" + new Date());
        try {
            return invocation.proceed();
        }
        finally {
            long end = System.currentTimeMillis();
            long time = end - start;
            log.info("Method "+name+" execution lasted:"+time+" ms");
            log.info("Method "+name+" execution ended at:"+new Date());
             
            if (time > 10){
                log.warn("Method execution longer than 10 ms!");
            }            
        }
    }
}
{% endhighlight %}

Make sure to use correct aspectj versions in your pom:

{% highlight java %}
	<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>1.7.0</version>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.7.0</version>
		</dependency>
{% endhighlight %}

* Log4j Logging level has to be at TRACE for the logs to show up.