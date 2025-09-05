---
title: "리눅스 기초"
excerpt: "linux class note : 컴퓨터 공학 심화"
categories: [Tools]
subcategory: development-environment
tags:
  - [Linux]

permalink: /linux/linux-basic/

toc: true
toc_sticky: true

date: 2024-04-30
last_modified_at: 2024-04-30
---

#### 1. 리눅스 기본 개념 및 시스템 관리

- **리눅스를 배워야 하는 이유**:  
  리눅스는 유닉스 기반의 운영체제로, 개인용 및 서버용으로 많이 사용되며 오픈소스라는 장점이 있다. 클라우드 환경에서도 리눅스가 많이 사용되므로 리눅스를 학습하는 것은 중요함.

  - **오픈소스**: 무료로 제공되지만, GNU 라이선스에 따라 오픈 정도가 다름.
  - **WSL2**: 우분투와 같은 리눅스 배포판을 윈도우 환경에서 개발하기 위한 도구.

- **리눅스 시스템 종료 및 재부팅 명령어**:

  ```bash
  sudo reboot
  shutdown -h now
  poweroff
  ```

- **Nginx 설치 및 관리**:  
  웹 서버로 많이 사용하는 Nginx를 설치하고 관리하는 방법.

  ```bash
  sudo apt -y install nginx
  sudo systemctl start nginx.service
  sudo systemctl status nginx.service
  ```

- **파일 시스템 레이아웃**:  
  모든 파일과 경로는 inode 주소를 가지며, 디렉터리는 파일과 주소의 inode를 포함한 정보 구조체임.
  - **파일 및 디렉터리 생성**:
    ```bash
    touch test1.txt  # 빈 파일 생성
    mkdir -p d1/d2   # 하위 디렉터리 생성
    ```

---

#### 2. 사용자 및 계정 관리

- **사용자 계정 생성 및 관리**:

  - 계정 생성시 옵션을 사용해 그룹과 사용자 ID를 설정.

    ```bash
    groupadd -g 1010 testgroup
    useradd -s /bin/bash -d /home/user1 -u 1010 -g 1010 user1
    ```

  - **비밀번호 설정**:

    ```bash
    sudo passwd user1
    ```

  - **계정 삭제**:

    ```bash
    sudo userdel user1
    sudo userdel -r user1  # 홈 디렉터리까지 삭제
    ```

  - **계정 잠금 및 해제**:

    ```bash
    sudo passwd -l user1  # 계정 잠금
    sudo passwd -u user1  # 계정 해제
    ```

  - **사용자 그룹 관리**:
    - 리눅스에서 사용자 계정은 그룹으로 묶여 관리되며, 그룹마다 권한을 설정할 수 있음.
    - `/etc/group` 파일에서 그룹 정보를 확인할 수 있음.

- **sudoers 설정**:  
  특정 사용자에게 관리자 권한을 부여할 때 `/etc/sudoers` 파일을 수정함.

  ```bash
  sudo vi /etc/sudoers
  user1 ALL=(ALL) ALL
  ```

- **SSH 설정**:  
  원격 접속을 관리하는 방법으로 `/etc/ssh/sshd_config` 파일에서 루트 접근을 제한.
  ```bash
  PermitRootLogin prohibit-password
  ```

---

#### 3. 파일 및 디렉터리 관리

- **파일 권한 관리**:  
  리눅스에서 파일과 디렉터리의 접근 권한을 관리할 때 `chmod` 명령어 사용.

  ```bash
  chmod 700 file
  chmod -R 777 dir
  ```

- **소유권 변경**:  
  특정 파일이나 디렉터리의 소유자를 변경할 때 `chown` 명령어를 사용함.

  ```bash
  chown ubuntu:ubuntu file_or_dir
  chown -R ubuntu:ubuntu dir  # 하위 디렉터리 포함
  ```

