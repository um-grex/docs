---
weight: 1500
linkTitle: "Running jobs on Grex"
Title: "Running jobs on Grex"
description: "Workshops and Training Material - 2026 - Running jobs"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
draft: true
#build:
# list: false
# render: false
---

## Introduction
---

To run jobs on the cluster, a scheduler is used. In this section, different job scripts are presented. There are specified directive for each type of application: serial, openmp, mpi, hybrid and gpu.
 
## Interactive job via salloc
---

{{< alert type="info" >}}
On this example, we will test lammps interactively via salloc. All the scripts and instructions are under the directory __interactive__.
{{< /alert >}}

**First, ask for interactive job using salloc:**

{{< highlight bash >}}
salloc --nodes=1 --ntasks=2 --cpus-per-task=1 --mem-per-cpu=500M --time=15:00
{{< /highlight >}}

If needed, add other options to salloc command: __salloc {+other options}__ 

**Once the job is granted, run the commands:**

{{< highlight bash >}}
hostname
env | grep SLURM
sq
{{< /highlight >}}

The above will show the name of the node, all slurm environment variables and the current jobs under your name.
 
**Before starting the test, load lammps module:**

__HINT:__ use __module spider lammps__
 
{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0  openmpi/4.1.6 lammps/2024-08-29p1
{{< /highlight >}}

**After loading the modules, run the commands:**

{{< highlight bash >}}
module list
module show lammps
ls $MODULE_LAMMPS_PREFIX/bin
{{< /highlight >}}

**Make show that the binary lmp is available in your environment:**

{{< highlight bash >}}
which lmp
/global/software/alma8/sb/opt/arch-avx512-gcc-13.2.0-openmpi-4.1.6/lammps/2024-08-29p1/bin/lmp
{{< /highlight >}}

**Now, run the lammps test using:**

{{< highlight bash >}}
srun lmp -in lammps-input.in
{{< /highlight >}}

or run it via a script:

{{< highlight bash >}}
sh ./grex-runlmp-interactive.sh
{{< /highlight >}}

{{< alert type="info" >}}
The above shows how to run interactive job for testing and debugging. In case you have many commands, you can bundle them inside a script as in the above.
{{< /alert >}}

As a summary, here are the steps to follow for running interactive jobs to test and debug your progrqams and scripts before submitting jobs via sbatch:

* salloc {+options} to ask for compute node.
* load the modules that are needed for your workflow.
* run tests and debug ...
* exit {to resume the interactive job} and go back to the login node.

## Sleep job
---

{{< alert type="info" >}}
On this example, we will test a sleep job submitted via sbatch. All the scripts and instructions are under the directory __sleep-job__. In addition to running sleep command, the job prints also the slurm environment variables.
{{< /alert >}}

**Under the directory sleep-job, use cat command to see the content of the script:**

{{< highlight bash >}}
cd sleep-job
cat grex-sleep-job.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-sleep-job.sh
{{< /highlight >}}

* If there are errors, fix them and/or add the options via cammnd line, like:

{{< highlight bash >}}
sbatch --partition=genoa grex-sleep-job.sh
{{< /highlight >}}

* What is the job id for your job?

* See if your job is on the queue by running the command: sq

* Once the job is done, inspect the output: slurm-<JobID>.out

## Serial job
---

{{< alert type="info" >}}
On this example, we will test lammps using a serial job via sbatch. All the scripts and instructions are under the directory __serial-job__.
{{< /alert >}}

**Under the directory serial-job, use cat command to see the content of the script:**

{{< highlight bash >}}
cd serial-job
cat grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

## OpenMP job
---

{{< alert type="info" >}}
On this example, we will test lammps using OpenMP job via sbatch. All the scripts and instructions are under the directory __openmp-job__.
{{< /alert >}}

**Under the directory openmp-job, use cat command to see the content of the scripts:**

{{< highlight bash >}}
cd openmp-job
cat grex-runlmp-2cpu-openmp.sh
cat grex-runlmp-4cpu-openmp.sh
cat grex-runlmp-8cpu-openmp.sh
cat grex-runlmp-16cpu-openmp.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-2cpu-openmp.sh
sbatch grex-runlmp-4cpu-openmp.sh
sbatch grex-runlmp-8cpu-openmp.sh
sbatch grex-runlmp-16cpu-openmp.sh
{{< /highlight >}}

**Monitor your jobs and inspect the oupt files:**

{{< highlight bash >}}
sq
sq -j <JOB ID>
{{< /highlight >}}

## MPI job
---

{{< alert type="info" >}}
On this example, we will test lammps using MPI job via sbatch. All the scripts and instructions are under the directory __mpi-job__.
{{< /alert >}}

**Under the directory mpi-job, use cat command to see the content of the scripts:**

{{< highlight bash >}}
cd mpi-job
cat grex-runlmp-16cpu-mpi.sh
cat grex-runlmp-2cpu-mpi.sh
cat grex-runlmp-4cpu-mpi.sh
cat grex-runlmp-8cpu-mpi.sh
cat grex-runlmp-32cpu-mpi-4nodes.sh
cat grex-runlmp-32cpu-mpi.sh
cat grex-runlmp-64cpu-mpi.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-16cpu-mpi.sh
sbatch grex-runlmp-2cpu-mpi.sh
sbatch grex-runlmp-4cpu-mpi.sh
sbatch grex-runlmp-8cpu-mpi.sh
sbatch grex-runlmp-32cpu-mpi-4nodes.sh
sbatch grex-runlmp-32cpu-mpi.sh
sbatch grex-runlmp-64cpu-mpi.sh
{{< /highlight >}}

## Hybrid job (MPI+OpenMP)
---

{{< alert type="info" >}}
On this example, we will test lammps using hybrid job (MPI and OpenMP) via sbatch. All the scripts and instructions are under the directory __hybrid-job__.
{{< /alert >}}

**Under the directory hybrid-job, use cat command to see the content of the scripts:**

{{< highlight bash >}}
cd hybrid-job
cat grex-runlmp-16tasks-2threads.sh
cat grex-runlmp-32cpu-mpi.sh
cat grex-runlmp-8tasks-4threads.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-16tasks-2threads.sh
sbatch grex-runlmp-32cpu-mpi.sh
sbatch grex-runlmp-8tasks-4threads.sh
{{< /highlight >}}

**The above will submit lamms using a total of 32 core:**

- grex-runlmp-16tasks-2threads.sh
      16 MPI Tasks and 2 Threads/Task ==> A total of 32 cores.
- grex-runlmp-32cpu-mpi.sh
      32 MPI Tasks and 1 Thread/Task  ==> A total of 32 cores.
- grex-runlmp-8tasks-4threads.sh
       8 MPI Tasks and 4 Threads/Task ==> A total of 32 cores.

## GPU job
---

{{< alert type="info" >}}
On this directory, there are two example: the first one is for running lammps on GPU using singularity and the second uses apptainer. 
{{< /alert >}}

** First, pull the container:**

On Grex, we use singularity:

{{< highlight bash >}}
module load singularity
singularity pull docker://nvcr.io/hpc/lammps:patch_3Nov2022
{{< /highlight >}}

On CC/MC, we use apptainer:

{{< highlight bash >}}
module load apptainer
apptainer pull docker://nvcr.io/hpc/lammps:patch_3Nov2022
{{< /highlight >}}

The above will build the image lammps_patch_3Nov2022.sif (about 560M) that can be used to run lammps.

Here is an example of script to run lammps with this container on Grex:

{{< collapsible title="Example of a job script to LAMMPS using singularity." >}}
{{< highlight bash >}}
#!/bin/bash

#SBATCH --gpus=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --mem=12000M
#SBATCH --time=0-1:00:00
#SBATCH --partition=livi-b
#SBATCH --job-name=GPU

#Load the modules:

module load singularity
module load cuda/12.9.1

echo "Starting run at: `date`"

singularity run --nv -B $PWD:/host_pwd --pwd /host_pwd ./lammps_patch_3Nov2022.sif ./run_lammps.sh

echo "Program finished with exit code $? at: `date`"
{{< /highlight >}}
{{< /collapsible >}}

The above job script uses a bash script __run_lammps.sh__ where the command line for running lammps is added.

{{< highlight bash >}}
#!/bin/bash

gpu_count=1
input="in.lj"

echo "Running Lennard Jones 8x4x8 example on ${gpu_count} GPUS..."

mpirun -n ${gpu_count} lmp -k on g ${gpu_count} -sf kk -pk kokkos cuda/aware on neigh full comm device binsize 2.8 -var x 8 -var y 4 -var z 8 -in ${input} -log output_lammps-gpu-${SLURM_JOBID}.txt

{{< /highlight >}}

The job requires an input file __in.lj__:

{{< collapsible title="Example of input file used to run LAMMPS." >}}
{{< highlight bash >}}
# 3d Lennard-Jones melt

units		lj
atom_style	atomic

lattice		fcc 0.8442
region		box block 0 200 0 200 0 200
create_box	1 box
create_atoms	1 box
mass		1 1.0

velocity	all create 3.0 87287

pair_style	lj/cut 2.5
pair_coeff	1 1 1.0 1.0 2.5

neighbor	0.3 bin
neigh_modify	every 20 delay 0 check no

fix		1 all nve

thermo		25

run		20000

#write_data     config.end_melt

# End of the Input file.
{{< /highlight >}}
{{< /collapsible >}}

<br>

{{< alert type="info" >}}
The same scripts can be adapted and used to run LAMMPS using apptainer.
{{< /alert >}}

**Note__: On Grex, it is also possible to use podman and pyxis.

## Example of time-out job
---

{{< alert type="info" >}}
On this example, we will test lammps using a serial job via sbatch and asking for very short time. All the scripts and instructions are under the directory __time-out-job__. The goal is to reproduce the __TIMEOUT__ message for the job.
{{< /alert >}}

**Under the directory time-out-job, use cat command to see the content of the scripts:**

{{< highlight bash >}}
cd time-out-job
cat grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

**Once the job is done, run the commands:**

{{< highlight bash >}}
squeue -j <JOB ID>
cat Slurm-<JOB ID>.out
{{< /highlight >}}

**Ask for more time and resubmit the job:**

{{< highlight bash >}}
sbatch --time=1:00:00 grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

Alternatively, edit the job script and increase the time:

**#SBATCH --time=0-1:00:00**

and re-submit the job.
 
## Example of out of memory kill job
---

{{< alert type="info" >}}
On this example, we will test lammps using a serial job via sbatch and asking for less memory. All the scripts and instructions are under the directory __oom-kill-job__. The goal is to reproduce the __oom-kill event__ for the job.
{{< /alert >}}

**Under the directory oom-kill-job, use cat command to see the content of the scripts:**

{{< highlight bash >}}
cd oom-kill-job
cat grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

**Once the job is done, run the commands:**

{{< highlight bash >}}
squeue -j <JOB ID>
cat Slurm-<JOB ID>.out
{{< /highlight >}}

**Ask for more memory and re-submit the job:**

{{< highlight bash >}}
sbatch --mem=3000M grex-runlmp-1cpu-serial.sh
{{< /highlight >}}

Alternatively, edit the job script and increase the time:

**#SBATCH --mem=3000M**

and re-submit the job.

## Performance of MPI and OpenMP jobs
---

{{< alert type="info" >}}
On this example, we will test lammps using MPI and/or OpenMP to compare the performance and how the program scales with the number of CPU. All the scripts and instructions are under the directory __performance__.
{{< /alert >}}

While using parallel programs (OpenMP and/or MPI based applications), it is highly recommended to test how a program scales with number of CPUs. It is well known that increasing the number of CPUs for OpenMP based programs do not increase the performance of the code. The idea is to take a small test case and run it using different number of CPUs: 1, 2, 4, 8, ... etc. While MPI programs run across the nodes and use multiple CPUs, OpenMP codes run only on one node. Therefore, the maximum threads to use should not exceed the number of physical cores available on the node. 

The command __seff__ can be used to see the CPU efficiency.

For this example, we used LAMMPS and run it on Grex using OpenMP and OpenMPI. This code prints at the end of the run, the performance of the simulation in terms of __Tau/day__ or __ns/day__ and/or __TimeStep/Second__.     

**Tests using OpenMP:**

| Job |CPUs | Tau/day    | TimeStep/Second | CPU      | Wall-clock time |
| :-: |:--: | :--------: | :-------------: | :------: |:--------------: |
| 01  | 1   |  32688.292 |  75.667         |  99.43%  | 00:44:05 |
| 02  | 2   |  68614.701 | 158.830         |  99.37%  | 00:21:03 |
| 03  | 4   | 132718.066 | 307.218         |  99.05%  | 00:10:55 |
| 04  | 8   | 158637.644 | 367.217         |  98.93%  | 00:09:08 |
| 05  |16   | 278099.343 | 643.748         |  96.96%  | 00:05:19 |
| 06  |32   | 274380.425 | 635.140         |  98.26%  | 00:05:19 |
| 07  |**64**   | **195418.938** | **452.359**         |  98.69%  | 00:07:23 |

**Tests using MPI:**

| Job |CPUs | Tau/day     | TimeStep/Second | CPU      | Wall-clock time |
| :-: |:--: | :--------:  | :-------------: | :------: |:--------------: |
| 01  |  1  |   29112.557 |   67.390        |  99.33%  | 00:49:32  |        
| 02  |  2  |   56738.337 |  131.339        |  99.25%  | 00:25:27  |
| 03  |  4  |  111917.318 |  259.068        |  99.03%  | 00:12:56  |
| 04  |  8  |  194286.703 |  449.738        |  98.58%  | 00:07:29  |       
| 05  | 16  |  440557.932 | 1019.810        |  97.23%  | 00:03:21  |
| 06  | 32  |  730610.193 | 1691.227        |  97.03%  | 00:02:02  |       
| 07  | 64  | 1464824.869 | 3390.798        |  95.19%  | 00:01:03  |
| 08  | 72  | 2214538.501 | 5126.247        |  96.03%  | 00:00:42  |

## Running jobs using job-arrays
---

{{< alert type="info" >}}
On this example, we will test lammps to run job array for running multiple copies of the job with different parameters. All the scripts and instructions are under the directory __array-job__. 
{{< /alert >}}

### Jobs from a single directory

On this example, we will submit multiple jobs from the same directory to run lammis with multiple input files: lammps-input-X.in where X=0,...,9

{{< alert type="warning" >}}
In this case, make sure that the output files do not overlap. That's why we used __output_lammps-array-${SLURM_ARRAY_TASK_ID}-${SLURM_JOBID}.txt__ as output. The environment variable __SLURM_ARRAY_TASK_ID__ will be used to name the output accourding to the indices used. 
{{< /alert >}}

The command line used in this case is:

{{< highlight bash >}}
lmp -in lammps-input-${SLURM_ARRAY_TASK_ID}.in -log output_lammps-array-${SLURM_ARRAY_TASK_ID}-${SLURM_JOBID}.txt
{{< /highlight >}}

**First, inspect the script using cat command:**

{{< highlight bash >}}
cat grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

Alternatively, remove the directive __#SBATCH --array=0-9__ from the job script and use the following command to submit the job:

{{< highlight bash >}}
sbatch --array=0-9 grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}
 
