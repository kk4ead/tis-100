---
layout: page
title: Chapter 8 - The End
---

## Segment 70601: Stored Image Decoder

### Naive solution: 3449 cycles, 4 nodes, 21 instructions

[Save file](../save/70601.0.txt)

No attempt was made to optimize this solution.

Node 1 stores the current color in `BAK` and the line length in `ACC`, generating a raw sequence of color values.

Node 5 breaks the sequence from node 1 into rows of 30 values, and inserts the appropriate control values: starting column (always zero), starting row, terminating -1.

### Optimized for speed: 1494 cycles, 12 nodes, 99 instructions

[Save file](../save/70601.1.txt)

Solution and writeup by [gmnenad](https://github.com/gmnenad).

Nodes 2 and 3 calculate how many pixels of new color are in first line (until wrap or until end of new color), and that value is passed via node 0 for draw. After that node 1 loop for any full line (30 pixels) of that color, and finally for remaining pixels at start of last line.

Node 4 remembers current X for all shapes/colors (it moves it as cursor), and also detect if new line (to reset X to 0) and also pass that new line info to node 8. Node 8 remember Y coord and increase it on new line info from node 4.

Node 5 remembers new color if passed from 1, or pass old remembered color if 1 sends -1 (for other lines of same color).

Nodes 6,10 and 11 draw one line in one row and color only. Node 11 gets width of line (originating from 1 or all way from 3 if first segment), and loop in chunks of 9 (that many `MOV -3,DOWN` is present in node 10). Node 10 gets X and Y from nodes 4 and 8 respectively, and then draws chunk of line up to 9 pixels long, looping as controlled by node 11.

## Segment UNKNOWN: Anti-Tamper Certification

### 843 cycles, 6 nodes, 37 instructions

[Save file](../save/UNKNOWN.0.txt)

No attempt was made to optimize this solution.

For each input value, `IN` divided by 27 is written to `OUT.R`. Every time `OUT.R` changes, the previous value of `OUT.R` and the number of inputs since the last change are written to `OUT.E`. For example, in Test 1, `OUT.E` could be read verbally as "four twos, seven zeros, thirteen ones, ... four twos, two zeros."

Node 1 calculates `OUT.R`; Nodes 4 and 6 read from node 1, passing one copy to the output and two copies to node 7. Node 7 compares the current output value to the previous value and either increments the counter in node 8 (by passing a one) or resets it (by passing a negative value).

Warning: this program crashes on the random test if `OUT.R` changes just before the final -1 input.
