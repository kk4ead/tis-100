# Chapter 7: Signal Window Filter through Sequence Sorter

## Segment 60099: Signal Window Filter

### 894 cycles, 7 nodes, 34 instructions

[Save file](save/60099.0.txt)

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

[Save file](save/61212.1.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

This solution uses a shift-and-subtract algorithm that is similar to both paper-and-pencil long division and the shift-and-add multiplication algorithm for Signal Multiplier.

Nodes 1 and 2 calculate the eights and fours place of the quotient, storing the quotient in the left Stack Memory Node and the divisor in the right Stack Memory Node.

Nodes 4 and 5 calculate the twos and ones place of the quotient, storing the quotient in node 6 and passing the remainder through node 6 to node 7.

[Back](chapter06.md) - [Contents](README.md)
