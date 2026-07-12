# From-grains-of-sand-to-neuronal-avalanches

### Reproducing papers of Self-Organized Criticality

> *How can simple local interactions generate complex behavior without external tuning?*

This repository reproduces two landmark papers that shaped our understanding of **Self-Organized Criticality (SOC)** and its application to **Computational Neuroscience**.

Starting from the classical **Bak-Tang-Wiesenfeld (1987)** sandpile model and continuing to the biologically-inspired **Levina et al. (2007)** neural network model, this project explores how scale-free dynamics emerge from simple local rules.

---

## Motivation

Self-Organized Criticality is one of the central ideas in Complex Systems.

The original sandpile model demonstrated that a system composed of extremely simple rules can naturally evolve toward a critical state characterized by

- scale-free avalanches,
- power-law distributions,
- absence of characteristic scales,
- emergent complexity.

Years later, these same ideas reached neuroscience after the discovery of **neuronal avalanches**, suggesting that cortical activity may operate near a critical point.

This repository reproduces both models and connects them through simulations, visualization, and statistical analysis.

---

# Repository Overview

---

# Contents

- Introduction to Self-Organized Criticality
- Bak-Tang-Wiesenfeld Sandpile Model
- Avalanche Dynamics
- Power-law Analysis
- Two-dimensional vs Three-dimensional Sandpiles
- Dynamic Synapses (Levina et al.)
- Connection to Neuronal Avalanches
- Discussion
- References

---

# 1. Self-Organized Criticality (SOC)

Many physical systems naturally evolve toward a critical state without requiring external parameter tuning.

Unlike classical phase transitions, where a control parameter must be adjusted precisely, SOC systems spontaneously organize themselves near the critical point.

Their main signatures include

- long-range correlations
- scale invariance
- avalanche dynamics
- power-law distributions

The sandpile model is the canonical example.

---

# 2. Bak-Tang-Wiesenfeld Sandpile Model

The first part of this project reproduces the classical 1987 model proposed by

> Bak, Tang & Wiesenfeld

A lattice stores grains of sand.

Whenever a cell accumulates more than a threshold number of grains, it topples, redistributing grains to neighboring cells.

Those neighbors may also become unstable, generating an avalanche.

Despite the simplicity of these local rules, the system self-organizes into a critical state.

---

## Local Dynamics

The intuition behind the model can be illustrated using a small **20×20 lattice**.

Grains of sand are repeatedly dropped onto the center of the lattice, just as one would continuously pour sand onto the top of a real sandpile. At first, the pile simply grows. Once the central site reaches the critical height, it becomes unstable and topples, redistributing grains to its four nearest neighbors.

Initially, avalanches remain localized around the center, where the sand is continuously added. As more grains accumulate over time, neighboring sites also approach the critical threshold, allowing avalanches to spread farther from the center and involve increasingly larger regions of the lattice.

<p align="center">
  <img src="https://github.com/user-attachments/assets/d2ffe3e3-017a-4dbd-84e4-8de006ba01eb"
       alt="20×20 Sandpile simulation"
       width="550">
</p>

<p align="center">
<i>Figure 1. Grains are continuously added to the central cell of a 20×20 lattice. Early avalanches remain confined near the deposition site, but as the sandpile grows, activity spreads outward, producing larger cascades that propagate through the lattice.</i>
</p>

This simple visualization mirrors the behavior of a real sandpile: as sand is continuously poured onto the top, small local collapses occur first, while larger avalanches gradually emerge and reshape the entire pile.

---

## Large-Scale Dynamics

After understanding the local mechanism, the simulation is extended to a **50×50 lattice**, initialized following the conditions described in the original paper.

Now, avalanches of different sizes naturally emerge.

<p align="center">
<img src="figures/bak/50x50.gif" width="500">
</p>

Most avalanches involve only a few cells, while a small number propagate through almost the entire system.

This absence of a characteristic avalanche size is one of the hallmarks of Self-Organized Criticality.

---

# 3. Avalanche Statistics

The avalanche size distribution follows an approximate power law.

Small avalanches occur frequently.

Large avalanches are rare but possible.

This scale-free behavior indicates that no characteristic avalanche size dominates the dynamics.

