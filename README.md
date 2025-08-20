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

![WhatsApp Image 2025-08-20 at 4 34 07 PM](https://github.com/user-attachments/assets/03782148-a6aa-4808-8265-344ce6adbee9)

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

# Day 2 – Velocity Saturation & CMOS Inverter VTC

## **Modes of Operation**

**The modes of operation for long channel devices (>250nm) are:**  
- Cut-off region  
- Resistive region  
- Saturation region  

**The modes of operation for short channel devices (<250nm) are:**  
- Cut-off region  
- Resistive region  
- Velocity Saturation region  
- Saturation region  

---

### **Velocity Saturation**

- In short-channel MOSFETs, carrier velocity **saturates** at high electric fields.  
- Drain current is no longer proportional to Vgs – instead, it **flattens out**.  
- Important for sub-micron processes like SKY130.  

# Lab Code for graph between Ids and Vds for short channel devices:**

```spice
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
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2
.control
run
display
setplot dc1
.endc
.end
```

## Output: 
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-07-41" src="https://github.com/user-attachments/assets/b602b473-9fa0-43d9-ac97-998eef541560" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-07-35" src="https://github.com/user-attachments/assets/bb82033f-b9c0-48fe-b306-d720256324a1" />

# Lab Code to calculate Threshold voltage for Id versus Vgs curve:**
```spice
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
```
## Output:
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-08-31" src="https://github.com/user-attachments/assets/b4ba7541-cc3c-4950-b882-faa934f49823" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-08-23" src="https://github.com/user-attachments/assets/2a7dc473-37e4-4ff4-ac6c-bd1146545ad0" />

**Observation:**

- Threshold voltage is obtained by extending the linear part of the Id–Vgs plot to the x-axis; the intercept gives Vt.
- For lower Vgs values → quadratic behaviour, higher values → linear behaviour.
- Velocity Saturation causes device to saturate early.
- Point of saturation is εc (critical electric field)
- \(V_n\) (m/s) = linear for ε ≤ εc
- \(V_n\) (m/s) = constant for ε ≥ εc
### CMOS Inverter & Voltage Transfer Characteristic (VTC) 
### Transistor as a Switch 
- Acts as **open circuit** (infinite resistance) when |Vgs| < |Vt| → **OFF state**
- - Acts as **closed circuit** (finite resistance) when |Vgs| > |Vt| → **ON state**

--- 

### CMOS Inverter Operation 
#### Case 1: Vin = Vdd (High) 
- PMOS → OFF
- NMOS → ON
- Current flows from Vout to Vss → **Vout = 0V**
#### Case 2: Vin = 0V (Low) 
- PMOS → ON
- NMOS → OFF
- Current flows from Vdd to Vout → **Vout = Vdd**
 
---

# Day 3 – Switching Threshold & Transistor Sizing

---

## For the given Spice Netlist:

![WhatsApp Image 2025-08-19 at 11 14 53 PM](https://github.com/user-attachments/assets/0713e12c-a1a7-4e7c-b5f0-8a957d0a45a8) 

## SPICE code is:

***MODEL Description***
***NETLIST Description***
M1 out in vdd vdd pmos W=0.375u L=0.25u
M2 out in  0   0  nmos W=0.375u L=0.25u

cload out 0 10f

Vdd vdd 0 2.5
Vin  in 0 2.5

***SIMULATION Commands***
.op
.dc Vin 0 2.5 0.05

***.include tsmc_025um_model.mod***
.LIB "tsmc_025um_model.mod" CMOS_MODELS
.end

**Code to plot VTC (tt):**

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

**Output:** 
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-20-23" src="https://github.com/user-attachments/assets/99f17088-72f2-49e1-a6a8-c0cffc3eac80" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-22-11" src="https://github.com/user-attachments/assets/94c09826-2f90-448d-a048-dc432cc618d4" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-22-18" src="https://github.com/user-attachments/assets/f7b5d788-3aea-4d88-80dc-c3164f77e70f" />

To find the switching threshold (Vm): 
- Zoom into the VTC curve where **Vout ≈ Vin**.
- Refine by zooming 2–3 times until the crossover point is clear.
- Click on the approximate point → values (x0, y0) appear in the terminal.
- At threshold, **x0 ≈ y0 = Vm**.

**Code for Transient Analysis:**

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

**Output:** 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-28-06" src="https://github.com/user-attachments/assets/86a78c01-0cc9-4d20-bdbe-4e5681afc733" />

**To Calculate rising edge delay:** 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-27-58" src="https://github.com/user-attachments/assets/c52d0c2a-b71f-4a0d-bdf0-6c9d51d63cc4" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-28-17" src="https://github.com/user-attachments/assets/41969100-ca17-4036-b313-54e1c6c0a296" /> 

- Zoom around input fall & output rise near **Vdd/2**.
- Click output rising edge → get (x0, y0).
- Click input falling edge at same level.
- Delay = Δx (output – input).

**To Calculate falling edge delay:**

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-32-03" src="https://github.com/user-attachments/assets/08d81608-64bb-483b-b5a9-0bf0c1326660" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-32-14" src="https://github.com/user-attachments/assets/ae16d8fa-b451-4849-9b24-8a9fdd22e8a4" /> 

- Zoom around input rise & output fall near **Vdd/2**.
- Click output falling edge → get (x0, y0).
- Click input rising edge at same level.
- Delay = Δx (output – input).

**Static Behavior Evaluation - CMOS Inverter Robustness: Switching threshold** 
- **Robustness:** Input–output curve shape stays the same for all W/L ratios.
- **Key parameters:** Switching threshold (Vm), noise margin, supply & device variation.

**Switching Threshold (Vm):** 
- Point where Vin = Vout.
- Found graphically using 45° line on VTC plot.
- Vm ≈ 0.98V for Wp/Lp = 1.5, Vm ≈ 1.2V for Wp/Lp = 3.75.
- At Vm, both NMOS & PMOS are ON.
- Condition: Vgs ≈ Vds, IdsP + IdsN = 0.
  
**Observation:** 
- Increasing Wp/Lp → rise delay decreases (faster capacitor charging due to larger PMOS).

# Day 4 – Noise Margins 

--- 

- **Vil (Input Low Voltage):** Any input between 0 and Vil (≈ Vdd/4) is logic ‘0’.
- **Vih (Input High Voltage):** Any input between Vih (≈ 3Vdd/4) and Vdd is logic ‘1’.
- **Vol (Output Low Voltage):** Any output between 0 and Vol (< Vil) is logic ‘0’.
- **Voh (Output High Voltage):** Any output between Voh (> Vih) and Vdd is logic ‘1’.

**Lab Code for Noise Margin:**

*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
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

**Output:** 
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-38-21" src="https://github.com/user-attachments/assets/c6b16568-9041-43f8-94e2-7fd094690e1b" /> 
<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-39-36" src="https://github.com/user-attachments/assets/74cb2528-432e-4455-9378-3cb4abb0ea4d" />

# Noise Margin Calculation 
1. Open NGSpice plot.
2. Select top point where slope ≈ -1 → x0 = 0.766667, y0 = 1.71351
3. Select bottom point where slope ≈ -1 → x1 = 0.977333, y1 = 0.110811
4. Calculate: - **NMh = Voh - Vih = y0 - x1 = 1.73571 - 0.971111 = 0.764599** - **NMl = Vil - Vol = x0 - y1 = 0.751111 - 0.12619 = 0.624921**

--- 

# Day 5 – CMOS Power supply and device variation robustness 

--- 
# CMOS Supply Voltage Scaling 
As technology scales from 250nm to ~20nm, supply voltage is reduced (e.g., from 1V → 0.7V). CMOS inverters can operate at 0.5V with the following trade-offs: 
**Advantages (0.5V supply):** 
- Gain increases by ~50%
- Energy consumption reduces by ~90%
**Disadvantages (0.5V supply):**
  - Performance suffers: rising/falling edges may not fully charge/discharge the load capacitance

**Lab Code to calculate supply variation:**

*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

.control

let powersupply = 1.8
alter Vdd = powersupply
        let voltagesupplyvariation = 0
        dowhile voltagesupplyvariation < 6
        dc Vin 0 1.8 0.01
        let powersupply = powersupply - 0.2
        alter Vdd = powersupply
        let voltagesupplyvariation = voltagesupplyvariation + 1
      end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc

.end

**Output:** 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-44-14" src="https://github.com/user-attachments/assets/f5a0d5a4-a29f-44cb-8e01-5d9057422c9a" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-45-31" src="https://github.com/user-attachments/assets/ddb1c70b-7f19-4840-aed2-898ff090db8a" />

# CMOS Inverter Gain Calculation 
1. Select the curve for which gain is to be calculated (e.g., Vdd = 1.8V).
2. Click near the top of the curve where slope changes → x0 = 0.78764, y0 = 1.68333
3. Click near the bottom of the curve where slope changes → x1 = 0.994382, y1 = 0.833333
4. Calculate differences: - Δy = y0 - y1 = 0.849997 - Δx = x0 - x1 = -0.206742
5. Gain: g = |Δy / Δx| = |0.849997 / -0.206742| = 0.643255

**Lab Code for device variations:**

*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description

XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15

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

**Output:** 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-49-28" src="https://github.com/user-attachments/assets/9e022d8c-ec4a-46f6-bacc-4e4af95e9d00" /> 

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-50-52" src="https://github.com/user-attachments/assets/3871b4b2-3d32-47fa-9b30-b5c1c69b5621" />

<img width="1847" height="934" alt="Screenshot from 2025-08-19 22-51-02" src="https://github.com/user-attachments/assets/ae11210f-5b7f-42a4-8e74-5132a5ae2459" />

# CMOS Inverter Switching Threshold 
- Due to a much larger PFET width compared to NFET, the VTC plot shifts right.
- To find the switching threshold (Vm):
1. Zoom in where Vin ≈ Vout.
2. Left-click the point where Vin ≈ Vout.
3. Example point: x0 = 0.990769, y0 = 0.966418 - Since x0 ≈ y0, the **Switching Threshold Voltage** is: Vm = 0.9 V

# Device Variation in CMOS Inverters Device variations arise from: 
1. **Etching Process Variation:**
- Defines structures in the CMOS layout (width, height).
- Directly impacts delay.
- Layout elements:
- -**P-diffusion:** green
- -**N-diffusion:** yellow
- - **Poly-silicon (gate):** red, defines gate length (node: 20nm, 30nm, etc.)
- - **Metal layer:** blue
- - **Contacts:** black crosses
- Fabrication involves chemicals, gases, water, etc., which can distort the ideal structure

2. **Oxide Thickness Variation:**
- Variations in gate oxide thickness affect transistor characteristics.
- Impacts threshold voltage, leakage current, and overall inverter performance.
- Critical in scaled nodes where small thickness changes significantly affect device behavior.

# Conclusion

The workshop provided hands-on experience with CMOS circuit design and SPICE simulation using SKY130 technology. Key takeaways include:

- Understanding **MOSFET operation** in both long-channel and short-channel devices, including **velocity saturation** effects.  
- Learning to extract **threshold voltage (Vt)** from Id–Vgs characteristics.  
- Analyzing **CMOS inverter behavior**, including **Voltage Transfer Characteristics (VTC)**, switching thresholds (Vm), and noise margins.  
- Observing the impact of **supply voltage scaling, and device variations** on inverter performance.  
- Performing **transient analysis** to calculate **rising/falling edge delays** and inverter gain.  

Overall, the workshop strengthened practical skills in **SPICE simulations, inverter characterization, and CMOS robustness analysis**, providing a strong foundation for VLSI circuit design and optimization in advanced technology nodes.


## 📌 References 
- Workshop material: https://www.vlsisystemdesign.com/cmos-circuit-design-spice-simulation-using-sky130-technology/
- https://github.com/kunalg123/sky130CircuitDesignWorkshop

THANK YOU
