---
title: AB-650 lab instructions
permalink: index.html
layout: home
---
{%- assign course = site.data.course -%}
{%- assign start_page = site.pages | where: "path", course.getting_started | first -%}
{%- assign total_labs = 0 -%}
{%- assign total_minutes = 0 -%}
{%- for lp in course.learning_paths -%}{%- for lab in lp.labs -%}
{%- assign p = site.pages | where: "path", lab.file | first -%}
{%- assign total_labs = total_labs | plus: 1 -%}
{%- assign total_minutes = total_minutes | plus: p.lab.duration -%}
{%- endfor -%}{%- endfor -%}
<link rel="stylesheet" href="{{ '/assets/course/course.css' | relative_url }}?v={{ site.github.build_revision }}">
<div class="course-index" markdown="0">
<h1>AB-650: Administer Microsoft 365 and AI services</h1>
<p class="course-intro">These hands-on labs give you practice configuring, securing, and governing Microsoft 365 tenants, workloads, Microsoft 365 Copilot, and agents. They complement the AB-650 learning paths on Microsoft Learn.</p>
<div class="course-start">
<div><strong>New to these labs?</strong>Read about the lab environment, the AllFiles (F:) drive, and how the labs build on each other.</div>
<a class="course-button" href="{{ start_page.url | relative_url }}">Get started</a>
</div>
<p class="course-totals">{{ total_labs }} labs &middot; {{ total_minutes }} minutes of hands-on practice &middot; Complete the labs in order. They build on one Microsoft 365 E7 lab tenant, and no Azure subscription is required.</p>
{%- for lp in course.learning_paths %}
{%- assign lp_minutes = 0 -%}
{%- for lab in lp.labs -%}{%- assign p = site.pages | where: "path", lab.file | first -%}{%- assign lp_minutes = lp_minutes | plus: p.lab.duration -%}{%- endfor %}
<details class="course-lp" id="learning-path-{{ lp.number }}" open>
<summary>Learning path {{ lp.number }}: {{ lp.title }}<span class="lp-meta">{{ lp.labs.size }} labs &middot; {{ lp_minutes }} minutes</span></summary>
<div class="lp-body">
<p class="lp-learn">Related training on Microsoft Learn: <a href="{{ lp.learn_url }}">{{ lp.title }}</a></p>
{%- for lab in lp.labs %}
{%- assign p = site.pages | where: "path", lab.file | first %}
<div class="lab-card" id="lab-{{ lab.number }}">
<h3><a href="{{ p.url | relative_url }}">{{ p.lab.title | replace: ' - ', ': ' }}</a></h3>
<span class="course-badge course-badge-time">{{ p.lab.duration }} minutes</span><span class="course-badge">Level {{ p.lab.level }}</span><span class="course-badge">{{ lab.exercises.size }} exercises</span>
<p>{{ p.lab.description }}</p>
<ol class="lab-exercises">
{%- for ex in lab.exercises %}
<li>{{ ex.title }} <span class="ex-min">({{ ex.minutes }} min)</span></li>
{%- endfor %}
</ol>
{%- if lab.files.size > 0 %}
<div class="lab-files">Lab files: {% for f in lab.files %}<code>F:\{{ f | replace: '/', '\' }}</code>{% unless forloop.last %}, {% endunless %}{% endfor %}</div>
{%- endif %}
</div>
{%- endfor %}
</div>
</details>
{%- endfor %}
</div>
<script src="{{ '/assets/course/course.js' | relative_url }}?v={{ site.github.build_revision }}"></script>
