---
layout: doc
title: Quick-start guide
date: April 25, 2014
---

XPUSH 를 가장 쉽고 빠르게 경험하기 위해서, Docker 기반의 이미지 파일을 사용할 수 있습니다.

XPUSH Docker 이미지 파일은, [docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 를 통해 이미지를 여러분의 서버 또는 PC 에 설치 할 수 있습니다.

<br />

## 1. Docker 설치

[Docker Installation](https://docs.docker.com/installation/#installation) 를 참조하여 Docker 를 설치 합니다.

<br />

## 2. XPUSH Docker 이미지 다운로드

[docker hub](https://registry.hub.docker.com/u/stalk/xpush/) 에는 이미 설정이 완료된 XPUSH 환경이 구성되어 있습니다. 다음과 같이 XPUSH 이미지 파일을 다운로드 받습니다.

	> docker pull stalk/xpush

다운로드 받은 이미지에는, XPUSH 에서 사용하는 MongoDB, Redis 그리고 Zookeeper 가 설치되어 있고, XPUSH Session 서버와 Channel 서버가 실행될 수 있도록 정의 되어 있습니다.

<br />

## 3. XPUSH 서버 실행

다운로드 받은 이미지를 Docker 환경에서 실행합니다.

	> docker run -d --name xpush -p 8000:8000 -p 9000:9000 stalk/xpush

8000 포트는 Session 서버가 사용하는 포트번호이고, 9000 포트는 Channel 서버가 사용하는 포트번호 입니다.

<br />

## 4. Sample JavaScript Source

Xpush client library를 include합니다.

<pre data-lang="html">
<code class="prettyprint">&lt;script type="text/javascript" charset="utf-8" src="xpush.min.js"&gt;&lt;/script&gt;
</code>
</pre>

Xpush를 생성합니다.

<pre data-lang="js">
<code class="prettyprint">// parameter : server to connect, applicationId
var xpush = new XPush('http://stalk-front-s01.cloudapp.net:8000', 'sample');
</code>
</pre>

channel01 생성 후에 event 발생 시에 호출할 function을 등록합니다.
`message` event를 처리할 수 있습니다.

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

channel01로 `message` event를 발생시키면서 Hello World를 전송합니다. 

<pre data-lang="js">
<code class="prettyprint">xpush.send( 'channel01', 'message', 'Hello world' );</code>
</pre>

<br />

## 5. Live Demo

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>

<!-- xpush -->
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>

<script type="text/javascript">
// Create new xpush
var xpush = new XPush('http://stalk-front-s01.cloudapp.net:8000', 'sample');

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

full source는 [여기](https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html)에서 확인할 수 있습니다.

<script type="text/javascript">
	prettyPrint();
</script>
