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

由于资源的限制，所有区域的 Route Server 实例都会运行在具有“Service Hosting”能力的节点上，请放心，到达 Route Server 的延迟并不影响你到达其它参与者路由器的延迟。

Due to the limit of resource, all Route Server Instance in all regions will run on those nodes which have "Service Hosting" ability, don't worry, the latency to Route Server does not affect latency to other participants' routers.

[Maintenance information of SERNET-IX](https://maintenance.sherpherd.top)

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
  * 65535:666 (Well-Known Community, suggested. Blackhole, support only /32 for IPv4 and /64 for IPv6)
  * 4242422245:666 (Blackhole, support only /32 for IPv4 and /64 for IPv6)

Session Establishment: Separate IPv4 & IPv6 session over IXP network address

### Service Map
---
<div id="map"></div>
<script>
    var map = new L.Map("map", {
        center: new L.LatLng(34.307144, -226.40625),
        zoom: 2,
    });
    L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);
    var marker_dg = L.marker([23.019076, -246.258545]).addTo(map).bindPopup("<br><b>Dongguan, Guangdong Province, China</b><br>Service Hosting<br>Direct Peering<br>SERNET-IX Participant (China Mainland)<br><br>");
    var marker_hk = L.marker([22.248429, -245.808105]).addTo(map).bindPopup("<br><b>Hong Kong, China</b><br>Service Hosting<br>Direct Peering<br>SERNET-IX Participant (East Asia)<br>SERNET-IX Direct Connect Point (East Asia)<br><br>");
    var marker_kr = L.marker([37.527154, -232.998047]).addTo(map).bindPopup("<br><b>Seoul, South Korea</b><br>Service Hosting<br>Direct Peering<br>SERNET-IX Participant (East Asia)<br>SERNET-IX Direct Connect Point (East Asia)<br><br>");
    var marker_us1 = L.marker([33.979809, -118.190918]).addTo(map).bindPopup("<br><b>Los Angeles, CA, United States</b><br>Direct Peering<br>SERNET-IX Participant (North America)<br>SERNET-IX Direct Connect Point (North America)<br><br>");
    var marker_de = L.marker([50.092393, -351.298828]).addTo(map).bindPopup("<br><b>Frankfurt, Germany</b><br>Direct Peering<br>SERNET-IX Participant (Europe)<br>SERNET-IX Direct Connect Point (Europe)<br><br>");
</script>

### Operation Region
*   #### China Mainland
    * Status: Under Construction
    * RFS: TBD
    * Peering Policy: Only accept participants have existing DN42 node in China Mainland
    * IPv6 Network Block: fd06:7bc4:e3fa:7940::/64
    * IPv4 Network Block: 172.16.128.0/24
    * L2VPN Technology: Zerotier
  
*  #### East Asia
    * Status: Operational
    * RFS: 2023/07/19
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7941::/64
    * IPv4 Network Block: 172.16.129.0/24
    * L2VPN Technology: N2N

*  #### North America
    * Status: Operational
    * RFS: 2023/07/23
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7942::/64
    * IPv4 Network Block: 172.16.130.0/24
    * L2VPN Technology: N2N

*  #### Europe
    * Status: Under Construction
    * RFS: TBD
    * Peering Policy: Open
    * IPv6 Network Block: fd06:7bc4:e3fa:7943::/64
    * IPv4 Network Block: 172.16.131.0/24
    * L2VPN Technology: N2N

### SERNET-IX 服务协议（The Service Agreement of SERNET-IX）
---
[SERNET-IX 服务协议](Agreement_SERNET_IX_cn.html)

[The Service Aggrement of SERNET-IX](Agreement_SERNET_IX_en.html)