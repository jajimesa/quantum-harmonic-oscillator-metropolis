# Monte Carlo estimates for the harmonic oscillator

This repository contains a Python implementation of **quantum trajectory simulations via imaginary-time (Euclidean) Feynman path integrals**. It allows for the computation of ground-state observables, the reconstruction of the ground-state wavefunction $|\psi_0(x)|^2$, and estimates of excited state energies using statistical sampling and the Metropolis algorithm.

The code follows the methodology presented in:

> **Creutz, M., & Freedman, B. (1981)**. *A statistical approach to quantum mechanics*. Annals of Physics, 132(2), 427–462.  
> [https://doi.org/10.1016/0003-4916(81)90074-9](https://doi.org/10.1016/0003-4916(81)90074-9)

---

## Physical and Mathematical Background

This simulation is based on **Feynman’s path integral formulation** of quantum mechanics in **imaginary time**, via Wick rotation. The Euclidean propagator is written as a weighted sum over all possible paths:

$$
\langle x_b | e^{-H T / \hbar} | x_a \rangle = \int \mathcal{D}[x(\tau)]\, e^{-S_\mathrm{E}[x(\tau)] / \hbar}
$$

with the **Euclidean action**:

$$
S_\mathrm{E}[x(\tau)] = \int_0^T d\tau \left[ \frac{m}{2} \left( \frac{dx}{d\tau} \right)^2 + V[x(\tau)] \right]
$$

For large $T = N \epsilon$, this becomes a projector onto the ground state, allowing us to extract $E_0$, $|\psi_0(x)|^2$, and even $E_1$ through correlation functions.

---

## Features

- **Metropolis algorithm** for generating statistically weighted quantum trajectories with periodic boundary conditions.
- **Customizable potentials**, including harmonic and anharmonic oscillators (e.g. $V(x) = \lambda(x^2 - f^2)^2$).
- **Ground state energy estimation** via $f[x(\tau)] = V[x(\tau)] + \frac{1}{2}x(\tau) V'[x(\tau)]$
- Proper computation of statistical errors.
- **Histogram of $|\psi_0(x)|^2$** from sampled paths with statistical error bars.
- Exponential adjust for $|\psi_0(x)|^2$.

---

## How to Run

Run the Jupyter Notebook `simulation.ipynb` and customize the parameters to obtain the desired results.

### Heuristic Recommendations

The Euclidean path integral simulation implemented here is not computationally demanding if the simulation parameters are chosen wisely. For the harmonic case ($\lambda = 0$), several heuristic guidelines are suggested based on lecture notes by Nieves (2024) and the original paper by Creutz and Freedman [^1]:

#### Guidelines

1. **Number of trajectories**: Use at least $N_\mathrm{E} \sim 10^2$ independent trajectories. This helps reduce statistical fluctuations, which typically scale as $1/\sqrt{N_\mathrm{E}}$.

2. **Total simulation time**: Ensure that the total imaginary time $T = N \epsilon$ is large enough to isolate the ground state, while balancing:
   - A number of discretization points $N \sim 10^2$ to $10^3$.
   - A time step \(\epsilon\) between $\pi/(5\omega)$ and $\pi/(10\omega)$, to control discretization errors.

3. **Initial sweep repetitions**: Before selecting a trajectory for measurement, it is advisable to generate and discard about $\bar{N} \in [3, 5]$ complete trajectories to help reach equilibrium (thermalization).

4. **Local updates**: For each selected trajectory, perform about $\bar{n} \in [5, 10]$ local Metropolis sweeps before moving to the next trajectory.

5. **Metropolis step size**: Choose a step size $a \sim 2\sqrt{\epsilon}$ such that the acceptance ratio remains close to $70$%. This ensures good exploration of configuration space, especially important when simulating more complex potentials with multiple minima (e.g., in the anharmonic oscillator).

[^1]: Creutz, M., & Freedman, B. (1981). *A Statistical Approach to Quantum Mechanics*. Annals of Physics, 132(2), 427–462. https://doi.org/10.1016/0003-4916(81)90074-9

