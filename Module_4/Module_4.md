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
- **Half Adder**: Able to add two 1-bit inputs and produce a sum and carry-out. However, there no process to compute the carry. Thus, called a Half-Adder.
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

Implementation: **Don't over think, it is straightforward with two separate outputs.**

### Full Adder
- **Full-Adder**: Using the Carry result from the Half-Adder, we can use the result into the Carry-In input to process the full calculation of binary addition. 
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

| a   | b   | Carry-In | sum | Carry-Out |
| --- | --- | -------- | --- | --------- |
| 0   | 0   | 0        | 0   | 0         |
| 0   | 0   | 1        | 1   | 0         |
| 0   | 1   | 0        | 1   | 0         |
| 0   | 1   | 1        | 0   | 1         |
| 1   | 0   | 0        | 1   | 0         |
| 1   | 0   | 1        | 0   | 1         |
| 1   | 1   | 0        | 0   | 1         |
| 1   | 1   | 1        | 1   | 1         |

### Multi-bits Adder
- **Multi-bits Adder**: Adds two numbers
- With the combination of full-adders and one half-adder, to represent the lower significant bit, we can sum multiple bits as a result. Overflow are ignored, if exceed the bits size of the memory. In this case, it would be 8-bits. 
- 2-bits Adder will composed of a half-adder and a full-adder. 
- 4-bits Adder: Half-Adder represent the lower-significant bit (first-bit). We can use that carry result to compute other full-adders for correctly calculating binary sums. 
- ![4-bits-adder](./Assets/4-bits-adder.webp)
- ![full-adder-demo](./Assets/Adder_Demo.png)

## Negative Numbers

### Sign Bit
- In the earliest consideration of time to represent negative numbers in binary, the first form was using Sign-bit. Essentially, the first bit, on the leftmost, is -/+ where - is followed by a 1. **Was later scraped due to complication**.
- As you can see in the table below, there are many complications including -0 and many edge cases that make it difficult to follow and even consider being used in computer hardware. Even -3, in this case, is hard to follow.

| 000 | 0   |
| --- | --- |
| 001 | 1   |
| 010 | 2   |
| 011 | 3   |
| 100 | -0  |
| 101 | -1  |
| 110 | -2  |
| 111 | -3  |
### 2's Complement
- This form called 2's complement was introduced later and it is currently used by all modern devices today to represent fixed point values from negative to positive numbers. Essentially, starting from the leftmost bit, 1 is for negative number and 0 is for positive. **Any overflowing binary will be represented by 1 for negative number**. 

| 000 | 0   |
| --- | --- |
| 001 | 1   |
| 010 | 2   |
| 011 | 3   |
| 100 | -4  |
| 101 | -3  |
| 110 | -2  |
| 111 | -1  |

- Two methods of determining the negative number of the binary...
	- Inverse Process (use more commonly for larger numbers):
		- Subtract 1 -> 1111 - 1 = 1110
		- Invert all bits -> 0001
		- Using standard binary notation -> 0001 = 1
		- Then add the negative sign -> -1
	- Reading it directly...
		- Take the leftmost bit and assign it negative -> 1111 -> -2^3 = -8
		- Use the other bits as positive numbers -> 4, 2, 1
		- -8 + 4 + 2 + 1 = -1 -> As a result, our negative number of our binary is -1. 
- Determine the binary of a given negative number...
	- Opposite of the inverse process (for example, let's do -5)...
		- Find the binary of the positive value: 5 -> 0101
		- Invert all bits -> 1010
		- Add 1 -> 1011
		- **1011** = -5
### Addition in 2's Compliments

- 4-bits 2's Compliment example: 7 + (-5) = 2
	- 7 -> 0111
	- -5 -> 1011
	- 0111 + 1011 -> 10010
	- Ignoring the overflow -> **0010**
	- 0010 -> 2

### Computer Representation

- To represent negative binary numbers within a computer hardware, we need to essentially make a tradeoff because every computer can only handle certain limitation of memory.
	- For example, unsigned integer is (2^32 - 1) bits which is 0 -> 4,294,967,295. 
	- As a result, it will end taking the range of -(2^31) to (2^31 - 1) bits to represent -2,147,483,648 to 2,147,483,647.

## Arithmetic Logic Unit

