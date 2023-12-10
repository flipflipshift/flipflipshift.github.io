## Where Human-Augmented AI will always beat lone AI

Once upon a time, it was believed that a computer could never beat the top players in chess. Today chess programs are so advanced that when they go head to head, assisstance from the top players is completely irrelevant.

As this <a href="https://www.businessinsider.com/computers-beating-humans-at-advanced-chess-2013-11">business insider</a> link posits, do all tasks have the same fate? Will we eventually get to the point where humans are completely unnecessary for all tasks?

I've seen many opinion posts arguing one way or another. It is probably true that for many tasks, AI will eventually be good enough to not require humans. But below I'll present a simple, well-defined task where it seems that human + AI will always outperform a lone AI.

This is not a real-world relevant task. But it formalizes the intuition that a human should always be able to think "out of the box" that contains the AI's code. And perhaps this reasoning can be applied to some real-world task someday.

---
# Overview

Imagine that you and I were playing a game to see who could describe the bigger integer in 1,000 letters or fewer. You could try to squeeze the definition of <a href="https://en.wikipedia.org/wiki/Graham%27s_number">Graham's Number</a> in there, then make tweaks to make the number even bigger. But what if I were to answer:

"The largest integer that can be described in 1,000 letters or fewer plus one."

This is known as Berry's paradox - I've supplied a description for an integer in fewer than 1,000 letters that is supposedly larger than all integers that can be described in 1,000 letters or fewer. The resolution is that what counts as a "formal description for a number" is not actually well-defined (and can never really be defined in a way that allows unlimited abstraction). 

But the game becomes well-defined if we require our descriptions to be programs in a given language. You might think that you can write a short program that searches through all 1,000 character programs and outputs the one that prints the largest integer, but the <a href="https://en.wikipedia.org/wiki/Halting_problem">halting problem</a> illustrates the impossibility of identifying which programs print a (finite) integer.

Now imagine you were to play this game against an AI, and let's say the character limit was twice the length of the AI's code. Then your answer can be "get the AI's output when asked this question, execute that output program, and add 1", converted into code. Even if you have no idea how the AI works, as long as you can read its source code, you can beat it.

---
# The Busy-Beaver game

Consider the following game between two players:

**Both players must write a Python code in 1,000 characters that prints an integer. Whoever writes the program that prints the largest integer wins. Any runtime is allowed, as long as the program eventually stops.**

For the sake of making the problem timeless, let's force the Python version to be Python 3.11.6, forbid any import statements, and require all characters to be ones that you can see on a typical US-International Windows computer keyboard in the year 2023. We'll also assume that additional memory can always be provided when required. There are a couple questions I'd like you to ponder:

1. Is there a "biggest possible integer" that a program following these rules can output?
2. If you are allowed to "cheat" and see the other players' answer before submitting your own, are you guaranteed victory? Or is it possible that the best you can do is tie?

To answer the first, consider that there are only a finite number of acceptable characters. I don't know the precise number, but let's say it's fewer than 200. So using the <a href="https://en.wikipedia.org/wiki/Rule_of_product">rule of product</a> from combinatorics, there are fewer than ${200}^{1000}$ texts of 1,000 characters in existence. This is a huge number, but even so it is _finite_. And so the number of those texts which happen to also be Python codes is also finite.

Because there are only a finite number of programs in 1,000 characters that satisfy the rules, there must be a _maximum_ integer outputted by all these valid programs. This answers the second question - if your opponent happens to write down a program that spits out this maximum integer, the best you can do is to copy that program and end the game in a tie. So the answer to the second question is "no, it is possible that at best you can tie".

But even if the other player doesn't have the maximum integer, it can still be extremely difficult to improve their program. After all, that player will have likely combed through their program several times to look for any possible improvements before submission. You will have to find an improvement that they were unable to find.

We'll call this game $BB(1000)$, where 1000 indicates the allowed number of characters and $BB$ is a reference to the <a href="https://en.wikipedia.org/wiki/Busy_beaver">Busy Beaver problem</a>, upon which this game is based. We'll use $BB(n)$ to indicate the version of the game where $n$ characters are allowed.

Let's consider some futuristic hyper-intelligent AI that is specifically designed to play the game $BB(n)$. In other words, it accepts an integer $n$ as input and spits out a Python 3.11.6 program of length $n$ that prints a very large integer. We'll show that you + AI can outperform the lone AI in the game of $BB(n)$ when $n$ is very large.


---
# Playing against the AI

We want to begin by translating this futuristic AI into a single Python 3.11.6 file without imports. This might sounds absurd, but this is a consequence of the <a href="https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis">Church-Turing thesis</a>. Roughly speaking, even an extremely simple programming language like BASIC can solve the same kinds of problems that any other programming language can solve (but the code and run-time will be much longer). Even alternative models of computation such as quantum computing are only speed-ups - they can still be simulated by BASIC, but with impractical run-time. Moreover, we can write a program that does this conversion automatically so we won't have to actually understand the code of the AI.


We will asssume for now that randomness is not involved in the AI, then discuss below how to deal with randomness if it is involved.

When we do this translation, we will structure it in a manner such that key method is titled:
```Python
run(n):
 #return a string that is n characters long
 #string must be readable as a Python 3.11.6 code without imports that eventually stops and prints an integer.
```
Let $P$ denote this program and let $M$ denote the number of its characters. We now want to play the game $BB(2M)$ against this AI. Our strategy will be very simple. One option could be to run the AI on $2M$, waiting a long time for the program to finish, then carefully comb through the entire long program to find any small area of improvement that the AI didn't already catch. But if the AI is really good, it could be extremely hard to do so.

