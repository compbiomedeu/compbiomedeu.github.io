---
layout: plain
---

# [INCOMPLETE PAGE - WORK IN PROGRESS] HemeLB - CompBioMed User Guide

**Provider :** [Centre for Computational Science, UCL, London, UK ](http://ccs.chem.ucl.ac.uk/)

---
## Description
---

**License :** LGPL v3.0

**Target system :**  HPC platforms

**Target users :** Interventional radiologists, ???

**Type of parallelism :** ???

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – XXXX cores

**System where it runs :** Cartesius, ???

**Extra :** HemeLB is part of the [2020 Science programme](http://www.2020science.net/software/hemelb.html)

---
## Download and installation
---

### Access mode:

Source code available from https://github.com/UCL/hemelb or 
https://drive.google.com/file/d/1khFF8vYL6vSS_f_IG1cJ9iO5K7bLCW7M/view?usp=sharing.

HemeLB source code is available for free under the conditions of [LGPL-3.0 license](https://www.gnu.org/licenses/lgpl-3.0.html).

HemeLB is written in C++. HemeLB uses professional software engineering techniques to achieve robustness:
- Test-driven design
- Agile project management
- Ticketing (Trac) and continuous integration (Jenkins)
- Systems programming for deployment (Fabric)
- Cross-platform build and dependency management (CMake)

### Prerequisites

To compile an installation of HemeLB capable to run the provided example, the following software is required:
- C and C++ compilers (Intel/GNU)
- MPI
- CMake
- Zlib
- Boost
- [METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) 
- ParMETIS
- TinyXML
- CPPUnit
- CTemplate

TinyXML, ParMETIS, CPPUnit, CTemplate, Boost and Zlib are provided with HemeLB sources and can be build during the HemeLB build.


### Installation steps using included dependencies

1 - Download from https://drive.google.com/file/d/1khFF8vYL6vSS_f_IG1cJ9iO5K7bLCW7M/view?usp=sharing
```bash
tar xvzf hemelb-pure-vphys.tar.gz
cd hemelb-pure-vphys/
export HEMELBROOT=$PWD
```

2 - Build all dependencies (except CMake, zlib, compilers and MPI)
2.1 - Enter the dependencies directory and create build directory in $HEMELBROOT/dep
```bash
cd dep
mkdir build
```

2.2 - Clean module environment and load toolchain (compilers, MPI and most used tools) and CMake modules
```bash
module purge
module load surfsara
module load foss/2018b    # or intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
```

2.3 - Configure using CMake
```bash
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DZLIB_LIBRARY=$EBROOTZLIB/lib -DZLIB_INCLUDE_DIR=$EBROOTZLIB/include ..
```

2.4 - Build dependencies (they will be installed in $HEMELBROOT/dep/install)
```bash
make
```

3 - Build HemeLB using downloaded dependencies
3.1 - Enter the HemeLB source directory ($HEMELBROOT/src) and create build and install directories
```bash
cd ../../src/
mkdir build
mkdir install
```

3.3 - Configure using CMake
```bash
cd build
cmake -DCMAKE_INSTALL_PREFIX=$HEMELBROOT/src/install -DCMAKE_BUILD_TYPE=Release ..
```

3.4 - Build HemeLB and install in the 'install' directory we created ($HEMELBROOT/src/install)
```bash
make
```
Then the 'hemelb' executable is located in '$HEMELBROOT/src/install/bin'

---
## Running HemeLB
---

### Input Preparation

#### Use case: XXXX

#### Input files

### Runtime Information


---

[Offical documentation](https://github.com/UCL/hemelb)

---

[Back](../..)
