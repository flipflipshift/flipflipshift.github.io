## Mathematical proof that Human-Augmented AI beats lone AI

Once upon a time, it was believed that a computer could never beat the top players in chess. Today chess programs are so advanced that when they go head to head, assisstance from the top players is completely irrelevant.

As this <a href="https://www.businessinsider.com/computers-beating-humans-at-advanced-chess-2013-11">business insider</a> link posits, do all tasks have the same fate? Will we eventually get to the point where humans are completely unnecessary for all tasks?

I've seen many opinion posts arguing one way or another. It is probably true that for many tasks, AI will eventually be good enough to not require humans. But below I'll present a simple, well-defined problem, where human + AI will always outperform a lone AI.

This is not a real-world problem. But asking yourself _why_ a normal human is always able to add value in this problem is worth pondering.

---
# The Busy-Beaver problem (simplified)

Consider the following game between two players:

> Both players must write a python code in $1000$ or fewer characters that prints an integer. Whoever writes the program that prints the largest integer wins. Any runtime is allowed, as long as the program eventually stops. 

For the sake of making the problem timeless, let's force the python version to be Python 3.11.6 and forbid any import statements. There are a couple questions I'd like you to ponder:

1. Is there a "biggest possible integer" that a program following these rules can output?
2. If one player is allowed to see the other players' answer before submitting their own, are they guaranteed victory or is it possible that they can only tie?


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
