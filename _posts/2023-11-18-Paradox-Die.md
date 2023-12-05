## A paradox in conditional expectation

Which of the following do you think is bigger?

$A$: The expected number of rolls of a fair die until you roll two $6\text{s}$ in a row, given that all rolls were even.

$B$: The expected number of rolls of a fair die until you roll the second $6$ (not necessarily in a row), given that all rolls were even.

If you are unfamiliar with conditional expectation, think of it this way: Imagine you were to perform a million sequences of die rolls, stopping each sequence when you roll two $6\text{s}$ in a row. Then you throw out all the sequences that contain an odd roll. The average number of rolls in the remaining sequences should be close to $A$. Next, perform a million sequences of die rolls, stopping each sequence when you roll the second $6$. Throw out all the sequences among these that contain an odd roll. The average number of rolls in the remaining sequence should be close to $B$.


I asked <a href="https://www.reddit.com/r/math/comments/17qcx8u/the_paradox_that_broke_me/">something like this</a> on r/math a couple weeks ago, and even with the hint that the answer was paradoxical, the early consensus was that $A$ must be larger. The justification was more or less the following: any time you roll until reaching two $6\text{s}$ in a row, you will have _also_ hit your second 6 at or before then. So regardless what the conditions are, $A$ must be larger than $B$.

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

The estimate for $A$ should be close to $2.727$ and the estimate for B should be close to $3.000$. The exact value for $A$ is $30/11$ and the exact value for $B$ is $3$, but it is helpful to first unambiguously experimentally verify that $B$ is greater than $A$ (and ensure that we are on the same page of what $A$ and $B$ mean) before diving into the possibly unintuitive math.

---
# Primer on geometric distributions

We'll begin by calculating the expected number of rolls of a die to see the first $6$. If you know how to quickly solve this problem, you can skip this subsection.

To do so, we first find the probability that it takes exactly $k$ rolls to see the first $6$. This means the first $k-1$ rolls were not $6$ and roll $k$ was $6$. 

The probability that a die rolls a $6$ is $\frac{1}{6}$ and the probability it does not is $\frac{5}{6}$. Following the <a href="https://brilliant.org/wiki/probability-rule-of-product/">rule of product</a> for independent probabilities, we get:

$\text{Pr(First $6$ on roll $k$)}=\left(\frac{5}{6}\right)^{k-1}\frac{1}{6}$

We can now get a formula for the expected number of rolls of a die until we see the first $6$. The <a href="https://online.stat.psu.edu/stat500/lesson/3/3.2/3.2.1">formula for expectation</a> gives:

$E[\text{num rolls until 6}]=\sum\limits_{k=1}^\infty k*\text{Pr(First $6$ on roll $k$)}=\sum\limits_{k=1}^\infty k\left(\frac{5}{6}\right)^{k-1}\frac{1}{6}$

Now we'll use the following fact: for $-1<x<1$:

$\sum\limits_{k=1}^\infty k x^{k-1}=\frac{1}{(1-x)^2}$

