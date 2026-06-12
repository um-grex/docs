---
weight: 2000
linkTitle: "Data sizes and Quotas"
title: "Data sizes and quotas"
description: "Everything you need to know about data sizes and quota."
categories: ["Information"]
#tags: ["Configuration"]
---

## Data size and quotas
---

This section explains how to find the actual space and inode usage of __/home/__ and __/project__ allocations on Grex. We limit the size of the data and the number of files that can be stored on these filesystems. The table provides a "default" storage quota on Grex. Larger quota can be obtained on __/project__ via UM local RAC process.

| File system         | Type        | Total space | Bulk Quota       | Files Quota    |
| -----------         | :---:       | :---------: | :------------:   | :------------: |
| __/home__           | NFSv4/RDMA  | **15 TB**   | 100 GB / user    |0.5M per user   |
| __/project__        | Lustre      | **2 PB**    | 5-20 TB / group  | 1M / user, 2M / group |

To figure out where your current usage stands with the limit, POSIX __quota__ or Lustre's analog, __lfs quota__, commands can be used. A convenient command, __diskusage_report__ summarizes usage and quota across all the available filesystems.

### NFS quota 
---

The __/home/__ filesystem is served by NFSv4 and thus supports the standard POSIX __quota__ command. For the current user, it is just:

{{< highlight bash >}}
quota
{{< /highlight >}}

or

{{< highlight bash >}}
quota -s
{{< /highlight >}}

The command will result in something like this (note the __-s__ flag added to make units human readable):

{{< highlight bash >}}
[someuser@yak ~]$ quota -s
  Disk quotas for user someuser (uid 12345):
     Filesystem  space quota limit grace files quota limit grace
192.168.x.y:/   249M  100G  105G        4953  500k 1000k       
{{< /highlight >}}

The output is a self-explanatory table. There are two values: __soft__ "quota" and __hard__ "limit" per each of (space, files) quotas. If you are over soft quota, the value of used resource (space or files) will have a star _*_ to it, and a __grace__ countdown will be shown. Getting over grace period, or over the hard limit prevents you from writing new data or creating new files on the filesystem. If you are over quota on __/home__, it is time to do some cleaning up there, or migrate the data-heavy items to __/global/scratch__ where they belong.

{{< alert type="warning" >}}
__CAVEAT:__ The Alliance (Compute Canada) breaks the POSIX standard by redefining the quota command in their software stack. So, after loading the __CCEnv__ module on Grex, the quota command may return garbage. For accurate output about your quota, load the local software environment __SBEnv__ first before running the command __quota__ or use the command __diskusage_report__ (see below).
{{< /alert >}}

### Lustre quota
---

The main Lustre storage appliances on Grex, is the __/project__ filesystem. The previous filesystem was called __/global/scratch/__ and , as of now, is disabled and not available for users. 

On a Lustre filesystem, three types of quota are possible: per user, across all the filesystem; per group, across all the filesystem; and a directory quota that is per directory, per group. 

#### /project/ (current)

