# Agent Guidelines for Jekyll Blog Modifications

## Purpose
This document provides instructions specifically for AI agents working with this Jekyll blog. Use these guidelines to understand how to interpret the documentation and make safe, effective modifications.

## Documentation Structure

### Primary Files
1. **[blog-architecture.yml](blog-architecture.yml)** - Comprehensive reference
   - Complete directory structure
   - Configuration details
   - Front matter templates
   - Feature descriptions
   - Design system documentation
   - ~1200 lines of detailed information

2. **[quick-reference.yml](quick-reference.yml)** - Fast lookups
   - Common file paths
   - Front matter templates
   - Category/tag references
   - ~200 lines of essential information

3. **This file (agent-guidelines.md)** - How to work with the blog

## How to Use the Documentation

### For Understanding Tasks
1. Read user request carefully
2. Check [quick-reference.yml](quick-reference.yml) for common patterns
3. Refer to [blog-architecture.yml](blog-architecture.yml) for detailed implementation
4. Always verify file paths and structure before making changes

### For Making Modifications
1. **Identify affected systems** - Check if changes impact:
   - Content only (safest)
   - Data files (requires validation)
   - Layouts/includes (affects multiple pages)
   - CSS (check variable usage)
   - JavaScript (test filtering logic)

2. **Read relevant files first** - Never modify files you haven't read

3. **Validate before writing** - Ensure:
   - YAML syntax is correct
   - Front matter matches required fields
   - File paths exist
   - Category/subcategory matches categories.yml
   - Study tags match studies.yml

## Common Modification Scenarios

### Scenario 1: Add New Blog Post

**Task**: User asks to add a blog post about Docker

**Steps**:
```markdown
1. Determine category from categories.yml:
   - Category: Development
   - Subcategory: devops-infra (for Docker content)

2. Create file path:
   _posts/Development/devops-infra/2026-01-10-docker-basics.md

3. Add front matter (reference quick-reference.yml → templates.blog_post):
   ---
   layout: post
   title: "Docker 기초"
   excerpt: "Docker 컨테이너 기본 개념"
   date: 2026-01-10
   categories: [Development]
   subcategory: devops-infra
   tags: [docker, containers, devops]
   ---

4. Write content in Markdown

5. If images needed, place in: assets/images/post_img/
```

**Validation**:
- ✓ Date in filename matches front matter date
- ✓ Categories: [Development] matches category slug "development"
- ✓ subcategory: devops-infra exists in Development category
- ✓ File created in correct folder structure

### Scenario 2: Add Study Post for Existing Study

**Task**: User asks to add Java study post about loops

**Steps**:
```markdown
1. Check _data/studies.yml for Java study:
   - Study: "자바 기초 학습"
   - study_tag: "study-java"
   - status: "진행중"

2. Create file:
   _studies/Study/java/2026-01-10-loops.md

3. Add front matter with study_tag:
   ---
   layout: post
   title: "자바 반복문"
   excerpt: "for, while 반복문 이해하기"
   date: 2026-01-10
   categories: [Study]
   tags: [study-java, java, loops, control-flow]
   ---

4. Write content

5. Progress updates automatically (counts posts with "study-java" tag)
```

**Critical**: MUST include `study-java` in tags for progress tracking

### Scenario 3: Create New Study Program

**Task**: User wants to start learning Python

**Steps**:
```markdown
1. Edit _data/studies.yml - Add entry:
   - title: "Python 기초부터 고급까지"
     description: "Python 프로그래밍 언어 완전 학습"
     status: "진행중"
     study_tag: "study-python"
     total: 20

2. Create directory:
   _studies/Study/python/

3. Guide user to create posts with tag "study-python"

4. Progress will display on:
   - Homepage (study-section.html)
   - Study page (study.html)
```

**Validation**:
- ✓ study_tag is unique (not used by other studies)
- ✓ Format: study-[topic] (lowercase, hyphen)
- ✓ total is realistic number

### Scenario 4: Add New Category

**Task**: User wants to add "Security" category

**Steps**:
```markdown
1. Edit _data/categories.yml:
   - name: "Security"
     slug: "security"
     description: "보안 기술"
     subcategories:
       - name: "Web Security"
         slug: "web-security"
         description: "웹 보안"

2. Create folder structure:
   _posts/Security/web-security/

3. Test category filtering:
   - Check categories.html displays new category
   - Verify JavaScript filtering works
   - Ensure post counts are correct
```

**Warning**: This affects filtering logic - test thoroughly

### Scenario 5: Modify Site Colors

**Task**: User wants to change accent color from green to blue

**Steps**:
```markdown
1. Read assets/css/main.css

2. Locate :root variables:
   --accent-primary: #3fd166;

3. Change to new color:
   --accent-primary: #4285f4;

4. All components using this variable update automatically:
   - Progress bars
   - Links
   - Badges
   - Buttons
```

**Best Practice**: Always use CSS variables, never hardcode colors

## Safety Guidelines

### ✅ Safe to Modify
- Content files (_posts/, _projects/, _studies/)
- Images in assets/images/
- Individual CSS files (except main.css variables)
- Data files with proper validation
- Research notes

### ⚠️ Requires Caution
- _config.yml (Jekyll rebuild required)
- _layouts/ (affects all pages)
- _includes/ (used by multiple pages)
- main.css variables (global impact)
- Embedded JavaScript (filtering logic)

