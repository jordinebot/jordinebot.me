---
title: "Advent of Code 2020 (2nd week)"
date: 2020-12-16T21:28:07+01:00
tags: ['programming', 'python', 'advent-of-code']
---

The second week of  Advent of Code 2020 started with a notable step in difficulty compared to the puzzles that we faced so far. Let's recap, day by day.


## Day 7: Handy Haversacks

That was a puzzle about content and containers. Colorful bags that contain other bags that contain other bags that... Part 1 was about finding all the containers that could, at the end, contain a particular bag (a _shiny gold_ one!), and part 2 was the other way around: count the total amount of bags that a specific container can contain.

I used a dictionary to represent the graph. Without quantities for the first part and with them for the second:

```python
# Part 1
{'wavy black': ['dotted indigo', 'light tan', 'bright cyan']}
# Part 2
{'wavy black': [('dotted indigo', '4'), ('light tan', '1'), ('bright cyan', '5')]}
```

I spent a good amount of time to solve that one...

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-7/main.py)

## Day 8: Handheld Halting

And finally a program that simulated kind of a VM. After last year's IntCode machine I cannot say I was happy when I saw the input, but at the end it wasn't that hard. This handheld console only had three instructions (NOP, ACC and JMP) and required a couple of registers (`acc` and `ip`).

The first part was easy enough, just run the code until `ip` repeats an address and return the value of the accumulator before that.

For the second part, that was about switching NOP and JMP instructions to try to avoid the infinite loop, I started with a brute force approach (and this is something I must avoid). After it worked I implemented a better solution using [backtracking](https://en.wikipedia.org/wiki/Backtracking).

```text
Part 1: acc = 1749 (0.428915ms)
Part 2: acc = 515 (319.941998ms) [brute force]
Part 2: acc = 515 (218.647242ms) [backtracking]
```

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-8/main.py)

## Day 9: Encoding Error

My biggest issue with this one was that I forgot to set the preamble to 25 after using the test data set that only required 5 numbers as preamble.

Besides that the puzzle easy to understand and it didn't take much time, which I was thankful for, after dealing with days 7 and 8.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-9/main.py)

## Day 10: Adapter Array

I didn't like much this one. I struggled a lot with part 2. The problem seemed a good fit for a recursive solution, but it had too much complexity to make it feasible that way.

Also, I don't know if I'm abusing unpacking my maps... In many of my solutions I use:

```python
data = [*map(lambda x:..., list)]
```

And I'm _afraid_ that might not be the best practice...

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-10/main.py)

## Day 11: Seating System

Day 11 seemed like a tribute to [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life), which I love, so I was happy at the beginning. But after a while I was pissed off at all the `IndexError: list index out of range` errors when trying to properly handle the edges of the grid.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-11/main.py)

## Day 12: Rain Risk

Rain Risk was basically about parsing movement instructions (direction and distance) in a 2D coordinate system.

I found it quite easy and entertaining (it was a relief that the only turns in the input were multiples of 90 degrees) and my only mistake was not considering that from the waypoint's point of view, the ship was the origin. So I tried to translate the waypoint to (0,0) before rotating. Doing that caused some points not to be integers, which didn't seem consistent with all the data so I quickly understood about my mistake.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-12/main.py)

## Day 13: Shuttle Search

A tough one for me. Even though I really enjoyed discrete maths at the uni, nowadays my mod arithmetics are rusty, to say the least.

I could manage to solve part 1 on my own my I had to rely on my company's `#adventofcode` Slack channel to know about the [Chinese Remainder Theorem](https://en.wikipedia.org/wiki/Chinese_remainder_theorem), which I implemented following this [document](http://homepages.math.uic.edu/~leon/mcs425-s08/handouts/chinese_remainder.pdf).

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-13/main.py)