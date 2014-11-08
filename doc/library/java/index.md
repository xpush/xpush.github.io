---
layout: doc-library
title: Java Library
date: April 25, 2014
---
# XPush Java Client

This is a XPush JAVA library for using Java and Android.

Details and architecture associated with XPush is available by connecting to the following page. 
visit **https://xpush.github.com**

## Installation

Currently you can use to compile and download the source from git.
visit https://github.com/xpush/lib-xpush-java

 * It will be supported in maven repository soon *

### Source

you can build from downloaded source in git repogitory, 
**https://github.com/xpush/lib-xpush-java**

## API Overview

In order to use the library to prepare the XPush Session Server and Channel server and the server must be able to connect to the Session. ApplicationId is just you serve to distinguish the system. In the same ApplicationId data such as user management and channel are separated.

{% highlight java %}
// Generates the main class for use XPush.
// XPush session address (XPUSH_SERVER_HOST)
// Create a unique Application Id to use in XPush
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);

// ID, password, device ID, the notification ID is required to create a user 
// If you have entered the NOTIFICATION_ID, XPUSH will send the GCM to android device. Where NOTIFICATION_ID do not need as WEB SERVER or not used.
// Equipment ID is usually used for NOTIFICATION ID in the mobile device,
It can be specified arbitrarily in WEB or SERver.
xpush.signup( USER_ID, PASSWORD, DEVICE_ID, NOTIFICAITON_ID-options );

// The user may possess a number of devices. The current name of the device for login also enter together.
xpush.login( USER_ID, PASSWORD, DEVICE_ID);

// Generate channel with user IDs to be included in the same channel.
// If you want to specify the Channel name, input the channle name insecond param. It will be automatically generated when it is null.
// You can enter additional information.
// callback function to be performed after generate
xpush.createChannel( new String[]{USER_IDS...}, null, new JSONObject(), new XPushEmitter.createChannelListener() {
    public void call(ChannelConnectionException e, String channelName,
                    ChannelConnection ch, List<User> users) {
    }
});

// And transmits the message. Input a name of the channel with message data to send. Then wait for a response
xpush.send(CHANNEL_NAME, SEND_KEY, SEND_OBJECT, new Emitter.Listener() {
    public void call(Object... args) {
        // call after message send
    }
});

// You can receive all the data coming from all channels. When data is received, call the callback function.
xpush.onMessageReceived(new XPushEmitter.messageReceived() {
    @Override
    public void call(String channelName, String key, JSONObject value) {
        System.out.println(channelName +":"+ key +":"+ value);
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
xpush2.onMessageReceived(new XPushEmitter.messageReceived() {
    @Override
    public void call(String channelName, String key, JSONObject value) {
        System.out.println(channelName +":"+ key +":"+ value);
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
