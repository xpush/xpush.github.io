---
layout: doc
title: Quick-start guide
date: April 25, 2014
---

XPUSH 를 가장 쉽고 빠르게 경험하기 위해서, Docker 기반의 이미지 파일을 사용할 수 있습니다.

XPUSH Docker 이미지 파일은, [docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 를 통해 이미지를 여러분의 서버 또는 PC 에 설치 할 수 있습니다.

<br />

#### 1. Docker 설치

[Docker Installation](https://docs.docker.com/installation/#installation) 를 참조하여 Docker 를 설치 합니다.

<br />

#### 2. XPUSH Docker 이미지 다운로드

[docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 에는 이미 설정이 완료된 XPUSH 환경이 구성되어 있습니다. 다음과 같이 XPUSH 이미지 파일을 다운로드 받습니다.

	> docker pull stalk/xpush

다운로드 받은 이미지에는, XPUSH 에서 사용하는 MongoDB, Redis 그리고 Zookeeper 가 설치되어 있고, XPUSH Session 서버와 Channel 서버가 실행될 수 있도록 정의 되어 있습니다.

<br />

#### 3. XPUSH 서버 실행.

다운로드 받은 이미지를 Docker 환경에서 실행합니다.

	> docker run -d --name xpush -p 8000:8000 -p 9000:9000 xpush/standalone

8000 포트는 Session 서버가 사용하는 포트번호이고, 9000 포트는 Channel 서버가 사용하곡 포트번호 입니다.

<br />

#### 4. Sample 프로그램 작성

***TODO 누가 작성좀 해주세요. (Javascript 또는 JAVA로, 아니면 둘다 ?)***

/doc/quick-start/index.md 파일 작성
