# 🔬 Chapter 23: Sky130 PDK - Your Fabrication Technology
## Understanding the Open Source Chip Manufacturing Process!

> **"Know your technology. Know your transistors. Build better chips!"**
>
> **"তোমার technology চেনো। তোমার transistor চেনো। Better chips বানাও!"**

---

## 🎯 এই Chapter এ তুমি শিখবে:

```
✅ Sky130 PDK - Google's open process
✅ Process Technology - 130nm details
✅ Standard Cells - pre-built blocks
✅ Transistor Models - SPICE simulation
✅ Design Rules - manufacturing limits
✅ IP Blocks - Memory, IO pads
✅ Characterization - cell timing
✅ তোমার technology expert! 🎉
```

**Time Required:** 1 week (deep understanding)  
**Prerequisites:** Chapters 21-22 complete

---

## 🚀 What is Sky130?

### PDK = Process Design Kit

```
Contains everything to design chips:
✅ Design rules (DRC)
✅ Device models (SPICE)
✅ Standard cell library
✅ IO pad library
✅ Memory compilers
✅ Verification rules (LVS)
✅ Documentation

Sky130 PDK:
- Developed by: Skywater Technology
- Open sourced by: Google (2020)
- Process node: 130nm
- Status: Production-ready!
- Cost: FREE to use!

Revolutionary: First open source PDK! 🎉
```

---

## ২৩.১ Sky130 Process Technology

### 130nm CMOS Process:

```
Transistor size: 130nm minimum length
That's 0.13 micrometers!

Comparison:
- Intel 4004 (1971): 10,000nm
- Intel Pentium (1993): 800nm
- Sky130 (2020): 130nm
- Modern (2024): 3-5nm

130nm is:
→ Old by today's standards
→ But perfect for learning!
→ Still used in automotive, IoT
→ Cheaper to manufacture
→ More forgiving design rules
```

### Available Layers:

```
Metal Layers: 5 layers
- Metal1 (local interconnect)
- Metal2-Metal3 (routing)
- Metal4-Metal5 (power, long routes)

Device Layers:
- N-diffusion (NMOS transistors)
- P-diffusion (PMOS transistors)  
- Polysilicon (transistor gates)
- N-well (isolation)

Special Layers:
- Deep N-well
- High-voltage devices
- Varactors (voltage variable caps)
- Resistors (poly, diffusion)
```

---

## ২৩.২ Standard Cell Library

### What's in the Library?

```
sky130_fd_sc_hd (High Density):
- 500+ standard cells
- Optimized for density
- Most common choice

Variants:
- sky130_fd_sc_hd: High density
- sky130_fd_sc_hdll: High density low leakage
- sky130_fd_sc_hs: High speed
- sky130_fd_sc_ms: Medium speed
- sky130_fd_sc_ls: Low speed
- sky130_fd_sc_lp: Low power
```

### Common Cells:

```
Logic Gates:
✅ AND2, AND3, AND4
✅ OR2, OR3, OR4
✅ NAND2, NAND3, NAND4
✅ NOR2, NOR3, NOR4
✅ XOR2, XNOR2
✅ INV (inverter)
✅ BUF (buffer)

Flip-Flops:
✅ DFF (D flip-flop)
✅ DFFR (with reset)
✅ DFFS (with set)
✅ DFFSR (with set and reset)
✅ LATCH

Multiplexers:
✅ MUX2, MUX4
✅ MUX2I (with invert)

Adders:
✅ FA (full adder)
✅ HA (half adder)

Total: 500+ cells!
```

### Cell Naming Convention:

```
Example: sky130_fd_sc_hd__and2_1

sky130     - Process
fd         - Foundry (SkyWater)
sc         - Standard cell
hd         - High density
__         - Separator
and2       - Function (2-input AND)
_1         - Drive strength

Drive strengths: _1, _2, _4, _8
(Higher = stronger, faster, bigger, more power)
```

---

## ২৩.৩ Transistor Models

### SPICE Models:

```
For circuit simulation:
- NMOS: nfet_01v8
- PMOS: pfet_01v8
- High voltage NMOS: nfet_g5v0d10v5
- High voltage PMOS: pfet_g5v0d10v5

Parameters include:
- Threshold voltage (Vth)
- Mobility
- Channel length modulation
- Gate oxide thickness
- Junction capacitances

Used in: Ngspice, Xyce, HSPICE
```

### Example SPICE Netlist:

```spice
* Simple inverter
.include "sky130_fd_pr/models/sky130.lib.spice tt"

Xm1 out in gnd gnd nfet_01v8 w=1 l=0.15
Xm2 out in vdd vdd pfet_01v8 w=2 l=0.15

Vdd vdd 0 1.8
Vin in 0 pulse(0 1.8 0 10p 10p 100n 200n)

.tran 1p 400n
.end
```

---

## ২৩.৪ Design Rules

### Minimum Dimensions:

```
Metal1:
- Min width: 140nm
- Min spacing: 140nm
- Min area: 0.083 µm²

Metal2-3:
- Min width: 140nm
- Min spacing: 140nm

Metal4-5:
- Min width: 290nm
- Min spacing: 290nm

Polysilicon:
- Min width: 150nm
- Min spacing: 210nm

Via:
- Size: 150nm × 150nm
- Spacing: 170nm
```

### Why Rules Matter:

```
Violate rules → Chip fails!

Example failures:
❌ Too narrow wire → Opens (breaks)
❌ Too close wires → Shorts (touch)
❌ Missing via → No connection
❌ Wrong enclosure → Reliability issues

Magic/KLayout checks these automatically!
```

---

## ২৩.৫ IP Blocks

### SRAM Compiler:

```
OpenRAM - Open source memory compiler

Generates SRAMs:
- Size: 32 bytes to 16 KB
- Ports: Single or dual port
- Word width: 8, 16, 32, 64 bits

Usage:
python3 openram.py myconfig.py

Outputs:
- GDSII layout
- Verilog model  
- SPICE netlist
- Timing library

Add memory to your chip! 🧠
```

### IO Pads:

```
For chip-to-outside connection:

Available pads:
✅ Digital input
✅ Digital output
✅ Bidirectional
✅ Analog
✅ Power (VDD, VSS)
✅ ESD protection

Pad ring:
Surrounds your core logic
Connects to package pins

Size: ~100µm × 100µm per pad
```

---

## ২৩.৬ Cell Characterization

### Timing Parameters:

```
For each standard cell:

Delays:
- Input to output delay (tpd)
- Rise time (tr)
- Fall time (tf)

Constraints:
- Setup time (tsu)
- Hold time (th)
- Clock-to-Q (tcq)

Varies with:
- Input slew
- Output load
- Voltage
- Temperature
```

### Liberty Format (.lib):

```
cell (sky130_fd_sc_hd__and2_1) {
    area : 3.5;
    
    pin(A) {
        direction : input;
        capacitance : 0.0015;
    }
    
    pin(Y) {
        direction : output;
        function : "(A&B)";
        timing() {
            related_pin : "A";
            cell_rise(delay_template) {
                index_1 ("0.01, 0.1, 1");
                index_2 ("0.01, 0.1, 1");
                values (
                    "0.1, 0.15, 0.3",
                    "0.12, 0.17, 0.32",
                    "0.2, 0.25, 0.4"
                );
            }
        }
    }
}
```

---

## ২৩.৭ Process Corners

### What are Corners?

```
Manufacturing variations:
- Transistors not exactly 130nm
- Vary ±10% or more!

Process corners:
✅ TT (Typical-Typical) - Nominal
✅ FF (Fast-Fast) - Best case
✅ SS (Slow-Slow) - Worst case
✅ FS (Fast-Slow) - Skewed
✅ SF (Slow-Fast) - Skewed

Must verify all corners!
```

### Corner Testing:

```
Your design must work at:
- SS corner: Slowest
- FF corner: Fastest  
- TT corner: Typical
- All temperatures: -40°C to 125°C
- All voltages: ±10%

If works at all corners → Robust design! ✅
```

---

## ২৩.৮ Analog Capabilities

### Analog Devices:

```
Sky130 includes:
✅ BJT transistors
✅ Varactors
✅ Resistors (multiple types)
✅ Capacitors (MiM, MoM)
✅ Photodiodes
✅ ESD devices

Can build:
→ Analog-to-Digital Converters (ADC)
→ Digital-to-Analog Converters (DAC)
→ Voltage references
→ Amplifiers
→ PLLs
→ Complete mixed-signal chips!
```

---

## ২৩.৯ Documentation

### Where to Learn More:

```
Official docs:
→ sky130-pdk.readthedocs.io
→ Design rules manual
→ Device specifications
→ Standard cell docs

GitHub:
→ github.com/google/skywater-pdk
→ All source files
→ Examples
→ Issues/discussions

Very well documented! 📚
```

---

## ২৩.১০ Using Sky130 in Your Design

### Best Practices:

```
1. Start with standard cells
   - Don't design custom gates first time
   - Use library cells
   - Proven, characterized

2. Follow design rules
   - Use DRC-clean layouts only
   - Magic checks this
   - Fix ALL violations

3. Simulate extensively
   - Test all corners
   - Verify timing
   - Check power

4. Use reference designs
   - Learn from examples
   - Caravel SoC
   - TinyTapeout projects

5. Community support
   - Ask questions!
   - Share learnings
   - Help others
```

---

## ২৩.১১ Chapter 23 Mission Complete!

### তুমি এখন জানো:

```
✅ Sky130 PDK complete overview
✅ Process technology details
✅ Standard cell library
✅ Transistor models
✅ Design rules
✅ IP blocks (SRAM, IO)
✅ Cell characterization
✅ Process corners
✅ Ready for real chip! 🎉
```

### Knowledge Level:

```
You now understand:
→ How chips are manufactured
→ What's in a PDK
→ How to use standard cells
→ Design rule constraints
→ Timing characterization

Professional knowledge! 💼
```

---

## 🎯 Chapter Exercise

### Project: Characterize a Standard Cell

```
Task: Analyze sky130_fd_sc_hd__and2_1

1. Extract from GDSII
   magic -T sky130A.tech
   gds read sky130_fd_sc_hd.gds
   load sky130_fd_sc_hd__and2_1

2. View layout
   - Count transistors
   - Measure dimensions
   - Identify layers

3. Simulate in SPICE
   - Extract netlist
   - Run transient analysis
   - Measure delay

4. Compare with .lib
   - Check documented delay
   - Verify functionality
   - Understand variations
```

---

## 🏆 Achievement Unlocked!

```
Level 23: ✅ COMPLETE - PDK Expert!
Progress: [████████████████████████████████████] 92%

XP Gained: +1500
Skills: PDK Knowledge, Process Technology

Badges Earned:
🥉 Sky130 Expert
🥈 Standard Cell Master
🥇 Design Rule Pro
🏅 Process Corner Tester
🎖️ Transistor Guru

NEXT: Submit to TinyTapeout! 📤
```

---

**[⬅️ Previous: Chapter 22](Chapter_22_OpenLane_Physical_Design.md)** | **[➡️ Next: Chapter 24](Chapter_24_TinyTapeout.md)**

---

<div align="center">

**"You know the technology. Time to submit your chip!"**

**"তুমি technology জানো। এবার chip submit করো!"**

Made with ❤️ for IC designers | IC designers দের জন্য ভালোবাসা দিয়ে তৈরি

</div>
