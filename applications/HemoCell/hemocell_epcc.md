---
layout: plain
---

# HemoCell on ARCHER

Archer is a Cray supercomputer, so the build procedure for Archer has a few differences from the build procedure for Cartesius.
Moreover, Archer uses the PBS workload manager, whereas Cartesius uses Slurm. So the submission scripts are different too.

There is a known problem when compiling HemoCell with the Cray compiler, and we were not able to successfully compile HemoCell with the Cray compiler on Archer.
So when building HemoCell on Archer we recommend you to use the GNU or Intel compilers.

CMake supports Cray architectures, but option '-DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment' has to be passed to the cmake command so that the compilers are properly set by CMake.

The download, configuration and build procedure is similar to the procedure for Cartesius. We do not show the dowload steps which are identical.

1 - Configure HemoCell build options
Choose a toolchain between foss (GNU) and Intel.
Here we use the 'CMakeLists.txt' file available on the github repository in branch 'cmakelists_architecture_specific_flags'.
One can use predefined optimisation flags for given set of architectures ('archer', 'cartesius', 'cartesius-haswell', cartesius-broadwell') using the '-DARCH' flag. The default behaviour when this flag is not used is to use '-march=native' for the foss toolchain, and '-xHost -axAVX,AVX2,CORE-AVX2,AVX512' for the Intel toolchain.
Remark: one can add additional flags using variables 'CXXFLAGS' or 'CMAKE_CXX_FLAGS'.

```bash
# with foss toolchain
module swap PrgEnv-cray PrgEnv-gnu
# load modules for the dependencies
module load cmake/3.10.2
module load cray-hdf5-parallel/1.10.0.1
# configure
cd /work/d137/d137/maximem/hemocell/hemocell-2.0/
export HEMOCELLROOT=$PWD
cd $HEMOCELLROOT/build/hemocell
cmake . -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DENABLE_PARMETIS=0 -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment -DARCH=archer
```

```bash
# with intel toolchain
module swap PrgEnv-cray PrgEnv-intel
module load gcc   # needed with intel compiler to get the GNU header files for C++11 support
# load modules for the dependencies
module load cmake/3.10.2
module load cray-hdf5-parallel/1.10.0.1
# configure
cd /work/d137/d137/maximem/hemocell/hemocell-2.0/
export HEMOCELLROOT=$PWD
cd $HEMOCELLROOT/build/hemocell
cmake . -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DENABLE_PARMETIS=0 -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment -DARCH=archer
```
This creates the 'Makefile'.

2 - Build the library
```bash
# build
make -j4
```
This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.

3 - Run computation intensive example

```bash
cd $HEMOCELLROOT/examples
# download from surfdrive
wget https://surfdrive.surf.nl/files/index.php/s/PvOpsuCv6ROpsYg/download
mv download performance_testing.tgz
tar xvzf performance_testing.tgz
cd performance_testing
```

2 - Configure build options
Using the 'CMakeLists_template.txt' file available on the github repository in branch 'cmakelists_architecture_specific_flags'.
```bash
# modify 'CMakeLists.txt'
cp ../CMakeLists_template.txt CMakeLists.txt
sed -i -e 's/FOLDER_NAME__/performance_testing/g' CMakeLists.txt
```
Use the same toolchain (foss (GNU) or Intel) that was used to build the library.
```bash
# with foss toolchain
module swap PrgEnv-cray PrgEnv-gnu
# load modules for the dependencies
module load cmake/3.10.2
module load cray-hdf5-parallel/1.10.0.1
# configure
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment -DARCH=archer
# build
make -j4
cd ..
```

```bash
# with intel toolchain
module swap PrgEnv-cray PrgEnv-intel
module load gcc   # needed with intel compiler to get the GNU header files for C++11 support
# load modules for the dependencies
module load cmake/3.10.2
module load cray-hdf5-parallel/1.10.0.1
# configure
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment -DARCH=archer
# build
make -j4
cd ..
```

Example of a submission script for Archer:
```bash
#!/bin/bash --login

# PBS -N is the job name (e.g. Example_MPI_Job)
#PBS -N hematocrit33
# PBS -l select is the number of nodes requested (e.g. 64 nodes=1536 cores)
#PBS -l select=3
# PBS -l walltime, maximum walltime allowed (e.g. 20 minutes)
#PBS -l walltime=00:30:00

# Replace [budget code] below with your project code (e.g. t01)
#PBS -A d137-surfsara


# load modules for foss toolchain
module swap PrgEnv-cray PrgEnv-gnu
module load cray-hdf5-parallel/1.10.0.1

# # load modules for intel toolchain
# module swap PrgEnv-cray PrgEnv-intel
# module load gcc   # needed with intel compiler to get the GNU header files for C++11 support
# module load cray-hdf5-parallel/1.10.0.1

# Make sure any symbolic links are resolved to absolute path
export PBS_O_WORKDIR=$(readlink -f $PBS_O_WORKDIR)

# Change to the directory that the job was submitted from
# (remember this should be on the /work filesystem)
cd $PBS_O_WORKDIR

# Set the number of threads to 1
#   This prevents any system libraries from automatically 
#   using threading.
export OMP_NUM_THREADS=1

# Launch the parallel job
aprun -n 64 ../performance_testing ../configs/config_64.xml > hematocrit_33_64_P_$PBS_JOBID.out 2> hematocrit_33_64_P_$PBS_JOBID.err

mv -r tmp_64/  outputs/tmp_$PBS_JOBID
```

```bash 
# create output directory
mkdir hematocrit_33/outputs
# submit job
qsub hematocrit_33/hematocrit_33_64.job
```

---

[Back](../..)
