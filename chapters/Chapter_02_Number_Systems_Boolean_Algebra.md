# 🔢 Chapter 2: Build Your Own Number System Tools
## Binary থেকে Hex - তোমার Processor এর ভাষা শেখো!

> **"Computers speak in Binary. You're about to become fluent!"**
>
> **"কম্পিউটার binary তে কথা বলে। তুমি এখন fluent হয়ে যাবে!"**

---

## 🎯 এই Chapter এ তুমি বানাবে:

```
✅ Binary ↔ Decimal converter
✅ Hex ↔ Binary converter
✅ Boolean expression simplifier
✅ K-Map solver
✅ তোমার processor এর number system! 🎉
```

**Time Required:** 1 week (3-4 hours/day)  
**Tools Needed:** Calculator, Paper, CircuitVerse

---

## 🚀 Quick Win - 5 মিনিটে Binary Master!

### এখনই করো:

**Challenge: Convert তোমার বয়স binary তে!**

```
Example: আমার বয়স 20

Step 1: 20 ÷ 2 = 10 remainder 0  (LSB)
Step 2: 10 ÷ 2 = 5  remainder 0
Step 3: 5  ÷ 2 = 2  remainder 1
Step 4: 2  ÷ 2 = 1  remainder 0
Step 5: 1  ÷ 2 = 0  remainder 1  (MSB)

Read bottom to top: 10100₂

Verify: 16+4 = 20 ✓
```

**এখন তোমার বয়স convert করো!** ✍️

Age in Binary: ________________

🎉 **Congratulations! তুমি binary তে count করতে পারো!**

---

## ২.১ Number Systems - তোমার Processor কোন ভাষায় কথা বলবে?

### কেন বিভিন্ন Number Systems?

তুমি যখন processor বানাবে, তখন বুঝবে:

```
Humans love:    Decimal (0-9)     - আমরা 10 আঙুল দিয়ে গুনি
Hardware uses:  Binary (0-1)      - Transistor: ON/OFF
Programmers:    Hex (0-F)         - Compact representation
```

### Number System Comparison

```
┌─────────┬──────┬────────────┬─────────┬──────────────┐
│ System  │ Base │   Digits   │ Example │   Use Case   │
├─────────┼──────┼────────────┼─────────┼──────────────┤
│ Binary  │  2   │    0,1     │  1011₂  │ Hardware     │
│ Octal   │  8   │   0-7      │   13₈   │ Permissions  │
│ Decimal │ 10   │   0-9      │   11₁₀  │ Humans       │
│ Hex     │ 16   │ 0-9, A-F   │   B₁₆   │ Memory addr  │
└─────────┴──────┴────────────┴─────────┴──────────────┘
```

---

## ২.২ Build Binary Converter

### Tool 1: Decimal → Binary

**Method: Divide by 2**

```
Convert: 45₁₀ = ?₂

45 ÷ 2 = 22  remainder 1  (LSB)
22 ÷ 2 = 11  remainder 0
11 ÷ 2 = 5   remainder 1
5  ÷ 2 = 2   remainder 1
2  ÷ 2 = 1   remainder 0
1  ÷ 2 = 0   remainder 1  (MSB)

Result: 101101₂

Verify: 32+8+4+1 = 45 ✓
```

### 🎯 Your Turn:

```
Convert these:
1. 25₁₀  = ________₂
2. 100₁₀ = ________₂
3. 255₁₀ = ________₂
```

---

### Tool 2: Binary → Decimal

**Method: Position × Weight**

```
Example: 11010110₂ = ?₁₀

Position: 7  6  5  4  3  2  1  0
Binary:   1  1  0  1  0  1  1  0
Weight: 128 64 32 16 8  4  2  1

= 128+64+16+4+2 = 214₁₀
```

### 🎯 Practice:

```
1. 1111₂     = ________₁₀
2. 10101₂    = ________₁₀
3. 11111111₂ = ________₁₀ (max byte!)
```

---

## ২.৩ Hexadecimal - Programmer এর Friend

### Hex Digits:

```
┌───┬──────┬─────┐  ┌───┬──────┬─────┐
│Dec│Binary│ Hex │  │Dec│Binary│ Hex │
├───┼──────┼─────┤  ├───┼──────┼─────┤
│ 0 │ 0000 │  0  │  │ 8 │ 1000 │  8  │
│ 1 │ 0001 │  1  │  │ 9 │ 1001 │  9  │
│ 2 │ 0010 │  2  │  │10 │ 1010 │  A  │
│ 3 │ 0011 │  3  │  │11 │ 1011 │  B  │
│ 4 │ 0100 │  4  │  │12 │ 1100 │  C  │
│ 5 │ 0101 │  5  │  │13 │ 1101 │  D  │
│ 6 │ 0110 │  6  │  │14 │ 1110 │  E  │
│ 7 │ 0111 │  7  │  │15 │ 1111 │  F  │
└───┴──────┴─────┘  └───┴──────┴─────┘
```

### Conversions:

**Binary → Hex:** Group by 4
```
11010110₂ = 1101 0110 = D6₁₆
```

**Hex → Binary:** Each digit = 4 bits
```
2F₁₆ = 0010 1111₂
```

---

## ২.৪ Boolean Algebra - Circuit Math!

### Basic Operations:

```
AND (·):  1·1=1, others=0
OR (+):   0+0=0, others=1  
NOT ('):  0'=1, 1'=0
```

### Important Laws:

```
Identity:     A·1=A,  A+0=A
Null:         A·0=0,  A+1=1
Idempotent:   A·A=A,  A+A=A
Complement:   A·A'=0, A+A'=1
```

### 🔥 De Morgan's Laws:

```
(A·B)' = A' + B'
(A+B)' = A'·B'
```

---

## ২.৫ Boolean Simplification

### Example:

```
Problem: A·B + A·B'

= A·(B+B')    [Factor]
= A·1         [Complement]
= A           [Identity]

Circuit: 4 gates → 0 gates!
```

### 🎯 Simplify:

```
1. A + A·B      = ________
2. (A+B)·(A+C)  = ________
3. A·B + A'·B   = ________
```

---

## ২.৬ K-Maps - Visual Simplification!

### 2-Variable K-Map:

```
        B
     0     1
   ┌─────┬─────┐
A 0│ m0  │ m1  │
   ├─────┼─────┤
  1│ m2  │ m3  │
   └─────┴─────┘
```

### 3-Variable K-Map:

```
         BC
       00  01  11  10
     ┌───┬───┬───┬───┐
A  0 │ 0 │ 1 │ 3 │ 2 │
     ├───┼───┼───┼───┤
   1 │ 4 │ 5 │ 7 │ 6 │
     └───┴───┴───┴───┘
```

### Example:

```
F(A,B,C) = Σm(0,2,4,6)

Fill K-Map:
         BC
       00  01  11  10
     ┌───┬───┬───┬───┐
A  0 │ 1 │ 0 │ 0 │ 1 │
     ├───┼───┼───┼───┤
   1 │ 1 │ 0 │ 0 │ 1 │
     └───┴───┴───┴───┘

Pattern: All have C=0
F = C'
```

---

## ২.৭ Your Build Challenge

### Day 1-2: Conversions
```
□ Master binary conversions
□ Learn hex
□ Create cheat sheet
```

### Day 3-4: Boolean Algebra
```
□ Memorize laws
□ Simplify 20 expressions
□ Build truth tables
```

### Day 5-7: K-Maps
```
□ Solve 2-variable K-Maps
□ Master 3-variable
□ Try 4-variable
□ Build real circuit!
```

---

## ২.৮ Practice Answers

### Conversions:
```
25₁₀  = 11001₂
100₁₀ = 1100100₂
255₁₀ = 11111111₂

1111₂ = 15₁₀
10101₂ = 21₁₀
```

### Boolean:
```
1. A + A·B = A
2. (A+B)·(A+C) = A+BC
3. A·B + A'·B = B
```

---

## 🏆 Chapter 2 Complete!

```
Level 2: ✅ Number System Master!
Progress: [██████████░░░░░] 10%

Skills Unlocked:
✅ Binary/Hex conversions
✅ Boolean algebra
✅ K-Maps
✅ Circuit simplification

Next: Chapter 3 - Build Your ALU!
```

---

**[⬅️ Chapter 1](Chapter_01_Digital_Logic_Introduction.md)** | **[➡️ Chapter 3](Chapter_03_Combinational_Circuits.md)**

---

<div align="center">

**"You now speak Binary. Time to build the processor!"**

Made with ❤️ for builders

</div>
