# Concurrency


## Levels of Concurrency
So far, I have identified 4 levels of managing consistency.

### Level 0: Not at all
This model does not scale at all as you can always only have a single thread of execution,
otherwise you would experience a lot of undefined behavior and it is completely unusable.

### Level 1: Pessimistic Locking / Strict Consistency
When a unit is about to enter a potential race condition, it halts all other units and informs them of this.
Then all other units have to wait if they wanted to participate in that race condition,
which prevents the actual race condition from happening and therefore enforces consistency (strictly).
When the unit is finished with its tasks it halts all other units again to inform them.

This model scales a little until it hits a certain constant limit.
From there on the time spent on managing the locks and exchanging information about the status of resources is far longer than performing the actual task.
Also, programming in this fashion is very hard as you can easily have dead locks, life locks or
run in situations where almost all units are waiting for just one unit to finish its task.

### Level 2: Optimistic Locking / Eventual Consistency
Optimistic Locking means that two or more units can enter a race condition without knowing of each other and
the first to finish its task "wins" while informing all other units to restart their tasks based on its own results.
Eventual consistency means that local branches of a master version can be maintained and changed independently, but only temporally.
Ultimately, all branches have to be merged back into the master branch, leading to consistency (eventually).
Both are similar, with the difference that optimistic locking simply discards the other branches implicitly,
while eventual consistency has to merge the differences explicitly and thus requires special programming for the merging.

### Level 3: VCS
This model can branch and can also merge but there is no need to do so.
Instead all branches form a directed acyclic graph (DAG) much like GIT.


## Reasoning

### Problem of Nested Transactions in Eventual Consistency
- Committing inner transactions right away, so others can see and build upon them
    - The outer could not be aborted / rolled back and thus would lose its status of an atomic transaction
- Committing inner transactions with the commit of the most outer one
    - The outer could become massive and too big / long, preventing interaction with others
    - Would be the same as using a pessimistic semaphore, not really optimistic
- Forbid nesting of transactions
    - Programmers might violate this rule without noticing, hidden in a called subroutine or recursion
