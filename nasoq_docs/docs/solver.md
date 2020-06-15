## Algorithm overview
NASOQ focuses on the solution of convex quadratic programming problems to find the linearly constrained minimizers of quadratic energies. In full generality our problem then is 
\begin{align} 
 \label{eq:qp}
\min_x \quad \frac{1}{2} x^THx + q^Tx \quad \text{s.t.}
\quad  Ax=b, \quad  C  x\leq d
\end{align}
where the unknown minimizer $x \in \mathbb{R}^n$ is constrained by linear equality constraints $A x=b$ and inequality constraints $Cx\leq d$. 
Note that in many cases we may have only inequality or equality constraints. However, in the following, without loss of generality, we consider the full mixed case. 
Here the symmetric matrix $H$ is, either by construction or standard user regularization, a positive-definite matrix thus the QP is strictly convex.




## Variants
NASOQ has two variants: NASOQ-Fixed and NASOQ-Tuned and each of these two variants individually offer a different balance in the trade-off between efficiency and accuracy for larger scale problems. 
A key feature of NASOQ is that in our construction of the linear system solver, i.e., LBL and the row modification, i.e., SoMoD we expose three parameters with direct and intuitive interpretations that enable us to balance efficiency against accuracy for different applications and problem
scales.
These three parameters are: 

- *max_iter:* the maximum number of refinement iterations for incrementally improving the solution of a KKT system after
the solve phase;

- *stop_tol:* the threshold defining the upper bound for the residual accuracy of the KKT system during the refinement phase;

- *diag_perturb:* value added to zero-entry diagonals of the KKT matrix to stabilize LBL and row modification in SoMod.


NASOQ-Fixed works well across the board without tuning a default setting. To use NASOQ-Fixed you can set ``` nasoq->variant = Fixed```. 
 NASOQ-Tuned uses a range of reasonable settings for these three parameters known as a priori, to perform a rapid sweep for improved accuracy. The setting for activating NASOQ-Tuned is:
 ``` nasoq->variant = Tuned```.


## Termination criteria
The termination criteria in NASOQ are four conditions that are known as KKT conditions and listed below:
$$
where $\epsilon$ is the accuracy threshold and is determined by the user. A lower accuracy typically needs more iterations. The termination criteria in different solver might differ depending how the accuracy threshold is defined. Some solvers use a relative criteria, i.e. normalizing the requested accuracy with a problem-related norm and some other supports both. It is important to know because of this, the definition of accurate in different solvers are different. 
We found absolute criteria do not often lead to false convergence and thus depending on the application nature, an optimistic accuracy threshold would work well. Relative tolerance however, require selecting a very small threshold to ensure accuracy for different problems. To set the accuracy threshold in NASOQ, you may use the following:
```nasoq->eps = 1e-3; 
```

## Settings


