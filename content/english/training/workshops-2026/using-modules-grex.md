---
weight: 1500
linkTitle: "Modules on Grex"
Title: "Working with modules"
description: "Workshops and Training Material - 2026 - Using modules"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

# Introduction
---

{{< alert type="info" >}}
How to use modules on Grex? As examples, we show how to load gromacs and lammps. 
{{< /alert >}}

## Modules
---

**Here are the most used commands for manipulating modules:**

* module __list__
* module __avail__
* module __spider__ soft/version
* module __load__ soft/version
* module __unload__ {__rm__} soft/version
* module __show__ soft
* module __help__ soft
* module __whatis__ soft
* module __purge__; module __--force purge__
* module __use__ ~/modulefiles; module __unuse__ ~/modulefiles

**To see if a given software or program, called foo, is available on the software stach, run the command:**

{{< highlight bash >}}
module spider foo
{{< /highlight >}}

## How to load gromacs on Grex?
---

**First, run the command __module spider gromacs__ to search if gromacs is already available as a module:**

{{< highlight bash >}}
module spider gromacs
{{< /highlight >}}

**The above shows that many versions are available:**

- gromacs/2021.6
- gromacs/2022
- gromacs/2023.3
- gromacs/2024.1
- gromacs/2025.2
- gromacs/2025.3

**Now, try to load a particular version 2025.3:**

To see how to load this particular version, run the command:

{{< highlight bash >}}
module spider gromacs/2025.3
{{< /highlight >}}

and read the instructions.

**Two modules are available:**

{{< highlight bash >}}
arch/avx512  gcc/13.2.0  openmpi/5.0.6
cuda/12.4.1  arch/avx2  gcc/13.2.0
{{< /highlight >}}

**Load one of the following modules:**

- For __CPU__ version, use: 

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0 openmpi/5.0.6 gromacs/2025.3
{{< /highlight >}}

- for __GPU__ version, use:

{{< highlight bash >}}
module load cuda/12.4.1  arch/avx2  gcc/13.2.0 gromacs/2025.3 gromacs/2025.3
{{< /highlight >}}

**Load one module and experiment with the commands:**

{{< highlight bash >}}
module list
module show gromacs
ls ${MODULE_GROMACS_PREFIX}/bin
which gmx_mpi
module rm gromacs
module show gromacs
which gmx_mpi
{{< /highlight >}}

{{< alert type="info" >}}
While running the command __module show gromacs__, have a look to the environment variables, like __GROMACS_PREFIX__ that points to t
he installation directory.
{{< /alert >}}

{{< alert type="warning" >}}
Note that __gmx_mpi__ is a binary used to run gromacs. For another program, you should know the binaries to use.
{{< /alert >}}

## How to load LAMMPS on Grex?
---

**First, run the command __module spider lammps__ to search if lammps is already available as a module:**

{{< highlight bash >}}
module spider lammps
{{< /highlight >}}

**The above shows that many versions are available:**

- lammps/2021-09-29
- lammps/2024-08-29p1-nep
- lammps/2024-08-29p1

**Now, try to load a particular version 2024-08-29p1:**

To see how to load this particular version, run the command:

{{< highlight bash >}}
module spider lammps/2024-08-29p1
{{< /highlight >}}

and read the instructions.

**Three modules are available:**

- arch/avx512  gcc/13.2.0  openmpi/4.1.6
- arch/avx512  intel-one/2024.1  openmpi/4.1.6
- cuda/12.4.1  arch/avx2  gcc/13.2.0  openmpi/4.1.6

**Load one of the following modules:**

- __CPU__ version built with GCC:

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0  openmpi/4.1.6 lammps/2024-08-29p1
{{< /highlight >}}

- __CPU__ version built with Intel vompiler:

{{< highlight bash >}}
module load arch/avx512  intel-one/2024.1  openmpi/4.1.6 lammps/2024-08-29p1
{{< /highlight >}}

- __GPU__ version:

{{< highlight bash >}}
module load cuda/12.4.1  arch/avx2  gcc/13.2.0  openmpi/4.1.6 lammps/2024-08-29p1
{{< /highlight >}}

