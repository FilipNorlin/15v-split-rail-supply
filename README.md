[[Boost Converter|Useful Notes]]

# LM3478 Useful Design Formulas

---

## 1. Output Voltage (Boost Converter)

$$
V_{out} = V_{in} \times \frac{1}{1 - D}
$$

- $D$ = duty cycle (0 to 1)  
- $V_{in}$ = input voltage

---

## 2. Output Voltage (Inverting Buck-Boost)

$$
V_{out} = - V_{in} \times \frac{D}{1 - D}
$$

- $D$ = duty cycle  
- $V_{in}$ = input voltage

---

## 3. Inductor Selection

The inductance $L$ based on desired ripple current $\Delta I_L$:

$$
L = \frac{V_{in} \times D}{f \times \Delta I_L}
$$

- $f$ = switching frequency (Hz)  
- $\Delta I_L$ = peak-to-peak inductor ripple current (A), typically 20–40% of max load current  
- $D$ = duty cycle

---

## 4. Inductor Ripple Current

$$
\Delta I_L = \frac{V_{in} \times D}{L \times f}
$$

---

## 5. Output Capacitor Ripple Voltage

For a boost converter, output voltage ripple $\Delta V_{out}$ approximated by:

$$
\Delta V_{out} = \frac{\Delta I_L}{8 \times f \times C_{out}}
$$

- $C_{out}$ = output capacitance (F)

---

## 6. Peak Inductor Current

At max load:

$$
I_{L_{peak}} = I_{out} \times \frac{1}{1-D} + \frac{\Delta I_L}{2}
$$

---

## 7. Switch Current Limit

Current sense resistor $R_{sense}$ is:

$$
R_{sense} = \frac{V_{CS}}{I_{peak}}
$$

- $V_{CS}$ = current sense threshold voltage (typ. 100 mV)  
- $I_{peak}$ = desired max MOSFET peak current

---

## 8. Frequency Adjust Resistor Formula

To set the switching frequency $f_S$ (in Hz), use:

$$
R_{FA} = 4.503 \times 10^{11} \cdot f_S^{-1.26}
$$

Where:

- $R_{FA}$ is the resistor connected from FA/SD to ground (in ohms)
- $f_S$ is the desired switching frequency in Hz


---

## 9. Duty Cycle Limit

Maximum duty cycle:

$$
D_{max} \approx 0.85
$$

---

## 10. Feedback Voltage Divider Formula

The output voltage is set by the resistor divider at the FB pin:

$$
V_{out} = V_{ref} \times \left(1 + \frac{R_1}{R_2}\right) = 1.26\,V \times \left(1 + \frac{R_1}{R_2}\right)
$$

- $R_1$ connected from $V_{out}$ to FB  
- $R_2$ connected from FB to ground  
- $V_{ref} = 1.26\,V$

---

# Summary Table

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


# Circuit Requirements for LM3478 ±17 V, 4 A Boost Converter And Components Scaling

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

---

# ✅ Component Selection Criteria

### 🔋 MOSFET

| Parameter                                | Specification                                             |
| ---------------------------------------- | --------------------------------------------------------- |
| Drain-Source Voltage (V<sub>DS</sub>)    | ≥ 30 V (1.5 × V<sub>out</sub>)                            |
| Continuous Drain Current (I<sub>D</sub>) | ≥ 10 A                                                    |
| On-Resistance (R<sub>DS(on)</sub>)       | ≤ 20 mΩ for low conduction loss                           |
| Gate Charge (Q<sub>g</sub>)              | Low, for faster switching and less heat                   |
| Package                                  | DPAK, SO-8, PowerPAK — good for hand soldering & thermals |

**Example parts:**
- IRFR3708 (30 V, 62 A, 11 mΩ, D2PAK)
- BSC026N08NS5 (80 V, 60 A, 2.6 mΩ, PowerPAK)

---

### 🌀 Inductor

| Parameter                            | Specification                          |
| ------------------------------------ | -------------------------------------- |
| Inductance                           | 5.6 μH (±20%)                          |
| Saturation Current (I<sub>sat</sub>) | ≥ 10–12 A                              |
| DCR (DC resistance)                  | Low (≤ 10–20 mΩ) for higher efficiency |
| Core Type                            | Shielded preferred (for lower EMI)     |

**Example parts:**
- Coilcraft XAL7030-562 (5.6 μH, 15.8 A I<sub>sat</sub>, 9 mΩ)
- Wurth 74437346056 (5.6 μH, 11 A, 12.5 mΩ)

---

### ⚡ Diode

| Parameter                               | Specification                                      |
| --------------------------------------- | -------------------------------------------------- |
| Reverse Voltage (V<sub>R</sub>)         | ≥ 30 V                                             |
| Average Forward Current (I<sub>F</sub>) | ≥ 10 A avg, 20–30 A peak                           |
| Type                                    | Schottky (for fast switching and low voltage drop) |
| Package                                 | TO-220, D2PAK, PowerDI — good thermal performance  |

**Example parts:**
- STPS20L30D (30 V, 20 A, TO-220)
- SBR10U30P5 (30 V, 10 A, PowerDI 5)

---



