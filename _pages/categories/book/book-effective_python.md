---
title: "[책] 파이썬 코딩의 기술"
layout: archive
permalink: categories/effective_python
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.effective_python %}
{% for post in posts reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}