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
Codesign activities were initiated earlier between UNIGE and NVIDIA on compilation technology for general-purpose GPU programming. These efforts lead to the efficient implementation of a GPU version of the Palabos library, and corresponding feedback for the improvement and correction of necessary functionality in the NVIDIA HPC SDK compilation environment. These efforts are currently being extended to include compiler technology for multi-GPU systems without explicit use of explicit message passing through the interconnection network. Current efforts in this collaboration focus on adapting a multi-GPU LBM code to use the SHMEM technology (implemented in the open-source OpenSHMEM framework and the NVIDIA NVSHMEM solution) to adapt inter-GPU communication.

## Energy analysis on HPC
Strong scaling tests we carried out to test the parallel efficiency of Palabos for the pre-processing (reading of an input file, voxelization, and setting up of the domain) and the simulation of fluid flow in an aneurysm Geometry. By using 495 nodes (63,360 cores) the observed performance (Million lattice updates per second – MLUPS) was 47677.60 or 47.67 GLUPS. The job processes were bound to each NUMA domain (via the `ldom` option with the srun launcher). As with HemeLB the binding of NUMA domains and/or memory sockets resulted in the best performance. Shown in the figure below, we highlight the strong scaling behaviour of Palabos on the full CPU partition on Snellius (495 nodes containing AMD EPYC 7742, 2.25 GHz, 64-core (x2)). The full system test was carried out using an aneurysm geometry containing 1.77e9 fluid sites, which showed very good strong scaling with an efficiency of 77.3% at 495 nodes.
<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/e5756930-ab51-4bb3-b062-7775c4a6a646">
</p>
Additionally, Energy analysis was also carried out on the Palabos strong scaling test to highlight the energy footprint of a large-scale scientific application. The energy analysis was carried out using the Energy Aware Runtime library (EAR). Scaling behavior of the total energy usage of Palabos is documented in the top right panel of the figure above. 
We observed a departure of linear scaling of total energy use, with the decrease in linear scaling of Palabos. This highlights the fact, that the longer and application is running on, the more energy it will use, thus an increase in performance relates to a decrease in total energy usage of an application. An estimated 30 kWh were consumed for the full system run of Palabos (495 nodes). The wall time of the 495-node job was approximately 10 minutes.
Additional energy analysis of Palabos was performed highlighting the performance increase and energy decrease when under populating the AMD CPU nodes with processes, and thus maximizing the memory bandwidth available to the application. The results showing the performance and energy differences between 64ppn case and a 128 ppn are shown in the figure below. 
<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/16112dfb-f48c-4a49-ad46-99598a684167">
</p>
We observe an increase in performance and a decrease in energy usage at larger node counts (120 nodes) when underpopulating the nodes with MPI ranks vs a fully populated case. We also observe lower power per node usage across all nodes when underpopulating the nodes as compared to a fully populated case, as shown in the top right panel.

Additional performance and energy analysis of Palabos is provided in the figure below. Here we detail the profile of Palabos in a case which uses 8 CPU nodes. Here we can see on the iteration and node level the exact behaviour of the application. This lets us characterize the application and understand more about how Palabos uses the resources of the nodes during the runtime of the application. These runtime resource usage metrics are shown.

<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/e65e2d9c-5cfa-4575-99d1-cb24ab538d79">
</p>

Weak scaling tests of Palabos were also carried out on the thin (256 GiB/node) and fat (1TiB/node) CPU partitions of Snellius. The same aneurysm geometry was used in the weak scaling test as was used in the strong scaling tests. To ensure that the geometry scaled with the resources, we maintained that 2 million lattice sites were allocated to each core. We observed approximately 93% weak scaling efficiency of Palabos up to 14 thin nodes (1,792 cores) on the thin partition, when running with 64 ppn. On the fat partition we observe almost perfect scaling up to 7 fat nodes (896 cores), when running with 128 ppn.


<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/07987455-0e6e-45be-97e9-2902b9b40532">
</p>





