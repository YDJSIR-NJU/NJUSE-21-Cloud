# Hadoop

## 概论

> - 面向数据存储和加工的平台
> - 定义好编程模型和接口
> - 用户编写Application，提交到平台：服务App vs. 运算App
> - 开源，面向数据分析
> - 不一定要在云上：分布式编程模型

### 问题

#### 存储

- 单机容量足够，各机器上的文件要求对外显示它们存于同一硬盘空间；

- 文件大小超出单机容量，要求利用多台机器存入后对外显示依旧为一个完整文件；

#### 计算

> 比如说数单词

#### 可靠性

应对宕机

> 网络开销、利用率、成本

### 分布式解决方案

#### 冗余存储与冗余计算

- 解决可靠性问题——不单纯靠额外增加设备的备份

- 将每台机器上存储的数据同时存于集群中的另一台机器上

- 将每台机器上数据的计算也同时在冗余数据的机器上计算

- `CMaster0`明确知道每一份数据都存储在多个地方

- `CMaster1`会要求存有待计算数据的机器都参与计算，并选择先结束的机器计算结果

冗余存储不仅提高了分布式存储的可靠性，也提高了分布式计算的可靠性。

> 分布式存储和分布式计算可以相互独立存在

#### 分布式存储（Distributed File System, DFS）

将多台机器硬盘以某种方式连接到一起

取机器`cSlave0`，`cSlave1`……和`cMaster0`，采用客户-服务器模式构建分布式存储集群。

让`cMaster0`管理`cSlave0`，`cSlave1`……。

##### 对内：客户-服务器模式

只要保证store master正常工作，我们很容易随意

添加store slave，硬盘存储空间无限大。

##### 对外：统一存储空间，统一文件接口

整个集群就像是一台机器、一片云，硬盘显示为

统一存储空间，文件接口统一。

#### 分布式计算

> “移动计算比移动数据更划算”——Google论文

##### 计算的并行-`Map`

![image-20211015092645930](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015092645930.png)

##### 合并的并行-`Reduce`

![image-20211015092905250](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015092905250.png)

> 经过洗牌之后，合并的时候分布的文本某一个特定单词的计数都送到某一个节点上进行合并，不同单词之间的合并技术可以并行

### Hadoop简介

#### 渊源

- Apache成立开源搜索引擎项目`Nutch`——但开发过程中无法有效地将计算任务分配到多台机器上

- 前后Google陆续发表`GFS`、`MapReduce`、`BigTable`（谷歌三板斧）

- Apache借鉴GFS和MapReduce，实现了自己的`NDFS`和`MapReduce`

- 发现`Nutch`侧重搜索，而`NDFS`和`MapReduce`偏向通用基础架构，将`NDFS`和`MapReduce`移出`Nutch`，成为独立开发项目，称为`Hadoop`

Hadoop 1.0 （1.X的统称）和Hadoop 2.0 （2.X的统称）架构差异较大。

#### 简介

> 可看作是Google Cloud的开源版本；但并不拘泥于复现Google Cloud的相关产品。
>
> | **Hadoop**        | Google            | 描述           |
> | ----------------- | ----------------- | -------------- |
> | Hadoop  HDFS      | Google  GFS       | 分布式文件系统 |
> | Hadoop  MapReduce | Google  MapReduce | 分布式计算     |
> | HBase             | Google  BigTable  | 分布式数据库   |
> | ZooKeeper         | Google Chubby     | 消息队列       |
> | Pig               | Google  Sawzall   | 脚本语言       |

通过调用程序库，可使用简单的编程模型处理分布在不同机器上的大规模数据。

采用客户-服务器模式，很容易从一台机器扩展至成千上万台机器，每台机器均能提供本地存储和本地计算。

##### Hadoop 1.0

- `Hadoop Common`：支持其他两个模块的公用组件

- `Hadoop DFS`（`HDFS`）：分布式文件系统

- `Hadoop MapReduce`：分布式计算框架

##### Hadoop 2.0

![image-20211015170805021](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015170805021.png)

- 分布式操作系统`Yarn`

- `ZooKeeper`：通用的分布式集群的数据管理者

  不仅仅只是为Hadoop服务！集群化思想

  - 统一命名服务
  - 配置管理
  - 集群管理

