# 🔧 Chapter 10: Build Your Own FPGA Projects - Tang Nano 9K!
## From Code to Hardware - Your First Real FPGA Project!

> **"Simulation was practice. Now it's REAL. Time to blink LEDs with YOUR code!"**
>
> **"Simulation ছিল practice। এখন REAL। তোমার code দিয়ে LED জ্বালাও!"**

---

## 🎯 এই Chapter এ তুমি বানাবে:

```
✅ Tang Nano 9K setup - $12 FPGA board!
✅ Gowin EDA installation - free tools
✅ First FPGA project - LED blink
✅ Constraint file - pin mapping
✅ Synthesis & implementation - place & route
✅ Programming - load to FPGA
✅ Debug techniques - real hardware
✅ তোমার processor component FPGA তে deploy! 🎉
```

**Time Required:** 1 week (4-5 hours/day)  
**Hardware Needed:** Tang Nano 9K board ($12), USB-C cable

---

## 🚀 Quick Win - 30 মিনিটে LED Blink!

### Step 1: Get the Board

```
Tang Nano 9K Board:
- Price: $12-15 USD
- FPGA: Gowin GW1NR-9C (8640 LUTs)
- Features:
  ✅ 6 LEDs onboard
  ✅ 2 Buttons
  ✅ HDMI output
  ✅ 32MB PSRAM
  ✅ USB-C (power + programming)
  ✅ Breadboard friendly!

Where to buy:
- AliExpress (cheapest)
- Seeed Studio
- Mouser/DigiKey (expensive)

Shipping: 2-4 weeks from China
```

### Step 2: Install Tools (Later!)

### Step 3: First Code

```verilog
module led_blink(
    input clk,      // 27 MHz onboard clock
    output reg led  // LED output
);
    reg [24:0] counter;
    
    always @(posedge clk) begin
        counter <= counter + 1;
        led <= counter[24];  // Blink at ~1.6 Hz
    end
endmodule
```

### Step 4: Program FPGA (Details later!)

🎉 **First LED blink - You're now a hardware engineer!**

---

## ১০.১ Tang Nano 9K Board Overview

### Board Layout:

```
Tang Nano 9K Board:

         USB-C
           │
    ┌──────┴──────┐
    │  ●  ●  ●    │  ← 6 LEDs (numbered 0-5)
    │             │
    │  ┌──────┐   │
    │  │ FPGA │   │  ← GW1NR-9C
    │  │ Chip │   │
    │  └──────┘   │
    │             │
    │  [S1] [S2]  │  ← 2 Buttons
    │             │
    │  [HDMI]     │  ← HDMI connector
    │             │
    │ [32MB PSRAM]│  ← External memory
    │             │
    │  Pin        │
    │  Headers    │  ← Breadboard pins
    └─────────────┘

Size: ~21mm × 72mm (fits breadboard!)
```

### Key Features:

```
FPGA Chip: Gowin GW1NR-9C
- 8640 LUTs (logic cells)
- 6480 Flip-Flops
- 468 Kb Block RAM (26 × 18Kb)
- 20 × 18×18 Multipliers (DSP)
- 1 × PLL (clock management)
- 174 User I/O pins

Onboard Peripherals:
✅ 27 MHz crystal oscillator
✅ 6 × LEDs (RGB × 2, single color × 3)
✅ 2 × Push buttons (S1, S2)
✅ HDMI output (video projects!)
✅ 32 MB PSRAM (external RAM)
✅ TF card slot (SD card)
✅ USB-C (programming + power)

Power:
- 5V via USB-C
- 3.3V onboard regulator
- ~100-200mA typical
```

### Pin Access:

```
All FPGA pins broken out to headers:
- Both sides of board
- 2.54mm spacing (breadboard compatible)
- Direct FPGA GPIO access
- Connect to external circuits!

Example uses:
- LED strips
- Sensors
- Motors
- Other chips (SPI, I2C)
- Custom peripherals
```

