---
layout: page
title: Agreement of SERNET-IX
---

# Introduction
---
SERNET-IX is operated by Hawkins Sherpherd. SERNET-IX itself DOES NOT provide transit bandwidth to DN42 Network. SERNET-IX is a Layer-2 DN42 IXP primarily for routing and switching regional DN42 traffic where the specific SERNET-IX instance exists. However, SERNET-IX also accept inter-region participant, one may connect a SERNET-IX instance from other region if there is no instance of SERNET-IX. (With exception, see "Regions unsupported inter-region connection")

# Requirements for Participants to Join SERNET-IX
---
The requirements for participants to join SERNET-IX are as follows:
* a. Participants MUST be one of DN42 member.
* b. Participants MUST have at least one valid AS number in DN42 registry.
* c. Participants MUST provide sufficient information to prove the ownership to DN42 network resource they claimed. (For example, signing text with appropriate key)
* d. Participants should have DN42 network connectivity independent of SERNET-IX.
* e. Participants should be self-sufficient. For example, operating authoritative DNS of owned domain and reverse zone on their own infrastructure.

# Policies & Guidelines
---
When a DN42 operator is interested to join, it MUST agree with the content this agreement states and fulfill the above requirements.

SERNET-IX reserves the right to change agreement unilaterally with further notice.

## The Principle of SERNET-IX
---
Provide DN42 IXP service with Best-Effort.

## Peering
---
There are two main methods to peer with SERNET-IX:
* Host a router instance in SERNET-IX facility 
* Join specific SERNET-IX instance's VPN network

## Liability
---
* Each participant connecting to SERNET-IX should take all necessary precautions to assure that abuse of network resources, both that of SERNET-IX and its peers does not occur.
* SERNET-IX will not be responsible for any loss and damages to the participants caused by the operations of SERNET-IX or its participants.
* SERNET-IX will not be responsible for any illegal activities performed by any of its participants.
* All participant MUST NOT use SERNET-IX to transmit worldwide well-known criminal activities related traffic including but not limited to:
  * Online Blackmarket
  * Human Trafficking
  * Frauding
  * Spy Activities
  * Cyber Attack
  * Drug Dealing
* All participant MUST NOT abuse SERNET-IX and DN42 network, any abuse behavior occurs in SERNET-IX is not tolerable. The abuse behavior including but not limited to:
  * Transmit large amount of traffic to SERNET-IX route server and other participants
  * Send Internet route to SERNET-IX route server
  * Occupy a seat of VPN Network but not actually online for long (above 1 month), in this case SERNET-IX MAY remove the seat of VPN Network of this participant
  * No peering with SERNET-IX route server. Peering with SERNET-IX route server is MANDATORY, if session never established or downed for long (above 1 month), in this case SERNET-IX MAY remove the seat of VPN Network of this participant

## Cost
---
Seriously, I have never see someone charge others for their services in DN42. Since people so do, so do I. We gathered there because we are not that rich to own lots of clearnet resources but networking enthusiast trying to act character of network operator.

## Service Suspend and Disconnection
---
* SERNET-IX reserves the right to suspend the service to or disconnect any participants if they violate any of the policies and guidelines stated above.

# Regions unsupported inter-region connection
---
Due to the inter-region connectivity issues in these regions (or wider range of connectivity issues), SERNET-IX DO NOT accept:
* The nodes from these regions connect to SERNET-IX in other regions.
* The SERNET-IX instances in these region connect nodes in other regions.

## The list of regions unsupported inter-region connection
---
* China Mainland (If you have to, you MUST have at least one available IP address in China Mainland to let VPN Network recognize your node as if it's really come from China Mainland. Including Hainan Province, though it's not physically attached)
* Middle East (Except the stated exceptions)
* Far East of Russia Federation
* Africa (Except the stated exceptions)
* South America (Except the stated exceptions)
* Guam
* Cuba
* Democratic People's Republic of Korea (DPRK, aka North Korea)
* Antarctica
* Any human-made objects in space (For example, ISS)
* Mercury
* Venus
* Moon
* Mars
* Jupiter
* Saturn
* Uranus
* Neptune
* Pluto
* Other satellites in solar system
* Anywhere outside the solar system
* The earth before 2020s

## Exceptions
---
None yet.

# Application
---
If you have decided to join SERNET-IX, please send an e-mail to [me](contact.html) with required information below. I will send you information on how to make the connection after receiving your mail. If you have any further questions, please feel free to [contact](contact.html).

The Information required for Application:
* Your AS Number (Must be a valid DN42 AS Number)
* Node Identifier (A name of your node which peer with SERNET-IX, determined by yourself.)
* Zerotier Node ID (Only required when the operating region you want to join using Zerotier)