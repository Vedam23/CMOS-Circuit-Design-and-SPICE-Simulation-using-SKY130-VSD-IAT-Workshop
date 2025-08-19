# CMOS-Circuit-Design-and-SPICE-Simulation-using-SKY130-VSD-IAT-Workshop
My notes and lab work from the SKY130 CMOS Design Workshop.

# SKY130 CMOS Circuit Design Workshop (VSD-IAT)

This repository contains my notes, lab exercises, and SPICE simulations from a 5-day workshop on CMOS inverter design using the **Skywater 130nm PDK (SKY130)**.

---

## 📖 Workshop Outline

### **Day 1 – MOSFET Basics & SPICE**
- MOSFET structure, threshold voltage, body effect.
- SPICE basics: `.op`, `.dc`, `.tran`.
- **Lab:** Simulate Id–Vds curve, extract threshold voltage.

### **Day 2 – CMOS Inverter & VTC**
- Velocity saturation, CMOS inverter introduction.
- Voltage Transfer Characteristic (VTC).
- **Lab:** Generate VTC, find switching threshold.

### **Day 3 – Switching Threshold & Dynamics**
- Effect of W/L ratios on switching point.
- **Lab:** Observe effect of different transistor sizes on inverter switching.

### **Day 4 – Noise Margin**
- Noise margin definitions: `NMH = VOH – VIH`, `NML = VIL – VOL`.
- **Lab:** Extract noise margins from VTC.

### **Day 5 – Robustness Analysis**
- Effect of supply voltage variation.
- Effect of process/device variation.
- **Lab:** Simulate inverter behavior under variations.

---

## 🛠️ Tools Used
- **SKY130 PDK**
- **ngspice**
- Linux environment (for running simulations)

---

## 📌 References
- Workshop material: https://www.vlsisystemdesign.com/cmos-circuit-design-spice-simulation-using-sky130-technology/
- https://github.com/kunalg123/sky130CircuitDesignWorkshop




# Day 1 – Basics of NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---

## Module 1: Introduction to Circuit Design & SPICE Simulations

### L0-1: Why do we need SPICE simulations?

- Circuit design is about using PMOS and NMOS in required connections to get functionality.  
- In SPICE, we feed input waveforms to the circuit and observe output characteristics.  
- Delay depends on W/L ratio. W/L controls current → current controls waveform → waveform controls delay.  
- To choose W/L for a required delay, SPICE simulation is used.  
- Whole flow like clock tree, crosstalk, timing closure is based on SPICE results.  
- Cell delay tables used in STA and physical design come from SPICE simulations.  
- Simulations verify if models are accurate and if STA values are correct.  

### **L1-2: Introduction to basic elements in circuit design – NMOS**
### MOSFET Structure

- NMOS is a 4-terminal device, built on a **p-type substrate**.  
- Two **SiO₂ isolation regions** are used to differentiate between two transistors.  
- **n+ diffusion regions** form the Source and Drain terminals.  
- A **thin gate oxide** is placed above the substrate (by etching).  
- A **poly-Si or metal gate** is placed above the oxide. This forms the Gate terminal that drives the NMOS.  

So, the four terminals are:  
- **G (Gate)**  
- **S (Source)**  
- **D (Drain)**  
- **B (Body)** (usually grounded, but any potential applied here tunes the threshold voltage).  

PMOS has an inverted structure similar to NMOS.

---

### Threshold Voltage (Vt)

- **Basic condition:**  
  - Vgs = 0, Drain, Source, and Bulk grounded.  
  - Substrate–Source (B–S) and Substrate–Drain (B–D) form **p–n junction diodes**.  
  - Both junctions are OFF due to 0V bias → Source–Drain resistance is very high.  

