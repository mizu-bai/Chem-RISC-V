# PIMD 2.5.2 on Allwinner D1

## Introduction

> PIMD is an open-source software for parallel molecular simulations originally developed by Dr. Motoyuki Shiga (Principal Researcher at Japan Atomic Energy Agency). This program, based on MPI Fortran 90, can be downloaded and used for anyone, free of charge, under Apache 2.0 License.

## Platform

- SBC: Mango Pi MQ Pro with Allwinner D1 and 1 GB RAM
- OS: Ubuntu 22.04.2 LTS riscv64
- Compilers: gcc version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1)
- MPI: Open MPI 4.1.2

## Prerequrests

- make
- GNU Compilers
- Open MPI

## Installation

Obtain source code of PIMD from website https://ccse.jaea.go.jp/software/PIMD/index.en.html, upload the archived file to Mango PI MQ Pro, and then decompress it.

```sh
ubuntu@ubuntu-mq-pro:~$ mkdir pimd.2.5.2
ubuntu@ubuntu-mq-pro:~$ tar xvf pimd.2.5.2.tar -C pimd.2.5.2
```

Then enter directory `pimd.2.5.2` and follow the instructions in PIMD's manual to build.

```sh
ubuntu@ubuntu-mq-pro:~$ cd pimd.2.5.2
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ mkdir compile
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ cp source/* compile
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ cd compile
```

Before compiling, some patches should be done. Open `makefile` in `compile` directory, add `-std=legacy` directly after line `FCMP = mpif90`, comment out some options in `MAC OSX` section, and enable option `-Dnolapack`。 Now these lines should be like:

```makefile
9 #-----------------------------------------------------------------------
10 #     Fortran compiler
11 #-----------------------------------------------------------------------
12
13 FCMP = mpif90 -std=legacy
14
15 #-----------------------------------------------------------------------
16 #     C compiler
17 #-----------------------------------------------------------------------
18
19 CC = gcc
20
21 #-----------------------------------------------------------------------
22 #     machine dependent compile options
23 #-----------------------------------------------------------------------
24
25 #     MAC OSX:
26 #OPTS = -g -Ofast -Wall -framework Accelerate
27 #OPTS += -fbounds-check -Wuninitialized -fbacktrace
28 #OPTS += -fallow-argument-mismatch
29
30 #     compile PIMD with lapack
31 # LAPACK = -Dlapack
32
33 #     compile PIMD without lapack
34 LAPACK = -Dnolapack
35
36 #     compile PIMD with PME
37 # PME = -Dpme
38
39 #     compile PIMD without PME
40 PME = -Dnopme
41
42 #-----------------------------------------------------------------------
```

Then you can build PIMD and have some coffee :)

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/compile$ make veryclean
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/compile$ make
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/compile$ cd ..
```

> If successful, the serial executable `pimd.x` and parallel executable `pimd.mpi.x` are created. If not, edit makeﬁle appropriately, and try it again.

> Finally, copy all the executable ﬁles which is named *.x (including pimd.x and pimd.mpi.x) to the directory (/xxx) where the PATH is accessible.

Here I create a directory named `bin`, copy executable files to here, and append `export PATH=$HOME/pimd.2.5.2/bin:$PATH` to `~/.bashrc`.

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ mkdir bin
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ cp compile/*.x bin
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ ls bin
calc.x            convert_tinker.x  pimd.mpi.x  polymers.x   prep_liquid.x
convert_charmm.x  gau.x             pimd.x      prep_best.x  run_molpro.x
```

Type `pimd.x` and here is the output. Now PIMD is successfully compiled.

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ which pimd.x
/home/ubuntu/pimd.2.5.2/bin/pimd.x
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ pimd.x

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

PIMD version 2.5.1

Author:        M. Shiga

Last Updated:  Sep 5, 2022.

Copyright(C) 2016-2022  M. Shiga  All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Error - file missing: input.dat.

Error termination at: subroutine setparams.

```

## Examples

> To check that everything is correct, it is recommended to start from running simple calculations.

We should create a directory for running examples at first.

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ mkdir run
```


1. Static

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ cp -r examples/H2O/water_static run
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2$ cd run/water_static
```

Since we have add `pimd.x` to `PATH`, to start the calculation, type `pimd.x`, and you will get such results.

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/run/water_static$ ls
input.dat  input_default.dat  structure.dat
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/run/water_static$ pimd.x

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

PIMD version 2.5.1

Author:        M. Shiga

Last Updated:  Sep 5, 2022.

Copyright(C) 2016-2022  M. Shiga  All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Method:   static.

Model:    water.

Ensemble: none.

System:     3 atoms,    1 beads.

------------------------------------------------------------------------------
     type  symbol  number           mass            atoms
------------------------------------------------------------------------------
        1  O            8       15.99491                1
        2  H            1        1.00783                2
------------------------------------------------------------------------------

Atomic positions read from structure.dat. Molecular configuration initialized.

Free boundary condition - isolated system.

==============================================================================
                           potential energy values
  bead                 hartree                kcal/mol                  kJ/mol
------------------------------------------------------------------------------
     1              0.00036497                0.229023                0.958230
------------------------------------------------------------------------------

Normal termination of pimd.

```

Of course, if type `mpirun -np 1 pimd.mpi.x`, the parallel version also works.

```sh
ubuntu@ubuntu-mq-pro:~/pimd.2.5.2/run/water_static$ mpirun -np 1 pimd.mpi.x

_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/

Information of parallel computation:

  Number of processors  =      1
  Number of beads       =      1
  Bead parallelization  =      1
  Force parallelization =      1
  Force cycles per step =      1

_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/_/

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

PIMD version 2.5.1

Author:        M. Shiga

Last Updated:  Sep 5, 2022.

Copyright(C) 2016-2022  M. Shiga  All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Method:   static.

Model:    water.

Ensemble: none.

System:     3 atoms,    1 beads.

------------------------------------------------------------------------------
     type  symbol  number           mass            atoms
------------------------------------------------------------------------------
        1  O            8       15.99491                1
        2  H            1        1.00783                2
------------------------------------------------------------------------------

Molecular configuration restarted, read from geometry.ini.

Free boundary condition - isolated system.

==============================================================================
                           potential energy values
  bead                 hartree                kcal/mol                  kJ/mol
------------------------------------------------------------------------------
     1              0.00036497                0.229023                0.958230
------------------------------------------------------------------------------

Normal termination of pimd.

```

## Reference

(1) Shiga, M.; Tachikawa, M.; Miura, S. Ab Initio Molecular Orbital Calculation Considering the Quantum Mechanical Effect of Nuclei by Path Integral Molecular Dynamics. _Chem. Phys. Lett._ **2000**, _332_ (3–4), 396–402. https://doi.org/10.1016/s0009-2614(00)01269-0.

(2) Shiga, M.; Tachikawa, M.; Miura, S. A Unified Scheme Forab Initiomolecular Orbital Theory and Path Integral Molecular Dynamics. _J. Chem. Phys._ **2001**, _115_ (20), 9149–9159. https://doi.org/10.1063/1.1407289.
