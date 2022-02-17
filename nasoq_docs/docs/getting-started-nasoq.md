
 Here we explain how you can install NASOQ and use its C++ API to solve your QP problems.

##Installation

### Getting the source
First you should clone the NASOQ repository in your project folder:
```bash
git clone https://github.com/sympiler/nasoq
```
You can also download one of the NASOQ releases from [here](https://github.com/sympiler/nasoq/releases). 


### Required dependencies 
You should have the following packages to be able to install NASOQ:

* **CMake:** version 3.16 or higher
* **C/C++ compiler:** Any GCC, ICC, or Clang that support C++11 should work. You can always change your compiler by setting `CMAKE_C_COMPILER` and `CMAKE_CXX_COMPILER`. For example, the following changes the compiler to GCC-9: 
```bash
cmake -DCMAKE_C_COMPILER=/usr/bin/gcc-9 -DCMAKE_CXX_COMPILER=/usr/bin/g++-9 ..
```

### Optional dependencies 
Following packages improve the performance of NASOQ, but it is not necessary because the NASOQ build system handles it internally. You can skip these details if you are satisfied with the NASOQ performance as installed. We explain different alternatives that the NASOQ build system accepts. 

* **OpenMP:** OpenMP enables thread-level parallelism in NASOQ. Without OpenMP, NASOQ runs sequentially. We are planning to add TBB as an alternative. If your compiler supports OpenMP, CMake detects it. GCC, ICC, and most LLVM versions natively support OpenMP.

* **METIS:** METIS is required for solving linear systems in NASOQ. CMake downloads  METIS and installs it if it does not find in your system. You can always add your favorite local METIS installation to `CMAKE_PREFIX_PATH=""` and NASOQ will use that instead. Alternatively, you can set `METIS_INCDIR` and `METIS_LIBDIR` to where METIS header and library are installed, respectively.   

* **BLAS Library:** NASOQ needs a BLAS library for its supernodal sparse linear solver. It supports both MKL BLAS and OpenBLAS. You should set `NASOQ_BLAS_BACKEND` when you are installing NASOQ through CMake. The default BLAS is set to MKL as NASOQ is tested more thoroughly with MKL BLAS, but OpenBLAS works as well. 

	* MKL BLAS (`-DNASOQ_BLAS_BACKEND="MKL"`): CMake detects the MKL library if you installed it on its default location. If you are using a new version, i.e., called oneAPI, you should set the include and binary directories to `CMAKE_PREFIX_PATH`. An example of using oneAPI 2021: 
	```	
	cmake -DCMAKE_PREFIX_PATH="/opt/intel/oneapi/mkl/2021.2.0/lib/intel64/;/opt/intel/oneapi/mkl/2021.2.0/include/" ..``` 

	* OpenBLAS (`-DNASOQ_BLAS_BACKEND="OpenBLAS"`): NASOQ's build system downloads OpenBLAS and installs it if it does not detect it in your system. NASOQ should detect OpenBLAS if it is installed through `apt` or `brew`.   



* **LAPACK Library:** The linear solver in NASOQ also uses LAPACK. LAPACK is often part of the BLAS library, such as MKL and OpenBLAS in Mac and Linux. However, if it is not included, or you have any installation issues due to LAPACK, you can use the LAPACK version that NASOQ downloads internally by setting `-DNASOQ_USE_CLAPACK=ON`. The installed BLAS is not fast but, it does not need a Fortran compiler which could be an issue in some operating systems such as Windows. 

### Build
If all required dependencies are installed, you can build NASOQ. If you want NASOQ with better performance, reading settings for optional dependencies is helpful. The build instruction below provides a good compromise between easy installation and acceptable performance. 

* Unix (Linux/Mac)
```bash
cd where/nasoq/cloned
cmake -DNASOQ_BLAS_BACKEND=OpenBLAS -DCMAKE_BUILD_TYPE=Release  -S . -B build
cmake --build build -j 6 
```

* Windows
```bash
cd where/nasoq/cloned
cmake -DNASOQ_BLAS_BACKEND=OpenBLAS -DNASOQ_USE_CLAPACK=ON -DCMAKE_BUILD_TYPE=Release  -S . -B build
cmake --build build --config Release -j 6 
```


A header-only version NASOQ is accessible from [here](https://github.com/sympiler/nasoq/archive/refs/tags/v0.1.0.zip).

Github workflows in the NASOQ repository also provides alternative and working build instructions for [Mac](https://github.com/sympiler/nasoq/blob/3565bba5c984e24a1e22f3555e2ba09e31c4486f/.github/workflows/cmakeMac.yml#L34) and [Linux](https://github.com/sympiler/nasoq/blob/3565bba5c984e24a1e22f3555e2ba09e31c4486f/.github/workflows/cmakeUbuntu.yml#L32).

## C++ API examples
We provide a set of examples to explain NASOQ API. You can use the NASOQ code in-tree or out-of-tree. We explain both possibilities using examples provided in the NASOQ repository.   

* Using NASOQ in-tree: 
NASOQ examples in `nasoq/examples` are inside the NASOQ source code and are built with NASOQ. If you want to work in-tree, you can directly modify examples here and rebuild NASOQ.   

* Using NASOQ out-of-tree: You can download the NASOQ code, e.g. as a submodule, and install it while building your project. We provide an [example](https://github.com/cheshmi/nasoq-example) of how to build the NASOQ examples out-of-tree. You can set the NASOQ settings you like before installing NASOQ right from your CMake file. For example, to set the NASOQ BLAS vendor, you can use: `set(NASOQ_BLAS_BACKEND "OpenBLAS" CACHE STRING "BLAS implementation for NASOQ to use")`.

Here we show a snapshot of the NASOQ QP Solver example (`nasoq/examples/nasoq_main.cpp`):   

```C++
#include <nasoq/nasoq.h>

int main(){
 /// Allocate
 ...
 /// Inputs
 q[0] = -4; q[1] = -4;
 // H should be the lower part of a symmetric matrix
 Hp[0]=0;Hp[1]=1;Hp[2]=2;
 Hi[0]=0;Hi[1]=1;
 Hx[0]=2;Hx[1]=2;
 // A and C matrices are general matrices
 Cp[0]=0;Cp[1]=4;Cp[2]=8;
 Ci[0]=0;Ci[1]=1;Ci[2]=2;Ci[3]=3;
 Ci[4]=0;Ci[5]=1;Ci[6]=2;Ci[7]=3;
 Cx[0]=2;Cx[1]=1;Cx[2]=-1;Cx[3]=-2;
 Cx[4]=1;Cx[5]=-1;Cx[6]=-1;Cx[7]=1;

 d[0]=2;d[1]=1;d[2]=1;d[3]=2;

 /// Solving the QP pronlem
 nasoq::Nasoq *qm;
 qm = new nasoq::Nasoq(sizeH,Hp,Hi,Hx,
                       q,CRows,CCols,Cp, Ci, Cx, d);
 qm->diag_perturb=pow(10,-9);
 qm->eps_abs=pow(10,-3);
 qm->max_iter = 0;
 qm->variant = nasoq::PREDET;
 int converged = qm->solve();

 /// Printing results
 if(converged)
  std::cout<<"The problem is converged\n";

 // expected x={0.4,1.2};
 auto *x = qm->primal_vars;
 std::cout<<"Primal variables: ";
 for (int i = 0; i < sizeH; ++i) {
  std::cout<<x[i]<<",";
 }

 // expected z = {1.6,0,0,0}
 std::cout<<"\nDual variables: ";
 auto *z = qm->dual_vars;
 for (int i = 0; i < CRows; ++i) {
  std::cout<<z[i]<<",";
 }


 /// Free memory
 ...
}
```
For details about inputs, outputs, and settings of NASOQ, please see the [NASOQ document page](solver.md). 

## NASOQ Step example
An example for NASOQ Step is also provided in the NASOQ example (`nasoq/examples/nasoq_step_main.cpp`). A snapshot of the example is provided below. As shown, NASOQ Step continues iterations till its output is equal to `nasoq::NotFinished`.

```C++
#include <nasoq/nasoq_step.h>

int main(int argc, char *argv[]){

/// Setting inputs	

// ...

/// Solving the QP pronlem
 nasoq::NasoqStep *qm;
 qm = new nasoq::NasoqStep(sizeH,Hp,Hi,Hx,
                       q,CRows,CCols,Cp, Ci, Cx, d);
 qm->diag_perturb=pow(10,-9);
 qm->eps_abs=pow(10,-3);
 qm->max_iter = 0;
 qm->variant = nasoq::PREDET;
 if(qm->solve_init()){
  std::cout<<"Initialization: \n";
  std::cout<<qm->primal_vars[0] <<","<<qm->primal_vars[1]<<"\n";
  std::cout<<qm->dual_vars[0] <<","<<qm->dual_vars[1]<<"\n";
 }

  while( true ){
   auto ret_val = qm->solve_step();
   if(ret_val != nasoq::NotFinished)
    break;
   std::cout<<"Iteration :"<< qm->num_iter<<"\n";
   std::cout<<qm->primal_vars[0] <<","<<qm->primal_vars[1]<<"\n";
   std::cout<<qm->dual_vars[0] <<","<<qm->dual_vars[1]<<"\n";
  }

/// Free memory

```


## Command-line interface
In addition to C++ API, a terminal interface is also provided in the NASOQ repository. To use this interface, you should follow [NASOQ instructions](getting-started-nasoq.md#installation) to build the interface, i.e., ```NASOQ-BIN```.
After the build is done, the terminal interface can be launched by calling ```NASOQ-BIN``` with an input QP problem and the list of switches, which can be seen by commanding ```NASOQ-BIN -h```. Some important options are shown below:
```bash
-i : input SMP file
-e : Requested accuracy ,e.g., -3
-v : NASOQ variant, e.g., fixed or tuned
```
For details of options please check [NASOQ document page](solver.md). And for details about SMP format, please check [SMP repository page](repository.md).
