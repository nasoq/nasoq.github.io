
NASOQ$^1$ is a scalable and efficient Quadratic Programming solver that obtains solutions to requested accuracies. Also, the NASOQ library includes a linear system solver for indefinite systems which, is named LBL.
NASOQ is tested for an extensive number of QP problems from real applications that are also shared and are accessible from this repository. 

*<span style="font-size:0.75em;">1. NASOQ /nʌzoʊk/ is a Persian/Indian/Urdu word that means delicate and indicates accuracy.</span>*
## Why NASOQ?
NASOQ is:

* **Accurate:** NASOQ converges to all reasonably requested accuracy thresholds. NASOQ provides the lowest failure rate among existing open-source and commercial solvers for a range of real QP problems. 

* **Fast:** NASOQ uses an efficient sparsity-oriented row modification algorithm (SOMOD) and a fast indefinite solver (LBL) enabling quick solve of the intermediate indefinite linear system of equations arising along the QP solve.

* **Versatile:** NASOQ efficiently and accurately solves QP problems from a wide range of applications including, contact simulation, shape deformation, model predictive control, model reconstruction, and... 

* **Scalable:** NASOQ uses the sparsity pattern of the problem to efficiently solve QP problems with small to a large number of variables and constraints. Problems from 10 to 100 thousands of variables and from 10 to thousands of linear constraints.

## Citing us
If you are using NASOQ, please cite the NASOQ paper or support us by putting a star in the NASOQ GitHub repository.

```
 @article{Cheshmi2020NASOQ, author = {Cheshmi, Kazem and Kaufman, Danny M. and Kamil, Shoaib and Dehnavi, Maryam Mehri}, title = {NASOQ: Numerically Accurate Sparsity-Oriented QP Solver}, journal = {ACM Transactions on Graphics}, year = {2020}, volume = {39}, number = {4} 
 ```

We are keen to hear your success stories with NASOQ. We are also ready to help you to fit NASOQ into your needs. Please [contact us](mailto:kazem@cs.toronto.edu).

## Credits
The people have been involved in the development of NASOQ:

* Kazem Cheshmi

* Danny M. Kaufman

* Shoaib Kamil

* Maryam Mehri Dehnavi

