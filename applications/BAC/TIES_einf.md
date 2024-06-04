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



The figure above shows scaling results for TIES running on Crusher and Polaris (the former being approved for release here by Oak Ridge National Lab). Figure 2 demonstrates linear scaling of TIES on these new platforms using up to around 50% of the two machines (the scalability test was executed up to half systems because of the long queue times to access the whole machine). Figure 3 shows a performance comparison of Polaris and Crusher using CUDA, HIP and OpenCL. Figure 3 demonstrates that using HIP and CUDA on MI250Xs and A100s respectively provide similar performance in the range of atoms relevant for BAC (15-100k atoms). 

