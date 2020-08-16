---

# Feel free to add content and custom Front Matter to this file.

# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Home
---

<!-- <link href="https://fonts.googleapis.com/css?family=Prompt&display=swap" rel="stylesheet">
<link rel="stylesheet" href="{{'/assets/css/style_font.css'}}" /> -->
<link rel="stylesheet" href="{{'/assets/css/style_tags.css'}}" />
<link rel="stylesheet" href="{{'/public/css/style_posts.css'}}" />
{% include collecttags.html %}

<!-- <div class="tags_div">

      Tags: 
      <br>
      {% for tag in tags %}
            <a class="tag yellow"
              href="{{ site.baseurl | append: "/tag/" | append: tag | append: "/" }}"
              rel="category tag">
              {{ tag }}
            </a>
      {% endfor %}

</div>
<br> -->
<h1> Posts </h1>
<hr>
<div class="posts">
  {% for post in site.posts %}

<section class="taxonomy-section post">
  <div class="entries-list">
  <article class="entry h-entry">
    <header class="entry-header">
      <h3 class="entry-title p-name">
        <a href="{{ post.url | absolute_url }}" rel="bookmark">{{ post.title }}</a>
      </h3>
    </header>
    {% if post.description %}
      <div class="entry-excerpt p-summary">
        <p>{{ post.description }}</p>
      </div>
    {% endif %}
  
  <footer class="entry-meta">
    {% if post.readmin %}
    <span class="read-time">~ {{post.readmin}} min read</span> || 
    {% endif %}
    <time class="entry-date dt-published">{{ post.date | date_to_string }} </time>
    <div class="tags_div tags_post">
      {% for tag in post.tags %}
            <a class="tag red"
              href="{{ site.baseurl | append: "/tag/" | append: tag | append: "/" }}"
              rel="category tag">
              {{ tag }}
            </a>
      {% endfor %}
    </div>
  </footer>
    
  </article>

  </div>

  <!-- <a href="#page-title" class="back-to-top">Back to Top ↑</a> -->

</section>

  <!-- <div class="post">

    <h1 class="post-title">
      <a href="{{ post.url | absolute_url }}">
        {{ post.title }}
      </a>
    </h1>
    <span class="post-date">{{ post.date | date_to_string }}</span>
    <div class="tags_div tags_post">
      {% for tag in post.tags %}
            <a class="tag red"
              href="{{ site.baseurl | append: "/tag/" | append: tag | append: "/" }}"
              rel="category tag">
              {{ tag }}
            </a>
      {% endfor %}
    </div>

  </div> -->

  {% endfor %}
</div>

<!-- <div class="pagination">
  {% if paginator.next_page %}

    <a class="pagination-item older" href="{{ paginator.next_page_path | absolute_url }}">Older</a>

  {% else %}

    <span class="pagination-item older">Older</span>

  {% endif %}
  {% if paginator.previous_page %}

    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="{{ '/' | absolute_url }}">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="{{ paginator.previous_page_path | absolute_url }}">Newer</a>
    {% endif %}

  {% else %}

    <span class="pagination-item newer">Newer</span>

  {% endif %}
</div>
 -->
