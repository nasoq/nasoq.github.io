## Algorithm overview
NASOQ focuses on the solution of convex quadratic programming problems to find the linearly constrained minimizers of quadratic energies. In full generality our problem then is 
\begin{align} 
 \label{eq:qp}
\min_x \quad \frac{1}{2} x^THx + q^Tx \quad \text{s.t.}
\quad  Ax=b, \quad  C  x\leq d
\end{align}
where the unknown minimizer $x \in \mathbb{R}^n$ is constrained by linear equality constraints $A x=b$ and inequality constraints $Cx\leq d$. 
Note that in many cases, we may have only inequality or equality constraints. However, in the following, without loss of generality, we consider the full mixed case. 
Here the symmetric matrix $H$ is, either by construction or standard user regularization, a positive-definite matrix, thus the QP is strictly convex.


NASOQ is an active-set QP solver based on Goldfarb-Idnani (GI) [Goldfarb and Idnani, 1983] strategy. 
Active-set methods start with a feasible solution and keep a running set of proposed active inequality constraints $\mathcal{W}$ to reach the optimal solution while maintaining feasibility conditions. 
Active-set methods are then either primal-feasible, preserving the primal-feasibility condition or else are dual-feasible, preserving the non-negativity condition. 
GI is a dual-feasible active-set approach, and so enables direct and inexpensive initialization.  
 

NASOQ begins by initializing an empty active-set proposal, $\mathcal{W} = \emptyset$ with zero dual variables, $z_0 = 0$.
The resulting initial KKT system to solve is then the indefinite linear system, 

$$\begin{bmatrix} H & A^T\\ A & 0 \end{bmatrix} \begin{bmatrix} x_0\\ y_0 \end{bmatrix} = \begin{bmatrix} -q\\ b \end{bmatrix}$$

Then, each successive iteration of NASOQ improves the last iterate's solution by updating the active-set proposal $\mathcal{W}$ and so the corresponding active-set constraint matrix $C_{\mathcal{W}}$ and the right-hand side constraint vector $c_{w}$. The NASOQ algorithm updates the active set by only adding one or removing one constraint in each successive iteration. Here $w$ is the activated constraint.
The next descent direction for the QP is then determined by solving the updated KKT system,

$$  \begin{bmatrix} H & A^T & C_{\mathcal{W}}^T\\ A & 0 & 0 \\C_{\mathcal{W}} & 0 & 0 \\ \end{bmatrix} \begin{bmatrix} \Delta x \\ \Delta y \\ \Delta z \\ \end{bmatrix} = \begin{bmatrix} c_{w} \\ 0 \\ 0 \\ \end{bmatrix}$$

The dual and primal variables of the next iteration are then updated by finding step lengths along the computed descent directions, i.e., $\Delta x, \Delta y, \Delta z$. The step lengths ensure that the activated constraint becomes primal-feasible and all dual variables remain dual-feasible. 
Thus, in each iteration, both the dual and primal variables corresponding to the constraints in the active set are both non-negative and primal-feasible. 


## Variants
NASOQ has two variants: *NASOQ-Fixed* and *NASOQ-Tuned*, and each of these two variants individually offers a different balance in the trade-off between efficiency and accuracy for larger-scale problems. 
A key feature of NASOQ is that in our construction of the linear system solver, i.e., LBL and the row modification, i.e., SoMoD we expose three parameters with direct and intuitive interpretations that enable us to balance efficiency against accuracy for different applications and problem
scales.
These three parameters are: 

- *max_iter:* the maximum number of refinement iterations for incrementally improving the solution of a KKT system after
the solve phase;

- *stop_tol:* the threshold defining the upper bound for the residual accuracy of the KKT system during the refinement phase;

- *diag_perturb:* value added to zero-entry diagonals of the KKT matrix to stabilize LBL and row modification in SoMod.


