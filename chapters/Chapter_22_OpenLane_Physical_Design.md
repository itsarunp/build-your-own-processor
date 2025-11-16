# 🛠️ Chapter 22: OpenLane & Physical Design Hands-On
## Build Your Chip Layout with Open Source Tools!

> **"Theory was good. Now let's BUILD the actual chip layout!"**
>
> **"Theory ভালো ছিল। এবার actual chip layout বানাই!"**

---

## 🎯 এই Chapter এ তুমি করবে:

```
✅ OpenLane Setup - complete toolchain
✅ First Design Flow - simple circuit
✅ Your Processor Layout - real chip!
✅ Floorplan Exploration - see the layout
✅ Timing Optimization - meet constraints
✅ Power Analysis - how much power
✅ Final GDSII - ready for fab!
✅ তোমার chip layout complete! 🎉
```

**Time Required:** 2 weeks (hands-on practice)  
**Prerequisites:** Chapter 21 complete

---

## 🚀 OpenLane - Complete ASIC Flow

### What is OpenLane?

```
OpenLane = Complete automated RTL-to-GDSII flow
Developed by: Efabless (Google partnership)
Used in: TinyTapeout, ChipIgnite, etc.

Features:
✅ Fully automated
✅ Open source
✅ Production-tested
✅ Sky130 PDK integrated
✅ Docker container available
✅ Active community

Perfect for learning and real chips!
```

---

## ২২.১ Setup OpenLane

### System Requirements:

```
Hardware:
- 8+ GB RAM (16 GB recommended)
- 50+ GB disk space
- Linux (Ubuntu 20.04/22.04)
- Or use Docker!

Software:
- Docker (easiest)
- OR native install (complex)

We'll use Docker! 🐳
```

### Installation Steps:

```bash
# 1. Install Docker
sudo apt update
sudo apt install docker.io
sudo usermod -aG docker $USER
# Logout and login again

# 2. Pull OpenLane
git clone https://github.com/The-OpenROAD-Project/OpenLane.git
cd OpenLane
make pull-openlane

# 3. Test installation
make test

# Should see: "Basic test passed"
```

---

## ২২.২ First Design: Simple Inverter

### Create Design Directory:

```bash
cd OpenLane/designs
mkdir my_inverter
cd my_inverter
```

### Create Verilog File:

```verilog
// inverter.v
module inverter(
    input wire in,
    output wire out
);
    assign out = ~in;
endmodule
```

### Create Config File:

```tcl
// config.tcl
set ::env(DESIGN_NAME) inverter

set ::env(VERILOG_FILES) [glob $::env(DESIGN_DIR)/src/*.v]

set ::env(CLOCK_PERIOD) "10.0"
set ::env(CLOCK_PORT) ""

set ::env(FP_CORE_UTIL) 40
set ::env(FP_ASPECT_RATIO) 1
set ::env(FP_PDN_VPITCH) 25
set ::env(FP_PDN_HPITCH) 25

set ::env(PL_TARGET_DENSITY) 0.5
```

### Run OpenLane:

```bash
# In OpenLane directory
make mount

# Inside Docker container
./flow.tcl -design inverter -tag run1

# Wait 5-10 minutes...
# Should complete successfully!
```

### View Results:

```bash
# Results in:
designs/inverter/runs/run1/results/

# View layout
klayout designs/inverter/runs/run1/results/final/gds/inverter.gds

# View reports
cat designs/inverter/runs/run1/reports/final/summary.rpt
```

🎉 **You made your first chip layout!**

---

## ২২.৩ Understanding OpenLane Flow

### Flow Steps:

```
1. Synthesis
   Input: Verilog
   Output: Gate-level netlist
   Tool: Yosys

2. Floorplanning
   Input: Netlist
   Output: Die size, IO placement
   Tool: OpenROAD

3. Placement
   Input: Netlist + Floorplan
   Output: Cell positions
   Tool: RePlAce

4. CTS (Clock Tree Synthesis)
   Input: Placed design
   Output: Clock distribution
   Tool: TritonCTS

5. Routing
   Input: Placed + CTS
   Output: Metal connections
   Tool: FastRoute + TritonRoute

6. GDSII Generation
   Input: Routed design
   Output: Final layout
   Tool: Magic

7. Verification
   Input: GDSII
   Output: DRC/LVS reports
   Tool: Magic + Netgen
```

### Interactive Mode:

```bash
# For debugging, use interactive mode
./flow.tcl -interactive

# Now run step by step:
prep -design inverter

run_synthesis
run_floorplan
run_placement
run_cts
run_routing
run_magic
run_magic_spice_export
run_lvs
run_antenna_check

# View at each stage!
```

---

## ২২.৪ Your RISC-V Processor Layout

### Prepare Processor Design:

