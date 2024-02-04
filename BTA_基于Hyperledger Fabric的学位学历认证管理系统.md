 <img src="/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-08-31 09.48.04.png" alt="截屏2023-08-31 09.48.04" style="zoom:60%;" />

| **报告名称**：   | 基于Hyperledger Fabric的学位学历认证管理系统 |
| ---------------- | -------------------------------------------- |
| **学生姓名：**   | 杨超然                                       |
| **学生学号：**   | 202106060220                                 |
| **专业班级：**   | 信管2101班                                   |
| **学**  **院：** | 工商管理学院                                 |
| **指导老师：**   | 周中定                                       |
| **日  期：**     | 2023.9.7                                     |

------

[TOC]

------

![截屏2023-12-04 10.56.17](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.56.17.png)

# 一、项目背景

学历造假和认证造假的问题在全球范围内日益普遍，这不仅对社会产生了巨大的负面影响，也极大地增加了企业和单位的用人成本，造成了无谓的经济消耗。同时，纸质或电子版的证书无论从保存便利性、验证可靠性、可信性等方面，都存在着很大的不足。为了解决这些问题，一种高度可靠、易保存、易证伪同时还顾全隐私保护的学位学历认证管理系统，显得尤为重要。

区块链技术提供了一种非中心化信任网络，非常适合作为此类分布式应用的底层架构和基础工具。区块链具有两大核心特点：一是数据难以篡改，二是去中心化。这意味着区块链所记录的信息更加真实可靠，可以帮助解决人们互不信任的问题。

通过区块链技术的应用，我们可以构建一个更加安全、可靠、高效的学位学历认证管理系统。这样的系统不仅可以有效防止学历造假和认证造假的问题，还可以提高证书的可信度和验证的便利性，降低企业和单位的用人成本。同时，通过合理的隐私保护设计，可以保护用户的个人隐私信息，避免不必要的泄露和风险。

因此，研究和发展基于区块链的学位学历认证管理系统具有重要的现实意义和实际应用价值。这不仅可以提高教育领域的管理水平和效率，还可以为其他领域提供更加安全、可靠、高效的数据管理和信息验证解决方案，推动社会的数字化转型和发展。

# 二、功能分析

由于系统需要保证人才受教育情况真实性，所以对于系统的用户而言，不可能由用户自己添加相应的学历信息，而是由具有一定权限的用户来完成添加或修改的功能。但普通用户可以通过系统溯源功能来确定信息的真伪。所以我们将系统用户的使用角色分为两种：

**·普通用户**
**·管理员用户**
普通用户具有对数据的查询功能 ，但实现查询之前必须经过登录认证：

**·用户登录：**系统只针对合法用户进行授权使用，所以用户必须先进行登录才能完成相应的功能。
**·查询实现：**查询分为两种方式实现
	根据证书编号与姓名查询：根据用户输入的证书编号与姓名进行查询。
	根据身份证号码查询：根据用户输入指定的身份证号码进行查询，此功能可以实现溯源。
管理员用户除具有普通用户的功能之外，额外添加了两个功能：

**·添加信息：**可以向系统中添加新的学历信息。
**·修改信息：**针对已存在的学历信息进行修改。
最后需要达到的要求

1.认证颁发时的数据生成和上链流程；
2.认证验证时的验证方法：核实电子证书文件的哈希值；
3.用户界面：证书生成、证书上链、证书查询、证书核验；
4.安全和隐私保护。

# 三、项目设计

## （一）架构设计

在此应用中需要编写实现完整的链码并通过业务层调用链码中的各个函数，以实现对数据状态的操作。界面为了方便用户操作使用，使用Web浏览器的方式实现。而且在此应用中我们将 Hyperledger Fabric 默认的状态数据库由 LevelDB 替换为 CouchDB 来实现

![截屏2023-10-25 07.56.47](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-25 07.56.47.png)

![截屏2023-10-25 07.57.15](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-25 07.57.15.png)

Fabric Network 结构包括几个组件，如 CA、MSP、Orderer 等 ，如下图所示

·CA 代表证书颁发机构。它负责颁发和管理数字证书，用于识别 Fabric Network 中的参与者 。
·MSP 代表成员服务提供商。它定义了验证网络参与者身份和角色的规则和策略 。
·Orderer 是一个节点，它将交易排序成区块，并将它们发送给对等节点 。
·Org1 是属于 Fabric Network 的一个组织的例子。它可以有自己的 CA、MSP、策略和通道 。
·Peer0 和 Peer1 是属于 Org1 的两个节点。它们存储账本的副本并执行链码（智能合约）。
![截屏2023-10-25 08.09.15](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-25 08.09.15.png)

## （二）数据模型设计

由于需要向分类账本中保存数据，所以必须设计相关的结构体用于声明要保存的数据结构，用于方便的在应用中处理数据。

![截屏2023-10-25 08.12.35](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-25 08.12.35.png)

![截屏2023-10-25 08.12.57](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-25 08.12.57.png)

为了能够从当前的分类状态中查询出详细的历史操作记录，我们在 Education 中设计了一个类型为HistoryItem 数组的 Historys 成员，表示当前状态的历史记录集。
HistoryItem 结构体设计如下表所示：

| 名称      | 数据类型  | 说明                   |
| --------- | --------- | ---------------------- |
| TxId      | string    | 交易编号               |
| Education | Education | 本次历史记录的详细信息 |

## （三）原型交互设计

