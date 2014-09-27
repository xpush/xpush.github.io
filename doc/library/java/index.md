---
layout: doc-library
title: Java Library
date: April 25, 2014
---
# XPush Java Client

XPush service를 이용하기 위한 자바 및 안드로이드 라이브러리 입니다. 

XPush와 관련된 자세한 사항 및 아키텍쳐는 다음 페이지에 접속하면 확인할 수 있습니다. 
visit **https://xpush.github.com**

## Installation

현재는 git 에서 소스를 다운받아 컴파일 하여 사용할 수 있습니다.
visit https://github.com/xpush/lib-xpush-java

 * 추후에는 maven repository 에서도 사용 가능합니다. *

### Source

git 으로 부터 소스를 다운 받아 빌드할 수 있습니다.
**https://github.com/xpush/lib-xpush-java**

## API Overview

라이브러리를 사용하기 위해서는 XPush Session 서버 및 Channel 서버를 준비하고 Session서버에 접속 할 수 있어야 합니다. ApplicationId는 당신이 시스템을 구분하기 위한 역할을 할 뿐입니다. 동일한 ApplicationId내에서는 사용자 관리 및 채널등의 데이터가 구분됩니다.

{% highlight java %}
// XPush 를 사용하기 위한 메인 클래스를 생성한다. 
// XPush session 주소 (XPUSH_SERVER_HOST)
// XPush 에서 사용할 유니크한 Application Id 를 넣어서 생성한다.
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);

// 사용자 생성을 위해서는 아이디,패스워드,장치아이디,알림아이디가 필요합니다. 
// 안드로이드와 같은 gcm 을 사용하는 곳에서는 NOTIFICATION_ID를 넣으면 안드로이드 장치로 GCM을 보내줍니다. WEB 혹은 SERVER 같이 NOTIFICATION_ID가 필요 없는 곳에서는 사용하지 않습니다.
// 장비 아이디는 보통 모바일 기기의 아이디로 구분되어 지며 WEB 혹은 SERVER는 개발자 임의로 지정해도 상관없습니다.
xpush.signup( USER_ID, PASSWORD, DEVICE_ID, NOTIFICAITON_ID-options );

// 사용자는 여러개의 디바이스를 소유할 수 있다. 로그인을 위한 현재의 장치명도 함께 입력한다.
xpush.login( USER_ID, PASSWORD, DEVICE_ID);

// 채널을 생성한다. 같은 채널에 포함시킬 사용자 아이디들을 넣고 
// Channel 명을 지정하고 싶다면 두번째 param에 넣지만 null을 넣으면 자동생성한다.
// Channel 에는 추가 정보를 입력할 수 있다.
// 채널을 생성하게 되면 응답을 받을 callback
xpush.createChannel( new String[]{USER_IDS...}, null, new JSONObject(), new XPushEmitter.createChannelListener() {
    public void call(ChannelConnectionException e, String channelName,
                    ChannelConnection ch, List<User> users) {
    }
});

// 메시지를 전송한다. 메시지를 보낼 채널이름을 넣고 key-value 쌍으로 데이터를 넣고 응답을 기다린다.
xpush.send(CHANNEL_NAME, SEND_KEY, SEND_OBJECT, new Emitter.Listener() {
    public void call(Object... args) {
        // call after message send
    }
});

// 모든 채널에서 오는 모든 데이터를 수신할 수 있다. 데이터가 수신되면 callback함수를 호출한다.
xpush.on( ChannelConnection.RECEIVE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        // TODO Auto-generated method stub
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
{% endhighlight %}

# io.stalk.xpush.XPush

## The XPush constructor

The standard constructor take a host and an application id.

{% highlight java %}
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);
{% endhighlight %}

## Connecting (Signin)

In order to send and receive messages you need to login to XPush server.

{% highlight java %}
xpush.login( USER_ID, PASSWORD, DEVICE_ID);
or 
xpush.login( USER_ID, PASSWORD, DEVICE_ID, NOTIFICATION_ID);
{% endhighlight %}

## Signup

{% highlight java %}
xpush.signup( USER_ID, PASSWORD, DEVICE_ID);    
or
xpush.signup( USER_ID, PASSWORD, DEVICE_ID, NOTIFICATION_ID);
{% endhighlight %}

## Create Channel

In order to send and receive messages you need to create channel.

{% highlight java %}
xpush.createChannel( new String[]{"notdol102"}, null, new JSONObject(),  new XPushEmitter.createChannelListener() {
    public void call(ChannelConnectionException e, String channelName,
                    ChannelConnection ch, List<User> users) {
        // e is error code
        // channelName is created new channel name
        // ch is ChannelConnection Object
        // users is people in channel
    }
});
{% endhighlight %}

## Get All Channels name

{% highlight java %}
xpush.getChannels( new XPushEmitter.receiveChannelList() {
            public void call(String err,
                    List<Channel> channels) {
            }
        });
{% endhighlight %}

## Get Channel Object

{% highlight java %}
Channel ch = xpush.getChannel( CHANNEL_NAME );
{% endhighlight %}

## Join Channel

{% highlight java %}
xpush2.joinChannel(CHANNEL_NAME , USER_ID, new Emitter.Listener() {
    public void call(Object... args) {
        String err = (String) args[0];
    }
});
{% endhighlight %}

## LEAVE Channel

{% highlight java %}
xpush.exitChannel( CHANNEL_NAME , new Emitter.Listener() {
    public void call(Object... args) {
        String err = (String) args[0];
    }
});
{% endhighlight %}

## Get All User List in Application

{% highlight java %}
xpush.getUserList(new JSONObject(), new XPushEmitter.receiveUserList() {
    public void call(String err, List<User> users) {
    }
});
{% endhighlight %}

## Get All User List in Channel

{% highlight java %}
xpush.getUserListInChannel( CHANNEL_NAME , new JSONObject(), new XPushEmitter.receiveUserList() {
    public void call(String err, List<User> users) {
    }
});
{% endhighlight %}


## Send Message

Send Message to all users in specific channel. Message type is JSONObject.

{% highlight java %}
xpush.send(CHANNEL_NAME, SEND_MESSAGE_KEY, MESSAGE - new JSONObject(), new Emitter.Listener() {
    public void call(Object... arg0) {
        // call after send message
    }
});
{% endhighlight %}
## Receive Message

Receiving Message is available in two ways:

Receive all message without message_key.

{% highlight java %}
xpush2.on( ChannelConnection.RECEIVE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
{% endhighlight %}

The other way is specific message_key.

{% highlight java %}
xpush2.on( ChannelConnection.RECEIVE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
{% endhighlight %}

# io.stalk.xpush.Channel

## Disconnect channel

{% highlight java %}
channel.disconnect();
{% endhighlight %}
