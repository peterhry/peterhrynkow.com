# Peter writes **code** and **words** about code.

## Code
- [CircleType.js](http://circletype.labwire.ca)
- `@todo write more code.`

## Words
{% for post in site.posts %}- <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a> - {{ post.date | date_to_string }}
{% endfor %}- `@todo write more words.`
