---
published: true
layout: post
title:  "Non Session Ajax Updates"
author: ishimoto
date:   2026-08-08 12:20:00 +0900
categories: ajax
tags: ajax
---
a simple ajax call from a non session page to an direct action.

{% highlight html %}
<tb:updateContainer id="tbSample">
    <span class="text-muted">Nothing loaded yet — click below.</span>
</tb:updateContainer>

<tb:updateLink actionClass="TBAjaxDirectAction" directActionName="sample" 
	updateContainer="tbSample">
    Load a live server fragment
</tb:updateLink>
{% endhighlight %}

{% highlight java %}
    @TBAction
    public ITBWActionResults sample() {
        final var html = "<span class=\"tb-check ok\">Live from the server at " 
        + TBFZonedDateTime.now() + " — no page reload.</span>";
        return dataResponseWithExpiredDate(html);
    }
{% endhighlight %}

a simple ajax call from a non session page to an direct action from a Selector Tag.

{% highlight html %}
<tb:updateContainer id="tbSelectStatus">
    <span class="text-muted">Pick an edition…</span>
</tb:updateContainer>

<tb:updateSelect name="edition" list="$editions" item="$edition"
        actionClass="TBAjaxDirectAction" directActionName="sample"
        updateContainer="tbSelectStatus" />
{% endhighlight %}
