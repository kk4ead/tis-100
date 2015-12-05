# Chapter 5: Signal Pattern Detector through Signal Multiplier

## Segment 40196: Signal Pattern Detector

### 174 cycles, 4 nodes, 14 instructions

[Save file](save/40196.0.txt)

Node 0 contains all the logic. The program switches between different _states_ (no zeros, one zero, and two or more zeros) instead of counting consecutive zeros by incrementing a register.

### Historical version

The Signal Pattern Detector was originally node 40633, and the sequence to be detected was 1, 5, 4. Here is the code for node 0 in this version:

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

[Save file](save/41427.0.txt)

Node 8 calculates the minimum value by comparing each input to the smallest value it has seen so far (or to 999 for the first value in a sequence). Two copies of each input are used. The second copy is either stored as the new minimum value or used to restore the previous minimum (`ADD UP` saves several cycles over `MOV UP NIL / SWP / SAV`).

Node 9 calculates the maximum value similarly.

## Segment 42656: Sequence Reverser

### 349 cycles, 4 nodes, 11 instructions

[Save file](save/42656.0.txt)

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

[Save file](save/42656.1.txt)

Node 4 makes each input sequence exactly 6 values long by adding -1s to the end, and removes the trailing zero.

Node 5 reads in a sequence of exactly 6 values from `LEFT`, then outputs the same sequence in reverse order to `DOWN`, followed by a 0. The values are stored in nodes 2, 6, and 8. (The sequence length could be increased by also using those nodes' `BAK` registers.)

Node 7 passes values from node 5 to the output, discarding all -1s that were added by node 4.

## Segment 43786: Signal Multiplier

### 1007 cycles, 5 nodes, 27 instructions

[Save file](save/43786.2.txt)

Nodes 1 and 2 are reused from Sequence Generator, and determine which is larger between `IN.A` and `IN.B`.

Node 5 stores the larger input in `BAK`, and passes a sequence to node 7 with length equal to the smaller input.

Node 7 is reused from Sequence Counter, and calculates the sum of each sequence to give the result of the multiplication.

[Back](chapter04.md) - [Contents](README.md) - [Next](chapter06.md)