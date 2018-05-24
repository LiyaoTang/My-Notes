# Reinforcement Learning

### Overall Structure

![Overall Structure](F:\Reinforcement Learning\Overall Structure.jpg) 



### Q-Learning

- Basic Notation

  - state $s \in \mathcal S$ 
  - action $a \in \mathcal A$ 
  - reward $r \in \mathcal R$ 

- Reward $R_t$ 

  - reward gained at time $t$ 

- Return $G_t$ 

  - specific function for a reward sequence after time-step $t$ 
  - $\displaystyle G_t = R_{t+1} + R_{t+2} + ....$ 
    - episodic tasks: $\displaystyle G_t = \sum_{k=0}^{T-t} R_{t+1+k}, \text{ where $T$ coresponds to terminal state}$ 
    - episodic tasks: $\displaystyle G_t = \sum_{k=0}^\infty \gamma^k R_{t+1+k}, \text{ where $\gamma\in[0,1]$ is discounted rate}$ 

- Policy $\pi$ 

  - mapping from pair of state $s\in \mathcal S$ and action $a\in\mathcal A(s)$ to a probability $\pi (a|s)$ of taking $a$ when in $s$ 

- Value Function  

  - $v_\pi (s)$: expected return starting from state $s$ and following policy $\pi$ thereafter

    - $\displaystyle v_\pi(s) = {\mathbb E}_\pi [G_t|S_t=s]$ 

    - **Bellman Equation** - using Markov Decision Process 

      $ \displaystyle \begin{align} v_\pi(s) &= {\mathbb E}_\pi [G_t|S_t=s] \\ \displaystyle &= {\mathbb E}_\pi\left[ \sum_{k=0}^\infty \gamma^k R_{t+1+k} \bigg| S_t=s \right] \\ &= \displaystyle {\mathbb E}_\pi \left[R_{t+1} + \gamma \sum_{k=0}^\infty \gamma^k R_{t+2+k} \bigg|S_t=s \right] \\ &= \displaystyle \sum_{a\in \mathcal A(s)} \pi(a|s) \sum_{s'}\sum_r p(s',r|s,a) \left[ r + \gamma \mathbb E\left[ \sum_{k=0}^\infty \gamma^k R_{t+2+k} \big | S_{t+1} = s' \right] \right] \\ &\boldsymbol= \displaystyle \sum_a \pi(a|s) \sum_{s',r} p(s', r|s,a) [r + \gamma v_\pi(s')] \end{align}$ 

  - $q_\pi(s, a)$: expected return of taking action $a\in A(s)$ at state $s$ and following policy $\pi$ thereafter

    - $q_\pi (s,a) ={ \mathbb E }_\pi [G_t|S_t=s, A_t=a]$ 

    - **Bellman Equation** - using Markov Decision Process

      $\begin{align} \displaystyle q_\pi(s,a) &= {\mathbb E}_\pi [G_t | S_t=s,A_t=a] \\ &= \displaystyle \mathbb E_\pi \left[ \sum_{k=0}^\infty \gamma^k R_{t+1+k} \bigg| S_t=s, A_t=a \right] \\ &= \displaystyle {\mathbb E}_\pi \left[R_{t+1} + \gamma \sum_{k=0}^\infty \gamma^k R_{t+2+k} \bigg|S_t=s, A_t=a \right]  \\ &= \displaystyle \sum_{s',r} p (s', r|s, a) \left[ r + \gamma {\mathbb E}_\pi \left[ \sum^\infty_{k=0} \gamma^k R_{t+2+k} \bigg | S_{t+1} = s' \right] \right] \\ &= \displaystyle \sum_{s',r} p(s',r|s,a)[r + \gamma v_\pi(s')] \end{align}$ 

      $\begin{align} \displaystyle q_\pi(s,a) &= {\mathbb E}_\pi [G_t | S_t=s,A_t=a] \\ &= \displaystyle \mathbb E_\pi \left[ \sum_{k=0}^\infty \gamma^k R_{t+1+k} \bigg| S_t=s, A_t=a \right] \\ &= \displaystyle {\mathbb E}_\pi \left[R_{t+1} + \gamma \sum_{k=0}^\infty \gamma^k R_{t+2+k} \bigg|S_t=s, A_t=a \right] \\ &= \displaystyle \sum_{s',r} p (s', r|s, a) \left[ r + \gamma {\mathbb E}_\pi \left[ \sum^\infty_{k=0} \gamma^k R_{t+2+k} \bigg | S_{t+1} = s' \right] \right]  \\ &= \displaystyle \sum_{s',r} p (s', r|s, a) \left[ r + \gamma \sum_{a'\in \mathcal A(s')} \pi (a'|s') {\mathbb E}_\pi \left[ \sum^\infty_{k=0} \gamma^k R_{t+2+k} \bigg | S_{t+1} = s' \right] \right]  \\ &= \displaystyle \sum_{s',r} p(s',r|s,a) \left[r + \gamma \sum_{a'\in \mathcal A(s')} \pi (a'|s') q_\pi (s',a') \right] \end{align}$ 

  - $\displaystyle \boldsymbol \Rightarrow v_\pi(s) = \sum_a \pi(a|s) q_\pi(s,a)$ 


### Policy Gradient

- Overall Structure:

  - Forward:

    - input $\rightarrow$ probability distribution over actions

  - Backward:

    - use forward process to sample instance (game process from the beginning to the end)
    - compute & record gradient along the way, with advantage parameter $A_i$ for $i^\text{th}$ game
    - when all games in current batch ends, thus all $A_i$ known, update the NN

    ![policy gradient structure](F:\Reinforcement Learning\policy gradient structure.png) 

- Loss Function:

  - General form

    - $\displaystyle E_{x\sim p(x|\theta)}[f(x)],$ 

      expectation of score function $f(x)$ under distribution $p(x|\theta)$ parameterized by $\theta$ 

  - Derive gradient

    $\begin{align} \displaystyle \nabla_\theta E_{p(x|\theta)}[f(x)] &= \nabla_\theta \sum_x p(x|\theta)f(x) \\ &= \sum_x \nabla_\theta p(x|\theta)f(x) \\ &= \sum_x p(x|\theta) \frac {\nabla_\theta p(x|\theta)} {p(x|\theta)} f(x) \\ &= \sum_x p(x|\theta) \nabla_\theta \log[ p(x|\theta) ] f(x) && \text{using } \nabla_\theta log(z) = \frac 1 z \nabla_\theta z \\ &= E_x [ f(x) \nabla_\theta log(p|\theta)] \end{align}$ 

- ​


























