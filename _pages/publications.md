---
layout: page
permalink: /publications/
title: publications
description: 
nav: true
nav_order: 1
---
<!-- _pages/publications.md -->
<div class="publications">

Reprints may be downloaded for personal use by clicking on the icon beside each reference. In doing so, you agree to use reprints exclusively "for purposes such as criticism, comment, news reporting, teaching (including multiple copies for classroom use), scholarship, or research" (United States copyright law, Title 17, United States Code, Section 107).

<div style="margin-bottom: 2em;"></div>

{% capture under_review %}{% bibliography -f papers -q @*[under_review=true]* %}{% endcapture %}
{% if under_review contains "<li" %}
<h2 class="bibliography">under review</h2>
{{ under_review }}
{% endif %}

{% bibliography -f papers -q @*[under_review!=true]* --group_by year --group_order descending %}

</div>