---

## ১০.২ Gowin EDA Installation

### System Requirements:

```
Operating System:
✅ Windows 7/8/10/11 (64-bit)
✅ Linux (Ubuntu 18.04+, CentOS 7+)
❌ macOS (not officially supported)

Hardware:
- CPU: x86-64, 2+ cores
- RAM: 4GB minimum, 8GB recommended
- Disk: 5GB free space
- Display: 1024×768 minimum

License: FREE for GW1N series!
```

### Installation Steps (Windows):

```
Step 1: Download
Go to: http://www.gowinsemi.com/en/support/download_eda/
Download: Gowin EDA (Education Version)
File: ~1.5 GB

Step 2: Install
- Run installer: Gowin_xxx_win.exe
- Choose installation directory
- Default: C:\Gowin\IDE\
- Click "Install"
- Wait 5-10 minutes

Step 3: License
- Education version = FREE
- No license key needed for GW1N!
- Full featured for our chip

Step 4: Programmer
- Included in IDE
- USB driver auto-installs
- No separate download needed

Step 5: Test
- Launch IDE
- Create test project
- Verify it opens
```

### Installation Steps (Linux):

```bash
# Step 1: Download
# Get .tar.gz from Gowin website

# Step 2: Extract
tar -xzf Gowin_xxx_linux.tar.gz
cd Gowin_xxx_linux

# Step 3: Run installer
chmod +x install.sh
./install.sh

# Follow prompts, default: /usr/local/Gowin/

# Step 4: Add to PATH (optional)
echo 'export PATH="/usr/local/Gowin/IDE/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Step 5: USB permissions
sudo cp gowin/programmer/driver/udev/*.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger

# Step 6: Launch
gw_ide
```

### IDE First Launch:

```
When you first open Gowin IDE:

1. Create New Project
   - File → New Project

2. Project Wizard appears
   - Project Name
   - Location
   - Device Selection ← IMPORTANT!

3. Select Device:
   - Family: GW1N-9C
   - Device: GW1NR-LV9QN88PC6/I5
   - (This is Tang Nano 9K chip!)

4. Project created!
   - Ready to add code
```

---

## ১০.৩ Your First Project - LED Blink

### Project Setup:

```
Step 1: New Project
File → New Project

Project Name: led_blink
Location: Choose folder
Device: GW1NR-LV9QN88PC6/I5
```

### Step 2: Create Verilog File

```
In Project window:
Right-click "Design" → Add File → New File

File name: led_blink.v
File type: Verilog

Click OK
```

### Step 3: Write the Code

```verilog
// led_blink.v
// First FPGA project - Blink LED!

module led_blink(
    input  wire clk,      // 27 MHz clock input
    output reg  led       // LED output
);
    // Counter for timing
    // 27,000,000 counts = 1 second
    // Use bit [24] for ~1.24 Hz blink
    reg [24:0] counter;
    
    always @(posedge clk) begin
        counter <= counter + 1;
        
        // LED toggles when counter[24] changes
        // This happens every 2^24 / 27MHz = 0.62 seconds
        led <= counter[24];
    end
endmodule
```

### Understanding the Code:

```verilog
// Clock frequency: 27 MHz = 27,000,000 Hz
// Counter width: 25 bits = 2^25 = 33,554,432

// Bit [24] toggles every:
// 2^24 / 27,000,000 = 16,777,216 / 27,000,000
//                   = 0.621 seconds

// LED period = 2 × 0.621 = 1.24 seconds
// Frequency = 1 / 1.24 = 0.81 Hz

// Want slower? Use counter[25]
// Want faster? Use counter[23]

// Formula:
// Period = 2 × (2^n / clock_freq)
// where n = bit number
```

---

## ১০.৪ Constraint File (.cst)

### What is a Constraint File?

