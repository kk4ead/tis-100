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

### Optimized for size: 617 cycles, 4 nodes, 36 instructions

[Save file](save/52544.0.txt)

For each rectangle, node 2 passes the starting X value and the width straight to node 5. For each row of the current rectangle, node 2 passes the current Y value and the number of rows remaining to node 5.

For each rectangle, node 5 stores the starting X value and 6 minus the width, to allow unrolling the loop in node 9. (No width values in the input are greater than 6.) For each row of the current rectangle, node 5 passes the starting X value, the current Y value, and 6 minus the width to node 9.

For each row, node 9 passes the starting X value, the current Y value, and a sequence of the correct width to the output.

### Optimized for speed: 601 cycles, 5 nodes, 36 instructions

[Save file](save/52544.1.txt)

Optimization by [CaitSith2](https://github.com/CaitSith2).

For each rectangle, the starting X value and the width are passed through node 4 instead of tying up node 2 for four extra cycles.

## Segment 53897: Histogram Viewer

### Optimized for size: 4736 cycles, 4 nodes, 18 instructions

[Save file](save/53897.0.txt)

All the computation takes place in node 9. For each column, the X value is stored in `BAK` and the starting Y value (18 minus the column height) is read into `ACC`. A pixel is drawn and the Y value is incremented until it reaches the bottom of the screen, then the X value is incremented and a new starting Y value is read from the input.

### Optimized for speed: 2557 cycles, 5 nodes, 26 instructions

[Save file](save/53897.1.txt)

Node 6 generates Y values, starting at the bottom of the screen for each column and moving upward until the column is finished.

Node 8 generates X values, starting at the left of the screen and moving one pixel to the right after each column is finished.

Node 9 does nothing but write values to the output. Since it is only idle 3% of the time (nodes 6 and 8 take an additional 3 cycles to reset in between columns), this solution is almost--but not quite--theoretically optimal.

### Theoretically optimal: 2467 cycles, 6 nodes, 25 instructions

[Save file](save/53897.2.txt)

Solution by [CaitSith2](https://github.com/CaitSith2).

Node 1 makes 2 copies of the input.

One copy gets fed into node 5, which determines which Y value the line is to start drawing at.

Node 8 then feeds the Y to node 9, and increments Y.

Node 2 determines if Node 6 should increment its X value prior to Node 6 feeding its X value to node 9.

Node 9 takes X, Y and draws the histogram lines, one pixel at a time.

[Back](chapter05.md) - [Contents](README.md) - [Next](chapter07.md)
