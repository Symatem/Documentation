# Programming
Almost all programming technologies today are arranged in some sort of spectrum. Usually we call the extremes low- / high-level and associate them with opposites like AOT vs. JIT, compiler vs. interpreter and virtual machines. Most software systems are specialized in one tiny area on the spectrum. Nothing covers the entire spectrum. And so we have to combine different systems and architectures to a huge inhomogeneous patchwork rug. This increases overall complexity and comes with a lot of problems: People have to learn all those implementations, they don't work well together, we have lots of adaptors instead of nice modularity and thus even more bugs. Programming languages come and go all the time, some stay longer than others. But most of them move around a few paradigms in circles and just paint the same ideas with new colors.


## Components
Our approach to programming is derived from graphical data flow models and similar to functional programming and the way we design logic circuits today.
Control flow is implemented by passing operators / lambdas around as data flow and then optionally executing them like in LISP.

- Operator
- Operation
- Operand
- Carrier


## Reasoning

### Inspiration
- LISP
    - Programs are data too: Express control flow in data flow using lambdas
    - Recursion instead of iteration
    - Minimalistic syntax: Almost projectional editing
    - Few primitives
- ECMA Script / JavaScript
    - Dynamic Attributes / Properties
    - Inheritance: Prototyping instead of classes
    - Array indices are just normal Attributes
- C++
    - Const Expressions
    - Templates
- Smalltalk / Squeak
    - Super IDE / All-in-one VM image: Operating System, Database, Editor, Compiler, Live Debugger, Version Control
    - Few primitives, move everything into Standard Library
- UNIX / POSIX
    - Inodes (Ontology Symbols)
    - Mounting (Virtualization / Namespaces)
    - Reification: Everything is a file
- LLVM IR
    - Basic blocks
    - SSA
    - DAG
- Semantic Web: RDF
    - Triples
    - EAV model
    - Reflection
- GIT
    - Branches
    - Distribution

### Programming Paradigms
There are three major approaches to programming:
- Imperative
    - Total order: Sequence of instructions
    - Strict evaluation of functions (sync-fences)
    - Data flow is implicit and obscure, e.g. "side effects"
        - Cached values become outdated if their dependencies change
        - State interference: Iterate while modify, race conditions, ...
    - Control flow is explicit and not integrated into data flow
    - Strict consistency, locking: Bad for multithreading
- Functional
    - Partial order: Data flow graph
    - Implicit control flow in data flow
    - Concurrent by default
    - Immutable: No stable object identity (over time), object orientation is impossible
- Declarative
    - Control flow and data flow are non existent: They aren't programs in that sense
    - Static: No concept of time at all, far from physical world
    - Constraint and relation based: Similar to Transport layer

Properties they share:
- Describe
    - Imperative and Functional: Describe how things are done, without necessarily describing the result
    - Declarative: Describing how valid results look like, without describing how to obtain them
- Single static assignment
    - Imperative can be transformed into functional using SSA
    - Functional can be seen and executed as imperative without any changes
- Referential transparency
    - Results and expressions are interchangeable.
    - No side effects: Always reproducible
    - Stateless: Interaction with physical world is impossible
    - Functional and declarative share this property

### Uni- vs bidirectional
- Unidirectional is simpler and two connections can be combined to be bidirectional
- Bidirectional is sometimes used in electronics like buses

### Connection cardinality
- Many-to-many would be pointless as it would be the same semantic as variables and introduce the same problems
- One-to-many leaves the exact scatter and gather semantic undefined
- One-to-one can model everything and groups of the same name can act like one-to-many connections

### Evaluation
- Push forward and execute next block as soon as all needed values arrive (Forward / Eager Evaluation)
- Pull values from previous blocks what is needed (Backward / Lazy Evaluation)

### Control Flow
- Continuation: Cycles, PHI Nodes
- Lambda: DAG, Recursion

### Lifetime Management
- Manual: Like new and delete
- Automatic reference counting (ARC)
- Stack / scope bound
- Garbage collector (GC)