```
Constraint file (.cst):
- Maps Verilog ports to FPGA pins
- Sets I/O standards (voltage levels)
- Defines timing constraints
- Required for every FPGA project!

Without .cst:
❌ Tools don't know which pins to use
❌ Synthesis fails
❌ Cannot generate bitstream
```

### Creating Constraint File:

```
Step 1: Add constraint file
Right-click "Design" → Add File → New File

File name: led_blink.cst
File type: Physical Constraints

Click OK
```

### Writing the Constraints:

```tcl
// led_blink.cst
// Pin constraints for Tang Nano 9K

// Clock input - 27 MHz oscillator
IO_LOC "clk" 52;
IO_PORT "clk" PULL_MODE=UP;

// LED output - LED 0 (blue)
IO_LOC "led" 10;
IO_PORT "led" PULL_MODE=UP DRIVE=8;
```

### Understanding Constraints:

```
IO_LOC: Pin location
- "clk" 52 → Connect clk signal to pin 52
- "led" 10 → Connect led signal to pin 10
- Pin numbers from Tang Nano 9K schematic

IO_PORT: Pin properties
- PULL_MODE=UP → Enable pull-up resistor
- PULL_MODE=DOWN → Enable pull-down
- PULL_MODE=NONE → No pull resistor
- DRIVE=8 → Output drive strength (mA)
  Options: 4, 8, 16, 24 mA

For LED:
- DRIVE=8 is sufficient
- Higher = brighter but more power
```

### Tang Nano 9K Pin Reference:

```
Important pins:

Clock:
- Pin 52: 27 MHz oscillator

LEDs:
- Pin 10: LED 0 (Blue)
- Pin 11: LED 1 (Green)
- Pin 13: LED 2 (Red)
- Pin 14: LED 3 (Blue)
- Pin 15: LED 4 (Green)
- Pin 16: LED 5 (Red)

Buttons:
- Pin 3: Button S1
- Pin 4: Button S2

HDMI:
- Pins 69-76, 71,77,79,80,81,82,83

Get full pinout:
- Tang Nano 9K schematic online
- Or board documentation
```

---

## ১০.৫ Synthesis and Implementation

### Design Flow Overview:

```
Verilog + .cst
     │
     ▼
  Synthesis ────► Netlist (logic gates)
     │
     ▼
  Place & Route ─► Physical layout
     │
     ▼
  Generate ──────► Bitstream (.fs)
     │
     ▼
  Program ───────► FPGA configured!
```

### Step-by-Step Process:

```
Step 1: Synthesis
Click: Process → Synthesize (or F11)

What happens:
- Reads your Verilog
- Converts to logic gates
- Optimizes logic
- Maps to LUTs and FFs
- Checks for errors

Output:
- Netlist file
- Resource usage report
- Synthesis log

Time: 5-30 seconds

Check Console for:
✅ "Synthesis task successfully"
❌ Errors (fix in code!)
⚠️ Warnings (usually OK)
```

### Step 2: Place & Route

```
Click: Process → Place & Route (or F12)

What happens:
- Reads netlist
- Assigns logic to CLBs
- Routes connections
- Applies constraints
- Timing analysis

Output:
- Placed design
- Routed design
- Timing report
- Resource report

Time: 10-60 seconds

Check for:
✅ "Place & Route task successfully"
✅ Timing constraints met
❌ Routing failures
```

### Step 3: Generate Bitstream

```
Usually automatic after Place & Route

Or manually:
Process → Generate Bitstream

Output file:
- impl/pnr/project_name.fs
- This is your bitstream!
- Contains all configuration data

File size: ~1-3 MB
```

---

## ১০.৬ Programming the FPGA

### Connecting the Board:

```
Step 1: Connect USB
- Plug Tang Nano 9K to computer
- Use USB-C cable
- Power LED should light up
- No external power needed

Step 2: Driver check (Windows)
- Device Manager
- Look for: "JTAG Debugger"
- Driver should auto-install
- If not, install from IDE folder

Step 3: Verify connection
Tools → Programmer
Click "Cable Settings"
Should detect: "USB Cable"
```

