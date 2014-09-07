---
layout: doc
title: Javascript Lybrary
date: April 25, 2014
---

Currently under development.

XPUSH is installed and managed via npm, the Node.js package manager.

### Preparation to install

Before install and run XPUSH servers, you have to install zookeeper, redis and mongoDB.

- Install and start zookeeper. (http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html)

- Install and start redis.
(http://redis.io/download)

- Install and start mongoDB. (http://docs.mongodb.org/manual/installation/)

### Install XPUSH

In order to get started, you'll want to install ```node-xpush``` globally. You may need to use sudo (for OSX, *nix, BSD etc) or run your command shell as Administrator (for Windows) to do this.

	npm install -g node-xpush

This will put the ```xpush``` command in your system path, allowing it to be run from any directory.

	$ xpush
	usage: xpush [options]

	Starts a xpush server using the specified command-line options

	options:
	  --port   PORT       (mandatory!) Port that the xpush server should run on
	  --config OUTFILE    (mandatory!) Location of the configuration file for the xpush server
	  --silent            Silence the log output from the xpush server
	  --session           start the session server for load-balancing xpush 	servers
	  --host              Hostname
	  -h, --help          You're staring at it


### Configuration

In order to start XPUSH server, you have to create configuration file with zookeeper, redis, mongoDB addresses. Empty address is localhost and default ports.

	$ vi config.json
	{
	  "zookeeper": {},
	  "redis": {},
	  "mongodb": {}
	}


### Run XPUSH Servers

There are two distinct ways to use XPUSH: through the command line interface, or by requiring the xpush module in your own code.

#### -- Start XPUSH Server from the command line

##### Start XPUSH Session server

	$ xpush --port 8000 --config ./config.sample.json —session

##### Start XPUSH Channel server

	$ xpush --port 9990 --config ./config.sample.json