**Other possibilities to submit array jobs:**

{{< highlight bash >}}
sbatch --array=0-9%2 grex-runlmp-1cpu-jobarray.sh
sbatch --array=0,2,4-9 grex-runlmp-1cpu-jobarray.sh
sbatch --array=1,3 grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

- The option __--array=0-9%2__ means that the script will submit an array job with indices __0-9__ and run a maximum of 2 at a time.
- The option __--array=0,2,4-9__ means that the script will submit an array job with indices __0,2__ and all indices between __4__ and __9 (4, 5, 6, 7, 8, 9)__.
 time.
- The option __--array=1,3__ means that the script will submit an array job with indices __1__ and __3__. time.

### Jobs on multiple directories

To avoid data overlapping, it is possible to create sub-directories and stage the input files for running the job with different parameters. In this case, there is no need to rename the outut files as they are generated in separate directories.

Here, we use directories with the name __Test_X__ where X=0,...,9 and add a corresponding input file. 

In the job script, we should make sure to change the directory to run the corresponding job for a given value of __SLURM_ARRAY_TASK_ID__

{{< highlight bash >}}
cd Test_${SLURM_ARRAY_TASK_ID}
lmp -in lammps-input.in -log output_lammps-array-${SLURM_ARRAY_TASK_ID}-${SLURM_JOBID}.txt
{{< /highlight >}}

