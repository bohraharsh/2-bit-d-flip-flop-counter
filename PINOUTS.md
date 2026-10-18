# IC Pinout Reference & Wiring Guide

A quick, beginner-friendly reference for all Integrated Circuits (ICs) used in the 2-bit counter and 7-segment decoder circuit.

---

## 1. NE555 Precision Timer (8-Pin DIP)

Generates the periodic clock pulse (~1 Hz) for the flip-flops.

```
       +---v---+
 GND  -| 1   8 |- VCC (+5V)
TRIG  -| 2   7 |- DISCH (Discharge)
 OUT  -| 3   6 |- THRES (Threshold)
RESET -| 4   5 |- CTRL (Control Voltage)
       +-------+
```

| Pin No. | Name | Function / Hackathon Connection |
|:-------:|:-----|:--------------------------------|
| **1** | GND | Connected to system Ground rail (0V) |
| **2** | TRIG | Connected together with Pin 6 to the timing capacitor node |
| **3** | OUT | Clock pulse output; feeds clock to D Flip-Flops and blue indicator LED |
| **4** | RESET | Tied directly to VCC (+5V) to prevent accidental reset |
| **5** | CTRL | Connected to Ground via a $0.01\,\mu\text{F}$ capacitor to bypass noise |
| **6** | THRES | Connected to Pin 2; detects 2/3 VCC threshold |
| **7** | DISCH | Connected between timing resistor $R_1$ and $R_2$ |
| **8** | VCC | Connected to +5V power rail |

---

## 2. 7474 Dual D-Type Positive-Edge-Triggered Flip-Flop (14-Pin DIP)

Acts as the 2-bit state register memory (Flip-Flop A = MSB, Flip-Flop B = LSB).

```
          +---v---+
   1CLR  -| 1  14 |- VCC (+5V)
     1D  -| 2  13 |- 2CLR
   1CLK  -| 3  12 |- 2D
   1PRE  -| 4  11 |- 2CLK
     1Q  -| 5  10 |- 2PRE
   1Q_b  -| 6   9 |- 2Q
    GND  -| 7   8 |- 2Q_b
          +-------+
```

| Pin No. | Name | Function |
|:-------:|:-----|:---------|
| **1, 13** | 1CLR, 2CLR | Active-LOW Clear inputs (tied to VCC during normal counting) |
| **2, 12** | 1D, 2D | Data inputs for Flip-Flop 1 and Flip-Flop 2 |
| **3, 11** | 1CLK, 2CLK | Clock inputs (driven by 555 timer output Pin 3) |
| **4, 10** | 1PRE, 2PRE | Active-LOW Preset inputs (tied to VCC during normal counting) |
| **5, 9** | 1Q, 2Q | Non-inverted outputs ($Q_A$ and $Q_B$) driving LEDs and decoder logic |
| **6, 8** | 1Q_bar, 2Q_bar | Inverted outputs ($\overline{Q_A}$ and $\overline{Q_B}$) |
| **7** | GND | Ground rail (0V) |
| **14** | VCC | +5V Supply |

---

## 3. 7404 Hex Inverter (NOT Gates) (14-Pin DIP)

Provides inverted logic signals ($\overline{A}$ and $\overline{B}$).

```
       +---v---+
 1A  -| 1  14 |- VCC
 1Y  -| 2  13 |- 6A
 2A  -| 3  12 |- 6Y
 2Y  -| 4  11 |- 5A
 3A  -| 5  10 |- 5Y
 3Y  -| 6   9 |- 4A
GND  -| 7   8 |- 4Y
       +-------+
```

- Each gate obeys: $Y = \overline{A}$
- Pin 7 = GND, Pin 14 = VCC

---

## 4. 7408 Quad 2-Input AND Gate (14-Pin DIP)

```
       +---v---+
 1A  -| 1  14 |- VCC
 1B  -| 2  13 |- 4B
 1Y  -| 3  12 |- 4A
 2A  -| 4  11 |- 4Y
 2B  -| 5  10 |- 3B
 2Y  -| 6   9 |- 3A
GND  -| 7   8 |- 3Y
       +-------+
```

- Each gate obeys: $Y = A \cdot B$
- Pin 7 = GND, Pin 14 = VCC

---

## 5. 7432 Quad 2-Input OR Gate (14-Pin DIP)

```
       +---v---+
 1A  -| 1  14 |- VCC
 1B  -| 2  13 |- 4B
 1Y  -| 3  12 |- 4A
 2A  -| 4  11 |- 4Y
 2B  -| 5  10 |- 3B
 2Y  -| 6   9 |- 3A
GND  -| 7   8 |- 3Y
       +-------+
```

- Each gate obeys: $Y = A + B$
- Pin 7 = GND, Pin 14 = VCC

---

## 6. 7400 Quad 2-Input NAND Gate (14-Pin DIP)

```
       +---v---+
 1A  -| 1  14 |- VCC
 1B  -| 2  13 |- 4B
 1Y  -| 3  12 |- 4A
 2A  -| 4  11 |- 4Y
 2B  -| 5  10 |- 3B
 2Y  -| 6   9 |- 3A
GND  -| 7   8 |- 3Y
       +-------+
```

- Universal gate: $Y = \overline{A \cdot B}$
- Pin 7 = GND, Pin 14 = VCC

---

## 7. Common Cathode 7-Segment Display Pinout

Standard 10-pin single-digit 7-segment display (viewed from front):

```
       -- a --
     |         |
     f         b
     |         |
       -- g --
     |         |
     e         c
     |         |
       -- d --   [dp]
```

```
       (Top View Pins)
   [10] [9] [8] [7] [6]
   (g)  (f) (CC)(a) (b)
   +------------------+
   |                  |
   |     DISPLAY      |
   |                  |
   +------------------+
   (e)  (d) (CC)(c) (dp)
   [1]  [2] [3] [4] [5]
      (Bottom Pins)
```

- **Pins 3 & 8**: Common Cathode (CC) connected to GND through current-limiting resistors (~$330\,\Omega$).
- **Pins 1, 2, 4, 6, 7, 9, 10**: Anode pins for individual segments $e, d, c, b, a, f, g$.
- High level (+5V) turns a segment ON; Low level (0V) turns it OFF.
