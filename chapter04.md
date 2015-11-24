# Chapter 4: Sequence Generator through Interrupt Handler

# Segment 30647: Sequence Generator

## Optimized for size: 156 cycles, 4 nodes, 17 instructions

[Save file](save/30647.0.txt)

All the logic is contained in Node 2.

We need to "look at" each input value twice: first for determining which one is larger, then again to create the output stream. Node 1 passes two copies of `IN.A` to Node 2, and Node 2 stores a copy of `IN.B` in `BAK`.

## Optimized for speed: 96 cycles, 7 nodes, 20 instructions

[Save file](save/30647.1.txt)

In Chapter 3, Sequence Amplifier taught us to speed up a calculation by using multiple nodes to do the same operations on multiple streams of data. Today we'll use multiple nodes to do _different_ operations on the _same_ stream of data.

It wasn't very efficient to have Node 2 doing all the work; in the size-optimized solution, Node 2 is always running while Nodes 6 and 9 are idle almost half the time. We can split its work into three separate tasks.

Node 2 still calculates `IN.B - IN.A`, but instead of using the result to sort the outputs as before, it just passes the result to Node 6 and grabs another input value as soon as possible.

Node 6 puts the outputs in the correct order, and Node 9 adds a `0` onto the end.

After running the program, look at the path from `IN.B` to `OUT`. Node 6 is busiest at 8% idle, but Nodes 2 and 9 are only 17% idle. (The other nodes are less busy, but they're just for plumbing.) By keeping each node about equally busy with its own step in the pipeline, we cut down on the amount of time spent waiting.

For homework, swap the order of the `MOV ACC RIGHT` and `MOV ACC DOWN` instructions in Node 1. What happens? Why?

# Segment 31904: Sequence Counter

(in progress)

[Back](chapter03.md) - [Contents](README.md)