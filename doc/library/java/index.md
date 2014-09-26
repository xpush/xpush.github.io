---
layout: doc-library
title: Java Library
date: April 25, 2014
---
# XPush Java Client

XPush client library for Java targeting **Android** and general Java.

## Installation

The compiled library is available in one ways:

ready for maven repository.

### Source

You can build the project from the source in this repository. See **Library development environment** for more information on build environment.

## API Overview

{% highlight java %}
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);
xpush.login( USER_ID, PASSWORD, DEVICE_ID);

// Create channel
xpush.createChannel( new String[]{USER_IDS...}, null, new JSONObject(), new XPushEmitter.createChannelListener() {
    public void call(ChannelConnectionException e, String channelName,
                    ChannelConnection ch, List<User> users) {
    }
});

// Send message
xpush.send(CHANNEL_NAME, SEND_KEY, SEND_OBJECT, new Emitter.Listener() {
    public void call(Object... args) {
        // call after message send
    }

});

// Receive message
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
