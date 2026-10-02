# BIONOx: Plasma-Derived Nitric Oxide (NO) Therapy System

Engineering design and COMSOL Multiphysics numerical validation of a life-support device developed for the treatment of Persistent Pulmonary Hypertension of the Newborn (PPHN). This system eliminates the reliance on high-pressure gas cylinders by synthesizing medical-grade Nitric Oxide (NO) directly from ambient air on-demand via cold atmospheric pressure plasma.

## 📌 Clinical Motivation and Project Goal
Current Inhaled Nitric Oxide (iNO) therapies rely on high-pressure $\mathrm{NO/N_2}$ gas cylinders, which impose heavy logistical burdens, reduce portability, and carry the risk of treatment interruption during cylinder changes. The BIONOx project aims to bypass these logistical hurdles by utilizing a dielectric-barrier discharge (DBD) reactor operating under the principle of thermodynamic non-equilibrium ($T_e \gg T_{gas}$) to synthesize NO directly from ambient air in a controlled manner.

## ⚙️ System Architecture
The end-to-end signal and gas flow consists of 5 sequential stages based on the principle of progressive redundancy:
1. **Plasma Reactor:** NO synthesis from ambient air via cold plasma.
2. **Mixing Line:** Injection of synthesized NO into the ventilator's $\mathrm{O_2}$-rich circuit.
3. **Purification Barrier:** Mechanical Filter (HEPA) + Catalytic Reduction ($\mathrm{MnO_2}$/Hopcalite) + Adsorption (Base-impregnated carbon) layers with >99% cumulative efficiency.
4. **Real-Time Sensor:** Electrochemical $\mathrm{NO_2}$/$\mathrm{O_3}$ monitoring for closed-loop dose control and fail-safe triggering.
5. **Patient Ventilator Line:** Delivery of therapeutic gas meeting all safety criteria to the patient.

## 🛡️ Binding Engineering Requirements (ER)
Considering the low toxicity tolerance of the neonatal population, the core safety parameters of the system are defined as follows:

| ID | Parameter | Requirement | Safety Trigger / Fail-Safe |
| :--- | :--- | :--- | :--- |
| **ER-01** | Therapeutic NO Production | $0\text{–}80\ \mathrm{ppm}$ (Continuously Adjustable) | $80\ \mathrm{ppm}$ upper lock (hard limit) |
| **ER-02** | Toxic $\mathrm{NO_2}$ Concentration | $< 2\ \mathrm{ppm}$ continuous (8-hour average) | $\ge 3\ \mathrm{ppm}$ $\rightarrow$ Alarm + Safe Mode |
| **ER-03** | Ozone ($\mathrm{O_3}$) Concentration | $< 0.1\ \mathrm{ppm}$ absolute limit | Filter-saturation alarm |
| **ER-04** | Purification System Efficiency | $> 99\%$ ($\mathrm{NO_2} + \mathrm{O_3} + \text{ROS/RNS}$) | Efficiency drop $\rightarrow$ Safe Mode transition |

## 💻 COMSOL Multiphysics Simulation and Solver Optimizations
The plasma-chemical behavior of BIONOx is modeled by the simultaneous solution of Electrostatics, Plasma (Drift-diffusion, Boltzmann EEDF), and Heavy Species Transport (Chemical Kinetics) interfaces. Severe computational bottlenecks were overcome with the following mathematical/numerical optimizations:
* **Geometry Reduction:** While a full 3D geometry consumed $>250\ \mathrm{GB}$ RAM, the model was reduced to a $10^{\circ}$ angular slice using rotational symmetry, dropping memory consumption to $\approx 28\ \mathrm{GB}$ ($\sim 97\%$ reduction).
* **Voltage Ramping (Singularity Resolution):** The $15\ \mathrm{kV}$ shock at $t=0$ (Heaviside step function) was replaced with a continuous, differentiable function $V(t) = 15000 \times \left(1 - e^{-t/1\,\mathrm{ns}}\right)$ to prevent convergence failure in the Newton iteration.
* **Segregated Solver & Damping:** Instability caused by the stiff time-scale difference between plasma and mass transfer was resolved by splitting the equations into subgroups and solving sequentially with a Damping Factor (under-relaxation) of $\lambda = 0.5$, guaranteeing monotonic convergence.

## 📂 Repository Contents
* `Engineering_Design_Rationale_Report.md`: Comprehensive R&D feasibility report containing thermodynamic non-equilibrium equations, $\mathrm{NO_2}$ oxidation kinetics, and numerical solver optimizations.
* `Presentation_Slides_and_Notes.md`: Internship presentation document visualizing the project vision, architectural diagrams, and COMSOL time-concentration simulation graphs.
