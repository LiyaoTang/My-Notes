# Neural Network

## Background

- Driver of Deep Learning
  - Scale
    - the scale of data (labeled)
    - the scale of neural network (interpretability)
    - the possible scale of computation: e.g. ReLu, faster parallel algorithm...
- Data Category
  - Structured
    - well-defined meaning for each feature
    - e.g. csv file, database...
  - Unstructured
    - input meaning & form undefined
    - e.g. image, text (word vs. character), audio, ...

### Basic Neural Networks

1. Goal:

   - Learn basis function(s) rather than manually choose
     - Generalized Linear Model: $\displaystyle h_\theta(x)=f(\phi(x)\theta), \text{ where $\phi$ is basis function, f($\cdot$) is activation function}$  
     - Let $\phi(x)$ depend on parameters, and then adjust parameters together with $\theta$ 
   - Complex non-linear hypotheses
     - hard to train: too many features with their polynomial terms (computer vision)

2. Problem in Neural Networks:

   - Weight-space Symmetries ($n$ units in one hiden layer)

     - Occure when activation function for hidden units has symmetries $\Rightarrow$ $\mathcal O (2^n)$ 

       $\small \text{Ex. $\arctan(-a)=-\arctan(a)$: changing sign of all input & output units has the same mapping}$ 

     - In one hidden layer, exchange unit with each other (along with their inout & output weights).

       Mapping stay the same $\Rightarrow \mathcal O(n!)$ 

     - $\Rightarrow \mathcal O(n!2^n)$ overall weight-space symmetries

   - Error function not convex

     - At least $\mathcal O(n!2^n)$ critical points ( $\small\nabla E(w)=0, \text{ where $E(w)$ is Error function}$) 

     - $\Rightarrow$ Can be expensive in finding even local minimum with Gradient Descent

       ​	$\mathcal O(n^3)$, using Laplace Approximation

   - Deep networks result in gradient vanishing 

     - But functions that can be compactly represented by a depth k
       architecture might require an exponential number of
       computational nodes using a depth k-1 architecture
     - Potential solution: Xavier initialization, ReLU Activations $h(x)=\max(x,0)$ 

3. Representation:

   - input layer (layer $1$), hidden layer(s), output layer
     - each layer has $x_0=1$ as bias unit $\small\text{(except for output layer)}$
   - $s_l$ = num of units in layer $l$ 
   - $z_i^j$ = output of unit $i$ in layer $j$  ($\text{represents parametrised basis}$) 
   - $\Theta^j$ = matrix of weights controlling function mapping from layer $j$ to layer $j+1$ 
     - with $s_j$units in layer $j$ and $s_{j+1}$ units in layer $j+1$, $\Theta^j$ is of dimension $s_{j+1}\times(s_j+1)$ 

4. Forward propagation:

   - Activation $a^{j+1} = \Theta^j \cdot [z_0^j,z_1^j,...,z_{s_j}^j]^T, \space z_0^j = 1$ 
   - Activation function $h(\cdot)$ to get output of a hidden units
     - Need to be nonlinear
     - **ReLU activation**: $h(x) = \max(x,0) \Rightarrow$ help avoiding saturation (gradient vanishing)
   - $z^{j+1} = h(a^{j+1})=[z_1^{j+1},z_2^{j+1},...,z_{s_{j+1}}^{j+1}]^T$ 

