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


## Reasoning

### Data Operations (Increase, Decrease, Replace)
To allow a differential to be applied as easy and fast as possible, it is structured in a way that resembles its execution / application.
Also the inversion of a differential should be as strait forward as possible to make rollbacks / reverts easy and fast.
First all increase operations happen, then all replace operations and finally all decrease operations.
This gives replace operations the chance to fetch data from sources which will be decreased later and store it into destinations which are already increased.
The increase and decrease operations shift the offsets of operations with higher offsets.
To avoid explicit ordering, the increase and decrease operations are sorted by their destination offset.
This corresponds with their offsets being seen as before or after all operations of their kind took place:
    - Sorting operations descending results in offsets being seen as before any operation took place
    - Sorting operations ascending results in offsets being seen as after all operations took place
Sorting increase operations ascending and decrease operations descending has two advantages:
    - Their offsets stay the same when the differential is inverted. Increase and decrease operations are inverted by swapping them and flipping their order accordingly: No need to update offsets or sort operations again.
    - Because replace operations happen in the middle (after all increase and before all decrease operations) they can share the same offset definition. This makes dependencies between different kinds of operations easier to handle as their offsets can be compared directly.