- **링크 파일 관리**:  
  하드링크와 소프트링크를 생성해 파일을 관리할 수 있음.
  ```bash
  ln -s file soft_link  # 소프트링크 생성
  ln file hard_link     # 하드링크 생성
  ```

---

#### 4. 프로세스 관리 및 모니터링

- **프로세스 상태 확인 및 종료**:  
  현재 실행 중인 프로세스를 확인하고, 필요에 따라 프로세스를 종료하는 방법.

  ```bash
  ps -ef      # 프로세스 상태 확인
  kill -9 PID # 프로세스 강제 종료
  ```

- **모니터링 도구**:  
  시스템 자원의 사용량(CPU, 메모리, 디스크 I/O)을 모니터링하는 다양한 도구가 있음.

  - `sar`, `iostat`, `vmstat` 등의 명령어를 사용해 자원 사용량을 모니터링.
  - **글랜스(Glances)**: CPU, 메모리, 디스크, 네트워크 사용량을 종합적으로 확인할 수 있는 도구.
    ```bash
    sudo apt -y install glances
    sudo glances
    ```

- **메모리 사용량 확인**:
  ```bash
  free -mt
  ```

---

#### 5. 업무 자동화 (스케줄링)

- **업무 자동화 도구**:

  - **at**: 한 번만 실행하는 작업 예약.
  - **cron**: 주기적으로 실행하는 작업 예약.

- **크론(Cron) 문법**:  
  특정 시간에 작업을 주기적으로 실행하도록 예약.

  ```bash
  * * * * * command_to_execute
  ```

- **크론 작업 예시**:  
  매일 오후 11:30에 로그 파일을 저장하는 작업.

  ```bash
  30 23 1 * * /usr/bin/ls -l /var/log > /data/log-$(date '+%y-%m-%d').txt
  ```

- **쉘 스크립트를 통한 자동화 예시**:  
  서버의 로그 백업 작업을 자동화.
  ```bash
  #!/bin/bash
  set $(date)
  fname = "%6-%2-%3-backup"
  tar -cvzf /home/ubuntu/LABS/$fname.tar.gz /var/log
  ```

---

#### 6. 패키지 설치 및 관리

- **APT 패키지 관리**:  
  우분투에서 패키지를 설치, 제거할 때 `apt` 명령어를 사용함.

  ```bash
  sudo apt -y install package_name
  sudo apt -y autoremove package_name  # 패키지 제거
  ```

- **패키지 저장소 설정**:  
  `/etc/apt/sources.list` 파일에 패키지 저장소 URL을 추가해 패키지 설치 가능.

- **MariaDB 설치**:  
  패키지 저장소를 추가하고, `apt`를 사용해 MariaDB 설치 및 설정.
  ```bash
  sudo apt -y install mariadb-server
  sudo systemctl start mariadb
  sudo systemctl status mariadb
  ```

---

#### 7. SQL 기초

- **SQL 쿼리 처리 순서**:  
  `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`, `ORDER BY` 순으로 쿼리가 처리됨.

  - **HAVING vs WHERE**: `HAVING`은 그룹화된 데이터에 적용되고, `WHERE`는 그룹화 전에 적용됨.

- **SQL 예시 쿼리**:
  ```sql
  SELECT SUM(salary)
  FROM employees
  WHERE job_id NOT LIKE "%REP%"
  GROUP BY job_id
  HAVING SUM(salary) >= 13000
  ORDER BY salary;
  ```

---

#### 8. 로그 분석 및 파이프 활용

- **로그 조회 및 분석**:

  ```bash
  tail -f /var/log/nginx/access.log  # 실시간 로그 확인
  awk '{print $1}' /var/log/nginx/access.log  # 특정 컬럼만 출력
  ```

- **파이프와 AWK 사용**:  
  파이프(`|`)와 `awk` 명령어를 사용해 로그 파일에서 특정 정보를 추출.
  ```bash
  awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -r  # 접속 IP 카운트
  ```