<p align="center">
<img src="figures/bak/powerlaw.png" width="500">
</p>

---

# 4. Reproducing the Original Results

The simulations reproduce the qualitative behavior reported in the original article.

The resulting distributions exhibit clear scale-free behavior in both two-dimensional and three-dimensional systems.

---

## Discussion

Although the fitted exponents differ from those reported in the original publication, all simulations consistently exhibit power-law behavior across multiple orders of magnitude.

Several factors can explain these differences:

- finite-size effects
- stochastic initialization
- simulation length
- transient removal
- fitting interval
- estimation methodology
- boundary conditions

Obtaining a different exponent does **not** necessarily imply that the model failed to reproduce the original phenomenon.

Instead, it illustrates one of the most important aspects of statistical physics:

> Estimating power-law exponents is highly sensitive to finite-size effects and statistical methodology.

Future improvements could include

- Maximum Likelihood Estimation
- Kolmogorov-Smirnov goodness-of-fit tests
- finite-size scaling analysis
- comparison against alternative distributions

---

# 5. From Sandpiles to the Brain

The sandpile model naturally raises an important question.

If criticality requires precise tuning, how could biological systems remain close to the critical point?

The brain has no external observer adjusting parameters.

So what mechanism could maintain criticality autonomously?

This question motivated the work of **Levina et al. (2007)**.

---

# 6. Dynamic Synapses

The second part of this repository reproduces

> Levina, Herrmann & Geisel (Nature Physics, 2007)

Instead of sand grains, the system now consists of integrate-and-fire neurons connected through **dynamic synapses**.

Unlike previous models requiring fine tuning, synaptic efficacy changes automatically according to neuronal activity.

Each spike

- consumes synaptic resources
- weakens the connection
- allows gradual recovery afterward

This adaptive mechanism naturally drives the network toward criticality.

<p align="center">
<img src="figures/levina/synapse.png" width="600">
</p>


---

# Model Overview

```
External Input
        │
        ▼
 Neuron fires
        │
        ▼
Synaptic depression
        │
        ▼
Spike propagation
        │
        ▼
Avalanche
        │
        ▼
Recovery
        │
        └───────────────┐
                        │
                        ▼
               Self-organization
```

---

# Results

The reproduced simulations recover the three dynamical regimes described in the original publication:

- subcritical
- critical
- supercritical

By varying the maximum synaptic strength parameter (α), the avalanche distribution transitions from exponential behavior to scale-free dynamics and eventually to system-wide avalanches.

<p align="center">
<img src="figures/levina/avalanches.png" width="500">
</p>

---

# Sandpile vs Neural Networks

| Sandpile | Neural Network |
|-----------|----------------|
| Sand grains | Neurons |
| Cell height | Membrane potential |
| Toppling | Spike generation |
| Grain redistribution | Synaptic transmission |
| Avalanche | Neuronal avalanche |
| Local conservation | Synaptic dynamics |
| Critical state | Critical brain dynamics |

Although the microscopic mechanisms differ, both systems exhibit remarkably similar macroscopic behavior.

---

# Relevance to Neuroscience

Experimental recordings have shown that spontaneous cortical activity often appears as **neuronal avalanches** whose sizes approximately follow a power-law distribution.

Critical dynamics have been associated with

- maximal dynamic range
- optimal information transmission
- efficient information storage
- increased computational capability
- robust responsiveness to external stimuli

The Levina model provides a biologically plausible mechanism explaining how neural systems could self-organize toward this regime without requiring external parameter tuning.





# References

Bak, P., Tang, C., & Wiesenfeld, K. (1987). *Self-Organized Criticality: An Explanation of 1/f Noise.*

Levina, A., Herrmann, J. M., & Geisel, T. (2007). *Dynamical Synapses Causing Self-Organized Criticality in Neural Networks.*

Beggs, J. M., & Plenz, D. (2003). *Neuronal Avalanches in Neocortical Circuits.*

---
# Author

**Emilio Hernández Vargas**
B.Sc. Neuroscience — National Autonomous University of Mexico (UNAM)

> *Simple rules can produce extraordinary complexity. Understanding those rules is the first step toward understanding the brain.*





