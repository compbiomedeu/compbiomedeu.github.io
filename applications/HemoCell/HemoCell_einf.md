---
layout: plain
---

# HemoCell

**Provider :** [**University of Amsterdam**](https://www.compbiomed.eu/about/compbiomed2-core-partners/uva/)

HemoCell is a high-performance cellular suspension simulation library. It is designed to simulate blood flows on the cellular scale, where blood is represented as a composite of blood plasma with immersed cells such as red blood cells, platelets, and white blood cells. The open-source library tightly integrates three main components:  a lattice Boltzmann solver (LBM) to compute the flow of blood plasma, an optimized implementation of immersed boundary coupling (IBM), and a high-performance solver for cellular mechanics and interaction (DEM). 

## Performance optimisation and bottlenecks

HemoCell is an advanced code that has been in development for 8 years (the code repository analysis estimates 18,000 development hours in total). The development and application of this code have yielded more than two dozen publications in peer-reviewed Q1 journals (including field-leading journals such as Blood), and it is being picked up by multiple third parties as evidenced by several independent publications and PhDs using HemoCell as the core component. The main selling points of HemoCell among competing solutions (primarily from the US) are the high-performance architecture and wide applicability due to the included advanced features and boundary conditions. These in combination allow the simulations to efficiently complement lab experiments by creating 1:1 digital twins of microfluidic setups, that allow access to many details of the flow that otherwise would not be measurable.

The development of HemoCell focuses on extending these capabilities to allow an even wider range of applications. The goals include larger domains (to move from microfluidics towards organ-scale computations), and additional coupled biochemical processes (to include for instance thrombus formation). The compute requirements of such tasks often fall into the exascale or pre-exascale category.

## Load-balancing

HemoCell includes many low-level optimizations for more efficient computing that are effective regardless of the simulation scenario. When carrying out more complex simulations in larger domains, the optimal decomposition of the simulation domain becomes increasingly important to balance the computational load as homogeneously as possible among the computing processes. If the simulation encompasses longer time spans, this static load-balancing problem can develop into a more complicated dynamic load-balancing question. Cells move and aggregate in some regions, while vacating others, creating an inhomogeneous distribution of computational load. While the technological implementation of how to carry out a load-balancing step is available, a more difficult question is when to do a rebalancing (since it is not free), and into what decomposition? It is especially challenging to answer in the case of coupled codes such as HemoCell, where the fluid component (LBM) has a different performance and scaling characteristics from the cell mechanics (DEM). Furthermore, these behave differently across different compute architectures. We have therefore developed a performance prediction model that after a relatively cheap calibration (< 1% of the compute cost of a production run) can estimate the performance over various decompositions. In a recently completed development effort, this new performance prediction model is embedded in the load-balancing mechanism of HemoCell. It provides the cost function for potential decompositions that serves as input for parallel METIS to compute the optimal decomposition. Currently, we are benchmarking this method on various architectures and various simulation setups. The typical performance prediction accuracy is within 10% error (see Figure 6 below for an example over 2048 CPU cores).

## Hybrid CPU-GPU execution 

Due to the trend of increasing accelerator hardware in current supercomputers, we have explored the option of partial GPU porting. HemoCell as a coupled code includes an LBM fluid solver, that is well known to be suitable for GPU execution. This is due primarily to two reasons: (1) the method employs a regular grid that maps very well to GPU architectures, and (2) it decouples the computationally heavy and the memory operation-intensive parts opening the venue for relatively easy optimization of both. The porting effort was carried out with strong support from EPCC, and the results are detailed in a separate document (WP 4.3 - Report on Porting HemoCell to GPUs). As a summary, a subset of HemoCell was successfully ported to GPU using the parallel algorithms STL interface (ISO C++17) with CUDA as the back-end. The implementation was tested on ARCHER2 (UK), Cirrus (UK), and Snellius (Netherlands). The current conclusion is that while partial GPU porting for hybrid execution is feasible, in order to maintain good performance and good scaling, the code needs to be optimized separately for every architecture. The primary reason lies with the coupled nature of the code. The ratio of available compute power for GPU and CPU is fixed for a given machine (e.g., a fixed number of GPU accelerators and a fixed number of CPU cores per node). However, the balance of fluid (GPU) and cell (CPU) computations don’t necessarily match what is available from the hardware. In the case of dynamic load-balancing, the problem becomes even more complicated and renders the current solution unfeasible for production runs.

We are investigating these questions further; one potential solution that seems promising is the instrumentation of hardware. That is, influencing the frequency (and energy consumption) of the executing CPUs and GPUs according to the needs of the simulation setup to bring the compute capability of the hardware components in synchrony with the needs of the simulation.

## Energy measurement 

In order to understand this emerging optimization target, we have carried out large-scale simulations of up to 330,000 CPU cores. During these runs both the energy and the performance (in terms of wall-time) were measured. The measurements were carried out on Archer2 (UK) and SuperMUC-NG (Germany). The benchmark case included the regular components of a production run: initialization, computation, and data saving. The energy usage was measured on the node level, the performance was measured fine-grained on a process level. The overall results show that the computation scales very well for a tightly coupled code, maintaining over 70% efficiency even on the largest scales. 

There was one significant difficulty at scale. Above 120,000 computing processes, the I/O system becomes saturated, which results in a significant overhead in the total wall time. Data is written in parallel, due to its size O(100 GB), and the current system implementations do not seem to be able to handle the high number of parallel write requests well. The data can be saved locally at the nodes with much better performance; however, the same problem will still arise at the end of the simulation when the results need to be copied to a network drive. Furthermore, saving data to a network drive is a requirement for load-balancing, that requires checkpointing the simulation before rebalancing.


<p align="center">
<img width="600" alt="image" src="https://github.com/compbiomedeu/compbiomedeu.github.io/assets/32201263/589b671d-d762-4a11-8c91-27bd0d93e7df">
</p>

The energy consumption (not counting the time spent on I/O operations) seems to be close to linear (see figure above). 

The energy costs of exascale executions will be very substantial. At 330,000 CPU cores, the benchmark run costs approximately 240 kWh. The smallest exascale-range run is three times larger, and a production simulation takes around O(1000) times longer. This very simple linear extrapolation projects an energy cost in the hundreds of thousands of euros for a single exascale execution. Similarly, for the hybrid execution case, the use of efficient hardware instrumentation could prove useful here by lowering the energy costs, for instance by decreasing the frequency of idling cores during I/O operations. 

Furthermore, in collaboration with Eviden (Atos), we are measuring the performance and energy usage of HemoCell on upcoming HPC architectures. The processors included in this investigation are: 

* AMD Epyc Bergamo 9754
* Intel Sapphire Rapids 9480 + DDR5
* Intel Sapphire Rapids 9480 + HBM2e
* Ampere Altra Q8030

The preliminary results suggest significant differences both in energy consumption and in scaling. The ARM-based architecture (Ampere Altra) used significantly less energy compared to the x86-based CPUs (up to a factor 2 difference). At the same time, the scaling efficiency to utilize all 80 cores seems poor. The reason is most likely the lower memory bandwidth (approx. half of the other DDR5 configurations), which with the current benchmark could only support sufficient scaling up to 64 cores. Similarly, the AMD Bergamo-based configuration also showed less than optimal scaling, possibly due to the smaller CPU cache size (i.e., producing many cache misses). This is under further investigation. 

Out of the four configurations the Intel Sapphire Rapids CPU combined with HBM2e memory proved to be the best solution leading to energy efficiency comparable to Ampere (approx. +20% energy), and the highest per node performance (approx. twice the other configurations). The profile analysis of HemoCell shows that this application to the test case used for benchmarking is 50 % CPU-frequency and ~50% CPU memory bandwidth dependent. The application therefore benefits the most from high clock speeds and high memory bandwidth.

## Deployment and performance engineering

### Issue/bug tracking
Recently the source code version control of HemoCell has been migrated from Gitlab to Github to facilitate open-source collaborations with 3rd party research groups. The Github interface is used to report problems with the code. Apart from this, the ‘Discussion’ surface has also been enabled and already generated a significant amount of communication with external users.

### Configure/build tools and code generators
CMake is used as a cross-platform build system for HemoCell, and our code can run on a variety of systems and HPC clusters. There are machine-specific configuration files available for several HPCs in the EU. Furthermore, several scripts are also provided with the source code to facilitate post-processing and visualisation of the output.

### Code coverage/unit/build test tools
The testing facility is currently partially implemented. There is a limited set of unit tests available, however, we are working on providing a much wider coverage of the functionality. We have postponed this work for until after the move to Github which concluded this September. Now we are in the process of extending and enriching our testing suite, triggered by Github actions to build a robust CD/CI environment.

