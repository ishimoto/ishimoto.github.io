---
published: true
layout: post
title:  "Non Session Ajax Updates"
author: ishimoto
date:   2026-08-08 12:20:00 +0900
categories: ajax
tags: ajax
---
# Non Session Ajax update

From the ControlCenter you can get access via  
**Policy.tb-core-extensions.show.dashboard.DashboardAjaxUI**.  
This is a new set of commands for having Ajax UpdateContainers in Non Session calls.

## Sample 1
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

## Sample 2

a simple ajax call from a non session page to an direct action from a Selector Tag.

{% highlight html %}
<tb:updateContainer id="tbSelectStatus">
    <span class="text-muted">Pick an edition…</span>
</tb:updateContainer>

<tb:updateSelect name="edition" list="$editions" item="$edition"
        actionClass="TBAjaxDirectAction" directActionName="sample"
        updateContainer="tbSelectStatus" />
{% endhighlight %}

## Sample 3

an updateLink with a confirm message.

{% highlight html %}
<tb:updateContainer id="tbDelete">
	<span class="text-muted">Reply appears here…</span>
</tb:updateContainer>
<tb:form>
    <tb:updateLink actionClass="TBAjaxDirectAction" directActionName="delete" method="post"
        updateContainer="tbDelete" confirm="Delete this permanently?" ?id="$deleteIdTest">
        Delete
    </tb:updateLink>
</tb:form>
{% endhighlight %}

## Sample 4

an updateField Sample

{% highlight html %}
<tb:updateContainer id="tbEcho">
	<span class="text-muted">POST reply appears here…</span>
</tb:updateContainer>
<tb:form>
    <tb:updateField name="message" placeholder="type & POST…"
        actionClass="TBAjaxDirectAction" directActionName="echo" method="post"
        updateContainer="tbEcho" />
</tb:form>
{% endhighlight %}

{% highlight java %}
@TBAction
public ITBWActionResults echo() {
    final var message = request().stringFormValueForKey("message");
    log.info("Sample: Echoing {}, formValueKeys {}", message, request().formValueKeys());
    final var safe = message == null 
    	? "" 
    	: message.replace("&", "&amp;").replace("<", "&lt;").replace(">", "&gt;");
    final var html = "<span class=\"tb-check ok\">POST received at " 
    	+ TBFZonedDateTime.now() + " — you said: <b>" + safe + "</b></span>";
    return dataResponseWithExpiredDate(html);
}
{% endhighlight %}

## Sample 5

Network Error Processing

{% highlight html %}
<tb:updateContainer id="tbErrorDemo"><span class="text-muted">Error demo output…</span></tb:updateContainer>
<tb:updateLink actionClass="TBAjaxDirectAction" directActionName="doesNotExist"
    updateContainer="tbErrorDemo" class="btn btn-secondary" style="margin-top:12px">
    Trigger a failing call
</tb:updateLink>
{% endhighlight %}

## IntelliJ Code Editor

![CodeEditorSample](/assets/CodeEditor/CodeEditorSample.png)