5. **Backward propagation**:

   - $\displaystyle J(\Theta)=-\frac{1}{m}\sum^m_{i=1}\sum^K_{k=1}[ y_k^i log((h_\Theta(x^i))_k) + (1-y_k^i)log(1-(h_\Theta(x^i))_k)] + \frac{\lambda}{2m}\sum_{l=1}^{L-1}\sum_{i=1}^{s_{l+1}}\sum_{j=1}^{s_l}(\Theta_{i,j}^l)^2$ 

     - sum the cost for all k categories
     - $s_l$: num of units in $l$th layer; $\Theta^l$: $s_{l+1}\times(s_i+1)$ => in $\Theta_{i,j},\space i\neq0$ 
     - can introduce regularised error $\widetilde J = J(\Theta)+\frac \lambda 2 \Theta^T\Theta$ 

   - $\displaystyle \frac{\partial J(\Theta)}{\partial \Theta_{i,j}^l} = \frac{\partial J(\Theta)}{\partial a^{l+1}_i}\frac{\partial a^{l+1}_i}{\partial\Theta^l_{i,j}} = \delta_i^{l+1}z_j^l$ , $\text{where } \delta_i^l=\frac{\partial J(\Theta)}{\partial a_i^l}$ 

     - $\displaystyle \delta_i^{l+1} = h'(a_i^{l+1}) \sum_{k=1}^{s_{l+1}} \delta_k^{l+2}\Theta_{ki}^{l+1}$ 
     - Intuition: $\text{Gradient$=$output error $\delta \space \times$ input associatd with $\Theta_{i,j}$}$ 

   - Efficiency: $\mathcal O(n), \text{where $n$ is the num of parameters in all $\Theta$}$ 

     - whereas numerical differentiation ($\small \frac{J(\Theta_{i,j}+\epsilon) - J(\Theta_{i,j}-\epsilon)}{2\epsilon}$) need $\mathcal O(n^2)$ 

   - **Algorithm**:

     - $\forall l,i,j,\space \Delta^l_{ij}:=0$ 

     - For $i=1$ to $m$ 

       ​	$a^1:=x^i$,  compute $a^l,\space l=2,...,L$ (forward)

       ​	$\delta^L:=a^L-y^i$, compute $\delta^{L-1},...,\delta^2$ (backward,  $a,y^i$ are column vectors )

       ​	$\Delta^l_{ij} += a_j^l\delta_i^{l+1}$ (vectorization: $\Delta^l+=\delta^{l+1}(a^l)^T$)

       ​	$\begin{equation} D_{i,j}^l=   \begin{cases} \frac1m(\Delta_{i,j}^l + \lambda\Theta_{i,j}^l) & j\neq0, \\  \frac1m(\Delta_{i,j}^l & j=0. \end{cases}  \end{equation}$ 

     - $D_{i,j}^l=\frac{\partial J(\Theta)}{\partial \Theta^l_{i,j}}$ 

6. Initialization: 

   - Symmetry problem:![Symmetry Problem .png](.\Symmetry Problem .png)

     - so that units in the same layer are computing the same features

   - Random initialization: $\Theta_{i,j}^l := random[-\epsilon, \epsilon]$

     - Default choice: $\displaystyle \epsilon=\frac{\sqrt{6}}{\sqrt{s_l + s_{l+1}}}$, $s_l$ is num of units in layer $l$ 

     - Matlab/Octave: $\Theta = rand(m, n) * 2\epsilon - \epsilon$

       ​	$rand(m, n)$ => m\*n matrix with randome num between 0,1

   - **Xavier Initialization**:

     - Avoid saturation $\text{ (gradient vanishing)}$ 

     - For each layer, assume: Input $x_i \sim \mathcal N(0,1)$, weights $w_i\sim \mathcal N(0,\sigma^2)$ and activation $a=\sum x_iw_i$ 

       $\Rightarrow Var(a)=\mathbb E(a-\mathbb E(a))^2=\mathbb E(a^2)=\sum\mathbb E ((x_iw_i)^2)= \sum\mathbb E(x_i^2)\mathbb E(w_i^2)=m\sigma^2 \\ \Rightarrow \text{ Set }\sigma^2 = \frac 1 m \\ \text{, where } m \text{ is total num of input }x_i$ 

7. Training

   - Gradient checking: $D_{i,j} \approx \frac{J(\Theta_{i,j}+\epsilon) - J(\Theta_{i,j}-\epsilon)}{2\epsilon}$ 

     - turn checking off when running the backpropagation algorithm

   - Healthy update process:

     - update scale = learning rate * gradient

       $ => \frac {\text{update scale}} {\text{parameter scale}} \approx 0.001$  

8. Network architecture

   - Num of input units: dimension of eatures in $x^i$
   - Num of output units: num of classes (in Classification)
   - Num of hidden layer:
     - default: 1
     - if >1: same num of units in each hidden layer
   - Num of units in layer: comparable to num of layer

9. Pre-train Network:

   - Use pre-trained CNN convolution layers as features selection & fine tune the output layer
     - work well when data is small

### Bayesian Neural Networks

1. Assumption:

   - $\text{Conditional distribution: } p(t|x,w) = \mathcal N(t|h(x,w),\beta^{-1})$ 
   - $\text{Prior: } p(w)=\mathcal N (w|0,\alpha^{-1}I)$ 

2. Likelihood: 

   - $\displaystyle p(\boldsymbol t|w) = \prod_{n=1}^N \mathcal N(t_n|h(x_n,w), \beta^{-1})$ 

3. Posterior:

   - $\displaystyle p(w|\boldsymbol t) \propto p(\boldsymbol t|w) p(w)$, Non-Guassian because of non-linear $h(x_n,w)$

   - Apply Laplace Approximation:

     $\displaystyle \Rightarrow$ find $w_{MAP}$ for $\ln p(w|\boldsymbol t)=-\frac \beta 2 \sum_{n=1}^N (h(x,w)-t_n)^2 - \frac \alpha 2 w^Tw + \text{const}$, 

     ​	* $w_{MAP}$ is local maximal due to non-convex $\ln p(x|\boldsymbol t)$, which due to non-linear $h(x,w)$ 

     $\Rightarrow \boldsymbol A = - \nabla\nabla \ln p(w|\boldsymbol t) =\alpha I + \beta \boldsymbol H$, 

     ​	where $\boldsymbol H$ is Hessian matrix of sum-of-square error function with respect to $w$ 

   - $p(w|\boldsymbol t) \simeq \mathcal N(w|w_{MAP}, \boldsymbol A^{-1})$ 

4. Predictive Distribution:

   - $\displaystyle p(t|x,\boldsymbol t) = \int p(t|x,w)p(w|\boldsymbol t) dw$, still analytically intractable due to non-linear $h(\cdot)$ in $p(t|x,w)$ 

   - Taylor Expansion: $h(x,w) \simeq h(x,w_{MAP}) + \boldsymbol g^T(w-w_{MAP})$, where $\boldsymbol g=\nabla h(x,w)|_{w=w_{MAP}}$ 

     $\Rightarrow \text{Conditional distribution: }p(t|x,w) \simeq \mathcal N(t|h(x,w_{MAP})+\boldsymbol g^T(w-w_{MAP}), \beta^{-1})$ 

   - $p(t|x,\boldsymbol t) = \mathcal N(t|h(x,w_{MAP}),\sigma^2(x)), \text{ where } \sigma^2(x)=\beta^{-1}+\boldsymbol{g}^T\boldsymbol A^{-1}\boldsymbol g$ 

#### Autoencoder - Neural Network

1. Problem:

   - Functions that can be compactly represented by a depth k architecture might require an exponential number of computational nodes using a depth k-1 architecture
     - $\Rightarrow$ deep network
   - Problem in Deep network:
     - More local minimum, plateaus and saddle points $\Rightarrow$ hard to generalize well

2. Goal: 

   - Unsupervised pre-training to find distributed representation
   - Find good lossy compression of data

3. Undercomplete Architecture:

   - Input: data $X$ 

   - Target output: $X$ (input itself)

   - Reconstruction error: $\displaystyle J=\sum_{n=1}^N \ell (x^n,f(c(x^n)))  $ 

     - $c(x) \text{ is encoder; }f(x) \text{ is decoder; } f(c(x)) \text{ is reconstruction from network}$ 

     - Minimize negative log likelihood: $-\ln P(x|c(x))$ 

       $\text{if } P(x|c(x)) \text{ Guassian} \Rightarrow \ell \text{ is squared error} \\ \text{if } x_i \text{ obey binomial} \Rightarrow -\ln P(x|c(x)=-x_i\ln f_i(c(x))+(1-x_i)\ln(1-f_i(c(x))\space)$ 

       ​	$\small\text{$f_i$ is the $i$-th component of decoder}$ 

   - Undercomplete: 

     - Small num of hidden units $\Rightarrow c(x)$ as a lossy compression of $x$ 

   - Stacking Encoders:

     - ![Stacking autoencoder](.\Stacking autoencoder.png) 

     - chain non-linear encoder $c_i(x)$ 

       ​	$\small\text{Compared to PCA: chain linear transformations $\Leftrightarrow$ single linear transformation}$ 

     - Discard decoding layer $\Rightarrow$ use $z_i=c_i(\cdots c_1(x)\cdots)$ as features for subsequent learning

4. Higher Dimensional Hidden Layer:

   - Reason:
     - Want many features wider than input
   - Constraint to force compression: $\small\text{(avoid learning identity function - "perfect reconstruction")}$ 
     - Regularization
     - Early stopping of stochastic gradient descent
     - Adding noise into input
     - Sparsity constraint on encoding $c(x)$ 

5. Denoising Autoencoder:

   - Idea: 
     - Add noise to input & noise free data as target output 
   - Goal:
     - preserve information of input
     - undo corruption process
   - Reconstruction likelihood: 
     - $P(x|c(\hat x)), \text{ where $x$ noise free, $\hat x$ corrupted}$ 

6. Sparse Representations

   - Idea: 

     - Some basis functions for a partcular $x$ may be of minor variation

       $\Rightarrow$ many hidden nodes (basis functions collected in $\Phi$) but a few active for a given code $c(x)$ 

       ​	$\small\text{use few element from $\Phi$ to represent $x$}$ 

   - $\ell_0$ Norm Penalty:

     - $\displaystyle \min_{w} = \sum_{n=1}^N \frac 1 2 \|x^n-\Phi w^n\|_2^2+\lambda\|w\|_0 $ 

     - Sparse: small num of non-zero $w_i$ $\text{($\|w\|_0$ is small for $x\approx \Phi w$)}$ 

     - Problem: $\|w\|_0$ non-convex

       ​	$\Rightarrow$ relax to $\|w\|_1$  

   - $\ell_1$ Norm Penalty:

     - $\displaystyle \min_{w} = \sum_{n=1}^N \frac 1 2 \|x^n-\Phi w^n\|_2^2+\lambda\|w\|_1 $ 

     - Let $K=\Phi\Phi^T$ be Gram matrix over $\Phi \small \text{, ($\Phi$ normalized)}$ 

       ​	$\displaystyle\text{Mutual Coherence: } M = M(\Phi)=\max_{i\neq j}|K_{i,j}| \\ \Rightarrow \text{similar rows, then }M\approx 1$ 

     - Therom:

       $\text{For $w^\star$ minimize $\ell_0$ problem }$ 

       ​	$\text{If $\|w^\star\|_0 < \frac 1 M \Rightarrow w^\star$ is the unique sparsest solution} $ 

       ​	$\text{If $\|w^\star\|_0 < \frac 1 2(1+\frac 1M) \Rightarrow $ minimizer of $\ell_1$ problem has the same sparsity pattern as $w^\star$} $ 

       ​							$\small \text{ (has 0 in the same places)}$ 

   - Probabilistic View of $\ell_1$:

     - Maximize Joint distribution: $p(x,w)=p(w)p(x|w),$  $\small\text{ gives rise to } \|\cdot\|_2, \text{where } w \text{ can be considered as latent variable}$ 
     - Laplace Prior: $p(w_i)=\frac \lambda 2 e^{-\lambda |w_i|},$ $\small \text{ gives rise to $\ell_1$ regularization}$ 

### Regularization

1. **L2 regularization**  

   - Description:
     -  Penalizing the squared magnitude of all parameters directly in the objective. That is, for every weight $w$ in the network: add the term $\frac 12 \lambda w^2$ to the objective, where $\lambda$ is the regularization strength. It is common to see the factor of $\frac 12$ in front because then the gradient of this term with respect to the parameter $w$ is simply $\lambda w$ instead of $2 \lambda w$. 
   - Interpretation: 
     - heavily penalizing peaky weight vectors and preferring diffuse weight vectors. 
     - encouraging the network to use all of its inputs a little rather that some of its inputs a lot, due to multiplicative interactions between weights and inputs this has the appealing property of 
     - every weight is decayed linearly: `W += -lambda * W` towards zero during gradient descent parameter update

2. **L1 regularization** is another relatively common form of regularization, where for each weight ww we add the term λ∣w∣λ∣w∣ to the objective. It is possible to combine the L1 regularization with the L2 regularization: λ1∣w∣+λ2w2λ1∣w∣+λ2w2(this is called [Elastic net regularization](http://web.stanford.edu/~hastie/Papers/B67.2%20%282005%29%20301-320%20Zou%20&%20Hastie.pdf)). The L1 regularization has the intriguing property that it leads the weight vectors to become sparse during optimization (i.e. very close to exactly zero). In other words, neurons with L1 regularization end up using only a sparse subset of their most important inputs and become nearly invariant to the “noisy” inputs. In comparison, final weight vectors from L2 regularization are usually diffuse, small numbers. In practice, if you are not concerned with explicit feature selection, L2 regularization can be expected to give superior performance over L1.

3. **Max norm constraints**. Another form of regularization is to enforce an absolute upper bound on the magnitude of the weight vector for every neuron and use projected gradient descent to enforce the constraint. In practice, this corresponds to performing the parameter update as normal, and then enforcing the constraint by clamping the weight vector w⃗ w→ of every neuron to satisfy ∥w⃗ ∥2<c‖w→‖2<c. Typical values of cc are on orders of 3 or 4. Some people report improvements when using this form of regularization. One of its appealing properties is that network cannot “explode” even when the learning rates are set too high because the updates are always bounded.

4. **Dropout** is an extremely effective, simple and recently introduced regularization technique by Srivastava et al. in [Dropout: A Simple Way to Prevent Neural Networks from Overfitting](http://www.cs.toronto.edu/~rsalakhu/papers/srivastava14a.pdf) (pdf) that complements the other methods (L1, L2, maxnorm). While training, dropout is implemented by only keeping a neuron active with some probability pp (a hyperparameter), or setting it to zero otherwise.

   ![img](./regularization-dropout.jpeg) 

   Understanding:

   1. 训练多个子网络，(视dropout之后的网络为子网络)，通过对网络输出取平均值(期望值)来抵消不同的过拟合。
      - 在预测时，只需要输出一个子网络的值 => 原文提议对dropout之后的神经元激活值进行rescale (对期望值进行估计)，实际上只需要关掉dropout 即可得到一个更好的 期望的估计值
   2. 使网络更robust，因为网络必须克服神经元丢失的现象，所以会避免过度依赖于输入的某些单一特征，然后可以学习到更稳定的特征。
      - 预测时，应当考虑每个已获得的 robust feature $\Rightarrow$ 关掉 dropout




### Conv Layer

1. Convolution as function:
   - 1-D:
     - $\displaystyle s(x) = (f * g)(x) = \sum_{x=-\infty}^\infty f(x)g(t-x)$ 
   - 2-D:
     - $\displaystyle S(i,j)= (I*K)(i,j) = \sum_m\sum_n I(m,n)K(i-m, j-n)$ 
     - $\displaystyle S(i,j)= (K*I)(i,j) = \sum_m\sum_n I(i-m, j-n)K(m,n), \text{ because of }$

2. Convolution demonstrated in image:

   - 1D convolution with stride 2

     - grey cell => padded 0; green cell => activated weights in filter

     ![Convolution in 1d](.\Convolution in 1d.PNG)  

3. Upsampling:

   - A specific case of **Resampling**:
     - re-construct the original continuous signal
     - sample again from the re-constructed signal
   - In Conv case => Deconvolution:
     - transposed conv
     - fractional conv

4. Transposed Conv vs. Fractional Conv

   - 

     ![transposed conv vs frationally strided conv ](.\transposed conv vs frationally strided conv .PNG) 

     - (a) Transposed Conv $\Leftrightarrow$ Backward Conv: 

       ​	grey in $y$ : cropping (previous padding becomes cropping in upsampling)

       ​	matrix is just the transposed matrix of a downsampling conv (**transposed kernel**)

       ​	same as the backward operation of a downsampling conv

       ​		(can always been seen as a backword process of *some* conv)

       ​		(**Note**: <u>practically, implemented as swapping back/forward-prop of another conv</u>)

     - (b) Fractional Conv $\Leftrightarrow$ Sub-pixel Conv: 

       ​	imaginary sub-pixel (inserted as 0) between each pixel

   -  Arithmic in Deconvolution:

      -  The simplest way: given input of deconv, think it as output of a conv from a same-size kernel

      -  A conv with stride $s=1$, padding $p=0$ and kernel size $k$ has an equivalent deconv, in the form of conv, with stride $s' = s$, kernel size $k'=k$ and padding $p'=k-1$ (padding at each side)

         (a full-padding conv with unit stride becomes deconv of a no padding conv)

         $\Rightarrow$ <u>In general, can use another conv as deoncv of a given conv</u> 

         ​	<u>while mentaining the mapping relation between orignal conv and corresponding deconv</u> 

      - A conv with strde $s>1$ will have a fractional conv as deconv, when mentaining the mapping relation

   -  Equivalence within Deconvolution:

      -  Only difference are the indices of weights used when contributing to $y$ from $x$ 

         $\Rightarrow$ reshape (re-order) the output will get to each other (swapping the odd and even pairs)

5. Convolution vs. Deconvolution

   - Actually can be **equivalent** !

     - $r^d$ channels ouput from conv can represent $1$ deconv output,

       ​	$\text{where } d \text{ is spatial dimension of data (usually 2 in inage), } r \text{ is upsampling rate}$ 

   - Deconvolution in 2D:

     ![decovolutin (fractional conv)](.\decovolutin (fractional conv).PNG) 

     (not all padding is shown => 2 rows of 0 padding on the top-left are hidden)

     - weights are divided into classes, e.g. purple, blue, green and red.
     - weights within each class are activated at the same time
     - weights between classes will NOT activated at the same time $\Rightarrow$ independent
     - The output are coloured into classes according to the class of activated weights

   - Convolution in 2D, with more filters:

     ![Convolution equivelant to deconv](.\Convolution equivelant to deconv.PNG)  

     (all padding is shown)

     - explicitly break weights into independent filters according to their classes
     - Convolve filters on the original image 
     - Each output map contains the result contributed by each weights class
     - Re-order (periodic shuffling) the result into the deconvolution result

   - Benefit & Loss:

     - Using convolution to represent deconvolution will have more parameters

       $\Rightarrow$ more representation power, harder to train

6. Convolution for Sparse Input

   - Notation

     - $f:$ feature vector
     - $w:$ weight vector
     - $l:$ window

   - Inverted Convolution

     - original: for each window $l$, collect result of $f \cdot w$ at each location within $l$ 

     - inverted: for each input $f$, calculate its contribution $f\cdot w_l$ for each possible window $l$  

       (where $w_l$ differ from window to window, as the relative location within $l$ changes)

     $\Rightarrow$ original: $\mathcal O(D_xD_y k_xk_y|f|)$; inverted: $\mathcal O(nk_xk_y|f|)$,

     ​	where $D_x,D_y$ are input dimension; $k_x,k_y$ kernel dimension; $n$ the number of non-zero input

### Point Cloud

1. Dataset Feature
   - Points
     - a set of point with location (usually x,y,z) and some other features

       $\Rightarrow$ orderless 

     - usually generated from radar / ladar scanning

       $\Rightarrow$ can be noisy 

   - Voxelization

     - apply grids on point cloud $\Rightarrow$ 3D grids
     - cell may contain more than one point $\Rightarrow$ feature learning
     - whole space can usually be large $\Rightarrow$ sparse grid with few occupied cell

2. Object Detection

   - Point Net

     - notation

       1. $N:$ number of points
       2. $D:$ dimension of vector for each point
       3. $f:$ function of the neural net

     - design

       1. orderless set point cloud 

          $\Rightarrow$ invariant to permutation of input order: $f(x_1,...,x_N) = f(x_{\pi_1},...,x_{\pi_N}), x_i\in\mathbb R^{D}$  

          $\Rightarrow$ restriction of neural net: can and only can represent the symmetric function

          ​	noting that $f(x_1,...,x_n) = \gamma \circ g(h(x_1),...,h(x_N))$ is symmetric if $g$ symmetric

          $\Rightarrow h:$ feature learning; $g:$ symmetric operation; $\gamma:$ not restricted (subnet for output)   

          ​	(such structure can and only can represent symmetric function) 

       2. invariance to geometric transformation

          $\Rightarrow$ analogue to <u>spatial transformer network</u> 

          $\Rightarrow$ transformer is only a matrix multiplication in point cloud (no interpolation needed)

          $\Rightarrow$ restrict the transformer close to orthogonal (avoid suboptimal) 

     - architecture

       1. input: row of vectors for a set of points

       2. transformer: analogue to spatial transformer network

       3. mapping to higher (64-D) dimension: 1x1 conv

       4. 2$^{nd}$ transformer

       5. mapping to higher (1024-D) dimension

       6. $g$ (symmetric operation): max pooling for each index on feature vector

          $\Rightarrow$ global feature

       7. k-class classification: fully connected sub-net to output

          segmentation: concatenated to local feature vector for points

          ​	$\Rightarrow$ shared 1x1 conv to output per-point classification 

     - analysis

       1. robust to  missing data: 

          critical points (contributors to global feature) capture contour and skeleton of objects

          upper-bound points: some points outside the object can result in similar global feature

   - Vote3Deep

     - design

       1. conv for sparse input at 3D space

       2. bounding box $\Leftrightarrow$ 3D conv kernel size at the output layer

          $\Rightarrow$ fine-tuning output layer for object of various size 

          ​	(after conv kernel in hidden layer trained)  

     - analysis

       1. shallow networks & simple features (including radar intensity) works

          $\Rightarrow$ spatial info matters

### Post Processing

1. CRF

   - Goal

     - remove improbable pattern 

       $\Rightarrow$ apply context-dependent rules

   - Procedure

     - similar to MRF; another layer before the output layer

       $\Rightarrow$ can be trained end-to-end

2. Non-Maximum Suppression

   - Goal

     - reduce duplicate / largely overlapped bounding box
   - Procedure
     - selecting window scored higher than a threshold (decision threshold)

     - candidates sorted in descending order; set of accepted window initialized to empty

     - taken candidate window one-by-one; compare with accepted windows

     - accepted the candidate if not overlapping with accepted window by more than a threshold

       (threshold usually is compared regarding ratio of intersection) 

### Training

1. Searching hyper-parameter:
   - Sampling hyper-parameter from uniform distribution over the parameters space
   - **Not** using fix steps
     - Usually some parameters have more influence on the performance => fix steps waste effort
2. Hard Negative Mining
   - Goal
     - improving precision; reducing false positive
     - overcome skewed dataset
   - Procedure
     - random sample out a initial training set (preferably balanced) from whole training set
     - after trained on initial training set, evaluate on the whole training set
     - add into training set the "hard negative" (false positive)