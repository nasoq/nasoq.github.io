## Algorithm overview
LBL is an algorithm for factorizing a sparse indefinite linear system of equations. 
LBL solves $Hx=q$ for $x$, where $q$ is a dense vector, and $H$ is a sparse symmetric indefinite matrix. 
To solve the system $Hx = q$, LBL applies LDL factorization to decompose the matrix $H$ into

$$H = P_{fill} P_{S} (LDL^{T} + E) P_{S}^{T} P_{fill}^{T}$$

where $D$ is a blocked diagonal symmetric matrix (due to our use of Bunch-Kaufman pivoting[schenk et.al. 2006]), $L$ is a sparse lower triangular matrix, $E$ is a diagonal perturbation matrix (necessary to avoid zero diagonals, which can cause instabilities, $P_{fill}$ is a fill-reducing ordering (such as METIS[karypis 1995]), and $P_S$ is reordering due to pivoting. Given this factorization of the matrix, LBL then uses $L$ and $D$ along with $q$ (the right-hand side) to quickly compute the solution $x$ via triangular solve and an iterative refinement method.


The overall process of the factorization in LBL closely follows that of standard sparse linear system solvers.  For efficient factorization, the sparsity pattern of $H$ is analyzed during *symbolic analysis*. The symbolic analysis uses the sparsity pattern of $H$ to construct *symbolic information*, which consists of the fill-reducing ordering $P_{fill}$ and the sparsity pattern of $L$.  Symbolic information guides the *numeric factorization*, which operates on the actual numeric values of $H$ to compute the nonzero values of $L$ and $D$. Matrices $L$ and $D$ are then used to find $x$ with a lower triangular system solve and blocked diagonal solver. An iterative refinement algorithm, GMRES is also used when accurate solution is required.

In addition to solving a standalone linear system of equations, LBL also supports row modification when the sparsity pattern of changes is known ahead of time. The LBL operating mode that supports row modification is used in NASOQ and is called *sparsity oriented row modification (SoMoD)*. Here we only discuss the interface LBL as a standalone solver. 

## Settings
The three parameters that are explained in the [NASOQ algorithm](solver.md) is in common with LBL. So the three important parameters are: 

- *max_iter:* the maximum number of refinement iterations, GMRES for incrementally improving the solution of a linear system after the solve phase;

- *stop_tol:* the threshold defining the upper bound for the residual accuracy of the linear system during the refinement phase;

- *diag_perturb:* value added to zero-entry diagonals of the matrix to stabilize LBL.



