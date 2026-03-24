---
title: "Reading"
permalink: /reading/
layout: single
author_profile: false
---

A visual bookshelf of books I’ve read. [Goodreads](https://www.goodreads.com)

<style>
.bookshelf-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 18px 16px;
  align-items: start;
  margin-top: 1.2rem;
}

.book-card {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.book-cover-link {
  display: block;
  text-decoration: none;
}

.book-cover {
  width: 100%;
  aspect-ratio: 2 / 3;
  object-fit: cover;
  border-radius: 6px;
  box-shadow: 0 3px 10px rgba(0,0,0,0.12);
  background: #f3f3f3;
}

.book-meta {
  margin-top: 8px;
  line-height: 1.35;
}

.book-title {
  font-size: 0.92rem;
  font-weight: 700;
  margin-bottom: 4px;
  word-break: keep-all;
}

.book-author,
.book-date-read,
.book-year {
  font-size: 0.78rem;
  color: #666;
  margin-bottom: 2px;
}

@media (min-width: 1200px) {
  .bookshelf-grid {
    grid-template-columns: repeat(5, minmax(0, 1fr));
  }
}

@media (max-width: 767px) {
  .bookshelf-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 14px 12px;
  }

  .book-title {
    font-size: 0.86rem;
  }

  .book-author,
  .book-date-read,
  .book-year {
    font-size: 0.74rem;
  }
}
</style>

<div class="bookshelf-grid">
{% for book in site.data.goodreads %}
  {% assign shelf = book["Exclusive Shelf"] | downcase | strip %}
  {% if shelf == "read" %}

    {% assign title = book["Title"] | strip %}
    {% assign pubyear = book["Year Published"] | strip | replace: ".0", "" %}
    {% assign author = book["Author"] | strip %}
    {% assign date_read = book["Date Read"] | strip %}
    {% assign isbn13 = book["ISBN13"] | replace: "=", "" | replace: "\"", "" | strip %}
    {% assign isbn10 = book["ISBN"] | replace: "=", "" | replace: "\"", "" | strip %}
    {% assign gr_cover = book["Image URL"] | strip %}

    {% if gr_cover != "" %}
      {% assign cover_url = gr_cover %}
    {% elsif isbn13 != "" %}
      {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn13 | append: "-L.jpg?default=false" %}
    {% elsif isbn10 != "" %}
      {% assign cover_url = "https://covers.openlibrary.org/b/isbn/" | append: isbn10 | append: "-L.jpg?default=false" %}
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
          onerror="
            this.onerror=null;
            if (this.src.includes('openlibrary')) {
              this.src='https://books.google.com/books/content?vid=ISBN{{ isbn13 | default: isbn10 }}&printsec=frontcover&img=1&zoom=1';
            } else {
              this.src='/assets/images/book-placeholder.png';
            }
          "
        >
      </a>

      <div class="book-meta">
        <div class="book-title">{{ title }}</div>

        {% if author != "" %}
          <div class="book-author">{{ author }}</div>
        {% endif %}

        {% if date_read != "" %}
          <div class="book-date-read">{{ date_read }}</div>
        {% endif %}

        {% if pubyear != "" %}
          <div class="book-year">{{ pubyear }}</div>
        {% endif %}
      </div>
    </article>

  {% endif %}
{% endfor %}
</div>