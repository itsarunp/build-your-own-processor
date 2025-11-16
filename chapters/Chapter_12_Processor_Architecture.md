# 🖥️ Chapter 12: Build Your Own Processor - Architecture Basics
## From Circuits to CPU - Understanding How Processors Work!

> **"Gates were building blocks. Now build the COMPUTER. Time to create intelligence!"**
>
> **"Gates ছিল building blocks। এখন COMPUTER বানাও। Intelligence তৈরি করো!"**

---

## 🎯 এই Chapter এ তুমি শিখবে:

```
✅ What is a Processor? - CPU fundamentals
✅ Instruction Set Architecture (ISA) - the interface
✅ CPU Components - datapath & control
✅ Instruction Execution - fetch-decode-execute
✅ Simple CPU Design - 8-bit processor
✅ Register File - fast storage
✅ ALU Integration - arithmetic unit
✅ তোমার নিজের processor এর architecture! 🎉
```

**Time Required:** 1 week (5-6 hours/day)  
**Prerequisites:** Chapters 1-11 complete

---

## 🚀 Quick Understanding - 5 মিনিটে Processor Basics!

### What is a Processor?

```
Processor (CPU) = Central Processing Unit

Components:
1. Datapath - Does the work
   - Registers (storage)
   - ALU (arithmetic)
   - Multiplexers (routing)

2. Control Unit - Directs traffic
   - Decode instructions
   - Generate control signals
   - Sequence operations

3. Memory Interface
   - Fetch instructions
   - Load/store data

Like a factory:
- Datapath = Workers & machines
- Control = Manager & schedule
- Memory = Warehouse
```

### Instruction Execution Cycle:

```
1. FETCH: Get instruction from memory
   PC → Memory → IR

2. DECODE: Understand what to do
   IR → Control Unit → Signals

3. EXECUTE: Do the operation
   Signals → Datapath → Result

4. WRITEBACK: Save result
   Result → Register/Memory

Then repeat!
This is how ALL processors work!
```

🎉 **Now you understand processor basics!**

---

## ১২.১ Processor Architecture Overview

### Von Neumann Architecture:

```
Classic computer architecture (1945):

┌─────────────────────────────────────┐
│           CPU                       │
│  ┌──────────┐    ┌──────────┐     │
│  │ Control  │    │ Datapath │     │
│  │  Unit    │───▶│  (ALU +  │     │
│  │          │    │ Registers)│     │
│  └──────────┘    └──────────┘     │
│       │               │            │
└───────┼───────────────┼────────────┘
        │               │
        ▼               ▼
┌───────────────────────────────┐
│         Memory                │
│  (Instructions + Data)        │
└───────────────────────────────┘

Key ideas:
✅ Stored program (von Neumann)
✅ Same memory for code & data
✅ Sequential execution
✅ Fetch-decode-execute cycle
```

### Harvard Architecture:

```
Separate instruction & data memory:

        ┌─────────┐
        │   CPU   │
        └────┬────┘
        ┌────┴────┐
        ▼         ▼
   ┌─────────┐ ┌─────────┐
   │Instr.   │ │ Data    │
   │Memory   │ │ Memory  │
   └─────────┘ └─────────┘

Advantages:
✅ Fetch instruction & data simultaneously
✅ No conflict
✅ Higher bandwidth
✅ Used in DSPs, microcontrollers

We'll use Modified Harvard:
- Unified memory address space
- Separate caches
- Best of both worlds!
```

---

## ১২.২ Instruction Set Architecture (ISA)

### What is ISA?

```
ISA = Instruction Set Architecture
The contract between hardware & software

Defines:
✅ Instructions (what operations)
✅ Registers (how many, size)
✅ Data types (byte, word, etc.)
✅ Addressing modes (how to find data)
✅ Memory model (how memory works)

ISA is the INTERFACE:
Software ────ISA───▶ Hardware

Examples:
- x86/x64 (Intel, AMD)
- ARM (phones, tablets)
- RISC-V (open source) ← We'll use!
- MIPS (educational)
```

### RISC vs CISC:

```
┌─────────────┬──────────────┬─────────────┐
│ Feature     │    RISC      │    CISC     │
├─────────────┼──────────────┼─────────────┤
│ Philosophy  │ Simple inst. │ Complex inst│
│ Instructions│ Few, simple  │ Many, varied│
│ Cycles/inst │ 1 (ideal)    │ Multiple    │
│ Inst. size  │ Fixed        │ Variable    │
│ Registers   │ Many (32)    │ Few (8-16)  │
│ Addressing  │ Simple       │ Complex     │
│ Compiler    │ Complex      │ Simple      │
│ Examples    │ ARM, RISC-V  │ x86         │
└─────────────┴──────────────┴─────────────┘

RISC advantages:
✅ Simple hardware
✅ Easier pipelining
✅ Predictable timing
✅ Better for learning!

We'll build RISC processor!
```

### Our Simple ISA (Educational):

```
8-bit processor with:
- 8-bit data width
- 8-bit address (256 bytes memory)
- 4 registers (R0-R3)
- 8 instructions

Instruction format (8 bits):
┌────────┬───────┬───────┐
│ Opcode │ Reg A │ Reg B │
│ 4 bits │ 2 bits│ 2 bits│
└────────┴───────┴───────┘

Instructions:
0000 - NOP   (no operation)
0001 - LOAD  (load from memory)
0010 - STORE (store to memory)
0011 - ADD   (add registers)
0100 - SUB   (subtract)
0101 - AND   (bitwise and)
0110 - OR    (bitwise or)
0111 - JUMP  (unconditional jump)
1000 - BEQ   (branch if equal)

Simple but complete!
```

---

## ১২.৩ CPU Components

### 1. Program Counter (PC):

```verilog
// Program Counter - tracks current instruction
module program_counter(
    input wire clk,
    input wire reset,
    input wire [7:0] pc_next,  // Next PC value
    input wire pc_write,        // Enable write
    output reg [7:0] pc         // Current PC
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            pc <= 8'h00;  // Start at address 0
        else if (pc_write)
            pc <= pc_next;
    end
endmodule

// Normal operation: PC = PC + 1
// Branch/Jump: PC = target address
```

### 2. Instruction Register (IR):

```verilog
// Holds current instruction being executed
module instruction_register(
    input wire clk,
    input wire reset,
    input wire [7:0] instruction,
    input wire ir_write,
    output reg [7:0] ir
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            ir <= 8'h00;
        else if (ir_write)
            ir <= instruction;
    end
endmodule

// Decode fields:
wire [3:0] opcode = ir[7:4];
wire [1:0] reg_a  = ir[3:2];
wire [1:0] reg_b  = ir[1:0];
```

### 3. Register File:

```verilog
// Register File - fast storage
module register_file(
    input wire clk,
    input wire reset,
    // Read ports
    input wire [1:0] read_addr1,
    input wire [1:0] read_addr2,
    output wire [7:0] read_data1,
    output wire [7:0] read_data2,
    // Write port
    input wire [1:0] write_addr,
    input wire [7:0] write_data,
    input wire write_enable
);
    // 4 registers: R0, R1, R2, R3
    reg [7:0] registers [0:3];
    
    // Combinational read
    assign read_data1 = registers[read_addr1];
    assign read_data2 = registers[read_addr2];
    
    // Sequential write
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            registers[0] <= 8'h00;
            registers[1] <= 8'h00;
            registers[2] <= 8'h00;
            registers[3] <= 8'h00;
        end else if (write_enable) begin
            registers[write_addr] <= write_data;
        end
    end
endmodule
```

### 4. ALU (Arithmetic Logic Unit):

```verilog
module alu(
    input wire [7:0] a,
    input wire [7:0] b,
    input wire [2:0] alu_op,
    output reg [7:0] result,
    output wire zero,
    output wire negative
);
    // ALU operations
    localparam OP_ADD = 3'b000;
    localparam OP_SUB = 3'b001;
    localparam OP_AND = 3'b010;
    localparam OP_OR  = 3'b011;
    localparam OP_XOR = 3'b100;
    localparam OP_SLL = 3'b101;  // Shift left
    localparam OP_SRL = 3'b110;  // Shift right
    localparam OP_PASS= 3'b111;  // Pass through
    
    always @(*) begin
        case (alu_op)
            OP_ADD:  result = a + b;
            OP_SUB:  result = a - b;
            OP_AND:  result = a & b;
            OP_OR:   result = a | b;
            OP_XOR:  result = a ^ b;
            OP_SLL:  result = a << b[2:0];
            OP_SRL:  result = a >> b[2:0];
            OP_PASS: result = a;
            default: result = 8'h00;
        endcase
    end
    
    // Flags
    assign zero = (result == 8'h00);
    assign negative = result[7];
endmodule
```

