# BIONOx Presentation Slides & Speaker Notes

## Slide 1: Clinical Motivation
**Title:** CLINICAL MOTIVATION: Inhaled Nitric Oxide (iNO) Therapy and Logistical Constraints
**Slide Text:**
* Inhaled NO is a clinically proven treatment that provides selective pulmonary vasodilation in pulmonary hypertension and hypoxic respiratory failure.
* **Challenges of Current Methods:**
  * Requires Pressurized Cylinder + External Mixing.
  * Requires a stock of compressed $\mathrm{NO/N_2}$ gas cylinders.
  * Risk of treatment interruption during cylinder changes.
  * Limited portability and heavy in-hospital logistical burden.
  * Fixed gas mixture ratio provides low dosage flexibility.
* **The BIONOx Approach:**
  * "On-demand" plasma synthesis from ambient air (no cylinders).
  * Continuous, precise dose adjustment between $0\text{–}80\ \mathrm{ppm}$.
  * Closed-loop, real-time toxicity monitoring.
  * Compact, bedside integration.

**Speaker Notes:**
"Respected Jury Members, I want to start with our clinical motivation. Today, the gold standard for treating Pulmonary Hypertension of the Newborn (PPHN) in neonatal intensive care units is inhaled Nitric Oxide therapy. However, current systems depend on high-pressure and toxic gas cylinders, which are a logistical nightmare. Even a seconds-long interruption when a cylinder runs out can have fatal consequences for critical patients. With the BIONOx project, we are breaking this paradigm. We are eliminating the cylinders. Our device takes the ambient air at the patient's bedside and synthesizes nitric oxide exactly when needed and at the precise desired dose (0-80 ppm). This is both a logistical revolution and a guarantee of uninterrupted treatment."

## Slide 2: Design Requirements
**Title:** DESIGN REQUIREMENTS: Binding Engineering Parameters
**Slide Text:**
* **ER-01:** Therapeutic NO production: $0\text{–}80\ \mathrm{ppm}$, continuously adjustable ($80\ \mathrm{ppm}$ upper hard limit).
* **ER-02:** Toxic $\mathrm{NO_2}$ concentration: $< 2\ \mathrm{ppm}$ continuously (Alarm + Safe Mode triggered at $\ge 3\ \mathrm{ppm}$).
* **ER-03:** Ozone ($\mathrm{O_3}$) concentration: $< 0.1\ \mathrm{ppm}$ (Filter-saturation alarm).
* **ER-04:** Purification system efficiency: $> 99\%$ ($\mathrm{NO_2} + \mathrm{O_3} + \text{ROS/RNS}$) (Safe Mode triggered upon efficiency drop).

**Speaker Notes:**
"The device we are developing is a life-support unit; therefore, our engineering tolerances are extremely strict. These four fundamental parameters (ER-01 to ER-04) lying at the heart of our design are non-negotiable rules for us. $80\ \mathrm{ppm}$ is our therapeutic target, but more importantly, our toxicity limits. Keeping $\mathrm{NO_2}$ below $2\ \mathrm{ppm}$ and $\mathrm{O_3}$ below $0.1\ \mathrm{ppm}$ is not just a goal, it's an absolute necessity. If the system reads $3\ \mathrm{ppm}$ of $\mathrm{NO_2}$, a hardware fail-safe mode is instantly triggered. Throughout this presentation, I will detail how we physically and chemically maintain these aggressive safety thresholds."

## Slide 3: System Architecture
**Title:** SYSTEM ARCHITECTURE: BIONOx Signal Flow
**Slide Text:**
* **1. Plasma Reactor:** NO synthesis from ambient air via cold plasma ($T_e \gg T_{gas}$).
* **2. Mixing Line:** Injection of synthesized NO into the ventilator's $\mathrm{O_2}$-rich flow with a short contact time.
* **3. Purification Barrier:** Filter + Catalytic Reduction + Adsorption stages eliminate $\mathrm{NO_2}$/$\mathrm{O_3}$ at $>99\%$.
* **4. Real-Time Sensor:** Electrochemical $\mathrm{NO_2}$/$\mathrm{O_3}$ monitoring for closed-loop dose control.
* **5. Patient Ventilator Line:** Purified, safe, and therapeutic NO flow reaches the patient.
* **Safety Protocol:** Each stage must meet its own safety criteria before proceeding to the next.

**Speaker Notes:**
"Looking at our architectural diagram, we see the critical journey from air entering the device to reaching the patient. We produce NO in the plasma reactor, mix it with an oxygen-rich flow, pass it through chemical barriers, and verify it with sensors. The most critical engineering philosophy here is 'Cascading Safety'. No gas can pass to the patient line without approval from our electrochemical sensors. If the sensors detect any toxic threshold breach, a mechanical isolation valve engages, and plasma power is cut within milliseconds."

