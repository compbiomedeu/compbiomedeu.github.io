---
layout: plain
---

# Alya - CompBioMed User Guide

**Provider :** [**Barcelona Supercomputing Center](https://www.compbiomed.eu/about/partners/bsc/)

Alya is a multi-scale, multi-physics simulation code developed by the team co-lead by Mariano Vazquez and Guillaume Houzeaux at the Barcelona Supercomputing Centre (BSC). The simulations involve the solution of Partial Differential Equations in an unstructured mesh using Finite Elements methods. 

In the context of CompBioMed the code has been used for coupled cardiac fluid-electro-mechanical simulations, from ion-channel kinetics up to organ level; and simulations of the respiratory system, particularly focusing on particle deposition.  

[**Official website**](https://www.bsc.es/research-development/research-areas/engineering-simulations/alya-high-performance-computational)

[**Official documentation**](http://bsccase02.bsc.es/alya/)

### Access the code

**Type:** Source code or Executable

Alya is available for use to researchers at several [HPC systems](https://www.bsc.es/research-development/research-areas/engineering-simulations/alya-high-performance-computational); for clinical and industrial users, BSC recommends users access it as a service, due to the complexity involved with setting up simulations. To this purpose BSC has launched a spin-off ([ELEM Biotech](http://www.elem.bio/)) that will provide commercial software-as-a-service to medical device, pharmaceutical and biomedical industries using Alya.

For more information contact [software@compbiomed.eu](emailto:software@compbiomed.eu)


### Technical specification and requirements

The code is written in modern Fortran and C, and its parallelisation is based on mesh partitioning, with MPI as the message passing library for inter-node and tasks level parallelism. In order to improve the efficiency on multi-core shared memory systems, some heavy weight loops are parallelized using [OpenMP](ttps://www.openmp.org/). Both MPI and OpenMP layers can be used at the same time in a hybrid scheme. GPU acceleration is available through the use of [OpenACC](https://www.openacc.org/) directives or [CUDA API](https://docs.nvidia.com/cuda/cuda-runtime-api/index.html), offloading some specific parts of the code, such as some matrix assembly loops or some types of solvers. Alya is also capable of actively using multi-code coupling on top of MPI/OpenMP parallelisation.

To build Alya from source, the following software is needed:

* [GNU make](https://www.gnu.org/)
* Fortran and C compilers
* MPI
* [METIS](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview.) (optional)
* [HDF5](https://www.hdfgroup.org/solutions/hdf5/) (optional)
* [VTK](https://vtk.org/) (optional)
* [CUDA](https://developer.nvidia.com/cuda-zone) (optional)

The code is highly portable and compatible with most available compilers including GCC, Intel, Cray and IBM XL compilers. There are no specific requirements on type and version of the compiler used, but OpenMP support and vectorisation capabilities can improve drastically the performances of the code. Alya is compatible with variety of MPI implementations, including Intel MPI and OpenMPI, as well as bespoke libraries for specific hardware such as the Cray-MPI library. The external library METIS is mainly used for domain decomposition at the MPI level. A compatible version of the library is shipped with the source to reduce dependencies and facilitate installation procedure avoiding version conflicts. Input data is generally composed of text files with a set format. Input files can be converted into binary inputs within Alya. Output files are post processed using tools within Alya to generate standard format files, like the widely used so-called ensight format that can be visualized with software visualization tools as the open-source viewer [Paraview](https://www.paraview.org/).

[Back](../..)
