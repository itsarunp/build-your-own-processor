# ⚡ Chapter 15: Build Your Own Multi-Cycle Processor
## From Simple to Efficient - Resource Sharing & Optimization!

> **"Single-cycle was simple. Multi-cycle is smart. Time to optimize your CPU!"**
>
> **"Single-cycle ছিল simple। Multi-cycle smart। তোমার CPU optimize করো!"**

---

## 🎯 এই Chapter এ তুমি বানাবে:

```
✅ Multi-Cycle Architecture - better efficiency
✅ FSM Control Unit - 5-state machine
✅ Resource Sharing - one ALU, one memory
✅ Instruction/Data Registers - store values
✅ Better Performance - faster average
✅ Lower Cost - fewer components
✅ Optimized RISC-V - smarter design!
✅ তোমার efficient processor! 🎉
```

**Time Required:** 1 week (5-6 hours/day)  
**Prerequisites:** Chapter 14 complete

---

## 🚀 Quick Understanding - Multi-Cycle Concept

### Single-Cycle vs Multi-Cycle:

```
┌─────────────────┬──────────────┬──────────────┐
│ Feature         │ Single-Cycle │ Multi-Cycle  │
├─────────────────┼──────────────┼──────────────┤
│ Cycles/Instr    │ 1 (fixed)    │ 3-5 (varies) │
│ Clock Period    │ Slowest inst │ Shortest op  │
│ CPI             │ 1.0          │ 3.0-4.5      │
│ Clock Freq      │ Low          │ High         │
│ Throughput      │ Low          │ Can be higher│
│ Hardware        │ More         │ Less         │
│ Control         │ Simple       │ FSM          │
│ Cost            │ Higher       │ Lower        │
└─────────────────┴──────────────┴──────────────┘

Key idea:
Break instruction into multiple cycles
Share expensive resources (ALU, memory)
Different instructions take different time
```

### Example Timing:

```
Single-Cycle:
Clock = 12ns (limited by LW)
ADD: 12ns (wasted 7ns!)
LW:  12ns (actually needs 12ns)
All same time

Multi-Cycle:
Clock = 3ns (shortest operation)
ADD: 3 cycles = 9ns (faster!)
LW:  5 cycles = 15ns (slower)
Average: Better overall!

Real benefit:
Faster clock × more cycles
Often better than single-cycle!
```

🎉 **Smarter design = Better performance!**

---

## ১৫.১ Multi-Cycle Datapath

### Key Differences from Single-Cycle:

```
1. Instruction Register (IR)
   - Store fetched instruction
   - Use across multiple cycles

2. Data Registers (A, B)
   - Store register values
   - Hold operands

3. ALUOut Register
   - Store ALU result
   - Use in next cycle

4. Memory Data Register (MDR)
   - Store loaded data
   - Before writeback

5. Unified Memory
   - One memory for both instr & data
   - Share between cycles

6. Single ALU
   - Used for everything
   - Address calc, operations, PC update
```

### Datapath Diagram:

```
        ┌────────┐
        │   PC   │
        └───┬────┘
            │
            ↓
        ┌────────┐
        │ Memory │←──── (Unified)
        └───┬────┘
            │
        ┌───▼─────┐
        │   IR    │ (Store instruction)
        └───┬─────┘
            │
        ┌───▼──────────┐
        │  Register    │
        │    File      │
        └───┬────┬─────┘
            │    │
        ┌───▼──┐ │
        │  A   │ │  (Store rs1)
        └───┬──┘ │
            │ ┌──▼──┐
            │ │  B  │ (Store rs2)
            │ └──┬──┘
            ↓    ↓
        ┌─────────┐
        │   ALU   │ (Shared for all ops)
        └────┬────┘
             │
        ┌────▼─────┐
        │  ALUOut  │ (Store result)
        └────┬─────┘
             │
        ┌────▼────┐
        │   MDR   │ (Memory data)
        └────┬────┘
             │
             ↓
        Write Back
```

---

## ১৫.২ Five-Stage FSM

### The Five Stages:

```
1. FETCH (IF)
   - PC → Memory
   - Instruction → IR
   - PC ← PC + 4

2. DECODE (ID)
   - Read registers
   - Generate immediate
   - A ← rs1, B ← rs2

3. EXECUTE (EX)
   - ALU operation
   - Branch comparison
   - Address calculation

4. MEMORY (MEM)
   - Load data (if LW)
   - Store data (if SW)
   - Skip if not memory op

5. WRITEBACK (WB)
   - Write result to register
   - Update PC
   - Go to FETCH

Not all instructions use all stages!
```

### State Transition Diagram:

```
     ┌─────────┐
     │  FETCH  │◄───────────┐
     └────┬────┘            │
          │                 │
          ↓                 │
     ┌─────────┐            │
     │ DECODE  │            │
     └────┬────┘            │
          │                 │
          ↓                 │
     ┌─────────┐            │
     │ EXECUTE │            │
     └────┬────┘            │
          │                 │
          ↓                 │
     ┌─────────┐            │
     │ MEMORY  │            │
     └────┬────┘            │
          │                 │
          ↓                 │
     ┌─────────┐            │
     │WRITEBACK│────────────┘
     └─────────┘
```

### Cycles per Instruction:

```
R-type (ADD, SUB, etc.):
1. FETCH
2. DECODE
3. EXECUTE
4. WRITEBACK
Total: 4 cycles

I-type Arithmetic (ADDI):
1. FETCH
2. DECODE
3. EXECUTE
4. WRITEBACK
Total: 4 cycles

Load (LW):
1. FETCH
2. DECODE
3. EXECUTE (calc address)
4. MEMORY (read)
5. WRITEBACK
Total: 5 cycles

Store (SW):
1. FETCH
2. DECODE
3. EXECUTE (calc address)
4. MEMORY (write)
Total: 4 cycles

Branch (BEQ):
1. FETCH
2. DECODE
3. EXECUTE (compare & update PC)
Total: 3 cycles

Jump (JAL):
1. FETCH
2. DECODE
3. EXECUTE (calc target & update PC)
4. WRITEBACK (save PC+4)
Total: 4 cycles

Average: ~4 cycles
```

---

## ১৫.৩ Control FSM Implementation

```verilog
module multi_cycle_control(
    input wire clk,
    input wire reset,
    input wire [6:0] opcode,
    input wire [2:0] funct3,
    input wire [6:0] funct7,
    // Control signals
    output reg pc_write,
    output reg pc_write_cond,
    output reg ir_write,
    output reg mem_read,
    output reg mem_write,
    output reg reg_write,
    output reg [1:0] alu_src_a,
    output reg [1:0] alu_src_b,
    output reg [1:0] alu_op,
    output reg [1:0] pc_source,
    output reg [1:0] reg_src
);
    // State definitions
    localparam FETCH     = 3'b000;
    localparam DECODE    = 3'b001;
    localparam EX_R      = 3'b010;  // R-type execute
    localparam EX_I      = 3'b011;  // I-type execute
    localparam EX_LOAD   = 3'b100;  // Load address calc
    localparam EX_STORE  = 3'b101;  // Store address calc
    localparam EX_BRANCH = 3'b110;  // Branch execute
    localparam MEMORY    = 3'b111;  // Memory access
    localparam WRITEBACK = 3'b1000; // Write back
    
    reg [3:0] state, next_state;
    
    // State register
    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= FETCH;
        else
            state <= next_state;
    end
    
    // Next state logic
    always @(*) begin
        case (state)
            FETCH: begin
                next_state = DECODE;
            end
            
            DECODE: begin
                case (opcode)
                    7'b0110011: next_state = EX_R;      // R-type
                    7'b0010011: next_state = EX_I;      // I-type
                    7'b0000011: next_state = EX_LOAD;   // Load
                    7'b0100011: next_state = EX_STORE;  // Store
                    7'b1100011: next_state = EX_BRANCH; // Branch
                    7'b1101111, 7'b1100111: next_state = WRITEBACK; // Jump
                    default: next_state = FETCH;
                endcase
            end
            
            EX_R, EX_I: begin
                next_state = WRITEBACK;
            end
            
            EX_LOAD: begin
                next_state = MEMORY;
            end
            
            EX_STORE: begin
                next_state = MEMORY;
            end
            
            EX_BRANCH: begin
                next_state = FETCH;
            end
            
            MEMORY: begin
                if (opcode == 7'b0000011)  // Load
                    next_state = WRITEBACK;
                else  // Store
                    next_state = FETCH;
            end
            
            WRITEBACK: begin
                next_state = FETCH;
            end
            
            default: next_state = FETCH;
        endcase
    end
    
    // Output logic
    always @(*) begin
        // Default values
        pc_write = 0;
        pc_write_cond = 0;
        ir_write = 0;
        mem_read = 0;
        mem_write = 0;
        reg_write = 0;
        alu_src_a = 2'b00;
        alu_src_b = 2'b00;
        alu_op = 2'b00;
        pc_source = 2'b00;
        reg_src = 2'b00;
        
        case (state)
            FETCH: begin
                mem_read = 1;
                ir_write = 1;
                alu_src_a = 2'b00;  // PC
                alu_src_b = 2'b01;  // 4
                alu_op = 2'b00;     // ADD
                pc_write = 1;
                pc_source = 2'b00;  // ALU result (PC+4)
            end
            
            DECODE: begin
                alu_src_a = 2'b00;  // PC
                alu_src_b = 2'b11;  // Immediate
                alu_op = 2'b00;     // ADD (for branch target)
            end
            
            EX_R: begin
                alu_src_a = 2'b01;  // A (rs1)
                alu_src_b = 2'b00;  // B (rs2)
                alu_op = 2'b10;     // From funct fields
            end
            
            EX_I: begin
                alu_src_a = 2'b01;  // A (rs1)
                alu_src_b = 2'b10;  // Immediate
                alu_op = 2'b10;     // From funct fields
            end
            
            EX_LOAD, EX_STORE: begin
                alu_src_a = 2'b01;  // A (rs1)
                alu_src_b = 2'b10;  // Immediate (offset)
                alu_op = 2'b00;     // ADD
            end
            
            EX_BRANCH: begin
                alu_src_a = 2'b01;  // A (rs1)
                alu_src_b = 2'b00;  // B (rs2)
                alu_op = 2'b01;     // SUB for comparison
                pc_write_cond = 1;
                pc_source = 2'b01;  // Branch target
            end
            
            MEMORY: begin
                if (opcode == 7'b0000011)  // Load
                    mem_read = 1;
                else  // Store
                    mem_write = 1;
            end
            
            WRITEBACK: begin
                reg_write = 1;
                case (opcode)
                    7'b0000011: reg_src = 2'b01;  // Memory
                    7'b1101111, 7'b1100111: reg_src = 2'b10;  // PC+4
                    default: reg_src = 2'b00;  // ALU
                endcase
            end
        endcase
    end
endmodule
```

