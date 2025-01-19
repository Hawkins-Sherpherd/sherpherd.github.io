---
layout: post_dn42
title: "使用 Linux 主机进行三层交叉互联"
---

[PDF 文档](https://1drv.ms/b/c/c11905f4033be9a6/EdRZkw-1CblIoOp9fiRFRAUBjPJfEoLHYFKDRg_5pDA3Cw?e=KyV7jc)

你好，你有过这样的愿景吗？

你的企业级设备可以直接和 DN42 建立 eBGP 互联，并且用的还是 Wireguard 协议。

从占据好几排机柜的核心路由器集群，到小只的桌面防火墙；从崭新的，每秒吞噬几 Tb 流量的交换机，再到90年代的古老路由器……

现在，它们可真正成为一员，成为 DN42 真实的一员，不再躲在软路由后面。

只要一台小小的，运行 iproute2，iptables 和 Wireguard 的机器，这些都可以成为真实。

# 案例 1：以太网与三层隧道的交叉连接

__免责声明：这并非真正的交叉连接__

```
 一对真实的交叉连接应当是一对只执行转发从一个端口传入的数据包到另一个端口的点对点连接。没有路由决策和数据包修改存在其中。
```

## 限制
* 无法传输组播流量
* 无法使用 IPv6 link-local 地址

## 步骤
* 在你的企业级设备上设置指向 Linux 机器的静态 ARP
* 在 Linux 机器上关闭与企业级设备互联的以太网接口的 ARP 功能
* 在 Linux 机器上设置 PBR
* 在 Linux 机器上通过 iptables 设置 TTL 修改
* 像以前那样建立 Peer（该方式无法使用 IPv6 link-local）

操作示例在 PDF 文档中有，敬请下载并阅读。