---
published: true
layout: post
title:  "Load JSON file from Resource Folder"
author: ishimoto
date:   2024-05-26 12:20:00 +0900
categories: json
tags: json
---
To read a json file from the resource folder following command is easy to use.

{% highlight java %}
final var json = TBFString.stringFromFileInResourceFolder(
	"static://app:sample.json");
{% endhighlight %}
