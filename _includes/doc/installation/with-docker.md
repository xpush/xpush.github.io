
<br /><center><img src="/doc/installation/resource/docker_logo.png"></center><br />


Installation with docker
===

XPUSH the image of Docker has been registered. You will be able to easily install the XPUSH by using the [Docker](https://www.docker.com/).
<a name="prepare"></a>
<br />

## 1. Prepare

To use the XPUSH, it is required [nodejs](http://nodejs.org/), [zookeeper](http://zookeeper.apache.org/), [redis](http://redis.io/), [mongodb](http://www.mongodb.org/).

Please install the [Docker installation](https://docs.docker.com/installation/#installation) with reference to the Docker. The following is how to install the Docker in Ubuntu13.10.

 Install the package for AUFS Filesystem support.

	sudo apt-get update
	sudo apt-get install linux-image-extra-`uname -r`

Register a Docker repository key to install the Docker with the Debian package.

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9

After registering the Docker repository, and then install the Docker.

	sudo sh -c "echo deb http://get.docker.io/ubuntu docker main/etc/apt/sources.list.d/docker.list"
	sudo apt-get update
	sudo apt-get install lxc-docker

<a name="install"></a>
<br />

## 2. Install xpush

When you are all ready, install the xpush image that has been registered in the docker. Depending on the network environment, it takes a total of about 10 to 30 minutes.

	docker pull stalk/xpush:standalone

<a name="run"></a>
<br />

## 3. Run with docker

In order to xpush run with docker, you can use the following options.

> <p /> **- d**: docker run as a daemon.
> <p /> **- name**: the name of the docker container.
> <p /> **- p**: It will map the port of host port with docker container. You can be connected to the port in the docker using this setting.
>
>&nbsp;&nbsp;&nbsp;&nbsp;Usage -p _hostPort:containerPort_
> <p /> **- v**: It will map the directory of host and the directory of docker container. You can use the configuration file of the host using this setting in the docker.
>
>&nbsp;&nbsp;&nbsp;&nbsp;Usage -v _hostDirectory:containerDirectory_

### 3-1. Run xpush standalone

stalk/xpush:standalone docker image will contain all of the S/W required to XPUSH.

Run the downloaded image with Docker environment. Use the start script`stand-alone-session.sh` that are included in the image. Please enter your ip and domain to *host*.

	docker run -d --name xpush -p 8000:8000 -p 9000:9000 stalk/xpush:standalone /bin/bash xpush-stand-alone.sh --host sample.stalk.io

> **Note**: If a run-time permission error was encountered, use the sudo to run the docker.

### 3-2. Run xpush separately

stalk/xpush:latest image will contain only the features required to use the xpush. You can use your redis, zookeeper and mongodb.

#### Run session server

Quick for the start, use the start script`xpush-session.sh` that are included in the image.

	docker run -d -p 8000:8000 -v /home/stalk/data:/data stalk/xpush:latest /bin/bash xpush-session.sh

> **Note**: shell script has been configured to use the option, such as the following.
> You need to make a`session.json` file in `/home/stalk/data` directory that has been configured information for redis, zookeeper, mongodb.

	--config /data/session.json --session --port 8000

Start the session server using the xpush executable file. You can set the way you want the option necessary to run. For more options, please make sure the [here](http://xpush.github.io/doc/configuration/#run_config).

	docker run -i -t -p 8080:8080 -v /home/stalk/data:/data stalk/xpush:latest xpush --config /data/session01.json --session --port 8080 --host sample.stalk.io

#### Run channel server

Quick for the start, use the start script `xpush-channel.sh` that are included in the image.

	docker run -d -p 9000:9000 -v /home/stalk/data:/data stalk/xpush:latest /bin/bash xpush-channel.sh

> **Note**: shell script has been configured to use the option, such as the following.
> You need to make a`channel.json` file in `/home/stalk/data` directory that has been configured information for redis, zookeeper, mongodb.

	--config /data/channel.json --port 9000

Start the channel server using the xpush executable file. You can set the way you want the option necessary to run. For more options, please make sure the [here](http://xpush.github.io/doc/configuration/#run_config).

	docker run -i -t -p 9090:9090 -v /home/stalk/data:/data stalk/xpush:latest xpush --config /data/channel01.json --port 9090 --host sample.stalk.io
