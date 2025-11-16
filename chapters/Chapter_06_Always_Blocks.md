# ⚡ Chapter 6: Build Your Own Sequential Logic - In Code!
## Always Blocks থেকে Registers - তোমার Processor কে Memory দাও Code এ!

> **"assign is great for wires. always is great for memory. Time to add state!"**
>
> **"assign ভালো wire এর জন্য। always ভালো memory র জন্য। এবার state যোগ করো!"**

---

## 🎯 এই Chapter এ তুমি বানাবে:

```
✅ D Flip-Flop - in Verilog
✅ 8-bit Register - with enable
✅ Counter - up/down counter
✅ Shift Register - SISO, SIPO
✅ Finite State Machine - traffic light controller
✅ Blocking vs Non-blocking - THE difference! ⚠️
✅ তোমার processor এর registers - in code! 🎉
```

**Time Required:** 1 week (4-5 hours/day)  
**Tools Needed:** Text editor, Icarus Verilog, GTKWave

**⚠️ WARNING: এই chapter সবচেয়ে important! Blocking/Non-blocking ভুল হলে circuit কাজ করবে না!**

---

## 🚀 Quick Win - 5 মিনিটে তোমার First Sequential Code!

### এখনই লেখো - D Flip-Flop in Verilog:

**Create file: `d_ff.v`**

```verilog
// তোমার প্রথম sequential logic!
module d_ff(
    input  clk,    // Clock
    input  d,      // Data input
    output reg q   // Output (reg কারণ always block এ)
);
    // Sequential logic - always @(posedge clk)
    always @(posedge clk) begin
        q <= d;  // Non-blocking assignment!
    end
endmodule
```

🎉 **Congratulations! তুমি hardware memory code করেছো!**

**এই 8 lines = একটা D Flip-Flop chip!**

---

## ৬.১ Always Blocks - The Heart of Sequential Logic

### assign vs always:

```verilog
// assign - Continuous (combinational)
assign y = a & b;
// Updates immediately when a or b changes
// No memory, just wires

// always - Procedural (can be sequential)
always @(posedge clk) begin
    q <= d;
end
// Updates only at clock edge
// Has memory!
```

### Two Types of always Blocks:

```verilog
// Type 1: Combinational always (Chapter 5 এও দেখেছো)
always @(*) begin
    y = a & b;
end
// @(*) = sensitive to all inputs
// Behaves like assign

// Type 2: Sequential always (New!)
always @(posedge clk) begin
    q <= d;
end
// @(posedge clk) = triggers on clock rising edge
// Creates flip-flops/registers!
```

---

## ৬.২ Blocking vs Non-Blocking - ⚠️ CRITICAL!

### THE Most Important Concept:

```verilog
// Blocking (=) - Executes sequentially
// Non-blocking (<=) - Executes in parallel

// This difference breaks or makes your circuit!
```

### Blocking Assignment (=):

```verilog
always @(posedge clk) begin
    a = b;      // Execute first
    c = a;      // Then execute (uses NEW value of a)
end

// Result: c gets value of b (passed through a)
// Like: c = a = b
// Serial execution!
```

### Non-blocking Assignment (<=):

```verilog
always @(posedge clk) begin
    a <= b;     // Schedule for end of block
    c <= a;     // Schedule for end of block (uses OLD value of a)
end

// Result: a gets b, c gets OLD a (swap!)
// Parallel execution!
// This is what you want for flip-flops!
```

### Visual Comparison:

```verilog
// Example: Shift register (3-bit)

// ❌ WRONG - Using blocking
always @(posedge clk) begin
    q2 = q1;    // q2 gets q1
    q1 = q0;    // q1 gets q0 (but q2 already got this!)
    q0 = d;     // q0 gets d
end
// Result: d flows through all in ONE clock!
// Not a shift register!

// ✅ CORRECT - Using non-blocking
always @(posedge clk) begin
    q2 <= q1;   // q2 will get OLD q1
    q1 <= q0;   // q1 will get OLD q0
    q0 <= d;    // q0 will get d
end
// Result: Proper shift register!
// Each stage updates simultaneously with OLD values
```

### The Golden Rules:

```verilog
// Rule 1: Sequential logic → Use non-blocking (<=)
always @(posedge clk) begin
    q <= d;  // ✅ Correct
end

// Rule 2: Combinational logic → Use blocking (=)
always @(*) begin
    y = a & b;  // ✅ Correct
end

// Rule 3: NEVER mix blocking and non-blocking
always @(posedge clk) begin
    a <= b;  // Non-blocking
    c = a;   // ❌ WRONG! Don't mix!
end

// Rule 4: Use <= for flip-flops, = for wires
```

