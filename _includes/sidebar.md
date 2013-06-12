<h3>Blog Posts</h3>

<ul>
  {% for post in site.posts limit:6 %}
    <li>
      <span>{{ post.date | date_to_string }}</span> &raquo; 
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
  <li><a href="/posts">See all</a></li>
</ul>