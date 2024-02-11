---
layout: post_dn42
title: 用 BIRD 运行你的 MPLS 网络
---

# 引言
目前，网络上大多数在 Linux 上跑 MPLS 的教程是基于 FRR 的。因为在很长一段时间里，FRR 及其前身 Quagga 是在 Linux 上构建 MPLS 网络的不二之选，因为它们提供行业标准的 MPLS 相关协议实现（LDP，BGP-LU，BGP IPv4/IPv6 MPLS L3VPN，诸如此类），而彼时大多数其它路由套件甚至拿不出可用的 MPLS 支持。

在 DN42 中为多数人所使用的 BIRD 也在其最新版本（2.14）中添加了对 MPLS 的可用支持，现在 BIRD 也有可以感知 MPLS 并产生带 MPLS 标签的路由协议了。（尽管还是没有 LDP）[<sup>[1]</sup>](#c1)

在最新的 BIRD 2.0 用户指南中，新增了 MPLS 功能相关章节[<sup>[2]</sup>](#c2)，其节选内容如下：

> In BIRD, the whole process generally works this way: A MPLS-aware routing protocol (say BGP) receives
routing information including remote label. It produces a route with attribute mpls policy (p. 30) specifying
desired MPLS label policy (p. 18). Such route then passes the import filter (which could modify the MPLS
label policy or perhaps assign a static label) and when it is accepted, a local MPLS label is selected (according
to the label policy) and attached to the route, producing labeled route. When a new MPLS label is allocated,
the MPLS-aware protocol automatically produces corresponding MPLS route. When all labeled routes that
use specific local MPLS label are retracted, the corresponding MPLS route is retracted too. <br>
There are three important concepts for MPLS in BIRD: MPLS domains, MPLS tables and MPLS channels.
MPLS domain represents an independent label space, all MPLS-aware protocols are associated with some MPLS domain. It is responsible for label management, handling label allocation requests from MPLS-aware protocols. MPLS table is just a routing table for MPLS routes. Routers usually have one MPLS domain and one MPLS table, with Kernel protocol to export MPLS routes into kernel FIB. <br> 
MPLS channels make protocols MPLS-aware, they are responsible for keeping track of active FECs (and corresponding allocated labels), selecting FECs / local labels for labeled routes, and maintaining correspondence between labeled routes and MPLS routes.

如上文所述，目前 BIRD 的 BGP 实现是 MPLS 感知的了，可以用于分配和分发 MPLS 标签路由。

在这篇文章中，我将结合 BIRD 的官方文档为读者展示一个简单的使用 BIRD 运行的 MPLS VPN 网络实例的构建。

# 前置要求
* 除非有特别配置，你的节点必须是有完全独立运行的内核的（物理机，KVM 虚拟化，诸如此类），如此方可启用 MPLS 内核模块。
* 如果你使用 Vultr 的 VPS，由于未知原因，Vultr 自带的系统镜像对 MPLS 的支持有问题，在部署后请用官方 ISO 再装一遍你想要的操作系统。

# 目录
- [引言](#引言)
- [前置要求](#前置要求)
- [目录](#目录)
- [1 实验拓扑](#1-实验拓扑)
  - [1.1 节点配置](#11-节点配置)
- [2 前置工作](#2-前置工作)
  - [2.1 启用 MPLS 内核模块](#21-启用-mpls-内核模块)
  - [2.2 内核参数调整](#22-内核参数调整)
    - [2.2.1 为 MPLS 接口启用 MPLS 输入](#221-为-mpls-接口启用-mpls-输入)
  - [2.3 创建 VRF 并为接口分配 VRF](#23-创建-vrf-并为接口分配-vrf)
    - [2.3.1 调整客户侧接口的 MTU 以避免分片](#231-调整客户侧接口的-mtu-以避免分片)
  - [2.4 IP 地址和静态路由配置](#24-ip-地址和静态路由配置)
  - [2.5 安装 BIRD](#25-安装-bird)
    - [2.5.1 安装编译源码和构建软件包所需软件](#251-安装编译源码和构建软件包所需软件)
    - [2.5.2 克隆 BIRD 2.14 仓库](#252-克隆-bird-214-仓库)
    - [2.5.3 构建 BIRD 2.14 软件包](#253-构建-bird-214-软件包)
- [3 BIRD 配置](#3-bird-配置)
  - [3.1 基础配置](#31-基础配置)
    - [3.1.1 配置 Router ID](#311-配置-router-id)
    - [3.1.2 配置](#312-配置)
  - [3.2 配置 BGP](#32-配置-bgp)
  - [3.3 配置 MPLS L3VPN](#33-配置-mpls-l3vpn)
  - [3.4 完整的 BIRD 配置文件](#34-完整的-bird-配置文件)
    - [3.4.1 R1](#341-r1)
    - [3.4.2 R2](#342-r2)
    - [3.4.3 R3](#343-r3)
- [4 验证](#4-验证)
  - [4.1 检查 MPLS L3VPN 路由表](#41-检查-mpls-l3vpn-路由表)
  - [4.2 检查 PC1 和 PC2 之间的连接性](#42-检查-pc1-和-pc2-之间的连接性)
  - [4.3 检查默认 IPv4 路由表](#43-检查默认-ipv4-路由表)
- [5 引用](#5-引用)

# 1 实验拓扑
```

                 ----------------------------------------------------------
--------         |    --------            --------            --------    |         --------
|      |  eth0 ens20  |      | ens19 ens19|      | ens20 ens19|      |  ens20 eth0  |      |
| PC1  | O==========O |  R1  |O==========O|  R3  |O==========O|  R2  | O==========O | PC2  |
|      |         |    |      |            |      |            |      |    |         |      |
--------         |    --------            --------            --------    |         --------
                 |                                                        |
                 |         Confederation ASN: 100                         |
                 |                    R1 ASN: 64512                       |
                 |                    R2 ASN: 64513                       |
                 |                    R3 ASN: 64514                       |
                 |                        RT: 100:500                     |
                 |                     R1 RT: 203.0.113.1:500             |
                 |                     R2 RT: 203.0.113.2:500             |
                 |                                                        |
                 ----------------------------------------------------------
                 |                    Address Assignment                  |
                 ----------------------------------------------------------
                 |                                                        |
                 |                  eth0@PC1: 192.168.1.2/24              |
                 |                  ens20@R1: 192.168.1.1/24              |
                 |                                (vrf blue)              |
                 |                     lo@R1: 203.0.113.1/32              |
                 |                  ens19@R1: 203.0.113.1/32              |
                 |                     lo@R3: 203.0.113.3/32              |
                 |                  ens19@R3: 203.0.113.3/32              |
                 |                  ens20@R3: 203.0.113.3/32              |
                 |                     lo@R2: 203.0.113.2/32              |
                 |                  ens19@R2: 203.0.113.2/32              |
                 |                  ens20@R2: 192.168.2.1/24              |
                 |                                (vrf blue)              |
                 |                  eth0@PC2: 192.168.2.2/24              |
                 |                                                        |
                 ----------------------------------------------------------

```

## 1.1 节点配置
PC1、R1、R2 和 PC2 均运行 Debian 12 操作系统。R1 和 R2 均安装本文写成时最新版本的 BIRD ，即 BIRD 2.14。

**注意：记得为 R1，R2 和 R3 添加第三个接口用于访问互联网下载 BIRD 安装包或源码和编译安装所需软件**

# 2 前置工作
## 2.1 启用 MPLS 内核模块
在 R1 和 R2 下以 root 身份运行如下命令：
```
modprobe mpls_router
modprobe mpls_iptunnel
modprobe mpls_gso
```
## 2.2 内核参数调整
在 R1 ，R2 和 R2 下以 root 身份运行如下命令以调整 IP 路由及 MPLS 相关内核参数，使之能正常工作[<sup>[3]</sup>](#c3)：
```
cat >/etc/sysctl.d/90-mpls-router.conf <<EOF
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
net.ipv4.conf.all.rp_filter=0
net.mpls.platform_labels=1048575
net.ipv4.tcp_l3mdev_accept=1
net.ipv4.udp_l3mdev_accept=1
net.mpls.conf.lo.input=1
EOF
sysctl -p /etc/sysctl.d/90-mpls-router.conf
```
### 2.2.1 为 MPLS 接口启用 MPLS 输入
每个需要传输 MPLS 流量的接口都要启用 MPLS 输入，在 R1 和 R2 下以 root 身份运行如下命令以启用它们的 ens19 接口的 MPLS 输入：
```
sysctl -w net.mpls.conf.ens19.input=1
```
在 R3 上如法炮制：
```
sysctl -w net.mpls.conf.ens19.input=1
sysctl -w net.mpls.conf.ens20.input=1
```
**注意：每个需要传输 MPLS 流量的接口都需要这样的配置**
## 2.3 创建 VRF 并为接口分配 VRF
在 R1 和 R2 下以 root 身份运行如下命令以创建名为 blue 的 VRF 接口：
```
ip link add blue type vrf table 500
ip link set blue up
```
在 R1 和 R2 下以 root 身份运行如下命令以将 ens20 分配至 VRF blue 并启用接口：
```
ip link set ens20 master blue up
```
### 2.3.1 调整客户侧接口的 MTU 以避免分片
在实际操作中，提高核心网络链路的 MTU 总比调低客户侧接口的 MTU 难，而使用 MPLS 会有额外的报文头开销（每个标签占用4字节），这使得大包可能在进入 MPLS 网络时被分片。为了避免分片，我们要适当调低客户侧接口的 MTU 。

在 PC1 和 PC2 下以 root 身份运行如下命令以调整 eth0 的 MTU 并启用接口：
```
ip link set eth0 mtu 1492 up
```
在 R1 和 R2 下以 root 身份运行如下命令以调整 ens20 的 MTU：
```
ip link set ens20 mtu 1492
```
## 2.4 IP 地址和静态路由配置
在如下节点以 root 身份执行对应的命令以完成配置。

R1：
```
ip addr add 203.0.113.1/32 dev lo
ip addr add 203.0.113.1/32 dev ens19 peer 203.0.113.3/32
ip addr add 192.168.1.1/24 dev ens20
```
R2：
```
ip addr add 203.0.113.2/32 dev lo
ip addr add 203.0.113.2/32 dev ens19 peer 203.0.113.3/32
ip addr add 192.168.2.1/24 dev ens20
```
R3：
```
ip addr add 203.0.113.3/32 dev lo
ip addr add 203.0.113.3/32 dev ens19 peer 203.0.113.1/32
ip addr add 203.0.113.3/32 dev ens20 peer 203.0.113.2/32
```
PC1：
```
ip addr add 192.168.1.2/24 dev eth0
ip route add 192.168.2.0/24 via 192.168.1.1
```
PC1：
```
ip addr add 192.168.2.2/24 dev eth0
ip route add 192.168.1.0/24 via 192.168.2.1
```
## 2.5 安装 BIRD
只是从源码编译安装的 BIRD 是不完全的，缺失系统服务文件、文档之类的内容。

想要完整的 BIRD 就要构建软件包然后安装。

如果想要省去自己构建软件包的过程，你可以在这里下载到我预先构建好的 deb 软件包：

[https://drive.google.com/drive/folders/1DUaFJgZGsEXI-RlreNxCG9mnERiAkIVB?usp=drive_link](https://drive.google.com/drive/folders/1DUaFJgZGsEXI-RlreNxCG9mnERiAkIVB?usp=drive_link)

如提示有依赖缺失，遵照提示安装缺失软件包即可。

如果想要自己构建 BIRD 2.14 的软件包，请阅读下文。

准备一台同样运行 Debian 12 的编译机器，配置不用太高，我的是4核4GB内存，然后在上面进行下文所示的操作。

### 2.5.1 安装编译源码和构建软件包所需软件
```
apt install -y git linuxdoc-tools autoconf build-essential libssh-dev libreadline-dev libncurses-dev flex bison checkinstall debhelper docbook-xsl libssh-gcrypt-dev quilt xsltproc linuxdoc-tools-latex texlive-latex-extra
```
```
pipx install apkg
```
### 2.5.2 克隆 BIRD 2.14 仓库
克隆 BIRD 2.14 的仓库：
```
git clone --branch v2.14 https://gitlab.nic.cz/labs/bird.git
```
### 2.5.3 构建 BIRD 2.14 软件包
进入 bird 然后执行如下命令：
```
apkg build
```
在构建完成后，apkg 会有类似的提示：
```
built 3 packages in: pkg/pkgs/debian-12/bird2_2.14.1707409394.0e1fbaa5-cznic.1
```
构建好的软件包就放在提示所示的目录中，自行取用。

# 3 BIRD 配置
由于 BIRD 目前尚没有通过 IGP 拓扑分发 MPLS 标签路由的实现，我们使用 BGP-LU 分发 MPLS 标签路由。
## 3.1 基础配置
### 3.1.1 配置 Router ID
配置一个静态 Router ID 总不是坏事。

R1：
```
router id 203.0.113.1;
```
R2、R3 如法炮制。

### 3.1.2 添加 MPLS Domain 和各种表
此处 R1 配置，其它节点如法炮制：
```
mpls domain mpls_dom;

mpls table bgp_mpls_table;

vpn4 table bgp_vpn4;

ipv4 table vrf_blue4;
```

### 3.1.3 添加 MPLS 和 VRF 相关协议及配置
以 R1 配置为例：
```
protocol kernel krt_mpls {
	mpls {
		table bgp_mpls_table;
		export all;
	};
}

protocol kernel vrf_blue_4 { # R3 无需添加该协议，因为它不运行任何 VRF 实例
	vrf "blue";
	ipv4 {
		table vrf_blue4;
		export all;
		import all;
	};
	kernel table 500;
}

protocol static {
	ipv4;
	route 203.0.113.1/32 reject; # 静态注入直连路由，用于在 BGP 中发布
}

protocol static {                # 同样地，R3 无需添加
	ipv4 { table vrf_blue4; };
	route 192.168.1.0/24 reject;
}
```

## 3.2 配置 BGP
以 R1 的配置为例：
```
protocol bgp r3 {
	local 203.0.113.1 as 64512;
	neighbor 203.0.113.3 as 64514;
	confederation 100;
	confederation member;
	ipv4 mpls { # 启用 IPv4 Labeled Unicast 通道，打通 MPLS 节点之间的 MPLS 可达性
		import all;
		export all;
	};
	vpn4 mpls { # 启用 VPNv4 通道，承载 IPv4 VPN 路由
		table bgp_vpn4;
		import all;
		export all;
	};
	mpls {
		label policy aggregate;
	};
}
```

## 3.3 配置 MPLS L3VPN 与 VRF 实例之间的绑定
R3 无需这些配置，因为 R3 不运行任何 VRF 实例。

以 R1 的配置为例：
```
protocol l3vpn vpn_blue4 {
	vrf "blue";
	ipv4 { table vrf_blue4; }; # 绑定 VRF 路由表
	vpn4 { table bgp_vpn4; }; # 绑定 VPNv4 路由表
	mpls { label policy vrf; };

	rd 203.0.113.1:500;
	import target [(rt,100,500)]; # 定义从绑定的 VPNv4 路由表里取用的路由所有的 RT
	export target [(rt,100,500)]; # 定义输出到绑定的 VPNv4 路由表里的本 VRF 实例路由附加的 RT
}
```

## 3.4 完整的 BIRD 配置文件
### 3.4.1 R1
```
log syslog all;

router id 203.0.113.1;

mpls domain mpls_dom;

mpls table bgp_mpls_table;

vpn4 table bgp_vpn4;

ipv4 table vrf_blue4;

protocol device {

}

protocol direct {
	disabled; # Disable by default
	ipv4;			# Connect to default IPv4 table
	ipv6;			# ... and to default IPv6 table
}

protocol kernel {
	ipv4 {			# Connect protocol to IPv4 table by channel
	      export all;	# Export to protocol. default is export none
	      import all;
	};
}

protocol kernel {
	ipv6 { export all; };
}

protocol kernel krt_mpls {
	mpls {
		table bgp_mpls_table;
		export all;
	};
}

protocol kernel vrf_blue_4 {
	vrf "blue";
	ipv4 {
		table vrf_blue4;
		export all;
		import all;
	};
	kernel table 500;
}

protocol static {
	ipv4;			# Again, IPv4 channel with default options
	route 203.0.113.1/32 reject;
}

protocol static {
	ipv4 { table vrf_blue4; };
	route 192.168.1.0/24 reject;
}

protocol bgp r3 {
	local 203.0.113.1 as 64512;
	neighbor 203.0.113.3 as 64514;
	confederation 100;
	confederation member;
	ipv4 mpls {
		import all;
		export all;
	};
	vpn4 mpls {
		table bgp_vpn4;
		import all;
		export all;
	};
	mpls {
		label policy aggregate;
	};
}

protocol l3vpn vpn_blue4 {
	vrf "blue";
	ipv4 { table vrf_blue4; };
	vpn4 { table bgp_vpn4; };
	mpls { label policy vrf; };

	rd 203.0.113.1:500;
	import target [(rt,100,500)];
	export target [(rt,100,500)];
}

```
### 3.4.2 R2
```
router id 203.0.113.2;

log syslog all;

mpls domain mpls_dom;

mpls table bgp_mpls_table;

vpn4 table bgp_vpn4;

ipv4 table vrf_blue4;

protocol device {
}

protocol direct {
	disabled; # Disable by default
	ipv4;			# Connect to default IPv4 table
	ipv6;			# ... and to default IPv6 table
}

protocol kernel krt_mpls {
        mpls {
                table bgp_mpls_table;
                export all;
        };
}

protocol kernel vrf_blue_4 {
        vrf "blue";
        ipv4 {
                table vrf_blue4;
                export all;
                import all;
        };
        kernel table 500;
}

protocol kernel {
	ipv4 {			# Connect protocol to IPv4 table by channel
	      export all;	# Export to protocol. default is export none
	};
}

protocol kernel {
	ipv6 { export all; };
}

protocol static {
	ipv4;			# Again, IPv4 channel with default options
	route 203.0.113.2/32 reject;
}

protocol static {
	ipv4 { table vrf_blue4; };
	route 192.168.2.0/24 reject;
}

protocol bgp r3 {
        local 203.0.113.2 as 64513;
        neighbor 203.0.113.3 as 64514;
        confederation 100;
        confederation member;
        ipv4 mpls {
                import all;
                export all;
        };
        vpn4 mpls {
                table bgp_vpn4;
                import all;
                export all;
        };
        mpls {
                label policy aggregate;
        };
}

protocol l3vpn vpn_blue4 {
        vrf "blue";
        ipv4 { table vrf_blue4; };
        vpn4 { table bgp_vpn4; };
        mpls { label policy vrf; };

        rd 203.0.113.2:500;
        import target [(rt,100,500)];
        export target [(rt,100,500)];
}

```
### 3.4.3 R3
```
log syslog all;

router id 203.0.113.3;

mpls domain mpls_dom;

mpls table bgp_mpls_table;

vpn4 table bgp_vpn4;

protocol device {
}

protocol direct {
	disabled;		# Disable by default
	ipv4;			# Connect to default IPv4 table
	ipv6;			# ... and to default IPv6 table
}

protocol kernel {
	ipv4 {			# Connect protocol to IPv4 table by channel
	      export all;	# Export to protocol. default is export none
	};
}

protocol kernel {
	ipv6 { export all; };
}

protocol kernel krt_mpls {
	mpls {
		table bgp_mpls_table;
		export all;
	};
};

protocol static {
	ipv4;			# Again, IPv4 channel with default options

}

protocol bgp r1 {
        local 203.0.113.3 as 64514;
        neighbor 203.0.113.1 as 64512;
        confederation 100;
        confederation member;
        ipv4 mpls {
		next hop self;
                import all;
                export all;
        };
        vpn4 mpls {
		next hop self;
                table bgp_vpn4;
                import all;
                export all;
        };
        mpls {
                label policy aggregate;
        };
}

protocol bgp r2 {
        local 203.0.113.3 as 64514;
        neighbor 203.0.113.2 as 64513;
        confederation 100;
        confederation member;
        ipv4 mpls {
		next hop self;
                import all;
                export all;
        };
        vpn4 mpls {
		next hop self;
                table bgp_vpn4;
                import all;
                export all;
        };
        mpls {
                label policy aggregate;
        };
}

```

# 4 验证
## 4.1 检查 VPNv4 路由表
R1：
```
bird> show route table bgp_vpn4
Table bgp_vpn4:
203.0.113.2:500 192.168.2.0/24 mpls 1001 unicast [r3 23:20:55.236] * (100) [AS64513i]
        via 203.0.113.3 on ens19 mpls 1002
203.0.113.1:500 192.168.1.0/24 mpls 1002 unicast [vpn_blue4 22:58:48.918] * (120/0)
        dev blue
bird>
```
R2：
```
bird> show route table bgp_vpn4
Table bgp_vpn4:
203.0.113.2:500 192.168.2.0/24 mpls 1001 unicast [vpn_blue4 23:20:55.219] * (120/0)
        dev blue
203.0.113.1:500 192.168.1.0/24 mpls 1002 unicast [r3 22:58:56.352] * (100) [AS64512i]
        via 203.0.113.3 on ens19 mpls 1003
bird>
```
## 4.2 检查默认 IPv4 路由表
R1：
```
bird> show route table master4
Table master4:
203.0.113.2/32 mpls 1000 unicast [r3 22:58:56.355] * (100) [AS64513i]
        via 203.0.113.3 on ens19 mpls 1000
203.0.113.1/32       unreachable [static1 22:33:27.446] * (200)
bird>
```
R2：
```
bird> show route table master4
Table master4:
203.0.113.2/32       unreachable [static1 22:32:38.874] * (200)
203.0.113.1/32 mpls 1000 unicast [r3 22:58:56.352] * (100) [AS64512i]
        via 203.0.113.3 on ens19 mpls 1001
bird>
```
## 4.3 检查 VRF 路由表
R1：
```
bird> show route table vrf_blue4
Table vrf_blue4:
192.168.1.0/24       unreachable [static2 22:58:48.918] * (200)
192.168.2.0/24       unicast [vpn_blue4 23:20:55.236] * (80/0)
        via 203.0.113.3 on ens19 mpls 1002
bird>
```
R2：
```
bird> show route table vrf_blue4
Table vrf_blue4:
192.168.1.0/24       unicast [vpn_blue4 23:20:55.219] * (80/0)
        via 203.0.113.3 on ens19 mpls 1003
192.168.2.0/24       unreachable [static2 22:54:38.777] * (200)
bird>
```
## 4.4 检查 PC1 和 PC2 之间的连接性
PC1：
```
root@pc1:~# ping -c 4 192.168.2.2
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_seq=1 ttl=61 time=5.53 ms
64 bytes from 192.168.2.2: icmp_seq=2 ttl=61 time=5.03 ms
64 bytes from 192.168.2.2: icmp_seq=3 ttl=61 time=3.73 ms
64 bytes from 192.168.2.2: icmp_seq=4 ttl=61 time=5.97 ms

--- 192.168.2.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 3.729/5.063/5.965/0.838 ms
root@pc1:~# traceroute 192.168.2.2
traceroute to 192.168.2.2 (192.168.2.2), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)  5.787 ms  6.165 ms *
 2  * * *
 3  * * *
 4  192.168.2.2 (192.168.2.2)  36.865 ms  37.489 ms  44.775 ms
root@pc1:~#
```
PC2：
```
root@pc2:~# ping -c 4 192.168.1.2
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
64 bytes from 192.168.1.2: icmp_seq=1 ttl=61 time=21.7 ms
64 bytes from 192.168.1.2: icmp_seq=2 ttl=61 time=4.35 ms
64 bytes from 192.168.1.2: icmp_seq=3 ttl=61 time=13.6 ms
64 bytes from 192.168.1.2: icmp_seq=4 ttl=61 time=4.67 ms

--- 192.168.1.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 4.352/11.098/21.731/7.181 ms
root@pc2:~# traceroute 192.168.1.2
traceroute to 192.168.1.2 (192.168.1.2), 30 hops max, 60 byte packets
 1  192.168.2.1 (192.168.2.1)  17.272 ms  17.125 ms  17.175 ms
 2  * * *
 3  * * *
 4  192.168.1.2 (192.168.1.2)  27.517 ms  27.945 ms  32.354 ms
root@pc2:~#
```

# 5 引用
<span id="c1">1. BIRD Team. (2023, October 7). _News Archive_. bird.network.cz. [https://bird.network.cz/?o_news/](https://bird.network.cz/?o_news/)</span>

<span id="c2">2. BIRD Team. (2023, October 7). BIRD 2.0 User’s Guide. _MPLS_, 9-10. [https://bird.network.cz/download/bird-doc-2.14.tar.gz](https://bird.network.cz/download/bird-doc-2.14.tar.gz)</span>

<span id="c3">3. James Swineson. (2020, February 22). _Use Linux as an MPLS Router_. blog.swineson.me. [https://blog.swineson.me/en/use-linux-as-an-mpls-router/](https://blog.swineson.me/en/use-linux-as-an-mpls-router/)</span>