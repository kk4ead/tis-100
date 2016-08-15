---
layout: page
title: Chapter 6 - Pretty Pictures
---

## Segment 50370: Image Test Pattern 1

### Single node: 2333 cycles, 1 node, 10 instructions

[Save file](../save/50370.1.txt)

After writing the starting coordinates of each row to `IMAGE`, a sequence of 30 pixels is generated. After each row is finished, the current Y coordinate is restored from `BAK` and incremented.

### Two nodes: 2282 cycles, 2 nodes, 9 instructions

[Save file](../save/50370.2.txt)

Node 5 stores the Y coordinate, saving the `SWP/ADD 1/SAV` delay at the end of each row.

### Optimized for speed: 1187 cycles, 4 nodes, 15 instructions

[Save file](../save/50370.0.txt)

Nodes 5 and 8 take turns writing values to the output. Since node 5 writes both the first and last value in each row, node 8 has to wait one cycle at the end of the row to avoid getting out of order.

## Segment 51781: Image Test Pattern 2

### Optimized for speed: 1187 cycles, 4 nodes, 27 instructions

[Save file](../save/51781.1.txt)

This is similar to the solution for Image Test Pattern 1, but nodes 5 and 8 swap colors on alternate rows.

## Segment 52544: Exposure Mask Viewer

### Optimized for size: 617 cycles, 4 nodes, 36 instructions

[Save file](../save/52544.0.txt)

For each rectangle, node 2 passes the starting X value and the width straight to node 5. For each row of the current rectangle, node 2 passes the current Y value and the number of rows remaining to node 5.

For each rectangle, node 5 stores the starting X value and 6 minus the width, to allow unrolling the loop in node 9. (No width values in the input are greater than 6.) For each row of the current rectangle, node 5 passes the starting X value, the current Y value, and 6 minus the width to node 9.

For each row, node 9 passes the starting X value, the current Y value, and a sequence of the correct width to the output.

### Optimized for speed: 601 cycles, 5 nodes, 36 instructions

[Save file](../save/52544.1.txt)

Optimization by [CaitSith2](https://github.com/CaitSith2).

For each rectangle, the starting X value and the width are passed through node 4 instead of tying up node 2 for four extra cycles.

## Segment 53897: Histogram Viewer

### Optimized for size: 4736 cycles, 4 nodes, 18 instructions

[Save file](../save/53897.0.txt)

All the computation takes place in node 9. For each column, the X value is stored in `BAK` and the starting Y value (18 minus the column height) is read into `ACC`. A pixel is drawn and the Y value is incremented until it reaches the bottom of the screen, then the X value is incremented and a new starting Y value is read from the input.

### Semi-optimized for speed: 1975 cycles, 11 nodes, 100 instructions

[Save file](../save/53897.1.txt)

Solution by [Confused-Enemy](https://github.com/Confused-Enemy).

Node 0 acts as a switching node for Node 4 (Lines) + Node 3 (Dots/Points). It determines which side of the two line column it will draw down the dot values ~ right side down <0; left side down >0; no dots = 0. It also sends a value to Node 3 instructing it to wait (no dots to send out due to equal line height) or to go ahead and process the Y value stored in acc. It will always process line values first, if any, before handing control over to Node 3. Exhaustively, this Node also tells Node 6 whether to increment its X value before processing the dots Y values as well as after Y processing, if needed.

Nodes 1 + 2 determine the highest and lowest input of 2 values at a time, reversing the scale from 18-0 to 0-18 (Bringing it in-line with TIS-100's image xy-coordinate system). The highest value goes to Node 0 and lowest goes to Node 3. They also both act as a gateway for 2-way communications between Nodes 0 + 3.

Node 3 counts Y values for dot coords. It also lets Node 0 know when it has completed sending out its dot values.

Node 4 counts Y values for line coords. It also switches on Node 0 commands instructing what Node 6 should do with X values.

Node 5 acts as a gateway to send line values down to Node 8 as well as send or increment the X value to push from Node 6 down to Node 9; It also responds to Node 7 for Dot coord processing.

Node 6 either pushes out the X value or increments the X value based on input from Node 0 which is passed to it via Node 4 + 5. 

Node 7 simply holds to send the Y value down from Node 3 and then instructs Node 6 to push the current X value down.

Node 8 takes the Y Value and stores it temporarilly (Kind of like a wait instruction before NOP) to allow for the X value to send first.

Node 9 pushes any down

Node 10 recieves Y value before NOPping, allowing exact sync timing for the X value to be sent down first.

May be possible to optimise this further based on the mixed scheme of using lines and points. Lines are more optimal to send out as at least a two point line which will use 5 instructions and to perform the same draw operation using individual points will use 8 instructions.

### Theoretically optimal: 2467 cycles, 6 nodes, 25 instructions

[Save file](../save/53897.2.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

Node 1 makes 2 copies of the input.

One copy gets fed into node 5, which determines which Y value the line is to start drawing at.

Node 8 then feeds the Y to node 9, and increments Y.

Node 2 determines if Node 6 should increment its X value prior to Node 6 feeding its X value to node 9.

Node 9 takes X, Y and draws the histogram lines, one pixel at a time.