### Programming Process:

```
Step 1: Open Programmer
Tools → Programmer (Ctrl+Alt+P)

Step 2: Configure
Device: GW1NR-9C
Access Mode: SRAM Mode (temporary)
            or Flash Mode (permanent)

Step 3: Add bitstream
Click "Browse"
Select: impl/pnr/led_blink.fs

Step 4: Program!
Click "Program/Configure" button
Watch progress bar

Time: 5-10 seconds

Result:
✅ Programming success!
✅ LED should start blinking!
```

### SRAM Mode vs Flash Mode:

```
┌─────────────┬──────────────┬─────────────┐
│ Feature     │  SRAM Mode   │ Flash Mode  │
├─────────────┼──────────────┼─────────────┤
│ Speed       │ Fast (~5s)   │ Slower (20s)│
│ Volatile    │ Yes          │ No          │
│ Power cycle │ Lost         │ Saved       │
│ Use case    │ Development  │ Production  │
│ Writes      │ Unlimited    │ Limited     │
└─────────────┴──────────────┴─────────────┘

Development: Use SRAM mode
- Fast reprogramming
- Unlimited writes
- Good for testing

Production: Use Flash mode
- Design survives power-off
- Board boots automatically
- Final deployment
```

---

## ১০.৭ Debugging on Hardware

### Common Issues:

```
Problem 1: LED not blinking
Possible causes:
❌ Wrong pin number in .cst
❌ Clock not connected
❌ Bitstream not loaded
❌ LED reversed (rare)

Solution:
✅ Verify pin numbers
✅ Check constraint file
✅ Reprogram FPGA
✅ Check synthesis log
```

### Debug Techniques:

```
Technique 1: Multiple LEDs
- Assign different counter bits
- See which ones work
- Isolate problem

module led_debug(
    input clk,
    output [5:0] leds
);
    reg [25:0] counter;
    
    always @(posedge clk) begin
        counter <= counter + 1;
    end
    
    // Different rates on different LEDs
    assign leds[0] = counter[20]; // Fast
    assign leds[1] = counter[21];
    assign leds[2] = counter[22];
    assign leds[3] = counter[23];
    assign leds[4] = counter[24];
    assign leds[5] = counter[25]; // Slow
endmodule
```

### Using Onboard Buttons:

```verilog
// Button-controlled LED
module button_led(
    input  wire clk,
    input  wire btn,    // Button input
    output reg  led
);
    // Debounce counter
    reg [19:0] debounce;
    reg btn_stable;
    
    always @(posedge clk) begin
        if (btn == btn_stable) begin
            debounce <= 0;
        end else begin
            debounce <= debounce + 1;
            if (debounce == 20'hFFFFF)
                btn_stable <= btn;
        end
    end
    
    // Toggle LED on button press
    always @(posedge clk) begin
        if (btn_stable && debounce == 0)
            led <= ~led;
    end
endmodule

// Constraint file additions:
// IO_LOC "btn" 3;
// IO_PORT "btn" PULL_MODE=UP;
```

### Viewing Internal Signals:

```
Problem: Can't see internal wires

Solutions:

1. Connect to LEDs
   - Assign internal signals to unused LEDs
   - Visual debugging!
   
2. UART output (advanced)
   - Send debug data via serial
   - Need USB-UART adapter
   
3. Chipscope/Logic Analyzer (advanced)
   - Gowin Analyzer tool
   - Capture internal signals
   - Like oscilloscope for FPGA
```

---

## ১০.৮ Project 2 - All LEDs Blink Pattern

### Requirements:

```
Create interesting LED pattern:
- Use all 6 LEDs
- Different blink rates
- Button to change pattern
- Reset functionality
```

### Code:

