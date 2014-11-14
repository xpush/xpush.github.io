---
title: "What is XPUSH ?"
description: "Opensource Project - Realtime Web Communication Platform"
layout: about
---

<div class="row centered">
  <div class="embed-responsive embed-responsive-4by3">
    
    <iframe class="embed-responsive-item" src="//www.youtube.com/embed/9r-4ZvWvRTg?rel=0" allowfullscreen></iframe>
  </div>
</div>

- - -

<img src="/about/resource/rwcp.png" width="45%" align="right">

You  can easily build real-time services with HTML5 Websocket and  socket.io, the module on node.js.

There are a lot of things to consider on building the real-time services. You need to consider various things such as distributed server configuration in consideration of the users who are rapidly increasing, network traffic processing of large capacity and scale-out strategy.

**XPUSH** is a server platform that provides such as real-time message communication, message storage, user and device management and Mobile Push Notification. Everyone will be able to install and use after you downloaded.
And, XPUSH is a service platform for developing a wide range of real-time services and applications over a single **XPUSH** server platform.

Without having to directly implement the real-time communication servers, After you install **XPUSH** platform, try adding a real-time data communication features of your service.

- - -

### 1. Real-time Web Communication Platform

" *We help you easily build real-time applications. XPUSH was designed for developers.* "

**XPUSH** consists of real-time communication servers that has been developed as a Web technology. You can build  a variety of real-time messaging applications such as your own messenger and chat services

Currently, **XPUSH** developers are provided Javascript libraries for developing Web services and JAVA libraries for developing JAVA applications and Android applications. We are continually developing additional libraries.

Real-time data communication, while maintaining the network connection between the Client and the Server, can transmit and receive data bidirectionally. If the connection to the server is lost, or even if some servers fails, it guarantees the service availability.

- - -

### 2. Works Everywhere

" *At the core of XPUSH is the HTML5 WebSocket protocol, but we also have fallback mechanisms so that XPUSH just works anywhere, anytime.* "

**XPUSH** was developed by node.js to implement high-performance servers. It uses Socket.IO, one of the modules for web-based real-time messaging. And we continue to update the modules to use the latest version.

Socket.IO enables real-time bidirectional event-based communication. It works on every platform, browser or device, focusing equally on reliability and speed.

- - -

<img src="/about/resource/software.png" width="40%" align="left">

### 3. Scalable Web Architecture

" *XPUSH was designed to work with commodity servers, an elastic virtualised environments saving you money and headaches. A scalable web application can handle growth – of users or work – without requiring changes to the source code and stoping existed servers* "

Real time server platform has to be designed to be able to handle a large amount of network traffic to a sharply rising splice. It has to run a large number of servers for load balancing, needs to designed to be non-disruptive expansion. **XPUSH** developers designed the **Scalable Web Architecture** for a long time, and continue to optimize the architecture design.

**XPUSH** manages the real-time status of the distributed servers through a **zookeeper** and use **Redis** to store connection information of the visitors and meta datas in the memory. And XPUSH stores various types of unstructured messages sent or received in **MongoDB**. 

**XPUSH** server platform run each in the Session server and Channel server.
Channel server is responsible to authenticate users, managing user and device information, and assigning distribution server for load balancing. Since relatively Channel servers are easy to increase the load on the network traffic, so that it can be added separately as an expansion only Channel server

#### - Standalone operations

You can install the XPUSH with standalone(all-in-one) mode from Docker image. MongoDB, Redis, Zookeeper, XPUSH Session server and XPUSH Channel server will be installed on one server at a time for testing purposes, or in the development stage. For more information, you can check in the [Installation](/doc/installation/#docker) document.

#### - Considering the scalability distributed environment (production environment)

In order to install and use the XPUSH in the operating environment, MongoDB Cluster, Redis Cluster, Zookeeper Cluster, XPUSH Session server and Channel server will be installed in each separate image or servers. For more information, you can check in the [Installation](/doc/installation) document.

<img src="/about/resource/infra.png" width="100%" align="center">

- - -

### Project Source Repository

#### **[github.com/xpush](http://github.com/xpush)**
> **node-xpush** : source of XPUSH server platform<br>
> **node-xpush-client**: source of XPUSH server Node.js Client module <br>
> **lib-xpush-web** : Javascript XPUSH library <br>
> **lib-xpush-java** : JAVA  XPUSH library <br>
> **dockerfile** : Docker files for XPUSH Images on Docker Hub <br>
> **messengerX** : Instant Messenger solution based XPUSH ([messengerx](http://messengerx.github.io)) <br>
> **chrome.messengerX** : Chrome Extension source for messengerX<br>
> **stalk.io** : XPUSH based web chatting widget source([stalk.io](http://stalk.io)) <br>
> **chrome.messengerX** : Chrome Extension source for stalk.io <br>
> **xpush.github.io**: Homepage source for introducing XPUSH
