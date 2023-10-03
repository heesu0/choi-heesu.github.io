---
title: "Docs"
layout: archive
permalink: categories/docs
author_profile: true
sidebar_main: true
classes: wide
---

***

{% assign posts = site.categories.Docs %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}