---
weight: 200
linkTitle: "2026"
Title: "Workshops - 2026"
description: "Workshops and Training Material - 2026"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
---

## Spring HPC and Cloud computing workshop, May 2026.
---

Below are the slides and some materials from the in-person workshop that was held on May 2026:

### May 20, 2026


> - **Introduction to Alliance and Manitoba HPC, AI and Cloud resources**: [Slides](/workshops/spring2026/Intro-to-Canadian-and-Local-DRI-Spring-2026.pdf)
<!--
> - **Linux (BASH) CLI tutorial, SSH**: [Slides](/workshops/spring2026/)
-->
> - **Introduction to High Performance Computing Software and Lmod**: [Slides](/workshops/spring2026/Introduction-to-HPC-software-2026.pdf)
<!--
> - **Data transfers and storage with Globus, NextCloud**: [Slides](/workshops/spring2026/)
-->

### May 21, 2026

<!--
> - **OpenOnDemand HPC Web portal: File Transfer, Remote Desktop and interactive GUI applications**: [Slides](/workshops/spring2026/)
> - **Containers in HPC: using Singularity/Apptainer**: [Slides](/workshops/spring2026/)
> - **Containers in HPC: using Podman and Pixis**: [Slides](/workshops/spring2026/)
-->
> - **Introduction to OpenOnDemand:Running jobs using the Job Composer**: [Slides](/workshops/spring2026/Running-Jobs-via-OOD.pdf)
> - **Running jobs on HPC cluster using SLURM: batch and interactive jobs**: [Slides](/workshops/spring2026/Running-Jobs-on-HPC-Cluster-2026.pdf)
<!--
> - **Using Jupyter on HPC systems via OOD, JupyterHub**: [Slides](/workshops/spring2026/)							
-->

### May 22, 2026

<!--
> - **AI on HPC topics**: [Slides](/workshops/spring2026/)
> - **Using OpenStack Cloud Dashboard**: [Slides](/workshops/spring2026/)
> - **Using OpenStack Cloud, deploying Web applications**: [Slides](/workshops/spring2026/)
> - **Using OpenStack CLI and ObjectStorage**: [Slides](/workshops/spring2026/)
-->

<!--
### UManitoba Spring 2026 HPC and Cloud computing workshop

Join us for our free Spring 2026 workshop, open to all researchers from Manitoba institutions! 

We are pleased to invite you to the Spring 2026 Manitoba High-Performance Computing (HPC) and Cloud Computing Workshop, taking place May 20–22, 2026, at the University of Manitoba (Fort Garry campus), Engineering Building, Lecture Hall __E2-350__. The workshop is designed for beginner and intermediate users. 

**Requirements:**

 * A laptop is required for hands-on exercises.
 * An active CCDB account is optional (but recommended).
 * Free RSVP is required: must be completed via Eventbrite, at the following URL: [Spring 2026 Registration](https://grexapps.hpc.umanitoba.ca/short/register-ws-2026)

Any individual sessions can be selected. We will issue certificates of attendance for participants who attended all the three days.

> Coffee will be provided before each days' session! We got cookies! 

### Program and materials

**Day 1: May 20**

| Start | End   | Session description           | Speaker |
| :---: | :--:  | :---------------------------: | :-------: |
| 10:00 | 10:45 | Introduction to Alliance and Manitoba HPC, AI and Cloud resources | Grigory |
| 10:45 | 11:00 | Housekeeping: how to connect to MC/Grex/CC | Stefano |
| 11:00 | 12:30 | Linux (BASH) CLI tutorial, SSH | Stefano |
| 12:30 | 13:00 | Lunch break | |
| 13:00 | 14:20 | Intro to HPC software, Lmod modules tool | Ali |
| 14:20 | 15:00 | Data transfers and storage with Globus, NextCloud | Stefano |


**Day 2: May 21**

| Start | End   | Session description           | Speaker |
| :---: | :--:  | :---------------------------: | :-------: |
| 10:00 | 10:10 | Housekeeping: how to connect to MC/Grex/CC | Stefano |
| 10:10 | 11:00 | OpenOnDemand HPC Web portal: File Transfer, Remote Desktop and interactive GUI applications | Stefano |
| 11:00 | 12:00 | Containers in HPC: using Singularity/Apptainer | Grigory |
| 12:00 | 12:30 | Containers in HPC: using Podman and Pixis | Stefano |
| 12:30 | 13:00 | Lunch break | |
| 13:00 | 13:30 | OpenOnDemand HPC Web portal: Running Jobs with Job Composer | Ali |
| 13:30 | 14:30 | Running HPC jobs with SLURM scheduler (hands-on) | Ali |
| 14:30 | 15:00 | Using Jupyter on HPC systems via OOD, JupyterHub | Grigory |                                                        

**Day 3: May 22**

| Start | End   | Session description           | Speaker |
| :---: | :--:  | :---------------------------: | :-------: |
| 10:00 | 10:10 | Housekeeping: how to connect to MC/Grex/CC | Grigory |
| 10:10 | 10:30 | Using ClusterPilot to run jobs | Julia Frank |
| 10:30 | 11:30 | AI on HPC topics | Grigory |
| 11:30 | 12:30 | Using OpenStack Cloud Dashboard | Stefano |
| 12:30 | 13:00 | Lunch break | |
| 13:00 | 13:45 | Using OpenStack Cloud, deploying Web applications | Stefano |
| 13:45 | 14:45 | Using OpenStack CLI and ObjectStorage  | Stefano |
| 14:45 | 15:00 | Closing Remarks | Grigory |
--->

## Various Workshop materials and walkthroughs
---

**The data used on this workshop is available on Grex and on MC:**

To copy the data to user's account on MC, use one of the following:
{{< highlight bash >}}
cp -r /home/shared/ws-may2026 ~/
or
cp -r /home/shared/ws-may2026 $SCRATCH
or
cp -r /home/shared/ws-may2026 /project/60004/$USER
{{< /highlight >}}

The first line will copy the directory __ws-may2026__ from the shared directory __/home/shared__ to the user's home directory. The second command will make a copy to the user's scratch and the third one to the project.

To copy the data to user's account on Grex, use one of the following:

{{< highlight bash >}}
cp -r /global/software/ws-may2026 ~/
or
cp -r /global/software/ws-may2026 path-to-your-project-directory
{{< /highlight >}}

The first line will copy the directory __ws-may2026__ from the directory __/global/software/ws-may2026__ to the user's home directory. The second command will make a copy to the project directory. Please replace __path-to-your-project-directory__ by the appropriate path to your project directory on Grex.

{{< treeview display="file" />}}

<!-- Changes and update:
* Last revision: May 12, 2026.
-->
