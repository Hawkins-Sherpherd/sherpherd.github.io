---
layout: post_dn42
title: "Layer 3 Cross-Connect with Linux Box"
---

[PDF Document](https://1drv.ms/b/c/c11905f4033be9a6/EYIvfpTlZoNFuNV4UNbHUJIB_rI_kc4kqL_gEG4qj_WE-g?e=ivCEXi)

Hello, have you ever imagine, your enterprise gears can talk eBGP with DN42 directly using Wireguard protocol one day?

From chunky core router cluster occupied rows of rack, to slim desktop firewall; from modern switch devours couple terabits per second, to vintage router from 90s......

Now they can all be the one, be the one of real element of DN42, no longer hiding behind commodity software routers.

With a small Linux box running iproute2, iptables and Wireguard, this can be true.

# Case 1: Ethernt to layer 3 tunnel xconnect

__Disclaimer: THIS IS NOT A REAL XCONNECT__

```
A real xconnect should just forward ingress packet from one port to 
another on a point-to-point connection. No routing decision nor packet 
mangling within.
```

## LIMITATIONS
* No multicast
* No IPv6 link-local

## PROCEDURES
* Set static ARP on your enterprise gear toward Linux box doing xconnect
* Turn off ARP on the ethernet interface which connected to your enterprise gear on the linux box doing xconnect
* Setup PBR on the linux box doing xconnect
* Setup TTL mangling with iptables on the linux box doing xconnect
* Peer as usual (No IPv6 link-local with this approach)

The opeartion example is shown in the PDF document, feel free to download and read.

