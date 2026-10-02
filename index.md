---
title: AB-650 lab instructions
permalink: index.html
layout: home
---

This page lists the hands-on labs for course **AB-650: Administer Microsoft 365 and AI services**. The labs build on each other in one Microsoft 365 E7 lab tenant, so complete them in order.

> **Note**: To complete these labs, you need the hosted lab environment provided by your lab hoster, including the Microsoft 365 tenant, sample users, and the SEA-DEV1 and SEA-DEV2 virtual machines. The labs don't require an Azure subscription.

<hr>

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
{% for activity in labs  %}
{% if activity.lab.title %}
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})


{% if activity.lab.level %}**Level**: {{activity.lab.level}} \| {% endif %}{% if activity.lab.duration %}**Duration**: {{activity.lab.duration}}{% endif %}

{% if activity.lab.description %}
*{{activity.lab.description}}*
{% endif %}
<hr>
{% endif %}
{% endfor %}
