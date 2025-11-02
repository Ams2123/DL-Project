The dataset utilized in this research is a 5-meter resolution Digital Elevation Model (DEM) from the Lunar Orbiter Laser Altimeter (LOLA) instrument. You can access LRO Data Products at [https://lunar.gsfc.nasa.gov/dataproducts.html](https://lunar.gsfc.nasa.gov/dataproducts.html).

### Model Used and Results

This research proposes a novel approach to global path and resource planning for lunar rovers using **reinforcement learning (RL)**, specifically a **Deep Q-Network (DQN)**. The core idea is to integrate various constraints (static, time-variant, and path-dependent) into a grid map as a penalty function, then use the DQN to solve a **resource-constrained shortest path problem (RCSP)**.

Here's a breakdown of the model and its results:

**1. Learning Architecture (DQN-based):**
   *   The model frames the problem as a **Markov Decision Process (MDP)**, characterized by a state `s`, actions `u`, a state-transition function `Pa`, and a reward function `Re`.
   *   The **state `s`** incorporates the rover's position (`Xk`), time (`tk`), thermal status (`T`), and battery power (`B%`).
   *   The **action `u`** dictates whether the rover stays in its current position or moves to an adjacent grid.
   *   **Greedy Orientation Control:** To reduce computational complexity, an optimal rover orientation (`θ*`) is determined at each step to minimize a combined thermal and power penalty, rather than including it as a state variable.

**2. Key Models Integrated:**
   *   **Lunar Environment Model:** Predicts sun vector and lunar surface temperature (critical for thermal and power management) based on real lunar digital elevation data.
   *   **Rover Thermal Model:** A node-based thermal model predicts the temperature of different parts of the rover (e.g., Top, Right, Left) based on solar radiation, albedo, heat transfer, dissipated energy from electronics, and radiation to outer space. This model highlights the **path-dependent** nature of the rover's temperature.
   *   **Rover Power Model:** Calculates power generation from solar panels, available power for battery charging/discharging, and the remaining battery power percentage. This model also shows the **path-dependent** nature of the rover's power status.
   *   **Reward Function:** A crucial component of RL, designed to guide the rover toward optimal paths. It includes penalties for:
      *   Time (`petime`)
      *   Terramechanics/slope (`peslope`)
      *   Thermal conditions (`pethermal`)
      *   Power consumption (`pepower`)
      *   And rewards for:
      *   Positioning (approaching the goal, `repos`)
      *   Reaching the goal (`regoal`)

**3. Simulation Results and Analysis:**

   *   **Scenario 1 (Less Challenging Environment):**
      *   The architecture successfully identified a path while consistently adhering to user-defined safety criteria for terramechanical, thermal, and power conditions across all positions and time epochs.
      *   **Example Path (Figure 3a):** 
      *   **Slope History (Figure 3b):** 
      *   **Rover Top Temperature History (Figure 3c):** 
      *   **Rover Battery Power History (Figure 3d):** 

   *   **Scenario 2 (More Challenging Environment):**
      *   Due to higher lunar surface temperatures and more undulating terrain, the total path time was longer.
      *   The rover took "stay" actions in the middle of the mission to remain in low-temperature grids until a thermally viable path became available.
      *   **Example Path (Figure 4a):** 
      *   **Slope History (Figure 4b):** 
      *   **Rover Top Temperature History (Figure 4c):** 
      *   **Rover Battery Power History (Figure 4d):** 

   *   **Comparative Analysis (Resource-Based vs. Environment-Based Path Search):**
      *   In Scenario 1, both approaches showed similar performance.
      *   In **Scenario 2**, the **resource-based path search (proposed method) outperformed the environment-based path search**. This indicates that the proposed method, by leveraging the rover's thermal and power capacitance, can temporarily navigate through challenging environmental conditions, leading to better-optimized paths.

   *   **Probabilistic Simulation:**
      *   Evaluated performance with different rover motion transition probabilities (`pt`).
      *   Higher `pt` (increased uncertainty in rover movement) led to more violations of safety criteria, especially in Scenario 2, demonstrating the approach's sensitivity to unforeseen uncertainties. However, for `pt=0` (no uncertainty), no violations occurred for both scenarios.

   *   **Reward Tuning:**
      *   The study showed how adjusting the scaling factors for thermal (`Kt`) and power (`Kp`) penalties in the reward function influences path selection. Prioritizing thermal constraints led to different paths and resource histories compared to prioritizing power constraints.

**Conclusion:** The simulation results demonstrate the effectiveness of the proposed reinforcement learning architecture in identifying optimal lunar rover paths that consider static, time-variant, and path-dependent constraints, outperforming conventional methods that only rely on environmental factors.

---

### Jargon Terms Explained:

*   **Reinforcement Learning (RL):** A type of machine learning where an agent learns to make decisions by taking actions in an environment to maximize a cumulative reward. It learns through trial and error.
*   **Deep Q-Network (DQN):** A specific type of reinforcement learning algorithm that combines Q-learning (a value-based RL algorithm) with deep neural networks. The neural network approximates the Q-function, which estimates the expected future rewards for taking a certain action in a given state.
*   **Resource-Constrained Shortest Path Problem (RCSP):** A variant of the shortest path problem where, in addition to minimizing path length (or time/cost), there are constraints on one or more resources (e.g., energy, time, thermal limits) that must not be violated.
*   **Grid Map:** A common way to represent an environment for path planning. It discretizes the environment into a grid of cells, where each cell can hold information about terrain properties (e.g., height, slope).
*   **Penalty Function:** A method used in optimization to transform a constrained problem into an unconstrained one. In this context, violating a constraint (like exceeding a temperature limit) adds a "penalty" to the reward function, making that path less desirable.
*   **Markov Decision Process (MDP):** A mathematical framework for modeling sequential decision-making in situations where outcomes are partly random and partly under the control of a decision-maker (agent). It's defined by states, actions, transition probabilities, and rewards.
*   **Markov Property:** A property of a stochastic process stating that the future state of the process depends only on the current state and not on the sequence of events that preceded it. It implies "memorylessness."
*   **State (`s`):** In an MDP, the current situation or configuration of the agent and its environment. In this paper, `s` includes rover position, time, temperature, and battery power.
*   **Action (`u`):** A decision or movement made by the agent in an MDP.
*   **State-Transition Function (`Pa`):** In an MDP, this function defines the probability of transitioning from one state to another given a specific action.
*   **Reward Function (`Re`):** In an MDP, a function that assigns a numerical value (reward or penalty) to each state-action pair, guiding the agent toward desirable behaviors.
*   **Greedy Orientation Control:** A strategy where, at each step, the rover's orientation is chosen to immediately minimize a penalty (thermal and power) without considering future steps. This simplifies the planning problem.
*   **Digital Elevation Model (DEM):** A 3D representation of terrain surface. In this research, a 5m resolution DEM from the Lunar Orbiter Laser Altimeter (LOLA) instrument provides height data for the lunar surface.
*   **Time-Variant Constraints:** Constraints that change over time (e.g., lunar surface temperature, sun position).
*   **Path-Dependent Constraints:** Constraints whose values depend on the sequence of previous actions and states taken by the rover (e.g., rover's internal temperature and battery level depend on its past trajectory and activities).
*   **Terramechanics:** The study of the interaction between a vehicle's locomotion system and the terrain it traverses, especially relevant for off-road vehicles like lunar rovers.
*   **Depth of Discharge (DoD):** The percentage of a battery's capacity that has been discharged. A low DoD typically contributes to a longer battery life.
*   **Hyperparameters:** Parameters whose values are set before the learning process begins, rather than being learned by the model itself (e.g., learning rate, discount factor in RL).
*   **Time Epoch:** A specific point in time during the mission.
*   **Capacitance (Thermal/Power):** Refers to the ability of the rover's thermal and power systems to store and release energy. Thermal capacitance allows the rover to resist rapid temperature changes, and power capacitance (battery) allows it to store and use power over time.
