# Physical Sandbox Guides
Use this guide to learn how to leverage the DevNet Physical Sandboxes!


## Table of Contents
- [Access Details](#access-details)
- [Sandbox Overview](#sandbox-overview)
- [Programmability Overview](#programmability-overview)
- [Benefits](#benefits)
- [Resources](#resources)

![Catalyst Family](./imgs/C9000-Family.png)

## Access Details
Developer reserves the Lab and receives software VPN access information and credentials via email at the start of the reservation. Once connected via software VPN the developer can access the lab's devices and server via Telnet/SSH/HTTP.

| Address | username | password  |
| --- | --- | ---  |
| 10.10.20.181 | developer | C1sco12345 |

### Ports

* Catalyst 9000 Host: 10.10.20.181 (After VPN connection)  
    * SSH Port: 22  
    * NETCONF Port: 830  
    * RESTCONF Ports: 443  

*Do not change the credentials for, or remove any user credentials configured on the sandbox.*

> **IMPORTANT NOTE:** Do not try to reload the physical switch in your sandbox, or change the software image running.  This will result in your loss of access to your switch as it is rebaselined back to default and prepared for the next reservation.

### VPN Information

[VPN access information for Windows, MAC and Linux](https://developer.cisco.com/docs/sandbox/#!getting-started/sandbox-vpn-info)

## Sandbox Overview

### Sandbox Summary
**C9200 Stacking - Key Differences at a Glance**
| Sandbox          | What's available?  | Version | # Simultaneous users | App Hosting
| --------         | ------- | ------- | ------- | ------- |
| Virtual Switch   | C9000 Virtual Swithes | 17.15.1 | many users will have access to the same always-on box    | Yes    |
| Standalone C9300 | 4 standalone C9300X-24HX devices and 5 standalone C9300 devices | 17.15.1 | 9     | Yes     |
| Switching Stack      | Two 2-member C9300X stacks, a 2-member C9300L-24U-4G stack, a 2-member C9300LM-24U-4Y stack, a 2-member C9200L-24P-4G stack and a 2-member C9200-24P stack | 17.15.1 | 6 (each user will receive access to a 2-memeber stack)    | No, note: 9300L supports App Hosting, but in this lab, there is no SSD, so no app hosting support; 9200 does not support app hosting    |
| C9200 Standalone | C9200CX | 17.15.1 | 1     | Not supported on this SKU     |
<!-- | C9200 Stack      | 2-member C9200L-24P-4G stack & 2-member C9200-24P stack | 17.15.1 | 4 (each user will receive access to a 2-memeber stack)     | Not supported on this SKU    | -->

Sandbox Rack
![Sandbox Rack](./imgs/sandbox-rack.jpg)


Learn more details about the various sandbox options:
1. **Virtual Switch** - Likely, you have already worked with the [C9000 Virtual Switch](https://devnetsandbox.cisco.com/DevNet/catalog/Cat9k-Always-On_cat9k-always-on) (or C9KV) Sandbox. Now, you're ready to explore physical hardware and capabilities. This device has hostname [devnetsandboxiosxec9k.cisco.com](devnetsandboxiosxec9k.cisco.com).

1. **C9300X & C9300 Standalone** (4 standalone C9300X-24HX devices and 5 standalone C9300 devices): In this guide, you will be able to work with a single (non-stacked) C9300 running Cisco IOS XE 17.15 with NETCONF & RESTCONF enabled. The C9300X-24HX has Stackable 24 Multigigabit Ethernet (100 Mbps or 1/2.5/5/10 Gbps) UPOE+ ports; PoE budget of 735W with 1100WAC power supply; supports StackPower+, StackWise-1T, and C9300X-NM network modules.

    C9300X-24HX
    ![C9300X-24HX](./imgs/C9300X-24HX+NM-8Y_Front.png)

    <!-- C9300X-24Y
    ![C9300X-24Y](./imgs/C9300X-24Y+NM-8Y_Front.png) -->

    <!-- 1. **C9300 Standalone** (): -->

1. **Switching Stack** (Two 2-member C9300X stacks, a 2-member C9300L-24U-4G stack, a 2-member C9300LM-24U-4Y stack, a 2-member C9200L-24P-4G stack and a 2-member C9200-24P stack): This guide you will be able to work with a stack running Cisco IOS XE 17.15 with NETCONF & RESTCONF enabled. 

    The C9300L has fixed uplinks and supports mGig density of 12x10G and the C9300LM supports 8x10G. The C9300LM-24U-4Y has Stackable 24 x 10/100/1000 M UPOE ports; 4 x 25 GE SFP28 fixed uplink ports; PoE budget of 420 W with a single default 600 WAC power supply; supports StackWise-320. You may be curious about the differences between the C9300L and C9300LM. One of the major differences is the C9300L supports 1/10G/40G uplinks while the C9300LM supoorts 1/10G/25G uplinks. Note: There is no SSD for app-hosting use case.
    
    C9300X-24HX
    ![C9300X-24HX](./imgs/C9300X-24HX+NM-8Y_Front.png)  
   
    C9300L-24P-4
    ![C9300L-24P-4G](./imgs/C9300L-24P-4_Front.jpg)


    C9300LM-24U-4Y
    ![C9300LM-24U-4Y](./imgs/C9300LM-24U-4Y_Front.jpg)

    Modular Uplinks: The C9200 models allow you to customize uplink ports (e.g., 1G, 10G, or 25G) by using pluggable uplink modules. These switches support full software features, including advanced programmability and network automation capabilities. Power and Flexibility: They are more flexible and suited for larger or more complex networks where scalability is needed. The C9200 supports 160 Gbps stacking bandwidth, which is higher and offers better performance for high-traffic environments. The C9200 uses modular StackWise-160 modules for stacking. These modules can be replaced or upgraded as needed. Because the stacking module is modular, it provides more flexibility in terms of deployment and future upgrades. The higher stacking bandwidth makes it more suitable for larger networks or those with intensive traffic needs. Note: App hosting is not available on this SKU.

    C9200-24P
    ![C9200-24P](./imgs/C9200-24P_with_NM-4X_Front.png)

    The C9200L models come with fixed uplink ports (predefined, non-modular), which simplifies deployment but reduces flexibility. These are more affordable compared to the C9200, making them suitable for smaller networks or cost-sensitive deployments. They support essential software features but may lack some advanced capabilities available in the C9200. The C9200L supports 80 Gbps stacking bandwidth, which is lower compared to the C9200 stack. The C9200L comes with built-in, fixed StackWise-80 ports, meaning you cannot upgrade or replace the stacking. While the stacking bandwidth is lower, the fixed design makes the C9200L more affordable and suitable for smaller networks where traffic demands are moderate.

    C9200L-24P-4G 
    ![C9200L-24P-4G](./imgs/C9200L-24P-4G_Front.png)

    For stacks, the devices will be connected together on the back similar to the diagram below. Notice the criss-crossed wires to make this possible:

    ![Stacking-Drawing](./imgs/stacking-drawing.png)


1. **C9200CX Standalone** (1 C9200CX device): In this guide, you will be able to work with a single (non-stacked) C9200 running Cisco IOS XE 17.15 with NETCONF & RESTCONF enabled. The C9200CX-12P offers PoE+ inline power on all downlink ports for a maximum power budget of 240W. Note: App hosting is not available on this SKU. The C9200CX is designed as a compact switch for space-constrained environments like offices, retail spaces, or industrial sites. Many C9200CX models are fanless, making them quiet and ideal for noise-sensitive areas Similar to the C9200L, it has fixed uplinks. Also, the C9300LM or mini switch is smaller in depth than the C9300L. Either switch is great for scenarios where space, noise, or heat are concerns.

    ![C9200CX-12P-2X2G](./imgs/C9200CX-12P-2X2G_Front.jpg)


<!-- 1. **C9200/C9200L Stack** (a 2-member C9200L-24P-4G stack and a 2-member C9200-24P stack): In this guide, you will be able to work with a C9200 stack running Cisco IOS XE 17.15 with NETCONF & RESTCONF enabled.  -->



## Programmability Overview


The **IOS XE on Catalyst 9000** Sandbox offers developers access to a physical Catalyst 9000 switch running release 17.15 IOS XE.  Here, you can test out the programmability features and data models available in this version. 

Some of the programmability features developers and network engineers can explore include: 
*   Model Driven Programmability with YANG Data Models and NETCONF and RESTCONF
*   Linux Guest Shell on-box for running Linux Applications and run Python Scripts directly at the at the edge.
*   Application Hosting allows application developers and network engineers to build and deploy applications (custom or off the shelf) on the network device

**IMPORTANT NOTE:** This sandbox is not built to support testing of Day 0 Technologies such as ZTP, iPXE, and PNP.  If you try to clear out configurations and use these features you will lose access to your switch.  

IOS XE is the software running on many platforms from Cisco including switches, routers, gateways, etc. Not all programmability features are available on every platform. Check specific platform documentation for details.

New Application Hosting Features available in this release:  

IOS XE 17.15 on Catalyst 9300 provides developers new application hosting capabilities. Developers can create their own docker container based apps and use Catalyst 9300 to host these apps. **Note: the app hosting capabilities are not supported on 9200 switches.**


[YANG Suite](https://github.com/CiscoDevNet/yangsuite) is HTML5 based tooling that is available for working with the YANG based programmable interfaces on Cisco IOS XE, XR, and NX Network Operating Systems. It has plugins that allow for interacting with the programmable interfaces and supports downloading YANG files directly from network devices. In this module, we will explore using NETCONF and RESTCONF to configure a switch and we will create a gRPC telemetry subscriptions.

This guide has the following sections:
- [NETCONF](https://github.com/CiscoDevNet/yangsuite/blob/main/examples/NETCONF.md)
- [RESTCONF](https://github.com/CiscoDevNet/yangsuite/blob/main/examples/RESTCONF.md)
- [gNMI](https://github.com/CiscoDevNet/yangsuite/blob/main/examples/gNMI.md)

### Learn more about YANG Suite
- [YANG Suite GitHub](https://github.com/CiscoDevNet/yangsuite)
- [YANG Suite on DevNet](https://developer.cisco.com/yangsuite/)

### YANG Suite Videos
- [Getting started with Cisco YANG Suite](https://youtu.be/smrhjL5Ayz0)
- [All YANG Suite, all the time, DevNet Snack Minute, Episode 9](https://www.youtube.com/watch?v=3zmNDfn8b38)
- [NETCONF with YANG Suite](https://www.youtube.com/watch?v=dTun33611JA)


##  Benefits

### 9300 
Catalyst 9300 switches have many benefits including enhanced high availability features such as Extended Fast Software Upgrade (xFSU), Stateful Switchover (SSO), Software Maintenance Upgrades (SMU), Graceful Insertion and Removal (GIR), Cisco StackWise® and StackPower technology. Further, the C9300 switches support all the foundational high-availability capabilities, including Platinum-efficient dual redundant power supplies and variable-speed, high-efficiency, redundant fans. Additionally, the 9300 has app hosting support, described in more detail below.

* **Easier hosting:** Create, deploy and run applications easily by making use of the ability to host docker containers on Catalyst 9000 series.
* **Enhanced agility:** Drive greater business agility as developers can quickly develop applications and deploy them where needed.

The sandbox provides developers an environment to experiment with Application Hosting on IOS XE. To understand the benefits of Application Hosting, developers can create their own apps in the developer environment or bring in their own apps. These apps can then be deployed using the Catalyst 9300 that the developer has reserved.

One of the major values of a switching stack is high availability. For more details and scenarios, check out the 9300 stacking configuration guide: https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9300/software/release/17-15/configuration_guide/stck_mgr_ha/b_1715_stck_mgr_ha_9300_cg.html


#### App Hosting Overview

Application Hosting can be used by development and operations (DevOps) personnel who plan to optimize networks by collecting analytics of the network in real-time, locate where problems occur, and investigate issues in a collaborative manner in order to maintain the health of the network.

**Note:** App hosting is only available on 9300 sandboxes, NOT on the 9200 sandboxes. In this particular sandbox, the 9300L does not have an external SSD, meaning App Hosting is not available within this sandbox. However, please note that with an SSD, the 9300L switch does support app hosting. 

Catalyst 9000 series switches were built to support Intent Based Networking. The switches can now support native docker applications. 

Docker helps in easy portability of applications, version controlling and expedites the devops cycle. You can now securely deploy the apps right next to mission critical infrastructure. Process and files access for apps are isolated and restricted making it completely safe and secure. Application Hosting solution on Catalyst 9000 series provides the much needed intelligence at the edge.


### 9200 
Catalyst 9200 switches have many benefits and support L3 features such as OSPF, EIGRP, ISIS, RIP, and routed access. The C9200-24P includes 24 ports with full PoE+ support. In addition, the 9200CX supports basic BGP.

One of the major values of a switching stack is high availability. For more details and scenarios, check out the 9200 stacking configuration guide: https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9200/software/release/17-15/configuration_guide/ha/b_1715_stck_mgr_ha_9200_cg.html

**Note: 9200 switches do not support app hosting**


## Resources
* [9200 Data Sheet](https://www.cisco.com/c/en/us/products/collateral/switches/catalyst-9200-series-switches/nb-06-cat9200-ser-data-sheet-cte-en.html)
* [9300 Data Sheet](https://www.cisco.com/c/en/us/products/collateral/switches/catalyst-9300-series-switches/nb-06-cat9300-ser-data-sheet-cte-en.html)
* [Cisco Catalyst 9000 Switching Platform FAQ](https://www.cisco.com/c/en/us/products/collateral/switches/catalyst-9000/nb-06-cat9k-swit-plat-faq-cte-en.html)
* [9200 configuration guides by release](
https://www.cisco.com/c/en/us/support/switches/catalyst-9300-series-switches/products-installation-and-configuration-guides-list.html)
* [9300 configuration guides by release](https://www.cisco.com/c/en/us/support/switches/catalyst-9300-series-switches/products-installation-and-configuration-guides-list.html)
* [DevNet Python Code Samples](https://github.com/CiscoDevNet/python_code_samples_network)  
* [SBX Sandbox](https://github.com/DevNetSandbox/sbx_iosxe)  
* [IOS XE on DevNet](https://developer.cisco.com/site/ios-xe/)
* [Application Hosting on IOS XE on DevNet](https://developer.cisco.com/app-hosting/)
* [Model Driven Programmability on DevNet](https://developer.cisco.com/site/standard-network-devices/)
* [Python for Network Automation on DevNet](https://developer.cisco.com/site/python/)

> Samples are being developed across these features and added as you read this so check back often!!  


### Learning Labs:
Here are some Learning Labs where you can get some guided walkthroughs of features.  

* ["Introduction to Model Driven Programmability (ex: NETCONF/YANG)" Learning Module](https://learninglabs.cisco.com/modules/intro-device-level-interfaces)
* [Introduction to Guest Shell on IOS XE](https://learninglabs.cisco.com/modules/net_app_hosting)