---
layout: page
title: "Study Progress"
permalink: /study/
---

<!-- 페이지 헤더 -->
<header class="page-header">
    <h1 class="page-title">Study Progress</h1>
    <p class="page-subtitle">
        꾸준한 학습을 통해 성장하는 과정을 기록합니다. 
        스터디 태그가 달린 포스트들로 진행 상황을 추적해보세요.
    </p>
</header>

<!-- 스터디 통계 -->
<section class="study-stats">
    {% assign study_posts = site.posts | where_exp: "post", "post.tags contains 'study'" %}
    {% assign study_groups = study_posts | group_by: 'study_name' %}
    {% assign in_progress_studies = study_groups | where_exp: "group", "group.items.last.study_status != 'completed'" %}
    {% assign completed_studies = study_groups | where_exp: "group", "group.items.last.study_status == 'completed'" %}
    
    <div class="stat-card">
        <div class="stat-icon">
            <i class="fas fa-play"></i>
        </div>
        <div class="stat-number">{{ in_progress_studies.size | default: 2 }}</div>
        <div class="stat-label">진행중인 스터디</div>
    </div>

    <div class="stat-card">
        <div class="stat-icon">
            <i class="fas fa-check"></i>
        </div>
        <div class="stat-number">{{ completed_studies.size | default: 3 }}</div>
        <div class="stat-label">완료한 스터디</div>
    </div>

    <div class="stat-card">
        <div class="stat-icon">
            <i class="fas fa-file-alt"></i>
        </div>
        <div class="stat-number">{{ study_posts.size | default: 15 }}</div>
        <div class="stat-label">스터디 포스트</div>
    </div>

    <div class="stat-card">
        <div class="stat-icon">
            <i class="fas fa-clock"></i>
        </div>
        {% assign total_reading_time = 0 %}
        {% for post in study_posts %}
            {% assign reading_time = post.reading_time | default: "5분" | replace: "분", "" | plus: 0 %}
            {% assign total_reading_time = total_reading_time | plus: reading_time %}
        {% endfor %}
        <div class="stat-number">{{ total_reading_time | default: 120 }}</div>
        <div class="stat-label">총 학습 시간(분)</div>
    </div>

</section>

