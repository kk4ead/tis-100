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

[Back](chapter04.md) - [Contents](README.md)