### 5. Control Unit:

```verilog
module control_unit(
    input wire clk,
    input wire reset,
    input wire [3:0] opcode,
    input wire zero_flag,
    // Control signals
    output reg pc_write,
    output reg ir_write,
    output reg reg_write,
    output reg mem_read,
    output reg mem_write,
    output reg [2:0] alu_op,
    output reg [1:0] alu_src,
    output reg pc_src
);
    // State machine
    localparam FETCH   = 3'b000;
    localparam DECODE  = 3'b001;
    localparam EXECUTE = 3'b010;
    localparam MEMORY  = 3'b011;
    localparam WRITEBACK = 3'b100;
    
    reg [2:0] state;
    
    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= FETCH;
        else begin
            case (state)
                FETCH:     state <= DECODE;
                DECODE:    state <= EXECUTE;
                EXECUTE:   state <= MEMORY;
                MEMORY:    state <= WRITEBACK;
                WRITEBACK: state <= FETCH;
                default:   state <= FETCH;
            endcase
        end
    end
    
    // Control signal generation
    always @(*) begin
        // Default values
        pc_write = 0;
        ir_write = 0;
        reg_write = 0;
        mem_read = 0;
        mem_write = 0;
        alu_op = 3'b000;
        alu_src = 2'b00;
        pc_src = 0;
        
        case (state)
            FETCH: begin
                mem_read = 1;
                ir_write = 1;
            end
            
            DECODE: begin
                // Decode instruction
            end
            
            EXECUTE: begin
                case (opcode)
                    4'b0011: alu_op = 3'b000; // ADD
                    4'b0100: alu_op = 3'b001; // SUB
                    4'b0101: alu_op = 3'b010; // AND
                    4'b0110: alu_op = 3'b011; // OR
                    default: alu_op = 3'b000;
                endcase
            end
            
            WRITEBACK: begin
                reg_write = 1;
                pc_write = 1;
            end
        endcase
    end
endmodule
```

---

## ১২.৪ Instruction Execution - Detailed

### Fetch Stage:

```
Purpose: Get next instruction from memory

Steps:
1. PC → Memory Address
2. Read instruction from memory
3. Instruction → IR (Instruction Register)
4. PC ← PC + 1 (for next time)

Verilog pseudocode:
address = PC;
instruction = memory[address];
IR = instruction;
PC = PC + 1;

Timing:
- 1 clock cycle
- Memory read
- IR update
```

### Decode Stage:

```
Purpose: Understand the instruction

Steps:
1. Extract opcode from IR
2. Extract operands (registers)
3. Generate control signals
4. Read register values if needed

Example:
IR = 0011_01_10  (ADD R1, R2)
     ^^^^ ^^ ^^
     │    │  └─ Reg B (R2)
     │    └──── Reg A (R1)
     └───────── Opcode (ADD)

Control unit generates:
- ALU operation: ADD
- Register read: R1, R2
- Register write: R1
- Memory: no access
```

### Execute Stage:

```
Purpose: Perform the operation

Steps:
1. ALU performs calculation
2. Or memory address calculation
3. Or branch target calculation

Examples:

ADD R1, R2:
  ALU_result = R1 + R2

LOAD R1, [R2]:
  Address = R2

JUMP target:
  PC_next = target

Timing:
- 1 clock cycle
- ALU operation
- Result generated
```

### Memory Stage:

```
Purpose: Access memory if needed

Operations:
1. LOAD: Read from memory
   data = memory[address]

2. STORE: Write to memory
   memory[address] = data

3. Other instructions: Skip

Timing:
- 1 clock cycle (if memory access)
- 0 cycles (if no access)
```

### Writeback Stage:

```
Purpose: Save results

Steps:
1. Write ALU result to register
2. Or write memory data to register
3. Update PC

Example:
ADD R1, R2:
  R1 ← ALU_result
  PC ← PC + 1

LOAD R1, [R2]:
  R1 ← memory_data
  PC ← PC + 1

Timing:
- 1 clock cycle
- Register write
- PC update
```

---

## ১২.৫ Simple 8-bit Processor - Complete Design

### Top-Level Architecture:

