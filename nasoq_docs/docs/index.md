
NASOQ is a scalable and efficient Quadratic Programming solver that obtains solutions to requested accuracies. In addition, the NASOQ library includes linear system solver for indefinite systems which is named LBL.
NASOQ is tested for an extensive number of QP problems from real application that is also shared and is accessible from this repositoy.  

## Why NASOQ?
NASOQ is:

* **Accurate:** NASOQ converges to all reasonable requested accuracy thresholds. NASOQ provides the lowest failure rate among esiting open-source and commercial solvers for a range of real QP problems. 

* **Fast:** NASOQ uses an efficient sparsity-oriented row modification algorithm (SOMOD) and a fast indefinite solver (LBL) enabling quick solve of intermediate indefinite linear system of equations arising along the QP solve.

* **Versatile:** NASOQ efficiently and accurately solves QP problems from a wide range of applications including contact simulation, shape deformation, model predictive control, model reconstruction and ... . 

* **Scalable:** NASOQ uses the sparsity pattern of the problem to efficiently solve QP problems with small to large number of variables and constraints. Problems from 10 to 100 thounds of variables and from 10 to thounds of linear constraints.

## Citing us

 *@article{Cheshmi2020NASOQ, author = {Cheshmi, Kazem and Kaufman, Danny M. and Kamil, Shoaib and Dehnavi, Maryam Mehri}, title = {NASOQ: Numerically Accurate Sparsity-Oriented QP Solver}, journal = {ACM Transactions on Graphics}, year = {2020}, volume = {39}, number = {4} }*


## Credits
The people have been involved in the developement of NASOQ:

* Kazem Cheshmi

* Danny M. Kaufman

* Shoaib Kamil

* Maryam Mehri Dehnavi

