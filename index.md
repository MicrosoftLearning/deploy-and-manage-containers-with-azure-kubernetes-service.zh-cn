---
title: 联机托管说明
permalink: index.html
layout: home
---

# 内容目录

下面列出了每个实验室练习和演示的超链接。

## 实验室

{% assign Exercise = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| 模块| 练习 |
| --- | --- | 
{% for activity in Exercise  %} | {{ activity.Exercise.module }} | [{{ activity.Exercise.title }}{% if activity.Exercise.type %} - {{ activity.Exercise.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