---

## ৬.৩ Sequential Always Block - Flip-Flops

### Basic D Flip-Flop:

```verilog
module d_ff(
    input      clk,
    input      d,
    output reg q
);
    always @(posedge clk) begin
        q <= d;
    end
endmodule
```

### D Flip-Flop with Reset:

```verilog
module d_ff_reset(
    input      clk,
    input      reset,  // Asynchronous reset
    input      d,
    output reg q
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;  // Reset to 0
        else
            q <= d;     // Normal operation
    end
endmodule
```

### D Flip-Flop with Synchronous Reset:

```verilog
module d_ff_sync_reset(
    input      clk,
    input      reset,  // Synchronous reset
    input      d,
    output reg q
);
    always @(posedge clk) begin
        if (reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

### D Flip-Flop with Enable:

```verilog
module d_ff_enable(
    input      clk,
    input      en,     // Enable
    input      d,
    output reg q
);
    always @(posedge clk) begin
        if (en)
            q <= d;    // Load when enabled
        // else: hold previous value
    end
endmodule
```

---

## ৬.৪ Build Registers - Multi-bit Storage

### 8-bit Register (Simple):

```verilog
module register_8bit(
    input            clk,
    input      [7:0] d,
    output reg [7:0] q
);
    always @(posedge clk) begin
        q <= d;
    end
