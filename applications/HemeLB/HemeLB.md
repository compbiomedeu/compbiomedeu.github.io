---
layout: plain
---

# HemeLB

**Provider :** [**University College London**](https://www.compbiomed.eu/about/partners/ucl/)

HemeLB developed by the team of Prof Peter Coveney at University College London (UCL), is a software pipeline that simulates blood flow in vessels in the human brain, in support of clinical neurosurgery. HemeLB is specifically designed to efficiently handle sparse topologies, supports real-time visualization and remote steering of the simulation and can handle fully resolved red blood cells. It runs on petascale platforms, alone and coupled to other codes. The pipeline takes as input an STL file of the surface geometry of the patient, generally obtained via segmentation of [DICOM images](https://www.dicomstandard.org/) from a CT-scan, and HemeLB, the parallel lattice-Boltzmann CFD solver, then simulates the fluid flow within that geometry, using the given velocity-time profiles for each inlet. Once complete, the simulation output is analysed using the hemeXtract utility, which can produce images of cross-sectional flow, or 3D shots of wall shear stress distribution in the geometry using ParaView visualisation software. 

For more information contact [software@compbiomed.eu](emailto:software@compbiomed.eu)

## Access the code

**Type:** Source code or Executable

HemeLB source code and documentation is available through the application website [www.hemelb.org](https://www.hemelb.org).
The UCL team also provide consulting to biomedical companies and clinical users for the use of the software.


## Technical specification and requirements

HemeLB is an open source massively parallel lattice-Boltzmann (LB) simulation framework. The main lattice-Boltzman solver is written in C++ and its parallelisation is implemented via MPI. The HemeLB application relies on several external libraries for tasks as XML processing, domain decomposition, unit testing, and real time visualisation.

* C and C++ compilers
* [Cmake](https://cmake.org/)
* MPI (>=3.0)
* Zlib
* Boost
* ParMETIS
* TinyXML
* CPPunit
* CTemplate

HemeLB is compatible with most versions of GCC and Intel compilers and most modern MPI implementations. TinyXML, ParMETIS, CPPUnit, CTemplate, Boost and Zlib are provided with HemeLB sources and can be built during the HemeLB build. 

## Running HemeLB

The HemeLB computational pipeline take as input an STL file of the surface geometry of the patient, usually obtained through segmentation of DICOM image files from CT-scans and uses the fully parallelised Palabos’ voxelizer to prepare the geometry for the LB solver. The approach is relatively rapid and with almost no user intervention needed. This is an advantage over traditional CFD methods, which rely on unstructured mesh generation procedures to obtain the discrete representation of the geometry; complex geometries tend to require high levels of user intervention and considerable CPU time to ensure mesh quality. The output of HemeLB can be analysed using the post-processing tool hemeXtract, which is distributed with the code. This utility produces images of different properties (i.e. cross-sectional flow or 3D image of the wall shear stress distribution) which can then be visualised with Paraview.

For more information on how to get access to a system to run the code check the [CompBioMed HPC allocations](https://www.compbiomed.eu/high-performance-computer-allocations/) service.

* [**UCL Tutorial**](example.md): brief tutorial on how to install and run the codes.

* [**BSC examples**](https://gitlab.bsc.es/alya/alya/wikis/Examples): set of examples from the code developers (external).

<!-- * [**Alya on Cartesius (SURFsara)**](Alya/cart.md): guide and script on how to run Alya on Cartesius HPC system. -->



## Alya on High Performance Computing systems

Alya has been specifically optimised for the efficient use of supercomputing resources. Combining the use of multiple level of parallelism (MPI, [OpenMP](ttps://www.openmp.org/), [OpenACC](https://www.openacc.org/), etc.) and efficient dynamic load balance techniques, the code is able to exploit both the full power current chips and the large number of compute elements on modern supercomputers.

The code is one of the two CFD codes of the Unified European Applications Benchmark Suite ([UEBAS](https://repository.prace-ri.eu/git/UEABS/ueabs/)) as well as the Accelerator benchmark suite of PRACE. 

**Alya typical HPC usage within the CompBioMed community**

<img src="spec_table.png" width="500"/>

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
