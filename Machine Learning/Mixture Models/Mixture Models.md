### Mixture Models

- Kullback-Leibler divergence

  - Distance between two distributions $p(x)$ and $q(x)$ 
    - $\text{discret: } \displaystyle KL(q\|p) = \sum_x q(x) \ln \frac{q(x)}{p(x)} = -\sum_x q(x)\ln \frac{p(x)}{q(x)}$ 
    - $\text{continuous: } \displaystyle KL(q\|p)=\int q(x)\ln \frac{q(x)}{p(x)}dx = -\int q(x)\ln \frac{p(x)}{q(x)}dx$  
  - Properties
    - $KL(q\|p) \ge 0$ 
    - not symmetric: $KL(q\|p) \neq KL(p\|q)$ 
    - $KL(q\|p) = 0 \Leftrightarrow q=p$ 
    - invariant under parameter tansformations

- **General Expectation Maximization Algorithm** 

  - Goal:

    - Find maximum likelihood solution for models with latent variables

  - Variables:

    - $X$: observed variables
    - $Z$: latent variables
    - $\theta$: model parameters

  - Log Likelihood:

    - $\displaystyle \ln p(X|\theta)=\ln \{ \sum_Z p(X,Z|\theta) \}$ 

      $\Rightarrow$ problem:	$\ln$ out of $\text{sum} \Rightarrow$ no clean math solution 

      ​			only $\{X\}$ observed $\Rightarrow$ cannot maximize $p(X,Z)$ directly, but have $p(Z|X,\theta)$ 

    - Introduce arbitrary $q(Z)$ and then sum over $Z,\text{ where $q(Z)$ is distribution} \small\Rightarrow \displaystyle \sum_Z q(Z)=1$ 

      $\Rightarrow$ $\displaystyle \sum_Z q(Z) \ln p(X,Z|\theta) = \sum_Z q(Z) \ln p(Z|X,\theta) + \ln p(X|\theta)$ 

      $\displaystyle \Rightarrow \ln p(X|\theta) = \underbrace{ \sum_Z q(Z)\ln \frac {p(X,Z|\theta)}{q(Z)} }_{\mathcal L (q,\theta)} + \underbrace{ (- \sum_Z q(Z)\ln \frac{p(Z|X,\theta)}{q(Z)}) }_{KL(q\|p)} ,$ 

      ​	$\space \text{ where } KL(q\|p) \text{ is Kullback-Leibler divergence}$ 

  - **Algorithm**:

    - $\text{E step:}$ fix $\theta^{\text{old}}$, maximize lower bound $\mathcal L(q,\theta^{\text{old}})$ 

      $\Rightarrow$ $\mathcal L(q,\theta^{\text{old}}) \text{ maximize when } KL(q\|p)=0 \small \text{ ( $\ln p(X|\theta)$ independent from $q(\cdot)$ ) }$ 

      $\displaystyle \Rightarrow q(Z) = p(Z|X,\theta^{\text{old}})$ 

      ​		$\text{where } p(Z|X,\theta^{\text{old}}) \text{ is posterior}$ 

    - $\text{M step:}$ fix $q(Z) = p(Z|X,\theta^{\text{old}})$, maximize lower bound $\mathcal L(q^{\text{old}},\theta)$ 

      $\begin{align} \Rightarrow \displaystyle \theta &= \arg \max_{\theta} \ln p(X|\theta) \\&= \arg \max_{\theta} \mathcal L(q^{\text{old}},\theta) \end{align}$  

      ​	$\text{taking gradient w.r.t. $\theta$ will contain } q(Z) = p(Z|X,\theta^{\text{old}})$ 

  - Convergence:

    - Start by initializing $\theta$ 

    - After $\text{M step}:$  $\mathcal L(q^{\text{old}},\theta) > \mathcal L(q^{\text{old}},\theta^{\text{old}}) \space \land \space \theta \neq \theta^{\text{old}}$ unless converged 

      $\Rightarrow KL(q\|p)>0 \\ \Rightarrow \text{E step}$ 

  - Understanding:

    - $q(\cdot):$ model to fit $\ln p(X|\theta)$ 

    - $\theta:$ model parameter for $\mathcal L(q,\theta)$ 

    - blue curve is one iteration before green curve

      ![general EM - exapmle](.\general EM - exapmle.PNG)  

#### Clustering - Naive K-means

1. Input:

   - $K$ (num of clusters)
   - Training set {$x^1,x^2,...,x^m$}, $x^i \in R^n$ (drop $x_0=1$ convention), $K<m$

