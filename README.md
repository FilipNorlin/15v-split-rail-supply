![[Main diagram.png| 650]]

---

![[lm347_pinout.png]]

# Design and Scaling of a ±17 V 4 A DC-DC Converter with LM3478

## LM3478 Useful Design Formulas

---

### 1. Output Voltage (Boost Converter)

$$
V_{out} = V_{in} \times \frac{1}{1 - D}
$$

- $D$ = duty cycle (0 to 1)  
- $V_{in}$ = input voltage

---

### 2. Output Voltage (Inverting Buck-Boost)

$$
V_{out} = - V_{in} \times \frac{D}{1 - D}
$$

- $D$ = duty cycle  
- $V_{in}$ = input voltage

---

### 3. Inductor Selection

The inductance $L$ based on desired ripple current $\Delta I_L$:

$$
L = \frac{V_{in} \times D}{f \times \Delta I_L}
$$

- $f$ = switching frequency (Hz)  
- $\Delta I_L$ = peak-to-peak inductor ripple current (A), typically 20–40% of max load current  
- $D$ = duty cycle

---

### 4. Inductor Ripple Current

$$
\Delta I_L = \frac{V_{in} \times D}{L \times f}
$$

---

### 5. Output Capacitor Ripple Voltage

For a boost converter, output voltage ripple $\Delta V_{out}$ approximated by:

$$
\Delta V_{out} = \frac{\Delta I_L}{8 \times f \times C_{out}}
$$

- $C_{out}$ = output capacitance (F)

---

### 6. Peak Inductor Current

At max load:

$$
I_{L_{peak}} = I_{out} \times \frac{1}{1-D} + \frac{\Delta I_L}{2}
$$

---

### 7. Switch Current Limit

Current sense resistor $R_{sense}$ is:

$$
R_{sense} = \frac{V_{CS}}{I_{peak}}
$$

- $V_{CS}$ = current sense threshold voltage (typ. 156 mV)  
- $I_{peak}$ = desired max MOSFET peak current

---

### 8. Frequency Adjust Resistor Formula

To set the switching frequency $f_S$ (in Hz), use:

$$
R_{FA} = 4.503 \times 10^{11} \cdot f_S^{-1.26}
$$

Where:

- $R_{FA}$ is the resistor connected from FA/SD to ground (in ohms)
- $f_S$ is the desired switching frequency in Hz

---

### 9. Feedback Voltage Divider Formula

The output voltage is set by the resistor divider at the FB pin:

$$
V_{out} = V_{ref} \times \left(1 + \frac{R_1}{R_2}\right) = 1.26\,V \times \left(1 + \frac{R_1}{R_2}\right)
$$

- $R_1$ connected from $V_{out}$ to FB  
- $R_2$ connected from FB to ground  
- $V_{ref} = 1.26\,V$

---

## Summary Table

| Parameter                    | Formula / Value                                                      |
| ---------------------------- | -------------------------------------------------------------------- |
| Internal reference $V_{ref}$ | 1.26 V                                                               |
| Output voltage               | $V_{out} = 1.26 \times \left(1 + \frac{R_1}{R_2}\right)$             |
| Inductor value               | $L = \frac{V_{in} \times D}{f \times \Delta I_L}$                    |
| Inductor ripple current      | $\Delta I_L = \frac{V_{in} \times D}{L \times f}$                    |
| Output voltage ripple        | $\Delta V_{out} = \frac{\Delta I_L}{8 \times f \times C_{out}}$      |
| Peak inductor current        | $I_{L_{peak}} = I_{out} \times \frac{1}{1-D} + \frac{\Delta I_L}{2}$ |
| Current sense resistor       | $R_{sense} = \frac{V_{CS}}{I_{peak}}$                                |
| Switching frequency          |                                                                      |
| Max duty cycle               | $D_{max} \approx 0.85$                                               |

---


## Circuit Requirements for LM3478 ±17 V, 4 A Boost Converter And Components Scaling

The LM3478 is used to generate ±17 V rails at up to 4 A from an input voltage of **9–12 V**. LDO regulators will post-regulate the outputs to ±15 V. Components must be selected for reliability, current handling, and thermal efficiency.