```verilog
module simple_processor(
    input wire clk,
    input wire reset,
    // Memory interface
    output wire [7:0] mem_addr,
    input wire [7:0] mem_read_data,
    output wire [7:0] mem_write_data,
    output wire mem_read,
    output wire mem_write,
    // Debug outputs
    output wire [7:0] pc_out,
    output wire [7:0] ir_out
);
    // Internal signals
    wire [7:0] pc, pc_next;
    wire [7:0] ir;
    wire [7:0] alu_a, alu_b, alu_result;
    wire [7:0] reg_read1, reg_read2, reg_write_data;
    wire [1:0] reg_addr1, reg_addr2, reg_write_addr;
    wire [2:0] alu_op;
    wire [3:0] opcode;
    wire zero, negative;
    wire pc_write, ir_write, reg_write;
    
    // Program Counter
    program_counter pc_inst(
        .clk(clk),
        .reset(reset),
        .pc_next(pc_next),
        .pc_write(pc_write),
        .pc(pc)
    );
    
    // Instruction Register
    instruction_register ir_inst(
        .clk(clk),
        .reset(reset),
        .instruction(mem_read_data),
        .ir_write(ir_write),
        .ir(ir)
    );
    
    // Decode instruction
    assign opcode = ir[7:4];
    assign reg_addr1 = ir[3:2];
    assign reg_addr2 = ir[1:0];
    assign reg_write_addr = ir[3:2];
    
    // Register File
    register_file rf_inst(
        .clk(clk),
        .reset(reset),
        .read_addr1(reg_addr1),
        .read_addr2(reg_addr2),
        .read_data1(reg_read1),
        .read_data2(reg_read2),
        .write_addr(reg_write_addr),
        .write_data(reg_write_data),
        .write_enable(reg_write)
    );
    
    // ALU
    assign alu_a = reg_read1;
    assign alu_b = reg_read2;
    
    alu alu_inst(
        .a(alu_a),
        .b(alu_b),
        .alu_op(alu_op),
        .result(alu_result),
        .zero(zero),
        .negative(negative)
    );
    
    // Control Unit
    control_unit cu_inst(
        .clk(clk),
        .reset(reset),
        .opcode(opcode),
        .zero_flag(zero),
        .pc_write(pc_write),
        .ir_write(ir_write),
        .reg_write(reg_write),
        .mem_read(mem_read),
        .mem_write(mem_write),
        .alu_op(alu_op),
        .alu_src(),
        .pc_src()
    );
    
    // PC increment
    assign pc_next = pc + 1;
    
    // Memory interface
    assign mem_addr = pc;
    assign mem_write_data = reg_read2;
    assign reg_write_data = alu_result;
    
    // Debug
    assign pc_out = pc;
    assign ir_out = ir;
endmodule
```

---

## ১২.৬ Sample Programs

### Program 1: Add Two Numbers

```assembly
; Add R1 and R2, store in R1
; R1 = 5, R2 = 3
; Expected: R1 = 8

Address | Instruction | Description
--------|-------------|-------------
0x00    | 0011_01_10  | ADD R1, R2
0x01    | 0000_00_00  | NOP (halt)

Binary:
0x00: 00110110  (ADD R1, R2)
0x01: 00000000  (NOP)
```

### Program 2: Load and Store

```assembly
; Load value from memory, add, store back
Address | Instruction | Description
--------|-------------|-------------
0x00    | 0001_01_00  | LOAD R1, [R0]
0x01    | 0011_01_10  | ADD R1, R2
0x02    | 0010_01_00  | STORE R1, [R0]
0x03    | 0000_00_00  | NOP

; If memory[R0] = 10, R2 = 5
; Result: memory[R0] = 15
```

### Program 3: Simple Loop

```assembly
; Count from 0 to 10
Address | Instruction | Description
--------|-------------|-------------
0x00    | 0001_01_11  | LOAD R1, [R3]  ; Load counter
0x01    | 0011_01_10  | ADD R1, R2     ; R2 = 1 (increment)
0x02    | 0010_01_11  | STORE R1, [R3] ; Save counter
0x03    | 1000_01_00  | BEQ R1, R0     ; If R1=R0, exit
0x04    | 0111_00_00  | JUMP 0x00      ; Loop back
0x05    | 0000_00_00  | NOP            ; Done

; R0 = 10 (target)
; R2 = 1  (increment)
; R3 = address of counter
```

