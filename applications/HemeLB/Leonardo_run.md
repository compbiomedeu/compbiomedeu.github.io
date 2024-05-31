---
layout: plain
---

```bash
#!/bin/bash
#SBATCH -p boost_usr_prod
# #SBATCH -q boost_qos_bprod
#SBATCH --qos=qos_special
#SBATCH --time 02:00:00
#SBATCH -N 512
#SBATCH --ntasks-per-node=4
#SBATCH --cpus-per-task=8
#SBATCH --gres=gpu:4
#SBATCH --mem=0
#SBATCH --job-name=CoW6um

module load gmp/6.2.1
module load mpfr/4.1.0
module load mpc/1.2.1
module load gcc/11.3.0
module load zlib/1.2.13--gcc--11.3.0
module load openmpi/4.1.4--gcc--11.3.0-cuda-11.8
module load boost/1.80.0--openmpi--4.1.4--gcc--11.3.0
module load python/3.10.8--gcc--11.3.0
module load cuda/11.8

export OMP_NUM_THREADS=1
export EXE_PATH=$HOME/HemePure-GPU/src/build

#Copy input files
cp CoW6um_input_nm.xml coW-6.4um.gmy $CINECA_SCRATCH
cd $CINECA_SCRATCH

#Run simulation
srun $EXE_PATH/hemepure_gpu -in CoW6um_input_nm.xml -out CoW6um_input_nm-$SLURM_JOBID.out

#Copy back output
cp -r CoW6um_input_nm-$SLURM_JOBID.out $HOME/HemePure_input/COW6
```