**Load one module and experiment with the commands:**

{{< highlight bash >}}
module list  
module show lammps
ls ${MODULE_LAMMPS_PREFIX}/bin
which lmp
module rm lammps
module show lammps
which lmp
{{< /highlight >}}

{{< alert type="info" >}}
While running the command __module show lammps__, have a look to the environment variables, like __MODULE_LAMMPS_PREFIX__ that points to the installation directory.
{{< /alert >}}

{{< alert type="warning" >}}
Note that __lmp__ is a binary used to run lammps. For another program, you should know the binaries to use.
{{< /alert >}}

## Experiment with other programs
---

{{< alert type="info" >}}
For more examples, experiment with other programs, like boost, netcdf, python, openmm, geant4 or any other program you have in mind.
{{< /alert >}}

## CVMFS on Grex
---

**How to switch to CCEnv which provides the same software stack installed on national clusters?**

For the first time, you may need to run the commands:

{{< highlight bash >}}
ls /cvmfs/
ls -1 /cvmfs/
{{< /highlight >}}

to see what directories are available.

{{< highlight bash >}}
[~@bison ~]$ ls -1 /cvmfs/
cvmfs-config.computecanada.ca
neurodesk.ardc.edu.au
oasis.opensciencegrid.org
singularity.opensciencegrid.org
soft.computecanada.ca
{{< /highlight >}}

**To switch to CCEnv, use the following:

{{< highlight bash >}}
module load CCEnv
module load arch/avx512
module load StdEnv/2023
{{< /highlight >}}

The above will ensure that the same software installed on national systems under the environment __StdEnv/2023__ is mounted on Grex.

Fo example, to load __geant4/11.3.0__ on national systems, one should use:

{{< highlight bash >}}
module load StdEnv/2023  gcc/12.3 geant4/11.3.0
{{< /highlight >}}

To load the same module on Grex, one should use:

{{< highlight bash >}}
module load CCEnv
module load arch/avx512
module load StdEnv/2023
module load gcc/12.3 geant4/11.3.0
{{< /highlight >}}

**How to mount neurodesk.ardc.edu.au on Grex?**

From the login node, run the commands:

{{< highlight bash >}}
[~@yak ~]$ ls -1 /cvmfs/
cvmfs-config.computecanada.ca
restricted.computecanada.ca
soft.computecanada.ca
{{< /highlight >}}

The first timje, we run __ls -1 /cvmfs/__, the repo __neurodesk.ardc.edu.au__ did not show up. The directories under __cvmfs__ are auto-mounted. It means, they should be first accessed using __ls__ for example:

{{< highlight bash >}}
[~@yak ~]$ ls /cvmfs/neurodesk.ardc.edu.au
[~@yak ~]$ ls -1 /cvmfs
cvmfs-config.computecanada.ca  
neurodesk.ardc.edu.au   
restricted.computecanada.ca  
soft.computecanada.ca
{{< /highlight >}}

Now, the repo shows up under __/cvmfs__

To use the modules from this repo, one should source the modulefiles directory:

{{< highlight bash >}}
[~@yak ~]$ ls /cvmfs/neurodesk.ardc.edu.au/neurodesk-modules
[~@yak ~]$ module use /cvmfs/neurodesk.ardc.edu.au/neurodesk-modules
{{< /highlight >}}

Now, let's search for a module called FSL that is availbale on this repo:

{{< highlight bash >}}
[~@yak ~]$ module spider fsl
[~@yak ~]$ module spider functional_imaging/fsl/6.0.7.18
[~@yak ~]$ module load functional_imaging/fsl/6.0.7.18
{{< /highlight >}}

This module relies on singularity:

{{< highlight bash >}}
[~@yak ~]$ module load singularity
[~@yak ~]$ module list
[~@yak ~]$ which fsl
/cvmfs/neurodesk.ardc.edu.au/containers/fsl_6.0.7.18_20250928/fsl
https://neurodesk.org/getting-started/neurocontainers/cvmfs/
{{< /highlight >}}

