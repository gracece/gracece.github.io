---
layout: default
title: Blog Archive
---
<div class="page-content wc-container">
  <h1 class="page-title">Archive</h1>
  {% for post in site.posts %}
  	{% capture currentyear %}{{post.date | date: "%Y"}}{% endcapture %}
  	{% if currentyear != year %}
    	{% unless forloop.first %}</ul>{% endunless %}
    		<h5 class="archive-year">{{ currentyear }}</h5>
    		<ul class="archive-list">
    		{% capture year %}{{currentyear}}{% endcapture %}
  		{% endif %}
    <li class="archive-item">
      <time>{{ post.date | date: "%m.%d" }}</time>
      <a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
    </li>
    {% if forloop.last %}</ul>{% endif %}
  {% endfor %}
</div>