---

### General Design Targets

- **Input Voltage:** 9–12 V
- **Output Voltage:** ±17 V
- **Output Current:** 4 A
- **Post-regulation:** ±15 V via LDOs
- **Switching Frequency:** 500 kHz (set using resistor on FA/SD pin)
- **Efficiency** $\approx$ $85\%$

### 1. Duty cycle

$$
D = 1 - \frac{V_{in} \cdot \eta}{V_{out}} \approx 0.52
$$
### 2. Estimate ripple current

This is a chicken and the egg problem since you need to know the ripple to get the inductor value and to need to know the inductance for the ripple. Therefore we estimate it to some percentage of the maximum output current. As mentioned before it somewhere around 20-40%.

$$
\Delta I_{L} = 0.3 \cdot I_{out(max)} \cdot \frac{V_{out}}{V_{in}}=2.27 A
$$
---

### 3. Inductance

$$
L = \frac{V_{in} \times D}{f \times \Delta I_L} = 4.4 \mu H
$$
Choose nearest standard value (e.g $4.7 \mu H$ - keep in mind typical $\pm20\%$ tolerance). A bigger Inductance will also reduce ripple. To compensate for the tolerance Im going to choose an even higher value inductor. $L=5.6 \mu H$ 

### 4. (Actual) inductor ripple current

$$
\Delta I_L = \frac{V_{in} \times D}{L \times f} = 1.77A
$$

### 5. Maximum sw./ind./diode current

$$
I_{L_{peak}} = I_{out} \times \frac{1}{1-D} + \frac{\Delta I_L}{2} = 9.77 A
$$

### 6. Estimate $C_{in}$

$$
C_{in} = \frac{I_{in} \cdot D}{f \cdot C_{in}} \approx 91 \mu F
$$

- $I_{in} \approx \frac{V_{out} \cdot I_{out}}{V_{in} \cdot \eta} = 8.9 A$

Use multiple capacitors in parallell: 
- **Fast reacting with low ESR:**
	- 2x 47 $\mu F$ (Ceramic (X7R))
- **Bulk capacitor for energy storing:**
	- 220 $\mu F$ (electrolytic/polymer)

**Input Capacitor Ripple Current:**

$$
I_{C_{in, RMS}} \approx I_{in}\cdot \sqrt{ D \cdot (1 - D) } \approx 4.42 A
$$

Input capacitors must collectively handle **~4.5 A RMS** ripple current

---

### 7. Estimate $C_{out}$

$$
C_{out} = \frac{\Delta I_{L}}{8 \cdot f \cdot \Delta V_{out}} \approx 88 \mu F
$$

To ensure **<50 mV ripple**, use at least **100 μF**.

Use multiple capacitors in parallell: 
- **Fast reacting with low ESR:**
	- 2x 47 $\mu F$ (Ceramic (X7R))
- **Bulk capacitor for energy storing:**
	- 220 $\mu F$ (electrolytic/polymer)
- ESR should be **low** (e.g., <30 mΩ)

**Output Capacitor Ripple Current:**

$$
I_{C_{out, RMS}} \approx \frac{\Delta I_{L}}{\sqrt{ 12 }} \approx 0.51 A
$$

output capacitors must collectively handle **~0.5 A RMS** ripple current


---

## Scaling and considerations

### $I_{SEN}$

The $I_{SEN}$ pin monitors the voltage across an external current-sense resistors ($R_{SEN}$). When the voltage across the resistor reaches a certain threshold the switch is turned off. This can be used to set a current limit.

$$
R_{sense} = \frac{V_{CS}}{I_{peak}}
$$
- $V_{CS} \approx 156$ mV

Lets set the maximum allowed current to 10A.

$$
R_{sense} = \frac{0.156}{10} = 15.6 \text{m} \Omega
$$
A $15$ - $20m\Omega$ can be used depending on availability and margin.

A low-inductance, high power shunt resistor (e.g 1206) is preferable. Also short clean routing is important since the ISEN pin is sensitive.

### COMP

