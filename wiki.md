```
layout: page
title: wiki
descrption: 一些必要的记录
header-img: img/facebook.jpg
```

> 指令那么多，怎么能记住。



<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ site.url }}{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>