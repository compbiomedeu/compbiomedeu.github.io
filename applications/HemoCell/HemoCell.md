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

**System where it runs :**  The code can be compiled on Linux, Mac OS X and Windows 10 using the linux subsystem extension.

**Extra :** The HemoCell framework is available as source code or as singularity image.

---
## Download and installation
---

### Access mode:

All download links can be found on the [official website's dowload page](https://www.hemocell.eu/user_guide/Downloads.html).
It can be downloaded as a Singularity image, a source code archive, or via the GitHub source code repository.
In this page we focus on the source code archive, but the installation steps are identical when downloading the source code from GitHub.




### Prerequisites
Dependency 	Version

- OpenMpi (>= 1.10.2) or Intel Mpi(>= 17.0.5)
- Gcc >= 5.2.0
- Cmake >=	3.7.2
- Hdf5 >=	1.8.16
- Gnu Patch >= 2.7.5
- h5py >= 2.6.0-1
- ParMetis 	4.0.3 (Optional)
- Palabos 	2.0

### Installation steps using included dependencies

1 - Download the most recent tarball from https://www.hemocell.eu/user_guide/_downloads/ (e.g. hemocell-2.0.tgz)
```bash
wget https://www.hemocell.eu/user_guide/_downloads/hemocell-2.0.tgz
tar xvzf hemocell-2.0.tgz
cd hemocell-2.0/
export HEMOCELLROOT=$PWD
```

2 - Compile with foss toolchain

2.1 - Load dependencies
```bash
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
```

2.2 - Download and patch Palabos
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz 
mv palabos-v2.0r0 palabos
cd patch && ./patchPLB.sh
cd ..
```

2.3 - Patch HemoCell's CMakeLists.txt file
```bash
cd $HEMOCELLROOT/build/hemocell
patch -i optim_cmake_flags_lib.patch CMakeLists.txt 
```

2.3 - Compile HemoCell
```bash
cd $HEMOCELLROOT/build/hemocell
mkdir ../../install
#cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install -DCMAKE_CXX_FLAGS='-O3 -march=sandybridge'  -DENABLE_MPI=1 -DENABLE_PARMETIS=1
cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install  -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DMARCH='-march=sandybridge'
make
```

This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.

WARNING: "[maximem@int1 hemocell]$ make install
make: *** No rule to make target `install'.  Stop."
so the install_prefix is not used and there is no way to put the libs in another directory


=> I did a small change in CMakeLists.txt, now the user can pass -DCMAKE_BUILD_TYPE=<Release or Debug> and -DCMAKE_MARCH=<arch> to cmake. A 'Release' build with use the march and optim flags, a 'Debug" build will use '-O0 -ggdb'. The default march is '-march=native', but a different march can be passed to cmake.
This change is is 'optim_cmake_flags_lib.patch'.



3 - Compile with intel toolchain -> not supported

3.1 - Load dependencies
```bash
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
```

3.2 - Download and patch Palabos
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz 
mv palabos-v2.0r0 palabos
cd patch && ./patchPLB.sh
cd ..
```

3.3 - Patch HemoCell's CMakeLists.txt file
```bash
cd $HEMOCELLROOT/build/hemocell
patch -i optim_cmake_flags_lib.patch CMakeLists.txt 
```

3.4 - Compile HemoCell
```bash
cd $HEMOCELLROOT/build/hemocell
mkdir ../../install
cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install  -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DMARCH='-xAVX -axCORE-AVX2' -DCMAKE_CXX_COMPILER=$EBROOTICC/bin/icpc -DCMAKE_C_COMPILER=$EBROOTICC/bin/icc
make
```
-> make fails
-> pb because I pass -march

---
## Running HemoCell
---

### Input Preparation



#### Use case: oneCellShear - with foss toolchain

1 - init environment
```bash
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
```

2 - patch CMakeLists.txt
```bash
cd $HEMOCELLROOT/examples/
patch -i optim_cmake_flags_examples.patch CMakeLists_template.txt
make cmakefiles   #replace <case>/CmakeLists.txt with ./CmakeLists_template.txt to update the build process for all cases.
```
 

3 - compile
3.1 - compile a single examples with custom options
```bash
cd $HEMOCELLROOT/examples/oneCellShear
mkdir build
cd build
#cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$HEMOCELLROOT/install -DCMAKE_CXX_FLAGS='-O3 -march=sandybridge'  -DENABLE_MPI=1 -DENABLE_PARMETIS=1
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DMARCH='-march=sandybridge'
make
cd ..
```
WARNING: this rebuilds 'libhemocell.a'..
To avoid that, manual compilation and linking of the example is possible:
```bash
mpicxx  -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX  -I$HEMOCELLROOT/palabos/externalLibraries  -I$HEMOCELLROOT/palabos/libraryInterfaces -I$HEMOCELLROOT -I$HEMOCELLROOT/helper -I$HEMOCELLROOT/config -I$HEMOCELLROOT/core -I$HEMOCELLROOT/models -I$HEMOCELLROOT/mechanics -I$HEMOCELLROOT/external  -I$HEMOCELLROOT/IO -I$HEMOCELLROOT/palabos/src  -O3 -march=sandybridge -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -o oneCellShear.o -c oneCellShear.cpp

mpicxx -O3 -march=sandybridge -std=c++11 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -DNDEBUG  -rdynamic oneCellShear.o -o ../oneCellShear $HEMOCELLROOT/build/hemocell/libhemocell.a -lhdf5 -lsz -lz -ldl -lm -lpthread -lhdf5 -lhdf5_hl -lsz -lz -ldl -lm -lpthread -lhdf5_hl
```

3.2 - compile all examples with default options
```bash
cd $HEMOCELLROOT/examples
make executables
```
WARNING: this rebuilds 'libhemocell.a'...

4 - run example oneCellShear
```bash
cd $HEMOCELLROOT/examples/oneCellShear
srun -n 4 oneCellShear config.xml
```
The results will be written in a 'tmp' directory, containing 3 subdirectories: 'csv', 'hdf5' and 'log'.

5 - Post processing of the results of example oneCellShear
When the jobs is completed, use 'batchPostProcess.sh' to create XDMF files ('.xmf. extension - http://www.xdmf.org/index.php/Main_Page). XDMF is an XML language that allows one to describe complex objects from a set of datasets (e.g. in HDF5 format), so that the results can be visualized with Paraview (or another visualization tool).
The 'batchPostProcess.sh' script should be run within the $HEMOCELLROOT/examples/<case> or $HEMOCELLROOT/examples/<case>/tmp directory.
```bash
$HEMOCELLROOT/scripts/batchPostProcess.sh
```
The XDMF files are written in the tmp directory.
WARNING: what happens when we have multiple 'tmp' directories ? It creates XDMF files in all tmp_* directories.

6 - Visualization of the results of example oneCellShear
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


#### Input files

### Runtime Information

#### Post processiong


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

[Offical documentation](https://www.hemocell.eu/user_guide/index.html)

---

[Back](../..)
