# Reinforcement Learning

## Learning Procedure

### Overall Structure

![Overall Structure](.\Overall Structure.jpg)  



### Q-Learning

- Overview

  - Goal
    - learns the utility of performing actions in states

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

  - a statistical decision making policy

    $\Rightarrow \pi (a|s)$ the probability of taking $a$ when in $s$, where state $s\in \mathcal S$ and action $a\in\mathcal A(s)$ 

- 

- Value Function  

  - $v_\pi (s)$: expected return starting from state $s$ and following policy $\pi$ thereafter

    - $\displaystyle v_\pi(s) = {\mathbb E}_\pi [G_t|S_t=s]$ 

    - **Bellman Equation** - using Markov Decision Process 

      $ \displaystyle \begin{align} v_\pi(s) &= {\mathbb E}_\pi [G_t|S_t=s] \\ \displaystyle &= {\mathbb E}_\pi\left[ \sum_{k=0}^\infty \gamma^k R_{t+1+k} \bigg| S_t=s \right] \\ &= \displaystyle {\mathbb E}_\pi \left[R_{t+1} + \gamma \sum_{k=0}^\infty \gamma^k R_{t+2+k} \bigg|S_t=s \right] \\ &= \displaystyle \sum_{a\in \mathcal A(s)} \pi(a|s) \sum_{s'}\sum_r p(s',r|s,a) \left[ r + \gamma \mathbb E\left[ \sum_{k=0}^\infty \gamma^k R_{t+2+k} \big | S_{t+1} = s' \right] \right] \\ &\boldsymbol= \displaystyle \sum_a \pi(a|s) \sum_{s',r} p(s', r|s,a) [r + \gamma v_\pi(s')] \end{align}$ 

  

  

  Q table

  - a matrix for $\mathcal S\times  \mathcal A$, with scores $q(s,a)$ the score for taking action $a$ when in state $s$
  - 

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

    ![policy gradient structure](.\policy gradient structure.png)  

- Loss Function:

  - General form

    - $\displaystyle E_{x\sim p(x|\theta)}[f(x)],$ 

      expectation of score function $f(x)$ under distribution $p(x|\theta)$ parameterized by $\theta$ 

  - Derive gradient

    $\begin{align} \displaystyle \nabla_\theta E_{p(x|\theta)}[f(x)] &= \nabla_\theta \sum_x p(x|\theta)f(x) \\ &= \sum_x \nabla_\theta p(x|\theta)f(x) \\ &= \sum_x p(x|\theta) \frac {\nabla_\theta p(x|\theta)} {p(x|\theta)} f(x) \\ &= \sum_x p(x|\theta) \nabla_\theta \log[ p(x|\theta) ] f(x) && \text{using } \nabla_\theta log(z) = \frac 1 z \nabla_\theta z \\ &= E_x [ f(x) \nabla_\theta log(p|\theta)] \end{align}$ 

### Temporal Difference Learning

- Overview
  - Goal
    - learns the utility of being in states

## Multi-Agent Learning

- Overview

  - Multi-Agent System

    - Multiple Agents
      1. asynchronous computation
      2. internal knowledge
      3. characterstics (control hierarchy, computability etc.)
    - Envirnment
      1. constraints
      2. feedback
    - Interaction
      1. between agents
      2. between agents and environment

  - Structure

    - team learning - a single learner discovering behaviours for the team

      1. homogeneous $\Rightarrow$ all agents assigned identical behaviour
      2. heterogeneous $\Rightarrow$ agents assigned differently, with speciality concerned
      3. hybrid $\Rightarrow$ hierarchy of sub-teams

      pros: focus on team outcome, easily adapted from single agent

      cons: ignore inter-agent credit assignment & interaction, combinatorial explosion in state space

    - **concurrent learning** - multiple learners

      1. different credit assignment methods
      2. approaches to dynamic environment and co-adaption
      3. ways to model others (interaction & collabration between agents)

      pros: reduction of search space (disjoint behviours in agents), more computability

- Credit Assignment

  - Global Reward

    - split the team reward equally $\Rightarrow$ 10same reward for everyone
    - cons:
      1. not sufficient feedback for agent's own specific behaviour
      2. global reward can somtimes be hard to compute

  - Local Reward

    - solely based on individual behaviour (not necessarily better than global reward)
    - pros: 
      1. discourages laziness
    - cons:
      1. greedy behaviour is encouraged

  - Social Reinforcement

    - observational reinforcement: obtained by observing and imitating other agents' behaviour
    - vicarious reinforcement: received when target agents are directly rewards 
    - pros:
      1. spread rare behaviour to others
      2. $\Rightarrow$ balance between global and local rewards

  - Contribution Reward

    - approach a:
      1. agents assume observed reward is the sum of each agent's direct contribution
      2. apportion reward based on the agent's contribution to the global reward
    - approach b:
      1. apportion reward based on how team would be disadvantaged had the agent never existed
    - pros:
      1. claim to be better than both global & local rewards (balance between them)

  - Non-disconted Reward Averaged over Time

    - averaging rewards of a sequence of tasks over time

    - pros:

      1. encourage collaboration 

         (relief the problem of not enough rewards for agents not directly achieving the goal)

