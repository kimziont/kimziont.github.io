---
title: "[NLP] CNN"
layout: archive
permalink: categories/nlp_cnn
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.nlp_cnn %}
{% for post in posts reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}