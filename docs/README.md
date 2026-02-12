# Blog Architecture Documentation

블로그 구조와 기능을 AI Agent가 이해하고 수정할 수 있도록 작성된 문서입니다.

## 📁 Documentation Files

### 1. [blog-architecture.yml](blog-architecture.yml)
**종합 아키텍처 문서 (약 1,200줄)**

블로그의 모든 구조와 기능을 상세히 설명하는 메인 문서입니다.

**포함 내용**:
- 전체 디렉토리 구조 및 각 파일의 용도
- Jekyll 설정 상세 설명
- Front matter 템플릿 (블로그 포스트, 프로젝트, 스터디)
- 컴포넌트 레퍼런스 (레이아웃, includes, CSS)
- 기능 구현 상세 (카테고리 필터링, 스터디 진행도, 프로젝트 갤러리)
- 디자인 시스템 (색상, 타이포그래피, 레이아웃)
- 데이터 파일 구조 (categories.yml, studies.yml, projects.yml)
- Agent 수정 가이드라인
- 일반 작업 예시

**사용 시기**:
- 블로그 구조를 처음 이해할 때
- 복잡한 기능 구현이 필요할 때
- 특정 컴포넌트의 동작 방식을 상세히 알아야 할 때
- 디자인 시스템을 이해해야 할 때

### 2. [quick-reference.yml](quick-reference.yml)
**빠른 참조 문서 (약 200줄)**

자주 사용하는 정보를 빠르게 찾을 수 있는 요약 문서입니다.

**포함 내용**:
- 주요 파일 경로
- Front matter 템플릿
- 카테고리 슬러그 목록
- 활성 스터디 태그 목록
- CSS 색상 변수
- 일반 명령어
- 체크리스트

**사용 시기**:
- 파일 경로를 빠르게 찾을 때
- Front matter 템플릿이 필요할 때
- 카테고리나 스터디 태그를 확인할 때
- 일상적인 작업 수행 시

### 3. [agent-guidelines.md](agent-guidelines.md)
**Agent 작업 가이드 (약 450줄)**

AI Agent가 블로그를 안전하게 수정하는 방법을 단계별로 설명합니다.

**포함 내용**:
- 문서 사용 방법
- 일반적인 수정 시나리오 (단계별 가이드)
  - 블로그 포스트 추가
  - 스터디 포스트 추가
  - 새 스터디 프로그램 생성
  - 카테고리 추가
  - 사이트 색상 변경
- 안전성 가이드라인
- 검증 체크리스트
- 테스트 워크플로우
- 흔한 실수와 해결 방법
- 스터디 진행도 시스템 이해
- Liquid 템플릿 빠른 참조

**사용 시기**:
- 블로그를 처음 수정할 때
- 특정 작업의 올바른 절차를 확인할 때
- 안전하게 수정 가능한 범위를 확인할 때
- 검증 방법을 알아야 할 때

### 4. [README.md](README.md) (이 파일)
**문서 색인**

모든 문서의 개요와 사용법을 설명합니다.

## 🚀 Quick Start for Agents

### 새로운 Agent가 작업을 시작할 때

1. **이 README 읽기** (지금 하고 있는 것!)
2. **[agent-guidelines.md](agent-guidelines.md)** 읽고 작업 방법 이해
3. **[quick-reference.yml](quick-reference.yml)** 북마크 - 자주 참조할 것
4. 필요할 때 **[blog-architecture.yml](blog-architecture.yml)** 상세 정보 확인

### 일반적인 작업 흐름

```
사용자 요청 받음
    ↓
quick-reference.yml에서 관련 템플릿/경로 확인
    ↓
필요시 blog-architecture.yml에서 상세 정보 확인
    ↓
agent-guidelines.md의 시나리오 참조
    ↓
파일 읽기 (절대 추측하지 말 것!)
    ↓
수정 실행
    ↓
검증 체크리스트 확인
    ↓
완료
```

## 📋 문서 사용 예시

### 예시 1: "Docker에 대한 블로그 포스트 추가"

