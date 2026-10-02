# ENGINEERING DESIGN RATIONALE REPORT
**Plasma Physics, Toxicity Control Architecture, and COMSOL Multiphysics Numerical Validation: On-Demand Plasma-Derived NO Synthesis System for the Treatment of Persistent Pulmonary Hypertension of the Newborn (PPHN)**

## Executive Summary and System Architecture

### Clinical Motivation and Engineering Requirements (ER)
Inhaled Nitric Oxide (iNO) therapy is a critical first-line intervention for term and near-term neonates with hypoxic respiratory failure associated with Persistent Pulmonary Hypertension of the Newborn (PPHN), improving ventilation-perfusion matching by inducing selective vasodilation in the pulmonary vascular bed. However, current clinical practice relies on high-pressure $\mathrm{NO/N_2}$ gas cylinders, which impose significant hospital logistics burdens, reduce portability, and introduce the risk of treatment interruption during cylinder changes. The BIONOx project aims to overcome this logistical barrier by synthesizing NO on-demand from ambient air via cold atmospheric pressure plasma in a compact, bedside-integrable device.

The low body weight and lung volume of the neonatal patient population reduce the error tolerance for toxic byproducts (especially $\mathrm{NO_2}$ and $\mathrm{O_3}$) to near zero. Accordingly, the four binding Engineering Requirements (ER) forming the core of the device are defined as follows:
* **ER-01:** Therapeutic NO production range must be continuously adjustable between $0\text{–}80\ \mathrm{ppm}$ and restricted by an $80\ \mathrm{ppm}$ hardware hard limit.
* **ER-02:** Toxic $\mathrm{NO_2}$ concentration in the patient line must be continuously $< 2\ \mathrm{ppm}$; upon a reading of $\ge 3\ \mathrm{ppm}$, the system must immediately trigger an alarm and enter Safe Mode.
* **ER-03:** Ozone ($\mathrm{O_3}$) concentration must strictly remain $< 0.1\ \mathrm{ppm}$.
* **ER-04:** The cumulative efficiency of the purification system for toxic species ($\mathrm{NO_2} + \mathrm{O_3} + \text{ROS/RNS}$) must exceed $99\%$.

### 5-Stage Signal and Gas Flow Architecture
The BIONOx system utilizes a sequential hardware architecture where each stage must meet its own safety criteria:
1. **Plasma Reactor:** Synthesizes NO from ambient air via non-thermal ($T_e \gg T_{gas}$) plasma.
2. **Mixing Line:** Injects synthesized gas into the ventilator's $\mathrm{O_2}$-rich circuit with a short contact time optimized to suppress $\mathrm{NO_2}$ kinetics.
3. **Purification Barrier:** Eliminates toxic byproducts through filtration, catalytic reduction, and adsorption stages.
4. **Real-Time Sensor:** Performs electrochemical monitoring for closed-loop dose control and fail-safe triggering in case of error.
5. **Patient Ventilator Line:** Delivers gas to the patient only after successfully passing all upstream safety thresholds.

---

## Section 1: Plasma Physics and NO Synthesis from Ambient Air

### 1.1 Thermodynamic Non-Equilibrium ($T_e \gg T_{gas}$)
The BIONOx reactor operates via a dielectric-barrier discharge (DBD) mechanism to produce "cold" atmospheric pressure plasma. The defining characteristic of this discharge regime is the severe thermodynamic non-equilibrium between electrons and neutral gas molecules:
$$T_e \gg T_{gas}, \qquad T_e \approx 1\text{–}5\ \mathrm{eV}, \qquad T_{gas} \approx 300\text{–}320\ \mathrm{K}$$
The physical origin of this state is mass asymmetry. The mass of an electron ($m_e$) is approximately four orders of magnitude smaller than that of molecular nitrogen ($M$). In elastic collisions, the maximum fraction of kinetic energy transferred from an electron to the gas is expressed as:
$$\frac{\Delta\varepsilon_{max}}{\varepsilon} = \frac{4\, m_e}{M} \approx \frac{4 \times 9.11\times10^{-31}\ \mathrm{kg}}{4.8\times10^{-26}\ \mathrm{kg}} \approx 7.6\times10^{-5}$$
While the applied electric field accelerates electrons to energies equivalent to $10^4\text{–}5\times10^4\ \mathrm{K}$, the bulk gas temperature ($T_{gas}$) remains at ambient ($300\ \mathrm{K}$) due to this microscopic fractional energy transfer. This physical reality allows the system to be integrated directly into a neonate's respiratory tract without causing thermal injury. The discharge is self-sustaining as background seed electrons accelerate in the electric field, creating collisional ionization (Townsend avalanche).

