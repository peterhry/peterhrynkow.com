---
title: "Home"
---

# {{ site.description }}

<a href="{{ site.author.github }}">GitHub</a> •
<a href="{{ site.author.linkedin }}">LinkedIn</a> •
<a href="{{ site.author.codepen }}">CodePen</a>

## Writing

<ul class="article-list" hx-boost="true">
  {% for post in site.posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a><br>{{ post.date | date_to_string }}</li>
  {% endfor %}
</ul>

## Projects
<ul class="article-list">
  <li><a href="https://circletype.labwire.ca">CircleType.js</a></li>
</ul>

![Peter Hrynkow]({{ site.baseurl }}/images/me.jpg){:class="circle-head"}
