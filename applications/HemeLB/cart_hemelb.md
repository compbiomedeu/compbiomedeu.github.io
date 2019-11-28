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

**Type of parallelism :** MPI

**Scalability :**	
: Typical run: XXX – XXX cores
: Large run: XXXX – 300,000 cores

**System where it runs :** Cartesius, ARCHER, Blue Waters

**Extra :** HemeLB is part of the [2020 Science programme](http://www.2020science.net/software/hemelb.html)

---
## Download and installation
---

### Access mode:

The source code is available from https://github.com/UCL/hemelb or 
https://drive.google.com/file/d/1khFF8vYL6vSS_f_IG1cJ9iO5K7bLCW7M/view?usp=sharing.

HemeLB source code is available for free under the conditions of [LGPL-3.0 license](https://www.gnu.org/licenses/lgpl-3.0.html).

HemeLB is written in C++ and uses professional software engineering techniques to achieve robustness:
- Test-driven design
- Agile project management
- Ticketing (Trac) and continuous integration (Jenkins)
- Systems programming for deployment (Fabric)
- Cross-platform build and dependency management (CMake)

### Prerequisites

To compile an installation of HemeLB capable to run the provided example, the following software is required:
- C and C++ compilers (Intel, GNU)
- a modern MPI implementation (e.g. Intel, OpenMPI)
- CMake
- Zlib
- [Boost](https://www.boost.org/)
- [METIS - Serial Graph Partitioning and Fill-reducing Matrix Ordering](http://glaros.dtc.umn.edu/gkhome/metis/metis/overview) 
- [ParMETIS](http://glaros.dtc.umn.edu/gkhome/metis/parmetis/overview)
- [TinyXML](https://sourceforge.net/projects/tinyxml/)
- [CPPUnit](https://sourceforge.net/projects/cppunit/)
- [CTemplate](http://goog-ctemplate.sourceforge.net/)

TinyXML, ParMETIS, CPPUnit, CTemplate, Boost and Zlib are provided with HemeLB sources and can be build during the HemeLB build.


### Unoptimized installation steps using included dependencies on Cartesius (not recommended)

1 - Download source code
First download archive from Google Drive: https://drive.google.com/file/d/1khFF8vYL6vSS_f_IG1cJ9iO5K7bLCW7M/view?usp=sharing, and copy it to Cartesius.
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


### Optimized installation steps using existing modules for the dependencies (recommended)
#### with foss toochain:
```bash
module purge
module load surfsara
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load ParMETIS/4.0.3-foss-2018b 
module load Boost/1.67.0-foss-2018b

cd $HEMELBROOT/src
mkdir build_foss
mkdir install_foss
cd build_foss
cmake -DCMAKE_INSTALL_PREFIX=$HEMELBROOT/src/install_foss -DCMAKE_BUILD_TYPE=Release -DPARMETIS_LIBRARY=$EBROOTPARMETIS/lib/libparmetis.a -DPARMETIS_INCLUDE_DIRS=$EBROOTPARMETIS/include -DMETIS_LIBRARY=$EBROOTPARMETIS/lib/libmetis.a -DMETIS_INCLUDE_DIRS=$EBROOTPARMETIS/include -DBoost_DIR=$EBROOTBOOST/lib -DBoost_INCLUDE_DIR=$EBROOTBOOST/include -DZLIB_LIBRARY=$EBROOTZLIB/lib/libz.a -DZLIB_INCLUDE_DIR=$EBROOTZLIB/include ..
# TinyXML, CPPUnit, Ctemplate will be downloaded and built from source
make
make install
```

#### with intel toolchain:
```bash
module purge
module load surfsara
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load ParMETIS/4.0.3-intel-2018b 
module load Boost/1.67.0-intel-2018b

cd $HEMELBROOT/src
mkdir build_intel
mkdir install_intel
cd build_intel
cmake  -DHEMELB_OPTIMISATION='-O3 -xAVX -axCORE-AVX2' -DHEMELB_USE_SSE3=OFF -DCMAKE_INSTALL_PREFIX=$HEMELBROOT/src/install_intel -DCMAKE_BUILD_TYPE=Release -DPARMETIS_LIBRARY=$EBROOTPARMETIS/lib/libparmetis.a -DPARMETIS_INCLUDE_DIRS=$EBROOTPARMETIS/include -DMETIS_LIBRARY=$EBROOTPARMETIS/lib/libmetis.a -DMETIS_INCLUDE_DIRS=$EBROOTPARMETIS/include -DBoost_DIR=$EBROOTBOOST/lib -DBoost_INCLUDE_DIR=$EBROOTBOOST/include -DZLIB_LIBRARY=$EBROOTZLIB/lib/libz.a -DZLIB_INCLUDE_DIR=$EBROOTZLIB/include -DCMAKE_CXX_COMPILER=$EBROOTICC/bin/icpc -DCMAKE_C_COMPILER=$EBROOTICC/bin/icc ..
# TinyXML, CPPUnit, Ctemplate will be downloaded and built from source
make
make install
```

#### Unit tests:
WARNING: the unittests do not work.
       With '-DHEMELB_BUILD_TESTS_UNIT=ON'
        we get error such as 
          /home/maximem/CompBioMed/hemelb_google_drive/hemelb-pure-vphys/src/unittests/geometry/NeedsTests.h:221:10: error: ‘tr1’ in namespace ‘std’ does not name a type
          std::tr1::unordered_set<site_t> inputNeededBlocks;
        this is due to the use of 'std::tr1' in the tests, which was an extension to the C++03. It was included in C++11, which is used for HemeLB. tr1 is now deprecated (is it?). Does it come from GoogleTest ? (cf. https://github.com/Microsoft/TestAdapterForGoogleTest/issues/119)

---
## Running HemeLB on Cartesius
---
#### unittests
```bash
cd $HEMELBROOT/src/unittests
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DHEMELB_DEPENDENCIES_PATH=$HEMELBROOT/dep/ -DHEMELB_DEPENDENCIES_INSTALL_PATH=$HEMELBROOT/dep/install ..
```

### Input Preparation

#### Use case: XXXX

#### Input files

### Runtime Information


---
### Benchmarking and scalability of HemeLB on Cartesius
---

TODO

---
### Guidelines for efficient parallel programs using HemeLB
---

TODO

---

[Offical documentation](https://github.com/UCL/hemelb)

---

[Back](../..)