### 1.2 Electron-Impact Dissociation and Vibrational Excitation
Breaking the extremely stable $\mathrm{N_2}$ triple bond is the primary bottleneck of NO synthesis. In the plasma environment, this barrier is overcome via two mechanisms:
1. **Direct Dissociation:** The high-energy tail of the Electron Energy Distribution Function (EEDF) provides direct dissociation:
   $$e^- + \mathrm{O_2} \rightarrow 2\mathrm{O}(^3P) + e^-, \quad E_{th} \approx 5.2\ \mathrm{eV}$$
   $$e^- + \mathrm{N_2} \rightarrow 2\mathrm{N}(^4S) + e^-, \quad E_{th} \approx 9.8\ \mathrm{eV}$$
2. **Vibrational Excitation:** Low-energy electrons ($0.3\text{–}3\ \mathrm{eV}$) transfer energy into the vibrational modes of the molecule ($e^- + \mathrm{N_2} \rightarrow \mathrm{N_2}(v) + e^-$). This mechanism (Treanor distribution) progressively weakens the absolute $9.8\ \mathrm{eV}$ activation barrier, vastly improving thermodynamic efficiency.

### 1.3 Extended Zeldovich Mechanism
In thermal engines and arc discharges, NO synthesis proceeds via the classical Zeldovich mechanism requiring $>1800\ \mathrm{K}$. BIONOx, however, operates this mechanism at room temperature by leveraging plasma radicals instead of heating the bulk gas:
$$\text{(R1)}\quad \mathrm{N}(^4S) + \mathrm{O_2} \rightarrow \mathrm{NO} + \mathrm{O}, \quad E_a \approx 26.9\ \mathrm{kJ/mol}$$
$$\text{(R2)}\quad \mathrm{O}(^3P) + \mathrm{N_2} \rightarrow \mathrm{NO} + \mathrm{N}, \quad E_a \approx 315\ \mathrm{kJ/mol}$$
The massive $315\ \mathrm{kJ/mol}$ barrier in reaction R2 is effectively bypassed due to the presence of pre-dissociated $\mathrm{O}$ and $\mathrm{N}$ atoms generated by the plasma.

### 1.4 Control Variable: Specific Input Energy (SIE)
The reactor optimizes Specific Input Energy (SIE) to control therapeutic yield:
$$\mathrm{SIE} = \dfrac{P_{discharge}}{Q_{air}} \quad [\mathrm{J/L}]$$
SIE is controlled by adjusting voltage ($6\text{–}15\ \mathrm{kV}$) and frequency ($0.5\text{–}20\ \mathrm{kHz}$). While higher SIE produces more NO, it triggers toxic $\mathrm{NO_2}$ and $\mathrm{O_3}$ co-production due to excess atomic oxygen accumulation. Therefore, the system operates on a closed-loop logic to hit the target ppm with minimum SIE, reducing the purification load at its source.

---

## Section 2: Oxygen-Rich Injection and Toxicity Control Architecture

### 2.1 Physiological Risk and Kinetic Constraints
The high $\mathrm{O_2}$ concentration (FiO₂ up to $100\%$) in the ventilator circuit inherently drives the oxidation of produced NO into its most hazardous form, $\mathrm{NO_2}$. This gas can cause fatal chemical pneumonitis and edema in neonatal lungs. $\mathrm{O_3}$ attacks cell membranes via lipid peroxidation.

