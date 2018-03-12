---
layout: post
title: "git reorder commits"
description: ""
category: "Git"
---

Say you committed some code, then committed some experimental stuff on top of that and went back to the original commit and changed some stuff. Totally normal situation. Now you want to submit a merge request, but only for the completely baked code. One solution is to re-order commits and squash the commits you want to merge into your cherry picked commit:

Below is my JPA repository:

{% highlight java %}
git rebase -i HEAD~<as many commits back as you need to go>
in the interactive screen, re-order the picks
git push origin --force

{% endhighlight %}

