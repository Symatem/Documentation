# Compiler

## Entry
Each instantiation of an operator with specific input operands creates one entry in a cache (similar to templates in C++).
- symbol: Identifies this specific instantiation
- inputOperands: Map of operand tags and their input operands
    - operator: The operator to be called / executed is one of the input operands
    - hash: Hash value of all input operands (used as cache key)
    - inputOperandBundle: Automatic bundling of all input operands
- outputOperands: Map of operand tags and their output operands
    - outputOperandBundle: Automatic bundling of all output operands

### Auxiliary Structure
The aux map of an entry only exists while the entry is being processed (thus indicates the processing state).
- llvmBasicBlock: The last basic block (instructions will land here by default)
- inputLlvmValueBundle: Automatic bundling of all input values
- operatDestinationOperands: Set of destination operands being collected per operation and the outer operator
- operationsBlockedByThis: Set of operations per entry which are waiting for this operator to finish processing.
- unsatisfiedOperations: Reference count of carriers per operation which have not been propagated yet.
- readyOperations: Queue of operations which can be processed next.
- blockedOperations: Set of operations which are waiting for an entry to finish processing.


## Execution
The compiler uses a cooperative concurrent (not parallel) version of [Kahn's algorithm](https://en.wikipedia.org/wiki/Topological_sorting#Kahn's_algorithm),
in order to schedule (topological sort) and then interpret the operation DAG of an operator sequentially.

### Phase 0: Cache Fetch / Primitive Detection
For each operator called / executed the compiler first checks if the specific instantiation was executed already (if an entry exists in the cache).
If so it is returned immediately (note that the entry can still have an auxiliary structure and be processed).
Else an entry is generated and pushed to the cache.
Next the compiler checks if the operator is a primitive.
If so it will be optimized and handled more directly, not going through the following phases in most cases.

### Phase 1: Collect Destinations
First an auxiliary structure is generated and attached to the entry.
Then the referenceCount (how many inputs / number of carriers with the destination being the given operation) of each operation is calculated.
Constants do not count and are directly propagated in this phase (added to operatDestinationOperands).
If the referenceCount is remains 0 then the operation goes into readyOperations directly else it added to unsatisfiedOperations along with the referenceCount.
The outer operator it self is also processed but only to gather the constants and is not added to readyOperations or unsatisfiedOperations.

### Phase 2: Call and Propagate Sources
First the outer operator it self is processed followed by all readyOperations (which increase during this phase).
Each operation (except for the outer operator) is called first and then all its outputs are propagated to their destinations (operatDestinationOperands) via carriers.
A propagated operand decreases the referenceCount of its destination in unsatisfiedOperations.
If the referenceCount reaches 0 the operation is transferred from unsatisfiedOperations to readyOperations and will be processed in one of the following iterations.
In this phase the compiler can also encounter a recursive call cycle, when the outer operator which concurrently is being processed is called from the inside.
Because such a call can not yield all outputs yet, the operation is instead skipped and transferred from readyOperations to blockedOperations.
Each operator also manages a set of operationsBlockedByThis which will be used in the next phase.

### Phase 3: Finish and Resume Execution
After all readyOperations are processed and there are no more blockedOperations inside the operator, the entry is cleaned up (The auxiliary structure is deleted).
Then all operationsBlockedByThis are woken up again, with their blockedOperations of this operator being transferred back into readyOperations.
Finally the operators of operationsBlockedByThis resume execution in phase 2.
