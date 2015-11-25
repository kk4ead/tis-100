# Chapter 3: Self-Test Diagnostic through Signal Multiplexer

## Segment 00150: Self-Test Diagnostic

### Optimized: 83 cycles, 8 nodes, 8 instructions

[Save file](save/00150.0.txt)

A pretty straightforward program: just `MOV` data from one port to the next. This program shows the theoretical maximum throughput of one value per 2 cycles.

### Pessimized: 100037 cycles, 8 nodes, 16 instructions

[Save file](save/00150.1.txt)

Mostly the same as the optimized version, but let's take a look at node 0. It spends over 99% of its time in the inner loop:

         MOV 638 ACC    #4
        INNER: SUB 1    #5
         JGZ INNER      #6

This is a simple loop that runs for 638 iterations, counting down from 638 to 0. It's almost always more efficient to count down than up in TIS-100; here's what the same loop would look like, but counting up from 0 to 638:

         MOV 0 ACC
        INNER: SUB 638
         JEZ DONE
         ADD 639
         JMP INNER
        DONE:

Since our only conditional jumps are based on comparing `ACC` to zero, we have to check `ACC` _minus 638_ on each iteration, then add back 638+1 before going on to the next iteration. This takes up 2 extra lines of code and 2 extra cycles per loop iteration. (Note: this loop runs for 639 iterations instead of 638. To understand why, try mentally walking through both loops using 2 for the number of iterations.)

Back to the program:

         MOV 2 ACC      #1
        OUTER: SUB 1    #2
         SAV            #3
         # INNER LOOP... #
         SWP            #7
         JGZ OUTER      #8

The inner loop is wrapped inside an outer loop that runs for 2 iterations. The outer loop's counter is saved to `BAK` before the inner loop writes all over `ACC`, and then restored once the inner loop is finished.

Finally, we let the node do what it's supposed to do and...

         MOV UP DOWN    #9

This program could be further pessimized by changing the constants from 2 and 638 to 999 and 999, for a total running time of about 80 million cycles.

## Segment 10981: Signal Amplifier

### Optimized for size: 160 cycles, 4 nodes, 6 instructions

[Save file](save/10981.0.txt)

Multiplying a number by 2 is the same as adding it to itself, but we have to read it into `ACC` first. The read and the addition take 2 cycles, leaving the input and the downstream nodes idle 50% of the time.

### Optimized for speed: 84 cycles, 5 nodes, 9 instructions

[Save file](save/10981.1.txt)

The addition step is a bottleneck, but fortunately it's simple enough to parallelize easily. If we can split the input values into two streams by sending them alternately to nodes 2 and 4, then we can join the streams back together at node 5 and double our throughput. We could write

        MOV UP DOWN
        MOV UP RIGHT
        
at node 1 and

        MOV LEFT DOWN
        MOV UP DOWN

at node 5 just to make sure the values come out in the right order, but we'll save two instructions by using `ANY` to handle the order for us: since both streams take the same number of cycles, whichever value went in first will still come out first!

## Segment 20176: Differential Converter

### 201 cycles, 5 nodes, 11 instructions

[Save file](save/20176.0.txt)

Node 2: calculate `IN.B - IN.A` and pass it on

Node 9: pass `IN.B - IN.A` to `OUT.N` and node 10

Node 10: `-(IN.B - IN.A) = IN.A - IN.B`; pass it to `OUT.P`

## Segment 21340: Signal Comparator

### Optimized for size: 278 cycles, 6 nodes, 20 instructions

[Save file](save/21340.0.txt)

Nodes 6, 7, and 8 have nearly identical programs: First, pass the input value along to the next node. If the value is (greater than / equal to / less than) zero, output a one. Otherwise, output a zero and move on to the next input.

### Without conditional jumps: 272 cycles, 6 nodes, 30 instructions

[Save file](save/21340.1.txt)

Nodes 0 and 4 do some preprocessing on the input, as shown in the table below. The final value going into node 6 will be 1 for any negative input, 3 for a zero input, and 5 or greater for any positive input.

Pipelining these steps instead of waiting until node 6 to do them saves almost 200 cycles.

Step | Operation | Min  | -1   | 0    | 1    | Max
---- | --------- | ---- | ---- | ---- | ---- | ---
0    | (Input)   | -999 | -1   | 0    | 1    | 999
1    | `SUB 998` | -999 | -999 | -998 | -997 | 1
2    | `ADD 999` | 0    | 0    | 1    | 2    | 999
3    | `ADD 1`   | 1    | 1    | 2    | 3    | 999
4    | `ADD ACC` | 2    | 2    | 4    | 6    | 999
5    | `SUB 1`   | 1    | 1    | 3    | 5    | 998

Nodes 6 through 8 use the processed input value as an index into the rest of the program, similar to the `switch` statement in C. (The labels are just for ease of reading.)

        LOOP:
         # ... #
         JRO ACC
        1: MOV 0 DOWN
           JMP LOOP
        3: MOV 1 DOWN
           JMP LOOP
        5+: MOV 0 DOWN
        
Since a positive input can result in a value anywhere from 5 to 998, and a `JRO` past the end of the program behaves the same as a `JRO` to the end, the 5 "case" needs to be the last instruction in the program.

If we had to choose between a fixed range of values (for example, 1 through 5), we could write a straightforward jump table instead. And in the next problem, we'll do that.

## Segment 22280: Signal Multiplexer

### Optimized for size: 272 cycles, 5 nodes, 16 instructions

[Save file](save/22280.0.txt)

Node 2 contains all the logic. When `IN.S` is nonzero, we need to discard one of the values, otherwise `IN.A` and `IN.B` will get out of sync with each other.

### Using JRO: 239 cycles, 7 nodes, 22 instructions

[Save file](save/22280.1.txt)

Node 2 calculates `(4*IN.S) + 5`, which transforms -1 / 0 / 1 into 1 / 5 / 9. Node 6 uses that value as an argument to `JRO`, and reads `IN.A` and `IN.B` from nodes 5 and 7. 

Notes on this program:
 - The `NOP`s are never executed; they're only there for padding.
 - It's much easier to multiply a number by a power of 2 than by an arbitrary integer.
 - We can read `IN.B` into the `ACC` of node 6 even before the `JRO`, since we know we'll need to consume both input values anyway.

### Optimized for speed: 204 cycles, 7 nodes, 21 instructions

[Save file](save/22280.2.txt)

The trick here is recognizing that instead of having one node that chooses between three output values, we can use _two_ nodes that each make a simpler choice between _two_ output values. When `IN.S` is zero, we add both `IN.A` and `IN.B` to the output; when `IN.S` is -1, we only add `IN.A`; and when `IN.S` is 1, we only add `IN.B`.

In C syntax, the behavior we're looking for is: `OUT = ( (IN.S > 0) ? 0 : IN.A ) + ( (IN.S < 0) ? 0 : IN.B )`. The conditional assignments are parallelized between nodes 1 and 3, and node 6 performs the addition.

[Back](chapter02.md) - [Contents](README.md) - [Next](chapter04.md)
