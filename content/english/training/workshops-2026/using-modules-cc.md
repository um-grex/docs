---
weight: 1500
linkTitle: "Modules on CC"
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
How to use modules on national systems (like Fir, Nibi, Rorqual and Narval)? As examples, we show how to load gromacs and lammps.
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

## How to load gromacs on national systems?
---

**First, run the command __module spider gromacs__ to search if gromacs is already available as a module:**

{{< highlight bash >}}
module spider gromacs
{{< /highlight >}}

**The above shows that many versions are available:**

- gromacs/2016.6
- gromacs/2020.4
- gromacs/2020.6
- gromacs/2021.2
- gromacs/2021.4
- gromacs/2021.6
- gromacs/2022.2
- gromacs/2022.3
- gromacs/2023
- gromacs/2023.2
- gromacs/2023.3
- gromacs/2023.5
- gromacs/2024.1
- gromacs/2024.4
- gromacs/2024.6
- gromacs/2025.4
- gromacs/2026.1

**Now, try to load a particular version 2025.4:** 

To see how to load this particular version, run the command:

{{< highlight bash >}}
module spider gromacs/2025.4
{{< /highlight >}}

and read the instructions.

**Two modules are available:**

- StdEnv/2023  gcc/12.3  openmpi/4.1.5
- StdEnv/2023  gcc/12.3  openmpi/4.1.5  cuda/12.6

**Load one of the following modules:**

- For __CPU__ version, use: 

{{< highlight bash >}}
module load StdEnv/2023  gcc/12.3  openmpi/4.1.5 gromacs/2025.4
{{< /highlight >}}

- for __GPU__ version, use:

{{< highlight bash >}}
module load StdEnv/2023 gcc/12.3 openmpi/4.1.5 cuda/12.6 gromacs/2025.4 
{{< /highlight >}}

**Load one module and experiment with the commands:**

{{< highlight bash >}}
module list
module show gromacs
ls ${EBROOTGROMACS}/bin
which gmx_mpi
module rm gromacs
module show gromacs
which gmx_mpi
{{< /highlight >}}

{{< alert type="info" >}}
While running the command __module show gromacs__, have a look to the environment variables, like __EBROOTGROMACS__ that points to t
he installation directory.
{{< /alert >}}

{{< alert type="warning" >}}
Note that __gmx_mpi__ is a binary used to run gromacs. For another program, you should know the binaries to use.
{{< /alert >}}

## How to load LAMMPS on national clusters?
---

**First, run the command __module spider lammps__ to search if lammps is already available as a module:**

{{< highlight bash >}}
module spider lammps
{{< /highlight >}}

**The above shows that many versions are available:**

- lammps-omp/20201029
- lammps-omp/20210929
- lammps-omp/20220623
- lammps-omp/20230802
- lammps-omp/20240829
- lammps-omp/20250722

**Now, try to load a particular version 20240829:**

To see how to load this particular version, run the command:

{{< highlight bash >}}
module spider lammps-omp/20240829
{{< /highlight >}}

and read the instructions.

**One module is available:**

{{< highlight bash >}}
module load StdEnv/2023  intel/2023.2.1  openmpi/4.1.5 lammps-omp/20240829
{{< /highlight >}}

**Load the module and experiment with the commands:**

{{< highlight bash >}}
module list  
module show lammps
ls ${EBROOTLAMMPS}/bin
which lmp
module rm lammps
module show lammps
which lmp
{{< /highlight >}}

{{< alert type="info" >}}
While running the command __module show lammps__, have a look to the environment variables, like __EBROOTLAMMPS__ that points to t
he installation directory.
{{< /alert >}}

{{< alert type="warning" >}}
Note that __lmp__ is a binary used to run lammps. For another program, you should know the binaries to use. 
{{< /alert >}}

## Experiment with other programs
---

{{< alert type="info" >}}
For more examples, experiment with other programs, like boost, netcdf, python, openmm, geant4 or any other program you have in mind.
{{< /alert >}}

## Quick test using LAMMPS
---

{{< alert type="info" >}}
Here, we show how to run a quick test of lammps on the login nodes in different modes: serial, OpenMP, MPI and Hybrid.         
{{< /alert >}}

**Here are the instructions to run a quick test using LAMMPS on national system:**

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

**First, connect to a cluster {MC or any national cluster you have access to}.**

**Search for lammps modules:**

- Module "lammps-omp/20240829".
- Use module spider to see how to load this module.

{{< highlight bash >}}
module purge
module spider lammps-omp/20240829
{{< /highlight >}}

**Then load the module "lammps-omp/20240829**

{{< highlight bash >}}
module load StdEnv/2023 intel/2023.2.1 openmpi/4.1.5 lammps-omp/20240829
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
sh ./mc-runlmp-1cpu-serial.sh
sh ./mc-runlmp-2cpu-openmp.sh
sh ./mc-runlmp-2cpu-mpi.sh
sh ./mc-runlmp-4cpu-openmp.sh
sh ./mc-runlmp-4cpu-mpi.sh
sh ./mc-runlmp-8cpu-2mpi-4openmp.sh  
sh ./mc-runlmp-8cpu-4mpi-2openmp.sh
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

* [Using modules](https://docs.alliancecan.ca/wiki/Utiliser_des_modules/en) on national clusters.
* [Running Lammps](https://docs.alliancecan.ca/wiki/LAMMPS) on national clusters.
* [Running GROMACS](https://docs.alliancecan.ca/wiki/GROMACS/en) on national clusters.

<!-- Changes and update:
* Last revision: May 12, 2026.
-->
