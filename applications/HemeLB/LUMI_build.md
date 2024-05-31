```bash
 #!/bin/bash
## Compilation/build script for HEMELB

export SOURCE_DIR=$HOME/HemePure-GPU

## MODULE loads
module load cpe/23.09
module load PrgEnv-amd
module load craype-accel-amd-gfx90a   # For GPU Aware MPI
module load perftools-base
module load perftools
module load amd/5.2.3
module load gcc-mixed/12.2.0
module load LUMI/23.09
module load Boost/1.82.0-cpeAMD-23.09 #needed for dep
module load buildtools/23.09 # Cmake
module load libtirpc/1.3.3-cpeAMD-23.09
module load GObject-Introspection/1.76.1-cpeAMD-23.09-cray-python3.10

export CC=$(which gcc)
export CXX=$(which g++)
export MPI_C_COMPILER=$(which mpicc)
export MPI_CXX_COMPILER=$(which mpicxx)

## HEMELB build
# 1) Dependencies
BuildDep(){
cd src
rm -rf build
mkdir build
cd build

export LD_LIBRARY_PATH=$CRAY_LD_LIBRARY_PATH:$LD_LIBRARY_PATH
export PATH=${ROCM_PATH}/llvm/bin:${PATH}
export LD_LIBRARY_PATH=${ROCM_PATH}/llvm/lib:${LD_LIBRARY_PATH}
export HIP_PATH=${ROCM_PATH}
export MPICH_GPU_SUPPORT_ENABLED=1
 
export MPICH_ROOT=/opt/cray/pe/mpich/8.1.27
export GTL_ROOT=$MPICH_ROOT/gtl/lib
export MPICH_DIR=${MPICH_ROOT}/ofi/amd/5.0
 
export MPI_CFLAGS="${CRAY_XPMEM_INCLUDE_OPTS} -I${MPICH_DIR}/include "
export MPI_LDFLAGS=" ${CRAY_XPMEM_POST_LINK_OPTS} -lxpmem  -Wl,-rpath=${MPICH_DIR}/lib -L${MPICH_DIR}/lib -lmpi -Wl,-rpath=${GTL_ROOT} -L${GTL_ROOT} -lmpi_gtl_hsa -L${ROCM_PATH}/llvm/lib -Wl,-rpath=${ROCM_PATH}/llvm/lib"


cmake -DHEMELB_GPU_BACKEND=HIP_ROCM \
  -DCMAKE_CXX_COMPILER=/opt/rocm-5.2.3/llvm/bin/amdclang++ \
  -DCMAKE_C_COMPILER=/opt/rocm-5.2.3/llvm/bin/amdclang \
  -DCMAKE_HIP_COMPILER=/opt/rocm-5.2.3/llvm/bin/amdclang++ \
  -DHEMELB_COMPUTE_ARCHITECTURE=NEUTRAL \
  -DCMAKE_CXX_EXTENSIONS=OFF \
  -DHEMELB_CUDA_AWARE_MPI=ON \
  -DCMAKE_CXX_FLAGS="-O3 -fopenmp -g  ${MPI_CFLAGS}" \
  -DCMAKE_HIP_FLAGS="-O3 -fopenmp -g  ${MPI_CFLAGS} --offload-arch=gfx90a" \
  -DCMAKE_SHARED_LINKER_FLAGS="-g -O3 -fopenmp ${MPI_LDFLAGS} --offload-arch=gfx90a" \
  -DHEMELB_LOG_LEVEL="Info" \
  -DHEMELB_USE_MPI_PARALLEL_IO=OFF \
  -DHEMELB_USE_VELOCITY_WEIGHTS_FILE="OFF" \
  -DHEMELB_INLET_BOUNDARY="NASHZEROTHORDERPRESSUREIOLET" \
  -DHEMELB_OUTLET_BOUNDARY="NASHZEROTHORDERPRESSUREIOLET" \
  -DHEMELB_WALL_INLET_BOUNDARY="NASHZEROTHORDERPRESSURESBB" \
  -DHEMELB_WALL_OUTLET_BOUNDARY="NASHZEROTHORDERPRESSURESBB" \
  -DHEMELB_WALL_BOUNDARY="SIMPLEBOUNCEBACK" \
  ..

make -j
cd ../..
}
```
