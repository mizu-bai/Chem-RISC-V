# VENUS96 on Allwinner D1

## Introduction

> **VENUS** is a general chemical dynamics computer program for performing classical trajectory simulations and a legacy of Prof. William L. Hase. The program uses analytic functions to represent the potential energy surface (PES) for the chemical system. The analytic functions include reactive potentials which allow bond rupture and formation and non-reactive molecular mechanical (MM) potentials. The program includes a variety of options for choosing initial conditions (such as rotation, vibration, and translation energies) for an ensemble of trajectories to represent both unimolecular and bimolecular reactions (_Adv. Chem. Phys._, **1999**, _105_, 171–201).

## Platform

- SBC: Mango Pi MQ Pro with Allwinner D1 and 1GB RAM
- OS: Ubuntu 22.04.2 LTS riscv64
- Compilers: gcc version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1)

## Prerequests

- GNU Compiler gfortran

## Installation

First, request VENUS code follow its site https://www.depts.ttu.edu/chemistry/Venus/index.php, upload the VENUS96 package and decompress the archive file. The Fortran 77 source code `venus96.f` is the only source file for VENUS96 and a collection of input datafiles are in a subdirectory called `datafiles`.

According to the manual

> It is safest to compile VENUS96 with the static memory allocation option. On the IBM RISC/6000 and SGI platforms it is "-qsave" and "static".

but for gfortran, `-qsave` should be replaced with `-fno-automatic`.

When simply running the following command

```sh
$ gfortran -std=legacy -fno-automatic -Ofast venus96.f -o venus96
```

you will get such infomation and fail to get VENUS96 compiled.

```
/usr/bin/ld: /tmp/ccv3rIvA.o: in function `gdate_':
venus96.f:(.text+0x51c0a): undefined reference to `date_'
/usr/bin/ld: venus96.f:(.text+0x51c19): undefined reference to `clock_'
collect2: error: ld returned 1 exit status
```

Open the file `venus96.f`, and search by the keywords `gdate`, the first matched result should be like this.

```fortran
359  C
360  C         THE FOLLOWING LINES ARE FOR WRITING THE DATE & TIME
361  C         IN THE OUTPUT, RIGHT AFTER THE TITLE. THIS, OF COURSE,
362  C         IS MACHINE AND OPERATING SYSTEM DEPENDENT.
363  C
364  C         SUBROUTINE FDATE IS DESIGNED FOR UNIX-SUPPORTED SYSTEMS.
365  C
366        CALL FDATE(ADATE)
367  C
368  C         SUBROUTINE GDATE IS DESIGNED FOR UNICOS-SUPPORTED SYSTEMS
369  C         (CRAY COMPUTER SYSTEMS WITH CFT77 COMPILER).
370  C
371  C     CALL GDATE(ADATE)
372  C
373        WRITE(6,802)ADATE
```

It is clear that the subroutine `FDATA` is for UNIX-Supported systems while subroutine `GDATA` is for UNICOS-Supported systems. Next, continue to find out if subroutine `GDATA` is invoked in other place and its definition. The definition of `GDATA` is at the end of this file, and just comment out it with a lot of "C" characters.

```fortran
13486  C
13487  C         GET CURRENT TIME AND DATE
13488  C         (CRAY-UNICOS SYSTEM WITH CFT77 COMPILER).
13489  C
13490  C      SUBROUTINE GDATE(ADATE)
13491  C      CHARACTER*30 ADATE
13492  C      CALL DATE(ADATE(1:11))
13493  C      CALL CLOCK(ADATE(15:26))
13494  C      RETURN
13495  C      END
```

By the way, I have tried `ifort` and `ifx` from Intel oneAPI and `pgfortran` (which is an alias of `nvfortran`) from NVIDIA HPC SDK, only `ifort` and `ifx` support subroutine `DATE` and `CLOCK`. After commenting out subroutine `GDATE`, the file `venus96.f` can be compiled successfully only **without** `-static` option.

Even VENUS96 is recommanded to be statically linked, but if compling with this command below,

```sh
$ gfortran -std=legacy -fno-automatic -static -Ofast venus96.f -o venus96
```

`ld` will remind you

```
/usr/bin/ld: /usr/lib/gcc/riscv64-linux-gnu/11/libgfortran.a(fpu.o): in function `.L0 ':
(.text._gfortrani_set_fpu_trap_exceptions+0x82): warning: fedisableexcept is not implemented and will always fail
/usr/bin/ld: (.text._gfortrani_set_fpu_trap_exceptions+0x70): warning: feenableexcept is not implemented and will always fail
/usr/bin/ld: (.text._gfortrani_get_fpu_trap_exceptions+0x4): warning: fegetexcept is not implemented and will always fail
```

