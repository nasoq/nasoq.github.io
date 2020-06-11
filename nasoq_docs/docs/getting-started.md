
## C++ API
NASOQ is a header-only library and does not need to be compiled. To use it in your code, you need to include ```<nasoq.h>``` in your code and add ```NASOQ``` to the list of libraries.
NASOQ needs METIS and MKL BLAS Libraries as prerequisites. Paths to these two libraries shoule be specifies by setting variables XX and XX as shown:
```bash
cmake -DXX -DXX= -DCMAKE_BULD_TYPE=release .. 
```

One example of using nasoq is shown in the repository in `nasoq/eigen_interface`. 

After installing you can use it as below:
```C++
#include <nasoq.h>

int main(){
...

}
```
For details about inputs, outputs, and settings of nasoq please see the [NASOQ document page](solver.md).


## Command-line interface
In addition to library API, a terminal interface is also provided. The terminal interface can be launched by calling ```NASOQ-BIN``` with an input QP problem. The list of switches, which can be seen by commanding ```NASOQ-BIN -h```, is shown below:
```bash
-i : 
-o :

```
For details of switches please check [NASOQ document page](solver.md).