```verilog
module led_pattern(
    input  wire       clk,      // 27 MHz
    input  wire       btn1,     // S1 button
    input  wire       btn2,     // S2 button (reset)
    output reg  [5:0] leds      // 6 LEDs
);
    reg [25:0] counter;
    reg [1:0] pattern;
    
    // Counter
    always @(posedge clk) begin
        if (!btn2)  // btn2 active low (pressed = 0)
            counter <= 0;
        else
            counter <= counter + 1;
    end
    
    // Pattern selector
    always @(posedge clk) begin
        if (!btn2)
            pattern <= 0;
        else if (!btn1 && counter[20:0] == 0)
            pattern <= pattern + 1;
    end
    
    // LED patterns
    always @(*) begin
        case(pattern)
            2'b00: begin  // All blink together
                leds = {6{counter[24]}};
            end
            
            2'b01: begin  // Running light
                leds = 6'b000001 << counter[23:21];
            end
            
            2'b10: begin  // Alternating
                leds = counter[24] ? 6'b101010 : 6'b010101;
            end
            
            2'b11: begin  // Binary counter
                leds = counter[25:20];
            end
        endcase
    end
endmodule
```

### Constraint File:

```tcl
// led_pattern.cst

// Clock
IO_LOC "clk" 52;
IO_PORT "clk" PULL_MODE=UP;

// Buttons (active low)
IO_LOC "btn1" 3;
IO_PORT "btn1" PULL_MODE=UP;
IO_LOC "btn2" 4;
IO_PORT "btn2" PULL_MODE=UP;

// LEDs
IO_LOC "leds[0]" 10;
IO_LOC "leds[1]" 11;
IO_LOC "leds[2]" 13;
IO_LOC "leds[3]" 14;
IO_LOC "leds[4]" 15;
IO_LOC "leds[5]" 16;

IO_PORT "leds[0]" DRIVE=8;
IO_PORT "leds[1]" DRIVE=8;
IO_PORT "leds[2]" DRIVE=8;
IO_PORT "leds[3]" DRIVE=8;
IO_PORT "leds[4]" DRIVE=8;
IO_PORT "leds[5]" DRIVE=8;
```

---

## ১০.৯ Understanding Reports

### Resource Utilization Report:

```
After synthesis, check:
Process → View Reports → Resource Utilization

Example output:
----------------------------------------
RESOURCE UTILIZATION
----------------------------------------
Logic Elements:  45 / 8640 (0.52%)
  - LUTs:       42
  - Registers:  26
  
Block RAM:      0 / 26 (0%)
DSP:            0 / 20 (0%)
PLLs:           0 / 1  (0%)
----------------------------------------

What this means:
- Very small design!
- Lots of room for more
- No memory or DSP used
- Simple LED blink = minimal resources
```

### Timing Report:

```
Check: Reports → Timing Report

Key metrics:

Fmax (Maximum Frequency):
- How fast your design can run
- Should be > your clock frequency
- Example: Fmax = 250 MHz > 27 MHz ✓

Setup Slack:
- Positive = Good!
- Negative = Timing violation ❌
- Slack = Available - Required

Hold Slack:
- Positive = Good!
- Negative = Timing violation ❌

For LED blink:
- Very slow logic
- Timing always met
- No issues
```

---

## ১০.১০ Your 1-Week Build Plan

### Day 1: Setup
```
□ Order Tang Nano 9K
□ Install Gowin EDA
□ Familiarize with IDE
□ Test installation
```

### Day 2: First Project
```
□ Create LED blink project
□ Write Verilog code
□ Create constraint file
□ Synthesize successfully
```

### Day 3: Programming
```
□ Connect board
□ Install drivers
□ Program FPGA (SRAM mode)
□ See first LED blink! 🎉
```

### Day 4: Experiments
```
□ Change blink rate
□ Use multiple LEDs
□ Try different patterns
□ Flash mode programming
```

### Day 5: Interactive
```
□ Add button input
□ Debounce logic
□ LED toggle on press
□ Multiple patterns
```

### Day 6: Complex Design
```
□ Multi-LED patterns
□ State machine
□ Counter display
□ Debug techniques
```

