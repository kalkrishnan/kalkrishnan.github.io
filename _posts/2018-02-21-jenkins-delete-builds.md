---
layout: post
title: "Jenkins delete builds"
description: ""
category: "Jenkins"
---

I recently ran into a situation where Jenkins upstream jobs screwed up and my downstream jobs started building every branch in Git. I had to fix the issue and then try to clear up the builds. Thankfully the Jenkins community is aware of these issues and they provided a CLI to play with the jenkins environment:

Access the CLI at:

{% highlight java %}
<jenkins url>/jnlpJars/jenkins-cli.jar
{% endhighlight %}

Then run the below command:


{% highlight java %}
PS C:\Users\kkrishnan\Downloads> java -jar jenkins-cli.jar -s <jenkins url> delete-builds <job name> 418-454 --
username xxxxx --password xxxxxx
{% endhighlight %}

There is a bug with the above where it throws the 'no job <job name> found error'. To fix this we have to give the anonymous user discover and read access for jobs under Jenkins Global security configuration.