---
layout: page
title: zxthirteen的博客
tagline: 慢慢回忆，快快写
---
{% include JB/setup %}

<!-- default and old, no use now
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
no use. end -->

<div class="wrapper" style="background-color: #cccccc">
	<h4> 最新文章 </h4><br />
	<ul class="postsList">{% for post in site.posts limit:10 %}{% include posts-list.html %} {% endfor %}
	<br />
	<li><span>{{ site.posts.size }} POSTS</span><a href="/archive.html">查看全部文章</a></li>
	</ul>
	<br />
	
	<div class="tag-cloud">{% for tag in site.tags %}{% if tag[1].size > 0 %}<a href="/tags.html#{{ tag | first }}-ref">{{ tag | first }}<span>{{ tag[1].size }}</span></a> {% endif %}{% endfor %}
		<br />
		<a href="/tags.html">查看全部Tag<span>{{ site.tags.size }}</span></a>
	</div>
</div>