endmodule
```

### 8-bit Register with Enable and Reset:

```verilog
module register_8bit_full(
    input            clk,
    input            reset,
    input            en,
    input      [7:0] d,
    output reg [7:0] q
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 8'b0;       // Async reset
        else if (en)
            q <= d;          // Load when enabled
        // else: hold
    end
endmodule
```

### Parameterized Register (Any width!):

```verilog
module register_param #(
    parameter WIDTH = 8  // Default 8-bit
)(
    input                    clk,
    input                    reset,
    input                    en,
    input      [WIDTH-1:0]   d,
    output reg [WIDTH-1:0]   q
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= {WIDTH{1'b0}};  // All zeros
        else if (en)
            q <= d;
    end
endmodule

// Usage:
// register_param #(.WIDTH(16)) reg16(...);  // 16-bit
// register_param #(.WIDTH(32)) reg32(...);  // 32-bit
```

---

## ৬.৫ if-else Statements

### Basic if-else:

```verilog
always @(*) begin
    if (sel == 0)
        y = a;
    else
        y = b;
end
```

### Multiple if-else:

```verilog
always @(*) begin
    if (sel == 2'b00)
        y = a;
    else if (sel == 2'b01)
        y = b;
    else if (sel == 2'b10)
        y = c;
    else
        y = d;
end
```

### Nested if-else:

```verilog
always @(posedge clk) begin
    if (reset) begin
        q <= 0;
    end else begin
        if (en) begin
            if (load)
                q <= d;
            else
                q <= q + 1;  // Increment
        end
    end
end
```

### if without else → Latch! ⚠️

```verilog
// ❌ DANGEROUS - Creates unwanted latch!
always @(*) begin
    if (sel)
        y = a;
    // Missing else - y retains value when sel=0
    // Creates latch in synthesis!
end

// ✅ CORRECT - Always provide else
always @(*) begin
    if (sel)
        y = a;
    else
        y = b;  // Or assign default value
end

// ✅ ALSO CORRECT - Default assignment
always @(*) begin
    y = b;  // Default
    if (sel)
        y = a;  // Override if sel
end
```

---

## ৬.৬ case Statements

### Basic case:

```verilog
always @(*) begin
    case(sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        2'b11: y = d;
    endcase
end
```

### case with default:

```verilog
always @(*) begin
    case(opcode)
        3'b000: result = a + b;
        3'b001: result = a - b;
        3'b010: result = a & b;
        3'b011: result = a | b;
        default: result = 8'b0;  // Important!
    endcase
end
```

### casex and casez (Don't care):

```verilog
// casex - X as don't care
always @(*) begin
    casex(instruction)
        4'b00xx: type = 2'b00;  // 0000, 0001, 0010, 0011
        4'b01xx: type = 2'b01;
        4'b10xx: type = 2'b10;
        4'b11xx: type = 2'b11;
    endcase
end

// casez - Z as don't care (more common)
always @(*) begin
    casez(instruction)
        4'b00??: type = 2'b00;  // Same as casex
        4'b01??: type = 2'b01;
        4'b10??: type = 2'b10;
        4'b11??: type = 2'b11;
    endcase
end
```

### case vs if-else:

```verilog
// case - Better for many options
case(sel)
    0: y = a;
    1: y = b;
    2: y = c;
    3: y = d;
endcase

// if-else - Better for priority/ranges
if (sel < 2)
    y = a;
else if (sel < 5)
    y = b;
else
    y = c;
```

---

## ৬.৭ for Loops

### Basic for loop:

```verilog
integer i;

always @(*) begin
    for (i = 0; i < 8; i = i + 1) begin
        result[i] = a[i] ^ b[i];
    end
end

// Unrolls to 8 XOR gates in hardware!
```

### Parameterized loop:

```verilog
module parity_checker #(
    parameter WIDTH = 8
)(
    input  [WIDTH-1:0] data,
    output reg         parity
);
    integer i;
    
    always @(*) begin
        parity = 0;
        for (i = 0; i < WIDTH; i = i + 1) begin
            parity = parity ^ data[i];
        end
    end
endmodule
```

### ⚠️ Important: Loops in Synthesis

```verilog
// ✅ SYNTHESIZABLE - Fixed iterations
for (i = 0; i < 8; i = i + 1) begin
    // Loop unrolls to 8 copies
end

// ❌ NOT SYNTHESIZABLE - Variable iterations
for (i = 0; i < count; i = i + 1) begin
    // count is variable - can't determine at compile time
end

// ❌ NOT SYNTHESIZABLE - while loops (usually)
while (condition) begin
    // Unknown number of iterations
end
```

---

## ৬.৮ Build Counters - In Verilog

### Simple 8-bit Up Counter:

```verilog
module counter_8bit(
    input            clk,
    input            reset,
    output reg [7:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 8'b0;
        else
            count <= count + 1;  // Increment
    end
endmodule
```

### Counter with Enable:

```verilog
module counter_enable(
    input            clk,
    input            reset,
    input            en,
    output reg [7:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 8'b0;
        else if (en)
            count <= count + 1;
    end
endmodule
```

### Up/Down Counter:

```verilog
module counter_updown(
    input            clk,
    input            reset,
    input            en,
    input            up_down,  // 1=up, 0=down
    output reg [7:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 8'b0;
        else if (en) begin
            if (up_down)
                count <= count + 1;  // Up
            else
                count <= count - 1;  // Down
        end
    end
endmodule
```

### BCD Counter (0-9):

```verilog
module counter_bcd(
    input            clk,
    input            reset,
    output reg [3:0] count
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b0;
        else begin
            if (count == 4'd9)
                count <= 4'd0;  // Wrap at 9
            else
                count <= count + 1;
        end
    end
endmodule
```

---

## ৬.৯ Build Shift Registers

### SISO - Serial In Serial Out:

```verilog
module shift_reg_siso(
    input      clk,
    input      reset,
    input      serial_in,
    output     serial_out
);
    reg [3:0] shift_reg;
    
    always @(posedge clk or posedge reset) begin
        if (reset)
            shift_reg <= 4'b0;
        else
            shift_reg <= {shift_reg[2:0], serial_in};
    end
    
    assign serial_out = shift_reg[3];
endmodule
```

### SIPO - Serial In Parallel Out:

```verilog
module shift_reg_sipo(
    input            clk,
    input            reset,
    input            serial_in,
    output reg [7:0] parallel_out
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            parallel_out <= 8'b0;
        else
            parallel_out <= {parallel_out[6:0], serial_in};
    end
endmodule
```

### PISO - Parallel In Serial Out:

```verilog
module shift_reg_piso(
    input      [7:0] parallel_in,
    input            clk,
    input            reset,
    input            load,      // Load parallel data
    input            shift_en,  // Enable shifting
    output           serial_out
);
    reg [7:0] shift_reg;
    
    always @(posedge clk or posedge reset) begin
        if (reset)
            shift_reg <= 8'b0;
        else if (load)
            shift_reg <= parallel_in;  // Load
        else if (shift_en)
            shift_reg <= {shift_reg[6:0], 1'b0};  // Shift
    end
    
    assign serial_out = shift_reg[7];
endmodule
```

---

## ৬.১০ Build Finite State Machines - In Code!

### FSM Structure:

```verilog
// Three always blocks (recommended):
// 1. State register (sequential)
// 2. Next state logic (combinational)
// 3. Output logic (combinational)

// Or two always blocks:
// 1. State register + next state (sequential)
// 2. Output logic (combinational)
```

### Example: Simple Traffic Light Controller

**States:**
```
RED    → 30 seconds
YELLOW → 5 seconds
GREEN  → 25 seconds
```

**Code:**

```verilog
module traffic_light(
    input      clk,
    input      reset,
    input      timer_done,
    output reg red,
    output reg yellow,
    output reg green
);
    // State encoding
    localparam RED    = 2'b00;
    localparam YELLOW = 2'b01;
    localparam GREEN  = 2'b10;
    
    reg [1:0] state, next_state;
    
    // State register (always @posedge)
    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= RED;
        else
            state <= next_state;
    end
    
    // Next state logic (always @*)
    always @(*) begin
        case(state)
            RED: begin
                if (timer_done)
                    next_state = GREEN;
                else
                    next_state = RED;
            end
            
            GREEN: begin
                if (timer_done)
                    next_state = YELLOW;
                else
                    next_state = GREEN;
            end
            
            YELLOW: begin
                if (timer_done)
                    next_state = RED;
                else
                    next_state = YELLOW;
            end
            
            default: next_state = RED;
        endcase
    end
    
    // Output logic (always @*)
    always @(*) begin
        // Default all off
        red = 0;
        yellow = 0;
        green = 0;
        
        case(state)
            RED:    red = 1;
            YELLOW: yellow = 1;
            GREEN:  green = 1;
            default: red = 1;
        endcase
    end
endmodule
```

### Example: Sequence Detector (101)

```verilog
module sequence_detector(
    input      clk,
    input      reset,
    input      in,
    output reg detected
);
    // States
    localparam S0 = 2'b00;  // Initial
    localparam S1 = 2'b01;  // Got 1
    localparam S2 = 2'b10;  // Got 10
    
    reg [1:0] state, next_state;
    
    // State register
    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0;
        else
            state <= next_state;
    end
    
    // Next state + output logic combined
    always @(*) begin
        // Defaults
        next_state = S0;
        detected = 0;
        
        case(state)
            S0: begin
                if (in)
                    next_state = S1;
                else
                    next_state = S0;
            end
            
            S1: begin
                if (in)
                    next_state = S1;  // Stay (multiple 1s)
                else
                    next_state = S2;  // Got "10"
            end
            
            S2: begin
                if (in) begin
                    detected = 1;     // Found "101"!
                    next_state = S1;  // Start looking for next
                end else begin
                    next_state = S0;
                end
            end
            
            default: next_state = S0;
        endcase
    end
endmodule
```

---

## ৬.১১ Common Mistakes & How to Fix

### Mistake 1: Mixing Blocking/Non-blocking ❌

```verilog
// ❌ WRONG
always @(posedge clk) begin
    a <= b;  // Non-blocking
    c = a;   // Blocking - DON'T MIX!
end

// ✅ CORRECT
always @(posedge clk) begin
    a <= b;
    c <= a;  // Both non-blocking
end
```

### Mistake 2: Using = in Sequential ❌

```verilog
// ❌ WRONG
always @(posedge clk) begin
    q = d;  // Blocking in sequential!
end

// ✅ CORRECT
always @(posedge clk) begin
    q <= d;  // Non-blocking
end
```

### Mistake 3: Missing else → Latch ❌

```verilog
// ❌ WRONG - Creates latch
always @(*) begin
    if (en)
        q = d;
    // Missing else!
end

// ✅ CORRECT
always @(*) begin
    if (en)
        q = d;
    else
        q = 0;  // Or some default
end
```

### Mistake 4: Multiple Drivers ❌

```verilog
// ❌ WRONG
always @(posedge clk) begin
    q <= a;
end

always @(posedge clk) begin
    q <= b;  // Multiple drivers!
end

// ✅ CORRECT - One always block
always @(posedge clk) begin
    if (sel)
        q <= a;
    else
        q <= b;
end
```

### Mistake 5: Incomplete case ❌

```verilog
// ❌ WRONG - Missing cases
always @(*) begin
    case(sel)
        2'b00: y = a;
        2'b01: y = b;
        // Missing 10 and 11!
    endcase
end

// ✅ CORRECT - Use default
always @(*) begin
    case(sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        default: y = d;
    endcase
end
```

---

## ৬.১২ Your 1-Week Build Plan

### Day 1: Flip-Flops
```
□ Write D FF (basic)
□ D FF with reset
□ D FF with enable
□ Test all versions
```

### Day 2: Registers
```
□ 8-bit register
□ Register with enable
□ Parameterized register
□ Test with different widths
```

### Day 3: if-else & case
```
□ MUX using if-else
□ MUX using case
□ Priority encoder
□ Understand latches!
```

### Day 4: Counters
```
□ Up counter
□ Down counter
□ Up/down counter
□ BCD counter
```

### Day 5: Shift Registers
```
□ SISO shift register
□ SIPO shift register
□ PISO shift register
□ Test serial data
```

### Day 6: FSMs
```
□ Traffic light FSM
□ Sequence detector FSM
□ Test state transitions
□ Waveform analysis
```

### Day 7: Review & Project
```
□ Review all mistakes
□ Blocking vs Non-blocking quiz
□ Complete final project
□ Test thoroughly
```

---

## ৬.১৩ Blocking vs Non-blocking - Final Quiz ⚠️

### Question 1:
```verilog
always @(posedge clk) begin
    a = 1;
    b = a;
end
// What is b? Answer: ___
```

### Question 2:
```verilog
always @(posedge clk) begin
    a <= 1;
    b <= a;
end
// What is b? Answer: ___
```

### Question 3:
```verilog
always @(posedge clk) begin
    x <= y;
    y <= x;
end
// What happens? Answer: ___
```

**Answers:**
```
Q1: b = 1 (blocking - b gets NEW a)
Q2: b = old_a (non-blocking - b gets OLD a)
Q3: Swap! x and y exchange values
```

---

## ৬.১৪ Chapter 6 Mission Complete!

### তুমি এখন পারো:

```
✅ Write sequential always blocks
✅ Use blocking vs non-blocking correctly
✅ Design flip-flops in Verilog
✅ Build registers with control
✅ Write counters (up/down/BCD)
✅ Design shift registers
✅ Create finite state machines
✅ Avoid common mistakes
✅ তোমার processor এর sequential parts code করা!
```

### তুমি বানিয়েছো:
```
✅ D Flip-Flops (multiple versions)
✅ 8-bit Registers
✅ Counters (4 types)
✅ Shift Registers (SISO, SIPO, PISO)
✅ FSMs (traffic light, sequence detector)
✅ All with testbenches! 🎉
```

### Stats:
```
Sequential modules: 15+
Lines of Verilog: 500+
State machines: 2
Critical concepts mastered: Blocking/Non-blocking! ✓
Level: Sequential Verilog Master! 🏆
```

### Next Level Unlocked:
```
→ Chapter 7: Testbenches & Simulation
   তুমি শিখবে: Advanced testing
   Waveforms, assertions, coverage!
   
   From coding → Professional testing!
```

---

## 🎯 Final Project - Before Next Chapter

### Project: Complete UART Transmitter

**Requirements:**
```
UART TX with:
✅ 8-bit parallel data input
✅ Start bit, data bits, stop bit
✅ Configurable baud rate
✅ FSM-based control
✅ Shift register for serialization
✅ Complete testbench

This uses:
- FSM (state control)
- Shift register (data)
- Counter (baud timing)
- All Chapter 6 concepts!
```

---

## 🏆 Achievement Unlocked!

```
Level 6: ✅ COMPLETE - Sequential Verilog Expert!
Progress: [██████████████████████████████] 30%

XP Gained: +3000
Skills: Sequential Logic, FSMs, Critical Concepts

Badges Earned:
🥉 Flip-Flop Coder
🥈 Register Builder
🥇 FSM Designer
🏅 Blocking/Non-blocking Master ⭐
🎖️ Counter Creator
🏆 Sequential Logic Expert

Next: Chapter 7 - Professional Testing!
      Waveforms, coverage, debugging! 📊
```

---

**[⬅️ Previous: Chapter 5](Chapter_05_Verilog_Basics.md)** | **[➡️ Next: Chapter 7](Chapter_07_Testbenches.md)**

---

<div align="center">

**"You mastered sequential Verilog. Next, you'll master testing!"**

**"তুমি sequential Verilog master করেছো। এবার testing master করবে!"**

Made with ❤️ for builders | বানানোর জন্য ভালোবাসা দিয়ে তৈরি

</div>