- `Hbase`：开源分布式数据库

  - 高可靠性

  - 高性能

  - 列存储

  - 可伸缩

  - 实时读写

  **逻辑模型**：用户对数据的组织形式

  `行键、列（<列族>:<限定符>）、时间戳；字节码，无类型`

  **物理模型**：在设备上具体存储形式

  将行按照列族分割存储；逻辑空值无存储

#### 应用领域

##### 构建大型分布式集群（存储+计算）

- 最直接的应用

- 构建大型分布式集群，提供海量存储和计算服务

- 类似产品中国移动“大云”、淘宝“云梯”

##### 数据仓库（存储）

存储半结构化业务数据，通过`Hive`、`Hbase`提供报表查询之类服务

##### 数据挖掘（计算）

- 大数据环境下的数据挖掘思路和算法没有太大变化

- 硬盘性能和内存大小带来的限制——通过分布式集群解决硬件限制

#### 部署方式

传统解压包（繁琐易错）和标准Linux部署方式（简单易用）

> 这里会有一个专门的作业，

![image-20211015095217686](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015095217686.png)

> 注意下面均以Hadoop 2.0为基础

## 体系架构

### Common

其他模块的公共组件，定义程序员取得集群服务的编程接口，为其他模块提供共用API。

#### 作用

降低Hadoop设计的复杂性，减少其他模块间耦合性，增强Hadoop健壮性。

#### 功能

- 提供公用API和程序员编程接口（例如Configuration类）；
- 本地Hadoop库（例如压缩解压缩用的是Hadoop本地库）；
- 超级用户superuser；
- 服务级别认证；
- HTTP认证；

### HDFS

高容错、高扩展、高可靠，并提供服务访问接口，如API接口和管理员接口。

#### 体系架构

- 架构：`master`/`slave`；文件分块存储；`namenode`/`datanode`；
- 典型拓扑：一般/商用（`ZooKeeper`选举`ActiveNamenode`，`JourNalNode`两个`Namenode`交换数据）

![image-20211015153417530](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015153417530.png)

#### 内部特性

- 冗余备份、副本存放、副本选择、心跳检测

- 数据完整性检测、元数据磁盘失效

- 简单一致性模型、流式数据访问、客户端缓存

- 流水线复制、架构特征、超大规模数据集

#### 对外功能

- `Namenode`==高可靠性==：配置多个`NameNode`，一个失效时立即替换
- `HDFS`快照：当数据损坏时，支持回滚到正确的时间节点
- 元数据管理与恢复工具：通过命令`hdfs oiv`和`hdfs oev`管理修复`fsimage`和`edits`
- `HDFS`安全性：用户和文件级别安全认证、机器和服务级别安全认证
- `HDFS`配额功能：管理目录或文件配额大小
- `HDFS` C语言接口：使用C语言操作HDFS的接口
- `HDFS Short-Circuit`功能：客户端可以绕开`Datanode`直接读取本机数据，加快`Map`操作
- `WebHdfs`：==通过`Web`方式操作`HDFS`==（插、删、改、查）

### Yarn

管理计算机资源、提供用户和程序访问系统资源的API。

- 一个高层的集群管理框架；

- 根据需要的计算类型，定制`ApplicationMaster`；
- 根据需要的调度策略，扩展`Scheduler`

#### 架构

![image-20211015154235691](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015154235691.png)

#### 组件详解

1. `Client`：客户端，负责向集群提交作业。

2. `ResourceManager`：集群主进程，仲裁中心，负责集群资源管理和任务调度。

3. `Scheduler`：资源仲裁模块。==纯粹的资源仲裁中心==

4. `ApplicationManager`：选定，启动和==只==监管`ApplicationMaster`。

5. `NodeManager`：集群从进程，管理监视`Containers`，执行具体任务。

6. `Container`：==本机==资源集合体，如某`Container`为4个CPU，8GB内存。

7. `ApplicationMaster`：任务执行和监管中心。==负责任务整体执行==

#### 作业流程

1. 提交作业
2. 任务分配
3. 任务执行
4. 进度和状态更新
5. 任务完成

![image-20211015154901595](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015154901595.png)

#### 典型拓扑

![image-20211015155031593](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015155031593.png)

#### 核心问题：调度策略

`ResourceManager`的`Scheduler`模块支持插拔，通过配置文件，用户可以个性化指定其调度策略。

##### 自带策略1：容量调度算法`CapacityScheduler`