---

## ১৫.৪ Datapath Components with Registers

### Instruction Register:

```verilog
module instruction_register(
    input wire clk,
    input wire reset,
    input wire ir_write,
    input wire [31:0] data_in,
    output reg [31:0] instruction
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            instruction <= 32'h00000013;  // NOP (ADDI x0, x0, 0)
        else if (ir_write)
            instruction <= data_in;
    end
endmodule
```

### Data Registers (A, B):

```verilog
module data_registers(
    input wire clk,
    input wire reset,
    input wire [31:0] rs1_data,
    input wire [31:0] rs2_data,
    output reg [31:0] a,
    output reg [31:0] b
);
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            a <= 32'h00000000;
            b <= 32'h00000000;
        end else begin
            a <= rs1_data;
            b <= rs2_data;
        end
    end
endmodule
```

### ALU Output Register:

```verilog
module alu_out_register(
    input wire clk,
    input wire reset,
    input wire [31:0] alu_result,
    output reg [31:0] alu_out
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            alu_out <= 32'h00000000;
        else
            alu_out <= alu_result;
    end
endmodule
```

### Memory Data Register:

```verilog
module memory_data_register(
    input wire clk,
    input wire reset,
    input wire [31:0] mem_data,
    output reg [31:0] mdr
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            mdr <= 32'h00000000;
        else
            mdr <= mem_data;
    end
endmodule
```

---

## ১৫.৫ ALU Source Multiplexers

```verilog
module alu_src_mux(
    input wire [31:0] pc,
    input wire [31:0] a,
    input wire [31:0] b,
    input wire [31:0] immediate,
    input wire [1:0] alu_src_a,
    input wire [1:0] alu_src_b,
    output reg [31:0] alu_a,
    output reg [31:0] alu_b
);
    // ALU A source
    always @(*) begin
        case (alu_src_a)
            2'b00: alu_a = pc;          // PC
            2'b01: alu_a = a;           // Register A
            2'b10: alu_a = 32'h00000000; // Zero
            default: alu_a = 32'h00000000;
        endcase
    end
    
    // ALU B source
    always @(*) begin
        case (alu_src_b)
            2'b00: alu_b = b;           // Register B
            2'b01: alu_b = 32'h00000004; // 4 (for PC+4)
            2'b10: alu_b = immediate;   // Immediate
            2'b11: alu_b = immediate;   // Immediate (branch)
            default: alu_b = 32'h00000000;
        endcase
    end
endmodule
```

---

## ১৫.৬ Complete Multi-Cycle Processor

