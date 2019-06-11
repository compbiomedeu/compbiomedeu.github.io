---
layout: plain
---

# [INCOMPLETE PAGE - WORK IN PROGRESS] HemoCell - CompBioMed User Guide

**Provider :** [Computational Science Lab - University of Amsterdam ](https://uva.computationalscience.nl/)

---
## Description
---
HemoCell is a framework for simulating dense suspensions of deformable cells, focusing on blood. It is based on the combined Immersed boundary-lattice Boltzmann method (IB-LBM) and is built on top of the open source C++ lattice Boltzmann solver Palabos.

**License :** AGPL v3.0

**Target system :**  HPC platforms

**Target users :** Scientists and engineers with a solid background in computational fluid dynamics, and potentially some knowledge in LB modeling

**Type of parallelism :** MPI

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – 1,000s cores

**System where it runs :**  Cartesius, ???. The code can be compiled on Linux, Mac OS X and Windows 10 using the linux subsystem extension.

**Extra :** The HemoCell framework is available as source code or as singularity image.

---
## Download and installation
---

### Access mode:

All download links can be found on the [official HemoCell website's download page](https://www.hemocell.eu/user_guide/Downloads.html).
It can be downloaded as a Singularity image, a source code archive, or via the GitHub source code repository.
In this page we focus on the source code archive, but the installation steps are identical when downloading the source code from GitHub.

### Prerequisites
The following softwares and libraries are needed to compile and run HemoCell:
- OpenMpi (>= 1.10.2) or Intel Mpi(>= 17.0.5)
- Gcc >= 5.2.0 or Intel C++ compiler(>= 17.0.5)
- Cmake >=	3.7.2
- Hdf5 >=	1.8.16
- Gnu Patch >= 2.7.5
- h5py >= 2.6.0-1
- [ParMetis](http://glaros.dtc.umn.edu/gkhome/metis/parmetis/overview) 4.0.3 (Optional)
- [Palabos 2.0](http://www.palabos.org/index.php)

### Download and patch steps on Cartesius

1 - Download the most recent tarball from the [official HemoCell website's download page](https://www.hemocell.eu/user_guide/Downloads.html) (e.g. hemocell-2.0.tgz).
```bash
wget https://www.hemocell.eu/user_guide/_downloads/hemocell-2.0.tgz
tar xvzf hemocell-2.0.tgz
cd hemocell-2.0/
export HEMOCELLROOT=$PWD
```


2 - Download and patch Palabos
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz 
mv palabos-v2.0r0 palabos
cd patch && ./patchPLB.sh
cd ..
```

3 - Patch HemoCell's CMakeLists.txt file
```bash
cd $HEMOCELLROOT/build/hemocell
patch -i optim_cmake_flags_lib.patch CMakeLists.txt 
```

Where `optim_cmake_flags_lib.patch` contains:
```
14,15c14,18
< SET(BUILD_TYPE Release)  # Debug or Release
< 
---
> IF(NOT CMAKE_BUILD_TYPE) 
>   SET(CMAKE_BUILD_TYPE "Release" CACHE STRING "Debug or Release")  # Debug or Release
> ELSE()
>   SET(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Debug or Release")  # Debug or Release
> ENDIF()
64,65c67,78
< SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -march=native -std=c++11")
< SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ggdb -Wformat -Wformat-security")
---
> IF(NOT MARCH)
>   SET(MARCH "-march=native" CACHE STRING "")
> ELSE()
>   SET(MARCH ${MARCH} CACHE STRING "")
> ENDIF()
> IF(${CMAKE_BUILD_TYPE} STREQUAL "Release")
>   SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 ${MARCH}")
> ELSE()
>   SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -ggdb")
> ENDIF()
> SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
> SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wformat -Wformat-security") 
```
This makes a difference between `Debug` and `Release` builds, and allows setting custom optimization flags through the `MARCH` variable.
Now the user can pass -DCMAKE_BUILD_TYPE=<Release or Debug> and -DMARCH=<arch> to cmake.
 A 'Release' build will use the march and optim flags, a 'Debug" build will use '-O0 -ggdb'. The default march is '-march=native', but a different march can be passed to cmake.

### Installation steps using foss toolchain (GNU) on Cartesius
 
1 - Load modules to put dependencies in the paths

```bash
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
```

2 - Compile HemoCell
```bash
cd $HEMOCELLROOT/build/hemocell
mkdir ../../install
#cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install -DCMAKE_CXX_FLAGS='-O3 -march=sandybridge'  -DENABLE_MPI=1 -DENABLE_PARMETIS=1
cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install  -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DMARCH='-march=sandybridge'
make
```

This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.

### Installation steps using Intel toolchain on Cartesius

1 - Load modules to put dependencies in the paths
```bash
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
```

5 - Compile HemoCell
```bash
cd $HEMOCELLROOT/build/hemocell
mkdir ../../install
cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install  -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DMARCH='-xAVX -axCORE-AVX2' -DCMAKE_CXX_COMPILER=$EBROOTICC/bin/icpc -DCMAKE_C_COMPILER=$EBROOTICC/bin/icc
make
```


This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.

---
## Running HemoCell on Cartesius
---

### Simple Use case with foss toolchain: oneCellShear

1 - Init environment
```bash
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
```

2 - Patch CMakeLists_template.txt
```bash
cd $HEMOCELLROOT/examples/
patch -i optim_cmake_flags_examples.patch CMakeLists_template.txt
```

Where `optim_cmake_flags_examples.patch` contains:
```
13c13,17
< 
---
> IF(NOT CMAKE_BUILD_TYPE)
>   SET(CMAKE_BUILD_TYPE "Release" CACHE STRING "Debug or Release")  # Debug or Release
> ELSE()
>   SET(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Debug or Release")  # Debug or Release
> ENDIF()
57,58c61,72
< SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -march=native -std=c++11")
< SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ggdb -Wformat -Wformat-security")
---
> IF(NOT MARCH)
>   SET(MARCH "-march=native" CACHE STRING "")
> ELSE()
>   SET(MARCH ${MARCH} CACHE STRING "")
> ENDIF()
> IF(${CMAKE_BUILD_TYPE} STREQUAL "Release")
>   SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 ${MARCH}")
> ELSE()
>   SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -ggdb")
> ENDIF(${CMAKE_BUILD_TYPE} STREQUAL "Release")
> SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
> SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wformat -Wformat-security")
```
This makes a difference between `Debug` and `Release` builds, and allows setting custom optimization flags through the `MARCH` variable.

3 - Update CMakeLists.txt for all testcases
```bash
make cmakefiles
```
This replaces all <case>/CmakeLists.txt with ./CmakeLists_template.txt to update the build process for all cases.

4 - Compile a single example with optimization flags
```bash
cd $HEMOCELLROOT/examples/oneCellShear
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DMARCH='-march=sandybridge'
make
cd ..
```
WARNING: this rebuilds/relinks `libhemocell.a`..
To avoid that, manual compilation and linking of the example is possible:
```bash
#compilation
mpicxx  -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX  -I$HEMOCELLROOT/palabos/externalLibraries  -I$HEMOCELLROOT/palabos/libraryInterfaces -I$HEMOCELLROOT -I$HEMOCELLROOT/helper -I$HEMOCELLROOT/config -I$HEMOCELLROOT/core -I$HEMOCELLROOT/models -I$HEMOCELLROOT/mechanics -I$HEMOCELLROOT/external  -I$HEMOCELLROOT/IO -I$HEMOCELLROOT/palabos/src  -O3 -march=sandybridge -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -o oneCellShear.o -c oneCellShear.cpp
#linking
mpicxx -O3 -march=sandybridge -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -rdynamic oneCellShear.o -o ../oneCellShear $HEMOCELLROOT/build/hemocell/libhemocell.a -lhdf5 -lsz -lz -ldl -lm -lpthread -lhdf5 -lhdf5_hl -lsz -lz -ldl -lm -lpthread -lhdf5_hl
```

5 - Compile all examples with default options (not recommended)
```bash
cd $HEMOCELLROOT/examples
make executables
```
WARNING: this rebuilds/relinks `libhemocell.a`..

6 - Run example oneCellShear on Cartesius
6.1 - interactively (not recommended)
```bash
cd $HEMOCELLROOT/examples/oneCellShear
srun -n 4 oneCellShear config.xml
```
6.2 - in a batch job (recommended)
Here is an example of a submission script for Cartesius:
```bash
#!/bin/bash
#SBATCH -J hemocell_oneCellShear
#SBATCH -n 24
#SBATCH -p normal
#SBATCH -o hemocell_oneCellShear_%j.out
#SBATCH -t 00:10:00

export HEMOCELLROOT=$HOME/CompBioMed/hemocell/hemocell-2.0_foss/

# load modules
module purge
module load surfsara
module load foss/2018b
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15

# copy input files to scratch-shared
export TEMPDIR=`mktemp -d -p /scratch-shared`
cd $HEMOCELLROOT/examples/oneCellShear
cp *.xml *.pos *.gpl $TEMPDIR
cd $TEMPDIR

# run simulation
srun -n 4 $HEMOCELLROOT/examples/oneCellShear/oneCellShear config.xml

# copy results back to the home directory
cp -r tmp/  $HEMOCELLROOT/examples/oneCellShear/tmp_$SLURM_JOB_ID
```

The output of a case is usually written to the <case>/tmp folder. The checkpoints are the .xml and .dat files. When a new checkpoint is created they are moved to .xml.old and ``.dat.old. The hdf5 output is stored per timestep in tmp/hdf5 and the csv output in tmp/csv.

7 - Post processing of the results of example oneCellShear
When the jobs is completed, use 'batchPostProcess.sh' to create XDMF files ('.xmf. extension - http://www.xdmf.org/index.php/Main_Page). XDMF is an XML language that allows one to describe complex objects from a set of datasets (e.g. in HDF5 format), so that the results can be visualized with Paraview (or another visualization tool).
The 'batchPostProcess.sh' script should be run within the $HEMOCELLROOT/examples/<case> or $HEMOCELLROOT/examples/<case>/tmp directory.
```bash
$HEMOCELLROOT/scripts/batchPostProcess.sh
```
The XDMF files are written in the tmp directory.
WARNING: what happens when we have multiple 'tmp' directories ? It creates XDMF files in all tmp_* directories.

8 - Visualization of the results of example oneCellShear
Visualization is usually not done directly on the supercomputer but rather on local machines when possible.
To copy files back to a local machine, one can use for example 'scp' on a linux machine.
From the local machine, open a terminal and run the following command (replacing '<login>' with your actual login on Cartesius, and '/path/to/hemocell/rootdir' with the path to your hemocell root directory on Cartesius).
```bash
scp -r <login>@cartesius.surfsara.nl:/home/login/path/to/hemocell/rootdir/examples/oneCellShear/tmp .
```
This copies the output directory 'tmp' and all its content to the local machine.
Now to visualize the results, one can use Paraview.
```bash
cd tmp
paraview &
```
Then open the XDMF files, and visualize the results of the simulation with Paraview.


### Simple use case with intel toolchain: cellCollision_interior_viscosity - with intel toolchain

1 - init environment
```bash
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
```

2 - Patch CMakeLists_template.txt
```bash
cd $HEMOCELLROOT/examples/
patch -i optim_cmake_flags_examples.patch CMakeLists_template.txt
```
This is the same patch as above.

3 - Update CMakeLists.txt for all testcases
```bash
make cmakefiles
```
This replaces all <case>/CmakeLists.txt with ./CmakeLists_template.txt to update the build process for all cases.

4 - Compile a single example with optimization flags
```bash
cd $HEMOCELLROOT/examples/oneCellShear
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DMARCH='-xAVX -axCORE-AVX2' -DCMAKE_CXX_COMPILER=$EBROOTICC/bin/icpc -DCMAKE_C_COMPILER=$EBROOTICC/bin/icc
make
cd ..
```
WARNING: this rebuilds/relinks `libhemocell.a`..
To avoid that, manual compilation and linking of the example is possible:
```bash
mpiicpc  -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX  -I$HEMOCELLROOT/palabos/externalLibraries  -I$HEMOCELLROOT/palabos/libraryInterfaces -I$HEMOCELLROOT -I$HEMOCELLROOT/helper -I$HEMOCELLROOT/config -I$HEMOCELLROOT/core -I$HEMOCELLROOT/models -I$HEMOCELLROOT/mechanics -I$HEMOCELLROOT/external  -I$HEMOCELLROOT/IO -I$HEMOCELLROOT/palabos/src  -O3 -xAVX -axCORE-AVX2 -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -o cellCollision_interior_viscosity.o -c cellCollision_interior_viscosity.cpp

mpicxx -O3 -xAVX -axCORE-AVX2 -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -rdynamic cellCollision_interior_viscosity.o -o ../cellCollision_interior_viscosity $HEMOCELLROOT/build/hemocell/libhemocell.a -lhdf5 -lsz -lz -ldl -lm -lpthread -lhdf5 -lhdf5_hl -lsz -lz -ldl -lm -lpthread -lhdf5_hl
```

5 - Compile all examples with default options (not recommended)
```bash
cd $HEMOCELLROOT/examples
make executables
```
WARNING: this rebuilds/relinks `libhemocell.a`..

6 - Run example cellCollision_interior_viscosity on Cartesius
6.1 - interactively (not recommended)
```bash
cd $HEMOCELLROOT/examples/cellCollision_interior_viscosity
srun -n 4 cellCollision_interior_viscosity config.xml
```
6.2 - in a batch job (recommended)
Here is an example of a submission script for Cartesius:
```bash
#!/bin/bash
#SBATCH -J hemocell_cellCollision_interior_viscosity
#SBATCH -n 24
#SBATCH -p normal
#SBATCH -o hemocell_cellCollision_interior_viscosity_%j.out
#SBATCH -t 00:10:00

export HEMOCELLROOT=$HOME/CompBioMed/hemocell/hemocell-2.0_intel/

# load modules
module purge
module load surfsara
module load intel/2018b
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15

# copy input files to scratch-shared
export TEMPDIR=`mktemp -d -p /scratch-shared`
cd $HEMOCELLROOT/examples/cellCollision_interior_viscosity
cp cellCollision_interior_viscosity *.xml *.pos $TEMPDIR
cd $TEMPDIR

# run simulation
srun -n 4 $HEMOCELLROOT/examples/cellCollision_interior_viscosity/cellCollision_interior_viscosity config.xml

# copy results back to the home directory
cp -r tmp/  $HEMOCELLROOT/examples/cellCollision_interior_viscosity/tmp_$SLURM_JOB_ID
```

-> fails with "(HemoCell) (AddCellType) (RBC_HO) Cannot enable interior viscosity when INTERIOR_VISCOSITY is not defined at compile time"
(idem with foss)

### Post processiong


see https://www.hemocell.eu/user_guide/QuickStart.html#setting-up-hemocell-from-source and https://www.hemocell.eu/user_guide/Scripts.html#hemocell-scripts-batchpostprocess-sh.


hemocell/scripts/batchPostProcess.sh

This scripts uses the *XMF.py files to generate all necessary xmf-files so that the output of a job can be read into ParaView and others. This script should be run within the hemocell/examples/<case> or hemocell/examples/<case>/tmp directory. like so:

cd hemocell/examples/<case>
../../scripts/batchPostProcess.sh


hemocell/scripts/CellInfoMergeCSV.sh

This script merges the CSV output from multiple processors into a single one in the current directory. Use it in the tmp directory like this:

cd hemocell/examples/<case>/tmp/
. ./scripts/CellInfoMergeCSV.sh

---
### Benchmarking and scalability of Palabos on Cartesius
---

TODO

---
### Guidelines for efficient parallel programs using Palabos
---

TODO

---
### HemoCell with singularity on Cartesius
---

TODO

---

[Offical documentation](https://www.hemocell.eu/user_guide/index.html)

---

[Back](../..)
