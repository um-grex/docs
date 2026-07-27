---
weight: 3500
linkTitle: "Access and usage conditions"
title: "Access and Usage Conditions"
description: "All you need to know for accessing and using Grex."
titleIcon: "fa-solid fa-id-card"
categories: ["Information"]
banner: false
bannerContent: "Call for applications/renewing Grex HPC resource allocations for __2026__: an email has been sent to all Faculty members with active CCDB account and the deadline is __Dec 12, 2025__."
#tags: ["Content management"]
---

# Access Conditions
---

Grex is open to all research groups at University of Manitoba, their sponsored users (students, postdocs and researchers) and their external  collaborators. The main purpose of the Grex system is research. However, it might also be used for grad studies courses with a strong research component, for their course-based research projects.

Access to the system, and resource allocations are _by research group_; that is, the Principal Investigator (PI) receives resources for his group, and approves access for his group members. 

Grex's resources (CPU and GPU time, disk space, software licenses) are automatically managed by a batch scheduler, [SLURM](running-jobs), according to the University's priorities. There is a process of resource allocation competition (RAC) to get an increased share of Grex resources. A  "Default" share of these resources is available immediately and free of charge by getting an account.

It is expected that Grex accounts and resource allocations are used for the research projects they are requested for.

Owners of the [user-contributed](running-jobs/contributed-systems) hardware on Grex have preferential access to their hardware, which can only be used by the general community of UM researchers when idle and not reserved.

## Getting an account on Grex
---

Presently, Grex is using the Compute Canada (now the Alliance) account management database ([CCDB](https://ccdb.alliancecan.ca/)). 

 **There are two technical conditions for getting access:**

>  - An active [CCDB](https://ccdb.computecanada.ca "CCDB") account. 
>  - An active CCDB "role" affiliated with UManitoba (University of Manitoba).

Any eligible Canadian Faculty member can get an Alliance [account](https://alliancecan.ca/en/services/advanced-research-computing/account-management/apply-account) in the CCDB system, in their own right. 

Researchers and tecnhical staff members in other roles (undergraduate students, doctoral student, postdoctoral fellow, research assistant, or a non-research staff member, external collaborator) will need to be sponsored in CCDB by a Faculty member. 

>  - The PI of a new group registers in the CCDB first; gets assinged a CCRI (CCDB Role Identifyer; a code of format _abc-NM-PQ_) .
>  - Sponsored users then apply in CCDB and provide CCRI of the PI 
>  - Then the PI approves her sponsored users  under her group's allocation

Once an Alliance account for a user has been approved and made active in CCDB, the user can start using Grex's computing and data storage facilities. There is no "opt-in" in CCDB required for Grex, as every role that is affiliated with the University gets the access automatically.


## Guidelines of the Acceptable Use
---

Grex adheres to the Alliance's Privacy and Security [policy](https://alliancecan.ca/en/privacy-policy), and to University of Manitoba IT Security and Privacy [policies](https://umanitoba.ca/computing/ist/security/policies.html)

Users that have Grex account have accepted both.

> * In particular, user's accounts **must  not be shared** with anyone for whatever reason. Our support staff will never ask for your password.

{{< alert type="warning" >}}
Sharing any account information (login/password or SSH private keys) leads to immediate blocking of the account. 
 UNIX groups and shared project spaces can be used for data [sharing](storage/data-sharing).
{{< /alert >}}

> * Usage of Grex is monitored, and usage statistics are collected automatically, to estimate the researcher's needs and future planning, and to troubleshoot day to day operational issues.

> * Users of Grex may receive notices by email, such as upcoming system outages, cybersecurity alerts, upcoming local HPC training events, and their Grex usage statistics. 

> * Users of Grex should be "**fair and considerate**" in their usage of the systems, trying not to allow for unnecessary and/or inefficient use of the resources or interfering with other users' work. We reserve to ourselves the right to monitor for inefficient use and ask users to stop their activities if they threaten the general stability of the Grex system.

## Getting a Resource Allocation on Grex
---

* Similarly, to the Alliance (formerly known as Compute Canada), there is a two-tier system for resource allocation on Grex. Every group gets a "Default" allocation of Grex resources (computing time and storage space) immediately after the PI registers in CCDB. Groups that need a larger fraction of resources and use Grex intensively, might want to apply for a local Resource Allocation Competition (Grex-RAC).

* Grex's local RAC calls are issued once a year. They are reviewed by a local Advanced Research Computing Committee and may be scaled according to the availability of resources. The instructions and conditions of this year's RAC are provided on the RAC template document (sent via email when the RAC is announced).

* The 2025/26 Call for applications/renewing Grex HPC resource allocations had been closed on __Dec 12, 2025__. Allocations that were awarded are now in place.

---

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last revision: July 27, 2026.
-->


