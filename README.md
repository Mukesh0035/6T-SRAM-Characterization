# 6-Transistor (6 T) SRAM Design & Characterization  
*A Cadence Virtuoso™ Project Guide*

## Project Overview
This repository documents the full custom design, simulation for a **6-Transistor (6 T) Static Random-Access Memory (SRAM) bit-cell** implemented in Cadence Virtuoso using the free GPDK-45 nm process.  
Key deliverables include:

* DC operating-point (VTC / butterfly curve) characterization.  
* Process-Voltage-Temperature (PVT) corner sweep executed in **ADE Assembler**.
* 

## 1  |  What Is an SRAM Cell?
Static RAM stores data in a **bistable latch** rather than on a capacitor, eliminating the need for periodic refresh. The canonical latch is realized with **six MOSFETs**—four form two cross-coupled inverters while two act as access gates—hence the term *6 T* SRAM cell.

## 2  |  6 T Cell Construction

| Node | Device | Function |
|------|--------|----------|
| Q, QB | MP1-2 (PMOS) | Pull-up transistors |
| Q, QB | MN1-2 (NMOS) | Pull-down transistors |
| BL, BLB | MAXL-AXR (NMOS) | Pass (access) transistors |

1. **Cross-Coupled Inverters**  
   MP1/MN1 and MP2/MN2 create a latch with two stable states (logic 0 or 1).  
2. **Access Path**  
   The pass devices connect the internal nodes to the complementary **bit-lines** only when the **word-line** is asserted.

## 3  |  Word-Line (WL) and Bit-Lines (BL/BLB)

* **Word-Line (WL)**  
  -  Runs horizontally across an entire row.  
  -  A high pulse turns both access NMOS devices **ON**, enabling a read or write.  
  -  Pulse width is carefully tuned—too long wastes energy; too short risks corrupt data [3].

* **Bit-Line Pair (BL, BLB)**  
  -  Column wires that serve as differential data rails.  
  -  **Read:** Both lines pre-charge high; the cell pulls one low, creating a small ΔV detected by a sense-amp.  
  -  **Write:** External drivers force BL/BLB to opposite rails; the stronger drivers overpower the latch and flip its state [4].

## 4  |  Design Environment

| Tool | Role |
|------|------|
| **Virtuoso Schematic Editor** | Transistor-level capture |
| **ADE Assembler** | Multi-test benches, DC & corner sweeps |
| **Process Kit** | GPDK-45 nm (TT, FF, SS, FS, SF corners) |


## 6  |  Simulation Flow

### 6.1 DC Analysis (Butterfly Curve)
1. In **ADE Assembler**, add a *DC sweep* on BL from 0 V→VDD while BLB is held at VDD.  
2. Plot `Q` vs `QB` twice (mirror curves) to form the butterfly.  
3. Measure SNM as the side of the largest square fitting between lobes [5].

> *Image placeholder*  
> `![Butterfly Curve](doc/images/butterfly_snm PVT Corner Sweep
1. Open the **Corners** tab → *Add Corner* → import `gpdk045.scs`.  
2. Enable FF, TT, SS, FS, and SF process sets.  
3. Add **Global Variables**: `VDD = {0.9, 1.0, 1.1}` V; `TEMP = {-40, 25, 125}` °C.  
4. Run **Multi-Test** sweep; ADE labels results per corner automatically.

> *Image placeholder*  
> `![Corner Analysis](doc/images/corner_sweep Key Metrics Captured
* `read_snm`  – via DC sweep with WL = VDD.  
* `hold_snm`  – WL = 0 V.  
* `write_margin` – BL pulled low until cell flips.  
* `iread`      – current through access NMOS during read.  
* `ileak`      – standby leakage at each corner.

## 7  |  Typical Results (GPDK-45 nm)

| Metric | TT @ 25 °C | Worst Corner | Units |
|--------|-----------:|-------------:|------:|
| Hold SNM | 238 | 165 (SS, 125 °C) | mV |
| Read SNM | 217 | 148 (SS, 125 °C) | mV |
| Write Margin | 315 | 210 (FF, -40 °C) | mV |
| Iread | 71 | 38 (SS, 125 °C) | µA |
| Leakage | 42 | 88 (FF, 125 °C) | pA |

5.1 Gain & Phase Plot
![AC Gain and Phase](images/ac_gain 5.2 Butterfly Curve
![SRAM SNM Butterfly](images/butterTip** — If your image already lives online (e.g., GitHub raw link), embed directly:

![SRAM SNM Butterfly](https://raw.githubusercontent.com/Mukesh0035/6T-SRAM-Characterization/master/butter_image.png)

