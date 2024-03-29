### Kernel Methods

- Basic Ideas
  - Use all training data to make prediction
- Assumptions:
  - Continuity: Most targets con't change abruptly
  - Similarity: Each traning pair shows something about the posible targets in its neighbourhood

#### Kernel Density Estimation

- Assumption: 
  - data are drawn independently from distribution $p(x)$ 
- Goal: 
  - Estimate $p(x)$ from data

1. Nonparametric Density Estimation – Histogram

   - Procedure:
     - Partition soace into bins of width $\Delta_i$ 
     - Compute density $p_i=\frac {n_i} {N \Delta_i}, \text{where $n_i$ is num of sample in bin $i$, $N$ is total num}$ 
   - Pros:
     - Data can be discarded after calculating $p_i$=> apply to sequential learning
   - Cons:
     - Dependent on bin width $\Delta_i$ 
     - Discontinuities due to bin edges
     - Exponential scaling with dimension of data ($M^D$ bins for $D$ dimensions, $M$ bins per dimension)

2. Nonparametric Density Estimation – Refined

   - Probability of points found In region $\mathcal R$: $\displaystyle\space P=\int_\mathcal{R}p(x)dx$ 

     - In $N$ observation from $p(x)$, $K$ points found inside region $\mathcal R$

       $\Rightarrow$ $\text{Prob}(K|N,P) = \frac{N!}{K!(N-K)!}P^K(1-P^{N-K})$ 

     - with large $N$: $\space P \approx \frac K N$ 

     - with small $\mathcal R$: $\space P \approx p(x)V, \text{where $V$ is volume of $\mathcal R$}$ 

   - Assume $\mathcal R$: 

     - small enough for $p(x)$ to be constant

     - big enough for enough $K$

       $\Rightarrow \space p(x)\approx \frac K {NV} $ 	

   - K-nearest-neighbours density estimation:

     - Fix $K$ : find a small region around $x$ with exactly $K$ points in it
     - $p(x) \approx \frac K {NV}$ 

   - Parzen window (kernel density estimation):

     - Fix $V$: define hypercube region $\mathcal R$ around $x$, with length $h$ 

     - Parzen window (kernel function): $k(u)=\begin{cases} 1,&|u_i| \le \frac 1 2 \text{ (threshold)} \\ 0, &\text{otherwise}\end{cases}$ 

     - num of point inside: $\displaystyle K = \sum_{n=1}^Nk(\frac {x-x_n} h) $ 

     - $\displaystyle p(x) \approx \frac K {NV} = \frac 1 N \sum_{n=1}^N \frac 1 {h^D} k(\frac {x- x_n}h )$ 

       Interpretation:  sum over $N$ cube centered at each of the $x_n$ 

   - Remaining Problem: 

     - Discontinuitiy due to hybercube (in\out)

     - Choose soomther kernel function

       $\Rightarrow \text{Gaussian kernel: } \displaystyle p(x)=\frac 1 N \sum_{n=1}^N \frac 1 {(2\pi h^2)^{D/2}} \text{exp{$-\frac {||x-x_n||^2}{2h^2}$}} $ 

       or any kernel function where $k(u)\ge 0, \space \displaystyle\int k(u)du$=1 

     - $h$ controls the trade-off between <u>sensitivity</u> and <u>soomthing</u>   

