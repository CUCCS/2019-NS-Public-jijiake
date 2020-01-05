# 基于 Scapy 编写端口扫描器 #
# 实验目的 #


- 掌握网络扫描之端口状态探测的基本原理
# 实验环境 #
- python + scapy
# 实验要求 #

- 禁止探测互联网上的 IP ，严格遵守网络安全相关法律法规

- 完成以下扫描技术的编程实现
        
   1. TCP connect scan / TCP stealth scan
        
   2. TCP Xmas scan / TCP fin scan / TCP null scan
       
   3.  UDP scan

- 上述每种扫描技术的实现测试均需要测试端口状态为：开放、关闭 和 过滤 状态时的程序执行结果

- 提供每一次扫描测试的抓包结果并分析与课本中的扫描方法原理是否相符？如果不同，试分析原因；

- 在实验报告中详细说明实验网络环境拓扑、被测试 IP 的端口状态是如何模拟的

- （可选）复刻 nmap 的上述扫描技术实现的命令行参数开关
# 实验过程 #
## （一）拓扑结构 ##
攻击者主机（kali）使用有intnet1的内部网络的网卡
靶机（kali2）也使用有intnet1的内部网络的网卡
![](https://i.imgur.com/vpH0aRj.png)
![](https://i.imgur.com/aIhq2dG.png)
![](https://i.imgur.com/WnnwpGZ.png)
## （二）扫描技术实现 ##
代码通过ssh，由主机传输到kali（攻击者）的code文件夹中
![](https://i.imgur.com/1yTGDgH.png)

## TCP Connect Scan ##
端口关闭状态的测试

此时发现kali2的80端口处于关闭状态
![](https://i.imgur.com/HjwbKHV.png)
在靶机中设置监听eth0的网卡开始抓包
在攻击者主机中运行TCP_connect_scan.py
在攻击者主机界面观察到端口扫描的结果是靶机的80端口处于关闭状态
![](https://i.imgur.com/RqYUmW2.png)
![](https://i.imgur.com/Q3eMtkv.png)
分析抓到的包：


1. 攻击者主机给靶机的80端口发送了设置SYN标志的TCP包


1. 靶机发送给攻击者主机的返回包中设置了RST标志
证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口开启状态的测试

打开靶机的80端口监听
![](https://i.imgur.com/U1BJ8Yx.png)
其他步骤类似
在攻击者主机界面观察到端口扫描的结果是：靶机的80端口为打开状态
![](https://i.imgur.com/eWbtAPe.png)
![](https://i.imgur.com/7eebbjX.png)
分析靶机抓到的包

 

1. 攻击者和靶机之间进行了完整的3次握手TCP通信（SYN, SYN/ACK, 和RST）
    

1. 并且该连接由靶机在最终握手中发送确认ACK+RST标志来建立


1. 证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤状态的测试

在靶机设置80端口被防火墙过滤，确保设置成功
![](https://i.imgur.com/gQG9Qwx.png)
再开启抓包，运行TCP_connect_scan.py
![](https://i.imgur.com/ygw6GZh.png)
![](https://i.imgur.com/HsqZ0z7.png)
在攻击者主机界面观察到端口扫描的结果是：靶机的80端口为过滤状态
分析抓到的包：
攻击者主机给靶机的80端口发送了设置SYN标志的TCP包
靶机未返回TCP数据包给攻击者
靶机返回给攻击者一个ICMP数据包，且该包类型为type3
证明了端口被过滤，这与课本中的扫描方法原理相符，实验成功。 
## TCP Stealth Scan ##
端口关闭的状态

关闭防火墙
![](https://i.imgur.com/bzdiUa5.png)

靶机的80端口没有打开监听
![](https://i.imgur.com/Uw3F7U0.png)

在靶机设置监听eth0网卡开始抓包

在攻击者虚拟机运行扫描文件TCP_stealth_scan.py，停止抓包，存储在本地
在攻击者主机界面观察到端口扫描的结果是：靶机的80端口为关闭状态
![](https://i.imgur.com/nEO8U9Y.png)
分析靶机抓到的包

  

1. 攻击者主机给靶机的80端口发送了设置SYN标志的TCP包
 

1. 靶机发送给攻击者主机的TCP返回包中设置了RST标志证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口开启状态的测试 
打开靶机的80端口监听
在靶机设置监听eth0网卡开始抓包
在攻击者虚拟机运行扫描文件TCP_stealth_scan.py，停止抓包，存储在本地
在攻击者主机界面观察到端口扫描的结果是：靶机的80端口为打开状态
![](https://i.imgur.com/40vjtEm.png)
分析靶机抓到的包

  

1. 前三个包类似于TCP连接扫描，完成了SYN, SYN/ACK, 和RST
2. 且最后在TCP数据包中发送的是RST标志而不是RST + ACK。


1. 证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤状态的测试 
![](https://i.imgur.com/YpBZtb9.png)
分析靶机抓到的包



1.  攻击者主机给靶机的80端口发送了设置SYN标志的TCP包


1.  靶机没有返回TCP数据包给攻击者主机
  

1. 靶机返回给攻击者主机一个ICMP数据包，且该包类型为type3
 

1. 证明了端口被过滤，这与课本中的扫描方法原理相符，实验成功。
 
## TCP XMAS Scan ##
端口关闭测试
![](https://i.imgur.com/8CEEw8c.png)
![](https://i.imgur.com/Ip6InkK.png)

分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了PSH，FIN和URG标志的TCP数据包
    

1. 靶机发送给攻击者主机的TCP返回包中设置了RST标志
    

1. 证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口开启测试

![](https://i.imgur.com/TlysdCF.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了PSH，FIN和URG标志的TCP数据包
   

1.  靶机没有发送TCP包响应，无法区分其80端口打开/被过滤。
    

1. 但是靶机也没有发送ICMP数据包给攻击者主机，说明端口不是被过滤状态
    

1. 由此证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤状态的测试 

![](https://i.imgur.com/gPhFocZ.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了PSH，FIN和URG标志的TCP数据包
    

1. 靶机返回给靶机一个ICMP数据包，且该包类型为type3
    

1. 证明了端口被过滤，这与课本中的扫描方法原理相符，实验成功。 

## TCP FIN Scan ##

端口关闭测试

![](https://i.imgur.com/DWro8Sf.png)
![](https://i.imgur.com/OBjjcsX.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了FIN标志的TCP数据包
    

1. 靶机发送给攻击者主机的TCP返回包中设置了RST标志
   

1.  证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口开启测试
![](https://i.imgur.com/xymZrUV.png)

分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了FIN标志的TCP数据包
    

1. 靶机没有发送TCP包响应，无法区分其80端口打开/被过滤。
    

1. 但是靶机也没有发送ICMP数据包给攻击者主机，说明端口不是被过滤状态
    

1. 由此证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤状态的测试 

![](https://i.imgur.com/Rs9psh1.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了设置了FIN标志的TCP数据包
    

1. 靶机返回给靶机一个ICMP数据包，且该包类型为type3
    

1. 证明了端口被过滤，这与课本中的扫描方法原理相符，实验成功。 

## TCP NULL Scan ##
端口关闭状态的测试

![](https://i.imgur.com/xWgk3cL.png)
![](https://i.imgur.com/lkGQsOg.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了一个没有任何标志位的TCP包
    

1. 靶机发送给攻击者主机的TCP返回包中设置了RST标志
    

1. 证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口打开测试

![](https://i.imgur.com/DQRatG9.png)
![](https://i.imgur.com/lzqcQ0S.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了一个没有任何标志位的TCP包
    

1. 靶机没有发送TCP包响应，无法区分其80端口打开/被过滤。
    

1. 但是靶机也没有发送ICMP数据包给攻击者主机，说明端口不是被过滤状态
    

1. 由此证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤测试

![](https://i.imgur.com/m6Bo5Xj.png)
![](https://i.imgur.com/aE2L1mJ.png)
![](https://i.imgur.com/ZgNzaCR.png)
分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了一个没有任何标志位的TCP包
    

1. 靶机返回给靶机一个ICMP数据包，且该包类型为type3
    

1. 证明了端口被过滤，这与课本中的扫描方法原理相符，实验成功。 

## UDP Scan ##

端口关闭测试

![](https://i.imgur.com/EDde2mC.png)

分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了UDP包
    

1. 靶机响应ICMP端口不可达错误type3和code3
   

1.  证明了端口关闭，这与课本中的扫描方法原理相符，实验成功。 

端口开启测试

![](https://i.imgur.com/hlx78br.png)

分析靶机抓到的包

    

1. 攻击者主机给靶机的80端口发送了UDP包
    

1. 靶机响应ipv4包
    

1. 由此证明了端口打开，这与课本中的扫描方法原理相符，实验成功。 

端口过滤测试

结果同端口关闭