- **Apply +ve Vgs:**  
  - Gate plate acts like a capacitor plate and becomes positively charged.  
  - Negative charges accumulate on the substrate surface, repelling positive charges.  
  - A **depletion region** is formed due to reverse bias on the p–n junction.  
  - As Vgs increases, the depletion width increases. The top portion of the p-substrate gets inverted.  
  - This condition is called **strong inversion**.  
  - The Vgs at which strong inversion occurs = **Threshold voltage (Vt)**.  

- **Further increase in Vgs:**  
  - Electrons from the n+ regions (Source) are attracted into the channel.  
  - Channel width increases, while depletion width remains unchanged.  
  - A continuous **n-channel** is formed between Source and Drain.  
  - Channel conductivity is modulated by Vgs.  
  - This is the **cut-off to conduction transition**.  

- **Effect of substrate bias:**  
  - Case 1: Vsb = 0 → Normal inversion.  
  - Case 2: Vsb = +ve → Depletion width near Source increases (additional reverse bias).  
  - No change near Drain.  
  - Inversion occurs later (i.e., higher Vgs required compared to Case 1).  

---

### Resistive / Linear Mode of Operation

- Consider **Vgs > Vt**.  
- The induced channel charge is proportional to (Vgs – Vt).  

- **Example:**  
  - Vgs = 1 V, Vds = 0.05 V (small), Vt (NMOS) = 0.45 V.  
  - Since Vgs > Vt, the transistor is ON.  

- **Voltage along the channel:**  
  - Not constant.  
  - Near Source ≈ 0 V, near Drain ≈ Vds.  

- The effective channel length is reduced due to fabrication processes.  

- **Axis definition:**  
  - x-axis: along the channel.  
  - y-axis: perpendicular to the channel.  

- At a point x along the channel:  
  - Voltage = V(x).  
  - Effective Gate-to-channel voltage = Vgs – V(x).  
  - The amount of induced charge, Q–(x), is proportional to:

    $$
    Q_i(x) = -C_{ox}\left[(V_{gs} - V(x)) - V_t\right]
    $$

---

### Derivation of Current Equations

- Gate oxide capacitance:  

  $$
  C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}
  $$  

- Oxide permittivity:  

  $$
  \epsilon_{ox} = 3.97 \times \epsilon_0 = 3.5 \times 10^{-11} \, F/m
  $$  

- $t_{ox}$: oxide thickness (constant, technology dependent).  

- **First-order analysis (from device perspective):**  
  - Two types of current exist:  
    - **Drift current** → due to potential difference.  
    - **Diffusion current** → due to difference in carrier concentration.  

- **Drift current (Id):**  

  $$
  I_d = \text{velocity of carriers} \times \text{available charge over channel width}
  $$  

  $$
  I_d = -v_n(x) \cdot Q_i(x) \cdot W
  $$  

- **Carrier velocity:**  

  $$
  v_n(x) = \mu_n \cdot E = \mu_n \cdot \frac{dV}{dx}
  $$  

- Substituting:  

  $$
  I_d = \mu_n \cdot \frac{dV}{dx} \cdot C_{ox} \cdot \left[(V_{gs} - V(x)) - V_t\right] \cdot W
  $$  

- Integrating over channel length L (LHS) and drain–source voltage Vds (RHS):  

  $$
  I_d = \mu_n \cdot C_{ox} \cdot \frac{W}{L} \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
  $$  

- Simplification using process transconductance parameter:  

  $$
  I_d = k_n' \cdot \frac{W}{L} \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
  $$  

  - $k_n'$: process transconductance parameter (technology constant).  
  - $k_n = k_n' \cdot \frac{W}{L}$: gain factor.  

- Final equation:  

  $$
  I_d = k_n \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
  $$  

- The quadratic term is very small and can be neglected for **Vds ≤ (Vgs – Vt)**.  

- Therefore, in the **linear region**:  

  $$
  I_d = k_n (V_{gs} - V_t)V_{ds}
  $$  

  which is linear in Vds.

## MOSFET Operation and SPICE Simulations

