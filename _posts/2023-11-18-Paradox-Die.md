## A paradox in conditional expectation

Which of the following do you think is bigger?

A: The expected number of rolls of a fair die until you roll 10 6s in a row, given that all rolls were even

B: The expected number of rolls of a fair die until you roll the 9th 6 (not necessarily in a row), given that all rolls were even.

If you're unfamiliar with conditional expectation, think of it this way: first perform a _lot_ of sequences of die rolls, stopping each sequence when you roll 10 6s in a row. Throw out all the sequences that contain an odd roll. The average number of rolls in the remaining sequences should be close to 'A'. Likewise, perform a bunch of sequences of die rolls that each stop at the 9th 6. Throw out all the sequences that contain an odd roll. The average number of rolls of the remaining sequences should be close to 'B'.

I asked <a href="https://www.reddit.com/r/math/comments/17qcx8u/the_paradox_that_broke_me/">here</a> something like this on r/math a couple weeks ago, and even with the hint that the answer was paradoxical, the early consensus was that 'A' must be larger. The justification was more or less the following: any time you roll until reaching 10 6s in a row, you will have _also_ hit your 9th 6 before then. So regardless what the conditions are, 'A' must be larger than 'B'.

But the correct answer is actually 'B'. What on earth is going on?

---
# Some basics