<!-- 타임라인 -->
<section class="timeline-section">
    <h2 class="section-title">
        <i class="fas fa-history"></i>
        학습 타임라인
    </h2>

    <div class="timeline">
        {% if study_groups.size > 0 %}
            {% comment %}
            실제 포스트 데이터로 타임라인 생성
            {% endcomment %}
            {% for group in study_groups %}
            {% assign latest_post = group.items.first %}
            {% assign total_posts = group.items.size %}
            {% assign completed_posts = group.items | where: 'study_status', 'completed' | size %}
            {% assign progress = completed_posts | times: 100.0 | divided_by: total_posts | round %}

            {% if latest_post.study_status == 'completed' %}
                {% assign status_class = 'completed' %}
                {% assign status_text = '완료' %}
                {% assign status_icon = 'check' %}
            {% elsif latest_post.study_status == 'planned' %}
                {% assign status_class = 'planned' %}
                {% assign status_text = '예정' %}
                {% assign status_icon = 'calendar' %}
            {% else %}
                {% assign status_class = 'in-progress' %}
                {% assign status_text = '진행중' %}
                {% assign status_icon = 'play' %}
            {% endif %}

            <div class="timeline-item">
                <div class="timeline-dot {{ status_class }}">
                    <i class="fas fa-{{ status_icon }}"></i>
                </div>
                <div class="timeline-content">
                    <div class="study-header">
                        <div>
                            <h3 class="study-title">{{ group.name | default: "일반 스터디" }}</h3>
                            <div class="study-period">
                                {{ group.items.last.date | date: "%Y.%m.%d" }} -
                                {% if status_class == 'completed' %}
                                    {{ latest_post.date | date: "%Y.%m.%d" }}
                                {% else %}
                                    진행중
                                {% endif %}
                            </div>
                        </div>
                        <div class="study-status {{ status_class }}">{{ status_text }}</div>
                    </div>

                    <p class="study-description">
                        {{ latest_post.study_description | default: latest_post.excerpt | strip_html | truncatewords: 30 }}
                    </p>

                    <div class="study-progress">
                        <div class="progress-header">
                            <span class="progress-label">진행률</span>
                            <span class="progress-percentage">{{ progress }}%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress-fill" style="width: {{ progress }}%"></div>
                        </div>
                    </div>

                    <div class="study-technologies">
                        {% assign unique_tags = group.items | map: 'tags' | join: ',' | split: ',' | uniq | sort %}
                        {% for tag in unique_tags limit: 6 %}
                        {% unless tag == 'study' %}
                        <span class="tech-tag">{{ tag | strip }}</span>
                        {% endunless %}
                        {% endfor %}
                    </div>

                    <div class="study-links">
                        <a href="/tags/study/" class="study-link">
                            <i class="fas fa-book"></i>
                            전체 스터디 포스트
                        </a>
                        {% if group.name %}
                        <a href="/tags/{{ group.name | slugify }}/" class="study-link">
                            <i class="fas fa-tag"></i>
                            {{ group.name }} 포스트
                        </a>
                        {% endif %}
                        {% if latest_post.study_github %}
                        <a href="{{ latest_post.study_github }}" class="study-link">
                            <i class="fab fa-github"></i>
                            GitHub 저장소
                        </a>
                        {% endif %}
                    </div>

                    <!-- 최근 포스트들 미리보기 -->
                    <div class="study-recent-posts">
                        <h4>최근 포스트 ({{ total_posts }}개)</h4>
                        <div class="recent-posts-list">
                            {% for post in group.items limit: 3 %}
                            <div class="recent-post-item">
                                <a href="{{ post.url }}" class="recent-post-link">
                                    <div class="recent-post-title">{{ post.title }}</div>
                                    <div class="recent-post-date">{{ post.date | date: "%Y.%m.%d" }}</div>
                                </a>
                            </div>
                            {% endfor %}
                        </div>
                    </div>
                </div>
            </div>
            {% endfor %}
        {% else %}
            {% comment %}
            스터디 포스트가 없는 경우 기본 예시
            {% endcomment %}
            <div class="timeline-item">
                <div class="timeline-dot in-progress">
                    <i class="fas fa-play"></i>
                </div>
                <div class="timeline-content">
                    <div class="study-header">
                        <div>
                            <h3 class="study-title">스터디를 시작해보세요!</h3>
                            <div class="study-period">언제든지 시작할 수 있어요</div>
                        </div>
                        <div class="study-status planned">시작 전</div>
                    </div>

                    <p class="study-description">
                        아직 스터디 포스트가 없습니다. 새로운 학습을 시작하고 포스트에 'study' 태그와 'study_name' 속성을 추가해보세요!
                    </p>

                    <div class="study-progress">
                        <div class="progress-header">
                            <span class="progress-label">진행률</span>
                            <span class="progress-percentage">0%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress-fill" style="width: 0%"></div>
                        </div>
                    </div>

                    <div class="study-technologies">
                        <span class="tech-tag">study</span>
                        <span class="tech-tag">learning</span>
                        <span class="tech-tag">progress</span>
                    </div>

                    <div class="study-links">
                        <a href="#" class="study-link">
                            <i class="fas fa-plus"></i>
                            첫 스터디 포스트 작성하기
                        </a>
                    </div>
                </div>
            </div>
        {% endif %}
    </div>

</section>

<!-- 스터디 포스트 작성 가이드 -->
<section class="study-guide-section">
    <div class="guide-card">
        <h3><i class="fas fa-lightbulb"></i> 스터디 포스트 작성 가이드</h3>
        <p>스터디 진행 상황을 추적하려면 포스트에 다음과 같이 설정하세요:</p>
        
        <div class="guide-code">
            <pre><code>---
layout: post
title: "스터디 포스트 제목"
tags: [study, python, algorithm]  # study 태그 필수
study_name: "Python 알고리즘"    # 스터디 그룹명
study_status: "in-progress"      # completed, in-progress, planned
study_description: "알고리즘 문제 해결 능력 향상을 위한 학습"
study_github: "https://github.com/..."  # (선택사항)
---</code></pre>
        </div>
        
        <div class="guide-tips">
            <h4>💡 팁</h4>
            <ul>
                <li><code>study_name</code>이 같은 포스트들은 하나의 스터디로 그룹화됩니다</li>
                <li><code>study_status</code>를 "completed"로 설정하면 완료된 포스트로 계산됩니다</li>
                <li>진행률은 완료된 포스트 수 / 전체 포스트 수로 자동 계산됩니다</li>
            </ul>
        </div>
    </div>
</section>
