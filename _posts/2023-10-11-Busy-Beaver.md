## Mathematical proof that Human-Augmented AI beats lone AI

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

We want to begin by translating this futuristic AI into a single Python 3.11.6 file without imports. This might sounds absurd, but the fact that one can do this is a consequence of the <a href="https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis">Church-Turing thesis</a>. Roughly speaking, even an extremely simple programming language like BASIC can solve the same kinds of problems that any other programming language can solve (but the code will be much longer). Even alternative models of computation such as quantum computing are only speed-ups - they can still be simulated by BASIC, but can take exponentially longer to run when they are.

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
