---
layout: page
title: Chapter 5 - Using Stack Memory Nodes
---

## Segment 40196: Signal Pattern Detector

### 174 cycles, 4 nodes, 14 instructions

[Save file](../save/40196.0.txt)

Node 0 contains all the logic. The program switches between different _states_ (no zeros, one zero, and two or more zeros) instead of counting consecutive zeros by incrementing a register.

### Historical version

The Signal Pattern Detector was originally segment 40633, and the sequence to be detected was 1, 5, 4. Here is the code for node 0 in this version:

        START: JRO UP
        ONE:   MOV -99 ACC
        TWO:   NOP
        THREE: JMP NOPE
        FOUR:  JMP LBL4
        FIVE:  ADD 99
         JNZ NOPE
         MOV 125 ACC
         JMP NOPE
        LBL4: SUB 125
         JNZ NOPE
         MOV 1 RIGHT
         JMP START
         NOPE: MOV 0 RIGHT

## Segment 41427: Sequence Peak Detector

### 265 cycles, 6 nodes, 32 instructions

[Save file](../save/41427.0.txt)

Node 8 calculates the minimum value by comparing each input to the smallest value it has seen so far (or to 999 for the first value in a sequence). Two copies of each input are used. The second copy is either stored as the new minimum value or used to restore the previous minimum (`ADD UP` saves several cycles over `MOV UP NIL / SWP / SAV`).

Node 9 calculates the maximum value similarly.

### Optimized for nodes: 400 cycles, 4 nodes, 34 instructions

[Save file](../save/41427.1.txt)

Solution by [Dostav](https://github.com/Dostav).

Node 5, firstly, sends down the first sequence element and then checks for 0 if it is then sends 11 and starts from the beginning otherwise sends down 1 and the sequence element 4 times.

Node 8 and 9, firstly, save the first sequence element and then calculate the minimum and the maximum values by following commands from Node 5 when Node 8 at the same time works as a bridge to pass values from Node 5 to Node 9.

## Segment 42656: Sequence Reverser

### Optimized for size: 349 cycles, 4 nodes, 11 instructions

[Save file](../save/42656.0.txt)

Node 5 saves a zero to the empty stack, so it can detect the "bottom" of the stack without having to keep track of the number of values on the stack. Node 5 reads input values and pushes them onto the stack until it receives a zero, then pops values off the stack and sends them to the output until it sees a -1 again.

The terminating zero in each input sequence gets written to the stack and then immediately discarded. This is still faster than checking whether the input is zero every time before writing it to the stack, like this:

        FWD: MOV LEFT ACC
         JEZ REV
         MOV ACC UP
         JMP FWD
        REV: # ...

In general, it's faster to write loops that have only one conditional jump at the very end of the loop, and that just continue to the next part of the program when the loop is finished. We add one instruction after the loop, but make the body of the loop (which is executed many times) one instruction shorter:

        FWD: MOV LEFT ACC
         MOV ACC UP
         JGZ FWD
         MOV UP NIL
        REV: # ...

### Without Stack Memory Nodes: 759 cycles, 7 nodes, 42 instructions

[Save file](../save/42656.1.txt)

Optimized by [CaitSith2](https://github.com/CaitSith2).

Node 1 makes each input sequence exactly 6 values long by adding -1s to the end, and removes the trailing zero.

Node 5 reads in a sequence of exactly 6 values from `LEFT`, then outputs the same sequence in reverse order to `DOWN`, followed by a 0. Three of the values are stored temporarily in node 6.

Node 7 passes values from node 5 to the output, discarding all -1s that were added by node 4.

### Optimized for speed: 257 cycles, 4 nodes, 33 instructions

[Save file](../save/42656.2.txt)

Solution by [gmnenad](https://github.com/gmnenad).

Nodes 1 and 4 save each sequence of numbers to the Stack Memory Nodes, alternating between the upper node (during the loop labeled `S`) and the lower node (during the loop labeled `S2`).

Nodes 5 and 7 read each sequence of numbers from the Stack Memory Nodes and write them to the output.

Each node passes a -1 to the other nodes when it finishes filling or emptying a stack.

## Segment 43786: Signal Multiplier

### Naive solution: 1007 cycles, 5 nodes, 32 instructions

[Save file](../save/43786.0.txt)

Nodes 1 and 2 are reused from Sequence Generator, and determine which is larger between `IN.A` and `IN.B`.

Node 5 stores the larger input in `BAK`, and passes a sequence to node 7 with length equal to the smaller input.

Node 7 is reused from Sequence Counter, and calculates the sum of each sequence to give the result of the multiplication.

### Optimized for size: 639 cycles, 4 nodes, 27 instructions

[Save file](../save/43786.1.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

Node 2 passes `IN.A` to node 5, followed by two copies of `10 - IN.B`.

Node 5 stores `IN.A` in `ACC` and passes one copy of `10 - IN.B` to node 7, followed by a sequence of `IN.A` repeated `IN.B` times. Instead of decrementing a loop counter, the `JRO` instruction is used to _unroll_ the loop by starting with a sequence of 10 `MOV ACC DOWN` instructions and skipping the first `10 - IN.B`.

Node 7 starts with zero in `ACC` and uses the copy of `10 - IN.B` passed from node 5 to count how many times to add `IN.A`. After writing the result to `OUT`, `ACC` is reset to zero.

### Optimized for speed: 474 cycles, 6 nodes, 45 instructions

[Save file](../save/43786.2.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

Node 2 compares `IN.A` and `IN.B` as in the naive solution, and passes the larger value first to minimize the number of repeated additions. Nodes 5, 6, and 7 in this solution (with some additional plumbing in node 4) are equivalent to nodes 2, 5, and 7 in the size-optimized solution.

### Shift-and-add: 789 cycles, 5 nodes, 46 instructions

[Save file](../save/43786.3.txt)

Optimized solution by [CaitSith2](https://github.com/CaitSith2).

This is a commonly used algorithm for multiplying two numbers, similar to the pencil-and-paper "long multiplication" taught in elementary schools.

Nodes 1 and 4 calculate the binary representation of `IN.A` (with 3 representing a binary one and 1 representing a binary zero) and pass it, most significant bit first, to node 5.

Node 5 uses the binary value of `IN.A` to determine when to pass a copy of `IN.B` to node 7 and when to pass a zero value.

Node 7 sums up the values from node 5, shifting `ACC` one bit to the left (i.e. multiplying it by 2) after each step.
