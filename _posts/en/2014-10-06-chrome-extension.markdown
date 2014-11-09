---
layout: post
title:  "STALK.IO 크롬 익스텐션"
date:   2014-10-06 14:08:11
categories: Programming
tags: xpush
og:
  type: article
  description: 크롬 익스텐션의 간략한 소개와 [STALK.IO 크롬 익스텐션](https://chrome.google.com/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher) 구현 방식을 소개합니다.
---

안녕하십니까. ***XPUSH*** 개발팀입니다.

XPUSH 를 기반으로 한 서비스인 [stalk.io](http://xpush.github.io/announcement/stalk-io.html) 는 웹페이지에 설치할 수 있는 체팅 위젯입니다.

이렇게 운영자 혹은 관리자가 직접 설치하는 것과는 별도로, 여러분들이 직접 원할 때, 원하는 웹 페이지에서 같은 페이지를 보는 다수의 사람들과
서로 대화할 수 있는 [stalk.io 크롬 익스텐션](https://chrome.google.com/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher)도 사용하실 수 있습니다.

##크롬 익스텐션이란(Chrome Extensions)?##

크롬 익스텐션은 크롬 브라우져에 추가(확장) 가능한 HTML + JavaScript 기반의 웹 어플리케이션 입니다.

[크롬 웹스토어](https://chrome.google.com/webstore/category/extensions?hl=ko)에서 설치 가능하며, 본인에게 필요한 App을 설치하여 사용한다면, 
웹 사용 환경을 보다 빠르고 강력하게 만들 수 있습니다.

[크롬 웹스토어](https://chrome.google.com/webstore/category/extensions?hl=ko)에서 설치 할수 있는 크롬 앱의 종류에는
[크롬 익스텐션](https://developer.chrome.com/extensions)과 [Packaged App](https://developer.chrome.com/apps/about_apps), [Hosted App](https://developers.google.com/chrome/apps/docs/developers_guide) 이 있습니다.

개발자가 Hosted App을 만들어야 할지, Packaged App을 만들어야 할지, Extensions를 만들어야 할지 고민 된다면

[Choosing an App Type](https://developer.chrome.com/webstore/choosing)에서 실마리를 찾을 수 있습니다.


<img src="https://developer.chrome.com/webstore/images/flowchart.png" width="100%" align="center">


##익스텐션 UI##

익스텐션 UI는 주소창 우측의 toolbar의 아이콘을 통해서 실행이 되며, 대표적인 구성 요소에는 팝업, [page action](https://developer.chrome.com/extensions/pageAction), [browser action](https://developer.chrome.com/extensions/browserAction)이 있습니다.


##익스텐션 구성요소##

#### 1.[백그라운드 페이지](https://developer.chrome.com/extensions/background_pages)

HTML또는 JavaScript파일로 구성되며, 익스텐션의 주요 로직을 담당합니다. 팝업이나 컨텐츠 스크립트등 익스텐션 내부의 
다른 페이지들과 interact하게 됩니다. browser action이나 page action에서 발생하는 이벤트들에 대한 listener를 정의할 수도 있습니다.
브라우져에 노출 되지는 않고 뒷단(Back ground)에서 별도의 프로세스로 실행됩니다.
    

#### 2.[팝업 페이지](https://developer.chrome.com/extensions/getstarted)

익스텐션 아이콘을 클릭했을 때 나타나는 페이지로 flickr API를 이용한 [Chrome extension Get Started](https://developer.chrome.com/extensions/getstarted) 페이지의 예제로 쉽게 이해할 수 있습니다. 백그라운드 페이지와 interact 가능합니다.
    

#### 3.[컨텐츠 스크립트](https://developer.chrome.com/extensions/content_scripts)

실행중인 웹 페이지에 삽입하는 스크립트로서 이 또한 백그라운드 페이지와 커뮤니케이션이 가능합니다.



##stalk.io 크롬 익스텐션##

[stalk.io 크롬 익스텐션](https://chrome.google.com/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher)은 browser action, 백그라운드 페이지로 개발되었습니다.

manifest.json

<pre data-lang="html">
<code class="prettyprint">
  "browser_action": {
    "default_icon": "images/stalk_16.png"
  },
  "background": {
        "scripts": ["background.js"]
  },
</code>
</pre>

background.js

<pre data-lang="html">
<code class="prettyprint">
  	chrome.browserAction.onClicked.addListener(
	    function(tab) { 
	        chrome.tabs.executeScript(null, { // defaults to the current tab
	            file: "main.js", // script to inject into page and run in sandbox
	            allFrames: false // This injects script into iframes in the page and doesn't work before 4.0.266.0.
	        });
	    }
	);
</code>
</pre>

stalk.io 아이콘을 클릭하면 background.js의 browswerAction.onClicked 이벤트가 발생되며, chrome.tabs.executeScript를 통해
main.js 파일이 실행 됩니다. 이 main.js 파일에는 [stalk.js](http://www.stalk.io/stalk.js)를 inject하고,
STALK.init() 을 실행시키는 코드가 담겨 있습니다.

완전한 소스는 [XPUSH github](https://github.com/xpush/chrome.stalk.io)을 통해 확인하시기 바랍니다.

