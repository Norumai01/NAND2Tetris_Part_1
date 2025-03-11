# Boolean Functions and Gate Logic

This repository contains implementations of fundamental digital logic components using a Hardware Description Language (HDL). The project follows the Nand to Tetris course methodology where complex systems are built from elementary logic gates.

## Table of Contents
- [Boolean Logic Foundations](#boolean-logic-foundations)
- [Logic Gates](#logic-gates)
- [Hardware Description Language](#hardware-description-language)
- [Multi-Bit Components](#multi-bit-components)
- [Basic Gates Implementation](#basic-gates-implementation)
- [Complex Gates Implementation](#complex-gates-implementation)
- [Hardware Simulation](#hardware-simulation)

## Boolean Logic Foundations

### Boolean Identities
- **Commutative Laws**
  - (x AND y) = (y AND x)
  - (x OR y) = (y OR x)
- **Associative Laws**
  - (x AND (y AND z)) = ((x AND y) AND z)
  - (x OR (y OR z)) = ((x OR y) OR z)
- **Distributive Laws**
  - (x AND (y OR z)) = ((x AND y) OR (x AND z))
  - (x OR (y AND z)) = ((x OR y) AND (x OR z))
- **De Morgan Laws**
  - NOT(x AND y) = Not(x) OR Not(y)
  - NOT(x OR y) = Not(x) AND Not(y)

### Boolean Function Synthesis

NAND Gate is a universal gate that can be used to implement any Boolean function.

| X | Y | NAND |
|---|---|------|
| 0 | 0 | 1    |
| 0 | 1 | 1    |
| 1 | 0 | 1    |
| 1 | 1 | 0    |

**Theorem**: (X AND Y) = NOT(X NAND Y)

**Important Observation**: NAND(X, X) = NOT(X)

### Finding Boolean Expressions from Truth Tables

To derive a Boolean expression from a truth table:
1. Identify all rows where the output is 1
2. Write an expression for each such row (using AND for all inputs, with NOT for 0 values)
3. Combine these expressions with OR operations
4. Simplify the resulting expression using Boolean algebra

## Logic Gates

### Elementary Gates
- **NAND**: Universal gate, can be used to build any other gate
- **AND**: Output is 1 only when all inputs are 1
- **OR**: Output is 1 when at least one input is 1
- **NOT**: Inverts the input

### Composite Gates
Gates built from combinations of elementary gates:
- Multiplexers (MUX)
- Demultiplexers (DMUX)
- Adders
- And more complex circuits

## Hardware Description Language

HDL is used to describe the implementation of gates and circuits.

### XOR Gate Example

XOR (Exclusive OR) outputs 1 if exactly one input is 1.
- Boolean Expression: (A AND NOT(B)) OR (NOT(A) AND B)

### Hardware Assembly Process

1. Start with requirements (e.g., truth table)
2. Design gate interface 
3. Implement using HDL
4. Test and verify

## Multi-Bit Components

Multi-bit components handle calculations for multiple bits simultaneously.

Examples:
- 16-bit Adders
- 16-bit Multiplexers

### Accessing Sub-buses
Multi-bit buses can be accessed in various ways:
- Whole bus: x[16]
- Single bit: x[3]
- Multiple consecutive bits: x[4..7]

## Basic Gates Implementation

### NOT Gate
```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
    Nand(a=in, b=in, out=out)
}
```

| In  | Out |
| --- | --- |
| 0   | 1   |
| 1   | 0   |

**Implementation Hint:** When you input the same value X into both inputs of a NAND gate, it outputs NOT(X). This is because NAND(X,X) = NOT(X AND X) = NOT(X).

### AND Gate
```
CHIP And {
    IN a, b;
    OUT out;

    PARTS:
    Nand(a=a, b=b, out=w1);
    Not(in=w1, out=out);
}
```

| a   | b   | Out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 0   |
| 1   | 0   | 0   |
| 1   | 1   | 1   |

**Implementation Hint:** NAND gate is AND but with the opposite output, so you can reverse engineer it by applying the NOT Gate to get AND gate outputs.

### OR Gate
```
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
    Not(in=a, out=nota);
    Not(in=b, out=notb);
    Nand(a=nota, b=notb, out=out);
}
```

| a   | b   | Out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 1   |
| 1   | 0   | 1   |
| 1   | 1   | 1   |

**Implementation Hint:** Look at De Morgan's Law identities and connect NOT(X OR Y) to making OR Gate, which equals NAND(NOT(X), NOT(Y)).

### XOR Gate
```
CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
    Not(in=a, out=nota);
    Not(in=b, out=notb);
    And(a=a, b=notb, out=w1);
    And(a=nota, b=b, out=w2);
    Or(a=w1, b=w2, out=out);
}
```

| a   | b   | Out |
| --- | --- | --- |
| 0   | 0   | 0   |
| 0   | 1   | 1   |
| 1   | 0   | 1   |
| 1   | 1   | 0   |

**Implementation Hint:** Look at the rows with output 1 and express them as: (NOT(A) AND B) OR (A AND NOT(B)). XOR outputs 1 only when inputs differ.

## Complex Gates Implementation

### Multiplexer (MUX)
Selects between two inputs based on a selection bit.

```
CHIP Mux {
    IN a, b, sel;
    OUT out;

    PARTS:
    Not(in=sel, out=notSel);
    And(a=notSel, b=a, out=w1);
    And(a=sel, b=b, out=w2);
    Or(a=w1, b=w2, out=out);
}
```

| sel | a   | b   | out |
| --- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 0   | 0   | 1   | 0   |
| 0   | 1   | 0   | 1   |
| 0   | 1   | 1   | 1   |
| 1   | 0   | 0   | 0   |
| 1   | 0   | 1   | 1   |
| 1   | 1   | 0   | 0   |
| 1   | 1   | 1   | 1   |

Boolean Equation: (NOT(sel) AND a) OR (sel AND b)

**Implementation Hint:** Think of MUX as "If (sel == 0) then out = a else out = b". The boolean expression implements this logic directly.

### Demultiplexer (DMUX)
Routes an input to one of two outputs based on a selection bit.

```
CHIP DMux {
    IN in, sel;
    OUT a, b;

    PARTS:
    Not(in=sel, out=notSel);
    And(a=in, b=notSel, out=a);
    And(a=in, b=sel, out=b);
}
```

| in  | sel | a   | b   |
| --- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 0   | 1   | 0   | 0   |
| 1   | 0   | 1   | 0   |
| 1   | 1   | 0   | 1   |

**Implementation Hint:** DMUX is the opposite of MUX - it takes one input and routes it to one of two outputs based on the selector. Think of it as "if (sel == 0) then {a,b} = {in,0} else {a,b} = {0,in}".

### Multi-Bit Implementations

#### 16-bit NOT
```
CHIP Not16 {
    IN in[16];
    OUT out[16];

    PARTS:
    Not(in=in[0], out=out[0]);
    Not(in=in[1], out=out[1]);
    Not(in=in[2], out=out[2]);
    Not(in=in[3], out=out[3]);
    Not(in=in[4], out=out[4]);
    Not(in=in[5], out=out[5]);
    Not(in=in[6], out=out[6]);
    Not(in=in[7], out=out[7]);
    Not(in=in[8], out=out[8]);
    Not(in=in[9], out=out[9]);
    Not(in=in[10], out=out[10]);
    Not(in=in[11], out=out[11]);
    Not(in=in[12], out=out[12]);
    Not(in=in[13], out=out[13]);
    Not(in=in[14], out=out[14]);
    Not(in=in[15], out=out[15]);
}
```

**Example Results:**

| In               | Out              |
| ---------------- | ---------------- |
| 0000000000000000 | 1111111111111111 |
| 1111111111111111 | 0000000000000000 |
| 1010101010101010 | 0101010101010101 |
| 0011110011000011 | 1100001100111100 |

**Implementation Hint:** You can use NAND Gate, but the Not Gate is allowed, so it's basically the Not operation applied to each of the 16 bits independently.

#### 16-bit AND
```
CHIP And16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    And(a=a[0], b=b[0], out=out[0]);
    And(a=a[1], b=b[1], out=out[1]);
    And(a=a[2], b=b[2], out=out[2]);
    And(a=a[3], b=b[3], out=out[3]);
    And(a=a[4], b=b[4], out=out[4]);
    And(a=a[5], b=b[5], out=out[5]);
    And(a=a[6], b=b[6], out=out[6]);
    And(a=a[7], b=b[7], out=out[7]);
    And(a=a[8], b=b[8], out=out[8]);
    And(a=a[9], b=b[9], out=out[9]);
    And(a=a[10], b=b[10], out=out[10]);
    And(a=a[11], b=b[11], out=out[11]);
    And(a=a[12], b=b[12], out=out[12]);
    And(a=a[13], b=b[13], out=out[13]);
    And(a=a[14], b=b[14], out=out[14]);
    And(a=a[15], b=b[15], out=out[15]);
}
```

**Example Results:**

| a                | b                | Out              |
| ---------------- | ---------------- | ---------------- |
| 0000000000000000 | 0000000000000000 | 0000000000000000 |
| 0000000000000000 | 1111111111111111 | 0000000000000000 |
| 1111111111111111 | 1111111111111111 | 1111111111111111 |
| 1010101010101010 | 0101010101010101 | 0000000000000000 |
| 0011110011000011 | 0000111111110000 | 0000110011000000 |

**Implementation Hint:** This is basically the AND operation applied independently to each corresponding bit position in the two 16-bit input buses.

### Multi-Way Multiplexers and Demultiplexers

#### 4-Way 16-bit Multiplexer
```
CHIP Mux4Way16 {
    IN a[16], b[16], c[16], d[16], sel[2];
    OUT out[16];
    
    PARTS:
    Mux16(a=a, b=b, sel=sel[0], out=w1);
    Mux16(a=c, b=d, sel=sel[0], out=w2);
    Mux16(a=w1, b=w2, sel=sel[1], out=out);
}
```

**Selector Logic:**
- out = a if sel = 00
- out = b if sel = 01
- out = c if sel = 10
- out = d if sel = 11

**Implementation Hint:** Can be implemented with just multiple Mux16 gates. First, use sel[0] to choose between a/b and between c/d, then use sel[1] to choose between those two results. This creates a tree-like structure that efficiently implements the 4-way selection.

#### 8-Way 16-bit Multiplexer
```
CHIP Mux8Way16 {
    IN a[16], b[16], c[16], d[16],
       e[16], f[16], g[16], h[16],
       sel[3];
    OUT out[16];

    PARTS:
    Mux16(a=a, b=b, sel=sel[0], out=w1);
    Mux16(a=c, b=d, sel=sel[0], out=w2);
    Mux16(a=e, b=f, sel=sel[0], out=w3);
    Mux16(a=g, b=h, sel=sel[0], out=w4);
    Mux16(a=w1, b=w2, sel=sel[1], out=x1);
    Mux16(a=w3, b=w4, sel=sel[1], out=x2);
    Mux16(a=x1, b=x2, sel=sel[2], out=out);
}
```

**Selector Logic:**
- out = a if sel = 000
- out = b if sel = 001
- out = c if sel = 010
- out = d if sel = 011
- out = e if sel = 100
- out = f if sel = 101
- out = g if sel = 110
- out = h if sel = 111

**Implementation Hint:** Similar to Mux4Way16 circuit, don't overthink it. Everything is built to follow a pattern. Use sel[0] to select between pairs, then sel[1] to select between pairs of pairs, and finally sel[2] to select the final output. To verify your approach, trace the path with sel = "000" - it should lead to output 'a'.

#### 4-Way Demultiplexer
```
CHIP DMux4Way {
    IN in, sel[2];
    OUT a, b, c, d;

    PARTS:
    DMux(in=in, sel=sel[1], a=w1, b=w2);
    DMux(in=w1, sel=sel[0], a=a, b=b);
    DMux(in=w2, sel=sel[0], a=c, b=d);
}
```

**Demultiplexer Logic:**
- {a,b,c,d} = {in,0,0,0} if sel = 00
- {a,b,c,d} = {0,in,0,0} if sel = 01
- {a,b,c,d} = {0,0,in,0} if sel = 10
- {a,b,c,d} = {0,0,0,in} if sel = 11

**Implementation Hint:** Think of it like decoding a signal. We would use a DMux to decode a signal that went through a Mux. It's essentially a Mux4Way but in the opposite direction. First split the input based on sel[1], then further split each result based on sel[0].

#### 8-Way Demultiplexer
```
CHIP DMux8Way {
    IN in, sel[3];
    OUT a, b, c, d, e, f, g, h;

    PARTS:
    DMux(in=in, sel=sel[2], a=w1, b=w2);
    DMux(in=w1, sel=sel[1], a=x1, b=x2);
    DMux(in=w2, sel=sel[1], a=x3, b=x4);
    DMux(in=x1, sel=sel[0], a=a, b=b);
    DMux(in=x2, sel=sel[0], a=c, b=d);
    DMux(in=x3, sel=sel[0], a=e, b=f);
    DMux(in=x4, sel=sel[0], a=g, b=h);
}
```

**Demultiplexer Logic:**
- {a,b,c,d,e,f,g,h} = {in,0,0,0,0,0,0,0} if sel = 000
- {a,b,c,d,e,f,g,h} = {0,in,0,0,0,0,0,0} if sel = 001
- {a,b,c,d,e,f,g,h} = {0,0,in,0,0,0,0,0} if sel = 010
- {a,b,c,d,e,f,g,h} = {0,0,0,in,0,0,0,0} if sel = 011
- {a,b,c,d,e,f,g,h} = {0,0,0,0,in,0,0,0} if sel = 100
- {a,b,c,d,e,f,g,h} = {0,0,0,0,0,in,0,0} if sel = 101
- {a,b,c,d,e,f,g,h} = {0,0,0,0,0,0,in,0} if sel = 110
- {a,b,c,d,e,f,g,h} = {0,0,0,0,0,0,0,in} if sel = 111

**Implementation Hint:** Follow what was done with Mux8Way16, but think of it as decoding a 1-bit input. To decode a Mux8Way, we would use a DMux8Way. It's the same implementation but in the opposite direction, creating a tree structure that routes the input to one of eight outputs.

## Additional Components

### Or8Way
A gate that outputs 1 if at least one of its 8 inputs is 1, otherwise outputs 0.

```
CHIP Or8Way {
    IN in[8];
    OUT out;

    PARTS:
    Or(a=in[0], b=in[1], out=w1);
    Or(a=in[2], b=in[3], out=w2);
    Or(a=in[4], b=in[5], out=w3);
    Or(a=in[6], b=in[7], out=w4);
    Or(a=w1, b=w2, out=x1);
    Or(a=w3, b=w4, out=x2);
    Or(a=x1, b=x2, out=out);
}
```

**Test Results:**

| in       | out |
| -------- | --- |
| 00000000 | 0   |
| 11111111 | 1   |
| 00010000 | 1   |
| 00000001 | 1   |
| 00100110 | 1   |

**Implementation Hint:** Don't overthink it - it's a straightforward tree of OR gates that combines the inputs in pairs and then combines the intermediate results until we have a single output.

