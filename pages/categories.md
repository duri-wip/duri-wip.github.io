---
layout: page
title: "Categories"
permalink: /categories/
---

<!-- 검색 섹션 -->
<section class="search-section">
    <div class="search-container">
        <i class="fas fa-search search-icon"></i>
        <input type="text" class="search-input" placeholder="카테고리나 태그를 검색해보세요..." id="searchInput">
    </div>
</section>

<!-- 카테고리 그리드 -->
<section class="categories-grid">
    {% for category in site.categories %}
    <a href="/category/{{ category[0] | slugify }}/" class="category-card {{ category[0] | slugify }}">
        <div class="category-header">
            <div class="category-icon {{ category[0] | slugify }}">
                <i class="{{ site.data.category_icons[category[0]] | default: 'fas fa-folder' }}"></i>
            </div>
            <div class="category-info">
                <h3>{{ category[0] | capitalize }}</h3>
                <div class="category-count">{{ category[1] | size }}개 포스트</div>
            </div>
        </div>
        <!-- 나머지 카테고리 정보... -->
    </a>
    {% endfor %}
</section>