- 多用户多任务调度策略；
- 以队列为单位划分任务，以Container为单位分配资源；
- 按照配置好的资源配比为不同层级的用户分配最大可用资源；

##### 自带策略2：公平调度算法`FairScheduler`

- 多任务公平使用集群资源的可插拔式调度策略；
- 当资源能够满足所有任务时，则按需分配资源；
- 当资源受限时，会将正在执行的任务释放的资源分配给在等待资源的任务；
- 短任务在合理时间内完成；长任务不至于永远等待；

### MapReduce

编程范式。

`Yarn`中`ApplicationMaster`用来管理任务的执行，其能够管理的任务类型是固定的

通过定义不同类型的`ApplicationMaster`，可以实现管理不同类型的任务。可以将`MapReduce`看作一种类型的计算任务。提供对应的`ApplicationMaster`来管理`MapReduce`任务。

==`ApplicationMaster`和`Scheduler`都是可变的==。

![image-20211015164310161](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015164310161.png)

### 拓展

- 在Hadoop框架内定义新的计算任务——编程模板
- 跳出Hadoop的限制——==Spark==——定义更加通用的编程
- 安全机制——不同级别、不同场景的安全认证

## 访问接口

### Web

| 服务        | Web地址                                 | 配置文件          | 配置参数                                  |
| ----------- | --------------------------------------- | ----------------- | ----------------------------------------- |
| `HDFS`      | `http://<NameNodeHostName>:50070`       | `hdfs-site.xml`   | `{dfs.namenode.http-address}`             |
| `Yarn`      | `http://<ResourceManagerHostName>:8088` | `yarn-site.xml`   | `{yarn.resourcemanager.webapp.address}  ` |
| `MapReduce` | `http://<JobHistoryHostName>:19888`     | `mapred-site.xml` | `{mapreduce.jobhistory.webapp.address}`   |

### 命令行

#### HDFS命令

- 以tar包方式部署时，其执行方式是`$HADOOP_HOME/bin/hdfs`
- 以完全模式部署时，使用HDFS用户执行`hdfs`即可

#### Yarn命令

- 以tar包方式部署时，其执行方式是`$HADOOP_HOME/bin/yarn`
- 以完全模式部署时，使用`Yarn`用户执行`yarn`即可

#### Hadoop命令

- 两种部署方式下分别为`$HADOOP_HOME/bin/Hadoop` 和 `hadoop`

#### 其他

- `sbin/`目录下的脚本

  ![image-20211015165528143](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015165528143.png)

- 启停服务/管理服务

### 开发接口

#### HDFS编程

- 实例化配置文件 `Configuration conf = new Configuration()`；
- 获取文件系统 `FileSystem hdfs = FileSystem.get(conf)`；
- 使用`hdfs`对象执行文件操作；

> https://hadoop.apache.org/docs/r2.6.4/api/org/apache/hadoop/conf/Configuration.html
>
> 通过configuration传参：`conf.set(key, value)`，`context.getConfiguration().getInt(key)`

#### Yarn编程

- 一套编程协议；
- `Client`负责提交任务，`ApplicationMaster`负责执行任务；
- Client中与RM通信；`ApplicationMaster`与RM通信；`ApplicationMaster`与NM通信
- 编写符合协议的`Client`和`ApplicationMaster`即可

#### 只需考虑MapReduce本身

`Hadoop`默认实现了`MapReduce`的`Client`和`ApplicationMaster`、`MRClientService`和`MRAppMaster`等。

`Yarn`处理MR程序时使用了各种默认的类。

# Spark

## 概论

### 简介

当今大数据领域最活跃、最热门的大数据计算处理框架

- 2009年——诞生于美国加州大学伯克利分校AMP实验室

- 2013年——Spark成为`Apache`基金项目

- 2014年——成为`Apache`基金顶级项目

一体化、多元化的大数据处理体系

- 批处理，`Batch Processing`

- 流处理，`Stream Processing`

- 即席查询，`Adhoc Query`

#### 开发包

`Spark SQL`、`Spark Streaming`、`Spark Mllib`、`Spark GraphX`……

![image-20211015173939819](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015173939819.png)

##### `Spark SQL`

- 前身是Shark：基于Hive的Spark SQL，代码量大、复杂，难优化和维护

- 交互式查询、标准访问接口、兼容Hive

- 专门用于处理结构化数据：

- 分布式SQL引擎；在Spark程序中调用API

#### `Spark Streaming`

