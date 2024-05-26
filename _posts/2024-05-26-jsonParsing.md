---
published: true
layout: post
title:  "Parse JSON data"
author: ishimoto
date:   2024-05-26 15:20:00 +0900
categories: json
tags: json
---
Parsing JSON data vie KeyValue Coding.  

Example JSON:

{% highlight json %}
{
    "id": "3f34d432-6868-4d19-b862-717b56721a89",
    "resourceType": "SomeType",
    "providedBy": {
        "id": "aa3d917-e428-4c56-932a-01869d479496",
        "reference": "8a13d917-e428-4c56-932a-01869d479496"
    },
    "meta": {
        "extension": [
            {
                "url": "http://...",
                "extension": [
                    {
                        "url": "..."
                    }
                ]
            }
        ]
    },
    "availableTime": [
        {
            "extension": [
                {
                    "url": "http://...",
                    "valueString": "..."
                }
            ],
            "daysOfWeek": [
                "mon",
                "tue",
                "wed",
                "thu",
                "fri"
            ],
            "availableStartTime": "09:00:00",
            "availableEndTime": "17:00:00"
        }
    ]
}
{% endhighlight %}

Example Code :

{% highlight java %}
final var node = TBWParserRequestNode.of(json);
Object o;
o = node.valueForKeyPath("id"));
o = node.valueForKeyPath("resourceType"));
o = node.valueForKeyPath("providedBy.reference");
o = node.valueForKeyPath("meta.extension[0].url");
o = node.valueForKeyPath("availableTime[0].extension[0].url"));
o = node.valueForKeyPath("availableTime[0].daysOfWeek[?]"));
o = node.valueForKeyPath("identifier[1].type.coding[0].code"));
{% endhighlight %}