```
1. quick-reference.yml 열기
   → categories 섹션에서 Development → devops-infra 확인
   → templates.blog_post에서 템플릿 복사

2. agent-guidelines.md 열기
   → "Scenario 1: Add New Blog Post" 섹션 참조
   → 단계별 가이드 따라하기

3. 포스트 작성
   → _posts/Development/devops-infra/2026-01-10-docker.md 생성
   → Front matter 작성
   → 내용 작성

4. 검증
   → agent-guidelines.md의 체크리스트 확인
```

### 예시 2: "Java 스터디 포스트 추가"

```
1. quick-reference.yml 열기
   → study_tags에서 "study-java" 확인
   → templates.study 템플릿 복사

2. agent-guidelines.md 열기
   → "Scenario 2: Add Study Post" 참조

3. 포스트 작성
   → _studies/Study/java/2026-01-10-arrays.md 생성
   → tags에 study-java 포함 필수!

4. 진행도 자동 업데이트 확인
```

### 예시 3: "사이트 색상 변경"

```
1. quick-reference.yml 열기
   → colors 섹션에서 현재 색상 확인

2. agent-guidelines.md 열기
   → "Scenario 5: Modify Site Colors" 참조

3. blog-architecture.yml 참조 (필요시)
   → design_system.color_palette 섹션 확인

4. assets/css/main.css 수정
   → :root의 --accent-primary 변경

5. 모든 컴포넌트 자동 업데이트됨
```

### 예시 4: "새 스터디 프로그램 시작 (Python 학습)"

```
1. agent-guidelines.md 열기
   → "Scenario 3: Create New Study Program" 참조

2. blog-architecture.yml 참조
   → content_organization.tags.study_tags 섹션 확인
   → data_files._data.studies.yml 스키마 확인

3. _data/studies.yml 수정
   → 새 스터디 추가 (study_tag: "study-python")

4. _studies/Study/python/ 디렉토리 생성

5. 향후 포스트에서 study-python 태그 사용
```

## 🏗️ 블로그 구조 개요

### 컨텐츠 타입
- **블로그 포스트** (`_posts/`): 기술 블로그 글 (73개)
- **프로젝트** (`_projects/`): 포트폴리오 프로젝트 (3개)
- **스터디** (`_studies/`): 학습 자료 및 알고리즘 (40개)

### 핵심 특징
- **계층적 카테고리**: 카테고리 → 서브카테고리 (5개 메인, 15개 서브)
- **스터디 진행도 추적**: 태그 기반 자동 계산
- **레트로 디자인**: Mac Paint 스타일 픽셀 아트 느낌
- **자동 배포**: GitHub Actions를 통한 CI/CD

### 주요 페이지
- **홈페이지** (`index.html`): 최근 포스트, 스터디 진행도
- **카테고리** (`categories.html`): JavaScript 필터링
- **스터디** (`study.html`): 진행도 바, 모달 상세보기
- **프로젝트** (`projects.html`): 갤러리, 정렬/필터링

## 🔧 로컬 개발 환경

### 설치
```bash
bundle install
```

### 로컬 서버 실행
```bash
bundle exec jekyll serve
```

### 접속
```
http://localhost:4000
```

## ⚙️ 배포 프로세스

### 자동 배포
1. `main` 또는 `master` 브랜치에 푸시
2. GitHub Actions가 Jekyll 빌드 실행
3. `duri-wip/duri-wip.github.io` 저장소로 배포
4. 2-3분 후 https://duri-wip.github.io 에서 확인

### 주의사항
- **절대 `duri-wip.github.io` 저장소를 직접 수정하지 말 것**
- 소스 저장소만 수정하면 자동으로 배포됨

## 📊 중요 개념

### 스터디 진행도 시스템
```
_data/studies.yml:
  study_tag: "study-java"
  total: 10

포스트에 "study-java" 태그 포함:
  _studies/Study/java/2026-01-10-loops.md
  tags: [study-java, ...]

진행도 자동 계산:
  - study-java 태그가 있는 포스트 수 카운트
  - (카운트 / total) * 100 = 진행률
  - 홈페이지와 스터디 페이지에 표시
```

