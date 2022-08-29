---
title: "Diary"
layout: archive
permalink: categories/diary
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.Diary %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}