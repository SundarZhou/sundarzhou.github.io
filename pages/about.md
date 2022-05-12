---
layout: page
title: About
description: 打码改变世界
keywords: Sundar Zhou
comments: true
menu: 关于
permalink: /about/
---

Sundar, 一个不是在写bug, 就是在写bug路上的人。

成为码农前以为自己很聪明，直到走上写代码这条路之后才知道自己有多笨。

YOLO；大其愿，坚其志，虚其心，柔其气 ；厚积薄发。

## 联系

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
{% if site.url contains 'mazhuang.org' %}
<li>
微信公众号：<br />
<img style="height:192px;width:192px;border:1px solid lightgrey;" src="{{ assets_base_url }}/assets/images/qrcode.jpg" alt="闷骚的程序员" />
</li>
{% endif %}
</ul>


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