2. Cost function:

   - $\displaystyle J(c^1,...,c^m,\mu_1,...,\mu_K) = \frac1m \sum^m_{i=1} ||x^i-\mu_{c^i}||^2$ 
     - $c^i= \text{index of cluster {1,2,...,K} assigned to } x^i \text{, K = num of cluster}$ 
     - $\mu_k = \text{cluster centroid }k \text{, $\mu_k \in R^n$}$ 
     - $\displaystyle \mu_{c^i} = \text{cluster centroid assigned to $x^i$}$ 
   - With $1\text{-of-}K$ coding scheme: $\displaystyle J=\frac 1 m \sum_{i=1}^m (\sum_{k=1}^K r^i_{k}\|x^i-\mu_k\|)$ 
     - $r^i= \text{1-of-K vector to denote cluster assigned to }x^i, r^i\in\{0,1\}^K $ 
     - $\mu_k = \text{cluster centroid }k \text{, $\mu_k \in R^n$}$ 

3. Goal:

   - Find $r^i,\mu_k$ to minimize $J$ 
     - Problem: cyclic dependence between $r^i,\mu_k \Rightarrow \text{EM algorithm}$ 

4. Algorithm:

   - Initialize K cluster centroid $\mu_1, \mu_2,...,\mu_K \in R^n$ 

   - Repeat

     - for $i=1$ to $m$ $\small \text {(cluster assignment - E step)}$

       ​	$c^i := \text{closest cluster centroid to } x^i$ 

     - for $k=1$ to $K$ $\small \text{(move centroid - M step)}$ 

       ​	$\mu_k := \text{mean of points assigned to cluster } k$ 

5. Initialization: $\small \text{(crucial for convergence)}$ 

   - **Randomly** pick $K$ **distinct** examples and set $\mu_1,...,\mu_K$ to them 
     - random algorithm may need to repeat multiple times to avoid local optimum

6. Understanding:

   - Cluster assignment: adjust $c^1,...,c^m$ to minimize $J$, holding $\mu_1,...,\mu_K$ 
   - Move centroid: adjust $\mu_1,...\mu_K$ to minimize $J$, holding $c^1,...,c^m$ 
   - $J$ is **not** possible to increase if algorithm is implemented correctly

7. Parameter $K$: 

   - Elbow method: plot $J-K$ graph:

     ![Elbow method](.\Elbow method.png)   

   - Consider downstream purpose

8. Possible Improvement:

   - Use triangle inequality $\Rightarrow$ Triangle K-means
   - Use Non-Euclidean distance $\Rightarrow$ Kernel K-means (K-medoids)
   - Build K-D tree

#### Mixture Models - Generative K-means

1. Gaussian Mixture Model Distribution:

   - $\displaystyle p(x) = \sum_{k=1}^K \pi_k \mathcal N (x|\mu_k,\Sigma_k)$ 
     - $\displaystyle \int p(x)dx = 1 \Rightarrow \sum_{k=1}^K\pi_k = 1$ 
     - Need to find $\pi,\mu,\Sigma$ 

2. Inroduce Latent Variable $\mathbf z$: 

   - $\mathbf z \in \{0,1\}^K: \text{1-of-K coding scheme to denote the distribution from which $x$ is drawn} $ 

   - Distribtion:

     - $p(\mathbf z_k = 1) = \pi_k$ 

       $\displaystyle \Rightarrow p(\mathbf z) = \prod_{k=1}^K \pi_k^{z_k}$ 

     - $p(x|\mathbf z_k=1) = \mathcal N(x|\mu_k,\Sigma_k)$ 

       $\displaystyle \Rightarrow p(x|\mathbf z) = \prod_{k=1}^K\mathcal N(x|\mu_k, \Sigma_k)^{z_k}$ 

     - $\displaystyle p(x,\mathbf z) = \prod_{k=1}^K [\pi_k \mathcal N(x|\mu_k,\Sigma_k)]^{z_k}$ 

     - $\displaystyle p(z_k=1|x) = \frac{\displaystyle p(z_k=1)p(x|z_k=1)}{\displaystyle\sum_{i=1}^Kp(z_i=1)p(x|z_i=1)} =\frac{\pi_k \mathcal N(x|\mu_k,\Sigma_k)}{\displaystyle \sum_{i=1}^K \pi_i \mathcal N(x|\mu_i,\Sigma_i)} $ 

       ​	denoted as $\gamma(z_k):$ responsibility of $z_k$ to explain the observed $x$ 

   - Recover $p(x)$: (to assert that we are on the right track)

     - $\displaystyle p(x) \!= \displaystyle \sum_{\mathbf z} p(\mathbf z)p(x|\mathbf z)$

       ​	$\displaystyle = \sum_{\mathbf z} \prod_{k=1}^K\pi_k^{z_k} \prod_{k=1}^K \mathcal N(x|\mu_k,\Sigma_k)^{z_k} \\ \displaystyle = \sum_{k=1}^K \pi_k \mathcal N(x|\mu_k, \Sigma_k)$ 

   - $N$ data drawn $\text{i.i.d.}$ 

     ![Mixture of Gaussians - N iid data](.\Mixture of Gaussians - N iid data.PNG) 

