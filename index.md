# Peter writes code and words about code.

## Peter

Peter is a software engineer and tech leader living in Vancouver, Canada. His passion is building great digital products and helping teams become highly effective. He currently works at a startup that develops a chat-driven research platform.



## Code
- [CircleType.js](http://circletype.labwire.ca)
- `@todo write more code.`

## Words
{% for post in site.posts %}- <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><br>{{ post.date | date_to_string }}<br>

{% endfor %}- `@todo write more words.`