- 实时对大量数据进行快速处理，处理周期短

- 对数据分段、定义了自动监听更新的框架、提供各种Spark计算函数

##### `Spark GraphX`

- 以图为基础数据结构的算法实现和相关应用

- 使用RDD存储图节点和边信息，提供各种计算函数

##### `Spark MLlib`

- 为解决机器学习开发的库

包括`分类`、`回归`、`聚类`和`协同过滤`等。

> 插曲，`Spark Mllib`的效率被基于`MPI`（`Message Passing Interface`）的机器学习吊打
>
> 可能的解释：https://zhuanlan.zhihu.com/p/81784947

`Spark`==专注于数据的计算==，而==数据的存储==在生产环境中往往还是由Hadoop分布式文件系统==HDFS==承担。

### 优势

#### 对比Hadoop

- 支持多种数据计算需求
  - 流式迭代的类MR计算
  - 图数据结构的计算

- 基于内存的计算范式，不用像`Hadoop`那样需要不停地写入硬盘（落盘）

> `Spark`曾经是一个`Hadoop`应用程序，但是`Spark`并不一定要依赖于`Hadoop`

#### 对比MapReduce

`Spark`是在`Hadoop`开创的分布式计算框架下对`MapReduce`编程范式进行扩展的一种更加通用的并行计算框架。

- 独立性更强

- 基于内存：RDD，速度更快；基于内存的计算快100x倍，基于硬盘的快10x倍

- 支持更多数据计算方法：`transformation`，`action`等

#### 整体优势

##### **易用**

  支持`Scala`、`Java`、`Python`和`R`等多种编程语言

##### **强大**

  支持`SQL`、`Streaming`、`Graph`和`Machine Learning`等多种应用场景

##### **通用**

  适用于自带的`Standalone`、`Mesos`、`Yarn`等多种不同分布式集群管理框架；

  适用多种不同数据存储方式（数据读取接口丰富）

### 部署模式

#### Local模式

`Local`是方便初学者入门学习和测试用的部署模式

#### Cluster模式

Cluster是真正的集群模式

- `Standalone`集群管理器
- `Yarn`集群管理器
- `Mesos`集群管理器

### 提交模式

`Client`模式和`Cluster`模式

`Local`部署模式只支持`Client`提交模式。

真正的`Cluster`集群部署模式同时支持`Client`和`Cluster`提交模式。

#### Client提交模式

在`worker`节点启动`Driver`程序运行应用程序，结果返回到`Client`端。

#### Cluster提交模式

在`Master`上启动`Driver`程序，结果不会返回给`Client`而是保存在`Master`上。

## 内核机制解析

![image-20211015193231739](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015193231739.png)

### `RDD`（`Resilient Distributed Datasets`）

Spark是建立在RDD(Resilient Distributed Datasets，弹性分布式数据集)之上的。

RDD是一个==容错的==、==并行的==逻辑数据结构，使得Spark可以用一致的方式处理大数据的不同应用场景。

#### 性质

RDD是高度受限的共享内存模型。

- RDD只能从外界接入或由其他RDD产生。
- 从父RDD到子RDD的过程可以构建一棵树（有向无环图，`Directed Acyclic Graph（DAG）`）。若子RDD出现问题，可以通过父RDD重新推演。

#### 特点

- 数据存储到内存和磁盘中

- 控制数据分区

- 丰富的API操作数据

#### 五大特性

- 分区列表：记录了数据块所在的分区位置；一个RDD对应的数据是切分为数据块存储在集群的不同节点上的。

- 依赖列表：记录了当前这个RDD依赖于哪些其它的RDD。

- 计算函数：用于计算RDD各个分区的值。

- **可选**：分区器，子类可以重新指定新的分区方式：Hash和Range。
- **可选**：计算各分区时优先的位置列表。例如从HDFS文件生成RDD时，HDFS文件所在位置就是对应生成的RDD分区所在位置的优先选择。

#### 两种算子

##### Transformation