### Day 7: Your Design
```
□ Design own project
□ Implement & test
□ Debug issues
□ Document design
```

---

## ১০.১১ Common Mistakes & Solutions

### Mistake 1: Wrong Pin Numbers ❌

```
Problem:
IO_LOC "led" 99;  // Wrong pin!

Symptom:
- Synthesis passes
- Programming succeeds
- Nothing happens

Solution:
✅ Check Tang Nano 9K schematic
✅ Verify pin numbers
✅ Use correct pins for LEDs
```

### Mistake 2: Missing Constraint File ❌

```
Problem:
No .cst file added

Symptom:
- Synthesis fails
- Error: "No constraint file"

Solution:
✅ Create .cst file
✅ Add to project
✅ Define all ports
```

### Mistake 3: Clock Not Constrained ❌

```
Problem:
Clock pin wrong or missing

Symptom:
- No activity on FPGA
- Design doesn't run

Solution:
✅ IO_LOC "clk" 52;
✅ Verify clock connection
✅ Check timing report
```

### Mistake 4: SRAM vs Flash Confusion ❌

```
Problem:
Programmed in SRAM, power cycled

Symptom:
- Design lost after power off

Solution:
✅ Use SRAM for development
✅ Use Flash for production
✅ Know which mode you're in!
```

---

## ১০.১২ Chapter 10 Mission Complete!

### তুমি এখন পারো:

```
✅ Setup Gowin EDA tools
✅ Create FPGA projects
✅ Write constraint files
✅ Synthesize designs
✅ Program FPGA (SRAM/Flash)
✅ Debug on hardware
✅ Use LEDs and buttons
✅ Deploy Verilog to REAL hardware! 🎉
```

### তুমি বানিয়েছো:
```
✅ LED blink project
✅ Multi-LED patterns
✅ Button-controlled design
✅ Working FPGA system!
✅ REAL hardware from YOUR code! 🔧
```

### Stats:
```
FPGA projects: 2+
LEDs controlled: 6
Code deployed: To real silicon!
Experience: Hardware engineer!
Level: FPGA Developer! 🏆
```

### Next Level Unlocked:
```
→ Chapter 11: FPGA Projects
   তুমি শিখবে: Advanced projects
   UART, VGA, SPI, Real peripherals!
   
   From LEDs → Complete systems!
```

---

## 🎯 Final Project

### Project: Binary Counter with 7-Segment Display

**Requirements:**
```
Create 4-bit counter:
✅ Display on 6 LEDs
✅ Count up/down button
✅ Reset button
✅ Speed control
✅ Pause/resume
✅ Complete documentation

Bonus:
- Add external 7-segment display
- BCD conversion
- Multiple digits
```

---

## 🏆 Achievement Unlocked!

```
Level 10: ✅ COMPLETE - Hardware Engineer!
Progress: [██████████████████████████████████] 50%

XP Gained: +5000 🎉
Skills: FPGA Programming, Hardware Deployment

Badges Earned:
🥉 First FPGA Project
🥈 LED Blink Master
🥇 Constraint File Expert
🏅 Hardware Debugger
🎖️ Multi-Project Developer
🏆 REAL Hardware Engineer!

MILESTONE: 50% COMPLETE! 🎊
You're now deploying to REAL hardware!

Next: Chapter 11 - Advanced FPGA Projects!
      UART, VGA, Peripherals! 🚀
```

---

**[⬅️ Previous: Chapter 9](Chapter_09_FPGA_Architecture.md)** | **[➡️ Next: Chapter 11](Chapter_11_FPGA_Projects.md)**

---

<div align="center">

**"Your code is now running on real silicon. You're a hardware engineer!"**

**"তোমার code এখন real silicon এ চলছে। তুমি hardware engineer!"**

Made with ❤️ for builders | বানানোর জন্য ভালোবাসা দিয়ে তৈরি

</div>