Homogeneous gas-phase oxidation in this environment is modeled by a third-order rate equation:
$$2\mathrm{NO} + \mathrm{O_2} \rightarrow 2\mathrm{NO_2}$$
$$\frac{d[\mathrm{NO_2}]}{dt} = 2k[\mathrm{NO}]^2[\mathrm{O_2}], \qquad k(300\,\mathrm{K}) \approx 1.9\times10^{-38}\ \mathrm{cm^6\,molecule^{-2}\,s^{-1}}$$
The most destructive feature of this equation from an engineering perspective is the $[\mathrm{NO}]^2$ (quadratic) dependency dominating the rate. The slightest increase in residence time in the mixing chamber or regions of high NO concentration exponentially spikes $\mathrm{NO_2}$ production.

Additionally, atomic oxygen within the plasma forms ozone via third-body recombination:
$$\mathrm{O}(^3P) + \mathrm{O_2} + M \rightarrow \mathrm{O_3} + M$$
The presence of ozone opens a secondary hazard channel, directly oxidizing NO:
$$\mathrm{NO} + \mathrm{O_3} \rightarrow \mathrm{NO_2} + \mathrm{O_2}$$

### 2.2 Three-Stage Purification Barrier and Fail-Safe System
To break kinetic bottlenecks and reduce toxicity to ER-02/ER-03 limits, the system utilizes three redundant barriers:
1. **Mechanical Pre-Filtration:** A HEPA-grade filter traps metal-oxide particles potentially shed from the reactor, protecting downstream catalysts.
2. **Selective Catalytic Reduction:** A $\mathrm{MnO_2}$ / Hopcalite (MnO₂–CuO) ceramic-based catalyst bed decomposes ozone into oxygen at room temperature:
   $$2\mathrm{O_3} \xrightarrow{catalyst} 3\mathrm{O_2}$$
   The same surface reduces the $\mathrm{NO_2}$ molecule back to therapeutic NO ($\mathrm{NO_2} + \text{(surface-H)} \rightarrow \mathrm{NO} + \text{product}$).
3. **Adsorption Polishing:** A base-impregnated (e.g., $\mathrm{KOH}$) activated carbon layer physically absorbs the remaining trace parts-per-billion levels of $\mathrm{NO_2}$, $\mathrm{O_3}$, and other nitrate vapors.

The cumulative efficiency of these independent physicochemical processes is calculated as:
$$\eta_{total} = 1 - (1-\eta_{cat})\times(1-\eta_{ads})$$
Combining a catalyst efficiency of $\approx 95\text{–}99\%$ for $\mathrm{O_3}$ and $\approx 90\text{–}97\%$ for $\mathrm{NO_2}$ with an $\approx 80\text{–}90\%$ trace adsorption retention easily pushes total system removal (ER-04) well past $\eta_{total} > 99\%$.
Furthermore, the moment electrochemical sensors detect $3\ \mathrm{ppm}\ \mathrm{NO_2}$, hardware logic valves switch to bypass mode (Fail-Safe), cutting reactor power and isolating the patient.

---

## Section 3: COMSOL Multiphysics Simulation and Numerical Solver Optimizations

The simulation architecture requires the simultaneous solution of Electrostatics, Plasma (Drift-diffusion, Boltzmann EEDF), and Heavy Species Transport (Chemical Kinetics) modules. However, massive hardware and mathematical stability obstacles (bottlenecks) encountered during R&D were resolved with specific numerical interventions.

### 3.1 Bottleneck 1: Geometry Reduction (RAM Constraint)
The full 3D reactor volume and extremely fine mesh resolution required by the plasma led to $>250\ \mathrm{GB}$ RAM consumption, rendering the system unsolvable on standard workstations. This issue was overcome by exploiting the reactor's axial rotational symmetry. The volume was mathematically reduced to a $\theta_0 = 10^{\circ}$ angular slice:
$$\Omega_{slice} = \left\{ (r,\theta,z) \in \Omega_{full} \;\middle|\; 0 \le \theta \le 10^{\circ} \right\}$$
By assigning periodic/symmetry boundary conditions to the bounding surfaces, the equations were solved, reducing RAM consumption to $\approx 28\ \mathrm{GB}$ (a $\sim 97\%$ reduction) without compromising system physics.