---

## ১২.৭ Processor Performance

### Clock Cycles Per Instruction (CPI):

```
Ideal RISC: CPI = 1
Our simple processor: CPI = 5

Stages:
1. Fetch     - 1 cycle
2. Decode    - 1 cycle
3. Execute   - 1 cycle
4. Memory    - 1 cycle (if needed)
5. Writeback - 1 cycle

Total: 5 cycles per instruction

Can we do better?
Yes! Pipelining! (Next chapters)
```

### Processor Speed:

```
Clock frequency: f Hz
CPI: cycles per instruction
Instructions per second (IPS):

IPS = f / CPI

Example:
f = 27 MHz (Tang Nano 9K)
CPI = 5
IPS = 27,000,000 / 5 = 5,400,000 IPS
    = 5.4 MIPS (Million Instructions Per Second)

Not bad for a simple processor!
```

---

## ১২.৮ Your 1-Week Build Plan

### Day 1: PC and IR
```
□ Implement Program Counter
□ Implement Instruction Register
□ Test with testbench
□ Understand control flow
```

### Day 2: Register File
```
□ Design register file
□ Multiple read/write ports
□ Test thoroughly
□ Debug issues
```

### Day 3: ALU
```
□ Complete ALU implementation
□ All operations
□ Flag generation
□ Test each operation
```

### Day 4: Control Unit
```
□ State machine design
□ Control signal generation
□ Instruction decoding
□ Test FSM
```

### Day 5: Integration
```
□ Connect all components
□ Top-level module
□ Wire everything
□ First synthesis
```

### Day 6: Memory Interface
```
□ Memory controller
□ Read/write timing
□ Integration
□ Test with simple program
```

### Day 7: Testing
```
□ Run sample programs
□ Debug issues
□ Waveform analysis
□ Documentation
```

---

## ১২.৯ Chapter 12 Mission Complete!

### তুমি এখন জানো:

```
✅ Processor architecture fundamentals
✅ ISA concepts (RISC vs CISC)
✅ CPU components (PC, IR, RF, ALU, CU)
✅ Instruction execution cycle
✅ Control unit design
✅ Simple processor design
✅ Assembly programming basics
✅ তোমার processor architecture! 🎉
```

### তুমি ডিজাইন করেছো:
```
✅ Program Counter
✅ Instruction Register
✅ Register File (4 registers)
✅ ALU (8 operations)
✅ Control Unit (FSM)
✅ Complete 8-bit processor!
✅ Sample programs! 💻
```

### Stats:
```
Components designed: 5
Instructions: 8
Register file: 4 registers
Data width: 8-bit
CPI: 5 cycles
Level: Processor Architect! 🏆
```

### Next Level Unlocked:
```
→ Chapter 13: RISC-V Basics
   তুমি শিখবে: Real ISA!
   Industry-standard architecture!
   
   From toy → Professional CPU!
```

---

## 🎯 Final Project

### Project: Enhanced 8-bit Processor

**Add features:**
```
✅ More instructions (16 total)
✅ More registers (8 total)
✅ Immediate values
✅ Conditional branches
✅ Stack operations
✅ Interrupt support (basic)

Test with:
- Fibonacci sequence
- Sorting algorithm
- Calculator program
```

---

## 🏆 Achievement Unlocked!

```
Level 12: ✅ COMPLETE - Processor Architect!
Progress: [██████████████████████████████] 60%

XP Gained: +5000 🎉
Skills: CPU Design, ISA, Architecture

Badges Earned:
🥉 Datapath Designer
🥈 Control Unit Expert
🥇 ISA Architect
🏅 Processor Builder
🎖️ Assembly Programmer
🏆 CPU Architect

PROCESSOR PART STARTED! 🖥️

Next: Chapter 13 - RISC-V Architecture!
      Real-world ISA! 🚀
```

---

**[⬅️ Previous: Chapter 11](Chapter_11_FPGA_Projects.md)** | **[➡️ Next: Chapter 13](Chapter_13_RISCV_Basics.md)**

---

<div align="center">

**"You designed your first processor. Now make it REAL with RISC-V!"**

**"তুমি প্রথম processor design করেছো। এবার RISC-V দিয়ে REAL বানাও!"**

Made with ❤️ for builders | বানানোর জন্য ভালোবাসা দিয়ে তৈরি

</div>
