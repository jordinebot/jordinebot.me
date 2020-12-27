---
title: "Advent of Code 2020 (4th week)"
date: 2020-12-27T12:56:37+01:00
tags: ['programming', 'python', 'advent-of-code']
---

I started the 4th week of Advent of Code without having completed all the puzzles from week 3 yet. I missed part 2 of day 18 and both parts of day 20. Still, I think the best approach it to prioritise the daily riddle before trying to finish the missing ones. So, last week of Advent of Code 2020, here we go:

## Day 21: Allergen Assessment
After my fiasco on day 20, day 21 put me back on track. I solved the allergens problem with sets and set operations iterating my data until I got the ingredient for each allergen. It was a problem similar to day-16 (Ticket Translation).

And part 2 was really easy. I already got the data I needed from my part 1 solution (which got me thinking that maybe I missed a simpler solution for the first part, but whatever...)

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-21/main.py)

## Day 22: Crab Combat
I messed up a couple of times in my card's combat with the small [crab](https://en.wikipedia.org/wiki/Crab). First, I forgot to convert my input numbers to integers so I fell for the classic:

```python
'4' > '32'
True
```

As if this wasn't an issue in JavaScript as well... Then I used a recursive solution, which worked well for part 1 but it hit the maximum recursion depth in part 2 before I realised that the problem was perfectly suitable for an iterative approach and use recursion just for the sub-games instead of for every hand. And finally, I missed a key point of the instructions:

> To play a sub-game of Recursive Combat, each player creates a new deck by making a copy of the next cards in their deck __(the quantity of cards copied is equal to the number on the card they drew to trigger the sub-game).__

I was using the whole deck to play the sub-game, and I lost a lot of time due to that error, because it was working perfectly for the test data but not for the real input.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-22/main.py)

## Day 23: Crab Cups

Another round with the crab fella... I had issues to understand what was going on between moves 3 and 4 in the sample game, but after that it was easy to find a solution. The big problem appeared in part 2 because I used a list to represent the cups and of course, my solution couldn't handle 10,000,000 iterations over 1,000,000 items. I tried a minimal optimisation by using a [deque](https://docs.python.org/3/library/collections.html#collections.deque) but it wasn't enough and I just gave up the second star.

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-23/main.py)

## Day 24: Lobby Layout

I really enjoyed Christmas Eve's puzzle. Mostly because I did it together with my <acronym title="Significant Other">SO</acronym>. She's been really supportive this whole month, asking about all the puzzles and so on. Even though she knows nothing about programming, we thought about the problem together and I was explaining step by step the code I was writing, trying to make it specially comprehensive. We had a good time and she really got a better understanding of what coding is all about after this.

{{< figure caption="The coordinates system we created to represent the tiles" src="/images/hex_grid_coordinates.png" >}}


👨‍💻👩🏻 [Our solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-24/main.py)

## Day 25: Combo Breaker

I appreciated that the last puzzle was that easy. The hardest part was understanding the instructions and figuring out that the subject number `7` was an _arbitrary_ (it would probably be better called _convenient_) constant.

I got my 45th star and I understood that the 2nd for the day was only granted by having the previous 49. Fair enough!

👨‍💻 [My solution](https://github.com/jordinebot/advent-of-code/blob/main/2020/day-25/main.py)

# Conclusion
I'm really happy with my journey in this year's AoC. I wouldn't say that I've written the most Python-ish code, but after this four weeks I can now read and write basic programs fluently, which is definitely a level up for me! I had a good time, I suppose I improved a bit my problem solving skills (I confirmed that I need to dust off my algorithms and data structures books), and I ranked 25th in my company's private leaderboard (sorted by number of stars) out of 61 people who got at least one star. Not bad! See you next year!

{{< figure src="/images/aoc_2020_private_leaderboard.png" >}}