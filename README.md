# CMOS-Circuit-Design-and-SPICE-Simulation-using-SKY130-VSD-IAT-Workshop
My notes and lab work from the SKY130 CMOS Design Workshop.

# SKY130 CMOS Circuit Design Workshop (VSD-IAT)

This repository contains my notes, lab exercises, and SPICE simulations from a 5-day workshop [11 August 2025 to 20 August 2025] on CMOS inverter design using the **SKY 130nm PDK**.

---

## 🛠️ Tools Used
- **SKY130 PDK**
- **ngspice**
- **Oracle Linux environment** (for running simulations)

---

# Day 1 – Basics of NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---

## Module 1: Introduction to Circuit Design & SPICE Simulations

### Why do we need SPICE simulations?

- Circuit design is about using **PMOS** and **NMOS** in required connections to get functionality.  
- In **SPICE**, we feed input waveforms to the circuit and observe output characteristics.  
- Delay depends on **W/L ratio**. W/L controls current → current controls waveform → waveform controls delay.  
- To choose W/L for a required delay, **SPICE simulation** is used.  
- Whole flow like clock tree, crosstalk, timing closure is based on **SPICE results**.  
- Cell delay tables used in **STA** and physical design come from SPICE simulations.  
- Simulations verify if models are accurate and if **STA values** are correct.  

### MOSFET Structure - NMOS

![WhatsApp Image 2025-08-19 at 11 46 38 PM](https://github.com/user-attachments/assets/f0613255-3042-4658-8419-e9607e056194)

- **NMOS** is a 4-terminal device, built on a **p-type substrate**.  
- Two **SiO₂ isolation regions** are used to differentiate between two transistors.  
- **n+ diffusion regions** form the Source and Drain terminals.  
- A **thin gate oxide** is placed above the substrate (by etching).  
- A **poly-Si or metal gate** is placed above the oxide. This forms the **Gate terminal** that drives the NMOS.  

**Four terminals:**  
- **G (Gate)**  
- **S (Source)**  
- **D (Drain)**  
- **B (Body)** (usually grounded, but any potential applied here tunes the threshold voltage).  

**PMOS** has an inverted structure similar to NMOS.

---

### Threshold Voltage (Vt)

**Basic condition:**  
- Vgs = 0, Drain, Source, and Bulk grounded.  
- Substrate–Source (B–S) and Substrate–Drain (B–D) form **p–n junction diodes**.  
- Both junctions are **OFF** due to 0V bias → Source–Drain resistance is very high.  

**Apply +ve Vgs:**  
- Gate plate acts like a capacitor plate and becomes positively charged.  
- Negative charges accumulate on the substrate surface, repelling positive charges.  
- A **depletion region** is formed due to reverse bias on the p–n junction.  
- As Vgs increases, the depletion width increases. The top portion of the p-substrate gets inverted.  
- This condition is called **strong inversion**.  
- The Vgs at which strong inversion occurs = **Threshold voltage (Vt)**.  

**Further increase in Vgs:**  
- Electrons from the n+ regions (Source) are attracted into the channel.  
- Channel width increases, while depletion width remains unchanged.  
- A continuous **n-channel** is formed between Source and Drain.  
- Channel conductivity is modulated by Vgs.  
- This is the **cut-off to conduction transition**.  

**Effect of substrate bias:**  
- **Case 1:** Vsb = 0 → Normal inversion.  
- **Case 2:** Vsb = +ve → Depletion width near Source increases (additional reverse bias).  
- No change near Drain.  
- Inversion occurs later (i.e., higher Vgs required compared to Case 1).  

---

### Resistive / Linear Mode of Operation

- Consider **Vgs > Vt**.  
- The induced channel charge is proportional to (Vgs – Vt).  

**Example:**  
- Vgs = 1 V, Vds = 0.05 V (small), Vt (NMOS) = 0.45 V.  
- Since Vgs > Vt, the transistor is **ON**.  

**Voltage along the channel:**  
- Not constant.  
- Near Source ≈ 0 V, near Drain ≈ Vds.  

- The effective channel length is reduced due to fabrication processes.  

**Axis definition:**  
- x-axis: along the channel.  
- y-axis: perpendicular to the channel.  

At a point x along the channel:  
- Voltage = V(x).  
- Effective Gate-to-channel voltage = Vgs – V(x).  
- Induced charge, Q–(x), is proportional to:

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

- t_{ox}: oxide thickness (constant, technology dependent).  

**First-order analysis:**  
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

- k_n': process transconductance parameter.  
- k_n = k_n' * W/L: gain factor.  

- Final equation:  

$$
I_d = k_n \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
$$  

- Quadratic term small → for Vds ≤ (Vgs − Vt):  

$$
I_d = k_n (V_{gs} - V_t)V_{ds}
$$  

---

## MOSFET Operation and SPICE Simulations

### Assumptions:
- Vgs = 0.5, 1, 1.5, 2, 2.5 V  
- (Vgs − Vt) = 0.05, 0.55, 1.05, 1.55, 2.05 V  
- Vds swept from 0 to 1.55 V  
- Vt = 0.45 V  

- SPICE simulations used instead of manual calculations.

---

### Effect of Increasing Vds

- Channel voltage = Vgs − Vds  

**Case 1: Vds < (Vgs − Vt)** → channel continuous.  
**Case 2: Vds ≈ (Vgs − Vt)** → pinch-off starts near drain.  
**Case 3: Vds > (Vgs − Vt)** → saturation (channel vanishes near drain).  

> Pinch-off condition:  
>
> $V_{DS} \geq (V_{GS} - V_T)$

---

### Drain Current Equations

- **Linear region:**

$$
I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
$$

- **Saturation region:**

$$
I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2
$$

- Including channel length modulation:

$$
I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2 \cdot (1 + \lambda V_{DS})
$$

---

### SPICE Simulations

- Provide **model parameters** and **netlist**.  
- SPICE derives responses → used for device characteristics and delay calculations.

---

### MOSFET Models

$$
V_T = V_{T0} + \gamma \left(\sqrt{| -2\phi_f + V_{SB}|} - \sqrt{| -2\phi_f|}\right)
$$

$$
\gamma = \frac{\sqrt{2qN_A\varepsilon_{Si}}}{C_{ox}}, \quad
\phi_f = -\phi_t \ln\left(\frac{N_A}{n_i}\right)
$$

---

### Summary of Equations

- **Linear region:**

$$
I_D = k_n \left[(V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2}\right]
$$

- **Saturation region:**

$$
I_D = \frac{k_n'}{2} \cdot \frac{W}{L} \cdot (V_{GS}-V_T)^2 (1 + \lambda V_{DS})
$$
