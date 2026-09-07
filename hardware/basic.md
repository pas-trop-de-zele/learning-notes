## BLUF
I have no hardware knowledge. This document shall document my understanding from the ground up

## Back to basics
- Electrical signals can be represented in binary states: 0 and 1 represented by different electrical voltage levels
- A `digital circuit` takes one or more 0/1 input signals and produce output signals representing 0/1
- A `logic gate` is one type of `digital circuit` which implement a simple logical operation like AND/OR/NOT
  ```
  A ──┐
      AND ──> Output
  B ──┘
  ```
- `transistors` are tiny physical  building blocks used to build circuits

## ALU
- ALU is the main executing unit inside each cpu core
- What it essntially does is taking input from 1 or more operands with an operation 
  ```
                   +--------------------+
  A -------------->|                    |
  B -------------->|  ALU ---> selector |------> result
  operation ------>|                    |
                   +--------------------+
  ```
- A/B here could be represented by multiple wires (8-bit/8 wires, 32-bit/ 32 wires, 64-bits/ 64 wires) so the more bit the more values each operand could represent
- operation tells the ALU which result should be selected as the final output