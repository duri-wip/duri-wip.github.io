---
layout: post
title: "YAML 파일 사용법과 기본 개념"
excerpt: "구성 파일 작성을 위한 YAML 기초"
date: 2024-09-01
categories: [tools, development-environment]
tags: [yaml, configuration, docker, tools]
---

- 0.  YAML? : yaml 파일 사용법

## YAML

Yaml aint markup language
구성파일을 작성하는 방법
쉽게 읽고 이해할 수 있도록 설계되어 있고 다른 프로그래밍 언어와 함께 사용할 수 있다.
xml, json 파일과 비교되는데 가독성이 더 높고 사용자 친화적이다.

![예시]

### 기본문법

- 계층을 나타낼 때 띄어쓰기 2칸을 사용한다.
- 키 : 값의 구조를 가진다. -> 키값을 찾으면 값을 반환받는다.
- 콜론의 왼쪽에는 공백이 없고 오른쪽에는 한칸 이상의 공백이 있어야 한다.
- -는 공백 하나를 대체하고, 리스트의 시작으로 해석한다.

### pyyaml 라이브러리의 활용

파이썬에서 yaml 파일을 다룰수 있도록 해주는 프레임워크

```
!pip install pyyaml

import pyyaml
```

- json을 yml 파일로 변환하기
-
