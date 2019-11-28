---
layout: plain
---

HemeLB source code is available for free under the conditions of [LGPL-3.0 license](https://www.gnu.org/licenses/lgpl-3.0.html) through the [HemeLB website](https://www.hemelb.org).

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
module load 2019
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
module load 2019
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
module load 2019
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

---

[Back](../..)
