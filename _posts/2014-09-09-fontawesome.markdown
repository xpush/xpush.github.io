---
layout: post
title:  "Font Awesome 에서 이미지 변환하기"
date:   2014-09-09 22:03:54
categories: Programming
tags: fontawesome
og:
  type: article
  description: font awesome to png 를 사용하여 쉽게 Font Awesome 에서 PNG 파일을 추출할 수 있습니다.
---

**XPUSH** 사이트에서 사용한 이미지 중 일부는 [Font Awesome] 에서 폰트를 이미지로 변환해 사용하였습니다.
이번 포스트에서는 XPUSH 개발팀이 사용했던 [font awesome to png] 도구에 대해 소개 합니다.

[font awesome to png] 는 Python 으로 개발된 소스이며, 사용방법이 아주 간단하고, 본인이 사용하고자 하는 목적에 따라 수정하기도 어렵지 않습니다.

OS X 환경을 기준으로,
가장먼저 Python이 설치되어 있는지 확인합니다. (OSX 는 기본적으로 Python 이 설치되어 있습니다.)

[font awesome to png] 의 [소스](https://github.com/odyniec/font-awesome-to-png/blob/master/font-awesome-to-png.py)를 보면, Image 라이브러리를 사용하는 것을 확인 할 수 있습니다.
그래서 [PIL] (Python Imaging Library) 가 필요합니다.
[PIL] 은 2009 년 이후로 업데이트가 안되고 있지만, 굉장히 잘만들어졌기 때문에 아직도 많은 사람들이 PIL을 사용하고 있긴 합니다.
하지만, Python 버젼이 계속 상승하면서 더이상 지원하지 않는 경우가 발생하고 있기 때문에 PIL 을 사용하지 않고, 동일한 기능을 제공하는 [Pillow] 를 사용하도록 합니다.

[Pillow] 는 [공식 사이트](http://pillow.readthedocs.org/en/latest/installation.html#mac-os-x-installation) 가이드를 따라 어렵지 않게 설치 할 수 있습니다.

    $ brew install libtiff libjpeg webp little-cms2
    $ sudo pip install Pillow

이렇게 Pillow 설치가 완료되면, [font awesome to png] 로 폰트 파일에서 이미지를 추출해보도록 합니다.

[Font Awesome] 사이트를 통해 Font Awesome 을 다운 받습니다. 다운받은 zip 파일을 풀어 보면 `/font-awesome-XXX/fonts` 폴더에 TTF 파일이 존재할 것입니다.
이 폴더로 이동한 후 [font awesome to png] 의 [font-awesome-to-png.py](https://github.com/odyniec/font-awesome-to-png/blob/master/font-awesome-to-png.py) 을 복사해 넣은 후
아래와 같은 명령어로 폰트형태의 아이콘을 이미지로 변환할 수 있습니다.

    $ font-awesome-to-png.py --size 280 facebook

[Font Awesome]: http://fortawesome.github.com/Font-Awesome/
[font awesome to png]: https://github.com/odyniec/font-awesome-to-png
[PIL]: http://www.pythonware.com/products/pil/
[Pillow]: http://pillow.readthedocs.org
