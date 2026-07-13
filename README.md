# From Grains of Sand to Neuronal Avalanches

### Reproducing two papers on Self-Organized Criticality (SOC)

> *How can simple local interactions generate complex behavior without external tuning?*

This repository reproduces two papers on SOC. One introducing the original sandpile model, which builds the central intuition behind SOC **(Bak, Tang & Wiesenfeld, 1987)**, and another showing how the same ideas can be extended in a neural setting, showing how similar dynamics can emerge from biologically motivated mechanisms **(Levina, Herrmann & Geisel, 2007)**. 

---

## Contents

- [The general mechanism behind SOC](#the-general-mechanism-behind-soc)
- [1. The Bak-Tang-Wiesenfeld sandpile](#1-the-bak-tang-wiesenfeld-sandpile)
- [2. Reproducing the sandpile](#2-reproducing-the-sandpile)
- [3. From sandpiles to the brain](#3-from-sandpiles-to-the-brain)
- [4. Levina, Herrmann & Geisel: dynamical synapses](#4-levina-herrmann--geisel-dynamical-synapses)
- [5. Reproducing the neural network](#5-reproducing-the-neural-network)
- [Sandpile vs. neural network: same logic, different substrate](#sandpile-vs-neural-network-same-logic-different-substrate)
- [Discussion and limitations](#discussion-and-limitations)
- [References](#references)

---

## The general mechanism behind SOC

This is the single idea both systems explored in this repository are built on:

```
Slow, continuous drive
  (something is added to the system: sand, energy, resources)
              │
              ▼
    Local accumulation
  (a local variable builds up: pile height, synaptic resource)
              │
              ▼
   Threshold exceeded →
     local instability
              │
              ▼
  Fast, discrete relaxation
        (an avalanche)
              │
              ▼
  Redistribution / dissipation
   restores a subcritical state
              │
              └──────────────┐
                              ▼
                 System re-enters the slow
                 accumulation phase 
```
Slow accumulation and fast relaxation allow the system to alternate between periods of gradual buildup and sudden avalanches. This creates events spanning a broad range of sizes rather than a single characteristic scale.

---

## 1. The Bak-Tang-Wiesenfeld sandpile

This 1987 paper wasn't primarily about sandpiles, it was an attempt to explain the ubiquity of **1/f noise**, the puzzling power-law power spectrum observed in everything from resistors to the flow of the Nile. Sand is just the clearest way to make the argument concrete.

The paper actually starts with a simpler picture: a 1D chain of damped pendula connected by torsion springs. Kicking one pendulum can trigger a chain reaction through its neighbors, and the authors show that in one dimension this settles into a trivial, stable configuration. In two or more dimensions, though, something different happens. No configuration is stable against all small perturbations, so the system is forced to keep evolving, and it does so until perturbations can propagate across every length scale simultaneously. That state, with no characteristic scale left, is the critical point.

The cellular-automaton version used for simulation keeps the same physics with an integer height variable `z(x,y)` on a lattice:

- Grains are added one at a time to a random (or fixed) site.
- If a site's height exceeds a critical value `K`, it topples: it loses 4 units and each of its four neighbors gains 1.
- Toppling can trigger further toppling in neighboring cells — an avalanche.
- The process is *locally conservative*: grains aren't created or destroyed, only moved, except at the open boundaries where they leave the system.

Mapping this onto the general loop above: the **slow drive** is grain-by-grain addition; the **local accumulation** is the height at each site; the **threshold instability** is the toppling condition `z > K`; and the **fast relaxation** is the avalanche of topplings that follows, redistributing height until every site is locally stable again. The threshold `K` only sets a scale, so there's no free parameter to tune. The system organizes itself into criticality purely as a byproduct of being driven slowly and relaxing locally.

---
## 2. Reproducing the sandpile

### Building intuition: the emergence of avalanches

To build intuition for the dynamics, grains are repeatedly dropped onto the central cell of an initially empty **20×20 lattice**.

<p align="center">
<img src="figures/abelian_fractal_20x20.gif" width="550">
</p>

<p align="center">
<i>Figure 1. Grains are continuously added to the central cell of an initially empty 20×20 lattice. As grains accumulate and neighboring sites approach their threshold, cascades progressively spread to larger regions of the lattice.</i>
</p>

Starting from a completely stable state, the system gradually builds up height around the driven region. Early perturbations produce small, local rearrangements, but each avalanche redistributes grains and leaves nearby sites closer to instability. As the accumulated structure expands, a small addition can trigger cascades that travel increasingly farther through the lattice. 

### Large-scale dynamics and avalanche statistics (50×50) & (20×20×20)

To better reproduce the results reported by Bak, Tang & Wiesenfeld, the lattice size was increased to **50×50**. A larger lattice reduces finite-size effects and gives avalanches more room to propagate, making the scale-free behavior easier to observe.

<p align="center">
<img src="figures/sand_50x50.gif" width="600">
</p>

<p align="center">
<i>Figure 2. Avalanche dynamics on a 50×50 lattice.</i>
</p>

After initializing the lattice with randomly distributed grains, avalanche statistics were collected over the course of the simulation. The resulting distributions of avalanche duration, size, and power spectrum are shown below.

<p align="center">
<img src="figures/50x50_Dt.png" width="31%">
<img src="figures/50x50_Ds.png" width="31%">
<img src="figures/50x50_Sf.png" width="31%">
</p>

<p align="center">
<i>Figure 3. Avalanche duration (left), avalanche size (center), and power spectrum (right) for the 50×50 lattice.</i>
</p>

Rather than clustering around a typical value, avalanches span many orders of magnitude. Small events occur frequently, larger cascades become progressively rarer, and no characteristic scale dominates the dynamics. This broad range of avalanche sizes is one of the defining signatures of self-organized criticality and is best reflected in log-log distribution.

The same qualitative behavior is also observed in the three-dimensional simulation.

<p align="center">
<img src="figures/20x20x20_Dt.png" width="31%">
<img src="figures/20x20x20_Ds.png" width="31%">
<img src="figures/20x20x20_Df.png" width="31%">
</p>

<p align="center">
<i>Figure 4. Avalanche duration (left), avalanche size (center), and fractal dimension (right) for the 20×20×20 lattice.</i>
</p>

Overall, the reproduced dynamics agree well with the behavior described in the original paper. The fitted exponents, however, are not identical to the published values. Bak, Tang & Wiesenfeld reported a cluster-size exponent of approximately `τ ≈ 0.98` for the 50×50 lattice and `τ ≈ 1.35` for the 20×20×20 system, together with lifetime exponents of `α ≈ 0.42` in two dimensions and `α ≈ 0.90` in three dimensions.

These lifetime exponents are particularly interesting because they connect avalanche dynamics with the widespread appearance of **1/f noise**. Using the relationship derived by the authors, the two-dimensional value `α ≈ 0.42` corresponds to a power spectrum close to `f^{-1.58}`, remarkably similar to the power-law spectra observed in many physical systems. In other words, a simple lattice of falling sand reproduces not only scale-free avalanches but also statistical properties associated with 1/f noise.

Obtaining different exponents does not necessarily indicate a failed reproduction. The exact numerical value of the exponent is often less important than its relative changes across different conditions. In both experiments and simulations, power-law exponents are primarily used to compare how system dynamics evolve, while the emergence of scale-free behavior remains the defining signature of criticality.


---

## 3. From sandpiles to the brain

BTW's model raises an obvious question once you take it seriously: if criticality normally requires tuning a parameter precisely (as in ordinary phase transitions), how could a biological system like the brain sit near a critical point without anyone — or anything — tuning it from the outside?

This is exactly the question that motivated Levina, Herrmann & Geisel (2007). Cortical recordings had already shown **neuronal avalanches**, bursts of activity with power-law size distributions, reminiscent of sandpile avalanches, but the earlier models that reproduced them needed a global connectivity parameter fixed at one very specific value to stay critical. That's the opposite of self-organization: it's fine-tuning by the experimenter, not an emergent property of the system. Levina et al. asked whether a more biologically realistic ingredient — synapses whose strength changes with use — could turn criticality from a fragile coincidence into a robust, self-organizing outcome.

---

## 4. Levina, Herrmann & Geisel: dynamical synapses

The network is a set of `N` integrate-and-fire neurons: each neuron accumulates input in its membrane potential and fires a spike once it crosses a threshold, resetting afterward. So far this has nothing to do with sand. The part that reconnects it to BTW is the synapse model.

Each synaptic connection `J_ij` isn't fixed — it follows Tsodyks–Markram-style short-term depression:

- When a presynaptic spike arrives, the synapse consumes a fraction `u` of its available "resource" (loosely, neurotransmitter), and its effective strength drops.
- While the presynaptic neuron stays silent, the synapse **slowly recovers** back toward its maximal strength, on a timescale much slower than a single spike.

This is precisely the BTW loop again, just relabeled: the **slow drive** is external input pushing membrane potentials toward threshold; the **local accumulation** is synaptic resource recovering between spikes; the **threshold instability** is the neuron firing once its potential crosses `θ`; and the **fast relaxation** is the avalanche of spikes it can trigger downstream, which depletes synaptic resources and, like a sandpile toppling, pulls the network back into a locally quieter state.

The network is different from a sandpile in real ways: it isn't spatially local (in the fully-connected version, any neuron can affect any other), and what's conserved isn't a physical quantity like sand but a resource budget bounded by the parameter `α` (the maximal synaptic strength). This is exactly why it's a good second case study rather than a redundant one: it shows that the BTW mechanism doesn't depend on a 2D lattice, or on grains, or on strict local conservation, what's essential is the slow-accumulation / fast-threshold-release loop, and dynamical synapses reproduce that loop with completely different biological machinery. The paper's central analytical result is that this reproduces the loop *robustly*: the mean-field self-consistency equations for average synaptic strength and inter-spike interval show that, in the large-network limit, the network is critical for essentially any `α ≥ 1` — not one exact value, which is the whole point relative to earlier fine-tuned models.

---

## 5. Reproducing the neural network

The reproduced simulations recover the three regimes the paper describes as the coupling strength `α` is varied:

- **Subcritical** (`α` small): avalanches stay short, dominated by a handful of neurons.
- **Critical** (`α` around the paper's reported range): avalanche sizes follow an approximate power law over several orders of magnitude, cut off only by the network size.
- **Supercritical** (`α` large): avalanches routinely sweep through most or all of the network.

<p align="center">
<img src="figures/alphas_levina.png" width="500">
</p>

<p align="center">
<i>Figure 5. Avalanche-size distributions for different maximal synaptic strengths α. Small α gives short, subcritical bursts; intermediate α gives an extended power-law region; large α pushes the network into avalanches that engulf most of the system.</i>
</p>

Experimentally, cortical avalanches follow a power law with exponent close to `-3/2`; that number is the benchmark the reproduction is measured against, and the critical-regime distribution lands in the same neighborhood, with the usual caveats about finite network size and where exactly the power-law fitting window is drawn — the same kind of sensitivity discussed above for the sandpile.

---

## Sandpile vs. neural network: same logic, different substrate

| Ingredient of the general SOC loop | Sandpile | Neural network |
|---|---|---|
| Slow drive | Grains added one at a time | External input raising membrane potentials |
| Local accumulation | Height / local slope at each site | Synaptic resource recovering between spikes |
| Threshold instability | Height exceeds `K` → site topples | Membrane potential exceeds `θ` → neuron spikes |
| Fast relaxation | Grains redistributed to 4 neighbors | Spike propagates, depleting downstream synapses |
| What tunes the system | Nothing — threshold sets a scale, not a special value | Nothing — critical for a wide range of `α` in the large-N limit |
| What's conserved | Number of grains (locally, at interior sites) | Bounded synaptic resource budget, not a physical quantity |

The point of putting these side by side isn't that the two systems are "the same model in disguise" — they're not. It's that both are concrete solutions to the same design problem: how do you get a system to sit near a critical point *by construction*, without anyone dialing in the right parameter from outside? Sand does it through local height and gravity; cortex-like networks can do it through local synaptic depression and recovery.

---

## Discussion and limitations

Both reproductions capture the qualitative signatures the original papers are known for power-law avalanche statistics with no obvious size cutoff except system size, and (for Levina) a genuinely parameter-*insensitive* critical region rather than a knife-edge one. Neither reproduction should be read as a precision replication of the original figures: exponents drift somewhat from the published values, mostly for reasons that are well understood (finite system size, fitting-window choice, transient handling) rather than mysterious ones.

What I'd flag as the more interesting open work, if I kept extending this:

- Proper MLE fitting + Kolmogorov–Smirnov tests instead of eyeballed log-log slopes, for both models.
- Finite-size scaling across a range of lattice/network sizes to see the exponents converge, rather than reporting single-size fits.
- For Levina's model specifically: checking how sensitive the critical region is to partial connectivity or leak currents, both of which the original paper discusses analytically but which I haven't pushed on numerically here.

---

## References

Bak, P., Tang, C., & Wiesenfeld, K. (1987). *Self-Organized Criticality: An Explanation of 1/f Noise.* Physical Review Letters, 59(4), 381–384.

Levina, A., Herrmann, J. M., & Geisel, T. (2007). *Dynamical Synapses Causing Self-Organized Criticality in Neural Networks.* Nature Physics, 3, 857–860.

Beggs, J. M., & Plenz, D. (2003). *Neuronal Avalanches in Neocortical Circuits.* Journal of Neuroscience, 23(35), 11167–11177.

---

## Author

**Emilio Hernández Vargas**
B.Sc. Neuroscience — National Autonomous University of Mexico (UNAM)

> *Simple rules can produce extraordinary complexity. Understanding those rules is the first step toward understanding the brain.*
