---
title: "Django"
layout: archive
permalink: categories/django
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.django %}
{% for post in posts reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}