**First, inspect the script using cat command:**

{{< highlight bash >}}
cat grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

**Now, submit the job using:**

{{< highlight bash >}}
sbatch grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

**Note:**

It is also possible to use the alternative options discussued in the previous example:

{{< highlight bash >}}
sbatch --array=0-9%2 grex-runlmp-1cpu-jobarray.sh
sbatch --array=0,2,4-9 grex-runlmp-1cpu-jobarray.sh
sbatch --array=1,3 grex-runlmp-1cpu-jobarray.sh
{{< /highlight >}}

## Running jobs using GLOST
---

{{< alert type="info" >}}
On this example, we will show how run multiple tasks using glost instead of job arrays. All the scripts and instructions are under the directory __glost-job__. 
{{< /alert >}}

Similar to job arrays, GLOST is used to run multiple independent tasks. In this case, we use a list where we add all the tasks. GLOST uses MPI and assign the first N lines of the list to the N CPUs asked for. Once one of these tasks is done, GLOST will assign the next available task in the list till all the tasks are done or the job times out.

**First, inspect the list of tasks and scripts under the directories: multiple-dir and single-dir**

{{< highlight bash >}}
cat single-dir/list_glost_tasks.txt
cat single-dir/grex-run-glost.sh
cat multiple-dir/list_glost_tasks.txt
cat multiple-dir/grex-run-glost.sh
{{< /highlight >}} 

**Now, submit the jobs using:**

{{< highlight bash >}}
pushd single-dir && sbatch grex-run-glost.sh && popd
pushd multiple-dir && sbatch grex-run-glost.sh && popd
{{< /highlight >}}            

## Useful links
---

* [GLOST](https://docs.alliancecan.ca/wiki/GLOST)
* [Job arrays](https://docs.alliancecan.ca/wiki/Job_arrays/en)
* [GNU Parallel](https://docs.alliancecan.ca/wiki/GNU_Parallel/en)
* [Scalability](https://docs.alliancecan.ca/wiki/Scalability/en)
* [Running jobs](https://um-grex.github.io/docs/running-jobs/)
* [Containers](https://um-grex.github.io/docs/software/containers/)

<!-- Changes and update:
* Last revision: May 12, 2026.
-->

