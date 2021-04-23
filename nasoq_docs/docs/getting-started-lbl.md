
LBL is a part of the NASOQ repository and can be installed similarly. Here we explain how LBL C++ API can be used to solve your sparse indefinite linear systems.

## Installation
LBL installation is the same as [NASOQ installation instructions](getting-started-nasoq.md#installation) 

## C++ API
An example of how to use LBL in your C++ code is provided in the`nasoq/examples` directory, and here we explain the example step by step.
We assume your project is somewhere like in `nasoq\examples\`. 
First you should install NASOQ and add its dependent libraries to your CMake file.
 Project `LBL_Test` in the CMakeLists of the `nasoq/examples/` directory  shows the components that should be added to your CMake file. 
Then you can call LBL solver to solve a linear system by including ```<linear_solver_wrapper.h>``` and preparing its inputs, as shown in the following code. 

```C++
#include <QP/linear_solver_wrapper.h>

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
For details about inputs, outputs, and settings of LBL, please see the [LBL document page](linear-solver.md).

You can also use LBL inside the NASOQ source tree. One example of using LBL is shown in the repository in the `nasoq/eigen_interface` directory.  


## SoMoD: Sparsity Oriented Row Modification
SoMoD is designed to update the factors of LDL factorization after one or multiple rows of the input matrix is modified. 
SoMoD is an expert routine that is only used inside NASOQ. SoMoD works when the sparsity pattern of changes is known as a priory. There is an example, `lbl_somod_main.cpp` on how to use SoMoD in `nasoq/examples`. 