| **Transformation操作**           | **说明**                                                     |
| -------------------------------- | ------------------------------------------------------------ |
| `map(func)`                      | 对源`RDD`中的每个元素调用`func`，生产新的元素，这些元素构成新的RDD并返回 |
| `flatMap(func)`                  | 与`map`类似，但是`func`的返回是多个成员                      |
| `filter(func)`                   | 对`RDD`成员进行过滤，成员调用`func`返回`True`的才保留，保留的构成新`RDD`返回 |
| `mapPartitions(func)`            | 和`map`类似，但是`func`作用于整个分区，类型是`Iterator<T>  => Iterator<T>` |
| `mapPartitionsWithIndex(func)`   | …                                                            |
| `union(otherDataset)`            | …                                                            |
| `reduceByKey(func, [numTasks])`  | 对`<key,  value>`结构的`RDD`聚合，相同`key`的`value`调用`reduce`，`func`是`(v,v)=>v` |
| `join(otherDataset, [numTasks])` | …                                                            |

##### Action

| **Action操作**             | **说明**                                                     |
| -------------------------- | ------------------------------------------------------------ |
| `reduce(func)`             | 对RDD成员使用`func`进行`reduce`，`func`接收两个值返回一个值；`reduce`只有一个返回值。`func`会并发执行 |
| `collect()`                | 将`RDD`读取到`Driver`程序里面，类型是`Array`，要求`RDD`不能太大 |
| `count()`                  | 返回`RDD`成员数量                                            |
| `first()`                  | 返回`RDD`第一个成员，等价于`take(1)`                         |
| `take(n)`                  | …                                                            |
| `saveAsTextFile(path)`     | 把`RDD`转换成文本内容并保存到指定的`path`路径下，可能有多个文件；`path`可以是本地文件系统路径，也可以是`HDFS`路径，转换方法是`RDD`成员的`toString`方法 |
| `saveAsSequenceFile(path)` | …                                                            |
| `foreach(func)`            | 对`RDD`的每个成员执行`func`方法，没有返回值                  |

#### 弹性的七个方面

##### 自动进行内存和磁盘存储的切换 `效率考虑`

优先内存，实在放不下则放到磁盘里。

##### 基于Lineage（血统）的高效容错机制 ==`效率考虑` `容错考虑`==

记录每一个数据分片的计算来源，便于快速恢复。

##### Task如果失败会自动进行特定次数的重试 ==`容错考虑`==

`TaskScheduler`获取一个`Stage`的`TaskSet`，运行它们；默认4次。

##### Stage如果失败会自动进行特定次数的重试 ==`容错考虑`==

`DAGScheduler`调度`Stage`，`Stage`跟踪执行情况；默认4次。

##### `Checkpoint`和`Persist`（检查点和持久化）可主动或被动触发 ==`效率考虑` `容错考虑`==

用户能见的`RDD`可以==主动调用==，自动产生的中间RDD则可==配置==。

##### 数据调度弹性（`DAGScheduler`、`TaskScheduler`和资源管理无关）==`效率考虑`==

任务调度、数据分配和计算资源的管理解耦：Yarn管理Spark集群

##### 数据分片的高度弹性：合并分片和切分分片 ==`效率考虑`==

根据产生的分片（数据块）大小自动选择继续切分还是放到磁盘

#### 创建方式

- 通过已存在的数据集合创建（变量）
- 通过HDFS和本地文件系统创建`textFile(…)`
- 通过其他RDD转换（`transformation`操作）
- 从数据库读入

#### 宽窄依赖

![image-20211015204733278](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015204733278.png)

|                 窄依赖（`NarrowDependency`）                 |                宽依赖（`ShuffleDependency`）                 |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ==父RDD==中的Partition==最多==被==子RDD==的==1个==Partition所使用 | ==一个====父RDD==的Partition会同时被==子RDD==的==多个==Partition所使用 |
|                       ==完全==并行执行                       | 需要==Shuffle后==才能对计算==并行化==；==Shuffle过程不能完全并行== |
| ![image-20211015205511434](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015205511434.png) | ![image-20211015205517567](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015205517567.png) |

#### 运算流程

![image-20211015213320153](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015213320153.png)

![image-20211015205800617](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015205800617.png)

构造DAG，划分`Job`、`Stage`、`Task`，==遇到`Action`，才会提交`Job`==。

![image-20211015210017829](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211015210017829.png)

- `Transformation`：从持久化存储中通过*变换*（**Transformations**，如 *map* 或者 *filter*）将其载入内存，然后可以对 RDD 施加任何系统支持的一系列变换。它只是一个声明，不碰到Action运算不会进行。

