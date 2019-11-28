---
layout: plain
---

# HemoCell on Cartesius (SURFsara)

1 - Download the most recent tarball from the [official HemoCell website's download page](https://www.hemocell.eu/user_guide/Downloads.html) (e.g. hemocell-2.0.tgz).
```bash
wget https://www.hemocell.eu/user_guide/_downloads/hemocell-2.0.tgz
tar xvzf hemocell-2.0.tgz
cd hemocell-2.0/
export HEMOCELLROOT=$PWD
```

2 - Download and patch Palabos
```bash
wget http://www.palabos.org/images/palabos_releases/palabos-v2.0r0.tgz
tar xvzf palabos-v2.0r0.tgz 
mv palabos-v2.0r0 palabos
cd patch && ./patchPLB.sh
cd ..
```

3 - Configure HemoCell build options
Choose a toolchain between foss (GNU) and Intel.
Here we use the 'CMakeLists.txt' file available on the github repository in branch 'cmakelists_architecture_specific_flags'.
One can use predefined optimisation flags for given set of architectures ('archer', 'cartesius', 'cartesius-haswell', cartesius-broadwell') using the '-DARCH' flag. The default behaviour when this flag is not used is to use '-march=native' for the foss toolchain, and '-xHost -axAVX,AVX2,CORE-AVX2,AVX512' for the Intel toolchain.
Remark: one can add additional flags using variables 'CXXFLAGS' or 'CMAKE_CXX_FLAGS'.

```bash
# with foss toolchain
# load modules for the dependencies
module load 2019
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
# configure
cd $HEMOCELLROOT/build/hemocell
cmake . -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DARCH="cartesius"
```

```bash
# with intel toolchain
# load modules for the dependencies
module load 2019
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
# configure
cd $HEMOCELLROOT/build/hemocell
CC=icc CXX=icpc cmake . -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DENABLE_PARMETIS=1 -DARCH="cartesius"
```
This creates the 'Makefile'.

4 - Build the library
```bash
# build
make -j4
```
This creates 'libhemocell.a' and 'libhemocell_pre_all_deps.a' in '$HEMOCELLROOT/build/hemocell'.

---
## Running HemoCell on Cartesius
---

### Simple Use case with foss toolchain: oneCellShear

1 - Configure build options
Here we use the 'CMakeLists_template.txt' file available on the github repository in branch 'cmakelists_architecture_specific_flags'.
```bash
cd $HEMOCELLROOT/examples/
# Update 'CMakeLists.txt' for all testcases with the new 'CMakeLists_template.txt'
make cmakefiles
cd $HEMOCELLROOT/examples/oneCellShear
```

Use the same toolchain (foss (GNU) or Intel) that was used to build the library.
```bash
# with foss toolchain
# load modules for the dependencies
module load 2019
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
# configure
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DARCH="cartesius"
```

```bash
# with intel toolchain
# load modules for the dependencies
module load 2019
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
# configure
mkdir build
cd build
CC=icc CXX=icpc cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DARCH="cartesius"
```

2 - Build the example
```bash
# build
make -j4
cd ..
```
WARNING: this reconfigures/rebuilds/relinks the HemoCell library `libhemocell.a`.

To avoid that, manual compilation and linking of the example is possible. However then the predifined architecture-specific optimization flags cannot be used, and one has to set all flags manually.
```bash
# with foss toolchain
mkdir build
cd build
#compilation
mpicxx  -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX  -I$HEMOCELLROOT/palabos/externalLibraries  -I$HEMOCELLROOT/palabos/libraryInterfaces -I$HEMOCELLROOT -I$HEMOCELLROOT/helper -I$HEMOCELLROOT/config -I$HEMOCELLROOT/core -I$HEMOCELLROOT/models -I$HEMOCELLROOT/mechanics -I$HEMOCELLROOT/external  -I$HEMOCELLROOT/IO -I$HEMOCELLROOT/palabos/src  -march=native -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -std=c++11 -O3 -DNDEBUG  -o oneCellShear.o -c ../oneCellShear.cpp
#linking
mpicxx -march=native -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -Wno-empty-body -Wno-unused-result -Wno-ignored-qualifiers -std=c++11 -O3 -DNDEBUG  -rdynamic oneCellShear.o -o ../oneCellShear $HEMOCELLROOT/build/hemocell/libhemocell.a -lhdf5 -lsz -lz -ldl -lm -lpthread -lhdf5 -lhdf5_hl -lsz -lz -ldl -lm -lpthread -lhdf5_hl
cd ..
```

```bash
# with intel toolchain
#compilation
mpiicpc  -DPLB_MPI_PARALLEL -DPLB_SMP_PARALLEL -DPLB_USE_POSIX  -I$HEMOCELLROOT/palabos/externalLibraries  -I$HEMOCELLROOT/palabos/libraryInterfaces -I$HEMOCELLROOT -I$HEMOCELLROOT/helper -I$HEMOCELLROOT/config -I$HEMOCELLROOT/core -I$HEMOCELLROOT/models -I$HEMOCELLROOT/mechanics -I$HEMOCELLROOT/external  -I$HEMOCELLROOT/IO -I$HEMOCELLROOT/palabos/src  -xHost -axAVX,AVX2,CORE-AVX2,AVX512 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -std=c++11 -O3 -wd858 -O3 -DNDEBUG -o oneCellShear.o -c ../oneCellShear.cpp
#linking
mpicpc -xHost -axAVX,AVX2,CORE-AVX2,AVX512 -Wformat -Wformat-security -Wno-deprecated-declarations -Wno-unknown-pragmas -Wno-unused-parameter -Wall -Wextra -Werror=format-security -std=c++11 -O3 -wd858 -O3 -DNDEBUG  -rdynamic oneCellShear.o -o ../oneCellShear $HEMOCELLROOT/build/hemocell/libhemocell.a -lhdf5 -lsz -lz -ldl -lm -lpthread -lhdf5 -lhdf5_hl -lsz -lz -ldl -lm -lpthread -lhdf5_hl
cd ..
```

5 - Compile all examples with default options (not recommended)
```bash
cd $HEMOCELLROOT/examples
make executables
```
WARNING: this reconfigures/rebuilds/relinks `libhemocell.a` and uses default flags.

6 - Run example oneCellShear on Cartesius in a batch job

Here is an example of a submission script for Cartesius. 
Use the same toolchain (foss (GNU) or Intel) that was used to build the library and the example.
```bash
#!/bin/bash
#SBATCH -J hemocell_oneCellShear
#SBATCH -n 24
#SBATCH -p normal
#SBATCH -o hemocell_oneCellShear_%j.out
#SBATCH -t 00:10:00

export HEMOCELLROOT=$HOME/CompBioMed/hemocell/hemocell-2.0/

# load modules for foss toolchain
module purge
module load 2019
module load foss/2018b
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15

# # load modules for intel toolchain
# module purge
# module load 2019
# module load intel/2018b
# module load HDF5/1.10.2-intel-2018b
# module load ParMETIS/4.0.3-intel-2018b
# module load h5py/2.8.0-intel-2018b-Python-2.7.15

# copy input files to scratch-shared
export TEMPDIR=`mktemp -d -p /scratch-shared`
cd $HEMOCELLROOT/examples/oneCellShear
cp *.xml *.pos *.gpl $TEMPDIR
cd $TEMPDIR

# run simulation
srun -n 4 $HEMOCELLROOT/examples/oneCellShear/oneCellShear config.xml

# copy results back to the home directory
cp -r tmp/  $HEMOCELLROOT/examples/oneCellShear/tmp_$SLURM_JOB_ID
```

The output of a case is usually written to the <case>/tmp folder. The checkpoints are the .xml and .dat files. When a new checkpoint is created they are moved to .xml.old and ``.dat.old. The hdf5 output is stored per timestep in tmp/hdf5 and the csv output in tmp/csv.


### Post processing

When the jobs is completed, use 'batchPostProcess.sh' to create XDMF files ('.xmf. extension - http://www.xdmf.org/index.php/Main_Page). XDMF is an XML language that allows one to describe complex objects from a set of datasets (e.g. in HDF5 format), so that the results can be visualized with Paraview (or another visualization tool).
The 'batchPostProcess.sh' script should be run within the $HEMOCELLROOT/examples/<case> or $HEMOCELLROOT/examples/<case>/tmp directory.
```bash
$HEMOCELLROOT/scripts/batchPostProcess.sh
```
The XDMF files are written in the tmp directory.
When we have multiple 'tmp' directories, it creates the XDMF files in all tmp_* directories.



The script 'hemocell/scripts/CellInfoMergeCSV.sh' merges the CSV output from multiple processors into a single one in the current directory. Use it in the tmp directory like this:

```bash
cd hemocell/examples/<case>/tmp/
. ./scripts/CellInfoMergeCSV.sh
```

see https://www.hemocell.eu/user_guide/QuickStart.html#setting-up-hemocell-from-source and https://www.hemocell.eu/user_guide/Scripts.html#hemocell-scripts-batchpostprocess-sh.

### Visualization

Visualization is usually not done directly on the supercomputer but rather on local machines when possible.
To copy files back to a local machine, one can use for example 'scp' on a linux machine.
From the local machine, open a terminal and run the following command (replacing '<login>' with your actual login on Cartesius, and '/path/to/hemocell/rootdir' with the path to your hemocell root directory on Cartesius).
```bash
scp -r <login>@cartesius.surfsara.nl:/home/login/path/to/hemocell/rootdir/examples/oneCellShear/tmp .
```
This copies the output directory 'tmp' and all its content to the local machine.
Now to visualize the results, one can use Paraview.
```bash
cd tmp
paraview &
```
Then open the XDMF files, and visualize the results of the simulation with Paraview.


---
### Computation intensive example and benchmarking on Cartesius
---

1 - Download computation intensive example 'hematocrit_33'.
```bash
cd $HEMOCELLROOT/examples
# download from surfdrive
wget https://surfdrive.surf.nl/files/index.php/s/PvOpsuCv6ROpsYg/download
mv download performance_testing.tgz
tar xvzf performance_testing.tgz
cd performance_testing
```

2 - Configure build options
```bash
# modify 'CMakeLists.txt'
cp ../CMakeLists_template.txt CMakeLists.txt
sed -i -e 's/FOLDER_NAME__/performance_testing/g' CMakeLists.txt
```
Use the same toolchain (foss (GNU) or Intel) that was used to build the library.
```bash
# with foss toolchain
# load modules for the dependencies
module load 2019
module load foss/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-foss-2018b
module load ParMETIS/4.0.3-foss-2018b
module load h5py/2.8.0-foss-2018b-Python-2.7.15
# configure
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DARCH="cartesius"
```

```bash
# with intel toolchain
# load modules for the dependencies
module load 2019
module load intel/2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15
# configure
mkdir build
cd build
CC=icc CXX=icpc cmake .. -DCMAKE_BUILD_TYPE=Release -DENABLE_MPI=1 -DARCH="cartesius"
```

3 - build
```bash
make -j4
cd ..
```

4 - Run computation intensive example hematocrit on Cartesius in a batch job
Here is an example of a submission script for Cartesius. 
Use the same toolchain (foss (GNU) or Intel) that was used to build the library and the example.
```bash
#!/bin/bash
#SBATCH -J hemocell_hematocrit_33_64
#SBATCH -n 64
#SBATCH -p normal
#SBATCH -o hemocell_hematocrit_33_64_%j.out
#SBATCH -t 00:30:00

export HEMOCELLROOT=$HOME/hemocell/hemocell-2.0/

# load modules for foss toolchain
module load surfsara
module load intel/2018b
module load HDF5/1.10.2-intel-2018b
module load ParMETIS/4.0.3-intel-2018b
module load h5py/2.8.0-intel-2018b-Python-2.7.15

# # load modules for intel toolchain
# module purge
# module load 2019
# module load intel/2018b
# module load HDF5/1.10.2-intel-2018b
# module load ParMETIS/4.0.3-intel-2018b
# module load h5py/2.8.0-intel-2018b-Python-2.7.15

# copy input files to scratch-shared
export TEMPDIR=`mktemp -d -p /scratch-shared`
cd $HEMOCELLROOT/examples/performance_testing
cp configs/config_64.xml hematocrit_33/RBC_HO.pos hematocrit_33/RBC_HO.xml $TEMPDIR
cd $TEMPDIR

srun -n 64 $HEMOCELLROOT/examples/performance_testing/performance_testing config_64.xml

cp -r tmp_64/  $HEMOCELLROOT/examples/performance_testing/hematocrit_33/outputs/tmp_$SLURM_JOB_ID
```

```bash
# create output directory
mkdir hematocrit_33/outputs
# submit job
sbatch hematocrit_33/hematocrit_33_64_cartesius.sh
```

---
### Guidelines for efficient parallel programs using HemoCell
---

We observed discrepancies in the performances of HemoCell when compiled with the foss (GNU) toolchain and with the Intel toolchain.
We observed significantly better performances when using the foss toolchain (more than 20% of the total runtime on example 'hematocrit_33'), on both Cartesius and Archer.
So we recommend to use the foss toolchain if possible to get the best performances with HemoCell.

You can check that your installation of HemoCell has the expected performances by running the 'hematocrit_33' example on 3 nodes / 64 cores on Cartesius or Archer and comparing with the following running time:
```
| system    | number of nodes / cores | toolchain | running time (s.) |
|-----------|-------------------------|-----------|-------------------|
| cartesius | 16/1                    | foss      | 1276.1            |
| cartesius | 64/3                    | foss      | 439.3             |
| cartesius | 16/1                    | intel     | 1731.6            |
| cartesius | 64/3                    | intel     | 521.2             |
| archer    | 16/1                    | foss      | 1457.2            |
| archer    | 64/3                    | foss      | 471.9             |
| archer    | 16/1                    | intel     | 1828.8            |
| archer    | 64/3                    | intel     | 565.8             |
```

---

[Back](../..)
