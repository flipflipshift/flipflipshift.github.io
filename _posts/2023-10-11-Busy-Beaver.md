## Where Human-Augmented AI will always beat lone AI

Once upon a time, it was believed that a computer could never beat the top players in chess. Today chess programs are so advanced that when they go head to head, assisstance from the top players is completely irrelevant.

As this <a href="https://www.businessinsider.com/computers-beating-humans-at-advanced-chess-2013-11">business insider</a> link posits, do all tasks have the same fate? Will we eventually get to the point where humans are completely unnecessary for all tasks?

I've seen many opinion posts arguing one way or another. It is probably true that for many tasks, AI will eventually be good enough to not require humans. But below I'll present a simple, well-defined task where human + AI will always outperform a lone AI.

This is not a real-world relevant task. But asking yourself _why_ a normal human is always able to add value in this problem is worth pondering.

---
# The Busy-Beaver game

Consider the following game between two players:

**Both players must write a python code in 1000 characters that prints an integer. Whoever writes the program that prints the largest integer wins. Any runtime is allowed, as long as the program eventually stops.**

For the sake of making the problem timeless, let's force the python version to be Python 3.11.6 and forbid any import statements. We'll also assume that additional memory can always be provided when required. There are a couple questions I'd like you to ponder:

1. Is there a "biggest possible integer" that a program following these rules can output?
2. If you are allowed to "cheat" and see the other players' answer before submitting your own, are you guaranteed victory? Or is it possible that the best you can do is tie?

To answer the first, consider that there are only a finite number of acceptable characters that can be used in python code. I don't know the precise number, but let's say it's fewer than 200. So using the <a href="https://en.wikipedia.org/wiki/Rule_of_product">rule of product</a> from combinatorics, there are fewer than ${200}^{1000}$ texts of 1000 characters in existence. This is a huge number, but even so it is _finite_. And so the number of those texts which happen to also be python codes is also finite.

Because there are only a finite number of programs in $1000$ characters that satisfy the rules, there must be a _maximum_ integer outputted by all these valid programs. This answers the second question - if your opponent happens to write down a program that spits out this maximum integer, the best you can do is to copy that program and end the game in a tie.

But even if the other player doesn't have the maximum integer, it can still be extremely difficult to improve their program. After all, that player will have likely combed through their program several times to look for any possible improvements before submission. You will have to find an improvement that they were unable to find.

We'll call this game $BB(1000)$, where $1000$ indicates the allowed number of characters and $BB$ is a reference to the <a href="https://en.wikipedia.org/wiki/Busy_beaver">Busy Beaver problem</a>, upon which this game is based. We'll use $BB(N)$ to indicate the version of the game where $N$ characters are allowed.

Let's consider some futuristic hyper-intelligent AI that is specifically designed to play the game $BB(n)$. In other words, it accepts an integer $n$ as input and spits out a Python 3.11.6 program of length $n$ that prints a very large integer. We'll show that you + AI can outperform the lone AI in the game of 
$BB(N)$ when $N$ is very large.


---
# Playing against the AI

We want to begin by translating this futuristic AI into a single Python 3.11.6 file without imports. This might sounds absurd, but the fact that one can do this is a consequence of the <a href="https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis">Church-Turing thesis</a>. Roughly speaking, even an extremely simple programming language like BASIC can solve the same kinds of problems that any other programming language can solve (but the code will be much longer). Even alternative models of computation such as quantum computing are only speed-ups - they can still be simulated by BASIC, but can take exponentially longer to run when they are. We will asssume for now that randomness is not involved in the AI, then discuss below how to deal with randomness if it is involved.

When we do this translation, we will structure it in a manner such that key method is titled:
```python
run(n):
 #return a string that is n characters long and can be read a python 3.11.6 without imports, which itself eventually stops and prints a very large integer.
```
Let $P$ denote this program and let $M$ denote the number of its characters. We now want to play the game $BB(2M)$ against this AI. Our strategy will be very simple. One option could be to run the AI on $2M$, waiting a long time for the program to finish, then carefully comb through the entire long program to find any small area of improvement that the AI didn't already catch. But if the AI is really good, it could be extremely hard to do so.

Our strategy will not involve actually running the AI on $2M$ at all. Or at least, not directly. Let's say that at the bottom of program $P$ we write:
```python
x=2*M
exec(run(x))
print("0")
```
(remember $M$ is the number of characters of $P$; it is not a variable).

The exec method in Python accepts a string as input and attempt to run it as python code. So these last few lines will call 'run' on $2M$ (which returns the AI's best answer for the game of $BB(2M)$), then executes that actual program (which prints a very, very large integer), and finally appends a $0$ to the end, making the output 10 times larger than the output of the AI's program.

Including newlines, this adds $29+\log_{10}(M)$ characters to $P$, so the total program length is much less than $2M$. We can pad it out with meaningless code to make it exactly $2M$. Call this program $P'$.

$P'$ is a program with $2*M$ characters that eventually stops and outputs a number ten times larger than the program the AI suggested does. We've won.

---
# What if the AI is probabilistic?

One might be tempted to believe that the "random" nature of many current Machine Learning algorithms can come to the rescue here since we have not built randomness into the algorithm. We can easily work around this by having the program print all possible outputs of the AI on input $2M$ (viewed as a single, long, integer). All we have to do is know the maximum amount of random information that will be required, and we can have our program loop through every possibility. Note that this does not impact how long it takes for us to give our answer to the $BB(2M)$ game - we just have to write the program, not run it.

---
# Why can't the AI do this strategy?

This is a good exercise, and it also proves that the AI will never be perfect at this game for large integers. Informally, this would require the program to recursively call itself ad infinitum, and you can prove via a proof by contradiction that there can never be a workaround for this.

---
# Okay, but how about another AI? Can't it do this?

Forget AI, even a basic program can do this, right? I've just given you an algorithm to beat the AI; of course a program can follow this algorithm as well. But viewing that combination of AI + program as a single AI, you can again defeat it for large $N$ using the above trick.

---
# Okay, but what if modify the AI to do something like this strategy whenever the input is bigger than its length?

No matter how "meta" the AI playing this game is, you can always "out-meta" the strategy by one level with the exact same trick.

---
# Caveats

One big assumption in all this is that the AI will give an acceptable answer when playing the game. If the AI does not spit out a valid Python program, then our strategy will fail as well and we are both disqualified.

So one might ask - "Can't we just check if the program the AI outputs is valid? And if it's not, then we can simply return 'print(1)' and win?". The trouble is that to do so, we would have to determine if the outputted program will run forever or stop, which is something that is <a href="[https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis](https://en.wikipedia.org/wiki/Halting_problem)https://en.wikipedia.org/wiki/Halting_problem">famously impossible to do in general</a>. In fact, you can see why with this very problem - if a program could answer the Halting problem, then it could give a best answer to $BB(N)$ (try to see why yourself), which contradicts our ability to always do better.

---
# Is this all an argument that we can never simulate the human brain?

Not completely - if it turns out that we _can_ upload your brain into a simulation, then one of the following is true:
1. There is a chance the brain spits out a "bad piece of code" which, for instance, never actually stops or
2. You + your simulated brain outperforms you alone.

I'm not much of a philosopher, so I won't speak much about point (2), but it's interesting to think about. There are probably some fun paradoxes you can come up with that involve pairing a human with a simulation of their brain.

The key takeaway here is that there exists at least one problem where you can strictly outperform any AI whose code you're able to see (even if the code itself is incomprehensible).


