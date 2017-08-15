# Posts

{% for post in site.posts %}
 - [{{ post.title }}]({{ post.url | prepend: site.github.url }} "{{ post.title }}")
{% endfor %}
