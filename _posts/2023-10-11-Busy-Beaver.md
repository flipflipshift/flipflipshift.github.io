## Mathematical proof that Human-Augmented AI beats lone AI

Once upon a time, it was believed that a computer could never beat the top players in chess. Today chess programs are so advanced that when they go head to head, assisstance from the top players is completely irrelevant.

As this <a href="https://www.businessinsider.com/computers-beating-humans-at-advanced-chess-2013-11">business insider</a> link posits, do all tasks have the same fate? Will we eventually get to the point where humans are completely unnecessary for all tasks?

I've seen many opinion posts arguing one way or another. It is probably true that for many tasks, AI will eventually be good enough to not require humans. But below I'll present a simple, well-defined problem, where human + AI will always outperform a lone AI.

This is not a real-world problem. But asking yourself _why_ a normal human is always able to add value in this problem is worth pondering.

---
# The Busy-Beaver game

Consider the following game between two players:

**Both players must write a python code in 1000 characters that prints an integer. Whoever writes the program that prints the largest integer wins. Any runtime is allowed, as long as the program eventually stops.**

For the sake of making the problem timeless, let's force the python version to be Python 3.11.6 and forbid any import statements. We'll also assume that additional memory can always be provided when required. There are a couple questions I'd like you to ponder:

1. Is there a "biggest possible integer" that a program following these rules can output?
2. If you are allowed to "cheat" and see the other players' answer before submitting your own, are you guaranteed victory? Or is it possible that the best you can do is tie?

To answer the first, consider that there are only a finite number of acceptable characters that can be used in python code. I don't know the precise number, but it's certainly fewer than 200. So using the <a href="https://en.wikipedia.org/wiki/Rule_of_product">rule of product</a> from combinatorics, there are fewer than ${200}^{1000}$ texts of 1000 characters in existence. This is a huge number, but even so it is _finite_. And so the number of those texts which happen to also be python codes is also finite.

Because there are only a finite number of programs in $1000$ characters that satisfy the rules, there must be a _maximum_ integer outputted by all these valid programs. This answers the second question - if your opponent happens to write down a program that spits out this maximum integer, the best you can do is to copy that program and end the game in a tie.

But even if the other player doesn't have the maximum integer, it can still be extremely difficult to improve their program. After all, that player will have likely combed through their program several times to look for any possible improvements before submission. You will have to find an improvement that they were unable to find.

We'll call this game $BB(1000)$, where $1000$ indicates the allowed number of characters and $BB$ is a reference to the <a href="https://en.wikipedia.org/wiki/Busy_beaver">Busy Beaver problem</a>, upon which this game is based.

Let's consider some futuristic hyper-intelligent AGI (artifical general intelligence) that accept text prompts as input and spits out text as output. We'll see why you + AGI can outperform the lone AGI in the game of $BB(N)$ for very large $N$. 

---
# Playing against the AGI

We want to begin by translating this futuristic AGI into a single Python 3.11.6 file without imports. This might sounds absurd, but the fact that one can do this is a consequence of the <a href="https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis">Church-Turing thesis</a>. Roughly speaking, even an extremely simple programming language like BASIC can solve the same kinds of problems that any other programming language can solve (but the code will probably be way longer and take much longer to run!) Even alternative models of computation such as quantum computing are only speed-ups - they can still be simulated by BASIC with a much longer run time!

Let $M$ be the number characters that our translation of this AGI into python code involves. $M$ will of course be extremely large.

We now want to play the game $BB(2M)$ against this AGI. If this AGI really is as intelligent as we expect, 

We now 

any programming languages that contains some simple things like loops and recursion can do any computation that any other programming language, modern or futuristic, can achieve. It just

The  is a conjecture that argues that all sufficiently advanced programming langauges can solve the exact same set of mathematical, well-defined problems. From BASIC to modern Python, all program languages you have ever used 

In other words, any 


states that a language like BASIC can solve any problem that any future programming



First of all, no matter what our technological advances are, 





### This is a header

#### Some T-SQL Code

```tsql
SELECT This, [Is], A, Code, Block -- Using SSMS style syntax highlighting
    , REVERSE('abc')
FROM dbo.SomeTable s
    CROSS JOIN dbo.OtherTable o;
```

#### Some PowerShell Code

```powershell
Write-Host "This is a powershell Code block";

# There are many other languages you can use, but the style has to be loaded first

ForEach ($thing in $things) {
    Write-Output "It highlights it using the GitHub style"
}
```
