---
weight: 1500
linkTitle: "Software on Grex"
Title: "Software installation"
description: "Workshops and Training Material - 2026 - Software installation"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

## Introduction
---

Many programs are installed locally under user's space. Some of these programs are: R packages, Python packages, Perl packages, Julia packages. There is already a minimal installation of R, Python, Perl and Julia as modules that can be used to install the packages a user may need. Other than that, there are programs hat can be installed under user's account or as modules. We do not use sudo and package managers, like __sudo apt-get install <package_name>__. Most of the programs are installed from source or from pre-compiled binaries. The installation is based on one of the following procedures:

- Precompiled programs and binaries: unpack and adjust the parameters where needed.

- Compile source codes using Make: in this case, usally a __Makefile__ is provides with the source files: __make__ or __make -j4__ or __make [some options]__. 

- Compile the codes using: __./configure && make && make test {or check} && make install__ The __./configure__ command takes options like __-\-prefix=[path to the installation directory]__ and generate the __Makefile__ used to build the program. 

- Compile the codes using: __cmake && make && make test {or check} && make install__ The __cmake__ command takes options like __-\-DCMAKE_INSTALL_PREFIX=[path to the installation directory]__ and generate the __Makefile__ used to build the program.

- For other programs, predefined bash scripts or python programs are provided to configure and build the software.

{{< alert type="warning" >}}
In many cases, a user may need to adjust the __cmake__ or __configure__ options and __Makefile__ to be able to install build, test and install the program. 
{{< /alert >}}

**This section describes how to install:**

- R packages

- Python packages

- Perl packages

- Julia packages

- Pre-compiled Java applications 

- Compile a code from source using __make__: __STAR__ as example.

- Configure and ompile a code from source using __./configure && make && make install__: __Treemix__ as example.

- Configure and ompile a code from source using __cmake && make && make install__: __DIAMOND__ as example.

## How to install R packages?
---

{{< alert type="info" >}}
How to install R packages? As examples, install __dplyr__ and __sp__ on Grex?
{{< /alert >}}

**First, use __module spider__ to see how to load R module:**

To load R module, use:

{{< highlight bash >}}
module load arch/avx512 gcc/13.2.0 r/4.4.1+mkl-2024.1
{{< /highlight >}}

**Then, launch R:**

{{< highlight bash >}}
R
{{< /highlight >}}

**Then, install the packages using the following commands:**

{{< highlight bash >}}
> Sys.setenv("DISPLAY"=":0.0")
> install.packages("sp")
> install.packages("dplyr")
{{< /highlight >}}

{{< alert type="info" >}}
The command __Sys.setenv("DISPLAY"=":0.0")__ is not mandatory. It is used to prevent using the GUI mode to select the mirror. With this option, it shows the list of mirrors as text instead of a pop up window that can be very slow over ssh.
{{< /alert >}}

**To see the list of all the packages installed, run the command:**

{{< highlight bash >}}
> installed.packages()
{{< /highlight >}}

**To load the packages, use:**

{{< highlight bash >}}
> library("sp")
> library("dplyr")
{{< /highlight >}}

**Install other packages as needed.**

**Once the installation is over, close R session:**

{{< highlight bash >}}
> quit()
{{< /highlight >}}

**What to include in your job script:**

- All the modules used to install the packages.
- The command line to run R program: Rscript your-r-program.R

## How to install python packages?
---

{{< alert type="info" >}}
How to install python packages? As an example, install __cutadapt__ on Grex?
{{< /alert >}}

**How to proceed with the installation of python packages?**

- Python module (use module spider to see if the module is available).
- Load python module: __module load ...__
- Load other dependencies if needed.
- Use module list to see if all the modules are correctly loaded.
- Create a virtual environment: __virtualenv ~/my_venv__
- Activate the virtual environment: __source ~/my_venv/bin/activate__
- Update pip if needed: __pip install --upgrade pip__
- Install the packages using __pip install [package name].__
- Generate a requirements.txt file for future reference: __pip freeze > ~/requirements.txt__
- Log all the steps and modules in a separate README file for future reference

**Here is an example for installing cutadapt on Grex**

- Load the modules:

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0 python/3.12.9
module list
{{< /highlight >}}

- Create a virtual environment:

{{< highlight bash >}}
virtualenv ~/my_venv
{{< /highlight >}}

- Activate the virtual environment:

{{< highlight bash >}}
source ~/my_venv/bin/activate
{{< /highlight >}}

- Upgrade pip if needed and install the packages:

{{< highlight bash >}}
pip install --upgrade pip
pip install cutadapt
{{< /highlight >}}

- Run a quick test:

{{< highlight bash >}}
cutadapt --help
{{< /highlight >}} 

- Generate the requirements.txt file for future reference:

