---
weight: 2000
title: "Grex: High Performance Computing Cluster at University of Manitoba"
linkTitle: "Grex"
description: "All you need to know about Grex and how to use it for running jobs"
titleIcon: "fa-solid fa-microchip"
categories: ["Information"]
banner: false
bannerContent: "__SCRATCH - OS and LUSTRE UPDATES__"
#tags: ["Content management"]
---

![HPCC](/hpcc/grex-room-2025.png)

## Introduction
---

__Grex__ is a UManitoba High Performance Computing (HPC) system, first put in production in early __2011__ as part of WestGrid consortium. "Grex" is a _Latin_ name for "herd" (or maybe "flock"?). The names of the Grex login nodes ([bison](https://en.wikipedia.org/wiki/Bison "Bison"), [yak](https://en.wikipedia.org/wiki/Yak "Yak")) also refer to various kinds of bovine animals.

{{< alert type="warning" >}}
Please note that older login nodes  _tatanka_ and _zebu_ are decommissioned during and after the outage of August - September 2024. These login nodes are no longer available.
{{< /alert >}}

<!-- For more information, visit the updates [page](updates) -->

Since being defunded by WestGrid (on April 2, 2018), Grex is now available only to the users affiliated with University of Manitoba and their collaborators. 

<!--
The old WestGrid documentation, hosted on the WestGrid website became irrelevant after the Grex upgrade, so please visit Grex's current [documentation.](/) 
-->

<!--
Thus, if you are an experienced user in the previous "version" of Grex, you might benefit from reading this document: description of Grex [changes.](changes/linux-slurm-update)
-->

If you are a new Grex user, proceed to the [quick start guide](start-guide) and documentation right away.

## Hardware 
---

* The original Grex was an SGI Altix machine, with 312 compute nodes (Xeon 5560, 12 CPU cores and 48 GB of RAM per node) and QDR 40 Gb/s InfiniBand network. 
{{< alert type="warning" >}}
The SGI Altix machines were decommissioned in Sep 2024.
{{< /alert >}}

* In 2017, a new Seagate **Storage Building Blocks (SBB)** based Lustre filesystem of **418 TB** of useful space was added to Grex.
{{< alert type="warning" >}}
The SBB that serves as _/scratch_ is not available.
{{< /alert >}}

* In 2020 and 2021, the University added 57 Intel CascadeLake CPU nodes, a few GPU nodes, a new NVME storage for home directories, and EDR InfiniBand interconnect.

* On March 2023, a new storage of **1 PB** was added to Grex. It is called **/project** filesystem.

* On January 2024, the **/project** was extended by another **1 PB**.

* On Sep 2024, new AMD Genoa nodes have been added (30 nodes with a total of 5760 cores).

* During 2025, two new GPU nodes with 2 L40S GPUs were added to Grex.

The current computing hardware available for general use is as follow:

### Login nodes
---

As of Sep 14, 2022, Grex is using UManitoba network. We have decommissioned the old WG and BCNET network that was used for about 11 years. Now, the DNS names use **hpc.umanitoba.ca** instead of the previous name **westgrid.ca**.

On Grex, there are multiple login nodes:

<!--
* __Bison__: bison.hpc.umanitoba.ca
* __Tatanka__: tatanka.hpc.umanitoba.ca
* __Grex__: grex.hpc.umanitoba.ca
-->
* __Yak__: yak.hpc.umanitoba.ca (please note that the architecture for this node is avx512)
* __Bison__: bison.hpc.umanitoba.ca (a second login nodes similar to Yak)
* __Grex__: grex.hpc.umanitoba.ca is a DNS alias to the above Yak and Bison login nodes
* __OOD__: ood.hpc.umanitoba.ca (only used for [OpenOnDemand Web interface](/ood) and requires VPN if used outside campus network)

To login to Grex in the text (bash) mode, connect to __grex.hpc.umanitoba.ca__  using a secure shell client, [SSH](connecting/#ssh). 


### Compute nodes
---

<!--
In addition to the original nodes, new skylake and AMD nodes have been added to Grex:
-->

There are several researcher-contributed nodes (CPU and GPU) to Grex which make it a "community cluster". The researcher-contributed nodes are available for others on opportunistic basis; the owner groups will preempt the others' workloads.

The current compute nodes available on Grex are listed in the following table:

| CPU                      | Nodes | CPUs/Node | Mem/Node | GPU               | GPUs/Node | VMem/GPU | Network<br/>(InfiniBand) |
| :----------------------: | :---: | :-------: | :------: | :---------------: | :-------: | :------: | :----------------------: |
| Intel<br/>Xeon 6248      | 12    | **40**    | 384 GB   | N/A               | N/A       | N/A      | EDR 100GB/s              |
| Intel<br/>Xeon 6230R     | 43    | **52**    | 188 GB   | N/A               | N/A       | N/A      | EDR 100GB/s              |
| AMD<br/>EPYC 9654[^1]    | 31    | **192**   | 750 GB   | N/A               | N/A       | N/A      | HDR 200GB/s              |
| AMD<br/>EPYC 9654[^1]    |  4    | **192**   | 1500 GB  | N/A               | N/A       | N/A      | HDR 200GB/s              |
| AMD<br/>EPYC 9634[^2]    |  5    | **168**   | 1500 GB  | N/A               | N/A       | N/A      | HDR 100GB/s              |
| Intel<br/>Xeon 5218[^3]  |  2    | **32**    | 180 GB   | nVidia Tesla V100 | **4**     | 32 GB    | FDR 56GB/s               |
| Intel<br/>Xeon 5218[^4]  |  3    | **32**    | 180 GB   | nVidia Tesla V100 | **4**     | 16 GB    | FDR 56GB/s               |
| Intel<br/>Xeon 6248R[^5] |  1    | **48**    | 1500 GB  | nVidia Tesla V100 | **16**    | 32 GB    | EDR 100GB/s              |
| AMD<br/>EPYC 7402P[^6]   |  2    | **24**    | 240 GB   | nVidia A30        | **2**     | 24 GB    | EDR 100GB/s              |
| AMD<br/>EPYC 7543P[^7]   |  2    | **32**    | 480 GB   | nVidia A30        | **2**     | 24 GB    | EDR 100GB/s              |
| AMD<br/>EPYC 9334[^3]    |  1    | **64**    | 370 GB   | nVidia L40S       | **2**     | 48 GB    | HDR 200GB/s              |

[^1]: CPU nodes available for all users (of these, five are contributed by a group of CHRIM researchers).
[^2]: CPU nodes contributed by Prof. M. Cordeiro (Department of Agriculture).
[^3]: GPU nodes available for all users.
[^4]: GPU nodes contributed by Prof. R. Stamps (Department of Physics and Astronomy).
[^5]: GPU nodes contributed by Prof. L. Livi (Department of Computer Science).
[^6]: GPU nodes contributed by Faculty of Agriculture.
[^7]: GPU nodes contributed by Prof. M. Cordeiro (Department of Agriculture).

<!--
| Hardware            | Number of nodes | CPUs/Node | Mem/Node | Network |
| :-------:           | :-------------: | :-------: | :------: | :-----: |
| Intel CPU           | 12              | **40**    | 384 GB   | EDR 100GB/s IB interconnect |
| Intel 6230R         | 42              | **52**    | 188 GB   | EDR 100GB/s IB interconnect |
| AMD EPYC 9654       | 27              | **192**   | 750 GB   | HDR 200GB/s IB interconnect |
| AMD EPYC 9654       |  3              | **192**   | 1500 GB  | HDR 200GB/s IB interconnect |
| AMD EPYC 9634[^2]   |  5              | **168**   | 1500 GB  | HDR 100GB/s IB interconnect |
| Intel Xeon 5560[^1] | 312             | **12**    |  48 GB   | QDR 40GB/s IB interconnect  |
[^1]: Original Grex nodes: **slated for decommission in the near furure**
-->

<!--
### GPU nodes
---

There are also several researcher-contributed nodes (CPU and GPU) to Grex which make it a "community cluster". The researcher-contributed nodes are available for others on opportunistic basis; the owner groups will preempt the others' workloads.

| Hardware             | Number of nodes | GPUs/Node | CPUs/node |Mem/Node |
| :-------:            | :-------------: | :-------: | :-------: |:------: |
| GPU[^8]              | 2               | 4         | 32        | 192 GB  |
| 4 [V100-32 GB][^3]   | 2               | 4         | 32        | 187 GB  |
| 4 [V100-16 GB][^4]   | 3               | 4         | 32        | 187 GB  |
| 16 [V100-32 GB][^5]  | 1               | 16        | 48        | 1500 GB |
| AMD [A30][^6]        | 2               | 2         | 18        | 500 GB  |
| NVIDIA AMD [A30][^7] | 2               | 4         | 32        | 500 GB  |
| L40S[^8]             | 2               | 2         | 64        | 370 GB  |

[^3]: GPU nodes available for all users (general purpose).
[^4]: GPU nodes contributed by Prof. R. Stamps (Department of Physics and Astronomy).
[^5]: NVSwitch server contributed by Prof. L. Livi (Department of Computer Science).
[^6]: GPU nodes contributed by Faculty of Agriculture.
[^7]: GPU nodes contributed by Prof. M. Cordeiro (Department of Agriculture).
[^8]: GPU nodes for general purpose.  

-->
## Storage
---

Grex's compute nodes have access to three filesystems:

<!--
- __/home__ filesystem, NFSv4/RDMA, **15 TB** total usable, 100 GB / user quota.
- __/global/scratch__ filesystem, Lustre, **418 TB** total usable, 4 TB / user quota.
-->

| File system  | Type       | Total space | Quota per user | Quota per group |
| :----------: | :--------: | :---------: | :------------: | :-------------: |
| __/home__    | NFSv4/RDMA | **15 TB**   | 100 GB         | N/A             |
| __/project__ | Lustre     | **2 PB**    | N/A            | 5 TB            |

<!--
| __/global/scratch__ | Lustre      | **418 TB**   | 4 TB             |
-->

In addition to the shared file system, the compute nodes have their own local disks that can be used as temporary storage when [running jobs](running-jobs).

<!--
There is a **10 GB/s** Ethernet connection between Grex and [CANARIE](https://www.canarie.ca/network/) network.
-->

## Software
---

Grex is a traditional HPC machine, running Linux and SLURM resource management systems. On Grex, we use different software [stacks](software).

## Web portals and GUI
---

In addition to the traditional bash mode (connecting via SSH), users have access to:

* [OpenOnDemand](/ood): on Grex, it is possible to use OpenOnDemand (OOD for short) to login to Grex and run batch or GUI applications (VNC Desktops, Matlab, Gaussview, Jupyter, ...)

<!--
* __X2Go:__ for more information, visit the page, connect to Grex via [X2Go](connecting/#x2go)
-->

## Useful links
---

* [Digital Research Alliance of Canada](https://alliancecan.ca/) (Alliance), formerly known as Compute Canada
* [Alliance documentation](https://docs.alliancecan.ca/wiki/Main_Page)
* [Local Resources at UManitoba](localit)
* [Grex status page](https://um-grex.github.io/status/)

---

{{< alert type="warning" >}}
__WestGrid__ ceased operations on April 1st, 2022. The former WestGrid institutions are now re-organized into two consortia: __BC DRI group__ and __Prairies DRI group.__
{{< /alert >}}

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last revision: Sep 10, 2024.
-->
