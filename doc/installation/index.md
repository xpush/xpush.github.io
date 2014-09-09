---
layout: doc
title: Installation
date: April 25, 2014
---

***/doc/installation/index.md 파일 수정해서 내용 좀더 채워야 합니다.***

XPUSH 설치 방법은, NPM(Node Package Manager) 로 XPUSH 모듈을 설치하는 방법과 Docker 이미지 파일을 다운로드 받아 실행하는 방법으로 2가지가 있습니다.

<ul id="tabs" class="nav nav-tabs" data-tabs="tabs">
    <li class="active"><a href="#npm" data-toggle="tab"><b>NPM</b> 으로 설치하기</a></li>
    <li><a href="#docker" data-toggle="tab"><b>Docker</b> 이미지로 실행하기</a></li>
</ul>
<div id="my-tab-content" class="tab-content">
    <div class="tab-pane active" id="npm">

      {% capture npm_include %}{% include doc/installation/with-npm.md %}{% endcapture %}
      {{ npm_include | markdownify }}

    </div>
    <div class="tab-pane" id="docker">

      {% capture docker_include %}{% include doc/installation/with-docker.md %}{% endcapture %}
      {{ docker_include | markdownify }}

    </div>
</div>
