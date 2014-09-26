---
layout: doc-library
title: Java Library
date: April 25, 2014
---
# XPush Java Client

XPush client library for Java targeting **Android** and general Java.

## Installation

The compiled library is available in one ways:
visit https://github.com/xpush/lib-xpush-java

 * ready for maven repository *

### Source

You can build the project from the source in this repository. See **https://github.com/xpush/lib-xpush-java** for more information on build environment.

## API Overview

{% highlight java %}
// XPush 를 사용하기 위한 메인 클래스를 생성한다. 
// XPush session 주소 (XPUSH_SERVER_HOST)
// XPush 에서 사용할 유니크한 Application Id 를 넣어서 생성한다.
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);

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

NOT YET.

## LEAVE Channel

NOT YET.

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