### Assumptions:
- Vgs = 0.5, 1, 1.5, 2, 2.5 V  
- (Vgs − Vt) = 0.05, 0.55, 1.05, 1.55, 2.05 V  
- Vds swept from 0 to 1.55 V  
- Vt = 0.45 V  

Since it is not feasible to calculate for each case manually, we can use **SPICE engine simulations**.

---

### Effect of Increasing Vds

- Channel voltage = Vgs − Vds  
- Example: Vgs = 1 V, Vds = 0.05 V, Vt = 0.45 V → surface inversion happens  

Now, as we increase Vds from 0.05 → 0.95:  

#### Case 1:  
- Condition: Vds < (Vgs − Vt)  
- Both source and drain edges of the channel are at higher potential.  
- Channel is continuous.  

#### Case 2:  
- Example: Vgs = 1 V, Vds = 0.55 V, Vgs − Vt = 0.55 V  
- Near source region → channel is already formed.  
- Near drain region → channel is disappearing.  
- Channel is tapered at drain side → **pinch-off starts**.  

#### Case 3:  
- Condition: Vds > (Vgs − Vt)  
- No channel near drain → channel vanishes near drain side, but remains near source.  
- This is the **saturation region**.  

> Pinch-off condition:  
>
> $$
> V_{DS} \geq (V_{GS} - V_T)
> $$

---

### Drain Current Equations

- In **linear region**:

  $$
  I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
  $$

- In **saturation** (replace $V_{DS}$ by $V_{GS}-V_T$):

  $$
  I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2
  $$

At first, it seems drain current is only a function of constants → like a perfect current source.  
But in reality, **channel length modulation** occurs:  

$$
I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2 \cdot (1 + \lambda V_{DS})
$$

where  
- $\lambda$ = channel length modulation parameter.  
- Current is not completely independent of Vds, but slightly dependent on λ.

---

### SPICE Simulations

SPICE is an engine with **predefined models**.  
- We provide:
  1. **Model parameters** (technology constants)  
     - $V_{to}, \gamma, k_n', \lambda$  
     - Given in the **model file**
  2. **SPICE netlist** (circuit description)

Then SPICE software derives the response, which is used to calculate device characteristics (e.g., delays in digital cells).

---

### MOSFET Models

Threshold voltage:

$$
V_T = V_{T0} + \gamma \left(\sqrt{| -2\phi_f + V_{SB}|} - \sqrt{| -2\phi_f|}\right)
$$

where:

$$
\gamma = \frac{\sqrt{2qN_A\varepsilon_{Si}}}{C_{ox}}, \quad
\phi_f = -\phi_t \ln\left(\frac{N_A}{n_i}\right)
$$

---

### Summary of Equations

- **Linear region**:

  $$
  I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
  $$

- **Saturation region**:

  $$
  I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2 (1 + \lambda V_{DS})
  $$

---

### Next Step

Provide the **SPICE netlist** for simulation.

**Lab Code:**
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end

**Lab Output:**  
<img width="1852" height="907" alt="Screenshot from 2025-08-19 21-35-39" src="https://github.com/user-attachments/assets/92a3d154-1147-4e71-a79f-0e4cafac9fcc" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 21-45-58" src="https://github.com/user-attachments/assets/c64a4db0-ca8e-4cf3-bf3f-93fd69be07d3" />

# Day 2 – Velocity Saturation & CMOS Inverter VTC

---

### Velocity Saturation

- In short-channel MOSFETs, carrier velocity **saturates** at high electric fields.  
- Drain current is no longer proportional to Vgs – instead, it **flattens out**.  
- Important for sub-micron processes like SKY130.  

**Lab:**  
- Run ID–Vgs simulations for different channel lengths.
- Code for graph between Ids and Vds for short channel devices:
- *Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end

Output:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-07-41" src="https://github.com/user-attachments/assets/b602b473-9fa0-43d9-ac97-998eef541560" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-07-35" src="https://github.com/user-attachments/assets/bb82033f-b9c0-48fe-b306-d720256324a1" />