This can be obtained by starting with the <a href="https://www.khanacademy.org/math/ap-calculus-bc/bc-series-new/bc-10-2/a/proof-of-infinite-geometric-series-formula">formula for geometric series</a> $\sum\limits_{k=0}^\infty x^{k}=\frac{1}{1-x}$ and taking the derivative of both sides (if you remember calculus) or squaring both sides (if you're very good at algebra).

Plugging in, we have 
$E[\text{num rolls until 6}]=\frac{1}{6}\frac{1}{(1-\frac{5}{6})^2}=6$. 
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

In this case, the experiment was rolling a die and success was defined by seeing a $6$, so it is a _geometric distribution with success rate_ $\frac{1}{6}$. And so the expected number of trials until sucess is $\frac{1}{1/6}=6$.

---
# Rolls until first 6, given all even

In this section, we will use $D6$ to refer to a fair $6$-sided die with sides labeled $1-6$ and $D3$ to refer to a fair $3$-sided die with sides labeled $2,4,6$. Consider the following two questions:

1. What is the expected number of rolls of a $D3$ until we see the first $6$?
2. What is the expected number of rolls of a $D6$ until we see the first $6$, given that all rolls are even?

For the first question, we have a geometric distribution of success rate $\frac{1}{3}$, so the expected number of trials until success is $\frac{1}{1/3}=3$.

The second question was posed by Gil Kalai in a 2017 <a href="https://gilkalai.wordpress.com/2017/09/07/tyi-30-expected-number-of-dice-throws/">blog post</a>. Most people incorrectly answered 3 (and keep in mind the audience for this blog is fairly math literate). The rationale was that the second question seems equivalent to the first. But let's calculate it explicitly.

Analogously to last section, we begin by calculating the probability that it takes exactly $k$ rolls to see the first $6$, given that all rolls were even. Following standard conventions, we'll write $\text{Pr}(X\mid Y)$ as shorthand for "Probability that $X$ occurs, given that $Y$ occurs".
Analogously to last section, we begin by calculating the probability that it takes exactly $k$ rolls to see the first $6$, given that all rolls were even. Following standard conventions, we'll write ${\rm Pr}(X\mid Y)$ as shorthand for "Probability that $X$ occurs, given that $Y$ occurs. From the <a href="https://en.wikipedia.org/wiki/Conditional_probability">formula for conditional probability</a>, we have:

$\text{Pr}(X\mid Y)=\frac{\text{Pr}(X\text{and }Y)}{\text{Pr}(Y)}$

Let's start with the numerator. If it takes us exactly $k$ rolls to see our first $6$ _and_ all rolls in the process were even, than the first $k-1$ rolls were all $2$ or $4$ and the $k\text{th}$ roll was a $6$. The probability of this occuring is $\left(\frac{2}{6}\right)^{k-1}\left(\frac{1}{6}\right)$

The denominator is the total probability that we roll a $6$ before the first odd. One way we could determine this is by evaluating 
$\sum\limits_{i=1}^\infty \left(\frac{2}{6}\right)^{i-1}\left(\frac{1}{6}\right)$ (that is, summing the probability it takes exactly $i$ rolls to get a $6$ and all rolls were even, over all possible values of $i$). We saw how to some those kinds of series in the last section.

But a more intuitive way is as follows - rephrase "Probability we roll a $6$ before the first odd" as "Probability that between the sides $\{1,3,5,6\}$, 6 is the first to show up". From here, we can immediately see by symmetry that the probability is $\frac{1}{4}$. Indeed summing the above series gives the same answer.

Altogether, we have:

$\text{Pr}(\text{First $6$ on roll $k$} \mid  \text{all rolls even})=\frac{\left(\frac{2}{6}\right)^{k-1}\left(\frac{1}{6}\right)}{1/4}=\frac{3}{2}\left(\frac{1}{3}\right)^{k-1}$

and so:

$E[\text{rolls until first 6}\mid  \text{all rolls even}]=\sum\limits_{k=1}^\infty k \frac{3}{2}\left(\frac{1}{3}\right)^{k-1}=\frac{3}{2}\left(\frac{1}{1-\frac{2}{3}}\right)^2=\frac{3}{2}$

There is another, cuter, way to answer the second problem that will be important for our evaluation of $B$. We will first rephrase the question as "What is 
the expected number of rolls of a $D6$ until the first $6$, given that $6$ is the first to occur out of ${1,3,5,6}$?". We can rephrase this again as "What is the expected number of rolls of a $D6$ until the first side in ${1,3,5,6}$ shows up, given that $6$ is the first to occur out of ${1,3,5,6}$?".

Now we have some neat symmetry - the expected number of rolls of a $D6$ until a the first side in ${1,3,5,6}$ shows up shouldn't depend on which of those four sides happened to be first. So the following will have the same answer as the second question: "What is the expected number of rolls of a die until the first side in ${1,3,5,6}$ shows up?"

That's a geometric distribution with success rate $\frac{4}{6}$! And so its expectation is $\frac{1}{4/6}=\frac{3}{2}$.


---
# Adding conditions after the first 6

We'll now make a modification that might seem harmless. We will roll a $D6$ one billion times (and keep going until we get a $6$ if we somehow didn't get it in the first 1 billion rolls). What is the expected number of rolls until the first 6 given that _every roll that ever showed up in this process_ is even.