Our strategy will not involve actually running the AI on $2M$ at all. Or at least, not directly. Let's say that at the bottom of program $P$ we write:
```Python
x=2*M
exec(run(x))
print('0', end='')
```
(remember $M$ is the number of characters of $P$; it is not a variable).

The exec method in Python accepts a string as input and attempt to run it as Python code. So these last few lines will call 'run' on $2M$ (which returns the AI's best answer for the game of $BB(2M)$), then executes that actual program (which prints a very, very large integer), and finally appends a $0$ to the end, making the output 10 times larger than the output of the AI's program.

Including newlines, this adds $29+\log_{10}(M)$ characters to $P$, so the total program length is much less than $2M$. We can pad it out with spaces to make it exactly $2M$. Call this program $P'$.

$P'$ is a program with $2*M$ characters that eventually stops and outputs a number ten times larger than the program the AI suggested does. We've won.

---
# What if the AI is probabilistic?

One might be tempted to believe that the "random" nature of many current Machine Learning algorithms can come to the rescue here since we have not built randomness into the algorithm. Furthermore, while the limits have computation has not changed since the very beginning, the ability to generate "random-seeming" pseudo-random bits _has_ improved and should continues to improve in the future.

One option around this is to "try every possibility" of what the random calls can return, and print all the integers together. This is a deterministic solution that would significantly outperform the AI. But if the probability the AI takes more than $k$ steps to stop is greater than $0$ for all $k$, then we have a problem. Some might not consider such an AI legitimate as it can take arbitrarily long to spit it out anwswer, but in my view it should still be a legitimate player as long as the expectation and variance are finite. In this case, simulating all possible outputs would have infinite run-time and therefore be unacceptable.

A workaround for this would be to first simulate the AI 99 times, with a slight tweak in the code that prints the output of every call for a random bit. We can then have our (deterministic) python code simulate the AI 99 times in a manner that copies the performance by using the same random bits. This process will win against the AI at least 99% of the time.

To summarize: if the probabilistic AI has some upper-bound on its run-time, we can win 100% of the time without ever simulating the AI. Without that upper-bound, due to the AI having a potentially superior random function, we have to actually simulate the AI some number of times to copy its random behavior.

---
# Why can't an AI do this strategy?

While it can be possible for an AI to read its own code (see <a href="https://en.wikipedia.org/wiki/Quine_(computing)">Quines</a>), attempting to apply this technique would require the outputted program to recursively call itself ad infinitum without ever actually printing anything, and therefore be invalid. Any workaround is impossible as it would lead to a logical paradox akin to Berry's paradox.

Okay, but how about another AI? Can't it do this too? I've just given you an algorithm to beat the AI; of course an AI can follow this algorithm as well. But viewing that combination of two AI's as a single AI, you can again defeat it for large $N$ using the above trick.

What if we had a system of multiple AIs that were designed to build new AIs when they found themselves to be insufficient for the required $n$ and/or modify their own codes? It doesn't matter - you can _still_ defeat the whole system with this technique as all instructions for code-modification and AI construction are in the original AI's code.

# Caveats

One big assumption in all this is that the AI will give an acceptable answer when playing the game. If the AI does not spit out a valid Python program, then our strategy will fail as well and we are both disqualified.

So one might ask - "Can't we just check if the program the AI outputs is valid? And if it's not, then we can simply return 'print(1)' and win?". The trouble is that to do so, we would have to determine if the outputted program will run forever or stop, which is something that is <a href="https://en.wikipedia.org/wiki/Halting_problem">famously impossible to do in general</a>. 

To illustrate why this should be the case, let's say the AI spits out the following program:
```Python
def is_prime(k):
 if k<2:
  return False
 for i in range(2,k):
  if k%i==0:
   return False
 return True

n=4
found=False
while(not found):
 for i in range(n):
  if is_prime(i) and is_prime(n-i):
   found = True
   print(n, end='')
   break
 n+=2
```
This Python program prints the first counter-example to  <a href="https://en.wikipedia.org/wiki/Goldbach%27s_conjecture">Goldbach's conjecture</a>. To determine if it runs forever or not, you would need to resolve Goldbach's conjecture, which remains unanswered after nearly 300 years of effort by many of the top mathematicians in the world. 

So we must assume that the AI is designed to always provide a valid answer to the game, choosing to risk losing by writing an inferior program which it knows is valid rather than risk getting disqualified by returning a program it isn't sure is valid or not.

We are also assuming that the Church-Turing thesis holds in order to translate the AI into Python code. This thesis has not been proven because it is hard to even formalize the statement, but the majority opinion is that the thesis holds. In fact, I haven't been able to find anything even speculative about futuristic models of computation that don't satisfy the Church-Turing thesis. Even futuristic quantum computers satisfy the Church-Turing thesis (as mentioned above).

---
# Is this all an argument that we can never simulate the human brain?

Not completely - if it turns out that we _can_ upload your brain into a simulation, then one of the following is true:
1. There is a chance the brain spits out a "bad piece of code" which, for instance, never actually stops or
2. You + your simulated brain outperforms you alone.

I'm not much of a philosopher, so I won't speak much about point (2), but it's interesting to think about. There are probably some fun paradoxes you can come up with that involve pairing a human with a simulation of their brain.

The key takeaway here is that there exists at least one problem where you can strictly outperform any AI whose code you're able to see (even if the code itself is incomprehensible).


