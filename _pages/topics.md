---
title: "Topics"
layout: topics
permalink: /topics/
description: "토픽별로 정리된 포스트들입니다"
---

<!-- 검색 섹션 -->
<section class="search-section">
    <div class="search-container">
        <i class="fas fa-search search-icon"></i>
        <input type="text" class="search-input" placeholder="토픽이나 태그를 검색해보세요..." id="searchInput">
    </div>
</section>

<!-- 토픽 그리드 -->
<section class="topics-grid">
    {% assign normalized_topics = "" | split: "" %}
    {% for topic in site.topics %}
        {% assign topic_name = topic[0] %}
        {% if topic_name.first %}
            {% assign topic_name = topic_name.first %}
        {% endif %}
        {% assign normalized_name = topic_name | downcase %}
        {% unless normalized_topics contains normalized_name %}
            {% assign normalized_topics = normalized_topics | push: normalized_name %}
        {% endunless %}
    {% endfor %}
    
    {% for normalized_name in normalized_topics %}
    {% assign topic_slug = normalized_name | slugify %}
    <a href="/topics/{{ topic_slug }}/" class="category-card {{ topic_slug }}">
        <div class="category-header">
            <div class="category-icon {{ topic_slug }}">
                <i class="{{ site.data.topic_icons[normalized_name] | default: 'fas fa-folder' }}"></i>
            </div>
            <div class="category-info">
                <h3>{{ normalized_name | capitalize }}</h3>
                {% assign topic_posts = 0 %}
                {% for topic in site.topics %}
                    {% assign topic_name = topic[0] %}
                    {% if topic_name.first %}
                        {% assign topic_name = topic_name.first %}
                    {% endif %}
                    {% assign topic_name_downcase = topic_name | downcase %}
                    {% if topic_name_downcase == normalized_name %}
                        {% assign topic_posts = topic_posts | plus: topic[1] | size %}
                    {% endif %}
                {% endfor %}
                <div class="category-count">{{ topic_posts }}개 포스트</div>
            </div>
        </div>
        <div class="category-description">
            {% case normalized_name %}
            {% when 'docker' %}
                컨테이너 기술과 DevOps 관련 포스트
            {% when 'database' %}
                데이터베이스 설계와 관리
            {% when 'spark' %}
                Apache Spark와 빅데이터 처리
            {% when 'algorithm' %}
                알고리즘 문제 풀이와 코딩
            {% when 'ml' %}
                머신러닝과 딥러닝
            {% when 'python' %}
                Python 프로그래밍
            {% when 'projects' %}
                프로젝트 포트폴리오
            {% when 'airflow' %}
                Apache Airflow 워크플로우
            {% when 'kafka' %}
                Apache Kafka 스트리밍
            {% when 'sql' %}
                SQL과 데이터 쿼리
            {% when 'mlops' %}
                MLOps와 모델 운영
            {% when 'fastapi' %}
                FastAPI 웹 개발
            {% when 'linux' %}
                Linux 시스템 관리
            {% when 'general' %}
                일반적인 기술 포스트
            {% else %}
                {{ normalized_name }} 관련 포스트
            {% endcase %}
        </div>
        <div class="category-stats-bar">
            <span class="recent-posts">최근 포스트 {{ topic_posts }}개</span>
            <span class="view-all">전체보기 →</span>
        </div>
    </a>
    {% endfor %}
</section>
