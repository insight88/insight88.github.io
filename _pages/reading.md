---
title: "Reading"
permalink: /reading/
layout: single
author_profile: true
---

## Reading

I keep a curated list of books that have influenced my thinking in neuroscience, machine learning, and philosophy.

You can also find my full reading log on  
👉 [Goodreads](https://www.goodreads.com/review/list/132118626-kipyo-kim?ref=nav_mybooks&shelf=read)

---

## 📚 Books I've Read

{% assign books = site.data.books | where: "status", "read" %}

{% for book in books %}
### {{ book.title }}

- **Author**: {{ book.author }}
{% if book.year %}- **Year**: {{ book.year }}{% endif %}
{% if book.category %}- **Category**: {{ book.category }}{% endif %}
{% if book.rating %}- **Rating**: ⭐ {{ book.rating }}/5{% endif %}

{% if book.note %}
> {{ book.note }}
{% endif %}

{% if book.goodreads %}
[Goodreads link]({{ book.goodreads }})
{% endif %}

---
{% endfor %}