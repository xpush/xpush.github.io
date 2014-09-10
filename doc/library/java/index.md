# XPush Java Client

XPush client library for Java targeting **Android** and general Java.

## Installation

The compiled library is available in one ways:

ready for maven repository.

### Source

You can build the project from the source in this repository. See **Library development environment** for more information on build environment.

## API Overview

```java
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);
xpush.login( USER_ID, PASSWORD, DEVICE_ID);

// Create channel
xpush.createChannel( USER_IDS, null, new JsonObject(), new Emitter.Listener() {
    public void call(Object... arg0) {
        // TODO Auto-generated method stub
        String result = (String)arg0[0];
        String chName = (String)arg0[1];
        io.stalk.xpush.Channel ch = (io.stalk.xpush.Channel)arg0[2];
    }
});

// Send message
xpush.send(CHANNEL_NAME, SEND_KEY, SEND_OBJECT, new Emitter.Listener() {
    public void call(Object... args) {
        // call after message send
    }

});

// Receive message
xpush.on( io.stalk.xpush.Channel.RECEIVE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        // TODO Auto-generated method stub
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
```

# io.stalk.xpush.XPush

## The XPush constructor

The standard constructor take a host and an application id.

```java
XPush xpush = new XPush( XPUSH_SERVER_HOST, APPLICATION_ID);
```

## Connecting (Signin)

In order to send and receive messages you need to login to XPush server.

```java
xpush.login( USER_ID, PASSWORD, DEVICE_ID);
```

## Signup

```java
xpush.signup( USER_ID, PASSWORD, DEVICE_ID);
```

## Create Channel

In order to send and receive messages you need to create channel.

```java
xpush.createChannel( new String[]{"notdol102"}, null, new JsonObject(), new Emitter.Listener() {
    public void call(Object... arg0) {
        // TODO Auto-generated method stub
        String result_status = (String)arg0[0];
        String channel_name = (String)arg0[1];
        io.stalk.xpush.Channel ch = (io.stalk.xpush.Channel)arg0[2];
    }
});
```

## Get All Channels name

```java
xpush.getChannels( new Emitter.Listener() {
    public void call(Object... arg0) {
        // TODO Auto-generated method stub
        String result_status = (String)arg0[0];
        String[] channels_name = (String)arg0[1];
    }
});
```

## Get Channel Object

```java
Channel ch = xpush.getChannel( CHANNEL_NAME );
```

## Join Channel

NOT YET.

## LEAVE Channel

NOT YET.


## Send Message

Send Message to all users in specific channel. Message type is JSONObject.

```java
xpush.send(CHANNEL_NAME, SEND_MESSAGE_KEY, MESSAGE - new JSONObject(), new Emitter.Listener() {
    public void call(Object... arg0) {
        // call after send message
    }
});
```
## Receive Message

Receiving Message is available in two ways:

Receive all message without message_key.

```java
xpush2.on( io.stalk.xpush.Channel.RECEIVE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
```

The other way is specific message_key.

```java
xpush2.on( MESSAGE_KEY ,new Emitter.Listener() {
    public void call(Object... args) {
        String channel_name = (String) args[0];
        String message_key = (String) args[1];
        JSONObject message_data = (JSONObject) args[2];
    }
});
```

# io.stalk.xpush.Channel

## Disconnect channel

```java
channel.disconnect();
```