- Dynamic Environment

  - Fully Cooperative Scenario

    - features

      1. global reward with equal divvy

      2. increase one agent's reward $\Leftrightarrow$ increase others reaward

         $\Rightarrow$ parato optimum agrees with Nash equilibria

    - deterministic repeated games

      1. update by estimating the likely best cooperation possible for agent's action
      2. update with stochastic sampling with agreed protocol for exploration & exploitation

    - stochastic (repeated) games

      1. optimal adaptive learning algorithm

         (guaranteed convergence to optimal Nash equilibria under finite actions and states)

    - partially observable markov decision process

      (stochastic game with constraint on agents' ability to observe the state of environment)

  - General Sum Game

    - features

      1. a game that is not zero-sum

      2. unequal-share credit assignment

         $\Rightarrow$ parato optimum may not agree with Nash equilibria

         $\Rightarrow$ may inadvertently cause non-cooperative scenarios

    - stochastic games

      1. learn not only its table of Q-values but also table for other agents

         $\Rightarrow$ other tables used to approximate actions from other

      2. directly approximate others policies, instead of Q-values

      3. EXORL algorithm: learn optimal response policies to both adaptive and fixed policies

  - Competitive Learning

    - features

      1. need to not only cooperate but also compete

         e.g. predator-prey game

      2. desirable to learn at similar pace

      3. loss of gradient problem: 'always win/lose' may occur 

         $\Rightarrow$ insufficient feed back for both sides

      4. red-queen effect: result of competition can be meaningless 

         $\Rightarrow$ agents not learning, but degenerate, at different paces

      5. cyclic behaviours: comptetitors exchange behaviours (no actual increase)

- Teammate Modeling

  - Goal

    - learn other agents in the environment $\Rightarrow$ approximate their expected behaviours
    - $\Rightarrow$ cooperate / response accordingly

  - Approches

    - simple learner (Bayesian) and model for other agents

    - learning the others belief, capability, preference etc. (statistical info)

      $\Rightarrow$ store a set of such model with their probability of being correct given the observation

    - communication protocol 

      1. for exchanging model
      2. for subcontracting tasks

      cons: usually constraints on communication $\Rightarrow$ combined with modeling

- Communication in Learning

  - Direct Communication
    - share immediate sensor information
      1. inform its state to others, under assumption (prior knowledge)
    - share past experience in form of episodes
      1. e.g. a sequence of <state, action, reward>
    - share directly current policies
    - can make communication learnable
      1. communication as an action, with rewards and etc.
      2. claim to learn to communicate even no reward attached to it
  - Indirect Communication
    - actions influence environment $\Rightarrow$ information observable by others
    - imitating others 

- Chanllenges

  - Scalability
    - currently multi-agents sys consists of 2~3 agents, limited actions (2~3)
    - yet, we need dozens, hundreds...

  - Adaptive Dynamics and Nash Equilibria
    - affected by credit assugnment scheme 
    - usually converge to sub-optimal Nash equilibria
    - my thought
      1. record level of learning level (ration level)
      2. communicate on striking
      3. adapt / update only when my level is lower (my fault)

  - Problem Decomposition

    - overwhelming state space

    - approaches

      1. domain knowledge $\Rightarrow$ smaller yet more powerful set of  customised actions

      2. heuristically & hierarchically decomposing the problem

      3. layered learning: learn basic behaviours, then compose more complex one based on them

         (bottom-up decomposition)

      4. shaping: gradually change reward function to favour those composed complex behaviours

         (soft layered learning)

      5. coordination graph: partially decompose joint Q-value based on coordination graph

         $\Rightarrow$ coordinate high-level behaivours 

         (balance between full joint utility table and separate independent tables) 

- Problem Domains and Applications

  - Emodied Agents
    - cooperative navigation
    - cooperative target observation
  - Game Theory
    - social delimma
  - Real-Time Distributed Decision Making
    - air traffic control
    - supply chains (JSP)
    - hierarchical multi-agent systems problems
    - social interaction modeling





