- It is the **output of the error amplifier** inside the LM3478.
- You connect a **compensation network** (typically an **RC or RCC** network) between the **COMP pin and ground**.
- This network **sets the frequency response** of the voltage control loop.

Assuming:
- Output capacitance: $C_{out} = 100,\mu F$
- Output load: $R_{load} = \frac{V_{out}}{I_{out}} = \frac{17}{4} = 4.25,\Omega$
- Crossover frequency: $f_c \approx \frac{f_{sw}}{10} = 50,\text{kHz}$

You might choose:
- $R_c = 10,\text{k}\Omega$
- $C_c = 2.2,\text{nF}$
- (Optional) $C_f = 100,\text{pF}$

This is just a **starting guess**. Final values should be tuned via:
- **Simulation (e.g., with SPICE)**
- **Empirical testing with a Bode plot analyzer**

#### Tips
- If the output oscillates or reacts slowly, **adjust $R_c$ and $C_c$**.
- **Too much gain** → instability. **Too little gain** → sluggish response.
- Always use **short, clean traces** for the COMP network — it's a high-impedance node.

---
### FB

$$
V_{out} = V_{REF} \left(  1+ \frac{R_{1}}{R_{2}} \right)
$$
- $V_{REF} = 1.26V$

$$
\frac{R_{1}}{R_{2}} = \frac{V_{out}}{V_{REF}} - 1 \approx 12.5
$$
- $R_{2}=10k\Omega$

$$
R_{1} = 12.5 \cdot R_{2} = 125k\Omega
$$
- Close E12 value resistor is $127k\Omega$

$$
V_{out} = V_{REF} \left(  1 + \frac{R_{1}}{R_{2}} \right) = 17.26V
$$

- Use resistors ≥10 kΩ to avoid loading the output too much.
- Higher resistor values → lower quiescent power draw.
- Lower resistor values → better noise immunity.
- Avoid too high values (e.g. >500 kΩ) due to FB input bias current.

### FA/SD

$$
R_{FA} = 4.503 \cdot 10^{11} \cdot f^{-1.26} = 13.1k\Omega
$$

$R_{FA}=13k\Omega$

- Choose frequency based on trade-off:
    - **Higher f** → smaller parts, more switching loss
    - **Lower f** → bigger parts, better efficiency
- Use a **low-noise layout** for this pin — it sets timing.

---

## ✅ Component Selection Criteria

---

### 🔋 MOSFET

| Parameter                      | Specification                                             |
| ------------------------------ | --------------------------------------------------------- |
| Drain-Source Voltage $V_{DS}$  | $\geq 30\,\text{V}$ (1.5 × $V_{out}$)                     |
| Continuous Drain Current $I_D$ | $\geq 10\,\text{A}$                                       |
| On-Resistance $R_{DS(on)}$     | $\leq 20\,\text{m}\Omega$ for low conduction loss         |
| Gate Charge $Q_g$              | Low, for faster switching and less heat                   |
| Package                        | DPAK, SO-8, PowerPAK — good for hand soldering & thermals |

**Example parts:**
- IRFR3708 (30 V, 62 A, 11 mΩ, D2PAK)
- BSC026N08NS5 (80 V, 60 A, 2.6 mΩ, PowerPAK)

---

### 🌀 Inductor

| Parameter                                | Specification                          |
|------------------------------------------|----------------------------------------|
| Inductance $L$                           | $5.6\,\mu\text{H}$ (±20%)              |
| Saturation Current $I_{\text{sat}}$      | $\geq 10\text{–}12\,\text{A}$          |
| DCR (DC resistance)                      | $\leq 10\text{–}20\,\text{m}\Omega$    |
| Core Type                                | Shielded preferred (for lower EMI)     |

**Example parts:**
- Coilcraft XAL7030-562 ($5.6\,\mu\text{H}$, $15.8\,\text{A}$ $I_{\text{sat}}$, $9\,\text{m}\Omega$)
- Wurth 74437346056 ($5.6\,\mu\text{H}$, $11\,\text{A}$, $12.5\,\text{m}\Omega$)

---

