---
layout: post
title:  "Welcome to Jekyll!"
date:   2024-05-24 19:56:05 +0900
categories: jekyll update
tags: classic hollywood
published: true
author: dave
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. 
You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. 
After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

**Jekyll** also offers powerful support for code snippets:

{% highlight shell %}
$ gem install bundler jekyll

$ jekyll new my-awesome-site
$ cd my-awesome-site

$ bundle exec jekyll serve
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. 
File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

More Informationa about [Perma Link][jekyll-permalink].


[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[jekyll-permalink]: https://mademistakes.com/mastering-jekyll/how-to-link/

---

{% assign author = site.data.people[page.author] %}
<a rel="author"
  href="https://twitter.com/{{ author.twitter }}"
  title="{{ author.name }}">
    {{ author.name }}
</a>

---

[TBTextFields](/documentation/TBTextField)

[Blog]({% post_url 2024-05-24-test %})

[string](TBString)

{{ page.title }}  The title of the Page  

{{ page.url }}  The URL of the Post without the domain, but with a leading slash,  




---

#Tag

{% for tag in site.tags %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}

---

{% for tag in site.categories %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}

---

<ul>
{% for member in site.data.members %}
  <li>
    <a href="https://github.com/{{ member.github }}">
      {{ member.name }}
    </a>
  </li>
{% endfor %}
</ul>


![alt](/assets/26824679_m.jpg)



  
