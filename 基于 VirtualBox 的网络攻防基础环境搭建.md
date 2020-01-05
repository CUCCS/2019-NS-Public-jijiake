基于 VirtualBox 的网络攻防基础环境搭建

### 实验目的 ###

掌握 VirtualBox 虚拟机的安装与使用；
掌握 VirtualBox 的虚拟网络类型和按需配置；
掌握 VirtualBox 的虚拟硬盘多重加载；

## 实验环境 ##

以下是本次实验需要使用的网络节点说明和主要软件：

VirtualBox 虚拟机
攻击者主机（Attacker）：Kali Rolling 2019.2
网关（Gateway, GW）：Debian Buster
靶机（Victim）：From Sqli to shell / xp-sp3 / Kali
## 实验要求 ##
靶机可以直接访问攻击者主机

攻击者主机无法直接访问靶机

网关可以直接访问攻击者主机和靶机

靶机的所有对外上下行流量必须经过网关

所有节点均可以访问互联网
# 实验过程 #


- 配置多重加载
![](https://i.imgur.com/Sd1fLuD.png)
![](https://i.imgur.com/nQSB5tp.png)
![](https://i.imgur.com/1AX0zn0.png)
![](https://i.imgur.com/7flLsbi.png)
![](https://i.imgur.com/WYV7hfJ.png)
- 依次进行网络配置，其中靶机均为内部网络，网关有四个网卡，分别是NAT网络，仅主机网络与两个子网（两个靶机分成的两个局域网），攻击者有三个网卡，，分别为NAT网络与两个仅主机网络。如下图
![](https://i.imgur.com/IK8vgkP.png)
![](https://i.imgur.com/3wYbawH.png)
![](https://i.imgur.com/u925g6B.png)

注意到在设置网关的NAT网络时会报错，通过查阅资料，得知需要在全局设置中添加NAT网络
![](https://i.imgur.com/BzbE7Lz.png)
- 构成如下图所示的简化版网络拓扑结构（3个虚拟机）
![](https://i.imgur.com/414VGjp.png)
## 拓扑结构检验 ##
网关（包含两个内部网络）
![](https://i.imgur.com/BPlfFWF.png)
靶机
![](https://i.imgur.com/HsmJqlF.png)
攻击者
![](https://i.imgur.com/hp6AmQn.png)
由上图可知拓扑结构正确
## 连通性测试 ##


- 靶机直接访问攻击者主机
![](https://i.imgur.com/gyFcwp3.png)


- 攻击者主机无法直接访问靶机
 ![](https://i.imgur.com/Ti1Aepb.png)


- 网关可以直接访问攻击者主机和靶机
- 
首先测试网关访问攻击者主机
![](https://i.imgur.com/3fpYRav.png)
可以直接访问
再测试网关访问靶机
![](https://i.imgur.com/G0joVUB.png)
可以直接访问
## 靶机的所有对外上下行必须经过网关 ##
首先在网关开启抓包器，在靶机ping baidu.com
![](https://i.imgur.com/1FpLLRz.png)
可以捕获到靶机请求接受的数据
再关闭网关，发现靶机无法上网，故说明靶机对外上下行必须经过网关
![](https://i.imgur.com/QpqbV9G.png)
## 所有节点均可访问网络 ##
![](https://i.imgur.com/dDBjfWp.png)
如图