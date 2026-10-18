# Circuit Logic, Truth Tables & Boolean Derivations

This document explains the mathematical theory, state tables, and gate-level Boolean derivations behind the 2-bit D Flip-Flop counter and its 7-segment display decoder.

---

## 1. The 2-Bit Binary Counter State Table

The counter cycles cyclically through 4 distinct decimal states: **0, 1, 2, 3**, and resets to **0**.

Let:
- $A$ = Most Significant Bit (MSB, Flip-Flop A output $Q_A$)
- $B$ = Least Significant Bit (LSB, Flip-Flop B output $Q_B$)

### State Transition & Excitation Table (D Flip-Flops)

For a D Flip-Flop, the next state $Q_{next}$ equals the data input $D$ ($Q_{next} = D$).

| Present State $A$ | Present State $B$ | Next State $A_{next}$ | Next State $B_{next}$ | Required Input $D_A$ | Required Input $D_B$ | Display Digit |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 0 | 0 | 0 | 1 | 0 | 1 | **0** |
| 0 | 1 | 1 | 0 | 1 | 0 | **1** |
| 1 | 0 | 1 | 1 | 1 | 1 | **2** |
| 1 | 1 | 0 | 0 | 0 | 0 | **3** |

### Next-State Equations:
- **$D_B = \overline{B}$**: The LSB toggles on every single clock pulse. This is achieved by feeding the inverted output $\overline{Q_B}$ back to $D_B$.
- **$D_A = A \oplus B = A\overline{B} + \overline{A}B$**: The MSB toggles only when $B = 1$. Implemented with logic gates (NAND / AND-OR) or by cascading flip-flops.

---

## 2. 7-Segment Display Combinational Decoder

In standard lab setups, a dedicated decoder IC (such as 7447 or 4511) is used. In this project, to genuinely understand combinational logic design, **we built the decoder from fundamental discrete logic gates (AND, OR, NOT)**.

### 7-Segment Truth Table for Digits 0, 1, 2, 3

A segment is active (**1**) when illuminated, and inactive (**0**) when unlit on a Common Cathode display.

| Digit | $A$ (MSB) | $B$ (LSB) | $a$ | $b$ | $c$ | $d$ | $e$ | $f$ | $g$ |
|:-----:|:---------:|:---------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **0** | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 1 | 0 |
| **1** | 0 | 1 | 0 | 1 | 1 | 0 | 0 | 0 | 0 |
| **2** | 1 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | 1 |
| **3** | 1 | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 1 |

---

## 3. Karnaugh Map (K-Map) & Boolean Simplification

Using the truth table values, each segment output is simplified:

### Segment $a$
- Minterms: $m(0, 2, 3)$
- Sum of Products: $a = \overline{A}\,\overline{B} + A\overline{B} + AB$
- Factoring: $\overline{B}(\overline{A} + A) + AB = \overline{B} + AB = A + \overline{B}$
- **Hardware Realization**: 1 OR gate with inputs $A$ and $\overline{B}$.

### Segment $b$
- Minterms: $m(0, 1, 2, 3)$
- All states require segment $b$ to be ON.
- **Hardware Realization**: Direct connection to $V_{CC}$ (+5V).

### Segment $c$
- Minterms: $m(0, 1, 3)$
- Sum of Products: $c = \overline{A}\,\overline{B} + \overline{A}B + AB$
- Factoring: $\overline{A}(\overline{B} + B) + AB = \overline{A} + AB = \overline{A} + B$
- **Hardware Realization**: 1 OR gate with inputs $\overline{A}$ and $B$.

### Segment $d$
- Minterms: $m(0, 2, 3)$
- Identical logic to Segment $a$:
- **Hardware Realization**: $d = A + \overline{B}$ (can share output of segment $a$'s gate or use another OR gate).

### Segment $e$
- Minterms: $m(0, 2)$
- Sum of Products: $e = \overline{A}\,\overline{B} + A\overline{B} = (\overline{A} + A)\overline{B} = \overline{B}$
- **Hardware Realization**: Direct connection from inverted LSB $\overline{B}$ (output of NOT gate / $\overline{Q_B}$).

### Segment $f$
- Minterms: $m(0)$
- Sum of Products: $f = \overline{A} \cdot \overline{B}$
- **Hardware Realization**: 1 AND gate with inputs $\overline{A}$ and $\overline{B}$ (or NOR gate $\overline{A + B}$).

### Segment $g$
- Minterms: $m(2, 3)$
- Sum of Products: $g = A\overline{B} + AB = A(\overline{B} + B) = A$
- **Hardware Realization**: Direct connection from flip-flop MSB output $A$ ($Q_A$).

---

## 4. Clock Generation via NE555 Astable Multivibrator

The clock pulse drives the clock inputs of the flip-flops.

$$f = \frac{1.44}{(R_1 + 2R_2) \cdot C_1}$$

- For a visual ~1 Hz clock rate (1 second per count):
  - $C_1 = 10\,\mu\text{F}$
  - Resistors $R_1 \approx 10\,\text{k}\Omega$, $R_2 \approx 68\,\text{k}\Omega$
  - Output pin 3 provides a clean square wave that toggles a blue LED and triggers the flip-flop clock pins.
