
# {{ site.description }}

<a href="{{ site.author.github }}">GitHub</a> /
<a href="{{ site.author.linkedin }}">LinkedIn</a> /
<a href="{{ site.author.stackoverflow }}">Stackoverflow</a> /
<a href="{{ site.author.codepen }}">CodePen</a> /
<a href="{{ site.author.twitter }}">Twitter</a>


## Projects
- [CircleType.js](https://circletype.labwire.ca)<br>

- `@todo write more code.`

## Words
{% for post in site.posts %}- <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><br>{{ post.date | date_to_string }}<br>

{% endfor %}- `@todo write more words.`

![Peter Hrynkow]({{ site.baseurl }}/images/me.jpg){:class="circle-head"}
