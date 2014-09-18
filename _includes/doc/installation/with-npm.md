Installation with npm
===

XPUSH는 nodejs 기반으로 되어 있습니다. 여러분은 [npm](https://www.npmjs.org/)을 통해 쉽게 XPUSH를 설치할 수 있습니다.
<a name="prepare"></a>
<br />

## 1. Prepare

XPUSH를 사용하기 위해서는 [nodejs](http://nodejs.org/), [zookeeper](http://zookeeper.apache.org/), [redis](http://redis.io/), [mongodb](http://www.mongodb.org/)가 필요합니다.

아래는 64bit linux에서 설치하는 방법입니다. 여러분의 환경에 맞게 설치하세요.
이미 설치되어 있으면, xpush를 [바로 설치](#install)하세요.

### nodejs
[nodejs installation](http://nodejs.org/download/)를 참조하여 nodejs를 다운로드하고 압축을 해제합니다.

	mkdir -p $HOME/xpush
	cd $HOME/xpush
	wget http://nodejs.org/dist/v0.10.31/node-v0.10.31-linux-x64.tar.gz
	tar zvf node-v0.10.31-linux-x64.tar.gz

환경변수 PATH에 설정하여 node와 npm을 global하게 사용할 수 있도록 합니다.

	PATH=$HOME/xpush/node-v0.10.31-linux-x64/bin:$PATH

### zookeeper
[zookeeper installation](http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html)를 참조하여 zookeeper를 설치하고 실행합니다.

아래는 zookeeper 3.4.6을 설치하고 실행하는 코드입니다.

	cd $HOME/xpush
	wget http://apache.mirror.cdnetworks.com/zookeeper/stable/zookeeper-3.4.6.tar.gz
	tar xvf zookeeper-3.4.6.tar.gz
<p/>
	cp zookeeper-3.4.6/conf/zoo_sample.cfg zookeeper-3.4.6/conf/zoo.cfg
	cd zookeeper-3.4.6/bin
	./zkServer.sh start


### redis
[redis installation](http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html)를 참조하여 redis를 설치하고 실행합니다.

아래는 redis 2.8.14을 설치하고 실행하는 코드입니다.

	cd $HOME/xpush
	wget http://download.redis.io/releases/redis-2.8.14.tar.gz
	tar xzf redis-2.8.14.tar.gz
	cd redis-2.8.14
	make
<p/>
	src/redis-server

### mongodb
[mongodb installation](http://docs.mongodb.org/manual/installation/)를 참조하여 mongodb를 설치하고 실행합니다.

아래는 redis 2.6.4을 설치하고 실행하는 코드입니다.

	cd $HOME/xpush
	wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-2.6.4.tgz
	tar xzf mongodb-linux-x86_64-2.6.4.tar.gz
<p/>
	cd mongodb-linux-x86_64-2.6.4
	mkdir db
	bin/mongod --dbpath db


**Note**: Disk 공간이 3379MB보다 적으면 --smallfiles 옵션을 사용하세요.

	bin/mongod --dbpath db --smallfiles

### imageMagick

[image](http://www.imagemagick.org/script/binary-releases.php)를 참조하여 imageMagicK를 설치하고 실행합니다.

아래는 imageMagicK를 설치하고 실행하는 코드 입니다.

	wget http://www.imagemagick.org/download/linux/CentOS/x86_64/ImageMagick-6.8.9-7.x86_64.rpm
	rpm -Uvh ImageMagick-6.8.9-7.x86_64.rpm

<a name="install"></a>
<br />

## 2. Install xpush

모든 준비가 되었으면, xpush를 설치하세요. 준비가 되지 않았으면, [준비](#prepare)를 확인하세요.

### Install with npm

	npm install -g xpush

>**Note**:node-gyp rebuild 에러가 발생하는 경우는 node-gyp 버전이 0.10.10인데 node.js 버전이 0.8.xx 일때, 버젼차이 때문에 gyp가 패키지 리빌드를 못합니다. 이런 경우에는 $HOME/.node-gyp 폴더를 지우고 npm을 이용해 node-gyp를 새로 설치하면 됩니다.

### Install from github

latest development version을 설치하기 위해서는 [git](https://github.com/xpush/node-xpush)을 이용해서 직접 설치하세요.

>**Note**:직접 설치 시에 에러가 발생할 경우, nodejs가 최신 version인지 확인하세요.

	git clone https://github.com/xpush/node-xpush.git
	cd node-xpush
	npm install

<a name="run"></a>
<br />

## 3. Run xpush

세션 서버를 실행하세요.

	cd $HOME/xpush/node_modules/xpush
	bin/xpush --port 8000 --config ./config.sample.json --session

채널 서버를 실행하세요.

	cd $HOME/xpush/node_modules/xpush
	bin/xpush --port 8080 --config ./config.sample.json
