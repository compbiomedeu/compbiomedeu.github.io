```bash
#!/bin/bash
#SBATCH --job-name=N640_gpn8
#SBATCH --ntasks=5120
#SBATCH --ntasks-per-node=8
#SBATCH --gpus-per-node=8
#SBATCH --output=N640_gpn8.out
#SBATCH --error=N640_gpn8.err
#SBATCH --time=01:59:00
#SBATCH -p partition

# MPICH specific flag
export MPICH_GPU_SUPPORT_ENABLED=1

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
 
dtg=$( date +"%Y-%m-%d-%H-%M" )
 
srun <path_to_HemePure_code_base>/HemePure-GPU/src/build/hemepure_gpu -in <path_to_input>/CoW6um_input_nm.xml -out results_$dtg
