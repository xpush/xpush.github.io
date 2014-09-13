---
layout: doc
title: Configuration
date: September 9, 2014
---

세션서버를 아래와 같이 실행 했듯이
	bin/xpush --port 8000 --config ./config.sample.json --session

XPUSH 를 실행하기 위해 설정파일은 json형태로 되어 있습니다. 설정에 대해 자세히 알아봅니다.

	{
	  "zookeeper": {},
	  "redis": {},
	  "mongodb": {},
	  "oauth": {},
	  "apps" : []
	}

<a name="sys_config"></a>
<br/>

## System Configuration

XPUSH가 사용할 zookeeper, redis, mongodb 정보를 설정합니다.

### zookeeper

xpush가 사용할 zookeeper의 주소를 설정합니다.

	zookeeper:{'address':'127.0.0.1:2181'}

### redis

xpush가 사용할 redis의 주소를 설정합니다.

	redis:{'address':'127.0.0.1:6379'}

### mongodb

xpush가 사용할 mongodb의 주소를 설정합니다.

	mongodb:{'address':'127.0.0.1:27017'}

<a name="app_config"></a>
<br/>

## Application Configuration

XPUSH를 통해 서비스할 Application에 대한 정보를 설정합니다.

<a name="oauth_config"></a>

### oauth

oauth provider를 설정을 추가합니다. XPUSH는 oauth provider에 등록된 Application 정보를 이용하여 LOGIN 처리 후 event를 발생시킬 수 있습니다.

#### 1. facebook

facebook에 등록한 App ID와 App Secret를 등록합니다.

	"facebook": {
	  "key": "App ID Here",
	  "secret": "App Secret Here",
	  "event": {
	    "name": "login-facebook"
	  },
	  "success": "<script>window.close();</script>"
	}

>**Note**:Site URL은 http://*www.sample.net*/auth/facebook/callback 으로 등록되어 있어야합니다.

#### 2. twitter

twitter에 등록한 App key와 App secret를 등록합니다.

	"twitter": {
	  "key": "App key Here",
	  "secret": "App secret Here",
	  "event": {
	    "name": "login-twitter"
	  },
	  "success": "<script>window.close();</script>"
	}


>**Note**:Callback URL는 http://*www.sample.net*/auth/google/callback 으로 등록되어 있어야합니다.


#### 3. Google+

Google console에 등록한 CLIENT ID와 CLIENT SECRET를 등록합니다.

	"googleplus": {
	  "key": "CLIENT ID Here",
	  "secret": "CLIENT SECRET Here",
	  "event": {
	    "name": "login-google"
	  },
	  "success": "<script>window.close();</script>"
	}

>**Note**:REDIRECT URIS는 http://*www.sample.net*/auth/google/callback 으로 등록되어 있어야합니다.

### apps

XPUSH 서버가 사용할 application정보와 GCM or APN key를 등록합니다.

	"apps" : [
	  {
	    "id" : "applicationId Here",
	    "name": "application Name Here",

	    "notification": {
	      "gcm": {
		"apiKey": "Google API KEY for GCM Here"
	      },
	      "apn": {
		"apiKey": "APNS Key Here"
	      }
	    }
	  }
	]

<a name="run_config"></a>
<br/>

## Runtime Configuration

xpush를 실행할 때 사용할 option을 설정합니다.

####port

XPUSH server가 실행될 때 사용할 PORT. default 80

	--port 8000

####config

XPUSH server 실행될 때 사용할 CONFIG FILE 위치

	--config ./config.sampel.json

####session

XPUSH server 실행될 때 ***SESSION*** mode로 실행한다. default ***CHANNEL***

	--session

####silent

XPUSH server 실행될 때 ***SILENT*** mode로 실행한다. Log를 찍지 않는다.

	--silent

####host

XPUSH server 실행될 때 사용할 hostname. 해당 URL은 oauth에 사용할 callback url의 앞부분에 사용된다.
[oauth 설정](#oauth_config) 참고

	--host http://www.sample.net
