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

## 📂 Repository Structure
- `Day1/` → MOSFET characteristics SPICE files.  
- `Day2/` → CMOS inverter netlists + VTC results.  
- `Day3/` → Switching threshold simulations.  
- `Day4/` → Noise margin analysis.  
- `Day5/` → Robustness analysis.  
- `images/` → Plots and screenshots.

---

## 🛠️ Tools Used
- **SKY130 PDK**
- **ngspice**
- Linux environment (for running simulations)

---

## 📌 References
- Workshop material [https://www.vlsisystemdesign.com/cmos-circuit-design-spice-simulation-using-sky130-technology/]
