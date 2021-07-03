---
title: "Python 자료형"
layout: archive
permalink: categories/data_type
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.data_type %}
{% for post in posts reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}