---
title: 初始Azure
date: 2020-07-12 00:44:51
tags: 
- Azure
- Overview
- MS Work
---

#### 一些Azure学习资源

- github上的Azure文档
  - [虚拟机网络概述](https://github.com/MicrosoftDocs/azure-docs/blob/master/includes/virtual-machines-common-network-overview.md)
  - [规划虚拟网络](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-network/virtual-network-vnet-plan-design-arm.md)

#### 什么是云计算(Cloud computing) ?

- 将计算服务—包括服务器, 存储, 数据库, 网络, 软件, 分析和智能分布在因特网上, 提供计算机系统资源(尤其是数据存储)和计算能力的按需可用性, 而无需用户进行主动管理。该术语通常用于描述Internet上可供许多用户使用的数据中心。
- 云计算的优势:
  - 避免了直接购买硬件, 软件, 部署并运行本地数据中心的花费, 只需要根据用户实际使用的服务进行收费, 更加经济。
  - 资源可以部署在全球各地, 伸缩性更强
  - 云计算服务运行在安全的数据中心构成的全球化网络上, 相较于单一的数据中心, 降低了网络延迟。
  - 由于可以在云提供商网络上的多个冗余站点上镜像数据，因此云计算使数据备份，灾难恢复和业务连续性变得更容易且成本更低。

#### 什么是Azure ?

Microsoft Azure是一组不断扩展的云服务，可帮助您的组织应对业务挑战。 您可以使用自己喜欢的工具和框架，在庞大的全球网络上自由构建，管理和部署应用程序。

#### Virtual private server

虚拟专有服务器是作为服务出售的虚拟机器, 用户对VPS具有superuser权限, 一个实体Server上能同时运行多个VPS， 因此花费更低。

#### Blob

Azure Blob存储是一项用于存储大量非结构化对象数据（例如文本或二进制数据）的服务。 您可以使用Blob存储向公众公开数据，也可以私下存储应用程序数据。 Blob存储的常见用途包括：

- 将图像或文档直接提供给浏览器
- 存储文件以进行分布式访问
- 流式传输视频和音频
- 存储数据以进行备份和还原，灾难恢复和归档
- 存储数据以供本地或Azure托管服务进行分析

#### Virtual Machine Scale Set

Azure VMSS使您可以创建和管理一组负载平衡的VM。 VM实例的数量可以根据需求或定义的计划自动增加或减少。 VMSS可为您的应用程序提供高可用性，并允许您集中管理，配置和更新大量VM。 借助VMSS，您可以为计算，大数据和容器工作负载等领域构建大规模服务。

VMSS的优势:

- 易于管理大量VM
  - 用大量VM运行应用时, 为了得到可靠的性能, 应该保持VM大小, 磁盘配置, 安装的应用等的一致
  - 支持Load Balancer和Gateway
- 为应用提供高可用性
- 随着资源需求的改变自动伸展

## Network

#### Azure Virtual Network(VNet)

VNet是在云中你的专有网络的一种表示. 是专用于您的订阅的Azure云的逻辑隔离。创建VNet后， 你的VNet中的服务和VM可以直接安全地在云中相互通信。

#### Network Interfaces (NIC)

- NIC是VM和VNet之间的互连。VM必须含有至少一个NIC。多个NIC可以使VM连接至多个Subnet.
- NIC与相连的VM和VNet都必须有相同的位置和订阅, VM在创建后不能改变相连的VNet， 但可以改变相连的Subnet。每个NIC都被赋予了一个不变的MAC地址。
- 可以将公有IP地址或私有IP地址分配给NIC。公有IP使得能与内部和外部资源(互联网)以及未连接到VNet的Azure资源进行通信, 且不需要NAT; 私有IP能在VNet内部, 以及本地网络中进行通信, 接入互联网需要NAT, 每个VM至少被分配一个私有IP

To learn more about NAT in Azure, read [Understanding outbound connections in Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/load-balancer/load-balancer-outbound-connections.md).

- 资源的IP地址分配有两种方式: 动态和静态. 默认是动态分配, 即IP在创建VM时被分配, 在VM终止或删除时被释放. 为了使得VM有不变的IP地址, 需要将分配方式设置为静态。
- 创建一个公有IP地址后, 通过将它分配给NIC将它与VM关联起来.

#### Subnets

- 一个subnet是VNet中一个区间的IP地址, 可将一个VNet划分为多个subnet用于组织和安全. VM的每一个MIC都同VNet下的一个subnet相连接. 同一VNet下的所有NIC(不论是否连接至一个subnet)不需要其他配置即可直接通信.
- 默认情况下, subnet之间没有安全界限, 不同subnet下的VM可以直接通信. 通过设置Network Security Groups (NSGs), 你可以控制subnet之间, VM之间的通信流.

#### Network security groups(NSG)

- NSG包含一系列Access Control List (ACL) 规则用来准许或拒绝通往subnets或NICs的网络流量. 如果NSG同一个subnet相关联, ACL规则会会应用于该subnet下的所有VM. 若BSG同某一NIC相关联, 那么只会限制通向这一NIC的流量, 若该NIC相连的VM有多个NIC相连, 那么不影响其他NIC的流量.
- ACL规则分为向内和向外两种, 同一set中的规则都具有不同的优先级, NSG中有自带的默认规则, 具有最低的优先级.
- 确保在规划你的VM和VNet时规划NSG.

#### Load balancers

 [doc](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/load-balancer/load-balancer-overview.md)

- 负载平衡是指在一组后端资源或服务器之间平均分配负载（传入网络流量）.
- Azure Load Balancer运作在OSI模型的第四层(传输层), 是端到端的单点通信, 将到达Load Balancer前端的入站流量均匀分发给后端的实例, 后端实例可以是Azure虚拟机或VMSS中的实例.
- public load balancer能为VNet中的VM提供同外部的连接, 通过将私有IP转换为公有IP. 用于将因特网流量负载至VM.
- internal(or private) load balancer仅在前端需要私有IP时被使用, 用于在VNet内部负载平衡流量. 在混合方案中，可以从本地网络访问负载均衡器前端。
- 负载平衡器在负载平衡器上的公用IP地址和端口与VM的专用IP地址和端口之间映射传入和传出流量。
- 使用port forwarding可以通过公有IP地址与端口访问VNet中的VM.