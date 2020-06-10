## Installation
NASOQ is a header-only library and does not need to be compiled. To use it in your code, you need to include it as a header. However, NASOQ needs METIS and MKL BLAS Libraries as prerequisite. To specify the paths for these two libraries, XX and XX variables have to be set properly as shown:
```
cmake -DXX -DXX= -DCMAKE_BULD_TYPE=release .. 
```

## Library interface



## Terminal interface
In addition to library API, a terminal interface is also provided. The terminal interface can be launched by calling ```NASOQ-BIN``` with right settings. 