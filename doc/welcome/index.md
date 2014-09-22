---
layout: doc
title: Documentation about XPUSH
date: September 8, 2014
---

XPUSH 설치부터 실행 및 API 사용방법에 대한 문서를 제공합니다.

이 문서를 통해서 여러분의 로컬 및 서버환경에 XPUSH 플랫폼을 설치하고, XPUSH를 활용한 다양한 어플리케이션 개발을 시작해 보세요.

- - -

### GETTING STARTED
XPUSH 를 설치하는 방법과 설정파일 작성 방법을 설명합니다.

#### > [Quick-start guide](/doc/quick-start) <span class="badge badge-theme">recommended</span>
여러분이 가장 빨리 XPUSH 를 경험하기 위해서, XPUSH 를 설치하고 예제를 통해 직접 간단한 어플레키에션을 만들어 볼 수 있습니다.

docker container 로 구성되어 있는 이미지 파일을 직접 설치하고 예제 어플리케이션을 실행해 보세요.

#### > [Installation](/doc/installation)
XPUSH 는 데이터 저장/관리, 실시간 세션 관리 그리고 분산 환경 구성을 위하여 MongoDB, Redis, Zookeeper를 사용하고 있습니다.
XPUSH 를 설치하기 전에, 먼저 이런 오픈소스 서버를 설치하는 방법 부터 설명합니다.

그리고, npm(node package manager)과 docker 이미지로 XPUSH를 설치하는 방법에 대해 자세히 설명합니다.

#### > [Configuration](/doc/configuration)
XPUSH 를 실행하기 위해 설정파일을 작성하는 방법과 설정 파라미터에 대해 알아봅니다.

- - -

### API DOCUMENTS
XPUSH 는 **session 서버** 와 **channel 서버** 로 구성되어 있습니다.

각 서버에서 제공하는 API 를 활용하여 여러분들의 어플리케이션에 실시간 메시지 통신 기능을 구현해 넣을 수 있습니다.

#### > [Session Server](/doc/api/session)
XPUSH 를 구성하고 있는 서버중 Session 서버는 사용자 인증과 메시지 전송서버(channel 서버) 를 할당하는 역할을 수행합니다.

이 기능을 사용하기 위한 API 목록을 조회하고 테스트 할 수 있습니다.

#### > [Channel Server](/doc/api/channel)
Channel 서버는 실제로 메시지를 실시간으로 송수신하게 해주는 역할을 담당합니다.

메시지 송수신 뿐 아니라 메시지 Push Notification 이나 전달되지 않은 메시지 관리 등 다양한 기능을 사용하기 위한 API 목록을 조회하고 테스트 할 수 있습니다.

- - -

### FOR CLIENTS
XPUSH 서버의 API 를 사용하기 위한 가장 쉬운 방법은 XPUSH 에서 제공하는 라이브러리를 사용 하는 것입니다.

현재는 Javascript 와 JAVA 라이브러리만 제공하고 있습니다. XPUSH 개발팀은 더욱 다양한 환경에서 XPUSH 가 쉽게 사용할 수 있도록 계속해서 개발 하고 있습니다.

#### > [Javascript library](/doc/library/javascript)
Javascript 라이브러리는 여러분의 웹서비스에서 실시간 메시지 통신을 구현하는데 필요한 라이브러리 입니다.

#### > [JAVA library](/doc/library/java)
JAVA 라이브러리는 일반적인 JAVA 어플리케이션 뿐 아니라, Android 어플리케이션 개발을 지원하는 라이브러리 입니다.

- - -

### EXAMPLES
[Quick-start guide](/doc/quick-start) 이외의 좀더 상세한 예제를 모아 가이드를 작성하고 있습니다.


