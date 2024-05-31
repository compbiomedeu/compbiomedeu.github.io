---
layout: plain
---

```bash
!/bin/bash
## Compilation/build script for HEMELB
## Run from found location

export SOURCE_DIR=$HOME/HemePure-GPU

## MODULE loads
module load cmake/3.24.3
module load gmp/6.2.1
module load mpfr/4.1.0
module load mpc/1.2.1
module load gcc/11.3.0
module load zlib/1.2.13--gcc--11.3.0
module load openmpi/4.1.4--gcc--11.3.0-cuda-11.8
module load boost/1.80.0--openmpi--4.1.4--gcc--11.3.0
module load python/3.10.8--gcc--11.3.0
module load cuda/11.8

export CC=$(which gcc)
export CXX=$(which g++)
export MPI_C_COMPILER=$(which mpicc)
export MPI_CXX_COMPILER=$(which mpicxx)

## HEMELB build
# 1) Dependencies
BuildDep(){
cd $SOURCE_DIR/dep
rm -rf build
mkdir build
cd build
cmake -DCMAKE_C_COMPILER=${CC} -DCMAKE_CXX_COMPILER=${CXX} ..
make -j  && echo "Done HemeLB Dependencies"
cd ../..
}

# 2) Source code
BuildSource(){
cd $SOURCE_DIR/src
rm -rf build
mkdir build
cd build
cmake  \
  -DCMAKE_CXX_FLAGS="-std=c++11 -g -Wno-narrowing -ltirpc" \
  -DCMAKE_C_COMPILER=${CC} \
  -DCMAKE_CXX_COMPILER=${CXX} \
  -DHEMELB_INLET_BOUNDARY="NASHZEROTHORDERPRESSUREIOLET"\
  -DHEMELB_WALL_INLET_BOUNDARY="NASHZEROTHORDERPRESSURESBB"\
  -DHEMELB_OUTLET_BOUNDARY="NASHZEROTHORDERPRESSUREIOLET"\
  -DHEMELB_WALL_OUTLET_BOUNDARY="NASHZEROTHORDERPRESSURESBB"\
  -DCMAKE_CUDA_FLAGS="-ccbin g++ -gencode arch=compute_80,code=sm_80 -lineinfo --ptxas-options=-v --disable-warnings" \
        $SOURCE_DIR/src
make -j && echo "Done HemeLB Source"
cd ../..
}

BuildDep
BuildSource
echo "Done build all"
```