I came up with this issue in 2023.05.27, the same time when I wrote these words. Okay, they are just **WARNING**s, and the statically linked executable files seem to work fine. I shared this issue with my friends. This issue does not exist on x86-64 platform, and I recommand to compile venus without the `static` option.

## Results

Elapsed time of VENUS96 compiled with different options.

|     | System  |  `-O3`  | `-Ofast` | `-static -O3` | `-static -Ofast` |
|:---:|:-------:|:-------:|:--------:|:-------------:|:----------------:|
|  1  |  al13   | 0:00.96 | 0:00.98  |    0:01.06    |     0:00.95      |
|  2  |   al3   | 0:08.44 | 0:06.44  |    0:06.90    |     0:06.33      |
|  3  | ar13nm  | 0:00.13 | 0:00.12  |    0:00.10    |     0:00.10      |
|  4  | arch4m  | 0:00.08 | 0:00.08  |    0:00.08    |     0:00.07      |
|  5  |  arh2o  | 0:00.09 | 0:00.10  |    0:00.10    |     0:00.08      |
|  6  | benzene | 0:00.95 | 0:00.96  |    0:00.90    |     0:00.81      |
|  7  |  cf3cn  | 0:00.14 | 0:00.14  |    0:00.13    |     0:00.12      |
|  8  |  cf3h   | 0:00.92 | 0:00.96  |    0:00.90    |     0:00.83      |
|  9  | cluster | 0:03.55 | 0:03.58  |    0:03.54    |     0:03.38      |
| 10  | diamond | 0:03.23 | 0:03.26  |    0:03.37    |     0:02.93      |
| 11  |   h2o   | 0:00.12 | 0:00.12  |    0:00.11    |     0:00.10      |
| 12  | h2odim  | 0:00.31 | 0:00.30  |    0:00.33    |     0:00.28      |
| 13  | h2oh2o  | 0:00.11 | 0:00.11  |    0:00.10    |     0:00.09      |
| 14  | h2oleps | 0:00.09 | 0:00.09  |    0:00.08    |     0:00.09      |
| 15  |  h2onm  | 0:00.03 | 0:00.04  |    0:00.02    |     0:00.02      |
| 16  |  h2orp  | 0:00.87 | 0:00.87  |    0:01.13    |     0:00.87      |
| 17  |  hch3   | 0:00.27 | 0:00.27  |    0:00.25    |     0:00.24      |
| 18  |  heh2   | 0:00.07 | 0:00.07  |    0:00.06    |     0:00.06      |
| 19  |  ho2rp  | 0:00.65 | 0:00.69  |    0:00.69    |     0:00.63      |
| 20  |  lidme  | 0:00.47 | 0:00.47  |    0:00.50    |     0:00.42      |
| 21  |  lmch4  | 0:12.45 | 0:12.36  |    0:11.23    |     0:10.83      |
| 22  |  mimo   | 0:00.35 | 0:00.35  |    0:00.35    |     0:00.31      |
| 23  | nacrown | 0:04.00 | 0:03.87  |    0:04.27    |     0:03.72      |
| 24  |  nmch4  | 0:00.05 | 0:00.04  |    0:00.03    |     0:00.03      |
| 25  |  nono   | 0:00.09 | 0:00.08  |    0:00.08    |     0:00.08      |
| 26  | propyn  | 0:00.06 | 0:00.06  |    0:00.05    |     0:00.05      |
| 27  | sn2bar  | 0:00.96 | 0:00.94  |    0:01.11    |     0:00.91      |
| 28  |  sn2nm  | 0:00.05 | 0:00.05  |    0:00.04    |     0:00.04      |
| 29  |  sn2rp  | 0:00.90 | 0:00.90  |    0:00.99    |     0:00.82      |
| 30  | sn2sel  | 0:01.02 | 0:00.93  |    0:01.13    |     0:00.95      |
| 31  | sn2trj  | 0:00.96 | 0:00.90  |    0:01.11    |     0:00.93      |
| 32  |  xe2xe  | 0:00.06 | 0:00.06  |    0:00.05    |     0:00.05      |

## Reference

Hase, W. L.; Duchovic, R. J.; Hu, X.; Komornicki, A.; Lim, K. F.; Lu, D.-H.; Peslherbe, G. H.; Swamy, K. N.; Vande Linde, S. R.; Varandas, A., Wang, H.; Wolf, R. J. VENUS96: A general chemical dynamics computer program, _Quantum Chemical Program Exchange (QCPE) Bulletin_, **1996**, _16_ (4), 671. https://www.depts.ttu.edu/chemistry/Venus/index.php
