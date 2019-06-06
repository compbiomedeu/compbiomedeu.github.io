---
layout: plain
---

# [INCOMPLETE PAGE - WORK IN PROGRESS] Palabos - CompBioMed User Guide

**Provider :** [The Palabos Project ](http://www.palabos.org/index.php)

---
## Description
---
The Palabos library is a framework for general-purpose computational fluid dynamics (CFD), with a kernel based on the lattice Boltzmann (LB) method. It is used both as a research and an engineering tool: its programming interface is straightforward and makes it possible to set up fluid flow simulations with relative ease, or, if you are knowledgeable of the lattice Boltzmann method, to extend the library with your own models.

**License :** AGPL v3.0

**Target system :**  HPC platforms

**Target users :** Scientists and engineers with a solid background in computational fluid dynamics, and potentially some knowledge in LB modeling

**Type of parallelism :** MPI, ???

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – 10,000s cores

**System where it runs :** Cartesius, BlueGene, ???

**Extra :** HemeLB is part of the [2020 Science programme](http://www.2020science.net/software/hemelb.html)

---
## Download and installation
---

### Access mode:

The library’s native programmer interface in written in C++.
Additional programmer intefaces are available for the Python and Java programming languages, which make it easier to rapidly prototype and develop CFD applications
It has practically no external dependencies (only Posix and MPI).

The software is freely available under the terms of an open-source [AGPLv3 license](https://www.gnu.org/licenses/agpl-3.0.html).


Source code available from http://www.palabos.org/software/download.

### Prerequisites

The library is exempt from external dependencies: all you need is a working compiler environment, including a modern C++ compiler (GCC, the Intel compiler, and the Portland Group compiler have been succesfully tested), the command make, and a reasonably recent version of the Python interpreter.

Palabos makes use of a few other open-source libraries. They doen’t need to be explicitly intalled, though, because they are part of the Palabos releases:
- SConstruct (Only tested with Linux): This Python-based library is used in Palabos to manage the compilation process.
- TinyXML: This library is used to read structured user input in XML format.

### Installation steps using included dependencies

1 - Download the most recent tarball from http://www.palabos.org/software/download (e.g. palabos-v2.0r0.tgz)
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz
cd palabos-v2.0r0/
export PALABOSROOT=$PWD
```

2 - compile

2.1 - simple unoptimized build with foss toolchain
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
make -j4
```

2.2 - optimized build with foss toolchain
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
module load foss/2018b
make palabosRoot=$PALABOSROOT optimFlags='-O3 -march=sandybridge' debug='false' 'SMPparallel=true' 'serialCXX=g++' 'parallelCXX=mpic++' -j4
```

2.3 - optimized build with intel toolchain
```bash
module load foss/2018b
cd $PALABOSROOT/examples/showCases/cylinder2d
module load intel/2018b
make palabosRoot=$PALABOSROOT optimFlags='-O3 -xAVX -axCORE-AVX2' debug='false' 'SMPparallel=true' 'serialCXX=icpc' 'parallelCXX=mpiicpc' -j4
```

This creates dir '$PALABOSROOT/lib' containing library 'libplb_mpi.a'.

---
## Running Palabos
---

1 - Compilation of the 'cavity2d' example
There is no need to recompile the whole lib

1.1 - With foss toolchain
```bash
module load foss/2018b
cd $PALABOSROOT/examples/showCases/cavity2d
mpicxx -Wall -Wnon-virtual-dtor -Wno-deprecated-declarations -O3 -march=sandybridge -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX -I$PALABOSROOT/src -I$PALABOSROOT/externalLibraries -o cavity2d cavity2d.cpp -L$PALABOSROOT/lib/ -lplb_mpi
```

1.2 - With intel toolchain
```bash
module load intel/2018b
cd $PALABOSROOT/examples/showCases/cavity2d
mpiicpc -Wall -Wnon-virtual-dtor -Wno-deprecated-declarations -O3 -xAVX -axCORE-AVX2 -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX -I$PALABOSROOT/src -I$PALABOSROOT/externalLibraries -o cavity2d cavity2d.cpp -L$PALABOSROOT/lib/ -lplb_mpi
```

2 - Running the 'cavity2d' example
```bash
srun -n 24 ./cavity2d
```

### Input Preparation

#### Use case: XXXX
look at the examples (http://www.palabos.org/documentation/userguide/appendix-samples.html#appendix-example-programs)

#### Input files

### Runtime Information


---

[Offical documentation](http://www.palabos.org/documentation/userguide/)

---

[Back](../..)