[Reference Link](https://js.design/v?i=8znNXl&p=R9WgEyOBdM&f=iGC6qsWuq2qnKAa_mCCcF)

![截屏2023-10-30 22.14.40](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.14.40.png)

![截屏2023-10-30 22.14.58](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.14.58.png)

![截屏2023-10-30 22.15.22](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.15.22.png)

![截屏2023-10-30 22.15.36](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.15.36.png)

# 四、其他项目相关说明

## （一）链码及API操作

education/chaincode/edu.go实现了使用链码相关的API如GetState，PutState，GetQueryResult等对分类账本状态进行具体操作的各个函数：

**·PutEdu**：实现将指定的对象序列化后保存至分类账本中
**·GetEduInfo**：根据指定的Key（身份证号码）查询对应的状态，反序列后将对象返回
**·getEduByQueryString**：根据指定的查询字符串从 CouchDB 中查询状态
**·addEdu**：接收对象并调用 PutEdu 函数实现保存状态的功能
**·queryEduByCertNoAndName**：根据指定的证书编号与姓名查询状态
**·queryEduInfoByEntityID**：根据指定的身份证号码（Key）查询状态
**·updateEdu**：实现对状态进行编辑功能

实例图如下：

![截屏2023-10-30 22.21.14](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.21.14.png)

![截屏2023-10-30 22.21.53](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-30 22.21.53.png)

## （二）数据存储方式说明

在开始部署项目之前，我们需要对数据存储方式和存储类别进行一个简要的说明：

我们所要进行存储操作的是链上数据，它是直接或者间接由区块链共识产生的数据，一般包括区块链节点数据与智能合约代码等

区块链节点数据包括区块数据和状态数据两者
1.区块数据描述区块自身信息数据，实际是区块链上面发生的每一笔交易的记录（如添加学历信息，修改学历信息及对应的时间戳）
2.状态数据则是记录了每个账户和智能合约的当前状态（如张三的学历信息）

区块链节点的数据存储最普遍的方式就是存入我们的磁盘中间，而我们的区块链不会直接的访问我们的磁盘，而是通过特定的数据库如LevelIDB，CouchDB等独立和分布式的数据库来操作我们的数据。

本项目选用**CouchDB**作为存储链上数据的数据库

CouchDB是用[Erlang](https://baike.baidu.com/item/Erlang?fromModule=lemma_inlink)开发的面向文档的[数据库系统](https://baike.baidu.com/item/数据库系统?fromModule=lemma_inlink)，2010年7月14日发布了1.0版本。CouchDB不是一个传统的关系数据库，而是面向文档的数据库，其数据存储方式有点类似[lucene](https://baike.baidu.com/item/lucene?fromModule=lemma_inlink)的index文件格式，CouchDB最大的意义在于它是一个面向web应用的新一代[存储系统](https://baike.baidu.com/item/存储系统?fromModule=lemma_inlink)，事实上，CouchDB的口号就是：下一代的Web应用存储系统。

CouchDB 可以安装在大部分 POSIX 系统上，包括 Linux® 和 Mac OS X。Version 2.2.0开始正式支持Windows (x64)。CouchDB 可以从源文件安装，也可以使用包管理器安装（比如在 Mac OS X 上使用 MacPorts）。

CouchDB 是一个顶级 Apache Software Foundation 开源项目，根据 Apache 许可 V2.0 发布。这个开源许可允许在其他软件中使用这些源代码，并根据需要进行修改，但前提是遵从版权需知和免责声明。与许多其他开源许可一样，这个许可允许用户根据需求使用、修改和分发该软件。不一定由同一个许可包含所有修改，因为我们仅维护一个 Apache 代码使用许可需知。

采用CouchDB存储数据，具有如下特点：

一、CouchDB是分布式的数据库，他可以把[存储系统](https://baike.baidu.com/item/存储系统?fromModule=lemma_inlink)分布到n台物理的节点上面，并且很好的协调和同步节点之间的数据读写一致性。这当然也得靠Erlang无与伦比的并发特性才能做到。对于基于web的大规模应用文档应用，分布式可以让它不必像传统的关系数据库那样分库拆表，在应用代码层进行大量的改动。

二、CouchDB是面向文档的数据库，存储半结构化的数据，比较类似[lucene](https://baike.baidu.com/item/lucene?fromModule=lemma_inlink)的index结构，特别适合存储文档，因此很适合CMS，电话本，地址本等应用，在这些应用场合，[文档数据库](https://baike.baidu.com/item/文档数据库?fromModule=lemma_inlink)要比关系数据库更加方便，性能更好。

三、CouchDB支持REST API，可以让用户使用[JavaScript](https://baike.baidu.com/item/JavaScript?fromModule=lemma_inlink)来操作CouchDB数据库，也可以用JavaScript编写查询语句，我们可以想像一下，用[AJAX](https://baike.baidu.com/item/AJAX?fromModule=lemma_inlink)技术结合CouchDB开发出来的CMS系统会是多么的简单和方便。

![截屏2023-11-08 21.23.32](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-08 21.23.32.png)

## （三）项目采用Fabric的优势

本项目采用fabric作为实现方式，其具有以下优势：

1.所有上链数据基于密码学数字签名，上链数据不可被第三方篡改，不可抵赖。
基于多参与方的共识算法，防止单方面作恶，修改自己链上历史数据。
2.Fabric淡化了去中心化(Decentralized)，而以分布式 (Distributed)代替，每个节点具有完整全账本，防止单机故障和数据丢失。
3.fabric采用中心化的CA机制来发放证书，只有持有有效证书的节点和用户才可以访问区块链上的账本数据，此外还有TLS加密通讯、通道隔离等技术，保证了数据的传播范围可控，防止数据被窃取。
4.Fabric充分利用了模块化的设计、容器技术和密码学技术，使得系统具有可扩展、灵活和安全等特性。采用传统数据库或许可以实现部分区块链的功能，如设计只写数据库，设计访问权限，共享数据库，分布式协议等，但这不是数据库的功能，只是创造出一个另一种形势的“区块链”，传统数据库无法达到区块链的分布式和信任。

## （四）Hyperledger explorer——Fabric浏览器

http:/localhost:8080/
Hyperledger Explorer是一个简单易用的开源工具，可以用于监视 区块链网络中的活动。Hyperledger Explorer支持Fabric、Iroha等 多种区块链，可以运行在MocOS和Ubuntu下。我们可以在这里看到链上的所有信息，例如交易的时间、区块的哈希值和数据的哈希值等。
[浏览器部署教程](https://blog.csdn.net/qq_45808700/article/details/130183648)——该教程仅供了解，本项目已经部署浏览器，不需要再部署，若有其他项目需要部署可参考该教程

![截屏2023-11-10 17.15.44](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.15.44.png)

成功部署之后的相关界面如下：

![](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.16.08.png)

![截屏2023-11-10 17.19.35](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.19.35.png)

![截屏2023-11-10 17.20.26](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.20.26.png)

## （五）分布式数据库

CouchDB 数据库1：peer0节点7051端口 http:/localhost:5984/_utils

CouchDB 数据库2：peer1节点9051端口 http://localhost:7984/_utils

上个读取与写入账本数据的实验中也使用了CouchDB,界面如下：

![截屏2023-11-10 17.17.05](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.17.05.png)

## （六）不可篡改性

CouchDB数据库存取链上数据，将学历信息等数据写入到数据库，窜改数据库的数据时链上的数据没有改变，查询时返回数据为链上数据，所以篡改数据库不可修改系统返回数据。

![截屏2023-11-10 17.21.09](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.21.09.png)

![截屏2023-11-10 17.21.21](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-10 17.21.21.png)

# 五、项目部署流程

[Project Reference Link](https://github.com/Pistachiout/Academic-Degree-BlockChain)

[视频教程](https://www.bilibili.com/video/BV15X4y1F7mX/?spm_id_from=333.788.recommend_more_video.-1&vd_source=1eecda9234223595880a3a83e762d22e)

![截屏2023-11-12 10.51.24](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-12 10.51.24.png)

## （一）配置文件说明

1. `config.yaml`配置文件：描述组织结构、通道结构、所有配置文件、证书的路径
2. `chaincode`：链码文件所在的目录
	- `vendor`：依赖包
	- `edu.go`：链码（智能合约）
3. `fixtures`：fabric基础网络所有文件所在的目录
4. `img`：存放前端的图片
5. `sdkInit`：sdk核心代码
	- `sdkSetting.go`：实现了包括Setup、CreateAndJoinChannel、CreateCCLifecycle等基础功能的函数
	- `integration.go`：实现了包括DiscoverLocalPeers、InitService等基础功能的函数
	- `sdkInfo.go`：定义了一些组织和SDK中用到的结构体
	- `set/get.go`：复制调用链码的两个函数
6. `service`：服务端代码，封装了合约接口的调用
	- `eduService.go`：调用链码的关键所在
7. `explorer`：区块链浏览器文件及配置

[项目功能介绍](https://blog.csdn.net/qq_45808700/article/details/129423030)

## （二）项目部署环境

•Linux Ubuntu（root 用户）

•Golang 语言 ：建议使用go1.17，版本过高会出现问题

•docker 18.09.7

•docker-compose 1.22.0


## （三）环境配置

### 1.安装 docker 以及 docker-compose

使用 docker 可以方便的解决程序依赖的环境问题；并且后续需要使用到的 Hyperledger Fabric 框架官方也提供了相应的 docker 的容器。
安装 docker 命令：

```bash
sudo apt install docker.io
```

验证 docker 成功安装：docker version，结果如图所示：

![截屏2023-12-04 10.01.13](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.01.13.png)

为了方便管理多个 docker 容器，还需要安装 docker-compose：
```bash
sudo apt install docker- compose
```
验证安装成功：docker-compose version，如图所示：

![截屏2023-12-04 10.01.48](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.01.48.png)

### 2.安装 golang

区块链框架Hyperledger Fabric 目前支持Java、Go 等主流编程语言并提供了相应的SDK，但是支持最全面的还是 Golang，因此采用 Go 语言来进行开发是比较好的选择；

 1. 安装 Golang：wget https://golang.google.cn/dl/go1.17.linux-amd64.tar.gz
 下载受网络环境影响，如果您本地有相应的 tar 包，则直接解压到指定的路径下即可。
 3. 使用 tar 命令将压缩文件解压至指定路径/usr/local/下： tar -zxvf go1.17.0.linux-amd64.tar.gz -C /usr/local
 4. sudo -s使当前用户为 root 用户，$HOME=/root
 5. sudo vim /etc/hosts,添加：

```bash
127.0.0.1  orderer.example.com
127.0.0.1  peer0.org1.example.com
127.0.0.1  peer1.org1.example.com
```

![截屏2023-12-04 10.03.07](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.03.07.png)


  5. sudo vim /root/.bashrc文件,添加环境变量 GOHOME 以及 GOROOT：  

```bash
export GOPATH=/root/go
export GOROOT=/usr/local/go
export PATH=$GOROOT/bin:$PATH
```

![截屏2023-12-04 10.04.35](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.04.35.png)

  6. 激活环境变量： source /etc/profile
  7. 更新网络：service network-manager restart
  8. 验证安装成功，使用 go version 结果如图所示：

![截屏2023-12-04 10.05.26](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.05.26.png)

### 3.安装部署Hyperledger explorer——Fabric浏览器

http:/localhost:8080/
Hyperledger Explorer是一个简单易用的开源工具，可以用于监视 区块链网络中的活动。Hyperledger Explorer支持Fabric、Iroha等 多种区块链，可以运行在MocOS和Ubuntu下。我们可以在这里看到链上的所有信息，例如交易的时间、区块的哈希值和数据的哈希值等。
[浏览器部署教程](https://blog.csdn.net/qq_45808700/article/details/130183648)——该教程仅供了解，本项目已经部署浏览器，不需要再部署，若有其他项目需要部署可参考该教程
![截屏2023-12-04 22.02.53](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 22.02.53.png)

![截屏2023-12-04 22.03.45](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 22.03.45.png)

## （四)前置部署

### 1.项目部署

创建保存项目的文件夹： mkdir -p $GOPATH/src

进入文件夹： cd $GOPATH/src

从 github 仓库克隆项目到education文件夹： git clone https://github.com/Pistachiout/Academic-Degree-BlockChain.git education

![截屏2023-12-04 10.09.09](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.09.09.png)

进入项目目录：cd education

添加项目开发需要依赖的 Golang 包：go mod tidy

![截屏2023-12-04 10.10.21](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.10.21.png)

![截屏2023-12-04 10.10.36](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.10.36.png)

**⚠️注意：**命令可能会执行失败，此时设置代理即可：go env -w GOPROXY=https://goproxy.cn


### 2.启动项目

由于每次启动流程相对固定，首先进入root用户，并配置环境，然后启动项目在项目的目录下运行 clean_docker.sh 脚本即可启动项目：

```bash
sudo -s
source /root/.bashrc
cd $GOPATH/src/education
export GO111MODULE=on
export GOPROXY=https://goproxy.io
go mod tidy
./clean_docker.sh
```
![截屏2023-12-04 10.14.52](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.14.52.png)

### 3.启动区块链浏览器

**再开一个终端**，按以下步骤启动Fabric浏览器（在启动Web项目之后，才能启动区块链浏览器）

```bash
sudo -s
source /root/.bashrc
cd $GOPATH/src/education/explorer
docker-compose -f docker-compose.yaml up -d
```
![截屏2023-12-04 10.26.12](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.26.12.png)

![截屏2023-12-04 10.26.38](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.26.38.png)

使用docker-compose down -v关闭浏览器，Ctrl + C 停止Web服务

## （五）项目结果展示

项目启动成功的结果如图所示：

运行shell脚本，成功后显示界面如下

![截屏2023-12-04 10.38.56](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.38.56.png)

（上图操作是把之前所有的容器和网络全部删除掉）

![截屏2023-12-04 10.43.58](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.43.58.png)

![截屏2023-12-04 10.44.22](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.44.22.png)

![截屏2023-12-04 10.44.46](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.44.46.png)


通过浏览器访问 localhost:9000端口即可进入 web 端，结果如图所示：

![截屏2023-12-04 10.45.48](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.45.48.png)

登陆后，可以进入系统主界面并可以看到相关功能：

![截屏2023-12-04 10.47.12](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.47.12.png)

学历查询范围界面展示

![截屏2023-12-04 10.46.54](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.46.54.png)

学历证书查询界面展示：

![截屏2023-12-04 10.48.29](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.48.29.png)

同时，我们也可以自己添加学历信息：

![截屏2023-12-04 10.51.09](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.51.09.png)

添加完成之后，可以进行搜索，发现新的信息已经成功添加：

![截屏2023-12-04 10.52.08](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.52.08.png)

也可以根据身份证号码查询，如下：

![截屏2023-12-04 22.00.14](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 22.00.14.png)

![截屏2023-12-04 22.00.24](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 22.00.24.png)

![截屏2023-12-04 22.00.38](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 22.00.38.png)



通过浏览器访问localhost:8080即可进入 区块链浏览器，结果如图所示：


CouchDB 数据库1：peer0节点7051端口 `http://localhost:5984/_utils`

CouchDB 数据库2：peer1节点9051端口 `http://localhost:7984/_utils`


![截屏2023-11-12 13.47.16](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-11-12 13.47.16.png)

管理员账号：admin 123456

普通用户账号密码：bob 123456

浏览器账号密码：exploreradmin  exploreradminpw



# 六、报错与解决方案

**（一）go插件无法安装**

在安装go的相关插件时，报错如下：

![截屏2023-09-14 13.35.47](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-14 13.35.47.png)

**解决方案：**由于go插件大部分来自于GitHub，因为网络原因无法下载，因此下载插件只能通过设置GOPROXY代理解决：

```shell
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/
```

同时，执行如下命令：

```shell
git clone https://github.com/josharian/impl
```

 然后切换到GOPATH目录，执行如下命令：

```shell
PS C:\go\repository> go install github.com/josharian/impl@latest
PS C:\go\repository> go install github.com/ramya-rao-a/go-outline@latest
go: downloading github.com/ramya-rao-a/go-outline v0.0.0-20210608161538-9736a4bde949
go: downloading golang.org/x/tools v0.1.1
PS C:\go\repository>
```

 上述命令结束之后，重启VSCode，这下写Go代码就没有问题了。

![截屏2023-09-14 13.47.23](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-14 13.47.23.png)

**（二）Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?**

下载镜像相关依赖包时出错：

![截屏2023-09-29 08.11.19](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-29 08.11.19.png)

**解决方案：**这里是因为apt-get 被锁住了，解决方法为在终端输入如下代码：

```shell
sudo rm /var/lib/dpkg/lock-frontend
sudo rm /var/lib/dpkg/lock
```

之后就可以正常使用apt-get install了：

![截屏2023-09-29 08.24.17](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-29 08.24.17.png)

------

**（三）gpg: no valid OpenPGP data found.**

添加 Docker 的官方 GPG 密钥时，报错如下：

![截屏2023-09-29 08.27.16](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-29 08.27.16.png)

**解决方案：**输入如下命令

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

![截屏2023-09-29 08.28.43](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-09-29 08.28.43.png)

**（四）unable to access 'https://github.com/hyperledger/fabric.git/': GnuTLS recv error (-110): The TLS connection was non-properly terminated.**

在获取Fabric源码的过程中，出现报错：

![截屏2023-10-09 10.17.02](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 10.17.02.png)

**解决方案：**在终端中执行如下命令：

```shell
git config --global --unset http.proxy 
git config --global --unset https.proxy
```

**（五）fatal: not a git repository (or any of the parent directories): .git**

![截屏2023-10-09 10.38.09](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 10.38.09.png)

**解决方案：**

```shell
git init
git status
git checkout -b v1.5.7
```

![截屏2023-10-09 10.40.26](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 10.40.26.png)

**（六）Local fabric binaries and docker images are out of sync. This may cause problems.**

初始化fabric网络时报错：

![截屏2023-10-09 21.01.40](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 21.01.40.png)

**解决方案：**原因是fabric二进制文件和镜像版本不一致，首先查看两者的版本（fabric的最新版本是2.5.0）：

```shell
peer version
docker run --rm hyperledger/fabric-tools:latest peer version | sed -ne 's/ Version: //p' | head -1
```

![截屏2023-10-09 22.35.00](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 22.35.00.png)因此只要我们的镜像版本随本地一致即可：

```shell
docker images | grep -E "(hyperledger)" | awk '{print $3}' | uniq | xargs -I {} docker rmi --force {} #删除fabric镜像
cd /go/src/github.com/hyperledger/fabric/scripts/
sudo vim bootstrap.sh
```

![截屏2023-10-09 22.36.38](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 22.36.38.png)

保存后执行./bootstrap.sh重新运行：

![截屏2023-10-09 22.37.11](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-09 22.37.11.png)

或是直接使用pull命令：

```shell
docker pull hyperledger/fabric-tools:2.5.3
docker tag hyperledger/fabric-tools:2.5.3 hyperledger/fabric-tools #修改标签
```

![截屏2023-10-10 08.17.31](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-10 08.17.31.png)

![截屏2023-10-10 08.17.43](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-10 08.17.43.png)

**（七）fabric-ca-client binary not found..**

在启动测试网络的时候显示找不到fabric-ca-client的二进制文件：

![截屏2023-10-13 20.20.46](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-13 20.20.46.png)

**解决方案：**在上个实验中笔者以为是没有这个文件，就重新进行了拉取，但似乎并不能解决问题。后来发现fabric和fabric-ca中，cmd目录下就有这些二进制文件，但是在fabric-samples中就没有，于是便在fabric- samples中新建bin目录，将那些二进制文件放进去即可

<img src="/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-13 20.20.33.png" alt="截屏2023-10-13 20.20.33" style="zoom:50%;" />

**（八）Cannot run peer because cannot init crypto, specified path "/home/hadoop/go/src/github.com/hyperledger/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp" does not exist or cannot be accessed: stat /home/hadoop/go/src/github.com/hyperledger/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp: no such file or directory**

解决报错一后，不会报找不到二进制文件的错了，但依旧无法启动：

![截屏2023-10-13 20.22.51](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-10-13 20.22.51.png)

解决方案：需要配置`msp`的路径，在终端输入：

```bash
sudo vim ~/.bashrc
```

添加如下路径：

```bash
export CORE_PEER_MSPCONFIGPATH=${PWD}/go/src/github.com/hyperledger/fabric/msp
export FABRIC_CFG_PATH=$PWD/go/src/github.com/hyperledger/fabric-samples/config$
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```

完成后输入：

```bash
source ~/.bashrc
```



**（九）./clean_docker.sh: Permission denied**

安装完Docker之后，使用Docker命令，启动项目，会提示ERROR: permission denied while trying to connect 
出现上述问题，我们进入了root用户，而root没有加入到Docker用户组。


![截屏2023-12-04 10.17.09](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.17.09.png)

**解决方案：**将当前用户加入到用户组

```bash
sudo groupadd docker               #添加用户组
sudo gpasswd -a ${USER} docker    #将当前用户添加至用户组
newgrp docker                      #更新用户组
```

![截屏2023-12-04 10.19.13](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.19.13.png)

然后就可以正常运行了。

**（十）network fixtures_test declared as external, but could not be found**

![截屏2023-12-04 10.30.47](/Users/yangchaoran/Library/Application Support/typora-user-images/截屏2023-12-04 10.30.47.png)

**解决方案：**

按照它的提示，使用docker network create tsg命令，再次执行部署成功。

# 七、实验总结

作为区块链技术这门课程的结课项目，虽然这个项目本身的结构并不是那么完美，有点“自己跟自己玩”的感觉，但这并不代表这是一个坏项目。项目的相关教程和讲解都十分清晰明了，按照文字和视频教程一步步做下来，也算是收益良多。

首先，我们按照教程完成了基于区块链的学历学位认证系统的部署，这是一个利用区块链技术来防止学历造假的项目。该项目旨在解决传统学历认证系统存在的一些问题，例如信息不对称、信任缺失和造假等问题。通过使用区块链技术，该项目可以有效地记录和验证用户的学历信息，提高信息的真实性和可信度。

该项目使用了Go语言和Hyperledger Fabric超级账本框架来实现。Go语言是一种高效、安全和跨平台的编程语言，适合用于开发高性能和分布式应用程序。Hyperledger Fabric是一个企业级的区块链平台，提供了丰富的功能和工具，支持多种共识机制和智能合约语言。

该项目的核心功能包括增加学历、修改学历和查询学历。用户可以通过智能合约向区块链系统中添加自己的学历信息，包括学历类型、毕业院校、学习时间等。用户也可以通过智能合约修改自己在区块链系统中的学历信息，但是修改后的信息会留下痕迹，方便追溯和验证。此外，用户可以通过智能合约查询自己或者其他用户在区块链系统中的学历信息，验证其真伪。

该项目的优点和创新点在于使用了Fabric与Couchdb数据库，实现了分布式数据库与不可篡改性。Fabric是一个有序的、不可篡改的状态转换记录，包括区块链和世界状态两部分。区块链中保存着不可变的顺序记录，包含配置记录和交易记录。世界状态中维护账本的当前状态，方便应用快速查询。Couchdb是一个面向文档的数据库，可以存储复杂结构化数据，并支持富查询功能。使用Fabric和Couchdb可以实现对数据的分布式存储和处理，提高系统的可靠性和可扩展性。

该项目的另一个优点是使用了Hyperledger Explorer，实现了对区块链网络和数据的可视化监控。Explorer是一个基于Web的应用程序，可以展示区块链网络中的节点、通道、交易、合约等信息，并提供图表和统计分析功能。用户可以通过浏览器查看网络中的信息，包括网络拓扑、区块信息、链码信息等等。使用Explorer可以方便地对区块链网络进行管理和监控，提高系统的可用性和可维护性。

同时，该项目也十分具有潜力，能对社会和教育领域产生积极的影响。

在社会层面，首先，通过解决学历造假问题，这个系统可以提高社会信任度。在传统的学历认证系统中，学历造假是一个普遍存在的问题，这给招聘方和用人单位带来了很大的困扰。通过使用区块链技术，所有的学历信息都可以被永久地、不可篡改地记录下来，并且可以被方便地验证。这将大大降低学历造假的可能性，提高整个社会的信任度。其次，这个系统可以提高教育资源的利用效率。在传统的学历认证系统中，学校需要花费大量的人力和物力来颁发和管理学历证书。通过使用区块链技术，学历证书可以被数字化并在区块链上存储，这将大大节省颁发和管理学历证书的成本。同时，这个系统也可以方便地进行跨机构和跨地区的学历认证，这将提高教育资源的利用效率。最后，这个系统可以促进教育公平。在传统的学历认证系统中，一些地区或者弱势群体可能无法获得公平的学历认证机会。通过使用区块链技术，所有的学历信息都可以被永久地、不可篡改地记录下来，并且可以被方便地验证，这将使得所有人都可以获得公平的学历认证机会。

在教育领域层面，学历认证系统同样有着广阔的前景。首先，它可以应用于学历学位证书的管理和验证。通过将学历学位证书保存在区块链上，可以防止证书被篡改或伪造，同时方便用人单位和招聘方验证证书的真实性。这对于招聘方和用人单位来说非常有用，可以有效地避免学历造假等问题。其次，它可以应用于学生的学习成果认证。通过将学生的学习成果保存在区块链上，可以方便地进行跨机构和跨地区的学分认证和转移。这对于学生来说非常有用，可以避免在不同机构和地区之间转学时面临的不必要的学分浪费和重复学习等问题。此外，基于区块链的学历学位认证系统还可以应用于课程资源的共享和交易。通过将课程资源上链，可以方便地进行课程资源的共享和交易，提高教育资源的利用效率。这对于教育机构、教师和学生来说都是非常有价值的，可以促进教育资源的共享和优化。最后，基于区块链的学历学位认证系统还可以应用于教育机构之间的合作。通过区块链技术，可以方便地进行教育机构之间的信息共享和合作，促进教育公平和跨地区、跨机构之间的学术交流与合作。所以，我们很容易发现，基于区块链的学历学位认证系统在教育领域有广泛的应用前景，可以促进教育资源的共享和优化，提高教育公平和教育机构之间的合作。

然而，该项目也遇到了一些困难和挑战。首先，Fabric框架涉及多个组件和配置文件，需要花费较多时间来理解和搭建。项目团队需要熟悉Fabric的架构和原理，正确配置各个组件之间的关系和参数。此外，Couchdb数据库需要创建索引来优化查询性能，并且需要考虑数据同步和一致性问题。项目团队需要熟悉Couchdb的特性和使用方法，合理设计数据库结构和索引，确保数据的准确性和一致性。

另外，Explorer应用程序需要与Fabric网络进行集成，并且需要适配不同版本和配置。项目团队需要熟悉Explorer的接口和功能，正确配置与Fabric网络的连接和认证方式。同时，还需要考虑Explorer应用程序的稳定性和可维护性，确保其能够长期稳定地运行和管理区块链网络。

虽然该项目已经取得了一些成果，但是也存在一些不足和改进空间。首先，学历信息只能由用户自己添加和修改，没有第三方机构来审核和认证。这可能导致一些用户为了获得更高的学历或者修改自己的学历而进行欺诈行为。因此，项目团队可以考虑引入第三方机构或者权威的数据源来审核和认证用户的学历信息。

其次，学历信息没有加密处理，可能存在隐私泄露风险。项目团队可以考虑对用户的学历信息进行加密处理或者使用零知识证明等技术来保护用户的隐私安全。此外，Explorer应用程序没有提供用户认证和权限控制功能，这可能导致一些未经授权的用户访问敏感信息或者进行恶意操作。项目团队可以考虑引入用户认证和权限控制机制来提高系统的安全性。

综上，通过这次实验，我不仅对学历认证系统的优势、潜力、前景和适用领域有了更深层次的认知，我也能够辩证地看待项目中可以继续优化的地方，比如需要引入更多的功能和机制来提高系统的可用性、安全性和可维护性。之后学有余力的话，我十分愿意在这个项目上继续钻研和学习。

当然，我也对区块链相关的技术和知识有了更深的了解，不过这部分我就放在课程总结中，按照知识模块进行更为系统地总结。

# 八、课程总结

## （一）分布式账本技术

分布式账本技术（[DLT](https://so.csdn.net/so/search?q=DLT&spm=1001.2101.3001.7020)）是指允许在一个网络化数据库中同时进行访问、验证和记录更新的技术基础设施和协议。DLT是区块链的创建技术，该基础设施允许用户查看任何变化以及谁做了这些变化，减少了对数据的审计需求，确保了数据的可靠性，并且只向需要它的人提供访问权限。

**关键点**

- 分布式账本由一个节点网络维护，每个节点都有一个账本的副本，验证信息，并帮助达成关于其准确性的共识。
- 分布式账本已经存在了几十年，但自从比特币推出以来，它们变得更加知名、被研究、使用和开发。
- 分布式账本可以用于几乎所有收集和使用数据的行业。
- 所有区块链都是分布式账本，但并不是所有分布式账本都是区块链。
- 尽管DLT增强了问责制、安全性和可访问性，但它仍然复杂、难以扩展，并且没有受到强有力的监管。

**分布式账本的历史**

分布式计算并不新鲜——企业和政府已经使用这个概念几十年了。在20世纪90年代，不同地点的多台计算机和用户能够解决问题并将解决方案返回到一个中心位置。

数据科学、计算、软件、硬件和其他技术的进步使账本变得更加强大。通过内部网和互联网协议提高了连接性，使得更多的数据可以被收集、分析和使用。然而，由于现在有很多用户可以访问数据，因此有必要验证这些变化。

计算机和数据科学家开发了减少对数据审计需求的程序。这些程序使用自动化和数据加密技术来验证[数据库事务](https://so.csdn.net/so/search?q=数据库事务&spm=1001.2101.3001.7020)或数据库状态的变化。这被称为共识——即对交易有效性的自动化多数意见一致的行为，其中交易只是对数据库状态所做的改变。

分布式账本技术（DLT）是指一种利用加密技术来安全准确地存储信息的技术，信息可以通过“密钥”和数字签名来访问。一旦信息被存储，它就可以成为一个不可变的数据库；网络的规则，写入数据库编程的代码中，决定了账本的管理。

如果某件事是不可变的，那么它就无法被改变。分布式账本只有在被编程成这样的时候才是不可变的。区块链总是不可变的，因为它们是去中心化的公共账本。

由于它们是去中心化、私密和加密的，分布式账本更不容易受到网络犯罪的影响，因为所有存储在网络上的副本都需要同时被攻击才能成功。此外，点对点共享和更新记录使整个过程更快、更有效、更便宜。

分布式账本网络上的每个设备都存储了账本的副本。这些设备被称为节点——一个网络可以有任意数量的节点。任何对账本的更改，例如将数据从一个区块移动到另一个区块，都会在所有节点上记录。因为每个节点都有账本的副本，每个节点都会发布其最新交易版本的版本。

如果网络对最新账本的有效性达成共识，那么交易就会被最终确定、加密，并作为后续交易的基础。这就是区块链如何发展的——每个区块包含了前一个区块的加密信息，这使得它们不可能被改变。 行业使用分布式账本技术 分布式账本被创建用于许多不同的目的，但其中最常用的一种方式是作为一个平台，供其他人进行扩展和使用。其中一个较为知名的分布式账本是Hyperledger Fabric。它是一个模块化和可扩展的DLT平台，许多企业已经使用它来创建涵盖多个行业的解决方案。一些实施了DLT解决方案的行业包括航空、教育、医疗、保险、制造、运输和公用事业。 

供应链可以从DLT中受益匪浅。许多因素使它们低效、不准确，并容易受到腐败或损失的影响。富士通（Fujitsu），一家全球数据和信息技术公司，设计了分布式账本技术来增强供应链透明度和防欺诈能力，通过在账本上记录和跟踪数据。

富士通的大米交易所（Rice Exchange）是为了交易大米而创建的，确保有关来源、价格、保险、运输和结算等数据记录在账本上。任何参与者都可以查看任何数据，并找到有关整个过程的准确信息，因为它无法被更改。所有数据都由平台自动输入和保护——它最终将为大米集装箱提供跟踪信息，因为它被运送到最终目的地。

## （二）Go语言

Go语言作为我们刚学习区块链接触的知识，也是大部分实验中的代码语言，只有把go语言学扎实，我们才能够顺利开展后续的实验

**Go语言知识框架**

Go语言，也称为Golang，是由Google开发的一种静态类型、编译型和并发型的编程语言。它在设计时强调简单性和高效性，并具有强大的标准库和跨平台支持，其大概的知识框架为：

1. 基础语法：Go语言的语法相对简单，包括变量、数据类型、控制结构、函数、接口、并发等基础概念。
2. 内存管理：Go语言具有垃圾回收机制，可以自动处理内存分配和回收。
3. 并发编程：Go语言的并发模型是它的一大特色，通过Goroutines和Channels可以实现高效的并发编程。
4. 标准库：Go语言提供了丰富的标准库，涵盖了网络、数据处理、文本处理等方面。

**Go语言的运用**

在区块链领域，Go语言被广泛使用，主要原因在于它的高效性能和并发能力。Go语言在区块链中的应用包括：

1. 区块链核心：许多区块链平台和协议的核心部分是用Go语言编写的，比如以太坊、IPFS等。
2. 智能合约：智能合约是区块链的重要组成部分，Go语言提供了良好的开发环境和高性能的合约执行环境。
3. 钱包开发：区块链钱包是用户与区块链网络进行交互的重要工具，Go语言提供了丰富的库和工具，使得钱包开发变得相对简单。
4. 区块链性能优化：Go语言的并发性能和垃圾回收机制使得在区块链应用中可以更好地进行性能优化。

**与区块链的关系**

在Go语言的学习中，以下几个知识点是至关重要的：

1. Goroutines和Channels：这是Go语言并发编程的核心，是实现高效并发的重要工具。在区块链中，这些特性可以用来处理大量的并发请求，提高网络性能。
2. 内存管理：Go语言的内存管理机制可以帮助开发者避免内存泄漏等问题，这在区块链应用中尤为重要，因为区块链应用通常需要长时间运行且保持高效率。
3. 智能合约开发：Go语言提供了丰富的库和工具来开发智能合约，使得开发者可以更加便捷地在区块链上部署和运行智能合约。
4. 区块链性能优化：通过学习Go语言的内存管理和并发模型，我们可以更好地理解和优化区块链的性能。

**学习过程中的问题与困难**

在学习Go语言的过程中，我遇到了一些问题和困难。首先，Go语言的语法相对简单，但它的某些特性和规则需要时间来适应和理解。例如，Go语言的接口实现是隐式的，这在一开始可能会让人感到困惑。其次，Go语言的并发模型虽然强大，但要正确地使用它并不容易。正确地使用Goroutines和Channels来处理并发问题需要深入的理解和实践。最后，虽然Go语言提供了丰富的标准库，但在某些特定的领域，如区块链开发，可能需要寻找第三方库或者自己编写代码来满足需求。这可能需要花费额外的时间和精力。

**未来学习展望**

在未来，我计划继续深入学习和实践Go语言及其在区块链中的应用。具体来说，我希望能做到以下几点：

1. 进一步深入理解Go语言的内存管理和垃圾回收机制，以更好地优化区块链应用的性能。
2. 提高并发编程能力。通过阅读更多的资料和书籍，以及编写更多的并发程序，我希望能够更深入地理解并掌握Go语言的并发模型。
3. 学习更多的区块链技术和应用，以更深入地理解它们如何与Go语言结合。我希望通过阅读相关论文、研究开源项目等方式来扩展我的知识面。
4. 参与开源项目和实践。通过参与开源项目和实践，我可以提高我的编程能力和问题解决能力，同时也可以学习到其他人的经验和最佳实践。

## （二）Docker

随着区块链技术的普及和发展，Docker作为一种轻量级、可移植的容器化技术，在区块链领域的应用价值日益凸显。Docker是一种开源的容器化技术，它允许开发者将应用程序以及所有依赖项打包到一个可移植的容器中，然后将其部署到任何Docker环境中。其中包含了例如Docker镜像（Image）、Docker容器（Container）、Docker仓库（Repository）和Docker主机（Host）之类基础且十分重要的概念。其工作原理为：使用Linux内核的一些特性，如cgroups（控制组）和namespaces（命名空间），来实现隔离和安全性。

如今，Docker已经建立了一套完整的生态系统，包括ocker Hub、Docker Swarm、Docker Compose等工具，用于镜像管理与容器编排和配置。

在区块链技术中，Docker得到了广泛的运用，包括如下几个主要方面：

1. 区块链应用部署：使用Docker可以将区块链应用及其所有依赖项打包到一个镜像中，然后快速部署到任何支持Docker的环境中。
2. 区块链服务容器化：通过Docker，可以将区块链服务（如矿工、钱包等）封装到单独的容器中，实现服务的快速扩展和管理。
3. 区块链跨平台开发：使用Docker可以简化不同环境之间的差异，使得开发者可以在自己的环境中开发和测试区块链应用。

其中，通过这学期课程的学习，我也学到了与docker相关的一些重要知识点，例如如何使用Dockerfile来构建自定义的区块链应用镜像、熟悉如何启动、停止和管理区块链容器的操作、理解如何配置和管理Docker网络，以支持区块链应用的多节点部署、深入理解Docker和区块链如何在实践中结合，包括如何打包和部署智能合约等方面，并通过相关实验，通过亲身实践进一步加深了对上述知识点的了解。

但同时，我在学习docker的过程中也遇到了一些困难和问题，如编写dockerfile，配置docker网络和docker安全性等问题，这些问题不仅体现在实验中的各种报错，也体现在实验之后自身的疑问，例如如何才能做到真正的“安全”，这些都是或多或少困扰我的问题。

因此，在未来学习过程中，我希望通过如下方面进一步学习docker相关知识：

1. 深入学习Docker的高级特性，如多容器通信、数据管理、高级配置等。
2. 学习更多关于区块链与Docker的最佳实践，包括如何优化性能、保障安全性等。
3. 探索新的Docker技术趋势，如Docker for Mac和Windows的集成，以及容器编排和管理的进一步发展。
4. 通过实践项目，提升自己在区块链和Docker领域的实际操作能力。
5. 关注和研究新的容器安全技术，以满足日益增长的安全需求。

## （三）React18+typescript hooks（前端）函数式写法

在本学期的国庆期间，我用一周的时间学习了react等相关的前端技术，虽然时间不长，但还算是有所收获。

React是一个流行的前端框架，而函数式编程在前端领域的应用越来越广泛。通过学习React和TypeScript的函数式编程，我了解到如何使用这些工具来提高前端的可维护性和可读性。函数式编程强调不变性和纯函数，这使得代码更易于理解和测试，也降低了代码的复杂性。而随着Web技术的发展和区块链领域的快速扩张，React 18与TypeScript Hooks的结合以其强大的功能性和可维护性得到了越来越广泛的应用。

React 18是Facebook推出的一个用于构建用户界面的JavaScript库，而TypeScript则是由Microsoft开发的一种静态类型检查的JavaScript超集。TypeScript Hooks是TypeScript在React 16.8之后引入的新特性，使得在使用React时能够更好地利用TypeScript的类型系统，提高代码的可读性和可维护性。

而在当下，React 18+TypeScript Hooks的运用范围也十分广泛，如在React 18中，函数式组件得到了更广泛的应用。使用函数式组件，可以更简洁地描述UI，并且可以利用TypeScript的类型系统进行更严格的约束。在React 18中，Hooks提供了更方便的方式来组织和复用组件逻辑。通过使用useState、useEffect等Hooks，可以使组件逻辑更加清晰，并且方便地复用状态和副作用处理。而在在区块链应用中，React 18+TypeScript Hooks可以用于构建用户界面，如区块链钱包、交易所等。通过函数式组件和Hooks，可以更方便地处理UI和业务逻辑，提高应用的可维护性和可扩展性。在实际运用中，React 18+TypeScript Hooks可以用于构建区块链应用的用户界面。例如，一个区块链钱包应用需要处理复杂的交易逻辑和安全性问题，使用函数式组件和Hooks可以更方便地处理UI和业务逻辑，提高应用的安全性和可维护性。

在这一周中，我学到的最重要的知识点便是函数式组件和Hooks的使用与类型安全性和可维护性。对于函数式组件，这是React 18+TypeScript Hooks的核心知识点，对于构建高效的区块链应用至关重要。通过函数式组件和Hooks，可以更简洁地描述UI和业务逻辑，提高代码的可读性和可维护性。而TypeScript的引入使得代码具有更强的类型安全性，可以帮助开发者在编译时发现潜在的错误。同时，通过使用TypeScript的类型系统，可以提高代码的可维护性，使开发者能够更容易地理解和修改代码。但是就我个人而言我觉得函数式组件与传统的class组件相比，具有更简洁的语法和更强的类型安全性。然而，刚接触函数式组件时，可能会感到有些概念和惯用的class组件有所不同，需要花费一些时间来适应。且TypeScript的类型系统比JavaScript更为复杂，需要花费一些时间来学习和掌握。然而，通过学习TypeScript的类型系统，可以更好地保证代码的类型安全性和可维护性。

因此，作为这些知识模块中我最感兴趣的技术之一，我想通过进一步学习函数式编程的思想和方法论，可以更好地理解和运用函数式组件和Hooks等函数式写法。同时，了解更多的函数式编程库和工具，如Ramda、lodash等，提高自己的技能。

## （四）Git

在当今的软件开发世界中，版本控制系统如Git的使用已经变得至关重要，而在这学期区块链的学习中，我也通过实际运用感受到了git的魅力。

Git是一种分布式版本控制系统，由Linus Torvalds于2005年为Linux内核开发。它被设计成快速、高效且适用于大规模项目，在这学期，我主要用它来存储相关项目的代码，在一次次的操作中，我对git的基本概念如仓库、分支、合并、提交等概念有了一个大致的了解，同时对git的操作流程如初始化仓库、添加文件、提交更改、创建分支等有了一定的熟悉（毕竟熟能生巧）

同时，我还对git进行了更进一步的了解，我发现git的运用十分广泛，如轻松地跟踪和管理代码的版本，从初版到后续的每个修改、创建、切换和合并分支（这对于处理并发开发和支持多个版本非常有用）、得代码审查变得简单和高效，因为它可以清晰地显示代码更改的历史记录，以及最重要的一点，他能安全地存储代码，即使在发生硬件故障或用户错误的情况下，也可以通过回滚到之前的版本找回数据。

当然，万事开头难，我一开始使用的时候也是到处撞墙，上传个文件都传不明白，这也十分正常，我想Git命令行工具的多样性和灵活性使得初学者可能会感到困惑。理解和记忆每个命令及其选项是一项挑战。而且要是碰着一个大型项目，估计我又搞不定了，只能说慢慢熟练吧。

总而言之，git的使用无疑会大大方便我后续做各种项目，因为我不用害怕在电脑里的文件因为意外而丢失了，很好的工具，使我效率如闪电般迅捷，爱来自中国。

## （五）Hyperledger Fabric

要说这学期给我留下印象最深的知识，那就是Hyperledger Fabric了

Hyperledger Fabric框架之所以给我留下了如此深刻的印象，是因为它作为一个企业级区块链解决方案，注重隐私保护和可扩展性，适用于各种行业场景。通过学习，我了解到Fabric采用了模块化的架构设计，可以灵活地满足不同的业务需求。它以链码为核心，通过智能合约来执行各种业务逻辑，保障了数据的安全性和一致性。

在实际运用方面，Hyperledger Fabric具备广泛的应用场景。它可以应用于金融、供应链、物联网等领域，实现数据的共享和交换。例如，在金融领域，Fabric可以用于构建跨机构的金融网络，实现资产的数字化和交易的自动化。在供应链领域，它可以用于建立透明的供应链管理系统，确保产品的追溯和防伪。这些应用案例让我深刻体会到了区块链技术的潜力和价值。

如果要说我学到的最重要的知识点，那就是Hyperledger Fabric的隐私保护机制。通过引入多通道和私有数据集合的特性，Fabric可以在保障数据隐私的同时实现数据的共享和验证。这一机制有效地解决了区块链技术中的隐私泄露问题，使得更多企业愿意采用区块链技术来改进业务流程。

在学习过程中，我也遇到了一些困难和挑战。首先是概念的理解和运用。刚开始接触Hyperledger Fabric时，我对一些专业术语和概念感到陌生，需要花费更多的时间和精力去理解和运用，而且有一周的实验也是让我没日没夜地坐在电脑面前解决报错，可谓十分痛苦。其次是实际操作的复杂性。Fabric的配置和部署相对复杂，需要掌握一定的命令行和配置文件操作技巧。这些困难让我意识到学习区块链技术需要耐心和实践。

展望未来，我对Hyperledger Fabric的知识学习充满期待。随着技术的不断发展和应用场景的不断拓展，我相信Fabric会在更多领域得到广泛应用和推广。我希望能够继续深入学习Fabric的核心原理和技术细节，掌握更多高级特性和工具的使用方法。同时，我也期待能够参与实际项目，将所学知识运用到实践中，为企业解决实际问题（如果有企业愿意要我的话）

## （六）Hyperledger Fabric Explorer

由于我们课程后期的实验需要观察整个区块链上的块数、节点数、通道、链码等等，因此当时我们花了一周的时间，将部署Hyperledger Fabric Explorer作为一个独立的实验来完成，我也花了一些时间私下了解了一下这个浏览器。

首先，Hyperledger Fabric Explorer是Hyperledger Fabric网络中的一种浏览器，可以帮助开发者和管理员浏览和管理Hyperledger Fabric网络。它的框架给我留下了深刻的印象。它是一个基于Web的应用程序，专为Hyperledger Fabric设计，用于查看、查询、管理链上的网络、节点、链码和数据。通过学习，我了解到Fabric Explorer的前端是用React.js构建的，后端则是用Node.js实现，与Fabric网络进行交互。

在实际运用方面，Hyperledger Fabric Explorer的使用场景相当广泛。在开发环境中，开发者可以利用Explorer进行链码的部署、查询和测试。在生产环境中，管理员可以通过Explorer监控网络状态、管理节点和链码。此外，业务用户还可以利用Explorer查看交易历史和数据统计。

如果要说我学到的最重要的知识点，那就是如何通过Hyperledger Fabric Explorer与Fabric网络进行交互。Explorer提供了一个直观的用户界面，使用户可以轻松地查询交易数据、查看网络配置和监控节点状态。通过Explorer，我深入了解了Fabric网络的运行机制和链码的执行流程。

在学习过程中，我也遇到了一些问题和困难。首先是配置环境的复杂性。为了运行Hyperledger Fabric Explorer，我需要配置多个组件和依赖项，包括Fabric网络、数据库和Explorer本身。这一过程相对繁琐，需要花费大量时间和精力。其次是理解链码和交易的过程。对于初学者来说，理解链码的执行逻辑和交易的生命周期可能有一定的难度。

当然，我对Hyperledger Fabric Explorer的学习充满期待。随着区块链技术的不断发展和应用场景的不断拓展，我相信Fabric Explorer会在更多领域得到广泛应用和推广，能运用到这些相关知识的场合肯定会越来越多（虽然我现在暂时是用不太到了）。我希望能够继续深入学习Explorer的高级特性和最佳实践，掌握更多关于性能优化和安全性的知识。

## （七）Hyperledger Fabric Gateway

Hyperledger Fabric Gateway就是Fabric网络和应用程序之间的一个“桥梁”。通过这个Gateway，应用程序可以更加安全、高效地与Fabric网络进行交互，执行各种操作，比如查询账本、提交交易等。它的设计也很灵活，可以根据不同的业务需求进行定制。

所以，显而易见，他能广泛运用于许多场景。举个例子，在一个供应链金融的应用中，银行和其他金融机构可以通过Gateway与供应链网络进行交互，实时查询交易状态、监控风险，甚至自动执行某些预设的业务逻辑，都离不开Gateway。

我觉得通过对Hyperledger Fabric Gateway，我学到的最重要的一点是Gateway如何保障数据的安全性和隐私性。通过身份认证、加密通信和访问控制等手段，Gateway确保了只有经过授权的应用程序才能与网络进行交互，而且所有的数据交互都是加密和安全的。这一点在区块链应用中至关重要，也是Hyperledger Fabric Gateway的一大亮点。

当然，学习的过程也不是一帆风顺，Gateway的配置和部署还是有点难度的，为了设置一个可用的Gateway，我需要配置多个组件和参数，包括证书、连接配置、链码等。这一过程需要一定的经验和技巧，对于初学者来说可能有一定的难度。其次是理解和运用链码的过程。链码是Fabric网络中的业务逻辑，而Gateway则是与这些链码进行交互的接口。因此，如何正确地编写和调用链码也是我在学习过程中遇到的一个挑战。

所以，如果想要加深自己的理解，提升自己的熟练程度，我还是希望在未来能够参加一些相关的项目将所学知识运用到实践中，解决实际问题，不然在这就止步的话，估计我下学期就啥也记不得了吧。

## （八）Gin

Gin是一个用于编写智能合约的Go语言框架。通过使用Gin，我们可以轻松地创建智能合约，并实现复杂的业务逻辑。此外，Gin还提供了许多有用的工具和库，例如Ledger和Crypto，这些工具和库可以帮助我们更好地处理账本数据。其核心组件包括路由器（负责处理HTTP请求，将请求路由到相应的处理函数或中间件）、中间件（用于处理请求的中间件，可以执行身份验证、授权、日志记录等操作）、模板引擎（用于渲染HTML页面和API响应）、ORM（提供了一种简单的方法来访问数据库，支持MySQL、PostgreSQL等常见数据库）和事件系统（用于在应用程序中触发和处理事件）等，使得gin可以用于构建各种基于区块链的应用程序，包括去中心化交易所、数字身份认证系统、供应链管理等。通过使用gin框架，开发者可以更加快速地构建出高效、安全、可扩展的应用程序。

Gin对中间件和事件系统的运用还是比较重要的一个知识点，中间件是gin中非常强大的一个功能，它可以对请求进行各种处理，例如身份验证、授权等。事件系统则可以让开发者在应用程序中触发和处理各种事件，例如在交易完成后发送通知等。这些功能使得开发者可以更加灵活地构建出各种复杂的应用程序。

但是学习gin的过程也让我十分头大，首先就是有很多的相关概念，我光是理解gin的框架就花了我好长时间。再比如，之前在做每周的实验时，我在使用Gin编写智能合约，但是在处理账本数据时无法正确读取和写入数据。经过仔细检查代码和文档，我发现原来是我对Gin的API理解不够深入。而且我发现关于gin的相关实例代码比较少，很难让我找到相关参考。总而言之，当时的报告里可能并没有几个报错，但是并不代表整个试验就像报告过程一样一帆风顺。但能从问题中学到点东西，也算是件好事吧。

展望未来的话就不再写了，参加项目之类的话先前也写了好几次，总之，就继续学，继续实践吧。

# 九、致谢

时间还是过的很快啊，一学期又要结束了，这也是周中定老师教我们的第三门课程了。

一开始听到区块链这个名字，我就想着可能是一个很高级很抽象的东西，事实也确实如此，虽然因为已经用了好长一段时间的Ubuntu，所以一些低级的，系统性的报错少了特别多，系统版本方面的问题我也是平常就有所注意，因此实验做起来是顺利了一些，但是碰到的错误却比之前学大数据时遇到的棘手了很多，有时候我就一天坐在电脑前，对着屏幕前的一个报错苦苦找不到答案，有时实验也会被一个错误卡住好几天都不能往下进行，这些记忆我到现在回想起来都痛苦不堪。

在我看来，区块链简单来说就是一个分布式数据库，每个节点都有完整的账本副本，并且使用加密算法保证数据的安全性和不可篡改性。区块链的核心思想是去中心化，通过去信任的方式来实现数据的共享和传递，从而解决了传统中心化机构存在的信任问题。因为这种特性，他能在很多领域得到使用，比如，在金融领域，区块链可以用于构建去中心化交易所、实现跨境支付等；在供应链管理领域，区块链可以追溯商品的来源和流向，保证商品的真实性和质量；在物联网领域，区块链可以确保设备之间的安全通信和数据共享。可以说，区块链正在逐渐渗透到我们生活的方方面面。（但是在上这门课之前，我确实啥都不知道，因为我之前完全没有了解过区块链）

要说我学到了什么，我印象最深刻的便是去中心化思想，它贯穿了整个区块链技术，我相信这是区块链能够解决信任问题的关键所在；其次是加密算法的运用。加密算法保证了数据的安全性和不可篡改性，是区块链技术能够实现数据共享和传递的基础。此外，智能合约也是一个非常重要的知识点。智能合约是一种自动执行的计算机程序，可以在满足预设条件的情况下自动执行合约内容，从而实现了去信任的交易。

肯定还是有很多不足的啦，但我还可以继续学，这一个学期下来我感觉我更有耐心了，所以碰到一些棘手的问题我还是能静下心来想办法去解决的。一些套话凑字数的话我也不再写了，也不晓得这是不是老师教我们的最后一门课，希望以后还能继续带我们上课吧

最后还是在我的每份期末报告里都有写的那句话，跟您总是能学到东西，祝您工作顺利，身体健康，生活愉快。

