# CompBioMed Tutorial

### Installation steps

1 - Enter the Executables directory:

```bash
cd Alya/Execuables/unix
```

2 - Choose a configuration file:

Intel Fortran compiler
```bash 
cp configure.in/config_ifort.in config.in 
```

GNU Fortran compiler
```bash 
cp configure.in/config_gfortran.in config.in 
```

3 - Configure Alya modules for compilation:

```bash
./configure nastin solidz parall alefor exmedi
```

4 - Build Metis library:

```bash
make metis4
```

5 - Build Alya:
 
```bash
make
```

Detailed installation instructions, as well as more information on the configuration files distributed with the code, are available at:
[http://bsccase02.bsc.es/alya/configure.html](http://bsccase02.bsc.es/alya/configure.html)

### Input Preparation
Alya input consists of multiple text files containing description of the physics of the problem and information on the modules which will be used to solve it. Filename (without extension) is common to each file and usually identify the problem.

#### Use case: Elbow (CFD problem)
[**Link**](https://gitlab.bsc.es/alya/alya/wikis/Examples/Elbow)

### Runtime Information

Alya executable is:

    Alya.x
  
The path to the directory where Alya installation is located must be added, or include the complete path to Alya.x in the terminal configuration file. 

Serial execution is performed by invoking the Alya.x executable: 

```
Alya.x elbow
```

Alya starts reading elbow case input files and runs the simulation. The terminal echoes the tracking of the calculations, listing timstep progression. 

Parallel execution is performed through mpi line command:

```
mpirun -np N Alya.x elbow
```

where N is the number of mpi tasks you want to use. 

Note: in all runs there is one master CPU, devoted to conduct the simulation, so that N-1 partitions will be done on the mesh.

---

[Back](../..)
