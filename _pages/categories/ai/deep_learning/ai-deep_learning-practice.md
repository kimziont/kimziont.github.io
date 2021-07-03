---
title: "딥러닝 활용편"
layout: archive
permalink: categories/dl_practice
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.dl_practice %}
{% for post in posts reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}