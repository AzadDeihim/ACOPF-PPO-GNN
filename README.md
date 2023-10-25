# ACOPF-PPO-GNN
This repository houses a proximal policy optimization (PPO) and graph neural network (GNN)-based method for computing initial guesses for an interior point solver (IPS) used to find the optimal solution to an AC optimal power flow (ACOPF).

Given a power system where $\mathscr{N}$ denotes the set of all buses, $\mathscr{G}$ the set of all generators, and $\mathscr{L}$ set of all transmission lines, we formulate the ACOPF as a set of equality and inequality constraints and an objective function to be minimized. Formally,

minimize:
     $$\sum_{i \in \mathscr{G}} (C_{2i} P_{gi}^2 + C_{1i} P_{gi}).$$


Subject to: 
$$P_{gi}^{min} \leq P_{gi} \leq P_{gi}^{max}, \qquad \qquad \forall i \in \mathscr{G}, $$
$$Q_{gi}^{min} \leq Q_{gi} \leq Q_{gi}^{max}, \qquad \qquad \forall i \in \mathscr{G}, $$
$$V_{i}^{min} \leq V_{i  } \leq V_{i}^{max}, \qquad \qquad \forall i \in \mathscr{N}, $$
$$|S_{flow, ij}| \leq |S_{flow, ij}^{max}|, \qquad \qquad \forall (i,j) \in \mathscr{L}, $$
$$P_{i} = \sum_{\forall j \in \mathscr{N}} V_iV_j(g_{ij}cos(\theta_j - \theta_i) + b_{ij}sin(\theta_j - \theta_i)), \quad \forall i \in \mathscr{N}, $$
$$Q_{i} = \sum_{\forall j \in \mathscr{N}} V_iV_j(g_{ij}sin(\theta_j - \theta_i) - b_{ij}cos(\theta_j - \theta_i)), \quad \forall i \in \mathscr{N}, $$

where $C_{1\cdot}$ and $C_{2 \cdot}$ are coefficients related to the costs of power generation. $P_i$ and $Q_i$ are the total real and reactive power at bus $i \in \mathscr{N}$, respectively; $P_{gi}$ and $Q_{gi}$  are the total real and reactive generation at generator $i \in \mathscr{G}$. $S_{flow,ij}$ is the apparent power of line $(i,j) \in \mathscr{L}$, $V_i$ and $\theta_i$ are the voltage magnitude and the phase angle at bus $i \in \mathscr{N}$, respectively. $g_{ij}$ and $b_{ij}$ are the conductance and susceptance, respectively, of the line $(i,j) \in \mathscr{L}$ that connects buses $i$ and $j$.

PPO is a simple actor-critic reinforcement learning algorithm that seeks to find an optimal policy (a function that maps observations to actions) rather than assigning values to state-action pairs. We couple this with a GNN to act as the agent’s learning mechanism — exploiting the graph structure of the power grid. GNNs are a specialized type of neural networks that are well-suited to process graph-structured data. By analyzing and utilizing the patterns and relationships within the graph, GNNs can provide more accurate predictions about the entities involved compared to models that only consider individual entities in isolation. Using a GNN also allows adaptation to changes in topology with only minor adjustments. In contrast, a fully connected neural network, which has been the standard in deep learning-based ACOPF literature, would require a complete retrain. Topology adaptability is especially important in real-world scenarios where line and generator outages or other unexpected changes to topology can occur.

We do not train the deep reinforcement learning agent to produce a solution to ACOPF that is feasible or cost-minimizing; we instead train the agent to compute an initial guess that minimizes the amount of time it would take for the IPS to find a solution. Due to the nonconvex properties of ACOPF, an initial guess that is nearer to the global optimum in terms of costs and feasibility may be very far in proximity to the global optimum. As a result, training an agent to generate initial guesses that prioritize feasibility and cost minimization can actually lead to longer IPS convergence times. The reward function is as follows:

The following Figure outlines the training algorithm: 