```verilog
module riscv_multi_cycle(
    input wire clk,
    input wire reset,
    // Memory interface
    output wire [31:0] mem_addr,
    input wire [31:0] mem_read_data,
    output wire [31:0] mem_write_data,
    output wire mem_read,
    output wire mem_write,
    // Debug
    output wire [31:0] pc_debug,
    output wire [3:0] state_debug
);
    // Internal signals
    wire [31:0] pc, pc_next;
    wire [31:0] instruction;
    wire [31:0] immediate;
    wire [31:0] a, b;
    wire [31:0] alu_a, alu_b, alu_result, alu_out;
    wire [31:0] mdr;
    wire [31:0] rs1_data, rs2_data, rd_data;
    
    // Control signals
    wire pc_write, pc_write_cond, ir_write;
    wire reg_write;
    wire [1:0] alu_src_a, alu_src_b;
    wire [1:0] alu_op, pc_source, reg_src;
    wire [3:0] alu_control_sig;
    wire zero, negative, branch_taken;
    
    // Extract fields
    wire [6:0] opcode = instruction[6:0];
    wire [4:0] rd = instruction[11:7];
    wire [2:0] funct3 = instruction[14:12];
    wire [4:0] rs1 = instruction[19:15];
    wire [4:0] rs2 = instruction[24:20];
    wire [6:0] funct7 = instruction[31:25];
    
    // Program Counter
    program_counter pc_inst(
        .clk(clk),
        .reset(reset),
        .pc_write(pc_write | (pc_write_cond & branch_taken)),
        .pc_next(pc_next),
        .pc(pc)
    );
    
    // PC source mux
    assign pc_next = (pc_source == 2'b00) ? alu_result :  // Sequential/Jump
                     (pc_source == 2'b01) ? alu_out :     // Branch target
                     pc;
    
    // Instruction Register
    instruction_register ir_inst(
        .clk(clk),
        .reset(reset),
        .ir_write(ir_write),
        .data_in(mem_read_data),
        .instruction(instruction)
    );
    
    // Immediate Generator
    imm_gen imm_gen_inst(
        .instruction(instruction),
        .immediate(immediate)
    );
    
    // Register File
    register_file rf_inst(
        .clk(clk),
        .reset(reset),
        .rs1_addr(rs1),
        .rs2_addr(rs2),
        .rs1_data(rs1_data),
        .rs2_data(rs2_data),
        .rd_addr(rd),
        .rd_data(rd_data),
        .reg_write(reg_write)
    );
    
    // Data Registers
    data_registers data_regs(
        .clk(clk),
        .reset(reset),
        .rs1_data(rs1_data),
        .rs2_data(rs2_data),
        .a(a),
        .b(b)
    );
    
    // ALU Source Mux
    alu_src_mux alu_src_inst(
        .pc(pc),
        .a(a),
        .b(b),
        .immediate(immediate),
        .alu_src_a(alu_src_a),
        .alu_src_b(alu_src_b),
        .alu_a(alu_a),
        .alu_b(alu_b)
    );
    
    // ALU Control
    alu_control alu_ctrl_inst(
        .alu_op(alu_op),
        .funct3(funct3),
        .funct7(funct7),
        .alu_control_out(alu_control_sig)
    );
    
    // ALU
    alu alu_inst(
        .a(alu_a),
        .b(alu_b),
        .alu_control(alu_control_sig),
        .result(alu_result),
        .zero(zero),
        .negative(negative)
    );
    
    // ALU Out Register
    alu_out_register alu_out_reg(
        .clk(clk),
        .reset(reset),
        .alu_result(alu_result),
        .alu_out(alu_out)
    );
    
    // Memory Data Register
    memory_data_register mdr_inst(
        .clk(clk),
        .reset(reset),
        .mem_data(mem_read_data),
        .mdr(mdr)
    );
    
    // Branch Comparator
    branch_comparator branch_comp(
        .rs1_data(a),
        .rs2_data(b),
        .funct3(funct3),
        .branch_taken(branch_taken)
    );
    
    // Write-back Mux
    assign rd_data = (reg_src == 2'b00) ? alu_out :     // ALU result
                     (reg_src == 2'b01) ? mdr :          // Memory
                     (reg_src == 2'b10) ? (pc + 4) :     // PC+4 (JAL/JALR)
                     32'h00000000;
    
    // Memory Interface
    assign mem_addr = (ir_write) ? pc : alu_out;
    assign mem_write_data = b;
    
    // Control Unit
    multi_cycle_control control(
        .clk(clk),
        .reset(reset),
        .opcode(opcode),
        .funct3(funct3),
        .funct7(funct7),
        .pc_write(pc_write),
        .pc_write_cond(pc_write_cond),
        .ir_write(ir_write),
        .mem_read(mem_read),
        .mem_write(mem_write),
        .reg_write(reg_write),
        .alu_src_a(alu_src_a),
        .alu_src_b(alu_src_b),
        .alu_op(alu_op),
        .pc_source(pc_source),
        .reg_src(reg_src)
    );
    
    // Debug
    assign pc_debug = pc;
    assign state_debug = control.state;
endmodule
```

