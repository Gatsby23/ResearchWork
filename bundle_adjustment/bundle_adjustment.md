# Bundle Adjustment

## 1. 分布式优化和交替乘子法(Alternating Direction Method of Multipliers, ADMM)

对于标准形式的优化问题：

<img src="http://latex.codecogs.com/gif.latex?\arg min\ f_0(x), \\ s.t.\ f_i(x) \leq 0, i = 1,...,m \\ h_i(x)=0, i=1,...,p" />

它的Lagrangian函数为 

<img src="http://latex.codecogs.com/gif.latex? L(x,\lambda, \nu)=f_0(x)+\sum_{i=1}^m \lambda_if_i(x) + \sum_{i=1}^p \nu_ih_i(x)" />


<img src="http://latex.codecogs.com/gif.latex?\lambda,\nu" />