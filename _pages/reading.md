---
title: "Reading"
permalink: /reading/
layout: single
author_profile: false
---

<div class="reading-grid-page">
  <div class="reading-header">
    <p>
      A visual bookshelf of books I’ve read.
      <a href="https://www.goodreads.com/review/list/132118626-kipyo-kim?ref=nav_mybooks&shelf=read" target="_blank" rel="noopener">
        Goodreads
      </a>
    </p>
  </div>

  <div class="book-grid">
    {% for book in site.data.goodreads %}
      {% assign shelf = book["Exclusive Shelf"] | downcase | strip %}
      {% if shelf == "read" %}

        {% assign title = book["Title"] | strip %}
        {% assign pubyear = book["Year Published"] | strip %}
        {% assign category = book["Bookshelves"] | strip %}
        {% assign isbn13 = book["ISBN13"] | replace: "=", "" | replace: "\"", "" | strip %}
        {% assign isbn10 = book["ISBN"] | replace: "=", "" | replace: "\"", "" | strip %}

        {% if isbn13 != "" %}
          {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn13 | append: "-L.jpg" %}
        {% elsif isbn10 != "" %}
          {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn10 | append: "-L.jpg" %}
        {% else %}
          {% assign cover_url = "/assets/images/book-placeholder.png" %}
        {% endif %}

        <article class="book-card">
          <a
            class="book-cover-link"
            href="https://www.goodreads.com/search?q={{ title | uri_escape }}"
            target="_blank"
            rel="noopener"
          >
            <img
              class="book-cover"
              src="{{ cover_url }}"
              alt="Cover of {{ title | escape }}"
              loading="lazy"
              onerror="this.onerror=null;this.src='/assets/images/book-placeholder.png';"
            >
          </a>

          <div class="book-meta">
            <div class="book-title">{{ title }}</div>

            {% if pubyear != "" %}
              <div class="book-year">{{ pubyear }}</div>
            {% endif %}

            {% if category != "" %}
              <div class="book-category">{{ category }}</div>
            {% else %}
              <div class="book-category">general</div>
            {% endif %}
          </div>
        </article>

      {% endif %}
    {% endfor %}
  </div>
</div>