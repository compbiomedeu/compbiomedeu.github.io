---
layout: plain
---

# Alya - CompBioMed User Guide

**Provider :** [Barcelona Supercomputing Center ](https://www.compbiomed.eu/about/partners/bsc/)

---
## Description
---

**License :** Limited

**Target system :**  HPC

**Target users :** Academic, Industrial

**Type of parallelism :** MPI, OpenMP, Hybrid (MPI+OpenMP), GPUs 

**Scalability :**	
: Typical run: 100 – 1,000 cores
: Large run: 1,000 – 100,000 cores

**System where it runs :** Marenostrum (BSC), Archer (EPCC), Cartesius (SURFsara) 

**Extra :** Alya is part of the [PRACE Benchmark Suite](https://repository.prace-ri.eu/git/UEABS/ueabs#alya)

---
## Download and installation
---

### Access mode:

Source code or Executable

Alya source code is available free of charge under collaboration agreement.

For more information contact [software@compbiomed.eu](emailto:software@compbiomed.eu)

Alya is written in Fortran and C. It is composed of different modules and is provided with several configurations files to easily build on different architectures. Alya has been tested on several OS, however this documentation refers only to Linux operating systems.


### Prerequisites

To compile an installation of Alya capable to run the provided example, the following software is required:
- C and Fortran compilers (Intel/GNU)
- An MPI installation 
- [METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) (optional; internal provided)


### Installation steps

1 - Enter the Executables directory:

```bash
cd Alya/Execuables/unix
```

2 - Choose a configuration file:

Intel Fortran compiler
```bash 
cp configure.in/config_ifort.in config.in 
```

GNU Fortran compiler
```bash 
cp configure.in/config_gfortran.in config.in 
```

3 - Configure Alya modules for compilation:

```bash
./configure nastin solidz parall alefor exmedi
```

4 - Build Metis library:

```bash
make metis4
```

5 - Build Alya:
 
```bash
make
```

Detailed installation instructions, as well as more information on the configuration files distributed with the code, are available at:
[http://bsccase02.bsc.es/alya/configure.html](http://bsccase02.bsc.es/alya/configure.html)

---
## Running Alya
---

Alya is able to runs in parallel and scale efficiently up to 100s thousands of cores.

### Input Preparation
Alya input consists of multiple text files containing description of the physics of the problem and information on the modules which will be used to solve it. Filename (without extension) is common to each file and usually identify the problem.

#### Use case: Elbow (CFD problem)
Detailed explanation of the problem can be found at: [http://bsccase02.bsc.es/alya/case_elbow.html](http://bsccase02.bsc.es/alya/case_elbow.html)

#### Input files

- [elbow.dat]({{ site.github.repository_url }}/tree/master/applications/Alya/input/elbow.dat)    (case global parameters)
- [elbow.dom.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.dom.dat) (domain input data)
- [elbow.geo.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.geo.dat) (mesh information)
- [elbow.set.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.set.dat) (groups for post-processing, optional)
- [elbow.fix.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.fix.dat) (boundary conditions data)
- [elbow.ker.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.ker.dat)
- [elbow.nsi.dat]({{ site.github.repository_url }}/tree/master/applications/Alya//input/elbow.nsi.dat) (incompressible Navier-Stokes solver parameters)

Download all input files [here](input/elbow.tar.gz).

### Runtime Information

Alya executable is:

    Alya.x
  
The path to the directory where Alya installation is located must be added, or include the complete path to Alya.x in the terminal configuration file. 

Serial execution is performed by invoking the Alya.x executable: 

```
Alya.x elbow
```

Alya starts reading elbow case input files and runs the simulation. The terminal echoes the tracking of the calculations, listing timstep progression. 

Parallel execution is performed through mpi line command:

```
mpirun -np N Alya.x elbow
```

where N is the number of mpi tasks you want to use. 

Note: in all runs there is one master CPU, devoted to conduct the simulation, so that N-1 partitions will be done on the mesh.

---

[Offical documentation](http://bsccase02.bsc.es/alya/index.html)

---

[Back](../..)
