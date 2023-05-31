---
layout: post
title: DN42 介绍
---

[English Version](Introduction-of-DN42_en.html)

# DN42 总述
---
[DN42 Wiki 主页](https://dn42.dev/Home)

DN42 是一个用于施展互联网技术（BGP，whois，数据库，DNS，诸如此类）的大型动态 VPN 网络。参与者彼此通过网络隧道（GRE，OpenVPN，Wireguard，Tinc，IPSec）连接并通过边界网关协议（BGP）交换他们的路由。IPv4 地址在 172.20.0.0/14 的范围内分配，使用私有自治系统号码（参见 registry），IPv6 地址也从 ULA 范围（fd00::/8）里选取。<sup>[[1]](#ref1)</sup>

<br>
<br>

# DN42 的用途
---
DN42 可用于学习网络工程和连接私有网络，如“黑客空间”或社群网络。最重要的是，在 DN42 里做路由实验真的很有意思！<sup>[[2]](#ref2)</sup>

## 实验路由技术
---
参与 DN42 网络最实用的好处便是利用这个大规模的网络（> 1500 个自治系统，> 1700 条前缀）学习像 BGP 这样的路由技术。

由于 DN42 与互联网非常相似，它还可作为你可以上手测试新东西的试验场，或是单纯学习那些你可能没法在互联网上做到的真实网络操作（BGP 多归接入，互联网流量转接）的地方。与互联网相比的最大的好处便是：如果你搞砸了一些东西，不会有任何大型网络运营商对你愤怒地骂街。<sup>[[3]](#ref3)</sup>

## 连接“黑客空间”
---
DN42 还是一个安全连接“黑客空间”的好方法，这样它们便可向彼此提供服务。

你想要对放在你本地的“黑客空间”里的树莓派进行 SSH 连接，但是却被 NAT 所困扰？如果你的“黑客空间”使用 DN42，那么这些就会容易许多。

当下，大多数终端用户网络使用 NAT 以将这些好好的计算服务挤到一个公共的 IPv4 地址上。这令得从一台位于 NAT 之后的主机想要直接提供服务相当困难。另外，你也可能只是想要向其它的“黑客空间”提供一些服务，而非向整个互联网提供服务。

DN42 解决了这些问题。通过将你的网络连接到 DN42，你的设备便可与其它所有参与者直接通讯，而无需依靠 NAT 这丑恶的造物。当然，这并不意味着你就要把你的网络完全向 DN42 敞开了：和 IPv6 类似，你还是能用防火墙。<sup>[[4]](#ref4)</sup>

<br>
<br>

# 引用
---
* <span id="ref1">1.DN42 Wiki Homepage, [https://dn42.dev/Home#about-dn42](https://dn42.dev/Home#about-dn42)</span>
* <span id="ref2">2.DN42 Wiki Homepage, [https://dn42.dev/Home#why-dn42](https://dn42.dev/Home#why-dn42)</span>
* <span id="ref3">3.DN42 Wiki Homepage, [https://dn42.dev/Home#experiment-with-routing-technology](https://dn42.dev/Home#experiment-with-routing-technology)</span>
* <span id="ref4">4.DN42 Wiki Homepage, [https://dn42.dev/Home#connect-hackerspaces](https://dn42.dev/Home#connect-hackerspaces)</span>