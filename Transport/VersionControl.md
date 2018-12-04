# Version Control
A version is one specific state the ontology can be in.
One independent instance of the version control is called repository.

## Version DAG
A repository contains a DAG of versions as vertices and transactions as edges.
There is an origin / orphan vertex which is the source of the DAG and represents the initially empty state of the version controlled ontology.
Each vertex / version is associated with:
- Parent version (mandatory)
- Merged versions (optional)
- Forward differential (mandatory)
- Backward differential (optional)
- Meta Data (optional)
    - User defined: Timestamp, author, cryptographic signature, etc.


## Snapshots
Doing a checkout results in a materialization of a version called snapshot.
Unlike GIT, this version control system allows for many snapshots to exist in a repository simultaneously.


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
A differential describes the minimal set of actions / operations forming a transaction,
as opposed to a journal which would record all operations, even those which cancel out each other (annihilation).

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

Order and Validity Constraints:
- Create Symbols
    - Symbol is seen as in the previous version
    - Symbol cannot be used as:
        - Source or Destination of Rename operations
        - Source of Replace Data operations
        - Destination of Decrease Data Length operations
        - Entity, Attribute or Value of Unlink Triple operations
        - Symbol of other Create Symbol operations (Duplication)
        - Symbol of Release Symbol operations (Mutually exclusive)
    - Symbol can only be used as:
        - Destination of Increase Data Length operations
        - Destination of Replace Data operations
        - Entity, Attribute or Value of Link Triple operations
- Rename Symbols
    - Symbols are seen as in the previous version
    - Unambiguous: Each Symbol can only be a source of one or no rename operation
    - A Symbol can be destination of multiple rename operations
- Increase Data Length
    - Symbol is seen as after the rename operations
    - Increase operations are sorted ascending by offset
    - Destination Offset is seen as in the previous version plus lower offset Increase Data Length operations
    - Mutually exclusive: One specific offset can remain unchanged, be increased or decreased, but not both
- Replace Data
    - Symbols are seen as after the rename operations
    - Source data is seen as in the previous version
    - Injective: Each bit of a Symbols data can only be a Destination of one or no Replace Data Operation (no overlaps are allowed)
    - Destination and Source Offset are seen as in the previous version plus lower offset Increase Data Length operations
- Decrease Data Length
    - Symbol is seen as after the rename operations
    - Decrease operations are sorted descending by offset
    - Destination Offset is seen as in the previous version plus lower offset Increase Data Length operations
    - Mutually exclusive: One specific offset can remain unchanged, be increased or decreased, but not both
- Link / Unlink Triples
    - Symbols are seen as after the rename operations
- Release Symbols
    - Symbol is seen as in the previous version
    - Symbol cannot be used as:
        - Source or Destination of Rename operations
        - Destination of Increase or Decrease Data Length operations
        - Destination of Replace Data operations
        - Entity, Attribute or Value of Link or Unlink Triple operations
        - Symbol of other Release Symbol operations (Duplication)
        - Symbol of Create Symbol operations (Mutually exclusive)
    - Symbol can only be used as:
        - Source of Replace Data operations


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
All version control is easy until you want to merge something.

### Transactions
- Holistic Snapshots
    - Consumes lots of memory
    - Spends time on copying all data all the time
    - Checkout is free
    - Differences and similarities must be calculated first: Hard to merge
- Copy-on-Write
    - Consumes less memory than holistic snapshots but more than differentials
    - Spends time on copying the data it is about to change
    - Checkout is free
    - Some differences and similarities can be obtained, but still hard to merge
- Journaling
    - Consumes memory for every recorded action, even if it is irrelevant to the outcome (annihilation)
    - Spends time on application / checkouts
    - Checkout needs a linear search from the last / closest checkout
    - Easy to merge, except for redundancy in actions
- Differential
    - Consumes the least memory and can be compressed, optimized and squashed
    - Spends time on recording, optimization
    - Checkout needs a linear search from the last / closest checkout
    - Easiest to merge

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
- Their offsets stay the same when the differential is inverted.
Increase and decrease operations just need to be swapped and their order be flipped accordingly: No need to update offsets or sort operations again.
- Because replace operations happen in the middle (after all increase and before all decrease operations) they can share the same offset definition.
This makes dependencies between different kinds of operations easier to handle as their offsets can be compared directly.
