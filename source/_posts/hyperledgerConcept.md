---
title: Hyperledger基本概念及环境部署
categories:
  - 区块链
abbrlink: 61740
date: 2019-02-13 15:44:09
img: /img/区块链技术实现.png
---
## 区块链学习笔记

### 共享账本
1. 世界状态：描述账本特定时间点的状态，是账本的数据库。
2. 交易记录：记录产生世界状态当前值的所有交易，是世界状态的更新历史。
3. 账本：世界状态数据库和交易历史记录的集合。

### 智能合约
1. chaincode
2. 只访问账本的数据库组件和世界状态，不会查询交易记录。目前支持语言：GO

### fabric环境配置
1. Git
2. Go - 1.10.x版本
3. Docker - 17.06.2-ce版本或以上
4. Docker Compose - 1.14.0版本或以上
5. Pip 安装方法：sudo apt-get install python-pip
6. 过高的docker和docker-compose环境和fabric1.0不兼容
7. fabric v1.0.0 使用 Docker - 17.06.2-ce， docker-compose - 1.12.0
8. ubuntu系统有兼容问题？？

### go环境的配置
1. 下载golang：

		https://studygolang.com/dl

2. 解压并移动到/usr/local/go中
	
		sudo tar -zvxf go1.6.2.linux-amd64.tar.gz
		sudo mv go /usr/local/go

3. 设置环境变量
	
		sudo gedit /etc/profile
		export GOROOT=/usr/local/go  #设置为go安装的路径
		export GOPATH=$HOME/gocode   #默认安装包的路径
		export PATH=$PATH:$GOROOT/bin:$GOPATH/bin

4. 应用环境

		source /etc/profile

### 拉取Fabric源码

	git clone https://github.com/hyperledger/fabric.git

### 编译configtxgen工具

	cd $GOPATH/src/github.com/hyperledger/fabric
	make configtxgen
	# 如果出错：'ltdl.h' file not found
	sudo apt install libtool libltdl-dev
	# 然后再运行make
	make configtxgen


### 测试e2e_cli
1. 执行如下脚本：
	
		./network_setup.sh up


	这个做了如下操作：
 
	1. 编译生成Fabric公私钥、证书的程序，程序在目录：fabric/release/linux-amd64/bin

	2. 基于configtx.yaml生成创世区块和通道相关信息，并保存在channel-artifacts文件夹。

	3. 基于crypto-config.yaml生成公私钥和证书信息，并保存在crypto-config文件夹中。

	4. 基于docker-compose-cli.yaml启动1Orderer+4Peer+1CLI的Fabric容器。

	5. 在CLI启动的时候，会运行scripts/script.sh文件，这个脚本文件包含了创建Channel，加入Channel，安装Example02，运行Example02等功能。


![images](/blog/img/区块链技术实现.png)


