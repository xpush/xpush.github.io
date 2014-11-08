---
layout: doc
title: XPUSH, Quick-start guide
date: April 25, 2014
---

여러분은 Docker 이미지 파일을 다운로드 받아 실행하여 XPUSH 를 설치 및 실행할 수 있으며 이를 통해 XPUSH 를 가장 쉽고 빠르게 경험해 볼 수 있습니다.

XPUSH Docker 이미지 파일은, [docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 에 공유되어 있으며, 이미지를 다운로드 받아서, 여러분의 서버 또는 개인 PC 에 설치하고 실행해 볼 수 있습니다.

<br />

## 1. Docker 설치

[Docker Installation](https://docs.docker.com/installation/#installation) 를 참조하여 Docker 를 설치 합니다.

<br />

## 2. XPUSH Docker 이미지 다운로드

[docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 에서 제공하는 XPUSH Docker 이미지에는 이미 설정이 완료된 XPUSH 환경이 구성되어 있습니다.
다음과 같이 XPUSH 이미지 파일을 다운로드 받습니다.

  > docker pull stalk/xpush:standalone

다운로드 받은 이미지에는, XPUSH 에서 사용하는 MongoDB, Redis 그리고 Zookeeper 가 설치되어 있고, XPUSH Session 서버와 Channel 서버가 실행되도록 되어 있습니다.

<br />

## 3. XPUSH 서버 실행

이제, 다운로드 받은 이미지를 Docker 환경에서 실행합니다.

  > docker run -d --name xpush -p 8000:8000 -p 9000:9000 stalk/xpush:standalone /bin/bash xpush-stand-alone.sh

8000 포트는 Session 서버가 사용하는 포트번호이고, 9000 포트는 Channel 서버가 사용하는 포트번호 입니다. 실행이 완료된 xpush 서버에는 127.0.0.1로 접근이 가능합니다.

OSX에서 docker로 실행한 XPUSH서버에 접근하기 위해서는 다음의 명령어를 통해 IP를 획득합니다. [More info](https://github.com/boot2docker/boot2docker)

  > boot2docker ip

<br />

## 4. Sample JavaScript Source

[XPUSH client library](http://xpush.github.io/doc/library/javascript/xpush.js/index.html) 를 include합니다.

<pre data-lang="html">
<code class="prettyprint">&lt;script type="text/javascript" charset="utf-8" src="xpush.min.js"&gt;&lt;/script&gt;
</code>
</pre>

XPUSH 인스턴스를 생성합니다.

<pre data-lang="js">
<code class="prettyprint">// parameter : server to connect, applicationId
var xpush = new XPush('http://127.0.0.1:8000', 'sample');
</code>
</pre>

XPUSH 인스턴스 생성시,
첫번째 인자로는 위에서 실행한 XPUSH의 Session 서버 주소 입니다.
코드에서 사용하는 XPUSH 서버 주소는 Session 서버 뿐입니다.
그러면 XPUSH 라이브러리가 내부에서 Channel 서버를 찾아 자동 연결시켜 주게 됩니다.

이제, 메시지 수신을 위한 채널을 생성하도록 합니다.

<pre data-lang="js">
<code class="prettyprint">xpush.createSimpleChannel('channel01', function(){
  console.log( 'create simple channel success' );

  // `message` event로 들어오는 data를 받아 화면에 출력합니다.
  xpush.on( 'message', function(channel, name, data){
    console.log( channel, name, data );
  });
});
</code>
</pre>

`channel01` 이라는 이름으로 채널을 생성합니다.
채널은 메시지를 송수신할 주소로 사용 되며, 체팅 프로그램에서의 체팅방이라고 생각할 수 있습니다.  생성 후에 event 발생 시에 호출할 function을 등록합니다.

생성한 채널에 `message` 라는 이름의 이벤트가 발생하면 로그가 남기도록 개발합니다.

이제, `channel01` 채널로 `message` 이벤트로 'Hello World' 문자열을 전송합니다. 문자열 뿐 아니라, JSON 타입도 가능합니다.

<pre data-lang="js">
<code class="prettyprint">xpush.send( 'channel01', 'message', 'Hello World' );</code>
</pre>

<br />

## 5. Live Demo

위와 같은 방법으로 간단한 채팅을 구현해봅니다.

full source는 [여기](https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html)에서 확인할 수 있습니다.

아래 데모에서 사용한 XPUSH 서버는 XPUSH 개발팀에서 제공하는 임시 테스트용 서버이므로, 일시적으로 동작하지 않거나 성능을 보장할 수 없습니다. 그러므로 여러분이 직접 설치하신 XPUSH 를 사용하시기 바랍니다.

#### 데모 전체 소스
{% highlight html %}
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>

<!-- xpush -->
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>

<script type="text/javascript">
// Create new xpush instance
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
  // channel01 을 생성합니다.
  xpush.createSimpleChannel('channel01', function(){
    // 생성 후에 success 메시지를 보여줍니다.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#success" ).html(html);
    $( "#success" ).show();

      // `message` event로 들어오는 data를 받아 화면에 출력합니다.
    xpush.on( 'message', function(channel, name, data){
      data = decodeURIComponent( data );
      $( "#success" ).html( '<strong>Received success</strong> : ' + data );

      // template을 복사하여 새로운 DOM 객체를 생성합니다..
      var newMessage = $( "#template" ).clone();

      // 새로 만든 DOM 객체를 수정합니다.
      newMessage.attr( "id", "template_"+ Date.now() );
      newMessage.html( data );

      // 새로 만든 DOM 객체를 ul DOM에 추가합니다.
      newMessage.appendTo( "#list" );
      newMessage.show();

      // 새로 생성한 DOM 객체에 class를 추가합니다.
      $( ".list-group-item-success" ).each(function(){
        $(this).removeClass( "list-group-item-success" );
      });
      newMessage.addClass( "list-group-item-success" );
    });
  });
});

var send = function( ){
  var msg = $( "#message" ).val();
  xpush.send( 'channel01', 'message', encodeURIComponent( msg ) );
  $( "#message" ).val('');
};

</script>

<div class="row" style="margin-top:20px;">
  <div class="col-sm-12">
    <div class="jumbotron">
      <h1>Simple Channel Example</h1>
      <p class="lead">Send a message with simple channel</p>
      <p>
        <a href="https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html" class="btn btn-primary btn-lg" role="button">
          View source from github
        </a>
      </p>
    </div>
    <div id="success" class="alert alert-success" role="alert" style="display:none">
    </div>

    <div style="display:flex;">
      <input class="form-control" placeholder="Input message" name="message" id="message" type="text" value=""/>
      <button type="submit" id="form-button" class="btn btn-primary" style="margin-left:10px;" onclick="send();">
        Send
      </button>
    </div>
    <span class="help-block">Input message to send. The message will be displayed in under area</span>

    <div class="row">
      <div class="col-sm-8">
        <h2>Received message</h2>
        <ul id="list" class="list-group">
          <li id="template" class="list-group-item" style="display:none;">There is no message</li>
        </ul>
      </div>
    </div>
  </div>
</div>
{% endhighlight %}



<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>

<!-- xpush -->
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>

<script type="text/javascript">
// Create new xpush instance
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
  // channel01 을 생성합니다.
  xpush.createSimpleChannel('channel01', function(){
    // 생성 후에 success 메시지를 보여줍니다.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#success" ).html(html);
    $( "#success" ).show();

      // `message` event로 들어오는 data를 받아 화면에 출력합니다.
    xpush.on( 'message', function(channel, name, data){
      data = decodeURIComponent( data );
      $( "#success" ).html( '<strong>Received success</strong> : ' + data );

      // template을 복사하여 새로운 DOM 객체를 생성합니다..
      var newMessage = $( "#template" ).clone();

      // 새로 만든 DOM 객체를 수정합니다.
      newMessage.attr( "id", "template_"+ Date.now() );
      newMessage.html( data );

      // 새로 만든 DOM 객체를 ul DOM에 추가합니다.
      newMessage.appendTo( "#list" );
      newMessage.show();

      // 새로 생성한 DOM 객체에 class를 추가합니다.
      $( ".list-group-item-success" ).each(function(){
        $(this).removeClass( "list-group-item-success" );
      });
      newMessage.addClass( "list-group-item-success" );
    });
  });
});

var send = function( ){
  var msg = $( "#message" ).val();
  xpush.send( 'channel01', 'message', encodeURIComponent( msg ) );
  $( "#message" ).val('');
};

</script>

<br />

#### 실행 결과

<div class="row" style="margin-top:20px;">
  <div class="col-sm-12">
    <div class="jumbotron">
      <h1>Simple Channel Example</h1>
      <p class="lead">Send a message with simple channel</p>
      <p><a href="https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html" class="btn btn-primary btn-lg" role="button">View source from github</a></p>
    </div>
    <div id="success" class="alert alert-success" role="alert" style="display:none">
    </div>

    <div style="display:flex;">
      <input class="form-control" placeholder="Input message" name="message" id="message" type="text" value=""/>
      <button type="submit" id="form-button" class="btn btn-primary" style="margin-left:10px;" onclick="send();">Send</button>
    </div>
    <span class="help-block">Input message to send. The message will be displayed in under area</span>

    <div class="row">
      <div class="col-sm-8">
        <h2>Received message</h2>
        <ul id="list" class="list-group">
          <li id="template" class="list-group-item" style="display:none;">There is no message</li>
        </ul>
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
  prettyPrint();
</script>
