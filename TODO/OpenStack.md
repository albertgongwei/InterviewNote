# OpenStack

OpenStack既是一个社区，也是一个项目和一个开源软件，提供开放源码软件，建立公共和私有云，它提供了一个部署云的操作平台或工具集，其宗旨在于：帮助组织运行为虚拟计算或存储服务的云，为公有云、私有云，也为大云、小云提供可扩展的、灵活的云计算。
OpenStackd开源项目由社区维护，包括OpenStack计算（代号为Nova），OpenStack对象存储（代号为Swift），并OpenStack镜像服务（代号Glance）的集合。 OpenStack提供了一个操作平台，或工具包，用于编排云。

下面列出Openstack的详细构架图

![img](https://images2015.cnblogs.com/blog/907596/201608/907596-20160803154744809-483681990.png)

Openstack的网络拓扑结构图

![img](https://images2015.cnblogs.com/blog/907596/201608/907596-20160803154957606-291369411.png)

整个OpenStack是由控制节点，计算节点，网络节点，存储节点四大部分组成。（这四个节点也可以安装在一台机器上，单机部署）
其中：

- 控制节点负责对其余节点的控制，包含虚拟机建立，迁移，网络分配，存储分配等等
- 计算节点负责虚拟机运行
- 网络节点负责对外网络与内网络之间的通信
- 存储节点负责对虚拟机的额外存储管理等等

## 控制节点架构

控制节点包括以下服务

- 管理支持服务
- 基础管理服务
- 扩展管理服务

1. 管理支持服务包含MySQL与Qpid两个服务

   MySQL：数据库作为基础/扩展服务产生的数据存放的地方

   Qpid：消息代理(也称消息中间件)为其他各种服务之间提供了统一的消息通信服务

2. 基础管理服务包含Keystone，Glance，Nova，Neutron，Horizon五个服务

   Keystone：认证管理服务，提供了其余所有组件的认证信息/令牌的管理，创建，修改等等，使用MySQL作为统一的数据库

   Glance：镜像管理服务，提供了对虚拟机部署的时候所能提供的镜像的管理，包含镜像的导入，格式，以及制作相应的模板

   Nova：计算管理服务，提供了对计算节点的Nova的管理，使用Nova-API进行通信

   Neutron：网络管理服务，提供了对网络节点的网络拓扑管理，同时提供Neutron在Horizon的管理面板

   Horizon：控制台服务，提供了以Web的形式对所有节点的所有服务的管理，通常把该服务称为DashBoard

3. 扩展管理服务包含Cinder，Swift，Trove，Heat，Centimeter五个服务

   Cinder：提供管理存储节点的Cinder相关，同时提供Cinder在Horizon中的管理面板

   Swift：提供管理存储节点的Swift相关，同时提供Swift在Horizon中的管理面板

   Trove：提供管理数据库节点的Trove相关，同时提供Trove在Horizon中的管理面板

   Heat：提供了基于模板来实现云环境中资源的初始化，依赖关系处理，部署等基本操作，也可以解决自动收缩,负载均衡等高级特性。

   Centimeter：提供对物理资源以及虚拟资源的监控，并记录这些数据，对该数据进行分析，在一定条件下触发相应动作

控制节点一般来说只需要一个网络端口用于通信/管理各个节点

## 网络节点架构

网络节点仅包含Neutron服务

- Neutron：负责管理私有网段与公有网段的通信，以及管理虚拟机网络之间的通信/拓扑，管理虚拟机之上的防火等等

网络节点包含三个网络端口

- eth0：用于与控制节点进行通信
- eth1：用于与除了控制节点之外的计算/存储节点之间的通信
- eth2：用于外部的虚拟机与相应网络之间的通信

## 计算节点架构

计算节点包含Nova，Neutron，Telemeter三个服务

1. 基础服务

   Nova：提供虚拟机的创建，运行，迁移，快照等各种围绕虚拟机的服务，并提供API与控制节点对接，由控制节点下发任务

   Neutron：提供计算节点与网络节点之间的通信服务

2. 扩展服务

   Telmeter：提供计算节点的监控代理，将虚拟机的情况反馈给控制节点，是Centimeter的代理服务

   计算节点包含最少两个网络端口

   - eth0：与控制节点进行通信，受控制节点统一调配
   - eth1：与网络节点，存储节点进行通信

## 存储节点架构

存储节点包含Cinder，Swift等服务

- Cinder：块存储服务，提供相应的块存储，简单来说，就是虚拟出一块磁盘，可以挂载到相应的虚拟机之上，不受文件系统等因素影响，对虚拟机来说，这个操作就像是新加了一块硬盘，可以完成对磁盘的任何操作，包括挂载，卸载，格式化，转换文件系统等等操作，大多应用于虚拟机空间不足的情况下的空间扩容等等
- Swift：对象存储服务，提供相应的对象存储，简单来说，就是虚拟出一块磁盘空间，可以在这个空间当中存放文件，也仅仅只能存放文件，不能进行格式化，转换文件系统，大多应用于云磁盘/文件

存储节点包含最少两个网络接口

- eth0：与控制节点进行通信，接受控制节点任务，受控制节点统一调配
- eth1：与计算/网络节点进行通信，完成控制节点下发的各类任务

## Openstack组件

- Nova - 计算服务
- Neutron-网络服务
- Swift - 对象存储服务
- Cinder-块存储服务
- Glance - 镜像服务
- Keystone - 认证服务
- Horizon - UI服务
- Ceilometer-监控服务
- Heat-集群服务
- Trove-数据库服务

组件间的关系图如下：

![img](https://images2015.cnblogs.com/blog/907596/201608/907596-20160803161818153-400482667.png)

### OpenStack认证服务（Keystone）

Keystone为所有的OpenStack组件提供认证和访问策略服务，它依赖自身REST（基于Identity API）系统进行工作，主要对（但不限于）Swift、Glance、Nova等进行认证与授权。事实上，授权通过对动作消息来源者请求的合法性进行鉴定。下图显示了身份认证服务流程：

![img](https://images2015.cnblogs.com/blog/907596/201608/907596-20160803162042356-1138315067.png)

 

Keystone采用两种授权方式，一种基于**用户名/密码**，另一种**基于令牌（Token）**。

除此之外，Keystone提供以下三种服务：

- 令牌服务：含有授权用户的授权信息
- 目录服务：含有用户合法操作的可用服务列表
- 策略服务：利用Keystone具体指定用户或群组某些访问权限

### keystone认证服务注意点

- 服务入口：如Nova、Swift和Glance一样每个OpenStack服务都拥有一个指定的端口和专属的URL，我们称其为入口（endpoints）。

- 区位：在某个数据中心，一个区位具体指定了一处物理位置。在典型的云架构中，如果不是所有的服务都访问分布式数据中心或服务器的话，则也称其为区位。

- 用户：Keystone授权使用者

  PS：代表一个个体，OpenStack以用户的形式来授权服务给它们。用户拥有证书（credentials），且可能分配给一个或多个租户。经过验证后，会为每个单独的租户提供一个特定的令牌。

- 服务：总体而言，任何通过Keystone进行连接或管理的组件都被称为服务。举个例子，我们可以称Glance为Keystone的服务。

- 角色：为了维护安全限定，就云内特定用户可执行的操作而言，该用户关联的角色是非常重要的。

  PS：一个角色是应用于某个租户的使用权限集合，以允许某个指定用户访问或使用特定操作。角色是使用权限的逻辑分组，它使得通用的权限可以简单地分组并绑定到与某个指定租户相关的用户。

租间：租间指的是具有全部服务入口并配有特定成员角色的一个项目。
　　PS：一个租间映射到一个Nova的“project-id”，在对象存储中，一个租间可以有多个容器。根据不同的安装方式，一个租间可以代表一个客户、帐号、组织或项目。

**OpenStack计算设施----Nova**

Nova是OpenStack计算的弹性控制器。OpenStack云实例生命期所需的各种动作都将由Nova进行处理和支撑，这就意味着Nova以管理平台的身份登场，负责管理整个云的计算资源、网络、授权及测度。虽然Nova本身并不提供任何虚拟能力，但是它将使用libvirt API与虚拟机的宿主机进行交互。Nova通过Web服务API来对外提供处理接口，而且这些接口与Amazon的Web服务接口是兼容的。

功能及特点：　
实例生命周期管理
计算资源管理
网络与授权管理
基于REST的API
异步连续通信
支持各种宿主：Xen、XenServer/XCP、KVM、UML、VMware vSphere及Hyper-V

Nova弹性云（OpenStack计算部件）包含以下主要部分：
API Server（nova-api）
消息队列（rabbit-mq server）
运算工作站（nova-compute）
网络控制器（nova-network）
卷管理（nova-volume）
调度器（nova-scheduler）

解释如下：
1）API服务器（nova-api）
API服务器提供了云设施与外界交互的接口，它是外界用户对云实施管理的唯一通道。通过使用web服务来调用各种EC2的API，接着API服务器便通过消息队列把请求送达至云内目标设施进行处理。作为对EC2-api的替代，用户也可以使用OpenStack的原生API，我们把它叫做“OpenStack API”。

2）消息队列（Rabbit MQ Server）
OpenStack内部在遵循AMQP（高级消息队列协议）的基础上采用消息队列进行通信。Nova对请求应答进行异步调用，当请求接收后便则立即触发一个回调。由于使用了异步通信，不会有用户的动作被长置于等待状态。例如，启动一个实例或上传一份镜像的过程较为耗时，API调用就将等待返回结果而不影响其它操作，在此异步通信起到了很大作用，使整个系统变得更加高效。

　3）调度器（nova-scheduler）　　

调度器负责把nova-API调用送达给目标。调度器以名为“nova-schedule”的守护进程方式运行，并根据调度算法从可用资源池中恰当地选择运算服务器。有很多因素都可以影响调度结果，比如负载、内存、子节点的远近、CPU架构等等。强大的是nova调度器采用的是可插入式架构。

目前nova调度器使用了几种基本的调度算法：
　　随机化：主机随机选择可用节点；
　　可用化：与随机相似，只是随机选择的范围被指定；
　　简单化：应用这种方式，主机选择负载最小者来运行实例。负载数据可以从别处获得，如负载均衡服务器。

4）运算工作站（nova-compute）
运算工作站的主要任务是管理实例的整个生命周期。他们通过消息队列接收请求并执行，从而对实例进行各种操作。在典型实际生产环境下，会架设许多运算工作站，根据调度算法，一个实例可以在可用的任意一台运算工作站上部署。

　5）网络控制器（nova-network）
网络控制器处理主机的网络配置，例如IP地址分配，配置项目VLAN，设定安全群组以及为计算节点配置网络。

　6）卷工作站（nova-volume）　
卷工作站管理基于LVM的 实例卷，它能够为一个实例创建、删除、附加卷，也可以从一个实例中分离卷。卷管理为何如此重要？因为它提供了一种保持实例持续存储的手段，比如当结束一个 实例后，根分区如果是非持续化的，那么对其的任何改变都将丢失。可是，如果从一个实例中将卷分离出来，或者为这个实例附加上卷的话，即使实例被关闭，数据 仍然保存其中。这些数据可以通过将卷附加到原实例或其他实例的方式而重新访问。

因此，为了日后访问，重要数据务必要写入卷中。这种应用对于数据服务器实例的存储而言，尤为重要。

**OpenStack镜像服务器----Glance**

OpenStack镜像服务器是一套虚拟机镜像发现、注册、检索系统，我们可以将镜像存储到以下任意一种存储中：
本地文件系统（默认）
S3直接存储
S3对象存储（作为S3访问的中间渠道）
OpenStack对象存储等等。
功能及特点：
提供镜像相关服务。

Glance构件：
1）Glance-API：
　 主要负责接收响应镜像管理命令的Restful请求，分析消息请求信息并分发其所带的命令（如新增，删除，更新等）。默认绑定端口是9292。
2）Glance-Registry：
　　主要负责接收响应镜像元数据命令的Restful请求。分析消息请求信息并分发其所带的命令（如获取元数据，更新元数据等）。默认绑定的端口是9191。

**OpenStack存储设施----Swift**

Swift为OpenStack提供一种分布式、持续虚拟对象存储，它类似于Amazon Web Service的S3简单存储服务。Swift具有跨节点百级对象的存储能力。Swift内建冗余和失效备援管理，也能够处理归档和媒体流，特别是对大数据（千兆字节）和大容量（多对象数量）的测度非常高效。

swift功能及特点：　　
海量对象存储
大文件（对象）存储
数据冗余管理
归档能力-----处理大数据集
为虚拟机和云应用提供数据容器
处理流媒体
对象安全存储
备份与归档
良好的可伸缩性

Swift组件
Swift账户
Swift容器
Swift对象
Swift代理
Swift RING
Swift代理服务器　　
用户都是通过Swift-API与代理服务器进行交互，代理服务器正是接收外界请求的门卫，它检测合法的实体位置并路由它们的请求。
此外，代理服务器也同时处理实体失效而转移时，故障切换的实体重复路由请求。

Swift对象服务器
对象服务器是一种二进制存储，它负责处理本地存储中的对象数据的存储、检索和删除。对象都是文件系统中存放的典型的二进制文件，具有扩展文件属性的元数据（xattr）。

注意：xattr格式被Linux中的ext3/4，XFS，Btrfs，JFS和ReiserFS所支持，但是并没有有效测试证明在XFS，JFS，ReiserFS，Reiser4和ZFS下也同样能运行良好。不过，XFS被认为是当前最好的选择。

Swift容器服务器
容器服务器将列出一个容器中的所有对象，默认对象列表将存储为SQLite文件（译者注：也可以修改为MySQL，安装中就是以MySQL为例）。容器服务器也会统计容器中包含的对象数量及容器的存储空间耗费。

Swift账户服务器
账户服务器与容器服务器类似，将列出容器中的对象。

Ring（索引环）

Ring容器记录着Swift中物理存储对象的位置信息，它是真实物理存储位置的实体名的虚拟映射，类似于查找及定位不同集群的实体真实物理位置的索引服务。这里所谓的实体指账户、容器、对象，它们都拥有属于自己的不同的Rings。

**OpenStack管理的Web接口----Horizon**

Horizon是一个用以管理、控制OpenStack服务的Web控制面板，它可以管理实例、镜像、创建密匙对，对实例添加卷、操作Swift容器等。除此之外，用户还可以在控制面板中使用终端（console）或VNC直接访问实例。

总之，Horizon具有如下一些特点：　　
实例管理：创建、终止实例，查看终端日志，VNC连接，添加卷等
访问与安全管理：创建安全群组，管理密匙对，设置浮动IP等
偏好设定：对虚拟硬件模板可以进行不同偏好设定
镜像管理：编辑或删除镜像
查看服务目录
管理用户、配额及项目用途
用户管理：创建用户等
卷管理：创建卷和快照
对象存储处理：创建、删除容器和对象
为项目下载环境变量