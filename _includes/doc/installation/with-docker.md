
<br /><center><img src="/doc/installation/resource/docker_logo.png"></center><br />


Installation with docker
===

XPUSH는 Docker에 이미지가 등록되어 있습니다. 여러분은 [Docker](https://www.docker.com/)을 이용해 쉽게 XPUSH를 설치할 수 있습니다.
<a name="prepare"></a>
<br />

## 1. Prepare

XPUSH를 사용하기 위해서는 [nodejs](http://nodejs.org/), [zookeeper](http://zookeeper.apache.org/), [redis](http://redis.io/), [mongodb](http://www.mongodb.org/)가 필요합니다.

[Docker installation](https://docs.docker.com/installation/#installation)를 참조하여 Docker를 설치하세요. 아래는 Ubuntu 13.10에서 Docker를 설치하는 방법입니다.

AUFS Filesystem support를 위한 package를 설치합니다. (이미 설치되어 있을 수도 있습니다.)

	sudo apt-get update
	sudo apt-get install linux-image-extra-`uname -r`

Debian package로 Docker를설치하기 위한 Docker repository key를 등록합니다.

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9

Docker repository를 등록하고, Docker를 설치합니다.

	sudo sh -c "echo deb http://get.docker.io/ubuntu docker main/etc/apt/sources.list.d/docker.list"
	sudo apt-get update
	sudo apt-get install lxc-docker

<a name="install"></a>
<br />

## 2. Install xpush

모든 준비가 되었으면, docker에 등록된 xpush image를 설치하세요. 네트워크 환경에 따라 총 10분~60분 정도 소요됩니다.

	docker pull stalk/xpush:standalone

<a name="run"></a>
<br />

## 3. Run with docker

docker로 xpush 실행하기 위해 아래와 같은 옵션을 사용합니다.

> <p />**-d** : docker 실행시 daemon형태로 실행합니다.
> <p />**--name** : docker 컨테이너의 이름을 설정합니다.
> <p />**-p** : docker 컨테이너의 port와 host의 포트를 매핑합니다. 이 설정을 통해 docker 안의 포트에 접속이 가능합니다.
>
>&nbsp;&nbsp;&nbsp;&nbsp;사용법 -p _hostPort:containerPort_
> <p />**-v** : docker 컨테이너의 디렉토리와 host의 디렉토리를 매핑합니다. 이 설정을 통해 host의 설정파일을 docker 안에서 사용할 수 있습니다.
>
>&nbsp;&nbsp;&nbsp;&nbsp;사용법 -v _hostDirectory:containerDirectory_

### 3-1. Run xpush standalone

stalk/xpush:standalone dokcer image에는 XPUSH에 필요한 모든 S/W가 포함되어 있습니다.

다운로드 받은 이미지를 Docker 환경에서 실행합니다. 이미지에 포함된 start script `stand-alone-session.sh`를 사용합니다. *host*에 여러분의 ip나 domain 을 입력하세요.

	docker run -d --name xpush -p 8000:8000 -p 9000:9000 stalk/xpush:standalone /bin/bash xpush-stand-alone.sh --host sample.stalk.io

>**Note**:docker 실행시 permission 에러가 발생할 경우, sudo 를 이용하여 docker를 실행하세요.

### 3-2. Run xpush separately

stalk/xpush:latest image에는 xpush를 사용하는데 필요한 기능만 포함되어 있습니다. 여러분의 redis, zookeeper, mongodb와 연동하여 XPUSH를  사용할 수 있습니다.

#### Run session server

빠른 시작을 위해서는 이미지에 포함된 start script `xpush-session.sh`를 사용합니다.

	docker run -d -p 8000:8000 -v /home/stalk/data:/data stalk/xpush:latest /bin/bash xpush-session.sh

>**Note**: shell script는 아래와 같은 option을 사용하도록 설정되어 있습니다.
>redis, zookeeper, mongodb 정보가 설정된 `session.json` 파일이 `/home/stalk/data` 디렉토리 안에 있어야합니다.

	--config /data/session.json --session --port 8000

xpush 실행파일을 사용해서 session 서버를 시작합니다. 실행에 필요한 option을 원하는대로 설정할 수 있습니다. 자세한 옵션은 [여기](http://xpush.github.io/doc/configuration/#run_config)를 확인하세요.

	docker run -i -t -p 8080:8080 -v /home/stalk/data:/data stalk/xpush:latest xpush --config /data/session01.json --session --port 8080 --host sample.stalk.io

#### Run channel server

빠른 시작을 위해서는 이미지에 포함된 start script `xpush-channel.sh`를 사용합니다.

	docker run -d -p 9000:9000 -v /home/stalk/data:/data stalk/xpush:latest /bin/bash xpush-channel.sh

>**Note**: shell script는 아래와 같은 option을 사용하도록 설정되어 있습니다.
>redis, zookeeper, mongodb 정보가 설정된 `channel.json` 파일이 `/home/stalk/data` 디렉토리 안에 있어야합니다.

	--config /data/channel.json --port 9000

xpush 실행파일을 사용해서 session 서버를 시작합니다. 실행에 필요한 option을 원하는대로 설정할 수 있습니다. 자세한 옵션은 [여기](http://xpush.github.io/doc/configuration/#run_config)를 확인하세요.

	docker run -i -t -p 9090:9090 -v /home/stalk/data:/data stalk/xpush:latest xpush --config /data/channel01.json --port 9090 --host sample.stalk.io