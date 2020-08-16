
LBL is a part of NASOQ repository and is similarly a header only library and do not need installation. Here we explain how LBL C++ API can be used to solve your sparse indefinite linear systems in your code.

## C++ API
An example for how to use LBL in your code is provided in `nasoq/examples` and here we explain the example step by step.
We assume your project is somewhere like in `nasoq\examples\`. 

First you need to clone the NASOQ repository in your project folder:
```bash
cd nasoq/examples/
git clone https://github.com/sympiler/nasoq
```

After cloning NASOQ, you need to modify the CMakeLists of your project file to include NASOQ dependencies. Project `LBL_Test` in the CMakeLists of `nasoq/examples/`  shows the components that you need to add to your cmake file. 
After modifying the cmake file of your project, you need to ensure the library dependencies of NASOQ are installed. 
NASOQ needs METIS and MKL BLAS Libraries as prerequisites.  
If installation paths of these libraries are in the system path, cmake should be able to handle dependences. If not, you need to set cmake variables as shown below: 
```bash
cd where/cloned/nasoq
mkdir build
cd build
cmake -DMKL_ROOT_PATH=path/to/intel -DMETIS_ROOT_PATH=path/to//metis-5.1.0/build/Linux-x86_64/  -DCMAKE_BUILD_TYPE=Release ..
```

After setting up dependencies, You can call LBL solver to solve your linear system by including ```<linear_solver_wrapper.h>``` and preparing its inputs as shown in the following code. 

```C++
#include "linear_solver_wrapper.h"

/*
 * Solving Hx = q
 * H is a sparse matrix stored in CSC format
 * q is a dense array
 */

int main(int argc, char *argv[]){

 /// Declaring input matrices
 // allocate
	...
 q[0] = -4; q[1] = -4;

 Hp[0]=0;Hp[1]=1;Hp[2]=2;
 Hi[0]=0;Hi[1]=1;
 Hx[0]=2;Hx[1]=2;

 auto *H = new nasoq::CSC; H->nzmax = nnzH; H->ncol= H->nrow =sizeH;
 H->p = Hp; H->i = Hi; H->x = Hx; H->stype=-1;

/// Solving the linear system
 auto *lbl = new nasoq::SolverSettings(H,q);
 lbl->ldl_variant = 4;
 lbl->req_ref_iter = 2;
 lbl->solver_mode = 0;
 lbl->reg_diag = pow(10,-9);
 lbl->symbolic_analysis();
 lbl->numerical_factorization();
 double *x = lbl->solve_only();


 /// Printing results
 // expected x={-2,-2};
 std::cout<<"Solution: ";
 for (int i = 0; i < sizeH; ++i) {
  std::cout<<x[i]<<",";
 }
 /// Free memory
 ...

}
```
For details about inputs, outputs, and settings of LBL please see the [LBL document page](linear-solver.md).
Finally, you will build your project with NASOQ by emitting `make`. 

You can also use LBL inside the NASOQ source tree. One example of using LBL is shown in the repository in `nasoq/eigen_interface`.  Choosing between in-source or out-source depend on your project settings. 

