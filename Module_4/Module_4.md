# Boolean Arithmetic and the ALU

## Binary Numbers

- Machine language is represented by 0 and 1. Follows by the **N-bits or 2^N possibilities**. 
- Most significant bits is represented on the leftmost of the binary representation. Least significant follows through the rightmost.

| Decimal | Binary (4-bits) |
| ------- | --------------- |
| 0       | 0000            |
| 2       | 0010            |
| 4       | 0100            |
| 7       | 0111            |
- Example: Binary representation of 99
	- Start with the highest leftmost bits and go down the sum.
	- 01100011
	- 64 + 32 + 2 + 1 = 99
	- 2^6 + 2^5 + 2^1 + 2^0 = 99 

## Binary Addition
![Binary_Addition](./Assets/Binary_Addition_example.png)
- Similar to adding math in elementary, but instead the max is one. 
- 01111101 + 00110110 = 10110011

### Occurence of Overflow
- There might be a situation where the operation of the binary digit will bypass the limited sizes of the bits, which is called overflow. 
	- If this happens, the one that is carry over the bit. It is either ignored or will crash the program.
- ![Overflow](./Assets/Overflow.png)

### Half Adder
- **Half Adder**: 2-bits Adder (No sums for carry)
- ![Half-adder-overview](./Assets/Half-Adder_Overview.png)
```
CHIP HalfAdder {
    IN a, b;    // 1-bit inputs
    OUT sum,    // Right bit of a + b 
        carry;  // Left bit of a + b

    PARTS:
    //// Replace this comment with your code.
    Xor(a = a, b = b, out = sum);
    And(a= a, b= b, out= carry);
}
```
![half-adder-circuit](./Assets/Half-Adder-circuit.png)

| a   | b   | sum | carry |
| --- | --- | --- | ----- |
| 0   | 0   | 0   | 0     |
| 0   | 1   | 1   | 0     |
| 1   | 0   | 1   | 0     |
| 1   | 1   | 0   | 1     |

Implementation: **Don't over think, it is straightforward with two seperate outputs.**

### Full Adder
- **Full-Adder**: 3-bits Adder
- ![Full-adder-overview](./Assets/Full-Adder_Overview.png)
```
CHIP FullAdder {
    IN a, b, c;  // 1-bit inputs
    OUT sum,     // Right bit of a + b + c
        carry;   // Left bit of a + b + c

    PARTS:
    //// Replace this comment with your code.
    Xor(a = a, b = b, out = w1);
    Xor(a = c, b = w1, out = sum);
    And(a= a, b= b, out= x1);
    And(a=c , b= w1, out= x2);
    Or(a=x1 , b= x2, out= carry);
}
```
![full-adder-circuit](./Assets/full-adder.webp)

| a   | b   | c   | sum | carry |
| --- | --- | --- | --- | ----- |
| 0   | 0   | 0   | 0   | 0     |
| 0   | 0   | 1   | 1   | 0     |
| 0   | 1   | 0   | 1   | 0     |
| 0   | 1   | 1   | 0   | 1     |
| 1   | 0   | 0   | 1   | 0     |
| 1   | 0   | 1   | 0   | 1     |
| 1   | 1   | 0   | 0   | 1     |
| 1   | 1   | 1   | 1   | 1     |

### Multi-bits Adder
- **Multi-bits Adder**: Adds two numbers
- Combination of full-adders to sum multiple bits. Overflow are ignored.
- ![full-adder-demo](./Assets/Adder_Demo.png)

- 16-bits Adder
- ![16-bits-adder](./Assets/16-bits-adder.png)