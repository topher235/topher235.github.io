---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Home
---

# Blog Posts
{% for post in site.posts %}
 
<ol>
 
<li>
	<h3>
		<a href="{{ post.url | relative_url }}">{{ post.title }}</a>
	</h3>
	<p>A devlog detailing features that went into creating a Pokemon Emerald mod to propose to my partner.</p>
</li>
 
</ol>
{% endfor %}
