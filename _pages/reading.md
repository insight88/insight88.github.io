---
title: "Reading"
permalink: /reading/
layout: single
author_profile: false
---

A visual bookshelf of books I’ve read. [Goodreads](https://www.goodreads.com)

<style>
/* Expand page width */
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

/* Grid */
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

.book-cover-wrap {
  position: relative;
  width: 100%;
  aspect-ratio: 2 / 3;
  border-radius: 6px;
  overflow: hidden;
  background: #f3f3f3;
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.12);
}

.book-cover {
  display: block;
  width: 100%;
  height: 100%;
  aspect-ratio: 2 / 3;
  object-fit: cover;
  background: #f3f3f3;
  color: transparent;
}

.book-cover.is-loading {
  color: transparent;
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

    {% if gr_cover != "" %}
      {% assign initial_cover = gr_cover %}
    {% elsif isbn13 != "" %}
      {% assign initial_cover = "https://covers.openlibrary.org/b/isbn/" | append: isbn13 | append: "-L.jpg?default=false" %}
    {% elsif isbn10 != "" %}
      {% assign initial_cover = "https://covers.openlibrary.org/b/isbn/" | append: isbn10 | append: "-L.jpg?default=false" %}
    {% else %}
      {% assign initial_cover = "/assets/images/book-placeholder.png" %}
    {% endif %}

    <article class="book-card">
      <a
        class="book-cover-link"
        href="https://www.goodreads.com/search?q={{ title | uri_escape }}"
        target="_blank"
        rel="noopener"
      >
        <div class="book-cover-wrap">
          <img
            class="book-cover js-book-cover is-loading"
            src="{{ initial_cover }}"
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
        </div>
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

  function isProbablyKorean(text) {
    return /[ㄱ-ㅎㅏ-ㅣ가-힣]/.test(text || "");
  }

  function normalizeThumbUrl(url) {
    if (!url) return "";
    return url
      .replace("http://", "https://")
      .replace("&edge=curl", "")
      .replace("zoom=1", "zoom=2");
  }

  function markLoaded(img) {
    img.classList.remove("is-loading");
  }

  async function preload(src) {
    return new Promise((resolve) => {
      if (!src) return resolve(false);
      const test = new Image();
      test.onload = () => resolve(true);
      test.onerror = () => resolve(false);
      test.src = src;
    });
  }

  async function fetchGoogleBooksThumbnail(img) {
    const isbn13 = (img.dataset.isbn13 || "").trim();
    const isbn10 = (img.dataset.isbn10 || "").trim();
    const title = (img.dataset.title || "").trim();
    const author = (img.dataset.author || "").trim();

    const isKo = isProbablyKorean(`${title} ${author}`);
    const queries = [];

    if (isbn13) queries.push({ q: `isbn:${isbn13}`, lang: "" });
    if (isbn10) queries.push({ q: `isbn:${isbn10}`, lang: "" });

    if (title && author) {
      queries.push({
        q: `intitle:${title} inauthor:${author}`,
        lang: isKo ? "ko" : ""
      });
    }

    if (title) {
      queries.push({
        q: `intitle:${title}`,
        lang: isKo ? "ko" : ""
      });
    }

    if (author) {
      queries.push({
        q: `inauthor:${author}`,
        lang: isKo ? "ko" : ""
      });
    }

    for (const item of queries) {
      try {
        const params = new URLSearchParams({
          q: item.q,
          maxResults: "5",
          printType: "books"
        });

        if (item.lang) {
          params.set("langRestrict", item.lang);
        }

        const url = `https://www.googleapis.com/books/v1/volumes?${params.toString()}`;
        const res = await fetch(url);
        if (!res.ok) continue;

        const data = await res.json();
        const books = data.items || [];

        for (const b of books) {
          const info = b.volumeInfo || {};
          const links = info.imageLinks || {};

          const candidates = [
            links.extraLarge,
            links.large,
            links.medium,
            links.small,
            links.thumbnail,
            links.smallThumbnail
          ].filter(Boolean);

          for (const c of candidates) {
            const clean = normalizeThumbUrl(c);
            if (clean && await preload(clean)) {
              return clean;
            }
          }
        }
      } catch (e) {
      }
    }

    return null;
  }

  async function attachFallback(img) {
    const candidates = [
      img.dataset.goodreads,
      img.dataset.openlibrary13,
      img.dataset.openlibrary10
    ].filter(Boolean);

    for (const src of candidates) {
      if (await preload(src)) {
        img.src = src;
        markLoaded(img);
        return;
      }
    }

    const googleThumb = await fetchGoogleBooksThumbnail(img);
    if (googleThumb) {
      img.src = googleThumb;
      markLoaded(img);
      return;
    }

    img.src = placeholder;
    markLoaded(img);
  }

  document.querySelectorAll(".js-book-cover").forEach((img) => {
    attachFallback(img);
  });
});
</script>