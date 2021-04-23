
 Here we explain how you can install NASOQ and use its C++ API to solve your QP problems.

##Installation
First you should clone the NASOQ repository in your project folder:
```bash
cd nasoq/examples/
git clone https://github.com/sympiler/nasoq
```
NASOQ needs the following dependencies:

* CMake
* C++ compiler (gcc, icc, or clang)
* OpenMP (optional) : OpenMP is an optional dependency but without OpenMP, NASOQ runs sequentially.
* METIS (Optional) : METIS dependency is already handled by CMake. 
* a BLAS Library (MKL or OpenBLAS)  

If the installation paths of these libraries are in the system path, CMake should be able to handle dependencies. If not, you should set CMake variables as shown below:
```bash
cd where/cloned/nasoq
mkdir build
cd build
cmake -DMKL_ROOT_PATH=path/to/intel -DMETIS_ROOT_PATH=path/to//metis-5.1.0/build/Linux-x86_64/  -DCMAKE_BUILD_TYPE=Release ..
make
```
A header-only version NASOQ is accessible from [here](https://github.com/sympiler/nasoq/archive/refs/tags/v0.1.0.zip).

## C++ API
To use NASOQ C++ API, you should first install NASOQ and then follow the example provided in `nasoq/examples`. Here we explain the example step by step.
After installing NASOQ, you should modify the CMakeLists of your project file to add nasoq and its dependent libraries, i.e., METIS and a BLAS library.  
After setting up dependencies, You can call NASOQ solver to solve your QP problems by including ```<nasoq.h>```, and by preparing its inputs, as shown in the following code. 

```C++
#include <nasoq.h>

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


You can also use NASOQ inside the NASOQ source tree. One example of using nasoq is shown in the `nasoq/eigen_interface` directory.   

## Command-line interface
In addition to C++ API, a terminal interface is also provided in the NASOQ repository. To use this interface, you should follow [NASOQ instructions](getting-started-nasoq.md#installation) to build the interface, i.e., ```NASOQ-BIN```.
After the build is done, the terminal interface can be launched by calling ```NASOQ-BIN``` with an input QP problem and the list of switches, which can be seen by commanding ```NASOQ-BIN -h```. Some important options are shown below:
```bash
-i : input SMP file
-e : Requested accuracy ,e.g., -3
-v : NASOQ variant, e.g., fixed or tuned
```
For details of options please check [NASOQ document page](solver.md). And for details about SMP format, please check [SMP repository page](repository.md).
