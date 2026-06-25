---
weight: 1600
linkTitle: "Containers"
title: "Containers for Software"
description: "Everything you need to know before to use Singularity containers"
categories: ["Software", "Applications"]
tags: ["Containers", "Singularity", "Docker", "Podman"]
---

## Introduction
---

Linux Containers are means to isolate software dependencies from the base Linux operating system (OS). Several different Linux container engines exist, most notably [Docker](https://www.docker.com) which was first to emerge as the most popular tool in the DevOps community. 

Since then, a lot of work had been done by major Linux players like Google, RedHat and others to develop an open standard for container runtimes, which developed based on Docker, [OCI](https://opencontainers.org/).

There are HPC-specific container engines/runtimes that offer similar or equivalent functionality but allow for easier integration with shared Linux HPC systems. At the time of writing, the most widely used is the [Singularity](https://sylabs.io/guides/3.11/user-guide/) container system, developed by a company called SyLabs, and its fork, a Linux Foundation project called [Apptainer](https://apptainer.org/). 
They are [compatible](https://apptainer.org/docs/user/latest/singularity_compatibility.html) with each other. Singularity/Apptainer provides functionality for running most Docker images by converting them to the Singularity Image format (SIF). However, Singularity/Apptainer own format is [not completely OCI-compatible](https://apptainer.org/docs/user/latest/docker_and_oci.html#differences-and-limitations-vs-docker), so there exists Docker images that would not work properly. 

Finally, recent developments in Linux Kernel namespaces allowed to happen such projects as "rootless Docker" and "rootless [Podman](https://podman.io)" which are more suitable for HPC systems than the original Docker implementation which requires privileged access to the Linux system.

On Grex, Sylabs Singularity-CE is supported on local SBEnv software stack, while Apptainer is supported as part of the ComputeCanada/Alliance CCEnv stack. At the time of writing, these engines can be used largely interchangeably, with both __singularity__ and __apptainer__ commands and/or symlinks available in either engine.

{{< alert type="info" >}}
 __New:__ There is also support for rootless Podman on Grex, for the use cases that require full OCI-compatibility. 
{{< /alert >}}

## Using Singularity from SBEnv on Grex
---

A brief introduction on [getting started with Singularity](https://docs.sylabs.io/guides/latest/user-guide/quick_start.html) can be useful to get started. There is also a good [Singularity 101 Tutorial](https://sylabs.github.io/singularity101/intro.html). You will not need to install Singularity on Grex since it is already provided as a module.

Start with __module spider singularity__; it will list the current version. Due to the nature of container runtime environments, we update Singularity regularly, so the installed version is usually the latest one. Load the module (in the default Grex environment) by the following command:

{{< highlight bash >}}
module load singularity
{{< /highlight >}}

With **singularity** command, one can list singularity commands and their options:

{{< highlight bash >}}
singularity help
{{< /highlight >}}

To execute an application within the container, do it in the usual way for that application, but prefix the command with __singularity exec image_name.sif__ or, if the container has a valid _entry point_, execute it with __singularity run image_name.sif__. 

{{< highlight bash >}}
singularity run docker://ghcr.io/apptainer/lolcow
{{< /highlight >}}

In the example above, Singularity downloads a Docker image from a registry, and runs it instantly. It is advisable, to avoid getting banned by container registries for massive downloads off a single HPC system, to "pull" or "build" containers first as images, and then "run" and "exec" them locally.

{{< highlight bash >}}
singularity pull lolcow_local.sif docker://ghcr.io/apptainer/lolcow
# The above should create a local image lolcow_local.sif 
# Lets run it with singularity
singularity run lolcow_local.sif
{{< /highlight >}}

For another example, to run R on an R script, using an existing container image named R-INLA.sif (INLA is a popular R library installed in the container):

{{< highlight bash >}}
singularity exec ./R-INLA.sif R --vanilla < myscript.R
{{< /highlight >}}
 
Quite often, it is necessary to provide the containerized application with data residing outside of the container image. For running HPC jobs, the data usually resides on a shared filesystem such as __/home__ or __/project__. This is done via [bind mounts](https://docs.sylabs.io/guides/latest/user-guide/bind_paths_and_mounts.html). Normally, the container **bind-mounts** $HOME, /tmp and the current working directory. It is possible to mount a subdirectory, such as _$PWD/workdir_, or an entire filesystem such as _/project_. 
Example below bind-mounts a __./workdir__ folder relative to the current path. The folder must exist before being bind-mounted.

{{< highlight bash >}}
singularity exec -B `pwd`/workdir:/workdir ./R-INLA.sif R --vanilla < myscript.R
{{< /highlight >}}

In case you do not want to mount anything to preserve the containers' environment from any overlapping of data/code from say $HOME, use the __-\-containall__ flag.

Some attention should be paid to Singularity's local cache and temporary directories. Singularity caches the container images it pulls and Docker layers under __$HOME/.singularity__. Containers can be large, in tens of gigabytes, and thus they can easily accumulate and exhaust the users' storage space quota on $HOME. Thus, users might want to set the __SINGULARITY_CACHEDIR__ and __SINGULARITY_TMPDIR__ variables to some place under their __/global/scratch__ space.

For example, to change the location of __SINGULARITY_CACHEDIR__ and __SINGULARITY_TMPDIR__, before building the singularity image, one might run:

{{< highlight bash >}}
mkdir -p /global/scratch/$USER/singularity/{cache,tmp}
export SINGULARITY_CACHEDIR="/global/scratch/$USER/singularity/cache"
export SINGULARITY_TMPDIR="/global/scratch/$USER/singularity/tmp"
{{< /highlight >}}

{{< alert type="warning" >}}
In the above example, the directory refers to __/global/scratch__ which is not available on grex for now. Therefore, we recommend to replace the path __/global/scratch/$USER/singularity__ by a location under your project directory __/home/$USER/projects/def-professor/$USER/singularity__ where _professor_ refers to the user name of your sponsor.
{{< /alert >}}

### Getting and building Singularity images
---

The commands __singularity build__ and/or __singularity pull__ would get pre-built Singularity images from DockerHub, SingularityHub, or SyLabsCloud. “pulling” images does not require elevated permissions (that is, sudo). There are several kinds of container repositories from which containers can be pulled. These repositories are distinguished by the URI string (library://, docker://, oras://, etc.).
It is also possible to convert a local Podman image (using the oci-archive:// URI ) into a Singularity image. Refer to the Podman section of this document below.

{{< highlight bash >}}
module load singularity
# Building Ubuntu image using Sylabs Library
singularity build ubuntu_latest0.sif library://ubuntu
# or using DockerHub, this will create ubuntu_latest.sif
singularity pull docker://ubuntu:latest
# or, using a local definition and --fakeroot option
singularity build --fakeroot My_HPC_container.sif Singularity.def
# or, using a suitable local OCI image from Podman
singularity build busybox.sif oci-archive://busybox.tar
{{< /highlight >}}

Singularity (SIF) images can also be built from other local images, local “sandbox” directories, and from recipes. A [Singularity recipe or definition file](https://apptainer.org/docs/user/main/definition_files.html) is a text file that specifies the base image and post-install commands to be performed on it. However, Singularity-CE requires _sudo_ (privileged) access to build images from recipes, which is not available for users of HPC machines. There are two solutions to this problem.

 * Using remote build on Sylabs cloud with _\-\-remote_ option. This requires [setting up a free account on Sylabs and getting access key](https://cloud.sylabs.io/builder). 
 * Using Singularity-CE or Apptainer (see below) with _\-\-fakeroot_ option.

> Make sure you understand licensing and intellectual property implications before using remote build services!

The _fakeroot_ method appears to be easier and does not require an external account. As of the time of writing, both Apptainer and Singularity-CE support the _fakeroot_ build method. On Grex, there could be differencences between running Apptainer _build_ in an interactive job and on a login node. Running builds in a _salloc_ job is preferred also because login nodes do limit memory and CPU per session.


### Singularity with GPUs
---

Add the __-\-nv__ flag to __singularity__ _run_ / _exec_ / _shell_ commands for the container to be able to access NVidia GPU hardware.
Naturally, your job should be on a node that has a GPU to use GPUs . Check out our [Running Jobs](running-jobs/slurm-partitions) documentation to find out which partitions have the GPU hardware.
NVIDIA provides many pre-built Docker and Singularity container images on their [GPU cloud](https://ngc.nvidia.com/), together with instructions on how to pull them and to run them. NVidia's NGC Docker images should, as a rule, work on HPC machines with Singularity without any changes.

### Singularity with OpenScienceGrid CVMFS
---

We can run Singularity containers distributed with OSG CVMFS which is currently mounted on Grex's CVMFS. The containers are distributed via CVMFS as unpacked directory images. So, the way to access them is to find a directory of interest and point singularity runtime to it. The directories will then be mounted and fetched automatically. The repository starts with __/cvmfs/singularity.opensciencegrid.org/__. Then you'd need an idea from somewhere what you are looking for in the subdirectories of the above-mentioned path. An example (accessing, that is, exploring via __singularity shell__ command, Remoll software distributed through OSG CVMFS by __jeffersonlab__):

{{< highlight bash >}}
module load singularity
singularity shell /cvmfs/singularity.opensciencegrid.org/jeffersonlab/remoll\:develop
{{< /highlight >}}

It looks like the list of what is present on the OSG CVMFS is on GitHub: [OSG GitHub docker images](https://github.com/opensciencegrid/cvmfs-singularity-sync/blob/master/docker_images.txt) .

## Using Apptainer from CCEnv on Grex
---

The Alliance's (formerly ComputeCanada) CCEnv software stack now provides Apptainer modules in the current Standard Environment, _StdEnv/2023_. Apptainer on the CCEnv stack is installed in suid-less mode, and thus can be used off the CVMFS CCEnv stack directly. We recommend always using the most recent Apptainer module available. The only caveat would be to first unload any "singularity" or "apptainer" modules from other software stacks by _module purge_. 

The following commands show how to run the image from the previous example _/R-INLA.sif_:

{{< highlight bash >}}
module purge
module load CCEnv
module load arch/avx512 
module load StdEnv/2023
module load apptainer
# testing if apptainer command works
apptainer version
# running the basic example
apptainer run docker://ghcr.io/apptainer/lolcow
{{< /highlight >}}

Similarly to Singularity, you will need to bind mount the required data directories for accessing data outside the container. 
The same best practices mentioned above for Singularity (pulling containers beforehand, controlling the cache location) equally apply for the Apptainer. The environment variables for Apptainer should be using APPTAINER_ instead of SINGULARITY_ prefixes.

### Building apptainer images with "fakeroot"

Apptainer supports building of SIF images from recipes without __sudo__ access using __-\-fakeroot__ option where available.

On Grex, it can be used as in the following example:

{{< highlight bash >}}
module purge
module load CCEnv
module load StdEnv/2023
module load apptainer
# testing if apptainer command works
apptainer version
# build an image, INLA, from a local Singularity.def recipe
# --fakeroot makes the build possible without elevated access
apptainer build --fakeroot INLA.sif Singularity.def
{{< /highlight >}}

The resulting SIF image should be compatible with either Singularity-CE or Apptainer runtimes.

## Using Podman from SBEnv on Grex
---

[Podman](https://podman.io/) modules are now provided under the default Grex SBEnv environment. On Grex, Podman is configured as _rootless_. Podman is meant to be used by experienced users for jobs that cannot be executed as regular binaries, or through Singularity due to OCI requirements. Grex is an HPC systems, so it is expected that users would be using Podman to run compute jobs rather than persistent services (including and not limited to databases, and network services). Thus, Podman jobs and/or running Podman containers that deemed to be inappropriate for HPC may be terminated without notice.

> It is not recommended to run Podman on login nodes; it should be run only on compute nodes (e.g. using __sbatch__ or __salloc__). 

The only allowed use on login nodes is to pull images before actually starting a job.

Access to the Podman runtime is through a module. Due to the nature of the container runtime environment, we strive to update Podman regularly, so in most cases, the latest installed version must be used:

{{< highlight bash >}}
module load podman
podman version
podman run --rm docker.io/godlovedc/lolcow
{{< /highlight >}}

The above command would pull a container image and run it. 


### Getting and Managing Podman images
---

When using Podman to run a job, we suggest to manually pre-download the required image to avoid wasting time during the job. 
Grex is hosting a Docker Registry proxy/cache locally to improve the download performance, and for avoiding rate limits that can be imposed by various container registries.

{{< highlight bash >}}
module load podman
podman pull <REQUIRED_IMAGE>
{{< /highlight >}}

The command **podman pull _image_name_** would get Podman images from a container registry. 

Images can also be built from other images, or from containerfiles (e.g. Dockerfiles) using the command **podman build _Containerfile_**. 
A _containerfile_ is a text "recipe" that specifies the base image and commands to be run on it. Podman's recipes are compatible with Docker's _Dockerfiles_.
Below is an example of building a container from a source tree of a package called Chemprop. The Dockerfile is provided by the authors.

{{< highlight bash >}}
# Lets try to build a local container from chemprop source directory
module load podman
# change the directory to the source tree where Dockerfile is located
cd chemprop-1.7.1
podman  build -t chemprop .
# this creates a local container image "localhost/chemprop  latest"
{{< /highlight >}}

>Podman, as configured on Grex, by default stores all pulled and locally built images inside the Slurm TMPDIR directory that is local to the node. This means that images will be deleted when the job finishes or get cancelled. 

To list the local _images_, users can take advantage of the following Podman commands:

{{< highlight bash >}}
# list images
podman image ls
# delete an unnecessary image
podman image rm <IMAGE_ID>
{{< /highlight >}}

However, the local images as listed by _image ls_ are local to the compute node they were pulled or created. That is, they are not visible on other nodes, or even the same node when the job ends, and would have to be pulled again or built again from the Containerfile.

To avoid rebuilding the same image every time the job runs, users can take advantage of **podman save** and **podman load** commands that allow for storing the local container as a single image file. Note that these files are large, and it is the user's responsibility to manage their Podman images (delete the old/unused ones)):

{{< highlight bash >}}
# after building an image locally, save it to a file
podman save --format=oci-archive -o ${HOME}/my-local-image.tar <LOCALLY_BUILT_IMAGE>
# when running a job that needs that image, load it
podman load -i ${HOME}/my-local-image.tar
{{< /highlight >}}

Note that it is also possible to convert podman OCI archives into Singularity _.sif_ images as described above, using _oci-archive://my-local-image.tar_ syntax for the source image.

### Podman with User Namespaces

OCI containers may contain multiple users in the container. For example, a Jupyter container would have the _jovian_ user, a Mambaforge container would have the user _mambauser_, etc., in addition to the _root_ user inside container. To properly run such contains with rootless Podman on HPC, an explicit switch __\-\-userns=keepid__ with corresponding userID and groupID mappings has to be provided to Podman.

{{< highlight bash >}}
# run a container with userID mapping for a user with id 1000
# while binding the home directory of the "outside" user
podman run -it --userns=keep-id:uid=1000,gid=1000 -v ${HOME}:${HOME} <REQUIRED_IMAGE>
{{< /highlight >}}

Since the user and group ID in arbitrary containers are not known _a priori_, it is sometimes needed to use the _id_ command when running the container to get the user and group IDs first.

Please refer to the Podman documentation on [User namespace options](https://docs.podman.io/en/v4.4/markdown/options/userns.container.html) for more information.

Namespaces [may cause issues](https://www.redhat.com/en/blog/supplemental-groups-podman-containers) when bind-mounting folders not owned by the user's primary group. Unfortunately, such folders are all the folders of the sponsored users on the _/project_ filesystems, that require access to supplemental groups to be accessible.
If you encounter such issues when using containers with __\-\-keep-id__, please add __\-\-annotation run.oci.keep_original_groups=1__ to the _podman_ command line options.

### Podman with GPUs
---

Use the __-\-device=nvidia.com/gpu=all__ flag to the _podman_ command when running a podman container that needs GPU hardware. 
Naturally, your job should be on a node that has a GPU to use GPUs . Check out our [Running Jobs](running-jobs/slurm-partitions) documentation to find out which partitions have the GPU hardware.
NVIDIA provides many pre-built Docker container images on their [NGC Cloud](https://ngc.nvidia.com/), together with instructions on how to pull and run them. 

Many software developers also put images to various container registries, such as DockerHub or Quay.io, or distribute the sources of containers along with Dockerfiles; all these can be used, but "your mileage may vary". 
Podman would usually run Docker containers without changes to the command line parameters.
It is a good idea to run a test job for a given container and use __nvidia-smi__ command on the node the job runs on to check whether the image is able to utilize the GPU(s).

## Using Pyxis on Grex
---

In addition to Singularity and Podman, it is possible to use Pyxis, a container engine developed by NVidia, to run containers on Grex.

[Pyxis](https://github.com/NVIDIA/pyxis) is a [SPANK](https://slurm.schedmd.com/spank.html) plugin for the Slurm Workload Manager. It allows unprivileged cluster users to run containerized tasks. Being a Slurm plugin, Pyxis extends the Slurm commands with container specific options, as described below, allowing you to execute container images seamlessly in both batch and interactive jobs.

Pyxis provides the following benefits:

> * Seamlessly execute Slurm jobs in unprivileged containers.
> * Simple command line interface.
> * Support for OCI image registries.
> * Support for layers caching and layers sharing across nodes.
> * Supports multi-node MPI jobs through PMI2 or PMIx.
> * Allows users to install packages inside the container.

The main Slurm commands (__sbatch__ and __salloc__ ) options used with Pyxis are:

> * __-\-container-image=IMAGE[:TAG]__ refers to the container image to use.
> * __-\-container-mounts=SRC:DST[:OPTS][,SRC:DST]__ used to bind mount(s) directories inside the container to allow access to data located on the file system.

> * __-\-container-workdir=PATH__ refers to the working directory inside the container.
> * __-\-container-remap-root__ Asks to be remapped to root inside the container. This does not grant elevated system permissions, despite appearances.
> * __-\-container-entrypoint__ Execute the entrypoint from the container image.
> * __-\-container-env=NAME[,NAME]__ Names of environment variables to override with the host environment and set at the entrypoint.

To see other options, run the command __salloc -\-help__ . 

While Pyxis (and the Enroot container engine it uses under the hood) is a generic containerization engine, potentially able to load any OCI-compliant image from any OCI registry, the easiest way to get started with it is to use NVIDIA-made containers from the NGC registry.

### Running GROMACS using Pyxis
---

For this example, we are going to use Pyxis/Enroot to run a GROMACS image **gromacs:2023.2** from the [NVidia NGC Catalog](https://catalog.ngc.nvidia.com/orgs/hpc/containers/gromacs?version=2023.2)

First, let's download the data needed to run this benchmark. The input files are located under the directory __stmv__ that we need to copy to the working directory, for example: /PATH/TO/USERNAME/Pyxis/GROMACS/STMV. 

{{< alert type="warning" >}}
Please replace __/PATH/TO/USERNAME/Pyxis/GROMACS/STMV__ by an appropriate and correct PATH under your project directory.
{{< /alert >}}

{{< highlight bash >}}
wget https://zenodo.org/record/3893789/files/GROMACS_heterogeneous_parallelization_benchmark_info_and_systems_JCP.tar.gz 
tar -xvf GROMACS_heterogeneous_parallelization_benchmark_info_and_systems_JCP.tar.gz 
cd GROMACS_heterogeneous_parallelization_benchmark_info_and_systems_JCP
cp -r stmv /PATH/TO/PROJECT/USERNAME/Pyxis/GROMACS/STMV
{{< /highlight >}}

In the job script, the option __-\-container-mounts__ should be set to the correct path under project where the input data are located:

{{< highlight bash >}}
#SBATCH --container-mounts=/PATH/TO/PROJECT/USERNAME/Pyxis/GROMACS/STMV
{{< /highlight >}}

{{< collapsible title="Script example for running GROMACS using Pyxis on Grex" >}}
{{< snippet
    file="scripts/jobs/pyxis/run-gromacs-pyxis.sh"
    caption="run-gromacs-pyxis.sh"
    codelang="bash"
/>}}
{{< /collapsible >}}

Once the job script __run-gromacs-pyxis.sh__ shown above is adjusted, the job can be submitted using:

{{< highlight bash >}}
sbatch [+some options] run-gromacs-pyxis.sh
{{< /highlight >}}

For more information about running jobs, please refer to this [page](running-jobs/)

<!--
### Running LAMMPS using Pyxis
---

For this example, we are going to use Pyxis/Enroot to run a LAMMPS image **nvcr.io/hpc/lammps:patch_15Jun2023** from the [nVidia NGC Catalog](https://catalog.ngc.nvidia.com/orgs/hpc/containers/lammps/tags?version=patch_15Jun2023)

As an input file, we will use __input-lj-lammps.in__ with the following content:

{{< highlight bash >}}
# 3d Lennard-Jones melt

units		lj
atom_style	atomic

lattice		fcc 0.8442
region		box block 0 200 0 200 0 200
create_box	1 box
create_atoms	1 box
mass		1 1.0

velocity	all create 1.44 87287 loop geom

pair_style	lj/cut 2.5
pair_coeff	1 1 1.0 1.0 2.5

neighbor	0.3 bin
neigh_modify    delay 5 every 1

fix		1 all nve

run		1000000
{{< /highlight >}}
-->

## External links
---

 * [Singularity/Sylabs homepage](https://sylabs.io)
 * [Singularity 101 Tutorial](https://sylabs.github.io/singularity101/intro.html)
 * [Apptainer homepage](https://apptainer.org/)
 * [Podman homepage](https://podman.io/)
 * [Apptainer documentation on the Alliance Wiki](https://docs.alliancecan.ca/wiki/Apptainer)
 * [Docker Hub](https://hub.docker.com)
 * [RedHat Quay.io Hub](https://quay.io/search)
 * [NVIDIA NGC Cloud](https://ngc.nvidia.com/)

<!-- {{< treeview display="tree" />}} -->

<!-- Changes and update:
* Last revision: Jan 24, 2024. 
-->
