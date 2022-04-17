---
title: Fetlang is turing complete
date: 2017-10-11 00:00:00 +0000
categories: [computer science, quick build]
tags: [humor, computer science, turing complete]
---

Recently, the esoteric programming language Fetlang was linked and discussed
on /r/programming. On the
[project's Github page](https://github.com/fetlang/fetlang) I noticed something
in its list of features:

> Probably Turing complete

is what it used to say. I decided to do something about that and try to prove
its Turing completeness. Initially I wanted to do Conways Game of Life,
always a fun project, but Fetlang's dense and unwieldy syntax made me drop down
to easy mode: Rule 110, a much simpler Turing complete cellular automaton. The
whole project took me around five hours, most of which were spent trying to
figure out how to handle the two only data types. I'll run you trough my
process, which should also help you a lot if you ever decide you need to write
erotica while at work. Note that this overview is entirely functional and
ignores the many unique features of Fetlang which enables you make your code
more prosaic, such as possessives and pronouns.

## Understanding Fetlang
### Understanding the Data Types

Fetlang has but two data types: Fractions and Chains. Fractions are what they say: Rational, non-float numbers. If they happen to have an ASCII-value, they can be printed to screen. Chains are linked lists of fractions and can be initialized by string literals. Fractions cannot be initialized by normal integer literals - rather, you have to spell out the numbers. Types are not changeable after initialization.

#### Initializing, setting and resetting

The following code will show you how to initialize variables

```
( Parens make comments )

( Erik is a fraction variable. The following code assumes "standalone" lines, i.e. a previously uninitialized variable )
Worship Erik ( Sets Erik to 0. Worship multiplies, by 1 if no second argument is given. Erik is not initialized, but from context, must be a fraction, set to 0 by default)
Lick Erik ( Sets Erik to 1. Lick adds, adding 1 if no second argument is given. Since Erik defaults to 0, he is licked once, hence Erik is 1)
Lick Erik forty five times ( Sets Erik to 45 )

( And if Erik is already set and needs to be reset )
Have Erik spank Erik ( Resets Erik to 0, by spanking (subtracting) him from himself )

( Tiffany is a Chain Variable )
Make Tiffany moan ( Declares Tiffany to be an empty chain. Can also be used to reset, if already set )
Make Tiffany moan "Wow, that's nice" ( String literals work. Concatenating is not directly possible )
```

So far, so easy.

### Control Flow

#### Comparisions

Comparing numbers is easy enough:

```
(Set Anna to 1 and Betty to 2)
Lick Anna
Lick Betty two times

( Anna > Betty )
If Anna is dominant towards Betty
( Anna == Betty )
If Anna is Betty
( Anna < Betty )
If Anna is submissive to Betty
( Anna != Betty )
If Anna is not Betty
```

However, there is no direct way to compare chains, and figuring out a correct way to do this was the hardest part for me.

#### The Bind Operator ("Bondage Loops")

A bondage loop allows access to all values inside a chain. It is, in essence, a "for-in" loop.

```
Make Anna moan "Array"

Bind Emma to Anna
  Make slave scream Emma

( Prints "A", "r", "r", "a", "y" on individual lines )
```

The important and neat thing here is that Emma is not a chain of length 1, but a fraction! This enables us to do many useful things.

There are also while and until loops, which are the most self-explanatory things in this language.

#### Checking for a specific ASCII-value

This is one more tool we're going to need to write somewhat understandable code. Let's try some things:

```
Make Anna moan "A"
Lick Betty sixty five times

(Print "A" two times)
Make Slave scream Anna
Make Slave scream Betty

(So, let's try...)

x If Anna is "A" (Can't use string literals in comparison)
x If Anna is Betty (Incompatible types)
x If Anna is sixty five (Can't use number literals either, also wrong types)

(So, uhmm....)

Bind Emma to Anna
  (Anna is a string literal, but Emma is not - she is a fraction!)
  If Emma is Betty ( This works! )
```

Neat.

### Iterating through a chain and assigning conditional values

So, now we can put these tools together to go from an input string to a series of conditional reactions, based on individual characters:

```
( This program detects if a string contains two zeroes in a row )
Make Anna moan "11101010100"
Lick AsciiZero forty eight times
Worship ZeroesInARow
Lick limit two times

Bind Emma to Anna
  If Emma is AsciiZero
    Lick ZeroesInARow
    If ZeroesInARow is limit
      Make Slave Scream "00 detected!"
  Otherwise
    Have ZeroesInARow Spank ZeroesInARow
```

And with this, there is just one more construct to understand for my proof code to become obvious


### Adding to a Chain

Adding to a Chain/String can be done with the 'hogtie'-operator. Here, a fraction has to hogtie a chain, writing the byte on its end.

```
( Generates the String "abcd" and prints it )
Make Anna moan
Lick char ninety seven times ( Set to "a" )
Worship counter
Lick loopmax four times

While counter is submissive to loopmax
  Lick counter
  Have char hogtie Anna
  Lick char

Make slave scream Anna
```

And that's it. We've now fully learned to juggle the data types and can begin writing the actual program - which is now fairly easy.

## Understanding the Turing completeness proof

Disclaimer: I'm 99% sure my code can be simplified or improved. But I don't care.

To understand the steps the program takes, it's probably best to look at my python generator code, which I wrote after completing the first version of the proof to expand the size of the Rule 110 board. Steps are:

```python
def main():
    generateIntro()             # Initialize required variables
    generateNumbers()           # Initialize all required numbers, since you can't use literals in control flow
                                # In the python code, this is inefficient, since I was too lazy to figure out how to spell the numbers, so I have many, many lines of licking
    beginLoop()                 # Literally just the while loop and condition
    resetTempVariables()        # Does what it says
    setEmptyPositions()         # That's also resetting, come to think of it
    incrementLoopCounter()      # Again, obvious
    printCurrentState()         # You probably get the idea
    getParentStrings()
    getDescendants()
    writeNextGeneration()       # Print the next generation
    saveNextGeneration()        # Save the next generation as current via bondage loop
```
Okay, so everything I've commented is pretty much boring cruft - setting and resetting values, mostly. Let's look at the other functions:

### getParentStrings()

Here we loop through the current state of the automaton (via bondage loop + iteration counter), doing this for all but the outermost positions:

```
If counter is submissive to number4
  If counter is dominant towards number0
    Have Emma hogtie position2
```

This just builds a 3-character-Chain for each position, by taking its upper left, upper middle and upper right neighbor and saving it.

### getDescendants()

This function requires a lot more code. First, we explicitly write the values of left, middle and right:

```
Bind Emma to position2
  if counter is number0
    if Emma is AsciiZero
      Have AsciiZero lick left
    Otherwise
      Have AsciiOne lick left

  if counter is number1
    if Emma is AsciiZero
      Have AsciiZero lick middle
    Otherwise
      Have AsciiOne lick middle

  if counter is number2
    if Emma is AsciiZero
      Have AsciiZero lick right
    Otherwise
      Have AsciiOne lick right

  Lick counter
```
Left, middle, right and counter are of course reset before each position is calculated.

Now we have three accessible variables for the current generation. A short if-tree gives us the calculated value for the next one:


```
if left is AsciiOne
  if middle is AsciiOne
    if right is AsciiZero
      Have AsciiOne hogtie nextposition2
    otherwise
      have AsciiZero hogtie nextposition2
  if middle is AsciiZero
    if right is AsciiOne
      have AsciiOne hogtie nextposition2
    otherwise
      have AsciiZero hogtie nextposition2
if left is AsciiZero
  if middle is AsciiOne
    Have AsciiOne hogtie nextposition2
  otherwise
    if right is AsciiOne
      Have AsciiOne hogtie nextposition2
    otherwise
      have AsciiZero hogtie nextposition2
```

And this is the entire trick. Rule 110 is incredibly simple, and thus very nice for these proofs.

I really enjoyed figuring all this out. If you want to understand my proof in more detail,
check out the code on [Github](https://github.com/KjeldSchmidt/Fetlang_110) or just ask.

The Fetlang repository no longer says that it is probably turing complete - they are sure now.
