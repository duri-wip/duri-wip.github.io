### 컴퓨터공학 심화 통합 정리 (카테고리별 정리)

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

---
\현재 정리된 내용을 보면, 매우 체계적으로 구성되어 있습니다. 다만 몇 가지 추가하면 좋을 만한 부분을 제안하고 추가할 수 있습니다. 추가된 내용에는 "추가됨"이라는 표시를 해두겠습니다.

### 1. 리눅스와 클라우드의 관계

- **추가됨:** 리눅스가 클라우드 환경에서 중요한 이유는 리눅스의 오픈소스 특성과 경량화된 구조 덕분에 클라우드 서비스 제공자들이 리눅스를 주로 채택하고 있기 때문입니다. 주요 클라우드 서비스(AWS, GCP, Azure 등)에서 제공하는 서버 인스턴스 중 대부분이 리눅스를 기반으로 하며, 특히 DevOps, 컨테이너 기반의 인프라에서 필수적입니다.

### 2. 파일 시스템과 디렉토리 구조

- **추가됨:** 리눅스 파일 시스템의 구조에서 가장 중요한 부분 중 하나는 "inode"입니다. 모든 파일과 디렉토리는 고유한 inode 번호를 가지고 있으며, 이 inode는 파일의 실제 데이터 위치를 추적하는 역할을 합니다. 따라서 파일 시스템에서 inode를 효율적으로 관리하는 것이 파일 탐색 성능에 큰 영향을 미칩니다.
  
### 3. 쉘 스크립트와 크론탭

- **추가됨:** 크론탭에서 스케줄링 작업을 자동화할 때, 특정 작업이 실패했을 경우 이를 다시 시도하거나, 실패 로그를 기록하는 방법을 설정할 수 있습니다. 이러한 기능은 `MAILTO` 변수를 사용하여 설정할 수 있으며, 작업 실패 시 이메일 알림을 받을 수 있습니다. 또한, `@reboot` 옵션을 사용하여 시스템이 재부팅될 때 특정 스크립트를 자동으로 실행하게 할 수 있습니다.

### 4. 사용자 보안 관리

- **추가됨:** 리눅스의 사용자 계정 보안에서 `faillog`를 사용하여 로그인 실패 기록을 관리할 수 있습니다. `faillog` 명령어는 특정 사용자에 대한 실패한 로그인 시도를 확인하고 이를 초기화하거나 제한하는 데 사용됩니다. 이러한 기능을 통해 brute-force 공격을 방지하는 등의 보안 강화를 할 수 있습니다.

### 5. 리눅스의 네트워크 관리

- **추가됨:** 리눅스에서 네트워크 관리는 `netstat`, `ifconfig`, `ip` 명령어를 통해 수행됩니다. 특히 `ip` 명령어는 최신 리눅스 시스템에서 네트워크 인터페이스를 관리하고 라우팅 테이블을 설정하는 데 자주 사용됩니다. 네트워크 트래픽 분석을 위해 `tcpdump`와 같은 패킷 캡처 도구도 유용하게 사용됩니다.

### 6. 패키지 관리

- **추가됨:** `apt`와 같은 패키지 관리 시스템 외에도 리눅스에서 중요한 패키지 관리는 `snap` 패키지 관리 시스템을 통해 이루어집니다. 스냅 패키지는 컨테이너처럼 독립적으로 실행되며, 다른 시스템 파일과 충돌하지 않게 설계된 소프트웨어 배포 시스템입니다. 이를 통해 최신 패키지를 손쉽게 설치하고 관리할 수 있습니다.

---

이렇게 몇 가지 추가 내용을 반영해 보완할 수 있었습니다. 각 부분에 따라 조금 더 심화된 설명을 추가하여 전체적인 이해를 도울 수 있도록 했습니다.