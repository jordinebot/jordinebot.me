---
title: "Advent of Code 2020 (1st week)"
date: 2020-12-05T14:03:07+01:00
tags: ['programming', 'python', 'advent-of-code']
---

Today is day 5 in 2020's [Advent of Code](https://adventofcode.com/2020/about). A kind of Advent Calendar of programming
puzzles that you can solve in any programming language of your choice. Every puzzle has two parts, and for each
correctly solved part you get one star ⭐️. Obviously, the goal is to get all 50 stars from December 1st to Christmas
Day. Last year I dropped after 4 days, and I only got 7 stars. Not a great achievement, truth be told.

So, this year I'm trying again and I've challenged myself to code the solutions in **Python**. A language that I've never
used before. My personal goals are:

- Learn a new programming language
- Level up my problem solving skills
- Focus on learning and having fun over performance

I will try to consistently update this post with notes on every day puzzles (not necessarily on a daily basis, though)

## Day 1: Report Repair

I focused on the basics: how to read the input, creating and iterating lists, defining functions. The puzzle was easy,
specially if you didn't care about
[complexity](https://en.wikipedia.org/wiki/Time_complexity#Table_of_common_time_complexities) and go for the quadratic
and cubic solutions for parts 1 and 2 respectively.

After solving the puzzle, I went to [r/adventofcode](https://www.reddit.com/r/adventofcode/) and I discovered [A Teacher
Looks at Advent of Code 2020 - Day 1](https://cestlaz.github.io/post/advent-2020-day-01/) which gave a hint on [List
Comprehensions](https://www.pythonforbeginners.com/basics/list-comprehensions-in-python). Will try to apply that if it
fits further riddles.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-1/main.py)

## Day 2: Password Philosophy

Day 2 was about validating passwords with two methods based on different interpretations of the same input string. No
big deal. The two most obvious approaches were using string splitting or regular expressions and I went for the second. 

It surprised me the way Python provides two ways for working with regular expressions, compiled or non-compiled ones.
Basically,

```python
import re
sample = "1-3 a: abcde"
pattern = "(\d+)-(\d+) ([a-z]): ([a-z]+)"

# Option 1: Compiled RegEx
regex = re.compile(pattern)
matches = regex.findall(sample)

# Option 2: Non-compiled RegEx
matches = re.findall(pattern, sample)
```

What are the benefits of one over the other? Performance?

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-2/main.py)

## Day 3: Toboggan Trajectory

Sooner or later I expected some kind of graphic input, and _Toboggan Trajectory_ was the first puzzle of this kind in
2020.

```
..##.......
#...#...#..
.#....#..#.
..#.#...#.#
.#...##..#.
..#.##.....
.#.#.#....#
.#........#
#.##...#...
#...##....#
.#..#...#.#
```

It represents a forest map, where `#` are trees. Assuming top-left as our initial position, the question was to count
the number of trees we would encounter by following a straight line of a given slope. It was an easy one, and I learned
that the `%` operator is actually the modulus in Python and not the remainder like in JavaScript or C.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-3/main.py)

## Day 4: Passport Processing

This puzzle had the most messed up input so far. The main problems were first to build a proper
[dictionary](https://docs.python.org/3/tutorial/datastructures.html#dictionaries) to represent every passport in the
input file, and second to write all the validation methods for each field.

It wasn't that hard but there were many ways to introduce bugs.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-4/main.py)

## Day 5: Binary Boarding

Day 5 was about decoding boarding passes to find our seat in the plane. Not even with the puzzle title hint I noticed
the equivalence between boarding passes like `FBFBBFFRLR` where `F|L` were the _lower_ part (0) and `B|R` the _upper_
(1), being equivalent to the binary `0101100101` (357). So, my [first
solution](https://github.com/jordinebot/advent-of-code/blob/f1b213a724f466fbaa7ff1e33b27054e482bebad/2020/day-5/main.py)
was a direct representation of the method described in the puzzle's prose.

```python
def get_seat(boarding_pass):
	row = [i for i in range(128)]
	for r in boarding_pass[:7]:
		if r == "F": row = row[:len(row) // 2]
		if r == "B": row = row[len(row) // 2:]

	col = [i for i in range(8)]
	for c in boarding_pass[7:]:
		if c == "L": col = col[:len(col) // 2]
		if c == "R": col = col[len(col) // 2:]

	return row[0] * 8 + col[0]
```

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-5/main.py)(After some optimisation)

## Day 6: Custom Customs

It was funny how part 2 started saying _As you finish the last group's customs declaration, you notice that you misread one word in the instructions_ (everyone instead of anyone) and I actually did misread that! Fortunately I got it right after reading it again.

The puzzle was an easy one about sets. Requiring union for part 1 (anyone) and intersection for part 2 (everyone).

I learned about [_subscriptable_](https://stackoverflow.com/a/49588151/1534704) objects and kept practicing [packing/unpacking](https://stackabuse.com/unpacking-in-python-beyond-parallel-assignment/) with the `*` operator. Used to have only arrays and objects in JavaScript, I'm still overwhelmed by tuples, lists, maps, dicts...

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-6/main.py)