NASOQ-Fixed works well across the board without changing a default setting. To use NASOQ-Fixed, you can set ``` nasoq->variant = Fixed```.  NASOQ-Fixed settings are slightly different from the settings in the siggraph2020 paper. The `max_iter` parameter in the new settings is decremented by one so it runs faster.
 NASOQ-Tuned uses a range of reasonable settings for these three parameters known as a priori, to perform a rapid sweep for improved accuracy. The setting for activating NASOQ-Tuned is:
 ``` nasoq->variant = Tuned``` (This mode has not been included in the API yet). 


## NASOQ Step
NASOQ iteratively improves the solution of a QP problem. It happens in some applications that the solution after a number of iterations is enough and waiting for the final solution is not necessary. Or using an intermediate solution might help simulations progress. NASOQ Step is an interface to the NASOQ solver that provides the solution after each iteration. Users can use the intermediate solutions based on their application's needs.  


## Termination criteria
The termination criteria in NASOQ are four conditions that are listed below:

* Primal-feasibility: $\Big( (Ax-b)^T, (\max(\textbf{0},Cx-d))^T \Big)^T \| < \epsilon_f$

* Stationarity: $|Hx + q + A^{T}y + C^{T}z\| < \epsilon_s$

* Complementarity: $|z \odot (Cx-d)\| < \epsilon_c$, Here $\odot$ is the Hadamard (element-wise) product.

* Non-negativity:$|\min(\textbf{0},z)\| < \epsilon_n$

We design NASOQ and analyze QP methods on their ability to drive all four of these measures ($\infty$-norm) below a common, maximum error threshold accuracy: $\epsilon \geq \max(\epsilon_f,\epsilon_s,\epsilon_c,\epsilon_n)$. 
While necessary accuracies for each of the four measures certainly change per application, a desirable goal for a general-purpose QP algorithm is to solve every reasonable problem to any requested accuracy. 
Here we design for general-purpose QP problems and so do not predict a priori what measures are most important. Thus we evaluate fitness by asking each solve to drive all measures below $\epsilon$. To set the accuracy threshold in NASOQ, you may use the following:
```nasoq->eps_abs = 1e-3; ```



## Settings
The three different parameters, *max_iter, stop_tol, diag_perturb* that exist in NASOQ, often show a significant effect on the performance and accuracy of the solver. Also, requesting a more accurate solution, i.e., lower termination criteria or `eps_abs` often leads to much more number of iterations and thus slower convergence time. 
NASOQ has some pre-defined variants, i.e., fixed and tuned. NASOQ-Tuned is designed conservatively for the lowest failure rate. NASOQ-Fixed often provides a reasonable balance between balance and failure-rate. However, a customized setting can lead to better performance if the requirements of the application are known. 
Here we provide a few suggestions based on our experience with working with different real applications:

<table>
  <thead>
    <tr>
      <th> Setting Property </th>
      <th> NASOQ Variant </th>
      <th> max_iter </th>
      <th> stop_tol </th>
      <th> diag_perturb </th>
      <th> eps_abs </th>
      <th> Example Applications</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Low accuracy and fast</td>
      <td>PREDET</td>
      <td>0</td>
      <td>1e-15</td>
      <td>1e-9</td>
      <td>1e-3</td>
      <td>Geometry processing, Model reconstruction</td>
    </tr>
    <tr>
      <td>accurate enough</td>
      <td>PREDET</td>
      <td>1</td>
      <td>1e-15</td>
      <td>1e-9</td>
      <td>1e-6</td>
      <td>Contact simulations, Control</td>
    </tr>
  </tbody>
</table>

The `PREDET` variant of NASOQ is a  variant that takes the input settings determined by the user. If you are not sure, you may start with the fixed variant of NASOQ. 

You can also set the maximum number of NASOQ solver iterations by setting `max_iter_nas` . After NASOQ's iterations reach this number, it terminates and returns the last solution. 


## Return value

NASOQ solver return value has four states:

0:`Infeasible` the problem is unbounded.

1:`Optimal` When primal-feasibility, stationary, and non-negativity are satisfied.

2:`Inaccurate` only primal-feasibility is satisfied. 

3:`NotConverged` None of the termination criteria are satisfied. 





