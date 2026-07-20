# Hodgkin–Huxley Neuron Model: Action Potential Simulation

This repository contains a numerical implementation and simulation of the **Hodgkin–Huxley model** (1952), a classic biophysical framework describing how action potentials are generated in neuronal membranes.

Developed as part of the course **Differential Equations in Mechanics and Biology** (*Double Degree in Physics and Mathematics, University of Granada*).

---

## 1. Biophysical Foundation

The neuronal plasma membrane acts as a capacitor ($C_m$) separating electrical charges between the intracellular and extracellular media. Embedded ion channels allow specific ions to cross the membrane, driven by electrochemical gradients.

Extracellular Medium

----------------------------------  <-- Lipid Bilayer (Capacitor $C_m$)

Intracellular Medium

The model accounts for three primary ionic currents:
- **Sodium current ($I_{\text{Na}}$):** Fast influx during depolarization.
- **Potassium current ($I_{\text{K}}$):** Efflux during repolarization.
- **Leakage current ($I_{\text{L}}$):** Passive ionic contributions (predominantly $\text{Cl}^-$).

---

## 2. Mathematical Formulation

### Kirchhoff's Current Law
The membrane potential $V$ evolves according to the balance between an externally injected current $I_{\text{ext}}$ and the ionic currents flowing through the channels:

$$C_m \frac{dV}{dt} = I_{\text{ext}} - I_{\text{Na}} - I_{\text{K}} - I_{\text{L}}$$

Where the individual ionic currents are given by:

$$I_{\text{Na}} = \bar{g}_{\text{Na},0} m^3 h (V - E_{\text{Na}}),$$

$$I_{\text{K}} = \bar{g}_{\text{K},0} n^4 (V - E_{\text{K}}),$$

$$I_{\text{L}} = g_{\text{L}} (V - E_{\text{L}}),$$

where $E_i = \frac{RT}{zF} \ln \left( \frac{c_{\text{ext}}}{c_{\text{int}}} \right)$ is Nernst's potential.

### Gating Variables & Rate Functions
The dimensionless gating variables $m, h, n \in [0, 1]$ represent the open probability/fraction of ion channel gates:
- $m$: Sodium channel activation.
- $h$: Sodium channel inactivation.
- $n$: Potassium channel activation.

Each gating variable $x \in \{m, h, n\}$ follows first-order kinetics:

$$\frac{dx}{dt} = \alpha_x(V)(1 - x) - \beta_x(V)x$$

The voltage-dependent rate functions $\alpha_x(V)$ and $\beta_x(V)$ determine the opening and closing rates:

$$\alpha_m(V) = \frac{0.1(25 - V)}{e^{\frac{25 - V}{10}} - 1}, \quad \beta_m(V) = 4 e^{-\frac{V}{18}}$$

$$\alpha_h(V) = 0.07 e^{-\frac{V}{20}}, \quad \beta_h(V) = \frac{1}{e^{\frac{30 - V}{10}} + 1}$$

$$\alpha_n(V) = \frac{0.01(10 - V)}{e^{\frac{10 - V}{10}} - 1}, \quad \beta_n(V) = 0.125 e^{-\frac{V}{80}}$$

---

## 3. Full Differential Equation System

The complete Hodgkin–Huxley model is a nonlinear system of **4 coupled ordinary differential equations (ODEs)** for the state vector $(V, m, h, n)$:

$$\begin{cases}
\frac{dV}{dt} = \frac{1}{C_m} \left( I_{\text{ext}}(t) - \bar{g}_{\text{Na},0} m^3 h (V - E_{\text{Na},0}) - \bar{g}_{\text{K},0} n^4 (V - E_{\text{K}}) - g_{\text{L}}(V - E_{\text{L}}) \right) \\
\frac{dm}{dt} = \alpha_m(V)(1 - m) - \beta_m(V)m \\
\frac{dh}{dt} = \alpha_h(V)(1 - h) - \beta_h(V)h \\
\frac{dn}{dt} = \alpha_n(V)(1 - n) - \beta_n(V)n
\end{cases}$$

---

## Numerical Simulations

The numerical simulations were developed using 4th-order **Runge-Kutta integration (RK4)** with a time step of $\text{dt} = 0.01\text{ ms}$ over a total duration of $50\text{ ms}$. Three main scenarios were evaluated:

* **Short current pulse with $V_0 = 0\text{ V}$:** An external pulse of $I_{\text{ext}} = 10\text{ mA}$ is applied between $t_1 = 1\text{ ms}$ and $t_2 = 1.5\text{ ms}$. This produces a single action potential (spike) that decays back to the resting state.
* **Short pulse with physiological resting potential ($V_0 = -68\text{ mV}$):** Applying the same $10\text{ mA}$ pulse displays a realistic depolarizing action potential curve.
* **Constant external current ($I_{\text{ext}} = 10\text{ mA}$):** The system enters a **repetitive firing regime** (a continuous train of action potentials), as sustained depolarization periodically reactivates sodium channels after each repolarization.