This filesystem has a similar [hierarchical directory structure](https://docs.alliancecan.ca/wiki/Project_layout) to the Alliance/ComputeCanada HPC systems. On Grex, it is like follows:

 *  /project/Project-GID/{user1, user2, user3} 

Where the Project-GID is a __number (or identifier)__ of the PI's default RAPI group in CCDB, and user1..3 are the users on Grex, including the PI. 

{{< alert type="warning" >}}
Note that the directories get created, and the quota is set, on a first login of a user to the Grex system. Before the first login of any user, no /home and /project directories exist and no quota is set for them on Grex. To have your directories created, please log in to Grex either via a normal SSH through a regular login node or via [OOD](/ood).
{{< /alert >}}

<!--
Note that the directories get created, and the quota is set, on a first login of a user to the Grex system. Before the first login of any user, no /home and /project directories exist and no quota is set for them on Grex. Thus, certain operations that need them ( running jobs through [OOD](/ood), and using automated workflows) would fail. Please log in to Grex via a normal SSH through a regular login node first!
-->

It is inconvenient to go by using numerical values of the Project-GID in the paths, so there are symbolic links present in each user's _/home/$USER/projects_ directory that point to his _/project_ directories.  A user can belong to more than one research group and thus can have more than one project link. Also, on the filesystem there is a system of symbolic links in the form of _/project/Faculty/def-PIname/_ . 

{{< highlight bash >}}
[someuser@yak ~]$ lfs quota -h -p 123456 /project/123456
Disk quotas for prj 123456 (pid 123456):
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
/project/123456
                 150.6G  4.883T  5.371T       -  208654  2000000 2200000       -   

{{< /highlight >}}

In addition to the directory quota, each user has her own quota, presently for inodes (the number of files and directories on the entire filesystem. To see the space and inode usage per user, run the following command:

{{< highlight bash >}}
[someuser@yak ~]$ lfs quota -h -u $USER /project
Disk quotas for user someuser (uid 123456):
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
       /project  150.6G      0k      0k       -  175394  1000000 1100000       -
{{< /highlight >}}

<!--
#### /global/scratch/  (old)

The __/global/scratch/__ filesystem is actually a link to a Lustre filesystem called __/sbb/__. We have retained the old name for compatibility with the old Lustre filesystem that was used on Grex between 2011 and 2017. Lustre filesystem provides a __lfs quota__ sub-command that requires the name of the filesystem specified. So, for the current user, the command to get current usage {space and number of files}, in the human-readable units, would be as follows:

{{< highlight bash >}}
lfs quota -h -u $USER /sbb
{{< /highlight >}}

With the output:

{{< highlight bash >}}
[someuser@yak ~]$ lfs quota -h -u $USER /sbb
 Disk quotas for usr someuser (uid 12345):
 Filesystem  used   quota  limit  grace   files  quota   limit  grace
      /sbb   622G  2.644T  3.653T     - 5070447 6000000 7000000     -
{{< /highlight >}}

Presently the  group or directory quotas on __/global/scratch__ are not enforced.

If you are over quota on Lustre __/global/scratch__ filesystem, just like for NFS, there will be a star to the value exceeding the limit, and the grace countdown will be active. 
-->
## A wrapper for quota
---

To make it easier, we have set a custom script with the same name as for the Alliance (Compute Canada) clusters, __diskusage_report__, that gives both __/home__ and __/project__ quotas (space and number of files: usage/quota or limits), as in the following example:
{{< highlight bash >}}

[someuser@bison ~]$  diskusage_report 
------------------------------------------------------------------------
              Description (FS)          Space (U/Q)     # of files (U/Q)
------------------------------------------------------------------------
              /home (someuser)           254M/104G            4953/500k
      /project (def-professor)          131G/2147G           992k/1000k
------------------------------------------------------------------------
   /project/6543210 = /home/someuser/projects/def-professor
------------------------------------------------------------------------
{{< /highlight >}}

<!--
for more details[^1], run the command:

[^1]: These options are not implemented on the Alliance clusters.

{{< highlight bash >}}
diskusage_report -dd -vv
{{< /highlight >}}

as in the example:

{{< highlight bash >}}
[someuser@bison ~]$ diskusage_report -dd -vv
+ Space and Inode quotas for user: someuser
+ Date: Thu Feb 24, 2022
          Description (FS)      Space (U/Q/L)   # of files(U/Q/L)
          /home (someuser)     254M/104G/110G     4953/500k/1000k
/global/scratch (someuser)    131G/2147G/3221G   992k/1000k/1100k

FS ==> File System (/home, /global/scratch)
U  ==> Current Usage (Space, Inode)        
Q  ==> Soft Quota (Space, Inode)           
L  ==> Hard Quota (Space, Inode)    
{{< /highlight >}}
-->

The command __diskusage_report__ can also be invoked with the argument __--home__ or __--project__ to get the quota for the corresponding file system.

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last reviewed on: Apr 29, 2024.
-->