3. **Classification** 

   - Role of training data:

     - Prediction is based on linear combination of **kernel functions** evaluated at data
     - Traning data: $\Phi = \begin{bmatrix} \phi(x_1) \\ \phi(x_2) \\ \vdots \\ \phi(x_N) \end{bmatrix}, \text{ where $\phi$ is basis function, $\Phi$ is of $N\times M$}$ 

   - Intuition: (linear regression)

     - optimal (regularized) $w=(\lambda I + \Phi ^T \Phi)^{-1} \Phi^T \boldsymbol t$ 

     - $\begin{align} \text{prediction: }y(x) = \phi(x)w &= \phi(x)\space(\lambda I+\Phi^T\Phi)^{-1}\Phi^T \boldsymbol t & (\lambda I+\Phi^T\Phi)^{-1} = M\times M\\ &= \phi(x) \space \Phi^T (\lambda I + \Phi \Phi^T)^{-1} \boldsymbol t &  (\lambda I + \Phi \Phi^T)^{-1} = N\times N\end{align}$ 

       ​	$\Phi\Phi ^ T$ is considered as **kernel matrix**, which measures similarity of pair of data

       ​	$\small\text{as inner product of 2 points is a measure of similarity (similar to cosine similarity)}$ 

   - Kernel Function:

     - $k(x,x')=\phi(x)\phi(x')^T \text{: inner product of two vectors; symmetric}$ 

   - Kernel Matrix $K=\Phi\Phi^T$: Gram matrix over $\Phi$ 

     - $K_{i,j}=\phi(x_i)\phi(x_j)^T = k(x_i,x_j)$ 

     - positve-semidefinite (半正定):

       $\begin{align} v^TKv &= v^T\Phi\Phi^Tv & \Leftrightarrow \forall \lambda \text{ is eigenvalue of } K, \lambda \ge 0 \space&\space \Leftrightarrow \sum^N_{i,j=1} K_{i,j}\cdot v_iv_j \ge 0 \\ &= (v^T\Phi)(v^T\Phi)^T \\ &= \|v^T\Phi\|^2 \ge 0  \\ &\text{where $v$ is eigenvector of $K$} \end{align} $ 

     - symmetry: $K^T = K$ 

   - Dual Representation:

     - Cost function: $J(w)=\frac 1 2 (\boldsymbol t - \Phi w)^T(\boldsymbol t - \Phi w)+\frac \lambda 2 w^Tw $ 

     - optimal (regularized) $w=(\lambda I + \Phi ^T \Phi)^{-1} \Phi^T \boldsymbol t$ 

       $\Rightarrow w = \frac 1 \lambda \Phi^T (\boldsymbol t -\Phi w)$ 

       $\Rightarrow \text{let } w=\Phi^Ta \text{, where }a=-\frac 1 \lambda(\Phi w-\boldsymbol t)$ 

       $\Rightarrow J(a)=\frac 1 2 a^T\Phi\Phi^T\Phi\Phi^Ta-a^T\Phi\Phi^T\boldsymbol t  +\frac 1 2 \boldsymbol t^T \boldsymbol t + \frac \lambda 2 a^T\Phi\Phi^Ta$ 

       $\displaystyle\boldsymbol \Rightarrow J(a)=\frac 12 a^TKKa-a^TK\boldsymbol t + \frac12 \boldsymbol t^T \boldsymbol t + \frac \lambda 2 a^T K a $ 

       $\Rightarrow \text{let } \mathcal DJ(a)(\xi) = \xi^TKKa-\xi^TK\boldsymbol t + \lambda \xi^TKa = 0$ 

       $\Rightarrow K(Ka-\boldsymbol t+\lambda a)=0 \text{, while $K \neq 0$ in most cases}$ 

       $\boldsymbol\Rightarrow a = (K+\lambda I)^{-1} \boldsymbol t \text{ minimizes } J(a)$ 

     - Gain: 

       ​	$K$ is $N\times N$ matrix $\Rightarrow$ can use infinite num of basis functions: $\phi(x)=[\phi_0(x),...,\phi_\infty(x)]$ 

       ​	Can construct new valid kernel from given ones (don't need to think of basis functions) 

       ​	Can define kernel over graphs, sets, documnets...

   - Prediction:

     - $\begin{align} y(x) &= \phi(x)w=\phi(x)\Phi^Ta \\ &=k(x)(K+\lambda I)^{-1}\boldsymbol t &\text{, where } k(x)=\phi(x)\Phi \text{, } k_n(x)=k(x,x_n)=\phi(x)\phi(x_n)^T \end{align}$ 
     - $y(x)$ is based on **kernel function** $k(x_{\text{new}},x_\text{traning})$, which measures **similarity** between a pair of data

   - Kernel Construction:

     - Kernels from Basis Function: $k(x,x')=\phi(x)\phi(x')^T$ 

       ​	Ex. Polynomial, Guassian, Logistic Sigmoid	

     - Kernel from Theory: 

       ​	choose $k(x,x')$ from intuition and then check validation

       $\text{$K$ is Gram matrix }(K = \Phi\Phi^T) \Leftrightarrow K \text{ is positive semidefinite and symmetric:}$ 

       ​	$K = \Phi\Phi^T \Rightarrow...\Rightarrow K \text{ positive semidefinite and symmetric}$ 

       ​	$K \text{ is positive semidefinite and symmetric}:$ 

       ​		(Real) Symmetric: $K=Q\Lambda Q^T \text{, where $\Lambda$ is diagonal matrix with $\Lambda_{ii}=\lambda_i$, Q 正交	}$  	

       ​		positive semidefinite: $\lambda \ge 0 \Rightarrow \Lambda= DD \text{, where $D$ is diagnal matrix with $D_{ii}=\sqrt \lambda_i$ }$ 

       ​		$\Rightarrow K=\Phi\Phi^T \text{, where $\Phi=QD$ }$ 

       $\begin{align} \boldsymbol \Rightarrow k(x,x') \text{ is valid} &\Leftrightarrow k(x,x')=\phi(x)\phi(x')^T \\ &\Leftrightarrow \text{corresponding $K$ is positive semidefinite and symmetric } \end{align} $ 

     - Kernel from other kernels: operations that remain positive semidefinite and symmetric

     - Kernel over Graphs, Sets, Texts: only need a valid **similarity measure** $k(x,x')$ 

     - Kernels from Probabilistic Generative Models:

       ​	Given $p(x)$ define $k(x,x') = p(x)p(x'),\small \text{ $x$ and $x'$ are similar if they are both of high probabilities}$ 

       ​	Include a weighting function $p(i)$: $\displaystyle k(x,x')=\sum_i p(x|i)p(x'|i)p(i)$ 

       ​	For a continuous variable $z$: $\displaystyle k(x,x')=\int p(x|z)p(x'|z)p(z) dz$ (Hidden Markov Model)

#### Support Vector Machine (SVM)

- Do perform feature scaling **before** using Guassian Kernel

- Sparse Kernel Machine

  - prediction evaluated on a subset of data (support vectors)
  - most data can be discarded after training

- Choose the decision boundary with Maximum margin $\Rightarrow$ Maximum Margin Classifier

  - Margin: smallest distance between decision boudary and any of the sample

- **Maximum Margin Solution** on linear sperable data from 2 classes: 

  - Assume: 

    ​	linear sperable data from two classes

    ​	using linear discriminant $y(x)=\phi(x)w+b$ 

    ​	predict based on sign of $y(x): t\in\{-1,+1\}$ 

  - Distance of $x_n$ from boundary: $\displaystyle \text{dist}=\frac{|y(x_n)|}{\|w\|}=\frac{t_n(w^T\phi(x_n)+b)}{\|w\|} \text{ (for perfect classification $\forall i,t_iy(x_i)>0$)}$ 

  - Goal: maximize the smallest distance to samples in both classes

    - $\displaystyle \arg\max_{w,b}\{\frac 1 {\|w\|} \min_n[t_n(w^T\phi(x_n)+b)] \} \text{, s.t. } t_ny(x_n) > 0$ 

      $\displaystyle \Leftrightarrow \arg \max_{w,b,\gamma}\{\frac\gamma {\|w\|} \} \text{ s.t. } \forall n \in [1,N],t_n(\phi(x_n)w+b) \ge \gamma $ , and then resacle by $\gamma$ 

  - Canonical representation: $\displaystyle \arg\max_{x,b}\{\frac 1 {\|w\|} \}, \text{ s.t. } \forall n, t_n(\phi(x_n)w+b)\ge1$ 

    $\displaystyle \Leftrightarrow \arg\min_{w,b} \{\frac 1 2 \|w\|^2\} \text{ s.t. } \forall n, t_n(\phi(x_n)w+b)-1 \ge 0$  

  - From KKT conditions:

    $\displaystyle \begin{cases} w^T=\sum_{n=1}^N a_nt_n\phi(x_n)   &① \\ \sum_{n=1}^N a_nt_n=0 &② \\ \forall n, \space a_n[t_n(\phi(x_n)w+b)-1]=0 &③ \\ \forall n, \space t_n(\phi(x_n)w + b )- 1 \ge 0 &④ \\ \forall n, \space a_n \ge 0 &⑤ \end{cases}$ 

  - Lagrangian:

    $\displaystyle L(w,b,a) = \frac 1 2 \|w\|^2 - \sum_{n=1}^N a_n\{t_n[\phi(x_n)w+b]-1 \} \text{, $a_n$ is Lagrange multiplier} \\ \text{(minimize $L$ with respect to $w,b$; maximize $L$ with respect to $a$)}$ 

    Eliminate $w, b$ using KKT$①②\space+\space$dual representation: 

    $\displaystyle L(a)=\sum_{n=1}^Na_n - \frac 1 2 \sum_{n,m=1}^Na_na_mt_nt_m k(x_n,x_m) \text{ s.t. } \forall n, \sum_{n=1}^N a_nt_n=0 \space \boldsymbol \land \space a_n \ge0 $ 

    Equivlant problem: $\displaystyle \arg \max_a L(a) \text{ s.t. } \forall n, \space \sum_{n=1}^N a_nt_n=0 \space \land \space a_n \ge0 $ 

    $\displaystyle \begin{align} \boldsymbol\Rightarrow \text{Maximize Obect: }&L(a)=a-\frac 1 2 a^TQa \text{, s.t. }a^Tt=0 \boldsymbol \land \forall n, a_n\ge0 \\&\text{where $Q_{nm}=t_nt_mk(x_n,x_m)$}  \end{align}$ 

  - Support Vectors:

    $\displaystyle y(x)=\phi(x)w+b=\sum_{n=1}^Na_nt_nk(x,x_n) + b \small \text{, using ①}$ 

    $\displaystyle \text{From KKT③④⑤}: \\ \begin{aligned} \text{if $a_n=0$ : inactive constraint, }&&&&&&&&\text{$x_n$ not contributing to $y(x)$}  \end{aligned} \\ \begin{align} \text{iff $a_n>0$ : active constraint ($t_ny(x_n)=1$), }&&\text{ $x_n$ contributing to $y(x)$} \end{align}\\ \large \boldsymbol \Rightarrow \text{Support Vectors: $\forall x_n, \space t_ny(x_n)=1$ (all $x_n$ on the margin boundaries)}$ 

  - Solving bias $b$:

    $\displaystyle \forall \text{support vector } x_n,\space t_ny(x_n)=1 \\ \displaystyle \Rightarrow t_n(\sum_{m\in \mathcal S}a_mt_mk(x_n,x_m)+b)=1 \text{, where $\mathcal S$ is set of support vectors}$ 

    For more numerical stability: use $t_n^2 = 1$ and average over all support vectors

    $\displaystyle \Rightarrow b = \frac 1 {N_\mathcal S} \sum_{n\in\mathcal S}(t_n-\sum_{m\in\mathcal S}a_mt_mk(x_n,x_m))$ 

  - Prediction:

    - $\displaystyle y(x)=\sum_{n\in \mathcal S}a_nt_nk(x,x_n) + b \text{, where $\mathcal S$ is set of support vectors} \\ \Rightarrow \text{kernels are only evaluated at a subset of training data (support vectors)}$ 


- **Soft Margin SVM** on non-seperable data:

  - Basic idea:

    ​	Allow for overlapping class distributions - allow some points on "wrong side"

    ​	Incrasing penalty linearly with distance

  - Slack variable $\forall n, \xi_n \ge 0$: 

    $\xi_n \space\begin{cases} =0, &\text{correctly classified (on margin boundary or beyond)} \\  <1, &\text{in correct margin} \\ =1, &\text{on decision boundary} \\ >1, &\text{misclassified} \end{cases}$ 

  - Constraint:

    ​	$t_ny(x_n)\ge1-\xi_n \small \space \text{ (instead of  $t_ny(x_n)\ge1$)} \\ \Rightarrow \text{In all }\begin{cases} \xi_n \ge 0 \\ \xi_n \ge 1- t_ny(x_n) \end{cases}$ 

  - Goal: $\displaystyle \min_{w,\xi} C \sum_{n=1}^N\xi_n + \frac 1 2 \|w\|^2 \text{ s.t. } \xi_n\ge0 \boldsymbol \land t_ny(x_n)-1 + \xi_n\ge 0, \\ \text{where $C>0$ controls the trade-off between slack variable penalty and the margin} \\ \displaystyle \sum_n \xi_n \text{ as upper bound on the num of misclassified points }(\xi_n>1\text{ if misclassified})  \\ \Rightarrow C \text{ is analogous to (the inverse of) a regularization coefficient} \\ \space \text{ } \space \text{ with }C\rightarrow\infty \text{, turn into SVM for linear sperable data}$ 

  - From KKT Condition:

    $\begin{cases} \displaystyle w = \sum_{n=1}^Na_nt_n\phi(x_n) &\Leftrightarrow \frac{\partial L}{\partial w} = 0 \\ \displaystyle \sum_{n=1}^Na_nt_n=0 &\Leftrightarrow \frac{\partial L}{\partial b}=0 \\ \displaystyle a_n=C-\mu_n &\Leftrightarrow \frac{\partial L}{\partial \xi_n} = 0 \end{cases} \space,\space \begin{cases} a_n(t_ny(x_n)-1+\xi_n) = 0 \\ t_ny(x_n)-1+\xi_n\ge0 \\ a_n\ge0 \\ \mu_n\xi_n=0 \\ \xi_n\ge0 \\ \mu_n\ge0 \end{cases}$ 

  - Lagrangian:

    $\displaystyle L(w,b,a, \xi,\mu)=\frac 1 2\|w\|^2 + C \sum_{n=1}^N\xi_n - \sum_{n=1}^Na_n\{t_ny(x_n)-1+\xi_n\}-\sum_{n=1}^N\mu_n\xi_n \text{, where $a_n,\mu_n$ Lagrange multipliers}$ Eliminate $w,b,\xi_n$, using KKT + dual representation: 

    $\displaystyle L(a)=\sum_{n=1}^Na_n - \frac 1 2 \sum_{n,m=1}^N a_na_mt_nt_mk(x_n,x_m) \text{ s.t. } \forall n, \boldsymbol{a_n\in[0,C]} \space \boldsymbol \land \space \sum_{n=1}^Na_nt_n=0 $ 

    Equivlant problem: $\displaystyle\arg \max_{a}L(a) \text{, with $\boldsymbol{box \space constraint}$: $a_n\in[0,C] $ and constraint $\sum_{n=1}^Na_nt_n=0$}$ 

  - Support Vectors:

    $\forall n \in \mathcal S, a_n>0 \Rightarrow t_ny(x_n)=1-\xi_n \\ \begin{cases} \text{if } a_n < C \Rightarrow \mu_n>0 \text{, hence } \xi_n=0 & \boldsymbol \Rightarrow t_ny(x_n)=1 \text{ (point on margin boundary)} \\ \text{if } a_n=C \Rightarrow \mu_0=0 \text{, hence no constraint on } \xi_n &\boldsymbol \Rightarrow \text{ point either correctly classfied or misclassified} \end{cases}$ 

  - Solving bias $b$: 

    $\displaystyle t_n(\sum_{m\in \mathcal M}a_mt_mk(x_n,x_m)+b)=1 \text{, where $\mathcal M$ is set of support vectors with $a<C$}$ 

    For numerical stability:

    $\displaystyle b=\frac 1 {N_{\mathcal M}} \sum_{n\in \mathcal M} (t_n - \sum_{m\in \mathcal S}a_mt_mk(x_n,x_m))$ 

  - Prediction:

    $\displaystyle y(x)=\sum_{n\in \mathcal S}a_nt_nk(x,x_n) + b$ 

  - Limitations:

    ​	Complexity parameter $C$: to be found (e.g. via cross-validation)

    ​	Problematic in scaling to k-classes

    ​	Kernel matrix need to be **positive definite** 

    ​	Prediction is linear combinations of kernel functions

    ​	Output is decision, not posterior probability

1. Hypothesis function: $\begin{equation} h_\theta(x)= \begin{cases} 1 & \theta^Tx \geq 0, \\ 0 & \small \text{otherwise.} \end{cases}  \end{equation}$

2. Cost Function:

   - $\displaystyle J(\theta)=C \sum^m_{i=1}[y^icost_1(\theta^Tx^i)+(1-y^i)cost_0(\theta^Tx^i)]+\frac12 \sum^n_{i=1}\theta^2_j$
   - Curve of $cost(\theta^Tx^i)$![SVM Cost Function Curve](.\SVM Cost Function Curve.png)  

3. SVM Decision Boundary (Large margin classification)

   - $\displaystyle min_\theta \frac12 \sum_{j=1}^n \theta_j^2 = min_\theta\frac12||\theta||^2$ 
     - vector $\theta$ vertical to the decision boundary
     - samll margin => small projection to $\theta$  => large $\theta$ $\small \text{due to $h_\theta(x)$}$ => contradiction         **margin is actually the projection from data to $\theta$** 

4. **Kernel**:

   - Similarity function (a way to measure distance between examples):
     - $\displaystyle f_i = similarity(x,l^i) = e^{-\frac{||x_j - l_j^i||^2}{2\sigma^2}}$ (Guassian Kernel)
   - Hypothesis function: $\begin{equation} h_\theta(x) = \begin{cases} 1 & \theta^Tf \geq 0, \\ 0 & \small \text{otherwise.} \end{cases} \end{equation}$
     - $f = [f_0, f_1, f_2,...,f_n], \space \small f_i = similarity(x,l^i), f_0=1$ 
   - Choosing landmark $l$:
     - Apply feature scaling
     - Initialize $m$ landmarks to training examples
     - map $x$ -> $f$, $\small \text{with } f_0 = 1$ 
     - Traning SVM: solving minimization poblem of $J(\theta)$ using $(f,y)$ instead of $(x,y)$)

5. SVM parameters:

   - $C ( = \frac1\lambda)$:
     - Large $C$: lower bias, higher variance (small $\lambda$)
     - Small $C$: higher bias, lower variance (large $\lambda$)
   - $\sigma^2 \small \text{ in Guassian Kernel}$:
     - Large $\sigma^2$: features $f_i$ vary more smoothly, higher bias, lower variance
     - Small $\sigma^2$: features $f_i$ vary less smoothly, lower bias, higher variance
   - Similarity function (Kernel): 
     - Linear kernel (no kernel): $f = x$ 
     - Guassian kernel
     - Polynomial kernel: $f = similarity(x, l) = (x^Tl + a)^b, a,b \in N, x,l \text{ $\small\text{(usually)}$} \in R^+$ 
     - String kernel, Chi-square kernel, Histogram kernel, Intersection kernel...

6. Comparison between logistic regression: $n$ - num of features, $m$ - num of traning data

   - $n >> m$ $\small \text{($n\geq10m$)}$: Logistic regression or SVM with Linear kernel

     $\small \text{(large n => linear function is enough & small m => not enough data to train complicated hypothesis function)}$

   - small $n$ with intermidiate $m$ ($m \approx 10n$): SVM with Guassian kernel

   - $n << m$ ($\small \text{$10n \leq m$}$): **add more features** and then Logistic regression or SVM with Linear kernel

   **(Logistic regression and SVM with Linear kernel are similar)** 

#### Relevance Vector Machine

1. Assumption:

   - $p(t|x,w,\beta)=\mathcal N(t|y(x),\beta^{-1})$ 
   - $\displaystyle y(x)=\sum_{n=1}^N w_nk(x,x_n)+b = w^T\Phi\phi(x) + b$ 
   - $\displaystyle \text{Prior: } p(w|\alpha)=\prod_{i=1}^M \mathcal N(w_i|0,\alpha_i^{-1}) \text{, with different precision $\alpha_i$}$ 

2. Maximize Posterior:

   - Iteratively calculates $\alpha_i,\beta$ from training data by optimising a nonconvex function

   - When maximizing with respect to $a_i$, a significant portion of $a, \space a_i \rightarrow \infty$ 

     $\Rightarrow$ corresponding $w_i$ not contributin to prediction

3. Advantages $vs.$ Disadvantages:

   - Adv:

     - Parameters governing complexity and noise are determined from data 

       ​	(compared to SVM, which needs cross-validation)

     - Num of relevance vetors is much more smaller than suppot vectors in SVM

       $\Rightarrow$ faster prediction

     - Scaling well to k classes

   - Dis:

     - Non-convex function $\Rightarrow$ may result in local minimum
     - Longer training time than SVM