{{< highlight bash >}}
pip freeze > ~/requirements-cutadapt.txt
{{< /highlight >}}

- Close the virtual environment:

{{< highlight bash >}}
deactivate
{{< /highlight >}}

**What to include in your job script:**

- All the modules used to install the packages: __module load ...__
- Activate the virtual environment: __source ~/my_venv/bin/activate__
- Command line to run the python program: __python my-program.py__ or __program [+options if any]__

## How to install Perl packages? 
---

{{< alert type="info" >}}
How to install perl packages? As an example, install __Hash::Merge__ on Grex?
{{< /alert >}}

**How to proceed with the installation of perl packages?**

- Load perl module
- Install the packages using cpan or cpanm
- Test if the module is installed.

**Here is an example for installing Hash::Merge package:**

- First load perl module:

{{< highlight bash >}}
module load arch/avx512 perl
cpan install Hash::Merge
{{< /highlight >}}

{{< alert type="info" >}}
For the first installation, you will need to answer some questions and choose __local::lib__ that the packages will be installed under your home directory. Otherwise, you will receive a __permission denied__ error message. 
{{< /alert >}}

**Run a quick test to see if the package is installed:**

{{< highlight bash >}}
echo "use Hash::Merge;" > test.pl && perl test.pl
echo "use HTTP::Tiny;" >> test.pl && perl test.pl
{{< /highlight >}}

## How to install Julia packages?
---

{{< alert type="info" >}}
How to install julia packages? As an example, install __JLD__ package on Grex?
{{< /alert >}}

**How to proceed with the installation of Julia packages?**

