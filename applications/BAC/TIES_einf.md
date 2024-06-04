---
layout: plain
---

# BAC

**Provider :** [**University College London**](https://www.compbiomed.eu/about/compbiomed2-core-partners/ucl/)

## HPC development

BAC or Binding Affinity Calculator encompasses two highly related methodologies. These two methodologies are Thermodynamic Integration with Enhanced Sampling (TIES) and Enhanced Sampling of Molecular dynamics with Approximation of Continuum Solvent (ESMACS). Centrally both these methods make use of ensembles of molecular dynamics (MD) simulations to control for the inherently chaotic nature of the trajectories MD produces. The trajectories collected can be used to calculate the binding affinity of ligand and protein. These affinities are calculated as a free energy of binding which is a critical quantity relevant in drug design and personalised medicine. The free energy difference between two states is an important quantity in biology, because it determines the ensemble at equilibrium. The binding free energy of an inhibitor often correlates to its effectiveness. As such the accurate prediction of binding free energies has been a longstanding goal of computational methods.

TIES aims to alleviate many of the bottlenecks in relative binding free energy calculations. Due to the complexity and large computational cost of running these calculations, TIES is targeted at expert users experienced in alchemical free energy calculation. We provide tutorials for how to run calculations using TIES via our online documentation. These tutorials make it easier for less expert users to apply these methods however the computation cost barriers remain high.

We made an initial open-source release of all TIES related code that was followed by rounds of user engagement and Quality Assurance (QA) testing. We are now nearing the release of version 2 of the code which includes many improvements; of most significance here is the inclusion of the Conda solution for Python package dissemination and this will be explained in later sections. 

TIES in its most recent iteration has been successfully tested with CUDA, OpenCL and HIP which positions it to use A100s (CUDA/Polaris), MI250Xs (HIP/Frontera) and Ponte Vecchio (PVCs) (OpenCL/Aurora/SuperMUC-NG phase 2) GPUs on the latest generation of HPC. Of these named next generation machines TIES has been tested on Polaris and the Frontier transition testbed machine named Crusher. We are currently in the process of testing TIES on Florentina, the Aurora transition testbed machine. 

<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/618824c8-d503-4239-8153-dabdc63c9e01">
</p>

The figure above shows scaling results for TIES running on Crusher (right panel) and Polaris (left panel). The figure demonstrates linear scaling of TIES on these new platforms using up to around 50% of the two machines (the scalability test was executed up to half systems because of the long queue times to access the whole machine). The figure below shows a performance comparison of Polaris and Crusher using CUDA, HIP and OpenCL. It demonstrates that using HIP and CUDA on MI250Xs and A100s respectively provide similar performance in the range of atoms relevant for BAC (15-100k atoms). 

<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/be8beb88-01e8-4602-a393-3da148aa486a">
</p>

IMPECCABLE is already functional on a variety of supercomputers including OLCF’s Frontier (the world's only exascale machine publicly available currently), ALCF’s Polaris and NSF’s Frontera. We have been able to successfully test its performance at 95% usage (CPUs and GPUs concurrently) on Frontera and Summit and ~80% on Frontier. Also, we are currently porting our codes/applications to Sunspot (a small production partition on Aurora) so as to get ready to implement our workflow on Aurora (the full machine) as soon as it is available to us (anticipated in Q4 2024). To prepare TIES to be performant on Aurora we have engaged in joint efforts with the DiRAC Federation to port OpenMM to SYCL. SYCL is the native GPU code used by Intel’s GPUs and OpenMM is one of the molecular-dynamics engines used by TIES. The first conclusion of this project was that the automated porting tools provided by Intel (DPC++ Compatibility Tool) were not sufficient to port OpenMM to SYCL and therefore a manual port would require significant time investment. The second conclusion was that OpenCL is a viable alternative to SYCL, which will allow PVCs to be used by TIES with zero-time investment. Our profiling efforts on Florentina will be key to determining the performance impact and then improving performance while using OpenCL with PVC.

ESMACS and TIES workflows have been also supported by the Rutger’s team within the context of COVID-workflows, with the development and integration of their suite Radical CyberTools (RCT). RCT, specifically RADICAL-Ensemble Toolkit (radical.entk), developed new capabilities to support ESMACS and TIES workflows: a `post_exec` hook was implemented to allow dynamic adaptation of the workflow structure depending on intermediate simulation results.

`Radical-EnTK` as RCT's workflow layer provides a high-level API to express pipelines, stages, and tasks as fundamental abstractions for workflows. Those abstractions match the protocols defined by ESMACS and TIES well. `Radical-Pilot` as RCT's task execution layer signs responsible for executing eligible simulation or analysis tasks on the target resources at scale. RCT as execution layer stays mostly out of the way in the context of application-level communication and storage layers. It does though provide means to express data staging requirements and data dependencies between tasks.

In the context of COVID docking pipelines which were running in production soon after the beginning of the pandemic, ESMACS and TIES simulations have been used as a part of a larger sequence of drug analysis pipelines at scale and have been successfully used to screen for drug candidates on Frontera, Summit and Theta. A total of several billion drug candidates were screened during this campaign. RCT further improved the support for executing MPI tasks at scale. On the Summit HPC system, RCT switched from using `jsrun` as launch method (which maxed out at ~800 concurrent tasks) to PMI-X / PRRTE which allows us to scale ESMACS/TIES workloads to the full machine.

GCS/LRZ granted a 2-year project for COVID research at the beginning of 2020, with an allocation of 30 million core hours. With the resource, we have performed large scale free energy predictions for thousands of compounds binding with key proteins in SARS-CoV-2. We applied for an extension when the initial project was going to expire. The extension has been granted for an extra year, with an additional allocation of 15 million core hours.
 
We have used the full SuperMUC-NG production partition a few times during its block operations. The most recent one was in November 2021, during which we were able to use 6400 nodes of SuperMUC-NG for 11 hours. We used a single submission script to execute 128 free energy simulations, each consisting of 25 replicas. All jobs were successfully completed. A total of ~3.38 M core hours were consumed during the block operation.