Code to calculate Threshold voltage for Id versus Vgs curve:
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.1

.control

run
display
setplot dc1
.endc

.end

Output:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-08-31" src="https://github.com/user-attachments/assets/b4ba7541-cc3c-4950-b882-faa934f49823" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-08-23" src="https://github.com/user-attachments/assets/2a7dc473-37e4-4ff4-ac6c-bd1146545ad0" />
 To calculate Threshold voltage:
Code:

- Observation:
- For lower Vgs values -> quadratic behaviour, higher values -> linear behaviour.

---

### CMOS Inverter & Voltage Transfer Characteristic (VTC)

- **CMOS inverter** = PMOS + NMOS connected complementarily.  
- Output switches between Vdd and 0 depending on input.  
- **VTC**: plot of Vout vs Vin.  

**Switching threshold (Vm):**  
- Point where Vin = Vout.  
- Controlled by relative W/L ratios of NMOS & PMOS.  

**Lab:**  
- Generate VTC using DC sweep of Vin.  
- Find switching threshold Vm.  

---

# Day 3 – Switching Threshold & Transistor Sizing

---

### Effect of W/L Ratios

- W/L ratio directly impacts **drive strength** of MOSFET.  
- Larger W/L → higher current drive.  
- In CMOS inverter, relative sizing of NMOS and PMOS sets the **switching threshold**.  

**Observations:**  
- Increasing NMOS size → switching threshold shifts down (logic favors ‘0’).  
- Increasing PMOS size → switching threshold shifts up (logic favors ‘1’).  

**Lab:**  
- Simulate inverters with different W/L ratios.
  
Code to plot VTC (tt):
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end  

Output:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-20-23" src="https://github.com/user-attachments/assets/99f17088-72f2-49e1-a6a8-c0cffc3eac80" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-22-11" src="https://github.com/user-attachments/assets/94c09826-2f90-448d-a048-dc432cc618d4" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-22-18" src="https://github.com/user-attachments/assets/f7b5d788-3aea-4d88-80dc-c3164f77e70f" />

Code for Transient Analysis:
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands

.tran 1n 10n

.control
run
.endc

.end


Output:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-28-06" src="https://github.com/user-attachments/assets/86a78c01-0cc9-4d20-bdbe-4e5681afc733" />

To Calculate rising edge:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-27-58" src="https://github.com/user-attachments/assets/c52d0c2a-b71f-4a0d-bdf0-6c9d51d63cc4" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-28-17" src="https://github.com/user-attachments/assets/41969100-ca17-4036-b313-54e1c6c0a296" />

To Calculate falling edge:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-32-03" src="https://github.com/user-attachments/assets/08d81608-64bb-483b-b5a9-0bf0c1326660" />
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-32-14" src="https://github.com/user-attachments/assets/ae16d8fa-b451-4849-9b24-8a9fdd22e8a4" />

---

# Day 4 – Noise Margins

---

### Noise Margin Basics

- **Noise Margin High (NMH):**  
  \[
  NMH = VOH - VIH
  \]

- **Noise Margin Low (NML):**  
  \[
  NML = VIL - VOL
  \]

Where:  
- VOH = output high voltage  
- VOL = output low voltage  
- VIH = min input recognized as logic ‘1’  
- VIL = max input recognized as logic ‘0’  

**Lab:**  
- Extract values from VTC curve.  
- Calculate NMH & NML.  
- Observe trade-off with transistor sizing.  

---

# Day 5 – Robustness Analysis

---

### Supply Voltage Variation

- Lower Vdd → reduced noise margins, slower transitions.  
- Higher Vdd → faster switching, more dynamic power consumption.  

### Process / Device Variation

- Variations in threshold voltage, mobility, oxide thickness affect inverter characteristics.  
- Robust design must tolerate variations.  

**Lab:**  
- Simulate inverter under:  
  - Reduced Vdd  
  - Increased Vdd  
  - Modified device parameters  
- Observe effect on VTC and noise margins.  

---

