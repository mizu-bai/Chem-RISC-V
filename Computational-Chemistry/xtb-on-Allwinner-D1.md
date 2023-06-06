# xtb on Allwinner D1

## Introduction

`xtb` is a Semiempirical Extended Tight-Binding Program Package, developed by Grimme's group.

- GitHub: https://github.com/grimme-lab/xtb
- Doc: https://xtb-docs.readthedocs.io/en/latest/contents.html

## Platform

- SBC: Mango Pi MQ Pro with Allwinner D1 and 1 GB RAM
- OS: Ubuntu 22.04.2 LTS riscv64
- Compilers: gcc version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04.1)
- Build System: CMake (3.22.1)
- BLAS: libblas-dev (3.10.0-2ubuntu1)
- LAPACK: liblapack-dev (3.10.0-2ubuntu1)

## Installation

On x86-64 platforms, in order to get better performances, `ifort` is widely used to compile quantum chemistry softwares. While for other platforms like ARM or RISC-V, we have to use `gfortran` in stead. Also, math libraries like linear algebra packages are the the most significant dependencies. However, the OpenBLAS package provided by Ubuntu cannot work well on Allwinner D1 chip, so I have to use Netlib BLAS instead.

Download the source code from GitHub release page, upload it to Mango Pi MQ Pro, and then decompress it.

I renamed the source code directory as `xtb-6.5.1-build`, since I wanted to set the install prefix as `$HOME/xtb-6.5.1`.

```sh
ubuntu@ubuntu-mq-pro:~/xtb-6.5.1-build$ cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/home/ubuntu/xtb-6.5.1
ubuntu@ubuntu-mq-pro:~/xtb-6.5.1-build$ make -C build
```

Building `xtb` on D1 took very long time, have a rest!

And it was a miracle that the `xtb` compiled with Netlib BLAS passed all the test.

