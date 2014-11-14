---
layout: doc
title: XPUSH, Quick-start guide
date: April 25, 2014
---

You can download and run the image file of XPUSH at the docker repository. it is possible to install and run and, in this way, you can be most easily and quickly experience the XPUSH.

XPUSH Docker image files is shared in [docker hub](https://registry.hub.docker.com/u/stalk/xpush/), You can download the image to your server or personal PC, and then you can install and run the xpush with it.

<br />

## 1. Docker Installation

[Docker Installation](https://docs.docker.com/installation/#installation) By referring to the link, install the Docker.

<br />

## 2. Downloading XPUSH Docker image

XPUSH Docker image which we provide is the already configured completely.
You can download the XPUSH image file as follows.

  > docker pull stalk/xpush:standalone

MongoDB and Redis and Zookeeper are installed in the downloaded image, and You can run the session server and channel server as a stand-alone.

<br />

## 3. Execution of XPUSH server

Now, you can run the image that you downloaded in Docker environment.

  > docker run -d --name xpush -p 8000:8000 -p 9000:9000 stalk/xpush:standalone /bin/bash xpush-stand-alone.sh

8000 port is a port number that Session server uses, 9000 port is the port number that Channel server uses. Xpush server that have been executed is accessible to 127.0.0.1.

To access to XPUSH server that you run in docker in OSX, you can obtain an IP address by using the following command. [More info](https://github.com/boot2docker/boot2docker)

  > boot2docker ip

<br />

## 4. Sample JavaScript Source

Include the [XPUSH client library](http://xpush.github.io/doc/library/javascript/xpush.js/index.html)

<pre data-lang="html">
<code class="prettyprint">&lt;script type="text/javascript" charset="utf-8" src="xpush.min.js"&gt;&lt;/script&gt;
</code>
</pre>

Create the xpush Object

<pre data-lang="js">
<code class="prettyprint">// parameter : server to connect, applicationId
var xpush = new XPush('http://127.0.0.1:8000', 'sample');
</code>
</pre>

When you create a XPUSH instance,
The first argument, it is session server address of XPUSH.
The server address to be used in the code is only session server.
As a result, XPUSH library will let you automatically connect to find the Channel server internally.

Now, I will generate a channel for the message received.

<pre data-lang="js">
<code class="prettyprint">xpush.createSimpleChannel('channel01', function(){
  console.log( 'create simple channel success' );

      // Output the data to the screen that comes in  `message` event.
  xpush.on( 'message', function(channel, name, data){
    console.log( channel, name, data );
  });
});
</code>
</pre>

Create a channel named `channel01`.
Channels are used as the address to send and receive messages, can be thought of as chat room in chatting program. Register a function to be called when it is created after the event occurred.

When the event named `message` occurs, the will be printed at the console.

Now, We can send the string "Hello World" to `message` events in` channel01` channel. String as well, JSON type is also available.

<pre data-lang="js">
<code class="prettyprint">xpush.send( 'channel01', 'message', 'Hello World' );</code>
</pre>

<br />

## 5. Live Demo

We will implement a simple chat in such a way as described above.

Full source can be found in [here](https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html).

The XPUSH server that was used in the demo below is a server for temporary test provided by the XPUSH development team, can not operate temporarily, it is not possible to guarantee the performance. So, please use the XPUSH on  your own.

#### Demo complete source
{% highlight html %}
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>

<!-- xpush -->
<script src="http://xpush.github.io/lib/dist/xpush.js"></script>

<script type="text/javascript">
// Create new xpush instance
var xpush = new XPush('http://demo.stalk.io:8000', 'demo');

$(document).ready( function(){
// Generate channel named `channel01`
  xpush.createSimpleChannel('channel01', function(){
    // Display success message after creation.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#success" ).html(html);
    $( "#success" ).show();

      // Output the data to the screen that comes in  `message` event.
    xpush.on( 'message', function(channel, name, data){
     data = decodeURIComponent( data );
      $( "#success" ).html( '<strong>Received success</strong> : ' + data );

      // Generate a new DOM object by copying the template.
      var newMessage = $( "#template" ).clone();

      // Change the DOM object that was the newly created.
      newMessage.attr( "id", "template_"+ Date.now() );
      newMessage.html( data );

      // Add the DOM object that was the newly created to the ul DOM object
      newMessage.appendTo( "#list" )
     newMessage.show()

      // Add css class to the DOM object that was the newly created
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
        <a href="https://github.com/xpush/lib-xpush-web/blob/master/example/simple.html" class="btn btn-primary btn-lg" role="button"
          View source from github
     </a>
      </p>
    </div>
    <div id="success" class="alert alert-success" role="alert" style="display:none">
    </div

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
// Generate a channel01.
xpush.createSimpleChannel('channel01', function(){
     // Display success message after creation.
    var html =  '<strong>Well done!</strong> Create simple channel success';
    $( "#success" ).html(html);
    $( "#success" ).show();

      // Output the data to the screen that comes in  `message` event.
    xpush.on( 'message', function(channel, name, data){
      data = decodeURIComponent( data );
      $( "#success" ).html( '<strong>Received success</strong> : ' + data );

      // Generate a new DOM object by copying the template.
      var newMessage = $( "#template" ).clone();

      // Change the DOM object that was the newly created.
      newMessage.attr( "id", "template_"+ Date.now() );
      newMessage.html( data );

      // Add the DOM object that was the newly created to the ul DOM object
      newMessage.appendTo( "#list" );
      newMessage.show();

      // Change the DOM object that was the newly created.
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

#### Code Result

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
