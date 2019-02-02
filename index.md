
# {{ site.description }}

<a href="{{ site.author.github }}">GitHub</a> •
<a href="{{ site.author.twitter }}">Twitter</a> •
<a href="{{ site.author.linkedin }}">LinkedIn</a> •
<a href="{{ site.author.codepen }}">CodePen</a> •
<a href="{{ site.author.stackoverflow }}">Stackoverflow</a>

## Projects
<ul class="article-list">
  <li><a href="https://circletype.labwire.ca">CircleType.js</a></li>
</ul>
## Words

<ul class="article-list">
  {% for post in site.posts %}
    <li><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><br>{{ post.date | date_to_string }}</li>
  {% endfor %}
</ul>

![Peter Hrynkow]({{ site.baseurl }}/images/me.jpg){:class="circle-head"}
