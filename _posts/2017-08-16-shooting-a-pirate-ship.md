---
title:     "Catching a pirate ship"
date:      2017-08-16
tags:      puzzles
published: false
---

# Catching a pirate ship

This blog post is about a logic puzzle that I particularly enjoyed.

I originally heard it in the University of Waterloo's Computer Science Club. Having looked for it online, I found that it has often been attributed to [Rustan Leino](https://www.microsoft.com/en-us/research/people/leino/) who calls it 'Catching a spy' and attributes it in turn to Radu Grigore.

## The puzzle

> A pirate ship is located on a one-dimensional line. At time 0, the ship is at location S. With each time interval, the ship moves V units to the right (if V is negative, the ship is moving left). S and V are fixed integers, but they are unknown to you. You are to destroy the ship. The means by which you can attempt to do that is: at each time interval (starting at time 0), you can choose a location on the line and blindly shoot at it. That is, you will ask a question like “Is the ship currently at location 27?” and you will get a yes/no answer. Devise an algorithm that will eventually find the ship.

## Analysis

This puzzle has a few attributes which are common among puzzles that I consider to be especially well crafted:

1. It isn't a trick/outside-the-box question. That is, the solution does not depend on a particular interpretation of the question. There is only one "correct" interpretation of this puzzle, and anyone sharing it ought to do their best to be as clear about it as possible.

1. It is possible to solve it with just pencil and paper, or even in your head. The solution does not rely on executing tedious math, or playing around with a compass and straightedge.

  - Furthermore, I think that using 'aids' are not likely to be helpful, as the puzzle does not lend itself very well to brute-force attempts at solving it.

1. The intended answer to this puzzle is self-evident. That is, it may be difficult to find the solution, but once you have it, it's comparatively easy to verify that the solution is correct.

1. The solution to the puzzle can be broken down into a fairly well-defined set of related concepts, which come together to give the solution.

## Solution

**Do not continue reading** if you wish to solve the puzzle for yourself.

The key to the solution to this puzzle is the fact that `S` and `V` must be integers. Normally, pirate ships are capable of having velocities that are [Real values](https://en.wikipedia.org/wiki/Real_number). The fact that the pirate ship in this world can move at a velocity of `2` or a velocity of `3`, but not at a velocity of `2.54` seems pretty unusual.

As it happens, the Integers have the property that they are _countably infinite_, as opposed to the Real Numbers, which are _uncountably infinite_. What this means is that if you try to count all of the integers, you will be counting forever, however, you will be able to count up to any given integer. On the other hand, if you try to count the Real numbers, not only will you be counting forever, but you also won't be able to count up to any given real number. We'll tie back into this later.

The next thing to understand is that `S` and `V` are the only values that define a ship in this universe. That is, if two ships have the same `S` value and the same `V` value, then if you manage to shoot one, you've managed to shoot both. For all intents and purposes, they are _the same ship_. The implication of this is that to find a solution to this puzzle, you need only to account for all possible combinations of values of `S` and `V`.

There are many possible ways to proceed from here. A natural approach is to consider some specific cases of `S` and `V` and try to generalize from there. For example, let's consider the ship `(0,0)` (Here I introduce some notation which means, the ship which has `S = 0` and `V = 0`). This ship is really easy to deal with. It starts and position 0 and just stays there, so we can shoot at position `0` at any time, and we're done. But we can do much better! Now consider all of the ships of the form `(0,V)`, that is all of the ships that start at position `0`, and have any velocity. We don't know where the ships are going, or how fast, but we do know that if we shoot at position `0` at time `0` we will have shot them. Look at that! With a single shot, we've already accounted for _infinitely many_ possibilities.

Unfortunately, there's still a considerable ways to go. We've accounted for one value of `S`, but infinitely many more values remain unaccounted for. That being said, given that we've gotten this far using only a single shot, hopefully, you have a feeling that this puzzle is solvable.

So, as a next step, what if we tackle all the ships of the form `(1,V)`. We know that at time `0`, all of _these_ ships will be at location `1`, however we've already used up our shot at time `0`, so we have to wait for one time interval. At time `1` however, we can no longer dispatch all such ships with a single shot because they've spread out. Ship `(1,0)` remains at location `1`, ship `(1,1)` has moved to location `2`, and ship `(1,100)` has moved to location `101`.

We can gain some intuition for why this has happened by considering the function which lets us figure out where a ship is at any given time `t`:

`position(t) = S + (V * t)`

If we again consider all ships that have `S = 0`, then the right-hand side of our function simplifies to `V * t`, and since we were shooting at time `0`, it further simplifies to just `0`.

In the case where `S = 1` and `t = 1`, the right-hand side simplifies to `1 + V`, which clearly can not be dealt with in a single shot. Our naive attempt at generalizing doesn't seem to be working so let's try something else.

Our problem here is stemming from trying to account for many possible ships at the same time, so what if we just try to deal with possible ships one at a time? At this point, a skeptic might say: "But there are infinitely many possible values of `S` and also of `V`! How do you plan to deal with both of those infinities at the same time?". However, as it happens, it is entirely possible to do this! Imagine a two-dimensional grid, extending infinitely in all directions. Let some point on the grid be the very center, and call it `(0,0)`. All possible ships can be listed somewhere on this grid by their coordinates, with respect to the center. Now, imagine drawing a line on the grid, starting from `(0,0)`, and spiraling outwards (next could be `(0,1)`, then `(1,1)`, `(1,0)`, and so on). If you kept drawing forever, this line would cover all the coordinates on this graph. Furthermore, this line, like time, is one dimensional.

## Illustration here

As you may have realized, this line describes the order in which we will shoot at potential ships. Since the line will eventually cross over any given coordinate on the grid, all possible ships have been accounted for. At each specific point in time, we have chosen specific values of `S` and `V`, and therefore we know exactly where to shoot, using our position function above. Here is an example of how we apply the function here:

```
TIME: 0, SHIP: (0,0)
POSITION: 0 + (0 * 0) = 0

TIME: 1, SHIP: (0,1)
POSITION: 0 + (1 * 1) = 1

TIME: 2, SHIP: (1,1)
POSITION: 1 + (1 * 2) = 3

TIME: 3, SHIP: (1,0)
POSITION: 1 + (0 * 3) = 1

TIME: 4, SHIP: (1,-1)
POSITION: 1 + (-1 * 4) = -3

...
```

Thus, the sequence of places that we will shoot, in order, looks like this:

`[0, 1, 3, 1, -3, ...]`

What we have done here is described something called a [Bijection](https://en.wikipedia.org/wiki/Bijection) between the Natural numbers, and the set of all pairs of Integers.

Hopefully, I have been able to convince you that by following this procedure, no matter where the ship starts, and no matter where it's going, you will eventually be able to shoot it, and so the puzzle has been solved.

I hope you enjoyed this write up!
