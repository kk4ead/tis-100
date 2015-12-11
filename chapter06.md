# Chapter 6: Image Test Pattern 1 through Histogram Viewer

## Segment 50370: Image Test Pattern 1

### Single node: 2333 cycles, 1 node, 10 instructions

[Save file](save/50370.1.txt)

After writing the starting coordinates of each row to `IMAGE`, a sequence of 30 pixels is generated. After each row is finished, the current Y coordinate is restored from `BAK` and incremented.

### Two nodes: 2282 cycles, 2 nodes, 9 instructions

[Save file](save/50370.2.txt)

Node 5 stores the Y coordinate, saving the `SWP/ADD 1/SAV` delay at the end of each row.

### Optimized for speed: 1187 cycles, 4 nodes, 15 instructions

[Save file](save/50370.0.txt)

Nodes 5 and 8 take turns writing values to the output. Since node 5 writes both the first and last value in each row, node 8 has to wait one cycle at the end of the row to avoid getting out of order.

## Segment 51781: Image Test Pattern 2

### Optimized for speed: 1187 cycles, 4 nodes, 27 instructions

[Save file](save/51781.1.txt)

This is similar to the solution for Image Test Pattern 1, but nodes 5 and 8 swap colors on alternate rows.

## Segment 52544: Exposure Mask Viewer

### 617 cycles, 4 nodes, 36 instructions

[Save file](save/52544.1.txt)

For each rectangle, node 2 passes the starting X value and the width straight to node 5. For each row of the current rectangle, node 2 passes the current Y value and the number of rows remaining to node 5.

For each rectangle, node 5 stores the starting X value and 6 minus the width, to allow unrolling the loop in node 9. (No width values in the input are greater than 6.) For each row of the current rectangle, node 5 passes the starting X value, the current Y value, and 6 minus the width to node 9.

For each row, node 9 passes the starting X value, the current Y value, and a sequence of the correct width to the output.

[Back](chapter05.md) - [Contents](README.md)
