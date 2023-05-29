---
layout: page
title: SERNET-IX
ref: ix
order: 2
---

# SERNET-IX
---
SERNET-IX 是在世界范内运作的一组致力于集中 DN42 BGP 会话（而非数据平面）的 IXP 。和传统的 IXP 不同，SERNET-IX 仅对 BGP 会话做集中，而将数据平面交给 P2P 的 L2VPN 技术（如 N2N，Zerotier），令整个互联网成为一台超大号的 IX 交换机。

SERNET-IX is a group of IXP concentrates DN42 BGP session but not data plane. Different from traditional IXP, SERNET-IX only concentrates BGP sessions. With the P2P L2VPN technology (N2N, Zerotier, etc.), SERNET-IX make whole Internet a big IX switch.

[Maintainance information of SERNET-IX](https://maintainance.sherpherd.top)

### 加入 SERNET-IX 的好处（The Benifit of joining in SERNET-IX）
---
  * 只需和 Route Server 进行 Peer，便可获得许多来自其它参与者的路由。（One BGP session to Route Server, many routes from other participants.）
  * 点对点通信，无需担忧 IX 出口带宽不足。(Point to point communication, no need of concerning lack bandwidth of IX.)

### SERNET-IX 网络信息（SERNET-IX Network Information）
---
[SERNET-IX 参与者信息](Participants_cn.html)

[Information of SERNET-IX Participants](Participants_en.html)

AS Number: 4242422245

Supported Communities:
  * 65535:666 (Well-Known Community, suggested. Blackhole, support only /32 for IPv4 and IPv6 address block equals to or smaller than /64)
  * 4242422245:666 (Blackhole, support only /32 for IPv4 and IPv6 address block equals to or smaller than /64)

Session Establishment: Separate IPv4 & IPv6 session over IXP network address

## Operation Region
*   #### China Mainland
    * Status: Testing
    * Time of Open peering: TBD
    * Peering Policy: Only accept participants have existing DN42 node in China Mainland
    * IPv6 Network Block: fd06:7bc4:e3fa:7940::/64
    * IPv4 Network Block: 172.16.128.0/24
    * Route Server: rs1.cn.ix.sernet.dn42(Dongguan ,Guangdong Province)
    * L2VPN Technology: Zerotier
  
*  #### East Asia
    * Status: Testing
    * Time of Open peering: TBD
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7941::/64
    * IPv4 Network Block: 172.16.129.0/24
    * Route Servers: rs1.hk.ix.sernet.dn42(Hong Kong), rs2.kr.ix.sernet.dn42(Seoul, South Korea)
    * L2VPN Technology: N2N

*  #### North America
    * Status: Under Construction
    * Time of Open peering: TBD
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7942::/64
    * IPv4 Network Block: 172.16.130.0/24
    * Route Server: rs1.us.ix.sernet.dn42(Los Angeles, CA, United States)
    * L2VPN Technology: N2N

*  #### Europe
    * Status: Under Construction
    * Time of Open peering: TBD
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7943::/64
    * IPv4 Network Block: 172.16.131.0/24
    * Route Server: rs1.de.ix.sernet.dn42(Frankfurt, Germany)
    * L2VPN Technology: N2N

### SERNET-IX 服务协议（The Service Agreement of SERNET-IX）
---
[SERNET-IX 服务协议](Agreement_SERNET_IX_cn.html)

[The Service Aggrement of SERNET-IX](Agreement_SERNET_IX_en.html)