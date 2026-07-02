
### 1. There are 13 caves arranged in a circle.
A thief is hiding in one of the caves. Every day:

The thief can move to a nearby cave or stay in the same cave.
Police can check any 2 caves per day.
Question: What is the minimum number of days required to guarantee catching the thief?
#### Solution: 
`C1, C2, C3, ... C13`
Police strategy
Police check C13 every day and use the second check to scan the remaining caves one by one.

Day 1 -> C13 and C12
Day 2 -> C13 and C11 
..
Day 12 -> C13 and C1

Why Check C13 Every Day? Think of C13 as a gate/blocking point.

If the puzzle is generalized from 13 caves to n caves arranged in a circle.
**Minimum days = n - 1**
For 13 caves: `n - 1 = 13 - 1 = 12 days`

Step 1: Fix one cave permanently
Police check cave Cn every day.
This cave acts like a blocked gate.

Step 2: Use the second check to scan remaining caves
Police check the remaining caves one by one in reverse order:
Day 1:  Cn and C(n-1)
Day 2:  Cn and C(n-2)
..
Day n-1: Cn and C1
In Total:
n - 1 days

#### using MDP:
``` text
A normal MDP is used when the thief’s movement has probabilities.
If the thief is intelligent/adversarial, it becomes a minimax / adversarial MDP or game.

An MDP is a framework for sequential decision-making using states, actions, transition probabilities, rewards/costs, and a policy.  The cave puzzle has 13 caves in the common version, where police check 2 caves per day and the thief may move to an adjacent cave or stay
```
1. Define the MDP: `C1, C2, C3, ..., Cn`
State: 
The true thief position is: `s_t ∈ {1, 2, 3, ..., n}`
But police do not know the exact cave, so practically we use a belief state: `b_t(i) = probability that thief is in cave i on day t`
Initially, if police know nothing: `b_0(i) = 1/n`
2. Actions:
Each police action is choosing 2 caves: `a_t = {i, j}`
Example: `a_t = {13, 12}`
The number of possible actions is: `C(n, 2) = n(n - 1) / 2`
3. Thief Movement Transition
If the thief can:

stay in the same cave,
move left,
move right,

then a simple probabilistic Markov model is:
``` text
P(i → i)       = 1/3
P(i → i - 1)   = 1/3
P(i → i + 1)   = 1/3
```
4. Catch Probability for an Action
If police check caves {i, j}, then probability of catching thief on that day is:
`P(catch) = b_t(i) + b_t(j)`
So the best greedy action for one day is: `Choose the two caves with highest belief probability.`
But MDP is not only greedy. It chooses the action that minimizes the expected total days.

5. Bellman Equation / Markov Decision Rule
Let `V(b)`
be the minimum expected number of days to catch the thief from belief state b.
For action a, define:
``` text
p_catch(a) = sum of probabilities in checked caves
```
If not caught, update the belief and continue.

``` text
V(b) = min over actions a [
    1 + (1 - p_catch(a)) * V(next_belief)
]
```

Meaning:

1 = one day spent checking caves,
p_catch(a) = probability of success today,
(1 - p_catch(a)) = probability thief survives today,
V(next_belief) = future expected days.

This is the Markov decision rule.

6. Belief Update Rule
Suppose police check caves: `a = {i, j}`
Remove those caves from belief because if thief was there, he would be caught.
For every unchecked cave k: `remaining_belief(k) = b(k) / (1 - p_catch)`

Then apply thief movement transition: `next_belief = T × remaining_belief`
Where T is the Markov transition matrix.

7. Example for 13 Caves
`Each cave has probability = 1/13`
If police check caves C13 and C12: `P(catch) = 1/13 + 1/13 = 2/13`
If not caught:  `P(not caught) = 11/13`
Then the remaining probability is redistributed among possible caves after the thief moves.
The MDP will compare all possible pairs: `{C1, C2}, {C1, C3}, ..., {C12, C13}`
and choose the pair that gives the lowest expected future capture time.

<img width="376" height="247" alt="image" src="https://github.com/user-attachments/assets/0e372f1b-4ac0-48c8-83f8-22d25f61c134" />





