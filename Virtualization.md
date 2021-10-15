# 虚拟化技术

## 概论

### 概念

虚拟化技术的核心思想是利用软件或固件管理程序构成虚拟化层，把物理资源映射为虚拟资源。在虚拟资源上可以安装和部署多个虚拟机，实现多用户共享物理资源。

### 历史

20世纪60年代，IBM公司推出虚拟化技术。

### 实现方法

虚拟化的本质就是将原先的物理设备进行==逻辑化==，转化成一个文件夹或文件，实现软硬件的解耦。

核心在于添加一个称为虚拟化层的软件以管理客户操作系统，使其能够==独立于==主机操作系统同时运行在同一个硬件上。

- Hypervisor

- Virtual Machine Monitor（虚拟机监视器）

虚拟化层软件用于虚拟化==基础设施==：CPU，内存，存储，I/O，网络。

## 虚拟化的层次

![image-20211006173424388](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006173424388.png)

![image-20211006174817362](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006174817362.png)

> 这个图里面由于实现复杂度是越低越好，所以X越多代表实现越简单。

## 硬件级虚拟化

### 三个要求

- 为程序提供与原始硬件机器基本一致的环境

- 运行在该虚拟硬件环境中的程序性能损失应该较低

- **系统资源应该处于VMM的完全控制之中**

虚拟机同时运行时，共享的底层系统资源对某些虚拟机而言不一定可用。

### 裸机虚拟化与寄居虚拟化

#### 裸机虚拟化

==没有主机操作系统== 。Hypervisor直接对硬件切分和抽象，再安装客户操作系统。如`ESXi`。

#### 寄居虚拟化

需要==主机操作系统==，再运行`VMM`，再安装操作系统。如`VMWare Workstation`。

> 补充：两种不同级别的`Hypervisor`
>
> https://www.vmware.com/topics/glossary/content/hypervisor
>
> Ⅰ类是直接跑在硬件上的，不需要以主机操作系统为基础，如`ESXi`；
>
> Ⅱ类需要跑在一个特定的操作系统上。

### 全虚拟化

Hypervisor或VMM负责控制硬件和“传话”。比如说各类指令集虚拟化方式（？）和部分硬件辅助虚拟化方式。

![image-20211006172910715](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006172910715.png)

### 半虚拟化

不再以普通操作系统的方式访问虚拟硬件资源，需要修改==虚拟机==操作系统。

![image-20211006172902227](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006172902227.png)

### 硬件辅助虚拟化

典型代表是`Intel`的`VT-x`和`AMD`的`AMD-V`两种技术，其核心思想都是通过引入新的指令和运行模式（如`Intel`的`-1`环机制）， 使VMM和Guest OS分别运行在不同模式。

