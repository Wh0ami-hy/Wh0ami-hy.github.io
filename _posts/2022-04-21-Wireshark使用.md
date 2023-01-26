---
layout: post   	
catalog: true 	
tags:
    - tool
---



## 介绍

### 用途

用户通常使用Wireshark来学习网络协议，分析网络问题，检测攻击和木马等。

### 缺点

Wireshark在数据包捕获和分析方面具有超强的能力，但是它不能修改和发送数据包

## 抓包

启动Wireshark后，在主界面会列出当前系统中所有的网卡信息

* 选择要监听的网卡，双击

![](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E7%BD%91%E5%8D%A1.jpg)

* 还有另一个入口就是上方的配置按钮

![配置按钮 ](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E9%85%8D%E7%BD%AE%E6%8C%89%E9%92%AE%20.jpg)

* 在软件的核心界面就是数据包列表，显示的列有序号、时间、源IP、目标IP、协议、长度、基本信息

![数据信息](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%95%B0%E6%8D%AE%E4%BF%A1%E6%81%AF-1612672457922.jpg)

* Wireshark使用不同的颜色对不同的协议做了区分，在视图菜单，我们可以找到和着色相关的命令。对话着色用来选择指定颜色对应的协议，着色分组列表用来隐藏非选中着色分组中的数据包，着色规则用来定义着色外观和包含的协议

![对话着色](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E5%AF%B9%E8%AF%9D%E7%9D%80%E8%89%B2.jpg)



## 包过滤

Wireshark提供了两种过滤器：捕捉过滤器和显示过滤器

### 捕获过滤器

捕捉过滤器是用来配置应该捕获什么样的数据包，在启动数据包捕捉之前就应该配置好

![捕获过滤器](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%8D%95%E8%8E%B7%E8%BF%87%E6%BB%A4%E5%99%A8.jpg)

#### 语法

![捕获过滤器语法](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%8D%95%E8%8E%B7%E8%BF%87%E6%BB%A4%E5%99%A8%E8%AF%AD%E6%B3%95.jpg)

Protocol（协议）

可能的值: ether, fddi, ip, arp, rarp, decnet, lat, sca, moprc, mopdl, tcp and udp.
如果没有特别指明是什么协议，则默认使用所有支持的协议

 Direction（方向）
可能的值: src, dst, src and dst, src or dst
如果没有特别指明来源或目的地，则默认使用 "src or dst" 作为关键字

 Host(s)
可能的值： net, port, host, portrange.
如果没有指定此值，则默认使用"host"关键字

 Logical Operations（逻辑运算）
可能的值：not, and, or
否("not")具有最高的优先级。或("or")和与("and")具有相同的优先级，运算时从左至右进行

例：

```
tcp dst port 3128

显示目的TCP端口为3128的封包。

ip src host 10.1.1.1

显示来源IP地址为10.1.1.1的封包。

host 10.1.2.3

显示目的或来源IP地址为10.1.2.3的封包。

src portrange 2000-2500

显示来源为UDP或TCP，并且端口号在2000至2500范围内的封包。

not imcp

显示除了icmp以外的所有封包。（icmp通常被ping工具使用）

src host 10.7.2.12 and not dst net 10.200.0.0/16

显示来源IP地址为10.7.2.12，但目的地不是10.200.0.0/16的封包。

(src host 10.4.1.12 or src net 10.6.0.0/16) and tcp dst portrange 200-10000 and dst net 10.0.0.0/8

注：

当使用关键字作为值时，需使用反斜杠"\"。"ether proto \ip" (与关键字"ip"相同)。这样写将会以IP协议作为目标。"ip proto \icmp" (与关键字"icmp"相同).这样写将会以ping工具常用的icmp作为目标。可以在"ip"或"ether"后面使用"multicast"及"broadcast"关键字。当您想排除广播请求时，"no broadcast"就会非常有用
```



### 显示过滤器

显示过滤器用来过滤已经捕获的数据包。在数据包列表的上方，有一个显示过滤器输入框，可以直接输入过滤表达式，也可以点击输入框右侧的表达式按钮，打开表达式编辑器，左侧框内是可供选择的字段。

![显示过滤器](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%98%BE%E7%A4%BA%E8%BF%87%E6%BB%A4%E5%99%A8-1612683520691.jpg)

#### 语法

格式：`Protocol.String1.String2 Comparison operator Value Logical Operations Other expression_r`

 Protocol，协议字段。支持的协议，从OSI 7层模型的2到7层都支持

 String1, String2 。协议的子类

![子协议](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E5%AD%90%E5%8D%8F%E8%AE%AE-1612684322327.jpg)

Comparison operators，比较运算符。可以使用6种比较运算符，逻辑运算符

![比较运算符](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%AF%94%E8%BE%83%E8%BF%90%E7%AE%97%E7%AC%A6-1612684785777.jpg)



![逻辑运算符](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E9%80%BB%E8%BE%91%E8%BF%90%E7%AE%97%E7%AC%A6.jpg)

注：逻辑异或是一种排除性的或。当其被用在过滤器的两个条件之间时，只有当且仅当其中的一个条件满足时，这样的结果才会被显示在屏幕上。

例：

```
"tcp.dstport 80 xor tcp.dstport 1025"

只有当目的TCP端口为80或者来源于端口1025（但又不能同时满足这两点）时，这样的封包才会被显示。

下面再通过一些实例来加深了解。

snmp || dns || icmp  

显示SNMP或DNS或ICMP封包。

ip.addr == 10.1.1.1

显示来源或目的IP地址为10.1.1.1的封包。

ip.src != 10.1.2.3 or ip.dst != 10.4.5.6

显示来源不为10.1.2.3或者目的不为10.4.5.6的封包。

ip.src != 10.1.2.3 and ip.dst != 10.4.5.6

显示来源不为10.1.2.3并且目的IP不为10.4.5.6的封包。

tcp.port == 25       

显示来源或目的TCP端口号为25的封包。

tcp.dstport == 25    

显示目的TCP端口号为25的封包。

tcp.flags    

显示包含TCP标志的封包。

tcp.flags.syn == 0x02

显示包含TCP SYN标志的封包。



在使用过滤器表达式编辑器的时候，如果过滤器的语法是正确的，表达式的背景呈绿色。如果呈红色，说明表达式有误。

生成表达式，点击Ok按钮，回到数据包列表界面
```

此外我们也可以通过选中数据包点击右键，来生成过滤器。

不同的选项，大家都可以尝试下，都是基本逻辑谓词的组合。比如选择“或选中”，可以组合多个数据包的条件

![其他过滤](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E5%85%B6%E4%BB%96%E8%BF%87%E6%BB%A4.jpg)

## 数据分析

![数据区](F:%5C%E7%AC%94%E8%AE%B0%5C%E5%8D%9A%E5%AE%A2%5C%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87%5C%E6%95%B0%E6%8D%AE%E5%8C%BA-1612685004232.jpg)

1区为详细信息显示区域，这个区域内对数据包按照协议字段做了较为详细的分析。2区为16进制数据区。结合1区和2区，再结合书本上的知识，我们就可以进行协议分析的研究和学习了

Frame:  物理层的数据帧概况

Ethernet II: 数据链路层以太网帧头部信息

Internet Protocol Version 4: 互联网层IP包头部信息

Transmission Control Protocol: 传输层T的数据段头部信息，此处是TCP

Hypertext Transfer Protocol: 应用层的信息，此处是HTTP协议

当我们点击1区的字段的时候，可以看到在2区对应的数据项

