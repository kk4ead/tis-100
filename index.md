---
layout: page
title: Contents
---

## Tessellated Intelligence System Best Practices - Patterns of Node Communication

This is an (eventually) comprehensive spoiler for [Zachtronics'](http://www.zachtronics.com/) game [TIS-100](http://www.zachtronics.com/tis-100/), with notes on the system architecture, some common optimization techniques, and annotated solutions for each program.

## Part I: General Information

### [Chapter 1: Implementation Details](chapter01)

This chapter documents various syntax quirks and undefined behaviors, such as integer overflows (everything is clipped to +/- 999), segfaults (can't happen), and multiple simultaneous reads and writes to a Stack Memory Node.

### [Chapter 2: Instruction Timings](chapter02)

In short: Writing to a port takes two cycles (or more if it blocks) unless a Stack Memory Node is doing the writing. Reading from a port takes one cycle (or more if it blocks). Everything else takes one cycle.

## Part II: TIS-100 Spoilers

### [Chapter 3: Getting Started](chapter03)

Covers segments 00150 (Self-Test Diagnostic) through 22280 (Signal Multiplexer).

An introduction to loops, parallelization using `ANY`, and `switch` statements with `JRO`.

### [Chapter 4: Learning about Pipelining](chapter04)

Covers segments 30647 (Sequence Generator) through 33762 (Interrupt Handler).

It's like parallelism, but in series!

### [Chapter 5: Using Stack Memory Nodes](chapter05)

Covers segments 40196 (Signal Pattern Detector) through 43786 (Signal Multiplier).

State machines; avoiding gratuitous `SWP/SAV`s; preventing stack underflows without using a counter.

### [Chapter 6: Pretty Pictures](chapter06)

Covers segments 50370 (Image Test Pattern 1) through 53897 (Histogram Viewer).

### [Chapter 7: Algorithms](chapter07)

Covers segments 60099 (Signal Window Filter) through 63534 (Sequence Sorter).

Here's where things really start to get complicated.

### [Chapter 8: The End](chapter08)

Covers segments 70601 [3 WORDS REDACTED] and UNKNOWN [2 WORDS REDACTED].

What really happened to Uncle Randy? And who built this machine anyway?

## Part III: TIS-NET Spoilers

Coming soon!

## Authors

- [kk4ead](https://github.com/kk4ead)
- [CaitSith2](https://github.com/CaitSith2) -- many optimized solutions
- [mercutiodesign](https://github.com/mercutiodesign) -- bugfixes
- [imamassi](https://github.com/imamassi) -- optimizations
- [Solomute](https://github.com/Solomute) -- optimizations

## License

This guide is released under the <a href="https://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International Public License</a> (CC BY-SA 4.0).

This site's theme is based on <a href="https://github.com/poole/lanyon">Lanyon</a>, &copy; 2014 Mark Otto and used under the <a href="https://opensource.org/licenses/MIT">MIT license</a>.
