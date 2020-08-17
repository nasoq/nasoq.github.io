
We are trying to build different interface to ease the use of NASOQ in different frameworks/languages. 

## Eigen interface
An Eigen interface is provided in the NASOQ repository in the ```nasoq/eigen_interface``` directory. The interface needs Eigen to be installed beforehand. 
There are two demos, `eigen_nasoq_driver.cpp` and `eigen_lbl_driver.cpp"` that show how NASOQ and LBL can be called from Eigen.
You need to only include `#include "lbl_eigen.h"` for using LBL and `#include "nasoq_eigen.h"` for using NASOQ in your Eigen code.


## Matlab interface
The NASOQ Matlab interface needs Libigl and Eigen as well as other dependencies that NASOQ has (see [getting started with NASOQ](getting-started-nasoq.md)). 
A simple manual for installing the Matlab interface is provided in the `nasoq/matlab` directory. 