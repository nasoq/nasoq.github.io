
NASOQ is a header-only library and does not need installation. Here we explain how NASOQ C++ API can be used to solve your QP problems in your code.

## C++ API
An example of how to use NASOQ in your code is provided in `nasoq/examples` and here we explain the example step by step.
We assume your project is somewhere like in `nasoq\examples\`. 

First you need to clone the NASOQ repository in your project folder:
```bash
cd nasoq/examples/
git clone https://github.com/sympiler/nasoq
```

After cloning NASOQ, you need to modify the CMakeLists of your project file to include NASOQ dependencies. Project `NASOQ_Test` in the CMakeLists of `nasoq/examples/`  shows the components that you need to add to your CMake file. 
After modifying the CMake file of your project, you need to ensure the library dependencies of NASOQ are installed. 
NASOQ needs METIS and MKL BLAS Libraries as prerequisites.  
If the installation paths of these libraries are in the system path, CMake should be able to handle dependencies. If not, you need to set CMake variables as shown below: 
```bash
cd where/cloned/nasoq
mkdir build
cd build
cmake -DMKL_ROOT_PATH=path/to/intel -DMETIS_ROOT_PATH=path/to//metis-5.1.0/build/Linux-x86_64/  -DCMAKE_BUILD_TYPE=Release ..
```
NASOQ optionally needs Suitesparse library, and if it is not in the system path, you can use `-DSUITE_ROOT_PATH=path/to/suitesparse` to tell CMake where Suitesparse is installed.

After setting up dependencies, You can call NASOQ solver to solve your QP problems by including ```<nasoq.h>```, and by preparing its inputs, as shown in the following code. 

```C++
#include <nasoq.h>

int main(){
 /// Allocate
 ...
 /// Inputs
 q[0] = -4; q[1] = -4;

 Hp[0]=0;Hp[1]=1;Hp[2]=2;
 Hi[0]=0;Hi[1]=1;
 Hx[0]=2;Hx[1]=2;

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
 qm->reg_diag=pow(10,-9);
 qm->eps_abs=pow(10,-3);
 qm->eps_rel=pow(10,-3);
 qm->inner_iter_ref = 0;
 qm->outer_iter_ref = 0;
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
Finally, you will build your project with NASOQ by emitting the `make` command. 

You can also use NASOQ inside the source tree. One example of using nasoq is shown in the `nasoq/eigen_interface` directory.  Choosing between in-source or out-source depends on your project settings. 

## Command-line interface
In addition to C++ API, a terminal interface is also provided in the NASOQ repository. To use this interface, you need to follow the below instructions to build the interface, i.e., ```NASOQ-BIN```:
```bash
cd where/you/want/to/install/nasoq/
git clone https://github.com/sympiler/nasoq
cd nasoq 
mkdir build
cd build
cmake -DMKL_ROOT_PATH=path/to/intel -DMETIS_ROOT_PATH=path/to//metis-5.1.0/build/Linux-x86_64/  -DCMAKE_BUILD_TYPE=Release ..
make
```
After the build is done the terminal interface can be launched by calling ```NASOQ-BIN``` with an input QP problem. The list of switches, which can be seen by commanding ```NASOQ-BIN -h```. Some important options are shown below:
```bash
-i : input SMP file
-e : Requested accuracy ,e.g., -3
-v : NASOQ variant, e.g., fixed or tuned
```
For details of options please check [NASOQ document page](solver.md). And for details about SMP format, please check [SMP repository page](repository.md).
