---
layout: page
permalink: /publications/
title: Publications
description:
years: [2023, 2022, 2021, 2020]
nav: true
nav_order: 1
---
<!-- _pages/publications.md -->
\* Equal contribution,
&dagger; Corresponding author.
<div class="publications">
{% assign exclusions = "2020" | split: ":" %}
{%- for y in page.years %}
	{% capture yeartext %}{{ y }}{% endcapture %}
	{% unless exclusions contains yeartext %}
	  <h2 class="year">{{y}}</h2>
	  {% bibliography -f papers -q @*[year={{y}}]* %}
	{% endunless%}
{% endfor %}

</div>