### ⚡ Diode

| Parameter                                | Specification                                        |
|------------------------------------------|------------------------------------------------------|
| Reverse Voltage $V_R$                    | $\geq 30\,\text{V}$                                  |
| Average Forward Current $I_F$            | $\geq 10\,\text{A}$ avg, 20–30 A peak                |
| Type                                     | Schottky (for fast switching and low voltage drop)   |
| Package                                  | TO-220, D2PAK, PowerDI — good thermal performance    |

**Example parts:**
- STPS20L30D (30 V, 20 A, TO-220)
- SBR10U30P5 (30 V, 10 A, PowerDI 5)

---

### 🔌 Input Capacitors $C_{in}$

| Parameter                                 | Specification                                            |
|-------------------------------------------|----------------------------------------------------------|
| Capacitance $C_{in}$                      | $\geq 91\,\mu\text{F}$ total                             |
| Ripple Current Rating $I_{C_{in, RMS}}$   | $\geq 4.5\,\text{A RMS}$                                 |
| Type                                      | Mix of ceramic (low ESR) and polymer/electrolytic (bulk)|
| Configuration                             | $2 \times 47\,\mu\text{F}$ ceramic + $220\,\mu\text{F}$ bulk |

---

### 🔋 Output Capacitors $C_{out}$

| Parameter                                | Specification                                                |
| ---------------------------------------- | ------------------------------------------------------------ |
| Capacitance $C_{out}$                    | $\geq 100\,\mu\text{F}$ total                                |
| Ripple Voltage $\Delta V_{out}$          | $\leq 50\,\text{mV}$                                         |
| Ripple Current Rating $I_{C_{out, RMS}}$ | $\geq 0.5\,\text{A RMS}$                                     |
| ESR                                      | Low ESR ($\leq 30\,\text{m}\Omega$)                          |
| Type                                     | Mix of ceramic (fast) + bulk (stabilization)                 |
| Configuration                            | $2 \times 47\,\mu\text{F}$ ceramic + $220\,\mu\text{F}$ bulk |

---

## Final Design Summary

| Parameter                        | Value / Formula                                       |
|----------------------------------|-------------------------------------------------------|
| Input Voltage Range              | 9–12 V                                                |
| Output Voltage                   | ±17 V (±15 V post-regulated)                          |
| Output Current                   | 4 A                                                   |
| Efficiency (est.)                | 85%                                                   |
| Switching Frequency              | 500 kHz                                               |
| Duty Cycle (at 9 V)              | $D = 1 - \frac{V_{in} \cdot \eta}{V_{out}} \approx 0.52$ |
| Ripple Current Estimate          | $\Delta I_L = 2.27\,\text{A}$ (initial estimate)      |
| Chosen Inductor Value            | $L = 5.6\,\mu\text{H}$                                 |
| Actual Ripple Current            | $\Delta I_L = 1.77\,\text{A}$                         |
| Peak Inductor Current            | $I_{L_{peak}} = 9.77\,\text{A}$                       |
| Input Current                    | $I_{in} \approx 8.9\,\text{A}$                        |
| Input Capacitance                | $C_{in} \geq 91\,\mu\text{F}$                         |
| Input Capacitor Ripple Current   | $I_{C_{in, RMS}} \approx 4.42\,\text{A}$              |
| Output Capacitance               | $C_{out} \geq 100\,\mu\text{F}$                       |
| Output Ripple Voltage Target     | $\leq 50\,\text{mV}$                                  |
| Output Capacitor Ripple Current | $I_{C_{out, RMS}} \approx 0.51\,\text{A}$             |
| Current Sense Resistor Value     | $R_{sense} = \frac{0.156}{10} = 15.6\,\text{m}\Omega$ |
| Feedback Resistors               | $R_1 = 127\,\text{k}\Omega$, $R_2 = 10\,\text{k}\Omega$ |
| COMP Network                     | $R_c = 10\,\text{k}\Omega$, $C_c = 2.2\,\text{nF}$ (start values) |
| FA/SD Resistor                   | $R_{FA} = 13\,\text{k}\Omega$                         |

---
