---
layout: default
title: not only coffee
---

<h2>{{ page.title }}</h2>
<p>最新文章</p>
<ul>
	{% for post in site.posts %}
	<li>
		<a href="{{ post.url }}">{{ post.title }}</a>.................................{{ post.date | date_to_string }}
	</li>

	{% endfor %}
</ul>