In other words, we are still looking at the number of rolls until the first 6, and we are still requiring that all rolls before the first 6 are even. But now we are also requiring that rolls _after_ the first 6 are even. Does this impact the expectation? Intuitively one might think "no", but we'll see that the expectation nearly doubles.

$E[\text{number of rolls}\mid \text{evens up to 6 and billionth roll}]=\sum\limits_{k=1}^\infty k\frac{\text{Pr(first $6$ on roll $k$ and even up to $6$ and billionth roll)}}{\text{Pr(even up to 
$6$ and billionth roll)}}$

Now even under the assumption of having all evens show up in the first billion rolls, having no $6$ show up in the first billion rolls is _extremely_ unlikely. So with extreme accuracy, we can approximate the denominator as just the probability that the first billion rolls are all even - $\left( \frac{1}{2}\right)$

Now for $k$ less than one billion, the probability that the first $6$ is on roll $k$ and there are only evens up to the billionth roll is:

$\left(\frac{2}{6}\right)^{k-1}\left(\frac{1}{6}\right)\left(\frac{1}{2}\right)^{1000000000-k}=\left(\frac{1}{3}\right)\left(\frac{2}{3}\right)^{k-1}\left(\frac{1}{2}\right)^{1000000000}$

For $k$ greater than one billion, the expressions slightly different, but the contributions are so astronomically small at this point that we can pretend they are the same with basically no percent change in our answer. So we have

$E[\text{number of rolls}\mid \text{evens up to 6 and billionth roll}]=\sum\limits_{k=0}^\infty k\left(\frac{1}{3}\right)\left(\frac{2}{3}\right)^{k-1}=\frac{1/3}{(1-2/3)^2}=3$.

which is roughly the expected number of rolls until $6$ on a $D3$ with sides labeled ${2,4,6}$. The conclusion is that adding conditions _after_ the first $6$ can indeed impact the expected number of rolls to the first $6$.

---
# Reviewing the paradox

Recall our definitions:

$A$: The expected number of rolls of a fair die until you roll two $6\text{s}$ in a row, given that all rolls were even.

$B$: The expected number of rolls of a fair die until you roll the second $6$ (not necessarily in a row), given that all rolls were even.

we will now consider a third:

$C$: The expected number of rolls of a fair die until you roll the second $6$, given that all rolls until the first instance of two $6\text{s}$ in a row are even.

$C$ and $A$ are directly comparable since they have the exact same condition - all rolls before the first instance of two $6\text{s}$ in a row are even and no conditions are given on any rolls that occur after. Since, amongst these rolls, the second $6$ will always occur at or before the first instance of two $6\text{s}$ in a row, we can safely conclude $A>C$

However, we've seen in the last section that $C$ is not necessarily the same as $B$, _even though we've only added conditions that apply at or after the second $6$_. So we cannot immediately conclude $A>B$. The two need to be calculated independently and directly compared.

---
# Proving $B>A$

$B$ ends up being a lot easier to calculate than $A$, so our strategy will be to prove that $B=3$, then use some nicer upper bounds to show that $A$ is less than $3$. The strategy to compute $B$ comes from a cute argument by reddit user u/bobjane in the aforementioned thread:

The average number of rolls until the first instance of ${1,3,5,6}$ is $3/2$, as it is a geometric distribution. Then after rolling the first, the average until the next instance of ${1,3,5,6}$ is again $3/2$, no matter how long it took to get the first instance. 

