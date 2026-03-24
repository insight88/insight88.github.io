---
title: "Reading"
permalink: /reading/
layout: single
author_profile: false
---

A visual bookshelf of books I’ve read. [Goodreads](https://www.goodreads.com)

<style>
/* Expand the content area so the page uses the empty right-side space */
@media (min-width: 80em) {
  .page {
    width: calc(100% - 220px) !important;
    max-width: 1600px !important;
    padding-right: 2rem !important;
  }

  .page__inner-wrap {
    max-width: 1400px !important;
    width: 100% !important;
  }
}

/* Make the reading page use the available width */
.bookshelf-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 28px 18px;
  align-items: start;
  width: 100%;
  margin-top: 1.5rem;
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
  display: block;
  width: 100%;
  aspect-ratio: 2 / 3;
  object-fit: cover;
  border-radius: 6px;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.12);
  background: #f3f3f3;
}

.book-meta {
  margin-top: 10px;
  line-height: 1.4;
}

.book-title {
  font-size: 0.95rem;
  font-weight: 700;
  margin-bottom: 5px;
  word-break: keep-all;
  overflow-wrap: break-word;
}

.book-author,
.book-date-read,
.book-year {
  font-size: 0.8rem;
  color: #666;
  margin-bottom: 2px;
}

/* Tablet */
@media (max-width: 1024px) {
  .bookshelf-grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}

/* Mobile */
@media (max-width: 767px) {
  .bookshelf-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 18px 12px;
  }

  .book-title {
    font-size: 0.88rem;
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

    <article class="book-card">
      <a
        class="book-cover-link"
        href="https://www.goodreads.com/search?q={{ title | uri_escape }}"
        target="_blank"
        rel="noopener"
      >
        <img
          class="book-cover js-book-cover"
          src="{% if gr_cover != '' %}{{ gr_cover }}{% elsif isbn13 != '' %}https://covers.openlibrary.org/b/isbn/{{ isbn13 }}-L.jpg?default=false{% elsif isbn10 != '' %}https://covers.openlibrary.org/b/isbn/{{ isbn10 }}-L.jpg?default=false{% else %}/assets/images/book-placeholder.png{% endif %}"
          alt="Cover of {{ title | escape }}"
          loading="lazy"
          data-goodreads="{% if gr_cover != '' %}{{ gr_cover }}{% endif %}"
          data-openlibrary13="{% if isbn13 != '' %}https://covers.openlibrary.org/b/isbn/{{ isbn13 }}-L.jpg?default=false{% endif %}"
          data-openlibrary10="{% if isbn10 != '' %}https://covers.openlibrary.org/b/isbn/{{ isbn10 }}-L.jpg?default=false{% endif %}"
          data-isbn13="{{ isbn13 }}"
          data-isbn10="{{ isbn10 }}"
          data-title="{{ title | escape }}"
          data-author="{{ author | escape }}"
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