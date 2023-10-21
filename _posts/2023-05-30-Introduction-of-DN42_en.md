---
layout: post_dn42
title: Introduction of DN42
---

[中文版本](Introduction-of-DN42_cn.html)

# Overview of DN42
---
[DN42 Wiki Homepage](https://dn42.dev/Home)

dn42 is a big dynamic VPN, which employs Internet technologies (BGP, whois database, DNS, etc). Participants connect to each other using network tunnels (GRE, OpenVPN, WireGuard, Tinc, IPsec) and exchange routes thanks to the Border Gateway Protocol. Network addresses are assigned in the 172.20.0.0/14 range and private AS numbers are used (see registry) as well as IPv6 addresses from the ULA-Range (fd00::/8). <sup>[[1]](#ref1)</sup>

<br>
<br>

# The usage of DN42
---
dn42 can be used to learn networking and to connect private networks, such as hackerspaces or community networks. But above all, experimenting with routing in dn42 is fun!<sup>[[2]](#ref2)</sup>

## Experiment with routing technology
---
Participating in dn42 is primarily useful for learning routing technologies such as BGP, using a reasonably large network (> 1500 AS, > 1700 prefixes).

Since dn42 is very similar to the Internet, it can be used as a hands-on testing ground for new ideas, or simply to learn real networking stuff that you probably can't do on the Internet (BGP multihoming, transit). The biggest advantage when compared to the Internet: if you break something in the network, you won't have any big network operator yelling angrily at you.<sup>[[3]](#ref3)</sup>

## Connect hackerspaces
---
dn42 is also a great way to connect hacker spaces in a secure way, so that they can provide services to each other.

Have you ever wanted to SSH on your Raspberry Pi hosted at your local hacker space and had trouble doing so because of NAT? If your hacker space was using dn42, it could have been much easier.

Nowadays, most end-user networks use NAT to squeeze all those nifty computing devices behind a single public IPv4 address. This makes it difficult to provide services directly from a machine behind the NAT. Besides, you might want to provide some services to other hackerspaces, but not to anybody on the Internet.

dn42 solves this problem. By addressing your network in dn42, your devices can communicate with all other participants in a transparent way, without resorting to this ugly thing called NAT. Of course, this doesn't mean that you have to fully open your network to dn42: similarly to IPv6, you can still use a firewall. <sup>[[4]](#ref4)</sup>

<br>
<br>

# Citation
---
* <span id="ref1">1.DN42 Wiki Homepage, [https://dn42.dev/Home#about-dn42](https://dn42.dev/Home#about-dn42)</span>
* <span id="ref2">2.DN42 Wiki Homepage, [https://dn42.dev/Home#why-dn42](https://dn42.dev/Home#why-dn42)</span>
* <span id="ref3">3.DN42 Wiki Homepage, [https://dn42.dev/Home#experiment-with-routing-technology](https://dn42.dev/Home#experiment-with-routing-technology)</span>
* <span id="ref4">4.DN42 Wiki Homepage, [https://dn42.dev/Home#connect-hackerspaces](https://dn42.dev/Home#connect-hackerspaces)</span>