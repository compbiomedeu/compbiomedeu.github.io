---
layout: plain
---


# Alya

**Provider :** [**Barcelona Supercomputing Center**](https://www.compbiomed.eu/about/compbiomed2-core-partners/bsc/)

Supercomputing efficiency of a simulation code is a multiplicative combination of different aspects (Figure 1). 

<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/d473925b-2288-4b6d-b6e5-26d6587974e6">
</p>

Efficiency is attained by focusing on the different aspects, notably choosing the proper algorithms to implement and their correct implementation. Moreover, due to Amdahl’s Law, eliminating a bottleneck automatically points to the next one, which can be in a different “box”, i.e., by improving an aspect of the Parallel Efficiency one can discover that the next problem is in the Computational Scalability side. 

In the Alya Development Team, shared between the Barcelona Supercomputing Center and its spinoff company ELEM Biotech, we are very aware of this important aspect. We take good care not only on the code accuracy but also on the parallel efficiency. Our cardiac computational model involves fluid mechanics, electrophysiology and solid mechanics of both tissue and biomaterials, all problems tightly coupled. Coupling comes as fluid-solid-interaction, immersed boundary methods, contact problems, or electromechanical coupling, with supplementary solution of particle transport or species concentration. All these problems must be coupled without losing the parallel efficiency of the individual Physics, requiring a specific effort when implementing the coupling schemes. In our case, coupling is based on a staggered multi-code coupling, in which different instances of Alya run using point-to-point MPI communication schemes employing communicators to group the different MPI tasks in an efficient way. Additionally, the different individual instances can run by off-loading part of work onto accelerators such as GPUs. In this way, a multi-physics simulation can run on heterogeneous systems made of CPUs hosting GPUs.



## RISC-V vector accelerator

BSC is involved in the development of a RISC-V accelerator, leveraging a wide vector processing unit, in collaboration with the European Processor Initiative (EPI), a European project that conducts research to advance High-Performance Computing (HPC) through the development of European technology. EPI aims to develop a general- purpose processor and a RISC-V-based accelerator. Efficient exploitation of the vector architecture is crucial for harnessing the computational power provided by the new design. 

In this co-design collaboration, we used Alya as the computational challenge to efficiently run on the EPI vector accelerator. We considered a computationally intensive part of Alya (the solver) and demonstrated that, after careful study and improvement, it can fully leverage a vector architecture while maintaining its portability.

This project can be summarized through the following items:

* Analysis of the CFD kernel
  * Instrument kernel to find huge computational zones. – Make the appropriate code optimizations.
  * Compile the kernel with RISC-V compiler.
  * Validate the solution respect the reference version.
* Evaluation of the different solutions
  * Define a set of rigorous metrics that represent the application's performance when running within the emulator.
  * Carefully measure the performance of the different implementations.
* Comparison with other HPC architectures
  * Evaluate the proposed solution with another state-of-the-art architectures.
  * Compare the resultant kernel with other vector architectures.


### Scalar metrics 

The scalar execution information is extracted using the FPGA-SDV performance analysis tools. In particular, the hardware counters are read using the PAPI library.

The metrics used for scalar analysis together with the value limits are the following:
* Total cycles: Cycles spent in executing the kernel. [0, +∞]
* Percentage cycles per phase: Cycles spent executing each phase relative to the total cycles. [0%, 100%]

### Vector metrics

The information about vector instructions is extracted using the Vehave emulator and later converted to a trace format. When running in the FPGA, the timings of these vector instructions are extracted using hardware counters.

The metrics used for vector analysis are the following:

* Total cycles: Cycles spent executing the kernel. [0, +∞]
* Percentage cycles per phase: Cycles spent executing each phase relative to the total cycles. [0%, 100%]
* Vector mix: Number of vector instructions divided by total instructions. [0%, 100%]
* Vector activity: Number of cycles executing vector instructions divided by total cycles. [0%, 100%]
* Cycles per vector instruction (vCPI): Average VPU cycles needed to execute a vector instruction. [0, +∞]
* Average vector length: Average number of elements processed (double floating-point precision) per vector instruction. [0, 256]
* Vector efficiency: Average vector length divided by 256 (maximum double floating-point elements processed per instruction). [0%, 100%]
* L1 misses per thousand instructions: L1 data cache misses per thousand instructions. [0, +∞]
* Percentage memory instructions: Number of total memory instructions divided by total instructions. [0%, 100%]

To gain a deeper understanding of the instructions, we considered an instruction hierarchy for later analysis purposes to classify each instruction into a type. The instruction hierarchy tree is shown in the figure below. The box Vector aggregates the instructions executed on the vector processing unit. Vector configuration instructions are the instructions that configure the vector length and element width of the vector processing unit at run-time. Control lane vector instructions include all vector instructions that do not compute any arithmetic values or communicate with memory, such as moves, shifts or sign extensions.

<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/e27987cc-5dbe-49e6-8e67-b8e6501647ca">
</p>

### Results

The overall conclusion from our general performance evaluation for this particular Alya kernel is that the VECTOR_SIZE240 configuration is the faster configuration when running in the Vector Processing Unit (VPU), achieving a speed-up of 7.6× over the scalar execution. The trend is that a RISC-V processor would provide a substantial improvement in terms of speed-up. We will extend this report in the final conclusions of the project.


## Porting to GPUs

BSC's start-up company, ELEM Biotech, is carrying out the porting of some specific kernels of Alya to GPUs. ELEM was created in 2018 to co-develop with and commercialize modelling and simulation R+D coming from BSC for the biomedical domain. In particular, Alya is the core simulation software used by ELEM's Virtual Human's platform. 

The specific GPU porting done by ELEM in collaboration with BSC is for Alya's electrophysiology module, focusing on three kernels, which represent the heaviest part of a typical electrophysiology run:

* Cell model, which implies a loop over all the mesh nodes, with many operations per-node. It solves a set of coupled ODEs in an explicit form.
* Tissue model matrix computation and assembly, which implies a loop over all the mesh elements, with many (tensorial) operations per node, and gathering and scattering operations. It computes the space matrix of an implicit solver.
* Tissue model matrix solution, which implies an iterative solver solution, with massive vector-vector and matrix-vector operations. This kernel solves the system matrix.

We have extracted the kernels and converted them into mini-apps, porting them using OpenACC in Fortran subroutines compiled using the NVIDIA Fortran compiler. We have tested and assessed the speed-up in the three mini-apps, which is positive in all cases. Today we are fusing the ported mini-apps back into the production Alya code and starting the testing of production runs.