## Slide 4: Plasma Physics (Thermodynamic Non-Equilibrium)
**Title:** SECTION 1 - PLASMA PHYSICS: Thermodynamic Non-Equilibrium ($T_e \gg T_{gas}$)
**Slide Text:**
* **Electron temperature:** $1\text{–}5\ \mathrm{eV}$ (equivalent to $\approx 10^4\text{–}5 \times 10^4\ \mathrm{K}$).
* **Gas / ion temperature:** $\approx 300\text{–}320\ \mathrm{K}$ (close to body temperature).
* **Why does the gas remain cold?** 
  * $\Delta\varepsilon_{max}/\varepsilon = 4\cdot(m_e/M) \approx 7.6 \times 10^{-5}$ (for $\mathrm{N_2}$).
  * The electron mass is $\approx 10^4$ times smaller; the fractional energy transferred in an elastic collision is limited by this ratio.
* **Result:** While electrons accelerate to high energies, the gas temperature remains around $300\ \mathrm{K}$. There is no risk of airway thermal injury.

**Speaker Notes:**
"Esteemed colleagues, the biggest challenge in producing NO from ambient air is the high energy required. Classical thermal plasmas or arc discharges reach temperatures of thousands of degrees; you cannot feed that into a neonate's respiratory tract. BIONOx uses 'Cold Atmospheric Plasma'. As you can see in the formula, due to the massive mass disparity between electrons and neutral gas molecules, electrons do not spend their acquired energy heating the gas. While electrons reach energies equivalent to 50,000 Kelvin, the gas remains at body temperature (around 300 K). We are not delivering heat to the patient, only chemical potential."

## Slide 5: Computational Bottlenecks (COMSOL)
**Title:** SECTION 3 - NUMERICAL OPTIMIZATIONS: Geometry Reduction & Voltage Ramping
**Slide Text:**
* **Bottleneck 1: Geometry & RAM Constraint**
  * Full 3D Model required $>250\ \mathrm{GB}$ RAM (exceeds hardware limits).
  * **Solution:** Utilized axial symmetry to reduce the model to a $10^{\circ}$ slice ($\approx 28\ \mathrm{GB}$ RAM, a $97\%$ reduction).
* **Bottleneck 2: Voltage Shock at $t=0$**
  * $15\ \mathrm{kV}$ applied as a Heaviside step function caused solver singularity ($dV/dt \rightarrow \infty$).
  * **Solution:** Implemented continuous exponential voltage ramping $V(t) = 15000 \times \left(1 - e^{-t/1\,\mathrm{ns}}\right)$, matching physical pulse rise time.

**Speaker Notes:**
"Before moving to prototype manufacturing, we simulated the process in COMSOL Multiphysics. However, we hit severe hardware limits. A full 3D mesh required over 250 GB of RAM. We overcame this by applying symmetry boundary conditions, cutting a 10-degree wedge of the reactor, reducing our RAM load to 28 GB without losing any physical accuracy. Furthermore, hitting the solver with a 15-kilovolt step function at time zero caused infinite derivatives and solver crashes. We solved this mathematically by writing a continuous exponential ramping function that rises over 1 nanosecond, which perfectly mimics the actual physical rise time of our pulse generator."

## Slide 6: Kinetic Graph Analysis
**Title:** SECTION 4 - KINETIC ANALYSIS: Time-Concentration Behavior at Patient Line
**Slide Text:**
* **$c_{NO}$ Curve (Therapeutic):** Exhibits a smooth, monotonic rise, perfectly converging to the target set-point ($0\text{–}80\ \mathrm{ppm}$) without overshoot.
* **$c_{O_3}$ Curve (Ozone):** Creates a brief transient spike, followed by a rapid, aggressive decline via catalytic decomposition, settling firmly below the $0.1\ \mathrm{ppm}$ plateau.
* **$c_{NO_2}$ Curve (Toxicity):** Despite initial upward pressure from the oxygen-rich environment, the curve is sharply flattened by the catalytic/adsorption sink, remaining strictly below the $2\ \mathrm{ppm}$ safety threshold.

**Speaker Notes:**
"This graph is the ultimate proof of our design. According to COMSOL data, the black curve representing NO smoothly reaches the 80 ppm target and locks in. The blue line representing ozone creates a momentary spike inside the plasma, but within milliseconds of hitting the manganese dioxide catalyst, it crashes and dies below 0.1 ppm. The red nitrogen dioxide curve attempts to rise when it sees oxygen, but due to the aggressive suppression of our filter barriers, it flatlines far below the 2 ppm red alarm line, deep within the safety zone."
