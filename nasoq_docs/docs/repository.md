## Applications

We assemble a repository for sparse QP problems of different scales, most of which come from applications in animation, geometry processing, and simulation. An overview of the repository is provided in the [SMP page](https://nasoq.github.io/smp.html).
Here we explain the storage format that is used in the Sparse Mathematical Programming (SMP) repository and also its corresponding I/O routines. We also explain how SMP is used in NASOQ benchmark to fairly compare different QP solvers across SMP repository problems. 

## SMP storage format
Sparse Mathematical Programming (SMP) format aims for providing a unified and readable format that is compatible with existing sparse matrix formats such as matrix market format. 
SMP uses YAML format to serialize the matrices of a QP problem into single unified file in a readable format.   
An example SMP format is shown below:

```yaml
```

As shown a QP problem in the SMP format is stored in the general form of:
\begin{align} 
\min_x \quad \frac{1}{2} x^THx + q^Tx \quad \text{s.t.}
\quad  Ax=b, \quad  l \leq C'  x\leq u
\end{align}
that is more compressed than the general form that NASOQ supports which we call *general inequality/equality form (IE)* and is shown below:
\begin{align} 
\min_x \quad \frac{1}{2} x^THx + q^Tx \quad \text{s.t.}
\quad  Ax=b, \quad  C  x\leq d
\end{align}
ans also more compressed than other general forms such as what we we call *general bounded form (bounded)* and is shown below:

\begin{align} 
 \label{eq:qp}
\min_x \quad \frac{1}{2} x^THx + q^Tx \quad \text{s.t.} \quad 
l' \leq  C''  x\leq u'
\end{align}

These three representations lead to the same optimal results however they are different in terms of storage requirements and also the number of iterations that one solver might need to solve the problem.


### SMP conversion
We developed a repository for converting different representations of a QP problem, i.e., IE and Bounded forms to SMP and also from SMP to others. The repository does not have any dependency and to install:
```bash
git clone https://github.com/sympiler/smp-format
cd smp-format 
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

After building there will be three drivers, i.e., `ie2smp`, `bounded2smp`, and `smp-convertor` are available.  The `ie2smp` driver takes all matrices of an IE format which are stored in matrix market format and generates an SMP format.
The `bounded2smp` driver takes all matrices of a bounded format which are again stored in matrix market format and generates an SMP format.
The `smp-convertor` shows an example of how SMP format is used an intermediate format to convert every two formats together. 



## NASOQ benchmark
We also developed a unified framework to run all solvers with their settings for the problems in the SMP repository. The installation of the benchmark depends on the solvers that are added and their dependencies. The instructions for installing the benchmark is provided in the [nasoq-benchmarks](https://github.com/sympiler/nasoq-benchmarks).

### Adding a new solver
Adding a new solver to the benchmark is easy. 

The *first* step is to write a driver for the solver that takes an SMP format and generates the format that is needed for the solver. Three examples for OSQP, NASOQ, and Gurobi are provided in [drivers subdirectory](https://github.com/sympiler/nasoq-benchmarks/tree/master/drivers). As explained, SMP repository provides support for different QP formats so they can be easily used here.

The *second* step is to update the cmake file in the `drivers` subdirectory by adding a new project that builds the driver for the new solver.

The *third* step is to add a line to the `run_all.sh` script as shown below:
```bash
echo "Running [The name of the new solver] ..."
bash scripts/NASOQ_bench.sh $BUILDIR/drivers/[New-solver-bin] $DATASET $eps "-v [if it has a variant]"> logs/new-solver-e${eps}.csv
```

The `run_all` script runs all installed solvers for the specified dataset and the requested accuracy and generates the performance profile graphs. 









