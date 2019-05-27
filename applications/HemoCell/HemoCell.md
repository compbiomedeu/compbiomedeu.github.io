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

**Type of parallelism :** MPI, ???

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – 10,000s cores

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

2.3 - Compile HemoCell
```bash
cd $HEMOCELLROOT/build/hemocell
cmake . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX= -DCMAKE_CXX_FLAGS='-O3 -march=sandybridge'  -DENABLE_MPI=1 -DPLB_MPI_PARALLEL=true -DPLB_SMP_PARALLEL=true -DENABLE_PARMETIS=1
make
```

This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.


3 - Compile with intel toolchain

3.1 - 

---
## Running HemoCell
---

### Input Preparation

#### Use case: XXXX

#### Input files

### Runtime Information


---

[Offical documentation](https://www.hemocell.eu/user_guide/index.html)

---

[Back](../..)