### ❌ Do Not Modify
- Gemfile (dependency management)
- .github/workflows/deploy.yml (CI/CD)
- Gemfile.lock (auto-generated)

## Validation Checklist

Before completing any modification:

### For Content Files
- [ ] File naming: YYYY-MM-DD-title.md
- [ ] Date in filename matches front matter
- [ ] Required front matter fields present
- [ ] Category matches categories.yml
- [ ] Subcategory exists under parent category
- [ ] Study tag matches studies.yml (if applicable)
- [ ] Image paths are correct

### For Data Files
- [ ] YAML syntax is valid
- [ ] Slugs are lowercase-hyphen format
- [ ] No duplicate slugs or study_tags
- [ ] All referenced relationships exist

### For Layouts/Includes
- [ ] Liquid syntax is correct
- [ ] Variable names match site/page structure
- [ ] No broken includes
- [ ] Responsive design maintained

### For CSS
- [ ] Use CSS variables, not hardcoded values
- [ ] Maintain window frame aesthetic
- [ ] Test at 768px breakpoint
- [ ] No syntax errors

## Testing Workflow

### Local Testing
```bash
# Install dependencies (first time)
bundle install

# Start local server
bundle exec jekyll serve

# Visit in browser
http://localhost:4000

# Test:
# - Navigate all pages
# - Test category filtering
# - Check study progress bars
# - Verify responsive layout
# - Check browser console for errors
```

### Pre-Deployment Checks
1. All new files committed
2. No YAML syntax errors
3. All image references valid
4. Category filtering works
5. Study progress calculates correctly
6. No JavaScript console errors
7. Mobile view works

### Deployment
- Push to main/master branch
- GitHub Actions builds and deploys automatically
- Check https://duri-wip.github.io after ~2-3 minutes
- Verify changes live

## Common Pitfalls

### ❌ Don't Do This
```yaml
# Wrong: Category doesn't exist
categories: [NonExistentCategory]

# Wrong: Missing study tag for study post
tags: [java, programming]  # Missing study-java

# Wrong: Hardcoded color
color: #3fd166;  # Should use var(--accent-primary)

# Wrong: Incorrect date format
date: 01-10-2026  # Should be 2026-01-10
```

### ✅ Do This
```yaml
# Correct: Valid category
categories: [ML-AI]

# Correct: Study tag included
tags: [study-java, java, programming]

# Correct: CSS variable
color: var(--accent-primary);

# Correct: ISO date format
date: 2026-01-10
```

## Understanding Study Progress System

**Critical Concept**: Progress is tag-based, automatic

```
Study Definition (_data/studies.yml):
  study_tag: "study-java"
  total: 10

Posts with tag "study-java": 7

Calculation (study-section.html):
  count = posts matching "study-java" = 7
  progress = (7 / 10) * 100 = 70%

Display:
  [██████████░░░░░░░░░░] 70%
```

**Key Points**:
1. No manual update needed - counts automatically
2. Tag MUST match exactly (case-sensitive)
3. Works across all collections (posts + studies)
4. Updates on every Jekyll build

## Liquid Templating Quick Reference

Common patterns in layouts/includes:

```liquid
{{ page.title }}              # Output page title
{{ site.author.name }}        # Site config value
{% if page.tags %}            # Conditional
{% for post in site.posts %}  # Loop through posts
{{ post.date | date: "%Y년 %m월 %d일" }}  # Date formatting
{% include sidebar.html %}    # Include component
```

## Korean Language Considerations

- **Front matter**: English field names, Korean values
- **Content**: Korean
- **UI text**: Korean in layouts/includes
- **Date format**: `"%Y년 %m월 %d일"` (년월일)
- **Status values**: "진행중", "완료" (Korean)
- **Labels**: "이전 글", "다음 글", "홈으로 돌아가기" (Korean)

## When in Doubt

1. **Read the file first** - Never guess file contents
2. **Check examples** - Look at existing posts/projects/studies
3. **Validate data files** - Use YAML validator
4. **Test locally** - Always test before pushing
5. **Ask for clarification** - If user request is ambiguous

## Quick Command Reference

```bash
# Local development
bundle exec jekyll serve

# Build only
bundle exec jekyll build

# Install/update dependencies
bundle install

# Check file paths
ls _posts/Development/backend/
ls _data/
ls _layouts/

# Validate YAML (if yamllint installed)
yamllint _data/categories.yml
```

## Resources

- **Primary docs**: [blog-architecture.yml](blog-architecture.yml)
- **Quick lookup**: [quick-reference.yml](quick-reference.yml)
- **Jekyll docs**: https://jekyllrb.com/docs/
- **Liquid docs**: https://shopify.github.io/liquid/

## Summary

As an AI agent working with this blog:

1. **Always read documentation first** - Especially blog-architecture.yml
2. **Validate before modifying** - Check categories, tags, paths
3. **Use templates** - Quick-reference.yml has ready-to-use templates
4. **Test locally** - Run `bundle exec jekyll serve`
5. **Understand the system** - Study progress is tag-based and automatic
6. **Be cautious with data files** - They affect multiple systems
7. **Respect the design** - Maintain retro aesthetic and CSS variables
8. **Check your work** - Use validation checklist before completing

The blog is well-structured with clear patterns. Follow existing conventions, validate your changes, and test thoroughly. When properly understood, it's straightforward to work with.