```bash
# Create processor design directory
cd OpenLane/designs
mkdir riscv_core
cd riscv_core
mkdir src
```

### Simplified Processor for First Tapeout:

```verilog
// riscv_simple.v
// Simplified RISC-V for first chip
module riscv_simple (
    input wire clk,
    input wire reset,
    input wire [7:0] gpio_in,
    output wire [7:0] gpio_out
);
    // Simplified version of your processor
    // Just RV32I base, 16 registers
    // 512 byte instruction memory
    // 512 byte data memory
    
    // (Use your actual processor code
    //  but simplified for area constraints)
    
endmodule
```

### Config for Processor:

```tcl
// config.tcl
set ::env(DESIGN_NAME) riscv_simple

set ::env(VERILOG_FILES) [glob $::env(DESIGN_DIR)/src/*.v]

# Clock: 50 MHz (20ns period)
set ::env(CLOCK_PERIOD) "20.0"
set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) "clk"

# Chip size (for TinyTapeout: 160um x 100um)
set ::env(FP_CORE_UTIL) 50
set ::env(FP_ASPECT_RATIO) 1
set ::env(FP_SIZING) absolute
set ::env(DIE_AREA) "0 0 200 200"

# Placement density
set ::env(PL_TARGET_DENSITY) 0.55

# IO placement
set ::env(FP_PIN_ORDER_CFG) $::env(DESIGN_DIR)/pin_order.cfg

# Routing
set ::env(ROUTING_CORES) 4

# Power
set ::env(VDD_NETS) [list {vccd1}]
set ::env(GND_NETS) [list {vssd1}]
```

### Run Processor Flow:

```bash
./flow.tcl -design riscv_simple -tag run1

# Will take 1-2 hours depending on design size
# Monitor progress in terminal

# Common issues and fixes:
# - Timing violations → Increase clock period
# - Congestion → Reduce density
# - DRC violations → Check design rules
```

---

## ২২.৫ Analyzing Results

### Synthesis Report:

```bash
cat designs/riscv_simple/runs/run1/reports/synthesis/1-synthesis.stat.rpt
```

Example output:
```
Chip area for module '\riscv_simple': 12500.000000

Number of cells:               850
Number of flip-flops:          250
Number of logic gates:         600

Max frequency: 55.2 MHz
```

### Timing Report:

```bash
cat designs/riscv_simple/runs/run1/reports/signoff/sta.rpt
```

```
Worst slack: -0.23 ns (VIOLATED!)
Critical path: clk → register → ALU → register

Need to:
- Increase clock period, OR
- Optimize critical path
```

### Power Report:

```bash
cat designs/riscv_simple/runs/run1/reports/signoff/power.rpt
```

```
Total Power: 2.5 mW @ 1.8V
Dynamic: 2.0 mW
Leakage: 0.5 mW

Good for IoT applications!
```

### Area Report:

```
Core area: 0.025 mm²
Total die area: 0.04 mm²
Utilization: 62%

Fits in TinyTapeout tile! ✅
```

---

## ২২.৬ Optimization Techniques

### Fix Timing Violations:

```tcl
# Option 1: Relax clock
set ::env(CLOCK_PERIOD) "25.0"  # 40 MHz instead of 50 MHz

# Option 2: Better placement
set ::env(PL_TARGET_DENSITY) 0.50  # More space

# Option 3: Use faster cells
set ::env(SYNTH_STRATEGY) "DELAY 0"

# Option 4: Add buffers
set ::env(SYNTH_BUFFERING) 1
set ::env(SYNTH_SIZING) 1

# Re-run
./flow.tcl -design riscv_simple -tag run2
```

### Reduce Area:

```tcl
# Use smaller cells
set ::env(SYNTH_STRATEGY) "AREA 0"

# Increase density
set ::env(PL_TARGET_DENSITY) 0.65

# Less routing resources
set ::env(GLB_RT_ADJUSTMENT) 0.15
```

### Reduce Power:

```tcl
# Clock gating
set ::env(CLOCK_GATING_ENABLE) 1

# Low power cells
set ::env(STD_CELL_LIBRARY) "sky130_fd_sc_lp"

# Power optimization
set ::env(PL_RESIZER_DESIGN_OPTIMIZATIONS) 1
set ::env(PL_RESIZER_TIMING_OPTIMIZATIONS) 1
```

---

## ২২.৭ Viewing Your Chip Layout

### KLayout - The Best Free Viewer:

```bash
# Install
sudo apt install klayout

# Or AppImage:
wget https://www.klayout.de/downloads/klayout-0.28-linux.AppImage
chmod +x klayout-0.28-linux.AppImage
./klayout-0.28-linux.AppImage

# Open your design
klayout designs/riscv_simple/runs/run1/results/final/gds/riscv_simple.gds
```

