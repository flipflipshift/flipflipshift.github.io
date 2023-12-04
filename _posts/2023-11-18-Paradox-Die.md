## A paradox in conditional expectation

Which of the following do you think is bigger?

$A$: The expected number of rolls of a fair die until you roll two $6\text{s}$ in a row, given that all rolls were even.

$B$: The expected number of rolls of a fair die until you roll the second $6$ (not necessarily in a row), given that all rolls were even.

If you are unfamiliar with conditional expectation, think of it this way: Imagine you were to perform a million sequences of die rolls, stopping each sequence when you roll two $6\text{s}$ in a row. Then you throw out all the sequences that contain an odd roll. The average number of rolls in the remaining sequences should be close to $A$. Next, perform a million sequences of die rolls, stopping each sequence when you roll the second $6$. Throw out all the sequences among these that contain an odd roll. The average number of rolls in the remaining sequence should be close to $B$.


I asked <a href="https://www.reddit.com/r/math/comments/17qcx8u/the_paradox_that_broke_me/">something like this</a> on r/math a couple weeks ago, and even with the hint that the answer was paradoxical, the early consensus was that $A$ must be larger. The justification was more or less the following: any time you roll until reaching 10 $6\text{s}$ in a row, you will have _also_ hit your 9th 6 before then. So regardless what the conditions are, $A$ must be larger than $B$.

But the correct answer is actually $B$. What on earth is going on?

---
# A quick verification

Before we proceed, let's write some code to estimate $A$ and $B$. The only goal here is to be as unambiguous as possible, so the code will be almost comically unoptimized in both run-time and length.

```Python
import random
def estimate_A(n):
  #Rolls 'n' sequences of die rolls, stopping each when two 6s in a row.
  #Tracks number of sequences with no odds
  #Tracks sum of number of rolls in all sequences with no odds
  #Computes average by dividing the two.
  num_sequences_without_odds=0
  sum_rolls_without_odds=0
  for i in range(n):
    counter=0
    last_roll_six=False
    has_odd=False

    #Roll until two sixes in a row
    while True:
      x=random.randint(1,6)
      counter+=1
      if x%2==1:
        has_odd=True
      
      if x==6:
        if last_roll_six:
          break
        last_roll_six=True
      else:
        last_roll_six=False

    if not has_odd:
      sum_rolls_without_odds+=counter
      num_sequences_without_odds+=1

  A_estimate=(sum_rolls_without_odds)/(num_sequences_without_odds)
  return A_estimate

def estimate_B(n):
  #Rolls 'n' sequences of die rolls, stopping each at second 6.
  #Tracks number of sequences with no odds
  #Tracks sum of number of rolls in all sequences with no odds
  #Computes average by dividing the two.
  num_sequences_without_odds=0
  sum_rolls_without_odds=0
  for i in range(n):
    counter=0
    six_count=0
    has_odd=False

    #Roll until second 6
    while True:
      x=random.randint(1,6)
      counter+=1
      if x%2==1:
        has_odd=True
      if x==6:
        six_count+=1
      if six_count==2:
        break
    
    if not has_odd:
      sum_rolls_without_odds+=counter
      num_sequences_without_odds+=1

  B_estimate=(sum_rolls_without_odds)/(num_sequences_without_odds)
  return B_estimate

print("Estimate for A: " + "{0:0.3f}".format(estimate_A(100000),2))
print("Estimate for B: " + "{0:0.3f}".format(estimate_B(100000),2))

```
The 100,000s in the last two lines represent the number of sequences being rolled for the estimate; you can add zeros for accuracy or subtract zeros for faster run-time.

The estimate for $A$ should be close to $2.727$ and the estimate for B should be close to $3.000$. We will later see that the exact value for $A$ is $30/11$ and the exact value for $B$ is $3$, but it is helpful to first unambiguously verify that $B$ is greater than $A$ (and ensure that we are on the same page of what $A$ and $B$ mean) before diving into the possibly unintuitive math.

---
# Primer on geometric distributions

We'll begin by calculating the expected number of rolls of a die to see the first $6$. If you know how to quickly solve this problem, you can skip this subsection.

To do so, we first find the probability that it takes exactly $k$ rolls to see the first $6$. This means the first $k-1$ rolls were not $6$ and roll $k$ was $6$. 

The probability that a die rolls a $6$ is $\frac{1}{6}$ and the probability it does not is $\frac{5}{6}$. Following the <a href="https://brilliant.org/wiki/probability-rule-of-product/">rule of product</a> for independent probabilities, we get:

$\text{Pr(First $6$ on roll $k$)}=\left(\frac{5}{6}\right)^{k-1}\frac{1}{6}$

We can now get a formula for the expected number of rolls of a die until we see the first $6$. The <a href="https://online.stat.psu.edu/stat500/lesson/3/3.2/3.2.1">formula for expectation</a> gives:

$E[\text{# rolls until 6}]=\sum\limits_{k=1}^\infty k*\text{Pr(First $6$ on roll $k$)}=\sum\limits_{k=1}^\infty k\left(\frac{5}{6}\right)^{k-1}\frac{1}{6}$

Now we'll use the following fact: for $-1<x<1$:

$\sum\limits_{k=1}^\infty k x^{k-1}=\frac{1}{(1-x)^2}$

This can be obtained by starting with the <a href="https://www.khanacademy.org/math/ap-calculus-bc/bc-series-new/bc-10-2/a/proof-of-infinite-geometric-series-formula">formula for geometric series</a> $\sum\limits_{k=0}^\infty x^{k}=\frac{1}{1-x}$ and taking the derivative of both sides (if you remember calculus) or squaring both sides (if you're very good at algebra).

Plugging in, we have 
$E[\text{# rolls until 6}]=\frac{1}{6}\frac{1}{(1-\frac{5}{6})^2}=6$. 
And we are done. Sort of.

Let's try that again, this time using an intuitive trick from Markov chains. We'll use "average" and "expected" interchangably as the former is more colloquial and we are going to be a bit informal here.

Let $x$ be the average number of rolls until we see the first $6$. Let's roll the die once. With probability $\frac{1}{6}$, we rolled a $6$ and can stop. With probability 
$\frac{5}{6}$, we didn't roll a $6$ and are then _still_ an average of $x$ steps away from a $6$. 

So with probability $1/6$, we are in a scenario where we take $1$ roll to see a $6$ and in the remaining probability $5/6$, it will take an average of $x+1$ steps to see a $6$. So the average number of rolls until we see the first $6$ is $\frac{1}{6}(1)+\frac{5}{6}(x+1)$. But the average is also $x$! This gives us the algebra equation:

$x=\frac{1}{6}+\frac{5}{6}(x+1)$

that gives $x=6$ when solving for $x$.

Let's generalize now. Let's say we have some experiment that has fixed probability $p$ of success. We repeat the experiment until it succeeds. Then if $x$ is the 
expected number of trials until success, we have:

$x=p(1)+(1-p)(x+1)\implies x=p+x-px+1-p \implies px=1\implies x=1/p$.

Probability distributions of this form are called _geometric distributions_. 

In this case, the experiment was rolling a die and success was defined by seeing a $6$, so it is a _geometric distribution with success rate_ $\frac{1}{6}$. And so the expected number of trials until sucess is $\frac{1}{\frac{1}{6}}=6$.

---
# Rolls until first 6, given all even

In this section, we will use $D6$ to refer to a fair $6$-sided die with sides labeled $1-6$ and $D3$ to refer to a fair $3$-sided die with sides labeled $2,4,6$. Consider the following two questions:

1. What is the expected number of rolls of a $D3$ until we see the first $6$?
2. What is the expected number of rolls of a $D6$ until we see the first $6$, given that all rolls are even?

For the first question, we have a geometric distribution of success rate $\frac{1}{3}$, so the expected number of trials until success is $\frac{1}{\frac{1}{3}}=3$.

The second question was posed by Gil Kalai in a 2017 <a href="https://gilkalai.wordpress.com/2017/09/07/tyi-30-expected-number-of-dice-throws/">blog post</a>. Most people incorrectly answered 3 (and keep in mind the audience for this blog is fairly math literate). The rationale was that the second question seems equivalent to the first. But let's calculate it explicitly.

Analogously to last section, we begin by calculating the probability that it takes exactly $k$ rolls to see the first $6$, given that all rolls were even. Following standard conventions, we'll write ${\rm Pr}(A|B)$ as shorthand for "Probability that $A$ occurs, given that $B$ occurs$. From the <a href="https://en.wikipedia.org/wiki/Conditional_probability">formula for conditional probability</a>, we have:

${\rm Pr}(A|B)=\frac{{\rm Pr}(A\text{ and }B)}{{\rm Pr}(B)}$

Let's start with the numerator. If it takes us exactly $k$ rolls to see our first $6$ _and_ all rolls in the process were even, than the first $k-1$ rolls were all $2$ or $4$ and the $k\text{th}$ roll was a $6$. The probability of this occuring is $\left(\frac{2}{6}\right)^{k-1}\left(\frac{1}{6}\right)$

The denominator is the total probability that we roll a $6$ before the first odd. One way we could determine this is by evaluating 
$\sum\limits_{i=1}^\infty \left(\frac{2}{6}\right)^{i-1}\left(\frac{1}{6}\right)$ (that is, summing the probability it takes exactly $i$ rolls to get a $6$ and all rolls were even, over all possible values of $i$). We saw how to some those kinds of series in the last section.

But a more intuitive way is as follows - rephrase "Probability we roll a $6$ before the first odd$ as "Probability that between the sides {1,3,5,6}, 6 is the first to show up". From here, we can immediately see by symmetry that the probability is $\frac{1}{4}$. Indeed summing the above series gives the same answer.

Altogether, we have:

${\rm Pr}(\text{First $6$ on roll $k$} | \text{all rolls even})=\frac{\left(\frac{2}{6}\right)^{k-1}\left(\frac{1}{6}\right)}{1/4}=\frac{3}{2}\left(\frac{1}{3}\right)^{k-1}$

and so:

$E[\text{rolls until first 6}| \text{ all rolls even}]=\sum\limits_{k=1}^\infty k \frac{3}{2}\left(\frac{1}{3}\right)^{k-1}=\frac{3}{2}\left(\frac{1}{(1-\frac{2}{3})^2}=\frac{3}{2}$


---
# Rolls until second 6, given all even


