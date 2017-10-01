# Peter writes code and words about code.

## Code
- [CircleType.js](http://circletype.labwire.ca)
- `@todo write more code.`

## Words About Code
{% for post in site.posts %}- {{ post.date | date_to_string }} &raquo; <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
{% endfor %}- `@todo write more words.`
