---
layout: doc
title: XPUSH, Installation & Run
date: April 25, 2014
javascript: /doc/installation/resource/installation.js
---

There are two methods to install XPUSH,
one methods is that install with NPM( Node Package Manger),
another way is that download and run the image file of the Docker,
you can install it by selecting one of the two.

<ul id="tabs" class="nav nav-tabs" data-tabs="tabs">
    <li class="active"><a href="#npm" data-toggle="tab"><b>NPM</b></a></li>
    <li><a href="#docker" data-toggle="tab"><b>Docker</b></a></li>
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
