# Version Control
A version is one specific state the ontology can be in.


## Version DAG
The version control system stores a DAG of versions as vertices and transactions as edges.
There is an origin / orphan vertex which is the source of the DAG and represents the initially empty state of the version controlled ontology.
Each vertex / version is associated with:
- Parent version
- Optional merged version
- Forward differential
- Optional backward differential
- Meta Data
    - User defined: Timestamp, author, cryptographic signature, etc.


## Transaction
A transaction is mathematically speaking a function which transforms versions of the ontology to others.
Thus, it is not dependent of one specific input state, but can be applied to a variety of input states.
However, a transaction can also be inapplicable for a specific input state.
Properties of a transaction:
- Atomicity: The transformation is applied entirely or not at all
- Consistency: If the input was valid then the output will be valid again
- Isolation: Multiple transactions can be applied without compromising each other
- Invertable: Every transaction has an inverse which reverses its effect
- Determinism: Applying the same transaction to two identical inputs leads to two identical outputs

### Differential
A differential describes a set of actions / operations forming a transaction.
They can be ambiguous meaning that a transaction can be described by a variety of differentials.
As opposed to other transaction mechanisms like backups of the entire state or copy-on-write this approach needs less memory but more computational power.

Composition:
- Draft Data
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
- Create a differential:
    - By recording actions
    - By comparing two versions (graph isomorphism is NP-complete)
- Optimize a differential / compress it
- Manipulate the version DAG:
    - Merge
    - Rebase
    - Squash / prune / truncate
    - Apply a differential to create a new version and its reverse differential
- Checkout / rollback: Using forward and reverse differential to reach any version