{{< alert type="info" >}}
There are other repositories available under cvmfs but they need to be listed first to be able to see them. They are auto-mounted.
{{< /alert >}}

## Quick test using LAMMPS
---

{{< alert type="info" >}}
Here, we show how to run a quick test of lammps on the login nodes in different modes: serial, OpenMP, MPI and Hybrid. 
{{< /alert >}}

**Here are the instructions to run a quick test using LAMMPS on Grex:**

**Requireements:** 

- access to login or compute node via salloc.
- access to a module (lammps in this case)
- Input file: lammps-input.in

{{< collapsible title="Example of input file used to run LAMMPS." >}}
{{< highlight bash >}}
# 3d Lennard-Jones melt
  
units           lj
atom_style      atomic

lattice         fcc 0.8442
region          box block 0 10 0 10 0 10
create_box      1 box
create_atoms    1 box
mass            1 1.0

velocity        all create 3.0 87287

pair_style      lj/cut 2.5
pair_coeff      1 1 1.0 1.0 2.5

neighbor        0.3 bin
neigh_modify    every 20 delay 0 check no

fix             1 all nve

thermo          50

run             10000

#write_data     config.end_melt

# End of the Input file.
{{< /highlight >}}
{{< /collapsible >}}

<br>

**First, connect to a cluster {Grex in this case}**

**Search for lammps modules:**
 
- Module "lammps/2024-08-29p1".
- Use module spider to see how to load this module.

{{< highlight bash >}}
module purge
module spider lammps/2024-08-29p1
{{< /highlight >}}

**Then load the module "lammps/2024-08-29p1**

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0  openmpi/4.1.6 lammps/2024-08-29p1
{{< /highlight >}}

**Make sure that the module is loaded and available in your environment by running**

- The binary for this program is called __lmp__
- See if the binary is in your path by running the command:

{{< highlight bash >}}
module list
which lmp
{{< /highlight >}}
   
**Now try to run the test interactively by invoking the command:**
     
{{< highlight bash >}}
lmp -in lammps-input.in
{{< /highlight >}}

**Before running any script, use cat command to see the content of the script**

{{< highlight bash >}}
cat <your script>
{{< /highlight >}}

**Run the following commands from your terminal**

{{< highlight bash >}}
sh ./grex-runlmp-1cpu-serial.sh
sh ./grex-runlmp-2cpu-openmp.sh
sh ./grex-runlmp-2cpu-mpi.sh
sh ./grex-runlmp-4cpu-openmp.sh
sh ./grex-runlmp-4cpu-mpi.sh
sh ./grex-runlmp-8cpu-2mpi-4openmp.sh
sh ./grex-runlmp-8cpu-4mpi-2openmp.sh
{{< /highlight >}}

**The above will run lammps using:**

- 1 CPU   ==> Serial program
- 2 CPUs  ==> OpenMP program
- 2 CPUs  ==> MPI program
- 4 CPUs  ==> OpenMP program
- 4 CPUs  ==> MPI program
- 8 CPUs  ==> Hybrid mode (2 MPI + 4 OpenMP)
- 8 CPUs  ==> Hybrid mode (4 MPI + 2 OpenMP)

**The above will generate the following output:**

- output_lammps-serial.txt
- output_lammps-openmp-2cpus.txt
- output_lammps-mpi-2cpus.txt
- output_lammps-openmp-4cpus.txt
- output_lammps-mpi-4cpus.txt
- output_lammps-8cpu-2mpi-4openmp.txt
- output_lammps-8cpu-4mpi-2openmp.txt

{{< alert type="warning" >}}
Note that login nodes are not meant for intensive tests. For any memory or CPU intensive test, please user interactive jobs via salloc.
{{< /alert >}}

## Useful links
---

* [Using modules](https://um-grex.github.io/docs/software/using-modules/) on Grex.
* [Running Lammps](https://um-grex.github.io/docs/specific-soft/lammps/) on Grex.
* [Running GROMACS](https://um-grex.github.io/docs/specific-soft/gromacs/) on Grex.

<!-- Changes and update:
* Last revision: May 12, 2026.
-->