By linearity of expectation, we then have that the expected number of rolls of a die until the second instance of a side in ${1,3,5,6}$ is $\frac{3}{2}+\frac{3}{2}=3$. Conditioning on the specific combination we see being $6$ and $6$ does not impact the expectation, hence we have $B=3$.

The same user actually <a href="https://www.reddit.com/r/mathriddles/comments/17kuong/comment/k7fg2kk/?utm_source=share&utm_medium=web2x&context=3">gave an explicit formula</a> for $A$ in the general setting of rolling until $n$ $6\text{s}$ in a row, but there are a lot of steps and we won't need the exact answer anyway. Instead, we will content ourselves with an upper bound for $A$ that is lower than $3$.

We first compute the probability that two $6\text{s}$ occur before the first odd via a Markov chain argument. A "success" occurs if we roll the two $6\text{s}$ before an odd and a "failure" occurs if we roll the odd first.

Let $p_0$ be the probability of success at the beginning. Any time our most recent roll was a $2$ or $4$ and we have neither rolled $2$ $6\text{s}$ in a row nor an odd, the probability of success is still $p_0$ - nothing has changed. We will refer to this state as $S_0$.

But when we roll a $6$, the probability of getting two $6\text{s}$ before an odd is temporarily higher - call this $p_6$. We will call this state $S_6$. If the next roll is a $2$ or $4$, we are back to $S_0$ and the new probability becomes $p_0$ again.

We will solve for $p_0$ and $p_6$ via a system of equations:

$p_0=\frac{1}{6}p_1+\frac{2}{6}p_0$

$p_1=\frac{1}{6}+\frac{2}{6}p_0$

In other words, the probability of success from $S_0$ is equal to the probability of going from $S_0$ to $S_1$ (which occurs when you roll a $6$, so $\frac{1}{6}$) times the probability of success from $S_1$, plus the probability you stay at $S_0$ (which occurs when rolling a $2$ or $4$) times the probability of success from $S_0$.

On the other hand the probability of success from $S_1$ is $\frac{1}{6}$ (the probability of rolling another $6$ and being done), plus the probability you go back to $S_0$ times the probability of success from $S_0$.

Solving this system gives $p_0=\frac{1}{22}$. So:

$A=\sum\limits_{k=0}^\infty k \frac{\text{Pr(first instance of two $6$ in a row at roll $k$ and all evens until roll $k$}}{1/22}$

That numerator is tricky to calculate. It is much easier to calculate the probability that _an_ instance of exactly two $6\text{s}$ in a row occurs at roll $k$ and all evens until row $k$; this will be higher but we just want an upper bound anyway!.

For $k<2$, this probability is $0$ and for $k=2$, the probability is $\frac{1}{6^2}=\frac{1}{36}$. For $k>2$, this is the probability that the first $k-3$ rolls are even, roll $k-2$ is a $2$ or $4$ and rolls $k-1,k$ are $6$. In other words, $\left(\frac{1}{2}\right)^{k-1}\left(\frac{1}{3}\right)\left(\frac{1}{6}\right)^2=\left(\frac{1}{108}\right)\left(\frac{1}{2^{k-3}}\right)$.

So:

$A<22(2\left(\frac{1}{36}\right)+\sum\limits_{k=3}^\infty k\left(\frac{1}{108}\right)\left(\frac{1}{2}\right)^{k-3})=77/27<2.852$.

(skipping some algebra to manipulate the sum). The exact answer for $A$ is actually $30/11$, which is about $2.72$.

---
# The general case

Following the above argument, you can show that the expected number of rolls until the $n\text{th}$ $6$, given that all rolls are odd, is $\frac{3n}{2}$. The expected number of rolls until the first instance of $n$ $6\text{s}$ in a row, given that all rolls are odd is a little less than $n+\frac{4}{5}$. So, for instance, the expected number of rolls until the $70\text{th}$ $6$ is more than the expected number of rolls until the first instance of $100$ $6\text{s}$ in a row when both require no odds to show up before the stopping condition.

And now you understand why.



