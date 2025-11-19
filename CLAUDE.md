# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is **"Build Your Own Processor"** (তোমার নিজের প্রসেসর বানাও) - a comprehensive Bengali-language educational book that teaches processor design from digital logic fundamentals to actual silicon chip fabrication. The book guides learners through building a complete RISC-V processor using Verilog HDL, deploying it on FPGA hardware, and ultimately submitting it for real chip fabrication via TinyTapeout/Efabless.

**Language:** Primary content is in Bengali (বাংলা) with English technical terms
**Format:** Markdown documentation with embedded Verilog code examples
**Target Audience:** Bangladeshi students and enthusiasts learning processor design from scratch

## Structure & Architecture

### Part 1: Digital Foundations (Chapters 1-4)
- Covers digital logic, number systems, Boolean algebra
- Combinational and sequential circuits
- Uses CircuitVerse for browser-based simulations

### Part 2: Verilog HDL (Chapters 5-8)
- Introduction to hardware description language
- Always blocks, testbenches, advanced Verilog features
- Tools: Icarus Verilog, GTKWave for simulation

### Part 3: FPGA Development (Chapters 9-11)
- FPGA architecture and development workflow
- Real hardware projects (LED patterns, UART, SPI, digital clock)
- Target Hardware: Tang Nano 9K FPGA (~২,০০০ টাকা / $25)
- Tools: Gowin IDE for synthesis and deployment

### Part 4: Processor Design (Chapters 12-19) 🔥
- Computer architecture fundamentals
- Complete RISC-V RV32I instruction set (47 instructions)
- **Chapter 14**: Single-cycle CPU implementation (the core milestone)
- Multi-cycle CPU, 5-stage pipelining, hazard handling
- Memory hierarchy with cache design
- Complete SoC with UART, GPIO, Timer, Interrupts

### Part 5: VLSI & Chip Fabrication (Chapters 20-25)
- RTL to GDSII flow
- OpenLane physical design automation
- Sky130 PDK (Google's open-source 130nm process)
- TinyTapeout submission process
- Real chip fabrication and testing

## Key Design Patterns

### Verilog Code Organization
The book teaches Verilog through progressive examples:
- Modules follow standard port declaration style
- Combinational logic uses `assign` statements
- Sequential logic uses `always @(posedge clk)` blocks
- Testbenches follow a consistent structure with initial blocks and simulation timing

### Processor Design Approach
- **Single-cycle**: All instructions complete in one clock cycle (simple, educational)
- **Multi-cycle**: FSM-based control with resource sharing (more efficient)
- **Pipelined**: 5-stage pipeline (IF, ID, EX, MEM, WB) with hazard detection and forwarding

### Teaching Philosophy
- Hands-on projects at every stage
- "Quick Win" sections for immediate gratification (5-minute tasks)
- Progressive complexity: gates → circuits → Verilog → FPGA → processor → chip
- Bilingual approach: Bengali explanations with English technical terminology
- Budget-friendly: All software is free, minimal hardware costs

## Common Development Workflows

### Simulating Verilog Code
```bash
# Compile Verilog source
iverilog -o output_file source.v testbench.v

# Run simulation
vvp output_file

# View waveforms
gtkwave waveform.vcd
```

### FPGA Development
```bash
# Uses Gowin IDE (GUI-based)
# Synthesis → Place & Route → Generate Bitstream → Program FPGA
# Target: Tang Nano 9K with Gowin GW1NR-9 FPGA
```

### VLSI Design Flow
```bash
# Uses OpenLane for RTL to GDSII
# Synthesis → Floorplanning → Placement → Routing → Verification
# Target: Sky130 PDK (130nm open-source process)
```

## Important Conventions

### File Naming
- Chapters: `Chapter_XX_Topic_Name.md` (numbered 01-25)
- Verilog files referenced: lowercase with underscores (e.g., `and_gate.v`, `single_cycle_cpu.v`)

### Content Style
- Use conversational Bengali (তুমি not আপনি)
- Keep motivating and encouraging tone
- Technical terms in English, explanations in Bengali
- Extensive use of ASCII diagrams for circuit illustrations
- Code blocks include comments in Bengali
- Each chapter has clear time estimates and prerequisites

### Documentation Principles
- **DO NOT** create new markdown files unless explicitly required
- **DO NOT** add generic development practices or obvious instructions
- Focus on processor-specific architecture and RISC-V implementation details
- Include practical Bangladeshi context (local pricing, availability)

## Learning Progression

**Beginner Path:** 12-18 months total
- Month 1-2: Digital foundations
- Month 3-4: Verilog programming
- Month 5-6: FPGA projects
- Month 7-12: Processor design
- Month 13-14: VLSI design
- Month 15-24: Chip fabrication wait time

**Key Milestones:**
- 🥉 Digital Logic Master (Chapters 1-4)
- 🥈 Verilog Ninja (Chapters 5-8)
- 🥇 FPGA Wizard (Chapters 9-11)
- 🏅 CPU Architect (Chapter 14 - working RISC-V processor)
- 🎖️ VLSI Engineer (Chapters 21-23)
- 🏆 Chip Master (Chapter 24-25 - receive real chip!)

## Tools & Dependencies

### Free Software (All platforms)
- **CircuitVerse**: Browser-based digital circuit simulator (https://circuitverse.org)
- **Icarus Verilog**: Open-source Verilog simulator
- **GTKWave**: Waveform viewer for simulation results
- **Gowin IDE**: FPGA development environment for Tang Nano 9K
- **RISC-V Toolchain**: GCC cross-compiler for RISC-V
- **OpenLane**: Complete RTL-to-GDSII flow (Docker-based)
- **KLayout**: GDSII layout viewer

### Optional Hardware
- Tang Nano 9K FPGA board (~২,০০০ টাকা / $25)
- USB-C cable for programming
- Breadboard and wires for peripherals (optional)

### Installation Commands (Ubuntu/Linux)
```bash
# Verilog tools
sudo apt update
sudo apt install iverilog gtkwave

# Verify installation
iverilog -v
gtkwave --version
```

## Repository Status

**Version:** 1.0
**Last Updated:** November 2025
**Completion:** All 25 chapters complete ✅
**Total Content:** ~1,600 pages, 5,000+ lines of Verilog code
**License:** Creative Commons Attribution-ShareAlike 4.0 International

## Contributing Guidelines

From CONTRIBUTING.md:
- Fix typos, add examples, improve explanations, add practice problems
- Use simple conversational Bengali
- Follow Verilog best practices with Bengali comments
- Test all code before submitting
- **DO NOT** change book structure without discussion
- **DO NOT** remove existing content or add unrelated content

## Target Careers & Applications

This book prepares students for:
- Digital Design Engineer (৳50,000-150,000/month in Bangladesh)
- FPGA Engineer (৳60,000-180,000/month)
- ASIC Design Engineer (৳80,000-250,000/month)
- Computer Architect ($200K-400K+ internationally)
- Hardware startup founder

## Quick Reference

**Start here:** `chapters/Chapter_01_Digital_Logic_Introduction.md`
**Quick start guide:** `QUICK_START.md`
**Chapter index:** `CHAPTERS.md`
**Main goal:** Build a working RISC-V processor and submit for chip fabrication
**Success metric:** Student receives their own fabricated silicon chip within 12-24 months