> 根据维基百科：https://en.wikipedia.org/wiki/Hardware-assisted_virtualization
>
> In [computing](https://en.wikipedia.org/wiki/Computing), **hardware-assisted virtualization** is a [platform virtualization](https://en.wikipedia.org/wiki/Platform_virtualization) approach that enables efficient [full virtualization](https://en.wikipedia.org/wiki/Full_virtualization) using help from hardware capabilities, primarily from the host processors. A full virtualization is used to emulate a complete hardware environment, or [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine), in which an unmodified guest [operating system](https://en.wikipedia.org/wiki/Operating_system) (using the same [instruction set](https://en.wikipedia.org/wiki/Instruction_set) as the host machine) effectively executes in complete isolation. 
>
> 硬件辅助虚拟化==不需要修改虚拟机操作系统==，是实现==全虚拟化==的重要基础。
>
> 硬件辅助虚拟化也常有助于实现混合虚拟化。

## 操作系统级虚拟化

该方式可看作是“寄居虚拟化”的一种。在该方式下没有`Hypervisor/VMM`。实现方法是在操作系统中插入一个虚拟化层，而后在同一个操作系统内核内（共享内核），运行多个虚拟机。这种类型的典型代表是`OpenVZ`——几乎是最廉价的`VPS`实现方案。此处的“虚拟机”被称为虚拟执行环境(`Virtual Execution Environment`)或虚拟专用系统(`Virtual Private System`)或`容器`。

### 优缺点

#### 优点

- 最小的启动或停止开销，资源需求低，可扩展性强

- 需要时，可同步虚拟机与宿主操作系统环境状态的变化
- 设计方法
- - 在同一台物理机上的操作系统级虚拟机共享同一个操作系统内核
  - 虚拟化层中允许虚拟机中进程访问尽可能多的主机资源

#### 缺点

- 同一个容器中的操作系统级虚拟机必须使用相同的客户操作系统

- 资源隔离较差

### Docker引擎和Docker容器

Docker引擎实现了一个高级API提供轻量级的容器，以隔离的方式运行软件进程

Docker容器与传统虚拟机不同：==不使用客户操作系统==，==包含应用程序及二进制文件和库==。

#### 与虚拟机对比

- 客户操作系统 vs. 独立/共享二进制文件和库；

- 20秒启动 vs. 500毫秒启动；

- 较高应用灵活性 vs. 低成本高性能；

- 适合没有编排的多个应用使用 vs. 适用于编排条件下多个副本相同的应用扩展；

![image-20211006175156001](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006175156001.png)

## 程序库API级虚拟化

在这方面最典型的例子莫过于`WINE`。该项目旨在以开源形式实现闭源的Windows API的方式让Windows应用能直接跑在Linux系统上。课上没有展开。

## 应用程序级虚拟化

![image-20211006173941588](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006173941588.png)

## 发展趋势

- 云计算中虚拟机和容器共存

- 容器成为云计算主流

- 高效率的unikernel技术

- - LibOS：在应用程序层中实施访问控制和隔离，用户应用无需在用户模式和内核模式间切换，直接访问硬件；然而多个应用程序同时运行时隔离性不好。
  - 使用操作系统虚拟化克服硬件资源隔离缺陷：LibOS通过VMM驱动物理硬件，像虚拟机一样运行。

![image-20211006175541386](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006175541386.png)

## 具体虚拟化技术介绍

![image-20211006175736423](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006175736423.png)

![image-20211006175747074](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006175747074.png)

# 云计算与虚拟化

## 服务器虚拟化

### 定义

将一个或多个物理服务器虚拟成多个逻辑上的服务器。

### CPU虚拟化

#### 概述

- 任意时刻一个物理CPU只能运行一个虚拟CPU；

- 每个客户操作系统可以使用一个或多个虚拟CPU；

- 各个操作系统之间虚拟CPU运行相互隔离，互不影响；

![image-20211006221148678](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006221148678.png)

|                             执行                             |                             调度                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| 虚拟CPU的正确运行是要保证虚拟机指令正确运行，现有的实现技术包括模拟执行和监控执行 | 调度问题是指VMM决定当前哪个虚拟CPU在物理CPU上运行，要保证隔离性、公平性和性能。 |

#### ==模拟执行==敏感指令

> 无害指令只需要监控执行即可。

敏感指令可以是控制敏感指令或行为敏感指令。

- 控制敏感指令是指那些尝试修改系统中资源配置的指令，例如，更改操作模式或 CPU 计时器。

- 行为敏感指令是指其行为由系统中当前资源配置确定的指令。

**特权指令**：虚拟机中的特权指令以宿主机的用户模式陷入，而不是以系统模式陷入。 陷入指令是指将控制传输到系统模式，其中虚拟机监控程序（如虚拟化中）或操作系统（如传统 OS 中）在将控制切换回原始进程之前执行某种操作。

> 仅当敏感指令集是特权指令集的子集时才能构造VMM。
>
> ![image-20211006230034195](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006230034195.png)

#### 虚拟机隔离

##### 定义

指虚拟机之间在没有授权许可的情况下，互相之间不可通信、不可联系的一种技术。

##### 软件角度

互相隔离的虚拟机之间保持独立，如同一个完整的计算机。

##### 硬件角度

被隔离的虚拟机相当于一台物理机，有自己的CPU、内存、硬盘、I/O等，它与宿主机之间保持互相独立的状态。

##### 网络角度

被隔离的虚拟机如同物理机一样，既可以对外提供网络服务，也一样可以从外界接受网络服务。

#### 现有虚拟机隔离机制

- 网络隔离

- 构建虚拟安全文件防护网

- 基于访问控制的逻辑隔离机制

- 通过硬件虚拟让每一个虚拟机无法突破虚拟机管理器给出的资源限制

- 硬件提供的内存保护机制

- 进程地址空间的保护机制，IP地址隔离

### 内存虚拟化

#### 概述

内存虚拟化技术把物理内存统一管理，包装成多个虚拟的物理内存提供给若干虚拟机使用，每个虚拟机拥有各自独立的内存空间。

> 这里考虑的更多地是操作系统看到的内存地址，而不是现代操作系统中广泛采用的虚存机制

在没有虚拟化的操作系统中看到的内存：直接是机器地址的物理空间；虚拟化后，VMM要让客户操作系统看到“假的”物理空间，要做两件事：

- 维护客户机物理地址和宿主机器的机器地址之间的映射；

- 截获虚拟机对客户机物理地址的访问，并根据所记录的映射关系，转换成机器地址；

> 这个过程挺像操作系统里面给进程提供虚拟内存空间的。

注意到有==基于软件==的内存虚拟化和==硬件辅助==的内存虚拟化之分。

![image-20211006230525144](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006230525144.png)

#### 内存隔离

##### 内存管理单元 Memory Management Unit

- 管理虚拟存储器的控制路线

- 管理物理存储器的控制线路

- 负责将虚拟地址映射为物理地址

- 提供硬件机制的内存访问授权

##### 实现方式

虚拟机监控器使用分段和分页机制对自身的物理内存进行保护。

##### 实例

`Xen`将这层中间地址真正地映射到机器地址上却可以是不连续的，这样保证了所有的物理内存可被任意分配给不同的`Guest OS`。x86体系结构提供了支持分段机制的虚拟内存，这能够提供另一种形式的特权级分离。

### I/O设备虚拟化

I/O设备虚拟化同样是由VMM进行管理的。I/O设备虚拟化能欺骗客户机操作系统，让它感受到自己像主机操作系统一样能够访问外设。

#### 三个部分

- 设备的==发现==：保证设备驱动可以正常使用

- 访问的==截获==：也是一种敏感指令，需要模拟执行（除非使硬件辅助I/O虚拟化）

- 设备的==模拟==：见下图

  ![image-20211006231140178](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006231140178.png)

  > `VT-d`技术是最右侧方式的典型代表

#### 网络隔离

##### 目标

确保把有害的攻击隔离，再可信网络之外和保证可信网络内部信息不外泄的前提下，完成网间数据的安全交换。

##### 关键

在于系统对通信数据的控制，即通过不可路由的协议完成网间数据交换

##### 安全要素

感知交换数据的机密性、完整性、可用性、可控性、抗抵赖

>  通信硬件工作在网络最底层，无法达到上述要求，所以需要额外的软件上的加强

##### 安全机制

访问控制、身份认证、加密签名

##### 实现原理与效果

![image-20211006233610659](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006233610659.png)

### 虚拟机迁移

#### 按源到目的地角度分类

- 物理机到虚拟机 `P2V`

- 虚拟机到虚拟机 `V2V`

- 虚拟机到物理机 `V2P`

#### 云计算关注重点：实时迁移

##### 定义

==保持虚拟机运行==的同时，将其从一个计算机迁移到另一个计算机，并在目的计算机恢复运行。

##### 重要意义

- 云计算中心物理服务器负载处于动态变化中，当无法提供额外物理资源而为了负载平衡，可将占用热门物理资源的虚拟机迁移到其他物理服务器；
- 物理服务器定期升级的需要，升级前将虚拟机迁移到其他物理服务器，等升级完成再迁移回来；



#### 迁移过程

![image-20211006232022517](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006232022517.png)

#### 迁移内存：==最困难==之处

第一阶段，`Push`阶段（把大段变动不频繁的部分先挪过去）；第二阶段，`Stop-and-Copy`阶段（停机复制）；第三阶段，`Pull`阶段（收尾）。

| **迁移方案**                    | **优势与劣势**                                               |
| ------------------------------- | :----------------------------------------------------------- |
| `Stop-and-Copy `                | 方法比较简单 <br>总迁移时间也最短 <br>停机时间无法接受       |
| `Stop-and-Copy`和`Pull`阶段结合 | 停机时间很短 <br/>总迁移时间很长<br/>`Pull`阶段复制造成的性能下降 |
| `Push`和`Stop-and-Copy`阶段结合 | 平衡了停机时间和总迁移时间之间的矛盾<br/>需要有一种算法能够测定工作集，以避免反复重传<br/>可能会占用大量的网络带宽，对其他服务造成影响 |

#### 网络资源的迁移

虚拟机这种系统级别的封装方式意味着迁移时VM的所有网络设备，包括协议状态（如TCP连接状态）以及IP地址都要随之一起迁移。

#### 存储设备迁移

迁移存储设备的最大障碍在于==需要占用大量时间==和==网络带宽==，通常的解决办法是==以共享的方式共享数据和文件系统==，而非真正迁移。（改个挂载点就好）

目前大多数集群使用NAS（Network Attached Storage，网络连接存储）作为存储设备共享数据。

## 存储虚拟化

### 定义

把分布的异构存储设备统一为一个或几个大的存储池。

存储虚拟化将系统中==分散==且==异构==的存储资源整合起来，建立一个统一的连续编址的逻辑存储空间。

<img src="https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006234341048.png" alt="image-20211006234341048" style="zoom:50%;" />

### 意义

- 提高存储资源利用率；
- 降低单位存储空间成本；
- 降低存储管理的负担和复杂性；
- 减少存储系统的管理开销；
- 实现存储系统数据共享
- 提供透明的高可靠性和可扩展性。

### 技术

| 技术                                             | 作用                                   |
| ------------------------------------------------ | -------------------------------------- |
| 在虚拟层通过适用数据镜像、数据校验和多路径等技术 | 提高了数据的可靠性及系统的可用性       |
| 利用负载均衡、数据迁移、数据块重组等技术         | 提升系统的潜在性能                     |
| 整合和重组底层物理资源                           | 得到多种不同性能和可靠性的新的虚拟设备 |
| 总结                                             | 满足多种存储应用的需求                 |

### 实现方式

![image-20211006234400964](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006234400964.png)

> 课上还补充了`vSphere`的实例

## 网络虚拟化

### 定义

在底层物理网络和网络用户之间增加一个抽象层。

### 演进与对比

#### 传统数据中心

- 服务器之间操作系统和上层软件异构、接口与数据格式不统一；

- 数据中心内网络传输效率低；

#### 云计算后

- 数据同步传送的大流量、备份的大流量、虚拟机迁移的大流量；
- 采用统一的交换网络减少布线、维护工作量和扩容成本；

#### 引入虚拟化技术后

数据中心网络虚拟化分为==核心层==、==接入层==和==虚拟机网络==虚拟化三个层面。

> 详细内容见数据中心节

![image-20211006235028492](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006235028492.png)

## 桌面虚拟化

每个桌面镜像就是一个带有应用程序的操作系统，终端用户通过一个虚拟显示协议来访问他们的桌面系统。这样做的目的就是**使用户的使用体验同他们使用桌面上的PC**一样。

**第一代桌面虚拟技术**实现了在同一个独立的计算机硬件平台上，同时安装多个操作系统，并同时运行这些操作系统。

**第二代桌面虚拟化技术**进一步将桌面系统的运行环境与安装环境、应用与桌面配置文件进行了拆分，从而大大降低了管理复杂度与成本，提高了管理效率。

![image-20211006234808711](https://ydjsir-cn.oss-cn-shenzhen.aliyuncs.com/GitPages/CloudComputing/Virtualization.assets/image-20211006234808711.png)

> 在这方面最常见的部署方式莫过于瘦客户机+中央服务器的组合了。桌面上放的小电脑仅用于开远程桌面或其他类似技术到中央服务器的虚拟机上。
>
> ~~夹带私货：瘦客户机做HTPC或者软路由什么的好玩得很，快入坑~~