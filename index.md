# Peter writes code and words about code.

Peter is a tech leader and software engineer living in Vancouver, Canada. His passion is building great digital products and coaching agile teams. He currently works for a startup developing a chat-driven market research platform.

![Peter Hrynkow]({{ site.baseurl }}/images/me.jpg){:class="circle-head"}

## Code
- [CircleType.js](http://circletype.labwire.ca)
- `@todo write more code.`

## Words
{% for post in site.posts %}- <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><br>{{ post.date | date_to_string }}<br>

{% endfor %}- `@todo write more words.`
