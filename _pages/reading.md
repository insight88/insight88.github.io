---
title: "Reading"
permalink: /reading/
layout: single
author_profile: true
---

## Reading

<div class="reading-grid-page">
  <p class="reading-intro">
    Books I’ve read, synced from my Goodreads export.
    <a href="https://www.goodreads.com/review/list/132118626-kipyo-kim?ref=nav_mybooks&shelf=read" target="_blank" rel="noopener">
      View Goodreads
    </a>
  </p>

  <div class="book-grid">
    {% for book in site.data.goodreads %}
      {% assign shelf = book["Exclusive Shelf"] | downcase %}
      {% if shelf == "read" %}
        
        {% assign isbn13 = book["ISBN13"] | strip %}
        {% assign isbn10 = book["ISBN"] | strip %}
        {% assign pubyear = book["Year Published"] | strip %}
        {% assign category = book["Bookshelves"] | strip %}

        {% if isbn13 != "" %}
          {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn13 | append: "-M.jpg?default=false" %}
        {% elsif isbn10 != "" %}
          {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn10 | append: "-M.jpg?default=false" %}
        {% else %}
          {% assign cover_url = "/assets/images/book-placeholder.png" %}
        {% endif %}

        <article class="book-card">
          <a class="book-cover-link"
             href="https://www.goodreads.com/search?q={{ book["Title"] | uri_escape }}"
             target="_blank" rel="noopener">
            <img
              class="book-cover"
              src="{{ cover_url }}"
              alt="Cover of {{ book["Title"] | escape }}"
              loading="lazy"
              onerror="this.onerror=null;this.src='/assets/images/book-placeholder.png';">
          </a>

          <div class="book-meta">
            <h3 class="book-title">{{ book["Title"] }}</h3>
            <p class="book-author">{{ book["Author"] }}</p>

            <div class="book-info-row">
              {% if pubyear != "" %}
                <span class="book-year">{{ pubyear }}</span>
              {% endif %}

              {% if category != "" %}
                <span class="book-category">{{ category }}</span>
              {% else %}
                <span class="book-category">general</span>
              {% endif %}
            </div>
          </div>
        </article>

      {% endif %}
    {% endfor %}
  </div>
</div>