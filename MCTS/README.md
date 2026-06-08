# This is an explanation of the mathematical principles of Monte Carlo Tree Search (MCTS)


# Monte Carlo Tree Search (MCTS): Mathematical Principles and Foundations

## 1. Introduction

**Monte Carlo Tree Search (MCTS)** is a sampling-based search algorithm designed to solve sequential decision-making problems. It became widely known after its success in computer Go and later formed a key component of systems such as AlphaGo and AlphaZero.

MCTS combines:

1. **Tree Search** (systematic exploration of decision sequences)
2. **Monte Carlo Sampling** (randomized estimation of future outcomes)
3. **Multi-Armed Bandit Theory** (balancing exploration and exploitation)

Unlike classical minimax search, MCTS **does not require exhaustive expansion of the game tree**. Instead, it selectively expands promising regions while estimating node values through stochastic simulations.

---

# 2. Problem Formulation

Consider a Markov Decision Process (MDP):

$$
(S, A, P, R)
$$

where:

- $s \in S$: state
- $a \in A$: action
- $P(s' \mid s, a)$: transition probability
- $R(s, a)$: reward

The objective is to find:

$$
a^* = \arg \max_a Q(s, a)
$$

where

$$
Q(s, a) = \mathbb{E}[G_t \mid s_t = s, a_t = a]
$$

and

$$
G_t = \sum_{k=0}^\infty \gamma^k r_{t+k}

is the discounted return.

In many practical problems, the exact computation of (Q(s,a)) is impossible because the state space grows exponentially.

MCTS approximates (Q(s,a)) through repeated sampling.

---

# 3. Tree Representation

Each node represents a state:

[
v \leftrightarrow s
]

For every node (v), we store:

### Visit Count

[
N(v)
]

Number of times node (v) has been visited.

### Action Visit Count

[
N(v,a)
]

Number of times action (a) was chosen from node (v).

### Total Reward

[
W(v)
]

Accumulated reward.

### Mean Value

[
Q(v)
====

\frac{W(v)}{N(v)}
]

or

[
Q(v,a)
======

\frac{W(v,a)}{N(v,a)}
]

which estimates the expected return.

---

# 4. Four Phases of MCTS

Each iteration consists of four steps:

1. Selection
2. Expansion
3. Simulation
4. Backpropagation

---

# 5. Selection Phase

Starting from the root node, the algorithm recursively selects children according to a tree policy.

The most important tree policy is **UCT (Upper Confidence Bound applied to Trees)**.

---

## 5.1 Multi-Armed Bandit Background

Suppose we have (K) slot machines.

Arm (i) has unknown mean reward:

[
\mu_i
]

After (n_i) pulls:

[
\hat{\mu}_i
===========

\frac{1}{n_i}
\sum_{j=1}^{n_i} r_j
]

The challenge is:

* Exploit: choose the best known arm
* Explore: gather information about uncertain arms

---

## 5.2 UCB1

Auer et al. derived:

[
UCB_i
=====

\hat{\mu}_i
+
\sqrt{
\frac{2\ln N}{n_i}
}
]

where:

* (N): total pulls
* (n_i): pulls of arm (i)

The second term is an uncertainty bonus.

As (n_i) increases:

[
\sqrt{\frac{\ln N}{n_i}}
\rightarrow 0
]

thus uncertainty gradually disappears.

---

# 6. UCT Formula

Kocsis and Szepesvári extended UCB to tree search.

For node (v):

[
UCT(v,a)
========

Q(v,a)
+
c
\sqrt{
\frac{\ln N(v)}
{N(v,a)}
}
]

where:

* (Q(v,a)): estimated value
* (N(v)): parent visits
* (N(v,a)): child visits
* (c): exploration constant

Selection chooses:

[
a^*
===

\arg\max_a UCT(v,a)
]

---

## Interpretation

The first term:

[
Q(v,a)
]

favors actions with high observed reward.

The second term:

[
c\sqrt{
\frac{\ln N(v)}
{N(v,a)}
}
]

favors actions with insufficient sampling.

Thus:

[
\text{Exploration}
+
\text{Exploitation}
]

are balanced automatically.

---

# 7. Expansion Phase

When selection reaches a node with unexpanded actions:

[
A_{untried}(v)\neq \emptyset
]

one action is chosen and a new child node is created.

If

[
s' = T(s,a)
]

then

[
v' \leftrightarrow s'
]

is added to the tree.

---

# 8. Simulation (Rollout)

From the newly expanded node, a simulation is performed until termination.

A rollout policy (\pi_r) generates actions:

[
a_t \sim \pi_r(a|s_t)
]

Typically:

[
\pi_r(a|s)
==========

\text{Uniform}
]

for pure MCTS.

The resulting trajectory is

[
s_0,a_0,s_1,a_1,\dots,s_T
]

yielding return

[
G
=

\sum_{t=0}^{T}
\gamma^t r_t
]

This provides a Monte Carlo estimate of the node value.

---

## Why Monte Carlo Works

By the Law of Large Numbers:

[
\frac{1}{M}
\sum_{i=1}^{M}
G_i
\rightarrow
\mathbb{E}[G]
]

as

[
M\rightarrow\infty
]

Therefore repeated rollouts converge to the true expected return.

---

# 9. Backpropagation

After rollout reward (G) is obtained, all nodes along the selected path are updated.

For each node (v):

### Visit Count

[
N(v)
\leftarrow
N(v)+1
]

### Total Reward

[
W(v)
\leftarrow
W(v)+G
]

### Value Estimate

[
Q(v)
====

\frac{W(v)}
{N(v)}
]

This propagates information from leaf evaluations back toward the root.

---

# 10. Statistical Convergence

One of the most important theoretical results is:

[
Q(v,a)
\rightarrow
Q^*(v,a)
]

with probability 1 as the number of simulations approaches infinity.

Under mild assumptions:

[
\lim_{n\to\infty}
P(a_{MCTS}=a^*)
===============

1
]

Thus MCTS is **asymptotically optimal**.

---

# 11. Computational Complexity

Suppose:

* branching factor (b)
* search depth (d)

Classical minimax requires:

[
O(b^d)
]

nodes.

MCTS uses only:

[
O(T)
]

simulations,

where (T) is chosen by the user.

The algorithm focuses computational effort on promising branches.

This is why MCTS can handle games such as Go, whose search space is approximately:

[
10^{170}
]

far beyond exhaustive search.

---

# 12. MCTS as Value Estimation

From a reinforcement learning perspective:

[
Q(s,a)
======

\mathbb{E}[G|s,a]
]

MCTS approximates this expectation by:

[
Q_{MCTS}(s,a)
=============

\frac{1}{N(s,a)}
\sum_{i=1}^{N(s,a)}
G_i
]

which is simply a Monte Carlo estimator.

Thus MCTS can be viewed as an adaptive sampling method for estimating action values.

---

# 13. AlphaGo / AlphaZero Extension

Modern variants replace random rollouts with neural networks.

Instead of rollout evaluation:

[
V_\theta(s)
]

predicts state value.

Instead of uniform exploration:

[
P_\theta(a|s)
]

provides prior probabilities.

The selection rule becomes:

[
PUCT(s,a)
=========

Q(s,a)
+
c_{puct}
P(s,a)
\frac{\sqrt{N(s)}}
{1+N(s,a)}
]

where:

* (P(s,a)) comes from the policy network,
* (Q(s,a)) comes from search statistics.

This dramatically improves search efficiency.

---

# 14. Bayesian Interpretation

UCT can be interpreted as an approximate Bayesian optimization process.

The exploitation term:

[
Q(v,a)
]

represents the posterior mean.

The exploration bonus:

[
c
\sqrt{\frac{\ln N(v)}{N(v,a)}}
]

represents uncertainty.

Selection therefore approximates:

[
\text{Expected Reward}
+
\text{Uncertainty Bonus}
]

which resembles an upper confidence bound on the true action value.

---

# 15. Summary

The mathematical essence of MCTS can be summarized as:

1. **Monte Carlo estimation**

[
Q(s,a)
======

\mathbb{E}[G]
]

estimated through random rollouts.

2. **Bandit optimization**

[
UCT
===

Q
+
c
\sqrt{
\frac{\ln N}{n}
}
]

balances exploration and exploitation.

3. **Recursive tree construction**

Promising branches receive exponentially more simulations.

4. **Asymptotic convergence**

[
Q_{MCTS}
\rightarrow
Q^*
]

and the probability of selecting the optimal action approaches 1 as the number of simulations increases.

In essence, MCTS transforms an intractable combinatorial search problem into a sequence of statistically guided sampling decisions, using confidence bounds to allocate computation where it is most valuable. This combination of Monte Carlo estimation and bandit theory is the core mathematical principle behind MCTS.
