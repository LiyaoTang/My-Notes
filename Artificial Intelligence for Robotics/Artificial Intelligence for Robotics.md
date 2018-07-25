# Artificial Intelligence for Robotics

### Localisation

- Notation

  - $X$: grid cell 
    - finest cell for location in 1D world
  - $Z$: measurement
    - robot observes the environment

- Bayesian Rule

  - Prior

    - $P(X_i)$: probability of currently being in cell $i$ 

  - Likelihood

    - $P(Z|X_i)​$: probability of having current observation given being in cell $i​$ 

  - Posterior

    - $P(X_i|Z)$: probability of being in cell $i$ after measurement (observation)

      $\begin{align} \displaystyle P(X_i|Z) &= \frac {P(X_i)P(Z|X_i)} {P(Z)} \\ &= \frac {P(X_i)P(Z|X_i)} {\sum_i P(X_i)P(Z|X_i)} \end{align}$

- 