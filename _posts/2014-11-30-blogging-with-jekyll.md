---
layout: post
title: "Blogging with Jekyll"
description: ""
category:"jekyll"
tags: []
---

###Jekyll

Jekyll is a markdown parser that allows creation of lightweight blogs in markdown without the need of databases.

##Quickstart with Jekyll:
>
* Fork https://github.com/poole/hyde
* Rename forked repository to <github.username>.github.io
* Copy Rakefile from https://github.com/plusjade/jekyll-bootstrap.git which allows tasks like:
  - rake post title="new post"
  - rake page name="new_page.md"
* Change url in _config.yml to http://<github.username>.github.io
* Change CNAME entry to <github.username>.github.io
* Push changes to git repo and view blog at http://<github.username>.github.io


##Folder Layout:

<table>
  <tr>
    <td>_config.yml</td>
    <td>Stores configuration data.</td>
  </tr>
  <tr>
    <td>_includes</td>
    <td>This folder is for partial views.</td>
  </tr>
  <tr>
    <td>_layouts</td>
    <td>This folder is for the main templates your content will be inserted into. You can have different layouts for different pages or page sections.</td>
  </tr>
  <tr>
    <td>_posts</td>
    <td>This folder contains your dynamic content/posts. the naming format is required to be @YEAR-MONTH-DATE-title.MARKUP@.</td>
  </tr>
  <tr>
    <td>_site</td>
    <td> This is where the generated site will be placed once Jekyll is done transforming it.</td>
  </tr>
  <tr>
    <td>assets</td>
    <td>

 This folder is not part of the standard jekyll structure. The assets folder represents any generic folder you happen to create in your root directory. Directories and files not properly formatted for jekyll will be left untouched for you to serve normally.Images can be kept here to be referenced as /assets/image.jpg</td>
  </tr>
</table>


