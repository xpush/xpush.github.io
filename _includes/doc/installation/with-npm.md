Installation with npm
===

XPUSH based on nodejs. You can easily install the XPUSH through the [npm] (https://www.npmjs.org/).
<a name="prepare"></a>
<br />

## 1. Prepare

To use the XPUSH is, [nodejs] (http://nodejs.org/), [zookeeper] (http://zookeeper.apache.org/), [redis] (http://redis.io/) , [mongodb] (http://www.mongodb.org/) is required .

The following is how to install 64bit linux. Please install to suit your environment.
If you have already been installed,  the xpush [installation immediately] (# install) please.

### nodejs
[nodejs installation] (http://nodejs.org/download/) by referring to Download and unzip the nodejs.

	mkdir -p $HOME/xpush
	cd $HOME/xpush
	wget http://nodejs.org/dist/v0.10.32/node-v0.10.32-linux-x64.tar.gz
	tar zvf node-v0.10.32-linux-x64.tar.gz

Set the PATH environment variable so that you can use the node and npm to global.

	PATH=$HOME/xpush/node-v0.10.32-linux-x64/bin:$PATH

### zookeeper
[zookeeper installation] install and run the with reference to the zookeeper (http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html).

The following is the code to install and run the zookeeper3.4.6.

	cd $HOME/xpush
	wget http://apache.mirror.cdnetworks.com/zookeeper/stable/zookeeper-3.4.6.tar.gz
	tar xvf zookeeper-3.4.6.tar.gz
<p/>
	cp zookeeper-3.4.6/conf/zoo_sample.cfg zookeeper-3.4.6/conf/zoo.cfg
	cd zookeeper-3.4.6/bin
	./zkServer.sh start


### redis
[redis installation] (http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html) install and run the with reference to redis.

The follow is the code to install and run redis 2.8.14.

	cd $HOME/xpush
	wget http://download.redis.io/releases/redis-2.8.14.tar.gz
	tar xzf redis-2.8.14.tar.gz
	cd redis-2.8.14
	make
<p/>
	src/redis-server
	(daemon : $ nohup src/redis-server & )

### mongodb
[mongodb installation] install and run  mongodb with reference (http://docs.mongodb.org/manual/installation/).

The follow is the code to install and run redis 2.6.4

	cd $HOME/xpush
	wget --no-check-certificate https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-2.6.4.tgz
	tar xzf mongodb-linux-x86_64-2.6.4.tar.gz
<p/>
	cd mongodb-linux-x86_64-2.6.4
	mkdir db
	mkdir logs
	bin/mongod --fork --dbpath db --logpath logs


**NOte**: use --smallfiles option if disk space less than 3379MB

	bin/mongod --dbpath db --smallfiles

### imageMagick

[image] to install and run imageMagicK  with reference to (http://www.imagemagick.org/script/binary-releases.php).

The follow is the code install and run imageMagick.

CentOS

	yum -y install ImageMagick-c++ ImageMagick-c++-devel

ubuntu

	apt-get install imagemagick

Install from Source

	wget http://www.imagemagick.org/download/ImageMagick.tar.gz
	tar xvzf ImageMagick.tar.gz
	cd ImageMagick-6.8.9-8
	./configure && make && sudo make install
	sudo ldconfig /usr/local/lib
	sudo ln -s /usr/local/bin/convert /usr/bin/convert

<a name="install"></a>
<br />

## 2. Install xpush

When you are all ready, please install the xpush. If not ready, please make sure the [preparation] (# prepare).
You can use the -g option it is recommended that you install to the global.

### Install with npm

	npm install -g xpush

>**Note**: If the node-gyp rebuild error occurs, when version of node-gyp is 0.10.10 node.js is 0.8.xx, gyp can not re-build the package for different versions. In such a case, you can install it using the npm by erasing the $ HOME / .node-gyp folder node-gyp to new.

	npm install -g node-gyp

### Install from github

To install the latest development version, please install directly using the [git] (https://github.com/xpush/node-xpush).

	git clone https://github.com/xpush/node-xpush.git
	cd node-xpush
	npm install

<a name="run"></a>
<br />

## 3. Run xpush

Please run the session server. For more options, please make sure the [here] (http://xpush.github.io/doc/configuration/#run_config).

	cd $HOME/xpush/node_modules/xpush
	bin/xpush --port 8000 --config ./config.sample.json --session

Please run the channel server. For more options, please make sure the [here] (http://xpush.github.io/doc/configuration/#run_config).

	cd $HOME/xpush/node_modules/xpush
	bin/xpush --port 9000 --config ./config.sample.json