- Load julia module
- Load any other dependency or external module
- Start or lauch julia: julia
- Install the packages using: __Pkg.add("name of the package")__
- Test if the package is installed: __using [name of the package__

**Note: This example was taken from this [page](https://docs.alliancecan.ca/wiki/Julia)**

- First load julia and HDF5 modules: this package requires HDF5.  

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0 hdf5/1.14.6 julia
export HDF5_DIR=$MODULE_HDF5_PREFIX
{{< /highlight >}}

The command **export HDF5_DIR=$MODULE_HDF5_PREFIX** is used to set the environment variable HDF5_DIR that points to the installation path of HDF5.

- Now, start julia and install the packages:

{{< highlight bash >}} 
julia            

julia> using Libdl
julia> push!(Libdl.DL_LOAD_PATH, ENV["HDF5_DIR"] * "/lib")
julia> using Pkg
julia> Pkg.add("JLD")
julia> using JLD
{{< /highlight >}}

{{< collapsible title="Example of installing julia packages:" >}}
{{< highlight bash >}}
[~@bison ~]$ module load arch/avx512  gcc/13.2.0 hdf5/1.14.6 julia
[~@bison ~]$ export HDF5_DIR=$MODULE_HDF5_PREFIX
[~@bison ~]$ julia 
               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 1.12.5 (2026-02-09)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org release
|__/                   |

julia> using Libdl

julia> push!(Libdl.DL_LOAD_PATH, ENV["HDF5_DIR"] * "/lib")
1-element Vector{String}:
 "/global/software/alma8/sb/opt/arch-avx512-gcc-13.2.0/hdf5/1.14.6/lib"

julia> using Pkg

julia> Pkg.add("JLD")

julia> using JLD
{{< /highlight >}}
{{< /collapsible >}}

<br>

## How to install Trimmomatic?
---

{{< alert type="info" >}}
How to install pre-compild java applications? As an example, install __Trimmomatic__ on Grex?
{{< /alert >}}

**How to proceed with pre-compiled java applications?**

- Download the jar file using _wget_, __curl__ ...
- Load java module, use module spider java to see how to load a module.
- Run the application

**Here is an example of Trimmomatic**

- First, download Trimmomatic archive using wget:

{{< highlight bash >}}
wget http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.39.zip
{{< /highlight >}}

- Unpack the archive: Trimmomatic-0.39.zip

{{< highlight bash >}}
unzip Trimmomatic-0.39.zip
{{< /highlight >}}

- Load java modules:

{{< highlight bash >}}
module load openjdk
{{< /highlight >}}

- Run the code using:

{{< highlight bash >}}
java -jar <path to>/trimmomatic-0.39.jar
or
java -jar Trimmomatic-0.39/trimmomatic-0.39.jar --help
{{< /highlight >}}

{{< alert type="warning" >}}
Trimmomatic is already available as a module. The procedure is discussed here to show how to proceed with installing similar application.
{{< /alert >}}

{{< alert type="info" >}}
A new version of Trimmomatic (__0.40__) is available. Try to use similar steps as above to test this version.
{{< /alert >}}

## How to install STAR?
---

{{< alert type="info" >}}
How to install a program that has already a Makefile? As an example, install __STAR__ on Grex?
{{< /alert >}}

**How to compile and install STAR on Grex?

Home page: https://github.com/alexdobin/STAR

- First, download the version 2.7.11b from GitHub:

{{< highlight bash >}}
wget https://github.com/alexdobin/STAR/archive/refs/tags/2.7.11b.tar.gz
{{< /highlight >}}

- Then, unpack the archive:

{{< highlight bash >}}
tar -xvf 2.7.11b.tar.gz
{{< /highlight >}}

- Then, load the required modules (gcc):

{{< highlight bash >}}
module load arch/avx512 gcc
{{< /highlight >}}

- Then, go to the directory STAR-2.7.11b/source 

{{< highlight bash >}}
cd STAR-2.7.11b/source/
ls
ls Makefile
make
{{< /highlight >}}

- Look for the binary "STAR":

{{< highlight bash >}}
ls -lrt STAR
realpath STAR
{{< /highlight >}}

- Run STAR command with --help option: 

{{< highlight bash >}}
./STAR --help
realpath STAR
{{< /highlight >}}

- Set the path and test the code:

Run the command 'realpath STAR' from the same directory from where you compiled the program to get the path to the program: 

- Set the path:

export PATH=${PATH}:<path-to-STAR-2.7.11b/source>

**What to include in a job script:**

- load the same modules used to compile the code.
- set path to the location of the binary (**STAR** in this case).
- command line to run the code

{{< highlight bash >}}
module load arch/avx512 gcc
export PATH=$PATH:<path-to-STAR-2.7.11b/source>
STAR --help
{{< /highlight >}}

## How to install Treemix?
---

{{< alert type="info" >}}
How to install a program that uses configure, make and make install? As an example, install __Treemix__ on Grex?
{{< /alert >}}

**How to configure and build Treemix on Grex?**

**Note:** the program requires boost and gcc
**Home page:** https://bitbucket.org/nygcresearch/treemix/wiki/Home

- First, download the version 1.13:

{{< highlight bash >}}
wget https://bitbucket.org/nygcresearch/treemix/downloads/treemix-1.13.tar.gz
{{< /highlight >}}

- Then, unpack the archive:

{{< highlight bash >}}
tar -xvf treemix-1.13.tar.gz 
{{< /highlight >}}

- Load the required modules and dependencies if any (Treemix requires boost):

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0 boost/1.85.0
{{< /highlight >}}

- Go to the directory treemix-1.13, then configure and compile the program:

{{< highlight bash >}}
cd treemix-1.13
ls
./configure --prefix=${HOME}/software/treemix/1.13
make
make install
{{< /highlight >}}

The option **--prefix=${HOME}/software/treemix/1.13** is used to specify the path to the installation directory. Otherwise, the command **make install** will try to install the program under a directory where you do not have write access (directories owned by root) and leads to __Permission denied__ error message.

**What to include in a job script:**

- load the same modules used to compile the code.
- set the path to the location of the binary.
- command line to run the code.

{{< highlight bash >}}
module load arch/avx512  gcc/13.2.0 boost/1.85.0
export PATH=$PATH:${HOME}/software/treemix/1.13/bin
treemix --help
treemix [+options if any]
{{< /highlight >}}

## How to install DIAMOND?
---

{{< alert type="info" >}}
How to install a program that uses cmake, make and make install? As an example, install __DIAMOND__ on Grex?
{{< /alert >}}

**How to configure and compile DIAMOND on CC clusters?**

**Home page:** https://github.com/bbuchfink/diamond
**Reuires:** gcc, cmake

- First, download the version 2.1.24:

{{< highlight bash >}}
wget https://github.com/bbuchfink/diamond/archive/refs/tags/v2.1.24.tar.gz
{{< /highlight >}}

- Then, unpack the archive:

{{< highlight bash >}}
tar -xvf v2.1.24.tar.gz
{{< /highlight >}}

- Load the required modules and dependencies if any:

{{< highlight bash >}}
module load arch/avx512 gcc/13.2.0 cmake
{{< /highlight >}}

- Then, go to the directory diamond-2.1.24 to configure and compile the program:

{{< highlight bash >}}
cd diamond-2.1.24
{{< /highlight >}}

- Create a build directory, go to __build__ directory:

{{< highlight bash >}}
mkdir build
cd build
{{< /highlight >}}

- Now, configure and compile the code:

{{< highlight bash >}}
cmake .. -DCMAKE_INSTALL_PREFIX=${HOME}/software/diamond/2.1.24
make
make test
make install
{{< /highlight >}}

The option **-DCMAKE_INSTALL_PREFIX=${HOME}/software/diamond/2.1.24** is used to specify the path to the installation directory. Otherwise, the command **make install** will try to install the program under a directory where you do not have write access (directories owned by root) and leads to __Permission denied__ error message.

**What to include in a job script:**

- load the same modules used during the compilation.
- set the path to the binaries {installation directory}
- command line to run the code.

**Note:** cmake is not required at run time {only used during the configuration and compilation of the code}. Therefore, it is not needed to add it to the list of modules inside a job script.

{{< highlight bash >}}
module load arch/avx512 gcc/13.2.0
export PATH=$PATH:${HOME}/software/diamond/2.1.24
diamond {+options if any}
{{< /highlight >}}

## How to install Geant4?
---

{{< alert type="info" >}}
How to install a program that uses cmake, make and make install? As an example, install __Geant4__ on Grex? This is an advanced example that requires many dependencies and more options at the configuration step. It takes quite more time to compile.
{{< /alert >}}

**How to configure and compile Geant4 on Grex?**

- First, download the version: 11.3.1

{{< highlight bash >}}
wget https://gitlab.cern.ch/geant4/geant4/-/archive/v11.3.1/geant4-v11.3.1.tar.gz
{{< /highlight >}}

- Then, unpack the archive:

{{< highlight bash >}}
tar -xvf geant4-v11.3.1.tar.gz
{{< /highlight >}}

- Load the required modules:

{{< highlight bash >}}
module load arch/avx512 gcc/13.2.0
module load clhep/2.4.7.1
module load tbb/2021.13.0
module load qt/6.8.1
module load cmake
{{< /highlight >}}

- Change the directory, create a build directory, configure and compile the code:

{{< highlight bash >}}
cd geant4-v11.3.1
mkdir build
cd build 
cmake .. -DCMAKE_INSTALL_PREFIX=${HOME}/Softs/geant4/11.3.1 -DCMAKE_BUILD_TYPE="Release" -DGEANT4_BUILD_MULTITHREADED="ON" -DBUILD_STATIC_LIBS:BOOL="ON" -DGEANT4_USE_GDML:BOOL="ON" -DXercesC_LIBRARY_RELEASE="/usr/lib64/libxerces-c.so" -DEXPAT_LIBRARY="/usr/lib64/libexpat.so" -DEXPAT_INCLUDE_DIR="/usr/include" -DCLHEP_DIR="$MODULE_CLHEP_PREFIX/lib/CLHEP-${CLHEPVERSION}" -DPKG_CONFIG_EXECUTABLE="/usr/bin/pkg-config" -DGEANT4_BUILD_STORE_TRAJECTORY:BOOL="ON" -DGEANT4_BUILD_TESTS:BOOL="ON" -DGEANT4_USE_RAYTRACER_X11="ON" -DGEANT4_USE_SYSTEM_ZLIB="ON" -DGEANT4_USE_OPENGL_X11="ON" -DGEANT4_ENABLE_TESTING="ON" -DGEANT4_USE_TBB="ON" -DGEANT4_USE_QT:BOOL="OFF" -DQT_QMAKE_EXECUTABLE="$MODULE_QT_PREFIX/bin/qmake" -DGEANT4_USE_QT_QT6="ON" -DGEANT4_INSTALL_EXAMPLES:BOOL="ON" -DGEANT4_INSTALL_DATA:BOOL="ON" -DGEANT4_INSTALL_DATASETS_NUDEXLIB="ON" -DGEANT4_INSTALL_DATASETS_TENDL="ON" -DGEANT4_INSTALL_DATASETS_URRPT="ON" -DGEANT4_USE_HDF5="OFF" -DGEANT4_USE_VTK="OFF" -DGEANT4_USE_FREETYPE="ON" -DGEANT4_USE_G3TOG4="ON" -DGEANT4_USE_GDML="ON" -DGEANT4_USE_SMARTSTACK="ON" -DGEANT4_USE_INVENTOR="OFF" -DGEANT4_USE_PTL_LOCKS="ON" -DGEANT4_USE_SMARTSTACK="ON" -DGEANT4_USE_SYSTEM_PTL="OFF" -DGEANT4_USE_USOLIDS="OFF"  -DGEANT4_USE_XM="ON" 
make -j8
make test
make install
{{< /highlight >}}

**Notes:**

- To see all available options for cmake, use the GUI mode by running the command: __ccmake__ instead of __cmake__.
- To avoid downloading data, use: **-DGEANT4_INSTALL_DATA:BOOL="OFF"**
- To enable downloading data, use: **-DGEANT4_INSTALL_DATA:BOOL="ON"**
- The configuration with cmake require many options, like setting the instalation path, the location of some external libraries (QT, CLHEP, EXPAT, ...). The external libraries could be installed as external modules or pat of the operating system.
- If the compilation fails, change the options for cmake and start over.

<!-- Changes and update:
* Last revision: May 12, 2026.
-->
