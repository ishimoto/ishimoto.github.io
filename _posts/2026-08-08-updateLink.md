---
published: true
layout: post
title:  "Non Session UpdateLink and UpdateContainer"
author: ishimoto
date:   2026-08-08 12:20:00 +0900
categories: ajax
tags: ajax
---
a new simple ajax call from a non session page to an direct action.

{% highlight html %}
	<tb:updateContainer id="tbSample">
    	<span class="text-muted">Nothing loaded yet — click below.</span>
    </tb:updateContainer>

    <tb:updateLink actionClass="TBAjaxDirectAction" directActionName="sample" updateContainer="tbSample" class="btn btn-primary" style="margin-top: 12px">
        Load a live server fragment
    </tb:updateLink>
{% endhighlight %}


{% highlight java %}
    @TBAction
    public ITBWActionResults sample() {
        final var html = "<span class=\"tb-check ok\">Live from the server at " + TBFZonedDateTime.now() + " — no page reload.</span>";
        return dataResponseWithExpiredDate(html);
    }
{% endhighlight %}
