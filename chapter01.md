# Chapter 1: Implementation Details

## Syntax

Commas are treated like whitespace.

Lines containing only comments and whitespace are ignored (not treated as NOPs), do not count toward relative offsets for the `JRO` instruction, and do not add to your instruction count for scoring purposes. Nodes containing only comments and whitespace remain in the `IDLE` state and do not add to your node count for scoring purposes.

Numbers and punctuation (except for `':'`, `' '`, `'#'`, `'!'`, and `','`) are valid characters in label names.

## Implementation-Defined Behavior

### Node Capacity

Basic Execution Nodes hold 15 lines of up to 18 characters each. The longest possible instruction is `XX:MOV RIGHT RIGHT`.

Stack Memory Nodes hold up to 15 values.

### Arithmetic Overflows

Results are clipped to +/- 999 and do not wrap around. Integer literals are clipped before evaluation; i.e. `ADD 1000` behaves the same as `ADD 999`, even when `ACC` contains a negative value.

### Uninitialized Pointers

Using the `LAST` pseudo-port when its value is `N/A` (before it has been initialized with `ANY`) has the same effect as using `NIL`.

## Undefined Behavior

### Segmentation Faults

A `JRO` forward past the end of a program behaves the same as a `JMP` to the last instruction. A `JRO` backward past the beginning of a program behaves the same as a `JMP` to the first instruction.

### Race Conditions: Simultaneous Reads

Simultaneous reads from a Basic Execution Node that is writing to `ANY` are resolved in the following priority order. The other destination nodes will block.

Simultaneous reads from a Stack Memory Node are resolved in the following priority order. The other destination nodes will block. If a Stack Memory Node is read from and written to on the same cycle, the read is performed before the write(s).

1. `UP`
2. `LEFT`
3. `RIGHT`
4. `DOWN`

### Race Conditions: Simultaneous Writes

Simultaneous writes to a Basic Execution Node that is reading from `ANY` are resolved in the following priority order. The other source nodes will block.

Simultaneous writes to a Stack Memory Node are all completed on the same cycle. The values are written to the stack in the following order _after_ any read operation for that cycle is resolved.

1. `LEFT`
2. `RIGHT`
3. `UP`
4. `DOWN`

### The Visualization Module

Writes with a color value greater than 4 are ignored, but still advance the cursor. Writes with a negative color value terminate the command sequence.

Writes beyond the right or bottom edge of the display are ignored.

Since any negative value terminates the command sequence, it is not possible to specify X or Y coordinates beyond the left or top edges of the display.
