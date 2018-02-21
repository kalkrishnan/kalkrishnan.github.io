---
layout: post
title: "git split commits"
description: ""
category: "Git"
---

We have all made commits which included "experimental" stuff. Suppose you run into this situation and want to split your commit into multiple commits:

{% highlight java %}
    git rebase -i HEAD~<as far back as the commit is, for most recent commit :1>
mark commit as edit in the rebase screen
git add + commit to create different commits
git rebase --continue

{% endhighlight %}
