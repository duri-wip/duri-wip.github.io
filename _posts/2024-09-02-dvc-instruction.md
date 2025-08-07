---
title: "DVC 사용법 가이드"
excerpt: "데이터 버전 관리를 위한 DVC 도구 사용법"
category: mlops
tags:
  - [DVC, MLOps, 버전관리, Git]
toc: true
toc_sticky: true
date: 2024-09-02
last_modified_at: 2024-09-02
---
## dvc 사용법

### 1. 설치

```
pip install dvc
dvc init
```

서버 1 환경과 동일하게 3.10.14

### 2. 파일이 생겼다고 가정(로컬에서)

일단 데이터 수집이 될 디렉토리로 이동해서
dvc init

```
ERROR: failed to initiate DVC - /home/swcpractice/data is not tracked by any supported SCM tool (e.g. Git). Use --no-scm if you don't want to use any SCM or --subdir if initializing inside a subdirectory of a parent SCM repository
```

ChatGPT의 말:
ChatGPT
이 오류는 DVC를 초기화하려는 디렉토리가 Git과 같은 SCM(소스 제어 관리) 툴에 의해 추적되지 않기 때문에 발생합니다. DVC는 기본적으로 Git과 같은 SCM 툴을 사용해 데이터를 버전 관리하도록 설계되어 있지만, Git으로 관리되지 않는 디렉토리에서 DVC를 사용하려고 할 경우 발생하는 오류입니다.

해결 방법은 두 가지입니다:

1. Git 저장소로 초기화
   DVC는 기본적으로 Git과 함께 사용하도록 설계되었기 때문에, DVC를 사용하기 전에 해당 디렉토리를 Git으로 초기화해야 합니다.

a. 디렉토리 Git으로 초기화
bash
코드 복사
cd /home/swcpractice/data
git init
b. DVC 초기화
Git 저장소로 초기화된 디렉토리에서 다시 DVC를 초기화합니다.

bash
코드 복사
dvc init
이제 Git으로 데이터 버전 관리를 할 수 있게 되며, dvc add 및 dvc push와 같은 명령어를 사용할 수 있습니다.

2. SCM 없이 DVC 사용하기 (--no-scm 옵션)
   Git을 사용하지 않고 DVC를 사용하고자 한다면, --no-scm 옵션을 사용하여 Git 없이 DVC를 초기화할 수 있습니다.

bash
코드 복사
dvc init --no-scm
이 명령을 사용하면 Git 없이도 DVC로 데이터를 버전 관리할 수 있지만, Git을 통한 변경 사항 추적 기능은 사용할 수 없습니다. 이 경우, DVC는 로컬로만 작동하며, Git에서 제공하는 브랜칭이나 커밋 등의 기능은 사용할 수 없습니다.

3. 상위 Git 저장소를 사용 중인 경우 (--subdir 옵션)
   만약 /home/swcpractice/data가 상위 Git 저장소의 하위 디렉토리인 경우, DVC를 서브 디렉토리에서 초기화할 수 있습니다. 이 경우 --subdir 옵션을 사용합니다.

bash
코드 복사
dvc init --subdir
이 옵션을 사용하면 상위 디렉토리의 Git 저장소를 기준으로 DVC가 해당 디렉토리에서 작동하게 됩니다.

원하는 방식에 따라 위의 방법 중 하나를 선택해 오류를 해결하시면 됩니다!

일단 1번을 사용해서 관리하기로 함

```
git init
dvc init
```

### 3. connection 관리

Airflow의 Connection 기능을 사용해 GitHub 자격 증명을 관리하면, 자격 증명을 안전하게 저장하고 DAG 내에서 이를 쉽게 사용할 수 있습니다. 이 방법은 GitHub의 OAuth 토큰이나 SSH 키를 활용하는 방식으로 자동화하는 데 유용합니다.

1. Airflow Connection 생성
   먼저 Airflow UI에서 GitHub에 대한 자격 증명을 저장할 Connection을 생성해야 합니다.

a. Airflow UI 접속
Airflow UI에 로그인한 후, 상단 메뉴에서 Admin > Connections로 이동합니다.

b. 새 Connection 추가
Connections 화면에서 우측 상단의 + 버튼을 클릭해 새로운 Connection을 추가합니다.

Conn Id: github_conn (원하는 ID를 입력)
Conn Type: Generic (GitHub에 맞는 유형 선택)
Host: github.com
Login: GitHub 사용자명
Password: GitHub Personal Access Token (PAT) 또는 OAuth 토큰을 입력
SSH 키를 사용하는 경우, Extra 필드에 SSH 키 경로를 JSON 형식으로 입력할 수 있습니다. 예:

json
코드 복사
{
"ssh_key_file": "/home/ubuntu/.ssh/id_rsa"
}
Save 버튼을 눌러 Connection을 저장합니다.

### 4. DAG에서 Connection 사용

Connection을 생성한 후, DAG 내에서 이를 사용하여 GitHub에 자격 증명을 쉽게 적용할 수 있습니다. 예를 들어, PythonOperator에서 GitHub에 push할 때 Airflow의 Connection을 참조하여 GitHub 토큰이나 SSH 키를 불러옵니다.

a. Connection에서 Token이나 SSH 불러오기
python

```
from airflow.hooks.base import BaseHook
import os

def dvc_push():
    # Airflow Connection에서 자격 증명 가져오기
    conn = BaseHook.get_connection('github_conn')

    # GitHub Personal Access Token 사용
    github_token = conn.password  # GitHub PAT

    # Git 설정 및 push
    os.system('git config --global user.email "your_email@example.com"')
    os.system('git config --global user.name "your_name"')
    os.system('git add collected_data.dvc')
    os.system('git commit -m "Add new data"')
    os.system(f'git push https://{github_token}@github.com/your-username/your-repo.git')
    os.system('dvc push')
```

3. s3에 연결하는 과정(가정)
   dvc init
   dvc remote add -d s3remote s3://your-bucket/path
   git add .dvc/config
   git commit -m "Initialize DVC"
