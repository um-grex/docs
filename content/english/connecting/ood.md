---
weight: 1600
linkTitle: "Connect with OOD"
title: "Connect and Transfer data with OOD"
description: "Everything you need to know about connecting and transferring data with OOD."
categories: []
banner: false
#bannerContent: "The OOD pages are work in progress. Stay tuned!"
#tags: ["Configuration"]
---

OpenOnDemand (OOD) is a Web portal application for High-Performance Computing systems. 
An OOD portal is available on Grex at [https://ood.hpc.umanitoba.ca](https://ood.hpc.umanitoba.ca) ; it can be used to [connect](../ood/#connect-to-openondemand) to Grex, Transfer data and and run both interactive and batch Applications. For more information about how to connect and use OOD, please refer to the main [OOD](../ood) page.

<!--

## OSC OpenOnDemand on Grex
---

OpenOnDemand is a Web portal application for High-Performance Computing systems. It is used on many of the Top 500 HPC machines across the World.
Grex's current OOD v.4.0.6 instance runs on a dedicated login node [**ood.hpc.umanitoba.ca**](https://ood.hpc.umanitoba.ca "Grex OOD").
It is available only from UManitoba IP addresses -- that is, your computer should be on the UM Campus network or the UM VPN to connect. 

To connect from outside the UM network, please install and start [UManitoba Virtual Private Network](https://umanitoba.ca/information-services-technology/my-security/vpn-support "UofM VPN"). OOD relies on in-browser VNC sessions; so, a modern browser with HTML5 support is required; we recommend Google Chrome or Firefox and its derivatives (Waterfox, for example).

**Connect to OOD when on campus network:**

> - Point your Web browser to [https://ood.hpc.umanitoba.ca](https://ood.hpc.umanitoba.ca "Grex OOD")
> - Use your Alliance CCDB username and password to log in to Grex OOD.
> - __New:__ use your **Alliance CCDB Duo second factor method** to continue logging in to Grex OOD.

**Connect to OOD using [UManitoba VPN](https://umanitoba.ca/information-services-technology/my-security/vpn-support "UofM VPN"):**

> - Make sure Umanitoba Ivanti Secure VPN Client is connected. Use UManitoba second factor auth (as of 2024, Microsoft Entra) if asked.
> - Point your Web browser to [https://ood.hpc.umanitoba.ca](https://ood.hpc.umanitoba.ca "Grex OOD")
> - Use your Alliance CCDB username and password to log in to Grex OOD.
> - __New:__ use your **Alliance CCDB Duo second factor method** to continue logging in to Grex OOD.


### Connect via OOD
---

First, the OOD host would present a Keycloak login page, followed by the usual Duo second factor prompt.

When authenticated, you will see the following screen with the current Grex Message-of-the-day (MOTD):

{{< collapsible title="Landing / MOTD page, OpenOndemand web portal on Grex" >}}
![OpenOnDemand Front Page](/ood/frontpage.png)
{{< /collapsible >}}

OOD expects user accounts and directories on Grex to be already created. Thus, new users who want to work with OOD should first connect to Grex normally, via SSH shell at least once, to make the creation of account, directories, and quota complete. Also, OOD creates a state directory under users' ``/home`` (__/home/$USER/ondemand__) where it keeps information about running and completed OOD jobs, shells, desktop sessions and such. Deleting the __ondemand__ directory while a job or session is running would likely cause the job or session to fail.

{{< alert type="warning" >}}
It is better to leave the __/home/$USER/ondemand__ directory alone!
{{< /alert >}}

### Transfer data
---

The _Files_ dropdown menu in the OOD Dashboard bar provides a File browser for each of the filesystems available to the user. 
The _Files_ interface allows for uploading, downloading and editing of the files on the HPC cluster.

{{< collapsible title="File view on OpenOndemand web portal on Grex" >}}
![](/ood/files.png)
{{< /collapsible >}}

-->

## Internal links
---

* Use OOD Files app to move data: [OOD Files](../ood/#files)
* Run applications with [OOD](../ood)

## External links
---

[Homepage of the OnDemand project](https://openondemand.org/)

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last reviewed on: Sept 10, 2024.
-->
