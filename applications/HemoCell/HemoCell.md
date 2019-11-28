---
layout: plain
---

# HemoCell

**Provider :** [**University of Amsterdam**](https://www.compbiomed.eu/about/partners/uva/)

HemoCell, developed by the team of Prof. Alfons Hoekstra at the University of Amsterdam (UvA), is a framework for simulating transport properties of dense suspensions of deformable cells, such as blood. The code is based on the combined Immersed boundary-lattice Boltzmann method (IB-LBM) and is built on top of the open source C++ lattice Boltzmann solver Palabos. HemoCell is currently used in a wide range of applications, from modelling aggregation of platelets and thrombus formation, via white blood cell margination, to studying details of the infectious pathways in malaria.

Palabos is a multi-physics, open-source lattice Boltzmann code which has been developed by CompBioMed Core Partner University of Geneve (UNIGE). The code runs on many platforms, from laptop to HPC machines. It has been used for several biomedical applications, such as thrombus formation in cerebral aneurysms, vertebroplasty, transport of fully resolved red blood cells and platelets in complex geometries, and haemodynamics in stented coronary arteries. It can also be deployed for the study of neuro-musculoskeletal problems. It is now available via the scientific software company NUMECA.

For more information contact [software@compbiomed.eu](emailto:software@compbiomed.eu)

## Access the code

**Type:** Source code or Executable

Hemocell download links can be found on the [official HemoCell website's download page](https://www.hemocell.eu/user_guide/Downloads.html).
It can be downloaded as a Singularity image, a source code archive, or via the GitHub source code repository.

Palabos source code and documentation are available through the applications website [palabos.unige.ch/](https://palabos.unige.ch/)

## Technical specification and requirements

Both HemoCell and Palabos are written in C++ and they make use of an on-demand compilation process, wherein the codes need to be compiled for each specific end-user application and then the produced executables can be re-used in future, until a new compilation is needed due to a modification of the code or compilation options. 


The following software and libraries are needed to compile and run HemoCell:

- Openmpi (>=1.10.2) or Intel Mpi (>=17.0.5)
- GCC (>= 5.2.0) or Intel C++ compiler(>=17.0.5)
- CMake >= 3.7.2
- HDF5 >=1.8.16
- GNU “patch” utility >=2.7.5
- h5py >= 2.6.0-1
- PARMETIS 4.0.3 (Optional)
- Palabos 2.0 
- Python

HemoCell uses Palabos to manage initial domain decomposition and to voxelize geometries. The outputs, after post processing with in-house developed tools, are then visualised through data visualisation software such as Paraview or Visit. 


## Running HemoCell

HemoCell uses Palabos to manage initial domain decomposition and to voxelize geometries. The outputs, after post processing with in-house developed tools, are then visualised through data visualisation software such as Paraview or Visit.

Detailed instructions on how to compile and run HemoCell are available [here](https://hemocell.eu/user_guide/QuickStart.html).

For more information on how to get access to an HPC system to run the code check the [CompBioMed HPC allocations](https://www.compbiomed.eu/high-performance-computer-allocations/) service.


## HemoCell on High Performance Computing systems

HemoCell is computationally capable of handling a large domain size with a high number of cells (10^4-10^6 cells). The code has been used and optimised to run on SURFsara systems Cartesius, Lisa, SuperMUC (LRZ) and Marenostrum IV (BSC). The code showed good performances on all the systems with good weak and strong scaling performances up to ~4,000 cores.
The framework uses data parallelism to distribute the workload over many compute elements, with Palabos used for the fluid phase simulation, and taking care of the boundary communications between cores, and HemoCell taking care of the cell-based flow and the communications between the two fluid and cell-based parts. The HDF5 library is used for handling I/O.

* [**HemoCell on Cartesius**](hemocell_cart.md)
* [**HemoCell on ARCHER**](hemocell_epcc.md)

**HemoCell typical HPC usage within the CompBioMed community**

<img src="spec_table.jpg" width="800"/>

### Benchmarks and code performances

The development and porting of the HemoCell framework is between some of the central activities carried out within WP2 and WP5 for the support of exemplar applications and  extension to novel architectures. CompBioMed Core Partner BULL/ATOS (BULL) has worked in collaboration with the developers of the code, to port and optimise a dense cellular suspension flow application, based on HemoCell, to Intel Skylake microarchitecture. This work provided insight of the main bottlenecks in the code parallelisation strategy as well as strong and weak scaling behaviour up to about 2,000 cores. The results suggested a degrading of performances, as the core count increases, due to an uneven distribution of the workload among the different MPI ranks and the relevance of data structures within the code to efficiently exploit core level vectorisation capabilities of modern CPUs. 

UvA, a CompBioMed Core Partner, has been continuously working to improve load balance within the code and increase communication efficiency of the codebase. In a recent study UvA have showed how the reorganisation of internal data structures and algorithms as well as the use of optimized communications procedures, bring a noticeable improvement of the base performances as well as of the strong scaling behaviour (dividing the same domain into smaller atomic blocks).



### Training material and presentations

Media and training material on HemeLB from the CompBioMed project.

* **Application Simple magnetic drug targeting simulations with HemeLB** <br/> 
[CompBioMed training at VPH2018 – High Performance for the VPH ](https://www.compbiomed.eu/events-2/high-performance-computing-for-the-vph/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/aIedCumP6bw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

* **HemeLB – Simulation of cardiovascular flow on high performance computers** <br/> 
[CompBioMed webinar](https://www.compbiomed.eu/compbiomed-webinar-10)

<iframe width="560" height="315" src="https://www.youtube.com/embed/WFSMRml_gn4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

[Back](../..)