**핵심**: 수동 업데이트 불필요, 태그만 정확히 매칭하면 자동!

### 카테고리 시스템
```
_data/categories.yml 정의:
  - name: "ML-AI"
    slug: "ml-ai"
    subcategories:
      - name: "LLM"
        slug: "llm"

포스트 작성:
  categories: [ML-AI]
  subcategory: llm

폴더 구조:
  _posts/ML&AI/llm/2025-07-28-LCEL3.md
```

## 🎨 디자인 시스템

### 색상
- **액센트**: `#3fd166` (구글 그린)
- **배경**: `#ffffff` (순백)
- **텍스트**: `#1a1a1a` (거의 검정)
- **테두리**: `#bdc1c6` (회색)

### 폰트
- **영문**: Courier New, Monaco (monospace)
- **한글**: JoseonGulim (조선 글림)
- **크기**: 12px base

### 스타일
- 3D 윈도우 프레임 효과
- 픽셀 렌더링
- 점 패턴 배경
- 미니멀리즘

## 📝 파일 수정 안전성

### ✅ 안전하게 수정 가능
- `_posts/`, `_projects/`, `_studies/` 내 콘텐츠 파일
- `assets/images/` 내 이미지
- 개별 CSS 파일 (변수 제외)
- `_data/` YAML 파일 (검증 후)

### ⚠️ 주의해서 수정
- `_config.yml` (Jekyll 재빌드 필요)
- `_layouts/`, `_includes/` (모든 페이지에 영향)
- `assets/css/main.css` (전역 변수)
- HTML 파일 내 JavaScript

### ❌ 수정 금지
- `Gemfile`, `Gemfile.lock`
- `.github/workflows/deploy.yml`

## 🔍 문제 해결

### "카테고리가 표시되지 않음"
→ `_data/categories.yml`에 정의되어 있는지 확인
→ 포스트의 `categories`와 `subcategory`가 정확히 매칭되는지 확인

### "스터디 진행도가 업데이트되지 않음"
→ 포스트 `tags`에 `study-*` 태그가 포함되어 있는지 확인
→ `_data/studies.yml`의 `study_tag`와 정확히 일치하는지 확인

### "이미지가 표시되지 않음"
→ 경로가 `/assets/images/...`로 시작하는지 확인
→ 파일이 실제로 존재하는지 확인

### "로컬에서 빌드 오류"
→ YAML 문법 오류 확인 (들여쓰기, 콜론, 따옴표)
→ `bundle exec jekyll serve` 출력 메시지 확인

## 📚 추가 자료

### Jekyll 공식 문서
- https://jekyllrb.com/docs/

### Liquid 템플릿 언어
- https://shopify.github.io/liquid/

### Kramdown 마크다운
- https://kramdown.gettalong.org/

### GitHub Pages
- https://docs.github.com/en/pages

## 🆘 도움이 필요할 때

1. **이 문서들을 먼저 확인**
   - agent-guidelines.md의 해당 시나리오
   - blog-architecture.yml의 상세 정보

2. **기존 파일 참조**
   - 비슷한 포스트/프로젝트/스터디 파일 확인
   - 동일한 패턴 따라하기

3. **파일을 읽어보기**
   - 절대 추측하지 말고 실제 파일 내용 확인

4. **로컬에서 테스트**
   - `bundle exec jekyll serve`로 확인

## 📝 문서 업데이트

이 문서들은 블로그 구조가 변경될 때마다 업데이트되어야 합니다.

### 업데이트가 필요한 경우
- 새로운 카테고리 추가
- 새로운 기능 구현
- 레이아웃 구조 변경
- 디자인 시스템 변경
- 배포 프로세스 변경

### 업데이트 방법
1. 해당 변경사항을 `blog-architecture.yml`에 반영
2. `quick-reference.yml` 업데이트 (필요시)
3. `agent-guidelines.md`에 새로운 시나리오 추가 (필요시)
4. 이 README 업데이트 (필요시)
5. 문서 버전 날짜 업데이트

---

**Last Updated**: 2026-01-09
**Documentation Version**: 1.0
**Blog Jekyll Version**: 4.3.0