```sh
ubuntu@ubuntu-mq-pro:~/xtb-6.5.1-build$ make -C build test
make: Entering directory '/home/ubuntu/xtb-6.5.1-build/build'
Running tests...
Test project /home/ubuntu/xtb-6.5.1-build/build
      Start  1: mctc-lib/math
 1/56 Test  #1: mctc-lib/math ....................   Passed    0.06 sec
      Start  2: mctc-lib/read
 2/56 Test  #2: mctc-lib/read ....................   Passed    0.08 sec
      Start  3: mctc-lib/read-aims
 3/56 Test  #3: mctc-lib/read-aims ...............   Passed    0.07 sec
      Start  4: mctc-lib/read-ctfile
 4/56 Test  #4: mctc-lib/read-ctfile .............   Passed    0.16 sec
      Start  5: mctc-lib/read-gaussian
 5/56 Test  #5: mctc-lib/read-gaussian ...........   Passed    0.04 sec
      Start  6: mctc-lib/read-genformat
 6/56 Test  #6: mctc-lib/read-genformat ..........   Passed    0.09 sec
      Start  7: mctc-lib/read-pdb
 7/56 Test  #7: mctc-lib/read-pdb ................   Passed    0.09 sec
      Start  8: mctc-lib/read-qchem
 8/56 Test  #8: mctc-lib/read-qchem ..............   Passed    0.05 sec
      Start  9: mctc-lib/read-qcschema
 9/56 Test  #9: mctc-lib/read-qcschema ...........   Passed    0.04 sec
      Start 10: mctc-lib/read-turbomole
10/56 Test #10: mctc-lib/read-turbomole ..........   Passed    0.12 sec
      Start 11: mctc-lib/read-vasp
11/56 Test #11: mctc-lib/read-vasp ...............   Passed    0.06 sec
      Start 12: mctc-lib/read-xyz
12/56 Test #12: mctc-lib/read-xyz ................   Passed    0.06 sec
      Start 13: mctc-lib/symbols
13/56 Test #13: mctc-lib/symbols .................   Passed    0.03 sec
      Start 14: mctc-lib/write
14/56 Test #14: mctc-lib/write ...................   Passed    0.12 sec
      Start 15: mctc-lib/write-aims
15/56 Test #15: mctc-lib/write-aims ..............   Passed    0.04 sec
      Start 16: mctc-lib/write-ctfile
16/56 Test #16: mctc-lib/write-ctfile ............   Passed    0.04 sec
      Start 17: mctc-lib/write-gaussian
17/56 Test #17: mctc-lib/write-gaussian ..........   Passed    0.03 sec
      Start 18: mctc-lib/write-genformat
18/56 Test #18: mctc-lib/write-genformat .........   Passed    0.05 sec
      Start 19: mctc-lib/write-pdb
19/56 Test #19: mctc-lib/write-pdb ...............   Passed    0.03 sec
      Start 20: mctc-lib/write-qchem
20/56 Test #20: mctc-lib/write-qchem .............   Passed    0.04 sec
      Start 21: mctc-lib/write-turbomole
21/56 Test #21: mctc-lib/write-turbomole .........   Passed    0.04 sec
      Start 22: mctc-lib/write-vasp
22/56 Test #22: mctc-lib/write-vasp ..............   Passed    0.06 sec
      Start 23: mctc-lib/write-xyz
23/56 Test #23: mctc-lib/write-xyz ...............   Passed    0.03 sec
      Start 24: xtb/CAPI
24/56 Test #24: xtb/CAPI .........................   Passed    1.64 sec
      Start 25: xtb/atomlist
25/56 Test #25: xtb/atomlist .....................   Passed    0.05 sec
      Start 26: xtb/coordinationnumber
26/56 Test #26: xtb/coordinationnumber ...........   Passed    1.04 sec
      Start 27: xtb/coulomb
27/56 Test #27: xtb/coulomb ......................   Passed    8.37 sec
      Start 28: xtb/dftd3
28/56 Test #28: xtb/dftd3 ........................   Passed   15.87 sec
      Start 29: xtb/dftd4
29/56 Test #29: xtb/dftd4 ........................   Passed   16.64 sec
      Start 30: xtb/eeq
30/56 Test #30: xtb/eeq ..........................   Passed    0.76 sec
      Start 31: xtb/gfn0
31/56 Test #31: xtb/gfn0 .........................   Passed    6.10 sec
      Start 32: xtb/gfn1
32/56 Test #32: xtb/gfn1 .........................   Passed   20.90 sec
      Start 33: xtb/gfn2
33/56 Test #33: xtb/gfn2 .........................   Passed   18.61 sec
      Start 34: xtb/gfnff
34/56 Test #34: xtb/gfnff ........................   Passed    4.40 sec
      Start 35: xtb/hessian
35/56 Test #35: xtb/hessian ......................   Passed    0.57 sec
      Start 36: xtb/latticepoint
36/56 Test #36: xtb/latticepoint .................   Passed    0.04 sec
      Start 37: xtb/molecule
37/56 Test #37: xtb/molecule .....................   Passed    0.04 sec
      Start 38: xtb/pbc-tools
38/56 Test #38: xtb/pbc-tools ....................   Passed    0.04 sec
      Start 39: xtb/peeq
39/56 Test #39: xtb/peeq .........................   Passed    1.79 sec
      Start 40: xtb/repulsion
40/56 Test #40: xtb/repulsion ....................   Passed    3.48 sec
      Start 41: xtb/symmetry
41/56 Test #41: xtb/symmetry .....................   Passed    0.93 sec
      Start 42: xtb/thermo
42/56 Test #42: xtb/thermo .......................   Passed    0.07 sec
      Start 43: xtb/wsc
43/56 Test #43: xtb/wsc ..........................   Passed    0.04 sec
      Start 44: xtb/Argparser_print_version
44/56 Test #44: xtb/Argparser_print_version ......   Passed    0.04 sec
      Start 45: xtb/Argparser_print_help
45/56 Test #45: xtb/Argparser_print_help .........   Passed    0.05 sec
      Start 46: xtb/Argparser_print_license
46/56 Test #46: xtb/Argparser_print_license ......   Passed    0.04 sec
      Start 47: xtb/Info
47/56 Test #47: xtb/Info .........................   Passed    0.21 sec
      Start 48: xtb/Singlepoint
48/56 Test #48: xtb/Singlepoint ..................   Passed    0.95 sec
      Start 49: xtb/IP/EA
49/56 Test #49: xtb/IP/EA ........................   Passed    2.05 sec
      Start 50: xtb/GFN0-xTB
50/56 Test #50: xtb/GFN0-xTB .....................   Passed    0.54 sec
      Start 51: xtb/GFN1-xTB
51/56 Test #51: xtb/GFN1-xTB .....................   Passed    1.08 sec
      Start 52: xtb/GFN2-xTB/GBSA
52/56 Test #52: xtb/GFN2-xTB/GBSA ................   Passed    0.92 sec
      Start 53: xtb/GFN2-FF
53/56 Test #53: xtb/GFN2-FF ......................   Passed    0.17 sec
      Start 54: all-tests
54/56 Test #54: all-tests ........................   Passed    0.05 sec
      Start 55: check
55/56 Test #55: check ............................   Passed    0.04 sec
      Start 56: select
56/56 Test #56: select ...........................   Passed    0.03 sec

100% tests passed, 0 tests failed out of 56

Total Test time (real) = 109.67 sec
make: Leaving directory '/home/ubuntu/xtb-6.5.1-build/build'
```