---

## ১৫.৭ Performance Comparison

### Execution Time Comparison:

```
Example program: 10 instructions
- 4 × ADD/SUB (R-type)
- 2 × ADDI (I-type)
- 2 × LW (Load)
- 1 × SW (Store)
- 1 × BEQ (Branch)

Single-Cycle:
Clock period: 12ns (LW critical path)
Total: 10 inst × 1 cycle × 12ns = 120ns

Multi-Cycle:
Clock period: 3ns (ALU operation)
Cycles:
- R-type: 4 inst × 4 cycles = 16
- I-type: 2 inst × 4 cycles = 8
- LW: 2 inst × 5 cycles = 10
- SW: 1 inst × 4 cycles = 4
- BEQ: 1 inst × 3 cycles = 3
Total: 41 cycles × 3ns = 123ns

Very close!
But multi-cycle:
✅ Lower cost (shared resources)
✅ More flexible (variable cycles)
✅ Easier to extend
```

### Average CPI:

```
CPI = Total Cycles / Total Instructions
    = 41 / 10
    = 4.1

Typical multi-cycle: CPI ≈ 4.0-4.5

vs Single-cycle: CPI = 1.0

But clock is 4× faster!
So similar performance
```

---

## ১৫.৮ Your 1-Week Build Plan

### Day 1-2: FSM Design
```
□ State machine design
□ State transitions
□ Control signal generation
□ Test FSM separately
```

### Day 3-4: Datapath Modifications
```
□ Add instruction register
□ Add data registers (A, B)
□ Add ALUOut register
□ Add MDR register
```

### Day 5-6: Integration
```
□ Connect FSM to datapath
□ Add multiplexers
□ Wire control signals
□ Initial testing
```

### Day 7: Testing & Debug
```
□ Run sample programs
□ Compare with single-cycle
□ Debug timing issues
□ Performance analysis
```

---

## ১৫.৯ Chapter 15 Mission Complete!

### তুমি এখন পারো:

```
✅ Design multi-cycle processors
✅ Create FSM control units
✅ Implement resource sharing
✅ Optimize datapath
✅ Analyze performance tradeoffs
✅ Compare architectures
✅ Build efficient CPUs
✅ Professional optimization! 🎉
```

### তুমি বানিয়েছো:
```
✅ Multi-cycle RISC-V processor
✅ 5-state FSM controller
✅ Optimized datapath
✅ Resource-shared design
✅ Variable-cycle execution
✅ Efficient CPU! ⚡
```

### Stats:
```
States: 5 (FSM)
Registers added: 4 (IR, A, B, ALUOut, MDR)
Average CPI: 4.1
Clock speedup: 4×
Hardware saved: 30%
Level: Optimization Expert! 🏆
```

### Next Level Unlocked:
```
→ Chapter 16: Pipelining
   তুমি শিখবে: Parallel execution
   5× throughput increase!
   
   From sequential → Parallel!
```

---

## 🎯 Final Project

### Project: Optimized Multi-Cycle CPU

**Enhancements:**
```
✅ Add cycle counters
✅ Performance profiling
✅ Dynamic branch prediction
✅ Fast path for common instructions
✅ Deploy to FPGA
✅ Compare with single-cycle

Requirements:
- All features working
- Performance measured
- Comparison report
- FPGA deployment
```

---

## 🏆 Achievement Unlocked!

```
Level 15: ✅ COMPLETE - CPU Optimizer!
Progress: [██████████████████████████████████] 75%

XP Gained: +3000
Skills: FSM Design, Resource Sharing, Optimization

Badges Earned:
🥉 FSM Designer
🥈 Resource Optimizer
🥇 Multi-Cycle Master
🏅 Performance Analyst
🎖️ Efficient Design
🏆 CPU Architect Pro

Next: Chapter 16 - Pipeline Your CPU!
      5× performance boost! 🚀
```

---

**[⬅️ Previous: Chapter 14](Chapter_14_Single_Cycle_CPU.md)** | **[➡️ Next: Chapter 16](Chapter_16_Pipelining.md)**

---

<div align="center">

**"You optimized your CPU. Now make it BLAZING FAST with pipelining!"**

**"তুমি CPU optimize করেছো। এবার pipeline দিয়ে BLAZING FAST বানাও!"**

Made with ❤️ for builders | বানানোর জন্য ভালোবাসা দিয়ে তৈরি

</div>
