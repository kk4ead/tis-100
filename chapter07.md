---
layout: page
title: Chapter 7 - Algorithms
---

## Segment 60099: Signal Window Filter

### 894 cycles, 7 nodes, 34 instructions

[Save file](../save/60099.0.txt)

Node 0 passes each input value to node 3.

Node 1 initializes the upper Stack Memory Node with five zeros, then halts.

Node 3 passes the 4th-most recent input from node 4 to node 7, then passes the most recent input from node 0 to nodes 4 and 7.

Node 4 passes the 6th-most recent input from node 5 to node 8, passes the 4th-most recent input from node 5 to node 3, then passes the most recent input from node 3 to nodes 5 and 8.

Node 5 passes five values from the upper Stack Memory Node:
 - the 6th-most recent input to node 4;
 - the 5th-most recent input to the lower Stack Memory Node;
 - the 4th-most recent input to both node 4 and the lower Stack Memory Node;
 - the 3rd-most recent input to the lower Stack Memory Node;
 - the 2nd-most recent input to the lower Stack Memory Node.

Node 5 then passes five values to the upper Stack Memory Node:
 - the most recent input from node 4;
 - the 2nd- through 5th-most recent inputs from the lower Stack Memory Node.

Node 7 keeps a running total of the 3 most recent inputs in `ACC` by subtracting the 4th-most recent input, adding the most recent input, then writing the result to `OUT.3`.

Node 8 keeps a running total of the 5 most recent inputs in `ACC` by subtracting the 6th-most recent input, adding the most recent input, then writing the result to `OUT.5`.

## Segment 61212: Signal Divider

### Optimized for speed: 1553 cycles, 9 nodes, 70 instructions

[Save file](../save/61212.1.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

This solution uses a shift-and-subtract algorithm that is similar to both paper-and-pencil long division and the shift-and-add multiplication algorithm for Signal Multiplier.

Nodes 1 and 2 calculate the eights and fours place of the quotient, storing the quotient in the left Stack Memory Node and the divisor in the right Stack Memory Node.

Nodes 4 and 5 calculate the twos and ones place of the quotient, storing the quotient in node 6 and passing the remainder through node 6 to node 7.

## Segment 62711: Sequence Indexer

### 4466 cycles, 6 nodes, 34 instructions

[Save file](../save/62711.0.txt)

No attempt was made to optimize this solution.

Node 0 reads a sequence of numbers from `IN.V` and stores it (in reverse order) to the upper Stack Memory Node, keeping track of the sequence length in `BAK`. After reading the terminating zero, it passes the sequence length to node 2 and halts.

Node 3 reads the sequence length from node 2 and stores it in `BAK`. It then passes the sequence back and forth between the upper and lower Stack Memory Nodes, while passing copies of the sequence to node 4.

Node 4 reads an index value from `IN.X` (through node 1), discards values from node 3 until it reaches the correct position in the sequence (in the loop beginning at `A: MOV LEFT  NIL`), writes a value from node 3 to the output (through node 7), and discards the remaining values from node 3 (in the loop beginning at `C: MOV LEFT  ACC`).

## Segment 63534: Sequence Sorter

### Insertion sort: 2761 cycles, 9 nodes, 77 instructions

[Save file](../save/63534.0.txt)

No attempt was made to optimize this solution.

The sequence being constructed is stored in reverse order in the lower Stack Memory Node, and stored in forward order in the upper Stack Memory Node.

Node 3 reads a value from the input sequence (through node 0), then passes it to node 2 for each value in the existing sequence. Nodes 2 and 6 compare the current input value to each existing value, insert it at the correct position in the sequence, and pass the sequence to the upper Stack Memory Node through node 3. After a value is inserted, nodes 3 and 4 pass the sequence back to the lower Stack Memory Node.

On reading the terminating zero, node 3 signals node 7 to begin executing by passing it a value. Nodes 1, 5, 7, and 8 reverse the sequence one last time, pass it to the output, and pass a value back to node 3 to let execution begin on the next sequence. This is only necessary because the author failed to keep track of which stack held the forward sequence and which one held the reversed sequence.
