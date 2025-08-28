---
layout: post
title: "도커 네트워크의 구조"
excerpt: "Docker Network"
date: 2024-09-01
categories: [Development]
tags: [devops-infra, docker, network, networking]
---

## Docker network

### 도커 0

- 도커 데몬이 실행될 때 도커 0라는 브릿지 네트워크가 기본으로 지정된다.
- 브릿지 네트워크는 모든 도커 호스트에 존재한다.
- 도커 호스트의 default network (172.17.0.0/16) 중에서도 172.17.0.1이 도커 0에 할당된다.
- 이 안에서 컨테이너가 실행되면 컨테이너가 실행되는 순서에 따라 ip address를 할당한다.
- 컨테이너의 게이트웨이는 도커 0가 되고, nat를 지원한다.
- 따라서 웹서버 컨테이너를 실행하면 게이트웨이인 도커0(172.17.0.1)을 통해 외부와 통신하게 된다.
- 외부유저는 포트를 가지고 접속하고, 포트 포워딩을 통해 내부 컨테이너에 접속할 수 있다.

### 컨테이너 네트워크 구성요소

- ip address : 할당된 ip
- gateway address : docker 0
- hostname : 컨테이너 id 중 앞 12문자
- 외부 접속이 가능하도록 하려면 포트 포워딩이 필요하다

컨테이너 포트와 호스트 포트 매핑

```
docker run -d --name web -p 80:80 nginx
```

web 컨테이너로 80번 포트에서 외부로 접속이 가능해진다.

```
docker inspect web
curl 172.17.0.3 #내부에서 확인
```

외부에서 80번 포트로 접속 확인

랜덤 포트 사용하기

```
docker run -d --name web -p 80 nginx
```

여러개의 컨테이너를 띄우는 경우에 랜덤하게 사용하지 않는 포트를 사용하도록 하여 유연하게 대응할 수 있다.

### docker network driver

- bridge : docker0. default driver
- host : 컨테이너가 호스트의 ip주소를 가지도록 하는 것.
- null
  - 컨테이너에 네트워크를 설정하지 않음. 통신을 하지 못하도록 설정한다.
  - 같은 네트워크를 가지는 컨테이너간에는 자유롭게 통신이 가능한데 이런 경우에 보안상 문제가 될 수 있으므로 차단하는 것.

```
docker run --net=bridge nginx
docker run --net=host nginx
docker run --net=none nginx
```
