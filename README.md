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


# Circuit Requirements for LM3478 ±17 V, 4 A Boost Converter

The LM3478 is used to generate ±17 V rails at up to 4 A from an input voltage of **9–12 V**. LDO regulators will post-regulate the outputs to ±15 V. Components must be selected for reliability, current handling, and thermal efficiency.

---

### General Design Targets

- **Input Voltage:** 9–12 V
- **Output Voltage:** ±17 V
- **Output Current:** 4 A
- **Post-regulation:** ±15 V via LDOs
- **Switching Frequency:** 500 kHz (set using resistor on FA/SD pin)

---

### Power Stage Component Requirements

| Component                  | Parameter               | Requirement                                                                  |
| -------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| **MOSFET**                 | Drain current           | $\geq 8$ A (includes margin for ripple and losses)                           |
|                            | Drain-source voltage    | $\geq 35$ V (≥ 2× $V_{out}$ for transient headroom)                          |
|                            | $R_{DS(on)}$            | $\leq 30$ mΩ for low conduction losses                                       |
|                            | Package                 | DPAK, TO-252, or similar for thermal performance                             |
| **Inductor**               | Saturation current      | $\geq 6$–8 A                                                                 |
|                            | Inductance              | Estimate using:  $L = \frac{V_{in} \cdot D}{f \cdot \Delta I_L}$             |
|                            |                         | where $\Delta I_L \approx 0.3 \cdot I_{out}$                                 |
| **Diode**                  | Average forward current | $\geq 5$ A                                                                   |
|                            | Reverse voltage         | $\geq 30$ V                                                                  |
|                            | Type                    | Schottky (fast recovery, low $V_F$)                                          |
| **Output Capacitor**       | Ripple current rating   | $\geq 3$ A RMS                                                               |
|                            | ESR                     | Low ESR ceramic or polymer/tantalum                                          |
|                            | Capacitance             | Use:  $\Delta V_{out} = \frac{\Delta I_L}{8 \cdot f \cdot C_{out}}$          |
| **Current Sense Resistor** | Peak current setpoint   | $$R_{sense} = \frac{V_{CS}}{I_{peak}}, \quad V_{CS} \approx 100\,\text{mV}$$ |
| **LDOs (±15 V)**           | Input voltage           | Must accept 17 V input                                                       |
|                            | Dropout voltage         | ≤ 2 V                                                                        |
|                            | Output current          | 4 A per rail                                                                 |
|                            | Thermal dissipation     | Ensure heat sinking or thermal pad if needed                                 |

---

### Design Notes

- At minimum input voltage (9 V), the duty cycle is approximately:

  $$
  D = 1 - \frac{V_{in} \cdot \eta}{V_{out}} \approx 1 - \frac{9 \cdot 0.9}{17} \approx 0.52
  $$

- Use **MOSFET, inductor, and diode** that can handle peak currents around:

  $$
  I_{peak} \approx I_{out} + \frac{\Delta I_L}{2}
  $$

- Ensure **input capacitors** rated for ripple currents ≥ 2 A.
- Use proper **thermal layout** with wide traces and solid ground plane.
- For switching frequency of 500 kHz, set:

  $$
  R_{FA} = 4.503 \times 10^{11} \cdot f^{-1.26}
  $$

  For $f = 500\,\text{kHz}$, this yields:

  $$
  R_{FA} \approx 13.1\,\text{k}\Omega
  $$