### What You'll See:

```
Layers visible:
- Metal 1-5 (wires)
- Vias (connections)
- Polysilicon (transistor gates)
- Diffusion (transistor sources/drains)
- N-well, P-well

Features:
✅ Zoom in/out
✅ Measure distances
✅ Check design rules
✅ Export images
✅ 3D view (Tools → 3D view)

Cool! You can see YOUR processor! 🔬
```

---

## ২২.৮ Design Rule Checking (DRC)

### Run Magic DRC:

```bash
# Already done in flow, but can re-run:
magic -dnull -noconsole << EOF
gds read designs/riscv_simple/runs/run1/results/final/gds/riscv_simple.gds
load riscv_simple
drc on
drc check
drc count
quit
EOF
```

### Check Reports:

```bash
cat designs/riscv_simple/runs/run1/reports/signoff/drc.rpt

# Should be:
# DRC violations: 0
# If not, need to fix!
```

---

## ২২.৯ Layout vs Schematic (LVS)

### What is LVS?

```
Compares:
1. Layout (from GDSII)
2. Schematic (from netlist)

Checks:
✅ All connections match
✅ All devices present
✅ Correct device sizes

Must pass for fabrication!
```

### Run LVS:

```bash
# Already done in flow
cat designs/riscv_simple/runs/run1/reports/signoff/lvs.rpt

# Should see:
# Circuits match uniquely.
# Total errors = 0
```

---

## ২২.১০ Chip Summary

### Generate Datasheet:

Your processor specs:
```
═══════════════════════════════════════
    RISC-V Simple Processor v1.0
═══════════════════════════════════════

Architecture:
- ISA: RV32I (base)
- Registers: 16 × 32-bit
- Pipeline: Single-cycle (educational)

Memory:
- Instruction: 512 bytes on-chip
- Data: 512 bytes on-chip
- External via GPIO

Performance:
- Clock: 40-50 MHz
- IPC: ~0.8 (with stalls)
- MIPS: ~35

Power:
- Supply: 1.8V
- Power: 2.5 mW
- Leakage: 0.5 mW

Physical:
- Technology: Sky130 (130nm)
- Die area: 0.04 mm²
- Package: QFN-24 (recommended)

IO:
- GPIO: 8 input, 8 output
- Clock: 1 input
- Reset: 1 input
- Power/Ground: 2 pins

Total: 20 pins (fits in standard package)

Applications:
- IoT sensor nodes
- Educational processors
- Embedded control
- Custom computing

═══════════════════════════════════════
```

---

## ২২.১১ Chapter 22 Mission Complete!

### তুমি এখন পারো:

```
✅ Setup OpenLane
✅ Run complete ASIC flow
✅ Optimize timing/area/power
✅ View chip layout in KLayout
✅ Understand DRC/LVS
✅ Generate GDSII for fab
✅ Your processor is layout-ready! 🎉
```

### তোমার কাছে এখন:

```
✅ Working GDSII file
✅ Verified layout (DRC passed)
✅ Matched schematic (LVS passed)
✅ Timing report (meets constraints)
✅ Power analysis
✅ Area report
✅ READY FOR FABRICATION! 🏭
```

---

## 🎯 Chapter Exercise

### Project: Optimize Your Processor

**Goal:** Achieve best PPA (Power, Performance, Area)

```
Try 3 runs with different goals:

Run 1: Maximum Performance
- Target: Highest frequency
- Adjust: Clock period, buffering
- Metric: MHz achieved

Run 2: Minimum Area
- Target: Smallest die
- Adjust: Density, strategy
- Metric: mm² used

Run 3: Lowest Power
- Target: Minimum power
- Adjust: Clock gating, cell library
- Metric: mW consumed

Compare and choose best trade-off!
```

---

## 🏆 Achievement Unlocked!

```
Level 22: ✅ COMPLETE - Physical Designer!
Progress: [███████████████████████████████████] 88%

XP Gained: +2500
Skills: OpenLane, Physical Design, Layout

Badges Earned:
🥉 OpenLane Expert
🥈 Layout Viewer
🥇 Chip Optimizer
🏅 DRC/LVS Master
🎖️ GDSII Generator
⭐ CHIP LAYOUT COMPLETE! ⭐

NEXT: Deep dive into Sky130! 🔬
```

---

**[⬅️ Previous: Chapter 21](Chapter_21_VLSI_Design_Flow.md)** | **[➡️ Next: Chapter 23](Chapter_23_Sky130_PDK.md)**

---

<div align="center">

**"Your processor has a layout! Real chip coming soon!"**

**"তোমার processor এর layout হয়ে গেছে! Real chip শীঘ্রই!"**

Made with ❤️ for chip makers | চিপ মেকারদের জন্য ভালোবাসা দিয়ে তৈরি

</div>
