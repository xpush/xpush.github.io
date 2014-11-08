---
layout: doc
title: Documentation about XPUSH
date: September 8, 2014
---

We offers the documents of how to install, run and use the API of XPUSH.

Install the XPUSH platform to your local and server environment through this document, and let's start the development of various applications that leverage the XPUSH.

- - -

### GETTING STARTED
And we will explain how to install the XPUSH and how to create a configuration file.

#### > [Quick-start guide](/doc/quick-start) <span class="badge badge-theme">recommended</span>
For your fastest experience for the XPUSH, you can make the simple application through sample by installing directly.

Please try to run the sample application by directly install the image file that is configured in the docker container.

#### > [Installation](/doc/installation)
XPUSH use MongoDB, redis, and zookeeper to storage data and manage the real-time session and distributed environment.
Before you install the XPUSH, first we will explain how to install this kind of open-source server.

And, we will explain how to install the XPUSH with npm(node package manager) or docker image.

#### > [Configuration](/doc/configuration)
It describes the configuration parameters and how to create a configuration file in order to run the XPUSH.

- - -

### API DOCUMENTS
XPUSH consists of  **Session server** and **Channel server**.

Using the API provided by each server, you can put it implements real-time message communication function in your application.

#### > [Session Server](/doc/api/session)
Session server, one of XPUSH server platform, is responsible for assigning messaging server to clients and user authentication.

You can see the list of the API through this document.

#### > [Channel Server](/doc/api/channel)
The Channel server that is actually responsible for the sending and receiving messages in real time.

It offers a variety of features such as sending and receiving messages, Push Notification and unsent messages management. You can see the list of the API through this document.

- - -

### FOR CLIENTS
In order to implement your real-time service through the XPUSH server, you can use the libraries provided by developers from XPUSH and worldwide.

Currently, XPUSH developers are provided Javascript libraries for developing Web services and JAVA libraries for developing JAVA applications and Android applications. We are continually developing additional libraries.

#### > [Javascript library](/doc/library/javascript)
This is a Javascript XPUSH library for developing real-time web services

#### > [JAVA library](/doc/library/java)
This is a JAVA XPUSH library that supports building application such as  Android and JAVA based programs

- - -

### EXAMPLES
[Quick-start guide](/doc/quick-start) Another practical examples
