# TCP+HTTP

&emsp;&emsp;&ensp;目的：介绍从浏览器输入链接开始，到接收全部数据为止。在应用层和传输层上的全过程。

## 目标简介

&emsp;&emsp;&ensp;以 ${wireshark}$ 抓包实例，以现实生活中到的一个实际例子，来学习从浏览器点击 ${web}$ 链接开始，到接收到 ${web}$ 服务器传回的数据为的全部过程。
&emsp;&emsp;&ensp;以访问百度服务器链接 ${www.baidu.com}$ 为例：

<div style=" margin: 0 auto; max-width: 40%;">
<img src="image.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;为了方便大家学习，我将网络上的基本设备画成下图所示的网络结构图。其中 ${H1}$ 为我的主机，${H2}$ 为同一个局域网内的主机。${R1}$ 为局域网的网关。${DNS}$ 服务器采用中国通用 ${DNS}$ 服务器。**所以 ${DNS}$ 服务器并未和主机 ${H1}$ 在同一个局域网中**。

<div style=" margin: 0 auto; max-width: 70%;">
<img src="image-1.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;通过 ${wget}$ 指令，获取百度门户网站 ${www.baidu.com}$ 的 ${html}$ 文本文件（不包含图片）的报文交互过程如下所示：

<div style=" margin: 0 auto; max-width: 90%;">
<img src="image-2.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;从输入 ${www.baidu.com}$ 到浏览器，点击回车开始，到主机收到全部数据为止。主要经过以下几个步骤：
&emsp;&emsp;&ensp;由于 ${DNS}$ 服务器与主机**不在一个局域网**内，所以需要先获取网关的 ${MAC}$ 地址。
&emsp;&emsp;&ensp;是否需要获取网关的 ${IP}$ 地址？主机是如何知道网关的 ${IP}$ 地址的？
&emsp;&emsp;&ensp;主机在连接上该局域网获取自身 ${IP}$ 地址是通过使用 ${DHCP}$ 协议的，${DHCP}$ 服务器返回的最后一个协议报文 ${DHCP\quad Acknowledge}$ 会携带当前局域网的一些必要信息，其中就有**该主机的默认网关 ${IP}$ 地址和子网掩码**。

<div style=" margin: 0 auto; max-width: 30%;">
<img src="image-3.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;${TCP}$ 段的 ${MSS}$：${TCP}$ 报文的数据部分可以达到的最大长度。假设 ${TCP}$ 的 ${MSS}$ 的大小为 ${1460B}$。

<div style=" margin: 0 auto; max-width: 90%;">
<img src="image-4.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;${web}$ 服务器不知道主机 ${H1}$ 的 ${MSS}$ 是多少，同理主机 ${H1}$ 也不知道 ${web}$ 服务器的 ${MSS}$ 是多少。如上图中的第 ${8}$ 和 ${9}$ 条所示，会在 ${TCP}$ 的**第一次拍手和第二次拍手时传递各自的 ${MSS}$**。
&emsp;&emsp;&ensp;在传输层就把应用层需要发送的数据进行切分，每一段数据部分的大小是 ${MSS}$，每一段再封装成一个 ${IP}$ 数据报，再封装成以太网帧进行发送，这样做的好处是什么？
&emsp;&emsp;&ensp;即使应用层传来特别大的文件，按照 ${MSS}$ 分割成几个 ${TCP}$ 报文段，每个 ${TCP}$ 报文段封装的 ${IP}$ 数据报的标识字段都是不一致的。一旦有一个 ${IP}$ 数据报发生丢失，通过后续接收的 ${IP}$ 数据报解析 ${TCP}$ 的字段部分，推断到底是哪段发生了丢失，对该段进行重传操作。
&emsp;&emsp;&ensp;若传输层使用的是 ${UDP}$，假设应用层传来 ${3000B}$ 的数据，是否会存在一个 ${MSS}$ 机制？
&emsp;&emsp;&ensp;不会，${UDP}$ 会直接封装成一个 ${IP}$ 数据报，只能将该 ${IP}$ 数据报分片。如果此时某个分片丢失了，只能将所有已收到的 ${IP}$ 数据报丢弃，而不能像 ${TCP}$ 那样重传某报文段。
&emsp;&emsp;&ensp;**域名输入浏览器后续流程**：百度的域名 ${www.baidu.com}$ 输入浏览器后，首先要将域名转换成 ${IP}$ 地址，**如果转换成 ${IP}$ 地址就必须访问 ${DNS}$ 域名服务器（本地域名服务器）**。也就是说，第一步是先将域名解析的 ${IP}$ 数据包发送给 ${DNS}$ 服务器（本地域名服务器）。

<div style=" margin: 0 auto; max-width: 90%;">
<img src="image-5.png" alt="Alt text" style="margin-top: 0; margin-bottom: 10px;" align="center">
</div>

&emsp;&emsp;&ensp;由图可知，本地域名服务器并不在子网 ${192.168.31.0/24}$ 中，所以该域名解析服务**要经过路由器转发到服务器 ${114.114.114.114}$**。

## ARP协议实例

## DNS协议实例

## TCP连接和断开实例

## web请求和接收实例
