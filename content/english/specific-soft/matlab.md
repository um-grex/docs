---
weight: 1000
linkTitle: "MATLAB"
title: "Running MATLAB on Grex"
description: "Everything you need to know to run MATLAB on Grex."
categories: ["Software", "Scheduler"]
#tags: ["Configuration"]
---

## Introduction
---

[MATLAB](http://www.mathworks.com/) is a general-purpose high-level programming package for numerical work such as linear algebra, signal processing and other calculations involving matrices or vectors of data. We have a campus license for MATLAB which is used on Grex and other local computing resources. <!-- MATLAB is available only for UManitoba users.-->

As with most of the Grex software, MATLAB is available as a module. The following command will load the latest version available on Grex:

{{< highlight bash >}}
module load matlab
{{< /highlight >}}

Then the **matlab** executable will be in the PATH.

To see all the available versions, use:

{{< highlight bash >}}
module spider matlab
{{< /highlight >}}

## Available Toolboxes
---

To see a list of the MATLAB toolboxes available with MATLAB license on Grex, for a given MATLAB version, use the following command: 

{{< highlight bash >}}
module load matlab
matlab -nodisplay -nojvm -batch "ver"
{{< /highlight >}}

## Running Matlab
---

It is possible to run MATLAB GUI interactively, for best performance in a [remote OOD Desktop](ood) session.
There is no menu shortcuts for Desktops for MATLAB, so a Terminal window has to be open and the matlab _module_ loaded in it. 
After loading the module, the command **matlab** will be in the PATH. 

[OOD](ood) also provides two versions of Matlab-specifc Interactive Applications: MatlabDesktop and MatlabServer. These apps would start a Matlab user interface directly.

The interactive / GUI access is very useful for debugging your Matlab code and for using Matlab to visualize your data. However, for production calculations that may take long time, or significant resources, or a large number of short Matlab tasks, we recommend using Matlab in batch mode. For running a MATLAB script in text mode, and/or a batch script, the following options can be used:

{{< highlight bash >}}
module load matlab
# to run a script your_matlab_script.m in batch mode, use -batch with the name only, no extension:
matlab -nodisplay -nojvm -nodesktop -nosplash -batch your_matlab_script
{{< /highlight >}}

Here is an example of a SLURM job script to submit a job that uses serial MATLAB in batch mode:

{{< collapsible title="Script example for running serial MATLAB in batch mode" >}}
{{< snippet
    file="scripts/jobs/matlab/run-matlab-job.sh"
    caption="run-matlab-job.sh"
    codelang="bash"
/>}}
{{< /collapsible >}}

Here is an example of a SLURM job script to submit a job that uses parallel (threaded) MATLAB in batch mode:

{{< collapsible title="Script example for running parallel (threaded) MATLAB in batch mode" >}}
{{< snippet
    file="scripts/jobs/matlab/run-matlab-job-parallel.sh"
    caption="run-matlab-job-parallel.sh"
    codelang="bash"
/>}}
{{< /collapsible >}}

{{< alert type="warning" >}}
The option __-singleCompThread__ is used for parallel (threaded) MATLAB. Please remove it if your code is serial. Use it only for parallel MATLAB and adjust the _\-\-cpus-per-task parameter_ as needed.
{{< /alert >}}

However, when running __matlab__ executable directly, each instance, GUI or command line, will consume a license unit. By submitting sufficiently many MATLAB jobs concurrently, there is a possibility to exhaust the entire University's license pool. Thus, in many cases, it makes sense to use compiled, standalone MATLAB code runners (MCRs) instead (please refer to the MCR section below).

### Using different BLAS/LAPACK in Matlab
---

Matlab relies heavily on linear algebra calculations. By default, Matlab would use Intel MKL for BLAS/LAPACK. Since version 2022a, it is possible to configure Matlab to use AMD AOCL libraries (BLIS and FLAME) instead, which may give some performance increase on AMD CPUs.

{{< highlight bash >}}
module load matlab
# lets check which linear algebra libraries are in use
matlab -nodisplay -nojvm  -batch "version -blas, version -lapack"
# the answer should be something like "ans =Intel(R) oneAPI Math Kernel Library"
# lets change to use AOCL 
export BLAS_VERSION=libblis-mt.so
export LAPACK_VERSION=libflame.so
# and check again
matlab -nodisplay -nojvm -batch "version -blas, version -lapack"
# "ans= AOCL-BLIS..., ans= AOCL-libFLAME... "
# When the above environments are set, Matlab would use them instead of MKL
{{< /highlight >}}

More information can be found on this [Matlab Knowledge Base Article](https://uk.mathworks.com/matlabcentral/answers/1672304-how-can-i-use-the-blas-and-lapack-implementations-included-in-amd-optimizing-cpu-libraries-aocl-wi?s_tid=srchtitle).

### Standalone Matlab runners: MCR
---

MATLAB compiler, the **mcc** command can be used to compile a source code (__.m__ file) into a standalone executable. There are couple of important considerations to keep in mind when creating an executable that can be run in the batch oriented, HPC environment. One is that there is no graphical display attached to your session and the other is that the number of threads used by the standalone application must be controlled.

For example, with code __mycode.m__ a source directory __src__, with the compiled files being written to a directory called __deploy__, the following **mcc** command line (at the Linux shell prompt) could be used:

{{< highlight bash >}}
module load matlab
mkdir deploy
cd src
mcc -R -nodisplay -R -singleCompThread -m -v -w enable -d ../deploy mycode.m
{{< /highlight >}}
  
Note the option __-singleCompThread__ has been included to limit the executable to just one computational thread. 

In the deploy directory, an executable __mycode__ will be created along with a script __run_mycode.sh__. These two files should be copied to the target machine where the code is going to be run as a batch job.

Note that for every MATLAB module, there is a corresponding MCR version. The correspondance between the version is shown on the following table:

| MATLAB module    | MCR module     |
| :--------------: | :------------: |
| matlab/R2020B2   | mcr/R2020b     |         
| matlab/R2022A    | mcr/R2022a     |
| matlab/R2023B    | mcr/R2023b     |
| matlab/R2024A    | mcr/R2024a     |
| matlab/R2025A    | mcr/R2025a     |
| matlab/R2026A3   | mcr/R2026a     |

### Example of SLURM script: MCR
---

After the standalone executable mycode and corresponding script __run_mycode.sh__ have been transferred to a directory on the target system on which they will be run, a batch job script needs to be created in the same directory. Here is an example batch job script.

{{< collapsible title="Script example for running MATLAB via MCR" >}}
{{< snippet
    file="scripts/jobs/matlab/run-matlab-mcr.sh"
    caption="run-matlab-mcr.sh"
    codelang="bash"
/>}}
{{< /collapsible >}}

The job is then submitted as any ordinary SLURM job with the sbatch command. See the Running Jobs page for more information. If the above script is called **run-matlab-mcr.sh**, it could be submitted using:

{{< highlight bash >}}
sbatch run-matlab-mcr.sh
{{< /highlight >}}

The specified __-\-time__ and total memory (__-\-mem-per-cpu__) limits should be adjusted to appropriate values for your specific run. The option __-\-partition__ is used to specify the partition to use for running the job. For more information, visit the page [running jobs on Grex](running-jobs)

An important part of the above script is the location of the MATLAB Compiler Runtime (MCR) directory. This directory contains files necessary for the standalone application to run. The version of the MCR files specified must match the version of MATLAB used to compile the code (check the [link](https://www.mathworks.com/matlabcentral/answers/102061-what-is-the-version-of-the-matlab-compiler-runtime-mcr-that-corresponds-to-the-version-of-matlab-c) for matching module and MCR versions).

## MATLAB on the Alliance's clusters
---

For using MATLAB on the Alliance's clusters, please visit the corresponding MATLAB [page](https://docs.alliancecan.ca/wiki/MATLAB). While there is a wide MATLAB license accessible for all users on [Fir](https://docs.alliancecan.ca/wiki/Fir), [Rorqual](https://docs.alliancecan.ca/wiki/Rorqual/en) and [narval](https://docs.alliancecan.ca/wiki/Narval/en), using MATLAB on graham requires access to an external [license](https://docs.alliancecan.ca/wiki/MATLAB#Using_an_external_license). UManitoba users could use MATLAB on [Nibi](https://docs.alliancecan.ca/wiki/Nibi) without additional settings.   

## Related links
---

* [MATLAB on the Alliance's clusters](https://docs.alliancecan.ca/wiki/MATLAB)
* MATLAB [documentation](https://www.mathworks.com/help/matlab/)
* [Running jobs on Grex](running-jobs)

---

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last revision: May 14 28, 2025. 
-->
