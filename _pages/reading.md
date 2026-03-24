---
title: "Reading"
permalink: /reading/
layout: single
author_profile: false
---

A visual bookshelf of books I’ve read. [Goodreads](https://www.goodreads.com)

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

<script>
document.addEventListener("DOMContentLoaded", () => {
  const placeholder = "/assets/images/book-placeholder.png";

  async function fetchGoogleBooksThumbnail(img) {
    const isbn13 = img.dataset.isbn13 || "";
    const isbn10 = img.dataset.isbn10 || "";
    const title = img.dataset.title || "";
    const author = img.dataset.author || "";

    const queries = [];

    if (isbn13) queries.push(`isbn:${isbn13}`);
    if (isbn10) queries.push(`isbn:${isbn10}`);
    if (title && author) queries.push(`intitle:${title}+inauthor:${author}`);
    if (title) queries.push(`intitle:${title}`);

    for (const q of queries) {
      try {
        const url = `https://www.googleapis.com/books/v1/volumes?q=${encodeURIComponent(q)}&maxResults=1`;
        const res = await fetch(url);
        if (!res.ok) continue;

        const data = await res.json();
        const item = data.items && data.items[0];
        const links = item && item.volumeInfo && item.volumeInfo.imageLinks;

        const thumb =
          (links && (links.thumbnail || links.smallThumbnail)) || "";

        if (thumb) {
          return thumb.replace("http://", "https://");
        }
      } catch (e) {
        // ignore and continue
      }
    }

    return null;
  }

  async function attachFallback(img) {
    const tried = new Set();

    async function trySet(src) {
      if (!src || tried.has(src)) return false;
      tried.add(src);

      return new Promise((resolve) => {
        const test = new Image();
        test.onload = () => {
          img.src = src;
          resolve(true);
        };
        test.onerror = () => resolve(false);
        test.src = src;
      });
    }

    const candidates = [
      img.dataset.goodreads,
      img.dataset.openlibrary13,
      img.dataset.openlibrary10,
    ].filter(Boolean);

    for (const src of candidates) {
      const ok = await trySet(src);
      if (ok) return;
    }

    const googleThumb = await fetchGoogleBooksThumbnail(img);
    if (googleThumb) {
      const ok = await trySet(googleThumb);
      if (ok) return;
    }

    img.src = placeholder;
  }

  document.querySelectorAll(".js-book-cover").forEach((img) => {
    attachFallback(img);
  });
});
</script>