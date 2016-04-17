---
layout: page
title: Chapter 8 - The End
---

## Segment 70601: Stored Image Decoder

### 3449 cycles, 4 nodes, 21 instructions

[Save file](save/70601.0.txt)

No attempt was made to optimize this solution.

Node 1 stores the current color in `BAK` and the line length in `ACC`, generating a raw sequence of color values.

Node 5 breaks the sequence from node 1 into rows of 30 values, and inserts the appropriate control values: starting column (always zero), starting row, terminating -1.

## Segment UNKNOWN: Anti-Tamper Certification

### 843 cycles, 6 nodes, 37 instructions

[Save file](save/UNKNOWN.0.txt)

No attempt was made to optimize this solution.

For each input value, `IN` divided by 27 is written to `OUT.R`. Every time `OUT.R` changes, the previous value of `OUT.R` and the number of inputs since the last change are written to `OUT.E`. For example, in Test 1, `OUT.E` could be read verbally as "four twos, seven zeros, thirteen ones, ... four twos, two zeros."

Node 1 calculates `OUT.R`; Nodes 4 and 6 read from node 1, passing one copy to the output and two copies to node 7. Node 7 compares the current output value to the previous value and either increments the counter in node 8 (by passing a one) or resets it (by passing a negative value).

Warning: this program crashes on the random test if `OUT.R` changes just before the final -1 input.

[Back](chapter07.html) - [Contents](index.html)
