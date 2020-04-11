---
layout: post
title: "WhatsApp Puzzle - Solution using ILP"
date: 2020-04-11
---
WhatsApp sent me this simple puzzle recently. Find the digits that will open the lock using the hints provided.

![Default PNG]({{ site.url }}/assets/images/wa_puzzle.png)

Its not terribly difficult to crack this puzzle. But why think, when it can be automated :) If we convert the hints to constraints, then this can be cast as an [ILP](https://en.wikipedia.org/wiki/Integer_programming) (Integer Linear Program) problem. Open source solvers like [Cbc](https://github.com/coin-or/Cbc) have come a long way and are readily available. 

To use the command line cbc utiluty, the problem has to be expressed in the [LP](http://lpsolve.sourceforge.net/5.0/CPLEX-format.htm) file format. There are many ways to express this problem as an ILP. The simplest is to create binary variables `x_ij`, to represent if a number `i = 0..9` is assigned to the position `j = 0..2` on the lock. The hints are used to create constraints on the variables.

```
\ ilp to solve whatsapp puzzle
\ xij = j'th position is assigned with i. i=0..9, j=0,1,2
Minimize
   obj: x00 + x10 + x20 + x30 + x40 + x50 + x60 + x70 + x80 + x90 + x01 + x11 + x21 + x31 + x41 + x51 + x61 + x71 + x81 + x91 + x02 + x12 + x22 + x32 + x42 + x52 + x62 + x72 + x82 + x92

Subject To
   \ 682 : one is correct and well placed
   x60 + x81 + x22 >= 1

   \ 614 : one is correct but wrongly placed
   x61 + x62 + x10 + x12 + x40 + x41 >= 1
   x60 + x11 + x42 = 0

   \ 206 : Two numbers are correct but wrongly placed
   x21 + x22 + x00 + x02 + x60 + x61 >= 2
   x20 + x01 + x62 = 0

   \ 738 : Nothing is correct
   x70 + x71 + x72 + x30 + x31 + x32 + x80 + x81 + x82 = 0

   \# 780 : one  is correct but wrongly placed
   x71 + x72 + x80 + x82 + x00 + x01 >= 1
   x70 + x81 + x02 = 0

   \ each pos can have only one value
   x00 + x10 + x20 + x30 + x40 + x50 + x60 + x70 + x80 + x90 = 1
   x01 + x11 + x21 + x31 + x41 + x51 + x61 + x71 + x81 + x91 = 1
   x02 + x12 + x22 + x32 + x42 + x52 + x62 + x72 + x82 + x92 = 1

Binary
   x00 x10 x20 x30 x40 x50 x60 x70 x80 x90 x01 x11 x21 x31 x41 x51 x61 x71 x81 x91 x02 x12 x22 x32 x42 x52 x62 x72 x82 x92

End
```

To solve it using cbc: `cbc code.lp solv solu code_soln.txt`. This is a small problem and cbc finds a solution in no time. 
```
Result - Optimal solution found

Objective value:                3.00000000
Enumerated nodes:               0
Total iterations:               0
Time (CPU seconds):             0.00
Time (Wallclock seconds):       0.00
```

Heres the solution. **x00**, **x41** and **x22** are **1** and the rest are zeros. The solution is therefore **042**.

```
Optimal - objective value 3.00000000
      0 x00                    1                       1
      1 x10                    0                       1
      2 x20                    0                       1
      3 x30                    0                       1
      4 x40                    0                       1
      5 x50                    0                       1
      6 x60                    0                       1
      7 x70                    0                       1
      8 x80                    0                       1
      9 x90                    0                       1
     10 x01                    0                       1
     11 x11                    0                       1
     12 x21                    0                       1
     13 x31                    0                       1
     14 x41                    1                       1
     15 x51                    0                       1
     16 x61                    0                       1
     17 x71                    0                       1
     18 x81                    0                       1
     19 x91                    0                       1
     20 x02                    0                       1
     21 x12                    0                       1
     22 x22                    1                       1
     23 x32                    0                       1
     24 x42                    0                       1
     25 x52                    0                       1
     26 x62                    0                       1
     27 x72                    0                       1
     28 x82                    0                       1
     29 x92                    0                       1
```

Overkill? certainly! 
