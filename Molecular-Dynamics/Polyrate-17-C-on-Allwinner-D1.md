# Polyrate 17-C on Allwinner D1

## Introduction

> _Polyrate 17-C_ is a suite of computer programs for the calculation of chemical reaction rates of polyatomic species (including atoms and diatoms as special cases) by variational transition state theory (VTST); conventional transition state theory is also supported. _Polyrate 17-C_ (called _Polyrate_ for short) consists of the main program, called Polyrate, and a set of utility codes.

> _Polyrate_ - version 17-C is licensed under the Apache License, Version 2.0.

> The manual of _Polyrate_ - version 17-C is licensed under CC-BY-4.0.

## Platform

- SBC: Mango Pi MQ Pro with Allwinner D1 and 1 GB RAM
- OS: Ubuntu 22.04.2 LTS riscv64
- Compilers: gcc version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1)

## Prerequests

- csh
- make
- GNU Compilers, including gcc, g++, and gfortran

If not installed, install them via apt.

```sh
$ ubuntu@ubuntu:~$ sudo apt install csh make gcc g++ gfortran -y
```

## Installation

First, download _Polyrate 17-C_ from its release site: https://comp.chem.umn.edu/sds/polyrate/polyrate.cgi and upload the file `polyrate17C_20190728_withSSQRRK_20190110.tar.gz` to Mango Pi MQ Pro.

Since _Polyrate_ will create 2 files at one's home directory named `.poly_path` and `.poly_ver` during the installation, it is recommanded to remove these files.

```sh
ubuntu@ubuntu:~$ rm .poly_path .poly_ver
```

Then decompress _Polyrate_ package. Once finished, a directory named `polyrate17` can be found.

```sh
ubuntu@ubuntu:~$ tar zxvf polyrate17C_20190728_withSSQRRK_20190110.tar.gz
```

Enter `polyrate17`, and run the script `configure`. When asked to choose between RP-VTST or VRC-VTST, type `RP` and return.

```sh
ubuntu@ubuntu:~/polyrate17$ ./configure

Welcome to POLYRATE!


                            Notice to users
 ************************************************************************
 Starting from version 2008, POLYRATE has two kinds of executables:

 1. RP-VTST for reactions with potential energy barriers (serial code)
 2. VRC-VTST for barrierless bimolecular association reactions (MPI code)

 RP-VTST and VRC-VTST need different executables and they should be
 compiled separately by running this script
 ************************************************************************

 Cleaning the src/ directory ...

... processor is riscv64
... OS is Linux

Is this running used to generate Makefile for RP-VTST or VRC-VTST?
 [ Choose 'RP' or 'VRC' ]: RP
....did not find Intel Fortran compiler - ifort
....found GNU Fortran compiler - gfortran :/usr/bin/gfortran
....found MPI compiler :/usr/bin/mpif77
....did not find Intel C compiler
....found GNU C compiler :/usr/bin/gcc
....found MPI C compiler :/usr/bin/mpicc

 Do the options chosen above look OK? [ yes ]:
... installing
.... creating Makefile

---- POLYRATE INSTALLATION COMPLETE ----

Have a nice day! :)

.... done with Makefile
```

Even _Polyrate_ wishes us a nice day, the `Makefile` has to be modified manually cause the codes are too ancient for gfortran-11 to compile. Open the `Makefile` in directory `src`, the first line shall be

```
F77C    = /usr/bin/gfortran -O -fno-automatic
```

The only thing you should do is to add `-std=legacy` after `/usr/bin/gfortran`, and this line should looks like

```
F77C    = /usr/bin/gfortran -std=legacy -O -fno-automatic
```

Now the installation is done , and _Have a nice day! :)_.

## Tests

_Polyrate_ offers some tests under `polyrate17/testrun`, in order to run all tests for RP-VTST version, simply type `./run_allPRVTST.jc`. All scripts in _Polyrate_ are written in `csh`. For the tests jobs shares mounds of codes that provides the molecular dynamics algorithms, the mainly differences between each systems are the potential energy surfaces and input files. Once a job runs under a fresh-installed _Polyrate_, which may be very slow on SBCs for compiling the shared codes, it will never be such painful to run other jobs.

## Results

|     | System | Elapsed Time |
|:---:|:------:|:------------:|
|  1  |   h3   |   0:07.70    |
|  2  |  cwmc  |   0:51.16    |
|  3  | ch4oh  |   0:02.99    |
|  4  |  ch4o  |   0:21.46    |
|  5  |  ch5   |   1:41.01    |
|  6  | ch4cl  |   0:01.10    |
|  7  |  oh3   |   0:42.46    |
|  8  |  ho2   |   1:07.09    |
|  9  | clhbr  |   0:31.21    |
| 10  |  cmc   |   0:19.73    |
| 11  |  nh3   |   0:02.93    |
| 12  |  hni   |   0:35.17    |
| 13  |  h2ni  |   0:15.29    |
| 14  |  ohcl  |   0:54.14    |
| 15  |  h3o2  |   0:01.87    |

## Reference

(1) Isaacson, A. D.; Truhlar, D. G.; Rai, S. N.; Steckler, R.; Hancock, G. C.; Garrett, B. C.; Redmon, M. J. POLYRATE: A General Computer Program for Variational Transition State Theory and Semiclassical Tunneling Calculations of Chemical Reaction Rates. _Comput. Phys. Commun._ **1987**, _47_ (1), 91–102. https://doi.org/10.1016/0010-4655(87)90069-5.