- `Action`：将 RDD 重新持久化到外存中或者将控制权交还用户。
- `Application`：客户端的一次提交，可以看作一个主函数。
- `Job`：由action触发的一系列计算任务。
- `Stage`：把一个`job`按照宽依赖分割成若干阶段。
- `Task`：把`stage`根据RDD分区数进行区分。

> 参考链接：https://stackoverflow.com/questions/42263270/what-is-the-concept-of-application-job-stage-and-task-in-spark

#### 解决的具体问题

`Hadoop`不能基于内存共享数据，==反复读写磁盘==。因此，`Hadoop`的`MapReduce`对==迭代==式算法支持的效率不高，更别提图算法和机器学习算法了。

`Spark`适应于在交互式数据挖掘工具中反复查询一个数据子集的应用场景。

#### 缓存（Cache）

将RDD保存到内存（也可能在磁盘上），读写速度极快。

实现方式是在`Persist`持久化时将`Storgelevel`设置为`MEMORY_ONLY`

共12种`StorageLevel`；本质上和`Persist`没有区别，都是`Persist`方法，只是级别不同。

##### 作用

计算RDD完成后对其进行缓存，若整个计算失败，可以从缓存读取这个RDD，避免重新计算这个RDD，提升容错效率。

##### 适用场景

- 获取大量数据后；
- 进行一个非常长的计算链条，设置一些缓存点；
- 某个步骤计算非常耗时，步骤完成后对结果进行缓存；
- 进行`checkpoint`前也会缓存；

#### Checkpoint

将RDD持久化到HDFS，利用HDFS的容错能力降低RDD数据丢失的风险。

它会将RDD的依赖清空，如果HDFS也不能保证数据不丢失，则任务需要重新启动。

#### 四个层次的容错

- Stage输出失败，上层调度器`DAGScheduler`重试

- Task内部任务失败，底层`TaskScheduler`调度器重试

- 根据RDD Lineage血统重新计算
  - 对于宽依赖而言，如果结果的一个`Partition`出错，需要重新计算父`RDD`所有`Partition`
  - 对于窄依赖而言，如果结果的一个`Partition`出错，只需重新计算父RDD被丢失`Partition`依赖的那个`Partition`
  - `Cache`可看作时`Lineage`容错机制的效率提升机制，本身并无容错考虑，如果`Cache`丢失则仍需重新计算

- Checkpoint机制

### `Scheduler`

调度器，简洁清晰和高效。

- 输入：Spark RDD
- 输出：执行器Executor

![image-20211018141659263](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018141659263.png)

#### `Spark Driver`

是运行`Application`的`main`函数。

负责初始化`SparkContext`，它负责。

与集群通讯、资源申请、任务分配和监控等。

#### 流程

##### 初始化`SparkContext`

初始化了`TaskScheduler`，`SchedulerBackend`，`DAGScheduler`。

##### `RDD Transformation`操作 *`RDD.Transformation`==Lazy*

只记录RDD之间依赖关系，和操作类型，不具体调用`compute`方法。

##### 触发点：Action操作 *`RDD.action`触发`SparkContext`的`runJob`*

调用`runJob`，触发`DAGScheduler`调用`submitJob`。

![image-20211018142148221](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018142148221.png)

![image-20211018142232497](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018142232497.png)

##### `DAGScheduler`构建`Stage`

在`createStage`时从最后一个`Stage`开始递归构建`Stage`，并根据`dependency`类型划分`Stage`。

![image-20211018142405321](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018142405321.png)

##### `DAGScheduler`构建`Stage`

在提交`Stage`时从最后一个`Stage`开始回溯直到没有前序`Stage`的第一个`Stage`，执行`submitMissingTask`。

构建好`TaskSet`，计算每一个`Task`最佳位置，将`TaskSet`提交给`TaskScheduler`。

![image-20211018142641421](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018142641421.png)

##### `TaskScheduler`针对每一个`TaskSet`细粒度调度和执行

- 根据`Task`位置和执行器信息分配`Task`到`Executor`。

- 通过`SchedulerBackend`将执行命令发送到`Executor`上开始执行。

- 通过`SchedulerBackend`获得执行器相关信息。

![image-20211018142836729](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018142836729.png)

> 注意图中的两个调度算法。先来先服务和公平调度。
>
> 要先知道执行器的相关信息才能发送执行，因此要看Driver、Master、Work如何通信！

### `Executor`

#### 创建`Executor`

- 首先`Application`要向`Master`注册——==由`SchedulerBackend`通过`AppClient`的子类`ClientEndpoint`完成==；

