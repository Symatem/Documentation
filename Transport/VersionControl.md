# Version Control
A version is one specific state the ontology can be in.


## Version DAG
The version control system stores a DAG of versions as vertices and transactions as edges.
An origin / orphan vertex as source of the DAG represents the initially empty state of the version controlled ontology.
Each vertex / version is associated with:
- Parent versions
- Forward and backward differential
- Meta Data
    - User defined: Timestamp, author, cryptographic signature, etc.


## Transaction
A transaction (is mathematically a function) which transforms versions of the ontology to others.
Thus, it is not dependent of one specific input state, but can be applied to a variety of input states.
However, a transaction can also be inapplicable for a specific input state.
A transaction provides:
- Atomicity: The transformation is applied entirely or not at all
- Consistency: Both input and output state are valid
- Isolation: Multiple transactions can exist without influencing each other
- Determinism: Applying the same transaction to two identical input states leads to two identical output states

### Differential
A differential describes a set of actions / operations forming a transaction.
They can be ambiguous meaning that a transaction can be described by a variety of differentials.
As opposed to other transaction mechanisms like backups of the entire state or copy-on-write this approach needs less memory but more computational power.

Operation Sets:
- Create / Release Symbol
    - Symbol
- Rename / Relocate Symbol
    - Destination Symbol
    - Source Symbol
- Link / Unlink a Triple
    - Entity Symbol
    - Attribute Symbol
    - Value Symbol
- Increase / Decrease Length
    - Symbol
    - Offset
    - Length
- Replace Data
    - Destination Symbol
    - Destination Offset
    - Source Symbol
    - Source Offset
    - Length


## Operations
- Record actions to create a differential
- Compare two versions to create a differential
- Optimize a differential / compress it
- Manipulate the version DAG:
    - Merge
    - Rebase
    - Squash / prune / truncate
    - Commit / fork: Apply a differential to create a new version and its reverse differential
- Rollback: Revert back to a previous version using its reverse differential
