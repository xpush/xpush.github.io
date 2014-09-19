---
title: "What is XPUSH ?"
description: "Opensource Project - Realtime Web Communication Platform"
layout: about
---

<div class="row centered">
  <div class="embed-responsive embed-responsive-4by3">
    <iframe class="embed-responsive-item" src="//www.youtube.com/embed/3MJfQXo_R9s?rel=0" allowfullscreen></iframe>
  </div>
</div>

- - -

<img src="/about/resource/rwcp.png" width="45%" align="right">

웹에서 실시간 데이터 통신을 구현하기 위해서는 HTML5 표준 Websocket 으로 구현하거나, node.js 의 socket.io 를 활용하여 구현하는 등 다양한 방법이 있습니다.

하지만, 대용량 트래픽을 고려한 데이터 송수신과 증가하는 사용자를 고려한 분산 시스템 및 무중단 서버 확장을 고려하여 개발하기에는 많은 시간이 걸릴 수 있습니다.

**XPUSH** 는 여러분이 직접 설치해서 사용할 수 있는 실시간 메시지 통신 기능과 메시지 저장 및 사용자와 장치를 관리하는 기능 그리고 Mobile Push 기능을 제공하는 서버 플랫폼 입니다.
또한, 하나의 **XPUSH** 플랫폼으로 다양한 실시간 서비스 및 어플리케이션을 구현할 수 있는 통합 실시간 서비스 플랫폼입니다.

이제, 직접 실시간 통신 서버를 구현하지 않고, **XPUSH** 를 설치 및 활용하여 여러분의 서비스에 실시간 데이터 통신 기능을 추가해 보세요.

- - -

### 1. Real-time Web Communication Platform

" *We help you easily build real-time applications. XPUSH was designed for developers.* "

**XPUSH** 는 웹 구현기술로 구현된 실시간 메시지 통신 서버들로 구성되어 있습니다. **XPUSH** 활용하여 메신져나 체팅서비스 등 다양한 실시간 메시지 송수신 기능을 구현할 수 있습니다.

현재는 다양한 웹서비스에서 사용 가능한 Javascript 라이브러라와, JAVA 어플리케이션이나 Android 에서 사용할 수 있는 JAVA 라이브러리가 제공되고 있습니다. **XPUSH** 개발팀은 계속해서 라이브러리를 추가 개발하고 있습니다.

실시간 데이터 통신은 Client 와 Server 간 연결을 유지하면서 양방향으로 데이터를 송수신할 수 있도록 되어 있으며, 연결이 끊어지거나 서버가 장애나더라도 서비스에 지장이 없도록 설계 되어 있습니다.

- - -

### 2. Works Everywhere

" *At the core of XPUSH is the HTML5 WebSocket protocol, but we also have fallback mechanisms so that XPUSH just works anywhere, anytime.* "

**XPUSH** 는 저비용 고성능의 비동기 이벤트 루핑 서버를 구현하기 위하여 node.js 로 개발되었습니다. 그리고, 웹기반의 실시간 메시지 통신을 위하여 Node.js 모듈인 socket.io 를 사용하고 있으며, 사용하는 모듈은 계속해서 최신 버젼으로 업데이트 하고 있습니다.

socket.io 를 통해 IE, Chrome, Opera 등 다양한 웹브라우져에서 동작할 수 있을 뿐 아니라, Android/IOS 또는 다양한 Standalone 어플리케이션에서도 사용이 가능합니다.

- - -

<img src="/about/resource/software.png" width="40%" align="left">

### 3. Scalable Web Architecture

" *XPUSH was designed to work with commodity servers, an elastic virtualised environments saving you money and headaches. A scalable web application can handle growth – of users or work – without requiring changes to the source code and stoping existed servers* "

일반적으로 실시간 메시지 통신은 대량의 네트워크 트래픽을 처리 할 수 있어야 하며, 사용자가 늘어나면서 급격하게 트래픽이 증가할 수 있습니다. 그러므로, 부하 분산을 위한 다수의 서버가 구동될 수 있도록 해야 하며, 무중단 확장 가능하도록 설계해야만 했습니다. 그래서 **XPUSH** 개발팀은 초기부터 Scalable Architecture 를 설계하고 검증하면서, 아키텍처 설계를 주기적으로 최적화 하고 있습니다.

**XPUSH** 는 분산 서버들의 실시간 코디네이션을 위하여 zookeeper 를 사용하고 있으며, 서버에 연결되어 있는 정보를 실시간 저장하고 기본데이터를 캐쉬하기 위한 Redis 를 사용합니다. 그리고, 다양한 형태의 비정형 통신 메시지를 저장하기 위하여 MongoDB 를 사용하고 있습니다.

**XPUSH** 를 구성하는 서버들은 내부적으로 Session 서버와 Channel 서버로 분리되어 동작합니다.
Session 서버는 사용자 접속을 위한 인증과 사용자 관리 및 부하 분산을 위한 서버 분배 할당을 처리합니다. 그리고 Channel 서버는 실제로 데이터 송수신을 하기 위해 Client 가 연결을 유지하는 서버 입니다. 상대적으로 Channel 서버의 부하가 증가하기 쉬우므로, Channel 서버만 증설할 수 있도록 서버 실행 파라미터를 구분하고 있습니다.

#### - standalon 동작 

테스트의 목적 또는 개발 단계에 설치할 수 있는 All In One 구성입니다. Docker 이미지중 standalone 으로 실행하면, MongoDB / Redis / Zookeeper / XPUSH Session 서버 / XPUSH Channel 서버 가 하나의 장치에 한번에 설치 될 것입니다. 자세한 설명은 [Installation](/doc/installation/#docker) 문서에서 확인 하실 수 있습니다.

#### - 확장성을 고려한 분산 환경 (실제 운영 환경)

실제로 운영환경에 적용하기 위해서는 별도로 구성된 MongoDB Cluster, Redis Cluster, Zookeeper Cluster 를 구성하고, XPUSH Session 서버와 Channel 서버를 각각 독립적인 이미지 또는 서버에 설치해야 할 것입니다. 설치에 대한 자세한 설명은 [Installation](/doc/installation) 문서에서 확인하실 수 있습니다.