- `Master`回复注册成功，并将`Application`发送到满足条件的`Worker`上，在`Work`上启动`Executor`；

- 通过`Worker`的`RpcEndpoint`向`Worker`发送启动`Executor`请求；

- `Worker`收到请求后启动`ExecutorRunner`，并通知`Master Executor`的相关信息；

- 此时本质上是启动了负责帮`Executor`和`Driver`通信的`Backend`：`ExecutorBackend`；

- `ExecutorBackend`直接向Driver注册`Worker`让它准备启动的`Executor`；

- 收到`Driver`确认后，`Executor`正式新建；

  ==是`Executor`主动联系`Driver`，让`SchedulerBackend`了解到所有`Executor`的信息，再让`TaskScheduler`根据`Executor`情况分配`Task`到`Executor`==

#### `Executor`资源分配

- `Driver`启动的时候从`spark-submit`收集对`executor`的需求，并在向`Master`注册时发送过去；
- 具体可以参考如何从提交程序收集需求，并一直传递给`Master`，再给`Worker`；
- `ExecutorRunner`从`Worker`那里得到这些参数，并在根据参数启动`ExecutorBackend` `JVM`进程；

#### `Executor`启动

- 在`ExecutorBackend`向`Driver`注册后，收到确认时的操作就是创建`Executor`；
- 此时`TaskScheduler`根据`Executor`情况安排好`TaskSet`调度，`Executor`也启动好了；

#### 通过`DriverEndpoint`向`ExecutorBackend`发送执行`Task`的消息

![image-20211018144030533](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018144030533.png)

#### `ExecutorBackend`收到`LaunchTask`通知后

- 调用`Executor`的`launchTask`方法；
- 内部创建`TaskRuner`，用线程池执行`TaskRunner`，调用`run`方法；

- - 反序列化出`Task`
  - 调用`Task`的`run`方法

### `Storage`

#### 通信层

`Master-Slave`结构，传输控制和状态信息。

`BlockManager`, `BlockManagerMaster`, `BlockManagerMasterEndpoint`, `BlockManagerSlaveEndpoint`。

#### 存储层

负责将数据存储到内存、磁盘或堆外内存中，为数据在远程节点生成副本。

`DiskStore`, `MemoryStore`。

![image-20211018144317048](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018144317048.png)

![image-20211018144453854](https://oss.ydjsir.com.cn/GitPages/SparkWithHadoop/image-20211018144453854.png)

### `Shuffle`

#### 可插拔的`Shuffle`框架

•`ShuffleDependency`，`ShuffleManager`，`ShuffleHandle`

•`ShuffleReader`，`ShuffleWriter`

•`ShuffleMapTask`，`ResultTask`

#### RDD创建过程

【1】构建`RDD`时确定好与父RDD之间的依赖关系，如果是`ShuffleDependency`，会向`ShuffleManager`注册，获取`Handle`，用来保存父RDD的相关信息。

#### `RDD`计算过程

【2】在实际计算时，`runTask`会调用`RDD compute`方法，其中根据`dependency`获取`reader`读取用来计算`RDD`的输入数据，也就是之前`shuffle`操作写入的数据。

#### `Task`结束处理

【3】当`runTask`完成计算后，获取子`RDD`之间的依赖关系，如果是`shuffle`依赖则同样通过`dependency`和`ShuffleManager`获得`Handle`，进而获得`writer`。

#### 拓展

`Shuffle`前后必不可少地需要网络I/O，因此通过数据序列化方法和压缩技术进行效率优化。

> Spark中序列化方法和压缩算法的配置

##### `Spark 1.0`：基于`Hash`的`Shuffle`机制

每一个`Mapper`阶段的`Task`都会为`Reduce`阶段的每一个`Task`生成1个文件，==M*R==个。

合并机制：每一个执行单位为`Reduce`阶段每一个`Task`生成==1个==文件。

##### `Spark 1.1`：基于`Sort`的`Shuffle`机制

每一个`Mapper`阶段的`Task`生成两个文件：索引和数据文件，`Reduce`阶段通过索引读取。

##### `Spark 1.4`：钨丝计划——优化内存管理模型

直接使用二进制数据，而不是`Java`对象；避免序列化和反序列化开销。

## 部署实验

详情参见 https://ydjsir.com.cn/2021/10/17/initSpark/。