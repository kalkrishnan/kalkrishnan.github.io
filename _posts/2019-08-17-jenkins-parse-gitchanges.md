---
layout: post
title: "Jenkins parse git change log"
description: ""
category: "Jenkins"
---

There is this neat plugin in Jenkins called Git Changelog which provides information about what was the git change that triggered the build. Recently I wanted to parse this change so I could send notification emails when certain things changed e.g: DB schemas.

My process for doing the same was:

* Parse the changelog to populate certain environment variables
* Based on values of said environment variables, trigger a conditional action
* Send notification emails with relevant attachments.


1.    Parsing the changelog: 

    * Get the current build: 

{% highlight java %}
import hudson.model.*;
import hudson.util.*;
import hudson.scm.*;
import hudson.plugins.git.*

def thr = Thread.currentThread();
def build = thr?.executable;
{% endhighlight %}

    * Get the gitlog changeset and entries within the changeset

{% highlight java %}
def changeSet= build.getChangeSet();
def entries = changeSet.getItems();
{% endhighlight %}

    * Parse entries to capture authors, comments and files changed

```Java
def map=[:]


def filePath = ""

for (int j = 0; j < entries.length; j++) {
                                   def entry = entries[0]
                                   map.put('AUTHOR',entry.getAuthor())
                                   def files = new ArrayList(entry.getAffectedFiles())
                                   for (int k = 0; k < files.size(); k++) {
                                                                                  def file = files[k]
                                                                                  if(file.getPath().contains("Schema"))
                                                                                  {
                                                                                    filePath = file.getPath() +","+filePath
                                                                                  }
                                                                            }
                                         }
if(filePath.length()>1)
{
   filePath = filePath.substring(0, filePath.length()-1)
}

map.put('FILE', filePath )

if(changeSet.getLogs())
{
                                   if(changeSet.getLogs().get(0))
                                   {
                                        def changeComment = changeSet.getLogs().get(0).getComment().split(":")[0];
                                        map.put('CHANGE_COMMENT',changeComment)
            
                                   }
}
return map

```

The above code parses the changeset entries to capture authors and logs and creates an environment variable with the files changed in this changeset.

2.    Conditional action to trigger based on files changed:

![Conditional Action](http://kalkrishnan.github.io/assets/jenkinsConditionalAction.png "Conditional Action")


3.    Editable Email notification if conditional action is triggered, with attachments: 


![Editable email notification](http://kalkrishnan.github.io/assets/jenkinsEditableEmail.png "Editable email")