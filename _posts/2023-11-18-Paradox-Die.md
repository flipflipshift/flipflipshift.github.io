## A paradox in conditional expectation

Which of the following do you think is bigger?

A: The expected number of rolls of a fair die until you roll two 6s in a row, given that all rolls were even.

B: The expected number of rolls of a fair die until you roll the second 6 (not necessarily in a row), given that all rolls were even.

If you're unfamiliar with conditional expectation, think of it this way: Imagine you were to perform a million sequences of die rolls, stopping each sequence when you roll two 6s in a row. Then you throw out all the sequences that contain an odd roll. The average number of rolls in the remaining sequences should be close to 'A'. Next, perform a million sequences of die rolls, stopping each sequence when you roll the second 6. Throw out all the sequences among these that contain an odd roll. The average number of rolls in the remaining sequence should be close to 'B'.

Likewise, perform a bunch of sequences of die rolls that each stop at the 9th 6. Throw out all the sequences that contain an odd roll. The average number of rolls of the remaining sequences should be close to 'B'.

I asked <a href="https://www.reddit.com/r/math/comments/17qcx8u/the_paradox_that_broke_me/">something like this</a> on r/math a couple weeks ago, and even with the hint that the answer was paradoxical, the early consensus was that 'A' must be larger. The justification was more or less the following: any time you roll until reaching 10 6s in a row, you will have _also_ hit your 9th 6 before then. So regardless what the conditions are, 'A' must be larger than 'B'.

But the correct answer is actually 'B'. What on earth is going on?

---
# A quick verification

Before we proceed, let's write some code to estimate 'A' and 'B'. The only goal here is to be as unambiguous as possible, so the code will be heavily unoptimized in both run-time and length.

```Python
import random
def estimate_A(n):
  num_sequences_without_odds=0
  sum_rolls_without_odds=0
  #Roll 'n' sequences of die rolls, stopping each when getting two 6s in a row. 
  #For each sequence, if no odd showed up, add number of rolls to sum_rolls_without_odds and increment num_sequences_without_odds
  #Average number of rolls in such sequences is then (sum_rolls_without_odds)/(num_sequences_without_odds)
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
  num_sequences_without_odds=0
  sum_rolls_without_odds=0
  #Roll 'n' sequences of die rolls, stopping each when getting second 6. 
  #For each sequence, if no odd showed up, add number of rolls to sum_rolls_without_odds and increment num_sequences_without_odds
  #Average number of rolls in such sequences is then (sum_rolls_without_odds)/(num_sequences_without_odds)
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

print("Estimate for A: " + str(estimate_A(1000000)))
print("Estimate for B: " + str(estimate_B(1000000)))

```