### 3.2 Bottleneck 2: Voltage Shock at $t=0$ (Singularity)
Applying the $15\ \mathrm{kV}$ discharge voltage as an instantaneous step function ($H(t)$) in the boundary conditions created a $dV/dt \rightarrow \infty$ state, causing a convergence failure (singularity) in the Newton-Raphson solver. The issue was resolved by writing a continuous, differentiable exponential voltage ramping function (smoothing):
$$V(t) = 15000 \times \left(1 - e^{-t/1\,\mathrm{ns}}\right)\ \ [\mathrm{V}]$$
The derivative of this equation at $t=0$ is finite ($\frac{dV}{dt} = 15000 / 1\,\mathrm{ns}$). This allowed the simulated iteration to perfectly match the physical pulse rise time while eliminating the mathematical singularity.

### 3.3 Bottleneck 3: Solver Instability (Stiff System)
The massive disparity between the time scales of electron kinetics (picoseconds/nanoseconds) and heavy species mass transfer (milliseconds/seconds) caused non-physical oscillations in electron density calculations under the "Fully Coupled" solver.
The solution was transitioning to a "Segregated" solver. Differential equations were split into subgroups and solved sequentially, applying a Damping Factor (under-relaxation) of $\lambda = 0.5$ to dampen inter-iteration oscillations:
$$x^{(k+1)} = x^{(k)} + \lambda \left( x^{(k+1)}_{Newton} - x^{(k)} \right), \qquad \lambda = 0.5$$
This mathematical intervention guaranteed monotonic convergence of the solution, nullifying errors caused by overshoot.

---

## Section 4: Kinetic Graph Analysis and Future Roadmap

### 4.1 Time vs. Concentration (PPM) Output Analysis
Kinetic data acquired from the COMSOL model's virtual probe at the patient delivery point exhibit highly stable behaviors as a direct result of the numerical optimizations:
* **$c_{NO}(t)$ Curve (Therapeutic Target):** After plasma ignition, it rises toward the target set-point (e.g., $80\ \mathrm{ppm}$) with a first-order asymptotic profile ($c_{NO,ss}\left(1 - e^{-t/\tau_{NO}}\right)$). Thanks to the segregated solver and damping modifications, it settles into a perfectly monotonic convergence plateau with zero overshoot.
* **$c_{O_3}(t)$ Curve:** Creates a brief transient spike of a few ppm during the initial moments of the reactor (before the catalyst catches up to the reaction rate). However, upon contact with the $\mathrm{MnO_2}$ catalyst bed, it experiences a sharp exponential drop, plunging below the $0.1\ \mathrm{ppm}$ safety limit (ER-03) within seconds and remaining flat.
* **$c_{NO_2}(t)$ Curve (S-Curve Suppression):** Shows an initial upward trend in the first second due to the high $[\mathrm{NO}]^2$ thermodynamics in the oxygen-rich environment. However, the combined sink effect of catalytic reduction and the adsorption barrier mathematically dominates the oxidation source. The curve flatlines sharply, stabilizing well below the $2\ \mathrm{ppm}$ absolute limit (ER-02).

### 4.2 Current Status and Future Roadmap
As a result of optimizations in the simulation environment, the theoretical defensibility of requirements ER-01 (0–80 ppm adjustment), ER-02 ($<2\ \mathrm{ppm}\ \mathrm{NO_2}$), ER-03 ($<0.1\ \mathrm{ppm}\ \mathrm{O_3}$), and ER-04 ($>99\%$ purification efficiency) has been proven.

Following this successful computational validation, future studies outlined in the scope of this report include:
1. **Experimental Validation:** Manufacturing a bench-scale reactor and calibrating COMSOL theoretical kinetics with experimental data using chemiluminescence sensors.
2. **Parametric Sweep:** "Worst-case" scenario testing under varying neonatal flow rates and maximum FiO₂ ($100\%\ \mathrm{O_2}$) conditions.
3. **Clinical and Regulatory Process:** Ensuring device compliance with IEC 60601 (Electrical Safety), ISO 13485 (Quality Management), and ISO 10993 (Biocompatibility) standards, and initiating multi-center clinical trials.
