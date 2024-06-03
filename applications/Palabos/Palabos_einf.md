---
layout: plain
---

# Palabos

**Provider :** [**University of Geneve**](https://www.compbiomed.eu/about/compbiomed2-core-partners/university-of-geneva/)

## HPC development
The npFEM - Palabos coupling is a massively parallel simulation framework for red blood cells (RBCs) at a cell-level description. The deformation of RBCs is described through a finite-element structural solver (npFEM) which runs entirely on GPUs. The fluid flow is simulated on CPUs with the help of Palabos. A coupling between both allows efficient execution on hybrid CPU/GPU machines, owing to the fact that the computational load for the finite-element solver is substantially higher than for the fluid solver and thus takes benefit from GPU acceleration. Benchmark tests are executed on up to 400 P100 GPUs and show excellent scaling. However, the communication between CPUs and GPUs is identified as a limiting bottleneck, leading to the conclusion that the fluid simulations need to run on GPU as well to reach exascale performance. This leads to the requirement for a GPU version of the Palabos library.

Large Parts of the Palabos library have been ported to GPU using a very recent formalism, C++ parallel algorithms. This allows efficient porting of the existent code base and execution performance that is situated close to state-of-the-art performance metrics.

With the help of Palabos' MPI backend, the GPU port has been extended to multiple GPUs. The code is executed for a complex fluid simulation problem, including fluid-structure interaction, on a A100 GPU cluster with up to 512 GPUs with close-to-linear scaling.

The multi-GPU port is intended to undergo a public release in its open-source repository (https://gitlab.com/unigespc/palabos), accompanied with a scientific publication describing the approach to GPU implementation and parallelism, and in-depth performance benchmarks.

UniGe developed a new, independent simulation framework for GPU-based LBM-DEM simulation, which complements the Palabos-npFEM framework to provide additional validation and explore different physical regimes. The first, completed prototype is GPU-based for both the LBM and the DEM simulations and their coupling. This work is highly innovative, as existing approaches to coupled fluid-DEM simulation favour the use of CPUs for DEM computations due to the algorithmic complexity of resolving inter-particle interactions.

Numerical validations show that the new framework exhibits state-of-the-art performance and the potential to scale to substantially larger systems. For this, an ongoing effort is to scale the prototype to a massively parallel multi-GPU system.

## Codesign
Codesign activities were initiated earlier between UNIGE and NVIDIA on compilation technology for general-purpose GPU programming. These efforts lead to the efficient implementation of a GPU version of the Palabos library, and to corresponding feedback for the improvement and correction of necessary functionality in the NVIDIA HPC SDK compilation environment. These efforts are currently being extended to include compiler technology for multi-GPU systems without explicit use of explicit message passing through the interconnection network. Current efforts in this collaboration focus on the adaptation of a multi-GPU LBM code to use the SHMEM technology (implemented in the open-source OpenSHMEM framework and the NVIDIA NVSHMEM solution) to adapt inter-GPU communication.