Then run `make install` to finish the installation.

## Example

Geometry optimization and frequencies

`benzene.xyz`

```
12

 C                  0.00000000    1.40140000    0.00000000
 C                  1.21364800    0.70070000    0.00000000
 C                 -1.21364800    0.70070000    0.00000000
 C                  1.21364800   -0.70070000    0.00000000
 H                  2.14029518    1.23570000    0.00000000
 H                  0.00000000    2.47140000    0.00000000
 C                 -1.21364800   -0.70070000    0.00000000
 H                 -2.14029518    1.23570000    0.00000000
 C                  0.00000000   -1.40140000    0.00000000
 H                  2.14029518   -1.23570000    0.00000000
 H                 -2.14029518   -1.23570000    0.00000000
 H                  0.00000000   -2.47140000    0.00000000
```

Running the task

```sh
ubuntu@ubuntu-mq-pro:~/benzene$ nohup xtb benzene.xyz --ohess > benzene.out &
```

The summary section in output file

```
total:
* wall-time:     0 d,  0 h,  0 min,  5.100 sec
*  cpu-time:     0 d,  0 h,  0 min,  5.073 sec
* ratio c/w:     0.995 speedup
SCF:
* wall-time:     0 d,  0 h,  0 min,  0.078 sec
*  cpu-time:     0 d,  0 h,  0 min,  0.078 sec
* ratio c/w:     0.997 speedup
ANC optimizer:
* wall-time:     0 d,  0 h,  0 min,  0.347 sec
*  cpu-time:     0 d,  0 h,  0 min,  0.347 sec
* ratio c/w:     0.999 speedup
Note: The following floating-point exceptions are signalling: IEEE_UNDERFLOW_FLAG
normal termination of xtb
analytical hessian:
* wall-time:     0 d,  0 h,  0 min,  4.525 sec
*  cpu-time:     0 d,  0 h,  0 min,  4.500 sec
* ratio c/w:     0.995 speedup
```

## Reference

(1) C. Bannwarth, E. Caldeweyher, S. Ehlert, A. Hansen, P. Pracht, J. Seibert, S. Spicher, S. Grimme WIREs Comput. _Mol. Sci._, **2020**, 11, e01493. DOI: 10.1002/wcms.1493
