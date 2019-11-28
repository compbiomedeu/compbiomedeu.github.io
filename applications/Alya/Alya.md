---
layout: plain
---

# Alya 

**Provider :** [**Barcelona Supercomputing Center**](https://www.compbiomed.eu/about/partners/bsc/)

Alya is a multi-scale, multi-physics simulation code developed by the team co-lead by Mariano Vazquez and Guillaume Houzeaux at the Barcelona Supercomputing Centre (BSC). The simulations involve the solution of Partial Differential Equations in an unstructured mesh using Finite Elements methods. 

In the context of CompBioMed the code has been used for coupled cardiac fluid-electro-mechanical simulations, from ion-channel kinetics up to organ level; and simulations of the respiratory system, particularly focusing on particle deposition.  

For more information contact [software@compbiomed.eu](emailto:software@compbiomed.eu)

## Access the code

**Type:** Source code or Executable

Alya is available for use to researchers at several [HPC systems](https://gitlab.bsc.es/alya/alya/wikis/User-Documentation/Access-to-Alya); for clinical and industrial users, BSC recommends users access it as a service, due to the complexity involved with setting up simulations. To this purpose BSC has launched a spin-off ([ELEM Biotech](http://www.elem.bio/)) that will provide commercial software-as-a-service to medical device, pharmaceutical and biomedical industries using Alya.

## Technical specification and requirements

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


## Running Alya

Alya is currently installed and available to research users on Marenostrum IV (BSC), Cartesius (SURFsara), and ARCHER (EPCC, UEDIN).

For more information check the [CompBioMed HPC allocations](https://www.compbiomed.eu/high-performance-computer-allocations/) service.

* [**CompBioMed Tutorial**](example.md): brief tutorial on how to install and run the codes.

* [**BSC examples**](https://gitlab.bsc.es/alya/alya/wikis/Examples): set of examples from the code developers (external).

<!-- * [**Alya on Cartesius (SURFsara)**](Alya/cart.md): guide and script on how to run Alya on Cartesius HPC system. -->



## Alya on High Performance Computing systems

Alya has been specifically optimised for the efficient use of supercomputing resources. Combining the use of multiple level of parallelism (MPI, [OpenMP](ttps://www.openmp.org/), [OpenACC](https://www.openacc.org/), etc.) and efficient dynamic load balance techniques, the code is able to exploit both the full power current chips and the large number of compute elements on modern supercomputers.

The code is one of the two CFD codes of the Unified European Applications Benchmark Suite ([UEBAS](https://repository.prace-ri.eu/git/UEABS/ueabs/)) as well as the Accelerator benchmark suite of PRACE. 

**Alya typical HPC usage within the CompBioMed community**

<img src="https://github.com/compbiomedeu/compbiomedeu.github.io/blob/master/applications/Alya/spec_table.png" width="600"/>

### Benchmarks and code performances

Alya has been tested by the BSC team, on a large number of Tier-0 and Tier-1 HPC systems. 

* [**Benchmarks and scalability on Blue Waters (NCSA)**](bench_ncsa.md)

* [**Performance evaluation on Cartesius (SURFsara)**](bench_surf.md)

### Training material and presentations

Media and training material on Alya from the CompBioMed project.


* **HPC Multi-scale computational modelling using Alya Red** <br/> 
[CompBioMed Training: Winter School 2018 at BSC](https://www.compbiomed.eu/events-2/compbiomed-training-winter-school-2018-at-bsc/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/AcuIrW82Cpg" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

--

* **The ELEM Biotech Case: Porting Alya to HPC Clouds** <br/>
[CompBioMed containerisation meeting](https://www.compbiomed.eu/events-2/compbiomed-containerisation-meeting/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/mZPA7jimvf4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

--

* **HPC simulations of cardiac electrophysiology using patient specific models** <br/>
[CompBioMed webinar](https://www.compbiomed.eu/compbiomed-webinar-1/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/pz8yJmClLQQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

--

* **Sensitivity analysis of a strongly coupled cardiac electro-mechanical model** <br/>
[CompBioMed webinar](https://www.compbiomed.eu/compbiomed-webinar-7/)

<iframe width="560" height="315" src="https://www.youtube.com/embed/pqDSuQv0Byw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

--

[Back](../..)
