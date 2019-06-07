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

**Type of parallelism :** MPI, shared memory (OpenMP ?) ?, hybrid MPI+OpenMP ?

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – 10,000s cores

**System where it runs :** Cartesius, ???

**Extra :** HemeLB is part of the [2020 Science programme](http://www.2020science.net/software/hemelb.html)

---
## Download and installation
---

### Access mode:

The Source code is available for download from [Palabos' Download page](http://www.palabos.org/software/download) as tar-ball.

The software is freely available under the terms of an open-source [AGPLv3 license](https://www.gnu.org/licenses/agpl-3.0.html).

The library’s native programmer interface in written in C++.
Additional programmer intefaces are available for the Python and Java programming languages, which make it easier to rapidly prototype and develop CFD applications
It has practically no external dependencies (only Posix and MPI).


### Prerequisites

The library is exempt from external dependencies: all you need is a working compiler environment, including a modern C++ compiler (GCC, the Intel compiler and the Portland Group compiler have been succesfully tested), and a reasonably recent version of the Python interpreter (Python 2.7.15 is used with Palabos on Cartesius).

Palabos makes use of a few other open-source libraries. They don’t need to be explicitly intalled, though, because they are part of the Palabos releases:
- [SConstruct](https://scons.org/) (Only tested with Linux): This Python-based library is used in Palabos to manage the compilation process.
- [TinyXML](https://sourceforge.net/projects/tinyxml/): This library is used to read structured user input in XML format.

### Installation steps using foss toolchain (GNU) on Cartesius

1 - Download the most recent tarball from [Palabos' Download page](http://www.palabos.org/software/download) (e.g. `palabos-v2.0r0.tgz`)
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz
cd palabos-v2.0r0/
export PALABOSROOT=$PWD
```

2 - simple unoptimized build (not recommended)
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
module load foss/2018b
make -j4
```

3 - optimized build (recommended)
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
module load foss/2018b
make palabosRoot=$PALABOSROOT optimFlags='-O3 -march=sandybridge' debug='false' 'SMPparallel=true' 'serialCXX=g++' 'parallelCXX=mpic++' -j4
```

This creates directory `$PALABOSROOT/lib`, containing library `libplb_mpi.a`.

### Installation steps using Intel toolchain on Cartesius

1 - Download the most recent tarball from http://www.palabos.org/software/download (e.g. palabos-v2.0r0.tgz)
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz
cd palabos-v2.0r0/
export PALABOSROOT=$PWD
```

2 - simple unoptimized build (not recommended)
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
module load intel/2018b
make -j4
```

3 - optimized build (recommended)
```bash
cd $PALABOSROOT/examples/showCases/cylinder2d
module load intel/2018b
make palabosRoot=$PALABOSROOT optimFlags='-O3 -xAVX -axCORE-AVX2' debug='false' 'SMPparallel=true' 'serialCXX=icpc' 'parallelCXX=mpiicpc' -j4
```

This creates dir '$PALABOSROOT/lib' containing library 'libplb_mpi.a'.

---
## Running Palabos on Cartesius
---

### Simple use case: cavity2d

1 - Compilation of the 'cavity2d' example
There is no need to recompile the whole library.

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

2 - Running the 'cavity2d' example interactively on (not recommended)
```bash
srun -n 24 ./cavity2d
```

3 - Running the 'cavity2d' example in a batch job (recommended)
Example of a working job script to be submitted with the `sbatch` command.

```bash
#!/bin/bash
#SBATCH -J palabos_cavity2d
#SBATCH -n 24
#SBATCH -p normal
#SBATCH -o palabos_cavity2d_%j.out
#SBATCH -t 00:10:00

export PALABOSROOT=$HOME/CompBioMed/palabos/palabos-v2.0r0

# load modules
module purge
module load surfsara
module load foss/2018b  # or module load intel/2018b

# copy input files to scratch-shared
export TEMPDIR=`mktemp -d -p /scratch-shared`
cd $PALABOSROOT/examples/showCases/cavity2d
cd $TEMPDIR
mkdir tmp

# run simulation
srun -n 24 $PALABOSROOT/examples/showCases/cavity2d/cavity2d

# copy results back to the home directory
cp -r tmp/  $PALABOSROOT/examples/showCases/cavity2d/tmp_$SLURM_JOB_ID
```

The output can then be found in directory `$PALABOSROOT/examples/showCases/cavity2d/tmp_$SLURM_JOB_ID`.

For more information about the examples included in the tar-ball please refer to http://www.palabos.org/documentation/userguide/appendix-samples.html#appendix-example-programs.


### Benchmarking and scalability of Palabos



### Guidelines for efficient parallel programs using Palabos

Refer to http://www.palabos.org/documentation/userguide/parallelism.html#parallel-programming-approach.


---

[Offical documentation](http://www.palabos.org/documentation/userguide/)

---

[Back](../..)