3. Likelihood:

   - $\displaystyle \ln p(X|\pi,\mu,\Sigma) = \sum_{n=1}^N \ln[\sum_{k=1}^K \pi_k \mathcal N(x|\mu_k,\Sigma_k)]$  
     - $\displaystyle P(X) = \sum_Z P(X,Z) \text{, where full combination } \small \sum_{Z}\prod_{\text{each combination of }z_{1-n}} = \prod_{X}\sum_{\text{all possibility of each }x}$ 
   - Goal: 
     - Find $\pi,\mu,\Sigma$ that maximize ($\ln$) likelihood
   - Problem:
     - $\ln$ does **not** apply on $\mathcal N \Rightarrow$ does **not** have neat direct math solution
     - $\Rightarrow$ apply $\text{EM}$ 

4. EM algorithm:

   - Critical points:

     - $\displaystyle \frac {\partial}{\partial \mu_k} \ln p = 0 \Rightarrow 0 = \sum_{n=1}^N   \underbrace{ \frac {\pi_k\mathcal N(x_n|\mu_k,\Sigma_k)}{ \sum_{i=1}^K \pi_i\mathcal N(x_n|\mu_i,\Sigma_i)} }_{\gamma(z_k)} \Sigma^{-1}(x_n-\mu_k)$ 

       $\displaystyle \Rightarrow \mu_k = \frac 1 {N_k} \sum_{n=1}^N \gamma(z_k)x_n$ 

     - $\displaystyle \frac {\partial}{\partial \Sigma_k}\ln p = 0$

       $\displaystyle \Rightarrow \Sigma_k = \frac 1 {N_k} \sum_{n=1}^N \gamma(z_k) (x_n-\mu_k)(x_n-\mu_k)^T$  

     - $\displaystyle \frac \partial {\partial \pi_k} \ln p = 0$ 

       $\displaystyle \Rightarrow \pi_k=\frac {N_k} N$ 

       ​	$\small \text{where $N_k$ is the effective num of data point assigned to $k$ : $N_k=\sum_{n=1}^N \gamma(z_k)$}$ 

   - $\text{E step}$: evaluate $q(Z) = \gamma(z_k)$ using current parameters $\mu,\Sigma,\pi$ $\space\small\text{ (soft assign point to Gaussian)}$ 

     - $\displaystyle \gamma(z_k) = \frac {\pi_k\mathcal N(x|\mu_k,\Sigma_k)} {\displaystyle \sum_{i=1}^K \pi_i\mathcal N(x|\mu_i,\Sigma_i)}$ 

   - $\text{M step}$: re-estimate parametes $\theta = \{\mu,\Sigma,\pi\}$ using current $\gamma(z_k)$ $\space\small\text{ (maximize likelihood)}$ 

     - $\displaystyle \mu_k^{\text{new}} = \frac 1 {N_k} \sum_{n=1}^N\gamma(z_{nk})x_n$ 
     - $\displaystyle \Sigma_k^{\text{new}} = \frac 1 {N_k} \sum_{n=1}^N \gamma(z_{nk}) (x_n-\mu_k^{\text{new}})(x_n-\mu_k^{\text{new}})^T$ 
     - $\displaystyle \pi_k^{\text{new}} = \frac {N_k} N$ 

5. Relation to naive K-means:

   - Setting $\Sigma_k=\epsilon,\epsilon \rightarrow 0$ 
     - $\gamma(z_{nk})$ with smallest $\|x_n-\mu_k\|^2$ goes to $0$  most slowly 
     - $\displaystyle \Rightarrow \gamma(z_{nk}) = \begin{cases} 1 & \text{if } \|x_n-\mu_k\| < \|x_n-\mu_i\| \forall i\neq k \\ 0 & \text{otherwise} \end{cases}$ 
   - Reduce to naive K-means when $\Sigma_k=\epsilon,\epsilon \rightarrow 0$ 
     - $\displaystyle \lim_{\epsilon \rightarrow 0} \gamma (z_{nk}) = r_{nk} \space \Rightarrow$ becomes hard assignment

6. Broad view:

   - Generalized to other distribution:
     - Mixture of Bernoulli Distributions
     - ...
   - Reducing to Naive K-means:
     - $\gamma(z_{nk}) = \begin{cases} 1 & \text{if }\|x_n-\mu_k\| < \|x_n-\mu_i\| & \forall i\neq k \\ 0 & \text{otherwise} \end{cases}$ 